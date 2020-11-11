---
title: SQLite-Datenbankanbieter – EF Core
description: Informationen zum Anbieter für Entity Framework Core-SQLite-Datenbanken
author: bricelam
ms.date: 10/27/2016
uid: core/providers/sqlite/index
ms.openlocfilehash: 6dac555456ed347b3eb07bbd0f2a89ab9179d20e
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430299"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="f3333-103">SQLite-EF Core-Datenbankanbieter</span><span class="sxs-lookup"><span data-stu-id="f3333-103">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="f3333-104">Dieser Datenbankanbieter ermöglicht die Verwendung von Entity Framework Core mit SQLite.</span><span class="sxs-lookup"><span data-stu-id="f3333-104">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="f3333-105">Dieser Anbieter wird nicht im Rahmen des [Entity Framework Core-Projekts](https://github.com/dotnet/efcore) verwaltet.</span><span class="sxs-lookup"><span data-stu-id="f3333-105">The provider is maintained as part of the [Entity Framework Core project](https://github.com/dotnet/efcore).</span></span>

## <a name="install"></a><span data-ttu-id="f3333-106">Installieren</span><span class="sxs-lookup"><span data-stu-id="f3333-106">Install</span></span>

<span data-ttu-id="f3333-107">Installieren Sie das [NuGet-Paket „Microsoft.EntityFrameworkCore.Sqlite“](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span><span class="sxs-lookup"><span data-stu-id="f3333-107">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f3333-108">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="f3333-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="f3333-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3333-109">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="f3333-110">Unterstützte Datenbank-Engines</span><span class="sxs-lookup"><span data-stu-id="f3333-110">Supported Database Engines</span></span>

* <span data-ttu-id="f3333-111">SQLite (3.7 oder höher)</span><span class="sxs-lookup"><span data-stu-id="f3333-111">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="f3333-112">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="f3333-112">Limitations</span></span>

<span data-ttu-id="f3333-113">Einige wichtige Einschränkungen zum SQLite-Anbieter finden Sie unter [Einschränkungen zu SQLite](xref:core/providers/sqlite/limitations).</span><span class="sxs-lookup"><span data-stu-id="f3333-113">See [SQLite Limitations](xref:core/providers/sqlite/limitations) for some important limitations of the SQLite provider.</span></span>
