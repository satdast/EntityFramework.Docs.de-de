---
title: 'Globale Abfragefilter: EF Core'
description: Verwenden von globalen Abfragefiltern zum Filtern von Ergebnissen mit Entity Framework Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: d5793760ea2e61111416284db8d5a8102dd51a41
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616385"
---
# <a name="global-query-filters"></a><span data-ttu-id="8673a-103">Globale Abfragefilter</span><span class="sxs-lookup"><span data-stu-id="8673a-103">Global Query Filters</span></span>

> [!NOTE]
> <span data-ttu-id="8673a-104">Dieses Feature wurde in EF Core 2.0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="8673a-104">This feature was introduced in EF Core 2.0.</span></span>

<span data-ttu-id="8673a-105">Globale Abfragefilter sind LINQ-Abfrageprädikate, die auf Entitätstypen im Metadatenmodell (normalerweise in *OnModelCreating*) angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="8673a-105">Global query filters are LINQ query predicates applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="8673a-106">Bei einem Abfrageprädikat handelt es sich um einen booleschen Ausdruck, der in der Regel an den LINQ-Abfrageoperator *, Where* (Wo) übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="8673a-106">A query predicate is a boolean expression typically passed to the LINQ *Where* query operator.</span></span>  <span data-ttu-id="8673a-107">EF Core wendet solche Filter automatisch auf alle LINQ-Abfragen an, die diese Entitätstypen einschließen.</span><span class="sxs-lookup"><span data-stu-id="8673a-107">EF Core applies such filters automatically to any LINQ queries involving those Entity Types.</span></span>  <span data-ttu-id="8673a-108">EF Core wendet sie auch auf Entitätstypen an, auf die indirekt durch Verwendung der Include-Eigenschaft oder Navigationseigenschaft verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="8673a-108">EF Core also applies them to Entity Types, referenced indirectly through use of Include or navigation property.</span></span> <span data-ttu-id="8673a-109">Zu den häufigsten Anwendungsfällen dieses Features zählen Folgende:</span><span class="sxs-lookup"><span data-stu-id="8673a-109">Some common applications of this feature are:</span></span>

* <span data-ttu-id="8673a-110">**Vorläufiges Löschen:** Ein Entitätstyp definiert eine *IsDeleted*-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="8673a-110">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="8673a-111">**Mehrinstanzenfähigkeit:** Ein Entitätstyp definiert eine *TenantId*-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="8673a-111">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="8673a-112">Beispiel</span><span class="sxs-lookup"><span data-stu-id="8673a-112">Example</span></span>

<span data-ttu-id="8673a-113">Im folgenden Beispiel wird in einem einfachen Blogmodell dargestellt, wie globale Abfragefilter zum Implementieren des Abfrageverhaltens für das vorläufige Löschen und die Mehrinstanzenfähigkeit verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8673a-113">The following example shows how to use Global Query Filters to implement multi-tenancy and soft-delete query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="8673a-114">Sie können sich auf GitHub ein [Beispiel mit mehreren Mandanten](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) und [Beispiele mit Navigationselementen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) ansehen.</span><span class="sxs-lookup"><span data-stu-id="8673a-114">You can view a [multi-tenancy sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) and [samples using navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) on GitHub.</span></span>

<span data-ttu-id="8673a-115">Definieren Sie zunächst die Entitäten:</span><span class="sxs-lookup"><span data-stu-id="8673a-115">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="8673a-116">Beachten Sie die Deklaration eines _tenantId_-Felds in der Entität _Blog_.</span><span class="sxs-lookup"><span data-stu-id="8673a-116">Note the declaration of a _tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="8673a-117">Dieses Feld wird dazu verwendet, jede Bloginstanz einem bestimmten Mandanten zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="8673a-117">This field will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="8673a-118">Außerdem wird eine _IsDeleted_-Eigenschaft auf dem Entitätstyp _Post_ definiert.</span><span class="sxs-lookup"><span data-stu-id="8673a-118">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="8673a-119">Mit dieser Eigenschaft wird nachverfolgt, ob eine _Post_-Instanz „vorläufig gelöscht“ wurde.</span><span class="sxs-lookup"><span data-stu-id="8673a-119">This property is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="8673a-120">Das heißt, die Instanz wird als gelöscht gekennzeichnet, ohne dass zugrunde liegende Daten physisch entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="8673a-120">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="8673a-121">Konfigurieren Sie als nächstes die Abfragefilter in _OnModelCreating_ mithilfe der `HasQueryFilter`-API.</span><span class="sxs-lookup"><span data-stu-id="8673a-121">Next, configure the query filters in _OnModelCreating_ using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="8673a-122">Die Prädikatausdrücke, die an _HasQueryFilter_ weitergegeben werden, werden nun automatisch auf alle LINQ-Abfragen dieser Typen angewendet.</span><span class="sxs-lookup"><span data-stu-id="8673a-122">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="8673a-123">Beachten Sie die Verwendung eines DbContext-Instanzfelds: `_tenantId` wird zum Festlegen des aktuellen Mandanten verwendet.</span><span class="sxs-lookup"><span data-stu-id="8673a-123">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="8673a-124">Filter auf Modellebene verwenden den Wert der korrekten Kontextinstanz, d.h. der Instanz, die die Abfrage ausführt.</span><span class="sxs-lookup"><span data-stu-id="8673a-124">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="8673a-125">Es ist derzeit nicht möglich, mehrere Abfragefilter für dieselbe Entität zu definieren, nur der letzte wird angewendet.</span><span class="sxs-lookup"><span data-stu-id="8673a-125">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="8673a-126">Mithilfe des logischen _AND_-Operators ([`&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)) können jedoch einen einzelnen Filter mit mehreren Bedingungen definieren.</span><span class="sxs-lookup"><span data-stu-id="8673a-126">However, you can define a single filter with multiple conditions using the logical _AND_ operator ([`&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="8673a-127">Verwenden von Navigationselementen</span><span class="sxs-lookup"><span data-stu-id="8673a-127">Use of navigations</span></span>

<span data-ttu-id="8673a-128">Navigationselemente können beim Definieren lokaler Abfragefilter verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8673a-128">You can also use navigations in defining global query filters.</span></span> <span data-ttu-id="8673a-129">Das Verwenden von Navigationselementen in Abfragefiltern führt dazu, dass Abfragefilter rekursiv angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="8673a-129">Using navigations in query filter will cause query filters to be applied recursively.</span></span> <span data-ttu-id="8673a-130">Wenn EF Core die in Navigationselementen verwendeten Abfragefilter erweitert, werden auch die für die Entitäten, auf die verwiesen wird, definierten Abfragefilter angewendet.</span><span class="sxs-lookup"><span data-stu-id="8673a-130">When EF Core expands navigations used in query filters, it will also apply query filters defined on referenced entities.</span></span>

> [!NOTE]
> <span data-ttu-id="8673a-131">Derzeit ermittelt EF Core keine Zyklen in globalen Abfragefilterdefinitionen. Daher sollten Sie bei der Definition vorsichtig vorgehen.</span><span class="sxs-lookup"><span data-stu-id="8673a-131">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="8673a-132">Wenn diese fehlerhaft angegeben wird, können Endlosschleifen bei der Abfrageübersetzung auftreten.</span><span class="sxs-lookup"><span data-stu-id="8673a-132">If specified incorrectly, cycles could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a><span data-ttu-id="8673a-133">Zugreifen auf Entitäten mit Abfragefilter mithilfe erforderlicher Navigationselemente</span><span class="sxs-lookup"><span data-stu-id="8673a-133">Accessing entity with query filter using required navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="8673a-134">Die Verwendung erforderlicher Navigationselemente für den Zugriff auf eine Entität mit einem definierten globalen Abfragefilter kann zu unerwarteten Ergebnissen führen.</span><span class="sxs-lookup"><span data-stu-id="8673a-134">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span>

<span data-ttu-id="8673a-135">Die erforderlichen Navigationselemente erwarten, dass die zugehörige Entität immer vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="8673a-135">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="8673a-136">Wenn erforderliche verwandte Entitäten vom Abfragefilter herausgefiltert werden, würde die übergeordnete Entität ebenso nicht im Ergebnis ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="8673a-136">If necessary related entity is filtered out by the query filter, the parent entity wouldn't be in result either.</span></span> <span data-ttu-id="8673a-137">Daher erhalten Sie möglicherweise weniger Elemente als erwartet.</span><span class="sxs-lookup"><span data-stu-id="8673a-137">So you may get fewer elements than expected in result.</span></span>

<span data-ttu-id="8673a-138">Zur Veranschaulichung dieses Problems können die oben angegebenen Entitäten `Blog` und `Post` mit der _OnModelCreating_-Methode verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="8673a-138">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following _OnModelCreating_ method:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="8673a-139">Das Modell kann mit den folgenden Daten eingerichtet werden:</span><span class="sxs-lookup"><span data-stu-id="8673a-139">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

<span data-ttu-id="8673a-140">Das Problem tritt auf, wenn zwei Abfragen ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="8673a-140">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

<span data-ttu-id="8673a-141">Bei diesem Setup gibt die erste Abfrage alle sechs `Post`-Anfragen zurück. Die zweite Abfrage gibt jedoch nur drei zurück.</span><span class="sxs-lookup"><span data-stu-id="8673a-141">With above setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="8673a-142">Das liegt daran, dass die _Include_-Methode in der zweiten Abfrage die zugehörigen `Blog`-Entitäten lädt.</span><span class="sxs-lookup"><span data-stu-id="8673a-142">This mismatch happens because _Include_ method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="8673a-143">Das die Navigation zwischen `Blog` und `Post` erforderlich ist, nutzt EF Core `INNER JOIN` beim Erstellen der Abfrage:</span><span class="sxs-lookup"><span data-stu-id="8673a-143">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="8673a-144">Bei Verwendung von `INNER JOIN` werden alle `Post`-Vorgänge herausgefiltert, deren zugehörigen `Blog`-Entitäten von einem globalen Abfragefilter entfernt wurden.</span><span class="sxs-lookup"><span data-stu-id="8673a-144">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span>

<span data-ttu-id="8673a-145">Dieses Problem können Sie mithilfe optionaler Navigationselemente anstelle erforderlicher umgehen.</span><span class="sxs-lookup"><span data-stu-id="8673a-145">It can be addressed by using optional navigation instead of required.</span></span>
<span data-ttu-id="8673a-146">Auf diese Weise bleibt die erste Abfrage unverändert, die zweite Abfrage generiert nun jedoch `LEFT JOIN` und gibt sechs Ergebnisse zurück.</span><span class="sxs-lookup"><span data-stu-id="8673a-146">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="8673a-147">Der alternative Ansatz besteht darin, konsistente Filter für die beiden Entitäten `Blog` und `Post` festzulegen.</span><span class="sxs-lookup"><span data-stu-id="8673a-147">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="8673a-148">Auf diese Weise werden entsprechende Filter auf `Blog` und `Post` angewendet.</span><span class="sxs-lookup"><span data-stu-id="8673a-148">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="8673a-149">`Post`-Entitäten, die einen unerwarteten Status aufweisen könnten, werden entfernt und beide Abfragen geben drei Ergebnisse zurück.</span><span class="sxs-lookup"><span data-stu-id="8673a-149">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a><span data-ttu-id="8673a-150">Deaktivieren von Filtern</span><span class="sxs-lookup"><span data-stu-id="8673a-150">Disabling Filters</span></span>

<span data-ttu-id="8673a-151">Filter können für einzelne LINQ-Abfragen mit dem `IgnoreQueryFilters()`-Operator deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="8673a-151">Filters may be disabled for individual LINQ queries by using the `IgnoreQueryFilters()` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="8673a-152">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="8673a-152">Limitations</span></span>

<span data-ttu-id="8673a-153">Globale Abfragefilter unterliegen den folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="8673a-153">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="8673a-154">Filter können nur für den Stammentitätstyp einer Vererbungshierarchie definiert werden.</span><span class="sxs-lookup"><span data-stu-id="8673a-154">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
