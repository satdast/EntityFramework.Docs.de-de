---
title: 'Überblick über Protokollierung und Abfangfunktionen: EF Core'
description: Übersicht über Protokollierung, Ereignisse, Interceptors und Diagnosefunktionen für EF Core
author: ajcvickers
ms.date: 10/01/2020
uid: core/miscellaneous/events/index
ms.openlocfilehash: 8c46b4efb205c60be51529a89cd9dd8fb5268156
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066495"
---
# <a name="overview-of-logging-and-interception"></a><span data-ttu-id="8a66b-103">Übersicht über Protokollierung und Abfangfunktionen</span><span class="sxs-lookup"><span data-stu-id="8a66b-103">Overview of logging and interception</span></span>

<span data-ttu-id="8a66b-104">Entity Framework Core (EF Core) enthält mehrere Mechanismen für das Erstellen von Protokollen, Reagieren auf Ereignisse und Erstellen von Diagnosen.</span><span class="sxs-lookup"><span data-stu-id="8a66b-104">Entity Framework Core (EF Core) contains several mechanisms for generating logs, responding to events, and obtaining diagnostics.</span></span> <span data-ttu-id="8a66b-105">Diese Mechanismen sind jeweils auf verschiedene Situationen zugeschnitten, und es ist wichtig, den besten Mechanismus für eine jeweilige Aufgabe zu wählen, auch wenn mehrere Mechanismen möglich wären.</span><span class="sxs-lookup"><span data-stu-id="8a66b-105">Each of these are tailored to different situations, and it is important to select the best mechanism for the task in hand, even when multiple mechanisms could work.</span></span> <span data-ttu-id="8a66b-106">Ein Datenbankinterceptor könnte beispielsweise für eine SQL-Protokollierung verwendet werden. Besser hierfür eignen sich jedoch protokollierungsspezifische Mechanismen.</span><span class="sxs-lookup"><span data-stu-id="8a66b-106">For example, a database interceptor could be used to log SQL, but this is better handled by one of the logging-specific mechanisms.</span></span> <span data-ttu-id="8a66b-107">Auf dieser Seite finden Sie eine Übersicht der verschiedenen Mechanismen sowie Erläuterungen, wann die einzelnen verwendet werden sollten.</span><span class="sxs-lookup"><span data-stu-id="8a66b-107">This page presents an overview of each of these mechanisms and describes when each should be used.</span></span>

## <a name="simple-logging"></a><span data-ttu-id="8a66b-108">Einfache Protokollierung</span><span class="sxs-lookup"><span data-stu-id="8a66b-108">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="8a66b-109">Dieses Feature wurde in EF Core 5.0 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8a66b-109">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="8a66b-110">Der Zugriff auf EF Core-Protokolle ist über Anwendungen jeglichen Typs über die Verwendung von [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span><span class="sxs-lookup"><span data-stu-id="8a66b-110">EF Core logs can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="8a66b-111">beim [Konfigurieren einer DbContext-Instanz](xref:core/miscellaneous/configuring-dbcontext) möglich.</span><span class="sxs-lookup"><span data-stu-id="8a66b-111">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="8a66b-112">Diese Konfiguration erfolgt in der Regel durch ein Überschreiben von <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="8a66b-112">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="8a66b-113">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8a66b-113">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="8a66b-114">Dieses Konzept ähnelt <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span><span class="sxs-lookup"><span data-stu-id="8a66b-114">This concept is similar to <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span></span>

<span data-ttu-id="8a66b-115">Weitere Informationen finden Sie unter [Einfache Protokollierung](xref:core/miscellaneous/events/simple-logging).</span><span class="sxs-lookup"><span data-stu-id="8a66b-115">See [Simple Logging](xref:core/miscellaneous/events/simple-logging) for more information.</span></span>
