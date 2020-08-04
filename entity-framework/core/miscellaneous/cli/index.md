---
title: Entity Framework Core Tools-Referenz – EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/19/2018
uid: core/miscellaneous/cli/index
ms.openlocfilehash: 2821263f1e9cd962569e8050b01d704cf858e6c8
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526485"
---
# <a name="entity-framework-core-tools-reference"></a><span data-ttu-id="73ad6-102">Entity Framework Core Tools-Referenz</span><span class="sxs-lookup"><span data-stu-id="73ad6-102">Entity Framework Core tools reference</span></span>

<span data-ttu-id="73ad6-103">Die Entity Framework Core Tools helfen zur Entwurfszeit bei Entwicklungsaufgaben.</span><span class="sxs-lookup"><span data-stu-id="73ad6-103">The Entity Framework Core tools help with design-time development tasks.</span></span> <span data-ttu-id="73ad6-104">Diese werden hauptsächlich verwendet, um Migrationen zu verwalten und ein Gerüst für `DbContext` und Entitätstypen durch Reverse Engineering eines Datenbankschemas zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="73ad6-104">They're primarily used to manage Migrations and to scaffold a `DbContext` and entity types by reverse engineering the schema of a database.</span></span>

* <span data-ttu-id="73ad6-105">Die [EF Core Paket-Manager-Konsolentools](powershell.md) werden in der [Paket-Manager-Konsole](/nuget/tools/package-manager-console) in Visual Studio ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="73ad6-105">The [EF Core Package Manager Console tools](powershell.md) run in the [Package Manager Console](/nuget/tools/package-manager-console) in Visual Studio.</span></span>

* <span data-ttu-id="73ad6-106">Die [EF Core .NET-Befehlszeilentools (CLI-Tools)](dotnet.md) stellen eine Erweiterung der plattformübergreifenden [.NET Core CLI-Tools](/dotnet/core/tools/) dar.</span><span class="sxs-lookup"><span data-stu-id="73ad6-106">The [EF Core .NET command-line interface (CLI) tools](dotnet.md) are an extension to the cross-platform [.NET Core CLI tools](/dotnet/core/tools/).</span></span> <span data-ttu-id="73ad6-107">Für diese Tools ist ein .NET Core-SDK-Projekt (mit `Sdk="Microsoft.NET.Sdk"` oder einem ähnlichen SDK in der Projektdatei) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="73ad6-107">These tools require a .NET Core SDK project (one with `Sdk="Microsoft.NET.Sdk"` or similar in the project file).</span></span>

<span data-ttu-id="73ad6-108">Beide Tools bieten dieselbe Funktionalität.</span><span class="sxs-lookup"><span data-stu-id="73ad6-108">Both tools expose the same functionality.</span></span> <span data-ttu-id="73ad6-109">Für die Entwicklung in Visual Studio empfehlen wir die Verwendung von **Paket-Manager-Konsolentools**, da diese ein besseres ganzheitliches Erlebnis bieten.</span><span class="sxs-lookup"><span data-stu-id="73ad6-109">If you're developing in Visual Studio, we recommend using the **Package Manager Console** tools since they provide a more integrated experience.</span></span>

## <a name="next-steps"></a><span data-ttu-id="73ad6-110">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="73ad6-110">Next steps</span></span>

* [<span data-ttu-id="73ad6-111">EF Core-Paket-Manager-Konsolentools – Referenz</span><span class="sxs-lookup"><span data-stu-id="73ad6-111">EF Core Package Manager Console tools reference</span></span>](powershell.md)
* [<span data-ttu-id="73ad6-112">EF Core .NET CLI-Tools – Referenz</span><span class="sxs-lookup"><span data-stu-id="73ad6-112">EF Core .NET CLI tools reference</span></span>](dotnet.md)
