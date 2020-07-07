---
title: Laden zugehöriger Daten – EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 86b9d08377ea8295b746e5f0217a408edcfe1517
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370472"
---
# <a name="loading-related-data"></a><span data-ttu-id="8dd4a-102">Laden zugehöriger Daten</span><span class="sxs-lookup"><span data-stu-id="8dd4a-102">Loading Related Data</span></span>

<span data-ttu-id="8dd4a-103">Mit Entity Framework Core können Sie die Navigationseigenschaften in Ihrem Modell zum Laden zugehöriger Entitäten verwenden.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-103">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="8dd4a-104">Es gibt drei allgemeine O/RM-Muster für das Laden zugehöriger Daten.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-104">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="8dd4a-105">**Eager Loading** bedeutet, dass die zugehörigen Daten als Teil der ersten Abfrage aus der Datenbank geladen werden.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-105">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="8dd4a-106">**Explizites Laden** bedeutet, dass die zugehörigen Daten zu einem späteren Zeitpunkt explizit aus der Datenbank geladen werden.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-106">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="8dd4a-107">**Lazy Loading** bedeutet, dass die zugehörigen Daten bei Zugriff auf die Navigationseigenschaft transparent aus der Datenbank geladen werden.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-107">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]  
> <span data-ttu-id="8dd4a-108">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="8dd4a-109">Eager Loading</span><span class="sxs-lookup"><span data-stu-id="8dd4a-109">Eager loading</span></span>

<span data-ttu-id="8dd4a-110">Sie können mit der `Include`-Methode zugehörige Daten angeben, die in den Abfrageergebnissen enthalten sein sollen.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-110">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="8dd4a-111">Im folgenden Beispiel weisen die in den Ergebnissen zurückgegebenen Blogs ihre `Posts`-Eigenschaft auf, die mit den zugehörigen Beiträgen aufgefüllt wurden.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-111">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> <span data-ttu-id="8dd4a-112">Entity Framework Core korrigiert automatisch Navigationseigenschaften zur Korrektur für alle anderen Entitäten, die zuvor in die Kontextinstanz geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-112">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="8dd4a-113">Auch wenn Sie die Daten für eine Navigationseigenschaft nicht explizit eingeschlossen haben, kann die Eigenschaft folglich immer noch aufgefüllt werden, wenn einige oder alle zugehörigen Entitäten zuvor geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-113">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="8dd4a-114">Sie können zugehörigen Daten aus mehreren Beziehungen in einer einzelnen Abfrage einschließen.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-114">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="8dd4a-115">Einschließen mehrerer Ebenen</span><span class="sxs-lookup"><span data-stu-id="8dd4a-115">Including multiple levels</span></span>

<span data-ttu-id="8dd4a-116">Sie können mit der `ThenInclude`-Methode einen Drilldown für Beziehungen ausführen, um mehrere Ebenen zugehöriger Daten einzuschließen.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-116">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="8dd4a-117">Im folgenden Beispiel werden sämtliche Blogs, die zugehörigen Beiträge und die Autoren der einzelnen Beiträge geladen.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-117">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="8dd4a-118">Sie können mehrere Aufrufe mit `ThenInclude` verbinden, um mit dem Einschließen weiterer Ebenen zugehöriger Daten fortzufahren.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-118">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="8dd4a-119">Sie können all dies kombinieren, um zugehörige Daten aus mehreren Ebenen und mehreren Stämmen in derselben Abfrage einzuschließen.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-119">You can combine all of this to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="8dd4a-120">Für eine der Entitäten, die eingeschlossen wird, sollten Sie mehrere zugehörige Entitäten einschließen.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-120">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="8dd4a-121">Beispiel: Beim Abfragen von `Blogs` schließen Sie `Posts` ein und möchten anschließend `Author` und `Tags` von `Posts` einschließen.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-121">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="8dd4a-122">Hierzu müssen Sie die einzelnen Includepfade beginnend beim Stamm angeben.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-122">To do this, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="8dd4a-123">Beispiel: `Blog -> Posts -> Author` und `Blog -> Posts -> Tags`.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-123">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="8dd4a-124">Dies bedeutet nicht, dass Sie redundante Verknüpfungen erhalten. In den meisten Fällen konsolidiert EF die Verknüpfungen beim Generieren von SQL.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-124">This does not mean you will get redundant joins; in most cases, EF will consolidate the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

> [!CAUTION]
> <span data-ttu-id="8dd4a-125">Seit Version 3.0.0 bewirkt jede `Include`, dass eine zusätzliche Verknüpfung zu von relationalen Anbietern erzeugten SQL-Abfragen hinzugefügt wird, während in früheren Versionen zusätzliche SQL-Abfragen generiert wurden.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-125">Since version 3.0.0, each `Include` will cause an additional JOIN to be added to SQL queries produced by relational providers, whereas previous versions generated additional SQL queries.</span></span> <span data-ttu-id="8dd4a-126">Dies kann die Leistung Ihrer Abfragen entweder verbessern oder verschlechtern.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-126">This can significantly change the performance of your queries, for better or worse.</span></span> <span data-ttu-id="8dd4a-127">Insbesondere müssen LINQ-Abfragen mit einer überaus hohen Anzahl an `Include`-Operatoren in mehrere einzelne LINQ-Abfragen aufgeteilt werden, um das Problem der kartesischen Explosion zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-127">In particular, LINQ queries with an exceedingly high number of `Include` operators may need to be broken down into multiple separate LINQ queries in order to avoid the cartesian explosion problem.</span></span>

### <a name="filtered-include"></a><span data-ttu-id="8dd4a-128">Gefilterte Include-Funktion</span><span class="sxs-lookup"><span data-stu-id="8dd4a-128">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="8dd4a-129">Diese Funktion wird in EF Core 5.0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-129">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="8dd4a-130">Wenn Sie „Include“ zum Laden von zugehörigen Daten anwenden, können Sie bestimmte aufzählbare Vorgänge auf die enthaltene Sammlungsnavigation anwenden, mit denen Sie die Ergebnisse filtern und sortieren können.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-130">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="8dd4a-131">Folgende Vorgänge werden unterstützt: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip` und `Take`.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-131">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="8dd4a-132">Solche Vorgänge sollten auf die Sammlungsnavigation in der Lambdafunktion angewendet werden, die an die Include-Methode übergeben wird, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="8dd4a-132">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="8dd4a-133">Jede per „Include“ eingeschlossene Navigation ermöglicht nur einen eindeutigen Satz von Filtervorgängen.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-133">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="8dd4a-134">In Fällen, in denen mehrere Include-Vorgänge für eine bestimmte Sammlungsnavigation angewendet werden (`blog.Posts` in den folgenden Beispielen), können Filtervorgänge nur für einen von ihnen angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="8dd4a-134">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span> 

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="8dd4a-135">Alternativ können identische Vorgänge für jede Navigation angewendet werden, die mehrmals per „Include“ eingeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="8dd4a-135">Alternatively, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="8dd4a-136">Bei der Nachverfolgung von Abfragen können aufgrund einer [Navigationskorrektur](tracking.md) unerwartete Ergebnisse für gefilterte Include-Abfragen auftreten.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-136">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](tracking.md).</span></span> <span data-ttu-id="8dd4a-137">Alle relevanten Entitäten, die zuvor abgefragt und in der Änderungsnachverfolgung gespeichert wurden, werden in den Ergebnissen der gefilterten Include-Abfrage angezeigt, auch wenn sie die Anforderungen des Filters nicht erfüllen.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-137">All relevant entities that have been querried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="8dd4a-138">Verwenden Sie `NoTracking`-Abfragen, oder erstellen Sie DbContext neu, wenn Sie gefilterte Include-Abfragen in diesen Situationen nutzen.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-138">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="8dd4a-139">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8dd4a-139">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rathat than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

### <a name="include-on-derived-types"></a><span data-ttu-id="8dd4a-140">Einschließen in abgeleiteten Typen</span><span class="sxs-lookup"><span data-stu-id="8dd4a-140">Include on derived types</span></span>

<span data-ttu-id="8dd4a-141">Sie können zugehörige Daten aus Navigationen einschließen, die nur in einem abgeleiteten Typ mit `Include` und `ThenInclude` definiert werden.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-141">You can include related data from navigations defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="8dd4a-142">Betrachten Sie das folgende Modell:</span><span class="sxs-lookup"><span data-stu-id="8dd4a-142">Given the following model:</span></span>

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

<span data-ttu-id="8dd4a-143">Inhalte der `School`-Navigation für alle Personen, die Studenten sind, können mit einer Reihe von Mustern vorzeitig geladen werden:</span><span class="sxs-lookup"><span data-stu-id="8dd4a-143">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="8dd4a-144">Mit cast</span><span class="sxs-lookup"><span data-stu-id="8dd4a-144">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="8dd4a-145">Mit dem Operator `as`</span><span class="sxs-lookup"><span data-stu-id="8dd4a-145">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="8dd4a-146">Mit einer Überladung von `Include`, die Parameter vom Typ `string` akzeptiert</span><span class="sxs-lookup"><span data-stu-id="8dd4a-146">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a><span data-ttu-id="8dd4a-147">Explizites Laden</span><span class="sxs-lookup"><span data-stu-id="8dd4a-147">Explicit loading</span></span>

<span data-ttu-id="8dd4a-148">Sie können eine Navigationseigenschaft über die API `DbContext.Entry(...)` explizit laden.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-148">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="8dd4a-149">Sie können eine Navigationseigenschaft auch explizit laden, indem Sie eine separate Abfrage ausführen, die die zugehörigen Entitäten zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-149">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="8dd4a-150">Wenn die Änderungsnachverfolgung aktiviert ist, legt EF Core beim Laden einer Entität die Navigationseigenschaften der neu geladenen Entität automatisch so fest, dass diese auf bereits geladene Entitäten verweisen, und die Navigationseigenschaften bereits geladener Entitäten so, dass diese auf die neu geladene Entität verweisen.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-150">If change tracking is enabled, then when loading an entity, EF Core will automatically set the navigation properties of the newly-loaded entitiy to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly-loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="8dd4a-151">Abfragen zugehöriger Entitäten</span><span class="sxs-lookup"><span data-stu-id="8dd4a-151">Querying related entities</span></span>

<span data-ttu-id="8dd4a-152">Sie können auch eine LINQ-Abfrage abrufen, welche die Inhalte einer Navigationseigenschaft darstellt.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-152">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="8dd4a-153">Dadurch können Sie Aktionen, wie z.B. das Ausführen eines Aggregatoperators, über die zugehörigen Entitäten ausführen, ohne diese in den Speicher laden zu müssen.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-153">This allows you to do things such as running an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="8dd4a-154">Darüber hinaus können Sie filtern, welche zugehörigen Entitäten in den Speicher geladen werden.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-154">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="8dd4a-155">Lazy Loading</span><span class="sxs-lookup"><span data-stu-id="8dd4a-155">Lazy loading</span></span>

<span data-ttu-id="8dd4a-156">Die einfachste Möglichkeit für die Verwendung von Lazy Loading besteht in der Installation des Pakets [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) und der Aktivierung dieses Pakets durch Aufrufen von `UseLazyLoadingProxies`.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-156">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="8dd4a-157">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8dd4a-157">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="8dd4a-158">Oder bei Verwendung von AddDbContext:</span><span class="sxs-lookup"><span data-stu-id="8dd4a-158">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="8dd4a-159">EF Core aktiviert Lazy Loading anschließend für beliebige überschreibbare Navigationseigenschaften – diese müssen `virtual` und in einer Klasse enthalten sein, aus der sie geerbt werden können.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-159">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="8dd4a-160">In den folgenden Entitäten wird beispielsweise Lazy Loading für die Navigationseigenschaften `Post.Blog` und `Blog.Posts` durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-160">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

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

### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="8dd4a-161">Lazy Loading ohne Proxys</span><span class="sxs-lookup"><span data-stu-id="8dd4a-161">Lazy loading without proxies</span></span>

<span data-ttu-id="8dd4a-162">Lazy Loading-Proxys funktionieren, indem der Dienst `ILazyLoader` in eine Entität eingefügt wird, wie unter [Entitätstypenkonstruktoren](../modeling/constructors.md) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-162">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](../modeling/constructors.md).</span></span> <span data-ttu-id="8dd4a-163">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8dd4a-163">For example:</span></span>

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

<span data-ttu-id="8dd4a-164">Es ist nicht erforderlich, dass aus Entitätstypen vererbt werden kann, oder dass Navigationseigenschaften „virtual“ sind. Mit `new` erstellte Entitätsinstanzen können Lazy Loading ausführen, sobald sie einem Kontext angefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-164">This doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="8dd4a-165">Erforderlich ist ein Verweis auf den `ILazyLoader`-Dienst, der im [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/)-Paket definiert ist.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-165">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="8dd4a-166">Dieses Paket enthält einen minimalen Satz von Typen, sodass es kaum Auswirkungen davon gibt.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-166">This package contains a minimal set of types so that there is very little impact in depending on it.</span></span> <span data-ttu-id="8dd4a-167">Um jedoch die Abhängigkeit von EF Core-Paketen in den Entitätstypen vollständig zu vermeiden, ist es möglich, die Methode `ILazyLoader.Load` als Delegat einzufügen.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-167">However, to completely avoid depending on any EF Core packages in the entity types, it is possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="8dd4a-168">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8dd4a-168">For example:</span></span>

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

<span data-ttu-id="8dd4a-169">Der oben stehende Code verwendet eine `Load`-Erweiterungsmethode, um die Verwendung des Delegaten sauberer zu gestalten:</span><span class="sxs-lookup"><span data-stu-id="8dd4a-169">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

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
> <span data-ttu-id="8dd4a-170">Der Konstruktorparameter für den Lazy Loading-Delegaten muss als „lazyLoader“ bezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-170">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="8dd4a-171">Eine Konfiguration für die Verwendung eines anderen Namens ist für ein zukünftiges Release geplant.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-171">Configuration to use a different name than this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="8dd4a-172">Zugehörige Daten und Serialisierung</span><span class="sxs-lookup"><span data-stu-id="8dd4a-172">Related data and serialization</span></span>

<span data-ttu-id="8dd4a-173">Da EF Core Navigationseigenschaften automatisch korrigiert, können sich in Ihrem Objektgraph Zyklen ergeben.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-173">Because EF Core will automatically fix-up navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="8dd4a-174">Das Laden eines Blogs und der zugehörigen Beiträge führt beispielsweise zu einem Blogobjekt, das auf eine Sammlung von Beiträgen verweist.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-174">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="8dd4a-175">Die einzelnen Beiträge verweisen wieder auf den Blog.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-175">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="8dd4a-176">Einige Serialisierungsframeworks lassen solche Zyklen nicht zu.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-176">Some serialization frameworks do not allow such cycles.</span></span> <span data-ttu-id="8dd4a-177">Json.NET löst beispielsweise folgende Ausnahme aus, wenn ein Zyklus erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-177">For example, Json.NET will throw the following exception if a cycle is encountered.</span></span>

> <span data-ttu-id="8dd4a-178">Newtonsoft.Json.JsonSerializationException: Für die Eigenschaft „Blog“ mit dem Typ „MyApplication.Models.Blog“ wurde eine auf sich selbst verweisende Schleife erkannt.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-178">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="8dd4a-179">Wenn Sie ASP.NET Core verwenden, können Sie Json.NET so konfigurieren, dass im Objektgraph gefundene Zyklen ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-179">If you are using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="8dd4a-180">Dies erfolgt in der Methode `ConfigureServices(...)` in `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-180">This is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

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

<span data-ttu-id="8dd4a-181">Eine weitere Alternative ist die Ergänzung einer der Navigationseigenschaften mit dem `[JsonIgnore]`-Attribut, das Json.NET anweist, diese Navigationseigenschaft während der Serialisierung nicht zu durchlaufen.</span><span class="sxs-lookup"><span data-stu-id="8dd4a-181">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
