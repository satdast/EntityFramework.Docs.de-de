---
title: Identitäts Auflösung-EF Core
description: Auflösen mehrerer Entitäts Instanzen in eine einzelne Instanz mithilfe von Primärschlüssel Werten
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/identity-resolution
ms.openlocfilehash: f94b61371dcead27853799719dabc7849500d466
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129604"
---
# <a name="identity-resolution-in-ef-core"></a><span data-ttu-id="47228-103">Identitäts Auflösung in EF Core</span><span class="sxs-lookup"><span data-stu-id="47228-103">Identity Resolution in EF Core</span></span>

<span data-ttu-id="47228-104">Ein <xref:Microsoft.EntityFrameworkCore.DbContext> kann nur eine Entitäts Instanz mit einem beliebigen Primärschlüssel Wert verfolgen.</span><span class="sxs-lookup"><span data-stu-id="47228-104">A <xref:Microsoft.EntityFrameworkCore.DbContext> can only track one entity instance with any given primary key value.</span></span> <span data-ttu-id="47228-105">Dies bedeutet, dass mehrere Instanzen einer Entität mit demselben Schlüsselwert in eine einzelne Instanz aufgelöst werden müssen.</span><span class="sxs-lookup"><span data-stu-id="47228-105">This means multiple instances of an entity with the same key value must be resolved to a single instance.</span></span> <span data-ttu-id="47228-106">Dies wird als "Identitäts Auflösung" bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="47228-106">This is called "identity resolution".</span></span> <span data-ttu-id="47228-107">Die Identitäts Auflösung stellt sicher, dass Entity Framework Core (EF Core) ein konsistentes Diagramm ohne Mehrdeutigkeiten zu den Beziehungen oder Eigenschafts Werten der Entitäten nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="47228-107">Identity resolution ensures Entity Framework Core (EF Core) is tracking a consistent graph with no ambiguities about the relationships or property values of the entities.</span></span>

> [!TIP]
> <span data-ttu-id="47228-108">In diesem Dokument wird davon ausgegangen, dass die Entitäts Zustände und die Grundlagen EF Core Änderungs Nachverfolgung verstanden werden.</span><span class="sxs-lookup"><span data-stu-id="47228-108">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="47228-109">Weitere Informationen zu diesen Themen finden Sie [unter Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="47228-109">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="47228-110">Sie können den gesamten Code in diesem Dokument ausführen und Debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/IdentityResolutionInEFCore).</span><span class="sxs-lookup"><span data-stu-id="47228-110">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/IdentityResolutionInEFCore).</span></span>

## <a name="introduction"></a><span data-ttu-id="47228-111">Einführung</span><span class="sxs-lookup"><span data-stu-id="47228-111">Introduction</span></span>

<span data-ttu-id="47228-112">Der folgende Code fragt eine Entität ab und versucht dann, eine andere Instanz mit demselben Primärschlüssel Wert anzufügen:</span><span class="sxs-lookup"><span data-stu-id="47228-112">The following code queries for an entity and then attempts to attach a different instance with the same primary key value:</span></span>

<!--
            using var context = new BlogsContext();

            var blogA = context.Blogs.Single(e => e.Id == 1);
            var blogB = new Blog { Id = 1, Name = ".NET Blog (All new!)" };

            try
            {
                context.Update(blogB); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Identity_Resolution_in_EF_Core_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Identity_Resolution_in_EF_Core_1)]

<span data-ttu-id="47228-113">Das Ausführen dieses Codes führt zu folgender Ausnahme:</span><span class="sxs-lookup"><span data-stu-id="47228-113">Running this code results in the following exception:</span></span>

> <span data-ttu-id="47228-114">System. InvalidOperationException: die Instanz des Entitäts Typs "Blog" kann nicht nachverfolgt werden, da bereits eine andere Instanz mit dem Schlüsselwert "{ID: 1}" nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="47228-114">System.InvalidOperationException: The instance of entity type 'Blog' cannot be tracked because another instance with the key value '{Id: 1}' is already being tracked.</span></span> <span data-ttu-id="47228-115">Wenn Sie vorhandene Entitäten anfügen, stellen Sie sicher, dass nur eine Entitäts Instanz mit einem angegebenen Schlüsselwert angefügt ist.</span><span class="sxs-lookup"><span data-stu-id="47228-115">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="47228-116">EF Core eine einzelne Instanz benötigt, weil Folgendes erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="47228-116">EF Core requires a single instance because:</span></span>

- <span data-ttu-id="47228-117">Eigenschaftswerte können sich zwischen mehreren Instanzen unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="47228-117">Property values may be different between multiple instances.</span></span> <span data-ttu-id="47228-118">Wenn Sie die Datenbank aktualisieren, muss EF Core wissen, welche Eigenschaftswerte verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="47228-118">When updating the database, EF Core needs to know which property values to use.</span></span>
- <span data-ttu-id="47228-119">Beziehungen mit anderen Entitäten können sich zwischen mehreren Instanzen unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="47228-119">Relationships with other entities may be different between multiple instances.</span></span> <span data-ttu-id="47228-120">Beispielsweise kann "bloga" mit einer anderen Sammlung von Beiträgen als "blogb" verknüpft werden.</span><span class="sxs-lookup"><span data-stu-id="47228-120">For example, "blogA" may be related to a different collection of posts than "blogB".</span></span>

<span data-ttu-id="47228-121">Die obige Ausnahme wird häufig in folgenden Situationen auftreten:</span><span class="sxs-lookup"><span data-stu-id="47228-121">The exception above is commonly encountered in these situations:</span></span>

- <span data-ttu-id="47228-122">Beim Versuch, eine Entität zu aktualisieren</span><span class="sxs-lookup"><span data-stu-id="47228-122">When attempting to update an entity</span></span>
- <span data-ttu-id="47228-123">Beim Versuch, ein serialisiertes Diagramm von Entitäten zu verfolgen</span><span class="sxs-lookup"><span data-stu-id="47228-123">When attempting to track a serialized graph of entities</span></span>
- <span data-ttu-id="47228-124">Wenn kein Schlüsselwert festgelegt wird, der nicht automatisch generiert wird</span><span class="sxs-lookup"><span data-stu-id="47228-124">When failing to set a key value that is not automatically generated</span></span>
- <span data-ttu-id="47228-125">Bei Wiederverwendung einer dbcontext-Instanz für mehrere Arbeitseinheiten</span><span class="sxs-lookup"><span data-stu-id="47228-125">When reusing a DbContext instance for multiple units-of-work</span></span>

<span data-ttu-id="47228-126">Jede dieser Situationen wird in den folgenden Abschnitten erläutert.</span><span class="sxs-lookup"><span data-stu-id="47228-126">Each of these situations is discussed in the following sections.</span></span>

## <a name="updating-an-entity"></a><span data-ttu-id="47228-127">Aktualisieren einer Entität</span><span class="sxs-lookup"><span data-stu-id="47228-127">Updating an entity</span></span>

<span data-ttu-id="47228-128">Es gibt verschiedene Methoden, um eine Entität mit neuen Werten zu aktualisieren, wie in [Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) und [expliziten Nachverfolgen von Entitäten](xref:core/change-tracking/explicit-tracking)beschrieben.</span><span class="sxs-lookup"><span data-stu-id="47228-128">There are several different approaches to update an entity with new values, as covered in [Change Tracking in EF Core](xref:core/change-tracking/index) and [Explicitly Tracking Entities](xref:core/change-tracking/explicit-tracking).</span></span> <span data-ttu-id="47228-129">Diese Vorgehensweisen sind unten im Kontext der Identitäts Auflösung aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="47228-129">These approaches are outlined below in the context of identity resolution.</span></span> <span data-ttu-id="47228-130">Ein wichtiger Punkt ist, dass jeder der Ansätze entweder eine Abfrage oder einen-aufrufswert von `Update` oder `Attach` , aber **_nie beides_** verwendet.</span><span class="sxs-lookup"><span data-stu-id="47228-130">An important point to notice is that each of the approaches use either a query or a call to one of `Update` or `Attach`, but **_never both_**.</span></span>

### <a name="call-update"></a><span data-ttu-id="47228-131">Update aktualisieren</span><span class="sxs-lookup"><span data-stu-id="47228-131">Call Update</span></span>

<span data-ttu-id="47228-132">Häufig wird die zu Aktualisier Ende Entität nicht aus einer Abfrage für den dbcontext stammen, die wir für SaveChanges verwenden werden.</span><span class="sxs-lookup"><span data-stu-id="47228-132">Often the entity to update does not come from a query on the DbContext that we are going to use for SaveChanges.</span></span> <span data-ttu-id="47228-133">Beispielsweise kann in einer Webanwendung eine Entitäts Instanz aus den Informationen in einer POST-Anforderung erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="47228-133">For example, in a web application, an entity instance may be created from the information in a POST request.</span></span> <span data-ttu-id="47228-134">Die einfachste Möglichkeit, dies zu behandeln, ist die Verwendung von <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> oder <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="47228-134">The simplest way to handle this is to use <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> or <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="47228-135">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="47228-135">For example:</span></span>

<!--
    public static void UpdateFromHttpPost1(Blog blog)
    {
        using var context = new BlogsContext();

        context.Update(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_1)]

<span data-ttu-id="47228-136">In diesem Fall:</span><span class="sxs-lookup"><span data-stu-id="47228-136">In this case:</span></span>

- <span data-ttu-id="47228-137">Es wird nur eine einzelne Instanz der Entität erstellt.</span><span class="sxs-lookup"><span data-stu-id="47228-137">Only a single instance of the entity is created.</span></span>
- <span data-ttu-id="47228-138">Die Entitäts Instanz wird **nicht** im Rahmen der Aktualisierung von der Datenbank abgefragt.</span><span class="sxs-lookup"><span data-stu-id="47228-138">The entity instance is **not** queried from the database as part of making the update.</span></span>
- <span data-ttu-id="47228-139">Alle Eigenschaftswerte werden in der Datenbank aktualisiert, unabhängig davon, ob Sie tatsächlich geändert wurden oder nicht.</span><span class="sxs-lookup"><span data-stu-id="47228-139">All property values will be updated in the database, regardless of whether they have actually changed or not.</span></span>
- <span data-ttu-id="47228-140">Ein Daten Bank Roundtrip wird durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="47228-140">One database round-trip is made.</span></span>

### <a name="query-then-apply-changes"></a><span data-ttu-id="47228-141">Abfrage dann Anwenden von Änderungen</span><span class="sxs-lookup"><span data-stu-id="47228-141">Query then apply changes</span></span>

<span data-ttu-id="47228-142">In der Regel ist nicht bekannt, welche Eigenschaftswerte tatsächlich geändert wurden, wenn eine Entität aus Informationen in einer POST-Anforderung oder ähnlich erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="47228-142">Usually it is not known which property values have actually been changed when an entity is created from information in a POST request or similar.</span></span> <span data-ttu-id="47228-143">Häufig ist es in Ordnung, alle Werte in der Datenbank zu aktualisieren, wie im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="47228-143">Often it is fine to just update all values in the database, as we did in the previous example.</span></span> <span data-ttu-id="47228-144">Wenn die Anwendung jedoch viele Entitäten verarbeitet und nur eine kleine Anzahl von tatsächlichen Änderungen vorhanden ist, kann es hilfreich sein, die gesendeten Updates einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="47228-144">However, if the application is handling many entities and only a small number of those have actual changes, then it may be useful to limit the updates sent.</span></span> <span data-ttu-id="47228-145">Dies kann erreicht werden, indem eine Abfrage ausgeführt wird, mit der die Entitäten nachverfolgt werden, die derzeit in der Datenbank vorhanden sind, und dann Änderungen an diesen überwachten Entitäten angewendet werden</span><span class="sxs-lookup"><span data-stu-id="47228-145">This can be achieved by executing a query to track the entities as they currently exist in the database, and then applying changes to these tracked entities.</span></span> <span data-ttu-id="47228-146">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="47228-146">For example:</span></span>

<!--
    public static void UpdateFromHttpPost2(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        trackedBlog.Name = blog.Name;
        trackedBlog.Summary = blog.Summary;

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_2)]

<span data-ttu-id="47228-147">In diesem Fall:</span><span class="sxs-lookup"><span data-stu-id="47228-147">In this case:</span></span>

- <span data-ttu-id="47228-148">Es wird nur eine einzelne Instanz der Entität nachverfolgt. der, der von der Abfrage von der Datenbank zurückgegeben wird `Find` .</span><span class="sxs-lookup"><span data-stu-id="47228-148">Only a single instance of the entity is tracked; the one that is returned from the database by the `Find` query.</span></span>
- <span data-ttu-id="47228-149">`Update`, `Attach` usw. werden **nicht** verwendet.</span><span class="sxs-lookup"><span data-stu-id="47228-149">`Update`, `Attach`, etc. are **not** used.</span></span>
- <span data-ttu-id="47228-150">Nur Eigenschaftswerte, die tatsächlich geändert wurden, werden in der Datenbank aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="47228-150">Only property values that have actually changed are updated in the database.</span></span>
- <span data-ttu-id="47228-151">Es werden zwei Roundtrips für die Datenbank durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="47228-151">Two database round-trips are made.</span></span>

<span data-ttu-id="47228-152">EF Core verfügt über einige Hilfsprogramme zum Übertragen von Eigenschafts Werten wie this.</span><span class="sxs-lookup"><span data-stu-id="47228-152">EF Core has some helpers for transferring property values like this.</span></span> <span data-ttu-id="47228-153">Beispielsweise <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> kopiert alle Werte aus dem angegebenen Objekt und legt Sie für das nach verfolgte Objekt fest:</span><span class="sxs-lookup"><span data-stu-id="47228-153">For example, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> will copy all the values from the given object and set them on the tracked object:</span></span>

<!--
    public static void UpdateFromHttpPost3(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_3)]

<span data-ttu-id="47228-154">`SetValues` akzeptiert verschiedene Objekttypen, einschließlich DTOs (Data Transfer Objects) mit Eigenschaftsnamen, die den Eigenschaften des Entitäts Typs entsprechen.</span><span class="sxs-lookup"><span data-stu-id="47228-154">`SetValues` accepts various object types, including data transfer objects (DTOs) with property names that match the properties of the entity type.</span></span> <span data-ttu-id="47228-155">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="47228-155">For example:</span></span>

<!--
    public static void UpdateFromHttpPost4(BlogDto dto)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(dto.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(dto);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_4)]

<span data-ttu-id="47228-156">Oder ein Wörterbuch mit Name-Wert-Einträgen für die Eigenschaftswerte:</span><span class="sxs-lookup"><span data-stu-id="47228-156">Or a dictionary with name/value entries for the property values:</span></span>

<!--
    public static void UpdateFromHttpPost5(Dictionary<string, object> propertyValues)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(propertyValues["Id"]);

        context.Entry(trackedBlog).CurrentValues.SetValues(propertyValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_5)]

<span data-ttu-id="47228-157">Weitere Informationen zum Arbeiten mit Eigenschafts Werten wie diesem finden Sie unter Zugreifen auf nach [verfolgte Entitäten](xref:core/change-tracking/entity-entries) .</span><span class="sxs-lookup"><span data-stu-id="47228-157">See [Accessing tracked entities](xref:core/change-tracking/entity-entries) for more information on working with property values like this.</span></span>

### <a name="use-original-values"></a><span data-ttu-id="47228-158">Ursprüngliche Werte verwenden</span><span class="sxs-lookup"><span data-stu-id="47228-158">Use original values</span></span>

<span data-ttu-id="47228-159">Bisher hat jeder Ansatz entweder eine Abfrage vor dem Update ausgeführt oder alle Eigenschaftswerte aktualisiert, unabhängig davon, ob Sie geändert wurden.</span><span class="sxs-lookup"><span data-stu-id="47228-159">So far each approach has either executed a query before making the update, or updated all property values regardless of whether or not they have changed.</span></span> <span data-ttu-id="47228-160">Um nur Werte zu aktualisieren, die ohne Abfragen als Teil des Updates geändert wurden, müssen bestimmte Informationen darüber, welche Eigenschaftswerte geändert wurden, geändert werden.</span><span class="sxs-lookup"><span data-stu-id="47228-160">To update only values that have changed without querying as part of the update requires specific information about which property values have changed.</span></span> <span data-ttu-id="47228-161">Eine gängige Methode, um diese Informationen zu erhalten, besteht darin, sowohl die aktuellen als auch die ursprünglichen Werte im HTTP-Post oder ähnlich zu senden.</span><span class="sxs-lookup"><span data-stu-id="47228-161">A common way to get this information is to send back both the current and original values in the HTTP Post or similar.</span></span> <span data-ttu-id="47228-162">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="47228-162">For example:</span></span>

<!--
    public static void UpdateFromHttpPost6(Blog blog, Dictionary<string, object> originalValues)
    {
        using var context = new BlogsContext();

        context.Attach(blog);
        context.Entry(blog).OriginalValues.SetValues(originalValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_6](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_6)]

<span data-ttu-id="47228-163">In diesem Code wird die Entität mit geänderten Werten zum ersten Mal angefügt.</span><span class="sxs-lookup"><span data-stu-id="47228-163">In this code the entity with modified values is first attached.</span></span> <span data-ttu-id="47228-164">Dies bewirkt EF Core, dass die Entität im Zustand nachverfolgt wird, d `Unchanged` . h., wenn keine Eigenschaftswerte als geändert markiert sind.</span><span class="sxs-lookup"><span data-stu-id="47228-164">This causes EF Core to track the entity in the `Unchanged` state; that is, with no property values marked as modified.</span></span> <span data-ttu-id="47228-165">Das Wörterbuch der ursprünglichen Werte wird dann auf diese nach verfolgte Entität angewendet.</span><span class="sxs-lookup"><span data-stu-id="47228-165">The dictionary of original values is then applied to this tracked entity.</span></span> <span data-ttu-id="47228-166">Dies wird als geänderte Eigenschaften mit anderen aktuellen und ursprünglichen Werten markiert.</span><span class="sxs-lookup"><span data-stu-id="47228-166">This will mark as modified properties with different current and original values.</span></span> <span data-ttu-id="47228-167">Eigenschaften, die die gleichen aktuellen und ursprünglichen Werte aufweisen, werden nicht als geändert markiert.</span><span class="sxs-lookup"><span data-stu-id="47228-167">Properties that have the same current and original values will not be marked as modified.</span></span>

<span data-ttu-id="47228-168">In diesem Fall:</span><span class="sxs-lookup"><span data-stu-id="47228-168">In this case:</span></span>

- <span data-ttu-id="47228-169">Nur eine einzelne Instanz der Entität wird mithilfe von Attach nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="47228-169">Only a single instance of the entity is tracked, using Attach.</span></span>
- <span data-ttu-id="47228-170">Die Entitäts Instanz wird **nicht** im Rahmen der Aktualisierung von der Datenbank abgefragt.</span><span class="sxs-lookup"><span data-stu-id="47228-170">The entity instance is **not** queried from the database as part of making the update.</span></span>
- <span data-ttu-id="47228-171">Durch das Anwenden der ursprünglichen Werte wird sichergestellt, dass nur Eigenschaftswerte, die tatsächlich geändert wurden, in der Datenbank aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="47228-171">Applying the original values ensures that only property values that have actually changed are updated in the database.</span></span>
- <span data-ttu-id="47228-172">Ein Daten Bank Roundtrip wird durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="47228-172">One database round-trip is made.</span></span>

<span data-ttu-id="47228-173">Wie bei den Beispielen im vorherigen Abschnitt müssen die ursprünglichen Werte nicht als Wörterbuch weitergegeben werden. eine Entitäts Instanz oder DTO funktioniert ebenfalls.</span><span class="sxs-lookup"><span data-stu-id="47228-173">As with the examples in the previous section, the original values do not have to passed as a dictionary; an entity instance or DTO will also work.</span></span>

> [!TIP]
> <span data-ttu-id="47228-174">Obwohl dieser Ansatz ansprechende Merkmale hat, ist es erforderlich, die ursprünglichen Werte der Entität an den und vom WebClient zu senden.</span><span class="sxs-lookup"><span data-stu-id="47228-174">While this approach has appealing characteristics, it does require sending the entity's original values to and from the web client.</span></span> <span data-ttu-id="47228-175">Berücksichtigen Sie sorgfältig, ob diese zusätzliche Komplexität die Vorteile bietet. bei vielen Anwendungen ist ein einfacherer Ansatz pragmatischer.</span><span class="sxs-lookup"><span data-stu-id="47228-175">Carefully consider whether this extra complexity is worth the benefits; for many applications one of the simpler approaches is more pragmatic.</span></span>

## <a name="attaching-a-serialized-graph"></a><span data-ttu-id="47228-176">Anfügen eines serialisierten Diagramms</span><span class="sxs-lookup"><span data-stu-id="47228-176">Attaching a serialized graph</span></span>

<span data-ttu-id="47228-177">EF Core funktioniert mit Diagrammen von Entitäten, die über Fremdschlüssel und Navigations Eigenschaften verbunden sind, wie unter [Ändern von Fremdschlüsseln und Navigationen](xref:core/change-tracking/relationship-changes)beschrieben.</span><span class="sxs-lookup"><span data-stu-id="47228-177">EF Core works with graphs of entities connected via foreign keys and navigation properties, as described in [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes).</span></span> <span data-ttu-id="47228-178">Wenn diese Diagramme außerhalb der EF Core mithilfe von erstellt werden, z. b. aus einer JSON-Datei, können Sie mehrere Instanzen derselben Entität haben.</span><span class="sxs-lookup"><span data-stu-id="47228-178">If these graphs are created outside of EF Core using, for example, from a JSON file, then they can have multiple instances of the same entity.</span></span> <span data-ttu-id="47228-179">Diese Duplikate müssen in einzelne Instanzen aufgelöst werden, bevor das Diagramm nachverfolgt werden kann.</span><span class="sxs-lookup"><span data-stu-id="47228-179">These duplicates need to be resolved into single instances before the graph can be tracked.</span></span>

### <a name="graphs-with-no-duplicates"></a><span data-ttu-id="47228-180">Diagramme ohne Duplikate</span><span class="sxs-lookup"><span data-stu-id="47228-180">Graphs with no duplicates</span></span>

<span data-ttu-id="47228-181">Bevor Sie fortfahren, ist es wichtig, Folgendes zu erkennen:</span><span class="sxs-lookup"><span data-stu-id="47228-181">Before going any further it is important to recognize that:</span></span>

- <span data-ttu-id="47228-182">Serialisierungssoren verfügen häufig über Optionen zum Verarbeiten von Schleifen und doppelten Instanzen im Diagramm.</span><span class="sxs-lookup"><span data-stu-id="47228-182">Serializers often have options for handling loops and duplicate instances in the graph.</span></span>
- <span data-ttu-id="47228-183">Die Auswahl des Objekts, das als Graph-Stamm verwendet wird, kann häufig zum reduzieren oder Entfernen von Duplikaten beitragen.</span><span class="sxs-lookup"><span data-stu-id="47228-183">The choice of object used as the graph root can often help reduce or remove duplicates.</span></span>

<span data-ttu-id="47228-184">Verwenden Sie nach Möglichkeit Serialisierungsoptionen, und wählen Sie Stämme aus, die keine Duplikate ergeben.</span><span class="sxs-lookup"><span data-stu-id="47228-184">If possible, use serialization options and choose roots that do not result in duplicates.</span></span> <span data-ttu-id="47228-185">Im folgenden Code wird z. b. [JSON.net](https://www.nuget.org/packages/Newtonsoft.Json/) verwendet, um eine Liste von Blogs mit den zugehörigen Beiträgen zu serialisieren:</span><span class="sxs-lookup"><span data-stu-id="47228-185">For example, the following code uses [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/) to serialize a list of blogs each with its associated posts:</span></span>

<!--
            using var context = new BlogsContext();

            var blogs = context.Blogs.Include(e => e.Posts).ToList();

            var serialized = JsonConvert.SerializeObject(
                blogs,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_1a](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1a)]

<span data-ttu-id="47228-186">Der aus diesem Code generierte JSON-Code lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="47228-186">The JSON generated from this code is:</span></span>

```json
[
  {
    "Id": 1,
    "Name": ".NET Blog",
    "Summary": "Posts about .NET",
    "Posts": [
      {
        "Id": 1,
        "Title": "Announcing the Release of EF Core 5.0",
        "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
        "BlogId": 1
      },
      {
        "Id": 2,
        "Title": "Announcing F# 5",
        "Content": "F# 5 is the latest version of F#, the functional programming language...",
        "BlogId": 1
      }
    ]
  },
  {
    "Id": 2,
    "Name": "Visual Studio Blog",
    "Summary": "Posts about Visual Studio",
    "Posts": [
      {
        "Id": 3,
        "Title": "Disassembly improvements for optimized managed debugging",
        "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
        "BlogId": 2
      },
      {
        "Id": 4,
        "Title": "Database Profiling with Visual Studio",
        "Content": "Examine when database queries were executed and measure how long the take using...",
        "BlogId": 2
      }
    ]
  }
]
```

<span data-ttu-id="47228-187">Beachten Sie, dass keine doppelten Blogs oder Beiträge in der JSON vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="47228-187">Notice that there are no duplicate blogs or posts in the JSON.</span></span> <span data-ttu-id="47228-188">Dies bedeutet, dass einfache Aufrufe von `Update` funktionieren, um diese Entitäten in der Datenbank zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="47228-188">This means that simple calls to `Update` will work to update these entities in the database:</span></span>

<!--
        public static void UpdateBlogsFromJson(string json)
        {
            using var context = new BlogsContext();

            var blogs = JsonConvert.DeserializeObject<List<Blog>>(json);

            foreach (var blog in blogs)
            {
                context.Update(blog);
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_1b](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1b)]

### <a name="handling-duplicates"></a><span data-ttu-id="47228-189">Verarbeiten von Duplikaten</span><span class="sxs-lookup"><span data-stu-id="47228-189">Handling duplicates</span></span>

<span data-ttu-id="47228-190">Der Code im vorherigen Beispiel hat jeden Blog mit den zugehörigen Beiträgen serialisiert.</span><span class="sxs-lookup"><span data-stu-id="47228-190">The code in the previous example serialized each blog with its associated posts.</span></span> <span data-ttu-id="47228-191">Wenn dies geändert wird, um jeden Beitrag mit dem zugehörigen Blog zu serialisieren, werden Duplikate in den serialisierten JSON-Code eingefügt.</span><span class="sxs-lookup"><span data-stu-id="47228-191">If this is changed to serialize each post with its associated blog, then duplicates are introduced into the serialized JSON.</span></span> <span data-ttu-id="47228-192">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="47228-192">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var posts = context.Posts.Include(e => e.Blog).ToList();

            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_2)]

<span data-ttu-id="47228-193">Der serialisierte JSON-Code sieht nun wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="47228-193">The serialized JSON now looks like this:</span></span>

```json
[
  {
    "Id": 1,
    "Title": "Announcing the Release of EF Core 5.0",
    "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 2,
          "Title": "Announcing F# 5",
          "Content": "F# 5 is the latest version of F#, the functional programming language...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 2,
    "Title": "Announcing F# 5",
    "Content": "F# 5 is the latest version of F#, the functional programming language...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 1,
          "Title": "Announcing the Release of EF Core 5.0",
          "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 3,
    "Title": "Disassembly improvements for optimized managed debugging",
    "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 4,
          "Title": "Database Profiling with Visual Studio",
          "Content": "Examine when database queries were executed and measure how long the take using...",
          "BlogId": 2
        }
      ]
    }
  },
  {
    "Id": 4,
    "Title": "Database Profiling with Visual Studio",
    "Content": "Examine when database queries were executed and measure how long the take using...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 3,
          "Title": "Disassembly improvements for optimized managed debugging",
          "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
          "BlogId": 2
        }
      ]
    }
  }
]
```

<span data-ttu-id="47228-194">Beachten Sie, dass das Diagramm nun mehrere Blog Instanzen mit demselben Schlüsselwert sowie mehrere Post-Instanzen mit demselben Schlüsselwert enthält.</span><span class="sxs-lookup"><span data-stu-id="47228-194">Notice that the graph now includes multiple Blog instances with the same key value, as well as multiple Post instance with the same key value.</span></span> <span data-ttu-id="47228-195">Wenn Sie versuchen, dieses Diagramm wie im vorherigen Beispiel zu verfolgen, wird Folgendes ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="47228-195">Attempting to track this graph like we did in the previous example will throw:</span></span>

> <span data-ttu-id="47228-196">System. InvalidOperationException: die Instanz des Entitäts Typs "Post" kann nicht nachverfolgt werden, da bereits eine andere Instanz mit dem Schlüsselwert "{ID: 2}" nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="47228-196">System.InvalidOperationException: The instance of entity type 'Post' cannot be tracked because another instance with the key value '{Id: 2}' is already being tracked.</span></span> <span data-ttu-id="47228-197">Wenn Sie vorhandene Entitäten anfügen, stellen Sie sicher, dass nur eine Entitäts Instanz mit einem angegebenen Schlüsselwert angefügt ist.</span><span class="sxs-lookup"><span data-stu-id="47228-197">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="47228-198">Dies kann auf zwei Arten behoben werden:</span><span class="sxs-lookup"><span data-stu-id="47228-198">We can fix this in two ways:</span></span>

- <span data-ttu-id="47228-199">Verwenden von JSON-Serialisierungsoptionen, die Verweise beibehalten</span><span class="sxs-lookup"><span data-stu-id="47228-199">Use JSON serialization options that preserve references</span></span>
- <span data-ttu-id="47228-200">Durchführen der Identitäts Auflösung während der Nachverfolgung des Diagramms</span><span class="sxs-lookup"><span data-stu-id="47228-200">Perform identity resolution while the graph is being tracked</span></span>

#### <a name="preserve-references"></a><span data-ttu-id="47228-201">Beibehalten von Verweisen</span><span class="sxs-lookup"><span data-stu-id="47228-201">Preserve references</span></span>

<span data-ttu-id="47228-202">JSON.net bietet die `PreserveReferencesHandling` Option zum Behandeln dieses.</span><span class="sxs-lookup"><span data-stu-id="47228-202">Json.NET provides the `PreserveReferencesHandling` option to handle this.</span></span> <span data-ttu-id="47228-203">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="47228-203">For example:</span></span>

<!--
            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    PreserveReferencesHandling = PreserveReferencesHandling.All,
                    Formatting = Formatting.Indented
                });
-->
[!code-csharp[Attaching_a_serialized_graph_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_3)]

<span data-ttu-id="47228-204">Der resultierende JSON-Code sieht nun wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="47228-204">The resulting JSON now looks like this:</span></span>

```json
{
  "$id": "1",
  "$values": [
    {
      "$id": "2",
      "Id": 1,
      "Title": "Announcing the Release of EF Core 5.0",
      "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
      "BlogId": 1,
      "Blog": {
        "$id": "3",
        "Id": 1,
        "Name": ".NET Blog",
        "Summary": "Posts about .NET",
        "Posts": [
          {
            "$ref": "2"
          },
          {
            "$id": "4",
            "Id": 2,
            "Title": "Announcing F# 5",
            "Content": "F# 5 is the latest version of F#, the functional programming language...",
            "BlogId": 1,
            "Blog": {
              "$ref": "3"
            }
          }
        ]
      }
    },
    {
      "$ref": "4"
    },
    {
      "$id": "5",
      "Id": 3,
      "Title": "Disassembly improvements for optimized managed debugging",
      "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
      "BlogId": 2,
      "Blog": {
        "$id": "6",
        "Id": 2,
        "Name": "Visual Studio Blog",
        "Summary": "Posts about Visual Studio",
        "Posts": [
          {
            "$ref": "5"
          },
          {
            "$id": "7",
            "Id": 4,
            "Title": "Database Profiling with Visual Studio",
            "Content": "Examine when database queries were executed and measure how long the take using...",
            "BlogId": 2,
            "Blog": {
              "$ref": "6"
            }
          }
        ]
      }
    },
    {
      "$ref": "7"
    }
  ]
}
```

<span data-ttu-id="47228-205">Beachten Sie, dass dieser JSON-Code Duplikate durch Verweise wie ersetzt hat `"$ref": "5"` , die auf die bereits vorhandene Instanz im Diagramm verweisen.</span><span class="sxs-lookup"><span data-stu-id="47228-205">Notice that this JSON has replaced duplicates with references like `"$ref": "5"` that refer to the already existing instance in the graph.</span></span> <span data-ttu-id="47228-206">Dieses Diagramm kann wieder mit den einfachen Aufrufen von nachverfolgt werden `Update` , wie oben gezeigt.</span><span class="sxs-lookup"><span data-stu-id="47228-206">This graph can again be tracked using the simple calls to `Update`, as shown above.</span></span>

<span data-ttu-id="47228-207">Die <xref:System.Text.Json> Unterstützung in den .net-Basisklassen Bibliotheken (BCL) verfügt über eine ähnliche Option, die das gleiche Ergebnis liefert.</span><span class="sxs-lookup"><span data-stu-id="47228-207">The <xref:System.Text.Json> support in the .NET base class libraries (BCL) has a similar option which produces the same result.</span></span> <span data-ttu-id="47228-208">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="47228-208">For example:</span></span>

<!--
            var serialized = System.Text.Json.JsonSerializer.Serialize(posts, new System.Text.Json.JsonSerializerOptions
            {
                ReferenceHandler = ReferenceHandler.Preserve,
                WriteIndented = true
            });
-->
[!code-csharp[Attaching_a_serialized_graph_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_4)]

#### <a name="resolve-duplicates"></a><span data-ttu-id="47228-209">Duplikate auflösen</span><span class="sxs-lookup"><span data-stu-id="47228-209">Resolve duplicates</span></span>

<span data-ttu-id="47228-210">Wenn es nicht möglich ist, Duplikate im Serialisierungsprozess auszuschließen, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> bietet eine Möglichkeit, diese zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="47228-210">If it is not possible to eliminate duplicates in the serialization process, then <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> provides a way to handle this.</span></span> <span data-ttu-id="47228-211">Trackgraph funktioniert wie `Add` , `Attach` und `Update` mit der Ausnahme, dass ein Rückruf für jede Entitäts Instanz generiert wird, bevor Sie nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="47228-211">TrackGraph works like `Add`, `Attach` and `Update` except that it generates a callback for every entity instance before tracking it.</span></span> <span data-ttu-id="47228-212">Dieser Rückruf kann verwendet werden, um die Entität zu verfolgen oder zu ignorieren.</span><span class="sxs-lookup"><span data-stu-id="47228-212">This callback can be used to either track the entity or ignore it.</span></span> <span data-ttu-id="47228-213">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="47228-213">For example:</span></span>

<!--
        public static void UpdatePostsFromJsonWithIdentityResolution(string json)
        {
            using var context = new BlogsContext();

            var posts = JsonConvert.DeserializeObject<List<Post>>(json);

            foreach (var post in posts)
            {
                context.ChangeTracker.TrackGraph(
                    post, node =>
                        {
                            var keyValue = node.Entry.Property("Id").CurrentValue;
                            var entityType = node.Entry.Metadata;

                            var existingEntity = node.Entry.Context.ChangeTracker.Entries()
                                .FirstOrDefault(
                                    e => Equals(e.Metadata, entityType)
                                         && Equals(e.Property("Id").CurrentValue, keyValue));

                            if (existingEntity == null)
                            {
                                Console.WriteLine($"Tracking {entityType} entity with key value {keyValue}");

                                node.Entry.State = EntityState.Modified;
                            }
                            else
                            {
                                Console.WriteLine($"Discarding duplicate {entityType} entity with key value {keyValue}");
                            }
                        });
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_5)]

<span data-ttu-id="47228-214">Für jede Entität im Diagramm führt dieser Code Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="47228-214">For each entity in the graph, this code will:</span></span>

- <span data-ttu-id="47228-215">Suchen des Entitäts Typs und des Schlüssel Werts der Entität</span><span class="sxs-lookup"><span data-stu-id="47228-215">Find the entity type and key value of the entity</span></span>
- <span data-ttu-id="47228-216">Suchen der Entität mit diesem Schlüssel in der Änderungs Nachverfolgung</span><span class="sxs-lookup"><span data-stu-id="47228-216">Lookup the entity with this key in the change tracker</span></span>
  - <span data-ttu-id="47228-217">Wenn die Entität gefunden wird, wird keine weitere Aktion durchgeführt, da die Entität ein Duplikat ist.</span><span class="sxs-lookup"><span data-stu-id="47228-217">If the entity is found, then no further action is taken as the entity is a duplicate</span></span>
  - <span data-ttu-id="47228-218">Wenn die Entität nicht gefunden wird, wird Sie nachverfolgt, indem der Status auf festgelegt wird. `Modified`</span><span class="sxs-lookup"><span data-stu-id="47228-218">If the entity is not found, then it is tracked by setting the state to `Modified`</span></span>

<span data-ttu-id="47228-219">Die Ausgabe aus dem Ausführen dieses Codes lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="47228-219">The output from running this code is:</span></span>

```output
Tracking EntityType: Post entity with key value 1
Tracking EntityType: Blog entity with key value 1
Tracking EntityType: Post entity with key value 2
Discarding duplicate EntityType: Post entity with key value 2
Tracking EntityType: Post entity with key value 3
Tracking EntityType: Blog entity with key value 2
Tracking EntityType: Post entity with key value 4
Discarding duplicate EntityType: Post entity with key value 4
```

> [!IMPORTANT]
> <span data-ttu-id="47228-220">Bei diesem Code **wird davon ausgegangen, dass alle Duplikate identisch sind**.</span><span class="sxs-lookup"><span data-stu-id="47228-220">This code **assumes that all duplicates are identical**.</span></span> <span data-ttu-id="47228-221">Dadurch ist es sicher, dass Sie beliebig eines der Duplikate auswählen, das beim Verwerfen der anderen nachverfolgt werden soll.</span><span class="sxs-lookup"><span data-stu-id="47228-221">This makes it safe to arbitrarily choose one of the duplicates to track while discarding the others.</span></span> <span data-ttu-id="47228-222">Wenn sich die Duplikate unterscheiden können, muss der Code entscheiden, wie bestimmt werden soll, welcher Wert verwendet werden soll, und wie Eigenschafts-und Navigations Werte zusammen kombiniert werden.</span><span class="sxs-lookup"><span data-stu-id="47228-222">If the duplicates can differ, then the code will need to decide how to determine which one to use, and how to combine property and navigation values together.</span></span>

> [!NOTE]
> <span data-ttu-id="47228-223">Der Einfachheit halber geht dieser Code davon aus, dass jede Entität über eine Primärschlüssel Eigenschaft namens verfügt `Id` .</span><span class="sxs-lookup"><span data-stu-id="47228-223">For simplicity, this code assumes each entity has a primary key property called `Id`.</span></span> <span data-ttu-id="47228-224">Dies könnte in eine abstrakte Basisklasse oder Schnittstelle cogeschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="47228-224">This could be codified into an abstract base class or interface.</span></span> <span data-ttu-id="47228-225">Alternativ können die Primärschlüssel Eigenschaft oder die Eigenschaften aus den Metadaten abgerufen werden <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> , sodass dieser Code mit jedem beliebigen Entitätstyp funktionieren würde.</span><span class="sxs-lookup"><span data-stu-id="47228-225">Alternately, the primary key property or properties could be obtained from the <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata such that this code would work with any type of entity.</span></span>

## <a name="failing-to-set-key-values"></a><span data-ttu-id="47228-226">Fehler beim Festlegen von Schlüsselwerten.</span><span class="sxs-lookup"><span data-stu-id="47228-226">Failing to set key values</span></span>

<span data-ttu-id="47228-227">Entitäts Typen sind häufig so konfiguriert, dass [automatisch generierte Schlüsselwerte](xref:core/modeling/generated-properties)verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="47228-227">Entity types are often configured to use [automatically generated key values](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="47228-228">Dies ist die Standardeinstellung für Integer-und GUID-Eigenschaften von nicht zusammengesetzten Schlüsseln.</span><span class="sxs-lookup"><span data-stu-id="47228-228">This is the default for integer and GUID properties of non-composite keys.</span></span> <span data-ttu-id="47228-229">Wenn der Entitätstyp jedoch nicht für die Verwendung automatisch generierter Schlüsselwerte konfiguriert ist, muss ein expliziter Schlüsselwert festgelegt werden, bevor die Entität nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="47228-229">However, if the entity type is not configured to use automatically generated key values, then an explicit key value must be set before tracking the entity.</span></span> <span data-ttu-id="47228-230">Verwenden Sie beispielsweise den folgenden Entitätstyp:</span><span class="sxs-lookup"><span data-stu-id="47228-230">For example, using the following entity type:</span></span>

<!--
    public class Pet
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }
    }
-->
[!code-csharp[Pet](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Pet)]

<span data-ttu-id="47228-231">Angenommen, Sie versuchen, zwei neue Entitäts Instanzen zu überprüfen, ohne Schlüsselwerte festzulegen:</span><span class="sxs-lookup"><span data-stu-id="47228-231">Consider code that attempts to tracker two new entity instances without setting key values:</span></span>

<!--
            using var context = new BlogsContext();

            context.Add(new Pet { Name = "Smokey" });

            try
            {
                context.Add(new Pet { Name = "Clippy" }); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Failing_to_set_key_values_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Failing_to_set_key_values_1)]

<span data-ttu-id="47228-232">Dieser Code löst Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="47228-232">This code will throw:</span></span>

> <span data-ttu-id="47228-233">System. InvalidOperationException: die Instanz des Entitäts Typs "Pet" kann nicht nachverfolgt werden, da bereits eine andere Instanz mit dem Schlüsselwert "{ID: 0}" nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="47228-233">System.InvalidOperationException: The instance of entity type 'Pet' cannot be tracked because another instance with the key value '{Id: 0}' is already being tracked.</span></span> <span data-ttu-id="47228-234">Wenn Sie vorhandene Entitäten anfügen, stellen Sie sicher, dass nur eine Entitäts Instanz mit einem angegebenen Schlüsselwert angefügt ist.</span><span class="sxs-lookup"><span data-stu-id="47228-234">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="47228-235">Die Korrektur hierfür besteht darin, entweder Schlüsselwerte explizit festzulegen oder die Schlüsseleigenschaft für die Verwendung generierter Schlüsselwerte zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="47228-235">The fix for this is to either to set key values explicitly or configure the key property to use generated key values.</span></span> <span data-ttu-id="47228-236">Weitere Informationen finden Sie unter [generierte Werte](xref:core/modeling/generated-properties) .</span><span class="sxs-lookup"><span data-stu-id="47228-236">See [Generated Values](xref:core/modeling/generated-properties) for more information.</span></span>

## <a name="overusing-a-single-dbcontext-instance"></a><span data-ttu-id="47228-237">Über Verwenden einer einzelnen dbcontext-Instanz</span><span class="sxs-lookup"><span data-stu-id="47228-237">Overusing a single DbContext instance</span></span>

<span data-ttu-id="47228-238"><xref:Microsoft.EntityFrameworkCore.DbContext> ist so konzipiert, dass es eine kurzlebige Arbeitseinheit darstellt, wie unter [dbcontext-Initialisierung und-Konfiguration](xref:core/dbcontext-configuration/index)beschrieben und in [Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index)ausführlich erläutert.</span><span class="sxs-lookup"><span data-stu-id="47228-238"><xref:Microsoft.EntityFrameworkCore.DbContext> is designed to represent a short-lived unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index), and elaborated on in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span> <span data-ttu-id="47228-239">Wenn Sie dieser Anleitung nicht folgen, können Sie problemlos in Situationen eintreten, in denen versucht wird, mehrere Instanzen derselben Entität zu verfolgen.</span><span class="sxs-lookup"><span data-stu-id="47228-239">Not following this guidance makes it is easy to run into situations where an attempt is made to track multiple instances of the same entity.</span></span> <span data-ttu-id="47228-240">Typische Beispiele:</span><span class="sxs-lookup"><span data-stu-id="47228-240">Common examples are:</span></span>

- <span data-ttu-id="47228-241">Verwenden Sie dieselbe dbcontext-Instanz, um den Testzustand einzurichten, und führen Sie dann den Test aus.</span><span class="sxs-lookup"><span data-stu-id="47228-241">Using the same DbContext instance to both set up test state and then execute the test.</span></span> <span data-ttu-id="47228-242">Dies führt häufig dazu, dass dbcontext nach wie vor eine Entitäts Instanz aus dem Test Setup nachverfolgt, während versucht wird, eine neue Instanz im Test ordnungsgemäß anzufügen.</span><span class="sxs-lookup"><span data-stu-id="47228-242">This often results in the DbContext still tracking one entity instance from test setup, while then attempting to attach a new instance in the test proper.</span></span> <span data-ttu-id="47228-243">Verwenden Sie stattdessen eine andere dbcontext-Instanz zum Einrichten des Test Zustands und des richtigen Testcodes.</span><span class="sxs-lookup"><span data-stu-id="47228-243">Instead, use a different DbContext instance for setting up test state and the test code proper.</span></span>
- <span data-ttu-id="47228-244">Verwenden einer freigegebenen dbcontext-Instanz in einem Repository oder einem ähnlichen Code.</span><span class="sxs-lookup"><span data-stu-id="47228-244">Using a shared DbContext instance in a repository or similar code.</span></span> <span data-ttu-id="47228-245">Stellen Sie stattdessen sicher, dass Ihr Repository für jede Arbeitseinheit eine einzelne dbcontext-Instanz verwendet.</span><span class="sxs-lookup"><span data-stu-id="47228-245">Instead, make sure your repository uses a single DbContext instance for each unit-of-work.</span></span>

## <a name="identity-resolution-and-queries"></a><span data-ttu-id="47228-246">Identitäts Auflösung und-Abfragen</span><span class="sxs-lookup"><span data-stu-id="47228-246">Identity resolution and queries</span></span>

<span data-ttu-id="47228-247">Die Identitäts Auflösung erfolgt automatisch, wenn Entitäten aus einer Abfrage nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="47228-247">Identity resolution happens automatically when entities are tracked from a query.</span></span> <span data-ttu-id="47228-248">Dies bedeutet Folgendes: Wenn eine Entitäts Instanz mit einem angegebenen Schlüsselwert bereits nachverfolgt wird, wird diese vorhandene überwachte Instanz anstelle der Erstellung einer neuen Instanz verwendet.</span><span class="sxs-lookup"><span data-stu-id="47228-248">This means that if an entity instance with a given key value is already tracked, then this existing tracked instance is used instead of creating a new instance.</span></span> <span data-ttu-id="47228-249">Dies hat eine wichtige Folge: Wenn sich die Daten in der Datenbank geändert haben, wird dies in den Ergebnissen der Abfrage nicht wiedergegeben.</span><span class="sxs-lookup"><span data-stu-id="47228-249">This has an important consequence: if the data has changed in the database, then this will not be reflected in the results of the query.</span></span> <span data-ttu-id="47228-250">Dies ist ein anguter Grund für die Verwendung einer neuen dbcontext-Instanz für jede Arbeitseinheit, wie unter [dbcontext-Initialisierung und-Konfiguration](xref:core/dbcontext-configuration/index)beschrieben und in [Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index)ausführlich erläutert.</span><span class="sxs-lookup"><span data-stu-id="47228-250">This is angood reason to use a new DbContext instance for each unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index), and elaborated on in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="47228-251">Es ist wichtig zu verstehen, dass EF Core immer eine LINQ-Abfrage für ein dbset für die Datenbank ausführt und nur Ergebnisse basierend auf der Datenbank zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="47228-251">It is important to understand that EF Core always executes a LINQ query on a DbSet against the database and only returns results based on what is in the database.</span></span> <span data-ttu-id="47228-252">Bei einer nach Verfolgungs Abfrage werden jedoch, wenn die zurückgegebenen Entitäten bereits nachverfolgt werden, die nach verfolgten Instanzen verwendet, anstatt aus den Daten in der Datenbank eine-Instanz zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="47228-252">However, for a tracking query, if the entities returned are already tracked, then the tracked instances are used instead of creating a instances from the data in the database.</span></span>

<span data-ttu-id="47228-253"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> oder <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> kann verwendet werden, wenn nach verfolgte Entitäten mit den neuesten Daten aus der Datenbank aktualisiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="47228-253"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> or <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> can be used when tracked entities need to be refreshed with the latest data from the database.</span></span> <span data-ttu-id="47228-254">Weitere Informationen finden Sie unter Zugreifen auf nach [verfolgte Entitäten](xref:core/change-tracking/entity-entries) .</span><span class="sxs-lookup"><span data-stu-id="47228-254">See [Accessing Tracked Entities](xref:core/change-tracking/entity-entries) for more information.</span></span>

<span data-ttu-id="47228-255">Anders als bei der Nachverfolgung von Abfragen führen Abfragen ohne Nachverfolgung keine Identitäts Auflösung durch.</span><span class="sxs-lookup"><span data-stu-id="47228-255">In contrast to tracking queries, no-tracking queries do not perform identity resolution.</span></span> <span data-ttu-id="47228-256">Dies bedeutet, dass keine nach Verfolgungs Abfragen wie im zuvor beschriebenen JSON-serialisierungsfall Duplikate zurückgeben können.</span><span class="sxs-lookup"><span data-stu-id="47228-256">This means that no-tracking queries can return duplicates just like in the JSON serialization case described earlier.</span></span> <span data-ttu-id="47228-257">Dies ist in der Regel kein Problem, wenn die Abfrageergebnisse serialisiert und an den Client gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="47228-257">This is usually not an issue if the query results are going to be serialized and sent to the client.</span></span>

> [!TIP]
> <span data-ttu-id="47228-258">Führen Sie nicht routinemäßig eine Abfrage ohne Nachverfolgung aus, und fügen Sie dann die zurückgegebenen Entitäten demselben Kontext hinzu.</span><span class="sxs-lookup"><span data-stu-id="47228-258">Do not routinely perform a no-tracking query and then attach the returned entities to the same context.</span></span> <span data-ttu-id="47228-259">Dies ist langsamer und schwieriger zu erreichen als die Verwendung einer nach Verfolgungs Abfrage.</span><span class="sxs-lookup"><span data-stu-id="47228-259">This will be both slower and harder to get right than using a tracking query.</span></span>

<span data-ttu-id="47228-260">Keine nach Verfolgungs Abfragen führen keine Identitäts Auflösung durch, da dies Auswirkungen auf die Leistung des Streamings einer großen Anzahl von Entitäten aus einer Abfrage hat.</span><span class="sxs-lookup"><span data-stu-id="47228-260">No-tracking queries do not perform identity resolution because doing so impacts the performance of streaming a large number of entities from a query.</span></span> <span data-ttu-id="47228-261">Dies liegt daran, dass bei der Identitäts Auflösung jede Instanz zurückgegeben werden muss, damit Sie verwendet werden kann, anstatt später ein Duplikat zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="47228-261">This is because identity resolution requires keeping track of each instance returned so that it can be used instead of later creating a duplicate.</span></span>

<span data-ttu-id="47228-262">Ab EF Core 5,0 können Abfragen für die Identitäts Auflösung mithilfe von nicht mehr nachverfolgt werden <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})> .</span><span class="sxs-lookup"><span data-stu-id="47228-262">Starting with EF Core 5.0, no-tracking queries can be forced to perform identity resolution by using <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})>.</span></span> <span data-ttu-id="47228-263">Die Abfrage verfolgt dann zurückgegebene Instanzen nach, ohne Sie auf die normale Weise nachzuverfolgen. Außerdem wird sichergestellt, dass in den Abfrage Ergebnissen keine Duplikate erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="47228-263">The query will then keep track of returned instances (without tracking them in the normal way) and ensure no duplicates are created in the query results.</span></span>

## <a name="overriding-object-equality"></a><span data-ttu-id="47228-264">Überschreiben der Objekt Gleichheit</span><span class="sxs-lookup"><span data-stu-id="47228-264">Overriding object equality</span></span>

<span data-ttu-id="47228-265">EF Core verwendet [Verweis Gleichheit](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) beim Vergleichen von Entitäts Instanzen.</span><span class="sxs-lookup"><span data-stu-id="47228-265">EF Core uses [reference equality](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) when comparing entity instances.</span></span> <span data-ttu-id="47228-266">Dies ist auch dann der Fall, wenn die Entitäts Typen die <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> Objekt Gleichheit überschreiben oder anderweitig ändern.</span><span class="sxs-lookup"><span data-stu-id="47228-266">This is the case even if the entity types override <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> or otherwise change object equality.</span></span> <span data-ttu-id="47228-267">Es gibt jedoch eine Stelle, an der sich das Überschreiben von Gleichheit auf EF Core Verhalten auswirken kann: Wenn Sammlungs Navigation die überschriebene Gleichheit anstelle der Verweis Gleichheit verwendet und daher mehrere Instanzen als identisch gemeldet werden.</span><span class="sxs-lookup"><span data-stu-id="47228-267">However, there is one place where overriding equality can impact EF Core behavior: when collection navigations use the overridden equality instead of reference equality, and hence report multiple instances as the same.</span></span>

<span data-ttu-id="47228-268">Aus diesem Grund empfiehlt es sich, das Überschreiben der Entitäts Gleichheit zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="47228-268">Because of this it is recommended that overriding entity equality should be avoided.</span></span> <span data-ttu-id="47228-269">Wenn Sie verwendet wird, stellen Sie sicher, dass Sie Sammlungs Navigation erstellen, die Verweis Gleichheit erzwingen.</span><span class="sxs-lookup"><span data-stu-id="47228-269">If it is used, then make sure to create collection navigations that force reference equality.</span></span> <span data-ttu-id="47228-270">Erstellen Sie z. b. einen Gleichheits Vergleich, der Verweis Gleichheit verwendet:</span><span class="sxs-lookup"><span data-stu-id="47228-270">For example, create an equality comparer that uses reference equality:</span></span>

<!--
    public sealed class ReferenceEqualityComparer : IEqualityComparer<object>
    {
        private ReferenceEqualityComparer()
        {
        }

        public static ReferenceEqualityComparer Instance { get; } = new ReferenceEqualityComparer();

        bool IEqualityComparer<object>.Equals(object x, object y) => x == y;

        int IEqualityComparer<object>.GetHashCode(object obj) => RuntimeHelpers.GetHashCode(obj);
    }
-->
[!code-csharp[ReferenceEqualityComparer](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/ReferenceEqualityComparer.cs?name=ReferenceEqualityComparer)]

<span data-ttu-id="47228-271">(Ab .net 5 ist diese in der BCL als enthalten <xref:System.Collections.Generic.ReferenceEqualityComparer> .)</span><span class="sxs-lookup"><span data-stu-id="47228-271">(Starting with .NET 5, this is included in the BCL as <xref:System.Collections.Generic.ReferenceEqualityComparer>.)</span></span>

<span data-ttu-id="47228-272">Dieser Vergleich kann dann beim Erstellen von Auflistungs Navigationen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="47228-272">This comparer can then be used when creating collection navigations.</span></span> <span data-ttu-id="47228-273">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="47228-273">For example:</span></span>

<!--
        public ICollection<Order> Orders { get; set; }
            = new HashSet<Order>(ReferenceEqualityComparer.Instance);
-->
[!code-csharp[OrdersCollection](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=OrdersCollection)]

### <a name="comparing-key-properties"></a><span data-ttu-id="47228-274">Vergleichen von Schlüsseleigenschaften</span><span class="sxs-lookup"><span data-stu-id="47228-274">Comparing key properties</span></span>

<span data-ttu-id="47228-275">Neben Gleichheits vergleichen müssen Schlüsselwerte auch geordnet werden.</span><span class="sxs-lookup"><span data-stu-id="47228-275">In addition to equality comparisons, key values also need to be ordered.</span></span> <span data-ttu-id="47228-276">Dies ist wichtig, um Deadlocks zu vermeiden, wenn mehrere Entitäten in einem einzelnen "SaveChanges"-Befehl aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="47228-276">This is important for avoiding deadlocks when updating multiple entities in a single call to SaveChanges.</span></span> <span data-ttu-id="47228-277">Alle Typen, die für die Eigenschaften Primary, Alternate oder Foreign Key sowie für eindeutige Indizes verwendet werden, müssen <xref:System.IComparable%601> und implementieren <xref:System.IEquatable%601> .</span><span class="sxs-lookup"><span data-stu-id="47228-277">All types used for primary, alternate, or foreign key properties, as well as those used for unique indexes, must implement <xref:System.IComparable%601> and <xref:System.IEquatable%601>.</span></span> <span data-ttu-id="47228-278">Typen, die normalerweise als Schlüssel (int, Guid, String usw.) verwendet werden, unterstützen diese Schnittstellen bereits.</span><span class="sxs-lookup"><span data-stu-id="47228-278">Types normally used as keys (int, Guid, string, etc.) already support these interfaces.</span></span> <span data-ttu-id="47228-279">Benutzerdefinierte Schlüsseltypen können diese Schnittstellen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="47228-279">Custom key types may to add these interfaces.</span></span>
