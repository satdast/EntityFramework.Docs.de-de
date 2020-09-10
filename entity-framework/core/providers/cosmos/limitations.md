---
title: Azure Cosmos DB Anbieter-Einschränkungen-EF Core
description: Einschränkungen der Entity Framework Core Azure Cosmos DB Anbieter im Vergleich zu anderen Anbietern
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/limitations
ms.openlocfilehash: 0b8edd9ac5fa3d684498efe222ce11a0356040b8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619276"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="af185-103">Einschränkungen für EF Core Azure Cosmos DB-Anbieter</span><span class="sxs-lookup"><span data-stu-id="af185-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="af185-104">Der Cosmos-Anbieter hat eine Reihe von Einschränkungen.</span><span class="sxs-lookup"><span data-stu-id="af185-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="af185-105">Viele dieser Einschränkungen sind das Ergebnis von Einschränkungen in der zugrunde liegenden Cosmos-Datenbank-Engine und nicht spezifisch für EF.</span><span class="sxs-lookup"><span data-stu-id="af185-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="af185-106">Die meisten [sind jedoch noch nicht implementiert](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="af185-106">But most simply [haven't been implemented yet](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

## <a name="temporary-limitations"></a><span data-ttu-id="af185-107">Temporäre Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="af185-107">Temporary limitations</span></span>

- <span data-ttu-id="af185-108">Auch wenn nur ein Entitätstyp ohne Vererbung einem Container zugeordnet ist, verfügt er immer noch über eine diskriminatoreigenschaft.</span><span class="sxs-lookup"><span data-stu-id="af185-108">Even if there is only one entity type without inheritance mapped to a container it still has a discriminator property.</span></span>
- <span data-ttu-id="af185-109">Entitäts Typen mit Partitions Schlüsseln funktionieren in einigen Szenarios nicht ordnungsgemäß.</span><span class="sxs-lookup"><span data-stu-id="af185-109">Entity types with partition keys don't work correctly in some scenarios</span></span>
- <span data-ttu-id="af185-110">`Include` Aufrufe werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="af185-110">`Include` calls are not supported</span></span>
- <span data-ttu-id="af185-111">`Join` Aufrufe werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="af185-111">`Join` calls are not supported</span></span>

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="af185-112">Azure Cosmos DB SDK-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="af185-112">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="af185-113">Es werden nur Async-Methoden bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="af185-113">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="af185-114">Da es keine Synchronisierungs Versionen der Low-Level-Methoden gibt EF Core die von abhängig sind, wird die entsprechende Funktionalität derzeit durch Aufrufen von `.Wait()` für das zurückgegebene implementiert `Task` .</span><span class="sxs-lookup"><span data-stu-id="af185-114">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="af185-115">Dies bedeutet, dass die Verwendung `SaveChanges` von Methoden wie oder anstelle der asynchronen `ToList` Gegenstücke zu einem Deadlock in der Anwendung führen kann.</span><span class="sxs-lookup"><span data-stu-id="af185-115">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="af185-116">Einschränkungen für Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="af185-116">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="af185-117">Im Vergleich zu einer relationalen Datenbank finden Sie die vollständige Übersicht über [Azure Cosmos DB unterstützten Features](/azure/cosmos-db/modeling-data).</span><span class="sxs-lookup"><span data-stu-id="af185-117">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="af185-118">Vom Client initiierte Transaktionen werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="af185-118">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="af185-119">Einige Partitions übergreifende Abfragen werden abhängig von den beteiligten Operatoren entweder nicht unterstützt oder sind viel langsamer.</span><span class="sxs-lookup"><span data-stu-id="af185-119">Some cross-partition queries are either not supported or much slower depending on the operators involved</span></span>
