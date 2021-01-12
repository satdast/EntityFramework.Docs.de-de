---
title: Änderungs Erkennung und Benachrichtigungen-EF Core
description: Erkennen von Eigenschafts-und Beziehungs Änderungen mithilfe von DetectChanges oder Benachrichtigungen
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/change-detection
ms.openlocfilehash: 39dc66a3ba74be89d3e470cfe788a357401965d1
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129592"
---
# <a name="change-detection-and-notifications"></a><span data-ttu-id="3ab6f-103">Änderungs Erkennung und Benachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="3ab6f-103">Change Detection and Notifications</span></span>

<span data-ttu-id="3ab6f-104">Jede <xref:Microsoft.EntityFrameworkCore.DbContext> Instanz verfolgt Änderungen, die an Entitäten vorgenommen wurden.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="3ab6f-105">Diese überwachten Entitäten Steuern wiederum die Änderungen an der Datenbank, wenn <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="3ab6f-106">Dies wird in [Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index)behandelt. in diesem Dokument wird davon ausgegangen, dass Entitäts Zustände und Grundlagen der Entity Framework Core (EF Core)-Änderungs Nachverfolgung verstanden werden.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-106">This is covered in [Change Tracking in EF Core](xref:core/change-tracking/index), and this document assumes that entity states and the basics of Entity Framework Core (EF Core) change tracking are understood.</span></span>

<span data-ttu-id="3ab6f-107">Für das Nachverfolgen von Eigenschaften-und Beziehungs Änderungen ist es erforderlich, dass dbcontext diese Änderungen erkennen kann.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-107">Tracking property and relationship changes requires that the DbContext is able to detect these changes.</span></span> <span data-ttu-id="3ab6f-108">In diesem Dokument wird erläutert, wie diese Erkennung erfolgt. Außerdem wird erläutert, wie Sie Eigenschafts Benachrichtigungen oder Proxys für die Änderungs Nachverfolgung verwenden können, um eine sofortige Erkennung von Änderungen</span><span class="sxs-lookup"><span data-stu-id="3ab6f-108">This document covers how this detection happens, as well as how to use property notifications or change-tracking proxies to force immediate detection of changes.</span></span>

> [!TIP]
> <span data-ttu-id="3ab6f-109">Sie können den gesamten Code in diesem Dokument ausführen und Debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeDetectionAndNotifications).</span><span class="sxs-lookup"><span data-stu-id="3ab6f-109">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeDetectionAndNotifications).</span></span>

## <a name="snapshot-change-tracking"></a><span data-ttu-id="3ab6f-110">Änderungsnachverfolgung für Momentaufnahmen</span><span class="sxs-lookup"><span data-stu-id="3ab6f-110">Snapshot change tracking</span></span>

<span data-ttu-id="3ab6f-111">Standardmäßig erstellt EF Core eine Momentaufnahme der Eigenschaftswerte jeder Entität, wenn Sie zuerst von einer dbcontext-Instanz nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-111">By default, EF Core creates a snapshot of every entity's property values when it is first tracked by a DbContext instance.</span></span> <span data-ttu-id="3ab6f-112">Die in dieser Momentaufnahme gespeicherten Werte werden dann mit den aktuellen Werten der Entität verglichen, um zu bestimmen, welche Eigenschaftswerte geändert wurden.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-112">The values stored in this snapshot are then compared against the current values of the entity in order to determine which property values have changed.</span></span>

<span data-ttu-id="3ab6f-113">Diese Erkennung von Änderungen erfolgt, wenn SaveChanges aufgerufen wird, um sicherzustellen, dass alle geänderten Werte erkannt werden, bevor Updates an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-113">This detection of changes happens when SaveChanges is called to ensure all changed values are detected before sending updates to the database.</span></span> <span data-ttu-id="3ab6f-114">Die Erkennung von Änderungen wird jedoch auch zu anderen Zeiten durchgeführt, um sicherzustellen, dass die Anwendung mit aktuellen Überwachungsinformationen arbeitet.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-114">However, the detection of changes also happens at other times to ensure the application is working with up-to-date tracking information.</span></span> <span data-ttu-id="3ab6f-115">Die Erkennung von Änderungen kann jederzeit durch Aufrufen von erzwungen werden <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="3ab6f-115">Detection of changes can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>.</span></span>

### <a name="when-change-detection-is-needed"></a><span data-ttu-id="3ab6f-116">Wenn die Änderungs Erkennung erforderlich ist</span><span class="sxs-lookup"><span data-stu-id="3ab6f-116">When change detection is needed</span></span>

<span data-ttu-id="3ab6f-117">Die Erkennung von Änderungen wird benötigt, wenn eine Eigenschaft oder Navigation geändert wurde _, ohne EF Core zu verwenden, um diese Änderung vorzunehmen_.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-117">Detection of changes is needed when a property or navigation has been changed _without using EF Core to make this change_.</span></span> <span data-ttu-id="3ab6f-118">Sie können z. b. Blogs und Beiträge laden und dann Änderungen an diesen Entitäten vornehmen:</span><span class="sxs-lookup"><span data-stu-id="3ab6f-118">For example, consider loading blogs and posts and then making changes to these entities:</span></span>

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        blog.Name = ".NET Blog (Updated!)";

        // Add a new entity to a navigation
        blog.Posts.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many..."
        });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_1)]

<span data-ttu-id="3ab6f-119">Wenn Sie die [Debugansicht der Änderungs](xref:core/change-tracking/debug-views) Nachverfolgung vor dem Aufruf von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> anzeigen, wird angezeigt, dass die vorgenommenen Änderungen nicht erkannt wurden und daher nicht in den Entitäts Zuständen und geänderten Eigenschaften Daten widergespiegelt werden:</span><span class="sxs-lookup"><span data-stu-id="3ab6f-119">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> shows that the changes made have not been detected and hence are not reflected in the entity states and modified property data:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, <not found>]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="3ab6f-120">Insbesondere ist der Status des Blogeintrags immer noch `Unchanged` , und der neue Beitrag wird nicht als nach verfolgte Entität angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-120">Specifically, the state of the blog entry is still `Unchanged`, and the new post does not appear as a tracked entity.</span></span> <span data-ttu-id="3ab6f-121">(Die aKlugen wird bemerken, dass Eigenschaften ihre neuen Werte melden, auch wenn diese Änderungen von EF Core noch nicht erkannt wurden.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-121">(The astute will notice properties report their new values, even though these changes have not yet been detected by EF Core.</span></span> <span data-ttu-id="3ab6f-122">Dies liegt daran, dass in der Debugansicht aktuelle Werte direkt aus der Entitäts Instanz gelesen werden.)</span><span class="sxs-lookup"><span data-stu-id="3ab6f-122">This is because the debug view is reading current values directly from the entity instance.)</span></span>

<span data-ttu-id="3ab6f-123">Vergleichen Sie dies mit der Debugansicht nach dem Aufrufen von "DetectChanges":</span><span class="sxs-lookup"><span data-stu-id="3ab6f-123">Contrast this with the debug view after calling DetectChanges:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
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
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="3ab6f-124">Der Blog ist nun ordnungsgemäß als gekennzeichnet, `Modified` und der neue Beitrag wurde erkannt und nachverfolgt `Added` .</span><span class="sxs-lookup"><span data-stu-id="3ab6f-124">Now the blog is correctly marked as `Modified` and the new post has been detected and is tracked as `Added`.</span></span>

<span data-ttu-id="3ab6f-125">Zu Beginn dieses Abschnitts haben wir festgestellt, dass das Erkennen von Änderungen erforderlich ist, wenn nicht _die Verwendung von EF Core zum vornehmen der Änderung_ verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-125">At the start of this section we stated that detecting changes is needed when not using _using EF Core to make the change_.</span></span> <span data-ttu-id="3ab6f-126">Dies geschieht im obigen Code.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-126">This is what is happening in the code above.</span></span> <span data-ttu-id="3ab6f-127">Das heißt, die Änderungen an der Eigenschaft und der Navigation werden _direkt auf den Entitäts Instanzen_ vorgenommen, nicht mithilfe von EF Core Methoden.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-127">That is, the changes to the property and navigation are made _directly on the entity instances_, and not by using any EF Core methods.</span></span>

<span data-ttu-id="3ab6f-128">Vergleichen Sie dies mit dem folgenden Code, der die Entitäten auf die gleiche Weise ändert, aber dieses Mal EF Core Methoden verwendet:</span><span class="sxs-lookup"><span data-stu-id="3ab6f-128">Contrast this to the following code which modifies the entities in the same way, but this time using EF Core methods:</span></span>

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        context.Entry(blog).Property(e => e.Name).CurrentValue = ".NET Blog (Updated!)";

        // Add a new entity to the DbContext
        context.Add(
            new Post
            {
                Blog = blog,
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_2)]

<span data-ttu-id="3ab6f-129">In diesem Fall zeigt die Debugansicht für die Änderungs Nachverfolgung an, dass alle Entitäts Zustände und Eigenschafts Änderungen bekannt sind, auch wenn keine Änderungen vorgenommen wurden.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-129">In this case the change tracker debug view shows that all entity states and property modifications are known, even though detection of changes has not happened.</span></span> <span data-ttu-id="3ab6f-130">Dies liegt daran <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType> , dass eine EF Core Methode ist, was bedeutet, dass EF Core sofort die von dieser Methode vorgenommene Änderung kennt.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-130">This is because <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType> is an EF Core method, which means that EF Core immediately knows about the change made by this method.</span></span> <span data-ttu-id="3ab6f-131">Ebenso <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> ermöglicht das Aufrufen von EF Core, sofort über die neue Entität zu informieren und Sie entsprechend zu verfolgen.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-131">Likewise, calling <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> allows EF Core to immediately know about the new entity and track it appropriately.</span></span>

> [!TIP]
> <span data-ttu-id="3ab6f-132">Versuchen Sie nicht, Änderungen zu erkennen, indem Sie immer EF Core-Methoden verwenden, um Entitäts Änderungen vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-132">Don't attempt to avoid detecting changes by always using EF Core methods to make entity changes.</span></span> <span data-ttu-id="3ab6f-133">Dies ist oft schwieriger und führt zu einer geringeren Leistung, als Änderungen an Entitäten auf normale Weise vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-133">Doing so is often more cumbersome and performs less well than making changes to entities in the normal way.</span></span> <span data-ttu-id="3ab6f-134">Das Ziel dieses Dokuments besteht darin, zu informieren, wann Änderungen erforderlich sind und wann dies nicht der Fall ist.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-134">The intention of this document is to inform as to when detecting changes is needed and when it is not.</span></span> <span data-ttu-id="3ab6f-135">Die Absicht besteht nicht darin, die Vermeidung von Änderungs Erkennung zu fördern.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-135">The intention is not to encourage avoidance of change detection.</span></span>

### <a name="methods-that-automatically-detect-changes"></a><span data-ttu-id="3ab6f-136">Methoden zum automatischen Erkennen von Änderungen</span><span class="sxs-lookup"><span data-stu-id="3ab6f-136">Methods that automatically detect changes</span></span>

<span data-ttu-id="3ab6f-137"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> wird automatisch von Methoden aufgerufen, bei denen sich dies wahrscheinlich auf die Ergebnisse auswirkt.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-137"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> is called automatically by methods where doing so is likely to impact the results.</span></span> <span data-ttu-id="3ab6f-138">Diese Methoden werden im Anschluss beschrieben:</span><span class="sxs-lookup"><span data-stu-id="3ab6f-138">These methods are:</span></span>

- <span data-ttu-id="3ab6f-139"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType> , um sicherzustellen, dass alle Änderungen vor dem Aktualisieren der Datenbank erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-139"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType>, to ensure that all changes are detected before updating the database.</span></span>
- <span data-ttu-id="3ab6f-140"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> , um sicherzustellen, dass Entitäts Zustände und geänderte Eigenschaften auf dem neuesten Stand sind.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-140"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType>, to ensure entity states and modified properties are up-to-date.</span></span>
- <span data-ttu-id="3ab6f-141"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>, um sicherzustellen, dass das Ergebnis korrekt ist.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-141"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>, to ensure that the result is accurate.</span></span>
- <span data-ttu-id="3ab6f-142"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>, um korrekte Entitäts Zustände für Prinzipal-/Parent-Entitäten vor dem kaskadieren sicherzustellen.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-142"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>, to ensure correct entity states for principal/parent entities before cascading.</span></span>
- <span data-ttu-id="3ab6f-143"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>, um sicherzustellen, dass das nach verfolgte Diagramm auf dem neuesten Stand ist.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-143"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>, to ensure that the tracked graph is up-to-date.</span></span>

<span data-ttu-id="3ab6f-144">Es gibt auch einige stellen, an denen die Ermittlung von Änderungen nur auf einer einzelnen Entitäts Instanz statt auf dem gesamten Diagramm der nach verfolgten Entitäten erfolgt.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-144">There are also some places where detection of changes happens on only a single entity instance, rather than on the entire graph of tracked entities.</span></span> <span data-ttu-id="3ab6f-145">Diese Stellen sind:</span><span class="sxs-lookup"><span data-stu-id="3ab6f-145">These places are:</span></span>

- <span data-ttu-id="3ab6f-146">Bei Verwendung <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> von, um sicherzustellen, dass der Zustand der Entität und die geänderten Eigenschaften auf dem neuesten Stand sind.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-146">When using <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType>, to ensure that the entity's state and modified properties are up-to-date.</span></span>
- <span data-ttu-id="3ab6f-147">Wenn Sie <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> Methoden wie `Property` , `Collection` oder verwenden `Reference` `Member` , um zu gewährleisten, dass Eigenschafts Änderungen, aktuelle Werte usw. auf dem neuesten Stand sind.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-147">When using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> methods such as `Property`, `Collection`, `Reference` or `Member` to ensure property modifications, current values, etc. are up-to-date.</span></span>
- <span data-ttu-id="3ab6f-148">Wenn eine abhängige/untergeordnete Entität gelöscht wird, weil eine erforderliche Beziehung getrennt wurde.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-148">When a dependent/child entity is going to be deleted because a required relationship has been severed.</span></span> <span data-ttu-id="3ab6f-149">Dadurch wird erkannt, wenn eine Entität nicht gelöscht werden soll, weil Sie erneut übergeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-149">This detects when an entity should not be deleted because it has been re-parented.</span></span>

<span data-ttu-id="3ab6f-150">Die lokale Erkennung von Änderungen für eine einzelne Entität kann explizit durch Aufrufen von ausgelöst werden <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="3ab6f-150">Local detection of changes for a single entity can be triggered explicitly by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType>.</span></span>

> [!NOTE]
> <span data-ttu-id="3ab6f-151">Lokale Erkennungs Änderungen können einige Änderungen übersehen, die durch eine vollständige Erkennung gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-151">Local detect changes can miss some changes that a full detection would find.</span></span> <span data-ttu-id="3ab6f-152">Dies geschieht, wenn kaskadierende Aktionen, die sich aus nicht erkannten Änderungen an anderen Entitäten ergeben, Auswirkungen auf die betreffende Entität haben.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-152">This happens when cascading actions resulting from undetected changes to other entities have an impact on the entity in question.</span></span> <span data-ttu-id="3ab6f-153">In solchen Fällen muss die Anwendung möglicherweise eine vollständige Überprüfung aller Entitäten erzwingen, indem explizit aufgerufen wird <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="3ab6f-153">In such situations the application may need to force a full scan of all entities by explicitly calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>.</span></span>

### <a name="disabling-automatic-change-detection"></a><span data-ttu-id="3ab6f-154">Deaktivieren der automatischen Änderungs Erkennung</span><span class="sxs-lookup"><span data-stu-id="3ab6f-154">Disabling automatic change detection</span></span>

<span data-ttu-id="3ab6f-155">Die Leistung beim Erkennen von Änderungen ist für die meisten Anwendungen kein Engpass.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-155">The performance of detecting changes is not a bottleneck for most applications.</span></span> <span data-ttu-id="3ab6f-156">Das Erkennen von Änderungen kann jedoch zu einem Leistungsproblem für einige Anwendungen werden, die Tausende von Entitäten nachverfolgen.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-156">However, detecting changes can become a performance problem for some applications that track thousands of entities.</span></span> <span data-ttu-id="3ab6f-157">(Die genaue Anzahl hängt von vielen Faktoren ab, z. b. von der Anzahl der Eigenschaften in der Entität.) Aus diesem Grund kann die automatische Erkennung von Änderungen mithilfe von deaktiviert werden <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="3ab6f-157">(The exact number will dependent on many things, such as the number of properties in the entity.) For this reason the automatic detection of changes can be disabled using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType>.</span></span> <span data-ttu-id="3ab6f-158">Sie sollten z. b. Verknüpfungs Entitäten in einer m:n-Beziehung mit Nutzlasten verarbeiten:</span><span class="sxs-lookup"><span data-stu-id="3ab6f-158">For example, consider processing join entities in a many-to-many relationship with payloads:</span></span>

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>()) // Detects changes automatically
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                    entityEntry.Entity.TaggedOn = DateTime.Now;
                }
            }

            try
            {
                ChangeTracker.AutoDetectChangesEnabled = false;
                return base.SaveChanges(); // Avoid automatically detecting changes again here
            }
            finally
            {
                ChangeTracker.AutoDetectChangesEnabled = true;
            }
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=SaveChanges)]

<span data-ttu-id="3ab6f-159">Wie wir im vorherigen Abschnitt wissen, erkennen sowohl als <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> auch <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> automatisch Änderungen.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-159">As we know from the previous section, both <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> automatically detect changes.</span></span> <span data-ttu-id="3ab6f-160">Nach dem Aufrufen von Einträgen führt der Code jedoch keine Änderungen an Entitäts-oder Eigenschafts Zuständen aus.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-160">However, after calling Entries, the code does not then make any entity or property state changes.</span></span> <span data-ttu-id="3ab6f-161">(Das Festlegen normaler Eigenschaftswerte für hinzugefügte Entitäten führt nicht zu Zustandsänderungen.) Der Code deaktiviert daher unnötige automatische Änderungs Erkennung beim Aufrufen der Basis-SaveChanges-Methode.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-161">(Setting normal property values on Added entities does not cause any state changes.) The code therefore disables unnecessary automatic change detection when calling down into the base SaveChanges method.</span></span> <span data-ttu-id="3ab6f-162">Der Code verwendet auch einen try/after-Block, um sicherzustellen, dass die Standardeinstellung wieder hergestellt wird, auch wenn SaveChanges fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-162">The code also makes use of a try/finally block to ensure that the default setting is restored even if SaveChanges fails.</span></span>

> [!TIP]
> <span data-ttu-id="3ab6f-163">Gehen Sie nicht davon aus, dass der Code die automatische Änderungs Erkennung in deaktivieren muss, damit Sie gut funktioniert.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-163">Do not assume that your code must disable automatic change detection to to perform well.</span></span> <span data-ttu-id="3ab6f-164">Dies ist nur erforderlich, wenn die Profilerstellung für eine Anwendung, bei der viele Entitäten nachverfolgt werden, ein Problem auftritt.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-164">This is only needed when profiling an application tracking many entities indicates that performance of change detection is an issue.</span></span>

### <a name="detecting-changes-and-value-conversions"></a><span data-ttu-id="3ab6f-165">Erkennen von Änderungen und Wert Konvertierungen</span><span class="sxs-lookup"><span data-stu-id="3ab6f-165">Detecting changes and value conversions</span></span>

<span data-ttu-id="3ab6f-166">Um die Nachverfolgung von Momentaufnahmen mit einem Entitätstyp zu verwenden, muss EF Core in der Lage sein,</span><span class="sxs-lookup"><span data-stu-id="3ab6f-166">To use snapshot change tracking with an entity type, EF Core must be able to:</span></span>

- <span data-ttu-id="3ab6f-167">Erstellen Sie eine Momentaufnahme jedes Eigenschafts Werts, wenn die Entität nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-167">Make a snapshot of each property value when the entity is tracked</span></span>
- <span data-ttu-id="3ab6f-168">Vergleicht diesen Wert mit dem aktuellen Wert der Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-168">Compare this value to the current value of the property</span></span>
- <span data-ttu-id="3ab6f-169">Generieren Sie einen Hashcode für den Wert.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-169">Generate a hash code for the value</span></span>

<span data-ttu-id="3ab6f-170">Dies erfolgt automatisch durch EF Core für Typen, die direkt der Datenbank zugeordnet werden können.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-170">This is handled automatically by EF Core for types that can be directly mapped to the database.</span></span> <span data-ttu-id="3ab6f-171">Wenn jedoch ein [Wert Konverter zum Zuordnen einer Eigenschaft verwendet wird](xref:core/modeling/value-conversions), muss dieser Konverter angeben, wie diese Aktionen durchgeführt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-171">However, when a [value converter is used to map a property](xref:core/modeling/value-conversions), then that converter must specify how to perform these actions.</span></span> <span data-ttu-id="3ab6f-172">Dies wird mit einem Wert Vergleich erreicht und in der Dokumentation zu den [Wert comparern](xref:core/modeling/value-comparers) ausführlich beschrieben.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-172">This is achieved with a value comparer, and is described in detail in the [Value Comparers](xref:core/modeling/value-comparers) documentation.</span></span>

## <a name="notification-entities"></a><span data-ttu-id="3ab6f-173">Benachrichtigungs Entitäten</span><span class="sxs-lookup"><span data-stu-id="3ab6f-173">Notification entities</span></span>

<span data-ttu-id="3ab6f-174">Die Änderungs Nachverfolgung von Momentaufnahmen wird für die meisten Anwendungen empfohlen.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-174">Snapshot change tracking is recommended for most applications.</span></span> <span data-ttu-id="3ab6f-175">Allerdings können Anwendungen, die viele Entitäten nachverfolgen und/oder viele Änderungen an diesen Entitäten vornehmen, von der Implementierung von Entitäten profitieren, die EF Core automatisch benachrichtigen, wenn sich ihre Eigenschaften und Navigations Werte ändern</span><span class="sxs-lookup"><span data-stu-id="3ab6f-175">However, applications that track many entities and/or make many changes to those entities may benefit from implementing entities that automatically notify EF Core when their property and navigation values change.</span></span> <span data-ttu-id="3ab6f-176">Diese werden als "Benachrichtigungs Entitäten" bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-176">These are known as "notification entities".</span></span>

### <a name="implementing-notification-entities"></a><span data-ttu-id="3ab6f-177">Implementieren von Benachrichtigungs Einheiten</span><span class="sxs-lookup"><span data-stu-id="3ab6f-177">Implementing notification entities</span></span>

<span data-ttu-id="3ab6f-178">Benachrichtigungs Entitäten verwenden die <xref:System.ComponentModel.INotifyPropertyChanging> -und- <xref:System.ComponentModel.INotifyPropertyChanged> Schnittstellen, die Teil der .net-Basisklassen Bibliothek (Base Class Library, BCL) sind.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-178">Notification entities make use of the <xref:System.ComponentModel.INotifyPropertyChanging> and <xref:System.ComponentModel.INotifyPropertyChanged> interfaces, which are part of the .NET base class library (BCL).</span></span> <span data-ttu-id="3ab6f-179">Diese Schnittstellen definieren Ereignisse, die vor und nach dem Ändern eines Eigenschafts Werts ausgelöst werden müssen.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-179">These interfaces define events that must be fired before and after changing a property value.</span></span> <span data-ttu-id="3ab6f-180">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3ab6f-180">For example:</span></span>

<!--
    public class Blog : INotifyPropertyChanging, INotifyPropertyChanged
    {
        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private int _id;
        public int Id
        {
            get => _id;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Id)));
                _id = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Id)));
            }
        }

        private string _name;
        public string Name
        {
            get => _name;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Name)));
                _name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Name)));
            }
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationEntitiesSamples.cs?name=Model)]

<span data-ttu-id="3ab6f-181">Darüber hinaus muss jede Auflistungs Navigation implementieren `INotifyCollectionChanged` . im obigen Beispiel wird dies mithilfe eines <xref:System.Collections.ObjectModel.ObservableCollection%601> von Beiträgen erfüllt.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-181">In addition, any collection navigations must implement `INotifyCollectionChanged`; in the example above this satisfied by using an <xref:System.Collections.ObjectModel.ObservableCollection%601> of posts.</span></span> <span data-ttu-id="3ab6f-182">EF Core ist auch mit einer <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> Implementierung ausgeliefert, die auf Kosten einer stabilen Reihenfolge effizientere Suchvorgänge bietet.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-182">EF Core also ships with an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> implementation that has more efficient lookups at the expense of stable ordering.</span></span>

<span data-ttu-id="3ab6f-183">Der größte Teil dieses Benachrichtigungs Codes wird in der Regel in eine nicht zugeordnete Basisklasse verschoben.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-183">Most of this notification code is typically moved into an unmapped base class.</span></span> <span data-ttu-id="3ab6f-184">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3ab6f-184">For example:</span></span>

<!--
    public class Blog : NotifyingEntity
    {
        private int _id;
        public int Id
        {
            get => _id;
            set => SetWithNotify(value, out _id);
        }

        private string _name;
        public string Name
        {
            get => _name;
            set => SetWithNotify(value, out _name);
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public abstract class NotifyingEntity : INotifyPropertyChanging, INotifyPropertyChanged
    {
        protected void SetWithNotify<T>(T value, out T field, [CallerMemberName] string propertyName = "")
        {
            NotifyChanging(propertyName);
            field = value;
            NotifyChanged(propertyName);
        }

        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private void NotifyChanged(string propertyName)
            => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));

        private void NotifyChanging(string propertyName)
            => PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(propertyName));
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Model)]

### <a name="configuring-notification-entities"></a><span data-ttu-id="3ab6f-185">Benachrichtigungs Entitäten konfigurieren</span><span class="sxs-lookup"><span data-stu-id="3ab6f-185">Configuring notification entities</span></span>

<span data-ttu-id="3ab6f-186">Es gibt keine Möglichkeit, EF Core zu überprüfen, ob `INotifyPropertyChanging` oder `INotifyPropertyChanged` vollständig für die Verwendung mit EF Core implementiert sind.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-186">There is no way for EF Core to validate that `INotifyPropertyChanging` or `INotifyPropertyChanged` are fully implemented for use with EF Core.</span></span> <span data-ttu-id="3ab6f-187">Insbesondere verwenden einige Verwendungsmöglichkeiten dieser Schnittstellen dies nur für bestimmte Eigenschaften, nicht für alle Eigenschaften (einschließlich Navigationen), wie dies für EF Core erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-187">In particular, some uses of these interfaces do so with notifications only on certain properties, rather than on all properties (including navigations) as required by EF Core.</span></span> <span data-ttu-id="3ab6f-188">Aus diesem Grund wird EF Core nicht automatisch in diese Ereignisse eingebunden.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-188">For this reason, EF Core does not automatically hook into these events.</span></span>

<span data-ttu-id="3ab6f-189">Stattdessen müssen EF Core so konfiguriert werden, dass diese Benachrichtigungs Entitäten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-189">Instead, EF Core must be configured to use these notification entities.</span></span> <span data-ttu-id="3ab6f-190">Dies erfolgt in der Regel für alle Entitäts Typen durch Aufrufen von <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="3ab6f-190">This is usually done for all entity types by calling <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="3ab6f-191">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3ab6f-191">For example:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.HasChangeTrackingStrategy(ChangeTrackingStrategy.ChangingAndChangedNotifications);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=OnModelCreating)]

<span data-ttu-id="3ab6f-192">(Die Strategie kann auch unterschiedlich für verschiedene Entitäts Typen festgelegt werden <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> . Dies ist jedoch in der Regel kontraproduktiv, da DetectChanges für die Typen, die keine Benachrichtigungs Entitäten sind, weiterhin erforderlich ist.)</span><span class="sxs-lookup"><span data-stu-id="3ab6f-192">(The strategy can also be set differently for different entity types using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType>, but this is usually counterproductive since DetectChanges is still required for those types that are not notification entities.)</span></span>

<span data-ttu-id="3ab6f-193">Bei der vollständigen Benachrichtigungs Änderungs Nachverfolgung müssen sowohl `INotifyPropertyChanging` als auch `INotifyPropertyChanged` implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-193">Full notification change tracking requires that both `INotifyPropertyChanging` and `INotifyPropertyChanged` are implemented.</span></span> <span data-ttu-id="3ab6f-194">Dies ermöglicht die Speicherung ursprünglicher Werte, kurz bevor der Eigenschafts Wert geändert wird, sodass EF Core keine Momentaufnahme erstellen muss, wenn die Entität nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-194">This allows original values to be saved just before the property value is changed, avoiding the need for EF Core to create a snapshot when tracking the entity.</span></span> <span data-ttu-id="3ab6f-195">Entitäts Typen, die nur implementieren, `INotifyPropertyChanged` können auch mit EF Core verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-195">Entity types that implement only `INotifyPropertyChanged` can also be used with EF Core.</span></span> <span data-ttu-id="3ab6f-196">In diesem Fall erstellt EF immer noch eine Momentaufnahme, wenn eine Entität nachverfolgt wird, um die ursprünglichen Werte nachzuverfolgen. die Benachrichtigungen werden dann jedoch verwendet, um Änderungen sofort zu erkennen, anstatt dass DetectChanges aufgerufen werden müssen.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-196">In this case, EF still creates a snapshot when tracking an entity to keep track of original values, but then uses the notifications to detect changes immediately, rather than needing DetectChanges to be called.</span></span>

<span data-ttu-id="3ab6f-197">Die unterschiedlichen <xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy> Werte werden in der folgenden Tabelle zusammengefasst.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-197">The different <xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy> values are summarized in the the following table.</span></span>

| <span data-ttu-id="3ab6f-198">Changetrackingstrategy</span><span class="sxs-lookup"><span data-stu-id="3ab6f-198">ChangeTrackingStrategy</span></span>                              | <span data-ttu-id="3ab6f-199">Benötigte Schnittstellen</span><span class="sxs-lookup"><span data-stu-id="3ab6f-199">Interfaces needed</span></span>                                      | <span data-ttu-id="3ab6f-200">DetectChanges erforderlich</span><span class="sxs-lookup"><span data-stu-id="3ab6f-200">Needs DetectChanges</span></span> | <span data-ttu-id="3ab6f-201">Momentaufnahmen ursprünglicher Werte</span><span class="sxs-lookup"><span data-stu-id="3ab6f-201">Snapshots original values</span></span>
|:----------------------------------------------------|--------------------------------------------------------|---------------------|--------------------------
| <span data-ttu-id="3ab6f-202">Momentaufnahme</span><span class="sxs-lookup"><span data-stu-id="3ab6f-202">Snapshot</span></span>                                            | <span data-ttu-id="3ab6f-203">Keine</span><span class="sxs-lookup"><span data-stu-id="3ab6f-203">None</span></span>                                                   | <span data-ttu-id="3ab6f-204">Ja</span><span class="sxs-lookup"><span data-stu-id="3ab6f-204">Yes</span></span>                 | <span data-ttu-id="3ab6f-205">Ja</span><span class="sxs-lookup"><span data-stu-id="3ab6f-205">Yes</span></span>
| <span data-ttu-id="3ab6f-206">Changedbenachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="3ab6f-206">ChangedNotifications</span></span>                                | <span data-ttu-id="3ab6f-207">INotifyPropertyChanged</span><span class="sxs-lookup"><span data-stu-id="3ab6f-207">INotifyPropertyChanged</span></span>                                 | <span data-ttu-id="3ab6f-208">Nein</span><span class="sxs-lookup"><span data-stu-id="3ab6f-208">No</span></span>                  | <span data-ttu-id="3ab6f-209">Ja</span><span class="sxs-lookup"><span data-stu-id="3ab6f-209">Yes</span></span>
| <span data-ttu-id="3ab6f-210">Changingandchangedbenachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="3ab6f-210">ChangingAndChangedNotifications</span></span>                     | <span data-ttu-id="3ab6f-211">INotifyPropertyChanged und INotifyPropertyChanging</span><span class="sxs-lookup"><span data-stu-id="3ab6f-211">INotifyPropertyChanged and INotifyPropertyChanging</span></span>     | <span data-ttu-id="3ab6f-212">Nein</span><span class="sxs-lookup"><span data-stu-id="3ab6f-212">No</span></span>                  | <span data-ttu-id="3ab6f-213">Nein</span><span class="sxs-lookup"><span data-stu-id="3ab6f-213">No</span></span>
| <span data-ttu-id="3ab6f-214">Changingandchangednotificationswithoriginalvalues</span><span class="sxs-lookup"><span data-stu-id="3ab6f-214">ChangingAndChangedNotificationsWithOriginalValues</span></span>   | <span data-ttu-id="3ab6f-215">INotifyPropertyChanged und INotifyPropertyChanging</span><span class="sxs-lookup"><span data-stu-id="3ab6f-215">INotifyPropertyChanged and INotifyPropertyChanging</span></span>     | <span data-ttu-id="3ab6f-216">Nein</span><span class="sxs-lookup"><span data-stu-id="3ab6f-216">No</span></span>                  | <span data-ttu-id="3ab6f-217">Ja</span><span class="sxs-lookup"><span data-stu-id="3ab6f-217">Yes</span></span>

### <a name="using-notification-entities"></a><span data-ttu-id="3ab6f-218">Mit Benachrichtigungs Entitäten</span><span class="sxs-lookup"><span data-stu-id="3ab6f-218">Using notification entities</span></span>

<span data-ttu-id="3ab6f-219">Benachrichtigungs Entitäten Verhalten sich wie alle anderen Entitäten, mit dem Unterschied, dass das vornehmen von Änderungen an den Entitäts Instanzen keinen <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> Rückruf von erfordert</span><span class="sxs-lookup"><span data-stu-id="3ab6f-219">Notification entities behave like any other entities, except that making changes to the entity instances do not require a call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> to detect these changes.</span></span> <span data-ttu-id="3ab6f-220">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3ab6f-220">For example:</span></span>

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Notification_entities_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Notification_entities_2)]

<span data-ttu-id="3ab6f-221">Bei normalen Entitäten zeigte die [Debugansicht der Änderungs nach](xref:core/change-tracking/debug-views) Verfolgung, dass diese Änderungen erst erkannt wurden, wenn DetectChanges aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-221">With normal entities, the [change tracker debug view](xref:core/change-tracking/debug-views) showed that these changes were not detected until DetectChanges was called.</span></span> <span data-ttu-id="3ab6f-222">Wenn Sie die Debugansicht anzeigen, wenn Benachrichtigungs Entitäten verwendet werden, zeigt dies, dass diese Änderungen sofort erkannt wurden:</span><span class="sxs-lookup"><span data-stu-id="3ab6f-222">Looking at the debug view when notification entities are used shows that these changes have been detected immediately:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
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
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

## <a name="change-tracking-proxies"></a><span data-ttu-id="3ab6f-223">Proxys zur Änderungsnachverfolgung</span><span class="sxs-lookup"><span data-stu-id="3ab6f-223">Change-tracking proxies</span></span>

> [!NOTE]
> <span data-ttu-id="3ab6f-224">Proxys für die Änderungs Nachverfolgung wurden in EF Core 5,0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-224">Change-tracking proxies were introduced in EF Core 5.0.</span></span>

<span data-ttu-id="3ab6f-225">EF Core können Proxy Typen, die und implementieren, dynamisch generieren <xref:System.ComponentModel.INotifyPropertyChanging> <xref:System.ComponentModel.INotifyPropertyChanged> .</span><span class="sxs-lookup"><span data-stu-id="3ab6f-225">EF Core can dynamically generate proxy types that implement <xref:System.ComponentModel.INotifyPropertyChanging> and <xref:System.ComponentModel.INotifyPropertyChanged>.</span></span> <span data-ttu-id="3ab6f-226">Hierfür müssen Sie das nuget-Paket [Microsoft. entityframeworkcore. Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) installieren und Proxys für die Änderungs Nachverfolgung wie folgt aktivieren <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> :</span><span class="sxs-lookup"><span data-stu-id="3ab6f-226">This requires installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet package, and enabling change-tracking proxies with <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.UseChangeTrackingProxies();
-->
[!code-csharp[OnConfiguring](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=OnConfiguring)]

<span data-ttu-id="3ab6f-227">Das Erstellen eines dynamischen Proxys umfasst das Erstellen eines neuen dynamischen .net-Typs (mit der Implementierung von " [Castle. Core](https://www.nuget.org/packages/Castle.Core/) Proxies"), der vom Entitätstyp erbt und dann alle Eigenschaften Setter überschreibt.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-227">Creating a dynamic proxy involves creating a new, dynamic .NET type (using the [Castle.Core](https://www.nuget.org/packages/Castle.Core/) proxies implementation), which inherits from the entity type and then overrides all property setters.</span></span> <span data-ttu-id="3ab6f-228">Entitäts Typen für Proxys müssen daher Typen sein, die von geerbt werden können und über Eigenschaften verfügen müssen, die überschrieben werden können.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-228">Entity types for proxies must therefore be types that can be inherited from and must have properties that can be overridden.</span></span> <span data-ttu-id="3ab6f-229">Außerdem müssen die explizit erstellten Sammlungs Navigation Beispiels <xref:System.Collections.Specialized.INotifyCollectionChanged> Weise Folgendes implementieren:</span><span class="sxs-lookup"><span data-stu-id="3ab6f-229">Also, collection navigations created explicitly must implement <xref:System.Collections.Specialized.INotifyCollectionChanged> For example:</span></span>

<!--
    public class Blog
    {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }

        public virtual IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public class Post
    {
        public virtual int Id { get; set; }
        public virtual string Title { get; set; }
        public virtual string Content { get; set; }

        public virtual int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Model)]

<span data-ttu-id="3ab6f-230">Ein wesentlicher Nachteil von Proxys für die Änderungs Nachverfolgung besteht darin, dass EF Core immer Instanzen des Proxys nachverfolgen muss, niemals Instanzen des zugrunde liegenden Entitäts Typs.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-230">One significant downside to change-tracking proxies is that EF Core must always track instances of the proxy, never instances of the underlying entity type.</span></span> <span data-ttu-id="3ab6f-231">Dies liegt daran, dass Instanzen des zugrunde liegenden Entitäts Typs keine Benachrichtigungen generieren, was bedeutet, dass an diesen Entitäten vorgenommene Änderungen übersehen werden.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-231">This is because instances of the underlying entity type will not generate notifications, which means changes made to these entities will be missed.</span></span>

<span data-ttu-id="3ab6f-232">EF Core beim Abfragen der Datenbank automatisch Proxy Instanzen erstellt, ist dieser Nachteil in der Regel auf das Nachverfolgen neuer Entitäts Instanzen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-232">EF Core creates proxy instances automatically when querying the database, so this downside is generally limited to tracking new entity instances.</span></span> <span data-ttu-id="3ab6f-233">Diese Instanzen müssen mithilfe der <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> Erweiterungs Methoden und **nicht** auf die normale Weise mithilfe von erstellt werden `new` .</span><span class="sxs-lookup"><span data-stu-id="3ab6f-233">These instances must be created using the <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> extension methods, and **not** in the normal way using `new`.</span></span> <span data-ttu-id="3ab6f-234">Dies bedeutet, dass der Code aus den vorherigen Beispielen nun Folgendes verwenden muss `CreateProxy` :</span><span class="sxs-lookup"><span data-stu-id="3ab6f-234">This means the code from the previous examples must now make use of `CreateProxy`:</span></span>

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(
                context.CreateProxy<Post>(
                    p =>
                        {
                            p.Title = "What’s next for System.Text.Json?";
                            p.Content = ".NET 5.0 was released recently and has come with many...";
                        }));

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracking_proxies_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Change_tracking_proxies_1)]

## <a name="change-tracking-events"></a><span data-ttu-id="3ab6f-235">Änderungs nach Verfolgungs Ereignisse</span><span class="sxs-lookup"><span data-stu-id="3ab6f-235">Change tracking events</span></span>

<span data-ttu-id="3ab6f-236">EF Core löst das- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> Ereignis aus, wenn eine Entität zum ersten Mal nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-236">EF Core fires the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> event when an entity is tracked for the first time.</span></span> <span data-ttu-id="3ab6f-237">Zukünftige Entitäts Zustandsänderungen führen zu <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> Ereignissen.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-237">Future entity state changes result in <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> events.</span></span> <span data-ttu-id="3ab6f-238">Weitere Informationen finden Sie unter [.NET-Ereignisse in EF Core](xref:core/logging-events-diagnostics/events).</span><span class="sxs-lookup"><span data-stu-id="3ab6f-238">See [.NET Events in EF Core](xref:core/logging-events-diagnostics/events) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="3ab6f-239">Das- `StateChanged` Ereignis wird nicht ausgelöst, wenn eine Entität zum ersten Mal nachverfolgt wird, auch wenn sich der Status von `Detached` in einen der anderen Zustände geändert hat.</span><span class="sxs-lookup"><span data-stu-id="3ab6f-239">The `StateChanged` event is not fired when an entity is first tracked, even though the state has changed from `Detached` to one of the other states.</span></span> <span data-ttu-id="3ab6f-240">Achten Sie darauf, sowohl auf `StateChanged` -als auch auf `Tracked` Ereignisse zu lauschen, um alle relevanten Benachrichtigungen zu erhalten</span><span class="sxs-lookup"><span data-stu-id="3ab6f-240">Make sure to listen for both `StateChanged` and `Tracked` events to get all relevant notifications.</span></span>
