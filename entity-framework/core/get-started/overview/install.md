---
title: Installieren von Entity Framework Core
description: Installationsanweisungen für Entity Framework Core
author: bricelam
ms.date: 08/06/2017
uid: core/get-started/overview/install
ms.openlocfilehash: 039e5339a6dba57f6a8f33acba6e467f8d6439c9
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431168"
---
# <a name="installing-entity-framework-core"></a><span data-ttu-id="28a97-103">Installieren von Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="28a97-103">Installing Entity Framework Core</span></span>

## <a name="prerequisites"></a><span data-ttu-id="28a97-104">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="28a97-104">Prerequisites</span></span>

* <span data-ttu-id="28a97-105">EF Core ist eine [.NET Standard 2.0](/dotnet/standard/net-standard)-Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="28a97-105">EF Core is a [.NET Standard 2.0](/dotnet/standard/net-standard) library.</span></span> <span data-ttu-id="28a97-106">D.h., dass EF Core eine .NET-Implementierung erfordert, die .NET Standard 2.0 unterstützt.</span><span class="sxs-lookup"><span data-stu-id="28a97-106">So EF Core requires a .NET implementation that supports .NET Standard 2.0 to run.</span></span> <span data-ttu-id="28a97-107">Auf EF Core kann auch von anderen .NET Standard 2.0-Bibliotheken verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="28a97-107">EF Core can also be referenced by other .NET Standard 2.0 libraries.</span></span>

* <span data-ttu-id="28a97-108">Sie können EF Core z.B. verwenden, um Apps für .NET Core zu entwickeln.</span><span class="sxs-lookup"><span data-stu-id="28a97-108">For example, you can use EF Core to develop apps that target .NET Core.</span></span> <span data-ttu-id="28a97-109">Zum Erstellen von .NET Core-Apps ist das [.NET Core SDK](https://dotnet.microsoft.com/download) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="28a97-109">Building .NET Core apps requires the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="28a97-110">Optional können Sie auch eine Entwicklungsumgebung wie [Visual Studio](https://visualstudio.microsoft.com/vs), [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac) oder [Visual Studio Code](https://code.visualstudio.com) verwenden.</span><span class="sxs-lookup"><span data-stu-id="28a97-110">Optionally, you can also use a development environment like [Visual Studio](https://visualstudio.microsoft.com/vs), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac), or [Visual Studio Code](https://code.visualstudio.com).</span></span> <span data-ttu-id="28a97-111">Weitere Informationen finden Sie unter [Erste Schritte mit .NET Core](/dotnet/core/get-started).</span><span class="sxs-lookup"><span data-stu-id="28a97-111">For more information, check [Getting Started with .NET Core](/dotnet/core/get-started).</span></span>

* <span data-ttu-id="28a97-112">Mit EF Core können Sie in Visual Studio unter Windows Anwendungen entwickeln.</span><span class="sxs-lookup"><span data-stu-id="28a97-112">You can use EF Core to develop applications on Windows using Visual Studio.</span></span> <span data-ttu-id="28a97-113">Es wird empfohlen, die aktuelle Version von [Visual Studio](https://visualstudio.microsoft.com/vs) zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="28a97-113">The latest version of [Visual Studio](https://visualstudio.microsoft.com/vs) is recommended.</span></span>

* <span data-ttu-id="28a97-114">EF Core kann auch in anderen .NET-Implementierungen wie [Xamarin](https://dotnet.microsoft.com/apps/xamarin) oder .NET Native ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="28a97-114">EF Core can run on other .NET implementations like [Xamarin](https://dotnet.microsoft.com/apps/xamarin) and .NET Native.</span></span> <span data-ttu-id="28a97-115">Diese Implementierungen haben jedoch Laufzeiteinschränkungen, die sich auf die Funktionsweise von EF Core in Ihrer App auswirken können.</span><span class="sxs-lookup"><span data-stu-id="28a97-115">But in practice those implementations have runtime limitations that may affect how well EF Core works on your app.</span></span> <span data-ttu-id="28a97-116">Weitere Informationen finden Sie unter [.NET implementations supported by EF Core (.NET-Implementierungen, die von EF Core unterstützt werden)](xref:core/miscellaneous/platforms).</span><span class="sxs-lookup"><span data-stu-id="28a97-116">For more information, see [.NET implementations supported by EF Core](xref:core/miscellaneous/platforms).</span></span>

* <span data-ttu-id="28a97-117">Es kann zudem sein, dass unterschiedliche Datenbankanbieter bestimmte Datenbank-Engineversionen, .NET-Implementierungen oder Betriebssysteme erfordern.</span><span class="sxs-lookup"><span data-stu-id="28a97-117">Finally, different database providers may require specific database engine versions, .NET implementations, or operating systems.</span></span> <span data-ttu-id="28a97-118">Achten Sie darauf, dass ein [EF Core-Datenbankanbieter](xref:core/providers/index) zur Verfügung steht, der eine für Ihre Anwendung geeignete Umgebung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="28a97-118">Make sure an [EF Core database provider](xref:core/providers/index) is available that supports the right environment for your application.</span></span>

## <a name="get-the-entity-framework-core-runtime"></a><span data-ttu-id="28a97-119">Abrufen der Entity Framework Core-Runtime</span><span class="sxs-lookup"><span data-stu-id="28a97-119">Get the Entity Framework Core runtime</span></span>

<span data-ttu-id="28a97-120">Installieren Sie das NuGet-Paket für den Datenbankanbieter, den Sie verwenden möchten, um einer Anwendung EF Core hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="28a97-120">To add EF Core to an application, install the NuGet package for the database provider you want to use.</span></span>

<span data-ttu-id="28a97-121">Wenn Sie eine ASP.NET Core-Anwendung erstellen, müssen Sie keine In-Memory- und SQL Server-Anbieter installieren.</span><span class="sxs-lookup"><span data-stu-id="28a97-121">If you're building an ASP.NET Core application, you don't need to install the in-memory and SQL Server providers.</span></span> <span data-ttu-id="28a97-122">Diese Anbieter sind neben der EF Core-Runtime in der aktuellen Versionen von ASP.NET Core enthalten.</span><span class="sxs-lookup"><span data-stu-id="28a97-122">Those providers are included in current versions of ASP.NET Core, alongside the EF Core runtime.</span></span>

<span data-ttu-id="28a97-123">Verwenden Sie die .NET Core-CLI (Command Line Interface, Befehlszeilenschnittstelle), das Dialogfeld für den Visual Studio-Paket-Manager oder die Visual Studio-Paket-Manager-Konsole, um NuGet-Pakete zu installieren oder zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="28a97-123">To install or update NuGet packages, you can use the .NET Core command-line interface (CLI), the Visual Studio Package Manager Dialog, or the Visual Studio Package Manager Console.</span></span>

### <a name="net-core-cli"></a><span data-ttu-id="28a97-124">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="28a97-124">.NET Core CLI</span></span>

* <span data-ttu-id="28a97-125">Verwenden Sie den folgenden .NET Core-CLI-Befehl über die Befehlszeile des Betriebssystems, um den EF Core-SQL Server-Anbieter zu installieren oder zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="28a97-125">Use the following .NET Core CLI command from the operating system's command line to install or update the EF Core SQL Server provider:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="28a97-126">Mit dem Modifizierer `-v` können Sie eine bestimmte Version im Befehl `dotnet add package` angeben.</span><span class="sxs-lookup"><span data-stu-id="28a97-126">You can indicate a specific version in the `dotnet add package` command, using the `-v` modifier.</span></span> <span data-ttu-id="28a97-127">Wenn Sie beispielsweise EF Core 2.2.0-Pakete installieren möchten, fügen Sie `-v 2.2.0` an den Befehl an.</span><span class="sxs-lookup"><span data-stu-id="28a97-127">For example, to install EF Core 2.2.0 packages, append `-v 2.2.0` to the command.</span></span>

<span data-ttu-id="28a97-128">Weitere Informationen finden Sie unter [Tools für die .NET Core-Befehlszeilenschnittstelle](/dotnet/core/tools/).</span><span class="sxs-lookup"><span data-stu-id="28a97-128">For more information, see [.NET command-line interface (CLI) tools](/dotnet/core/tools/).</span></span>

### <a name="visual-studio-nuget-package-manager-dialog"></a><span data-ttu-id="28a97-129">Dialogfeld für den NuGet-Paket-Manager in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28a97-129">Visual Studio NuGet Package Manager Dialog</span></span>

* <span data-ttu-id="28a97-130">Klicken Sie im Visual Studio-Menü auf **Projekt > NuGet-Pakete verwalten**.</span><span class="sxs-lookup"><span data-stu-id="28a97-130">From the Visual Studio menu, select **Project > Manage NuGet Packages**</span></span>

* <span data-ttu-id="28a97-131">Klicken Sie auf **Durchsuchen** oder die Registerkarte **Updates**.</span><span class="sxs-lookup"><span data-stu-id="28a97-131">Click on the **Browse** or the **Updates** tab</span></span>

* <span data-ttu-id="28a97-132">Wählen Sie das `Microsoft.EntityFrameworkCore.SqlServer`-Paket aus, und bestätigen Sie, um den SQL Server-Anbieter zu installieren oder zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="28a97-132">To install or update the SQL Server provider, select the `Microsoft.EntityFrameworkCore.SqlServer` package, and confirm.</span></span>

<span data-ttu-id="28a97-133">Weitere Informationen finden Sie im [Dialogfeld des NuGet-Paket-Managers](/nuget/tools/package-manager-ui).</span><span class="sxs-lookup"><span data-stu-id="28a97-133">For more information, see [NuGet Package Manager Dialog](/nuget/tools/package-manager-ui).</span></span>

### <a name="visual-studio-nuget-package-manager-console"></a><span data-ttu-id="28a97-134">NuGet-Paket-Manager-Konsole (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="28a97-134">Visual Studio NuGet Package Manager Console</span></span>

* <span data-ttu-id="28a97-135">Wählen Sie im Visual Studio-Menü **Extras > NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="28a97-135">From the Visual Studio menu, select **Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="28a97-136">Führen Sie den folgenden Befehl in der Paket-Manager-Konsole aus, um den SQL Server-Anbieter zu installieren:</span><span class="sxs-lookup"><span data-stu-id="28a97-136">To install the SQL Server provider, run the following command in the Package Manager Console:</span></span>

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="28a97-137">Verwenden Sie den `Update-Package`-Befehl, um den Anbieter zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="28a97-137">To update the provider, use the `Update-Package` command.</span></span>

* <span data-ttu-id="28a97-138">Um eine bestimmte Version anzugeben, können Sie den Modifizierer `-Version` verwenden.</span><span class="sxs-lookup"><span data-stu-id="28a97-138">To specify a specific version, use the `-Version` modifier.</span></span> <span data-ttu-id="28a97-139">Wenn Sie beispielsweise EF Core 2.2.0-Pakete installieren möchten, fügen Sie `-Version 2.2.0` an den Befehl an.</span><span class="sxs-lookup"><span data-stu-id="28a97-139">For example, to install EF Core 2.2.0 packages, append `-Version 2.2.0` to the commands</span></span>

<span data-ttu-id="28a97-140">Weitere Informationen finden Sie im Artikel zur [Paket-Manager-Konsole](/nuget/tools/package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="28a97-140">For more information, see [Package Manager Console](/nuget/tools/package-manager-console).</span></span>

## <a name="get-the-entity-framework-core-tools"></a><span data-ttu-id="28a97-141">Installieren der Entity Framework Core-Tools</span><span class="sxs-lookup"><span data-stu-id="28a97-141">Get the Entity Framework Core tools</span></span>

<span data-ttu-id="28a97-142">Sie können Tools installieren, die Tasks im Zusammenhang mit EF Core in Ihrem Projekt ausführen, z.B. das Erstellen und Durchführen von Datenbankmigrationen oder das Erstellen eines EF Core-Modells basierend auf einer vorhandenen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="28a97-142">You can install tools to carry out EF Core-related tasks in your project, like creating and applying database migrations, or creating an EF Core model based on an existing database.</span></span>

<span data-ttu-id="28a97-143">Es sind zwei Sätze von Tools verfügbar:</span><span class="sxs-lookup"><span data-stu-id="28a97-143">Two sets of tools are available:</span></span>

* <span data-ttu-id="28a97-144">Die [.NET Core-CLI-Tools](xref:core/cli/dotnet) können unter Windows, Linux und macOS verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="28a97-144">The [.NET Core command-line interface (CLI) tools](xref:core/cli/dotnet) can be used on Windows, Linux, or macOS.</span></span> <span data-ttu-id="28a97-145">Diese Befehle beginnen mit `dotnet ef`.</span><span class="sxs-lookup"><span data-stu-id="28a97-145">These commands begin with `dotnet ef`.</span></span>

* <span data-ttu-id="28a97-146">Die [Tools der Paket-Manager-Konsole (PMC-Tools)](xref:core/cli/powershell) werden in Visual Studio unter Windows ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="28a97-146">The [Package Manager Console (PMC) tools](xref:core/cli/powershell) run in Visual Studio on Windows.</span></span> <span data-ttu-id="28a97-147">Diese Befehlen beginnen mit einem Verb, z.B. `Add-Migration`, `Update-Database`.</span><span class="sxs-lookup"><span data-stu-id="28a97-147">These commands start with a verb, for example `Add-Migration`, `Update-Database`.</span></span>

<span data-ttu-id="28a97-148">Obwohl Sie die `dotnet ef`-Befehle auch über die Paket-Manager-Konsole verwenden können, wird empfohlen, die Tools der Paket-Manager-Konsole zu verwenden, wenn Sie Visual Studio nutzen:</span><span class="sxs-lookup"><span data-stu-id="28a97-148">Although you can also use the `dotnet ef` commands from the Package Manager Console, it's recommended to use the Package Manager Console tools when you're using Visual Studio:</span></span>

* <span data-ttu-id="28a97-149">Sie werden automatisch mit dem aktuellen in der PMC in Visual Studio ausgewählten Projekt ausgeführt, ohne dass manuell zwischen den Verzeichnissen gewechselt werden muss.</span><span class="sxs-lookup"><span data-stu-id="28a97-149">They automatically work with the current project selected in the PMC in Visual Studio, without requiring manually switching directories.</span></span>

* <span data-ttu-id="28a97-150">Nach Abschluss des Befehls öffnen sie automatisch Dateien, die von den Befehlen in Visual Studio generiert wurden.</span><span class="sxs-lookup"><span data-stu-id="28a97-150">They automatically open files generated by the commands in Visual Studio after the command is completed.</span></span>

<a name="cli"></a>

### <a name="get-the-net-core-cli-tools"></a><span data-ttu-id="28a97-151">Installieren von .NET Core-CLI-Tools</span><span class="sxs-lookup"><span data-stu-id="28a97-151">Get the .NET Core CLI tools</span></span>

<span data-ttu-id="28a97-152">.NET Core-CLI-Tools erfordern das .NET Core SDK, das bereits unter [Erforderliche Komponenten](#prerequisites) erwähnt wurde.</span><span class="sxs-lookup"><span data-stu-id="28a97-152">.NET Core CLI tools require the .NET Core SDK, mentioned earlier in [Prerequisites](#prerequisites).</span></span>

* <span data-ttu-id="28a97-153">`dotnet ef` muss als globales oder lokales Tool installiert werden.</span><span class="sxs-lookup"><span data-stu-id="28a97-153">`dotnet ef` must be installed as a global or local tool.</span></span> <span data-ttu-id="28a97-154">Die meisten Entwickler bevorzugen das Installieren von `dotnet ef` als globales Tool mithilfe des folgenden Befehls:</span><span class="sxs-lookup"><span data-stu-id="28a97-154">Most developers prefer installing `dotnet ef` as a global tool using the following command:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

  <span data-ttu-id="28a97-155">`dotnet ef` kann auch als lokales Tool verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="28a97-155">`dotnet ef` can also be used as a local tool.</span></span> <span data-ttu-id="28a97-156">Für die Verwendung als lokales Tool stellen Sie die Abhängigkeiten eines Projekts wieder her, das das Tool mithilfe einer [Toolmanifestdatei](/dotnet/core/tools/global-tools#install-a-local-tool) als Toolabhängigkeit deklariert.</span><span class="sxs-lookup"><span data-stu-id="28a97-156">To use it as a local tool, restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](/dotnet/core/tools/global-tools#install-a-local-tool).</span></span>

* <span data-ttu-id="28a97-157">Verwenden Sie zum Aktualisieren der Tools den `dotnet tool update`-Befehl.</span><span class="sxs-lookup"><span data-stu-id="28a97-157">To update the tools, use the `dotnet tool update` command.</span></span>

* <span data-ttu-id="28a97-158">Installieren Sie das aktuelle `Microsoft.EntityFrameworkCore.Design`-Paket.</span><span class="sxs-lookup"><span data-stu-id="28a97-158">Install the latest `Microsoft.EntityFrameworkCore.Design` package.</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

> [!IMPORTANT]
> <span data-ttu-id="28a97-159">Verwenden Sie immer die Toolpaketversion, die der Hauptversion der Runtimepakete entspricht.</span><span class="sxs-lookup"><span data-stu-id="28a97-159">Always use the version of the tools package that matches the major version of the runtime packages.</span></span>

### <a name="get-the-package-manager-console-tools"></a><span data-ttu-id="28a97-160">Abrufen der Tools für die Paket-Manager-Konsole</span><span class="sxs-lookup"><span data-stu-id="28a97-160">Get the Package Manager Console tools</span></span>

<span data-ttu-id="28a97-161">Installieren Sie das `Microsoft.EntityFrameworkCore.Tools`-Paket, um die PMC-Tools für EF Core zu installieren.</span><span class="sxs-lookup"><span data-stu-id="28a97-161">To get the Package Manager Console tools for EF Core, install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span> <span data-ttu-id="28a97-162">In Visual Studio sieht dies z.B. folgendermaßen aus:</span><span class="sxs-lookup"><span data-stu-id="28a97-162">For example, from Visual Studio:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="28a97-163">Für ASP.NET Core-Apps ist dieses Paket automatisch enthalten.</span><span class="sxs-lookup"><span data-stu-id="28a97-163">For ASP.NET Core apps, this package is included automatically.</span></span>

## <a name="upgrading-to-the-latest-ef-core"></a><span data-ttu-id="28a97-164">Upgrade auf die aktuelle Version von EF Core</span><span class="sxs-lookup"><span data-stu-id="28a97-164">Upgrading to the latest EF Core</span></span>

* <span data-ttu-id="28a97-165">Mit jedem Release von EF Core veröffentlichen wir auch eine neue Version der Anbieter, die Teil des EF Core-Projekts sind, u.a. Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite und Microsoft.EntityFrameworkCore.InMemory.</span><span class="sxs-lookup"><span data-stu-id="28a97-165">Any time we release a new version of EF Core, we also release a new version of the providers that are part of the EF Core project, like Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite, and Microsoft.EntityFrameworkCore.InMemory.</span></span> <span data-ttu-id="28a97-166">Führen Sie ein Upgrade auf die neue Version eines Anbieters durch, um alle Verbesserungen nutzen zu können.</span><span class="sxs-lookup"><span data-stu-id="28a97-166">You can just upgrade to the new version of the provider to get all the improvements.</span></span>

* <span data-ttu-id="28a97-167">EF Core ist neben den SQL Server- und In-Memory-Anbietern in den aktuellen Versionen von ASP.NET Core enthalten.</span><span class="sxs-lookup"><span data-stu-id="28a97-167">EF Core, together with the SQL Server and the in-memory providers are included in current versions of ASP.NET Core.</span></span> <span data-ttu-id="28a97-168">Führen Sie immer ein Upgrade von ASP.NET Core durch, um eine vorhandene ASP.NET Core-Anwendung auf eine neuere Version von EF Core zu upgraden.</span><span class="sxs-lookup"><span data-stu-id="28a97-168">To upgrade an existing ASP.NET Core application to a newer version of EF Core, always upgrade the version of ASP.NET Core.</span></span>

* <span data-ttu-id="28a97-169">Wenn Sie eine Anwendung aktualisieren müssen, die einen Datenbankanbieter eines Drittanbieters verwendet, sollten Sie immer nach einem Update des Anbieters suchen, der mit der von Ihnen gewünschten Version von EF Core kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="28a97-169">If you need to update an application that is using a third-party database provider, always check for an update of the provider that is compatible with the version of EF Core you want to use.</span></span> <span data-ttu-id="28a97-170">Datenbankanbieter für Version 1.0 sind beispielsweise nicht mit Version 2.0 der EF Core-Runtime kompatibel.</span><span class="sxs-lookup"><span data-stu-id="28a97-170">For example, database providers for version 1.0 are not compatible with version 2.0 of the EF Core runtime.</span></span>

* <span data-ttu-id="28a97-171">Drittanbieter für EF Core veröffentlichen normalerweise keine Patchversionen mit der EF Core-Runtime.</span><span class="sxs-lookup"><span data-stu-id="28a97-171">Third-party providers for EF Core usually don't release patch versions alongside the EF Core runtime.</span></span> <span data-ttu-id="28a97-172">Fügen Sie einen direkten Verweis auf einzelne Komponenten der EF Core-Runtime hinzu, um eine Anwendung upzugraden, die einen Drittanbieter verwendet, um eine Version von EF Core zu patchen, z.B. Microsoft.EntityFrameworkCore und Microsoft.EntityFrameworkCore.Relational.</span><span class="sxs-lookup"><span data-stu-id="28a97-172">To upgrade an application that uses a third-party provider to a patch version of EF Core, you may need to add a direct reference to individual EF Core runtime components, such as Microsoft.EntityFrameworkCore, and Microsoft.EntityFrameworkCore.Relational.</span></span>
