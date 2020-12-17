---
title: Verbindungsresilienz-EF Core
description: Verwenden der verbindungsresilienz zum automatischen erneuten Versuch fehlgeschlagener Befehle mit Entity Framework Core
author: AndriySvyryd
ms.date: 11/15/2016
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: bcafdf5de26ecfd7539d426388154550a39332ab
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635782"
---
# <a name="connection-resiliency"></a>Verbindungsstabilität

Die verbindungsresilienz wiederholt fehlerhafte Daten Bank Befehle automatisch. Die Funktion kann mit jeder Datenbank verwendet werden, indem eine "Ausführungs Strategie" bereitgestellt wird, die die erforderliche Logik zum Erkennen von Fehlern und Wiederholungs Befehlen kapselt. EF Core Anbieter können Ausführungs Strategien bereitstellen, die auf Ihre spezifischen Datenbankfehler Bedingungen und die optimalen Wiederholungs Richtlinien zugeschnitten sind.

Beispielsweise enthält der SQL Server Anbieter eine Ausführungs Strategie, die speziell auf SQL Server (einschließlich SQL Azure) zugeschnitten ist. Es kennt die Ausnahme Typen, die wiederholt werden können und sinnvolle Standardwerte für maximale Wiederholungs Versuche, Verzögerung zwischen Wiederholungen usw. aufweisen.

Beim Konfigurieren der Optionen für den Kontext wird eine Ausführungs Strategie angegeben. Dies erfolgt in der Regel in der- `OnConfiguring` Methode Ihres abgeleiteten Kontexts:

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

oder in `Startup.cs` für eine ASP.net Core Anwendung:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<PicnicContext>(
        options => options.UseSqlServer(
            "<connection string>",
            providerOptions => providerOptions.EnableRetryOnFailure()));
}
```

> [!NOTE]
> Das Aktivieren der Wiederholungs Versuche bei einem Fehler bewirkt, dass EF das Resultset intern puffert, was die Arbeitsspeicher Anforderungen für Abfragen, die große Resultsets zurückgeben, erheblich erhöhen kann Weitere Informationen finden Sie unter [Pufferung und Streaming](xref:core/performance/efficient-querying#buffering-and-streaming) .

## <a name="custom-execution-strategy"></a>Benutzerdefinierte Ausführungs Strategie

Es gibt einen Mechanismus, mit dem Sie eine benutzerdefinierte Ausführungs Strategie selbst registrieren können, wenn Sie die Standardwerte ändern möchten.

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a>Ausführungs Strategien und Transaktionen

Eine Ausführungs Strategie, die automatisch Wiederholungs Versuche für Fehler durchführt, muss jeden Vorgang in einem Wiederholungs Block wiedergeben können, der fehlschlägt. Wenn Wiederholungen aktiviert sind, wird jeder Vorgang, den Sie über EF Core ausführen, zu einem eigenen Abruf Vorgang. Das heißt, jede Abfrage und jeder Rückruf von werden `SaveChanges()` als Einheit wiederholt, wenn ein vorübergehender Fehler auftritt.

Wenn Ihr Code jedoch mithilfe von eine Transaktion initiiert, `BeginTransaction()` definieren Sie eine eigene Gruppe von Vorgängen, die als Einheit behandelt werden müssen, und alles innerhalb der Transaktion muss wiedergegeben werden, wenn ein Fehler auftritt. Eine Ausnahme wie die folgende wird angezeigt, wenn Sie versuchen, dies zu tun, wenn eine Ausführungs Strategie verwendet wird:

> InvalidOperationException: die konfigurierte Ausführungs Strategie "sqlserverretryingexecutionstrategy" unterstützt keine vom Benutzer initiierten Transaktionen. Verwenden Sie die Ausführungsstrategie, die von „DbContext.Database.CreateExecutionStrategy()“ zurückgegeben wird, um alle Vorgänge in der Transaktion als wiederholbare Einheit auszuführen.

Die Lösung besteht darin, die Ausführungs Strategie manuell mit einem Delegaten aufzurufen, der alle auszuführenden Elemente darstellt. Die Ausführungsstrategie ruft den Delegaten erneut auf, wenn ein vorübergehender Fehler auftritt.

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

Dieser Ansatz kann auch mit Ambient-Transaktionen verwendet werden.

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a>Transaktionscommitfehler und das Idempotenz-Problem

Im Allgemeinen wird bei einem Verbindungsfehler für die aktuelle Transaktion ein Rollback ausgeführt. Wenn die Verbindung jedoch getrennt wird, während für die Transaktion ein Commit ausgeführt wird, ist der resultierende Status der Transaktion unbekannt.

Standardmäßig wiederholt die Ausführungs Strategie den Vorgang so, als ob für die Transaktion ein Rollback ausgeführt wurde. wenn dies jedoch nicht der Fall ist, führt dies zu einer Ausnahme, wenn der neue Daten Bank Status nicht kompatibel ist oder zu **Daten** Beschädigungen führen kann, wenn der Vorgang nicht von einem bestimmten Zustand abhängig ist, z. b. Wenn eine neue Zeile mit automatisch generierten Schlüsselwerten eingefügt wird.

Es gibt mehrere Möglichkeiten, damit umzugehen.

### <a name="option-1---do-almost-nothing"></a>Option 1-do (fast) Nothing

Die Wahrscheinlichkeit eines Verbindungsfehlers bei einem Transaktionscommit ist niedrig, sodass es für Ihre Anwendung möglicherweise nicht zu einem Fehler kommt, wenn diese Bedingung tatsächlich auftritt.

Sie müssen jedoch die Verwendung von Speicher generierten Schlüsseln vermeiden, um sicherzustellen, dass eine Ausnahme ausgelöst wird, anstatt eine doppelte Zeile hinzuzufügen. Es empfiehlt sich, einen vom Client generierten GUID-Wert oder einen Client seitigen Wert Generator zu verwenden.

### <a name="option-2---rebuild-application-state"></a>Option 2: Erneutes Erstellen des Anwendungs Zustands

1. Verwerfen der aktuellen `DbContext` .
2. Erstellen Sie einen neuen, `DbContext` und stellen Sie den Status der Anwendung aus der Datenbank wieder her.
3. Informieren Sie den Benutzer darüber, dass der letzte Vorgang möglicherweise nicht erfolgreich abgeschlossen wurde.

### <a name="option-3---add-state-verification"></a>Option 3: Hinzufügen der Zustands Überprüfung

Für die meisten Vorgänge, die den Daten Bank Status ändern, ist es möglich, Code hinzuzufügen, der überprüft, ob er erfolgreich war. EF bietet eine Erweiterungsmethode, um dies zu vereinfachen `IExecutionStrategy.ExecuteInTransaction` .

Diese Methode beginnt und führt einen Commit für eine Transaktion aus und akzeptiert auch eine Funktion in dem- `verifySucceeded` Parameter, die aufgerufen wird, wenn während des Transaktions Commits ein vorübergehender Fehler auftritt.

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> Hier `SaveChanges` wird aufgerufen, wobei `acceptAllChangesOnSuccess` auf festgelegt wird `false` , um zu verhindern, dass der Zustand der `Blog` Entität in geändert wird `Unchanged` `SaveChanges` . Dadurch kann derselbe Vorgang wiederholt werden, wenn ein Commit fehlschlägt und für die Transaktion ein Rollback ausgeführt wird.

### <a name="option-4---manually-track-the-transaction"></a>Option 4: Manuelles Nachverfolgen der Transaktion

Wenn Sie vom Speicher generierte Schlüssel verwenden oder eine generische Methode zur Behandlung von commitfehlern benötigen, die nicht vom Vorgang ausgeführt werden, könnte jeder Transaktion eine ID zugewiesen werden, die bei einem fehlgeschlagenen Commit geprüft wird.

1. Fügen Sie der Datenbank eine Tabelle hinzu, die zum Nachverfolgen des Status der Transaktionen verwendet wird.
2. Fügen Sie am Anfang jeder Transaktion eine Zeile in die Tabelle ein.
3. Wenn die Verbindung während des Commit fehlschlägt, überprüfen Sie, ob die entsprechende Zeile in der Datenbank vorhanden ist.
4. Wenn der Commit erfolgreich ausgeführt wurde, löschen Sie die entsprechende Zeile, um das Wachstum der Tabelle zu vermeiden.

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> Stellen Sie sicher, dass für den für die Überprüfung verwendeten Kontext eine Ausführungs Strategie definiert ist, da die Verbindung bei der Überprüfung wahrscheinlich erneut ausfällt, wenn während des Transaktions Commits ein Fehler aufgetreten ist.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Behandeln vorübergehender Verbindungsfehler in Azure SQL-Datenbank und SQL verwaltete Instanz](/azure/azure-sql/database/troubleshoot-common-connectivity-issues)
