---
title: Änderungs Nachverfolgung-Debugging-EF Core
description: Verwenden von ChangeTracker DebugView und Log Messages zum Debuggen EF Core Änderungs Nachverfolgung
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/debug-views
ms.openlocfilehash: 76108120cf7f532d085fef12121bb639b065add0
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129591"
---
# <a name="change-tracker-debugging"></a><span data-ttu-id="baf29-103">Änderungs nach Verfolgungs Debugging</span><span class="sxs-lookup"><span data-stu-id="baf29-103">Change Tracker Debugging</span></span>

<span data-ttu-id="baf29-104">Der Änderungs Tracker Entity Framework Core (EF Core) generiert zwei Arten von Ausgaben, die beim Debuggen hilfreich sind:</span><span class="sxs-lookup"><span data-stu-id="baf29-104">The Entity Framework Core (EF Core) change tracker generates two kinds of output to help with debugging:</span></span>

- <span data-ttu-id="baf29-105"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType>Stellt eine lesbare Ansicht aller Entitäten bereit, die nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="baf29-105">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> provides a human-readable view of all entities being tracked</span></span>
- <span data-ttu-id="baf29-106">Protokollmeldungen auf Debugebene werden generiert, wenn die Änderungs Nachverfolgung den Status erkennt und Beziehungen korrigiert.</span><span class="sxs-lookup"><span data-stu-id="baf29-106">Debug-level log messages are generated when the change tracker detects state and fixes up relationships</span></span>

> [!TIP]
> <span data-ttu-id="baf29-107">In diesem Dokument wird davon ausgegangen, dass die Entitäts Zustände und die Grundlagen EF Core Änderungs Nachverfolgung verstanden werden.</span><span class="sxs-lookup"><span data-stu-id="baf29-107">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="baf29-108">Weitere Informationen zu diesen Themen finden Sie [unter Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="baf29-108">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="baf29-109">Sie können den gesamten Code in diesem Dokument ausführen und Debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackerDebugging).</span><span class="sxs-lookup"><span data-stu-id="baf29-109">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackerDebugging).</span></span>

## <a name="change-tracker-debug-view"></a><span data-ttu-id="baf29-110">Debug-Ansicht für Änderungs Nachverfolgung</span><span class="sxs-lookup"><span data-stu-id="baf29-110">Change tracker debug view</span></span>

<span data-ttu-id="baf29-111">Sie können im Debugger Ihrer IDE auf die Debugansicht für die Änderungs Nachverfolgung zugreifen.</span><span class="sxs-lookup"><span data-stu-id="baf29-111">The change tracker debug view can be accessed in the debugger of your IDE.</span></span> <span data-ttu-id="baf29-112">Beispielsweise mit Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="baf29-112">For example, with Visual Studio:</span></span>

![Zugreifen auf die Debugansicht der Änderungs Nachverfolgung über den Visual Studio-Debugger](_static/debug-view.png)

<span data-ttu-id="baf29-114">Sie können auch direkt über den Code darauf zugreifen, z. b. um die Debugansicht an die Konsole zu senden:</span><span class="sxs-lookup"><span data-stu-id="baf29-114">It can also be accessed directly from code, for example to send the debug view to the console:</span></span>

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.ShortView);
-->
[!code-csharp[Change_tracker_debug_view_1b](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1b)]

<span data-ttu-id="baf29-115">Die Debugansicht hat eine Kurzform und eine lange Form.</span><span class="sxs-lookup"><span data-stu-id="baf29-115">The debug view has a short form and a long form.</span></span> <span data-ttu-id="baf29-116">In der Kurzform werden nach verfolgte Entitäten, deren Status und Schlüsselwerte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="baf29-116">The short form shows tracked entities, their state, and key values.</span></span> <span data-ttu-id="baf29-117">Die Long-Form umfasst auch alle Eigenschafts-und Navigations Werte und den Status.</span><span class="sxs-lookup"><span data-stu-id="baf29-117">The long form also includes all property and navigation values and state.</span></span>

### <a name="the-short-view"></a><span data-ttu-id="baf29-118">Die kurze Ansicht</span><span class="sxs-lookup"><span data-stu-id="baf29-118">The short view</span></span>

<span data-ttu-id="baf29-119">Sehen wir uns ein Beispiel für eine Debugansicht an, das das am Ende dieses Dokuments gezeigte Modell verwendet.</span><span class="sxs-lookup"><span data-stu-id="baf29-119">Let's look at a debug view example using the model shown at the end of this document.</span></span> <span data-ttu-id="baf29-120">Zunächst werden einige Entitäten nachverfolgt und in verschiedenen Zuständen abgelegt, damit wir gute Änderungs nach Verfolgungs Daten anzeigen können:</span><span class="sxs-lookup"><span data-stu-id="baf29-120">First, we will track some entities and put them in some different states, just so we have good change tracking data to view:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts).ThenInclude(e => e.Tags)
            .Include(e => e.Assets)
            .ToList();

        // Mark something Added
        blogs[0].Posts.Add(
            new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many new features and..."
            });

        // Mark something Deleted
        blogs[1].Posts.Remove(blogs[1].Posts[1]);

        // Make something Modified
        blogs[0].Name = ".NET Blog (All new!)";

        context.ChangeTracker.DetectChanges();
-->
[!code-csharp[Change_tracker_debug_view_1a](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1a)]

<span data-ttu-id="baf29-121">Das Drucken der kurzen Ansicht an dieser Stelle, wie oben gezeigt, führt zur folgenden Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="baf29-121">Printing the short view at this point, as shown above, results in the following output:</span></span>

```output
Blog {Id: 1} Modified AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}
Blog {Id: 2} Unchanged AK {AssetsId: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
BlogAssets {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65} Unchanged FK {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
BlogAssets {Id: ed727978-1ffe-4709-baee-73913e8e44a0} Unchanged FK {Id: ed727978-1ffe-4709-baee-73913e8e44a0}
Post {Id: -2147482643} Added FK {BlogId: 1}
Post {Id: 1} Unchanged FK {BlogId: 1}
Post {Id: 2} Unchanged FK {BlogId: 1}
Post {Id: 3} Unchanged FK {BlogId: 2}
Post {Id: 4} Deleted FK {BlogId: 2}
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 1} Unchanged FK {PostsId: 1} FK {TagsId: 1}
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 3} Unchanged FK {PostsId: 1} FK {TagsId: 3}
PostTag (Dictionary<string, object>) {PostsId: 2, TagsId: 1} Unchanged FK {PostsId: 2} FK {TagsId: 1}
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 2} Unchanged FK {PostsId: 3} FK {TagsId: 2}
PostTag (Dictionary<string, object>) {PostsId: 4, TagsId: 2} Deleted FK {PostsId: 4} FK {TagsId: 2}
Tag {Id: 1} Unchanged
Tag {Id: 2} Unchanged
Tag {Id: 3} Unchanged
```

<span data-ttu-id="baf29-122">Beachten Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="baf29-122">Notice:</span></span>

- <span data-ttu-id="baf29-123">Jede nach verfolgte Entität wird mit ihrem Primärschlüssel Wert (PK) aufgelistet.</span><span class="sxs-lookup"><span data-stu-id="baf29-123">Each tracked entity is listed with its primary key (PK) value.</span></span> <span data-ttu-id="baf29-124">Beispiel: `Blog {Id: 1}`.</span><span class="sxs-lookup"><span data-stu-id="baf29-124">For example, `Blog {Id: 1}`.</span></span>
- <span data-ttu-id="baf29-125">Wenn es sich bei der Entität um einen Entitätstyp mit gemeinsam genutzter Entität handelt, wird auch der CLR-Typ angezeigt.</span><span class="sxs-lookup"><span data-stu-id="baf29-125">If the entity is a shared-type entity type, then it's CLR type is also shown.</span></span> <span data-ttu-id="baf29-126">Beispiel: `PostTag (Dictionary<string, object>)`.</span><span class="sxs-lookup"><span data-stu-id="baf29-126">For example, `PostTag (Dictionary<string, object>)`.</span></span>
- <span data-ttu-id="baf29-127">Der <xref:Microsoft.EntityFrameworkCore.EntityState> wird als nächstes angezeigt.</span><span class="sxs-lookup"><span data-stu-id="baf29-127">The <xref:Microsoft.EntityFrameworkCore.EntityState> is shown next.</span></span> <span data-ttu-id="baf29-128">Dabei handelt es sich um eine von `Unchanged` , `Added` , `Modified` oder `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="baf29-128">This will be one of `Unchanged`, `Added`, `Modified`, or `Deleted`.</span></span>
- <span data-ttu-id="baf29-129">Die Werte für beliebige Alternative Schlüssel (AKS) werden als nächstes angezeigt.</span><span class="sxs-lookup"><span data-stu-id="baf29-129">Values for any alternate keys (AKs) are shown next.</span></span> <span data-ttu-id="baf29-130">Beispiel: `AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}`.</span><span class="sxs-lookup"><span data-stu-id="baf29-130">For example, `AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}`.</span></span>
- <span data-ttu-id="baf29-131">Schließlich werden die Werte für alle Fremdschlüssel (Key Keys, f) angezeigt.</span><span class="sxs-lookup"><span data-stu-id="baf29-131">Finally, values for any foreign keys (FKs) are shown.</span></span> <span data-ttu-id="baf29-132">Beispiel: `FK {PostsId: 4} FK {TagsId: 2}`.</span><span class="sxs-lookup"><span data-stu-id="baf29-132">For example, `FK {PostsId: 4} FK {TagsId: 2}`.</span></span>

### <a name="the-long-view"></a><span data-ttu-id="baf29-133">Die lange Ansicht</span><span class="sxs-lookup"><span data-stu-id="baf29-133">The long view</span></span>

<span data-ttu-id="baf29-134">Die Ansicht Long kann auf die gleiche Weise wie in der kurzen Ansicht an die Konsole gesendet werden:</span><span class="sxs-lookup"><span data-stu-id="baf29-134">The long view can be sent to the console in the same way as the short view:</span></span>

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracker_debug_view_1c](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1c)]

<span data-ttu-id="baf29-135">Die Ausgabe für denselben Zustand wie die kurze Ansicht oben ist:</span><span class="sxs-lookup"><span data-stu-id="baf29-135">The output for the same state as the short view above is:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK
  Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'
  Assets: {Id: ed727978-1ffe-4709-baee-73913e8e44a0}
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  AssetsId: '3a54b880-2b9d-486b-9403-dc2e52d36d65' AK
  Name: 'Visual Studio Blog'
  Assets: {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
  Posts: [{Id: 3}]
BlogAssets {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65} Unchanged
  Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK
  Banner: <null>
  Blog: {Id: 2}
BlogAssets {Id: ed727978-1ffe-4709-baee-73913e8e44a0} Unchanged
  Id: 'ed727978-1ffe-4709-baee-73913e8e44a0' PK FK
  Banner: <null>
  Blog: {Id: 1}
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many new fe...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
  Tags: []
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: [{Id: 1}, {Id: 3}]
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: [{Id: 1}]
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: [{Id: 2}]
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: [{Id: 2}]
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 1} Unchanged
  PostsId: 1 PK FK
  TagsId: 1 PK FK
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 3} Unchanged
  PostsId: 1 PK FK
  TagsId: 3 PK FK
PostTag (Dictionary<string, object>) {PostsId: 2, TagsId: 1} Unchanged
  PostsId: 2 PK FK
  TagsId: 1 PK FK
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 2} Unchanged
  PostsId: 3 PK FK
  TagsId: 2 PK FK
PostTag (Dictionary<string, object>) {PostsId: 4, TagsId: 2} Deleted
  PostsId: 4 PK FK
  TagsId: 2 PK FK
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 1}, {Id: 2}]
Tag {Id: 2} Unchanged
  Id: 2 PK
  Text: 'Visual Studio'
  Posts: [{Id: 3}, {Id: 4}]
Tag {Id: 3} Unchanged
  Id: 3 PK
  Text: 'EF Core'
  Posts: [{Id: 1}]
```

<span data-ttu-id="baf29-136">Jede nach verfolgte Entität und Ihr Status werden wie zuvor angezeigt.</span><span class="sxs-lookup"><span data-stu-id="baf29-136">Each tracked entity and its state is shown as before.</span></span> <span data-ttu-id="baf29-137">In der Long-Ansicht werden jedoch auch Eigenschaften-und Navigations Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="baf29-137">However, the long view also shows property and navigation values.</span></span>

#### <a name="property-values"></a><span data-ttu-id="baf29-138">Eigenschaftswerte</span><span class="sxs-lookup"><span data-stu-id="baf29-138">Property values</span></span>

<span data-ttu-id="baf29-139">Für jede Eigenschaft wird in der langen Ansicht angezeigt, ob die Eigenschaft Teil eines Primärschlüssels (PK), eines alternativen Schlüssels (AK) oder eines fremd Schlüssels (FK) ist.</span><span class="sxs-lookup"><span data-stu-id="baf29-139">For each property, the long view shows whether or not the property is part of a primary key (PK), alternate key (AK), or foreign key (FK).</span></span> <span data-ttu-id="baf29-140">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="baf29-140">For example:</span></span>

- <span data-ttu-id="baf29-141">`Blog.Id` ist eine Primärschlüssel Eigenschaft: `Id: 1 PK`</span><span class="sxs-lookup"><span data-stu-id="baf29-141">`Blog.Id` is a primary key property: `Id: 1 PK`</span></span>
- <span data-ttu-id="baf29-142">`Blog.AssetsId` ist eine Alternative Schlüsseleigenschaft: `AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK`</span><span class="sxs-lookup"><span data-stu-id="baf29-142">`Blog.AssetsId` is an alternate key property: `AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK`</span></span>
- <span data-ttu-id="baf29-143">`Post.BlogId` ist eine Fremdschlüssel Eigenschaft: `BlogId: 2 FK`</span><span class="sxs-lookup"><span data-stu-id="baf29-143">`Post.BlogId` is a foreign key property: `BlogId: 2 FK`</span></span>
- <span data-ttu-id="baf29-144">`BlogAssets.Id` ist sowohl eine Primärschlüssel-als auch eine Fremdschlüssel Eigenschaft: `Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK`</span><span class="sxs-lookup"><span data-stu-id="baf29-144">`BlogAssets.Id` is both a primary key and a foreign key property: `Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK`</span></span>

<span data-ttu-id="baf29-145">Eigenschaftswerte, die geändert wurden, werden als solche gekennzeichnet, und der ursprüngliche Wert der-Eigenschaft wird ebenfalls angezeigt.</span><span class="sxs-lookup"><span data-stu-id="baf29-145">Property values that have been modified are marked as such, and the original value of the property is also shown.</span></span> <span data-ttu-id="baf29-146">Beispiel: `Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'`.</span><span class="sxs-lookup"><span data-stu-id="baf29-146">For example, `Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'`.</span></span>

<span data-ttu-id="baf29-147">Schließlich `Added` geben Entitäten mit temporären Schlüsselwerten an, dass der Wert temporär ist.</span><span class="sxs-lookup"><span data-stu-id="baf29-147">Finally, `Added` entities with temporary key values indicate that the value is temporary.</span></span> <span data-ttu-id="baf29-148">Beispiel: `Id: -2147482643 PK Temporary`.</span><span class="sxs-lookup"><span data-stu-id="baf29-148">For example, `Id: -2147482643 PK Temporary`.</span></span>

#### <a name="navigation-values"></a><span data-ttu-id="baf29-149">Navigations Werte</span><span class="sxs-lookup"><span data-stu-id="baf29-149">Navigation values</span></span>

<span data-ttu-id="baf29-150">Navigations Werte werden mithilfe der Primärschlüssel Werte der Entitäten angezeigt, auf die der Navigations Verweis verweist.</span><span class="sxs-lookup"><span data-stu-id="baf29-150">Navigation values are displayed using the primary key values of the entities that the navigations reference.</span></span> <span data-ttu-id="baf29-151">In der obigen Ausgabe bezieht sich z. b. Post 3 auf Blog 2.</span><span class="sxs-lookup"><span data-stu-id="baf29-151">For example, in the output above, post 3 is related to blog 2.</span></span> <span data-ttu-id="baf29-152">Dies bedeutet, dass die `Post.Blog` Navigation auf die `Blog` Instanz mit der ID 2 verweist.</span><span class="sxs-lookup"><span data-stu-id="baf29-152">This means that the `Post.Blog` navigation points to the `Blog` instance with ID 2.</span></span> <span data-ttu-id="baf29-153">Dies wird als dargestellt `Blog: {Id: 2}` .</span><span class="sxs-lookup"><span data-stu-id="baf29-153">This is shown as `Blog: {Id: 2}`.</span></span>

<span data-ttu-id="baf29-154">Dasselbe gilt für Auflistungs Navigationen, mit dem Unterschied, dass es in diesem Fall mehrere Verwandte Entitäten geben kann.</span><span class="sxs-lookup"><span data-stu-id="baf29-154">The same thing happens for collection navigations, except that in this case there can be multiple related entities.</span></span> <span data-ttu-id="baf29-155">Beispielsweise enthält die Auflistungs Navigation `Blog.Posts` drei Entitäten mit den Schlüsselwerten 1, 2 und-2147482643.</span><span class="sxs-lookup"><span data-stu-id="baf29-155">For example, the collection navigation `Blog.Posts` contains three entities, with key values 1, 2, and -2147482643 respectively.</span></span> <span data-ttu-id="baf29-156">Dies wird als dargestellt `[{Id: 1}, {Id: 2}, {Id: -2147482643}]` .</span><span class="sxs-lookup"><span data-stu-id="baf29-156">This is shown as `[{Id: 1}, {Id: 2}, {Id: -2147482643}]`.</span></span>

## <a name="change-tracker-logging"></a><span data-ttu-id="baf29-157">Protokollierung protokollieren</span><span class="sxs-lookup"><span data-stu-id="baf29-157">Change tracker logging</span></span>

<span data-ttu-id="baf29-158">Der Change Tracker protokolliert Nachrichten bei, `Debug` <xref:Microsoft.Extensions.Logging.LogLevel> Wenn [Änderungen an der Eigenschaft oder Navigation](xref:core/change-tracking/debug-views)erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="baf29-158">The change tracker logs messages at the `Debug` <xref:Microsoft.Extensions.Logging.LogLevel> whenever it [detects property or navigation changes](xref:core/change-tracking/debug-views).</span></span> <span data-ttu-id="baf29-159">Wenn z. b. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> im Code am Anfang dieses Dokuments aufgerufen wird und die [Debugprotokollierung aktiviert ist](xref:core/logging-events-diagnostics/index), werden die folgenden Protokolle generiert:</span><span class="sxs-lookup"><span data-stu-id="baf29-159">For example, when <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is called in the code at the top of this document and [debug logging is enabled](xref:core/logging-events-diagnostics/index), then the following logs are generated:</span></span>

```output
dbug: 12/30/2020 13:52:44.815 CoreEventId.DetectChangesStarting[10800] (Microsoft.EntityFrameworkCore.ChangeTracking)
      DetectChanges starting for 'BlogsContext'.
dbug: 12/30/2020 13:52:44.818 CoreEventId.PropertyChangeDetected[10802] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The unchanged property 'Blog.Name' was detected as changed from '.NET Blog' to '.NET Blog (All new!)' and will be marked as modified for entity with key '{Id: 1}'.
dbug: 12/30/2020 13:52:44.820 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Blog' entity with key '{Id: 1}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Modified'.
dbug: 12/30/2020 13:52:44.821 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      1 entities were added and 0 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 1}'.
dbug: 12/30/2020 13:52:44.822 CoreEventId.ValueGenerated[10808] (Microsoft.EntityFrameworkCore.ChangeTracking)
      'BlogsContext' generated temporary value '-2147482638' for the property 'Id.Post'.
dbug: 12/30/2020 13:52:44.822 CoreEventId.StartedTracking[10806] (Microsoft.EntityFrameworkCore.ChangeTracking)
      Context 'BlogsContext' started tracking 'Post' entity with key '{Id: -2147482638}'.
dbug: 12/30/2020 13:52:44.827 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      0 entities were added and 1 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 2}'.
dbug: 12/30/2020 13:52:44.827 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Post' entity with key '{Id: 4}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Modified'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.CascadeDeleteOrphan[10003] (Microsoft.EntityFrameworkCore.Update)
      An entity of type 'Post' with key '{Id: 4}' changed to 'Deleted' state due to severed required relationship to its parent entity of type 'Blog'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Post' entity with key '{Id: 4}' tracked by 'BlogsContext' changed state from 'Modified' to 'Deleted'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      0 entities were added and 1 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 2}'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.CascadeDelete[10002] (Microsoft.EntityFrameworkCore.Update)
      A cascade state change of an entity of type 'PostTag' with key '{PostsId: 4, TagsId: 2}' to 'Deleted' occurred due to the deletion of its parent entity of type 'Post' with key '{Id: 4}'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'PostTag' entity with key '{PostsId: 4, TagsId: 2}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Deleted'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.DetectChangesCompleted[10801] (Microsoft.EntityFrameworkCore.ChangeTracking)
      DetectChanges completed for 'BlogsContext'.
```

<span data-ttu-id="baf29-160">In der folgenden Tabelle werden die Protokollierungs Meldungen der Änderungs Protokollierung zusammengefasst</span><span class="sxs-lookup"><span data-stu-id="baf29-160">The following table summaries the change tracker logging messages:</span></span>

| <span data-ttu-id="baf29-161">Ereignis-ID</span><span class="sxs-lookup"><span data-stu-id="baf29-161">Event ID</span></span>                                                                                                               | <span data-ttu-id="baf29-162">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="baf29-162">Description</span></span>
|:-----------------------------------------------------------------------------------------------------------------------|----
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesStarting?displayProperty=nameWithType>        | <span data-ttu-id="baf29-163"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> wird gestartet</span><span class="sxs-lookup"><span data-stu-id="baf29-163"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> is starting</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesCompleted?displayProperty=nameWithType>       | <span data-ttu-id="baf29-164"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> wurde abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="baf29-164"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> has completed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.PropertyChangeDetected?displayProperty=nameWithType>       | <span data-ttu-id="baf29-165">Ein normaler Eigenschafts Wert wurde geändert.</span><span class="sxs-lookup"><span data-stu-id="baf29-165">A normal property value has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ForeignKeyChangeDetected?displayProperty=nameWithType>     | <span data-ttu-id="baf29-166">Ein Fremdschlüssel-Eigenschafts Wert wurde geändert.</span><span class="sxs-lookup"><span data-stu-id="baf29-166">A foreign key property value has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.CollectionChangeDetected?displayProperty=nameWithType>     | <span data-ttu-id="baf29-167">In einer Auflistungs Navigation ohne überspringen wurden Verwandte Entitäten hinzugefügt oder entfernt.</span><span class="sxs-lookup"><span data-stu-id="baf29-167">A non-skip collection navigation has had related entities added or removed.</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ReferenceChangeDetected?displayProperty=nameWithType>      | <span data-ttu-id="baf29-168">Eine Verweis Navigation wurde geändert, um auf eine andere Entität zu zeigen, oder auf NULL festgelegt.</span><span class="sxs-lookup"><span data-stu-id="baf29-168">A reference navigation has been changed to point to another entity, or set to null</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StartedTracking?displayProperty=nameWithType>              | <span data-ttu-id="baf29-169">EF Core die Nachverfolgung einer Entität gestartet</span><span class="sxs-lookup"><span data-stu-id="baf29-169">EF Core started tracking an entity</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StateChanged?displayProperty=nameWithType>                 | <span data-ttu-id="baf29-170">Der einer <xref:Microsoft.EntityFrameworkCore.EntityState> Entität hat sich geändert.</span><span class="sxs-lookup"><span data-stu-id="baf29-170">The <xref:Microsoft.EntityFrameworkCore.EntityState> of an entity has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ValueGenerated?displayProperty=nameWithType>               | <span data-ttu-id="baf29-171">Für eine Eigenschaft wurde ein Wert generiert.</span><span class="sxs-lookup"><span data-stu-id="baf29-171">A value was generated for a property</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.SkipCollectionChangeDetected?displayProperty=nameWithType> | <span data-ttu-id="baf29-172">In einer Navigation zum Überspringen der Auflistung wurden Verwandte Entitäten hinzugefügt oder entfernt</span><span class="sxs-lookup"><span data-stu-id="baf29-172">A skip collection navigation has had related entities added or removed</span></span>

## <a name="the-model"></a><span data-ttu-id="baf29-173">Die Model-Komponente</span><span class="sxs-lookup"><span data-stu-id="baf29-173">The model</span></span>

<span data-ttu-id="baf29-174">Das Modell, das für die obigen Beispiele verwendet wird, enthält die folgenden Entitäts Typen:</span><span class="sxs-lookup"><span data-stu-id="baf29-174">The model used for the examples above contains the following entity types:</span></span>

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public Guid AssetsId { get; set; } // Alternate key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public Guid Id { get; set; } // Primary key and foreign key
    public byte[] Banner { get; set; }

    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; } // Foreign key
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
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Model)]

<span data-ttu-id="baf29-175">Das Modell wird größtenteils gemäß der Konvention konfiguriert, mit nur wenigen Zeilen in "onmodelcreating":</span><span class="sxs-lookup"><span data-stu-id="baf29-175">The model is mostly configured by convention, with just a few lines in OnModelCreating:</span></span>

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<Blog>()
            .Property(e => e.AssetsId)
            .ValueGeneratedOnAdd();

        modelBuilder
            .Entity<BlogAssets>()
            .HasOne(e => e.Blog)
            .WithOne(e => e.Assets)
            .HasForeignKey<BlogAssets>(e => e.Id)
            .HasPrincipalKey<Blog>(e => e.AssetsId);
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=OnModelCreating)]
