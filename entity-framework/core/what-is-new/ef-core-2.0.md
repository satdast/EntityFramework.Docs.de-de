---
title: Neue Features in EF Core 2.0 – EF Core
author: divega
ms.date: 02/20/2018
ms.assetid: 2CB5809E-0EFB-44F6-AF14-9D5BFFFBFF9D
uid: core/what-is-new/ef-core-2.0
ms.openlocfilehash: 83f6b819409d502dba17a678d44a0746a4a77f4b
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413576"
---
# <a name="new-features-in-ef-core-20"></a>Neue Features in EF Core 2.0

## <a name="net-standard-20"></a>.NET-Standard 2.0

Für EF Core wird nun die Zielversion für .NET Standard 2.0 festgelegt, was bedeutet, dass EF Core mit .NET Core 2.0, .NET Framework 4.6.1 und anderen Bibliotheken, die.NET Standard 2.0 implementieren, kompatibel ist.
Weitere Informationen zu unterstützten Implementierungen finden Sie unter [Unterstützte .NET-Implementierungen](../platforms/index.md).

## <a name="modeling"></a>Modellierung

### <a name="table-splitting"></a>Tabellenaufteilung

Nun können mindestens zwei Entitätstypen derselben Tabelle zugeordnet werden, wobei Primärschlüsselspalten gemeinsam genutzt werden und jede Zeile mindestens zwei Entitäten entspricht.

Für die Durchführung der Tabellenaufteilung muss eine identifizierende Beziehung (wobei die Fremdschlüsseleigenschaften den Primärschlüssel bilden) zwischen allen Entitätstypen konfiguriert werden, die die Tabelle gemeinsam nutzen:

``` csharp
modelBuilder.Entity<Product>()
    .HasOne(e => e.Details).WithOne(e => e.Product)
    .HasForeignKey<ProductDetails>(e => e.Id);
modelBuilder.Entity<Product>().ToTable("Products");
modelBuilder.Entity<ProductDetails>().ToTable("Products");
```

Weitere Informationen zu dieser Funktion finden Sie im [Abschnitt zur Tabellenaufteilung](xref:core/modeling/table-splitting).

### <a name="owned-types"></a>Eigene Typen

Ein eigener Entitätstyp kann denselben .NET-Typ mit einem anderen eigenen Entitätstyp verwenden, aber da dieser nicht allein durch den .NET-Typ identifiziert werden kann, muss eine Navigation von einem anderen Entitätstyp zu diesem erfolgen. Die Entität, die die definierende Navigation enthält, ist der Besitzer. Beim Abfragen des Besitzers werden standardmäßig eigene Typen eingeschlossen.

Gemäß den Konventionen wird ein Schattenprimärschlüssel für den eigenen Typ erstellt und mithilfe der Tabellenaufteilung der gleichen Tabelle wie der des Besitzers zugeordnet. Dies ermöglicht die Verwendung von eigenen Typen, ähnlich wie bei den in EF 6 verwendeten komplexen Typen:

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, cb =>
    {
        cb.OwnsOne(c => c.BillingAddress);
        cb.OwnsOne(c => c.ShippingAddress);
    });

public class Order
{
    public int Id { get; set; }
    public OrderDetails OrderDetails { get; set; }
}

public class OrderDetails
{
    public StreetAddress BillingAddress { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}

public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}
```

Weitere Informationen zu dieser Funktion finden Sie im [Abschnitt zu Entitätstypen im Besitz](xref:core/modeling/owned-entities).

### <a name="model-level-query-filters"></a>Abfragefilter auf Modellebene

EF Core 2.0 beinhaltet ein neues Feature, das als Abfragefilter auf Modellebene bezeichnet wird. Dieses Feature ermöglicht es, LINQ-Abfrageprädikate (einen booleschen Ausdruck, der typischerweise an den LINQ-Abfrageoperator „Where“ übergeben wird) in direktem Zusammenhang mit Entitätstypen im Metadatenmodell (normalerweise in OnModelCreating) zu definieren. Solche Filter werden automatisch auf alle diese Entitätstypen betreffenden LINQ-Abfragen (z.B. indirekt referenzierte Entitätstypen) angewendet, beispielsweise durch include-Verweise oder direkte Verweise auf Navigationseigenschaften. Zu den häufigsten Anwendungsfällen dieses Features zählen Folgende:

- Vorläufiges Löschen – Ein Entitätstyp definiert eine IsDeleted-Eigenschaft.
- Mehrinstanzenfähigkeit – Ein Entitätstyp definiert eine TenantId-Eigenschaft.

Im Folgenden wird ein einfaches Beispiel vorgestellt, das das Feature für die zwei oben genannten Szenarien veranschaulicht:

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    public int TenantId { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>().HasQueryFilter(
            p => !p.IsDeleted
            && p.TenantId == this.TenantId);
    }
}
```

Wir definieren einen Filter auf Modellebene, der Mehrinstanzenfähigkeit sowie das vorläufige Löschen für Instanzen des Entitätstyps `Post` implementiert. Beachten Sie die Verwendung einer `TenantId`-Eigenschaft auf der `DbContext`-Instanzebene. Filter auf Modellebene verwenden den Wert der korrekten Kontextinstanz (d.h. der Kontextinstanz, die die Abfrage ausführt).

Filter können für einzelne LINQ-Abfragen mit dem IgnoreQueryFilters()-Operator deaktiviert werden.

#### <a name="limitations"></a>Einschränkungen

- Navigationsverweise sind unzulässig. Dieses Feature kann basierend auf Feedback hinzugefügt werden.
- Filter können nur im Stammentitätstyp einer Hierarchie definiert werden.

### <a name="database-scalar-function-mapping"></a>Zuordnung von Skalarfunktionen für Datenbanken

EF Core 2.0 enthält einen wichtigen Beitrag von [Paul Middleton](https://github.com/pmiddleton), der die Zuordnung von Skalarfunktionen für Datenbanken zu Methodenstubs ermöglicht, sodass sie in LINQ-Abfragen verwendet und in SQL übersetzt werden können.

Im Folgenden wird kurz beschrieben, wie das Feature verwendet werden kann:

Deklarieren Sie eine statische Methode für den Typ `DbContext`, und kommentieren Sie ihn mit `DbFunctionAttribute`:

``` csharp
public class BloggingContext : DbContext
{
    [DbFunction]
    public static int PostReadCount(int blogId)
    {
        throw new NotImplementedException();
    }
}
```

Derartige Methoden werden automatisch registriert. Nach der Registrierung können Aufrufe der Methode in eine LINQ-Abfrage zu Funktionsaufrufen in SQL übersetzt werden:

``` csharp
var query =
    from p in context.Posts
    where BloggingContext.PostReadCount(p.Id) > 5
    select p;
```

Im Folgenden einige Hinweise, die Sie beachten sollten:

- Der Name der Methode wird gemäß den Konventionen als Name einer Funktion (in diesem Fall einer benutzerdefinierten Funktion) bei der SQL-Generierung verwendet, Sie können den Namen und das Schema bei der Methodenregistrierung jedoch überschreiben.
- Derzeit werden nur Skalarfunktionen unterstützt.
- Sie müssen die zugeordnete Funktion in der Datenbank erstellen. EF Core-Migrationen erstellen diese nicht.

### <a name="self-contained-type-configuration-for-code-first"></a>Eigenständige Typkonfiguration für den Code First

In EF 6 war es möglich, die Code First-Konfiguration eines bestimmten Entitätstyps durch Ableitung von *EntityTypeConfiguration* zu kapseln. In EF Core 2.0 wird dieses Muster wieder eingeführt:

``` csharp
class CustomerConfiguration : IEntityTypeConfiguration<Customer>
{
    public void Configure(EntityTypeBuilder<Customer> builder)
    {
        builder.HasKey(c => c.AlternateKey);
        builder.Property(c => c.Name).HasMaxLength(200);
    }
}

...
// OnModelCreating
builder.ApplyConfiguration(new CustomerConfiguration());
```

## <a name="high-performance"></a>Leistung

### <a name="dbcontext-pooling"></a>DbContext-Pooling

Das grundlegende Muster für die Verwendung von EF Core in einer ASP.NET Core-Anwendung besteht in der Regel darin, einen benutzerdefinierten DbContext-Typ im Abhängigkeitsinjektionssystem zu registrieren und später Instanzen dieses Typs über Konstruktorparameter in Controllern abzurufen. Das bedeutet, dass für jede Anforderung eine neue DbContext-Instanz erstellt wird.

In Version 2.0 stellen wir eine neue Möglichkeit vor, um benutzerdefinierte DbContext-Typen im Abhängigkeitsinjektionssystem zu registrieren, in dem transparent ein Pool von wiederverwendbaren DbContext-Instanzen eingeführt wird. Um DbContext-Pooling zu verwenden, verwenden Sie bei der Dienstregistrierung `AddDbContextPool` anstelle von `AddDbContext`:

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

Wird diese Methode verwendet, wird beim Anfordern einer DbContext-Instanz durch einen Controller zunächst geprüft, ob eine Instanz im Pool vorhanden ist. Sobald die Anforderungsverarbeitung abgeschlossen ist, werden alle Zustände der Instanz zurückgesetzt, und die Instanz selbst wird an den Pool zurückgegeben.

Dies ist konzeptionell vergleichbar mit der Funktionsweise des Verbindungspoolings bei ADO.NET-Anbietern und bietet den Vorteil, dass ein Teil der Initialisierungskosten der DbContext-Instanz eingespart werden kann.

### <a name="limitations"></a>Einschränkungen

Bei der neuen Methode werden einige Einschränkungen zu den Vorgängen in der `OnConfiguring()`-Methode des DbContext-Typs eingeführt.

> [!WARNING]  
> Verwenden Sie kein DbContext-Pooling, wenn Sie Ihren eigenen Zustand (z.B. private Felder) in Ihrer abgeleiteten DbContext-Klasse verwalten, da dieser nicht für alle Anforderungen genutzt werden sollte. EF Core setzt nur den Zustand zurück, der bekannt ist, bevor eine DbContext-Instanz zum Pool hinzugefügt wird.

### <a name="explicitly-compiled-queries"></a>Explizit kompilierte Abfragen

Dies ist das zweite optionale Leistungsfeature, das vor allem für die Verwendung in Szenarios mit umfangreicher Skalierung optimiert wurde.

Manuell oder explizit kompilierte Abfrage-APIs waren in vorherigen EF-Versionen und auch in LINQ to SQL verfügbar. So können Anwendungen die Übersetzung von Abfragen zwischenspeichern, sodass sie nur einmal berechnet und mehrmals ausgeführt werden können.

Obwohl EF Core generell auch automatisch Abfragen basierend auf einer verschlüsselten Darstellung von Abfrageausdrücken kompilieren und zwischenspeichern kann, kann mithilfe dieses Mechanismus ein geringfügiger Leistungsgewinn erzielt werden, indem die Berechnung des Hashwerts sowie die Cachesuche umgangen werden. Dies ermöglicht die Verwendung einer bereits kompilierten Abfrage in der Anwendung durch Aufrufen eines Delegaten.

``` csharp
// Create an explicitly compiled query
private static Func<CustomerContext, int, Customer> _customerById =
    EF.CompileQuery((CustomerContext db, int id) =>
        db.Customers
            .Include(c => c.Address)
            .Single(c => c.Id == id));

// Use the compiled query by invoking it
using (var db = new CustomerContext())
{
   var customer = _customerById(db, 147);
}
```

## <a name="change-tracking"></a>Change Tracking

### <a name="attach-can-track-a-graph-of-new-and-existing-entities"></a>Nachverfolgen eines Graphen von neuen und vorhandenen Entitäten durch Anfügen

EF Core unterstützt die automatische Generierung von Schlüsselwerten durch eine Vielzahl von Mechanismen. Bei Verwendung dieses Features wird ein Wert generiert, wenn die Schlüsseleigenschaft der CLR-Standardwert ist (normalerweise Null). Das bedeutet, dass ein Graph von Entitäten an `DbContext.Attach` oder `DbSet.Attach` übergeben werden kann und EF Core Entitäten markiert, die bereits auf `Unchanged` festgelegt wurden, während Entitäten ohne Schlüsselsatz mit `Added` markiert werden. Dies vereinfacht das Anfügen eines Graphen mit gemischten neuen und bestehenden Entitäten, wenn generierte Schlüssel verwendet werden. `DbContext.Update` und `DbSet.Update` funktionieren auf die gleiche Weise, außer dass Entitäten mit dem Schlüsselsatz `Modified` statt `Unchanged` markiert sind.

## <a name="query"></a>Abfrage

### <a name="improved-linq-translation"></a>Verbesserte LINQ-Übersetzung

Dies ermöglicht die erfolgreiche Ausführung von weiteren Abfragen, wobei ein größerer Teil der Logik in der Datenbank ausgewertet wird (statt im Speicher) und weniger Daten unnötig aus der Datenbank abgerufen werden.

### <a name="groupjoin-improvements"></a>GroupJoin-Verbesserungen

Durch diese Änderungen wird das SQL-Skript verbessert, das für Gruppenverknüpfungen generiert wird. Gruppenverknüpfungen sind oftmals das Ergebnis von Unterabfragen zu optionalen Navigationseigenschaften.

### <a name="string-interpolation-in-fromsql-and-executesqlcommand"></a>Zeichenfolgeninterpolation in FromSql und ExecuteSqlCommand

Mit C# 6 wurde Zeichenfolgeninterpolation eingeführt, ein Feature, mit dem C#-Ausdrücke direkt in Zeichenfolgenliterale eingebettet werden können, was eine komfortable Möglichkeit zum Erstellen von Zeichenfolgen zur Runtime bietet. In EF Core 2.0 haben wir eine spezielle Unterstützung für interpolierte Zeichenfolgen zu unseren zwei primären APIs hinzugefügt, die SQL-Rohzeichenfolgen akzeptieren: `FromSql` und `ExecuteSqlCommand`. Diese neue Unterstützung ermöglicht die „sichere“ Verwendung von C#-Zeichenfolgeninterpolation. Das heißt, sie wird vor häufigen Fehlern durch Angriffe durch Einschleusung von SQL-Befehlen geschützt, die auftreten können, wenn SQL dynamisch zur Laufzeit erstellt wird.

Beispiel:

``` csharp
var city = "London";
var contactTitle = "Sales Representative";

using (var context = CreateContext())
{
    context.Set<Customer>()
        .FromSql($@"
            SELECT *
            FROM ""Customers""
            WHERE ""City"" = {city} AND
                ""ContactTitle"" = {contactTitle}")
            .ToArray();
  }
```

In diesem Beispiel sind zwei Variablen in die SQL-Formatzeichenfolge eingebettet. EF Core erzeugt die folgende SQL-Syntax:

```sql
@p0='London' (Size = 4000)
@p1='Sales Representative' (Size = 4000)

SELECT *
FROM ""Customers""
WHERE ""City"" = @p0
    AND ""ContactTitle"" = @p1
```

### <a name="effunctionslike"></a>EF.Functions.Like()

Wir haben die EF.Functions-Eigenschaft hinzugefügt, mit denen EF Core oder Anbieter Methoden definieren können, die Datenbankfunktionen oder -operatoren zuordnen, sodass diese in LINQ-Abfragen aufgerufen werden können. Das erste Beispiel einer solchen Methode ist „Like()“:

``` csharp
var aCustomers =
    from c in context.Customers
    where EF.Functions.Like(c.Name, "a%")
    select c;
```

Beachten Sie, dass „Like()“ über eine In-Memory-Implementierung verfügt, die für die Arbeit mit einer In-Memory Database oder bei der Auswertung des Prädikats auf der Clientseite auftreten muss.

## <a name="database-management"></a>Datenbankverwaltung

### <a name="pluralization-hook-for-dbcontext-scaffolding"></a>Pluralisierungshook für den DbContext-Gerüstbau

In EF Core 2.0 wird ein neuer *IPluralizer*-Dienst eingeführt, mit dem Entitätstypnamen in den Singular und DbSet-Namen in den Plural gesetzt werden. Die Standardimplementierung ist eine No-Op-Bedingung. Somit ist dies lediglich ein Hook, mit dem Benutzer mühelos eigene Pluralisierer verbinden können.

Im Folgenden wird gezeigt, wie es für einen Entwickler aussieht, wenn er seinen eigenen Pluralisierer als Hook integriert:

``` csharp
public class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
    {
        services.AddSingleton<IPluralizer, MyPluralizer>();
    }
}

public class MyPluralizer : IPluralizer
{
    public string Pluralize(string name)
    {
        return Inflector.Inflector.Pluralize(name) ?? name;
    }

    public string Singularize(string name)
    {
        return Inflector.Inflector.Singularize(name) ?? name;
    }
}
```

## <a name="others"></a>Andere

### <a name="move-adonet-sqlite-provider-to-sqlitepclraw"></a>Migrieren des ADO.NET SQLite-Anbieters nach SQLitePCL.raw

Dies bietet uns eine zuverlässigere Lösung in „Microsoft.Data.Sqlite“ für die Verteilung von nativen SQLite-Binärdateien auf verschiedene Plattformen.

### <a name="only-one-provider-per-model"></a>Verwenden von nur einem Anbieter pro Modell

Dies erweitert erheblich die Möglichkeiten der Anbieter bezüglich der Interaktion mit dem Modell und vereinfacht die Verwendung von Konventionen, Anmerkungen und Fluent-APIs mit verschiedenen Anbietern.

EF Core 2.0 erstellt nun eine andere [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs)-Klasse für die verschiedenen verwendeten Anbieter. Dies geschieht in der Regel auf eine für die Anwendung transparente Weise. Dies hat eine Vereinfachung der untergeordneten Metadaten-APIs ermöglicht, sodass Zugriffe auf *gemeinsame relationale Metadatenkonzepte* immer durch Aufrufen von `.Relational` statt `.SqlServer`, `.Sqlite` usw. erfolgen.

### <a name="consolidated-logging-and-diagnostics"></a>Konsolidierte Protokollierung und Diagnose

Die Mechanismen zur Protokollierung (basierend auf ILogger) und Diagnose (basierend auf DiagnosticSource) nutzen nun weitere Codes gemeinsam.

Die Ereignis-IDs für an eine ILogger-Schnittstelle gesendete Nachrichten haben sich in 2.0 geändert. Die Ereignis-IDs sind nun für alle EF Core-Codes eindeutig. Diese Nachrichten entsprechen nun auch dem Standardmuster für die strukturierte Protokollierung, das z.B. von MVC eingesetzt wird.

Die Protokollierungskategorien haben sich ebenfalls geändert. Nun gibt es eine bekannte Gruppe von Kategorien, auf die über [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs) zugegriffen werden kann.

DiagnosticSource-Ereignisse verwenden nun dieselbe Ereignis-ID-Namen wie die der entsprechenden `ILogger`-Nachrichten.
