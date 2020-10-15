---
title: Konfigurieren eines dbcontext-EF Core
description: Strategien zum Konfigurieren von dbcontext mit Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 3afad8d220acecbb01b15bbb855b52a895e6eb66
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062021"
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="cbce4-103">Konfigurieren einer DbContext-Instanz</span><span class="sxs-lookup"><span data-stu-id="cbce4-103">Configuring a DbContext</span></span>

<span data-ttu-id="cbce4-104">In diesem Artikel werden grundlegende Muster für `DbContext` die Konfiguration eines über eine veranschaulicht `DbContextOptions` , um mithilfe eines bestimmten EF Core Anbieters und optionalem Verhalten eine Verbindung mit einer Datenbank herzustellen.</span><span class="sxs-lookup"><span data-stu-id="cbce4-104">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="cbce4-105">Dbcontext-Konfiguration zur Entwurfszeit</span><span class="sxs-lookup"><span data-stu-id="cbce4-105">Design-time DbContext configuration</span></span>

<span data-ttu-id="cbce4-106">EF Core Entwurfszeit Tools (z. b. [Migrationen](xref:core/managing-schemas/migrations/index) ) müssen in der Lage sein, eine funktionierende Instanz eines Typs zu ermitteln und zu erstellen, um `DbContext` Details zu den Entitäts Typen der Anwendung und deren Zuordnung zu einem Datenbankschema zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="cbce4-106">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="cbce4-107">Dieser Prozess kann automatisch ausgeführt werden, solange das Tool auf einfache Weise so erstellen kann `DbContext` , dass es so konfiguriert wird, wie es zur Laufzeit konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="cbce4-107">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="cbce4-108">Obwohl jedes Muster, das die erforderlichen Konfigurationsinformationen für bereitstellt `DbContext` , zur Laufzeit funktionieren kann, können Tools, die die Verwendung `DbContext` von zur Entwurfszeit erfordern, nur mit einer begrenzten Anzahl von Mustern funktionieren.</span><span class="sxs-lookup"><span data-stu-id="cbce4-108">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="cbce4-109">Diese werden im Abschnitt [Entwurfszeit-Kontext Erstellung](xref:core/miscellaneous/cli/dbcontext-creation) ausführlicher behandelt.</span><span class="sxs-lookup"><span data-stu-id="cbce4-109">These are covered in more detail in the [Design-Time Context Creation](xref:core/miscellaneous/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="cbce4-110">Konfigurieren von dbcontextoptions</span><span class="sxs-lookup"><span data-stu-id="cbce4-110">Configuring DbContextOptions</span></span>

<span data-ttu-id="cbce4-111">`DbContext` muss über eine Instanz von verfügen `DbContextOptions` , um die Arbeit auszuführen.</span><span class="sxs-lookup"><span data-stu-id="cbce4-111">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="cbce4-112">Die- `DbContextOptions` Instanz enthält Konfigurationsinformationen wie z. b.:</span><span class="sxs-lookup"><span data-stu-id="cbce4-112">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="cbce4-113">Der zu verwendende Datenbankanbieter, der in der Regel durch Aufrufen einer Methode wie oder ausgewählt wird `UseSqlServer` `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="cbce4-113">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`.</span></span> <span data-ttu-id="cbce4-114">Diese Erweiterungs Methoden erfordern das entsprechende Anbieter Paket, z `Microsoft.EntityFrameworkCore.SqlServer` . b `Microsoft.EntityFrameworkCore.Sqlite` . oder.</span><span class="sxs-lookup"><span data-stu-id="cbce4-114">These extension methods require the corresponding provider package, such as `Microsoft.EntityFrameworkCore.SqlServer` or `Microsoft.EntityFrameworkCore.Sqlite`.</span></span> <span data-ttu-id="cbce4-115">Die-Methoden werden im- `Microsoft.EntityFrameworkCore` Namespace definiert.</span><span class="sxs-lookup"><span data-stu-id="cbce4-115">The methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span>
- <span data-ttu-id="cbce4-116">Alle erforderlichen Verbindungs Zeichenfolgen oder Bezeichner der Daten Bank Instanz, die in der Regel als Argument an die oben genannte Anbieter Auswahlmethode weitergegeben werden.</span><span class="sxs-lookup"><span data-stu-id="cbce4-116">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="cbce4-117">Beliebige optionale verhaltenselektoren auf Anbieter Ebene, die in der Regel auch innerhalb des Aufrufes der Anbieter Auswahlmethode verkettet sind</span><span class="sxs-lookup"><span data-stu-id="cbce4-117">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="cbce4-118">Alle allgemeinen EF Core verhaltenselektoren, die normalerweise nach oder vor der Anbieter Auswahlmethode verkettet sind</span><span class="sxs-lookup"><span data-stu-id="cbce4-118">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="cbce4-119">Im folgenden Beispiel wird das `DbContextOptions` für die Verwendung des SQL Server Anbieters, eine in der-Variable enthaltene Verbindung, `connectionString` ein Befehls Timeout auf Anbieter Ebene und eine EF Core Verhaltens Auswahl konfiguriert, die standardmäßig alle Abfragen in der `DbContext` [nicht-Nachverfolgung](xref:core/querying/tracking#no-tracking-queries) ausführt:</span><span class="sxs-lookup"><span data-stu-id="cbce4-119">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

```csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]
> <span data-ttu-id="cbce4-120">Anbieter Auswahlmethoden und andere Verhaltens Auswahlmethoden, die oben erwähnt werden, sind Erweiterungs Methoden für `DbContextOptions` oder anbieterspezifische Options Klassen.</span><span class="sxs-lookup"><span data-stu-id="cbce4-120">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="cbce4-121">Um Zugriff auf diese Erweiterungs Methoden zu erhalten, benötigen Sie möglicherweise einen Namespace (in der Regel `Microsoft.EntityFrameworkCore` ) im Gültigkeitsbereich und fügen zusätzliche Paketabhängigkeiten in das Projekt ein.</span><span class="sxs-lookup"><span data-stu-id="cbce4-121">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="cbce4-122">`DbContextOptions`Kann für das bereitgestellt werden, `DbContext` indem die-Methode überschrieben wird, `OnConfiguring` oder extern über ein Konstruktorargument.</span><span class="sxs-lookup"><span data-stu-id="cbce4-122">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="cbce4-123">Wenn beide verwendet werden, `OnConfiguring` wird zuletzt angewendet, und die für das Konstruktorargument bereitgestellten Optionen können überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="cbce4-123">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="cbce4-124">Konstruktorargument</span><span class="sxs-lookup"><span data-stu-id="cbce4-124">Constructor argument</span></span>

<span data-ttu-id="cbce4-125">Der Konstruktor kann einfach `DbContextOptions` wie folgt akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="cbce4-125">Your constructor can simply accept a `DbContextOptions` as follows:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]
> <span data-ttu-id="cbce4-126">Der Basiskonstruktor von dbcontext akzeptiert auch die nicht generische Version von `DbContextOptions` , aber die Verwendung der nicht generischen Version wird nicht für Anwendungen mit mehreren Kontext Typen empfohlen.</span><span class="sxs-lookup"><span data-stu-id="cbce4-126">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="cbce4-127">Die Anwendung kann nun beim `DbContextOptions` Instanziieren eines Kontexts wie folgt übergeben:</span><span class="sxs-lookup"><span data-stu-id="cbce4-127">Your application can now pass the `DbContextOptions` when instantiating a context, as follows:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="cbce4-128">Onkonfiguration</span><span class="sxs-lookup"><span data-stu-id="cbce4-128">OnConfiguring</span></span>

<span data-ttu-id="cbce4-129">Sie können auch den `DbContextOptions` innerhalb des Kontexts selbst initialisieren.</span><span class="sxs-lookup"><span data-stu-id="cbce4-129">You can also initialize the `DbContextOptions` within the context itself.</span></span> <span data-ttu-id="cbce4-130">Obwohl Sie dieses Verfahren für die Basiskonfiguration verwenden können, müssen Sie in der Regel weiterhin bestimmte Konfigurationsdetails von außen (z. b. eine Daten bankverbindungs Zeichenfolge) erhalten.</span><span class="sxs-lookup"><span data-stu-id="cbce4-130">While you can use this technique for basic configuration, you will typically still need to get certain configuration details from the outside, e.g. a database connection string.</span></span> <span data-ttu-id="cbce4-131">Dies kann mit einer Konfigurations-API oder mit einer beliebigen anderen Möglichkeit erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="cbce4-131">This can be done with a configuration API or any other means.</span></span>

<span data-ttu-id="cbce4-132">Um `DbContextOptions` innerhalb des Kontexts zu initialisieren, überschreiben Sie die `OnConfiguring` -Methode, und nennen Sie die Methoden für die bereitgestellten `DbContextOptionsBuilder`</span><span class="sxs-lookup"><span data-stu-id="cbce4-132">To initialize `DbContextOptions` within the context, override the `OnConfiguring` method and call the methods on the provided `DbContextOptionsBuilder`:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=blog.db");
    }
}
```

<span data-ttu-id="cbce4-133">Eine Anwendung kann einen solchen Kontext einfach instanziieren, ohne etwas an den Konstruktor zu übergeben:</span><span class="sxs-lookup"><span data-stu-id="cbce4-133">An application can simply instantiate such a context without passing anything to its constructor:</span></span>

```csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="cbce4-134">Dieser Ansatz eignet sich nicht für Tests, es sei denn, die Tests sind auf die vollständige Datenbank ausgerichtet.</span><span class="sxs-lookup"><span data-stu-id="cbce4-134">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="cbce4-135">Verwenden von dbcontext mit Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="cbce4-135">Using DbContext with dependency injection</span></span>

<span data-ttu-id="cbce4-136">EF Core unterstützt die Verwendung `DbContext` von mit einem Container für Abhängigkeitsinjektion.</span><span class="sxs-lookup"><span data-stu-id="cbce4-136">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="cbce4-137">Der dbcontext-Typ kann dem Dienst Container mithilfe der-Methode hinzugefügt werden `AddDbContext<TContext>` .</span><span class="sxs-lookup"><span data-stu-id="cbce4-137">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="cbce4-138">`AddDbContext<TContext>` von werden sowohl der dbcontext-Typ `TContext` als auch der entsprechende `DbContextOptions<TContext>` für Injection aus dem Dienst Container verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="cbce4-138">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="cbce4-139">Weitere Informationen zur Abhängigkeitsinjektion finden Sie unten unter [Weitere](#more-reading) Informationen.</span><span class="sxs-lookup"><span data-stu-id="cbce4-139">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="cbce4-140">Hinzufügen von `DbContext` zur Abhängigkeitsinjektion:</span><span class="sxs-lookup"><span data-stu-id="cbce4-140">Adding the `DbContext` to dependency injection:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="cbce4-141">Dies erfordert das Hinzufügen eines [Konstruktorarguments](#constructor-argument) zu Ihrem dbcontext-Typ, der akzeptiert `DbContextOptions<TContext>` .</span><span class="sxs-lookup"><span data-stu-id="cbce4-141">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="cbce4-142">Kontext Code:</span><span class="sxs-lookup"><span data-stu-id="cbce4-142">Context code:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="cbce4-143">Anwendungscode (in ASP.net Core):</span><span class="sxs-lookup"><span data-stu-id="cbce4-143">Application code (in ASP.NET Core):</span></span>

```csharp
public class MyController
{
    private readonly BloggingContext _context;

    public MyController(BloggingContext context)
    {
      _context = context;
    }

    ...
}
```

<span data-ttu-id="cbce4-144">Anwendungscode (Verwenden von Service Provider direkt, weniger häufig):</span><span class="sxs-lookup"><span data-stu-id="cbce4-144">Application code (using ServiceProvider directly, less common):</span></span>

```csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="cbce4-145">Vermeiden von dbcontext-Threading Problemen</span><span class="sxs-lookup"><span data-stu-id="cbce4-145">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="cbce4-146">Entity Framework Core unterstützt nicht mehrere parallele Vorgänge, die auf derselben Instanz ausgeführt werden `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="cbce4-146">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="cbce4-147">Dies schließt die parallele Ausführung von asynchronen Abfragen und jede explizite gleichzeitige Verwendung mehrerer Threads ein.</span><span class="sxs-lookup"><span data-stu-id="cbce4-147">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="cbce4-148">Daher wird immer `await` Async sofort aufgerufen, oder es werden separate `DbContext` Instanzen für Vorgänge verwendet, die parallel ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="cbce4-148">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="cbce4-149">Wenn EF Core den Versuch erkennt, eine- `DbContext` Instanz gleichzeitig zu verwenden, wird eine `InvalidOperationException` mit einer Meldung wie die folgende angezeigt:</span><span class="sxs-lookup"><span data-stu-id="cbce4-149">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="cbce4-150">Ein zweiter Vorgang, der in diesem Kontext gestartet wurde, bevor ein vorheriger Vorgang abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="cbce4-150">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="cbce4-151">Dies wird in der Regel durch unterschiedliche Threads verursacht, die dieselbe Instanz von dbcontext verwenden, aber es ist nicht garantiert, dass Instanzmember Thread sicher sind.</span><span class="sxs-lookup"><span data-stu-id="cbce4-151">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="cbce4-152">Wenn der gleichzeitige Zugriff nicht erkannt wird, kann dies zu nicht definiertem Verhalten, Anwendungs abstürzen und Daten Beschädigung führen.</span><span class="sxs-lookup"><span data-stu-id="cbce4-152">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="cbce4-153">Es gibt häufige Fehler, die versehentlich den gleichzeitigen Zugriff auf dieselbe `DbContext` Instanz verursachen können:</span><span class="sxs-lookup"><span data-stu-id="cbce4-153">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a><span data-ttu-id="cbce4-154">Vergessen Sie nicht, auf den Abschluss eines asynchronen Vorgangs zu warten, bevor Sie einen anderen Vorgang im gleichen dbcontext starten.</span><span class="sxs-lookup"><span data-stu-id="cbce4-154">Forgetting to await the completion of an asynchronous operation before starting any other operation on the same DbContext</span></span>

<span data-ttu-id="cbce4-155">Asynchrone Methoden ermöglichen EF Core das Initiieren von Vorgängen, die auf eine nicht blockierende Weise auf die Datenbank zugreifen.</span><span class="sxs-lookup"><span data-stu-id="cbce4-155">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="cbce4-156">Wenn ein Aufrufer jedoch nicht auf den Abschluss einer dieser Methoden wartet und weiterhin andere Vorgänge auf dem ausführt `DbContext` , kann der Zustand von `DbContext` (und wahrscheinlich) beschädigt sein.</span><span class="sxs-lookup"><span data-stu-id="cbce4-156">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="cbce4-157">Asynchrone Methoden sollten immer sofort EF Core werden.</span><span class="sxs-lookup"><span data-stu-id="cbce4-157">Always await EF Core asynchronous methods immediately.</span></span>

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a><span data-ttu-id="cbce4-158">Implizites Freigeben von dbcontext-Instanzen über mehrere Threads über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="cbce4-158">Implicitly sharing DbContext instances across multiple threads via dependency injection</span></span>

<span data-ttu-id="cbce4-159">Die [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) Erweiterungsmethode registriert `DbContext` Typen standardmäßig mit einer Gültigkeits [Dauer](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) .</span><span class="sxs-lookup"><span data-stu-id="cbce4-159">The [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="cbce4-160">Dies ist vor gleichzeitigen Zugriffsproblemen in den meisten ASP.net Core Anwendungen sicher, weil nur ein Thread zu einem bestimmten Zeitpunkt jede Client Anforderung ausführt und jede Anforderung einen separaten Abhängigkeits einschleusungs Bereich (und somit eine separate `DbContext` Instanz) erhält.</span><span class="sxs-lookup"><span data-stu-id="cbce4-160">This is safe from concurrent access issues in most ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span> <span data-ttu-id="cbce4-161">Für das blazor-Server Hostingmodell wird eine logische Anforderung zum Verwalten der blazor-Benutzer Verbindung verwendet. Daher ist nur eine Bereichs bezogene dbcontext-Instanz pro Benutzer Verbindung verfügbar, wenn der standardmäßige Injektions Bereich verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cbce4-161">For Blazor Server hosting model, one logical request is used for maintaining the Blazor user circuit, and thus only one scoped DbContext instance is available per user circuit if the default injection scope is used.</span></span>

<span data-ttu-id="cbce4-162">Jeglicher Code, der explizit mehrere Threads parallel ausführt, sollte sicherstellen, dass auf `DbContext` Instanzen nicht gleichzeitig zugegriffen wird.</span><span class="sxs-lookup"><span data-stu-id="cbce4-162">Any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="cbce4-163">Mithilfe der Abhängigkeitsinjektion kann dies erreicht werden, indem der Kontext als Bereichs bezogen registriert wird und Bereiche (mit `IServiceScopeFactory` ) für jeden Thread erstellt werden, oder indem der `DbContext` als vorübergehender registriert wird (mithilfe der Überladung von `AddDbContext` , die einen- `ServiceLifetime` Parameter annimmt).</span><span class="sxs-lookup"><span data-stu-id="cbce4-163">Using dependency injection, this can be achieved by either registering the context as scoped, and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="cbce4-164">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="cbce4-164">More reading</span></span>

- <span data-ttu-id="cbce4-165">Weitere Informationen zur Verwendung von di finden Sie unter [Abhängigkeitsinjektion](/aspnet/core/fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="cbce4-165">Read [Dependency Injection](/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="cbce4-166">Weitere Informationen finden Sie unter [Testen](xref:core/miscellaneous/testing/index) .</span><span class="sxs-lookup"><span data-stu-id="cbce4-166">Read [Testing](xref:core/miscellaneous/testing/index) for more information.</span></span>
