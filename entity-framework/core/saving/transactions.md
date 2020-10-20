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
# <a name="using-transactions"></a>Verwenden von Transaktionen

Mit Transaktionen können mehrere Datenbankvorgänge einzeln verarbeitet werden. Wenn die Transaktion committet wird, werden alle Vorgänge erfolgreich auf die Datenbank angewendet. Falls für die Transaktion ein Rollback ausgeführt wird, wird keiner der Vorgänge für die Datenbank übernommen.

> [!TIP]
> Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) finden Sie auf GitHub.

## <a name="default-transaction-behavior"></a>Standardtransaktionsverhalten

Wenn der Datenbankanbieter Transaktionen unterstützt, werden standardmäßig alle Änderungen in einem einzigen Aufruf von `SaveChanges` in einer Transaktion angewendet. Wenn bei einer der Änderungen ein Fehler auftritt, wird ein Rollback ausgeführt, und keine der Änderungen wird für die Datenbank übernommen. `SaveChanges` wird also entweder vollständig ausgeführt oder überhaupt nicht, wenn ein Fehler auftritt. Die Datenbank bleibt in diesem Fall unverändert.

Bei den meisten Anwendungen ist dieses Standardverhalten ausreichend. Sie sollten Transaktionen nur manuell steuern, wenn die Anforderungen Ihrer Anwendung dies notwendig machen.

## <a name="controlling-transactions"></a>Steuern von Transaktionen

Mit der `DbContext.Database`-API können Sie Transaktionen beginnen, committen und ein Rollback dafür ausführen. Im folgenden Beispiel werden zwei `SaveChanges`-Vorgänge und eine LINQ-Abfrage dargestellt, die in einer einzelnen Transaktion ausgeführt werden:

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction.cs?name=Transaction&highlight=2,16-18)]

Während alle Anbieter relationaler Datenbanken Transaktionen unterstützen, können andere Anbieter möglicherweise beim Aufrufen von Transaktions-APIs Fehler auslösen oder dies nicht ausführen.

## <a name="savepoints"></a>Sicherungspunkte

Wenn `SaveChanges` aufgerufen und im Kontext bereits eine Transaktion ausgeführt wird, erstellt EF automatisch einen *Sicherungspunkt*, bevor Daten gespeichert werden. Sicherungspunkte sind Punkte innerhalb einer Datenbanktransaktion, für die später ein Rollback ausgeführt werden kann, wenn ein Fehler auftritt, oder aus einem anderen Grund. Wenn bei `SaveChanges` ein Fehler auftritt, wird die Transaktion automatisch an den Sicherungspunkt zurückgesetzt, sodass die Transaktion sich in demselben Zustand befindet, als sei sie nie gestartet worden. Dies ermöglicht Ihnen, mögliche Probleme zu beheben und den Speichervorgang zu wiederholen, insbesondere wenn Probleme mit der [optimistischen Nebenläufigkeit](xref:core/saving/concurrency) auftreten.

Es ist auch möglich, Sicherungspunkte wie bei Transaktionen manuell zu verwalten. Im folgenden Beispiel wird ein Sicherungspunkt innerhalb einer Transaktion erstellt und bei einem Fehler ein Rollback dahin ausgeführt:

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ManagingSavepoints.cs?name=Savepoints&highlight=9,19-20)]

## <a name="cross-context-transaction"></a>Kontextübergreifende Transaktion

Sie können Transaktionen auch für mehrere Kontextinstanzen freigeben. Diese Funktion ist nur bei Anbietern von relationalen Datenbanken verfügbar, da `DbTransaction` und `DbConnection` verwendet werden müssen, die charakteristisch für relationale Datenbanken sind.

Um eine Transaktion freigeben zu können, müssen die Kontexte `DbConnection` und `DbTransaction` verwenden.

### <a name="allow-connection-to-be-externally-provided"></a>Zulassen von extern bereitgestellten Verbindungen

Um `DbConnection` freigeben zu können, muss eine Verbindung an einen Kontext übergeben werden können, während dieser erstellt wird.

Die einfachste Möglichkeit, eine externe Bereitstellung von `DbConnection` zuzulassen, ist, den Kontext nicht mehr mit der `DbContext.OnConfiguring`-Methode zu konfigurieren, sondern extern `DbContextOptions` zu erstellen und es an den Kontextkonstruktor zu übergeben.

> [!TIP]
> `DbContextOptionsBuilder` ist die API, die Sie in `DbContext.OnConfiguring` zum Konfigurieren des Kontexts verwendet haben. Jetzt werden Sie es extern verwenden und `DbContextOptions` erstellen.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Context&highlight=3,4,5)]

Alternativ können Sie weiterhin `DbContext.OnConfiguring` verwenden und eine `DbConnection` akzeptieren, die gespeichert und dann in `DbContext.OnConfiguring` verwendet wird.

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

### <a name="share-connection-and-transaction"></a>Freigeben der Verbindung und der Transaktion

Sie können nun mehrere Kontextinstanzen erstellen, die die gleiche Verbindung verwenden. Anschließend tragen Sie mit der `DbContext.Database.UseTransaction(DbTransaction)`-API beide Kontexte in derselben Transaktion ein.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Transaction&highlight=1-3,6,14,21-23)]

## <a name="using-external-dbtransactions-relational-databases-only"></a>Verwenden von externen DbTransactions (nur relationale Datenbanken)

Wenn Sie mit verschiedenen Datenzugriffstechnologien auf eine relationale Datenbank zugreifen, sollten Sie eine gemeinsame Transaktion für die Vorgänge einrichten, die von diesen verschiedenen Technologien ausgeführt werden.

Im folgenden Beispiel wird gezeigt, wie ein ADO.NET SqlClient-Vorgang und ein Entity Framework Core-Vorgang in derselben Transaktion ausgeführt werden.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction.cs?name=Transaction&highlight=4,9,20,25-27)]

## <a name="using-systemtransactions"></a>Verwenden von 'System.Transactions'

Es ist möglich, Ambient-Transaktionen zu verwenden, wenn Sie einen größeren Bereich koordinieren müssen.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction.cs?name=Transaction&highlight=1,2,3,26-28)]

Sie können auch eine Eintragung in einer expliziten Transaktion vornehmen.

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction.cs?name=Transaction&highlight=1-2,15,28-30)]

### <a name="limitations-of-systemtransactions"></a>Einschränkungen von System.Transactions

1. In EF Core müssen die Datenbankanbieter die Unterstützung für System.Transactions implementieren. Wenn ein Anbieter keine Unterstützung für System.Transactions implementiert, ist es möglich, dass Aufrufe dieser APIs vollständig ignoriert werden. SqlClient unterstützt dies.

   > [!IMPORTANT]
   > Daher sollten Sie testen, ob die API ordnungsgemäß mit Ihrem Anbieter funktioniert, bevor Sie sie für die Verwaltung von Transaktionen einsetzen. Sollte die API nicht funktionieren, wenden Sie sich bitte an den Maintainer des Datenbankanbieters.

2. Ab .NET Core 2.1 enthält die System.Transactions-Implementierung keine Unterstützung für verteilte Transaktionen. Aus diesem Grund können Sie weder mit `TransactionScope` noch mit `CommittableTransaction` Transaktionen in mehreren Ressourcen-Managern koordinieren.
