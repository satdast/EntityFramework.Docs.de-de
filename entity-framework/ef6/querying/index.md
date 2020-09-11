---
title: 'Abfragen und Suchen von Entitäten: EF6'
description: Abfragen und Suchen von Entitäten in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 65bb3db2-2226-44af-8864-caa575cf1b46
uid: ef6/querying/index
ms.openlocfilehash: 4bfdc59527ab75e7a2e08dc743a1974246643aa3
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620337"
---
# <a name="querying-and-finding-entities"></a>Abfragen und Suchen von Entitäten
Dieses Thema behandelt die verschiedenen Methoden, mit denen Sie Daten über Entity Framework abfragen können, einschließlich LINQ und der Find-Methode. Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.  

## <a name="finding-entities-using-a-query"></a>Suchen von Entitäten über eine Abfrage  

DbSet und IDbSet implementieren IQueryable, was als Startpunkt zum Schreiben einer LINQ-Abfrage für die Datenbank verwendet werden kann. In diesem Artikel wird LINQ nicht ausführlich erläutert, jedoch finden Sie nachstehend einige einfache Beispiele:  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs with names starting with B
    var blogs = from b in context.Blogs
                   where b.Name.StartsWith("B")
                   select b;

    // Query for the Blog named ADO.NET Blog
    var blog = context.Blogs
                    .Where(b => b.Name == "ADO.NET Blog")
                    .FirstOrDefault();
}
```  

Beachten Sie, dass DbSet und IDbSet immer Abfragen für die Datenbank erstellen und immer einen Roundtrip zur Datenbank mit sich bringen, auch wenn die zurückgegebenen Entitäten bereits im Kontext vorhanden sind. Eine Abfrage wird für die Datenbank ausgeführt, wenn:  

- Sie durch eine **foreach**-Anweisung (C#) oder eine **ForEach**-Anweisung (Visual Basic) aufgezählt wird.  
- Sie wird von einem Auflistungsvorgang wie [ToArray](https://msdn.microsoft.com/library/bb298736), [ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary) oder [ToList](https://msdn.microsoft.com/library/bb342261) aufgelistet.  
- LINQ-Operatoren, wie beispielsweise [First](https://msdn.microsoft.com/library/bb291976) oder [Any](https://msdn.microsoft.com/library/bb337697), werden im äußersten Teil der Abfrage angegeben.  
- Die folgenden Methoden werden aufgerufen: die Erweiterungsmethode [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) (auf einem DBSet), [DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx) und Database.ExecuteSqlCommand.  

Wenn Ergebnisse von der Datenbank zurückgegeben werden, werden Objekte, die nicht im Kontext vorhanden sind, an den Kontext angefügt. Wenn sich ein Objekt bereits im Kontext befindet, wird das vorhandene Objekt zurückgegeben (die aktuellen und ursprünglichen Werte der Objekteigenschaften im Eintrag werden **nicht** mit Datenquellenwerten überschrieben).  

Wenn Sie eine Abfrage ausführen, werden Entitäten, die zum Kontext hinzugefügt wurden, jedoch nicht auf der Datenbank gespeichert wurden, nicht als Teil des Resultsets zurückgegeben. Unter [Entity Framework Local Data (Lokale Daten von Entity Framework)](xref:ef6/querying/local-data) erhalten Sie Informationen zum Abrufen von Daten, die sich im Kontext befinden.  

Wenn eine Abfrage keine Zeilen aus der Datenbank zurückgibt, ist das Ergebnis eine leere Sammlung anstatt **NULL**.  

## <a name="finding-entities-using-primary-keys"></a>Suchen von Entitäten über Primärschlüssel  

Die Find-Methoden auf DbSet verwendet den Primärschlüsselwert, um eine Entität, die vom Kontext nachverfolgt wird, zu finden. Wenn die Entität im Kontext nicht gefunden wird, wird eine Abfrage an die Datenbank gesendet, um dort die Entität zu suchen. Wenn die Entität nicht im Kontext oder in der Datenbank gefunden wird, wird NULL zurückgegeben.  

„Find“ unterscheidet sich von der Verwendung einer Abfrage in zwei Punkten:  

- Es wird nur ein Roundtrip zur Datenbank durchgeführt, wenn die Entität im angegebenen Schlüssel nicht im Kontext gefunden wird.  
- „Find“ gibt Entitäten zurück, die den Zustand „Added“ (Hinzugefügt) aufweisen. Das bedeutet, dass „Find“ Entitäten zurückgibt, die zum Kontext hinzugefügt, jedoch noch nicht in der Datenbank gespeichert wurden.  
### <a name="finding-an-entity-by-primary-key"></a>Suchen nach einer Entität nach Primärschlüsseln  

Der folgende Code zeigt einige Verwendungsmöglichkeiten für „Find“:  

``` csharp
using (var context = new BloggingContext())
{
    // Will hit the database
    var blog = context.Blogs.Find(3);

    // Will return the same instance without hitting the database
    var blogAgain = context.Blogs.Find(3);

    context.Blogs.Add(new Blog { Id = -1 });

    // Will find the new blog even though it does not exist in the database
    var newBlog = context.Blogs.Find(-1);

    // Will find a User which has a string primary key
    var user = context.Users.Find("johndoe1987");
}
```  

### <a name="finding-an-entity-by-composite-primary-key"></a>Suchen nach einer Entität nach zusammengesetztem Primärschlüsseln (Verbundprimärschlüssel)  

Entity Framework lässt zusammengesetzte Schlüssel für Ihre Entität zu. Diese sind Schlüssel, die aus mehr aus einer Eigenschaft bestehen. Sie verfügen z.B. über die Entität „BlogSettings“, die eine Benutzereinstellung für einen bestimmten Blog darstellt. Da ein Benutzer immer nur eine BlogSettings-Entität für jeden Blog besitzen würde, könnten Sie aus dem Primärschlüssel von BlogSettings eine Kombination aus BlogId und Benutzername machen. Durch folgenden Code wird versucht, BlogSettings mit BlogId = 3 und Benutzername = „johndoe1987“ zu finden.  

``` csharp  
using (var context = new BloggingContext())
{
    var settings = context.BlogSettings.Find(3, "johndoe1987");
}
```  

Wenn Sie über zusammengesetzte Schlüssel verfügen, dann beachten Sie, dass Sie das ColumnAttribute oder die Fluent-API verwenden müssen, um eine Reihenfolge für die Eigenschaften der zusammengesetzten Schlüssel anzugeben. Der Aufruf von „Find“ muss diese Reihenfolge verwenden, wenn die Werte, aus denen der Schlüssel besteht, angegeben werden.  
