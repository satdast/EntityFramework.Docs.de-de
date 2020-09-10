---
title: Lokale Daten EF6
description: Lokale Daten in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 2eda668b-1e5d-487d-9a8c-0e3beef03fcb
uid: ef6/querying/local-data
ms.openlocfilehash: f7c4c8904a2985901491e423f655d4aea79f666d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620309"
---
# <a name="local-data"></a><span data-ttu-id="dbbad-103">Lokale Daten</span><span class="sxs-lookup"><span data-stu-id="dbbad-103">Local Data</span></span>
<span data-ttu-id="dbbad-104">Wenn Sie eine LINQ-Abfrage direkt für ein dbset ausführen, wird immer eine Abfrage an die Datenbank gesendet. Sie können jedoch auf die Daten zugreifen, die derzeit im Arbeitsspeicher vorhanden sind, indem Sie die dbset. Local-Eigenschaft verwenden.</span><span class="sxs-lookup"><span data-stu-id="dbbad-104">Running a LINQ query directly against a DbSet will always send a query to the database, but you can access the data that is currently in-memory using the DbSet.Local property.</span></span> <span data-ttu-id="dbbad-105">Sie können auch auf die zusätzlichen Informationen zugreifen, die EF über die Entitäten nachverfolgt, indem Sie die Methoden dbcontext. Entry und dbcontext. ChangeTracker. Entries verwenden.</span><span class="sxs-lookup"><span data-stu-id="dbbad-105">You can also access the extra information EF is tracking about your entities using the DbContext.Entry and DbContext.ChangeTracker.Entries methods.</span></span> <span data-ttu-id="dbbad-106">Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="dbbad-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="using-local-to-look-at-local-data"></a><span data-ttu-id="dbbad-107">Verwenden von local, um lokale Daten zu untersuchen</span><span class="sxs-lookup"><span data-stu-id="dbbad-107">Using Local to look at local data</span></span>  

<span data-ttu-id="dbbad-108">Die Local-Eigenschaft von dbset bietet einfachen Zugriff auf die Entitäten der Menge, die zurzeit vom Kontext nachverfolgt werden und nicht als gelöscht markiert wurden.</span><span class="sxs-lookup"><span data-stu-id="dbbad-108">The Local property of DbSet provides simple access to the entities of the set that are currently being tracked by the context and have not been marked as Deleted.</span></span> <span data-ttu-id="dbbad-109">Der Zugriff auf die lokale Eigenschaft bewirkt nie, dass eine Abfrage an die Datenbank gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="dbbad-109">Accessing the Local property never causes a query to be sent to the database.</span></span> <span data-ttu-id="dbbad-110">Dies bedeutet, dass Sie normalerweise verwendet wird, wenn bereits eine Abfrage durchgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="dbbad-110">This means that it is usually used after a query has already been performed.</span></span> <span data-ttu-id="dbbad-111">Die Load-Erweiterungsmethode kann verwendet werden, um eine Abfrage auszuführen, sodass der Kontext die Ergebnisse nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="dbbad-111">The Load extension method can be used to execute a query so that the context tracks the results.</span></span> <span data-ttu-id="dbbad-112">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="dbbad-112">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs from the database into the context
    context.Blogs.Load();

    // Add a new blog to the context
    context.Blogs.Add(new Blog { Name = "My New Blog" });

    // Mark one of the existing blogs as Deleted
    context.Blogs.Remove(context.Blogs.Find(1));

    // Loop over the blogs in the context.
    Console.WriteLine("In Local: ");
    foreach (var blog in context.Blogs.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }

    // Perform a query against the database.
    Console.WriteLine("\nIn DbSet query: ");
    foreach (var blog in context.Blogs)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }
}
```  

<span data-ttu-id="dbbad-113">Wenn wir zwei Blogs in der Datenbank haben: "ADO.net Blog" with a BlogId of 1 und "The Visual Studio Blog" with a BlogId of 2-Wir könnten die folgende Ausgabe erwarten:</span><span class="sxs-lookup"><span data-stu-id="dbbad-113">If we had two blogs in the database - 'ADO.NET Blog' with a BlogId of 1 and 'The Visual Studio Blog' with a BlogId of 2 - we could expect the following output:</span></span>  

```console
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

<span data-ttu-id="dbbad-114">Dies veranschaulicht drei Punkte:</span><span class="sxs-lookup"><span data-stu-id="dbbad-114">This illustrates three points:</span></span>  

- <span data-ttu-id="dbbad-115">Der neue Blog "Mein neuer Blog" ist in der lokalen Sammlung enthalten, obwohl er noch nicht in der Datenbank gespeichert wurde.</span><span class="sxs-lookup"><span data-stu-id="dbbad-115">The new blog 'My New Blog' is included in the Local collection even though it has not yet been saved to the database.</span></span> <span data-ttu-id="dbbad-116">In diesem Blog ist der Primärschlüssel 0 (null), da die Datenbank noch keinen echten Schlüssel für die Entität generiert hat.</span><span class="sxs-lookup"><span data-stu-id="dbbad-116">This blog has a primary key of zero because the database has not yet generated a real key for the entity.</span></span>  
- <span data-ttu-id="dbbad-117">Der ADO.net-Blog ist nicht in der lokalen Auflistung enthalten, obwohl er noch vom Kontext nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="dbbad-117">The 'ADO.NET Blog' is not included in the local collection even though it is still being tracked by the context.</span></span> <span data-ttu-id="dbbad-118">Der Grund hierfür ist, dass wir ihn aus dem dbset entfernt haben und ihn als gelöscht markieren.</span><span class="sxs-lookup"><span data-stu-id="dbbad-118">This is because we removed it from the DbSet thereby marking it as deleted.</span></span>  
- <span data-ttu-id="dbbad-119">Wenn dbset zum Ausführen einer Abfrage verwendet wird, ist der zum Löschen markierte Blog (ADO.net-Blog) in den Ergebnissen enthalten, und der neue Blog (mein neuer Blog), der noch nicht in der Datenbank gespeichert wurde, ist nicht in den Ergebnissen enthalten.</span><span class="sxs-lookup"><span data-stu-id="dbbad-119">When DbSet is used to perform a query the blog marked for deletion (ADO.NET Blog) is included in the results and the new blog (My New Blog) that has not yet been saved to the database is not included in the results.</span></span> <span data-ttu-id="dbbad-120">Dies liegt daran, dass dbset eine Abfrage für die Datenbank ausführt und die zurückgegebenen Ergebnisse immer den Inhalt der Datenbank widerspiegeln.</span><span class="sxs-lookup"><span data-stu-id="dbbad-120">This is because DbSet is performing a query against the database and the results returned always reflect what is in the database.</span></span>  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a><span data-ttu-id="dbbad-121">Verwenden von local zum Hinzufügen und Entfernen von Entitäten aus dem Kontext</span><span class="sxs-lookup"><span data-stu-id="dbbad-121">Using Local to add and remove entities from the context</span></span>  

<span data-ttu-id="dbbad-122">Die Local-Eigenschaft in dbset gibt eine [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) mit gehockten Ereignissen zurück, sodass Sie mit dem Inhalt des Kontexts synchron bleibt.</span><span class="sxs-lookup"><span data-stu-id="dbbad-122">The Local property on DbSet returns an [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) with events hooked up such that it stays in sync with the contents of the context.</span></span> <span data-ttu-id="dbbad-123">Dies bedeutet, dass Entitäten entweder der lokalen Auflistung oder dem dbset hinzugefügt oder daraus entfernt werden können.</span><span class="sxs-lookup"><span data-stu-id="dbbad-123">This means that entities can be added or removed from either the Local collection or the DbSet.</span></span> <span data-ttu-id="dbbad-124">Dies bedeutet auch, dass Abfragen, die neue Entitäten in den Kontext bringen, dazu führen, dass die lokale Sammlung mit diesen Entitäten aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="dbbad-124">It also means that queries that bring new entities into the context will result in the Local collection being updated with those entities.</span></span> <span data-ttu-id="dbbad-125">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="dbbad-125">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load some posts from the database into the context
    context.Posts.Where(p => p.Tags.Contains("entity-framework")).Load();  

    // Get the local collection and make some changes to it
    var localPosts = context.Posts.Local;
    localPosts.Add(new Post { Name = "What's New in EF" });
    localPosts.Remove(context.Posts.Find(1));  

    // Loop over the posts in the context.
    Console.WriteLine("In Local after entity-framework query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }

    var post1 = context.Posts.Find(1);
    Console.WriteLine(
        "State of post 1: {0} is {1}",
        post1.Name,  
        context.Entry(post1).State);  

    // Query some more posts from the database
    context.Posts.Where(p => p.Tags.Contains("asp.net").Load();  

    // Loop over the posts in the context again.
    Console.WriteLine("\nIn Local after asp.net query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }
}
```  

<span data-ttu-id="dbbad-126">Wenn einige Beiträge mit "Entity-Framework" und "ASP.net" gekennzeichnet sind, sieht die Ausgabe in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="dbbad-126">Assuming we had a few posts tagged with 'entity-framework' and 'asp.net' the output may look something like this:</span></span>  

```console
In Local after entity-framework query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
State of post 1: EF Beginners Guide is Deleted

In Local after asp.net query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
Found 4: ASP.NET Beginners Guide with state Unchanged
```  

<span data-ttu-id="dbbad-127">Dies veranschaulicht drei Punkte:</span><span class="sxs-lookup"><span data-stu-id="dbbad-127">This illustrates three points:</span></span>  

- <span data-ttu-id="dbbad-128">Der neue Beitrag ' What es New in EF ', der der lokalen Auflistung hinzugefügt wurde, wird vom Kontext im hinzugefügten Zustand nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="dbbad-128">The new post 'What's New in EF' that was added to the Local collection becomes tracked by the context in the Added state.</span></span> <span data-ttu-id="dbbad-129">Sie wird daher in die Datenbank eingefügt, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="dbbad-129">It will therefore be inserted into the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="dbbad-130">Der Beitrag, der aus der lokalen Sammlung entfernt wurde (EF-Anfänger Handbuch) ist jetzt im Kontext als gelöscht gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="dbbad-130">The post that was removed from the Local collection (EF Beginners Guide) is now marked as deleted in the context.</span></span> <span data-ttu-id="dbbad-131">Daher wird Sie aus der Datenbank gelöscht, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="dbbad-131">It will therefore be deleted from the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="dbbad-132">Der zusätzliche Post (ASP.net Beginners Guide), der mit der zweiten Abfrage in den Kontext geladen wird, wird der lokalen Auflistung automatisch hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="dbbad-132">The additional post (ASP.NET Beginners Guide) loaded into the context with the second query is automatically added to the Local collection.</span></span>  

<span data-ttu-id="dbbad-133">Eine letzte Anmerkung zu Local ist, dass es sich bei einer ObservableCollection-Leistung nicht hervorragend für eine große Anzahl von Entitäten eignet.</span><span class="sxs-lookup"><span data-stu-id="dbbad-133">One final thing to note about Local is that because it is an ObservableCollection performance is not great for large numbers of entities.</span></span> <span data-ttu-id="dbbad-134">Wenn Sie also Tausende von Entitäten in ihrem Kontext verwenden, ist es möglicherweise nicht empfehlenswert, lokale zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="dbbad-134">Therefore if you are dealing with thousands of entities in your context it may not be advisable to use Local.</span></span>  

## <a name="using-local-for-wpf-data-binding"></a><span data-ttu-id="dbbad-135">Verwenden von Local für die WPF-Datenbindung</span><span class="sxs-lookup"><span data-stu-id="dbbad-135">Using Local for WPF data binding</span></span>  

<span data-ttu-id="dbbad-136">Die lokale Eigenschaft in dbset kann direkt für die Datenbindung in einer WPF-Anwendung verwendet werden, da es sich um eine Instanz von ObservableCollection handelt.</span><span class="sxs-lookup"><span data-stu-id="dbbad-136">The Local property on DbSet can be used directly for data binding in a WPF application because it is an instance of ObservableCollection.</span></span> <span data-ttu-id="dbbad-137">Wie in den vorherigen Abschnitten beschrieben, bedeutet dies, dass Sie automatisch mit dem Inhalt des Kontexts synchronisiert wird und der Inhalt des Kontexts automatisch synchron bleibt.</span><span class="sxs-lookup"><span data-stu-id="dbbad-137">As described in the previous sections this means that it will automatically stay in sync with the contents of the context and the contents of the context will automatically stay in sync with it.</span></span> <span data-ttu-id="dbbad-138">Beachten Sie, dass Sie die lokale Sammlung mit Daten füllen müssen, damit Sie an eine Bindung gebunden werden, da local nie eine Datenbankabfrage verursacht.</span><span class="sxs-lookup"><span data-stu-id="dbbad-138">Note that you do need to pre-populate the Local collection with data for there to be anything to bind to since Local never causes a database query.</span></span>  

<span data-ttu-id="dbbad-139">Dies ist kein geeigneter Ort für ein vollständiges WPF-Daten Bindungs Beispiel, aber die wichtigsten Elemente sind:</span><span class="sxs-lookup"><span data-stu-id="dbbad-139">This is not an appropriate place for a full WPF data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="dbbad-140">Einrichten einer Bindungs Quelle</span><span class="sxs-lookup"><span data-stu-id="dbbad-140">Setup a binding source</span></span>  
- <span data-ttu-id="dbbad-141">Binden Sie es an die lokale Eigenschaft ihrer Gruppe.</span><span class="sxs-lookup"><span data-stu-id="dbbad-141">Bind it to the Local property of your set</span></span>  
- <span data-ttu-id="dbbad-142">Füllen Sie local mithilfe einer Abfrage an die Datenbank auf.</span><span class="sxs-lookup"><span data-stu-id="dbbad-142">Populate Local using a query to the database.</span></span>  

## <a name="wpf-binding-to-navigation-properties"></a><span data-ttu-id="dbbad-143">WPF-Bindung an Navigations Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="dbbad-143">WPF binding to navigation properties</span></span>  

<span data-ttu-id="dbbad-144">Wenn Sie die Master/Detail-Datenbindung durchgeführt haben, möchten Sie möglicherweise die Detailansicht an eine Navigations Eigenschaft einer ihrer Entitäten binden.</span><span class="sxs-lookup"><span data-stu-id="dbbad-144">If you are doing master/detail data binding you may want to bind the detail view to a navigation property of one of your entities.</span></span> <span data-ttu-id="dbbad-145">Eine einfache Möglichkeit, dies zu tun, ist die Verwendung einer ObservableCollection für die Navigations Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="dbbad-145">An easy way to make this work is to use an ObservableCollection for the navigation property.</span></span> <span data-ttu-id="dbbad-146">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="dbbad-146">For example:</span></span>  

``` csharp
public class Blog
{
    private readonly ObservableCollection<Post> _posts =
        new ObservableCollection<Post>();

    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual ObservableCollection<Post> Posts
    {
        get { return _posts; }
    }
}
```  

## <a name="using-local-to-clean-up-entities-in-savechanges"></a><span data-ttu-id="dbbad-147">Verwenden von local zum Bereinigen von Entitäten in SaveChanges</span><span class="sxs-lookup"><span data-stu-id="dbbad-147">Using Local to clean up entities in SaveChanges</span></span>  

<span data-ttu-id="dbbad-148">In den meisten Fällen werden Entitäten, die aus einer Navigations Eigenschaft entfernt werden, im Kontext nicht automatisch als gelöscht markiert.</span><span class="sxs-lookup"><span data-stu-id="dbbad-148">In most cases entities removed from a navigation property will not be automatically marked as deleted in the context.</span></span> <span data-ttu-id="dbbad-149">Wenn Sie z. b. ein Post-Objekt aus der Blog. Posts-Sammlung entfernen, wird dieser Beitrag nicht automatisch gelöscht, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="dbbad-149">For example, if you remove a Post object from the Blog.Posts collection then that post will not be automatically deleted when SaveChanges is called.</span></span> <span data-ttu-id="dbbad-150">Wenn Sie den Löschvorgang benötigen, müssen Sie möglicherweise diese verbleibenden Entitäten suchen und als gelöscht markieren, bevor Sie SaveChanges oder als Teil einer überschriebenen SaveChanges aufrufen.</span><span class="sxs-lookup"><span data-stu-id="dbbad-150">If you need it to be deleted then you may need to find these dangling entities and mark them as deleted before calling SaveChanges or as part of an overridden SaveChanges.</span></span> <span data-ttu-id="dbbad-151">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="dbbad-151">For example:</span></span>  

``` csharp
public override int SaveChanges()
{
    foreach (var post in this.Posts.Local.ToList())
    {
        if (post.Blog == null)
        {
            this.Posts.Remove(post);
        }
    }

    return base.SaveChanges();
}
```  

<span data-ttu-id="dbbad-152">Der obige Code verwendet die lokale Auflistung, um nach allen Beiträgen zu suchen, und kennzeichnet alle, die keinen Blog Verweis als gelöscht haben.</span><span class="sxs-lookup"><span data-stu-id="dbbad-152">The code above uses the Local collection to find all posts and marks any that do not have a blog reference as deleted.</span></span> <span data-ttu-id="dbbad-153">Der Auflistungs aufrufsvorgang ist erforderlich, da die Auflistung andernfalls durch den remove-Befehl geändert wird, während diese aufgelistet wird.</span><span class="sxs-lookup"><span data-stu-id="dbbad-153">The ToList call is required because otherwise the collection will be modified by the Remove call while it is being enumerated.</span></span> <span data-ttu-id="dbbad-154">In den meisten anderen Situationen können Sie die lokale Eigenschaft direkt Abfragen, ohne zuerst zuerst "-List" zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="dbbad-154">In most other situations you can query directly against the Local property without using ToList first.</span></span>  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a><span data-ttu-id="dbbad-155">Verwenden von "local" und "ToBindingList" für Windows Forms Datenbindung</span><span class="sxs-lookup"><span data-stu-id="dbbad-155">Using Local and ToBindingList for Windows Forms data binding</span></span>  

<span data-ttu-id="dbbad-156">Windows Forms unterstützt die Datenbindung mit vollständiger Genauigkeit nicht direkt mithilfe von ObservableCollection.</span><span class="sxs-lookup"><span data-stu-id="dbbad-156">Windows Forms does not support full fidelity data binding using ObservableCollection directly.</span></span> <span data-ttu-id="dbbad-157">Sie können jedoch weiterhin die lokale dbset-Eigenschaft für die Datenbindung verwenden, um alle Vorteile zu erhalten, die in den vorherigen Abschnitten beschrieben wurden.</span><span class="sxs-lookup"><span data-stu-id="dbbad-157">However, you can still use the DbSet Local property for data binding to get all the benefits described in the previous sections.</span></span> <span data-ttu-id="dbbad-158">Dies wird durch die "debindinglist"-Erweiterungsmethode erreicht, die eine [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) -Implementierung erstellt, die von der lokalen ObservableCollection unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="dbbad-158">This is achieved through the ToBindingList extension method which creates an [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) implementation backed by the Local ObservableCollection.</span></span>  

<span data-ttu-id="dbbad-159">Dies ist kein geeigneter Ort für ein vollständiges Windows Forms Daten Bindungs Beispiel, aber die wichtigsten Elemente sind:</span><span class="sxs-lookup"><span data-stu-id="dbbad-159">This is not an appropriate place for a full Windows Forms data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="dbbad-160">Einrichten einer Objekt Bindungs Quelle</span><span class="sxs-lookup"><span data-stu-id="dbbad-160">Setup an object binding source</span></span>  
- <span data-ttu-id="dbbad-161">Binden Sie es an die lokale Eigenschaft ihrer Gruppe, indem Sie local. debindinglist () verwenden.</span><span class="sxs-lookup"><span data-stu-id="dbbad-161">Bind it to the Local property of your set using Local.ToBindingList()</span></span>  
- <span data-ttu-id="dbbad-162">Lokales Auffüllen mithilfe einer Abfrage an die Datenbank</span><span class="sxs-lookup"><span data-stu-id="dbbad-162">Populate Local using a query to the database</span></span>  

## <a name="getting-detailed-information-about-tracked-entities"></a><span data-ttu-id="dbbad-163">Ausführliche Informationen zu nach verfolgten Entitäten</span><span class="sxs-lookup"><span data-stu-id="dbbad-163">Getting detailed information about tracked entities</span></span>  

<span data-ttu-id="dbbad-164">Viele der Beispiele in dieser Reihe verwenden die Entry-Methode, um eine dbentityentry-Instanz für eine Entität zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="dbbad-164">Many of the examples in this series use the Entry method to return a DbEntityEntry instance for an entity.</span></span> <span data-ttu-id="dbbad-165">Dieses Einstiegs Objekt fungiert dann als Ausgangspunkt für das Sammeln von Informationen über die Entität, z. b. den aktuellen Status, sowie für das Ausführen von Vorgängen für die Entität, z. b. das explizite Laden einer verknüpften Entität</span><span class="sxs-lookup"><span data-stu-id="dbbad-165">This entry object then acts as the starting point for gathering information about the entity such as its current state, as well as for performing operations on the entity such as explicitly loading a related entity.</span></span>  

<span data-ttu-id="dbbad-166">Die Entries-Methoden geben dbentityentry-Objekte für viele oder alle Entitäten zurück, die vom Kontext nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="dbbad-166">The Entries methods return DbEntityEntry objects for many or all entities being tracked by the context.</span></span> <span data-ttu-id="dbbad-167">Dies ermöglicht es Ihnen, Informationen zu sammeln oder Vorgänge für viele Entitäten statt nur für einen einzelnen Eintrag auszuführen.</span><span class="sxs-lookup"><span data-stu-id="dbbad-167">This allows you to gather information or perform operations on many entities rather than just a single entry.</span></span> <span data-ttu-id="dbbad-168">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="dbbad-168">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load some entities into the context
    context.Blogs.Load();
    context.Authors.Load();
    context.Readers.Load();

    // Make some changes
    context.Blogs.Find(1).Title = "The New ADO.NET Blog";
    context.Blogs.Remove(context.Blogs.Find(2));
    context.Authors.Add(new Author { Name = "Jane Doe" });
    context.Readers.Find(1).Username = "johndoe1987";

    // Look at the state of all entities in the context
    Console.WriteLine("All tracked entities: ");
    foreach (var entry in context.ChangeTracker.Entries())
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Find modified entities of any type
    Console.WriteLine("\nAll modified entities: ");
    foreach (var entry in context.ChangeTracker.Entries()
                              .Where(e => e.State == EntityState.Modified))
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Get some information about just the tracked blogs
    Console.WriteLine("\nTracked blogs: ");
    foreach (var entry in context.ChangeTracker.Entries<Blog>())
    {
        Console.WriteLine(
            "Found Blog {0}: {1} with original Name {2}",
            entry.Entity.BlogId,  
            entry.Entity.Name,
            entry.Property(p => p.Name).OriginalValue);
    }

    // Find all people (author or reader)
    Console.WriteLine("\nPeople: ");
    foreach (var entry in context.ChangeTracker.Entries<IPerson>())
    {
        Console.WriteLine("Found Person {0}", entry.Entity.Name);
    }
}
```  

<span data-ttu-id="dbbad-169">Sie werden feststellen, dass wir eine Author-und Reader-Klasse in das Beispiel einführen. beide Klassen implementieren die IPerson-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="dbbad-169">You'll notice we are introducing a Author and Reader class into the example - both of these classes implement the IPerson interface.</span></span>  

``` csharp
public class Author : IPerson
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public string Biography { get; set; }
}

public class Reader : IPerson
{
    public int ReaderId { get; set; }
    public string Name { get; set; }
    public string Username { get; set; }
}

public interface IPerson
{
    string Name { get; }
}
```  

<span data-ttu-id="dbbad-170">Angenommen, wir haben die folgenden Daten in der Datenbank:</span><span class="sxs-lookup"><span data-stu-id="dbbad-170">Let's assume we have the following data in the database:</span></span>

<span data-ttu-id="dbbad-171">Blog mit BlogId = 1 und Name = "ADO.net Blog"</span><span class="sxs-lookup"><span data-stu-id="dbbad-171">Blog with BlogId = 1 and Name = 'ADO.NET Blog'</span></span>  
<span data-ttu-id="dbbad-172">Blog mit BlogId = 2 und Name = "der Visual Studio-Blog"</span><span class="sxs-lookup"><span data-stu-id="dbbad-172">Blog with BlogId = 2 and Name = 'The Visual Studio Blog'</span></span>  
<span data-ttu-id="dbbad-173">Blog mit BlogId = 3 und Name = ".NET Framework Blog"</span><span class="sxs-lookup"><span data-stu-id="dbbad-173">Blog with BlogId = 3 and Name = '.NET Framework Blog'</span></span>  
<span data-ttu-id="dbbad-174">Autor mit AutorID = 1 und Name = ' Joe Bloggs '</span><span class="sxs-lookup"><span data-stu-id="dbbad-174">Author with AuthorId = 1 and Name = 'Joe Bloggs'</span></span>  
<span data-ttu-id="dbbad-175">Reader mit readerid = 1 und Name = ' John Doe '</span><span class="sxs-lookup"><span data-stu-id="dbbad-175">Reader with ReaderId = 1 and Name = 'John Doe'</span></span>  

<span data-ttu-id="dbbad-176">Die Ausgabe der Ausführung des Codes lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="dbbad-176">The output from running the code would be:</span></span>  

```console
All tracked entities:
Found entity of type Blog with state Modified
Found entity of type Blog with state Deleted
Found entity of type Blog with state Unchanged
Found entity of type Author with state Unchanged
Found entity of type Author with state Added
Found entity of type Reader with state Modified

All modified entities:
Found entity of type Blog with state Modified
Found entity of type Reader with state Modified

Tracked blogs:
Found Blog 1: The New ADO.NET Blog with original Name ADO.NET Blog
Found Blog 2: The Visual Studio Blog with original Name The Visual Studio Blog
Found Blog 3: .NET Framework Blog with original Name .NET Framework Blog

People:
Found Person John Doe
Found Person Joe Bloggs
Found Person Jane Doe
```  

<span data-ttu-id="dbbad-177">In diesen Beispielen werden mehrere Punkte veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="dbbad-177">These examples illustrate several points:</span></span>  

- <span data-ttu-id="dbbad-178">Die Entries-Methoden geben Einträge für Entitäten in allen Zuständen zurück, einschließlich des gelöschten.</span><span class="sxs-lookup"><span data-stu-id="dbbad-178">The Entries methods return entries for entities in all states, including Deleted.</span></span> <span data-ttu-id="dbbad-179">Vergleichen Sie dies mit Local, wodurch gelöschte Entitäten ausgeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="dbbad-179">Compare this to Local which excludes Deleted entities.</span></span>  
- <span data-ttu-id="dbbad-180">Einträge für alle Entitäts Typen werden zurückgegeben, wenn die nicht generische Entries-Methode verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="dbbad-180">Entries for all entity types are returned when the non-generic Entries method is used.</span></span> <span data-ttu-id="dbbad-181">Wenn die generische Entries-Methode verwendet wird, werden Einträge nur für Entitäten zurückgegeben, die Instanzen des generischen Typs sind.</span><span class="sxs-lookup"><span data-stu-id="dbbad-181">When the generic entries method is used entries are only returned for entities that are instances of the generic type.</span></span> <span data-ttu-id="dbbad-182">Dies wurde oben verwendet, um Einträge für alle Blogs zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="dbbad-182">This was used above to get entries for all blogs.</span></span> <span data-ttu-id="dbbad-183">Es wurde auch verwendet, um Einträge für alle Entitäten zu erhalten, die IPerson implementieren.</span><span class="sxs-lookup"><span data-stu-id="dbbad-183">It was also used to get entries for all entities that implement IPerson.</span></span> <span data-ttu-id="dbbad-184">Dies zeigt, dass es sich bei dem generischen Typ nicht um einen tatsächlichen Entitätstyp handeln muss.</span><span class="sxs-lookup"><span data-stu-id="dbbad-184">This demonstrates that the generic type does not have to be an actual entity type.</span></span>  
- <span data-ttu-id="dbbad-185">LINQ to Objects können verwendet werden, um die zurückgegebenen Ergebnisse zu filtern.</span><span class="sxs-lookup"><span data-stu-id="dbbad-185">LINQ to Objects can be used to filter the results returned.</span></span> <span data-ttu-id="dbbad-186">Dies wurde oben verwendet, um Entitäten eines beliebigen Typs zu suchen, solange Sie geändert werden.</span><span class="sxs-lookup"><span data-stu-id="dbbad-186">This was used above to find entities of any type as long as they are modified.</span></span>  

<span data-ttu-id="dbbad-187">Beachten Sie, dass die dbentityentry-Instanzen immer eine Entität ungleich NULL enthalten.</span><span class="sxs-lookup"><span data-stu-id="dbbad-187">Note that DbEntityEntry instances always contain a non-null Entity.</span></span> <span data-ttu-id="dbbad-188">Beziehungs Einträge und Stub-Einträge werden nicht als dbentityentry-Instanzen dargestellt, sodass es nicht erforderlich ist, diese zu filtern.</span><span class="sxs-lookup"><span data-stu-id="dbbad-188">Relationship entries and stub entries are not represented as DbEntityEntry instances so there is no need to filter for these.</span></span>
