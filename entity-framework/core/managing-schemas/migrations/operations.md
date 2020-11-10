---
title: Benutzerdefinierte Migrations Vorgänge-EF Core
description: Verwalten von benutzerdefinierten und unformatierten SQL-Migrationen für die Datenbankschema Entity Framework Core Verwaltung
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 2abde4d5eac977a746863dcfd77bc85a34e2166c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429831"
---
# <a name="custom-migrations-operations"></a><span data-ttu-id="6dcc5-103">Benutzerdefinierte Migrations Vorgänge</span><span class="sxs-lookup"><span data-stu-id="6dcc5-103">Custom Migrations Operations</span></span>

<span data-ttu-id="6dcc5-104">Die migrationbuilder-API ermöglicht es Ihnen, viele verschiedene Arten von Vorgängen während einer Migration auszuführen, aber es ist weit von der Vollständigkeit her.</span><span class="sxs-lookup"><span data-stu-id="6dcc5-104">The MigrationBuilder API allows you to perform many different kinds of operations during a migration, but it's far from exhaustive.</span></span> <span data-ttu-id="6dcc5-105">Die API ist jedoch auch erweiterbar, sodass Sie eigene Vorgänge definieren können.</span><span class="sxs-lookup"><span data-stu-id="6dcc5-105">However, the API is also extensible allowing you to define your own operations.</span></span> <span data-ttu-id="6dcc5-106">Es gibt zwei Möglichkeiten, die API zu erweitern: mithilfe der- `Sql()` Methode oder durch Definieren von benutzerdefinierten `MigrationOperation` Objekten.</span><span class="sxs-lookup"><span data-stu-id="6dcc5-106">There are two ways to extend the API: Using the `Sql()` method, or by defining custom `MigrationOperation` objects.</span></span>

<span data-ttu-id="6dcc5-107">Um dies zu veranschaulichen, betrachten wir die Implementierung eines Vorgangs, der einen Datenbankbenutzer mit jedem Ansatz erstellt.</span><span class="sxs-lookup"><span data-stu-id="6dcc5-107">To illustrate, let's look at implementing an operation that creates a database user using each approach.</span></span> <span data-ttu-id="6dcc5-108">In unseren Migrationen möchten wir das Schreiben des folgenden Codes aktivieren:</span><span class="sxs-lookup"><span data-stu-id="6dcc5-108">In our migrations, we want to enable writing the following code:</span></span>

```csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a><span data-ttu-id="6dcc5-109">Verwenden von migrationbuilder. SQL ()</span><span class="sxs-lookup"><span data-stu-id="6dcc5-109">Using MigrationBuilder.Sql()</span></span>

<span data-ttu-id="6dcc5-110">Die einfachste Möglichkeit zum Implementieren eines benutzerdefinierten Vorgangs besteht darin, eine Erweiterungsmethode zu definieren, die aufruft `MigrationBuilder.Sql()` .</span><span class="sxs-lookup"><span data-stu-id="6dcc5-110">The easiest way to implement a custom operation is to define an extension method that calls `MigrationBuilder.Sql()`.</span></span> <span data-ttu-id="6dcc5-111">Es folgt ein Beispiel, das die entsprechende Transact-SQL-Anwendung generiert.</span><span class="sxs-lookup"><span data-stu-id="6dcc5-111">Here is an example that generates the appropriate Transact-SQL.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationSql.cs#snippet_CustomOperationSql)]

> [!TIP]
> <span data-ttu-id="6dcc5-112">Verwenden Sie die- `EXEC` Funktion, wenn eine Anweisung der erste oder einzige in einem SQL-Batch sein muss.</span><span class="sxs-lookup"><span data-stu-id="6dcc5-112">Use the `EXEC` function when a statement must be the first or only one in a SQL batch.</span></span> <span data-ttu-id="6dcc5-113">Es kann auch erforderlich sein, Parserfehler in idempotenten Migrations Skripts zu umgehen, die auftreten können, wenn Spalten, auf die verwiesen wird, derzeit nicht in einer Tabelle vorhanden sind</span><span class="sxs-lookup"><span data-stu-id="6dcc5-113">It might also be needed to work around parser errors in idempotent migration scripts that can occur when referenced columns don't currently exist on a table.</span></span>

<span data-ttu-id="6dcc5-114">Wenn Ihre Migrationen mehrere Datenbankanbieter unterstützen müssen, können Sie die- `MigrationBuilder.ActiveProvider` Eigenschaft verwenden.</span><span class="sxs-lookup"><span data-stu-id="6dcc5-114">If your migrations need to support multiple database providers, you can use the `MigrationBuilder.ActiveProvider` property.</span></span> <span data-ttu-id="6dcc5-115">Hier sehen Sie ein Beispiel, das sowohl Microsoft SQL Server als auch PostgreSQL unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6dcc5-115">Here's an example supporting both Microsoft SQL Server and PostgreSQL.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationMultiSql.cs#snippet_CustomOperationMultiSql)]

<span data-ttu-id="6dcc5-116">Dieser Ansatz funktioniert nur, wenn Sie jeden Anbieter kennen, auf den der benutzerdefinierte Vorgang angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="6dcc5-116">This approach only works if you know every provider where your custom operation will be applied.</span></span>

## <a name="using-a-migrationoperation"></a><span data-ttu-id="6dcc5-117">Verwenden von migrationoperation</span><span class="sxs-lookup"><span data-stu-id="6dcc5-117">Using a MigrationOperation</span></span>

<span data-ttu-id="6dcc5-118">Um den benutzerdefinierten Vorgang von SQL zu entkoppeln, können Sie einen eigenen definieren, `MigrationOperation` um ihn zu repräsentieren.</span><span class="sxs-lookup"><span data-stu-id="6dcc5-118">To decouple the custom operation from the SQL, you can define your own `MigrationOperation` to represent it.</span></span> <span data-ttu-id="6dcc5-119">Der Vorgang wird dann an den Anbieter weitergegeben, damit er die zu generierende SQL-Datei bestimmen kann.</span><span class="sxs-lookup"><span data-stu-id="6dcc5-119">The operation is then passed to the provider so it can determine the appropriate SQL to generate.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_CreateUserOperation)]

<span data-ttu-id="6dcc5-120">Bei diesem Ansatz muss die Erweiterungsmethode nur einen dieser Vorgänge hinzufügen `MigrationBuilder.Operations` .</span><span class="sxs-lookup"><span data-stu-id="6dcc5-120">With this approach, the extension method just needs to add one of these operations to `MigrationBuilder.Operations`.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationBuilderExtension)]

<span data-ttu-id="6dcc5-121">Diese Vorgehensweise erfordert, dass jeder Anbieter weiß, wie SQL für diesen Vorgang in seinem Dienst generiert wird `IMigrationsSqlGenerator` .</span><span class="sxs-lookup"><span data-stu-id="6dcc5-121">This approach requires each provider to know how to generate SQL for this operation in their `IMigrationsSqlGenerator` service.</span></span> <span data-ttu-id="6dcc5-122">Hier ist ein Beispiel, das den Generator der SQL Server überschreibt, um den neuen Vorgang zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="6dcc5-122">Here is an example overriding the SQL Server's generator to handle the new operation.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationsSqlGenerator)]

<span data-ttu-id="6dcc5-123">Ersetzen Sie den SQL-Generator-Standard Migrationsdienst durch den aktualisierten Dienst.</span><span class="sxs-lookup"><span data-stu-id="6dcc5-123">Replace the default migrations sql generator service with the updated one.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_OnConfiguring)]
