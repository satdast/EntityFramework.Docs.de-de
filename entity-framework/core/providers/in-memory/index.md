---
title: In-Memory Database-Anbieter – EF Core
description: Informationen zum Anbieter für Entity Framework Core-In-Memory-Datenbanken
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
uid: core/providers/in-memory/index
ms.openlocfilehash: 6af1d61a6ff76b82cc0096edbf095a6338d21109
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618999"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="58910-103">EF Core-In-Memory Database-Anbieter</span><span class="sxs-lookup"><span data-stu-id="58910-103">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="58910-104">Dieser Datenbankanbieter ermöglicht, dass Entity Framework Core mit einer In-Memory Database verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="58910-104">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="58910-105">Dies kann für Tests nützlich sein, obwohl der SQLite-Anbieter im In-Memory-Modus für Testersatz für relationale Datenbanken besser geeignet sein kann.</span><span class="sxs-lookup"><span data-stu-id="58910-105">This can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="58910-106">Dieser Anbieter wird nicht im Rahmen des [Entity Framework Core-Projekts](https://github.com/aspnet/EntityFrameworkCore) verwaltet.</span><span class="sxs-lookup"><span data-stu-id="58910-106">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="58910-107">Installieren</span><span class="sxs-lookup"><span data-stu-id="58910-107">Install</span></span>

<span data-ttu-id="58910-108">Installieren Sie das [NuGet-Paket „Microsoft.EntityFrameworkCore.InMemory“](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span><span class="sxs-lookup"><span data-stu-id="58910-108">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="58910-109">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="58910-109">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[<span data-ttu-id="58910-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58910-110">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="58910-111">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="58910-111">Get Started</span></span>

<span data-ttu-id="58910-112">Die folgenden Ressourcen unterstützen Sie bei den ersten Schritten mit diesem Anbieter:</span><span class="sxs-lookup"><span data-stu-id="58910-112">The following resources will help you get started with this provider.</span></span>

* [<span data-ttu-id="58910-113">Testen mit InMemory</span><span class="sxs-lookup"><span data-stu-id="58910-113">Testing with InMemory</span></span>](xref:core/miscellaneous/testing/in-memory)
* [<span data-ttu-id="58910-114">Tests der UnicornStore-Beispielanwendung</span><span class="sxs-lookup"><span data-stu-id="58910-114">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="58910-115">Unterstützte Datenbank-Engines</span><span class="sxs-lookup"><span data-stu-id="58910-115">Supported Database Engines</span></span>

<span data-ttu-id="58910-116">In-Process-Speicherdatenbank (nur für Testzwecke konzipiert)</span><span class="sxs-lookup"><span data-stu-id="58910-116">In-process memory database (designed for testing purposes only)</span></span>
