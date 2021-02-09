---
title: Kaskadierendes Delete – EF Core
description: In diesem Artikel finden Sie Informationen zur Konfiguration des kaskadierenden Verhaltens, das ausgelöst wird, wenn eine Entität gelöscht oder vom Prinzipal bzw. der übergeordneten Entität getrennt wird.
author: ajcvickers
ms.date: 01/07/2021
uid: core/saving/cascade-delete
ms.openlocfilehash: 27ba84fa5d7e0d72e66ccbd96df9b6a5008791fb
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983338"
---
# <a name="cascade-delete"></a><span data-ttu-id="c0112-103">Kaskadierendes Delete</span><span class="sxs-lookup"><span data-stu-id="c0112-103">Cascade Delete</span></span>

<span data-ttu-id="c0112-104">Entity Framework Core (EF Core) stellt Beziehungen mithilfe von Fremdschlüsseln dar.</span><span class="sxs-lookup"><span data-stu-id="c0112-104">Entity Framework Core (EF Core) represents relationships using foreign keys.</span></span> <span data-ttu-id="c0112-105">Eine Entität mit einem Fremdschlüssel ist die untergeordnete oder abhängige Entität in der Beziehung.</span><span class="sxs-lookup"><span data-stu-id="c0112-105">An entity with a foreign key is the child or dependent entity in the relationship.</span></span> <span data-ttu-id="c0112-106">Der Fremdschlüsselwert der Entität muss mit dem Primärschlüsselwert (oder einem alternativen Schlüsselwert) des verwandten Prinzipals bzw. der übergeordneten Entität übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="c0112-106">This entity's foreign key value must match the primary key value (or an alternate key value) of the related principal/parent entity.</span></span>

<span data-ttu-id="c0112-107">Wenn der Prinzipal bzw. die übergeordnete Entität gelöscht wird, stimmen die Fremdschlüsselwerte der abhängigen/untergeordneten Entitäten nicht mehr mit dem Primärschlüssel oder alternativen Schlüssel eines _beliebigen_ Prinzipals bzw. einer übergeordneten Entität überein.</span><span class="sxs-lookup"><span data-stu-id="c0112-107">If the principal/parent entity is deleted, then the foreign key values of the dependents/children will no longer match the primary or alternate key of _any_ principal/parent.</span></span> <span data-ttu-id="c0112-108">Dabei handelt es sich um einen ungültigen Zustand, der bei den meisten Datenbanken zu einem Verstoß gegen eine referenzielle Einschränkung führt.</span><span class="sxs-lookup"><span data-stu-id="c0112-108">This is an invalid state, and will cause a referential constraint violation in most databases.</span></span>

<span data-ttu-id="c0112-109">Es gibt zwei Möglichkeiten, diesen Verstoß gegen eine referenzielle Einschränkung zu umgehen:</span><span class="sxs-lookup"><span data-stu-id="c0112-109">There are two options to avoid this referential constraint violation:</span></span>

1. <span data-ttu-id="c0112-110">Legen Sie die FK-Werte auf NULL fest.</span><span class="sxs-lookup"><span data-stu-id="c0112-110">Set the FK values to null</span></span>
2. <span data-ttu-id="c0112-111">Löschen Sie auch die abhängigen/untergeordneten Entitäten.</span><span class="sxs-lookup"><span data-stu-id="c0112-111">Also delete the dependent/child entities</span></span>

<span data-ttu-id="c0112-112">Die erste Option ist nur für optionale Beziehungen zulässig, bei denen die Fremdschlüsseleigenschaft (und die Datenbankspalte, der sie zugeordnet ist) NULL-Werte zulassen muss.</span><span class="sxs-lookup"><span data-stu-id="c0112-112">The first option in only valid for optional relationships where the foreign key property (and the database column to which it is mapped) must be nullable.</span></span>

<span data-ttu-id="c0112-113">Die zweite Option ist für jede Art von Beziehung zulässig und wird als „kaskadierendes Delete“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="c0112-113">The second option is valid for any kind of relationship and is known as "cascade delete".</span></span>

> [!TIP]
> <span data-ttu-id="c0112-114">In dieser Dokumentation wird das kaskadierende Delete (und das Löschen verwaister Entitäten) aus der Perspektive von Updates der Datenbank beschrieben.</span><span class="sxs-lookup"><span data-stu-id="c0112-114">This document describes cascade deletes (and deleting orphans) from the perspective of updating the database.</span></span> <span data-ttu-id="c0112-115">Dabei werden die in [Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) und [Ändern von Fremdschlüsseln und Navigationen](xref:core/change-tracking/relationship-changes) vorgestellten Konzepte ausgiebig verwendet.</span><span class="sxs-lookup"><span data-stu-id="c0112-115">It makes heavy use of concepts introduced in [Change Tracking in EF Core](xref:core/change-tracking/index) and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes).</span></span> <span data-ttu-id="c0112-116">Stellen Sie sicher, dass Sie diese Konzepte vollständig verstehen, bevor Sie sich mit dem Inhalt dieses Artikels beschäftigen.</span><span class="sxs-lookup"><span data-stu-id="c0112-116">Make sure to fully understand these concepts before tackling the material here.</span></span>

> [!TIP]  
> <span data-ttu-id="c0112-117">Sie können den gesamten Code in dieser Dokumentation ausführen und debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/CascadeDeletes).</span><span class="sxs-lookup"><span data-stu-id="c0112-117">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/CascadeDeletes).</span></span>

## <a name="when-cascading-behaviors-happen"></a><span data-ttu-id="c0112-118">Wann kaskadierendes Verhalten auftritt</span><span class="sxs-lookup"><span data-stu-id="c0112-118">When cascading behaviors happen</span></span>

<span data-ttu-id="c0112-119">Kaskadierende Deletes sind erforderlich, wenn eine abhängige/untergeordnete Entität nicht mehr dem Prinzipal bzw. der übergeordneten Entität zugeordnet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c0112-119">Cascading deletes are needed when a dependent/child entity can no longer be associated with its current principal/parent.</span></span> <span data-ttu-id="c0112-120">Dieser Fall kann auftreten, wenn der Prinzipal bzw. die übergeordnete Entität gelöscht wird oder die abhängige/untergeordnete Entität nicht mehr zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="c0112-120">This can happen because the principal/parent is deleted, or it can happen when the principal/parent still exists but the dependent/child is no longer associated with it.</span></span>

### <a name="deleting-a-principalparent"></a><span data-ttu-id="c0112-121">Löschen eines Prinzipal bzw. einer übergeordneten Entität</span><span class="sxs-lookup"><span data-stu-id="c0112-121">Deleting a principal/parent</span></span>

<span data-ttu-id="c0112-122">Ziehen Sie dieses einfache Modell in Betracht, bei dem `Blog` der Prinzipal bzw. die übergeordnete Entität in einer Beziehung mit der abhängigen/untergeordneten Entität `Post` ist.</span><span class="sxs-lookup"><span data-stu-id="c0112-122">Consider this simple model where `Blog` is the principal/parent in a relationship with `Post`, which is the dependent/child.</span></span> <span data-ttu-id="c0112-123">`Post.BlogId` ist eine Fremdschlüsseleigenschaft, deren Wert mit dem Primärschlüssel `Post.Id` des Beitrags übereinstimmen muss, zu dem der Blog gehört.</span><span class="sxs-lookup"><span data-stu-id="c0112-123">`Post.BlogId` is a foreign key property, the value of which must match the `Post.Id` primary key of the post to which the blog belongs.</span></span>

<!--
    public class Blog
    {
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Model)]

<span data-ttu-id="c0112-124">Gemäß der Konventionen wird diese Beziehung als erforderlich konfiguriert, da die Fremdschlüsseleigenschaft `Post.BlogId` keine NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="c0112-124">By convention, this relationship is configured as a required, since the `Post.BlogId` foreign key property is non-nullable.</span></span> <span data-ttu-id="c0112-125">Erforderliche Beziehungen werden standardmäßig zur Verwendung von kaskadierenden Deletes konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c0112-125">Required relationships are configured to use cascade deletes by default.</span></span> <span data-ttu-id="c0112-126">Weitere Informationen zum Modellieren von Beziehungen finden Sie unter [Beziehungen](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="c0112-126">See [Relationships](xref:core/modeling/relationships) for more information on modeling relationships.</span></span>

<span data-ttu-id="c0112-127">Beim Löschen eines Blogs werden alle Beiträge durch kaskadierende Deletes gelöscht.</span><span class="sxs-lookup"><span data-stu-id="c0112-127">When deleting a blog, all posts are cascade deleted.</span></span> <span data-ttu-id="c0112-128">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c0112-128">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Deleting_principal_parent_1)]

<span data-ttu-id="c0112-129">SaveChanges generiert beispielsweise den folgenden SQL-Code mithilfe von SQL Server:</span><span class="sxs-lookup"><span data-stu-id="c0112-129">SaveChanges generates the following SQL, using SQL Server as an example:</span></span>

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="severing-a-relationship"></a><span data-ttu-id="c0112-130">Aufheben einer Beziehung</span><span class="sxs-lookup"><span data-stu-id="c0112-130">Severing a relationship</span></span>

<span data-ttu-id="c0112-131">Anstatt den Blog zu löschen, können Sie auch die Beziehung zwischen jedem Beitrag und dem zugehörigen Blog löschen.</span><span class="sxs-lookup"><span data-stu-id="c0112-131">Rather than deleting the blog, we could instead sever the relationship between each post and its blog.</span></span> <span data-ttu-id="c0112-132">Hierzu legen Sie die Referenznavigation `Post.Blog` für jeden Beitrag auf NULL fest:</span><span class="sxs-lookup"><span data-stu-id="c0112-132">This can be done by setting the reference navigation `Post.Blog` to null for each post:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_1)]

<span data-ttu-id="c0112-133">Die Beziehung kann auch durch Entfernen aller Beiträge aus der Sammlungsnavigation `Blog.Posts` aufgehoben werden:</span><span class="sxs-lookup"><span data-stu-id="c0112-133">The relationship can also be severed by removing each post from the `Blog.Posts` collection navigation:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_2)]

<span data-ttu-id="c0112-134">In beiden Fällen ist das Ergebnis identisch: der Blog wird nicht gelöscht, aber die Beiträge werden gelöscht, die keinem Blog mehr zugeordnet sind:</span><span class="sxs-lookup"><span data-stu-id="c0112-134">In either case the result is the same: the blog is not deleted, but the posts that are no longer associated with any blog are deleted:</span></span>

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="c0112-135">Das Löschen von Entitäten, die nicht mehr einem Prinzipal bzw. einer abhängigen Entität zugeordnet sind, wird als „Löschen verwaister Entitäten“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="c0112-135">Deleting entities that are no longer associated with any principal/dependent is known as "deleting orphans".</span></span>

> [!TIP]
> <span data-ttu-id="c0112-136">Das kaskadierende Delete und das Löschen verwaister Entitäten sind eng verwandt.</span><span class="sxs-lookup"><span data-stu-id="c0112-136">Cascade delete and deleting orphans are closely related.</span></span> <span data-ttu-id="c0112-137">Beide führen dazu, dass abhängige/untergeordnete Entitäten gelöscht werden, wenn die Beziehung zum erforderlichen Prinzipal bzw. der übergeordneten Entität getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-137">Both result in deleting dependent/child entities when the relationship to their required principal/parent is severed.</span></span> <span data-ttu-id="c0112-138">Beim kaskadierenden Delete erfolgt diese Trennung, weil der Prinzipal bzw. die übergeordnete Entität selbst gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-138">For cascade delete, this severing happens because the principal/parent is itself deleted.</span></span> <span data-ttu-id="c0112-139">Bei verwaisten Entitäten ist der Prinzipal bzw. die übergeordnete Entität weiterhin vorhanden, jedoch besteht die Beziehung zur abhängigen/untergeordneten Entität nicht mehr.</span><span class="sxs-lookup"><span data-stu-id="c0112-139">For orphans, the principal/parent entity still exists, but is no longer related to the dependent/child entities.</span></span>  

## <a name="where-cascading-behaviors-happen"></a><span data-ttu-id="c0112-140">Wo kaskadierendes Verhalten auftritt</span><span class="sxs-lookup"><span data-stu-id="c0112-140">Where cascading behaviors happen</span></span>

<span data-ttu-id="c0112-141">Kaskadierendes Verhalten kann in folgenden Szenarios angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="c0112-141">Cascading behaviors can be applied to:</span></span>

- <span data-ttu-id="c0112-142">Entitäten, die durch die aktuelle <xref:Microsoft.EntityFrameworkCore.DbContext>-Klasse überwacht werden</span><span class="sxs-lookup"><span data-stu-id="c0112-142">Entities tracked by the current <xref:Microsoft.EntityFrameworkCore.DbContext></span></span>
- <span data-ttu-id="c0112-143">Entitäten in der Datenbank, die nicht in den Kontext geladen wurden</span><span class="sxs-lookup"><span data-stu-id="c0112-143">Entities in the database that have not been loaded into the context</span></span>

### <a name="cascade-delete-of-tracked-entities"></a><span data-ttu-id="c0112-144">Kaskadierendes Delete für überwachte Entitäten</span><span class="sxs-lookup"><span data-stu-id="c0112-144">Cascade delete of tracked entities</span></span>

<span data-ttu-id="c0112-145">EF Core wendet kaskadierendes Verhalten immer auf überwachte Entitäten an.</span><span class="sxs-lookup"><span data-stu-id="c0112-145">EF Core always applies configured cascading behaviors to tracked entities.</span></span> <span data-ttu-id="c0112-146">Das bedeutet, dass kaskadierende Verhaltensweisen unabhängig von der Datenbankkonfiguration immer ordnungsgemäß angewendet werden, wenn die Anwendung alle relevanten abhängigen/untergeordneten Entitäten in die DbContext-Klasse lädt (siehe Beispiel oben).</span><span class="sxs-lookup"><span data-stu-id="c0112-146">This means that if the application loads all relevant dependent/child entities into the DbContext, as is shown in the examples above, then cascading behaviors will be correctly applied regardless of how the database is configured.</span></span>

> [!TIP]
> <span data-ttu-id="c0112-147">Das genaue Timing für die Anwendung kaskadierender Verhaltensweisen auf überwachte Entitäten kann mit <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> gesteuert werden.</span><span class="sxs-lookup"><span data-stu-id="c0112-147">The exact timing of when cascading behaviors happen to tracked entities can be controlled using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType>.</span></span> <span data-ttu-id="c0112-148">Weitere Informationen finden Sie unter [Ändern von Fremdschlüsseln und Navigationen](xref:core/change-tracking/relationship-changes).</span><span class="sxs-lookup"><span data-stu-id="c0112-148">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information.</span></span>

### <a name="cascade-delete-in-the-database"></a><span data-ttu-id="c0112-149">Kaskadierendes Delete in der Datenbank</span><span class="sxs-lookup"><span data-stu-id="c0112-149">Cascade delete in the database</span></span>

<span data-ttu-id="c0112-150">Viele Datenbanksysteme bieten auch kaskadierende Verhalten, die ausgelöst werden, wenn eine Entität in der Datenbank gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-150">Many database systems also offer cascading behaviors that are triggered when an entity is deleted in the database.</span></span> <span data-ttu-id="c0112-151">EF Core konfiguriert diese Verhalten anhand der Verhaltensweise des kaskadierenden Deletes im EF Core-Modell, wenn eine Datenbank mit <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> oder EF Core-Migrationen erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-151">EF Core configures these behaviors based on the cascade delete behavior in the EF Core model when a database is created using <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> or EF Core migrations.</span></span> <span data-ttu-id="c0112-152">Bei Verwendung des obigen Modells wird beispielsweise die folgende Tabelle für Beiträge erstellt, wenn SQL Server verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="c0112-152">For example, using the model above, the following table is created for posts when using SQL Server:</span></span>

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    [BlogId] int NOT NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Posts_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([Id]) ON DELETE CASCADE
);
```

<span data-ttu-id="c0112-153">Beachten Sie, dass die Einschränkung für Fremdschlüssel, die die Beziehung zwischen Blogs und Beiträgen definieren, mit `ON DELETE CASCADE` konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="c0112-153">Notice that the foreign key constraint defining the relationship between blogs and posts is configured with `ON DELETE CASCADE`.</span></span>

<span data-ttu-id="c0112-154">Wenn Sie wissen, dass die Datenbank diese Konfiguration aufweist, können Sie einen Blog löschen, _ohne zuerst die Beiträge zu laden_. Die Datenbank übernimmt dann das Löschen aller Beiträge, die mit dem Blog verwandt waren.</span><span class="sxs-lookup"><span data-stu-id="c0112-154">If we know that the database is configured like this, then we can delete a blog _without first loading posts_ and the database will take care of deleting all the posts that were related to that blog.</span></span> <span data-ttu-id="c0112-155">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c0112-155">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Where_cascading_behaviors_happen_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Where_cascading_behaviors_happen_1)]

<span data-ttu-id="c0112-156">Beachten Sie, dass es keine `Include`-Anweisung für Beiträge gibt. Diese werden also nicht geladen.</span><span class="sxs-lookup"><span data-stu-id="c0112-156">Notice that there is no `Include` for posts, so they are not loaded.</span></span> <span data-ttu-id="c0112-157">In diesem Fall wird mit SaveChanges nur der Blog gelöscht, da dieser die einzige Entität darstellt, die überwacht wird:</span><span class="sxs-lookup"><span data-stu-id="c0112-157">SaveChanges in this case will delete just the blog, since that's the only entity being tracked:</span></span>

```sql
-- Executed DbCommand (6ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="c0112-158">Dies würde zu einer Ausnahme führen, wenn die Fremdschlüsseleinschränkung in der Datenbank nicht für kaskadierende Deletes konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="c0112-158">This would result in an exception if the foreign key constraint in the database is not configured for cascade deletes.</span></span> <span data-ttu-id="c0112-159">In diesem Fall werden die Beiträge von der Datenbank gelöscht, da sie bei der Erstellung mit `ON DELETE CASCADE` konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="c0112-159">However, in this case the posts are deleted by the database because it has been configured with `ON DELETE CASCADE` when it was created.</span></span>

> [!NOTE]
> <span data-ttu-id="c0112-160">Datenbanken verfügen normalerweise über keine Möglichkeit zum automatischen Löschen von verwaisten Entitäten.</span><span class="sxs-lookup"><span data-stu-id="c0112-160">Databases don't typically have any way to automatically delete orphans.</span></span> <span data-ttu-id="c0112-161">Das liegt daran, dass Datenbanken nur über Fremdschlüssel und über keine Navigationen verfügen, obwohl EF Core Beziehungen mithilfe von Navigationen und Fremdschlüsseln darstellt.</span><span class="sxs-lookup"><span data-stu-id="c0112-161">This is because while EF Core represents relationships using navigations as well of foreign keys, databases have only foreign keys and no navigations.</span></span> <span data-ttu-id="c0112-162">Das bedeutet, dass es in der Regel nicht möglich ist, eine Beziehung zu trennen, ohne beide Seiten in die DbContext-Klasse zu laden.</span><span class="sxs-lookup"><span data-stu-id="c0112-162">This means that it is usually not possible to sever a relationship without loading both sides into the DbContext.</span></span>

> [!NOTE]
> <span data-ttu-id="c0112-163">Die In-Memory-Datenbank von EF Core unterstützt kaskadierende Deletes in der Datenbank derzeit nicht.</span><span class="sxs-lookup"><span data-stu-id="c0112-163">The EF Core in-memory database does not currently support cascade deletes in the database.</span></span>

> [!WARNING]
> <span data-ttu-id="c0112-164">Konfigurieren Sie beim vorläufigen Löschen von Entitäten kein kaskadierendes Delete in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c0112-164">Do not configure cascade delete in the database when soft-deleting entities.</span></span> <span data-ttu-id="c0112-165">Dies kann dazu führen, dass Entitäten versehentlich tatsächlich gelöscht werden, anstatt nur vorläufig gelöscht zu werden.</span><span class="sxs-lookup"><span data-stu-id="c0112-165">This may cause entities to be accidentally really deleted instead of soft-deleted.</span></span>

### <a name="database-cascade-limitations"></a><span data-ttu-id="c0112-166">Einschränkungen bei kaskadierendem Verhalten in Datenbanken</span><span class="sxs-lookup"><span data-stu-id="c0112-166">Database cascade limitations</span></span>

<span data-ttu-id="c0112-167">Einige Datenbanken, insbesondere SQL Server, weisen Einschränkungen bei den kaskadierenden Verhaltensweisen auf, die Zyklen bilden.</span><span class="sxs-lookup"><span data-stu-id="c0112-167">Some databases, most notably SQL Server, have limitations on the cascade behaviors that form cycles.</span></span> <span data-ttu-id="c0112-168">Betrachten Sie beispielsweise das folgende Modell:</span><span class="sxs-lookup"><span data-stu-id="c0112-168">For example, consider the following model:</span></span>

<!--
    public class Blog
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
        
        public int OwnerId { get; set; }
        public Person Owner { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
        
        public int AuthorId { get; set; }
        public Person Author { get; set; }
    }

    public class Person
    {
        public int Id { get; set; }
        public string Name { get; set; }
        
        public IList<Post> Posts { get; } = new List<Post>();

        public Blog OwnedBlog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Model)]

<span data-ttu-id="c0112-169">Dieses Modell umfasst drei Beziehungen, die alle erforderlich sind und daher konventionsbedingt für kaskadierende Deletes konfiguriert sind:</span><span class="sxs-lookup"><span data-stu-id="c0112-169">This model has three relationships, all required and therefore configured to cascade delete by convention:</span></span>

- <span data-ttu-id="c0112-170">Das Löschen eines Blogs führt zu einem kaskadierenden Delete aller zugehöriger Beiträge.</span><span class="sxs-lookup"><span data-stu-id="c0112-170">Deleting a blog will cascade delete all the related posts</span></span>
- <span data-ttu-id="c0112-171">Das Löschen des Erstellers von Beiträgen führt zu einem kaskadierenden Delete dieser Beiträge.</span><span class="sxs-lookup"><span data-stu-id="c0112-171">Deleting the author of posts will cause the authored posts to be cascade deleted</span></span>
- <span data-ttu-id="c0112-172">Das Löschen des Besitzers eines Blogs führt zu einem kaskadierenden Delete des Blogs.</span><span class="sxs-lookup"><span data-stu-id="c0112-172">Deleting the owner of a blog will cause the blog to be cascade deleted</span></span>

<span data-ttu-id="c0112-173">Dieses Verhalten ist sinnvoll (und höchstens streng im Kontext von Blogverwaltungsrichtlinien), jedoch könnte ein Versuch, eine SQL Server-Datenbank mit diesen kaskadierenden Deletes zu erstellen, die folgende Ausnahme auslösen:</span><span class="sxs-lookup"><span data-stu-id="c0112-173">This is all reasonable (if a bit draconian in blog management policies!) but attempting to create a SQL Server database with these cascades configured results in the following exception:</span></span>

> <span data-ttu-id="c0112-174">Microsoft.Data.SqlClient.SqlException (0x80131904): Das Einführen der FOREIGN KEY-Einschränkung 'FK_Posts_Person_AuthorId' für die 'Posts'-Tabelle kann Schleifen oder mehrere kaskadierende Pfade verursachen.</span><span class="sxs-lookup"><span data-stu-id="c0112-174">Microsoft.Data.SqlClient.SqlException (0x80131904): Introducing FOREIGN KEY constraint 'FK_Posts_Person_AuthorId' on table 'Posts' may cause cycles or multiple cascade paths.</span></span> <span data-ttu-id="c0112-175">Geben Sie ON DELETE NO ACTION oder ON UPDATE NO ACTION an, oder ändern Sie andere FOREIGN KEY-Einschränkungen.</span><span class="sxs-lookup"><span data-stu-id="c0112-175">Specify ON DELETE NO ACTION or ON UPDATE NO ACTION, or modify other FOREIGN KEY constraints.</span></span>

<span data-ttu-id="c0112-176">Es gibt zwei Möglichkeiten, diese Situation zu beheben:</span><span class="sxs-lookup"><span data-stu-id="c0112-176">There are two ways to handle this situation:</span></span>

1. <span data-ttu-id="c0112-177">Ändern Sie mindestens eine der Beziehungen so, dass kein kaskadierendes Delete durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-177">Change one or more of the relationships to not cascade delete.</span></span>
2. <span data-ttu-id="c0112-178">Konfigurieren Sie die Datenbanken ohne diese kaskadierenden Deletes, und stellen Sie dann sicher, dass alle abhängigen Entitäten geladen werden, damit EF Core das kaskadierende Verhalten durchführen kann.</span><span class="sxs-lookup"><span data-stu-id="c0112-178">Configure the database without one or more of these cascade deletes, then ensure all dependent entities are loaded so that EF Core can perform the cascading behavior.</span></span>

<span data-ttu-id="c0112-179">Wenn Sie beispielsweise den ersten Ansatz im Beispiel anwenden, könnten Sie die Blogbesitzerbeziehung als optional einrichten, indem Sie diese mit einer NULL-Werte zulassenden Fremdschlüsseleigenschaft versehen.</span><span class="sxs-lookup"><span data-stu-id="c0112-179">Taking the first approach with our example, we could make the blog-owner relationship optional by giving it a nullable foreign key property:</span></span>

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

<span data-ttu-id="c0112-180">Eine optionale Beziehung ermöglicht, dass der Blog ohne Besitzer vorhanden ist, was bedeutet, dass das kaskadierende Delete nicht mehr standardmäßig konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="c0112-180">An optional relationship allows the blog to exist without an owner, which means cascade delete will no longer be configured by default.</span></span> <span data-ttu-id="c0112-181">Aus diesem Grund besteht der Zyklus aus kaskadierenden Aktionen nicht mehr, und die Datenbank kann ohne Fehler in SQL Server erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="c0112-181">This means there is no longer a cycle in cascading actions, and the database can be created without error on SQL Server.</span></span>

<span data-ttu-id="c0112-182">Wenn Sie hingegen den zweiten Ansatz anwenden, können Sie die Blogbesitzerbeziehung als erforderlich und mit der Konfiguration für ein kaskadierendes Delete beibehalten, aber die Konfiguration so einrichten, dass sie nur auf überwachte Entitäten, anstelle der gesamten Datenbank angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="c0112-182">Taking the second approach instead, we can keep the blog-owner relationship required and configured for cascade delete, but make this configuration only apply to tracked entities, not the database:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

<span data-ttu-id="c0112-183">Was geschieht jedoch nun, wenn sowohl eine Person als auch der sich im Besitz dieser Person befindende Blog geladen werden und dann die Person gelöscht wird?</span><span class="sxs-lookup"><span data-stu-id="c0112-183">Now what happens if we load both a person and the blog they own, then delete the person?</span></span>

<!--
            using var context = new BlogsContext();

            var owner = context.People.Single(e => e.Name == "ajcvickers");
            var blog = context.Blogs.Single(e => e.Owner == owner);

            context.Remove(owner);
            
            context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_1](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_1)]

<span data-ttu-id="c0112-184">EF Core führt ein kaskadierendes Delete für den Besitzer durch, sodass der Blog ebenfalls gelöscht wird:</span><span class="sxs-lookup"><span data-stu-id="c0112-184">EF Core will cascade the delete of the owner so that the blog is also deleted:</span></span>

```sql
-- Executed DbCommand (8ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [People]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="c0112-185">Wenn der Blog jedoch nicht geladen ist, wenn der Besitzer gelöscht wird:</span><span class="sxs-lookup"><span data-stu-id="c0112-185">However, if the blog is not loaded when the owner is deleted:</span></span>

<!--
                using var context = new BlogsContext();

                var owner = context.People.Single(e => e.Name == "ajcvickers");

                context.Remove(owner);
            
                context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_2](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_2)]

<span data-ttu-id="c0112-186">Dann wird eine Ausnahme aufgrund des Verstoßes gegen die Fremdschlüsseleinschränkung in der Datenbank ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="c0112-186">Then an exception will be thrown due to violation of the foreign key constraint in the database:</span></span>

> <span data-ttu-id="c0112-187">Microsoft.Data.SqlClient.SqlException: Die DELETE-Anweisung steht in Konflikt mit der REFERENCE-Einschränkung „FK_Blogs_People_OwnerId“.</span><span class="sxs-lookup"><span data-stu-id="c0112-187">Microsoft.Data.SqlClient.SqlException: The DELETE statement conflicted with the REFERENCE constraint "FK_Blogs_People_OwnerId".</span></span> <span data-ttu-id="c0112-188">Der Konflikt ist in der Spalte „OwnerId“ der Tabelle „dbo.Blogs“ in der Datenbank „Scratch“ aufgetreten.</span><span class="sxs-lookup"><span data-stu-id="c0112-188">The conflict occurred in database "Scratch", table "dbo.Blogs", column 'OwnerId'.</span></span>
<span data-ttu-id="c0112-189">Die Anweisung wurde beendet.</span><span class="sxs-lookup"><span data-stu-id="c0112-189">The statement has been terminated.</span></span>

## <a name="cascading-nulls"></a><span data-ttu-id="c0112-190">Kaskadierende NULL-Werte</span><span class="sxs-lookup"><span data-stu-id="c0112-190">Cascading nulls</span></span>

<span data-ttu-id="c0112-191">Optionale Beziehungen umfassen NULL-Werte zulassende Fremdschlüsseleigenschaften, die NULL-Werte zulassenden Datenbankspalten zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="c0112-191">Optional relationships have nullable foreign key properties mapped to nullable database columns.</span></span> <span data-ttu-id="c0112-192">Das bedeutet, dass der Fremdschlüsselwert auf NULL festgelegt werden kann, wenn der aktuelle Prinzipal bzw. die übergeordnete Entität gelöscht oder von der abhängigen/untergeordneten Entität getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-192">This means that the foreign key value can be set to null when the current principal/parent is deleted or is severed from the dependent/child.</span></span>

<span data-ttu-id="c0112-193">Im Folgenden werden die Beispiele aus dem Abschnitt [Wann kaskadierendes Verhalten auftritt](#when-cascading-behaviors-happen) noch mal untersucht, jedoch verfügen sie diesmal über eine optionale Beziehung, die von einer NULL-Werte zulassenden `Post.BlogId`-Fremdschlüsseleigenschaft dargestellt wird:</span><span class="sxs-lookup"><span data-stu-id="c0112-193">Let's look again at the examples from [When cascading behaviors happen](#when-cascading-behaviors-happen), but this time with an optional relationship represented by a nullable `Post.BlogId` foreign key property:</span></span>

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

<span data-ttu-id="c0112-194">Diese Fremdschlüsseleigenschaft wird für jeden Beitrag auf NULL festgelegt, wenn der zugehörige Blog gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-194">This foreign key property will be set to null for each post when its related blog is deleted.</span></span> <span data-ttu-id="c0112-195">Ein Beispiel hierfür ist der folgende Code, der identisch mit dem vorherigen ist:</span><span class="sxs-lookup"><span data-stu-id="c0112-195">For example, this code, which is the same as before:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Deleting_principal_parent_1b)]

<span data-ttu-id="c0112-196">Der Code resultiert nun in den folgenden Updates der Datenbank, wenn SaveChanges aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="c0112-196">Will now result in the following database updates when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (1ms) [Parameters=[@p2='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p2;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="c0112-197">Dies gilt auch, wenn die Beziehung mit einem der oben genannten Beispiele getrennt wird:</span><span class="sxs-lookup"><span data-stu-id="c0112-197">Likewise, if the relationship is severed using either of the examples from above:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_1b)]

<span data-ttu-id="c0112-198">Oder:</span><span class="sxs-lookup"><span data-stu-id="c0112-198">Or:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_2b)]

<span data-ttu-id="c0112-199">Dann werden die Beiträge mit NULL-Fremdschlüsselwerten aktualisiert, wenn SaveChanges aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="c0112-199">Then the posts are updated with null foreign key values when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

<span data-ttu-id="c0112-200">Weitere Informationen zur EF Core-Verwaltung von Fremdschlüsseln und Navigationen bei Änderungen ihrer Werte finden Sie unter [Ändern von Fremdschlüsseln und Navigationen](xref:core/change-tracking/relationship-changes).</span><span class="sxs-lookup"><span data-stu-id="c0112-200">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information on how EF Core manages foreign keys and navigations as their values are changed.</span></span>

> [!NOTE]
> <span data-ttu-id="c0112-201">Die Behebung von Beziehungen wie dieser stellt seit der ersten Version von Entity Framework in 2008 das Standardverhalten dar.</span><span class="sxs-lookup"><span data-stu-id="c0112-201">The fixup of relationships like this has been the default behavior of Entity Framework since the first version in 2008.</span></span> <span data-ttu-id="c0112-202">Vor EF Core gab es keinen Namen für dieses Verhalten und es konnte nicht geändert werden.</span><span class="sxs-lookup"><span data-stu-id="c0112-202">Prior to EF Core it didn't have a name and was not possible to change.</span></span> <span data-ttu-id="c0112-203">Nun ist es wie im nächsten Abschnitt beschrieben als `ClientSetNull` bekannt.</span><span class="sxs-lookup"><span data-stu-id="c0112-203">It is now known as `ClientSetNull` as described in the next section.</span></span>

<span data-ttu-id="c0112-204">Datenbanken können auf diese Weise auch für kaskadierende NULL-Werte konfiguriert werden, wenn ein Prinzipal bzw. eine übergeordnete Entität in einer optionalen Beziehung gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-204">Databases can also be configured to cascade nulls like this when a principal/parent in an optional relationship is deleted.</span></span> <span data-ttu-id="c0112-205">Allerdings ist dieser Fall weitaus seltener als die Verwendung von kaskadierenden Deletes in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c0112-205">However, this is much less common than using cascading deletes in the database.</span></span> <span data-ttu-id="c0112-206">Die gleichzeitige Verwendung von kaskadierenden Deletes und kaskadierenden NULL-Werten in der Datenbank führt fast immer zu Beziehungszyklen, wenn Sie SQL Server verwenden.</span><span class="sxs-lookup"><span data-stu-id="c0112-206">Using cascading deletes and cascading nulls in the database at the same time will almost always result in relationship cycles when using SQL Server.</span></span> <span data-ttu-id="c0112-207">Weitere Informationen zur Konfiguration von kaskadierenden NULL-Werten finden Sie im nächsten Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="c0112-207">See the next section for more information on configuring cascading nulls.</span></span>

## <a name="configuring-cascading-behaviors"></a><span data-ttu-id="c0112-208">Konfigurieren von kaskadierendem Verhalten</span><span class="sxs-lookup"><span data-stu-id="c0112-208">Configuring cascading behaviors</span></span>

> [!TIP]
> <span data-ttu-id="c0112-209">Lesen Sie unbedingt die vorherigen Abschnitte, bevor Sie mit diesem Abschnitt beginnen.</span><span class="sxs-lookup"><span data-stu-id="c0112-209">Be sure to read sections above before coming here.</span></span> <span data-ttu-id="c0112-210">Sie werden die hier erläuterten Konfigurationsoptionen vermutlich nicht verstehen, wenn Sie die vorherigen Informationen nicht verinnerlicht haben.</span><span class="sxs-lookup"><span data-stu-id="c0112-210">The configuration options will likely not make sense if the preceding material is not understood.</span></span>

<span data-ttu-id="c0112-211">Kaskadierende Verhalten werden mithilfe der <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A>-Methode in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> pro Beziehung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c0112-211">Cascade behaviors are configured per relationship using the <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A> method in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="c0112-212">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c0112-212">For example:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

<span data-ttu-id="c0112-213">Weitere Informationen zum Konfigurieren von Beziehungen zwischen Entitätstypen finden Sie unter [Beziehungen](xref:core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="c0112-213">See [Relationships](xref:core/modeling/relationships) for more information on configuring relationships between entity types.</span></span>

<span data-ttu-id="c0112-214">`OnDelete` akzeptiert einen Wert aus der <xref:Microsoft.EntityFrameworkCore.DeleteBehavior>-Enumeration (was zugegebenermaßen verwirrend sein kann).</span><span class="sxs-lookup"><span data-stu-id="c0112-214">`OnDelete` accepts a value from the, admittedly confusing, <xref:Microsoft.EntityFrameworkCore.DeleteBehavior> enum.</span></span> <span data-ttu-id="c0112-215">Diese Enumeration definiert sowohl das Verhalten von EF Core für überwachte Entitäten und die Konfiguration von kaskadierenden Deletes in der Datenbank, wenn Entity Framework zum Erstellen des Schemas verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-215">This enum defines both the behavior of EF Core on tracked entities, and the configuration of cascade delete in the database when EF is used to create the schema.</span></span>

### <a name="impact-on-database-schema"></a><span data-ttu-id="c0112-216">Auswirkungen auf das Datenbankschema</span><span class="sxs-lookup"><span data-stu-id="c0112-216">Impact on database schema</span></span>

<span data-ttu-id="c0112-217">In der folgenden Tabelle werden die Ergebnisse der einzelnen `OnDelete`-Werte für die Fremdschlüsseleinschränkung aufgeführt, die von EF Core-Migrationen oder <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-217">The following table shows the result of each `OnDelete` value on the foreign key constraint created by EF Core migrations or <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A>.</span></span>

| <span data-ttu-id="c0112-218">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="c0112-218">DeleteBehavior</span></span>        | <span data-ttu-id="c0112-219">Auswirkungen auf das Datenbankschema</span><span class="sxs-lookup"><span data-stu-id="c0112-219">Impact on database schema</span></span>
|:----------------------|--------------------------
| <span data-ttu-id="c0112-220">Cascade</span><span class="sxs-lookup"><span data-stu-id="c0112-220">Cascade</span></span>               | <span data-ttu-id="c0112-221">ON DELETE CASCADE</span><span class="sxs-lookup"><span data-stu-id="c0112-221">ON DELETE CASCADE</span></span>
| <span data-ttu-id="c0112-222">Einschränken</span><span class="sxs-lookup"><span data-stu-id="c0112-222">Restrict</span></span>              | <span data-ttu-id="c0112-223">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="c0112-223">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="c0112-224">NoAction</span><span class="sxs-lookup"><span data-stu-id="c0112-224">NoAction</span></span>              | <span data-ttu-id="c0112-225">Datenbankstandard</span><span class="sxs-lookup"><span data-stu-id="c0112-225">database default</span></span>
| <span data-ttu-id="c0112-226">SetNull</span><span class="sxs-lookup"><span data-stu-id="c0112-226">SetNull</span></span>               | <span data-ttu-id="c0112-227">ON DELETE SET NULL</span><span class="sxs-lookup"><span data-stu-id="c0112-227">ON DELETE SET NULL</span></span>
| <span data-ttu-id="c0112-228">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="c0112-228">ClientSetNull</span></span>         | <span data-ttu-id="c0112-229">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="c0112-229">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="c0112-230">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="c0112-230">ClientCascade</span></span>         | <span data-ttu-id="c0112-231">ON DELETE NO ACTION</span><span class="sxs-lookup"><span data-stu-id="c0112-231">ON DELETE NO ACTION</span></span>
| <span data-ttu-id="c0112-232">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="c0112-232">ClientNoAction</span></span>        | <span data-ttu-id="c0112-233">Datenbankstandard</span><span class="sxs-lookup"><span data-stu-id="c0112-233">database default</span></span>

> [!NOTE]
> <span data-ttu-id="c0112-234">Da diese Tabelle verwirrend sein kann, wird sie in einem zukünftigen Release überarbeitet.</span><span class="sxs-lookup"><span data-stu-id="c0112-234">This table is confusing and we plan to revisit this in a future release.</span></span> <span data-ttu-id="c0112-235">Weitere Informationen finden Sie unter [GitHub-Issue #21252](https://github.com/dotnet/efcore/issues/21252).</span><span class="sxs-lookup"><span data-stu-id="c0112-235">See [GitHub Issue #21252](https://github.com/dotnet/efcore/issues/21252).</span></span>

<span data-ttu-id="c0112-236">Die Verhaltensweisen von `ON DELETE NO ACTION` und `ON DELETE RESTRICT` in relationalen Datenbanken sind in der Regel entweder identisch oder sehr ähnlich.</span><span class="sxs-lookup"><span data-stu-id="c0112-236">The behaviors of `ON DELETE NO ACTION` and `ON DELETE RESTRICT` in relational databases are typically either identical or very similar.</span></span> <span data-ttu-id="c0112-237">Unabhängig davon, was von `NO ACTION` impliziert wird, führen beide dieser Optionen zu referenziellen Einschränkungen.</span><span class="sxs-lookup"><span data-stu-id="c0112-237">Despite what `NO ACTION` may imply, both of these options cause referential constraints to be enforced.</span></span> <span data-ttu-id="c0112-238">Sofern es einen Unterschied gibt, besteht dieser darin, _wann_ die Datenbank die Einschränkungen überprüft.</span><span class="sxs-lookup"><span data-stu-id="c0112-238">The difference, when there is one, is _when_ the database checks the constraints.</span></span>  <span data-ttu-id="c0112-239">Überprüfen Sie die Dokumentation Ihrer Datenbank auf spezifische Unterschiede zwischen `ON DELETE NO ACTION` und `ON DELETE RESTRICT` für Ihr Datenbanksystem.</span><span class="sxs-lookup"><span data-stu-id="c0112-239">Check your database documentation for the specific differences between `ON DELETE NO ACTION` and `ON DELETE RESTRICT` on your database system.</span></span>

<span data-ttu-id="c0112-240">Die einzigen Werte, die kaskadierendes Verhalten in der Datenbank auslösen, sind `Cascade` und `SetNull`.</span><span class="sxs-lookup"><span data-stu-id="c0112-240">The only values that will cause cascading behaviors on the database are `Cascade` and `SetNull`.</span></span> <span data-ttu-id="c0112-241">Alle anderen Werte konfigurieren die Datenbank so, dass keine kaskadierenden Änderungen auftreten.</span><span class="sxs-lookup"><span data-stu-id="c0112-241">All other values will configure the database to not cascade any changes.</span></span>

### <a name="impact-on-savechanges-behavior"></a><span data-ttu-id="c0112-242">Auswirkung auf das Verhalten von SaveChanges</span><span class="sxs-lookup"><span data-stu-id="c0112-242">Impact on SaveChanges behavior</span></span>

<span data-ttu-id="c0112-243">Die Tabellen in den folgenden Abschnitten befassen sich damit, was mit den abhängigen/untergeordneten Entitäten geschieht, wenn der Prinzipal bzw. die übergeordnete Entität gelöscht oder die Beziehung zu den abhängigen/untergeordneten Entitäten getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-243">The tables in the following sections cover what happens to dependent/child entities when the principal/parent is deleted, or its relationship to the dependent/child entities is severed.</span></span> <span data-ttu-id="c0112-244">Jede Tabelle umfasst:</span><span class="sxs-lookup"><span data-stu-id="c0112-244">Each table covers one of:</span></span>

- <span data-ttu-id="c0112-245">Optionale (NULL-Werte zulassende Fremdschlüssel) und erforderliche (NULL-Werte nicht zulassende Fremdschlüssel) Beziehungen</span><span class="sxs-lookup"><span data-stu-id="c0112-245">Optional (nullable FK) and required (non-nullable FK) relationships</span></span>
- <span data-ttu-id="c0112-246">Wann abhängige/untergeordnete Entitäten geladen und von der DbContext-Klasse überwacht werden, sowie wann sie nur in der Datenbank vorhanden sind</span><span class="sxs-lookup"><span data-stu-id="c0112-246">When dependents/children are loaded and tracked by the DbContext and when they exist only in the database</span></span>

#### <a name="required-relationship-with-dependentschildren-loaded"></a><span data-ttu-id="c0112-247">Erforderliche Beziehung mit geladenen abhängigen/untergeordneten Entitäten</span><span class="sxs-lookup"><span data-stu-id="c0112-247">Required relationship with dependents/children loaded</span></span>

| <span data-ttu-id="c0112-248">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="c0112-248">DeleteBehavior</span></span>    | <span data-ttu-id="c0112-249">Bei Löschung des Prinzipals bzw. der übergeordneten Entität</span><span class="sxs-lookup"><span data-stu-id="c0112-249">On deleting principal/parent</span></span>             | <span data-ttu-id="c0112-250">Bei Trennung der Beziehung zum Prinzipal bzw. zur übergeordneten Entität</span><span class="sxs-lookup"><span data-stu-id="c0112-250">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="c0112-251">Cascade</span><span class="sxs-lookup"><span data-stu-id="c0112-251">Cascade</span></span>           | <span data-ttu-id="c0112-252">Abhängige Entitäten werden von EF Core gelöscht</span><span class="sxs-lookup"><span data-stu-id="c0112-252">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="c0112-253">Abhängige Entitäten werden von EF Core gelöscht</span><span class="sxs-lookup"><span data-stu-id="c0112-253">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="c0112-254">Einschränken</span><span class="sxs-lookup"><span data-stu-id="c0112-254">Restrict</span></span>          | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="c0112-255">NoAction</span><span class="sxs-lookup"><span data-stu-id="c0112-255">NoAction</span></span>          | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="c0112-256">SetNull</span><span class="sxs-lookup"><span data-stu-id="c0112-256">SetNull</span></span>           | <span data-ttu-id="c0112-257">`SqlException` bei Erstellung der Datenbank</span><span class="sxs-lookup"><span data-stu-id="c0112-257">`SqlException` on creating database</span></span>      | <span data-ttu-id="c0112-258">`SqlException` bei Erstellung der Datenbank</span><span class="sxs-lookup"><span data-stu-id="c0112-258">`SqlException` on creating database</span></span>
| <span data-ttu-id="c0112-259">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="c0112-259">ClientSetNull</span></span>     | `InvalidOperationException`              | `InvalidOperationException`
| <span data-ttu-id="c0112-260">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="c0112-260">ClientCascade</span></span>     | <span data-ttu-id="c0112-261">Abhängige Entitäten werden von EF Core gelöscht</span><span class="sxs-lookup"><span data-stu-id="c0112-261">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="c0112-262">Abhängige Entitäten werden von EF Core gelöscht</span><span class="sxs-lookup"><span data-stu-id="c0112-262">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="c0112-263">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="c0112-263">ClientNoAction</span></span>    | `DbUpdateException`                      | `InvalidOperationException`

<span data-ttu-id="c0112-264">Hinweise:</span><span class="sxs-lookup"><span data-stu-id="c0112-264">Notes:</span></span>

- <span data-ttu-id="c0112-265">Der Standardwert für erforderliche Beziehungen wie diese lautet: `Cascade`.</span><span class="sxs-lookup"><span data-stu-id="c0112-265">The default for required relationships like this is `Cascade`.</span></span>
- <span data-ttu-id="c0112-266">Die Verwendung anderer Optionen als das kaskadierende Delete für erforderliche Beziehungen führt zu einer Ausnahme, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-266">Using anything other than cascade delete for required relationships will result in an exception when SaveChanges is called.</span></span>
  - <span data-ttu-id="c0112-267">Hierbei handelt es sich in der Regel um eine `InvalidOperationException`-Ausnahme von EF Core, da der ungültige Zustand in den geladenen untergeordneten/abhängigen Entitäten erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-267">Typically, this is an `InvalidOperationException` from EF Core since the invalid state is detected in the loaded children/dependents.</span></span>
  - <span data-ttu-id="c0112-268">`ClientNoAction` zwingt EF Core dazu, abhängige Behebungen zu überprüfen, bevor sie an die Datenbank übermittelt werden. Daher löst die Datenbank in diesem Fall eine Ausnahme aus, die dann von SaveChanges mit einer `DbUpdateException`-Ausnahme umschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-268">`ClientNoAction` forces EF Core to not check fixup dependents before sending them to the database, so in this case the database throws an exception, which is then wrapped in a `DbUpdateException` by SaveChanges.</span></span>
  - <span data-ttu-id="c0112-269">`SetNull` wird beim Erstellen der Datenbank nicht zugelassen, da die Fremdschlüsselspalte keine NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="c0112-269">`SetNull` is rejected when creating the database since the foreign key column is not nullable.</span></span>
- <span data-ttu-id="c0112-270">Da abhängige/untergeordnete Entitäten geladen werden, werden sie immer von EF Core gelöscht. Sie werden nie von der Datenbank gelöscht.</span><span class="sxs-lookup"><span data-stu-id="c0112-270">Since dependents/children are loaded, they are always deleted by EF Core, and never left for the database to delete.</span></span>

#### <a name="required-relationship-with-dependentschildren-not-loaded"></a><span data-ttu-id="c0112-271">Erforderliche Beziehung mit nicht geladenen abhängigen/untergeordneten Entitäten</span><span class="sxs-lookup"><span data-stu-id="c0112-271">Required relationship with dependents/children not loaded</span></span>

| <span data-ttu-id="c0112-272">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="c0112-272">DeleteBehavior</span></span>    | <span data-ttu-id="c0112-273">Bei Löschung des Prinzipals bzw. der übergeordneten Entität</span><span class="sxs-lookup"><span data-stu-id="c0112-273">On deleting principal/parent</span></span>             | <span data-ttu-id="c0112-274">Bei Trennung der Beziehung zum Prinzipal bzw. zur übergeordneten Entität</span><span class="sxs-lookup"><span data-stu-id="c0112-274">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="c0112-275">Cascade</span><span class="sxs-lookup"><span data-stu-id="c0112-275">Cascade</span></span>           | <span data-ttu-id="c0112-276">Abhängige Entitäten werden von der Datenbank gelöscht</span><span class="sxs-lookup"><span data-stu-id="c0112-276">Dependents deleted by database</span></span>           | <span data-ttu-id="c0112-277">–</span><span class="sxs-lookup"><span data-stu-id="c0112-277">N/A</span></span>
| <span data-ttu-id="c0112-278">Einschränken</span><span class="sxs-lookup"><span data-stu-id="c0112-278">Restrict</span></span>          | `DbUpdateException`                      | <span data-ttu-id="c0112-279">–</span><span class="sxs-lookup"><span data-stu-id="c0112-279">N/A</span></span>
| <span data-ttu-id="c0112-280">NoAction</span><span class="sxs-lookup"><span data-stu-id="c0112-280">NoAction</span></span>          | `DbUpdateException`                      | <span data-ttu-id="c0112-281">–</span><span class="sxs-lookup"><span data-stu-id="c0112-281">N/A</span></span>
| <span data-ttu-id="c0112-282">SetNull</span><span class="sxs-lookup"><span data-stu-id="c0112-282">SetNull</span></span>           | <span data-ttu-id="c0112-283">`SqlException` bei Erstellung der Datenbank</span><span class="sxs-lookup"><span data-stu-id="c0112-283">`SqlException` on creating database</span></span>      | <span data-ttu-id="c0112-284">–</span><span class="sxs-lookup"><span data-stu-id="c0112-284">N/A</span></span>
| <span data-ttu-id="c0112-285">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="c0112-285">ClientSetNull</span></span>     | `DbUpdateException`                      | <span data-ttu-id="c0112-286">–</span><span class="sxs-lookup"><span data-stu-id="c0112-286">N/A</span></span>
| <span data-ttu-id="c0112-287">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="c0112-287">ClientCascade</span></span>     | `DbUpdateException`                      | <span data-ttu-id="c0112-288">–</span><span class="sxs-lookup"><span data-stu-id="c0112-288">N/A</span></span>
| <span data-ttu-id="c0112-289">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="c0112-289">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="c0112-290">–</span><span class="sxs-lookup"><span data-stu-id="c0112-290">N/A</span></span>

<span data-ttu-id="c0112-291">Hinweise:</span><span class="sxs-lookup"><span data-stu-id="c0112-291">Notes:</span></span>

- <span data-ttu-id="c0112-292">Das Trennen einer Beziehung ist hier nicht zulässig, da die abhängigen/untergeordneten Entitäten nicht geladen werden.</span><span class="sxs-lookup"><span data-stu-id="c0112-292">Severing a relationship is not valid here since the dependents/children are not loaded.</span></span>
- <span data-ttu-id="c0112-293">Der Standardwert für erforderliche Beziehungen wie diese lautet: `Cascade`.</span><span class="sxs-lookup"><span data-stu-id="c0112-293">The default for required relationships like this is `Cascade`.</span></span>
- <span data-ttu-id="c0112-294">Die Verwendung anderer Optionen als das kaskadierende Delete für erforderliche Beziehungen führt zu einer Ausnahme, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-294">Using anything other than cascade delete for required relationships will result in an exception when SaveChanges is called.</span></span>
  - <span data-ttu-id="c0112-295">In der Regel handelt es sich dabei um eine `DbUpdateException`-Ausnahme, da die abhängigen/untergeordneten Entitäten nicht geladen werden. Aus diesem Grund kann der ungültige Zustand nur von der Datenbank erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="c0112-295">Typically, this is a `DbUpdateException` because the dependents/children are not loaded, and hence the invalid state can only be detected by the database.</span></span> <span data-ttu-id="c0112-296">Anschließend umschließt SaveChanges die Datenbankausnahme in `DbUpdateException`.</span><span class="sxs-lookup"><span data-stu-id="c0112-296">SaveChanges then wraps the database exception in a `DbUpdateException`.</span></span>
  - <span data-ttu-id="c0112-297">`SetNull` wird beim Erstellen der Datenbank nicht zugelassen, da die Fremdschlüsselspalte keine NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="c0112-297">`SetNull` is rejected when creating the database since the foreign key column is not nullable.</span></span>

#### <a name="optional-relationship-with-dependentschildren-loaded"></a><span data-ttu-id="c0112-298">Optionale Beziehung mit geladenen abhängigen/untergeordneten Entitäten</span><span class="sxs-lookup"><span data-stu-id="c0112-298">Optional relationship with dependents/children loaded</span></span>

| <span data-ttu-id="c0112-299">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="c0112-299">DeleteBehavior</span></span>    | <span data-ttu-id="c0112-300">Bei Löschung des Prinzipals bzw. der übergeordneten Entität</span><span class="sxs-lookup"><span data-stu-id="c0112-300">On deleting principal/parent</span></span>             | <span data-ttu-id="c0112-301">Bei Trennung der Beziehung zum Prinzipal bzw. zur übergeordneten Entität</span><span class="sxs-lookup"><span data-stu-id="c0112-301">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="c0112-302">Cascade</span><span class="sxs-lookup"><span data-stu-id="c0112-302">Cascade</span></span>           | <span data-ttu-id="c0112-303">Abhängige Entitäten werden von EF Core gelöscht</span><span class="sxs-lookup"><span data-stu-id="c0112-303">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="c0112-304">Abhängige Entitäten werden von EF Core gelöscht</span><span class="sxs-lookup"><span data-stu-id="c0112-304">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="c0112-305">Einschränken</span><span class="sxs-lookup"><span data-stu-id="c0112-305">Restrict</span></span>          | <span data-ttu-id="c0112-306">Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt</span><span class="sxs-lookup"><span data-stu-id="c0112-306">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="c0112-307">Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt</span><span class="sxs-lookup"><span data-stu-id="c0112-307">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="c0112-308">NoAction</span><span class="sxs-lookup"><span data-stu-id="c0112-308">NoAction</span></span>          | <span data-ttu-id="c0112-309">Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt</span><span class="sxs-lookup"><span data-stu-id="c0112-309">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="c0112-310">Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt</span><span class="sxs-lookup"><span data-stu-id="c0112-310">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="c0112-311">SetNull</span><span class="sxs-lookup"><span data-stu-id="c0112-311">SetNull</span></span>           | <span data-ttu-id="c0112-312">Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt</span><span class="sxs-lookup"><span data-stu-id="c0112-312">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="c0112-313">Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt</span><span class="sxs-lookup"><span data-stu-id="c0112-313">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="c0112-314">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="c0112-314">ClientSetNull</span></span>     | <span data-ttu-id="c0112-315">Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt</span><span class="sxs-lookup"><span data-stu-id="c0112-315">Dependent FKs set to null by EF Core</span></span>     | <span data-ttu-id="c0112-316">Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt</span><span class="sxs-lookup"><span data-stu-id="c0112-316">Dependent FKs set to null by EF Core</span></span>
| <span data-ttu-id="c0112-317">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="c0112-317">ClientCascade</span></span>     | <span data-ttu-id="c0112-318">Abhängige Entitäten werden von EF Core gelöscht</span><span class="sxs-lookup"><span data-stu-id="c0112-318">Dependents deleted by EF Core</span></span>            | <span data-ttu-id="c0112-319">Abhängige Entitäten werden von EF Core gelöscht</span><span class="sxs-lookup"><span data-stu-id="c0112-319">Dependents deleted by EF Core</span></span>
| <span data-ttu-id="c0112-320">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="c0112-320">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="c0112-321">Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt</span><span class="sxs-lookup"><span data-stu-id="c0112-321">Dependent FKs set to null by EF Core</span></span>

<span data-ttu-id="c0112-322">Hinweise:</span><span class="sxs-lookup"><span data-stu-id="c0112-322">Notes:</span></span>

- <span data-ttu-id="c0112-323">Der Standardwert für optionale Beziehungen wie diese lautet: `ClientSetNull`.</span><span class="sxs-lookup"><span data-stu-id="c0112-323">The default for optional relationships like this is `ClientSetNull`.</span></span>
- <span data-ttu-id="c0112-324">Abhängige/untergeordnete Entitäten werden nie gelöscht, es sei denn, `Cascade` oder `ClientCascade` ist konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c0112-324">Dependents/children are never deleted unless `Cascade` or `ClientCascade` are configured.</span></span>
- <span data-ttu-id="c0112-325">Alle anderen Werte führen dazu, dass die abhängigen Fremdschlüssel von EF Core auf NULL festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c0112-325">All other values cause the dependent FKs to be set to null by EF Core...</span></span>
  - <span data-ttu-id="c0112-326">Eine Ausnahme hierfür ist der Wert `ClientNoAction`, der EF Core anweist, keine Änderungen an den Fremdschlüsseln von abhängigen/untergeordneten Entitäten vorzunehmen, wenn das Prinzipal bzw. die übergeordnete Entität gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-326">...except `ClientNoAction` which tells EF Core not to touch the foreign keys of dependents/children when the principal/parent is deleted.</span></span> <span data-ttu-id="c0112-327">Die Datenbank löst daher eine Ausnahme aus, die von SaveChanges in `DbUpdateException` umschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="c0112-327">The database therefore throws an exception, which is wrapped as a `DbUpdateException` by SaveChanges.</span></span>

#### <a name="optional-relationship-with-dependentschildren-not-loaded"></a><span data-ttu-id="c0112-328">Optionale Beziehung mit nicht geladenen abhängigen/untergeordneten Entitäten</span><span class="sxs-lookup"><span data-stu-id="c0112-328">Optional relationship with dependents/children not loaded</span></span>

| <span data-ttu-id="c0112-329">DeleteBehavior</span><span class="sxs-lookup"><span data-stu-id="c0112-329">DeleteBehavior</span></span>    | <span data-ttu-id="c0112-330">Bei Löschung des Prinzipals bzw. der übergeordneten Entität</span><span class="sxs-lookup"><span data-stu-id="c0112-330">On deleting principal/parent</span></span>             | <span data-ttu-id="c0112-331">Bei Trennung der Beziehung zum Prinzipal bzw. zur übergeordneten Entität</span><span class="sxs-lookup"><span data-stu-id="c0112-331">On severing from principal/parent</span></span>
|:------------------|------------------------------------------|----------------------------------------
| <span data-ttu-id="c0112-332">Cascade</span><span class="sxs-lookup"><span data-stu-id="c0112-332">Cascade</span></span>           | <span data-ttu-id="c0112-333">Abhängige Entitäten werden von der Datenbank gelöscht</span><span class="sxs-lookup"><span data-stu-id="c0112-333">Dependents deleted by database</span></span>           | <span data-ttu-id="c0112-334">–</span><span class="sxs-lookup"><span data-stu-id="c0112-334">N/A</span></span>
| <span data-ttu-id="c0112-335">Einschränken</span><span class="sxs-lookup"><span data-stu-id="c0112-335">Restrict</span></span>          | `DbUpdateException`                      | <span data-ttu-id="c0112-336">–</span><span class="sxs-lookup"><span data-stu-id="c0112-336">N/A</span></span>
| <span data-ttu-id="c0112-337">NoAction</span><span class="sxs-lookup"><span data-stu-id="c0112-337">NoAction</span></span>          | `DbUpdateException`                      | <span data-ttu-id="c0112-338">–</span><span class="sxs-lookup"><span data-stu-id="c0112-338">N/A</span></span>
| <span data-ttu-id="c0112-339">SetNull</span><span class="sxs-lookup"><span data-stu-id="c0112-339">SetNull</span></span>           | <span data-ttu-id="c0112-340">Abhängige Fremdschlüssel werden von der Datenbank auf NULL festgelegt</span><span class="sxs-lookup"><span data-stu-id="c0112-340">Dependent FKs set to null by database</span></span>    | <span data-ttu-id="c0112-341">–</span><span class="sxs-lookup"><span data-stu-id="c0112-341">N/A</span></span>
| <span data-ttu-id="c0112-342">ClientSetNull</span><span class="sxs-lookup"><span data-stu-id="c0112-342">ClientSetNull</span></span>     | `DbUpdateException`                      | <span data-ttu-id="c0112-343">–</span><span class="sxs-lookup"><span data-stu-id="c0112-343">N/A</span></span>
| <span data-ttu-id="c0112-344">ClientCascade</span><span class="sxs-lookup"><span data-stu-id="c0112-344">ClientCascade</span></span>     | `DbUpdateException`                      | <span data-ttu-id="c0112-345">–</span><span class="sxs-lookup"><span data-stu-id="c0112-345">N/A</span></span>
| <span data-ttu-id="c0112-346">ClientNoAction</span><span class="sxs-lookup"><span data-stu-id="c0112-346">ClientNoAction</span></span>    | `DbUpdateException`                      | <span data-ttu-id="c0112-347">–</span><span class="sxs-lookup"><span data-stu-id="c0112-347">N/A</span></span>

<span data-ttu-id="c0112-348">Hinweise:</span><span class="sxs-lookup"><span data-stu-id="c0112-348">Notes:</span></span>

- <span data-ttu-id="c0112-349">Das Trennen einer Beziehung ist hier nicht zulässig, da die abhängigen/untergeordneten Entitäten nicht geladen werden.</span><span class="sxs-lookup"><span data-stu-id="c0112-349">Severing a relationship is not valid here since the dependents/children are not loaded.</span></span>
- <span data-ttu-id="c0112-350">Der Standardwert für optionale Beziehungen wie diese lautet: `ClientSetNull`.</span><span class="sxs-lookup"><span data-stu-id="c0112-350">The default for optional relationships like this is `ClientSetNull`.</span></span>
- <span data-ttu-id="c0112-351">Abhängige/untergeordnete Entitäten müssen geladen werden, um eine Datenbankausnahme zu vermeiden, es sei denn, die Datenbank wurde für kaskadierende Deletes oder NULL-Werte konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c0112-351">Dependents/children must be loaded to avoid a database exception unless the database has been configured to cascade either deletes or nulls.</span></span>
