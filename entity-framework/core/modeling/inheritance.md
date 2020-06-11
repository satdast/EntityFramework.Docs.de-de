---
title: Vererbung-EF Core
description: Konfigurieren der Vererbung von Entitäts Typen mit Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 10/27/2016
uid: core/modeling/inheritance
ms.openlocfilehash: 0e94013a0b894b162f4bb3ca8e7acb1aca349011
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664051"
---
# <a name="inheritance"></a><span data-ttu-id="6b624-103">Vererbung</span><span class="sxs-lookup"><span data-stu-id="6b624-103">Inheritance</span></span>

<span data-ttu-id="6b624-104">EF kann eine .net-Typhierarchie einer Datenbank zuordnen.</span><span class="sxs-lookup"><span data-stu-id="6b624-104">EF can map a .NET type hierarchy to a database.</span></span> <span data-ttu-id="6b624-105">Dies ermöglicht es Ihnen, Ihre .NET-Entitäten wie gewohnt mit Basis-und abgeleiteten Typen in Code zu schreiben und EF nahtlos das geeignete Datenbankschema zu erstellen, Abfragen auszugeben usw. Die tatsächlichen Details, wie eine Typhierarchie zugeordnet wird, sind Anbieter abhängig. Diese Seite beschreibt die Vererbungs Unterstützung im Kontext einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="6b624-105">This allows you to write your .NET entities in code as usual, using base and derived types, and have EF seamlessly create the appropriate database schema, issue queries, etc. The actual details of how a type hierarchy is mapped are provider-dependent; this page describes inheritance support in the context of a relational database.</span></span>

<span data-ttu-id="6b624-106">Zurzeit unterstützt EF Core nur das TPH-Muster (Table-per Hierarchy).</span><span class="sxs-lookup"><span data-stu-id="6b624-106">At the moment, EF Core only supports the table-per-hierarchy (TPH) pattern.</span></span> <span data-ttu-id="6b624-107">TPH verwendet eine einzelne Tabelle zum Speichern der Daten für alle Typen in der Hierarchie, und eine diskriminatorspalte wird verwendet, um den Typ zu identifizieren, den jede Zeile darstellt.</span><span class="sxs-lookup"><span data-stu-id="6b624-107">TPH uses a single table to store the data for all types in the hierarchy, and a discriminator column is used to identify which type each row represents.</span></span>

> [!NOTE]
> <span data-ttu-id="6b624-108">Die Tabelle pro Typ (TPT) und "Table-per-Concrete-Type" (TPC), die von EF6 unterstützt werden, werden von EF Core noch nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6b624-108">The table-per-type (TPT) and table-per-concrete-type (TPC), which are supported by EF6, are not yet supported by EF Core.</span></span> <span data-ttu-id="6b624-109">TPT ist ein wichtiges Feature, das für EF Core 5,0 geplant ist.</span><span class="sxs-lookup"><span data-stu-id="6b624-109">TPT is a major feature planned for EF Core 5.0.</span></span>

## <a name="entity-type-hierarchy-mapping"></a><span data-ttu-id="6b624-110">Entitätstyp-Hierarchie Zuordnung</span><span class="sxs-lookup"><span data-stu-id="6b624-110">Entity type hierarchy mapping</span></span>

<span data-ttu-id="6b624-111">Gemäß der Konvention scannt EF nicht automatisch nach Basis-oder abgeleiteten Typen. Dies bedeutet, dass Sie den Typ für das Modell explizit angeben müssen, wenn Sie möchten, dass ein CLR-Typ in der Hierarchie zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="6b624-111">By convention, EF will not automatically scan for base or derived types; this means that if you want a CLR type in your hierarchy to be mapped, you must explicitly specify that type on your model.</span></span> <span data-ttu-id="6b624-112">Wenn Sie z. b. nur den Basistyp einer Hierarchie angeben, bewirkt dies nicht, dass EF Core implizit alle seine Untertypen einschließt.</span><span class="sxs-lookup"><span data-stu-id="6b624-112">For example, specifying only the base type of a hierarchy will not cause EF Core to implicitly include all of its sub-types.</span></span>

<span data-ttu-id="6b624-113">Im folgenden Beispiel wird ein dbset für `Blog` und seine Unterklasse verfügbar gemacht `RssBlog` .</span><span class="sxs-lookup"><span data-stu-id="6b624-113">The following sample exposes a DbSet for `Blog` and its subclass `RssBlog`.</span></span> <span data-ttu-id="6b624-114">Wenn `Blog` eine andere Unterklasse aufweist, wird Sie nicht in das Modell eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="6b624-114">If `Blog` has any other subclass, it will not be included in the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

<span data-ttu-id="6b624-115">Dieses Modell wird dem folgenden Datenbankschema zugeordnet (Beachten Sie die implizit erstellte *diskriminatorspalte* , die angibt, welcher Typ von *Blog* in den einzelnen Zeilen gespeichert wird):</span><span class="sxs-lookup"><span data-stu-id="6b624-115">This model is mapped to the following database schema (note the implicitly-created *Discriminator* column, which identifies which type of *Blog* is stored in each row):</span></span>

![image](_static/inheritance-tph-data.png)

>[!NOTE]
> <span data-ttu-id="6b624-117">Bei Verwendung der TPH-Zuordnung werden bei Bedarf automatisch NULL-Werte für Daten Bank Spalten festgelegt.</span><span class="sxs-lookup"><span data-stu-id="6b624-117">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span> <span data-ttu-id="6b624-118">Beispielsweise kann die Spalte *rssurl* auf NULL festgelegt werden, da reguläre *Blog* Instanzen nicht über diese Eigenschaft verfügen.</span><span class="sxs-lookup"><span data-stu-id="6b624-118">For example, the *RssUrl* column is nullable because regular *Blog* instances do not have that property.</span></span>

<span data-ttu-id="6b624-119">Wenn Sie kein dbset für eine oder mehrere Entitäten in der Hierarchie verfügbar machen möchten, können Sie auch die fließende API verwenden, um sicherzustellen, dass Sie im Modell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="6b624-119">If you don't want to expose a DbSet for one or more entities in the hierarchy, you can also use the Fluent API to ensure they are included in the model.</span></span>

> [!TIP]
> <span data-ttu-id="6b624-120">Wenn Sie sich nicht auf Konventionen verlassen, können Sie den Basistyp explizit mithilfe von angeben `HasBaseType` .</span><span class="sxs-lookup"><span data-stu-id="6b624-120">If you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span> <span data-ttu-id="6b624-121">Sie können auch verwenden `.HasBaseType((Type)null)` , um einen Entitätstyp aus der Hierarchie zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="6b624-121">You can also use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="discriminator-configuration"></a><span data-ttu-id="6b624-122">Diskriminatorkonfiguration</span><span class="sxs-lookup"><span data-stu-id="6b624-122">Discriminator configuration</span></span>

<span data-ttu-id="6b624-123">Sie können den Namen und den Typ der diskriminatorspalte und die Werte, die zum Identifizieren der einzelnen Typen in der Hierarchie verwendet werden, konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="6b624-123">You can configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

<span data-ttu-id="6b624-124">In den obigen Beispielen wurde der Diskriminator von EF implizit als [Schatten Eigenschaft](xref:core/modeling/shadow-properties) für die Basis Entität der Hierarchie hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6b624-124">In the examples above, EF added the discriminator implicitly as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="6b624-125">Diese Eigenschaft kann wie jede andere konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="6b624-125">This property can be configured like any other:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

<span data-ttu-id="6b624-126">Schließlich kann der Diskriminator auch einer regulären .net-Eigenschaft in der Entität zugeordnet werden:</span><span class="sxs-lookup"><span data-stu-id="6b624-126">Finally, the discriminator can also be mapped to a regular .NET property in your entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

## <a name="shared-columns"></a><span data-ttu-id="6b624-127">Freigegebene Spalten</span><span class="sxs-lookup"><span data-stu-id="6b624-127">Shared columns</span></span>

<span data-ttu-id="6b624-128">Wenn zwei gleich geordnete Entitäts Typen in der Hierarchie über eine Eigenschaft mit demselben Namen verfügen, werden Sie standardmäßig zwei separaten Spalten zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="6b624-128">By default, when two sibling entity types in the hierarchy have a property with the same name, they will be mapped to two separate columns.</span></span> <span data-ttu-id="6b624-129">Wenn Ihr Typ jedoch identisch ist, können Sie derselben Daten Bank Spalte zugeordnet werden:</span><span class="sxs-lookup"><span data-stu-id="6b624-129">However, if their type is identical they can be mapped to the same database column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]
