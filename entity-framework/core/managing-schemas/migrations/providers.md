---
title: Migrationen mit mehreren Anbietern-EF Core
description: Verwenden von Migrationen zum Verwalten von Datenbankschemas beim Festlegen mehrerer Datenbankanbieter mit Entity Framework Core
author: bricelam
ms.date: 10/29/2020
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: fb8c6121a4baccf573e57b52ebeb3fcd29fe2cba
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429779"
---
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="97993-103">Migrationen mit mehreren Anbietern</span><span class="sxs-lookup"><span data-stu-id="97993-103">Migrations with Multiple Providers</span></span>

<span data-ttu-id="97993-104">Mit den [EF Core Tools](xref:core/cli/index) werden nur die Migrationen für den aktiven Anbieter Gerüstbau.</span><span class="sxs-lookup"><span data-stu-id="97993-104">The [EF Core Tools](xref:core/cli/index) only scaffold migrations for the active provider.</span></span> <span data-ttu-id="97993-105">Manchmal möchten Sie jedoch möglicherweise mehr als einen Anbieter (z. b. Microsoft SQL Server und SQLite) mit dbcontext verwenden.</span><span class="sxs-lookup"><span data-stu-id="97993-105">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="97993-106">Behandeln Sie dieses Vorgehen, indem Sie mehrere Sätze von Migrationen (eines für jeden Anbieter) beibehalten und eine Migration zu jeder Modell Änderung hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="97993-106">Handle this by maintaining multiple sets of migrations--one for each provider--and adding a migration to each for every model change.</span></span>

## <a name="using-multiple-context-types"></a><span data-ttu-id="97993-107">Verwenden mehrerer Kontext Typen</span><span class="sxs-lookup"><span data-stu-id="97993-107">Using multiple context types</span></span>

<span data-ttu-id="97993-108">Eine Möglichkeit zum Erstellen mehrerer Migrations Sätze besteht darin, einen dbcontext-Typ pro Anbieter zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="97993-108">One way to create multiple migration sets is to use one DbContext type per provider.</span></span>

```csharp
class SqliteBlogContext : BlogContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

<span data-ttu-id="97993-109">Geben Sie beim Hinzufügen neuer Migrationen den Kontexttyp an</span><span class="sxs-lookup"><span data-stu-id="97993-109">Specify the context type when adding new migrations.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="97993-110">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="97993-110">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context BlogContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context SqliteBlogContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[<span data-ttu-id="97993-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="97993-111">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -Context BlogContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context SqliteBlogContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> <span data-ttu-id="97993-112">Sie müssen das Ausgabeverzeichnis für nachfolgende Migrationen nicht angeben, da diese als gleich geordnete Elemente der letzten erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="97993-112">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="using-one-context-type"></a><span data-ttu-id="97993-113">Verwenden eines Kontext Typs</span><span class="sxs-lookup"><span data-stu-id="97993-113">Using one context type</span></span>

<span data-ttu-id="97993-114">Es ist auch möglich, einen dbcontext-Typ zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="97993-114">It's also possible to use one DbContext type.</span></span> <span data-ttu-id="97993-115">Dies erfordert derzeit, dass die Migrationen in eine separate Assembly verschoben werden.</span><span class="sxs-lookup"><span data-stu-id="97993-115">This currently requires moving the migrations into a separate assembly.</span></span> <span data-ttu-id="97993-116">Anweisungen zum Einrichten von Projekten finden Sie unter [Verwenden eines separaten Migrations Projekts](xref:core/managing-schemas/migrations/projects) .</span><span class="sxs-lookup"><span data-stu-id="97993-116">Please refer to [Using a Separate Migrations Project](xref:core/managing-schemas/migrations/projects) for instructions on setting up your projects.</span></span>

> [!TIP]
> <span data-ttu-id="97993-117">Das in diesem Artikel verwendete [Beispiel finden Sie auf GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/TwoProjectMigrations).</span><span class="sxs-lookup"><span data-stu-id="97993-117">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/TwoProjectMigrations).</span></span>

<span data-ttu-id="97993-118">Ab EF Core 5,0 können Sie Argumente aus den-Tools an die APP übergeben.</span><span class="sxs-lookup"><span data-stu-id="97993-118">Starting in EF Core 5.0, you can pass arguments into the app from the tools.</span></span> <span data-ttu-id="97993-119">Dies kann einen optimierten Workflow ermöglichen, der beim Ausführen der Tools keine manuellen Änderungen am Projekt vornehmen muss.</span><span class="sxs-lookup"><span data-stu-id="97993-119">This can enable a more streamlined workflow that avoids having to make manual changes to the project when running the tools.</span></span>

<span data-ttu-id="97993-120">Hier ist ein Muster, das bei der Verwendung eines [generischen Hosts](/dotnet/core/extensions/generic-host)gut funktioniert.</span><span class="sxs-lookup"><span data-stu-id="97993-120">Here's one pattern that works well when using a [Generic Host](/dotnet/core/extensions/generic-host).</span></span>

[!code-csharp[](../../../../samples/core/Schemas/TwoProjectMigrations/WorkerService1/Program.cs#snippet_CreateHostBuilder)]

<span data-ttu-id="97993-121">Da der Standard Host-Generator die Konfiguration über Befehlszeilenargumente liest, können Sie den Anbieter angeben, wenn Sie die Tools ausführen.</span><span class="sxs-lookup"><span data-stu-id="97993-121">Since the default host builder reads configuration from command-line arguments, you can specify the provider when running the tools.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="97993-122">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="97993-122">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add MyMigration --project ../SqlServerMigrations -- --provider SqlServer
dotnet ef migrations add MyMigration --project ../SqliteMigrations -- --provider Sqlite
```

> [!TIP]
> <span data-ttu-id="97993-123">Das `--` Token leitet `dotnet ef` , um alle nachfolgenden Elemente als Argument zu behandeln, und versucht nicht, Sie als Optionen zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="97993-123">The `--` token directs `dotnet ef` to treat everything that follows as an argument and not try to parse them as options.</span></span> <span data-ttu-id="97993-124">Alle zusätzlichen Argumente, die nicht von verwendet `dotnet ef` werden, werden an die APP weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="97993-124">Any extra arguments not used by `dotnet ef` are forwarded to the app.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="97993-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="97993-125">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration MyMigration -Args "--provider SqlServer"
Add-Migration MyMigration -Args "--provider Sqlite"
```

***

> [!NOTE]
> <span data-ttu-id="97993-126">Die Möglichkeit, zusätzliche Argumente für die APP anzugeben, wurde in EF Core 5,0 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="97993-126">The ability to specify additional arguments for the app was added in EF Core 5.0.</span></span> <span data-ttu-id="97993-127">Wenn Sie eine ältere Version verwenden, geben Sie stattdessen Konfigurationswerte mit Umgebungsvariablen an.</span><span class="sxs-lookup"><span data-stu-id="97993-127">If you're using an older version, specify configuration values with environment variables instead.</span></span>
