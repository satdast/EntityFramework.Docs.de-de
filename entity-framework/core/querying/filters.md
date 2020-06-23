---
title: 'Globale Abfragefilter: EF Core'
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: f6c59bcbab31edcbed22079a1320c060ce08c6f7
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664129"
---
# <a name="global-query-filters"></a><span data-ttu-id="1a9fc-102">Globale Abfragefilter</span><span class="sxs-lookup"><span data-stu-id="1a9fc-102">Global Query Filters</span></span>

> [!NOTE]
> <span data-ttu-id="1a9fc-103">Dieses Feature wurde in EF Core 2.0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-103">This feature was introduced in EF Core 2.0.</span></span>

<span data-ttu-id="1a9fc-104">Globale Abfragefilter sind LINQ-Abfrageprädikate (ein boolescher Ausdruck, der in der Regel an den LINQ-Abfrageoperator *Where* übergeben wird), die auf Entitätstypen im Metadatenmodell (normalerweise in *OnModelCreating*) angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-104">Global query filters are LINQ query predicates (a boolean expression typically passed to the LINQ *Where* query operator) applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="1a9fc-105">Solche Filter werden automatisch auf alle diese Entitätstypen betreffenden LINQ-Abfragen (z.B. indirekt referenzierte Entitätstypen) angewendet, beispielsweise durch include-Verweise oder direkte Verweise auf Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-105">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="1a9fc-106">Zu den häufigsten Anwendungsfällen dieses Features zählen Folgende:</span><span class="sxs-lookup"><span data-stu-id="1a9fc-106">Some common applications of this feature are:</span></span>

* <span data-ttu-id="1a9fc-107">**Vorläufiges Löschen:** Ein Entitätstyp definiert eine *IsDeleted*-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-107">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="1a9fc-108">**Mehrinstanzenfähigkeit:** Ein Entitätstyp definiert eine *TenantId*-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-108">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="1a9fc-109">Beispiel</span><span class="sxs-lookup"><span data-stu-id="1a9fc-109">Example</span></span>

<span data-ttu-id="1a9fc-110">Im folgenden Beispiel wird in einem einfachen Blogmodell dargestellt, wie globale Abfragefilter zum Implementieren des Abfrageverhaltens für das vorläufige Löschen und die Mehrinstanzenfähigkeit verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-110">The following example shows how to use Global Query Filters to implement soft-delete and multi-tenancy query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="1a9fc-111">Sie können sich auf GitHub ein [Beispiel mit mehreren Mandanten](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) und [Beispiele mit Navigationselementen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) ansehen.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-111">You can view a [multi-tenancy sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) and [samples using navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) on GitHub.</span></span> 

<span data-ttu-id="1a9fc-112">Definieren Sie zunächst die Entitäten:</span><span class="sxs-lookup"><span data-stu-id="1a9fc-112">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="1a9fc-113">Beachten Sie die Deklaration eines _tenantId_-Felds in der Entität _Blog_.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-113">Note the declaration of a _tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="1a9fc-114">Dies wird dazu verwendet, jede Bloginstanz einem bestimmten Mandanten zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-114">This will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="1a9fc-115">Außerdem wird eine _IsDeleted_-Eigenschaft auf dem Entitätstyp _Post_ definiert.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-115">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="1a9fc-116">Damit wird nachverfolgt, ob eine _Post_-Instanz „vorläufig gelöscht“ wurde.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-116">This is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="1a9fc-117">Das heißt, die Instanz wird als gelöscht gekennzeichnet, ohne dass zugrunde liegende Daten physisch entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-117">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="1a9fc-118">Konfigurieren Sie als nächstes die Abfragefilter in _OnModelCreating_ mithilfe der `HasQueryFilter`-API.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-118">Next, configure the query filters in _OnModelCreating_ using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="1a9fc-119">Die Prädikatausdrücke, die an _HasQueryFilter_ weitergegeben werden, werden nun automatisch auf alle LINQ-Abfragen dieser Typen angewendet.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-119">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="1a9fc-120">Beachten Sie die Verwendung eines DbContext-Instanzfelds: `_tenantId` wird zum Festlegen des aktuellen Mandanten verwendet.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-120">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="1a9fc-121">Filter auf Modellebene verwenden den Wert der korrekten Kontextinstanz, d.h. der Instanz, die die Abfrage ausführt.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-121">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="1a9fc-122">Es ist derzeit nicht möglich, mehrere Abfragefilter für dieselbe Entität zu definieren, nur der letzte wird angewendet.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-122">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="1a9fc-123">Mithilfe des logischen _AND_-Operators ([`&&` in C#](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)) können jedoch einen einzelnen Filter mit mehreren Bedingungen definieren.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-123">However, you can define a single filter with multiple conditions using the logical _AND_ operator ([`&&` in C#](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="1a9fc-124">Verwenden von Navigationselementen</span><span class="sxs-lookup"><span data-stu-id="1a9fc-124">Use of navigations</span></span>

<span data-ttu-id="1a9fc-125">Navigationselemente können beim Definieren lokaler Abfragefilter verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-125">Navigations can be used when defining global query filters.</span></span> <span data-ttu-id="1a9fc-126">Diese werden rekursiv angewendet: Wenn Navigationselemente in Abfragefiltern übersetzt werden, werden die in referenzierten Entitäten definierten Abfragefilter ebenfalls angewendet, wodurch möglicherweise weitere Navigationselemente hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-126">They are applied recursively - when navigations used in query filters are translated, query filters defined on referenced entities are also applied, potentially adding more navigations.</span></span>

> [!NOTE]
> <span data-ttu-id="1a9fc-127">Derzeit ermittelt EF Core keine Zyklen in globalen Abfragefilterdefinitionen. Daher sollten Sie bei der Definition vorsichtig vorgehen.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-127">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="1a9fc-128">Wenn diese fehlerhaft angegeben wird, können Endlosschleifen bei der Abfrageübersetzung auftreten.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-128">If specified incorrectly, this could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-reqiured-navigation"></a><span data-ttu-id="1a9fc-129">Zugreifen auf Entitäten mit Abfragefilter mithilfe erforderlicher Navigationselemente</span><span class="sxs-lookup"><span data-stu-id="1a9fc-129">Accessing entity with query filter using reqiured navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="1a9fc-130">Die Verwendung erforderlicher Navigationselemente für den Zugriff auf eine Entität mit einem definierten globalen Abfragefilter kann zu unerwarteten Ergebnissen führen.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-130">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span> 

<span data-ttu-id="1a9fc-131">Die erforderlichen Navigationselemente erwarten, dass die zugehörige Entität immer vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-131">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="1a9fc-132">Wenn erforderliche verwandte Entitäten vom Abfragefilter herausgefiltert werden, könnte die übergeordnete Entität einen unerwarteten Status aufweisen.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-132">If required related entity is filtered out by the query filter, the parent entity could end up in unexpected state.</span></span> <span data-ttu-id="1a9fc-133">Dies kann dazu führen, dass weniger Elemente als erwartet zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-133">This may result in returning fewer elements than expected.</span></span> 

<span data-ttu-id="1a9fc-134">Zur Veranschaulichung dieses Problems können die oben angegebenen Entitäten `Blog` und `Post` mit der _OnModelCreating_-Methode verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="1a9fc-134">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following _OnModelCreating_ method:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="1a9fc-135">Das Modell kann mit den folgenden Daten eingerichtet werden:</span><span class="sxs-lookup"><span data-stu-id="1a9fc-135">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

<span data-ttu-id="1a9fc-136">Das Problem tritt auf, wenn zwei Abfragen ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="1a9fc-136">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

<span data-ttu-id="1a9fc-137">Bei diesem Setup gibt die erste Abfrage alle sechs `Post`-Anfragen zurück. Die zweite Abfrage gibt jedoch nur drei zurück.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-137">With this setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="1a9fc-138">Das liegt daran, dass die _Include_-Methode in der zweiten Abfrage die zugehörigen `Blog`-Entitäten lädt.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-138">This happens because _Include_ method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="1a9fc-139">Das die Navigation zwischen `Blog` und `Post` erforderlich ist, nutzt EF Core `INNER JOIN` beim Erstellen der Abfrage:</span><span class="sxs-lookup"><span data-stu-id="1a9fc-139">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="1a9fc-140">Bei Verwendung von `INNER JOIN` werden alle `Post`-Vorgänge herausgefiltert, deren zugehörigen `Blog`-Entitäten von einem globalen Abfragefilter entfernt wurden.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-140">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span> 

<span data-ttu-id="1a9fc-141">Dieses Problem können Sie mithilfe optionaler Navigationselemente anstelle erforderlicher umgehen.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-141">It can be addressed by using optional navigation instead of required.</span></span> <span data-ttu-id="1a9fc-142">Auf diese Weise bleibt die erste Abfrage unverändert, die zweite Abfrage generiert nun jedoch `LEFT JOIN` und gibt sechs Ergebnisse zurück.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-142">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="1a9fc-143">Der alternative Ansatz besteht darin, konsistente Filter für die beiden Entitäten `Blog` und `Post` festzulegen.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-143">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="1a9fc-144">Auf diese Weise werden entsprechende Filter auf `Blog` und `Post` angewendet.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-144">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="1a9fc-145">`Post`-Entitäten, die einen unerwarteten Status aufweisen könnten, werden entfernt und beide Abfragen geben drei Ergebnisse zurück.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-145">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span> 

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a><span data-ttu-id="1a9fc-146">Deaktivieren von Filtern</span><span class="sxs-lookup"><span data-stu-id="1a9fc-146">Disabling Filters</span></span>

<span data-ttu-id="1a9fc-147">Filter können für einzelne LINQ-Abfragen mit dem `IgnoreQueryFilters()`-Operator deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-147">Filters may be disabled for individual LINQ queries by using the `IgnoreQueryFilters()` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="1a9fc-148">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="1a9fc-148">Limitations</span></span>

<span data-ttu-id="1a9fc-149">Globale Abfragefilter unterliegen den folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="1a9fc-149">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="1a9fc-150">Filter können nur für den Stammentitätstyp einer Vererbungshierarchie definiert werden.</span><span class="sxs-lookup"><span data-stu-id="1a9fc-150">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
