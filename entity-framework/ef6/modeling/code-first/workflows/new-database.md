---
title: Code First einer neuen Datenbank EF6
description: Code First einer neuen Datenbank in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 2df6cb0a-7d8b-4e28-9d05-e2b9a90125af
uid: ef6/modeling/code-first/workflows/new-database
ms.openlocfilehash: 4e927bab07ae7a9719c46655e10685b59d0b65dc
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616892"
---
# <a name="code-first-to-a-new-database"></a><span data-ttu-id="b4eb8-103">Code First to a New Database (Code First für eine neue Datenbank)</span><span class="sxs-lookup"><span data-stu-id="b4eb8-103">Code First to a New Database</span></span>
<span data-ttu-id="b4eb8-104">Dieses Video und die schrittweise exemplarische Vorgehensweise bieten eine Einführung in Code First Entwicklung für eine neue Datenbank.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-104">This video and step-by-step walkthrough provide an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="b4eb8-105">Dieses Szenario umfasst das Ziel einer Datenbank, die nicht vorhanden ist und Code First erstellt wird, oder eine leere Datenbank, der Code First neue Tabellen hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-105">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="b4eb8-106">Mit Code First können Sie das Modell mithilfe von C- \# oder VB.NET-Klassen definieren.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-106">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="b4eb8-107">Die zusätzliche Konfiguration kann optional mithilfe von Attributen in ihren Klassen und Eigenschaften oder mithilfe einer fließenden API ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-107">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="b4eb8-108">Video ansehen</span><span class="sxs-lookup"><span data-stu-id="b4eb8-108">Watch the video</span></span>
<span data-ttu-id="b4eb8-109">Dieses Video bietet eine Einführung in Code First Entwicklung für eine neue Datenbank.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-109">This video provides an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="b4eb8-110">Dieses Szenario umfasst das Ziel einer Datenbank, die nicht vorhanden ist und Code First erstellt wird, oder eine leere Datenbank, der Code First neue Tabellen hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-110">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="b4eb8-111">Code First ermöglicht es Ihnen, das Modell mit c#-oder VB.NET-Klassen zu definieren.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-111">Code First allows you to define your model using C# or VB.Net classes.</span></span> <span data-ttu-id="b4eb8-112">Die zusätzliche Konfiguration kann optional mithilfe von Attributen in ihren Klassen und Eigenschaften oder mithilfe einer fließenden API ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-112">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

<span data-ttu-id="b4eb8-113">**Präsentation:**[Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="b4eb8-113">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="b4eb8-114">**Video**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv)  |  [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span><span class="sxs-lookup"><span data-stu-id="b4eb8-114">**Video**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="b4eb8-115">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="b4eb8-115">Pre-Requisites</span></span>

<span data-ttu-id="b4eb8-116">Sie müssen mindestens Visual Studio 2010 oder Visual Studio 2012 installiert haben, um diese exemplarische Vorgehensweise abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-116">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="b4eb8-117">Wenn Sie Visual Studio 2010 verwenden, müssen Sie auch [nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installieren.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-117">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="b4eb8-118">1. Erstellen Sie die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-118">1. Create the Application</span></span>

<span data-ttu-id="b4eb8-119">Um die Dinge einfach zu halten, erstellen wir eine einfache Konsolenanwendung, die Code First verwendet, um Daten Zugriffe auszuführen.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-119">To keep things simple we’re going to build a basic console application that uses Code First to perform data access.</span></span>

-   <span data-ttu-id="b4eb8-120">Öffnen Sie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-120">Open Visual Studio</span></span>
-   <span data-ttu-id="b4eb8-121">**Datei- &gt; New- &gt; Project...**</span><span class="sxs-lookup"><span data-stu-id="b4eb8-121">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="b4eb8-122">Wählen Sie im Menü auf der linken Seite und **Konsolenanwendung** **Windows** aus.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-122">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="b4eb8-123">Geben Sie **codefirstnewdatabasesample** als Name ein.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-123">Enter **CodeFirstNewDatabaseSample** as the name</span></span>
-   <span data-ttu-id="b4eb8-124">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-124">Select **OK**</span></span>

## <a name="2-create-the-model"></a><span data-ttu-id="b4eb8-125">2. Erstellen des Modells</span><span class="sxs-lookup"><span data-stu-id="b4eb8-125">2. Create the Model</span></span>

<span data-ttu-id="b4eb8-126">Definieren wir ein sehr einfaches Modell mithilfe von Klassen.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-126">Let’s define a very simple model using classes.</span></span> <span data-ttu-id="b4eb8-127">Wir definieren Sie lediglich in der Program.cs-Datei, aber in einer realen Anwendung würden Sie Ihre Klassen in separate Dateien und potenziell ein separates Projekt aufteilen.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-127">We’re just defining them in the Program.cs file but in a real world application you would split your classes out into separate files and potentially a separate project.</span></span>

<span data-ttu-id="b4eb8-128">Fügen Sie unter der Programm Klassendefinition in Program.cs die beiden folgenden Klassen hinzu.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-128">Below the Program class definition in Program.cs add the following two classes.</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; }
    public virtual Blog Blog { get; set; }
}
```

<span data-ttu-id="b4eb8-129">Sie werden feststellen, dass die beiden Navigations Eigenschaften (Blog. Posts und Post. Blog) virtuell sind.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-129">You’ll notice that we’re making the two navigation properties (Blog.Posts and Post.Blog) virtual.</span></span> <span data-ttu-id="b4eb8-130">Dies ermöglicht die Lazy Load-Funktion von Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-130">This enables the Lazy Loading feature of Entity Framework.</span></span> <span data-ttu-id="b4eb8-131">Lazy Load bedeutet, dass der Inhalt dieser Eigenschaften automatisch aus der Datenbank geladen wird, wenn Sie versuchen, darauf zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-131">Lazy Loading means that the contents of these properties will be automatically loaded from the database when you try to access them.</span></span>

## <a name="3-create-a-context"></a><span data-ttu-id="b4eb8-132">3. Erstellen eines Kontexts</span><span class="sxs-lookup"><span data-stu-id="b4eb8-132">3. Create a Context</span></span>

<span data-ttu-id="b4eb8-133">Nun ist es an der Zeit, einen abgeleiteten Kontext zu definieren, der eine Sitzung mit der Datenbank darstellt, sodass wir Daten Abfragen und speichern können.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-133">Now it’s time to define a derived context, which represents a session with the database, allowing us to query and save data.</span></span> <span data-ttu-id="b4eb8-134">Wir definieren einen Kontext, der von System. Data. Entity. dbcontext abgeleitet ist und &lt; &gt; für jede Klasse in unserem Modell eine typisierte dbset TEntity verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-134">We define a context that derives from System.Data.Entity.DbContext and exposes a typed DbSet&lt;TEntity&gt; for each class in our model.</span></span>

<span data-ttu-id="b4eb8-135">Wir beginnen jetzt mit der Verwendung von Typen aus der Entity Framework daher müssen wir das nuget-Paket "EntityFramework" hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-135">We’re now starting to use types from the Entity Framework so we need to add the EntityFramework NuGet package.</span></span>

-   <span data-ttu-id="b4eb8-136">**Project – &gt; nuget-Pakete verwalten...**</span><span class="sxs-lookup"><span data-stu-id="b4eb8-136">**Project –&gt; Manage NuGet Packages…**</span></span>
    <span data-ttu-id="b4eb8-137">Hinweis: Wenn Sie nicht über die **nuget-Pakete verwalten verfügen...**</span><span class="sxs-lookup"><span data-stu-id="b4eb8-137">Note: If you don’t have the **Manage NuGet Packages…**</span></span> <span data-ttu-id="b4eb8-138">Option Sie sollten die [neueste Version von nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installieren.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-138">option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span></span>
-   <span data-ttu-id="b4eb8-139">Auswählen der Registerkarte " **Online** "</span><span class="sxs-lookup"><span data-stu-id="b4eb8-139">Select the **Online** tab</span></span>
-   <span data-ttu-id="b4eb8-140">Auswählen des " **EntityFramework** "-Pakets</span><span class="sxs-lookup"><span data-stu-id="b4eb8-140">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="b4eb8-141">Klicken Sie auf **Install** (Installieren).</span><span class="sxs-lookup"><span data-stu-id="b4eb8-141">Click **Install**</span></span>

<span data-ttu-id="b4eb8-142">Fügen Sie am Anfang von Program.cs eine using-Anweisung für System. Data. Entity hinzu.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-142">Add a using statement for System.Data.Entity at the top of Program.cs.</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="b4eb8-143">Fügen Sie unter der Post-Klasse in Program.cs den folgenden abgeleiteten Kontext hinzu.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-143">Below the Post class in Program.cs add the following derived context.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

<span data-ttu-id="b4eb8-144">Im folgenden finden Sie eine komplette Liste der Komponenten, die Program.cs jetzt enthalten sollte.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-144">Here is a complete listing of what Program.cs should now contain.</span></span>

``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Entity;

namespace CodeFirstNewDatabaseSample
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }

    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }
}
```

<span data-ttu-id="b4eb8-145">Dies ist der gesamte Code, der zum Speichern und Abrufen von Daten erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-145">That is all the code we need to start storing and retrieving data.</span></span> <span data-ttu-id="b4eb8-146">Natürlich gibt es im Hintergrund etwas, das wir in einem Moment sehen werden, aber zuerst sehen wir uns dies in Aktion an.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-146">Obviously there is quite a bit going on behind the scenes and we’ll take a look at that in a moment but first let’s see it in action.</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="b4eb8-147">4. lesen & schreiben von Daten</span><span class="sxs-lookup"><span data-stu-id="b4eb8-147">4. Reading & Writing Data</span></span>

<span data-ttu-id="b4eb8-148">Implementieren Sie die Main-Methode in Program.cs, wie unten gezeigt.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-148">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="b4eb8-149">Mit diesem Code wird eine neue Instanz des Kontexts erstellt und anschließend verwendet, um einen neuen Blog einzufügen.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-149">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="b4eb8-150">Anschließend wird eine LINQ-Abfrage verwendet, um alle Blogs aus der Datenbank alphabetisch nach Titel abzurufen.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-150">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

<span data-ttu-id="b4eb8-151">Sie können die Anwendung jetzt ausführen und testen.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-151">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a><span data-ttu-id="b4eb8-152">Wo sind meine Daten?</span><span class="sxs-lookup"><span data-stu-id="b4eb8-152">Where’s My Data?</span></span>

<span data-ttu-id="b4eb8-153">Gemäß der Konvention hat dbcontext eine Datenbank für Sie erstellt.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-153">By convention DbContext has created a database for you.</span></span>

-   <span data-ttu-id="b4eb8-154">Wenn eine lokale SQL Express-Instanz verfügbar ist (standardmäßig mit Visual Studio 2010 installiert), hat Code First die Datenbank auf dieser Instanz erstellt.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-154">If a local SQL Express instance is available (installed by default with Visual Studio 2010) then Code First has created the database on that instance</span></span>
-   <span data-ttu-id="b4eb8-155">Wenn SQL Express nicht verfügbar ist, werden Code First versuchen, [localdb](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (standardmäßig mit Visual Studio 2012 installiert) zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-155">If SQL Express isn’t available then Code First will try and use [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (installed by default with Visual Studio 2012)</span></span>
-   <span data-ttu-id="b4eb8-156">Die Datenbank wird nach dem voll qualifizierten Namen des abgeleiteten Kontexts benannt, in unserem Fall **codefirstnewdatabasesample. bloggingcontext.**</span><span class="sxs-lookup"><span data-stu-id="b4eb8-156">The database is named after the fully qualified name of the derived context, in our case that is **CodeFirstNewDatabaseSample.BloggingContext**</span></span>

<span data-ttu-id="b4eb8-157">Dabei handelt es sich nur um die Standard Konventionen, und es gibt verschiedene Möglichkeiten, die Datenbank zu ändern, die Code First verwendet. Weitere Informationen finden Sie im Thema **wie dbcontext das Modell und die Datenbankverbindung** erkennt.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-157">These are just the default conventions and there are various ways to change the database that Code First uses, more information is available in the **How DbContext Discovers the Model and Database Connection** topic.</span></span>
<span data-ttu-id="b4eb8-158">Sie können mit Server-Explorer in Visual Studio eine Verbindung mit dieser Datenbank herstellen.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-158">You can connect to this database using Server Explorer in Visual Studio</span></span>

-   <span data-ttu-id="b4eb8-159">**Ansicht- &gt; Server-Explorer**</span><span class="sxs-lookup"><span data-stu-id="b4eb8-159">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="b4eb8-160">Klicken Sie mit der rechten Maustaste auf **Datenverbindungen** , und wählen Sie **Verbindung hinzufügen aus.**</span><span class="sxs-lookup"><span data-stu-id="b4eb8-160">Right click on **Data Connections** and select **Add Connection…**</span></span>
-   <span data-ttu-id="b4eb8-161">Wenn Sie über Server-Explorer keine Verbindung mit einer Datenbank hergestellt haben, müssen Sie Microsoft SQL Server als Datenquelle auswählen.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-161">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![Datenquelle auswählen](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="b4eb8-163">Stellen Sie eine Verbindung mit localdb oder SQL Express her, je nachdem, welche installiert wurde.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-163">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>

<span data-ttu-id="b4eb8-164">Wir können nun das Schema überprüfen, das von Code First erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-164">We can now inspect the schema that Code First created.</span></span>

![Schema anfänglich](~/ef6/media/schemainitial.png)

<span data-ttu-id="b4eb8-166">Dbcontext hat festgelegt, welche Klassen in das Modell aufgenommen werden sollen, indem wir die von uns definierten dbset-Eigenschaften betrachten.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-166">DbContext worked out what classes to include in the model by looking at the DbSet properties that we defined.</span></span> <span data-ttu-id="b4eb8-167">Anschließend wird der Standardsatz von Code First Konventionen verwendet, um Tabellen-und Spaltennamen zu bestimmen, Datentypen zu ermitteln, Primärschlüssel zu suchen usw.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-167">It then uses the default set of Code First conventions to determine table and column names, determine data types, find primary keys, etc.</span></span> <span data-ttu-id="b4eb8-168">Später in dieser exemplarischen Vorgehensweise wird erläutert, wie Sie diese Konventionen überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-168">Later in this walkthrough we’ll look at how you can override these conventions.</span></span>

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="b4eb8-169">5. Umgang mit Modelländerungen</span><span class="sxs-lookup"><span data-stu-id="b4eb8-169">5. Dealing with Model Changes</span></span>

<span data-ttu-id="b4eb8-170">Nun ist es an der Zeit, einige Änderungen an unserem Modell vorzunehmen, wenn wir diese Änderungen vornehmen, müssen wir auch das Datenbankschema aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-170">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span> <span data-ttu-id="b4eb8-171">Zu diesem Zweck wird eine Funktion namens "Code First-Migrationen" oder "Migrationen" verwendet.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-171">To do this we are going to use a feature called Code First Migrations, or Migrations for short.</span></span>

<span data-ttu-id="b4eb8-172">Migrationen ermöglichen es uns, einen geordneten Satz von Schritten zu haben, die beschreiben, wie das Datenbankschema aktualisiert (und herabgestuft) werden kann.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-172">Migrations allows us to have an ordered set of steps that describe how to upgrade (and downgrade) our database schema.</span></span> <span data-ttu-id="b4eb8-173">Jeder dieser Schritte, die als Migration bezeichnet wird, enthält Code, der die anzuwendenden Änderungen beschreibt.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-173">Each of these steps, known as a migration, contains some code that describes the changes to be applied.</span></span> 

<span data-ttu-id="b4eb8-174">Der erste Schritt besteht darin, Code First-Migrationen für den bloggingcontext zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-174">The first step is to enable Code First Migrations for our BloggingContext.</span></span>

-   <span data-ttu-id="b4eb8-175">**Tools- &gt; Bibliothekspaket-Manager- &gt; Paket-Manager-Konsole**</span><span class="sxs-lookup"><span data-stu-id="b4eb8-175">**Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
-   <span data-ttu-id="b4eb8-176">Führen Sie in der Paket-Manager-Konsole den Befehl **Enable-Migrations** aus.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-176">Run the **Enable-Migrations** command in Package Manager Console</span></span>
-   <span data-ttu-id="b4eb8-177">Dem Projekt wurde ein neuer Migrations Ordner hinzugefügt, der zwei Elemente enthält:</span><span class="sxs-lookup"><span data-stu-id="b4eb8-177">A new Migrations folder has been added to our project that contains two items:</span></span>
    -   <span data-ttu-id="b4eb8-178">**Configuration.cs** – diese Datei enthält die Einstellungen, die Migrationen zum Migrieren von bloggingcontext verwenden.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-178">**Configuration.cs** – This file contains the settings that Migrations will use for migrating BloggingContext.</span></span> <span data-ttu-id="b4eb8-179">Wir müssen nichts für diese exemplarische Vorgehensweise ändern, aber hier können Sie Seed-Daten angeben, Anbieter für andere Datenbanken registrieren, den Namespace ändern, in dem Migrationen generiert werden usw.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-179">We don’t need to change anything for this walkthrough, but here is where you can specify seed data, register providers for other databases, changes the namespace that migrations are generated in etc.</span></span>
    -   <span data-ttu-id="b4eb8-180">\*\* &lt; Zeitstempel &gt; \_ InitialCreate.cs\*\* – Dies ist die erste Migration. Sie stellt die Änderungen dar, die bereits auf die Datenbank angewendet wurden, damit Sie aus einer leeren Datenbank zu einer Datenbank mit den Tabellen Blogs und Posts geführt werden.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-180">**&lt;timestamp&gt;\_InitialCreate.cs** – This is your first migration, it represents the changes that have already been applied to the database to take it from being an empty database to one that includes the Blogs and Posts tables.</span></span> <span data-ttu-id="b4eb8-181">Obwohl wir Code First diese Tabellen für uns automatisch erstellen lassen, haben wir nun die Migration in eine Migration durchführen.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-181">Although we let Code First automatically create these tables for us, now that we have opted in to Migrations they have been converted into a Migration.</span></span> <span data-ttu-id="b4eb8-182">Code First wurde auch in unserer lokalen Datenbank aufgezeichnet, dass diese Migration bereits angewendet wurde.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-182">Code First has also recorded in our local database that this Migration has already been applied.</span></span> <span data-ttu-id="b4eb8-183">Der Zeitstempel des Datei namens wird zu bestellzwecken verwendet.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-183">The timestamp on the filename is used for ordering purposes.</span></span>

    <span data-ttu-id="b4eb8-184">Nehmen wir nun eine Änderung an unserem Modell vor. Fügen Sie der Blog-Klasse eine URL-Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="b4eb8-184">Now let’s make a change to our model, add a Url property to the Blog class:</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   <span data-ttu-id="b4eb8-185">Führen Sie den Befehl **Add-Migration addurl** in der Paket-Manager-Konsole aus.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-185">Run the **Add-Migration AddUrl** command in Package Manager Console.</span></span>
    <span data-ttu-id="b4eb8-186">Der Befehl "Add-Migration" prüft seit der letzten Migration auf Änderungen und richtet eine neue Migration mit gefundenen Änderungen ein.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-186">The Add-Migration command checks for changes since your last migration and scaffolds a new migration with any changes that are found.</span></span> <span data-ttu-id="b4eb8-187">Wir können Migrationen einen Namen übergeben. in diesem Fall rufen wir die Migration "addUrl" auf.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-187">We can give migrations a name; in this case we are calling the migration ‘AddUrl’.</span></span>
    <span data-ttu-id="b4eb8-188">Der Gerüst Code besagt, dass wir eine URL-Spalte, die Zeichen folgen Daten enthalten kann, zum dbo hinzufügen müssen. Blogs-Tabelle.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-188">The scaffolded code is saying that we need to add a Url column, that can hold string data, to the dbo.Blogs table.</span></span> <span data-ttu-id="b4eb8-189">Bei Bedarf könnten wir den gerüdierten Code bearbeiten, dies ist in diesem Fall jedoch nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-189">If needed, we could edit the scaffolded code but that’s not required in this case.</span></span>

``` csharp
namespace CodeFirstNewDatabaseSample.Migrations
{
    using System;
    using System.Data.Entity.Migrations;

    public partial class AddUrl : DbMigration
    {
        public override void Up()
        {
            AddColumn("dbo.Blogs", "Url", c => c.String());
        }

        public override void Down()
        {
            DropColumn("dbo.Blogs", "Url");
        }
    }
}
```

-   <span data-ttu-id="b4eb8-190">Führen Sie den Befehl **Update-Database** in der Paket-Manager-Konsole aus.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-190">Run the **Update-Database** command in Package Manager Console.</span></span> <span data-ttu-id="b4eb8-191">Mit diesem Befehl werden alle ausstehenden Migrationen auf die Datenbank angewendet.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-191">This command will apply any pending migrations to the database.</span></span> <span data-ttu-id="b4eb8-192">Unsere InitialCreate-Migration wurde bereits angewendet, sodass bei der Migration nur die neue addurl-Migration angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-192">Our InitialCreate migration has already been applied so migrations will just apply our new AddUrl migration.</span></span>
    <span data-ttu-id="b4eb8-193">Tipp: Sie können den Schalter " **– verbose** " verwenden, wenn Sie "Update-Database" aufrufen, um die SQL-Anweisung anzuzeigen, die für die Datenbank ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-193">Tip: You can use the **–Verbose** switch when calling Update-Database to see the SQL that is being executed against the database.</span></span>

<span data-ttu-id="b4eb8-194">Die neue URL-Spalte wird nun der Tabelle Blogs in der-Datenbank hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="b4eb8-194">The new Url column is now added to the Blogs table in the database:</span></span>

![Schema mit URL](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a><span data-ttu-id="b4eb8-196">6. Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="b4eb8-196">6. Data Annotations</span></span>

<span data-ttu-id="b4eb8-197">Bisher haben wir EF das Modell nur mit seinen Standard Konventionen ermitteln lassen, aber es gibt Zeiten, in denen unsere Klassen nicht den Konventionen folgen und eine weitere Konfiguration durchführen müssen.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-197">So far we’ve just let EF discover the model using its default conventions, but there are going to be times when our classes don’t follow the conventions and we need to be able to perform further configuration.</span></span> <span data-ttu-id="b4eb8-198">Hierfür gibt es zwei Optionen: Wir betrachten die Daten Anmerkungen in diesem Abschnitt und dann die fließende API im nächsten Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-198">There are two options for this; we’ll look at Data Annotations in this section and then the fluent API in the next section.</span></span>

-   <span data-ttu-id="b4eb8-199">Fügen wir unserem Modell eine Benutzerklasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-199">Let’s add a User class to our model</span></span>

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="b4eb8-200">Wir müssen dem abgeleiteten Kontext auch eine Menge hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-200">We also need to add a set to our derived context</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   <span data-ttu-id="b4eb8-201">Wenn wir versuchen, eine Migration hinzuzufügen, wird eine Fehlermeldung angezeigt, die besagt, dass für*EntityType ' User ' kein Schlüssel definiert ist. Hiermit wird der Schlüssel für diesen EntityType definiert. "*</span><span class="sxs-lookup"><span data-stu-id="b4eb8-201">If we tried to add a migration we’d get an error saying “*EntityType ‘User’ has no key defined. Define the key for this EntityType.”*</span></span> <span data-ttu-id="b4eb8-202">EF kann nicht wissen, dass der Benutzername der Primärschlüssel für den Benutzer sein sollte.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-202">because EF has no way of knowing that Username should be the primary key for User.</span></span>
-   <span data-ttu-id="b4eb8-203">Wir verwenden jetzt Daten Anmerkungen, damit wir am Anfang von Program.cs eine using-Anweisung hinzufügen müssen.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-203">We’re going to use Data Annotations now so we need to add a using statement at the top of Program.cs</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
```

-   <span data-ttu-id="b4eb8-204">Kommentieren Sie nun die UserName-Eigenschaft, um festzustellen, dass es sich um den Primärschlüssel handelt.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-204">Now annotate the Username property to identify that it is the primary key</span></span>

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="b4eb8-205">Verwenden Sie den Befehl **Add-Migration adduser** zum Gerüstbau einer Migration, um diese Änderungen auf die Datenbank anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-205">Use the **Add-Migration AddUser** command to scaffold a migration to apply these changes to the database</span></span>
-   <span data-ttu-id="b4eb8-206">Führen Sie den **Update-Database-** Befehl aus, um die neue Migration auf die Datenbank anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-206">Run the **Update-Database** command to apply the new migration to the database</span></span>

<span data-ttu-id="b4eb8-207">Die neue Tabelle wird nun der-Datenbank hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="b4eb8-207">The new table is now added to the database:</span></span>

![Schema mit Benutzern](~/ef6/media/schemawithusers.png)

<span data-ttu-id="b4eb8-209">Die vollständige Liste der von EF unterstützten Anmerkungen lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="b4eb8-209">The full list of annotations supported by EF is:</span></span>

-   [<span data-ttu-id="b4eb8-210">Keyattribute</span><span class="sxs-lookup"><span data-stu-id="b4eb8-210">KeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [<span data-ttu-id="b4eb8-211">StringLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="b4eb8-211">StringLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [<span data-ttu-id="b4eb8-212">MaxLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="b4eb8-212">MaxLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [<span data-ttu-id="b4eb8-213">"-Attribut"</span><span class="sxs-lookup"><span data-stu-id="b4eb8-213">ConcurrencyCheckAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [<span data-ttu-id="b4eb8-214">RequiredAttribute</span><span class="sxs-lookup"><span data-stu-id="b4eb8-214">RequiredAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [<span data-ttu-id="b4eb8-215">Timestampattribute</span><span class="sxs-lookup"><span data-stu-id="b4eb8-215">TimestampAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [<span data-ttu-id="b4eb8-216">Complextypeattribute</span><span class="sxs-lookup"><span data-stu-id="b4eb8-216">ComplexTypeAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [<span data-ttu-id="b4eb8-217">ColumnAttribute</span><span class="sxs-lookup"><span data-stu-id="b4eb8-217">ColumnAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [<span data-ttu-id="b4eb8-218">TableAttribute</span><span class="sxs-lookup"><span data-stu-id="b4eb8-218">TableAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [<span data-ttu-id="b4eb8-219">Invertspropertyattribute</span><span class="sxs-lookup"><span data-stu-id="b4eb8-219">InversePropertyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [<span data-ttu-id="b4eb8-220">Fremdschlüssel Attribut</span><span class="sxs-lookup"><span data-stu-id="b4eb8-220">ForeignKeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [<span data-ttu-id="b4eb8-221">Databasegeneratedattribute</span><span class="sxs-lookup"><span data-stu-id="b4eb8-221">DatabaseGeneratedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [<span data-ttu-id="b4eb8-222">Notmappeer dattribute</span><span class="sxs-lookup"><span data-stu-id="b4eb8-222">NotMappedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a><span data-ttu-id="b4eb8-223">7. fließende API</span><span class="sxs-lookup"><span data-stu-id="b4eb8-223">7. Fluent API</span></span>

<span data-ttu-id="b4eb8-224">Im vorherigen Abschnitt haben wir uns mit der Verwendung von Daten Anmerkungen beschäftigt, um zu ergänzen oder zu überschreiben, was von der Konvention erkannt wurde.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-224">In the previous section we looked at using Data Annotations to supplement or override what was detected by convention.</span></span> <span data-ttu-id="b4eb8-225">Die andere Möglichkeit, das Modell zu konfigurieren, ist über die Code First fließende API.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-225">The other way to configure the model is via the Code First fluent API.</span></span>

<span data-ttu-id="b4eb8-226">Die meisten Modell Konfigurationen können mithilfe von einfachen Daten Anmerkungen durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-226">Most model configuration can be done using simple data annotations.</span></span> <span data-ttu-id="b4eb8-227">Die fließende API ist eine fortgeschrittenere Methode zum Angeben der Modell Konfiguration, die alles abdeckt, was Daten Anmerkungen zusätzlich zu einer erweiterten Konfiguration tun können, die mit Daten Anmerkungen nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-227">The fluent API is a more advanced way of specifying model configuration that covers everything that data annotations can do in addition to some more advanced configuration not possible with data annotations.</span></span> <span data-ttu-id="b4eb8-228">Daten Anmerkungen und die fließende API können gleichzeitig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-228">Data annotations and the fluent API can be used together.</span></span>

<span data-ttu-id="b4eb8-229">Für den Zugriff auf die fließende API überschreiben Sie die onmodelcreating-Methode in dbcontext.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-229">To access the fluent API you override the OnModelCreating method in DbContext.</span></span> <span data-ttu-id="b4eb8-230">Nehmen wir an, dass wir die Spalte, in der "User. Display Name" gespeichert ist, in "Display Name" umbenennen möchten \_ .</span><span class="sxs-lookup"><span data-stu-id="b4eb8-230">Let’s say we wanted to rename the column that User.DisplayName is stored in to display\_name.</span></span>

-   <span data-ttu-id="b4eb8-231">Überschreiben Sie die onmodelcreating-Methode auf bloggingcontext mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b4eb8-231">Override the OnModelCreating method on BloggingContext with the following code</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>()
            .Property(u => u.DisplayName)
            .HasColumnName("display_name");
    }
}
```

-   <span data-ttu-id="b4eb8-232">Verwenden Sie den Befehl **Add-Migration changedisplayname** , um ein Gerüst für eine Migration zum Anwenden dieser Änderungen auf die Datenbank zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-232">Use the **Add-Migration ChangeDisplayName** command to scaffold a migration to apply these changes to the database.</span></span>
-   <span data-ttu-id="b4eb8-233">Führen Sie den **Update-Database-** Befehl aus, um die neue Migration auf die Datenbank anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-233">Run the **Update-Database** command to apply the new migration to the database.</span></span>

<span data-ttu-id="b4eb8-234">Die Spalte Display Name wurde nun in Anzeige Name umbenannt \_ :</span><span class="sxs-lookup"><span data-stu-id="b4eb8-234">The DisplayName column is now renamed to display\_name:</span></span>

![Schema mit umbenannten anzeigen Amen](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a><span data-ttu-id="b4eb8-236">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="b4eb8-236">Summary</span></span>

<span data-ttu-id="b4eb8-237">In dieser exemplarischen Vorgehensweise haben wir Code First Entwicklung mit einer neuen Datenbank untersucht.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-237">In this walkthrough we looked at Code First development using a new database.</span></span> <span data-ttu-id="b4eb8-238">Wir haben ein Modell mithilfe von Klassen definiert und dann das Modell verwendet, um eine Datenbank zu erstellen und Daten zu speichern und abzurufen.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-238">We defined a model using classes then used that model to create a database and store and retrieve data.</span></span> <span data-ttu-id="b4eb8-239">Nachdem die Datenbank erstellt wurde, haben wir Code First-Migrationen verwendet, um das Schema zu ändern, als das Modell entwickelt wurde.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-239">Once the database was created we used Code First Migrations to change the schema as our model evolved.</span></span> <span data-ttu-id="b4eb8-240">Außerdem wurde erläutert, wie ein Modell mithilfe von Daten Anmerkungen und der fließenden API konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="b4eb8-240">We also saw how to configure a model using Data Annotations and the Fluent API.</span></span>
