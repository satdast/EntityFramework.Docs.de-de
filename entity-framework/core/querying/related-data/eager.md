---
title: Eager Loading zugehöriger Daten – EF Core
description: Eager Loading zugehöriger Daten mit Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: 5ac15a85b28f21588639f34cbaa9ef76f366f7b5
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210466"
---
# <a name="eager-loading-of-related-data"></a><span data-ttu-id="19625-103">Eager Loading zugehöriger Daten</span><span class="sxs-lookup"><span data-stu-id="19625-103">Eager Loading of Related Data</span></span>

## <a name="eager-loading"></a><span data-ttu-id="19625-104">Eager Loading</span><span class="sxs-lookup"><span data-stu-id="19625-104">Eager loading</span></span>

<span data-ttu-id="19625-105">Sie können mit der `Include`-Methode zugehörige Daten angeben, die in den Abfrageergebnissen enthalten sein sollen.</span><span class="sxs-lookup"><span data-stu-id="19625-105">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="19625-106">Im folgenden Beispiel weisen die in den Ergebnissen zurückgegebenen Blogs ihre `Posts`-Eigenschaft auf, die mit den zugehörigen Beiträgen aufgefüllt wurden.</span><span class="sxs-lookup"><span data-stu-id="19625-106">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="19625-107">Entity Framework Core korrigiert automatisch Navigationseigenschaften zur Korrektur für alle anderen Entitäten, die zuvor in die Kontextinstanz geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="19625-107">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="19625-108">Auch wenn Sie die Daten für eine Navigationseigenschaft nicht explizit eingeschlossen haben, kann die Eigenschaft folglich immer noch aufgefüllt werden, wenn einige oder alle zugehörigen Entitäten zuvor geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="19625-108">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="19625-109">Sie können zugehörigen Daten aus mehreren Beziehungen in einer einzelnen Abfrage einschließen.</span><span class="sxs-lookup"><span data-stu-id="19625-109">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

## <a name="including-multiple-levels"></a><span data-ttu-id="19625-110">Einschließen mehrerer Ebenen</span><span class="sxs-lookup"><span data-stu-id="19625-110">Including multiple levels</span></span>

<span data-ttu-id="19625-111">Sie können mit der `ThenInclude`-Methode einen Drilldown für Beziehungen ausführen, um mehrere Ebenen zugehöriger Daten einzuschließen.</span><span class="sxs-lookup"><span data-stu-id="19625-111">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="19625-112">Im folgenden Beispiel werden sämtliche Blogs, die zugehörigen Beiträge und die Autoren der einzelnen Beiträge geladen.</span><span class="sxs-lookup"><span data-stu-id="19625-112">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="19625-113">Sie können mehrere Aufrufe mit `ThenInclude` verbinden, um mit dem Einschließen weiterer Ebenen zugehöriger Daten fortzufahren.</span><span class="sxs-lookup"><span data-stu-id="19625-113">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="19625-114">Sie können alle diese Aufrufe kombinieren, um eine gemeinsame Abfrage für verknüpfte Daten aus mehreren Ebenen und Stämmen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="19625-114">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="19625-115">Für eine der Entitäten, die eingeschlossen wird, sollten Sie mehrere zugehörige Entitäten einschließen.</span><span class="sxs-lookup"><span data-stu-id="19625-115">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="19625-116">Beispiel: Beim Abfragen von `Blogs` schließen Sie `Posts` ein und möchten anschließend `Author` und `Tags` von `Posts` einschließen.</span><span class="sxs-lookup"><span data-stu-id="19625-116">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="19625-117">Hierzu müssen Sie die einzelnen Include-Pfade beginnend beim Stamm angeben.</span><span class="sxs-lookup"><span data-stu-id="19625-117">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="19625-118">Beispiel: `Blog -> Posts -> Author` und `Blog -> Posts -> Tags`.</span><span class="sxs-lookup"><span data-stu-id="19625-118">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="19625-119">Dies bedeutet nicht, dass Sie redundante Verknüpfungen erhalten. In den meisten Fällen kombiniert EF die Verknüpfungen beim Generieren von SQL-Code.</span><span class="sxs-lookup"><span data-stu-id="19625-119">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

## <a name="single-and-split-queries"></a><span data-ttu-id="19625-120">Einzelne und geteilte Abfragen</span><span class="sxs-lookup"><span data-stu-id="19625-120">Single and split queries</span></span>

### <a name="single-queries"></a><span data-ttu-id="19625-121">Einzelne Abfragen</span><span class="sxs-lookup"><span data-stu-id="19625-121">Single queries</span></span>

<span data-ttu-id="19625-122">In relationalen Datenbanken werden alle zugehörigen Entitäten standardmäßig durch Einführung von JOINs geladen:</span><span class="sxs-lookup"><span data-stu-id="19625-122">In relational databases, all related entities are by default loaded by introducing JOINs:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="19625-123">Wenn ein typischer Blog mehrere zugehörige Beiträge enthält, duplizieren die Zeilen für diese Beiträge die Informationen des Blogs, was zum so genannten „Problem der kartesischen Explosion“ führt.</span><span class="sxs-lookup"><span data-stu-id="19625-123">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information, leading to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="19625-124">Wenn weitere 1:n-Beziehungen geladen werden, wächst die Menge an duplizierten Daten weiter und beeinträchtigt die Leistung Ihrer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="19625-124">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span> <span data-ttu-id="19625-125">Standardmäßig gibt EF Core eine Warnung aus, wenn Abfragen erkannt werden, die Include-Vorgänge für Sammlungen laden, die zu Leistungsproblemen führen können.</span><span class="sxs-lookup"><span data-stu-id="19625-125">By default, EF Core emits a warning if it detects queries loading collection includes that may cause performance issues.</span></span>

### <a name="split-queries"></a><span data-ttu-id="19625-126">Geteilte Abfragen</span><span class="sxs-lookup"><span data-stu-id="19625-126">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="19625-127">Diese Funktion wird in EF Core 5.0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="19625-127">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="19625-128">In EF können Sie angeben, dass eine bestimmte LINQ-Abfrage in auf mehrere SQL-Abfragen *aufgeteilt* werden soll.</span><span class="sxs-lookup"><span data-stu-id="19625-128">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="19625-129">Anstelle von JOINs führen aufgeteilte Abfragen eine zusätzliche SQL-Abfrage für jede enthaltene 1:n-Navigation durch:</span><span class="sxs-lookup"><span data-stu-id="19625-129">Instead of JOINs, split queries perform an additional SQL query for each included one-to-many navigation:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="19625-130">Dabei wird der folgende SQL-Code erzeugt:</span><span class="sxs-lookup"><span data-stu-id="19625-130">It will produce the following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> <span data-ttu-id="19625-131">Entitäten mit 1:1-Beziehung werden immer über Verknüpfungen in dieselbe Abfrage geladen, da dies keine Auswirkungen auf die Leistung hat.</span><span class="sxs-lookup"><span data-stu-id="19625-131">One-to-one related entities are always loaded via JOINs in the same query, as this has no performance impact.</span></span>

### <a name="enabling-split-queries-globally"></a><span data-ttu-id="19625-132">Globales Aktivieren von geteilten Abfragen</span><span class="sxs-lookup"><span data-stu-id="19625-132">Enabling split queries globally</span></span>

<span data-ttu-id="19625-133">Sie können auch geteilte Abfragen als Standardeinstellung für den Kontext Ihrer Anwendung konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="19625-133">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="19625-134">Wenn geteilte Abfragen als Standardeinstellung konfiguriert werden, ist es dennoch möglich, bestimmte Abfragen so zu konfigurieren, dass sie als einzelne Abfragen ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="19625-134">When split queries are configured as the default, it is still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSingleQuery&highlight=5)]

<span data-ttu-id="19625-135">Wenn der Abfrageteilungsmodus nicht explizit festgelegt ist, weder global noch für die Abfrage, und EF Core erkennt, dass eine einzelne Abfrage mehrere Include-Vorgänge für Sammlungen lädt, wird eine Warnung ausgegeben, um auf die möglichen daraus resultierenden Leistungsprobleme hinzuweisen.</span><span class="sxs-lookup"><span data-stu-id="19625-135">If the query splitting mode isn't explicitly specified - neither globally nor on the query - and EF Core detects that a single query loads multiple collection includes, a warning is emitted to draw attention to the potential resulting performance issues.</span></span> <span data-ttu-id="19625-136">Das Festlegen des Abfragemodus auf SingleQuery führt dazu, dass diese Warnung nicht generiert wird.</span><span class="sxs-lookup"><span data-stu-id="19625-136">Setting the query mode to SingleQuery will cause the warning not to be generated.</span></span>

### <a name="characteristics-of-split-queries"></a><span data-ttu-id="19625-137">Merkmale von geteilten Abfragen</span><span class="sxs-lookup"><span data-stu-id="19625-137">Characteristics of split queries</span></span>

<span data-ttu-id="19625-138">Zwar werden bei geteilten Abfragen die Leistungsprobleme im Zusammenhang mit Verknüpfungen und der kartesischen Explosion vermieden, allerdings weist dieser Modus auch einige Nachteile auf:</span><span class="sxs-lookup"><span data-stu-id="19625-138">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

* <span data-ttu-id="19625-139">Die meisten Datenbanken garantieren Datenkonsistenz bei einzelnen Abfragen. Bei mehreren Abfragen gibt es eine solche Garantie nicht.</span><span class="sxs-lookup"><span data-stu-id="19625-139">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="19625-140">Wenn die Datenbank während dem Ausführen der Abfragen aktualisiert wird, sind die resultierenden Daten möglicherweise nicht konsistent.</span><span class="sxs-lookup"><span data-stu-id="19625-140">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="19625-141">Dieses Problem können Sie minimieren, indem Sie die Abfragen mit einer serialisierbaren Transaktion oder Momentaufnahmentransaktion umschließen. Allerdings kann auch dieses Vorgehen Leistungsprobleme nach sich ziehen.</span><span class="sxs-lookup"><span data-stu-id="19625-141">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="19625-142">Weitere Informationen finden Sie in der Dokumentation Ihrer Datenbank.</span><span class="sxs-lookup"><span data-stu-id="19625-142">For more information, see your database's documentation.</span></span>
* <span data-ttu-id="19625-143">Jede Abfrage impliziert derzeit einen zusätzlichen Netzwerkroundtrip zu Ihrer Datenbank.</span><span class="sxs-lookup"><span data-stu-id="19625-143">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="19625-144">Durch mehrere Netzwerkroundtrips kann die Leistung beeinträchtigt werden, insbesondere, wenn die Latenz bei der Datenbank hoch ist (z. B. bei Clouddiensten).</span><span class="sxs-lookup"><span data-stu-id="19625-144">Multiple network roundtrip can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
* <span data-ttu-id="19625-145">Zwar erlauben einige Datenbanken die gleichzeitige Nutzung der Ergebnisse mehrerer Abfragen (SQL Server mit MARS, Sqlite), aber in den meisten darf zu jedem Zeitpunkt immer nur eine Abfrage aktiv sein.</span><span class="sxs-lookup"><span data-stu-id="19625-145">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="19625-146">Folglich müssen alle Ergebnisse früherer Abfragen im Arbeitsspeicher der Anwendung gepuffert werden, bevor spätere Abfragen ausgeführt werden. Dadurch steigen die Arbeitsspeicheranforderungen.</span><span class="sxs-lookup"><span data-stu-id="19625-146">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="19625-147">Leider gibt es nicht die eine perfekte Strategie zum Laden von zugehörigen Entitäten, die in allen Szenarien passt.</span><span class="sxs-lookup"><span data-stu-id="19625-147">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="19625-148">Erwägen Sie sehr sorgfältig die Vor- und Nachteile von einzelnen und geteilten Abfragen, und wählen Sie die Variante aus, die sich für Ihre Anforderungen besser eignet.</span><span class="sxs-lookup"><span data-stu-id="19625-148">Carefully consider the advantages and disadvantages of single and split queries, and select the one that fits your needs.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="19625-149">Gefilterte Include-Funktion</span><span class="sxs-lookup"><span data-stu-id="19625-149">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="19625-150">Diese Funktion wird in EF Core 5.0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="19625-150">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="19625-151">Wenn Sie „Include“ zum Laden von zugehörigen Daten anwenden, können Sie bestimmte aufzählbare Vorgänge auf die enthaltene Sammlungsnavigation anwenden, mit denen Sie die Ergebnisse filtern und sortieren können.</span><span class="sxs-lookup"><span data-stu-id="19625-151">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="19625-152">Folgende Vorgänge werden unterstützt: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip` und `Take`.</span><span class="sxs-lookup"><span data-stu-id="19625-152">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="19625-153">Solche Vorgänge sollten auf die Sammlungsnavigation in der Lambdafunktion angewendet werden, die an die Include-Methode übergeben wird, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="19625-153">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="19625-154">Jede per „Include“ eingeschlossene Navigation ermöglicht nur einen eindeutigen Satz von Filtervorgängen.</span><span class="sxs-lookup"><span data-stu-id="19625-154">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="19625-155">In Fällen, in denen mehrere Include-Vorgänge für eine bestimmte Sammlungsnavigation angewendet werden (`blog.Posts` in den folgenden Beispielen), können Filtervorgänge nur für einen von ihnen angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="19625-155">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="19625-156">Alternativ können identische Vorgänge für jede Navigation angewendet werden, die mehrmals über „Include“ eingeschlossen wird:</span><span class="sxs-lookup"><span data-stu-id="19625-156">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="19625-157">Bei der Nachverfolgung von Abfragen können aufgrund einer [Navigationskorrektur](xref:core/querying/tracking) unerwartete Ergebnisse für gefilterte Include-Abfragen auftreten.</span><span class="sxs-lookup"><span data-stu-id="19625-157">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="19625-158">Alle relevanten Entitäten, die zuvor abgefragt und in der Änderungsnachverfolgung gespeichert wurden, werden in den Ergebnissen der gefilterten Include-Abfrage angezeigt, auch wenn sie die Anforderungen des Filters nicht erfüllen.</span><span class="sxs-lookup"><span data-stu-id="19625-158">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="19625-159">Verwenden Sie `NoTracking`-Abfragen, oder erstellen Sie DbContext neu, wenn Sie gefilterte Include-Abfragen in diesen Situationen nutzen.</span><span class="sxs-lookup"><span data-stu-id="19625-159">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="19625-160">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="19625-160">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

## <a name="include-on-derived-types"></a><span data-ttu-id="19625-161">Einschließen in abgeleiteten Typen</span><span class="sxs-lookup"><span data-stu-id="19625-161">Include on derived types</span></span>

<span data-ttu-id="19625-162">Mit `Include` und `ThenInclude` können Sie verknüpfte Daten aus einer Navigation einschließen, die nur für einen abgeleiteten Typ definiert wurde.</span><span class="sxs-lookup"><span data-stu-id="19625-162">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="19625-163">Betrachten Sie das folgende Modell:</span><span class="sxs-lookup"><span data-stu-id="19625-163">Given the following model:</span></span>

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

<span data-ttu-id="19625-164">Inhalte der `School`-Navigation für alle Personen, die Studenten sind, können mit einer Reihe von Mustern vorzeitig geladen werden:</span><span class="sxs-lookup"><span data-stu-id="19625-164">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="19625-165">Mit cast</span><span class="sxs-lookup"><span data-stu-id="19625-165">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="19625-166">Mit dem Operator `as`</span><span class="sxs-lookup"><span data-stu-id="19625-166">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="19625-167">Mit einer Überladung von `Include`, die Parameter vom Typ `string` akzeptiert</span><span class="sxs-lookup"><span data-stu-id="19625-167">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```
  
