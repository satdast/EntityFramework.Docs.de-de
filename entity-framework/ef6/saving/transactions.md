---
title: Arbeiten mit Transaktionen EF6
description: Arbeiten mit Transaktionen in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/saving/transactions
ms.openlocfilehash: 15cc2171338defe482767114c58afe16cc1ffe21
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073716"
---
# <a name="working-with-transactions"></a>Arbeiten mit Transaktionen
> [!NOTE]
> **Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.  

Dieses Dokument beschreibt die Verwendung von Transaktionen in EF6 einschließlich der Verbesserungen, die wir seit EF5 hinzugefügt haben, um die Arbeit mit Transaktionen zu vereinfachen.  

## <a name="what-ef-does-by-default"></a>Funktionsweise von EF standardmäßig  

Wenn Sie in allen Versionen von Entity Framework **SaveChanges ()** ausführen, um die Datenbank einzufügen, zu aktualisieren oder zu löschen, packt das Framework diesen Vorgang in eine Transaktion. Diese Transaktion dauert nur lange genug, um den Vorgang auszuführen und dann abgeschlossen zu werden. Wenn Sie einen solchen Vorgang ausführen, wird eine neue Transaktion gestartet.  

Beginnend mit "EF6 **Database.Executesqlcommand ()** " wird der Befehl in einer Transaktion umschlossen, wenn noch keiner vorhanden ist. Es gibt über Ladungen dieser Methode, die es Ihnen ermöglichen, dieses Verhalten zu überschreiben, wenn Sie möchten. Außerdem wird in EF6 die Ausführung gespeicherter Prozeduren, die im Modell durch APIs wie **ObjectContext.Executefunction ()** enthalten sind, identisch (mit der Ausnahme, dass das Standardverhalten im Moment nicht überschrieben werden kann).  

In beiden Fällen ist die Isolationsstufe der Transaktion unabhängig von der Isolationsstufe, die der Datenbankanbieter als Standardeinstellung ansieht. Standardmäßig wird beispielsweise auf SQL Server für die ein Lese Commit ausgeführt wird.  

Entity Framework packt keine Abfragen in einer Transaktion.  

Diese Standardfunktionalität eignet sich für viele Benutzer, und wenn dies der Fall ist, ist es nicht erforderlich, in EF6 etwas anderes zu tun. Schreiben Sie den Code einfach wie gewohnt.  

Einige Benutzer benötigen jedoch eine bessere Kontrolle über Ihre Transaktionen – Dies wird in den folgenden Abschnitten beschrieben.  

## <a name="how-the-apis-work"></a>Funktionsweise der APIs  

Vor EF6 Entity Framework das Öffnen der Datenbankverbindung selbst bestanden (es wurde eine Ausnahme ausgelöst, wenn eine bereits geöffnete Verbindung bestanden wurde). Da eine Transaktion nur für eine geöffnete Verbindung gestartet werden kann, bedeutete dies, dass ein Benutzer nur mehrere Vorgänge in eine Transaktion einschließen konnte, indem er entweder einen [transaktionscope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) verwendet oder die **ObjectContext. Connection** -Eigenschaft verwendet und mit dem Aufrufen von **Open ()** und **BeginTransaction ()** direkt auf dem zurückgegebenen **EntityConnection** -Objekt beginnt. Außerdem würden API-Aufrufe, die die Datenbank kontaktiert haben, fehlschlagen, wenn Sie eine Transaktion auf der zugrunde liegenden Datenbankverbindung selbst gestartet haben.  

> [!NOTE]
> Die Einschränkung, dass nur geschlossene Verbindungen akzeptiert werden, wurde in Entity Framework 6 entfernt. Weitere Informationen finden Sie unter [Verbindungs Verwaltung](xref:ef6/fundamentals/connection-management).  

Ab EF6 bietet das Framework nun Folgendes:  

1. **Database. BeginTransaction ()** : eine einfachere Methode für einen Benutzer, Transaktionen selbst innerhalb eines vorhandenen dbcontext zu starten und abzuschließen – so können mehrere Vorgänge in der gleichen Transaktion kombiniert werden, und es muss entweder ein Commit oder ein Rollback als ein Commit ausgeführt werden. Außerdem kann der Benutzer die Isolationsstufe für die Transaktion leichter angeben.  
2. **Database. UseTransaction ()** : Hiermit kann dbcontext eine Transaktion verwenden, die außerhalb der Entity Framework gestartet wurde.  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a>Kombinieren mehrerer Vorgänge zu einer Transaktion innerhalb desselben Kontexts  

**Database. BeginTransaction ()** verfügt über zwei über schreibungen – eine, die einen expliziten [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) annimmt und einen, der keine Argumente annimmt und den standardmäßigen IsolationLevel des zugrunde liegenden Datenbankanbieters verwendet. Beide über schreibungen geben ein **dbcontexttransaction** -Objekt zurück, das **Commit ()** -und **Rollback ()** -Methoden bereitstellt, die Commit und Rollback für die zugrunde liegende Speicher Transaktion ausführen.  

**Dbcontexttransaction** soll verworfen werden, nachdem ein Commit oder ein Rollback ausgeführt wurde. Eine einfache Möglichkeit hierfür ist die Verwendung von **(...) {...}** Syntax, die "verwerfen **()** " automatisch aufruft, wenn der using-Block abgeschlossen wird:  

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
> Das Starten einer Transaktion erfordert, dass die zugrunde liegende Speicher Verbindung geöffnet ist. Wenn Sie also Database. BeginTransaction () aufrufen, wird die Verbindung geöffnet, wenn Sie nicht bereits geöffnet ist. Wenn dbcontexttransaction die Verbindung geöffnet hat, wird Sie geschlossen, wenn "verwerfen ()" aufgerufen wird.  

### <a name="passing-an-existing-transaction-to-the-context"></a>Übergeben einer vorhandenen Transaktion an den Kontext  

Manchmal möchten Sie eine Transaktion, die im Bereich noch breiter ist und Vorgänge in derselben Datenbank, aber außerhalb von EF, umfasst. Um dies zu erreichen, müssen Sie die Verbindung öffnen und die Transaktion selbst starten und EF a dann mitteilen, dass die bereits geöffnete Datenbankverbindung verwendet werden soll, und b), um die vorhandene Transaktion für diese Verbindung zu verwenden.  

Zu diesem Zweck müssen Sie einen Konstruktor in der Kontext Klasse definieren und verwenden, die von einem der dbcontext-Konstruktoren erbt, von dem ich einen vorhandenen Verbindungsparameter und II) den booleschen Wert contextownsconnection erbt.  

> [!NOTE]
> Das contextownsconnection-Flag muss auf "false" festgelegt werden, wenn es in diesem Szenario aufgerufen wird. Dies ist wichtig, da es Entity Framework, dass die Verbindung nicht geschlossen werden soll, wenn es damit abgeschlossen ist (z. b. siehe Zeile 4 unten):  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

Außerdem müssen Sie die Transaktion selbst starten (einschließlich der IsolationLevel-Einstellung, wenn Sie die Standardeinstellung vermeiden möchten) und Entity Framework wissen, dass bereits eine Transaktion für die Verbindung gestartet wurde (siehe Zeile 33 unten).  

Anschließend können Sie Daten Bank Vorgänge entweder direkt auf der SqlConnection selbst oder im dbcontext ausführen. Alle Vorgänge dieser Art werden innerhalb einer Transaktion ausgeführt. Sie übernehmen die Verantwortung für das Commit oder Rollback der Transaktion und für das Aufrufen von "verwerfen ()" sowie für das Schließen und verwerfen der Datenbankverbindung. Beispiel:  

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

### <a name="clearing-up-the-transaction"></a>Löschen der Transaktion

Sie können NULL an Database. UseTransaction () übergeben, um Entity Framework Wissen der aktuellen Transaktion zu löschen. Entity Framework für die vorhandene Transaktion weder einen Commit durchführen noch ein Rollback durchführen, verwenden Sie daher with Care und nur, wenn Sie sicher sind, dass dies alles ist, was Sie tun möchten.  

### <a name="errors-in-usetransaction"></a>Fehler in UseTransaction

Es wird eine Ausnahme von Database. UseTransaction () angezeigt, wenn Sie eine Transaktion übergeben, wenn Folgendes gilt:  
- Entity Framework bereits eine Transaktion vorhanden ist.  
- Entity Framework wird bereits in einem Transaktions Bereich ausgeführt.  
- Das Verbindungs Objekt in der Transaktion hat den Wert NULL. Das heißt, die Transaktion ist keiner Verbindung zugeordnet – normalerweise ist dies ein Vorzeichen, dass diese Transaktion bereits abgeschlossen wurde.  
- Das Verbindungs Objekt in der übergebenen Transaktion entspricht nicht der Verbindung des Entity Framework.  

## <a name="using-transactions-with-other-features"></a>Verwenden von Transaktionen mit anderen Features  

In diesem Abschnitt wird erläutert, wie die obigen Transaktionen mit interagieren:  

- Verbindungsstabilität  
- Asynchrone Methoden  
- Transaktionscope-Transaktionen  

### <a name="connection-resiliency"></a>Verbindungsstabilität  

Das neue Feature für die Verbindungs Resilienz funktioniert nicht mit vom Benutzer initiierten Transaktionen. Weitere Informationen finden Sie unter [Wiederholungs Versuche für Ausführungs Strategien](xref:ef6/fundamentals/connection-resiliency/retry-logic#user-initiated-transactions-are-not-supported).  

### <a name="asynchronous-programming"></a>Asynchrone Programmierung  

Der Ansatz, der in den vorherigen Abschnitten beschrieben wird, benötigt keine weiteren Optionen oder Einstellungen für die Verwendung der [asynchronen Abfrage-und Speichermethoden](xref:ef6/fundamentals/async
). Beachten Sie jedoch, dass dies in Abhängigkeit davon, was Sie in den asynchronen Methoden tun, dazu führen kann, dass Transaktionen mit langer Ausführungszeit ausgeführt werden – was wiederum zu Deadlocks oder Blockierungen führen kann, was für die Leistung der gesamten Anwendung schlecht ist.  

### <a name="transactionscope-transactions"></a>Transaktionscope-Transaktionen  

Vor EF6 war die empfohlene Vorgehensweise zum Bereitstellen größerer Bereichs Transaktionen die Verwendung eines transaktionscope-Objekts:  

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

Der SqlConnection-und der-Entity Framework würden beide die Ambient-Transaktion transaktionscope verwenden und daher zusammen ein Commit ausgeführt werden.  

Beginnend mit .NET 4.5.1 transaktionscope wurde aktualisiert und funktioniert auch mit asynchronen Methoden über die [transaktionscopeasyncflowoption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) -Enumeration:  

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

Es gibt noch einige Einschränkungen für den transaktionscope-Ansatz:  

- Erfordert .NET 4.5.1 oder höher, um mit asynchronen Methoden zu arbeiten.  
- Sie kann nur in cloudumgebungen verwendet werden, wenn Sie sicher sind, dass Sie über eine und nur eine Verbindung verfügen (cloudszenarien unterstützen keine verteilten Transaktionen).  
- Sie kann nicht mit dem Database. UseTransaction ()-Ansatz der vorherigen Abschnitte kombiniert werden.  
- Sie löst Ausnahmen aus, wenn Sie DDL ausgeben und verteilte Transaktionen nicht über den MSDTC-Dienst aktiviert haben.  

Vorteile des transaktionscope-Ansatzes:  

- Eine lokale Transaktion wird automatisch auf eine verteilte Transaktion aktualisiert, wenn Sie mehr als eine Verbindung mit einer bestimmten Datenbank herstellen oder eine Verbindung mit einer Datenbank mit einer Verbindung mit einer anderen Datenbank innerhalb derselben Transaktion herstellen (Hinweis: der MSDTC-Dienst muss so konfiguriert sein, dass verteilte Transaktionen damit funktionieren).  
- Einfache Codierung. Wenn Sie möchten, dass die Transaktion Ambient ist und implizit im Hintergrund und nicht explizit unter ihrer Kontrolle behandelt wird, ist der transaktionscope-Ansatz möglicherweise besser geeignet.  

Zusammengefasst mit den neuen APIs "Database. BeginTransaction ()" und "Database. UseTransaction ()", ist der transaktionscope-Ansatz für die meisten Benutzer nicht mehr erforderlich. Wenn Sie transaktionscope weiterhin verwenden, beachten Sie die oben genannten Einschränkungen. Wir empfehlen, nach Möglichkeit den in den vorherigen Abschnitten beschriebenen Ansatz zu verwenden.  
