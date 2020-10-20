---
title: Abfragetags – EF Core
description: Verwenden von Abfragetags zum Identifizieren bestimmter Abfragen in von Entity Framework Core ausgegebenen Protokollmeldungen
author: smitpatel
ms.date: 11/14/2018
uid: core/querying/tags
ms.openlocfilehash: f7cd3558682b1c19e03fc6d04957c7112e870734
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065731"
---
# <a name="query-tags"></a><span data-ttu-id="aa1f3-103">Abfragetags</span><span class="sxs-lookup"><span data-stu-id="aa1f3-103">Query tags</span></span>

<span data-ttu-id="aa1f3-104">Abfragetags ermöglichen das Korrelieren zwischen LINQ-Abfragen im Code und generierten SQL-Abfragen, die in Protokollen erfasst werden.</span><span class="sxs-lookup"><span data-stu-id="aa1f3-104">Query tags help correlate LINQ queries in code with generated SQL queries captured in logs.</span></span>
<span data-ttu-id="aa1f3-105">Sie können eine LINQ-Abfrage mithilfe der neuen `TagWith()`-Methode kommentieren:</span><span class="sxs-lookup"><span data-stu-id="aa1f3-105">You annotate a LINQ query using the new `TagWith()` method:</span></span>

> [!TIP]
> <span data-ttu-id="aa1f3-106">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="aa1f3-106">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags) on GitHub.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

<span data-ttu-id="aa1f3-107">Diese LINQ-Abfrage wird in die folgende SQL-Anweisung übersetzt:</span><span class="sxs-lookup"><span data-stu-id="aa1f3-107">This LINQ query is translated to the following SQL statement:</span></span>

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="aa1f3-108">`TagWith()` kann mehrmals in der gleichen Abfrage aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="aa1f3-108">It's possible to call `TagWith()` many times on the same query.</span></span>
<span data-ttu-id="aa1f3-109">Abfragetags sind kumulativ.</span><span class="sxs-lookup"><span data-stu-id="aa1f3-109">Query tags are cumulative.</span></span>
<span data-ttu-id="aa1f3-110">Betrachten Sie beispielsweise die folgenden Methoden:</span><span class="sxs-lookup"><span data-stu-id="aa1f3-110">For example, given the following methods:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#QueryableMethods)]

<span data-ttu-id="aa1f3-111">Diese Abfrage</span><span class="sxs-lookup"><span data-stu-id="aa1f3-111">The following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#ChainedQueryTags)]

<span data-ttu-id="aa1f3-112">wird übersetzt in</span><span class="sxs-lookup"><span data-stu-id="aa1f3-112">Translates to:</span></span>

```sql
-- GetNearestPeople

-- Limit

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="aa1f3-113">Es ist auch möglich, Multi-Linienzeichenfolgen als Abfragetags zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="aa1f3-113">It's also possible to use multi-line strings as query tags.</span></span>
<span data-ttu-id="aa1f3-114">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="aa1f3-114">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#MultilineQueryTag)]

<span data-ttu-id="aa1f3-115">Dies generiert diese SQL-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="aa1f3-115">Produces the following SQL:</span></span>

```sql
-- GetNearestPeople

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a><span data-ttu-id="aa1f3-116">Bekannte Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="aa1f3-116">Known limitations</span></span>

<span data-ttu-id="aa1f3-117">**Abfragetags lassen sich nicht parametrisieren.** EF Core behandelt Abfragetags in LINQ-Abfragen immer als Zeichenfolgenliterale, die in der generierten SQL-Anweisung enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="aa1f3-117">**Query tags aren't parameterizable:** EF Core always treats query tags in the LINQ query as string literals that are included in the generated SQL.</span></span>
<span data-ttu-id="aa1f3-118">Kompilierte Abfragen, die Abfragetags als Parameter verwenden, sind nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="aa1f3-118">Compiled queries that take query tags as parameters aren't allowed.</span></span>
