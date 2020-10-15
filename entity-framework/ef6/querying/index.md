---
title: 'Abfragen und Suchen von Entitäten: EF6'
description: Abfragen und Suchen von Entitäten in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/index
ms.openlocfilehash: ec0239ed54099226d009031af79388f5ae00cdc4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065893"
---
# <a name="querying-and-finding-entities"></a><span data-ttu-id="eebd1-103">Abfragen und Suchen von Entitäten</span><span class="sxs-lookup"><span data-stu-id="eebd1-103">Querying and Finding Entities</span></span>
<span data-ttu-id="eebd1-104">Dieses Thema behandelt die verschiedenen Methoden, mit denen Sie Daten über Entity Framework abfragen können, einschließlich LINQ und der Find-Methode.</span><span class="sxs-lookup"><span data-stu-id="eebd1-104">This topic covers the various ways you can query for data using Entity Framework, including LINQ and the Find method.</span></span> <span data-ttu-id="eebd1-105">Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="eebd1-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="finding-entities-using-a-query"></a><span data-ttu-id="eebd1-106">Suchen von Entitäten über eine Abfrage</span><span class="sxs-lookup"><span data-stu-id="eebd1-106">Finding entities using a query</span></span>  

<span data-ttu-id="eebd1-107">DbSet und IDbSet implementieren IQueryable, was als Startpunkt zum Schreiben einer LINQ-Abfrage für die Datenbank verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="eebd1-107">DbSet and IDbSet implement IQueryable and so can be used as the starting point for writing a LINQ query against the database.</span></span> <span data-ttu-id="eebd1-108">In diesem Artikel wird LINQ nicht ausführlich erläutert, jedoch finden Sie nachstehend einige einfache Beispiele:</span><span class="sxs-lookup"><span data-stu-id="eebd1-108">This is not the appropriate place for an in-depth discussion of LINQ, but here are a couple of simple examples:</span></span>  

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

<span data-ttu-id="eebd1-109">Beachten Sie, dass DbSet und IDbSet immer Abfragen für die Datenbank erstellen und immer einen Roundtrip zur Datenbank mit sich bringen, auch wenn die zurückgegebenen Entitäten bereits im Kontext vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="eebd1-109">Note that DbSet and IDbSet always create queries against the database and will always involve a round trip to the database even if the entities returned already exist in the context.</span></span> <span data-ttu-id="eebd1-110">Eine Abfrage wird für die Datenbank ausgeführt, wenn:</span><span class="sxs-lookup"><span data-stu-id="eebd1-110">A query is executed against the database when:</span></span>  

- <span data-ttu-id="eebd1-111">Sie durch eine **foreach**-Anweisung (C#) oder eine **ForEach**-Anweisung (Visual Basic) aufgezählt wird.</span><span class="sxs-lookup"><span data-stu-id="eebd1-111">It is enumerated by a **foreach** (C#) or **For Each** (Visual Basic) statement.</span></span>  
- <span data-ttu-id="eebd1-112">Sie wird von einem Auflistungsvorgang wie [ToArray](https://msdn.microsoft.com/library/bb298736), [ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary) oder [ToList](https://msdn.microsoft.com/library/bb342261) aufgelistet.</span><span class="sxs-lookup"><span data-stu-id="eebd1-112">It is enumerated by a collection operation such as [ToArray](https://msdn.microsoft.com/library/bb298736), [ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary), or [ToList](https://msdn.microsoft.com/library/bb342261).</span></span>  
- <span data-ttu-id="eebd1-113">LINQ-Operatoren, wie beispielsweise [First](https://msdn.microsoft.com/library/bb291976) oder [Any](https://msdn.microsoft.com/library/bb337697), werden im äußersten Teil der Abfrage angegeben.</span><span class="sxs-lookup"><span data-stu-id="eebd1-113">LINQ operators such as [First](https://msdn.microsoft.com/library/bb291976) or [Any](https://msdn.microsoft.com/library/bb337697) are specified in the outermost part of the query.</span></span>  
- <span data-ttu-id="eebd1-114">Die folgenden Methoden werden aufgerufen: die Erweiterungsmethode [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) (auf einem DBSet), [DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx) und Database.ExecuteSqlCommand.</span><span class="sxs-lookup"><span data-stu-id="eebd1-114">The following methods are called: the [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) extension method on a DbSet, [DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx), and Database.ExecuteSqlCommand.</span></span>  

<span data-ttu-id="eebd1-115">Wenn Ergebnisse von der Datenbank zurückgegeben werden, werden Objekte, die nicht im Kontext vorhanden sind, an den Kontext angefügt.</span><span class="sxs-lookup"><span data-stu-id="eebd1-115">When results are returned from the database, objects that do not exist in the context are attached to the context.</span></span> <span data-ttu-id="eebd1-116">Wenn sich ein Objekt bereits im Kontext befindet, wird das vorhandene Objekt zurückgegeben (die aktuellen und ursprünglichen Werte der Objekteigenschaften im Eintrag werden **nicht** mit Datenquellenwerten überschrieben).</span><span class="sxs-lookup"><span data-stu-id="eebd1-116">If an object is already in the context, the existing object is returned (the current and original values of the object's properties in the entry are **not** overwritten with database values).</span></span>  

<span data-ttu-id="eebd1-117">Wenn Sie eine Abfrage ausführen, werden Entitäten, die zum Kontext hinzugefügt wurden, jedoch nicht auf der Datenbank gespeichert wurden, nicht als Teil des Resultsets zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="eebd1-117">When you perform a query, entities that have been added to the context but have not yet been saved to the database are not returned as part of the result set.</span></span> <span data-ttu-id="eebd1-118">Unter [Entity Framework Local Data (Lokale Daten von Entity Framework)](xref:ef6/querying/local-data) erhalten Sie Informationen zum Abrufen von Daten, die sich im Kontext befinden.</span><span class="sxs-lookup"><span data-stu-id="eebd1-118">To get the data that is in the context, see [Local Data](xref:ef6/querying/local-data).</span></span>  

<span data-ttu-id="eebd1-119">Wenn eine Abfrage keine Zeilen aus der Datenbank zurückgibt, ist das Ergebnis eine leere Sammlung anstatt **NULL**.</span><span class="sxs-lookup"><span data-stu-id="eebd1-119">If a query returns no rows from the database, the result will be an empty collection, rather than **null**.</span></span>  

## <a name="finding-entities-using-primary-keys"></a><span data-ttu-id="eebd1-120">Suchen von Entitäten über Primärschlüssel</span><span class="sxs-lookup"><span data-stu-id="eebd1-120">Finding entities using primary keys</span></span>  

<span data-ttu-id="eebd1-121">Die Find-Methoden auf DbSet verwendet den Primärschlüsselwert, um eine Entität, die vom Kontext nachverfolgt wird, zu finden.</span><span class="sxs-lookup"><span data-stu-id="eebd1-121">The Find method on DbSet uses the primary key value to attempt to find an entity tracked by the context.</span></span> <span data-ttu-id="eebd1-122">Wenn die Entität im Kontext nicht gefunden wird, wird eine Abfrage an die Datenbank gesendet, um dort die Entität zu suchen.</span><span class="sxs-lookup"><span data-stu-id="eebd1-122">If the entity is not found in the context then a query will be sent to the database to find the entity there.</span></span> <span data-ttu-id="eebd1-123">Wenn die Entität nicht im Kontext oder in der Datenbank gefunden wird, wird NULL zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="eebd1-123">Null is returned if the entity is not found in the context or in the database.</span></span>  

<span data-ttu-id="eebd1-124">„Find“ unterscheidet sich von der Verwendung einer Abfrage in zwei Punkten:</span><span class="sxs-lookup"><span data-stu-id="eebd1-124">Find is different from using a query in two significant ways:</span></span>  

- <span data-ttu-id="eebd1-125">Es wird nur ein Roundtrip zur Datenbank durchgeführt, wenn die Entität im angegebenen Schlüssel nicht im Kontext gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="eebd1-125">A round-trip to the database will only be made if the entity with the given key is not found in the context.</span></span>  
- <span data-ttu-id="eebd1-126">„Find“ gibt Entitäten zurück, die den Zustand „Added“ (Hinzugefügt) aufweisen.</span><span class="sxs-lookup"><span data-stu-id="eebd1-126">Find will return entities that are in the Added state.</span></span> <span data-ttu-id="eebd1-127">Das bedeutet, dass „Find“ Entitäten zurückgibt, die zum Kontext hinzugefügt, jedoch noch nicht in der Datenbank gespeichert wurden.</span><span class="sxs-lookup"><span data-stu-id="eebd1-127">That is, Find will return entities that have been added to the context but have not yet been saved to the database.</span></span>  
### <a name="finding-an-entity-by-primary-key"></a><span data-ttu-id="eebd1-128">Suchen nach einer Entität nach Primärschlüsseln</span><span class="sxs-lookup"><span data-stu-id="eebd1-128">Finding an entity by primary key</span></span>  

<span data-ttu-id="eebd1-129">Der folgende Code zeigt einige Verwendungsmöglichkeiten für „Find“:</span><span class="sxs-lookup"><span data-stu-id="eebd1-129">The following code shows some uses of Find:</span></span>  

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

### <a name="finding-an-entity-by-composite-primary-key"></a><span data-ttu-id="eebd1-130">Suchen nach einer Entität nach zusammengesetztem Primärschlüsseln (Verbundprimärschlüssel)</span><span class="sxs-lookup"><span data-stu-id="eebd1-130">Finding an entity by composite primary key</span></span>  

<span data-ttu-id="eebd1-131">Entity Framework lässt zusammengesetzte Schlüssel für Ihre Entität zu. Diese sind Schlüssel, die aus mehr aus einer Eigenschaft bestehen.</span><span class="sxs-lookup"><span data-stu-id="eebd1-131">Entity Framework allows your entities to have composite keys - that's a key that is made up of more than one property.</span></span> <span data-ttu-id="eebd1-132">Sie verfügen z.B. über die Entität „BlogSettings“, die eine Benutzereinstellung für einen bestimmten Blog darstellt.</span><span class="sxs-lookup"><span data-stu-id="eebd1-132">For example, you could have a BlogSettings entity that represents a users settings for a particular blog.</span></span> <span data-ttu-id="eebd1-133">Da ein Benutzer immer nur eine BlogSettings-Entität für jeden Blog besitzen würde, könnten Sie aus dem Primärschlüssel von BlogSettings eine Kombination aus BlogId und Benutzername machen.</span><span class="sxs-lookup"><span data-stu-id="eebd1-133">Because a user would only ever have one BlogSettings for each blog you could chose to make the primary key of BlogSettings a combination of BlogId and Username.</span></span> <span data-ttu-id="eebd1-134">Durch folgenden Code wird versucht, BlogSettings mit BlogId = 3 und Benutzername = „johndoe1987“ zu finden.</span><span class="sxs-lookup"><span data-stu-id="eebd1-134">The following code attempts to find the BlogSettings with BlogId = 3 and Username = "johndoe1987":</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var settings = context.BlogSettings.Find(3, "johndoe1987");
}
```  

<span data-ttu-id="eebd1-135">Wenn Sie über zusammengesetzte Schlüssel verfügen, dann beachten Sie, dass Sie das ColumnAttribute oder die Fluent-API verwenden müssen, um eine Reihenfolge für die Eigenschaften der zusammengesetzten Schlüssel anzugeben.</span><span class="sxs-lookup"><span data-stu-id="eebd1-135">Note that when you have composite keys you need to use ColumnAttribute or the fluent API to specify an ordering for the properties of the composite key.</span></span> <span data-ttu-id="eebd1-136">Der Aufruf von „Find“ muss diese Reihenfolge verwenden, wenn die Werte, aus denen der Schlüssel besteht, angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="eebd1-136">The call to Find must use this order when specifying the values that form the key.</span></span>  
