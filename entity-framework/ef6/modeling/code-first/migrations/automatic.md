---
title: Automatisches Code First-Migrationen-EF6
description: Automatisches Code First-Migrationen in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 0eb86787-2161-4cb4-9cb8-67c5d6e95650
uid: ef6/modeling/code-first/migrations/automatic
ms.openlocfilehash: 541adf5aed517685b54d675730996c12ff0c824a
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618077"
---
# <a name="automatic-code-first-migrations"></a><span data-ttu-id="131a3-103">Automatisches Code First-Migrationen</span><span class="sxs-lookup"><span data-stu-id="131a3-103">Automatic Code First Migrations</span></span>
<span data-ttu-id="131a3-104">Automatische Migrationen ermöglichen es Ihnen, Code First-Migrationen zu verwenden, ohne dass eine Codedatei in Ihrem Projekt für jede von Ihnen vornimmt.</span><span class="sxs-lookup"><span data-stu-id="131a3-104">Automatic Migrations allows you to use Code First Migrations without having a code file in your project for each change you make.</span></span> <span data-ttu-id="131a3-105">Nicht alle Änderungen können automatisch angewendet werden, z. b. für Spalten Umbenennungen ist die Verwendung einer Code basierten Migration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="131a3-105">Not all changes can be applied automatically - for example column renames require the use of a code-based migration.</span></span>

> [!NOTE]
> <span data-ttu-id="131a3-106">In diesem Artikel wird davon ausgegangen, dass Sie wissen, wie Code First-Migrationen in einfachen Szenarien verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="131a3-106">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="131a3-107">Andernfalls müssen Sie [Code First-Migrationen](xref:ef6/modeling/code-first/migrations/index) lesen, bevor Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="131a3-107">If you don’t, then you’ll need to read [Code First Migrations](xref:ef6/modeling/code-first/migrations/index) before continuing.</span></span>

## <a name="recommendation-for-team-environments"></a><span data-ttu-id="131a3-108">Empfehlung für Team Umgebungen</span><span class="sxs-lookup"><span data-stu-id="131a3-108">Recommendation for Team Environments</span></span>

<span data-ttu-id="131a3-109">Sie können automatische und Code basierte Migrationen interagieren, dies wird jedoch in Team Entwicklungsszenarien nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="131a3-109">You can intersperse automatic and code-based migrations but this is not recommended in team development scenarios.</span></span> <span data-ttu-id="131a3-110">Wenn Sie Teil eines Teams von Entwicklern sind, das die Quell Code Verwaltung verwendet, sollten Sie entweder reine automatische Migrationen oder rein Code basierte Migrationen verwenden.</span><span class="sxs-lookup"><span data-stu-id="131a3-110">If you are part of a team of developers that use source control you should either use purely automatic migrations or purely code-based migrations.</span></span> <span data-ttu-id="131a3-111">Aufgrund der Einschränkungen für automatische Migrationen empfiehlt sich die Verwendung von Code basierten Migrationen in Team Umgebungen.</span><span class="sxs-lookup"><span data-stu-id="131a3-111">Given the limitations of automatic migrations we recommend using code-based migrations in team environments.</span></span>

## <a name="building-an-initial-model--database"></a><span data-ttu-id="131a3-112">Erstellen eines Ausgangsmodells und einer Ausgangsdatenbank</span><span class="sxs-lookup"><span data-stu-id="131a3-112">Building an Initial Model & Database</span></span>

<span data-ttu-id="131a3-113">Bevor Sie Migrationen verwenden können, benötigen Sie ein Projekt und ein Code First-Modell.</span><span class="sxs-lookup"><span data-stu-id="131a3-113">Before we start using migrations we need a project and a Code First model to work with.</span></span> <span data-ttu-id="131a3-114">In dieser exemplarischen Vorgehensweise werden die kanonischen Modelle **Blog** und **Post** verwendet.</span><span class="sxs-lookup"><span data-stu-id="131a3-114">For this walkthrough we are going to use the canonical **Blog** and **Post** model.</span></span>

-   <span data-ttu-id="131a3-115">Erstellen einer neuen **migrationsautomaticdemo** -Konsolenanwendung</span><span class="sxs-lookup"><span data-stu-id="131a3-115">Create a new **MigrationsAutomaticDemo** Console application</span></span>
-   <span data-ttu-id="131a3-116">Fügen Sie dem Projekt die neueste Version des NuGet-Pakets **EntityFramework** hinzu.</span><span class="sxs-lookup"><span data-stu-id="131a3-116">Add the latest version of the **EntityFramework** NuGet package to the project</span></span>
    -   <span data-ttu-id="131a3-117">**Tools – &gt; Bibliothekspaket-Manager – &gt; Paket-Manager-Konsole**</span><span class="sxs-lookup"><span data-stu-id="131a3-117">**Tools –&gt; Library Package Manager –&gt; Package Manager Console**</span></span>
    -   <span data-ttu-id="131a3-118">Führen Sie den Befehl **Install-Package EntityFramework** aus.</span><span class="sxs-lookup"><span data-stu-id="131a3-118">Run the **Install-Package EntityFramework** command</span></span>
-   <span data-ttu-id="131a3-119">Fügen Sie eine **Model.cs**-Datei mit dem unten aufgeführten Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="131a3-119">Add a **Model.cs** file with the code shown below.</span></span> <span data-ttu-id="131a3-120">Mit diesem Code wird eine einzelne **Blog**-Klasse, die das Domänenmodell bildet, und eine **BlogContext**-Klasse, die den EF Code First-Kontext darstellt, definiert.</span><span class="sxs-lookup"><span data-stu-id="131a3-120">This code defines a single **Blog** class that makes up our domain model and a **BlogContext** class that is our EF Code First context</span></span>

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsAutomaticDemo
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

-   <span data-ttu-id="131a3-121">Mit dem Modell können Sie nun auf die Daten zugreifen.</span><span class="sxs-lookup"><span data-stu-id="131a3-121">Now that we have a model it’s time to use it to perform data access.</span></span> <span data-ttu-id="131a3-122">Aktualisieren Sie die **Program.cs**-Datei mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="131a3-122">Update the **Program.cs** file with the code shown below.</span></span>

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsAutomaticDemo
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

-   <span data-ttu-id="131a3-123">Führen Sie die Anwendung aus, und Sie werden feststellen, dass eine **migrationsautomaticcodedemo. blogcontext** -Datenbank für Sie erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="131a3-123">Run your application and you will see that a **MigrationsAutomaticCodeDemo.BlogContext** database is created for you.</span></span>

    ![Localdb-Datenbank](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a><span data-ttu-id="131a3-125">Aktivieren von Migrationen</span><span class="sxs-lookup"><span data-stu-id="131a3-125">Enabling Migrations</span></span>

<span data-ttu-id="131a3-126">Im Folgenden erfahren Sie, wie Sie weitere Änderungen am Modell vornehmen.</span><span class="sxs-lookup"><span data-stu-id="131a3-126">It’s time to make some more changes to our model.</span></span>

-   <span data-ttu-id="131a3-127">Fügen Sie der Blog-Klasse eine URL-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="131a3-127">Let’s introduce a Url property to the Blog class.</span></span>

``` csharp
    public string Url { get; set; }
```

<span data-ttu-id="131a3-128">Wenn Sie die Anwendung erneut ausführen, erhalten Sie eine InvalidOperationException, die besagt, dass *das Modell, das den Kontext "blogcontext" unterstützt, seit der Erstellung der Datenbank geändert wurde. Verwenden Sie Code First-Migrationen, um die Datenbank zu aktualisieren (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269) *).*</span><span class="sxs-lookup"><span data-stu-id="131a3-128">If you were to run the application again you would get an InvalidOperationException stating *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*).*</span></span>

<span data-ttu-id="131a3-129">Beginnen Sie daher mit der Verwendung von Code First-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="131a3-129">As the exception suggests, it’s time to start using Code First Migrations.</span></span> <span data-ttu-id="131a3-130">Da wir automatische Migrationen verwenden möchten, geben wir den Schalter **– enableautomaticmigrationen** an.</span><span class="sxs-lookup"><span data-stu-id="131a3-130">Because we want to use automatic migrations we’re going to specify the **–EnableAutomaticMigrations** switch.</span></span>

-   <span data-ttu-id="131a3-131">Führen Sie den Befehl **enable-Migrationen – enableautomaticmigrationen** in der Paket-Manager-Konsole aus. dieser Befehl hat dem Projekt einen **Migrations** Ordner hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="131a3-131">Run the **Enable-Migrations –EnableAutomaticMigrations** command in Package Manager Console This command has added a **Migrations** folder to our project.</span></span> <span data-ttu-id="131a3-132">Dieser neue Ordner enthält eine Datei:</span><span class="sxs-lookup"><span data-stu-id="131a3-132">This new folder contains one file:</span></span>

-   <span data-ttu-id="131a3-133">**Die Dateikonfigurationsklasse:**</span><span class="sxs-lookup"><span data-stu-id="131a3-133">**The Configuration class.**</span></span> <span data-ttu-id="131a3-134">Mit dieser Klasse können Sie das Verhalten von Migrationen für den Kontext konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="131a3-134">This class allows you to configure how Migrations behaves for your context.</span></span> <span data-ttu-id="131a3-135">Verwenden Sie für diese exemplarische Vorgehensweise die Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="131a3-135">For this walkthrough we will just use the default configuration.</span></span>
    <span data-ttu-id="131a3-136">*Da in Ihrem Projekt nur ein einzelner Code First-Kontext vorhanden ist, wurde der Kontexttyp, der für diese Konfiguration gilt, automatisch von „Enable-Migrations“ ausgefüllt.*</span><span class="sxs-lookup"><span data-stu-id="131a3-136">*Because there is just a single Code First context in your project, Enable-Migrations has automatically filled in the context type this configuration applies to.*</span></span>

 

## <a name="your-first-automatic-migration"></a><span data-ttu-id="131a3-137">Ihre erste automatische Migration</span><span class="sxs-lookup"><span data-stu-id="131a3-137">Your First Automatic Migration</span></span>

<span data-ttu-id="131a3-138">Code First-Migrationen verfügt über zwei primäre Befehle, die Sie im Folgenden kennenlernen werden:</span><span class="sxs-lookup"><span data-stu-id="131a3-138">Code First Migrations has two primary commands that you are going to become familiar with.</span></span>

-   <span data-ttu-id="131a3-139">**Add-Migration** erstellt den Entwurf für die nächste Migration basierend auf Änderungen, die Sie seit dem Erstellen der letzten Migration an Ihrem Modell vorgenommen haben.</span><span class="sxs-lookup"><span data-stu-id="131a3-139">**Add-Migration** will scaffold the next migration based on changes you have made to your model since the last migration was created</span></span>
-   <span data-ttu-id="131a3-140">**Update-Database** übernimmt alle ausstehenden Migrationen für die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="131a3-140">**Update-Database** will apply any pending migrations to the database</span></span>

<span data-ttu-id="131a3-141">Wir werden die Verwendung der Add-Migration vermeiden (es sei denn, wir brauchen dies wirklich) und konzentrieren uns darauf, dass Code First-Migrationen die Änderungen automatisch berechnen und anwenden.</span><span class="sxs-lookup"><span data-stu-id="131a3-141">We are going to avoid using Add-Migration (unless we really need to) and focus on letting Code First Migrations automatically calculate and apply the changes.</span></span> <span data-ttu-id="131a3-142">Verwenden Sie " **Update-Database** ", um Code First-Migrationen, die Änderungen an unser Modell (die neue Eigenschaft " **Blog. ur**l") an die Datenbank zu überführen.</span><span class="sxs-lookup"><span data-stu-id="131a3-142">Let’s use **Update-Database** to get Code First Migrations to push the changes to our model (the new **Blog.Ur**l property) to the database.</span></span>

-   <span data-ttu-id="131a3-143">Führen Sie den Befehl **Update-Database** in der Paket-Manager-Konsole aus.</span><span class="sxs-lookup"><span data-stu-id="131a3-143">Run the **Update-Database** command in Package Manager Console.</span></span>

<span data-ttu-id="131a3-144">Die **migrationsautomaticdemo. blogcontext** -Datenbank wurde nun aktualisiert und enthält nun die **URL** -Spalte in der **Blogs** -Tabelle.</span><span class="sxs-lookup"><span data-stu-id="131a3-144">The **MigrationsAutomaticDemo.BlogContext** database is now updated to include the **Url** column in the **Blogs** table.</span></span>

 

## <a name="your-second-automatic-migration"></a><span data-ttu-id="131a3-145">Ihre zweite automatische Migration</span><span class="sxs-lookup"><span data-stu-id="131a3-145">Your Second Automatic Migration</span></span>

<span data-ttu-id="131a3-146">Nehmen wir eine weitere Änderung vor, und lassen Sie Code First-Migrationen die Änderungen für uns automatisch per Push an die Datenbank überführen.</span><span class="sxs-lookup"><span data-stu-id="131a3-146">Let’s make another change and let Code First Migrations automatically push the changes to the database for us.</span></span>

-   <span data-ttu-id="131a3-147">Fügen Sie zudem eine neue **Post**-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="131a3-147">Let's also add a new **Post** class</span></span>

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

-   <span data-ttu-id="131a3-148">Fügen Sie anschließend der **Blog**-Klasse eine **Posts**-Sammlung hinzu, die das andere Ende der Beziehung zwischen **Blog** und **Post** bilden soll.</span><span class="sxs-lookup"><span data-stu-id="131a3-148">We'll also add a **Posts** collection to the **Blog** class to form the other end of the relationship between **Blog** and **Post**</span></span>

``` csharp
    public virtual List<Post> Posts { get; set; }
```

<span data-ttu-id="131a3-149">Verwenden Sie jetzt **Update-Database** , um die Datenbank auf den neuesten Stand zu bringen.</span><span class="sxs-lookup"><span data-stu-id="131a3-149">Now use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="131a3-150">Geben Sie dieses Mal das Flag **–Verbose** an, damit das SQL angezeigt wird, mit dem Code First-Migrationen ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="131a3-150">This time let’s specify the **–Verbose** flag so that you can see the SQL that Code First Migrations is running.</span></span>

-   <span data-ttu-id="131a3-151">Führen Sie in der Paket-Manager-Konsole den Befehl **Update-Database –Verbose** aus.</span><span class="sxs-lookup"><span data-stu-id="131a3-151">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="adding-a-code-based-migration"></a><span data-ttu-id="131a3-152">Hinzufügen einer Code basierten Migration</span><span class="sxs-lookup"><span data-stu-id="131a3-152">Adding a Code Based Migration</span></span>

<span data-ttu-id="131a3-153">Sehen wir uns nun an, wie wir eine Code basierte Migration für verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="131a3-153">Now let’s look at something we might want to use a code-based migration for.</span></span>

-   <span data-ttu-id="131a3-154">Fügen Sie der **Blog** -Klasse eine **Bewertungs** Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="131a3-154">Let’s add a **Rating** property to the **Blog** class</span></span>

``` csharp
    public int Rating { get; set; }
```

<span data-ttu-id="131a3-155">Wir könnten einfach " **Update-Database** " ausführen, um diese Änderungen per Push in die Datenbank zu überführen.</span><span class="sxs-lookup"><span data-stu-id="131a3-155">We could just run **Update-Database** to push these changes to the database.</span></span> <span data-ttu-id="131a3-156">Allerdings fügen wir eine **Blogs. Rating** -Spalte hinzu, die keine NULL-Werte zulässt. Wenn in der Tabelle vorhandene Daten vorhanden sind, wird ihr die CLR-Standardeinstellung des Datentyps für die neue Spalte zugewiesen (die Bewertung ist Integer, d. h. **0**).</span><span class="sxs-lookup"><span data-stu-id="131a3-156">However, we're adding a non-nullable **Blogs.Rating** column, if there is any existing data in the table it will get assigned the CLR default of the data type for new column (Rating is integer, so that would be **0**).</span></span> <span data-ttu-id="131a3-157">Damit die vorhandenen Spalten der **Blogs**-Tabelle mit einem vernünftigen Rating-Wert beginnen, geben Sie den Standardwert **3** an.</span><span class="sxs-lookup"><span data-stu-id="131a3-157">But we want to specify a default value of **3** so that existing rows in the **Blogs** table will start with a decent rating.</span></span>
<span data-ttu-id="131a3-158">Verwenden Sie den Befehl "Add-Migration", um diese Änderung in eine Code basierte Migration zu schreiben, damit wir Sie bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="131a3-158">Let’s use the Add-Migration command to write this change out to a code-based migration so that we can edit it.</span></span> <span data-ttu-id="131a3-159">Der Befehl " **Add-Migration** " ermöglicht es uns, diesen Migrationen einen Namen zu geben. wir nennen einfach "unsere **addblograting**".</span><span class="sxs-lookup"><span data-stu-id="131a3-159">The **Add-Migration** command allows us to give these migrations a name, let’s just call ours **AddBlogRating**.</span></span>

-   <span data-ttu-id="131a3-160">Führen Sie den Befehl **Add-Migration addblograting** in der Paket-Manager-Konsole aus.</span><span class="sxs-lookup"><span data-stu-id="131a3-160">Run the **Add-Migration AddBlogRating** command in Package Manager Console.</span></span>
-   <span data-ttu-id="131a3-161">Im **Migrations** Ordner verfügen wir nun über eine neue Migration von **addblograting** .</span><span class="sxs-lookup"><span data-stu-id="131a3-161">In the **Migrations** folder we now have a new **AddBlogRating** migration.</span></span> <span data-ttu-id="131a3-162">Der Dateiname der Migration ist mit einem Zeitstempel versehen, um die Reihenfolge zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="131a3-162">The migration filename is pre-fixed with a timestamp to help with ordering.</span></span> <span data-ttu-id="131a3-163">Wir bearbeiten den generierten Code, um den Standardwert 3 für Blog. Rating (Zeile 10 im folgenden Code) anzugeben.</span><span class="sxs-lookup"><span data-stu-id="131a3-163">Let’s edit the generated code to specify a default value of 3 for Blog.Rating (Line 10 in the code below)</span></span>

<span data-ttu-id="131a3-164">*Die Migration umfasst auch eine Code Behind-Datei, in der einige Metadaten erfasst werden. Diese Metadaten ermöglichen es Code First-Migrationen, die automatischen Migrationen zu replizieren, die wir vor dieser Code basierten Migration durchgeführt haben. Dies ist wichtig, wenn ein anderer Entwickler unsere Migrationen ausführen möchte oder wenn es an der Zeit ist, die Anwendung bereitzustellen.*</span><span class="sxs-lookup"><span data-stu-id="131a3-164">*The migration also has a code-behind file that captures some metadata. This metadata will allow Code First Migrations to replicate the automatic migrations we performed before this code-based migration. This is important if another developer wants to run our migrations or when it’s time to deploy our application.*</span></span>

``` csharp
    namespace MigrationsAutomaticDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogRating : DbMigration
        {
            public override void Up()
            {
                AddColumn("Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropColumn("Blogs", "Rating");
            }
        }
    }
```

<span data-ttu-id="131a3-165">Die Migration ist nun fertig bearbeitet. Aktualisieren Sie daher die Datenbank mit **Update-Database**.</span><span class="sxs-lookup"><span data-stu-id="131a3-165">Our edited migration is looking good, so let’s use **Update-Database** to bring the database up-to-date.</span></span>

-   <span data-ttu-id="131a3-166">Führen Sie den Befehl **Update-Database** in der Paket-Manager-Konsole aus.</span><span class="sxs-lookup"><span data-stu-id="131a3-166">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="back-to-automatic-migrations"></a><span data-ttu-id="131a3-167">Zurück zu automatischen Migrationen</span><span class="sxs-lookup"><span data-stu-id="131a3-167">Back to Automatic Migrations</span></span>

<span data-ttu-id="131a3-168">Wir können nun für unsere einfacheren Änderungen wieder zu automatischen Migrationen wechseln.</span><span class="sxs-lookup"><span data-stu-id="131a3-168">We are now free to switch back to automatic migrations for our simpler changes.</span></span> <span data-ttu-id="131a3-169">Code First-Migrationen übernimmt die automatische und Code basierte Migration in der richtigen Reihenfolge basierend auf den Metadaten, die Sie in der Code-Behind-Datei für jede Code basierte Migration speichert.</span><span class="sxs-lookup"><span data-stu-id="131a3-169">Code First Migrations will take care of performing the automatic and code-based migrations in the correct order based on the metadata it is storing in the code-behind file for each code-based migration.</span></span>

-   <span data-ttu-id="131a3-170">Fügen wir dem Modell eine Post. Abstract-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="131a3-170">Let’s add a Post.Abstract property to our model</span></span>

``` csharp
    public string Abstract { get; set; }
```

<span data-ttu-id="131a3-171">Nun können wir mit **Update-Database** Code First-Migrationen, diese Änderung mithilfe einer automatischen Migration an die Datenbank zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="131a3-171">Now we can use **Update-Database** to get Code First Migrations to push this change to the database using an automatic migration.</span></span>

-   <span data-ttu-id="131a3-172">Führen Sie den Befehl **Update-Database** in der Paket-Manager-Konsole aus.</span><span class="sxs-lookup"><span data-stu-id="131a3-172">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="summary"></a><span data-ttu-id="131a3-173">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="131a3-173">Summary</span></span>

<span data-ttu-id="131a3-174">In dieser exemplarischen Vorgehensweise wurde erläutert, wie automatische Migrationen verwendet werden, um Modelländerungen per Push an die Datenbank zu überführen</span><span class="sxs-lookup"><span data-stu-id="131a3-174">In this walkthrough you saw how to use automatic migrations to push model changes to the database.</span></span> <span data-ttu-id="131a3-175">Sie haben auch gesehen, wie Sie Code basierte Migrationen zwischen automatischen Migrationen aufbauen und ausführen können, wenn Sie mehr Kontrolle benötigen.</span><span class="sxs-lookup"><span data-stu-id="131a3-175">You also saw how to scaffold and run code-based migrations in between automatic migrations when you need more control.</span></span>
