---
title: Asynchrone Abfrage und speichern-EF6
description: Asynchrone Abfrage und Speicherung in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/async
ms.openlocfilehash: 2b5f6f868cbf2e0699a943cf68c8568550f4ba36
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063399"
---
# <a name="async-query-and-save"></a>Asynchrone Abfrage und Speicherung
> [!NOTE]
> **Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.

EF6 hat Unterstützung für asynchrone Abfragen und speichern mithilfe der [Schlüsselwörter "Async" und "warten](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) " eingeführt, die in .NET 4,5 eingeführt wurden. Obwohl nicht alle Anwendungen von der Asynchronie profitieren können, kann es verwendet werden, um die Reaktionsfähigkeit von Clients und die Server Skalierbarkeit bei der Verarbeitung langer, Netzwerk-oder e/a-gebundener Aufgaben zu verbessern.

## <a name="when-to-really-use-async"></a>Verwendung von Async

In dieser exemplarischen Vorgehensweise wird erläutert, wie die asynchronen Konzepte in eine Weise eingeführt werden, die die Unterschiede zwischen der asynchronen und der synchronen Programmausführung leicht erkennen lässt. Diese exemplarische Vorgehensweise soll keines der wichtigen Szenarios veranschaulichen, in denen die asynchrone Programmierung Vorteile bietet.

Die asynchrone Programmierung konzentriert sich hauptsächlich auf die Freigabe des aktuellen verwalteten Threads (Thread, der .NET-Code ausgeführt wird), um andere Aufgaben zu erledigen, während er auf einen Vorgang wartet, der keine computezeit von einem verwalteten Thread erfordert. Wenn die Datenbank-Engine z. b. eine Abfrage verarbeitet, gibt es keinen .NET-Code.

In Client Anwendungen (WinForms, WPF usw.) kann der aktuelle Thread verwendet werden, damit die Benutzeroberfläche reaktionsfähig bleibt, während der asynchrone Vorgang ausgeführt wird. In Server Anwendungen (ASP.net usw.) kann der Thread verwendet werden, um andere eingehende Anforderungen zu verarbeiten. Hierdurch kann die Speicherauslastung reduziert und/oder der Durchsatz des Servers erhöht werden.

In den meisten Anwendungen, die Async verwenden, haben Sie keinerlei spürbare Vorteile und können sogar schädlich sein. Verwenden Sie Tests, Profilerstellung und allgemeine Bedeutung, um die Auswirkungen von Async in Ihrem speziellen Szenario zu messen, bevor Sie einen Commit dafür durchführen.

Hier finden Sie weitere Informationen zu Async:

-   [Brandon Bray: Übersicht über Async/Erwartung in .NET 4,5](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   [Asynchrone Programmier](https://msdn.microsoft.com/library/hh191443.aspx) Seiten in der MSDN Library
-   [Erstellen von ASP.NET-Webanwendungen mithilfe von Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (enthält eine Demo zu erhöhtem Server Durchsatz)

## <a name="create-the-model"></a>Erstellen des Modells

Wir verwenden den [Code First Workflow](xref:ef6/modeling/code-first/workflows/new-database) , um das Modell zu erstellen und die Datenbank zu generieren. die asynchronen Funktionen funktionieren jedoch mit allen EF-Modellen, einschließlich derjenigen, die mit dem EF-Designer erstellt werden.

-   Erstellen Sie eine Konsolenanwendung, und nennen Sie Sie **AsyncDemo** .
-   Hinzufügen des nuget-Pakets "EntityFramework"
    -   Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf das **AsyncDemo** -Projekt.
    -   Wählen Sie **nuget-Pakete verwalten... aus.**
    -   Wählen Sie im Dialogfeld nuget-Pakete verwalten die Registerkarte **Online** aus, und wählen Sie das Paket **EntityFramework** aus.
    -   Klicken Sie auf **Install** (Installieren).
-   Fügen Sie eine **Model.cs** -Klasse mit der folgenden Implementierung hinzu.

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

 

## <a name="create-a-synchronous-program"></a>Erstellen eines synchronen Programms

Nachdem wir nun über ein EF-Modell verfügen, schreiben wir Code, der ihn verwendet, um Daten Zugriffe auszuführen.

-   Ersetzen Sie den Inhalt von **Program.cs** durch den folgenden Code:

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

Dieser Code Ruft die **performdatabaseoperations** -Methode auf, die einen neuen **Blog** in der Datenbank speichert und dann alle **Blogs** aus der Datenbank abruft und Sie in der **Konsole**ausgibt. Danach schreibt das Programm ein Anführungszeichen in die **Konsole**.

Da der Code synchron ist, können wir beim Ausführen des Programms den folgenden Ausführungs Ablauf beobachten:

1.  **SaveChanges** beginnt, den neuen **Blog** an die Datenbank zu überbringen.
2.  **SaveChanges** ist abgeschlossen.
3.  Die Abfrage für alle **Blogs** wird an die Datenbank gesendet.
4.  Abfrage Rückgabe und Ergebnisse werden in die **Konsole** geschrieben
5.  Das Anführungszeichen des Tages wird in die **Konsole** geschrieben.

![Ausgabe synchronisieren](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>Asynchrone Erstellung

Nachdem das Programm jetzt betriebsbereit ist, können wir mit der Verwendung der neuen Schlüsselwörter "Async" und "Erwartung" beginnen. Wir haben die folgenden Änderungen an Program.cs vorgenommen.

1.  Zeile 2: die using-Anweisung für den **System. Data. Entity** -Namespace ermöglicht uns den Zugriff auf die EF-Async-Erweiterungs Methoden.
2.  Zeile 4: mithilfe der using-Anweisung für den **System. Threading. Tasks** -Namespace können wir den **Tasktyp verwenden** .
3.  Zeile 12 & 18: Wir erfassen als Task, der den Status von **performsomedatabaseoperations** (Zeile 12) überwacht und dann die Ausführung des Programms blockiert, damit diese Aufgabe abgeschlossen wird, sobald die gesamte Arbeit für das Programm abgeschlossen ist (Zeile 18).
4.  Zeile 25: Wir haben " **performsomedatabaseoperations** " so aktualisiert, dass Sie als " **Async** " gekennzeichnet sind und eine **Aufgabe**zurückgeben.
5.  Zeile 35: Wir rufen nun die asynchrone Version von SaveChanges auf und warten darauf, dass der Vorgang abgeschlossen ist.
6.  Zeile 42: Wir rufen jetzt die Async-Version von ToList auf und warten auf das Ergebnis.

Eine umfassende Liste der verfügbaren Erweiterungs Methoden im System. Data. Entity-Namespace finden Sie in der queryableextensions-Klasse. *Sie müssen auch "using System. Data. Entity" zu ihren using-Anweisungen hinzufügen.*

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

Nun, da der Code asynchron ist, können wir einen anderen Ausführungs Fluss beobachten, wenn wir das Programm ausführen:

1. **SaveChanges** beginnt, den neuen **Blog** an die Datenbank zu überbringen.  
    *Nachdem der Befehl an die Datenbank gesendet wurde, ist für den aktuell verwalteten Thread keine Compute-Zeit mehr erforderlich. Die **performdatabaseoperations** -Methode gibt zurück (obwohl die Ausführung noch nicht abgeschlossen ist), und der Programmablauf in der Main-Methode wird fortgesetzt.*
2. **Das Anführungszeichen des Tages wird in die Konsole geschrieben.**  
    *Da keine weiteren Aufgaben in der Main-Methode durchgeführt werden müssen, wird der verwaltete Thread für den Wait-Vorgang blockiert, bis der Daten Bank Vorgang abgeschlossen ist. Sobald der Vorgang abgeschlossen ist, werden die restlichen **performdatabaseoperations** ausgeführt.*
3.  **SaveChanges** ist abgeschlossen.  
4.  Die Abfrage für alle **Blogs** wird an die Datenbank gesendet.  
    *Auch hier kann der verwaltete Thread andere Aufgaben ausführen, während die Abfrage in der Datenbank verarbeitet wird. Da die Ausführung der anderen Ausführung abgeschlossen ist, hält der Thread den Wait-Befehl jedoch nur an.*
5.  Abfrage Rückgabe und Ergebnisse werden in die **Konsole** geschrieben  

![Asynchrone Ausgabe](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>Der Schluss

Wir haben nun gesehen, wie einfach es ist, die asynchronen Methoden von EF zu verwenden. Obwohl die Vorteile von Async bei einer einfachen Konsolen-App möglicherweise nicht sehr offensichtlich sind, können dieselben Strategien in Situationen angewendet werden, in denen lange oder Netzwerk gebundene Aktivitäten die Anwendung andernfalls blockieren könnten oder eine große Anzahl von Threads bewirken, um den Speicherbedarf zu erhöhen.
