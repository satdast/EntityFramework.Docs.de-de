---
title: Zugreifen auf nach verfolgte Entitäten-EF Core
description: Verwenden von entityentry, dbcontext. Entries und dbset. local für den Zugriff auf nach verfolgte Entitäten
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/entity-entries
ms.openlocfilehash: f385016aba61535f33e34c622dd43ce6dc823fc5
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129593"
---
# <a name="accessing-tracked-entities"></a><span data-ttu-id="d0c33-103">Zugreifen auf verfolgte Entitäten</span><span class="sxs-lookup"><span data-stu-id="d0c33-103">Accessing Tracked Entities</span></span>

<span data-ttu-id="d0c33-104">Es gibt vier Haupt-APIs für den Zugriff auf Entitäten, die von verfolgt werden <xref:Microsoft.EntityFrameworkCore.DbContext> :</span><span class="sxs-lookup"><span data-stu-id="d0c33-104">There are four main APIs for accessing entities tracked by a <xref:Microsoft.EntityFrameworkCore.DbContext>:</span></span>

- <span data-ttu-id="d0c33-105"><xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> gibt eine- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> Instanz für eine angegebene Entitäts Instanz zurück.</span><span class="sxs-lookup"><span data-stu-id="d0c33-105"><xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> returns an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instance for a given entity instance.</span></span>
- <span data-ttu-id="d0c33-106"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType> gibt- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> Instanzen für alle nach verfolgten Entitäten oder für alle nach verfolgten Entitäten eines bestimmten Typs zurück.</span><span class="sxs-lookup"><span data-stu-id="d0c33-106"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType> returns <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instances for all tracked entities, or for all tracked entities of a given type.</span></span>
- <span data-ttu-id="d0c33-107"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> Suchen nach einer einzelnen Entität nach Primärschlüssel, suchen zuerst nach überwachten Entitäten und dann nach Bedarf eine Abfrage der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="d0c33-107"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> find a single entity by primary key, first looking in tracked entities, and then querying the database if needed.</span></span>
- <span data-ttu-id="d0c33-108"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> gibt tatsächliche Entitäten (keine entityentry-Instanzen) für Entitäten des Entitäts Typs zurück, der durch das dbset dargestellt wird.</span><span class="sxs-lookup"><span data-stu-id="d0c33-108"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns actual entities (not EntityEntry instances) for entities of the entity type represented by the DbSet.</span></span>

<span data-ttu-id="d0c33-109">Diese werden in den folgenden Abschnitten ausführlicher beschrieben.</span><span class="sxs-lookup"><span data-stu-id="d0c33-109">Each of these is described in more detail in the sections below.</span></span>

> [!TIP]
> <span data-ttu-id="d0c33-110">In diesem Dokument wird davon ausgegangen, dass die Entitäts Zustände und die Grundlagen EF Core Änderungs Nachverfolgung verstanden werden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-110">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="d0c33-111">Weitere Informationen zu diesen Themen finden Sie [unter Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="d0c33-111">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="d0c33-112">Sie können den gesamten Code in diesem Dokument ausführen und Debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AccessingTrackedEntities).</span><span class="sxs-lookup"><span data-stu-id="d0c33-112">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AccessingTrackedEntities).</span></span>

## <a name="using-dbcontextentry-and-entityentry-instances"></a><span data-ttu-id="d0c33-113">Verwenden von dbcontext. Entry-und entityentry-Instanzen</span><span class="sxs-lookup"><span data-stu-id="d0c33-113">Using DbContext.Entry and EntityEntry instances</span></span>

<span data-ttu-id="d0c33-114">Für jede überwachte Entität verfolgt Entity Framework Core (EF Core) Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d0c33-114">For each tracked entity, Entity Framework Core (EF Core) keeps track of:</span></span>

- <span data-ttu-id="d0c33-115">Der Gesamtzustand der Entität.</span><span class="sxs-lookup"><span data-stu-id="d0c33-115">The overall state of the entity.</span></span> <span data-ttu-id="d0c33-116">Dies ist eine von `Unchanged` , `Modified` , `Added` oder `Deleted` . Weitere Informationen finden Sie [unter Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="d0c33-116">This is one of `Unchanged`, `Modified`, `Added`, or `Deleted`; see [Change Tracking in EF Core](xref:core/change-tracking/index) for more information.</span></span>
- <span data-ttu-id="d0c33-117">Die Beziehungen zwischen überwachten Entitäten.</span><span class="sxs-lookup"><span data-stu-id="d0c33-117">The relationships between tracked entities.</span></span> <span data-ttu-id="d0c33-118">Beispielsweise der Blog, zu dem ein Beitrag gehört.</span><span class="sxs-lookup"><span data-stu-id="d0c33-118">For example, the blog to which a post belongs.</span></span>
- <span data-ttu-id="d0c33-119">Die "aktuellen Werte" von Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="d0c33-119">The "current values" of properties.</span></span>
- <span data-ttu-id="d0c33-120">Die "ursprünglichen Werte" der Eigenschaften, wenn diese Informationen verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="d0c33-120">The "original values" of properties, when this information is available.</span></span> <span data-ttu-id="d0c33-121">Ursprüngliche Werte sind die Eigenschaftswerte, die vorhanden waren, als die Entität von der Datenbank abgefragt wurde.</span><span class="sxs-lookup"><span data-stu-id="d0c33-121">Original values are the property values that existed when entity was queried from the database.</span></span>
- <span data-ttu-id="d0c33-122">Welche Eigenschaftswerte geändert wurden, seit Sie abgefragt wurden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-122">Which property values have been modified since they were queried.</span></span>
- <span data-ttu-id="d0c33-123">Weitere Informationen zu Eigenschafts Werten, z. b. ob der Wert [temporär](xref:core/change-tracking/miscellaneous#temporary-values)ist oder nicht.</span><span class="sxs-lookup"><span data-stu-id="d0c33-123">Other information about property values, such as whether or not the value is [temporary](xref:core/change-tracking/miscellaneous#temporary-values).</span></span>

<span data-ttu-id="d0c33-124">Das übergeben einer Entitäts Instanz an führt dazu, <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> dass ein Zugriff auf diese Informationen für die angegebene Entität bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-124">Passing an entity instance to <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> results in an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> providing access to this information for the given entity.</span></span> <span data-ttu-id="d0c33-125">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-125">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var blog = context.Blogs.Single(e => e.Id == 1);
        var entityEntry = context.Entry(blog);

-->
[!code-csharp[Using_DbContext_Entry_and_EntityEntry_instances_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbContext_Entry_and_EntityEntry_instances_1)]

<span data-ttu-id="d0c33-126">In den folgenden Abschnitten wird gezeigt, wie ein entityentry verwendet wird, um auf den Entitäts Status zuzugreifen und ihn zu bearbeiten, sowie den Zustand der Eigenschaften und Navigationen der Entität.</span><span class="sxs-lookup"><span data-stu-id="d0c33-126">The following sections show how to use an EntityEntry to access and manipulate entity state, as well as the state of the entity's properties and navigations.</span></span>

### <a name="working-with-the-entity"></a><span data-ttu-id="d0c33-127">Arbeiten mit der Entität</span><span class="sxs-lookup"><span data-stu-id="d0c33-127">Working with the entity</span></span>

<span data-ttu-id="d0c33-128">Die häufigste Verwendung von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> ist der Zugriff auf den aktuellen <xref:Microsoft.EntityFrameworkCore.EntityState> einer Entität.</span><span class="sxs-lookup"><span data-stu-id="d0c33-128">The most common use of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> is to access the current <xref:Microsoft.EntityFrameworkCore.EntityState> of an entity.</span></span> <span data-ttu-id="d0c33-129">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-129">For example:</span></span>

<!--
        var currentState = context.Entry(blog).State;
        if (currentState == EntityState.Unchanged)
        {
            context.Entry(blog).State = EntityState.Modified;
        }
-->
[!code-csharp[Work_with_the_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_1)]

<span data-ttu-id="d0c33-130">Die Entry-Methode kann auch für Entitäten verwendet werden, die noch nicht nachverfolgt wurden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-130">The Entry method can also be used on entities that are not yet tracked.</span></span> <span data-ttu-id="d0c33-131">Dadurch _wird die Nachverfolgung der Entität nicht gestartet_. der Zustand der Entität ist weiterhin `Detatched` .</span><span class="sxs-lookup"><span data-stu-id="d0c33-131">This _does not start tracking the entity_; the state of the entity is still `Detatched`.</span></span> <span data-ttu-id="d0c33-132">Der zurückgegebene entityentry kann jedoch verwendet werden, um den Entitäts Status zu ändern. an diesem Punkt wird die Entität im angegebenen Zustand nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-132">However, the returned EntityEntry can then be used to change the entity state, at which point the entity will become tracked in the given state.</span></span> <span data-ttu-id="d0c33-133">Mit dem folgenden Code wird beispielsweise die Nachverfolgung einer Blog Instanz wie folgt gestartet `Added` :</span><span class="sxs-lookup"><span data-stu-id="d0c33-133">For example, the following code will start tracking a Blog instance as `Added`:</span></span>

<!--
        var newBlog = new Blog();
        Debug.Assert(context.Entry(newBlog).State == EntityState.Detached);

        context.Entry(newBlog).State = EntityState.Added;
        Debug.Assert(context.Entry(newBlog).State == EntityState.Added);
-->
[!code-csharp[Work_with_the_entity_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_2)]

> [!TIP]
> <span data-ttu-id="d0c33-134">Anders als bei EF6 führt das Festlegen des Zustands einer einzelnen Entität nicht dazu, dass alle verbundenen Entitäten nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-134">Unlike in EF6, setting the state of an individual entity will not cause all connected entities to be tracked.</span></span> <span data-ttu-id="d0c33-135">Dies bewirkt, dass der Zustand auf die gleiche Weise als ein Vorgang auf niedrigerer Ebene festgelegt wird, der `Add` `Attach` `Update` auf ein gesamtes Diagramm von Entitäten angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="d0c33-135">This makes setting the state this way a lower-level operation than calling `Add`, `Attach`, or `Update`, which operate on an entire graph of entities.</span></span>

<span data-ttu-id="d0c33-136">In der folgenden Tabelle sind die Möglichkeiten für die Verwendung eines entityentry zum Arbeiten mit einer gesamten Entität zusammengefasst:</span><span class="sxs-lookup"><span data-stu-id="d0c33-136">The following table summarizes ways to use an EntityEntry to work with an entire entity:</span></span>

| <span data-ttu-id="d0c33-137">Entityentry-Member</span><span class="sxs-lookup"><span data-stu-id="d0c33-137">EntityEntry member</span></span>                                                                                         | <span data-ttu-id="d0c33-138">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="d0c33-138">Description</span></span>
|:-----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.State?displayProperty=nameWithType>         | <span data-ttu-id="d0c33-139">Ruft den der Entität ab und legt ihn fest <xref:Microsoft.EntityFrameworkCore.EntityState> .</span><span class="sxs-lookup"><span data-stu-id="d0c33-139">Gets and sets the <xref:Microsoft.EntityFrameworkCore.EntityState> of the entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Entity?displayProperty=nameWithType>        | <span data-ttu-id="d0c33-140">Ruft die Entitäts Instanz ab.</span><span class="sxs-lookup"><span data-stu-id="d0c33-140">Gets the entity instance.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Context?displayProperty=nameWithType>       | <span data-ttu-id="d0c33-141">Der <xref:Microsoft.EntityFrameworkCore.DbContext> , der diese Entität nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-141">The <xref:Microsoft.EntityFrameworkCore.DbContext> that is tracking this entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Metadata?displayProperty=nameWithType>      | <span data-ttu-id="d0c33-142"><xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> Metadaten für den Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="d0c33-142"><xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata for the type of entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.IsKeySet?displayProperty=nameWithType>      | <span data-ttu-id="d0c33-143">Gibt an, ob für die Entität der Schlüsselwert festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="d0c33-143">Whether or not the entity has had its key value set.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload?displayProperty=nameWithType>        | <span data-ttu-id="d0c33-144">Überschreibt Eigenschaftswerte mit aus der Datenbank gelesenen Werten.</span><span class="sxs-lookup"><span data-stu-id="d0c33-144">Overwrites property values with values read from the database.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> | <span data-ttu-id="d0c33-145">Erzwingt nur das Erkennen von Änderungen für diese Entität. siehe [Änderungs Erkennung und Benachrichtigungen](xref:core/change-tracking/change-detection).</span><span class="sxs-lookup"><span data-stu-id="d0c33-145">Forces detection of changes for this entity only; see [Change Detection and Notifications](xref:core/change-tracking/change-detection).</span></span>

### <a name="working-with-a-single-property"></a><span data-ttu-id="d0c33-146">Arbeiten mit einer einzelnen Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="d0c33-146">Working with a single property</span></span>

<span data-ttu-id="d0c33-147">Mehrere über Ladungen von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> ermöglichen den Zugriff auf Informationen über eine einzelne Eigenschaft einer Entität.</span><span class="sxs-lookup"><span data-stu-id="d0c33-147">Several overloads of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> allow access to information about an individual property of an entity.</span></span> <span data-ttu-id="d0c33-148">Verwenden Sie beispielsweise eine stark typisierte, fließend ähnliche API:</span><span class="sxs-lookup"><span data-stu-id="d0c33-148">For example, using a strongly-typed, fluent-like API:</span></span>

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property(e => e.Name);
-->
[!code-csharp[Work_with_a_single_property_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1a)]

<span data-ttu-id="d0c33-149">Der Eigenschaftsname kann stattdessen als Zeichenfolge übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-149">The property name can instead be passed as a string.</span></span> <span data-ttu-id="d0c33-150">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-150">For example:</span></span>

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property<string>("Name");
-->
[!code-csharp[Work_with_a_single_property_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1b)]

<span data-ttu-id="d0c33-151">Der zurückgegebene <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> kann dann verwendet werden, um auf Informationen über die Eigenschaft zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="d0c33-151">The returned <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> can then be used to access information about the property.</span></span> <span data-ttu-id="d0c33-152">Beispielsweise kann Sie verwendet werden, um den aktuellen Wert der-Eigenschaft für diese Entität zu erhalten und festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d0c33-152">For example, it can be used to get and set the current value of the property on this entity:</span></span>

<!--
            string currentValue = context.Entry(blog).Property(e => e.Name).CurrentValue;
            context.Entry(blog).Property(e => e.Name).CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1d)]

<span data-ttu-id="d0c33-153">Beide oben verwendeten Eigenschaften Methoden geben eine stark typisierte generische Instanz zurück <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> .</span><span class="sxs-lookup"><span data-stu-id="d0c33-153">Both of the Property methods used above return a strongly-typed generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> instance.</span></span> <span data-ttu-id="d0c33-154">Die Verwendung dieses generischen Typs wird bevorzugt, da Sie den Zugriff auf Eigenschaftswerte ohne [Boxing-Werttypen](/dotnet/csharp/programming-guide/types/boxing-and-unboxing)zulässt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-154">Using this generic type is preferred because it allows access to property values without [boxing value types](/dotnet/csharp/programming-guide/types/boxing-and-unboxing).</span></span> <span data-ttu-id="d0c33-155">Wenn der Typ der Entität oder Eigenschaft jedoch zur Kompilierzeit nicht bekannt ist, kann stattdessen ein nicht generischer Wert <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> abgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="d0c33-155">However, if the type of entity or property is not known at compile-time, then a non-generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> can be obtained instead:</span></span>

<!--
            var propertyEntry = context.Entry(blog).Property("Name");
-->
[!code-csharp[Work_with_a_single_property_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1c)]

<span data-ttu-id="d0c33-156">Dies ermöglicht den Zugriff auf Eigenschafts Informationen für jede Eigenschaft, unabhängig vom Typ, auf der Kosten der Boxing-Werttypen.</span><span class="sxs-lookup"><span data-stu-id="d0c33-156">This allows access to property information for any property regardless of its type, at the expense of boxing value types.</span></span> <span data-ttu-id="d0c33-157">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-157">For example:</span></span>

<!--
            object blog = context.Blogs.Single(e => e.Id == 1);

            object currentValue = context.Entry(blog).Property("Name").CurrentValue;
            context.Entry(blog).Property("Name").CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1e)]

<span data-ttu-id="d0c33-158">In der folgenden Tabelle werden die von PropertyEntry verfügbar gemachten Eigenschaften Informationen zusammengefasst:</span><span class="sxs-lookup"><span data-stu-id="d0c33-158">The following table summarizes property information exposed by PropertyEntry:</span></span>

| <span data-ttu-id="d0c33-159">PropertyEntry-Member</span><span class="sxs-lookup"><span data-stu-id="d0c33-159">PropertyEntry member</span></span>                               | <span data-ttu-id="d0c33-160">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="d0c33-160">Description</span></span>
|:-------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.CurrentValue?displayProperty=nameWithType>  | <span data-ttu-id="d0c33-161">Ruft den aktuellen Wert der-Eigenschaft ab und legt diesen fest.</span><span class="sxs-lookup"><span data-stu-id="d0c33-161">Gets and sets the current value of the property.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.OriginalValue?displayProperty=nameWithType> | <span data-ttu-id="d0c33-162">Ruft den ursprünglichen Wert der Eigenschaft ab, sofern verfügbar, oder legt ihn fest.</span><span class="sxs-lookup"><span data-stu-id="d0c33-162">Gets and sets the original value of the property, if available.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.EntityEntry?displayProperty=nameWithType>   | <span data-ttu-id="d0c33-163">Ein Rück Verweis auf die <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> für die Entität.</span><span class="sxs-lookup"><span data-stu-id="d0c33-163">A back reference to the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> for the entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.Metadata?displayProperty=nameWithType>          | <span data-ttu-id="d0c33-164"><xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> Metadaten für die Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="d0c33-164"><xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> metadata for the property.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified?displayProperty=nameWithType>        | <span data-ttu-id="d0c33-165">Gibt an, ob diese Eigenschaft als geändert markiert ist, und ermöglicht es, diesen Zustand zu ändern.</span><span class="sxs-lookup"><span data-stu-id="d0c33-165">Indicates whether this property is marked as modified, and allows this state to be changed.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary?displayProperty=nameWithType>       | <span data-ttu-id="d0c33-166">Gibt an, ob diese Eigenschaft als [temporär](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values)markiert ist, und ermöglicht es, diesen Zustand zu ändern.</span><span class="sxs-lookup"><span data-stu-id="d0c33-166">Indicates whether this property is marked as [temporary](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values), and allows this state to be changed.</span></span>

<span data-ttu-id="d0c33-167">Hinweise:</span><span class="sxs-lookup"><span data-stu-id="d0c33-167">Notes:</span></span>

- <span data-ttu-id="d0c33-168">Der ursprüngliche Wert einer Eigenschaft ist der Wert, den die Eigenschaft enthielt, als die Entität von der Datenbank abgefragt wurde.</span><span class="sxs-lookup"><span data-stu-id="d0c33-168">The original value of a property is the value that the property had when the entity was queried from the database.</span></span> <span data-ttu-id="d0c33-169">Ursprüngliche Werte sind jedoch nicht verfügbar, wenn die Entität getrennt und dann explizit an einen anderen dbcontext angefügt wurde, `Attach` z `Update` . b. mit oder.</span><span class="sxs-lookup"><span data-stu-id="d0c33-169">However, original values are not available if the entity was disconnected and then explicitly attached to another DbContext, for example with `Attach` or `Update`.</span></span> <span data-ttu-id="d0c33-170">In diesem Fall ist der ursprüngliche Wert, der zurückgegeben wird, mit dem aktuellen Wert identisch.</span><span class="sxs-lookup"><span data-stu-id="d0c33-170">In this case, the original value returned will be the same as the current value.</span></span>
- <span data-ttu-id="d0c33-171"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aktualisiert nur Eigenschaften, die als geändert markiert sind.</span><span class="sxs-lookup"><span data-stu-id="d0c33-171"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> will only update properties marked as modified.</span></span> <span data-ttu-id="d0c33-172">Legen Sie <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified> EF Core diese Eigenschaft auf "true" fest, um EF Core zu erzwingen, dass ein bestimmter Eigenschafts Wert aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="d0c33-172">Set <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified> to true to force EF Core to update a given property value, or set it to false to prevent EF Core from updating the property value.</span></span>
- <span data-ttu-id="d0c33-173">[Temporäre Werte](xref:core/change-tracking/miscellaneous) werden in der Regel von EF Core [Wert Generatoren](xref:core/modeling/generated-properties)generiert.</span><span class="sxs-lookup"><span data-stu-id="d0c33-173">[Temporary values](xref:core/change-tracking/miscellaneous) are typically generated by EF Core [value generators](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="d0c33-174">Wenn Sie den aktuellen Wert einer Eigenschaft festlegen, wird der temporäre Wert durch den angegebenen Wert ersetzt, und die Eigenschaft wird als nicht temporär markiert.</span><span class="sxs-lookup"><span data-stu-id="d0c33-174">Setting the current value of a property will replace the temporary value with the given value and mark the property as not temporary.</span></span> <span data-ttu-id="d0c33-175">Legen Sie diese Einstellung auf "true" fest <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary> , um zu erzwingen, dass ein Wert temporär ist, auch wenn er explizit</span><span class="sxs-lookup"><span data-stu-id="d0c33-175">Set <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary> to true to force a value to be temporary even after it has been explicitly set.</span></span>

### <a name="working-with-a-single-navigation"></a><span data-ttu-id="d0c33-176">Arbeiten mit einer einzelnen Navigation</span><span class="sxs-lookup"><span data-stu-id="d0c33-176">Working with a single navigation</span></span>

<span data-ttu-id="d0c33-177">Mehrere über Ladungen von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> ermöglichen den Zugriff auf Informationen über eine einzelne Navigation.</span><span class="sxs-lookup"><span data-stu-id="d0c33-177">Several overloads of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> allow access to information about an individual navigation.</span></span>

<span data-ttu-id="d0c33-178">Der Zugriff auf die Verweis Navigation zu einer einzelnen verknüpften Entität erfolgt über die- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> Methoden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-178">Reference navigations to a single related entity are accessed through the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> methods.</span></span> <span data-ttu-id="d0c33-179">Die Verweis Navigation verweist auf die 1-Seite der 1: n-Beziehungen und beide Seiten von 1: eins-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="d0c33-179">Reference navigations point to the "one" sides of one-to-many relationships, and both sides of one-to-one relationships.</span></span> <span data-ttu-id="d0c33-180">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-180">For example:</span></span>

<!--
        ReferenceEntry<Post, Blog> referenceEntry1 = context.Entry(post).Reference(e => e.Blog);
        ReferenceEntry<Post, Blog> referenceEntry2 = context.Entry(post).Reference<Blog>("Blog");
        ReferenceEntry referenceEntry3 = context.Entry(post).Reference("Blog");
-->
[!code-csharp[Work_with_a_single_navigation_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_1)]

<span data-ttu-id="d0c33-181">Navigationen können auch Auflistungen verwandter Entitäten sein, wenn Sie für die "vielen" Seiten von 1: n-und m:n-Beziehungen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-181">Navigations can also be collections of related entities when used for the "many" sides of one-to-many and many-to-many relationships.</span></span> <span data-ttu-id="d0c33-182">Die- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> Methoden werden für den Zugriff auf Auflistungs Navigation verwendet.</span><span class="sxs-lookup"><span data-stu-id="d0c33-182">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> methods are used to access collection navigations.</span></span> <span data-ttu-id="d0c33-183">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-183">For example:</span></span>

<!--
        CollectionEntry<Blog, Post> collectionEntry1 = context.Entry(blog).Collection(e => e.Posts);
        CollectionEntry<Blog, Post> collectionEntry2 = context.Entry(blog).Collection<Post>("Posts");
        CollectionEntry collectionEntry3 = context.Entry(blog).Collection("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2a)]

<span data-ttu-id="d0c33-184">Einige Vorgänge sind für alle Navigationen üblich.</span><span class="sxs-lookup"><span data-stu-id="d0c33-184">Some operations are common for all navigations.</span></span> <span data-ttu-id="d0c33-185">Auf diese kann über die-Methode sowohl für Verweis-als auch Auflistungs Navigation zugegriffen werden <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d0c33-185">These can be accessed for both reference and collection navigations using the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> method.</span></span> <span data-ttu-id="d0c33-186">Beachten Sie, dass nur nicht generischer Zugriff verfügbar ist, wenn Sie auf alle Navigations Zugriffen zugreifen.</span><span class="sxs-lookup"><span data-stu-id="d0c33-186">Note that only non-generic access is available when accessing all navigations together.</span></span> <span data-ttu-id="d0c33-187">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-187">For example:</span></span>

<!--
        NavigationEntry navigationEntry = context.Entry(blog).Navigation("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2b)]

<span data-ttu-id="d0c33-188">In der folgenden Tabelle sind die Verwendungsmöglichkeiten von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602> , und zusammengefasst <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> :</span><span class="sxs-lookup"><span data-stu-id="d0c33-188">The following table summarizes ways to use <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602>, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602>, and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry>:</span></span>

| <span data-ttu-id="d0c33-189">Navigationentry-Member</span><span class="sxs-lookup"><span data-stu-id="d0c33-189">NavigationEntry member</span></span>                                                                                    | <span data-ttu-id="d0c33-190">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="d0c33-190">Description</span></span>
|:----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.MemberEntry.CurrentValue?displayProperty=nameWithType> | <span data-ttu-id="d0c33-191">Ruft den aktuellen Wert der Navigation ab und legt diesen fest.</span><span class="sxs-lookup"><span data-stu-id="d0c33-191">Gets and sets the current value of the navigation.</span></span> <span data-ttu-id="d0c33-192">Dies ist die gesamte Sammlung für Auflistungs Navigation.</span><span class="sxs-lookup"><span data-stu-id="d0c33-192">This is the entire collection for collection navigations.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Metadata?displayProperty=nameWithType> | <span data-ttu-id="d0c33-193"><xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> Metadaten für die Navigation.</span><span class="sxs-lookup"><span data-stu-id="d0c33-193"><xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> metadata for the navigation.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.IsLoaded?displayProperty=nameWithType> | <span data-ttu-id="d0c33-194">Dient zum Abrufen oder Festlegen eines Werts, der angibt, ob die verknüpfte Entität oder Auflistung vollständig aus der Datenbank geladen wurde.</span><span class="sxs-lookup"><span data-stu-id="d0c33-194">Gets or sets a value indicating whether the related entity or collection has been fully loaded from the database.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Load?displayProperty=nameWithType>     | <span data-ttu-id="d0c33-195">Lädt die verknüpfte Entität oder Auflistung aus der Datenbank. siehe [Explizites Laden verwandter Daten](xref:core/querying/related-data/explicit).</span><span class="sxs-lookup"><span data-stu-id="d0c33-195">Loads the related entity or collection from the database; see [Explicit Loading of Related Data](xref:core/querying/related-data/explicit).</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Query?displayProperty=nameWithType>    | <span data-ttu-id="d0c33-196">Die Abfrage EF Core würde verwenden, um diese Navigation als zu laden `IQueryable` , der weiter zusammengesetzt werden kann. siehe [Explizites Laden](xref:core/querying/related-data/explicit)verknüpfter Daten.</span><span class="sxs-lookup"><span data-stu-id="d0c33-196">The query EF Core would use to load this navigation as an `IQueryable` that can be further composed; see [Explicit Loading of Related Data](xref:core/querying/related-data/explicit).</span></span>

### <a name="working-with-all-properties-of-an-entity"></a><span data-ttu-id="d0c33-197">Arbeiten mit allen Eigenschaften einer Entität</span><span class="sxs-lookup"><span data-stu-id="d0c33-197">Working with all properties of an entity</span></span>

<span data-ttu-id="d0c33-198"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType> gibt eine <xref:System.Collections.Generic.IEnumerable%601> von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> für jede Eigenschaft der Entität zurück.</span><span class="sxs-lookup"><span data-stu-id="d0c33-198"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType> returns an <xref:System.Collections.Generic.IEnumerable%601> of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> for every property of the entity.</span></span> <span data-ttu-id="d0c33-199">Dies kann verwendet werden, um eine Aktion für jede Eigenschaft der Entität auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d0c33-199">This can be used to perform an action for every property of the entity.</span></span> <span data-ttu-id="d0c33-200">So legen Sie beispielsweise eine beliebige DateTime-Eigenschaft auf fest `DateTime.Now` :</span><span class="sxs-lookup"><span data-stu-id="d0c33-200">For example, to set any DateTime property to `DateTime.Now`:</span></span>

<!--
        foreach (var propertyEntry in context.Entry(blog).Properties)
        {
            if (propertyEntry.Metadata.ClrType == typeof(DateTime))
            {
                propertyEntry.CurrentValue = DateTime.Now;
            }
        }
-->
[!code-csharp[Work_with_all_properties_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_1)]

<span data-ttu-id="d0c33-201">Außerdem enthält entityentry mehrere Methoden, um alle Eigenschaftswerte gleichzeitig zu erhalten und festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d0c33-201">In addition, EntityEntry contains several methods to get and set all property values at the same time.</span></span> <span data-ttu-id="d0c33-202">Diese Methoden verwenden die <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> -Klasse, die eine Auflistung von Eigenschaften und deren Werten darstellt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-202">These methods use the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> class, which represents a collection of properties and their values.</span></span> <span data-ttu-id="d0c33-203">PropertyValues können für die aktuellen oder ursprünglichen Werte oder für die Werte abgerufen werden, die derzeit in der Datenbank gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="d0c33-203">PropertyValues can be obtained for current or original values, or for the values as currently stored in the database.</span></span> <span data-ttu-id="d0c33-204">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-204">For example:</span></span>

<!--
        var currentValues = context.Entry(blog).CurrentValues;
        var originalValues = context.Entry(blog).OriginalValues;
        var databaseValues = context.Entry(blog).GetDatabaseValues();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2a)]

<span data-ttu-id="d0c33-205">Diese propertyValues-Objekte sind selbst nicht sehr nützlich.</span><span class="sxs-lookup"><span data-stu-id="d0c33-205">These PropertyValues objects are not very useful on their own.</span></span> <span data-ttu-id="d0c33-206">Allerdings können Sie kombiniert werden, um allgemeine Vorgänge auszuführen, die beim Bearbeiten von Entitäten erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="d0c33-206">However, they can be combined to perform common operations needed when manipulating entities.</span></span> <span data-ttu-id="d0c33-207">Dies ist nützlich, wenn Sie mit Datenübertragungs Objekten arbeiten und [Konflikte mit optimistischer](xref:core/saving/concurrency)Parallelität lösen.</span><span class="sxs-lookup"><span data-stu-id="d0c33-207">This is useful when working with data transfer objects and when resolving [optimistic concurrency conflicts](xref:core/saving/concurrency).</span></span> <span data-ttu-id="d0c33-208">In den folgenden Abschnitten werden einige Beispiele gezeigt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-208">The following sections show some examples.</span></span>

#### <a name="setting-current-or-original-values-from-an-entity-or-dto"></a><span data-ttu-id="d0c33-209">Festlegen aktueller oder ursprünglicher Werte aus einer Entität oder einem DTO</span><span class="sxs-lookup"><span data-stu-id="d0c33-209">Setting current or original values from an entity or DTO</span></span>

<span data-ttu-id="d0c33-210">Die aktuellen oder ursprünglichen Werte einer Entität können aktualisiert werden, indem Werte aus einem anderen Objekt kopiert werden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-210">The current or original values of an entity can be updated by copying values from another object.</span></span> <span data-ttu-id="d0c33-211">Angenommen, ein `BlogDto` Datenübertragungs Objekt (Data Transfer Object, dto) mit denselben Eigenschaften wie der Entitätstyp:</span><span class="sxs-lookup"><span data-stu-id="d0c33-211">For example, consider a `BlogDto` data transfer object (DTO) with the same properties as the entity type:</span></span>

<!--
public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
-->
[!code-csharp[BlogDto](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=BlogDto)]

<span data-ttu-id="d0c33-212">Dies kann verwendet werden, um die aktuellen Werte einer nach verfolgten Entität mit festzulegen <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="d0c33-212">This can be used to set the current values of a tracked entity using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType>:</span></span>

<!--
        var blogDto = new BlogDto { Id = 1, Name = "1unicorn2" };

        context.Entry(blog).CurrentValues.SetValues(blogDto);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2b)]

<span data-ttu-id="d0c33-213">Diese Technik wird manchmal beim Aktualisieren einer Entität mit Werten verwendet, die von einem Dienst-oder Client in einer n-Tier-Anwendung abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-213">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="d0c33-214">Beachten Sie, dass das verwendete Objekt nicht denselben Typ wie die Entität aufweisen muss, solange es über Eigenschaften verfügt, deren Namen mit denen der Entität übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="d0c33-214">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="d0c33-215">Im obigen Beispiel wird eine Instanz des DTO-Werts `BlogDto` verwendet, um die aktuellen Werte einer nach verfolgten `Blog` Entität festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d0c33-215">In the example above, an instance of the DTO `BlogDto` is used to set the current values of a tracked `Blog` entity.</span></span>

<span data-ttu-id="d0c33-216">Beachten Sie, dass Eigenschaften nur als geändert gekennzeichnet werden, wenn sich der festgelegte Wert vom aktuellen Wert unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="d0c33-216">Note that properties will only be marked as modified if the value set differs from the current value.</span></span>

#### <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="d0c33-217">Festlegen aktueller oder ursprünglicher Werte aus einem Wörterbuch</span><span class="sxs-lookup"><span data-stu-id="d0c33-217">Setting current or original values from a dictionary</span></span>

<span data-ttu-id="d0c33-218">Im vorherigen Beispiel wurden Werte aus einer Entität oder einer DTO-Instanz festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-218">The previous example set values from an entity or DTO instance.</span></span> <span data-ttu-id="d0c33-219">Das gleiche Verhalten ist verfügbar, wenn Eigenschaftswerte als Name-Wert-Paare in einem Wörterbuch gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-219">The same behavior is available when property values are stored as name/value pairs in a dictionary.</span></span> <span data-ttu-id="d0c33-220">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-220">For example:</span></span>

<!--
        var blogDictionary = new Dictionary<string, object>
        {
            ["Id"] = 1,
            ["Name"] = "1unicorn2"
        };

        context.Entry(blog).CurrentValues.SetValues(blogDictionary);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2d)]

#### <a name="setting-current-or-original-values-from-the-database"></a><span data-ttu-id="d0c33-221">Festlegen aktueller oder ursprünglicher Werte aus der Datenbank</span><span class="sxs-lookup"><span data-stu-id="d0c33-221">Setting current or original values from the database</span></span>

<span data-ttu-id="d0c33-222">Die aktuellen oder ursprünglichen Werte einer Entität können mit den neuesten Werten aus der Datenbank aktualisiert werden, indem <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> oder aufgerufen <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> und das zurückgegebene-Objekt verwendet wird, um aktuelle oder ursprüngliche Werte festzulegen, oder beides.</span><span class="sxs-lookup"><span data-stu-id="d0c33-222">The current or original values of an entity can be updated with the latest values from the database by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> or <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> and using the returned object to set current or original values, or both.</span></span> <span data-ttu-id="d0c33-223">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-223">For example:</span></span>

<!--
        var databaseValues = context.Entry(blog).GetDatabaseValues();
        context.Entry(blog).CurrentValues.SetValues(databaseValues);
        context.Entry(blog).OriginalValues.SetValues(databaseValues);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2c)]

#### <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="d0c33-224">Erstellen eines geklonten Objekts mit aktuellen, ursprünglichen oder Daten bankwerten</span><span class="sxs-lookup"><span data-stu-id="d0c33-224">Creating a cloned object containing current, original, or database values</span></span>

<span data-ttu-id="d0c33-225">Das propertyValues-Objekt, das von CurrentValues, OriginalValues oder getdatabasevalues zurückgegeben wird, kann verwendet werden, um mithilfe von einen Klon der Entität zu erstellen <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d0c33-225">The PropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType>.</span></span> <span data-ttu-id="d0c33-226">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-226">For example:</span></span>

<!--
var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2e)]

<span data-ttu-id="d0c33-227">Beachten Sie, dass `ToObject` eine neue-Instanz zurückgibt, die nicht von dbcontext nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="d0c33-227">Note that `ToObject` returns a new instance that is not tracked by the DbContext.</span></span> <span data-ttu-id="d0c33-228">Für das zurückgegebene Objekt sind auch keine Beziehungen auf andere Entitäten festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-228">The returned object also does not have any relationships set to other entities.</span></span>

<span data-ttu-id="d0c33-229">Das geklonte Objekt kann nützlich sein, um Probleme im Zusammenhang mit gleichzeitigen Aktualisierungen der Datenbank zu beheben, insbesondere bei der Datenbindung an Objekte eines bestimmten Typs.</span><span class="sxs-lookup"><span data-stu-id="d0c33-229">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially when data binding to objects of a certain type.</span></span> <span data-ttu-id="d0c33-230">Weitere [Informationen finden Sie](xref:core/saving/concurrency) unter vollständige Parallelität.</span><span class="sxs-lookup"><span data-stu-id="d0c33-230">See [optimistic concurrency](xref:core/saving/concurrency) for more information.</span></span>

### <a name="working-with-all-navigations-of-an-entity"></a><span data-ttu-id="d0c33-231">Arbeiten mit allen Navigationen einer Entität</span><span class="sxs-lookup"><span data-stu-id="d0c33-231">Working with all navigations of an entity</span></span>

<span data-ttu-id="d0c33-232"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType> Gibt einen <xref:System.Collections.Generic.IEnumerable%601> von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> für jede Navigation der Entität zurück.</span><span class="sxs-lookup"><span data-stu-id="d0c33-232"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType> returns an <xref:System.Collections.Generic.IEnumerable%601> of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> for every navigation of the entity.</span></span> <span data-ttu-id="d0c33-233"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> führen Sie dasselbe aus, aber beschränkt auf Verweis-bzw. Sammlungs Navigation.</span><span class="sxs-lookup"><span data-stu-id="d0c33-233"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> do the same thing, but restricted to reference or collection navigations respectively.</span></span> <span data-ttu-id="d0c33-234">Dies kann verwendet werden, um eine Aktion für jede Navigation der Entität auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d0c33-234">This can be used to perform an action for every navigation of the entity.</span></span> <span data-ttu-id="d0c33-235">So erzwingen Sie z. b. das Laden aller zugehörigen Entitäten:</span><span class="sxs-lookup"><span data-stu-id="d0c33-235">For example, to force loading of all related entities:</span></span>

<!--
        foreach (var navigationEntry in context.Entry(blog).Navigations)
        {
            navigationEntry.Load();
        }
-->
[!code-csharp[Work_with_all_navigations_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_navigations_of_an_entity_1)]

### <a name="working-with-all-members-of-an-entity"></a><span data-ttu-id="d0c33-236">Arbeiten mit allen Membern einer Entität</span><span class="sxs-lookup"><span data-stu-id="d0c33-236">Working with all members of an entity</span></span>

<span data-ttu-id="d0c33-237">Reguläre Eigenschaften und Navigations Eigenschaften haben unterschiedliche Zustände und Verhalten.</span><span class="sxs-lookup"><span data-stu-id="d0c33-237">Regular properties and navigation properties have different state and behavior.</span></span> <span data-ttu-id="d0c33-238">Es ist daher üblich, Navigationen und nicht-Navigationen separat zu verarbeiten, wie in den obigen Abschnitten gezeigt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-238">It is therefore common to process navigations and non-navigations separately, as shown in the sections above.</span></span> <span data-ttu-id="d0c33-239">Manchmal kann es jedoch nützlich sein, etwas mit einem beliebigen Member der Entität durchzuführen, unabhängig davon, ob es sich um eine reguläre Eigenschaft oder Navigation handelt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-239">However, sometimes it can be useful to do something with any member of the entity, regardless of whether it is a regular property or navigation.</span></span> <span data-ttu-id="d0c33-240"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType> werden zu diesem Zweck bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-240"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType> are provided for this purpose.</span></span> <span data-ttu-id="d0c33-241">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-241">For example:</span></span>

<!--
        foreach (var memberEntry in context.Entry(blog).Members)
        {
            Console.WriteLine(
                $"Member {memberEntry.Metadata.Name} is of type {memberEntry.Metadata.ClrType.ShortDisplayName()} and has value {memberEntry.CurrentValue}");
        }
-->
[!code-csharp[Work_with_all_members_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_members_of_an_entity_1)]

<span data-ttu-id="d0c33-242">Wenn Sie diesen Code in einem Blog aus dem Beispiel ausführen, wird die folgende Ausgabe generiert:</span><span class="sxs-lookup"><span data-stu-id="d0c33-242">Running this code on a blog from the sample generates the following output:</span></span>

```output
Member Id is of type int and has value 1
Member Name is of type string and has value .NET Blog
Member Posts is of type IList<Post> and has value System.Collections.Generic.List`1[Post]
```

> [!TIP]
> <span data-ttu-id="d0c33-243">In der [Debugansicht Änderungs nach](xref:core/change-tracking/debug-views) Verfolgung werden Informationen wie diese angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-243">The [change tracker debug view](xref:core/change-tracking/debug-views) shows information like this.</span></span> <span data-ttu-id="d0c33-244">Die Debugansicht für die gesamte Änderungs Nachverfolgung wird von der einzelnen einzelnen nach <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> verfolgten Entität generiert.</span><span class="sxs-lookup"><span data-stu-id="d0c33-244">The debug view for the entire change tracker is generated from the individual <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> of each tracked entity.</span></span>

## <a name="find-and-findasync"></a><span data-ttu-id="d0c33-245">Suchen und FindAsync</span><span class="sxs-lookup"><span data-stu-id="d0c33-245">Find and FindAsync</span></span>

<span data-ttu-id="d0c33-246"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> sind für die effiziente Suche einer einzelnen Entität konzipiert, wenn der primäre Schlüssel bekannt ist.</span><span class="sxs-lookup"><span data-stu-id="d0c33-246"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> are designed for efficient lookup of a single entity when its primary key is known.</span></span> <span data-ttu-id="d0c33-247">Sucht zuerst nach der Überprüfung, ob die Entität bereits nachverfolgt wird, und gibt dann die Entität sofort zurück</span><span class="sxs-lookup"><span data-stu-id="d0c33-247">Find first checks if the entity is already tracked, and if so returns the entity immediately.</span></span> <span data-ttu-id="d0c33-248">Eine Datenbankabfrage wird nur vorgenommen, wenn die Entität nicht lokal nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="d0c33-248">A database query is only made if the entity is not tracked locally.</span></span> <span data-ttu-id="d0c33-249">Sehen Sie sich beispielsweise den folgenden Code an, der die Suche zweimal für dieselbe Entität aufruft:</span><span class="sxs-lookup"><span data-stu-id="d0c33-249">For example, consider this code that calls Find twice for the same entity:</span></span>

<!--
        using var context = new BlogsContext();

        Console.WriteLine("First call to Find...");
        var blog1 = context.Blogs.Find(1);

        Console.WriteLine($"...found blog {blog1.Name}");

        Console.WriteLine();
        Console.WriteLine("Second call to Find...");
        var blog2 = context.Blogs.Find(1);
        Debug.Assert(blog1 == blog2);

        Console.WriteLine("...returned the same instance without executing a query.");
-->
[!code-csharp[Find_and_FindAsync_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_1)]

<span data-ttu-id="d0c33-250">Die Ausgabe dieses Codes (einschließlich EF Core Protokollierung) bei Verwendung von SQLite lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="d0c33-250">The output from this code (including EF Core logging) when using SQLite is:</span></span>

```output
First call to Find...
info: 12/29/2020 07:45:53.682 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (1ms) [Parameters=[@__p_0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
      SELECT "b"."Id", "b"."Name"
      FROM "Blogs" AS "b"
      WHERE "b"."Id" = @__p_0
      LIMIT 1
...found blog .NET Blog

Second call to Find...
...returned the same instance without executing a query.
```

<span data-ttu-id="d0c33-251">Beachten Sie, dass der erste-Befehl die Entität nicht lokal findet und somit eine Datenbankabfrage ausführt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-251">Notice that the first call does not find the entity locally and so executes a database query.</span></span> <span data-ttu-id="d0c33-252">Umgekehrt gibt der zweite-Befehl dieselbe Instanz zurück, ohne die Datenbank abzufragen, weil Sie bereits nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="d0c33-252">Conversely, the second call returns the same instance without querying the database because it is already being tracked.</span></span>

<span data-ttu-id="d0c33-253">Find gibt NULL zurück, wenn eine Entität mit dem angegebenen Schlüssel nicht lokal nachverfolgt wird und nicht in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="d0c33-253">Find returns null if an entity with the given key is not tracked locally and does not exist in the database.</span></span>

### <a name="composite-keys"></a><span data-ttu-id="d0c33-254">Zusammengesetzte Schlüssel</span><span class="sxs-lookup"><span data-stu-id="d0c33-254">Composite keys</span></span>

<span data-ttu-id="d0c33-255">Find kann auch mit zusammengesetzten Schlüsseln verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-255">Find can also be used with composite keys.</span></span> <span data-ttu-id="d0c33-256">Stellen Sie sich beispielsweise eine `OrderLine` Entität mit einem zusammengesetzten Schlüssel vor, der aus der Bestell-ID und der Produkt-ID besteht:</span><span class="sxs-lookup"><span data-stu-id="d0c33-256">For example, consider an `OrderLine` entity with a composite key consisting of the order ID and the product ID:</span></span>

<!--
public class OrderLine
{
    public int OrderId { get; set; }
    public int ProductId { get; set; }

    //...
}
-->
[!code-csharp[OrderLine](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OrderLine)]

<span data-ttu-id="d0c33-257">Der zusammengesetzte Schlüssel muss in konfiguriert werden <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> , um die Schlüsselteile _und deren Reihenfolge_ zu definieren.</span><span class="sxs-lookup"><span data-stu-id="d0c33-257">The composite key must be configured in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> to define the key parts _and their order_.</span></span> <span data-ttu-id="d0c33-258">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-258">For example:</span></span>

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<OrderLine>()
            .HasKey(e => new { e.OrderId, e.ProductId });
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OnModelCreating)]

<span data-ttu-id="d0c33-259">Beachten Sie, dass `OrderId` der erste Teil des Schlüssels und `ProductId` der zweite Teil des Schlüssels ist.</span><span class="sxs-lookup"><span data-stu-id="d0c33-259">Notice that `OrderId` is the first part of the key and `ProductId` is the second part of the key.</span></span> <span data-ttu-id="d0c33-260">Diese Reihenfolge muss beim Übergeben von Schlüsselwerten für die Suche verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-260">This order must be used when passing key values to Find.</span></span> <span data-ttu-id="d0c33-261">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-261">For example:</span></span>

<!--
        var orderline = context.OrderLines.Find(orderId, productId);
-->
[!code-csharp[Find_and_FindAsync_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_2)]

## <a name="using-changetrackerentries-to-access-all-tracked-entities"></a><span data-ttu-id="d0c33-262">Verwenden von ChangeTracker. Entries für den Zugriff auf alle nach verfolgten Entitäten</span><span class="sxs-lookup"><span data-stu-id="d0c33-262">Using ChangeTracker.Entries to access all tracked entities</span></span>

<span data-ttu-id="d0c33-263">Bisher haben wir nur jeweils ein einzelnes-Paar aufgerufen <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> .</span><span class="sxs-lookup"><span data-stu-id="d0c33-263">So far we have accessed only a single <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> at a time.</span></span> <span data-ttu-id="d0c33-264"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> Gibt einen entityentry für jede Entität zurück, die derzeit von dbcontext nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="d0c33-264"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> returns an EntityEntry for every entity currently tracked by the DbContext.</span></span> <span data-ttu-id="d0c33-265">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-265">For example:</span></span>

<!--
        using var context = new BlogsContext();
        var blogs = context.Blogs.Include(e => e.Posts).ToList();

        foreach (var entityEntry in context.ChangeTracker.Entries())
        {
            Console.WriteLine($"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property("Id").CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a)]

<span data-ttu-id="d0c33-266">Dieser Code generiert die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="d0c33-266">This code generates the following output:</span></span>

```output
Found Blog entity with ID 1
Found Post entity with ID 1
Found Post entity with ID 2
```

<span data-ttu-id="d0c33-267">Beachten Sie, dass Einträge für beide Blogs und Beiträge zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-267">Notice that entries for both blogs and posts are returned.</span></span> <span data-ttu-id="d0c33-268">Die Ergebnisse können stattdessen mithilfe der generischen Überladung zu einem bestimmten Entitätstyp gefiltert werden <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="d0c33-268">The results can instead be filtered to a specific entity type using the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> generic overload:</span></span>

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<Post>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b)]

<span data-ttu-id="d0c33-269">Die Ausgabe dieses Codes zeigt, dass nur Beiträge zurückgegeben werden:</span><span class="sxs-lookup"><span data-stu-id="d0c33-269">The output from this code shows that only posts are returned:</span></span>

```output
Found Post entity with ID 1
Found Post entity with ID 2
```

<span data-ttu-id="d0c33-270">Außerdem gibt die generische Überladung generische <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> Instanzen zurück.</span><span class="sxs-lookup"><span data-stu-id="d0c33-270">Also, using the generic overload returns generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instances.</span></span> <span data-ttu-id="d0c33-271">Dies ermöglicht es, dass in diesem Beispiel ein fließend ähnlicher Zugriff auf die-Eigenschaft möglich ist `Id` .</span><span class="sxs-lookup"><span data-stu-id="d0c33-271">This is what allows that fluent-like access to the `Id` property in this example.</span></span>

<span data-ttu-id="d0c33-272">Der zum Filtern verwendete generische Typ muss kein zugeordneter Entitätstyp sein. Stattdessen kann ein nicht zugeordneter Basistyp oder eine nicht zugeordnete Schnittstelle verwendet werden</span><span class="sxs-lookup"><span data-stu-id="d0c33-272">The generic type used for filtering does not have to be a mapped entity type; an unmapped base type or interface can be used instead.</span></span> <span data-ttu-id="d0c33-273">Wenn z. b. alle Entitäts Typen im Modell eine Schnittstelle implementieren, die ihre Schlüsseleigenschaft definiert:</span><span class="sxs-lookup"><span data-stu-id="d0c33-273">For example, if all the entity types in the model implement an interface defining their key property:</span></span>

<!--
public interface IEntityWithKey
{
    int Id { get; set; }
}
-->
[!code-csharp[IEntityWithKey](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=IEntityWithKey)]

<span data-ttu-id="d0c33-274">Diese Schnittstelle kann dann verwendet werden, um mit dem Schlüssel einer nach verfolgten Entität in einer stark typisierten Weise zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="d0c33-274">Then this interface can be used to work with the key of any tracked entity in a strongly-typed manner.</span></span> <span data-ttu-id="d0c33-275">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-275">For example:</span></span>

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<IEntityWithKey>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c)]

## <a name="using-dbsetlocal-to-query-tracked-entities"></a><span data-ttu-id="d0c33-276">Abfragen von nach verfolgten Entitäten mithilfe von "dbset. local"</span><span class="sxs-lookup"><span data-stu-id="d0c33-276">Using DbSet.Local to query tracked entities</span></span>

<span data-ttu-id="d0c33-277">EF Core Abfragen werden stets für die Datenbank ausgeführt, und es werden nur Entitäten zurückgegeben, die in der Datenbank gespeichert wurden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-277">EF Core queries are always executed on the database, and only return entities that have been saved to the database.</span></span> <span data-ttu-id="d0c33-278"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> stellt einen Mechanismus zum Abfragen von dbcontext für lokale, nach verfolgte Entitäten bereit.</span><span class="sxs-lookup"><span data-stu-id="d0c33-278"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> provides a mechanism to query the DbContext for local, tracked entities.</span></span>

<span data-ttu-id="d0c33-279">Da `DbSet.Local` verwendet wird, um nach verfolgte Entitäten abzufragen, ist es typisch, Entitäten in den dbcontext zu laden und dann mit diesen geladenen Entitäten zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="d0c33-279">Since `DbSet.Local` is used to query tracked entities, it is typical to load entities into the DbContext and then work with those loaded entities.</span></span> <span data-ttu-id="d0c33-280">Dies gilt insbesondere für die Datenbindung, kann aber auch in anderen Situationen nützlich sein.</span><span class="sxs-lookup"><span data-stu-id="d0c33-280">This is especially true for data binding, but can also be useful in other situations.</span></span> <span data-ttu-id="d0c33-281">Im folgenden Code wird die Datenbank z. b. zuerst nach allen Blogs und Beiträgen abgefragt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-281">For example, in the following code the database is first queried for all blogs and posts.</span></span> <span data-ttu-id="d0c33-282">Die- <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A> Erweiterungsmethode wird verwendet, um diese Abfrage mit den vom Kontext nach verfolgten Ergebnissen auszuführen, ohne direkt an die Anwendung zurückgegeben zu werden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-282">The <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A> extension method is used to execute this query with the results tracked by the context without being returned directly to the application.</span></span> <span data-ttu-id="d0c33-283">(Die Verwendung `ToList` von oder ähnlichem hat denselben Effekt, aber mit dem Aufwand zum Erstellen der zurückgegebenen Liste, der hier nicht benötigt wird.) Im Beispiel wird dann verwendet `DbSet.Local` , um auf die lokal überwachten Entitäten zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="d0c33-283">(Using `ToList` or similar has the same effect but with the overhead of creating the returned list, which is not needed here.) The example then uses `DbSet.Local` to access the locally tracked entities:</span></span>

<!--
        using var context = new BlogsContext();

        context.Blogs.Include(e => e.Posts).Load();

        foreach (var blog in context.Blogs.Local)
        {
            Console.WriteLine($"Blog: {blog.Name}");
        }

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_1)]

<span data-ttu-id="d0c33-284">Beachten Sie, dass im Gegensatz zu <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> `DbSet.Local` Entitäts Instanzen direkt zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-284">Notice that, unlike <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType>, `DbSet.Local` returns entity instances directly.</span></span> <span data-ttu-id="d0c33-285">Ein entityentry kann natürlich immer für die zurückgegebene Entität abgerufen werden, indem aufgerufen wird <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="d0c33-285">An EntityEntry can, of course, always be obtained for the returned entity by calling <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType>.</span></span>

### <a name="the-local-view"></a><span data-ttu-id="d0c33-286">Die lokale Ansicht</span><span class="sxs-lookup"><span data-stu-id="d0c33-286">The local view</span></span>

<span data-ttu-id="d0c33-287"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> gibt eine Ansicht der lokal überwachten Entitäten zurück, die die aktuellen <xref:Microsoft.EntityFrameworkCore.EntityState> dieser Entitäten widerspiegeln.</span><span class="sxs-lookup"><span data-stu-id="d0c33-287"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns a view of locally tracked entities that reflects the current <xref:Microsoft.EntityFrameworkCore.EntityState> of those entities.</span></span> <span data-ttu-id="d0c33-288">Dies bedeutet insbesondere Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d0c33-288">Specifically, this means that:</span></span>

- <span data-ttu-id="d0c33-289">`Added` Entitäten sind eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="d0c33-289">`Added` entities are included.</span></span> <span data-ttu-id="d0c33-290">Beachten Sie, dass dies für normale EF Core Abfragen nicht der Fall ist, da `Added` Entitäten noch nicht in der Datenbank vorhanden sind und daher nie von einer Datenbankabfrage zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-290">Note that this is not the case for normal EF Core queries, since `Added` entities do not yet exist in the database and so are therefore never returned by a database query.</span></span>
- <span data-ttu-id="d0c33-291">`Deleted` Entitäten werden ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="d0c33-291">`Deleted` entities are excluded.</span></span> <span data-ttu-id="d0c33-292">Beachten Sie, dass dies für normale EF Core Abfragen noch nicht der Fall ist, da `Deleted` Entitäten in der Datenbank noch vorhanden sind und von Datenbankabfragen zurückgegeben _werden_ .</span><span class="sxs-lookup"><span data-stu-id="d0c33-292">Note that this is again not the case for normal EF Core queries, since `Deleted` entities still exist in the database and so _are_ returned by database queries.</span></span>

<span data-ttu-id="d0c33-293">Dies bedeutet, dass `DbSet.Local` die Daten anzeigen, die den aktuellen konzeptionellen Zustand des Entitäts Diagramms widerspiegeln, wobei `Added` enthaltene Entitäten und `Deleted` Entitäten ausgeschlossen sind.</span><span class="sxs-lookup"><span data-stu-id="d0c33-293">All of this means that `DbSet.Local` is view over the data that reflects the current conceptual state of the entity graph, with `Added` entities included and `Deleted` entities excluded.</span></span> <span data-ttu-id="d0c33-294">Dies entspricht dem Namen des Daten Bank Zustands, der nach dem Aufruf von SaveChanges erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="d0c33-294">This matches what database state is expected to be after SaveChanges is called.</span></span>

<span data-ttu-id="d0c33-295">Dies ist in der Regel die ideale Ansicht für die Datenbindung, da dem Benutzer die Daten entsprechend den von der Anwendung vorgenommenen Änderungen angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-295">This is typically the ideal view for data binding, since it presents to the user the data as they understand it based on the changes made by the application.</span></span>

<span data-ttu-id="d0c33-296">Der folgende Code zeigt, wie ich einen Beitrag als markiere `Deleted` und dann einen neuen Beitrag zufüge. `Added`</span><span class="sxs-lookup"><span data-stu-id="d0c33-296">The following code demonstrates this my marking one post as `Deleted` and then adding a new post, marking it as `Added`:</span></span>

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Remove(posts[1]);

        context.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_2)]

<span data-ttu-id="d0c33-297">Die Ausgabe dieses Codes lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="d0c33-297">The output from this code is:</span></span>

```output
Local view after loading posts:
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing F# 5
  Post: Announcing .NET 5.0
Local view after adding and deleting posts:
  Post: What’s next for System.Text.Json?
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing .NET 5.0
```

<span data-ttu-id="d0c33-298">Beachten Sie, dass der gelöschte Beitrag aus der lokalen Ansicht entfernt wird und der hinzugefügte Beitrag enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="d0c33-298">Notice that the deleted post is removed from the local view, and the added post is included.</span></span>

### <a name="using-local-to-add-and-remove-entities"></a><span data-ttu-id="d0c33-299">Verwenden von local zum Hinzufügen und Entfernen von Entitäten</span><span class="sxs-lookup"><span data-stu-id="d0c33-299">Using Local to add and remove entities</span></span>

<span data-ttu-id="d0c33-300"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> gibt eine Instanz von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601> zurück.</span><span class="sxs-lookup"><span data-stu-id="d0c33-300"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns an instance of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601>.</span></span> <span data-ttu-id="d0c33-301">Dies ist eine Implementierung von <xref:System.Collections.Generic.ICollection%601> , die Benachrichtigungen generiert und antwortet, wenn Entitäten hinzugefügt und aus der Auflistung entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-301">This is an implementation of <xref:System.Collections.Generic.ICollection%601> that generates and responds to notifications when entities are added and removed from the collection.</span></span> <span data-ttu-id="d0c33-302">(Dies ist das gleiche Konzept wie <xref:System.Collections.ObjectModel.ObservableCollection%601> , wird jedoch als Projektion über vorhandene EF Core Änderungs nach Verfolgungs Einträge implementiert, nicht als unabhängige Sammlung.)</span><span class="sxs-lookup"><span data-stu-id="d0c33-302">(This is the same concept as <xref:System.Collections.ObjectModel.ObservableCollection%601>, but implemented as a projection over existing EF Core change tracking entries, rather than as an independent collection.)</span></span>

<span data-ttu-id="d0c33-303">Die Benachrichtigungen der lokalen Ansicht werden mit der dbcontext-Änderungs Nachverfolgung verknüpft, sodass die lokale Ansicht mit dbcontext synchron bleibt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-303">The local view's notifications are hooked into DbContext change tracking such that the local view stays in sync with the DbContext.</span></span> <span data-ttu-id="d0c33-304">Dies gilt insbesondere in folgenden Fällen:</span><span class="sxs-lookup"><span data-stu-id="d0c33-304">Specifically:</span></span>

- <span data-ttu-id="d0c33-305">Wenn eine neue Entität hinzugefügt `DbSet.Local` wird, wird Sie von dbcontext nachverfolgt, in der Regel im- `Added` Zustand.</span><span class="sxs-lookup"><span data-stu-id="d0c33-305">Adding a new entity to `DbSet.Local` causes it to be tracked by the DbContext, typically in the `Added` state.</span></span> <span data-ttu-id="d0c33-306">(Wenn die Entität bereits einen generierten Schlüsselwert aufweist, wird Sie stattdessen als nachverfolgt `Unchanged` .)</span><span class="sxs-lookup"><span data-stu-id="d0c33-306">(If the entity already has a generated key value, then it is tracked as `Unchanged` instead.)</span></span>
- <span data-ttu-id="d0c33-307">Wenn eine Entität aus entfernt `DbSet.Local` wird, wird Sie als gekennzeichnet `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="d0c33-307">Removing an entity from `DbSet.Local` causes it to be marked as `Deleted`.</span></span>
- <span data-ttu-id="d0c33-308">Eine Entität, die von dbcontext nachverfolgt wird, wird automatisch in der Auflistung angezeigt `DbSet.Local` .</span><span class="sxs-lookup"><span data-stu-id="d0c33-308">An entity that becomes tracked by the DbContext will automatically appear in the `DbSet.Local` collection.</span></span> <span data-ttu-id="d0c33-309">Wenn Sie z. b. eine Abfrage ausführen, um weitere Entitäten zu aktivieren, wird die lokale Ansicht aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="d0c33-309">For example, executing a query to bring in more entities automatically causes the local view to be updated.</span></span>
- <span data-ttu-id="d0c33-310">Eine Entität, die als markiert ist, `Deleted` wird automatisch aus der lokalen Auflistung entfernt.</span><span class="sxs-lookup"><span data-stu-id="d0c33-310">An entity that is marked as `Deleted` will be removed from the local collection automatically.</span></span>

<span data-ttu-id="d0c33-311">Dies bedeutet, dass die lokale Ansicht verwendet werden kann, um überwachte Entitäten einfach durch Hinzufügen und entfernen aus der Auflistung zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="d0c33-311">This means the local view can be used to manipulate tracked entities simply by adding and removing from the collection.</span></span> <span data-ttu-id="d0c33-312">Beispielsweise können Sie den vorherigen Beispielcode ändern, um Beiträge in der lokalen Sammlung hinzuzufügen und zu entfernen:</span><span class="sxs-lookup"><span data-stu-id="d0c33-312">For example, lets modify the previous example code to add and remove posts from the local collection:</span></span>

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Posts.Local.Remove(posts[1]);

        context.Posts.Local.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_3](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_3)]

<span data-ttu-id="d0c33-313">Die Ausgabe bleibt unverändert im vorherigen Beispiel, da an der lokalen Ansicht vorgenommene Änderungen mit dbcontext synchronisiert werden.</span><span class="sxs-lookup"><span data-stu-id="d0c33-313">The output remains unchanged from the previous example because changes made to the local view are synced with the DbContext.</span></span>

### <a name="using-the-local-view-for-windows-forms-or-wpf-data-binding"></a><span data-ttu-id="d0c33-314">Verwenden der lokalen Ansicht für Windows Forms-oder WPF-Datenbindung</span><span class="sxs-lookup"><span data-stu-id="d0c33-314">Using the local view for Windows Forms or WPF data binding</span></span>

<span data-ttu-id="d0c33-315"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> bildet die Basis für die Datenbindung an EF Core Entitäten.</span><span class="sxs-lookup"><span data-stu-id="d0c33-315"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> forms the basis for data binding to EF Core entities.</span></span> <span data-ttu-id="d0c33-316">Allerdings funktionieren sowohl Windows Forms als auch WPF am besten, wenn Sie mit dem spezifischen Typ der Benachrichtigungs Sammlung verwendet werden, die Sie erwarten.</span><span class="sxs-lookup"><span data-stu-id="d0c33-316">However, both Windows Forms and WPF work best when used with the specific type of notifying collection that they expect.</span></span> <span data-ttu-id="d0c33-317">Die lokale Ansicht unterstützt das Erstellen dieser speziellen Sammlungs Typen:</span><span class="sxs-lookup"><span data-stu-id="d0c33-317">The local view supports creating these specific collection types:</span></span>

- <span data-ttu-id="d0c33-318"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType> Gibt einen <xref:System.Collections.ObjectModel.ObservableCollection%601> für die WPF-Datenbindung zurück.</span><span class="sxs-lookup"><span data-stu-id="d0c33-318"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType> returns an <xref:System.Collections.ObjectModel.ObservableCollection%601> for WPF data binding.</span></span>
- <span data-ttu-id="d0c33-319"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType> Gibt einen <xref:System.ComponentModel.BindingList%601> für Windows Forms Datenbindung zurück.</span><span class="sxs-lookup"><span data-stu-id="d0c33-319"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType> returns a <xref:System.ComponentModel.BindingList%601> for Windows Forms data binding.</span></span>

<span data-ttu-id="d0c33-320">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d0c33-320">For example:</span></span>

<!--
        ObservableCollection<Post> observableCollection = context.Posts.Local.ToObservableCollection();
        BindingList<Post> bindingList = context.Posts.Local.ToBindingList();
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_4](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_4)]

<span data-ttu-id="d0c33-321">Weitere Informationen zur WPF-Datenbindung mit EF Core finden [Sie unter Get Started with WPF](xref:core/get-started/wpf) .</span><span class="sxs-lookup"><span data-stu-id="d0c33-321">See [Get Started with WPF](xref:core/get-started/wpf) for more information on WPF data binding with EF Core.</span></span>

> [!TIP]
> <span data-ttu-id="d0c33-322">Die lokale Ansicht für eine bestimmte dbset-Instanz wird verzögert erstellt, wenn zuerst darauf zugegriffen und dann zwischengespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="d0c33-322">The local view for a given DbSet instance is created lazily when first accessed and then cached.</span></span> <span data-ttu-id="d0c33-323">Die localview-Erstellung selbst ist schnell und verwendet keinen signifikanten Arbeitsspeicher.</span><span class="sxs-lookup"><span data-stu-id="d0c33-323">LocalView creation itself is fast and it does not use significant memory.</span></span> <span data-ttu-id="d0c33-324">Es wird jedoch " [DetectChanges](xref:core/change-tracking/change-detection)" aufgerufen, was für eine große Anzahl von Entitäten langsam sein kann.</span><span class="sxs-lookup"><span data-stu-id="d0c33-324">However, it does call [DetectChanges](xref:core/change-tracking/change-detection), which can be slow for large numbers of entities.</span></span> <span data-ttu-id="d0c33-325">Die von und erstellten `ToObservableCollection` Sammlungen `ToBindingList` werden ebenfalls verzögert und dann zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="d0c33-325">The collections created by `ToObservableCollection` and `ToBindingList` are also created lazily and and then cached.</span></span> <span data-ttu-id="d0c33-326">Beide Methoden erstellen neue Auflistungen, die langsam sein können und viel Arbeitsspeicher nutzen, wenn Tausende von Entitäten beteiligt sind.</span><span class="sxs-lookup"><span data-stu-id="d0c33-326">Both of these methods create new collections, which can be slow and use a lot of memory when thousands of entities are involved.</span></span>
