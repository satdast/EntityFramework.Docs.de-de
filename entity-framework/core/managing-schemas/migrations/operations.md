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
# <a name="custom-migrations-operations"></a><span data-ttu-id="8a8e1-103">Benutzerdefinierte Migrations Vorgänge</span><span class="sxs-lookup"><span data-stu-id="8a8e1-103">Custom Migrations Operations</span></span>

<span data-ttu-id="8a8e1-104">Die migrationbuilder-API ermöglicht es Ihnen, viele verschiedene Arten von Vorgängen während einer Migration auszuführen, aber es ist weit von der Vollständigkeit her.</span><span class="sxs-lookup"><span data-stu-id="8a8e1-104">The MigrationBuilder API allows you to perform many different kinds of operations during a migration, but it's far from exhaustive.</span></span> <span data-ttu-id="8a8e1-105">Die API ist jedoch auch erweiterbar, sodass Sie eigene Vorgänge definieren können.</span><span class="sxs-lookup"><span data-stu-id="8a8e1-105">However, the API is also extensible allowing you to define your own operations.</span></span> <span data-ttu-id="8a8e1-106">Es gibt zwei Möglichkeiten, die API zu erweitern: mithilfe der- `Sql()` Methode oder durch Definieren von benutzerdefinierten `MigrationOperation` Objekten.</span><span class="sxs-lookup"><span data-stu-id="8a8e1-106">There are two ways to extend the API: Using the `Sql()` method, or by defining custom `MigrationOperation` objects.</span></span>

<span data-ttu-id="8a8e1-107">Um dies zu veranschaulichen, betrachten wir die Implementierung eines Vorgangs, der einen Datenbankbenutzer mit jedem Ansatz erstellt.</span><span class="sxs-lookup"><span data-stu-id="8a8e1-107">To illustrate, let's look at implementing an operation that creates a database user using each approach.</span></span> <span data-ttu-id="8a8e1-108">In unseren Migrationen möchten wir das Schreiben des folgenden Codes aktivieren:</span><span class="sxs-lookup"><span data-stu-id="8a8e1-108">In our migrations, we want to enable writing the following code:</span></span>

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a><span data-ttu-id="8a8e1-109">Verwenden von migrationbuilder. SQL ()</span><span class="sxs-lookup"><span data-stu-id="8a8e1-109">Using MigrationBuilder.Sql()</span></span>

<span data-ttu-id="8a8e1-110">Die einfachste Möglichkeit zum Implementieren eines benutzerdefinierten Vorgangs besteht darin, eine Erweiterungsmethode zu definieren, die aufruft `MigrationBuilder.Sql()` .</span><span class="sxs-lookup"><span data-stu-id="8a8e1-110">The easiest way to implement a custom operation is to define an extension method that calls `MigrationBuilder.Sql()`.</span></span> <span data-ttu-id="8a8e1-111">Es folgt ein Beispiel, das die entsprechende Transact-SQL-Anwendung generiert.</span><span class="sxs-lookup"><span data-stu-id="8a8e1-111">Here is an example that generates the appropriate Transact-SQL.</span></span>

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

<span data-ttu-id="8a8e1-112">Wenn Ihre Migrationen mehrere Datenbankanbieter unterstützen müssen, können Sie die- `MigrationBuilder.ActiveProvider` Eigenschaft verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a8e1-112">If your migrations need to support multiple database providers, you can use the `MigrationBuilder.ActiveProvider` property.</span></span> <span data-ttu-id="8a8e1-113">Hier sehen Sie ein Beispiel, das sowohl Microsoft SQL Server als auch PostgreSQL unterstützt.</span><span class="sxs-lookup"><span data-stu-id="8a8e1-113">Here's an example supporting both Microsoft SQL Server and PostgreSQL.</span></span>

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

<span data-ttu-id="8a8e1-114">Dieser Ansatz funktioniert nur, wenn Sie jeden Anbieter kennen, auf den der benutzerdefinierte Vorgang angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="8a8e1-114">This approach only works if you know every provider where your custom operation will be applied.</span></span>

## <a name="using-a-migrationoperation"></a><span data-ttu-id="8a8e1-115">Verwenden von migrationoperation</span><span class="sxs-lookup"><span data-stu-id="8a8e1-115">Using a MigrationOperation</span></span>

<span data-ttu-id="8a8e1-116">Um den benutzerdefinierten Vorgang von SQL zu entkoppeln, können Sie einen eigenen definieren, `MigrationOperation` um ihn zu repräsentieren.</span><span class="sxs-lookup"><span data-stu-id="8a8e1-116">To decouple the custom operation from the SQL, you can define your own `MigrationOperation` to represent it.</span></span> <span data-ttu-id="8a8e1-117">Der Vorgang wird dann an den Anbieter weitergegeben, damit er die zu generierende SQL-Datei bestimmen kann.</span><span class="sxs-lookup"><span data-stu-id="8a8e1-117">The operation is then passed to the provider so it can determine the appropriate SQL to generate.</span></span>

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

<span data-ttu-id="8a8e1-118">Bei diesem Ansatz muss die Erweiterungsmethode nur einen dieser Vorgänge hinzufügen `MigrationBuilder.Operations` .</span><span class="sxs-lookup"><span data-stu-id="8a8e1-118">With this approach, the extension method just needs to add one of these operations to `MigrationBuilder.Operations`.</span></span>

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

<span data-ttu-id="8a8e1-119">Diese Vorgehensweise erfordert, dass jeder Anbieter weiß, wie SQL für diesen Vorgang in seinem Dienst generiert wird `IMigrationsSqlGenerator` .</span><span class="sxs-lookup"><span data-stu-id="8a8e1-119">This approach requires each provider to know how to generate SQL for this operation in their `IMigrationsSqlGenerator` service.</span></span> <span data-ttu-id="8a8e1-120">Hier ist ein Beispiel, das den Generator der SQL Server überschreibt, um den neuen Vorgang zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="8a8e1-120">Here is an example overriding the SQL Server's generator to handle the new operation.</span></span>

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

<span data-ttu-id="8a8e1-121">Ersetzen Sie den SQL-Generator-Standard Migrationsdienst durch den aktualisierten Dienst.</span><span class="sxs-lookup"><span data-stu-id="8a8e1-121">Replace the default migrations sql generator service with the updated one.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```
