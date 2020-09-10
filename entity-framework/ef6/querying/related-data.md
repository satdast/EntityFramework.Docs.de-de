---
title: Laden verwandter Entitäten EF6
description: Laden von verknüpften Entitäten in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: c8417e18-a2ee-499c-9ce9-2a48cc5b468a
uid: ef6/querying/related-data
ms.openlocfilehash: 9b4e8ecda618e11e87cd595502210234b1f3e27d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620266"
---
# <a name="loading-related-entities"></a>Laden verwandter Entitäten

Entity Framework unterstützt drei Möglichkeiten, verwandte Daten Eager Loading, Lazy Loading und expliziten Laden zu laden. Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.

## <a name="eagerly-loading"></a>Eifrig geladen

Das unverzügliches Laden ist der Prozess, bei dem eine Abfrage für einen Entitätstyp auch Verwandte Entitäten als Teil der Abfrage lädt. Das unverzügliches Laden wird durch die Verwendung der Include-Methode erreicht. Die nachfolgenden Abfragen laden z. b. Blogs und alle Beiträge im Zusammenhang mit den einzelnen Blogs.

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts)
                        .ToList();

    // Load one blog and its related posts.
    var blog1 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include(b => b.Posts)
                       .FirstOrDefault();

    // Load all blogs and related posts
    // using a string to specify the relationship.
    var blogs2 = context.Blogs
                        .Include("Posts")
                        .ToList();

    // Load one blog and its related posts
    // using a string to specify the relationship.
    var blog2 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include("Posts")
                       .FirstOrDefault();
}
```

> [!NOTE]
> Include ist eine Erweiterungsmethode im System. Data. Entity-Namespace. Stellen Sie daher sicher, dass Sie diesen Namespace verwenden.

### <a name="eagerly-loading-multiple-levels"></a>Das eifrig Laden mehrerer Ebenen

Es ist auch möglich, mehrere Ebenen verwandter Entitäten zu laden. Die folgenden Abfragen zeigen Beispiele dazu, wie Sie dies für die Sammlungs-und Verweis Navigations Eigenschaften durchführen können.  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts.Select(p => p.Comments))
                        .ToList();

    // Load all users, their related profiles, and related avatar.
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships.
    var blogs2 = context.Blogs
                        .Include("Posts.Comments")
                        .ToList();

    // Load all users, their related profiles, and related avatar  
    // using a string to specify the relationships.
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

> [!NOTE]
> Es ist derzeit nicht möglich, zu filtern, welche verknüpften Entitäten geladen werden. Include führt immer alle zugehörigen Entitäten ein.  

## <a name="lazy-loading"></a>Lazy Loading

Lazy Load ist der Prozess, bei dem eine Entität oder eine Auflistung von Entitäten automatisch aus der Datenbank geladen wird, wenn zum ersten Mal auf eine Eigenschaft zugegriffen wird, die auf Entitäten/Entitäten verweist. Wenn poco-Entitäts Typen verwendet werden, wird Lazy Loading durch Erstellen von Instanzen abgeleiteter Proxy Typen und anschließendes Überschreiben von virtuellen Eigenschaften zum Hinzufügen des Lade Hooks erreicht. Wenn Sie z. b. die unten definierte Blog-Entitäts Klasse verwenden, werden die zugehörigen Beiträge beim ersten Zugriff auf die "Posts"-Navigations Eigenschaft geladen:

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

### <a name="turn-lazy-loading-off-for-serialization"></a>Lazy Loading für Serialisierung deaktivieren

Lazy Load und Serialisierung sind nicht gut gemischt, und wenn Sie nicht vorsichtig sind, können Sie die Abfrage für Ihre gesamte Datenbank durchzuführen, da Lazy Loading aktiviert ist. Die meisten serialisierungsinitialisierer funktionieren, indem Sie auf jede Eigenschaft einer Instanz eines Typs zugreifen. Eigenschaften Zugriffs Trigger Lazy Loading, sodass weitere Entitäten serialisiert werden. Auf diesen Entitäten wird zugegriffen, und es werden sogar weitere Entitäten geladen. Es empfiehlt sich, Lazy Loading zu deaktivieren, bevor Sie eine Entität serialisieren. Dies wird in den folgenden Abschnitte gezeigt.

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a>Ausschalten Lazy Loading für bestimmte Navigations Eigenschaften

Das Lazy Load der Posts-Auflistung kann deaktiviert werden, indem die Posts-Eigenschaft nicht virtuell ist:

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public ICollection<Post> Posts { get; set; }
}
```

Das Laden der Posts-Auflistung kann weiterhin mithilfe von Eager Loading (siehe das explizite *Laden* oben) oder der Load-Methode erzielt werden (siehe *Explizites Laden* unten).

### <a name="turn-off-lazy-loading-for-all-entities"></a>Lazy Loading für alle Entitäten deaktivieren

Lazy Load kann für alle Entitäten im Kontext deaktiviert werden, indem ein Flag für die Konfigurations Eigenschaft festgelegt wird. Beispiel:

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```

Das Laden von verknüpften Entitäten kann weiterhin mithilfe von Eager Loading (siehe das explizite *Laden* oben) oder der Load-Methode erzielt werden (siehe *Explizites Laden* unten).

## <a name="explicitly-loading"></a>Explizites Laden

Auch wenn Lazy Loading deaktiviert ist, ist es weiterhin möglich, verknüpfte Entitäten verzögert zu laden, aber es muss ein expliziter-Vorgang ausgeführt werden. Zu diesem Zweck verwenden Sie die Load-Methode für den Eintrag der zugehörigen Entität. Beispiel:

``` csharp
using (var context = new BloggingContext())
{
    var post = context.Posts.Find(2);

    // Load the blog related to a given post.
    context.Entry(post).Reference(p => p.Blog).Load();

    // Load the blog related to a given post using a string.
    context.Entry(post).Reference("Blog").Load();

    var blog = context.Blogs.Find(1);

    // Load the posts related to a given blog.
    context.Entry(blog).Collection(p => p.Posts).Load();

    // Load the posts related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog).Collection("Posts").Load();
}
```

> [!NOTE]
> Die Reference-Methode sollte verwendet werden, wenn eine Entität über eine Navigations Eigenschaft zu einer anderen einzelnen Entität verfügt. Dagegen sollte die-Auflistungs Methode verwendet werden, wenn eine Entität über eine Navigations Eigenschaft für eine Auflistung von anderen Entitäten verfügt.

### <a name="applying-filters-when-explicitly-loading-related-entities"></a>Anwenden von Filtern beim expliziten Laden verwandter Entitäten

Die Query-Methode ermöglicht den Zugriff auf die zugrunde liegende Abfrage, die Entity Framework beim Laden von verknüpften Entitäten verwenden wird. Anschließend können Sie mithilfe von LINQ Filter auf die Abfrage anwenden, bevor Sie Sie ausführen, indem Sie eine LINQ-Erweiterungsmethode wie z. b. ToList, Load usw. verwenden. Die Query-Methode kann sowohl mit Verweis-als auch Auflistungs Navigations Eigenschaften verwendet werden, ist jedoch besonders nützlich für Auflistungen, in denen Sie zum Laden von nur einem Teil der Auflistung verwendet werden kann. Beispiel:

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog.
    context.Entry(blog)
           .Collection(b => b.Posts)
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();

    // Load the posts with the 'entity-framework' tag related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog)
           .Collection("Posts")
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();
}
```

Wenn Sie die Abfrage Methode verwenden, empfiehlt es sich in der Regel, Lazy Loading für die Navigations Eigenschaft zu deaktivieren. Der Grund hierfür ist, dass die gesamte Auflistung möglicherweise automatisch vom Lazy Loading Mechanismus geladen wird, entweder vor oder nach der Ausführung der gefilterten Abfrage.

> [!NOTE]
> Obwohl die Beziehung anstelle eines Lambda-Ausdrucks als Zeichenfolge angegeben werden kann, ist das zurückgegebene iquerable-Element nicht generisch, wenn eine Zeichenfolge verwendet wird, sodass die Cast-Methode normalerweise benötigt wird, bevor etwas Nützliches möglich ist.

## <a name="using-query-to-count-related-entities-without-loading-them"></a>Verwenden von Abfragen zum zählen verwandter Entitäten, ohne Sie zu laden

Manchmal ist es hilfreich zu wissen, wie viele Entitäten mit einer anderen Entität in der Datenbank verknüpft sind, ohne dass tatsächlich die Kosten für das Laden all dieser Entitäten anfallen. Hierfür kann die Query-Methode mit der LINQ Count-Methode verwendet werden. Beispiel:

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Count how many posts the blog has.
    var postCount = context.Entry(blog)
                           .Collection(b => b.Posts)
                           .Query()
                           .Count();
}
```
