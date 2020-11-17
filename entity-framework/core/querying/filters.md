---
title: 'Globale Abfragefilter: EF Core'
description: Verwenden von globalen Abfragefiltern zum Filtern von Ergebnissen mit Entity Framework Core
author: maumar
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 6436f9f8e2e09d44ef9528fd2022720d40095fe0
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430130"
---
# <a name="global-query-filters"></a><span data-ttu-id="dd687-103">Globale Abfragefilter</span><span class="sxs-lookup"><span data-stu-id="dd687-103">Global Query Filters</span></span>

<span data-ttu-id="dd687-104">Globale Abfragefilter sind LINQ-Abfrageprädikate, die auf Entitätstypen im Metadatenmodell (normalerweise in `OnModelCreating`) angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="dd687-104">Global query filters are LINQ query predicates applied to Entity Types in the metadata model (usually in `OnModelCreating`).</span></span> <span data-ttu-id="dd687-105">Bei einem Abfrageprädikat handelt es sich um einen booleschen Ausdruck, der in der Regel an den LINQ-Abfrageoperator `Where` (Wo) übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="dd687-105">A query predicate is a boolean expression typically passed to the LINQ `Where` query operator.</span></span>  <span data-ttu-id="dd687-106">EF Core wendet solche Filter automatisch auf alle LINQ-Abfragen an, die diese Entitätstypen einschließen.</span><span class="sxs-lookup"><span data-stu-id="dd687-106">EF Core applies such filters automatically to any LINQ queries involving those Entity Types.</span></span>  <span data-ttu-id="dd687-107">EF Core wendet sie auch auf Entitätstypen an, auf die indirekt durch Verwendung der Include-Eigenschaft oder Navigationseigenschaft verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="dd687-107">EF Core also applies them to Entity Types, referenced indirectly through use of Include or navigation property.</span></span> <span data-ttu-id="dd687-108">Zu den häufigsten Anwendungsfällen dieses Features zählen Folgende:</span><span class="sxs-lookup"><span data-stu-id="dd687-108">Some common applications of this feature are:</span></span>

* <span data-ttu-id="dd687-109">**Vorläufiges Löschen:** Ein Entitätstyp definiert eine `IsDeleted`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="dd687-109">**Soft delete** - An Entity Type defines an `IsDeleted` property.</span></span>
* <span data-ttu-id="dd687-110">**Mehrinstanzenfähigkeit:** Ein Entitätstyp definiert eine `TenantId`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="dd687-110">**Multi-tenancy** - An Entity Type defines a `TenantId` property.</span></span>

## <a name="example"></a><span data-ttu-id="dd687-111">Beispiel</span><span class="sxs-lookup"><span data-stu-id="dd687-111">Example</span></span>

<span data-ttu-id="dd687-112">Im folgenden Beispiel wird in einem einfachen Blogmodell dargestellt, wie globale Abfragefilter zum Implementieren des Abfrageverhaltens für das vorläufige Löschen und die Mehrinstanzenfähigkeit verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="dd687-112">The following example shows how to use Global Query Filters to implement multi-tenancy and soft-delete query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="dd687-113">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/QueryFilters) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="dd687-113">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/QueryFilters) on GitHub.</span></span>

<span data-ttu-id="dd687-114">Definieren Sie zunächst die Entitäten:</span><span class="sxs-lookup"><span data-stu-id="dd687-114">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Entities.cs#Entities)]

<span data-ttu-id="dd687-115">Beachten Sie die Deklaration eines `_tenantId`-Felds in der Entität `Blog`.</span><span class="sxs-lookup"><span data-stu-id="dd687-115">Note the declaration of a `_tenantId` field on the `Blog` entity.</span></span> <span data-ttu-id="dd687-116">Dieses Feld wird dazu verwendet, jede Bloginstanz einem bestimmten Mandanten zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="dd687-116">This field will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="dd687-117">Außerdem wird eine `IsDeleted`-Eigenschaft auf dem Entitätstyp `Post` definiert.</span><span class="sxs-lookup"><span data-stu-id="dd687-117">Also defined is an `IsDeleted` property on the `Post` entity type.</span></span> <span data-ttu-id="dd687-118">Mit dieser Eigenschaft wird nachverfolgt, ob eine Post-Instanz „vorläufig gelöscht“ wurde.</span><span class="sxs-lookup"><span data-stu-id="dd687-118">This property is used to keep track of whether a post instance has been "soft-deleted".</span></span> <span data-ttu-id="dd687-119">Das heißt, die Instanz wird als gelöscht gekennzeichnet, ohne dass zugrunde liegende Daten physisch entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="dd687-119">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="dd687-120">Konfigurieren Sie als nächstes die Abfragefilter in `OnModelCreating` mithilfe der `HasQueryFilter`-API.</span><span class="sxs-lookup"><span data-stu-id="dd687-120">Next, configure the query filters in `OnModelCreating` using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/BloggingContext.cs#FilterConfiguration)]

<span data-ttu-id="dd687-121">Die Prädikatausdrücke, die an `HasQueryFilter` weitergegeben werden, werden nun automatisch auf alle LINQ-Abfragen dieser Typen angewendet.</span><span class="sxs-lookup"><span data-stu-id="dd687-121">The predicate expressions passed to the `HasQueryFilter` calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="dd687-122">Beachten Sie die Verwendung eines DbContext-Instanzfelds: `_tenantId` wird zum Festlegen des aktuellen Mandanten verwendet.</span><span class="sxs-lookup"><span data-stu-id="dd687-122">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="dd687-123">Filter auf Modellebene verwenden den Wert der korrekten Kontextinstanz, d.h. der Instanz, die die Abfrage ausführt.</span><span class="sxs-lookup"><span data-stu-id="dd687-123">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="dd687-124">Es ist derzeit nicht möglich, mehrere Abfragefilter für dieselbe Entität zu definieren, nur der letzte wird angewendet.</span><span class="sxs-lookup"><span data-stu-id="dd687-124">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="dd687-125">Mithilfe des logischen `AND`-Operators ([`&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)) können jedoch einen einzelnen Filter mit mehreren Bedingungen definieren.</span><span class="sxs-lookup"><span data-stu-id="dd687-125">However, you can define a single filter with multiple conditions using the logical `AND` operator ([`&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="dd687-126">Verwenden von Navigationselementen</span><span class="sxs-lookup"><span data-stu-id="dd687-126">Use of navigations</span></span>

<span data-ttu-id="dd687-127">Navigationselemente können beim Definieren lokaler Abfragefilter verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="dd687-127">You can also use navigations in defining global query filters.</span></span> <span data-ttu-id="dd687-128">Das Verwenden von Navigationselementen in Abfragefiltern führt dazu, dass Abfragefilter rekursiv angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="dd687-128">Using navigations in query filter will cause query filters to be applied recursively.</span></span> <span data-ttu-id="dd687-129">Wenn EF Core die in Navigationselementen verwendeten Abfragefilter erweitert, werden auch die für die Entitäten, auf die verwiesen wird, definierten Abfragefilter angewendet.</span><span class="sxs-lookup"><span data-stu-id="dd687-129">When EF Core expands navigations used in query filters, it will also apply query filters defined on referenced entities.</span></span>

<span data-ttu-id="dd687-130">Um dies zu veranschaulichen, konfigurieren Sie Abfragefilter in `OnModelCreating` wie folgt: [!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#NavigationInFilter)]</span><span class="sxs-lookup"><span data-stu-id="dd687-130">To illustrate this configure query filters in `OnModelCreating` in the following way: [!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#NavigationInFilter)]</span></span>

<span data-ttu-id="dd687-131">Als Nächstes fragen Sie alle `Blog`-Entitäten ab: [!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#QueriesNavigation)]</span><span class="sxs-lookup"><span data-stu-id="dd687-131">Next, query for all `Blog` entities: [!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#QueriesNavigation)]</span></span>

<span data-ttu-id="dd687-132">Diese Abfrage erzeugt den folgenden SQL-Code, der Abfragefilter anwendet, die für die Entitäten `Blog` und `Post` definiert sind:</span><span class="sxs-lookup"><span data-stu-id="dd687-132">This query produces the following SQL, which applies query filters defined for both `Blog` and `Post` entities:</span></span>

```sql
SELECT [b].[BlogId], [b].[Name], [b].[Url]
FROM [Blogs] AS [b]
WHERE (
    SELECT COUNT(*)
    FROM [Posts] AS [p]
    WHERE ([p].[Title] LIKE N'%fish%') AND ([b].[BlogId] = [p].[BlogId])) > 0
```

> [!NOTE]
> <span data-ttu-id="dd687-133">Derzeit ermittelt EF Core keine Zyklen in globalen Abfragefilterdefinitionen. Daher sollten Sie bei der Definition vorsichtig vorgehen.</span><span class="sxs-lookup"><span data-stu-id="dd687-133">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="dd687-134">Wenn diese fehlerhaft angegeben wird, können Endlosschleifen bei der Abfrageübersetzung auftreten.</span><span class="sxs-lookup"><span data-stu-id="dd687-134">If specified incorrectly, cycles could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a><span data-ttu-id="dd687-135">Zugreifen auf Entitäten mit Abfragefilter mithilfe erforderlicher Navigationselemente</span><span class="sxs-lookup"><span data-stu-id="dd687-135">Accessing entity with query filter using required navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="dd687-136">Die Verwendung erforderlicher Navigationselemente für den Zugriff auf eine Entität mit einem definierten globalen Abfragefilter kann zu unerwarteten Ergebnissen führen.</span><span class="sxs-lookup"><span data-stu-id="dd687-136">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span>

<span data-ttu-id="dd687-137">Die erforderlichen Navigationselemente erwarten, dass die zugehörige Entität immer vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="dd687-137">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="dd687-138">Wenn erforderliche verwandte Entitäten vom Abfragefilter herausgefiltert werden, würde die übergeordnete Entität ebenso nicht im Ergebnis ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="dd687-138">If necessary related entity is filtered out by the query filter, the parent entity wouldn't be in result either.</span></span> <span data-ttu-id="dd687-139">Daher erhalten Sie möglicherweise weniger Elemente als erwartet.</span><span class="sxs-lookup"><span data-stu-id="dd687-139">So you may get fewer elements than expected in result.</span></span>

<span data-ttu-id="dd687-140">Zur Veranschaulichung dieses Problems können die oben angegebenen Entitäten `Blog` und `Post` mit der `OnModelCreating`-Methode verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="dd687-140">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following `OnModelCreating` method:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#IncorrectFilter)]

<span data-ttu-id="dd687-141">Das Modell kann mit den folgenden Daten eingerichtet werden:</span><span class="sxs-lookup"><span data-stu-id="dd687-141">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#SeedData)]

<span data-ttu-id="dd687-142">Das Problem tritt auf, wenn zwei Abfragen ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="dd687-142">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#Queries)]

<span data-ttu-id="dd687-143">Bei diesem Setup gibt die erste Abfrage alle sechs `Post`-Anfragen zurück. Die zweite Abfrage gibt jedoch nur drei zurück.</span><span class="sxs-lookup"><span data-stu-id="dd687-143">With above setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="dd687-144">Das liegt daran, dass die `Include`-Methode in der zweiten Abfrage die zugehörigen `Blog`-Entitäten lädt.</span><span class="sxs-lookup"><span data-stu-id="dd687-144">This mismatch happens because `Include` method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="dd687-145">Das die Navigation zwischen `Blog` und `Post` erforderlich ist, nutzt EF Core `INNER JOIN` beim Erstellen der Abfrage:</span><span class="sxs-lookup"><span data-stu-id="dd687-145">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Posts] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE [b].[Url] LIKE N'%fish%'
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="dd687-146">Bei Verwendung von `INNER JOIN` werden alle `Post`-Vorgänge herausgefiltert, deren zugehörigen `Blog`-Entitäten von einem globalen Abfragefilter entfernt wurden.</span><span class="sxs-lookup"><span data-stu-id="dd687-146">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span>

<span data-ttu-id="dd687-147">Dieses Problem können Sie mithilfe optionaler Navigationselemente anstelle erforderlicher umgehen.</span><span class="sxs-lookup"><span data-stu-id="dd687-147">It can be addressed by using optional navigation instead of required.</span></span>
<span data-ttu-id="dd687-148">Auf diese Weise bleibt die erste Abfrage unverändert, die zweite Abfrage generiert nun jedoch `LEFT JOIN` und gibt sechs Ergebnisse zurück.</span><span class="sxs-lookup"><span data-stu-id="dd687-148">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#OptionalNavigation)]

<span data-ttu-id="dd687-149">Der alternative Ansatz besteht darin, konsistente Filter für die beiden Entitäten `Blog` und `Post` festzulegen.</span><span class="sxs-lookup"><span data-stu-id="dd687-149">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="dd687-150">Auf diese Weise werden entsprechende Filter auf `Blog` und `Post` angewendet.</span><span class="sxs-lookup"><span data-stu-id="dd687-150">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="dd687-151">`Post`-Entitäten, die einen unerwarteten Status aufweisen könnten, werden entfernt und beide Abfragen geben drei Ergebnisse zurück.</span><span class="sxs-lookup"><span data-stu-id="dd687-151">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#MatchingFilters)]

## <a name="disabling-filters"></a><span data-ttu-id="dd687-152">Deaktivieren von Filtern</span><span class="sxs-lookup"><span data-stu-id="dd687-152">Disabling Filters</span></span>

<span data-ttu-id="dd687-153">Filter können für einzelne LINQ-Abfragen mit dem <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.IgnoreQueryFilters%2A>-Operator deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="dd687-153">Filters may be disabled for individual LINQ queries by using the <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.IgnoreQueryFilters%2A> operator.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="dd687-154">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="dd687-154">Limitations</span></span>

<span data-ttu-id="dd687-155">Globale Abfragefilter unterliegen den folgenden Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="dd687-155">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="dd687-156">Filter können nur für den Stammentitätstyp einer Vererbungshierarchie definiert werden.</span><span class="sxs-lookup"><span data-stu-id="dd687-156">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
