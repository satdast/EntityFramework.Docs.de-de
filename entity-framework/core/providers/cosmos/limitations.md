---
title: Azure Cosmos DB Anbieter-Einschränkungen-EF Core
description: Einschränkungen der Entity Framework Core Azure Cosmos DB Anbieter im Vergleich zu anderen Anbietern
author: AndriySvyryd
ms.date: 11/05/2020
uid: core/providers/cosmos/limitations
ms.openlocfilehash: 088f593dddd9b5691d87566d7e31a699ba90d7c5
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635717"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="c668b-103">Einschränkungen für EF Core Azure Cosmos DB-Anbieter</span><span class="sxs-lookup"><span data-stu-id="c668b-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="c668b-104">Der Cosmos-Anbieter hat eine Reihe von Einschränkungen.</span><span class="sxs-lookup"><span data-stu-id="c668b-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="c668b-105">Viele dieser Einschränkungen sind das Ergebnis von Einschränkungen in der zugrunde liegenden Cosmos-Datenbank-Engine und nicht spezifisch für EF.</span><span class="sxs-lookup"><span data-stu-id="c668b-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="c668b-106">Die meisten [sind jedoch noch nicht implementiert](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="c668b-106">But most simply [haven't been implemented yet](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

<span data-ttu-id="c668b-107">Dies sind einige der häufig angeforderten Features:</span><span class="sxs-lookup"><span data-stu-id="c668b-107">These are some of the commonly requested features:</span></span>

- [<span data-ttu-id="c668b-108">`Include` Förder</span><span class="sxs-lookup"><span data-stu-id="c668b-108">`Include` support</span></span>](https://github.com/dotnet/efcore/issues/16920)
- [<span data-ttu-id="c668b-109">`Join` Förder</span><span class="sxs-lookup"><span data-stu-id="c668b-109">`Join` support</span></span>](https://github.com/dotnet/efcore/issues/17314)
- [<span data-ttu-id="c668b-110">Auflistungen primitiver Typen Unterstützung</span><span class="sxs-lookup"><span data-stu-id="c668b-110">Collections of primitive types support</span></span>](https://github.com/dotnet/efcore/issues/14762)

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="c668b-111">Azure Cosmos DB SDK-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="c668b-111">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="c668b-112">Es werden nur Async-Methoden bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c668b-112">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="c668b-113">Da es keine Synchronisierungs Versionen der Low-Level-Methoden gibt EF Core die von abhängig sind, wird die entsprechende Funktionalität derzeit durch Aufrufen von `.Wait()` für das zurückgegebene implementiert `Task` .</span><span class="sxs-lookup"><span data-stu-id="c668b-113">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="c668b-114">Dies bedeutet, dass die Verwendung `SaveChanges` von Methoden wie oder anstelle der asynchronen `ToList` Gegenstücke zu einem Deadlock in der Anwendung führen kann.</span><span class="sxs-lookup"><span data-stu-id="c668b-114">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="c668b-115">Einschränkungen für Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="c668b-115">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="c668b-116">Im Vergleich zu einer relationalen Datenbank finden Sie die vollständige Übersicht über [Azure Cosmos DB unterstützten Features](/azure/cosmos-db/modeling-data).</span><span class="sxs-lookup"><span data-stu-id="c668b-116">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="c668b-117">Vom Client initiierte Transaktionen werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c668b-117">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="c668b-118">Einige Partitions übergreifende Abfragen sind abhängig von den beteiligten Operatoren langsamer (z. b. `Skip/Take` oder `OFFSET LIMIT` ).</span><span class="sxs-lookup"><span data-stu-id="c668b-118">Some cross-partition queries are slower depending on the operators involved (for example `Skip/Take` or `OFFSET LIMIT`)</span></span>
