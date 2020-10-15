---
title: Eigenschaften von Schatten und Indexer-EF Core
description: Konfigurieren von Schatten-und Indexereigenschaften in einem Entity Framework Core Modell
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: 417ab57a4a77ecf626e54eeca900744d84e3fe08
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063893"
---
# <a name="shadow-and-indexer-properties"></a><span data-ttu-id="42d6d-103">Eigenschaften von Schatten und Indexer</span><span class="sxs-lookup"><span data-stu-id="42d6d-103">Shadow and Indexer Properties</span></span>

<span data-ttu-id="42d6d-104">Schatten Eigenschaften sind Eigenschaften, die nicht in der .net-Entitäts Klasse definiert sind, aber für diesen Entitätstyp im EF Core Modell definiert sind.</span><span class="sxs-lookup"><span data-stu-id="42d6d-104">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="42d6d-105">Der Wert und der Status dieser Eigenschaften werden ausschließlich in der Änderungs Nachverfolgung beibehalten.</span><span class="sxs-lookup"><span data-stu-id="42d6d-105">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="42d6d-106">Schatten Eigenschaften sind nützlich, wenn Daten in der Datenbank vorhanden sind, die für die zugeordneten Entitäts Typen nicht verfügbar gemacht werden sollen.</span><span class="sxs-lookup"><span data-stu-id="42d6d-106">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="42d6d-107">Fremdschlüssel Schatten-Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="42d6d-107">Foreign key shadow properties</span></span>

<span data-ttu-id="42d6d-108">Schatten Eigenschaften werden am häufigsten für Fremdschlüssel Eigenschaften verwendet, bei denen die Beziehung zwischen zwei Entitäten durch einen Fremdschlüssel Wert in der Datenbank dargestellt wird, die Beziehung jedoch für die Entitäts Typen mithilfe von Navigations Eigenschaften zwischen den Entitäts Typen verwaltet wird.</span><span class="sxs-lookup"><span data-stu-id="42d6d-108">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="42d6d-109">Gemäß der Konvention führt EF eine Schatten Eigenschaft ein, wenn eine Beziehung entdeckt wird, aber in der abhängigen Entitäts Klasse keine Fremdschlüssel Eigenschaft gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="42d6d-109">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="42d6d-110">Die-Eigenschaft wird benannt `<navigation property name><principal key property name>` (die Navigation auf der abhängigen Entität, die auf die Prinzipal Entität verweist, wird für die Benennung verwendet).</span><span class="sxs-lookup"><span data-stu-id="42d6d-110">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="42d6d-111">Wenn der Name der Prinzipal Schlüsseleigenschaft den Namen der Navigations Eigenschaft enthält, ist der Name einfach `<principal key property name>` .</span><span class="sxs-lookup"><span data-stu-id="42d6d-111">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="42d6d-112">Wenn für die abhängige Entität keine Navigations Eigenschaft vorhanden ist, wird der Prinzipal Typname an seiner Stelle verwendet.</span><span class="sxs-lookup"><span data-stu-id="42d6d-112">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="42d6d-113">Beispielsweise führt das folgende Codebeispiel dazu, dass eine `BlogId` Schatten Eigenschaft in die- `Post` Entität eingefügt wird:</span><span class="sxs-lookup"><span data-stu-id="42d6d-113">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="42d6d-114">Konfigurieren von Schatten Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="42d6d-114">Configuring shadow properties</span></span>

<span data-ttu-id="42d6d-115">Sie können die fließende API verwenden, um Schatten Eigenschaften zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="42d6d-115">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="42d6d-116">Nachdem Sie die Zeichen folgen Überladung von aufgerufen haben `Property` , können Sie alle Konfigurations Aufrufe verketten, die für andere Eigenschaften gelten.</span><span class="sxs-lookup"><span data-stu-id="42d6d-116">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="42d6d-117">Im folgenden Beispiel `Blog` `LastUpdated` wird eine Schatten Eigenschaft erstellt, da keine CLR-Eigenschaft mit dem Namen hat:</span><span class="sxs-lookup"><span data-stu-id="42d6d-117">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="42d6d-118">Wenn der für die Methode angegebene Name mit `Property` dem Namen einer vorhandenen Eigenschaft übereinstimmt (eine Schatten Eigenschaft oder eine, die für die Entitäts Klasse definiert ist), konfiguriert der Code diese vorhandene Eigenschaft, anstatt eine neue Schatten Eigenschaft einzuführen.</span><span class="sxs-lookup"><span data-stu-id="42d6d-118">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="42d6d-119">Zugreifen auf Schatten Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="42d6d-119">Accessing shadow properties</span></span>

<span data-ttu-id="42d6d-120">Schatten Eigenschaftswerte können über die API abgerufen und geändert werden `ChangeTracker` :</span><span class="sxs-lookup"><span data-stu-id="42d6d-120">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="42d6d-121">Auf Schatten Eigenschaften kann in LINQ-Abfragen über die `EF.Property` statische Methode verwiesen werden:</span><span class="sxs-lookup"><span data-stu-id="42d6d-121">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="42d6d-122">Auf Schatten Eigenschaften kann nicht nach einer Abfrage ohne Nachverfolgung zugegriffen werden, da die zurückgegebenen Entitäten nicht von der Änderungs Nachverfolgung nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="42d6d-122">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>

## <a name="property-bag-entity-types"></a><span data-ttu-id="42d6d-123">Eigenschaften Behälter-Entitäts Typen</span><span class="sxs-lookup"><span data-stu-id="42d6d-123">Property bag entity types</span></span>

> [!NOTE]
> <span data-ttu-id="42d6d-124">Unterstützung für Eigenschaften Behälter-Entitäts Typen wurde in EF Core 5,0 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="42d6d-124">Support for Property bag entity types was added in EF Core 5.0.</span></span>

<span data-ttu-id="42d6d-125">Entitäts Typen, die nur Indexer-Eigenschaften enthalten, werden als Entitäts Typen für Eigenschaften Behälter bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="42d6d-125">Entity types that contain only indexer properties are known as property bag entity types.</span></span> <span data-ttu-id="42d6d-126">Diese Entitäts Typen haben keine Schatten Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="42d6d-126">These entity types don't have shadow properties.</span></span> <span data-ttu-id="42d6d-127">Derzeit `Dictionary<string, object>` wird nur als Entitätstyp für Eigenschaften Behälter unterstützt.</span><span class="sxs-lookup"><span data-stu-id="42d6d-127">Currently only `Dictionary<string, object>` is supported as a property bag entity type.</span></span> <span data-ttu-id="42d6d-128">Dies bedeutet, dass Sie als frei gegebener Entitätstyp mit einem eindeutigen Namen konfiguriert werden muss, und die entsprechende `DbSet` Eigenschaft muss mithilfe eines- `Set` Aufrufes implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="42d6d-128">This means that it must be configured as a shared entity type with a unique name and the corresponding `DbSet` property must be implemented using a `Set` call.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]