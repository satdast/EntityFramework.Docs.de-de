---
title: Verwenden eines separaten Migrations Projekts-EF Core
description: Verwenden eines separaten Migrationsprojekts zum Verwalten von Datenbankschemas mit Entity Framework Core
author: bricelam
ms.date: 11/06/2020
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 2d1c093e0bb307584e2bf19cb93deec98aa10692
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429805"
---
# <a name="using-a-separate-migrations-project"></a><span data-ttu-id="78ba4-103">Verwenden eines separaten Migrations Projekts</span><span class="sxs-lookup"><span data-stu-id="78ba4-103">Using a Separate Migrations Project</span></span>

<span data-ttu-id="78ba4-104">Möglicherweise möchten Sie die Migrationen in einem anderen Projekt als dem mit Ihrem speichern `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="78ba4-104">You may want to store your migrations in a different project than the one containing your `DbContext`.</span></span> <span data-ttu-id="78ba4-105">Sie können diese Strategie auch verwenden, um mehrere Sätze von Migrationen beizubehalten, z. b. eine für die Entwicklung und eine andere für releaseaktualisierungen.</span><span class="sxs-lookup"><span data-stu-id="78ba4-105">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

> [!TIP]
> <span data-ttu-id="78ba4-106">Das in diesem Artikel verwendete [Beispiel finden Sie auf GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/ThreeProjectMigrations).</span><span class="sxs-lookup"><span data-stu-id="78ba4-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/ThreeProjectMigrations).</span></span>

## <a name="steps"></a><span data-ttu-id="78ba4-107">Schritte</span><span class="sxs-lookup"><span data-stu-id="78ba4-107">Steps</span></span>

1. <span data-ttu-id="78ba4-108">Erstellen Sie eine neue Klassenbibliothek.</span><span class="sxs-lookup"><span data-stu-id="78ba4-108">Create a new class library.</span></span>

2. <span data-ttu-id="78ba4-109">Fügen Sie einen Verweis auf das dbcontext-Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ba4-109">Add a reference to your DbContext project.</span></span>

3. <span data-ttu-id="78ba4-110">Verschieben Sie die Migrationen und die Modell Momentaufnahme-Dateien in die Klassenbibliothek.</span><span class="sxs-lookup"><span data-stu-id="78ba4-110">Move the migrations and model snapshot files to the class library.</span></span>
   > [!TIP]
   > <span data-ttu-id="78ba4-111">Wenn keine Migrationen vorhanden sind, generieren Sie eine im Projekt, das den dbcontext enthält, und verschieben Sie Sie dann.</span><span class="sxs-lookup"><span data-stu-id="78ba4-111">If you have no existing migrations, generate one in the project containing the DbContext then move it.</span></span>
   > <span data-ttu-id="78ba4-112">Dies ist wichtig, denn wenn das Migrationsprojekt keine vorhandene Migration enthält, kann der Add-Migration Befehl dbcontext nicht finden.</span><span class="sxs-lookup"><span data-stu-id="78ba4-112">This is important because if the migrations project does not contain an existing migration, the Add-Migration command will be unable to find the DbContext.</span></span>

4. <span data-ttu-id="78ba4-113">Konfigurieren Sie die Migrations Assembly:</span><span class="sxs-lookup"><span data-stu-id="78ba4-113">Configure the migrations assembly:</span></span>

   [!code-csharp[](../../../../samples/core/Schemas/ThreeProjectMigrations/WebApplication1/Startup.cs#snippet_MigrationsAssembly)]

5. <span data-ttu-id="78ba4-114">Fügen Sie dem **Start** Projekt einen Verweis auf das Migrationsprojekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="78ba4-114">Add a reference to your migrations project from the **startup** project.</span></span>

   ```xml
   <ItemGroup>
     <ProjectReference Include="..\WebApplication1.Migrations\WebApplication1.Migrations.csproj">
   </ItemGroup>
   ```

   <span data-ttu-id="78ba4-115">Wenn dadurch eine zirkuläre Abhängigkeit verursacht wird, können Sie stattdessen den basisausgabe Pfad des **Migrations** Projekts aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="78ba4-115">If this causes a circular dependency, you can update the base output path of the **migrations** project instead:</span></span>

   ```xml
   <PropertyGroup>
     <BaseOutputPath>..\WebApplication1\bin\</BaseOutputPath>
   </PropertyGroup>
   ```

<span data-ttu-id="78ba4-116">Wenn Sie alles ordnungsgemäß durchgeführt haben, sollten Sie dem Projekt neue Migrationen hinzufügen können.</span><span class="sxs-lookup"><span data-stu-id="78ba4-116">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

## <a name="net-core-cli"></a>[<span data-ttu-id="78ba4-117">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="78ba4-117">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project WebApplication1.Migrations
```

## <a name="visual-studio"></a>[<span data-ttu-id="78ba4-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78ba4-118">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration NewMigration -Project WebApplication1.Migrations
```

***
