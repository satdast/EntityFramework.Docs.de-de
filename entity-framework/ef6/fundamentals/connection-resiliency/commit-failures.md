---
title: Behandeln von Fehlern bei Transaktionscommit EF6
description: Behandeln von Transaktionscommit-Fehlern in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
uid: ef6/fundamentals/connection-resiliency/commit-failures
ms.openlocfilehash: 10cb67837264bea37a0621aa078e1753af954c2f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616290"
---
# <a name="handling-transaction-commit-failures"></a>Behandeln von transaktionscommitfehlern

> [!NOTE]
> **Nur EF 6.1** : die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 6,1 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.  

Als Teil von 6,1 wird ein neues Feature für die Verbindungs Resilienz für EF eingeführt: die Möglichkeit, automatisch zu erkennen und wiederherzustellen, wenn vorübergehende Verbindungsfehler die Bestätigung von Transaktionscommits beeinflussen. Die vollständigen Details des Szenarios werden am besten im Blogbeitrag [SQL-Datenbankkonnektivität und das Idempotenz-Problem](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)beschrieben.  Zusammenfassend gilt: Wenn eine Ausnahme während eines Transaktionscommits ausgelöst wird, gibt es zwei mögliche Ursachen:  

1. Fehler beim Commit der Transaktion auf dem Server.
2. Der Transaktionscommit war auf dem Server erfolgreich, aber aufgrund eines Konnektivitätsproblems wurde die Erfolgs Benachrichtigung an den Client  

Bei der ersten Situation kann die Anwendung oder der Benutzer den Vorgang wiederholen, aber wenn die zweite Situation eintritt, sollten Wiederholungs Versuche vermieden werden, und die Anwendung kann automatisch wieder hergestellt werden. Die Herausforderung besteht darin, dass die Anwendung nicht die richtige Vorgehensweise auswählen kann, ohne zu erkennen, was der tatsächliche Grund ist, warum eine Ausnahme während des Commit gemeldet wurde. Das neue Feature in EF 6,1 ermöglicht EF, sich mit der Datenbank zu überprüfen, wenn die Transaktion erfolgreich war, und die richtige Vorgehensweise zur transparenten Ausführung zu treffen.  

## <a name="using-the-feature"></a>Verwenden der Funktion  

Um die Funktion zu aktivieren, die Sie benötigen, müssen Sie im Konstruktor von **dbconfiguration**einen [calltransaktionshandlerbefehl](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) einfügen. Wenn Sie mit **dbconfiguration**nicht vertraut sind, finden Sie unter [Code basierte Konfiguration](xref:ef6/fundamentals/configuring/code-based)Weitere Informationen. Diese Funktion kann in Kombination mit den automatischen Wiederholungen verwendet werden, die wir in EF6 eingeführt haben, was zu einer Situation beiträgt, in der die Transaktion aufgrund eines vorübergehenden Fehlers tatsächlich keinen Commit auf dem Server ausgeführt hat:  

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

## <a name="how-transactions-are-tracked"></a>Nachverfolgen von Transaktionen  

Wenn das Feature aktiviert ist, fügt EF der Datenbank automatisch eine neue Tabelle mit dem Namen **__Transactions**hinzu. Wenn eine Transaktion von EF erstellt wird, wird in diese Tabelle eine neue Zeile eingefügt, und diese Zeile wird auf vorhanden sein überprüft, wenn während des Commits ein Transaktionsfehler auftritt.  

Obwohl EF den besten Aufwand zum Löschen von Zeilen aus der Tabelle durchführt, wenn Sie nicht mehr benötigt werden, kann die Tabelle vergrößert werden, wenn die Anwendung vorzeitig beendet wird. aus diesem Grund müssen Sie die Tabelle in einigen Fällen manuell löschen.  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>Behandeln von commitfehlern mit früheren Versionen

Vor EF 6,1 gab es keinen Mechanismus zum Behandeln von commitfehlern im EF-Produkt. Es gibt mehrere Möglichkeiten, diese Situation zu behandeln, die auf frühere Versionen von EF6 angewendet werden kann:  

* Option 1-nichts tun  

  Die Wahrscheinlichkeit eines Verbindungsfehlers bei einem Transaktionscommit ist niedrig, sodass es für Ihre Anwendung möglicherweise nicht zu einem Fehler kommt, wenn diese Bedingung tatsächlich auftritt.  

* Option 2: Verwenden der Datenbank zum Zurücksetzen des Zustands  

  1. Aktuellen dbcontext verwerfen  
  2. Erstellen Sie einen neuen dbcontext, und stellen Sie den Status der Anwendung aus der Datenbank wieder her.  
  3. Informieren Sie den Benutzer darüber, dass der letzte Vorgang möglicherweise nicht erfolgreich abgeschlossen wurde.  

* Option 3: Manuelles Nachverfolgen der Transaktion  

  1. Fügen Sie der Datenbank, die zum Nachverfolgen des Status der Transaktionen verwendet wird, eine nicht nach verfolgte Tabelle hinzu.  
  2. Fügen Sie am Anfang jeder Transaktion eine Zeile in die Tabelle ein.  
  3. Wenn die Verbindung während des Commit fehlschlägt, überprüfen Sie, ob die entsprechende Zeile in der Datenbank vorhanden ist.  
     * Wenn die Zeile vorhanden ist, fahren Sie normal fort, da für die Transaktion erfolgreich ein Commit ausgeführt wurde.  
     * Wenn die Zeile nicht vorhanden ist, verwenden Sie eine Ausführungs Strategie, um den aktuellen Vorgang zu wiederholen.  
  4. Wenn der Commit erfolgreich ausgeführt wurde, löschen Sie die entsprechende Zeile, um das Wachstum der Tabelle zu vermeiden.  

[Dieser Blogbeitrag](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) enthält Beispielcode zum Erreichen dieser SQL Azure.  
