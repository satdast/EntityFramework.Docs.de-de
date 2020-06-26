---
title: Aktualisieren von EF Core 1,0 rc1 auf RC2-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: 5500addc98a0c0eb314c4d9f9401fa301ce5c6f6
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370525"
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a><span data-ttu-id="91b84-102">Upgrade von EF Core 1,0 rc1 auf 1,0 rc2</span><span class="sxs-lookup"><span data-stu-id="91b84-102">Upgrading from EF Core 1.0 RC1 to 1.0 RC2</span></span>

<span data-ttu-id="91b84-103">Dieser Artikel enthält Anleitungen zum Verschieben einer Anwendung, die mit den RC1-Paketen in RC2 erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="91b84-103">This article provides guidance for moving an application built with the RC1 packages to RC2.</span></span>

## <a name="package-names-and-versions"></a><span data-ttu-id="91b84-104">Paketnamen und-Versionen</span><span class="sxs-lookup"><span data-stu-id="91b84-104">Package Names and Versions</span></span>

<span data-ttu-id="91b84-105">Zwischen RC1 und RC2 wurde von "Entity Framework 7" in "Entity Framework Core" geändert.</span><span class="sxs-lookup"><span data-stu-id="91b84-105">Between RC1 and RC2, we changed from "Entity Framework 7" to "Entity Framework Core".</span></span> <span data-ttu-id="91b84-106">Weitere Informationen zu den Gründen für die Änderung [dieses Beitrags von Scott Hanselman](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx)finden Sie hier.</span><span class="sxs-lookup"><span data-stu-id="91b84-106">You can read more about the reasons for the change in [this post by Scott Hanselman](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx).</span></span> <span data-ttu-id="91b84-107">Aufgrund dieser Änderung haben sich die Paketnamen von `EntityFramework.*` in `Microsoft.EntityFrameworkCore.*` und unsere Versionen von `7.0.0-rc1-final` in geändert `1.0.0-rc2-final` (oder `1.0.0-preview1-final` für-Tools).</span><span class="sxs-lookup"><span data-stu-id="91b84-107">Because of this change, our package names changed from `EntityFramework.*` to `Microsoft.EntityFrameworkCore.*` and our versions from `7.0.0-rc1-final` to `1.0.0-rc2-final` (or `1.0.0-preview1-final` for tooling).</span></span>

<span data-ttu-id="91b84-108">**Sie müssen die RC1-Pakete vollständig entfernen und dann RC2 installieren.**</span><span class="sxs-lookup"><span data-stu-id="91b84-108">**You will need to completely remove the RC1 packages and then install the RC2 ones.**</span></span> <span data-ttu-id="91b84-109">Hier ist die Zuordnung für einige gängige Pakete.</span><span class="sxs-lookup"><span data-stu-id="91b84-109">Here is the mapping for some common packages.</span></span>

| <span data-ttu-id="91b84-110">RC1-Paket</span><span class="sxs-lookup"><span data-stu-id="91b84-110">RC1 Package</span></span>                                               | <span data-ttu-id="91b84-111">RC2-Entsprechung</span><span class="sxs-lookup"><span data-stu-id="91b84-111">RC2 Equivalent</span></span>                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| <span data-ttu-id="91b84-112">EntityFramework. MicrosoftSQLServer 7.0.0-rc1-Final</span><span class="sxs-lookup"><span data-stu-id="91b84-112">EntityFramework.MicrosoftSqlServer        7.0.0-rc1-final</span></span> | <span data-ttu-id="91b84-113">Microsoft. entityframeworkcore. SqlServer 1.0.0-rc2-Final</span><span class="sxs-lookup"><span data-stu-id="91b84-113">Microsoft.EntityFrameworkCore.SqlServer         1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="91b84-114">EntityFramework. sqlite 7.0.0-rc1-Final</span><span class="sxs-lookup"><span data-stu-id="91b84-114">EntityFramework.SQLite                    7.0.0-rc1-final</span></span> | <span data-ttu-id="91b84-115">Microsoft. entityframeworkcore. sqlite 1.0.0-rc2-Final</span><span class="sxs-lookup"><span data-stu-id="91b84-115">Microsoft.EntityFrameworkCore.Sqlite            1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="91b84-116">EntityFramework7. npgsql 3.1.0-rc1-3</span><span class="sxs-lookup"><span data-stu-id="91b84-116">EntityFramework7.Npgsql                   3.1.0-rc1-3</span></span>     | <span data-ttu-id="91b84-117">Npgsql. entityframeworkcore. postgres<to be advised></span><span class="sxs-lookup"><span data-stu-id="91b84-117">NpgSql.EntityFrameworkCore.Postgres             <to be advised></span></span>      |
| <span data-ttu-id="91b84-118">EntityFramework. SqlServerCompact35 7.0.0-rc1-Final</span><span class="sxs-lookup"><span data-stu-id="91b84-118">EntityFramework.SqlServerCompact35        7.0.0-rc1-final</span></span> | <span data-ttu-id="91b84-119">Entityframeworkcore. SqlServerCompact35 1.0.0-rc2-Final</span><span class="sxs-lookup"><span data-stu-id="91b84-119">EntityFrameworkCore.SqlServerCompact35          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="91b84-120">EntityFramework. SqlServerCompact40 7.0.0-rc1-Final</span><span class="sxs-lookup"><span data-stu-id="91b84-120">EntityFramework.SqlServerCompact40        7.0.0-rc1-final</span></span> | <span data-ttu-id="91b84-121">Entityframeworkcore. SqlServerCompact40 1.0.0-rc2-Final</span><span class="sxs-lookup"><span data-stu-id="91b84-121">EntityFrameworkCore.SqlServerCompact40          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="91b84-122">EntityFramework. inMemory 7.0.0-rc1-Final</span><span class="sxs-lookup"><span data-stu-id="91b84-122">EntityFramework.InMemory                  7.0.0-rc1-final</span></span> | <span data-ttu-id="91b84-123">Microsoft. entityframeworkcore. inMemory 1.0.0-rc2-Final</span><span class="sxs-lookup"><span data-stu-id="91b84-123">Microsoft.EntityFrameworkCore.InMemory          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="91b84-124">EntityFramework. ibmdataserver 7.0.0-beta1</span><span class="sxs-lookup"><span data-stu-id="91b84-124">EntityFramework.IBMDataServer             7.0.0-beta1</span></span>     | <span data-ttu-id="91b84-125">Noch nicht für RC2 verfügbar</span><span class="sxs-lookup"><span data-stu-id="91b84-125">Not yet available for RC2</span></span>                                            |
| <span data-ttu-id="91b84-126">EntityFramework. Commands 7.0.0-rc1-Final</span><span class="sxs-lookup"><span data-stu-id="91b84-126">EntityFramework.Commands                  7.0.0-rc1-final</span></span> | <span data-ttu-id="91b84-127">Microsoft. entityframeworkcore. Tools 1.0.0-Preview1-Final</span><span class="sxs-lookup"><span data-stu-id="91b84-127">Microsoft.EntityFrameworkCore.Tools             1.0.0-preview1-final</span></span> |
| <span data-ttu-id="91b84-128">EntityFramework. MicrosoftSQLServer. Design 7.0.0-rc1-Final</span><span class="sxs-lookup"><span data-stu-id="91b84-128">EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final</span></span> | <span data-ttu-id="91b84-129">Microsoft. entityframeworkcore. SqlServer. Design 1.0.0-rc2-Final</span><span class="sxs-lookup"><span data-stu-id="91b84-129">Microsoft.EntityFrameworkCore.SqlServer.Design  1.0.0-rc2-final</span></span>      |

## <a name="namespaces"></a><span data-ttu-id="91b84-130">Namespaces</span><span class="sxs-lookup"><span data-stu-id="91b84-130">Namespaces</span></span>

<span data-ttu-id="91b84-131">Zusammen mit den Paketnamen haben sich Namespaces von `Microsoft.Data.Entity.*` in geändert `Microsoft.EntityFrameworkCore.*` .</span><span class="sxs-lookup"><span data-stu-id="91b84-131">Along with package names, namespaces changed from `Microsoft.Data.Entity.*` to `Microsoft.EntityFrameworkCore.*`.</span></span> <span data-ttu-id="91b84-132">Sie können diese Änderung mit einem Suchen/Ersetzen von `using Microsoft.Data.Entity` mit verarbeiten `using Microsoft.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="91b84-132">You can handle this change with a find/replace of `using Microsoft.Data.Entity` with `using Microsoft.EntityFrameworkCore`.</span></span>

## <a name="table-naming-convention-changes"></a><span data-ttu-id="91b84-133">Änderungen der Tabellen Benennungs Konvention</span><span class="sxs-lookup"><span data-stu-id="91b84-133">Table Naming Convention Changes</span></span>

<span data-ttu-id="91b84-134">Eine bedeutende funktionale Änderung in RC2 bestand darin, den Namen der `DbSet<TEntity>` Eigenschaft für eine bestimmte Entität als den Tabellennamen zu verwenden, dem Sie zugeordnet ist, anstatt nur den Klassennamen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="91b84-134">A significant functional change we took in RC2 was to use the name of the `DbSet<TEntity>` property for a given entity as the table name it maps to, rather than just the class name.</span></span> <span data-ttu-id="91b84-135">Weitere Informationen zu dieser Änderung finden Sie im [zugehörigen Ankündigungs Problem](https://github.com/aspnet/Announcements/issues/167).</span><span class="sxs-lookup"><span data-stu-id="91b84-135">You can read more about this change in [the related announcement issue](https://github.com/aspnet/Announcements/issues/167).</span></span>

<span data-ttu-id="91b84-136">Für vorhandene RC1-Anwendungen wird empfohlen, den folgenden Code am Anfang der Methode hinzuzufügen, `OnModelCreating` um die RC1-Benennungs Strategie beizubehalten:</span><span class="sxs-lookup"><span data-stu-id="91b84-136">For existing RC1 applications, we recommend adding the following code to the start of your `OnModelCreating` method to keep the RC1 naming strategy:</span></span>

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

<span data-ttu-id="91b84-137">Wenn Sie die neue Benennungs Strategie übernehmen möchten, empfehlen wir, die restlichen Upgradeschritte erfolgreich abzuschließen und dann den Code zu entfernen und eine Migration zum Anwenden der Tabellennamen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="91b84-137">If you want to adopt the new naming strategy, we would recommend successfully completing the rest of the upgrade steps and then removing the code and creating a migration to apply the table renames.</span></span>

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a><span data-ttu-id="91b84-138">Adddbcontext/Startup.cs-Änderungen (nur ASP.net Core-Projekte)</span><span class="sxs-lookup"><span data-stu-id="91b84-138">AddDbContext / Startup.cs Changes (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="91b84-139">In RC1 mussten Sie dem Anwendungs Dienstanbieter Entity Framework Dienste hinzufügen `Startup.ConfigureServices(...)` :</span><span class="sxs-lookup"><span data-stu-id="91b84-139">In RC1, you had to add Entity Framework services to the application service provider - in `Startup.ConfigureServices(...)`:</span></span>

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

<span data-ttu-id="91b84-140">In RC2 können Sie die Aufrufe von `AddEntityFramework()` , `AddSqlServer()` usw. entfernen:</span><span class="sxs-lookup"><span data-stu-id="91b84-140">In RC2, you can remove the calls to `AddEntityFramework()`, `AddSqlServer()`, etc.:</span></span>

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

<span data-ttu-id="91b84-141">Außerdem müssen Sie Ihrem abgeleiteten Kontext einen Konstruktor hinzufügen, der Kontextoptionen annimmt und Sie an den Basiskonstruktor übergibt.</span><span class="sxs-lookup"><span data-stu-id="91b84-141">You also need to add a constructor, to your derived context, that takes context options and passes them to the base constructor.</span></span> <span data-ttu-id="91b84-142">Dies ist erforderlich, da wir einige der beängstigenden Magie entfernt haben, die Sie hinter den Kulissen schnuppern:</span><span class="sxs-lookup"><span data-stu-id="91b84-142">This is needed because we removed some of the scary magic that snuck them in behind the scenes:</span></span>

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a><span data-ttu-id="91b84-143">Übergeben von IServiceProvider</span><span class="sxs-lookup"><span data-stu-id="91b84-143">Passing in an IServiceProvider</span></span>

<span data-ttu-id="91b84-144">Wenn Sie über RC1-Code verfügen, `IServiceProvider` der ein an den Kontext übergibt, wird dieser nun in verschoben `DbContextOptions` , anstatt einen separaten Konstruktorparameter zu sein.</span><span class="sxs-lookup"><span data-stu-id="91b84-144">If you have RC1 code that passes an `IServiceProvider` to the context, this has now moved to `DbContextOptions`, rather than being a separate constructor parameter.</span></span> <span data-ttu-id="91b84-145">Verwenden `DbContextOptionsBuilder.UseInternalServiceProvider(...)` Sie, um den Dienstanbieter festzulegen.</span><span class="sxs-lookup"><span data-stu-id="91b84-145">Use `DbContextOptionsBuilder.UseInternalServiceProvider(...)` to set the service provider.</span></span>

### <a name="testing"></a><span data-ttu-id="91b84-146">Testen</span><span class="sxs-lookup"><span data-stu-id="91b84-146">Testing</span></span>

<span data-ttu-id="91b84-147">Das häufigste Szenario hierfür war, den Umfang einer inMemory-Datenbank beim Testen zu steuern.</span><span class="sxs-lookup"><span data-stu-id="91b84-147">The most common scenario for doing this was to control the scope of an InMemory database when testing.</span></span> <span data-ttu-id="91b84-148">Ein Beispiel für die Verwendung von rc2 finden Sie im Artikel aktualisierte [Tests](testing/index.md) .</span><span class="sxs-lookup"><span data-stu-id="91b84-148">See the updated [Testing](testing/index.md) article for an example of doing this with RC2.</span></span>

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a><span data-ttu-id="91b84-149">Auflösen interner Dienste vom Anwendungs Dienstanbieter (nur ASP.net Core Projekte)</span><span class="sxs-lookup"><span data-stu-id="91b84-149">Resolving Internal Services from Application Service Provider (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="91b84-150">Wenn Sie über eine ASP.net Core Anwendung verfügen und EF möchten, dass interne Dienste vom Anwendungs Dienstanbieter aufgelöst werden, gibt es eine Überladung von, mit der `AddDbContext` Sie Folgendes konfigurieren können:</span><span class="sxs-lookup"><span data-stu-id="91b84-150">If you have an ASP.NET Core application and you want EF to resolve internal services from the application service provider, there is an overload of `AddDbContext` that allows you to configure this:</span></span>

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider));
```

> [!WARNING]  
> <span data-ttu-id="91b84-151">Es wird empfohlen, dass EF intern seine eigenen Dienste verwaltet, es sei denn, Sie haben einen Grund, die internen EF-Dienste in Ihrem Anwendungs Dienstanbieter zu kombinieren.</span><span class="sxs-lookup"><span data-stu-id="91b84-151">We recommend allowing EF to internally manage its own services, unless you have a reason to combine the internal EF services into your application service provider.</span></span> <span data-ttu-id="91b84-152">Der Hauptgrund hierfür ist, dass Sie den Anwendungs Dienstanbieter verwenden, um Dienste zu ersetzen, die von EF intern verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="91b84-152">The main reason you may want to do this is to use your application service provider to replace services that EF uses internally</span></span>

## <a name="dnx-commands--net-core-cli-aspnet-core-projects-only"></a><span data-ttu-id="91b84-153">DNX-Befehle => .net Core-CLI (nur ASP.net Core Projekte)</span><span class="sxs-lookup"><span data-stu-id="91b84-153">DNX Commands => .NET Core CLI (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="91b84-154">Wenn Sie zuvor die `dnx ef` Befehle für ASP.net 5-Projekte verwendet haben, wurden diese nun in `dotnet ef` Befehle verschoben.</span><span class="sxs-lookup"><span data-stu-id="91b84-154">If you previously used the `dnx ef` commands for ASP.NET 5 projects, these have now moved to `dotnet ef` commands.</span></span> <span data-ttu-id="91b84-155">Dieselbe Befehlssyntax gilt weiterhin.</span><span class="sxs-lookup"><span data-stu-id="91b84-155">The same command syntax still applies.</span></span> <span data-ttu-id="91b84-156">Sie können `dotnet ef --help` für Syntax Informationen verwenden.</span><span class="sxs-lookup"><span data-stu-id="91b84-156">You can use `dotnet ef --help` for syntax information.</span></span>

<span data-ttu-id="91b84-157">Die Art und Weise, in der Befehle registriert werden, wurde in RC2 geändert, weil DNX durch den .net Core-CLI ersetzt wurde.</span><span class="sxs-lookup"><span data-stu-id="91b84-157">The way commands are registered has changed in RC2, due to DNX being replaced by the .NET Core CLI.</span></span> <span data-ttu-id="91b84-158">Befehle sind nun in einem `tools` Abschnitt in registriert `project.json` :</span><span class="sxs-lookup"><span data-stu-id="91b84-158">Commands are now registered in a `tools` section in `project.json`:</span></span>

``` json
"tools": {
  "Microsoft.EntityFrameworkCore.Tools": {
    "version": "1.0.0-preview1-final",
    "imports": [
      "portable-net45+win8+dnxcore50",
      "portable-net45+win8"
    ]
  }
}
```

> [!TIP]  
> <span data-ttu-id="91b84-159">Wenn Sie Visual Studio verwenden, können Sie jetzt die Paket-Manager-Konsole verwenden, um EF-Befehle für ASP.net Core Projekte auszuführen (Dies wurde in RC1 nicht unterstützt).</span><span class="sxs-lookup"><span data-stu-id="91b84-159">If you use Visual Studio, you can now use Package Manager Console to run EF commands for ASP.NET Core projects (this was not supported in RC1).</span></span> <span data-ttu-id="91b84-160">`tools`Um dies zu tun, müssen Sie die Befehle im Abschnitt von immer noch registrieren `project.json` .</span><span class="sxs-lookup"><span data-stu-id="91b84-160">You still need to register the commands in the `tools` section of `project.json` to do this.</span></span>

## <a name="package-manager-commands-require-powershell-5"></a><span data-ttu-id="91b84-161">Paket-Manager-Befehle erfordern PowerShell 5</span><span class="sxs-lookup"><span data-stu-id="91b84-161">Package Manager Commands Require PowerShell 5</span></span>

<span data-ttu-id="91b84-162">Wenn Sie die Entity Framework-Befehle in der Paket-Manager-Konsole in Visual Studio verwenden, müssen Sie sicherstellen, dass PowerShell 5 installiert ist.</span><span class="sxs-lookup"><span data-stu-id="91b84-162">If you use the Entity Framework commands in Package Manager Console in Visual Studio, then you will need to ensure you have PowerShell 5 installed.</span></span> <span data-ttu-id="91b84-163">Dies ist eine temporäre Anforderung, die in der nächsten Version entfernt wird (Weitere Informationen finden Sie unter [Problem #5327](https://github.com/aspnet/EntityFramework/issues/5327) ).</span><span class="sxs-lookup"><span data-stu-id="91b84-163">This is a temporary requirement that will be removed in the next release (see [issue #5327](https://github.com/aspnet/EntityFramework/issues/5327) for more details).</span></span>

## <a name="using-imports-in-projectjson"></a><span data-ttu-id="91b84-164">Verwenden von "Imports" in project.js</span><span class="sxs-lookup"><span data-stu-id="91b84-164">Using "imports" in project.json</span></span>

<span data-ttu-id="91b84-165">Einige der Abhängigkeiten von EF Core unterstützen .NET Standard noch nicht.</span><span class="sxs-lookup"><span data-stu-id="91b84-165">Some of EF Core's dependencies do not support .NET Standard yet.</span></span> <span data-ttu-id="91b84-166">EF Core in .NET Standard und in .net Core-Projekten erfordert möglicherweise das Hinzufügen von "Imports" zum project.jsvon als vorübergehende Problem Umgehung.</span><span class="sxs-lookup"><span data-stu-id="91b84-166">EF Core in .NET Standard and .NET Core projects may require adding "imports" to project.json as a temporary workaround.</span></span>

<span data-ttu-id="91b84-167">Beim Hinzufügen von EF wird in der nuget-Wiederherstellung die folgende Fehlermeldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="91b84-167">When adding EF, NuGet restore will display this error message:</span></span>

``` Console
Package Ix-Async 1.2.5 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Ix-Async 1.2.5 supports:
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8 (.NETPortable,Version=v0.0,Profile=Profile78)
Package Remotion.Linq 2.0.2 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Remotion.Linq 2.0.2 supports:
  - net35 (.NETFramework,Version=v3.5)
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
```

<span data-ttu-id="91b84-168">Die Problem Umgehung besteht darin, das Portable Profil "Portable-net451 + win8" manuell zu importieren.</span><span class="sxs-lookup"><span data-stu-id="91b84-168">The workaround is to manually import the portable profile "portable-net451+win8".</span></span> <span data-ttu-id="91b84-169">Dies erzwingt, dass nuget diese Binärdateien, die diesem entsprechen, als kompatibles Framework mit .NET Standard behandelt, auch wenn dies nicht der Fall ist.</span><span class="sxs-lookup"><span data-stu-id="91b84-169">This forces NuGet to treat this binaries that match this provide as a compatible framework with .NET Standard, even though they are not.</span></span> <span data-ttu-id="91b84-170">Obwohl "Portable-net451 + win8" 100 nicht mit .NET Standard kompatibel ist, ist es für den Übergang von PCL zu .NET Standard kompatibel.</span><span class="sxs-lookup"><span data-stu-id="91b84-170">Although "portable-net451+win8" is not 100% compatible with .NET Standard, it is compatible enough for the transition from PCL to .NET Standard.</span></span> <span data-ttu-id="91b84-171">Importe können entfernt werden, wenn die Abhängigkeiten von EF schließlich auf .NET Standard aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="91b84-171">Imports can be removed when EF's dependencies eventually upgrade to .NET Standard.</span></span>

<span data-ttu-id="91b84-172">Mehrere Frameworks können "Imports" in der Array Syntax hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="91b84-172">Multiple frameworks can be added to "imports" in array syntax.</span></span> <span data-ttu-id="91b84-173">Andere Importe sind möglicherweise erforderlich, wenn Sie Ihrem Projekt weitere Bibliotheken hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="91b84-173">Other imports may be necessary if you add additional libraries to your project.</span></span>

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

<span data-ttu-id="91b84-174">Siehe [Problem #5176](https://github.com/aspnet/EntityFramework/issues/5176).</span><span class="sxs-lookup"><span data-stu-id="91b84-174">See [Issue #5176](https://github.com/aspnet/EntityFramework/issues/5176).</span></span>
