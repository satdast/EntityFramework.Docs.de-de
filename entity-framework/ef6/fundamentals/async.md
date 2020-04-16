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
# <a name="async-query-and-save"></a>Async-Abfrage und Speichern
> [!NOTE]
> **Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.

EF6 hat die Unterstützung für asynchrone Abfragen und Speichern mithilfe der [Schlüsselwörter async und await](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) eingeführt, die in .NET 4.5 eingeführt wurden. Obwohl nicht alle Anwendungen von Asynchronität profitieren, kann sie verwendet werden, um die Clientreaktionsfähigkeit und Serverskalierbarkeit bei der Verarbeitung von Langlauf-, Netzwerk- oder E/A-gebundenen Aufgaben zu verbessern.

## <a name="when-to-really-use-async"></a>Wann sollte man wirklich async verwenden?

Der Zweck dieser exemplarischen Vorgehensweise besteht darin, die asynchronen Konzepte so einzuführen, dass der Unterschied zwischen asynchroner und synchroner Programmausführung leicht zu beobachten ist. Diese exemplarische Vorgehensweise dient nicht dazu, eines der wichtigsten Szenarien zu veranschaulichen, in denen die asynchrone Programmierung Vorteile bietet.

Die Async-Programmierung konzentriert sich in erster Linie auf die Freigabe des aktuellen verwalteten Threads (Thread, der .NET-Code ausführt), um andere Aufgaben auszuführen, während er auf einen Vorgang wartet, der keine Rechenzeit von einem verwalteten Thread erfordert. Während das Datenbankmodul beispielsweise eine Abfrage verarbeitet, ist von .NET-Code nichts zu tun.

In Clientanwendungen (WinForms, WPF usw.) kann der aktuelle Thread verwendet werden, um die Benutzeroberflächenreaktion zu halten, während der asynchrone Vorgang ausgeführt wird. In Serveranwendungen (ASP.NET usw.) kann der Thread verwendet werden, um andere eingehende Anforderungen zu verarbeiten – dies kann die Speichernutzung reduzieren und/oder den Durchsatz des Servers erhöhen.

In den meisten Anwendungen, die async verwenden, hat async keine spürbaren Vorteile und könnte sogar schädlich sein. Verwenden Sie Tests, Profilerstellung und gesunden Menschenverstand, um die Auswirkungen von async in Ihrem bestimmten Szenario zu messen, bevor Sie sich darauf verpflichten.

Hier sind einige weitere Ressourcen, um mehr über async zu erfahren:

-   [Brandon Brays Übersicht über async/await in .NET 4.5](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   [Asynchrone Programmierseiten](https://msdn.microsoft.com/library/hh191443.aspx) in der MSDN-Bibliothek
-   [Erstellen ASP.NET Webanwendungen mit Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (enthält eine Demo mit erhöhtem Serverdurchsatz)

## <a name="create-the-model"></a>Erstellen des Modells

Wir verwenden den [Code First-Workflow,](~/ef6/modeling/code-first/workflows/new-database.md) um unser Modell zu erstellen und die Datenbank zu generieren, jedoch funktioniert die asynchrone Funktionalität mit allen EF-Modellen, einschließlich der mit dem EF-Designer erstellten.

-   Erstellen einer Konsolenanwendung und Aufrufen **von AsyncDemo**
-   Hinzufügen des EntityFramework NuGet-Pakets
    -   Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das **AsyncDemo-Projekt**
    -   **NuGet-Pakete verwalten...**
    -   Wählen Sie im Dialogfeld NuGet-Pakete verwalten die Registerkarte **Online** aus, und wählen Sie das **EntityFramework-Paket** aus.
    -   Klicken Sie auf **Installieren**
-   Hinzufügen einer **Model.cs** Klasse mit der folgenden Implementierung

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

Nachdem wir nun über ein EF-Modell verfügen, schreiben wir Code, der es zum Ausführen eines Datenzugriffs verwendet.

-   Ersetzen Sie den Inhalt von **Program.cs** durch den folgenden Code

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

Dieser Code ruft die **PerformDatabaseOperations-Methode** auf, die einen neuen **Blog** in der Datenbank speichert und dann alle **Blogs** aus der Datenbank abruft und in die **Konsole**druckt. Danach schreibt das Programm ein Zitat des Tages in die **Konsole**.

Da der Code synchron ist, können wir den folgenden Ausführungsfluss beobachten, wenn wir das Programm ausführen:

1.  **SaveChanges** beginnt, den neuen **Blog** in die Datenbank zu übertragen
2.  **SaveChanges** abgeschlossen
3.  Abfrage für alle **Blogs** wird an die Datenbank gesendet
4.  Abfragerückgaben und Ergebnisse werden in **die Konsole** geschrieben
5.  Zitat des Tages wird in **Konsole** geschrieben

![Sync-Ausgabe](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>Asynchron machen

Nun, da wir unser Programm gestartet haben, können wir beginnen, die neuen async und warten Keywords zu nutzen. Wir haben die folgenden Änderungen an Program.cs

1.  Zeile 2: Die using-Anweisung für den **Namespace System.Data.Entity** gibt uns Zugriff auf die EF-async-Erweiterungsmethoden.
2.  Zeile 4: Die using-Anweisung für den **Namespace System.Threading.Tasks** ermöglicht es uns, den **Task-Typ** zu verwenden.
3.  Zeile 12 & 18: Wir erfassen als Aufgabe, die den Fortschritt von **PerformSomeDatabaseOperations** (Zeile 12) überwacht, und blockieren dann die Programmausführung für diese Aufgabe, sobald alle Arbeit für das Programm erledigt ist (Zeile 18).
4.  Zeile 25: Wir haben **PerformSomeDatabaseOperations** aktualisiert, um als **async** markiert zu werden und eine **Aufgabe**zurückzugeben.
5.  Zeile 35: Wir rufen jetzt die Async-Version von SaveChanges auf und warten auf ihre Fertigstellung.
6.  Zeile 42: Wir rufen jetzt die Async-Version von ToList auf und warten auf das Ergebnis.

Eine umfassende Liste der verfügbaren Erweiterungsmethoden im Namespace System.Data.Entity finden Sie in der QueryableExtensions-Klasse. *Außerdem müssen Sie Ihren Usinger Anweisungen "using System.Data.Entity" hinzufügen.*

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

Da der Code nun asynchron ist, können wir einen anderen Ausführungsfluss beobachten, wenn wir das Programm ausführen:

1. **SaveChanges** beginnt, den neuen **Blog** in die Datenbank zu übertragen  
    *Sobald der Befehl an die Datenbank gesendet wurde, ist für den aktuellen verwalteten Thread keine Rechenzeit mehr erforderlich. Die **PerformDatabaseOperations-Methode** gibt zurück (obwohl die Ausführung noch nicht abgeschlossen ist), und der Programmfluss in der Main-Methode wird fortgesetzt.*
2. **Zitat des Tages wird in Konsole geschrieben**  
    *Da in der Main-Methode keine Arbeit mehr zu erledigen ist, wird der verwaltete Thread beim Wait-Aufruf blockiert, bis der Datenbankvorgang abgeschlossen ist. Sobald es abgeschlossen ist, wird der Rest unserer **PerformDatabaseOperations** ausgeführt.*
3.  **SaveChanges** abgeschlossen  
4.  Abfrage für alle **Blogs** wird an die Datenbank gesendet  
    *Auch hier kann der verwaltete Thread andere Arbeiten erledigen, während die Abfrage in der Datenbank verarbeitet wird. Da alle anderen Ausführungen abgeschlossen sind, wird der Thread jedoch nur beim Wait-Aufruf angehalten.*
5.  Abfragerückgaben und Ergebnisse werden in **die Konsole** geschrieben  

![Async-Ausgabe](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>Das Mitnehmen

Wir haben nun gesehen, wie einfach es ist, die asynchronen Methoden von EF zu nutzen. Obwohl die Vorteile von async bei einer einfachen Konsolen-App möglicherweise nicht sehr offensichtlich sind, können dieselben Strategien in Situationen angewendet werden, in denen lang andauernde oder netzwerkgebundene Aktivitäten die Anwendung anderweitig blockieren oder dazu führen könnten, dass eine große Anzahl von Threads den Speicherbedarf erhöht.
