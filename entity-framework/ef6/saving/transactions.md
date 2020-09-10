---
title: Arbeiten mit Transaktionen EF6
description: Arbeiten mit Transaktionen in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 0d0f1824-d781-4cb3-8fda-b7eaefced1cd
uid: ef6/saving/transactions
ms.openlocfilehash: 65eebd82d4f9c583885af72d5b3cffd79fedf623
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619844"
---
# <a name="working-with-transactions"></a><span data-ttu-id="ab0d2-103">Arbeiten mit Transaktionen</span><span class="sxs-lookup"><span data-stu-id="ab0d2-103">Working with Transactions</span></span>
> [!NOTE]
> <span data-ttu-id="ab0d2-104">**Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="ab0d2-105">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="ab0d2-106">Dieses Dokument beschreibt die Verwendung von Transaktionen in EF6 einschließlich der Verbesserungen, die wir seit EF5 hinzugefügt haben, um die Arbeit mit Transaktionen zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-106">This document will describe using transactions in EF6 including the enhancements we have added since EF5 to make working with transactions easy.</span></span>  

## <a name="what-ef-does-by-default"></a><span data-ttu-id="ab0d2-107">Funktionsweise von EF standardmäßig</span><span class="sxs-lookup"><span data-stu-id="ab0d2-107">What EF does by default</span></span>  

<span data-ttu-id="ab0d2-108">Wenn Sie in allen Versionen von Entity Framework **SaveChanges ()** ausführen, um die Datenbank einzufügen, zu aktualisieren oder zu löschen, packt das Framework diesen Vorgang in eine Transaktion.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-108">In all versions of Entity Framework, whenever you execute **SaveChanges()** to insert, update or delete on the database the framework will wrap that operation in a transaction.</span></span> <span data-ttu-id="ab0d2-109">Diese Transaktion dauert nur lange genug, um den Vorgang auszuführen und dann abgeschlossen zu werden.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-109">This transaction lasts only long enough to execute the operation and then completes.</span></span> <span data-ttu-id="ab0d2-110">Wenn Sie einen solchen Vorgang ausführen, wird eine neue Transaktion gestartet.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-110">When you execute another such operation a new transaction is started.</span></span>  

<span data-ttu-id="ab0d2-111">Beginnend mit "EF6 **Database.Executesqlcommand ()** " wird der Befehl in einer Transaktion umschlossen, wenn noch keiner vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-111">Starting with EF6 **Database.ExecuteSqlCommand()** by default will wrap the command in a transaction if one was not already present.</span></span> <span data-ttu-id="ab0d2-112">Es gibt über Ladungen dieser Methode, die es Ihnen ermöglichen, dieses Verhalten zu überschreiben, wenn Sie möchten.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-112">There are overloads of this method that allow you to override this behavior if you wish.</span></span> <span data-ttu-id="ab0d2-113">Außerdem wird in EF6 die Ausführung gespeicherter Prozeduren, die im Modell durch APIs wie **ObjectContext.Executefunction ()** enthalten sind, identisch (mit der Ausnahme, dass das Standardverhalten im Moment nicht überschrieben werden kann).</span><span class="sxs-lookup"><span data-stu-id="ab0d2-113">Also in EF6 execution of stored procedures included in the model through APIs such as **ObjectContext.ExecuteFunction()** does the same (except that the default behavior cannot at the moment be overridden).</span></span>  

<span data-ttu-id="ab0d2-114">In beiden Fällen ist die Isolationsstufe der Transaktion unabhängig von der Isolationsstufe, die der Datenbankanbieter als Standardeinstellung ansieht.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-114">In either case, the isolation level of the transaction is whatever isolation level the database provider considers its default setting.</span></span> <span data-ttu-id="ab0d2-115">Standardmäßig wird beispielsweise auf SQL Server für die ein Lese Commit ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-115">By default, for instance, on SQL Server this is READ COMMITTED.</span></span>  

<span data-ttu-id="ab0d2-116">Entity Framework packt keine Abfragen in einer Transaktion.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-116">Entity Framework does not wrap queries in a transaction.</span></span>  

<span data-ttu-id="ab0d2-117">Diese Standardfunktionalität eignet sich für viele Benutzer, und wenn dies der Fall ist, ist es nicht erforderlich, in EF6 etwas anderes zu tun. Schreiben Sie den Code einfach wie gewohnt.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-117">This default functionality is suitable for a lot of users and if so there is no need to do anything different in EF6; just write the code as you always did.</span></span>  

<span data-ttu-id="ab0d2-118">Einige Benutzer benötigen jedoch eine bessere Kontrolle über Ihre Transaktionen – Dies wird in den folgenden Abschnitten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-118">However some users require greater control over their transactions – this is covered in the following sections.</span></span>  

## <a name="how-the-apis-work"></a><span data-ttu-id="ab0d2-119">Funktionsweise der APIs</span><span class="sxs-lookup"><span data-stu-id="ab0d2-119">How the APIs work</span></span>  

<span data-ttu-id="ab0d2-120">Vor EF6 Entity Framework das Öffnen der Datenbankverbindung selbst bestanden (es wurde eine Ausnahme ausgelöst, wenn eine bereits geöffnete Verbindung bestanden wurde).</span><span class="sxs-lookup"><span data-stu-id="ab0d2-120">Prior to EF6 Entity Framework insisted on opening the database connection itself (it threw an exception if it was passed a connection that was already open).</span></span> <span data-ttu-id="ab0d2-121">Da eine Transaktion nur für eine geöffnete Verbindung gestartet werden kann, bedeutete dies, dass ein Benutzer nur mehrere Vorgänge in eine Transaktion einschließen konnte, indem er entweder einen [transaktionscope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) verwendet oder die **ObjectContext. Connection** -Eigenschaft verwendet und mit dem Aufrufen von **Open ()** und **BeginTransaction ()** direkt auf dem zurückgegebenen **EntityConnection** -Objekt beginnt.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-121">Since a transaction can only be started on an open connection, this meant that the only way a user could wrap several operations into one transaction was either to use a [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) or use the **ObjectContext.Connection** property and start calling **Open()** and **BeginTransaction()** directly on the returned **EntityConnection** object.</span></span> <span data-ttu-id="ab0d2-122">Außerdem würden API-Aufrufe, die die Datenbank kontaktiert haben, fehlschlagen, wenn Sie eine Transaktion auf der zugrunde liegenden Datenbankverbindung selbst gestartet haben.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-122">In addition, API calls which contacted the database would fail if you had started a transaction on the underlying database connection on your own.</span></span>  

> [!NOTE]
> <span data-ttu-id="ab0d2-123">Die Einschränkung, dass nur geschlossene Verbindungen akzeptiert werden, wurde in Entity Framework 6 entfernt.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-123">The limitation of only accepting closed connections was removed in Entity Framework 6.</span></span> <span data-ttu-id="ab0d2-124">Weitere Informationen finden Sie unter [Verbindungs Verwaltung](xref:ef6/fundamentals/connection-management).</span><span class="sxs-lookup"><span data-stu-id="ab0d2-124">For details, see [Connection Management](xref:ef6/fundamentals/connection-management).</span></span>  

<span data-ttu-id="ab0d2-125">Ab EF6 bietet das Framework nun Folgendes:</span><span class="sxs-lookup"><span data-stu-id="ab0d2-125">Starting with EF6 the framework now provides:</span></span>  

1. <span data-ttu-id="ab0d2-126">**Database. BeginTransaction ()** : eine einfachere Methode für einen Benutzer, Transaktionen selbst innerhalb eines vorhandenen dbcontext zu starten und abzuschließen – so können mehrere Vorgänge in der gleichen Transaktion kombiniert werden, und es muss entweder ein Commit oder ein Rollback als ein Commit ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-126">**Database.BeginTransaction()** : An easier method for a user to start and complete transactions themselves within an existing DbContext – allowing several operations to be combined within the same transaction and hence either all committed or all rolled back as one.</span></span> <span data-ttu-id="ab0d2-127">Außerdem kann der Benutzer die Isolationsstufe für die Transaktion leichter angeben.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-127">It also allows the user to more easily specify the isolation level for the transaction.</span></span>  
2. <span data-ttu-id="ab0d2-128">**Database. UseTransaction ()** : Hiermit kann dbcontext eine Transaktion verwenden, die außerhalb der Entity Framework gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-128">**Database.UseTransaction()** : which allows the DbContext to use a transaction which was started outside of the Entity Framework.</span></span>  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a><span data-ttu-id="ab0d2-129">Kombinieren mehrerer Vorgänge zu einer Transaktion innerhalb desselben Kontexts</span><span class="sxs-lookup"><span data-stu-id="ab0d2-129">Combining several operations into one transaction within the same context</span></span>  

<span data-ttu-id="ab0d2-130">**Database. BeginTransaction ()** verfügt über zwei über schreibungen – eine, die einen expliziten [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) annimmt und einen, der keine Argumente annimmt und den standardmäßigen IsolationLevel des zugrunde liegenden Datenbankanbieters verwendet.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-130">**Database.BeginTransaction()** has two overrides – one which takes an explicit [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) and one which takes no arguments and uses the default IsolationLevel from the underlying database provider.</span></span> <span data-ttu-id="ab0d2-131">Beide über schreibungen geben ein **dbcontexttransaction** -Objekt zurück, das **Commit ()** -und **Rollback ()** -Methoden bereitstellt, die Commit und Rollback für die zugrunde liegende Speicher Transaktion ausführen.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-131">Both overrides return a **DbContextTransaction** object which provides **Commit()** and **Rollback()** methods which perform commit and rollback on the underlying store transaction.</span></span>  

<span data-ttu-id="ab0d2-132">**Dbcontexttransaction** soll verworfen werden, nachdem ein Commit oder ein Rollback ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-132">The **DbContextTransaction** is meant to be disposed once it has been committed or rolled back.</span></span> <span data-ttu-id="ab0d2-133">Eine einfache Möglichkeit hierfür ist die Verwendung von **(...) {...}**</span><span class="sxs-lookup"><span data-stu-id="ab0d2-133">One easy way to accomplish this is the **using(…) {…}**</span></span> <span data-ttu-id="ab0d2-134">Syntax, die "verwerfen **()** " automatisch aufruft, wenn der using-Block abgeschlossen wird:</span><span class="sxs-lookup"><span data-stu-id="ab0d2-134">syntax which will automatically call **Dispose()** when the using block completes:</span></span>  

``` csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        static void StartOwnTransactionWithinContext()
        {
            using (var context = new BloggingContext())
            {
                using (var dbContextTransaction = context.Database.BeginTransaction())
                {
                    context.Database.ExecuteSqlCommand(
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'"
                        );

                    var query = context.Posts.Where(p => p.Blog.Rating >= 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }

                    context.SaveChanges();

                    dbContextTransaction.Commit();
                }
            }
        }
    }
}
```  

> [!NOTE]
> <span data-ttu-id="ab0d2-135">Das Starten einer Transaktion erfordert, dass die zugrunde liegende Speicher Verbindung geöffnet ist.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-135">Beginning a transaction requires that the underlying store connection is open.</span></span> <span data-ttu-id="ab0d2-136">Wenn Sie also Database. BeginTransaction () aufrufen, wird die Verbindung geöffnet, wenn Sie nicht bereits geöffnet ist.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-136">So calling Database.BeginTransaction() will open the connection  if it is not already opened.</span></span> <span data-ttu-id="ab0d2-137">Wenn dbcontexttransaction die Verbindung geöffnet hat, wird Sie geschlossen, wenn "verwerfen ()" aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-137">If DbContextTransaction opened the connection then it will close it when Dispose() is called.</span></span>  

### <a name="passing-an-existing-transaction-to-the-context"></a><span data-ttu-id="ab0d2-138">Übergeben einer vorhandenen Transaktion an den Kontext</span><span class="sxs-lookup"><span data-stu-id="ab0d2-138">Passing an existing transaction to the context</span></span>  

<span data-ttu-id="ab0d2-139">Manchmal möchten Sie eine Transaktion, die im Bereich noch breiter ist und Vorgänge in derselben Datenbank, aber außerhalb von EF, umfasst.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-139">Sometimes you would like a transaction which is even broader in scope and which includes operations on the same database but outside of EF completely.</span></span> <span data-ttu-id="ab0d2-140">Um dies zu erreichen, müssen Sie die Verbindung öffnen und die Transaktion selbst starten und EF a dann mitteilen, dass die bereits geöffnete Datenbankverbindung verwendet werden soll, und b), um die vorhandene Transaktion für diese Verbindung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-140">To accomplish this you must open the connection and start the transaction yourself and then tell EF a) to use the already-opened database connection, and b) to use the existing transaction on that connection.</span></span>  

<span data-ttu-id="ab0d2-141">Zu diesem Zweck müssen Sie einen Konstruktor in der Kontext Klasse definieren und verwenden, die von einem der dbcontext-Konstruktoren erbt, von dem ich einen vorhandenen Verbindungsparameter und II) den booleschen Wert contextownsconnection erbt.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-141">To do this you must define and use a constructor on your context class which inherits from one of the DbContext constructors which take i) an existing connection parameter and ii) the contextOwnsConnection boolean.</span></span>  

> [!NOTE]
> <span data-ttu-id="ab0d2-142">Das contextownsconnection-Flag muss auf "false" festgelegt werden, wenn es in diesem Szenario aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-142">The contextOwnsConnection flag must be set to false when called in this scenario.</span></span> <span data-ttu-id="ab0d2-143">Dies ist wichtig, da es Entity Framework, dass die Verbindung nicht geschlossen werden soll, wenn es damit abgeschlossen ist (z. b. siehe Zeile 4 unten):</span><span class="sxs-lookup"><span data-stu-id="ab0d2-143">This is important as it informs Entity Framework that it should not close the connection when it is done with it (for example, see line 4 below):</span></span>  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

<span data-ttu-id="ab0d2-144">Außerdem müssen Sie die Transaktion selbst starten (einschließlich der IsolationLevel-Einstellung, wenn Sie die Standardeinstellung vermeiden möchten) und Entity Framework wissen, dass bereits eine Transaktion für die Verbindung gestartet wurde (siehe Zeile 33 unten).</span><span class="sxs-lookup"><span data-stu-id="ab0d2-144">Furthermore, you must start the transaction yourself (including the IsolationLevel if you want to avoid the default setting) and let Entity Framework know that there is an existing transaction already started on the connection (see line 33 below).</span></span>  

<span data-ttu-id="ab0d2-145">Anschließend können Sie Daten Bank Vorgänge entweder direkt auf der SqlConnection selbst oder im dbcontext ausführen.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-145">Then you are free to execute database operations either directly on the SqlConnection itself, or on the DbContext.</span></span> <span data-ttu-id="ab0d2-146">Alle Vorgänge dieser Art werden innerhalb einer Transaktion ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-146">All such operations are executed within one transaction.</span></span> <span data-ttu-id="ab0d2-147">Sie übernehmen die Verantwortung für das Commit oder Rollback der Transaktion und für das Aufrufen von "verwerfen ()" sowie für das Schließen und verwerfen der Datenbankverbindung.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-147">You take responsibility for committing or rolling back the transaction and for calling Dispose() on it, as well as for closing and disposing the database connection.</span></span> <span data-ttu-id="ab0d2-148">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ab0d2-148">For example:</span></span>  

``` csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
     class TransactionsExample
     {
        static void UsingExternalTransaction()
        {
            using (var conn = new SqlConnection("..."))
            {
               conn.Open();

               using (var sqlTxn = conn.BeginTransaction(System.Data.IsolationLevel.Snapshot))
               {
                   var sqlCommand = new SqlCommand();
                   sqlCommand.Connection = conn;
                   sqlCommand.Transaction = sqlTxn;
                   sqlCommand.CommandText =
                       @"UPDATE Blogs SET Rating = 5" +
                        " WHERE Name LIKE '%Entity Framework%'";
                   sqlCommand.ExecuteNonQuery();

                   using (var context =  
                     new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        context.Database.UseTransaction(sqlTxn);

                        var query =  context.Posts.Where(p => p.Blog.Rating >= 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }
                       context.SaveChanges();
                    }

                    sqlTxn.Commit();
                }
            }
        }
    }
}
```  

### <a name="clearing-up-the-transaction"></a><span data-ttu-id="ab0d2-149">Löschen der Transaktion</span><span class="sxs-lookup"><span data-stu-id="ab0d2-149">Clearing up the transaction</span></span>

<span data-ttu-id="ab0d2-150">Sie können NULL an Database. UseTransaction () übergeben, um Entity Framework Wissen der aktuellen Transaktion zu löschen.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-150">You can pass null to Database.UseTransaction() to clear Entity Framework’s knowledge of the current transaction.</span></span> <span data-ttu-id="ab0d2-151">Entity Framework für die vorhandene Transaktion weder einen Commit durchführen noch ein Rollback durchführen, verwenden Sie daher with Care und nur, wenn Sie sicher sind, dass dies alles ist, was Sie tun möchten.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-151">Entity Framework will neither commit nor rollback the existing transaction when you do this, so use with care and only if you’re sure this is what you want to do.</span></span>  

### <a name="errors-in-usetransaction"></a><span data-ttu-id="ab0d2-152">Fehler in UseTransaction</span><span class="sxs-lookup"><span data-stu-id="ab0d2-152">Errors in UseTransaction</span></span>

<span data-ttu-id="ab0d2-153">Es wird eine Ausnahme von Database. UseTransaction () angezeigt, wenn Sie eine Transaktion übergeben, wenn Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="ab0d2-153">You will see an exception from Database.UseTransaction() if you pass a transaction when:</span></span>  
- <span data-ttu-id="ab0d2-154">Entity Framework bereits eine Transaktion vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-154">Entity Framework already has an existing transaction</span></span>  
- <span data-ttu-id="ab0d2-155">Entity Framework wird bereits in einem Transaktions Bereich ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-155">Entity Framework is already operating within a TransactionScope</span></span>  
- <span data-ttu-id="ab0d2-156">Das Verbindungs Objekt in der Transaktion hat den Wert NULL.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-156">The connection object in the transaction passed is null.</span></span> <span data-ttu-id="ab0d2-157">Das heißt, die Transaktion ist keiner Verbindung zugeordnet – normalerweise ist dies ein Vorzeichen, dass diese Transaktion bereits abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-157">That is, the transaction is not associated with a connection – usually this is a sign that that transaction has already completed</span></span>  
- <span data-ttu-id="ab0d2-158">Das Verbindungs Objekt in der übergebenen Transaktion entspricht nicht der Verbindung des Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-158">The connection object in the transaction passed does not match the Entity Framework’s connection.</span></span>  

## <a name="using-transactions-with-other-features"></a><span data-ttu-id="ab0d2-159">Verwenden von Transaktionen mit anderen Features</span><span class="sxs-lookup"><span data-stu-id="ab0d2-159">Using transactions with other features</span></span>  

<span data-ttu-id="ab0d2-160">In diesem Abschnitt wird erläutert, wie die obigen Transaktionen mit interagieren:</span><span class="sxs-lookup"><span data-stu-id="ab0d2-160">This section details how the above transactions interact with:</span></span>  

- <span data-ttu-id="ab0d2-161">Verbindungsstabilität</span><span class="sxs-lookup"><span data-stu-id="ab0d2-161">Connection resiliency</span></span>  
- <span data-ttu-id="ab0d2-162">Asynchrone Methoden</span><span class="sxs-lookup"><span data-stu-id="ab0d2-162">Asynchronous methods</span></span>  
- <span data-ttu-id="ab0d2-163">Transaktionscope-Transaktionen</span><span class="sxs-lookup"><span data-stu-id="ab0d2-163">TransactionScope transactions</span></span>  

### <a name="connection-resiliency"></a><span data-ttu-id="ab0d2-164">Verbindungsstabilität</span><span class="sxs-lookup"><span data-stu-id="ab0d2-164">Connection Resiliency</span></span>  

<span data-ttu-id="ab0d2-165">Das neue Feature für die Verbindungs Resilienz funktioniert nicht mit vom Benutzer initiierten Transaktionen.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-165">The new Connection Resiliency feature does not work with user-initiated transactions.</span></span> <span data-ttu-id="ab0d2-166">Weitere Informationen finden Sie unter [Wiederholungs Versuche für Ausführungs Strategien](xref:ef6/fundamentals/connection-resiliency/retry-logic#user-initiated-transactions-are-not-supported).</span><span class="sxs-lookup"><span data-stu-id="ab0d2-166">For details, see [Retrying Execution Strategies](xref:ef6/fundamentals/connection-resiliency/retry-logic#user-initiated-transactions-are-not-supported).</span></span>  

### <a name="asynchronous-programming"></a><span data-ttu-id="ab0d2-167">Asynchrone Programmierung</span><span class="sxs-lookup"><span data-stu-id="ab0d2-167">Asynchronous Programming</span></span>  

<span data-ttu-id="ab0d2-168">Der Ansatz, der in den vorherigen Abschnitten beschrieben wird, benötigt keine weiteren Optionen oder Einstellungen für die Verwendung der [asynchronen Abfrage-und Speichermethoden](xref:ef6/fundamentals/async
).</span><span class="sxs-lookup"><span data-stu-id="ab0d2-168">The approach outlined in the previous sections needs no further options or settings to work with the [asynchronous query and save methods](xref:ef6/fundamentals/async
).</span></span> <span data-ttu-id="ab0d2-169">Beachten Sie jedoch, dass dies in Abhängigkeit davon, was Sie in den asynchronen Methoden tun, dazu führen kann, dass Transaktionen mit langer Ausführungszeit ausgeführt werden – was wiederum zu Deadlocks oder Blockierungen führen kann, was für die Leistung der gesamten Anwendung schlecht ist.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-169">But be aware that, depending on what you do within the asynchronous methods, this may result in long-running transactions – which can in turn cause deadlocks or blocking which is bad for the performance of the overall application.</span></span>  

### <a name="transactionscope-transactions"></a><span data-ttu-id="ab0d2-170">Transaktionscope-Transaktionen</span><span class="sxs-lookup"><span data-stu-id="ab0d2-170">TransactionScope Transactions</span></span>  

<span data-ttu-id="ab0d2-171">Vor EF6 war die empfohlene Vorgehensweise zum Bereitstellen größerer Bereichs Transaktionen die Verwendung eines transaktionscope-Objekts:</span><span class="sxs-lookup"><span data-stu-id="ab0d2-171">Prior to EF6 the recommended way of providing larger scope transactions was to use a TransactionScope object:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        static void UsingTransactionScope()
        {
            using (var scope = new TransactionScope(TransactionScopeOption.Required))
            {
                using (var conn = new SqlConnection("..."))
                {
                    conn.Open();

                    var sqlCommand = new SqlCommand();
                    sqlCommand.Connection = conn;
                    sqlCommand.CommandText =
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'";
                    sqlCommand.ExecuteNonQuery();

                    using (var context =
                        new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        var query = context.Posts.Where(p => p.Blog.Rating > 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }
                        context.SaveChanges();
                    }
                }

                scope.Complete();
            }
        }
    }
}
```  

<span data-ttu-id="ab0d2-172">Der SqlConnection-und der-Entity Framework würden beide die Ambient-Transaktion transaktionscope verwenden und daher zusammen ein Commit ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-172">The SqlConnection and Entity Framework would both use the ambient TransactionScope transaction and hence be committed together.</span></span>  

<span data-ttu-id="ab0d2-173">Beginnend mit .NET 4.5.1 transaktionscope wurde aktualisiert und funktioniert auch mit asynchronen Methoden über die [transaktionscopeasyncflowoption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) -Enumeration:</span><span class="sxs-lookup"><span data-stu-id="ab0d2-173">Starting with .NET 4.5.1 TransactionScope has been updated to also work with asynchronous methods via the use of the [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) enumeration:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        public static void AsyncTransactionScope()
        {
            using (var scope = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
            {
                using (var conn = new SqlConnection("..."))
                {
                    await conn.OpenAsync();

                    var sqlCommand = new SqlCommand();
                    sqlCommand.Connection = conn;
                    sqlCommand.CommandText =
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'";
                    await sqlCommand.ExecuteNonQueryAsync();

                    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        var query = context.Posts.Where(p => p.Blog.Rating > 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }

                        await context.SaveChangesAsync();
                    }
                }
            }
        }
    }
}
```  

<span data-ttu-id="ab0d2-174">Es gibt noch einige Einschränkungen für den transaktionscope-Ansatz:</span><span class="sxs-lookup"><span data-stu-id="ab0d2-174">There are still some limitations to the TransactionScope approach:</span></span>  

- <span data-ttu-id="ab0d2-175">Erfordert .NET 4.5.1 oder höher, um mit asynchronen Methoden zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-175">Requires .NET 4.5.1 or greater to work with asynchronous methods.</span></span>  
- <span data-ttu-id="ab0d2-176">Sie kann nur in cloudumgebungen verwendet werden, wenn Sie sicher sind, dass Sie über eine und nur eine Verbindung verfügen (cloudszenarien unterstützen keine verteilten Transaktionen).</span><span class="sxs-lookup"><span data-stu-id="ab0d2-176">It cannot be used in cloud scenarios unless you are sure you have one and only one connection (cloud scenarios do not support distributed transactions).</span></span>  
- <span data-ttu-id="ab0d2-177">Sie kann nicht mit dem Database. UseTransaction ()-Ansatz der vorherigen Abschnitte kombiniert werden.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-177">It cannot be combined with the Database.UseTransaction() approach of the previous sections.</span></span>  
- <span data-ttu-id="ab0d2-178">Sie löst Ausnahmen aus, wenn Sie DDL ausgeben und verteilte Transaktionen nicht über den MSDTC-Dienst aktiviert haben.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-178">It will throw exceptions if you issue any DDL and have not enabled distributed transactions through the MSDTC Service.</span></span>  

<span data-ttu-id="ab0d2-179">Vorteile des transaktionscope-Ansatzes:</span><span class="sxs-lookup"><span data-stu-id="ab0d2-179">Advantages of the TransactionScope approach:</span></span>  

- <span data-ttu-id="ab0d2-180">Eine lokale Transaktion wird automatisch auf eine verteilte Transaktion aktualisiert, wenn Sie mehr als eine Verbindung mit einer bestimmten Datenbank herstellen oder eine Verbindung mit einer Datenbank mit einer Verbindung mit einer anderen Datenbank innerhalb derselben Transaktion herstellen (Hinweis: der MSDTC-Dienst muss so konfiguriert sein, dass verteilte Transaktionen damit funktionieren).</span><span class="sxs-lookup"><span data-stu-id="ab0d2-180">It will automatically upgrade a local transaction to a distributed transaction if you make more than one connection to a given database or combine a connection to one database with a connection to a different database within the same transaction (note: you must have the MSDTC service configured to allow distributed transactions for this to work).</span></span>  
- <span data-ttu-id="ab0d2-181">Einfache Codierung.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-181">Ease of coding.</span></span> <span data-ttu-id="ab0d2-182">Wenn Sie möchten, dass die Transaktion Ambient ist und implizit im Hintergrund und nicht explizit unter ihrer Kontrolle behandelt wird, ist der transaktionscope-Ansatz möglicherweise besser geeignet.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-182">If you prefer the transaction to be ambient and dealt with implicitly in the background rather than explicitly under you control then the TransactionScope approach may suit you better.</span></span>  

<span data-ttu-id="ab0d2-183">Zusammengefasst mit den neuen APIs "Database. BeginTransaction ()" und "Database. UseTransaction ()", ist der transaktionscope-Ansatz für die meisten Benutzer nicht mehr erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-183">In summary, with the new Database.BeginTransaction() and Database.UseTransaction() APIs above, the TransactionScope approach is no longer necessary for most users.</span></span> <span data-ttu-id="ab0d2-184">Wenn Sie transaktionscope weiterhin verwenden, beachten Sie die oben genannten Einschränkungen.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-184">If you do continue to use TransactionScope then be aware of the above limitations.</span></span> <span data-ttu-id="ab0d2-185">Wir empfehlen, nach Möglichkeit den in den vorherigen Abschnitten beschriebenen Ansatz zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="ab0d2-185">We recommend using the approach outlined in the previous sections instead where possible.</span></span>  
