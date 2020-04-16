---
title: Async-Abfrage und Speichern - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: a001df726b7cd3342fcca566c1373f452811d07d
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434239"
---
# <a name="async-query-and-save"></a><span data-ttu-id="9bac0-102">Async-Abfrage und Speichern</span><span class="sxs-lookup"><span data-stu-id="9bac0-102">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="9bac0-103">**Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="9bac0-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="9bac0-104">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="9bac0-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="9bac0-105">EF6 hat die Unterstützung für asynchrone Abfragen und Speichern mithilfe der [Schlüsselwörter async und await](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) eingeführt, die in .NET 4.5 eingeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="9bac0-105">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="9bac0-106">Obwohl nicht alle Anwendungen von Asynchronität profitieren, kann sie verwendet werden, um die Clientreaktionsfähigkeit und Serverskalierbarkeit bei der Verarbeitung von Langlauf-, Netzwerk- oder E/A-gebundenen Aufgaben zu verbessern.</span><span class="sxs-lookup"><span data-stu-id="9bac0-106">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="9bac0-107">Wann sollte man wirklich async verwenden?</span><span class="sxs-lookup"><span data-stu-id="9bac0-107">When to really use async</span></span>

<span data-ttu-id="9bac0-108">Der Zweck dieser exemplarischen Vorgehensweise besteht darin, die asynchronen Konzepte so einzuführen, dass der Unterschied zwischen asynchroner und synchroner Programmausführung leicht zu beobachten ist.</span><span class="sxs-lookup"><span data-stu-id="9bac0-108">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="9bac0-109">Diese exemplarische Vorgehensweise dient nicht dazu, eines der wichtigsten Szenarien zu veranschaulichen, in denen die asynchrone Programmierung Vorteile bietet.</span><span class="sxs-lookup"><span data-stu-id="9bac0-109">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="9bac0-110">Die Async-Programmierung konzentriert sich in erster Linie auf die Freigabe des aktuellen verwalteten Threads (Thread, der .NET-Code ausführt), um andere Aufgaben auszuführen, während er auf einen Vorgang wartet, der keine Rechenzeit von einem verwalteten Thread erfordert.</span><span class="sxs-lookup"><span data-stu-id="9bac0-110">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="9bac0-111">Während das Datenbankmodul beispielsweise eine Abfrage verarbeitet, ist von .NET-Code nichts zu tun.</span><span class="sxs-lookup"><span data-stu-id="9bac0-111">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="9bac0-112">In Clientanwendungen (WinForms, WPF usw.) kann der aktuelle Thread verwendet werden, um die Benutzeroberflächenreaktion zu halten, während der asynchrone Vorgang ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="9bac0-112">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="9bac0-113">In Serveranwendungen (ASP.NET usw.) kann der Thread verwendet werden, um andere eingehende Anforderungen zu verarbeiten – dies kann die Speichernutzung reduzieren und/oder den Durchsatz des Servers erhöhen.</span><span class="sxs-lookup"><span data-stu-id="9bac0-113">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="9bac0-114">In den meisten Anwendungen, die async verwenden, hat async keine spürbaren Vorteile und könnte sogar schädlich sein.</span><span class="sxs-lookup"><span data-stu-id="9bac0-114">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="9bac0-115">Verwenden Sie Tests, Profilerstellung und gesunden Menschenverstand, um die Auswirkungen von async in Ihrem bestimmten Szenario zu messen, bevor Sie sich darauf verpflichten.</span><span class="sxs-lookup"><span data-stu-id="9bac0-115">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="9bac0-116">Hier sind einige weitere Ressourcen, um mehr über async zu erfahren:</span><span class="sxs-lookup"><span data-stu-id="9bac0-116">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="9bac0-117">Brandon Brays Übersicht über async/await in .NET 4.5</span><span class="sxs-lookup"><span data-stu-id="9bac0-117">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   <span data-ttu-id="9bac0-118">[Asynchrone Programmierseiten](https://msdn.microsoft.com/library/hh191443.aspx) in der MSDN-Bibliothek</span><span class="sxs-lookup"><span data-stu-id="9bac0-118">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="9bac0-119">[Erstellen ASP.NET Webanwendungen mit Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (enthält eine Demo mit erhöhtem Serverdurchsatz)</span><span class="sxs-lookup"><span data-stu-id="9bac0-119">[How to Build ASP.NET Web Applications Using Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="9bac0-120">Erstellen des Modells</span><span class="sxs-lookup"><span data-stu-id="9bac0-120">Create the model</span></span>

<span data-ttu-id="9bac0-121">Wir verwenden den [Code First-Workflow,](~/ef6/modeling/code-first/workflows/new-database.md) um unser Modell zu erstellen und die Datenbank zu generieren, jedoch funktioniert die asynchrone Funktionalität mit allen EF-Modellen, einschließlich der mit dem EF-Designer erstellten.</span><span class="sxs-lookup"><span data-stu-id="9bac0-121">We’ll be using the [Code First workflow](~/ef6/modeling/code-first/workflows/new-database.md) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="9bac0-122">Erstellen einer Konsolenanwendung und Aufrufen **von AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="9bac0-122">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="9bac0-123">Hinzufügen des EntityFramework NuGet-Pakets</span><span class="sxs-lookup"><span data-stu-id="9bac0-123">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="9bac0-124">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das **AsyncDemo-Projekt**</span><span class="sxs-lookup"><span data-stu-id="9bac0-124">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="9bac0-125">**NuGet-Pakete verwalten...**</span><span class="sxs-lookup"><span data-stu-id="9bac0-125">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="9bac0-126">Wählen Sie im Dialogfeld NuGet-Pakete verwalten die Registerkarte **Online** aus, und wählen Sie das **EntityFramework-Paket** aus.</span><span class="sxs-lookup"><span data-stu-id="9bac0-126">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="9bac0-127">Klicken Sie auf **Installieren**</span><span class="sxs-lookup"><span data-stu-id="9bac0-127">Click **Install**</span></span>
-   <span data-ttu-id="9bac0-128">Hinzufügen einer **Model.cs** Klasse mit der folgenden Implementierung</span><span class="sxs-lookup"><span data-stu-id="9bac0-128">Add a **Model.cs** class with the following implementation</span></span>

``` csharp
    using System.Collections.Generic;
    using System.Data.Entity;

    namespace AsyncDemo
    {
        public class BloggingContext : DbContext
        {
            public DbSet<Blog> Blogs { get; set; }
            public DbSet<Post> Posts { get; set; }
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
    }
```

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="9bac0-129">Erstellen eines synchronen Programms</span><span class="sxs-lookup"><span data-stu-id="9bac0-129">Create a synchronous program</span></span>

<span data-ttu-id="9bac0-130">Nachdem wir nun über ein EF-Modell verfügen, schreiben wir Code, der es zum Ausführen eines Datenzugriffs verwendet.</span><span class="sxs-lookup"><span data-stu-id="9bac0-130">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="9bac0-131">Ersetzen Sie den Inhalt von **Program.cs** durch den folgenden Code</span><span class="sxs-lookup"><span data-stu-id="9bac0-131">Replace the contents of **Program.cs** with the following code</span></span>

``` csharp
    using System;
    using System.Linq;

    namespace AsyncDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                PerformDatabaseOperations();

                Console.WriteLine("Quote of the day");
                Console.WriteLine(" Don't worry about the world coming to an end today... ");
                Console.WriteLine(" It's already tomorrow in Australia.");

                Console.WriteLine();
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            public static void PerformDatabaseOperations()
            {
                using (var db = new BloggingContext())
                {
                    // Create a new blog and save it
                    db.Blogs.Add(new Blog
                    {
                        Name = "Test Blog #" + (db.Blogs.Count() + 1)
                    });
                    Console.WriteLine("Calling SaveChanges.");
                    db.SaveChanges();
                    Console.WriteLine("SaveChanges completed.");

                    // Query for all blogs ordered by name
                    Console.WriteLine("Executing query.");
                    var blogs = (from b in db.Blogs
                                orderby b.Name
                                select b).ToList();

                    // Write all blogs out to Console
                    Console.WriteLine("Query completed with following results:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" " + blog.Name);
                    }
                }
            }
        }
    }
```

<span data-ttu-id="9bac0-132">Dieser Code ruft die **PerformDatabaseOperations-Methode** auf, die einen neuen **Blog** in der Datenbank speichert und dann alle **Blogs** aus der Datenbank abruft und in die **Konsole**druckt.</span><span class="sxs-lookup"><span data-stu-id="9bac0-132">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="9bac0-133">Danach schreibt das Programm ein Zitat des Tages in die **Konsole**.</span><span class="sxs-lookup"><span data-stu-id="9bac0-133">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="9bac0-134">Da der Code synchron ist, können wir den folgenden Ausführungsfluss beobachten, wenn wir das Programm ausführen:</span><span class="sxs-lookup"><span data-stu-id="9bac0-134">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="9bac0-135">**SaveChanges** beginnt, den neuen **Blog** in die Datenbank zu übertragen</span><span class="sxs-lookup"><span data-stu-id="9bac0-135">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="9bac0-136">**SaveChanges** abgeschlossen</span><span class="sxs-lookup"><span data-stu-id="9bac0-136">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="9bac0-137">Abfrage für alle **Blogs** wird an die Datenbank gesendet</span><span class="sxs-lookup"><span data-stu-id="9bac0-137">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="9bac0-138">Abfragerückgaben und Ergebnisse werden in **die Konsole** geschrieben</span><span class="sxs-lookup"><span data-stu-id="9bac0-138">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="9bac0-139">Zitat des Tages wird in **Konsole** geschrieben</span><span class="sxs-lookup"><span data-stu-id="9bac0-139">Quote of the day is written to **Console**</span></span>

![Sync-Ausgabe](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="9bac0-141">Asynchron machen</span><span class="sxs-lookup"><span data-stu-id="9bac0-141">Making it asynchronous</span></span>

<span data-ttu-id="9bac0-142">Nun, da wir unser Programm gestartet haben, können wir beginnen, die neuen async und warten Keywords zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="9bac0-142">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="9bac0-143">Wir haben die folgenden Änderungen an Program.cs</span><span class="sxs-lookup"><span data-stu-id="9bac0-143">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="9bac0-144">Zeile 2: Die using-Anweisung für den **Namespace System.Data.Entity** gibt uns Zugriff auf die EF-async-Erweiterungsmethoden.</span><span class="sxs-lookup"><span data-stu-id="9bac0-144">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="9bac0-145">Zeile 4: Die using-Anweisung für den **Namespace System.Threading.Tasks** ermöglicht es uns, den **Task-Typ** zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="9bac0-145">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="9bac0-146">Zeile 12 & 18: Wir erfassen als Aufgabe, die den Fortschritt von **PerformSomeDatabaseOperations** (Zeile 12) überwacht, und blockieren dann die Programmausführung für diese Aufgabe, sobald alle Arbeit für das Programm erledigt ist (Zeile 18).</span><span class="sxs-lookup"><span data-stu-id="9bac0-146">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="9bac0-147">Zeile 25: Wir haben **PerformSomeDatabaseOperations** aktualisiert, um als **async** markiert zu werden und eine **Aufgabe**zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="9bac0-147">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="9bac0-148">Zeile 35: Wir rufen jetzt die Async-Version von SaveChanges auf und warten auf ihre Fertigstellung.</span><span class="sxs-lookup"><span data-stu-id="9bac0-148">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="9bac0-149">Zeile 42: Wir rufen jetzt die Async-Version von ToList auf und warten auf das Ergebnis.</span><span class="sxs-lookup"><span data-stu-id="9bac0-149">Line 42: We're now calling the Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="9bac0-150">Eine umfassende Liste der verfügbaren Erweiterungsmethoden im Namespace System.Data.Entity finden Sie in der QueryableExtensions-Klasse.</span><span class="sxs-lookup"><span data-stu-id="9bac0-150">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="9bac0-151">*Außerdem müssen Sie Ihren Usinger Anweisungen "using System.Data.Entity" hinzufügen.*</span><span class="sxs-lookup"><span data-stu-id="9bac0-151">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

``` csharp
    using System;
    using System.Data.Entity;
    using System.Linq;
    using System.Threading.Tasks;

    namespace AsyncDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                var task = PerformDatabaseOperations();

                Console.WriteLine("Quote of the day");
                Console.WriteLine(" Don't worry about the world coming to an end today... ");
                Console.WriteLine(" It's already tomorrow in Australia.");

                task.Wait();

                Console.WriteLine();
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            public static async Task PerformDatabaseOperations()
            {
                using (var db = new BloggingContext())
                {
                    // Create a new blog and save it
                    db.Blogs.Add(new Blog
                    {
                        Name = "Test Blog #" + (db.Blogs.Count() + 1)
                    });
                    Console.WriteLine("Calling SaveChanges.");
                    await db.SaveChangesAsync();
                    Console.WriteLine("SaveChanges completed.");

                    // Query for all blogs ordered by name
                    Console.WriteLine("Executing query.");
                    var blogs = await (from b in db.Blogs
                                orderby b.Name
                                select b).ToListAsync();

                    // Write all blogs out to Console
                    Console.WriteLine("Query completed with following results:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" - " + blog.Name);
                    }
                }
            }
        }
    }
```

<span data-ttu-id="9bac0-152">Da der Code nun asynchron ist, können wir einen anderen Ausführungsfluss beobachten, wenn wir das Programm ausführen:</span><span class="sxs-lookup"><span data-stu-id="9bac0-152">Now that the code is asynchronous, we can observe a different execution flow when we run the program:</span></span>

1. <span data-ttu-id="9bac0-153">**SaveChanges** beginnt, den neuen **Blog** in die Datenbank zu übertragen</span><span class="sxs-lookup"><span data-stu-id="9bac0-153">**SaveChanges** begins to push the new **Blog** to the database</span></span>  
    <span data-ttu-id="9bac0-154">*Sobald der Befehl an die Datenbank gesendet wurde, ist für den aktuellen verwalteten Thread keine Rechenzeit mehr erforderlich. Die **PerformDatabaseOperations-Methode** gibt zurück (obwohl die Ausführung noch nicht abgeschlossen ist), und der Programmfluss in der Main-Methode wird fortgesetzt.*</span><span class="sxs-lookup"><span data-stu-id="9bac0-154">*Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2. <span data-ttu-id="9bac0-155">**Zitat des Tages wird in Konsole geschrieben**</span><span class="sxs-lookup"><span data-stu-id="9bac0-155">**Quote of the day is written to Console**</span></span>  
    <span data-ttu-id="9bac0-156">*Da in der Main-Methode keine Arbeit mehr zu erledigen ist, wird der verwaltete Thread beim Wait-Aufruf blockiert, bis der Datenbankvorgang abgeschlossen ist. Sobald es abgeschlossen ist, wird der Rest unserer **PerformDatabaseOperations** ausgeführt.*</span><span class="sxs-lookup"><span data-stu-id="9bac0-156">*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations** will be executed.*</span></span>
3.  <span data-ttu-id="9bac0-157">**SaveChanges** abgeschlossen</span><span class="sxs-lookup"><span data-stu-id="9bac0-157">**SaveChanges** completes</span></span>  
4.  <span data-ttu-id="9bac0-158">Abfrage für alle **Blogs** wird an die Datenbank gesendet</span><span class="sxs-lookup"><span data-stu-id="9bac0-158">Query for all **Blogs** is sent to the database</span></span>  
    <span data-ttu-id="9bac0-159">*Auch hier kann der verwaltete Thread andere Arbeiten erledigen, während die Abfrage in der Datenbank verarbeitet wird. Da alle anderen Ausführungen abgeschlossen sind, wird der Thread jedoch nur beim Wait-Aufruf angehalten.*</span><span class="sxs-lookup"><span data-stu-id="9bac0-159">*Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="9bac0-160">Abfragerückgaben und Ergebnisse werden in **die Konsole** geschrieben</span><span class="sxs-lookup"><span data-stu-id="9bac0-160">Query returns and results are written to **Console**</span></span>  

![Async-Ausgabe](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="9bac0-162">Das Mitnehmen</span><span class="sxs-lookup"><span data-stu-id="9bac0-162">The takeaway</span></span>

<span data-ttu-id="9bac0-163">Wir haben nun gesehen, wie einfach es ist, die asynchronen Methoden von EF zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="9bac0-163">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="9bac0-164">Obwohl die Vorteile von async bei einer einfachen Konsolen-App möglicherweise nicht sehr offensichtlich sind, können dieselben Strategien in Situationen angewendet werden, in denen lang andauernde oder netzwerkgebundene Aktivitäten die Anwendung anderweitig blockieren oder dazu führen könnten, dass eine große Anzahl von Threads den Speicherbedarf erhöht.</span><span class="sxs-lookup"><span data-stu-id="9bac0-164">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>
