---
title: Behandeln von Transaktionscommitfehlern - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
ms.openlocfilehash: cf2722496e207a8ecaa9cfaa4ca61e7248e5e58f
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434135"
---
# <a name="handling-transaction-commit-failures"></a><span data-ttu-id="944fe-102">Behandeln von Transaktionscommitfehlern</span><span class="sxs-lookup"><span data-stu-id="944fe-102">Handling transaction commit failures</span></span>
> [!NOTE]
> <span data-ttu-id="944fe-103">**EF6.1 Nur ab -** Die auf dieser Seite diskutierten Features, APIs usw. wurden in Entity Framework 6.1 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="944fe-103">**EF6.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="944fe-104">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="944fe-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="944fe-105">Im Rahmen von 6.1 führen wir eine neue Verbindungsresilienzfunktion für EF ein: die Fähigkeit, automatisch zu erkennen und wiederherzustellen, wenn vorübergehende Verbindungsfehler die Bestätigung von Transaktionscommits beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="944fe-105">As part of 6.1 we are introducing a new connection resiliency feature for EF: the ability to detect and recover automatically when transient connection failures affect the acknowledgement of transaction commits.</span></span> <span data-ttu-id="944fe-106">Die vollständigen Details des Szenarios werden am besten im Blogbeitrag [SQL Database Connectivity und das Idempotency Issue](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)beschrieben.</span><span class="sxs-lookup"><span data-stu-id="944fe-106">The full details of the scenario are best described in the blog post [SQL Database Connectivity and the Idempotency Issue](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).</span></span>  <span data-ttu-id="944fe-107">Zusammenfassend lässt sich sagen, dass beim Auslösen einer Ausnahme während eines Transaktionscommits zwei mögliche Ursachen vorliegen:</span><span class="sxs-lookup"><span data-stu-id="944fe-107">In summary, the scenario is that when an exception is raised during a transaction commit there are two possible causes:</span></span>  

1. <span data-ttu-id="944fe-108">Der Transaktionscommit ist auf dem Server fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="944fe-108">The transaction commit failed on the server</span></span>
2. <span data-ttu-id="944fe-109">Der Transaktionscommit war auf dem Server erfolgreich, aber ein Verbindungsproblem verhinderte, dass die Erfolgsbenachrichtigung den Client erreichte.</span><span class="sxs-lookup"><span data-stu-id="944fe-109">The transaction commit succeeded on the server but a connectivity issue prevented the success notification from reaching the client</span></span>  

<span data-ttu-id="944fe-110">Wenn die erste Situation eintritt, kann die Anwendung oder der Benutzer den Vorgang wiederholen, aber wenn die zweite Situation auftritt, sollten Wiederholungen vermieden werden, und die Anwendung kann automatisch wiederhergestellt werden.</span><span class="sxs-lookup"><span data-stu-id="944fe-110">When the first situation happens the application or the user can retry the operation, but when the second situation occurs retries should be avoided and the application could recover automatically.</span></span> <span data-ttu-id="944fe-111">Die Herausforderung besteht darin, dass die Anwendung ohne die Möglichkeit, den eigentlichen Grund zu erkennen, warum eine Ausnahme während des Commits gemeldet wurde, nicht die richtige Vorgehensweise wählen kann.</span><span class="sxs-lookup"><span data-stu-id="944fe-111">The challenge is that without the ability to detect what was the actual reason an exception was reported during commit, the application cannot choose the right course of action.</span></span> <span data-ttu-id="944fe-112">Die neue Funktion in EF 6.1 ermöglicht es EF, die Datenbank zu überprüfen, wenn die Transaktion erfolgreich war, und die richtige Vorgehensweise transparent zu verfolgen.</span><span class="sxs-lookup"><span data-stu-id="944fe-112">The new feature in EF 6.1 allows EF to double-check with the database if the transaction succeeded and take the right course of action transparently.</span></span>  

## <a name="using-the-feature"></a><span data-ttu-id="944fe-113">Verwenden der Funktion</span><span class="sxs-lookup"><span data-stu-id="944fe-113">Using the feature</span></span>  

<span data-ttu-id="944fe-114">Um die Funktion zu aktivieren, müssen Sie einen Aufruf von [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in den Konstruktor Ihrer **DbConfiguration**einschließen.</span><span class="sxs-lookup"><span data-stu-id="944fe-114">In order to enable the feature you need include a call to [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in the constructor of your **DbConfiguration**.</span></span> <span data-ttu-id="944fe-115">Wenn Sie mit **DbConfiguration**nicht vertraut sind, finden Sie weitere Informationen unter [Codebasierte Konfiguration](~/ef6/fundamentals/configuring/code-based.md).</span><span class="sxs-lookup"><span data-stu-id="944fe-115">If you are unfamiliar with **DbConfiguration**, see [Code Based Configuration](~/ef6/fundamentals/configuring/code-based.md).</span></span> <span data-ttu-id="944fe-116">Diese Funktion kann in Kombination mit den automatischen Wiederholungen verwendet werden, die wir in EF6 eingeführt haben, was in der Situation hilft, in der die Transaktion aufgrund eines vorübergehenden Fehlers tatsächlich nicht auf dem Server übertragen werden konnte:</span><span class="sxs-lookup"><span data-stu-id="944fe-116">This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

public class MyConfiguration : DbConfiguration  
{
  public MyConfiguration()  
  {  
    SetTransactionHandler(SqlProviderServices.ProviderInvariantName, () => new CommitFailureHandler());  
    SetExecutionStrategy(SqlProviderServices.ProviderInvariantName, () => new SqlAzureExecutionStrategy());  
  }  
}
```  

## <a name="how-transactions-are-tracked"></a><span data-ttu-id="944fe-117">Wie Transaktionen nachverfolgt werden</span><span class="sxs-lookup"><span data-stu-id="944fe-117">How transactions are tracked</span></span>  

<span data-ttu-id="944fe-118">Wenn das Feature aktiviert ist, fügt EF automatisch eine neue Tabelle zur Datenbank mit dem Namen **__Transactions**hinzu.</span><span class="sxs-lookup"><span data-stu-id="944fe-118">When the feature is enabled, EF will automatically add a new table to the database called **__Transactions**.</span></span> <span data-ttu-id="944fe-119">Jedes Mal, wenn eine Transaktion von EF erstellt wird und diese Zeile auf vorhanden überprüft wird, wenn während des Commits ein Transaktionsfehler auftritt, wird in diese Tabelle eine neue Zeile eingefügt.</span><span class="sxs-lookup"><span data-stu-id="944fe-119">A new row is inserted in this table every time a transaction is created by EF and that row is checked for existence if a transaction failure occurs during commit.</span></span>  

<span data-ttu-id="944fe-120">Obwohl EF die meisten Anstrengungen unternimmt, um Zeilen aus der Tabelle zu löschen, wenn sie nicht mehr benötigt werden, kann die Tabelle wachsen, wenn die Anwendung vorzeitig beendet wird, und aus diesem Grund müssen Sie die Tabelle in einigen Fällen manuell löschen.</span><span class="sxs-lookup"><span data-stu-id="944fe-120">Although EF will do a best effort to prune rows from the table when they aren’t needed anymore, the table can grow if the application exits prematurely and for that reason you may need to purge the table manually in some cases.</span></span>  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a><span data-ttu-id="944fe-121">Umgang mit Commit-Fehlern mit früheren Versionen</span><span class="sxs-lookup"><span data-stu-id="944fe-121">How to handle commit failures with previous Versions</span></span>

<span data-ttu-id="944fe-122">Vor EF 6.1 gab es keinen Mechanismus zum Behandeln von Commitfehlern im EF-Produkt.</span><span class="sxs-lookup"><span data-stu-id="944fe-122">Before EF 6.1 there was not mechanism to handle commit failures in the EF product.</span></span> <span data-ttu-id="944fe-123">Es gibt mehrere Möglichkeiten, mit dieser Situation umzugehen, die auf frühere Versionen von EF6 angewendet werden können:</span><span class="sxs-lookup"><span data-stu-id="944fe-123">There are several ways to dealing with this situation that can be applied to previous versions of EF6:</span></span>  

* <span data-ttu-id="944fe-124">Option 1 - Nichts tun</span><span class="sxs-lookup"><span data-stu-id="944fe-124">Option 1 - Do nothing</span></span>  

  <span data-ttu-id="944fe-125">Die Wahrscheinlichkeit eines Verbindungsfehlers während des Transaktionscommits ist gering, sodass es für Ihre Anwendung möglicherweise akzeptabel ist, nur fehlschlägt, wenn diese Bedingung tatsächlich auftritt.</span><span class="sxs-lookup"><span data-stu-id="944fe-125">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>  

* <span data-ttu-id="944fe-126">Option 2 - Verwenden sie die Datenbank, um den Status zurückzusetzen</span><span class="sxs-lookup"><span data-stu-id="944fe-126">Option 2 - Use the database to reset state</span></span>  

  1. <span data-ttu-id="944fe-127">Verwerfen des aktuellen DbContext</span><span class="sxs-lookup"><span data-stu-id="944fe-127">Discard the current DbContext</span></span>  
  2. <span data-ttu-id="944fe-128">Erstellen eines neuen DbContext und Wiederherstellen des Status Ihrer Anwendung aus der Datenbank</span><span class="sxs-lookup"><span data-stu-id="944fe-128">Create a new DbContext and restore the state of your application from the database</span></span>  
  3. <span data-ttu-id="944fe-129">Informieren Sie den Benutzer, dass der letzte Vorgang möglicherweise nicht erfolgreich abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="944fe-129">Inform the user that the last operation might not have been completed successfully</span></span>  

* <span data-ttu-id="944fe-130">Option 3 - Die Transaktion manuell nachverfolgen</span><span class="sxs-lookup"><span data-stu-id="944fe-130">Option 3 - Manually track the transaction</span></span>  

  1. <span data-ttu-id="944fe-131">Fügen Sie der Datenbank eine nicht nachverfolgte Tabelle hinzu, die zum Nachverfolgen des Status der Transaktionen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="944fe-131">Add a non-tracked table to the database used to track the status of the transactions.</span></span>  
  2. <span data-ttu-id="944fe-132">Fügen Sie eine Zeile in die Tabelle am Anfang jeder Transaktion ein.</span><span class="sxs-lookup"><span data-stu-id="944fe-132">Insert a row into the table at the beginning of each transaction.</span></span>  
  3. <span data-ttu-id="944fe-133">Wenn die Verbindung während des Commits fehlschlägt, überprüfen Sie, ob die entsprechende Zeile in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="944fe-133">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>  
     - <span data-ttu-id="944fe-134">Wenn die Zeile vorhanden ist, fahren Sie normal fort, da die Transaktion erfolgreich festgeschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="944fe-134">If the row is present, continue normally, as the transaction was committed successfully</span></span>  
     - <span data-ttu-id="944fe-135">Wenn die Zeile nicht vorhanden ist, verwenden Sie eine Ausführungsstrategie, um den aktuellen Vorgang erneut zu versuchen.</span><span class="sxs-lookup"><span data-stu-id="944fe-135">If the row is absent, use an execution strategy to retry the current operation.</span></span>  
  4. <span data-ttu-id="944fe-136">Wenn der Commit erfolgreich ist, löschen Sie die entsprechende Zeile, um das Wachstum der Tabelle zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="944fe-136">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>  

<span data-ttu-id="944fe-137">[Dieser Blogbeitrag](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) enthält Beispielcode, um dies in SQL Azure zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="944fe-137">[This blog post](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contains sample code for accomplishing this on SQL Azure.</span></span>  
