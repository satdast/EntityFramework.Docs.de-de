---
title: Migrationen mit mehreren Anbietern-EF Core
description: Verwenden von Migrationen zum Verwalten von Datenbankschemas beim Festlegen mehrerer Datenbankanbieter mit Entity Framework Core
author: bricelam
ms.date: 11/08/2017
uid: core/managing-schemas/migrations/providers
ms.openlocfilehash: f44abb5156ea3a175c68c1a0ec23ff41a9d13452
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061982"
---
# <a name="migrations-with-multiple-providers"></a>Migrationen mit mehreren Anbietern

Mit den [EF Core Tools][1] werden nur die Migrationen für den aktiven Anbieter Gerüstbau. Manchmal möchten Sie jedoch möglicherweise mehr als einen Anbieter (z. b. Microsoft SQL Server und SQLite) mit dbcontext verwenden. Es gibt zwei Möglichkeiten, dies mit Migrationen zu behandeln. Sie können zwei Sätze von Migrationen verwalten: eine für jeden Anbieter, oder Sie können Sie in einem einzelnen Satz zusammenführen, der für beides geeignet ist.

## <a name="two-migration-sets"></a>Zwei Migrations Sätze

Beim ersten Ansatz generieren Sie zwei Migrationen für jede Modell Änderung.

Eine Möglichkeit besteht darin, jeden Migrations Satz [in einer separaten Assembly][2] zu platzieren und den aktiven Anbieter (und die Migrationsassembly) manuell zwischen dem Hinzufügen der beiden Migrationen zu wechseln.

Ein weiterer Ansatz, der die Arbeit mit den Tools vereinfacht, besteht darin, einen neuen Typ zu erstellen, der von dbcontext abgeleitet ist und den aktiven Anbieter überschreibt. Dieser Typ wird zur Entwurfszeit beim Hinzufügen oder Anwenden von Migrationen verwendet.

```csharp
class MySqliteDbContext : MyDbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

> [!NOTE]
> Da für jeden Migrations Satz seine eigenen dbcontext-Typen verwendet werden, ist für diese Vorgehensweise die Verwendung einer separaten Migrationsassembly nicht erforderlich.

Beim Hinzufügen einer neuen Migration geben Sie die Kontext Typen an.

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context MyDbContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context MySqliteDbContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration InitialCreate -Context MyDbContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context MySqliteDbContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> Sie müssen das Ausgabeverzeichnis für nachfolgende Migrationen nicht angeben, da diese als gleich geordnete Elemente der letzten erstellt werden.

## <a name="one-migration-set"></a>Ein Migrations Satz

Wenn Sie nicht möchten, dass zwei Migrations Sätze vorhanden sind, können Sie diese manuell in einem einzelnen Satz kombinieren, der auf beide Anbieter angewendet werden kann.

Anmerkungen können gleichzeitig vorhanden sein, da ein Anbieter alle Anmerkungen ignoriert, die er nicht versteht. Beispielsweise könnte eine Primärschlüssel Spalte, die sowohl mit Microsoft SQL Server als auch mit SQLite funktioniert, wie folgt aussehen.

```csharp
Id = table.Column<int>(nullable: false)
    .Annotation("SqlServer:ValueGenerationStrategy",
        SqlServerValueGenerationStrategy.IdentityColumn)
    .Annotation("Sqlite:Autoincrement", true),
```

Verwenden Sie die- `ActiveProvider` Eigenschaft, um zu bestimmen, welcher Anbieter aktiv ist, wenn Vorgänge nur für einen Anbieter angewendet werden können oder sich zwischen den Anbietern unterscheiden:

```csharp
if (migrationBuilder.ActiveProvider == "Microsoft.EntityFrameworkCore.SqlServer")
{
    migrationBuilder.CreateSequence(
        name: "EntityFrameworkHiLoSequence");
}
```

  [1]: xref:core/miscellaneous/cli/index
  [2]: xref:core/managing-schemas/migrations/projects
