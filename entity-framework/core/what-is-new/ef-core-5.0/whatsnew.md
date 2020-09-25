---
title: Neuerungen in EF Core 5.0
description: Übersicht über neue Features in EF Core 5.0
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 0605d021b46066c6af7b631c99e86c0e53caa8db
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070756"
---
# <a name="whats-new-in-ef-core-50"></a>Neuerungen in EF Core 5.0

Alle für EF Core 5.0 geplanten Features sind jetzt verfügbar. Diese Seite bietet eine Übersicht über interessante Änderungen in den einzelnen Vorschauversionen.

Auf dieser Seite wird der [Plan für EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan) nicht erneut aufgeführt. Der Plan beschreibt die allgemeinen Themen für EF Core 5.0 einschließlich sämtlicher Features, die wir vor Auslieferung des finalen Releases integrieren möchten.

## <a name="rc1"></a>RC1

### <a name="many-to-many"></a>M:n

EF Core 5.0 unterstützt m:n-Beziehungen ohne explizite Zuordnung der Jointabelle.

Betrachten Sie beispielsweise die folgenden Entitätstypen:

```C#
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

`Post` enthält eine `Tags`-Auflistung, und `Tag` enthält eine `Posts`-Auflistung. EF Core 5.0 erkennt dies gemäß Konvention als m:n-Beziehung. Das bedeutet, dass in `OnModelCreating` kein Code erforderlich ist:

```C#
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Blog> Blogs { get; set; }
}
```

Wenn zum Erstellen der Datenbank Migrationen (oder `EnsureCreated`) verwendet werden, erstellt EF Core automatisch die Jointabelle. Beispielsweise generiert EF Core unter SQL Server für dieses Modell Folgendes:

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id])
);

CREATE TABLE [Tag] (
    [Id] int NOT NULL IDENTITY,
    [Text] nvarchar(max) NULL,
    CONSTRAINT [PK_Tag] PRIMARY KEY ([Id])
);

CREATE TABLE [PostTag] (
    [PostsId] int NOT NULL,
    [TagsId] int NOT NULL,
    CONSTRAINT [PK_PostTag] PRIMARY KEY ([PostsId], [TagsId]),
    CONSTRAINT [FK_PostTag_Posts_PostsId] FOREIGN KEY ([PostsId]) REFERENCES [Posts] ([Id]) ON DELETE CASCADE,
    CONSTRAINT [FK_PostTag_Tag_TagsId] FOREIGN KEY ([TagsId]) REFERENCES [Tag] ([Id]) ON DELETE CASCADE
);

CREATE INDEX [IX_PostTag_TagsId] ON [PostTag] ([TagsId]);
```

Wenn `Blog`- und `Post`-Entitäten erstellt und zugeordnet werden, wird die Jointabelle automatisch aktualisiert. Zum Beispiel:

```C#
var beginnerTag = new Tag {Text = "Beginner"};
var advancedTag = new Tag {Text = "Advanced"};
var efCoreTag = new Tag {Text = "EF Core"};

context.AddRange(
    new Post {Name = "EF Core 101", Tags = new List<Tag> {beginnerTag, efCoreTag}},
    new Post {Name = "Writing an EF database provider", Tags = new List<Tag> {advancedTag, efCoreTag}},
    new Post {Name = "Savepoints in EF Core", Tags = new List<Tag> {beginnerTag, efCoreTag}});

context.SaveChanges();
```

Nach dem Einfügen von Beiträgen und Tags erstellt EF automatisch Zeilen in der Jointabelle. Beispielsweise unter SQL Server:

```sql
SET NOCOUNT ON;
INSERT INTO [PostTag] ([PostsId], [TagsId])
VALUES (@p6, @p7),
(@p8, @p9),
(@p10, @p11),
(@p12, @p13),
(@p14, @p15),
(@p16, @p17);
```

Bei Abfragen funktionieren Include- und andere Abfragevorgänge wie bei jeder anderen Beziehung. Zum Beispiel:

```C#
foreach (var post in context.Posts.Include(e => e.Tags))
{
    Console.Write($"Post \"{post.Name}\" has tags");

    foreach (var tag in post.Tags)
    {
        Console.Write($" '{tag.Text}'");
    }
}
```

Die generierte SQL-Anweisung verwendet automatisch die Jointabelle, um alle zugehörigen Tags wiederherzustellen:

```sql
SELECT [p].[Id], [p].[Name], [t0].[PostsId], [t0].[TagsId], [t0].[Id], [t0].[Text]
FROM [Posts] AS [p]
LEFT JOIN (
    SELECT [p0].[PostsId], [p0].[TagsId], [t].[Id], [t].[Text]
    FROM [PostTag] AS [p0]
    INNER JOIN [Tag] AS [t] ON [p0].[TagsId] = [t].[Id]
) AS [t0] ON [p].[Id] = [t0].[PostsId]
ORDER BY [p].[Id], [t0].[PostsId], [t0].[TagsId], [t0].[Id]
```

Im Gegensatz zu EF6 kann bei EF Core die Jointabelle umfassend angepasst werden. Mit dem folgenden Code wird beispielsweise eine m:n-Beziehung konfiguriert, die auch Navigationen zur Joinentität aufweist und bei der die Joinentität eine Nutzdateneigenschaft enthält:

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Community>()
        .HasMany(e => e.Members)
        .WithMany(e => e.Memberships)
        .UsingEntity<PersonCommunity>(
            b => b.HasOne(e => e.Member).WithMany().HasForeignKey(e => e.MembersId),
            b => b.HasOne(e => e.Membership).WithMany().HasForeignKey(e => e.MembershipsId))
        .Property(e => e.MemberSince).HasDefaultValueSql("CURRENT_TIMESTAMP");
}
```

### <a name="map-entity-types-to-queries"></a>Zuordnen von Entitätstypen zu Abfragen

Entitätstypen werden häufig Tabellen oder Sichten zugeordnet, sodass EF Core den Inhalt der Tabelle oder der Sicht zurückgibt, wenn dieser Typ abgefragt wird. In EF Core 5.0 kann ein Entitätstyp einer „definierenden Abfrage“ zugeordnet werden. (Diese Funktion wurde in früheren Versionen teilweise unterstützt, wurde jedoch wesentlich verbessert und weist in EF Core 5.0 eine andere Syntax auf.)

Stellen Sie sich beispielsweise zwei Tabellen vor: eine mit modernen Beiträgen, die andere mit Legacybeiträgen. Die Tabelle mit modernen Beiträgen enthält einige zusätzliche Spalten. Für Ihre Anwendung sollen jedoch moderne Beiträge und Legacybeiträge vereint und mit allen erforderlichen Eigenschaften einem Entitätstyp zugeordnet werden:

```c#
public class Post
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Category { get; set; }
    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

In EF Core 5.0 kann `ToSqlQuery` verwendet werden, um diesen Entitätstyp einer Abfrage zuzuordnen, die Zeilen aus beiden Tabellen abruft und vereint:

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");
}
```

Die Tabelle `legacy_posts` enthält keine `Category`-Spalte. Daher wird stattdessen für alle Legacybeiträge ein Standardwert synthetisiert.

Dieser Entitätstyp kann dann auf übliche Weise für LINQ-Abfragen verwendet werden. Beispiel: LINQ-Abfrage:

```c#
var posts = context.Posts.Where(e => e.Blog.Name.Contains("Unicorn")).ToList();
```

Generiert den folgenden SQL-Code in SQLite:

```sql
SELECT "p"."Id", "p"."BlogId", "p"."Category", "p"."Name"
FROM (
    SELECT Id, Name, Category, BlogId FROM posts
    UNION ALL
    SELECT Id, Name, "Legacy", BlogId from legacy_posts
) AS "p"
INNER JOIN "Blogs" AS "b" ON "p"."BlogId" = "b"."Id"
WHERE ('Unicorn' = '') OR (instr("b"."Name", 'Unicorn') > 0)
```

Die für den Entitätstyp konfigurierte Abfrage dient als Ausgangspunkt für die Erstellung der gesamten LINQ-Abfrage.

### <a name="event-counters"></a>Ereignisindikatoren

Mit [.NET-Ereignisindikatoren](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) können Leistungsmetriken aus einer Anwendung effizient verfügbar gemacht werden. Bei EF Core 5.0 befinden sich die Ereignisindikatoren in der Kategorie `Microsoft.EntityFrameworkCore`. Zum Beispiel:

```
dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496
```

Damit werden dotnet-Indikatoren aufgefordert, mit der Sammlung von EF Core-Ereignissen für Prozess 49496 zu beginnen. Dadurch wird in der Konsole eine Ausgabe wie die Folgende generiert:

```
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

### <a name="property-bags"></a>Eigenschaftenbehälter

Bei EF Core 5.0 kann ein CLR-Typ verschiedenen Entitätstypen zugeordnet werden. Diese Typen werden als „Entitätstypen mit gemeinsamen Typen“ bezeichnet. Dieses Feature in Kombination mit Indexereigenschaften (in der Vorschauversion 1 enthalten) ermöglicht die Verwendung von Eigenschaftenbehältern als Entitätstyp.

Beispielsweise wird im Folgenden mit DbContext der BCL-Typ `Dictionary<string, object>` als Entitätstyp mit gemeinsamem Typ sowohl für Produkte als auch für Kategorien verwendet.

```c#
public class ProductsContext : DbContext
{
    public DbSet<Dictionary<string, object>> Products => Set<Dictionary<string, object>>("Product");
    public DbSet<Dictionary<string, object>> Categories => Set<Dictionary<string, object>>("Category");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Category", b =>
        {
            b.IndexerProperty<string>("Description");
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
        });

        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Product", b =>
        {
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
            b.IndexerProperty<string>("Description");
            b.IndexerProperty<decimal>("Price");
            b.IndexerProperty<int?>("CategoryId");

            b.HasOne("Category", null).WithMany();
        });
    }
}
```

Wörterbuchobjekte („Eigenschaftenbehälter“) können nun dem Kontext als Entitätsinstanzen hinzugefügt und gespeichert werden. Zum Beispiel:

```c#
var beverages = new Dictionary<string, object>
{
    ["Name"] = "Beverages",
    ["Description"] = "Stuff to sip on"
};

context.Categories.Add(beverages);

context.SaveChanges();
```

Diese Entitäten können dann auf übliche Weise abgefragt und aktualisiert werden:

```c#
var foods = context.Categories.Single(e => e["Name"] == "Foods");
var marmite = context.Products.Single(e => e["Name"] == "Marmite");

marmite["CategoryId"] = foods["Id"];
marmite["Description"] = "Yummy when spread _thinly_ on buttered Toast!";

context.SaveChanges();
```

### <a name="savechanges-interception-and-events"></a>SaveChanges-Interceptor und -Ereignisse

Mit EF Core 5.0 wurden .NET-Ereignisse und ein EF Core-Interceptor eingeführt, der beim Aufrufen von SaveChanges ausgelöst wird.

Die Ereignisse sind einfach zu verwenden. Zum Beispiel:

```c#
context.SavingChanges += (sender, args) =>
{
    Console.WriteLine($"Saving changes for {((DbContext)sender).Database.GetConnectionString()}");
};

context.SavedChanges += (sender, args) =>
{
    Console.WriteLine($"Saved {args.EntitiesSavedCount} changes for {((DbContext)sender).Database.GetConnectionString()}");
};
```

Beachten Sie Folgendes:
* Der Ereignissender ist die `DbContext`-Instanz.
* Die Argumente für das `SavedChanges`-Ereignis enthalten die Anzahl der in der Datenbank gespeicherten Entitäten.

Der Interceptor wird zwar durch `ISaveChangesInterceptor` definiert. Häufig ist es jedoch bequemer, wenn von `SaveChangesInterceptor` geerbt und so vermieden wird, dass jede einzelne Methode implementiert wird. Zum Beispiel:

```c#
public class MySaveChangesInterceptor : SaveChangesInterceptor
{
    public override InterceptionResult<int> SavingChanges(
        DbContextEventData eventData,
        InterceptionResult<int> result)
    {
        Console.WriteLine($"Saving changes for {eventData.Context.Database.GetConnectionString()}");

        return result;
    }

    public override ValueTask<InterceptionResult<int>> SavingChangesAsync(
        DbContextEventData eventData,
        InterceptionResult<int> result,
        CancellationToken cancellationToken = new CancellationToken())
    {
        Console.WriteLine($"Saving changes asynchronously for {eventData.Context.Database.GetConnectionString()}");

        return new ValueTask<InterceptionResult<int>>(result);
    }
}
```

Beachten Sie Folgendes:
* Der Interceptor arbeitet mit synchronen und asynchronen Methoden. Das kann nützlich sein, wenn Sie asynchrone E/A-Vorgänge wie Schreibvorgänge auf einem Überwachungsserver durchführen müssen.
* Mit dem Interceptor kann SaveChanges mithilfe des in allen Interceptors vorhandenen `InterceptionResult`-Mechanismus übersprungen werden.

Interceptors haben den Nachteil, dass sie bei der Erstellung bei DbContext registriert werden müssen. Zum Beispiel:

```c#
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .AddInterceptors(new MySaveChangesInterceptor())
            .UseSqlite("Data Source = test.db");
```

Im Gegensatz dazu können Ereignisse jederzeit in der DbContext-Instanz registriert werden.

### <a name="exclude-tables-from-migrations"></a>Ausschließen von Tabellen aus Migrationen

In manchen Fällen ist es sinnvoll, wenn ein einzelner Entitätstyp in mehreren DbContext-Objekten zugeordnet ist. Dies ist vor allem dann der Fall, wenn [Kontextgrenzen](https://www.martinfowler.com/bliki/BoundedContext.html) verwendet werden, bei denen üblicherweise für jede Kontextgrenze ein anderer DbContext-Typ verwendet wird.

So kann beispielsweise ein `User`-Typ von einem Autorisierungskontext und einem Berichtskontext benötigt werden. Wenn am `User`-Typ eine Änderung vorgenommen wird, wird bei der Migration für beide DbContext-Objekte versucht, die Datenbank zu aktualisieren. Um dies zu verhindern, kann das Modell für eines der Kontextobjekte so konfiguriert werden, dass die Tabelle aus der jeweiligen Migration ausgeschlossen wird.

Im folgenden Code generiert der `AuthorizationContext` Migrationen für Änderungen an der `Users`-Tabelle, der `ReportingContext` jedoch nicht. Dadurch wird verhindert, dass die Migrationen kollidieren.

```C#
public class AuthorizationContext : DbContext
{
    public DbSet<User> Users { get; set; }
}

public class ReportingContext : DbContext
{
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>().ToTable("Users", t => t.ExcludeFromMigrations());
    }
}
```

### <a name="required-11-dependents"></a>Erforderliche 1:1-Abhängigkeiten

Bei EF Core 3.1 war das abhängige Ende einer 1:1-Beziehung immer optional. Bei der Verwendung von nicht eigenständigen Entitäten wurde das besonders deutlich. Betrachten Sie beispielsweise das folgende Modell und die folgende Konfiguration:

```c#
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }

    public Address HomeAddress { get; set; }
    public Address WorkAddress { get; set; }
}

public class Address
{
    public string Line1 { get; set; }
    public string Line2 { get; set; }
    public string City { get; set; }
    public string Region { get; set; }
    public string Country { get; set; }
    public string Postcode { get; set; }
}
```

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.Line1).IsRequired();
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Region).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });

        b.OwnsOne(e => e.WorkAddress);
    });
}
```

Dies führt zu Migrationen, bei denen für SQLite die folgende Tabelle erstellt wird:

```sql
CREATE TABLE "People" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_People" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "HomeAddress_Line1" TEXT NULL,
    "HomeAddress_Line2" TEXT NULL,
    "HomeAddress_City" TEXT NULL,
    "HomeAddress_Region" TEXT NULL,
    "HomeAddress_Country" TEXT NULL,
    "HomeAddress_Postcode" TEXT NULL,
    "WorkAddress_Line1" TEXT NULL,
    "WorkAddress_Line2" TEXT NULL,
    "WorkAddress_City" TEXT NULL,
    "WorkAddress_Region" TEXT NULL,
    "WorkAddress_Country" TEXT NULL,
    "WorkAddress_Postcode" TEXT NULL
);
```

Beachten Sie, dass alle Spalten NULL-Werte zulassen, auch wenn einige der `HomeAddress`-Eigenschaften als erforderlich konfiguriert wurden. Wenn beim Abfragen einer `Person` alle Spalten für die private oder geschäftliche Adresse NULL sind, werden die `HomeAddress`- und/oder `WorkAddress`-Eigenschaften von EF Core als NULL belassen, anstatt eine leere Instanz von `Address` festzulegen.

Bei EF Core 5.0 kann die `HomeAddress`-Navigation nun als erforderliche Abhängigkeit konfiguriert werden. Zum Beispiel:

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.Line1).IsRequired();
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Region).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });
        b.Navigation(e => e.HomeAddress).IsRequired();

        b.OwnsOne(e => e.WorkAddress);
    });
}
```

Die von Migrationen erstellte Tabelle enthält jetzt Non-Nullable-Spalten für die erforderlichen Eigenschaften der erforderlichen Abhängigkeit:

```sql
CREATE TABLE "People" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_People" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "HomeAddress_Line1" TEXT NOT NULL,
    "HomeAddress_Line2" TEXT NULL,
    "HomeAddress_City" TEXT NOT NULL,
    "HomeAddress_Region" TEXT NOT NULL,
    "HomeAddress_Country" TEXT NULL,
    "HomeAddress_Postcode" TEXT NOT NULL,
    "WorkAddress_Line1" TEXT NULL,
    "WorkAddress_Line2" TEXT NULL,
    "WorkAddress_City" TEXT NULL,
    "WorkAddress_Region" TEXT NULL,
    "WorkAddress_Country" TEXT NULL,
    "WorkAddress_Postcode" TEXT NULL
);
```

Außerdem löst EF Core jetzt eine Ausnahme aus, wenn versucht wird, einen Besitzer zu speichern, der eine Abhängigkeit aufweist, die einen NULL-Wert erfordert. In diesem Beispiel löst EF Core eine Ausnahme aus, wenn versucht wird, eine `Person` mit einer `HomeAddress` mit einem NULL-Wert zu speichern.

Und schließlich erstellt EF Core weiterhin eine Instanz einer erforderlichen Abhängigkeit, auch wenn alle Spalten für die erforderliche Abhängigkeit NULL-Werte aufweisen.

### <a name="options-for-migration-generation"></a>Optionen für die Migrationserstellung

Ab EF Core 5.0 haben Sie eine bessere Kontrolle über die Erstellung von Migrationen für unterschiedliche Zwecke. Dazu gehören folgenden Funktionen:

* Erkennen, ob die Migration für ein Skript oder für eine sofortige Ausführung erstellt wird
* Erkennen, ob ein idempotentes Skript erstellt wird
* Erkennen, ob das Skript Transaktionsanweisungen ausschließen soll (Informationen hierzu finden Sie unter _Migrationsskripts bei Transaktionen_ weiter unten)

Dieses Verhalten wird durch eine `MigrationsSqlGenerationOptions`-Enumeration angegeben, die nun an `IMigrator.GenerateScript`übergeben werden kann.

Zudem ermöglicht diese Version bei Bedarf eine bessere Erstellung von idempotenten Skripts mit Aufrufen von `EXEC` unter SQL Server. Diese Version enthält darüber hinaus auch ähnliche Verbesserungen an den Skripts, die von anderen Datenbankanbietern wie PostgreSQL erstellt werden.

### <a name="migrations-scripts-with-transactions"></a>Migrationsskripts bei Transaktionen

Über Migrationen erstellte SQL-Skripts enthalten nun Anweisungen zum Starten und Committen von Transaktionen für die Migration. So wurde das folgende Migrationsskript beispielsweise über zwei Migrationen erstellt. Beachten Sie, dass nun jede Migration in einer Transaktion angewendet wird.

```sql
BEGIN TRANSACTION;
GO

CREATE TABLE [Groups] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Groups] PRIMARY KEY ([Id])
);
GO

CREATE TABLE [Members] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    [GroupId] int NULL,
    CONSTRAINT [PK_Members] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Members_Groups_GroupId] FOREIGN KEY ([GroupId]) REFERENCES [Groups] ([Id]) ON DELETE NO ACTION
);
GO

CREATE INDEX [IX_Members_GroupId] ON [Members] ([GroupId]);
GO

INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
VALUES (N'20200910194835_One', N'6.0.0-alpha.1.20460.2');
GO

COMMIT;
GO

BEGIN TRANSACTION;
GO

EXEC sp_rename N'[Groups].[Name]', N'GroupName', N'COLUMN';
GO

INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
VALUES (N'20200910195234_Two', N'6.0.0-alpha.1.20460.2');
GO

COMMIT;
```

Wie bereits im vorherigen Abschnitt erwähnt, kann diese Verwendung von Transaktionen deaktiviert werden, wenn Transaktionen anders behandelt werden müssen.

### <a name="see-pending-migrations"></a>Ausstehende Migrationen anzeigen

Dieses Feature stammt vom Communitymitglied [@Psypher9](https://github.com/Psypher9). Danke für diesen Beitrag!

Mit dem Befehl `dotnet ef migrations list` wird nun angezeigt, welche Migrationen noch nicht auf die Datenbank angewendet wurden. Zum Beispiel:

```
ajcvickers@avickers420u:~/AllTogetherNow/Daily$ dotnet ef migrations list
Build started...
Build succeeded.
20200910201647_One
20200910201708_Two
20200910202050_Three (Pending)
ajcvickers@avickers420u:~/AllTogetherNow/Daily$
```

Außerdem gibt es jetzt den Befehl `Get-Migration` für die Paket-Manager-Konsole mit derselben Funktionalität.

### <a name="modelbuilder-api-for-value-comparers"></a>ModelBuilder-API für Wertevergleiche

EF Core-Eigenschaften für benutzerdefinierte änderbare Typen [erfordern einen Wertevergleich](xref:core/modeling/value-comparers), damit Eigenschaftsänderungen richtig erkannt werden. Dies kann jetzt als Teil der Konfiguration der Wertkonvertierung für den Typ angegeben werden. Zum Beispiel:

```c#
modelBuilder
    .Entity<EntityType>()
    .Property(e => e.MyProperty)
    .HasConversion(
        v => JsonSerializer.Serialize(v, null),
        v => JsonSerializer.Deserialize<List<int>>(v, null),
        new ValueComparer<List<int>>(
            (c1, c2) => c1.SequenceEqual(c2),
            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
            c => c.ToList()));
```

### <a name="entityentry-trygetvalue-methods"></a>EntityEntry-TryGetValue-Methoden

Dieses Feature stammt vom Communitymitglied [@m4ss1m0g](https://github.com/m4ss1m0g). Danke für diesen Beitrag!

`EntityEntry.CurrentValues` und `EntityEntry.OriginalValues` wurde eine `TryGetValue`-Methode hinzugefügt. Dadurch kann der Wert einer Eigenschaft angefordert werden, ohne zuerst zu überprüfen, ob die Eigenschaft im EF-Modell zugeordnet ist. Zum Beispiel:

```c#
if (entry.CurrentValues.TryGetValue(propertyName, out var value))
{
    Console.WriteLine(value);
}
```

### <a name="default-max-batch-size-for-sql-server"></a>Standardwert für maximale Batchgröße bei SQL Server

Ab EF Core 5.0 beträgt der Standardwert für die maximale Batchgröße bei SaveChanges unter SQL Server nun 42. Bekanntlich ist dies auch die Antwort auf die ultimative Frage des Lebens, des Universums und alles Übrigen. Dies ist jedoch wahrscheinlich ein Zufall, da der Wert durch die [Analyse der Batchverarbeitungsleistung](https://github.com/dotnet/efcore/issues/9270) ermittelt wurde. Wir glauben nicht, dass wir eine Form der ultimativen Frage entdeckt haben, obwohl es einigermaßen plausibel erscheint, dass die Erde erschaffen wurde, um zu verstehen, warum SQL Server so funktioniert, wie es funktioniert.

### <a name="default-environment-to-development"></a>Standardumgebung für die Entwicklung

Die Befehlszeilentools von EF Core konfigurieren nun die `ASPNETCORE_ENVIRONMENT`- _und_ `DOTNET_ENVIRONMENT`-Umgebungsvariablen automatisch als „Entwicklung“. Dadurch wird die Benutzeroberfläche bei der Verwendung des generischen Hosts mit der Benutzeroberfläche für die Entwicklung von ASP.NET Core in Einklang gebracht. Informationen hierzu finden Sie unter [#19903](https://github.com/dotnet/efcore/issues/19903).

### <a name="better-migrations-column-ordering"></a>Bessere Anordnung der Spalten für Migrationen

Die Spalten für nicht zugeordnete Basisklassen sind nun nach anderen Spalten für zugeordnete Entitätstypen angeordnet. Dies betrifft jedoch nur neu erstellte Tabellen. Die Spaltenreihenfolge bei bereits vorhandenen Tabellen bleibt unverändert. Informationen hierzu finden Sie unter [#11314](https://github.com/dotnet/efcore/issues/11314).

### <a name="query-improvements"></a>Verbesserungen bei Abfragen

EF Core 5.0 RC1 umfasst einige zusätzliche Verbesserungen in Bezug auf die Abfrageübersetzung:

* Übersetzung von `is` in Cosmos. Informationen hierzu finden Sie unter [#16391](https://github.com/dotnet/efcore/issues/16391).
* Vom Benutzer zugeordnete Funktionen können nun zur Steuerung der NULL-Weitergabe mit Anmerkungen versehen werden. Informationen hierzu finden Sie unter [#19609](https://github.com/dotnet/efcore/issues/19609).
* Unterstützung für die Übersetzung von GroupBy mit bedingten Aggregaten. Informationen hierzu finden Sie unter [#11711](https://github.com/dotnet/efcore/issues/11711).
* Übersetzung des Distinct-Operators über Group-Element vor dem Aggregieren. Informationen hierzu finden Sie unter [#17376](https://github.com/dotnet/efcore/issues/17376).

### <a name="model-building-for-fields"></a>Modellerstellung für Felder

Und schließlich können mit EF Core RC1 im ModelBuilder nun Lambdamethoden sowohl für Felder als auch für Eigenschaften verwendet werden. Wenn Sie beispielsweise aus irgendeinem Grund keine Eigenschaften, sondern öffentliche Felder verwenden möchten, können diese Felder nun mithilfe der Lambda-Generatoren zugeordnet werden:

```c#
public class Post
{
    public int Id;
    public string Name;
    public string Category;
    public int BlogId;
    public Blog Blog;
}

public class Blog
{
    public int Id;
    public string Name;
    public ICollection<Post> Posts;
}
```

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(b =>
    {
        b.Property(e => e.Id);
        b.Property(e => e.Name);
    });

    modelBuilder.Entity<Post>(b =>
    {
        b.Property(e => e.Id);
        b.Property(e => e.Name);
        b.Property(e => e.Category);
        b.Property(e => e.BlogId);
        b.HasOne(e => e.Blog).WithMany(e => e.Posts);
    });
}
```

Obwohl dies nun möglich ist, wird diese Vorgehensweise nicht empfohlen. Zudem wird EF Core damit nicht um zusätzliche Feldzuordnungsfunktionen erweitert. Damit können lediglich anstelle von Zeichenfolgenmethoden Lambdamethoden verwendet werden. Dies ist nur in Ausnahmefällen nützlich, da Felder selten öffentlich sind.

## <a name="preview-8"></a>Preview 8

### <a name="table-per-type-tpt-mapping"></a>TPT-Zuordnung (Tabelle pro Typ)

EF Core ordnet standardmäßig eine Vererbungshierarchie von .NET-Typen einer einzelnen Datenbanktabelle zu. Dies wird als TPH-Zuordnung (Tabelle pro Hierarchie) bezeichnet. EF Core 5.0 ermöglicht außerdem das Zuordnen der einzelnen .NET-Typen in einer Vererbungshierarchie zu einer anderen Datenbanktabelle. Dies wird als TPT-Zuordnung (Tabelle pro Typ) bezeichnet.

Stellen Sie sich beispielsweise das folgende Modell mit einer zugeordneten Hierarchie vor:

```c#
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

public class Pet : Animal
{
    public string Name { get; set; }
}

public class Cat : Pet
{
    public string EducationLevel { get; set; }
}

public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

EF Core ordnet dieses standardmäßig einer einzelnen Tabelle zu:

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [Name] nvarchar(max) NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);
```

Allerdings resultiert die Zuordnung aller Entitätstypen zu anderen Tabellen zu einer Tabelle pro Typ:

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Pets] (
    [Id] int NOT NULL,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Pets] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Pets_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Cats_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Dogs_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);
```

Beachten Sie, dass die Erstellung der oben gezeigten Fremdschlüsseleinschränkungen nach der Verzweigung des Codes für Preview 8 hinzugefügt wurde.

Entitätstypen können mithilfe von Zuordnungsattributen verschiedenen Tabellen zugeordnet werden:

```c#
[Table("Animals")]
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

[Table("Pets")]
public class Pet : Animal
{
    public string Name { get; set; }
}

[Table("Cats")]
public class Cat : Pet
{
    public string EdcuationLevel { get; set; }
}

[Table("Dogs")]
public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

Alternativ kann die `ModelBuilder`-Konfiguration verwendet werden:

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

Die Dokumentation finden Sie im [Issue 1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).

### <a name="migrations-rebuild-sqlite-tables"></a>Migrationen: Neuerstellen von SQLite-Tabellen

Im Vergleich zu anderen Datenbanken ist SQLite in seinen Schemabearbeitungsfunktionen relativ eingeschränkt. Wenn Sie beispielsweise eine Spalte aus einer vorhandenen Tabelle löschen möchten, muss die gesamte Tabelle gelöscht und neu erstellt werden. EF Core 5.0-Migrationen unterstützen nun die automatische Neuerstellung der Tabelle für Schemaänderungen, die diese erfordern.

Angenommen, Sie verfügen über eine `Unicorns`-Tabelle, die für den Entitätstyp `Unicorn` erstellt wurde:

```c#
public class Unicorn
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

```sql
CREATE TABLE "Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "Age" INTEGER NOT NULL
);
```

Dann erfahren Sie jedoch, dass das Speichern des Alters eines Einhorns sehr unhöflich ist. Daher entfernen Sie diese Eigenschaft, fügen eine neue Migration hinzu und aktualisieren die Datenbank. Dieses Update schlägt bei Verwendung von EF Core 3.1 fehl, da die Spalte nicht gelöscht werden kann. In EF Core 5.0 wird die Tabelle durch Migrationen stattdessen neu erstellt:

```sql
CREATE TABLE "ef_temp_Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL
);

INSERT INTO "ef_temp_Unicorns" ("Id", "Name")
SELECT "Id", "Name"
FROM Unicorns;

PRAGMA foreign_keys = 0;

DROP TABLE "Unicorns";

ALTER TABLE "ef_temp_Unicorns" RENAME TO "Unicorns";

PRAGMA foreign_keys = 1;
```

Beachten Sie Folgendes:
* Eine temporäre Tabelle wird mit dem gewünschten Schema für die neue Tabelle erstellt.
* Daten werden aus der aktuellen Tabelle in die temporäre Tabelle kopiert.
* Die Fremdschlüsselerzwingung wird deaktiviert.
* Die aktuelle Tabelle wird gelöscht.
* Die temporäre Tabelle wird umbenannt, sodass sie als neue Tabelle fungiert.

Die Dokumentation finden Sie im [Issue 2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).

### <a name="table-valued-functions"></a>Tabellenwertfunktionen

Dieses Feature stammt vom Communitymitglied [@pmiddleton](https://github.com/pmiddleton). Danke für diesen Beitrag!

EF Core 5.0 umfasst erstklassige Unterstützung für die Zuordnung von .NET-Methoden zu Tabellenwertfunktionen. Diese Funktionen können dann in LINQ-Abfragen verwendet werden, wobei eine zusätzliche Komposition der Ergebnisse der Funktion ebenfalls in SQL übersetzt wird.

Sehen Sie sich das folgende Beispiel an, bei dem die Tabellenwertfunktionen in einer SQL Server-Datenbank definiert wurden:

```sql
CREATE FUNCTION GetReports(@employeeId int)
RETURNS @reports TABLE
(
    Name nvarchar(50) NOT NULL,
    IsDeveloper bit NOT NULL
)
AS
BEGIN
    WITH cteEmployees AS
    (
        SELECT Id, Name, ManagerId, IsDeveloper
        FROM Employees
        WHERE Id = @employeeId
        UNION ALL
        SELECT e.Id, e.Name, e.ManagerId, e.IsDeveloper
        FROM Employees e
        INNER JOIN cteEmployees cteEmp ON cteEmp.Id = e.ManagerId
    )
    INSERT INTO @reports
    SELECT Name, IsDeveloper
    FROM cteEmployees
    WHERE Id != @employeeId

    RETURN
END
```

Das EF Core-Modell erfordert zwei Entitätstypen, um diese Tabellenwertfunktionen zu verwenden:
* Ein `Employee`-Typ, der auf normale Weise der Tabelle „Employees“ zugeordnet wird
* Ein `Report`-Typ, der dem von der Tabellenwertfunktion zurückgegebenen Format entspricht

```c#
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }

    public int? ManagerId { get; set; }
    public virtual Employee Manager { get; set; }
}
```

```c#
public class Report
{
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }
}
```

Diese Typen müssen im EF Core-Modell enthalten sein:

```c#
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

Beachten Sie, dass `Report` über keinen Primärschlüssel verfügt und daher entsprechend konfiguriert werden muss.

Schließlich muss eine .NET-Methode der Tabellenwertfunktion in der Datenbank zugeordnet werden. Diese Methode kann mithilfe der neuen `FromExpression`-Methode in DbContext definiert werden:

```c#
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

Diese Methode verwendet einen Parameter und Rückgabetyp, die mit der oben definierten Tabellenwertfunktion übereinstimmen. Die Methode wird dann zum EF Core-Modell in OnModelCreating hinzugefügt:

```c#
modelBuilder.HasDbFunction(() => GetReports(default));
```

(Die Verwendung eines Lambdaausdrucks ist hier eine einfache Methode, um `MethodInfo` an EF Core zu übergeben. Die an die Methode übergebenen Argumente werden ignoriert.)

Sie können nun Abfragen schreiben, die `GetReports` aufrufen und die Ergebnisse überschreiben. Zum Beispiel:

```c#
from e in context.Employees
from rc in context.GetReports(e.Id)
where rc.IsDeveloper == true
select new
{
  ManagerName = e.Name,
  EmployeeName = rc.Name,
})
```

In SQL Server funktioniert dies wie folgt:

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

Beachten Sie, dass der in der `Employees`-Tabelle verankerte SQL-Code `GetReports` aufruft und dann eine zusätzliche WHERE-Klausel für die Ergebnisse der Funktion hinzufügt.

### <a name="flexible-queryupdate-mapping"></a>Flexible Zuordnung von Abfragen und Updates

EF Core 5.0 ermöglicht das Zuordnen desselben Entitätstyps zu verschiedenen Datenbankobjekten. Bei diesen Objekten kann es sich um Tabellen, Ansichten oder Funktionen handeln.

Ein Entitätstyp kann beispielsweise sowohl einer Datenbankansicht als auch einer Datenbanktabelle zugeordnet werden:

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

EF Core führt Abfragen dann standardmäßig über die Ansicht aus und sendet Updates an die Tabelle. Angenommen, der folgende Code wird ausgeführt:

```c#
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

Dies resultiert in einer Abfrage der Ansicht und einem anschließenden Update der Tabelle:

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a>Kontextbezogene Konfiguration von geteilten Abfragen

Geteilte Abfragen (siehe unten) können nun als Standardabfragen konfiguriert werden, die von DbContext ausgeführt werden. Diese Konfiguration ist nur für relationale Anbieter verfügbar und muss daher im Rahmen der `UseProvider`-Konfiguration festgelegt werden. Zum Beispiel:

```c#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

Die Dokumentation finden Sie im [Issue 2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).

### <a name="physicaladdress-mapping"></a>Zuordnung von PhysicalAddress

Dieses Feature stammt vom Communitymitglied [@ralmsdeveloper](https://github.com/ralmsdeveloper). Danke für diesen Beitrag!

Die neue .NET Standard-Klasse [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) wird jetzt automatisch einer Zeichenfolgenspalte für Datenbanken zugeordnet, die noch nicht nativ unterstützt werden. Weitere Informationen finden Sie unten in den Beispielen zu `IPAddress`.

## <a name="preview-7"></a>Preview 7

### <a name="dbcontextfactory"></a>DbContextFactory

EF Core 5.0 führt `AddDbContextFactory` und `AddPooledDbContextFactory` ein, um eine Zuordnungsinstanz zum Erstellen von DbContext-Instanzen im Abhängigkeitsinjektionscontainer der Anwendung zu registrieren. Zum Beispiel:

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

Anwendungsdienste wie ASP.NET Core-Controller können dann von `IDbContextFactory<TContext>` im Dienstkonstruktor abhängen. Zum Beispiel:

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

DbContext-Instanzen können dann nach Bedarf erstellt und verwendet werden. Zum Beispiel:

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

Beachten Sie, dass die DbContext-Instanzen, die auf diese Weise erstellt werden, _nicht_ vom Dienstanbieter der Anwendung verwaltet werden, weshalb diese von der Anwendung gelöscht werden müssen. Diese Entkopplung ist für Blazor-Anwendungen sehr nützlich, bei denen die Verwendung von `IDbContextFactory` empfohlen wird. Sie kann sich aber auch in anderen Szenarios als nützlich erweisen.

DbContext-Instanzen können durch Aufrufe von `AddPooledDbContextFactory` gepoolt werden. Dieses Pooling funktioniert auf dieselbe Weise wie `AddDbContextPool` und weist dieselben Einschränkungen auf.

Die Dokumentation finden Sie im [Issue 2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).

### <a name="reset-dbcontext-state"></a>Zurücksetzen des DbContext-Zustands

EF Core 5.0 führt `ChangeTracker.Clear()` ein, wodurch die DbContext-Instanzen aller überwachten Entitäten gelöscht werden. Dies sollte normalerweise nicht erforderlich sein, wenn die bewährte Methode zum Erstellen einer neuen kurzlebigen Kontextinstanz für jede Arbeitseinheit angewandt wird. Allerdings ist die Verwendung der neuen `Clear()`-Methode leistungsfähiger und robuster als das Trennen aller Entitäten in Massen, wenn der Zustand einer DbContext-Instanz nicht zurückgesetzt werden muss.

Die Dokumentation finden Sie im [Issue 2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).

### <a name="new-pattern-for-store-generated-defaults"></a>Neues Muster für vom Speicher generierte Standardwerte

EF Core lässt das Festlegen eines expliziten Werts für eine Spalte zu, die auch eine Standardwerteinschränkung aufweisen kann. EF Core verwendet hierfür den CLR-Standardwert für den Eigenschaftentyp als Schutzmechanismus. Wenn es sich bei dem Wert nicht um den CLR-Standardwert handelt, wird dieser eingefügt, andernfalls wird der Standardwert der Datenbank verwendet.

Dies führt zu Problemen bei Typen, bei denen der CLR-Standardwert kein guter Schutzmechanismus ist. Insbesondere `bool`-Eigenschaften sind dafür bekannt. EF Core 5.0 lässt nun zu, dass das Unterstützungsfeld in solchen Fällen NULL-Werte zulässt. Zum Beispiel:

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

Beachten Sie, dass das Unterstützungsfeld NULL-Werte zulässt, die öffentlich zur Verfügung gestellte Eigenschaft jedoch nicht. Daher kann der Sentinelwert `null` sein, ohne dass sich dies auf die öffentliche Oberfläche des Entitätstyps auswirkt. Wenn `IsValid` in diesem Fall nie festgelegt wird, wird der Standardwert der Datenbank verwendet, da das Unterstützungsfeld weiterhin NULL ist. Wenn weder `true` noch `false` festgelegt werden, wird dieser Wert explizit in der Datenbank gespeichert.

Die Dokumentation finden Sie im [Issue 2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).

### <a name="cosmos-partition-keys"></a>Cosmos-Partitionsschlüssel

EF Core lässt das Einschließen des Cosmos-Partitionsschlüssels im EF-Modell zu. Zum Beispiel:

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

Ab Preview 7 ist der Partitionsschlüssel im primären Schlüssel des Entitätstyps enthalten und wird zur Verbesserung der Leistung einiger Abfragen verwendet.

Die Dokumentation finden Sie im [Issue 2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).

### <a name="cosmos-configuration"></a>Cosmos-Konfiguration

EF Core 5.0 verbessert die Konfiguration von Cosmos und Cosmos-Verbindungen.

Zuvor erforderte EF Core, dass der Endpunkt und der Schlüssel explizit festgelegt werden, wenn eine Verbindung mit einer Cosmos-Datenbank hergestellt wird. EF Core 5.0 erlaubt stattdessen die Verwendung einer Verbindungszeichenfolge. Darüber hinaus ermöglicht EF Core 5.0 das explizite Festlegen der WebProxy-Instanz. Zum Beispiel:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

Viele andere Timeoutwerte, Grenzwerte und mehr können nun ebenfalls konfiguriert werden. Zum Beispiel:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

Schließlich ist `ConnectionMode.Gateway` nun der Standardverbindungsmodus, der im Allgemeinen kompatibler ist.

Die Dokumentation finden Sie im [Issue 2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).

### <a name="scaffold-dbcontext-now-singularizes"></a>Scaffold-DbContext nutzt nun die Singularform

Beim Gerüstbau einer DbContext-Instanz anhand einer vorhandenen Datenbank hat EF Core zuvor Entitätstypnamen erstellt, die mit den Tabellennamen in der Datenbank übereinstimmen. Beispielsweise führten die Tabellen `People` und `Addresses` zu Entitätstypen mit den Namen `People` und `Addresses`.

In früheren Releases konnte dieses Verhalten über die Registrierung eines Pluralisierungsdiensts konfiguriert werden. In EF Core 5.0 wird nun das Paket [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) als Standardpluralisierungsdienst verwendet. Das bedeutet, die Tabellen `People` und `Addresses` werden nun so rekonstruiert, dass Entitätstypen mit den Namen `Person` und `Address` erstellt werden.

### <a name="savepoints"></a>Sicherungspunkte

EF Core unterstützt nun [Sicherungspunkte](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks), um mehr Kontrolle über Transaktionen zu bieten, die mehrere Vorgänge ausführen.

Sicherungspunkte können manuell erstellt, veröffentlicht und zurückgesetzt werden. Zum Beispiel:

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

Darüber hinaus führt EF Core nun ein Rollback auf den letzten Sicherungspunkt aus, wenn die Ausführung von `SaveChanges` fehlschlägt. Dies ermöglicht das erneute Ausführen von SaveChanges, ohne dass die gesamte Transaktion wiederholt werden muss.

Die Dokumentation finden Sie im [Issue 2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).

## <a name="preview-6"></a>Vorschau 6

### <a name="split-queries-for-related-collections"></a>Aufteilen von Abfragen für ähnliche Sammlungen

Ab EF Core 3.0 generiert EF Core für jede LINQ-Abfrage eine einzelne SQL-Abfrage. Dadurch wird sichergestellt, dass die zurückgegebenen Daten im Rahmen der Einschränkungen des verwendeten Transaktionsmodus konsistent sind. Wenn die Abfrage `Include` oder eine Projektion verwendet, um mehrere ähnliche Sammlungen zurückzugeben, kann die Ausführung jedoch sehr lange dauern.

In EF Core 5.0 ist es jetzt möglich, eine einzelne LINQ-Abfrage und die zugehörigen Sammlungen auf mehrere SQL-Abfragen aufzuteilen. Die Leistung kann dadurch zwar erheblich verbessert werden, doch bei den zurückgegebenen Ergebnissen kann es zu Inkonsistenzen kommen, wenn die Daten sich zwischen den beiden Abfragen ändern. Serialisierbare Transaktionen oder Momentaufnahmentransaktionen können verwendet werden, um das zu verhindern und Konsistenz bei aufgeteilten Abfragen zu erzielen. Dadurch kann jedoch die Leistung beeinträchtigt werden, und das Verhalten kann sich unterscheiden.

#### <a name="split-queries-with-include"></a>Aufteilen von Abfragen mit Include

Gehen Sie von einer Abfrage aus, die mithilfe von `Include` zwei Ebenen ähnlicher Sammlungen einschließt:

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

Standardmäßig generiert EF Core den folgenden SQL-Wert, wenn der SQLite-Anbieter verwendet wird:

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

Die neue `AsSplitQuery`-API kann verwendet werden, um dieses Verhalten zu ändern. Zum Beispiel:

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

AsSplitQuery ist für alle relationalen Datenbankanbieter verfügbar und kann genau wie AsNoTracking an beliebiger Stelle in der Abfrage verwendet werden. In EF Core werden nun die folgenden drei SQL-Abfragen generiert:

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

Alle Vorgänge für den Abfragestamm werden unterstützt. Das schließt OrderBy-, Skip-, Take-, Join-, FirstOrDefault-Vorgänge und ähnliche Vorgänge ein, die ein einzelnes Ergebnis auswählen.

Beachten Sie, dass nach OrderBy, Skip und Take gefilterte Include-Abfragen in Preview 6 nicht unterstützt werden, aber in den täglichen Builds und zukünftig in Preview 7 enthalten sind.

#### <a name="split-queries-with-collection-projections"></a>Aufteilen von Abfragen mit Sammlungsprojektionen

`AsSplitQuery` kann auch verwendet werden, wenn Sammlungen in Projektionen geladen werden. Zum Beispiel:

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

Diese LINQ-Abfrage generiert die folgenden zwei SQL-Abfragen, wenn der SQLite-Anbieter verwendet wird:

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

Beachten Sie, dass nur die Materialisierung der Sammlung unterstützt wird. Jede Komposition nach `e.Albums` im obigen Fall führt nicht zu einer aufgeteilten Abfrage. Die Verbesserungen in diesem Bereich werden in [Issue 21234](https://github.com/dotnet/efcore/issues/21234) nachverfolgt.

### <a name="indexattribute"></a>IndexAttribute

Die neue IndexAttribute-Klasse kann für einen Entitätstyp verwendet werden, um einen Index für eine einzelne Spalte anzugeben. Zum Beispiel:

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

Bei SQL Server generieren Migrationen dann folgenden SQL-Code:

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

IndexAttribute kann auch verwendet werden, um einen Index anzugeben, der mehrere Spalten umfasst. Zum Beispiel:

```CSharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

Für SQL Server ergibt sich Folgendes:

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

Die Dokumentation finden Sie im [Issue 2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).

### <a name="improved-query-translation-exceptions"></a>Verbesserte Ausnahmen bei der Abfrageübersetzung

Wir arbeiten weiter an der Verbesserung der Ausnahmemeldungen, die generiert werden, wenn bei der Abfrageübersetzung ein Fehler auftritt. Diese Abfrage verwendet z. B. die nicht zugeordnete Eigenschaft `IsSigned`:

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

EF Core löst die folgende Ausnahme aus, die angibt, dass bei der Übersetzung ein Fehler aufgetreten ist, da `IsSigned` nicht zugeordnet ist:

> Ausnahmefehler. System.InvalidOperationException: Der LINQ-Ausdruck „DbSet<Artist>() .Where(a => a.IsSigned)“ kann nicht übersetzt werden. Zusätzliche Informationen: Bei der Übersetzung des Members IsSigned für den Entitätstyp Artist ist ein Fehler aufgetreten. Möglicherweise ist der angegebene Member nicht zugeordnet. Schreiben Sie die Abfrage in einem übersetzbaren Format neu, oder wechseln Sie explizit zur Clientauswertung, indem Sie einen Aufruf von AsEnumerable(), AsAsyncEnumerable(), ToList() oder ToListAsync() einfügen. Weitere Informationen finden Sie unter https://go.microsoft.com/fwlink/?linkid=2101038.

Zudem werden jetzt bessere Ausnahmemeldungen generiert, wenn Sie versuchen, Zeichenfolgenvergleiche mit kulturabhängiger Semantik zu übersetzen. Diese Abfrage versucht beispielsweise, `StringComparison.CurrentCulture` zu verwenden:

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

In EF Core wird nun die folgende Ausnahme ausgelöst:

> Ausnahmefehler. System.InvalidOperationException: Der LINQ-Ausdruck „DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))“ kann nicht übersetzt werden. Zusätzliche Informationen: Die Übersetzung der Methode string.Equals, die das Argument StringComparison verwendet, wird nicht unterstützt. Weitere Informationen finden Sie unter https://go.microsoft.com/fwlink/?linkid=2129535. Schreiben Sie die Abfrage in einem übersetzbaren Format neu, oder wechseln Sie explizit zur Clientauswertung, indem Sie einen Aufruf von AsEnumerable(), AsAsyncEnumerable(), ToList() oder ToListAsync() einfügen. Weitere Informationen finden Sie unter https://go.microsoft.com/fwlink/?linkid=2101038.

### <a name="specify-transaction-id"></a>Angeben der Transaktions-ID

Dieses Feature stammt vom Communitymitglied [@Marusyk](https://github.com/Marusyk). Danke für diesen Beitrag!

EF Core macht eine Transaktions-ID für die Korrelation von aufrufübergreifenden Transaktionen verfügbar. Diese ID wird in der Regel durch EF Core festgelegt, wenn eine Transaktion gestartet wird. Wenn die Anwendung stattdessen die Transaktion startet, ermöglicht dieses Feature der Anwendung, die Transaktions-ID explizit festzulegen, damit sie überall ordnungsgemäß korreliert, wo sie verwendet wird. Zum Beispiel:

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a>IPAddress-Zuordnung

Dieses Feature stammt vom Communitymitglied [@ralmsdeveloper](https://github.com/ralmsdeveloper). Danke für diesen Beitrag!

Die neue .NET Standard-Klasse [IPAddress](/dotnet/api/system.net.ipaddress) wird jetzt automatisch einer Zeichenfolgenspalte für Datenbanken zugeordnet, die noch nicht nativ unterstützt werden. Sehen Sie sich beispielsweise die Zuordnung dieses Entitätstyps an:

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

In SQL Server führt dies zu Migrationen, die die folgende Tabelle erstellen:

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

Entitäten können dann wie gewohnt hinzugefügt werden:

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

Die resultierende SQL-Abfrage fügt die normalisierte IPv4- oder IPv6-Adresse ein:

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a>Ausschließen von OnConfiguring beim Gerüstbau

Wenn das Gerüst für DbContext aus einer vorhandenen Datenbank erstellt wird, erstellt EF Core standardmäßig eine OnConfiguring-Überladung mit einer Verbindungszeichenfolge, damit der Kontext sofort einsatzbereit ist. Das ist jedoch nicht nützlich, wenn Sie bereits eine partielle Klasse mit OnConfiguring besitzen oder den Kontext auf andere Weise konfigurieren.

Die Gerüstbaubefehle können jetzt so geändert werden, dass OnConfiguring nicht generiert wird, um das Problem zu beheben. Zum Beispiel:

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

In der Paket-Manager-Konsole:

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

Die Verwendung einer [benannten Verbindungszeichenfolge und eines sicheren Speichers wie Benutzergeheimnisse](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets) wird empfohlen.

### <a name="translations-for-firstordefault-on-strings"></a>Übersetzungen für FirstOrDefault in Zeichenfolgen

Dieses Feature stammt vom Communitymitglied [@dvoreckyaa](https://github.com/dvoreckyaa). Danke für diesen Beitrag!

FirstOrDefault und ähnliche Operatoren für Verbindungszeichenfolgen werden jetzt übersetzt. Nehmen Sie diese LINQ-Abfrage als Beispiel:

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

Sie wird jetzt in folgenden SQL-Code übersetzt, wenn Sie SQL Server verwenden:

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a>Vereinfachen von CASE-Blöcken

EF Core generiert nun bessere Abfragen, wenn CASE-Blöcke verwendet werden. Nehmen Sie diese LINQ-Abfrage als Beispiel:

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

In SQL Server wurde sie zuvor in Folgendes übersetzt:

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

Jetzt wird sie folgendermaßen übersetzt:

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a>Preview 5

### <a name="database-collations"></a>Datenbanksortierungen

Die Standardsortierung einer Datenbank kann nun im EF-Modell angegeben werden. Diese durchläuft generierte Migrationen, um die Sortierung beim Erstellen der Datenbank festzulegen. Zum Beispiel:

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

Die Migrationen generieren dann Folgendes, um die Datenbank in SQL Server zu erstellen:

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

Die Sortierung für spezifische Datenbankspalten kann ebenfalls angegeben werden. Zum Beispiel:

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

Für diejenigen, die keine Migrationen verwenden, wird für Sortierungen nun ein Reverse Engineering von der Datenbank aus durchgeführt, wenn der Gerüstbau für eine DbContext-Instanz durchgeführt wird.

Schließlich lässt `EF.Functions.Collate()` Ad-hoc-Abfragen mit verschiedenen Sortierungen zu. Zum Beispiel:

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

Dadurch wird die folgende Abfrage für SQL Server generiert:

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

Beachten Sie, dass Ad-Hoc-Sortierungen mit Bedacht verwendet werden sollten, da sie sich negativ auf die Datenbankleistung auswirken können.

Die Dokumentation finden Sie im [Issue 2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a>Übergeben von Argumenten in IDesignTimeDbContextFactory

Argumente können nun über die Befehlszeile in die `CreateDbContext`-Methode von [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1) übertragen werden. Sie können beispielsweise angeben, dass es sich um einen Entwicklungsbuild handelt, indem Sie in der Befehlszeile ein benutzerdefiniertes Argument (z. B. `dev`) übergeben:

```
dotnet ef migrations add two --verbose --dev
```

Dieses Argument wird dann an die Factory weitergeleitet, wo es zum Steuern der Erstellung und Initialisierung des Kontexts verwendet werden kann. Zum Beispiel:

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

Die Dokumentation finden Sie im [Issue 2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).

### <a name="no-tracking-queries-with-identity-resolution"></a>Abfragen ohne Nachverfolgung mit Identitätsauflösung

Abfragen ohne Nachverfolgung können nun zum Durchführen von Identitätsauflösungen konfiguriert werden. Die folgende Abfrage erstellt beispielsweise eine neue Blog-Instanz für alle Post-Entitäten, selbst wenn alle Blog-Instanzen über denselben Primärschlüssel verfügen.

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

Diese Abfrage kann angepasst werden, um sicherzustellen, dass nur eine einzige Blog-Instanz erstellt wird. Dies ist jedoch in der Regel ein wenig langsamer und nutzt mehr Arbeitsspeicher:

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

Beachten Sie, dass dies nur für Abfragen ohne Nachverfolgung nützlich ist, weil alle Abfragen mit Nachverfolgung dieses Verhalten bereits aufweisen. Nach einer Überprüfung der API wird auch die `PerformIdentityResolution`-Syntax geändert. Weitere Informationen dazu finden Sie im [Issue 19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).

Die Dokumentation finden Sie im [Issue 1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).

### <a name="stored-persisted-computed-columns"></a>Gespeicherte (persistente) berechnete Spalten

Die meisten Datenbanken lassen zu, dass berechnete Spaltenwerte nach der Berechnung gespeichert werden. Zwar wird dadurch Speicherplatz belegt, jedoch wird die berechnete Spalte nur einmal pro Update berechnet, anstatt jedes Mal, wenn der Wert abgerufen wird. Dies ermöglicht auch die Indizierung der Spalte für einige Datenbanken.

EF Core 5.0 ermöglicht die Konfiguration von berechneten Spalten als gespeichert. Zum Beispiel:

```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a>Berechnete Spalten in SQLite

EF Core unterstützt nun berechnete Spalten in SQLite-Datenbanken.

## <a name="preview-4"></a>Preview 4

### <a name="configure-database-precisionscale-in-model"></a>Konfigurieren von Datenbankgenauigkeit/Skalierung im Modell

Genauigkeit und Skalierung einer Eigenschaft können jetzt mit dem Modellgenerator angegeben werden. Zum Beispiel:

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

Genauigkeit und Skalierung können weiterhin über den vollständigen Datenbanktyp festgelegt werden, z. B. „decimal(16,4)“.

Die Dokumentation finden Sie im Issue [527](https://github.com/dotnet/EntityFramework.Docs/issues/527).

### <a name="specify-sql-server-index-fill-factor"></a>Angeben des Standardfüllfaktors für SQL Server-Indizes

Der Füllfaktor kann nun beim Erstellen eines Indexes auf SQL Server angegeben werden. Zum Beispiel:

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a>Preview 3

### <a name="filtered-include"></a>Gefilterte Include-Funktion

Die Include-Methode unterstützt jetzt das Filtern der enthaltenen Entitäten. Zum Beispiel:

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

Mit dieser Abfrage werden Blogs zusammen mit den jeweiligen Beiträgen zurückgegeben, jedoch nur, wenn der Titel „Cheese“ enthält.

Skip und Take können auch verwendet werden, um die Anzahl der enthaltenen Entitäten zu verringern. Zum Beispiel:

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
Mit dieser Abfrage werden Blogs mit höchstens fünf Beiträgen zurückgegeben.

Ausführliche Informationen finden Sie in der [Dokumentation zu Include](xref:core/querying/related-data#filtered-include).

### <a name="new-modelbuilder-api-for-navigation-properties"></a>Neue ModelBuilder-API für Navigationseigenschaften

Navigationseigenschaften werden in erster Linie beim [Definieren von Beziehungen](xref:core/modeling/relationships) konfiguriert. Die neue `Navigation`-Methode kann jedoch in den Fällen verwendet werden, in denen Navigationseigenschaften eine zusätzliche Konfiguration erfordern. So können Sie z. B. ein Unterstützungsfeld für die Navigation festlegen, wenn das Feld nach Konvention nicht gefunden wird:

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

Beachten Sie, dass die `Navigation`-API die Beziehungskonfiguration nicht ersetzt. Stattdessen ermöglicht sie eine zusätzliche Konfiguration der Navigationseigenschaften in bereits ermittelten oder definierten Beziehungen.

Weitere Informationen finden Sie unter [Konfigurieren von Navigationseigenschaften](xref:core/modeling/relationships#configuring-navigation-properties).

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a>Neue Befehlszeilenparameter für Namespaces und Verbindungszeichenfolgen

Bei Migrationen und Gerüstbau können Namespaces jetzt in der Befehlszeile angegeben werden. So können Sie beispielsweise ein Reverse Engineering für eine Datenbank durchführen, um die Kontext- und Modellklassen in verschiedenen Namespaces zu platzieren:

```dotnetcli
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

Ausführliche Informationen finden Sie in den Dokumentationen zu [Migrationen](xref:core/managing-schemas/migrations/index#namespaces) und [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces).

---
Eine Verbindungszeichenfolge kann nun auch an den `database-update`-Befehl übermittelt werden:

```dotnetcli
dotnet ef database update --connection "connection string"
```

Ausführliche Informationen finden Sie in der [Entity Framework Core Tools-Referenz – .NET-CLI](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update).

Gleichwertige Parameter wurden den PowerShell-Befehlen hinzugefügt, die in der Paket-Manager-Konsole in Visual Studio verwendet werden.

### <a name="enabledetailederrors-has-returned"></a>EnableDetailedErrors wieder verfügbar

Aus Leistungsgründen führt EF keine zusätzlichen NULL-Überprüfungen durch, wenn Werte aus der Datenbank gelesen werden. Dadurch können Ausnahmen ausgelöst werden, deren Ursache schwer zu ermitteln ist, wenn ein unerwarteter NULL-Wert auftritt.

Durch die Verwendung von `EnableDetailedErrors` wird eine zusätzliche NULL-Überprüfung auf Abfragen hinzugefügt, sodass diese Fehler mit geringem Leistungsaufwand einfacher auf ihre Ursache zurückzuführen sind.

Zum Beispiel:

```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

Die Dokumentation finden Sie im Issue [955](https://github.com/dotnet/EntityFramework.Docs/issues/955).

### <a name="cosmos-partition-keys"></a>Cosmos-Partitionsschlüssel

Der Partitionsschlüssel, der für eine bestimmte Abfrage verwendet werden soll, kann jetzt in der Abfrage angegeben werden. Zum Beispiel:

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

Die Dokumentation finden Sie im Issue [2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).

### <a name="support-for-the-sql-server-datalength-function"></a>Unterstützung für die DATALENGTH-Funktion von SQL Server

Auf diese kann mit der neuen `EF.Functions.DataLength`-Methode zugegriffen werden. Zum Beispiel:

```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a>Vorschau 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>Verwenden eines C#-Attributs zum Angeben eines Eigenschaftsunterstützungsfelds

Es kann jetzt ein C#-Attribut verwendet werden, um das Unterstützungsfeld für eine Eigenschaft anzugeben. Dieses Attribut ermöglicht es EF Core, wie sonst auch in das Unterstützungsfeld zu schreiben und daraus zu lesen, auch wenn das Unterstützungsfeld nicht automatisch gefunden werden kann. Zum Beispiel:

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

Die Dokumentation finden Sie im Issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).

### <a name="complete-discriminator-mapping"></a>Vollständige Diskriminatorzuordnung

EF Core verwendet eine Diskriminatorspalte für die [TPH-Zuordnung (Tabelle pro Hierarchie) von Vererbungshierarchien](xref:core/modeling/inheritance). Es sind einige Leistungsverbesserungen möglich, solange EF Core alle möglichen Werte für den Diskriminator kennt. Diese Verbesserungen sind nun in EF Core 5.0 implementiert.

Frühere Versionen von EF Core generierten beispielsweise für Abfragen immer einen SQL-Code wie den folgenden, in dem alle Typen in einer Hierarchie zurückgegeben wurden:

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

EF Core 5.0 generiert nun Folgendes, wenn eine vollständige Diskriminatorzuordnung konfiguriert ist:

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

Ab Vorschauversion 3 wird dies das Standardverhalten.

### <a name="performance-improvements-in-microsoftdatasqlite"></a>Leistungsverbesserungen in Microsoft.Data.Sqlite

Es wurden zwei Leistungsverbesserungen für SQLite vorgenommen:

* Das Abrufen von Binär- und Zeichenfolgendaten mit GetBytes, GetChars und GetTextReader ist nun effizienter durch die Verwendung von SqliteBlob und Datenströmen.
* Die Initialisierung von SqliteConnection ist nun verzögert.

Diese Verbesserungen wurden am ADO.NET-Anbieter Microsoft.Data.Sqlite vorgenommen und führen daher auch zu Leistungsverbesserungen außerhalb von EF Core.

## <a name="preview-1"></a>Vorschauversion 1

### <a name="simple-logging"></a>Einfache Protokollierung

Dieses Feature fügt eine Funktion hinzu, die `Database.Log` in EF6 ähnelt. Es bietet eine einfache Möglichkeit zum Abrufen von Protokollen aus EF Core, ohne ein externes Protokollierungsframework konfigurieren zu müssen.

Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 5. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863) (in englischer Sprache) enthalten.

Zusätzliche Dokumentation finden Sie im Issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).

### <a name="simple-way-to-get-generated-sql"></a>Einfache Möglichkeit zum Abrufen von generiertem SQL-Code

In EF Core 5.0 wird die Erweiterungsmethode `ToQueryString` eingeführt, die den von EF Core beim Ausführen einer LINQ-Abfrage generierten SQL-Code zurückgibt.

Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 9. Januar 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246) (in englischer Sprache) enthalten.

Zusätzliche Dokumentation finden Sie im Issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a>Verwenden eines C#-Attributs zum Angeben des Fehlens eines Schlüssels einer Entität

Ein Entitätstyp kann nun mithilfe des neuen Attributs `KeylessAttribute` als schlüssellos konfiguriert werden. Zum Beispiel:

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

Die Dokumentation finden Sie im Issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a>Ändern von Verbindung oder Verbindungszeichenfolge in initialisiertem DbContext möglich

Es ist jetzt einfacher, eine DbContext-Instanz ohne Verbindung oder Verbindungszeichenfolge zu erstellen. Die Verbindung oder Verbindungszeichenfolge kann jetzt auch in der Kontextinstanz verändert werden. Mithilfe dieses Features kann ein und dieselbe Kontextinstanz dynamisch eine Verbindung mit verschiedenen Datenbanken herstellen.

Die Dokumentation finden Sie im Issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).

### <a name="change-tracking-proxies"></a>Proxys zur Änderungsnachverfolgung

EF Core kann jetzt Runtimeproxys generieren, die automatisch [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) und [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) implementieren. Diese melden dann Wertänderungen bei Entitätseigenschaften direkt an EF Core, sodass die Notwendigkeit einer Überprüfung auf Änderungen entfällt. Für Proxys gelten jedoch bestimmte Einschränkungen, daher eignen sie sich nicht in jedem Fall.

Die Dokumentation finden Sie im Issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).

### <a name="enhanced-debug-views"></a>Erweiterte Debugansichten

Debugansichten sind eine einfache Möglichkeit, beim Debuggen von Problemen interne Vorgänge in EF Core zu untersuchen. Ein Debugansicht für das Modell wurde bereits vor einiger Zeit implementiert. Für EF Core 5.0 haben wir die Modellansicht vereinfacht und im Zustands-Manager eine neue Debugansicht für nachverfolgte Entitäten hinzugefügt.

Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 12. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206) (in englischer Sprache) enthalten.

Zusätzliche Dokumentation finden Sie im Issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).

### <a name="improved-handling-of-database-null-semantics"></a>Verbesserte Verarbeitung von NULL-Semantik in der Datenbank

Relationale Datenbanken behandeln NULL in der Regel als unbekannten Wert und daher als ungleich jedem anderen NULL-Wert. C# hingegen behandelt NULL als definierten Wert, der mit jedem anderen NULL-Wert übereinstimmt. EF Core übersetzt Abfragen standardmäßig so, dass sie die NULL-Semantik von C# verwenden. In EF Core 5.0 wurde die Effizienz dieser Übersetzungen erheblich verbessert.

Die Dokumentation finden Sie im Issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).

### <a name="indexer-properties"></a>Indexereigenschaften

EF Core 5.0 unterstützt die Zuordnung von C#-Indexereigenschaften. Durch diese Eigenschaften können Entitäten als Eigenschaftenbehälter fungieren, wobei Spalten benannten Eigenschaften im Behälter zugeordnet werden.

Die Dokumentation finden Sie im Issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).

### <a name="generation-of-check-constraints-for-enum-mappings"></a>Generieren von CHECK-Einschränkungen für Enumerationszuordnungen

EF Core 5.0-Migrationen können jetzt CHECK-Einschränkungen aus Zuordnungen der enum-Eigenschaft generieren. Zum Beispiel:

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

Die Dokumentation finden Sie im Issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).

### <a name="isrelational"></a>IsRelational

Zusätzlich zu den existierenden `IsSqlServer`-, `IsSqlite`- und `IsInMemory`-Methoden wurde eine neue `IsRelational`-Methode hinzugefügt. Diese Methode kann verwendet werden, um zu testen, ob DbContext einen Anbieter einer relationalen Datenbank verwendet. Zum Beispiel:

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

Die Dokumentation finden Sie im Issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).

### <a name="cosmos-optimistic-concurrency-with-etags"></a>Optimistische Nebenläufigkeit in Cosmos mit Entitätstags (ETags)

Der Azure Cosmos DB-Datenbankanbieter unterstützt nun optimistische Nebenläufigkeit mithilfe von ETags. Verwenden Sie das Modellerstellungstool in OnModelCreating, um ein ETag zu konfigurieren:

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

SaveChanges löst dann eine `DbUpdateConcurrencyException`-Ausnahme für einen Nebenläufigkeitskonflikt aus, die [verarbeitet werden kann](xref:core/saving/concurrency), z. B. um Wiederholungsversuche zu implementieren.

Die Dokumentation finden Sie im Issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).

### <a name="query-translations-for-more-datetime-constructs"></a>Abfrageübersetzungen für weitere DateTime-Konstrukte

Abfragen mit neuer DateTime-Konstruktion werden jetzt übersetzt.

Außerdem werden nun die folgenden SQL Server-Funktionen zugeordnet:

* DateDiffWeek
* DateFromParts

Zum Beispiel:

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translations-for-more-byte-array-constructs"></a>Abfrageübersetzungen für weitere Bytearraykonstrukte

Abfragen, die Contains, Length, SequenceEqual usw. in byte[]-Eigenschaften verwenden, werden jetzt in SQL übersetzt.

Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 5. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863) (in englischer Sprache) enthalten.

Zusätzliche Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translation-for-reverse"></a>Abfrageübersetzung für Reverse

Abfragen, die `Reverse` verwenden, werden jetzt übersetzt. Zum Beispiel:

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translation-for-bitwise-operators"></a>Abfrageübersetzung für bitweise Operatoren

Abfragen, die bitweise Operatoren verwenden, werden jetzt in mehr Fällen übersetzt. Beispiel:

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).

### <a name="query-translation-for-strings-on-cosmos"></a>Abfrageübersetzung für Zeichenfolgen in Cosmos

Abfragen, die die Zeichenfolgenmethoden Contains, StartsWith und EndsWith verwenden, werden jetzt übersetzt, wenn der Azure Cosmos DB-Anbieter verwendet wird.

Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).
