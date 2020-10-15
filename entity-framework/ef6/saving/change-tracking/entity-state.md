---
title: Arbeiten mit Entitäts Zuständen EF6
description: Arbeiten mit Entitäts Zuständen in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/entity-state
ms.openlocfilehash: 5373f7157882062e8a73b2bd414c6a8b9accdba4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064457"
---
# <a name="working-with-entity-states"></a>Arbeiten mit Entitäts Zuständen
In diesem Thema wird das Hinzufügen und Anfügen von Entitäten zu einem Kontext und die Art und Weise beschrieben, wie Entity Framework diese während SaveChanges verarbeitet.
Entity Framework übernimmt die Überwachung des Zustands von Entitäten, während Sie mit einem Kontext verbunden sind, aber in getrennten oder N-Tier-Szenarios können Sie EF wissen, in welchem Zustand sich Ihre Entitäten befinden sollten.
Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.  

## <a name="entity-states-and-savechanges"></a>Entitäts Zustände und SaveChanges

Eine Entität kann einen von fünf Zuständen aufweisen, wie von der EntityState-Enumeration definiert. Folgende Status sind möglich:  

- Hinzugefügt: die Entität wird vom Kontext nachverfolgt, ist aber noch nicht in der Datenbank vorhanden.  
- Unverändert: die Entität wird vom Kontext nachverfolgt und ist in der Datenbank vorhanden, und die Eigenschaftswerte wurden aus den Werten in der Datenbank nicht geändert.  
- Geändert: die Entität wird vom Kontext nachverfolgt und ist in der Datenbank vorhanden, und einige oder alle Eigenschaftswerte wurden geändert.  
- Gelöscht: die Entität wird vom Kontext nachverfolgt und ist in der Datenbank vorhanden, wurde aber beim nächsten Aufrufen von SaveChanges zum Löschen aus der Datenbank markiert.  
- Getrennt: die Entität wird nicht vom Kontext nachverfolgt.  

"SaveChanges" unterscheidet sich für Entitäten in unterschiedlichen Zuständen:  

- Nicht geänderte Entitäten werden von SaveChanges nicht berührt. Für Entitäten mit dem Status "unverändert" werden keine Updates an die Datenbank gesendet.  
- Hinzugefügte Entitäten werden in die Datenbank eingefügt und dann bei Rückgabe von SaveChanges unverändert.  
- Geänderte Entitäten werden in der Datenbank aktualisiert und werden bei Rückgabe von SaveChanges unverändert.  
- Gelöschte Entitäten werden aus der Datenbank gelöscht und dann vom Kontext getrennt.  

In den folgenden Beispielen wird veranschaulicht, wie der Zustand einer Entität oder eines Entitäts Diagramms geändert werden kann.  

## <a name="adding-a-new-entity-to-the-context"></a>Hinzufügen einer neuen Entität zum Kontext  

Eine neue Entität kann dem Kontext hinzugefügt werden, indem die Add-Methode für dbset aufgerufen wird.
Dadurch wird die Entität in den hinzugefügten Zustand versetzt, was bedeutet, dass Sie beim nächsten Aufruf von SaveChanges in die Datenbank eingefügt wird.
Beispiel:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Blogs.Add(blog);
    context.SaveChanges();
}
```  

Eine weitere Möglichkeit zum Hinzufügen einer neuen Entität zum Kontext besteht darin, den Zustand in hinzugefügt zu ändern. Beispiel:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = new Blog { Name = "ADO.NET Blog" };
    context.Entry(blog).State = EntityState.Added;
    context.SaveChanges();
}
```  

Schließlich können Sie dem Kontext eine neue Entität hinzufügen, indem Sie Sie mit einer anderen Entität verknüpfen, die bereits nachverfolgt wird.
Dazu kann die neue Entität der Auflistungs Navigations Eigenschaft einer anderen Entität hinzugefügt werden oder indem eine Verweis Navigations Eigenschaft einer anderen Entität festgelegt wird, um auf die neue Entität zu verweisen. Beispiel:  

``` csharp
using (var context = new BloggingContext())
{
    // Add a new User by setting a reference from a tracked Blog
    var blog = context.Blogs.Find(1);
    blog.Owner = new User { UserName = "johndoe1987" };

    // Add a new Post by adding to the collection of a tracked Blog
    blog.Posts.Add(new Post { Name = "How to Add Entities" });

    context.SaveChanges();
}
```  

Beachten Sie, dass für alle diese Beispiele, wenn die hinzugefügte Entität Verweise auf andere Entitäten aufweist, die noch nicht nachverfolgt werden, diese neuen Entitäten ebenfalls dem Kontext hinzugefügt werden und beim nächsten Aufruf von SaveChanges in die Datenbank eingefügt werden.  

## <a name="attaching-an-existing-entity-to-the-context"></a>Anfügen einer vorhandenen Entität an den Kontext  

Wenn Sie bereits über eine Entität verfügen, die bereits in der Datenbank vorhanden ist, die aber zurzeit nicht vom Kontext nachverfolgt wird, können Sie den Kontext darüber informieren, dass die Entität mithilfe der Attach-Methode in dbset nachverfolgt wird. Die Entität befindet sich im Zustand "unverändert" im Kontext. Beispiel:  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);

    // Do some more work...  

    context.SaveChanges();
}
```  

Beachten Sie, dass keine Änderungen an der Datenbank vorgenommen werden, wenn SaveChanges aufgerufen wird, ohne dass eine andere Bearbeitung der angefügten Entität erfolgt. Dies liegt daran, dass sich die Entität im unveränderten Zustand befindet.  

Eine andere Möglichkeit, eine vorhandene Entität an den Kontext anzufügen, besteht darin, den Zustand in unverändert zu ändern. Beispiel:  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

Beachten Sie, dass in beiden Beispielen, wenn die angefügte Entität Verweise auf andere Entitäten enthält, die noch nicht nachverfolgt werden, diese neuen Entitäten ebenfalls im unveränderten Zustand an den Kontext angefügt werden.  

## <a name="attaching-an-existing-but-modified-entity-to-the-context"></a>Anfügen einer vorhandenen, aber geänderten Entität an den Kontext  

Wenn Sie bereits über eine Entität verfügen, die bereits in der Datenbank vorhanden ist, für die jedoch möglicherweise Änderungen vorgenommen wurden, können Sie den Kontext anfügen, um die Entität anzufügen und ihren Status auf "geändert" festzulegen.
Beispiel:  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Entry(existingBlog).State = EntityState.Modified;

    // Do some more work...  

    context.SaveChanges();
}
```  

Wenn Sie den Status in geändert ändern, werden alle Eigenschaften der Entität als geändert markiert, und alle Eigenschaftswerte werden an die Datenbank gesendet, wenn SaveChanges aufgerufen wird.  

Beachten Sie Folgendes: Wenn die anzufügende Entität Verweise auf andere Entitäten enthält, die noch nicht nachverfolgt werden, werden diese neuen Entitäten im unveränderten Zustand an den Kontext angefügt – Sie werden nicht automatisch geändert.
Wenn Sie über mehrere Entitäten verfügen, die als geändert markiert werden müssen, sollten Sie den Status für jede dieser Entitäten einzeln festlegen.  

## <a name="changing-the-state-of-a-tracked-entity"></a>Ändern des Status einer nach verfolgten Entität  

Sie können den Zustand einer Entität, die bereits nachverfolgt wird, ändern, indem Sie die State-Eigenschaft für Ihren Eintrag festlegen. Beispiel:  

``` csharp
var existingBlog = new Blog { BlogId = 1, Name = "ADO.NET Blog" };

using (var context = new BloggingContext())
{
    context.Blogs.Attach(existingBlog);
    context.Entry(existingBlog).State = EntityState.Unchanged;

    // Do some more work...  

    context.SaveChanges();
}
```  

Beachten Sie, dass das Aufrufen von Add oder Attach für eine Entität, die bereits nachverfolgt ist, auch zum Ändern des Entitäts Zustands verwendet werden kann. Wenn z. b. Attach für eine Entität aufgerufen wird, die sich derzeit im hinzugefügten Zustand befindet, ändert sich der Status in unverändert.  

## <a name="insert-or-update-pattern"></a>INSERT-oder Update-Muster  

Ein gängiges Muster für einige Anwendungen besteht darin, entweder eine Entität als neu hinzuzufügen (was zu einer Daten Bank Einfügung führt) oder eine Entität als vorhanden anzufügen und Sie als geändert (d. h. ein Datenbankupdate) zu kennzeichnen, je nach dem Wert des Primärschlüssels.
Wenn z. b. Daten Bank generierte ganzzahlige Primärschlüssel verwendet werden, wird eine Entität mit einem NULL-Schlüssel als neu und eine Entität mit einem Schlüssel ungleich 0 (null) als vorhanden behandelt.
Dieses Muster kann erreicht werden, indem der Entitäts Zustand basierend auf einer Überprüfung des Primärschlüssel Werts festgelegt wird. Beispiel:  

``` csharp
public void InsertOrUpdate(Blog blog)
{
    using (var context = new BloggingContext())
    {
        context.Entry(blog).State = blog.BlogId == 0 ?
                                   EntityState.Added :
                                   EntityState.Modified;

        context.SaveChanges();
    }
}
```  

Beachten Sie, dass beim Ändern des Zustands in geändert alle Eigenschaften der Entität als geändert gekennzeichnet werden und alle Eigenschaftswerte an die Datenbank gesendet werden, wenn SaveChanges aufgerufen wird.  
