---
title: 'DbContext-Lebensdauer, -Konfiguration und -Initialisierung: EF Core'
description: Muster für das Erstellen und Verwalten von DbContext-Instanzen mit oder ohne Abhängigkeitsinjektion
author: ajcvickers
ms.date: 11/07/2020
uid: core/dbcontext-configuration/index
ms.openlocfilehash: 10ed474df2c4c52e61083d9d671909be02cd8cef
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129030"
---
# <a name="dbcontext-lifetime-configuration-and-initialization"></a><span data-ttu-id="daeb1-103">DbContext-Lebensdauer, -Konfiguration und -Initialisierung</span><span class="sxs-lookup"><span data-stu-id="daeb1-103">DbContext Lifetime, Configuration, and Initialization</span></span>

<span data-ttu-id="daeb1-104">Dieser Artikel zeigt grundlegende Muster für die Initialisierung und Konfiguration einer <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanz.</span><span class="sxs-lookup"><span data-stu-id="daeb1-104">This article shows basic patterns for initialization and configuration of a <xref:Microsoft.EntityFrameworkCore.DbContext> instance.</span></span>

## <a name="the-dbcontext-lifetime"></a><span data-ttu-id="daeb1-105">Die DbContext-Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="daeb1-105">The DbContext lifetime</span></span>

<span data-ttu-id="daeb1-106">Die Lebensdauer eines `DbContext` beginnt mit dem Erstellen der Instanz und endet, wenn die Instanz [verworfen](/dotnet/standard/garbage-collection/unmanaged) wird.</span><span class="sxs-lookup"><span data-stu-id="daeb1-106">The lifetime of a `DbContext` begins when the instance is created and ends when the instance is [disposed](/dotnet/standard/garbage-collection/unmanaged).</span></span> <span data-ttu-id="daeb1-107">Eine `DbContext`-Instanz ist für die Verwendung in einer _einzelnen_ [Arbeitseinheit](https://www.martinfowler.com/eaaCatalog/unitOfWork.html) konzipiert.</span><span class="sxs-lookup"><span data-stu-id="daeb1-107">A `DbContext` instance is designed to be used for a _single_ [unit-of-work](https://www.martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="daeb1-108">Dies bedeutet, dass die Lebensdauer einer `DbContext`-Instanz in der Regel sehr kurz ist.</span><span class="sxs-lookup"><span data-stu-id="daeb1-108">This means that the lifetime of a `DbContext` instance is usually very short.</span></span>

> [!TIP]
> <span data-ttu-id="daeb1-109">Um Martin Fowler aus dem Link oben zu zitieren: „Eine Arbeitseinheit verfolgt alle Aufgaben nach, die Sie während einer Geschäftstransaktion ausführen, die sich auf die Datenbank auswirken können.</span><span class="sxs-lookup"><span data-stu-id="daeb1-109">To quote Martin Fowler from the link above, "A Unit of Work keeps track of everything you do during a business transaction that can affect the database.</span></span> <span data-ttu-id="daeb1-110">Wenn Sie fertig sind, findet sie alles heraus, was getan werden muss, um die Datenbank als Ergebnis Ihrer Arbeit zu verändern“.</span><span class="sxs-lookup"><span data-stu-id="daeb1-110">When you're done, it figures out everything that needs to be done to alter the database as a result of your work."</span></span>

<span data-ttu-id="daeb1-111">Eine typische Arbeitseinheit bei Verwendung von Entity Framework Core (EF Core) umfasst Folgendes:</span><span class="sxs-lookup"><span data-stu-id="daeb1-111">A typical unit-of-work when using Entity Framework Core (EF Core) involves:</span></span>

- <span data-ttu-id="daeb1-112">Erstellen einer `DbContext`-Instanz.</span><span class="sxs-lookup"><span data-stu-id="daeb1-112">Creation of a `DbContext` instance</span></span>
- <span data-ttu-id="daeb1-113">Nachverfolgen von Entitätsinstanzen durch den Kontext.</span><span class="sxs-lookup"><span data-stu-id="daeb1-113">Tracking of entity instances by the context.</span></span> <span data-ttu-id="daeb1-114">Entitäten werden folgendermaßen nachverfolgt:</span><span class="sxs-lookup"><span data-stu-id="daeb1-114">Entities become tracked by</span></span>
  - <span data-ttu-id="daeb1-115">Durch [Rückgabe von einer Abfrage](xref:core/querying/tracking)</span><span class="sxs-lookup"><span data-stu-id="daeb1-115">Being [returned from a query](xref:core/querying/tracking)</span></span>
  - <span data-ttu-id="daeb1-116">Durch [Hinzufügen oder Anfügen an den Kontext](xref:core/saving/disconnected-entities)</span><span class="sxs-lookup"><span data-stu-id="daeb1-116">Being [added or attached to the context](xref:core/saving/disconnected-entities)</span></span>
- <span data-ttu-id="daeb1-117">An den nachverfolgten Entitäten werden Änderungen vorgenommen, die für die Implementierung der Geschäftsregel erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="daeb1-117">Changes are made to the tracked entities as needed to implement the business rule</span></span>
- <span data-ttu-id="daeb1-118"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> oder <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="daeb1-118"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> is called.</span></span> <span data-ttu-id="daeb1-119">EF Core erkennt die vorgenommenen Änderungen und schreibt sie in die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="daeb1-119">EF Core detects the changes made and writes them to the database.</span></span>
- <span data-ttu-id="daeb1-120">Die `DbContext` Instanz wird verworfen.</span><span class="sxs-lookup"><span data-stu-id="daeb1-120">The `DbContext` instance is disposed</span></span>

> [!IMPORTANT]
>
> - <span data-ttu-id="daeb1-121">Es ist sehr wichtig, <xref:Microsoft.EntityFrameworkCore.DbContext> nach der Verwendung zu verwerfen.</span><span class="sxs-lookup"><span data-stu-id="daeb1-121">It is very important to dispose the <xref:Microsoft.EntityFrameworkCore.DbContext> after use.</span></span> <span data-ttu-id="daeb1-122">Dadurch wird sichergestellt, dass alle nicht verwalteten Ressourcen freigegeben werden und dass die Registrierung für alle Ereignisse oder anderen Hooks aufgehoben wird, um Speicherverluste zu verhindern, falls die Instanz weiterhin referenziert wird.</span><span class="sxs-lookup"><span data-stu-id="daeb1-122">This ensures both that any unmanaged resources are freed, and that any events or other hooks are unregistered so as to prevent memory leaks in case the instance remains referenced.</span></span>
> - <span data-ttu-id="daeb1-123">[DbContext ist **nicht threadsicher**](#avoiding-dbcontext-threading-issues).</span><span class="sxs-lookup"><span data-stu-id="daeb1-123">[DbContext is **not thread-safe**](#avoiding-dbcontext-threading-issues).</span></span> <span data-ttu-id="daeb1-124">Geben Sie keine Kontexte zwischen Threads frei.</span><span class="sxs-lookup"><span data-stu-id="daeb1-124">Do not share contexts between threads.</span></span> <span data-ttu-id="daeb1-125">Stellen Sie sicher, dass [await](/dotnet/csharp/language-reference/operators/await) für alle asynchronen Aufrufe verwendet wird, bevor Sie die Kontextinstanz weiterhin verwenden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-125">Make sure to [await](/dotnet/csharp/language-reference/operators/await) all async calls before continuing to use the context instance.</span></span>
> - <span data-ttu-id="daeb1-126">Eine <xref:System.InvalidOperationException>, die von EF Core-Code ausgelöst wird, kann den Kontext in einen nicht wiederherstellbaren Zustand versetzen.</span><span class="sxs-lookup"><span data-stu-id="daeb1-126">An <xref:System.InvalidOperationException> thrown by EF Core code can put the context into an unrecoverable state.</span></span> <span data-ttu-id="daeb1-127">Solche Ausnahmen weisen auf einen Programmfehler hin und sind nicht für Wiederherstellung konzipiert.</span><span class="sxs-lookup"><span data-stu-id="daeb1-127">Such exceptions indicate a program error and are not designed to be recovered from.</span></span>

## <a name="dbcontext-in-dependency-injection-for-aspnet-core"></a><span data-ttu-id="daeb1-128">DbContext in Abhängigkeitsinjektion für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="daeb1-128">DbContext in dependency injection for ASP.NET Core</span></span>

<span data-ttu-id="daeb1-129">In vielen Webanwendungen entspricht jede HTTP-Anforderung einer einzelnen Arbeitseinheit.</span><span class="sxs-lookup"><span data-stu-id="daeb1-129">In many web applications, each HTTP request corresponds to a single unit-of-work.</span></span> <span data-ttu-id="daeb1-130">Dies bewirkt, dass das Anbinden der Kontextlebensdauer an die Anforderung für Webanwendungen eine gute Standardeinstellung ist.</span><span class="sxs-lookup"><span data-stu-id="daeb1-130">This makes tying the context lifetime to that of the request a good default for web applications.</span></span>

<span data-ttu-id="daeb1-131">ASP.NET Core-Anwendungen werden [mithilfe von Abhängigkeitsinjektion konfiguriert](/aspnet/core/fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="daeb1-131">ASP.NET Core applications are [configured using dependency injection](/aspnet/core/fundamentals/startup).</span></span> <span data-ttu-id="daeb1-132">EF Core kann dieser Konfiguration mithilfe von <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> in der [`ConfigureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method)-Methode von `Startup.cs` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-132">EF Core can be added to this configuration using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> in the [`ConfigureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method) method of `Startup.cs`.</span></span> <span data-ttu-id="daeb1-133">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="daeb1-133">For example:</span></span>

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();

            services.AddDbContext<ApplicationDbContext>(
                options => options.UseSqlServer("name=ConnectionStrings:DefaultConnection"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Startup.cs?name=ConfigureServices)]

<span data-ttu-id="daeb1-134">In diesem Beispiel wird eine `DbContext`-Unterklasse mit dem Namen `ApplicationDbContext` als bereichsbezogener Dienst im ASP.NET Core-Anwendungsdienstanbieter registriert (auch als</span><span class="sxs-lookup"><span data-stu-id="daeb1-134">This example registers a `DbContext` subclass called `ApplicationDbContext` as a scoped service in the ASP.NET Core application service provider (a.k.a.</span></span> <span data-ttu-id="daeb1-135">Abhängigkeitsinjektionscontainer bezeichnet).</span><span class="sxs-lookup"><span data-stu-id="daeb1-135">the dependency injection container).</span></span> <span data-ttu-id="daeb1-136">Der Kontext ist so konfiguriert, dass der SQL Server-Datenbankanbieter verwendet und die Verbindungszeichenfolge aus der ASP.NET Core-Konfiguration gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="daeb1-136">The context is configured to use the SQL Server database provider and will read the connection string from ASP.NET Core configuration.</span></span> <span data-ttu-id="daeb1-137">Es spielt in der Regel keine Rolle, _wo_ in `ConfigureServices` der Aufruf von `AddDbContext` erfolgt.</span><span class="sxs-lookup"><span data-stu-id="daeb1-137">It typically does not matter _where_ in `ConfigureServices` the call to `AddDbContext` is made.</span></span>

<span data-ttu-id="daeb1-138">Die `ApplicationDbContext`-Klasse muss einen öffentlichen Konstruktor mit einem Parameter `DbContextOptions<ApplicationDbContext>` bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="daeb1-138">The `ApplicationDbContext` class must expose a public constructor with a `DbContextOptions<ApplicationDbContext>` parameter.</span></span> <span data-ttu-id="daeb1-139">Auf diese Weise wird die Kontextkonfiguration aus `AddDbContext` an `DbContext` übergeben.</span><span class="sxs-lookup"><span data-stu-id="daeb1-139">This is how context configuration from `AddDbContext` is passed to the `DbContext`.</span></span> <span data-ttu-id="daeb1-140">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="daeb1-140">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="daeb1-141">`ApplicationDbContext` kann dann mithilfe von Konstruktorinjektion in ASP.NET Core-Controllern oder anderen Diensten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-141">`ApplicationDbContext` can then be used in ASP.NET Core controllers or other services through constructor injection.</span></span> <span data-ttu-id="daeb1-142">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="daeb1-142">For example:</span></span>

<!--
    public class MyController
    {
        private readonly ApplicationDbContext _context;

        public MyController(ApplicationDbContext context)
        {
            _context = context;
        }
    }
-->
[!code-csharp[MyController](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Controllers/MyController.cs?name=MyController)]

<span data-ttu-id="daeb1-143">Das Endergebnis ist eine `ApplicationDbContext`-Instanz, die für jede Anforderung erstellt und an den Controller zum Ausführen einer Arbeitseinheit übergeben wird, bevor sie verworfen wird, wenn die Anforderung beendet wird.</span><span class="sxs-lookup"><span data-stu-id="daeb1-143">The final result is an `ApplicationDbContext` instance created for each request and passed to the controller to perform a unit-of-work before being disposed when the request ends.</span></span>

<span data-ttu-id="daeb1-144">Weitere Informationen zu den Konfigurationsoptionen finden Sie weiter unten in diesem Artikel.</span><span class="sxs-lookup"><span data-stu-id="daeb1-144">Read further in this article to learn more about configuration options.</span></span> <span data-ttu-id="daeb1-145">Weitere Informationen zur Konfiguration und zu Abhängigkeitsinjektion in ASP.NET Core finden Sie unter [App-Start in ASP.NET Core](/aspnet/core/fundamentals/startup) und [Abhängigkeitsinjektion in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="daeb1-145">In addition, see [App startup in ASP.NET Core](/aspnet/core/fundamentals/startup) and [Dependency injection in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection) for more information on configuration and dependency injection in ASP.NET Core.</span></span>

<!-- See also [Using Dependency Injection](TODO) for advanced dependency injection configuration with EF Core. -->

## <a name="simple-dbcontext-initialization-with-new"></a><span data-ttu-id="daeb1-146">Einfache DbContext-Initialisierung mit „new“</span><span class="sxs-lookup"><span data-stu-id="daeb1-146">Simple DbContext initialization with 'new'</span></span>

<span data-ttu-id="daeb1-147">`DbContext`-Instanzen können auf die normale .NET-Weise erstellt werden, z. B. mit `new` in C#.</span><span class="sxs-lookup"><span data-stu-id="daeb1-147">`DbContext` instances can be constructed in the normal .NET way, for example with `new` in C#.</span></span> <span data-ttu-id="daeb1-148">Die Konfiguration kann durch Überschreiben der `OnConfiguring`-Methode oder durch Übergeben von Optionen an den Konstruktor ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-148">Configuration can be performed by overriding the `OnConfiguring` method, or by passing options to the constructor.</span></span> <span data-ttu-id="daeb1-149">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="daeb1-149">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNew/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="daeb1-150">Mit diesem Muster ist es auch einfach, Konfiguration wie etwa eine Verbindungszeichenfolge über den `DbContext`-Konstruktor zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="daeb1-150">This pattern also makes it easy to pass configuration like the connection string via the `DbContext` constructor.</span></span> <span data-ttu-id="daeb1-151">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="daeb1-151">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        private readonly string _connectionString;

        public ApplicationDbContext(string connectionString)
        {
            _connectionString = connectionString;
        }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(_connectionString);
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNewAndArgs/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="daeb1-152">Alternativ kann `DbContextOptionsBuilder` verwendet werden, um ein `DbContextOptions`-Objekt zu erstellen, das dann an den `DbContext`-Konstruktor übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="daeb1-152">Alternately, `DbContextOptionsBuilder` can be used to create a `DbContextOptions` object that is then passed to the `DbContext` constructor.</span></span> <span data-ttu-id="daeb1-153">Dadurch kann ein `DbContext`, der für Abhängigkeitsinjektion konfiguriert ist, ebenfalls explizit erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-153">This allows a `DbContext` configured for dependency injection to also be constructed explicitly.</span></span> <span data-ttu-id="daeb1-154">Wenn Sie z. B. `ApplicationDbContext` verwenden, der für ASP.NET Core Web-Apps oben definiert ist:</span><span class="sxs-lookup"><span data-stu-id="daeb1-154">For example, when using `ApplicationDbContext` defined for ASP.NET Core web apps above:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="daeb1-155">`DbContextOptions` kann erstellt werden, und der Konstruktor kann explizit aufgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="daeb1-155">The `DbContextOptions` can be created and the constructor can be called explicitly:</span></span>

<!--
            var contextOptions = new DbContextOptionsBuilder<ApplicationDbContext>()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test")
                .Options;

            using var context = new ApplicationDbContext(contextOptions);
-->
[!code-csharp[UseNewForWebApp](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/UseNewForWebApp.cs?name=UseNewForWebApp)]

## <a name="using-a-dbcontext-factory-eg-for-blazor"></a><span data-ttu-id="daeb1-156">Verwenden einer DbContext-Factory (z. B. für Blazor)</span><span class="sxs-lookup"><span data-stu-id="daeb1-156">Using a DbContext factory (e.g. for Blazor)</span></span>

<span data-ttu-id="daeb1-157">Einige Anwendungstypen (z. B. [ASP.NET Core Blazor](/aspnet/core/blazor/)) verwenden Abhängigkeitsinjektion, erstellen aber keinen Dienstbereich, der der gewünschten `DbContext`-Lebensdauer entspricht.</span><span class="sxs-lookup"><span data-stu-id="daeb1-157">Some application types (e.g. [ASP.NET Core Blazor](/aspnet/core/blazor/)) use dependency injection but do not create a service scope that aligns with the desired `DbContext` lifetime.</span></span> <span data-ttu-id="daeb1-158">Auch wenn eine solche Ausrichtung vorhanden ist, muss die Anwendung möglicherweise mehrere Arbeitseinheiten innerhalb dieses Bereichs ausführen.</span><span class="sxs-lookup"><span data-stu-id="daeb1-158">Even where such an alignment does exist, the application may need to perform multiple units-of-work within this scope.</span></span> <span data-ttu-id="daeb1-159">Beispielsweise mehrere Arbeitseinheiten innerhalb einer einzelnen HTTP-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="daeb1-159">For example, multiple units-of-work within a single HTTP request.</span></span>

<span data-ttu-id="daeb1-160">In diesen Fällen kann <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> zum Registrieren einer Factory zum Erstellen von `DbContext`-Instanzen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-160">In these cases, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> can be used to register a factory for creation of `DbContext` instances.</span></span> <span data-ttu-id="daeb1-161">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="daeb1-161">For example:</span></span>

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContextFactory<ApplicationDbContext>(options =>
                options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/FactoryServicesExample.cs?name=ConfigureServices)]

<span data-ttu-id="daeb1-162">Die `ApplicationDbContext`-Klasse muss einen öffentlichen Konstruktor mit einem Parameter `DbContextOptions<ApplicationDbContext>` bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="daeb1-162">The `ApplicationDbContext` class must expose a public constructor with a `DbContextOptions<ApplicationDbContext>` parameter.</span></span> <span data-ttu-id="daeb1-163">Dabei handelt es sich um dasselbe Muster, das im Abschnitt „Traditionelles ASP.NET Core“ weiter oben verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="daeb1-163">This is the same pattern as used in the traditional ASP.NET Core section above.</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="daeb1-164">Die `DbContextFactory`-Factory kann dann mithilfe von Konstruktorinjektion in anderen Diensten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-164">The `DbContextFactory` factory can then be used in other services through constructor injection.</span></span> <span data-ttu-id="daeb1-165">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="daeb1-165">For example:</span></span>

<!--
        private readonly IDbContextFactory<ApplicationDbContext> _contextFactory;

        public MyController(IDbContextFactory<ApplicationDbContext> contextFactory)
        {
            _contextFactory = contextFactory;
        }
-->
[!code-csharp[Construct](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=Construct)]

<span data-ttu-id="daeb1-166">Die injizierte Factory kann dann verwendet werden, um DbContext-Instanzen im Dienstcode zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="daeb1-166">The injected factory can then be used to construct DbContext instances in the service code.</span></span> <span data-ttu-id="daeb1-167">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="daeb1-167">For example:</span></span>

<!--
        public void DoSomething()
        {
            using (var context = _contextFactory.CreateDbContext())
            {
                // ...
            }
        }
-->
[!code-csharp[DoSomething](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=DoSomething)]

<span data-ttu-id="daeb1-168">Beachten Sie, dass die `DbContext`-Instanzen, die auf diese Weise erstellt werden, **nicht** vom Dienstanbieter der Anwendung verwaltet werden, weshalb diese von der Anwendung gelöscht werden müssen.</span><span class="sxs-lookup"><span data-stu-id="daeb1-168">Notice that the `DbContext` instances created in this way are **not** managed by the application's service provider and therefore must be disposed by the application.</span></span>

<span data-ttu-id="daeb1-169">Weitere Informationen zur Verwendung von EF Core mit Blazor finden Sie unter [ASP.NET Core Blazor Server mit Entity Framework Core](/aspnet/core/blazor/blazor-server-ef-core).</span><span class="sxs-lookup"><span data-stu-id="daeb1-169">See [ASP.NET Core Blazor Server with Entity Framework Core](/aspnet/core/blazor/blazor-server-ef-core) for more information on using EF Core with Blazor.</span></span>

## <a name="dbcontextoptions"></a><span data-ttu-id="daeb1-170">DbContextOptions</span><span class="sxs-lookup"><span data-stu-id="daeb1-170">DbContextOptions</span></span>

<span data-ttu-id="daeb1-171">Der Ausgangspunkt für die gesamte `DbContext`-Konfiguration ist <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.</span><span class="sxs-lookup"><span data-stu-id="daeb1-171">The starting point for all `DbContext` configuration is <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.</span></span> <span data-ttu-id="daeb1-172">Es gibt drei Möglichkeiten, diesen Generator abzurufen:</span><span class="sxs-lookup"><span data-stu-id="daeb1-172">There are three ways to get this builder:</span></span>

- <span data-ttu-id="daeb1-173">In `AddDbContext` und verwandte Methoden</span><span class="sxs-lookup"><span data-stu-id="daeb1-173">In `AddDbContext` and related methods</span></span>
- <span data-ttu-id="daeb1-174">In `OnConfiguring`</span><span class="sxs-lookup"><span data-stu-id="daeb1-174">In `OnConfiguring`</span></span>
- <span data-ttu-id="daeb1-175">Explizit generiert mit `new`</span><span class="sxs-lookup"><span data-stu-id="daeb1-175">Constructed explicitly with `new`</span></span>

<span data-ttu-id="daeb1-176">Beispiele für jede dieser Möglichkeiten werden in den vorangehenden Abschnitten aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="daeb1-176">Examples of each of these are shown in the preceding sections.</span></span> <span data-ttu-id="daeb1-177">Dieselbe Konfiguration kann unabhängig davon angewendet werden, wie der Generator abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="daeb1-177">The same configuration can be applied regardless of where the builder comes from.</span></span> <span data-ttu-id="daeb1-178">Außerdem wird `OnConfiguring` immer unabhängig davon aufgerufen, wie der Kontext erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="daeb1-178">In addition, `OnConfiguring` is always called regardless of how the context is constructed.</span></span> <span data-ttu-id="daeb1-179">Dies bedeutet, dass `OnConfiguring` selbst dann verwendet werden kann, um zusätzliche Konfigurationen auszuführen, wenn `AddDbContext` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="daeb1-179">This means `OnConfiguring` can be used to perform additional configuration even when `AddDbContext` is being used.</span></span>

### <a name="configuring-the-database-provider"></a><span data-ttu-id="daeb1-180">Konfigurieren des Datenbankanbieters</span><span class="sxs-lookup"><span data-stu-id="daeb1-180">Configuring the database provider</span></span>

<span data-ttu-id="daeb1-181">Jede `DbContext`-Instanz muss so konfiguriert werden, dass sie nur einen Datenbankanbieter verwendet.</span><span class="sxs-lookup"><span data-stu-id="daeb1-181">Each `DbContext` instance must be configured to use one and only one database provider.</span></span> <span data-ttu-id="daeb1-182">(Verschiedene Instanzen eines `DbContext`-Untertyps können mit unterschiedlichen Datenbankanbietern verwendet werden, aber eine einzelne Instanz darf nur einen Datenbankanbieter verwenden.) Ein Datenbankanbieter wird mithilfe eines bestimmten `Use*`"-Aufrufs konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="daeb1-182">(Different instances of a `DbContext` subtype can be used with different database providers, but a single instance must only use one.) A database provider is configured using a specific `Use*`" call.</span></span> <span data-ttu-id="daeb1-183">Wenn beispielsweise der SQL Server-Datenbankanbieter verwendet werden soll:</span><span class="sxs-lookup"><span data-stu-id="daeb1-183">For example, to use the SQL Server database provider:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNew/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="daeb1-184">Diese `Use*`"-Methoden sind Erweiterungsmethoden, die vom Datenbankanbieter implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-184">These `Use*`" methods are extension methods implemented by the database provider.</span></span> <span data-ttu-id="daeb1-185">Dies bedeutet, dass das NuGet-Paket des Datenbankanbieters installiert werden muss, bevor die Erweiterungsmethode verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="daeb1-185">This means that the database provider NuGet package must be installed before the extension method can be used.</span></span>

> [!TIP]
> <span data-ttu-id="daeb1-186">EF Core-Datenbankanbieter nutzen [Erweiterungsmethoden](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods) ausgiebig.</span><span class="sxs-lookup"><span data-stu-id="daeb1-186">EF Core database providers make extensive use of [extension methods](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods).</span></span> <span data-ttu-id="daeb1-187">Wenn der Compiler angibt, dass eine Methode nicht gefunden werden kann, stellen Sie sicher, dass das NuGet-Paket des Anbieters installiert ist und dass Sie `using Microsoft.EntityFrameworkCore;` in Ihrem Code verwenden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-187">If the compiler indicates that a method cannot be found, then make sure that the provider's NuGet package is installed and that you have `using Microsoft.EntityFrameworkCore;` in your code.</span></span>

<span data-ttu-id="daeb1-188">Die folgende Tabelle enthält Beispiele für gängige Datenbankanbieter.</span><span class="sxs-lookup"><span data-stu-id="daeb1-188">The following table contains examples for common database providers.</span></span>

| <span data-ttu-id="daeb1-189">Datenbanksystem</span><span class="sxs-lookup"><span data-stu-id="daeb1-189">Database system</span></span>              | <span data-ttu-id="daeb1-190">Beispielkonfiguration</span><span class="sxs-lookup"><span data-stu-id="daeb1-190">Example configuration</span></span>                         | <span data-ttu-id="daeb1-191">NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="daeb1-191">NuGet package</span></span>
|:-----------------------------|-----------------------------------------------|--------------
| <span data-ttu-id="daeb1-192">SQL Server oder Azure SQL</span><span class="sxs-lookup"><span data-stu-id="daeb1-192">SQL Server or Azure SQL</span></span>      | `.UseSqlServer(connectionString)`             | [<span data-ttu-id="daeb1-193">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="daeb1-193">Microsoft.EntityFrameworkCore.SqlServer</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)
| <span data-ttu-id="daeb1-194">Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="daeb1-194">Azure Cosmos DB</span></span>              | `.UseCosmos(connectionString, databaseName)`  | [<span data-ttu-id="daeb1-195">Microsoft.EntityFrameworkCore.Cosmos</span><span class="sxs-lookup"><span data-stu-id="daeb1-195">Microsoft.EntityFrameworkCore.Cosmos</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)
| <span data-ttu-id="daeb1-196">SQLite</span><span class="sxs-lookup"><span data-stu-id="daeb1-196">SQLite</span></span>                       | `.UseSqlite(connectionString)`                | [<span data-ttu-id="daeb1-197">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="daeb1-197">Microsoft.EntityFrameworkCore.Sqlite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)
| <span data-ttu-id="daeb1-198">EF Core-In-Memory-Datenbank</span><span class="sxs-lookup"><span data-stu-id="daeb1-198">EF Core in-memory database</span></span>   | `.UseInMemoryDatabase(databaseName)`          | [<span data-ttu-id="daeb1-199">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="daeb1-199">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)
| <span data-ttu-id="daeb1-200">PostgreSQL\*</span><span class="sxs-lookup"><span data-stu-id="daeb1-200">PostgreSQL\*</span></span>                  | `.UseNpgsql(connectionString)`                | [<span data-ttu-id="daeb1-201">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="daeb1-201">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)
| <span data-ttu-id="daeb1-202">MySQL/MariaDB\*</span><span class="sxs-lookup"><span data-stu-id="daeb1-202">MySQL/MariaDB\*</span></span>               | `.UseMySql((connectionString)`                | [<span data-ttu-id="daeb1-203">Pomelo.EntityFrameworkCore.MySql</span><span class="sxs-lookup"><span data-stu-id="daeb1-203">Pomelo.EntityFrameworkCore.MySql</span></span>](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)
| <span data-ttu-id="daeb1-204">Oracle\*</span><span class="sxs-lookup"><span data-stu-id="daeb1-204">Oracle\*</span></span>                      | `.UseOracle(connectionString)`                | [<span data-ttu-id="daeb1-205">Oracle.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="daeb1-205">Oracle.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)

<span data-ttu-id="daeb1-206">\*Diese Datenbankanbieter werden nicht von Microsoft ausgeliefert.</span><span class="sxs-lookup"><span data-stu-id="daeb1-206">\*These database providers are not shipped by Microsoft.</span></span> <span data-ttu-id="daeb1-207">Weitere Informationen zu Datenbankanbietern finden Sie unter [Datenbankanbieter](xref:core/providers/index).</span><span class="sxs-lookup"><span data-stu-id="daeb1-207">See [Database Providers](xref:core/providers/index) for more information about database providers.</span></span>

> [!WARNING]
> <span data-ttu-id="daeb1-208">Die In-Memory-Datenbank von EF Core ist nicht für die Verwendung in der Produktion vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="daeb1-208">The EF Core in-memory database is not designed for production use.</span></span> <span data-ttu-id="daeb1-209">Außerdem ist sie möglicherweise auch nicht die beste Wahl für Tests.</span><span class="sxs-lookup"><span data-stu-id="daeb1-209">In addition, it may not be the best choice even for testing.</span></span> <span data-ttu-id="daeb1-210">Weitere Informationen finden Sie unter [Testen von Code, der EF Core](xref:core/testing/index) verwendet.</span><span class="sxs-lookup"><span data-stu-id="daeb1-210">See [Testing Code That Uses EF Core](xref:core/testing/index) for more information.</span></span>

<span data-ttu-id="daeb1-211">Weitere Informationen zur Verwendung von Verbindungszeichenfolgen mit EF Core finden Sie unter [Verbindungszeichenfolgen](xref:core/miscellaneous/connection-strings).</span><span class="sxs-lookup"><span data-stu-id="daeb1-211">See [Connection Strings](xref:core/miscellaneous/connection-strings) for more information on using connection strings with EF Core.</span></span>

<span data-ttu-id="daeb1-212">Eine für den Datenbankanbieter spezifische optionale Konfiguration wird in einem zusätzlichen anbieterspezifischen Generator ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="daeb1-212">Optional configuration specific to the database provider is performed in an additional provider-specific builder.</span></span> <span data-ttu-id="daeb1-213">Verwenden Sie beispielsweise <xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A>, um Wiederholungsversuche für Verbindungsresilienz beim Herstellen einer Verbindung mit Azure SQL zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="daeb1-213">For example, using <xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A> to configure retries for connection resiliency when connecting to Azure SQL:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .UseSqlServer(
                    @"Server=(localdb)\mssqllocaldb;Database=Test",
                    providerOptions =>
                        {
                            providerOptions.EnableRetryOnFailure();
                        });
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/AdditionalProviderConfiguration/ApplicationDbContext.cs?name=ApplicationDbContext)]

> [!TIP]
> <span data-ttu-id="daeb1-214">Der gleiche Datenbankanbieter wird für SQL Server und Azure SQL verwendet.</span><span class="sxs-lookup"><span data-stu-id="daeb1-214">The same database provider is used for SQL Server and Azure SQL.</span></span> <span data-ttu-id="daeb1-215">Es wird jedoch empfohlen, beim Herstellen einer Verbindung mit SQL Azure [Verbindungsresilienz](xref:core/miscellaneous/connection-resiliency) zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-215">However, it is recommended that [connection resiliency](xref:core/miscellaneous/connection-resiliency) be used when connecting to SQL Azure.</span></span>

<span data-ttu-id="daeb1-216">Weitere Informationen zur anbieterspezifischen Konfiguration finden Sie unter [Datenbankanbieter](xref:core/providers/index).</span><span class="sxs-lookup"><span data-stu-id="daeb1-216">See [Database Providers](xref:core/providers/index) for more information on provider-specific configuration.</span></span>

### <a name="other-dbcontext-configuration"></a><span data-ttu-id="daeb1-217">Weitere DbContext-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="daeb1-217">Other DbContext configuration</span></span>

<span data-ttu-id="daeb1-218">Weitere `DbContext`-Konfiguration kann vor oder nach dem Aufruf von `Use*` verkettet werden (dies macht keinen Unterschied).</span><span class="sxs-lookup"><span data-stu-id="daeb1-218">Other `DbContext` configuration can be chained either before or after (it makes no difference which) the `Use*` call.</span></span> <span data-ttu-id="daeb1-219">So aktivieren Sie z. B. Protokollierung sensibler Daten:</span><span class="sxs-lookup"><span data-stu-id="daeb1-219">For example, to turn on sensitive-data logging:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .EnableSensitiveDataLogging()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/AdditionalConfiguration/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="daeb1-220">Die folgende Tabelle enthält Beispiele für gängige Methoden, die für `DbContextOptionsBuilder` aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-220">The following table contains examples of common methods called on `DbContextOptionsBuilder`.</span></span>

| <span data-ttu-id="daeb1-221">DbContextOptionsBuilder-Methode</span><span class="sxs-lookup"><span data-stu-id="daeb1-221">DbContextOptionsBuilder method</span></span>                                                             | <span data-ttu-id="daeb1-222">Funktionsbeschreibung</span><span class="sxs-lookup"><span data-stu-id="daeb1-222">What it does</span></span>                                                | <span data-ttu-id="daeb1-223">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="daeb1-223">Learn more</span></span>
|:-------------------------------------------------------------------------------------------|-------------------------------------------------------------|--------------
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseQueryTrackingBehavior%2A>   | <span data-ttu-id="daeb1-224">Legt das Standardverhalten der Nachverfolgung für Abfragen fest.</span><span class="sxs-lookup"><span data-stu-id="daeb1-224">Sets the default tracking behavior for queries</span></span>              | [<span data-ttu-id="daeb1-225">Verhalten der Abfragenachverfolgung</span><span class="sxs-lookup"><span data-stu-id="daeb1-225">Query Tracking Behavior</span></span>](xref:core/querying/tracking)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A>                      | <span data-ttu-id="daeb1-226">Eine einfache Möglichkeit, EF Core-Protokolle zu erhalten (EF Core 5.0 oder höher).</span><span class="sxs-lookup"><span data-stu-id="daeb1-226">A simple way to get EF Core logs (EF Core 5.0 and later)</span></span>    | [<span data-ttu-id="daeb1-227">Protokollierung, Ereignisse und Diagnose</span><span class="sxs-lookup"><span data-stu-id="daeb1-227">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseLoggerFactory%2A>           | <span data-ttu-id="daeb1-228">Registriert eine `Microsoft.Extensions.Logging`-Factory.</span><span class="sxs-lookup"><span data-stu-id="daeb1-228">Registers an `Microsoft.Extensions.Logging` factory</span></span>         | [<span data-ttu-id="daeb1-229">Protokollierung, Ereignisse und Diagnose</span><span class="sxs-lookup"><span data-stu-id="daeb1-229">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> | <span data-ttu-id="daeb1-230">Schließt Anwendungsdaten in Ausnahmen und Protokollierung ein.</span><span class="sxs-lookup"><span data-stu-id="daeb1-230">Includes application data in exceptions and logging</span></span>         | [<span data-ttu-id="daeb1-231">Protokollierung, Ereignisse und Diagnose</span><span class="sxs-lookup"><span data-stu-id="daeb1-231">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A>       | <span data-ttu-id="daeb1-232">Ausführlichere Abfragefehler (auf Kosten der Leistung).</span><span class="sxs-lookup"><span data-stu-id="daeb1-232">More detailed query errors (at the expense of performance)</span></span>  | [<span data-ttu-id="daeb1-233">Protokollierung, Ereignisse und Diagnose</span><span class="sxs-lookup"><span data-stu-id="daeb1-233">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A>          | <span data-ttu-id="daeb1-234">Ignoriert Warnungen und andere Ereignisse oder löst diese aus.</span><span class="sxs-lookup"><span data-stu-id="daeb1-234">Ignore or throw for warnings and other events</span></span>               | [<span data-ttu-id="daeb1-235">Protokollierung, Ereignisse und Diagnose</span><span class="sxs-lookup"><span data-stu-id="daeb1-235">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A>            | <span data-ttu-id="daeb1-236">Registriert EF Core-Interceptors.</span><span class="sxs-lookup"><span data-stu-id="daeb1-236">Registers EF Core interceptors</span></span>                              | [<span data-ttu-id="daeb1-237">Protokollierung, Ereignisse und Diagnose</span><span class="sxs-lookup"><span data-stu-id="daeb1-237">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A>            | <span data-ttu-id="daeb1-238">Verwendet dynamischer Proxys für verzögertes Laden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-238">Use dynamic proxies for lazy-loading</span></span>                        | [<span data-ttu-id="daeb1-239">Verzögertes Laden</span><span class="sxs-lookup"><span data-stu-id="daeb1-239">Lazy Loading</span></span>](xref:core/querying/related-data/lazy)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A>         | <span data-ttu-id="daeb1-240">Verwendet dynamische Proxys für Änderungsnachverfolgung.</span><span class="sxs-lookup"><span data-stu-id="daeb1-240">Use dynamic proxies for change-tracking</span></span>                     | <span data-ttu-id="daeb1-241">Bald verfügbar...</span><span class="sxs-lookup"><span data-stu-id="daeb1-241">Coming soon...</span></span>

> [!NOTE]
> <span data-ttu-id="daeb1-242"><xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> und <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> sind Erweiterungsmethoden aus dem NuGet-Paket [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/).</span><span class="sxs-lookup"><span data-stu-id="daeb1-242"><xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> and <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> are extension methods from the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet package.</span></span> <span data-ttu-id="daeb1-243">Diese Art von „.UseSomething()“-Aufruf ist die empfohlene Methode zur Konfiguration und/oder Verwendung von EF Core-Erweiterungen, die in anderen Paketen enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="daeb1-243">This kind of ".UseSomething()" call is the recommended way to configure and/or use EF Core extensions contained in other packages.</span></span>

### <a name="dbcontextoptions-verses-dbcontextoptionstcontext"></a><span data-ttu-id="daeb1-244">`DbContextOptions` und `DbContextOptions<TContext>` im Vergleich</span><span class="sxs-lookup"><span data-stu-id="daeb1-244">`DbContextOptions` verses `DbContextOptions<TContext>`</span></span>

<span data-ttu-id="daeb1-245">Die meisten `DbContext`-Unterklassen, die `DbContextOptions` akzeptieren, sollten die [generische](/dotnet/csharp/programming-guide/generics/) `DbContextOptions<TContext>`-Variation verwenden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-245">Most `DbContext` subclasses that accept a `DbContextOptions` should use the [generic](/dotnet/csharp/programming-guide/generics/) `DbContextOptions<TContext>` variation.</span></span> <span data-ttu-id="daeb1-246">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="daeb1-246">For example:</span></span>

<!--
    public sealed class SealedApplicationDbContext : DbContext
    {
        public SealedApplicationDbContext(DbContextOptions<SealedApplicationDbContext> contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[SealedApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=SealedApplicationDbContext)]

<span data-ttu-id="daeb1-247">Dadurch wird sichergestellt, dass die richtigen Optionen für den spezifischen `DbContext`-Untertyp aus der Abhängigkeitsinjektion aufgelöst werden, auch wenn mehrere `DbContext`-Untertypen registriert sind.</span><span class="sxs-lookup"><span data-stu-id="daeb1-247">This ensures that the correct options for the specific `DbContext` subtype are resolved from dependency injection, even when multiple `DbContext` subtypes are registered.</span></span>

> [!TIP]
> <span data-ttu-id="daeb1-248">Ihr DbContext muss nicht versiegelt sein, aber das Versiegeln ist eine bewährte Vorgehensweise für Klassen, die nicht für Vererbung entworfen wurden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-248">Your DbContext does not need to be sealed, but sealing is best practice to do so for classes not designed to be inherited from.</span></span>

<span data-ttu-id="daeb1-249">Wenn jedoch vom `DbContext`-Untertyp selbst geerbt werden soll, sollte ein geschützter Konstruktor bereitgestellt werden, der generische `DbContextOptions` annimmt.</span><span class="sxs-lookup"><span data-stu-id="daeb1-249">However, if the `DbContext` subtype is itself intended to be inherited from, then it should expose a protected constructor taking a non-generic `DbContextOptions`.</span></span> <span data-ttu-id="daeb1-250">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="daeb1-250">For example:</span></span>

<!--
    public abstract class ApplicationDbContextBase : DbContext
    {
        protected ApplicationDbContextBase(DbContextOptions contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContextBase](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContextBase)]

<span data-ttu-id="daeb1-251">Dies ermöglicht es mehreren konkreten Unterklassen, diesen Basiskonstruktor mithilfe ihrer verschiedenen generischen `DbContextOptions<TContext>`-Instanzen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="daeb1-251">This allows multiple concrete subclasses to call this base constructor using their different generic `DbContextOptions<TContext>` instances.</span></span> <span data-ttu-id="daeb1-252">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="daeb1-252">For example:</span></span>

<!--
    public sealed class ApplicationDbContext1 : ApplicationDbContextBase
    {
        public ApplicationDbContext1(DbContextOptions<ApplicationDbContext1> contextOptions)
            : base(contextOptions)
        {
        }
    }

    public sealed class ApplicationDbContext2 : ApplicationDbContextBase
    {
        public ApplicationDbContext2(DbContextOptions<ApplicationDbContext2> contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext12](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContext12)]

<span data-ttu-id="daeb1-253">Beachten Sie, dass dies genau das gleiche Muster wie beim direkten Erben von `DbContext` ist.</span><span class="sxs-lookup"><span data-stu-id="daeb1-253">Notice that this is exactly the same pattern as when inheriting from `DbContext` directly.</span></span> <span data-ttu-id="daeb1-254">Das heißt, der `DbContext`-Konstruktor selbst akzeptiert aus diesem Grund nicht generische `DbContextOptions`.</span><span class="sxs-lookup"><span data-stu-id="daeb1-254">That is, the `DbContext` constructor itself accepts a non-generic `DbContextOptions` for this reason.</span></span>

<span data-ttu-id="daeb1-255">Eine `DbContext`-Unterklasse, die instanziiert und von der geerbt werden soll, sollte beide Formen des Konstruktors verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="daeb1-255">A `DbContext` subclass intended to be both instantiated and inherited from should expose both forms of constructor.</span></span> <span data-ttu-id="daeb1-256">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="daeb1-256">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> contextOptions)
            : base(contextOptions)
        {
        }

        protected ApplicationDbContext(DbContextOptions contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContext)]

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="daeb1-257">DbContext-Konfiguration zur Entwurfszeit</span><span class="sxs-lookup"><span data-stu-id="daeb1-257">Design-time DbContext configuration</span></span>

<span data-ttu-id="daeb1-258">EF Core-Entwurfszeittools (z. B. für [EF Core-Migrationen](xref:core/managing-schemas/migrations/index)) müssen in der Lage sein, eine funktionierende Instanz eines `DbContext`-Typs zu ermitteln und zu erstellen, um Details zu den Entitätstypen der Anwendung und deren Zuordnung zu einem Datenbankschema zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="daeb1-258">EF Core design-time tools such as those for [EF Core migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="daeb1-259">Dieser Prozess kann automatisch ausgeführt werden, solange das Tool die `DbContext`-Instanz problemlos so erstellen kann, dass sie ähnlich konfiguriert wird, wie sie zur Laufzeit konfiguriert würde.</span><span class="sxs-lookup"><span data-stu-id="daeb1-259">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="daeb1-260">Obwohl jedes Muster, das die erforderlichen Konfigurationsinformationen für die `DbContext`-Instanz bereitstellt, zur Laufzeit funktionieren kann, können Tools, die die Verwendung einer `DbContext`-Instanz zur Entwurfszeit erfordern, nur mit einer begrenzten Anzahl von Mustern funktionieren.</span><span class="sxs-lookup"><span data-stu-id="daeb1-260">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="daeb1-261">Diese werden unter [Kontexterstellung zur Entwurfszeit](xref:core/cli/dbcontext-creation) ausführlicher behandelt.</span><span class="sxs-lookup"><span data-stu-id="daeb1-261">These are covered in more detail in [Design-Time Context Creation](xref:core/cli/dbcontext-creation).</span></span>

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="daeb1-262">Vermeiden von DbContext-Threadingproblemen</span><span class="sxs-lookup"><span data-stu-id="daeb1-262">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="daeb1-263">Entity Framework Core unterstützt nicht die Ausführung mehrerer paralleler Vorgänge, die für dieselbe `DbContext`-Instanz ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-263">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="daeb1-264">Dies schließt die parallele Ausführung von asynchronen Abfragen und jede explizite gleichzeitige Verwendung aus mehreren Threads ein.</span><span class="sxs-lookup"><span data-stu-id="daeb1-264">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="daeb1-265">Verwenden Sie daher immer sofort asynchrone `await`-Aufrufe, oder verwenden Sie separate `DbContext`-Instanzen für Vorgänge, die parallel ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-265">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="daeb1-266">Wenn EF Core den Versuch erkennt, eine `DbContext`-Instanz parallel zu verwenden, wird eine `InvalidOperationException` mit einer Meldung wie der folgenden angezeigt:</span><span class="sxs-lookup"><span data-stu-id="daeb1-266">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="daeb1-267">Ein zweiter Vorgang wurde für diesen Kontext gestartet, bevor ein vorheriger Vorgang abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="daeb1-267">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="daeb1-268">Dies wird in der Regel durch verschiedene Threads verursacht, die dieselbe Instanz von DbContext verwenden, aber es ist nicht garantiert, dass Instanzmember threadsicher sind.</span><span class="sxs-lookup"><span data-stu-id="daeb1-268">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="daeb1-269">Wenn gleichzeitiger Zugriff nicht erkannt wird, kann dies zu nicht definiertem Verhalten, Anwendungsabstürzen und Datenbeschädigung führen.</span><span class="sxs-lookup"><span data-stu-id="daeb1-269">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="daeb1-270">Es gibt häufige Fehler, die versehentlich gleichzeitigen Zugriff auf dieselbe `DbContext`-Instanz verursachen können:</span><span class="sxs-lookup"><span data-stu-id="daeb1-270">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="asynchronous-operation-pitfalls"></a><span data-ttu-id="daeb1-271">Fehler bei asynchronen Vorgängen</span><span class="sxs-lookup"><span data-stu-id="daeb1-271">Asynchronous operation pitfalls</span></span>

<span data-ttu-id="daeb1-272">Asynchrone Methoden ermöglichen EF Core das Initiieren von Vorgängen, die auf nicht blockierende Weise auf die Datenbank zugreifen.</span><span class="sxs-lookup"><span data-stu-id="daeb1-272">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="daeb1-273">Wenn ein Aufrufer jedoch nicht auf den Abschluss einer dieser Methoden wartet und andere Vorgänge für `DbContext` ausführt, kann der Status von `DbContext` beschädigt sein (und ist es sehr wahrscheinlich auch).</span><span class="sxs-lookup"><span data-stu-id="daeb1-273">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="daeb1-274">Warten Sie immer sofort auf asynchrone EF Core-Methoden.</span><span class="sxs-lookup"><span data-stu-id="daeb1-274">Always await EF Core asynchronous methods immediately.</span></span>

### <a name="implicitly-sharing-dbcontext-instances-via-dependency-injection"></a><span data-ttu-id="daeb1-275">Implizites Freigeben von DbContext-Instanzen über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="daeb1-275">Implicitly sharing DbContext instances via dependency injection</span></span>

<span data-ttu-id="daeb1-276">Die [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Erweiterungsmethode registriert standardmäßig `DbContext`-Typen mit einer [begrenzten Lebensdauer](/aspnet/core/fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="daeb1-276">The [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="daeb1-277">Dies vermeidet in den meisten ASP.NET Core-Anwendungen Probleme durch gleichzeitigen Zugriff, weil es nur einen Thread gibt, der jede Clientanforderung zu einer bestimmten Zeit ausführt, und weil jede Anforderung einen separaten Abhängigkeitsinjektionsbereich (und damit eine separate `DbContext`-Instanz) erhält.</span><span class="sxs-lookup"><span data-stu-id="daeb1-277">This is safe from concurrent access issues in most ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span> <span data-ttu-id="daeb1-278">Für das Blazor Server-Hostingmodell wird eine logische Anforderung zum Verwalten der Blazor-Benutzerverbindung verwendet. Daher ist nur eine bereichsbezogene DbContext-Instanz pro Benutzerverbindung verfügbar, wenn der standardmäßige Injektionsbereich verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="daeb1-278">For Blazor Server hosting model, one logical request is used for maintaining the Blazor user circuit, and thus only one scoped DbContext instance is available per user circuit if the default injection scope is used.</span></span>

<span data-ttu-id="daeb1-279">Jeder Code, der explizit mehrere Threads parallel ausführt, sollte sicherstellen, dass auf `DbContext`-Instanzen niemals gleichzeitig zugegriffen wird.</span><span class="sxs-lookup"><span data-stu-id="daeb1-279">Any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="daeb1-280">Mithilfe von Abhängigkeitsinjektion kann dies erreicht werden, indem der Kontext als bereichsbezogen registriert wird und Bereiche (mit `IServiceScopeFactory`) für jeden Thread erstellt werden, oder indem `DbContext` als vorübergehender Wert registriert wird (mithilfe der Überladung von `AddDbContext`, die einen `ServiceLifetime`-Parameter annimmt).</span><span class="sxs-lookup"><span data-stu-id="daeb1-280">Using dependency injection, this can be achieved by either registering the context as scoped, and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="daeb1-281">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="daeb1-281">More reading</span></span>

- <span data-ttu-id="daeb1-282">Unter [Abhängigkeitsinjektion](/aspnet/core/fundamentals/dependency-injection) finden Sie weitere Informationen zur Abhängigkeitsinjektion.</span><span class="sxs-lookup"><span data-stu-id="daeb1-282">Read [Dependency Injection](/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="daeb1-283">Weitere Informationen finden Sie unter [Tests](xref:core/testing/index).</span><span class="sxs-lookup"><span data-stu-id="daeb1-283">Read [Testing](xref:core/testing/index) for more information.</span></span>
