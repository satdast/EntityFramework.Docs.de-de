---
title: Eigenschaften von Schatten und Indexer-EF Core
description: Konfigurieren von Schatten-und Indexereigenschaften in einem Entity Framework Core Modell
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: 180478212b683a271d2519cc1a4c79be5d3f11b9
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503188"
---
# <a name="shadow-and-indexer-properties"></a><span data-ttu-id="482ba-103">Eigenschaften von Schatten und Indexer</span><span class="sxs-lookup"><span data-stu-id="482ba-103">Shadow and Indexer Properties</span></span>

<span data-ttu-id="482ba-104">Schatten Eigenschaften sind Eigenschaften, die nicht in der .net-Entitäts Klasse definiert sind, aber für diesen Entitätstyp im EF Core Modell definiert sind.</span><span class="sxs-lookup"><span data-stu-id="482ba-104">Shadow properties are properties that aren't defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="482ba-105">Der Wert und der Status dieser Eigenschaften werden ausschließlich in der Änderungs Nachverfolgung beibehalten.</span><span class="sxs-lookup"><span data-stu-id="482ba-105">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="482ba-106">Schatten Eigenschaften sind nützlich, wenn Daten in der Datenbank vorhanden sind, die für die zugeordneten Entitäts Typen nicht verfügbar gemacht werden sollen.</span><span class="sxs-lookup"><span data-stu-id="482ba-106">Shadow properties are useful when there's data in the database that shouldn't be exposed on the mapped entity types.</span></span>

<span data-ttu-id="482ba-107">Indexer-Eigenschaften sind Entitätstyp Eigenschaften, die von einem [Indexer](/dotnet/csharp/programming-guide/indexers/) in der .net-Entitäts Klasse unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="482ba-107">Indexer properties are entity type properties, which are backed by an [indexer](/dotnet/csharp/programming-guide/indexers/) in .NET entity class.</span></span> <span data-ttu-id="482ba-108">Auf Sie kann mithilfe des Indexers auf den .NET-Klassen Instanzen zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="482ba-108">They can be accessed using the indexer on the .NET class instances.</span></span> <span data-ttu-id="482ba-109">Außerdem können Sie zusätzliche Eigenschaften zum Entitätstyp hinzufügen, ohne die CLR-Klasse zu ändern.</span><span class="sxs-lookup"><span data-stu-id="482ba-109">It also allows you to add additional properties to the entity type without changing the CLR class.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="482ba-110">Fremdschlüssel Schatten-Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="482ba-110">Foreign key shadow properties</span></span>

<span data-ttu-id="482ba-111">Schatten Eigenschaften werden am häufigsten für Fremdschlüssel Eigenschaften verwendet, bei denen die Beziehung zwischen zwei Entitäten durch einen Fremdschlüssel Wert in der Datenbank dargestellt wird, die Beziehung jedoch für die Entitäts Typen mithilfe von Navigations Eigenschaften zwischen den Entitäts Typen verwaltet wird.</span><span class="sxs-lookup"><span data-stu-id="482ba-111">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="482ba-112">Gemäß der Konvention führt EF eine Schatten Eigenschaft ein, wenn eine Beziehung entdeckt wird, aber in der abhängigen Entitäts Klasse keine Fremdschlüssel Eigenschaft gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="482ba-112">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="482ba-113">Die-Eigenschaft wird benannt `<navigation property name><principal key property name>` (die Navigation auf der abhängigen Entität, die auf die Prinzipal Entität verweist, wird für die Benennung verwendet).</span><span class="sxs-lookup"><span data-stu-id="482ba-113">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="482ba-114">Wenn der Name der Prinzipal Schlüsseleigenschaft den Namen der Navigations Eigenschaft enthält, ist der Name einfach `<principal key property name>` .</span><span class="sxs-lookup"><span data-stu-id="482ba-114">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="482ba-115">Wenn für die abhängige Entität keine Navigations Eigenschaft vorhanden ist, wird der Prinzipal Typname an seiner Stelle verwendet.</span><span class="sxs-lookup"><span data-stu-id="482ba-115">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="482ba-116">Beispielsweise führt das folgende Codebeispiel dazu, dass eine `BlogId` Schatten Eigenschaft in die- `Post` Entität eingefügt wird:</span><span class="sxs-lookup"><span data-stu-id="482ba-116">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="482ba-117">Konfigurieren von Schatten Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="482ba-117">Configuring shadow properties</span></span>

<span data-ttu-id="482ba-118">Sie können die fließende API verwenden, um Schatten Eigenschaften zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="482ba-118">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="482ba-119">Nachdem Sie die Zeichen folgen Überladung von aufgerufen haben `Property` , können Sie alle Konfigurations Aufrufe verketten, die für andere Eigenschaften gelten.</span><span class="sxs-lookup"><span data-stu-id="482ba-119">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="482ba-120">Im folgenden Beispiel `Blog` `LastUpdated` wird eine Schatten Eigenschaft erstellt, da keine CLR-Eigenschaft mit dem Namen hat:</span><span class="sxs-lookup"><span data-stu-id="482ba-120">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="482ba-121">Wenn der für die Methode angegebene Name mit `Property` dem Namen einer vorhandenen Eigenschaft übereinstimmt (eine Schatten Eigenschaft oder eine, die für die Entitäts Klasse definiert ist), konfiguriert der Code diese vorhandene Eigenschaft, anstatt eine neue Schatten Eigenschaft einzuführen.</span><span class="sxs-lookup"><span data-stu-id="482ba-121">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="482ba-122">Zugreifen auf Schatten Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="482ba-122">Accessing shadow properties</span></span>

<span data-ttu-id="482ba-123">Schatten Eigenschaftswerte können über die API abgerufen und geändert werden `ChangeTracker` :</span><span class="sxs-lookup"><span data-stu-id="482ba-123">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="482ba-124">Auf Schatten Eigenschaften kann in LINQ-Abfragen über die `EF.Property` statische Methode verwiesen werden:</span><span class="sxs-lookup"><span data-stu-id="482ba-124">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="482ba-125">Auf Schatten Eigenschaften kann nicht nach einer Abfrage ohne Nachverfolgung zugegriffen werden, da die zurückgegebenen Entitäten nicht von der Änderungs Nachverfolgung nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="482ba-125">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>

## <a name="configuring-indexer-properties"></a><span data-ttu-id="482ba-126">Konfigurieren von Indexer-Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="482ba-126">Configuring indexer properties</span></span>

<span data-ttu-id="482ba-127">Sie können die fließende API verwenden, um Indexer-Eigenschaften zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="482ba-127">You can use the Fluent API to configure indexer properties.</span></span> <span data-ttu-id="482ba-128">Nachdem Sie die-Methode aufgerufen haben `IndexerProperty` , können Sie alle Konfigurations Aufrufe verketten, die Sie für andere Eigenschaften durchsuchen.</span><span class="sxs-lookup"><span data-stu-id="482ba-128">Once you've called the method `IndexerProperty`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="482ba-129">Im folgenden Beispiel `Blog` ist ein Indexer definiert, der zum Erstellen einer Indexer-Eigenschaft verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="482ba-129">In the following sample, `Blog` has an indexer defined and it will be used to create an indexer property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexerProperty.cs?name=ShadowProperty&highlight=3)]

<span data-ttu-id="482ba-130">Wenn der für die Methode angegebene Name mit `IndexerProperty` dem Namen einer vorhandenen Indexer-Eigenschaft übereinstimmt, wird diese vorhandene Eigenschaft vom Code konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="482ba-130">If the name supplied to the `IndexerProperty` method matches the name of an existing indexer property, then the code will configure that existing property.</span></span> <span data-ttu-id="482ba-131">Wenn der Entitätstyp über eine Eigenschaft verfügt, die von einer Eigenschaft in der Entitäts Klasse unterstützt wird, wird eine Ausnahme ausgelöst, da auf Indexer-Eigenschaften nur über den Indexer zugegriffen werden muss.</span><span class="sxs-lookup"><span data-stu-id="482ba-131">If the entity type has a property, which is backed by a property on the entity class, then an exception is thrown since indexer properties must only be accessed via the indexer.</span></span>

## <a name="property-bag-entity-types"></a><span data-ttu-id="482ba-132">Eigenschaften Behälter-Entitäts Typen</span><span class="sxs-lookup"><span data-stu-id="482ba-132">Property bag entity types</span></span>

> [!NOTE]
> <span data-ttu-id="482ba-133">Unterstützung für Eigenschaften Behälter-Entitäts Typen wurde in EF Core 5,0 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="482ba-133">Support for Property bag entity types was added in EF Core 5.0.</span></span>

<span data-ttu-id="482ba-134">Entitäts Typen, die nur Indexer-Eigenschaften enthalten, werden als Entitäts Typen für Eigenschaften Behälter bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="482ba-134">Entity types that contain only indexer properties are known as property bag entity types.</span></span> <span data-ttu-id="482ba-135">Diese Entitäts Typen haben keine Schatten Eigenschaften, stattdessen erstellt EF Indexer-Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="482ba-135">These entity types don't have shadow properties, instead EF will create indexer properties.</span></span> <span data-ttu-id="482ba-136">Derzeit `Dictionary<string, object>` wird nur als Entitätstyp für Eigenschaften Behälter unterstützt.</span><span class="sxs-lookup"><span data-stu-id="482ba-136">Currently only `Dictionary<string, object>` is supported as a property bag entity type.</span></span> <span data-ttu-id="482ba-137">Er muss als frei gegebener Entitätstyp mit einem eindeutigen Namen konfiguriert werden, und die entsprechende `DbSet` Eigenschaft muss mithilfe eines- `Set` Aufrufes implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="482ba-137">It must be configured as a shared entity type with a unique name and the corresponding `DbSet` property must be implemented using a `Set` call.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]
