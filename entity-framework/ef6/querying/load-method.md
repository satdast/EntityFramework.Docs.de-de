---
title: Die Load-Methode-EF6
description: Die Load-Methode in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/querying/load-method
ms.openlocfilehash: 1e6dd172eebdc177dad8ef312c8d14083a409410
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072966"
---
# <a name="the-load-method"></a><span data-ttu-id="b1924-103">Load-Methode</span><span class="sxs-lookup"><span data-stu-id="b1924-103">The Load Method</span></span>
<span data-ttu-id="b1924-104">Es gibt mehrere Szenarien, in denen Sie möglicherweise Entitäten aus der Datenbank in den Kontext laden möchten, ohne sofort etwas mit diesen Entitäten zu tun.</span><span class="sxs-lookup"><span data-stu-id="b1924-104">There are several scenarios where you may want to load entities from the database into the context without immediately doing anything with those entities.</span></span> <span data-ttu-id="b1924-105">Ein gutes Beispiel hierfür ist das Laden von Entitäten für die Datenbindung, wie unter [lokale Daten](xref:ef6/querying/local-data)beschrieben.</span><span class="sxs-lookup"><span data-stu-id="b1924-105">A good example of this is loading entities for data binding as described in [Local Data](xref:ef6/querying/local-data).</span></span> <span data-ttu-id="b1924-106">Eine gängige Methode hierfür ist das Schreiben einer LINQ-Abfrage und das anschließende aufzurufen der Liste, um die erstellte Liste sofort zu verwerfen.</span><span class="sxs-lookup"><span data-stu-id="b1924-106">One common way to do this is to write a LINQ query and then call ToList on it, only to immediately discard the created list.</span></span> <span data-ttu-id="b1924-107">Die Load-Erweiterungsmethode funktioniert genauso wie die-Liste, mit der Ausnahme, dass Sie die vollständige Erstellung der Liste vermeidet.</span><span class="sxs-lookup"><span data-stu-id="b1924-107">The Load extension method works just like ToList except that it avoids the creation of the list altogether.</span></span>  

<span data-ttu-id="b1924-108">Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="b1924-108">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="b1924-109">Im folgenden finden Sie zwei Beispiele für die Verwendung von Load.</span><span class="sxs-lookup"><span data-stu-id="b1924-109">Here are two examples of using Load.</span></span> <span data-ttu-id="b1924-110">Der erste wird aus einer Windows Forms Daten Bindungs Anwendung übernommen, bei der Load verwendet wird, um Entitäten vor der Bindung an die lokale Auflistung abzufragen, wie unter [lokale Daten](xref:ef6/querying/local-data)beschrieben:</span><span class="sxs-lookup"><span data-stu-id="b1924-110">The first is taken from a Windows Forms data binding application where Load is used to query for entities before binding to the local collection, as described in [Local Data](xref:ef6/querying/local-data):</span></span>  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

<span data-ttu-id="b1924-111">Das zweite Beispiel zeigt die Verwendung von Load, um eine gefilterte Auflistung verwandter Entitäten zu laden, wie unter Laden von verknüpften [Entitäten](xref:ef6/querying/related-data)beschrieben:</span><span class="sxs-lookup"><span data-stu-id="b1924-111">The second example shows using Load to load a filtered collection of related entities, as described in [Loading Related Entities](xref:ef6/querying/related-data):</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
        .Collection(b => b.Posts)
        .Query()
        .Where(p => p.Tags.Contains("entity-framework"))
        .Load();
}
```  
