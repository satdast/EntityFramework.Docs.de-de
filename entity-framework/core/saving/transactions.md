---
title: Transaktionen – EF Core
description: Verwalten von Transaktionen für Unteilbarkeit beim Speichern von Daten mit Entity Framework Core
author: roji
ms.date: 9/26/2020
uid: core/saving/transactions
ms.openlocfilehash: 2cefe23068a40122b7a37c21536213456eef7b66
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063620"
---
# <a name="using-transactions"></a><span data-ttu-id="df691-103">Verwenden von Transaktionen</span><span class="sxs-lookup"><span data-stu-id="df691-103">Using Transactions</span></span>

<span data-ttu-id="df691-104">Mit Transaktionen können mehrere Datenbankvorgänge einzeln verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="df691-104">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="df691-105">Wenn die Transaktion committet wird, werden alle Vorgänge erfolgreich auf die Datenbank angewendet.</span><span class="sxs-lookup"><span data-stu-id="df691-105">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="df691-106">Falls für die Transaktion ein Rollback ausgeführt wird, wird keiner der Vorgänge für die Datenbank übernommen.</span><span class="sxs-lookup"><span data-stu-id="df691-106">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]
> <span data-ttu-id="df691-107">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="df691-107">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="df691-108">Standardtransaktionsverhalten</span><span class="sxs-lookup"><span data-stu-id="df691-108">Default transaction behavior</span></span>

<span data-ttu-id="df691-109">Wenn der Datenbankanbieter Transaktionen unterstützt, werden standardmäßig alle Änderungen in einem einzigen Aufruf von `SaveChanges` in einer Transaktion angewendet.</span><span class="sxs-lookup"><span data-stu-id="df691-109">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges` are applied in a transaction.</span></span> <span data-ttu-id="df691-110">Wenn bei einer der Änderungen ein Fehler auftritt, wird ein Rollback ausgeführt, und keine der Änderungen wird für die Datenbank übernommen.</span><span class="sxs-lookup"><span data-stu-id="df691-110">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="df691-111">`SaveChanges` wird also entweder vollständig ausgeführt oder überhaupt nicht, wenn ein Fehler auftritt. Die Datenbank bleibt in diesem Fall unverändert.</span><span class="sxs-lookup"><span data-stu-id="df691-111">This means that `SaveChanges` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="df691-112">Bei den meisten Anwendungen ist dieses Standardverhalten ausreichend.</span><span class="sxs-lookup"><span data-stu-id="df691-112">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="df691-113">Sie sollten Transaktionen nur manuell steuern, wenn die Anforderungen Ihrer Anwendung dies notwendig machen.</span><span class="sxs-lookup"><span data-stu-id="df691-113">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="df691-114">Steuern von Transaktionen</span><span class="sxs-lookup"><span data-stu-id="df691-114">Controlling transactions</span></span>

<span data-ttu-id="df691-115">Mit der `DbContext.Database`-API können Sie Transaktionen beginnen, committen und ein Rollback dafür ausführen.</span><span class="sxs-lookup"><span data-stu-id="df691-115">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="df691-116">Im folgenden Beispiel werden zwei `SaveChanges`-Vorgänge und eine LINQ-Abfrage dargestellt, die in einer einzelnen Transaktion ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="df691-116">The following example shows two `SaveChanges` operations and a LINQ query being executed in a single transaction:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction.cs?name=Transaction&highlight=2,16-18)]

<span data-ttu-id="df691-117">Während alle Anbieter relationaler Datenbanken Transaktionen unterstützen, können andere Anbieter möglicherweise beim Aufrufen von Transaktions-APIs Fehler auslösen oder dies nicht ausführen.</span><span class="sxs-lookup"><span data-stu-id="df691-117">While all relational database providers support transactions, other providers types may throw or no-op when transaction APIs are called.</span></span>

## <a name="savepoints"></a><span data-ttu-id="df691-118">Sicherungspunkte</span><span class="sxs-lookup"><span data-stu-id="df691-118">Savepoints</span></span>

<span data-ttu-id="df691-119">Wenn `SaveChanges` aufgerufen und im Kontext bereits eine Transaktion ausgeführt wird, erstellt EF automatisch einen *Sicherungspunkt*, bevor Daten gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="df691-119">When `SaveChanges` is invoked and a transaction is already in progress on the context, EF automatically creates a *savepoint* before saving any data.</span></span> <span data-ttu-id="df691-120">Sicherungspunkte sind Punkte innerhalb einer Datenbanktransaktion, für die später ein Rollback ausgeführt werden kann, wenn ein Fehler auftritt, oder aus einem anderen Grund.</span><span class="sxs-lookup"><span data-stu-id="df691-120">Savepoints are points within a database transaction which may later be rolled back to, if an error occurs or for any other reason.</span></span> <span data-ttu-id="df691-121">Wenn bei `SaveChanges` ein Fehler auftritt, wird die Transaktion automatisch an den Sicherungspunkt zurückgesetzt, sodass die Transaktion sich in demselben Zustand befindet, als sei sie nie gestartet worden.</span><span class="sxs-lookup"><span data-stu-id="df691-121">If `SaveChanges` encounters any error, it automatically rolls the transaction back to the savepoint, leaving the transaction in the same state as if it had never started.</span></span> <span data-ttu-id="df691-122">Dies ermöglicht Ihnen, mögliche Probleme zu beheben und den Speichervorgang zu wiederholen, insbesondere wenn Probleme mit der [optimistischen Nebenläufigkeit](xref:core/saving/concurrency) auftreten.</span><span class="sxs-lookup"><span data-stu-id="df691-122">This allows you to possibly correct issues and retry saving, in particular when [optimistic concurrency](xref:core/saving/concurrency) issues occur.</span></span>

<span data-ttu-id="df691-123">Es ist auch möglich, Sicherungspunkte wie bei Transaktionen manuell zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="df691-123">It's also possible to manually manage savepoints, just as it is with transactions.</span></span> <span data-ttu-id="df691-124">Im folgenden Beispiel wird ein Sicherungspunkt innerhalb einer Transaktion erstellt und bei einem Fehler ein Rollback dahin ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="df691-124">The following example creates a savepoint within a transaction, and rolls back to it on failure:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ManagingSavepoints.cs?name=Savepoints&highlight=9,19-20)]

## <a name="cross-context-transaction"></a><span data-ttu-id="df691-125">Kontextübergreifende Transaktion</span><span class="sxs-lookup"><span data-stu-id="df691-125">Cross-context transaction</span></span>

<span data-ttu-id="df691-126">Sie können Transaktionen auch für mehrere Kontextinstanzen freigeben.</span><span class="sxs-lookup"><span data-stu-id="df691-126">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="df691-127">Diese Funktion ist nur bei Anbietern von relationalen Datenbanken verfügbar, da `DbTransaction` und `DbConnection` verwendet werden müssen, die charakteristisch für relationale Datenbanken sind.</span><span class="sxs-lookup"><span data-stu-id="df691-127">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="df691-128">Um eine Transaktion freigeben zu können, müssen die Kontexte `DbConnection` und `DbTransaction` verwenden.</span><span class="sxs-lookup"><span data-stu-id="df691-128">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="df691-129">Zulassen von extern bereitgestellten Verbindungen</span><span class="sxs-lookup"><span data-stu-id="df691-129">Allow connection to be externally provided</span></span>

<span data-ttu-id="df691-130">Um `DbConnection` freigeben zu können, muss eine Verbindung an einen Kontext übergeben werden können, während dieser erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="df691-130">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="df691-131">Die einfachste Möglichkeit, eine externe Bereitstellung von `DbConnection` zuzulassen, ist, den Kontext nicht mehr mit der `DbContext.OnConfiguring`-Methode zu konfigurieren, sondern extern `DbContextOptions` zu erstellen und es an den Kontextkonstruktor zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="df691-131">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]
> <span data-ttu-id="df691-132">`DbContextOptionsBuilder` ist die API, die Sie in `DbContext.OnConfiguring` zum Konfigurieren des Kontexts verwendet haben. Jetzt werden Sie es extern verwenden und `DbContextOptions` erstellen.</span><span class="sxs-lookup"><span data-stu-id="df691-132">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Context&highlight=3,4,5)]

<span data-ttu-id="df691-133">Alternativ können Sie weiterhin `DbContext.OnConfiguring` verwenden und eine `DbConnection` akzeptieren, die gespeichert und dann in `DbContext.OnConfiguring` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="df691-133">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

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

### <a name="share-connection-and-transaction"></a><span data-ttu-id="df691-134">Freigeben der Verbindung und der Transaktion</span><span class="sxs-lookup"><span data-stu-id="df691-134">Share connection and transaction</span></span>

<span data-ttu-id="df691-135">Sie können nun mehrere Kontextinstanzen erstellen, die die gleiche Verbindung verwenden.</span><span class="sxs-lookup"><span data-stu-id="df691-135">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="df691-136">Anschließend tragen Sie mit der `DbContext.Database.UseTransaction(DbTransaction)`-API beide Kontexte in derselben Transaktion ein.</span><span class="sxs-lookup"><span data-stu-id="df691-136">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Transaction&highlight=1-3,6,14,21-23)]

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="df691-137">Verwenden von externen DbTransactions (nur relationale Datenbanken)</span><span class="sxs-lookup"><span data-stu-id="df691-137">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="df691-138">Wenn Sie mit verschiedenen Datenzugriffstechnologien auf eine relationale Datenbank zugreifen, sollten Sie eine gemeinsame Transaktion für die Vorgänge einrichten, die von diesen verschiedenen Technologien ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="df691-138">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="df691-139">Im folgenden Beispiel wird gezeigt, wie ein ADO.NET SqlClient-Vorgang und ein Entity Framework Core-Vorgang in derselben Transaktion ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="df691-139">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction.cs?name=Transaction&highlight=4,9,20,25-27)]

## <a name="using-systemtransactions"></a><span data-ttu-id="df691-140">Verwenden von 'System.Transactions'</span><span class="sxs-lookup"><span data-stu-id="df691-140">Using System.Transactions</span></span>

<span data-ttu-id="df691-141">Es ist möglich, Ambient-Transaktionen zu verwenden, wenn Sie einen größeren Bereich koordinieren müssen.</span><span class="sxs-lookup"><span data-stu-id="df691-141">It is possible to use ambient transactions if you need to coordinate across a larger scope.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction.cs?name=Transaction&highlight=1,2,3,26-28)]

<span data-ttu-id="df691-142">Sie können auch eine Eintragung in einer expliziten Transaktion vornehmen.</span><span class="sxs-lookup"><span data-stu-id="df691-142">It is also possible to enlist in an explicit transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction.cs?name=Transaction&highlight=1-2,15,28-30)]

### <a name="limitations-of-systemtransactions"></a><span data-ttu-id="df691-143">Einschränkungen von System.Transactions</span><span class="sxs-lookup"><span data-stu-id="df691-143">Limitations of System.Transactions</span></span>

1. <span data-ttu-id="df691-144">In EF Core müssen die Datenbankanbieter die Unterstützung für System.Transactions implementieren.</span><span class="sxs-lookup"><span data-stu-id="df691-144">EF Core relies on database providers to implement support for System.Transactions.</span></span> <span data-ttu-id="df691-145">Wenn ein Anbieter keine Unterstützung für System.Transactions implementiert, ist es möglich, dass Aufrufe dieser APIs vollständig ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="df691-145">If a provider does not implement support for System.Transactions, it is possible that calls to these APIs will be completely ignored.</span></span> <span data-ttu-id="df691-146">SqlClient unterstützt dies.</span><span class="sxs-lookup"><span data-stu-id="df691-146">SqlClient supports it.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="df691-147">Daher sollten Sie testen, ob die API ordnungsgemäß mit Ihrem Anbieter funktioniert, bevor Sie sie für die Verwaltung von Transaktionen einsetzen.</span><span class="sxs-lookup"><span data-stu-id="df691-147">It is recommended that you test that the API behaves correctly with your provider before you rely on it for managing transactions.</span></span> <span data-ttu-id="df691-148">Sollte die API nicht funktionieren, wenden Sie sich bitte an den Maintainer des Datenbankanbieters.</span><span class="sxs-lookup"><span data-stu-id="df691-148">You are encouraged to contact the maintainer of the database provider if it does not.</span></span>

2. <span data-ttu-id="df691-149">Ab .NET Core 2.1 enthält die System.Transactions-Implementierung keine Unterstützung für verteilte Transaktionen. Aus diesem Grund können Sie weder mit `TransactionScope` noch mit `CommittableTransaction` Transaktionen in mehreren Ressourcen-Managern koordinieren.</span><span class="sxs-lookup"><span data-stu-id="df691-149">As of .NET Core 2.1, the System.Transactions implementation does not include support for distributed transactions, therefore you cannot use `TransactionScope` or `CommittableTransaction` to coordinate transactions across multiple resource managers.</span></span>
