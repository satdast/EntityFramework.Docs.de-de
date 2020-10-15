---
title: 'Code First-Migrationen: EF6'
description: Code-First-Migrationen in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/index
ms.openlocfilehash: 7b58ed82202e5a0b7e8676020e32deaac39bcf70
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064977"
---
# <a name="code-first-migrations"></a><span data-ttu-id="d483e-103">Code First-Migrationen</span><span class="sxs-lookup"><span data-stu-id="d483e-103">Code First Migrations</span></span>
<span data-ttu-id="d483e-104">Code First-Migrationen ist die empfohlene Methode, das Datenbankschema einer Anwendung weiterzuentwickeln, wenn Sie den Code First-Workflow verwenden.</span><span class="sxs-lookup"><span data-stu-id="d483e-104">Code First Migrations is the recommended way to evolve your application's database schema if you are using the Code First workflow.</span></span> <span data-ttu-id="d483e-105">Code First-Migrationen stellt eine Reihe von Tools bereit, mit denen Sie folgende Aktionen ausführen können:</span><span class="sxs-lookup"><span data-stu-id="d483e-105">Migrations provide a set of tools that allow:</span></span>

1. <span data-ttu-id="d483e-106">Erstellen einer Ausgangsdatenbank, die mit Ihrem EF-Modell funktioniert</span><span class="sxs-lookup"><span data-stu-id="d483e-106">Create an initial database that works with your EF model</span></span>
2. <span data-ttu-id="d483e-107">Generieren von Migrationen, um Änderungen an Ihrem EF-Modell nachzuverfolgen</span><span class="sxs-lookup"><span data-stu-id="d483e-107">Generating migrations to keep track of changes you make to your EF model</span></span>
2. <span data-ttu-id="d483e-108">Aktualisieren der Datenbank mit den entsprechenden Änderungen</span><span class="sxs-lookup"><span data-stu-id="d483e-108">Keep your database up to date with those changes</span></span>

<span data-ttu-id="d483e-109">Diese exemplarische Vorgehensweise bietet eine Übersicht über Code First-Migrationen in Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="d483e-109">The following walkthrough will provide an overview of Code First Migrations in Entity Framework.</span></span> <span data-ttu-id="d483e-110">Sie können entweder die gesamte exemplarische Vorgehensweise ausführen oder mit einem Thema fortfahren, das Sie besonders interessiert.</span><span class="sxs-lookup"><span data-stu-id="d483e-110">You can either complete the entire walkthrough or skip to the topic you are interested in.</span></span> <span data-ttu-id="d483e-111">Die folgenden Themen werden erörtert:</span><span class="sxs-lookup"><span data-stu-id="d483e-111">The following topics are covered:</span></span>

## <a name="building-an-initial-model--database"></a><span data-ttu-id="d483e-112">Erstellen eines Ausgangsmodells und einer Ausgangsdatenbank</span><span class="sxs-lookup"><span data-stu-id="d483e-112">Building an Initial Model & Database</span></span>

<span data-ttu-id="d483e-113">Bevor Sie Migrationen verwenden können, benötigen Sie ein Projekt und ein Code First-Modell.</span><span class="sxs-lookup"><span data-stu-id="d483e-113">Before we start using migrations we need a project and a Code First model to work with.</span></span> <span data-ttu-id="d483e-114">In dieser exemplarischen Vorgehensweise werden die kanonischen Modelle **Blog** und **Post** verwendet.</span><span class="sxs-lookup"><span data-stu-id="d483e-114">For this walkthrough we are going to use the canonical **Blog** and **Post** model.</span></span>

-   <span data-ttu-id="d483e-115">Erstellen Sie eine neue **MigrationsDemo**-Konsolenanwendung.</span><span class="sxs-lookup"><span data-stu-id="d483e-115">Create a new **MigrationsDemo** Console application</span></span>
-   <span data-ttu-id="d483e-116">Fügen Sie dem Projekt die neueste Version des NuGet-Pakets **EntityFramework** hinzu.</span><span class="sxs-lookup"><span data-stu-id="d483e-116">Add the latest version of the **EntityFramework** NuGet package to the project</span></span>
    -   <span data-ttu-id="d483e-117">Wählen Sie **Tools > Bibliotheks-Paket-Manger > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="d483e-117">**Tools –&gt; Library Package Manager –&gt; Package Manager Console**</span></span>
    -   <span data-ttu-id="d483e-118">Führen Sie den Befehl **Install-Package EntityFramework** aus.</span><span class="sxs-lookup"><span data-stu-id="d483e-118">Run the **Install-Package EntityFramework** command</span></span>
-   <span data-ttu-id="d483e-119">Fügen Sie eine **Model.cs**-Datei mit dem unten aufgeführten Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="d483e-119">Add a **Model.cs** file with the code shown below.</span></span> <span data-ttu-id="d483e-120">Mit diesem Code wird eine einzelne **Blog**-Klasse, die das Domänenmodell bildet, und eine **BlogContext**-Klasse, die den EF Code First-Kontext darstellt, definiert.</span><span class="sxs-lookup"><span data-stu-id="d483e-120">This code defines a single **Blog** class that makes up our domain model and a **BlogContext** class that is our EF Code First context</span></span>

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsDemo
      {
          public class BlogContext : DbContext
          {
              public DbSet<Blog> Blogs { get; set; }
          }

          public class Blog
          {
              public int BlogId { get; set; }
              public string Name { get; set; }
          }
      }
  ```

-   <span data-ttu-id="d483e-121">Mit dem Modell können Sie nun auf die Daten zugreifen.</span><span class="sxs-lookup"><span data-stu-id="d483e-121">Now that we have a model it’s time to use it to perform data access.</span></span> <span data-ttu-id="d483e-122">Aktualisieren Sie die **Program.cs**-Datei mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="d483e-122">Update the **Program.cs** file with the code shown below.</span></span>

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsDemo
      {
          class Program
          {
              static void Main(string[] args)
              {
                  using (var db = new BlogContext())
                  {
                      db.Blogs.Add(new Blog { Name = "Another Blog " });
                      db.SaveChanges();

                      foreach (var blog in db.Blogs)
                      {
                          Console.WriteLine(blog.Name);
                      }
                  }

                  Console.WriteLine("Press any key to exit...");
                  Console.ReadKey();
              }
          }
      }
  ```

-   <span data-ttu-id="d483e-123">Wenn Sie nun die Anwendung ausführen, wird die Datenbank **MigrationsCodeDemo.BlogContext** erstellt.</span><span class="sxs-lookup"><span data-stu-id="d483e-123">Run your application and you will see that a **MigrationsCodeDemo.BlogContext** database is created for you.</span></span>

    ![Datenbank LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a><span data-ttu-id="d483e-125">Aktivieren von Migrationen</span><span class="sxs-lookup"><span data-stu-id="d483e-125">Enabling Migrations</span></span>

<span data-ttu-id="d483e-126">Im Folgenden erfahren Sie, wie Sie weitere Änderungen am Modell vornehmen.</span><span class="sxs-lookup"><span data-stu-id="d483e-126">It’s time to make some more changes to our model.</span></span>

-   <span data-ttu-id="d483e-127">Fügen Sie der Blog-Klasse eine URL-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="d483e-127">Let’s introduce a Url property to the Blog class.</span></span>

``` csharp
    public string Url { get; set; }
```

<span data-ttu-id="d483e-128">Wenn Sie zu diesem Zeitpunkt die Anwendung erneut ausführen würden, würden Sie die Ausnahme „InvalidOperationException“ mit folgendem Text erhalten: *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *) (Das Modell, das den Kontext „BlogContext“ unterstützt, wurde seit der Erstellung der Datenbank verändert. Verwenden Sie Code First-Migrationen, um die Datenbank zu aktualisieren.).*</span><span class="sxs-lookup"><span data-stu-id="d483e-128">If you were to run the application again you would get an InvalidOperationException stating *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*).*</span></span>

<span data-ttu-id="d483e-129">Beginnen Sie daher mit der Verwendung von Code First-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="d483e-129">As the exception suggests, it’s time to start using Code First Migrations.</span></span> <span data-ttu-id="d483e-130">Aktivieren Sie dafür zunächst Migrationen für den Kontext.</span><span class="sxs-lookup"><span data-stu-id="d483e-130">The first step is to enable migrations for our context.</span></span>

-   <span data-ttu-id="d483e-131">Führen Sie in der Paket-Manager-Konsole den Befehl **Enable-Migrations** aus.</span><span class="sxs-lookup"><span data-stu-id="d483e-131">Run the **Enable-Migrations** command in Package Manager Console</span></span>

    <span data-ttu-id="d483e-132">Mit diesem Befehl wird dem Projekt ein **Migrations**-Ordner hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d483e-132">This command has added a **Migrations** folder to our project.</span></span> <span data-ttu-id="d483e-133">Der neue Ordner enthält zwei Dateien:</span><span class="sxs-lookup"><span data-stu-id="d483e-133">This new folder contains two files:</span></span>

-   <span data-ttu-id="d483e-134">**Die Dateikonfigurationsklasse:**</span><span class="sxs-lookup"><span data-stu-id="d483e-134">**The Configuration class.**</span></span> <span data-ttu-id="d483e-135">Mit dieser Klasse können Sie das Verhalten von Migrationen für den Kontext konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="d483e-135">This class allows you to configure how Migrations behaves for your context.</span></span> <span data-ttu-id="d483e-136">Verwenden Sie für diese exemplarische Vorgehensweise die Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="d483e-136">For this walkthrough we will just use the default configuration.</span></span>
    <span data-ttu-id="d483e-137">*Da in Ihrem Projekt nur ein einzelner Code First-Kontext vorhanden ist, wurde der Kontexttyp, der für diese Konfiguration gilt, automatisch von „Enable-Migrations“ ausgefüllt.*</span><span class="sxs-lookup"><span data-stu-id="d483e-137">*Because there is just a single Code First context in your project, Enable-Migrations has automatically filled in the context type this configuration applies to.*</span></span>
-   <span data-ttu-id="d483e-138">**Eine InitialCreate-Migration:**</span><span class="sxs-lookup"><span data-stu-id="d483e-138">**An InitialCreate migration**.</span></span> <span data-ttu-id="d483e-139">Diese Migration wurde generiert, da Code First bereits vor dem Aktivieren von Migrationen eine Datenbank erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="d483e-139">This migration was generated because we already had Code First create a database for us, before we enabled migrations.</span></span> <span data-ttu-id="d483e-140">Der Code in diesem Entwurf für die Migration stellt die Objekte dar, die in der Datenbank bereits erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="d483e-140">The code in this scaffolded migration represents the objects that have already been created in the database.</span></span> <span data-ttu-id="d483e-141">In unserem Fall handelt es sich dabei um die **Blog**-Tabelle mit einer **BlogId** und **Namen**-Spalten.</span><span class="sxs-lookup"><span data-stu-id="d483e-141">In our case that is the **Blog** table with a **BlogId** and **Name** columns.</span></span> <span data-ttu-id="d483e-142">Zur besseren Sortierung enthält der Dateiname einen Zeitstempel.</span><span class="sxs-lookup"><span data-stu-id="d483e-142">The filename includes a timestamp to help with ordering.</span></span>
    <span data-ttu-id="d483e-143">*Wäre die Datenbank nicht bereits erstellt worden, wäre die InitialCreate-Migration dem Projekt nicht hinzugefügt worden. Stattdessen wäre beim ersten Aufruf von „Add-Migration“ der Code zum Erstellen dieser Tabellen als Entwurf für eine neue Migration verwendet worden.*</span><span class="sxs-lookup"><span data-stu-id="d483e-143">*If the database had not already been created this InitialCreate migration would not have been added to the project. Instead, the first time we call Add-Migration the code to create these tables would be scaffolded to a new migration.*</span></span>

### <a name="multiple-models-targeting-the-same-database"></a><span data-ttu-id="d483e-144">Mehrere Modelle mit derselben Zieldatenbank</span><span class="sxs-lookup"><span data-stu-id="d483e-144">Multiple Models Targeting the Same Database</span></span>

<span data-ttu-id="d483e-145">In Versionen vor EF6 konnte nur ein Code First-Modell zum Generieren/Verwalten eines Datenbankschemas verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d483e-145">When using versions prior to EF6, only one Code First model could be used to generate/manage the schema of a database.</span></span> <span data-ttu-id="d483e-146">Dies ist das Ergebnis einer einzelnen **\_\_MigrationsHistory**-Tabelle pro Datenbank, ohne Möglichkeit, die Zuordnung eines Eintrags zu einem Modell zu identifizieren.</span><span class="sxs-lookup"><span data-stu-id="d483e-146">This is the result of a single **\_\_MigrationsHistory** table per database with no way to identify which entries belong to which model.</span></span>

<span data-ttu-id="d483e-147">Ab EF6 enthält die **Configuration**-Klasse eine **ContextKey**-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="d483e-147">Starting with EF6, the **Configuration** class includes a **ContextKey** property.</span></span> <span data-ttu-id="d483e-148">Diese dient als eindeutiger Bezeichner für jedes Code First-Modell.</span><span class="sxs-lookup"><span data-stu-id="d483e-148">This acts as a unique identifier for each Code First model.</span></span> <span data-ttu-id="d483e-149">Durch eine entsprechende Spalte in der **\_\_MigrationsHistory**-Tabelle können Einträge aus mehreren Modellen die Tabelle gemeinsam nutzen.</span><span class="sxs-lookup"><span data-stu-id="d483e-149">A corresponding column in the **\_\_MigrationsHistory** table allows entries from multiple models to share the table.</span></span> <span data-ttu-id="d483e-150">Die Standardeinstellung für diese Eigenschaft ist der vollqualifizierte Name des Kontexts.</span><span class="sxs-lookup"><span data-stu-id="d483e-150">By default, this property is set to the fully qualified name of your context.</span></span>

## <a name="generating--running-migrations"></a><span data-ttu-id="d483e-151">Generieren und Ausführen von Migrationen</span><span class="sxs-lookup"><span data-stu-id="d483e-151">Generating & Running Migrations</span></span>

<span data-ttu-id="d483e-152">Code First-Migrationen verfügt über zwei primäre Befehle, die Sie im Folgenden kennenlernen werden:</span><span class="sxs-lookup"><span data-stu-id="d483e-152">Code First Migrations has two primary commands that you are going to become familiar with.</span></span>

-   <span data-ttu-id="d483e-153">**Add-Migration** erstellt den Entwurf für die nächste Migration basierend auf Änderungen, die Sie seit dem Erstellen der letzten Migration an Ihrem Modell vorgenommen haben.</span><span class="sxs-lookup"><span data-stu-id="d483e-153">**Add-Migration** will scaffold the next migration based on changes you have made to your model since the last migration was created</span></span>
-   <span data-ttu-id="d483e-154">**Update-Database** übernimmt alle ausstehenden Migrationen für die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="d483e-154">**Update-Database** will apply any pending migrations to the database</span></span>

<span data-ttu-id="d483e-155">Erstellen Sie einen Entwurf für die Migration, um die neu hinzugefügte URL-Eigenschaft zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="d483e-155">We need to scaffold a migration to take care of the new Url property we have added.</span></span> <span data-ttu-id="d483e-156">Mit dem **Add-Migration**-Befehl können Sie diesen Migrationen einen Namen geben, z.B. **AddBlogUrl**.</span><span class="sxs-lookup"><span data-stu-id="d483e-156">The **Add-Migration** command allows us to give these migrations a name, let’s just call ours **AddBlogUrl**.</span></span>

-   <span data-ttu-id="d483e-157">Führen Sie in der Paket-Manager-Konsole den Befehl **Add-Migration AddBlogUrl** aus.</span><span class="sxs-lookup"><span data-stu-id="d483e-157">Run the **Add-Migration AddBlogUrl** command in Package Manager Console</span></span>
-   <span data-ttu-id="d483e-158">Im **Migrations**-Ordner befindet sich nun eine neue **AddBlogUrl**-Migration.</span><span class="sxs-lookup"><span data-stu-id="d483e-158">In the **Migrations** folder we now have a new **AddBlogUrl** migration.</span></span> <span data-ttu-id="d483e-159">Zur besseren Sortierung wurde der Dateiname für die Migration vorab mit einem Zeitstempel versehen.</span><span class="sxs-lookup"><span data-stu-id="d483e-159">The migration filename is pre-fixed with a timestamp to help with ordering</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogUrl : DbMigration
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

<span data-ttu-id="d483e-160">Zu diesem Zeitpunkt kann die Migration bei Bedarf bearbeitet werden, oder ihr kann etwas hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="d483e-160">We could now edit or add to this migration but everything looks pretty good.</span></span> <span data-ttu-id="d483e-161">Übernehmen Sie die Migration mit **Update-Database** für die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="d483e-161">Let’s use **Update-Database** to apply this migration to the database.</span></span>

-   <span data-ttu-id="d483e-162">Führen Sie in der Paket-Manager-Konsole den Befehl **Update-Database** aus.</span><span class="sxs-lookup"><span data-stu-id="d483e-162">Run the **Update-Database** command in Package Manager Console</span></span>
-   <span data-ttu-id="d483e-163">Mit Code First-Migrationen werden die Migrationen im **Migrations**-Ordner mit den für die Datenbank übernommenen Migrationen verglichen.</span><span class="sxs-lookup"><span data-stu-id="d483e-163">Code First Migrations will compare the migrations in our **Migrations** folder with the ones that have been applied to the database.</span></span> <span data-ttu-id="d483e-164">Dabei wird festgestellt, dass die Migration **AddBlogUrl** übernommen und ausgeführt werden muss.</span><span class="sxs-lookup"><span data-stu-id="d483e-164">It will see that the **AddBlogUrl** migration needs to be applied, and run it.</span></span>

<span data-ttu-id="d483e-165">Die aktualisierte Datenbank **MigrationsDemo.BlogContext** enthält nun in der **Blogs**-Tabelle die Spalte **URL**.</span><span class="sxs-lookup"><span data-stu-id="d483e-165">The **MigrationsDemo.BlogContext** database is now updated to include the **Url** column in the **Blogs** table.</span></span>

## <a name="customizing-migrations"></a><span data-ttu-id="d483e-166">Anpassen von Migrationen</span><span class="sxs-lookup"><span data-stu-id="d483e-166">Customizing Migrations</span></span>

<span data-ttu-id="d483e-167">Bisher haben Sie eine Migration generiert und ausgeführt, ohne Änderungen vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="d483e-167">So far we’ve generated and run a migration without making any changes.</span></span> <span data-ttu-id="d483e-168">Nun erfahren Sie, wie Sie standardmäßig generierten Code bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="d483e-168">Now let’s look at editing the code that gets generated by default.</span></span>

-   <span data-ttu-id="d483e-169">Fügen Sie zunächst der **Blog**-Klasse eine neue **Rating**-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="d483e-169">It’s time to make some more changes to our model, let’s add a new **Rating** property to the **Blog** class</span></span>

``` csharp
    public int Rating { get; set; }
```

-   <span data-ttu-id="d483e-170">Fügen Sie zudem eine neue **Post**-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="d483e-170">Let's also add a new **Post** class</span></span>

``` csharp
    public class Post
    {
        public int PostId { get; set; }
        [MaxLength(200)]
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
```

-   <span data-ttu-id="d483e-171">Fügen Sie anschließend der **Blog**-Klasse eine **Posts**-Sammlung hinzu, die das andere Ende der Beziehung zwischen **Blog** und **Post** bilden soll.</span><span class="sxs-lookup"><span data-stu-id="d483e-171">We'll also add a **Posts** collection to the **Blog** class to form the other end of the relationship between **Blog** and **Post**</span></span>

``` csharp
    public virtual List<Post> Posts { get; set; }
```

<span data-ttu-id="d483e-172">Verwenden Sie den Befehl **Add-Migration**, damit Code First-Migrationen die am besten passenden Migrationen für Sie entwirft.</span><span class="sxs-lookup"><span data-stu-id="d483e-172">We'll use the **Add-Migration** command to let Code First Migrations scaffold its best guess at the migration for us.</span></span> <span data-ttu-id="d483e-173">Geben Sie dieser Migration den Namen **AddPostClass**.</span><span class="sxs-lookup"><span data-stu-id="d483e-173">We’re going to call this migration **AddPostClass**.</span></span>

-   <span data-ttu-id="d483e-174">Führen Sie in der Paket-Manager-Konsole den Befehl **Add-Migration AddPostClass** aus.</span><span class="sxs-lookup"><span data-stu-id="d483e-174">Run the **Add-Migration AddPostClass** command in Package Manager Console.</span></span>

<span data-ttu-id="d483e-175">Der Entwurf für die Änderungen wurde von Code First-Migrationen bestmöglich erstellt. Nun sollten Sie einige Änderungen vornehmen:</span><span class="sxs-lookup"><span data-stu-id="d483e-175">Code First Migrations did a pretty good job of scaffolding these changes, but there are some things we might want to change:</span></span>

1.  <span data-ttu-id="d483e-176">Fügen Sie zunächst der Spalte **Posts.Title** einen eindeutigen Index hinzu (indem Sie untenstehendem Code die Zeilen 22 und 29 hinzufügen).</span><span class="sxs-lookup"><span data-stu-id="d483e-176">First up, let’s add a unique index to **Posts.Title** column (Adding in line 22 & 29 in the code below).</span></span>
2.  <span data-ttu-id="d483e-177">Fügen Sie außerdem eine **Blogs.Rating**-Spalte hinzu, die keine NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="d483e-177">We’re also adding a non-nullable **Blogs.Rating** column.</span></span> <span data-ttu-id="d483e-178">Sind in der Tabelle Daten vorhanden, wird ihnen der CLR-Standarddatentyp für die neue Spalte zugewiesen („Rating“ erfordert einen ganzzahligen Wert, in diesem Fall **0**).</span><span class="sxs-lookup"><span data-stu-id="d483e-178">If there is any existing data in the table it will get assigned the CLR default of the data type for new column (Rating is integer, so that would be **0**).</span></span> <span data-ttu-id="d483e-179">Damit die vorhandenen Spalten der **Blogs**-Tabelle mit einem vernünftigen Rating-Wert beginnen, geben Sie den Standardwert **3** an.</span><span class="sxs-lookup"><span data-stu-id="d483e-179">But we want to specify a default value of **3** so that existing rows in the **Blogs** table will start with a decent rating.</span></span>
    <span data-ttu-id="d483e-180">(Der angegebene Standardwert wird im folgenden Code in Zeile 24 angezeigt.)</span><span class="sxs-lookup"><span data-stu-id="d483e-180">(You can see the default value specified on line 24 of the code below)</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostClass : DbMigration
        {
            public override void Up()
            {
                CreateTable(
                    "dbo.Posts",
                    c => new
                        {
                            PostId = c.Int(nullable: false, identity: true),
                            Title = c.String(maxLength: 200),
                            Content = c.String(),
                            BlogId = c.Int(nullable: false),
                        })
                    .PrimaryKey(t => t.PostId)
                    .ForeignKey("dbo.Blogs", t => t.BlogId, cascadeDelete: true)
                    .Index(t => t.BlogId)
                    .Index(p => p.Title, unique: true);

                AddColumn("dbo.Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropIndex("dbo.Posts", new[] { "Title" });
                DropIndex("dbo.Posts", new[] { "BlogId" });
                DropForeignKey("dbo.Posts", "BlogId", "dbo.Blogs");
                DropColumn("dbo.Blogs", "Rating");
                DropTable("dbo.Posts");
            }
        }
    }
```

<span data-ttu-id="d483e-181">Die bearbeitete Migration ist jetzt einsatzbereit. Aktualisieren Sie daher die Datenbank mit **Update-Database**.</span><span class="sxs-lookup"><span data-stu-id="d483e-181">Our edited migration is ready to go, so let’s use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="d483e-182">Geben Sie dieses Mal das Flag **–Verbose** an, damit das SQL angezeigt wird, mit dem Code First-Migrationen ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="d483e-182">This time let’s specify the **–Verbose** flag so that you can see the SQL that Code First Migrations is running.</span></span>

-   <span data-ttu-id="d483e-183">Führen Sie in der Paket-Manager-Konsole den Befehl **Update-Database –Verbose** aus.</span><span class="sxs-lookup"><span data-stu-id="d483e-183">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="data-motion--custom-sql"></a><span data-ttu-id="d483e-184">Verschieben von Daten mit benutzerdefiniertem SQL</span><span class="sxs-lookup"><span data-stu-id="d483e-184">Data Motion / Custom SQL</span></span>

<span data-ttu-id="d483e-185">Bisher haben Sie nur Migrationsvorgänge kennengelernt, bei denen die Daten weder geändert noch verschoben werden. Im Folgenden werden auch Vorgänge mit Datenverschiebungen behandelt.</span><span class="sxs-lookup"><span data-stu-id="d483e-185">So far we have looked at migration operations that don’t change or move any data, now let’s look at something that needs to move some data around.</span></span> <span data-ttu-id="d483e-186">Für Datenverschiebungen ist derzeit noch keine native Unterstützung verfügbar. Doch Sie können jederzeit einige beliebige SQL-Befehle im Skript ausführen.</span><span class="sxs-lookup"><span data-stu-id="d483e-186">There is no native support for data motion yet, but we can run some arbitrary SQL commands at any point in our script.</span></span>

-   <span data-ttu-id="d483e-187">Fügen Sie dem Modell eine **Post.Abstract**-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="d483e-187">Let’s add a **Post.Abstract** property to our model.</span></span> <span data-ttu-id="d483e-188">Später erfahren Sie, wie Sie für vorhandene Beiträge die **Abstract**-Spalte mit Text vom Anfang der **Content**-Spalte vorab auffüllen.</span><span class="sxs-lookup"><span data-stu-id="d483e-188">Later, we’re going to pre-populate the **Abstract** for existing posts using some text from the start of the **Content** column.</span></span>

``` csharp
    public string Abstract { get; set; }
```

<span data-ttu-id="d483e-189">Verwenden Sie den Befehl **Add-Migration**, damit Code First-Migrationen die am besten passenden Migrationen für Sie entwirft.</span><span class="sxs-lookup"><span data-stu-id="d483e-189">We'll use the **Add-Migration** command to let Code First Migrations scaffold its best guess at the migration for us.</span></span>

-   <span data-ttu-id="d483e-190">Führen Sie in der Paket-Manager-Konsole den Befehl **Add-Migration AddPostAbstract** aus.</span><span class="sxs-lookup"><span data-stu-id="d483e-190">Run the **Add-Migration AddPostAbstract** command in Package Manager Console.</span></span>
-   <span data-ttu-id="d483e-191">Die generierte Migration übernimmt die Änderungen des Schemas. Doch es soll auch die **Abstract**-Spalte mit den ersten 100 Zeichen des Inhalts für jeden Beitrag vorab aufgefüllt werden.</span><span class="sxs-lookup"><span data-stu-id="d483e-191">The generated migration takes care of the schema changes but we also want to pre-populate the **Abstract** column using the first 100 characters of content for each post.</span></span> <span data-ttu-id="d483e-192">Führen Sie hierzu in SQL die **UPDATE**-Anweisung aus, nachdem die Spalte hinzugefügt wurde</span><span class="sxs-lookup"><span data-stu-id="d483e-192">We can do this by dropping down to SQL and running an **UPDATE** statement after the column is added.</span></span>
    <span data-ttu-id="d483e-193">(indem Sie untenstehendem Code die Zeile 12 hinzufügen).</span><span class="sxs-lookup"><span data-stu-id="d483e-193">(Adding in line 12 in the code below)</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostAbstract : DbMigration
        {
            public override void Up()
            {
                AddColumn("dbo.Posts", "Abstract", c => c.String());

                Sql("UPDATE dbo.Posts SET Abstract = LEFT(Content, 100) WHERE Abstract IS NULL");
            }

            public override void Down()
            {
                DropColumn("dbo.Posts", "Abstract");
            }
        }
    }
```

<span data-ttu-id="d483e-194">Die Migration ist nun fertig bearbeitet. Aktualisieren Sie daher die Datenbank mit **Update-Database**.</span><span class="sxs-lookup"><span data-stu-id="d483e-194">Our edited migration is looking good, so let’s use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="d483e-195">Geben Sie das Flag **–Verbose** an, damit das SQL angezeigt wird, das auf der Datenbank ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="d483e-195">We’ll specify the **–Verbose** flag so that we can see the SQL being run against the database.</span></span>

-   <span data-ttu-id="d483e-196">Führen Sie in der Paket-Manager-Konsole den Befehl **Update-Database –Verbose** aus.</span><span class="sxs-lookup"><span data-stu-id="d483e-196">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="migrate-to-a-specific-version-including-downgrade"></a><span data-ttu-id="d483e-197">Migrieren zu einer bestimmten Version (einschließlich Downgrade)</span><span class="sxs-lookup"><span data-stu-id="d483e-197">Migrate to a Specific Version (Including Downgrade)</span></span>

<span data-ttu-id="d483e-198">Bisher haben Sie immer ein Upgrade auf die neueste Migration durchgeführt, doch manchmal möchten Sie vielleicht ein Upgrade/Downgrade auf eine bestimmte Migration durchführen.</span><span class="sxs-lookup"><span data-stu-id="d483e-198">So far we have always upgraded to the latest migration, but there may be times when you want upgrade/downgrade to a specific migration.</span></span>

<span data-ttu-id="d483e-199">Angenommen, Sie möchten die Datenbank zu dem Zustand nach der **AddBlogUrl**-Migration migrieren.</span><span class="sxs-lookup"><span data-stu-id="d483e-199">Let’s say we want to migrate our database to the state it was in after running our **AddBlogUrl** migration.</span></span> <span data-ttu-id="d483e-200">Verwenden Sie den Schalter **–TargetMigration**, um ein Downgrade auf diese Migration durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="d483e-200">We can use the **–TargetMigration** switch to downgrade to this migration.</span></span>

-   <span data-ttu-id="d483e-201">Führen Sie in der Paket-Manager-Konsole den Befehl **Update-Database –TargetMigration: AddBlogUrl** aus.</span><span class="sxs-lookup"><span data-stu-id="d483e-201">Run the **Update-Database –TargetMigration: AddBlogUrl** command in Package Manager Console.</span></span>

<span data-ttu-id="d483e-202">Mit diesem Befehl wird das Down-Skript für die Migrationen **AddBlogAbstract** und **AddPostClass** ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="d483e-202">This command will run the Down script for our **AddBlogAbstract** and **AddPostClass** migrations.</span></span>

<span data-ttu-id="d483e-203">Verwenden Sie zum Zurücksetzen auf eine leere Datenbank den Befehl **Update-Database –TargetMigration: $InitialDatabase**.</span><span class="sxs-lookup"><span data-stu-id="d483e-203">If you want to roll all the way back to an empty database then you can use the **Update-Database –TargetMigration: $InitialDatabase** command.</span></span>

## <a name="getting-a-sql-script"></a><span data-ttu-id="d483e-204">Abrufen eines SQL-Skripts</span><span class="sxs-lookup"><span data-stu-id="d483e-204">Getting a SQL Script</span></span>

<span data-ttu-id="d483e-205">Andere Entwickler können diese Änderungen auf ihren Computer übertragen, indem sie eine Synchronisierung durchführen, nachdem Sie die Änderungen in die Quellcodeverwaltung eingecheckt haben.</span><span class="sxs-lookup"><span data-stu-id="d483e-205">If another developer wants these changes on their machine they can just sync once we check our changes into source control.</span></span> <span data-ttu-id="d483e-206">Sobald die neuen Migrationen bei ihnen angekommen sind, müssen sie nur noch den Befehl „Update-Database“ ausführen, um die Änderungen lokal zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="d483e-206">Once they have our new migrations they can just run the Update-Database command to have the changes applied locally.</span></span> <span data-ttu-id="d483e-207">Wenn Sie jedoch diese Änderungen auf einen Testserver und eventuell zur Produktion weiterleiten möchten, benötigen Sie vermutlich ein SQL-Skript, das Sie an den DBA übergeben können.</span><span class="sxs-lookup"><span data-stu-id="d483e-207">However if we want to push these changes out to a test server, and eventually production, we probably want a SQL script we can hand off to our DBA.</span></span>

-   <span data-ttu-id="d483e-208">Führen Sie den Befehl **Update-Database** aus, doch geben Sie dieses Mal das Flag **–Script** an, damit die Änderungen nicht übernommen, sondern in ein Skript geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="d483e-208">Run the **Update-Database** command but this time specify the **–Script** flag so that changes are written to a script rather than applied.</span></span> <span data-ttu-id="d483e-209">Geben Sie zudem eine Quell- und Zielmigration an, für die das Skript generiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="d483e-209">We’ll also specify a source and target migration to generate the script for.</span></span> <span data-ttu-id="d483e-210">Das Skript sollte von einer leeren Datenbank (**$InitialDatabase**) bis zur neuesten Version (**AddPostAbstract**-Migration) reichen.</span><span class="sxs-lookup"><span data-stu-id="d483e-210">We want a script to go from an empty database (**$InitialDatabase**) to the latest version (migration **AddPostAbstract**).</span></span>
    <span data-ttu-id="d483e-211">*Wenn Sie keine Zielmigration angeben, verwendet Code First-Migrationen die neueste Migration als Ziel. Wenn Sie keine Quellmigrationen angeben, verwendet Code First-Migrationen den aktuellen Zustand der Datenbank.*</span><span class="sxs-lookup"><span data-stu-id="d483e-211">*If you don’t specify a target migration, Migrations will use the latest migration as the target. If you don't specify a source migrations, Migrations will use the current state of the database.*</span></span>
-   <span data-ttu-id="d483e-212">Führen Sie in der Paket-Manager-Konsole den Befehl **Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration: AddPostAbstract** aus.</span><span class="sxs-lookup"><span data-stu-id="d483e-212">Run the **Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration: AddPostAbstract** command in Package Manager Console</span></span>

<span data-ttu-id="d483e-213">Code First-Migrationen führt die Migrationspipeline aus. Doch anstatt die Änderungen tatsächlich zu übernehmen, werden sie in eine SQL-Datei geschrieben.</span><span class="sxs-lookup"><span data-stu-id="d483e-213">Code First Migrations will run the migration pipeline but instead of actually applying the changes it will write them out to a .sql file for you.</span></span> <span data-ttu-id="d483e-214">Nachdem das Skript generiert wurde, wird es in Visual Studio geöffnet. Dort können Sie es sich ansehen oder speichern.</span><span class="sxs-lookup"><span data-stu-id="d483e-214">Once the script is generated, it is opened for you in Visual Studio, ready for you to view or save.</span></span>

### <a name="generating-idempotent-scripts"></a><span data-ttu-id="d483e-215">Generieren von idempotenten Skripts</span><span class="sxs-lookup"><span data-stu-id="d483e-215">Generating Idempotent Scripts</span></span>

<span data-ttu-id="d483e-216">Ab EF6 wird mit der Angabe von **–SourceMigration $InitialDatabase** ein idempotentes Skript generiert.</span><span class="sxs-lookup"><span data-stu-id="d483e-216">Starting with EF6, if you specify **–SourceMigration $InitialDatabase** then the generated script will be ‘idempotent’.</span></span> <span data-ttu-id="d483e-217">Mit idempotenten Skripts kann für eine Datenbank ein Upgrade von einer beliebigen Version auf die neueste Version (oder die angegebene Version, wenn Sie **–TargetMigration** verwenden) durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="d483e-217">Idempotent scripts can upgrade a database currently at any version to the latest version (or the specified version if you use **–TargetMigration**).</span></span> <span data-ttu-id="d483e-218">Das generierte Skript enthält Logik zum Überprüfen der **\_\_MigrationsHistory**-Tabelle. Es werden nur Änderungen übernommen, die nicht schon zuvor übernommen wurden.</span><span class="sxs-lookup"><span data-stu-id="d483e-218">The generated script includes logic to check the **\_\_MigrationsHistory** table and only apply changes that haven't been previously applied.</span></span>

## <a name="automatically-upgrading-on-application-startup-migratedatabasetolatestversion-initializer"></a><span data-ttu-id="d483e-219">Automatisches Upgrade beim Starten der Anwendung (Initialisierer „MigrateDatabaseToLatestVersion“)</span><span class="sxs-lookup"><span data-stu-id="d483e-219">Automatically Upgrading on Application Startup (MigrateDatabaseToLatestVersion Initializer)</span></span>

<span data-ttu-id="d483e-220">Beim Bereitstellen der Anwendung sollten Sie festlegen, dass für die Datenbank beim Starten der Anwendung automatisch ein Upgrade durchgeführt wird (indem die ausstehenden Migrationen übernommen werden).</span><span class="sxs-lookup"><span data-stu-id="d483e-220">If you are deploying your application you may want it to automatically upgrade the database (by applying any pending migrations) when the application launches.</span></span> <span data-ttu-id="d483e-221">Registrieren Sie hierzu den Datenbankinitialisierer **MigrateDatabaseToLatestVersion**.</span><span class="sxs-lookup"><span data-stu-id="d483e-221">You can do this by registering the **MigrateDatabaseToLatestVersion** database initializer.</span></span> <span data-ttu-id="d483e-222">Ein Datenbankinitialisierer enthält Logik, mit der sichergestellt wird, dass die Datenbank ordnungsgemäß eingerichtet ist.</span><span class="sxs-lookup"><span data-stu-id="d483e-222">A database initializer simply contains some logic that is used to make sure the database is setup correctly.</span></span> <span data-ttu-id="d483e-223">Diese Logik wird bei der ersten Verwendung des Kontexts im Anwendungsprozess ausgeführt (**AppDomain**).</span><span class="sxs-lookup"><span data-stu-id="d483e-223">This logic is run the first time the context is used within the application process (**AppDomain**).</span></span>

<span data-ttu-id="d483e-224">Sie können die Datei **Program.cs** wie unten dargestellt so aktualisieren, dass der Initialisierer **MigrateDatabaseToLatestVersion** auf „BlogContext“ festgelegt wird, bevor der Kontext verwendet wird (Zeile 14).</span><span class="sxs-lookup"><span data-stu-id="d483e-224">We can update the **Program.cs** file, as shown below, to set the **MigrateDatabaseToLatestVersion** initializer for BlogContext before we use the context (Line 14).</span></span> <span data-ttu-id="d483e-225">Beachten Sie, dass Sie für den Namespace **System.Data.Entity** auch eine Using-Anweisung hinzufügen müssen (Zeile 5).</span><span class="sxs-lookup"><span data-stu-id="d483e-225">Note that you also need to add a using statement for the **System.Data.Entity** namespace (Line 5).</span></span>

<span data-ttu-id="d483e-226">*Beim Erstellen einer Instanz von diesem Initialisierer müssen Sie den Kontexttyp (**BlogContext**) und die Migrationskonfiguration (**Configuration**) angeben. Die Migrationskonfiguration ist die Klasse, die dem **Migrations**-Ordner beim Aktivieren von Migrationen hinzugefügt wurde.*</span><span class="sxs-lookup"><span data-stu-id="d483e-226">*When we create an instance of this initializer we need to specify the context type (**BlogContext**) and the migrations configuration (**Configuration**) - the migrations configuration is the class that got added to our **Migrations** folder when we enabled Migrations.*</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Data.Entity;
    using MigrationsDemo.Migrations;

    namespace MigrationsDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                Database.SetInitializer(new MigrateDatabaseToLatestVersion<BlogContext, Configuration>());

                using (var db = new BlogContext())
                {
                    db.Blogs.Add(new Blog { Name = "Another Blog " });
                    db.SaveChanges();

                    foreach (var blog in db.Blogs)
                    {
                        Console.WriteLine(blog.Name);
                    }
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }
        }
    }
```

<span data-ttu-id="d483e-227">Die Anwendung überprüft nun bei jeder Ausführung zunächst, ob die Zieldatenbank auf dem neuesten Stand ist, und übernimmt gegebenenfalls ausstehende Migrationen.</span><span class="sxs-lookup"><span data-stu-id="d483e-227">Now whenever our application runs it will first check if the database it is targeting is up-to-date, and apply any pending migrations if it is not.</span></span>
