---
title: Explizites Laden zugehöriger Daten – EF Core
description: Explizites Laden zugehöriger Daten mit Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/explicit
ms.openlocfilehash: 0cfc4b5304d81d5a58497857474804946d3f85d7
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065718"
---
# <a name="explicit-loading-of-related-data"></a><span data-ttu-id="0607d-103">Explizites Laden zugehöriger Daten</span><span class="sxs-lookup"><span data-stu-id="0607d-103">Explicit Loading of Related Data</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="0607d-104">Explizites Laden</span><span class="sxs-lookup"><span data-stu-id="0607d-104">Explicit loading</span></span>

<span data-ttu-id="0607d-105">Sie können eine Navigationseigenschaft über die API `DbContext.Entry(...)` explizit laden.</span><span class="sxs-lookup"><span data-stu-id="0607d-105">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#Eager)]

<span data-ttu-id="0607d-106">Sie können eine Navigationseigenschaft auch explizit laden, indem Sie eine separate Abfrage ausführen, die die zugehörigen Entitäten zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="0607d-106">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="0607d-107">Wenn die Änderungsnachverfolgung aktiviert ist, legt EF Core, wenn eine Abfrage eine Entität materialisiert, die Navigationseigenschaften der neu geladenen Entität automatisch so fest, dass diese auf bereits geladene Entitäten verweisen, und die Navigationseigenschaften der bereits geladenen Entitäten so, dass diese auf die neu geladene Entität verweisen.</span><span class="sxs-lookup"><span data-stu-id="0607d-107">If change tracking is enabled, then when query materializes an entity, EF Core will automatically set the navigation properties of the newly loaded entity to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly loaded entity.</span></span>

## <a name="querying-related-entities"></a><span data-ttu-id="0607d-108">Abfragen zugehöriger Entitäten</span><span class="sxs-lookup"><span data-stu-id="0607d-108">Querying related entities</span></span>

<span data-ttu-id="0607d-109">Sie können auch eine LINQ-Abfrage abrufen, welche die Inhalte einer Navigationseigenschaft darstellt.</span><span class="sxs-lookup"><span data-stu-id="0607d-109">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="0607d-110">Damit können Sie zusätzliche Operatoren auf die Abfrage anwenden.</span><span class="sxs-lookup"><span data-stu-id="0607d-110">It allows you to apply additional operators over the query.</span></span> <span data-ttu-id="0607d-111">Beispielsweise können Sie einen Aggregatoperator auf die verknüpften Entitäten anwenden, ohne diese in den Arbeitsspeicher zu laden.</span><span class="sxs-lookup"><span data-stu-id="0607d-111">For example applying an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#NavQueryAggregate)]

<span data-ttu-id="0607d-112">Darüber hinaus können Sie filtern, welche zugehörigen Entitäten in den Speicher geladen werden.</span><span class="sxs-lookup"><span data-stu-id="0607d-112">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#NavQueryFiltered)]
