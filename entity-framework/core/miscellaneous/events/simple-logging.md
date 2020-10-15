---
title: Einfache Protokollierung-EF Core
description: Protokollieren von einem efcore-dbcontext mithilfe von logto
author: ajcvickers
ms.date: 10/03/2020
uid: core/miscellaneous/events/simple-logging
ms.openlocfilehash: 2d671dd9daad16a6431b91419736ec67733e41b3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066522"
---
# <a name="simple-logging"></a><span data-ttu-id="40993-103">Einfache Protokollierung</span><span class="sxs-lookup"><span data-stu-id="40993-103">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="40993-104">Diese Funktion wurde in EF Core 5,0 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="40993-104">This feature was added in EF Core 5.0.</span></span>

> [!TIP]  
> <span data-ttu-id="40993-105">Sie können [das Beispiel dieses Artikels auf GitHub anzeigen und herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) .</span><span class="sxs-lookup"><span data-stu-id="40993-105">You can [view and download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) on GitHub.</span></span>

<span data-ttu-id="40993-106">Die einfache Protokollierung von Entity Framework Core (EF Core) kann verwendet werden, um beim entwickeln und Debuggen von Anwendungen problemlos Protokolle zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="40993-106">Entity Framework Core (EF Core) simple logging can be used to easily obtain logs while developing and debugging applications.</span></span> <span data-ttu-id="40993-107">Für diese Art der Protokollierung ist eine minimale Konfiguration und keine zusätzliche nuget-Pakete erforderlich.</span><span class="sxs-lookup"><span data-stu-id="40993-107">This form of logging requires minimal configuration and no additional NuGet packages.</span></span>

> [!TIP]
> <span data-ttu-id="40993-108">EF Core auch in [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging)integriert, was eine höhere Konfiguration erfordert, aber häufig für die Protokollierung in Produktionsanwendungen geeignet ist.</span><span class="sxs-lookup"><span data-stu-id="40993-108">EF Core also integrates with [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging), which requires more configuration, but is often more suitable for logging in production applications.</span></span>

## <a name="configuration"></a><span data-ttu-id="40993-109">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="40993-109">Configuration</span></span>

<span data-ttu-id="40993-110">Auf EF Core Protokolle können von jedem Anwendungstyp aus mithilfe von [logto](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="40993-110">EF Core logs can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="40993-111">beim [Konfigurieren einer dbcontext-Instanz](xref:core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="40993-111">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="40993-112">Diese Konfiguration erfolgt in der Regel in einer außer Kraft Setzung von <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="40993-112">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="40993-113">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="40993-113">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="40993-114">Alternativ `LogTo` kann auch als Teil von oder aufgerufen werden, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> Wenn eine-Instanz erstellt wird <xref:Microsoft.EntityFrameworkCore.DbContextOptions> , die an den-Konstruktor übergeben werden soll `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="40993-114">Alternately, `LogTo` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the `DbContext` constructor.</span></span>

> [!TIP]
> <span data-ttu-id="40993-115">On-Konfiguration wird immer noch aufgerufen, wenn adddbcontext verwendet wird oder eine dbcontextoptions-Instanz an den dbcontext-Konstruktor übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="40993-115">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="40993-116">Dies ist der ideale Ort zum Anwenden der Kontext Konfiguration, unabhängig davon, wie der dbcontext erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="40993-116">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

## <a name="directing-the-logs"></a><span data-ttu-id="40993-117">Leiten der Protokolle</span><span class="sxs-lookup"><span data-stu-id="40993-117">Directing the logs</span></span>

### <a name="logging-to-the-console"></a><span data-ttu-id="40993-118">Protokollierung an der Konsole</span><span class="sxs-lookup"><span data-stu-id="40993-118">Logging to the console</span></span>

<span data-ttu-id="40993-119">`LogTo` erfordert einen Delegaten <xref:System.Action%601> , der eine Zeichenfolge akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="40993-119">`LogTo` requires an <xref:System.Action%601> delegate that accepts a string.</span></span> <span data-ttu-id="40993-120">EF Core wird diesen Delegaten mit einer Zeichenfolge für jede generierte Protokollnachricht aufruft.</span><span class="sxs-lookup"><span data-stu-id="40993-120">EF Core will call this delegate with a string for each log message generated.</span></span> <span data-ttu-id="40993-121">Anschließend wird der Delegat zum Ausführen von etwas mit der angegebenen Meldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="40993-121">It is then up to the delegate to do something with the given message.</span></span>

<span data-ttu-id="40993-122">Die- <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> Methode wird häufig für diesen Delegaten verwendet, wie oben gezeigt.</span><span class="sxs-lookup"><span data-stu-id="40993-122">The <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> method is often used for this delegate, as shown above.</span></span> <span data-ttu-id="40993-123">Dies führt dazu, dass jede Protokollmeldung in die Konsole geschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="40993-123">This results in each log message being written to the console.</span></span>

### <a name="logging-to-the-debug-window"></a><span data-ttu-id="40993-124">Protokollierung im Debugfenster</span><span class="sxs-lookup"><span data-stu-id="40993-124">Logging to the debug window</span></span>

<span data-ttu-id="40993-125"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> kann verwendet werden, um die Ausgabe an das Debugfenster in Visual Studio oder anderen IDEs zu senden.</span><span class="sxs-lookup"><span data-stu-id="40993-125"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> can be used to send output to the Debug window in Visual Studio or other IDEs.</span></span> <span data-ttu-id="40993-126">Die [Lambda-Syntax](/dotnet/csharp/language-reference/operators/lambda-expressions) muss in diesem Fall verwendet werden, da die- `Debug` Klasse aus Releasebuilds kompiliert wird.</span><span class="sxs-lookup"><span data-stu-id="40993-126">[Lambda syntax](/dotnet/csharp/language-reference/operators/lambda-expressions) must be used in this case because the `Debug` class is compiled out of release builds.</span></span> <span data-ttu-id="40993-127">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="40993-127">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a><span data-ttu-id="40993-128">Protokollierung in einer Datei</span><span class="sxs-lookup"><span data-stu-id="40993-128">Logging to a file</span></span>

<span data-ttu-id="40993-129">Zum Schreiben in eine Datei muss ein <xref:System.IO.StreamWriter> oder ein ähnliches für die Datei erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="40993-129">Writing to a file requires creating a <xref:System.IO.StreamWriter> or similar for the file.</span></span> <span data-ttu-id="40993-130">Die- <xref:System.IO.StreamWriter.WriteLine%2A> Methode kann dann wie in den anderen Beispielen oben verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="40993-130">The <xref:System.IO.StreamWriter.WriteLine%2A> method can then be used as in the other examples above.</span></span> <span data-ttu-id="40993-131">Stellen Sie sicher, dass die Datei ordnungsgemäß geschlossen wird, indem Sie den Writer verwerfen, wenn der Kontext verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="40993-131">Remember to ensure the file is closed cleanly by disposing the writer when the context is disposed.</span></span> <span data-ttu-id="40993-132">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="40993-132">For example:</span></span>

<!--
    private readonly StreamWriter _logStream = new StreamWriter("mylog.txt", append: true); 

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(_logStream.WriteLine);

    public override void Dispose()
    {
        base.Dispose();
        _logStream.Dispose();
    }
    
    public override async ValueTask DisposeAsync()
    {
        await base.DisposeAsync();
        await _logStream.DisposeAsync();
    }
-->
[!code-csharp[LogToFile](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToFile)]

> [!TIP]
> <span data-ttu-id="40993-133">Verwenden Sie [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) zum Protokollieren in Dateien in Produktionsanwendungen.</span><span class="sxs-lookup"><span data-stu-id="40993-133">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for logging to files in production applications.</span></span>

## <a name="getting-detailed-messages"></a><span data-ttu-id="40993-134">Ausführliche Meldungen werden erhalten.</span><span class="sxs-lookup"><span data-stu-id="40993-134">Getting detailed messages</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="40993-135">Sensible Daten</span><span class="sxs-lookup"><span data-stu-id="40993-135">Sensitive data</span></span>

<span data-ttu-id="40993-136">Standardmäßig werden in EF Core die Werte von Daten in Ausnahme Meldungen nicht eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="40993-136">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="40993-137">Dies liegt daran, dass solche Daten möglicherweise vertraulich sind und in der Produktionsumgebung verwendet werden können, wenn eine Ausnahme nicht behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="40993-137">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="40993-138">Allerdings kann das Wissen von Datenwerten, insbesondere bei Schlüsseln, beim Debuggen sehr hilfreich sein.</span><span class="sxs-lookup"><span data-stu-id="40993-138">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="40993-139">Dies kann in EF Core aktiviert werden, indem aufgerufen wird <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> .</span><span class="sxs-lookup"><span data-stu-id="40993-139">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="40993-140">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="40993-140">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="40993-141">Ausführliche Abfrage Ausnahmen</span><span class="sxs-lookup"><span data-stu-id="40993-141">Detailed query exceptions</span></span>

<span data-ttu-id="40993-142">Aus Leistungsgründen wird von EF Core nicht jeder aufzurufende Befehl zum Lesen eines Werts vom Datenbankanbieter in einem try-catch-Block eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="40993-142">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="40993-143">Dies führt jedoch manchmal zu Ausnahmen, die schwer zu diagnostizieren sind, insbesondere dann, wenn die Datenbank einen NULL-Wert zurückgibt, wenn Sie vom Modell nicht zugelassen wird.</span><span class="sxs-lookup"><span data-stu-id="40993-143">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="40993-144">Das aktivieren <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> führt dazu, dass EF diese try-catch-Blöcke einführt und somit ausführlichere Fehler bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="40993-144">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="40993-145">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="40993-145">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a><span data-ttu-id="40993-146">Filterung</span><span class="sxs-lookup"><span data-stu-id="40993-146">Filtering</span></span>

### <a name="log-levels"></a><span data-ttu-id="40993-147">Protokollebenen</span><span class="sxs-lookup"><span data-stu-id="40993-147">Log levels</span></span>

<span data-ttu-id="40993-148">Jede EF Core Log-Nachricht wird einer Ebene zugewiesen, die von der-Aufzählung definiert wird <xref:Microsoft.Extensions.Logging.LogLevel> .</span><span class="sxs-lookup"><span data-stu-id="40993-148">Every EF Core log message is assigned to a level defined by the <xref:Microsoft.Extensions.Logging.LogLevel> enum.</span></span> <span data-ttu-id="40993-149">Standardmäßig enthält EF Core einfache Protokollierung jede Nachricht auf der `Debug` Ebene oder höher.</span><span class="sxs-lookup"><span data-stu-id="40993-149">By default, EF Core simple logging includes every message at `Debug` level or above.</span></span> <span data-ttu-id="40993-150">`LogTo` kann ein höheres Mindestwert für das Herausfiltern von Nachrichten weitergegeben werden.</span><span class="sxs-lookup"><span data-stu-id="40993-150">`LogTo` can be passed a higher minimum level to filter out some messages.</span></span> <span data-ttu-id="40993-151">Wenn z. b `Information` . die Übergabe von zu einem minimalen Satz von Protokollen durchführt, ist dies auf den Datenbankzugriff und einige in der</span><span class="sxs-lookup"><span data-stu-id="40993-151">For example, passing `Information` results in a minimal set of logs limited to database access and some housekeeping messages.</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a><span data-ttu-id="40993-152">Bestimmte Nachrichten</span><span class="sxs-lookup"><span data-stu-id="40993-152">Specific messages</span></span>

<span data-ttu-id="40993-153">Jeder Protokollmeldung wird eine zugewiesen <xref:Microsoft.Extensions.Logging.EventId> .</span><span class="sxs-lookup"><span data-stu-id="40993-153">Every log message is assigned an <xref:Microsoft.Extensions.Logging.EventId>.</span></span> <span data-ttu-id="40993-154">Auf diese IDs kann von der- <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> Klasse oder der- <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> Klasse für relationale spezifische Nachrichten zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="40993-154">These IDs can be accessed from the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> class or the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> class for relational-specific messages.</span></span> <span data-ttu-id="40993-155">Ein Datenbankanbieter kann auch anbieterspezifische IDs in einer ähnlichen Klasse aufweisen.</span><span class="sxs-lookup"><span data-stu-id="40993-155">A database provider may also have provider-specific IDs in a similar class.</span></span> <span data-ttu-id="40993-156">Beispielsweise <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> für den SQL Server-Anbieter.</span><span class="sxs-lookup"><span data-stu-id="40993-156">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="40993-157">`LogTo` kann so konfiguriert werden, dass nur die einer oder mehreren Ereignis-IDs zugeordneten Nachrichten protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="40993-157">`LogTo` can be configured to only log the messages associated with one or more event IDs.</span></span> <span data-ttu-id="40993-158">Zum Protokollieren von Meldungen für den Kontext, der initialisiert oder verworfen wird, z. b.:</span><span class="sxs-lookup"><span data-stu-id="40993-158">For example, to log only messages for the context being initialized or disposed:</span></span>  

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a><span data-ttu-id="40993-159">Meldungskategorien</span><span class="sxs-lookup"><span data-stu-id="40993-159">Message categories</span></span>

<span data-ttu-id="40993-160">Jede Protokollmeldung wird einer benannten hierarchischen Protokollierungs Kategorie zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="40993-160">Every log message is assigned to a named hierarchical logger category.</span></span> <span data-ttu-id="40993-161">Die Kategorien lauten:</span><span class="sxs-lookup"><span data-stu-id="40993-161">The categories are:</span></span>

| <span data-ttu-id="40993-162">Kategorie</span><span class="sxs-lookup"><span data-stu-id="40993-162">Category</span></span>                                             | <span data-ttu-id="40993-163">Nachrichten</span><span class="sxs-lookup"><span data-stu-id="40993-163">Messages</span></span>
|:-----------------------------------------------------|-------------------------------------------------
| <span data-ttu-id="40993-164">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="40993-164">Microsoft.EntityFrameworkCore</span></span>                        | <span data-ttu-id="40993-165">Alle EF Core Meldungen</span><span class="sxs-lookup"><span data-stu-id="40993-165">All EF Core messages</span></span>
| <span data-ttu-id="40993-166">Microsoft. entityframeworkcore. Database</span><span class="sxs-lookup"><span data-stu-id="40993-166">Microsoft.EntityFrameworkCore.Database</span></span>               | <span data-ttu-id="40993-167">Alle Daten Bank Interaktionen</span><span class="sxs-lookup"><span data-stu-id="40993-167">All database interactions</span></span>
| <span data-ttu-id="40993-168">Microsoft. entityframeworkcore. Database. Connection</span><span class="sxs-lookup"><span data-stu-id="40993-168">Microsoft.EntityFrameworkCore.Database.Connection</span></span>    | <span data-ttu-id="40993-169">Verwendung einer Datenbankverbindung</span><span class="sxs-lookup"><span data-stu-id="40993-169">Uses of a database connection</span></span>
| <span data-ttu-id="40993-170">Microsoft. entityframeworkcore. Database. Command</span><span class="sxs-lookup"><span data-stu-id="40993-170">Microsoft.EntityFrameworkCore.Database.Command</span></span>       | <span data-ttu-id="40993-171">Verwendung eines Daten Bank Befehls</span><span class="sxs-lookup"><span data-stu-id="40993-171">Uses of a database command</span></span>
| <span data-ttu-id="40993-172">Microsoft. entityframeworkcore. Database. Transaction</span><span class="sxs-lookup"><span data-stu-id="40993-172">Microsoft.EntityFrameworkCore.Database.Transaction</span></span>   | <span data-ttu-id="40993-173">Verwendung einer Datenbanktransaktion</span><span class="sxs-lookup"><span data-stu-id="40993-173">Uses of a database transaction</span></span>
| <span data-ttu-id="40993-174">Microsoft. entityframeworkcore. Update</span><span class="sxs-lookup"><span data-stu-id="40993-174">Microsoft.EntityFrameworkCore.Update</span></span>                 | <span data-ttu-id="40993-175">Speichern von Entitäten, Ausschließen von Daten Bank Interaktionen</span><span class="sxs-lookup"><span data-stu-id="40993-175">Saving entities, excluding database interactions</span></span>
| <span data-ttu-id="40993-176">Microsoft. entityframeworkcore. Model</span><span class="sxs-lookup"><span data-stu-id="40993-176">Microsoft.EntityFrameworkCore.Model</span></span>                  | <span data-ttu-id="40993-177">Alle Modell-und metadateninteraktionen</span><span class="sxs-lookup"><span data-stu-id="40993-177">All model and metadata interactions</span></span>
| <span data-ttu-id="40993-178">Microsoft. entityframeworkcore. Model. Validation</span><span class="sxs-lookup"><span data-stu-id="40993-178">Microsoft.EntityFrameworkCore.Model.Validation</span></span>       | <span data-ttu-id="40993-179">Modellvalidierung</span><span class="sxs-lookup"><span data-stu-id="40993-179">Model validation</span></span>
| <span data-ttu-id="40993-180">Microsoft. entityframeworkcore. Query</span><span class="sxs-lookup"><span data-stu-id="40993-180">Microsoft.EntityFrameworkCore.Query</span></span>                  | <span data-ttu-id="40993-181">Abfragen, ausgenommen Daten Bank Interaktionen</span><span class="sxs-lookup"><span data-stu-id="40993-181">Queries, excluding database interactions</span></span>
| <span data-ttu-id="40993-182">Microsoft. entityframeworkcore. Infrastructure</span><span class="sxs-lookup"><span data-stu-id="40993-182">Microsoft.EntityFrameworkCore.Infrastructure</span></span>         | <span data-ttu-id="40993-183">Allgemeine Ereignisse, z. b. die Erstellung von Kontexten</span><span class="sxs-lookup"><span data-stu-id="40993-183">General events, such as context creation</span></span>
| <span data-ttu-id="40993-184">Microsoft. entityframeworkcore. Gerüstbau</span><span class="sxs-lookup"><span data-stu-id="40993-184">Microsoft.EntityFrameworkCore.Scaffolding</span></span>            | <span data-ttu-id="40993-185">Daten Bank Reverse Engineering</span><span class="sxs-lookup"><span data-stu-id="40993-185">Database reverse engineering</span></span>
| <span data-ttu-id="40993-186">Microsoft. entityframeworkcore. Migrationen</span><span class="sxs-lookup"><span data-stu-id="40993-186">Microsoft.EntityFrameworkCore.Migrations</span></span>             | <span data-ttu-id="40993-187">Migrationen</span><span class="sxs-lookup"><span data-stu-id="40993-187">Migrations</span></span>
| <span data-ttu-id="40993-188">Microsoft. entityframeworkcore. changeTracking</span><span class="sxs-lookup"><span data-stu-id="40993-188">Microsoft.EntityFrameworkCore.ChangeTracking</span></span>         | <span data-ttu-id="40993-189">Interaktionen mit Änderungs Nachverfolgung</span><span class="sxs-lookup"><span data-stu-id="40993-189">Change tracking interactions</span></span>

<span data-ttu-id="40993-190">`LogTo` kann so konfiguriert werden, dass nur die Nachrichten aus einer oder mehreren Kategorien protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="40993-190">`LogTo` can be configured to only log the messages from one or more categories.</span></span> <span data-ttu-id="40993-191">Beispielsweise, um nur Daten Bank Interaktionen zu protokollieren:</span><span class="sxs-lookup"><span data-stu-id="40993-191">For example, to log only database interactions:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

<span data-ttu-id="40993-192">Beachten Sie, dass die- <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> Klasse eine hierarchische API zum Suchen einer Kategorie bereitstellt und die Notwendigkeit, Zeichen folgen hart zu codieren, vermeidet.</span><span class="sxs-lookup"><span data-stu-id="40993-192">Notice that the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class provides a hierarchical API for finding a category and avoids the need to hard-code strings.</span></span>

<span data-ttu-id="40993-193">Da Kategorien hierarchisch sind, beinhaltet dieses Beispiel mit der `Database` Kategorie alle Nachrichten für die Unterkategorien `Database.Connection` , `Database.Command` und `Database.Transaction` .</span><span class="sxs-lookup"><span data-stu-id="40993-193">Since categories are hierarchical, this example using the `Database` category will include all messages for the subcategories `Database.Connection`, `Database.Command`, and `Database.Transaction`.</span></span>

### <a name="custom-filters"></a><span data-ttu-id="40993-194">Benutzerdefinierte Filter</span><span class="sxs-lookup"><span data-stu-id="40993-194">Custom filters</span></span>

<span data-ttu-id="40993-195">`LogTo` ermöglicht die Verwendung eines benutzerdefinierten Filters in Fällen, in denen keine der oben genannten Filteroptionen ausreicht.</span><span class="sxs-lookup"><span data-stu-id="40993-195">`LogTo` allows a custom filter to be used for cases where none of the filtering options above are sufficient.</span></span> <span data-ttu-id="40993-196">Zum Protokollieren von Nachrichten auf der Ebene `Information` oder höher sowie Nachrichten zum Öffnen und Schließen einer Verbindung:</span><span class="sxs-lookup"><span data-stu-id="40993-196">For example, to log any message at level `Information` or above, as well as messages for opening and closing a connection:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(
                Console.WriteLine,
                (eventId, logLevel) => logLevel >= LogLevel.Information
                                       || eventId == RelationalEventId.ConnectionOpened
                                       || eventId == RelationalEventId.ConnectionClosed);
-->
[!code-csharp[CustomFilter](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=CustomFilter)]

> [!TIP]
> <span data-ttu-id="40993-197">Das Filtern mit benutzerdefinierten Filtern oder das Verwenden einer der hier gezeigten anderen Optionen ist effizienter als das Filtern im logto-Delegaten.</span><span class="sxs-lookup"><span data-stu-id="40993-197">Filtering using custom filters or using any of the other options shown here is more efficient than filtering in the LogTo delegate.</span></span> <span data-ttu-id="40993-198">Dies liegt daran, dass die Protokollmeldung nicht einmal erstellt wird, wenn der Filter bestimmt, dass die Nachricht nicht protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="40993-198">This is because if the filter determines the message should not be logged, then the log message is not even created.</span></span>

## <a name="message-contents-and-formatting"></a><span data-ttu-id="40993-199">Nachrichten Inhalt und-Formatierung</span><span class="sxs-lookup"><span data-stu-id="40993-199">Message contents and formatting</span></span>

<span data-ttu-id="40993-200">Der Standard Inhalt von `LogTo` wird über mehrere Zeilen formatiert.</span><span class="sxs-lookup"><span data-stu-id="40993-200">The default content from `LogTo` is formatted across multiple lines.</span></span> <span data-ttu-id="40993-201">Die erste Zeile enthält Nachrichten Metadaten:</span><span class="sxs-lookup"><span data-stu-id="40993-201">The first line contains message metadata:</span></span>

* <span data-ttu-id="40993-202">Das <xref:Microsoft.Extensions.Logging.LogLevel> -Zeichen als vier-Zeichen-Präfix.</span><span class="sxs-lookup"><span data-stu-id="40993-202">The <xref:Microsoft.Extensions.Logging.LogLevel> as a four-character prefix</span></span>
* <span data-ttu-id="40993-203">Ein lokaler Zeitstempel, der für die aktuelle Kultur formatiert ist.</span><span class="sxs-lookup"><span data-stu-id="40993-203">A local timestamp, formatted for the current culture</span></span>
* <span data-ttu-id="40993-204">Das <xref:Microsoft.Extensions.Logging.EventId> in der Form, das kopiert/eingefügt werden kann, um den Member aus <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> oder eine der anderen Klassen sowie den unformatierten ID-Wert zu erhalten `EventId` .</span><span class="sxs-lookup"><span data-stu-id="40993-204">The <xref:Microsoft.Extensions.Logging.EventId> in the form that can be copy/pasted to get the member from <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> or one of the other `EventId` classes, plus the raw ID value</span></span>
* <span data-ttu-id="40993-205">Die Ereignis Kategorie, wie oben beschrieben.</span><span class="sxs-lookup"><span data-stu-id="40993-205">The event category, as described above.</span></span>

<span data-ttu-id="40993-206">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="40993-206">For example:</span></span>

```output
info: 10/6/2020 10:52:45.581 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) 
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 10/6/2020 10:52:45.582 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) 
      Committing transaction.
dbug: 10/6/2020 10:52:45.585 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) 
      Committed transaction.
```

<span data-ttu-id="40993-207">Dieser Inhalt kann durch Übergeben von Werten von [dbcontextloggeroptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15) angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="40993-207">This content can be customized by passing values from [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> --><span data-ttu-id="40993-208">, wie in den folgenden Abschnitten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="40993-208">, as shown in the following sections.</span></span>

> [!TIP]
> <span data-ttu-id="40993-209">Verwenden Sie [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) , um die Protokoll Formatierung besser steuern zu können.</span><span class="sxs-lookup"><span data-stu-id="40993-209">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for more control over log formatting.</span></span>

### <a name="using-utc-time"></a><span data-ttu-id="40993-210">Verwenden der UTC-Zeit</span><span class="sxs-lookup"><span data-stu-id="40993-210">Using UTC time</span></span>

<span data-ttu-id="40993-211">Standardmäßig sind timestamnps für den lokalen Verbrauch während des Debuggens konzipiert.</span><span class="sxs-lookup"><span data-stu-id="40993-211">By default, timestamnps are designed for local consumption while debugging.</span></span> <span data-ttu-id="40993-212">Verwenden `DbContextLoggerOptions.DefaultWithUtcTime` Sie stattdessen, um die Kultur unabhängigen UTC-Zeitstempel zu verwenden, aber behalten Sie alle anderen Werte bei.</span><span class="sxs-lookup"><span data-stu-id="40993-212">Use `DbContextLoggerOptions.DefaultWithUtcTime` to use culture-agnostic UTC timestamps instead, but keep everything else the same.</span></span> <span data-ttu-id="40993-213">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="40993-213">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

<span data-ttu-id="40993-214">In diesem Beispiel wird die folgende Protokoll Formatierung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="40993-214">This example results in the following log formatting:</span></span>

```output
info: 2020-10-06T17:55:39.0333701Z RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) 
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 2020-10-06T17:55:39.0333892Z RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) 
      Committing transaction.
dbug: 2020-10-06T17:55:39.0351684Z RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) 
      Committed transaction.
```

### <a name="single-line-logging"></a><span data-ttu-id="40993-215">Einzeilige Protokollierung</span><span class="sxs-lookup"><span data-stu-id="40993-215">Single line logging</span></span>

<span data-ttu-id="40993-216">Manchmal ist es hilfreich, genau eine Zeile pro Protokollnachricht zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="40993-216">Sometimes it is useful to get exactly one line per log message.</span></span> <span data-ttu-id="40993-217">Dies kann durch aktiviert werden `DbContextLoggerOptions.SingleLine` .</span><span class="sxs-lookup"><span data-stu-id="40993-217">This can be enabled by `DbContextLoggerOptions.SingleLine`.</span></span> <span data-ttu-id="40993-218">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="40993-218">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

<span data-ttu-id="40993-219">In diesem Beispiel wird die folgende Protokoll Formatierung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="40993-219">This example results in the following log formatting:</span></span>

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a><span data-ttu-id="40993-220">Weitere Inhalts Optionen</span><span class="sxs-lookup"><span data-stu-id="40993-220">Other content options</span></span>

<span data-ttu-id="40993-221">Andere Flags in [dbcontextloggeroptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span><span class="sxs-lookup"><span data-stu-id="40993-221">Other flags in [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> --> <span data-ttu-id="40993-222">kann verwendet werden, um die Menge der im Protokoll enthaltenen Metadaten zu kürzen.</span><span class="sxs-lookup"><span data-stu-id="40993-222">can be used to trim down the amount of metadata included in the log.</span></span> <span data-ttu-id="40993-223">Dies kann in Verbindung mit der einzeiligen Protokollierung nützlich sein.</span><span class="sxs-lookup"><span data-stu-id="40993-223">This is can be useful in conjunction with single-line logging.</span></span> <span data-ttu-id="40993-224">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="40993-224">For example:</span></span>

<span data-ttu-id="40993-225">In diesem Beispiel wird die folgende Protokoll Formatierung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="40993-225">This example results in the following log formatting:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a><span data-ttu-id="40993-226">Verschieben von EF6</span><span class="sxs-lookup"><span data-stu-id="40993-226">Moving from EF6</span></span>

<span data-ttu-id="40993-227">EF Core einfache Protokollierung unterscheidet <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> sich von in EF6 auf zwei Arten:</span><span class="sxs-lookup"><span data-stu-id="40993-227">EF Core simple logging differs from <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6 in two important ways:</span></span>

* <span data-ttu-id="40993-228">Protokollmeldungen sind nicht auf Daten Bank Interaktionen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="40993-228">Log messages are not limited to only database interactions</span></span>
* <span data-ttu-id="40993-229">Die Protokollierung muss zum Zeitpunkt der kontextinitialisierung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="40993-229">The logging must be configured at context initialization time</span></span>

<span data-ttu-id="40993-230">Für den ersten Unterschied kann die oben beschriebene Filterung verwendet werden, um einzuschränken, welche Nachrichten protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="40993-230">For the first difference, the filtering described above can be used to limit which messages are logged.</span></span>

<span data-ttu-id="40993-231">Der zweite Unterschied ist eine absichtliche Änderung, um die Leistung zu verbessern, indem keine Protokollmeldungen erzeugt werden, wenn Sie nicht benötigt werden</span><span class="sxs-lookup"><span data-stu-id="40993-231">The second difference is an intentional change to improve performance by not generating log messages when they are not needed.</span></span> <span data-ttu-id="40993-232">Es ist jedoch weiterhin möglich, ein ähnliches Verhalten zu EF6 zu erzielen, indem Sie eine `Log` -Eigenschaft auf Ihrem erstellen `DbContext` und diese dann nur dann verwenden, wenn Sie festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="40993-232">However, it is still possible to get a similar behavior to EF6 by creating a `Log` property on your `DbContext` and then using it only when it has been set.</span></span> <span data-ttu-id="40993-233">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="40993-233">For example:</span></span>

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
