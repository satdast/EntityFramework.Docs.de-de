---
title: In-Memory Database-Anbieter – EF Core
description: Informationen zum Anbieter für Entity Framework Core-In-Memory-Datenbanken
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/in-memory/index
ms.openlocfilehash: 1af75088ae892e3b428caf6bdb31dd2b750a05fe
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430169"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="b19c9-103">EF Core-In-Memory Database-Anbieter</span><span class="sxs-lookup"><span data-stu-id="b19c9-103">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="b19c9-104">Dieser Datenbankanbieter ermöglicht, dass Entity Framework Core mit einer In-Memory Database verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b19c9-104">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="b19c9-105">Die In-Memory-Datenbank kann für Tests nützlich sein, obwohl der SQLite-Anbieter im In-Memory-Modus als Testersatz für relationale Datenbanken besser geeignet sein kann.</span><span class="sxs-lookup"><span data-stu-id="b19c9-105">The in-memory database can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="b19c9-106">Die In-Memory-Datenbank ist nur für Testzwecke vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="b19c9-106">The in-memory database is designed for testing only.</span></span> <span data-ttu-id="b19c9-107">Dieser Anbieter wird nicht im Rahmen des [Entity Framework Core-Projekts](https://github.com/dotnet/efcore) verwaltet.</span><span class="sxs-lookup"><span data-stu-id="b19c9-107">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/dotnet/efcore).</span></span>

## <a name="install"></a><span data-ttu-id="b19c9-108">Installieren</span><span class="sxs-lookup"><span data-stu-id="b19c9-108">Install</span></span>

<span data-ttu-id="b19c9-109">Installieren Sie das [NuGet-Paket „Microsoft.EntityFrameworkCore.InMemory“](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span><span class="sxs-lookup"><span data-stu-id="b19c9-109">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="b19c9-110">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="b19c9-110">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[<span data-ttu-id="b19c9-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b19c9-111">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="b19c9-112">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="b19c9-112">Get Started</span></span>

<span data-ttu-id="b19c9-113">Die folgenden Ressourcen unterstützen Sie bei den ersten Schritten mit diesem Anbieter:</span><span class="sxs-lookup"><span data-stu-id="b19c9-113">The following resources will help you get started with this provider.</span></span>

* [<span data-ttu-id="b19c9-114">Testen mit InMemory</span><span class="sxs-lookup"><span data-stu-id="b19c9-114">Testing with InMemory</span></span>](xref:core/testing/in-memory)
* [<span data-ttu-id="b19c9-115">Tests der UnicornStore-Beispielanwendung</span><span class="sxs-lookup"><span data-stu-id="b19c9-115">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="b19c9-116">Unterstützte Datenbank-Engines</span><span class="sxs-lookup"><span data-stu-id="b19c9-116">Supported Database Engines</span></span>

<span data-ttu-id="b19c9-117">In-Process-Arbeitsspeicherdatenbank (nur für Testzwecke konzipiert).</span><span class="sxs-lookup"><span data-stu-id="b19c9-117">In-process memory database, designed for testing purposes only.</span></span>
