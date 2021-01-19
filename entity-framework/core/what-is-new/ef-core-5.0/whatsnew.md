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
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="36c32-103">Neuerungen in EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="36c32-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="36c32-104">Die folgende Liste enthält die wichtigsten neuen Features in EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="36c32-104">The following list includes the major new features in EF Core 5.0.</span></span> <span data-ttu-id="36c32-105">Eine vollständige Liste der Probleme in diesem Release finden Sie im [Issue-Tracker](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0).</span><span class="sxs-lookup"><span data-stu-id="36c32-105">For the full list of issues in the release, see our [issue tracker](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0).</span></span>

<span data-ttu-id="36c32-106">Da es sich um ein Hauptrelease handelt, umfasst EF Core 5.0 auch zahlreiche [Breaking Changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), bei denen es sich um API-Verbesserungen oder Behavior Changes handelt, die sich negativ auf vorhandene Anwendungen auswirken können.</span><span class="sxs-lookup"><span data-stu-id="36c32-106">As a major release, EF Core 5.0 also contains several [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), which are API improvements or behavioral changes that may have negative impact on existing applications.</span></span>

## <a name="many-to-many"></a><span data-ttu-id="36c32-107">M:n</span><span class="sxs-lookup"><span data-stu-id="36c32-107">Many-to-many</span></span>

<span data-ttu-id="36c32-108">EF Core 5.0 unterstützt m:n-Beziehungen ohne explizite Zuordnung der Jointabelle.</span><span class="sxs-lookup"><span data-stu-id="36c32-108">EF Core 5.0 supports many-to-many relationships without explicitly mapping the join table.</span></span>

<span data-ttu-id="36c32-109">Betrachten Sie beispielsweise die folgenden Entitätstypen:</span><span class="sxs-lookup"><span data-stu-id="36c32-109">For example, consider these entity types:</span></span>

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

<span data-ttu-id="36c32-110">EF Core 5.0 erkennt dies gemäß der Konvention als m:n-Beziehung an und erstellt automatisch eine `PostTag`-Jointabelle in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="36c32-110">EF Core 5.0 recognizes this as a many-to-many relationship by convention, and automatically creates a `PostTag` join table in the database.</span></span> <span data-ttu-id="36c32-111">Die Daten können ohne explizite Referenz der Jointabelle abgefragt und geändert werden, was den Code deutlich vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="36c32-111">Data can be queried and updated without explicitly referencing the join table, considerably simplifying code.</span></span> <span data-ttu-id="36c32-112">Die Jointabelle kann weiterhin bei Bedarf angepasst und abgefragt werden.</span><span class="sxs-lookup"><span data-stu-id="36c32-112">The join table can still be customized and queried explicitly if needed.</span></span>

<span data-ttu-id="36c32-113">Weitere Informationen finden Sie in der Dokumentation zu [m:n-Beziehungen](xref:core/modeling/relationships#many-to-many).</span><span class="sxs-lookup"><span data-stu-id="36c32-113">For further information, [see the full documentation on many-to-many](xref:core/modeling/relationships#many-to-many).</span></span>

## <a name="split-queries"></a><span data-ttu-id="36c32-114">Geteilte Abfragen</span><span class="sxs-lookup"><span data-stu-id="36c32-114">Split queries</span></span>

<span data-ttu-id="36c32-115">Ab EF Core 3.0 generiert EF Core für jede LINQ-Abfrage eine einzelne SQL-Abfrage.</span><span class="sxs-lookup"><span data-stu-id="36c32-115">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="36c32-116">Dadurch wird sichergestellt, dass die zurückgegebenen Daten im Rahmen der Einschränkungen des verwendeten Transaktionsmodus konsistent sind.</span><span class="sxs-lookup"><span data-stu-id="36c32-116">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="36c32-117">Wenn die Abfrage `Include` oder eine Projektion verwendet, um mehrere ähnliche Sammlungen zurückzugeben, kann die Ausführung jedoch sehr lange dauern.</span><span class="sxs-lookup"><span data-stu-id="36c32-117">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="36c32-118">In EF Core 5.0 ist es jetzt möglich, eine einzelne LINQ-Abfrage und die zugehörigen Sammlungen auf mehrere SQL-Abfragen aufzuteilen.</span><span class="sxs-lookup"><span data-stu-id="36c32-118">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="36c32-119">Die Leistung kann dadurch zwar erheblich verbessert werden, doch bei den zurückgegebenen Ergebnissen kann es zu Inkonsistenzen kommen, wenn die Daten sich zwischen den beiden Abfragen ändern.</span><span class="sxs-lookup"><span data-stu-id="36c32-119">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="36c32-120">Serialisierbare Transaktionen oder Momentaufnahmentransaktionen können verwendet werden, um das zu verhindern und Konsistenz bei aufgeteilten Abfragen zu erzielen. Dadurch kann jedoch die Leistung beeinträchtigt werden, und das Verhalten kann sich unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="36c32-120">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

<span data-ttu-id="36c32-121">Gehen Sie von einer Abfrage aus, die mithilfe von `Include` zwei Ebenen ähnlicher Sammlungen einschließt:</span><span class="sxs-lookup"><span data-stu-id="36c32-121">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```csharp
var artists = context.Artists
    .Include(e => e.Albums)
    .ToList();
```

<span data-ttu-id="36c32-122">Standardmäßig generiert EF Core den folgenden SQL-Wert, wenn der SQLite-Anbieter verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="36c32-122">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT a."Id", a."Name", a0."Id", a0."ArtistId", a0."Title"
FROM "Artists" AS a
LEFT JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id", a0."Id"
```

<span data-ttu-id="36c32-123">Mit geteilten Abfragen wird stattdessen der folgende SQL-Code generiert:</span><span class="sxs-lookup"><span data-stu-id="36c32-123">With split queries, the following SQL is generated instead:</span></span>

```sql
SELECT a."Id", a."Name"
FROM "Artists" AS a
ORDER BY a."Id"

SELECT a0."Id", a0."ArtistId", a0."Title", a."Id"
FROM "Artists" AS a
INNER JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id"
```

<span data-ttu-id="36c32-124">Geteilte Abfragen können durch Platzieren des neuen `AsSplitQuery`-Operators an einer beliebigen Stelle in Ihrer LINQ-Abfrage oder global in der `OnConfiguring`-Methode Ihres Modells.</span><span class="sxs-lookup"><span data-stu-id="36c32-124">Split queries can be enabled by placing the new `AsSplitQuery` operator anywhere in your LINQ query, or globally in your model's `OnConfiguring`.</span></span> <span data-ttu-id="36c32-125">Weitere Informationen finden Sie in der Dokumentation zu [Geteilten Abfragen](xref:core/querying/single-split-queries).</span><span class="sxs-lookup"><span data-stu-id="36c32-125">For further information, [see the full documentation on split queries](xref:core/querying/single-split-queries).</span></span>

## <a name="simple-logging-and-improved-diagnostics"></a><span data-ttu-id="36c32-126">Einfache Protokollierung und verbesserte Diagnose</span><span class="sxs-lookup"><span data-stu-id="36c32-126">Simple logging and improved diagnostics</span></span>

<span data-ttu-id="36c32-127">Mit EF Core 5.0 wird eine einfache Möglichkeit zum Einrichten der Protokollierung über die neue `LogTo`-Methode eingeführt.</span><span class="sxs-lookup"><span data-stu-id="36c32-127">EF Core 5.0 introduces a simple way to set up logging via the new `LogTo` method.</span></span> <span data-ttu-id="36c32-128">Der folgende Code führt dazu, dass Protokollierungsmeldungen, einschließlich des gesamten von EF Core generierten SQL-Codes, in die Konsole geschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="36c32-128">The following will cause logging messages to be written to the console, including all SQL generated by EF Core:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder.LogTo(Console.WriteLine);
```

<span data-ttu-id="36c32-129">Außerdem ist es jetzt möglich, `ToQueryString` für beliebige LINQ-Abfragen aufzurufen, um den SQL-Code abzurufen, den die Abfrage ausführen würde:</span><span class="sxs-lookup"><span data-stu-id="36c32-129">In addition, it is now possible to call `ToQueryString` on any LINQ query, retrieving the SQL that the query would execute:</span></span>

```csharp
Console.WriteLine(
    ctx.Artists
    .Where(a => a.Name == "Pink Floyd")
    .ToQueryString());
```

<span data-ttu-id="36c32-130">Letztlich wurden verschiedene EF Core-Typen mit einer erweiterten `DebugView`-Eigenschaft ausgestattet, die eine ausführliche Ansicht über interne Informationen bietet.</span><span class="sxs-lookup"><span data-stu-id="36c32-130">Finally, various EF Core types have been fitted with an enhanced `DebugView` property which provides a detailed view into the internals.</span></span> <span data-ttu-id="36c32-131">Beispielsweise können Sie mit <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> überprüfen, welche Entitäten zu einem bestimmten Zeitpunkt nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="36c32-131">For example, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> can be consulted to see exactly which entities are being tracked in a given moment.</span></span>

<span data-ttu-id="36c32-132">Weitere Informationen finden Sie in der Dokumentation zu [Protokollierung und Abfangfunktionen](xref:core/logging-events-diagnostics/index).</span><span class="sxs-lookup"><span data-stu-id="36c32-132">For further information, [see the documentation on logging and interception](xref:core/logging-events-diagnostics/index).</span></span>

## <a name="filtered-include"></a><span data-ttu-id="36c32-133">Gefilterte Include-Funktion</span><span class="sxs-lookup"><span data-stu-id="36c32-133">Filtered include</span></span>

<span data-ttu-id="36c32-134">Die `Include`-Methode unterstützt nun das Filtern der enthaltenen Entitäten:</span><span class="sxs-lookup"><span data-stu-id="36c32-134">The `Include` method now supports filtering of the entities included:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="36c32-135">Mit dieser Abfrage werden Blogs zusammen mit den jeweiligen Beiträgen zurückgegeben, jedoch nur, wenn der Titel „Cheese“ enthält.</span><span class="sxs-lookup"><span data-stu-id="36c32-135">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="36c32-136">Weitere Informationen finden Sie in der Dokumentation zu [Geteilten Abfragen](xref:core/querying/related-data/eager#filtered-include).</span><span class="sxs-lookup"><span data-stu-id="36c32-136">For further information, [see the full documentation on split queries](xref:core/querying/related-data/eager#filtered-include).</span></span>

## <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="36c32-137">TPT-Zuordnung (Tabelle pro Typ)</span><span class="sxs-lookup"><span data-stu-id="36c32-137">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="36c32-138">EF Core ordnet standardmäßig eine Vererbungshierarchie von .NET-Typen einer einzelnen Datenbanktabelle zu.</span><span class="sxs-lookup"><span data-stu-id="36c32-138">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="36c32-139">Dies wird als TPH-Zuordnung (Tabelle pro Hierarchie) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="36c32-139">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="36c32-140">EF Core 5.0 ermöglicht außerdem das Zuordnen der einzelnen .NET-Typen in einer Vererbungshierarchie zu einer anderen Datenbanktabelle. Dies wird als TPT-Zuordnung (Tabelle pro Typ) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="36c32-140">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="36c32-141">Stellen Sie sich beispielsweise das folgende Modell mit einer zugeordneten Hierarchie vor:</span><span class="sxs-lookup"><span data-stu-id="36c32-141">For example, consider this model with a mapped hierarchy:</span></span>

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

<span data-ttu-id="36c32-142">Bei TPT wird eine Datenbanktabelle für jeden Typ in der Hierarchie erstellt:</span><span class="sxs-lookup"><span data-stu-id="36c32-142">With TPT, a database table is created for each type in the hierarchy:</span></span>

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

<span data-ttu-id="36c32-143">Weitere Informationen finden Sie in der Dokumentation zu [TPT](xref:core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="36c32-143">For further information, [see the full documentation on TPT](xref:core/modeling/inheritance).</span></span>

## <a name="flexible-entity-mapping"></a><span data-ttu-id="36c32-144">Flexible Entitätszuordnung</span><span class="sxs-lookup"><span data-stu-id="36c32-144">Flexible entity mapping</span></span>

<span data-ttu-id="36c32-145">Entitätstypen werden häufig Tabellen oder Sichten zugeordnet, sodass EF Core den Inhalt der Tabelle oder der Sicht zurückgibt, wenn dieser Typ abgefragt wird.</span><span class="sxs-lookup"><span data-stu-id="36c32-145">Entity types are commonly mapped to tables or views such that EF Core will pull back the contents of the table or view when querying for that type.</span></span> <span data-ttu-id="36c32-146">Mit EF Core 5.0 wurden zusätzliche Zuordnungsoptionen hinzugefügt, mit denen eine Entität beispielsweise einer SQL-Abfrage (eine sogenannte „definierende Abfrage“) oder einer Tabellenwertfunktion (TVF) zugeordnet werden kann:</span><span class="sxs-lookup"><span data-stu-id="36c32-146">EF Core 5.0 adds additional mapping options, where an entity can be mapped to a SQL query (called a "defining query"), or to a table-valued function (TVF):</span></span>

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

<span data-ttu-id="36c32-147">Tabellenwertfunktionen können auch einer .NET-Methode anstelle einer DbSet-Klasse zugeordnet werden, sodass Parameter übergeben werden können. Die Zuordnung kann mithilfe von <xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A> eingerichtet werden.</span><span class="sxs-lookup"><span data-stu-id="36c32-147">Table-valued functions can also be mapped to a .NET method rather than to a DbSet, allowing parameters to be passed; the mapping can be set up with <xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A>.</span></span>

<span data-ttu-id="36c32-148">Außerdem ist es nun möglich, eine Entität bei Abfragen einer Ansicht (oder einer Funktion oder definierenden Abfrage) zuzuordnen, jedoch ist die Zuordnung zu einer Tabelle beim Vornehmen von Änderungen nicht möglich:</span><span class="sxs-lookup"><span data-stu-id="36c32-148">Finally, it is now possible to map an entity to a view when querying (or to a function or defining query), but to a table when updating:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

## <a name="shared-type-entity-types-and-property-bags"></a><span data-ttu-id="36c32-149">Freigegebene Entitätstypen und Eigenschaftenbehälter</span><span class="sxs-lookup"><span data-stu-id="36c32-149">Shared-type entity types and property bags</span></span>

<span data-ttu-id="36c32-150">EF Core 5.0 ermöglicht die Zuordnung desselben CLR-Typs zu mehreren verschiedenen Entitätstypen. Diese Typen werden als freigegebene Entitätstypen bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="36c32-150">EF Core 5.0 allows the same CLR type to be mapped to multiple different entity types; such types are known as shared-type entity types.</span></span> <span data-ttu-id="36c32-151">Während beliebige CLR-Typen mit diesem Feature verwendet werden können, bietet die .NET-Klasse `Dictionary` einen besonders überzeugenden Anwendungsfall, der als „Eigenschaftenbehälter“ bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="36c32-151">While any CLR type can be used with this feature, .NET `Dictionary` offers a particularly compelling use-case which we call "property bags":</span></span>

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

<span data-ttu-id="36c32-152">Diese Entitäten können dann wie normale Entitätstypen mit ihrem eigenen dedizierten CLR-Typ abgefragt und geändert werden.</span><span class="sxs-lookup"><span data-stu-id="36c32-152">These entities can then be queried and updated just like normal entity types with their own, dedicated CLR type.</span></span> <span data-ttu-id="36c32-153">Weitere Informationen finden Sie in der Dokumentation zu [Eigenschaftenbehältern](xref:core/modeling/shadow-properties).</span><span class="sxs-lookup"><span data-stu-id="36c32-153">More information can be found in the documentation on [property bags](xref:core/modeling/shadow-properties).</span></span>

## <a name="required-11-dependents"></a><span data-ttu-id="36c32-154">Erforderliche 1:1-Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="36c32-154">Required 1:1 dependents</span></span>

<span data-ttu-id="36c32-155">Bei EF Core 3.1 war das abhängige Ende einer 1:1-Beziehung immer optional.</span><span class="sxs-lookup"><span data-stu-id="36c32-155">In EF Core 3.1, the dependent end of a one-to-one relationship was always considered optional.</span></span> <span data-ttu-id="36c32-156">Dies war insbesondere bei Verwendung von nicht eigenständigen Entitäten offensichtlich, da alle Spalten der nicht eigenständigen Entität in der Datenbank als NULL-Werte zulassend erstellt wurden, obwohl sie im Modell als erforderlich konfiguriert waren.</span><span class="sxs-lookup"><span data-stu-id="36c32-156">This was most apparent when using owned entities, as all the owned entity's column were created as nullable in the database, even if they were configured as required in the model.</span></span>

<span data-ttu-id="36c32-157">In EF Core 5.0 kann eine Navigation zu einer nicht eigenständigen Entität als erforderliche Abhängigkeit konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="36c32-157">In EF Core 5.0, a navigation to an owned entity can be configured as as a required dependent.</span></span> <span data-ttu-id="36c32-158">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="36c32-158">For example:</span></span>

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

## <a name="dbcontextfactory"></a><span data-ttu-id="36c32-159">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="36c32-159">DbContextFactory</span></span>

<span data-ttu-id="36c32-160">Mit EF Core 5.0 wurden `AddDbContextFactory` und `AddPooledDbContextFactory` zum Registrieren einer Factory zum Erstellen von DbContext-Instanzen im Dependency Injection-Container der Anwendung eingeführt. Dies kann sich als hilfreich erweisen, wenn Anwendungscode Kontextinstanzen manuell erstellen und löschen muss.</span><span class="sxs-lookup"><span data-stu-id="36c32-160">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container; this can be useful when application code needs to create and dispose context instances manually.</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="36c32-161">Zu diesem Zeitpunkt kann `IDbContextFactory<TContext>` in Anwendungsdienste wie ASP.NET Core-Controller eingefügt werden, was dann zum Instanziieren von Kontextinstanzen verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="36c32-161">At this point, application services such as ASP.NET Core controllers can then be injected with `IDbContextFactory<TContext>`, and use it to instantiate context instances:</span></span>

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

<span data-ttu-id="36c32-162">Weitere Informationen finden Sie in der Dokumentation zu [DbContextFactory](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor).</span><span class="sxs-lookup"><span data-stu-id="36c32-162">For further information, [see the full documentation on DbContextFactory](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor).</span></span>

## <a name="sqlite-table-rebuilds"></a><span data-ttu-id="36c32-163">Neuerstellung von SQLite-Tabellen</span><span class="sxs-lookup"><span data-stu-id="36c32-163">SQLite table rebuilds</span></span>

<span data-ttu-id="36c32-164">Im Vergleich zu anderen Datenbanken ist SQLite hinsichtlich der Schemamanipulationsfunktionen relativ eingeschränkt, beispielsweise wird das Löschen einer vorhandenen Tabelle nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="36c32-164">Compared to other databases, SQLite is relatively limited in its schema manipulation capabilities; for example, dropping a column from an existing table is not supported.</span></span> <span data-ttu-id="36c32-165">EF Core 5.0 umgeht diese Einschränkungen, indem automatisch eine neue Tabelle erstellt wird, die Daten aus der alten Tabelle kopiert werden, die alte Tabelle gelöscht wird und die neue Tabelle umbenannt wird.</span><span class="sxs-lookup"><span data-stu-id="36c32-165">EF Core 5.0 works around these limitations by automatically creating a new table, copying the data from the old table, dropping the old table and renaming the new one.</span></span> <span data-ttu-id="36c32-166">Dadurch wird die Tabelle „neu erstellt“, und zuvor nicht unterstützte Migrationsvorgänge können dann sicher angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="36c32-166">This "rebuilds" the table, and allows previously unsupported migration operations to be safely applied.</span></span>

<span data-ttu-id="36c32-167">Ausführliche Informationen dazu, welche Migrationsvorgänge nun über Neuerstellungen von Tabellen unterstützt werden, finden Sie in [dieser Dokumentation](xref:core/providers/sqlite/limitations#migrations-limitations).</span><span class="sxs-lookup"><span data-stu-id="36c32-167">For details on which migration operations are now supported via table rebuilds, [see this documentation page](xref:core/providers/sqlite/limitations#migrations-limitations).</span></span>

## <a name="database-collations"></a><span data-ttu-id="36c32-168">Datenbanksortierungen</span><span class="sxs-lookup"><span data-stu-id="36c32-168">Database collations</span></span>

<span data-ttu-id="36c32-169">Mit EF Core 5.0 wird Unterstützung für das Festlegen von Textsortierungen auf Datenbank-, Spalten- oder Abfrageebene eingeführt.</span><span class="sxs-lookup"><span data-stu-id="36c32-169">EF Core 5.0 introduces support for specifying text collations at the database, column or query level.</span></span> <span data-ttu-id="36c32-170">Dies ermöglicht die Konfiguration der Unterscheidung nach Groß-/Kleinschreibung und anderen Textaspekten, die flexibel ist und die Abfrageleistung nicht beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="36c32-170">This allows case sensitivity and other textual aspects to be configured in a way that is both flexible and does not compromise query performance.</span></span>

<span data-ttu-id="36c32-171">Mit dem folgenden Code wird die `Name`-Spalte so konfiguriert, dass sie die Groß-/Kleinschreibung bei SQL Server beachtet, und alle für die Spalten erstellten Indizes funktionieren entsprechend:</span><span class="sxs-lookup"><span data-stu-id="36c32-171">For example, the following will configure the `Name` column to be case-sensitive on SQL Server, and any indexes created on the column will function accordingly:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("SQL_Latin1_General_CP1_CS_AS");
```

<span data-ttu-id="36c32-172">Weitere Informationen finden Sie in der Dokumentation zu [Sortierungen und die Beachtung der Groß-/Kleinschreibung](xref:core/miscellaneous/collations-and-case-sensitivity).</span><span class="sxs-lookup"><span data-stu-id="36c32-172">For further information, [see the full documentation on collations and case sensitivity](xref:core/miscellaneous/collations-and-case-sensitivity).</span></span>

## <a name="event-counters"></a><span data-ttu-id="36c32-173">Ereignisindikatoren</span><span class="sxs-lookup"><span data-stu-id="36c32-173">Event counters</span></span>

<span data-ttu-id="36c32-174">EF Core 5.0 stellt [Ereignisindikatoren](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0) zur Verfügung, die dazu verwendet werden können, die Leistung und verschiedene Anomalien Ihrer Anwendung zu überwachen.</span><span class="sxs-lookup"><span data-stu-id="36c32-174">EF Core 5.0 exposes [event counters](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0) which can be used to track your application's performance and spot various anomalies.</span></span> <span data-ttu-id="36c32-175">Fügen Sie einfach einen Prozess an, der Entity Framework mit dem Tool [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) ausführt:</span><span class="sxs-lookup"><span data-stu-id="36c32-175">Simply attach to a process running EF with the [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) tool:</span></span>

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

<span data-ttu-id="36c32-176">Weitere Informationen finden Sie in der Dokumentation zu [Ereignisindikatoren](xref:core/logging-events-diagnostics/event-counters).</span><span class="sxs-lookup"><span data-stu-id="36c32-176">For further information, [see the full documentation on event counters](xref:core/logging-events-diagnostics/event-counters).</span></span>

## <a name="other-features"></a><span data-ttu-id="36c32-177">Weitere Features</span><span class="sxs-lookup"><span data-stu-id="36c32-177">Other features</span></span>

### <a name="model-building"></a><span data-ttu-id="36c32-178">Modellerstellung</span><span class="sxs-lookup"><span data-stu-id="36c32-178">Model building</span></span>

* <span data-ttu-id="36c32-179">Es wurden APIs für die Modellerstellung eingeführt, um die Konfiguration von [Wertvergleichen](xref:core/modeling/value-comparers) zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="36c32-179">Model building APIs have been introduced for easier configuration of [value comparers](xref:core/modeling/value-comparers).</span></span>
* <span data-ttu-id="36c32-180">Berechnete Spalten können nun als [*gespeichert* oder *virtuell*](xref:core/modeling/generated-properties#computed-columns) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="36c32-180">Computed columns can now be configured as [*stored* or *virtual*](xref:core/modeling/generated-properties#computed-columns).</span></span>
* <span data-ttu-id="36c32-181">Die Präzision und Skalierung kann nun [über die Fluent-API](xref:core/modeling/entity-properties#precision-and-scale) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="36c32-181">Precision and scale can now be configured [via the Fluent API](xref:core/modeling/entity-properties#precision-and-scale).</span></span>
* <span data-ttu-id="36c32-182">Für [Navigationseigenschaften](xref:core/modeling/relationships#configuring-navigation-properties) wurden neue APIs für die Modellerstellung eingeführt.</span><span class="sxs-lookup"><span data-stu-id="36c32-182">New model building APIs have been introduced for [navigation properties](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>
* <span data-ttu-id="36c32-183">Für Felder wurden ähnlich wie für Eigenschaften neue APIs für die Modellerstellung eingeführt.</span><span class="sxs-lookup"><span data-stu-id="36c32-183">New model building APIs have been introduced for fields, similar to properties.</span></span>
* <span data-ttu-id="36c32-184">Die .NET-Typen [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) und [IPAddress](/dotnet/api/system.net.ipaddress) können nun zu Zeichenfolgenspalten der Datenbank zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="36c32-184">The .NET [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) and [IPAddress](/dotnet/api/system.net.ipaddress) types can now be mapped to database string columns.</span></span>
* <span data-ttu-id="36c32-185">Über [das neue `[BackingField]`-Attribut](xref:core/modeling/backing-field) kann nun eine Unterstützungsvariable konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="36c32-185">A backing field can now be configured via [the new `[BackingField]` attribute](xref:core/modeling/backing-field).</span></span>
* <span data-ttu-id="36c32-186">Unterstützungsvariablen, die NULL-Werte zulassen, sind nun zulässig, wodurch bessere Unterstützung für vom Speicher generierte Standardwerte bereitgestellt wird, wenn CLR-Standardwerte sich nicht als guter Sentinelwert eignen (insbesondere `bool`).</span><span class="sxs-lookup"><span data-stu-id="36c32-186">Nullable backing fields are now allowed, providing better support for store-generated defaults where the CLR default isn't a good sentinel value (notable `bool`).</span></span>
* <span data-ttu-id="36c32-187">Anstelle der Fluent-API kann nun ein neues `[Index]`-Attribut für einen Entitätstyp verwendet werden, um einen Index anzugeben.</span><span class="sxs-lookup"><span data-stu-id="36c32-187">A new `[Index]` attribute can be used on an entity type to specify an index, instead of using the Fluent API.</span></span>
* <span data-ttu-id="36c32-188">Ein neues `[Keyless]`-Attribut kann verwendet werden, um einen Entitätstyp so zu konfigurieren, [dass er über keinen Schlüssel verfügt](xref:core/modeling/keyless-entity-types).</span><span class="sxs-lookup"><span data-stu-id="36c32-188">A new `[Keyless]` attribute can be used to configure an entity type [as having no key](xref:core/modeling/keyless-entity-types).</span></span>
* <span data-ttu-id="36c32-189">Standardmäßig [betrachtet EF Core Diskriminatoren nun als *vollständig*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration), d. h., es wird davon ausgegangen, dass nie Diskriminatorwerte vorliegen, die nicht von der Anwendung im Modell konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="36c32-189">By default, [EF Core now regards discriminators as *complete*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration), meaning that it expects to never see discriminator values not configured by the application in the model.</span></span> <span data-ttu-id="36c32-190">Dies ermöglicht einige Leistungsverbesserungen und kann deaktiviert werden, wenn Ihre Diskriminatorspalte unbekannte Werte enthalten kann.</span><span class="sxs-lookup"><span data-stu-id="36c32-190">This allows for some performance improvements, and can be disabled if your discriminator column might hold unknown values.</span></span>

### <a name="query"></a><span data-ttu-id="36c32-191">Abfrage</span><span class="sxs-lookup"><span data-stu-id="36c32-191">Query</span></span>

* <span data-ttu-id="36c32-192">Ausnahmen für Fehler bei der Abfrageübersetzung enthalten nun explizitere Grüne für Fehler, um die Ermittlung des Problems zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="36c32-192">Query translation failure exceptions now contain more explicit reasons about the reasons for the failure, to help pinpoint the issue.</span></span>
* <span data-ttu-id="36c32-193">Abfragen ohne Nachverfolgung können nun die [Identitätsauflösung](xref:core/querying/tracking#identity-resolution) durchführen, um zu vermeiden, dass mehrere Entitätsinstanzen für dasselbe Datenbankobjekt zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="36c32-193">No-tracking queries can now perform [identity resolution](xref:core/querying/tracking#identity-resolution), avoiding multiple entity instances being returned for the same database object.</span></span>
* <span data-ttu-id="36c32-194">Es wurde Unterstützung für GroupBy mit bedingten Aggregaten hinzugefügt (z. B. `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))`).</span><span class="sxs-lookup"><span data-stu-id="36c32-194">Added support for GroupBy with conditional aggregates (e.g. `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))`).</span></span>
* <span data-ttu-id="36c32-195">Es wurde Unterstützung für die Übersetzung des Distinct-Operators über Gruppenelemente vor der Aggregation hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="36c32-195">Added support for translating the Distinct operator over group elements before aggregate.</span></span>
* <span data-ttu-id="36c32-196">Die Übersetzung von [`Reverse`](/dotnet/api/system.linq.queryable.reverse) wurde hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="36c32-196">Translation of [`Reverse`](/dotnet/api/system.linq.queryable.reverse).</span></span>
* <span data-ttu-id="36c32-197">Die Übersetzung bezüglich `DateTime` wurde für SQL Server verbessert (z. B. [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A), [`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A)).</span><span class="sxs-lookup"><span data-stu-id="36c32-197">Improved translation around `DateTime` for SQL Server (e.g. [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A), [`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A)).</span></span>
* <span data-ttu-id="36c32-198">Die Übersetzung neuer Methoden in Bytearrays wurde hinzugefügt (z. B. [`Contains`](/dotnet/api/system.linq.enumerable.contains), [`Length`](/dotnet/api/system.array.length), [`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal)).</span><span class="sxs-lookup"><span data-stu-id="36c32-198">Translation of new methods on byte arrays (e.g. [`Contains`](/dotnet/api/system.linq.enumerable.contains), [`Length`](/dotnet/api/system.array.length), [`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal)).</span></span>
* <span data-ttu-id="36c32-199">Die Übersetzung von einigen zusätzlichen bitweisen Operatoren wurde hinzugefügt (z. B. das Zweierkomplement).</span><span class="sxs-lookup"><span data-stu-id="36c32-199">Translation of some additional bitwise operators, such as two's complement.</span></span>
* <span data-ttu-id="36c32-200">Die Übersetzung von `FirstOrDefault` über Zeichenfolgen wurde hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="36c32-200">Translation of `FirstOrDefault` over strings.</span></span>
* <span data-ttu-id="36c32-201">Die Abfrageübersetzung bezüglich der NULL-Semantik wurde verbessert, wodurch effizientere Abfragen ermöglicht werden.</span><span class="sxs-lookup"><span data-stu-id="36c32-201">Improved query translation around null semantics, resulting in tighter, more efficient queries.</span></span>
* <span data-ttu-id="36c32-202">Von Benutzern zugeordnete Funktionen können nun mit Anmerkungen versehen werden, um die NULL-Weitergabe zu steuern, was ebenfalls zu effizienteren Abfragen führt.</span><span class="sxs-lookup"><span data-stu-id="36c32-202">User-mapped functions can now be annotated to control null propagation, again resulting in tighter, more efficient queries.</span></span>
* <span data-ttu-id="36c32-203">SQL-Code mit CASE-Blöcken ist nun deutlich kürzer.</span><span class="sxs-lookup"><span data-stu-id="36c32-203">SQL containing CASE blocks is now considerably more concise.</span></span>
* <span data-ttu-id="36c32-204">Die SQL Server-Funktion [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql) kann nun über die neue Methode [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A) in Abfragen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="36c32-204">The SQL Server [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql) function can now be called in queries via the new [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A) method.</span></span>
* <span data-ttu-id="36c32-205">`EnableDetailedErrors` fügt [zusätzliche Informationen zu Ausnahmen hinzu](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions).</span><span class="sxs-lookup"><span data-stu-id="36c32-205">`EnableDetailedErrors` adds [additional details to exceptions](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions).</span></span>

### <a name="saving"></a><span data-ttu-id="36c32-206">Speichern</span><span class="sxs-lookup"><span data-stu-id="36c32-206">Saving</span></span>

* <span data-ttu-id="36c32-207">[Abfangen](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) und [Ereignisse](xref:core/logging-events-diagnostics/events) von SaveChanges</span><span class="sxs-lookup"><span data-stu-id="36c32-207">SaveChanges [interception](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) and [events](xref:core/logging-events-diagnostics/events).</span></span>
* <span data-ttu-id="36c32-208">Es wurden APIs zum Steuern von [Transaktionssicherungspunkten](xref:core/saving/transactions#savepoints) eingeführt.</span><span class="sxs-lookup"><span data-stu-id="36c32-208">APIs have been introduced for controlling [transaction savepoints](xref:core/saving/transactions#savepoints).</span></span> <span data-ttu-id="36c32-209">Außerdem erstellt EF Core automatisch einen Sicherungspunkt, wenn `SaveChanges` aufgerufen wird und bereits eine Transaktion ausgeführt wird. Im Falle eines Fehlers wird ein Rollback ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="36c32-209">In addition, EF Core will automatically create a savepoint when `SaveChanges` is called and a transaction is already in progress, and roll back to it in case of failure.</span></span>
* <span data-ttu-id="36c32-210">Eine Transaktions-ID kann explizit von der Anwendung festgelegt werden, was eine einfachere Korrelation von Transaktionsereignissen bei der Protokollierung und in anderen Szenarios ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="36c32-210">A transaction ID can be explicitly set by the application, allowing for easier correlation of transaction events in logging and elsewhere.</span></span>
* <span data-ttu-id="36c32-211">Die standardmäßige maximale Batchgröße für SQL Server wurde basierend auf einer Analyse der Batchverarbeitungsleistung in „42“ geändert.</span><span class="sxs-lookup"><span data-stu-id="36c32-211">The default maximum batch size for SQL Server has been changed to 42 based on an analysis of batching performance.</span></span>

### <a name="migrations-and-scaffolding"></a><span data-ttu-id="36c32-212">Migrationen und Gerüstbau</span><span class="sxs-lookup"><span data-stu-id="36c32-212">Migrations and scaffolding</span></span>

* <span data-ttu-id="36c32-213">Tabellen können nun [aus Migrationen ausgeschlossen werden](xref:core/modeling/entity-types#excluding-from-migrations).</span><span class="sxs-lookup"><span data-stu-id="36c32-213">Tables can now be [excluded from migrations](xref:core/modeling/entity-types#excluding-from-migrations).</span></span>
* <span data-ttu-id="36c32-214">Ein neuer [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list)-Befehl zeigt an, welche Migrationen noch nicht auf die Datenbank angewendet wurden ([`Get-Migration`](xref:core/cli/powershell#get-migration) erzielt dasselbe in der Paketverwaltungskonsole).</span><span class="sxs-lookup"><span data-stu-id="36c32-214">A new [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list) command now shows which migrations have not yet been applied to the database ([`Get-Migration`](xref:core/cli/powershell#get-migration) does the same in the Package Management Console).</span></span>
* <span data-ttu-id="36c32-215">Migrationsskripts enthalten nun ggf.Transaktionsanweisungen, um die Verarbeitung von Fällen zu verbessern, in denen die Migrationsanwendung nicht erfolgreich ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="36c32-215">Migrations scripts now contain transaction statements where appropriate to improve handling cases where migration application fails.</span></span>
* <span data-ttu-id="36c32-216">Die Spalten für nicht zugeordnete Basisklassen sind nun nach anderen Spalten für zugeordnete Entitätstypen angeordnet.</span><span class="sxs-lookup"><span data-stu-id="36c32-216">The columns for unmapped base classes are now ordered after other columns for mapped entity types.</span></span> <span data-ttu-id="36c32-217">Beachten Sie, dass sich dies nur auf neu erstellte Tabellen auswirkt. Die Spaltenreihenfolge für vorhandene Tabellen bleibt unverändert.</span><span class="sxs-lookup"><span data-stu-id="36c32-217">Note this only impacts newly created tables; the column order for existing tables remains unchanged.</span></span>
* <span data-ttu-id="36c32-218">Die Migrationsgeneration kann nun erkennen, wenn die Migration als idempotent generiert wird und ob die Ausgabe sofort ausgeführt oder als Skript generiert wird.</span><span class="sxs-lookup"><span data-stu-id="36c32-218">Migration generation can now be aware if the migration being generated is idempotent, and whether the output will be executed immediately or generated as a script.</span></span>
* <span data-ttu-id="36c32-219">Es wurden neue Befehlszeilenparameter zum Festlegen von Namespaces in [Migrationen](xref:core/managing-schemas/migrations/index#namespaces) und im [Gerüstbau](xref:core/managing-schemas/scaffolding#directories-and-namespaces) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="36c32-219">New command-line parameters have been added for specifying namespaces in [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [scaffolding](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span></span>
* <span data-ttu-id="36c32-220">Der Befehl [dotnet ef database update](xref:core/cli/dotnet#dotnet-ef-database-update) akzeptiert nun einen neuen `--connection`-Parameter zum Festlegen der Verbindungszeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="36c32-220">The [dotnet ef database update](xref:core/cli/dotnet#dotnet-ef-database-update) command now accepts a new `--connection` parameter for specifying the connection string.</span></span>
* <span data-ttu-id="36c32-221">Der Gerüstbau für vorhandene Datenbanken formuliert Tabellennamen nun im Singular. Für Tabellen mit den Namen `People` und `Addresses` werden nun beispielsweise Entitätstypen namens `Person` und `Address` erstellt.</span><span class="sxs-lookup"><span data-stu-id="36c32-221">Scaffolding existing databases now singularizes table names, so tables named `People` and `Addresses` will be scaffolded to entity types called `Person` and `Address`.</span></span> <span data-ttu-id="36c32-222">[Die ursprünglichen Datenbanknamen können weiterhin beibehalten werden](xref:core/managing-schemas/scaffolding#preserving-names).</span><span class="sxs-lookup"><span data-stu-id="36c32-222">[Original database names can still be preserved](xref:core/managing-schemas/scaffolding#preserving-names).</span></span>
* <span data-ttu-id="36c32-223">Die neue Option [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) kann EF Core dazu anweisen, `OnModelConfiguring` beim Gerüstbau eines Modells auszuschließen.</span><span class="sxs-lookup"><span data-stu-id="36c32-223">The new [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) option can instruct EF Core to exclude `OnModelConfiguring` when scaffolding a model.</span></span>

### <a name="cosmos"></a><span data-ttu-id="36c32-224">Cosmos</span><span class="sxs-lookup"><span data-stu-id="36c32-224">Cosmos</span></span>

* <span data-ttu-id="36c32-225">Die [Cosmos-Verbindungseinstellungen](xref:core/providers/cosmos/index#cosmos-options) wurden erweitert.</span><span class="sxs-lookup"><span data-stu-id="36c32-225">[Cosmos connection settings](xref:core/providers/cosmos/index#cosmos-options) have been expanded.</span></span>
* <span data-ttu-id="36c32-226">Die optimistische Nebenläufigkeit wird nun [über die Verwendung von ETags für Cosmos unterstützt](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags).</span><span class="sxs-lookup"><span data-stu-id="36c32-226">Optimistic concurrency is now [supported on Cosmos via the use of ETags](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags).</span></span>
* <span data-ttu-id="36c32-227">Die neue `WithPartitionKey`-Methode ermöglicht, dass der Cosmos-[Partitionsschlüssel](xref:core/providers/cosmos/index#partition-keys) sowohl im Modell als auch in Abfragen enthalten sein kann.</span><span class="sxs-lookup"><span data-stu-id="36c32-227">The new `WithPartitionKey` method allows the Cosmos [partition key](xref:core/providers/cosmos/index#partition-keys) to be included both in the model and in queries.</span></span>
* <span data-ttu-id="36c32-228">Die Zeichenfolgenmethoden [`Contains`](/dotnet/api/system.string.contains), [`StartsWith`](/dotnet/api/system.string.startswith) und [`EndsWith`](/dotnet/api/system.string.endswith) werden nun für Cosmos übersetzt.</span><span class="sxs-lookup"><span data-stu-id="36c32-228">The string methods [`Contains`](/dotnet/api/system.string.contains), [`StartsWith`](/dotnet/api/system.string.startswith) and [`EndsWith`](/dotnet/api/system.string.endswith) are now translated for Cosmos.</span></span>
* <span data-ttu-id="36c32-229">Der C#-Operator `is` wird nun für Cosmos übersetzt.</span><span class="sxs-lookup"><span data-stu-id="36c32-229">The C# `is` operator is now translated on Cosmos.</span></span>

### <a name="sqlite"></a><span data-ttu-id="36c32-230">SQLite</span><span class="sxs-lookup"><span data-stu-id="36c32-230">Sqlite</span></span>

* <span data-ttu-id="36c32-231">Berechnete Spalten werden nun unterstützt.</span><span class="sxs-lookup"><span data-stu-id="36c32-231">Computed columns are now supported.</span></span>
* <span data-ttu-id="36c32-232">Das Abrufen von Binär- und Zeichenfolgendaten mit GetBytes, GetChars und GetTextReader ist nun effizienter durch die Verwendung von SqliteBlob und Datenströmen.</span><span class="sxs-lookup"><span data-stu-id="36c32-232">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="36c32-233">Die Initialisierung von SqliteConnection ist nun verzögert.</span><span class="sxs-lookup"><span data-stu-id="36c32-233">Initialization of SqliteConnection is now lazy.</span></span>

### <a name="other"></a><span data-ttu-id="36c32-234">Andere</span><span class="sxs-lookup"><span data-stu-id="36c32-234">Other</span></span>

* <span data-ttu-id="36c32-235">Nun können Proxys für die Änderungsnachverfolgung generiert werden, die automatisch [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) und [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) implementieren.</span><span class="sxs-lookup"><span data-stu-id="36c32-235">Change-tracking proxies can be generated that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span></span> <span data-ttu-id="36c32-236">Dies bietet einen alternativen Ansatz für die Änderungsnachverfolgung, die nicht nach Änderungen sucht, wenn `SaveChanges` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="36c32-236">This provides an alternative approach to change-tracking that doesn't scan for changes when `SaveChanges` is called.</span></span>
* <span data-ttu-id="36c32-237">Eine <xref:System.Data.Common.DbConnection>-Klasse oder Verbindungszeichenfolge kann nun für eine bereits initialisierte DbContext-Instanz geändert werden.</span><span class="sxs-lookup"><span data-stu-id="36c32-237">A <xref:System.Data.Common.DbConnection> or connection string can now be changed on an already-initialized DbContext.</span></span>
* <span data-ttu-id="36c32-238">Die neue Methode <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType>.(#Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) löscht die DbContext-Instanzen aller überwachten Entitäten.</span><span class="sxs-lookup"><span data-stu-id="36c32-238">The new <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType>.0#Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) method clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="36c32-239">Dies sollte normalerweise nicht erforderlich sein, wenn die bewährte Methode zum Erstellen einer neuen kurzlebigen Kontextinstanz für jede Arbeitseinheit angewandt wird.</span><span class="sxs-lookup"><span data-stu-id="36c32-239">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="36c32-240">Wenn der Zustand einer DbContext-Instanz jedoch zurückgesetzt werden muss, ist die Verwendung der neuen `Clear()`-Methode effizienter und robuster als ein Vorgang zum Trennen aller Entitäten.</span><span class="sxs-lookup"><span data-stu-id="36c32-240">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more efficient and robust than mass-detaching all entities.</span></span>
* <span data-ttu-id="36c32-241">Die Befehlszeilentools von EF Core konfigurieren nun die `ASPNETCORE_ENVIRONMENT`- _und_ `DOTNET_ENVIRONMENT`-Umgebungsvariablen automatisch als „Entwicklung“.</span><span class="sxs-lookup"><span data-stu-id="36c32-241">The EF Core command line tools now automatically configure the `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` environment variables to "Development".</span></span> <span data-ttu-id="36c32-242">Dadurch wird die Benutzeroberfläche bei der Verwendung des generischen Hosts mit der Benutzeroberfläche für die Entwicklung von ASP.NET Core in Einklang gebracht.</span><span class="sxs-lookup"><span data-stu-id="36c32-242">This brings the experience when using the generic host in line with the experience for ASP.NET Core during development.</span></span>
* <span data-ttu-id="36c32-243">Benutzerdefinierte Befehlszeilenargumente können nun an <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601> übertragen werden, sodass Anwendungen steuern können, wie der Kontext erstellt und initialisiert wird.</span><span class="sxs-lookup"><span data-stu-id="36c32-243">Custom command-line arguments can be flowed into <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601>, allowing applications to control how the context is created and initialized.</span></span>
* <span data-ttu-id="36c32-244">Der Füllfaktor für den Index kann nun [in SQL Server konfiguriert](xref:core/providers/sql-server/indexes#fill-factor) werden.</span><span class="sxs-lookup"><span data-stu-id="36c32-244">The index fill factor can now be [configured on SQL Server](xref:core/providers/sql-server/indexes#fill-factor).</span></span>
* <span data-ttu-id="36c32-245">Die neue Eigenschaft <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A> kann dazu verwendet werden, zu unterscheiden, ob ein relationaler oder nicht relationaler Anbieter verwendet wird (z. B. InMemory).</span><span class="sxs-lookup"><span data-stu-id="36c32-245">The new <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A> property can be used to distinguish when using a relational provider and a non-relation provider (such as InMemory).</span></span>
