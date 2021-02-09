---
title: Vergleiche mit NULL-Werten in Abfragen
description: Hier erfahren Sie, wie Entity Framework Core NULL-Vergleiche in Abfragen verarbeitet.
author: maumar
ms.date: 11/11/2020
uid: core/querying/null-comparisons
ms.openlocfilehash: fc63d0e0e6aea09e46b1700152312d4b74270219
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983351"
---
# <a name="query-null-semantics"></a><span data-ttu-id="e29c3-103">NULL-Semantik in Abfragen</span><span class="sxs-lookup"><span data-stu-id="e29c3-103">Query null semantics</span></span>

## <a name="introduction"></a><span data-ttu-id="e29c3-104">Einführung</span><span class="sxs-lookup"><span data-stu-id="e29c3-104">Introduction</span></span>

<span data-ttu-id="e29c3-105">Im Gegensatz zur booleschen Logik von C# arbeiten SQL-Datenbanken bei Vergleichen mit einer dreiwertigen Logik (`true`, `false`, `null`).</span><span class="sxs-lookup"><span data-stu-id="e29c3-105">SQL databases operate on 3-valued logic (`true`, `false`, `null`) when performing comparisons, as opposed to the boolean logic of C#.</span></span> <span data-ttu-id="e29c3-106">Beim Übersetzen von LINQ-Abfragen in SQL versucht EF Core, den Unterschied zu kompensieren, indem zusätzliche NULL-Überprüfungen für einige Elemente der Abfrage eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e29c3-106">When translating LINQ queries to SQL, EF Core tries to compensate for the difference by introducing additional null checks for some elements of the query.</span></span>
<span data-ttu-id="e29c3-107">Zur Veranschaulichung wird die folgende Entität definiert:</span><span class="sxs-lookup"><span data-stu-id="e29c3-107">To illustrate this, let's define the following entity:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsEntity.cs#Entity)]

<span data-ttu-id="e29c3-108">Außerdem werden mehrere Abfragen ausgegeben:</span><span class="sxs-lookup"><span data-stu-id="e29c3-108">and issue several queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#BasicExamples)]

<span data-ttu-id="e29c3-109">Die ersten beiden Abfragen erzeugen einfache Vergleiche.</span><span class="sxs-lookup"><span data-stu-id="e29c3-109">The first two queries produce simple comparisons.</span></span> <span data-ttu-id="e29c3-110">In der ersten Abfrage sind beide Spalten Non-Nullable, sodass keine NULL-Überprüfungen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="e29c3-110">In the first query, both columns are non-nullable so null checks are not needed.</span></span> <span data-ttu-id="e29c3-111">In der zweiten Abfrage kann `NullableInt` den Wert `null` enthalten. `Id` ist jedoch Non-Nullable. Ein Vergleich von `null` mit Nicht-NULL ergibt `null`, wobei das Ergebnis durch den `WHERE`-Vorgang herausgefiltert wird.</span><span class="sxs-lookup"><span data-stu-id="e29c3-111">In the second query, `NullableInt` could contain `null`, but `Id` is non-nullable; comparing `null` to non-null yields `null` as a result, which would be filtered out by `WHERE` operation.</span></span> <span data-ttu-id="e29c3-112">Es sind also auch keine zusätzlichen Bedingungen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="e29c3-112">So no additional terms are needed either.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[Int]

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[NullableInt]
```

<span data-ttu-id="e29c3-113">Die dritte Abfrage führt eine NULL-Überprüfung ein.</span><span class="sxs-lookup"><span data-stu-id="e29c3-113">The third query introduces a null check.</span></span> <span data-ttu-id="e29c3-114">Wenn `NullableInt` `null` entspricht, ergibt der Vergleich `Id <> NullableInt` `null`, wobei das Ergebnis durch den `WHERE`-Vorgang herausgefiltert wird.</span><span class="sxs-lookup"><span data-stu-id="e29c3-114">When `NullableInt` is `null` the comparison `Id <> NullableInt` yields `null`, which would be filtered out by `WHERE` operation.</span></span> <span data-ttu-id="e29c3-115">Aus der Perspektive der booleschen Logik sollte dieser Fall jedoch als Teil des Ergebnisses zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e29c3-115">However, from the boolean logic perspective this case should be returned as part of the result.</span></span> <span data-ttu-id="e29c3-116">Daher wird in EF Core die erforderliche Überprüfung hinzugefügt, um dies sicherzustellen.</span><span class="sxs-lookup"><span data-stu-id="e29c3-116">Hence EF Core adds the necessary check to ensure that.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[Id] <> [e].[NullableInt]) OR [e].[NullableInt] IS NULL
```

<span data-ttu-id="e29c3-117">Die vierte und fünfte Abfrage weisen ein Muster auf, in dem beide Spalten Nullwerte zulassen.</span><span class="sxs-lookup"><span data-stu-id="e29c3-117">Queries four and five show the pattern when both columns are nullable.</span></span> <span data-ttu-id="e29c3-118">Beachten Sie, dass der `<>`-Vorgang eine kompliziertere (und potenziell langsamere) Abfrage erzeugt als der `==`-Vorgang.</span><span class="sxs-lookup"><span data-stu-id="e29c3-118">It's worth noting that the `<>` operation produces more complicated (and potentially slower) query than the `==` operation.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] = [e].[String2]) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL)

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE (([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)
```

## <a name="treatment-of-nullable-values-in-functions"></a><span data-ttu-id="e29c3-119">Verarbeitung von Nullwerte zulassenden Variablen in Funktionen</span><span class="sxs-lookup"><span data-stu-id="e29c3-119">Treatment of nullable values in functions</span></span>

<span data-ttu-id="e29c3-120">Viele Funktionen in SQL können nur dann ein `null`-Ergebnis zurückgeben, wenn einige ihrer Argumente `null` sind.</span><span class="sxs-lookup"><span data-stu-id="e29c3-120">Many functions in SQL can only return a `null` result if some of their arguments are `null`.</span></span> <span data-ttu-id="e29c3-121">EF Core nutzt dies, um effizientere Abfragen zu erzeugen.</span><span class="sxs-lookup"><span data-stu-id="e29c3-121">EF Core takes advantage of this to produce more efficient queries.</span></span>
<span data-ttu-id="e29c3-122">Die folgende Abfrage veranschaulicht die Optimierung:</span><span class="sxs-lookup"><span data-stu-id="e29c3-122">The query below illustrates the optimization:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#Functions)]

<span data-ttu-id="e29c3-123">Der generierte SQL-Code lautet wie folgt (die `SUBSTRING`-Funktion muss nicht ausgewertet werden, da sie nur NULL ist, wenn eines ihrer Argumente NULL lautet):</span><span class="sxs-lookup"><span data-stu-id="e29c3-123">The generated SQL is as follows (we don't need to evaluate the `SUBSTRING` function since it will be only null when either of the arguments to it is null.):</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[String1] IS NULL OR [e].[String2] IS NULL
```

<span data-ttu-id="e29c3-124">Die Optimierung kann auch für benutzerdefinierte Funktionen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e29c3-124">The optimization can also be used for user-defined functions.</span></span> <span data-ttu-id="e29c3-125">Weitere Informationen finden Sie unter [Zuordnung von benutzerdefinierten Funktionen](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments).</span><span class="sxs-lookup"><span data-stu-id="e29c3-125">See [user defined function mapping](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments) page for more details.</span></span>

## <a name="writing-performant-queries"></a><span data-ttu-id="e29c3-126">Schreiben von leistungsstarken Abfragen</span><span class="sxs-lookup"><span data-stu-id="e29c3-126">Writing performant queries</span></span>

- <span data-ttu-id="e29c3-127">Das Vergleichen von Non-Nullable-Spalten ist einfacher und schneller als das Vergleichen von Nullwerte zulassenden Spalten.</span><span class="sxs-lookup"><span data-stu-id="e29c3-127">Comparing non-nullable columns is simpler and faster than comparing nullable columns.</span></span> <span data-ttu-id="e29c3-128">Markieren Sie Spalten, wenn möglich, immer als Non-Nullable.</span><span class="sxs-lookup"><span data-stu-id="e29c3-128">Consider marking columns as non-nullable whenever possible.</span></span>

- <span data-ttu-id="e29c3-129">Die Überprüfung auf Gleichheit (`==`) ist einfacher und schneller als die Überprüfung auf Ungleichheit (`!=`), da die Abfrage nicht zwischen `null`- und `false`-Ergebnissen unterscheiden muss.</span><span class="sxs-lookup"><span data-stu-id="e29c3-129">Checking for equality (`==`) is simpler and faster than checking for non-equality (`!=`), because query doesn't need to distinguish between `null` and `false` result.</span></span> <span data-ttu-id="e29c3-130">Verwenden Sie nach Möglichkeit den Gleichheitsvergleich.</span><span class="sxs-lookup"><span data-stu-id="e29c3-130">Use equality comparison whenever possible.</span></span> <span data-ttu-id="e29c3-131">Den Vergleich `==` einfach zu negieren ist jedoch identisch mit `!=` und führt nicht zu einer Leistungsverbesserung.</span><span class="sxs-lookup"><span data-stu-id="e29c3-131">However, simply negating `==` comparison is effectively the same as `!=`, so it doesn't result in performance improvement.</span></span>

- <span data-ttu-id="e29c3-132">In einigen Fällen ist es möglich, einen komplexen Vergleich zu vereinfachen, indem `null`-Werte explizit aus einer Spalte herausgefiltert werden, z. B. wenn keine `null`-Werte vorhanden oder diese im Ergebnis nicht relevant sind.</span><span class="sxs-lookup"><span data-stu-id="e29c3-132">In some cases, it is possible to simplify a complex comparison by filtering out `null` values from a column explicitly - for example when no `null` values are present or these values are not relevant in the result.</span></span> <span data-ttu-id="e29c3-133">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e29c3-133">Consider the following example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#ManualOptimization)]

<span data-ttu-id="e29c3-134">Diese Abfragen erzeugen den folgenden SQL-Code:</span><span class="sxs-lookup"><span data-stu-id="e29c3-134">These queries produce the following SQL:</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ((([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)) OR ((CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL))

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] IS NOT NULL AND [e].[String2] IS NOT NULL) AND (([e].[String1] <> [e].[String2]) OR (CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)))
```

<span data-ttu-id="e29c3-135">In der zweiten Abfrage werden `null`-Ergebnisse explizit aus der Spalte `String1` herausgefiltert.</span><span class="sxs-lookup"><span data-stu-id="e29c3-135">In the second query, `null` results are filtered out from `String1` column explicitly.</span></span> <span data-ttu-id="e29c3-136">EF Core kann die Spalte `String1` beim Vergleich gefahrlos als Non-Nullable behandeln, was zu einer einfacheren Abfrage führt.</span><span class="sxs-lookup"><span data-stu-id="e29c3-136">EF Core can safely treat the `String1` column as non-nullable during comparison, resulting in a simpler query.</span></span>

## <a name="using-relational-null-semantics"></a><span data-ttu-id="e29c3-137">Verwenden der relationalen NULL-Semantik</span><span class="sxs-lookup"><span data-stu-id="e29c3-137">Using relational null semantics</span></span>

<span data-ttu-id="e29c3-138">Es ist möglich, die Kompensierung von NULL-Vergleichen zu deaktivieren und direkt eine relationale NULL-Semantik zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="e29c3-138">It's possible to disable the null comparison compensation and use relational null semantics directly.</span></span> <span data-ttu-id="e29c3-139">Dazu muss die Methode `UseRelationalNulls(true)` für OptionsBuilder innerhalb Methode `OnConfiguring` aufgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="e29c3-139">This can be done by calling `UseRelationalNulls(true)` method on the options builder inside `OnConfiguring` method:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsContext.cs#UseRelationalNulls)]

> [!WARNING]
> <span data-ttu-id="e29c3-140">Wenn Sie die relationale NULL-Semantik verwenden, haben Ihre LINQ-Abfragen nicht mehr dieselbe Bedeutung wie in C#, und es kann zu unerwarteten Ergebnissen kommen.</span><span class="sxs-lookup"><span data-stu-id="e29c3-140">When using relational null semantics, your LINQ queries no longer have the same meaning as they do in C#, and may yield different results than expected.</span></span> <span data-ttu-id="e29c3-141">Gehen Sie umsichtig vor, wenn Sie diesen Modus verwenden.</span><span class="sxs-lookup"><span data-stu-id="e29c3-141">Exercise caution when using this mode.</span></span>
