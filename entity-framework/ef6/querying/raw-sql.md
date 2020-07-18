---
title: RAW-SQL-Abfragen EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9e1ee76e-2499-408c-81e8-9b6c5d1945a0
ms.openlocfilehash: d336066b982e682e81067bbdac5b3781524cf6be
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451215"
---
# <a name="raw-sql-queries-ef6"></a>Unformatierte SQL-Abfragen (EF6)

Entity Framework ermöglicht es Ihnen, mithilfe von LINQ mit ihren Entitäts Klassen abzufragen. Es kann jedoch vorkommen, dass Sie Abfragen mit unformatierten SQL-Daten direkt für die Datenbank ausführen möchten. Dies schließt das Aufrufen von gespeicherten Prozeduren ein, was für Code First Modelle hilfreich sein kann, die derzeit keine Zuordnung zu gespeicherten Prozeduren unterstützen. Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.  

## <a name="writing-sql-queries-for-entities"></a>Schreiben von SQL-Abfragen für Entitäten  

Die sqlQuery-Methode in dbset ermöglicht das Schreiben einer unformatierten SQL-Abfrage, die Entitäts Instanzen zurückgibt. Die zurückgegebenen Objekte werden vom Kontext genau so nachverfolgt, als wären Sie durch eine LINQ-Abfrage zurückgegeben worden. Beispiel:  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

Beachten Sie, dass die Abfrage wie bei LINQ-Abfragen erst ausgeführt wird, wenn die Ergebnisse aufgezählt werden – im obigen Beispiel wird dies mit dem Auflistungs Auflistungs Vorgang durchgeführt.  

Es muss sorgfältig vorgegangen werden, wenn unformatierte SQL-Abfragen aus zwei Gründen geschrieben werden. Zuerst muss die Abfrage geschrieben werden, um sicherzustellen, dass nur Entitäten zurückgegeben werden, die tatsächlich den angeforderten Typ aufweisen. Wenn z. b. Funktionen wie die Vererbung verwendet werden, ist es einfach, eine Abfrage zu schreiben, mit der Entitäten des falschen CLR-Typs erstellt werden.  

Zweitens machen einige Typen von unformatierten SQL-Abfragen potenzielle Sicherheitsrisiken offen, insbesondere bei SQL Injection-Angriffen. Stellen Sie sicher, dass Sie die Parameter in der Abfrage auf die richtige Weise verwenden, um solche Angriffe zu schützen.  

### <a name="loading-entities-from-stored-procedures"></a>Entitäten aus gespeicherten Prozeduren laden  

Mit dbset. sqlQuery können Sie Entitäten aus den Ergebnissen einer gespeicherten Prozedur laden. Der folgende Code ruft beispielsweise den dbo auf. Getblogs-Prozedur in der-Datenbank:  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

Sie können Parameter auch an eine gespeicherte Prozedur übergeben, indem Sie die folgende Syntax verwenden:  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a>Schreiben von SQL-Abfragen für nicht-Entitäts Typen  

Eine SQL-Abfrage, die Instanzen eines beliebigen Typs zurückgibt, einschließlich primitiver Typen, kann mithilfe der sqlQuery-Methode für die Datenbankklasse erstellt werden. Beispiel:  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

Die von sqlQuery für die Datenbank zurückgegebenen Ergebnisse werden nie vom Kontext nachverfolgt, auch wenn die Objekte Instanzen eines Entitäts Typs sind.  

## <a name="sending-raw-commands-to-the-database"></a>Senden von rohbefehlen an die Datenbank  

Nicht-Abfrage Befehle können mithilfe der ExecuteSqlCommand-Methode für die Datenbank an die Datenbank gesendet werden. Beispiel:  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

Beachten Sie, dass alle Änderungen, die an Daten in der Datenbank mithilfe von ExecuteSqlCommand vorgenommen werden, für den Kontext nicht transparent sind, bis Entitäten geladen oder aus der Datenbank erneut geladen werden.  

### <a name="output-parameters"></a>Ausgabeparameter  

Wenn Output-Parameter verwendet werden, sind ihre Werte erst verfügbar, wenn die Ergebnisse vollständig gelesen wurden. Dies ist auf das zugrunde liegende Verhalten von DbDataReader zurückzuführen. Weitere Informationen finden [Sie unter Abrufen von Daten mithilfe eines DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) .  
