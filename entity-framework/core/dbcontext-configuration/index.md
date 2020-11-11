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
# <a name="configuring-a-dbcontext"></a>Konfigurieren einer DbContext-Instanz

In diesem Artikel werden grundlegende Muster für die Konfiguration einer `DbContext`-Instanz über `DbContextOptions` erläutert, um mithilfe eines bestimmten EF Core-Anbieters und optionalem Verhalten eine Verbindung mit einer Datenbank herzustellen.

## <a name="design-time-dbcontext-configuration"></a>DbContext-Konfiguration zur Entwurfszeit

EF Core-Entwurfszeittools (z. B. [Migrationen](xref:core/managing-schemas/migrations/index)) müssen in der Lage sein, eine funktionierende Instanz eines `DbContext`-Typs zu ermitteln und zu erstellen, um Details zu den Entitätstypen der Anwendung und deren Zuordnung zu einem Datenbankschema zu erfassen. Dieser Prozess kann automatisch ausgeführt werden, solange das Tool die `DbContext`-Instanz problemlos so erstellen kann, dass sie ähnlich konfiguriert wird, wie sie zur Laufzeit konfiguriert würde.

Obwohl jedes Muster, das die erforderlichen Konfigurationsinformationen für die `DbContext`-Instanz bereitstellt, zur Laufzeit funktionieren kann, können Tools, die die Verwendung einer `DbContext`-Instanz zur Entwurfszeit erfordern, nur mit einer begrenzten Anzahl von Mustern funktionieren. Diese werden im Abschnitt [Kontexterstellung zur Entwurfszeit](xref:core/cli/dbcontext-creation) ausführlicher behandelt.

## <a name="configuring-dbcontextoptions"></a>Konfigurieren von DbContextOptions

`DbContext` muss über eine Instanz von `DbContextOptions` verfügen, um Aufgaben ausführen zu können. Die `DbContextOptions`-Instanz enthält Konfigurationsinformationen wie die folgenden:

- Den zu verwendenden Datenbankanbieter, der in der Regel durch Aufrufen einer Methode wie `UseSqlServer` oder `UseSqlite` ausgewählt wird. Diese Erweiterungsmethoden erfordern das entsprechende Anbieterpaket, z. B. `Microsoft.EntityFrameworkCore.SqlServer` oder `Microsoft.EntityFrameworkCore.Sqlite`. Die Methoden werden im `Microsoft.EntityFrameworkCore`-Namespace definiert.
- Alle erforderlichen Verbindungszeichenfolgen oder Bezeichner der Datenbankinstanz, die in der Regel als Argument an die oben genannte Anbieterauswahlmethode übergeben werden.
- Beliebige optionale Verhaltenselektoren auf Anbieterebene, die in der Regel auch innerhalb des Aufrufs der Anbieterauswahlmethode verkettet sind.
- Alle allgemeinen EF Core-Verhaltenselektoren, die normalerweise nach oder vor der Anbieterauswahlmethode verkettet sind.

Im folgenden Beispiel wird `DbContextOptions` für Folgendes konfiguriert: die Verwendung des SQL Server-Anbieters, eine Verbindung, die in der `connectionString`-Variablen enthalten ist, ein Befehlstimeout auf Anbieterebene und einen EF Core-Verhaltenselektor, der alle Abfragen, die in `DbContext` ausgeführt werden, standardmäßig als [nicht nachverfolgend](xref:core/querying/tracking#no-tracking-queries) definiert:

```csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]
> Anbieterauswahlmethoden und andere Verhaltensauswahlmethoden, die oben genannt werden, sind Erweiterungsmethoden für `DbContextOptions` oder anbieterspezifische Optionsklassen. Um Zugriff auf diese Erweiterungsmethoden zu erhalten, benötigen Sie möglicherweise einen Namespace (in der Regel `Microsoft.EntityFrameworkCore`) im Gültigkeitsbereich, und fügen Sie zusätzliche Paketabhängigkeiten in das Projekt ein.

`DbContextOptions` kann für `DbContext` bereitgestellt werden, indem die `OnConfiguring`-Methode überschrieben oder extern ein Konstruktorargument verwendet wird.

Wenn beides Möglichkeiten verwendet werden, wird `OnConfiguring` zuletzt angewendet und kann die für das Konstruktorargument bereitgestellten Optionen überschreiben.

### <a name="constructor-argument"></a>Konstruktorargument

Ihr Konstruktor kann `DbContextOptions` einfach wie folgt akzeptieren:

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
> Der Basiskonstruktor von DbContext akzeptiert auch die nicht generische Version von `DbContextOptions`, aber die Verwendung der nicht generischen Version ist für Anwendungen mit mehreren Kontexttypen nicht empfehlenswert.

Ihre Anwendung kann jetzt `DbContextOptions` übergeben, wenn ein Kontext instanziiert wird, wie im Folgenden gezeigt:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>OnConfiguring

Sie können `DbContextOptions` auch innerhalb des Kontexts selbst initialisieren. Obwohl Sie dieses Verfahren für die Basiskonfiguration verwenden können, müssen Sie in der Regel dennoch bestimmte Konfigurationsdetails von außen (z. B. eine Datenbankverbindungszeichenfolge) abrufen. Dies kann mit einer Konfigurations-API oder mit einer beliebigen anderen Möglichkeit erreicht werden.

Um `DbContextOptions` innerhalb des Kontexts zu initialisieren, überschreiben Sie die `OnConfiguring`-Methode und rufen die Methoden für den bereitgestellten `DbContextOptionsBuilder` auf:

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

Eine Anwendung kann einen solchen Kontext einfach instanziieren, ohne etwas an den Konstruktor zu übergeben:

```csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> Dieser Ansatz eignet sich nicht für Tests, es sei denn, die Tests zielen auf die vollständige Datenbank ab.

### <a name="using-dbcontext-with-dependency-injection"></a>Verwenden von DbContext mit Abhängigkeitsinjektion

EF Core unterstützt die Verwendung von `DbContext` mit einem Container für Abhängigkeitsinjektion. Ihr DbContext-Typ kann dem Dienstcontainer hinzugefügt werden, indem die `AddDbContext<TContext>`-Methode verwendet wird.

`AddDbContext<TContext>` stellt sowohl den DbContext-Typ (`TContext`) als auch den entsprechenden `DbContextOptions<TContext>` für die Injektion aus dem Dienstcontainer bereit.

Einzelheiten zur Abhängigkeitsinjektion finden Sie unter [Weitere Informationen](#more-reading) weiter unten.

Hinzufügen von `DbContext` zur Abhängigkeitsinjektion

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

Dies erfordert das Hinzufügen eines [Konstruktorarguments](#constructor-argument) zum DbContext-Typ, der `DbContextOptions<TContext>` akzeptiert.

Kontextcode:

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

Anwendungscode (in ASP.NET Core):

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

Anwendungscode (direktes Verwenden von ServiceProvider, weniger häufig):

```csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a>Vermeiden von DbContext-Threadingproblemen

Entity Framework Core unterstützt nicht die Ausführung mehrerer paralleler Vorgänge, die für dieselbe `DbContext`-Instanz ausgeführt werden. Dies schließt die parallele Ausführung von asynchronen Abfragen und jede explizite gleichzeitige Verwendung aus mehreren Threads ein. Verwenden Sie daher immer sofort asynchrone `await`-Aufrufe, oder verwenden Sie separate `DbContext`-Instanzen für Vorgänge, die parallel ausgeführt werden.

Wenn EF Core den Versuch erkennt, eine `DbContext`-Instanz parallel zu verwenden, wird eine `InvalidOperationException` mit einer Meldung wie der folgenden angezeigt:

> Ein zweiter Vorgang wurde für diesen Kontext gestartet, bevor ein vorheriger Vorgang abgeschlossen wurde. Dies wird in der Regel durch verschiedene Threads verursacht, die dieselbe Instanz von DbContext verwenden, aber es ist nicht garantiert, dass Instanzmember threadsicher sind.

Wenn gleichzeitiger Zugriff nicht erkannt wird, kann dies zu nicht definiertem Verhalten, Anwendungsabstürzen und Datenbeschädigung führen.

Es gibt häufige Fehler, die versehentlich gleichzeitigen Zugriff auf dieselbe `DbContext`-Instanz verursachen können:

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>Vergessen, auf den Abschluss eines asynchronen Vorgangs zu warten, bevor ein anderer Vorgang im gleichen DbContext gestartet wird

Asynchrone Methoden ermöglichen EF Core das Initiieren von Vorgängen, die auf nicht blockierende Weise auf die Datenbank zugreifen. Wenn ein Aufrufer jedoch nicht auf den Abschluss einer dieser Methoden wartet und andere Vorgänge für `DbContext` ausführt, kann der Status von `DbContext` beschädigt sein (und ist es sehr wahrscheinlich auch).

Warten Sie immer sofort auf asynchrone EF Core-Methoden.

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>Implizites Freigeben von DbContext-Instanzen über mehrere Threads hinweg über Abhängigkeitsinjektion

Die [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Erweiterungsmethode registriert standardmäßig `DbContext`-Typen mit einer [begrenzten Lebensdauer](/aspnet/core/fundamentals/dependency-injection#service-lifetimes).

Dies vermeidet in den meisten ASP.NET Core-Anwendungen Probleme durch gleichzeitigen Zugriff, weil es nur einen Thread gibt, der jede Clientanforderung zu einer bestimmten Zeit ausführt, und weil jede Anforderung einen separaten Abhängigkeitsinjektionsbereich (und damit eine separate `DbContext`-Instanz) erhält. Für das Blazor Server-Hostingmodell wird eine logische Anforderung zum Verwalten der Blazor-Benutzerverbindung verwendet. Daher ist nur eine bereichsbezogene DbContext-Instanz pro Benutzerverbindung verfügbar, wenn der standardmäßige Injektionsbereich verwendet wird.

Jeder Code, der explizit mehrere Threads parallel ausführt, sollte sicherstellen, dass auf `DbContext`-Instanzen niemals gleichzeitig zugegriffen wird.

Mithilfe von Abhängigkeitsinjektion kann dies erreicht werden, indem der Kontext als bereichsbezogen registriert wird und Bereiche (mit `IServiceScopeFactory`) für jeden Thread erstellt werden, oder indem `DbContext` als vorübergehender Wert registriert wird (mithilfe der Überladung von `AddDbContext`, die einen `ServiceLifetime`-Parameter annimmt).

## <a name="more-reading"></a>Weitere Informationen

- Unter [Abhängigkeitsinjektion](/aspnet/core/fundamentals/dependency-injection) finden Sie weitere Informationen zur Abhängigkeitsinjektion.
- Weitere Informationen finden Sie unter [Tests](xref:core/testing/index).
