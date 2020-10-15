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
# <a name="automatic-detect-changes"></a><span data-ttu-id="c5a74-103">Automatisches Erkennen von Änderungen</span><span class="sxs-lookup"><span data-stu-id="c5a74-103">Automatic detect changes</span></span>
<span data-ttu-id="c5a74-104">Bei der Verwendung der meisten poco-Entitäten wird die Bestimmung, wie sich eine Entität geändert hat (und somit, welche Updates an die Datenbank gesendet werden müssen) vom Algorithmus zum Erkennen von Änderungen behandelt.</span><span class="sxs-lookup"><span data-stu-id="c5a74-104">When using most POCO entities the determination of how an entity has changed (and therefore which updates need to be sent to the database) is handled by the Detect Changes algorithm.</span></span> <span data-ttu-id="c5a74-105">Erkennen von Änderungen funktioniert, indem die Unterschiede zwischen den aktuellen Eigenschafts Werten der Entität und den ursprünglichen Eigenschafts Werten erkannt werden, die in einer Momentaufnahme gespeichert werden, wenn die Entität abgefragt oder angefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="c5a74-105">Detect Changes works by detecting the differences between the current property values of the entity and the original property values that are stored in a snapshot when the entity was queried or attached.</span></span> <span data-ttu-id="c5a74-106">Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="c5a74-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="c5a74-107">Standardmäßig erkennen Entity Framework Änderungen automatisch, wenn die folgenden Methoden aufgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="c5a74-107">By default, Entity Framework performs Detect Changes automatically when the following methods are called:</span></span>  

- <span data-ttu-id="c5a74-108">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="c5a74-108">DbSet.Find</span></span>  
- <span data-ttu-id="c5a74-109">Dbset. local</span><span class="sxs-lookup"><span data-stu-id="c5a74-109">DbSet.Local</span></span>  
- <span data-ttu-id="c5a74-110">Dbset. Add</span><span class="sxs-lookup"><span data-stu-id="c5a74-110">DbSet.Add</span></span>  
- <span data-ttu-id="c5a74-111">Dbset. AddRange</span><span class="sxs-lookup"><span data-stu-id="c5a74-111">DbSet.AddRange</span></span>
- <span data-ttu-id="c5a74-112">Dbset. Remove</span><span class="sxs-lookup"><span data-stu-id="c5a74-112">DbSet.Remove</span></span>  
- <span data-ttu-id="c5a74-113">Dbset. RemoveRange</span><span class="sxs-lookup"><span data-stu-id="c5a74-113">DbSet.RemoveRange</span></span>
- <span data-ttu-id="c5a74-114">Dbset. Attach</span><span class="sxs-lookup"><span data-stu-id="c5a74-114">DbSet.Attach</span></span>  
- <span data-ttu-id="c5a74-115">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="c5a74-115">DbContext.SaveChanges</span></span>  
- <span data-ttu-id="c5a74-116">Dbcontext. getvalidationerrors</span><span class="sxs-lookup"><span data-stu-id="c5a74-116">DbContext.GetValidationErrors</span></span>  
- <span data-ttu-id="c5a74-117">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="c5a74-117">DbContext.Entry</span></span>  
- <span data-ttu-id="c5a74-118">Dbchangetracker. Entries</span><span class="sxs-lookup"><span data-stu-id="c5a74-118">DbChangeTracker.Entries</span></span>  

## <a name="disabling-automatic-detection-of-changes"></a><span data-ttu-id="c5a74-119">Deaktivieren der automatischen Erkennung von Änderungen</span><span class="sxs-lookup"><span data-stu-id="c5a74-119">Disabling automatic detection of changes</span></span>  

<span data-ttu-id="c5a74-120">Wenn Sie viele Entitäten in ihrem Kontext nachverfolgen und eine dieser Methoden mehrmals in einer Schleife aufzurufen, können Sie erhebliche Leistungsverbesserungen erzielen, indem Sie die Erkennung von Änderungen für die Dauer der Schleife deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="c5a74-120">If you are tracking a lot of entities in your context and you call one of these methods many times in a loop, then you may get significant performance improvements by turning off detection of changes for the duration of the loop.</span></span> <span data-ttu-id="c5a74-121">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c5a74-121">For example:</span></span>  

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

<span data-ttu-id="c5a74-122">Vergessen Sie nicht, die Erkennung von Änderungen nach der Schleife erneut zu aktivieren – wir haben einen try/after-Vorgang verwendet, um sicherzustellen, dass Sie immer erneut aktiviert wird, auch wenn der Code in der Schleife eine Ausnahme auslöst.</span><span class="sxs-lookup"><span data-stu-id="c5a74-122">Don’t forget to re-enable detection of changes after the loop — We've used a try/finally to ensure it is always re-enabled even if code in the loop throws an exception.</span></span>  

<span data-ttu-id="c5a74-123">Eine Alternative zum Deaktivieren und erneuten Aktivieren von besteht darin, die automatische Erkennung von Änderungen jederzeit und entweder den Kontext aufzurufen. ChangeTracker. DetectChanges wird explizit verwendet oder verwendet Proxys für die Änderungs Nachverfolgung.</span><span class="sxs-lookup"><span data-stu-id="c5a74-123">An alternative to disabling and re-enabling is to leave automatic detection of changes turned off at all times and either call context.ChangeTracker.DetectChanges explicitly or use change tracking proxies diligently.</span></span> <span data-ttu-id="c5a74-124">Beide Optionen sind erweitert und können einfache Fehler in Ihre Anwendung einführen. verwenden Sie Sie daher mit Bedacht.</span><span class="sxs-lookup"><span data-stu-id="c5a74-124">Both of these options are advanced and can easily introduce subtle bugs into your application so use them with care.</span></span>  

<span data-ttu-id="c5a74-125">Wenn Sie viele Objekte aus einem Kontext hinzufügen oder entfernen müssen, sollten Sie die Verwendung von dbset. AddRange und dbset. RemoveRange in Erwägung ziehen.</span><span class="sxs-lookup"><span data-stu-id="c5a74-125">If you need to add or remove many objects from a context, consider using DbSet.AddRange and DbSet.RemoveRange.</span></span> <span data-ttu-id="c5a74-126">Diese Methoden erkennen Änderungen automatisch nur einmal, nachdem die hinzu füge-oder Entfernungs Vorgänge abgeschlossen wurden.</span><span class="sxs-lookup"><span data-stu-id="c5a74-126">This methods automatically detect changes only once after the add or remove operations are completed.</span></span> 
