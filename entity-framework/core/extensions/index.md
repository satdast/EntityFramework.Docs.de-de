---
title: Tools und Erweiterungen – EF Core
author: ErikEJ
ms.date: 04/11/2020
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: 5b7945bd24ac19a82b08f4d5709555cf53485f4c
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238254"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="2cd52-102">EF Core Tools und -Erweiterungen</span><span class="sxs-lookup"><span data-stu-id="2cd52-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="2cd52-103">Diese Tools und Erweiterungen bieten zusätzliche Funktionen für Entity Framework Core 2.1 und höher.</span><span class="sxs-lookup"><span data-stu-id="2cd52-103">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="2cd52-104">Erweiterungen werden durch eine Vielzahl von Quellen erstellt und nicht als Teil des Entity Framework Core-Projekts verwaltet.</span><span class="sxs-lookup"><span data-stu-id="2cd52-104">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="2cd52-105">Wenn Sie die Erweiterung eines Drittanbieters in Betracht ziehen, sollten Sie Qualität, Lizenzierung, Kompatibilität, Support usw. auswerten, um sicherzustellen, dass diese Ihren Anforderungen entspricht.</span><span class="sxs-lookup"><span data-stu-id="2cd52-105">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="2cd52-106">Bei Erweiterungen, die für eine ältere EF Core-Version erstellt wurden, ist insbesondere darauf zu achten, dass sie möglicherweise aktualisiert werden müssen, bevor sie mit den neuesten Versionen kompatibel sind.</span><span class="sxs-lookup"><span data-stu-id="2cd52-106">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="2cd52-107">Tools</span><span class="sxs-lookup"><span data-stu-id="2cd52-107">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="2cd52-108">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="2cd52-108">LLBLGen Pro</span></span>

<span data-ttu-id="2cd52-109">LLBLGen Pro ist eine Entitätsmodelllösung, die Entity Framework und Entity Framework Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd52-109">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="2cd52-110">Sie können problemlos ihr Entitätsmodell definieren und es Ihrer Datenbank zuordnen, indem Sie Database First oder Model First verwenden, sodass Sie sofort mit dem Schreiben von Abfragen beginnen können.</span><span class="sxs-lookup"><span data-stu-id="2cd52-110">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="2cd52-111">Für EF Core: 2, 3</span><span class="sxs-lookup"><span data-stu-id="2cd52-111">For EF Core: 2, 3</span></span>

[<span data-ttu-id="2cd52-112">Website</span><span class="sxs-lookup"><span data-stu-id="2cd52-112">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="2cd52-113">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="2cd52-113">Devart Entity Developer</span></span>

<span data-ttu-id="2cd52-114">Entity Developer ist ein leistungsstarker ORM-Designer für ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access und LINQ to SQL.</span><span class="sxs-lookup"><span data-stu-id="2cd52-114">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="2cd52-115">Sie können damit EF Core-Modelle mithilfe von Model First oder Database First visuell entwerfen sowie C#- oder Visual Basic-Code generieren.</span><span class="sxs-lookup"><span data-stu-id="2cd52-115">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="2cd52-116">Für EF Core: 1, 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="2cd52-116">For EF Core: 1, 2, 3, 5.</span></span>

[<span data-ttu-id="2cd52-117">Website</span><span class="sxs-lookup"><span data-stu-id="2cd52-117">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="2cd52-118">nHydrate ORM für Entity Framework</span><span class="sxs-lookup"><span data-stu-id="2cd52-118">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="2cd52-119">Ein ORM, der stark typisierte, erweiterbare Klassen für Entity Framework erstellt.</span><span class="sxs-lookup"><span data-stu-id="2cd52-119">An ORM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="2cd52-120">Der generierte Code ist Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="2cd52-120">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="2cd52-121">Es besteht kein Unterschied.</span><span class="sxs-lookup"><span data-stu-id="2cd52-121">There is no difference.</span></span> <span data-ttu-id="2cd52-122">Dies ist kein Ersatz für EF oder einen benutzerdefinierten ORM.</span><span class="sxs-lookup"><span data-stu-id="2cd52-122">This is not a replacement for EF or a custom ORM.</span></span> <span data-ttu-id="2cd52-123">Dabei handelt es sich um eine visuelle, Modellierungsebene, die einem Team ermöglicht, komplexe Datenbankschemas zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="2cd52-123">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="2cd52-124">Dies funktioniert gut mit SCM-Software wie Git, sodass mehrere Benutzer mit minimalen Konflikten auf Ihr Modell zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="2cd52-124">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="2cd52-125">Das Installationsprogramm verfolgt Modelländerungen und erstellt Upgradeskripts.</span><span class="sxs-lookup"><span data-stu-id="2cd52-125">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="2cd52-126">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-126">For EF Core: 3.</span></span>

[<span data-ttu-id="2cd52-127">GitHub-Website</span><span class="sxs-lookup"><span data-stu-id="2cd52-127">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="2cd52-128">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="2cd52-128">EF Core Power Tools</span></span>

<span data-ttu-id="2cd52-129">EF Core Power Tools ist eine Erweiterung von Visual Studio, die verschiedene Aufgaben von EF Core zur Entwurfszeit in einer einfachen Benutzeroberfläche verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="2cd52-129">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="2cd52-130">Dadurch wird Reverse Engineering (Zurückentwicklung) von DbContext- und Entitätsklassen aus vorhandenen Datenbanken und [SQL Server-DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), die Verwaltung von Datenbankmigrationen und das Vornehmen von Modellvisualisierungen ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="2cd52-130">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="2cd52-131">Für EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-131">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="2cd52-132">GitHub-Wiki</span><span class="sxs-lookup"><span data-stu-id="2cd52-132">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="2cd52-133">Visueller Editor für Entity Framework</span><span class="sxs-lookup"><span data-stu-id="2cd52-133">Entity Framework Visual Editor</span></span>

<span data-ttu-id="2cd52-134">Entity Framework Visual Editor ist eine Erweiterung von Visual Studio, mit der ein ORM-Designer für visuelle Entwürfe von EF 6- und EF Core-Klassen hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="2cd52-134">Entity Framework Visual Editor is a Visual Studio extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="2cd52-135">Code wird mithilfe von T4-Vorlagen generiert und kann somit vollständig an die Anforderungen des Benutzers angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="2cd52-135">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="2cd52-136">Vererbung, uni- und bidirektionale Zuordnungen, Enumerationen, Farbcode für Klassen und das Hinzufügen von Textblöcken für Erklärungen zu möglicherweise schwer durchschaubaren Bereichen Ihres Designs werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd52-136">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="2cd52-137">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="2cd52-137">For EF Core: 2.</span></span>

[<span data-ttu-id="2cd52-138">Marketplace</span><span class="sxs-lookup"><span data-stu-id="2cd52-138">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="2cd52-139">CatFactory</span><span class="sxs-lookup"><span data-stu-id="2cd52-139">CatFactory</span></span>

<span data-ttu-id="2cd52-140">CatFactory ist eine Gerüstbau-Engine für .NET Core, mit der die Generierung von DbContext-Klassen und -Entitäten, Zuordnungskonfigurationen und Repositoryklassen aus einer SQL Server-Datenbank automatisiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="2cd52-140">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="2cd52-141">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="2cd52-141">For EF Core: 2.</span></span>

[<span data-ttu-id="2cd52-142">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-142">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="2cd52-143">Entity Framework Core-Generator von LoreSoft</span><span class="sxs-lookup"><span data-stu-id="2cd52-143">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="2cd52-144">Entity Framework Core-Generator (efg) ist ein .NET Core-CLI-Tool, mit dem EF Core-Modelle aus einer vorhandenen Datenbank generiert werden können, wie beispielsweise `dotnet ef dbcontext scaffold`. Es unterstützt aber auch die [Neugenerierung](https://efg.loresoft.com/en/latest/regeneration/) sicheren Codes durch das Ersetzen eines Bereichs oder durch das Analysieren von Zuordnungsdateien.</span><span class="sxs-lookup"><span data-stu-id="2cd52-144">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="2cd52-145">Dieses Tool unterstützt das Generieren von Ansichtsmodellen, die Validierung und Objektzuordnungscode.</span><span class="sxs-lookup"><span data-stu-id="2cd52-145">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="2cd52-146">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="2cd52-146">For EF Core: 2.</span></span>

<span data-ttu-id="2cd52-147">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Dokumentation](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="2cd52-147">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="2cd52-148">Erweiterungen</span><span class="sxs-lookup"><span data-stu-id="2cd52-148">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="2cd52-149">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="2cd52-149">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="2cd52-150">Eine Plug-in-Bibliothek, mit deren Hilfe automatisch von EF Core durchgeführte Änderungen an den Daten in einer Verlaufstabelle aufgezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="2cd52-150">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="2cd52-151">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="2cd52-151">For EF Core: 2.</span></span>

[<span data-ttu-id="2cd52-152">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-152">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="2cd52-153">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="2cd52-153">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="2cd52-154">Das Zwischenspeichern zweiter Ebene ist ein Abfragecache.</span><span class="sxs-lookup"><span data-stu-id="2cd52-154">Second level caching is a query cache.</span></span> <span data-ttu-id="2cd52-155">Die Ergebnisse von EF-Befehlen werden im Cache gespeichert, sodass die gleichen EF-Befehle ihre Daten eher aus dem Cache abrufen, als die Datenbank erneut zu durchsuchen.</span><span class="sxs-lookup"><span data-stu-id="2cd52-155">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="2cd52-156">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-156">For EF Core: 3.</span></span>

[<span data-ttu-id="2cd52-157">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-157">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="2cd52-158">Geco</span><span class="sxs-lookup"><span data-stu-id="2cd52-158">Geco</span></span>

<span data-ttu-id="2cd52-159">Geco (Generator-Konsole) ist ein einfacher Code-Generator, der auf einem Konsolenprojekt basiert, das auf .NET Core ausgeführt wird und interpolierte Zeichenfolgen in C# für die Codegenerierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="2cd52-159">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="2cd52-160">Geco enthält einen Reversemodell-Generator für EF Core, der die Pluralisierung, Singularisierung und bearbeitbare Vorlagen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2cd52-160">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="2cd52-161">Er enthält auch einen Startdatenskript-Generator, einen Skript-Runner sowie eine Datenbankbereinigung.</span><span class="sxs-lookup"><span data-stu-id="2cd52-161">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="2cd52-162">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="2cd52-162">For EF Core: 2.</span></span>

[<span data-ttu-id="2cd52-163">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-163">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="2cd52-164">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="2cd52-164">EntityFrameworkCore.Scaffolding.Handlebars</span></span> 

<span data-ttu-id="2cd52-165">Mit dieser Erweiterung können Klassen angepasst werden, die per Reverse Engineering mithilfe der Entity Framework Core-Toolkette mit Handlebars-Vorlagen aus einer vorhandenen Datenbank erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="2cd52-165">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="2cd52-166">Für EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-166">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="2cd52-167">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-167">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="2cd52-168">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="2cd52-168">NeinLinq.EntityFrameworkCore</span></span> 

<span data-ttu-id="2cd52-169">NeinLinq ist eine Erweiterung für LINQ-Anbieter wie Entity Framework, um Funktionen wiederverwenden zu können, Abfragen erneut schreiben zu können und dynamische Abfragen mithilfe von übersetzbaren Prädikaten und Selektoren erstellen zu können.</span><span class="sxs-lookup"><span data-stu-id="2cd52-169">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="2cd52-170">Für EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-170">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="2cd52-171">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-171">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="2cd52-172">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="2cd52-172">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="2cd52-173">Ein Plug-In für Microsoft.EntityFrameworkCore zur Unterstützung von Repositorys, Arbeitseinheitsmustern und mehreren Datenbanken, die verteilte Transaktionen unterstützen.</span><span class="sxs-lookup"><span data-stu-id="2cd52-173">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="2cd52-174">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="2cd52-174">For EF Core: 2.</span></span>

[<span data-ttu-id="2cd52-175">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-175">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="2cd52-176">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="2cd52-176">EFCore.BulkExtensions</span></span>

<span data-ttu-id="2cd52-177">EF Core-Erweiterungen für Massenvorgänge (Einfügen, Aktualisieren, Löschen)</span><span class="sxs-lookup"><span data-stu-id="2cd52-177">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="2cd52-178">Für EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-178">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="2cd52-179">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-179">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="2cd52-180">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="2cd52-180">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="2cd52-181">Diese Erweiterung fügt die Pluralisierung zur Entwurfszeit hinzu.</span><span class="sxs-lookup"><span data-stu-id="2cd52-181">Adds design-time pluralization.</span></span> <span data-ttu-id="2cd52-182">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="2cd52-182">For EF Core: 2.</span></span>

[<span data-ttu-id="2cd52-183">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-183">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="2cd52-184">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="2cd52-184">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="2cd52-185">Über diese Erweiterung ist das [Index]-Attribut mit der Erweiterung für die Modellerstellung wieder verfügbar.</span><span class="sxs-lookup"><span data-stu-id="2cd52-185">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="2cd52-186">Für EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-186">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="2cd52-187">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-187">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="2cd52-188">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="2cd52-188">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="2cd52-189">Bietet einen Wrapper, der den In-Memory-Datenbankanbieter für EF Core umschließt.</span><span class="sxs-lookup"><span data-stu-id="2cd52-189">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="2cd52-190">Fungiert dadurch mehr als relationaler Anbieter.</span><span class="sxs-lookup"><span data-stu-id="2cd52-190">Makes it act more like a relational provider.</span></span> <span data-ttu-id="2cd52-191">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="2cd52-191">For EF Core: 2.</span></span>

[<span data-ttu-id="2cd52-192">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-192">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="2cd52-193">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="2cd52-193">EFCore.TemporalSupport</span></span>

<span data-ttu-id="2cd52-194">Mit dieser Erweiterung wird die Unterstützung temporaler Daten implementiert.</span><span class="sxs-lookup"><span data-stu-id="2cd52-194">An implementation of temporal support.</span></span> <span data-ttu-id="2cd52-195">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="2cd52-195">For EF Core: 2.</span></span>

[<span data-ttu-id="2cd52-196">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-196">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="2cd52-197">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="2cd52-197">EfCoreTemporalTable</span></span>

<span data-ttu-id="2cd52-198">Mit dieser Erweiterung können Sie mithilfe bereits eingeführter Erweiterungsmethoden temporale Abfragen für eine Datenbank Ihrer Wahl ausführen: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span><span class="sxs-lookup"><span data-stu-id="2cd52-198">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="2cd52-199">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-199">For EF Core: 3.</span></span>

[<span data-ttu-id="2cd52-200">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-200">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="2cd52-201">EFCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="2cd52-201">EFCore.TimeTraveler</span></span>

<span data-ttu-id="2cd52-202">Diese Erweiterung ermöglicht vollständige Entity Framework Core-Abfragen von [temporalen SQL Server-Verlaufsdaten](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel). Dazu werden der von Ihnen bereits definierte EF Core-Code sowie die dazugehörigen Entitäten und Zuordnungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="2cd52-202">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="2cd52-203">Führen Sie Zeitpunktanalysen (Zeitreise) durch, indem Sie Ihren Code mithilfe der Anweisung `using (TemporalQuery.AsOf(targetDateTime)) {...}` umschließen.</span><span class="sxs-lookup"><span data-stu-id="2cd52-203">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="2cd52-204">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-204">For EF Core: 3.</span></span>

[<span data-ttu-id="2cd52-205">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-205">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="2cd52-206">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="2cd52-206">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="2cd52-207">Eine Erweiterungsbibliothek für Entity Framework Core, mit der Entwickler, die SQL Server nutzen, temporale Tabellen einfach verwenden können.</span><span class="sxs-lookup"><span data-stu-id="2cd52-207">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="2cd52-208">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="2cd52-208">For EF Core: 2.</span></span>

[<span data-ttu-id="2cd52-209">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-209">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="2cd52-210">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="2cd52-210">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="2cd52-211">Ein Abfragecache auf zweiter Ebene mit hoher Leistung.</span><span class="sxs-lookup"><span data-stu-id="2cd52-211">A high-performance second-level query cache.</span></span> <span data-ttu-id="2cd52-212">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="2cd52-212">For EF Core: 2.</span></span>

[<span data-ttu-id="2cd52-213">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-213">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a><span data-ttu-id="2cd52-214">EntityFrameworkCore.NCache</span><span class="sxs-lookup"><span data-stu-id="2cd52-214">EntityFrameworkCore.NCache</span></span>

<span data-ttu-id="2cd52-215">Der NCache-Anbieter von Entity Framework Core ist ein verteilter Cacheanbieter der zweiten Ebene zum Zwischenspeichern von Abfrageergebnissen.</span><span class="sxs-lookup"><span data-stu-id="2cd52-215">NCache Entity Framework Core Provider is a distributed second level cache provider for caching query results.</span></span> <span data-ttu-id="2cd52-216">Die verteilte Architektur von NCache macht den Anbieter skalierbarer und hochverfügbar.</span><span class="sxs-lookup"><span data-stu-id="2cd52-216">The distributed architecture of NCache makes it more scalable and highly available.</span></span> <span data-ttu-id="2cd52-217">Für EF Core 2.</span><span class="sxs-lookup"><span data-stu-id="2cd52-217">For EF Core 2.</span></span>

[<span data-ttu-id="2cd52-218">Website</span><span class="sxs-lookup"><span data-stu-id="2cd52-218">Website</span></span>](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entity-framework-plus"></a><span data-ttu-id="2cd52-219">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="2cd52-219">Entity Framework Plus</span></span>

<span data-ttu-id="2cd52-220">Erweitert Ihren DbContext mit Features wie den folgenden: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update und vielen weiteren.</span><span class="sxs-lookup"><span data-stu-id="2cd52-220">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="2cd52-221">Für EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-221">For EF Core: 2, 3.</span></span>

<span data-ttu-id="2cd52-222">[Website](https://entityframework-plus.net/)
[GitHub-Repository](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="2cd52-222">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="2cd52-223">Entity Framework-Erweiterungen</span><span class="sxs-lookup"><span data-stu-id="2cd52-223">Entity Framework Extensions</span></span>

<span data-ttu-id="2cd52-224">Erweitert Ihren DbContext mit hochleistungsfähigen Massenvorgängen: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge und vielen weiteren.</span><span class="sxs-lookup"><span data-stu-id="2cd52-224">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="2cd52-225">Für EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-225">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="2cd52-226">Website</span><span class="sxs-lookup"><span data-stu-id="2cd52-226">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="2cd52-227">Expressionify</span><span class="sxs-lookup"><span data-stu-id="2cd52-227">Expressionify</span></span>

<span data-ttu-id="2cd52-228">Fügt Unterstützung für das Aufrufen von Erweiterungsmethoden in LINQ-Lambdas hinzu.</span><span class="sxs-lookup"><span data-stu-id="2cd52-228">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="2cd52-229">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-229">For EF Core: 3.</span></span>

[<span data-ttu-id="2cd52-230">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-230">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a><span data-ttu-id="2cd52-231">XLinq</span><span class="sxs-lookup"><span data-stu-id="2cd52-231">XLinq</span></span>

<span data-ttu-id="2cd52-232">LINQ-Technologie (Language Integrated Query) für relationale Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="2cd52-232">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="2cd52-233">Sie ermöglicht Ihnen, mit C# stark typisierte Abfragen zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="2cd52-233">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="2cd52-234">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-234">For EF Core: 3.</span></span>

- <span data-ttu-id="2cd52-235">Volle Unterstützung von C# für das Erstellen von Abfragen: mehrere Anweisungen innerhalb von Lambdaausdrücken, Variablen, Funktionen usw.</span><span class="sxs-lookup"><span data-stu-id="2cd52-235">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="2cd52-236">Keine semantische Lücke zu SQL.</span><span class="sxs-lookup"><span data-stu-id="2cd52-236">No semantic gap with SQL.</span></span> <span data-ttu-id="2cd52-237">XLinq deklariert SQL-Anweisungen (wie `SELECT`, `FROM`, `WHERE`) als erstklassige C#-Methoden, wobei die vertraute Syntax mit Intellisense, Typsicherheit und Refactoring kombiniert wird.</span><span class="sxs-lookup"><span data-stu-id="2cd52-237">XLinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="2cd52-238">Infolgedessen wird SQL einfach zu einer „weiteren“ Klassenbibliothek, die ihre API lokal verfügbar macht, wörtlich *Sprachintegrierte SQL*.</span><span class="sxs-lookup"><span data-stu-id="2cd52-238">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="2cd52-239">Website</span><span class="sxs-lookup"><span data-stu-id="2cd52-239">Website</span></span>](http://xlinq.live/)

### <a name="ramses"></a><span data-ttu-id="2cd52-240">Ramses</span><span class="sxs-lookup"><span data-stu-id="2cd52-240">Ramses</span></span>

<span data-ttu-id="2cd52-241">Lebenszyklushooks (für SaveChanges).</span><span class="sxs-lookup"><span data-stu-id="2cd52-241">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="2cd52-242">Für EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-242">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="2cd52-243">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-243">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="2cd52-244">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="2cd52-244">EFCore.NamingConventions</span></span>

<span data-ttu-id="2cd52-245">Hiermit wird automatisch snake_case-Schreibung, reine Großschreibung oder reine Kleinschreibung auf alle Tabellen- und Spaltennamen angewendet.</span><span class="sxs-lookup"><span data-stu-id="2cd52-245">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="2cd52-246">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-246">For EF Core: 3.</span></span>

[<span data-ttu-id="2cd52-247">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-247">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="2cd52-248">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="2cd52-248">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="2cd52-249">Fügt EntityFrameworkCore für SQL Server native Unterstützung für die NodaTime-Typen hinzu.</span><span class="sxs-lookup"><span data-stu-id="2cd52-249">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="2cd52-250">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-250">For EF Core: 3.</span></span>

[<span data-ttu-id="2cd52-251">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-251">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="2cd52-252">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="2cd52-252">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="2cd52-253">LINQ-Erweiterungen für Entity Framework Core 3.1 zur Unterstützung von Abfragen temporaler Tabellen in Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="2cd52-253">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="2cd52-254">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-254">For EF Core: 3.</span></span>

[<span data-ttu-id="2cd52-255">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-255">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a><span data-ttu-id="2cd52-256">EntityFrameworkCore.SqlServer.HierarchyId</span><span class="sxs-lookup"><span data-stu-id="2cd52-256">EntityFrameworkCore.SqlServer.HierarchyId</span></span>

<span data-ttu-id="2cd52-257">Fügt dem SQL Server EF Core-Anbieter hierarchyid-Unterstützung hinzu.</span><span class="sxs-lookup"><span data-stu-id="2cd52-257">Adds hierarchyid support to the SQL Server EF Core provider.</span></span> <span data-ttu-id="2cd52-258">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-258">For EF Core: 3.</span></span>

[<span data-ttu-id="2cd52-259">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-259">GitHub repository</span></span>](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a><span data-ttu-id="2cd52-260">linq2db.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="2cd52-260">linq2db.EntityFrameworkCore</span></span>

<span data-ttu-id="2cd52-261">Alternativer Übersetzer von LINQ-Abfragen an SQL-Ausdrücke</span><span class="sxs-lookup"><span data-stu-id="2cd52-261">Alternative translator of LINQ queries to SQL expressions.</span></span> <span data-ttu-id="2cd52-262">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-262">For EF Core: 3.</span></span>

<span data-ttu-id="2cd52-263">Bietet Unterstützung für erweiterte SQL-Features wie CTEs, Massenkopiervorgänge, Tabellenhinweise, Fensterfunktionen, temporäre Tabellen und Erstell-, Update, und Löschvorgänge auf Datenbankseite</span><span class="sxs-lookup"><span data-stu-id="2cd52-263">Includes support for advanced SQL features such as CTEs, bulk copy, table hints, windowed functions, temporary tables, and database-side create/update/delete operations.</span></span>

[<span data-ttu-id="2cd52-264">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="2cd52-264">GitHub repository</span></span>](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a><span data-ttu-id="2cd52-265">EFCore.SoftDelete</span><span class="sxs-lookup"><span data-stu-id="2cd52-265">EFCore.SoftDelete</span></span>

<span data-ttu-id="2cd52-266">Eine-Implementierung für das vorläufige Löschen von Entitäten.</span><span class="sxs-lookup"><span data-stu-id="2cd52-266">An implementation for soft deleting entities.</span></span> <span data-ttu-id="2cd52-267">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="2cd52-267">For EF Core: 3.</span></span>

[<span data-ttu-id="2cd52-268">NuGet</span><span class="sxs-lookup"><span data-stu-id="2cd52-268">NuGet</span></span>](https://www.nuget.org/packages/EFCore.SoftDelete)
