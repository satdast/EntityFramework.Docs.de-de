---
title: Tabellen Aufteilung-EF Core
description: Konfigurieren der Tabellen Aufteilung mithilfe von Entity Framework Core
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/table-splitting
ms.openlocfilehash: 71d332ec3f500f48e12863c71ac44ce4d60699f0
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063035"
---
# <a name="table-splitting"></a><span data-ttu-id="20093-103">Tabellenaufteilung</span><span class="sxs-lookup"><span data-stu-id="20093-103">Table Splitting</span></span>

<span data-ttu-id="20093-104">EF Core ermöglicht das Zuordnen von zwei oder mehr Entitäten zu einer einzelnen Zeile.</span><span class="sxs-lookup"><span data-stu-id="20093-104">EF Core allows to map two or more entities to a single row.</span></span> <span data-ttu-id="20093-105">Dies wird als _Tabellen Aufteilung_ oder _Tabellen Freigabe_bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="20093-105">This is called _table splitting_ or _table sharing_.</span></span>

## <a name="configuration"></a><span data-ttu-id="20093-106">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="20093-106">Configuration</span></span>

<span data-ttu-id="20093-107">Zur Verwendung der Tabellen Aufteilung müssen die Entitäts Typen derselben Tabelle zugeordnet werden, die Primärschlüssel müssen denselben Spalten zugeordnet werden, und mindestens eine Beziehung muss zwischen dem Primärschlüssel eines Entitäts Typs und einem anderen in derselben Tabelle konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="20093-107">To use table splitting the entity types need to be mapped to the same table, have the primary keys mapped to the same columns and at least one relationship configured between the primary key of one entity type and another in the same table.</span></span>

<span data-ttu-id="20093-108">Ein häufiges Szenario für die Tabellen Aufteilung ist die Verwendung einer Teilmenge der Spalten in der Tabelle, um eine höhere Leistung oder Kapselung zu erzielen.</span><span class="sxs-lookup"><span data-stu-id="20093-108">A common scenario for table splitting is using only a subset of the columns in the table for greater performance or encapsulation.</span></span>

<span data-ttu-id="20093-109">In diesem Beispiel `Order` stellt eine Teilmenge von dar `DetailedOrder` .</span><span class="sxs-lookup"><span data-stu-id="20093-109">In this example `Order` represents a subset of `DetailedOrder`.</span></span>

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

<span data-ttu-id="20093-110">Zusätzlich zur erforderlichen Konfiguration wird aufgerufen `Property(o => o.Status).HasColumnName("Status")` , um `DetailedOrder.Status` derselben Spalte wie zuzuordnen `Order.Status` .</span><span class="sxs-lookup"><span data-stu-id="20093-110">In addition to the required configuration we call `Property(o => o.Status).HasColumnName("Status")` to map `DetailedOrder.Status` to the same column as `Order.Status`.</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting)]

> [!TIP]
> <span data-ttu-id="20093-111">Weitere Informationen finden Sie im [vollständigen Beispiel Projekt](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) .</span><span class="sxs-lookup"><span data-stu-id="20093-111">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) for more context.</span></span>

## <a name="usage"></a><span data-ttu-id="20093-112">Verwendung</span><span class="sxs-lookup"><span data-stu-id="20093-112">Usage</span></span>

<span data-ttu-id="20093-113">Das Speichern und Abfragen von Entitäten mithilfe von Tabellen Aufteilung erfolgt auf dieselbe Weise wie andere Entitäten:</span><span class="sxs-lookup"><span data-stu-id="20093-113">Saving and querying entities using table splitting is done in the same way as other entities:</span></span>

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="optional-dependent-entity"></a><span data-ttu-id="20093-114">Optionale abhängige Entität</span><span class="sxs-lookup"><span data-stu-id="20093-114">Optional dependent entity</span></span>

> [!NOTE]
> <span data-ttu-id="20093-115">Diese Funktion wurde in EF Core 3,0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="20093-115">This feature was introduced in EF Core 3.0.</span></span>

<span data-ttu-id="20093-116">Wenn sich alle Spalten, die von einer abhängigen Entität verwendet werden `NULL` , in der Datenbank befinden, wird beim Abfragen keine Instanz für Sie erstellt.</span><span class="sxs-lookup"><span data-stu-id="20093-116">If all of the columns used by a dependent entity are `NULL` in the database, then no instance for it will be created when queried.</span></span> <span data-ttu-id="20093-117">Dadurch kann eine optionale abhängige Entität modelliert werden, bei der die Beziehungs Eigenschaft für den Prinzipal NULL ist.</span><span class="sxs-lookup"><span data-stu-id="20093-117">This allows modeling an optional dependent entity, where the relationship property on the principal would be null.</span></span> <span data-ttu-id="20093-118">Beachten Sie, dass dies auch dann der Fall wäre, wenn alle Eigenschaften der abhängigen Eigenschaft optional sind und auf festgelegt sind `null` , was nicht erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="20093-118">Note that this would also happen if all of the dependent's properties are optional and set to `null`, which might not be expected.</span></span>

## <a name="concurrency-tokens"></a><span data-ttu-id="20093-119">Parallelitätstoken</span><span class="sxs-lookup"><span data-stu-id="20093-119">Concurrency tokens</span></span>

<span data-ttu-id="20093-120">Wenn einer der Entitäts Typen, die eine Tabelle gemeinsam nutzen, über ein Parallelitäts Token verfügt, muss dieser auch in allen anderen Entitäts Typen enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="20093-120">If any of the entity types sharing a table has a concurrency token then it must be included in all other entity types as well.</span></span> <span data-ttu-id="20093-121">Dies ist erforderlich, um einen veralteten Parallelitäts Token-Wert zu vermeiden, wenn nur eine Entität aktualisiert wird, die derselben Tabelle zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="20093-121">This is necessary in order to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="20093-122">Um zu vermeiden, dass das Parallelitäts Token für den verarbeitenden Code verfügbar gemacht wird, ist es möglich, eine als [Schatten Eigenschaft](xref:core/modeling/shadow-properties)zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="20093-122">To avoid exposing the concurrency token to the consuming code, it's possible the create one as a [shadow property](xref:core/modeling/shadow-properties):</span></span>

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
