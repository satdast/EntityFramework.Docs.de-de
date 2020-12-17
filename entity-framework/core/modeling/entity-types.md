---
title: Entitäts Typen-EF Core
description: Konfigurieren und Zuordnen von Entitäts Typen mithilfe von Entity Framework Core
author: roji
ms.date: 10/06/2020
uid: core/modeling/entity-types
ms.openlocfilehash: ca8cb8560afe374218e763bc0476839187a40ece
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635769"
---
# <a name="entity-types"></a><span data-ttu-id="5f6ef-103">Entitätstypen</span><span class="sxs-lookup"><span data-stu-id="5f6ef-103">Entity Types</span></span>

<span data-ttu-id="5f6ef-104">Das Einschließen eines dbsets eines Typs in den Kontext bedeutet, dass es in EF Core Modell enthalten ist. Normalerweise wird ein solcher Typ als *Entität* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="5f6ef-105">EF Core können Entitäts Instanzen aus der Datenbank lesen und in diese schreiben. Wenn Sie eine relationale Datenbank verwenden, können EF Core Tabellen für Ihre Entitäten über Migrationen erstellen.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="5f6ef-106">Einschließen von Typen im Modell</span><span class="sxs-lookup"><span data-stu-id="5f6ef-106">Including types in the model</span></span>

<span data-ttu-id="5f6ef-107">Gemäß der Konvention sind Typen, die in dbset-Eigenschaften für den Kontext verfügbar gemacht werden, im Modell als Entitäten enthalten.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="5f6ef-108">In der-Methode angegebene Entitäts Typen `OnModelCreating` werden ebenfalls eingeschlossen, ebenso wie alle Typen, die gefunden werden, indem die Navigations Eigenschaften anderer ermittelter Entitäts Typen rekursiv untersucht werden.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="5f6ef-109">Im folgenden Codebeispiel sind alle Typen enthalten:</span><span class="sxs-lookup"><span data-stu-id="5f6ef-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="5f6ef-110">`Blog` ist enthalten, da es in einer dbset-Eigenschaft für den Kontext verfügbar gemacht wird.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="5f6ef-111">`Post` ist enthalten, da Sie über die- `Blog.Posts` Navigations Eigenschaft erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="5f6ef-112">`AuditEntry` Da es in angegeben wird `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="5f6ef-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="5f6ef-113">Ausschließen von Typen aus dem Modell</span><span class="sxs-lookup"><span data-stu-id="5f6ef-113">Excluding types from the model</span></span>

<span data-ttu-id="5f6ef-114">Wenn Sie nicht möchten, dass ein Typ im Modell enthalten ist, können Sie ihn ausschließen:</span><span class="sxs-lookup"><span data-stu-id="5f6ef-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="5f6ef-115">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="5f6ef-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="5f6ef-116">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="5f6ef-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

### <a name="excluding-from-migrations"></a><span data-ttu-id="5f6ef-117">Ausschließen von Migrationen</span><span class="sxs-lookup"><span data-stu-id="5f6ef-117">Excluding from migrations</span></span>

> [!NOTE]
> <span data-ttu-id="5f6ef-118">Die Möglichkeit, Tabellen aus Migrationen auszuschließen, wurde in EF Core 5,0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-118">The ability to exclude tables from migrations was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="5f6ef-119">Es ist manchmal hilfreich, den gleichen Entitätstyp in mehreren Typen zugeordnet zu haben `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="5f6ef-119">It is sometimes useful to have the same entity type mapped in multiple `DbContext` types.</span></span> <span data-ttu-id="5f6ef-120">Dies trifft vor allem dann zu, wenn [begrenzte Kontexte](https://www.martinfowler.com/bliki/BoundedContext.html)verwendet werden, für die es üblich ist, `DbContext` für jeden Kontext Kontext einen anderen Typ zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-120">This is especially true when using [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html), for which it is common to have a different `DbContext` type for each bounded context.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs?name=TableExcludeFromMigrations&highlight=4)]

<span data-ttu-id="5f6ef-121">Bei dieser Konfigurations Migration wird die Tabelle nicht erstellt `AspNetUsers` , Sie `IdentityUser` ist jedoch noch im Modell enthalten und kann normal verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-121">With this configuration migrations will not create the `AspNetUsers` table, but `IdentityUser` is still included in the model and can be used normally.</span></span>

<span data-ttu-id="5f6ef-122">Wenn Sie erneut mit der Verwaltung der Tabelle beginnen müssen, sollte eine neue Migration erstellt werden, bei der `AspNetUsers` nicht ausgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-122">If you need to start managing the table using migrations again then a new migration should be created where `AspNetUsers` is not excluded.</span></span> <span data-ttu-id="5f6ef-123">Die nächste Migration enthält jetzt alle Änderungen, die an der Tabelle vorgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-123">The next migration will now contain any changes made to the table.</span></span>

## <a name="table-name"></a><span data-ttu-id="5f6ef-124">Tabellenname</span><span class="sxs-lookup"><span data-stu-id="5f6ef-124">Table name</span></span>

<span data-ttu-id="5f6ef-125">Gemäß der Konvention wird jeder Entitätstyp so eingerichtet, dass er einer Datenbanktabelle mit dem gleichen Namen wie die dbset-Eigenschaft zugeordnet wird, die die Entität verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-125">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="5f6ef-126">Wenn kein dbset für die angegebene Entität vorhanden ist, wird der Klassenname verwendet.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-126">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="5f6ef-127">Sie können den Tabellennamen manuell konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="5f6ef-127">You can manually configure the table name:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="5f6ef-128">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="5f6ef-128">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="5f6ef-129">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="5f6ef-129">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

<span data-ttu-id="5f6ef-130">\*\*_</span><span class="sxs-lookup"><span data-stu-id="5f6ef-130">\*\*_</span></span>

## <a name="table-schema"></a><span data-ttu-id="5f6ef-131">Tabellenschema</span><span class="sxs-lookup"><span data-stu-id="5f6ef-131">Table schema</span></span>

<span data-ttu-id="5f6ef-132">Wenn Sie eine relationale Datenbank verwenden, werden Tabellen gemäß der Konvention im Standardschema Ihrer Datenbank erstellt.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-132">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="5f6ef-133">Beispielsweise wird Microsoft SQL Server das Schema verwenden `dbo` (SQLite unterstützt keine Schemas).</span><span class="sxs-lookup"><span data-stu-id="5f6ef-133">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="5f6ef-134">Tabellen, die in einem bestimmten Schema erstellt werden sollen, können wie folgt konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="5f6ef-134">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="5f6ef-135">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="5f6ef-135">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="5f6ef-136">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="5f6ef-136">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

_*_

<span data-ttu-id="5f6ef-137">Anstatt das Schema für jede Tabelle anzugeben, können Sie auch das Standardschema auf Modell Ebene mit der fließenden API definieren:</span><span class="sxs-lookup"><span data-stu-id="5f6ef-137">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="5f6ef-138">Beachten Sie, dass das Festlegen des Standard Schemas auch andere Datenbankobjekte, z. b. Sequenzen, beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-138">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>

## <a name="view-mapping"></a><span data-ttu-id="5f6ef-139">Zuordnung anzeigen</span><span class="sxs-lookup"><span data-stu-id="5f6ef-139">View mapping</span></span>

<span data-ttu-id="5f6ef-140">Entitäts Typen können mithilfe der flüssigen API Daten Bank Sichten zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-140">Entity types can be mapped to database views using the Fluent API.</span></span>

> [!Note]
> <span data-ttu-id="5f6ef-141">EF nimmt an, dass die Sicht, auf die verwiesen wird, bereits in der Datenbank vorhanden ist, wird Sie in einer Migration nicht automatisch erstellt.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-141">EF will assume that the referenced view already exists in the database, it will not create it automatically in a migration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ViewNameAndSchema.cs?name=ViewNameAndSchema&highlight=1)]

 <span data-ttu-id="5f6ef-142">Durch die Zuordnung zu einer Sicht wird die Standard Tabellen Zuordnung entfernt. ab EF 5,0 kann der Entitätstyp jedoch auch explizit einer Tabelle zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-142">Mapping to a view will remove the default table mapping, but starting with EF 5.0 the entity type can also be mapped to a table explicitly.</span></span> <span data-ttu-id="5f6ef-143">In diesem Fall wird die Abfrage Zuordnung für Abfragen verwendet, und die Tabellen Zuordnung wird für Updates verwendet.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-143">In this case the query mapping will be used for queries and the table mapping will be used for updates.</span></span>

> [!TIP]
> <span data-ttu-id="5f6ef-144">Zum Testen von Entitäts Typen, die Ansichten zugeordnet sind, mithilfe des Speicher internen Anbieters, ordnen Sie Sie einer Abfrage über zu `ToInMemoryQuery` .</span><span class="sxs-lookup"><span data-stu-id="5f6ef-144">To test entity types mapped to views using the in-memory provider map them to a query via `ToInMemoryQuery`.</span></span> <span data-ttu-id="5f6ef-145">Weitere Informationen finden Sie unter [ausführbares Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) mit dieser Technik.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-145">See a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) using this technique for more details.</span></span>

## <a name="table-valued-function-mapping"></a><span data-ttu-id="5f6ef-146">Zuordnung von Tabellenwert Funktionen</span><span class="sxs-lookup"><span data-stu-id="5f6ef-146">Table-valued function mapping</span></span>

<span data-ttu-id="5f6ef-147">Es ist möglich, einen Entitätstyp einer Tabellenwert Funktion (TVF) anstatt einer Tabelle in der Datenbank zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-147">It's possible to map an entity type to a table-valued function (TVF) instead of a table in the database.</span></span> <span data-ttu-id="5f6ef-148">Um dies zu veranschaulichen, definieren wir eine andere Entität, die einen Blog mit mehreren Beiträgen darstellt.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-148">To illustrate this, let's define another entity that represents blog with multiple posts.</span></span> <span data-ttu-id="5f6ef-149">Im Beispiel ist die-Entität [Schlüssel](xref:core/modeling/keyless-entity-types), aber Sie muss nicht sein.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-149">In the example, the entity is [keyless](xref:core/modeling/keyless-entity-types), but it doesn't have to be.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#BlogWithMultiplePostsEntity)]

<span data-ttu-id="5f6ef-150">Erstellen Sie als nächstes die folgende Tabellenwert Funktion in der-Datenbank, die nur Blogs mit mehreren Beiträgen sowie die Anzahl von Beiträgen zurückgibt, die mit den einzelnen Blogs verknüpft sind:</span><span class="sxs-lookup"><span data-stu-id="5f6ef-150">Next, create the following table-valued function in the database, which returns only blogs with multiple posts as well as the number of posts associated with each of these blogs:</span></span>

```sql
CREATE FUNCTION dbo.BlogsWithMultiplePosts()
RETURNS TABLE
AS
RETURN
(
    SELECT b.Url, COUNT(p.BlogId) AS PostCount
    FROM Blogs AS b
    JOIN Posts AS p ON b.BlogId = p.BlogId
    GROUP BY b.BlogId, b.Url
    HAVING COUNT(p.BlogId) > 1
)
```

<span data-ttu-id="5f6ef-151">Nun kann die Entität `BlogWithMultiplePost` auf folgende Weise dieser Funktion zugeordnet werden:</span><span class="sxs-lookup"><span data-stu-id="5f6ef-151">Now, the entity `BlogWithMultiplePost` can be mapped to this function in a following way:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs#QueryableFunctionConfigurationToFunction)]

> [!NOTE]
> <span data-ttu-id="5f6ef-152">Um eine Entität einer Tabellenwert Funktion zuzuordnen, muss die Funktion parameterlos sein.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-152">In order to map an entity to a table-valued function the function must be parameterless.</span></span>

<span data-ttu-id="5f6ef-153">Konventionell werden die Entitäts Eigenschaften übereinstimmenden Spalten zugeordnet, die von der TVF zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-153">Conventionally the entity properties will be mapped to matching columns returned by the TVF.</span></span> <span data-ttu-id="5f6ef-154">Wenn die von TVF zurückgegebenen Spalten einen anderen Namen als die Entitäts Eigenschaft aufweisen, kann Sie mithilfe der-Methode konfiguriert werden `HasColumnName` , genau wie bei der Zuordnung zu einer regulären Tabelle.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-154">If the columns returned by TVF has different name than entity property then it can be configured using `HasColumnName` method, just like when mapping to a regular table.</span></span>

<span data-ttu-id="5f6ef-155">Wenn der Entitätstyp einer Tabellenwert Funktion zugeordnet ist, lautet die Abfrage:</span><span class="sxs-lookup"><span data-stu-id="5f6ef-155">When the entity type is mapped to a table-valued function, the query:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Program.cs#ToFunctionQuery)]

<span data-ttu-id="5f6ef-156">Dies generiert diese SQL-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="5f6ef-156">Produces the following SQL:</span></span>

```sql
SELECT [b].[Url], [b].[PostCount]
FROM [dbo].[BlogsWithMultiplePosts]() AS [b]
WHERE [b].[PostCount] > 3
```

## <a name="table-comments"></a><span data-ttu-id="5f6ef-157">Tabellen Kommentare</span><span class="sxs-lookup"><span data-stu-id="5f6ef-157">Table comments</span></span>

<span data-ttu-id="5f6ef-158">Sie können einen beliebigen Textkommentar festlegen, der für die Datenbanktabelle festgelegt wird, sodass Sie das Schema in der Datenbank dokumentieren können:</span><span class="sxs-lookup"><span data-stu-id="5f6ef-158">You can set an arbitrary text comment that gets set on the database table, allowing you to document your schema in the database:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="5f6ef-159">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="5f6ef-159">Data Annotations</span></span>](#tab/data-annotations)

> [!NOTE]
> <span data-ttu-id="5f6ef-160">Das Festlegen von Kommentaren über Daten Anmerkungen wurde in EF Core 5,0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="5f6ef-160">Setting comments via data annotations was introduced in EF Core 5.0.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableComment.cs?name=TableComment&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="5f6ef-161">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="5f6ef-161">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableComment.cs?name=TableComment&highlight=4)]

<span data-ttu-id="5f6ef-162">_\*\*</span><span class="sxs-lookup"><span data-stu-id="5f6ef-162">_\*\*</span></span>
