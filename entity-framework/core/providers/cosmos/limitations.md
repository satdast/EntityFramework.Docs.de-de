---
title: Azure Cosmos DB Anbieter-Einschränkungen-EF Core
description: Einschränkungen der Entity Framework Core Azure Cosmos DB Anbieter im Vergleich zu anderen Anbietern
author: AndriySvyryd
ms.date: 11/05/2020
uid: core/providers/cosmos/limitations
ms.openlocfilehash: f7517954b854c8ea1020c039e490b4fb0dea7181
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430195"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="7751c-103">Einschränkungen für EF Core Azure Cosmos DB-Anbieter</span><span class="sxs-lookup"><span data-stu-id="7751c-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="7751c-104">Der Cosmos-Anbieter hat eine Reihe von Einschränkungen.</span><span class="sxs-lookup"><span data-stu-id="7751c-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="7751c-105">Viele dieser Einschränkungen sind das Ergebnis von Einschränkungen in der zugrunde liegenden Cosmos-Datenbank-Engine und nicht spezifisch für EF.</span><span class="sxs-lookup"><span data-stu-id="7751c-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="7751c-106">Die meisten [sind jedoch noch nicht implementiert](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="7751c-106">But most simply [haven't been implemented yet](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

## <a name="temporary-limitations"></a><span data-ttu-id="7751c-107">Temporäre Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="7751c-107">Temporary limitations</span></span>

- <span data-ttu-id="7751c-108">`Include` Aufrufe werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7751c-108">`Include` calls are not supported</span></span>
- <span data-ttu-id="7751c-109">`Join` Aufrufe werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7751c-109">`Join` calls are not supported</span></span>

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="7751c-110">Azure Cosmos DB SDK-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="7751c-110">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="7751c-111">Es werden nur Async-Methoden bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="7751c-111">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="7751c-112">Da es keine Synchronisierungs Versionen der Low-Level-Methoden gibt EF Core die von abhängig sind, wird die entsprechende Funktionalität derzeit durch Aufrufen von `.Wait()` für das zurückgegebene implementiert `Task` .</span><span class="sxs-lookup"><span data-stu-id="7751c-112">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="7751c-113">Dies bedeutet, dass die Verwendung `SaveChanges` von Methoden wie oder anstelle der asynchronen `ToList` Gegenstücke zu einem Deadlock in der Anwendung führen kann.</span><span class="sxs-lookup"><span data-stu-id="7751c-113">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="7751c-114">Einschränkungen für Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="7751c-114">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="7751c-115">Im Vergleich zu einer relationalen Datenbank finden Sie die vollständige Übersicht über [Azure Cosmos DB unterstützten Features](/azure/cosmos-db/modeling-data).</span><span class="sxs-lookup"><span data-stu-id="7751c-115">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="7751c-116">Vom Client initiierte Transaktionen werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7751c-116">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="7751c-117">Einige Partitions übergreifende Abfragen sind abhängig von den beteiligten Operatoren langsamer (z. b. `Skip/Take` oder `OFFSET LIMIT` ).</span><span class="sxs-lookup"><span data-stu-id="7751c-117">Some cross-partition queries are slower depending on the operators involved (for example `Skip/Take` or `OFFSET LIMIT`)</span></span>
