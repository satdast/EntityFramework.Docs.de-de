---
title: Erstellung von dbcontext zur Entwurfszeit EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: 0b0271dcabea63a2529c091cc14cb9059d56ac8d
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672958"
---
# <a name="design-time-dbcontext-creation"></a><span data-ttu-id="b04e5-102">DbContext-Instanzerstellung zur Entwurfszeit</span><span class="sxs-lookup"><span data-stu-id="b04e5-102">Design-time DbContext Creation</span></span>

<span data-ttu-id="b04e5-103">Einige der EF Core Tools-Befehle (z. b. die [Migrations][1] Befehle) erfordern, dass eine abgeleitete `DbContext` Instanz zur Entwurfszeit erstellt wird, um Details zu den Entitäts Typen der Anwendung und deren Zuordnung zu einem Datenbankschema zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="b04e5-103">Some of the EF Core Tools commands (for example, the [Migrations][1] commands) require a derived `DbContext` instance to be created at design time in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="b04e5-104">In den meisten Fällen ist es wünschenswert, dass die, die `DbContext` erstellt wird, auf ähnliche Weise konfiguriert wird, wie Sie zur [Laufzeit konfiguriert][2]wird.</span><span class="sxs-lookup"><span data-stu-id="b04e5-104">In most cases, it is desirable that the `DbContext` thereby created is configured in a similar way to how it would be [configured at run time][2].</span></span>

<span data-ttu-id="b04e5-105">Es gibt verschiedene Möglichkeiten, die Tools zu erstellen `DbContext` :</span><span class="sxs-lookup"><span data-stu-id="b04e5-105">There are various ways the tools try to create the `DbContext`:</span></span>

## <a name="from-application-services"></a><span data-ttu-id="b04e5-106">Aus Anwendungsdiensten</span><span class="sxs-lookup"><span data-stu-id="b04e5-106">From application services</span></span>

<span data-ttu-id="b04e5-107">Wenn das Startprojekt den [ASP.net Core Webhost][3] oder den [generischen .net Core-Host][4]verwendet, versuchen die Tools, das dbcontext-Objekt vom Dienstanbieter der Anwendung abzurufen.</span><span class="sxs-lookup"><span data-stu-id="b04e5-107">If your startup project uses the [ASP.NET Core Web Host][3] or [.NET Core Generic Host][4], the tools try to obtain the DbContext object from the application's service provider.</span></span>

<span data-ttu-id="b04e5-108">Die Tools versuchen zunächst, den Dienstanbieter `Program.CreateHostBuilder()` abzurufen, indem Sie aufrufen und dann auf die- `Build()` `Services` Eigenschaft zugreifen.</span><span class="sxs-lookup"><span data-stu-id="b04e5-108">The tools first try to obtain the service provider by invoking `Program.CreateHostBuilder()`, calling `Build()`, then accessing the `Services` property.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> <span data-ttu-id="b04e5-109">Wenn Sie eine neue ASP.net Core Anwendung erstellen, ist dieser Hook standardmäßig enthalten.</span><span class="sxs-lookup"><span data-stu-id="b04e5-109">When you create a new ASP.NET Core application, this hook is included by default.</span></span>

<span data-ttu-id="b04e5-110">Die `DbContext` selbst und alle Abhängigkeiten im Konstruktor müssen als Dienste im Dienstanbieter der Anwendung registriert werden.</span><span class="sxs-lookup"><span data-stu-id="b04e5-110">The `DbContext` itself and any dependencies in its constructor need to be registered as services in the application's service provider.</span></span> <span data-ttu-id="b04e5-111">Dies kann problemlos erreicht werden, indem [ein Konstruktor für den verwendet wird `DbContext` , der eine Instanz von `DbContextOptions<TContext>` als Argument annimmt][5] und die- [ `AddDbContext<TContext>` Methode][6]verwendet.</span><span class="sxs-lookup"><span data-stu-id="b04e5-111">This can be easily achieved by having [a constructor on the `DbContext` that takes an instance of `DbContextOptions<TContext>` as an argument][5] and using the [`AddDbContext<TContext>` method][6].</span></span>

## <a name="using-a-constructor-with-no-parameters"></a><span data-ttu-id="b04e5-112">Verwenden eines Konstruktors ohne Parameter</span><span class="sxs-lookup"><span data-stu-id="b04e5-112">Using a constructor with no parameters</span></span>

<span data-ttu-id="b04e5-113">Wenn dbcontext vom Anwendungs Dienstanbieter nicht abgerufen werden kann, suchen die Tools im Projekt nach dem abgeleiteten `DbContext` Typ.</span><span class="sxs-lookup"><span data-stu-id="b04e5-113">If the DbContext can't be obtained from the application service provider, the tools look for the derived `DbContext` type inside the project.</span></span> <span data-ttu-id="b04e5-114">Anschließend wird versucht, eine Instanz mit einem Konstruktor ohne Parameter zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b04e5-114">Then they try to create an instance using a constructor with no parameters.</span></span> <span data-ttu-id="b04e5-115">Dies kann der Standardkonstruktor sein, wenn `DbContext` mithilfe der- [`OnConfiguring`][7] Methode konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="b04e5-115">This can be the default constructor if the `DbContext` is configured using the [`OnConfiguring`][7] method.</span></span>

## <a name="from-a-design-time-factory"></a><span data-ttu-id="b04e5-116">Aus einer Entwurfszeit Factory</span><span class="sxs-lookup"><span data-stu-id="b04e5-116">From a design-time factory</span></span>

<span data-ttu-id="b04e5-117">Sie können den Tools auch mitteilen, wie Sie dbcontext durch Implementieren der- `IDesignTimeDbContextFactory<TContext>` Schnittstelle erstellen: Wenn eine Klasse, die diese Schnittstelle implementiert, sich im gleichen Projekt wie die abgeleitete `DbContext` oder im Startprojekt der Anwendung befindet, umgehen die Tools die anderen Methoden zum Erstellen von dbcontext und verwenden stattdessen die entwurfszeitfactory.</span><span class="sxs-lookup"><span data-stu-id="b04e5-117">You can also tell the tools how to create your DbContext by implementing the `IDesignTimeDbContextFactory<TContext>` interface: If a class implementing this interface is found in either the same project as the derived `DbContext` or in the application's startup project, the tools bypass the other ways of creating the DbContext and use the design-time factory instead.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> <span data-ttu-id="b04e5-118">Vor efcore 5,0 wurde der `args` Parameter nicht verwendet (siehe [dieses Problem][8]).</span><span class="sxs-lookup"><span data-stu-id="b04e5-118">Prior to EFCore 5.0 the `args` parameter was unused (see [this issue][8]).</span></span>
> <span data-ttu-id="b04e5-119">Dies wird in efcore 5,0 korrigiert, und alle zusätzlichen Entwurfszeit Argumente werden über diesen Parameter an die Anwendung übergeben.</span><span class="sxs-lookup"><span data-stu-id="b04e5-119">This is fixed in EFCore 5.0 and any additional design-time arguments are passed into the application through that parameter.</span></span>

<span data-ttu-id="b04e5-120">Eine Entwurfszeit Factory kann besonders nützlich sein, wenn Sie dbcontext zur Entwurfszeit anders konfigurieren müssen als zur Laufzeit, wenn der `DbContext` Konstruktor zusätzliche Parameter nicht in di registriert ist, wenn Sie di überhaupt nicht verwenden oder wenn Sie aus irgendeinem Grund keine `BuildWebHost` Methode in der Klasse der ASP.net Core Anwendung verwenden möchten `Main` .</span><span class="sxs-lookup"><span data-stu-id="b04e5-120">A design-time factory can be especially useful if you need to configure the DbContext differently for design time than at run time, if the `DbContext` constructor takes additional parameters are not registered in DI, if you are not using DI at all, or if for some reason you prefer not to have a `BuildWebHost` method in your ASP.NET Core application's `Main` class.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
