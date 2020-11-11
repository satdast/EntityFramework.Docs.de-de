---
title: Microsoft SQL Server-Datenbankanbieter – EF Core
description: Dokumentation für den Datenbankanbieter, mit der Entity Framework Core mit Microsoft SQL Server verwendet werden kann
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/index
ms.openlocfilehash: 4118cd5737ece1ad084bb85d409523d217065353
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430322"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="b491f-103">Microsoft SQL Server-EF Core-Datenbankanbieter</span><span class="sxs-lookup"><span data-stu-id="b491f-103">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="b491f-104">Dieser Datenbankanbieter ermöglicht die Verwendung von Entity Framework Core mit Microsoft SQL Server (einschließlich Azure SQL-Datenbank).</span><span class="sxs-lookup"><span data-stu-id="b491f-104">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including Azure SQL Database).</span></span> <span data-ttu-id="b491f-105">Dieser Anbieter wird nicht im Rahmen des [Entity Framework Core-Projekts](https://github.com/dotnet/efcore) verwaltet.</span><span class="sxs-lookup"><span data-stu-id="b491f-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/dotnet/efcore).</span></span>

## <a name="install"></a><span data-ttu-id="b491f-106">Installieren</span><span class="sxs-lookup"><span data-stu-id="b491f-106">Install</span></span>

<span data-ttu-id="b491f-107">Installieren Sie das [NuGet-Paket „Microsoft.EntityFrameworkCore.SqlServer“](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="b491f-107">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="b491f-108">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="b491f-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[<span data-ttu-id="b491f-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b491f-109">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> <span data-ttu-id="b491f-110">Seit Version 3.0.0 verweist der Anbieter auf Microsoft.Data.SqlClient (vorherige Versionen waren von System.Data.SqlClient abhängig).</span><span class="sxs-lookup"><span data-stu-id="b491f-110">Since version 3.0.0, the provider references Microsoft.Data.SqlClient (previous versions depended on System.Data.SqlClient).</span></span> <span data-ttu-id="b491f-111">Wenn Ihr Projekt direkt von SqlClient abhängt, stellen Sie sicher, dass es auf das Microsoft.Data.SqlClient-Paket verweist.</span><span class="sxs-lookup"><span data-stu-id="b491f-111">If your project takes a direct dependency on SqlClient, make sure it references the Microsoft.Data.SqlClient package.</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="b491f-112">Unterstützte Datenbank-Engines</span><span class="sxs-lookup"><span data-stu-id="b491f-112">Supported Database Engines</span></span>

* <span data-ttu-id="b491f-113">Microsoft SQL Server (2012 oder höher)</span><span class="sxs-lookup"><span data-stu-id="b491f-113">Microsoft SQL Server (2012 onwards)</span></span>
