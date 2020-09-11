---
title: SQLite-Datenbankanbieter – EF Core
description: Informationen zum Anbieter für Entity Framework Core-SQLite-Datenbanken
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: 8620f0e37825368cb3d7965a05118ab1297fb9e8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616557"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="d3a95-103">SQLite-EF Core-Datenbankanbieter</span><span class="sxs-lookup"><span data-stu-id="d3a95-103">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="d3a95-104">Dieser Datenbankanbieter ermöglicht die Verwendung von Entity Framework Core mit SQLite.</span><span class="sxs-lookup"><span data-stu-id="d3a95-104">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="d3a95-105">Dieser Anbieter wird nicht im Rahmen des [Entity Framework Core-Projekts](https://github.com/aspnet/EntityFrameworkCore) verwaltet.</span><span class="sxs-lookup"><span data-stu-id="d3a95-105">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="d3a95-106">Installieren</span><span class="sxs-lookup"><span data-stu-id="d3a95-106">Install</span></span>

<span data-ttu-id="d3a95-107">Installieren Sie das [NuGet-Paket „Microsoft.EntityFrameworkCore.Sqlite“](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span><span class="sxs-lookup"><span data-stu-id="d3a95-107">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="d3a95-108">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="d3a95-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="d3a95-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d3a95-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="d3a95-110">Unterstützte Datenbank-Engines</span><span class="sxs-lookup"><span data-stu-id="d3a95-110">Supported Database Engines</span></span>

* <span data-ttu-id="d3a95-111">SQLite (3.7 oder höher)</span><span class="sxs-lookup"><span data-stu-id="d3a95-111">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="d3a95-112">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="d3a95-112">Limitations</span></span>

<span data-ttu-id="d3a95-113">Einige wichtige Einschränkungen zum SQLite-Anbieter finden Sie unter [Einschränkungen zu SQLite](xref:core/providers/sqlite/limitations).</span><span class="sxs-lookup"><span data-stu-id="d3a95-113">See [SQLite Limitations](xref:core/providers/sqlite/limitations) for some important limitations of the SQLite provider.</span></span>
