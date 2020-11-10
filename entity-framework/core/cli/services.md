---
title: Entwurfszeit Dienste-EF Core
description: Informationen zu Entity Framework Core Entwurfszeit Diensten
author: bricelam
ms.date: 10/22/2020
uid: core/cli/services
ms.openlocfilehash: 07fc8a39fb106f357e135a17f6867ab338621910
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431260"
---
# <a name="design-time-services"></a><span data-ttu-id="02d4b-103">Entwurfszeitdienste</span><span class="sxs-lookup"><span data-stu-id="02d4b-103">Design-time services</span></span>

<span data-ttu-id="02d4b-104">Einige Dienste, die von den-Tools verwendet werden, werden nur zur Entwurfszeit verwendet.</span><span class="sxs-lookup"><span data-stu-id="02d4b-104">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="02d4b-105">Diese Dienste werden getrennt von den Lauf Zeit Diensten EF Core verwaltet, um zu verhindern, dass Sie mit Ihrer APP bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="02d4b-105">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="02d4b-106">Um einen dieser Dienste (z. b. den-Dienst zum Generieren von Migrations Dateien) zu überschreiben, fügen Sie dem Startprojekt eine Implementierung von hinzu `IDesignTimeServices` .</span><span class="sxs-lookup"><span data-stu-id="02d4b-106">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs#DesignTimeServices)]

## <a name="referencing-microsoftentityframeworkcoredesign"></a><span data-ttu-id="02d4b-107">Verweisen auf "Microsoft. entityframeworkcore. Design"</span><span class="sxs-lookup"><span data-stu-id="02d4b-107">Referencing Microsoft.EntityFrameworkCore.Design</span></span>

<span data-ttu-id="02d4b-108">Microsoft. entityframeworkcore. Design ist ein developmentdepenket-Paket.</span><span class="sxs-lookup"><span data-stu-id="02d4b-108">Microsoft.EntityFrameworkCore.Design is a DevelopmentDependency package.</span></span> <span data-ttu-id="02d4b-109">Dies bedeutet, dass die Abhängigkeit nicht transitiv in andere Projekte fließt und dass Sie standardmäßig nicht auf die Typen verweisen können.</span><span class="sxs-lookup"><span data-stu-id="02d4b-109">This means that the dependency won't flow transitively into other projects, and that you cannot, by default, reference its types.</span></span>

<span data-ttu-id="02d4b-110">Um auf die Typen zu verweisen und Entwurfszeit Dienste zu überschreiben, aktualisieren Sie die Metadaten des packagereferenzierungselements in der Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="02d4b-110">In order to reference its types and override design-time services, update the PackageReference item's metadata in your project file.</span></span>

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.9">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="02d4b-111">Wenn auf das Paket transitiv über Microsoft. entityframeworkcore. Tools verwiesen wird, müssen Sie dem Paket eine explizite packagereferenzierung hinzufügen und die zugehörigen Metadaten ändern.</span><span class="sxs-lookup"><span data-stu-id="02d4b-111">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package and change its metadata.</span></span>

## <a name="list-of-services"></a><span data-ttu-id="02d4b-112">Liste der Dienste</span><span class="sxs-lookup"><span data-stu-id="02d4b-112">List of services</span></span>

<span data-ttu-id="02d4b-113">Im folgenden finden Sie eine Liste der Entwurfszeit Dienste.</span><span class="sxs-lookup"><span data-stu-id="02d4b-113">The following is a list of the design-time services.</span></span>

<span data-ttu-id="02d4b-114">Dienst</span><span class="sxs-lookup"><span data-stu-id="02d4b-114">Service</span></span>                                                                              | <span data-ttu-id="02d4b-115">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="02d4b-115">Description</span></span>
------------------------------------------------------------------------------------ | -----------
<xref:Microsoft.EntityFrameworkCore.Design.IAnnotationCodeGenerator>                 | <span data-ttu-id="02d4b-116">Generiert den Code für entsprechende Modell Anmerkungen.</span><span class="sxs-lookup"><span data-stu-id="02d4b-116">Generates the code for corresponding model annotations.</span></span>
<xref:Microsoft.EntityFrameworkCore.Design.ICSharpHelper>                            | <span data-ttu-id="02d4b-117">Unterstützt die Erstellung von c#-Code.</span><span class="sxs-lookup"><span data-stu-id="02d4b-117">Helps with generating C# code.</span></span>
<xref:Microsoft.EntityFrameworkCore.Design.IPluralizer>                              | <span data-ttu-id="02d4b-118">Pluralisiert und singularisiert Wörter.</span><span class="sxs-lookup"><span data-stu-id="02d4b-118">Pluralizes and singularizes words.</span></span>
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsCodeGenerator>      | <span data-ttu-id="02d4b-119">Generiert Code für eine Migration.</span><span class="sxs-lookup"><span data-stu-id="02d4b-119">Generates code for a migration.</span></span>
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsScaffolder>         | <span data-ttu-id="02d4b-120">Die Hauptklasse für die Verwaltung von Migrations Dateien.</span><span class="sxs-lookup"><span data-stu-id="02d4b-120">The main class for managing migration files.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IDatabaseModelFactory>               | <span data-ttu-id="02d4b-121">Erstellt ein Datenbankmodell aus einer Datenbank.</span><span class="sxs-lookup"><span data-stu-id="02d4b-121">Creates a database model from a database.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IModelCodeGenerator>                 | <span data-ttu-id="02d4b-122">Generiert Code für ein Modell.</span><span class="sxs-lookup"><span data-stu-id="02d4b-122">Generates code for a model.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IProviderConfigurationCodeGenerator> | <span data-ttu-id="02d4b-123">Generiert den onkonfigurierungscode.</span><span class="sxs-lookup"><span data-stu-id="02d4b-123">Generates OnConfiguring code.</span></span>
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IReverseEngineerScaffolder>          | <span data-ttu-id="02d4b-124">Die Hauptklasse für das Gerüstbau von rückwärts entwickelten Modellen.</span><span class="sxs-lookup"><span data-stu-id="02d4b-124">The main class for scaffolding reverse engineered models.</span></span>

## <a name="using-services"></a><span data-ttu-id="02d4b-125">Verwenden von Diensten</span><span class="sxs-lookup"><span data-stu-id="02d4b-125">Using services</span></span>

<span data-ttu-id="02d4b-126">Diese Dienste können auch zum Erstellen eigener Tools nützlich sein.</span><span class="sxs-lookup"><span data-stu-id="02d4b-126">These services can also be useful for creating your own tools.</span></span> <span data-ttu-id="02d4b-127">Beispielsweise, wenn Sie einen Teil des Entwurfszeit Workflows automatisieren möchten.</span><span class="sxs-lookup"><span data-stu-id="02d4b-127">For example, when you want to automate part of you design-time workflow.</span></span>

<span data-ttu-id="02d4b-128">Mithilfe der Erweiterungs Methoden addentityframeworkdesigntimeservices und adddbcontextdesigntimeservices können Sie einen Dienstanbieter erstellen, der diese Dienste enthält.</span><span class="sxs-lookup"><span data-stu-id="02d4b-128">You can build a service provider containing these services using the AddEntityFrameworkDesignTimeServices and AddDbContextDesignTimeServices extension methods.</span></span>

[!code-csharp[](../../../samples/core/Miscellaneous/CommandLine/CustomTools.cs#CustomTools)]
