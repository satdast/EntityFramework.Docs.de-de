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
# <a name="automatic-detect-changes"></a><span data-ttu-id="492ba-103">Automatisches Erkennen von Änderungen</span><span class="sxs-lookup"><span data-stu-id="492ba-103">Automatic detect changes</span></span>
<span data-ttu-id="492ba-104">Bei der Verwendung der meisten poco-Entitäten wird die Bestimmung, wie sich eine Entität geändert hat (und somit, welche Updates an die Datenbank gesendet werden müssen) vom Algorithmus zum Erkennen von Änderungen behandelt.</span><span class="sxs-lookup"><span data-stu-id="492ba-104">When using most POCO entities the determination of how an entity has changed (and therefore which updates need to be sent to the database) is handled by the Detect Changes algorithm.</span></span> <span data-ttu-id="492ba-105">Erkennen von Änderungen funktioniert, indem die Unterschiede zwischen den aktuellen Eigenschafts Werten der Entität und den ursprünglichen Eigenschafts Werten erkannt werden, die in einer Momentaufnahme gespeichert werden, wenn die Entität abgefragt oder angefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="492ba-105">Detect Changes works by detecting the differences between the current property values of the entity and the original property values that are stored in a snapshot when the entity was queried or attached.</span></span> <span data-ttu-id="492ba-106">Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="492ba-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="492ba-107">Standardmäßig erkennen Entity Framework Änderungen automatisch, wenn die folgenden Methoden aufgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="492ba-107">By default, Entity Framework performs Detect Changes automatically when the following methods are called:</span></span>  

- <span data-ttu-id="492ba-108">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="492ba-108">DbSet.Find</span></span>  
- <span data-ttu-id="492ba-109">Dbset. local</span><span class="sxs-lookup"><span data-stu-id="492ba-109">DbSet.Local</span></span>  
- <span data-ttu-id="492ba-110">Dbset. Add</span><span class="sxs-lookup"><span data-stu-id="492ba-110">DbSet.Add</span></span>  
- <span data-ttu-id="492ba-111">Dbset. AddRange</span><span class="sxs-lookup"><span data-stu-id="492ba-111">DbSet.AddRange</span></span>
- <span data-ttu-id="492ba-112">Dbset. Remove</span><span class="sxs-lookup"><span data-stu-id="492ba-112">DbSet.Remove</span></span>  
- <span data-ttu-id="492ba-113">Dbset. RemoveRange</span><span class="sxs-lookup"><span data-stu-id="492ba-113">DbSet.RemoveRange</span></span>
- <span data-ttu-id="492ba-114">Dbset. Attach</span><span class="sxs-lookup"><span data-stu-id="492ba-114">DbSet.Attach</span></span>  
- <span data-ttu-id="492ba-115">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="492ba-115">DbContext.SaveChanges</span></span>  
- <span data-ttu-id="492ba-116">Dbcontext. getvalidationerrors</span><span class="sxs-lookup"><span data-stu-id="492ba-116">DbContext.GetValidationErrors</span></span>  
- <span data-ttu-id="492ba-117">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="492ba-117">DbContext.Entry</span></span>  
- <span data-ttu-id="492ba-118">Dbchangetracker. Entries</span><span class="sxs-lookup"><span data-stu-id="492ba-118">DbChangeTracker.Entries</span></span>  

## <a name="disabling-automatic-detection-of-changes"></a><span data-ttu-id="492ba-119">Deaktivieren der automatischen Erkennung von Änderungen</span><span class="sxs-lookup"><span data-stu-id="492ba-119">Disabling automatic detection of changes</span></span>  

<span data-ttu-id="492ba-120">Wenn Sie viele Entitäten in ihrem Kontext nachverfolgen und eine dieser Methoden mehrmals in einer Schleife aufzurufen, können Sie erhebliche Leistungsverbesserungen erzielen, indem Sie die Erkennung von Änderungen für die Dauer der Schleife deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="492ba-120">If you are tracking a lot of entities in your context and you call one of these methods many times in a loop, then you may get significant performance improvements by turning off detection of changes for the duration of the loop.</span></span> <span data-ttu-id="492ba-121">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="492ba-121">For example:</span></span>  

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

<span data-ttu-id="492ba-122">Vergessen Sie nicht, die Erkennung von Änderungen nach der Schleife erneut zu aktivieren – wir haben einen try/after-Vorgang verwendet, um sicherzustellen, dass Sie immer erneut aktiviert wird, auch wenn der Code in der Schleife eine Ausnahme auslöst.</span><span class="sxs-lookup"><span data-stu-id="492ba-122">Don’t forget to re-enable detection of changes after the loop — We've used a try/finally to ensure it is always re-enabled even if code in the loop throws an exception.</span></span>  

<span data-ttu-id="492ba-123">Eine Alternative zum Deaktivieren und erneuten Aktivieren von besteht darin, die automatische Erkennung von Änderungen jederzeit und entweder den Kontext aufzurufen. ChangeTracker. DetectChanges wird explizit verwendet oder verwendet Proxys für die Änderungs Nachverfolgung.</span><span class="sxs-lookup"><span data-stu-id="492ba-123">An alternative to disabling and re-enabling is to leave automatic detection of changes turned off at all times and either call context.ChangeTracker.DetectChanges explicitly or use change tracking proxies diligently.</span></span> <span data-ttu-id="492ba-124">Beide Optionen sind erweitert und können einfache Fehler in Ihre Anwendung einführen. verwenden Sie Sie daher mit Bedacht.</span><span class="sxs-lookup"><span data-stu-id="492ba-124">Both of these options are advanced and can easily introduce subtle bugs into your application so use them with care.</span></span>  

<span data-ttu-id="492ba-125">Wenn Sie viele Objekte aus einem Kontext hinzufügen oder entfernen müssen, sollten Sie die Verwendung von dbset. AddRange und dbset. RemoveRange in Erwägung ziehen.</span><span class="sxs-lookup"><span data-stu-id="492ba-125">If you need to add or remove many objects from a context, consider using DbSet.AddRange and DbSet.RemoveRange.</span></span> <span data-ttu-id="492ba-126">Diese Methoden erkennen Änderungen automatisch nur einmal, nachdem die hinzu füge-oder Entfernungs Vorgänge abgeschlossen wurden.</span><span class="sxs-lookup"><span data-stu-id="492ba-126">This methods automatically detect changes only once after the add or remove operations are completed.</span></span> 
