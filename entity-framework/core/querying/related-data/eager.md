---
title: Eager Loading zugehöriger Daten – EF Core
description: Eager Loading zugehöriger Daten mit Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: 66956fcd85bb21a08c69fa93b93c12382bbfc8eb
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003574"
---
# <a name="eager-loading-of-related-data"></a><span data-ttu-id="7dae4-103">Eager Loading zugehöriger Daten</span><span class="sxs-lookup"><span data-stu-id="7dae4-103">Eager Loading of Related Data</span></span>

## <a name="eager-loading"></a><span data-ttu-id="7dae4-104">Eager Loading</span><span class="sxs-lookup"><span data-stu-id="7dae4-104">Eager loading</span></span>

<span data-ttu-id="7dae4-105">Sie können mit der `Include`-Methode zugehörige Daten angeben, die in den Abfrageergebnissen enthalten sein sollen.</span><span class="sxs-lookup"><span data-stu-id="7dae4-105">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="7dae4-106">Im folgenden Beispiel weisen die in den Ergebnissen zurückgegebenen Blogs ihre `Posts`-Eigenschaft auf, die mit den zugehörigen Beiträgen aufgefüllt wurden.</span><span class="sxs-lookup"><span data-stu-id="7dae4-106">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="7dae4-107">Entity Framework Core korrigiert automatisch Navigationseigenschaften zur Korrektur für alle anderen Entitäten, die zuvor in die Kontextinstanz geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="7dae4-107">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="7dae4-108">Auch wenn Sie die Daten für eine Navigationseigenschaft nicht explizit eingeschlossen haben, kann die Eigenschaft folglich immer noch aufgefüllt werden, wenn einige oder alle zugehörigen Entitäten zuvor geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="7dae4-108">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="7dae4-109">Sie können zugehörigen Daten aus mehreren Beziehungen in einer einzelnen Abfrage einschließen.</span><span class="sxs-lookup"><span data-stu-id="7dae4-109">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

> [!CAUTION]
> <span data-ttu-id="7dae4-110">Eager Loading einer Sammlungsnavigation in einer einzelnen Abfrage kann zu Leistungsproblemen führen.</span><span class="sxs-lookup"><span data-stu-id="7dae4-110">Eager loading a collection navigation in a single query may cause performance issues.</span></span> <span data-ttu-id="7dae4-111">Weitere Informationen finden Sie unter [Vergleich von Einzel- und geteilten Abfragen](xref:core/querying/single-split-queries).</span><span class="sxs-lookup"><span data-stu-id="7dae4-111">For more information, see [Single vs. split queries](xref:core/querying/single-split-queries).</span></span>

## <a name="including-multiple-levels"></a><span data-ttu-id="7dae4-112">Einschließen mehrerer Ebenen</span><span class="sxs-lookup"><span data-stu-id="7dae4-112">Including multiple levels</span></span>

<span data-ttu-id="7dae4-113">Sie können mit der `ThenInclude`-Methode einen Drilldown für Beziehungen ausführen, um mehrere Ebenen zugehöriger Daten einzuschließen.</span><span class="sxs-lookup"><span data-stu-id="7dae4-113">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="7dae4-114">Im folgenden Beispiel werden sämtliche Blogs, die zugehörigen Beiträge und die Autoren der einzelnen Beiträge geladen.</span><span class="sxs-lookup"><span data-stu-id="7dae4-114">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

<span data-ttu-id="7dae4-115">Sie können mehrere Aufrufe mit `ThenInclude` verbinden, um mit dem Einschließen weiterer Ebenen zugehöriger Daten fortzufahren.</span><span class="sxs-lookup"><span data-stu-id="7dae4-115">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

<span data-ttu-id="7dae4-116">Sie können alle diese Aufrufe kombinieren, um eine gemeinsame Abfrage für verknüpfte Daten aus mehreren Ebenen und Stämmen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="7dae4-116">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

<span data-ttu-id="7dae4-117">Für eine der Entitäten, die eingeschlossen wird, sollten Sie mehrere zugehörige Entitäten einschließen.</span><span class="sxs-lookup"><span data-stu-id="7dae4-117">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="7dae4-118">Beispiel: Beim Abfragen von `Blogs` schließen Sie `Posts` ein und möchten anschließend `Author` und `Tags` von `Posts` einschließen.</span><span class="sxs-lookup"><span data-stu-id="7dae4-118">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="7dae4-119">Hierzu müssen Sie die einzelnen Include-Pfade beginnend beim Stamm angeben.</span><span class="sxs-lookup"><span data-stu-id="7dae4-119">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="7dae4-120">Beispiel: `Blog -> Posts -> Author` und `Blog -> Posts -> Tags`.</span><span class="sxs-lookup"><span data-stu-id="7dae4-120">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="7dae4-121">Dies bedeutet nicht, dass Sie redundante Verknüpfungen erhalten. In den meisten Fällen kombiniert EF die Verknüpfungen beim Generieren von SQL-Code.</span><span class="sxs-lookup"><span data-stu-id="7dae4-121">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

> [!TIP]
> <span data-ttu-id="7dae4-122">Sie können auch mehrere Navigationen mit einer einzelnen `Include`-Methode laden.</span><span class="sxs-lookup"><span data-stu-id="7dae4-122">You can also load multiple navigations using a single `Include` method.</span></span> <span data-ttu-id="7dae4-123">Dies ist für „Navigationsketten“ möglich, die alle Verweise sind, oder wenn Sie mit einer einzelnen Sammlung enden.</span><span class="sxs-lookup"><span data-stu-id="7dae4-123">This is possible for navigation "chains" that are all references, or when they end with a single collection.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeMultipleNavigationsWithSingleInclude)]

## <a name="filtered-include"></a><span data-ttu-id="7dae4-124">Gefilterte Include-Funktion</span><span class="sxs-lookup"><span data-stu-id="7dae4-124">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="7dae4-125">Dieses Feature wurde in EF Core 5.0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="7dae4-125">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="7dae4-126">Wenn Sie „Include“ zum Laden zugehöriger Daten anwenden, können Sie bestimmte aufzählbare Vorgänge zur enthaltenen Sammlungsnavigation hinzufügen, mit denen Sie die Ergebnisse filtern und sortieren können.</span><span class="sxs-lookup"><span data-stu-id="7dae4-126">When applying Include to load related data, you can add certain enumerable operations to the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="7dae4-127">Folgende Vorgänge werden unterstützt: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip` und `Take`.</span><span class="sxs-lookup"><span data-stu-id="7dae4-127">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="7dae4-128">Solche Vorgänge sollten auf die Sammlungsnavigation in der Lambdafunktion angewendet werden, die an die Include-Methode übergeben wird, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="7dae4-128">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

<span data-ttu-id="7dae4-129">Jede per „Include“ eingeschlossene Navigation ermöglicht nur einen eindeutigen Satz von Filtervorgängen.</span><span class="sxs-lookup"><span data-stu-id="7dae4-129">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="7dae4-130">In Fällen, in denen mehrere Include-Vorgänge für eine bestimmte Sammlungsnavigation angewendet werden (`blog.Posts` in den folgenden Beispielen), können Filtervorgänge nur für einen von ihnen angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="7dae4-130">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="7dae4-131">Alternativ können identische Vorgänge für jede Navigation angewendet werden, die mehrmals über „Include“ eingeschlossen wird:</span><span class="sxs-lookup"><span data-stu-id="7dae4-131">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="7dae4-132">Bei der Nachverfolgung von Abfragen können aufgrund einer [Navigationskorrektur](xref:core/querying/tracking) unerwartete Ergebnisse für gefilterte Include-Abfragen auftreten.</span><span class="sxs-lookup"><span data-stu-id="7dae4-132">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="7dae4-133">Alle relevanten Entitäten, die zuvor abgefragt und in der Änderungsnachverfolgung gespeichert wurden, werden in den Ergebnissen der gefilterten Include-Abfrage angezeigt, auch wenn sie die Anforderungen des Filters nicht erfüllen.</span><span class="sxs-lookup"><span data-stu-id="7dae4-133">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="7dae4-134">Verwenden Sie `NoTracking`-Abfragen, oder erstellen Sie DbContext neu, wenn Sie gefilterte Include-Abfragen in diesen Situationen nutzen.</span><span class="sxs-lookup"><span data-stu-id="7dae4-134">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="7dae4-135">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7dae4-135">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

> [!NOTE]
> <span data-ttu-id="7dae4-136">Bei Nachverfolgungsabfragen gilt die Navigation, auf die die gefilterte Include-Funktion angewendet wurde, als geladen.</span><span class="sxs-lookup"><span data-stu-id="7dae4-136">In case of tracking queries, the navigation on which filtered include was applied is considered to be loaded.</span></span> <span data-ttu-id="7dae4-137">Das bedeutet, dass EF Core nicht versucht, seine Werte mit [explizitem Laden](xref:core/querying/related-data/explicit) oder [verzögertem Laden](xref:core/querying/related-data/lazy) neu zu laden, auch wenn einige Elemente noch fehlen könnten.</span><span class="sxs-lookup"><span data-stu-id="7dae4-137">This means that EF Core will not attempt to re-load it's values using [explicit loading](xref:core/querying/related-data/explicit) or [lazy loading](xref:core/querying/related-data/lazy), even though some elements could still be missing.</span></span>

## <a name="include-on-derived-types"></a><span data-ttu-id="7dae4-138">Einschließen in abgeleiteten Typen</span><span class="sxs-lookup"><span data-stu-id="7dae4-138">Include on derived types</span></span>

<span data-ttu-id="7dae4-139">Mit `Include` und `ThenInclude` können Sie verknüpfte Daten aus einer Navigation einschließen, die nur für einen abgeleiteten Typ definiert wurde.</span><span class="sxs-lookup"><span data-stu-id="7dae4-139">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="7dae4-140">Betrachten Sie das folgende Modell:</span><span class="sxs-lookup"><span data-stu-id="7dae4-140">Given the following model:</span></span>

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

<span data-ttu-id="7dae4-141">Inhalte der `School`-Navigation für alle Personen, die Studenten sind, können mit einer Reihe von Mustern vorzeitig geladen werden:</span><span class="sxs-lookup"><span data-stu-id="7dae4-141">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="7dae4-142">Mit cast</span><span class="sxs-lookup"><span data-stu-id="7dae4-142">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="7dae4-143">Mit dem Operator `as`</span><span class="sxs-lookup"><span data-stu-id="7dae4-143">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="7dae4-144">Mit einer Überladung von `Include`, die Parameter vom Typ `string` akzeptiert</span><span class="sxs-lookup"><span data-stu-id="7dae4-144">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```
