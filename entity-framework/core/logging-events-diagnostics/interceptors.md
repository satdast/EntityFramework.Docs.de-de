---
title: Interceptors-EF Core
description: Abfangen von Daten Bank Vorgängen und anderen Ereignissen
author: ajcvickers
ms.date: 10/08/2020
uid: core/logging-events-diagnostics/interceptors
ms.openlocfilehash: e3b2f1a0f1a97d211bcaba0633955a7fe9c0aa91
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128588"
---
# <a name="interceptors"></a><span data-ttu-id="75c0e-103">Interceptors</span><span class="sxs-lookup"><span data-stu-id="75c0e-103">Interceptors</span></span>

<span data-ttu-id="75c0e-104">Entity Framework Core (EF Core)-Interceptors ermöglichen das Abfangen, ändern und/oder Unterdrücken von EF Core Vorgängen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-104">Entity Framework Core (EF Core) interceptors enable interception, modification, and/or suppression of EF Core operations.</span></span> <span data-ttu-id="75c0e-105">Dies umfasst Datenbankvorgänge auf niedriger Ebene, z. B. das Ausführen eines Befehls, sowie Vorgänge höherer Ebene, z. B. Aufrufe von SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="75c0e-105">This includes low-level database operations such as executing a command, as well as higher-level operations, such as calls to SaveChanges.</span></span>

<span data-ttu-id="75c0e-106">Abfangfunktionen unterscheiden sich von Protokollierung und Diagnose insofern, als sie eine Änderung oder Unterdrückung des abzufangenden Vorgangs ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-106">Interceptors are different from logging and diagnostics in that they allow modification or suppression of the operation being intercepted.</span></span> <span data-ttu-id="75c0e-107">[Einfache Protokollierung](xref:core/logging-events-diagnostics/simple-logging) oder [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) sind bessere Optionen für Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="75c0e-107">[Simple logging](xref:core/logging-events-diagnostics/simple-logging) or [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) are better choices for logging.</span></span>

<span data-ttu-id="75c0e-108">Abfangfunktionen werden pro DbContext-Instanz registriert, wenn der Kontext konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="75c0e-108">Interceptors are registered per DbContext instance when the context is configured.</span></span> <span data-ttu-id="75c0e-109">Verwenden Sie einen [Diagnoselistener](xref:core/logging-events-diagnostics/diagnostic-listeners), um dieselben Informationen abzurufen, jedoch für alle DbContext-Instanzen im Prozess.</span><span class="sxs-lookup"><span data-stu-id="75c0e-109">Use a [diagnostic listener](xref:core/logging-events-diagnostics/diagnostic-listeners) to get the same information but for all DbContext instances in the process.</span></span>

## <a name="registering-interceptors"></a><span data-ttu-id="75c0e-110">Interceptors werden registriert</span><span class="sxs-lookup"><span data-stu-id="75c0e-110">Registering interceptors</span></span>

<span data-ttu-id="75c0e-111">Interceptors werden <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> beim [Konfigurieren einer dbcontext-Instanz](xref:core/dbcontext-configuration/index)mithilfe von registriert.</span><span class="sxs-lookup"><span data-stu-id="75c0e-111">Interceptors are registered using <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> when [configuring a DbContext instance](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="75c0e-112">Dies erfolgt in der Regel in einer außer Kraft Setzung von <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="75c0e-112">This is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="75c0e-113">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="75c0e-113">For example:</span></span>

<!--
public class ExampleContext : BlogsContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.AddInterceptors(new TaggedQueryCommandInterceptor());
}
-->
[!code-csharp[RegisterInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterInterceptor)]

<span data-ttu-id="75c0e-114">Alternativ `AddInterceptors` kann auch als Teil von oder aufgerufen werden, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> Wenn eine-Instanz erstellt wird <xref:Microsoft.EntityFrameworkCore.DbContextOptions> , die an den dbcontext-Konstruktor übergeben werden soll.</span><span class="sxs-lookup"><span data-stu-id="75c0e-114">Alternately, `AddInterceptors` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the DbContext constructor.</span></span>

> [!TIP]
> <span data-ttu-id="75c0e-115">On-Konfiguration wird immer noch aufgerufen, wenn adddbcontext verwendet wird oder eine dbcontextoptions-Instanz an den dbcontext-Konstruktor übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="75c0e-115">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="75c0e-116">Dies ist der ideale Ort zum Anwenden der Kontext Konfiguration, unabhängig davon, wie der dbcontext erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="75c0e-116">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

<span data-ttu-id="75c0e-117">Interceptors sind häufig zustandslos. Dies bedeutet, dass eine einzelne Interceptor Instanz für alle dbcontext-Instanzen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="75c0e-117">Interceptors are often stateless, which means that a single interceptor instance can be used for all DbContext instances.</span></span> <span data-ttu-id="75c0e-118">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="75c0e-118">For example:</span></span>

<!--
public class TaggedQueryCommandInterceptorContext : BlogsContext
{
    private static readonly TaggedQueryCommandInterceptor _interceptor
        = new TaggedQueryCommandInterceptor();

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.AddInterceptors(_interceptor);
}
-->
[!code-csharp[RegisterStatelessInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterStatelessInterceptor)]

<span data-ttu-id="75c0e-119">Jede Interceptor Instanz muss eine oder mehrere Schnittstellen implementieren, die von abgeleitet werden <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor> .</span><span class="sxs-lookup"><span data-stu-id="75c0e-119">Every interceptor instance must implement one or more interface derived from <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor>.</span></span> <span data-ttu-id="75c0e-120">Jede Instanz sollte nur einmal registriert werden, auch wenn Sie mehrere Abfang Schnittstellen implementiert. EF Core leiten Ereignisse für jede Schnittstelle nach Bedarf weiter.</span><span class="sxs-lookup"><span data-stu-id="75c0e-120">Each instance should only be registered once even if it implements multiple interception interfaces; EF Core will route events for each interface as appropriate.</span></span>

## <a name="database-interception"></a><span data-ttu-id="75c0e-121">Daten Bank Abfang</span><span class="sxs-lookup"><span data-stu-id="75c0e-121">Database interception</span></span>

> [!NOTE]
> <span data-ttu-id="75c0e-122">Die Daten Bank Abfang Funktion wurde in EF Core 3,0 eingeführt und ist nur für relationale Datenbankanbieter verfügbar.</span><span class="sxs-lookup"><span data-stu-id="75c0e-122">Database interception was introduced in EF Core 3.0 and is only available for relational database providers.</span></span>
> <span data-ttu-id="75c0e-123">Die SAVEPOINT-Unterstützung wurde in EF Core 5,0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="75c0e-123">Savepoint support was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="75c0e-124">Die Daten Bank Abfang Funktion auf niedriger Ebene wird in die drei in der folgenden Tabelle dargestellten Schnittstellen aufgeteilt.</span><span class="sxs-lookup"><span data-stu-id="75c0e-124">Low-level database interception is split into the three interfaces shown in the following table.</span></span>

| <span data-ttu-id="75c0e-125">Interceptor</span><span class="sxs-lookup"><span data-stu-id="75c0e-125">Interceptor</span></span>                                                            | <span data-ttu-id="75c0e-126">Daten Bank Vorgänge abgefangen</span><span class="sxs-lookup"><span data-stu-id="75c0e-126">Database operations intercepted</span></span>
|:-----------------------------------------------------------------------|-------------------------------------------------
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> | <span data-ttu-id="75c0e-127">Erstellen von Befehlen</span><span class="sxs-lookup"><span data-stu-id="75c0e-127">Creating commands</span></span></br><span data-ttu-id="75c0e-128">Ausführen von Befehlen</span><span class="sxs-lookup"><span data-stu-id="75c0e-128">Executing commands</span></span></br><span data-ttu-id="75c0e-129">Befehls Fehler</span><span class="sxs-lookup"><span data-stu-id="75c0e-129">Command failures</span></span></br><span data-ttu-id="75c0e-130">Verwerfen des DbDataReader des Befehls</span><span class="sxs-lookup"><span data-stu-id="75c0e-130">Disposing the command's DbDataReader</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> | <span data-ttu-id="75c0e-131">Öffnen und Schließen von Verbindungen</span><span class="sxs-lookup"><span data-stu-id="75c0e-131">Opening and closing connections</span></span></br><span data-ttu-id="75c0e-132">Verbindungsfehler</span><span class="sxs-lookup"><span data-stu-id="75c0e-132">Connection failures</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbTransactionInterceptor> | <span data-ttu-id="75c0e-133">Erstellen von Transaktionen</span><span class="sxs-lookup"><span data-stu-id="75c0e-133">Creating transactions</span></span></br><span data-ttu-id="75c0e-134">Verwenden vorhandener Transaktionen</span><span class="sxs-lookup"><span data-stu-id="75c0e-134">Using existing transactions</span></span></br><span data-ttu-id="75c0e-135">Commit für Transaktionen</span><span class="sxs-lookup"><span data-stu-id="75c0e-135">Committing transactions</span></span></br><span data-ttu-id="75c0e-136">Rollback von Transaktionen</span><span class="sxs-lookup"><span data-stu-id="75c0e-136">Rolling back transactions</span></span></br><span data-ttu-id="75c0e-137">Erstellen und Verwenden von Sicherungs Punkten</span><span class="sxs-lookup"><span data-stu-id="75c0e-137">Creating and using savepoints</span></span></br><span data-ttu-id="75c0e-138">Transaktionsfehler</span><span class="sxs-lookup"><span data-stu-id="75c0e-138">Transaction failures</span></span>

<span data-ttu-id="75c0e-139">Die Basisklassen <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> , <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor> und <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> enthalten keine-op-Implementierungen für jede Methode in der entsprechenden-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="75c0e-139">The base classes <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor>, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor>, and <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> contain no-op implementations for each method in the corresponding interface.</span></span> <span data-ttu-id="75c0e-140">Verwenden Sie die Basisklassen, um zu vermeiden, dass nicht verwendete Abhörmethoden implementiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-140">Use the base classes to avoid the need to implement unused interception methods.</span></span>

<span data-ttu-id="75c0e-141">Die Methoden für die einzelnen Interceptor Typen sind paarweise, wobei der erste aufgerufen wird, bevor der Daten Bank Vorgang gestartet wird, und der zweite, nachdem der Vorgang abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="75c0e-141">The methods on each interceptor type come in pairs, with the first being called before the database operation is started, and the second after the operation has completed.</span></span> <span data-ttu-id="75c0e-142">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="75c0e-142">For example.</span></span> <span data-ttu-id="75c0e-143">Beispielsweise <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> wird aufgerufen, bevor eine Abfrage ausgeführt wird, und <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> wird aufgerufen, nachdem die Abfrage an die Datenbank gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="75c0e-143">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> is called before a query is executed, and <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> is called after query has been sent to the database.</span></span>

<span data-ttu-id="75c0e-144">Jedes Methoden paar verfügt sowohl über Synchronisierungs-als auch Async-Variationen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-144">Each pair of methods have both sync and async variations.</span></span> <span data-ttu-id="75c0e-145">Dies ermöglicht es, dass asynchrone e/a-Vorgänge (z. b. das Anfordern eines Zugriffs Tokens) als Teil des Abfangen eines asynchronen Daten Bank Vorgangs durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="75c0e-145">This allows for asynchronous I/O, such as requesting an access token, to happen as part of intercepting an async database operation.</span></span>

### <a name="example-command-interception-to-add-query-hints"></a><span data-ttu-id="75c0e-146">Beispiel: Befehls Abfang Funktion zum Hinzufügen von Abfrage hinweisen</span><span class="sxs-lookup"><span data-stu-id="75c0e-146">Example: Command interception to add query hints</span></span>

> [!TIP]
> <span data-ttu-id="75c0e-147">Sie können [das Beispiel für den Befehl Interceptor](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CommandInterception) von GitHub herunterladen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-147">You can [download the command interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CommandInterception) from GitHub.</span></span>

<span data-ttu-id="75c0e-148">Ein <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> kann verwendet werden, um SQL zu ändern, bevor es an die Datenbank gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="75c0e-148">An <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> can be used to modify SQL before it is sent to the database.</span></span> <span data-ttu-id="75c0e-149">Dieses Beispiel zeigt, wie Sie die SQL-Datei so ändern, dass Sie einen Abfrage Hinweis enthält.</span><span class="sxs-lookup"><span data-stu-id="75c0e-149">This example shows how to modify the SQL to include a query hint.</span></span>

<span data-ttu-id="75c0e-150">Häufig ist der schwierigste Teil der Abfang Funktion die Bestimmung, wann der Befehl der Abfrage entspricht, die geändert werden muss.</span><span class="sxs-lookup"><span data-stu-id="75c0e-150">Often, the trickiest part of the interception is determining when the command corresponds to the query that needs to be modified.</span></span> <span data-ttu-id="75c0e-151">Die SQL-Verarbeitung ist eine Option, Sie ist jedoch tendenziell anfällig.</span><span class="sxs-lookup"><span data-stu-id="75c0e-151">Parsing the SQL is one option, but tends to be fragile.</span></span> <span data-ttu-id="75c0e-152">Eine andere Möglichkeit ist die Verwendung [EF Core Abfrage Tags](xref:core/querying/tags) , um jede zu ändernde Abfrage zu kennzeichnen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-152">Another option is to use [EF Core query tags](xref:core/querying/tags) to tag each query that should be modified.</span></span> <span data-ttu-id="75c0e-153">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="75c0e-153">For example:</span></span>

<!--
            var blogs1 = context.Blogs.TagWith("Use hint: robust plan").ToList();
-->
[!code-csharp[TaggedQuery](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=TaggedQuery)]

<span data-ttu-id="75c0e-154">Dieses Tag kann dann im Interceptor erkannt werden, da es in der ersten Zeile des Befehls Texts immer als Kommentar eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="75c0e-154">This tag can then be detected in the interceptor as it will always be included as a comment in the first line of the command text.</span></span> <span data-ttu-id="75c0e-155">Wenn Sie das-Tag erkennen, wird die SQL-Abfrage geändert, um den entsprechenden Hinweis hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="75c0e-155">On detecting the tag, the query SQL is modified to add the appropriate hint:</span></span>

<!--
public class TaggedQueryCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult<DbDataReader> ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result)
    {
        ManipulateCommand(command);

        return result;
    }

    public override ValueTask<InterceptionResult<DbDataReader>> ReaderExecutingAsync(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result,
        CancellationToken cancellationToken = default)
    {
        ManipulateCommand(command);

        return new ValueTask<InterceptionResult<DbDataReader>>(result);
    }

    private static void ManipulateCommand(DbCommand command)
    {
        if (command.CommandText.StartsWith("-- Use hint: robust plan", StringComparison.Ordinal))
        {
            command.CommandText += " OPTION (ROBUST PLAN)";
        }
    }
}
-->
[!code-csharp[TaggedQueryCommandInterceptor](../../../samples/core/Miscellaneous/CommandInterception/TaggedQueryCommandInterceptor.cs?name=TaggedQueryCommandInterceptor)]

<span data-ttu-id="75c0e-156">Beachten Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="75c0e-156">Notice:</span></span>

* <span data-ttu-id="75c0e-157">Der Interceptor erbt von <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> , um zu vermeiden, dass jede Methode in der Interceptor Schnittstelle implementiert werden muss.</span><span class="sxs-lookup"><span data-stu-id="75c0e-157">The interceptor inherits from <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> to avoid having to implement every method in the interceptor interface.</span></span>
* <span data-ttu-id="75c0e-158">Der Interceptor implementiert sowohl Sync-als auch Async-Methoden.</span><span class="sxs-lookup"><span data-stu-id="75c0e-158">The interceptor implements both sync and async methods.</span></span> <span data-ttu-id="75c0e-159">Dadurch wird sichergestellt, dass derselbe Abfrage Hinweis auf Synchronisierungs-und asynchrone Abfragen angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="75c0e-159">This ensures that the same query hint is applied to sync and async queries.</span></span>
* <span data-ttu-id="75c0e-160">Der Interceptor implementiert die `Executing` Methoden, die von EF Core mit dem generierten SQL aufgerufen werden, _bevor_ es an die Datenbank gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="75c0e-160">The interceptor implements the `Executing` methods which are called by EF Core with the generated SQL _before_ it is sent to the database.</span></span> <span data-ttu-id="75c0e-161">Vergleichen Sie dies mit den `Executed` Methoden, die aufgerufen werden, nachdem der Daten Bank Aufruf zurückgegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="75c0e-161">Contrast this with the `Executed` methods, which are called after the database call has returned.</span></span>

<span data-ttu-id="75c0e-162">Wenn Sie den Code in diesem Beispiel ausführen, wird Folgendes generiert, wenn eine Abfrage gekennzeichnet ist:</span><span class="sxs-lookup"><span data-stu-id="75c0e-162">Running the code in this example generates the following when a query is tagged:</span></span>

```sql
-- Use hint: robust plan

SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b] OPTION (ROBUST PLAN)
```

<span data-ttu-id="75c0e-163">Wenn eine Abfrage nicht markiert ist, wird Sie dagegen unverändert an die Datenbank gesendet:</span><span class="sxs-lookup"><span data-stu-id="75c0e-163">On the other hand, when a query is not tagged, then it is sent to the database unmodified:</span></span>

```sql
SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
```

### <a name="example-connection-interception-for-sql-azure-authentication-using-add"></a><span data-ttu-id="75c0e-164">Beispiel: Verbindungs Abfang für SQL Azure Authentifizierung mit hinzufügen</span><span class="sxs-lookup"><span data-stu-id="75c0e-164">Example: Connection interception for SQL Azure authentication using ADD</span></span>

> [!TIP]
> <span data-ttu-id="75c0e-165">Sie können [das Beispiel für den Verbindungs Interceptor](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/ConnectionInterception) von GitHub herunterladen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-165">You can [download the connection interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/ConnectionInterception) from GitHub.</span></span>

<span data-ttu-id="75c0e-166">Ein <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> kann verwendet werden, um das zu bearbeiten, <xref:System.Data.Common.DbConnection> bevor es zum Herstellen einer Verbindung mit der Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="75c0e-166">An <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> can be used to manipulate the <xref:System.Data.Common.DbConnection> before it is used to connect to the database.</span></span> <span data-ttu-id="75c0e-167">Dies kann verwendet werden, um ein Azure Active Directory (AAD)-Zugriffs Token abzurufen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-167">This can be used to obtain an Azure Active Directory (AAD) access token.</span></span> <span data-ttu-id="75c0e-168">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="75c0e-168">For example:</span></span>

<!--
public class AadAuthenticationInterceptor : DbConnectionInterceptor
{
    public override InterceptionResult ConnectionOpening(
        DbConnection connection,
        ConnectionEventData eventData,
        InterceptionResult result)
        => throw new InvalidOperationException("Open connections asynchronously when using AAD authentication.");

    public override async ValueTask<InterceptionResult> ConnectionOpeningAsync(
        DbConnection connection,
        ConnectionEventData eventData,
        InterceptionResult result,
        CancellationToken cancellationToken = default)
    {
        var sqlConnection = (SqlConnection)connection;

        var provider = new AzureServiceTokenProvider();
        // Note: in some situations the access token may not be cached automatically the Azure Token Provider.
        // Depending on the kind of token requested, you may need to implement your own caching here.
        sqlConnection.AccessToken = await provider.GetAccessTokenAsync("https://database.windows.net/", null, cancellationToken);

        return result;
    }
}
-->
[!code-csharp[AadAuthenticationInterceptor](../../../samples/core/Miscellaneous/ConnectionInterception/AadAuthenticationInterceptor.cs?name=AadAuthenticationInterceptor)]

> [!TIP]
> <span data-ttu-id="75c0e-169">[Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) unterstützt jetzt die Aad-Authentifizierung über die Verbindungs Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="75c0e-169">[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) now supports AAD authentication via connection string.</span></span> <span data-ttu-id="75c0e-170">Weitere Informationen finden Sie unter <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod>.</span><span class="sxs-lookup"><span data-stu-id="75c0e-170">See <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod> for more information.</span></span>

> [!WARNING]
> <span data-ttu-id="75c0e-171">Beachten Sie, dass der Interceptor auslöst, wenn zum Öffnen der Verbindung ein Synchronisierungs Versuch erfolgt.</span><span class="sxs-lookup"><span data-stu-id="75c0e-171">Notice that the interceptor throws if a sync call is made to open the connection.</span></span> <span data-ttu-id="75c0e-172">Dies liegt daran, dass keine nicht Async-Methode zum Abrufen des Zugriffs Tokens vorhanden ist und es [keine universelle und einfache Möglichkeit gibt, eine asynchrone Methode von einem nicht asynchronen Kontext aus aufzurufen, ohne einen Deadlock zu riskieren](https://devblogs.microsoft.com/dotnet/configureawait-faq/).</span><span class="sxs-lookup"><span data-stu-id="75c0e-172">This is because there is no non-async method to obtain the access token and there is [no universal and simple way to call an async method from non-async context without risking deadlock](https://devblogs.microsoft.com/dotnet/configureawait-faq/).</span></span>

> [!WARNING]
> <span data-ttu-id="75c0e-173">in einigen Situationen wird das Zugriffs Token möglicherweise nicht automatisch vom Azure-Tokenanbieter zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="75c0e-173">in some situations the access token may not be cached automatically the Azure Token Provider.</span></span> <span data-ttu-id="75c0e-174">Abhängig von der Art des angeforderten Tokens müssen Sie hier möglicherweise Ihr eigenes Caching implementieren.</span><span class="sxs-lookup"><span data-stu-id="75c0e-174">Depending on the kind of token requested, you may need to implement your own caching here.</span></span>

### <a name="example-advanced-command-interception-for-caching"></a><span data-ttu-id="75c0e-175">Beispiel: Erweiterte Befehls Abfang Funktion für Caching</span><span class="sxs-lookup"><span data-stu-id="75c0e-175">Example: Advanced command interception for caching</span></span>

> [!TIP]
> <span data-ttu-id="75c0e-176">Sie können [das Beispiel für das erweiterte Befehls Interceptor](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) von GitHub herunterladen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-176">You can [download the advanced command interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) from GitHub.</span></span>

<span data-ttu-id="75c0e-177">EF Core Interceptors können folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="75c0e-177">EF Core interceptors can:</span></span>

* <span data-ttu-id="75c0e-178">EF Core, die Ausführung des abzufangenden Vorgangs zu unterdrücken</span><span class="sxs-lookup"><span data-stu-id="75c0e-178">Tell EF Core to suppress executing the operation being intercepted</span></span>
* <span data-ttu-id="75c0e-179">Ändern Sie das Ergebnis des Vorgangs, der zurückgemeldet wird, in EF Core</span><span class="sxs-lookup"><span data-stu-id="75c0e-179">Change the result of the operation reported back to EF Core</span></span>

<span data-ttu-id="75c0e-180">Dieses Beispiel zeigt einen Interceptor, der diese Funktionen verwendet, um sich wie ein primitiver Cache auf zweiter Ebene zu Verhalten.</span><span class="sxs-lookup"><span data-stu-id="75c0e-180">This example shows an interceptor that uses these features to behave like a primitive second-level cache.</span></span> <span data-ttu-id="75c0e-181">Zwischengespeicherte Abfrageergebnisse werden für eine bestimmte Abfrage zurückgegeben, um einen datenbankroundtrip zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="75c0e-181">Cached query results are returned for a specific query, avoiding a database roundtrip.</span></span>

> [!WARNING]
> <span data-ttu-id="75c0e-182">Achten Sie darauf, dass Sie das EF Core Standardverhalten auf diese Weise ändern.</span><span class="sxs-lookup"><span data-stu-id="75c0e-182">Take care when changing the EF Core default behavior in this way.</span></span> <span data-ttu-id="75c0e-183">EF Core Verhalten sich möglicherweise auf unerwartete Weise, wenn ein nicht normales Ergebnis angezeigt wird, das nicht ordnungsgemäß verarbeitet werden kann.</span><span class="sxs-lookup"><span data-stu-id="75c0e-183">EF Core may behave in unexpected ways if it gets an abnormal result that it cannot process correctly.</span></span> <span data-ttu-id="75c0e-184">In diesem Beispiel werden auch Interceptor Konzepte veranschaulicht. Es ist nicht als Vorlage für eine robuste Cache Implementierung auf zweiter Ebene vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-184">Also, this example demonstrates interceptor concepts; it is not intended as a template for a robust second-level cache implementation.</span></span>

<span data-ttu-id="75c0e-185">In diesem Beispiel führt die Anwendung häufig eine Abfrage aus, um die aktuelle "tägliche Nachricht" zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="75c0e-185">In this example, the application frequently executes a query to obtain the most recent "daily message":</span></span>

<!--
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
-->
[!code-csharp[GetDailyMessage](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=GetDailyMessage)]

<span data-ttu-id="75c0e-186">Diese Abfrage ist so [gekennzeichnet](xref:core/querying/tags) , dass Sie problemlos im Interceptor erkannt werden kann.</span><span class="sxs-lookup"><span data-stu-id="75c0e-186">This query is [tagged](xref:core/querying/tags) so that it can be easily detected in the interceptor.</span></span> <span data-ttu-id="75c0e-187">Die Idee besteht darin, die Datenbank nur einmal täglich auf eine neue Nachricht abzufragen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-187">The idea is to only query the database for a new message once every day.</span></span> <span data-ttu-id="75c0e-188">Zu einem anderen Zeitpunkt wird von der Anwendung ein zwischengespeichertes Ergebnis verwendet.</span><span class="sxs-lookup"><span data-stu-id="75c0e-188">At other times the application will use a cached result.</span></span> <span data-ttu-id="75c0e-189">(Im Beispiel wird eine Verzögerung von 10 Sekunden im Beispiel verwendet, um einen neuen Tag zu simulieren.)</span><span class="sxs-lookup"><span data-stu-id="75c0e-189">(The sample uses delay of 10 seconds in the sample to simulate a new day.)</span></span>

#### <a name="interceptor-state"></a><span data-ttu-id="75c0e-190">Interceptor Zustand</span><span class="sxs-lookup"><span data-stu-id="75c0e-190">Interceptor state</span></span>

<span data-ttu-id="75c0e-191">Dieser Interceptor ist Zustands behaftet: er speichert die ID und den Meldungs Text der aktuellen täglich abgefragten Nachricht sowie die Zeit, zu der die Abfrage ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="75c0e-191">This interceptor is stateful: it stores the ID and message text of the most recent daily message queried, plus the time when that query was executed.</span></span> <span data-ttu-id="75c0e-192">Aufgrund dieses Status ist auch eine [Sperre](/dotnet/csharp/language-reference/keywords/lock-statement) erforderlich, da das Zwischenspeichern erfordert, dass derselbe Interceptor von mehreren Kontext Instanzen verwendet werden muss.</span><span class="sxs-lookup"><span data-stu-id="75c0e-192">Because of this state we also need a [lock](/dotnet/csharp/language-reference/keywords/lock-statement) since the caching requires that same interceptor must be used by multiple context instances.</span></span>

<!--
    private readonly object _lock = new object();
    private int _id;
    private string _message;
    private DateTime _queriedAt;
-->
[!code-csharp[InterceptorState](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=InterceptorState)]

#### <a name="before-execution"></a><span data-ttu-id="75c0e-193">Vor der Ausführung</span><span class="sxs-lookup"><span data-stu-id="75c0e-193">Before execution</span></span>

<span data-ttu-id="75c0e-194">In der- `Executing` Methode (d. h. vor dem Erstellen eines Daten Bank Aufrufes) erkennt der Interceptor die markierte Abfrage und überprüft dann, ob ein zwischengespeichertes Ergebnis vorliegt.</span><span class="sxs-lookup"><span data-stu-id="75c0e-194">In the `Executing` method (i.e. before making a database call), the interceptor detects the tagged query and then checks if there is a cached result.</span></span> <span data-ttu-id="75c0e-195">Wenn ein solches Ergebnis gefunden wird, wird die Abfrage unterdrückt, und stattdessen werden zwischengespeicherte Ergebnisse verwendet.</span><span class="sxs-lookup"><span data-stu-id="75c0e-195">If such a result is found, then the query is suppressed and cached results are used instead.</span></span>

<!--
    public override ValueTask<InterceptionResult<DbDataReader>> ReaderExecutingAsync(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result,
        CancellationToken cancellationToken = default)
    {
        if (command.CommandText.StartsWith("-- Get_Daily_Message", StringComparison.Ordinal))
        {
            lock (_lock)
            {
                if (_message != null
                    && DateTime.UtcNow < _queriedAt + new TimeSpan(0, 0, 10))
                {
                    command.CommandText = "-- Get_Daily_Message: Skipping DB call; using cache.";
                    result = InterceptionResult<DbDataReader>.SuppressWithResult(new CachedDailyMessageDataReader(_id, _message));
                }
            }
        }

        return new ValueTask<InterceptionResult<DbDataReader>>(result);
    }
-->
[!code-csharp[ReaderExecutingAsync](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=ReaderExecutingAsync)]

<span data-ttu-id="75c0e-196">Beachten Sie, wie der Code aufruft <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> und einen Austausch übergibt, <xref:System.Data.Common.DbDataReader> der die zwischengespeicherten Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="75c0e-196">Notice how the code calls <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> and passes a replacement <xref:System.Data.Common.DbDataReader> containing the cached data.</span></span> <span data-ttu-id="75c0e-197">Dieses interceptionresult wird dann zurückgegeben, wodurch die Abfrage Ausführung unterdrückt wird.</span><span class="sxs-lookup"><span data-stu-id="75c0e-197">This InterceptionResult is then returned, causing suppression of query execution.</span></span> <span data-ttu-id="75c0e-198">Der Ersetzungs Reader wird stattdessen von EF Core als Ergebnisse der Abfrage verwendet.</span><span class="sxs-lookup"><span data-stu-id="75c0e-198">The replacement reader is instead used by EF Core as the results of the query.</span></span>

<span data-ttu-id="75c0e-199">Dieser Interceptor bearbeitet auch den Befehls Text.</span><span class="sxs-lookup"><span data-stu-id="75c0e-199">This interceptor also manipulates the command text.</span></span> <span data-ttu-id="75c0e-200">Diese Bearbeitung ist nicht erforderlich, verbessert jedoch die Übersichtlichkeit in Protokollmeldungen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-200">This manipulation is not required, but improves clarity in log messages.</span></span> <span data-ttu-id="75c0e-201">Der Befehls Text muss keine gültige SQL-Anweisung sein, da die Abfrage jetzt nicht mehr ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="75c0e-201">The command text does not need to be valid SQL since the query is now not going to be executed.</span></span>

#### <a name="after-execution"></a><span data-ttu-id="75c0e-202">Nach der Ausführung</span><span class="sxs-lookup"><span data-stu-id="75c0e-202">After execution</span></span>

<span data-ttu-id="75c0e-203">Wenn keine zwischengespeicherte Meldung verfügbar ist oder Sie abgelaufen ist, unterdrückt der obige Code das Ergebnis nicht.</span><span class="sxs-lookup"><span data-stu-id="75c0e-203">If no cached message is available, or if it has expired, then the code above does not suppress the result.</span></span> <span data-ttu-id="75c0e-204">Die Abfrage wird von EF Core daher wie gewohnt ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="75c0e-204">EF Core will therefore execute the query as normal.</span></span> <span data-ttu-id="75c0e-205">Nach der Ausführung wird die Methode des Interceptors zurückgegeben `Executed` .</span><span class="sxs-lookup"><span data-stu-id="75c0e-205">It will then return to the interceptor's `Executed` method after execution.</span></span> <span data-ttu-id="75c0e-206">Wenn das Ergebnis nicht bereits ein zwischen gespeicherter Reader ist, dann wird die neue Nachrichten-ID und die Zeichenfolge aus dem echten Reader entfernt und für die nächste Verwendung dieser Abfrage bereit zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="75c0e-206">At this point if the result is not already a cached reader, then the new message ID and string is exacted from the real reader and cached ready for the next use of this query.</span></span>

<!--
    public override async ValueTask<DbDataReader> ReaderExecutedAsync(
        DbCommand command,
        CommandExecutedEventData eventData,
        DbDataReader result,
        CancellationToken cancellationToken = default)
    {
        if (command.CommandText.StartsWith("-- Get_Daily_Message", StringComparison.Ordinal)
            && !(result is CachedDailyMessageDataReader))
        {
            try
            {
                await result.ReadAsync(cancellationToken);

                lock (_lock)
                {
                    _id = result.GetInt32(0);
                    _message = result.GetString(1);
                    _queriedAt = DateTime.UtcNow;
                    return new CachedDailyMessageDataReader(_id, message);
                }
            }
            finally
            {
                await result.DisposeAsync();
            }
        }

        return result;
    }
-->
[!code-csharp[ReaderExecutedAsync](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=ReaderExecutedAsync)]

#### <a name="demonstration"></a><span data-ttu-id="75c0e-207">Demo</span><span class="sxs-lookup"><span data-stu-id="75c0e-207">Demonstration</span></span>

<span data-ttu-id="75c0e-208">Das [Beispiel Caching Interceptor](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) enthält eine einfache Konsolenanwendung, die tägliche Nachrichten abfragt, um die Zwischenspeicherung zu testen:</span><span class="sxs-lookup"><span data-stu-id="75c0e-208">The [caching interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) contains a simple console application that queries for daily messages to test the caching:</span></span>

<!--
        // 1. Initialize the database with some daily messages.
        using (var context = new DailyMessageContext())
        {
            await context.Database.EnsureDeletedAsync();
            await context.Database.EnsureCreatedAsync();

            context.AddRange(
                new DailyMessage { Message = "Remember: All builds are GA; no builds are RTM." },
                new DailyMessage { Message = "Keep calm and drink tea" });

            await context.SaveChangesAsync();
        }

        // 2. Query for the most recent daily message. It will be cached for 10 seconds.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        // 3. Insert a new daily message.
        using (var context = new DailyMessageContext())
        {
            context.Add(new DailyMessage { Message = "Free beer for unicorns" });

            await context.SaveChangesAsync();
        }

        // 4. Cached message is used until cache expires.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        // 5. Pretend it's the next day.
        Thread.Sleep(10000);

        // 6. Cache is expired, so the last message will noe be queried again.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        #region GetDailyMessage
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
        #endregion
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=Main)]

<span data-ttu-id="75c0e-209">Dadurch wird die folgende Ausgabe zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="75c0e-209">This results in the following output:</span></span>

```output
info: 10/15/2020 12:32:11.801 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message

      SELECT "d"."Id", "d"."Message"
      FROM "DailyMessages" AS "d"
      ORDER BY "d"."Id" DESC
      LIMIT 1

Keep calm and drink tea

info: 10/15/2020 12:32:11.821 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[@p0='Free beer for unicorns' (Size = 22)], CommandType='Text', CommandTimeout='30']
      INSERT INTO "DailyMessages" ("Message")
      VALUES (@p0);
      SELECT "Id"
      FROM "DailyMessages"
      WHERE changes() = 1 AND "rowid" = last_insert_rowid();

info: 10/15/2020 12:32:11.826 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message: Skipping DB call; using cache.

Keep calm and drink tea

info: 10/15/2020 12:32:21.833 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message

      SELECT "d"."Id", "d"."Message"
      FROM "DailyMessages" AS "d"
      ORDER BY "d"."Id" DESC
      LIMIT 1

Free beer for unicorns
```

<span data-ttu-id="75c0e-210">Beachten Sie aus der Protokoll Ausgabe, dass die Anwendung die zwischengespeicherte Nachricht weiterhin verwendet, bis das Timeout abläuft. an diesem Punkt wird die Datenbank für jede neue Nachricht erneut abgefragt.</span><span class="sxs-lookup"><span data-stu-id="75c0e-210">Notice from the log output that the application continues to use the cached message until the timeout expires, at which point the database is queried again for any new message.</span></span>

## <a name="savechanges-interception"></a><span data-ttu-id="75c0e-211">Abfangen von SaveChanges</span><span class="sxs-lookup"><span data-stu-id="75c0e-211">SaveChanges interception</span></span>

> [!NOTE]
> <span data-ttu-id="75c0e-212">Das Abfangen von SaveChanges wurde in EF Core 5,0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="75c0e-212">SaveChanges interception was introduced in EF Core 5.0.</span></span>

> [!TIP]
> <span data-ttu-id="75c0e-213">Sie können [das Beispiel für das SaveChanges-Interceptor](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) von GitHub herunterladen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-213">You can [download the SaveChanges interceptor sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) from GitHub.</span></span>

<span data-ttu-id="75c0e-214"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> und <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> Abfang Punkte werden von der- <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor> Schnittstelle definiert.</span><span class="sxs-lookup"><span data-stu-id="75c0e-214"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> interception points are defined by the <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor> interface.</span></span> <span data-ttu-id="75c0e-215">Wie bei anderen Interceptors wird die <xref:Microsoft.EntityFrameworkCore.Diagnostics.SaveChangesInterceptor> Basisklasse ohne op-Methoden als praktische Bereitstellung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="75c0e-215">As for other interceptors, the <xref:Microsoft.EntityFrameworkCore.Diagnostics.SaveChangesInterceptor> base class with no-op methods is provided as a convenience.</span></span>

> [!TIP]
> <span data-ttu-id="75c0e-216">Interceptors sind leistungsstark.</span><span class="sxs-lookup"><span data-stu-id="75c0e-216">Interceptors are powerful.</span></span> <span data-ttu-id="75c0e-217">In vielen Fällen kann es jedoch einfacher sein, die SaveChanges-Methode außer Kraft zu setzen oder die [.net-Ereignisse für SaveChanges](xref:core/logging-events-diagnostics/events) zu verwenden, die in dbcontext verfügbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="75c0e-217">However, in many cases it may be easier to override the SaveChanges method or use the [.NET events for SaveChanges](xref:core/logging-events-diagnostics/events) exposed on DbContext.</span></span>

### <a name="example-savechanges-interception-for-auditing"></a><span data-ttu-id="75c0e-218">Beispiel: SaveChanges-Abfang Vorgänge für die Überwachung</span><span class="sxs-lookup"><span data-stu-id="75c0e-218">Example: SaveChanges interception for auditing</span></span>

<span data-ttu-id="75c0e-219">SaveChanges kann abgefangen werden, um einen unabhängigen Überwachungsdaten Satz der vorgenommenen Änderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-219">SaveChanges can be intercepted to create an independent audit record of the changes made.</span></span>

> [!NOTE]
> <span data-ttu-id="75c0e-220">Dabei handelt es sich nicht um eine robuste Überwachungslösung.</span><span class="sxs-lookup"><span data-stu-id="75c0e-220">This is not intended to be a robust auditing solution.</span></span> <span data-ttu-id="75c0e-221">Vielmehr handelt es sich um ein vereinfachtes Beispiel zur Veranschaulichung der Funktionen der Abfang Funktion.</span><span class="sxs-lookup"><span data-stu-id="75c0e-221">Rather it is a simplistic example used to demonstrate the features of interception.</span></span>

#### <a name="the-application-context"></a><span data-ttu-id="75c0e-222">Der Anwendungskontext</span><span class="sxs-lookup"><span data-stu-id="75c0e-222">The application context</span></span>

<span data-ttu-id="75c0e-223">Das [Beispiel für](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) die Überwachung verwendet einen einfachen dbcontext mit Blogs und Beiträgen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-223">The [sample for auditing](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) uses a simple DbContext with blogs and posts.</span></span>

<!--
public class BlogsContext : DbContext
{
    private readonly AuditingInterceptor _auditingInterceptor = new AuditingInterceptor("DataSource=audit.db");

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .AddInterceptors(_auditingInterceptor)
            .UseSqlite("DataSource=blogs.db");

    public DbSet<Blog> Blogs { get; set; }
}

public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }

    public Blog Blog { get; set; }
}
-->
[!code-csharp[BlogsContext](../../../samples/core/Miscellaneous/SaveChangesInterception/BlogsContext.cs?name=BlogsContext)]

<span data-ttu-id="75c0e-224">Beachten Sie, dass für jede dbcontext-Instanz eine neue Instanz des Interceptors registriert wird.</span><span class="sxs-lookup"><span data-stu-id="75c0e-224">Notice that a new instance of the interceptor is registered for each DbContext instance.</span></span> <span data-ttu-id="75c0e-225">Dies liegt daran, dass der Überwachungs Interceptor den mit der aktuellen Kontext Instanz verknüpften Zustand enthält.</span><span class="sxs-lookup"><span data-stu-id="75c0e-225">This is because the auditing interceptor contains state linked to the current context instance.</span></span>

#### <a name="the-audit-context"></a><span data-ttu-id="75c0e-226">Der Überwachungs Kontext</span><span class="sxs-lookup"><span data-stu-id="75c0e-226">The audit context</span></span>

<span data-ttu-id="75c0e-227">Das Beispiel enthält auch ein zweites dbcontext-und-Modell, das für die Überwachungs Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="75c0e-227">The sample also contains a second DbContext and model used for the auditing database.</span></span>

<!--
public class AuditContext : DbContext
{
    private readonly string _connectionString;

    public AuditContext(string connectionString)
    {
        _connectionString = connectionString;
    }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.UseSqlite(_connectionString);

    public DbSet<SaveChangesAudit> SaveChangesAudits { get; set; }
}

public class SaveChangesAudit
{
    public int Id { get; set; }
    public Guid AuditId { get; set; }
    public DateTime StartTime { get; set; }
    public DateTime EndTime { get; set; }
    public bool Succeeded { get; set; }
    public string ErrorMessage { get; set; }

    public ICollection<EntityAudit> Entities { get; } = new List<EntityAudit>();
}

public class EntityAudit
{
    public int Id { get; set; }
    public EntityState State { get; set; }
    public string AuditMessage { get; set; }

    public SaveChangesAudit SaveChangesAudit { get; set; }
}
-->
[!code-csharp[AuditContext](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditContext.cs?name=AuditContext)]

#### <a name="the-interceptor"></a><span data-ttu-id="75c0e-228">Der Interceptor</span><span class="sxs-lookup"><span data-stu-id="75c0e-228">The interceptor</span></span>

<span data-ttu-id="75c0e-229">Die allgemeine Idee für die Überwachung mit dem Interceptor lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="75c0e-229">The general idea for auditing with the interceptor is:</span></span>

* <span data-ttu-id="75c0e-230">Eine Überwachungs Meldung wird zu Beginn von SaveChanges erstellt und in die Überwachungs Datenbank geschrieben.</span><span class="sxs-lookup"><span data-stu-id="75c0e-230">An audit message is created at the beginning of SaveChanges and is written to the auditing database</span></span>
* <span data-ttu-id="75c0e-231">"SaveChanges" kann fortgesetzt werden.</span><span class="sxs-lookup"><span data-stu-id="75c0e-231">SaveChanges is allowed to continue</span></span>
* <span data-ttu-id="75c0e-232">Wenn SaveChanges erfolgreich ist, wird die Überwachungs Meldung aktualisiert, um die erfolgreiche Ausführung anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-232">If SaveChanges succeeds, then the audit message is updated to indicate success</span></span>
* <span data-ttu-id="75c0e-233">Wenn SaveChanges fehlschlägt, wird die Überwachungs Meldung aktualisiert, um den Fehler anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-233">If SaveChanges fails, then the audit message is updated to indicate the failure</span></span>

<span data-ttu-id="75c0e-234">Die erste Phase wird behandelt, bevor Änderungen mithilfe von außer Kraft setzungen von und an die Datenbank gesendet werden <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChanges%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChangesAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="75c0e-234">The first stage is handled before any changes are sent to the database using overrides of <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChanges%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChangesAsync%2A?displayProperty=nameWithType>.</span></span>

<!--
    public async ValueTask<InterceptionResult<int>> SavingChangesAsync(
        DbContextEventData eventData,
        InterceptionResult<int> result,
        CancellationToken cancellationToken = default)
    {
        _audit = CreateAudit(eventData.Context);

        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Add(_audit);
            await auditContext.SaveChangesAsync();
        }

        return result;
    }

    public InterceptionResult<int> SavingChanges(
        DbContextEventData eventData,
        InterceptionResult<int> result)
    {
        _audit = CreateAudit(eventData.Context);

        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Add(_audit);
            auditContext.SaveChanges();
        }

        return result;
    }
-->
[!code-csharp[SavingChanges](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SavingChanges)]

<span data-ttu-id="75c0e-235">Durch Überschreiben von Synchronisierungs-und asynchronen Methoden wird sichergestellt, dass die Überwachung unabhängig davon erfolgt, ob `SaveChanges` oder `SaveChangesAsync` aufgerufen werden</span><span class="sxs-lookup"><span data-stu-id="75c0e-235">Overriding both sync and async methods ensures that auditing will happen regardless of whether `SaveChanges` or `SaveChangesAsync` are called.</span></span> <span data-ttu-id="75c0e-236">Beachten Sie auch, dass die asynchrone Überladung selbst eine nicht blockierende asynchrone e/a für die Überwachungs Datenbank ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="75c0e-236">Notice also that the async overload is itself able to perform non-blocking async I/O to the auditing database.</span></span> <span data-ttu-id="75c0e-237">Möglicherweise möchten Sie die Synchronisierungsmethode auslösen, `SavingChanges` um sicherzustellen, dass alle Datenbank-e/a Async ist.</span><span class="sxs-lookup"><span data-stu-id="75c0e-237">You may wish to throw from the sync `SavingChanges` method to ensure that all database I/O is async.</span></span> <span data-ttu-id="75c0e-238">Dies erfordert, dass die Anwendung immer `SaveChangesAsync` und nie aufruft `SaveChanges` .</span><span class="sxs-lookup"><span data-stu-id="75c0e-238">This then requires that the application always calls `SaveChangesAsync` and never `SaveChanges`.</span></span>

#### <a name="the-audit-message"></a><span data-ttu-id="75c0e-239">Die Überwachungs Meldung</span><span class="sxs-lookup"><span data-stu-id="75c0e-239">The audit message</span></span>

<span data-ttu-id="75c0e-240">Jede Interceptor Methode verfügt über einen- `eventData` Parameter, der Kontextinformationen über das abzufangene Ereignis bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="75c0e-240">Every interceptor method has an `eventData` parameter providing contextual information about the event being intercepted.</span></span> <span data-ttu-id="75c0e-241">In diesem Fall ist der aktuelle Anwendungs-dbcontext in den Ereignisdaten enthalten, die dann zum Erstellen einer Überwachungs Nachricht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="75c0e-241">In this case the current application DbContext is included in the event data, which is then used to create an audit message.</span></span>

<!--
    private static SaveChangesAudit CreateAudit(DbContext context)
    {
        context.ChangeTracker.DetectChanges();

        var audit = new SaveChangesAudit
        {
            AuditId = Guid.NewGuid(),
            StartTime = DateTime.UtcNow
        };

        foreach (var entry in context.ChangeTracker.Entries())
        {
            var auditMessage = entry.State switch
            {
                EntityState.Deleted => CreateDeletedMessage(entry),
                EntityState.Modified => CreateModifiedMessage(entry),
                EntityState.Added => CreateAddedMessage(entry),
                _ => null
            };

            if (auditMessage != null)
            {
                audit.Entities.Add(new EntityAudit { State = entry.State, AuditMessage = auditMessage });
            }
        }

        return audit;

        string CreateAddedMessage(EntityEntry entry)
            => entry.Properties.Aggregate(
                $"Inserting {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");

        string CreateModifiedMessage(EntityEntry entry)
            => entry.Properties.Where(property => property.IsModified || property.Metadata.IsPrimaryKey()).Aggregate(
                $"Updating {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");

        string CreateDeletedMessage(EntityEntry entry)
            => entry.Properties.Where(property => property.Metadata.IsPrimaryKey()).Aggregate(
                $"Deleting {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");
    }
-->
[!code-csharp[CreateAudit](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=CreateAudit)]

<span data-ttu-id="75c0e-242">Das Ergebnis ist eine `SaveChangesAudit` Entität mit einer Auflistung von `EntityAudit` Entitäten, eine für jede INSERT-, Update-oder DELETE-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="75c0e-242">The result is a `SaveChangesAudit` entity with a collection of `EntityAudit` entities, one for each insert, update, or delete.</span></span> <span data-ttu-id="75c0e-243">Der Interceptor fügt diese Entitäten dann in die Überwachungs Datenbank ein.</span><span class="sxs-lookup"><span data-stu-id="75c0e-243">The interceptor then inserts these entities into the audit database.</span></span>

> [!TIP]
> <span data-ttu-id="75c0e-244">ToString wird in jeder EF Core Ereignisdaten Klasse überschrieben, um die entsprechende Protokollmeldung für das Ereignis zu generieren.</span><span class="sxs-lookup"><span data-stu-id="75c0e-244">ToString is overridden in every EF Core event data class to generate the equivalent log message for the event.</span></span> <span data-ttu-id="75c0e-245">Durch Aufrufen von wird z. b. `ContextInitializedEventData.ToString` "Entity Framework Core 5.0.0" blogscontext "mithilfe des Anbieters" Microsoft. entityframeworkcore. sqlite "mit den Optionen" None "initialisiert.</span><span class="sxs-lookup"><span data-stu-id="75c0e-245">For example, calling `ContextInitializedEventData.ToString` generates "Entity Framework Core 5.0.0 initialized 'BlogsContext' using provider 'Microsoft.EntityFrameworkCore.Sqlite' with options: None".</span></span>

#### <a name="detecting-success"></a><span data-ttu-id="75c0e-246">Erkennen von Erfolg</span><span class="sxs-lookup"><span data-stu-id="75c0e-246">Detecting success</span></span>

<span data-ttu-id="75c0e-247">Die Audit-Entität wird auf dem Interceptor gespeichert, sodass wieder auf Sie zugegriffen werden kann, sobald SaveChanges entweder erfolgreich ist oder fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="75c0e-247">The audit entity is stored on the interceptor so that it can be accessed again once SaveChanges either succeeds or fails.</span></span> <span data-ttu-id="75c0e-248">Für Erfolg <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChanges%2A?displayProperty=nameWithType> wird oder <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChangesAsync%2A?displayProperty=nameWithType> aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="75c0e-248">For success, <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChanges%2A?displayProperty=nameWithType> or <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChangesAsync%2A?displayProperty=nameWithType> is called.</span></span>

<!--
    public int SavedChanges(SaveChangesCompletedEventData eventData, int result)
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = true;
            _audit.EndTime = DateTime.UtcNow;

            auditContext.SaveChanges();
        }

        return result;
    }

    public async ValueTask<int> SavedChangesAsync(
        SaveChangesCompletedEventData eventData,
        int result,
        CancellationToken cancellationToken = new CancellationToken())
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = true;
            _audit.EndTime = DateTime.UtcNow;

            await auditContext.SaveChangesAsync(cancellationToken);
        }

        return result;
    }
-->
[!code-csharp[SavedChanges](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SavedChanges)]

<span data-ttu-id="75c0e-249">Die Audit-Entität wird an den Überwachungs Kontext angefügt, da Sie bereits in der Datenbank vorhanden ist und aktualisiert werden muss.</span><span class="sxs-lookup"><span data-stu-id="75c0e-249">The audit entity is attached to the audit context, since it already exists in the database and needs to be updated.</span></span> <span data-ttu-id="75c0e-250">Anschließend legen wir `Succeeded` und fest `EndTime` , wodurch diese Eigenschaften als geändert markiert werden, sodass SaveChanges ein Update an die Überwachungs Datenbank sendet.</span><span class="sxs-lookup"><span data-stu-id="75c0e-250">We then set `Succeeded` and `EndTime`, which marks these properties as modified so SaveChanges will send an update to the audit database.</span></span>

#### <a name="detecting-failure"></a><span data-ttu-id="75c0e-251">Erkennen von Fehlern</span><span class="sxs-lookup"><span data-stu-id="75c0e-251">Detecting failure</span></span>

<span data-ttu-id="75c0e-252">Fehler werden auf die gleiche Weise behandelt wie Erfolg, aber in der- <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailed%2A?displayProperty=nameWithType> Methode oder der- <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailedAsync%2A?displayProperty=nameWithType> Methode.</span><span class="sxs-lookup"><span data-stu-id="75c0e-252">Failure is handled in much the same way as success, but in the <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailed%2A?displayProperty=nameWithType> or <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailedAsync%2A?displayProperty=nameWithType> method.</span></span> <span data-ttu-id="75c0e-253">Die Ereignisdaten enthalten die ausgelöste Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="75c0e-253">The event data contains the exception that was thrown.</span></span>

<!--
    public void SaveChangesFailed(DbContextErrorEventData eventData)
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = false;
            _audit.EndTime = DateTime.UtcNow;
            _audit.ErrorMessage = eventData.Exception.Message;

            auditContext.SaveChanges();
        }
    }

    public async Task SaveChangesFailedAsync(
        DbContextErrorEventData eventData,
        CancellationToken cancellationToken = new CancellationToken())
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = false;
            _audit.EndTime = DateTime.UtcNow;
            _audit.ErrorMessage = eventData.Exception.InnerException?.Message;

            await auditContext.SaveChangesAsync(cancellationToken);
        }
    }
-->
[!code-csharp[SaveChangesFailed](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SaveChangesFailed)]

#### <a name="demonstration"></a><span data-ttu-id="75c0e-254">Demo</span><span class="sxs-lookup"><span data-stu-id="75c0e-254">Demonstration</span></span>

<span data-ttu-id="75c0e-255">Das Überwachungs [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) enthält eine einfache Konsolenanwendung, die Änderungen an der Blog Datenbank vornimmt und dann die erstellte Überwachung anzeigt.</span><span class="sxs-lookup"><span data-stu-id="75c0e-255">The [auditing sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) contains a simple console application that makes changes to the blogging database and then shows the auditing that was created.</span></span>

<!--
        // Insert, update, and delete some entities

        using (var context = new BlogsContext())
        {
            context.Add(
                new Blog
                {
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Title = "EF Core 3.1!" },
                        new Post { Title = "EF Core 5.0!" }
                    }
                });

            await context.SaveChangesAsync();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Title = "EF Core 6.0!" });

            context.SaveChanges();
        }

        // Do an insert that will fail

        using (var context = new BlogsContext())
        {
            try
            {
                context.Add(new Post { Id = 3, Title = "EF Core 3.1!" });

                await context.SaveChangesAsync();
            }
            catch (DbUpdateException)
            {
            }
        }

        // Look at the audit trail

        using (var context = new AuditContext("DataSource=audit.db"))
        {
            foreach (var audit in context.SaveChangesAudits.Include(e => e.Entities).ToList())
            {
                Console.WriteLine(
                    $"Audit {audit.AuditId} from {audit.StartTime} to {audit.EndTime} was{(audit.Succeeded ? "" : " not")} successful.");

                foreach (var entity in audit.Entities)
                {
                    Console.WriteLine($"  {entity.AuditMessage}");
                }

                if (!audit.Succeeded)
                {
                    Console.WriteLine($"  Error: {audit.ErrorMessage}");
                }
            }
        }
-->
[!code-csharp[Program](../../../samples/core/Miscellaneous/SaveChangesInterception/Program.cs?name=Program)]

<span data-ttu-id="75c0e-256">Das Ergebnis zeigt den Inhalt der Überwachungs Datenbank:</span><span class="sxs-lookup"><span data-stu-id="75c0e-256">The result shows the contents of the auditing database:</span></span>

```output
Audit 52e94327-1767-4046-a3ca-4c6b1eecbca6 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was successful.
  Inserting Blog with Id: '-2147482647' Name: 'EF Blog'
  Inserting Post with Id: '-2147482647' BlogId: '-2147482647' Title: 'EF Core 3.1!'
  Inserting Post with Id: '-2147482646' BlogId: '-2147482647' Title: 'EF Core 5.0!'
Audit 8450f57a-5030-4211-a534-eb66b8da7040 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was successful.
  Inserting Post with Id: '-2147482645' BlogId: '1' Title: 'EF Core 6.0!'
  Updating Blog with Id: '1' Name: 'EF Core Blog'
  Deleting Post with Id: '1'
Audit 201fef4d-66a7-43ad-b9b6-b57e9d3f37b3 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was not successful.
  Inserting Post with Id: '3' BlogId: '' Title: 'EF Core 3.1!'
  Error: SQLite Error 19: 'UNIQUE constraint failed: Post.Id'.
```
