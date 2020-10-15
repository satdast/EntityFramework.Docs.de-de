---
title: Verbindungs Verwaltung-EF6
description: Verbindungs Verwaltung in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/connection-management
ms.openlocfilehash: e8c5c3615b4019be4c1915d3acaa91d60a55c85d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063295"
---
# <a name="connection-management"></a>Verbindungsverwaltung
Auf dieser Seite wird das Verhalten von Entity Framework in Bezug auf das Übergeben von Verbindungen mit dem Kontext und die Funktionalität der **Database. Connection. Open ()** -API beschrieben.  

## <a name="passing-connections-to-the-context"></a>Übergeben von Verbindungen an den Kontext  

### <a name="behavior-for-ef5-and-earlier-versions"></a>Verhalten für EF5 und frühere Versionen  

Es gibt zwei Konstruktoren, die Verbindungen akzeptieren:  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

Es ist möglich, diese zu verwenden, aber Sie müssen einige Einschränkungen umgehen:  

1. Wenn Sie eine offene Verbindung an eine dieser Optionen übergeben, wird das erste Mal, wenn das Framework versucht, es zu verwenden, eine InvalidOperationException ausgelöst, die besagt, dass eine bereits geöffnete Verbindung nicht erneut geöffnet werden kann.  
2. Das contextownsconnection-Flag gibt an, ob die zugrunde liegende Speicher Verbindung verworfen werden soll, wenn der Kontext verworfen wird. Unabhängig von dieser Einstellung wird die Speicher Verbindung jedoch immer geschlossen, wenn der Kontext verworfen wird. Wenn Sie also über mehr als einen dbcontext mit derselben Verbindung verfügen, wenn der Kontext zuerst verworfen wird, wird die Verbindung geschlossen (ähnlich, wenn Sie eine vorhandene ADO.NET-Verbindung mit einem dbcontext gemischt haben, schließt dbcontext die Verbindung immer, wenn Sie verworfen wird).  

Es ist möglich, die oben beschriebene Einschränkung zu umgehen, indem Sie eine geschlossene Verbindung übergibt und nur Code ausführt, der Sie nach der Erstellung aller Kontexte öffnet:  

``` csharp
using System.Collections.Generic;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Linq;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExampleEF5
    {         
        public static void TwoDbContextsOneConnection()
        {
            using (var context1 = new BloggingContext())
            {
                var conn =
                    ((EntityConnection)  
                        ((IObjectContextAdapter)context1).ObjectContext.Connection)  
                            .StoreConnection;

                using (var context2 = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    context2.Database.ExecuteSqlCommand(
                        @"UPDATE Blogs SET Rating = 5" +
                        " WHERE Name LIKE '%Entity Framework%'");

                    var query = context1.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context1.SaveChanges();
                }
            }
        }
    }
}
```  

Die zweite Einschränkung bedeutet lediglich, dass Sie Ihre dbcontext-Objekte verwerfen müssen, bis Sie bereit sind, die Verbindung zu schließen.  

### <a name="behavior-in-ef6-and-future-versions"></a>Verhalten in EF6 und zukünftigen Versionen  

In EF6 und zukünftigen Versionen hat dbcontext die gleichen beiden Konstruktoren, erfordert jedoch nicht mehr, dass die an den Konstruktor weiter gegebene Verbindung geschlossen wird, wenn Sie empfangen wird. Dies ist nun möglich:  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExample
    {
        public static void PassingAnOpenConnection()
        {
            using (var conn = new SqlConnection("{connectionString}"))
            {
                conn.Open();

                var sqlCommand = new SqlCommand();
                sqlCommand.Connection = conn;
                sqlCommand.CommandText =
                    @"UPDATE Blogs SET Rating = 5" +
                     " WHERE Name LIKE '%Entity Framework%'";
                sqlCommand.ExecuteNonQuery();

                using (var context = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    var query = context.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context.SaveChanges();
                }

                var sqlCommand2 = new SqlCommand();
                sqlCommand2.Connection = conn;
                sqlCommand2.CommandText =
                    @"UPDATE Blogs SET Rating = 7" +
                     " WHERE Name LIKE '%Entity Framework Rocks%'";
                sqlCommand2.ExecuteNonQuery();
            }
        }
    }
}
```  

Außerdem steuert das contextownsconnection-Flag nun, ob die Verbindung geschlossen und freigegeben wird, wenn der dbcontext verworfen wird. Im obigen Beispiel wird die Verbindung nicht geschlossen, wenn der kontextfrei gegeben wird (Zeile 32), wie es in früheren Versionen von EF der Fall war, sondern wenn die Verbindung selbst verworfen wurde (Zeile 40).  

Natürlich kann dbcontext weiterhin die Kontrolle über die Verbindung übernehmen (legen Sie einfach "contextownsconnection" auf "true" fest, oder verwenden Sie einen der anderen Konstruktoren), wenn Sie dies wünschen.  

> [!NOTE]
> Bei der Verwendung von Transaktionen mit diesem neuen Modell sind einige weitere Überlegungen zu beachten. Weitere Informationen finden Sie [unter Arbeiten mit Transaktionen](xref:ef6/saving/transactions).  

## <a name="databaseconnectionopen"></a>Database. Connection. Open ()  

### <a name="behavior-for-ef5-and-earlier-versions"></a>Verhalten für EF5 und frühere Versionen  

In EF5 und früheren Versionen ist ein Fehler aufgetreten, so dass **ObjectContext. Connection. State** nicht aktualisiert wurde, um den tatsächlichen Status der zugrunde liegenden Speicher Verbindung widerzuspiegeln. Wenn Sie z. b. den folgenden Code ausgeführt haben, können Sie den Status " **geschlossen** " zurückgeben, obwohl die zugrunde liegende Speicher Verbindung tatsächlich **geöffnet**ist.  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

Wenn Sie die Datenbankverbindung öffnen, indem Sie Database. Connection. Open () aufrufen, wird Sie separat geöffnet, bis Sie das nächste Mal eine Abfrage ausführen oder etwas aufrufen, das eine Datenbankverbindung erfordert (z. b. SaveChanges ()), aber danach wird die zugrunde liegende Speicher Verbindung geschlossen. Der Kontext wird dann erneut geöffnet, und die Verbindung wird immer wieder geschlossen, wenn ein anderer Daten Bank Vorgang erforderlich ist:  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;

namespace ConnectionManagementExamples
{
    public class DatabaseOpenConnectionBehaviorEF5
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open  
                // (though ObjectContext.Connection.State will report closed)

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);

                // The underlying store connection is still open  

                context.SaveChanges();

                // After SaveChanges() the underlying store connection is closed  
                // Each SaveChanges() / query etc now opens and immediately closes
                // the underlying store connection

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();
            }
        }
    }
}
```  

### <a name="behavior-in-ef6-and-future-versions"></a>Verhalten in EF6 und zukünftigen Versionen  

Bei EF6 und zukünftigen Versionen haben wir den Ansatz herangezogen, wenn der aufrufenden Code die Verbindung durch Aufrufen von Context öffnet. Database. Connection. Open () hat dann einen guten Grund dafür, und das Framework geht davon aus, dass es die Kontrolle über das Öffnen und Schließen der Verbindung wünscht und die Verbindung nicht mehr automatisch schließt.  

> [!NOTE]
> Dies kann potenziell zu Verbindungen führen, die für einen längeren Zeitraum geöffnet sind. verwenden Sie daher mit Bedacht.  

Wir haben auch den Code so aktualisiert, dass ObjectContext. Connection. State nun den Status der zugrunde liegenden Verbindung korrekt nachverfolgt.  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Core.EntityClient;
using System.Data.Entity.Infrastructure;

namespace ConnectionManagementExamples
{
    internal class DatabaseOpenConnectionBehaviorEF6
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open and the
                // ObjectContext.Connection.State correctly reports open too

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection remains open for the next operation  

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection is still open

           } // The context is disposed – so now the underlying store connection is closed
        }
    }
}
```  
