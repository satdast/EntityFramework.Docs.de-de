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
# <a name="event-counters"></a><span data-ttu-id="85866-103">Ereignis Zähler</span><span class="sxs-lookup"><span data-stu-id="85866-103">Event Counters</span></span>

> [!NOTE]
> <span data-ttu-id="85866-104">Dieses Feature wurde in EF Core 5.0 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="85866-104">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="85866-105">Entity Framework Core (EF Core) macht kontinuierliche numerische Metriken verfügbar, die Aufschluss über die Integrität Ihres Programms geben können.</span><span class="sxs-lookup"><span data-stu-id="85866-105">Entity Framework Core (EF Core) exposes continuous numeric metrics which can provide a good indication of your program's health.</span></span> <span data-ttu-id="85866-106">Diese Metriken können für folgende Zwecke verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="85866-106">These metrics can be used for the following purposes:</span></span>

* <span data-ttu-id="85866-107">Allgemeine Daten Bank Auslastung in Echtzeit nachverfolgen, während die Anwendung ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="85866-107">Track general database load in realtime as the application is running</span></span>
* <span data-ttu-id="85866-108">Machen Sie problematische Codierungs Methoden verfügbar, die zu Leistungseinbußen führen können</span><span class="sxs-lookup"><span data-stu-id="85866-108">Expose problematic coding practices which can lead to degraded performance</span></span>
* <span data-ttu-id="85866-109">Nachverfolgen und isolieren anormales Programmverhalten</span><span class="sxs-lookup"><span data-stu-id="85866-109">Track down and isolate anomalous program behavior</span></span>

<span data-ttu-id="85866-110">EF Core Metriken über das Standard Feature für .NET-Ereignis Indikatoren aus. Es wird empfohlen, [diesen Blogbeitrag](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) zu lesen, um eine kurze Übersicht über die Funktionsweise von Leistungsindikatoren zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="85866-110">EF Core reports metrics via the standard .NET event counters feature; it's recommended to read [this blog post](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) for a quick overview of how counters work.</span></span>

## <a name="attach-to-a-process-using-dotnet-counters"></a><span data-ttu-id="85866-111">Anfügen an einen Prozess mithilfe von dotnet-Counters</span><span class="sxs-lookup"><span data-stu-id="85866-111">Attach to a process using dotnet-counters</span></span>

<span data-ttu-id="85866-112">Das [Tool dotnet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) kann verwendet werden, um regelmäßig an einen laufenden Prozess anzufügen und EF Core Ereignis Zähler zu melden. Es müssen keine besonderen Schritte im Programm ausgeführt werden, damit diese Leistungsindikatoren verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="85866-112">The [dotnet-counters tool](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) can be used to attach to a running process and report EF Core event counters regularly; nothing special needs to be done in the program for these counters to be available.</span></span>

<span data-ttu-id="85866-113">Installieren Sie zunächst das `dotnet-counters` Tool: `dotnet tool install --global dotnet-counters` .</span><span class="sxs-lookup"><span data-stu-id="85866-113">First, install the `dotnet-counters` tool: `dotnet tool install --global dotnet-counters`.</span></span>

<span data-ttu-id="85866-114">Suchen Sie als nächstes die Prozess-ID (PID) des .NET-Prozesses, der die EF Core Anwendung ausgeführt hat:</span><span class="sxs-lookup"><span data-stu-id="85866-114">Next, find the process ID (PID) of the .NET process running your EF Core application:</span></span>

### <a name="windows"></a>[<span data-ttu-id="85866-115">Windows</span><span class="sxs-lookup"><span data-stu-id="85866-115">Windows</span></span>](#tab/windows)

1. <span data-ttu-id="85866-116">Öffnen Sie den Windows Task-Manager, indem Sie mit der rechten Maustaste auf die Taskleiste klicken und "Task-Manager" auswählen.</span><span class="sxs-lookup"><span data-stu-id="85866-116">Open the Windows Task Manager by right-clicking on the task bar and selecting "Task Manager".</span></span>
2. <span data-ttu-id="85866-117">Stellen Sie sicher, dass am unteren Rand des Fensters die Option "Weitere Details" ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="85866-117">Make sure that the "More details" option is selected at the bottom of the window.</span></span>
3. <span data-ttu-id="85866-118">Klicken Sie auf der Registerkarte Prozesse mit der rechten Maustaste auf eine Spalte, und vergewissern Sie sich, dass die Spalte PID aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="85866-118">In the Processes tab, right-click a column and make sure that the PID column is enabled.</span></span>
4. <span data-ttu-id="85866-119">Suchen Sie in der Liste Prozess nach Ihrer Anwendung, und erhalten Sie die Prozess-ID aus der PID-Spalte.</span><span class="sxs-lookup"><span data-stu-id="85866-119">Locate your application in the process list, and get its process ID from the PID column.</span></span>

### <a name="linux-or-macos"></a>[<span data-ttu-id="85866-120">Linux oder macOS</span><span class="sxs-lookup"><span data-stu-id="85866-120">Linux or macOS</span></span>](#tab/fluent-api)

1. <span data-ttu-id="85866-121">Verwenden Sie den- `ps` Befehl, um alle Prozesse aufzulisten, die für den Benutzer ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="85866-121">Use the `ps` command to list all processes running for your user.</span></span>
2. <span data-ttu-id="85866-122">Suchen Sie in der Liste Prozess nach Ihrer Anwendung, und erhalten Sie die Prozess-ID aus der PID-Spalte.</span><span class="sxs-lookup"><span data-stu-id="85866-122">Locate your application in the process list, and get its process ID from the PID column.</span></span>

***

<span data-ttu-id="85866-123">In Ihrer .NET-Anwendung ist die Prozess-ID als verfügbar `Process.GetCurrentProcess().Id` . Dies kann zum Drucken der PID beim Start nützlich sein.</span><span class="sxs-lookup"><span data-stu-id="85866-123">Inside your .NET application, the process ID is available as `Process.GetCurrentProcess().Id`; this can be useful for printing the PID upon startup.</span></span>

<span data-ttu-id="85866-124">Starten Sie abschließend `dotnet-counters` wie folgt:</span><span class="sxs-lookup"><span data-stu-id="85866-124">Finally, launch `dotnet-counters` as follows:</span></span>

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p <PID>
```

<span data-ttu-id="85866-125">`dotnet-counters` wird nun an Ihren laufenden Prozess angefügt und beginnt mit der Berichterstellung für fortlaufende Daten:</span><span class="sxs-lookup"><span data-stu-id="85866-125">`dotnet-counters` will now attach to your running process and start reporting continuous counter data:</span></span>

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

## <a name="counters-and-their-meaning"></a><span data-ttu-id="85866-126">Zähler und ihre Bedeutung</span><span class="sxs-lookup"><span data-stu-id="85866-126">Counters and their meaning</span></span>

<span data-ttu-id="85866-127">Name des Leistungsindikators</span><span class="sxs-lookup"><span data-stu-id="85866-127">Counter name</span></span>                          | <span data-ttu-id="85866-128">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="85866-128">Description</span></span>
------------------------------------- | ----
<span data-ttu-id="85866-129">Aktive dbkontexte</span><span class="sxs-lookup"><span data-stu-id="85866-129">Active DbContexts</span></span>                     | <span data-ttu-id="85866-130">Die Anzahl der aktiven, nicht verworfenen dbcontext-Instanzen, die sich derzeit in der Anwendung befinden.</span><span class="sxs-lookup"><span data-stu-id="85866-130">The number of active, undisposed DbContext instances currently in your application.</span></span> <span data-ttu-id="85866-131">Wenn diese Anzahl kontinuierlich zunimmt, liegt möglicherweise ein Leck vor, da dbcontext-Instanzen nicht ordnungsgemäß verworfen werden.</span><span class="sxs-lookup"><span data-stu-id="85866-131">If this number grows continuously, you may have a leak because DbContext instances aren't being properly disposed.</span></span> <span data-ttu-id="85866-132">Beachten Sie, dass bei aktiviertem [Kontext Pooling](xref:core/performance/advanced-performance-topics#dbcontext-pooling) diese Anzahl in einem Pool zusammengefasste dbcontext-Instanzen enthält, die derzeit nicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="85866-132">Note that if [context pooling](xref:core/performance/advanced-performance-topics#dbcontext-pooling) is enabled, this number includes pooled DbContext instances not currently in use.</span></span>
<span data-ttu-id="85866-133">Vorgangs Fehler bei Ausführungs Strategie</span><span class="sxs-lookup"><span data-stu-id="85866-133">Execution Strategy Operation Failures</span></span> | <span data-ttu-id="85866-134">Gibt an, wie oft ein Daten Bank Vorgang nicht ausgeführt werden konnte.</span><span class="sxs-lookup"><span data-stu-id="85866-134">The number of times a database operation failed to execute.</span></span> <span data-ttu-id="85866-135">Wenn eine Wiederholungs Ausführungs Strategie aktiviert ist, schließt dies jeden einzelnen Fehler innerhalb mehrerer Versuche desselben Vorgangs ein.</span><span class="sxs-lookup"><span data-stu-id="85866-135">If a retrying execution strategy is enabled, this includes each individual failure within multiple attempts on the same operation.</span></span> <span data-ttu-id="85866-136">Dies kann zum erkennen vorübergehender Probleme mit Ihrer-Infrastruktur verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="85866-136">This can be used to detect transient issues with your infrastructure.</span></span>
<span data-ttu-id="85866-137">Fehler bei optimistischer Parallelität</span><span class="sxs-lookup"><span data-stu-id="85866-137">Optimistic Concurrency Failures</span></span>       | <span data-ttu-id="85866-138">Die Häufigkeit, mit der aufgrund eines vollständigen Parallelitäts `SaveChanges` Fehlers ein Fehler aufgetreten ist, da die Daten im Datenspeicher geändert wurden, seit Sie vom Code geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="85866-138">The number of times `SaveChanges` failed because of an optimistic concurrency error, because data in the data store was changed since your code loaded it.</span></span> <span data-ttu-id="85866-139">Dies entspricht einem, das ausgelöst <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> wird.</span><span class="sxs-lookup"><span data-stu-id="85866-139">This corresponds to a <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> being thrown.</span></span>
<span data-ttu-id="85866-140">Abfragen</span><span class="sxs-lookup"><span data-stu-id="85866-140">Queries</span></span>                               | <span data-ttu-id="85866-141">Die Anzahl der ausgeführten Abfragen.</span><span class="sxs-lookup"><span data-stu-id="85866-141">The number of queries executed.</span></span>
<span data-ttu-id="85866-142">Abfrage Cache-Treffer Rate (%)</span><span class="sxs-lookup"><span data-stu-id="85866-142">Query Cache Hit Rate (%)</span></span>              | <span data-ttu-id="85866-143">Das Verhältnis von Abfragecache Treffern zu Fehlern.</span><span class="sxs-lookup"><span data-stu-id="85866-143">The ratio of query cache hits to misses.</span></span> <span data-ttu-id="85866-144">Wenn eine bestimmte LINQ-Abfrage zum ersten Mal von EF Core ausgeführt wird (ohne Parameter), muss Sie in einem relativ hohen Prozess kompiliert werden.</span><span class="sxs-lookup"><span data-stu-id="85866-144">The first time a given LINQ query is executed by EF Core (excluding parameters), it must be compiled in what is a relatively heavy process.</span></span> <span data-ttu-id="85866-145">In einer normalen Anwendung werden alle Abfragen wieder verwendet, und die Abfragecache-Treffer Rate sollte nach einem anfänglichen Aufwärm Zeitraum um 100% stabil sein.</span><span class="sxs-lookup"><span data-stu-id="85866-145">In a normal application, all queries are reused, and the query cache hit rate should be stable at 100% after an initial warmup period.</span></span> <span data-ttu-id="85866-146">Wenn diese Zahl im Laufe der Zeit weniger als 100% beträgt, kann die Leistung aufgrund wiederholter Kompilierungen beeinträchtigt werden, was ein Ergebnis der suboptimalen Generierung dynamischer Abfragen sein könnte.</span><span class="sxs-lookup"><span data-stu-id="85866-146">If this number is less than 100% over time, you may experience degraded perf due to repeated compilations, which could be a result of suboptimal dynamic query generation.</span></span>
<span data-ttu-id="85866-147">SaveChanges</span><span class="sxs-lookup"><span data-stu-id="85866-147">SaveChanges</span></span>                           | <span data-ttu-id="85866-148">Gibt an, wie oft `SaveChanges` aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="85866-148">The number of times `SaveChanges` has been called.</span></span> <span data-ttu-id="85866-149">Beachten Sie, dass `SaveChanges` mehrere Änderungen in einem einzelnen Batch speichert, sodass nicht notwendigerweise jedes einzelne Update für eine einzelne Entität dargestellt wird.</span><span class="sxs-lookup"><span data-stu-id="85866-149">Note that `SaveChanges` saves multiple changes in a single batch, so this doesn't necessarily represent each individual update done on a single entity.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="85866-150">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="85866-150">Additional resources</span></span>

* [<span data-ttu-id="85866-151">.NET-Dokumentation zu Ereignis Indikatoren</span><span class="sxs-lookup"><span data-stu-id="85866-151">.NET documentation on event counters</span></span>](https://docs.microsoft.com/dotnet/core/diagnostics/event-counters)
