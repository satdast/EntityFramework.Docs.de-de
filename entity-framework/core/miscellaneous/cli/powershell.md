---
title: Referenz zu EF Core Tools (Paket-Manager-Konsole)-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 698a2cadadb1389f2e659e3ecab2fb21d020322e
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672934"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="db90b-102">Referenz zur Entity Framework Core Tools-Paket-Manager-Konsole in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="db90b-102">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="db90b-103">Die Paket-Manager-Konsole (PMC)-Tools für Entity Framework Core die Entwicklungsaufgaben zur Entwurfszeit ausführen.</span><span class="sxs-lookup"><span data-stu-id="db90b-103">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="db90b-104">Beispielsweise erstellen Sie [Migrationen](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), wenden Migrationen an und generieren Code für ein Modell, das auf einer vorhandenen Datenbank basiert.</span><span class="sxs-lookup"><span data-stu-id="db90b-104">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="db90b-105">Die Befehle werden innerhalb von Visual Studio mithilfe der [Paket-Manager-Konsole](/nuget/tools/package-manager-console)ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="db90b-105">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="db90b-106">Diese Tools funktionieren sowohl mit .NET Framework- als auch mit .NET Core-Projekten.</span><span class="sxs-lookup"><span data-stu-id="db90b-106">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="db90b-107">Wenn Sie Visual Studio nicht verwenden, empfiehlt es sich, stattdessen die [EF Core Befehlszeilen Tools](dotnet.md) zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="db90b-107">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](dotnet.md) instead.</span></span> <span data-ttu-id="db90b-108">Die CLI-Tools sind plattformübergreifend und werden innerhalb einer Eingabeaufforderung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="db90b-108">The CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="db90b-109">Installieren der Tools</span><span class="sxs-lookup"><span data-stu-id="db90b-109">Installing the tools</span></span>

<span data-ttu-id="db90b-110">Die Verfahren zum Installieren und Aktualisieren der Tools unterscheiden sich zwischen ASP.net Core 2.1 und früheren Versionen oder anderen Projekttypen.</span><span class="sxs-lookup"><span data-stu-id="db90b-110">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="db90b-111">ASP.net Core Version 2,1 und höher</span><span class="sxs-lookup"><span data-stu-id="db90b-111">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="db90b-112">Die Tools werden automatisch in ein ASP.net Core 2.1 +-Projekt eingeschlossen, da das `Microsoft.EntityFrameworkCore.Tools` Paket im [Metapaket Microsoft. aspnetcore. app](/aspnet/core/fundamentals/metapackage-app)enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="db90b-112">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="db90b-113">Daher müssen Sie keine weiteren Schritte ausführen, um die Tools zu installieren, aber Sie müssen folgende Schritte ausführen:</span><span class="sxs-lookup"><span data-stu-id="db90b-113">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>

* <span data-ttu-id="db90b-114">Stellen Sie Pakete wieder her, bevor Sie die Tools in einem neuen Projekt verwenden.</span><span class="sxs-lookup"><span data-stu-id="db90b-114">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="db90b-115">Installieren Sie ein Paket, um die Tools auf eine neuere Version zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="db90b-115">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="db90b-116">Um sicherzustellen, dass Sie die neueste Version der Tools erhalten, empfiehlt es sich, auch den folgenden Schritt durchzuführen:</span><span class="sxs-lookup"><span data-stu-id="db90b-116">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="db90b-117">Bearbeiten Sie die *csproj* -Datei, und fügen Sie eine Zeile hinzu, die die neueste Version des [Microsoft. entityframeworkcore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) -Pakets angibt.</span><span class="sxs-lookup"><span data-stu-id="db90b-117">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="db90b-118">Die *csproj* -Datei kann beispielsweise einen enthalten, der `ItemGroup` wie folgt aussieht:</span><span class="sxs-lookup"><span data-stu-id="db90b-118">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="3.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.2" />
  </ItemGroup>
  ```

<span data-ttu-id="db90b-119">Aktualisieren Sie die Tools, wenn Sie eine Meldung wie im folgenden Beispiel erhalten:</span><span class="sxs-lookup"><span data-stu-id="db90b-119">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="db90b-120">Die EF Core Tools-Version "2.1.1-RTM-30846" ist älter als die der Runtime "2.1.3-RTM-32065".</span><span class="sxs-lookup"><span data-stu-id="db90b-120">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="db90b-121">Aktualisieren Sie die Tools für die neuesten Features und Fehlerbehebungen.</span><span class="sxs-lookup"><span data-stu-id="db90b-121">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="db90b-122">So aktualisieren Sie die Tools:</span><span class="sxs-lookup"><span data-stu-id="db90b-122">To update the tools:</span></span>

* <span data-ttu-id="db90b-123">Installieren Sie das neueste .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="db90b-123">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="db90b-124">Aktualisieren Sie Visual Studio auf die neuste Version.</span><span class="sxs-lookup"><span data-stu-id="db90b-124">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="db90b-125">Bearbeiten Sie die *csproj* -Datei so, dass Sie einen Paket Verweis auf das aktuellste Tool Paket enthält, wie zuvor gezeigt.</span><span class="sxs-lookup"><span data-stu-id="db90b-125">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="db90b-126">Andere Versionen und Projekttypen</span><span class="sxs-lookup"><span data-stu-id="db90b-126">Other versions and project types</span></span>

<span data-ttu-id="db90b-127">Installieren Sie die Paket-Manager-Konsole, indem Sie den folgenden Befehl in der **Paket-Manager-Konsole**ausführen:</span><span class="sxs-lookup"><span data-stu-id="db90b-127">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="db90b-128">Aktualisieren Sie die Tools, indem Sie den folgenden Befehl in der **Paket-Manager-Konsole**ausführen.</span><span class="sxs-lookup"><span data-stu-id="db90b-128">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="db90b-129">Überprüfen der Installation</span><span class="sxs-lookup"><span data-stu-id="db90b-129">Verify the installation</span></span>

<span data-ttu-id="db90b-130">Stellen Sie sicher, dass die Tools installiert sind, indem Sie folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="db90b-130">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="db90b-131">Die Ausgabe sieht wie folgt aus (Sie gibt nicht an, welche Version der Tools Sie verwenden):</span><span class="sxs-lookup"><span data-stu-id="db90b-131">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

```console

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

TOPIC
    about_EntityFrameworkCore

SHORT DESCRIPTION
    Provides information about the Entity Framework Core Package Manager Console Tools.

<A list of available commands follows, omitted here.>
```

## <a name="using-the-tools"></a><span data-ttu-id="db90b-132">Verwenden der Tools</span><span class="sxs-lookup"><span data-stu-id="db90b-132">Using the tools</span></span>

<span data-ttu-id="db90b-133">Vor der Verwendung der Tools:</span><span class="sxs-lookup"><span data-stu-id="db90b-133">Before using the tools:</span></span>

* <span data-ttu-id="db90b-134">Verstehen Sie den Unterschied Zwischenziel und Startprojekt.</span><span class="sxs-lookup"><span data-stu-id="db90b-134">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="db90b-135">Erfahren Sie, wie Sie die Tools mit .NET Standard-Klassenbibliotheken verwenden.</span><span class="sxs-lookup"><span data-stu-id="db90b-135">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="db90b-136">Legen Sie für ASP.net Core Projekte die Umgebung fest.</span><span class="sxs-lookup"><span data-stu-id="db90b-136">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="db90b-137">Ziel-und Startprojekt</span><span class="sxs-lookup"><span data-stu-id="db90b-137">Target and startup project</span></span>

<span data-ttu-id="db90b-138">Die Befehle verweisen auf ein *Projekt* und ein *Startprojekt*.</span><span class="sxs-lookup"><span data-stu-id="db90b-138">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="db90b-139">Das *Projekt* wird auch als *Ziel Projekt* bezeichnet, weil die Befehle Dateien hinzufügen oder entfernen.</span><span class="sxs-lookup"><span data-stu-id="db90b-139">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="db90b-140">Standardmäßig ist das **Standard Projekt** , das in der **Paket-Manager-Konsole** ausgewählt ist, das Ziel Projekt.</span><span class="sxs-lookup"><span data-stu-id="db90b-140">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="db90b-141">Sie können ein anderes Projekt als Ziel Projekt angeben, indem Sie die <nobr>`--project`</nobr> Option verwenden.</span><span class="sxs-lookup"><span data-stu-id="db90b-141">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="db90b-142">Das *Startprojekt ist das Startprojekt* , das von den Tools erstellt und ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="db90b-142">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="db90b-143">Die Tools müssen Anwendungscode zur Entwurfszeit ausführen, um Informationen zum Projekt zu erhalten, z. b. die Daten bankverbindungs Zeichenfolge und die Konfiguration des Modells.</span><span class="sxs-lookup"><span data-stu-id="db90b-143">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="db90b-144">Standardmäßig ist das **Startprojekt** in **Projektmappen-Explorer** das Startprojekt.</span><span class="sxs-lookup"><span data-stu-id="db90b-144">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="db90b-145">Sie können ein anderes Projekt als Startprojekt angeben, indem Sie die <nobr>`--startup-project`</nobr> Option verwenden.</span><span class="sxs-lookup"><span data-stu-id="db90b-145">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="db90b-146">Das Startprojekt und das Ziel Projekt sind häufig das gleiche Projekt.</span><span class="sxs-lookup"><span data-stu-id="db90b-146">The startup project and target project are often the same project.</span></span> <span data-ttu-id="db90b-147">Ein typisches Szenario, bei dem es sich um separate Projekte handelt, sind folgende:</span><span class="sxs-lookup"><span data-stu-id="db90b-147">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="db90b-148">Die EF Core Kontext-und Entitäts Klassen befinden sich in einer .net Core-Klassenbibliothek.</span><span class="sxs-lookup"><span data-stu-id="db90b-148">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="db90b-149">Eine .net Core-Konsolen-APP oder-Web-App verweist auf die Klassenbibliothek.</span><span class="sxs-lookup"><span data-stu-id="db90b-149">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="db90b-150">Es ist auch möglich, [Migrations Code in einer Klassenbibliothek zu platzieren, getrennt vom EF Core Kontext](xref:core/managing-schemas/migrations/projects).</span><span class="sxs-lookup"><span data-stu-id="db90b-150">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="db90b-151">Andere Ziel-Frameworks</span><span class="sxs-lookup"><span data-stu-id="db90b-151">Other target frameworks</span></span>

<span data-ttu-id="db90b-152">Die Paket-Manager-Konsolen Tools können mit .net Core-oder .NET Framework-Projekten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="db90b-152">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="db90b-153">Apps, die über das EF Core Modell in einer .NET Standard-Klassenbibliothek verfügen, verfügen möglicherweise nicht über ein .net Core-oder .NET Framework-Projekt.</span><span class="sxs-lookup"><span data-stu-id="db90b-153">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="db90b-154">Dies gilt z. b. für xamarin-und universelle Windows-Plattform-apps.</span><span class="sxs-lookup"><span data-stu-id="db90b-154">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="db90b-155">In solchen Fällen können Sie ein .net Core-oder .NET Framework Konsolen-App-Projekt erstellen, dessen einziger Zweck darin besteht, als Startprojekt für die Tools zu fungieren.</span><span class="sxs-lookup"><span data-stu-id="db90b-155">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="db90b-156">Das Projekt kann ein Dummyprojekt ohne echten Code sein &mdash; . es ist nur erforderlich, um ein Ziel für die Tools bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="db90b-156">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="db90b-157">Warum ist ein Dummyprojekt erforderlich?</span><span class="sxs-lookup"><span data-stu-id="db90b-157">Why is a dummy project required?</span></span> <span data-ttu-id="db90b-158">Wie bereits erwähnt, müssen die Tools Anwendungscode zur Entwurfszeit ausführen.</span><span class="sxs-lookup"><span data-stu-id="db90b-158">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="db90b-159">Hierzu müssen Sie die .net Core-oder .NET Framework-Laufzeit verwenden.</span><span class="sxs-lookup"><span data-stu-id="db90b-159">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="db90b-160">Wenn sich das EF Core Modell in einem Projekt befindet, das .net Core oder .NET Framework als Ziel hat, wird die Laufzeit von den EF Core Tools aus dem Projekt ausgeliehen.</span><span class="sxs-lookup"><span data-stu-id="db90b-160">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="db90b-161">Dies ist nicht möglich, wenn sich das EF Core Modell in einer .NET Standard Klassenbibliothek befindet.</span><span class="sxs-lookup"><span data-stu-id="db90b-161">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="db90b-162">Der .NET Standard ist keine tatsächliche .NET-Implementierung. Es handelt sich um eine Spezifikation für eine Reihe von APIs, die von .net-Implementierungen unterstützt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="db90b-162">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="db90b-163">Daher ist .NET Standard für die EF Core Tools nicht ausreichend, um Anwendungscode auszuführen.</span><span class="sxs-lookup"><span data-stu-id="db90b-163">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="db90b-164">Das Dummyprojekt, das Sie als Startprojekt verwenden, stellt eine konkrete Zielplattform bereit, in die die Tools die .NET Standard Klassenbibliothek laden können.</span><span class="sxs-lookup"><span data-stu-id="db90b-164">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="db90b-165">ASP.net Core Umgebung</span><span class="sxs-lookup"><span data-stu-id="db90b-165">ASP.NET Core environment</span></span>

<span data-ttu-id="db90b-166">Wenn Sie die Umgebung für ASP.net Core Projekte angeben möchten, legen Sie **ENV: ASPNETCORE_ENVIRONMENT** vor dem Ausführen von Befehlen fest.</span><span class="sxs-lookup"><span data-stu-id="db90b-166">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="db90b-167">Allgemeine Parameter</span><span class="sxs-lookup"><span data-stu-id="db90b-167">Common parameters</span></span>

<span data-ttu-id="db90b-168">In der folgenden Tabelle sind die Parameter aufgeführt, die für alle EF Core-Befehle gelten:</span><span class="sxs-lookup"><span data-stu-id="db90b-168">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="db90b-169">Parameter</span><span class="sxs-lookup"><span data-stu-id="db90b-169">Parameter</span></span>                 | <span data-ttu-id="db90b-170">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="db90b-170">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="db90b-171">-Kontext \< Zeichenfolge></span><span class="sxs-lookup"><span data-stu-id="db90b-171">-Context \<String></span></span>        | <span data-ttu-id="db90b-172">Die `DbContext`-Klasse, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="db90b-172">The `DbContext` class to use.</span></span> <span data-ttu-id="db90b-173">Der Klassenname oder voll qualifiziert mit Namespaces.</span><span class="sxs-lookup"><span data-stu-id="db90b-173">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="db90b-174">Wenn dieser Parameter ausgelassen wird, wird EF Core die Kontext Klasse findet.</span><span class="sxs-lookup"><span data-stu-id="db90b-174">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="db90b-175">Wenn mehrere Kontext Klassen vorhanden sind, ist dieser Parameter erforderlich.</span><span class="sxs-lookup"><span data-stu-id="db90b-175">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="db90b-176">-Projekt \< Zeichenfolge></span><span class="sxs-lookup"><span data-stu-id="db90b-176">-Project \<String></span></span>        | <span data-ttu-id="db90b-177">Das Ziel Projekt.</span><span class="sxs-lookup"><span data-stu-id="db90b-177">The target project.</span></span> <span data-ttu-id="db90b-178">Wenn dieser Parameter ausgelassen wird, wird das **Standard Projekt** für die **Paket-Manager-Konsole** als Ziel Projekt verwendet.</span><span class="sxs-lookup"><span data-stu-id="db90b-178">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="db90b-179"><nobr>-StartupProject</nobr> \< Zeichen folgen></span><span class="sxs-lookup"><span data-stu-id="db90b-179"><nobr>-StartupProject</nobr> \<String></span></span> | <span data-ttu-id="db90b-180">Das Startprojekt.</span><span class="sxs-lookup"><span data-stu-id="db90b-180">The startup project.</span></span> <span data-ttu-id="db90b-181">Wenn dieser Parameter ausgelassen wird, wird das **Startprojekt** in Projektmappeneigenschaften als Ziel Projekt verwendet. **Solution properties**</span><span class="sxs-lookup"><span data-stu-id="db90b-181">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="db90b-182">-Verbose</span><span class="sxs-lookup"><span data-stu-id="db90b-182">-Verbose</span></span>                  | <span data-ttu-id="db90b-183">Zeigt eine ausführliche Ausgabe an.</span><span class="sxs-lookup"><span data-stu-id="db90b-183">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="db90b-184">Verwenden Sie den PowerShell-Befehl, um Hilfe Informationen zu einem Befehl anzuzeigen `Get-Help` .</span><span class="sxs-lookup"><span data-stu-id="db90b-184">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="db90b-185">Die Parameter "Context", "Project" und "StartupProject" unterstützen die Tab-Erweiterung.</span><span class="sxs-lookup"><span data-stu-id="db90b-185">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="db90b-186">Migration hinzufügen</span><span class="sxs-lookup"><span data-stu-id="db90b-186">Add-Migration</span></span>

<span data-ttu-id="db90b-187">Fügt eine neue Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="db90b-187">Adds a new migration.</span></span>

<span data-ttu-id="db90b-188">Parameter:</span><span class="sxs-lookup"><span data-stu-id="db90b-188">Parameters:</span></span>

| <span data-ttu-id="db90b-189">Parameter</span><span class="sxs-lookup"><span data-stu-id="db90b-189">Parameter</span></span>                         | <span data-ttu-id="db90b-190">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="db90b-190">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="db90b-191"><nobr>-Name \< Zeichenfolge><nobr></span><span class="sxs-lookup"><span data-stu-id="db90b-191"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="db90b-192">Der Name der Migration.</span><span class="sxs-lookup"><span data-stu-id="db90b-192">The name of the migration.</span></span> <span data-ttu-id="db90b-193">Dies ist ein Positions Parameter, der erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="db90b-193">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="db90b-194"><nobr>-OutputDir \< Zeichenfolge></nobr></span><span class="sxs-lookup"><span data-stu-id="db90b-194"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="db90b-195">Das Verzeichnis, mit dem die Dateien ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="db90b-195">The directory use to output the files.</span></span> <span data-ttu-id="db90b-196">Pfade sind relativ zum Ziel Projektverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="db90b-196">Paths are relative to the target project directory.</span></span> <span data-ttu-id="db90b-197">Der Standardwert ist "Migrationen".</span><span class="sxs-lookup"><span data-stu-id="db90b-197">Defaults to "Migrations".</span></span> |
| <span data-ttu-id="db90b-198"><nobr>-Namespace \< Zeichenfolge></nobr></span><span class="sxs-lookup"><span data-stu-id="db90b-198"><nobr>-Namespace \<String></nobr></span></span> | <span data-ttu-id="db90b-199">Der Namespace, der für die generierten Klassen verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="db90b-199">The namespace to use for the generated classes.</span></span> <span data-ttu-id="db90b-200">Wird standardmäßig aus dem Ausgabeverzeichnis generiert.</span><span class="sxs-lookup"><span data-stu-id="db90b-200">Defaults to generated from the output directory.</span></span> |

## <a name="drop-database"></a><span data-ttu-id="db90b-201">Drop-Database</span><span class="sxs-lookup"><span data-stu-id="db90b-201">Drop-Database</span></span>

<span data-ttu-id="db90b-202">Löscht die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="db90b-202">Drops the database.</span></span>

<span data-ttu-id="db90b-203">Parameter:</span><span class="sxs-lookup"><span data-stu-id="db90b-203">Parameters:</span></span>

| <span data-ttu-id="db90b-204">Parameter</span><span class="sxs-lookup"><span data-stu-id="db90b-204">Parameter</span></span> | <span data-ttu-id="db90b-205">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="db90b-205">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="db90b-206">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="db90b-206">-WhatIf</span></span>   | <span data-ttu-id="db90b-207">Zeigen Sie an, welche Datenbank gelöscht werden soll, aber löschen Sie Sie nicht.</span><span class="sxs-lookup"><span data-stu-id="db90b-207">Show which database would be dropped, but don't drop it.</span></span> |

## <a name="get-dbcontext"></a><span data-ttu-id="db90b-208">Get-dbcontext</span><span class="sxs-lookup"><span data-stu-id="db90b-208">Get-DbContext</span></span>

<span data-ttu-id="db90b-209">Ruft Informationen zu einem `DbContext` Typ ab.</span><span class="sxs-lookup"><span data-stu-id="db90b-209">Gets information about a `DbContext` type.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="db90b-210">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="db90b-210">Remove-Migration</span></span>

<span data-ttu-id="db90b-211">Entfernt die letzte Migration (führt einen Rollback für die Codeänderungen aus, die für die Migration durchgeführt wurden).</span><span class="sxs-lookup"><span data-stu-id="db90b-211">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="db90b-212">Parameter:</span><span class="sxs-lookup"><span data-stu-id="db90b-212">Parameters:</span></span>

| <span data-ttu-id="db90b-213">Parameter</span><span class="sxs-lookup"><span data-stu-id="db90b-213">Parameter</span></span> | <span data-ttu-id="db90b-214">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="db90b-214">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="db90b-215">-Force</span><span class="sxs-lookup"><span data-stu-id="db90b-215">-Force</span></span>    | <span data-ttu-id="db90b-216">Setzen Sie die Migration zurück (führen Sie ein Rollback der Änderungen aus, die auf die Datenbank angewendet wurden).</span><span class="sxs-lookup"><span data-stu-id="db90b-216">Revert the migration (roll back the changes that were applied to the database).</span></span> |

## <a name="scaffold-dbcontext"></a><span data-ttu-id="db90b-217">Gerüst-dbcontext</span><span class="sxs-lookup"><span data-stu-id="db90b-217">Scaffold-DbContext</span></span>

<span data-ttu-id="db90b-218">Generiert Code für einen `DbContext` und Entitäts Typen für eine Datenbank.</span><span class="sxs-lookup"><span data-stu-id="db90b-218">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="db90b-219">Damit `Scaffold-DbContext` ein Entitätstyp generiert werden kann, muss die Datenbanktabelle über einen Primärschlüssel verfügen.</span><span class="sxs-lookup"><span data-stu-id="db90b-219">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="db90b-220">Parameter:</span><span class="sxs-lookup"><span data-stu-id="db90b-220">Parameters:</span></span>

| <span data-ttu-id="db90b-221">Parameter</span><span class="sxs-lookup"><span data-stu-id="db90b-221">Parameter</span></span>                          | <span data-ttu-id="db90b-222">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="db90b-222">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="db90b-223"><nobr>-Verbindungs \< Zeichenfolge></nobr></span><span class="sxs-lookup"><span data-stu-id="db90b-223"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="db90b-224">Die Verbindungszeichenfolge für die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="db90b-224">The connection string to the database.</span></span> <span data-ttu-id="db90b-225">Bei ASP.net Core 2. x-Projekten kann der Wert *Name = \< Name der Verbindungs Zeichenfolge>* sein.</span><span class="sxs-lookup"><span data-stu-id="db90b-225">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="db90b-226">In diesem Fall stammt der Name aus den Konfigurations Quellen, die für das Projekt eingerichtet sind.</span><span class="sxs-lookup"><span data-stu-id="db90b-226">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="db90b-227">Dies ist ein Positions Parameter, der erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="db90b-227">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="db90b-228"><nobr>-Anbieter \< Zeichenfolge></nobr></span><span class="sxs-lookup"><span data-stu-id="db90b-228"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="db90b-229">Der zu verwendende Anbieter.</span><span class="sxs-lookup"><span data-stu-id="db90b-229">The provider to use.</span></span> <span data-ttu-id="db90b-230">In der Regel ist dies der Name des nuget-Pakets, z `Microsoft.EntityFrameworkCore.SqlServer` . b.:.</span><span class="sxs-lookup"><span data-stu-id="db90b-230">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="db90b-231">Dies ist ein Positions Parameter, der erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="db90b-231">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="db90b-232">-OutputDir \< Zeichenfolge></span><span class="sxs-lookup"><span data-stu-id="db90b-232">-OutputDir \<String></span></span>               | <span data-ttu-id="db90b-233">Das Verzeichnis, in dem Dateien abgelegt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="db90b-233">The directory to put files in.</span></span> <span data-ttu-id="db90b-234">Pfade sind relativ zum Projektverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="db90b-234">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="db90b-235">-Contextdir \< String></span><span class="sxs-lookup"><span data-stu-id="db90b-235">-ContextDir \<String></span></span>              | <span data-ttu-id="db90b-236">Das Verzeichnis, in das die Datei eingefügt werden soll `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="db90b-236">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="db90b-237">Pfade sind relativ zum Projektverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="db90b-237">Paths are relative to the project directory.</span></span>                                                                                                                                                               |
| <span data-ttu-id="db90b-238">-Namespace \< Zeichenfolge></span><span class="sxs-lookup"><span data-stu-id="db90b-238">-Namespace \<String></span></span>               | <span data-ttu-id="db90b-239">Der Namespace, der für alle generierten Klassen verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="db90b-239">The namespace to use for all generated classes.</span></span> <span data-ttu-id="db90b-240">Wird standardmäßig aus dem Stamm Namespace und dem Ausgabeverzeichnis generiert.</span><span class="sxs-lookup"><span data-stu-id="db90b-240">Defaults to generated from the root namespace and the output directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="db90b-241">-Contextnamespace- \< Zeichenfolge></span><span class="sxs-lookup"><span data-stu-id="db90b-241">-ContextNamespace \<String></span></span>        | <span data-ttu-id="db90b-242">Der Namespace, der für die generierte Klasse verwendet werden soll `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="db90b-242">The namespace to use for the generated `DbContext` class.</span></span> <span data-ttu-id="db90b-243">Hinweis: überschreibt `-Namespace` .</span><span class="sxs-lookup"><span data-stu-id="db90b-243">Note: overrides `-Namespace`.</span></span>                                                                                                                                                                              |
| <span data-ttu-id="db90b-244">-Kontext \< Zeichenfolge></span><span class="sxs-lookup"><span data-stu-id="db90b-244">-Context \<String></span></span>                 | <span data-ttu-id="db90b-245">Der Name der `DbContext` zu generierenden Klasse.</span><span class="sxs-lookup"><span data-stu-id="db90b-245">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="db90b-246">-Schema \< Zeichenfolge [] ></span><span class="sxs-lookup"><span data-stu-id="db90b-246">-Schemas \<String[]></span></span>               | <span data-ttu-id="db90b-247">Die Schemas von Tabellen, für die Entitäts Typen generiert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="db90b-247">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="db90b-248">Wenn dieser Parameter ausgelassen wird, werden alle Schemas eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="db90b-248">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="db90b-249">-Tabellen \< Zeichenfolge [] ></span><span class="sxs-lookup"><span data-stu-id="db90b-249">-Tables \<String[]></span></span>                | <span data-ttu-id="db90b-250">Die Tabellen, für die Entitäts Typen generiert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="db90b-250">The tables to generate entity types for.</span></span> <span data-ttu-id="db90b-251">Wenn dieser Parameter ausgelassen wird, werden alle Tabellen eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="db90b-251">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="db90b-252">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="db90b-252">-DataAnnotations</span></span>                   | <span data-ttu-id="db90b-253">Verwenden Sie Attribute, um das Modell zu konfigurieren (sofern möglich).</span><span class="sxs-lookup"><span data-stu-id="db90b-253">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="db90b-254">Wenn dieser Parameter ausgelassen wird, wird nur die fließende API verwendet.</span><span class="sxs-lookup"><span data-stu-id="db90b-254">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="db90b-255">-Usedatabasenames</span><span class="sxs-lookup"><span data-stu-id="db90b-255">-UseDatabaseNames</span></span>                  | <span data-ttu-id="db90b-256">Verwenden Sie Tabellen-und Spaltennamen genau so, wie Sie in der Datenbank angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="db90b-256">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="db90b-257">Wenn dieser Parameter ausgelassen wird, werden Datenbanknamen geändert, sodass Sie den c#-namens Stil Konventionen genauer entsprechen.</span><span class="sxs-lookup"><span data-stu-id="db90b-257">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="db90b-258">-Force</span><span class="sxs-lookup"><span data-stu-id="db90b-258">-Force</span></span>                             | <span data-ttu-id="db90b-259">Überschreibt vorhandene Dateien.</span><span class="sxs-lookup"><span data-stu-id="db90b-259">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |

<span data-ttu-id="db90b-260">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="db90b-260">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="db90b-261">Beispiel, bei dem nur ausgewählte Tabellen gerüziert werden und der Kontext in einem separaten Ordner mit einem angegebenen Namen und Namespace erstellt wird:</span><span class="sxs-lookup"><span data-stu-id="db90b-261">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name and namespace:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-migration"></a><span data-ttu-id="db90b-262">Skript-Migration</span><span class="sxs-lookup"><span data-stu-id="db90b-262">Script-Migration</span></span>

<span data-ttu-id="db90b-263">Generiert ein SQL-Skript, mit dem alle Änderungen von einer ausgewählten Migration zu einer anderen ausgewählten Migration übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="db90b-263">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="db90b-264">Parameter:</span><span class="sxs-lookup"><span data-stu-id="db90b-264">Parameters:</span></span>

| <span data-ttu-id="db90b-265">Parameter</span><span class="sxs-lookup"><span data-stu-id="db90b-265">Parameter</span></span>                | <span data-ttu-id="db90b-266">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="db90b-266">Description</span></span>                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="db90b-267">*-Von* \< Zeichen folgen></span><span class="sxs-lookup"><span data-stu-id="db90b-267">*-From* \<String></span></span>        | <span data-ttu-id="db90b-268">Die Migration wird gestartet.</span><span class="sxs-lookup"><span data-stu-id="db90b-268">The starting migration.</span></span> <span data-ttu-id="db90b-269">Migrationen können anhand des Namens oder der ID identifiziert werden.</span><span class="sxs-lookup"><span data-stu-id="db90b-269">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="db90b-270">Die Zahl 0 (null) ist ein Sonderfall, der *vor der ersten Migration*liegt.</span><span class="sxs-lookup"><span data-stu-id="db90b-270">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="db90b-271">Der Standardwert ist 0.</span><span class="sxs-lookup"><span data-stu-id="db90b-271">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="db90b-272">*Bis* \< Zeichen folgen></span><span class="sxs-lookup"><span data-stu-id="db90b-272">*-To* \<String></span></span>          | <span data-ttu-id="db90b-273">Die Beendigung der Migration.</span><span class="sxs-lookup"><span data-stu-id="db90b-273">The ending migration.</span></span> <span data-ttu-id="db90b-274">Standardmäßig wird die letzte Migration verwendet.</span><span class="sxs-lookup"><span data-stu-id="db90b-274">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="db90b-275"><nobr>-Idempotent</nobr></span><span class="sxs-lookup"><span data-stu-id="db90b-275"><nobr>-Idempotent</nobr></span></span> | <span data-ttu-id="db90b-276">Generieren Sie ein Skript, das bei jeder Migration in einer Datenbank verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="db90b-276">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="db90b-277">-Ausgabe \< Zeichenfolge></span><span class="sxs-lookup"><span data-stu-id="db90b-277">-Output \<String></span></span>        | <span data-ttu-id="db90b-278">Die Datei, in die das Ergebnis geschrieben werden soll.</span><span class="sxs-lookup"><span data-stu-id="db90b-278">The file to write the result to.</span></span> <span data-ttu-id="db90b-279">Wenn dieser Parameter ausgelassen wird, wird die Datei mit einem generierten Namen im selben Ordner erstellt, in dem die Laufzeitdateien der App erstellt werden, z. b.: */obj/Debug/netcoreapp2.1/ghbkztfz.SQL/*.</span><span class="sxs-lookup"><span data-stu-id="db90b-279">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

> [!TIP]
> <span data-ttu-id="db90b-280">Die Parameter to, from und Output unterstützen die Tab-Erweiterung.</span><span class="sxs-lookup"><span data-stu-id="db90b-280">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="db90b-281">Im folgenden Beispiel wird ein Skript für die InitialCreate-Migration mit dem Namen der Migration erstellt.</span><span class="sxs-lookup"><span data-stu-id="db90b-281">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="db90b-282">Im folgenden Beispiel wird ein Skript für alle Migrationen nach der InitialCreate-Migration mit der Migrations-ID erstellt.</span><span class="sxs-lookup"><span data-stu-id="db90b-282">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="db90b-283">Update-Database</span><span class="sxs-lookup"><span data-stu-id="db90b-283">Update-Database</span></span>

<span data-ttu-id="db90b-284">Aktualisiert die Datenbank auf die letzte Migration oder eine angegebene Migration.</span><span class="sxs-lookup"><span data-stu-id="db90b-284">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="db90b-285">Parameter</span><span class="sxs-lookup"><span data-stu-id="db90b-285">Parameter</span></span>                           | <span data-ttu-id="db90b-286">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="db90b-286">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="db90b-287"><nobr>*-Migration* \< Zeichen folgen></nobr></span><span class="sxs-lookup"><span data-stu-id="db90b-287"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="db90b-288">Die Ziel Migration.</span><span class="sxs-lookup"><span data-stu-id="db90b-288">The target migration.</span></span> <span data-ttu-id="db90b-289">Migrationen können anhand des Namens oder der ID identifiziert werden.</span><span class="sxs-lookup"><span data-stu-id="db90b-289">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="db90b-290">Die Zahl 0 (null) ist ein Sonderfall, der *vor der ersten Migration* steht und bewirkt, dass alle Migrationen rückgängig gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="db90b-290">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="db90b-291">Wenn keine Migration angegeben ist, wird für den Befehl standardmäßig die letzte Migration verwendet.</span><span class="sxs-lookup"><span data-stu-id="db90b-291">If no migration is specified, the command defaults to the last migration.</span></span> |
| <span data-ttu-id="db90b-292"><nobr>-Verbindungs \< Zeichenfolge></nobr></span><span class="sxs-lookup"><span data-stu-id="db90b-292"><nobr>-Connection \<String></nobr></span></span>  | <span data-ttu-id="db90b-293">Die Verbindungszeichenfolge für die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="db90b-293">The connection string to the database.</span></span> <span data-ttu-id="db90b-294">Der Standardwert ist der in `AddDbContext` oder angegebene `OnConfiguring` .</span><span class="sxs-lookup"><span data-stu-id="db90b-294">Defaults to the one specified in `AddDbContext` or `OnConfiguring`.</span></span> |

> [!TIP]
> <span data-ttu-id="db90b-295">Der Migrations Parameter unterstützt die Erweiterung der Registerkarte.</span><span class="sxs-lookup"><span data-stu-id="db90b-295">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="db90b-296">Im folgenden Beispiel werden alle Migrationen wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="db90b-296">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="db90b-297">In den folgenden Beispielen wird die-Datenbank auf eine angegebene Migration aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="db90b-297">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="db90b-298">Der erste verwendet den Migrations Namen, der zweite verwendet die Migrations-ID und eine angegebene Verbindung:</span><span class="sxs-lookup"><span data-stu-id="db90b-298">The first uses the migration name and the second uses the migration ID and a specified connection:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a><span data-ttu-id="db90b-299">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="db90b-299">Additional resources</span></span>

* [<span data-ttu-id="db90b-300">Migrationen</span><span class="sxs-lookup"><span data-stu-id="db90b-300">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="db90b-301">Reverse Engineering</span><span class="sxs-lookup"><span data-stu-id="db90b-301">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
