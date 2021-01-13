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
# <a name="dbcontext-lifetime-configuration-and-initialization"></a>DbContext-Lebensdauer, -Konfiguration und -Initialisierung

Dieser Artikel zeigt grundlegende Muster für die Initialisierung und Konfiguration einer <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanz.

## <a name="the-dbcontext-lifetime"></a>Die DbContext-Lebensdauer

Die Lebensdauer eines `DbContext` beginnt mit dem Erstellen der Instanz und endet, wenn die Instanz [verworfen](/dotnet/standard/garbage-collection/unmanaged) wird. Eine `DbContext`-Instanz ist für die Verwendung in einer _einzelnen_ [Arbeitseinheit](https://www.martinfowler.com/eaaCatalog/unitOfWork.html) konzipiert. Dies bedeutet, dass die Lebensdauer einer `DbContext`-Instanz in der Regel sehr kurz ist.

> [!TIP]
> Um Martin Fowler aus dem Link oben zu zitieren: „Eine Arbeitseinheit verfolgt alle Aufgaben nach, die Sie während einer Geschäftstransaktion ausführen, die sich auf die Datenbank auswirken können. Wenn Sie fertig sind, findet sie alles heraus, was getan werden muss, um die Datenbank als Ergebnis Ihrer Arbeit zu verändern“.

Eine typische Arbeitseinheit bei Verwendung von Entity Framework Core (EF Core) umfasst Folgendes:

- Erstellen einer `DbContext`-Instanz.
- Nachverfolgen von Entitätsinstanzen durch den Kontext. Entitäten werden folgendermaßen nachverfolgt:
  - Durch [Rückgabe von einer Abfrage](xref:core/querying/tracking)
  - Durch [Hinzufügen oder Anfügen an den Kontext](xref:core/saving/disconnected-entities)
- An den nachverfolgten Entitäten werden Änderungen vorgenommen, die für die Implementierung der Geschäftsregel erforderlich sind.
- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> oder <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> wird aufgerufen. EF Core erkennt die vorgenommenen Änderungen und schreibt sie in die Datenbank.
- Die `DbContext` Instanz wird verworfen.

> [!IMPORTANT]
>
> - Es ist sehr wichtig, <xref:Microsoft.EntityFrameworkCore.DbContext> nach der Verwendung zu verwerfen. Dadurch wird sichergestellt, dass alle nicht verwalteten Ressourcen freigegeben werden und dass die Registrierung für alle Ereignisse oder anderen Hooks aufgehoben wird, um Speicherverluste zu verhindern, falls die Instanz weiterhin referenziert wird.
> - [DbContext ist **nicht threadsicher**](#avoiding-dbcontext-threading-issues). Geben Sie keine Kontexte zwischen Threads frei. Stellen Sie sicher, dass [await](/dotnet/csharp/language-reference/operators/await) für alle asynchronen Aufrufe verwendet wird, bevor Sie die Kontextinstanz weiterhin verwenden.
> - Eine <xref:System.InvalidOperationException>, die von EF Core-Code ausgelöst wird, kann den Kontext in einen nicht wiederherstellbaren Zustand versetzen. Solche Ausnahmen weisen auf einen Programmfehler hin und sind nicht für Wiederherstellung konzipiert.

## <a name="dbcontext-in-dependency-injection-for-aspnet-core"></a>DbContext in Abhängigkeitsinjektion für ASP.NET Core

In vielen Webanwendungen entspricht jede HTTP-Anforderung einer einzelnen Arbeitseinheit. Dies bewirkt, dass das Anbinden der Kontextlebensdauer an die Anforderung für Webanwendungen eine gute Standardeinstellung ist.

ASP.NET Core-Anwendungen werden [mithilfe von Abhängigkeitsinjektion konfiguriert](/aspnet/core/fundamentals/startup). EF Core kann dieser Konfiguration mithilfe von <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> in der [`ConfigureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method)-Methode von `Startup.cs` hinzugefügt werden. Beispiel:

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();

            services.AddDbContext<ApplicationDbContext>(
                options => options.UseSqlServer("name=ConnectionStrings:DefaultConnection"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Startup.cs?name=ConfigureServices)]

In diesem Beispiel wird eine `DbContext`-Unterklasse mit dem Namen `ApplicationDbContext` als bereichsbezogener Dienst im ASP.NET Core-Anwendungsdienstanbieter registriert (auch als Abhängigkeitsinjektionscontainer bezeichnet). Der Kontext ist so konfiguriert, dass der SQL Server-Datenbankanbieter verwendet und die Verbindungszeichenfolge aus der ASP.NET Core-Konfiguration gelesen wird. Es spielt in der Regel keine Rolle, _wo_ in `ConfigureServices` der Aufruf von `AddDbContext` erfolgt.

Die `ApplicationDbContext`-Klasse muss einen öffentlichen Konstruktor mit einem Parameter `DbContextOptions<ApplicationDbContext>` bereitstellen. Auf diese Weise wird die Kontextkonfiguration aus `AddDbContext` an `DbContext` übergeben. Beispiel:

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

`ApplicationDbContext` kann dann mithilfe von Konstruktorinjektion in ASP.NET Core-Controllern oder anderen Diensten verwendet werden. Beispiel:

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

Das Endergebnis ist eine `ApplicationDbContext`-Instanz, die für jede Anforderung erstellt und an den Controller zum Ausführen einer Arbeitseinheit übergeben wird, bevor sie verworfen wird, wenn die Anforderung beendet wird.

Weitere Informationen zu den Konfigurationsoptionen finden Sie weiter unten in diesem Artikel. Weitere Informationen zur Konfiguration und zu Abhängigkeitsinjektion in ASP.NET Core finden Sie unter [App-Start in ASP.NET Core](/aspnet/core/fundamentals/startup) und [Abhängigkeitsinjektion in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection).

<!-- See also [Using Dependency Injection](TODO) for advanced dependency injection configuration with EF Core. -->

## <a name="simple-dbcontext-initialization-with-new"></a>Einfache DbContext-Initialisierung mit „new“

`DbContext`-Instanzen können auf die normale .NET-Weise erstellt werden, z. B. mit `new` in C#. Die Konfiguration kann durch Überschreiben der `OnConfiguring`-Methode oder durch Übergeben von Optionen an den Konstruktor ausgeführt werden. Beispiel:

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

Mit diesem Muster ist es auch einfach, Konfiguration wie etwa eine Verbindungszeichenfolge über den `DbContext`-Konstruktor zu übergeben. Beispiel:

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

Alternativ kann `DbContextOptionsBuilder` verwendet werden, um ein `DbContextOptions`-Objekt zu erstellen, das dann an den `DbContext`-Konstruktor übergeben wird. Dadurch kann ein `DbContext`, der für Abhängigkeitsinjektion konfiguriert ist, ebenfalls explizit erstellt werden. Wenn Sie z. B. `ApplicationDbContext` verwenden, der für ASP.NET Core Web-Apps oben definiert ist:

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

`DbContextOptions` kann erstellt werden, und der Konstruktor kann explizit aufgerufen werden:

<!--
            var contextOptions = new DbContextOptionsBuilder<ApplicationDbContext>()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test")
                .Options;

            using var context = new ApplicationDbContext(contextOptions);
-->
[!code-csharp[UseNewForWebApp](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/UseNewForWebApp.cs?name=UseNewForWebApp)]

## <a name="using-a-dbcontext-factory-eg-for-blazor"></a>Verwenden einer DbContext-Factory (z. B. für Blazor)

Einige Anwendungstypen (z. B. [ASP.NET Core Blazor](/aspnet/core/blazor/)) verwenden Abhängigkeitsinjektion, erstellen aber keinen Dienstbereich, der der gewünschten `DbContext`-Lebensdauer entspricht. Auch wenn eine solche Ausrichtung vorhanden ist, muss die Anwendung möglicherweise mehrere Arbeitseinheiten innerhalb dieses Bereichs ausführen. Beispielsweise mehrere Arbeitseinheiten innerhalb einer einzelnen HTTP-Anforderung.

In diesen Fällen kann <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> zum Registrieren einer Factory zum Erstellen von `DbContext`-Instanzen verwendet werden. Beispiel:

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContextFactory<ApplicationDbContext>(options =>
                options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/FactoryServicesExample.cs?name=ConfigureServices)]

Die `ApplicationDbContext`-Klasse muss einen öffentlichen Konstruktor mit einem Parameter `DbContextOptions<ApplicationDbContext>` bereitstellen. Dabei handelt es sich um dasselbe Muster, das im Abschnitt „Traditionelles ASP.NET Core“ weiter oben verwendet wird.

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

Die `DbContextFactory`-Factory kann dann mithilfe von Konstruktorinjektion in anderen Diensten verwendet werden. Beispiel:

<!--
        private readonly IDbContextFactory<ApplicationDbContext> _contextFactory;

        public MyController(IDbContextFactory<ApplicationDbContext> contextFactory)
        {
            _contextFactory = contextFactory;
        }
-->
[!code-csharp[Construct](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=Construct)]

Die injizierte Factory kann dann verwendet werden, um DbContext-Instanzen im Dienstcode zu erstellen. Beispiel:

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

Beachten Sie, dass die `DbContext`-Instanzen, die auf diese Weise erstellt werden, **nicht** vom Dienstanbieter der Anwendung verwaltet werden, weshalb diese von der Anwendung gelöscht werden müssen.

Weitere Informationen zur Verwendung von EF Core mit Blazor finden Sie unter [ASP.NET Core Blazor Server mit Entity Framework Core](/aspnet/core/blazor/blazor-server-ef-core).

## <a name="dbcontextoptions"></a>DbContextOptions

Der Ausgangspunkt für die gesamte `DbContext`-Konfiguration ist <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>. Es gibt drei Möglichkeiten, diesen Generator abzurufen:

- In `AddDbContext` und verwandte Methoden
- In `OnConfiguring`
- Explizit generiert mit `new`

Beispiele für jede dieser Möglichkeiten werden in den vorangehenden Abschnitten aufgeführt. Dieselbe Konfiguration kann unabhängig davon angewendet werden, wie der Generator abgerufen wurde. Außerdem wird `OnConfiguring` immer unabhängig davon aufgerufen, wie der Kontext erstellt wird. Dies bedeutet, dass `OnConfiguring` selbst dann verwendet werden kann, um zusätzliche Konfigurationen auszuführen, wenn `AddDbContext` verwendet wird.

### <a name="configuring-the-database-provider"></a>Konfigurieren des Datenbankanbieters

Jede `DbContext`-Instanz muss so konfiguriert werden, dass sie nur einen Datenbankanbieter verwendet. (Verschiedene Instanzen eines `DbContext`-Untertyps können mit unterschiedlichen Datenbankanbietern verwendet werden, aber eine einzelne Instanz darf nur einen Datenbankanbieter verwenden.) Ein Datenbankanbieter wird mithilfe eines bestimmten `Use*`"-Aufrufs konfiguriert. Wenn beispielsweise der SQL Server-Datenbankanbieter verwendet werden soll:

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

Diese `Use*`"-Methoden sind Erweiterungsmethoden, die vom Datenbankanbieter implementiert werden. Dies bedeutet, dass das NuGet-Paket des Datenbankanbieters installiert werden muss, bevor die Erweiterungsmethode verwendet werden kann.

> [!TIP]
> EF Core-Datenbankanbieter nutzen [Erweiterungsmethoden](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods) ausgiebig. Wenn der Compiler angibt, dass eine Methode nicht gefunden werden kann, stellen Sie sicher, dass das NuGet-Paket des Anbieters installiert ist und dass Sie `using Microsoft.EntityFrameworkCore;` in Ihrem Code verwenden.

Die folgende Tabelle enthält Beispiele für gängige Datenbankanbieter.

| Datenbanksystem              | Beispielkonfiguration                         | NuGet-Paket
|:-----------------------------|-----------------------------------------------|--------------
| SQL Server oder Azure SQL      | `.UseSqlServer(connectionString)`             | [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)
| Azure Cosmos DB              | `.UseCosmos(connectionString, databaseName)`  | [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)
| SQLite                       | `.UseSqlite(connectionString)`                | [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)
| EF Core-In-Memory-Datenbank   | `.UseInMemoryDatabase(databaseName)`          | [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)
| PostgreSQL*                  | `.UseNpgsql(connectionString)`                | [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)
| MySQL/MariaDB*               | `.UseMySql((connectionString)`                | [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)
| Oracle*                      | `.UseOracle(connectionString)`                | [Oracle.EntityFrameworkCore](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)

*Diese Datenbankanbieter werden nicht von Microsoft ausgeliefert. Weitere Informationen zu Datenbankanbietern finden Sie unter [Datenbankanbieter](xref:core/providers/index).

> [!WARNING]
> Die In-Memory-Datenbank von EF Core ist nicht für die Verwendung in der Produktion vorgesehen. Außerdem ist sie möglicherweise auch nicht die beste Wahl für Tests. Weitere Informationen finden Sie unter [Testen von Code, der EF Core](xref:core/testing/index) verwendet.

Weitere Informationen zur Verwendung von Verbindungszeichenfolgen mit EF Core finden Sie unter [Verbindungszeichenfolgen](xref:core/miscellaneous/connection-strings).

Eine für den Datenbankanbieter spezifische optionale Konfiguration wird in einem zusätzlichen anbieterspezifischen Generator ausgeführt. Verwenden Sie beispielsweise <xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A>, um Wiederholungsversuche für Verbindungsresilienz beim Herstellen einer Verbindung mit Azure SQL zu konfigurieren:

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
> Der gleiche Datenbankanbieter wird für SQL Server und Azure SQL verwendet. Es wird jedoch empfohlen, beim Herstellen einer Verbindung mit SQL Azure [Verbindungsresilienz](xref:core/miscellaneous/connection-resiliency) zu verwenden.

Weitere Informationen zur anbieterspezifischen Konfiguration finden Sie unter [Datenbankanbieter](xref:core/providers/index).

### <a name="other-dbcontext-configuration"></a>Weitere DbContext-Konfiguration

Weitere `DbContext`-Konfiguration kann vor oder nach dem Aufruf von `Use*` verkettet werden (dies macht keinen Unterschied). So aktivieren Sie z. B. Protokollierung sensibler Daten:

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

Die folgende Tabelle enthält Beispiele für gängige Methoden, die für `DbContextOptionsBuilder` aufgerufen werden.

| DbContextOptionsBuilder-Methode                                                             | Funktionsbeschreibung                                                | Weitere Informationen
|:-------------------------------------------------------------------------------------------|-------------------------------------------------------------|--------------
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseQueryTrackingBehavior%2A>   | Legt das Standardverhalten der Nachverfolgung für Abfragen fest.              | [Verhalten der Abfragenachverfolgung](xref:core/querying/tracking)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A>                      | Eine einfache Möglichkeit, EF Core-Protokolle zu erhalten (EF Core 5.0 oder höher).    | [Protokollierung, Ereignisse und Diagnose](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseLoggerFactory%2A>           | Registriert eine `Microsoft.Extensions.Logging`-Factory.         | [Protokollierung, Ereignisse und Diagnose](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> | Schließt Anwendungsdaten in Ausnahmen und Protokollierung ein.         | [Protokollierung, Ereignisse und Diagnose](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A>       | Ausführlichere Abfragefehler (auf Kosten der Leistung).  | [Protokollierung, Ereignisse und Diagnose](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A>          | Ignoriert Warnungen und andere Ereignisse oder löst diese aus.               | [Protokollierung, Ereignisse und Diagnose](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A>            | Registriert EF Core-Interceptors.                              | [Protokollierung, Ereignisse und Diagnose](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A>            | Verwendet dynamischer Proxys für verzögertes Laden.                        | [Verzögertes Laden](xref:core/querying/related-data/lazy)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A>         | Verwendet dynamische Proxys für Änderungsnachverfolgung.                     | Bald verfügbar...

> [!NOTE]
> <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> und <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> sind Erweiterungsmethoden aus dem NuGet-Paket [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/). Diese Art von „.UseSomething()“-Aufruf ist die empfohlene Methode zur Konfiguration und/oder Verwendung von EF Core-Erweiterungen, die in anderen Paketen enthalten sind.

### <a name="dbcontextoptions-verses-dbcontextoptionstcontext"></a>`DbContextOptions` und `DbContextOptions<TContext>` im Vergleich

Die meisten `DbContext`-Unterklassen, die `DbContextOptions` akzeptieren, sollten die [generische](/dotnet/csharp/programming-guide/generics/) `DbContextOptions<TContext>`-Variation verwenden. Beispiel:

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

Dadurch wird sichergestellt, dass die richtigen Optionen für den spezifischen `DbContext`-Untertyp aus der Abhängigkeitsinjektion aufgelöst werden, auch wenn mehrere `DbContext`-Untertypen registriert sind.

> [!TIP]
> Ihr DbContext muss nicht versiegelt sein, aber das Versiegeln ist eine bewährte Vorgehensweise für Klassen, die nicht für Vererbung entworfen wurden.

Wenn jedoch vom `DbContext`-Untertyp selbst geerbt werden soll, sollte ein geschützter Konstruktor bereitgestellt werden, der generische `DbContextOptions` annimmt. Beispiel:

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

Dies ermöglicht es mehreren konkreten Unterklassen, diesen Basiskonstruktor mithilfe ihrer verschiedenen generischen `DbContextOptions<TContext>`-Instanzen aufzurufen. Beispiel:

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

Beachten Sie, dass dies genau das gleiche Muster wie beim direkten Erben von `DbContext` ist. Das heißt, der `DbContext`-Konstruktor selbst akzeptiert aus diesem Grund nicht generische `DbContextOptions`.

Eine `DbContext`-Unterklasse, die instanziiert und von der geerbt werden soll, sollte beide Formen des Konstruktors verfügbar machen. Beispiel:

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

## <a name="design-time-dbcontext-configuration"></a>DbContext-Konfiguration zur Entwurfszeit

EF Core-Entwurfszeittools (z. B. für [EF Core-Migrationen](xref:core/managing-schemas/migrations/index)) müssen in der Lage sein, eine funktionierende Instanz eines `DbContext`-Typs zu ermitteln und zu erstellen, um Details zu den Entitätstypen der Anwendung und deren Zuordnung zu einem Datenbankschema zu erfassen. Dieser Prozess kann automatisch ausgeführt werden, solange das Tool die `DbContext`-Instanz problemlos so erstellen kann, dass sie ähnlich konfiguriert wird, wie sie zur Laufzeit konfiguriert würde.

Obwohl jedes Muster, das die erforderlichen Konfigurationsinformationen für die `DbContext`-Instanz bereitstellt, zur Laufzeit funktionieren kann, können Tools, die die Verwendung einer `DbContext`-Instanz zur Entwurfszeit erfordern, nur mit einer begrenzten Anzahl von Mustern funktionieren. Diese werden unter [Kontexterstellung zur Entwurfszeit](xref:core/cli/dbcontext-creation) ausführlicher behandelt.

## <a name="avoiding-dbcontext-threading-issues"></a>Vermeiden von DbContext-Threadingproblemen

Entity Framework Core unterstützt nicht die Ausführung mehrerer paralleler Vorgänge, die für dieselbe `DbContext`-Instanz ausgeführt werden. Dies schließt die parallele Ausführung von asynchronen Abfragen und jede explizite gleichzeitige Verwendung aus mehreren Threads ein. Verwenden Sie daher immer sofort asynchrone `await`-Aufrufe, oder verwenden Sie separate `DbContext`-Instanzen für Vorgänge, die parallel ausgeführt werden.

Wenn EF Core den Versuch erkennt, eine `DbContext`-Instanz parallel zu verwenden, wird eine `InvalidOperationException` mit einer Meldung wie der folgenden angezeigt:

> Ein zweiter Vorgang wurde für diesen Kontext gestartet, bevor ein vorheriger Vorgang abgeschlossen wurde. Dies wird in der Regel durch verschiedene Threads verursacht, die dieselbe Instanz von DbContext verwenden, aber es ist nicht garantiert, dass Instanzmember threadsicher sind.

Wenn gleichzeitiger Zugriff nicht erkannt wird, kann dies zu nicht definiertem Verhalten, Anwendungsabstürzen und Datenbeschädigung führen.

Es gibt häufige Fehler, die versehentlich gleichzeitigen Zugriff auf dieselbe `DbContext`-Instanz verursachen können:

### <a name="asynchronous-operation-pitfalls"></a>Fehler bei asynchronen Vorgängen

Asynchrone Methoden ermöglichen EF Core das Initiieren von Vorgängen, die auf nicht blockierende Weise auf die Datenbank zugreifen. Wenn ein Aufrufer jedoch nicht auf den Abschluss einer dieser Methoden wartet und andere Vorgänge für `DbContext` ausführt, kann der Status von `DbContext` beschädigt sein (und ist es sehr wahrscheinlich auch).

Warten Sie immer sofort auf asynchrone EF Core-Methoden.

### <a name="implicitly-sharing-dbcontext-instances-via-dependency-injection"></a>Implizites Freigeben von DbContext-Instanzen über Abhängigkeitsinjektion

Die [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Erweiterungsmethode registriert standardmäßig `DbContext`-Typen mit einer [begrenzten Lebensdauer](/aspnet/core/fundamentals/dependency-injection#service-lifetimes).

Dies vermeidet in den meisten ASP.NET Core-Anwendungen Probleme durch gleichzeitigen Zugriff, weil es nur einen Thread gibt, der jede Clientanforderung zu einer bestimmten Zeit ausführt, und weil jede Anforderung einen separaten Abhängigkeitsinjektionsbereich (und damit eine separate `DbContext`-Instanz) erhält. Für das Blazor Server-Hostingmodell wird eine logische Anforderung zum Verwalten der Blazor-Benutzerverbindung verwendet. Daher ist nur eine bereichsbezogene DbContext-Instanz pro Benutzerverbindung verfügbar, wenn der standardmäßige Injektionsbereich verwendet wird.

Jeder Code, der explizit mehrere Threads parallel ausführt, sollte sicherstellen, dass auf `DbContext`-Instanzen niemals gleichzeitig zugegriffen wird.

Mithilfe von Abhängigkeitsinjektion kann dies erreicht werden, indem der Kontext als bereichsbezogen registriert wird und Bereiche (mit `IServiceScopeFactory`) für jeden Thread erstellt werden, oder indem `DbContext` als vorübergehender Wert registriert wird (mithilfe der Überladung von `AddDbContext`, die einen `ServiceLifetime`-Parameter annimmt).

## <a name="more-reading"></a>Weitere Informationen

- Unter [Abhängigkeitsinjektion](/aspnet/core/fundamentals/dependency-injection) finden Sie weitere Informationen zur Abhängigkeitsinjektion.
- Weitere Informationen finden Sie unter [Tests](xref:core/testing/index).
