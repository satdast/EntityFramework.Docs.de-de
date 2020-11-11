---
title: 'Konfigurieren einer DbContext-Instanz: EF Core'
description: Strategien zum Konfigurieren von DbContext-Instanzen mit Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/dbcontext-configuration/index
ms.openlocfilehash: 179cb1e4ff7a433c13677ec3f1e457de96004489
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431122"
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="43ced-103">Konfigurieren einer DbContext-Instanz</span><span class="sxs-lookup"><span data-stu-id="43ced-103">Configuring a DbContext</span></span>

<span data-ttu-id="43ced-104">In diesem Artikel werden grundlegende Muster für die Konfiguration einer `DbContext`-Instanz über `DbContextOptions` erläutert, um mithilfe eines bestimmten EF Core-Anbieters und optionalem Verhalten eine Verbindung mit einer Datenbank herzustellen.</span><span class="sxs-lookup"><span data-stu-id="43ced-104">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="43ced-105">DbContext-Konfiguration zur Entwurfszeit</span><span class="sxs-lookup"><span data-stu-id="43ced-105">Design-time DbContext configuration</span></span>

<span data-ttu-id="43ced-106">EF Core-Entwurfszeittools (z. B. [Migrationen](xref:core/managing-schemas/migrations/index)) müssen in der Lage sein, eine funktionierende Instanz eines `DbContext`-Typs zu ermitteln und zu erstellen, um Details zu den Entitätstypen der Anwendung und deren Zuordnung zu einem Datenbankschema zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="43ced-106">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="43ced-107">Dieser Prozess kann automatisch ausgeführt werden, solange das Tool die `DbContext`-Instanz problemlos so erstellen kann, dass sie ähnlich konfiguriert wird, wie sie zur Laufzeit konfiguriert würde.</span><span class="sxs-lookup"><span data-stu-id="43ced-107">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="43ced-108">Obwohl jedes Muster, das die erforderlichen Konfigurationsinformationen für die `DbContext`-Instanz bereitstellt, zur Laufzeit funktionieren kann, können Tools, die die Verwendung einer `DbContext`-Instanz zur Entwurfszeit erfordern, nur mit einer begrenzten Anzahl von Mustern funktionieren.</span><span class="sxs-lookup"><span data-stu-id="43ced-108">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="43ced-109">Diese werden im Abschnitt [Kontexterstellung zur Entwurfszeit](xref:core/cli/dbcontext-creation) ausführlicher behandelt.</span><span class="sxs-lookup"><span data-stu-id="43ced-109">These are covered in more detail in the [Design-Time Context Creation](xref:core/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="43ced-110">Konfigurieren von DbContextOptions</span><span class="sxs-lookup"><span data-stu-id="43ced-110">Configuring DbContextOptions</span></span>

<span data-ttu-id="43ced-111">`DbContext` muss über eine Instanz von `DbContextOptions` verfügen, um Aufgaben ausführen zu können.</span><span class="sxs-lookup"><span data-stu-id="43ced-111">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="43ced-112">Die `DbContextOptions`-Instanz enthält Konfigurationsinformationen wie die folgenden:</span><span class="sxs-lookup"><span data-stu-id="43ced-112">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="43ced-113">Den zu verwendenden Datenbankanbieter, der in der Regel durch Aufrufen einer Methode wie `UseSqlServer` oder `UseSqlite` ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="43ced-113">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`.</span></span> <span data-ttu-id="43ced-114">Diese Erweiterungsmethoden erfordern das entsprechende Anbieterpaket, z. B. `Microsoft.EntityFrameworkCore.SqlServer` oder `Microsoft.EntityFrameworkCore.Sqlite`.</span><span class="sxs-lookup"><span data-stu-id="43ced-114">These extension methods require the corresponding provider package, such as `Microsoft.EntityFrameworkCore.SqlServer` or `Microsoft.EntityFrameworkCore.Sqlite`.</span></span> <span data-ttu-id="43ced-115">Die Methoden werden im `Microsoft.EntityFrameworkCore`-Namespace definiert.</span><span class="sxs-lookup"><span data-stu-id="43ced-115">The methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span>
- <span data-ttu-id="43ced-116">Alle erforderlichen Verbindungszeichenfolgen oder Bezeichner der Datenbankinstanz, die in der Regel als Argument an die oben genannte Anbieterauswahlmethode übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="43ced-116">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="43ced-117">Beliebige optionale Verhaltenselektoren auf Anbieterebene, die in der Regel auch innerhalb des Aufrufs der Anbieterauswahlmethode verkettet sind.</span><span class="sxs-lookup"><span data-stu-id="43ced-117">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="43ced-118">Alle allgemeinen EF Core-Verhaltenselektoren, die normalerweise nach oder vor der Anbieterauswahlmethode verkettet sind.</span><span class="sxs-lookup"><span data-stu-id="43ced-118">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="43ced-119">Im folgenden Beispiel wird `DbContextOptions` für Folgendes konfiguriert: die Verwendung des SQL Server-Anbieters, eine Verbindung, die in der `connectionString`-Variablen enthalten ist, ein Befehlstimeout auf Anbieterebene und einen EF Core-Verhaltenselektor, der alle Abfragen, die in `DbContext` ausgeführt werden, standardmäßig als [nicht nachverfolgend](xref:core/querying/tracking#no-tracking-queries) definiert:</span><span class="sxs-lookup"><span data-stu-id="43ced-119">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

```csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]
> <span data-ttu-id="43ced-120">Anbieterauswahlmethoden und andere Verhaltensauswahlmethoden, die oben genannt werden, sind Erweiterungsmethoden für `DbContextOptions` oder anbieterspezifische Optionsklassen.</span><span class="sxs-lookup"><span data-stu-id="43ced-120">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="43ced-121">Um Zugriff auf diese Erweiterungsmethoden zu erhalten, benötigen Sie möglicherweise einen Namespace (in der Regel `Microsoft.EntityFrameworkCore`) im Gültigkeitsbereich, und fügen Sie zusätzliche Paketabhängigkeiten in das Projekt ein.</span><span class="sxs-lookup"><span data-stu-id="43ced-121">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="43ced-122">`DbContextOptions` kann für `DbContext` bereitgestellt werden, indem die `OnConfiguring`-Methode überschrieben oder extern ein Konstruktorargument verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="43ced-122">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="43ced-123">Wenn beides Möglichkeiten verwendet werden, wird `OnConfiguring` zuletzt angewendet und kann die für das Konstruktorargument bereitgestellten Optionen überschreiben.</span><span class="sxs-lookup"><span data-stu-id="43ced-123">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="43ced-124">Konstruktorargument</span><span class="sxs-lookup"><span data-stu-id="43ced-124">Constructor argument</span></span>

<span data-ttu-id="43ced-125">Ihr Konstruktor kann `DbContextOptions` einfach wie folgt akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="43ced-125">Your constructor can simply accept a `DbContextOptions` as follows:</span></span>

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
> <span data-ttu-id="43ced-126">Der Basiskonstruktor von DbContext akzeptiert auch die nicht generische Version von `DbContextOptions`, aber die Verwendung der nicht generischen Version ist für Anwendungen mit mehreren Kontexttypen nicht empfehlenswert.</span><span class="sxs-lookup"><span data-stu-id="43ced-126">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="43ced-127">Ihre Anwendung kann jetzt `DbContextOptions` übergeben, wenn ein Kontext instanziiert wird, wie im Folgenden gezeigt:</span><span class="sxs-lookup"><span data-stu-id="43ced-127">Your application can now pass the `DbContextOptions` when instantiating a context, as follows:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="43ced-128">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="43ced-128">OnConfiguring</span></span>

<span data-ttu-id="43ced-129">Sie können `DbContextOptions` auch innerhalb des Kontexts selbst initialisieren.</span><span class="sxs-lookup"><span data-stu-id="43ced-129">You can also initialize the `DbContextOptions` within the context itself.</span></span> <span data-ttu-id="43ced-130">Obwohl Sie dieses Verfahren für die Basiskonfiguration verwenden können, müssen Sie in der Regel dennoch bestimmte Konfigurationsdetails von außen (z. B. eine Datenbankverbindungszeichenfolge) abrufen.</span><span class="sxs-lookup"><span data-stu-id="43ced-130">While you can use this technique for basic configuration, you will typically still need to get certain configuration details from the outside, e.g. a database connection string.</span></span> <span data-ttu-id="43ced-131">Dies kann mit einer Konfigurations-API oder mit einer beliebigen anderen Möglichkeit erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="43ced-131">This can be done with a configuration API or any other means.</span></span>

<span data-ttu-id="43ced-132">Um `DbContextOptions` innerhalb des Kontexts zu initialisieren, überschreiben Sie die `OnConfiguring`-Methode und rufen die Methoden für den bereitgestellten `DbContextOptionsBuilder` auf:</span><span class="sxs-lookup"><span data-stu-id="43ced-132">To initialize `DbContextOptions` within the context, override the `OnConfiguring` method and call the methods on the provided `DbContextOptionsBuilder`:</span></span>

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

<span data-ttu-id="43ced-133">Eine Anwendung kann einen solchen Kontext einfach instanziieren, ohne etwas an den Konstruktor zu übergeben:</span><span class="sxs-lookup"><span data-stu-id="43ced-133">An application can simply instantiate such a context without passing anything to its constructor:</span></span>

```csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="43ced-134">Dieser Ansatz eignet sich nicht für Tests, es sei denn, die Tests zielen auf die vollständige Datenbank ab.</span><span class="sxs-lookup"><span data-stu-id="43ced-134">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="43ced-135">Verwenden von DbContext mit Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="43ced-135">Using DbContext with dependency injection</span></span>

<span data-ttu-id="43ced-136">EF Core unterstützt die Verwendung von `DbContext` mit einem Container für Abhängigkeitsinjektion.</span><span class="sxs-lookup"><span data-stu-id="43ced-136">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="43ced-137">Ihr DbContext-Typ kann dem Dienstcontainer hinzugefügt werden, indem die `AddDbContext<TContext>`-Methode verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="43ced-137">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="43ced-138">`AddDbContext<TContext>` stellt sowohl den DbContext-Typ (`TContext`) als auch den entsprechenden `DbContextOptions<TContext>` für die Injektion aus dem Dienstcontainer bereit.</span><span class="sxs-lookup"><span data-stu-id="43ced-138">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="43ced-139">Einzelheiten zur Abhängigkeitsinjektion finden Sie unter [Weitere Informationen](#more-reading) weiter unten.</span><span class="sxs-lookup"><span data-stu-id="43ced-139">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="43ced-140">Hinzufügen von `DbContext` zur Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="43ced-140">Adding the `DbContext` to dependency injection:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="43ced-141">Dies erfordert das Hinzufügen eines [Konstruktorarguments](#constructor-argument) zum DbContext-Typ, der `DbContextOptions<TContext>` akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="43ced-141">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="43ced-142">Kontextcode:</span><span class="sxs-lookup"><span data-stu-id="43ced-142">Context code:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="43ced-143">Anwendungscode (in ASP.NET Core):</span><span class="sxs-lookup"><span data-stu-id="43ced-143">Application code (in ASP.NET Core):</span></span>

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

<span data-ttu-id="43ced-144">Anwendungscode (direktes Verwenden von ServiceProvider, weniger häufig):</span><span class="sxs-lookup"><span data-stu-id="43ced-144">Application code (using ServiceProvider directly, less common):</span></span>

```csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="43ced-145">Vermeiden von DbContext-Threadingproblemen</span><span class="sxs-lookup"><span data-stu-id="43ced-145">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="43ced-146">Entity Framework Core unterstützt nicht die Ausführung mehrerer paralleler Vorgänge, die für dieselbe `DbContext`-Instanz ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="43ced-146">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="43ced-147">Dies schließt die parallele Ausführung von asynchronen Abfragen und jede explizite gleichzeitige Verwendung aus mehreren Threads ein.</span><span class="sxs-lookup"><span data-stu-id="43ced-147">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="43ced-148">Verwenden Sie daher immer sofort asynchrone `await`-Aufrufe, oder verwenden Sie separate `DbContext`-Instanzen für Vorgänge, die parallel ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="43ced-148">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="43ced-149">Wenn EF Core den Versuch erkennt, eine `DbContext`-Instanz parallel zu verwenden, wird eine `InvalidOperationException` mit einer Meldung wie der folgenden angezeigt:</span><span class="sxs-lookup"><span data-stu-id="43ced-149">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="43ced-150">Ein zweiter Vorgang wurde für diesen Kontext gestartet, bevor ein vorheriger Vorgang abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="43ced-150">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="43ced-151">Dies wird in der Regel durch verschiedene Threads verursacht, die dieselbe Instanz von DbContext verwenden, aber es ist nicht garantiert, dass Instanzmember threadsicher sind.</span><span class="sxs-lookup"><span data-stu-id="43ced-151">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="43ced-152">Wenn gleichzeitiger Zugriff nicht erkannt wird, kann dies zu nicht definiertem Verhalten, Anwendungsabstürzen und Datenbeschädigung führen.</span><span class="sxs-lookup"><span data-stu-id="43ced-152">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="43ced-153">Es gibt häufige Fehler, die versehentlich gleichzeitigen Zugriff auf dieselbe `DbContext`-Instanz verursachen können:</span><span class="sxs-lookup"><span data-stu-id="43ced-153">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a><span data-ttu-id="43ced-154">Vergessen, auf den Abschluss eines asynchronen Vorgangs zu warten, bevor ein anderer Vorgang im gleichen DbContext gestartet wird</span><span class="sxs-lookup"><span data-stu-id="43ced-154">Forgetting to await the completion of an asynchronous operation before starting any other operation on the same DbContext</span></span>

<span data-ttu-id="43ced-155">Asynchrone Methoden ermöglichen EF Core das Initiieren von Vorgängen, die auf nicht blockierende Weise auf die Datenbank zugreifen.</span><span class="sxs-lookup"><span data-stu-id="43ced-155">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="43ced-156">Wenn ein Aufrufer jedoch nicht auf den Abschluss einer dieser Methoden wartet und andere Vorgänge für `DbContext` ausführt, kann der Status von `DbContext` beschädigt sein (und ist es sehr wahrscheinlich auch).</span><span class="sxs-lookup"><span data-stu-id="43ced-156">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="43ced-157">Warten Sie immer sofort auf asynchrone EF Core-Methoden.</span><span class="sxs-lookup"><span data-stu-id="43ced-157">Always await EF Core asynchronous methods immediately.</span></span>

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a><span data-ttu-id="43ced-158">Implizites Freigeben von DbContext-Instanzen über mehrere Threads hinweg über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="43ced-158">Implicitly sharing DbContext instances across multiple threads via dependency injection</span></span>

<span data-ttu-id="43ced-159">Die [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Erweiterungsmethode registriert standardmäßig `DbContext`-Typen mit einer [begrenzten Lebensdauer](/aspnet/core/fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="43ced-159">The [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="43ced-160">Dies vermeidet in den meisten ASP.NET Core-Anwendungen Probleme durch gleichzeitigen Zugriff, weil es nur einen Thread gibt, der jede Clientanforderung zu einer bestimmten Zeit ausführt, und weil jede Anforderung einen separaten Abhängigkeitsinjektionsbereich (und damit eine separate `DbContext`-Instanz) erhält.</span><span class="sxs-lookup"><span data-stu-id="43ced-160">This is safe from concurrent access issues in most ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span> <span data-ttu-id="43ced-161">Für das Blazor Server-Hostingmodell wird eine logische Anforderung zum Verwalten der Blazor-Benutzerverbindung verwendet. Daher ist nur eine bereichsbezogene DbContext-Instanz pro Benutzerverbindung verfügbar, wenn der standardmäßige Injektionsbereich verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="43ced-161">For Blazor Server hosting model, one logical request is used for maintaining the Blazor user circuit, and thus only one scoped DbContext instance is available per user circuit if the default injection scope is used.</span></span>

<span data-ttu-id="43ced-162">Jeder Code, der explizit mehrere Threads parallel ausführt, sollte sicherstellen, dass auf `DbContext`-Instanzen niemals gleichzeitig zugegriffen wird.</span><span class="sxs-lookup"><span data-stu-id="43ced-162">Any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="43ced-163">Mithilfe von Abhängigkeitsinjektion kann dies erreicht werden, indem der Kontext als bereichsbezogen registriert wird und Bereiche (mit `IServiceScopeFactory`) für jeden Thread erstellt werden, oder indem `DbContext` als vorübergehender Wert registriert wird (mithilfe der Überladung von `AddDbContext`, die einen `ServiceLifetime`-Parameter annimmt).</span><span class="sxs-lookup"><span data-stu-id="43ced-163">Using dependency injection, this can be achieved by either registering the context as scoped, and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="43ced-164">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="43ced-164">More reading</span></span>

- <span data-ttu-id="43ced-165">Unter [Abhängigkeitsinjektion](/aspnet/core/fundamentals/dependency-injection) finden Sie weitere Informationen zur Abhängigkeitsinjektion.</span><span class="sxs-lookup"><span data-stu-id="43ced-165">Read [Dependency Injection](/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="43ced-166">Weitere Informationen finden Sie unter [Tests](xref:core/testing/index).</span><span class="sxs-lookup"><span data-stu-id="43ced-166">Read [Testing](xref:core/testing/index) for more information.</span></span>
