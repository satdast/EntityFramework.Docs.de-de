---
title: Arbeiten mit dbcontext-EF6
description: Arbeiten mit dbcontext in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/working-with-dbcontext
ms.openlocfilehash: 83f3f318c6cd1b0ea440bd472a651ff4454716f1
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072147"
---
# <a name="working-with-dbcontext"></a><span data-ttu-id="370b4-103">Arbeiten mit DbContext</span><span class="sxs-lookup"><span data-stu-id="370b4-103">Working with DbContext</span></span>

<span data-ttu-id="370b4-104">Um Entity Framework zum Abfragen, einfügen, aktualisieren und Löschen von Daten mithilfe von .NET-Objekten verwenden zu können, müssen Sie zunächst [ein Modell erstellen](xref:ef6/modeling/index) , das die im Modell definierten Entitäten und Beziehungen den Tabellen in einer Datenbank zuordnet.</span><span class="sxs-lookup"><span data-stu-id="370b4-104">In order to use Entity Framework to query, insert, update, and delete data using .NET objects, you first need to [Create a Model](xref:ef6/modeling/index) which maps the entities and relationships that are defined in your model to tables in a database.</span></span>

<span data-ttu-id="370b4-105">Sobald Sie über ein Modell verfügen, wird die primäre Klasse, mit der Ihre Anwendung interagiert, `System.Data.Entity.DbContext` (häufig als Kontext Klasse bezeichnet) behandelt.</span><span class="sxs-lookup"><span data-stu-id="370b4-105">Once you have a model, the primary class your application interacts with is `System.Data.Entity.DbContext` (often referred to as the context class).</span></span> <span data-ttu-id="370b4-106">Sie können einen mit einem Modell verknüpften dbcontext für Folgendes verwenden:</span><span class="sxs-lookup"><span data-stu-id="370b4-106">You can use a DbContext associated to a model to:</span></span>
- <span data-ttu-id="370b4-107">Schreiben und Ausführen von Abfragen</span><span class="sxs-lookup"><span data-stu-id="370b4-107">Write and execute queries</span></span>   
- <span data-ttu-id="370b4-108">Materialisieren von Abfrage Ergebnissen als Entitäts Objekte</span><span class="sxs-lookup"><span data-stu-id="370b4-108">Materialize query results as entity objects</span></span>
- <span data-ttu-id="370b4-109">An diesen Objekten vorgenommene Änderungen nachverfolgen</span><span class="sxs-lookup"><span data-stu-id="370b4-109">Track changes that are made to those objects</span></span>
- <span data-ttu-id="370b4-110">Objektänderungen in der Datenbank beibehalten</span><span class="sxs-lookup"><span data-stu-id="370b4-110">Persist object changes back on the database</span></span>
- <span data-ttu-id="370b4-111">Binden von Objekten im Arbeitsspeicher an UI-Steuerelemente</span><span class="sxs-lookup"><span data-stu-id="370b4-111">Bind objects in memory to UI controls</span></span>

<span data-ttu-id="370b4-112">Diese Seite enthält eine Anleitung zum Verwalten der Kontext Klasse.</span><span class="sxs-lookup"><span data-stu-id="370b4-112">This page gives some guidance on how to manage the context class.</span></span>  

## <a name="defining-a-dbcontext-derived-class"></a><span data-ttu-id="370b4-113">Definieren einer von dbcontext abgeleiteten Klasse</span><span class="sxs-lookup"><span data-stu-id="370b4-113">Defining a DbContext derived class</span></span>  

<span data-ttu-id="370b4-114">Die empfohlene Vorgehensweise, um mit Kontext zu arbeiten, besteht darin, eine Klasse zu definieren, die von dbcontext abgeleitet ist und dbset-Eigenschaften verfügbar macht, die Sammlungen der angegebenen Entitäten im Kontext darstellen.</span><span class="sxs-lookup"><span data-stu-id="370b4-114">The recommended way to work with context is to define a class that derives from DbContext and exposes DbSet properties that represent collections of the specified entities in the context.</span></span> <span data-ttu-id="370b4-115">Wenn Sie mit dem EF-Designer arbeiten, wird der Kontext für Sie generiert.</span><span class="sxs-lookup"><span data-stu-id="370b4-115">If you are working with the EF Designer, the context will be generated for you.</span></span> <span data-ttu-id="370b4-116">Wenn Sie mit Code First arbeiten, schreiben Sie den Kontext in der Regel selbst.</span><span class="sxs-lookup"><span data-stu-id="370b4-116">If you are working with Code First, you will typically write the context yourself.</span></span>  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

<span data-ttu-id="370b4-117">Wenn Sie über einen Kontext verfügen, können Sie über diese Eigenschaften Abfragen, hinzufügen (using- `Add` oder- `Attach` Methoden) oder (mithilfe von `Remove` ) Entitäten im Kontext entfernen.</span><span class="sxs-lookup"><span data-stu-id="370b4-117">Once you have a context, you would query for, add (using `Add` or `Attach` methods ) or remove (using `Remove`) entities in the context through these properties.</span></span> <span data-ttu-id="370b4-118">Der Zugriff `DbSet` auf eine Eigenschaft für ein Kontext Objekt stellt eine Start Abfrage dar, die alle Entitäten des angegebenen Typs zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="370b4-118">Accessing a `DbSet` property on a context object represent a starting query that returns all entities of the specified type.</span></span> <span data-ttu-id="370b4-119">Beachten Sie, dass beim Zugreifen auf eine Eigenschaft die Abfrage nicht ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="370b4-119">Note that just accessing a property will not execute the query.</span></span> <span data-ttu-id="370b4-120">Eine Abfrage wird ausgeführt, wenn Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="370b4-120">A query is executed when:</span></span>  

- <span data-ttu-id="370b4-121">Sie wird durch eine `foreach` (C#)-Anweisung oder eine `For Each` (Visual Basic)-Anweisung aufgezählt.</span><span class="sxs-lookup"><span data-stu-id="370b4-121">It is enumerated by a `foreach` (C#) or `For Each` (Visual Basic) statement.</span></span>  
- <span data-ttu-id="370b4-122">Sie wird durch einen Auflistungs Vorgang, z `ToArray` . b., oder, aufgezählt `ToDictionary` `ToList` .</span><span class="sxs-lookup"><span data-stu-id="370b4-122">It is enumerated by a collection operation such as `ToArray`, `ToDictionary`, or `ToList`.</span></span>  
- <span data-ttu-id="370b4-123">LINQ-Operatoren, z `First` . b. oder, `Any` werden im äußersten Teil der Abfrage angegeben.</span><span class="sxs-lookup"><span data-stu-id="370b4-123">LINQ operators such as `First` or `Any` are specified in the outermost part of the query.</span></span>  
- <span data-ttu-id="370b4-124">Eine der folgenden Methoden wird aufgerufen: die `Load` Erweiterungsmethode,, `DbEntityEntry.Reload`  `Database.ExecuteSqlCommand` und `DbSet<T>.Find` , wenn eine Entität mit dem angegebenen Schlüssel nicht gefunden wurde, die bereits im Kontext geladen wurde.</span><span class="sxs-lookup"><span data-stu-id="370b4-124">One of the following methods are called: the `Load` extension method, `DbEntityEntry.Reload`,  `Database.ExecuteSqlCommand`, and `DbSet<T>.Find`, if an entity with the specified key is not found already loaded in the context.</span></span>  

## <a name="lifetime"></a><span data-ttu-id="370b4-125">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="370b4-125">Lifetime</span></span>  

<span data-ttu-id="370b4-126">Die Lebensdauer des Kontexts beginnt, wenn die Instanz erstellt wird, und endet, wenn die Instanz entweder verworfen oder Garbage Collection durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="370b4-126">The lifetime of the context begins when the instance is created and ends when the instance is either disposed or garbage-collected.</span></span> <span data-ttu-id="370b4-127">Verwenden Sie **die Verwendung von, wenn alle** Ressourcen, die die Kontext Steuerelemente haben, am Ende des Blocks verworfen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="370b4-127">Use **using** if you want all the resources that the context controls to be disposed at the end of the block.</span></span> <span data-ttu-id="370b4-128">Wenn Sie **mit**verwenden, erstellt der Compiler automatisch einen try/after-Block und ruft **die Freigabe im letzten Block auf** .</span><span class="sxs-lookup"><span data-stu-id="370b4-128">When you use **using**, the compiler automatically creates a try/finally block and calls dispose in the **finally** block.</span></span>  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

<span data-ttu-id="370b4-129">Im folgenden finden Sie einige allgemeine Richtlinien für die Entscheidung über die Lebensdauer des Kontexts:</span><span class="sxs-lookup"><span data-stu-id="370b4-129">Here are some general guidelines when deciding on the lifetime of the context:</span></span>  

- <span data-ttu-id="370b4-130">Verwenden Sie bei der Arbeit mit Webanwendungen eine Kontext Instanz pro Anforderung.</span><span class="sxs-lookup"><span data-stu-id="370b4-130">When working with Web applications, use a context instance per request.</span></span>  
- <span data-ttu-id="370b4-131">Verwenden Sie beim Arbeiten mit Windows Presentation Foundation (WPF) oder Windows Forms eine Kontext Instanz pro Formular.</span><span class="sxs-lookup"><span data-stu-id="370b4-131">When working with Windows Presentation Foundation (WPF) or Windows Forms, use a context instance per form.</span></span> <span data-ttu-id="370b4-132">Auf diese Weise können Sie Funktionen zur Änderungs Nachverfolgung verwenden, die der Kontext bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="370b4-132">This lets you use change-tracking functionality that context provides.</span></span>  
- <span data-ttu-id="370b4-133">Wenn die Kontext Instanz von einem Container für die Abhängigkeitsinjektion erstellt wird, liegt es in der Regel in der Verantwortung des Containers, den Kontext zu verwerfen.</span><span class="sxs-lookup"><span data-stu-id="370b4-133">If the context instance is created by a dependency injection container, it is usually the responsibility of the container to dispose the context.</span></span>
- <span data-ttu-id="370b4-134">Wenn der Kontext im Anwendungscode erstellt wird, sollten Sie den Kontext verwerfen, wenn er nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="370b4-134">If the context is created in application code, remember to dispose of the context when it is no longer required.</span></span>  
- <span data-ttu-id="370b4-135">Beim Arbeiten mit einem Kontext mit langer Ausführungszeit sollten Sie Folgendes beachten:</span><span class="sxs-lookup"><span data-stu-id="370b4-135">When working with long-running context consider the following:</span></span>  
    - <span data-ttu-id="370b4-136">Wenn Sie weitere Objekte und ihre Verweise in den Arbeitsspeicher laden, kann sich die Arbeitsspeicher Nutzung des Kontexts schnell erhöhen.</span><span class="sxs-lookup"><span data-stu-id="370b4-136">As you load more objects and their references into memory, the memory consumption of the context may increase rapidly.</span></span> <span data-ttu-id="370b4-137">Dies kann zu Leistungseinbußen führen.</span><span class="sxs-lookup"><span data-stu-id="370b4-137">This may cause performance issues.</span></span>  
    - <span data-ttu-id="370b4-138">Der Kontext ist nicht Thread sicher und sollte daher nicht für mehrere Threads freigegeben werden, die gleichzeitig daran arbeiten.</span><span class="sxs-lookup"><span data-stu-id="370b4-138">The context is not thread-safe, therefore it should not be shared across multiple threads doing work on it concurrently.</span></span>
    - <span data-ttu-id="370b4-139">Wenn eine Ausnahme bewirkt, dass sich der Kontext in einem nicht wiederherstellbaren Zustand befindet, kann die gesamte Anwendung beendet werden.</span><span class="sxs-lookup"><span data-stu-id="370b4-139">If an exception causes the context to be in an unrecoverable state, the whole application may terminate.</span></span>  
    - <span data-ttu-id="370b4-140">Je größer der zeitliche Abstand zwischen der Abfrage der Daten und ihrer Aktualisierung, desto höher ist die Wahrscheinlichkeit, dass Parallelitätsprobleme auftreten.</span><span class="sxs-lookup"><span data-stu-id="370b4-140">The chances of running into concurrency-related issues increase as the gap between the time when the data is queried and updated grows.</span></span>  

## <a name="connections"></a><span data-ttu-id="370b4-141">Verbindungen</span><span class="sxs-lookup"><span data-stu-id="370b4-141">Connections</span></span>  

<span data-ttu-id="370b4-142">Standardmäßig verwaltet der Kontext Verbindungen mit der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="370b4-142">By default, the context manages connections to the database.</span></span> <span data-ttu-id="370b4-143">Der Kontext wird geöffnet und schließt die Verbindungen nach Bedarf.</span><span class="sxs-lookup"><span data-stu-id="370b4-143">The context opens and closes connections as needed.</span></span> <span data-ttu-id="370b4-144">Der Kontext öffnet z. b. eine Verbindung, um eine Abfrage auszuführen, und schließt dann die Verbindung, wenn alle Resultsets verarbeitet wurden.</span><span class="sxs-lookup"><span data-stu-id="370b4-144">For example, the context opens a connection to execute a query, and then closes the connection when all the result sets have been processed.</span></span>  

<span data-ttu-id="370b4-145">In bestimmten Fällen ist es erforderlich, den Zeitpunkt für das Öffnen und Schließen der Verbindung genauer zu steuern.</span><span class="sxs-lookup"><span data-stu-id="370b4-145">There are cases when you want to have more control over when the connection opens and closes.</span></span> <span data-ttu-id="370b4-146">Wenn Sie z. b. mit SQL Server Compact arbeiten, wird häufig empfohlen, für die Lebensdauer der Anwendung eine separate offene Verbindung mit der Datenbank beizubehalten, um die Leistung zu verbessern.</span><span class="sxs-lookup"><span data-stu-id="370b4-146">For example, when working with SQL Server Compact, it is often recommended to maintain a separate open connection to the database for the lifetime of the application to improve performance.</span></span> <span data-ttu-id="370b4-147">Mit der `Connection`-Eigenschaft können Sie diesen Vorgang manuell steuern.</span><span class="sxs-lookup"><span data-stu-id="370b4-147">You can manage this process manually by using the `Connection` property.</span></span>  
