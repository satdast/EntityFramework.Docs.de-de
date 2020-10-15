---
title: Behandeln von Fehlern bei Transaktionscommit EF6
description: Behandeln von Transaktionscommit-Fehlern in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/connection-resiliency/commit-failures
ms.openlocfilehash: ecc9daf568f12e3bf93af8c93e543c12dfddbb06
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065588"
---
# <a name="handling-transaction-commit-failures"></a><span data-ttu-id="1c8f5-103">Behandeln von transaktionscommitfehlern</span><span class="sxs-lookup"><span data-stu-id="1c8f5-103">Handling transaction commit failures</span></span>

> [!NOTE]
> <span data-ttu-id="1c8f5-104">**Nur EF 6.1** : die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 6,1 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-104">**EF6.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="1c8f5-105">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="1c8f5-106">Als Teil von 6,1 wird ein neues Feature für die Verbindungs Resilienz für EF eingeführt: die Möglichkeit, automatisch zu erkennen und wiederherzustellen, wenn vorübergehende Verbindungsfehler die Bestätigung von Transaktionscommits beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-106">As part of 6.1 we are introducing a new connection resiliency feature for EF: the ability to detect and recover automatically when transient connection failures affect the acknowledgement of transaction commits.</span></span> <span data-ttu-id="1c8f5-107">Die vollständigen Details des Szenarios werden am besten im Blogbeitrag [SQL-Datenbankkonnektivität und das Idempotenz-Problem](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)beschrieben.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-107">The full details of the scenario are best described in the blog post [SQL Database Connectivity and the Idempotency Issue](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).</span></span>  <span data-ttu-id="1c8f5-108">Zusammenfassend gilt: Wenn eine Ausnahme während eines Transaktionscommits ausgelöst wird, gibt es zwei mögliche Ursachen:</span><span class="sxs-lookup"><span data-stu-id="1c8f5-108">In summary, the scenario is that when an exception is raised during a transaction commit there are two possible causes:</span></span>  

1. <span data-ttu-id="1c8f5-109">Fehler beim Commit der Transaktion auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-109">The transaction commit failed on the server</span></span>
2. <span data-ttu-id="1c8f5-110">Der Transaktionscommit war auf dem Server erfolgreich, aber aufgrund eines Konnektivitätsproblems wurde die Erfolgs Benachrichtigung an den Client</span><span class="sxs-lookup"><span data-stu-id="1c8f5-110">The transaction commit succeeded on the server but a connectivity issue prevented the success notification from reaching the client</span></span>  

<span data-ttu-id="1c8f5-111">Bei der ersten Situation kann die Anwendung oder der Benutzer den Vorgang wiederholen, aber wenn die zweite Situation eintritt, sollten Wiederholungs Versuche vermieden werden, und die Anwendung kann automatisch wieder hergestellt werden.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-111">When the first situation happens the application or the user can retry the operation, but when the second situation occurs retries should be avoided and the application could recover automatically.</span></span> <span data-ttu-id="1c8f5-112">Die Herausforderung besteht darin, dass die Anwendung nicht die richtige Vorgehensweise auswählen kann, ohne zu erkennen, was der tatsächliche Grund ist, warum eine Ausnahme während des Commit gemeldet wurde.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-112">The challenge is that without the ability to detect what was the actual reason an exception was reported during commit, the application cannot choose the right course of action.</span></span> <span data-ttu-id="1c8f5-113">Das neue Feature in EF 6,1 ermöglicht EF, sich mit der Datenbank zu überprüfen, wenn die Transaktion erfolgreich war, und die richtige Vorgehensweise zur transparenten Ausführung zu treffen.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-113">The new feature in EF 6.1 allows EF to double-check with the database if the transaction succeeded and take the right course of action transparently.</span></span>  

## <a name="using-the-feature"></a><span data-ttu-id="1c8f5-114">Verwenden der Funktion</span><span class="sxs-lookup"><span data-stu-id="1c8f5-114">Using the feature</span></span>  

<span data-ttu-id="1c8f5-115">Um die Funktion zu aktivieren, die Sie benötigen, müssen Sie im Konstruktor von **dbconfiguration**einen [calltransaktionshandlerbefehl](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) einfügen.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-115">In order to enable the feature you need include a call to [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in the constructor of your **DbConfiguration**.</span></span> <span data-ttu-id="1c8f5-116">Wenn Sie mit **dbconfiguration**nicht vertraut sind, finden Sie unter [Code basierte Konfiguration](xref:ef6/fundamentals/configuring/code-based)Weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-116">If you are unfamiliar with **DbConfiguration**, see [Code Based Configuration](xref:ef6/fundamentals/configuring/code-based).</span></span> <span data-ttu-id="1c8f5-117">Diese Funktion kann in Kombination mit den automatischen Wiederholungen verwendet werden, die wir in EF6 eingeführt haben, was zu einer Situation beiträgt, in der die Transaktion aufgrund eines vorübergehenden Fehlers tatsächlich keinen Commit auf dem Server ausgeführt hat:</span><span class="sxs-lookup"><span data-stu-id="1c8f5-117">This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span></span>  

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

## <a name="how-transactions-are-tracked"></a><span data-ttu-id="1c8f5-118">Nachverfolgen von Transaktionen</span><span class="sxs-lookup"><span data-stu-id="1c8f5-118">How transactions are tracked</span></span>  

<span data-ttu-id="1c8f5-119">Wenn das Feature aktiviert ist, fügt EF der Datenbank automatisch eine neue Tabelle mit dem Namen **__Transactions**hinzu.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-119">When the feature is enabled, EF will automatically add a new table to the database called **__Transactions**.</span></span> <span data-ttu-id="1c8f5-120">Wenn eine Transaktion von EF erstellt wird, wird in diese Tabelle eine neue Zeile eingefügt, und diese Zeile wird auf vorhanden sein überprüft, wenn während des Commits ein Transaktionsfehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-120">A new row is inserted in this table every time a transaction is created by EF and that row is checked for existence if a transaction failure occurs during commit.</span></span>  

<span data-ttu-id="1c8f5-121">Obwohl EF den besten Aufwand zum Löschen von Zeilen aus der Tabelle durchführt, wenn Sie nicht mehr benötigt werden, kann die Tabelle vergrößert werden, wenn die Anwendung vorzeitig beendet wird. aus diesem Grund müssen Sie die Tabelle in einigen Fällen manuell löschen.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-121">Although EF will do a best effort to prune rows from the table when they aren’t needed anymore, the table can grow if the application exits prematurely and for that reason you may need to purge the table manually in some cases.</span></span>  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a><span data-ttu-id="1c8f5-122">Behandeln von commitfehlern mit früheren Versionen</span><span class="sxs-lookup"><span data-stu-id="1c8f5-122">How to handle commit failures with previous Versions</span></span>

<span data-ttu-id="1c8f5-123">Vor EF 6,1 gab es keinen Mechanismus zum Behandeln von commitfehlern im EF-Produkt.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-123">Before EF 6.1 there was not mechanism to handle commit failures in the EF product.</span></span> <span data-ttu-id="1c8f5-124">Es gibt mehrere Möglichkeiten, diese Situation zu behandeln, die auf frühere Versionen von EF6 angewendet werden kann:</span><span class="sxs-lookup"><span data-stu-id="1c8f5-124">There are several ways to dealing with this situation that can be applied to previous versions of EF6:</span></span>  

* <span data-ttu-id="1c8f5-125">Option 1-nichts tun</span><span class="sxs-lookup"><span data-stu-id="1c8f5-125">Option 1 - Do nothing</span></span>  

  <span data-ttu-id="1c8f5-126">Die Wahrscheinlichkeit eines Verbindungsfehlers bei einem Transaktionscommit ist niedrig, sodass es für Ihre Anwendung möglicherweise nicht zu einem Fehler kommt, wenn diese Bedingung tatsächlich auftritt.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-126">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>  

* <span data-ttu-id="1c8f5-127">Option 2: Verwenden der Datenbank zum Zurücksetzen des Zustands</span><span class="sxs-lookup"><span data-stu-id="1c8f5-127">Option 2 - Use the database to reset state</span></span>  

  1. <span data-ttu-id="1c8f5-128">Aktuellen dbcontext verwerfen</span><span class="sxs-lookup"><span data-stu-id="1c8f5-128">Discard the current DbContext</span></span>  
  2. <span data-ttu-id="1c8f5-129">Erstellen Sie einen neuen dbcontext, und stellen Sie den Status der Anwendung aus der Datenbank wieder her.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-129">Create a new DbContext and restore the state of your application from the database</span></span>  
  3. <span data-ttu-id="1c8f5-130">Informieren Sie den Benutzer darüber, dass der letzte Vorgang möglicherweise nicht erfolgreich abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-130">Inform the user that the last operation might not have been completed successfully</span></span>  

* <span data-ttu-id="1c8f5-131">Option 3: Manuelles Nachverfolgen der Transaktion</span><span class="sxs-lookup"><span data-stu-id="1c8f5-131">Option 3 - Manually track the transaction</span></span>  

  1. <span data-ttu-id="1c8f5-132">Fügen Sie der Datenbank, die zum Nachverfolgen des Status der Transaktionen verwendet wird, eine nicht nach verfolgte Tabelle hinzu.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-132">Add a non-tracked table to the database used to track the status of the transactions.</span></span>  
  2. <span data-ttu-id="1c8f5-133">Fügen Sie am Anfang jeder Transaktion eine Zeile in die Tabelle ein.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-133">Insert a row into the table at the beginning of each transaction.</span></span>  
  3. <span data-ttu-id="1c8f5-134">Wenn die Verbindung während des Commit fehlschlägt, überprüfen Sie, ob die entsprechende Zeile in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-134">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>  
     * <span data-ttu-id="1c8f5-135">Wenn die Zeile vorhanden ist, fahren Sie normal fort, da für die Transaktion erfolgreich ein Commit ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-135">If the row is present, continue normally, as the transaction was committed successfully</span></span>  
     * <span data-ttu-id="1c8f5-136">Wenn die Zeile nicht vorhanden ist, verwenden Sie eine Ausführungs Strategie, um den aktuellen Vorgang zu wiederholen.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-136">If the row is absent, use an execution strategy to retry the current operation.</span></span>  
  4. <span data-ttu-id="1c8f5-137">Wenn der Commit erfolgreich ausgeführt wurde, löschen Sie die entsprechende Zeile, um das Wachstum der Tabelle zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-137">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>  

<span data-ttu-id="1c8f5-138">[Dieser Blogbeitrag](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) enthält Beispielcode zum Erreichen dieser SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="1c8f5-138">[This blog post](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contains sample code for accomplishing this on SQL Azure.</span></span>  
