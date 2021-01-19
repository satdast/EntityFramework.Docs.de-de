---
title: 'Zuordnung benutzerdefinierter Funktionen: EF Core'
description: Hier erhalten Sie Informationen zum Zuordnen benutzerdefinierter Funktionen zu Datenbankfunktionen.
author: maumar
ms.date: 11/23/2020
uid: core/querying/user-defined-function-mapping
ms.openlocfilehash: 3e49ed9c49b38b98430128ffdc7ceef0b844b9df
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129121"
---
# <a name="user-defined-function-mapping"></a><span data-ttu-id="d7b28-103">Zuordnung benutzerdefinierter Funktionen</span><span class="sxs-lookup"><span data-stu-id="d7b28-103">User-defined function mapping</span></span>

<span data-ttu-id="d7b28-104">EF Core ermöglicht die Verwendung benutzerdefinierter SQL-Funktionen in Abfragen.</span><span class="sxs-lookup"><span data-stu-id="d7b28-104">EF Core allows for using user-defined SQL functions in queries.</span></span> <span data-ttu-id="d7b28-105">Dazu müssen die Funktionen während der Modellkonfiguration einer CLR-Methode zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="d7b28-105">To do that, the functions need to be mapped to a CLR method during model configuration.</span></span> <span data-ttu-id="d7b28-106">Beim Übersetzen der LINQ-Abfrage in SQL wird die benutzerdefinierte Funktion anstelle der CLR-Funktion aufgerufen, der sie zugeordnet wurde.</span><span class="sxs-lookup"><span data-stu-id="d7b28-106">When translating the LINQ query to SQL, the user-defined function is called instead of the CLR function it has been mapped to.</span></span>

## <a name="mapping-a-method-to-a-sql-function"></a><span data-ttu-id="d7b28-107">Zuordnen einer Methode zu einer SQL-Funktion</span><span class="sxs-lookup"><span data-stu-id="d7b28-107">Mapping a method to a SQL function</span></span>

<span data-ttu-id="d7b28-108">Damit veranschaulicht werden kann, wie das Zuordnen benutzerdefinierter Funktionen funktioniert, werden hier die folgenden Entitäten definiert:</span><span class="sxs-lookup"><span data-stu-id="d7b28-108">To illustrate how user-defined function mapping work, let's define the following entities:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#Entities)]

<span data-ttu-id="d7b28-109">Außerdem wird als Beispiel die folgende Modellkonfiguration verwendet:</span><span class="sxs-lookup"><span data-stu-id="d7b28-109">And the following model configuration:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#EntityConfiguration)]

<span data-ttu-id="d7b28-110">In Blogs finden sich viele Beiträge. Jeder Beitrag wiederum kann über viele Kommentare verfügen.</span><span class="sxs-lookup"><span data-stu-id="d7b28-110">Blog can have many posts and each post can have many comments.</span></span>

<span data-ttu-id="d7b28-111">Erstellen Sie als Nächstes die benutzerdefinierte Funktion `CommentedPostCountForBlog`. Diese gibt die Anzahl der Beiträge zurück, für die pro Blog – Blog `Id` in diesem Beispiel – mindestens ein Kommentar vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="d7b28-111">Next, create the user-defined function `CommentedPostCountForBlog`, which returns the count of posts with at least one comment for a given blog, based on the blog `Id`:</span></span>

```sql
CREATE FUNCTION dbo.CommentedPostCountForBlog(@id int)
RETURNS int
AS
BEGIN
    RETURN (SELECT COUNT(*)
        FROM [Posts] AS [p]
        WHERE ([p].[BlogId] = @id) AND ((
            SELECT COUNT(*)
            FROM [Comments] AS [c]
            WHERE [p].[PostId] = [c].[PostId]) > 0));
END
```

<span data-ttu-id="d7b28-112">Zur Verwendung dieser Funktion in EF Core wird die folgende CLR-Methode definiert, die der benutzerdefinierten Funktion zugeordnet wird:</span><span class="sxs-lookup"><span data-stu-id="d7b28-112">To use this function in EF Core, we define the following CLR method, which we map to the user-defined function:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionDefinition)]

<span data-ttu-id="d7b28-113">Der Methodenkörper der CLR-Methode spielt keine Rolle.</span><span class="sxs-lookup"><span data-stu-id="d7b28-113">The body of the CLR method is not important.</span></span> <span data-ttu-id="d7b28-114">Die Methode wird nur clientseitig aufgerufen, wenn EF Core die dazugehörigen Argumente nicht übersetzen kann.</span><span class="sxs-lookup"><span data-stu-id="d7b28-114">The method will not be invoked client-side, unless EF Core can't translate its arguments.</span></span> <span data-ttu-id="d7b28-115">Wenn die Argumente übersetzt werden können, spielt nur die Methodensignatur eine Rolle für EF Core.</span><span class="sxs-lookup"><span data-stu-id="d7b28-115">If the arguments can be translated, EF Core only cares about the method signature.</span></span>

> [!NOTE]
> <span data-ttu-id="d7b28-116">Im Beispiel wird die Methode für `DbContext` definiert. Die Definition als statische Methode innerhalb anderer Klassen ist aber ebenfalls möglich.</span><span class="sxs-lookup"><span data-stu-id="d7b28-116">In the example, the method is defined on `DbContext`, but it can also be defined as a static method inside other classes.</span></span>

<span data-ttu-id="d7b28-117">Diese Funktionsdefinition kann nun der benutzerdefinierten Funktion in der Modellkonfiguration zugeordnet werden:</span><span class="sxs-lookup"><span data-stu-id="d7b28-117">This function definition can now be associated with user-defined function in the model configuration:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionConfiguration)]

<span data-ttu-id="d7b28-118">Standardmäßig versucht EF Core, CLR-Funktionen einer benutzerdefinierten Funktion mit demselben Name zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="d7b28-118">By default, EF Core tries to map CLR function to a user-defined function with the same name.</span></span> <span data-ttu-id="d7b28-119">Wenn sich der Name unterscheidet, kann `HasName` verwendet werden, um den richtigen Namen für die benutzerdefinierte Funktion anzugeben, zu der die Zuordnung erfolgen soll.</span><span class="sxs-lookup"><span data-stu-id="d7b28-119">If the names differ, we can use `HasName` to provide the correct name for the user-defined function we want to map to.</span></span>

<span data-ttu-id="d7b28-120">Als Nächstes wird nun die folgende Abfrage ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="d7b28-120">Now, executing the following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#BasicQuery)]

<span data-ttu-id="d7b28-121">Dies führt zu folgendem SQL-Code:</span><span class="sxs-lookup"><span data-stu-id="d7b28-121">Will produce this SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE [dbo].[CommentedPostCountForBlog]([b].[BlogId]) > 1
```

## <a name="mapping-a-method-to-a-custom-sql"></a><span data-ttu-id="d7b28-122">Zuordnen einer Methode zu einem benutzerdefinierten SQL-Ausdruck</span><span class="sxs-lookup"><span data-stu-id="d7b28-122">Mapping a method to a custom SQL</span></span>

<span data-ttu-id="d7b28-123">EF Core ermöglicht es auch, benutzerdefinierte Funktionen in einen bestimmten SQL-Ausdruck zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="d7b28-123">EF Core also allows for user-defined functions that get converted to a specific SQL.</span></span> <span data-ttu-id="d7b28-124">Der SQL-Ausdruck wird mithilfe der `HasTranslation`-Methode während der Konfiguration der benutzerdefinierten Funktion angegeben.</span><span class="sxs-lookup"><span data-stu-id="d7b28-124">The SQL expression is provided using `HasTranslation` method during user-defined function configuration.</span></span>

<span data-ttu-id="d7b28-125">Im Beispiel unten wird eine Funktion erstellt, die den prozentualen Unterschied zwischen zwei Integern berechnet.</span><span class="sxs-lookup"><span data-stu-id="d7b28-125">In the example below, we'll create a function that computes percentage difference between two integers.</span></span>

<span data-ttu-id="d7b28-126">Die CLR-Methode lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="d7b28-126">The CLR method is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionDefinition)]

<span data-ttu-id="d7b28-127">Die Funktionsdefinition lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="d7b28-127">The function definition is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionConfiguration)]

<span data-ttu-id="d7b28-128">Sobald die Funktion definiert wurde, kann sie in der Abfrage verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d7b28-128">Once we define the function, it can be used in the query.</span></span> <span data-ttu-id="d7b28-129">Anstatt die Datenbankfunktion aufzurufen, übersetzt EF Core den Methodenkörper direkt in die SQL-basierte SQL-Ausdrucksbaumstruktur, die auf Grundlage von HasTranslation erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="d7b28-129">Instead of calling database function, EF Core will translate the method body directly into SQL based on the SQL expression tree constructed from the HasTranslation.</span></span> <span data-ttu-id="d7b28-130">Betrachten Sie die folgende LINQ-Abfrage:</span><span class="sxs-lookup"><span data-stu-id="d7b28-130">The following LINQ query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#HasTranslationQuery)]

<span data-ttu-id="d7b28-131">Dies generiert diese SQL-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="d7b28-131">Produces the following SQL:</span></span>

```sql
SELECT 100 * (ABS(CAST([p].[BlogId] AS float) - 3) / ((CAST([p].[BlogId] AS float) + 3) / 2))
FROM [Posts] AS [p]
```

## <a name="configuring-nullability-of-user-defined-function-based-on-its-arguments"></a><span data-ttu-id="d7b28-132">Konfigurieren der NULL-Zulässigkeit von benutzerdefinierten Funktionen auf Basis von Argumenten</span><span class="sxs-lookup"><span data-stu-id="d7b28-132">Configuring nullability of user-defined function based on its arguments</span></span>

<span data-ttu-id="d7b28-133">Wenn die benutzerdefinierte Funktion nur `null` zurückgeben kann, wenn mindestens ein Argument `null` ist, kann dies in EF Core angegeben werden. Dies führt zu einem leistungsfähigeren SQL-Code.</span><span class="sxs-lookup"><span data-stu-id="d7b28-133">If the user-defined function can only return `null` when one or more of its arguments are `null`, EFCore provides way to specify that, resulting in more performant SQL.</span></span> <span data-ttu-id="d7b28-134">Dazu muss der Modellkonfiguration des relevanten Funktionsparameters ein `PropagatesNullability()`-Aufruf hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="d7b28-134">It can be done by adding a `PropagatesNullability()` call to the relevant function parameters model configuration.</span></span>

<span data-ttu-id="d7b28-135">Zur Veranschaulichung wird die Benutzerfunktion `ConcatStrings` definiert:</span><span class="sxs-lookup"><span data-stu-id="d7b28-135">To illustrate this, define user function `ConcatStrings`:</span></span>

```sql
CREATE FUNCTION [dbo].[ConcatStrings] (@prm1 nvarchar(max), @prm2 nvarchar(max))
RETURNS nvarchar(max)
AS
BEGIN
    RETURN @prm1 + @prm2;
END
```

<span data-ttu-id="d7b28-136">Außerdem werden zwei CLR-Methoden definiert, die ihr zugeordnet sind:</span><span class="sxs-lookup"><span data-stu-id="d7b28-136">and two CLR methods that map to it:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationFunctionDefinition)]

<span data-ttu-id="d7b28-137">Die Modellkonfiguration (innerhalb der Methode `OnModelCreating`) lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="d7b28-137">The model configuration (inside `OnModelCreating` method) is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationModelConfiguration)]

<span data-ttu-id="d7b28-138">Die erste Funktion wird standardmäßig konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="d7b28-138">The first function is configured in the standard way.</span></span> <span data-ttu-id="d7b28-139">Die zweite Funktion ist so konfiguriert, dass sie die Propagierungsoptimierung der NULL-Zulässigkeit nutzt und weitere Informationen zur Funktionsweise der Funktion im Kontext von NULL-Parametern bietet.</span><span class="sxs-lookup"><span data-stu-id="d7b28-139">The second function is configured to take advantage of the nullability propagation optimization, providing more information on how the function behaves around null parameters.</span></span>

<span data-ttu-id="d7b28-140">Die folgenden Abfragen:</span><span class="sxs-lookup"><span data-stu-id="d7b28-140">When issuing the following queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#NullabilityPropagationExamples)]

<span data-ttu-id="d7b28-141">Ergeben den folgenden SQL-Code:</span><span class="sxs-lookup"><span data-stu-id="d7b28-141">We get this SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR [dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) IS NULL

SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR ([b].[Url] IS NULL OR [b].[Rating] IS NULL)
```

<span data-ttu-id="d7b28-142">Die zweite Abfrage muss die Funktion nicht selbst neu auswerten, um deren NULL-Zulässigkeit zu testen.</span><span class="sxs-lookup"><span data-stu-id="d7b28-142">The second query doesn't need to re-evaluate the function itself to test its nullability.</span></span>

> [!NOTE]
> <span data-ttu-id="d7b28-143">Diese Optimierung sollte nur eingesetzt werden, wenn die Funktion nur `null` zurückgeben kann, wenn ihre Parameter `null` sind.</span><span class="sxs-lookup"><span data-stu-id="d7b28-143">This optimization should only be used if the function can only return `null` when it's parameters are `null`.</span></span>

## <a name="mapping-a-queryable-function-to-a-table-valued-function"></a><span data-ttu-id="d7b28-144">Zuordnen einer abfragbaren Funktion zu einer Tabellenwertfunktion</span><span class="sxs-lookup"><span data-stu-id="d7b28-144">Mapping a queryable function to a table-valued function</span></span>

<span data-ttu-id="d7b28-145">EF Core unterstützt auch das Zuordnen zu einer Tabellenwertfunktion mithilfe einer benutzerdefinierten CLR-Methode. Dabei wird eine `IQueryable`-Klasse der Entitätstypen zurückgegeben, was es EF Core ermöglicht, Tabellenwertfunktionen Parameter zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="d7b28-145">EF Core also supports mapping to a table-valued function using a user-defined CLR method returning an `IQueryable` of entity types, allowing EF Core to map TVFs with parameters.</span></span> <span data-ttu-id="d7b28-146">Der Prozess ähnelt dem Zuordnen einer benutzerdefinierten Skalarfunktion zu einer SQL-Funktion: Es wird eine Tabellenwertfunktion in der Datenbank benötigt, eine CLR-Funktion, die in den LINQ-Abfragen verwendet wird, sowie eine Zuordnung zwischen diesen beiden.</span><span class="sxs-lookup"><span data-stu-id="d7b28-146">The process is similar to mapping a scalar user-defined function to a SQL function: we need a TVF in the database, a CLR function that is used in the LINQ queries, and a mapping between the two.</span></span>

<span data-ttu-id="d7b28-147">Als Beispiel soll eine Tabellenwertfunktion verwendet werden, die alle Beiträge zurückgibt, die über mindestens einen Kommentar verfügen, der einen gesetzten Schwellenwert an „Gefällt mir“-Markierungen erreicht:</span><span class="sxs-lookup"><span data-stu-id="d7b28-147">As an example, we'll use a table-valued function that returns all posts having at least one comment that meets a given "Like" threshold:</span></span>

```sql
CREATE FUNCTION dbo.PostsWithPopularComments(@likeThreshold int)
RETURNS TABLE
AS
RETURN
(
    SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
    FROM [Posts] AS [p]
    WHERE (
        SELECT COUNT(*)
        FROM [Comments] AS [c]
        WHERE ([p].[PostId] = [c].[PostId]) AND ([c].[Likes] >= @likeThreshold)) > 0
)
```

<span data-ttu-id="d7b28-148">Die Signatur der CLR-Methode sieht folgendermaßen aus:</span><span class="sxs-lookup"><span data-stu-id="d7b28-148">The CLR method signature is as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionDefinition)]

> [!TIP]
> <span data-ttu-id="d7b28-149">Der `FromExpression`-Aufruf im CLR-Funktionskörper ermöglicht es, dass die Funktion anstelle der regulären DbSet-Klasse verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="d7b28-149">The `FromExpression` call in the CLR function body allows for the function to be used instead of a regular DbSet.</span></span>

<span data-ttu-id="d7b28-150">Unten sehen Sie die Zuordnung:</span><span class="sxs-lookup"><span data-stu-id="d7b28-150">And below is the mapping:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionConfigurationHasDbFunction)]

> [!CAUTION]
> <span data-ttu-id="d7b28-151">Solange das [Issue 23408](https://github.com/dotnet/efcore/issues/23408) nicht behoben ist, wird bei Zuordnungen zu einer `IQueryable`-Schnittstelle von Entitätstypen die Standardzuordnung zu einer Tabelle für die DbSet-Klasse überschrieben.</span><span class="sxs-lookup"><span data-stu-id="d7b28-151">Until [issue 23408](https://github.com/dotnet/efcore/issues/23408) is fixed, mapping to an `IQueryable` of entity types overrides the default mapping to a table for the DbSet.</span></span> <span data-ttu-id="d7b28-152">Falls erforderlich, z. B. wenn die Entität nicht schlüssellos ist, muss die Zuordnung zur Tabelle mithilfe der `ToTable`-Methode explizit angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="d7b28-152">If necessary - for example when the entity is not keyless - mapping to the table must be specified explicitly using `ToTable` method.</span></span>

> [!NOTE]
> <span data-ttu-id="d7b28-153">Abfragbare Funktionen müssen einer Tabellenwertfunktion zugeordnet werden und können `HasTranslation` nicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="d7b28-153">Queryable function must be mapped to a table-valued function and can't use of `HasTranslation`.</span></span>

<span data-ttu-id="d7b28-154">Sehen Sie sich die folgende Abfrage an, bei der die Funktion zugeordnet ist:</span><span class="sxs-lookup"><span data-stu-id="d7b28-154">When the function is mapped, the following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#TableValuedFunctionQuery)]

<span data-ttu-id="d7b28-155">Sie führt zu folgender Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="d7b28-155">Produces:</span></span>

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [dbo].[PostsWithPopularComments](@likeThreshold) AS [p]
ORDER BY [p].[Rating]
```
