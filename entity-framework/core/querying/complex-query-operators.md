---
title: Komplexe Abfrageoperatoren – EF Core
description: Umfassende Informationen zu komplexeren LINQ-Abfrageoperatoren bei Verwendung von Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/complex-query-operators
ms.openlocfilehash: 84c2518972355d31cf5a6a7bafc57b44162412c8
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430481"
---
# <a name="complex-query-operators"></a><span data-ttu-id="c4603-103">Komplexe Abfrageoperatoren</span><span class="sxs-lookup"><span data-stu-id="c4603-103">Complex Query Operators</span></span>

<span data-ttu-id="c4603-104">LINQ (Language Integrated Query) enthält viele komplexe Operatoren, die mehrere Datenquellen kombinieren oder komplexe Verarbeitungen durchführen.</span><span class="sxs-lookup"><span data-stu-id="c4603-104">Language Integrated Query (LINQ) contains many complex operators, which combine multiple data sources or does complex processing.</span></span> <span data-ttu-id="c4603-105">Nicht für alle LINQ-Operatoren gibt es geeignete Übersetzungen auf der Serverseite.</span><span class="sxs-lookup"><span data-stu-id="c4603-105">Not all LINQ operators have suitable translations on the server side.</span></span> <span data-ttu-id="c4603-106">Manchmal wird eine Abfrage in einem Format für den Server übersetzt, aber in einem anderen Format nicht, obwohl das Ergebnis gleich ist.</span><span class="sxs-lookup"><span data-stu-id="c4603-106">Sometimes, a query in one form translates to the server but if written in a different form doesn't translate even if the result is the same.</span></span> <span data-ttu-id="c4603-107">Auf dieser Seite werden einige der komplexen Operatoren und ihre unterstützten Variationen beschrieben.</span><span class="sxs-lookup"><span data-stu-id="c4603-107">This page describes some of the complex operators and their supported variations.</span></span> <span data-ttu-id="c4603-108">Möglicherweise werden in zukünftigen Releases mehr Muster erkannt und deren entsprechenden Übersetzungen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c4603-108">In future releases, we may recognize more patterns and add their corresponding translations.</span></span> <span data-ttu-id="c4603-109">Es ist auch wichtig, zu beachten, dass die Übersetzungsunterstützung je nach Anbieter variiert.</span><span class="sxs-lookup"><span data-stu-id="c4603-109">It's also important to keep in mind that translation support varies between providers.</span></span> <span data-ttu-id="c4603-110">Eine bestimmte Abfrage, die für SQL Server übersetzt wird, funktioniert möglicherweise nicht für SQLite-Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="c4603-110">A particular query, which is translated in SqlServer, may not work for SQLite databases.</span></span>

> [!TIP]
> <span data-ttu-id="c4603-111">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/ComplexQuery) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="c4603-111">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/ComplexQuery) on GitHub.</span></span>

## <a name="join"></a><span data-ttu-id="c4603-112">Join</span><span class="sxs-lookup"><span data-stu-id="c4603-112">Join</span></span>

<span data-ttu-id="c4603-113">Mit dem LINQ-Join-Operator können Sie zwei Datenquellen anhand des Schlüsselselektors für die jeweilige Quelle verbinden und ein Tupel von Werten erstellen, wenn der Schlüssel übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="c4603-113">The LINQ Join operator allows you to connect two data sources based on the key selector for each source, generating a tuple of values when the key matches.</span></span> <span data-ttu-id="c4603-114">Dies wird für relationale Datenbanken natürlich in `INNER JOIN` übersetzt.</span><span class="sxs-lookup"><span data-stu-id="c4603-114">It naturally translates to `INNER JOIN` on relational databases.</span></span> <span data-ttu-id="c4603-115">Während der LINQ-Join-Operator äußere und innere Schlüsselselektoren aufweist, erfordert die Datenbank eine einzelne Verknüpfungsbedingung.</span><span class="sxs-lookup"><span data-stu-id="c4603-115">While the LINQ Join has outer and inner key selectors, the database requires a single join condition.</span></span> <span data-ttu-id="c4603-116">Daher generiert EF Core eine Verknüpfungsbedingung mithilfe eines Vergleichs der Gleichheit des äußeren und inneren Schlüsselselektors.</span><span class="sxs-lookup"><span data-stu-id="c4603-116">So EF Core generates a join condition by comparing the outer key selector to the inner key selector for equality.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#Join)]

```sql
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

<span data-ttu-id="c4603-117">Des Weiteren generiert EF Core eine Verknüpfungsbedingung, um die Gleichheit der Komponenten zu vergleichen, wenn die Schlüsselselektoren einen anonymen Typ aufweisen.</span><span class="sxs-lookup"><span data-stu-id="c4603-117">Further, if the key selectors are anonymous types, EF Core generates a join condition to compare equality component-wise.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#JoinComposite)]

```sql
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON ([p0].[PersonPhotoId] = [p].[PhotoId] AND ([p0].[Caption] = N'SN'))
```

## <a name="groupjoin"></a><span data-ttu-id="c4603-118">GroupJoin</span><span class="sxs-lookup"><span data-stu-id="c4603-118">GroupJoin</span></span>

<span data-ttu-id="c4603-119">Mit dem LINQ-GroupJoin-Operator können Sie zwei Datenquellen ähnlich wie mit dem Join-Operator verbinden, jedoch wird dabei eine Gruppe innerer Werte zum Vergleichen äußerer Elemente erstellt.</span><span class="sxs-lookup"><span data-stu-id="c4603-119">The LINQ GroupJoin operator allows you to connect two data sources similar to Join, but it creates a group of inner values for matching outer elements.</span></span> <span data-ttu-id="c4603-120">Das Ausführen einer Abfrage ähnlich dem folgenden Beispiel führt zum Ergebnis: `Blog` & `IEnumerable<Post>`.</span><span class="sxs-lookup"><span data-stu-id="c4603-120">Executing a query like the following example generates a result of `Blog` & `IEnumerable<Post>`.</span></span> <span data-ttu-id="c4603-121">Da Datenbanken (insbesondere relationale Datenbanken) keine Möglichkeit zum Darstellen einer Sammlung von clientseitigen Objekten aufweisen, können GroupJoin-Operatoren in vielen Fällen nicht auf den Server übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="c4603-121">Since databases (especially relational databases) don't have a way to represent a collection of client-side objects, GroupJoin doesn't translate to the server in many cases.</span></span> <span data-ttu-id="c4603-122">Hierzu ist erforderlich, dass Sie alle Daten für GroupJoin ohne einen speziellen Selektor vom Server abrufen (die erste Abfrage unten).</span><span class="sxs-lookup"><span data-stu-id="c4603-122">It requires you to get all of the data from the server to do GroupJoin without a special selector (first query below).</span></span> <span data-ttu-id="c4603-123">Wenn der Selektor jedoch die Auswahl von Daten einschränkt und dann alle Daten vom Server abruft, können Leistungsprobleme auftreten (die zweite Abfrage unten).</span><span class="sxs-lookup"><span data-stu-id="c4603-123">But if the selector is limiting data being selected then fetching all of the data from the server may cause performance issues (second query below).</span></span> <span data-ttu-id="c4603-124">Daher übersetzt EF Core GroupJoin nicht.</span><span class="sxs-lookup"><span data-stu-id="c4603-124">That's why EF Core doesn't translate GroupJoin.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupJoinComposed)]

## <a name="selectmany"></a><span data-ttu-id="c4603-125">SelectMany</span><span class="sxs-lookup"><span data-stu-id="c4603-125">SelectMany</span></span>

<span data-ttu-id="c4603-126">Mit dem LINQ-SelectMany-Operator können Sie einen Sammlungsselektor für äußere Elemente aufzählen und Tupel von Werten aus einzelnen Datenquellen generieren.</span><span class="sxs-lookup"><span data-stu-id="c4603-126">The LINQ SelectMany operator allows you to enumerate over a collection selector for each outer element and generate tuples of values from each data source.</span></span> <span data-ttu-id="c4603-127">Auf gewisse Zeit ist dies ein Join, der jedoch keine Bedingungen aufweist, sodass alle äußeren Elemente mit einem Element aus der Sammlungsquelle verknüpft werden.</span><span class="sxs-lookup"><span data-stu-id="c4603-127">In a way, it's a join but without any condition so every outer element is connected with an element from the collection source.</span></span> <span data-ttu-id="c4603-128">Je nachdem, wie der Sammlungsselektor im Bezug mit der äußeren Datenquelle steht, kann SelectMany in verschiedene Abfragen auf der Serverseite übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="c4603-128">Depending on how the collection selector is related to the outer data source, SelectMany can translate into various different queries on the server side.</span></span>

### <a name="collection-selector-doesnt-reference-outer"></a><span data-ttu-id="c4603-129">Sammlungsselektor referenziert nicht die äußere Quelle</span><span class="sxs-lookup"><span data-stu-id="c4603-129">Collection selector doesn't reference outer</span></span>

<span data-ttu-id="c4603-130">Wenn der Sammlungsselektor nichts von der äußeren Quelle referenziert, ist das Ergebnis ein kartesisches Produkt beider Datenquellen.</span><span class="sxs-lookup"><span data-stu-id="c4603-130">When the collection selector isn't referencing anything from the outer source, the result is a cartesian product of both data sources.</span></span> <span data-ttu-id="c4603-131">Dies wird für relationale Datenbanken in `CROSS JOIN` übersetzt.</span><span class="sxs-lookup"><span data-stu-id="c4603-131">It translates to `CROSS JOIN` in relational databases.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToCrossJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a><span data-ttu-id="c4603-132">Sammlungsselektor referenziert die äußere Datenquelle in einer WHERE-Klausel</span><span class="sxs-lookup"><span data-stu-id="c4603-132">Collection selector references outer in a where clause</span></span>

<span data-ttu-id="c4603-133">Wenn der Sammlungsselektor eine WHERE-Klausel enthält, die das äußere Element referenziert, dann übersetzt EF Core sie in einen Join der Datenbank und verwendet das Prädikat als Joinbedingung.</span><span class="sxs-lookup"><span data-stu-id="c4603-133">When the collection selector has a where clause, which references the outer element, then EF Core translates it to a database join and uses the predicate as the join condition.</span></span> <span data-ttu-id="c4603-134">Normalerweise tritt dieser Fall auf, wenn die Sammlungsnavigation im äußeren Element als Sammlungsselektor verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c4603-134">Normally this case arises when using collection navigation on the outer element as the collection selector.</span></span> <span data-ttu-id="c4603-135">Wenn die Sammlung für ein äußeres Element leer ist, werden keine Ergebnisse für dieses generiert.</span><span class="sxs-lookup"><span data-stu-id="c4603-135">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="c4603-136">Wenn `DefaultIfEmpty` jedoch auf den Sammlungsselektor angewendet wird, wird das äußere Element mit einem Standardwert des inneren Elements verknüpft.</span><span class="sxs-lookup"><span data-stu-id="c4603-136">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="c4603-137">Aufgrund dieser Unterscheidung wird diese Art von Abfrage bei Abwesenheit von `DefaultIfEmpty` in `INNER JOIN` übersetzt und in `LEFT JOIN`, wenn `DefaultIfEmpty` angewendet wurde.</span><span class="sxs-lookup"><span data-stu-id="c4603-137">Because of this distinction, this kind of queries translates to `INNER JOIN` in the absence of `DefaultIfEmpty` and `LEFT JOIN` when `DefaultIfEmpty` is applied.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a><span data-ttu-id="c4603-138">Sammlungsselektor referenziert die äußere Datenquelle in einem Fall ohne WHERE-Klausel</span><span class="sxs-lookup"><span data-stu-id="c4603-138">Collection selector references outer in a non-where case</span></span>

<span data-ttu-id="c4603-139">Wenn der Sammlungsselektor das äußere Element referenziert, das sich nicht in einer WHERE-Klausel befindet (wie im obigen Fall), wird es nicht in einen Join der Datenbank übersetzt.</span><span class="sxs-lookup"><span data-stu-id="c4603-139">When the collection selector references the outer element, which isn't in a where clause (as the case above), it doesn't translate to a database join.</span></span> <span data-ttu-id="c4603-140">Daher muss der Sammlungsselektor für alle äußeren Elemente ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="c4603-140">That's why we need to evaluate the collection selector for each outer element.</span></span> <span data-ttu-id="c4603-141">Für viele relationale Datenbanken wird dies in `APPLY`-Vorgänge übersetzt.</span><span class="sxs-lookup"><span data-stu-id="c4603-141">It translates to `APPLY` operations in many relational databases.</span></span> <span data-ttu-id="c4603-142">Wenn die Sammlung für ein äußeres Element leer ist, werden keine Ergebnisse für dieses generiert.</span><span class="sxs-lookup"><span data-stu-id="c4603-142">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="c4603-143">Wenn `DefaultIfEmpty` jedoch auf den Sammlungsselektor angewendet wird, wird das äußere Element mit einem Standardwert des inneren Elements verknüpft.</span><span class="sxs-lookup"><span data-stu-id="c4603-143">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="c4603-144">Aufgrund dieser Unterscheidung wird diese Art von Abfrage bei Abwesenheit von `DefaultIfEmpty` in `CROSS APPLY` übersetzt und in `OUTER APPLY`, wenn `DefaultIfEmpty` angewendet wurde.</span><span class="sxs-lookup"><span data-stu-id="c4603-144">Because of this distinction, this kind of queries translates to `CROSS APPLY` in the absence of `DefaultIfEmpty` and `OUTER APPLY` when `DefaultIfEmpty` is applied.</span></span> <span data-ttu-id="c4603-145">Bestimmte Datenbanken wie SQLite unterstützen `APPLY`-Operatoren nicht. Daher wird diese Art von Abfrage möglicherweise nicht übersetzt.</span><span class="sxs-lookup"><span data-stu-id="c4603-145">Certain databases like SQLite don't support `APPLY` operators so this kind of query may not be translated.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToApply)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a><span data-ttu-id="c4603-146">GroupBy</span><span class="sxs-lookup"><span data-stu-id="c4603-146">GroupBy</span></span>

<span data-ttu-id="c4603-147">LINQ-GroupBy-Operatoren erstellen ein Ergebnis vom Typ `IGrouping<TKey, TElement>`, wobei `TKey` und `TElement` einen beliebigen arbiträren Typ aufweisen können.</span><span class="sxs-lookup"><span data-stu-id="c4603-147">LINQ GroupBy operators create a result of type `IGrouping<TKey, TElement>` where `TKey` and `TElement` could be any arbitrary type.</span></span> <span data-ttu-id="c4603-148">Außerdem wird `IEnumerable<TElement>` von `IGrouping` implementiert, d. h. Sie können es nach der Gruppierung mithilfe eines beliebigen LINQ-Operators zusammensetzen.</span><span class="sxs-lookup"><span data-stu-id="c4603-148">Furthermore, `IGrouping` implements `IEnumerable<TElement>`, which means you can compose over it using any LINQ operator after the grouping.</span></span> <span data-ttu-id="c4603-149">Da keine Datenbankstruktur eine `IGrouping`-Schnittstelle darstellen kann, gibt es in den meisten Fällen keine Übersetzung für GroupBy-Operatoren.</span><span class="sxs-lookup"><span data-stu-id="c4603-149">Since no database structure can represent an `IGrouping`, GroupBy operators have no translation in most cases.</span></span> <span data-ttu-id="c4603-150">Wenn ein Aggregate-Operator auf alle Gruppen angewendet wird, die einen Skalarwert zurückgeben, kann dieser für relationale Datenbanken in die SQL-Anweisung `GROUP BY` übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="c4603-150">When an aggregate operator is applied to each group, which returns a scalar, it can be translated to SQL `GROUP BY` in relational databases.</span></span> <span data-ttu-id="c4603-151">Die SQL-Anweisung `GROUP BY` ist ebenfalls einschränkend.</span><span class="sxs-lookup"><span data-stu-id="c4603-151">The SQL `GROUP BY` is restrictive too.</span></span> <span data-ttu-id="c4603-152">Sie erfordert, dass Sie nur nach Skalarwerten gruppieren.</span><span class="sxs-lookup"><span data-stu-id="c4603-152">It requires you to group only by scalar values.</span></span> <span data-ttu-id="c4603-153">Die Projektion kann nur gruppierende Schlüsselspalten oder auf eine Spalte angewendete Aggregate-Operatoren enthalten.</span><span class="sxs-lookup"><span data-stu-id="c4603-153">The projection can only contain grouping key columns or any aggregate applied over a column.</span></span> <span data-ttu-id="c4603-154">EF Core identifiziert dieses Muster und übersetzt es wie im folgenden Beispiel für den Server:</span><span class="sxs-lookup"><span data-stu-id="c4603-154">EF Core identifies this pattern and translates it to the server, as in the following example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupBy)]

```sql
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

<span data-ttu-id="c4603-155">EF Core übersetzt Abfragen auch, wenn ein Aggregate-Operator für die Gruppierung in einem WHERE- oder OrderBy-LINQ-Operator (oder in anderen Order-Operatoren) vorliegt.</span><span class="sxs-lookup"><span data-stu-id="c4603-155">EF Core also translates queries where an aggregate operator on the grouping appears in a Where or OrderBy (or other ordering) LINQ operator.</span></span> <span data-ttu-id="c4603-156">Für die WHERE-Klausel wird in SQL eine `HAVING`-Klausel verwendet.</span><span class="sxs-lookup"><span data-stu-id="c4603-156">It uses `HAVING` clause in SQL for the where clause.</span></span> <span data-ttu-id="c4603-157">Der Teil der Abfrage, bevor der GroupBy-Operator angewendet wird, kann einer beliebigen komplexen Abfrage entsprechen, solange diese für den Server übersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c4603-157">The part of the query before applying the GroupBy operator can be any complex query as long as it can be translated to server.</span></span> <span data-ttu-id="c4603-158">Sobald Sie Aggregate-Operatoren in einer Gruppierungsabfrage anwenden, um Gruppierungen aus der resultierenden Quelle zu entfernen, können Sie sie außerdem wie jede andere Abfrage zusammensetzen.</span><span class="sxs-lookup"><span data-stu-id="c4603-158">Furthermore, once you apply aggregate operators on a grouping query to remove groupings from the resulting source, you can compose on top of it like any other query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupByFilter)]

```sql
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

<span data-ttu-id="c4603-159">Die von EF Core unterstützten Aggregate-Operatoren lauten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="c4603-159">The aggregate operators EF Core supports are as follows</span></span>

- <span data-ttu-id="c4603-160">Average</span><span class="sxs-lookup"><span data-stu-id="c4603-160">Average</span></span>
- <span data-ttu-id="c4603-161">Anzahl</span><span class="sxs-lookup"><span data-stu-id="c4603-161">Count</span></span>
- <span data-ttu-id="c4603-162">LongCount</span><span class="sxs-lookup"><span data-stu-id="c4603-162">LongCount</span></span>
- <span data-ttu-id="c4603-163">Max</span><span class="sxs-lookup"><span data-stu-id="c4603-163">Max</span></span>
- <span data-ttu-id="c4603-164">Min</span><span class="sxs-lookup"><span data-stu-id="c4603-164">Min</span></span>
- <span data-ttu-id="c4603-165">Sum</span><span class="sxs-lookup"><span data-stu-id="c4603-165">Sum</span></span>

## <a name="left-join"></a><span data-ttu-id="c4603-166">Left Join</span><span class="sxs-lookup"><span data-stu-id="c4603-166">Left Join</span></span>

<span data-ttu-id="c4603-167">„Left Join“ ist zwar kein LINQ-Operator, jedoch verfügen relationale Datenbanken über das Konzept von „Left Join“, das häufig in Abfragen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c4603-167">While Left Join isn't a LINQ operator, relational databases have the concept of a Left Join which is frequently used in queries.</span></span> <span data-ttu-id="c4603-168">Ein bestimmtes Muster in LINQ-Abfragen führt zu demselben Ergebnis wie ein `LEFT JOIN` auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="c4603-168">A particular pattern in LINQ queries gives the same result as a `LEFT JOIN` on the server.</span></span> <span data-ttu-id="c4603-169">EF Core identifiziert solche Muster und generiert den entsprechenden `LEFT JOIN` auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="c4603-169">EF Core identifies such patterns and generates the equivalent `LEFT JOIN` on the server side.</span></span> <span data-ttu-id="c4603-170">Das Muster umfasst die Erstellung eines GroupJoin-Operators auf beiden Datenquellen sowie die anschließende Vereinfachung der Gruppierung mithilfe des SelectMany-Operators mit DefaultIfEmpty für die Gruppierungsquelle, um eine Übereinstimmung mit NULL zu suchen, wenn die innere Quelle kein zugehöriges Element aufweist.</span><span class="sxs-lookup"><span data-stu-id="c4603-170">The pattern involves creating a GroupJoin between both the data sources and then flattening out the grouping by using the SelectMany operator with DefaultIfEmpty on the grouping source to match null when the inner doesn't have a related element.</span></span> <span data-ttu-id="c4603-171">Im folgenden Beispiel wird das Muster und dessen Ergebnis veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="c4603-171">The following example shows what that pattern looks like and what it generates.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#LeftJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

<span data-ttu-id="c4603-172">Das obige Muster erstellt eine komplexe Struktur in der Ausdrucksbaumstruktur.</span><span class="sxs-lookup"><span data-stu-id="c4603-172">The above pattern creates a complex structure in the expression tree.</span></span> <span data-ttu-id="c4603-173">Daher erfordert EF Core, dass Sie die Gruppierungsergebnisse des GroupJoin-Operators in dem Schritt vereinfachen, der direkt auf den Operator folgt.</span><span class="sxs-lookup"><span data-stu-id="c4603-173">Because of that, EF Core requires you to flatten out the grouping results of the GroupJoin operator in a step immediately following the operator.</span></span> <span data-ttu-id="c4603-174">Selbst wenn GroupJoin-DefaultIfEmpty-SelectMany in einem anderen Muster verwendet wird, wird dies möglicherweise nicht als Left Join identifiziert.</span><span class="sxs-lookup"><span data-stu-id="c4603-174">Even if the GroupJoin-DefaultIfEmpty-SelectMany is used but in a different pattern, we may not identify it as a Left Join.</span></span>
