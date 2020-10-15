---
title: 'Migrationsübersicht: EF Core'
description: Übersicht über die Verwendung von Migrationen zum Verwalten von Datenbankschemas mit Entity Framework Core
author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: eea2c32cccbb678cacaa63760c4f7d36d2d19bb1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062281"
---
# <a name="migrations-overview"></a><span data-ttu-id="955ec-103">Migrationsübersicht</span><span class="sxs-lookup"><span data-stu-id="955ec-103">Migrations Overview</span></span>

<span data-ttu-id="955ec-104">In realen Projekten ändern sich die Datenmodelle mit der Implementierung von Funktionen: Neue Entitäten oder Eigenschaften werden hinzugefügt oder entfernt, und Datenbankschemas müssen entsprechend geändert werden, um mit der Anwendung synchron zu bleiben.</span><span class="sxs-lookup"><span data-stu-id="955ec-104">In real world projects, data models change as features get implemented: new entities or properties are added and removed, and database schemas needs to be changed accordingly to be kept in sync with the application.</span></span> <span data-ttu-id="955ec-105">Das Migrations-Feature in EF Core bietet einen Weg, das Datenbankschema inkrementell zu aktualisieren, um es mit dem Datenmodell der Anwendung synchron zu halten und zugleich die vorhandenen Daten in der Datenbank beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="955ec-105">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="955ec-106">Auf hoher Ebene funktionieren Migrationen wie folgt:</span><span class="sxs-lookup"><span data-stu-id="955ec-106">At a high level, migrations function in the following way:</span></span>

* <span data-ttu-id="955ec-107">Wenn eine Datenmodelländerung eingeführt wird, verwendet der Entwickler EF Core-Tools, um eine entsprechende Migration hinzuzufügen, die die Aktualisierungen beschreibt, die erforderlich sind, um das Datenbankschema synchron zu halten. EF Core vergleicht das aktuelle Modell mit einer Momentaufnahme des alten Modells, um die Unterschiede zu ermitteln, und generiert Migrationsquelldateien. Die Dateien können in der Quellcodeverwaltung Ihres Projekts wie jede andere Quelldatei nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="955ec-107">When a data model change is introduced, the developer uses EF Core tools to add a corresponding migration describing the updates necessary to keep the database schema in sync. EF Core compares the current model against a snapshot of the old model to determine the differences, and generates migration source files; the files can be tracked in your project's source control like any other source file.</span></span>
* <span data-ttu-id="955ec-108">Nachdem eine neue Migration generiert wurde, kann Sie auf verschiedene Weise auf eine Datenbank angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="955ec-108">Once a new migration has been generated, it can be applied to a database in various ways.</span></span> <span data-ttu-id="955ec-109">EF Core zeichnet alle angewendeten Migrationen in einer speziellen Verlaufstabelle auf und ermöglicht es Ihnen zu erkennen, welche Migrationen angewendet wurden und welche nicht.</span><span class="sxs-lookup"><span data-stu-id="955ec-109">EF Core records all applied migrations in a special history table, allowing it to know which migrations have been applied and which haven't.</span></span>

<span data-ttu-id="955ec-110">Der Rest dieser Seite ist eine schrittweise Anleitung für die Verwendung von Migrationen für Einsteiger.</span><span class="sxs-lookup"><span data-stu-id="955ec-110">The rest of this page is a step-by-step beginner's guide for using migrations.</span></span> <span data-ttu-id="955ec-111">Weitere ausführliche Informationen finden Sie auf den anderen Seiten in diesem Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="955ec-111">Consult the other pages in this section for more in-depth information.</span></span>

## <a name="getting-started"></a><span data-ttu-id="955ec-112">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="955ec-112">Getting started</span></span>

<span data-ttu-id="955ec-113">Angenommen, Sie haben ihre erste EF Core Anwendung abgeschlossen, die das folgende einfache Modell enthält:</span><span class="sxs-lookup"><span data-stu-id="955ec-113">Let's assume you've just completed your first EF Core application, which contains the following simple model:</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

<span data-ttu-id="955ec-114">Während der Entwicklung haben Sie vielleicht [Erstellen und Verwerfen von APIs](xref:core/managing-schemas/ensure-created) verwendet, um schnell zu iterieren und Ihr Modell nach Bedarf zu ändern. Jetzt aber, wo Ihre Anwendung in Produktion geht, benötigen Sie eine Möglichkeit, das Schema sicher zu entwickeln, ohne die gesamte Datenbank zu verwerfen.</span><span class="sxs-lookup"><span data-stu-id="955ec-114">During development, you may have used the [Create and Drop APIs](xref:core/managing-schemas/ensure-created) to iterate quickly, changing your model as needed; but now that your application is going to production, you need a way to safely evolve the schema without dropping the entire database.</span></span>

### <a name="install-the-tools"></a><span data-ttu-id="955ec-115">Installieren der Tools</span><span class="sxs-lookup"><span data-stu-id="955ec-115">Install the tools</span></span>

<span data-ttu-id="955ec-116">Zunächst müssen Sie die [EF Core-Befehlszeilentools](xref:core/miscellaneous/cli/index) installieren:</span><span class="sxs-lookup"><span data-stu-id="955ec-116">First, you'll have to install the [EF Core command-line tools](xref:core/miscellaneous/cli/index):</span></span>

* <span data-ttu-id="955ec-117">Im Allgemeinen wird empfohlen, die [.NET Core CLI-Tools](xref:core/miscellaneous/cli/dotnet) zu verwenden, die auf allen Plattformen funktionieren.</span><span class="sxs-lookup"><span data-stu-id="955ec-117">We generally recommend using the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet), which work on all platforms.</span></span>
* <span data-ttu-id="955ec-118">Wenn Ihnen die Arbeit in Visual Studio angenehmer ist oder Sie Erfahrung mit EF6-Migrationen haben, können Sie auch die [Tools der Paket-Manager-Konsole](xref:core/miscellaneous/cli/powershell) verwenden.</span><span class="sxs-lookup"><span data-stu-id="955ec-118">If you're more comfortable working inside Visual Studio or have experience with EF6 migrations, you can also use the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell).</span></span>

### <a name="create-your-first-migration"></a><span data-ttu-id="955ec-119">Erstellen Ihrer ersten Migration</span><span class="sxs-lookup"><span data-stu-id="955ec-119">Create your first migration</span></span>

<span data-ttu-id="955ec-120">Sie sind jetzt bereit, Ihre erste Migration hinzuzufügen!</span><span class="sxs-lookup"><span data-stu-id="955ec-120">You're now ready to add your first migration!</span></span> <span data-ttu-id="955ec-121">Weisen Sie EF Core an, eine Migration mit dem Namen **InitialCreate** zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="955ec-121">Instruct EF Core to create a migration named **InitialCreate**:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="955ec-122">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="955ec-122">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[<span data-ttu-id="955ec-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="955ec-123">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate
```

***

<span data-ttu-id="955ec-124">EF Core erstellt ein Verzeichnis namens **Migrations** in Ihrem Projekt und generiert einige Dateien.</span><span class="sxs-lookup"><span data-stu-id="955ec-124">EF Core will create a directory called **Migrations** in your project, and generate some files.</span></span> <span data-ttu-id="955ec-125">Es ist eine gute Idee, zu überprüfen, was genau EF Core generiert hat (und es möglicherweise zu ändern), aber wir überspringen dies vorerst.</span><span class="sxs-lookup"><span data-stu-id="955ec-125">It's a good idea to inspect what exactly EF Core generated - and possibly amend it - but we'll skip over that for now.</span></span>

### <a name="create-your-database-and-schema"></a><span data-ttu-id="955ec-126">Erstellen der Datenbank und des Schemas</span><span class="sxs-lookup"><span data-stu-id="955ec-126">Create your database and schema</span></span>

<span data-ttu-id="955ec-127">An diesem Punkt können Sie EF Ihre Datenbank und Ihr Schema aus der Migration erstellen lassen.</span><span class="sxs-lookup"><span data-stu-id="955ec-127">At this point you can have EF create your database and create your schema from the migration.</span></span> <span data-ttu-id="955ec-128">Hierzu können Sie Folgendes verwenden:</span><span class="sxs-lookup"><span data-stu-id="955ec-128">This can be done via the following:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="955ec-129">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="955ec-129">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[<span data-ttu-id="955ec-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="955ec-130">Visual Studio</span></span>](#tab/vs)

```powershell
Update-Database
```

***

<span data-ttu-id="955ec-131">Das ist alles: Ihre Anwendung ist bereit, mit Ihrer neuen Datenbank ausgeführt zu werden, und Sie mussten nicht eine einzige Zeile SQL-Code schreiben.</span><span class="sxs-lookup"><span data-stu-id="955ec-131">That's all there is to it - your application is ready to run on your new database, and you didn't need to write a single line of SQL.</span></span> <span data-ttu-id="955ec-132">Beachten Sie, dass diese Art der Anwendung von Migrationen ideal für die lokale Entwicklung ist, aber für Produktionsumgebungen weniger geeignet ist. Weitere Informationen finden Sie auf der Seite [Anwenden von Migrationen](xref:core/managing-schemas/migrations/applying).</span><span class="sxs-lookup"><span data-stu-id="955ec-132">Note that this way of applying migrations is ideal for local development, but is less suitable for production environments - see the [Applying Migrations page](xref:core/managing-schemas/migrations/applying) for more info.</span></span>

### <a name="evolving-your-model"></a><span data-ttu-id="955ec-133">Entwickeln Ihres Modells</span><span class="sxs-lookup"><span data-stu-id="955ec-133">Evolving your model</span></span>

<span data-ttu-id="955ec-134">Es sind einige Tage vergangen, und Sie werden gebeten, Ihren Blogs einen Zeitstempel für die Erstellung hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="955ec-134">A few days have passed, and you're asked to add a creation timestamp to your blogs.</span></span> <span data-ttu-id="955ec-135">Sie haben die erforderlichen Änderungen an Ihrer Anwendung vorgenommen, und Ihr Modell sieht jetzt so aus:</span><span class="sxs-lookup"><span data-stu-id="955ec-135">You've done the necessary changes to your application, and your model now looks like this:</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

<span data-ttu-id="955ec-136">Ihr Modell und ihre Produktionsdatenbank sind jetzt nicht mehr synchron. Sie müssen dem Datenbankschema eine neue Spalte hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="955ec-136">Your model and your production database are now out of sync - we must add a new column to your database schema.</span></span> <span data-ttu-id="955ec-137">Erstellen Sie dazu eine neue Migration:</span><span class="sxs-lookup"><span data-stu-id="955ec-137">Let's create a new migration for this:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="955ec-138">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="955ec-138">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[<span data-ttu-id="955ec-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="955ec-139">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="955ec-140">Beachten Sie, dass wir Migrationen einen beschreibenden Namen geben, um den Projektverlauf später leichter verständlich zu machen.</span><span class="sxs-lookup"><span data-stu-id="955ec-140">Note that we give migrations a descriptive name, to make it easier to understand the project history later.</span></span>

<span data-ttu-id="955ec-141">Da es sich nicht um die erste Migration des Projekts handelt, vergleicht EF Core jetzt Ihr aktualisiertes Modell mit einer Momentaufnahme des alten Modells, bevor die Spalte hinzugefügt wurde. Die Modellmomentaufnahme ist eine der Dateien, die von EF Core generiert wird, wenn Sie eine Migration hinzufügen, und sie wird in die Quellcodeverwaltung eingecheckt.</span><span class="sxs-lookup"><span data-stu-id="955ec-141">Since this isn't the project's first migration, EF Core now compares your updated model against a snapshot of the old model, before the column was added; the model snapshot is one of the files generated by EF Core when you add a migration, and is checked into source control.</span></span> <span data-ttu-id="955ec-142">Basierend auf diesem Vergleich erkennt EF Core, dass eine Spalte hinzugefügt wurde, und fügt die entsprechende Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="955ec-142">Based on that comparison, EF Core detects that a column has been added, and adds the appropriate migration.</span></span>

<span data-ttu-id="955ec-143">Sie können Ihre Migration jetzt wie zuvor anwenden:</span><span class="sxs-lookup"><span data-stu-id="955ec-143">You can now apply your migration as before:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="955ec-144">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="955ec-144">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[<span data-ttu-id="955ec-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="955ec-145">Visual Studio</span></span>](#tab/vs)

```powershell
Update-Database
```

***

<span data-ttu-id="955ec-146">Beachten Sie, dass EF dieses Mal erkennt, dass die Datenbank bereits vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="955ec-146">Note that this time, EF detects that the database already exists.</span></span> <span data-ttu-id="955ec-147">Darüber hinaus wurde diese Tatsache bei der Anwendung unserer ersten Migration oben in einer speziellen Migrationsverlaufstabelle in Ihrer Datenbank aufgezeichnet. Dies ermöglicht es EF, automatisch nur die neue Migration anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="955ec-147">In addition, when our first migration was applied above, this fact was recorded in a special migrations history table in your database; this allows EF to automatically apply only the new migration.</span></span>

### <a name="next-steps"></a><span data-ttu-id="955ec-148">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="955ec-148">Next steps</span></span>

<span data-ttu-id="955ec-149">Die Ausführungen oben waren nur eine kurze Einführung in Migrationen.</span><span class="sxs-lookup"><span data-stu-id="955ec-149">The above was only a brief introduction to migrations.</span></span> <span data-ttu-id="955ec-150">Weitere Informationen zum [Verwalten von Migrationen](xref:core/managing-schemas/migrations/managing), [Anwenden von Migrationen](xref:core/managing-schemas/migrations/applying) und zu weiteren Aspekte finden Sie auf den anderen Dokumentationsseiten.</span><span class="sxs-lookup"><span data-stu-id="955ec-150">Please consult the other documentation pages to learn more about [managing migrations](xref:core/managing-schemas/migrations/managing), [applying them](xref:core/managing-schemas/migrations/applying), and other aspects.</span></span> <span data-ttu-id="955ec-151">Die [.NET Core CLI-Toolreferenz](xref:core/miscellaneous/cli/index) enthält ebenfalls nützliche Informationen zu den verschiedenen Befehlen.</span><span class="sxs-lookup"><span data-stu-id="955ec-151">The [.NET Core CLI tool reference](xref:core/miscellaneous/cli/index) also contains useful information on the different commands</span></span>
