---
title: 'Vergleich von Einzel- und geteilten Abfragen: EF Core'
description: Übersetzen von Abfragen in einfache und geteilte Abfragen in SQL mit Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/single-split-queries
ms.openlocfilehash: 8615e7ba5247a90a1f980e9f7b1b23c81170971f
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128991"
---
# <a name="split-queries"></a>Geteilte Abfragen

## <a name="single-queries"></a>Einzelabfragen

In relationalen Datenbanken werden alle zugehörigen Entitäten standardmäßig durch Einführung von JOIN-Vorgängen in eine Einzelabfrage geladen.

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

Wenn ein typischer Blog mehrere zugehörige Beiträge enthält, werden in den Zeilen für diese Beiträge die Informationen des Blogs dupliziert. Diese Duplizierung führt zum so genannten Problem der „kartesischen Explosion“. Wenn weitere 1:n-Beziehungen geladen werden, wächst die Menge an duplizierten Daten weiter und beeinträchtigt die Leistung Ihrer Anwendung.

## <a name="split-queries"></a>Geteilte Abfragen

> [!NOTE]
> Dieses Feature wurde in EF Core 5.0 eingeführt. Sie funktioniert nur bei Verwenden von `Include`. [Dieses Problem](https://github.com/dotnet/efcore/issues/21234) stellt die Nachverfolgung der Unterstützung für geteilte Abfragen beim Laden zugehöriger Daten in die Projektion ohne `Include` dar.

In EF können Sie angeben, dass eine bestimmte LINQ-Abfrage in auf mehrere SQL-Abfragen *aufgeteilt* werden soll. Anstelle von JOIN-Vorgängen generieren geteilte Abfragen für jede enthaltene Sammlungsnavigation eine zusätzliche SQL-Abfrage:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

Dabei wird der folgende SQL-Code erzeugt:

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> Entitäten mit 1:1-Beziehung werden stets über JOIN-Vorgänge in dieselbe Abfrage geladen, da dies keine Auswirkungen auf die Leistung hat.

## <a name="enabling-split-queries-globally"></a>Globales Aktivieren von geteilten Abfragen

Sie können auch geteilte Abfragen als Standardeinstellung für den Kontext Ihrer Anwendung konfigurieren:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

Wenn geteilte Abfragen als Standardeinstellung konfiguriert werden, ist es dennoch möglich, bestimmte Abfragen so zu konfigurieren, dass sie als einzelne Abfragen ausgeführt werden:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

EF Core verwendet beim Fehlen jeglicher Konfiguration standardmäßig den Einzelabfragemodus. Da dies zu Leistungsproblemen führen kann, generiert EF Core immer dann eine Warnung, wenn die folgenden Bedingungen vorliegen:

- EF Core erkennt, dass die Abfrage mehrere Sammlungen lädt.
- Der Benutzer hat den Abfrageteilungsmodus nicht global konfiguriert.
- Der Benutzer hat den Operator `AsSingleQuery`/`AsSplitQuery`nicht auf die Abfrage angewendet.

Um die Warnung zu deaktivieren, konfigurieren Sie den Abfrageteilungsmodus global oder auf Abfrageebene mit einem geeigneten Wert.

## <a name="characteristics-of-split-queries"></a>Merkmale von geteilten Abfragen

Zwar werden bei geteilten Abfragen die Leistungsprobleme im Zusammenhang mit Verknüpfungen und der kartesischen Explosion vermieden, allerdings weist dieser Modus auch einige Nachteile auf:

- Die meisten Datenbanken garantieren Datenkonsistenz bei einzelnen Abfragen. Bei mehreren Abfragen gibt es eine solche Garantie nicht. Wenn die Datenbank während dem Ausführen der Abfragen aktualisiert wird, sind die resultierenden Daten möglicherweise nicht konsistent. Dieses Problem können Sie minimieren, indem Sie die Abfragen mit einer serialisierbaren Transaktion oder Momentaufnahmentransaktion umschließen. Allerdings kann auch dieses Vorgehen Leistungsprobleme nach sich ziehen. Weitere Informationen finden Sie in der Dokumentation Ihrer Datenbank.
- Jede Abfrage impliziert derzeit einen zusätzlichen Netzwerkroundtrip zu Ihrer Datenbank. Durch mehrere Netzwerkroundtrips kann die Leistung beeinträchtigt werden, insbesondere, wenn die Latenz bei der Datenbank hoch ist (z. B. bei Clouddiensten).
- Zwar erlauben einige Datenbanken die gleichzeitige Nutzung der Ergebnisse mehrerer Abfragen (SQL Server mit MARS, Sqlite), aber in den meisten darf zu jedem Zeitpunkt immer nur eine Abfrage aktiv sein. Folglich müssen alle Ergebnisse früherer Abfragen im Arbeitsspeicher der Anwendung gepuffert werden, bevor spätere Abfragen ausgeführt werden. Dadurch steigen die Arbeitsspeicheranforderungen.

Leider gibt es nicht die eine perfekte Strategie zum Laden von zugehörigen Entitäten, die in allen Szenarien passt. Wägen Sie sehr sorgfältig die Vor- und Nachteile einzelner und geteilter Abfragen ab, und wählen Sie die Variante aus, die sich für Ihre Anforderungen besser eignet.
