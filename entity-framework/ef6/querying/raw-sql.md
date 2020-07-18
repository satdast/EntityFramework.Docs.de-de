---
title: RAW-SQL-Abfragen EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9e1ee76e-2499-408c-81e8-9b6c5d1945a0
ms.openlocfilehash: d336066b982e682e81067bbdac5b3781524cf6be
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451215"
---
# <a name="raw-sql-queries-ef6"></a><span data-ttu-id="4026a-102">Unformatierte SQL-Abfragen (EF6)</span><span class="sxs-lookup"><span data-stu-id="4026a-102">Raw SQL Queries (EF6)</span></span>

<span data-ttu-id="4026a-103">Entity Framework ermöglicht es Ihnen, mithilfe von LINQ mit ihren Entitäts Klassen abzufragen.</span><span class="sxs-lookup"><span data-stu-id="4026a-103">Entity Framework allows you to query using LINQ with your entity classes.</span></span> <span data-ttu-id="4026a-104">Es kann jedoch vorkommen, dass Sie Abfragen mit unformatierten SQL-Daten direkt für die Datenbank ausführen möchten.</span><span class="sxs-lookup"><span data-stu-id="4026a-104">However, there may be times that you want to run queries using raw SQL directly against the database.</span></span> <span data-ttu-id="4026a-105">Dies schließt das Aufrufen von gespeicherten Prozeduren ein, was für Code First Modelle hilfreich sein kann, die derzeit keine Zuordnung zu gespeicherten Prozeduren unterstützen.</span><span class="sxs-lookup"><span data-stu-id="4026a-105">This includes calling stored procedures, which can be helpful for Code First models that currently do not support mapping to stored procedures.</span></span> <span data-ttu-id="4026a-106">Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="4026a-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="writing-sql-queries-for-entities"></a><span data-ttu-id="4026a-107">Schreiben von SQL-Abfragen für Entitäten</span><span class="sxs-lookup"><span data-stu-id="4026a-107">Writing SQL queries for entities</span></span>  

<span data-ttu-id="4026a-108">Die sqlQuery-Methode in dbset ermöglicht das Schreiben einer unformatierten SQL-Abfrage, die Entitäts Instanzen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="4026a-108">The SqlQuery method on DbSet allows a raw SQL query to be written that will return entity instances.</span></span> <span data-ttu-id="4026a-109">Die zurückgegebenen Objekte werden vom Kontext genau so nachverfolgt, als wären Sie durch eine LINQ-Abfrage zurückgegeben worden.</span><span class="sxs-lookup"><span data-stu-id="4026a-109">The returned objects will be tracked by the context just as they would be if they were returned by a LINQ query.</span></span> <span data-ttu-id="4026a-110">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4026a-110">For example:</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="4026a-111">Beachten Sie, dass die Abfrage wie bei LINQ-Abfragen erst ausgeführt wird, wenn die Ergebnisse aufgezählt werden – im obigen Beispiel wird dies mit dem Auflistungs Auflistungs Vorgang durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="4026a-111">Note that, just as for LINQ queries, the query is not executed until the results are enumerated—in the example above this is done with the call to ToList.</span></span>  

<span data-ttu-id="4026a-112">Es muss sorgfältig vorgegangen werden, wenn unformatierte SQL-Abfragen aus zwei Gründen geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="4026a-112">Care should be taken whenever raw SQL queries are written for two reasons.</span></span> <span data-ttu-id="4026a-113">Zuerst muss die Abfrage geschrieben werden, um sicherzustellen, dass nur Entitäten zurückgegeben werden, die tatsächlich den angeforderten Typ aufweisen.</span><span class="sxs-lookup"><span data-stu-id="4026a-113">First, the query should be written to ensure that it only returns entities that are really of the requested type.</span></span> <span data-ttu-id="4026a-114">Wenn z. b. Funktionen wie die Vererbung verwendet werden, ist es einfach, eine Abfrage zu schreiben, mit der Entitäten des falschen CLR-Typs erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="4026a-114">For example, when using features such as inheritance it is easy to write a query that will create entities that are of the wrong CLR type.</span></span>  

<span data-ttu-id="4026a-115">Zweitens machen einige Typen von unformatierten SQL-Abfragen potenzielle Sicherheitsrisiken offen, insbesondere bei SQL Injection-Angriffen.</span><span class="sxs-lookup"><span data-stu-id="4026a-115">Second, some types of raw SQL query expose potential security risks, especially around SQL injection attacks.</span></span> <span data-ttu-id="4026a-116">Stellen Sie sicher, dass Sie die Parameter in der Abfrage auf die richtige Weise verwenden, um solche Angriffe zu schützen.</span><span class="sxs-lookup"><span data-stu-id="4026a-116">Make sure that you use parameters in your query in the correct way to guard against such attacks.</span></span>  

### <a name="loading-entities-from-stored-procedures"></a><span data-ttu-id="4026a-117">Entitäten aus gespeicherten Prozeduren laden</span><span class="sxs-lookup"><span data-stu-id="4026a-117">Loading entities from stored procedures</span></span>  

<span data-ttu-id="4026a-118">Mit dbset. sqlQuery können Sie Entitäten aus den Ergebnissen einer gespeicherten Prozedur laden.</span><span class="sxs-lookup"><span data-stu-id="4026a-118">You can use DbSet.SqlQuery to load entities from the results of a stored procedure.</span></span> <span data-ttu-id="4026a-119">Der folgende Code ruft beispielsweise den dbo auf. Getblogs-Prozedur in der-Datenbank:</span><span class="sxs-lookup"><span data-stu-id="4026a-119">For example, the following code calls the dbo.GetBlogs procedure in the database:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

<span data-ttu-id="4026a-120">Sie können Parameter auch an eine gespeicherte Prozedur übergeben, indem Sie die folgende Syntax verwenden:</span><span class="sxs-lookup"><span data-stu-id="4026a-120">You can also pass parameters to a stored procedure using the following syntax:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a><span data-ttu-id="4026a-121">Schreiben von SQL-Abfragen für nicht-Entitäts Typen</span><span class="sxs-lookup"><span data-stu-id="4026a-121">Writing SQL queries for non-entity types</span></span>  

<span data-ttu-id="4026a-122">Eine SQL-Abfrage, die Instanzen eines beliebigen Typs zurückgibt, einschließlich primitiver Typen, kann mithilfe der sqlQuery-Methode für die Datenbankklasse erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="4026a-122">A SQL query returning instances of any type, including primitive types, can be created using the SqlQuery method on the Database class.</span></span> <span data-ttu-id="4026a-123">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4026a-123">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="4026a-124">Die von sqlQuery für die Datenbank zurückgegebenen Ergebnisse werden nie vom Kontext nachverfolgt, auch wenn die Objekte Instanzen eines Entitäts Typs sind.</span><span class="sxs-lookup"><span data-stu-id="4026a-124">The results returned from SqlQuery on Database will never be tracked by the context even if the objects are instances of an entity type.</span></span>  

## <a name="sending-raw-commands-to-the-database"></a><span data-ttu-id="4026a-125">Senden von rohbefehlen an die Datenbank</span><span class="sxs-lookup"><span data-stu-id="4026a-125">Sending raw commands to the database</span></span>  

<span data-ttu-id="4026a-126">Nicht-Abfrage Befehle können mithilfe der ExecuteSqlCommand-Methode für die Datenbank an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="4026a-126">Non-query commands can be sent to the database using the ExecuteSqlCommand method on Database.</span></span> <span data-ttu-id="4026a-127">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4026a-127">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

<span data-ttu-id="4026a-128">Beachten Sie, dass alle Änderungen, die an Daten in der Datenbank mithilfe von ExecuteSqlCommand vorgenommen werden, für den Kontext nicht transparent sind, bis Entitäten geladen oder aus der Datenbank erneut geladen werden.</span><span class="sxs-lookup"><span data-stu-id="4026a-128">Note that any changes made to data in the database using ExecuteSqlCommand are opaque to the context until entities are loaded or reloaded from the database.</span></span>  

### <a name="output-parameters"></a><span data-ttu-id="4026a-129">Ausgabeparameter</span><span class="sxs-lookup"><span data-stu-id="4026a-129">Output Parameters</span></span>  

<span data-ttu-id="4026a-130">Wenn Output-Parameter verwendet werden, sind ihre Werte erst verfügbar, wenn die Ergebnisse vollständig gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="4026a-130">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="4026a-131">Dies ist auf das zugrunde liegende Verhalten von DbDataReader zurückzuführen. Weitere Informationen finden [Sie unter Abrufen von Daten mithilfe eines DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) .</span><span class="sxs-lookup"><span data-stu-id="4026a-131">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>  
