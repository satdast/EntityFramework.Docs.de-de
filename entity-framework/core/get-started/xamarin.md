---
title: Erste Schritte mit EF Core und Xamarin – EF Core
description: Tutorial zu den ersten Schritten für die Verwendung von Xamarin zum Erstellen mobiler Apps mit Entity Framework Core
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: b4a7e2260337d74329d309e9db32fe97a2131d73
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619295"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a><span data-ttu-id="5a63e-103">Erste Schritte mit EF Core und Xamarin</span><span class="sxs-lookup"><span data-stu-id="5a63e-103">Getting Started with EF Core and Xamarin</span></span>

<span data-ttu-id="5a63e-104">In diesem Tutorial erstellen Sie eine [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms)-Anwendung, die den Datenzugriff auf eine SQLite-Datenbank mithilfe von Entity Framework Core durchführt.</span><span class="sxs-lookup"><span data-stu-id="5a63e-104">In this tutorial, you create a [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) application that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="5a63e-105">Sie können dieses Tutorial mit Visual Studio unter Windows oder Visual Studio für Mac abschließen.</span><span class="sxs-lookup"><span data-stu-id="5a63e-105">You can follow the tutorial by using Visual Studio on Windows or Visual Studio for Mac.</span></span>

> [!TIP]
> <span data-ttu-id="5a63e-106">Das in diesem Artikel verwendete [Beispiel finden Sie auf GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span><span class="sxs-lookup"><span data-stu-id="5a63e-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5a63e-107">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="5a63e-107">Prerequisites</span></span>

<span data-ttu-id="5a63e-108">Installieren Sie eines der folgenden Tools:</span><span class="sxs-lookup"><span data-stu-id="5a63e-108">Install one of the below:</span></span>

* <span data-ttu-id="5a63e-109">[Visual Studio 2019 Version 16.3 oder höher ](https://www.visualstudio.com/downloads/) mit dieser Workload:</span><span class="sxs-lookup"><span data-stu-id="5a63e-109">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this workload:</span></span>
  * <span data-ttu-id="5a63e-110">**Mobile Entwicklung mit .NET**</span><span class="sxs-lookup"><span data-stu-id="5a63e-110">**Mobile Development with .NET**</span></span>
* [<span data-ttu-id="5a63e-111">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="5a63e-111">Visual Studio for Mac</span></span>](https://visualstudio.microsoft.com/vs/mac/)

<span data-ttu-id="5a63e-112">In dieser [Dokumentation finden Sie ausführliche Installationsanweisungen](/xamarin/get-started/installation) für jede Plattform.</span><span class="sxs-lookup"><span data-stu-id="5a63e-112">This [documentation provides detailed step-by-step installation instructions](/xamarin/get-started/installation) for each platform.</span></span>

## <a name="download-and-run-the-sample-project"></a><span data-ttu-id="5a63e-113">Herunterladen und Ausführen des Beispielprojekts</span><span class="sxs-lookup"><span data-stu-id="5a63e-113">Download and run the sample project</span></span>

<span data-ttu-id="5a63e-114">Laden Sie den Code von GitHub herunter, um diese Beispielanwendung auszuführen und zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="5a63e-114">To run and explore this sample application, download the code on GitHub.</span></span>

<span data-ttu-id="5a63e-115">Sobald Sie sie heruntergeladen haben, öffnen Sie die Projektmappendatei `EFGettingStarted.sln` in Visual Studio oder Visual Studio für Mac, und führen Sie die Anwendung auf der Plattform Ihrer Wahl aus.</span><span class="sxs-lookup"><span data-stu-id="5a63e-115">Once downloaded, open the solution file `EFGettingStarted.sln` in Visual Studio or Visual Studio for Mac and run the application on the platform of your choice.</span></span>

<span data-ttu-id="5a63e-116">Beim ersten Start der App wird die lokale SQLite-Datenbank mit zwei Einträgen aufgefüllt, die Blogs darstellen.</span><span class="sxs-lookup"><span data-stu-id="5a63e-116">When the app first starts, it will populate the local SQLite database with two entries representing blogs.</span></span>

![Screenshot: Seite mit der Liste aller Blogs](_static/xamarin-tutorial-1.png)

<span data-ttu-id="5a63e-118">Klicken Sie in der Symbolleiste auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="5a63e-118">Click the **Add** button in the toolbar.</span></span>

<span data-ttu-id="5a63e-119">Daraufhin wird eine neue Seite angezeigt, auf der Sie Informationen über einen neuen Blog eingeben können.</span><span class="sxs-lookup"><span data-stu-id="5a63e-119">A new page will appear that allows you to enter information about a new blog.</span></span>

![Screenshot: Seite für neuen Blogeintrag](_static/xamarin-tutorial-2.png)

<span data-ttu-id="5a63e-121">Geben Sie alle Informationen ein, und klicken Sie in der Symbolleiste auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="5a63e-121">Fill out all the info and click **Save** from the toolbar.</span></span> <span data-ttu-id="5a63e-122">Der neue Blog wird in der SQLite-Datenbank der App gespeichert und in der Liste angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5a63e-122">The new blog will save to the app's SQLite database and will show in the list.</span></span>

<span data-ttu-id="5a63e-123">Sie können auf einen der Blogeinträge in der Liste klicken, um alle Beiträge für diesen Blog anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="5a63e-123">You can click on one of the blog entries in the list and see any posts for that blog.</span></span>

![Screenshot: Seite mit der Liste der Blogbeiträge](_static/xamarin-tutorial-3.png)

<span data-ttu-id="5a63e-125">Klicken Sie auf der Symbolleiste auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="5a63e-125">Click **Add** in the toolbar.</span></span>

<span data-ttu-id="5a63e-126">Dann wird eine Seite angezeigt, auf der Sie Informationen über einen neuen Blogbeitrag eingeben können.</span><span class="sxs-lookup"><span data-stu-id="5a63e-126">A page then appears that allows you to fill out information about a new blog post.</span></span>

![Screenshot: Seite zum Hinzufügen eines neuen Blogbeitrags](_static/xamarin-tutorial-4.png)

<span data-ttu-id="5a63e-128">Geben Sie alle Informationen ein, und klicken Sie in der Symbolleiste auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="5a63e-128">Fill out all the information and click **Save** in the toolbar.</span></span>

<span data-ttu-id="5a63e-129">Der neue Beitrag wird dem Blogbeitrag zugeordnet, auf den Sie im vorherigen Schritt geklickt haben, und dann in der SQLite-Datenbank der App gespeichert und in der Liste angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5a63e-129">The new post will be associated to the blog post you clicked on in a previous step and will save to the app's SQLite database and show in the list.</span></span>

<span data-ttu-id="5a63e-130">Wechseln Sie zurück zur Seite mit der Liste der Blogs.</span><span class="sxs-lookup"><span data-stu-id="5a63e-130">Go back to the blog list page.</span></span> <span data-ttu-id="5a63e-131">Klicken Sie dann auf der Symbolleiste auf **Alle löschen**.</span><span class="sxs-lookup"><span data-stu-id="5a63e-131">And click **Delete All** in the toolbar.</span></span> <span data-ttu-id="5a63e-132">Daraufhin werden alle Blogs und ihre zugehörigen Beiträge aus der SQLite-Datenbank der App gelöscht.</span><span class="sxs-lookup"><span data-stu-id="5a63e-132">All blogs and their corresponding posts will then be deleted from the app's SQLite database.</span></span>

![Screenshot der App mit allen Blogs gelöscht](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a><span data-ttu-id="5a63e-134">Untersuchen des Codes</span><span class="sxs-lookup"><span data-stu-id="5a63e-134">Explore the code</span></span>

<span data-ttu-id="5a63e-135">In den folgenden Abschnitten durchlaufen Sie den Code im Beispielprojekt, der Daten aus einer SQLite-Datenbank mithilfe von EF Core mit Xamarin.Forms liest, erstellt, aktualisiert und löscht.</span><span class="sxs-lookup"><span data-stu-id="5a63e-135">The following sections will walk you through the code in the sample project that reads, creates, updates, and deletes data from a SQLite database using EF Core with Xamarin.Forms.</span></span>

<span data-ttu-id="5a63e-136">Dabei wird davon ausgegangen, dass Sie mit den Xamarin.Forms-Themen zum [Anzeigen von Daten](/xamarin/xamarin-forms/app-fundamentals/data-binding/) und zum [Navigieren zwischen Seiten](/xamarin/xamarin-forms/app-fundamentals/navigation/) vertraut sind.</span><span class="sxs-lookup"><span data-stu-id="5a63e-136">It is assumed that you are familiar with the Xamarin.Forms topics of [displaying data](/xamarin/xamarin-forms/app-fundamentals/data-binding/) and [navigating between pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span></span>

## <a name="entity-framework-core-nuget-packages"></a><span data-ttu-id="5a63e-137">NuGet-Pakete für Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5a63e-137">Entity Framework Core NuGet packages</span></span>

<span data-ttu-id="5a63e-138">Zum Erstellen von Xamarin.Forms-Apps mit EF Core installieren Sie das Paket für den EF Core-Datenbankanbieter, den Sie als Ziel für alle Projekte in der Xamarin.Forms-Projektmappe verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="5a63e-138">To create Xamarin.Forms apps with EF Core, you install the package for the EF Core database provider(s) you want to target into all of the projects in the Xamarin.Forms solution.</span></span> <span data-ttu-id="5a63e-139">In diesem Tutorial wird der SQLite-Anbieter verwendet.</span><span class="sxs-lookup"><span data-stu-id="5a63e-139">This tutorial uses the SQLite provider.</span></span>

<span data-ttu-id="5a63e-140">Das folgende NuGet-Paket ist für alle Projekte in der Xamarin.Forms-Projektmappe erforderlich.</span><span class="sxs-lookup"><span data-stu-id="5a63e-140">The following NuGet package is needed in each of the projects in the Xamarin.Forms solution.</span></span>

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a><span data-ttu-id="5a63e-141">Modellklassen</span><span class="sxs-lookup"><span data-stu-id="5a63e-141">Model classes</span></span>

<span data-ttu-id="5a63e-142">Alle Tabellen in der SQLite-Datenbank, auf die über EF Core zugegriffen wird, werden in einer Klasse modelliert.</span><span class="sxs-lookup"><span data-stu-id="5a63e-142">Each table in the SQLite database accessed through EF Core is modeled in a class.</span></span> <span data-ttu-id="5a63e-143">In diesem Beispiel werden zwei Klassen verwendet: `Blog` und `Post`, die sich im Ordner `Models` befinden.</span><span class="sxs-lookup"><span data-stu-id="5a63e-143">In this sample, two classes are used: `Blog` and `Post` which can be found in the `Models` folder.</span></span>

<span data-ttu-id="5a63e-144">Die Modellklassen bestehen lediglich aus Eigenschaften, die Spalten in der Datenbank modellieren.</span><span class="sxs-lookup"><span data-stu-id="5a63e-144">The model classes are composed only of properties, which model columns in the database.</span></span>

* <span data-ttu-id="5a63e-145">**Blog.cs**</span><span class="sxs-lookup"><span data-stu-id="5a63e-145">**Blog.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* <span data-ttu-id="5a63e-146">Die Eigenschaft `Posts` definiert eine Über-/Unterordnungsbeziehung zwischen `Blog` und `Post`.</span><span class="sxs-lookup"><span data-stu-id="5a63e-146">The `Posts` property defines a parent-child relationship between `Blog` and `Post`.</span></span>

* <span data-ttu-id="5a63e-147">**Post.cs**</span><span class="sxs-lookup"><span data-stu-id="5a63e-147">**Post.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* <span data-ttu-id="5a63e-148">Die Eigenschaften `BlogId` und `Blog` beziehen sich auf das übergeordnete `Blog`-Objekt für die `Post`-Instanz.</span><span class="sxs-lookup"><span data-stu-id="5a63e-148">The `BlogId` and `Blog` properties relate back to the parent `Blog` object for the instance of the `Post`.</span></span>

## <a name="data-context"></a><span data-ttu-id="5a63e-149">Datenkontext</span><span class="sxs-lookup"><span data-stu-id="5a63e-149">Data context</span></span>

<span data-ttu-id="5a63e-150">Die `BloggingContext`-Klasse befindet sich im Ordner `Services` und erbt von der EF Core-Klasse `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="5a63e-150">The `BloggingContext` class is located in the `Services` folder and inherits from the EF Core `DbContext` class.</span></span> <span data-ttu-id="5a63e-151">Eine `DbContext`-Klasse wird zum Gruppieren von Datenbankabfragen und -änderungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="5a63e-151">A `DbContext` is used to group together database queries and changes.</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* <span data-ttu-id="5a63e-152">Beide Eigenschaften in dieser Klasse vom Typ `DbSet` werden verwendet, um die zugrunde liegenden Tabellen zu betreiben, die Blogs und Beiträge darstellen.</span><span class="sxs-lookup"><span data-stu-id="5a63e-152">Both properties in this class of type `DbSet` are used to operate on the underlying tables representing Blogs and Posts.</span></span>
* <span data-ttu-id="5a63e-153">`SQLitePCL.Batteries_V2.Init()` ist im Konstruktor erforderlich, um SQLite unter iOS zu initiieren.</span><span class="sxs-lookup"><span data-stu-id="5a63e-153">The `SQLitePCL.Batteries_V2.Init()` is needed in the constructor to initiate SQLite on iOS.</span></span>
* <span data-ttu-id="5a63e-154">Die `OnConfiguring`-Funktion richtet den Speicherort der SQLite-Datenbank auf dem physischen Gerät ein.</span><span class="sxs-lookup"><span data-stu-id="5a63e-154">The `OnConfiguring` function sets up the location of the SQLite database on the physical device.</span></span>

## <a name="create-read-update--delete"></a><span data-ttu-id="5a63e-155">Erstellen, Lesen, Aktualisieren und Löschen</span><span class="sxs-lookup"><span data-stu-id="5a63e-155">Create, read, update & delete</span></span>

<span data-ttu-id="5a63e-156">Im Folgenden werden einige Instanzen in der App aufgeführt, in der EF Core für den Zugriff auf SQLite verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5a63e-156">The following are some instances in the app where EF Core is used to access SQLite.</span></span>

### <a name="read"></a><span data-ttu-id="5a63e-157">Lesen</span><span class="sxs-lookup"><span data-stu-id="5a63e-157">Read</span></span>

* <span data-ttu-id="5a63e-158">Zurückgeben aller Datensätze</span><span class="sxs-lookup"><span data-stu-id="5a63e-158">Return all records.</span></span>
  * <span data-ttu-id="5a63e-159">Die `OnAppearing`-Funktion von `BlogsPage.xaml.cs` gibt alle `Blog`-Datensätze zurück und speichert diese in einer `List`-Variable.</span><span class="sxs-lookup"><span data-stu-id="5a63e-159">The `OnAppearing` function of `BlogsPage.xaml.cs` returns all `Blog` records and stores them into a `List` variable.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* <span data-ttu-id="5a63e-160">Zurückgeben spezifischer Datensätze</span><span class="sxs-lookup"><span data-stu-id="5a63e-160">Return specific records.</span></span>
  * <span data-ttu-id="5a63e-161">Die `OnAppearing`-Funktion von `PostsPage.xaml.cs` gibt `Post`-Datensätze zurück, die einen spezifischen `BlogId`-Wert enthalten.</span><span class="sxs-lookup"><span data-stu-id="5a63e-161">The `OnAppearing` function of `PostsPage.xaml.cs` returns `Post` records that contain a specific `BlogId`.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a><span data-ttu-id="5a63e-162">Erstellen</span><span class="sxs-lookup"><span data-stu-id="5a63e-162">Create</span></span>

* <span data-ttu-id="5a63e-163">Einfügen eines neuen Datensatzes</span><span class="sxs-lookup"><span data-stu-id="5a63e-163">Insert a new record.</span></span>
  * <span data-ttu-id="5a63e-164">Die `Save_Clicked`-Funktion von `AddBlogPage.xaml.cs` fügt ein neues `Blog`-Objekt in die SQLite-Datenbank ein.</span><span class="sxs-lookup"><span data-stu-id="5a63e-164">The `Save_Clicked` function of `AddBlogPage.xaml.cs` inserts a new `Blog` object into the SQLite database.</span></span>

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a><span data-ttu-id="5a63e-165">Update</span><span class="sxs-lookup"><span data-stu-id="5a63e-165">Update</span></span>

* <span data-ttu-id="5a63e-166">Aktualisieren eines vorhandenen Datensatzes</span><span class="sxs-lookup"><span data-stu-id="5a63e-166">Update an existing record.</span></span>
  * <span data-ttu-id="5a63e-167">Die `Save_Clicked`-Funktion von `AddPostPage.xaml.cs` aktualisiert ein vorhandenes `Blog`-Objekt mit einem neuen `Post`.</span><span class="sxs-lookup"><span data-stu-id="5a63e-167">The `Save_Clicked` function of `AddPostPage.xaml.cs` updates an existing `Blog` object with a new `Post`.</span></span>
  
```csharp
var newPost = new Post
{
    BlogId = BlogId,
    Content = postCell.Text,
    Title = titleCell.Text
};

using (var blogContext = new BloggingContext())
{
    var blog = await blogContext
        .Blogs
        .FirstAsync(b => b.BlogId == BlogId);

    blog.Posts.Add(newPost);

    await blogContext.SaveChangesAsync();
}
```

### <a name="delete"></a><span data-ttu-id="5a63e-168">Löschen</span><span class="sxs-lookup"><span data-stu-id="5a63e-168">Delete</span></span>

* <span data-ttu-id="5a63e-169">Löschen aller Datensätze mit einer Weitergabe an untergeordnete Datensätze</span><span class="sxs-lookup"><span data-stu-id="5a63e-169">Delete all records with cascade to child records.</span></span>
  * <span data-ttu-id="5a63e-170">Die `DeleteAll_Clicked`-Funktion von `BlogsPage.xaml.cs` löscht alle `Blog`-Datensätze in der SQLite-Datenbank und gibt die Löschvorgänge an alle untergeordneten `Post`-Datensätze von `Blog` weiter.</span><span class="sxs-lookup"><span data-stu-id="5a63e-170">The `DeleteAll_Clicked` function of `BlogsPage.xaml.cs` deletes all the `Blog` records in the SQLite database and cascades the deletes to all of the `Blog` child `Post` records.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a><span data-ttu-id="5a63e-171">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="5a63e-171">Next steps</span></span>

<span data-ttu-id="5a63e-172">In diesen ersten Schritten haben Sie gelernt, wie Sie eine Xamarin.Forms-Anwendung für den Zugriff auf eine SQLite-Datenbank mithilfe von Entity Framework Core verwenden.</span><span class="sxs-lookup"><span data-stu-id="5a63e-172">In this getting started you have learned how to use a Xamarin.Forms application to access a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="5a63e-173">Im Folgenden finden Sie weitere Entity Framework Core-Themen, die für Xamarin-Entwickler interessant sind:</span><span class="sxs-lookup"><span data-stu-id="5a63e-173">Other Entity Framework Core topics of interest to Xamarin developers:</span></span>

* [<span data-ttu-id="5a63e-174">Konfigurieren einer `DbContext`-Klasse</span><span class="sxs-lookup"><span data-stu-id="5a63e-174">Configuring a `DbContext`</span></span>](xref:core/miscellaneous/configuring-dbcontext)
* <span data-ttu-id="5a63e-175">Weitere Informationen zu [LINQ-Abfrageausdrücken](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="5a63e-175">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="5a63e-176">[Konfigurieren Sie das Modell](xref:core/modeling/index), um Aspekte wie [required](xref:core/modeling/entity-properties#required-and-optional-properties) (erforderlich) und [maximum length](xref:core/modeling/entity-properties#maximum-length) (maximale Länge) anzugeben.</span><span class="sxs-lookup"><span data-stu-id="5a63e-176">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
