---
title: Automatisches Erkennen von Änderungen EF6
description: Automatisches Erkennen von Änderungen in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/auto-detect-changes
ms.openlocfilehash: 159143a9ade64a65e857a30117e577b21e0b9f98
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064495"
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
