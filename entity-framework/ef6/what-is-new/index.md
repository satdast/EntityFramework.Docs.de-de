---
title: Neuerungen – EF6
description: Neues in Entity Framework 6
author: ajcvickers
ms.date: 09/12/2019
uid: ef6/what-is-new/index
ms.openlocfilehash: 3c588475268ea063433f55fdb2f994d70f8c51ae
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064301"
---
# <a name="whats-new-in-ef6"></a><span data-ttu-id="81f6a-103">Neuerungen in EF6</span><span class="sxs-lookup"><span data-stu-id="81f6a-103">What's new in EF6</span></span>

<span data-ttu-id="81f6a-104">Es wird dringend empfohlen, dass Sie die neueste veröffentlichte Version von Entity Framework verwenden. So wird sichergestellt, dass Sie die neuesten Features nutzen und die höchste Stabilität erhalten.</span><span class="sxs-lookup"><span data-stu-id="81f6a-104">We highly recommend that you use the latest released version of Entity Framework to ensure you get the latest features and the highest stability.</span></span>
<span data-ttu-id="81f6a-105">Uns ist jedoch klar, dass Sie möglicherweise eine frühere Version verwenden müssen oder die neuen Verbesserungen für die aktuelle Vorabversion ausprobieren möchten.</span><span class="sxs-lookup"><span data-stu-id="81f6a-105">However, we realize that you may need to use a previous version, or that you may want to experiment with new improvements in the latest pre-release.</span></span>
<span data-ttu-id="81f6a-106">Wie Sie bestimmte Versionen von EF installieren, erfahren Sie unter [Get Entity Framework (Beziehen von Entity Framework)](xref:ef6/fundamentals/install).</span><span class="sxs-lookup"><span data-stu-id="81f6a-106">To install specific versions of EF, see [Get Entity Framework](xref:ef6/fundamentals/install).</span></span>

## <a name="ef-640"></a><span data-ttu-id="81f6a-107">EF 6.4.0</span><span class="sxs-lookup"><span data-stu-id="81f6a-107">EF 6.4.0</span></span>

<span data-ttu-id="81f6a-108">Die EF 6.4.0-Runtime wurde im Dezember 2019 für NuGet veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="81f6a-108">The EF 6.4.0 runtime was released to NuGet in December  2019.</span></span> <span data-ttu-id="81f6a-109">Das Hauptziel von EF 6.4 ist es, die Features und Szenarios von EF 6.3 zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="81f6a-109">The primary goal of EF 6.4 is to polish the features and scenarios that was delivered in EF 6.3.</span></span> <span data-ttu-id="81f6a-110">Auf GitHub finden Sie eine [Liste wichtiger Fixes](https://github.com/dotnet/ef6/milestone/14?closed=1).</span><span class="sxs-lookup"><span data-stu-id="81f6a-110">See [list of important fixes](https://github.com/dotnet/ef6/milestone/14?closed=1) on Github.</span></span>

## <a name="ef-630"></a><span data-ttu-id="81f6a-111">EF 6.3.0</span><span class="sxs-lookup"><span data-stu-id="81f6a-111">EF 6.3.0</span></span>

<span data-ttu-id="81f6a-112">Die EF 6.3.0-Runtime wurde im September 2019 für NuGet veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="81f6a-112">The EF 6.3.0 runtime was released to NuGet in September 2019.</span></span> <span data-ttu-id="81f6a-113">Das Hauptziel dieses Release bestand darin, die Migration vorhandener Anwendungen, die EF 6 verwenden, zu . NET Core 3.0 zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="81f6a-113">The main goal of this release was to facilitate migrating existing applications that use EF 6 to .NET Core 3.0.</span></span> <span data-ttu-id="81f6a-114">Die Community hat außerdem verschiedene Fehlerbehebungen und Verbesserungen beigetragen.</span><span class="sxs-lookup"><span data-stu-id="81f6a-114">The community has also contributed several bug fixes and enhancements.</span></span> <span data-ttu-id="81f6a-115">Einzelheiten zu den gelösten Problemen finden Sie in den [Meilensteinen](https://github.com/aspnet/EntityFramework6/milestones?state=closed) zu Version 6.3.0.</span><span class="sxs-lookup"><span data-stu-id="81f6a-115">See the issues closed in each 6.3.0 [milestone](https://github.com/aspnet/EntityFramework6/milestones?state=closed) for details.</span></span> <span data-ttu-id="81f6a-116">In Folgenden werden einige der wichtigsten aufgeführt:</span><span class="sxs-lookup"><span data-stu-id="81f6a-116">Here are some of the more notable ones:</span></span>

- <span data-ttu-id="81f6a-117">Unterstützung für .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="81f6a-117">Support for .NET Core 3.0</span></span>
  - <span data-ttu-id="81f6a-118">Das EntityFramework-Paket verwendet nun .NET Standard 2.1 zusätzlich zu .NET Framework 4.x als Ziel.</span><span class="sxs-lookup"><span data-stu-id="81f6a-118">The EntityFramework package now targets .NET Standard 2.1 in addition to .NET Framework 4.x.</span></span>
  - <span data-ttu-id="81f6a-119">Dies bedeutet, dass EF 6.3 plattformübergreifend ist und auf anderen Betriebssystemen als Windows unterstützt wird, wie z. B. Linux und macOS.</span><span class="sxs-lookup"><span data-stu-id="81f6a-119">This means that EF 6.3 is cross-platform and supported on other operating systems besides Windows, like Linux and macOS.</span></span>
  - <span data-ttu-id="81f6a-120">Die Migrationsbefehle wurden so umgeschrieben, dass Sie außerhalb des Prozesses ausgeführt werden und mit Projekten im SDK-Stil funktionieren.</span><span class="sxs-lookup"><span data-stu-id="81f6a-120">The migrations commands have been rewritten to execute out of process and work with SDK-style projects.</span></span>
- <span data-ttu-id="81f6a-121">Unterstützung für HierarchyId von SQL Server.</span><span class="sxs-lookup"><span data-stu-id="81f6a-121">Support for SQL Server HierarchyId.</span></span>
- <span data-ttu-id="81f6a-122">Verbesserte Kompatibilität mit PackageReference von Roslyn und NuGet.</span><span class="sxs-lookup"><span data-stu-id="81f6a-122">Improved compatibility with Roslyn and NuGet PackageReference.</span></span>
- <span data-ttu-id="81f6a-123">Das `ef6.exe`-Dienstprogramm wurde zum Aktivieren, Hinzufügen, Skripten und Anwenden von Migrationen aus Assemblys hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="81f6a-123">Added `ef6.exe` utility for enabling, adding, scripting, and applying migrations from assemblies.</span></span> <span data-ttu-id="81f6a-124">Hierdurch wird `migrate.exe` ersetzt.</span><span class="sxs-lookup"><span data-stu-id="81f6a-124">This replaces `migrate.exe`.</span></span>

### <a name="ef-designer-support"></a><span data-ttu-id="81f6a-125">Unterstützung des EF-Designers</span><span class="sxs-lookup"><span data-stu-id="81f6a-125">EF designer support</span></span>

<span data-ttu-id="81f6a-126">Es gibt derzeit keine Unterstützung für die direkte Verwendung des EF-Designers in .NET Core- oder .NET Standard-Projekten oder für SDK-ähnliche .NET Framework-Projekte.</span><span class="sxs-lookup"><span data-stu-id="81f6a-126">There's currently no support for using the EF designer directly on .NET Core or .NET Standard projects or on an SDK-style .NET Framework project.</span></span> 

<span data-ttu-id="81f6a-127">Sie können diese Einschränkung umgehen, indem Sie die EDMX-Datei und die generierten Klassen für die Entitäten und DbContext als verknüpfte Dateien zu einem .NET Core 3.0- oder .NET Standard 2.1-Projekt in derselben Projektmappe hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="81f6a-127">You can work around this limitation by adding the EDMX file and the generated classes for the entities and the DbContext as linked files to a .NET Core 3.0 or .NET Standard 2.1 project in the same solution.</span></span>

<span data-ttu-id="81f6a-128">Die verknüpften Dateien sehen in der Projektdatei wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="81f6a-128">The linked files will look like this in the project file:</span></span>

``` csproj 
<ItemGroup>
  <EntityDeploy Include="..\EdmxDesignHost\Entities.edmx" Link="Model\Entities.edmx" />
  <Compile Include="..\EdmxDesignHost\Entities.Context.cs" Link="Model\Entities.Context.cs" />
  <Compile Include="..\EdmxDesignHost\Thing.cs" Link="Model\Thing.cs" />
  <Compile Include="..\EdmxDesignHost\Person.cs" Link="Model\Person.cs" />
</ItemGroup>
```

<span data-ttu-id="81f6a-129">Beachten Sie, dass die EDMX-Datei mit dem EntityDeploy-Buildvorgang verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="81f6a-129">Note that the EDMX file is linked with the EntityDeploy build action.</span></span> <span data-ttu-id="81f6a-130">Dabei handelt es sich um eine spezielle MSBuild-Aufgabe (die jetzt im EF 6.3-Paket enthalten ist), mit der das EF-Modell in der Zielassembly als eingebettete Ressourcen hinzugefügt wird (oder als Dateien in den Ausgabeordner kopiert wird, abhängig von der Einstellung für die Verarbeitung von Metadatenartefakten in der EDMX-Datei).</span><span class="sxs-lookup"><span data-stu-id="81f6a-130">This is a special MSBuild task (now included in the EF 6.3 package) that takes care of adding the EF model into the target assembly as embedded resources (or copying it as files in the output folder, depending on the Metadata Artifact Processing setting in the EDMX).</span></span> <span data-ttu-id="81f6a-131">Weitere Informationen zur Einrichtung finden Sie im [EDMX-Beispiel zu .NET Core](https://aka.ms/EdmxDotNetCoreSample).</span><span class="sxs-lookup"><span data-stu-id="81f6a-131">For more details on how to get this set up, see our [EDMX .NET Core sample](https://aka.ms/EdmxDotNetCoreSample).</span></span>

<span data-ttu-id="81f6a-132">Warnung: Achten Sie darauf, dass das alte .NET Framework-Projekt (d. h. nicht SDK-ähnlich) die „echte“ EDMX-Datei definiert, _bevor_ das Projekt den Link innerhalb der SLN-Datei definiert.</span><span class="sxs-lookup"><span data-stu-id="81f6a-132">Warning: make sure the old style (i.e. non-SDK-style) .NET Framework project defining the "real" .edmx file comes _before_ the project defining the link inside the .sln file.</span></span> <span data-ttu-id="81f6a-133">Andernfalls wird beim Öffnen der EDMX-Datei im Designer die Fehlermeldung „Das Entity Framework ist im derzeit für dieses Projekt angegebenen Zielframework nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="81f6a-133">Otherwise, when you open the .edmx file in the designer, you see the error message "The Entity Framework is not available in the target framework currently specified for the project.</span></span> <span data-ttu-id="81f6a-134">Sie können das Zielframework des Projekts ändern oder das Modell in XmlEditor bearbeiten.“ angezeigt.</span><span class="sxs-lookup"><span data-stu-id="81f6a-134">You can change the target framework of the project or edit the model in the XmlEditor".</span></span>

## <a name="past-releases"></a><span data-ttu-id="81f6a-135">Frühere Releases</span><span class="sxs-lookup"><span data-stu-id="81f6a-135">Past Releases</span></span>

<span data-ttu-id="81f6a-136">Auf der Seite [Frühere Releases](xref:ef6/what-is-new/past-releases) finden Sie ein Archiv aller früheren Versionen von EF und die wichtigsten Features, die in den einzelnen Releases eingeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="81f6a-136">The [Past Releases](xref:ef6/what-is-new/past-releases) page contains an archive of all previous versions of EF and the major features that were introduced on each release.</span></span>
