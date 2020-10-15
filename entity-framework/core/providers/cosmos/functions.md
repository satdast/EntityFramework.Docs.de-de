---
title: Funktions Zuordnungen-Azure Cosmos DB Anbieter-EF Core
description: Funktions Zuordnungen des Azure Cosmos DB EF Core Anbieters
author: bricelam
ms.date: 10/08/2020
uid: core/providers/cosmos/functions
ms.openlocfilehash: ffcf82fbe57ea16e6fc62f3c9c80c572b3e1ed91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066511"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a><span data-ttu-id="1cc40-103">Funktions Zuordnungen des Azure Cosmos DB EF Core Anbieters</span><span class="sxs-lookup"><span data-stu-id="1cc40-103">Function Mappings of the Azure Cosmos DB EF Core Provider</span></span>

<span data-ttu-id="1cc40-104">Diese Seite zeigt, welche .net-Member bei Verwendung des Azure Cosmos DB Anbieters in welche SQL-Funktionen übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="1cc40-104">This page shows which .NET members are translated into which SQL functions when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="1cc40-105">.NET</span><span class="sxs-lookup"><span data-stu-id="1cc40-105">.NET</span></span>                          | <span data-ttu-id="1cc40-106">SQL</span><span class="sxs-lookup"><span data-stu-id="1cc40-106">SQL</span></span>                              | <span data-ttu-id="1cc40-107">Hinzugefügt in</span><span class="sxs-lookup"><span data-stu-id="1cc40-107">Added in</span></span>
----------------------------- | -------------------------------- | --------
<span data-ttu-id="1cc40-108">Ausstellung. Enthält (Element)</span><span class="sxs-lookup"><span data-stu-id="1cc40-108">collection.Contains(item)</span></span>     | <span data-ttu-id="1cc40-109">@item IN @collection</span><span class="sxs-lookup"><span data-stu-id="1cc40-109">@item IN @collection</span></span>
<span data-ttu-id="1cc40-110">StringValue. enthält (Wert)</span><span class="sxs-lookup"><span data-stu-id="1cc40-110">stringValue.Contains(value)</span></span>   | <span data-ttu-id="1cc40-111">Enthält ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="1cc40-111">CONTAINS(@stringValue, @value)</span></span>   | <span data-ttu-id="1cc40-112">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="1cc40-112">EF Core 5.0</span></span>
<span data-ttu-id="1cc40-113">StringValue. EndsWith (Wert)</span><span class="sxs-lookup"><span data-stu-id="1cc40-113">stringValue.EndsWith(value)</span></span>   | <span data-ttu-id="1cc40-114">EndsWith ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="1cc40-114">ENDSWITH(@stringValue, @value)</span></span>   | <span data-ttu-id="1cc40-115">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="1cc40-115">EF Core 5.0</span></span>
<span data-ttu-id="1cc40-116">StringValue. FirstOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="1cc40-116">stringValue.FirstOrDefault()</span></span>  | <span data-ttu-id="1cc40-117">Links ( @stringValue , 1)</span><span class="sxs-lookup"><span data-stu-id="1cc40-117">LEFT(@stringValue, 1)</span></span>            | <span data-ttu-id="1cc40-118">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="1cc40-118">EF Core 5.0</span></span>
<span data-ttu-id="1cc40-119">StringValue. LastOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="1cc40-119">stringValue.LastOrDefault()</span></span>   | <span data-ttu-id="1cc40-120">Right ( @stringValue , 1)</span><span class="sxs-lookup"><span data-stu-id="1cc40-120">RIGHT(@stringValue, 1)</span></span>           | <span data-ttu-id="1cc40-121">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="1cc40-121">EF Core 5.0</span></span>
<span data-ttu-id="1cc40-122">StringValue. starttwith (Wert)</span><span class="sxs-lookup"><span data-stu-id="1cc40-122">stringValue.StartsWith(value)</span></span> | <span data-ttu-id="1cc40-123">Start mit ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="1cc40-123">STARTSWITH(@stringValue, @value)</span></span> | <span data-ttu-id="1cc40-124">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="1cc40-124">EF Core 5.0</span></span>
