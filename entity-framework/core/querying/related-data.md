---
title: Laden zugehöriger Daten – EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: d3a1810599771befb451715d93454fff63949771
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238306"
---
# <a name="loading-related-data"></a><span data-ttu-id="860f6-102">Laden zugehöriger Daten</span><span class="sxs-lookup"><span data-stu-id="860f6-102">Loading Related Data</span></span>

<span data-ttu-id="860f6-103">Mit Entity Framework Core können Sie die Navigationseigenschaften in Ihrem Modell zum Laden zugehöriger Entitäten verwenden.</span><span class="sxs-lookup"><span data-stu-id="860f6-103">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="860f6-104">Es gibt drei allgemeine O/RM-Muster für das Laden zugehöriger Daten.</span><span class="sxs-lookup"><span data-stu-id="860f6-104">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="860f6-105">**Eager Loading** bedeutet, dass die zugehörigen Daten als Teil der ersten Abfrage aus der Datenbank geladen werden.</span><span class="sxs-lookup"><span data-stu-id="860f6-105">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="860f6-106">**Explizites Laden** bedeutet, dass die zugehörigen Daten zu einem späteren Zeitpunkt explizit aus der Datenbank geladen werden.</span><span class="sxs-lookup"><span data-stu-id="860f6-106">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="860f6-107">**Lazy Loading** bedeutet, dass die zugehörigen Daten bei Zugriff auf die Navigationseigenschaft transparent aus der Datenbank geladen werden.</span><span class="sxs-lookup"><span data-stu-id="860f6-107">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]  
> <span data-ttu-id="860f6-108">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="860f6-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="860f6-109">Eager Loading</span><span class="sxs-lookup"><span data-stu-id="860f6-109">Eager loading</span></span>

<span data-ttu-id="860f6-110">Sie können mit der `Include`-Methode zugehörige Daten angeben, die in den Abfrageergebnissen enthalten sein sollen.</span><span class="sxs-lookup"><span data-stu-id="860f6-110">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="860f6-111">Im folgenden Beispiel weisen die in den Ergebnissen zurückgegebenen Blogs ihre `Posts`-Eigenschaft auf, die mit den zugehörigen Beiträgen aufgefüllt wurden.</span><span class="sxs-lookup"><span data-stu-id="860f6-111">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> <span data-ttu-id="860f6-112">Entity Framework Core korrigiert automatisch Navigationseigenschaften zur Korrektur für alle anderen Entitäten, die zuvor in die Kontextinstanz geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="860f6-112">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="860f6-113">Auch wenn Sie die Daten für eine Navigationseigenschaft nicht explizit eingeschlossen haben, kann die Eigenschaft folglich immer noch aufgefüllt werden, wenn einige oder alle zugehörigen Entitäten zuvor geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="860f6-113">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="860f6-114">Sie können zugehörigen Daten aus mehreren Beziehungen in einer einzelnen Abfrage einschließen.</span><span class="sxs-lookup"><span data-stu-id="860f6-114">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="860f6-115">Einschließen mehrerer Ebenen</span><span class="sxs-lookup"><span data-stu-id="860f6-115">Including multiple levels</span></span>

<span data-ttu-id="860f6-116">Sie können mit der `ThenInclude`-Methode einen Drilldown für Beziehungen ausführen, um mehrere Ebenen zugehöriger Daten einzuschließen.</span><span class="sxs-lookup"><span data-stu-id="860f6-116">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="860f6-117">Im folgenden Beispiel werden sämtliche Blogs, die zugehörigen Beiträge und die Autoren der einzelnen Beiträge geladen.</span><span class="sxs-lookup"><span data-stu-id="860f6-117">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="860f6-118">Sie können mehrere Aufrufe mit `ThenInclude` verbinden, um mit dem Einschließen weiterer Ebenen zugehöriger Daten fortzufahren.</span><span class="sxs-lookup"><span data-stu-id="860f6-118">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="860f6-119">Sie können all dies kombinieren, um zugehörige Daten aus mehreren Ebenen und mehreren Stämmen in derselben Abfrage einzuschließen.</span><span class="sxs-lookup"><span data-stu-id="860f6-119">You can combine all of this to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="860f6-120">Für eine der Entitäten, die eingeschlossen wird, sollten Sie mehrere zugehörige Entitäten einschließen.</span><span class="sxs-lookup"><span data-stu-id="860f6-120">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="860f6-121">Beispiel: Beim Abfragen von `Blogs` schließen Sie `Posts` ein und möchten anschließend `Author` und `Tags` von `Posts` einschließen.</span><span class="sxs-lookup"><span data-stu-id="860f6-121">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="860f6-122">Hierzu müssen Sie die einzelnen Includepfade beginnend beim Stamm angeben.</span><span class="sxs-lookup"><span data-stu-id="860f6-122">To do this, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="860f6-123">Beispiel: `Blog -> Posts -> Author` und `Blog -> Posts -> Tags`.</span><span class="sxs-lookup"><span data-stu-id="860f6-123">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="860f6-124">Dies bedeutet nicht, dass Sie redundante Verknüpfungen erhalten. In den meisten Fällen konsolidiert EF die Verknüpfungen beim Generieren von SQL.</span><span class="sxs-lookup"><span data-stu-id="860f6-124">This does not mean you will get redundant joins; in most cases, EF will consolidate the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="single-and-split-queries"></a><span data-ttu-id="860f6-125">Einzelne und geteilte Abfragen</span><span class="sxs-lookup"><span data-stu-id="860f6-125">Single and split queries</span></span>

> [!NOTE]
> <span data-ttu-id="860f6-126">Diese Funktion wird in EF Core 5.0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="860f6-126">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="860f6-127">In relationalen Datenbanken werden alle zugehörigen Entitäten standardmäßig durch Einführung von JOINs geladen:</span><span class="sxs-lookup"><span data-stu-id="860f6-127">In relational databases, all related entities are by default loaded by introducing JOINs:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="860f6-128">Wenn ein typischer Blog mehrere zugehörige Beiträge enthält, duplizieren die Zeilen für diese Beiträge die Informationen des Blogs, was zum so genannten „Problem der kartesischen Explosion“ führt.</span><span class="sxs-lookup"><span data-stu-id="860f6-128">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information, leading to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="860f6-129">Wenn weitere 1:n-Beziehungen geladen werden, wächst die Menge an duplizierten Daten weiter und beeinträchtigt die Leistung Ihrer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="860f6-129">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span>

<span data-ttu-id="860f6-130">In EF können Sie angeben, dass eine bestimmte LINQ-Abfrage in auf mehrere SQL-Abfragen *aufgeteilt* werden soll.</span><span class="sxs-lookup"><span data-stu-id="860f6-130">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="860f6-131">Anstelle von JOINs führen aufgeteilte Abfragen eine zusätzliche SQL-Abfrage für jede enthaltene 1:n-Navigation durch:</span><span class="sxs-lookup"><span data-stu-id="860f6-131">Instead of JOINs, split queries perform an additional SQL query for each included one-to-many navigation:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="860f6-132">Dadurch wird der folgende SQL-Code erzeugt:</span><span class="sxs-lookup"><span data-stu-id="860f6-132">This will produce the following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

<span data-ttu-id="860f6-133">Während bei diesem Vorgehen die Leistungsprobleme vermieden werden, die durch JOINs und die kartesische Explosion entstehen, weist es auch einige Nachteile auf:</span><span class="sxs-lookup"><span data-stu-id="860f6-133">While this avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

* <span data-ttu-id="860f6-134">Die meisten Datenbanken garantieren Datenkonsistenz bei einzelnen Abfragen. Bei mehreren Abfragen gibt es eine solche Garantie nicht.</span><span class="sxs-lookup"><span data-stu-id="860f6-134">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="860f6-135">Das bedeutet Folgendes: Wenn die Datenbank gleichzeitig mit der Ausführung Ihrer Abfragen aktualisiert wird, sind die resultierenden Daten möglicherweise nicht konsistent.</span><span class="sxs-lookup"><span data-stu-id="860f6-135">This means that if the database is being updated concurrently as your queries are being executed, resulting data may not be consistent.</span></span> <span data-ttu-id="860f6-136">Dieses Problem lässt sich minimieren, indem die Abfragen mit einer serialisierbaren oder Momentaufnahmentransaktion umschlossen werden, wobei auch dieses Vorgehen Leistungsprobleme nach sich ziehen kann.</span><span class="sxs-lookup"><span data-stu-id="860f6-136">This may be mitigated by wrapping the queries in a serializable or snapshot transaction, although this may create performance issues of its own.</span></span> <span data-ttu-id="860f6-137">Nähere Informationen dazu finden Sie in der Dokumentation zu Ihrer Datenbank.</span><span class="sxs-lookup"><span data-stu-id="860f6-137">Consult your database's documentation for more details.</span></span>
* <span data-ttu-id="860f6-138">Zurzeit impliziert jede Abfrage einen zusätzlichen Netzwerkroundtrip zu Ihrer Datenbank. Dies kann die Leistung beeinträchtigen, insbesondere dann, wenn die Latenz zur Datenbank hoch ist (z. B. bei Clouddiensten).</span><span class="sxs-lookup"><span data-stu-id="860f6-138">Each query currently implies an additional network roundtrip to your database; this can degrade performance, especially where latency to the database is high (e.g. cloud services).</span></span> <span data-ttu-id="860f6-139">EF Core wird dies zukünftig verbessern, indem die Abfragen in Batches für einen einzigen Roundtrip zusammengefasst werden.</span><span class="sxs-lookup"><span data-stu-id="860f6-139">EF Core will improve this in the future by batching the queries into a single roundtrip.</span></span>
* <span data-ttu-id="860f6-140">Zwar erlauben einige Datenbanken die gleichzeitige Nutzung der Ergebnisse mehrerer Abfragen (SQL Server mit MARS, Sqlite), aber in den meisten darf zu jedem Zeitpunkt immer nur eine Abfrage aktiv sein.</span><span class="sxs-lookup"><span data-stu-id="860f6-140">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="860f6-141">Das bedeutet, dass alle Ergebnisse früherer Abfragen im Arbeitsspeicher der Anwendung gepuffert werden müssen, bevor spätere Abfragen ausgeführt werden. Dadurch steigen die Arbeitsspeicheranforderungen möglicherweise deutlich.</span><span class="sxs-lookup"><span data-stu-id="860f6-141">This means that all results from earlier queries must be buffered in your application's memory before executing later queries, increasing your memory requirements in a potentially significant way.</span></span>

<span data-ttu-id="860f6-142">Leider gibt es nicht die eine perfekte Strategie zum Laden von zugehörigen Entitäten, die in allen Szenarien passt.</span><span class="sxs-lookup"><span data-stu-id="860f6-142">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="860f6-143">Erwägen Sie sehr sorgfältig die Vor- und Nachteile von einzelnen und geteilten Abfragen, und wählen Sie die Variante aus, die sich für Ihre Anforderungen besser eignet.</span><span class="sxs-lookup"><span data-stu-id="860f6-143">Carefully consider the advantages and disadvantages of single and split queries, and select the one that fits your needs.</span></span>

> [!NOTE]
> <span data-ttu-id="860f6-144">Zugehörige Entitäten mit 1:1-Beziehung werden immer über JOINs geladen, da dies keine Auswirkungen auf die Leistung hat.</span><span class="sxs-lookup"><span data-stu-id="860f6-144">One-to-one related entities are always loaded via JOINs, as this has no performance impact.</span></span>
>
> <span data-ttu-id="860f6-145">Zurzeit erfordert die Verwendung der Abfrageaufteilung in SQL Server die Einstellung `MultipleActiveResultSets=true` in der Verbindungszeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="860f6-145">At the moment, use of query splitting on SQL Server requires settings `MultipleActiveResultSets=true` in your connection string.</span></span> <span data-ttu-id="860f6-146">Diese Anforderung entfällt in einer zukünftigen Vorschauversion.</span><span class="sxs-lookup"><span data-stu-id="860f6-146">This requirement will be removed in a future preview.</span></span>
>
> <span data-ttu-id="860f6-147">Zukünftige Vorschauversionen von EF Core 5.0 ermöglichen die Angabe der Abfrageaufteilung als Standardwert für Ihren Kontext.</span><span class="sxs-lookup"><span data-stu-id="860f6-147">Future previews of EF Core 5.0 will allow specifying query splitting as the default for your context.</span></span>

### <a name="filtered-include"></a><span data-ttu-id="860f6-148">Gefilterte Include-Funktion</span><span class="sxs-lookup"><span data-stu-id="860f6-148">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="860f6-149">Diese Funktion wird in EF Core 5.0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="860f6-149">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="860f6-150">Wenn Sie „Include“ zum Laden von zugehörigen Daten anwenden, können Sie bestimmte aufzählbare Vorgänge auf die enthaltene Sammlungsnavigation anwenden, mit denen Sie die Ergebnisse filtern und sortieren können.</span><span class="sxs-lookup"><span data-stu-id="860f6-150">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="860f6-151">Folgende Vorgänge werden unterstützt: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip` und `Take`.</span><span class="sxs-lookup"><span data-stu-id="860f6-151">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="860f6-152">Solche Vorgänge sollten auf die Sammlungsnavigation in der Lambdafunktion angewendet werden, die an die Include-Methode übergeben wird, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="860f6-152">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="860f6-153">Jede per „Include“ eingeschlossene Navigation ermöglicht nur einen eindeutigen Satz von Filtervorgängen.</span><span class="sxs-lookup"><span data-stu-id="860f6-153">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="860f6-154">In Fällen, in denen mehrere Include-Vorgänge für eine bestimmte Sammlungsnavigation angewendet werden (`blog.Posts` in den folgenden Beispielen), können Filtervorgänge nur für einen von ihnen angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="860f6-154">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span> 

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="860f6-155">Alternativ können identische Vorgänge für jede Navigation angewendet werden, die mehrmals per „Include“ eingeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="860f6-155">Alternatively, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="860f6-156">Bei der Nachverfolgung von Abfragen können aufgrund einer [Navigationskorrektur](tracking.md) unerwartete Ergebnisse für gefilterte Include-Abfragen auftreten.</span><span class="sxs-lookup"><span data-stu-id="860f6-156">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](tracking.md).</span></span> <span data-ttu-id="860f6-157">Alle relevanten Entitäten, die zuvor abgefragt und in der Änderungsnachverfolgung gespeichert wurden, werden in den Ergebnissen der gefilterten Include-Abfrage angezeigt, auch wenn sie die Anforderungen des Filters nicht erfüllen.</span><span class="sxs-lookup"><span data-stu-id="860f6-157">All relevant entities that have been querried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="860f6-158">Verwenden Sie `NoTracking`-Abfragen, oder erstellen Sie DbContext neu, wenn Sie gefilterte Include-Abfragen in diesen Situationen nutzen.</span><span class="sxs-lookup"><span data-stu-id="860f6-158">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="860f6-159">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="860f6-159">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rathat than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

### <a name="include-on-derived-types"></a><span data-ttu-id="860f6-160">Einschließen in abgeleiteten Typen</span><span class="sxs-lookup"><span data-stu-id="860f6-160">Include on derived types</span></span>

<span data-ttu-id="860f6-161">Sie können zugehörige Daten aus Navigationen einschließen, die nur in einem abgeleiteten Typ mit `Include` und `ThenInclude` definiert werden.</span><span class="sxs-lookup"><span data-stu-id="860f6-161">You can include related data from navigations defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="860f6-162">Betrachten Sie das folgende Modell:</span><span class="sxs-lookup"><span data-stu-id="860f6-162">Given the following model:</span></span>

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

<span data-ttu-id="860f6-163">Inhalte der `School`-Navigation für alle Personen, die Studenten sind, können mit einer Reihe von Mustern vorzeitig geladen werden:</span><span class="sxs-lookup"><span data-stu-id="860f6-163">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="860f6-164">Mit cast</span><span class="sxs-lookup"><span data-stu-id="860f6-164">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="860f6-165">Mit dem Operator `as`</span><span class="sxs-lookup"><span data-stu-id="860f6-165">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="860f6-166">Mit einer Überladung von `Include`, die Parameter vom Typ `string` akzeptiert</span><span class="sxs-lookup"><span data-stu-id="860f6-166">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a><span data-ttu-id="860f6-167">Explizites Laden</span><span class="sxs-lookup"><span data-stu-id="860f6-167">Explicit loading</span></span>

<span data-ttu-id="860f6-168">Sie können eine Navigationseigenschaft über die API `DbContext.Entry(...)` explizit laden.</span><span class="sxs-lookup"><span data-stu-id="860f6-168">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="860f6-169">Sie können eine Navigationseigenschaft auch explizit laden, indem Sie eine separate Abfrage ausführen, die die zugehörigen Entitäten zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="860f6-169">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="860f6-170">Wenn die Änderungsnachverfolgung aktiviert ist, legt EF Core beim Laden einer Entität die Navigationseigenschaften der neu geladenen Entität automatisch so fest, dass diese auf bereits geladene Entitäten verweisen, und die Navigationseigenschaften bereits geladener Entitäten so, dass diese auf die neu geladene Entität verweisen.</span><span class="sxs-lookup"><span data-stu-id="860f6-170">If change tracking is enabled, then when loading an entity, EF Core will automatically set the navigation properties of the newly-loaded entitiy to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly-loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="860f6-171">Abfragen zugehöriger Entitäten</span><span class="sxs-lookup"><span data-stu-id="860f6-171">Querying related entities</span></span>

<span data-ttu-id="860f6-172">Sie können auch eine LINQ-Abfrage abrufen, welche die Inhalte einer Navigationseigenschaft darstellt.</span><span class="sxs-lookup"><span data-stu-id="860f6-172">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="860f6-173">Dadurch können Sie Aktionen, wie z.B. das Ausführen eines Aggregatoperators, über die zugehörigen Entitäten ausführen, ohne diese in den Speicher laden zu müssen.</span><span class="sxs-lookup"><span data-stu-id="860f6-173">This allows you to do things such as running an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="860f6-174">Darüber hinaus können Sie filtern, welche zugehörigen Entitäten in den Speicher geladen werden.</span><span class="sxs-lookup"><span data-stu-id="860f6-174">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="860f6-175">Lazy Loading</span><span class="sxs-lookup"><span data-stu-id="860f6-175">Lazy loading</span></span>

<span data-ttu-id="860f6-176">Die einfachste Möglichkeit für die Verwendung von Lazy Loading besteht in der Installation des Pakets [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) und der Aktivierung dieses Pakets durch Aufrufen von `UseLazyLoadingProxies`.</span><span class="sxs-lookup"><span data-stu-id="860f6-176">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="860f6-177">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="860f6-177">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="860f6-178">Oder bei Verwendung von AddDbContext:</span><span class="sxs-lookup"><span data-stu-id="860f6-178">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="860f6-179">EF Core aktiviert Lazy Loading anschließend für beliebige überschreibbare Navigationseigenschaften – diese müssen `virtual` und in einer Klasse enthalten sein, aus der sie geerbt werden können.</span><span class="sxs-lookup"><span data-stu-id="860f6-179">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="860f6-180">In den folgenden Entitäten wird beispielsweise Lazy Loading für die Navigationseigenschaften `Post.Blog` und `Blog.Posts` durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="860f6-180">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```

### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="860f6-181">Lazy Loading ohne Proxys</span><span class="sxs-lookup"><span data-stu-id="860f6-181">Lazy loading without proxies</span></span>

<span data-ttu-id="860f6-182">Lazy Loading-Proxys funktionieren, indem der Dienst `ILazyLoader` in eine Entität eingefügt wird, wie unter [Entitätstypenkonstruktoren](../modeling/constructors.md) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="860f6-182">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](../modeling/constructors.md).</span></span> <span data-ttu-id="860f6-183">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="860f6-183">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="860f6-184">Es ist nicht erforderlich, dass aus Entitätstypen vererbt werden kann, oder dass Navigationseigenschaften „virtual“ sind. Mit `new` erstellte Entitätsinstanzen können Lazy Loading ausführen, sobald sie einem Kontext angefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="860f6-184">This doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="860f6-185">Erforderlich ist ein Verweis auf den `ILazyLoader`-Dienst, der im [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/)-Paket definiert ist.</span><span class="sxs-lookup"><span data-stu-id="860f6-185">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="860f6-186">Dieses Paket enthält einen minimalen Satz von Typen, sodass es kaum Auswirkungen davon gibt.</span><span class="sxs-lookup"><span data-stu-id="860f6-186">This package contains a minimal set of types so that there is very little impact in depending on it.</span></span> <span data-ttu-id="860f6-187">Um jedoch die Abhängigkeit von EF Core-Paketen in den Entitätstypen vollständig zu vermeiden, ist es möglich, die Methode `ILazyLoader.Load` als Delegat einzufügen.</span><span class="sxs-lookup"><span data-stu-id="860f6-187">However, to completely avoid depending on any EF Core packages in the entity types, it is possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="860f6-188">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="860f6-188">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="860f6-189">Der oben stehende Code verwendet eine `Load`-Erweiterungsmethode, um die Verwendung des Delegaten sauberer zu gestalten:</span><span class="sxs-lookup"><span data-stu-id="860f6-189">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```

> [!NOTE]  
> <span data-ttu-id="860f6-190">Der Konstruktorparameter für den Lazy Loading-Delegaten muss als „lazyLoader“ bezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="860f6-190">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="860f6-191">Eine Konfiguration für die Verwendung eines anderen Namens ist für ein zukünftiges Release geplant.</span><span class="sxs-lookup"><span data-stu-id="860f6-191">Configuration to use a different name than this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="860f6-192">Zugehörige Daten und Serialisierung</span><span class="sxs-lookup"><span data-stu-id="860f6-192">Related data and serialization</span></span>

<span data-ttu-id="860f6-193">Da EF Core Navigationseigenschaften automatisch korrigiert, können sich in Ihrem Objektgraph Zyklen ergeben.</span><span class="sxs-lookup"><span data-stu-id="860f6-193">Because EF Core will automatically fix-up navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="860f6-194">Das Laden eines Blogs und der zugehörigen Beiträge führt beispielsweise zu einem Blogobjekt, das auf eine Sammlung von Beiträgen verweist.</span><span class="sxs-lookup"><span data-stu-id="860f6-194">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="860f6-195">Die einzelnen Beiträge verweisen wieder auf den Blog.</span><span class="sxs-lookup"><span data-stu-id="860f6-195">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="860f6-196">Einige Serialisierungsframeworks lassen solche Zyklen nicht zu.</span><span class="sxs-lookup"><span data-stu-id="860f6-196">Some serialization frameworks do not allow such cycles.</span></span> <span data-ttu-id="860f6-197">Json.NET löst beispielsweise folgende Ausnahme aus, wenn ein Zyklus erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="860f6-197">For example, Json.NET will throw the following exception if a cycle is encountered.</span></span>

> <span data-ttu-id="860f6-198">Newtonsoft.Json.JsonSerializationException: Für die Eigenschaft „Blog“ mit dem Typ „MyApplication.Models.Blog“ wurde eine auf sich selbst verweisende Schleife erkannt.</span><span class="sxs-lookup"><span data-stu-id="860f6-198">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="860f6-199">Wenn Sie ASP.NET Core verwenden, können Sie Json.NET so konfigurieren, dass im Objektgraph gefundene Zyklen ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="860f6-199">If you are using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="860f6-200">Dies erfolgt in der Methode `ConfigureServices(...)` in `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="860f6-200">This is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

<span data-ttu-id="860f6-201">Eine weitere Alternative ist die Ergänzung einer der Navigationseigenschaften mit dem `[JsonIgnore]`-Attribut, das Json.NET anweist, diese Navigationseigenschaft während der Serialisierung nicht zu durchlaufen.</span><span class="sxs-lookup"><span data-stu-id="860f6-201">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
