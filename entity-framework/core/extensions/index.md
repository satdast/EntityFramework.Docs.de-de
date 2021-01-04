---
title: Tools und Erweiterungen – EF Core
description: Externe Tools und Erweiterungen für Entity Framework Core
author: ErikEJ
ms.date: 11/22/2020
uid: core/extensions/index
ms.openlocfilehash: 4136710567d0f6b088a0dd5ad9895d8b93313e5e
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635405"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="ba658-103">EF Core Tools und -Erweiterungen</span><span class="sxs-lookup"><span data-stu-id="ba658-103">EF Core Tools & Extensions</span></span>

<span data-ttu-id="ba658-104">Diese Tools und Erweiterungen bieten zusätzliche Funktionen für Entity Framework Core 2.1 und höher.</span><span class="sxs-lookup"><span data-stu-id="ba658-104">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="ba658-105">Erweiterungen werden durch eine Vielzahl von Quellen erstellt und nicht als Teil des Entity Framework Core-Projekts verwaltet.</span><span class="sxs-lookup"><span data-stu-id="ba658-105">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="ba658-106">Wenn Sie die Erweiterung eines Drittanbieters in Betracht ziehen, sollten Sie Qualität, Lizenzierung, Kompatibilität, Support usw. auswerten, um sicherzustellen, dass diese Ihren Anforderungen entspricht.</span><span class="sxs-lookup"><span data-stu-id="ba658-106">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="ba658-107">Bei Erweiterungen, die für eine ältere EF Core-Version erstellt wurden, ist insbesondere darauf zu achten, dass sie möglicherweise aktualisiert werden müssen, bevor sie mit den neuesten Versionen kompatibel sind.</span><span class="sxs-lookup"><span data-stu-id="ba658-107">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="ba658-108">Tools</span><span class="sxs-lookup"><span data-stu-id="ba658-108">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="ba658-109">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="ba658-109">LLBLGen Pro</span></span>

<span data-ttu-id="ba658-110">LLBLGen Pro ist eine Entitätsmodelllösung, die Entity Framework und Entity Framework Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ba658-110">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="ba658-111">Sie können problemlos ihr Entitätsmodell definieren und es Ihrer Datenbank zuordnen, indem Sie Database First oder Model First verwenden, sodass Sie sofort mit dem Schreiben von Abfragen beginnen können.</span><span class="sxs-lookup"><span data-stu-id="ba658-111">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="ba658-112">Für EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-112">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="ba658-113">Website</span><span class="sxs-lookup"><span data-stu-id="ba658-113">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="ba658-114">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="ba658-114">Devart Entity Developer</span></span>

<span data-ttu-id="ba658-115">Entity Developer ist ein leistungsstarker O/RM-Designer für ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access und LINQ to SQL.</span><span class="sxs-lookup"><span data-stu-id="ba658-115">Entity Developer is a powerful O/RM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="ba658-116">Sie können damit EF Core-Modelle mithilfe von Model First oder Database First visuell entwerfen sowie C#- oder Visual Basic-Code generieren.</span><span class="sxs-lookup"><span data-stu-id="ba658-116">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="ba658-117">Für EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="ba658-117">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="ba658-118">Website</span><span class="sxs-lookup"><span data-stu-id="ba658-118">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="ba658-119">nHydrate ORM für Entity Framework</span><span class="sxs-lookup"><span data-stu-id="ba658-119">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="ba658-120">Ein O/RM, der stark typisierte, erweiterbare Klassen für Entity Framework erstellt.</span><span class="sxs-lookup"><span data-stu-id="ba658-120">An O/RM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="ba658-121">Der generierte Code ist Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="ba658-121">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="ba658-122">Es besteht kein Unterschied.</span><span class="sxs-lookup"><span data-stu-id="ba658-122">There is no difference.</span></span> <span data-ttu-id="ba658-123">Dies ist kein Ersatz für EF oder einen benutzerdefinierten O/RM.</span><span class="sxs-lookup"><span data-stu-id="ba658-123">This is not a replacement for EF or a custom O/RM.</span></span> <span data-ttu-id="ba658-124">Dabei handelt es sich um eine visuelle, Modellierungsebene, die einem Team ermöglicht, komplexe Datenbankschemas zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="ba658-124">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="ba658-125">Dies funktioniert gut mit SCM-Software wie Git, sodass mehrere Benutzer mit minimalen Konflikten auf Ihr Modell zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="ba658-125">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="ba658-126">Das Installationsprogramm verfolgt Modelländerungen und erstellt Upgradeskripts.</span><span class="sxs-lookup"><span data-stu-id="ba658-126">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="ba658-127">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-127">For EF Core: 3.</span></span>

[<span data-ttu-id="ba658-128">GitHub-Website</span><span class="sxs-lookup"><span data-stu-id="ba658-128">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="ba658-129">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="ba658-129">EF Core Power Tools</span></span>

<span data-ttu-id="ba658-130">EF Core Power Tools ist eine Erweiterung von Visual Studio, die verschiedene Aufgaben von EF Core zur Entwurfszeit in einer einfachen Benutzeroberfläche verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="ba658-130">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="ba658-131">Dadurch wird Reverse Engineering (Zurückentwicklung) von DbContext- und Entitätsklassen aus vorhandenen Datenbanken und [SQL Server-DACPACs](/sql/relational-databases/data-tier-applications/data-tier-applications), die Verwaltung von Datenbankmigrationen und das Vornehmen von Modellvisualisierungen ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="ba658-131">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="ba658-132">Für EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="ba658-132">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="ba658-133">GitHub-Wiki</span><span class="sxs-lookup"><span data-stu-id="ba658-133">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="ba658-134">Visueller Editor für Entity Framework</span><span class="sxs-lookup"><span data-stu-id="ba658-134">Entity Framework Visual Editor</span></span>

<span data-ttu-id="ba658-135">Entity Framework Visual Editor ist eine Erweiterung von Visual Studio, mit der ein O/RM-Designer für visuelle Entwürfe von EF 6- und EF Core-Klassen hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="ba658-135">Entity Framework Visual Editor is a Visual Studio extension that adds an O/RM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="ba658-136">Code wird mithilfe von T4-Vorlagen generiert und kann somit vollständig an die Anforderungen des Benutzers angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="ba658-136">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="ba658-137">Vererbung, uni- und bidirektionale Zuordnungen, Enumerationen, Farbcode für Klassen und das Hinzufügen von Textblöcken für Erklärungen zu möglicherweise schwer durchschaubaren Bereichen Ihres Designs werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ba658-137">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="ba658-138">Für EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="ba658-138">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="ba658-139">Marketplace</span><span class="sxs-lookup"><span data-stu-id="ba658-139">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="ba658-140">CatFactory</span><span class="sxs-lookup"><span data-stu-id="ba658-140">CatFactory</span></span>

<span data-ttu-id="ba658-141">CatFactory ist eine Gerüstbau-Engine für .NET Core, mit der die Generierung von DbContext-Klassen und -Entitäten, Zuordnungskonfigurationen und Repositoryklassen aus einer SQL Server-Datenbank automatisiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="ba658-141">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="ba658-142">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="ba658-142">For EF Core: 2.</span></span>

[<span data-ttu-id="ba658-143">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-143">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="ba658-144">Entity Framework Core-Generator von LoreSoft</span><span class="sxs-lookup"><span data-stu-id="ba658-144">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="ba658-145">Entity Framework Core-Generator (efg) ist ein .NET Core-CLI-Tool, mit dem EF Core-Modelle aus einer vorhandenen Datenbank generiert werden können, wie beispielsweise `dotnet ef dbcontext scaffold`. Es unterstützt aber auch die [Neugenerierung](https://efg.loresoft.com/en/latest/regeneration/) sicheren Codes durch das Ersetzen eines Bereichs oder durch das Analysieren von Zuordnungsdateien.</span><span class="sxs-lookup"><span data-stu-id="ba658-145">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="ba658-146">Dieses Tool unterstützt das Generieren von Ansichtsmodellen, die Validierung und Objektzuordnungscode.</span><span class="sxs-lookup"><span data-stu-id="ba658-146">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="ba658-147">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="ba658-147">For EF Core: 2.</span></span>

<span data-ttu-id="ba658-148">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Dokumentation](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="ba658-148">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="ba658-149">Erweiterungen</span><span class="sxs-lookup"><span data-stu-id="ba658-149">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="ba658-150">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="ba658-150">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="ba658-151">Eine Plug-in-Bibliothek, mit deren Hilfe automatisch von EF Core durchgeführte Änderungen an den Daten in einer Verlaufstabelle aufgezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="ba658-151">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="ba658-152">Für EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-152">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="ba658-153">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-153">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="ba658-154">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="ba658-154">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="ba658-155">Das Zwischenspeichern zweiter Ebene ist ein Abfragecache.</span><span class="sxs-lookup"><span data-stu-id="ba658-155">Second level caching is a query cache.</span></span> <span data-ttu-id="ba658-156">Die Ergebnisse von EF-Befehlen werden im Cache gespeichert, sodass die gleichen EF-Befehle ihre Daten eher aus dem Cache abrufen, als die Datenbank erneut zu durchsuchen.</span><span class="sxs-lookup"><span data-stu-id="ba658-156">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="ba658-157">Für EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="ba658-157">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="ba658-158">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-158">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="ba658-159">Geco</span><span class="sxs-lookup"><span data-stu-id="ba658-159">Geco</span></span>

<span data-ttu-id="ba658-160">Geco (Generator-Konsole) ist ein einfacher Code-Generator, der auf einem Konsolenprojekt basiert, das auf .NET Core ausgeführt wird und interpolierte Zeichenfolgen in C# für die Codegenerierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="ba658-160">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="ba658-161">Geco enthält einen Reversemodell-Generator für EF Core, der die Pluralisierung, Singularisierung und bearbeitbare Vorlagen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ba658-161">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="ba658-162">Er enthält auch einen Startdatenskript-Generator, einen Skript-Runner sowie eine Datenbankbereinigung.</span><span class="sxs-lookup"><span data-stu-id="ba658-162">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="ba658-163">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="ba658-163">For EF Core: 2.</span></span>

[<span data-ttu-id="ba658-164">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-164">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="ba658-165">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="ba658-165">EntityFrameworkCore.Scaffolding.Handlebars</span></span>

<span data-ttu-id="ba658-166">Mit dieser Erweiterung können Klassen angepasst werden, die per Reverse Engineering mithilfe der Entity Framework Core-Toolkette mit Handlebars-Vorlagen aus einer vorhandenen Datenbank erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="ba658-166">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="ba658-167">Für EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="ba658-167">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="ba658-168">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-168">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="ba658-169">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="ba658-169">NeinLinq.EntityFrameworkCore</span></span>

<span data-ttu-id="ba658-170">NeinLinq ist eine Erweiterung für LINQ-Anbieter wie Entity Framework, um Funktionen wiederverwenden zu können, Abfragen erneut schreiben zu können und dynamische Abfragen mithilfe von übersetzbaren Prädikaten und Selektoren erstellen zu können.</span><span class="sxs-lookup"><span data-stu-id="ba658-170">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="ba658-171">Für EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="ba658-171">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="ba658-172">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-172">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="ba658-173">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="ba658-173">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="ba658-174">Ein Plug-In für Microsoft.EntityFrameworkCore zur Unterstützung von Repositorys, Arbeitseinheitsmustern und mehreren Datenbanken, die verteilte Transaktionen unterstützen.</span><span class="sxs-lookup"><span data-stu-id="ba658-174">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="ba658-175">Für EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-175">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="ba658-176">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-176">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="ba658-177">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="ba658-177">EFCore.BulkExtensions</span></span>

<span data-ttu-id="ba658-178">EF Core-Erweiterungen für Massenvorgänge (Einfügen, Aktualisieren, Löschen)</span><span class="sxs-lookup"><span data-stu-id="ba658-178">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="ba658-179">Für EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-179">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="ba658-180">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-180">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="ba658-181">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="ba658-181">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="ba658-182">Diese Erweiterung fügt die Pluralisierung zur Entwurfszeit hinzu.</span><span class="sxs-lookup"><span data-stu-id="ba658-182">Adds design-time pluralization.</span></span> <span data-ttu-id="ba658-183">Für EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-183">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="ba658-184">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-184">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="ba658-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="ba658-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="ba658-186">Über diese Erweiterung ist das [Index]-Attribut mit der Erweiterung für die Modellerstellung wieder verfügbar.</span><span class="sxs-lookup"><span data-stu-id="ba658-186">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="ba658-187">Für EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="ba658-187">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="ba658-188">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-188">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="ba658-189">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="ba658-189">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="ba658-190">Bietet einen Wrapper, der den In-Memory-Datenbankanbieter für EF Core umschließt.</span><span class="sxs-lookup"><span data-stu-id="ba658-190">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="ba658-191">Fungiert dadurch mehr als relationaler Anbieter.</span><span class="sxs-lookup"><span data-stu-id="ba658-191">Makes it act more like a relational provider.</span></span> <span data-ttu-id="ba658-192">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="ba658-192">For EF Core: 2.</span></span>

[<span data-ttu-id="ba658-193">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-193">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="ba658-194">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="ba658-194">EFCore.TemporalSupport</span></span>

<span data-ttu-id="ba658-195">Mit dieser Erweiterung wird die Unterstützung temporaler Daten implementiert.</span><span class="sxs-lookup"><span data-stu-id="ba658-195">An implementation of temporal support.</span></span> <span data-ttu-id="ba658-196">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="ba658-196">For EF Core: 2.</span></span>

[<span data-ttu-id="ba658-197">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-197">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="ba658-198">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="ba658-198">EfCoreTemporalTable</span></span>

<span data-ttu-id="ba658-199">Mit dieser Erweiterung können Sie mithilfe bereits eingeführter Erweiterungsmethoden temporale Abfragen für eine Datenbank Ihrer Wahl ausführen: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span><span class="sxs-lookup"><span data-stu-id="ba658-199">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="ba658-200">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-200">For EF Core: 3.</span></span>

[<span data-ttu-id="ba658-201">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-201">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="ba658-202">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="ba658-202">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="ba658-203">Eine Erweiterungsbibliothek für Entity Framework Core, mit der Entwickler, die SQL Server nutzen, temporale Tabellen einfach verwenden können.</span><span class="sxs-lookup"><span data-stu-id="ba658-203">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="ba658-204">Für EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-204">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="ba658-205">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-205">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)

### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="ba658-206">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="ba658-206">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="ba658-207">Ein Abfragecache auf zweiter Ebene mit hoher Leistung.</span><span class="sxs-lookup"><span data-stu-id="ba658-207">A high-performance second-level query cache.</span></span> <span data-ttu-id="ba658-208">Für EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="ba658-208">For EF Core: 2.</span></span>

[<span data-ttu-id="ba658-209">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-209">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a><span data-ttu-id="ba658-210">EntityFrameworkCore.NCache</span><span class="sxs-lookup"><span data-stu-id="ba658-210">EntityFrameworkCore.NCache</span></span>

<span data-ttu-id="ba658-211">Der NCache-Anbieter von Entity Framework Core ist ein verteilter Cacheanbieter der zweiten Ebene zum Zwischenspeichern von Abfrageergebnissen.</span><span class="sxs-lookup"><span data-stu-id="ba658-211">NCache Entity Framework Core Provider is a distributed second level cache provider for caching query results.</span></span> <span data-ttu-id="ba658-212">Die verteilte Architektur von NCache macht den Anbieter skalierbarer und hochverfügbar.</span><span class="sxs-lookup"><span data-stu-id="ba658-212">The distributed architecture of NCache makes it more scalable and highly available.</span></span> <span data-ttu-id="ba658-213">Für EF Core 2, 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-213">For EF Core 2, 3.</span></span>

[<span data-ttu-id="ba658-214">Website</span><span class="sxs-lookup"><span data-stu-id="ba658-214">Website</span></span>](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entityframeworkcoretriggered"></a><span data-ttu-id="ba658-215">EntityFrameworkCore.Triggered</span><span class="sxs-lookup"><span data-stu-id="ba658-215">EntityFrameworkCore.Triggered</span></span>

<span data-ttu-id="ba658-216">Trigger für EF Core.</span><span class="sxs-lookup"><span data-stu-id="ba658-216">Triggers for EF Core.</span></span> <span data-ttu-id="ba658-217">Hiermit können Sie auf Änderungen an DbContext reagieren, bevor und nachdem sie in der Datenbank committet wurden.</span><span class="sxs-lookup"><span data-stu-id="ba658-217">Respond to changes in your DbContext before and after they are committed to the database.</span></span> <span data-ttu-id="ba658-218">Trigger sind vollständig asynchron und unterstützen Dependency Injection, Vererbung, Kaskadierung und vieles mehr.</span><span class="sxs-lookup"><span data-stu-id="ba658-218">Triggers are fully asynchronous and support dependency injection, inheritance, cascading and more.</span></span> <span data-ttu-id="ba658-219">Für EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="ba658-219">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="ba658-220">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-220">GitHub repository</span></span>](https://github.com/koenbeuk/EntityFrameworkCore.Triggered)

### <a name="entity-framework-plus"></a><span data-ttu-id="ba658-221">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="ba658-221">Entity Framework Plus</span></span>

<span data-ttu-id="ba658-222">Erweitert Ihren DbContext mit Features wie den folgenden: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update und vielen weiteren.</span><span class="sxs-lookup"><span data-stu-id="ba658-222">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="ba658-223">Für EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="ba658-223">For EF Core: 2, 3, 5.</span></span>

<span data-ttu-id="ba658-224">[Website](https://entityframework-plus.net/)
[GitHub-Repository](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="ba658-224">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="ba658-225">Entity Framework-Erweiterungen</span><span class="sxs-lookup"><span data-stu-id="ba658-225">Entity Framework Extensions</span></span>

<span data-ttu-id="ba658-226">Erweitert Ihren DbContext mit hochleistungsfähigen Massenvorgängen: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge und vielen weiteren.</span><span class="sxs-lookup"><span data-stu-id="ba658-226">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="ba658-227">Für EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-227">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="ba658-228">Website</span><span class="sxs-lookup"><span data-stu-id="ba658-228">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="ba658-229">Expressionify</span><span class="sxs-lookup"><span data-stu-id="ba658-229">Expressionify</span></span>

<span data-ttu-id="ba658-230">Fügt Unterstützung für das Aufrufen von Erweiterungsmethoden in LINQ-Lambdas hinzu.</span><span class="sxs-lookup"><span data-stu-id="ba658-230">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="ba658-231">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-231">For EF Core: 3.</span></span>

[<span data-ttu-id="ba658-232">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-232">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="elinq"></a><span data-ttu-id="ba658-233">ELinq</span><span class="sxs-lookup"><span data-stu-id="ba658-233">ELinq</span></span>

<span data-ttu-id="ba658-234">LINQ-Technologie (Language Integrated Query) für relationale Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="ba658-234">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="ba658-235">Sie ermöglicht Ihnen, mit C# stark typisierte Abfragen zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="ba658-235">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="ba658-236">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-236">For EF Core: 3.</span></span>

- <span data-ttu-id="ba658-237">Volle Unterstützung von C# für das Erstellen von Abfragen: mehrere Anweisungen innerhalb von Lambdaausdrücken, Variablen, Funktionen usw.</span><span class="sxs-lookup"><span data-stu-id="ba658-237">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="ba658-238">Keine semantische Lücke zu SQL.</span><span class="sxs-lookup"><span data-stu-id="ba658-238">No semantic gap with SQL.</span></span> <span data-ttu-id="ba658-239">ELinq deklariert SQL-Anweisungen (wie `SELECT`, `FROM`, `WHERE`) als erstklassige C#-Methoden, wobei die vertraute Syntax mit IntelliSense, Typsicherheit und Refactoring kombiniert wird.</span><span class="sxs-lookup"><span data-stu-id="ba658-239">ELinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="ba658-240">Infolgedessen wird SQL einfach zu einer „weiteren“ Klassenbibliothek, die ihre API lokal verfügbar macht, wörtlich *Sprachintegrierte SQL*.</span><span class="sxs-lookup"><span data-stu-id="ba658-240">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="ba658-241">Website</span><span class="sxs-lookup"><span data-stu-id="ba658-241">Website</span></span>](https://entitylinq.com/)

### <a name="ramses"></a><span data-ttu-id="ba658-242">Ramses</span><span class="sxs-lookup"><span data-stu-id="ba658-242">Ramses</span></span>

<span data-ttu-id="ba658-243">Lebenszyklushooks (für SaveChanges).</span><span class="sxs-lookup"><span data-stu-id="ba658-243">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="ba658-244">Für EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-244">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="ba658-245">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-245">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="ba658-246">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="ba658-246">EFCore.NamingConventions</span></span>

<span data-ttu-id="ba658-247">Hiermit wird automatisch snake_case-Schreibung, reine Großschreibung oder reine Kleinschreibung auf alle Tabellen- und Spaltennamen angewendet.</span><span class="sxs-lookup"><span data-stu-id="ba658-247">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="ba658-248">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-248">For EF Core: 3.</span></span>

[<span data-ttu-id="ba658-249">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-249">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="ba658-250">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="ba658-250">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="ba658-251">Fügt EntityFrameworkCore für SQL Server native Unterstützung für die NodaTime-Typen hinzu.</span><span class="sxs-lookup"><span data-stu-id="ba658-251">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="ba658-252">Für EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="ba658-252">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="ba658-253">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-253">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="ba658-254">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="ba658-254">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="ba658-255">LINQ-Erweiterungen für Entity Framework Core 3.1 zur Unterstützung von Abfragen temporaler Tabellen in Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="ba658-255">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="ba658-256">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-256">For EF Core: 3.</span></span>

[<span data-ttu-id="ba658-257">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-257">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a><span data-ttu-id="ba658-258">EntityFrameworkCore.SqlServer.HierarchyId</span><span class="sxs-lookup"><span data-stu-id="ba658-258">EntityFrameworkCore.SqlServer.HierarchyId</span></span>

<span data-ttu-id="ba658-259">Fügt dem SQL Server EF Core-Anbieter hierarchyid-Unterstützung hinzu.</span><span class="sxs-lookup"><span data-stu-id="ba658-259">Adds hierarchyid support to the SQL Server EF Core provider.</span></span> <span data-ttu-id="ba658-260">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-260">For EF Core: 3.</span></span>

[<span data-ttu-id="ba658-261">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-261">GitHub repository</span></span>](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a><span data-ttu-id="ba658-262">linq2db.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="ba658-262">linq2db.EntityFrameworkCore</span></span>

<span data-ttu-id="ba658-263">Alternativer Übersetzer von LINQ-Abfragen an SQL-Ausdrücke</span><span class="sxs-lookup"><span data-stu-id="ba658-263">Alternative translator of LINQ queries to SQL expressions.</span></span> <span data-ttu-id="ba658-264">Für EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="ba658-264">For EF Core: 3, 5.</span></span>

<span data-ttu-id="ba658-265">Bietet Unterstützung für erweiterte SQL-Features wie CTEs, Massenkopiervorgänge, Tabellenhinweise, Fensterfunktionen, temporäre Tabellen und Erstell-, Update, und Löschvorgänge auf Datenbankseite</span><span class="sxs-lookup"><span data-stu-id="ba658-265">Includes support for advanced SQL features such as CTEs, bulk copy, table hints, windowed functions, temporary tables, and database-side create/update/delete operations.</span></span>

[<span data-ttu-id="ba658-266">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-266">GitHub repository</span></span>](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a><span data-ttu-id="ba658-267">EFCore.SoftDelete</span><span class="sxs-lookup"><span data-stu-id="ba658-267">EFCore.SoftDelete</span></span>

<span data-ttu-id="ba658-268">Eine-Implementierung für das vorläufige Löschen von Entitäten.</span><span class="sxs-lookup"><span data-stu-id="ba658-268">An implementation for soft deleting entities.</span></span> <span data-ttu-id="ba658-269">Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-269">For EF Core: 3.</span></span>

[<span data-ttu-id="ba658-270">NuGet</span><span class="sxs-lookup"><span data-stu-id="ba658-270">NuGet</span></span>](https://www.nuget.org/packages/EFCore.SoftDelete)

### <a name="entityframeworkcoreconfigurationmanager"></a><span data-ttu-id="ba658-271">EntityFrameworkCore.ConfigurationManager</span><span class="sxs-lookup"><span data-stu-id="ba658-271">EntityFrameworkCore.ConfigurationManager</span></span>

<span data-ttu-id="ba658-272">Erweitert EF Core zum Auflösen von Verbindungszeichenfolgen aus „App.config“. Für EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="ba658-272">Extends EF Core to resolve connection strings from App.config. For EF Core: 3.</span></span>

[<span data-ttu-id="ba658-273">GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="ba658-273">GitHub repository</span></span>](https://github.com/efcore/EFCore.ConfigurationManager)

### <a name="detached-mapper"></a><span data-ttu-id="ba658-274">Getrennter Mapper</span><span class="sxs-lookup"><span data-stu-id="ba658-274">Detached Mapper</span></span>

<span data-ttu-id="ba658-275">Ein DTO-Entity-Mapper mit Kompositions-/Aggregationsverarbeitung (ähnlich wie GraphDiff).</span><span class="sxs-lookup"><span data-stu-id="ba658-275">A DTO-Entity mapper with composition/aggregation handling (similar to GraphDiff).</span></span> <span data-ttu-id="ba658-276">Für EF Core: 3, 5.</span><span class="sxs-lookup"><span data-stu-id="ba658-276">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="ba658-277">NuGet</span><span class="sxs-lookup"><span data-stu-id="ba658-277">NuGet</span></span>](https://www.nuget.org/packages/Detached.Mappers.EntityFramework)
