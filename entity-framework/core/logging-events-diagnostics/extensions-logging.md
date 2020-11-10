---
title: Verwenden von Microsoft. Extensions. Logging-EF Core
description: Protokollierung von EF Core mithilfe von "Microsoft. Extensions. Logging" in ASP.net Core und anderen Anwendungs Typen
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/extensions-logging
ms.openlocfilehash: 3732bda0ef29b05672a2dfb83405da802f899191
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431253"
---
# <a name="using-microsoftextensionslogging-in-ef-core"></a><span data-ttu-id="f4a78-103">Verwenden von Microsoft. Extensions. Logging in EF Core</span><span class="sxs-lookup"><span data-stu-id="f4a78-103">Using Microsoft.Extensions.Logging in EF Core</span></span>

<span data-ttu-id="f4a78-104">[Microsoft. Extensions. Logging](/dotnet/core/extensions/logging) ist ein erweiterbarer Protokollierungs Mechanismus mit Plug-in-Anbietern für viele gängige Protokollierungs Systeme.</span><span class="sxs-lookup"><span data-stu-id="f4a78-104">[Microsoft.Extensions.Logging](/dotnet/core/extensions/logging) is an extensible logging mechanism with plug-in providers for many common logging systems.</span></span> <span data-ttu-id="f4a78-105">Sowohl von Microsoft bereitgestellte Plug-ins (z. b. [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)) als auch Drittanbieter-Plug-ins (z. b. [serilog. Extensions. Logging](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) sind als nuget-Pakete verfügbar.</span><span class="sxs-lookup"><span data-stu-id="f4a78-105">Both Microsoft-supplied plug-ins (e.g [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)) and third-party plug-ins (e.g. [Serilog.Extensions.Logging](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) are available as NuGet packages.</span></span>

<span data-ttu-id="f4a78-106">Entity Framework Core (EF Core) ist vollständig in integriert `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="f4a78-106">Entity Framework Core (EF Core) fully integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="f4a78-107">Sie sollten jedoch die [einfache Protokollierung](xref:core/logging-events-diagnostics/simple-logging) für eine einfachere Methode zur Protokollierung verwenden, insbesondere für Anwendungen, die keine Abhängigkeitsinjektion verwenden.</span><span class="sxs-lookup"><span data-stu-id="f4a78-107">However, consider using [simple logging](xref:core/logging-events-diagnostics/simple-logging) for a simpler way to log, especially for applications that don't use dependency injection.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="f4a78-108">ASP.NET Core-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="f4a78-108">ASP.NET Core applications</span></span>

<span data-ttu-id="f4a78-109">`Microsoft.Extensions.Logging` wird [standardmäßig in ASP.net Core Anwendungen verwendet](/aspnet/core/fundamentals/logging).</span><span class="sxs-lookup"><span data-stu-id="f4a78-109">`Microsoft.Extensions.Logging` is [used by default in ASP.NET Core applications](/aspnet/core/fundamentals/logging).</span></span> <span data-ttu-id="f4a78-110">Aufrufen von <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> oder <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> .</span><span class="sxs-lookup"><span data-stu-id="f4a78-110">Calling <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A>.</span></span>

## <a name="other-application-types"></a><span data-ttu-id="f4a78-111">Andere Anwendungs Typen</span><span class="sxs-lookup"><span data-stu-id="f4a78-111">Other application types</span></span>

<span data-ttu-id="f4a78-112">Andere Anwendungs Typen können den [generichost](/dotnet/core/extensions/generic-host) verwenden, um die gleichen Abhängigkeits einschleusungs Muster wie in ASP.net Core zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="f4a78-112">Other application types can use the [GenericHost](/dotnet/core/extensions/generic-host) to get the same dependency injection patterns as are used in ASP.NET Core.</span></span> <span data-ttu-id="f4a78-113">Adddbcontext oder adddbcontextpool kann dann wie in ASP.net Core Anwendungen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f4a78-113">AddDbContext or AddDbContextPool can then be used just like in ASP.NET Core applications.</span></span>

<span data-ttu-id="f4a78-114">`Microsoft.Extensions.Logging` kann auch für Anwendungen verwendet werden, die keine Abhängigkeitsinjektion verwenden, obwohl die [einfache Protokollierung](xref:core/logging-events-diagnostics/simple-logging) einfacher einzurichten ist.</span><span class="sxs-lookup"><span data-stu-id="f4a78-114">`Microsoft.Extensions.Logging` can also be used for applications that don't use dependency injection, although [simple logging](xref:core/logging-events-diagnostics/simple-logging) can be easier to set up.</span></span>

<span data-ttu-id="f4a78-115">`Microsoft.Extensions.Logging` erfordert das Erstellen eines <xref:Microsoft.Extensions.Logging.LoggerFactory> .</span><span class="sxs-lookup"><span data-stu-id="f4a78-115">`Microsoft.Extensions.Logging` requires creation of a <xref:Microsoft.Extensions.Logging.LoggerFactory>.</span></span> <span data-ttu-id="f4a78-116">Diese Factory sollte als statische/globale Instanz an einem beliebigen Speicherort gespeichert werden und bei jeder Erstellung eines dbcontext verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f4a78-116">This factory should be stored as a static/global instance somewhere and used each time a DbContext is created.</span></span> <span data-ttu-id="f4a78-117">Beispielsweise ist es üblich, die Protokollierungs-Factory als statische Eigenschaft im dbcontext zu speichern.</span><span class="sxs-lookup"><span data-stu-id="f4a78-117">For example, it is common to store the logger factory as a static property on the DbContext.</span></span>

### <a name="ef-core-30-and-above"></a>[<span data-ttu-id="f4a78-118">EF Core 3,0 und höher</span><span class="sxs-lookup"><span data-stu-id="f4a78-118">EF Core 3.0 and above</span></span>](#tab/v3)

<!--
        public static readonly ILoggerFactory MyLoggerFactory
            = LoggerFactory.Create(builder => { builder.AddConsole(); });
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="ef-core-21"></a>[<span data-ttu-id="f4a78-119">EF Core 2.1</span><span class="sxs-lookup"><span data-stu-id="f4a78-119">EF Core 2.1</span></span>](#tab/v2)

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

> [!WARNING]
> <span data-ttu-id="f4a78-120">In EF Core 2,1 ist es sehr wichtig, dass Anwendungen für jede dbcontext-Instanz keine neue loggerfactory-Instanz erstellen.</span><span class="sxs-lookup"><span data-stu-id="f4a78-120">In EF Core 2.1, It is very important that applications do not create a new LoggerFactory instance for each DbContext instance.</span></span> <span data-ttu-id="f4a78-121">Dies führt zu einem Speichermangel und einer unzureichenden Leistung.</span><span class="sxs-lookup"><span data-stu-id="f4a78-121">Doing so will result in a memory leak and poor performance.</span></span> <span data-ttu-id="f4a78-122">Dies wurde in EF Core 3,0 und höher behoben.</span><span class="sxs-lookup"><span data-stu-id="f4a78-122">This has been fixed in EF Core 3.0 and above.</span></span>

***

<span data-ttu-id="f4a78-123">Diese Singleton-/globale Instanz sollte dann bei EF Core auf dem registriert werden <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> .</span><span class="sxs-lookup"><span data-stu-id="f4a78-123">This singleton/global instance should then be registered with EF Core on the <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.</span></span> <span data-ttu-id="f4a78-124">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f4a78-124">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .UseLoggerFactory(MyLoggerFactory)
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFLogging;ConnectRetryCount=0");
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

## <a name="getting-detailed-messages"></a><span data-ttu-id="f4a78-125">Ausführliche Meldungen werden erhalten.</span><span class="sxs-lookup"><span data-stu-id="f4a78-125">Getting detailed messages</span></span>

> [!TIP]
> <span data-ttu-id="f4a78-126">On-Konfiguration wird immer noch aufgerufen, wenn adddbcontext verwendet wird oder eine dbcontextoptions-Instanz an den dbcontext-Konstruktor übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="f4a78-126">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="f4a78-127">Dies ist der ideale Ort zum Anwenden der Kontext Konfiguration, unabhängig davon, wie der dbcontext erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="f4a78-127">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="f4a78-128">Sensible Daten</span><span class="sxs-lookup"><span data-stu-id="f4a78-128">Sensitive data</span></span>

<span data-ttu-id="f4a78-129">Standardmäßig werden in EF Core die Werte von Daten in Ausnahme Meldungen nicht eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="f4a78-129">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="f4a78-130">Dies liegt daran, dass solche Daten möglicherweise vertraulich sind und in der Produktionsumgebung verwendet werden können, wenn eine Ausnahme nicht behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="f4a78-130">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="f4a78-131">Allerdings kann das Wissen von Datenwerten, insbesondere bei Schlüsseln, beim Debuggen sehr hilfreich sein.</span><span class="sxs-lookup"><span data-stu-id="f4a78-131">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="f4a78-132">Dies kann in EF Core aktiviert werden, indem aufgerufen wird <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> .</span><span class="sxs-lookup"><span data-stu-id="f4a78-132">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="f4a78-133">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f4a78-133">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableSensitiveDataLogging();
-->
[!code-csharp[EnableSensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableSensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="f4a78-134">Ausführliche Abfrage Ausnahmen</span><span class="sxs-lookup"><span data-stu-id="f4a78-134">Detailed query exceptions</span></span>

<span data-ttu-id="f4a78-135">Aus Leistungsgründen wird von EF Core nicht jeder aufzurufende Befehl zum Lesen eines Werts vom Datenbankanbieter in einem try-catch-Block eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="f4a78-135">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="f4a78-136">Dies führt jedoch manchmal zu Ausnahmen, die schwer zu diagnostizieren sind, insbesondere dann, wenn die Datenbank einen NULL-Wert zurückgibt, wenn Sie vom Modell nicht zugelassen wird.</span><span class="sxs-lookup"><span data-stu-id="f4a78-136">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="f4a78-137">Das aktivieren <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> führt dazu, dass EF diese try-catch-Blöcke einführt und somit ausführlichere Fehler bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="f4a78-137">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="f4a78-138">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f4a78-138">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableDetailedErrors)]

## <a name="configuration-for-specific-messages"></a><span data-ttu-id="f4a78-139">Konfiguration für bestimmte Nachrichten</span><span class="sxs-lookup"><span data-stu-id="f4a78-139">Configuration for specific messages</span></span>

<span data-ttu-id="f4a78-140">Die EF Core- <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API ermöglicht es Anwendungen, zu ändern, was geschieht, wenn ein bestimmtes Ereignis auftritt.</span><span class="sxs-lookup"><span data-stu-id="f4a78-140">The EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API allows applications to change what happens when a specific event is encountered.</span></span> <span data-ttu-id="f4a78-141">Dies kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="f4a78-141">This can be used to:</span></span>

* <span data-ttu-id="f4a78-142">Ändern Sie die Protokollebene, bei der das Ereignis protokolliert wird.</span><span class="sxs-lookup"><span data-stu-id="f4a78-142">Change the log level at which the event is logged</span></span>
* <span data-ttu-id="f4a78-143">Protokollierung des Ereignisses überspringen</span><span class="sxs-lookup"><span data-stu-id="f4a78-143">Skip logging the event altogether</span></span>
* <span data-ttu-id="f4a78-144">Löst eine Ausnahme aus, wenn das Ereignis eintritt.</span><span class="sxs-lookup"><span data-stu-id="f4a78-144">Throw an exception when the event occurs</span></span>

### <a name="changing-the-log-level-for-an-event"></a><span data-ttu-id="f4a78-145">Ändern der Protokollebene für ein Ereignis</span><span class="sxs-lookup"><span data-stu-id="f4a78-145">Changing the log level for an event</span></span>

<span data-ttu-id="f4a78-146">Manchmal kann es hilfreich sein, die vordefinierte Protokollebene für ein Ereignis zu ändern.</span><span class="sxs-lookup"><span data-stu-id="f4a78-146">Sometimes it can be useful to change the pre-defined log level for an event.</span></span> <span data-ttu-id="f4a78-147">Dies kann z. b. verwendet werden, um zwei weitere Ereignisse von auf heraufzustufen `LogLevel.Debug` `LogLevel.Information` :</span><span class="sxs-lookup"><span data-stu-id="f4a78-147">For example, this can be used to promote two additional events from `LogLevel.Debug` to `LogLevel.Information`:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Log(
                    (RelationalEventId.ConnectionOpened, LogLevel.Information),
                    (RelationalEventId.ConnectionClosed, LogLevel.Information)));
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a><span data-ttu-id="f4a78-148">Protokollierung eines Ereignisses unterdrücken</span><span class="sxs-lookup"><span data-stu-id="f4a78-148">Suppress logging an event</span></span>

<span data-ttu-id="f4a78-149">Auf ähnliche Weise kann ein einzelnes Ereignis aus der Protokollierung unterdrückt werden.</span><span class="sxs-lookup"><span data-stu-id="f4a78-149">In a similar way, an individual event can be suppressed from logging.</span></span> <span data-ttu-id="f4a78-150">Dies ist besonders nützlich, wenn eine Warnung ignoriert werden soll, die überprüft und verstanden wurde.</span><span class="sxs-lookup"><span data-stu-id="f4a78-150">This is particularly useful for ignoring a warning that has been reviewed and understood.</span></span> <span data-ttu-id="f4a78-151">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f4a78-151">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning));
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a><span data-ttu-id="f4a78-152">Auslösen für ein Ereignis</span><span class="sxs-lookup"><span data-stu-id="f4a78-152">Throw for an event</span></span>

<span data-ttu-id="f4a78-153">Zum Schluss können EF Core so konfiguriert werden, dass für ein bestimmtes Ereignis ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="f4a78-153">Finally, EF Core can be configured to throw for a given event.</span></span> <span data-ttu-id="f4a78-154">Dies ist besonders nützlich, um eine Warnung in einen Fehler zu ändern.</span><span class="sxs-lookup"><span data-stu-id="f4a78-154">This is particularly useful for changing a warning into an error.</span></span> <span data-ttu-id="f4a78-155">(Dies war tatsächlich der ursprüngliche Zweck der- `ConfigureWarnings` Methode, daher der Name.) Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f4a78-155">(Indeed, this was the original purpose of `ConfigureWarnings` method, hence the name.) For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Throw(RelationalEventId.QueryPossibleUnintendedUseOfEqualsWarning));
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ThrowForEvent)]

## <a name="filtering-and-other-configuration"></a><span data-ttu-id="f4a78-156">Filterung und andere Konfiguration</span><span class="sxs-lookup"><span data-stu-id="f4a78-156">Filtering and other configuration</span></span>

<span data-ttu-id="f4a78-157">Anleitungen zur Protokollfilterung und anderen Konfigurationen finden Sie unter [Protokollierung in .net](/dotnet/core/extensions/logging) .</span><span class="sxs-lookup"><span data-stu-id="f4a78-157">See [Logging in .NET](/dotnet/core/extensions/logging) for guidance on log filtering and other configuration.</span></span>

<span data-ttu-id="f4a78-158">EF Core Protokollierungs Ereignisse werden in einer der folgenden-Ereignisse definiert:</span><span class="sxs-lookup"><span data-stu-id="f4a78-158">EF Core logging events are defined in one of:</span></span>

* <span data-ttu-id="f4a78-159"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> für Ereignisse, die für alle EF Core Datenbankanbieter üblich sind</span><span class="sxs-lookup"><span data-stu-id="f4a78-159"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> for events common to all EF Core database providers</span></span>
* <span data-ttu-id="f4a78-160"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> für Ereignisse, die allen relationalen Datenbankanbietern gemeinsam sind</span><span class="sxs-lookup"><span data-stu-id="f4a78-160"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> for events common to all relational database providers</span></span>
* <span data-ttu-id="f4a78-161">Eine ähnliche Klasse für Ereignisse, die für den aktuellen Datenbankanbieter spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="f4a78-161">A similar class for events specific to the current database provider.</span></span> <span data-ttu-id="f4a78-162">Beispielsweise <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> für den SQL Server-Anbieter.</span><span class="sxs-lookup"><span data-stu-id="f4a78-162">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="f4a78-163">Diese Definitionen enthalten die Ereignis-IDs, die Protokollebene und die Kategorie für jedes Ereignis, wie von verwendet `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="f4a78-163">These definitions contain the event IDs, log level, and category for each event, as used by `Microsoft.Extensions.Logging`.</span></span>
