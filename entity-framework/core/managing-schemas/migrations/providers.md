---
title: Migrationen mit mehreren Anbietern-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: 388829992687e626648889a47130cc61df1c0d1f
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370617"
---
# <a name="migrations-with-multiple-providers"></a><span data-ttu-id="fc931-102">Migrationen mit mehreren Anbietern</span><span class="sxs-lookup"><span data-stu-id="fc931-102">Migrations with Multiple Providers</span></span>

<span data-ttu-id="fc931-103">Mit den [EF Core Tools][1] werden nur die Migrationen für den aktiven Anbieter Gerüstbau.</span><span class="sxs-lookup"><span data-stu-id="fc931-103">The [EF Core Tools][1] only scaffold migrations for the active provider.</span></span> <span data-ttu-id="fc931-104">Manchmal möchten Sie jedoch möglicherweise mehr als einen Anbieter (z. b. Microsoft SQL Server und SQLite) mit dbcontext verwenden.</span><span class="sxs-lookup"><span data-stu-id="fc931-104">Sometimes, however, you may want to use more than one provider (for example Microsoft SQL Server and SQLite) with your DbContext.</span></span> <span data-ttu-id="fc931-105">Es gibt zwei Möglichkeiten, dies mit Migrationen zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="fc931-105">There are two ways to handle this with Migrations.</span></span> <span data-ttu-id="fc931-106">Sie können zwei Sätze von Migrationen verwalten: eine für jeden Anbieter, oder Sie können Sie in einem einzelnen Satz zusammenführen, der für beides geeignet ist.</span><span class="sxs-lookup"><span data-stu-id="fc931-106">You can maintain two sets of migrations--one for each provider--or merge them into a single set that can work on both.</span></span>

## <a name="two-migration-sets"></a><span data-ttu-id="fc931-107">Zwei Migrations Sätze</span><span class="sxs-lookup"><span data-stu-id="fc931-107">Two migration sets</span></span>

<span data-ttu-id="fc931-108">Beim ersten Ansatz generieren Sie zwei Migrationen für jede Modell Änderung.</span><span class="sxs-lookup"><span data-stu-id="fc931-108">In the first approach, you generate two migrations for each model change.</span></span>

<span data-ttu-id="fc931-109">Eine Möglichkeit besteht darin, jeden Migrations Satz [in einer separaten Assembly][2] zu platzieren und den aktiven Anbieter (und die Migrationsassembly) manuell zwischen dem Hinzufügen der beiden Migrationen zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="fc931-109">One way to do this is to put each migration set [in a separate assembly][2] and manually switch the active provider (and migrations assembly) between adding the two migrations.</span></span>

<span data-ttu-id="fc931-110">Ein weiterer Ansatz, der die Arbeit mit den Tools vereinfacht, besteht darin, einen neuen Typ zu erstellen, der von dbcontext abgeleitet ist und den aktiven Anbieter überschreibt.</span><span class="sxs-lookup"><span data-stu-id="fc931-110">Another approach that makes working with the tools easier is to create a new type that derives from your DbContext and overrides the active provider.</span></span> <span data-ttu-id="fc931-111">Dieser Typ wird zur Entwurfszeit beim Hinzufügen oder Anwenden von Migrationen verwendet.</span><span class="sxs-lookup"><span data-stu-id="fc931-111">This type is used at design time when adding or applying migrations.</span></span>

``` csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> <span data-ttu-id="fc931-112">Da für jeden Migrations Satz seine eigenen dbcontext-Typen verwendet werden, ist für diese Vorgehensweise die Verwendung einer separaten Migrationsassembly nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="fc931-112">Since each migration set uses its own DbContext types, this approach doesn't require using a separate migrations assembly.</span></span>

<span data-ttu-id="fc931-113">Beim Hinzufügen einer neuen Migration geben Sie die Kontext Typen an.</span><span class="sxs-lookup"><span data-stu-id="fc931-113">When adding new migration, specify the context types.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="fc931-114">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="fc931-114">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[<span data-ttu-id="fc931-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fc931-115">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> <span data-ttu-id="fc931-116">Sie müssen das Ausgabeverzeichnis für nachfolgende Migrationen nicht angeben, da diese als gleich geordnete Elemente der letzten erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="fc931-116">You don't need to specify the output directory for subsequent migrations since they are created as siblings to the last one.</span></span>

## <a name="one-migration-set"></a><span data-ttu-id="fc931-117">Ein Migrations Satz</span><span class="sxs-lookup"><span data-stu-id="fc931-117">One migration set</span></span>

<span data-ttu-id="fc931-118">Wenn Sie nicht möchten, dass zwei Migrations Sätze vorhanden sind, können Sie diese manuell in einem einzelnen Satz kombinieren, der auf beide Anbieter angewendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="fc931-118">If you don't like having two sets of migrations, you can manually combine them into a single set that can be applied to both providers.</span></span>

<span data-ttu-id="fc931-119">Anmerkungen können gleichzeitig vorhanden sein, da ein Anbieter alle Anmerkungen ignoriert, die er nicht versteht.</span><span class="sxs-lookup"><span data-stu-id="fc931-119">Annotations can coexist since a provider ignores any annotations that it doesn't understand.</span></span> <span data-ttu-id="fc931-120">Beispielsweise könnte eine Primärschlüssel Spalte, die sowohl mit Microsoft SQL Server als auch mit SQLite funktioniert, wie folgt aussehen.</span><span class="sxs-lookup"><span data-stu-id="fc931-120">For example, a primary key column that works with both Microsoft SQL Server and SQLite might look like this.</span></span>

``` csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

<span data-ttu-id="fc931-121">Verwenden Sie die- `ActiveProvider` Eigenschaft, um zu bestimmen, welcher Anbieter aktiv ist, wenn Vorgänge nur für einen Anbieter angewendet werden können oder sich zwischen den Anbietern unterscheiden:</span><span class="sxs-lookup"><span data-stu-id="fc931-121">If operations can be applied only for one provider, or they're different between providers, use the `ActiveProvider` property to determine which provider is active:</span></span>

``` csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: ../../miscellaneous/cli/index.md
  [2]: projects.md
