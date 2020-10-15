---
title: Verbindungsresilienz-EF Core
description: Verwenden der verbindungsresilienz zum automatischen erneuten Versuch fehlgeschlagener Befehle mit Entity Framework Core
author: AndriySvyryd
ms.date: 11/15/2016
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: db0666a49cbd41ef3eacf447eaeed1fb54ffcbf4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061917"
---
# <a name="connection-resiliency"></a><span data-ttu-id="e2fbb-103">Verbindungsstabilität</span><span class="sxs-lookup"><span data-stu-id="e2fbb-103">Connection Resiliency</span></span>

<span data-ttu-id="e2fbb-104">Die verbindungsresilienz wiederholt fehlerhafte Daten Bank Befehle automatisch.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-104">Connection resiliency automatically retries failed database commands.</span></span> <span data-ttu-id="e2fbb-105">Die Funktion kann mit jeder Datenbank verwendet werden, indem eine "Ausführungs Strategie" bereitgestellt wird, die die erforderliche Logik zum Erkennen von Fehlern und Wiederholungs Befehlen kapselt.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-105">The feature can be used with any database by supplying an "execution strategy", which encapsulates the logic necessary to detect failures and retry commands.</span></span> <span data-ttu-id="e2fbb-106">EF Core Anbieter können Ausführungs Strategien bereitstellen, die auf Ihre spezifischen Datenbankfehler Bedingungen und die optimalen Wiederholungs Richtlinien zugeschnitten sind.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-106">EF Core providers can supply execution strategies tailored to their specific database failure conditions and optimal retry policies.</span></span>

<span data-ttu-id="e2fbb-107">Beispielsweise enthält der SQL Server Anbieter eine Ausführungs Strategie, die speziell auf SQL Server (einschließlich SQL Azure) zugeschnitten ist.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-107">As an example, the SQL Server provider includes an execution strategy that is specifically tailored to SQL Server (including SQL Azure).</span></span> <span data-ttu-id="e2fbb-108">Es kennt die Ausnahme Typen, die wiederholt werden können und sinnvolle Standardwerte für maximale Wiederholungs Versuche, Verzögerung zwischen Wiederholungen usw. aufweisen.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-108">It is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, etc.</span></span>

<span data-ttu-id="e2fbb-109">Beim Konfigurieren der Optionen für den Kontext wird eine Ausführungs Strategie angegeben.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-109">An execution strategy is specified when configuring the options for your context.</span></span> <span data-ttu-id="e2fbb-110">Dies erfolgt in der Regel in der- `OnConfiguring` Methode Ihres abgeleiteten Kontexts:</span><span class="sxs-lookup"><span data-stu-id="e2fbb-110">This is typically in the `OnConfiguring` method of your derived context:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

<span data-ttu-id="e2fbb-111">oder in `Startup.cs` für eine ASP.net Core Anwendung:</span><span class="sxs-lookup"><span data-stu-id="e2fbb-111">or in `Startup.cs` for an ASP.NET Core application:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<PicnicContext>(
        options => options.UseSqlServer(
            "<connection string>",
            providerOptions => providerOptions.EnableRetryOnFailure()));
}
```

## <a name="custom-execution-strategy"></a><span data-ttu-id="e2fbb-112">Benutzerdefinierte Ausführungs Strategie</span><span class="sxs-lookup"><span data-stu-id="e2fbb-112">Custom execution strategy</span></span>

<span data-ttu-id="e2fbb-113">Es gibt einen Mechanismus, mit dem Sie eine benutzerdefinierte Ausführungs Strategie selbst registrieren können, wenn Sie die Standardwerte ändern möchten.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-113">There is a mechanism to register a custom execution strategy of your own if you wish to change any of the defaults.</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a><span data-ttu-id="e2fbb-114">Ausführungs Strategien und Transaktionen</span><span class="sxs-lookup"><span data-stu-id="e2fbb-114">Execution strategies and transactions</span></span>

<span data-ttu-id="e2fbb-115">Eine Ausführungs Strategie, die automatisch Wiederholungs Versuche für Fehler durchführt, muss jeden Vorgang in einem Wiederholungs Block wiedergeben können, der fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-115">An execution strategy that automatically retries on failures needs to be able to play back each operation in a retry block that fails.</span></span> <span data-ttu-id="e2fbb-116">Wenn Wiederholungen aktiviert sind, wird jeder Vorgang, den Sie über EF Core ausführen, zu einem eigenen Abruf Vorgang.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-116">When retries are enabled, each operation you perform via EF Core becomes its own retriable operation.</span></span> <span data-ttu-id="e2fbb-117">Das heißt, jede Abfrage und jeder Rückruf von werden `SaveChanges()` als Einheit wiederholt, wenn ein vorübergehender Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-117">That is, each query and each call to `SaveChanges()` will be retried as a unit if a transient failure occurs.</span></span>

<span data-ttu-id="e2fbb-118">Wenn Ihr Code jedoch mithilfe von eine Transaktion initiiert, `BeginTransaction()` definieren Sie eine eigene Gruppe von Vorgängen, die als Einheit behandelt werden müssen, und alles innerhalb der Transaktion muss wiedergegeben werden, wenn ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-118">However, if your code initiates a transaction using `BeginTransaction()` you are defining your own group of operations that need to be treated as a unit, and everything inside the transaction would need to be played back shall a failure occur.</span></span> <span data-ttu-id="e2fbb-119">Eine Ausnahme wie die folgende wird angezeigt, wenn Sie versuchen, dies zu tun, wenn eine Ausführungs Strategie verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="e2fbb-119">You will receive an exception like the following if you attempt to do this when using an execution strategy:</span></span>

> <span data-ttu-id="e2fbb-120">InvalidOperationException: die konfigurierte Ausführungs Strategie "sqlserverretryingexecutionstrategy" unterstützt keine vom Benutzer initiierten Transaktionen.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-120">InvalidOperationException: The configured execution strategy 'SqlServerRetryingExecutionStrategy' does not support user initiated transactions.</span></span> <span data-ttu-id="e2fbb-121">Verwenden Sie die Ausführungsstrategie, die von „DbContext.Database.CreateExecutionStrategy()“ zurückgegeben wird, um alle Vorgänge in der Transaktion als wiederholbare Einheit auszuführen.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-121">Use the execution strategy returned by 'DbContext.Database.CreateExecutionStrategy()' to execute all the operations in the transaction as a retriable unit.</span></span>

<span data-ttu-id="e2fbb-122">Die Lösung besteht darin, die Ausführungs Strategie manuell mit einem Delegaten aufzurufen, der alle auszuführenden Elemente darstellt.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-122">The solution is to manually invoke the execution strategy with a delegate representing everything that needs to be executed.</span></span> <span data-ttu-id="e2fbb-123">Die Ausführungsstrategie ruft den Delegaten erneut auf, wenn ein vorübergehender Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-123">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

<span data-ttu-id="e2fbb-124">Dieser Ansatz kann auch mit Ambient-Transaktionen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-124">This approach can also be used with ambient transactions.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a><span data-ttu-id="e2fbb-125">Transaktionscommitfehler und das Idempotenz-Problem</span><span class="sxs-lookup"><span data-stu-id="e2fbb-125">Transaction commit failure and the idempotency issue</span></span>

<span data-ttu-id="e2fbb-126">Im Allgemeinen wird bei einem Verbindungsfehler für die aktuelle Transaktion ein Rollback ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-126">In general, when there is a connection failure the current transaction is rolled back.</span></span> <span data-ttu-id="e2fbb-127">Wenn die Verbindung jedoch getrennt wird, während für die Transaktion ein Commit ausgeführt wird, ist der resultierende Status der Transaktion unbekannt.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-127">However, if the connection is dropped while the transaction is being committed the resulting state of the transaction is unknown.</span></span>

<span data-ttu-id="e2fbb-128">Standardmäßig wiederholt die Ausführungs Strategie den Vorgang so, als ob für die Transaktion ein Rollback ausgeführt wurde. wenn dies jedoch nicht der Fall ist, führt dies zu einer Ausnahme, wenn der neue Daten Bank Status nicht kompatibel ist oder zu **Daten** Beschädigungen führen kann, wenn der Vorgang nicht von einem bestimmten Zustand abhängig ist, z. b. Wenn eine neue Zeile mit automatisch generierten Schlüsselwerten eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-128">By default, the execution strategy will retry the operation as if the transaction was rolled back, but if it's not the case this will result in an exception if the new database state is incompatible or could lead to **data corruption** if the operation does not rely on a particular state, for example when inserting a new row with auto-generated key values.</span></span>

<span data-ttu-id="e2fbb-129">Es gibt mehrere Möglichkeiten, damit umzugehen.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-129">There are several ways to deal with this.</span></span>

### <a name="option-1---do-almost-nothing"></a><span data-ttu-id="e2fbb-130">Option 1-do (fast) Nothing</span><span class="sxs-lookup"><span data-stu-id="e2fbb-130">Option 1 - Do (almost) nothing</span></span>

<span data-ttu-id="e2fbb-131">Die Wahrscheinlichkeit eines Verbindungsfehlers bei einem Transaktionscommit ist niedrig, sodass es für Ihre Anwendung möglicherweise nicht zu einem Fehler kommt, wenn diese Bedingung tatsächlich auftritt.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-131">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>

<span data-ttu-id="e2fbb-132">Sie müssen jedoch die Verwendung von Speicher generierten Schlüsseln vermeiden, um sicherzustellen, dass eine Ausnahme ausgelöst wird, anstatt eine doppelte Zeile hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-132">However, you need to avoid using store-generated keys in order to ensure that an exception is thrown instead of adding a duplicate row.</span></span> <span data-ttu-id="e2fbb-133">Es empfiehlt sich, einen vom Client generierten GUID-Wert oder einen Client seitigen Wert Generator zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-133">Consider using a client-generated GUID value or a client-side value generator.</span></span>

### <a name="option-2---rebuild-application-state"></a><span data-ttu-id="e2fbb-134">Option 2: Erneutes Erstellen des Anwendungs Zustands</span><span class="sxs-lookup"><span data-stu-id="e2fbb-134">Option 2 - Rebuild application state</span></span>

1. <span data-ttu-id="e2fbb-135">Verwerfen der aktuellen `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="e2fbb-135">Discard the current `DbContext`.</span></span>
2. <span data-ttu-id="e2fbb-136">Erstellen Sie einen neuen, `DbContext` und stellen Sie den Status der Anwendung aus der Datenbank wieder her.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-136">Create a new `DbContext` and restore the state of your application from the database.</span></span>
3. <span data-ttu-id="e2fbb-137">Informieren Sie den Benutzer darüber, dass der letzte Vorgang möglicherweise nicht erfolgreich abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-137">Inform the user that the last operation might not have been completed successfully.</span></span>

### <a name="option-3---add-state-verification"></a><span data-ttu-id="e2fbb-138">Option 3: Hinzufügen der Zustands Überprüfung</span><span class="sxs-lookup"><span data-stu-id="e2fbb-138">Option 3 - Add state verification</span></span>

<span data-ttu-id="e2fbb-139">Für die meisten Vorgänge, die den Daten Bank Status ändern, ist es möglich, Code hinzuzufügen, der überprüft, ob er erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-139">For most of the operations that change the database state it is possible to add code that checks whether it succeeded.</span></span> <span data-ttu-id="e2fbb-140">EF bietet eine Erweiterungsmethode, um dies zu vereinfachen `IExecutionStrategy.ExecuteInTransaction` .</span><span class="sxs-lookup"><span data-stu-id="e2fbb-140">EF provides an extension method to make this easier - `IExecutionStrategy.ExecuteInTransaction`.</span></span>

<span data-ttu-id="e2fbb-141">Diese Methode beginnt und führt einen Commit für eine Transaktion aus und akzeptiert auch eine Funktion in dem- `verifySucceeded` Parameter, die aufgerufen wird, wenn während des Transaktions Commits ein vorübergehender Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-141">This method begins and commits a transaction and also accepts a function in the `verifySucceeded` parameter that is invoked when a transient error occurs during the transaction commit.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> <span data-ttu-id="e2fbb-142">Hier `SaveChanges` wird aufgerufen, wobei `acceptAllChangesOnSuccess` auf festgelegt wird `false` , um zu verhindern, dass der Zustand der `Blog` Entität in geändert wird `Unchanged` `SaveChanges` .</span><span class="sxs-lookup"><span data-stu-id="e2fbb-142">Here `SaveChanges` is invoked with `acceptAllChangesOnSuccess` set to `false` to avoid changing the state of the `Blog` entity to `Unchanged` if `SaveChanges` succeeds.</span></span> <span data-ttu-id="e2fbb-143">Dadurch kann derselbe Vorgang wiederholt werden, wenn ein Commit fehlschlägt und für die Transaktion ein Rollback ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-143">This allows to retry the same operation if the commit fails and the transaction is rolled back.</span></span>

### <a name="option-4---manually-track-the-transaction"></a><span data-ttu-id="e2fbb-144">Option 4: Manuelles Nachverfolgen der Transaktion</span><span class="sxs-lookup"><span data-stu-id="e2fbb-144">Option 4 - Manually track the transaction</span></span>

<span data-ttu-id="e2fbb-145">Wenn Sie vom Speicher generierte Schlüssel verwenden oder eine generische Methode zur Behandlung von commitfehlern benötigen, die nicht vom Vorgang ausgeführt werden, könnte jeder Transaktion eine ID zugewiesen werden, die bei einem fehlgeschlagenen Commit geprüft wird.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-145">If you need to use store-generated keys or need a generic way of handling commit failures that doesn't depend on the operation performed each transaction could be assigned an ID that is checked when the commit fails.</span></span>

1. <span data-ttu-id="e2fbb-146">Fügen Sie der Datenbank eine Tabelle hinzu, die zum Nachverfolgen des Status der Transaktionen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-146">Add a table to the database used to track the status of the transactions.</span></span>
2. <span data-ttu-id="e2fbb-147">Fügen Sie am Anfang jeder Transaktion eine Zeile in die Tabelle ein.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-147">Insert a row into the table at the beginning of each transaction.</span></span>
3. <span data-ttu-id="e2fbb-148">Wenn die Verbindung während des Commit fehlschlägt, überprüfen Sie, ob die entsprechende Zeile in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-148">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>
4. <span data-ttu-id="e2fbb-149">Wenn der Commit erfolgreich ausgeführt wurde, löschen Sie die entsprechende Zeile, um das Wachstum der Tabelle zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-149">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> <span data-ttu-id="e2fbb-150">Stellen Sie sicher, dass für den für die Überprüfung verwendeten Kontext eine Ausführungs Strategie definiert ist, da die Verbindung bei der Überprüfung wahrscheinlich erneut ausfällt, wenn während des Transaktions Commits ein Fehler aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="e2fbb-150">Make sure that the context used for the verification has an execution strategy defined as the connection is likely to fail again during verification if it failed during transaction commit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e2fbb-151">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e2fbb-151">Additional resources</span></span>

* [<span data-ttu-id="e2fbb-152">Behandeln vorübergehender Verbindungsfehler in Azure SQL-Datenbank und SQL verwaltete Instanz</span><span class="sxs-lookup"><span data-stu-id="e2fbb-152">Troubleshoot transient connection errors in Azure SQL Database and SQL Managed Instance</span></span>](/azure/azure-sql/database/troubleshoot-common-connectivity-issues)
