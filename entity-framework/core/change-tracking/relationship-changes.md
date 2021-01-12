---
title: Ändern von Fremdschlüsseln und Navigationen-EF Core
description: Ändern von Beziehungen zwischen Entitäten durch manipulieren von Fremdschlüsseln und Navigationen
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/relationship-changes
ms.openlocfilehash: ac2110509b6748e85411dbb14989522465925ecf
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129596"
---
# <a name="changing-foreign-keys-and-navigations"></a><span data-ttu-id="cd372-103">Ändern von Fremdschlüsseln und Navigationen</span><span class="sxs-lookup"><span data-stu-id="cd372-103">Changing Foreign Keys and Navigations</span></span>

## <a name="overview-of-foreign-keys-and-navigations"></a><span data-ttu-id="cd372-104">Übersicht über Fremdschlüssel und Navigationen</span><span class="sxs-lookup"><span data-stu-id="cd372-104">Overview of foreign keys and navigations</span></span>

<span data-ttu-id="cd372-105">Beziehungen in einem Entity Framework Core (EF Core)-Modell werden mithilfe von Fremdschlüsseln (Key Keys, f) dargestellt.</span><span class="sxs-lookup"><span data-stu-id="cd372-105">Relationships in an Entity Framework Core (EF Core) model are represented using foreign keys (FKs).</span></span> <span data-ttu-id="cd372-106">Ein FK besteht aus einer oder mehreren Eigenschaften der abhängigen oder untergeordneten Entität in der Beziehung.</span><span class="sxs-lookup"><span data-stu-id="cd372-106">An FK consists of one or more properties on the dependent or child entity in the relationship.</span></span> <span data-ttu-id="cd372-107">Diese abhängige/untergeordnete Entität ist einer angegebenen Prinzipal-/Parent-Entität zugeordnet, wenn die Werte der Fremdschlüssel Eigenschaften des abhängigen/untergeordneten Elements den Werten der alternativen oder Primärschlüssel Eigenschaften (PK) des Prinzipals/übergeordneten Elements entsprechen.</span><span class="sxs-lookup"><span data-stu-id="cd372-107">This dependent/child entity is associated with a given principal/parent entity when the values of the foreign key properties on the dependent/child match the values of the alternate or primary key (PK) properties on the principal/parent.</span></span>

<span data-ttu-id="cd372-108">Fremdschlüssel sind eine gute Möglichkeit, Beziehungen in der Datenbank zu speichern und zu bearbeiten. Sie sind jedoch beim Arbeiten mit mehreren verknüpften Entitäten im Anwendungscode nicht sehr benutzerfreundlich.</span><span class="sxs-lookup"><span data-stu-id="cd372-108">Foreign keys are a good way to store and manipulate relationships in the database, but are not very friendly when working with multiple related entities in application code.</span></span> <span data-ttu-id="cd372-109">Aus diesem Grund ist die meisten EF Core Modelle auch "Navigationen" über die FK-Darstellung.</span><span class="sxs-lookup"><span data-stu-id="cd372-109">Therefore, most EF Core models also layer "navigations" over the FK representation.</span></span> <span data-ttu-id="cd372-110">Navigationen bilden c#-/.net Verweise zwischen Entitäts Instanzen, die die Zuordnungen widerspiegeln, die durch die Übereinstimmung von Fremdschlüssel Werten mit primären oder alternativen Schlüsselwerten</span><span class="sxs-lookup"><span data-stu-id="cd372-110">Navigations form C#/.NET references between entity instances that reflect the associations found by matching foreign key values to primary or alternate key values.</span></span>

<span data-ttu-id="cd372-111">Navigationen können auf beiden Seiten der Beziehung nur auf einer Seite oder gar nicht verwendet werden, wobei nur die FK-Eigenschaft belassen wird.</span><span class="sxs-lookup"><span data-stu-id="cd372-111">Navigations can be used on both sides of the relationship, on one side only, or not at all, leaving only the FK property.</span></span> <span data-ttu-id="cd372-112">Die FK-Eigenschaft kann ausgeblendet werden, indem Sie eine [Schatten Eigenschaft](xref:core/modeling/shadow-properties)ist.</span><span class="sxs-lookup"><span data-stu-id="cd372-112">The FK property can be hidden by making it a [shadow property](xref:core/modeling/shadow-properties).</span></span> <span data-ttu-id="cd372-113">Weitere Informationen zum Modellieren von Beziehungen finden Sie unter [Beziehungen](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="cd372-113">See [Relationships](xref:core/modeling/relationships) for more information on modelling relationships.</span></span>

> [!TIP]
> <span data-ttu-id="cd372-114">In diesem Dokument wird davon ausgegangen, dass die Entitäts Zustände und die Grundlagen EF Core Änderungs Nachverfolgung verstanden werden.</span><span class="sxs-lookup"><span data-stu-id="cd372-114">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="cd372-115">Weitere Informationen zu diesen Themen finden Sie [unter Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="cd372-115">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="cd372-116">Sie können den gesamten Code in diesem Dokument ausführen und Debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangingFKsAndNavigations).</span><span class="sxs-lookup"><span data-stu-id="cd372-116">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangingFKsAndNavigations).</span></span>

### <a name="example-model"></a><span data-ttu-id="cd372-117">Beispielmodell</span><span class="sxs-lookup"><span data-stu-id="cd372-117">Example model</span></span>

<span data-ttu-id="cd372-118">Das folgende Modell enthält vier Entitäts Typen mit Beziehungen zwischen Ihnen.</span><span class="sxs-lookup"><span data-stu-id="cd372-118">The following model contains four entity types with relationships between them.</span></span> <span data-ttu-id="cd372-119">Die Kommentare im Code geben an, welche Eigenschaften Fremdschlüssel, Primärschlüssel und Navigationen sind.</span><span class="sxs-lookup"><span data-stu-id="cd372-119">The comments in the code indicate which properties are foreign keys, primary keys, and navigations.</span></span>

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public int Id { get; set; } // Primary key
    public byte[] Banner { get; set; }

    public int BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation

    public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
}

public class Tag
{
    public int Id { get; set; } // Primary key
    public string Text { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Model)]

<span data-ttu-id="cd372-120">Die drei Beziehungen in diesem Modell lauten:</span><span class="sxs-lookup"><span data-stu-id="cd372-120">The three relationships in this model are:</span></span>

- <span data-ttu-id="cd372-121">Jeder Blog kann über viele Beiträge (1: n) verfügen:</span><span class="sxs-lookup"><span data-stu-id="cd372-121">Each blog can have many posts (one-to-many):</span></span>
  - <span data-ttu-id="cd372-122">`Blog` ist das Prinzipal/übergeordnete Element.</span><span class="sxs-lookup"><span data-stu-id="cd372-122">`Blog` is the principal/parent.</span></span>
  - <span data-ttu-id="cd372-123">`Post` ist das abhängige/untergeordnete Element.</span><span class="sxs-lookup"><span data-stu-id="cd372-123">`Post` is the dependent/child.</span></span> <span data-ttu-id="cd372-124">Sie enthält die FK-Eigenschaft `Post.BlogId` , deren Wert dem `Blog.Id` PK-Wert des zugehörigen Blogs entsprechen muss.</span><span class="sxs-lookup"><span data-stu-id="cd372-124">It contains the FK property `Post.BlogId`, the value of which must match the `Blog.Id` PK value of the related blog.</span></span>
  - <span data-ttu-id="cd372-125">`Post.Blog` ist eine Referenz Navigation von einem Beitrag zum zugehörigen Blog.</span><span class="sxs-lookup"><span data-stu-id="cd372-125">`Post.Blog` is a reference navigation from a post to the associated blog.</span></span> <span data-ttu-id="cd372-126">`Post.Blog` ist die umgekehrte Navigation für `Blog.Posts` .</span><span class="sxs-lookup"><span data-stu-id="cd372-126">`Post.Blog` is the inverse navigation for `Blog.Posts`.</span></span>
  - <span data-ttu-id="cd372-127">`Blog.Posts` ist eine Auflistungs Navigation von einem Blog zu allen zugeordneten Beiträgen.</span><span class="sxs-lookup"><span data-stu-id="cd372-127">`Blog.Posts` is a collection navigation from a blog to all the associated posts.</span></span> <span data-ttu-id="cd372-128">`Blog.Posts` ist die umgekehrte Navigation für `Post.Blog` .</span><span class="sxs-lookup"><span data-stu-id="cd372-128">`Blog.Posts` is the inverse navigation for `Post.Blog`.</span></span>
- <span data-ttu-id="cd372-129">Jeder Blog kann über ein Medienobjekt (eins zu eins) verfügen:</span><span class="sxs-lookup"><span data-stu-id="cd372-129">Each blog can have one assets (one-to-one):</span></span>
  - <span data-ttu-id="cd372-130">`Blog` ist das Prinzipal/übergeordnete Element.</span><span class="sxs-lookup"><span data-stu-id="cd372-130">`Blog` is the principal/parent.</span></span>
  - <span data-ttu-id="cd372-131">`BlogAssets` ist das abhängige/untergeordnete Element.</span><span class="sxs-lookup"><span data-stu-id="cd372-131">`BlogAssets` is the dependent/child.</span></span> <span data-ttu-id="cd372-132">Sie enthält die FK-Eigenschaft `BlogAssets.BlogId` , deren Wert dem `Blog.Id` PK-Wert des zugehörigen Blogs entsprechen muss.</span><span class="sxs-lookup"><span data-stu-id="cd372-132">It contains the FK property `BlogAssets.BlogId`, the value of which must match the `Blog.Id` PK value of the related blog.</span></span>
  - <span data-ttu-id="cd372-133">`BlogAssets.Blog` ist eine Referenz Navigation von den Assets zum zugeordneten Blog.</span><span class="sxs-lookup"><span data-stu-id="cd372-133">`BlogAssets.Blog` is a reference navigation from the assets to the associated blog.</span></span> <span data-ttu-id="cd372-134">`BlogAssets.Blog` ist die umgekehrte Navigation für `Blog.Assets` .</span><span class="sxs-lookup"><span data-stu-id="cd372-134">`BlogAssets.Blog` is the inverse navigation for `Blog.Assets`.</span></span>
  - <span data-ttu-id="cd372-135">`Blog.Assets` ist eine Referenz Navigation vom Blog zu den zugeordneten Ressourcen.</span><span class="sxs-lookup"><span data-stu-id="cd372-135">`Blog.Assets` is a reference navigation from the blog to the associated assets.</span></span> <span data-ttu-id="cd372-136">`Blog.Assets` ist die umgekehrte Navigation für `BlogAssets.Blog` .</span><span class="sxs-lookup"><span data-stu-id="cd372-136">`Blog.Assets` is the inverse navigation for `BlogAssets.Blog`.</span></span>
- <span data-ttu-id="cd372-137">Jeder Beitrag kann über viele Tags verfügen, und jedes Tag kann über viele Beiträge verfügen (m:n):</span><span class="sxs-lookup"><span data-stu-id="cd372-137">Each post can have many tags and each tag can have many posts (many-to-many):</span></span>
  - <span data-ttu-id="cd372-138">M:n-Beziehungen sind eine weitere Schicht über 2 1-zu-viele-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="cd372-138">Many-to-many relationships are a further layer over two one-to-many relationships.</span></span> <span data-ttu-id="cd372-139">M:n-Beziehungen werden weiter unten in diesem Dokument behandelt.</span><span class="sxs-lookup"><span data-stu-id="cd372-139">Many-to-many relationships are covered later in this document.</span></span>
  - <span data-ttu-id="cd372-140">`Post.Tags` ist eine Auflistungs Navigation von einem Beitrag zu allen zugeordneten Tags.</span><span class="sxs-lookup"><span data-stu-id="cd372-140">`Post.Tags` is a collection navigation from a post to all the associated tags.</span></span> <span data-ttu-id="cd372-141">`Post.Tags` ist die umgekehrte Navigation für `Tag.Posts` .</span><span class="sxs-lookup"><span data-stu-id="cd372-141">`Post.Tags` is the inverse navigation for `Tag.Posts`.</span></span>
  - <span data-ttu-id="cd372-142">`Tag.Posts` ist eine Auflistungs Navigation von einem Tag zu allen zugeordneten Beiträgen.</span><span class="sxs-lookup"><span data-stu-id="cd372-142">`Tag.Posts` is a collection navigation from a tag to all the associated posts.</span></span> <span data-ttu-id="cd372-143">`Tag.Posts` ist die umgekehrte Navigation für `Post.Tags` .</span><span class="sxs-lookup"><span data-stu-id="cd372-143">`Tag.Posts` is the inverse navigation for `Post.Tags`.</span></span>

<span data-ttu-id="cd372-144">Weitere Informationen zum Modellieren und Konfigurieren von Beziehungen finden Sie unter [Beziehungen](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="cd372-144">See [Relationships](xref:core/modeling/relationships) for more information on how to model and configure relationships.</span></span>

## <a name="relationship-fixup"></a><span data-ttu-id="cd372-145">Beziehungs Korrektur</span><span class="sxs-lookup"><span data-stu-id="cd372-145">Relationship fixup</span></span>

<span data-ttu-id="cd372-146">EF Core behält die Navigation mit Fremdschlüssel Werten bei und umgekehrt.</span><span class="sxs-lookup"><span data-stu-id="cd372-146">EF Core keeps navigations in alignment with foreign key values and vice versa.</span></span> <span data-ttu-id="cd372-147">Das heißt, wenn sich ein Fremdschlüssel Wert ändert, sodass er nun auf eine andere Prinzipal/übergeordnete Entität verweist, werden die Navigationen aktualisiert, um diese Änderung widerzuspiegeln.</span><span class="sxs-lookup"><span data-stu-id="cd372-147">That is, if a foreign key value changes such that it now refers to a different principal/parent entity, then the navigations are updated to reflect this change.</span></span> <span data-ttu-id="cd372-148">Wenn eine Navigation geändert wird, werden die Fremdschlüssel Werte der beteiligten Entitäten entsprechend aktualisiert, um diese Änderung widerzuspiegeln.</span><span class="sxs-lookup"><span data-stu-id="cd372-148">Likewise, if a navigation is changed, then the foreign key values of the entities involved are updated to reflect this change.</span></span> <span data-ttu-id="cd372-149">Dies wird als "Beziehungs Korrektur" bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="cd372-149">This is called "relationship fixup".</span></span>

### <a name="fixup-by-query"></a><span data-ttu-id="cd372-150">Korrektur nach Abfrage</span><span class="sxs-lookup"><span data-stu-id="cd372-150">Fixup by query</span></span>

<span data-ttu-id="cd372-151">Fixup tritt zuerst auf, wenn Entitäten von der Datenbank abgefragt werden.</span><span class="sxs-lookup"><span data-stu-id="cd372-151">Fixup first occurs when entities are queried from the database.</span></span> <span data-ttu-id="cd372-152">Die Datenbank verfügt nur über Fremdschlüssel Werte. wenn EF Core eine Entitäts Instanz aus der Datenbank erstellt, verwendet Sie die Fremdschlüssel Werte zum Festlegen der Verweis Navigation und zum Hinzufügen von Entitäten zur Auflistungs Navigation nach Bedarf.</span><span class="sxs-lookup"><span data-stu-id="cd372-152">The database has only foreign key values, so when EF Core creates an entity instance from the database it uses the foreign key values to set reference navigations and add entities to collection navigations as appropriate.</span></span> <span data-ttu-id="cd372-153">Stellen Sie sich beispielsweise eine Abfrage nach Blogs und den zugehörigen Beiträgen und Assets vor:</span><span class="sxs-lookup"><span data-stu-id="cd372-153">For example, consider a query for blogs and its associated posts and assets:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .ToList();

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_1)]

<span data-ttu-id="cd372-154">Für jeden Blog wird EF Core zuerst eine- `Blog` Instanz erstellen.</span><span class="sxs-lookup"><span data-stu-id="cd372-154">For each blog, EF Core will first create a `Blog` instance.</span></span> <span data-ttu-id="cd372-155">Wenn dann jeder Beitrag aus der Datenbank geladen wird, `Post.Blog` wird die Verweis Navigation so festgelegt, dass Sie auf den zugehörigen Blog verweist.</span><span class="sxs-lookup"><span data-stu-id="cd372-155">Then, as each post is loaded from the database its `Post.Blog` reference navigation is set to point to the associated blog.</span></span> <span data-ttu-id="cd372-156">Entsprechend wird das Posting der Auflistungs `Blog.Posts` Navigation hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="cd372-156">Likewise, the post is added to the `Blog.Posts` collection navigation.</span></span> <span data-ttu-id="cd372-157">Dasselbe geschieht mit `BlogAssets` , außer in diesem Fall sind beide Navigationen Verweise.</span><span class="sxs-lookup"><span data-stu-id="cd372-157">The same thing happens with `BlogAssets`, except in this case both navigations are references.</span></span> <span data-ttu-id="cd372-158">Die `Blog.Assets` Navigation ist so festgelegt, dass Sie auf die Assets-Instanz verweist, und die `BlogAsserts.Blog` Navigation wird so festgelegt, dass Sie auf die Blog Instanz verweist.</span><span class="sxs-lookup"><span data-stu-id="cd372-158">The `Blog.Assets` navigation is set to point to the assets instance, and the `BlogAsserts.Blog` navigation is set to point to the blog instance.</span></span>

<span data-ttu-id="cd372-159">Sehen Sie sich die [Debugansicht](xref:core/change-tracking/debug-views) für die Änderungs Nachverfolgung an, nachdem diese Abfrage zwei Blogs anzeigt, die jeweils ein Objekt und zwei Beiträge nachverfolgt haben:</span><span class="sxs-lookup"><span data-stu-id="cd372-159">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after this query shows two blogs, each with one assets and two posts being tracked:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="cd372-160">In der Debugansicht werden sowohl Schlüsselwerte als auch Navigationen angezeigt.</span><span class="sxs-lookup"><span data-stu-id="cd372-160">The debug view shows both key values and navigations.</span></span> <span data-ttu-id="cd372-161">Navigationen werden mithilfe der Primärschlüssel Werte der zugehörigen Entitäten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="cd372-161">Navigations are shown using the primary key values of the related entities.</span></span> <span data-ttu-id="cd372-162">`Posts: [{Id: 1}, {Id: 2}]`In der obigen Ausgabe gibt beispielsweise an, dass die Auflistungs `Blog.Posts` Navigation zwei zugehörige Beiträge mit den primär Schlüsseln 1 und 2 enthält.</span><span class="sxs-lookup"><span data-stu-id="cd372-162">For example, `Posts: [{Id: 1}, {Id: 2}]` in the output above indicates that the `Blog.Posts` collection navigation contains two related posts with primary keys 1 and 2 respectively.</span></span> <span data-ttu-id="cd372-163">Entsprechend zeigt die Zeile für jeden Beitrag, der dem ersten Blog zugeordnet ist, an `Blog: {Id: 1}` , dass die `Post.Blog` Navigation auf den Blog mit dem Primärschlüssel 1 verweist.</span><span class="sxs-lookup"><span data-stu-id="cd372-163">Similarly, for each post associated with the first blog, the `Blog: {Id: 1}` line indicates that the `Post.Blog` navigation references the Blog with primary key 1.</span></span>

### <a name="fixup-to-locally-tracked-entities"></a><span data-ttu-id="cd372-164">Reparieren von lokal überwachten Entitäten</span><span class="sxs-lookup"><span data-stu-id="cd372-164">Fixup to locally tracked entities</span></span>

<span data-ttu-id="cd372-165">Beziehungs Korrektur erfolgt auch zwischen Entitäten, die von einer Überwachungs Abfrage zurückgegeben werden, und Entitäten, die bereits von dbcontext verfolgt wurden.</span><span class="sxs-lookup"><span data-stu-id="cd372-165">Relationship fixup also happens between entities returned from a tracking query and entities already tracked by the DbContext.</span></span> <span data-ttu-id="cd372-166">Beispielsweise sollten Sie drei separate Abfragen für Blogs, Beiträge und Assets ausführen:</span><span class="sxs-lookup"><span data-stu-id="cd372-166">For example, consider executing three separate queries for blogs, posts, and assets:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var assets = context.Assets.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var posts = context.Posts.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
<span data-ttu-id="cd372-167">[! Code-csharp-[Relationship_fixup_2](../../../ samples / core / ChangeTracking / ChangingFKsAndNavigations / OptionalRelationshipsSamples.cs ? name = Relationship_fixup_2
) ] Wenn Sie die debugansichten erneut betrachten, werden nur die beiden Blogs nachverfolgt, nachdem die erste Abfrage durchgeführt wurde:</span><span class="sxs-lookup"><span data-stu-id="cd372-167">[!code-csharp[Relationship_fixup_2](../../../ samples / core / ChangeTracking / ChangingFKsAndNavigations / OptionalRelationshipsSamples.cs ? name = Relationship_fixup_2
) ] Looking again at the debug views, after the first query only the two blogs are tracked:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: []
```

<span data-ttu-id="cd372-168">Die `Blog.Assets` Referenz Navigation ist NULL, und die Auflistungs `Blog.Posts` Navigation ist leer, da derzeit keine zugeordneten Entitäten vom Kontext nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="cd372-168">The `Blog.Assets` reference navigations are null, and the `Blog.Posts` collection navigations are empty because no associated entities are currently being tracked by the context.</span></span>

<span data-ttu-id="cd372-169">Nach der zweiten Abfrage wurde die `Blogs.Assets` Verweis Navigation so festgeschrieben, dass Sie auf die neu verfolgten `BlogAsset` Instanzen verweist.</span><span class="sxs-lookup"><span data-stu-id="cd372-169">After the second query, the `Blogs.Assets` reference navigations have been fixed up to point to the newly tracked `BlogAsset` instances.</span></span> <span data-ttu-id="cd372-170">`BlogAssets.Blog`Entsprechend sind die Referenz Navigationen so festgelegt, dass Sie auf die entsprechende bereits verfolgte `Blog` Instanz verweisen.</span><span class="sxs-lookup"><span data-stu-id="cd372-170">Likewise, the `BlogAssets.Blog` reference navigations are set to point to the appropriate already tracked `Blog` instance.</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: []
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
```

<span data-ttu-id="cd372-171">Nach der dritten Abfrage enthält die Auflistungs `Blog.Posts` Navigation jetzt alle zugehörigen Beiträge, und die `Post.Blog` Verweise zeigen auf die entsprechende- `Blog` Instanz:</span><span class="sxs-lookup"><span data-stu-id="cd372-171">Finally, after the third query, the `Blog.Posts` collection navigations now contain all related posts, and the `Post.Blog` references point to the appropriate `Blog` instance:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="cd372-172">Dabei handelt es sich um denselben Endzustand wie mit der ursprünglichen einzelnen Abfrage, da EF Core eine aufwärtsnavigation als Entitäten korrigiert haben, auch wenn Sie aus mehreren verschiedenen Abfragen stammen.</span><span class="sxs-lookup"><span data-stu-id="cd372-172">This is the same end-state as was achieved with the original single query, since EF Core fixed up navigations as entities were tracked, even when coming from multiple different queries.</span></span>

> [!NOTE]
> <span data-ttu-id="cd372-173">Fixup bewirkt niemals, dass mehr Daten aus der Datenbank zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="cd372-173">Fixup never causes more data to be returned from the database.</span></span> <span data-ttu-id="cd372-174">Es werden nur Entitäten verbunden, die bereits von der Abfrage zurückgegeben wurden oder die bereits von dbcontext nachverfolgt wurden.</span><span class="sxs-lookup"><span data-stu-id="cd372-174">It only connects entities that are already returned by the query or already tracked by the DbContext.</span></span> <span data-ttu-id="cd372-175">Weitere Informationen zum Verarbeiten von Duplikaten beim Serialisieren von Entitäten finden Sie [unter Identitäts Auflösung in EF Core](xref:core/change-tracking/identity-resolution) .</span><span class="sxs-lookup"><span data-stu-id="cd372-175">See [Identity Resolution in EF Core](xref:core/change-tracking/identity-resolution) for information about handling duplicates when serializing entities.</span></span>

## <a name="changing-relationships-using-navigations"></a><span data-ttu-id="cd372-176">Ändern von Beziehungen mithilfe von Navigationen</span><span class="sxs-lookup"><span data-stu-id="cd372-176">Changing relationships using navigations</span></span>

<span data-ttu-id="cd372-177">Die einfachste Möglichkeit, die Beziehung zwischen zwei Entitäten zu ändern EF Core, besteht darin, eine Navigation zu ändern, ohne die umgekehrten Navigations-und FK-Werte entsprechend zu fixieren.</span><span class="sxs-lookup"><span data-stu-id="cd372-177">The easiest way to change the relationship between two entities is by manipulating a navigation, while leaving EF Core to fixup the inverse navigation and FK values appropriately.</span></span> <span data-ttu-id="cd372-178">Dies kann folgendermaßen erfolgen:</span><span class="sxs-lookup"><span data-stu-id="cd372-178">This can be done by:</span></span>

- <span data-ttu-id="cd372-179">Hinzufügen oder Entfernen einer Entität aus einer Auflistungs Navigation.</span><span class="sxs-lookup"><span data-stu-id="cd372-179">Adding or removing an entity from a collection navigation.</span></span>
- <span data-ttu-id="cd372-180">Ändern einer Verweis Navigation, um auf eine andere Entität zu verweisen oder auf NULL festzulegen.</span><span class="sxs-lookup"><span data-stu-id="cd372-180">Changing a reference navigation to point to a different entity, or setting it to null.</span></span>

### <a name="adding-or-removing-from-collection-navigations"></a><span data-ttu-id="cd372-181">Hinzufügen oder entfernen aus der Auflistungs Navigation</span><span class="sxs-lookup"><span data-stu-id="cd372-181">Adding or removing from collection navigations</span></span>

<span data-ttu-id="cd372-182">Verschieben wir z. b. einen der Beiträge aus dem Visual Studio-Blog in den .net-Blog.</span><span class="sxs-lookup"><span data-stu-id="cd372-182">For example, let's move one of the posts from the Visual Studio blog to the .NET blog.</span></span> <span data-ttu-id="cd372-183">Hierfür müssen zunächst die Blogs und Beiträge geladen werden, und anschließend wird der Beitrag aus der Navigations Sammlung in einem Blog in die Navigations Sammlung im anderen Blog verschoben:</span><span class="sxs-lookup"><span data-stu-id="cd372-183">This requires first loading the blogs and posts, and then moving the post from the navigation collection on one blog to the navigation collection on the other blog:</span></span>

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");
        var vsBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == "Visual Studio Blog");

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Changing_relationships_using_navigations_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_1)]

> [!TIP]
> <span data-ttu-id="cd372-184"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>Hier wird ein Aufrufen von benötigt, da der Zugriff auf die Debugansicht keine [Automatische Erkennung von Änderungen](xref:core/change-tracking/change-detection)bewirkt.</span><span class="sxs-lookup"><span data-stu-id="cd372-184">A call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is needed here because accessing the debug view does not cause [automatic detection of changes](xref:core/change-tracking/change-detection).</span></span>

<span data-ttu-id="cd372-185">Dies ist die Debugansicht, die nach dem Ausführen des obigen Codes gedruckt wurde:</span><span class="sxs-lookup"><span data-stu-id="cd372-185">This is the debug view printed after running the code above:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: [{Id: 4}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="cd372-186">Die `Blog.Posts` Navigation im .net-Blog umfasst nun drei Beiträge ( `Posts: [{Id: 1}, {Id: 2}, {Id: 3}]` ).</span><span class="sxs-lookup"><span data-stu-id="cd372-186">The `Blog.Posts` navigation on the .NET Blog now has three posts (`Posts: [{Id: 1}, {Id: 2}, {Id: 3}]`).</span></span> <span data-ttu-id="cd372-187">Ebenso hat die `Blog.Posts` Navigation im Visual Studio-Blog nur einen Beitrag ( `Posts: [{Id: 4}]` ).</span><span class="sxs-lookup"><span data-stu-id="cd372-187">Likewise, the `Blog.Posts` navigation on the Visual Studio blog only has one post (`Posts: [{Id: 4}]`).</span></span> <span data-ttu-id="cd372-188">Dies ist zu erwarten, da der Code diese Auflistungen explizit geändert hat.</span><span class="sxs-lookup"><span data-stu-id="cd372-188">This is to be expected since the code explicitly changed these collections.</span></span>

<span data-ttu-id="cd372-189">Interessanter ist, obwohl der Code die Navigation nicht explizit geändert hat `Post.Blog` , dass er so festgeschrieben wurde, dass er auf den Visual Studio-Blog ( `Blog: {Id: 1}` ) verweist.</span><span class="sxs-lookup"><span data-stu-id="cd372-189">More interestingly, even though the code did not explicitly change the `Post.Blog` navigation, it has been fixed-up to point to the Visual Studio blog (`Blog: {Id: 1}`).</span></span> <span data-ttu-id="cd372-190">Außerdem wurde der `Post.BlogId` Fremdschlüssel Wert so aktualisiert, dass er mit dem Primärschlüssel Wert des .net-Blogs identisch ist.</span><span class="sxs-lookup"><span data-stu-id="cd372-190">Also, the `Post.BlogId` foreign key value has been updated to match the primary key value of the .NET blog.</span></span> <span data-ttu-id="cd372-191">Diese Änderung am FK-Wert in wird dann in der Datenbank persistent gespeichert, wenn SaveChanges aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="cd372-191">This change to the FK value in then persisted to the database when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='3' (DbType = String), @p0='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

### <a name="changing-reference-navigations"></a><span data-ttu-id="cd372-192">Ändern der Verweis Navigation</span><span class="sxs-lookup"><span data-stu-id="cd372-192">Changing reference navigations</span></span>

<span data-ttu-id="cd372-193">Im vorherigen Beispiel wurde ein Beitrag von einem Blog zu einem anderen verschoben, indem die Sammlungs Navigation von Beiträgen in den einzelnen Blogs bearbeitet wurde.</span><span class="sxs-lookup"><span data-stu-id="cd372-193">In the previous example, a post was moved from one blog to another by manipulating the collection navigation of posts on each blog.</span></span> <span data-ttu-id="cd372-194">Das gleiche kann erreicht werden, indem Sie stattdessen die `Post.Blog` Verweis Navigation ändern, sodass Sie auf den neuen Blog verweist.</span><span class="sxs-lookup"><span data-stu-id="cd372-194">The same thing can be achieved by instead changing the `Post.Blog` reference navigation to point to the new blog.</span></span> <span data-ttu-id="cd372-195">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cd372-195">For example:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.Blog = dotNetBlog;
-->
[!code-csharp[Changing_relationships_using_navigations_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_2)]

<span data-ttu-id="cd372-196">Die Debugansicht ist nach dieser Änderung _exakt identisch_ mit der im vorherigen Beispiel.</span><span class="sxs-lookup"><span data-stu-id="cd372-196">The debug view after this change is _exactly the same_ as it was in the previous example.</span></span> <span data-ttu-id="cd372-197">Dies liegt daran, dass EF Core die Änderung der Verweis Navigation erkannt und die Auflistungs Navigation und den FK-Wert entsprechend korrigiert haben.</span><span class="sxs-lookup"><span data-stu-id="cd372-197">This because EF Core detected the reference navigation change and then fixed up the collection navigations and FK value to match.</span></span>

## <a name="changing-relationships-using-foreign-key-values"></a><span data-ttu-id="cd372-198">Ändern von Beziehungen mithilfe von Fremdschlüssel Werten</span><span class="sxs-lookup"><span data-stu-id="cd372-198">Changing relationships using foreign key values</span></span>

<span data-ttu-id="cd372-199">Im vorherigen Abschnitt wurden die Beziehungen durch die Navigation geändert, sodass Fremdschlüssel Werte automatisch aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="cd372-199">In the previous section, relationships were manipulated by navigations leaving foreign key values to be updated automatically.</span></span> <span data-ttu-id="cd372-200">Dies ist die empfohlene Vorgehensweise zum Bearbeiten von Beziehungen in EF Core.</span><span class="sxs-lookup"><span data-stu-id="cd372-200">This is the recommended way to manipulate relationships in EF Core.</span></span> <span data-ttu-id="cd372-201">Es ist jedoch auch möglich, die FK-Werte direkt zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="cd372-201">However, it is also possible to manipulate FK values directly.</span></span> <span data-ttu-id="cd372-202">Beispielsweise können wir einen Beitrag von einem Blog in einen anderen verschieben, indem wir den `Post.BlogId` Fremdschlüssel Wert ändern:</span><span class="sxs-lookup"><span data-stu-id="cd372-202">For example, we can move a post from one blog to another by changing the `Post.BlogId` foreign key value:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.BlogId = dotNetBlog.Id;
-->
[!code-csharp[Changing_relationships_using_foreign_key_values_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_foreign_key_values_1)]

<span data-ttu-id="cd372-203">Beachten Sie, dass dies dem Ändern der Verweis Navigation sehr ähnlich ist, wie im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="cd372-203">Notice how this is very similar to changing the reference navigation, as shown in the previous example.</span></span>

<span data-ttu-id="cd372-204">Die Debugansicht, die nach dieser Änderung wieder _identisch_ ist, entspricht der Groß-/Kleinschreibung für die beiden vorherigen Beispiele.</span><span class="sxs-lookup"><span data-stu-id="cd372-204">The debug view after this change is again _exactly the same_ as was the case for the previous two examples.</span></span> <span data-ttu-id="cd372-205">Dies liegt daran, dass EF Core erkannt hat, dass sich der FK-Wert geändert hat, und dann den Verweis und die Auflistungs Navigation entsprechend</span><span class="sxs-lookup"><span data-stu-id="cd372-205">This because EF Core detected the FK value change and then fixed up both the reference and collection navigations to match.</span></span>

> [!TIP]
> <span data-ttu-id="cd372-206">Schreiben Sie keinen Code, um alle Navigations-und FK-Werte zu bearbeiten, wenn sich eine Beziehung ändert.</span><span class="sxs-lookup"><span data-stu-id="cd372-206">Do not write code to manipulate all navigations and FK values each time a relationship changes.</span></span> <span data-ttu-id="cd372-207">Ein solcher Code ist komplizierter und muss in jedem Fall konsistente Änderungen an Fremdschlüsseln und Navigationen sicherstellen.</span><span class="sxs-lookup"><span data-stu-id="cd372-207">Such code is more complicated and must ensure consistent changes to foreign keys and navigations in every case.</span></span> <span data-ttu-id="cd372-208">Ändern Sie, wenn möglich, nur eine einzelne Navigation oder möglicherweise beide Navigationen.</span><span class="sxs-lookup"><span data-stu-id="cd372-208">If possible, just manipulate a single navigation, or maybe both navigations.</span></span> <span data-ttu-id="cd372-209">Bearbeiten Sie bei Bedarf nur die FK-Werte.</span><span class="sxs-lookup"><span data-stu-id="cd372-209">If needed, just manipulate FK values.</span></span> <span data-ttu-id="cd372-210">Vermeiden Sie die Bearbeitung von navigationswerten und FK-Werten</span><span class="sxs-lookup"><span data-stu-id="cd372-210">Avoid manipulating both navigations and FK values.</span></span>

## <a name="fixup-for-added-or-deleted-entities"></a><span data-ttu-id="cd372-211">Korrektur für hinzugefügte oder gelöschte Entitäten</span><span class="sxs-lookup"><span data-stu-id="cd372-211">Fixup for added or deleted entities</span></span>

### <a name="adding-to-a-collection-navigation"></a><span data-ttu-id="cd372-212">Hinzufügen zu einer Sammlungs Navigation</span><span class="sxs-lookup"><span data-stu-id="cd372-212">Adding to a collection navigation</span></span>

<span data-ttu-id="cd372-213">EF Core führt die folgenden Aktionen aus, wenn erkannt wird [, dass einer](xref:core/change-tracking/change-detection) Auflistungs Navigation eine neue abhängige/untergeordnete Entität hinzugefügt wurde:</span><span class="sxs-lookup"><span data-stu-id="cd372-213">EF Core performs the following actions when it [detects](xref:core/change-tracking/change-detection) that a new dependent/child entity has been added to a collection navigation:</span></span>

- <span data-ttu-id="cd372-214">Wenn die Entität nicht nachverfolgt wird, wird Sie nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="cd372-214">If the entity is not tracked, then it is tracked.</span></span> <span data-ttu-id="cd372-215">(Die Entität befindet sich normalerweise im- `Added` Zustand.</span><span class="sxs-lookup"><span data-stu-id="cd372-215">(The entity will usually be in the `Added` state.</span></span> <span data-ttu-id="cd372-216">Wenn der Entitätstyp jedoch für die Verwendung generierter Schlüssel konfiguriert ist und der Primärschlüssel Wert festgelegt ist, wird die Entität im-Zustand nachverfolgt `Unchanged` .)</span><span class="sxs-lookup"><span data-stu-id="cd372-216">However, if the entity type is configured to use generated keys and the primary key value is set, then the entity is tracked in the `Unchanged` state.)</span></span>
- <span data-ttu-id="cd372-217">Wenn die Entität einem anderen Prinzipal/übergeordneten Element zugeordnet ist, wird diese Beziehung getrennt.</span><span class="sxs-lookup"><span data-stu-id="cd372-217">If the entity is associated with a different principal/parent, then that relationship is severed.</span></span>
- <span data-ttu-id="cd372-218">Die Entität wird dem Prinzipal/übergeordneten Element zugeordnet, das die Auflistungs Navigation besitzt.</span><span class="sxs-lookup"><span data-stu-id="cd372-218">The entity becomes associated with the principal/parent that owns the collection navigation.</span></span>
- <span data-ttu-id="cd372-219">Die Navigations-und Fremdschlüssel Werte werden für alle Beteiligten Entitäten korrigiert.</span><span class="sxs-lookup"><span data-stu-id="cd372-219">Navigations and foreign key values are fixed up for all entities involved.</span></span>

<span data-ttu-id="cd372-220">Auf Grundlage dieser Informationen können wir sehen, dass zum Verschieben eines Beitrags von einem Blog zu einem anderen nicht unbedingt aus der alten Auflistungs Navigation entfernt werden muss, bevor der neue hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="cd372-220">Based on this we can see that to move a post from one blog to another we don't actually need to remove it from the old collection navigation before adding it to the new.</span></span> <span data-ttu-id="cd372-221">Daher kann der Code aus dem obigen Beispiel in geändert werden:</span><span class="sxs-lookup"><span data-stu-id="cd372-221">So the code from the example above can be changed from:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_1)]

<span data-ttu-id="cd372-222">Nach:</span><span class="sxs-lookup"><span data-stu-id="cd372-222">To:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_2)]

<span data-ttu-id="cd372-223">EF Core sehen, dass der Beitrag einem neuen Blog hinzugefügt wurde, und entfernt ihn automatisch aus der Sammlung im ersten Blog.</span><span class="sxs-lookup"><span data-stu-id="cd372-223">EF Core sees that the post has been added to a new blog and automatically removes it from the collection on the first blog.</span></span>

### <a name="removing-from-a-collection-navigation"></a><span data-ttu-id="cd372-224">Entfernen aus einer Sammlungs Navigation</span><span class="sxs-lookup"><span data-stu-id="cd372-224">Removing from a collection navigation</span></span>

<span data-ttu-id="cd372-225">Durch das Entfernen einer abhängigen/untergeordneten Entität aus der Auflistungs Navigation des Prinzipals/übergeordneten Elements wird die Beziehung zu diesem Prinzipal bzw. übergeordneten Element</span><span class="sxs-lookup"><span data-stu-id="cd372-225">Removing a dependent/child entity from the collection navigation of the principal/parent causes severing of the relationship to that principal/parent.</span></span> <span data-ttu-id="cd372-226">Was als nächstes passiert, hängt davon ab, ob die Beziehung optional oder erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="cd372-226">What happens next depends on whether the relationship is optional or required.</span></span>

#### <a name="optional-relationships"></a><span data-ttu-id="cd372-227">Optionale Beziehungen</span><span class="sxs-lookup"><span data-stu-id="cd372-227">Optional relationships</span></span>

<span data-ttu-id="cd372-228">Standardmäßig wird für optionale Beziehungen der Fremdschlüssel Wert auf NULL festgelegt.</span><span class="sxs-lookup"><span data-stu-id="cd372-228">By default for optional relationships, the foreign key value is set to null.</span></span> <span data-ttu-id="cd372-229">Dies bedeutet, dass das abhängige _/untergeordnete Element keinem Prinzipal_ /übergeordneten Element mehr zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="cd372-229">This means that the dependent/child is no longer associated with _any_ principal/parent.</span></span> <span data-ttu-id="cd372-230">Laden wir beispielsweise einen Blog und Beiträge, und entfernen Sie dann einen der Beiträge aus der `Blog.Posts` Sammlungs Navigation:</span><span class="sxs-lookup"><span data-stu-id="cd372-230">For example, let's load a blog and posts and then remove one of the posts from the `Blog.Posts` collection navigation:</span></span>

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_3)]

<span data-ttu-id="cd372-231">Wenn Sie die [Debugansicht der Änderungs Nachverfolgung](xref:core/change-tracking/debug-views) nach dieser Änderung sehen, wird Folgendes angezeigt:</span><span class="sxs-lookup"><span data-stu-id="cd372-231">Looking at the [change tracking debug view](xref:core/change-tracking/debug-views) after this change shows that:</span></span>

- <span data-ttu-id="cd372-232">Der `Post.BlogId` FK wurde auf NULL () festgelegt. `BlogId: <null> FK Modified Originally 1`</span><span class="sxs-lookup"><span data-stu-id="cd372-232">The `Post.BlogId` FK has been set to null (`BlogId: <null> FK Modified Originally 1`)</span></span>
- <span data-ttu-id="cd372-233">Die `Post.Blog` Verweis Navigation wurde auf NULL () festgelegt. `Blog: <null>`</span><span class="sxs-lookup"><span data-stu-id="cd372-233">The `Post.Blog` reference navigation has been set to null (`Blog: <null>`)</span></span>
- <span data-ttu-id="cd372-234">Der Beitrag wurde aus der `Blog.Posts` Sammlungs Navigation entfernt ( `Posts: [{Id: 1}]` ).</span><span class="sxs-lookup"><span data-stu-id="cd372-234">The post has been removed from `Blog.Posts` collection navigation (`Posts: [{Id: 1}]`)</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="cd372-235">Beachten Sie, dass der Beitrag _nicht_ als gekennzeichnet ist `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="cd372-235">Notice that the post is _not_ marked as `Deleted`.</span></span> <span data-ttu-id="cd372-236">Er ist als gekennzeichnet `Modified` , sodass der FK-Wert in der Datenbank auf NULL festgelegt wird, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="cd372-236">It is marked as `Modified` so that the FK value in the database will be set to null when SaveChanges is called.</span></span>

#### <a name="required-relationships"></a><span data-ttu-id="cd372-237">Erforderliche Beziehungen</span><span class="sxs-lookup"><span data-stu-id="cd372-237">Required relationships</span></span>

<span data-ttu-id="cd372-238">Das Festlegen des FK-Werts auf NULL ist nicht zulässig (und in der Regel nicht möglich) für erforderliche Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="cd372-238">Setting the FK value to null is not allowed (and is usually not possible) for required relationships.</span></span> <span data-ttu-id="cd372-239">Daher bedeutet das Durchlaufen einer erforderlichen Beziehung, dass die abhängige/untergeordnete Entität entweder einem neuen Prinzipal/übergeordneten Element neu übergeordnet werden muss oder aus der Datenbank entfernt werden muss, wenn SaveChanges aufgerufen wird, um eine Verletzung der referenziellen Einschränkung zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="cd372-239">Therefore, severing a required relationship means that the dependent/child entity must be either re-parented to a new principal/parent, or removed from the database when SaveChanges is called to avoid a referential constraint violation.</span></span> <span data-ttu-id="cd372-240">Dies wird als "Löschen von Waisen" bezeichnet und ist das Standardverhalten in EF Core für erforderliche Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="cd372-240">This is known as "deleting orphans", and is the default behavior in EF Core for required relationships.</span></span>

<span data-ttu-id="cd372-241">Nehmen wir beispielsweise an, dass die Beziehung zwischen Blog und Beiträgen erforderlich ist, und führen Sie dann denselben Code wie im vorherigen Beispiel aus:</span><span class="sxs-lookup"><span data-stu-id="cd372-241">For example, let's change the relationship between blog and posts to be required and then run the same code as in the previous example:</span></span>

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_4)]

<span data-ttu-id="cd372-242">Wenn Sie sich die debuggingansicht ansehen, zeigt dies Folgendes:</span><span class="sxs-lookup"><span data-stu-id="cd372-242">Looking at the debug view after this change shows that:</span></span>

- <span data-ttu-id="cd372-243">Der Beitrag wurde so gekennzeichnet `Deleted` , dass er aus der Datenbank gelöscht wird, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="cd372-243">The post has been marked as `Deleted` such that it will be deleted from the database when SaveChanges is called.</span></span>
- <span data-ttu-id="cd372-244">Die `Post.Blog` Verweis Navigation wurde auf NULL () festgelegt `Blog: <null>` .</span><span class="sxs-lookup"><span data-stu-id="cd372-244">The `Post.Blog` reference navigation has been set to null (`Blog: <null>`).</span></span>
- <span data-ttu-id="cd372-245">Der Beitrag wurde aus der `Blog.Posts` Sammlungs Navigation entfernt ( `Posts: [{Id: 1}]` ).</span><span class="sxs-lookup"><span data-stu-id="cd372-245">The post has been removed from `Blog.Posts` collection navigation (`Posts: [{Id: 1}]`).</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="cd372-246">Beachten Sie, dass das `Post.BlogId` unverändert bleibt, da es für eine erforderliche Beziehung nicht auf NULL festgelegt werden kann.</span><span class="sxs-lookup"><span data-stu-id="cd372-246">Notice that the `Post.BlogId` remains unchanged since for a required relationship it cannot be set to null.</span></span>

<span data-ttu-id="cd372-247">Das Aufrufen von SaveChanges führt dazu, dass der verwaiste Beitrag gelöscht wird:</span><span class="sxs-lookup"><span data-stu-id="cd372-247">Calling SaveChanges results in the orphaned post being deleted:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

#### <a name="delete-orphans-timing-and-re-parenting"></a><span data-ttu-id="cd372-248">Entfernen der zeitlichen Steuerung und erneuten Verarbeitung von Waisen</span><span class="sxs-lookup"><span data-stu-id="cd372-248">Delete orphans timing and re-parenting</span></span>

<span data-ttu-id="cd372-249">Standardmäßig wird das Markieren von Waisen Vorgängen durchgeführt, sobald `Deleted` die Beziehungs Änderung [erkannt](xref:core/change-tracking/change-detection)wird.</span><span class="sxs-lookup"><span data-stu-id="cd372-249">By default, marking orphans as `Deleted` happens as soon as the relationship change is [detected](xref:core/change-tracking/change-detection).</span></span> <span data-ttu-id="cd372-250">Dieser Prozess kann jedoch verzögert werden, bis SaveChanges tatsächlich aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="cd372-250">However, this process can be delayed until SaveChanges is actually called.</span></span> <span data-ttu-id="cd372-251">Dies kann hilfreich sein, um zu vermeiden, dass verwaiste von Entitäten, die von einem Prinzipal/übergeordneten Element entfernt wurden, mit einem neuen Prinzipal/übergeordneten Element versehen werden, bevor SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="cd372-251">This can be useful to avoid making orphans of entities that have been removed from one principal/parent, but will be re-parented with a new principal/parent before SaveChanges is called.</span></span> <span data-ttu-id="cd372-252"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> wird verwendet, um diese zeitliche Steuerung festzulegen.</span><span class="sxs-lookup"><span data-stu-id="cd372-252"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> is used to set this timing.</span></span> <span data-ttu-id="cd372-253">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cd372-253">For example:</span></span>

<!--
        context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_5)]

<span data-ttu-id="cd372-254">Nachdem der Beitrag aus der ersten Auflistung entfernt wurde, ist das Objekt nicht wie `Deleted` im vorherigen Beispiel dargestellt.</span><span class="sxs-lookup"><span data-stu-id="cd372-254">After removing the post from the first collection the object is not marked as `Deleted` as it was in the previous example.</span></span> <span data-ttu-id="cd372-255">Stattdessen verfolgt EF Core, dass die Beziehung getrennt wird, _auch wenn dies eine erforderliche Beziehung ist_.</span><span class="sxs-lookup"><span data-stu-id="cd372-255">Instead, EF Core is tracking that the relationship is severed _even though this is a required relationship_.</span></span> <span data-ttu-id="cd372-256">(Der FK-Wert wird von EF Core als null betrachtet, obwohl er nicht tatsächlich NULL sein kann, da der Typ keine NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="cd372-256">(The FK value is considered null by EF Core even though it cannot really be null because the type is not nullable.</span></span> <span data-ttu-id="cd372-257">Dies wird als "konzeptionelle NULL" bezeichnet.)</span><span class="sxs-lookup"><span data-stu-id="cd372-257">This is known as a "conceptual null".)</span></span>

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="cd372-258">Wenn Sie SaveChanges zu diesem Zeitpunkt aufrufen, würde dies dazu führen, dass der verwaiste Beitrag gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="cd372-258">Calling SaveChanges at this time would result in the orphaned post being deleted.</span></span> <span data-ttu-id="cd372-259">Wenn jedoch wie im obigen Beispiel ein Beitrag zu einem neuen Blog zugeordnet ist, bevor SaveChanges aufgerufen wird, wird er entsprechend dem neuen Blog korrigiert und nicht mehr als verwaiste gilt:</span><span class="sxs-lookup"><span data-stu-id="cd372-259">However, if as in the example above, post is associated with a new blog before SaveChanges is called, then it will be fixed up appropriately to that new blog and is no longer considered an orphan:</span></span>

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
```

<span data-ttu-id="cd372-260">Mit SaveChanges, die an dieser Stelle aufgerufen werden, wird der Beitrag in der Datenbank aktualisiert, anstatt ihn zu löschen.</span><span class="sxs-lookup"><span data-stu-id="cd372-260">SaveChanges called at this point will update the post in the database rather than deleting it.</span></span>

<span data-ttu-id="cd372-261">Es ist auch möglich, das automatische Löschen von Waisen zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="cd372-261">It is also possible to turn off automatic deletion of orphans.</span></span> <span data-ttu-id="cd372-262">Dies führt zu einer Ausnahme, wenn SaveChanges aufgerufen wird, während ein verwaistes nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="cd372-262">This will result in an exception if SaveChanges is called while an orphan is being tracked.</span></span> <span data-ttu-id="cd372-263">Der folgende Code zeigt z. b.:</span><span class="sxs-lookup"><span data-stu-id="cd372-263">For example, this code:</span></span>

<!--
                var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");

                context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.Never;

                var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
                dotNetBlog.Posts.Remove(post);

                context.SaveChanges(); // Throws
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_6)]

<span data-ttu-id="cd372-264">Löst diese Ausnahme aus:</span><span class="sxs-lookup"><span data-stu-id="cd372-264">Will throw this exception:</span></span>

> <span data-ttu-id="cd372-265">System. InvalidOperationException: die Zuordnung zwischen den Entitäten "Blog" und "Post" mit dem Schlüsselwert "{BlogId: 1}" wurde getrennt, aber die Beziehung wird entweder als erforderlich markiert oder ist implizit erforderlich, da der Fremdschlüssel nicht auf NULL festgelegt werden kann.</span><span class="sxs-lookup"><span data-stu-id="cd372-265">System.InvalidOperationException: The association between entities 'Blog' and 'Post' with the key value '{BlogId: 1}' has been severed, but the relationship is either marked as required or is implicitly required because the foreign key is not nullable.</span></span> <span data-ttu-id="cd372-266">Wenn die abhängige/untergeordnete Entität gelöscht werden soll, wenn eine erforderliche Beziehung getrennt wird, konfigurieren Sie die Beziehung für die Verwendung von Cascade-Lösch Vorgängen.</span><span class="sxs-lookup"><span data-stu-id="cd372-266">If the dependent/child entity should be deleted when a required relationship is severed, configure the relationship to use cascade deletes.</span></span>

<span data-ttu-id="cd372-267">Das Löschen von verwaisten und kaskadierenden Lösch Vorgängen kann jederzeit durch Aufrufen von erzwungen werden <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="cd372-267">Deletion of orphans, as well as cascade deletes, can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>.</span></span> <span data-ttu-id="cd372-268">Wenn Sie dies mit dem Festlegen der zeitlichen Steuerung für Löschvorgang auf löschen, wird sichergestellt, dass verwaiste Dateien `Never` nie gelöscht werden, es sei denn, EF Core explizit</span><span class="sxs-lookup"><span data-stu-id="cd372-268">Combining this with setting the delete orphan timing to `Never` will ensure orphans are never deleted unless EF Core is explicitly instructed to do so.</span></span>

### <a name="changing-a-reference-navigation"></a><span data-ttu-id="cd372-269">Ändern einer Referenz Navigation</span><span class="sxs-lookup"><span data-stu-id="cd372-269">Changing a reference navigation</span></span>

<span data-ttu-id="cd372-270">Das Ändern der Verweis Navigation einer 1: n-Beziehung hat denselben Effekt wie das Ändern der Sammlungs Navigation am anderen Ende der Beziehung.</span><span class="sxs-lookup"><span data-stu-id="cd372-270">Changing the reference navigation of a one-to-many relationship has the same effect as changing the collection navigation on the other end of the relationship.</span></span> <span data-ttu-id="cd372-271">Das Festlegen der Verweis Navigation von Dependent/Child auf Null entspricht dem Entfernen der Entität aus der Auflistungs Navigation des Prinzipals/übergeordneten Elements.</span><span class="sxs-lookup"><span data-stu-id="cd372-271">Setting the reference navigation of dependent/child to null is equivalent to removing the entity from the collection navigation of the principal/parent.</span></span> <span data-ttu-id="cd372-272">Alle Fixup-und Daten Bank Änderungen erfolgen wie im vorherigen Abschnitt beschrieben, einschließlich der Erstellung einer verwaisten Entität, wenn die Beziehung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="cd372-272">All fixup and database changes happen as described in the previous section, including making the entity an orphan if the relationship is required.</span></span>

#### <a name="optional-one-to-one-relationships"></a><span data-ttu-id="cd372-273">Optionale 1:1-Beziehungen</span><span class="sxs-lookup"><span data-stu-id="cd372-273">Optional one-to-one relationships</span></span>

<span data-ttu-id="cd372-274">Bei 1-zu-eins-Beziehungen bewirkt das Ändern einer Verweis Navigation, dass alle vorherigen Beziehungen getrennt werden.</span><span class="sxs-lookup"><span data-stu-id="cd372-274">For one-to-one relationships, changing a reference navigation causes any previous relationship to be severed.</span></span> <span data-ttu-id="cd372-275">Bei optionalen Beziehungen bedeutet dies, dass der FK-Wert für das zuvor Verwandte abhängige/untergeordnete Element auf NULL festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="cd372-275">For optional relationships, this means that the FK value on the previously related dependent/child is set to null.</span></span> <span data-ttu-id="cd372-276">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cd372-276">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Assets).Single(e => e.Name == ".NET Blog");
        dotNetBlog.Assets = new BlogAssets();

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_7)]

<span data-ttu-id="cd372-277">Die Debugansicht vor dem Aufruf von SaveChanges zeigt, dass die neuen Assets die vorhandenen Assets ersetzt haben, die nun `Modified` mit einem NULL-FK-Wert gekennzeichnet sind `BlogAssets.BlogId` :</span><span class="sxs-lookup"><span data-stu-id="cd372-277">The debug view before calling SaveChanges shows that the new assets has replaced the existing assets, which is now marked as `Modified` with a null `BlogAssets.BlogId` FK value:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482629}
  Posts: []
BlogAssets {Id: -2147482629} Added
  Id: -2147482629 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Modified
  Id: 1 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 1
  Blog: <null>
```

<span data-ttu-id="cd372-278">Dies führt zu einem Update und einer Einfügung, wenn SaveChanges aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="cd372-278">This results in an update and an insert when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Assets" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2=NULL, @p3='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p2, @p3);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

#### <a name="required-one-to-one-relationships"></a><span data-ttu-id="cd372-279">Erforderliche 1-zu-eins-Beziehung</span><span class="sxs-lookup"><span data-stu-id="cd372-279">Required one-to-one relationships</span></span>

<span data-ttu-id="cd372-280">Wenn Sie denselben Code wie im vorherigen Beispiel ausführen, aber dieses Mal mit einer erforderlichen eins-zu-eins-Beziehung, zeigt, dass der zuvor zugeordnete `BlogAssets` nun als gekennzeichnet ist `Deleted` , da er zu einem verwaisten wird, wenn der neue `BlogAssets` seinen Platz annimmt:</span><span class="sxs-lookup"><span data-stu-id="cd372-280">Running the same code as in the previous example, but this time with a required one-to-one relationship, shows that the previously associated `BlogAssets` is now marked as `Deleted`, since it becomes an orphan when the new `BlogAssets` takes its place:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482639}
  Posts: []
BlogAssets {Id: -2147482639} Added
  Id: -2147482639 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Deleted
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: <null>
```

<span data-ttu-id="cd372-281">Dies führt dann zum Löschen eines und zum Einfügen, wenn SaveChanges aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="cd372-281">This then results in an delete an and insert when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Assets"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1=NULL, @p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p1, @p2);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="cd372-282">Die zeitliche Steuerung der Markierung von Waisen als gelöscht kann auf die gleiche Weise wie für Sammlungs Navigation geändert werden und hat die gleichen Auswirkungen.</span><span class="sxs-lookup"><span data-stu-id="cd372-282">The timing of marking orphans as deleted can be changed in the same way as shown for collection navigations and has the same effects.</span></span>

### <a name="deleting-an-entity"></a><span data-ttu-id="cd372-283">Löschen einer Entität</span><span class="sxs-lookup"><span data-stu-id="cd372-283">Deleting an entity</span></span>

#### <a name="optional-relationships"></a><span data-ttu-id="cd372-284">Optionale Beziehungen</span><span class="sxs-lookup"><span data-stu-id="cd372-284">Optional relationships</span></span>

<span data-ttu-id="cd372-285">Wenn eine Entität als gekennzeichnet ist `Deleted` , z. b. durch Aufrufen von <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> , werden Verweise auf die gelöschte Entität aus der Navigation anderer Entitäten entfernt.</span><span class="sxs-lookup"><span data-stu-id="cd372-285">When an entity is marked as `Deleted`, for example by calling <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>, then references to the deleted entity are removed from the navigations of other entities.</span></span> <span data-ttu-id="cd372-286">Für optionale Beziehungen werden die FK-Werte in abhängigen Entitäten auf NULL festgelegt.</span><span class="sxs-lookup"><span data-stu-id="cd372-286">For optional relationships, the FK values in dependent entities are set to null.</span></span>

<span data-ttu-id="cd372-287">Als Beispiel markieren wir den Visual Studio-Blog wie folgt `Deleted` :</span><span class="sxs-lookup"><span data-stu-id="cd372-287">For example, let's mark the Visual Studio blog as `Deleted`:</span></span>

<!--
        using var context = new BlogsContext();

        var vsBlog = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .Single(e => e.Name == "Visual Studio Blog");

        context.Remove(vsBlog);

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Deleting_an_entity_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Deleting_an_entity_1)]

<span data-ttu-id="cd372-288">Wenn Sie die [Debugansicht der Änderungs](xref:core/change-tracking/debug-views) Nachverfolgung anzeigen, bevor Sie SaveChanges aufrufen</span><span class="sxs-lookup"><span data-stu-id="cd372-288">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows:</span></span>

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Modified
  Id: 2 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 2
  Blog: <null>
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
Post {Id: 4} Modified
  Id: 4 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="cd372-289">Beachten Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="cd372-289">Notice that:</span></span>

- <span data-ttu-id="cd372-290">Der Blog ist als markiert `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="cd372-290">The blog is marked as `Deleted`.</span></span>
- <span data-ttu-id="cd372-291">Die Ressourcen im Zusammenhang mit dem gelöschten Blog haben einen NULL-Wert für den FK ( `BlogId: <null> FK Modified Originally 2` ) und eine NULL-Verweis Navigation ( `Blog: <null>` ).</span><span class="sxs-lookup"><span data-stu-id="cd372-291">The assets related to the deleted blog has a null FK value (`BlogId: <null> FK Modified Originally 2`) and a null reference navigation (`Blog: <null>`)</span></span>
- <span data-ttu-id="cd372-292">Jeder Beitrag im Zusammenhang mit dem gelöschten Blog weist einen NULL `BlogId: <null> FK Modified Originally 2` -Wert () und eine NULL-Verweis Navigation () auf. `Blog: <null>`</span><span class="sxs-lookup"><span data-stu-id="cd372-292">Each post related to the deleted blog has a null FK value (`BlogId: <null> FK Modified Originally 2`) and a null reference navigation (`Blog: <null>`)</span></span>

#### <a name="required-relationships"></a><span data-ttu-id="cd372-293">Erforderliche Beziehungen</span><span class="sxs-lookup"><span data-stu-id="cd372-293">Required relationships</span></span>

<span data-ttu-id="cd372-294">Das fixupverhalten für erforderliche Beziehungen ist das gleiche wie bei optionalen Beziehungen, mit dem Unterschied, dass die abhängigen/untergeordneten Entitäten als gekennzeichnet sind, `Deleted` da Sie nicht ohne einen Prinzipal/Parent vorhanden sein können und aus der Datenbank entfernt werden müssen, wenn SaveChanges aufgerufen wird, um eine referenzielle Einschränkungs Ausnahme zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="cd372-294">The fixup behavior for required relationships is the same as for optional relationships except that the dependent/child entities are marked as `Deleted` since they cannot exist without a principal/parent and must be removed from the database when SaveChanges is called to avoid a referential constraint exception.</span></span> <span data-ttu-id="cd372-295">Dies wird als "Cascade Delete" bezeichnet und ist das Standardverhalten in EF Core für erforderliche Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="cd372-295">This is known as "cascade delete", and is the default behavior in EF Core for required relationships.</span></span> <span data-ttu-id="cd372-296">Beispielsweise führt die Ausführung desselben Codes, wie im vorherigen Beispiel, aber mit einer erforderlichen Beziehung, zu der folgenden Debugansicht, bevor SaveChanges aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="cd372-296">For example, running the same code as in the previous example but with a required relationship results in the following debug view before SaveChanges is called:</span></span>

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Deleted
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 3} Deleted
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="cd372-297">Erwartungsgemäß werden die abhängigen Elemente/untergeordneten Elemente nun als markiert `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="cd372-297">As expected, the dependents/children are now marked as `Deleted`.</span></span> <span data-ttu-id="cd372-298">Beachten Sie jedoch, dass sich die Navigation in den gelöschten Entitäten _nicht_ geändert hat.</span><span class="sxs-lookup"><span data-stu-id="cd372-298">However, notice that the navigations on the deleted entities have _not_ changed.</span></span> <span data-ttu-id="cd372-299">Dies mag seltsam erscheinen, vermeidet jedoch das vollständige Aufteilen eines gelöschten Diagramms von Entitäten, indem alle Navigationen gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="cd372-299">This may seem strange, but it avoids completely shredding a deleted graph of entities by clearing all navigations.</span></span> <span data-ttu-id="cd372-300">Das heißt, dass der Blog, das Asset und die Beiträge auch nach dem Löschen eines Diagramms von Entitäten vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="cd372-300">That is, the blog, asset, and posts still form a graph of entities even after having been deleted.</span></span> <span data-ttu-id="cd372-301">Dies erleichtert das Aufheben der Löschung eines Diagramms von Entitäten, als dies in EF6 der Fall war, in dem das Diagramm zerrochen wurde.</span><span class="sxs-lookup"><span data-stu-id="cd372-301">This makes it much easier to un-delete a graph of entities than was the case in EF6 where the graph was shredded.</span></span>

#### <a name="cascade-delete-timing-and-re-parenting"></a><span data-ttu-id="cd372-302">Löschen der zeitlichen Steuerung und erneute Verarbeitung</span><span class="sxs-lookup"><span data-stu-id="cd372-302">Cascade delete timing and re-parenting</span></span>

<span data-ttu-id="cd372-303">Standardmäßig erfolgt das Löschen von Lösch Vorgängen, sobald der übergeordnete/Prinzipal als gekennzeichnet ist `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="cd372-303">By default, cascade delete happens as soon as the parent/principal is marked as `Deleted`.</span></span> <span data-ttu-id="cd372-304">Dies ist das gleiche wie für das Löschen von Waisen, wie zuvor beschrieben.</span><span class="sxs-lookup"><span data-stu-id="cd372-304">This is the same as for deleting orphans, as described previously.</span></span> <span data-ttu-id="cd372-305">Wie beim Löschen von verwaisten, kann dieser Prozess verzögert werden, bis SaveChanges aufgerufen oder sogar vollständig deaktiviert wird, indem entsprechend festgelegt wird <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="cd372-305">As with deleting orphans, this process can be delayed until SaveChanges is called, or even disabled entirely, by setting <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> appropriately.</span></span> <span data-ttu-id="cd372-306">Dies ist auf die gleiche Weise wie beim Löschen von Waisen, einschließlich der erneuten untergeordneten Elemente/abhängigen Elemente nach dem Löschen eines Prinzipals/übergeordneten Elements.</span><span class="sxs-lookup"><span data-stu-id="cd372-306">This is useful in the same way as it is for deleting orphans, including for re-parenting children/dependents after deletion of a principal/parent.</span></span>

<span data-ttu-id="cd372-307">Das Löschen von Lösch Vorgängen sowie das Löschen von Waisen können jederzeit erzwungen werden, indem aufgerufen wird <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="cd372-307">Cascade deletes, as well as deleting orphans, can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>.</span></span> <span data-ttu-id="cd372-308">Durch die Kombination dieser mit dem Festlegen der Zeitüberschreitungen für Löschvorgänge auf `Never` wird sichergestellt, dass Löschvorgänge nie durchgeführt werden, es sei denn, EF Core</span><span class="sxs-lookup"><span data-stu-id="cd372-308">Combining this with setting the cascade delete timing to `Never` will ensure cascade deletes never happen unless EF Core is explicitly instructed to do so.</span></span>

> [!TIP]
> <span data-ttu-id="cd372-309">Kaskadierende Lösch-und Lösch verwaisten sind eng verwandt.</span><span class="sxs-lookup"><span data-stu-id="cd372-309">Cascade delete and deleting orphans are closely related.</span></span> <span data-ttu-id="cd372-310">Beide führen dazu, dass abhängige/untergeordnete Entitäten gelöscht werden, wenn die Beziehung zum erforderlichen Prinzipal/übergeordneten Element getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="cd372-310">Both result in deleting dependent/child entities when the relationship to their required principal/parent is severed.</span></span> <span data-ttu-id="cd372-311">Zum Löschen von Lösch Vorgängen tritt dieser Vorgang auf, weil der Prinzipal/das übergeordnete Element selbst gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="cd372-311">For cascade delete, this severing happens because the principal/parent is itself deleted.</span></span> <span data-ttu-id="cd372-312">Für verwaiste Elemente ist die Prinzipal/übergeordnete Entität weiterhin vorhanden, Sie ist jedoch nicht mehr mit den abhängigen/untergeordneten Entitäten verknüpft.</span><span class="sxs-lookup"><span data-stu-id="cd372-312">For orphans, the principal/parent entity still exists, but is no longer related to the dependent/child entities.</span></span>

## <a name="many-to-many-relationships"></a><span data-ttu-id="cd372-313">M:n-Beziehungen</span><span class="sxs-lookup"><span data-stu-id="cd372-313">Many-to-many relationships</span></span>

<span data-ttu-id="cd372-314">M:n-Beziehungen in EF Core werden mithilfe einer JOIN-Entität implementiert.</span><span class="sxs-lookup"><span data-stu-id="cd372-314">Many-to-many relationships in EF Core are implemented using a join entity.</span></span> <span data-ttu-id="cd372-315">Jede Seite: die m:n-Beziehung bezieht sich auf diese joinentität mit einer 1: n-Beziehung.</span><span class="sxs-lookup"><span data-stu-id="cd372-315">Each side the many-to-many relationship is related to this join entity with a one-to-many relationship.</span></span> <span data-ttu-id="cd372-316">Vor EF Core 5,0 musste diese Verknüpfungs Entität explizit definiert und zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="cd372-316">Before EF Core 5.0, this join entity had to explicitly defined and mapped.</span></span> <span data-ttu-id="cd372-317">Ab EF Core 5,0 können Sie implizit und ausgeblendet erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="cd372-317">Starting with EF Core 5.0, it can be created implicitly and hidden.</span></span> <span data-ttu-id="cd372-318">In beiden Fällen ist das zugrunde liegende Verhalten jedoch identisch.</span><span class="sxs-lookup"><span data-stu-id="cd372-318">However, in both cases the underlying behavior is the same.</span></span> <span data-ttu-id="cd372-319">Wir betrachten dieses zugrunde liegende Verhalten zunächst, um zu verstehen, wie die Nachverfolgung von m:n-Beziehungen funktioniert.</span><span class="sxs-lookup"><span data-stu-id="cd372-319">We will look at this underlying behavior first to understand how tracking of many-to-many relationships works.</span></span>

### <a name="how-many-to-many-relationships-work"></a><span data-ttu-id="cd372-320">Funktionsweise von m:n-Beziehungen</span><span class="sxs-lookup"><span data-stu-id="cd372-320">How many-to-many relationships work</span></span>

<span data-ttu-id="cd372-321">Beachten Sie dieses EF Core Modell, das eine m:n-Beziehung zwischen Beiträgen und Tags mithilfe eines explizit definierten joinentitäts Typs erstellt:</span><span class="sxs-lookup"><span data-stu-id="cd372-321">Consider this EF Core model that creates a many-to-many relationship between posts and tags using an explicitly defined join entity type:</span></span>

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
    -->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Model)]

<span data-ttu-id="cd372-322">Beachten Sie, dass der `PostTag` Join-Entitätstyp zwei Fremdschlüssel Eigenschaften enthält.</span><span class="sxs-lookup"><span data-stu-id="cd372-322">Notice that the `PostTag` join entity type contains two foreign key properties.</span></span> <span data-ttu-id="cd372-323">In diesem Modell muss eine posttag-joinentität vorhanden sein, damit ein Post mit einem Tag verknüpft werden kann, wobei der `PostTag.PostId` Fremdschlüssel Wert mit dem Wert des `Post.Id` Primärschlüssels übereinstimmt und der `PostTag.TagId` Fremdschlüssel Wert mit dem Primärschlüssel Wert übereinstimmt `Tag.Id` .</span><span class="sxs-lookup"><span data-stu-id="cd372-323">In this model, for a post to be related to a tag, there must be a PostTag join entity where the `PostTag.PostId` foreign key value matches the `Post.Id` primary key value, and where the `PostTag.TagId` foreign key value matches the `Tag.Id` primary key value.</span></span> <span data-ttu-id="cd372-324">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cd372-324">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_1)]

<span data-ttu-id="cd372-325">Wenn Sie die [Debugansicht Änderungs](xref:core/change-tracking/debug-views) Nachverfolgung nach dem Ausführen dieses Codes anzeigen, zeigt dies, dass der Beitrag und das Tag mit der neuen `PostTag` joinentität verknüpft sind:</span><span class="sxs-lookup"><span data-stu-id="cd372-325">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after running this code shows that the post and tag are related by the new `PostTag` join entity:</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
```

<span data-ttu-id="cd372-326">Beachten Sie, dass die Auflistungs Navigation für `Post` und `Tag` korrigiert wurden, wie Sie die Referenz Navigation auf aufweisen `PostTag` .</span><span class="sxs-lookup"><span data-stu-id="cd372-326">Notice that the collection navigations on `Post` and `Tag` have been fixed up, as have the reference navigations on `PostTag`.</span></span> <span data-ttu-id="cd372-327">Diese Beziehungen können von Navigationen anstelle von FK-Werten, wie in allen vorherigen Beispielen, manipuliert werden.</span><span class="sxs-lookup"><span data-stu-id="cd372-327">These relationships can be manipulated by navigations instead of FK values, just as in all the preceding examples.</span></span> <span data-ttu-id="cd372-328">Der obige Code kann z. b. so geändert werden, dass die Beziehung durch Festlegen der Verweis Navigation für die joinentität hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="cd372-328">For example, the code above can be modified to add the relationship by setting the reference navigations on the join entity:</span></span>

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_2)]

<span data-ttu-id="cd372-329">Dies führt zu genau denselben Änderungen an Fert und Navigationen wie im vorherigen Beispiel.</span><span class="sxs-lookup"><span data-stu-id="cd372-329">This results in exactly the same change to FKs and navigations as in the previous example.</span></span>

### <a name="skip-navigations"></a><span data-ttu-id="cd372-330">Navigation überspringen</span><span class="sxs-lookup"><span data-stu-id="cd372-330">Skip navigations</span></span>

> [!NOTE]
> <span data-ttu-id="cd372-331">Die Navigation überspringen wurde in EF Core 5,0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="cd372-331">Skip navigations were introduced in EF Core 5.0.</span></span>

<span data-ttu-id="cd372-332">Eine manuelle Bearbeitung der jointabelle kann mühsam sein.</span><span class="sxs-lookup"><span data-stu-id="cd372-332">Manipulating the join table manually can be cumbersome.</span></span> <span data-ttu-id="cd372-333">Beginnend mit EF Core 5,0 können m:n-Beziehungen direkt mithilfe spezieller Sammlungs Navigation, die die joinentität überspringen, manipuliert werden.</span><span class="sxs-lookup"><span data-stu-id="cd372-333">Starting with EF Core 5.0, many-to-many relationships can be manipulated directly using special collection navigations that "skip over" the join entity.</span></span> <span data-ttu-id="cd372-334">Beispielsweise können zwei Skip-Navigationen zum obigen Modell hinzugefügt werden. eine von Post zu Tags und die andere von Tag zu Beiträgen:</span><span class="sxs-lookup"><span data-stu-id="cd372-334">For example, two skip navigations can be added to the model above; one from Post to Tags, and the other from Tag to Posts:</span></span>

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Model)]

<span data-ttu-id="cd372-335">Für diese m:n-Beziehung ist die folgende Konfiguration erforderlich, um sicherzustellen, dass die Skip-Navigation und die normale Navigation für dieselbe m:n-Beziehung verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="cd372-335">This many-to-many relationship requires the following configuration to ensure the skip navigations and normal navigations are all used for the same many-to-many relationship:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne(t => t.Tag).WithMany(p => p.PostTags),
                    j => j.HasOne(t => t.Post).WithMany(p => p.PostTags));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=OnModelCreating)]

<span data-ttu-id="cd372-336">Weitere Informationen zum Mapping von m:n-Beziehungen finden Sie unter [Beziehungen](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="cd372-336">See [Relationships](xref:core/modeling/relationships) for more information on mapping many-to-many relationships.</span></span>

<span data-ttu-id="cd372-337">Navigation überspringen und Verhalten sich wie normale Sammlungs Navigation.</span><span class="sxs-lookup"><span data-stu-id="cd372-337">Skip navigations look and behave like normal collection navigations.</span></span> <span data-ttu-id="cd372-338">Die Art und Weise, wie Sie mit Fremdschlüssel Werten arbeiten, unterscheidet sich jedoch.</span><span class="sxs-lookup"><span data-stu-id="cd372-338">However, the way they work with foreign key values is different.</span></span> <span data-ttu-id="cd372-339">Wir ordnen einem Tag einen Beitrag zu, aber dieses Mal wird eine Skip-Navigation verwendet:</span><span class="sxs-lookup"><span data-stu-id="cd372-339">Let's associate a post with a tag, but this time using a skip navigation:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_3)]

<span data-ttu-id="cd372-340">Beachten Sie, dass dieser Code die joinentität nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="cd372-340">Notice that this code doesn't use the join entity.</span></span> <span data-ttu-id="cd372-341">Stattdessen wird eine Entität nur auf dieselbe Weise wie bei einer 1: n-Beziehung zu einer Navigations Auflistung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="cd372-341">It instead just adds an entity to a navigation collection in the same way as would be done if this were a one-to-many relationship.</span></span> <span data-ttu-id="cd372-342">Die resultierende Debugansicht ist im Wesentlichen identisch mit der folgenden:</span><span class="sxs-lookup"><span data-stu-id="cd372-342">The resulting debug view is essentially the same as before:</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
  Posts: [{Id: 3}]
```

<span data-ttu-id="cd372-343">Beachten Sie, dass eine Instanz der `PostTag` joinentität automatisch erstellt wurde, wobei die FK-Werte auf die PK-Werte des Tags und des Beitrags festgelegt sind, die jetzt zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="cd372-343">Notice that an instance of the `PostTag` join entity was created automatically with FK values set to the PK values of the tag and post that are now associated.</span></span> <span data-ttu-id="cd372-344">Alle normalen Verweise und Auflistungs Navigation wurden so korrigiert, dass Sie diesen FK-Werten entsprechen.</span><span class="sxs-lookup"><span data-stu-id="cd372-344">All the normal reference and collection navigations have been fixed up to match these FK values.</span></span> <span data-ttu-id="cd372-345">Da dieses Modell außerdem Skip-Navigationen enthält, wurden diese ebenfalls korrigiert.</span><span class="sxs-lookup"><span data-stu-id="cd372-345">Also, since this model contains skip navigations, these have also been fixed up.</span></span> <span data-ttu-id="cd372-346">Insbesondere, obwohl wir das Tag zur `Post.Tags` Navigation überspringen hinzugefügt haben, wurde die `Tag.Posts` umgekehrte Skip-Navigation auf der anderen Seite dieser Beziehung ebenfalls so eingerichtet, dass Sie den zugehörigen Beitrag enthält.</span><span class="sxs-lookup"><span data-stu-id="cd372-346">Specifically, even though we added the tag to the `Post.Tags` skip navigation, the `Tag.Posts` inverse skip navigation on the other side of this relationship has also been fixed up to contain the associated post.</span></span>

<span data-ttu-id="cd372-347">Beachten Sie, dass die zugrunde liegenden m:n-Beziehungen weiterhin direkt bearbeitet werden können, auch wenn die über sprunnavigationen oben angeordnet wurden.</span><span class="sxs-lookup"><span data-stu-id="cd372-347">It is worth noting that the underlying many-to-many relationships can still be manipulated directly even when skip navigations have been layered on top.</span></span> <span data-ttu-id="cd372-348">Beispielsweise könnten Tag und Post wie folgt verknüpft werden, bevor Skip-Navigationen eingeführt werden:</span><span class="sxs-lookup"><span data-stu-id="cd372-348">For example, the tag and Post could be associated as we did before introducing skip navigations:</span></span>

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_4)]

<span data-ttu-id="cd372-349">Oder mithilfe von FK-Werten:</span><span class="sxs-lookup"><span data-stu-id="cd372-349">Or using FK values:</span></span>

<!--
            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });
-->
[!code-csharp[Many_to_many_relationships_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_5)]

<span data-ttu-id="cd372-350">Dies führt dennoch dazu, dass die Skip-Navigation ordnungsgemäß korrigiert wird, was zur Ausgabe der Ausgabe der Debugansicht führt wie im vorherigen Beispiel.</span><span class="sxs-lookup"><span data-stu-id="cd372-350">This will still result in the skip navigations being fixed up correctly, resulting in the same debug view output as in the previous example.</span></span>

### <a name="skip-navigations-only"></a><span data-ttu-id="cd372-351">Nur Navigation überspringen</span><span class="sxs-lookup"><span data-stu-id="cd372-351">Skip navigations only</span></span>

<span data-ttu-id="cd372-352">Im vorherigen Abschnitt haben wir _zusätzlich zum_ vollständigen definieren der beiden zugrunde liegenden 1: n-Beziehungen die Skip-Navigation hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="cd372-352">In the previous section we added skip navigations _in addition to_ fully defining the two underlying one-to-many relationships.</span></span> <span data-ttu-id="cd372-353">Dies ist hilfreich, um zu veranschaulichen, was mit den FK-Werten passiert, aber häufig unnötig ist.</span><span class="sxs-lookup"><span data-stu-id="cd372-353">This is useful to illustrate what happens to FK values, but is often unnecessary.</span></span> <span data-ttu-id="cd372-354">Stattdessen kann die m:n-Beziehung nur mithilfe von Skip- _Navigationen_ definiert werden.</span><span class="sxs-lookup"><span data-stu-id="cd372-354">Instead, the many-to-many relationship can be defined using _only skip navigations_.</span></span> <span data-ttu-id="cd372-355">Auf diese Weise wird die m:n-Beziehung im Modell am Anfang dieses Dokuments definiert.</span><span class="sxs-lookup"><span data-stu-id="cd372-355">This is how the many-to-many relationship is defined in the model at the very top of this document.</span></span> <span data-ttu-id="cd372-356">Mit diesem Modell können wir erneut einen Beitrag und ein Tag zuordnen, indem wir der Navigations Navigation einen Beitrag hinzufügen `Tag.Posts` (oder alternativ ein Tag zur Navigation überspringen hinzufügen `Post.Tags` ):</span><span class="sxs-lookup"><span data-stu-id="cd372-356">Using this model, we can again associate a Post and a Tag by adding a post to the `Tag.Posts` skip navigation (or, alternately, adding a tag to the `Post.Tags` skip navigation):</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Many_to_many_relationships_6)]

<span data-ttu-id="cd372-357">Wenn Sie die Debugansicht nach dieser Änderung betrachten, wird angezeigt, dass EF Core eine Instanz von erstellt hat `Dictionary<string, object>` , die die joinentität darstellt.</span><span class="sxs-lookup"><span data-stu-id="cd372-357">Looking at the debug view after making this change reveals that EF Core has created an instance of `Dictionary<string, object>` to represent the join entity.</span></span> <span data-ttu-id="cd372-358">Diese joinentität enthält sowohl `PostsId` die-als auch die- `TagsId` Fremdschlüssel Eigenschaften, die so festgelegt wurden, dass Sie mit den PK-Werten des zugewiesenen Beitrags und des zugehörigen Tags</span><span class="sxs-lookup"><span data-stu-id="cd372-358">This join entity contains both `PostsId` and `TagsId` foreign key properties which have been set to match the PK values of the post and tag that are associated.</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 1} Added
  PostsId: 3 PK FK
  TagsId: 1 PK FK
```

<span data-ttu-id="cd372-359">Weitere Informationen zu impliziten joinentitäten und zur Verwendung von Entitäts Typen finden Sie unter [Beziehungen](xref:core/modeling/relationships) `Dictionary<string, object>` .</span><span class="sxs-lookup"><span data-stu-id="cd372-359">See [Relationships](xref:core/modeling/relationships) for more information about implicit join entities and the use of `Dictionary<string, object>` entity types.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cd372-360">Der CLR-Typ, der für joinentitäts Typen gemäß der Konvention verwendet wird, kann sich in zukünftigen Versionen ändern, um die Leistung</span><span class="sxs-lookup"><span data-stu-id="cd372-360">The CLR type used for join entity types by convention may change in future releases to improve performance.</span></span> <span data-ttu-id="cd372-361">Verlassen Sie sich nicht auf den Jointyp, `Dictionary<string, object>` Wenn dies nicht explizit konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="cd372-361">Do not depend on the join type being `Dictionary<string, object>` unless this has been explicitly configured.</span></span>

### <a name="join-entities-with-payloads"></a><span data-ttu-id="cd372-362">Verknüpfen von Entitäten mit Nutzlasten</span><span class="sxs-lookup"><span data-stu-id="cd372-362">Join entities with payloads</span></span>

<span data-ttu-id="cd372-363">Bisher haben alle Beispiele einen Join-Entitätstyp (ob explizit oder implizit) verwendet, der nur die beiden Fremdschlüssel Eigenschaften enthält, die für die m:n-Beziehung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="cd372-363">So far all the examples have used a join entity type (whether explicit or implicit) that contains only the two foreign key properties needed for the many-to-many relationship.</span></span> <span data-ttu-id="cd372-364">Keine dieser FK-Werte muss explizit von der Anwendung festgelegt werden, wenn Beziehungen bearbeitet werden, da ihre Werte aus den Primärschlüssel Eigenschaften der verknüpften Entitäten stammen.</span><span class="sxs-lookup"><span data-stu-id="cd372-364">Neither of these FK values need to be explicitly set by the application when manipulating relationships because their values come from the primary key properties of the related entities.</span></span> <span data-ttu-id="cd372-365">Dadurch können EF Core Instanzen der joinentität erstellen, ohne dass Daten fehlen.</span><span class="sxs-lookup"><span data-stu-id="cd372-365">This allows EF Core to create instances of the join entity without missing data.</span></span>

#### <a name="payloads-with-generated-values"></a><span data-ttu-id="cd372-366">Nutzlasten mit generierten Werten</span><span class="sxs-lookup"><span data-stu-id="cd372-366">Payloads with generated values</span></span>

<span data-ttu-id="cd372-367">EF Core unterstützt das Hinzufügen zusätzlicher Eigenschaften zum joinentitätstyp.</span><span class="sxs-lookup"><span data-stu-id="cd372-367">EF Core supports adding additional properties to the join entity type.</span></span> <span data-ttu-id="cd372-368">Dies wird als "Nutzlast" der joinentität bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="cd372-368">This is known as giving the join entity a "payload".</span></span> <span data-ttu-id="cd372-369">Fügen Sie z. b. `TaggedOn` der joinentität eine-Eigenschaft hinzu `PostTag` :</span><span class="sxs-lookup"><span data-stu-id="cd372-369">For example, let's add `TaggedOn` property to the `PostTag` join entity:</span></span>

<!--
    public class PostTag
    {
        public int PostId { get; set; } // Foreign key to Post
        public int TagId { get; set; } // Foreign key to Tag

        public DateTime TaggedOn { get; set; } // Payload
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Model)]

<span data-ttu-id="cd372-370">Diese Nutz Last Eigenschaft wird nicht festgelegt, wenn EF Core eine Join-Entitäts Instanz erstellt.</span><span class="sxs-lookup"><span data-stu-id="cd372-370">This payload property will not be set when EF Core creates a join entity instance.</span></span> <span data-ttu-id="cd372-371">Die gängigste Methode, damit umzugehen, ist die Verwendung von Nutz Last Eigenschaften mit automatisch generierten Werten.</span><span class="sxs-lookup"><span data-stu-id="cd372-371">The most common way to deal with this is to use payload properties with automatically generated values.</span></span> <span data-ttu-id="cd372-372">Die-Eigenschaft kann z. b. `TaggedOn` so konfiguriert werden, dass ein vom Speicher generierter Zeitstempel verwendet wird, wenn jede neue Entität eingefügt wird:</span><span class="sxs-lookup"><span data-stu-id="cd372-372">For example, the `TaggedOn` property can be configured to use a store-generated timestamp when each new entity is inserted:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany(),
                    j => j.Property(e => e.TaggedOn).HasDefaultValueSql("CURRENT_TIMESTAMP"));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=OnModelCreating)]

<span data-ttu-id="cd372-373">Ein Beitrag kann jetzt auf die gleiche Weise wie zuvor markiert werden:</span><span class="sxs-lookup"><span data-stu-id="cd372-373">A post can now be tagged in the same way as before:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Many_to_many_relationships_7)]

<span data-ttu-id="cd372-374">Wenn Sie die [Debugansicht Änderungs](xref:core/change-tracking/debug-views) Nachverfolgung nach dem Aufruf von SaveChanges anzeigen, wird angezeigt, dass die Payload-Eigenschaft entsprechend festgelegt wurde</span><span class="sxs-lookup"><span data-stu-id="cd372-374">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after calling SaveChanges shows that the payload property has been set appropriately:</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Unchanged
  PostId: 3 PK FK
  TagId: 1 PK FK
  TaggedOn: '12/29/2020 8:13:21 PM'
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
```

#### <a name="explicitly-setting-payload-values"></a><span data-ttu-id="cd372-375">Explizit festlegen von Nutz Last Werten</span><span class="sxs-lookup"><span data-stu-id="cd372-375">Explicitly setting payload values</span></span>

<span data-ttu-id="cd372-376">Im Anschluss an das vorherige Beispiel fügen wir eine Nutz Last Eigenschaft hinzu, die keinen automatisch generierten Wert verwendet:</span><span class="sxs-lookup"><span data-stu-id="cd372-376">Following on from the previous example, let's add a payload property that does not use an automatically generated value:</span></span>

<!--
    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public DateTime TaggedOn { get; set; } // Auto-generated payload property
        public string TaggedBy { get; set; } // Not-generated payload property
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Model)]

<span data-ttu-id="cd372-377">Ein Beitrag kann jetzt auf die gleiche Weise wie zuvor gekennzeichnet werden, und die joinentität wird immer noch automatisch erstellt.</span><span class="sxs-lookup"><span data-stu-id="cd372-377">A post can now be tagged in the same way as before, and the join entity will still be created automatically.</span></span> <span data-ttu-id="cd372-378">Auf diese Entität kann dann mithilfe eines der unter Zugreifen auf über [wachte Entitäten](xref:core/change-tracking/entity-entries)beschriebenen Mechanismen zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="cd372-378">This entity can then be accessed using one of the mechanisms described in [Accessing Tracked Entities](xref:core/change-tracking/entity-entries).</span></span> <span data-ttu-id="cd372-379">Der folgende Code verwendet beispielsweise <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> , um auf die Join-Entitäts Instanz zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="cd372-379">For example, the code below uses <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> to access the join entity instance:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();

            var joinEntity = context.Set<PostTag>().Find(post.Id, tag.Id);

            joinEntity.TaggedBy = "ajcvickers";

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_8](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_8)]

<span data-ttu-id="cd372-380">Nachdem die verknüpfte Entität gefunden wurde, kann Sie auf die normale Weise manipuliert werden, in diesem Beispiel, um die `TaggedBy` Nutz Last Eigenschaft vor dem Aufrufen von SaveChanges festzulegen.</span><span class="sxs-lookup"><span data-stu-id="cd372-380">Once the join entity has been located it can be manipulated in the normal way--in this example, to set the `TaggedBy` payload property before calling SaveChanges.</span></span>

> [!NOTE]
> <span data-ttu-id="cd372-381">Beachten Sie, dass hier ein-Befehl <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> erforderlich ist, um EF Core die Möglichkeit zu haben, die Änderung der Navigations Eigenschaft zu erkennen und die Join-Entitäts Instanz zu erstellen, bevor `Find` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cd372-381">Note that a call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is required here to give EF Core a chance to detect the navigation property change and create the join entity instance before `Find` is used.</span></span> <span data-ttu-id="cd372-382">Weitere Informationen finden Sie unter [Änderungs Erkennung und Benachrichtigungen](xref:core/change-tracking/change-detection) .</span><span class="sxs-lookup"><span data-stu-id="cd372-382">See [Change Detection and Notifications](xref:core/change-tracking/change-detection) for more information.</span></span>

<span data-ttu-id="cd372-383">Alternativ kann die joinentität explizit erstellt werden, um einem Tag einen Beitrag zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="cd372-383">Alternately, the join entity can be created explicitly to associate a post with a tag.</span></span> <span data-ttu-id="cd372-384">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cd372-384">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag()
            {
                PostId = post.Id,
                TagId = tag.Id,
                TaggedBy = "ajcvickers"
            });

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_9](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_9)]

<span data-ttu-id="cd372-385">Eine weitere Möglichkeit zum Festlegen von Nutzlastdaten besteht darin, <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> das-Ereignis zu überschreiben oder <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> zu verwenden, um Entitäten vor dem Aktualisieren der Datenbank zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="cd372-385">Finally, another way to set payload data is by either overriding <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or using the <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> event to process entities before updating the database.</span></span> <span data-ttu-id="cd372-386">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cd372-386">For example:</span></span>

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>())
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                }
            }

            return base.SaveChanges();
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=SaveChanges)]
