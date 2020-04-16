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
# <a name="handling-transaction-commit-failures"></a>Behandeln von Transaktionscommitfehlern
> [!NOTE]
> **EF6.1 Nur ab -** Die auf dieser Seite diskutierten Features, APIs usw. wurden in Entity Framework 6.1 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.  

Im Rahmen von 6.1 führen wir eine neue Verbindungsresilienzfunktion für EF ein: die Fähigkeit, automatisch zu erkennen und wiederherzustellen, wenn vorübergehende Verbindungsfehler die Bestätigung von Transaktionscommits beeinflussen. Die vollständigen Details des Szenarios werden am besten im Blogbeitrag [SQL Database Connectivity und das Idempotency Issue](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)beschrieben.  Zusammenfassend lässt sich sagen, dass beim Auslösen einer Ausnahme während eines Transaktionscommits zwei mögliche Ursachen vorliegen:  

1. Der Transaktionscommit ist auf dem Server fehlgeschlagen.
2. Der Transaktionscommit war auf dem Server erfolgreich, aber ein Verbindungsproblem verhinderte, dass die Erfolgsbenachrichtigung den Client erreichte.  

Wenn die erste Situation eintritt, kann die Anwendung oder der Benutzer den Vorgang wiederholen, aber wenn die zweite Situation auftritt, sollten Wiederholungen vermieden werden, und die Anwendung kann automatisch wiederhergestellt werden. Die Herausforderung besteht darin, dass die Anwendung ohne die Möglichkeit, den eigentlichen Grund zu erkennen, warum eine Ausnahme während des Commits gemeldet wurde, nicht die richtige Vorgehensweise wählen kann. Die neue Funktion in EF 6.1 ermöglicht es EF, die Datenbank zu überprüfen, wenn die Transaktion erfolgreich war, und die richtige Vorgehensweise transparent zu verfolgen.  

## <a name="using-the-feature"></a>Verwenden der Funktion  

Um die Funktion zu aktivieren, müssen Sie einen Aufruf von [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in den Konstruktor Ihrer **DbConfiguration**einschließen. Wenn Sie mit **DbConfiguration**nicht vertraut sind, finden Sie weitere Informationen unter [Codebasierte Konfiguration](~/ef6/fundamentals/configuring/code-based.md). Diese Funktion kann in Kombination mit den automatischen Wiederholungen verwendet werden, die wir in EF6 eingeführt haben, was in der Situation hilft, in der die Transaktion aufgrund eines vorübergehenden Fehlers tatsächlich nicht auf dem Server übertragen werden konnte:  

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

## <a name="how-transactions-are-tracked"></a>Wie Transaktionen nachverfolgt werden  

Wenn das Feature aktiviert ist, fügt EF automatisch eine neue Tabelle zur Datenbank mit dem Namen **__Transactions**hinzu. Jedes Mal, wenn eine Transaktion von EF erstellt wird und diese Zeile auf vorhanden überprüft wird, wenn während des Commits ein Transaktionsfehler auftritt, wird in diese Tabelle eine neue Zeile eingefügt.  

Obwohl EF die meisten Anstrengungen unternimmt, um Zeilen aus der Tabelle zu löschen, wenn sie nicht mehr benötigt werden, kann die Tabelle wachsen, wenn die Anwendung vorzeitig beendet wird, und aus diesem Grund müssen Sie die Tabelle in einigen Fällen manuell löschen.  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>Umgang mit Commit-Fehlern mit früheren Versionen

Vor EF 6.1 gab es keinen Mechanismus zum Behandeln von Commitfehlern im EF-Produkt. Es gibt mehrere Möglichkeiten, mit dieser Situation umzugehen, die auf frühere Versionen von EF6 angewendet werden können:  

* Option 1 - Nichts tun  

  Die Wahrscheinlichkeit eines Verbindungsfehlers während des Transaktionscommits ist gering, sodass es für Ihre Anwendung möglicherweise akzeptabel ist, nur fehlschlägt, wenn diese Bedingung tatsächlich auftritt.  

* Option 2 - Verwenden sie die Datenbank, um den Status zurückzusetzen  

  1. Verwerfen des aktuellen DbContext  
  2. Erstellen eines neuen DbContext und Wiederherstellen des Status Ihrer Anwendung aus der Datenbank  
  3. Informieren Sie den Benutzer, dass der letzte Vorgang möglicherweise nicht erfolgreich abgeschlossen wurde.  

* Option 3 - Die Transaktion manuell nachverfolgen  

  1. Fügen Sie der Datenbank eine nicht nachverfolgte Tabelle hinzu, die zum Nachverfolgen des Status der Transaktionen verwendet wird.  
  2. Fügen Sie eine Zeile in die Tabelle am Anfang jeder Transaktion ein.  
  3. Wenn die Verbindung während des Commits fehlschlägt, überprüfen Sie, ob die entsprechende Zeile in der Datenbank vorhanden ist.  
     - Wenn die Zeile vorhanden ist, fahren Sie normal fort, da die Transaktion erfolgreich festgeschrieben wurde.  
     - Wenn die Zeile nicht vorhanden ist, verwenden Sie eine Ausführungsstrategie, um den aktuellen Vorgang erneut zu versuchen.  
  4. Wenn der Commit erfolgreich ist, löschen Sie die entsprechende Zeile, um das Wachstum der Tabelle zu vermeiden.  

[Dieser Blogbeitrag](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) enthält Beispielcode, um dies in SQL Azure zu erreichen.  
