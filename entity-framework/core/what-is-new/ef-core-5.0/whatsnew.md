---
title: Neuerungen in EF Core 5.0
description: Übersicht über neue Features in EF Core 5.0
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 64b72ba2a6f752b9d71ea9b64ab08f4cf92ef03d
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129277"
---
# <a name="whats-new-in-ef-core-50"></a>Neuerungen in EF Core 5.0

Die folgende Liste enthält die wichtigsten neuen Features in EF Core 5.0. Eine vollständige Liste der Probleme in diesem Release finden Sie im [Issue-Tracker](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0).

Da es sich um ein Hauptrelease handelt, umfasst EF Core 5.0 auch zahlreiche [Breaking Changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), bei denen es sich um API-Verbesserungen oder Behavior Changes handelt, die sich negativ auf vorhandene Anwendungen auswirken können.

## <a name="many-to-many"></a>M:n

EF Core 5.0 unterstützt m:n-Beziehungen ohne explizite Zuordnung der Jointabelle.

Betrachten Sie beispielsweise die folgenden Entitätstypen:

```csharp
public class Post
{
    public int Id { get; set; }
    public string Name { get; set; }
    public ICollection<Tag> Tags { get; set; }
}

public class Tag
{
    public int Id { get; set; }
    public string Text { get; set; }
    public ICollection<Post> Posts { get; set; }
}
```

EF Core 5.0 erkennt dies gemäß der Konvention als m:n-Beziehung an und erstellt automatisch eine `PostTag`-Jointabelle in der Datenbank. Die Daten können ohne explizite Referenz der Jointabelle abgefragt und geändert werden, was den Code deutlich vereinfacht. Die Jointabelle kann weiterhin bei Bedarf angepasst und abgefragt werden.

Weitere Informationen finden Sie in der Dokumentation zu [m:n-Beziehungen](xref:core/modeling/relationships#many-to-many).

## <a name="split-queries"></a>Geteilte Abfragen

Ab EF Core 3.0 generiert EF Core für jede LINQ-Abfrage eine einzelne SQL-Abfrage. Dadurch wird sichergestellt, dass die zurückgegebenen Daten im Rahmen der Einschränkungen des verwendeten Transaktionsmodus konsistent sind. Wenn die Abfrage `Include` oder eine Projektion verwendet, um mehrere ähnliche Sammlungen zurückzugeben, kann die Ausführung jedoch sehr lange dauern.

In EF Core 5.0 ist es jetzt möglich, eine einzelne LINQ-Abfrage und die zugehörigen Sammlungen auf mehrere SQL-Abfragen aufzuteilen. Die Leistung kann dadurch zwar erheblich verbessert werden, doch bei den zurückgegebenen Ergebnissen kann es zu Inkonsistenzen kommen, wenn die Daten sich zwischen den beiden Abfragen ändern. Serialisierbare Transaktionen oder Momentaufnahmentransaktionen können verwendet werden, um das zu verhindern und Konsistenz bei aufgeteilten Abfragen zu erzielen. Dadurch kann jedoch die Leistung beeinträchtigt werden, und das Verhalten kann sich unterscheiden.

Gehen Sie von einer Abfrage aus, die mithilfe von `Include` zwei Ebenen ähnlicher Sammlungen einschließt:

```csharp
var artists = context.Artists
    .Include(e => e.Albums)
    .ToList();
```

Standardmäßig generiert EF Core den folgenden SQL-Wert, wenn der SQLite-Anbieter verwendet wird:

```sql
SELECT a."Id", a."Name", a0."Id", a0."ArtistId", a0."Title"
FROM "Artists" AS a
LEFT JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id", a0."Id"
```

Mit geteilten Abfragen wird stattdessen der folgende SQL-Code generiert:

```sql
SELECT a."Id", a."Name"
FROM "Artists" AS a
ORDER BY a."Id"

SELECT a0."Id", a0."ArtistId", a0."Title", a."Id"
FROM "Artists" AS a
INNER JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id"
```

Geteilte Abfragen können durch Platzieren des neuen `AsSplitQuery`-Operators an einer beliebigen Stelle in Ihrer LINQ-Abfrage oder global in der `OnConfiguring`-Methode Ihres Modells. Weitere Informationen finden Sie in der Dokumentation zu [Geteilten Abfragen](xref:core/querying/single-split-queries).

## <a name="simple-logging-and-improved-diagnostics"></a>Einfache Protokollierung und verbesserte Diagnose

Mit EF Core 5.0 wird eine einfache Möglichkeit zum Einrichten der Protokollierung über die neue `LogTo`-Methode eingeführt. Der folgende Code führt dazu, dass Protokollierungsmeldungen, einschließlich des gesamten von EF Core generierten SQL-Codes, in die Konsole geschrieben werden:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder.LogTo(Console.WriteLine);
```

Außerdem ist es jetzt möglich, `ToQueryString` für beliebige LINQ-Abfragen aufzurufen, um den SQL-Code abzurufen, den die Abfrage ausführen würde:

```csharp
Console.WriteLine(
    ctx.Artists
    .Where(a => a.Name == "Pink Floyd")
    .ToQueryString());
```

Letztlich wurden verschiedene EF Core-Typen mit einer erweiterten `DebugView`-Eigenschaft ausgestattet, die eine ausführliche Ansicht über interne Informationen bietet. Beispielsweise können Sie mit <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> überprüfen, welche Entitäten zu einem bestimmten Zeitpunkt nachverfolgt werden.

Weitere Informationen finden Sie in der Dokumentation zu [Protokollierung und Abfangfunktionen](xref:core/logging-events-diagnostics/index).

## <a name="filtered-include"></a>Gefilterte Include-Funktion

Die `Include`-Methode unterstützt nun das Filtern der enthaltenen Entitäten:

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

Mit dieser Abfrage werden Blogs zusammen mit den jeweiligen Beiträgen zurückgegeben, jedoch nur, wenn der Titel „Cheese“ enthält.

Weitere Informationen finden Sie in der Dokumentation zu [Geteilten Abfragen](xref:core/querying/related-data/eager#filtered-include).

## <a name="table-per-type-tpt-mapping"></a>TPT-Zuordnung (Tabelle pro Typ)

EF Core ordnet standardmäßig eine Vererbungshierarchie von .NET-Typen einer einzelnen Datenbanktabelle zu. Dies wird als TPH-Zuordnung (Tabelle pro Hierarchie) bezeichnet. EF Core 5.0 ermöglicht außerdem das Zuordnen der einzelnen .NET-Typen in einer Vererbungshierarchie zu einer anderen Datenbanktabelle. Dies wird als TPT-Zuordnung (Tabelle pro Typ) bezeichnet.

Stellen Sie sich beispielsweise das folgende Modell mit einer zugeordneten Hierarchie vor:

```csharp
public class Animal
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Cat : Animal
{
    public string EducationLevel { get; set; }
}

public class Dog : Animal
{
    public string FavoriteToy { get; set; }
}
```

Bei TPT wird eine Datenbanktabelle für jeden Typ in der Hierarchie erstellt:

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
);
```

Weitere Informationen finden Sie in der Dokumentation zu [TPT](xref:core/modeling/inheritance).

## <a name="flexible-entity-mapping"></a>Flexible Entitätszuordnung

Entitätstypen werden häufig Tabellen oder Sichten zugeordnet, sodass EF Core den Inhalt der Tabelle oder der Sicht zurückgibt, wenn dieser Typ abgefragt wird. Mit EF Core 5.0 wurden zusätzliche Zuordnungsoptionen hinzugefügt, mit denen eine Entität beispielsweise einer SQL-Abfrage (eine sogenannte „definierende Abfrage“) oder einer Tabellenwertfunktion (TVF) zugeordnet werden kann:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");

    modelBuilder.Entity<Blog>().ToFunction("BlogsReturningFunction");
}
```

Tabellenwertfunktionen können auch einer .NET-Methode anstelle einer DbSet-Klasse zugeordnet werden, sodass Parameter übergeben werden können. Die Zuordnung kann mithilfe von <xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A> eingerichtet werden.

Außerdem ist es nun möglich, eine Entität bei Abfragen einer Ansicht (oder einer Funktion oder definierenden Abfrage) zuzuordnen, jedoch ist die Zuordnung zu einer Tabelle beim Vornehmen von Änderungen nicht möglich:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

## <a name="shared-type-entity-types-and-property-bags"></a>Freigegebene Entitätstypen und Eigenschaftenbehälter

EF Core 5.0 ermöglicht die Zuordnung desselben CLR-Typs zu mehreren verschiedenen Entitätstypen. Diese Typen werden als freigegebene Entitätstypen bezeichnet. Während beliebige CLR-Typen mit diesem Feature verwendet werden können, bietet die .NET-Klasse `Dictionary` einen besonders überzeugenden Anwendungsfall, der als „Eigenschaftenbehälter“ bezeichnet wird.

```csharp
public class ProductsContext : DbContext
{
    public DbSet<Dictionary<string, object>> Products => Set<Dictionary<string, object>>("Product");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Product", b =>
        {
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
            b.IndexerProperty<decimal>("Price");
        });
    }
}
```

Diese Entitäten können dann wie normale Entitätstypen mit ihrem eigenen dedizierten CLR-Typ abgefragt und geändert werden. Weitere Informationen finden Sie in der Dokumentation zu [Eigenschaftenbehältern](xref:core/modeling/shadow-properties).

## <a name="required-11-dependents"></a>Erforderliche 1:1-Abhängigkeiten

Bei EF Core 3.1 war das abhängige Ende einer 1:1-Beziehung immer optional. Dies war insbesondere bei Verwendung von nicht eigenständigen Entitäten offensichtlich, da alle Spalten der nicht eigenständigen Entität in der Datenbank als NULL-Werte zulassend erstellt wurden, obwohl sie im Modell als erforderlich konfiguriert waren.

In EF Core 5.0 kann eine Navigation zu einer nicht eigenständigen Entität als erforderliche Abhängigkeit konfiguriert werden. Beispiel:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });
        b.Navigation(e => e.HomeAddress).IsRequired();
    });
}
```

## <a name="dbcontextfactory"></a>DbContextFactory

Mit EF Core 5.0 wurden `AddDbContextFactory` und `AddPooledDbContextFactory` zum Registrieren einer Factory zum Erstellen von DbContext-Instanzen im Dependency Injection-Container der Anwendung eingeführt. Dies kann sich als hilfreich erweisen, wenn Anwendungscode Kontextinstanzen manuell erstellen und löschen muss.

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

Zu diesem Zeitpunkt kann `IDbContextFactory<TContext>` in Anwendungsdienste wie ASP.NET Core-Controller eingefügt werden, was dann zum Instanziieren von Kontextinstanzen verwendet wird:

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
        => _contextFactory = contextFactory;

    public void DoSomeThing()
    {
        using (var context = _contextFactory.CreateDbContext())
        {
            // ...
        }
    }
}
```

Weitere Informationen finden Sie in der Dokumentation zu [DbContextFactory](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor).

## <a name="sqlite-table-rebuilds"></a>Neuerstellung von SQLite-Tabellen

Im Vergleich zu anderen Datenbanken ist SQLite hinsichtlich der Schemamanipulationsfunktionen relativ eingeschränkt, beispielsweise wird das Löschen einer vorhandenen Tabelle nicht unterstützt. EF Core 5.0 umgeht diese Einschränkungen, indem automatisch eine neue Tabelle erstellt wird, die Daten aus der alten Tabelle kopiert werden, die alte Tabelle gelöscht wird und die neue Tabelle umbenannt wird. Dadurch wird die Tabelle „neu erstellt“, und zuvor nicht unterstützte Migrationsvorgänge können dann sicher angewendet werden.

Ausführliche Informationen dazu, welche Migrationsvorgänge nun über Neuerstellungen von Tabellen unterstützt werden, finden Sie in [dieser Dokumentation](xref:core/providers/sqlite/limitations#migrations-limitations).

## <a name="database-collations"></a>Datenbanksortierungen

Mit EF Core 5.0 wird Unterstützung für das Festlegen von Textsortierungen auf Datenbank-, Spalten- oder Abfrageebene eingeführt. Dies ermöglicht die Konfiguration der Unterscheidung nach Groß-/Kleinschreibung und anderen Textaspekten, die flexibel ist und die Abfrageleistung nicht beeinträchtigt.

Mit dem folgenden Code wird die `Name`-Spalte so konfiguriert, dass sie die Groß-/Kleinschreibung bei SQL Server beachtet, und alle für die Spalten erstellten Indizes funktionieren entsprechend:

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("SQL_Latin1_General_CP1_CS_AS");
```

Weitere Informationen finden Sie in der Dokumentation zu [Sortierungen und die Beachtung der Groß-/Kleinschreibung](xref:core/miscellaneous/collations-and-case-sensitivity).

## <a name="event-counters"></a>Ereignisindikatoren

EF Core 5.0 stellt [Ereignisindikatoren](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0) zur Verfügung, die dazu verwendet werden können, die Leistung und verschiedene Anomalien Ihrer Anwendung zu überwachen. Fügen Sie einfach einen Prozess an, der Entity Framework mit dem Tool [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) ausführt:

```console
> dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496

[Microsoft.EntityFrameworkCore]
    Active DbContexts                                               1
    Execution Strategy Operation Failures (Count / 1 sec)           0
    Execution Strategy Operation Failures (Total)                   0
    Optimistic Concurrency Failures (Count / 1 sec)                 0
    Optimistic Concurrency Failures (Total)                         0
    Queries (Count / 1 sec)                                     1,755
    Queries (Total)                                            98,402
    Query Cache Hit Rate (%)                                      100
    SaveChanges (Count / 1 sec)                                     0
    SaveChanges (Total)                                             1
```

Weitere Informationen finden Sie in der Dokumentation zu [Ereignisindikatoren](xref:core/logging-events-diagnostics/event-counters).

## <a name="other-features"></a>Weitere Features

### <a name="model-building"></a>Modellerstellung

* Es wurden APIs für die Modellerstellung eingeführt, um die Konfiguration von [Wertvergleichen](xref:core/modeling/value-comparers) zu vereinfachen.
* Berechnete Spalten können nun als [*gespeichert* oder *virtuell*](xref:core/modeling/generated-properties#computed-columns) konfiguriert werden.
* Die Präzision und Skalierung kann nun [über die Fluent-API](xref:core/modeling/entity-properties#precision-and-scale) konfiguriert werden.
* Für [Navigationseigenschaften](xref:core/modeling/relationships#configuring-navigation-properties) wurden neue APIs für die Modellerstellung eingeführt.
* Für Felder wurden ähnlich wie für Eigenschaften neue APIs für die Modellerstellung eingeführt.
* Die .NET-Typen [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) und [IPAddress](/dotnet/api/system.net.ipaddress) können nun zu Zeichenfolgenspalten der Datenbank zugeordnet werden.
* Über [das neue `[BackingField]`-Attribut](xref:core/modeling/backing-field) kann nun eine Unterstützungsvariable konfiguriert werden.
* Unterstützungsvariablen, die NULL-Werte zulassen, sind nun zulässig, wodurch bessere Unterstützung für vom Speicher generierte Standardwerte bereitgestellt wird, wenn CLR-Standardwerte sich nicht als guter Sentinelwert eignen (insbesondere `bool`).
* Anstelle der Fluent-API kann nun ein neues `[Index]`-Attribut für einen Entitätstyp verwendet werden, um einen Index anzugeben.
* Ein neues `[Keyless]`-Attribut kann verwendet werden, um einen Entitätstyp so zu konfigurieren, [dass er über keinen Schlüssel verfügt](xref:core/modeling/keyless-entity-types).
* Standardmäßig [betrachtet EF Core Diskriminatoren nun als *vollständig*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration), d. h., es wird davon ausgegangen, dass nie Diskriminatorwerte vorliegen, die nicht von der Anwendung im Modell konfiguriert wurden. Dies ermöglicht einige Leistungsverbesserungen und kann deaktiviert werden, wenn Ihre Diskriminatorspalte unbekannte Werte enthalten kann.

### <a name="query"></a>Abfrage

* Ausnahmen für Fehler bei der Abfrageübersetzung enthalten nun explizitere Grüne für Fehler, um die Ermittlung des Problems zu vereinfachen.
* Abfragen ohne Nachverfolgung können nun die [Identitätsauflösung](xref:core/querying/tracking#identity-resolution) durchführen, um zu vermeiden, dass mehrere Entitätsinstanzen für dasselbe Datenbankobjekt zurückgegeben werden.
* Es wurde Unterstützung für GroupBy mit bedingten Aggregaten hinzugefügt (z. B. `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))`).
* Es wurde Unterstützung für die Übersetzung des Distinct-Operators über Gruppenelemente vor der Aggregation hinzugefügt.
* Die Übersetzung von [`Reverse`](/dotnet/api/system.linq.queryable.reverse) wurde hinzugefügt.
* Die Übersetzung bezüglich `DateTime` wurde für SQL Server verbessert (z. B. [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A), [`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A)).
* Die Übersetzung neuer Methoden in Bytearrays wurde hinzugefügt (z. B. [`Contains`](/dotnet/api/system.linq.enumerable.contains), [`Length`](/dotnet/api/system.array.length), [`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal)).
* Die Übersetzung von einigen zusätzlichen bitweisen Operatoren wurde hinzugefügt (z. B. das Zweierkomplement).
* Die Übersetzung von `FirstOrDefault` über Zeichenfolgen wurde hinzugefügt.
* Die Abfrageübersetzung bezüglich der NULL-Semantik wurde verbessert, wodurch effizientere Abfragen ermöglicht werden.
* Von Benutzern zugeordnete Funktionen können nun mit Anmerkungen versehen werden, um die NULL-Weitergabe zu steuern, was ebenfalls zu effizienteren Abfragen führt.
* SQL-Code mit CASE-Blöcken ist nun deutlich kürzer.
* Die SQL Server-Funktion [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql) kann nun über die neue Methode [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A) in Abfragen aufgerufen werden.
* `EnableDetailedErrors` fügt [zusätzliche Informationen zu Ausnahmen hinzu](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions).

### <a name="saving"></a>Speichern

* [Abfangen](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) und [Ereignisse](xref:core/logging-events-diagnostics/events) von SaveChanges
* Es wurden APIs zum Steuern von [Transaktionssicherungspunkten](xref:core/saving/transactions#savepoints) eingeführt. Außerdem erstellt EF Core automatisch einen Sicherungspunkt, wenn `SaveChanges` aufgerufen wird und bereits eine Transaktion ausgeführt wird. Im Falle eines Fehlers wird ein Rollback ausgeführt.
* Eine Transaktions-ID kann explizit von der Anwendung festgelegt werden, was eine einfachere Korrelation von Transaktionsereignissen bei der Protokollierung und in anderen Szenarios ermöglicht.
* Die standardmäßige maximale Batchgröße für SQL Server wurde basierend auf einer Analyse der Batchverarbeitungsleistung in „42“ geändert.

### <a name="migrations-and-scaffolding"></a>Migrationen und Gerüstbau

* Tabellen können nun [aus Migrationen ausgeschlossen werden](xref:core/modeling/entity-types#excluding-from-migrations).
* Ein neuer [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list)-Befehl zeigt an, welche Migrationen noch nicht auf die Datenbank angewendet wurden ([`Get-Migration`](xref:core/cli/powershell#get-migration) erzielt dasselbe in der Paketverwaltungskonsole).
* Migrationsskripts enthalten nun ggf.Transaktionsanweisungen, um die Verarbeitung von Fällen zu verbessern, in denen die Migrationsanwendung nicht erfolgreich ausgeführt wurde.
* Die Spalten für nicht zugeordnete Basisklassen sind nun nach anderen Spalten für zugeordnete Entitätstypen angeordnet. Beachten Sie, dass sich dies nur auf neu erstellte Tabellen auswirkt. Die Spaltenreihenfolge für vorhandene Tabellen bleibt unverändert.
* Die Migrationsgeneration kann nun erkennen, wenn die Migration als idempotent generiert wird und ob die Ausgabe sofort ausgeführt oder als Skript generiert wird.
* Es wurden neue Befehlszeilenparameter zum Festlegen von Namespaces in [Migrationen](xref:core/managing-schemas/migrations/index#namespaces) und im [Gerüstbau](xref:core/managing-schemas/scaffolding#directories-and-namespaces) hinzugefügt.
* Der Befehl [dotnet ef database update](xref:core/cli/dotnet#dotnet-ef-database-update) akzeptiert nun einen neuen `--connection`-Parameter zum Festlegen der Verbindungszeichenfolge.
* Der Gerüstbau für vorhandene Datenbanken formuliert Tabellennamen nun im Singular. Für Tabellen mit den Namen `People` und `Addresses` werden nun beispielsweise Entitätstypen namens `Person` und `Address` erstellt. [Die ursprünglichen Datenbanknamen können weiterhin beibehalten werden](xref:core/managing-schemas/scaffolding#preserving-names).
* Die neue Option [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) kann EF Core dazu anweisen, `OnModelConfiguring` beim Gerüstbau eines Modells auszuschließen.

### <a name="cosmos"></a>Cosmos

* Die [Cosmos-Verbindungseinstellungen](xref:core/providers/cosmos/index#cosmos-options) wurden erweitert.
* Die optimistische Nebenläufigkeit wird nun [über die Verwendung von ETags für Cosmos unterstützt](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags).
* Die neue `WithPartitionKey`-Methode ermöglicht, dass der Cosmos-[Partitionsschlüssel](xref:core/providers/cosmos/index#partition-keys) sowohl im Modell als auch in Abfragen enthalten sein kann.
* Die Zeichenfolgenmethoden [`Contains`](/dotnet/api/system.string.contains), [`StartsWith`](/dotnet/api/system.string.startswith) und [`EndsWith`](/dotnet/api/system.string.endswith) werden nun für Cosmos übersetzt.
* Der C#-Operator `is` wird nun für Cosmos übersetzt.

### <a name="sqlite"></a>SQLite

* Berechnete Spalten werden nun unterstützt.
* Das Abrufen von Binär- und Zeichenfolgendaten mit GetBytes, GetChars und GetTextReader ist nun effizienter durch die Verwendung von SqliteBlob und Datenströmen.
* Die Initialisierung von SqliteConnection ist nun verzögert.

### <a name="other"></a>Andere

* Nun können Proxys für die Änderungsnachverfolgung generiert werden, die automatisch [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) und [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) implementieren. Dies bietet einen alternativen Ansatz für die Änderungsnachverfolgung, die nicht nach Änderungen sucht, wenn `SaveChanges` aufgerufen wird.
* Eine <xref:System.Data.Common.DbConnection>-Klasse oder Verbindungszeichenfolge kann nun für eine bereits initialisierte DbContext-Instanz geändert werden.
* Die neue Methode <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType>.(#Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) löscht die DbContext-Instanzen aller überwachten Entitäten. Dies sollte normalerweise nicht erforderlich sein, wenn die bewährte Methode zum Erstellen einer neuen kurzlebigen Kontextinstanz für jede Arbeitseinheit angewandt wird. Wenn der Zustand einer DbContext-Instanz jedoch zurückgesetzt werden muss, ist die Verwendung der neuen `Clear()`-Methode effizienter und robuster als ein Vorgang zum Trennen aller Entitäten.
* Die Befehlszeilentools von EF Core konfigurieren nun die `ASPNETCORE_ENVIRONMENT`- _und_ `DOTNET_ENVIRONMENT`-Umgebungsvariablen automatisch als „Entwicklung“. Dadurch wird die Benutzeroberfläche bei der Verwendung des generischen Hosts mit der Benutzeroberfläche für die Entwicklung von ASP.NET Core in Einklang gebracht.
* Benutzerdefinierte Befehlszeilenargumente können nun an <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601> übertragen werden, sodass Anwendungen steuern können, wie der Kontext erstellt und initialisiert wird.
* Der Füllfaktor für den Index kann nun [in SQL Server konfiguriert](xref:core/providers/sql-server/indexes#fill-factor) werden.
* Die neue Eigenschaft <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A> kann dazu verwendet werden, zu unterscheiden, ob ein relationaler oder nicht relationaler Anbieter verwendet wird (z. B. InMemory).
