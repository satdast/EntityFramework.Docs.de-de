---
title: .Net-Ereignisse-EF Core
description: Durch EF Core definierte .net-Ereignisse
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/events
ms.openlocfilehash: 51c0bba5cf25e1d9ddd1fd9aebea50b9a03481a3
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635691"
---
# <a name="net-events-in-ef-core"></a><span data-ttu-id="305b2-103">.Net-Ereignisse in EF Core</span><span class="sxs-lookup"><span data-stu-id="305b2-103">.NET Events in EF Core</span></span>

> [!TIP]  
> <span data-ttu-id="305b2-104">Sie können [das Ereignis Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) von GitHub herunterladen.</span><span class="sxs-lookup"><span data-stu-id="305b2-104">You can [download the events sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) from GitHub.</span></span>

<span data-ttu-id="305b2-105">Entity Framework Core (EF Core) macht [.net-Ereignisse](/dotnet/standard/events/) verfügbar, die als Rückrufe fungieren, wenn bestimmte Dinge im EF Core Code auftreten.</span><span class="sxs-lookup"><span data-stu-id="305b2-105">Entity Framework Core (EF Core) exposes [.NET events](/dotnet/standard/events/) to act as callbacks when certain things happen in the EF Core code.</span></span> <span data-ttu-id="305b2-106">Ereignisse sind einfacher als [Interceptors](xref:core/logging-events-diagnostics/interceptors) und ermöglichen eine flexiblere Registrierung.</span><span class="sxs-lookup"><span data-stu-id="305b2-106">Events are simpler than [interceptors](xref:core/logging-events-diagnostics/interceptors) and allow more flexible registration.</span></span> <span data-ttu-id="305b2-107">Sie sind jedoch nur synchron und können daher keine nicht blockierenden asynchronen E/A-Vorgänge durchführen.</span><span class="sxs-lookup"><span data-stu-id="305b2-107">However, they are sync only and so cannot perform non-blocking async I/O.</span></span>

<span data-ttu-id="305b2-108">Ereignisse werden pro `DbContext` Instanz registriert.</span><span class="sxs-lookup"><span data-stu-id="305b2-108">Events are registered per `DbContext` instance.</span></span> <span data-ttu-id="305b2-109">Verwenden Sie einen [Diagnoselistener](xref:core/logging-events-diagnostics/diagnostic-listeners), um dieselben Informationen abzurufen, jedoch für alle DbContext-Instanzen im Prozess.</span><span class="sxs-lookup"><span data-stu-id="305b2-109">Use a [diagnostic listener](xref:core/logging-events-diagnostics/diagnostic-listeners) to get the same information but for all DbContext instances in the process.</span></span>

## <a name="events-raised-by-ef-core"></a><span data-ttu-id="305b2-110">Ereignisse, die von EF Core ausgelöst werden</span><span class="sxs-lookup"><span data-stu-id="305b2-110">Events raised by EF Core</span></span>

<span data-ttu-id="305b2-111">Die folgenden Ereignisse werden von EF Core ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="305b2-111">The following events are raised by EF Core:</span></span>

| <span data-ttu-id="305b2-112">Ereignis</span><span class="sxs-lookup"><span data-stu-id="305b2-112">Event</span></span> | <span data-ttu-id="305b2-113">Eingeführt in Version</span><span class="sxs-lookup"><span data-stu-id="305b2-113">Version introduced</span></span> | <span data-ttu-id="305b2-114">Wenn ausgelöst</span><span class="sxs-lookup"><span data-stu-id="305b2-114">When raised</span></span>
|:------|--------------------|-------
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> | <span data-ttu-id="305b2-115">5.0</span><span class="sxs-lookup"><span data-stu-id="305b2-115">5.0</span></span> | <span data-ttu-id="305b2-116">Am Anfang von <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> oder <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="305b2-116">At the start of <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SavedChanges?displayProperty=nameWithType> | <span data-ttu-id="305b2-117">5.0</span><span class="sxs-lookup"><span data-stu-id="305b2-117">5.0</span></span> | <span data-ttu-id="305b2-118">Am Ende eines erfolgreichen <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> oder <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="305b2-118">At the end of a successful <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesFailed?displayProperty=nameWithType> | <span data-ttu-id="305b2-119">5.0</span><span class="sxs-lookup"><span data-stu-id="305b2-119">5.0</span></span> | <span data-ttu-id="305b2-120">Am Ende einer fehlgeschlagenen <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> oder <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span><span class="sxs-lookup"><span data-stu-id="305b2-120">At the end of a failed <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A></span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> | <span data-ttu-id="305b2-121">2.1</span><span class="sxs-lookup"><span data-stu-id="305b2-121">2.1</span></span> | <span data-ttu-id="305b2-122">Wenn eine Entität vom Kontext nachverfolgt wird</span><span class="sxs-lookup"><span data-stu-id="305b2-122">When an entity is tracked by the context</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> | <span data-ttu-id="305b2-123">2.1</span><span class="sxs-lookup"><span data-stu-id="305b2-123">2.1</span></span> | <span data-ttu-id="305b2-124">Wenn eine nach verfolgte Entität ihren Zustand ändert</span><span class="sxs-lookup"><span data-stu-id="305b2-124">When a tracked entity changes its state</span></span>

### <a name="example-timestamp-state-changes"></a><span data-ttu-id="305b2-125">Beispiel: Timestamp-Zustandsänderungen</span><span class="sxs-lookup"><span data-stu-id="305b2-125">Example: Timestamp state changes</span></span>

<span data-ttu-id="305b2-126">Jede Entität, die von einem dbcontext nachverfolgt wird, hat eine <xref:Microsoft.EntityFrameworkCore.EntityState></span><span class="sxs-lookup"><span data-stu-id="305b2-126">Each entity tracked by a DbContext has an <xref:Microsoft.EntityFrameworkCore.EntityState>.</span></span> <span data-ttu-id="305b2-127">Der-Status gibt beispielsweise an `Added` , dass die Entität in die Datenbank eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="305b2-127">For example, the `Added` state indicates that the entity will be inserted into the database.</span></span>

<span data-ttu-id="305b2-128">In diesem Beispiel werden das <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> -Ereignis und das- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> Ereignis verwendet, um zu erkennen, wann sich ein</span><span class="sxs-lookup"><span data-stu-id="305b2-128">This example uses the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> events to detect when an entity changes state.</span></span> <span data-ttu-id="305b2-129">Anschließend wird die Entität mit der aktuellen Zeitstempel versehen, die anzeigt, wann diese Änderung eingetreten ist.</span><span class="sxs-lookup"><span data-stu-id="305b2-129">It then stamps the entity with the current time indicating when this change happened.</span></span> <span data-ttu-id="305b2-130">Dies führt zu Zeitstempel, die angeben, wann die Entität eingefügt, gelöscht und/oder zuletzt aktualisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="305b2-130">This results in timestamps indicating when the entity was inserted, deleted, and/or last updated.</span></span>

<span data-ttu-id="305b2-131">Die Entitäts Typen in diesem Beispiel implementieren eine Schnittstelle, die die Zeitstempel-Eigenschaften definiert:</span><span class="sxs-lookup"><span data-stu-id="305b2-131">The entity types in this example implement an interface that defines the timestamp properties:</span></span>

<!--
public interface IHasTimestamps
{
    DateTime? Added { get; set; }
    DateTime? Deleted { get; set; }
    DateTime? Modified { get; set; }
}
-->
[!code-csharp[IHasTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=IHasTimestamps)]

<span data-ttu-id="305b2-132">Eine Methode im dbcontext der Anwendung kann dann Zeitstempel für jede Entität festlegen, die diese Schnittstelle implementiert:</span><span class="sxs-lookup"><span data-stu-id="305b2-132">A method on the application's DbContext can then set timestamps for any entity that implements this interface:</span></span>

<!--
    private static void UpdateTimestamps(object sender, EntityEntryEventArgs e)
    {
        if (e.Entry.Entity is IHasTimestamps entityWithTimestamps)
        {
            switch (e.Entry.State)
            {
                case EntityState.Deleted:
                    entityWithTimestamps.Deleted = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for delete: {e.Entry.Entity}");
                    break;
                case EntityState.Modified:
                    entityWithTimestamps.Modified = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for update: {e.Entry.Entity}");
                    break;
                case EntityState.Added:
                    entityWithTimestamps.Added = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for insert: {e.Entry.Entity}");
                    break;
            }
        }
    }
-->
[!code-csharp[UpdateTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=UpdateTimestamps)]

<span data-ttu-id="305b2-133">Diese Methode verfügt über die entsprechende Signatur, die als Ereignishandler für das-Ereignis und das-Ereignis verwendet werden soll `Tracked` `StateChanged` .</span><span class="sxs-lookup"><span data-stu-id="305b2-133">This method has the appropriate signature to use as an event handler for both the `Tracked` and `StateChanged` events.</span></span> <span data-ttu-id="305b2-134">Der Handler ist für beide Ereignisse im dbcontext-Konstruktor registriert.</span><span class="sxs-lookup"><span data-stu-id="305b2-134">The handler is registered for both events in the DbContext constructor.</span></span> <span data-ttu-id="305b2-135">Beachten Sie, dass Ereignisse jederzeit an einen dbcontext angefügt werden können. Es ist nicht erforderlich, dass dies im kontextkonstruktor auftritt.</span><span class="sxs-lookup"><span data-stu-id="305b2-135">Note that events can be attached to a DbContext at any time; it is not required that this happen in the context constructor.</span></span>

<!--
    public BlogsContext()
    {
        ChangeTracker.StateChanged += UpdateTimestamps;
        ChangeTracker.Tracked += UpdateTimestamps;
    }
-->
[!code-csharp[ContextConstructor](../../../samples/core/Miscellaneous/Events/Program.cs?name=ContextConstructor)]

<span data-ttu-id="305b2-136">Beide Ereignisse sind erforderlich, da neue Entitäten Ereignisse auslösen, `Tracked` Wenn Sie zum ersten Mal nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="305b2-136">Both events are needed because new entities fire `Tracked` events when they are first tracked.</span></span> <span data-ttu-id="305b2-137">`StateChanged` Ereignisse werden nur für Entitäten ausgelöst, die den Zustand ändern, während Sie _bereits_ nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="305b2-137">`StateChanged` events are only fired for entities that change state while they are _already_ being tracked.</span></span>

<span data-ttu-id="305b2-138">Das [Beispiel für dieses](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) Beispiel enthält eine einfache Konsolenanwendung, die Änderungen an der Blog Datenbank vornimmt:</span><span class="sxs-lookup"><span data-stu-id="305b2-138">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) for this example contains a simple console application that makes changes to the blogging database:</span></span>

<!--
        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();
            
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Id = 1, Title = "EF Core 3.1!" },
                        new Post { Id = 2, Title = "EF Core 5.0!" }
                    }
                });

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Id = 3, Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
-->
[!code-csharp[Demonstration](../../../samples/core/Miscellaneous/Events/Program.cs?name=Demonstration)]

<span data-ttu-id="305b2-139">Die Ausgabe dieses Codes zeigt die Zustandsänderungen und die angewendeten Zeitstempel:</span><span class="sxs-lookup"><span data-stu-id="305b2-139">The output from this code shows the state changes happening and the timestamps being applied:</span></span>

```output
Stamped for insert: Blog 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 2 Added on: 10/15/2020 11:01:26 PM
Stamped for delete: Post 1 Added on: 10/15/2020 11:01:26 PM Deleted on: 10/15/2020 11:01:26 PM
Stamped for update: Blog 1 Added on: 10/15/2020 11:01:26 PM Modified on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 3 Added on: 10/15/2020 11:01:26 PM
```
