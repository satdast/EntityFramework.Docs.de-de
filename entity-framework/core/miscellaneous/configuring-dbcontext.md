---
title: Konfigurieren eines dbcontext-EF Core
description: Strategien zum Konfigurieren von dbcontext mit Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 3e45199e6fc0c8c105ccb0bb03175b4b08716b3f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617790"
---
# <a name="configuring-a-dbcontext"></a>Konfigurieren einer DbContext-Instanz

In diesem Artikel werden grundlegende Muster für `DbContext` die Konfiguration eines über eine veranschaulicht `DbContextOptions` , um mithilfe eines bestimmten EF Core Anbieters und optionalem Verhalten eine Verbindung mit einer Datenbank herzustellen.

## <a name="design-time-dbcontext-configuration"></a>Dbcontext-Konfiguration zur Entwurfszeit

EF Core Entwurfszeit Tools (z. b. [Migrationen](xref:core/managing-schemas/migrations/index) ) müssen in der Lage sein, eine funktionierende Instanz eines Typs zu ermitteln und zu erstellen, um `DbContext` Details zu den Entitäts Typen der Anwendung und deren Zuordnung zu einem Datenbankschema zu erfassen. Dieser Prozess kann automatisch ausgeführt werden, solange das Tool auf einfache Weise so erstellen kann `DbContext` , dass es so konfiguriert wird, wie es zur Laufzeit konfiguriert wird.

Obwohl jedes Muster, das die erforderlichen Konfigurationsinformationen für bereitstellt `DbContext` , zur Laufzeit funktionieren kann, können Tools, die die Verwendung `DbContext` von zur Entwurfszeit erfordern, nur mit einer begrenzten Anzahl von Mustern funktionieren. Diese werden im Abschnitt [Entwurfszeit-Kontext Erstellung](xref:core/miscellaneous/cli/dbcontext-creation) ausführlicher behandelt.

## <a name="configuring-dbcontextoptions"></a>Konfigurieren von dbcontextoptions

`DbContext` muss über eine Instanz von verfügen `DbContextOptions` , um die Arbeit auszuführen. Die- `DbContextOptions` Instanz enthält Konfigurationsinformationen wie z. b.:

- Der zu verwendende Datenbankanbieter, der in der Regel durch Aufrufen einer Methode wie oder ausgewählt wird `UseSqlServer` `UseSqlite` . Diese Erweiterungs Methoden erfordern das entsprechende Anbieter Paket, z `Microsoft.EntityFrameworkCore.SqlServer` . b `Microsoft.EntityFrameworkCore.Sqlite` . oder. Die-Methoden werden im- `Microsoft.EntityFrameworkCore` Namespace definiert.
- Alle erforderlichen Verbindungs Zeichenfolgen oder Bezeichner der Daten Bank Instanz, die in der Regel als Argument an die oben genannte Anbieter Auswahlmethode weitergegeben werden.
- Beliebige optionale verhaltenselektoren auf Anbieter Ebene, die in der Regel auch innerhalb des Aufrufes der Anbieter Auswahlmethode verkettet sind
- Alle allgemeinen EF Core verhaltenselektoren, die normalerweise nach oder vor der Anbieter Auswahlmethode verkettet sind

Im folgenden Beispiel wird das `DbContextOptions` für die Verwendung des SQL Server Anbieters, eine in der-Variable enthaltene Verbindung, `connectionString` ein Befehls Timeout auf Anbieter Ebene und eine EF Core Verhaltens Auswahl konfiguriert, die standardmäßig alle Abfragen in der `DbContext` [nicht-Nachverfolgung](xref:core/querying/tracking#no-tracking-queries) ausführt:

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> Anbieter Auswahlmethoden und andere Verhaltens Auswahlmethoden, die oben erwähnt werden, sind Erweiterungs Methoden für `DbContextOptions` oder anbieterspezifische Options Klassen. Um Zugriff auf diese Erweiterungs Methoden zu erhalten, benötigen Sie möglicherweise einen Namespace (in der Regel `Microsoft.EntityFrameworkCore` ) im Gültigkeitsbereich und fügen zusätzliche Paketabhängigkeiten in das Projekt ein.

`DbContextOptions`Kann für das bereitgestellt werden, `DbContext` indem die-Methode überschrieben wird, `OnConfiguring` oder extern über ein Konstruktorargument.

Wenn beide verwendet werden, `OnConfiguring` wird zuletzt angewendet, und die für das Konstruktorargument bereitgestellten Optionen können überschrieben werden.

### <a name="constructor-argument"></a>Konstruktorargument

Der Konstruktor kann einfach `DbContextOptions` wie folgt akzeptieren:

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]  
> Der Basiskonstruktor von dbcontext akzeptiert auch die nicht generische Version von `DbContextOptions` , aber die Verwendung der nicht generischen Version wird nicht für Anwendungen mit mehreren Kontext Typen empfohlen.

Die Anwendung kann nun beim `DbContextOptions` Instanziieren eines Kontexts wie folgt übergeben:

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a>Onkonfiguration

Sie können auch den `DbContextOptions` innerhalb des Kontexts selbst initialisieren. Obwohl Sie dieses Verfahren für die Basiskonfiguration verwenden können, müssen Sie in der Regel weiterhin bestimmte Konfigurationsdetails von außen (z. b. eine Daten bankverbindungs Zeichenfolge) erhalten. Dies kann mit einer Konfigurations-API oder mit einer beliebigen anderen Möglichkeit erreicht werden.

Um `DbContextOptions` innerhalb des Kontexts zu initialisieren, überschreiben Sie die `OnConfiguring` -Methode, und nennen Sie die Methoden für die bereitgestellten `DbContextOptionsBuilder`

``` csharp
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

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> Dieser Ansatz eignet sich nicht für Tests, es sei denn, die Tests sind auf die vollständige Datenbank ausgerichtet.

### <a name="using-dbcontext-with-dependency-injection"></a>Verwenden von dbcontext mit Abhängigkeitsinjektion

EF Core unterstützt die Verwendung `DbContext` von mit einem Container für Abhängigkeitsinjektion. Der dbcontext-Typ kann dem Dienst Container mithilfe der-Methode hinzugefügt werden `AddDbContext<TContext>` .

`AddDbContext<TContext>` von werden sowohl der dbcontext-Typ `TContext` als auch der entsprechende `DbContextOptions<TContext>` für Injection aus dem Dienst Container verfügbar gemacht.

Weitere Informationen zur Abhängigkeitsinjektion finden Sie unten unter [Weitere](#more-reading) Informationen.

Hinzufügen von `DbContext` zur Abhängigkeitsinjektion:

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

Dies erfordert das Hinzufügen eines [Konstruktorarguments](#constructor-argument) zu Ihrem dbcontext-Typ, der akzeptiert `DbContextOptions<TContext>` .

Kontext Code:

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

Anwendungscode (in ASP.net Core):

``` csharp
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

Anwendungscode (Verwenden von Service Provider direkt, weniger häufig):

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a>Vermeiden von dbcontext-Threading Problemen

Entity Framework Core unterstützt nicht mehrere parallele Vorgänge, die auf derselben Instanz ausgeführt werden `DbContext` . Dies schließt die parallele Ausführung von asynchronen Abfragen und jede explizite gleichzeitige Verwendung mehrerer Threads ein. Daher wird immer `await` Async sofort aufgerufen, oder es werden separate `DbContext` Instanzen für Vorgänge verwendet, die parallel ausgeführt werden.

Wenn EF Core den Versuch erkennt, eine- `DbContext` Instanz gleichzeitig zu verwenden, wird eine `InvalidOperationException` mit einer Meldung wie die folgende angezeigt:

> Ein zweiter Vorgang, der in diesem Kontext gestartet wurde, bevor ein vorheriger Vorgang abgeschlossen wurde. Dies wird in der Regel durch unterschiedliche Threads verursacht, die dieselbe Instanz von dbcontext verwenden, aber es ist nicht garantiert, dass Instanzmember Thread sicher sind.

Wenn der gleichzeitige Zugriff nicht erkannt wird, kann dies zu nicht definiertem Verhalten, Anwendungs abstürzen und Daten Beschädigung führen.

Es gibt häufige Fehler, die versehentlich den gleichzeitigen Zugriff auf dieselbe `DbContext` Instanz verursachen können:

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a>Vergessen Sie nicht, auf den Abschluss eines asynchronen Vorgangs zu warten, bevor Sie einen anderen Vorgang im gleichen dbcontext starten.

Asynchrone Methoden ermöglichen EF Core das Initiieren von Vorgängen, die auf eine nicht blockierende Weise auf die Datenbank zugreifen. Wenn ein Aufrufer jedoch nicht auf den Abschluss einer dieser Methoden wartet und weiterhin andere Vorgänge auf dem ausführt `DbContext` , kann der Zustand von `DbContext` (und wahrscheinlich) beschädigt sein.

Asynchrone Methoden sollten immer sofort EF Core werden.  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a>Implizites Freigeben von dbcontext-Instanzen über mehrere Threads über Abhängigkeitsinjektion

Die [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) Erweiterungsmethode registriert `DbContext` Typen standardmäßig mit einer Gültigkeits [Dauer](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) .

Dies ist vor gleichzeitigen Zugriffsproblemen in den meisten ASP.net Core Anwendungen sicher, weil nur ein Thread zu einem bestimmten Zeitpunkt jede Client Anforderung ausführt und jede Anforderung einen separaten Abhängigkeits einschleusungs Bereich (und somit eine separate `DbContext` Instanz) erhält. Für das blazor-Server Hostingmodell wird eine logische Anforderung zum Verwalten der blazor-Benutzer Verbindung verwendet. Daher ist nur eine Bereichs bezogene dbcontext-Instanz pro Benutzer Verbindung verfügbar, wenn der standardmäßige Injektions Bereich verwendet wird.

Jeglicher Code, der explizit mehrere Threads parallel ausführt, sollte sicherstellen, dass auf `DbContext` Instanzen nicht gleichzeitig zugegriffen wird.

Mithilfe der Abhängigkeitsinjektion kann dies erreicht werden, indem der Kontext als Bereichs bezogen registriert wird und Bereiche (mit `IServiceScopeFactory` ) für jeden Thread erstellt werden, oder indem der `DbContext` als vorübergehender registriert wird (mithilfe der Überladung von `AddDbContext` , die einen- `ServiceLifetime` Parameter annimmt).

## <a name="more-reading"></a>Weitere Informationen

- Weitere Informationen zur Verwendung von di finden Sie unter [Abhängigkeitsinjektion](/aspnet/core/fundamentals/dependency-injection) .
- Weitere Informationen finden Sie unter [Testen](xref:core/miscellaneous/testing/index) .
