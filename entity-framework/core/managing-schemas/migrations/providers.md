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
# <a name="migrations-with-multiple-providers"></a>Migrationen mit mehreren Anbietern

Mit den [EF Core Tools](xref:core/cli/index) werden nur die Migrationen für den aktiven Anbieter Gerüstbau. Manchmal möchten Sie jedoch möglicherweise mehr als einen Anbieter (z. b. Microsoft SQL Server und SQLite) mit dbcontext verwenden. Behandeln Sie dieses Vorgehen, indem Sie mehrere Sätze von Migrationen (eines für jeden Anbieter) beibehalten und eine Migration zu jeder Modell Änderung hinzufügen.

## <a name="using-multiple-context-types"></a>Verwenden mehrerer Kontext Typen

Eine Möglichkeit zum Erstellen mehrerer Migrations Sätze besteht darin, einen dbcontext-Typ pro Anbieter zu verwenden.

```csharp
class SqliteBlogContext : BlogContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=my.db");
}
```

Geben Sie beim Hinzufügen neuer Migrationen den Kontexttyp an

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --context BlogContext --output-dir Migrations/SqlServerMigrations
dotnet ef migrations add InitialCreate --context SqliteBlogContext --output-dir Migrations/SqliteMigrations
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration InitialCreate -Context BlogContext -OutputDir Migrations\SqlServerMigrations
Add-Migration InitialCreate -Context SqliteBlogContext -OutputDir Migrations\SqliteMigrations
```

***

> [!TIP]
> Sie müssen das Ausgabeverzeichnis für nachfolgende Migrationen nicht angeben, da diese als gleich geordnete Elemente der letzten erstellt werden.

## <a name="using-one-context-type"></a>Verwenden eines Kontext Typs

Es ist auch möglich, einen dbcontext-Typ zu verwenden. Dies erfordert derzeit, dass die Migrationen in eine separate Assembly verschoben werden. Anweisungen zum Einrichten von Projekten finden Sie unter [Verwenden eines separaten Migrations Projekts](xref:core/managing-schemas/migrations/projects) .

> [!TIP]
> Das in diesem Artikel verwendete [Beispiel finden Sie auf GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/TwoProjectMigrations).

Ab EF Core 5,0 können Sie Argumente aus den-Tools an die APP übergeben. Dies kann einen optimierten Workflow ermöglichen, der beim Ausführen der Tools keine manuellen Änderungen am Projekt vornehmen muss.

Hier ist ein Muster, das bei der Verwendung eines [generischen Hosts](/dotnet/core/extensions/generic-host)gut funktioniert.

[!code-csharp[](../../../../samples/core/Schemas/TwoProjectMigrations/WorkerService1/Program.cs#snippet_CreateHostBuilder)]

Da der Standard Host-Generator die Konfiguration über Befehlszeilenargumente liest, können Sie den Anbieter angeben, wenn Sie die Tools ausführen.

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add MyMigration --project ../SqlServerMigrations -- --provider SqlServer
dotnet ef migrations add MyMigration --project ../SqliteMigrations -- --provider Sqlite
```

> [!TIP]
> Das `--` Token leitet `dotnet ef` , um alle nachfolgenden Elemente als Argument zu behandeln, und versucht nicht, Sie als Optionen zu analysieren. Alle zusätzlichen Argumente, die nicht von verwendet `dotnet ef` werden, werden an die APP weitergeleitet.

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration MyMigration -Args "--provider SqlServer"
Add-Migration MyMigration -Args "--provider Sqlite"
```

***

> [!NOTE]
> Die Möglichkeit, zusätzliche Argumente für die APP anzugeben, wurde in EF Core 5,0 hinzugefügt. Wenn Sie eine ältere Version verwenden, geben Sie stattdessen Konfigurationswerte mit Umgebungsvariablen an.
