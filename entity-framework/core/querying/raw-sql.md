---
title: Unformatierte SQL-Abfragen – EF Core
author: smitpatel
ms.date: 10/08/2019
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: a54bb67c0fce9d621382f6372e70fe4cdca48a20
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413714"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="2e449-102">Unformatierte SQL-Abfragen</span><span class="sxs-lookup"><span data-stu-id="2e449-102">Raw SQL Queries</span></span>

<span data-ttu-id="2e449-103">Mit Entity Framework Core können Sie bei der Arbeit mit einer relationalen Datenbank die Struktur unformatierter SQL-Abfragen maximieren.</span><span class="sxs-lookup"><span data-stu-id="2e449-103">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="2e449-104">Unformatierte SQL-Abfragen sind nützlich, wenn die gewünschte Abfrage nicht mithilfe von LINQ ausgedrückt werden kann.</span><span class="sxs-lookup"><span data-stu-id="2e449-104">Raw SQL queries are useful if the query you want can't be expressed using LINQ.</span></span> <span data-ttu-id="2e449-105">Unformatierte SQL-Abfragen werden auch verwendet, wenn eine LINQ-Abfrage zu einer ineffizienten SQL-Abfrage führt.</span><span class="sxs-lookup"><span data-stu-id="2e449-105">Raw SQL queries are also used if using a LINQ query is resulting in an inefficient SQL query.</span></span> <span data-ttu-id="2e449-106">Unformatierte SQL-Abfragen können reguläre Entitätstypen oder [schlüssellose Entitätstypen](xref:core/modeling/keyless-entity-types) zurückgeben, die Teil des Modells sind.</span><span class="sxs-lookup"><span data-stu-id="2e449-106">Raw SQL queries can return regular entity types or [keyless entity types](xref:core/modeling/keyless-entity-types) that are part of your model.</span></span>

> [!TIP]  
> <span data-ttu-id="2e449-107">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="2e449-107">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/) on GitHub.</span></span>

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="2e449-108">Grundlegende unformatierte SQL-Abfragen</span><span class="sxs-lookup"><span data-stu-id="2e449-108">Basic raw SQL queries</span></span>

<span data-ttu-id="2e449-109">Sie können die `FromSqlRaw`-Erweiterungsmethode verwenden, um eine LINQ-Abfrage basierend auf einer unformatierten SQL-Abfrage zu starten.</span><span class="sxs-lookup"><span data-stu-id="2e449-109">You can use the `FromSqlRaw` extension method to begin a LINQ query based on a raw SQL query.</span></span> <span data-ttu-id="2e449-110">`FromSqlRaw` kann nur für Abfragestämme verwendet werden, die sich direkt in der `DbSet<>`-Klasse befinden.</span><span class="sxs-lookup"><span data-stu-id="2e449-110">`FromSqlRaw` can only be used on query roots, that is directly on the `DbSet<>`.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRaw)]

<span data-ttu-id="2e449-111">Unformatierte SQL-Abfragen können für die Ausführung einer gespeicherten Prozedur verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2e449-111">Raw SQL queries can be used to execute a stored procedure.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedure)]

## <a name="passing-parameters"></a><span data-ttu-id="2e449-112">Übergeben von Parametern</span><span class="sxs-lookup"><span data-stu-id="2e449-112">Passing parameters</span></span>

> [!WARNING]
> <span data-ttu-id="2e449-113">**Für unformatierte SQL-Abfragen immer Parametrisierung verwenden**</span><span class="sxs-lookup"><span data-stu-id="2e449-113">**Always use parameterization for raw SQL queries**</span></span>
>
> <span data-ttu-id="2e449-114">Wenn Sie von Benutzern bereitgestellte Werte in eine unformatierte SQL-Abfrage einführen, muss darauf geachtet werden, dass Angriffe durch Einschleusung von SQL-Befehlen vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="2e449-114">When introducing any user-provided values into a raw SQL query, care must be taken to avoid SQL injection attacks.</span></span> <span data-ttu-id="2e449-115">Überprüfen Sie nicht nur, ob solche Werte keine ungültigen Zeichen enthalten, sondern verwenden Sie auch immer die Parametrisierung, die die Werte getrennt vom SQL-Text sendet.</span><span class="sxs-lookup"><span data-stu-id="2e449-115">In addition to validating that such values don't contain invalid characters, always use parameterization which sends the values separate from the SQL text.</span></span>
>
> <span data-ttu-id="2e449-116">Übergeben Sie insbesondere niemals eine verkettete oder interpolierte Zeichenfolge (`$""`) mit nicht validierten und von Benutzern bereitgestellten Werten in `FromSqlRaw` oder `ExecuteSqlRaw`.</span><span class="sxs-lookup"><span data-stu-id="2e449-116">In particular, never pass a concatenated or interpolated string (`$""`) with non-validated user-provided values into `FromSqlRaw` or `ExecuteSqlRaw`.</span></span> <span data-ttu-id="2e449-117">Dank der `FromSqlInterpolated`- und `ExecuteSqlInterpolated`-Methode kann die Zeichenfolgeninterpolationssyntax so eingesetzt werden, dass sie vor Angriffen durch Einschleusung von SQL-Befehlen schützt.</span><span class="sxs-lookup"><span data-stu-id="2e449-117">The `FromSqlInterpolated` and `ExecuteSqlInterpolated` methods allow using string interpolation syntax in a way that protects against SQL injection attacks.</span></span>

<span data-ttu-id="2e449-118">Im folgenden Beispiel wird ein einzelner Parameter an eine gespeicherte Prozedur übergeben, indem ein Parameterplatzhalter in die SQL-Abfragezeichenfolge eingeschlossen und ein zusätzliches Argument bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="2e449-118">The following example passes a single parameter to a stored procedure by including a parameter placeholder in the SQL query string and providing an additional argument.</span></span> <span data-ttu-id="2e449-119">Diese Syntax sieht zwar aus wie eine `String.Format`-Syntax, jedoch wird der angegebene Wert in einer `DbParameter`-Klasse eingeschlossen und der generierte Parametername wird dort eingefügt, wo der `{0}`-Platzhalter angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="2e449-119">While this syntax may look like `String.Format` syntax, the supplied value is wrapped in a `DbParameter` and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureParameter)]

<span data-ttu-id="2e449-120">`FromSqlInterpolated` ähnelt `FromSqlRaw`, lässt jedoch die Verwendung der Zeichenfolgeninterpolationsyntax zu.</span><span class="sxs-lookup"><span data-stu-id="2e449-120">`FromSqlInterpolated` is similar to `FromSqlRaw` but allows you to use string interpolation syntax.</span></span> <span data-ttu-id="2e449-121">`FromSqlInterpolated` kann wie `FromSqlRaw` nur für Abfragestämme verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2e449-121">Just like `FromSqlRaw`, `FromSqlInterpolated` can only be used on query roots.</span></span> <span data-ttu-id="2e449-122">Wie im vorherigen Beispiel wird der Wert in eine `DbParameter`-Klasse konvertiert und ist nicht anfällig für die Einschleusung von SQL-Befehlen.</span><span class="sxs-lookup"><span data-stu-id="2e449-122">As with the previous example, the value is converted to a `DbParameter` and isn't vulnerable to SQL injection.</span></span>

> [!NOTE]
> <span data-ttu-id="2e449-123">Vor Version 3.0 waren `FromSqlRaw` und `FromSqlInterpolated` zwei Überladungen namens `FromSql`.</span><span class="sxs-lookup"><span data-stu-id="2e449-123">Prior to version 3.0, `FromSqlRaw` and `FromSqlInterpolated` were two overloads named `FromSql`.</span></span> <span data-ttu-id="2e449-124">Weitere Informationen finden Sie im [Abschnitt zu vorherigen Versionen](#previous-versions).</span><span class="sxs-lookup"><span data-stu-id="2e449-124">For more information, see the [previous versions section](#previous-versions).</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedStoredProcedureParameter)]

<span data-ttu-id="2e449-125">Sie können auch einen DbParameter erstellen und diesen als Parameterwert bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="2e449-125">You can also construct a DbParameter and supply it as a parameter value.</span></span> <span data-ttu-id="2e449-126">Da anstelle eines Zeichenfolgeplatzhalters ein regulärer SQL-Parameterplatzhalter verwendet wird, kann `FromSqlRaw` sicher verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="2e449-126">Since a regular SQL parameter placeholder is used, rather than a string placeholder, `FromSqlRaw` can be safely used:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureSqlParameter)]

<span data-ttu-id="2e449-127">Mit `FromSqlRaw` können Sie benannte Parameter in der SQL-Abfragezeichenfolge verwenden. Dies ist nützlich, wenn eine gespeicherte Prozedur optionale Parameter aufweist:</span><span class="sxs-lookup"><span data-stu-id="2e449-127">`FromSqlRaw` allows you to use named parameters in the SQL query string, which is useful when a stored procedure has optional parameters:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureNamedSqlParameter)]

## <a name="composing-with-linq"></a><span data-ttu-id="2e449-128">Zusammensetzen mit LINQ</span><span class="sxs-lookup"><span data-stu-id="2e449-128">Composing with LINQ</span></span>

<span data-ttu-id="2e449-129">Mithilfe von LINQ-Operatoren können Sie die zunächst unformatierten SQL-Abfragen zusammensetzen.</span><span class="sxs-lookup"><span data-stu-id="2e449-129">You can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="2e449-130">EF Core behandelt diese als Unterabfragen und fügt sie in der Datenbank zusammen.</span><span class="sxs-lookup"><span data-stu-id="2e449-130">EF Core will treat it as subquery and compose over it in the database.</span></span> <span data-ttu-id="2e449-131">Im folgenden Beispiel wird eine unformatierte SQL-Abfrage verwendet, die aus einer Tabellenwertfunktion auswählt.</span><span class="sxs-lookup"><span data-stu-id="2e449-131">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF).</span></span> <span data-ttu-id="2e449-132">Anschließend wird sie mithilfe von LINQ zusammengesetzt, um die Filterung und Sortierung durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="2e449-132">And then composes on it using LINQ to do filtering and sorting.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedComposed)]

<span data-ttu-id="2e449-133">Mit der obigen Abfrage wird folgendes SQL generiert:</span><span class="sxs-lookup"><span data-stu-id="2e449-133">Above query generates following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM (
    SELECT * FROM dbo.SearchBlogs(@p0)
) AS [b]
WHERE [b].[Rating] > 3
ORDER BY [b].[Rating] DESC
```

### <a name="including-related-data"></a><span data-ttu-id="2e449-134">Einschließen zugehöriger Daten</span><span class="sxs-lookup"><span data-stu-id="2e449-134">Including related data</span></span>

<span data-ttu-id="2e449-135">Die `Include`-Methode kann verwendet werden, um verwandte Daten einzuschließen, genauso wie bei jeder anderen LINQ-Abfrage:</span><span class="sxs-lookup"><span data-stu-id="2e449-135">The `Include` method can be used to include related data, just like with any other LINQ query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedInclude)]

<span data-ttu-id="2e449-136">Für die Zusammensetzung mit LINQ muss Ihre unformatierte SQL-Abfrage zusammensetzbar sein, da EF Core das angegebene SQL als Unterabfrage behandelt.</span><span class="sxs-lookup"><span data-stu-id="2e449-136">Composing with LINQ requires your raw SQL query to be composable since EF Core will treat the supplied SQL as a subquery.</span></span> <span data-ttu-id="2e449-137">Zusammensetzbare SQL-Abfragen beginnen mit dem Schlüsselwort `SELECT`.</span><span class="sxs-lookup"><span data-stu-id="2e449-137">SQL queries that can be composed on begin with the `SELECT` keyword.</span></span> <span data-ttu-id="2e449-138">Außerdem sollte übergebenes SQL keine Zeichen oder Optionen enthalten, die für eine Unterabfrage ungültig sind, zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2e449-138">Further, SQL passed shouldn't contain any characters or options that aren't valid on a subquery, such as:</span></span>

- <span data-ttu-id="2e449-139">Ein nachfolgendes Semikolon</span><span class="sxs-lookup"><span data-stu-id="2e449-139">A trailing semicolon</span></span>
- <span data-ttu-id="2e449-140">Auf SQL Server ein nachfolgender Hinweis auf Abfrageebene, z.B. `OPTION (HASH JOIN)`</span><span class="sxs-lookup"><span data-stu-id="2e449-140">On SQL Server, a trailing query-level hint (for example, `OPTION (HASH JOIN)`)</span></span>
- <span data-ttu-id="2e449-141">Eine `ORDER BY`-Klausel in SQL Server, die nicht mit `OFFSET 0` ODER `TOP 100 PERCENT` in der `SELECT`-Klausel verwendet wird</span><span class="sxs-lookup"><span data-stu-id="2e449-141">On SQL Server, an `ORDER BY` clause that isn't used with `OFFSET 0` OR `TOP 100 PERCENT` in the `SELECT` clause</span></span>

<span data-ttu-id="2e449-142">SQL Server erlaubt die Zusammensetzung von Aufrufen gespeicherter Prozeduren nicht. Daher resultieren Versuche, zusätzliche Abfrageoperatoren auf solche Aufrufe anzuwenden, in ungültigem SQL.</span><span class="sxs-lookup"><span data-stu-id="2e449-142">SQL Server doesn't allow composing over stored procedure calls, so any attempt to apply additional query operators to such a call will result in invalid SQL.</span></span> <span data-ttu-id="2e449-143">Verwenden Sie die `AsEnumerable`- oder `AsAsyncEnumerable`-Methode direkt nach der `FromSqlRaw`- oder `FromSqlInterpolated`-Methode, um sicherzustellen, dass EF Core nicht ersucht, eine gespeicherte Prozedur zusammenzusetzen.</span><span class="sxs-lookup"><span data-stu-id="2e449-143">Use `AsEnumerable` or `AsAsyncEnumerable` method right after `FromSqlRaw` or `FromSqlInterpolated` methods to make sure that EF Core doesn't try to compose over a stored procedure.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="2e449-144">Change Tracking</span><span class="sxs-lookup"><span data-stu-id="2e449-144">Change Tracking</span></span>

<span data-ttu-id="2e449-145">Abfragen, die die `FromSqlRaw`- oder `FromSqlInterpolated`-Methode verwenden, befolgen genau dieselben Änderungsnachverfolgungsregeln wie jede andere LINQ-Abfrage in EF Core.</span><span class="sxs-lookup"><span data-stu-id="2e449-145">Queries that use the `FromSqlRaw` or `FromSqlInterpolated` methods follow the exact same change tracking rules as any other LINQ query in EF Core.</span></span> <span data-ttu-id="2e449-146">Wenn beispielsweise die Abfrage Entitätstypen projiziert, werden die Ergebnisse standardmäßig nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="2e449-146">For example, if the query projects entity types, the results will be tracked by default.</span></span>

<span data-ttu-id="2e449-147">Im folgenden Beispiel wird eine unformatierte SQL-Abfrage verwendet, die aus einer Tabellenwertfunktion (TVF) auswählt und anschließend die Änderungsnachverfolgung mit dem Aufruf auf `AsNoTracking` deaktiviert:</span><span class="sxs-lookup"><span data-stu-id="2e449-147">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF), then disables change tracking with the call to `AsNoTracking`:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedAsNoTracking)]

## <a name="limitations"></a><span data-ttu-id="2e449-148">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="2e449-148">Limitations</span></span>

<span data-ttu-id="2e449-149">Bei der Verwendung unformatierter SQL-Abfragen sind einige wenige Einschränkungen zu beachten:</span><span class="sxs-lookup"><span data-stu-id="2e449-149">There are a few limitations to be aware of when using raw SQL queries:</span></span>

- <span data-ttu-id="2e449-150">Die SQL-Abfrage muss Daten für sämtliche Eigenschaften des Entitätstyps zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="2e449-150">The SQL query must return data for all properties of the entity type.</span></span>
- <span data-ttu-id="2e449-151">Die Spaltennamen im Resultset müssen mit den Spaltennamen übereinstimmen, denen Eigenschaften zugewiesen sind.</span><span class="sxs-lookup"><span data-stu-id="2e449-151">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="2e449-152">Beachten Sie, dass sich dieses Verhalten von EF6 unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="2e449-152">Note this behavior is different from EF6.</span></span> <span data-ttu-id="2e449-153">EF6 ignoriert die Zuordnung von Eigenschaften zu Spalten bei unformatierten SQL-Abfragen, und Spaltennamen von Resultsets mussten mit den Eigenschaftennamen übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="2e449-153">EF6 ignored property to column mapping for raw SQL queries and result set column names had to match the property names.</span></span>
- <span data-ttu-id="2e449-154">Die SQL-Abfrage darf keine zugehörigen Daten enthalten.</span><span class="sxs-lookup"><span data-stu-id="2e449-154">The SQL query can't contain related data.</span></span> <span data-ttu-id="2e449-155">In vielen Fällen können Sie die Abfrage jedoch mit dem Operator `Include` zusammensetzen, damit zugehörige Daten zurückgegeben werden (siehe [Einschließen zugehöriger Daten](#including-related-data)).</span><span class="sxs-lookup"><span data-stu-id="2e449-155">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

## <a name="previous-versions"></a><span data-ttu-id="2e449-156">Vorgängerversionen</span><span class="sxs-lookup"><span data-stu-id="2e449-156">Previous versions</span></span>

<span data-ttu-id="2e449-157">In EF Core Version 2.2 und früher gab es zwei Überladungen für eine Methode namens `FromSql`, die sich wie die neueren Methoden `FromSqlRaw` und `FromSqlInterpolated` verhielten.</span><span class="sxs-lookup"><span data-stu-id="2e449-157">EF Core version 2.2 and earlier had two overloads of method named `FromSql`, which behaved in the same way as the newer `FromSqlRaw` and `FromSqlInterpolated`.</span></span> <span data-ttu-id="2e449-158">Es war leicht, versehentlich die unformatierte Zeichenfolgenmethode aufzurufen, wenn eigentlich ein Aufruf der interpolierten Zeichenfolgenmethode beabsichtigt war oder umgekehrt.</span><span class="sxs-lookup"><span data-stu-id="2e449-158">It was easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span> <span data-ttu-id="2e449-159">Versehentliche Aufrufe der falschen Überladung konnten dazu führen, dass Abfragen nicht parametrisiert wurden, wenn sie parametrisiert werden sollten.</span><span class="sxs-lookup"><span data-stu-id="2e449-159">Calling wrong overload accidentally could result in queries not being parameterized when they should have been.</span></span>
