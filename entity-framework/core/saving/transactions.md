---
title: Transaktionen – EF Core
description: Verwalten von Transaktionen für Unteilbarkeit beim Speichern von Daten mit Entity Framework Core
author: roji
ms.date: 9/26/2020
uid: core/saving/transactions
ms.openlocfilehash: b5e1fa2a0bcc466f22f03fee7ecaef9dcea1efaf
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003548"
---
# <a name="using-transactions"></a><span data-ttu-id="0c70a-103">Verwenden von Transaktionen</span><span class="sxs-lookup"><span data-stu-id="0c70a-103">Using Transactions</span></span>

<span data-ttu-id="0c70a-104">Mit Transaktionen können mehrere Datenbankvorgänge einzeln verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="0c70a-104">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="0c70a-105">Wenn die Transaktion committet wird, werden alle Vorgänge erfolgreich auf die Datenbank angewendet.</span><span class="sxs-lookup"><span data-stu-id="0c70a-105">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="0c70a-106">Falls für die Transaktion ein Rollback ausgeführt wird, wird keiner der Vorgänge für die Datenbank übernommen.</span><span class="sxs-lookup"><span data-stu-id="0c70a-106">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]
> <span data-ttu-id="0c70a-107">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="0c70a-107">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="0c70a-108">Standardtransaktionsverhalten</span><span class="sxs-lookup"><span data-stu-id="0c70a-108">Default transaction behavior</span></span>

<span data-ttu-id="0c70a-109">Wenn der Datenbankanbieter Transaktionen unterstützt, werden standardmäßig alle Änderungen in einem einzigen Aufruf von `SaveChanges` in einer Transaktion angewendet.</span><span class="sxs-lookup"><span data-stu-id="0c70a-109">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges` are applied in a transaction.</span></span> <span data-ttu-id="0c70a-110">Wenn bei einer der Änderungen ein Fehler auftritt, wird ein Rollback ausgeführt, und keine der Änderungen wird für die Datenbank übernommen.</span><span class="sxs-lookup"><span data-stu-id="0c70a-110">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="0c70a-111">`SaveChanges` wird also entweder vollständig ausgeführt oder überhaupt nicht, wenn ein Fehler auftritt. Die Datenbank bleibt in diesem Fall unverändert.</span><span class="sxs-lookup"><span data-stu-id="0c70a-111">This means that `SaveChanges` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="0c70a-112">Bei den meisten Anwendungen ist dieses Standardverhalten ausreichend.</span><span class="sxs-lookup"><span data-stu-id="0c70a-112">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="0c70a-113">Sie sollten Transaktionen nur manuell steuern, wenn die Anforderungen Ihrer Anwendung dies notwendig machen.</span><span class="sxs-lookup"><span data-stu-id="0c70a-113">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="0c70a-114">Steuern von Transaktionen</span><span class="sxs-lookup"><span data-stu-id="0c70a-114">Controlling transactions</span></span>

<span data-ttu-id="0c70a-115">Mit der `DbContext.Database`-API können Sie Transaktionen beginnen, committen und ein Rollback dafür ausführen.</span><span class="sxs-lookup"><span data-stu-id="0c70a-115">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="0c70a-116">Im folgenden Beispiel werden zwei `SaveChanges`-Vorgänge und eine LINQ-Abfrage dargestellt, die in einer einzelnen Transaktion ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="0c70a-116">The following example shows two `SaveChanges` operations and a LINQ query being executed in a single transaction:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction.cs?name=Transaction&highlight=2,16-18)]

<span data-ttu-id="0c70a-117">Während alle Anbieter relationaler Datenbanken Transaktionen unterstützen, können andere Anbieter möglicherweise beim Aufrufen von Transaktions-APIs Fehler auslösen oder dies nicht ausführen.</span><span class="sxs-lookup"><span data-stu-id="0c70a-117">While all relational database providers support transactions, other providers types may throw or no-op when transaction APIs are called.</span></span>

## <a name="savepoints"></a><span data-ttu-id="0c70a-118">Sicherungspunkte</span><span class="sxs-lookup"><span data-stu-id="0c70a-118">Savepoints</span></span>

> [!NOTE]
> <span data-ttu-id="0c70a-119">Dieses Feature wurde in EF Core 5.0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="0c70a-119">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="0c70a-120">Wenn `SaveChanges` aufgerufen und im Kontext bereits eine Transaktion ausgeführt wird, erstellt EF automatisch einen *Sicherungspunkt*, bevor Daten gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="0c70a-120">When `SaveChanges` is invoked and a transaction is already in progress on the context, EF automatically creates a *savepoint* before saving any data.</span></span> <span data-ttu-id="0c70a-121">Sicherungspunkte sind Punkte innerhalb einer Datenbanktransaktion, für die später ein Rollback ausgeführt werden kann, wenn ein Fehler auftritt, oder aus einem anderen Grund.</span><span class="sxs-lookup"><span data-stu-id="0c70a-121">Savepoints are points within a database transaction which may later be rolled back to, if an error occurs or for any other reason.</span></span> <span data-ttu-id="0c70a-122">Wenn bei `SaveChanges` ein Fehler auftritt, wird die Transaktion automatisch an den Sicherungspunkt zurückgesetzt, sodass die Transaktion sich in demselben Zustand befindet, als sei sie nie gestartet worden.</span><span class="sxs-lookup"><span data-stu-id="0c70a-122">If `SaveChanges` encounters any error, it automatically rolls the transaction back to the savepoint, leaving the transaction in the same state as if it had never started.</span></span> <span data-ttu-id="0c70a-123">Dies ermöglicht Ihnen, mögliche Probleme zu beheben und den Speichervorgang zu wiederholen, insbesondere wenn Probleme mit der [optimistischen Nebenläufigkeit](xref:core/saving/concurrency) auftreten.</span><span class="sxs-lookup"><span data-stu-id="0c70a-123">This allows you to possibly correct issues and retry saving, in particular when [optimistic concurrency](xref:core/saving/concurrency) issues occur.</span></span>

> [!WARNING]
> <span data-ttu-id="0c70a-124">Sicherungspunkte sind mit mehreren aktiven Resultsets von SQL Server nicht kompatibel und werden nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="0c70a-124">Savepoints are incompatible with SQL Server's Multiple Active Result Sets, and are not used.</span></span> <span data-ttu-id="0c70a-125">Wenn während `SaveChanges` ein Fehler auftritt, bleibt die Transaktion möglicherweise in einem unbekannten Zustand.</span><span class="sxs-lookup"><span data-stu-id="0c70a-125">If an error occurs during `SaveChanges`, the transaction may be left in an unknown state.</span></span>

<span data-ttu-id="0c70a-126">Es ist auch möglich, Sicherungspunkte wie bei Transaktionen manuell zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="0c70a-126">It's also possible to manually manage savepoints, just as it is with transactions.</span></span> <span data-ttu-id="0c70a-127">Im folgenden Beispiel wird ein Sicherungspunkt innerhalb einer Transaktion erstellt und bei einem Fehler ein Rollback dahin ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="0c70a-127">The following example creates a savepoint within a transaction, and rolls back to it on failure:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ManagingSavepoints.cs?name=Savepoints&highlight=9,19-20)]

## <a name="cross-context-transaction"></a><span data-ttu-id="0c70a-128">Kontextübergreifende Transaktion</span><span class="sxs-lookup"><span data-stu-id="0c70a-128">Cross-context transaction</span></span>

<span data-ttu-id="0c70a-129">Sie können Transaktionen auch für mehrere Kontextinstanzen freigeben.</span><span class="sxs-lookup"><span data-stu-id="0c70a-129">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="0c70a-130">Diese Funktion ist nur bei Anbietern von relationalen Datenbanken verfügbar, da `DbTransaction` und `DbConnection` verwendet werden müssen, die charakteristisch für relationale Datenbanken sind.</span><span class="sxs-lookup"><span data-stu-id="0c70a-130">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="0c70a-131">Um eine Transaktion freigeben zu können, müssen die Kontexte `DbConnection` und `DbTransaction` verwenden.</span><span class="sxs-lookup"><span data-stu-id="0c70a-131">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="0c70a-132">Zulassen von extern bereitgestellten Verbindungen</span><span class="sxs-lookup"><span data-stu-id="0c70a-132">Allow connection to be externally provided</span></span>

<span data-ttu-id="0c70a-133">Um `DbConnection` freigeben zu können, muss eine Verbindung an einen Kontext übergeben werden können, während dieser erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="0c70a-133">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="0c70a-134">Die einfachste Möglichkeit, eine externe Bereitstellung von `DbConnection` zuzulassen, ist, den Kontext nicht mehr mit der `DbContext.OnConfiguring`-Methode zu konfigurieren, sondern extern `DbContextOptions` zu erstellen und es an den Kontextkonstruktor zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="0c70a-134">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]
> <span data-ttu-id="0c70a-135">`DbContextOptionsBuilder` ist die API, die Sie in `DbContext.OnConfiguring` zum Konfigurieren des Kontexts verwendet haben. Jetzt werden Sie es extern verwenden und `DbContextOptions` erstellen.</span><span class="sxs-lookup"><span data-stu-id="0c70a-135">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Context&highlight=3,4,5)]

<span data-ttu-id="0c70a-136">Alternativ können Sie weiterhin `DbContext.OnConfiguring` verwenden und eine `DbConnection` akzeptieren, die gespeichert und dann in `DbContext.OnConfiguring` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="0c70a-136">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

```csharp
public class BloggingContext : DbContext
{
    private DbConnection _connection;

    public BloggingContext(DbConnection connection)
    {
      _connection = connection;
    }

    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(_connection);
    }
}
```

### <a name="share-connection-and-transaction"></a><span data-ttu-id="0c70a-137">Freigeben der Verbindung und der Transaktion</span><span class="sxs-lookup"><span data-stu-id="0c70a-137">Share connection and transaction</span></span>

<span data-ttu-id="0c70a-138">Sie können nun mehrere Kontextinstanzen erstellen, die die gleiche Verbindung verwenden.</span><span class="sxs-lookup"><span data-stu-id="0c70a-138">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="0c70a-139">Anschließend tragen Sie mit der `DbContext.Database.UseTransaction(DbTransaction)`-API beide Kontexte in derselben Transaktion ein.</span><span class="sxs-lookup"><span data-stu-id="0c70a-139">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Transaction&highlight=1-3,6,14,21-23)]

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="0c70a-140">Verwenden von externen DbTransactions (nur relationale Datenbanken)</span><span class="sxs-lookup"><span data-stu-id="0c70a-140">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="0c70a-141">Wenn Sie mit verschiedenen Datenzugriffstechnologien auf eine relationale Datenbank zugreifen, sollten Sie eine gemeinsame Transaktion für die Vorgänge einrichten, die von diesen verschiedenen Technologien ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="0c70a-141">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="0c70a-142">Im folgenden Beispiel wird gezeigt, wie ein ADO.NET SqlClient-Vorgang und ein Entity Framework Core-Vorgang in derselben Transaktion ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="0c70a-142">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction.cs?name=Transaction&highlight=4,9,20,25-27)]

## <a name="using-systemtransactions"></a><span data-ttu-id="0c70a-143">Verwenden von 'System.Transactions'</span><span class="sxs-lookup"><span data-stu-id="0c70a-143">Using System.Transactions</span></span>

<span data-ttu-id="0c70a-144">Es ist möglich, Ambient-Transaktionen zu verwenden, wenn Sie einen größeren Bereich koordinieren müssen.</span><span class="sxs-lookup"><span data-stu-id="0c70a-144">It is possible to use ambient transactions if you need to coordinate across a larger scope.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction.cs?name=Transaction&highlight=1,2,3,26-28)]

<span data-ttu-id="0c70a-145">Sie können auch eine Eintragung in einer expliziten Transaktion vornehmen.</span><span class="sxs-lookup"><span data-stu-id="0c70a-145">It is also possible to enlist in an explicit transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction.cs?name=Transaction&highlight=1-2,15,28-30)]

### <a name="limitations-of-systemtransactions"></a><span data-ttu-id="0c70a-146">Einschränkungen von System.Transactions</span><span class="sxs-lookup"><span data-stu-id="0c70a-146">Limitations of System.Transactions</span></span>

1. <span data-ttu-id="0c70a-147">In EF Core müssen die Datenbankanbieter die Unterstützung für System.Transactions implementieren.</span><span class="sxs-lookup"><span data-stu-id="0c70a-147">EF Core relies on database providers to implement support for System.Transactions.</span></span> <span data-ttu-id="0c70a-148">Wenn ein Anbieter keine Unterstützung für System.Transactions implementiert, ist es möglich, dass Aufrufe dieser APIs vollständig ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="0c70a-148">If a provider does not implement support for System.Transactions, it is possible that calls to these APIs will be completely ignored.</span></span> <span data-ttu-id="0c70a-149">SqlClient unterstützt dies.</span><span class="sxs-lookup"><span data-stu-id="0c70a-149">SqlClient supports it.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="0c70a-150">Daher sollten Sie testen, ob die API ordnungsgemäß mit Ihrem Anbieter funktioniert, bevor Sie sie für die Verwaltung von Transaktionen einsetzen.</span><span class="sxs-lookup"><span data-stu-id="0c70a-150">It is recommended that you test that the API behaves correctly with your provider before you rely on it for managing transactions.</span></span> <span data-ttu-id="0c70a-151">Sollte die API nicht funktionieren, wenden Sie sich bitte an den Maintainer des Datenbankanbieters.</span><span class="sxs-lookup"><span data-stu-id="0c70a-151">You are encouraged to contact the maintainer of the database provider if it does not.</span></span>

2. <span data-ttu-id="0c70a-152">Ab .NET Core 2.1 enthält die System.Transactions-Implementierung keine Unterstützung für verteilte Transaktionen. Aus diesem Grund können Sie weder mit `TransactionScope` noch mit `CommittableTransaction` Transaktionen in mehreren Ressourcen-Managern koordinieren.</span><span class="sxs-lookup"><span data-stu-id="0c70a-152">As of .NET Core 2.1, the System.Transactions implementation does not include support for distributed transactions, therefore you cannot use `TransactionScope` or `CommittableTransaction` to coordinate transactions across multiple resource managers.</span></span>
