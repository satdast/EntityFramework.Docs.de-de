---
title: Sortierungen und Berücksichtigung der Groß-/Kleinschreibung-EF Core
description: Konfigurieren von Sortierungen und Berücksichtigung der Groß-/Kleinschreibung in der Datenbank und in Abfragen
author: roji
ms.date: 04/27/2020
ms.assetid: bde4e0ee-fba3-4813-a849-27049323d301
uid: core/miscellaneous/collations-and-case-sensitivity.md
ms.openlocfilehash: 1c9344b8e96142645f55e8d830dfd350dc10aa96
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84666180"
---
# <a name="collations-and-case-sensitivity"></a>Sortierungen und Groß-/Kleinschreibung

> [!NOTE]
> Diese Funktion wird in EF Core 5.0 eingeführt.

Die Text Verarbeitung in Datenbanken kann sehr komplex sein und erfordert eine höhere Benutzer Aufmerksamkeit. Datenbanken unterscheiden sich in der Art und Weise, wie Sie Text verarbeiten. während bei einigen Datenbanken standardmäßig die Groß-/Kleinschreibung beachtet wird (z. b. sqlite, PostgreSQL), wird bei anderen Datenbanken die Groß-/Kleinschreibung nicht beachtet (SQL Server MySQL Außerdem können aufgrund der Index Verwendung die Unterscheidung nach Groß-/Kleinschreibung und ähnliche Aspekte eine weitreichende Auswirkung auf die Abfrageleistung haben: Obwohl es möglicherweise verlockend ist, zu verwenden, um einen Vergleich ohne Berücksichtigung der Groß-/Kleinschreibung `string.Lower` zu erzwingen, kann dies dazu führen, dass Ihre Anwendung keine Indizes verwendet. Auf dieser Seite erfahren Sie, wie Sie die Groß-/Kleinschreibung oder eher Sortierungen konfigurieren, und wie Sie dies auf effiziente Weise durchführen können, ohne die Abfrageleistung zu beeinträchtigen.

## <a name="introduction-to-collations"></a>Einführung in Sortierungen

Ein grundlegendes Konzept bei der Textverarbeitung ist die *Sortierung*, bei der es sich um einen Satz von Regeln handelt, der festlegt, wie Text Werte auf Gleichheit sortiert und verglichen werden. Bei einer Sortierung ohne Beachtung der Groß-/Kleinschreibung werden z. b. Unterschiede zwischen Groß-und Kleinbuchstaben für den Gleichheits Vergleich ignoriert. bei der Sortierung wird die Groß-/Kleinschreibung beachtet. Da die Unterscheidung nach Groß-/Kleinschreibung Kultur abhängig ist (z. b. `i` und `I` einen anderen Buchstaben in Türkisch darstellt), gibt es mehrere Sortierungen ohne Beachtung der Groß-/Kleinschreibung, die jeweils über einen eigenen Regelsatz verfügen Der Gültigkeitsbereich von Sortierungen ist auch über die Unterscheidung nach Groß-/Kleinschreibung und andere Aspekte von Zeichendaten hinaus. in Deutsch ist beispielsweise manchmal (aber nicht immer) wünschenswert, um `ä` und `ae` als identisch zu behandeln. Und schließlich definieren Sortierungen auch, wie Text Werte *sortiert*werden: bei deutschen stellen `ä` nach `a` wird der schwedische am Ende des Alphabets platziert.

Bei allen Text Vorgängen in einer Datenbank wird eine Sortierung (explizit oder implizit) verwendet, um zu bestimmen, wie der Vorgang im Vergleich und Auftrags Zeichenfolgen verwendet wird. Die tatsächliche Liste der verfügbaren Sortierungen und ihrer Benennungs Schemas ist datenbankspezifisch. Links zu relevanten Dokumentationsseiten verschiedener Datenbanken [finden Sie im Abschnitt weiter unten](#database-specific-information) . Glücklicherweise ist es in der Regel möglich, eine Standardsortierung auf Datenbank-oder Spaltenebene zu definieren und explizit anzugeben, welche Sortierung für bestimmte Vorgänge in einer Abfrage verwendet werden soll.

## <a name="database-collation"></a>Datenbanksortierung

In den meisten Datenbanksystemen wird eine Standardsortierung auf Datenbankebene definiert. Wenn diese Sortierung nicht außer Kraft gesetzt wird, gilt sie implizit für alle Text Vorgänge, die in dieser Datenbank stattfinden. Die Daten Bank Sortierung wird in der Regel beim Erstellen der Datenbank (über die `CREATE DATABASE` DDL-Anweisung) festgelegt. Wenn Sie nicht angegeben wird, wird standardmäßig ein Wert auf Serverebene festgelegt, der zum Zeitpunkt der Einrichtung festgelegt wird. Die Standardsortierung auf Serverebene in SQL Server z. b., bei der es sich um eine Sortierung mit Unterscheidung nach `SQL_Latin1_General_CP1_CI_AS` Groß-/Kleinschreibung und Akzent handelt. Obwohl Datenbanksysteme in der Regel das Ändern der Sortierung einer vorhandenen Datenbank zulassen, kann dies zu Komplikationen führen. Es wird empfohlen, vor der Erstellung der Datenbank eine Sortierung auszuwählen.

Wenn Sie EF Core Migrationen zum Verwalten des Datenbankschemas verwenden, konfiguriert die folgende in der-Methode des Modells `OnModelCreating` eine SQL Server-Datenbank für die Verwendung einer Sortierung mit Beachtung der Groß-/Kleinschreibung:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=40)]

## <a name="column-collation"></a>Spaltensortierung

Sortierungen können auch für Textspalten definiert werden, wobei der Standardwert der Datenbank überschrieben wird. Dies kann hilfreich sein, wenn bei bestimmten Spalten die Groß-/Kleinschreibung nicht beachtet werden muss, während bei der restlichen Datenbank die Groß-/Kleinschreibung beachtet werden muss.

Wenn Sie EF Core Migrationen zum Verwalten des Datenbankschemas verwenden, wird die-Spalte für die-Eigenschaft in einer Datenbank, bei der andernfalls die Groß-/Kleinschreibung beachtet wird, durch die folgende Konfiguration konfiguriert `Name` :

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=OnModelCreating&highlight=6)]

## <a name="explicit-collation-in-a-query"></a>Explizite Sortierung in einer Abfrage

In einigen Fällen muss die gleiche Spalte mithilfe verschiedener Sortierungen durch unterschiedliche Abfragen abgefragt werden. Beispielsweise kann eine Abfrage einen Vergleich mit Berücksichtigung der Groß-/Kleinschreibung für eine Spalte durchführen, während eine andere Abfrage einen Vergleich für dieselbe Spalte ohne Beachtung der Groß-und Kleinschreibung durchführen muss. Dies kann durch explizites angeben einer Sortierung in der Abfrage selbst erreicht werden:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=SimpleQueryCollation)]

Dadurch wird eine- `COLLATE` Klausel in der SQL-Abfrage generiert, die unabhängig von der auf der Spalten-oder Datenbankebene definierten Sortierung eine Sortierung mit Berücksichtigung der Groß-/Kleinschreibung anwendet:

```sql
SELECT [c].[Id], [c].[Name]
FROM [Customers] AS [c]
WHERE [c].[Name] COLLATE SQL_Latin1_General_CP1_CS_AS = N'John'
```

### <a name="explicit-collations-and-indexes"></a>Explizite Sortierungen und Indizes

Indizes sind einer der wichtigsten Faktoren in Bezug auf die Datenbankleistung. eine Abfrage, die effizient mit einem Index ausgeführt wird, kann ohne diesen Index an einen Halt enden. Indizes erben implizit die Sortierung Ihrer Spalte. Dies bedeutet, dass alle Abfragen für die Spalte automatisch für die Verwendung von Indizes berechtigt sind, die für diese Spalte definiert sind, vorausgesetzt, dass die Abfrage keine andere Sortierung angibt. Wenn eine explizite Sortierung in einer Abfrage angegeben wird, wird in der Regel verhindert, dass diese Abfrage einen für diese Spalte definierten Index verwendet, da die Sortierungen nicht mehr entsprechen. Daher wird empfohlen, bei der Verwendung dieses Features Vorsicht walten zu lassen. Es ist immer vorzuziehen, die Sortierung auf Spaltenebene (oder Datenbank) zu definieren, sodass alle Abfragen diese Sortierung implizit verwenden und von einem beliebigen Index profitieren können.

Beachten Sie, dass bei einigen Datenbanken die Sortierung beim Erstellen eines Indexes (z. b. PostgreSQL, SQLite) definiert werden kann. Dadurch können mehrere Indizes für dieselbe Spalte definiert werden, sodass Vorgänge mit unterschiedlichen Sortierungen beschleunigt werden (z. b. bei vergleichen sowohl bei Groß-/Kleinschreibung als auch bei vergleichen). Weitere Informationen finden Sie in der Dokumentation des Datenbankanbieters.

> [!WARNING]
> Überprüfen Sie immer die Abfrage Pläne Ihrer Abfragen, und stellen Sie sicher, dass die richtigen Indizes in Leistungs kritischen Abfragen verwendet werden, die über große Datenmengen ausgeführt werden. Das Überschreiben der Unterscheidung nach Groß-/Kleinschreibung in einer Abfrage über `EF.Functions.Collate` (oder durch Aufrufen von `string.ToLower` ) kann die Leistung Ihrer Anwendung erheblich beeinträchtigen.

## <a name="translation-of-built-in-net-string-operations"></a>Übersetzung integrierter .net-Zeichen folgen Vorgänge

In .net wird bei der Zeichen folgen Gleichheit standardmäßig die Groß-/Kleinschreibung beachtet: `s1 == s2` führt einen Ordinalvergleich durch, bei dem die Zeichen folgen identisch sein müssen. Da die Standardsortierung der Datenbanken variiert und die einfache Gleichheit bei der Verwendung von Indizes wünschenswert ist, versucht EF Core nicht, einfache Gleichheit in einen Daten Bank Vorgang zu übersetzen, bei dem die Groß-/Kleinschreibung beachtet wird: c#-Gleichheit wird direkt in SQL-Gleichheit übersetzt, bei der die Groß-/Kleinschreibung beachtet wird, je nach verwendeter und Sortierungs Konfiguration.

Außerdem bietet .net über Ladungen [`string.Equals`](https://docs.microsoft.com/dotnet/api/system.string.equals#System_String_Equals_System_String_System_StringComparison_) [`StringComparison`](https://docs.microsoft.com/dotnet/api/system.stringcomparison) , die eine-Enum akzeptieren, die die Berücksichtigung der Groß-/Kleinschreibung und der Kultur für den Vergleich ermöglicht. EF Core können diese über Ladungen nicht in SQL übersetzen, und der Versuch, Sie zu verwenden, führt zu einer Ausnahme. EF Core weiß nicht, welche Sortierung mit Beachtung der Groß-/Kleinschreibung oder ohne Beachtung der Groß-/Kleinschreibung verwendet werden sollte. Noch wichtiger ist, dass das Anwenden einer Sortierung in den meisten Fällen die Index Verwendung verhindert, wodurch sich die Leistung für ein sehr einfaches und häufig verwendetes .net-Konstrukt erheblich beeinträchtigt. Wenn Sie erzwingen möchten, dass eine Abfrage die Groß-/Kleinschreibung beachtet oder den Vergleich ohne Berücksichtigung der Groß-/Kleinschreibung verwendet, geben Sie eine Sortierung explizit über `EF.Functions.Collate` [detailed above](#explicit-collations-and-indexes)

## <a name="database-specific-information"></a>Datenbankspezifische Informationen

* [SQL Server Dokumentation zu Sortierungen](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support).
* [Microsoft. Data. sqlite-Dokumentation zu Sortierungen](https://docs.microsoft.com/dotnet/standard/data/sqlite/collation).
* [PostgreSQL-Dokumentation zu Sortierungen](https://www.postgresql.org/docs/current/collation.html).
* [MySQL-Dokumentation zu Sortierungen](https://dev.mysql.com/doc/refman/en/charset-general.html).
