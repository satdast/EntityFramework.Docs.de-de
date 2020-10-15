---
title: RAW-SQL-Abfragen EF6
description: Unformatierte SQL-Abfragen in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/raw-sql
ms.openlocfilehash: da813ede818b24a5e7930202bfa761d65e4e6b72
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064717"
---
# <a name="raw-sql-queries-ef6"></a><span data-ttu-id="e6755-103">Unformatierte SQL-Abfragen (EF6)</span><span class="sxs-lookup"><span data-stu-id="e6755-103">Raw SQL Queries (EF6)</span></span>

<span data-ttu-id="e6755-104">Entity Framework ermöglicht es Ihnen, mithilfe von LINQ mit ihren Entitäts Klassen abzufragen.</span><span class="sxs-lookup"><span data-stu-id="e6755-104">Entity Framework allows you to query using LINQ with your entity classes.</span></span> <span data-ttu-id="e6755-105">Es kann jedoch vorkommen, dass Sie Abfragen mit unformatierten SQL-Daten direkt für die Datenbank ausführen möchten.</span><span class="sxs-lookup"><span data-stu-id="e6755-105">However, there may be times that you want to run queries using raw SQL directly against the database.</span></span> <span data-ttu-id="e6755-106">Dies schließt das Aufrufen von gespeicherten Prozeduren ein, was für Code First Modelle hilfreich sein kann, die derzeit keine Zuordnung zu gespeicherten Prozeduren unterstützen.</span><span class="sxs-lookup"><span data-stu-id="e6755-106">This includes calling stored procedures, which can be helpful for Code First models that currently do not support mapping to stored procedures.</span></span> <span data-ttu-id="e6755-107">Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="e6755-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="writing-sql-queries-for-entities"></a><span data-ttu-id="e6755-108">Schreiben von SQL-Abfragen für Entitäten</span><span class="sxs-lookup"><span data-stu-id="e6755-108">Writing SQL queries for entities</span></span>  

<span data-ttu-id="e6755-109">Die sqlQuery-Methode in dbset ermöglicht das Schreiben einer unformatierten SQL-Abfrage, die Entitäts Instanzen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="e6755-109">The SqlQuery method on DbSet allows a raw SQL query to be written that will return entity instances.</span></span> <span data-ttu-id="e6755-110">Die zurückgegebenen Objekte werden vom Kontext genau so nachverfolgt, als wären Sie durch eine LINQ-Abfrage zurückgegeben worden.</span><span class="sxs-lookup"><span data-stu-id="e6755-110">The returned objects will be tracked by the context just as they would be if they were returned by a LINQ query.</span></span> <span data-ttu-id="e6755-111">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e6755-111">For example:</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="e6755-112">Beachten Sie, dass die Abfrage wie bei LINQ-Abfragen erst ausgeführt wird, wenn die Ergebnisse aufgezählt werden – im obigen Beispiel wird dies mit dem Auflistungs Auflistungs Vorgang durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="e6755-112">Note that, just as for LINQ queries, the query is not executed until the results are enumerated—in the example above this is done with the call to ToList.</span></span>  

<span data-ttu-id="e6755-113">Es muss sorgfältig vorgegangen werden, wenn unformatierte SQL-Abfragen aus zwei Gründen geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="e6755-113">Care should be taken whenever raw SQL queries are written for two reasons.</span></span> <span data-ttu-id="e6755-114">Zuerst muss die Abfrage geschrieben werden, um sicherzustellen, dass nur Entitäten zurückgegeben werden, die tatsächlich den angeforderten Typ aufweisen.</span><span class="sxs-lookup"><span data-stu-id="e6755-114">First, the query should be written to ensure that it only returns entities that are really of the requested type.</span></span> <span data-ttu-id="e6755-115">Wenn z. b. Funktionen wie die Vererbung verwendet werden, ist es einfach, eine Abfrage zu schreiben, mit der Entitäten des falschen CLR-Typs erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="e6755-115">For example, when using features such as inheritance it is easy to write a query that will create entities that are of the wrong CLR type.</span></span>  

<span data-ttu-id="e6755-116">Zweitens machen einige Typen von unformatierten SQL-Abfragen potenzielle Sicherheitsrisiken offen, insbesondere bei SQL Injection-Angriffen.</span><span class="sxs-lookup"><span data-stu-id="e6755-116">Second, some types of raw SQL query expose potential security risks, especially around SQL injection attacks.</span></span> <span data-ttu-id="e6755-117">Stellen Sie sicher, dass Sie die Parameter in der Abfrage auf die richtige Weise verwenden, um solche Angriffe zu schützen.</span><span class="sxs-lookup"><span data-stu-id="e6755-117">Make sure that you use parameters in your query in the correct way to guard against such attacks.</span></span>  

### <a name="loading-entities-from-stored-procedures"></a><span data-ttu-id="e6755-118">Entitäten aus gespeicherten Prozeduren laden</span><span class="sxs-lookup"><span data-stu-id="e6755-118">Loading entities from stored procedures</span></span>  

<span data-ttu-id="e6755-119">Mit dbset. sqlQuery können Sie Entitäten aus den Ergebnissen einer gespeicherten Prozedur laden.</span><span class="sxs-lookup"><span data-stu-id="e6755-119">You can use DbSet.SqlQuery to load entities from the results of a stored procedure.</span></span> <span data-ttu-id="e6755-120">Der folgende Code ruft beispielsweise den dbo auf. Getblogs-Prozedur in der-Datenbank:</span><span class="sxs-lookup"><span data-stu-id="e6755-120">For example, the following code calls the dbo.GetBlogs procedure in the database:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

<span data-ttu-id="e6755-121">Sie können Parameter auch an eine gespeicherte Prozedur übergeben, indem Sie die folgende Syntax verwenden:</span><span class="sxs-lookup"><span data-stu-id="e6755-121">You can also pass parameters to a stored procedure using the following syntax:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a><span data-ttu-id="e6755-122">Schreiben von SQL-Abfragen für nicht-Entitäts Typen</span><span class="sxs-lookup"><span data-stu-id="e6755-122">Writing SQL queries for non-entity types</span></span>  

<span data-ttu-id="e6755-123">Eine SQL-Abfrage, die Instanzen eines beliebigen Typs zurückgibt, einschließlich primitiver Typen, kann mithilfe der sqlQuery-Methode für die Datenbankklasse erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="e6755-123">A SQL query returning instances of any type, including primitive types, can be created using the SqlQuery method on the Database class.</span></span> <span data-ttu-id="e6755-124">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e6755-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="e6755-125">Die von sqlQuery für die Datenbank zurückgegebenen Ergebnisse werden nie vom Kontext nachverfolgt, auch wenn die Objekte Instanzen eines Entitäts Typs sind.</span><span class="sxs-lookup"><span data-stu-id="e6755-125">The results returned from SqlQuery on Database will never be tracked by the context even if the objects are instances of an entity type.</span></span>  

## <a name="sending-raw-commands-to-the-database"></a><span data-ttu-id="e6755-126">Senden von rohbefehlen an die Datenbank</span><span class="sxs-lookup"><span data-stu-id="e6755-126">Sending raw commands to the database</span></span>  

<span data-ttu-id="e6755-127">Nicht-Abfrage Befehle können mithilfe der ExecuteSqlCommand-Methode für die Datenbank an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="e6755-127">Non-query commands can be sent to the database using the ExecuteSqlCommand method on Database.</span></span> <span data-ttu-id="e6755-128">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e6755-128">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

<span data-ttu-id="e6755-129">Beachten Sie, dass alle Änderungen, die an Daten in der Datenbank mithilfe von ExecuteSqlCommand vorgenommen werden, für den Kontext nicht transparent sind, bis Entitäten geladen oder aus der Datenbank erneut geladen werden.</span><span class="sxs-lookup"><span data-stu-id="e6755-129">Note that any changes made to data in the database using ExecuteSqlCommand are opaque to the context until entities are loaded or reloaded from the database.</span></span>  

### <a name="output-parameters"></a><span data-ttu-id="e6755-130">Ausgabeparameter</span><span class="sxs-lookup"><span data-stu-id="e6755-130">Output Parameters</span></span>  

<span data-ttu-id="e6755-131">Wenn Output-Parameter verwendet werden, sind ihre Werte erst verfügbar, wenn die Ergebnisse vollständig gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="e6755-131">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="e6755-132">Dies ist auf das zugrunde liegende Verhalten von DbDataReader zurückzuführen. Weitere Informationen finden [Sie unter Abrufen von Daten mithilfe eines DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) .</span><span class="sxs-lookup"><span data-stu-id="e6755-132">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>  
