---
title: Entity Framework Core Tools-Referenz – EF Core
description: Referenzleitfaden zur Entity Framework Core-CLI und zur Paket-Manager-Konsole in Visual Studio
author: bricelam
ms.date: 09/19/2018
uid: core/cli/index
ms.openlocfilehash: 9a6ae8f945e92453ddfaa089ae1d606d142f725a
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431123"
---
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="69345-103">Entity Framework Core Tools-Referenz</span><span class="sxs-lookup"><span data-stu-id="69345-103">Entity Framework Core tools reference</span></span>

<span data-ttu-id="69345-104">Die Entity Framework Core Tools helfen zur Entwurfszeit bei Entwicklungsaufgaben.</span><span class="sxs-lookup"><span data-stu-id="69345-104">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="69345-105">Diese werden hauptsächlich verwendet, um Migrationen zu verwalten und ein Gerüst für `DbContext` und Entitätstypen durch Reverse Engineering eines Datenbankschemas zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="69345-105">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

* <span data-ttu-id="69345-106">Die [EF Core Paket-Manager-Konsolentools](xref:core/cli/powershell) werden in der [Paket-Manager-Konsole](/nuget/tools/package-manager-console) in Visual Studio ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="69345-106">The [EF Core Package Manager Console tools](xref:core/cli/powershell) run in the [Package Manager Console](/nuget/tools/package-manager-console) in Visual Studio.</span></span>

* <span data-ttu-id="69345-107">Die [EF Core .NET-Befehlszeilentools (CLI-Tools)](xref:core/cli/dotnet) stellen eine Erweiterung der plattformübergreifenden [.NET Core CLI-Tools](/dotnet/core/tools/) dar.</span><span class="sxs-lookup"><span data-stu-id="69345-107">The [EF Core .NET command-line interface (CLI) tools](xref:core/cli/dotnet) are an extension to the cross-platform [.NET Core CLI tools](/dotnet/core/tools/).</span></span> <span data-ttu-id="69345-108">Für diese Tools ist ein .NET Core-SDK-Projekt (mit `Sdk="Microsoft.NET.Sdk"` oder einem ähnlichen SDK in der Projektdatei) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="69345-108">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

<span data-ttu-id="69345-109">Beide Tools bieten dieselbe Funktionalität.</span><span class="sxs-lookup"><span data-stu-id="69345-109">Both tools expose the same functionality.</span></span> <span data-ttu-id="69345-110">Für die Entwicklung in Visual Studio empfehlen wir die Verwendung von **Paket-Manager-Konsolentools** , da diese ein besseres ganzheitliches Erlebnis bieten.</span><span class="sxs-lookup"><span data-stu-id="69345-110">If you're developing in Visual Studio, we recommend using the **Package Manager Console** tools since they provide a more integrated experience.</span></span>

## <a name="next-steps"></a><span data-ttu-id="69345-111">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="69345-111">Next steps</span></span>

* [<span data-ttu-id="69345-112">EF Core-Paket-Manager-Konsolentools – Referenz</span><span class="sxs-lookup"><span data-stu-id="69345-112">EF Core Package Manager Console tools reference</span></span>](xref:core/cli/powershell)
* [<span data-ttu-id="69345-113">EF Core .NET CLI-Tools – Referenz</span><span class="sxs-lookup"><span data-stu-id="69345-113">EF Core .NET CLI tools reference</span></span>](xref:core/cli/dotnet)
