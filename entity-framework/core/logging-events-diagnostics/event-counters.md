---
title: Ereignis Zähler-EF Core
description: Verfolgen der EF Core Leistung und Diagnostizieren von Anomalien mit .NET-Ereignis Indikatoren
author: roji
ms.date: 11/17/2020
uid: core/logging-events-diagnostics/event-counters
ms.openlocfilehash: 73d360b266db3d3252defbf4a4035c0eb430e22e
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635821"
---
# <a name="event-counters"></a>Ereignis Zähler

> [!NOTE]
> Dieses Feature wurde in EF Core 5.0 hinzugefügt.

Entity Framework Core (EF Core) macht kontinuierliche numerische Metriken verfügbar, die Aufschluss über die Integrität Ihres Programms geben können. Diese Metriken können für folgende Zwecke verwendet werden:

* Allgemeine Daten Bank Auslastung in Echtzeit nachverfolgen, während die Anwendung ausgeführt wird
* Machen Sie problematische Codierungs Methoden verfügbar, die zu Leistungseinbußen führen können
* Nachverfolgen und isolieren anormales Programmverhalten

EF Core Metriken über das Standard Feature für .NET-Ereignis Indikatoren aus. Es wird empfohlen, [diesen Blogbeitrag](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) zu lesen, um eine kurze Übersicht über die Funktionsweise von Leistungsindikatoren zu erhalten.

## <a name="attach-to-a-process-using-dotnet-counters"></a>Anfügen an einen Prozess mithilfe von dotnet-Counters

Das [Tool dotnet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) kann verwendet werden, um regelmäßig an einen laufenden Prozess anzufügen und EF Core Ereignis Zähler zu melden. Es müssen keine besonderen Schritte im Programm ausgeführt werden, damit diese Leistungsindikatoren verfügbar sind.

Installieren Sie zunächst das `dotnet-counters` Tool: `dotnet tool install --global dotnet-counters` .

Suchen Sie als nächstes die Prozess-ID (PID) des .NET-Prozesses, der die EF Core Anwendung ausgeführt hat:

### <a name="windows"></a>[Windows](#tab/windows)

1. Öffnen Sie den Windows Task-Manager, indem Sie mit der rechten Maustaste auf die Taskleiste klicken und "Task-Manager" auswählen.
2. Stellen Sie sicher, dass am unteren Rand des Fensters die Option "Weitere Details" ausgewählt ist.
3. Klicken Sie auf der Registerkarte Prozesse mit der rechten Maustaste auf eine Spalte, und vergewissern Sie sich, dass die Spalte PID aktiviert ist.
4. Suchen Sie in der Liste Prozess nach Ihrer Anwendung, und erhalten Sie die Prozess-ID aus der PID-Spalte.

### <a name="linux-or-macos"></a>[Linux oder macOS](#tab/fluent-api)

1. Verwenden Sie den- `ps` Befehl, um alle Prozesse aufzulisten, die für den Benutzer ausgeführt werden.
2. Suchen Sie in der Liste Prozess nach Ihrer Anwendung, und erhalten Sie die Prozess-ID aus der PID-Spalte.

***

In Ihrer .NET-Anwendung ist die Prozess-ID als verfügbar `Process.GetCurrentProcess().Id` . Dies kann zum Drucken der PID beim Start nützlich sein.

Starten Sie abschließend `dotnet-counters` wie folgt:

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p <PID>
```

`dotnet-counters` wird nun an Ihren laufenden Prozess angefügt und beginnt mit der Berichterstellung für fortlaufende Daten:

```console
Press p to pause, r to resume, q to quit.
 Status: Running

[Microsoft.EntityFrameworkCore]
    Active DbContexts                                               1
    Execution Strategy Operation Failures (Count / 1 sec)           0
    Execution Strategy Operation Failures (Total)                   0
    Optimistic Concurrency Failures (Count / 1 sec)                 0
    Optimistic Concurrency Failures (Total)                         0
    Queries (Count / 1 sec)                                         1
    Queries (Total)                                               189
    Query Cache Hit Rate (%)                                      100
    SaveChanges (Count / 1 sec)                                     0
    SaveChanges (Total)                                             0
```

## <a name="counters-and-their-meaning"></a>Zähler und ihre Bedeutung

Name des Leistungsindikators                          | BESCHREIBUNG
------------------------------------- | ----
Aktive dbkontexte                     | Die Anzahl der aktiven, nicht verworfenen dbcontext-Instanzen, die sich derzeit in der Anwendung befinden. Wenn diese Anzahl kontinuierlich zunimmt, liegt möglicherweise ein Leck vor, da dbcontext-Instanzen nicht ordnungsgemäß verworfen werden. Beachten Sie, dass bei aktiviertem [Kontext Pooling](xref:core/performance/advanced-performance-topics#dbcontext-pooling) diese Anzahl in einem Pool zusammengefasste dbcontext-Instanzen enthält, die derzeit nicht verwendet werden.
Vorgangs Fehler bei Ausführungs Strategie | Gibt an, wie oft ein Daten Bank Vorgang nicht ausgeführt werden konnte. Wenn eine Wiederholungs Ausführungs Strategie aktiviert ist, schließt dies jeden einzelnen Fehler innerhalb mehrerer Versuche desselben Vorgangs ein. Dies kann zum erkennen vorübergehender Probleme mit Ihrer-Infrastruktur verwendet werden.
Fehler bei optimistischer Parallelität       | Die Häufigkeit, mit der aufgrund eines vollständigen Parallelitäts `SaveChanges` Fehlers ein Fehler aufgetreten ist, da die Daten im Datenspeicher geändert wurden, seit Sie vom Code geladen wurden. Dies entspricht einem, das ausgelöst <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> wird.
Abfragen                               | Die Anzahl der ausgeführten Abfragen.
Abfrage Cache-Treffer Rate (%)              | Das Verhältnis von Abfragecache Treffern zu Fehlern. Wenn eine bestimmte LINQ-Abfrage zum ersten Mal von EF Core ausgeführt wird (ohne Parameter), muss Sie in einem relativ hohen Prozess kompiliert werden. In einer normalen Anwendung werden alle Abfragen wieder verwendet, und die Abfragecache-Treffer Rate sollte nach einem anfänglichen Aufwärm Zeitraum um 100% stabil sein. Wenn diese Zahl im Laufe der Zeit weniger als 100% beträgt, kann die Leistung aufgrund wiederholter Kompilierungen beeinträchtigt werden, was ein Ergebnis der suboptimalen Generierung dynamischer Abfragen sein könnte.
SaveChanges                           | Gibt an, wie oft `SaveChanges` aufgerufen wurde. Beachten Sie, dass `SaveChanges` mehrere Änderungen in einem einzelnen Batch speichert, sodass nicht notwendigerweise jedes einzelne Update für eine einzelne Entität dargestellt wird.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [.NET-Dokumentation zu Ereignis Indikatoren](https://docs.microsoft.com/dotnet/core/diagnostics/event-counters)
