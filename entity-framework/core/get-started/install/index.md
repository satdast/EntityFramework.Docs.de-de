---
title: Installieren von Entity Framework Core
author: divega
ms.date: 08/06/2017
ms.assetid: 608cc774-c570-4809-8a3e-cd2c8446b8b2
uid: core/get-started/install/index
ms.openlocfilehash: 6575b1ac028f8b67b49ca7f4e49d6f19500be98f
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80136170"
---
# <a name="installing-entity-framework-core"></a><span data-ttu-id="3fb20-102">Installieren von Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="3fb20-102">Installing Entity Framework Core</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3fb20-103">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="3fb20-103">Prerequisites</span></span>

* <span data-ttu-id="3fb20-104">EF Core ist eine [.NET Standard 2.0](/dotnet/standard/net-standard)-Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="3fb20-104">EF Core is a [.NET Standard 2.0](/dotnet/standard/net-standard) library.</span></span> <span data-ttu-id="3fb20-105">D.h., dass EF Core eine .NET-Implementierung erfordert, die .NET Standard 2.0 unterstützt.</span><span class="sxs-lookup"><span data-stu-id="3fb20-105">So EF Core requires a .NET implementation that supports .NET Standard 2.0 to run.</span></span> <span data-ttu-id="3fb20-106">Auf EF Core kann auch von anderen .NET Standard 2.0-Bibliotheken verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="3fb20-106">EF Core can also be referenced by other .NET Standard 2.0 libraries.</span></span>

* <span data-ttu-id="3fb20-107">Sie können EF Core z.B. verwenden, um Apps für .NET Core zu entwickeln.</span><span class="sxs-lookup"><span data-stu-id="3fb20-107">For example, you can use EF Core to develop apps that target .NET Core.</span></span> <span data-ttu-id="3fb20-108">Zum Erstellen von .NET Core-Apps ist das [.NET Core SDK](https://dotnet.microsoft.com/download) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="3fb20-108">Building .NET Core apps requires the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="3fb20-109">Optional können Sie auch eine Entwicklungsumgebung wie [Visual Studio](https://visualstudio.microsoft.com/vs), [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac) oder [Visual Studio Code](https://code.visualstudio.com) verwenden.</span><span class="sxs-lookup"><span data-stu-id="3fb20-109">Optionally, you can also use a development environment like [Visual Studio](https://visualstudio.microsoft.com/vs), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac), or [Visual Studio Code](https://code.visualstudio.com).</span></span> <span data-ttu-id="3fb20-110">Weitere Informationen finden Sie unter [Erste Schritte mit .NET Core](/dotnet/core/get-started).</span><span class="sxs-lookup"><span data-stu-id="3fb20-110">For more information, check [Getting Started with .NET Core](/dotnet/core/get-started).</span></span>

* <span data-ttu-id="3fb20-111">Mit EF Core können Sie in Visual Studio unter Windows Anwendungen entwickeln.</span><span class="sxs-lookup"><span data-stu-id="3fb20-111">You can use EF Core to develop applications on Windows using Visual Studio.</span></span> <span data-ttu-id="3fb20-112">Es wird empfohlen, die aktuelle Version von [Visual Studio](https://visualstudio.microsoft.com/vs) zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="3fb20-112">The latest version of [Visual Studio](https://visualstudio.microsoft.com/vs) is recommended.</span></span>

* <span data-ttu-id="3fb20-113">EF Core kann auch in anderen .NET-Implementierungen wie [Xamarin](https://dotnet.microsoft.com/apps/xamarin) oder .NET Native ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="3fb20-113">EF Core can run on other .NET implementations like [Xamarin](https://dotnet.microsoft.com/apps/xamarin) and .NET Native.</span></span> <span data-ttu-id="3fb20-114">Diese Implementierungen haben jedoch Laufzeiteinschränkungen, die sich auf die Funktionsweise von EF Core in Ihrer App auswirken können.</span><span class="sxs-lookup"><span data-stu-id="3fb20-114">But in practice those implementations have runtime limitations that may affect how well EF Core works on your app.</span></span> <span data-ttu-id="3fb20-115">Weitere Informationen finden Sie unter [.NET implementations supported by EF Core (.NET-Implementierungen, die von EF Core unterstützt werden)](xref:core/platforms/index).</span><span class="sxs-lookup"><span data-stu-id="3fb20-115">For more information, see [.NET implementations supported by EF Core](xref:core/platforms/index).</span></span>

* <span data-ttu-id="3fb20-116">Es kann zudem sein, dass unterschiedliche Datenbankanbieter bestimmte Datenbank-Engineversionen, .NET-Implementierungen oder Betriebssysteme erfordern.</span><span class="sxs-lookup"><span data-stu-id="3fb20-116">Finally, different database providers may require specific database engine versions, .NET implementations, or operating systems.</span></span> <span data-ttu-id="3fb20-117">Achten Sie darauf, dass ein [EF Core-Datenbankanbieter](xref:core/providers/index) zur Verfügung steht, der eine für Ihre Anwendung geeignete Umgebung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="3fb20-117">Make sure an [EF Core database provider](xref:core/providers/index) is available that supports the right environment for your application.</span></span>

## <a name="get-the-entity-framework-core-runtime"></a><span data-ttu-id="3fb20-118">Abrufen der Entity Framework Core-Runtime</span><span class="sxs-lookup"><span data-stu-id="3fb20-118">Get the Entity Framework Core runtime</span></span>

<span data-ttu-id="3fb20-119">Installieren Sie das NuGet-Paket für den Datenbankanbieter, den Sie verwenden möchten, um einer Anwendung EF Core hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="3fb20-119">To add EF Core to an application, install the NuGet package for the database provider you want to use.</span></span>

<span data-ttu-id="3fb20-120">Wenn Sie eine ASP.NET Core-Anwendung erstellen, müssen Sie keine In-Memory- und SQL Server-Anbieter installieren.</span><span class="sxs-lookup"><span data-stu-id="3fb20-120">If you're building an ASP.NET Core application, you don't need to install the in-memory and SQL Server providers.</span></span> <span data-ttu-id="3fb20-121">Diese Anbieter sind neben der EF Core-Runtime in der aktuellen Versionen von ASP.NET Core enthalten.</span><span class="sxs-lookup"><span data-stu-id="3fb20-121">Those providers are included in current versions of ASP.NET Core, alongside the EF Core runtime.</span></span>  

<span data-ttu-id="3fb20-122">Verwenden Sie die .NET Core-CLI (Command Line Interface, Befehlszeilenschnittstelle), das Dialogfeld für den Visual Studio-Paket-Manager oder die Visual Studio-Paket-Manager-Konsole, um NuGet-Pakete zu installieren oder zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="3fb20-122">To install or update NuGet packages, you can use the .NET Core command-line interface (CLI), the Visual Studio Package Manager Dialog, or the Visual Studio Package Manager Console.</span></span>

### <a name="net-core-cli"></a><span data-ttu-id="3fb20-123">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3fb20-123">.NET Core CLI</span></span>

* <span data-ttu-id="3fb20-124">Verwenden Sie den folgenden .NET Core-CLI-Befehl über die Befehlszeile des Betriebssystems, um den EF Core-SQL Server-Anbieter zu installieren oder zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="3fb20-124">Use the following .NET Core CLI command from the operating system's command line to install or update the EF Core SQL Server provider:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="3fb20-125">Mit dem Modifizierer `-v` können Sie eine bestimmte Version im Befehl `dotnet add package` angeben.</span><span class="sxs-lookup"><span data-stu-id="3fb20-125">You can indicate a specific version in the `dotnet add package` command, using the `-v` modifier.</span></span> <span data-ttu-id="3fb20-126">Wenn Sie beispielsweise EF Core 2.2.0-Pakete installieren möchten, fügen Sie `-v 2.2.0` an den Befehl an.</span><span class="sxs-lookup"><span data-stu-id="3fb20-126">For example, to install EF Core 2.2.0 packages, append `-v 2.2.0` to the command.</span></span>

<span data-ttu-id="3fb20-127">Weitere Informationen finden Sie unter [Tools für die .NET Core-Befehlszeilenschnittstelle](/dotnet/core/tools/).</span><span class="sxs-lookup"><span data-stu-id="3fb20-127">For more information, see [.NET command-line interface (CLI) tools](/dotnet/core/tools/).</span></span>

### <a name="visual-studio-nuget-package-manager-dialog"></a><span data-ttu-id="3fb20-128">Dialogfeld für den NuGet-Paket-Manager in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3fb20-128">Visual Studio NuGet Package Manager Dialog</span></span>

* <span data-ttu-id="3fb20-129">Klicken Sie im Visual Studio-Menü auf **Projekt > NuGet-Pakete verwalten**.</span><span class="sxs-lookup"><span data-stu-id="3fb20-129">From the Visual Studio menu, select **Project > Manage NuGet Packages**</span></span>

* <span data-ttu-id="3fb20-130">Klicken Sie auf **Durchsuchen** oder die Registerkarte **Updates**.</span><span class="sxs-lookup"><span data-stu-id="3fb20-130">Click on the **Browse** or the **Updates** tab</span></span>

* <span data-ttu-id="3fb20-131">Wählen Sie das `Microsoft.EntityFrameworkCore.SqlServer`-Paket aus, und bestätigen Sie, um den SQL Server-Anbieter zu installieren oder zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="3fb20-131">To install or update the SQL Server provider, select the `Microsoft.EntityFrameworkCore.SqlServer` package, and confirm.</span></span>

<span data-ttu-id="3fb20-132">Weitere Informationen finden Sie im [Dialogfeld des NuGet-Paket-Managers](/nuget/tools/package-manager-ui).</span><span class="sxs-lookup"><span data-stu-id="3fb20-132">For more information, see [NuGet Package Manager Dialog](/nuget/tools/package-manager-ui).</span></span>

### <a name="visual-studio-nuget-package-manager-console"></a><span data-ttu-id="3fb20-133">NuGet-Paket-Manager-Konsole (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="3fb20-133">Visual Studio NuGet Package Manager Console</span></span>

* <span data-ttu-id="3fb20-134">Wählen Sie im Visual Studio-Menü **Extras > NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="3fb20-134">From the Visual Studio menu, select **Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="3fb20-135">Führen Sie den folgenden Befehl in der Paket-Manager-Konsole aus, um den SQL Server-Anbieter zu installieren:</span><span class="sxs-lookup"><span data-stu-id="3fb20-135">To install the SQL Server provider, run the following command in the Package Manager Console:</span></span>

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="3fb20-136">Verwenden Sie den `Update-Package`-Befehl, um den Anbieter zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="3fb20-136">To update the provider, use the `Update-Package` command.</span></span>

* <span data-ttu-id="3fb20-137">Um eine bestimmte Version anzugeben, können Sie den Modifizierer `-Version` verwenden.</span><span class="sxs-lookup"><span data-stu-id="3fb20-137">To specify a specific version, use the `-Version` modifier.</span></span> <span data-ttu-id="3fb20-138">Wenn Sie beispielsweise EF Core 2.2.0-Pakete installieren möchten, fügen Sie `-Version 2.2.0` an den Befehl an.</span><span class="sxs-lookup"><span data-stu-id="3fb20-138">For example, to install EF Core 2.2.0 packages, append `-Version 2.2.0` to the commands</span></span>

<span data-ttu-id="3fb20-139">Weitere Informationen finden Sie im Artikel zur [Paket-Manager-Konsole](/nuget/tools/package-manager-console).</span><span class="sxs-lookup"><span data-stu-id="3fb20-139">For more information, see [Package Manager Console](/nuget/tools/package-manager-console).</span></span>

## <a name="get-the-entity-framework-core-tools"></a><span data-ttu-id="3fb20-140">Installieren der Entity Framework Core-Tools</span><span class="sxs-lookup"><span data-stu-id="3fb20-140">Get the Entity Framework Core tools</span></span>

<span data-ttu-id="3fb20-141">Sie können Tools installieren, die Tasks im Zusammenhang mit EF Core in Ihrem Projekt ausführen, z.B. das Erstellen und Durchführen von Datenbankmigrationen oder das Erstellen eines EF Core-Modells basierend auf einer vorhandenen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="3fb20-141">You can install tools to carry out EF Core-related tasks in your project, like creating and applying database migrations, or creating an EF Core model based on an existing database.</span></span>

<span data-ttu-id="3fb20-142">Es sind zwei Sätze von Tools verfügbar:</span><span class="sxs-lookup"><span data-stu-id="3fb20-142">Two sets of tools are available:</span></span>

* <span data-ttu-id="3fb20-143">Die [.NET Core-CLI-Tools](xref:core/miscellaneous/cli/dotnet) können unter Windows, Linux und macOS verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3fb20-143">The [.NET Core command-line interface (CLI) tools](xref:core/miscellaneous/cli/dotnet) can be used on Windows, Linux, or macOS.</span></span> <span data-ttu-id="3fb20-144">Diese Befehle beginnen mit `dotnet ef`.</span><span class="sxs-lookup"><span data-stu-id="3fb20-144">These commands begin with `dotnet ef`.</span></span>

* <span data-ttu-id="3fb20-145">Die [Tools der Paket-Manager-Konsole (PMC-Tools)](xref:core/miscellaneous/cli/powershell) werden in Visual Studio unter Windows ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="3fb20-145">The [Package Manager Console (PMC) tools](xref:core/miscellaneous/cli/powershell) run in Visual Studio on Windows.</span></span> <span data-ttu-id="3fb20-146">Diese Befehlen beginnen mit einem Verb, z.B. `Add-Migration`, `Update-Database`.</span><span class="sxs-lookup"><span data-stu-id="3fb20-146">These commands start with a verb, for example `Add-Migration`, `Update-Database`.</span></span>

<span data-ttu-id="3fb20-147">Obwohl Sie die `dotnet ef`-Befehle auch über die Paket-Manager-Konsole verwenden können, wird empfohlen, die Tools der Paket-Manager-Konsole zu verwenden, wenn Sie Visual Studio nutzen:</span><span class="sxs-lookup"><span data-stu-id="3fb20-147">Although you can also use the `dotnet ef` commands from the Package Manager Console, it's recommended to use the Package Manager Console tools when you're using Visual Studio:</span></span>

* <span data-ttu-id="3fb20-148">Sie werden automatisch mit dem aktuellen in der PMC in Visual Studio ausgewählten Projekt ausgeführt, ohne dass manuell zwischen den Verzeichnissen gewechselt werden muss.</span><span class="sxs-lookup"><span data-stu-id="3fb20-148">They automatically work with the current project selected in the PMC in Visual Studio, without requiring manually switching directories.</span></span>  

* <span data-ttu-id="3fb20-149">Nach Abschluss des Befehls öffnen sie automatisch Dateien, die von den Befehlen in Visual Studio generiert wurden.</span><span class="sxs-lookup"><span data-stu-id="3fb20-149">They automatically open files generated by the commands in Visual Studio after the command is completed.</span></span>

<a name="cli"></a>

### <a name="get-the-net-core-cli-tools"></a><span data-ttu-id="3fb20-150">Installieren von .NET Core-CLI-Tools</span><span class="sxs-lookup"><span data-stu-id="3fb20-150">Get the .NET Core CLI tools</span></span>

<span data-ttu-id="3fb20-151">.NET Core-CLI-Tools erfordern das .NET Core SDK, das bereits unter [Erforderliche Komponenten](#prerequisites) erwähnt wurde.</span><span class="sxs-lookup"><span data-stu-id="3fb20-151">.NET Core CLI tools require the .NET Core SDK, mentioned earlier in [Prerequisites](#prerequisites).</span></span>

<span data-ttu-id="3fb20-152">Die `dotnet ef`-Befehle sind in den aktuellen Versionen des .NET Core SDK enthalten. Sie müssen jedoch das `Microsoft.EntityFrameworkCore.Design`-Paket installieren, um diese Befehle für ein bestimmtes Projekt nutzen zu können:</span><span class="sxs-lookup"><span data-stu-id="3fb20-152">The `dotnet ef` commands are included in current versions of the .NET Core SDK, but to enable the commands on a specific project, you have to install the `Microsoft.EntityFrameworkCore.Design` package:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Design
```

> [!IMPORTANT]
> <span data-ttu-id="3fb20-153">Verwenden Sie immer die Toolpaketversion, die der Hauptversion der Runtimepakete entspricht.</span><span class="sxs-lookup"><span data-stu-id="3fb20-153">Always use the version of the tools package that matches the major version of the runtime packages.</span></span>

### <a name="get-the-package-manager-console-tools"></a><span data-ttu-id="3fb20-154">Abrufen der Tools für die Paket-Manager-Konsole</span><span class="sxs-lookup"><span data-stu-id="3fb20-154">Get the Package Manager Console tools</span></span>

<span data-ttu-id="3fb20-155">Installieren Sie das `Microsoft.EntityFrameworkCore.Tools`-Paket, um die PMC-Tools für EF Core zu installieren.</span><span class="sxs-lookup"><span data-stu-id="3fb20-155">To get the Package Manager Console tools for EF Core, install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span> <span data-ttu-id="3fb20-156">In Visual Studio sieht dies z.B. folgendermaßen aus:</span><span class="sxs-lookup"><span data-stu-id="3fb20-156">For example, from Visual Studio:</span></span>

``` PowerShell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="3fb20-157">Für ASP.NET Core-Apps ist dieses Paket automatisch enthalten.</span><span class="sxs-lookup"><span data-stu-id="3fb20-157">For ASP.NET Core apps, this package is included automatically.</span></span>

## <a name="upgrading-to-the-latest-ef-core"></a><span data-ttu-id="3fb20-158">Upgrade auf die aktuelle Version von EF Core</span><span class="sxs-lookup"><span data-stu-id="3fb20-158">Upgrading to the latest EF Core</span></span>

* <span data-ttu-id="3fb20-159">Mit jedem Release von EF Core veröffentlichen wir auch eine neue Version der Anbieter, die Teil des EF Core-Projekts sind, u.a. Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite und Microsoft.EntityFrameworkCore.InMemory.</span><span class="sxs-lookup"><span data-stu-id="3fb20-159">Any time we release a new version of EF Core, we also release a new version of the providers that are part of the EF Core project, like Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite, and Microsoft.EntityFrameworkCore.InMemory.</span></span> <span data-ttu-id="3fb20-160">Führen Sie ein Upgrade auf die neue Version eines Anbieters durch, um alle Verbesserungen nutzen zu können.</span><span class="sxs-lookup"><span data-stu-id="3fb20-160">You can just upgrade to the new version of the provider to get all the improvements.</span></span>

* <span data-ttu-id="3fb20-161">EF Core ist neben den SQL Server- und In-Memory-Anbietern in den aktuellen Versionen von ASP.NET Core enthalten.</span><span class="sxs-lookup"><span data-stu-id="3fb20-161">EF Core, together with the SQL Server and the in-memory providers are included in current versions of ASP.NET Core.</span></span> <span data-ttu-id="3fb20-162">Führen Sie immer ein Upgrade von ASP.NET Core durch, um eine vorhandene ASP.NET Core-Anwendung auf eine neuere Version von EF Core zu upgraden.</span><span class="sxs-lookup"><span data-stu-id="3fb20-162">To upgrade an existing ASP.NET Core application to a newer version of EF Core, always upgrade the version of ASP.NET Core.</span></span>

* <span data-ttu-id="3fb20-163">Wenn Sie eine Anwendung aktualisieren müssen, die einen Datenbankanbieter eines Drittanbieters verwendet, sollten Sie immer nach einem Update des Anbieters suchen, der mit der von Ihnen gewünschten Version von EF Core kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="3fb20-163">If you need to update an application that is using a third-party database provider, always check for an update of the provider that is compatible with the version of EF Core you want to use.</span></span> <span data-ttu-id="3fb20-164">Datenbankanbieter für vorherige Versionen sind nicht mit Version 2.0 der EF Core-Runtime kompatibel.</span><span class="sxs-lookup"><span data-stu-id="3fb20-164">For example, database providers for previous versions are not compatible with version 2.0 of the EF Core runtime.</span></span>

* <span data-ttu-id="3fb20-165">Drittanbieter für EF Core veröffentlichen normalerweise keine Patchversionen mit der EF Core-Runtime.</span><span class="sxs-lookup"><span data-stu-id="3fb20-165">Third-party providers for EF Core usually don't release patch versions alongside the EF Core runtime.</span></span> <span data-ttu-id="3fb20-166">Fügen Sie einen direkten Verweis auf einzelne Komponenten der EF Core-Runtime hinzu, um eine Anwendung upzugraden, die einen Drittanbieter verwendet, um eine Version von EF Core zu patchen, z.B. Microsoft.EntityFrameworkCore und Microsoft.EntityFrameworkCore.Relational.</span><span class="sxs-lookup"><span data-stu-id="3fb20-166">To upgrade an application that uses a third-party provider to a patch version of EF Core, you may need to add a direct reference to individual EF Core runtime components, such as Microsoft.EntityFrameworkCore, and Microsoft.EntityFrameworkCore.Relational.</span></span>

* <span data-ttu-id="3fb20-167">Wenn Sie ein Upgrade einer vorhandenen Anwendung auf die aktuelle Version von EF Core durchführen, müssen einige Verweise auf ältere EF Core-Pakete eventuell manuell entfernt werden:</span><span class="sxs-lookup"><span data-stu-id="3fb20-167">If you're upgrading an existing application to the latest version of EF Core, some references to older EF Core packages may need to be removed manually:</span></span>

  * <span data-ttu-id="3fb20-168">Entwurfszeitpakete von Datenbankanbietern wie `Microsoft.EntityFrameworkCore.SqlServer.Design` sind ab EF Core 2.0 nicht mehr erforderlich und werden nicht unterstützt. Sie werden bei der Durchführung eines Upgrades anderer Pakete jedoch nicht automatisch entfernt.</span><span class="sxs-lookup"><span data-stu-id="3fb20-168">Database provider design-time packages such as `Microsoft.EntityFrameworkCore.SqlServer.Design` are no longer required or supported from EF Core 2.0 and later, but aren't automatically removed when upgrading the other packages.</span></span>

  * <span data-ttu-id="3fb20-169">Die .NET-CLI-Tools sind im .NET SDK ab Version 2.1 enthalten. Der Verweis auf das Paket kann also aus der Projektdatei entfernt werden:</span><span class="sxs-lookup"><span data-stu-id="3fb20-169">The .NET CLI tools are included in the .NET SDK since version 2.1, so the reference to that package can be removed from the project file:</span></span>

    ``` xml
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
    ```
