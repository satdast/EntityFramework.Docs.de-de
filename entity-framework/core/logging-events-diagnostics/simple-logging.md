---
title: Einfache Protokollierung-EF Core
description: Protokollieren von einem EF Core dbcontext mithilfe von logto
author: ajcvickers
ms.date: 10/03/2020
uid: core/logging-events-diagnostics/simple-logging
ms.openlocfilehash: 49619cc10ea098e39e71dde347e00bbc3c39b13a
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431250"
---
# <a name="simple-logging"></a><span data-ttu-id="f3b81-103">Einfache Protokollierung</span><span class="sxs-lookup"><span data-stu-id="f3b81-103">Simple Logging</span></span>

> [!NOTE]
> <span data-ttu-id="f3b81-104">Dieses Feature wurde in EF Core 5.0 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="f3b81-104">This feature was added in EF Core 5.0.</span></span>

> [!TIP]  
> <span data-ttu-id="f3b81-105">Sie können [das Beispiel dieses Artikels](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) von GitHub herunterladen.</span><span class="sxs-lookup"><span data-stu-id="f3b81-105">You can [download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) from GitHub.</span></span>

<span data-ttu-id="f3b81-106">Die einfache Protokollierung von Entity Framework Core (EF Core) kann verwendet werden, um beim entwickeln und Debuggen von Anwendungen problemlos Protokolle zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="f3b81-106">Entity Framework Core (EF Core) simple logging can be used to easily obtain logs while developing and debugging applications.</span></span> <span data-ttu-id="f3b81-107">Für diese Art der Protokollierung ist eine minimale Konfiguration und keine zusätzliche nuget-Pakete erforderlich.</span><span class="sxs-lookup"><span data-stu-id="f3b81-107">This form of logging requires minimal configuration and no additional NuGet packages.</span></span>

> [!TIP]
> <span data-ttu-id="f3b81-108">EF Core auch in [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging)integriert, was eine höhere Konfiguration erfordert, aber häufig für die Protokollierung in Produktionsanwendungen geeignet ist.</span><span class="sxs-lookup"><span data-stu-id="f3b81-108">EF Core also integrates with [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging), which requires more configuration, but is often more suitable for logging in production applications.</span></span>

## <a name="configuration"></a><span data-ttu-id="f3b81-109">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="f3b81-109">Configuration</span></span>

<span data-ttu-id="f3b81-110">Auf EF Core Protokolle können von jedem Anwendungstyp aus mithilfe von [logto](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="f3b81-110">EF Core logs can be accessed from any type of application through the use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="f3b81-111">beim [Konfigurieren einer DbContext-Instanz](xref:core/dbcontext-configuration/index) möglich.</span><span class="sxs-lookup"><span data-stu-id="f3b81-111">when [configuring a DbContext instance](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="f3b81-112">Diese Konfiguration erfolgt in der Regel durch ein Überschreiben von <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="f3b81-112">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="f3b81-113">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f3b81-113">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="f3b81-114">Alternativ `LogTo` kann auch als Teil von oder aufgerufen werden, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> Wenn eine-Instanz erstellt wird <xref:Microsoft.EntityFrameworkCore.DbContextOptions> , die an den-Konstruktor übergeben werden soll `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="f3b81-114">Alternately, `LogTo` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the `DbContext` constructor.</span></span>

> [!TIP]
> <span data-ttu-id="f3b81-115">On-Konfiguration wird immer noch aufgerufen, wenn adddbcontext verwendet wird oder eine dbcontextoptions-Instanz an den dbcontext-Konstruktor übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="f3b81-115">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="f3b81-116">Dies ist der ideale Ort zum Anwenden der Kontext Konfiguration, unabhängig davon, wie der dbcontext erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="f3b81-116">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

## <a name="directing-the-logs"></a><span data-ttu-id="f3b81-117">Leiten der Protokolle</span><span class="sxs-lookup"><span data-stu-id="f3b81-117">Directing the logs</span></span>

### <a name="logging-to-the-console"></a><span data-ttu-id="f3b81-118">Protokollierung an der Konsole</span><span class="sxs-lookup"><span data-stu-id="f3b81-118">Logging to the console</span></span>

<span data-ttu-id="f3b81-119">`LogTo` erfordert einen Delegaten <xref:System.Action%601> , der eine Zeichenfolge akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="f3b81-119">`LogTo` requires an <xref:System.Action%601> delegate that accepts a string.</span></span> <span data-ttu-id="f3b81-120">EF Core wird diesen Delegaten mit einer Zeichenfolge für jede generierte Protokollnachricht aufruft.</span><span class="sxs-lookup"><span data-stu-id="f3b81-120">EF Core will call this delegate with a string for each log message generated.</span></span> <span data-ttu-id="f3b81-121">Anschließend wird der Delegat zum Ausführen von etwas mit der angegebenen Meldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="f3b81-121">It is then up to the delegate to do something with the given message.</span></span>

<span data-ttu-id="f3b81-122">Die- <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> Methode wird häufig für diesen Delegaten verwendet, wie oben gezeigt.</span><span class="sxs-lookup"><span data-stu-id="f3b81-122">The <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> method is often used for this delegate, as shown above.</span></span> <span data-ttu-id="f3b81-123">Dies führt dazu, dass jede Protokollmeldung in die Konsole geschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="f3b81-123">This results in each log message being written to the console.</span></span>

### <a name="logging-to-the-debug-window"></a><span data-ttu-id="f3b81-124">Protokollierung im Debugfenster</span><span class="sxs-lookup"><span data-stu-id="f3b81-124">Logging to the debug window</span></span>

<span data-ttu-id="f3b81-125"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> kann verwendet werden, um die Ausgabe an das Debugfenster in Visual Studio oder anderen IDEs zu senden.</span><span class="sxs-lookup"><span data-stu-id="f3b81-125"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> can be used to send output to the Debug window in Visual Studio or other IDEs.</span></span> <span data-ttu-id="f3b81-126">Die [Lambda-Syntax](/dotnet/csharp/language-reference/operators/lambda-expressions) muss in diesem Fall verwendet werden, da die- `Debug` Klasse aus Releasebuilds kompiliert wird.</span><span class="sxs-lookup"><span data-stu-id="f3b81-126">[Lambda syntax](/dotnet/csharp/language-reference/operators/lambda-expressions) must be used in this case because the `Debug` class is compiled out of release builds.</span></span> <span data-ttu-id="f3b81-127">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f3b81-127">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a><span data-ttu-id="f3b81-128">Protokollierung in einer Datei</span><span class="sxs-lookup"><span data-stu-id="f3b81-128">Logging to a file</span></span>

<span data-ttu-id="f3b81-129">Zum Schreiben in eine Datei muss ein <xref:System.IO.StreamWriter> oder ein ähnliches für die Datei erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="f3b81-129">Writing to a file requires creating a <xref:System.IO.StreamWriter> or similar for the file.</span></span> <span data-ttu-id="f3b81-130">Die- <xref:System.IO.StreamWriter.WriteLine%2A> Methode kann dann wie in den anderen Beispielen oben verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f3b81-130">The <xref:System.IO.StreamWriter.WriteLine%2A> method can then be used as in the other examples above.</span></span> <span data-ttu-id="f3b81-131">Stellen Sie sicher, dass die Datei ordnungsgemäß geschlossen wird, indem Sie den Writer verwerfen, wenn der Kontext verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="f3b81-131">Remember to ensure the file is closed cleanly by disposing the writer when the context is disposed.</span></span> <span data-ttu-id="f3b81-132">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f3b81-132">For example:</span></span>

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
[!code-csharp[LogToFile](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToFile)]

> [!TIP]
> <span data-ttu-id="f3b81-133">Verwenden Sie [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) zum Protokollieren in Dateien in Produktionsanwendungen.</span><span class="sxs-lookup"><span data-stu-id="f3b81-133">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for logging to files in production applications.</span></span>

## <a name="getting-detailed-messages"></a><span data-ttu-id="f3b81-134">Ausführliche Meldungen werden erhalten.</span><span class="sxs-lookup"><span data-stu-id="f3b81-134">Getting detailed messages</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="f3b81-135">Sensible Daten</span><span class="sxs-lookup"><span data-stu-id="f3b81-135">Sensitive data</span></span>

<span data-ttu-id="f3b81-136">Standardmäßig werden in EF Core die Werte von Daten in Ausnahme Meldungen nicht eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="f3b81-136">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="f3b81-137">Dies liegt daran, dass solche Daten möglicherweise vertraulich sind und in der Produktionsumgebung verwendet werden können, wenn eine Ausnahme nicht behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="f3b81-137">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="f3b81-138">Allerdings kann das Wissen von Datenwerten, insbesondere bei Schlüsseln, beim Debuggen sehr hilfreich sein.</span><span class="sxs-lookup"><span data-stu-id="f3b81-138">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="f3b81-139">Dies kann in EF Core aktiviert werden, indem aufgerufen wird <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> .</span><span class="sxs-lookup"><span data-stu-id="f3b81-139">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="f3b81-140">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f3b81-140">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="f3b81-141">Ausführliche Abfrage Ausnahmen</span><span class="sxs-lookup"><span data-stu-id="f3b81-141">Detailed query exceptions</span></span>

<span data-ttu-id="f3b81-142">Aus Leistungsgründen wird von EF Core nicht jeder aufzurufende Befehl zum Lesen eines Werts vom Datenbankanbieter in einem try-catch-Block eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="f3b81-142">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="f3b81-143">Dies führt jedoch manchmal zu Ausnahmen, die schwer zu diagnostizieren sind, insbesondere dann, wenn die Datenbank einen NULL-Wert zurückgibt, wenn Sie vom Modell nicht zugelassen wird.</span><span class="sxs-lookup"><span data-stu-id="f3b81-143">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="f3b81-144">Das aktivieren <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> führt dazu, dass EF diese try-catch-Blöcke einführt und somit ausführlichere Fehler bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="f3b81-144">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="f3b81-145">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f3b81-145">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a><span data-ttu-id="f3b81-146">Filtern</span><span class="sxs-lookup"><span data-stu-id="f3b81-146">Filtering</span></span>

### <a name="log-levels"></a><span data-ttu-id="f3b81-147">Protokollebenen</span><span class="sxs-lookup"><span data-stu-id="f3b81-147">Log levels</span></span>

<span data-ttu-id="f3b81-148">Jede EF Core Log-Nachricht wird einer Ebene zugewiesen, die von der-Aufzählung definiert wird <xref:Microsoft.Extensions.Logging.LogLevel> .</span><span class="sxs-lookup"><span data-stu-id="f3b81-148">Every EF Core log message is assigned to a level defined by the <xref:Microsoft.Extensions.Logging.LogLevel> enum.</span></span> <span data-ttu-id="f3b81-149">Standardmäßig enthält EF Core einfache Protokollierung jede Nachricht auf der `Debug` Ebene oder höher.</span><span class="sxs-lookup"><span data-stu-id="f3b81-149">By default, EF Core simple logging includes every message at `Debug` level or above.</span></span> <span data-ttu-id="f3b81-150">`LogTo` kann ein höheres Mindestwert für das Herausfiltern von Nachrichten weitergegeben werden.</span><span class="sxs-lookup"><span data-stu-id="f3b81-150">`LogTo` can be passed a higher minimum level to filter out some messages.</span></span> <span data-ttu-id="f3b81-151">Wenn z. b `Information` . die Übergabe von zu einem minimalen Satz von Protokollen durchführt, ist dies auf den Datenbankzugriff und einige in der</span><span class="sxs-lookup"><span data-stu-id="f3b81-151">For example, passing `Information` results in a minimal set of logs limited to database access and some housekeeping messages.</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a><span data-ttu-id="f3b81-152">Bestimmte Nachrichten</span><span class="sxs-lookup"><span data-stu-id="f3b81-152">Specific messages</span></span>

<span data-ttu-id="f3b81-153">Jeder Protokollmeldung wird eine zugewiesen <xref:Microsoft.Extensions.Logging.EventId> .</span><span class="sxs-lookup"><span data-stu-id="f3b81-153">Every log message is assigned an <xref:Microsoft.Extensions.Logging.EventId>.</span></span> <span data-ttu-id="f3b81-154">Auf diese IDs kann von der- <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> Klasse oder der- <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> Klasse für relationale spezifische Nachrichten zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="f3b81-154">These IDs can be accessed from the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> class or the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> class for relational-specific messages.</span></span> <span data-ttu-id="f3b81-155">Ein Datenbankanbieter kann auch anbieterspezifische IDs in einer ähnlichen Klasse aufweisen.</span><span class="sxs-lookup"><span data-stu-id="f3b81-155">A database provider may also have provider-specific IDs in a similar class.</span></span> <span data-ttu-id="f3b81-156">Beispielsweise <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> für den SQL Server-Anbieter.</span><span class="sxs-lookup"><span data-stu-id="f3b81-156">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="f3b81-157">`LogTo` kann so konfiguriert werden, dass nur die einer oder mehreren Ereignis-IDs zugeordneten Nachrichten protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="f3b81-157">`LogTo` can be configured to only log the messages associated with one or more event IDs.</span></span> <span data-ttu-id="f3b81-158">Zum Protokollieren von Meldungen für den Kontext, der initialisiert oder verworfen wird, z. b.:</span><span class="sxs-lookup"><span data-stu-id="f3b81-158">For example, to log only messages for the context being initialized or disposed:</span></span>  

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a><span data-ttu-id="f3b81-159">Meldungskategorien</span><span class="sxs-lookup"><span data-stu-id="f3b81-159">Message categories</span></span>

<span data-ttu-id="f3b81-160">Jede Protokollmeldung wird einer benannten hierarchischen Protokollierungs Kategorie zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="f3b81-160">Every log message is assigned to a named hierarchical logger category.</span></span> <span data-ttu-id="f3b81-161">Die Kategorien lauten:</span><span class="sxs-lookup"><span data-stu-id="f3b81-161">The categories are:</span></span>

| <span data-ttu-id="f3b81-162">Category</span><span class="sxs-lookup"><span data-stu-id="f3b81-162">Category</span></span>                                             | <span data-ttu-id="f3b81-163">Meldungen</span><span class="sxs-lookup"><span data-stu-id="f3b81-163">Messages</span></span>
|:-----------------------------------------------------|-------------------------------------------------
| <span data-ttu-id="f3b81-164">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="f3b81-164">Microsoft.EntityFrameworkCore</span></span>                        | <span data-ttu-id="f3b81-165">Alle EF Core Meldungen</span><span class="sxs-lookup"><span data-stu-id="f3b81-165">All EF Core messages</span></span>
| <span data-ttu-id="f3b81-166">Microsoft. entityframeworkcore. Database</span><span class="sxs-lookup"><span data-stu-id="f3b81-166">Microsoft.EntityFrameworkCore.Database</span></span>               | <span data-ttu-id="f3b81-167">Alle Daten Bank Interaktionen</span><span class="sxs-lookup"><span data-stu-id="f3b81-167">All database interactions</span></span>
| <span data-ttu-id="f3b81-168">Microsoft. entityframeworkcore. Database. Connection</span><span class="sxs-lookup"><span data-stu-id="f3b81-168">Microsoft.EntityFrameworkCore.Database.Connection</span></span>    | <span data-ttu-id="f3b81-169">Verwendung einer Datenbankverbindung</span><span class="sxs-lookup"><span data-stu-id="f3b81-169">Uses of a database connection</span></span>
| <span data-ttu-id="f3b81-170">Microsoft. entityframeworkcore. Database. Command</span><span class="sxs-lookup"><span data-stu-id="f3b81-170">Microsoft.EntityFrameworkCore.Database.Command</span></span>       | <span data-ttu-id="f3b81-171">Verwendung eines Daten Bank Befehls</span><span class="sxs-lookup"><span data-stu-id="f3b81-171">Uses of a database command</span></span>
| <span data-ttu-id="f3b81-172">Microsoft. entityframeworkcore. Database. Transaction</span><span class="sxs-lookup"><span data-stu-id="f3b81-172">Microsoft.EntityFrameworkCore.Database.Transaction</span></span>   | <span data-ttu-id="f3b81-173">Verwendung einer Datenbanktransaktion</span><span class="sxs-lookup"><span data-stu-id="f3b81-173">Uses of a database transaction</span></span>
| <span data-ttu-id="f3b81-174">Microsoft. entityframeworkcore. Update</span><span class="sxs-lookup"><span data-stu-id="f3b81-174">Microsoft.EntityFrameworkCore.Update</span></span>                 | <span data-ttu-id="f3b81-175">Speichern von Entitäten, Ausschließen von Daten Bank Interaktionen</span><span class="sxs-lookup"><span data-stu-id="f3b81-175">Saving entities, excluding database interactions</span></span>
| <span data-ttu-id="f3b81-176">Microsoft. entityframeworkcore. Model</span><span class="sxs-lookup"><span data-stu-id="f3b81-176">Microsoft.EntityFrameworkCore.Model</span></span>                  | <span data-ttu-id="f3b81-177">Alle Modell-und metadateninteraktionen</span><span class="sxs-lookup"><span data-stu-id="f3b81-177">All model and metadata interactions</span></span>
| <span data-ttu-id="f3b81-178">Microsoft. entityframeworkcore. Model. Validation</span><span class="sxs-lookup"><span data-stu-id="f3b81-178">Microsoft.EntityFrameworkCore.Model.Validation</span></span>       | <span data-ttu-id="f3b81-179">Modellvalidierung</span><span class="sxs-lookup"><span data-stu-id="f3b81-179">Model validation</span></span>
| <span data-ttu-id="f3b81-180">Microsoft. entityframeworkcore. Query</span><span class="sxs-lookup"><span data-stu-id="f3b81-180">Microsoft.EntityFrameworkCore.Query</span></span>                  | <span data-ttu-id="f3b81-181">Abfragen, ausgenommen Daten Bank Interaktionen</span><span class="sxs-lookup"><span data-stu-id="f3b81-181">Queries, excluding database interactions</span></span>
| <span data-ttu-id="f3b81-182">Microsoft. entityframeworkcore. Infrastructure</span><span class="sxs-lookup"><span data-stu-id="f3b81-182">Microsoft.EntityFrameworkCore.Infrastructure</span></span>         | <span data-ttu-id="f3b81-183">Allgemeine Ereignisse, z. b. die Erstellung von Kontexten</span><span class="sxs-lookup"><span data-stu-id="f3b81-183">General events, such as context creation</span></span>
| <span data-ttu-id="f3b81-184">Microsoft. entityframeworkcore. Gerüstbau</span><span class="sxs-lookup"><span data-stu-id="f3b81-184">Microsoft.EntityFrameworkCore.Scaffolding</span></span>            | <span data-ttu-id="f3b81-185">Daten Bank Reverse Engineering</span><span class="sxs-lookup"><span data-stu-id="f3b81-185">Database reverse engineering</span></span>
| <span data-ttu-id="f3b81-186">Microsoft. entityframeworkcore. Migrationen</span><span class="sxs-lookup"><span data-stu-id="f3b81-186">Microsoft.EntityFrameworkCore.Migrations</span></span>             | <span data-ttu-id="f3b81-187">Migrationen</span><span class="sxs-lookup"><span data-stu-id="f3b81-187">Migrations</span></span>
| <span data-ttu-id="f3b81-188">Microsoft. entityframeworkcore. changeTracking</span><span class="sxs-lookup"><span data-stu-id="f3b81-188">Microsoft.EntityFrameworkCore.ChangeTracking</span></span>         | <span data-ttu-id="f3b81-189">Interaktionen mit Änderungs Nachverfolgung</span><span class="sxs-lookup"><span data-stu-id="f3b81-189">Change tracking interactions</span></span>

<span data-ttu-id="f3b81-190">`LogTo` kann so konfiguriert werden, dass nur die Nachrichten aus einer oder mehreren Kategorien protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="f3b81-190">`LogTo` can be configured to only log the messages from one or more categories.</span></span> <span data-ttu-id="f3b81-191">Beispielsweise, um nur Daten Bank Interaktionen zu protokollieren:</span><span class="sxs-lookup"><span data-stu-id="f3b81-191">For example, to log only database interactions:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

<span data-ttu-id="f3b81-192">Beachten Sie, dass die- <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> Klasse eine hierarchische API zum Suchen einer Kategorie bereitstellt und die Notwendigkeit, Zeichen folgen hart zu codieren, vermeidet.</span><span class="sxs-lookup"><span data-stu-id="f3b81-192">Notice that the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class provides a hierarchical API for finding a category and avoids the need to hard-code strings.</span></span>

<span data-ttu-id="f3b81-193">Da Kategorien hierarchisch sind, beinhaltet dieses Beispiel mit der `Database` Kategorie alle Nachrichten für die Unterkategorien `Database.Connection` , `Database.Command` und `Database.Transaction` .</span><span class="sxs-lookup"><span data-stu-id="f3b81-193">Since categories are hierarchical, this example using the `Database` category will include all messages for the subcategories `Database.Connection`, `Database.Command`, and `Database.Transaction`.</span></span>

### <a name="custom-filters"></a><span data-ttu-id="f3b81-194">Benutzerdefinierte Filter</span><span class="sxs-lookup"><span data-stu-id="f3b81-194">Custom filters</span></span>

<span data-ttu-id="f3b81-195">`LogTo` ermöglicht die Verwendung eines benutzerdefinierten Filters in Fällen, in denen keine der oben genannten Filteroptionen ausreicht.</span><span class="sxs-lookup"><span data-stu-id="f3b81-195">`LogTo` allows a custom filter to be used for cases where none of the filtering options above are sufficient.</span></span> <span data-ttu-id="f3b81-196">Zum Protokollieren von Nachrichten auf der Ebene `Information` oder höher sowie Nachrichten zum Öffnen und Schließen einer Verbindung:</span><span class="sxs-lookup"><span data-stu-id="f3b81-196">For example, to log any message at level `Information` or above, as well as messages for opening and closing a connection:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(
                Console.WriteLine,
                (eventId, logLevel) => logLevel >= LogLevel.Information
                                       || eventId == RelationalEventId.ConnectionOpened
                                       || eventId == RelationalEventId.ConnectionClosed);
-->
[!code-csharp[CustomFilter](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=CustomFilter)]

> [!TIP]
> <span data-ttu-id="f3b81-197">Das Filtern mit benutzerdefinierten Filtern oder das Verwenden einer der hier gezeigten anderen Optionen ist effizienter als das Filtern im logto-Delegaten.</span><span class="sxs-lookup"><span data-stu-id="f3b81-197">Filtering using custom filters or using any of the other options shown here is more efficient than filtering in the LogTo delegate.</span></span> <span data-ttu-id="f3b81-198">Dies liegt daran, dass die Protokollmeldung nicht einmal erstellt wird, wenn der Filter bestimmt, dass die Nachricht nicht protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="f3b81-198">This is because if the filter determines the message should not be logged, then the log message is not even created.</span></span>

## <a name="configuration-for-specific-messages"></a><span data-ttu-id="f3b81-199">Konfiguration für bestimmte Nachrichten</span><span class="sxs-lookup"><span data-stu-id="f3b81-199">Configuration for specific messages</span></span>

<span data-ttu-id="f3b81-200">Die EF Core- <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API ermöglicht es Anwendungen, zu ändern, was geschieht, wenn ein bestimmtes Ereignis auftritt.</span><span class="sxs-lookup"><span data-stu-id="f3b81-200">The EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API allows applications to change what happens when a specific event is encountered.</span></span> <span data-ttu-id="f3b81-201">Dies kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="f3b81-201">This can be used to:</span></span>

* <span data-ttu-id="f3b81-202">Ändern Sie die Protokollebene, bei der das Ereignis protokolliert wird.</span><span class="sxs-lookup"><span data-stu-id="f3b81-202">Change the log level at which the event is logged</span></span>
* <span data-ttu-id="f3b81-203">Protokollierung des Ereignisses überspringen</span><span class="sxs-lookup"><span data-stu-id="f3b81-203">Skip logging the event altogether</span></span>
* <span data-ttu-id="f3b81-204">Löst eine Ausnahme aus, wenn das Ereignis eintritt.</span><span class="sxs-lookup"><span data-stu-id="f3b81-204">Throw an exception when the event occurs</span></span>

### <a name="changing-the-log-level-for-an-event"></a><span data-ttu-id="f3b81-205">Ändern der Protokollebene für ein Ereignis</span><span class="sxs-lookup"><span data-stu-id="f3b81-205">Changing the log level for an event</span></span>

<span data-ttu-id="f3b81-206">Im vorherigen Beispiel wurde ein benutzerdefinierter Filter verwendet, um jede Nachricht bei `LogLevel.Information` und zwei für definierte Ereignisse zu protokollieren `LogLevel.Debug` .</span><span class="sxs-lookup"><span data-stu-id="f3b81-206">The previous example used a custom filter to log every message at `LogLevel.Information` as well as two events defined for `LogLevel.Debug`.</span></span> <span data-ttu-id="f3b81-207">Dies kann erreicht werden, indem die Protokollebene der beiden `Debug` Ereignisse in geändert wird `Information` :</span><span class="sxs-lookup"><span data-stu-id="f3b81-207">The same can be achieved by changing the log level of the two `Debug` events to `Information`:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Log(
                (RelationalEventId.ConnectionOpened, LogLevel.Information),
                (RelationalEventId.ConnectionClosed, LogLevel.Information)))
            .LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a><span data-ttu-id="f3b81-208">Protokollierung eines Ereignisses unterdrücken</span><span class="sxs-lookup"><span data-stu-id="f3b81-208">Suppress logging an event</span></span>

<span data-ttu-id="f3b81-209">Auf ähnliche Weise kann ein einzelnes Ereignis aus der Protokollierung unterdrückt werden.</span><span class="sxs-lookup"><span data-stu-id="f3b81-209">In a similar way, an individual event can be suppressed from logging.</span></span> <span data-ttu-id="f3b81-210">Dies ist besonders nützlich, wenn eine Warnung ignoriert werden soll, die überprüft und verstanden wurde.</span><span class="sxs-lookup"><span data-stu-id="f3b81-210">This is particularly useful for ignoring a warning that has been reviewed and understood.</span></span> <span data-ttu-id="f3b81-211">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f3b81-211">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a><span data-ttu-id="f3b81-212">Auslösen für ein Ereignis</span><span class="sxs-lookup"><span data-stu-id="f3b81-212">Throw for an event</span></span>

<span data-ttu-id="f3b81-213">Zum Schluss können EF Core so konfiguriert werden, dass für ein bestimmtes Ereignis ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="f3b81-213">Finally, EF Core can be configured to throw for a given event.</span></span> <span data-ttu-id="f3b81-214">Dies ist besonders nützlich, um eine Warnung in einen Fehler zu ändern.</span><span class="sxs-lookup"><span data-stu-id="f3b81-214">This is particularly useful for changing a warning into an error.</span></span> <span data-ttu-id="f3b81-215">(Dies war tatsächlich der ursprüngliche Zweck der- `ConfigureWarnings` Methode, daher der Name.) Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f3b81-215">(Indeed, this was the original purpose of `ConfigureWarnings` method, hence the name.) For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Throw(RelationalEventId.MultipleCollectionIncludeWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ThrowForEvent)]

## <a name="message-contents-and-formatting"></a><span data-ttu-id="f3b81-216">Nachrichten Inhalt und-Formatierung</span><span class="sxs-lookup"><span data-stu-id="f3b81-216">Message contents and formatting</span></span>

<span data-ttu-id="f3b81-217">Der Standard Inhalt von `LogTo` wird über mehrere Zeilen formatiert.</span><span class="sxs-lookup"><span data-stu-id="f3b81-217">The default content from `LogTo` is formatted across multiple lines.</span></span> <span data-ttu-id="f3b81-218">Die erste Zeile enthält Nachrichten Metadaten:</span><span class="sxs-lookup"><span data-stu-id="f3b81-218">The first line contains message metadata:</span></span>

* <span data-ttu-id="f3b81-219">Das <xref:Microsoft.Extensions.Logging.LogLevel> -Zeichen als vier-Zeichen-Präfix.</span><span class="sxs-lookup"><span data-stu-id="f3b81-219">The <xref:Microsoft.Extensions.Logging.LogLevel> as a four-character prefix</span></span>
* <span data-ttu-id="f3b81-220">Ein lokaler Zeitstempel, der für die aktuelle Kultur formatiert ist.</span><span class="sxs-lookup"><span data-stu-id="f3b81-220">A local timestamp, formatted for the current culture</span></span>
* <span data-ttu-id="f3b81-221">Das <xref:Microsoft.Extensions.Logging.EventId> in der Form, das kopiert/eingefügt werden kann, um den Member aus <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> oder eine der anderen Klassen sowie den unformatierten ID-Wert zu erhalten `EventId` .</span><span class="sxs-lookup"><span data-stu-id="f3b81-221">The <xref:Microsoft.Extensions.Logging.EventId> in the form that can be copy/pasted to get the member from <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> or one of the other `EventId` classes, plus the raw ID value</span></span>
* <span data-ttu-id="f3b81-222">Die Ereignis Kategorie, wie oben beschrieben.</span><span class="sxs-lookup"><span data-stu-id="f3b81-222">The event category, as described above.</span></span>

<span data-ttu-id="f3b81-223">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f3b81-223">For example:</span></span>

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

<span data-ttu-id="f3b81-224">Dieser Inhalt kann durch Übergeben von Werten von [dbcontextloggeroptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15) angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="f3b81-224">This content can be customized by passing values from [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> --><span data-ttu-id="f3b81-225">, wie in den folgenden Abschnitten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="f3b81-225">, as shown in the following sections.</span></span>

> [!TIP]
> <span data-ttu-id="f3b81-226">Verwenden Sie [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) , um die Protokoll Formatierung besser steuern zu können.</span><span class="sxs-lookup"><span data-stu-id="f3b81-226">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for more control over log formatting.</span></span>

### <a name="using-utc-time"></a><span data-ttu-id="f3b81-227">Verwenden der UTC-Zeit</span><span class="sxs-lookup"><span data-stu-id="f3b81-227">Using UTC time</span></span>

<span data-ttu-id="f3b81-228">Standardmäßig sind timestamnps für den lokalen Verbrauch während des Debuggens konzipiert.</span><span class="sxs-lookup"><span data-stu-id="f3b81-228">By default, timestamnps are designed for local consumption while debugging.</span></span> <span data-ttu-id="f3b81-229">Verwenden `DbContextLoggerOptions.DefaultWithUtcTime` Sie stattdessen, um die Kultur unabhängigen UTC-Zeitstempel zu verwenden, aber behalten Sie alle anderen Werte bei.</span><span class="sxs-lookup"><span data-stu-id="f3b81-229">Use `DbContextLoggerOptions.DefaultWithUtcTime` to use culture-agnostic UTC timestamps instead, but keep everything else the same.</span></span> <span data-ttu-id="f3b81-230">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f3b81-230">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

<span data-ttu-id="f3b81-231">In diesem Beispiel wird die folgende Protokoll Formatierung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f3b81-231">This example results in the following log formatting:</span></span>

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

### <a name="single-line-logging"></a><span data-ttu-id="f3b81-232">Einzeilige Protokollierung</span><span class="sxs-lookup"><span data-stu-id="f3b81-232">Single line logging</span></span>

<span data-ttu-id="f3b81-233">Manchmal ist es hilfreich, genau eine Zeile pro Protokollnachricht zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="f3b81-233">Sometimes it is useful to get exactly one line per log message.</span></span> <span data-ttu-id="f3b81-234">Dies kann durch aktiviert werden `DbContextLoggerOptions.SingleLine` .</span><span class="sxs-lookup"><span data-stu-id="f3b81-234">This can be enabled by `DbContextLoggerOptions.SingleLine`.</span></span> <span data-ttu-id="f3b81-235">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f3b81-235">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

<span data-ttu-id="f3b81-236">In diesem Beispiel wird die folgende Protokoll Formatierung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f3b81-236">This example results in the following log formatting:</span></span>

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a><span data-ttu-id="f3b81-237">Weitere Inhalts Optionen</span><span class="sxs-lookup"><span data-stu-id="f3b81-237">Other content options</span></span>

<span data-ttu-id="f3b81-238">Andere Flags in [dbcontextloggeroptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span><span class="sxs-lookup"><span data-stu-id="f3b81-238">Other flags in [DbContextLoggerOptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> --> <span data-ttu-id="f3b81-239">kann verwendet werden, um die Menge der im Protokoll enthaltenen Metadaten zu kürzen.</span><span class="sxs-lookup"><span data-stu-id="f3b81-239">can be used to trim down the amount of metadata included in the log.</span></span> <span data-ttu-id="f3b81-240">Dies kann in Verbindung mit der einzeiligen Protokollierung nützlich sein.</span><span class="sxs-lookup"><span data-stu-id="f3b81-240">This is can be useful in conjunction with single-line logging.</span></span> <span data-ttu-id="f3b81-241">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f3b81-241">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

<span data-ttu-id="f3b81-242">In diesem Beispiel wird die folgende Protokoll Formatierung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f3b81-242">This example results in the following log formatting:</span></span>

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a><span data-ttu-id="f3b81-243">Verschieben von EF6</span><span class="sxs-lookup"><span data-stu-id="f3b81-243">Moving from EF6</span></span>

<span data-ttu-id="f3b81-244">EF Core einfache Protokollierung unterscheidet <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> sich von in EF6 auf zwei Arten:</span><span class="sxs-lookup"><span data-stu-id="f3b81-244">EF Core simple logging differs from <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6 in two important ways:</span></span>

* <span data-ttu-id="f3b81-245">Protokollmeldungen sind nicht auf Daten Bank Interaktionen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="f3b81-245">Log messages are not limited to only database interactions</span></span>
* <span data-ttu-id="f3b81-246">Die Protokollierung muss zum Zeitpunkt der kontextinitialisierung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="f3b81-246">The logging must be configured at context initialization time</span></span>

<span data-ttu-id="f3b81-247">Für den ersten Unterschied kann die oben beschriebene Filterung verwendet werden, um einzuschränken, welche Nachrichten protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="f3b81-247">For the first difference, the filtering described above can be used to limit which messages are logged.</span></span>

<span data-ttu-id="f3b81-248">Der zweite Unterschied ist eine absichtliche Änderung, um die Leistung zu verbessern, indem keine Protokollmeldungen erzeugt werden, wenn Sie nicht benötigt werden</span><span class="sxs-lookup"><span data-stu-id="f3b81-248">The second difference is an intentional change to improve performance by not generating log messages when they are not needed.</span></span> <span data-ttu-id="f3b81-249">Es ist jedoch weiterhin möglich, ein ähnliches Verhalten zu EF6 zu erzielen, indem Sie eine `Log` -Eigenschaft auf Ihrem erstellen `DbContext` und diese dann nur dann verwenden, wenn Sie festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="f3b81-249">However, it is still possible to get a similar behavior to EF6 by creating a `Log` property on your `DbContext` and then using it only when it has been set.</span></span> <span data-ttu-id="f3b81-250">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f3b81-250">For example:</span></span>

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
