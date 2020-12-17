---
title: Effiziente Abfrage-EF Core
description: Leistungs Leit Faden für effiziente Abfragen mithilfe von Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/performance/efficient-querying
ms.openlocfilehash: acd5388745e74a42925c8500ce610aef83e75384
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657714"
---
# <a name="efficient-querying"></a>Effiziente Abfragen

Das effiziente Abfragen ist ein umfassender Artikel, der Themen wie Indizes, zugehörige Entitäts Lade Strategien und viele andere Themen behandelt. In diesem Abschnitt werden einige gängige Themen zum schnelleren Ausführen von Abfragen erläutert

## <a name="use-indexes-properly"></a>Ordnungsgemäße Verwendung von Indizes

Der wichtigste Entscheidungsfaktor, ob eine Abfrage schnell ausgeführt wird oder nicht, ist, ob Sie ggf. Indizes ordnungsgemäß verwendet: Datenbanken werden normalerweise verwendet, um große Datenmengen zu speichern, und Abfragen, die ganze Tabellen durchlaufen, sind in der Regel Quellen für schwerwiegende Leistungsprobleme. Indizierungs Probleme sind nicht leicht zu erkennen, weil es nicht sofort ersichtlich ist, ob eine bestimmte Abfrage einen Index verwendet oder nicht. Beispiel:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#Indexes)]

Eine gute Möglichkeit, Indizierungs Probleme zu erkennen, besteht darin, zuerst eine langsame Abfrage zu ermitteln und dann den Abfrageplan über das bevorzugte Tool Ihrer Datenbank zu untersuchen. Weitere Informationen zur Vorgehensweise finden Sie auf der Seite " [Leistungs Diagnose](xref:core/performance/performance-diagnosis) ". Der Abfrageplan zeigt an, ob die Abfrage die gesamte Tabelle durchläuft oder einen Index verwendet.

Als allgemeine Regel gilt: Es gibt keine speziellen EF-Kenntnisse zur Verwendung von Indizes oder zur Diagnose von Leistungsproblemen, die im Zusammenhang mit Ihnen auftreten. Allgemeine Daten Bank Kenntnisse im Zusammenhang mit Indizes sind für EF-Anwendungen ebenso wichtig wie für Anwendungen, die EF nicht verwenden. Im folgenden sind einige allgemeine Richtlinien aufgeführt, die bei der Verwendung von Indizes beachtet werden sollten:

* Obwohl Indizes Abfragen beschleunigen, verlangsamen Sie auch Updates, da Sie auf dem neuesten Stand gehalten werden müssen. Vermeiden Sie die Definition von Indizes, die nicht benötigt werden, und verwenden Sie ggf. [Index Filter](xref:core/modeling/indexes#index-filter) , um den Index auf eine Teilmenge der Zeilen einzuschränken, wodurch der Aufwand verringert wird.
* Zusammengesetzte Indizes können Abfragen beschleunigen, die mehrere Spalten filtern, aber Sie können auch Abfragen beschleunigen, die nicht nach allen Spalten des Indexes Filtern (abhängig von der Reihenfolge). Ein Index für die Spalten a und b beschleunigt z. b. Abfragen, die durch a und b gefiltert werden, sowie Abfragen, die nur durch einen filtern. es werden jedoch keine Abfragen beschleunigt, die nur durch B gefiltert werden.
* Wenn eine Abfrage anhand eines Ausdrucks über eine Spalte (z. b.) filtert `price / 2` , kann kein einfacher Index verwendet werden. Sie können jedoch eine gespeicherte beibehaltene [Spalte](xref:core/modeling/generated-properties#computed-columns) für ihren Ausdruck definieren und einen Index dafür erstellen. Einige Datenbanken unterstützen auch Ausdrucks Indizes, die direkt verwendet werden können, um Abfragen zu beschleunigen, die durch einen beliebigen Ausdruck gefiltert werden.
* Mit unterschiedlichen Datenbanken können Indizes auf verschiedene Weise konfiguriert werden, und in vielen Fällen EF Core Anbieter diese über die fließende API verfügbar machen. Beispielsweise können Sie mit dem SQL Server-Anbieter konfigurieren, ob ein Index [gruppiert](xref:core/providers/sql-server/indexes#clustering)ist, oder den [Füllfaktor](xref:core/providers/sql-server/indexes#fill-factor)festlegen. Weitere Informationen finden Sie in der Dokumentation des Anbieters.

## <a name="project-only-properties-you-need"></a>Nur Projekteigenschaften, die Sie benötigen

Mit EF Core ist es sehr einfach, Entitäts Instanzen abzufragen und diese Instanzen dann im Code zu verwenden. Beim Abfragen von Entitäts Instanzen können jedoch häufig mehr Daten als notwendig aus der Datenbank abgerufen werden. Beachten Sie Folgendes:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#ProjectEntities)]

Obwohl dieser Code nur die-Eigenschaft jedes Blogs benötigt `Url` , wird die gesamte Blog-Entität abgerufen und nicht benötigte Spalten aus der Datenbank übertragen:

```sql
SELECT [b].[BlogId], [b].[CreationDate], [b].[Name], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
```

Dies kann mithilfe von optimiert werden `Select` , um EF mitzuteilen, welche Spalten zu projizieren sind:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#ProjectSingleProperty)]

Das resultierende SQL ruft nur die benötigten Spalten zurück:

```csharp
SELECT [b].[Url]
FROM [Blogs] AS [b]
```

Wenn Sie mehr als eine Spalte projizieren müssen, projizieren Sie die gewünschten Eigenschaften in einen anonymen c#-Typ.

Beachten Sie, dass diese Technik für schreibgeschützte Abfragen sehr nützlich ist. es wird jedoch komplizierter, wenn Sie die abgerufenen Blogs *Aktualisieren* müssen, da die Änderungs Nachverfolgung von EF nur mit Entitäts Instanzen funktioniert. Es ist möglich, Aktualisierungen auszuführen, ohne ganze Entitäten zu laden, indem Sie eine geänderte Blog Instanz anfügen und EF mitteilen, welche Eigenschaften geändert wurden, aber das ist eine erweiterte Technik, die sich möglicherweise nicht lohnt.

## <a name="limit-the-resultset-size"></a>Begrenzen der Größe des Resultsets

Standardmäßig gibt eine Abfrage alle Zeilen zurück, die mit Ihren Filtern übereinstimmen:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#NoLimit)]

Da die Anzahl der zurückgegebenen Zeilen von den tatsächlichen Daten in der Datenbank abhängt, ist es nicht möglich zu wissen, wie viele Daten aus der Datenbank geladen werden, wie viel Arbeitsspeicher von den Ergebnissen belegt wird und wie viel zusätzliche Auslastung bei der Verarbeitung dieser Ergebnisse generiert wird (z. b. durch Senden an einen Benutzer Browser über das Netzwerk). Entscheidend ist, dass Testdatenbanken häufig wenig Daten enthalten, sodass alles gut funktioniert, wenn die Abfrage in realen Daten ausgeführt wird und viele Zeilen zurückgegeben werden.

Daher ist es in der Regel sinnvoll, die Anzahl der Ergebnisse einzuschränken:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#Limit25)]

Die Benutzeroberfläche kann mindestens eine Meldung anzeigen, die besagt, dass in der Datenbank mehr Zeilen vorhanden sein können (und Sie auf andere Weise abrufen können). Eine vollständige Lösung würde das *Paging* implementieren, bei dem Ihre Benutzeroberfläche nur eine bestimmte Anzahl von Zeilen gleichzeitig anzeigt und Benutzern ermöglicht, bei Bedarf zur nächsten Seite zu gelangen. in der Regel werden <xref:System.Linq.Enumerable.Take%2A> die <xref:System.Linq.Enumerable.Skip%2A> Operatoren und kombiniert, um jedes Mal einen bestimmten Bereich im Resultset auszuwählen.

## <a name="avoid-cartesian-explosion-when-loading-related-entities"></a>Vermeiden der kartesischen Explosion beim Laden verwandter Entitäten

In relationalen Datenbanken werden alle zugehörigen Entitäten standardmäßig durch Einführung von JOIN-Vorgängen in eine Einzelabfrage geladen.

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

Wenn ein typischer Blog mehrere zugehörige Beiträge enthält, werden in den Zeilen für diese Beiträge die Informationen des Blogs dupliziert. Diese Duplizierung führt zum so genannten Problem der „kartesischen Explosion“. Wenn weitere 1:n-Beziehungen geladen werden, wächst die Menge an duplizierten Daten weiter und beeinträchtigt die Leistung Ihrer Anwendung.

EF ermöglicht die Vermeidung dieses Effekts durch die Verwendung von "Split Queries", die die verknüpften Entitäten über separate Abfragen laden. Weitere Informationen finden Sie [in der Dokumentation zu Split und Single Queries](xref:core/querying/single-split-queries).

> [!NOTE]
> Die aktuelle Implementierung von [Split-Abfragen](xref:core/querying/single-split-queries) führt einen Roundtrip für jede Abfrage aus. Wir planen, dies in Zukunft zu verbessern und alle Abfragen in einem einzigen Roundtrip auszuführen.

## <a name="load-related-entities-eagerly-when-possible"></a>Verwandte Entitäten nach Möglichkeit eifrig laden

Es wird empfohlen, [die dedizierte Seite für Verwandte Entitäten](xref:core/querying/related-data) zu lesen, bevor Sie mit diesem Abschnitt fortfahren.

Beim Umgang mit verwandten Entitäten wissen wir in der Regel, was wir laden müssen: ein typisches Beispiel wäre das Laden einer bestimmten Gruppe von Blogs zusammen mit allen Beiträgen. In diesen Szenarien ist es immer besser, [Eager Loading](xref:core/querying/related-data/eager)zu verwenden, damit EF alle erforderlichen Daten in einem Roundtrip abrufen kann. Das [gefilterte include](xref:core/querying/related-data/eager#filtered-include) -Feature, das in EF Core 5,0 eingeführt wurde, ermöglicht Ihnen außerdem, zu begrenzen, welche verknüpften Entitäten Sie laden möchten, während der Ladeprozess in einem einzigen Roundtrip erwartungsgemäß bleibt:

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

In anderen Szenarien wissen wir möglicherweise nicht, welche verwandte Entität wir benötigen, bevor wir die Prinzipal Entität erhalten. Wenn Sie z. b. einen Blog laden, müssen wir uns möglicherweise eine andere Datenquelle (möglicherweise einen Webdienst) ansehen, um zu erfahren, ob wir an den Beiträgen dieses Blogs interessiert sind. In diesen Fällen kann [explizites](xref:core/querying/related-data/explicit) oder [Lazy](xref:core/querying/related-data/lazy) Load verwendet werden, um verknüpfte Entitäten separat abzurufen und die Post-Navigation des Blogs aufzufüllen. Beachten Sie, dass, da diese Methoden nicht eifrig sind, zusätzliche Roundtrips zur Datenbank erforderlich sind, d. h. die Quelle der Verlangsamung. abhängig von Ihrem speziellen Szenario ist es möglicherweise effizienter, alle Beiträge immer zu laden, anstatt die zusätzlichen Roundtrips auszuführen und selektiv nur die benötigten Beiträge zu erhalten.

### <a name="beware-of-lazy-loading"></a>Vorsicht Lazy Loading

[Lazy Load](xref:core/querying/related-data/lazy) scheint eine sehr nützliche Methode zum Schreiben von Daten Bank Logik zu sein, da EF Core automatisch verknüpfte Entitäten aus der Datenbank lädt, wenn der Code auf Sie zugreift. Dadurch wird vermieden, dass verknüpfte Entitäten geladen werden, die nicht benötigt werden (z. b. [Explizites Laden](xref:core/querying/related-data/explicit)) Lazy Loading ist jedoch besonders anfällig für die Erstellung nicht benötigter zusätzlicher Roundtrips, die die Anwendung verlangsamen können.

Beachten Sie Folgendes:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#NPlusOne)]

Dieser scheinbar unschuldige Code Abschnitt durchläuft alle Blogs und ihre Beiträge und druckt sie. Beim Aktivieren der [Anweisungs Protokollierung](xref:core/logging-events-diagnostics/index) von EF Core wird Folgendes angezeigt:

```console
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      SELECT [b].[BlogId], [b].[Rating], [b].[Url]
      FROM [Blogs] AS [b]
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (5ms) [Parameters=[@__p_0='1'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[@__p_0='2'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[@__p_0='3'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0

... and so on
```

Was geht da vor? Warum werden all diese Abfragen für die oben genannten einfachen Schleifen gesendet? Bei Lazy Loading werden die Beiträge eines Blogs nur (verzögert) geladen, wenn auf seine Posts-Eigenschaft zugegriffen wird. Folglich löst jede Iterationen im Inneren foreach eine zusätzliche Datenbankabfrage in einem eigenen Roundtrip aus. Nachdem die erste Abfrage alle Blogs geladen hat, haben wir daher eine weitere Abfrage *pro Blog*, die alle Beiträge lädt. Dies wird manchmal als " *N + 1* "-Problem bezeichnet und kann zu erheblichen Leistungsproblemen führen.

Angenommen, wir benötigen alle Blogbeiträge, aber es ist sinnvoll, stattdessen Eager Loading zu verwenden. Wir können den [include](xref:core/querying/related-data/eager#eager-loading) -Operator verwenden, um das Laden auszuführen, aber da wir nur die URLs der Blogs benötigen (und nur die [benötigten Elemente laden](xref:core/performance/efficient-updating#project-only-properties-you-need)sollten). Wir verwenden stattdessen eine Projektion:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#EagerlyLoadRelatedAndProject)]

Dadurch wird EF Core alle Blogs zusammen mit ihren Beiträgen in einer einzigen Abfrage abrufen können. In einigen Fällen kann es auch hilfreich sein, kartesische Explosions Effekte mithilfe von Split- [Abfragen](xref:core/querying/single-split-queries)zu vermeiden.

> [!WARNING]
> Da Lazy Loading das Auftreten des Problems mit N + 1 äußerst einfach machen kann, empfiehlt es sich, dies zu vermeiden. Ein eifriges oder explizites Laden macht es im Quellcode ganz klar, wenn ein datenbankroundtrip auftritt.

## <a name="buffering-and-streaming"></a>Pufferung und Streaming

Die Pufferung bezieht sich auf das Laden aller Abfrageergebnisse in den Arbeitsspeicher, während Streaming bedeutet, dass EF die Anwendung jedes Mal ein einzelnes Ergebnis übergibt, ohne dass das gesamte Resultset im Arbeitsspeicher enthalten ist. Im Prinzip werden die Arbeitsspeicher Anforderungen einer streaminganfrage korrigiert. Sie sind identisch, unabhängig davon, ob die Abfrage eine Zeile oder 1000 zurückgibt. eine Puffer Abfrage hingegen erfordert mehr Arbeitsspeicher, je mehr Zeilen zurückgegeben werden. Bei Abfragen, die umfangreiche Resultsets ergeben, kann dies ein wichtiger Leistungsfaktor sein.

Ob ein Abfrage Puffer oder Datenströme von der Auswertung abhängig sind:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#BufferingAndStreaming)]

Wenn Ihre Abfragen nur ein paar Ergebnisse zurückgeben, müssen Sie sich nicht darum kümmern. Wenn die Abfrage jedoch möglicherweise eine große Anzahl von Zeilen zurückgibt, ist es sinnvoll, das Streaming anstelle von Pufferung zu überdenken.

> [!NOTE]
> Vermeiden <xref:System.Linq.Enumerable.ToList%2A> <xref:System.Linq.Enumerable.ToArray%2A> Sie die Verwendung von oder, wenn Sie einen anderen LINQ-Operator für das Ergebnis verwenden möchten. Dadurch werden alle Ergebnisse unnötig in den Arbeitsspeicher gepuffert. Verwenden Sie stattdessen <xref:System.Linq.Enumerable.AsEnumerable%2A>.

### <a name="internal-buffering-by-ef"></a>Interne Pufferung von EF

In bestimmten Situationen wird das Resultset unabhängig davon, wie Sie die Abfrage auswerten, von EF intern gepuffert. Dies sind die beiden Fälle, in denen dies geschieht:

* Wenn eine Wiederholungs Ausführungs Strategie vorhanden ist. Dadurch wird sichergestellt, dass die gleichen Ergebnisse zurückgegeben werden, wenn die Abfrage später wiederholt wird.
* Wenn [Split Query](xref:core/querying/single-split-queries) verwendet wird, werden die Resultsets aller außer der letzten Abfrage gepuffert, es sei denn, Mars ist für SQL Server aktiviert. Dies liegt daran, dass es in der Regel nicht möglich ist, mehrere Resultsets für Abfragen gleichzeitig zu aktivieren.

Beachten Sie, dass diese interne Pufferung zusätzlich zu sämtlicher Pufferung erfolgt, die Sie über LINQ-Operatoren auslösen. Wenn Sie z. b. für <xref:System.Linq.Enumerable.ToList%2A> eine Abfrage verwenden und eine Wiederholungs Ausführungs Strategie vorhanden ist, wird das Resultset *zweimal* in den Arbeitsspeicher geladen: einmal intern von EF und einmal von <xref:System.Linq.Enumerable.ToList%2A> .

## <a name="tracking-no-tracking-and-identity-resolution"></a>Nachverfolgung, keine Nachverfolgung und Identitäts Auflösung

Es wird empfohlen, [die dedizierte Seite zur Überwachung und ohne Nachverfolgung](xref:core/querying/tracking) zu lesen, bevor Sie mit diesem Abschnitt fortfahren.

EF verfolgt standardmäßig Entitäts Instanzen, sodass Änderungen an den Instanzen erkannt und persistent gespeichert werden, wenn <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aufgerufen wird. Ein weiterer Effekt bei der Nachverfolgung von Abfragen besteht darin, dass EF erkennt, ob bereits eine Instanz für Ihre Daten geladen wurde, und dass diese überwachte Instanz automatisch zurückgegeben wird, anstatt eine neue Instanz zurückzugeben. Dies wird als *Identitäts Auflösung* bezeichnet. Im Hinblick auf die Leistung bedeutet die Änderungs Nachverfolgung Folgendes:

* EF verwaltet intern ein Wörterbuch mit nach verfolgten Instanzen. Wenn neue Daten geladen werden, prüft EF das Wörterbuch, um festzustellen, ob bereits eine Instanz für den Schlüssel der Entität (Identitäts Auflösung) nachverfolgt wird. Die Wörterbuch Wartung und Suchvorgänge nehmen einige Zeit in Anspruch, wenn die Ergebnisse der Abfrage geladen werden.
* Bevor eine geladene Instanz an die Anwendung übergeben *wird, erstellt EF diese* Instanz und speichert die Momentaufnahme intern. Wenn <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aufgerufen wird, wird die Instanz der Anwendung mit der Momentaufnahme verglichen, um die Änderungen zu ermitteln, die persistent gespeichert werden sollen. Die Momentaufnahme benötigt mehr Arbeitsspeicher, und der Snapshotprozess selbst nimmt Zeit in Rechnung. Manchmal ist es möglich, ein anderes, möglicherweise effizienteres snapshotverhalten über [Wert Vergleiche](xref:core/modeling/value-comparers)anzugeben oder Proxys für die Änderungs Nachverfolgung zu verwenden, um den Snapshotprozess vollständig zu umgehen (Dies ist jedoch mit einem eigenen Satz von Nachteilen verbunden).

In schreibgeschützten Szenarien, in denen Änderungen nicht in der Datenbank gespeichert werden, können die oben genannten Aufwand mithilfe von [Abfragen ohne Nachverfolgung](xref:core/querying/tracking#no-tracking-queries)vermieden werden. Da keine nach Verfolgungs Abfragen jedoch keine Identitäts Auflösung durchführen, wird eine Datenbankzeile, auf die von mehreren anderen geladenen Zeilen verwiesen wird, als unterschiedliche Instanzen materialisiert.

Um dies zu veranschaulichen, nehmen wir an, dass wir eine große Anzahl von Beiträgen aus der Datenbank laden, ebenso wie der Blog, auf den jeder Beitrag verweist. Wenn 100 Beiträge auf denselben Blog verweisen, erkennt eine Überwachungs Abfrage dies über die Identitäts Auflösung, und alle Post-Instanzen verweisen auf dieselbe deduplizierte Blog Instanz. Eine Abfrage ohne Nachverfolgung hingegen dupliziert denselben Blog 100-Mal, und der Anwendungscode muss entsprechend geschrieben werden.

Im folgenden finden Sie die Ergebnisse für einen Vergleichstest zum Vergleichen von Nachverfolgung und ohne Nachverfolgung für eine Abfrage, bei der 10 Blogs mit 20 Beiträgen geladen werden. [Der Quellcode ist hier verfügbar](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Benchmarks/QueryTrackingBehavior.cs). Sie können ihn als Grundlage für Ihre eigenen Messungen verwenden.

|       Methode | Numblogs | Numpostsperblog |       Mittelwert |    Fehler |   StdDev |     Median | Seitenverhältnis | Ratiosd |   Gen 0 |   Gen 1 | Gen 2 | Zugeordnet |
|------------- |--------- |---------------- |-----------:|---------:|---------:|-----------:|------:|--------:|--------:|--------:|------:|----------:|
|   Astracking |       10 |              20 | 1.414,7 US- | 27,20 US- | 45,44 US- | 1.405,5 US- |  1.00 |    0.00 | 60,5469 | 13,6719 |     - | 380,11 KB |
| AsNoTracking |       10 |              20 |   993,3 US- | 24,04 US- | 65,40 US- |   966,2 US- |  0.71 |    0.05 | 37,1094 |  6,8359 |     - | 232,89 KB |

Schließlich ist es möglich, Updates ohne den Aufwand der Änderungs Nachverfolgung durchzuführen, indem eine Abfrage ohne Nachverfolgung genutzt und dann die zurückgegebene Instanz an den Kontext angefügt wird. dabei wird angegeben, welche Änderungen vorgenommen werden sollen. Dies überträgt die Last der Änderungs Nachverfolgung von EF auf den Benutzer und sollte nur dann versucht werden, wenn der mehr Aufwand für die Änderungs Nachverfolgung durch Profilerstellung oder Benchmarktests als nicht akzeptabel angezeigt wird.

## <a name="using-raw-sql"></a>Verwenden von RAW SQL

In einigen Fällen ist eine optimierte SQL-Datei für die Abfrage vorhanden, die EF nicht generiert. Dies kann der Fall sein, wenn das SQL-Konstrukt eine Erweiterung für Ihre Datenbank ist, die nicht unterstützt wird, oder nur, weil EF noch nicht in die Datenbank übersetzt wird. In diesen Fällen kann das Schreiben von SQL durch Hand eine beträchtliche Leistungssteigerung bieten, und EF unterstützt mehrere Möglichkeiten.

* Verwenden Sie Rohdaten von SQL [direkt in der Abfrage](xref:core/querying/raw-sql), z. b. über <xref:Microsoft.EntityFrameworkCore.RelationalQueryableExtensions.FromSqlRaw%2A> . EF ermöglicht Ihnen sogar das Verfassen von SQL-Rohdaten mit regulären LINQ-Abfragen, sodass Sie nur einen Teil der Abfrage in unformatierten SQL-Daten Ausdrücken können. Dies ist eine gute Methode, wenn die unformatierte SQL-Datei nur in einer einzelnen Abfrage in ihrer CodeBase verwendet werden muss.
* Definieren Sie eine [benutzerdefinierte Funktion (User-Defined Function](xref:core/querying/database-functions) , UDF), und nennen Sie Sie dann in Ihren Abfragen. Beachten Sie, dass EF seit 5,0 das Zurückgeben von vollständigen Resultsets ermöglicht. Diese werden als Tabellenwert Funktionen (Table-Wert Functions, TVFs) bezeichnet und ermöglichen außerdem die Zuordnung einer `DbSet` zu einer Funktion, sodass Sie genau wie in einer anderen Tabelle aussieht.
* Definieren Sie eine Daten Bank Sicht, und Fragen Sie Sie in Ihren Abfragen ab. Beachten Sie, dass Sichten im Gegensatz zu Funktionen keine Parameter annehmen können.

> [!NOTE]
> Unformatierte SQL-Daten sollten in der Regel als letztes Mittel verwendet werden, nachdem sichergestellt wurde, dass EF das gewünschte SQL nicht generieren kann, und wenn die Leistung für die jeweilige Abfrage recht wichtig ist. Die Verwendung von RAW SQL bringt erhebliche Wartungs Nachteile mit sich.

## <a name="asynchronous-programming"></a>Asynchrone Programmierung

Als allgemeine Regel gilt: Wenn die Anwendung skalierbar sein soll, ist es wichtig, immer asynchrone APIs anstelle von synchronen APIs zu verwenden (z. b. <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> anstelle von <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ). Synchrone APIs blockieren den Thread für die Dauer der Datenbank-e/a, wodurch die Notwendigkeit von Threads und die Anzahl von Thread Kontext Schaltern erhöht werden muss.

Weitere Informationen finden Sie auf der Seite zu [Async-Programmierung](xref:core/miscellaneous/async).

> [!WARNING]
> Vermeiden Sie das Kombinieren von synchronem und asynchronem Code in derselben Anwendung. es ist sehr einfach, Probleme mit einem geringfügigen Thread Pool zu beheben.
