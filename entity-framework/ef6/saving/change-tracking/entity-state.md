---
title: Arbeiten mit Entitäts Zuständen EF6
description: Arbeiten mit Entitäts Zuständen in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/entity-state
ms.openlocfilehash: 5373f7157882062e8a73b2bd414c6a8b9accdba4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064457"
---
# <a name="working-with-entity-states"></a><span data-ttu-id="e47cb-103">Arbeiten mit Entitäts Zuständen</span><span class="sxs-lookup"><span data-stu-id="e47cb-103">Working with entity states</span></span>
<span data-ttu-id="e47cb-104">In diesem Thema wird das Hinzufügen und Anfügen von Entitäten zu einem Kontext und die Art und Weise beschrieben, wie Entity Framework diese während SaveChanges verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="e47cb-104">This topic will cover how to add and attach entities to a context and how Entity Framework processes these during SaveChanges.</span></span>
<span data-ttu-id="e47cb-105">Entity Framework übernimmt die Überwachung des Zustands von Entitäten, während Sie mit einem Kontext verbunden sind, aber in getrennten oder N-Tier-Szenarios können Sie EF wissen, in welchem Zustand sich Ihre Entitäten befinden sollten.</span><span class="sxs-lookup"><span data-stu-id="e47cb-105">Entity Framework takes care of tracking the state of entities while they are connected to a context, but in disconnected or N-Tier scenarios you can let EF know what state your entities should be in.</span></span>
<span data-ttu-id="e47cb-106">Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="e47cb-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="entity-states-and-savechanges"></a><span data-ttu-id="e47cb-107">Entitäts Zustände und SaveChanges</span><span class="sxs-lookup"><span data-stu-id="e47cb-107">Entity states and SaveChanges</span></span>

<span data-ttu-id="e47cb-108">Eine Entität kann einen von fünf Zuständen aufweisen, wie von der EntityState-Enumeration definiert.</span><span class="sxs-lookup"><span data-stu-id="e47cb-108">An entity can be in one of five states as defined by the EntityState enumeration.</span></span> <span data-ttu-id="e47cb-109">Folgende Status sind möglich:</span><span class="sxs-lookup"><span data-stu-id="e47cb-109">These states are:</span></span>  

- <span data-ttu-id="e47cb-110">Hinzugefügt: die Entität wird vom Kontext nachverfolgt, ist aber noch nicht in der Datenbank vorhanden.</span><span class="sxs-lookup"><span data-stu-id="e47cb-110">Added: the entity is being tracked by the context but does not yet exist in the database</span></span>  
- <span data-ttu-id="e47cb-111">Unverändert: die Entität wird vom Kontext nachverfolgt und ist in der Datenbank vorhanden, und die Eigenschaftswerte wurden aus den Werten in der Datenbank nicht geändert.</span><span class="sxs-lookup"><span data-stu-id="e47cb-111">Unchanged: the entity is being tracked by the context and exists in the database, and its property values have not changed from the values in the database</span></span>  
- <span data-ttu-id="e47cb-112">Geändert: die Entität wird vom Kontext nachverfolgt und ist in der Datenbank vorhanden, und einige oder alle Eigenschaftswerte wurden geändert.</span><span class="sxs-lookup"><span data-stu-id="e47cb-112">Modified: the entity is being tracked by the context and exists in the database, and some or all of its property values have been modified</span></span>  
- <span data-ttu-id="e47cb-113">Gelöscht: die Entität wird vom Kontext nachverfolgt und ist in der Datenbank vorhanden, wurde aber beim nächsten Aufrufen von SaveChanges zum Löschen aus der Datenbank markiert.</span><span class="sxs-lookup"><span data-stu-id="e47cb-113">Deleted: the entity is being tracked by the context and exists in the database, but has been marked for deletion from the database the next time SaveChanges is called</span></span>  
- <span data-ttu-id="e47cb-114">Getrennt: die Entität wird nicht vom Kontext nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="e47cb-114">Detached: the entity is not being tracked by the context</span></span>  

<span data-ttu-id="e47cb-115">"SaveChanges" unterscheidet sich für Entitäten in unterschiedlichen Zuständen:</span><span class="sxs-lookup"><span data-stu-id="e47cb-115">SaveChanges does different things for entities in different states:</span></span>  

- <span data-ttu-id="e47cb-116">Nicht geänderte Entitäten werden von SaveChanges nicht berührt.</span><span class="sxs-lookup"><span data-stu-id="e47cb-116">Unchanged entities are not touched by SaveChanges.</span></span> <span data-ttu-id="e47cb-117">Für Entitäten mit dem Status "unverändert" werden keine Updates an die Datenbank gesendet.</span><span class="sxs-lookup"><span data-stu-id="e47cb-117">Updates are not sent to the database for entities in the Unchanged state.</span></span>  
- <span data-ttu-id="e47cb-118">Hinzugefügte Entitäten werden in die Datenbank eingefügt und dann bei Rückgabe von SaveChanges unverändert.</span><span class="sxs-lookup"><span data-stu-id="e47cb-118">Added entities are inserted into the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="e47cb-119">Geänderte Entitäten werden in der Datenbank aktualisiert und werden bei Rückgabe von SaveChanges unverändert.</span><span class="sxs-lookup"><span data-stu-id="e47cb-119">Modified entities are updated in the database and then become Unchanged when SaveChanges returns.</span></span>  
- <span data-ttu-id="e47cb-120">Gelöschte Entitäten werden aus der Datenbank gelöscht und dann vom Kontext getrennt.</span><span class="sxs-lookup"><span data-stu-id="e47cb-120">Deleted entities are deleted from the database and are then detached from the context.</span></span>  

<span data-ttu-id="e47cb-121">In den folgenden Beispielen wird veranschaulicht, wie der Zustand einer Entität oder eines Entitäts Diagramms geändert werden kann.</span><span class="sxs-lookup"><span data-stu-id="e47cb-121">The following examples show ways in which the state of an entity or an entity graph can be changed.</span></span>  

## <a name="adding-a-new-entity-to-the-context"></a><span data-ttu-id="e47cb-122">Hinzufügen einer neuen Entität zum Kontext</span><span class="sxs-lookup"><span data-stu-id="e47cb-122">Adding a new entity to the context</span></span>  

<span data-ttu-id="e47cb-123">Eine neue Entität kann dem Kontext hinzugefügt werden, indem die Add-Methode für dbset aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="e47cb-123">A new entity can be added to the context by calling the Add method on DbSet.</span></span>
<span data-ttu-id="e47cb-124">Dadurch wird die Entität in den hinzugefügten Zustand versetzt, was bedeutet, dass Sie beim nächsten Aufruf von SaveChanges in die Datenbank eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="e47cb-124">This puts the entity into the Added state, meaning that it will be inserted into the database the next time that SaveChanges is called.</span></span>
<span data-ttu-id="e47cb-125">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e47cb-125">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

<span data-ttu-id="e47cb-126">Eine weitere Möglichkeit zum Hinzufügen einer neuen Entität zum Kontext besteht darin, den Zustand in hinzugefügt zu ändern.</span><span class="sxs-lookup"><span data-stu-id="e47cb-126">Another way to add a new entity to the context is to change its state to Added.</span></span> <span data-ttu-id="e47cb-127">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e47cb-127">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

<span data-ttu-id="e47cb-128">Schließlich können Sie dem Kontext eine neue Entität hinzufügen, indem Sie Sie mit einer anderen Entität verknüpfen, die bereits nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="e47cb-128">Finally, you can add a new entity to the context by hooking it up to another entity that is already being tracked.</span></span>
<span data-ttu-id="e47cb-129">Dazu kann die neue Entität der Auflistungs Navigations Eigenschaft einer anderen Entität hinzugefügt werden oder indem eine Verweis Navigations Eigenschaft einer anderen Entität festgelegt wird, um auf die neue Entität zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="e47cb-129">This could be by adding the new entity to the collection navigation property of another entity or by setting a reference navigation property of another entity to point to the new entity.</span></span> <span data-ttu-id="e47cb-130">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e47cb-130">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Add a new User by setting a reference from a tracked Blog
    var blog = context.Blogs.Find(1);
    blog.Owner = new User { UserName = "johndoe1987" };

    // Add a new Post by adding to the collection of a tracked Blog
    blog.Posts.Add(new Post { Name = "How to Add Entities" });

    context.SaveChanges();
}
```  

<span data-ttu-id="e47cb-131">Beachten Sie, dass für alle diese Beispiele, wenn die hinzugefügte Entität Verweise auf andere Entitäten aufweist, die noch nicht nachverfolgt werden, diese neuen Entitäten ebenfalls dem Kontext hinzugefügt werden und beim nächsten Aufruf von SaveChanges in die Datenbank eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="e47cb-131">Note that for all of these examples if the entity being added has references to other entities that are not yet tracked then these new entities will also be added to the context and will be inserted into the database the next time that SaveChanges is called.</span></span>  

## <a name="attaching-an-existing-entity-to-the-context"></a><span data-ttu-id="e47cb-132">Anfügen einer vorhandenen Entität an den Kontext</span><span class="sxs-lookup"><span data-stu-id="e47cb-132">Attaching an existing entity to the context</span></span>  

<span data-ttu-id="e47cb-133">Wenn Sie bereits über eine Entität verfügen, die bereits in der Datenbank vorhanden ist, die aber zurzeit nicht vom Kontext nachverfolgt wird, können Sie den Kontext darüber informieren, dass die Entität mithilfe der Attach-Methode in dbset nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="e47cb-133">If you have an entity that you know already exists in the database but which is not currently being tracked by the context then you can tell the context to track the entity using the Attach method on DbSet.</span></span> <span data-ttu-id="e47cb-134">Die Entität befindet sich im Zustand "unverändert" im Kontext.</span><span class="sxs-lookup"><span data-stu-id="e47cb-134">The entity will be in the Unchanged state in the context.</span></span> <span data-ttu-id="e47cb-135">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e47cb-135">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="e47cb-136">Beachten Sie, dass keine Änderungen an der Datenbank vorgenommen werden, wenn SaveChanges aufgerufen wird, ohne dass eine andere Bearbeitung der angefügten Entität erfolgt.</span><span class="sxs-lookup"><span data-stu-id="e47cb-136">Note that no changes will be made to the database if SaveChanges is called without doing any other manipulation of the attached entity.</span></span> <span data-ttu-id="e47cb-137">Dies liegt daran, dass sich die Entität im unveränderten Zustand befindet.</span><span class="sxs-lookup"><span data-stu-id="e47cb-137">This is because the entity is in the Unchanged state.</span></span>  

<span data-ttu-id="e47cb-138">Eine andere Möglichkeit, eine vorhandene Entität an den Kontext anzufügen, besteht darin, den Zustand in unverändert zu ändern.</span><span class="sxs-lookup"><span data-stu-id="e47cb-138">Another way to attach an existing entity to the context is to change its state to Unchanged.</span></span> <span data-ttu-id="e47cb-139">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e47cb-139">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="e47cb-140">Beachten Sie, dass in beiden Beispielen, wenn die angefügte Entität Verweise auf andere Entitäten enthält, die noch nicht nachverfolgt werden, diese neuen Entitäten ebenfalls im unveränderten Zustand an den Kontext angefügt werden.</span><span class="sxs-lookup"><span data-stu-id="e47cb-140">Note that for both of these examples if the entity being attached has references to other entities that are not yet tracked then these new entities will also attached to the context in the Unchanged state.</span></span>  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a><span data-ttu-id="e47cb-141">Anfügen einer vorhandenen, aber geänderten Entität an den Kontext</span><span class="sxs-lookup"><span data-stu-id="e47cb-141">Attaching an existing but modified entity to the context</span></span>  

<span data-ttu-id="e47cb-142">Wenn Sie bereits über eine Entität verfügen, die bereits in der Datenbank vorhanden ist, für die jedoch möglicherweise Änderungen vorgenommen wurden, können Sie den Kontext anfügen, um die Entität anzufügen und ihren Status auf "geändert" festzulegen.</span><span class="sxs-lookup"><span data-stu-id="e47cb-142">If you have an entity that you know already exists in the database but to which changes may have been made then you can tell the context to attach the entity and set its state to Modified.</span></span>
<span data-ttu-id="e47cb-143">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e47cb-143">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="e47cb-144">Wenn Sie den Status in geändert ändern, werden alle Eigenschaften der Entität als geändert markiert, und alle Eigenschaftswerte werden an die Datenbank gesendet, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="e47cb-144">When you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  

<span data-ttu-id="e47cb-145">Beachten Sie Folgendes: Wenn die anzufügende Entität Verweise auf andere Entitäten enthält, die noch nicht nachverfolgt werden, werden diese neuen Entitäten im unveränderten Zustand an den Kontext angefügt – Sie werden nicht automatisch geändert.</span><span class="sxs-lookup"><span data-stu-id="e47cb-145">Note that if the entity being attached has references to other entities that are not yet tracked, then these new entities will attached to the context in the Unchanged state—they will not automatically be made Modified.</span></span>
<span data-ttu-id="e47cb-146">Wenn Sie über mehrere Entitäten verfügen, die als geändert markiert werden müssen, sollten Sie den Status für jede dieser Entitäten einzeln festlegen.</span><span class="sxs-lookup"><span data-stu-id="e47cb-146">If you have multiple entities that need to be marked Modified you should set the state for each of these entities individually.</span></span>  

## <a name="changing-the-state-of-a-tracked-entity"></a><span data-ttu-id="e47cb-147">Ändern des Status einer nach verfolgten Entität</span><span class="sxs-lookup"><span data-stu-id="e47cb-147">Changing the state of a tracked entity</span></span>  

<span data-ttu-id="e47cb-148">Sie können den Zustand einer Entität, die bereits nachverfolgt wird, ändern, indem Sie die State-Eigenschaft für Ihren Eintrag festlegen.</span><span class="sxs-lookup"><span data-stu-id="e47cb-148">You can change the state of an entity that is already being tracked by setting the State property on its entry.</span></span> <span data-ttu-id="e47cb-149">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e47cb-149">For example:</span></span>  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

<span data-ttu-id="e47cb-150">Beachten Sie, dass das Aufrufen von Add oder Attach für eine Entität, die bereits nachverfolgt ist, auch zum Ändern des Entitäts Zustands verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="e47cb-150">Note that calling Add or Attach for an entity that is already tracked can also be used to change the entity state.</span></span> <span data-ttu-id="e47cb-151">Wenn z. b. Attach für eine Entität aufgerufen wird, die sich derzeit im hinzugefügten Zustand befindet, ändert sich der Status in unverändert.</span><span class="sxs-lookup"><span data-stu-id="e47cb-151">For example, calling Attach for an entity that is currently in the Added state will change its state to Unchanged.</span></span>  

## <a name="insert-or-update-pattern"></a><span data-ttu-id="e47cb-152">INSERT-oder Update-Muster</span><span class="sxs-lookup"><span data-stu-id="e47cb-152">Insert or update pattern</span></span>  

<span data-ttu-id="e47cb-153">Ein gängiges Muster für einige Anwendungen besteht darin, entweder eine Entität als neu hinzuzufügen (was zu einer Daten Bank Einfügung führt) oder eine Entität als vorhanden anzufügen und Sie als geändert (d. h. ein Datenbankupdate) zu kennzeichnen, je nach dem Wert des Primärschlüssels.</span><span class="sxs-lookup"><span data-stu-id="e47cb-153">A common pattern for some applications is to either Add an entity as new (resulting in a database insert) or Attach an entity as existing and mark it as modified (resulting in a database update) depending on the value of the primary key.</span></span>
<span data-ttu-id="e47cb-154">Wenn z. b. Daten Bank generierte ganzzahlige Primärschlüssel verwendet werden, wird eine Entität mit einem NULL-Schlüssel als neu und eine Entität mit einem Schlüssel ungleich 0 (null) als vorhanden behandelt.</span><span class="sxs-lookup"><span data-stu-id="e47cb-154">For example, when using database generated integer primary keys it is common to treat an entity with a zero key as new and an entity with a non-zero key as existing.</span></span>
<span data-ttu-id="e47cb-155">Dieses Muster kann erreicht werden, indem der Entitäts Zustand basierend auf einer Überprüfung des Primärschlüssel Werts festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="e47cb-155">This pattern can be achieved by setting the entity state based on a check of the primary key value.</span></span> <span data-ttu-id="e47cb-156">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e47cb-156">For example:</span></span>  

``` csharp
public void InsertOrUpdate(Blog blog)
{
    using (var context = new BloggingContext())
    {
        context.Entry(blog).State = blog.BlogId == 0 ?
                                   EntityState.Added :
                                   EntityState.Modified;

        context.SaveChanges();
    }
}
```  

<span data-ttu-id="e47cb-157">Beachten Sie, dass beim Ändern des Zustands in geändert alle Eigenschaften der Entität als geändert gekennzeichnet werden und alle Eigenschaftswerte an die Datenbank gesendet werden, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="e47cb-157">Note that when you change the state to Modified all the properties of the entity will be marked as modified and all the property values will be sent to the database when SaveChanges is called.</span></span>  
