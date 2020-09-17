---
title: Abfragetags – EF Core
description: Verwenden von Abfragetags zum Identifizieren bestimmter Abfragen in von Entity Framework Core ausgegebenen Protokollmeldungen
author: divega
ms.date: 11/14/2018
uid: core/querying/tags
ms.openlocfilehash: 27f757f4159a36bec324cce56d74b7860e1c3741
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070990"
---
# <a name="query-tags"></a><span data-ttu-id="24bec-103">Abfragetags</span><span class="sxs-lookup"><span data-stu-id="24bec-103">Query tags</span></span>

> [!NOTE]
> <span data-ttu-id="24bec-104">Dieses Feature ist neu in EF Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="24bec-104">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="24bec-105">Diese Funktion ermöglicht das Korrelieren zwischen LINQ-Abfragen im Code und generierten SQL-Abfragen, die in Protokollen erfasst werden.</span><span class="sxs-lookup"><span data-stu-id="24bec-105">This feature helps correlate LINQ queries in code with generated SQL queries captured in logs.</span></span>
<span data-ttu-id="24bec-106">Sie können eine LINQ-Abfrage mithilfe der neuen `TagWith()`-Methode kommentieren:</span><span class="sxs-lookup"><span data-stu-id="24bec-106">You annotate a LINQ query using the new `TagWith()` method:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith("This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="24bec-107">Diese LINQ-Abfrage wird in die folgende SQL-Anweisung übersetzt:</span><span class="sxs-lookup"><span data-stu-id="24bec-107">This LINQ query is translated to the following SQL statement:</span></span>

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="24bec-108">`TagWith()` kann mehrmals in der gleichen Abfrage aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="24bec-108">It's possible to call `TagWith()` many times on the same query.</span></span>
<span data-ttu-id="24bec-109">Abfragetags sind kumulativ.</span><span class="sxs-lookup"><span data-stu-id="24bec-109">Query tags are cumulative.</span></span>
<span data-ttu-id="24bec-110">Betrachten Sie beispielsweise die folgenden Methoden:</span><span class="sxs-lookup"><span data-stu-id="24bec-110">For example, given the following methods:</span></span>

``` csharp
IQueryable<Friend> GetNearestFriends(Point myLocation) =>
    from f in context.Friends.TagWith("GetNearestFriends")
    orderby f.Location.Distance(myLocation) descending
    select f;

IQueryable<T> Limit<T>(IQueryable<T> source, int limit) =>
    source.TagWith("Limit").Take(limit);
```

<span data-ttu-id="24bec-111">Diese Abfrage</span><span class="sxs-lookup"><span data-stu-id="24bec-111">The following query:</span></span>

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).ToList();
```

<span data-ttu-id="24bec-112">wird übersetzt in</span><span class="sxs-lookup"><span data-stu-id="24bec-112">Translates to:</span></span>

``` sql
-- GetNearestFriends

-- Limit

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="24bec-113">Es ist auch möglich, Multi-Linienzeichenfolgen als Abfragetags zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="24bec-113">It's also possible to use multi-line strings as query tags.</span></span>
<span data-ttu-id="24bec-114">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="24bec-114">For example:</span></span>

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).TagWith(
@"This is a multi-line
string").ToList();
```

<span data-ttu-id="24bec-115">Dies generiert diese SQL-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="24bec-115">Produces the following SQL:</span></span>

``` sql
-- GetNearestFriends

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a><span data-ttu-id="24bec-116">Bekannte Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="24bec-116">Known limitations</span></span>

<span data-ttu-id="24bec-117">**Abfragetags lassen sich nicht parametrisieren.** EF Core behandelt Abfragetags in LINQ-Abfragen immer als Zeichenfolgenliterale, die in der generierten SQL-Anweisung enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="24bec-117">**Query tags aren't parameterizable:** EF Core always treats query tags in the LINQ query as string literals that are included in the generated SQL.</span></span>
<span data-ttu-id="24bec-118">Kompilierte Abfragen, die Abfragetags als Parameter verwenden, sind nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="24bec-118">Compiled queries that take query tags as parameters aren't allowed.</span></span>
