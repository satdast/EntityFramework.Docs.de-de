---
title: Entwurfszeit Dienste-EF Core
description: Informationen zu Entity Framework Core Entwurfszeit Diensten
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: aa761c4a20e0848a77aa7b4ad625124a1d372a70
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617846"
---
# <a name="design-time-services"></a>Entwurfszeitdienste

Einige Dienste, die von den-Tools verwendet werden, werden nur zur Entwurfszeit verwendet. Diese Dienste werden getrennt von den Lauf Zeit Diensten EF Core verwaltet, um zu verhindern, dass Sie mit Ihrer APP bereitgestellt werden. Um einen dieser Dienste (z. b. den-Dienst zum Generieren von Migrations Dateien) zu überschreiben, fügen Sie dem Startprojekt eine Implementierung von hinzu `IDesignTimeServices` .

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
