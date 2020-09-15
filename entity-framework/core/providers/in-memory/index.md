---
title: In-Memory Database-Anbieter – EF Core
description: Informationen zum Anbieter für Entity Framework Core-In-Memory-Datenbanken
author: rowanmiller
ms.date: 10/27/2016
uid: core/providers/in-memory/index
ms.openlocfilehash: 9d0a5d14cdb047b80788fbe4d9d34deccdbd4ce1
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071354"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="2a9ce-103">EF Core-In-Memory Database-Anbieter</span><span class="sxs-lookup"><span data-stu-id="2a9ce-103">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="2a9ce-104">Dieser Datenbankanbieter ermöglicht, dass Entity Framework Core mit einer In-Memory Database verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2a9ce-104">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="2a9ce-105">Dies kann für Tests nützlich sein, obwohl der SQLite-Anbieter im In-Memory-Modus für Testersatz für relationale Datenbanken besser geeignet sein kann.</span><span class="sxs-lookup"><span data-stu-id="2a9ce-105">This can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="2a9ce-106">Dieser Anbieter wird nicht im Rahmen des [Entity Framework Core-Projekts](https://github.com/aspnet/EntityFrameworkCore) verwaltet.</span><span class="sxs-lookup"><span data-stu-id="2a9ce-106">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="2a9ce-107">Installieren</span><span class="sxs-lookup"><span data-stu-id="2a9ce-107">Install</span></span>

<span data-ttu-id="2a9ce-108">Installieren Sie das [NuGet-Paket „Microsoft.EntityFrameworkCore.InMemory“](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span><span class="sxs-lookup"><span data-stu-id="2a9ce-108">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="2a9ce-109">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="2a9ce-109">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[<span data-ttu-id="2a9ce-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a9ce-110">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="2a9ce-111">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="2a9ce-111">Get Started</span></span>

<span data-ttu-id="2a9ce-112">Die folgenden Ressourcen unterstützen Sie bei den ersten Schritten mit diesem Anbieter:</span><span class="sxs-lookup"><span data-stu-id="2a9ce-112">The following resources will help you get started with this provider.</span></span>

* [<span data-ttu-id="2a9ce-113">Testen mit InMemory</span><span class="sxs-lookup"><span data-stu-id="2a9ce-113">Testing with InMemory</span></span>](xref:core/miscellaneous/testing/in-memory)
* [<span data-ttu-id="2a9ce-114">Tests der UnicornStore-Beispielanwendung</span><span class="sxs-lookup"><span data-stu-id="2a9ce-114">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="2a9ce-115">Unterstützte Datenbank-Engines</span><span class="sxs-lookup"><span data-stu-id="2a9ce-115">Supported Database Engines</span></span>

<span data-ttu-id="2a9ce-116">In-Process-Speicherdatenbank (nur für Testzwecke konzipiert)</span><span class="sxs-lookup"><span data-stu-id="2a9ce-116">In-process memory database (designed for testing purposes only)</span></span>
