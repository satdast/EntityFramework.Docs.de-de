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
# <a name="design-time-services"></a>Entwurfszeitdienste

Einige Dienste, die von den-Tools verwendet werden, werden nur zur Entwurfszeit verwendet. Diese Dienste werden getrennt von den Lauf Zeit Diensten EF Core verwaltet, um zu verhindern, dass Sie mit Ihrer APP bereitgestellt werden. Um einen dieser Dienste (z. b. den-Dienst zum Generieren von Migrations Dateien) zu überschreiben, fügen Sie dem Startprojekt eine Implementierung von hinzu `IDesignTimeServices` .

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
