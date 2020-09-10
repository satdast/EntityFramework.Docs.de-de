---
title: Benutzerdefinierte Migrations Vorgänge-EF Core
description: Verwalten von benutzerdefinierten und unformatierten SQL-Migrationen für die Datenbankschema Entity Framework Core Verwaltung
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 708894d8d567a4644be3a4ace98cc837465710e0
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617953"
---
# <a name="custom-migrations-operations"></a>Benutzerdefinierte Migrations Vorgänge

Die migrationbuilder-API ermöglicht es Ihnen, viele verschiedene Arten von Vorgängen während einer Migration auszuführen, aber es ist weit von der Vollständigkeit her. Die API ist jedoch auch erweiterbar, sodass Sie eigene Vorgänge definieren können. Es gibt zwei Möglichkeiten, die API zu erweitern: mithilfe der- `Sql()` Methode oder durch Definieren von benutzerdefinierten `MigrationOperation` Objekten.

Um dies zu veranschaulichen, betrachten wir die Implementierung eines Vorgangs, der einen Datenbankbenutzer mit jedem Ansatz erstellt. In unseren Migrationen möchten wir das Schreiben des folgenden Codes aktivieren:

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a>Verwenden von migrationbuilder. SQL ()

Die einfachste Möglichkeit zum Implementieren eines benutzerdefinierten Vorgangs besteht darin, eine Erweiterungsmethode zu definieren, die aufruft `MigrationBuilder.Sql()` . Es folgt ein Beispiel, das die entsprechende Transact-SQL-Anwendung generiert.

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

Wenn Ihre Migrationen mehrere Datenbankanbieter unterstützen müssen, können Sie die- `MigrationBuilder.ActiveProvider` Eigenschaft verwenden. Hier sehen Sie ein Beispiel, das sowohl Microsoft SQL Server als auch PostgreSQL unterstützt.

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
{
    switch (migrationBuilder.ActiveProvider)
    {
        case "Npgsql.EntityFrameworkCore.PostgreSQL":
            return migrationBuilder
                .Sql($"CREATE USER {name} WITH PASSWORD '{password}';");

        case "Microsoft.EntityFrameworkCore.SqlServer":
            return migrationBuilder
                .Sql($"CREATE USER {name} WITH PASSWORD = '{password}';");
    }

    return migrationBuilder;
}
```

Dieser Ansatz funktioniert nur, wenn Sie jeden Anbieter kennen, auf den der benutzerdefinierte Vorgang angewendet wird.

## <a name="using-a-migrationoperation"></a>Verwenden von migrationoperation

Um den benutzerdefinierten Vorgang von SQL zu entkoppeln, können Sie einen eigenen definieren, `MigrationOperation` um ihn zu repräsentieren. Der Vorgang wird dann an den Anbieter weitergegeben, damit er die zu generierende SQL-Datei bestimmen kann.

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

Bei diesem Ansatz muss die Erweiterungsmethode nur einen dieser Vorgänge hinzufügen `MigrationBuilder.Operations` .

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
{
    migrationBuilder.Operations.Add(
        new CreateUserOperation
        {
            Name = name,
            Password = password
        });

    return migrationBuilder;
}
```

Diese Vorgehensweise erfordert, dass jeder Anbieter weiß, wie SQL für diesen Vorgang in seinem Dienst generiert wird `IMigrationsSqlGenerator` . Hier ist ein Beispiel, das den Generator der SQL Server überschreibt, um den neuen Vorgang zu verarbeiten.

``` csharp
class MyMigrationsSqlGenerator : SqlServerMigrationsSqlGenerator
{
    public MyMigrationsSqlGenerator(
        MigrationsSqlGeneratorDependencies dependencies,
        IMigrationsAnnotationProvider migrationsAnnotations)
        : base(dependencies, migrationsAnnotations)
    {
    }

    protected override void Generate(
        MigrationOperation operation,
        IModel model,
        MigrationCommandListBuilder builder)
    {
        if (operation is CreateUserOperation createUserOperation)
        {
            Generate(createUserOperation, builder);
        }
        else
        {
            base.Generate(operation, model, builder);
        }
    }

    private void Generate(
        CreateUserOperation operation,
        MigrationCommandListBuilder builder)
    {
        var sqlHelper = Dependencies.SqlGenerationHelper;
        var stringMapping = Dependencies.TypeMappingSource.FindMapping(typeof(string));

        builder
            .Append("CREATE USER ")
            .Append(sqlHelper.DelimitIdentifier(operation.Name))
            .Append(" WITH PASSWORD = ")
            .Append(stringMapping.GenerateSqlLiteral(operation.Password))
            .AppendLine(sqlHelper.StatementTerminator)
            .EndCommand();
    }
}
```

Ersetzen Sie den SQL-Generator-Standard Migrationsdienst durch den aktualisierten Dienst.

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```
