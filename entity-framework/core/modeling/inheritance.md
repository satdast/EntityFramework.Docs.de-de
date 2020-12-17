---
title: Vererbung-EF Core
description: Konfigurieren der Vererbung von Entitäts Typen mit Entity Framework Core
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/inheritance
ms.openlocfilehash: 11bd653a53767aa732790b1222da1beff8ad26a9
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635756"
---
# <a name="inheritance"></a><span data-ttu-id="e949f-103">Vererbung</span><span class="sxs-lookup"><span data-stu-id="e949f-103">Inheritance</span></span>

<span data-ttu-id="e949f-104">EF kann eine .net-Typhierarchie einer Datenbank zuordnen.</span><span class="sxs-lookup"><span data-stu-id="e949f-104">EF can map a .NET type hierarchy to a database.</span></span> <span data-ttu-id="e949f-105">Dies ermöglicht es Ihnen, Ihre .NET-Entitäten wie gewohnt mit Basis-und abgeleiteten Typen in Code zu schreiben und EF nahtlos das geeignete Datenbankschema zu erstellen, Abfragen auszugeben usw. Die tatsächlichen Details, wie eine Typhierarchie zugeordnet wird, sind Anbieter abhängig. Diese Seite beschreibt die Vererbungs Unterstützung im Kontext einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="e949f-105">This allows you to write your .NET entities in code as usual, using base and derived types, and have EF seamlessly create the appropriate database schema, issue queries, etc. The actual details of how a type hierarchy is mapped are provider-dependent; this page describes inheritance support in the context of a relational database.</span></span>

## <a name="entity-type-hierarchy-mapping"></a><span data-ttu-id="e949f-106">Entitätstyp-Hierarchie Zuordnung</span><span class="sxs-lookup"><span data-stu-id="e949f-106">Entity type hierarchy mapping</span></span>

<span data-ttu-id="e949f-107">Gemäß der Konvention scannt EF nicht automatisch nach Basis-oder abgeleiteten Typen. Dies bedeutet, dass Sie den Typ für das Modell explizit angeben müssen, wenn Sie möchten, dass ein CLR-Typ in der Hierarchie zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="e949f-107">By convention, EF will not automatically scan for base or derived types; this means that if you want a CLR type in your hierarchy to be mapped, you must explicitly specify that type on your model.</span></span> <span data-ttu-id="e949f-108">Wenn Sie z. b. nur den Basistyp einer Hierarchie angeben, bewirkt dies nicht, dass EF Core implizit alle seine Untertypen einschließt.</span><span class="sxs-lookup"><span data-stu-id="e949f-108">For example, specifying only the base type of a hierarchy will not cause EF Core to implicitly include all of its sub-types.</span></span>

<span data-ttu-id="e949f-109">Im folgenden Beispiel wird ein dbset für `Blog` und seine Unterklasse verfügbar gemacht `RssBlog` .</span><span class="sxs-lookup"><span data-stu-id="e949f-109">The following sample exposes a DbSet for `Blog` and its subclass `RssBlog`.</span></span> <span data-ttu-id="e949f-110">Wenn `Blog` eine andere Unterklasse aufweist, wird Sie nicht in das Modell eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="e949f-110">If `Blog` has any other subclass, it will not be included in the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

> [!NOTE]
> <span data-ttu-id="e949f-111">Bei Verwendung der TPH-Zuordnung werden bei Bedarf automatisch NULL-Werte für Daten Bank Spalten festgelegt.</span><span class="sxs-lookup"><span data-stu-id="e949f-111">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span> <span data-ttu-id="e949f-112">Beispielsweise kann die-Spalte auf NULL festgelegt werden, `RssUrl` da reguläre `Blog` Instanzen nicht über diese Eigenschaft verfügen.</span><span class="sxs-lookup"><span data-stu-id="e949f-112">For example, the `RssUrl` column is nullable because regular `Blog` instances do not have that property.</span></span>

<span data-ttu-id="e949f-113">Wenn Sie `DbSet` für eine oder mehrere Entitäten in der Hierarchie keinen verfügbar machen möchten, können Sie auch die fließende API verwenden, um sicherzustellen, dass Sie im Modell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="e949f-113">If you don't want to expose a `DbSet` for one or more entities in the hierarchy, you can also use the Fluent API to ensure they are included in the model.</span></span>

> [!TIP]
> <span data-ttu-id="e949f-114">Wenn Sie sich nicht auf Konventionen verlassen, können Sie den Basistyp explizit mithilfe von angeben `HasBaseType` .</span><span class="sxs-lookup"><span data-stu-id="e949f-114">If you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span> <span data-ttu-id="e949f-115">Sie können auch verwenden `.HasBaseType((Type)null)` , um einen Entitätstyp aus der Hierarchie zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="e949f-115">You can also use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="table-per-hierarchy-and-discriminator-configuration"></a><span data-ttu-id="e949f-116">Tabelle pro Hierarchie und diskriminatorkonfiguration</span><span class="sxs-lookup"><span data-stu-id="e949f-116">Table-per-hierarchy and discriminator configuration</span></span>

<span data-ttu-id="e949f-117">Standardmäßig ordnet EF die Vererbung mithilfe des TPH-Musters ( *Table-per Hierarchy* ) zu.</span><span class="sxs-lookup"><span data-stu-id="e949f-117">By default, EF maps the inheritance using the *table-per-hierarchy* (TPH) pattern.</span></span> <span data-ttu-id="e949f-118">TPH verwendet eine einzelne Tabelle zum Speichern der Daten für alle Typen in der Hierarchie, und eine diskriminatorspalte wird verwendet, um den Typ zu identifizieren, den jede Zeile darstellt.</span><span class="sxs-lookup"><span data-stu-id="e949f-118">TPH uses a single table to store the data for all types in the hierarchy, and a discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="e949f-119">Das obige Modell ist dem folgenden Datenbankschema zugeordnet (Beachten Sie die implizit erstellte `Discriminator` Spalte, die angibt, welcher Typ von `Blog` in den einzelnen Zeilen gespeichert ist).</span><span class="sxs-lookup"><span data-stu-id="e949f-119">The model above is mapped to the following database schema (note the implicitly created `Discriminator` column, which identifies which type of `Blog` is stored in each row).</span></span>

![Screenshot der Ergebnisse der Abfrage der Blog-Entitäts Hierarchie mithilfe eines "Tabelle pro Hierarchie"-Musters](_static/inheritance-tph-data.png)

<span data-ttu-id="e949f-121">Sie können den Namen und den Typ der diskriminatorspalte und die Werte, die zum Identifizieren der einzelnen Typen in der Hierarchie verwendet werden, konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="e949f-121">You can configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

<span data-ttu-id="e949f-122">In den obigen Beispielen wurde der Diskriminator von EF implizit als [Schatten Eigenschaft](xref:core/modeling/shadow-properties) für die Basis Entität der Hierarchie hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e949f-122">In the examples above, EF added the discriminator implicitly as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="e949f-123">Diese Eigenschaft kann wie jede andere konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="e949f-123">This property can be configured like any other:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

<span data-ttu-id="e949f-124">Schließlich kann der Diskriminator auch einer regulären .net-Eigenschaft in der Entität zugeordnet werden:</span><span class="sxs-lookup"><span data-stu-id="e949f-124">Finally, the discriminator can also be mapped to a regular .NET property in your entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

<span data-ttu-id="e949f-125">Beim Abfragen abgeleiteter Entitäten, die das TPH-Muster verwenden, fügt EF Core in der Abfrage ein Prädikat für die diskriminatorspalte hinzu.</span><span class="sxs-lookup"><span data-stu-id="e949f-125">When querying for derived entities, which use the TPH pattern, EF Core adds a predicate over discriminator column in the query.</span></span> <span data-ttu-id="e949f-126">Dieser Filter stellt sicher, dass keine zusätzlichen Zeilen für Basis Typen oder gleich geordnete Typen, die nicht im Ergebnis sind, angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="e949f-126">This filter makes sure that we don't get any additional rows for base types or sibling types not in the result.</span></span> <span data-ttu-id="e949f-127">Dieses Filter Prädikat wird für den Basis Entitätstyp übersprungen, da Abfragen für die Basis Entität Ergebnisse für alle Entitäten in der Hierarchie erhalten.</span><span class="sxs-lookup"><span data-stu-id="e949f-127">This filter predicate is skipped for the base entity type since querying for the base entity will get results for all the entities in the hierarchy.</span></span> <span data-ttu-id="e949f-128">Wenn beim Materialisieren der Ergebnisse aus einer Abfrage ein Diskriminatorwert vorhanden ist, der keinem Entitätstyp im Modell zugeordnet ist, wird eine Ausnahme ausgelöst, da wir nicht wissen, wie die Ergebnisse materialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="e949f-128">When materializing results from a query, if we come across a discriminator value, which isn't mapped to any entity type in the model, we throw an exception since we don't know how to materialize the results.</span></span> <span data-ttu-id="e949f-129">Dieser Fehler tritt nur auf, wenn die Datenbank Zeilen mit diskriminatorwerten enthält, die im EF-Modell nicht zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="e949f-129">This error only occurs if your database contains rows with discriminator values, which aren't mapped in the EF model.</span></span> <span data-ttu-id="e949f-130">Wenn Sie solche Daten haben, können Sie die diskriminatorzuordnung in EF Core Modell als unvollständig kennzeichnen, um anzugeben, dass wir immer ein Filter Prädikat zum Abfragen beliebiger Typen in der Hierarchie hinzufügen sollten.</span><span class="sxs-lookup"><span data-stu-id="e949f-130">If you have such data, then you can mark the discriminator mapping in EF Core model as incomplete to indicate that we should always add filter predicate for querying any type in the hierarchy.</span></span> <span data-ttu-id="e949f-131">`IsComplete(false)` der-Befehl für die diskriminatorkonfiguration markiert die Zuordnung als unvollständig.</span><span class="sxs-lookup"><span data-stu-id="e949f-131">`IsComplete(false)` call on the discriminator configuration marks the mapping to be incomplete.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorMappingIncomplete.cs?name=DiscriminatorMappingIncomplete&highlight=5)]

### <a name="shared-columns"></a><span data-ttu-id="e949f-132">Freigegebene Spalten</span><span class="sxs-lookup"><span data-stu-id="e949f-132">Shared columns</span></span>

<span data-ttu-id="e949f-133">Wenn zwei gleich geordnete Entitäts Typen in der Hierarchie über eine Eigenschaft mit demselben Namen verfügen, werden Sie standardmäßig zwei separaten Spalten zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="e949f-133">By default, when two sibling entity types in the hierarchy have a property with the same name, they will be mapped to two separate columns.</span></span> <span data-ttu-id="e949f-134">Wenn Ihr Typ jedoch identisch ist, können Sie derselben Daten Bank Spalte zugeordnet werden:</span><span class="sxs-lookup"><span data-stu-id="e949f-134">However, if their type is identical they can be mapped to the same database column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]

## <a name="table-per-type-configuration"></a><span data-ttu-id="e949f-135">"Tabelle pro Typ"-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="e949f-135">Table-per-type configuration</span></span>

> [!NOTE]
> <span data-ttu-id="e949f-136">Die Funktion "Tabelle pro Typ" (TPT) wurde in EF Core 5,0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="e949f-136">The table-per-type (TPT) feature was introduced in EF Core 5.0.</span></span> <span data-ttu-id="e949f-137">"Table-per-Concrete-Type" (TPC) wird von EF6 unterstützt, aber noch nicht von EF Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e949f-137">Table-per-concrete-type (TPC) is supported by EF6, but is not yet supported by EF Core.</span></span>

<span data-ttu-id="e949f-138">Im TPT-Zuordnungsmuster werden alle Typen einzelnen Tabellen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="e949f-138">In the TPT mapping pattern, all the types are mapped to individual tables.</span></span> <span data-ttu-id="e949f-139">Eigenschaften, die nur zu einem Basistyp oder einem abgeleiteten Typ gehören, werden in einer Tabelle gespeichert, die diesem Typ zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="e949f-139">Properties that belong solely to a base type or derived type are stored in a table that maps to that type.</span></span> <span data-ttu-id="e949f-140">Tabellen, die abgeleiteten Typen zugeordnet sind, speichern auch einen Fremdschlüssel, der die abgeleitete Tabelle mit der Basistabelle verbindet.</span><span class="sxs-lookup"><span data-stu-id="e949f-140">Tables that map to derived types also store a foreign key that joins the derived table with the base table.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=TPTConfiguration)]

<span data-ttu-id="e949f-141">EF erstellt das folgende Datenbankschema für das obige Modell.</span><span class="sxs-lookup"><span data-stu-id="e949f-141">EF will create the following database schema for the model above.</span></span>

```sql
CREATE TABLE [Blogs] (
    [BlogId] int NOT NULL IDENTITY,
    [Url] nvarchar(max) NULL,
    CONSTRAINT [PK_Blogs] PRIMARY KEY ([BlogId])
);

CREATE TABLE [RssBlogs] (
    [BlogId] int NOT NULL,
    [RssUrl] nvarchar(max) NULL,
    CONSTRAINT [PK_RssBlogs] PRIMARY KEY ([BlogId]),
    CONSTRAINT [FK_RssBlogs_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([BlogId]) ON DELETE NO ACTION
);
```

> [!NOTE]
> <span data-ttu-id="e949f-142">Wenn die PRIMARY KEY-Einschränkung umbenannt wird, wird der neue Name auf alle Tabellen angewendet, die der Hierarchie zugeordnet sind. in zukünftigen EF-Versionen wird das Umbenennen der Einschränkung nur für eine bestimmte Tabelle ermöglicht, wenn das [Problem 19970](https://github.com/dotnet/efcore/issues/19970) behoben ist.</span><span class="sxs-lookup"><span data-stu-id="e949f-142">If the primary key constraint is renamed the new name will be applied to all the tables mapped to the hierarchy, future EF versions will allow renaming the constraint only for a particular table when [issue 19970](https://github.com/dotnet/efcore/issues/19970) is fixed.</span></span>

<span data-ttu-id="e949f-143">Wenn Sie die Massen Konfiguration verwenden, können Sie den Spaltennamen für eine bestimmte Tabelle abrufen, indem Sie aufrufen <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)> .</span><span class="sxs-lookup"><span data-stu-id="e949f-143">If you are employing bulk configuration you can retrieve the column name for a specific table by calling <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)>.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=Metadata&highlight=10)]

> [!WARNING]
> <span data-ttu-id="e949f-144">In vielen Fällen zeigt TPT im Vergleich zu TPH eine geringere Leistung.</span><span class="sxs-lookup"><span data-stu-id="e949f-144">In many cases, TPT shows inferior performance when compared to TPH.</span></span> <span data-ttu-id="e949f-145">[Weitere Informationen finden](xref:core/performance/modeling-for-performance#inheritance-mapping)Sie in der Dokumentation zur Leistung.</span><span class="sxs-lookup"><span data-stu-id="e949f-145">[See the performance docs for more information](xref:core/performance/modeling-for-performance#inheritance-mapping).</span></span>
