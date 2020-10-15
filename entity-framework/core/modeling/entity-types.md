---
title: Entitäts Typen-EF Core
description: Konfigurieren und Zuordnen von Entitäts Typen mithilfe von Entity Framework Core
author: roji
ms.date: 10/06/2020
uid: core/modeling/entity-types
ms.openlocfilehash: bfefa29c08679a1524c00769b3495d75a301e2d3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062229"
---
# <a name="entity-types"></a><span data-ttu-id="4df52-103">Entitätstypen</span><span class="sxs-lookup"><span data-stu-id="4df52-103">Entity Types</span></span>

<span data-ttu-id="4df52-104">Das Einschließen eines dbsets eines Typs in den Kontext bedeutet, dass es in EF Core Modell enthalten ist. Normalerweise wird ein solcher Typ als *Entität*bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="4df52-104">Including a DbSet of a type on your context means that it is included in EF Core's model; we usually refer to such a type as an *entity*.</span></span> <span data-ttu-id="4df52-105">EF Core können Entitäts Instanzen aus der Datenbank lesen und in diese schreiben. Wenn Sie eine relationale Datenbank verwenden, können EF Core Tabellen für Ihre Entitäten über Migrationen erstellen.</span><span class="sxs-lookup"><span data-stu-id="4df52-105">EF Core can read and write entity instances from/to the database, and if you're using a relational database, EF Core can create tables for your entities via migrations.</span></span>

## <a name="including-types-in-the-model"></a><span data-ttu-id="4df52-106">Einschließen von Typen im Modell</span><span class="sxs-lookup"><span data-stu-id="4df52-106">Including types in the model</span></span>

<span data-ttu-id="4df52-107">Gemäß der Konvention sind Typen, die in dbset-Eigenschaften für den Kontext verfügbar gemacht werden, im Modell als Entitäten enthalten.</span><span class="sxs-lookup"><span data-stu-id="4df52-107">By convention, types that are exposed in DbSet properties on your context are included in the model as entities.</span></span> <span data-ttu-id="4df52-108">In der-Methode angegebene Entitäts Typen `OnModelCreating` werden ebenfalls eingeschlossen, ebenso wie alle Typen, die gefunden werden, indem die Navigations Eigenschaften anderer ermittelter Entitäts Typen rekursiv untersucht werden.</span><span class="sxs-lookup"><span data-stu-id="4df52-108">Entity types that are specified in the `OnModelCreating` method are also included, as are any types that are found by recursively exploring the navigation properties of other discovered entity types.</span></span>

<span data-ttu-id="4df52-109">Im folgenden Codebeispiel sind alle Typen enthalten:</span><span class="sxs-lookup"><span data-stu-id="4df52-109">In the code sample below, all types are included:</span></span>

* <span data-ttu-id="4df52-110">`Blog` ist enthalten, da es in einer dbset-Eigenschaft für den Kontext verfügbar gemacht wird.</span><span class="sxs-lookup"><span data-stu-id="4df52-110">`Blog` is included because it's exposed in a DbSet property on the context.</span></span>
* <span data-ttu-id="4df52-111">`Post` ist enthalten, da Sie über die- `Blog.Posts` Navigations Eigenschaft erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="4df52-111">`Post` is included because it's discovered via the `Blog.Posts` navigation property.</span></span>
* <span data-ttu-id="4df52-112">`AuditEntry` Da es in angegeben wird `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="4df52-112">`AuditEntry` because it is specified in `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a><span data-ttu-id="4df52-113">Ausschließen von Typen aus dem Modell</span><span class="sxs-lookup"><span data-stu-id="4df52-113">Excluding types from the model</span></span>

<span data-ttu-id="4df52-114">Wenn Sie nicht möchten, dass ein Typ im Modell enthalten ist, können Sie ihn ausschließen:</span><span class="sxs-lookup"><span data-stu-id="4df52-114">If you don't want a type to be included in the model, you can exclude it:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="4df52-115">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="4df52-115">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="4df52-116">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="4df52-116">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

### <a name="excluding-from-migrations"></a><span data-ttu-id="4df52-117">Ausschließen von Migrationen</span><span class="sxs-lookup"><span data-stu-id="4df52-117">Excluding from migrations</span></span>

> [!NOTE]
> <span data-ttu-id="4df52-118">Die Möglichkeit, Tabellen aus Migrationen auszuschließen, wurde in EF Core 5,0 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4df52-118">The ability to exclude tables from migrations was added in EF Core 5.0.</span></span>

<span data-ttu-id="4df52-119">Es ist manchmal hilfreich, den gleichen Entitätstyp in mehreren Typen zugeordnet zu haben `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="4df52-119">It is sometimes useful to have the same entity type mapped in multiple `DbContext` types.</span></span> <span data-ttu-id="4df52-120">Dies trifft vor allem dann zu, wenn [begrenzte Kontexte](https://www.martinfowler.com/bliki/BoundedContext.html)verwendet werden, für die es üblich ist, `DbContext` für jeden Kontext Kontext einen anderen Typ zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="4df52-120">This is especially true when using [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html), for which it is common to have a different `DbContext` type for each bounded context.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs?name=TableExcludeFromMigrations&highlight=4)]

<span data-ttu-id="4df52-121">Bei dieser Konfigurations Migration wird die Tabelle nicht erstellt `blogs` , Sie `Blog` ist jedoch noch im Modell enthalten und kann normal verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4df52-121">With this configuration migrations will not create the `blogs` table, but `Blog` is still included in the model and can be used normally.</span></span>

<span data-ttu-id="4df52-122">Wenn Sie erneut mit der Verwaltung der Tabelle beginnen müssen, sollte eine neue Migration erstellt werden, bei der `blogs` nicht ausgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="4df52-122">If you need to start managing the table using migrations again then a new migration should be created where `blogs` is not excluded.</span></span> <span data-ttu-id="4df52-123">Die nächste Migration enthält jetzt alle Änderungen, die an der Tabelle vorgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="4df52-123">The next migration will now contain any changes made to the table.</span></span>

## <a name="table-name"></a><span data-ttu-id="4df52-124">Tabellenname</span><span class="sxs-lookup"><span data-stu-id="4df52-124">Table name</span></span>

<span data-ttu-id="4df52-125">Gemäß der Konvention wird jeder Entitätstyp so eingerichtet, dass er einer Datenbanktabelle mit dem gleichen Namen wie die dbset-Eigenschaft zugeordnet wird, die die Entität verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="4df52-125">By convention, each entity type will be set up to map to a database table with the same name as the DbSet property that exposes the entity.</span></span> <span data-ttu-id="4df52-126">Wenn kein dbset für die angegebene Entität vorhanden ist, wird der Klassenname verwendet.</span><span class="sxs-lookup"><span data-stu-id="4df52-126">If no DbSet exists for the given entity, the class name is used.</span></span>

<span data-ttu-id="4df52-127">Sie können den Tabellennamen manuell konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="4df52-127">You can manually configure the table name:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="4df52-128">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="4df52-128">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="4df52-129">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="4df52-129">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

***

## <a name="table-schema"></a><span data-ttu-id="4df52-130">Tabellenschema</span><span class="sxs-lookup"><span data-stu-id="4df52-130">Table schema</span></span>

<span data-ttu-id="4df52-131">Wenn Sie eine relationale Datenbank verwenden, werden Tabellen gemäß der Konvention im Standardschema Ihrer Datenbank erstellt.</span><span class="sxs-lookup"><span data-stu-id="4df52-131">When using a relational database, tables are by convention created in your database's default schema.</span></span> <span data-ttu-id="4df52-132">Beispielsweise wird Microsoft SQL Server das Schema verwenden `dbo` (SQLite unterstützt keine Schemas).</span><span class="sxs-lookup"><span data-stu-id="4df52-132">For example, Microsoft SQL Server will use the `dbo` schema (SQLite does not support schemas).</span></span>

<span data-ttu-id="4df52-133">Tabellen, die in einem bestimmten Schema erstellt werden sollen, können wie folgt konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="4df52-133">You can configure tables to be created in a specific schema as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="4df52-134">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="4df52-134">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="4df52-135">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="4df52-135">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

***

<span data-ttu-id="4df52-136">Anstatt das Schema für jede Tabelle anzugeben, können Sie auch das Standardschema auf Modell Ebene mit der fließenden API definieren:</span><span class="sxs-lookup"><span data-stu-id="4df52-136">Rather than specifying the schema for each table, you can also define the default schema at the model level with the fluent API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

<span data-ttu-id="4df52-137">Beachten Sie, dass das Festlegen des Standard Schemas auch andere Datenbankobjekte, z. b. Sequenzen, beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="4df52-137">Note that setting the default schema will also affect other database objects, such as sequences.</span></span>

## <a name="view-mapping"></a><span data-ttu-id="4df52-138">Zuordnung anzeigen</span><span class="sxs-lookup"><span data-stu-id="4df52-138">View mapping</span></span>

<span data-ttu-id="4df52-139">Entitäts Typen können mithilfe der flüssigen API Daten Bank Sichten zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="4df52-139">Entity types can be mapped to database views using the Fluent API.</span></span>

> [!Note]
> <span data-ttu-id="4df52-140">EF nimmt an, dass die Sicht, auf die verwiesen wird, bereits in der Datenbank vorhanden ist, wird Sie in einer Migration nicht automatisch erstellt.</span><span class="sxs-lookup"><span data-stu-id="4df52-140">EF will assume that the referenced view already exists in the database, it will not create it automatically in a migration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ViewNameAndSchema.cs?name=ViewNameAndSchema&highlight=1)]

 <span data-ttu-id="4df52-141">Durch die Zuordnung zu einer Sicht wird die Standard Tabellen Zuordnung entfernt. der Entitätstyp kann jedoch auch explizit einer Tabelle zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="4df52-141">Mapping to a view will remove the default table mapping, but the entity type can also be mapped to a table explicitly.</span></span> <span data-ttu-id="4df52-142">In diesem Fall wird die Abfrage Zuordnung für Abfragen verwendet, und die Tabellen Zuordnung wird für Updates verwendet.</span><span class="sxs-lookup"><span data-stu-id="4df52-142">In this case the query mapping will be used for queries and the table mapping will be used for updates.</span></span>
