---
title: Abfragen ohne Nachverfolgung EF6
description: Keine nach Verfolgungs Abfragen in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: f80ac260-c2dc-484d-94a3-3424fd862f8b
uid: ef6/querying/no-tracking
ms.openlocfilehash: eb9e29c219e0cdf1e379cf8bb925f4226b1434a9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620289"
---
# <a name="no-tracking-queries"></a><span data-ttu-id="3e286-103">Abfragen ohne Nachverfolgung</span><span class="sxs-lookup"><span data-stu-id="3e286-103">No-Tracking Queries</span></span>
<span data-ttu-id="3e286-104">In manchen Fällen möchten Sie Entitäten aus einer Abfrage zurückerhalten, aber diese Entitäten können nicht vom Kontext nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="3e286-104">Sometimes you may want to get entities back from a query but not have those entities be tracked by the context.</span></span> <span data-ttu-id="3e286-105">Dies kann zu einer besseren Leistung führen, wenn eine große Anzahl von Entitäten in schreibgeschützten Szenarien abgefragt wird.</span><span class="sxs-lookup"><span data-stu-id="3e286-105">This may result in better performance when querying for large numbers of entities in read-only scenarios.</span></span> <span data-ttu-id="3e286-106">Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="3e286-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="3e286-107">Mit der neuen Erweiterungsmethode asnotracking können alle Abfragen auf diese Weise ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="3e286-107">A new extension method AsNoTracking allows any query to be run in this way.</span></span> <span data-ttu-id="3e286-108">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3e286-108">For example:</span></span>  

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
