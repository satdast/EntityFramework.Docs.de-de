---
title: Verwenden eines separaten Migrations Projekts-EF Core
description: Verwenden eines separaten Migrationsprojekts zum Verwalten von Datenbankschemas mit Entity Framework Core
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 6f28027c320f0d1c13d13bef7d8227b2bb68df91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062489"
---
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="3fa26-103">Verwenden eines separaten Migrations Projekts</span><span class="sxs-lookup"><span data-stu-id="3fa26-103">Using a Separate Migrations Project</span></span>

<span data-ttu-id="3fa26-104">Möglicherweise möchten Sie Ihre Migrationen in einer anderen Assembly als der mit Ihrem speichern `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="3fa26-104">You may want to store your migrations in a different assembly than the one containing your `DbContext`.</span></span> <span data-ttu-id="3fa26-105">Sie können diese Strategie auch verwenden, um mehrere Sätze von Migrationen beizubehalten, z. b. eine für die Entwicklung und eine andere für releaseaktualisierungen.</span><span class="sxs-lookup"><span data-stu-id="3fa26-105">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

<span data-ttu-id="3fa26-106">Aufgabe</span><span class="sxs-lookup"><span data-stu-id="3fa26-106">To do this...</span></span>

1. <span data-ttu-id="3fa26-107">Erstellen Sie eine neue Klassenbibliothek.</span><span class="sxs-lookup"><span data-stu-id="3fa26-107">Create a new class library.</span></span>

2. <span data-ttu-id="3fa26-108">Fügen Sie einen Verweis auf die dbcontext-Assembly hinzu.</span><span class="sxs-lookup"><span data-stu-id="3fa26-108">Add a reference to your DbContext assembly.</span></span>

3. <span data-ttu-id="3fa26-109">Verschieben Sie die Migrationen und die Modell Momentaufnahme-Dateien in die Klassenbibliothek.</span><span class="sxs-lookup"><span data-stu-id="3fa26-109">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="3fa26-110">Wenn keine Migrationen vorhanden sind, generieren Sie eine im Projekt, das den dbcontext enthält, und verschieben Sie Sie dann.</span><span class="sxs-lookup"><span data-stu-id="3fa26-110">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="3fa26-111">Dies ist wichtig, denn wenn die Migrationsassembly keine vorhandene Migration enthält, wird der Add-Migration-Befehl den dbcontext nicht finden.</span><span class="sxs-lookup"><span data-stu-id="3fa26-111">This is important because if the migrations assembly does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="3fa26-112">Konfigurieren Sie die Migrations Assembly:</span><span class="sxs-lookup"><span data-stu-id="3fa26-112">Configure the migrations assembly:</span></span>

   ```csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. <span data-ttu-id="3fa26-113">Fügen Sie einen Verweis auf ihre Migrationen-Assembly aus der Startassembly hinzu.</span><span class="sxs-lookup"><span data-stu-id="3fa26-113">Add a reference to your migrations assembly from the startup assembly.</span></span>
   * <span data-ttu-id="3fa26-114">Wenn dies eine zirkuläre Abhängigkeit verursacht, aktualisieren Sie den Ausgabepfad der Klassenbibliothek:</span><span class="sxs-lookup"><span data-stu-id="3fa26-114">If this causes a circular dependency, update the output path of the class library:</span></span>

     ```xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

<span data-ttu-id="3fa26-115">Wenn Sie alles ordnungsgemäß durchgeführt haben, sollten Sie dem Projekt neue Migrationen hinzufügen können.</span><span class="sxs-lookup"><span data-stu-id="3fa26-115">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

## <a name="net-core-cli"></a>[<span data-ttu-id="3fa26-116">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="3fa26-116">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project MyApp.Migrations
```

## <a name="visual-studio"></a>[<span data-ttu-id="3fa26-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3fa26-117">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

***
