---
title: Automatisches Erkennen von Änderungen EF6
description: Automatisches Erkennen von Änderungen in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: a8d1488d-9a54-4623-a76b-e81329ff2756
uid: ef6/saving/change-tracking/auto-detect-changes
ms.openlocfilehash: 938af809ac89d3490da9885497fc5601453e1c34
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619988"
---
# <a name="automatic-detect-changes"></a>Automatisches Erkennen von Änderungen
Bei der Verwendung der meisten poco-Entitäten wird die Bestimmung, wie sich eine Entität geändert hat (und somit, welche Updates an die Datenbank gesendet werden müssen) vom Algorithmus zum Erkennen von Änderungen behandelt. Erkennen von Änderungen funktioniert, indem die Unterschiede zwischen den aktuellen Eigenschafts Werten der Entität und den ursprünglichen Eigenschafts Werten erkannt werden, die in einer Momentaufnahme gespeichert werden, wenn die Entität abgefragt oder angefügt wurde. Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.  

Standardmäßig erkennen Entity Framework Änderungen automatisch, wenn die folgenden Methoden aufgerufen werden:  

- DbSet.Find  
- Dbset. local  
- Dbset. Add  
- Dbset. AddRange
- Dbset. Remove  
- Dbset. RemoveRange
- Dbset. Attach  
- DbContext.SaveChanges  
- Dbcontext. getvalidationerrors  
- DbContext.Entry  
- Dbchangetracker. Entries  

## <a name="disabling-automatic-detection-of-changes"></a>Deaktivieren der automatischen Erkennung von Änderungen  

Wenn Sie viele Entitäten in ihrem Kontext nachverfolgen und eine dieser Methoden mehrmals in einer Schleife aufzurufen, können Sie erhebliche Leistungsverbesserungen erzielen, indem Sie die Erkennung von Änderungen für die Dauer der Schleife deaktivieren. Beispiel:  

``` csharp
using (var context = new BloggingContext())
{
    try
    {
        context.Configuration.AutoDetectChangesEnabled = false;

        // Make many calls in a loop
        foreach (var blog in aLotOfBlogs)
        {
            context.Blogs.Add(blog);
        }
    }
    finally
    {
        context.Configuration.AutoDetectChangesEnabled = true;
    }
}
```  

Vergessen Sie nicht, die Erkennung von Änderungen nach der Schleife erneut zu aktivieren – wir haben einen try/after-Vorgang verwendet, um sicherzustellen, dass Sie immer erneut aktiviert wird, auch wenn der Code in der Schleife eine Ausnahme auslöst.  

Eine Alternative zum Deaktivieren und erneuten Aktivieren von besteht darin, die automatische Erkennung von Änderungen jederzeit und entweder den Kontext aufzurufen. ChangeTracker. DetectChanges wird explizit verwendet oder verwendet Proxys für die Änderungs Nachverfolgung. Beide Optionen sind erweitert und können einfache Fehler in Ihre Anwendung einführen. verwenden Sie Sie daher mit Bedacht.  

Wenn Sie viele Objekte aus einem Kontext hinzufügen oder entfernen müssen, sollten Sie die Verwendung von dbset. AddRange und dbset. RemoveRange in Erwägung ziehen. Diese Methoden erkennen Änderungen automatisch nur einmal, nachdem die hinzu füge-oder Entfernungs Vorgänge abgeschlossen wurden. 
