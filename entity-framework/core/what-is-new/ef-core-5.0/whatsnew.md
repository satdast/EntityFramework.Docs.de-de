---
title: Neuerungen in EF Core 5.0
description: Übersicht über neue Features in EF Core 5.0
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 7737a143b9478412cdafe3c2fe5e8d7a106c9a3f
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635496"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="66615-103">Neuerungen in EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="66615-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="66615-104">Alle für EF Core 5.0 geplanten Features sind jetzt verfügbar.</span><span class="sxs-lookup"><span data-stu-id="66615-104">All features planned for EF Core 5.0 have now been completed.</span></span> <span data-ttu-id="66615-105">Diese Seite bietet eine Übersicht über interessante Änderungen in den einzelnen Vorschauversionen.</span><span class="sxs-lookup"><span data-stu-id="66615-105">This page contains an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="66615-106">Auf dieser Seite wird der [Plan für EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan) nicht erneut aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="66615-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="66615-107">Der Plan beschreibt die allgemeinen Themen für EF Core 5.0 einschließlich sämtlicher Features, die wir vor Auslieferung des finalen Releases integrieren möchten.</span><span class="sxs-lookup"><span data-stu-id="66615-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

## <a name="rc1"></a><span data-ttu-id="66615-108">RC1</span><span class="sxs-lookup"><span data-stu-id="66615-108">RC1</span></span>

### <a name="many-to-many"></a><span data-ttu-id="66615-109">M:n</span><span class="sxs-lookup"><span data-stu-id="66615-109">Many-to-many</span></span>

<span data-ttu-id="66615-110">EF Core 5.0 unterstützt m:n-Beziehungen ohne explizite Zuordnung der Jointabelle.</span><span class="sxs-lookup"><span data-stu-id="66615-110">EF Core 5.0 supports many-to-many relationships without explicitly mapping the join table.</span></span>

<span data-ttu-id="66615-111">Betrachten Sie beispielsweise die folgenden Entitätstypen:</span><span class="sxs-lookup"><span data-stu-id="66615-111">For example, consider these entity types:</span></span>

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

<span data-ttu-id="66615-112">`Post` enthält eine `Tags`-Auflistung, und `Tag` enthält eine `Posts`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="66615-112">Notice that `Post` contains a collection of `Tags`, and `Tag` contains a collection of `Posts`.</span></span> <span data-ttu-id="66615-113">EF Core 5.0 erkennt dies gemäß Konvention als m:n-Beziehung.</span><span class="sxs-lookup"><span data-stu-id="66615-113">EF Core 5.0 recognizes this as a many-to-many relationship by convention.</span></span> <span data-ttu-id="66615-114">Das bedeutet, dass in `OnModelCreating` kein Code erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="66615-114">This means no code is required in `OnModelCreating`:</span></span>

```csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }
}
```

<span data-ttu-id="66615-115">Wenn zum Erstellen der Datenbank Migrationen (oder `EnsureCreated`) verwendet werden, erstellt EF Core automatisch die Jointabelle.</span><span class="sxs-lookup"><span data-stu-id="66615-115">When Migrations (or `EnsureCreated`) are used to create the database, EF Core will automatically create the join table.</span></span> <span data-ttu-id="66615-116">Beispielsweise generiert EF Core unter SQL Server für dieses Modell Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66615-116">For example, on SQL Server for this model, EF Core generates:</span></span>

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

<span data-ttu-id="66615-117">Wenn `Tag`- und `Post`-Entitäten erstellt und zugeordnet werden, wird die Jointabelle automatisch aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="66615-117">Creating and associating `Tag` and `Post` entities results in join table updates happening automatically.</span></span> <span data-ttu-id="66615-118">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-118">For example:</span></span>

```csharp
var beginnerTag = new Tag {Text = "Beginner"};
var advancedTag = new Tag {Text = "Advanced"};
var efCoreTag = new Tag {Text = "EF Core"};

context.AddRange(
    new Post {Name = "EF Core 101", Tags = new List<Tag> {beginnerTag, efCoreTag}},
    new Post {Name = "Writing an EF database provider", Tags = new List<Tag> {advancedTag, efCoreTag}},
    new Post {Name = "Savepoints in EF Core", Tags = new List<Tag> {beginnerTag, efCoreTag}});

context.SaveChanges();
```

<span data-ttu-id="66615-119">Nach dem Einfügen von Beiträgen und Tags erstellt EF automatisch Zeilen in der Jointabelle.</span><span class="sxs-lookup"><span data-stu-id="66615-119">After inserting the Posts and Tags, EF will then automatically create rows in the join table.</span></span> <span data-ttu-id="66615-120">Beispielsweise unter SQL Server:</span><span class="sxs-lookup"><span data-stu-id="66615-120">For example, on SQL Server:</span></span>

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

<span data-ttu-id="66615-121">Bei Abfragen funktionieren Include- und andere Abfragevorgänge wie bei jeder anderen Beziehung.</span><span class="sxs-lookup"><span data-stu-id="66615-121">For queries, Include and other query operations work just like for any other relationship.</span></span> <span data-ttu-id="66615-122">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-122">For example:</span></span>

```csharp
foreach (var post in context.Posts.Include(e => e.Tags))
{
    Console.Write($"Post \"{post.Name}\" has tags");

    foreach (var tag in post.Tags)
    {
        Console.Write($" '{tag.Text}'");
    }
}
```

<span data-ttu-id="66615-123">Die generierte SQL-Anweisung verwendet automatisch die Jointabelle, um alle zugehörigen Tags wiederherzustellen:</span><span class="sxs-lookup"><span data-stu-id="66615-123">The SQL generated uses the join table automatically to bring back all related Tags:</span></span>

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

<span data-ttu-id="66615-124">Im Gegensatz zu EF6 kann bei EF Core die Jointabelle umfassend angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="66615-124">Unlike EF6, EF Core allows full customization of the join table.</span></span> <span data-ttu-id="66615-125">Mit dem folgenden Code wird beispielsweise eine m:n-Beziehung konfiguriert, die auch Navigationen zur Joinentität aufweist und bei der die Joinentität eine Nutzdateneigenschaft enthält:</span><span class="sxs-lookup"><span data-stu-id="66615-125">For example, the code below configures a many-to-many relationship that also has navigations to the join entity, and in which the join entity contains a payload property:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Post>()
        .HasMany(p => p.Tags)
        .WithMany(p => p.Posts)
        .UsingEntity<PostTag>(
            j => j
                .HasOne(pt => pt.Tag)
                .WithMany()
                .HasForeignKey(pt => pt.TagId),
            j => j
                .HasOne(pt => pt.Post)
                .WithMany()
                .HasForeignKey(pt => pt.PostId),
            j =>
            {
                j.Property(pt => pt.PublicationDate).HasDefaultValueSql("CURRENT_TIMESTAMP");
                j.HasKey(t => new { t.PostId, t.TagId });
            });
}
```

> [!NOTE]
> <span data-ttu-id="66615-126">Der Gerüstbau für m:n-Beziehungen anhand der Datenbank wird noch nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="66615-126">Support for scaffolding many-to-many relationships from the database is not yet added.</span></span> <span data-ttu-id="66615-127">Siehe [Nachverfolgung von Issue](https://github.com/dotnet/efcore/issues/22475).</span><span class="sxs-lookup"><span data-stu-id="66615-127">See [tracking issue](https://github.com/dotnet/efcore/issues/22475).</span></span>

### <a name="map-entity-types-to-queries"></a><span data-ttu-id="66615-128">Zuordnen von Entitätstypen zu Abfragen</span><span class="sxs-lookup"><span data-stu-id="66615-128">Map entity types to queries</span></span>

<span data-ttu-id="66615-129">Entitätstypen werden häufig Tabellen oder Sichten zugeordnet, sodass EF Core den Inhalt der Tabelle oder der Sicht zurückgibt, wenn dieser Typ abgefragt wird.</span><span class="sxs-lookup"><span data-stu-id="66615-129">Entity types are commonly mapped to tables or views such that EF Core will pull back the contents of the table or view when querying for that type.</span></span> <span data-ttu-id="66615-130">In EF Core 5.0 kann ein Entitätstyp einer „definierenden Abfrage“ zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="66615-130">EF Core 5.0 allows an entity type to mapped to a "defining query".</span></span> <span data-ttu-id="66615-131">(Diese Funktion wurde in früheren Versionen teilweise unterstützt, wurde jedoch wesentlich verbessert und weist in EF Core 5.0 eine andere Syntax auf.)</span><span class="sxs-lookup"><span data-stu-id="66615-131">(This was partially supported in previous versions, but is much improved and has different syntax in EF Core 5.0.)</span></span>

<span data-ttu-id="66615-132">Stellen Sie sich beispielsweise zwei Tabellen vor: eine mit modernen Beiträgen, die andere mit Legacybeiträgen.</span><span class="sxs-lookup"><span data-stu-id="66615-132">For example, consider two tables; one with modern posts; the other with legacy posts.</span></span> <span data-ttu-id="66615-133">Die Tabelle mit modernen Beiträgen enthält einige zusätzliche Spalten. Für Ihre Anwendung sollen jedoch moderne Beiträge und Legacybeiträge vereint und mit allen erforderlichen Eigenschaften einem Entitätstyp zugeordnet werden:</span><span class="sxs-lookup"><span data-stu-id="66615-133">The modern posts table has some additional columns, but for the purpose of our application we want both modern and legacy posts to be combined and mapped to an entity type with all necessary properties:</span></span>

```csharp
public class Post
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Category { get; set; }
    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

<span data-ttu-id="66615-134">In EF Core 5.0 kann `ToSqlQuery` verwendet werden, um diesen Entitätstyp einer Abfrage zuzuordnen, die Zeilen aus beiden Tabellen abruft und vereint:</span><span class="sxs-lookup"><span data-stu-id="66615-134">In EF Core 5.0, `ToSqlQuery` can be used to map this entity type to a query that pulls and combines rows from both tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");
}
```

<span data-ttu-id="66615-135">Die Tabelle `legacy_posts` enthält keine `Category`-Spalte. Daher wird stattdessen für alle Legacybeiträge ein Standardwert synthetisiert.</span><span class="sxs-lookup"><span data-stu-id="66615-135">Notice that the `legacy_posts` table does not have a `Category` column, so we instead synthesize a default value for all legacy posts.</span></span>

<span data-ttu-id="66615-136">Dieser Entitätstyp kann dann auf übliche Weise für LINQ-Abfragen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66615-136">This entity type can then be used in the normal way for LINQ queries.</span></span> <span data-ttu-id="66615-137">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-137">For example.</span></span> <span data-ttu-id="66615-138">LINQ-Abfrage:</span><span class="sxs-lookup"><span data-stu-id="66615-138">the LINQ query:</span></span>

```csharp
var posts = context.Posts.Where(e => e.Blog.Name.Contains("Unicorn")).ToList();
```

<span data-ttu-id="66615-139">Generiert den folgenden SQL-Code in SQLite:</span><span class="sxs-lookup"><span data-stu-id="66615-139">Generates the following SQL on SQLite:</span></span>

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

<span data-ttu-id="66615-140">Die für den Entitätstyp konfigurierte Abfrage dient als Ausgangspunkt für die Erstellung der gesamten LINQ-Abfrage.</span><span class="sxs-lookup"><span data-stu-id="66615-140">Notice that the query configured for the entity type is used as a starting for composing the full LINQ query.</span></span>

### <a name="event-counters"></a><span data-ttu-id="66615-141">Ereignisindikatoren</span><span class="sxs-lookup"><span data-stu-id="66615-141">Event counters</span></span>

<span data-ttu-id="66615-142">Mit [.NET-Ereignisindikatoren](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) können Leistungsmetriken aus einer Anwendung effizient verfügbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="66615-142">[.NET event counters](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) are a way to efficiently expose performance metrics from an application.</span></span> <span data-ttu-id="66615-143">Bei EF Core 5.0 befinden sich die Ereignisindikatoren in der Kategorie `Microsoft.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="66615-143">EF Core 5.0 includes event counters under the `Microsoft.EntityFrameworkCore` category.</span></span> <span data-ttu-id="66615-144">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-144">For example:</span></span>

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496
```

<span data-ttu-id="66615-145">Damit werden dotnet-Indikatoren aufgefordert, mit der Sammlung von EF Core-Ereignissen für Prozess 49496 zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="66615-145">This tells dotnet counters to start collecting EF Core events for process 49496.</span></span> <span data-ttu-id="66615-146">Dadurch wird in der Konsole eine Ausgabe wie die Folgende generiert:</span><span class="sxs-lookup"><span data-stu-id="66615-146">This generates output like this in the console:</span></span>

```console
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

### <a name="property-bags"></a><span data-ttu-id="66615-147">Eigenschaftenbehälter</span><span class="sxs-lookup"><span data-stu-id="66615-147">Property bags</span></span>

<span data-ttu-id="66615-148">Bei EF Core 5.0 kann ein CLR-Typ verschiedenen Entitätstypen zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="66615-148">EF Core 5.0 allows the same CLR type to be mapped to multiple different entity types.</span></span> <span data-ttu-id="66615-149">Diese Typen werden als „Entitätstypen mit gemeinsamen Typen“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="66615-149">Such types are known as shared-type entity types.</span></span> <span data-ttu-id="66615-150">Dieses Feature in Kombination mit Indexereigenschaften (in der Vorschauversion 1 enthalten) ermöglicht die Verwendung von Eigenschaftenbehältern als Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="66615-150">This feature combined with indexer properties (included in preview 1) allows property bags to be used as entity type.</span></span>

<span data-ttu-id="66615-151">Beispielsweise wird im Folgenden mit DbContext der BCL-Typ `Dictionary<string, object>` als Entitätstyp mit gemeinsamem Typ sowohl für Produkte als auch für Kategorien verwendet.</span><span class="sxs-lookup"><span data-stu-id="66615-151">For example, the DbContext below configures the BCL type `Dictionary<string, object>` as a shared-type entity type for both products and categories.</span></span>

```csharp
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

<span data-ttu-id="66615-152">Wörterbuchobjekte („Eigenschaftenbehälter“) können nun dem Kontext als Entitätsinstanzen hinzugefügt und gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="66615-152">Dictionary objects ("property bags") can now be added to the context as entity instances and saved.</span></span> <span data-ttu-id="66615-153">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-153">For example:</span></span>

```csharp
var beverages = new Dictionary<string, object>
{
    ["Name"] = "Beverages",
    ["Description"] = "Stuff to sip on"
};

context.Categories.Add(beverages);

context.SaveChanges();
```

<span data-ttu-id="66615-154">Diese Entitäten können dann auf übliche Weise abgefragt und aktualisiert werden:</span><span class="sxs-lookup"><span data-stu-id="66615-154">These entities can then be queried and updated in the normal way:</span></span>

```csharp
var foods = context.Categories.Single(e => e["Name"] == "Foods");
var marmite = context.Products.Single(e => e["Name"] == "Marmite");

marmite["CategoryId"] = foods["Id"];
marmite["Description"] = "Yummy when spread _thinly_ on buttered Toast!";

context.SaveChanges();
```

### <a name="savechanges-interception-and-events"></a><span data-ttu-id="66615-155">SaveChanges-Interceptor und -Ereignisse</span><span class="sxs-lookup"><span data-stu-id="66615-155">SaveChanges interception and events</span></span>

<span data-ttu-id="66615-156">Mit EF Core 5.0 wurden .NET-Ereignisse und ein EF Core-Interceptor eingeführt, der beim Aufrufen von SaveChanges ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="66615-156">EF Core 5.0 introduces both .NET events and an EF Core interceptor triggered when SaveChanges is called.</span></span>

<span data-ttu-id="66615-157">Die Ereignisse sind einfach zu verwenden. Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-157">The events are simple to use; for example:</span></span>

```csharp
context.SavingChanges += (sender, args) =>
{
    Console.WriteLine($"Saving changes for {((DbContext)sender).Database.GetConnectionString()}");
};

context.SavedChanges += (sender, args) =>
{
    Console.WriteLine($"Saved {args.EntitiesSavedCount} changes for {((DbContext)sender).Database.GetConnectionString()}");
};
```

<span data-ttu-id="66615-158">Beachten Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66615-158">Notice that:</span></span>

* <span data-ttu-id="66615-159">Der Ereignissender ist die `DbContext`-Instanz.</span><span class="sxs-lookup"><span data-stu-id="66615-159">The event sender is the `DbContext` instance</span></span>
* <span data-ttu-id="66615-160">Die Argumente für das `SavedChanges`-Ereignis enthalten die Anzahl der in der Datenbank gespeicherten Entitäten.</span><span class="sxs-lookup"><span data-stu-id="66615-160">The args for the `SavedChanges` event contains the number of entities saved to the database</span></span>

<span data-ttu-id="66615-161">Der Interceptor wird zwar durch `ISaveChangesInterceptor` definiert. Häufig ist es jedoch bequemer, wenn von `SaveChangesInterceptor` geerbt und so vermieden wird, dass jede einzelne Methode implementiert wird.</span><span class="sxs-lookup"><span data-stu-id="66615-161">The interceptor is defined by `ISaveChangesInterceptor`, but it is often convenient to inherit from `SaveChangesInterceptor` to avoid implementing every method.</span></span> <span data-ttu-id="66615-162">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-162">For example:</span></span>

```csharp
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

<span data-ttu-id="66615-163">Beachten Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66615-163">Notice that:</span></span>

* <span data-ttu-id="66615-164">Der Interceptor arbeitet mit synchronen und asynchronen Methoden.</span><span class="sxs-lookup"><span data-stu-id="66615-164">The interceptor has both sync and async methods.</span></span> <span data-ttu-id="66615-165">Das kann nützlich sein, wenn Sie asynchrone E/A-Vorgänge wie Schreibvorgänge auf einem Überwachungsserver durchführen müssen.</span><span class="sxs-lookup"><span data-stu-id="66615-165">This can be useful if you need to perform async I/O, such as writing to an audit server.</span></span>
* <span data-ttu-id="66615-166">Mit dem Interceptor kann SaveChanges mithilfe des in allen Interceptors vorhandenen `InterceptionResult`-Mechanismus übersprungen werden.</span><span class="sxs-lookup"><span data-stu-id="66615-166">The interceptor allows SaveChanges to be skipped using the `InterceptionResult` mechanism common to all interceptors.</span></span>

<span data-ttu-id="66615-167">Interceptors haben den Nachteil, dass sie bei der Erstellung bei DbContext registriert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="66615-167">The downside of interceptors is that they must be registered on the DbContext when it is being constructed.</span></span> <span data-ttu-id="66615-168">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-168">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .AddInterceptors(new MySaveChangesInterceptor())
        .UseSqlite("Data Source = test.db");
```

<span data-ttu-id="66615-169">Im Gegensatz dazu können Ereignisse jederzeit in der DbContext-Instanz registriert werden.</span><span class="sxs-lookup"><span data-stu-id="66615-169">In contrast, the events can be registered on the DbContext instance at any time.</span></span>

### <a name="exclude-tables-from-migrations"></a><span data-ttu-id="66615-170">Ausschließen von Tabellen aus Migrationen</span><span class="sxs-lookup"><span data-stu-id="66615-170">Exclude tables from migrations</span></span>

<span data-ttu-id="66615-171">In manchen Fällen ist es sinnvoll, wenn ein einzelner Entitätstyp in mehreren DbContext-Objekten zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="66615-171">It is sometimes useful to have a single entity type mapped in multiple DbContexts.</span></span> <span data-ttu-id="66615-172">Dies ist vor allem dann der Fall, wenn [Kontextgrenzen](https://www.martinfowler.com/bliki/BoundedContext.html) verwendet werden, bei denen üblicherweise für jede Kontextgrenze ein anderer DbContext-Typ verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="66615-172">This is especially true when using [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html), for which it is common to have a different DbContext type for each bounded context.</span></span>

<span data-ttu-id="66615-173">So kann beispielsweise ein `User`-Typ von einem Autorisierungskontext und einem Berichtskontext benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="66615-173">For example, a `User` type may be needed by both an authorization context and a reporting context.</span></span> <span data-ttu-id="66615-174">Wenn am `User`-Typ eine Änderung vorgenommen wird, wird bei der Migration für beide DbContext-Objekte versucht, die Datenbank zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="66615-174">If a change is made to the `User` type, then migrations for both DbContexts will attempt to update the database.</span></span> <span data-ttu-id="66615-175">Um dies zu verhindern, kann das Modell für eines der Kontextobjekte so konfiguriert werden, dass die Tabelle aus der jeweiligen Migration ausgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="66615-175">To prevent this, the model for one of the contexts can be configured to exclude the table from its migrations.</span></span>

<span data-ttu-id="66615-176">Im folgenden Code generiert der `AuthorizationContext` Migrationen für Änderungen an der `Users`-Tabelle, der `ReportingContext` jedoch nicht. Dadurch wird verhindert, dass die Migrationen kollidieren.</span><span class="sxs-lookup"><span data-stu-id="66615-176">In the code below, the `AuthorizationContext` will generate migrations for changes to the `Users` table, but the `ReportingContext` will not, preventing the migrations from clashing.</span></span>

```csharp
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

### <a name="required-11-dependents"></a><span data-ttu-id="66615-177">Erforderliche 1:1-Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="66615-177">Required 1:1 dependents</span></span>

<span data-ttu-id="66615-178">Bei EF Core 3.1 war das abhängige Ende einer 1:1-Beziehung immer optional.</span><span class="sxs-lookup"><span data-stu-id="66615-178">In EF Core 3.1, the dependent end of a one-to-one relationship was always considered optional.</span></span> <span data-ttu-id="66615-179">Bei der Verwendung von nicht eigenständigen Entitäten wurde das besonders deutlich.</span><span class="sxs-lookup"><span data-stu-id="66615-179">This was most apparent when using owned entities.</span></span> <span data-ttu-id="66615-180">Betrachten Sie beispielsweise das folgende Modell und die folgende Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="66615-180">For example, consider the following model and configuration:</span></span>

```csharp
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

```csharp
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

<span data-ttu-id="66615-181">Dies führt zu Migrationen, bei denen für SQLite die folgende Tabelle erstellt wird:</span><span class="sxs-lookup"><span data-stu-id="66615-181">This results in Migrations creating the following table for SQLite:</span></span>

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

<span data-ttu-id="66615-182">Beachten Sie, dass alle Spalten NULL-Werte zulassen, auch wenn einige der `HomeAddress`-Eigenschaften als erforderlich konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="66615-182">Notice that all the columns are nullable, even though some of the `HomeAddress` properties have been configured as required.</span></span> <span data-ttu-id="66615-183">Wenn beim Abfragen einer `Person` alle Spalten für die private oder geschäftliche Adresse NULL sind, werden die `HomeAddress`- und/oder `WorkAddress`-Eigenschaften von EF Core als NULL belassen, anstatt eine leere Instanz von `Address` festzulegen.</span><span class="sxs-lookup"><span data-stu-id="66615-183">Also, when querying for a `Person`, if all the columns for either the home or work address are null, then EF Core will leave the `HomeAddress` and/or `WorkAddress` properties as null, rather than setting an empty instance of `Address`.</span></span>

<span data-ttu-id="66615-184">Bei EF Core 5.0 kann die `HomeAddress`-Navigation nun als erforderliche Abhängigkeit konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="66615-184">In EF Core 5.0, the `HomeAddress` navigation can now be configured as as a required dependent.</span></span> <span data-ttu-id="66615-185">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-185">For example:</span></span>

```csharp
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

<span data-ttu-id="66615-186">Die von Migrationen erstellte Tabelle enthält jetzt Non-Nullable-Spalten für die erforderlichen Eigenschaften der erforderlichen Abhängigkeit:</span><span class="sxs-lookup"><span data-stu-id="66615-186">The table created by Migrations will now included non-nullable columns for the required properties of the required dependent:</span></span>

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

<span data-ttu-id="66615-187">Außerdem löst EF Core jetzt eine Ausnahme aus, wenn versucht wird, einen Besitzer zu speichern, der eine Abhängigkeit aufweist, die einen NULL-Wert erfordert.</span><span class="sxs-lookup"><span data-stu-id="66615-187">In addition, EF Core will now throw an exception if an attempt is made to save an owner which has a null required dependent.</span></span> <span data-ttu-id="66615-188">In diesem Beispiel löst EF Core eine Ausnahme aus, wenn versucht wird, eine `Person` mit einer `HomeAddress` mit einem NULL-Wert zu speichern.</span><span class="sxs-lookup"><span data-stu-id="66615-188">In this example, EF Core will throw when attempting to save a `Person` with a null `HomeAddress`.</span></span>

<span data-ttu-id="66615-189">Und schließlich erstellt EF Core weiterhin eine Instanz einer erforderlichen Abhängigkeit, auch wenn alle Spalten für die erforderliche Abhängigkeit NULL-Werte aufweisen.</span><span class="sxs-lookup"><span data-stu-id="66615-189">Finally, EF Core will still create an instance of a required dependent even when all the columns for the required dependent have null values.</span></span>

### <a name="options-for-migration-generation"></a><span data-ttu-id="66615-190">Optionen für die Migrationserstellung</span><span class="sxs-lookup"><span data-stu-id="66615-190">Options for migration generation</span></span>

<span data-ttu-id="66615-191">Ab EF Core 5.0 haben Sie eine bessere Kontrolle über die Erstellung von Migrationen für unterschiedliche Zwecke.</span><span class="sxs-lookup"><span data-stu-id="66615-191">EF Core 5.0 introduces greater control over generation of migrations for different purposes.</span></span> <span data-ttu-id="66615-192">Dazu gehören folgenden Funktionen:</span><span class="sxs-lookup"><span data-stu-id="66615-192">This includes the ability to:</span></span>

* <span data-ttu-id="66615-193">Erkennen, ob die Migration für ein Skript oder für eine sofortige Ausführung erstellt wird</span><span class="sxs-lookup"><span data-stu-id="66615-193">Know if the migration is being generated for a script or for immediate execution</span></span>
* <span data-ttu-id="66615-194">Erkennen, ob ein idempotentes Skript erstellt wird</span><span class="sxs-lookup"><span data-stu-id="66615-194">Know if an idempotent script is being generated</span></span>
* <span data-ttu-id="66615-195">Erkennen, ob das Skript Transaktionsanweisungen ausschließen soll (Informationen hierzu finden Sie unter _Migrationsskripts bei Transaktionen_ weiter unten)</span><span class="sxs-lookup"><span data-stu-id="66615-195">Know if the script should exclude transaction statements (See _Migrations scripts with transactions_ below.)</span></span>

<span data-ttu-id="66615-196">Dieses Verhalten wird durch eine `MigrationsSqlGenerationOptions`-Enumeration angegeben, die nun an `IMigrator.GenerateScript`übergeben werden kann.</span><span class="sxs-lookup"><span data-stu-id="66615-196">This behavior is specified by an the `MigrationsSqlGenerationOptions` enum, which can now be passed to `IMigrator.GenerateScript`.</span></span>

<span data-ttu-id="66615-197">Zudem ermöglicht diese Version bei Bedarf eine bessere Erstellung von idempotenten Skripts mit Aufrufen von `EXEC` unter SQL Server.</span><span class="sxs-lookup"><span data-stu-id="66615-197">Also included in this work is better generation of idempotent scripts with calls to `EXEC` on SQL Server when needed.</span></span> <span data-ttu-id="66615-198">Diese Version enthält darüber hinaus auch ähnliche Verbesserungen an den Skripts, die von anderen Datenbankanbietern wie PostgreSQL erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="66615-198">This work also enables similar improvements to the scripts generated by other database providers, including PostgreSQL.</span></span>

### <a name="migrations-scripts-with-transactions"></a><span data-ttu-id="66615-199">Migrationsskripts bei Transaktionen</span><span class="sxs-lookup"><span data-stu-id="66615-199">Migrations scripts with transactions</span></span>

<span data-ttu-id="66615-200">Über Migrationen erstellte SQL-Skripts enthalten nun Anweisungen zum Starten und Committen von Transaktionen für die Migration.</span><span class="sxs-lookup"><span data-stu-id="66615-200">SQL scripts generated from migrations now contain statements to begin and commit transactions as appropriate for the migration.</span></span> <span data-ttu-id="66615-201">So wurde das folgende Migrationsskript beispielsweise über zwei Migrationen erstellt.</span><span class="sxs-lookup"><span data-stu-id="66615-201">For example, the migration script below was generated from two migrations.</span></span> <span data-ttu-id="66615-202">Beachten Sie, dass nun jede Migration in einer Transaktion angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="66615-202">Notice that each migration is now applied inside a transaction.</span></span>

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

<span data-ttu-id="66615-203">Wie bereits im vorherigen Abschnitt erwähnt, kann diese Verwendung von Transaktionen deaktiviert werden, wenn Transaktionen anders behandelt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="66615-203">As mentioned in the previous section, this use of transactions can be disabled if transactions need to be handled differently.</span></span>

### <a name="see-pending-migrations"></a><span data-ttu-id="66615-204">Ausstehende Migrationen anzeigen</span><span class="sxs-lookup"><span data-stu-id="66615-204">See pending migrations</span></span>

<span data-ttu-id="66615-205">Dieses Feature stammt vom Communitymitglied [@Psypher9](https://github.com/Psypher9).</span><span class="sxs-lookup"><span data-stu-id="66615-205">This feature was contributed from the community by [@Psypher9](https://github.com/Psypher9).</span></span> <span data-ttu-id="66615-206">Danke für diesen Beitrag!</span><span class="sxs-lookup"><span data-stu-id="66615-206">Many thanks for the contribution!</span></span>

<span data-ttu-id="66615-207">Mit dem Befehl `dotnet ef migrations list` wird nun angezeigt, welche Migrationen noch nicht auf die Datenbank angewendet wurden.</span><span class="sxs-lookup"><span data-stu-id="66615-207">The `dotnet ef migrations list` command now shows which migrations have not yet been applied to the database.</span></span> <span data-ttu-id="66615-208">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-208">For example:</span></span>

```console
ajcvickers@avickers420u:~/AllTogetherNow/Daily$ dotnet ef migrations list
Build started...
Build succeeded.
20200910201647_One
20200910201708_Two
20200910202050_Three (Pending)
ajcvickers@avickers420u:~/AllTogetherNow/Daily$
```

<span data-ttu-id="66615-209">Außerdem gibt es jetzt den Befehl `Get-Migration` für die Paket-Manager-Konsole mit derselben Funktionalität.</span><span class="sxs-lookup"><span data-stu-id="66615-209">In addition, there is now a `Get-Migration` command for the Package Manager Console with the same functionality.</span></span>

### <a name="modelbuilder-api-for-value-comparers"></a><span data-ttu-id="66615-210">ModelBuilder-API für Wertevergleiche</span><span class="sxs-lookup"><span data-stu-id="66615-210">ModelBuilder API for value comparers</span></span>

<span data-ttu-id="66615-211">EF Core-Eigenschaften für benutzerdefinierte änderbare Typen [erfordern einen Wertevergleich](xref:core/modeling/value-comparers), damit Eigenschaftsänderungen richtig erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="66615-211">EF Core properties for custom mutable types [require a value comparer](xref:core/modeling/value-comparers) for property changes to be detected correctly.</span></span> <span data-ttu-id="66615-212">Dies kann jetzt als Teil der Konfiguration der Wertkonvertierung für den Typ angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="66615-212">This can now be specified as part of configuring the value conversion for the type.</span></span> <span data-ttu-id="66615-213">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-213">For example:</span></span>

```csharp
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

### <a name="entityentry-trygetvalue-methods"></a><span data-ttu-id="66615-214">EntityEntry-TryGetValue-Methoden</span><span class="sxs-lookup"><span data-stu-id="66615-214">EntityEntry TryGetValue methods</span></span>

<span data-ttu-id="66615-215">Dieses Feature stammt vom Communitymitglied [@m4ss1m0g](https://github.com/m4ss1m0g).</span><span class="sxs-lookup"><span data-stu-id="66615-215">This feature was contributed from the community by [@m4ss1m0g](https://github.com/m4ss1m0g).</span></span> <span data-ttu-id="66615-216">Danke für diesen Beitrag!</span><span class="sxs-lookup"><span data-stu-id="66615-216">Many thanks for the contribution!</span></span>

<span data-ttu-id="66615-217">`EntityEntry.CurrentValues` und `EntityEntry.OriginalValues` wurde eine `TryGetValue`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="66615-217">A `TryGetValue` method has been added to `EntityEntry.CurrentValues` and `EntityEntry.OriginalValues`.</span></span> <span data-ttu-id="66615-218">Dadurch kann der Wert einer Eigenschaft angefordert werden, ohne zuerst zu überprüfen, ob die Eigenschaft im EF-Modell zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="66615-218">This allows the value of a property to be requested without first checking if the property is mapped in the EF model.</span></span> <span data-ttu-id="66615-219">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-219">For example:</span></span>

```csharp
if (entry.CurrentValues.TryGetValue(propertyName, out var value))
{
    Console.WriteLine(value);
}
```

### <a name="default-max-batch-size-for-sql-server"></a><span data-ttu-id="66615-220">Standardwert für maximale Batchgröße bei SQL Server</span><span class="sxs-lookup"><span data-stu-id="66615-220">Default max batch size for SQL Server</span></span>

<span data-ttu-id="66615-221">Ab EF Core 5.0 beträgt der Standardwert für die maximale Batchgröße bei SaveChanges unter SQL Server nun 42.</span><span class="sxs-lookup"><span data-stu-id="66615-221">Starting with EF Core 5.0, the default maximum batch size for SaveChanges on SQL Server is now 42.</span></span> <span data-ttu-id="66615-222">Bekanntlich ist dies auch die Antwort auf die ultimative Frage des Lebens, des Universums und alles Übrigen.</span><span class="sxs-lookup"><span data-stu-id="66615-222">As is well known, this is also the answer to the Ultimate Question of Life, the Universe, and Everything.</span></span> <span data-ttu-id="66615-223">Dies ist jedoch wahrscheinlich ein Zufall, da der Wert durch die [Analyse der Batchverarbeitungsleistung](https://github.com/dotnet/efcore/issues/9270) ermittelt wurde.</span><span class="sxs-lookup"><span data-stu-id="66615-223">However, this is probably a coincidence, since the value was obtained through [analysis of batching performance](https://github.com/dotnet/efcore/issues/9270).</span></span> <span data-ttu-id="66615-224">Wir glauben nicht, dass wir eine Form der ultimativen Frage entdeckt haben, obwohl es einigermaßen plausibel erscheint, dass die Erde erschaffen wurde, um zu verstehen, warum SQL Server so funktioniert, wie es funktioniert.</span><span class="sxs-lookup"><span data-stu-id="66615-224">We do not believe that we have discovered a form of the Ultimate Question, although it does seem somewhat plausible that the Earth was created to understand why SQL Server works the way it does.</span></span>

### <a name="default-environment-to-development"></a><span data-ttu-id="66615-225">Standardumgebung für die Entwicklung</span><span class="sxs-lookup"><span data-stu-id="66615-225">Default environment to Development</span></span>

<span data-ttu-id="66615-226">Die Befehlszeilentools von EF Core konfigurieren nun die `ASPNETCORE_ENVIRONMENT`- _und_ `DOTNET_ENVIRONMENT`-Umgebungsvariablen automatisch als „Entwicklung“.</span><span class="sxs-lookup"><span data-stu-id="66615-226">The EF Core command line tools now automatically configure the `ASPNETCORE_ENVIRONMENT` _and_ `DOTNET_ENVIRONMENT` environment variables to "Development".</span></span> <span data-ttu-id="66615-227">Dadurch wird die Benutzeroberfläche bei der Verwendung des generischen Hosts mit der Benutzeroberfläche für die Entwicklung von ASP.NET Core in Einklang gebracht.</span><span class="sxs-lookup"><span data-stu-id="66615-227">This brings the experience when using the generic host in line with the experience for ASP.NET Core during development.</span></span> <span data-ttu-id="66615-228">Informationen hierzu finden Sie unter [#19903](https://github.com/dotnet/efcore/issues/19903).</span><span class="sxs-lookup"><span data-stu-id="66615-228">See [#19903](https://github.com/dotnet/efcore/issues/19903).</span></span>

### <a name="better-migrations-column-ordering"></a><span data-ttu-id="66615-229">Bessere Anordnung der Spalten für Migrationen</span><span class="sxs-lookup"><span data-stu-id="66615-229">Better migrations column ordering</span></span>

<span data-ttu-id="66615-230">Die Spalten für nicht zugeordnete Basisklassen sind nun nach anderen Spalten für zugeordnete Entitätstypen angeordnet.</span><span class="sxs-lookup"><span data-stu-id="66615-230">The columns for unmapped base classes are now ordered after other columns for mapped entity types.</span></span> <span data-ttu-id="66615-231">Dies betrifft jedoch nur neu erstellte Tabellen.</span><span class="sxs-lookup"><span data-stu-id="66615-231">Note this only impacts newly created tables.</span></span> <span data-ttu-id="66615-232">Die Spaltenreihenfolge bei bereits vorhandenen Tabellen bleibt unverändert.</span><span class="sxs-lookup"><span data-stu-id="66615-232">The column order for existing tables remains unchanged.</span></span> <span data-ttu-id="66615-233">Informationen hierzu finden Sie unter [#11314](https://github.com/dotnet/efcore/issues/11314).</span><span class="sxs-lookup"><span data-stu-id="66615-233">See [#11314](https://github.com/dotnet/efcore/issues/11314).</span></span>

### <a name="query-improvements"></a><span data-ttu-id="66615-234">Verbesserungen bei Abfragen</span><span class="sxs-lookup"><span data-stu-id="66615-234">Query improvements</span></span>

<span data-ttu-id="66615-235">EF Core 5.0 RC1 umfasst einige zusätzliche Verbesserungen in Bezug auf die Abfrageübersetzung:</span><span class="sxs-lookup"><span data-stu-id="66615-235">EF Core 5.0 RC1 contains some additional query translation improvements:</span></span>

* <span data-ttu-id="66615-236">Übersetzung von `is` in Cosmos. Informationen hierzu finden Sie unter [#16391](https://github.com/dotnet/efcore/issues/16391).</span><span class="sxs-lookup"><span data-stu-id="66615-236">Translation of `is` on Cosmos--see [#16391](https://github.com/dotnet/efcore/issues/16391)</span></span>
* <span data-ttu-id="66615-237">Vom Benutzer zugeordnete Funktionen können nun zur Steuerung der NULL-Weitergabe mit Anmerkungen versehen werden. Informationen hierzu finden Sie unter [#19609](https://github.com/dotnet/efcore/issues/19609).</span><span class="sxs-lookup"><span data-stu-id="66615-237">User-mapped functions can now be annotated to control null propagation--see [#19609](https://github.com/dotnet/efcore/issues/19609)</span></span>
* <span data-ttu-id="66615-238">Unterstützung für die Übersetzung von GroupBy mit bedingten Aggregaten. Informationen hierzu finden Sie unter [#11711](https://github.com/dotnet/efcore/issues/11711).</span><span class="sxs-lookup"><span data-stu-id="66615-238">Support for translation of GroupBy with conditional aggregates--see [#11711](https://github.com/dotnet/efcore/issues/11711)</span></span>
* <span data-ttu-id="66615-239">Übersetzung des Distinct-Operators über Group-Element vor dem Aggregieren. Informationen hierzu finden Sie unter [#17376](https://github.com/dotnet/efcore/issues/17376).</span><span class="sxs-lookup"><span data-stu-id="66615-239">Translation of Distinct operator over group element before aggregate--see [#17376](https://github.com/dotnet/efcore/issues/17376)</span></span>

### <a name="model-building-for-fields"></a><span data-ttu-id="66615-240">Modellerstellung für Felder</span><span class="sxs-lookup"><span data-stu-id="66615-240">Model building for fields</span></span>

<span data-ttu-id="66615-241">Und schließlich können mit EF Core RC1 im ModelBuilder nun Lambdamethoden sowohl für Felder als auch für Eigenschaften verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66615-241">Finally for RC1, EF Core now allows use of the lambda methods in the ModelBuilder for fields as well as properties.</span></span> <span data-ttu-id="66615-242">Wenn Sie beispielsweise aus irgendeinem Grund keine Eigenschaften, sondern öffentliche Felder verwenden möchten, können diese Felder nun mithilfe der Lambda-Generatoren zugeordnet werden:</span><span class="sxs-lookup"><span data-stu-id="66615-242">For example, if you are averse to properties for some reason and decide to use public fields, then these fields can now be mapped using the lambda builders:</span></span>

```csharp
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

```csharp
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

<span data-ttu-id="66615-243">Obwohl dies nun möglich ist, wird diese Vorgehensweise nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="66615-243">While this is now possible, we are certainly not recommending that you do this.</span></span> <span data-ttu-id="66615-244">Zudem wird EF Core damit nicht um zusätzliche Feldzuordnungsfunktionen erweitert. Damit können lediglich anstelle von Zeichenfolgenmethoden Lambdamethoden verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66615-244">Also, note that this does not add any additional field mapping capabilities to EF Core, it only allows the lambda methods to be used instead of always requiring the string methods.</span></span> <span data-ttu-id="66615-245">Dies ist nur in Ausnahmefällen nützlich, da Felder selten öffentlich sind.</span><span class="sxs-lookup"><span data-stu-id="66615-245">This is seldom useful since fields are rarely public.</span></span>

## <a name="preview-8"></a><span data-ttu-id="66615-246">Preview 8</span><span class="sxs-lookup"><span data-stu-id="66615-246">Preview 8</span></span>

### <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="66615-247">TPT-Zuordnung (Tabelle pro Typ)</span><span class="sxs-lookup"><span data-stu-id="66615-247">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="66615-248">EF Core ordnet standardmäßig eine Vererbungshierarchie von .NET-Typen einer einzelnen Datenbanktabelle zu.</span><span class="sxs-lookup"><span data-stu-id="66615-248">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="66615-249">Dies wird als TPH-Zuordnung (Tabelle pro Hierarchie) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="66615-249">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="66615-250">EF Core 5.0 ermöglicht außerdem das Zuordnen der einzelnen .NET-Typen in einer Vererbungshierarchie zu einer anderen Datenbanktabelle. Dies wird als TPT-Zuordnung (Tabelle pro Typ) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="66615-250">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="66615-251">Stellen Sie sich beispielsweise das folgende Modell mit einer zugeordneten Hierarchie vor:</span><span class="sxs-lookup"><span data-stu-id="66615-251">For example, consider this model with a mapped hierarchy:</span></span>

```csharp
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

<span data-ttu-id="66615-252">EF Core ordnet dieses standardmäßig einer einzelnen Tabelle zu:</span><span class="sxs-lookup"><span data-stu-id="66615-252">By default, EF Core will map this to a single table:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [Name] nvarchar(max) NULL,
    [EducationLevel] nvarchar(max) NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);
```

<span data-ttu-id="66615-253">Allerdings resultiert die Zuordnung aller Entitätstypen zu anderen Tabellen zu einer Tabelle pro Typ:</span><span class="sxs-lookup"><span data-stu-id="66615-253">However, mapping each entity type to a different table will instead result in one table per type:</span></span>

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
    [EducationLevel] nvarchar(max) NULL,
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

<span data-ttu-id="66615-254">Beachten Sie, dass die Erstellung der oben gezeigten Fremdschlüsseleinschränkungen nach der Verzweigung des Codes für Preview 8 hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="66615-254">Note that creation of the foreign key constraints shown above were added after branching the code for preview 8.</span></span>

<span data-ttu-id="66615-255">Entitätstypen können mithilfe von Zuordnungsattributen verschiedenen Tabellen zugeordnet werden:</span><span class="sxs-lookup"><span data-stu-id="66615-255">Entity types can be mapped to different tables using mapping attributes:</span></span>

```csharp
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
    public string EducationLevel { get; set; }
}

[Table("Dogs")]
public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="66615-256">Alternativ kann die `ModelBuilder`-Konfiguration verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="66615-256">Or using `ModelBuilder` configuration:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

<span data-ttu-id="66615-257">Die Dokumentation finden Sie im [Issue 1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span><span class="sxs-lookup"><span data-stu-id="66615-257">Documentation is tracked by issue [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span></span>

### <a name="migrations-rebuild-sqlite-tables"></a><span data-ttu-id="66615-258">Migrationen: Neuerstellen von SQLite-Tabellen</span><span class="sxs-lookup"><span data-stu-id="66615-258">Migrations: Rebuild SQLite tables</span></span>

<span data-ttu-id="66615-259">Im Vergleich zu anderen Datenbanken ist SQLite in seinen Schemabearbeitungsfunktionen relativ eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="66615-259">Compared to other database, SQLite is relatively limited in its schema manipulation capabilities.</span></span> <span data-ttu-id="66615-260">Wenn Sie beispielsweise eine Spalte aus einer vorhandenen Tabelle löschen möchten, muss die gesamte Tabelle gelöscht und neu erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="66615-260">For example, dropping a column from an existing table requires that the entire table be dropped and re-created.</span></span> <span data-ttu-id="66615-261">EF Core 5.0-Migrationen unterstützen nun die automatische Neuerstellung der Tabelle für Schemaänderungen, die diese erfordern.</span><span class="sxs-lookup"><span data-stu-id="66615-261">EF Core 5.0 Migrations now supports automatic rebuilding the table for schema changes that require it.</span></span>

<span data-ttu-id="66615-262">Angenommen, Sie verfügen über eine `Unicorns`-Tabelle, die für den Entitätstyp `Unicorn` erstellt wurde:</span><span class="sxs-lookup"><span data-stu-id="66615-262">For example, imagine we have a `Unicorns` table created for a `Unicorn` entity type:</span></span>

```csharp
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

<span data-ttu-id="66615-263">Dann erfahren Sie jedoch, dass das Speichern des Alters eines Einhorns sehr unhöflich ist. Daher entfernen Sie diese Eigenschaft, fügen eine neue Migration hinzu und aktualisieren die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="66615-263">We then learn that storing the age of a unicorn is considered very rude, so let's remove that property, add a new migration, and update the database.</span></span> <span data-ttu-id="66615-264">Dieses Update schlägt bei Verwendung von EF Core 3.1 fehl, da die Spalte nicht gelöscht werden kann.</span><span class="sxs-lookup"><span data-stu-id="66615-264">This update will fail when using EF Core 3.1 because the column cannot be dropped.</span></span> <span data-ttu-id="66615-265">In EF Core 5.0 wird die Tabelle durch Migrationen stattdessen neu erstellt:</span><span class="sxs-lookup"><span data-stu-id="66615-265">In EF Core 5.0, Migrations will instead rebuild the table:</span></span>

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

<span data-ttu-id="66615-266">Beachten Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66615-266">Notice that:</span></span>

* <span data-ttu-id="66615-267">Eine temporäre Tabelle wird mit dem gewünschten Schema für die neue Tabelle erstellt.</span><span class="sxs-lookup"><span data-stu-id="66615-267">A temporary table is created with the desired schema for the new table</span></span>
* <span data-ttu-id="66615-268">Daten werden aus der aktuellen Tabelle in die temporäre Tabelle kopiert.</span><span class="sxs-lookup"><span data-stu-id="66615-268">Data is copied from the current table into the temporary table</span></span>
* <span data-ttu-id="66615-269">Die Fremdschlüsselerzwingung wird deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="66615-269">Foreign key enforcement is switched off</span></span>
* <span data-ttu-id="66615-270">Die aktuelle Tabelle wird gelöscht.</span><span class="sxs-lookup"><span data-stu-id="66615-270">The current table is dropped</span></span>
* <span data-ttu-id="66615-271">Die temporäre Tabelle wird umbenannt, sodass sie als neue Tabelle fungiert.</span><span class="sxs-lookup"><span data-stu-id="66615-271">The temporary table is renamed to be the new table</span></span>

<span data-ttu-id="66615-272">Die Dokumentation finden Sie im [Issue 2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span><span class="sxs-lookup"><span data-stu-id="66615-272">Documentation is tracked by issue [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span></span>

### <a name="table-valued-functions"></a><span data-ttu-id="66615-273">Tabellenwertfunktionen</span><span class="sxs-lookup"><span data-stu-id="66615-273">Table-valued functions</span></span>

<span data-ttu-id="66615-274">Dieses Feature stammt vom Communitymitglied [@pmiddleton](https://github.com/pmiddleton).</span><span class="sxs-lookup"><span data-stu-id="66615-274">This feature was contributed from the community by [@pmiddleton](https://github.com/pmiddleton).</span></span> <span data-ttu-id="66615-275">Danke für diesen Beitrag!</span><span class="sxs-lookup"><span data-stu-id="66615-275">Many thanks for the contribution!</span></span>

<span data-ttu-id="66615-276">EF Core 5.0 umfasst erstklassige Unterstützung für die Zuordnung von .NET-Methoden zu Tabellenwertfunktionen.</span><span class="sxs-lookup"><span data-stu-id="66615-276">EF Core 5.0 includes first-class support for mapping .NET methods to table-valued functions (TVFs).</span></span> <span data-ttu-id="66615-277">Diese Funktionen können dann in LINQ-Abfragen verwendet werden, wobei eine zusätzliche Komposition der Ergebnisse der Funktion ebenfalls in SQL übersetzt wird.</span><span class="sxs-lookup"><span data-stu-id="66615-277">These functions can then be used in LINQ queries where additional composition on the results of the function will also be translated to SQL.</span></span>

<span data-ttu-id="66615-278">Sehen Sie sich das folgende Beispiel an, bei dem die Tabellenwertfunktionen in einer SQL Server-Datenbank definiert wurden:</span><span class="sxs-lookup"><span data-stu-id="66615-278">For example, consider this TVF defined in a SQL Server database:</span></span>

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

<span data-ttu-id="66615-279">Das EF Core-Modell erfordert zwei Entitätstypen, um diese Tabellenwertfunktionen zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="66615-279">The EF Core model requires two entity types to use this TVF:</span></span>

* <span data-ttu-id="66615-280">Ein `Employee`-Typ, der auf normale Weise der Tabelle „Employees“ zugeordnet wird</span><span class="sxs-lookup"><span data-stu-id="66615-280">An `Employee` type that maps to the Employees table in the normal way</span></span>
* <span data-ttu-id="66615-281">Ein `Report`-Typ, der dem von der Tabellenwertfunktion zurückgegebenen Format entspricht</span><span class="sxs-lookup"><span data-stu-id="66615-281">A `Report` type that matches the shape returned by the TVF</span></span>

```csharp
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }

    public int? ManagerId { get; set; }
    public virtual Employee Manager { get; set; }
}
```

```csharp
public class Report
{
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }
}
```

<span data-ttu-id="66615-282">Diese Typen müssen im EF Core-Modell enthalten sein:</span><span class="sxs-lookup"><span data-stu-id="66615-282">These types must be included in the EF Core model:</span></span>

```csharp
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

<span data-ttu-id="66615-283">Beachten Sie, dass `Report` über keinen Primärschlüssel verfügt und daher entsprechend konfiguriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="66615-283">Notice that `Report` has no primary key and so must be configured as such.</span></span>

<span data-ttu-id="66615-284">Schließlich muss eine .NET-Methode der Tabellenwertfunktion in der Datenbank zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="66615-284">Finally, a .NET method must be mapped to the TVF in the database.</span></span> <span data-ttu-id="66615-285">Diese Methode kann mithilfe der neuen `FromExpression`-Methode in DbContext definiert werden:</span><span class="sxs-lookup"><span data-stu-id="66615-285">This method can be defined on the DbContext using the new `FromExpression` method:</span></span>

```csharp
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

<span data-ttu-id="66615-286">Diese Methode verwendet einen Parameter und Rückgabetyp, die mit der oben definierten Tabellenwertfunktion übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="66615-286">This method uses a parameter and return type that match the TVF defined above.</span></span> <span data-ttu-id="66615-287">Die Methode wird dann zum EF Core-Modell in OnModelCreating hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="66615-287">The method is then added to the EF Core model in OnModelCreating:</span></span>

```csharp
modelBuilder.HasDbFunction(() => GetReports(default));
```

<span data-ttu-id="66615-288">(Die Verwendung eines Lambdaausdrucks ist hier eine einfache Methode, um `MethodInfo` an EF Core zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="66615-288">(Using a lambda here is an easy way to pass the `MethodInfo` to EF Core.</span></span> <span data-ttu-id="66615-289">Die an die Methode übergebenen Argumente werden ignoriert.)</span><span class="sxs-lookup"><span data-stu-id="66615-289">The arguments passed to the method are ignored.)</span></span>

<span data-ttu-id="66615-290">Sie können nun Abfragen schreiben, die `GetReports` aufrufen und die Ergebnisse überschreiben.</span><span class="sxs-lookup"><span data-stu-id="66615-290">We can now write queries that call `GetReports` and compose over the results.</span></span> <span data-ttu-id="66615-291">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-291">For example:</span></span>

```csharp
from e in context.Employees
from rc in context.GetReports(e.Id)
where rc.IsDeveloper == true
select new
{
  ManagerName = e.Name,
  EmployeeName = rc.Name,
})
```

<span data-ttu-id="66615-292">In SQL Server funktioniert dies wie folgt:</span><span class="sxs-lookup"><span data-stu-id="66615-292">On SQL Server, this translates to:</span></span>

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

<span data-ttu-id="66615-293">Beachten Sie, dass der in der `Employees`-Tabelle verankerte SQL-Code `GetReports` aufruft und dann eine zusätzliche WHERE-Klausel für die Ergebnisse der Funktion hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="66615-293">Notice that the SQL is rooted in the `Employees` table, calls `GetReports`, and then adds an additional WHERE clause on the results of the function.</span></span>

### <a name="flexible-queryupdate-mapping"></a><span data-ttu-id="66615-294">Flexible Zuordnung von Abfragen und Updates</span><span class="sxs-lookup"><span data-stu-id="66615-294">Flexible query/update mapping</span></span>

<span data-ttu-id="66615-295">EF Core 5.0 ermöglicht das Zuordnen desselben Entitätstyps zu verschiedenen Datenbankobjekten.</span><span class="sxs-lookup"><span data-stu-id="66615-295">EF Core 5.0 allows mapping the same entity type to different database objects.</span></span> <span data-ttu-id="66615-296">Bei diesen Objekten kann es sich um Tabellen, Ansichten oder Funktionen handeln.</span><span class="sxs-lookup"><span data-stu-id="66615-296">These objects may be tables, views, or functions.</span></span>

<span data-ttu-id="66615-297">Ein Entitätstyp kann beispielsweise sowohl einer Datenbankansicht als auch einer Datenbanktabelle zugeordnet werden:</span><span class="sxs-lookup"><span data-stu-id="66615-297">For example, an entity type can be mapped to both a database view and a database table:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

<span data-ttu-id="66615-298">EF Core führt Abfragen dann standardmäßig über die Ansicht aus und sendet Updates an die Tabelle.</span><span class="sxs-lookup"><span data-stu-id="66615-298">By default, EF Core will then query from the view and send updates to the table.</span></span> <span data-ttu-id="66615-299">Angenommen, der folgende Code wird ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="66615-299">For example, executing the following code:</span></span>

```csharp
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

<span data-ttu-id="66615-300">Dies resultiert in einer Abfrage der Ansicht und einem anschließenden Update der Tabelle:</span><span class="sxs-lookup"><span data-stu-id="66615-300">Results in a query against the view, and then an update to the table:</span></span>

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a><span data-ttu-id="66615-301">Kontextbezogene Konfiguration von geteilten Abfragen</span><span class="sxs-lookup"><span data-stu-id="66615-301">Context-wide split-query configuration</span></span>

<span data-ttu-id="66615-302">Geteilte Abfragen (siehe unten) können nun als Standardabfragen konfiguriert werden, die von DbContext ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="66615-302">Split queries (see below) can now be configured as the default for any query executed by the DbContext.</span></span> <span data-ttu-id="66615-303">Diese Konfiguration ist nur für relationale Anbieter verfügbar und muss daher im Rahmen der `UseProvider`-Konfiguration festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="66615-303">This configuration is only available for relational providers, and so must be specified as part of the `UseProvider` configuration.</span></span> <span data-ttu-id="66615-304">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-304">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

<span data-ttu-id="66615-305">Die Dokumentation finden Sie im [Issue 2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="66615-305">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="physicaladdress-mapping"></a><span data-ttu-id="66615-306">Zuordnung von PhysicalAddress</span><span class="sxs-lookup"><span data-stu-id="66615-306">PhysicalAddress mapping</span></span>

<span data-ttu-id="66615-307">Dieses Feature stammt vom Communitymitglied [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span><span class="sxs-lookup"><span data-stu-id="66615-307">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="66615-308">Danke für diesen Beitrag!</span><span class="sxs-lookup"><span data-stu-id="66615-308">Many thanks for the contribution!</span></span>

<span data-ttu-id="66615-309">Die neue .NET Standard-Klasse [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) wird jetzt automatisch einer Zeichenfolgenspalte für Datenbanken zugeordnet, die noch nicht nativ unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="66615-309">The standard .NET [PhysicalAddress class](/dotnet/api/system.net.networkinformation.physicaladdress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="66615-310">Weitere Informationen finden Sie unten in den Beispielen zu `IPAddress`.</span><span class="sxs-lookup"><span data-stu-id="66615-310">For more information, see the examples for `IPAddress` below.</span></span>

## <a name="preview-7"></a><span data-ttu-id="66615-311">Preview 7</span><span class="sxs-lookup"><span data-stu-id="66615-311">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="66615-312">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="66615-312">DbContextFactory</span></span>

<span data-ttu-id="66615-313">EF Core 5.0 führt `AddDbContextFactory` und `AddPooledDbContextFactory` ein, um eine Zuordnungsinstanz zum Erstellen von DbContext-Instanzen im Abhängigkeitsinjektionscontainer der Anwendung zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="66615-313">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="66615-314">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-314">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="66615-315">Anwendungsdienste wie ASP.NET Core-Controller können dann von `IDbContextFactory<TContext>` im Dienstkonstruktor abhängen.</span><span class="sxs-lookup"><span data-stu-id="66615-315">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="66615-316">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-316">For example:</span></span>

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

<span data-ttu-id="66615-317">DbContext-Instanzen können dann nach Bedarf erstellt und verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66615-317">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="66615-318">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-318">For example:</span></span>

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

<span data-ttu-id="66615-319">Beachten Sie, dass die DbContext-Instanzen, die auf diese Weise erstellt werden, _nicht_ vom Dienstanbieter der Anwendung verwaltet werden, weshalb diese von der Anwendung gelöscht werden müssen.</span><span class="sxs-lookup"><span data-stu-id="66615-319">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="66615-320">Diese Entkopplung ist für Blazor-Anwendungen sehr nützlich, bei denen die Verwendung von `IDbContextFactory` empfohlen wird. Sie kann sich aber auch in anderen Szenarios als nützlich erweisen.</span><span class="sxs-lookup"><span data-stu-id="66615-320">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="66615-321">DbContext-Instanzen können durch Aufrufe von `AddPooledDbContextFactory` gepoolt werden.</span><span class="sxs-lookup"><span data-stu-id="66615-321">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="66615-322">Dieses Pooling funktioniert auf dieselbe Weise wie `AddDbContextPool` und weist dieselben Einschränkungen auf.</span><span class="sxs-lookup"><span data-stu-id="66615-322">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="66615-323">Die Dokumentation finden Sie im [Issue 2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span><span class="sxs-lookup"><span data-stu-id="66615-323">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="66615-324">Zurücksetzen des DbContext-Zustands</span><span class="sxs-lookup"><span data-stu-id="66615-324">Reset DbContext state</span></span>

<span data-ttu-id="66615-325">EF Core 5.0 führt `ChangeTracker.Clear()` ein, wodurch die DbContext-Instanzen aller überwachten Entitäten gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="66615-325">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="66615-326">Dies sollte normalerweise nicht erforderlich sein, wenn die bewährte Methode zum Erstellen einer neuen kurzlebigen Kontextinstanz für jede Arbeitseinheit angewandt wird.</span><span class="sxs-lookup"><span data-stu-id="66615-326">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="66615-327">Allerdings ist die Verwendung der neuen `Clear()`-Methode leistungsfähiger und robuster als das Trennen aller Entitäten in Massen, wenn der Zustand einer DbContext-Instanz nicht zurückgesetzt werden muss.</span><span class="sxs-lookup"><span data-stu-id="66615-327">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>

<span data-ttu-id="66615-328">Die Dokumentation finden Sie im [Issue 2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span><span class="sxs-lookup"><span data-stu-id="66615-328">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="66615-329">Neues Muster für vom Speicher generierte Standardwerte</span><span class="sxs-lookup"><span data-stu-id="66615-329">New pattern for store-generated defaults</span></span>

<span data-ttu-id="66615-330">EF Core lässt das Festlegen eines expliziten Werts für eine Spalte zu, die auch eine Standardwerteinschränkung aufweisen kann.</span><span class="sxs-lookup"><span data-stu-id="66615-330">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="66615-331">EF Core verwendet hierfür den CLR-Standardwert für den Eigenschaftentyp als Schutzmechanismus. Wenn es sich bei dem Wert nicht um den CLR-Standardwert handelt, wird dieser eingefügt, andernfalls wird der Standardwert der Datenbank verwendet.</span><span class="sxs-lookup"><span data-stu-id="66615-331">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="66615-332">Dies führt zu Problemen bei Typen, bei denen der CLR-Standardwert kein guter Schutzmechanismus ist. Insbesondere `bool`-Eigenschaften sind dafür bekannt.</span><span class="sxs-lookup"><span data-stu-id="66615-332">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="66615-333">EF Core 5.0 lässt nun zu, dass das Unterstützungsfeld in solchen Fällen NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="66615-333">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="66615-334">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-334">For example:</span></span>

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

<span data-ttu-id="66615-335">Beachten Sie, dass das Unterstützungsfeld NULL-Werte zulässt, die öffentlich zur Verfügung gestellte Eigenschaft jedoch nicht.</span><span class="sxs-lookup"><span data-stu-id="66615-335">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="66615-336">Daher kann der Sentinelwert `null` sein, ohne dass sich dies auf die öffentliche Oberfläche des Entitätstyps auswirkt.</span><span class="sxs-lookup"><span data-stu-id="66615-336">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="66615-337">Wenn `IsValid` in diesem Fall nie festgelegt wird, wird der Standardwert der Datenbank verwendet, da das Unterstützungsfeld weiterhin NULL ist.</span><span class="sxs-lookup"><span data-stu-id="66615-337">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="66615-338">Wenn weder `true` noch `false` festgelegt werden, wird dieser Wert explizit in der Datenbank gespeichert.</span><span class="sxs-lookup"><span data-stu-id="66615-338">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="66615-339">Die Dokumentation finden Sie im [Issue 2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span><span class="sxs-lookup"><span data-stu-id="66615-339">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="66615-340">Cosmos-Partitionsschlüssel</span><span class="sxs-lookup"><span data-stu-id="66615-340">Cosmos partition keys</span></span>

<span data-ttu-id="66615-341">EF Core lässt das Einschließen des Cosmos-Partitionsschlüssels im EF-Modell zu.</span><span class="sxs-lookup"><span data-stu-id="66615-341">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="66615-342">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-342">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="66615-343">Ab Preview 7 ist der Partitionsschlüssel im primären Schlüssel des Entitätstyps enthalten und wird zur Verbesserung der Leistung einiger Abfragen verwendet.</span><span class="sxs-lookup"><span data-stu-id="66615-343">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="66615-344">Die Dokumentation finden Sie im [Issue 2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="66615-344">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="66615-345">Cosmos-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="66615-345">Cosmos configuration</span></span>

<span data-ttu-id="66615-346">EF Core 5.0 verbessert die Konfiguration von Cosmos und Cosmos-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="66615-346">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="66615-347">Zuvor erforderte EF Core, dass der Endpunkt und der Schlüssel explizit festgelegt werden, wenn eine Verbindung mit einer Cosmos-Datenbank hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="66615-347">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="66615-348">EF Core 5.0 erlaubt stattdessen die Verwendung einer Verbindungszeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="66615-348">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="66615-349">Darüber hinaus ermöglicht EF Core 5.0 das explizite Festlegen der WebProxy-Instanz.</span><span class="sxs-lookup"><span data-stu-id="66615-349">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="66615-350">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-350">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="66615-351">Viele andere Timeoutwerte, Grenzwerte und mehr können nun ebenfalls konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="66615-351">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="66615-352">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-352">For example:</span></span>

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

<span data-ttu-id="66615-353">Schließlich ist `ConnectionMode.Gateway` nun der Standardverbindungsmodus, der im Allgemeinen kompatibler ist.</span><span class="sxs-lookup"><span data-stu-id="66615-353">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="66615-354">Die Dokumentation finden Sie im [Issue 2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="66615-354">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="66615-355">Scaffold-DbContext nutzt nun die Singularform</span><span class="sxs-lookup"><span data-stu-id="66615-355">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="66615-356">Beim Gerüstbau einer DbContext-Instanz anhand einer vorhandenen Datenbank hat EF Core zuvor Entitätstypnamen erstellt, die mit den Tabellennamen in der Datenbank übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="66615-356">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="66615-357">Beispielsweise führten die Tabellen `People` und `Addresses` zu Entitätstypen mit den Namen `People` und `Addresses`.</span><span class="sxs-lookup"><span data-stu-id="66615-357">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="66615-358">In früheren Releases konnte dieses Verhalten über die Registrierung eines Pluralisierungsdiensts konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="66615-358">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="66615-359">In EF Core 5.0 wird nun das Paket [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) als Standardpluralisierungsdienst verwendet.</span><span class="sxs-lookup"><span data-stu-id="66615-359">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="66615-360">Das bedeutet, die Tabellen `People` und `Addresses` werden nun so rekonstruiert, dass Entitätstypen mit den Namen `Person` und `Address` erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="66615-360">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="66615-361">Sicherungspunkte</span><span class="sxs-lookup"><span data-stu-id="66615-361">Savepoints</span></span>

<span data-ttu-id="66615-362">EF Core unterstützt nun [Sicherungspunkte](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks), um mehr Kontrolle über Transaktionen zu bieten, die mehrere Vorgänge ausführen.</span><span class="sxs-lookup"><span data-stu-id="66615-362">EF Core now supports [savepoints](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="66615-363">Sicherungspunkte können manuell erstellt, veröffentlicht und zurückgesetzt werden.</span><span class="sxs-lookup"><span data-stu-id="66615-363">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="66615-364">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-364">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

<span data-ttu-id="66615-365">Darüber hinaus führt EF Core nun ein Rollback auf den letzten Sicherungspunkt aus, wenn die Ausführung von `SaveChanges` fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="66615-365">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="66615-366">Dies ermöglicht das erneute Ausführen von SaveChanges, ohne dass die gesamte Transaktion wiederholt werden muss.</span><span class="sxs-lookup"><span data-stu-id="66615-366">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="66615-367">Die Dokumentation finden Sie im [Issue 2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span><span class="sxs-lookup"><span data-stu-id="66615-367">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="66615-368">Vorschau 6</span><span class="sxs-lookup"><span data-stu-id="66615-368">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="66615-369">Aufteilen von Abfragen für ähnliche Sammlungen</span><span class="sxs-lookup"><span data-stu-id="66615-369">Split queries for related collections</span></span>

<span data-ttu-id="66615-370">Ab EF Core 3.0 generiert EF Core für jede LINQ-Abfrage eine einzelne SQL-Abfrage.</span><span class="sxs-lookup"><span data-stu-id="66615-370">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="66615-371">Dadurch wird sichergestellt, dass die zurückgegebenen Daten im Rahmen der Einschränkungen des verwendeten Transaktionsmodus konsistent sind.</span><span class="sxs-lookup"><span data-stu-id="66615-371">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="66615-372">Wenn die Abfrage `Include` oder eine Projektion verwendet, um mehrere ähnliche Sammlungen zurückzugeben, kann die Ausführung jedoch sehr lange dauern.</span><span class="sxs-lookup"><span data-stu-id="66615-372">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="66615-373">In EF Core 5.0 ist es jetzt möglich, eine einzelne LINQ-Abfrage und die zugehörigen Sammlungen auf mehrere SQL-Abfragen aufzuteilen.</span><span class="sxs-lookup"><span data-stu-id="66615-373">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="66615-374">Die Leistung kann dadurch zwar erheblich verbessert werden, doch bei den zurückgegebenen Ergebnissen kann es zu Inkonsistenzen kommen, wenn die Daten sich zwischen den beiden Abfragen ändern.</span><span class="sxs-lookup"><span data-stu-id="66615-374">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="66615-375">Serialisierbare Transaktionen oder Momentaufnahmentransaktionen können verwendet werden, um das zu verhindern und Konsistenz bei aufgeteilten Abfragen zu erzielen. Dadurch kann jedoch die Leistung beeinträchtigt werden, und das Verhalten kann sich unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="66615-375">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="66615-376">Aufteilen von Abfragen mit Include</span><span class="sxs-lookup"><span data-stu-id="66615-376">Split queries with Include</span></span>

<span data-ttu-id="66615-377">Gehen Sie von einer Abfrage aus, die mithilfe von `Include` zwei Ebenen ähnlicher Sammlungen einschließt:</span><span class="sxs-lookup"><span data-stu-id="66615-377">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```csharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="66615-378">Standardmäßig generiert EF Core den folgenden SQL-Wert, wenn der SQLite-Anbieter verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="66615-378">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

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

<span data-ttu-id="66615-379">Die neue `AsSplitQuery`-API kann verwendet werden, um dieses Verhalten zu ändern.</span><span class="sxs-lookup"><span data-stu-id="66615-379">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="66615-380">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-380">For example:</span></span>

```csharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="66615-381">AsSplitQuery ist für alle relationalen Datenbankanbieter verfügbar und kann genau wie AsNoTracking an beliebiger Stelle in der Abfrage verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66615-381">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="66615-382">In EF Core werden nun die folgenden drei SQL-Abfragen generiert:</span><span class="sxs-lookup"><span data-stu-id="66615-382">EF Core will now generate the following three SQL queries:</span></span>

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

<span data-ttu-id="66615-383">Alle Vorgänge für den Abfragestamm werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="66615-383">All operations on the query root are supported.</span></span> <span data-ttu-id="66615-384">Das schließt OrderBy-, Skip-, Take-, Join-, FirstOrDefault-Vorgänge und ähnliche Vorgänge ein, die ein einzelnes Ergebnis auswählen.</span><span class="sxs-lookup"><span data-stu-id="66615-384">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="66615-385">Beachten Sie, dass nach OrderBy, Skip und Take gefilterte Include-Abfragen in Preview 6 nicht unterstützt werden, aber in den täglichen Builds und zukünftig in Preview 7 enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="66615-385">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="66615-386">Aufteilen von Abfragen mit Sammlungsprojektionen</span><span class="sxs-lookup"><span data-stu-id="66615-386">Split queries with collection projections</span></span>

<span data-ttu-id="66615-387">`AsSplitQuery` kann auch verwendet werden, wenn Sammlungen in Projektionen geladen werden.</span><span class="sxs-lookup"><span data-stu-id="66615-387">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="66615-388">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-388">For example:</span></span>

```csharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="66615-389">Diese LINQ-Abfrage generiert die folgenden zwei SQL-Abfragen, wenn der SQLite-Anbieter verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="66615-389">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="66615-390">Beachten Sie, dass nur die Materialisierung der Sammlung unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="66615-390">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="66615-391">Jede Komposition nach `e.Albums` im obigen Fall führt nicht zu einer aufgeteilten Abfrage.</span><span class="sxs-lookup"><span data-stu-id="66615-391">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="66615-392">Die Verbesserungen in diesem Bereich werden in [Issue 21234](https://github.com/dotnet/efcore/issues/21234) nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="66615-392">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="66615-393">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="66615-393">IndexAttribute</span></span>

<span data-ttu-id="66615-394">Die neue IndexAttribute-Klasse kann für einen Entitätstyp verwendet werden, um einen Index für eine einzelne Spalte anzugeben.</span><span class="sxs-lookup"><span data-stu-id="66615-394">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="66615-395">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-395">For example:</span></span>

```csharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="66615-396">Bei SQL Server generieren Migrationen dann folgenden SQL-Code:</span><span class="sxs-lookup"><span data-stu-id="66615-396">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="66615-397">IndexAttribute kann auch verwendet werden, um einen Index anzugeben, der mehrere Spalten umfasst.</span><span class="sxs-lookup"><span data-stu-id="66615-397">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="66615-398">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-398">For example:</span></span>

```csharp
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

<span data-ttu-id="66615-399">Für SQL Server ergibt sich Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66615-399">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="66615-400">Die Dokumentation finden Sie im [Issue 2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="66615-400">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="66615-401">Verbesserte Ausnahmen bei der Abfrageübersetzung</span><span class="sxs-lookup"><span data-stu-id="66615-401">Improved query translation exceptions</span></span>

<span data-ttu-id="66615-402">Wir arbeiten weiter an der Verbesserung der Ausnahmemeldungen, die generiert werden, wenn bei der Abfrageübersetzung ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="66615-402">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="66615-403">Diese Abfrage verwendet z. B. die nicht zugeordnete Eigenschaft `IsSigned`:</span><span class="sxs-lookup"><span data-stu-id="66615-403">For example, this query uses the unmapped property `IsSigned`:</span></span>

```csharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="66615-404">EF Core löst die folgende Ausnahme aus, die angibt, dass bei der Übersetzung ein Fehler aufgetreten ist, da `IsSigned` nicht zugeordnet ist:</span><span class="sxs-lookup"><span data-stu-id="66615-404">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

```exception
Unhandled exception. System.InvalidOperationException: The LINQ expression 'DbSet<Artist>()
   .Where(a => a.IsSigned)' could not be translated. Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed. Possibly the specified member is not mapped. Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync(). See <https://go.microsoft.com/fwlink/?linkid=2101038> for more information.
```

<span data-ttu-id="66615-405">Zudem werden jetzt bessere Ausnahmemeldungen generiert, wenn Sie versuchen, Zeichenfolgenvergleiche mit kulturabhängiger Semantik zu übersetzen.</span><span class="sxs-lookup"><span data-stu-id="66615-405">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="66615-406">Diese Abfrage versucht beispielsweise, `StringComparison.CurrentCulture` zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="66615-406">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```csharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="66615-407">In EF Core wird nun die folgende Ausnahme ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="66615-407">EF Core will now throw the following exception:</span></span>

```exception
Unhandled exception. System.InvalidOperationException: The LINQ expression 'DbSet<Artist>()
     .Where(a => a.Name.Equals(
         value: "The Unicorns",
         comparisonType: CurrentCulture))' could not be translated. Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported. See <https://go.microsoft.com/fwlink/?linkid=2129535> for more information. Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync(). See <https://go.microsoft.com/fwlink/?linkid=2101038> for more information.
```

### <a name="specify-transaction-id"></a><span data-ttu-id="66615-408">Angeben der Transaktions-ID</span><span class="sxs-lookup"><span data-stu-id="66615-408">Specify transaction ID</span></span>

<span data-ttu-id="66615-409">Dieses Feature stammt vom Communitymitglied [@Marusyk](https://github.com/Marusyk).</span><span class="sxs-lookup"><span data-stu-id="66615-409">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="66615-410">Danke für diesen Beitrag!</span><span class="sxs-lookup"><span data-stu-id="66615-410">Many thanks for the contribution!</span></span>

<span data-ttu-id="66615-411">EF Core macht eine Transaktions-ID für die Korrelation von aufrufübergreifenden Transaktionen verfügbar.</span><span class="sxs-lookup"><span data-stu-id="66615-411">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="66615-412">Diese ID wird in der Regel durch EF Core festgelegt, wenn eine Transaktion gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="66615-412">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="66615-413">Wenn die Anwendung stattdessen die Transaktion startet, ermöglicht dieses Feature der Anwendung, die Transaktions-ID explizit festzulegen, damit sie überall ordnungsgemäß korreliert, wo sie verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="66615-413">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="66615-414">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-414">For example:</span></span>

```csharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="66615-415">IPAddress-Zuordnung</span><span class="sxs-lookup"><span data-stu-id="66615-415">IPAddress mapping</span></span>

<span data-ttu-id="66615-416">Dieses Feature stammt vom Communitymitglied [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span><span class="sxs-lookup"><span data-stu-id="66615-416">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="66615-417">Danke für diesen Beitrag!</span><span class="sxs-lookup"><span data-stu-id="66615-417">Many thanks for the contribution!</span></span>

<span data-ttu-id="66615-418">Die neue .NET Standard-Klasse [IPAddress](/dotnet/api/system.net.ipaddress) wird jetzt automatisch einer Zeichenfolgenspalte für Datenbanken zugeordnet, die noch nicht nativ unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="66615-418">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="66615-419">Sehen Sie sich beispielsweise die Zuordnung dieses Entitätstyps an:</span><span class="sxs-lookup"><span data-stu-id="66615-419">For example, consider mapping this entity type:</span></span>

```csharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="66615-420">In SQL Server führt dies zu Migrationen, die die folgende Tabelle erstellen:</span><span class="sxs-lookup"><span data-stu-id="66615-420">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

<span data-ttu-id="66615-421">Entitäten können dann wie gewohnt hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="66615-421">Entities can then be added in the normal way:</span></span>

```csharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

<span data-ttu-id="66615-422">Die resultierende SQL-Abfrage fügt die normalisierte IPv4- oder IPv6-Adresse ein:</span><span class="sxs-lookup"><span data-stu-id="66615-422">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="66615-423">Ausschließen von OnConfiguring beim Gerüstbau</span><span class="sxs-lookup"><span data-stu-id="66615-423">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="66615-424">Wenn das Gerüst für DbContext aus einer vorhandenen Datenbank erstellt wird, erstellt EF Core standardmäßig eine OnConfiguring-Überladung mit einer Verbindungszeichenfolge, damit der Kontext sofort einsatzbereit ist.</span><span class="sxs-lookup"><span data-stu-id="66615-424">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="66615-425">Das ist jedoch nicht nützlich, wenn Sie bereits eine partielle Klasse mit OnConfiguring besitzen oder den Kontext auf andere Weise konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="66615-425">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="66615-426">Die Gerüstbaubefehle können jetzt so geändert werden, dass OnConfiguring nicht generiert wird, um das Problem zu beheben.</span><span class="sxs-lookup"><span data-stu-id="66615-426">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="66615-427">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-427">For example:</span></span>

```console
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="66615-428">In der Paket-Manager-Konsole:</span><span class="sxs-lookup"><span data-stu-id="66615-428">Or in the Package Manager Console:</span></span>

```console
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

<span data-ttu-id="66615-429">Die Verwendung einer [benannten Verbindungszeichenfolge und eines sicheren Speichers wie Benutzergeheimnisse](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets) wird empfohlen.</span><span class="sxs-lookup"><span data-stu-id="66615-429">Note that we recommend using [a named connection string and secure storage like User Secrets](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="66615-430">Übersetzungen für FirstOrDefault in Zeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="66615-430">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="66615-431">Dieses Feature stammt vom Communitymitglied [@dvoreckyaa](https://github.com/dvoreckyaa).</span><span class="sxs-lookup"><span data-stu-id="66615-431">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="66615-432">Danke für diesen Beitrag!</span><span class="sxs-lookup"><span data-stu-id="66615-432">Many thanks for the contribution!</span></span>

<span data-ttu-id="66615-433">FirstOrDefault und ähnliche Operatoren für Verbindungszeichenfolgen werden jetzt übersetzt.</span><span class="sxs-lookup"><span data-stu-id="66615-433">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="66615-434">Nehmen Sie diese LINQ-Abfrage als Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-434">For example, this LINQ query:</span></span>

```csharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="66615-435">Sie wird jetzt in folgenden SQL-Code übersetzt, wenn Sie SQL Server verwenden:</span><span class="sxs-lookup"><span data-stu-id="66615-435">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="66615-436">Vereinfachen von CASE-Blöcken</span><span class="sxs-lookup"><span data-stu-id="66615-436">Simplify case blocks</span></span>

<span data-ttu-id="66615-437">EF Core generiert nun bessere Abfragen, wenn CASE-Blöcke verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66615-437">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="66615-438">Nehmen Sie diese LINQ-Abfrage als Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-438">For example, this LINQ query:</span></span>

```csharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="66615-439">In SQL Server wurde sie zuvor in Folgendes übersetzt:</span><span class="sxs-lookup"><span data-stu-id="66615-439">Was on SQL Server formally translated to:</span></span>

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

<span data-ttu-id="66615-440">Jetzt wird sie folgendermaßen übersetzt:</span><span class="sxs-lookup"><span data-stu-id="66615-440">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a><span data-ttu-id="66615-441">Preview 5</span><span class="sxs-lookup"><span data-stu-id="66615-441">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="66615-442">Datenbanksortierungen</span><span class="sxs-lookup"><span data-stu-id="66615-442">Database collations</span></span>

<span data-ttu-id="66615-443">Die Standardsortierung einer Datenbank kann nun im EF-Modell angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="66615-443">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="66615-444">Diese durchläuft generierte Migrationen, um die Sortierung beim Erstellen der Datenbank festzulegen.</span><span class="sxs-lookup"><span data-stu-id="66615-444">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="66615-445">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-445">For example:</span></span>

```csharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="66615-446">Die Migrationen generieren dann Folgendes, um die Datenbank in SQL Server zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="66615-446">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="66615-447">Die Sortierung für spezifische Datenbankspalten kann ebenfalls angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="66615-447">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="66615-448">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-448">For example:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="66615-449">Für diejenigen, die keine Migrationen verwenden, wird für Sortierungen nun ein Reverse Engineering von der Datenbank aus durchgeführt, wenn der Gerüstbau für eine DbContext-Instanz durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="66615-449">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="66615-450">Schließlich lässt `EF.Functions.Collate()` Ad-hoc-Abfragen mit verschiedenen Sortierungen zu.</span><span class="sxs-lookup"><span data-stu-id="66615-450">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="66615-451">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-451">For example:</span></span>

```csharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="66615-452">Dadurch wird die folgende Abfrage für SQL Server generiert:</span><span class="sxs-lookup"><span data-stu-id="66615-452">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="66615-453">Beachten Sie, dass Ad-Hoc-Sortierungen mit Bedacht verwendet werden sollten, da sie sich negativ auf die Datenbankleistung auswirken können.</span><span class="sxs-lookup"><span data-stu-id="66615-453">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="66615-454">Die Dokumentation finden Sie im [Issue 2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span><span class="sxs-lookup"><span data-stu-id="66615-454">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="66615-455">Übergeben von Argumenten in IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="66615-455">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="66615-456">Argumente können nun über die Befehlszeile in die `CreateDbContext`-Methode von [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1) übertragen werden.</span><span class="sxs-lookup"><span data-stu-id="66615-456">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1).</span></span> <span data-ttu-id="66615-457">Sie können beispielsweise angeben, dass es sich um einen Entwicklungsbuild handelt, indem Sie in der Befehlszeile ein benutzerdefiniertes Argument (z. B. `dev`) übergeben:</span><span class="sxs-lookup"><span data-stu-id="66615-457">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```console
dotnet ef migrations add two --verbose --dev
```

<span data-ttu-id="66615-458">Dieses Argument wird dann an die Factory weitergeleitet, wo es zum Steuern der Erstellung und Initialisierung des Kontexts verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="66615-458">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="66615-459">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-459">For example:</span></span>

```csharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="66615-460">Die Dokumentation finden Sie im [Issue 2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span><span class="sxs-lookup"><span data-stu-id="66615-460">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="66615-461">Abfragen ohne Nachverfolgung mit Identitätsauflösung</span><span class="sxs-lookup"><span data-stu-id="66615-461">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="66615-462">Abfragen ohne Nachverfolgung können nun zum Durchführen von Identitätsauflösungen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="66615-462">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="66615-463">Die folgende Abfrage erstellt beispielsweise eine neue Blog-Instanz für alle Post-Entitäten, selbst wenn alle Blog-Instanzen über denselben Primärschlüssel verfügen.</span><span class="sxs-lookup"><span data-stu-id="66615-463">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span>

```csharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="66615-464">Diese Abfrage kann angepasst werden, um sicherzustellen, dass nur eine einzige Blog-Instanz erstellt wird. Dies ist jedoch in der Regel ein wenig langsamer und nutzt mehr Arbeitsspeicher:</span><span class="sxs-lookup"><span data-stu-id="66615-464">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```csharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="66615-465">Beachten Sie, dass dies nur für Abfragen ohne Nachverfolgung nützlich ist, weil alle Abfragen mit Nachverfolgung dieses Verhalten bereits aufweisen.</span><span class="sxs-lookup"><span data-stu-id="66615-465">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="66615-466">Nach einer Überprüfung der API wird auch die `PerformIdentityResolution`-Syntax geändert.</span><span class="sxs-lookup"><span data-stu-id="66615-466">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="66615-467">Weitere Informationen dazu finden Sie im [Issue 19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="66615-467">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="66615-468">Die Dokumentation finden Sie im [Issue 1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span><span class="sxs-lookup"><span data-stu-id="66615-468">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="66615-469">Gespeicherte (persistente) berechnete Spalten</span><span class="sxs-lookup"><span data-stu-id="66615-469">Stored (persisted) computed columns</span></span>

<span data-ttu-id="66615-470">Die meisten Datenbanken lassen zu, dass berechnete Spaltenwerte nach der Berechnung gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="66615-470">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="66615-471">Zwar wird dadurch Speicherplatz belegt, jedoch wird die berechnete Spalte nur einmal pro Update berechnet, anstatt jedes Mal, wenn der Wert abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="66615-471">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="66615-472">Dies ermöglicht auch die Indizierung der Spalte für einige Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="66615-472">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="66615-473">EF Core 5.0 ermöglicht die Konfiguration von berechneten Spalten als gespeichert.</span><span class="sxs-lookup"><span data-stu-id="66615-473">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="66615-474">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-474">For example:</span></span>

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="66615-475">Berechnete Spalten in SQLite</span><span class="sxs-lookup"><span data-stu-id="66615-475">SQLite computed columns</span></span>

<span data-ttu-id="66615-476">EF Core unterstützt nun berechnete Spalten in SQLite-Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="66615-476">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="66615-477">Preview 4</span><span class="sxs-lookup"><span data-stu-id="66615-477">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="66615-478">Konfigurieren von Datenbankgenauigkeit/Skalierung im Modell</span><span class="sxs-lookup"><span data-stu-id="66615-478">Configure database precision/scale in model</span></span>

<span data-ttu-id="66615-479">Genauigkeit und Skalierung einer Eigenschaft können jetzt mit dem Modellgenerator angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="66615-479">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="66615-480">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-480">For example:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="66615-481">Genauigkeit und Skalierung können weiterhin über den vollständigen Datenbanktyp festgelegt werden, z. B. „decimal(16,4)“.</span><span class="sxs-lookup"><span data-stu-id="66615-481">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span>

<span data-ttu-id="66615-482">Die Dokumentation finden Sie im Issue [527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span><span class="sxs-lookup"><span data-stu-id="66615-482">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="66615-483">Angeben des Standardfüllfaktors für SQL Server-Indizes</span><span class="sxs-lookup"><span data-stu-id="66615-483">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="66615-484">Der Füllfaktor kann nun beim Erstellen eines Indexes auf SQL Server angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="66615-484">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="66615-485">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-485">For example:</span></span>

```csharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="66615-486">Preview 3</span><span class="sxs-lookup"><span data-stu-id="66615-486">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="66615-487">Gefilterte Include-Funktion</span><span class="sxs-lookup"><span data-stu-id="66615-487">Filtered Include</span></span>

<span data-ttu-id="66615-488">Die Include-Methode unterstützt jetzt das Filtern der enthaltenen Entitäten.</span><span class="sxs-lookup"><span data-stu-id="66615-488">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="66615-489">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-489">For example:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="66615-490">Mit dieser Abfrage werden Blogs zusammen mit den jeweiligen Beiträgen zurückgegeben, jedoch nur, wenn der Titel „Cheese“ enthält.</span><span class="sxs-lookup"><span data-stu-id="66615-490">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="66615-491">Skip und Take können auch verwendet werden, um die Anzahl der enthaltenen Entitäten zu verringern.</span><span class="sxs-lookup"><span data-stu-id="66615-491">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="66615-492">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-492">For example:</span></span>

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```

<span data-ttu-id="66615-493">Mit dieser Abfrage werden Blogs mit höchstens fünf Beiträgen zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="66615-493">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="66615-494">Ausführliche Informationen finden Sie in der [Dokumentation zu Include](xref:core/querying/related-data#filtered-include).</span><span class="sxs-lookup"><span data-stu-id="66615-494">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="66615-495">Neue ModelBuilder-API für Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="66615-495">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="66615-496">Navigationseigenschaften werden in erster Linie beim [Definieren von Beziehungen](xref:core/modeling/relationships) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="66615-496">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="66615-497">Die neue `Navigation`-Methode kann jedoch in den Fällen verwendet werden, in denen Navigationseigenschaften eine zusätzliche Konfiguration erfordern.</span><span class="sxs-lookup"><span data-stu-id="66615-497">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="66615-498">So können Sie z. B. ein Unterstützungsfeld für die Navigation festlegen, wenn das Feld nach Konvention nicht gefunden wird:</span><span class="sxs-lookup"><span data-stu-id="66615-498">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```csharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="66615-499">Beachten Sie, dass die `Navigation`-API die Beziehungskonfiguration nicht ersetzt.</span><span class="sxs-lookup"><span data-stu-id="66615-499">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="66615-500">Stattdessen ermöglicht sie eine zusätzliche Konfiguration der Navigationseigenschaften in bereits ermittelten oder definierten Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="66615-500">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="66615-501">Weitere Informationen finden Sie unter [Konfigurieren von Navigationseigenschaften](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="66615-501">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="66615-502">Neue Befehlszeilenparameter für Namespaces und Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="66615-502">New command-line parameters for namespaces and connection strings</span></span>

<span data-ttu-id="66615-503">Bei Migrationen und Gerüstbau können Namespaces jetzt in der Befehlszeile angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="66615-503">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="66615-504">So können Sie beispielsweise ein Reverse Engineering für eine Datenbank durchführen, um die Kontext- und Modellklassen in verschiedenen Namespaces zu platzieren:</span><span class="sxs-lookup"><span data-stu-id="66615-504">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="66615-505">Ausführliche Informationen finden Sie in den Dokumentationen zu [Migrationen](xref:core/managing-schemas/migrations/index#namespaces) und [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span><span class="sxs-lookup"><span data-stu-id="66615-505">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="66615-506">Eine Verbindungszeichenfolge kann nun auch an den `database-update`-Befehl übermittelt werden:</span><span class="sxs-lookup"><span data-stu-id="66615-506">Also, a connection string can now be passed to the `database-update` command:</span></span>

```dotnetcli
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="66615-507">Ausführliche Informationen finden Sie in der [Entity Framework Core Tools-Referenz – .NET-CLI](xref:core/cli/dotnet#dotnet-ef-database-update).</span><span class="sxs-lookup"><span data-stu-id="66615-507">See the [Tools documentation](xref:core/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="66615-508">Gleichwertige Parameter wurden den PowerShell-Befehlen hinzugefügt, die in der Paket-Manager-Konsole in Visual Studio verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66615-508">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="66615-509">EnableDetailedErrors wieder verfügbar</span><span class="sxs-lookup"><span data-stu-id="66615-509">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="66615-510">Aus Leistungsgründen führt EF keine zusätzlichen NULL-Überprüfungen durch, wenn Werte aus der Datenbank gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="66615-510">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="66615-511">Dadurch können Ausnahmen ausgelöst werden, deren Ursache schwer zu ermitteln ist, wenn ein unerwarteter NULL-Wert auftritt.</span><span class="sxs-lookup"><span data-stu-id="66615-511">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="66615-512">Durch die Verwendung von `EnableDetailedErrors` wird eine zusätzliche NULL-Überprüfung auf Abfragen hinzugefügt, sodass diese Fehler mit geringem Leistungsaufwand einfacher auf ihre Ursache zurückzuführen sind.</span><span class="sxs-lookup"><span data-stu-id="66615-512">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>

<span data-ttu-id="66615-513">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-513">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="66615-514">Die Dokumentation finden Sie im Issue [955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span><span class="sxs-lookup"><span data-stu-id="66615-514">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="66615-515">Cosmos-Partitionsschlüssel</span><span class="sxs-lookup"><span data-stu-id="66615-515">Cosmos partition keys</span></span>

<span data-ttu-id="66615-516">Der Partitionsschlüssel, der für eine bestimmte Abfrage verwendet werden soll, kann jetzt in der Abfrage angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="66615-516">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="66615-517">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-517">For example:</span></span>

```csharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="66615-518">Die Dokumentation finden Sie im Issue [2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span><span class="sxs-lookup"><span data-stu-id="66615-518">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="66615-519">Unterstützung für die DATALENGTH-Funktion von SQL Server</span><span class="sxs-lookup"><span data-stu-id="66615-519">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="66615-520">Auf diese kann mit der neuen `EF.Functions.DataLength`-Methode zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="66615-520">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="66615-521">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-521">For example:</span></span>

```csharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a><span data-ttu-id="66615-522">Vorschau 2</span><span class="sxs-lookup"><span data-stu-id="66615-522">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="66615-523">Verwenden eines C#-Attributs zum Angeben eines Eigenschaftsunterstützungsfelds</span><span class="sxs-lookup"><span data-stu-id="66615-523">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="66615-524">Es kann jetzt ein C#-Attribut verwendet werden, um das Unterstützungsfeld für eine Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="66615-524">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="66615-525">Dieses Attribut ermöglicht es EF Core, wie sonst auch in das Unterstützungsfeld zu schreiben und daraus zu lesen, auch wenn das Unterstützungsfeld nicht automatisch gefunden werden kann.</span><span class="sxs-lookup"><span data-stu-id="66615-525">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="66615-526">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-526">For example:</span></span>

```csharp
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

<span data-ttu-id="66615-527">Die Dokumentation finden Sie im Issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span><span class="sxs-lookup"><span data-stu-id="66615-527">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="66615-528">Vollständige Diskriminatorzuordnung</span><span class="sxs-lookup"><span data-stu-id="66615-528">Complete discriminator mapping</span></span>

<span data-ttu-id="66615-529">EF Core verwendet eine Diskriminatorspalte für die [TPH-Zuordnung (Tabelle pro Hierarchie) von Vererbungshierarchien](xref:core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="66615-529">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](xref:core/modeling/inheritance).</span></span> <span data-ttu-id="66615-530">Es sind einige Leistungsverbesserungen möglich, solange EF Core alle möglichen Werte für den Diskriminator kennt.</span><span class="sxs-lookup"><span data-stu-id="66615-530">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="66615-531">Diese Verbesserungen sind nun in EF Core 5.0 implementiert.</span><span class="sxs-lookup"><span data-stu-id="66615-531">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="66615-532">Frühere Versionen von EF Core generierten beispielsweise für Abfragen immer einen SQL-Code wie den folgenden, in dem alle Typen in einer Hierarchie zurückgegeben wurden:</span><span class="sxs-lookup"><span data-stu-id="66615-532">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="66615-533">EF Core 5.0 generiert nun Folgendes, wenn eine vollständige Diskriminatorzuordnung konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="66615-533">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="66615-534">Ab Vorschauversion 3 wird dies das Standardverhalten.</span><span class="sxs-lookup"><span data-stu-id="66615-534">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="66615-535">Leistungsverbesserungen in Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="66615-535">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="66615-536">Es wurden zwei Leistungsverbesserungen für SQLite vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="66615-536">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="66615-537">Das Abrufen von Binär- und Zeichenfolgendaten mit GetBytes, GetChars und GetTextReader ist nun effizienter durch die Verwendung von SqliteBlob und Datenströmen.</span><span class="sxs-lookup"><span data-stu-id="66615-537">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="66615-538">Die Initialisierung von SqliteConnection ist nun verzögert.</span><span class="sxs-lookup"><span data-stu-id="66615-538">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="66615-539">Diese Verbesserungen wurden am ADO.NET-Anbieter Microsoft.Data.Sqlite vorgenommen und führen daher auch zu Leistungsverbesserungen außerhalb von EF Core.</span><span class="sxs-lookup"><span data-stu-id="66615-539">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="66615-540">Vorschauversion 1</span><span class="sxs-lookup"><span data-stu-id="66615-540">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="66615-541">Einfache Protokollierung</span><span class="sxs-lookup"><span data-stu-id="66615-541">Simple logging</span></span>

<span data-ttu-id="66615-542">Dieses Feature fügt eine Funktion hinzu, die `Database.Log` in EF6 ähnelt.</span><span class="sxs-lookup"><span data-stu-id="66615-542">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="66615-543">Es bietet eine einfache Möglichkeit zum Abrufen von Protokollen aus EF Core, ohne ein externes Protokollierungsframework konfigurieren zu müssen.</span><span class="sxs-lookup"><span data-stu-id="66615-543">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="66615-544">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 5. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="66615-544">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="66615-545">Zusätzliche Dokumentation finden Sie im Issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span><span class="sxs-lookup"><span data-stu-id="66615-545">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="66615-546">Einfache Möglichkeit zum Abrufen von generiertem SQL-Code</span><span class="sxs-lookup"><span data-stu-id="66615-546">Simple way to get generated SQL</span></span>

<span data-ttu-id="66615-547">In EF Core 5.0 wird die Erweiterungsmethode `ToQueryString` eingeführt, die den von EF Core beim Ausführen einer LINQ-Abfrage generierten SQL-Code zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="66615-547">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="66615-548">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 9. Januar 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="66615-548">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="66615-549">Zusätzliche Dokumentation finden Sie im Issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span><span class="sxs-lookup"><span data-stu-id="66615-549">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="66615-550">Verwenden eines C#-Attributs zum Angeben des Fehlens eines Schlüssels einer Entität</span><span class="sxs-lookup"><span data-stu-id="66615-550">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="66615-551">Ein Entitätstyp kann nun mithilfe des neuen Attributs `KeylessAttribute` als schlüssellos konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="66615-551">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="66615-552">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-552">For example:</span></span>

```csharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="66615-553">Die Dokumentation finden Sie im Issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span><span class="sxs-lookup"><span data-stu-id="66615-553">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="66615-554">Ändern von Verbindung oder Verbindungszeichenfolge in initialisiertem DbContext möglich</span><span class="sxs-lookup"><span data-stu-id="66615-554">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="66615-555">Es ist jetzt einfacher, eine DbContext-Instanz ohne Verbindung oder Verbindungszeichenfolge zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="66615-555">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="66615-556">Die Verbindung oder Verbindungszeichenfolge kann jetzt auch in der Kontextinstanz verändert werden.</span><span class="sxs-lookup"><span data-stu-id="66615-556">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="66615-557">Mithilfe dieses Features kann ein und dieselbe Kontextinstanz dynamisch eine Verbindung mit verschiedenen Datenbanken herstellen.</span><span class="sxs-lookup"><span data-stu-id="66615-557">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="66615-558">Die Dokumentation finden Sie im Issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span><span class="sxs-lookup"><span data-stu-id="66615-558">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="66615-559">Proxys zur Änderungsnachverfolgung</span><span class="sxs-lookup"><span data-stu-id="66615-559">Change-tracking proxies</span></span>

<span data-ttu-id="66615-560">EF Core kann jetzt Runtimeproxys generieren, die automatisch [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) und [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) implementieren.</span><span class="sxs-lookup"><span data-stu-id="66615-560">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span></span> <span data-ttu-id="66615-561">Diese melden dann Wertänderungen bei Entitätseigenschaften direkt an EF Core, sodass die Notwendigkeit einer Überprüfung auf Änderungen entfällt.</span><span class="sxs-lookup"><span data-stu-id="66615-561">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="66615-562">Für Proxys gelten jedoch bestimmte Einschränkungen, daher eignen sie sich nicht in jedem Fall.</span><span class="sxs-lookup"><span data-stu-id="66615-562">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="66615-563">Die Dokumentation finden Sie im Issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span><span class="sxs-lookup"><span data-stu-id="66615-563">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="66615-564">Erweiterte Debugansichten</span><span class="sxs-lookup"><span data-stu-id="66615-564">Enhanced debug views</span></span>

<span data-ttu-id="66615-565">Debugansichten sind eine einfache Möglichkeit, beim Debuggen von Problemen interne Vorgänge in EF Core zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="66615-565">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="66615-566">Ein Debugansicht für das Modell wurde bereits vor einiger Zeit implementiert.</span><span class="sxs-lookup"><span data-stu-id="66615-566">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="66615-567">Für EF Core 5.0 haben wir die Modellansicht vereinfacht und im Zustands-Manager eine neue Debugansicht für nachverfolgte Entitäten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="66615-567">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="66615-568">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 12. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="66615-568">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="66615-569">Zusätzliche Dokumentation finden Sie im Issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span><span class="sxs-lookup"><span data-stu-id="66615-569">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="66615-570">Verbesserte Verarbeitung von NULL-Semantik in der Datenbank</span><span class="sxs-lookup"><span data-stu-id="66615-570">Improved handling of database null semantics</span></span>

<span data-ttu-id="66615-571">Relationale Datenbanken behandeln NULL in der Regel als unbekannten Wert und daher als ungleich jedem anderen NULL-Wert.</span><span class="sxs-lookup"><span data-stu-id="66615-571">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="66615-572">C# hingegen behandelt NULL als definierten Wert, der mit jedem anderen NULL-Wert übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="66615-572">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="66615-573">EF Core übersetzt Abfragen standardmäßig so, dass sie die NULL-Semantik von C# verwenden.</span><span class="sxs-lookup"><span data-stu-id="66615-573">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="66615-574">In EF Core 5.0 wurde die Effizienz dieser Übersetzungen erheblich verbessert.</span><span class="sxs-lookup"><span data-stu-id="66615-574">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="66615-575">Die Dokumentation finden Sie im Issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span><span class="sxs-lookup"><span data-stu-id="66615-575">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="66615-576">Indexereigenschaften</span><span class="sxs-lookup"><span data-stu-id="66615-576">Indexer properties</span></span>

<span data-ttu-id="66615-577">EF Core 5.0 unterstützt die Zuordnung von C#-Indexereigenschaften.</span><span class="sxs-lookup"><span data-stu-id="66615-577">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="66615-578">Durch diese Eigenschaften können Entitäten als Eigenschaftenbehälter fungieren, wobei Spalten benannten Eigenschaften im Behälter zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="66615-578">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="66615-579">Die Dokumentation finden Sie im Issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span><span class="sxs-lookup"><span data-stu-id="66615-579">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="66615-580">Generieren von CHECK-Einschränkungen für Enumerationszuordnungen</span><span class="sxs-lookup"><span data-stu-id="66615-580">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="66615-581">EF Core 5.0-Migrationen können jetzt CHECK-Einschränkungen aus Zuordnungen der enum-Eigenschaft generieren.</span><span class="sxs-lookup"><span data-stu-id="66615-581">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="66615-582">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-582">For example:</span></span>

```sql
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="66615-583">Die Dokumentation finden Sie im Issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span><span class="sxs-lookup"><span data-stu-id="66615-583">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="66615-584">IsRelational</span><span class="sxs-lookup"><span data-stu-id="66615-584">IsRelational</span></span>

<span data-ttu-id="66615-585">Zusätzlich zu den existierenden `IsSqlServer`-, `IsSqlite`- und `IsInMemory`-Methoden wurde eine neue `IsRelational`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="66615-585">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="66615-586">Diese Methode kann verwendet werden, um zu testen, ob DbContext einen Anbieter einer relationalen Datenbank verwendet.</span><span class="sxs-lookup"><span data-stu-id="66615-586">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="66615-587">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-587">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="66615-588">Die Dokumentation finden Sie im Issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span><span class="sxs-lookup"><span data-stu-id="66615-588">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="66615-589">Optimistische Nebenläufigkeit in Cosmos mit Entitätstags (ETags)</span><span class="sxs-lookup"><span data-stu-id="66615-589">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="66615-590">Der Azure Cosmos DB-Datenbankanbieter unterstützt nun optimistische Nebenläufigkeit mithilfe von ETags.</span><span class="sxs-lookup"><span data-stu-id="66615-590">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="66615-591">Verwenden Sie das Modellerstellungstool in OnModelCreating, um ein ETag zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="66615-591">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```csharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="66615-592">SaveChanges löst dann eine `DbUpdateConcurrencyException`-Ausnahme für einen Nebenläufigkeitskonflikt aus, die [verarbeitet werden kann](xref:core/saving/concurrency), z. B. um Wiederholungsversuche zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="66615-592">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](xref:core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="66615-593">Die Dokumentation finden Sie im Issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span><span class="sxs-lookup"><span data-stu-id="66615-593">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="66615-594">Abfrageübersetzungen für weitere DateTime-Konstrukte</span><span class="sxs-lookup"><span data-stu-id="66615-594">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="66615-595">Abfragen mit neuer DateTime-Konstruktion werden jetzt übersetzt.</span><span class="sxs-lookup"><span data-stu-id="66615-595">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="66615-596">Außerdem werden nun die folgenden SQL Server-Funktionen zugeordnet:</span><span class="sxs-lookup"><span data-stu-id="66615-596">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="66615-597">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="66615-597">DateDiffWeek</span></span>
* <span data-ttu-id="66615-598">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="66615-598">DateFromParts</span></span>

<span data-ttu-id="66615-599">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-599">For example:</span></span>

```csharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="66615-600">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="66615-600">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="66615-601">Abfrageübersetzungen für weitere Bytearraykonstrukte</span><span class="sxs-lookup"><span data-stu-id="66615-601">Query translations for more byte array constructs</span></span>

<span data-ttu-id="66615-602">Abfragen, die Contains, Length, SequenceEqual usw. in byte[]-Eigenschaften verwenden, werden jetzt in SQL übersetzt.</span><span class="sxs-lookup"><span data-stu-id="66615-602">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="66615-603">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 5. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="66615-603">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="66615-604">Zusätzliche Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="66615-604">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="66615-605">Abfrageübersetzung für Reverse</span><span class="sxs-lookup"><span data-stu-id="66615-605">Query translation for Reverse</span></span>

<span data-ttu-id="66615-606">Abfragen, die `Reverse` verwenden, werden jetzt übersetzt.</span><span class="sxs-lookup"><span data-stu-id="66615-606">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="66615-607">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-607">For example:</span></span>

```csharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="66615-608">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="66615-608">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="66615-609">Abfrageübersetzung für bitweise Operatoren</span><span class="sxs-lookup"><span data-stu-id="66615-609">Query translation for bitwise operators</span></span>

<span data-ttu-id="66615-610">Abfragen, die bitweise Operatoren verwenden, werden jetzt in mehr Fällen übersetzt. Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66615-610">Queries using bitwise operators are now translated in more cases For example:</span></span>

```csharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="66615-611">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="66615-611">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="66615-612">Abfrageübersetzung für Zeichenfolgen in Cosmos</span><span class="sxs-lookup"><span data-stu-id="66615-612">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="66615-613">Abfragen, die die Zeichenfolgenmethoden Contains, StartsWith und EndsWith verwenden, werden jetzt übersetzt, wenn der Azure Cosmos DB-Anbieter verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="66615-613">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="66615-614">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="66615-614">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
