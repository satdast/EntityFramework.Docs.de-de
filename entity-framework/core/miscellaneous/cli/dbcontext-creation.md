---
title: Erstellung von dbcontext zur Entwurfszeit EF Core
description: Strategien zum Erstellen eines Entwurfszeit-dbcontext mit Entity Framework Core
author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: a9d7da2bb76d60ca63eb0dc189f924df125f0a7d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062008"
---
# <a name="design-time-dbcontext-creation"></a><span data-ttu-id="9eb9a-103">DbContext-Instanzerstellung zur Entwurfszeit</span><span class="sxs-lookup"><span data-stu-id="9eb9a-103">Design-time DbContext Creation</span></span>

<span data-ttu-id="9eb9a-104">Einige der EF Core Tools-Befehle (z. b. die [Migrations][1] Befehle) erfordern, dass eine abgeleitete `DbContext` Instanz zur Entwurfszeit erstellt wird, um Details zu den Entitäts Typen der Anwendung und deren Zuordnung zu einem Datenbankschema zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="9eb9a-104">Some of the EF Core Tools commands (for example, the [Migrations][1] commands) require a derived `DbContext` instance to be created at design time in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="9eb9a-105">In den meisten Fällen ist es wünschenswert, dass die, die `DbContext` erstellt wird, auf ähnliche Weise konfiguriert wird, wie Sie zur [Laufzeit konfiguriert][2]wird.</span><span class="sxs-lookup"><span data-stu-id="9eb9a-105">In most cases, it is desirable that the `DbContext` thereby created is configured in a similar way to how it would be [configured at run time][2].</span></span>

<span data-ttu-id="9eb9a-106">Es gibt verschiedene Möglichkeiten, die Tools zu erstellen `DbContext` :</span><span class="sxs-lookup"><span data-stu-id="9eb9a-106">There are various ways the tools try to create the `DbContext`:</span></span>

## <a name="from-application-services"></a><span data-ttu-id="9eb9a-107">Aus Anwendungsdiensten</span><span class="sxs-lookup"><span data-stu-id="9eb9a-107">From application services</span></span>

<span data-ttu-id="9eb9a-108">Wenn das Startprojekt den [ASP.net Core Webhost][3] oder den [generischen .net Core-Host][4]verwendet, versuchen die Tools, das dbcontext-Objekt vom Dienstanbieter der Anwendung abzurufen.</span><span class="sxs-lookup"><span data-stu-id="9eb9a-108">If your startup project uses the [ASP.NET Core Web Host][3] or [.NET Core Generic Host][4], the tools try to obtain the DbContext object from the application's service provider.</span></span>

<span data-ttu-id="9eb9a-109">Die Tools versuchen zunächst, den Dienstanbieter `Program.CreateHostBuilder()` abzurufen, indem Sie aufrufen und dann auf die- `Build()` `Services` Eigenschaft zugreifen.</span><span class="sxs-lookup"><span data-stu-id="9eb9a-109">The tools first try to obtain the service provider by invoking `Program.CreateHostBuilder()`, calling `Build()`, then accessing the `Services` property.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> <span data-ttu-id="9eb9a-110">Wenn Sie eine neue ASP.net Core Anwendung erstellen, ist dieser Hook standardmäßig enthalten.</span><span class="sxs-lookup"><span data-stu-id="9eb9a-110">When you create a new ASP.NET Core application, this hook is included by default.</span></span>

<span data-ttu-id="9eb9a-111">Die `DbContext` selbst und alle Abhängigkeiten im Konstruktor müssen als Dienste im Dienstanbieter der Anwendung registriert werden.</span><span class="sxs-lookup"><span data-stu-id="9eb9a-111">The `DbContext` itself and any dependencies in its constructor need to be registered as services in the application's service provider.</span></span> <span data-ttu-id="9eb9a-112">Dies kann problemlos erreicht werden, indem [ein Konstruktor für den verwendet wird `DbContext` , der eine Instanz von `DbContextOptions<TContext>` als Argument annimmt][5] und die- [ `AddDbContext<TContext>` Methode][6]verwendet.</span><span class="sxs-lookup"><span data-stu-id="9eb9a-112">This can be easily achieved by having [a constructor on the `DbContext` that takes an instance of `DbContextOptions<TContext>` as an argument][5] and using the [`AddDbContext<TContext>` method][6].</span></span>

## <a name="using-a-constructor-with-no-parameters"></a><span data-ttu-id="9eb9a-113">Verwenden eines Konstruktors ohne Parameter</span><span class="sxs-lookup"><span data-stu-id="9eb9a-113">Using a constructor with no parameters</span></span>

<span data-ttu-id="9eb9a-114">Wenn dbcontext vom Anwendungs Dienstanbieter nicht abgerufen werden kann, suchen die Tools im Projekt nach dem abgeleiteten `DbContext` Typ.</span><span class="sxs-lookup"><span data-stu-id="9eb9a-114">If the DbContext can't be obtained from the application service provider, the tools look for the derived `DbContext` type inside the project.</span></span> <span data-ttu-id="9eb9a-115">Anschließend wird versucht, eine Instanz mit einem Konstruktor ohne Parameter zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9eb9a-115">Then they try to create an instance using a constructor with no parameters.</span></span> <span data-ttu-id="9eb9a-116">Dies kann der Standardkonstruktor sein, wenn `DbContext` mithilfe der- [`OnConfiguring`][7] Methode konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="9eb9a-116">This can be the default constructor if the `DbContext` is configured using the [`OnConfiguring`][7] method.</span></span>

## <a name="from-a-design-time-factory"></a><span data-ttu-id="9eb9a-117">Aus einer Entwurfszeit Factory</span><span class="sxs-lookup"><span data-stu-id="9eb9a-117">From a design-time factory</span></span>

<span data-ttu-id="9eb9a-118">Sie können den Tools auch mitteilen, wie Sie dbcontext durch Implementieren der- `IDesignTimeDbContextFactory<TContext>` Schnittstelle erstellen: Wenn eine Klasse, die diese Schnittstelle implementiert, sich im gleichen Projekt wie die abgeleitete `DbContext` oder im Startprojekt der Anwendung befindet, umgehen die Tools die anderen Methoden zum Erstellen von dbcontext und verwenden stattdessen die entwurfszeitfactory.</span><span class="sxs-lookup"><span data-stu-id="9eb9a-118">You can also tell the tools how to create your DbContext by implementing the `IDesignTimeDbContextFactory<TContext>` interface: If a class implementing this interface is found in either the same project as the derived `DbContext` or in the application's startup project, the tools bypass the other ways of creating the DbContext and use the design-time factory instead.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> <span data-ttu-id="9eb9a-119">Vor efcore 5,0 wurde der `args` Parameter nicht verwendet (siehe [dieses Problem][8]).</span><span class="sxs-lookup"><span data-stu-id="9eb9a-119">Prior to EFCore 5.0 the `args` parameter was unused (see [this issue][8]).</span></span>
> <span data-ttu-id="9eb9a-120">Dies wird in efcore 5,0 korrigiert, und alle zusätzlichen Entwurfszeit Argumente werden über diesen Parameter an die Anwendung übergeben.</span><span class="sxs-lookup"><span data-stu-id="9eb9a-120">This is fixed in EFCore 5.0 and any additional design-time arguments are passed into the application through that parameter.</span></span>

<span data-ttu-id="9eb9a-121">Eine Entwurfszeit Factory kann besonders nützlich sein, wenn Sie dbcontext zur Entwurfszeit anders konfigurieren müssen als zur Laufzeit, wenn der `DbContext` Konstruktor zusätzliche Parameter nicht in di registriert ist, wenn Sie di überhaupt nicht verwenden oder wenn Sie aus irgendeinem Grund keine `BuildWebHost` Methode in der Klasse der ASP.net Core Anwendung verwenden möchten `Main` .</span><span class="sxs-lookup"><span data-stu-id="9eb9a-121">A design-time factory can be especially useful if you need to configure the DbContext differently for design time than at run time, if the `DbContext` constructor takes additional parameters are not registered in DI, if you are not using DI at all, or if for some reason you prefer not to have a `BuildWebHost` method in your ASP.NET Core application's `Main` class.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
