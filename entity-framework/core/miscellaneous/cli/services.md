---
title: Entwurfszeit Dienste-EF Core
description: Informationen zu Entity Framework Core Entwurfszeit Diensten
author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: 6778a1fba025e9fbce1bb3e43462b0ee7c5dcfc4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061943"
---
# <a name="design-time-services"></a><span data-ttu-id="59a36-103">Entwurfszeitdienste</span><span class="sxs-lookup"><span data-stu-id="59a36-103">Design-time services</span></span>

<span data-ttu-id="59a36-104">Einige Dienste, die von den-Tools verwendet werden, werden nur zur Entwurfszeit verwendet.</span><span class="sxs-lookup"><span data-stu-id="59a36-104">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="59a36-105">Diese Dienste werden getrennt von den Lauf Zeit Diensten EF Core verwaltet, um zu verhindern, dass Sie mit Ihrer APP bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="59a36-105">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="59a36-106">Um einen dieser Dienste (z. b. den-Dienst zum Generieren von Migrations Dateien) zu überschreiben, fügen Sie dem Startprojekt eine Implementierung von hinzu `IDesignTimeServices` .</span><span class="sxs-lookup"><span data-stu-id="59a36-106">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
