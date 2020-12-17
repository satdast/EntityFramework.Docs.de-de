---
title: Leistungs Diagnose-EF Core
description: Diagnostizieren Entity Framework Core Leistung und Identifizieren von Engpässen
author: roji
ms.date: 12/1/2020
uid: core/performance/performance-diagnosis
ms.openlocfilehash: 9416acf3326056ef7a5d732c4bd456dac751167b
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657713"
---
# <a name="performance-diagnosis"></a>Leistungs Diagnose

In diesem Abschnitt wird erläutert, wie Sie Leistungsprobleme in ihrer EF-Anwendung erkennen. Nachdem ein problematischer Bereich identifiziert wurde, können Sie diese weiter analysieren, um das Problem zu identifizieren. Es ist wichtig, alle Probleme sorgfältig zu diagnostizieren und zu untersuchen, bevor Sie zu den Schlussfolgerungen springen, und um zu vermeiden, dass die Ursache des Problems ist.

## <a name="identifying-slow-database-commands-via-logging"></a>Identifizieren langsamer Daten Bank Befehle über die Protokollierung

Am Ende des Tages werden von EF die Befehle vorbereitet und ausgeführt, die für die Datenbank ausgeführt werden. bei relationalen Datenbanken bedeutet das, SQL-Anweisungen über die ADO.net-Datenbank-API auszuführen. Wenn eine bestimmte Abfrage zu viel Zeit in Anspruch nimmt (z. b. weil ein Index fehlt), kann dies erkannt werden, indem die Befehle zur Befehlsausführung überprüft werden und Sie beobachten, wie lange Sie tatsächlich dauern.

EF vereinfacht das Erfassen von Ausführungszeiten von Befehlen sehr einfach über [einfache Protokollierung](xref:core/logging-events-diagnostics/simple-logging) oder [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging):

### <a name="simple-logging"></a>[Einfache Protokollierung](#tab/simple-logging)

[!code-csharp[Main](../../../samples/core/Performance/BloggingContext.cs#SimpleLogging)]

### <a name="microsoftextensionslogging"></a>[Microsoft.Extensions.Logging](#tab/microsoft-extensions-logging)

[!code-csharp[Main](../../../samples/core/Performance/ExtensionsLoggingContext.cs#ExtensionsLogging)]

***

Wenn der Protokolliergrad auf festgelegt ist `LogLevel.Information` , gibt EF eine Protokollmeldung für jede Befehlsausführung mit der folgenden Zeit aus:

```log
info: 06/12/2020 09:12:36.117 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (4ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      SELECT [b].[Id], [b].[Name]
      FROM [Blogs] AS [b]
      WHERE [b].[Name] = N'foo'
```

Der obige Befehl hat 4 Millisekunden gedauert. Wenn ein bestimmter Befehl mehr als erwartet erfordert, haben Sie einen möglichen Fehler bei einem Leistungsproblem festgestellt und können sich nun darauf konzentrieren, um zu verstehen, warum er langsam ausgeführt wird. Die Befehls Protokollierung kann auch Fälle anzeigen, in denen unerwartete Datenbankroundtrips durchgeführt werden. Dies würde als mehrere Befehle angezeigt werden, bei denen nur eine erwartet wird.

> [!WARNING]
> Es ist in der Regel eine gute Idee, die Protokollierung der Befehlsausführung in der Produktionsumgebung zu aktivieren. Die Protokollierung selbst verlangsamt Ihre Anwendung und kann schnell sehr große Protokolldateien erstellen, die den Datenträger des Servers auffüllen können. Es wird empfohlen, sich nur für ein kurzes Zeitintervall anzumelden, um Daten zu sammeln, während Sie Ihre Anwendung sorgfältig überwachen oder Protokollierungs Daten auf einem präproduktionssystem erfassen.

## <a name="correlating-database-commands-to-linq-queries"></a>Korrelieren von Daten Bank Befehlen mit LINQ-Abfragen

Ein Problem bei der Protokollierung der Befehlsausführung ist, dass es manchmal schwierig ist, SQL-Abfragen und LINQ-Abfragen zu korrelieren: die von EF ausgeführten SQL-Befehle können sich von den LINQ-Abfragen unterscheiden, von denen Sie generiert wurden. Zur Unterstützung dieser Schwierigkeit empfiehlt es sich, das Feature " [Abfrage Tags](xref:core/querying/tags) " von EF zu verwenden, mit dem Sie einen kleinen, identifizierenden Kommentar in die SQL-Abfrage einfügen können:

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

Das Tag wird in den Protokollen angezeigt:

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

Häufig ist es sinnvoll, die wichtigsten Abfragen einer Anwendung auf diese Weise zu markieren, damit die Befehlsausführung die Protokolle schneller lesbar wird.

## <a name="other-interfaces-for-capturing-performance-data"></a>Weitere Schnittstellen zur Erfassung von Leistungsdaten

Es gibt verschiedene Alternativen zum Protokollierungs Feature von EF zum Erfassen von Ausführungszeiten von Befehlen, die möglicherweise leistungsfähiger sind. Datenbanken verfügen in der Regel über eigene Ablaufverfolgungs-und Leistungsanalyse Tools, die in der Regel umfangreichere, datenbankspezifische Informationen über einfache Ausführungszeiten bereitstellen. die tatsächliche Einrichtung, die Funktionen und die Verwendung variieren in den Datenbanken erheblich.

[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ist beispielsweise ein leistungsfähiger Client, der eine Verbindung mit Ihrer SQL Server Instanz herstellen und wertvolle Verwaltungs-und Leistungsinformationen bereitstellen kann. Es geht über den Rahmen dieses Abschnitts hinaus, die Details zu überschreiten, aber zwei zu beachnende Funktionen sind der [Aktivitäts Monitor](/sql/relational-databases/performance-monitor/open-activity-monitor-sql-server-management-studio), der ein Live-Dashboard der Serveraktivität (einschließlich der teuersten Abfragen) und das Feature für [Erweiterte Ereignisse (XEvent)](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) bereitstellt, mit dem Sie beliebige Daten Erfassungs Sitzungen definieren können, die auf ihre exakten Anforderungen zugeschnitten sind. In [der SQL Server Dokumentation zur Überwachung](/sql/relational-databases/performance/monitor-and-tune-for-performance) finden Sie weitere Informationen zu diesen Features und anderen.

Ein weiterer Ansatz zur Erfassung von Leistungsdaten ist die Erfassung von Informationen, die entweder von EF oder dem Datenbanktreiber über die- `DiagnosticSource` Schnittstelle automatisch ausgegeben werden, und dann die Analyse dieser Daten oder die Anzeige auf einem Dashboard. Wenn Sie Azure verwenden, bietet [Azure-Anwendung Insights](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-performance) standardmäßig eine solche leistungsfähige Überwachung, die Datenbankleistung und Abfrage Ausführungszeiten in die Analyse der Art und Weise, wie schnell Ihre Webanforderungen verarbeitet werden, zu integrieren. Weitere Informationen hierzu finden Sie im Tutorial zur [Application Insights Leistung](/azure/azure-monitor/learn/tutorial-performance)und auf der [Seite Azure SQL-Analyse](/azure/azure-monitor/insights/azure-sql).

## <a name="inspecting-query-execution-plans"></a>Überprüfen von Abfrage Ausführungsplänen

Wenn Sie auf eine problematische Abfrage geklickt haben, die optimiert werden muss, wird im nächsten Schritt in der Regel der *Ausführungsplan* der Abfrage analysiert. Wenn Datenbanken eine SQL-Anweisung empfangen, wird in der Regel ein Plan zur Ausführung des Plans erzeugt. Dies erfordert mitunter eine komplizierte Entscheidungsfindung, je nachdem, welche Indizes definiert wurden, wie viele Daten in Tabellen vorhanden sind usw. (der Plan selbst sollte in der Regel auf dem Server zwischengespeichert werden, um eine optimale Leistung zu erzielen). Relationale Datenbanken bieten Benutzern in der Regel die Möglichkeit, den Abfrageplan zusammen mit der berechneten Kosten für verschiedene Teile der Abfrage anzuzeigen. Dies ist für die Verbesserung Ihrer Abfragen von Bedeutung.

Informationen zu den ersten Schritten mit SQL Server finden Sie in der Dokumentation zu [Abfrage Ausführungsplänen](/sql/relational-databases/performance/execution-plans). Der typische Analyse Workflow wäre, [SQL Server Management Studio](/sql/relational-databases/performance/display-an-actual-execution-plan)zu verwenden, das SQL einer langsamen Abfrage, die über eine der oben genannten Mittel festgelegt wurde, zu einfügen und [einen grafischen Ausführungsplan zu erstellen](/sql/relational-databases/performance/display-an-actual-execution-plan):

![Anzeigen eines SQL Server Ausführungs Plans](_static/actualexecplan.png)

Obwohl Ausführungspläne zunächst kompliziert erscheinen, ist es sinnvoll, sich mit Ihnen vertraut zu machen. Es ist besonders wichtig, die mit den einzelnen Knoten des Plans verbundenen Kosten zu notieren und zu ermitteln, wie Indizes in den verschiedenen Knoten verwendet werden (oder nicht).

Obwohl die oben genannten Informationen für SQL Server spezifisch sind, bieten andere Datenbanken in der Regel die gleiche Art von Tools mit ähnlicher Visualisierung.

> [!IMPORTANT]
> Datenbanken generieren manchmal unterschiedliche Abfrage Pläne, abhängig von den tatsächlichen Daten in der Datenbank. Wenn eine Tabelle z. b. nur wenige Zeilen enthält, kann eine Datenbank auswählen, dass kein Index für diese Tabelle verwendet wird, sondern stattdessen ein vollständiger Tabellenscan durchgeführt werden soll. Wenn Sie Abfrage Pläne für eine Testdatenbank analysieren, stellen Sie immer sicher, dass Sie Daten enthält, die dem Produktionssystem ähneln.

## <a name="event-counters"></a>Ereignisindikatoren

In den obigen Abschnitten wurde erläutert, wie Sie Informationen zu ihren Befehlen erhalten und wie diese Befehle in der Datenbank ausgeführt werden. Darüber hinaus macht EF eine Reihe von *Ereignis* Indikatoren verfügbar, die genauere Informationen zu den Vorgängen in EF und ihrer Anwendung bereitstellen. Diese Leistungsindikatoren können sehr nützlich sein, um bestimmte Leistungsprobleme und Leistungs Anomalien zu diagnostizieren, wie z. b. Probleme bei der [Abfrage](xref:core/performance/advanced-performance-topics#dynamically-constructed-queries) Zwischenspeicherung, die eine Konstante Neukompilierung, nicht ausgelegte dbcontext-Lecks und andere verursachen

Weitere Informationen finden Sie auf der dedizierten Seite zu den Ereignis Indikatoren von [EF](xref:core/logging-events-diagnostics/event-counters) .

## <a name="benchmarking-with-ef-core"></a>Benchmarktests mit EF Core

Am Ende des Tages müssen Sie manchmal wissen, ob eine bestimmte Methode zum Schreiben oder Ausführen einer Abfrage schneller als eine andere ist. Es ist wichtig, die Antwort niemals zu übernehmen oder zu überdenken, und es ist äußerst einfach, einen schnellen Benchmark zu kombinieren, um die Antwort zu erhalten. Beim Schreiben von Benchmarks wird dringend empfohlen, die bekannte [benchmarkdotnet](https://benchmarkdotnet.org/index.html) -Bibliothek zu verwenden, die viele Fehler behandelt, die Benutzer beim Schreiben Ihrer eigenen Benchmarks treffen: haben Sie einige Aufwärm Iterationen durchgeführt? Wie viele Iterationen werden tatsächlich durchgeführt und warum? Sehen wir uns an, wie ein Benchmark mit EF Core aussieht.

> [!TIP]
> Das vollständige Benchmark-Projekt für die unten stehende Quelle ist [hier](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Benchmarks/AverageBlogRanking.cs)verfügbar. Es wird empfohlen, die Datei zu kopieren und als Vorlage für Ihre eigenen Benchmarks zu verwenden.

Als einfaches Vergleichs Szenario vergleichen wir die folgenden verschiedenen Methoden zum Berechnen der durchschnittlichen Rangfolge aller Blogs in unserer Datenbank:

* Laden Sie alle Entitäten, addieren Sie Ihre einzelnen Rang folgen, und berechnen Sie den Durchschnitt.
* Verwenden Sie wie oben beschrieben nur eine Abfrage ohne Nachverfolgung. Dies sollte schneller erfolgen, da die Identitäts Auflösung nicht durchgeführt wird und die Entitäten für die Änderungs Nachverfolgung nicht per snapshotet werden.
* Vermeiden Sie das Laden der gesamten blobentitätsinstanzen, indem Sie nur die Rangfolge projizieren. Das speichert uns daran, die anderen, nicht benötigten Spalten des Blog-Entitäts Typs zu übertragen.
* Berechnen Sie den Durchschnitt in der Datenbank, indem Sie ihn in die Abfrage einteilen. Dies sollte die schnellste Methode sein, da alles in der Datenbank berechnet wird und nur das Ergebnis zurück an den Client übertragen wird.

Mit benchmarkdotnet schreiben Sie den Code, der als einfache Methode bezeichnet werden soll (genau wie ein Komponenten Test), und benchmarkdotnet führt automatisch jede Methode für eine ausreichende Anzahl von Iterationen aus, wobei zuverlässig gemessen wird, wie lange es dauert und wie viel Arbeitsspeicher zugeordnet wird. Hier sehen Sie die verschiedenen Methoden ([der vollständige Benchmarkcode finden Sie hier](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Benchmarks/AverageBlogRanking.cs)):

### <a name="load-entities"></a>[Laden von Entitäten](#tab/load-entities)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=LoadEntities)]

### <a name="load-entities-no-tracking"></a>[Laden von Entitäten, keine Nachverfolgung](#tab/load-entities-no-tracking)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=LoadEntitiesNoTracking)]

### <a name="project-only-ranking"></a>[Nur Projekt Rangfolge](#tab/project-only-ranking)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=ProjectOnlyRanking)]

### <a name="calculate-in-database"></a>[In Datenbank berechnen](#tab/calculate-in-database)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=CalculateInDatabase)]

***

Die Ergebnisse sind unten aufgeführt, wie von benchmarkdotnet gedruckt:

|                 Methode |       Mittelwert |    Fehler |   StdDev |     Median | Seitenverhältnis | Ratiosd |    Gen 0 |   Gen 1 | Gen 2 |  Zugeordnet |
|----------------------- |-----------:|---------:|---------:|-----------:|------:|--------:|---------:|--------:|------:|-----------:|
|           Loadentities | 2.860,4 US- | 54,31 US- | 93,68 US- | 2.844,5 US- |  4.55 |    0,33 | 210,9375 | 70,3125 |     - | 1309,56 KB |
| Loadentitiesnotracking | 1.353,0 US- | 21,26 US- | 18,85 US- | 1.355,6 US- |  2.10 |    0.14 |  87,8906 |  3,9063 |     - |  540,09 KB |
|     Projectonlyrangfolge |   910,9 US- | 20,91 US- | 61,65 US- |   892,9 US- |  1,46 |    0.14 |  41,0156 |  0,9766 |     - |  252,08 KB |
|    Calculateingedatabase |   627,1 US- | 14,58 US- | 42,54 US- |   626,4 US- |  1.00 |    0.00 |   4,8828 |       - |     - |   33,27 KB |

> [!NOTE]
> Wenn die-Methoden den Kontext in der-Methode instanziieren und verwerfen, werden diese Vorgänge für den Benchmarkwert gezählt, obwohl Sie streng genommen nicht Teil des Abfrage Prozesses sind. Dies sollte nicht von Bedeutung sein, wenn zwei Alternativen miteinander verglichen werden sollen (da die Kontext Instanziierung und-Entsorgung identisch sind) und eine stärker ganzheitliche Messung für den gesamten Vorgang durchführt.

Eine Einschränkung von benchmarkdotnet besteht darin, dass Sie eine einfache Leistung der einzelnen Threads der von Ihnen bereitgestellten Methoden misst und daher nicht für benchmarkübergreifende Szenarios geeignet ist.

> [!IMPORTANT]
> Stellen Sie immer sicher, dass Daten in der Datenbank vorhanden sind, die Produktionsdaten beim benchmarkvorgang ähneln; andernfalls stellen die Vergleichstests möglicherweise nicht die tatsächliche Leistung in der Produktion dar.
