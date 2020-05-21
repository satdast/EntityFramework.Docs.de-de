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
# <a name="design-time-dbcontext-creation"></a>DbContext-Instanzerstellung zur Entwurfszeit

Einige der EF Core Tools-Befehle (z. b. die [Migrations][1] Befehle) erfordern, dass eine abgeleitete `DbContext` Instanz zur Entwurfszeit erstellt wird, um Details zu den Entitäts Typen der Anwendung und deren Zuordnung zu einem Datenbankschema zu erfassen. In den meisten Fällen ist es wünschenswert, dass die, die `DbContext` erstellt wird, auf ähnliche Weise konfiguriert wird, wie Sie zur [Laufzeit konfiguriert][2]wird.

Es gibt verschiedene Möglichkeiten, die Tools zu erstellen `DbContext` :

## <a name="from-application-services"></a>Aus Anwendungsdiensten

Wenn das Startprojekt den [ASP.net Core Webhost][3] oder den [generischen .net Core-Host][4]verwendet, versuchen die Tools, das dbcontext-Objekt vom Dienstanbieter der Anwendung abzurufen.

Die Tools versuchen zunächst, den Dienstanbieter `Program.CreateHostBuilder()` abzurufen, indem Sie aufrufen und dann auf die- `Build()` `Services` Eigenschaft zugreifen.

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> Wenn Sie eine neue ASP.net Core Anwendung erstellen, ist dieser Hook standardmäßig enthalten.

Die `DbContext` selbst und alle Abhängigkeiten im Konstruktor müssen als Dienste im Dienstanbieter der Anwendung registriert werden. Dies kann problemlos erreicht werden, indem [ein Konstruktor für den verwendet wird `DbContext` , der eine Instanz von `DbContextOptions<TContext>` als Argument annimmt][5] und die- [ `AddDbContext<TContext>` Methode][6]verwendet.

## <a name="using-a-constructor-with-no-parameters"></a>Verwenden eines Konstruktors ohne Parameter

Wenn dbcontext vom Anwendungs Dienstanbieter nicht abgerufen werden kann, suchen die Tools im Projekt nach dem abgeleiteten `DbContext` Typ. Anschließend wird versucht, eine Instanz mit einem Konstruktor ohne Parameter zu erstellen. Dies kann der Standardkonstruktor sein, wenn `DbContext` mithilfe der- [`OnConfiguring`][7] Methode konfiguriert wird.

## <a name="from-a-design-time-factory"></a>Aus einer Entwurfszeit Factory

Sie können den Tools auch mitteilen, wie Sie dbcontext durch Implementieren der- `IDesignTimeDbContextFactory<TContext>` Schnittstelle erstellen: Wenn eine Klasse, die diese Schnittstelle implementiert, sich im gleichen Projekt wie die abgeleitete `DbContext` oder im Startprojekt der Anwendung befindet, umgehen die Tools die anderen Methoden zum Erstellen von dbcontext und verwenden stattdessen die entwurfszeitfactory.

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> Vor efcore 5,0 wurde der `args` Parameter nicht verwendet (siehe [dieses Problem][8]).
> Dies wird in efcore 5,0 korrigiert, und alle zusätzlichen Entwurfszeit Argumente werden über diesen Parameter an die Anwendung übergeben.

Eine Entwurfszeit Factory kann besonders nützlich sein, wenn Sie dbcontext zur Entwurfszeit anders konfigurieren müssen als zur Laufzeit, wenn der `DbContext` Konstruktor zusätzliche Parameter nicht in di registriert ist, wenn Sie di überhaupt nicht verwenden oder wenn Sie aus irgendeinem Grund keine `BuildWebHost` Methode in der Klasse der ASP.net Core Anwendung verwenden möchten `Main` .

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
