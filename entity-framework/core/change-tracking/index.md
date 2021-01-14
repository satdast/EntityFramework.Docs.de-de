---
title: Änderungsnachverfolgung – EF Core
description: Übersicht zur Änderungsnachverfolgung für EF Core
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/index
ms.openlocfilehash: 52223e5472b09271d19ac9449a3989b4a0e277f7
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129519"
---
# <a name="change-tracking-in-ef-core"></a><span data-ttu-id="97e18-103">Änderungsnachverfolgung in EF Core</span><span class="sxs-lookup"><span data-stu-id="97e18-103">Change Tracking in EF Core</span></span>

<span data-ttu-id="97e18-104">Jede <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanz verfolgt Änderungen nach, die an Entitäten vorgenommen wurden.</span><span class="sxs-lookup"><span data-stu-id="97e18-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="97e18-105">Diese nachverfolgten Entitäten bestimmen wiederum die Änderungen an der Datenbank, wenn <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="97e18-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="97e18-106">Dieses Dokument enthält eine Übersicht über die EF Core-Änderungsnachverfolgung (Entity Framework Core) und deren Beziehung zu Abfragen und Updates.</span><span class="sxs-lookup"><span data-stu-id="97e18-106">This document presents an overview of Entity Framework Core (EF Core) change tracking and how it relates to queries and updates.</span></span>

> [!TIP]
> <span data-ttu-id="97e18-107">Sie können den gesamten Code in diesem Dokument ausführen und debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span><span class="sxs-lookup"><span data-stu-id="97e18-107">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span></span>

> [!TIP]
> <span data-ttu-id="97e18-108">Der Einfachheit halber werden in diesem Dokument synchrone Methoden wie z. B. <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> anstatt ihrer asynchronen Entsprechungen wie <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> verwendet und darauf verwiesen.</span><span class="sxs-lookup"><span data-stu-id="97e18-108">For simplicity, this document uses and references synchronous methods such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> rather their async equivalents such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>.</span></span> <span data-ttu-id="97e18-109">Das Aufrufen und Warten auf die asynchrone Methode kann ersetzt werden, sofern nicht anders angegeben.</span><span class="sxs-lookup"><span data-stu-id="97e18-109">Calling and awaiting the async method can be substituted unless otherwise noted.</span></span>

## <a name="how-to-track-entities"></a><span data-ttu-id="97e18-110">Vorgehensweise: Nachverfolgen von Entitäten</span><span class="sxs-lookup"><span data-stu-id="97e18-110">How to track entities</span></span>

<span data-ttu-id="97e18-111">Entitätsinstanzen werden nachverfolgt, wenn auf sie Folgendes zutrifft:</span><span class="sxs-lookup"><span data-stu-id="97e18-111">Entity instances become tracked when they are:</span></span>

- <span data-ttu-id="97e18-112">Zurückgegeben von einer Abfrage der Datenbank</span><span class="sxs-lookup"><span data-stu-id="97e18-112">Returned from a query executed against the database</span></span>
- <span data-ttu-id="97e18-113">Explizit durch `Add`, `Attach`, `Update` oder ähnliche Methoden DbContext angefügt</span><span class="sxs-lookup"><span data-stu-id="97e18-113">Explicitly attached to the DbContext by `Add`, `Attach`, `Update`, or similar methods</span></span>
- <span data-ttu-id="97e18-114">Erkannt als neue, mit vorhandenen nachverfolgten Entitäten verbundene Entitäten</span><span class="sxs-lookup"><span data-stu-id="97e18-114">Detected as new entities connected to existing tracked entities</span></span>

<span data-ttu-id="97e18-115">Entitätsinstanzen werden in folgenden Fällen nicht mehr nachverfolgt:</span><span class="sxs-lookup"><span data-stu-id="97e18-115">Entity instances are no longer tracked when:</span></span>

- <span data-ttu-id="97e18-116">DbContext wurde verworfen</span><span class="sxs-lookup"><span data-stu-id="97e18-116">The DbContext is disposed</span></span>
- <span data-ttu-id="97e18-117">Die Änderungsnachverfolgung ist deaktiviert (EF Core 5.0 und höher)</span><span class="sxs-lookup"><span data-stu-id="97e18-117">The change tracker is cleared (EF Core 5.0 and later)</span></span>
- <span data-ttu-id="97e18-118">Die Entitäten sind explizit getrennt</span><span class="sxs-lookup"><span data-stu-id="97e18-118">The entities are explicitly detached</span></span>

<span data-ttu-id="97e18-119">DbContext ist dazu konzipiert, eine kurzlebige Arbeitseinheit darzustellen, wie in [DbContext-Lebensdauer, -Konfiguration und -Initialisierung](xref:core/dbcontext-configuration/index) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="97e18-119">DbContext is designed to represent a short-lived unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="97e18-120">Dies bedeutet, dass das Verwerfen von DbContext _die normale Art und Weise_ ist, die Nachverfolgung von Entitäten zu beenden.</span><span class="sxs-lookup"><span data-stu-id="97e18-120">This means that disposing the DbContext is _the normal way_ to stop tracking entities.</span></span> <span data-ttu-id="97e18-121">Anders ausgedrückt, die Lebensdauer einer DbContext-Instanz sollte so aussehen:</span><span class="sxs-lookup"><span data-stu-id="97e18-121">In other words, the lifetime of a DbContext should be:</span></span>

1. <span data-ttu-id="97e18-122">Erstellen der DbContext-Instanz</span><span class="sxs-lookup"><span data-stu-id="97e18-122">Create the DbContext instance</span></span>
2. <span data-ttu-id="97e18-123">Nachverfolgen einiger Entitäten</span><span class="sxs-lookup"><span data-stu-id="97e18-123">Track some entities</span></span>
3. <span data-ttu-id="97e18-124">Vornehmen von Änderungen an den Entitäten</span><span class="sxs-lookup"><span data-stu-id="97e18-124">Make some changes to the entities</span></span>
4. <span data-ttu-id="97e18-125">Aufrufen von SaveChanges zum Aktualisieren der Datenbank</span><span class="sxs-lookup"><span data-stu-id="97e18-125">Call SaveChanges to update the database</span></span>
5. <span data-ttu-id="97e18-126">Verwerfen der DbContext-Instanz</span><span class="sxs-lookup"><span data-stu-id="97e18-126">Dispose the DbContext instance</span></span>

> [!TIP]
> <span data-ttu-id="97e18-127">Es ist nicht erforderlich, die Änderungsnachverfolgung zu löschen oder Entitätsinstanzen explizit zu trennen, wenn Sie derart vorgehen.</span><span class="sxs-lookup"><span data-stu-id="97e18-127">It is not necessary to clear the change tracker or explicitly detach entity instances when taking this approach.</span></span> <span data-ttu-id="97e18-128">Wenn Sie jedoch Entitäten trennen müssen, ist das Aufrufen von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> effizienter, als Entitäten einzeln zu trennen.</span><span class="sxs-lookup"><span data-stu-id="97e18-128">However, if you do need to detach entities, then calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> is more efficient than detaching entities one-by-one.</span></span>

## <a name="entity-states"></a><span data-ttu-id="97e18-129">Status von Entitäten</span><span class="sxs-lookup"><span data-stu-id="97e18-129">Entity states</span></span>

<span data-ttu-id="97e18-130">Jede Entität ist einem bestimmten <xref:Microsoft.EntityFrameworkCore.EntityState> zugeordnet:</span><span class="sxs-lookup"><span data-stu-id="97e18-130">Every entity is is associated with a given <xref:Microsoft.EntityFrameworkCore.EntityState>:</span></span>

- <span data-ttu-id="97e18-131">`Detached` Entitäten werden von <xref:Microsoft.EntityFrameworkCore.DbContext> nicht nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="97e18-131">`Detached` entities are not being tracked by the <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
- <span data-ttu-id="97e18-132">`Added` Entitäten sind neu und wurden noch nicht in die Datenbank eingefügt.</span><span class="sxs-lookup"><span data-stu-id="97e18-132">`Added` entities are new and have not yet been inserted into the database.</span></span> <span data-ttu-id="97e18-133">Dies bedeutet, dass sie eingefügt werden, wenn <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="97e18-133">This means they will be inserted when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>
- <span data-ttu-id="97e18-134">`Unchanged` Entitäten wurden _nicht_ geändert, seit sie von der Datenbank abgefragt wurden.</span><span class="sxs-lookup"><span data-stu-id="97e18-134">`Unchanged` entities have _not_ been changed since they were queried from the database.</span></span> <span data-ttu-id="97e18-135">Alle von Abfragen zurückgegebenen Entitäten sind anfänglich in diesem Zustand.</span><span class="sxs-lookup"><span data-stu-id="97e18-135">All entities returned from queries are initially in this state.</span></span>
- <span data-ttu-id="97e18-136">`Modified` Entitäten wurden geändert, seit sie von der Datenbank abgefragt wurden.</span><span class="sxs-lookup"><span data-stu-id="97e18-136">`Modified` entities have been changed since they were queried from the database.</span></span> <span data-ttu-id="97e18-137">Dies bedeutet, dass sie aktualisiert werden, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="97e18-137">This means they will be updated when SaveChanges is called.</span></span>
- <span data-ttu-id="97e18-138">`Deleted` Entitäten sind in der Datenbank vorhanden, werden jedoch als gelöscht markiert, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="97e18-138">`Deleted` entities exist in the database, but are marked to be deleted when SaveChanges is called.</span></span>

<span data-ttu-id="97e18-139">EF Core verfolgt Änderungen auf der Eigenschaftenebene nach.</span><span class="sxs-lookup"><span data-stu-id="97e18-139">EF Core tracks changes at the property level.</span></span> <span data-ttu-id="97e18-140">Wenn z. B. nur ein einzelner Eigenschaftswert geändert wird, ändert ein Datenbankupdate nur diesen Wert.</span><span class="sxs-lookup"><span data-stu-id="97e18-140">For example, if only a single property value is modified, then a database update will change only that value.</span></span> <span data-ttu-id="97e18-141">Eigenschaften können jedoch nur als geändert gekennzeichnet werden, wenn sich die Entität selbst im Zustand „Geändert“ befindet.</span><span class="sxs-lookup"><span data-stu-id="97e18-141">However, properties can only be marked as modified when the entity itself is in the Modified state.</span></span> <span data-ttu-id="97e18-142">(Aus alternativer Sicht bedeutet der Zustand „Geändert“, dass mindestens ein Eigenschaftswert als geändert markiert wurde.)</span><span class="sxs-lookup"><span data-stu-id="97e18-142">(Or, from an alternate perspective, the Modified state means that at least one property value has been marked as modified.)</span></span>

<span data-ttu-id="97e18-143">In der folgenden Tabelle sind die verschiedenen Status zusammengefasst:</span><span class="sxs-lookup"><span data-stu-id="97e18-143">The following table summarizes the different states:</span></span>

| <span data-ttu-id="97e18-144">Entitätszustand</span><span class="sxs-lookup"><span data-stu-id="97e18-144">Entity state</span></span>     | <span data-ttu-id="97e18-145">Nachverfolgt von DbContext</span><span class="sxs-lookup"><span data-stu-id="97e18-145">Tracked by DbContext</span></span> | <span data-ttu-id="97e18-146">In Datenbank vorhanden</span><span class="sxs-lookup"><span data-stu-id="97e18-146">Exists in database</span></span> | <span data-ttu-id="97e18-147">Geänderte Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="97e18-147">Properties modified</span></span> | <span data-ttu-id="97e18-148">Aktion bei SaveChanges</span><span class="sxs-lookup"><span data-stu-id="97e18-148">Action on SaveChanges</span></span>
|:-----------------|----------------------|--------------------|---------------------|-----------------------
| `Detached`       | <span data-ttu-id="97e18-149">Nein</span><span class="sxs-lookup"><span data-stu-id="97e18-149">No</span></span>                   | -                  | -                   | -
| `Added`          | <span data-ttu-id="97e18-150">Ja</span><span class="sxs-lookup"><span data-stu-id="97e18-150">Yes</span></span>                  | <span data-ttu-id="97e18-151">Nein</span><span class="sxs-lookup"><span data-stu-id="97e18-151">No</span></span>                 | -                   | <span data-ttu-id="97e18-152">Einfügen</span><span class="sxs-lookup"><span data-stu-id="97e18-152">Insert</span></span>
| `Unchanged`      | <span data-ttu-id="97e18-153">Ja</span><span class="sxs-lookup"><span data-stu-id="97e18-153">Yes</span></span>                  | <span data-ttu-id="97e18-154">Ja</span><span class="sxs-lookup"><span data-stu-id="97e18-154">Yes</span></span>                | <span data-ttu-id="97e18-155">Nein</span><span class="sxs-lookup"><span data-stu-id="97e18-155">No</span></span>                  | -
| `Modified`       | <span data-ttu-id="97e18-156">Ja</span><span class="sxs-lookup"><span data-stu-id="97e18-156">Yes</span></span>                  | <span data-ttu-id="97e18-157">Ja</span><span class="sxs-lookup"><span data-stu-id="97e18-157">Yes</span></span>                | <span data-ttu-id="97e18-158">Ja</span><span class="sxs-lookup"><span data-stu-id="97e18-158">Yes</span></span>                 | <span data-ttu-id="97e18-159">Aktualisieren</span><span class="sxs-lookup"><span data-stu-id="97e18-159">Update</span></span>
| `Deleted`        | <span data-ttu-id="97e18-160">Ja</span><span class="sxs-lookup"><span data-stu-id="97e18-160">Yes</span></span>                  | <span data-ttu-id="97e18-161">Ja</span><span class="sxs-lookup"><span data-stu-id="97e18-161">Yes</span></span>                | -                   | <span data-ttu-id="97e18-162">Löschen</span><span class="sxs-lookup"><span data-stu-id="97e18-162">Delete</span></span>

> [!NOTE]
> <span data-ttu-id="97e18-163">In diesem Text werden zur Verdeutlichung Begriffe für relationale Datenbanken verwendet.</span><span class="sxs-lookup"><span data-stu-id="97e18-163">This text uses relational database terms for clarity.</span></span> <span data-ttu-id="97e18-164">NoSQL-Datenbanken unterstützen in der Regel ähnliche Vorgänge, aber möglicherweise mit anderen Namen.</span><span class="sxs-lookup"><span data-stu-id="97e18-164">NoSQL databases typically support similar operations but possibly with different names.</span></span> <span data-ttu-id="97e18-165">Nähere Informationen dazu finden Sie in der Dokumentation Ihres Datenbankanbieters.</span><span class="sxs-lookup"><span data-stu-id="97e18-165">Consult your database provider documentation for more information.</span></span>

## <a name="tracking-from-queries"></a><span data-ttu-id="97e18-166">Nachverfolgung von Abfragen</span><span class="sxs-lookup"><span data-stu-id="97e18-166">Tracking from queries</span></span>

<span data-ttu-id="97e18-167">EF Core-Änderungsnachverfolgung funktioniert am besten, wenn dieselbe <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanz verwendet wird, um Entitäten abzufragen und sie durch Aufrufen von <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="97e18-167">EF Core change tracking works best when the same <xref:Microsoft.EntityFrameworkCore.DbContext> instance is used to both query for entities and update them by calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="97e18-168">Der Grund hierfür ist, dass EF Core den Status von abgefragten Entitäten automatisch nachverfolgt und dann alle Änderungen erkennt, die an diesen Entitäten vorgenommen werden, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="97e18-168">This is because EF Core automatically tracks the state of queried entities and then detects any changes made to these entities when SaveChanges is called.</span></span>

<span data-ttu-id="97e18-169">Dieser Ansatz bietet mehrere Vorteile gegenüber der [expliziten Nachverfolgung von Entitätsinstanzen](xref:core/change-tracking/explicit-tracking):</span><span class="sxs-lookup"><span data-stu-id="97e18-169">This approach has several advantages over [explicitly tracking entity instances](xref:core/change-tracking/explicit-tracking):</span></span>

- <span data-ttu-id="97e18-170">Er ist einfach.</span><span class="sxs-lookup"><span data-stu-id="97e18-170">It is simple.</span></span> <span data-ttu-id="97e18-171">Entitätszustände müssen selten explizit bearbeitet werden, weil EF Core die Zustandsänderungen übernimmt.</span><span class="sxs-lookup"><span data-stu-id="97e18-171">Entity states rarely need to be manipulated explicitly--EF Core takes care of state changes.</span></span>
- <span data-ttu-id="97e18-172">Updates sind auf die Werte beschränkt, die tatsächlich geändert wurden.</span><span class="sxs-lookup"><span data-stu-id="97e18-172">Updates are limited to only those values that have actually changed.</span></span>
- <span data-ttu-id="97e18-173">Die Werte von [Schatteneigenschaften](xref:core/modeling/shadow-properties) werden beibehalten und bei Bedarf verwendet.</span><span class="sxs-lookup"><span data-stu-id="97e18-173">The values of [shadow properties](xref:core/modeling/shadow-properties) are preserved and used as needed.</span></span> <span data-ttu-id="97e18-174">Dies ist besonders relevant, wenn Fremdschlüssel im Schattenzustand gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="97e18-174">This is especially relevant when foreign keys are stored in shadow state.</span></span>
- <span data-ttu-id="97e18-175">Die ursprünglichen Eigenschaftswerte werden automatisch beibehalten und für effiziente Updates verwendet.</span><span class="sxs-lookup"><span data-stu-id="97e18-175">The original values of properties are preserved automatically and used for efficient updates.</span></span>

## <a name="simple-query-and-update"></a><span data-ttu-id="97e18-176">Einfache Abfrage und Aktualisierung</span><span class="sxs-lookup"><span data-stu-id="97e18-176">Simple query and update</span></span>

<span data-ttu-id="97e18-177">Stellen Sie sich beispielsweise ein einfaches Blog/Beitrag-Modell vor:</span><span class="sxs-lookup"><span data-stu-id="97e18-177">For example, consider a simple blog/posts model:</span></span>

<!--
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Model)]

<span data-ttu-id="97e18-178">Wir können dieses Modell verwenden, um Blogs und Beiträge abzufragen und dann einige Updates der Datenbank vorzunehmen:</span><span class="sxs-lookup"><span data-stu-id="97e18-178">We can use this model to query for blogs and posts and then make some updates to the database:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            blog.Name = ".NET Blog (Updated!)";

            foreach (var post in blog.Posts.Where(e => !e.Title.Contains("5.0")))
            {
                post.Title = post.Title.Replace("5", "5.0");
            }

            context.SaveChanges();
-->
[!code-csharp[Simple_query_and_update_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_1)]

<span data-ttu-id="97e18-179">Das Aufrufen von SaveChanges führt zu den folgenden Datenbankupdates, wobei SQLite als Beispieldatenbank verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="97e18-179">Calling SaveChanges results in the following database updates, using SQLite as an example database:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0='Announcing F# 5.0' (Size = 17)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "Title" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

<span data-ttu-id="97e18-180">Die [Debugansicht der Änderungsnachverfolgung](xref:core/change-tracking/debug-views) ist hervorragend geeignet, um zu visualisieren, welche Entitäten nachverfolgt werden und wie ihre Zustände sind.</span><span class="sxs-lookup"><span data-stu-id="97e18-180">The [change tracker debug view](xref:core/change-tracking/debug-views) is a great way visualize which entities are being tracked and what their states are.</span></span> <span data-ttu-id="97e18-181">Fügen Sie beispielsweise den folgenden Code in das obige Beispiel ein, bevor Sie SaveChanges aufrufen:</span><span class="sxs-lookup"><span data-stu-id="97e18-181">For example, inserting the following code into the sample above before calling SaveChanges:</span></span>

<!--
                context.ChangeTracker.DetectChanges();
                Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Simple_query_and_update_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_2)]

<span data-ttu-id="97e18-182">Generiert folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="97e18-182">Generates the following output:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="97e18-183">Beachten Sie insbesondere:</span><span class="sxs-lookup"><span data-stu-id="97e18-183">Notice specifically:</span></span>

- <span data-ttu-id="97e18-184">Die `Blog.Name`-Eigenschaft wird als geändert markiert (`Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'`), und dies führt dazu, dass sich der Blog im `Modified`-Zustand befindet.</span><span class="sxs-lookup"><span data-stu-id="97e18-184">The `Blog.Name` property is marked as modified (`Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'`), and this results in the blog being in the `Modified` state.</span></span>
- <span data-ttu-id="97e18-185">Die `Post.Title`-Eigenschaft von Beitrag 2 wird als geändert markiert (`Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'`), und dies führt dazu, dass sich der Beitrag im `Modified`-Zustand befindet.</span><span class="sxs-lookup"><span data-stu-id="97e18-185">The `Post.Title` property of post 2 is marked as modified (`Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'`), and this results in this post being in the `Modified` state.</span></span>
- <span data-ttu-id="97e18-186">Die anderen Eigenschaftswerte von Beitrag 2 wurden nicht geändert und sind daher nicht als geändert gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="97e18-186">The other property values of post 2 have not changed and are therefore not marked as modified.</span></span> <span data-ttu-id="97e18-187">Aus diesem Grund sind diese Werte nicht im Datenbankupdate enthalten.</span><span class="sxs-lookup"><span data-stu-id="97e18-187">This is why these values are not included in the database update.</span></span>
- <span data-ttu-id="97e18-188">Der andere Beitrag wurde in keiner Weise geändert.</span><span class="sxs-lookup"><span data-stu-id="97e18-188">The other post was not modified in any way.</span></span> <span data-ttu-id="97e18-189">Der Grund dafür ist, dass er sich noch im `Unchanged`-Zustand befindet und nicht im Datenbankupdate enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="97e18-189">This is why it is still in the `Unchanged` state and is not included in the database update.</span></span>

## <a name="query-then-insert-update-and-delete"></a><span data-ttu-id="97e18-190">Abfragen und dann einfügen, aktualisieren und löschen</span><span class="sxs-lookup"><span data-stu-id="97e18-190">Query then insert, update, and delete</span></span>

<span data-ttu-id="97e18-191">Updates wie im vorherigen Beispiel können mit Einfügungen und Löschungen in derselben Arbeitseinheit kombiniert werden.</span><span class="sxs-lookup"><span data-stu-id="97e18-191">Updates like those in the previous example can be combined with inserts and deletes in the same unit-of-work.</span></span> <span data-ttu-id="97e18-192">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="97e18-192">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Modify property values
            blog.Name = ".NET Blog (Updated!)";

            // Insert a new Post
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            // Mark an existing Post as Deleted
            var postToDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            context.Remove(postToDelete);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[Query_then_insert_update_and_delete_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Query_then_insert_update_and_delete_1)]

<span data-ttu-id="97e18-193">In diesem Beispiel:</span><span class="sxs-lookup"><span data-stu-id="97e18-193">In this example:</span></span>

- <span data-ttu-id="97e18-194">Ein Blog und zugehörige Beiträge werden von der Datenbank abgefragt und nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="97e18-194">A blog and related posts are queried from the database and tracked</span></span>
- <span data-ttu-id="97e18-195">Die `Blog.Name`-Eigenschaft wird geändert.</span><span class="sxs-lookup"><span data-stu-id="97e18-195">The `Blog.Name` property is changed</span></span>
- <span data-ttu-id="97e18-196">Der Sammlung vorhandener Beiträge für den Blog wird ein neuer Beitrag hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="97e18-196">A new post is added to the collection of existing posts for the blog</span></span>
- <span data-ttu-id="97e18-197">Ein vorhandener Beitrag ist zum Löschen durch Aufrufen von <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> markiert.</span><span class="sxs-lookup"><span data-stu-id="97e18-197">An existing post is marked for deletion by calling <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType></span></span>

<span data-ttu-id="97e18-198">Schauen Sie sich die [Debugansicht der Änderungsnachverfolgung](xref:core/change-tracking/debug-views) erneut an, bevor Sie SaveChanges aufrufen, um zu sehen, wie EF Core diese Änderungen nachverfolgt:</span><span class="sxs-lookup"><span data-stu-id="97e18-198">Looking again at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows how EF Core is tracking these changes:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}, {Id: -2147482638}]
Post {Id: -2147482638} Added
  Id: -2147482638 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="97e18-199">Beachten Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="97e18-199">Notice that:</span></span>

- <span data-ttu-id="97e18-200">Der Blog ist als `Modified` markiert.</span><span class="sxs-lookup"><span data-stu-id="97e18-200">The blog is marked as `Modified`.</span></span> <span data-ttu-id="97e18-201">Dadurch wird ein Datenbankupdate generiert.</span><span class="sxs-lookup"><span data-stu-id="97e18-201">This will generate a database update.</span></span>
- <span data-ttu-id="97e18-202">Beitrag 2 wird als `Deleted` gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="97e18-202">Post 2 is marked as `Deleted`.</span></span> <span data-ttu-id="97e18-203">Dadurch wird eine Datenbanklöschung generiert.</span><span class="sxs-lookup"><span data-stu-id="97e18-203">This will generate a database delete.</span></span>
- <span data-ttu-id="97e18-204">Ein neuer Beitrag mit einer temporären ID ist mit Blog 1 verknüpft und wird als `Added` gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="97e18-204">A new post with a temporary ID is associated with blog 1 and is marked as `Added`.</span></span> <span data-ttu-id="97e18-205">Dadurch wird eine Datenbankeinfügung generiert.</span><span class="sxs-lookup"><span data-stu-id="97e18-205">This will generate a database insert.</span></span>

<span data-ttu-id="97e18-206">Dies führt zu den folgenden Datenbankbefehlen (bei Verwendung von SQLite), wenn SaveChanges aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="97e18-206">This results in the following database commands (using SQLite) when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 was released recently and has come with many...' (Size = 56), @p2='What's next for System.Text.Json?' (Size = 33)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="97e18-207">Weitere Informationen zum Einfügen und Löschen von Entitäten finden Sie unter [Explizites Nachverfolgen von Entitäten](xref:core/change-tracking/explicit-tracking).</span><span class="sxs-lookup"><span data-stu-id="97e18-207">See [Explicitly Tracking Entities](xref:core/change-tracking/explicit-tracking) for more information on inserting and deleting entities.</span></span> <span data-ttu-id="97e18-208">Weitere Informationen zur automatischen Erkennung von Änderungen wie dieser durch EF Core finden Sie unter [Änderungserkennung und Benachrichtigungen](xref:core/change-tracking/change-detection).</span><span class="sxs-lookup"><span data-stu-id="97e18-208">See [Change Detection and Notifications](xref:core/change-tracking/change-detection) for more information on how EF Core automatically detects changes like this.</span></span>

> [!TIP]
> <span data-ttu-id="97e18-209">Rufen Sie <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> auf, um zu bestimmen, ob Änderungen vorgenommen wurden, die SaveChanges veranlassen, Datenbankupdates vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="97e18-209">Call <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> to determine whether any changes have been made that will cause SaveChanges to make updates to the database.</span></span> <span data-ttu-id="97e18-210">Wenn HasChanges „false“ zurückgibt, wird SaveChanges nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="97e18-210">If HasChanges return false, then SaveChanges will be a no-op.</span></span>
