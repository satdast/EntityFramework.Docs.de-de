---
title: Abfragen ohne Nachverfolgung EF6
description: Keine nach Verfolgungs Abfragen in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/querying/no-tracking
ms.openlocfilehash: ea4f05eb7a9b95fba55f70f249876bc9c5630f18
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073898"
---
# <a name="no-tracking-queries"></a>Abfragen ohne Nachverfolgung
In manchen Fällen möchten Sie Entitäten aus einer Abfrage zurückerhalten, aber diese Entitäten können nicht vom Kontext nachverfolgt werden. Dies kann zu einer besseren Leistung führen, wenn eine große Anzahl von Entitäten in schreibgeschützten Szenarien abgefragt wird. Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.  

Mit der neuen Erweiterungsmethode asnotracking können alle Abfragen auf diese Weise ausgeführt werden. Beispiel:  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs without tracking them
    var blogs1 = context.Blogs.AsNoTracking();

    // Query for some blogs without tracking them
    var blogs2 = context.Blogs
                        .Where(b => b.Name.Contains(".NET"))
                        .AsNoTracking()
                        .ToList();
}
```  
