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
# <a name="no-tracking-queries"></a><span data-ttu-id="7d237-103">Abfragen ohne Nachverfolgung</span><span class="sxs-lookup"><span data-stu-id="7d237-103">No-Tracking Queries</span></span>
<span data-ttu-id="7d237-104">In manchen Fällen möchten Sie Entitäten aus einer Abfrage zurückerhalten, aber diese Entitäten können nicht vom Kontext nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="7d237-104">Sometimes you may want to get entities back from a query but not have those entities be tracked by the context.</span></span> <span data-ttu-id="7d237-105">Dies kann zu einer besseren Leistung führen, wenn eine große Anzahl von Entitäten in schreibgeschützten Szenarien abgefragt wird.</span><span class="sxs-lookup"><span data-stu-id="7d237-105">This may result in better performance when querying for large numbers of entities in read-only scenarios.</span></span> <span data-ttu-id="7d237-106">Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="7d237-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="7d237-107">Mit der neuen Erweiterungsmethode asnotracking können alle Abfragen auf diese Weise ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="7d237-107">A new extension method AsNoTracking allows any query to be run in this way.</span></span> <span data-ttu-id="7d237-108">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7d237-108">For example:</span></span>  

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
