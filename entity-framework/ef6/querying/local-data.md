---
title: Lokale Daten EF6
description: Lokale Daten in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/querying/local-data
ms.openlocfilehash: 5763424ab295ed6ceec655368fe3565a10c911d3
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073911"
---
# <a name="local-data"></a>Lokale Daten
Wenn Sie eine LINQ-Abfrage direkt für ein dbset ausführen, wird immer eine Abfrage an die Datenbank gesendet. Sie können jedoch auf die Daten zugreifen, die derzeit im Arbeitsspeicher vorhanden sind, indem Sie die dbset. Local-Eigenschaft verwenden. Sie können auch auf die zusätzlichen Informationen zugreifen, die EF über die Entitäten nachverfolgt, indem Sie die Methoden dbcontext. Entry und dbcontext. ChangeTracker. Entries verwenden. Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.  

## <a name="using-local-to-look-at-local-data"></a>Verwenden von local, um lokale Daten zu untersuchen  

Die Local-Eigenschaft von dbset bietet einfachen Zugriff auf die Entitäten der Menge, die zurzeit vom Kontext nachverfolgt werden und nicht als gelöscht markiert wurden. Der Zugriff auf die lokale Eigenschaft bewirkt nie, dass eine Abfrage an die Datenbank gesendet wird. Dies bedeutet, dass Sie normalerweise verwendet wird, wenn bereits eine Abfrage durchgeführt wurde. Die Load-Erweiterungsmethode kann verwendet werden, um eine Abfrage auszuführen, sodass der Kontext die Ergebnisse nachverfolgt. Beispiel:  

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

Wenn wir zwei Blogs in der Datenbank haben: "ADO.net Blog" with a BlogId of 1 und "The Visual Studio Blog" with a BlogId of 2-Wir könnten die folgende Ausgabe erwarten:  

```console
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

Dies veranschaulicht drei Punkte:  

- Der neue Blog "Mein neuer Blog" ist in der lokalen Sammlung enthalten, obwohl er noch nicht in der Datenbank gespeichert wurde. In diesem Blog ist der Primärschlüssel 0 (null), da die Datenbank noch keinen echten Schlüssel für die Entität generiert hat.  
- Der ADO.net-Blog ist nicht in der lokalen Auflistung enthalten, obwohl er noch vom Kontext nachverfolgt wird. Der Grund hierfür ist, dass wir ihn aus dem dbset entfernt haben und ihn als gelöscht markieren.  
- Wenn dbset zum Ausführen einer Abfrage verwendet wird, ist der zum Löschen markierte Blog (ADO.net-Blog) in den Ergebnissen enthalten, und der neue Blog (mein neuer Blog), der noch nicht in der Datenbank gespeichert wurde, ist nicht in den Ergebnissen enthalten. Dies liegt daran, dass dbset eine Abfrage für die Datenbank ausführt und die zurückgegebenen Ergebnisse immer den Inhalt der Datenbank widerspiegeln.  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a>Verwenden von local zum Hinzufügen und Entfernen von Entitäten aus dem Kontext  

Die Local-Eigenschaft in dbset gibt eine [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) mit gehockten Ereignissen zurück, sodass Sie mit dem Inhalt des Kontexts synchron bleibt. Dies bedeutet, dass Entitäten entweder der lokalen Auflistung oder dem dbset hinzugefügt oder daraus entfernt werden können. Dies bedeutet auch, dass Abfragen, die neue Entitäten in den Kontext bringen, dazu führen, dass die lokale Sammlung mit diesen Entitäten aktualisiert wird. Beispiel:  

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

Wenn einige Beiträge mit "Entity-Framework" und "ASP.net" gekennzeichnet sind, sieht die Ausgabe in etwa wie folgt aus:  

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

Dies veranschaulicht drei Punkte:  

- Der neue Beitrag ' What es New in EF ', der der lokalen Auflistung hinzugefügt wurde, wird vom Kontext im hinzugefügten Zustand nachverfolgt. Sie wird daher in die Datenbank eingefügt, wenn SaveChanges aufgerufen wird.  
- Der Beitrag, der aus der lokalen Sammlung entfernt wurde (EF-Anfänger Handbuch) ist jetzt im Kontext als gelöscht gekennzeichnet. Daher wird Sie aus der Datenbank gelöscht, wenn SaveChanges aufgerufen wird.  
- Der zusätzliche Post (ASP.net Beginners Guide), der mit der zweiten Abfrage in den Kontext geladen wird, wird der lokalen Auflistung automatisch hinzugefügt.  

Eine letzte Anmerkung zu Local ist, dass es sich bei einer ObservableCollection-Leistung nicht hervorragend für eine große Anzahl von Entitäten eignet. Wenn Sie also Tausende von Entitäten in ihrem Kontext verwenden, ist es möglicherweise nicht empfehlenswert, lokale zu verwenden.  

## <a name="using-local-for-wpf-data-binding"></a>Verwenden von Local für die WPF-Datenbindung  

Die lokale Eigenschaft in dbset kann direkt für die Datenbindung in einer WPF-Anwendung verwendet werden, da es sich um eine Instanz von ObservableCollection handelt. Wie in den vorherigen Abschnitten beschrieben, bedeutet dies, dass Sie automatisch mit dem Inhalt des Kontexts synchronisiert wird und der Inhalt des Kontexts automatisch synchron bleibt. Beachten Sie, dass Sie die lokale Sammlung mit Daten füllen müssen, damit Sie an eine Bindung gebunden werden, da local nie eine Datenbankabfrage verursacht.  

Dies ist kein geeigneter Ort für ein vollständiges WPF-Daten Bindungs Beispiel, aber die wichtigsten Elemente sind:  

- Einrichten einer Bindungs Quelle  
- Binden Sie es an die lokale Eigenschaft ihrer Gruppe.  
- Füllen Sie local mithilfe einer Abfrage an die Datenbank auf.  

## <a name="wpf-binding-to-navigation-properties"></a>WPF-Bindung an Navigations Eigenschaften  

Wenn Sie die Master/Detail-Datenbindung durchgeführt haben, möchten Sie möglicherweise die Detailansicht an eine Navigations Eigenschaft einer ihrer Entitäten binden. Eine einfache Möglichkeit, dies zu tun, ist die Verwendung einer ObservableCollection für die Navigations Eigenschaft. Beispiel:  

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

## <a name="using-local-to-clean-up-entities-in-savechanges"></a>Verwenden von local zum Bereinigen von Entitäten in SaveChanges  

In den meisten Fällen werden Entitäten, die aus einer Navigations Eigenschaft entfernt werden, im Kontext nicht automatisch als gelöscht markiert. Wenn Sie z. b. ein Post-Objekt aus der Blog. Posts-Sammlung entfernen, wird dieser Beitrag nicht automatisch gelöscht, wenn SaveChanges aufgerufen wird. Wenn Sie den Löschvorgang benötigen, müssen Sie möglicherweise diese verbleibenden Entitäten suchen und als gelöscht markieren, bevor Sie SaveChanges oder als Teil einer überschriebenen SaveChanges aufrufen. Beispiel:  

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

Der obige Code verwendet die lokale Auflistung, um nach allen Beiträgen zu suchen, und kennzeichnet alle, die keinen Blog Verweis als gelöscht haben. Der Auflistungs aufrufsvorgang ist erforderlich, da die Auflistung andernfalls durch den remove-Befehl geändert wird, während diese aufgelistet wird. In den meisten anderen Situationen können Sie die lokale Eigenschaft direkt Abfragen, ohne zuerst zuerst "-List" zu verwenden.  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a>Verwenden von "local" und "ToBindingList" für Windows Forms Datenbindung  

Windows Forms unterstützt die Datenbindung mit vollständiger Genauigkeit nicht direkt mithilfe von ObservableCollection. Sie können jedoch weiterhin die lokale dbset-Eigenschaft für die Datenbindung verwenden, um alle Vorteile zu erhalten, die in den vorherigen Abschnitten beschrieben wurden. Dies wird durch die "debindinglist"-Erweiterungsmethode erreicht, die eine [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) -Implementierung erstellt, die von der lokalen ObservableCollection unterstützt wird.  

Dies ist kein geeigneter Ort für ein vollständiges Windows Forms Daten Bindungs Beispiel, aber die wichtigsten Elemente sind:  

- Einrichten einer Objekt Bindungs Quelle  
- Binden Sie es an die lokale Eigenschaft ihrer Gruppe, indem Sie local. debindinglist () verwenden.  
- Lokales Auffüllen mithilfe einer Abfrage an die Datenbank  

## <a name="getting-detailed-information-about-tracked-entities"></a>Ausführliche Informationen zu nach verfolgten Entitäten  

Viele der Beispiele in dieser Reihe verwenden die Entry-Methode, um eine dbentityentry-Instanz für eine Entität zurückzugeben. Dieses Einstiegs Objekt fungiert dann als Ausgangspunkt für das Sammeln von Informationen über die Entität, z. b. den aktuellen Status, sowie für das Ausführen von Vorgängen für die Entität, z. b. das explizite Laden einer verknüpften Entität  

Die Entries-Methoden geben dbentityentry-Objekte für viele oder alle Entitäten zurück, die vom Kontext nachverfolgt werden. Dies ermöglicht es Ihnen, Informationen zu sammeln oder Vorgänge für viele Entitäten statt nur für einen einzelnen Eintrag auszuführen. Beispiel:  

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

Sie werden feststellen, dass wir eine Author-und Reader-Klasse in das Beispiel einführen. beide Klassen implementieren die IPerson-Schnittstelle.  

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

Angenommen, wir haben die folgenden Daten in der Datenbank:

Blog mit BlogId = 1 und Name = "ADO.net Blog"  
Blog mit BlogId = 2 und Name = "der Visual Studio-Blog"  
Blog mit BlogId = 3 und Name = ".NET Framework Blog"  
Autor mit AutorID = 1 und Name = ' Joe Bloggs '  
Reader mit readerid = 1 und Name = ' John Doe '  

Die Ausgabe der Ausführung des Codes lautet wie folgt:  

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

In diesen Beispielen werden mehrere Punkte veranschaulicht:  

- Die Entries-Methoden geben Einträge für Entitäten in allen Zuständen zurück, einschließlich des gelöschten. Vergleichen Sie dies mit Local, wodurch gelöschte Entitäten ausgeschlossen werden.  
- Einträge für alle Entitäts Typen werden zurückgegeben, wenn die nicht generische Entries-Methode verwendet wird. Wenn die generische Entries-Methode verwendet wird, werden Einträge nur für Entitäten zurückgegeben, die Instanzen des generischen Typs sind. Dies wurde oben verwendet, um Einträge für alle Blogs zu erhalten. Es wurde auch verwendet, um Einträge für alle Entitäten zu erhalten, die IPerson implementieren. Dies zeigt, dass es sich bei dem generischen Typ nicht um einen tatsächlichen Entitätstyp handeln muss.  
- LINQ to Objects können verwendet werden, um die zurückgegebenen Ergebnisse zu filtern. Dies wurde oben verwendet, um Entitäten eines beliebigen Typs zu suchen, solange Sie geändert werden.  

Beachten Sie, dass die dbentityentry-Instanzen immer eine Entität ungleich NULL enthalten. Beziehungs Einträge und Stub-Einträge werden nicht als dbentityentry-Instanzen dargestellt, sodass es nicht erforderlich ist, diese zu filtern.
