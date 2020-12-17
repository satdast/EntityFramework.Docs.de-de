---
title: Asynchrone Abfrage und speichern-EF6
description: Asynchrone Abfrage und Speicherung in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/async
ms.openlocfilehash: 77204f56e4dca63322c8ae2e1117318262f16f83
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635730"
---
# <a name="async-query-and-save"></a><span data-ttu-id="5f9ba-103">Asynchrone Abfrage und Speicherung</span><span class="sxs-lookup"><span data-stu-id="5f9ba-103">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="5f9ba-104">**Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="5f9ba-105">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="5f9ba-106">EF6 hat Unterstützung für asynchrone Abfragen und speichern mithilfe der [Schlüsselwörter "Async" und "warten](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) " eingeführt, die in .NET 4,5 eingeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-106">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="5f9ba-107">Obwohl nicht alle Anwendungen von der Asynchronie profitieren können, kann es verwendet werden, um die Reaktionsfähigkeit von Clients und die Server Skalierbarkeit bei der Verarbeitung langer, Netzwerk-oder e/a-gebundener Aufgaben zu verbessern.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-107">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="5f9ba-108">Verwendung von Async</span><span class="sxs-lookup"><span data-stu-id="5f9ba-108">When to really use async</span></span>

<span data-ttu-id="5f9ba-109">In dieser exemplarischen Vorgehensweise wird erläutert, wie die asynchronen Konzepte in eine Weise eingeführt werden, die die Unterschiede zwischen der asynchronen und der synchronen Programmausführung leicht erkennen lässt.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-109">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="5f9ba-110">Diese exemplarische Vorgehensweise soll keines der wichtigen Szenarios veranschaulichen, in denen die asynchrone Programmierung Vorteile bietet.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-110">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="5f9ba-111">Die asynchrone Programmierung konzentriert sich hauptsächlich auf die Freigabe des aktuellen verwalteten Threads (Thread, der .NET-Code ausgeführt wird), um andere Aufgaben zu erledigen, während er auf einen Vorgang wartet, der keine computezeit von einem verwalteten Thread erfordert.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-111">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="5f9ba-112">Wenn die Datenbank-Engine z. b. eine Abfrage verarbeitet, gibt es keinen .NET-Code.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-112">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="5f9ba-113">In Client Anwendungen (WinForms, WPF usw.) kann der aktuelle Thread verwendet werden, damit die Benutzeroberfläche reaktionsfähig bleibt, während der asynchrone Vorgang ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-113">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="5f9ba-114">In Server Anwendungen (ASP.net usw.) kann der Thread verwendet werden, um andere eingehende Anforderungen zu verarbeiten. Hierdurch kann die Speicherauslastung reduziert und/oder der Durchsatz des Servers erhöht werden.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-114">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="5f9ba-115">In den meisten Anwendungen, die Async verwenden, haben Sie keinerlei spürbare Vorteile und können sogar schädlich sein.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-115">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="5f9ba-116">Verwenden Sie Tests, Profilerstellung und allgemeine Bedeutung, um die Auswirkungen von Async in Ihrem speziellen Szenario zu messen, bevor Sie einen Commit dafür durchführen.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-116">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="5f9ba-117">Hier finden Sie weitere Informationen zu Async:</span><span class="sxs-lookup"><span data-stu-id="5f9ba-117">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="5f9ba-118">Brandon Bray: Übersicht über Async/Erwartung in .NET 4,5</span><span class="sxs-lookup"><span data-stu-id="5f9ba-118">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   <span data-ttu-id="5f9ba-119">[Asynchrone Programmier](https://msdn.microsoft.com/library/hh191443.aspx) Seiten in der MSDN Library</span><span class="sxs-lookup"><span data-stu-id="5f9ba-119">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="5f9ba-120">[Erstellen von ASP.NET-Webanwendungen mithilfe von Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (enthält eine Demo zu erhöhtem Server Durchsatz)</span><span class="sxs-lookup"><span data-stu-id="5f9ba-120">[How to Build ASP.NET Web Applications Using Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="5f9ba-121">Erstellen des Modells</span><span class="sxs-lookup"><span data-stu-id="5f9ba-121">Create the model</span></span>

<span data-ttu-id="5f9ba-122">Wir verwenden den [Code First Workflow](xref:ef6/modeling/code-first/workflows/new-database) , um das Modell zu erstellen und die Datenbank zu generieren. die asynchronen Funktionen funktionieren jedoch mit allen EF-Modellen, einschließlich derjenigen, die mit dem EF-Designer erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-122">We’ll be using the [Code First workflow](xref:ef6/modeling/code-first/workflows/new-database) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="5f9ba-123">Erstellen Sie eine Konsolenanwendung, und nennen Sie Sie **AsyncDemo** .</span><span class="sxs-lookup"><span data-stu-id="5f9ba-123">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="5f9ba-124">Hinzufügen des nuget-Pakets "EntityFramework"</span><span class="sxs-lookup"><span data-stu-id="5f9ba-124">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="5f9ba-125">Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf das **AsyncDemo** -Projekt.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-125">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="5f9ba-126">Wählen Sie **nuget-Pakete verwalten... aus.**</span><span class="sxs-lookup"><span data-stu-id="5f9ba-126">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="5f9ba-127">Wählen Sie im Dialogfeld nuget-Pakete verwalten die Registerkarte **Online** aus, und wählen Sie das Paket **EntityFramework** aus.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-127">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="5f9ba-128">Klicken Sie auf **Install** (Installieren).</span><span class="sxs-lookup"><span data-stu-id="5f9ba-128">Click **Install**</span></span>
-   <span data-ttu-id="5f9ba-129">Fügen Sie eine **Model.cs** -Klasse mit der folgenden Implementierung hinzu.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-129">Add a **Model.cs** class with the following implementation</span></span>

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

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="5f9ba-130">Erstellen eines synchronen Programms</span><span class="sxs-lookup"><span data-stu-id="5f9ba-130">Create a synchronous program</span></span>

<span data-ttu-id="5f9ba-131">Nachdem wir nun über ein EF-Modell verfügen, schreiben wir Code, der ihn verwendet, um Daten Zugriffe auszuführen.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-131">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="5f9ba-132">Ersetzen Sie den Inhalt von **Program.cs** durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="5f9ba-132">Replace the contents of **Program.cs** with the following code</span></span>

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

<span data-ttu-id="5f9ba-133">Dieser Code Ruft die `PerformDatabaseOperations` -Methode auf, die einen neuen **Blog** in der Datenbank speichert und dann alle **Blogs** aus der Datenbank abruft und Sie in der **Konsole** ausgibt.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-133">This code calls the `PerformDatabaseOperations` method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="5f9ba-134">Danach schreibt das Programm ein Anführungszeichen in die **Konsole**.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-134">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="5f9ba-135">Da der Code synchron ist, können wir beim Ausführen des Programms den folgenden Ausführungs Ablauf beobachten:</span><span class="sxs-lookup"><span data-stu-id="5f9ba-135">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="5f9ba-136">`SaveChanges` beginnt, den neuen **Blog** an die Datenbank zu überbringen.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-136">`SaveChanges` begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="5f9ba-137">`SaveChanges` Bit</span><span class="sxs-lookup"><span data-stu-id="5f9ba-137">`SaveChanges` completes</span></span>
3.  <span data-ttu-id="5f9ba-138">Die Abfrage für alle **Blogs** wird an die Datenbank gesendet.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-138">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="5f9ba-139">Abfrage Rückgabe und Ergebnisse werden in die **Konsole** geschrieben</span><span class="sxs-lookup"><span data-stu-id="5f9ba-139">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="5f9ba-140">Das Anführungszeichen des Tages wird in die **Konsole** geschrieben.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-140">Quote of the day is written to **Console**</span></span>

![Ausgabe synchronisieren](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="5f9ba-142">Asynchrone Erstellung</span><span class="sxs-lookup"><span data-stu-id="5f9ba-142">Making it asynchronous</span></span>

<span data-ttu-id="5f9ba-143">Nachdem das Programm jetzt betriebsbereit ist, können wir mit der Verwendung der neuen Schlüsselwörter "Async" und "Erwartung" beginnen.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-143">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="5f9ba-144">Wir haben die folgenden Änderungen an Program.cs vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-144">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="5f9ba-145">Zeile 2: die using-Anweisung für den- `System.Data.Entity` Namespace ermöglicht uns den Zugriff auf die EF-Async-Erweiterungs Methoden.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-145">Line 2: The using statement for the `System.Data.Entity` namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="5f9ba-146">Zeile 4: mithilfe der using-Anweisung für den- `System.Threading.Tasks` Namespace können wir den- `Task` Typ verwenden.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-146">Line 4: The using statement for the `System.Threading.Tasks` namespace allows us to use the `Task` type.</span></span>
3.  <span data-ttu-id="5f9ba-147">Zeile 12 & 18: Wir erfassen als Task, der den Fortschritt von `PerformSomeDatabaseOperations` (Zeile 12) überwacht und dann die Ausführung des Programms blockiert, damit diese Aufgabe abgeschlossen ist, sobald die gesamte Arbeit für das Programm abgeschlossen ist (Zeile 18).</span><span class="sxs-lookup"><span data-stu-id="5f9ba-147">Line 12 & 18: We are capturing as task that monitors the progress of `PerformSomeDatabaseOperations` (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="5f9ba-148">Zeile 25: Wir aktualisieren `PerformSomeDatabaseOperations` , damit Sie als gekennzeichnet werden, `async` und geben zurück `Task` .</span><span class="sxs-lookup"><span data-stu-id="5f9ba-148">Line 25: We've update `PerformSomeDatabaseOperations` to be marked as `async` and return a `Task`.</span></span>
5.  <span data-ttu-id="5f9ba-149">Zeile 35: Wir rufen nun die asynchrone Version von auf und warten darauf, dass der Vorgang `SaveChanges` abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-149">Line 35: We're now calling the Async version of `SaveChanges` and awaiting it's completion.</span></span>
6.  <span data-ttu-id="5f9ba-150">Zeile 42: Wir rufen nun die asynchrone Version von `ToList` auf und warten auf das Ergebnis.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-150">Line 42: We're now calling the Async version of `ToList` and awaiting on the result.</span></span>

<span data-ttu-id="5f9ba-151">Eine umfassende Liste der verfügbaren Erweiterungs Methoden im- `System.Data.Entity` Namespace finden Sie in der- `QueryableExtensions` Klasse.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-151">For a comprehensive list of available extension methods in the `System.Data.Entity` namespace, refer to the `QueryableExtensions` class.</span></span> <span data-ttu-id="5f9ba-152">*Sie müssen auch `using System.Data.Entity` ihren using-Anweisungen hinzufügen.*</span><span class="sxs-lookup"><span data-stu-id="5f9ba-152">*You’ll also need to add `using System.Data.Entity` to your using statements.*</span></span>

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

<span data-ttu-id="5f9ba-153">Nun, da der Code asynchron ist, können wir einen anderen Ausführungs Fluss beobachten, wenn wir das Programm ausführen:</span><span class="sxs-lookup"><span data-stu-id="5f9ba-153">Now that the code is asynchronous, we can observe a different execution flow when we run the program:</span></span>

1. <span data-ttu-id="5f9ba-154">`SaveChanges` beginnt, den neuen **Blog** an die Datenbank zu überbringen.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-154">`SaveChanges` begins to push the new **Blog** to the database</span></span>  
    <span data-ttu-id="5f9ba-155">*Nachdem der Befehl an die Datenbank gesendet wurde, ist für den aktuell verwalteten Thread keine Compute-Zeit mehr erforderlich. Die `PerformDatabaseOperations` -Methode gibt zurück (obwohl die Ausführung noch nicht abgeschlossen ist), und der Programmfluss in der Main-Methode wird fortgesetzt.*</span><span class="sxs-lookup"><span data-stu-id="5f9ba-155">*Once the command is sent to the database no more compute time is needed on the current managed thread. The `PerformDatabaseOperations` method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2. <span data-ttu-id="5f9ba-156">**Das Anführungszeichen des Tages wird in die Konsole geschrieben.**</span><span class="sxs-lookup"><span data-stu-id="5f9ba-156">**Quote of the day is written to Console**</span></span>  
    <span data-ttu-id="5f9ba-157">*Da es in der Main-Methode keine weiteren Aufgaben gibt, wird der verwaltete Thread für den- `Wait` aufrufblock blockiert, bis der Daten Bank Vorgang abgeschlossen ist. Sobald der Vorgang abgeschlossen ist, wird der Rest des `PerformDatabaseOperations` ausgeführt.*</span><span class="sxs-lookup"><span data-stu-id="5f9ba-157">*Since there is no more work to do in the Main method, the managed thread is blocked on the `Wait` call until the database operation completes. Once it completes, the remainder of our `PerformDatabaseOperations` will be executed.*</span></span>
3.  <span data-ttu-id="5f9ba-158">`SaveChanges` Bit</span><span class="sxs-lookup"><span data-stu-id="5f9ba-158">`SaveChanges` completes</span></span>  
4.  <span data-ttu-id="5f9ba-159">Die Abfrage für alle **Blogs** wird an die Datenbank gesendet.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-159">Query for all **Blogs** is sent to the database</span></span>  
    <span data-ttu-id="5f9ba-160">*Auch hier kann der verwaltete Thread andere Aufgaben ausführen, während die Abfrage in der Datenbank verarbeitet wird. Da die Ausführung der anderen Ausführung abgeschlossen ist, hält der Thread den Wait-Befehl jedoch nur an.*</span><span class="sxs-lookup"><span data-stu-id="5f9ba-160">*Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="5f9ba-161">Abfrage Rückgabe und Ergebnisse werden in die **Konsole** geschrieben</span><span class="sxs-lookup"><span data-stu-id="5f9ba-161">Query returns and results are written to **Console**</span></span>  

![Asynchrone Ausgabe](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="5f9ba-163">Der Schluss</span><span class="sxs-lookup"><span data-stu-id="5f9ba-163">The takeaway</span></span>

<span data-ttu-id="5f9ba-164">Wir haben nun gesehen, wie einfach es ist, die asynchronen Methoden von EF zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-164">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="5f9ba-165">Obwohl die Vorteile von Async bei einer einfachen Konsolen-App möglicherweise nicht sehr offensichtlich sind, können dieselben Strategien in Situationen angewendet werden, in denen lange oder Netzwerk gebundene Aktivitäten die Anwendung andernfalls blockieren könnten oder eine große Anzahl von Threads bewirken, um den Speicherbedarf zu erhöhen.</span><span class="sxs-lookup"><span data-stu-id="5f9ba-165">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>
