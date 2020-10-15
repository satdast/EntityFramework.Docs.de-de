---
title: Tabelle "benutzerdefinierte Migrations Verlauf"-EF Core
description: Anpassen einer Verlaufs Tabelle zur Verwendung für Migrationen mit Entity Framework Core
author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/history-table
ms.openlocfilehash: 1f6dcb2fbb20e7dafed17160832ccec453839aa3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062307"
---
# <a name="custom-migrations-history-table"></a><span data-ttu-id="5b878-103">Benutzerdefinierte Migrations Verlaufs Tabelle</span><span class="sxs-lookup"><span data-stu-id="5b878-103">Custom Migrations History Table</span></span>

<span data-ttu-id="5b878-104">Standardmäßig wird von EF Core nachverfolgt, welche Migrationen auf die Datenbank angewendet wurden, indem Sie in einer Tabelle mit dem Namen aufgezeichnet werden `__EFMigrationsHistory` .</span><span class="sxs-lookup"><span data-stu-id="5b878-104">By default, EF Core keeps track of which migrations have been applied to the database by recording them in a table named `__EFMigrationsHistory`.</span></span> <span data-ttu-id="5b878-105">Aus verschiedenen Gründen kann es sinnvoll sein, diese Tabelle anzupassen, damit Sie Ihren Anforderungen besser entspricht.</span><span class="sxs-lookup"><span data-stu-id="5b878-105">For various reasons, you may want to customize this table to better suit your needs.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5b878-106">Wenn Sie die Migrations Verlaufs Tabelle *nach* dem Anwenden von Migrationen anpassen, sind Sie dafür verantwortlich, die vorhandene Tabelle in der Datenbank zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="5b878-106">If you customize the Migrations history table *after* applying migrations, you are responsible for updating the existing table in the database.</span></span>

## <a name="schema-and-table-name"></a><span data-ttu-id="5b878-107">Schema-und Tabellenname</span><span class="sxs-lookup"><span data-stu-id="5b878-107">Schema and table name</span></span>

<span data-ttu-id="5b878-108">Sie können das Schema und den Tabellennamen mithilfe der- `MigrationsHistoryTable()` Methode in `OnConfiguring()` (oder `ConfigureServices()` auf ASP.net Core) ändern.</span><span class="sxs-lookup"><span data-stu-id="5b878-108">You can change the schema and table name using the `MigrationsHistoryTable()` method in `OnConfiguring()` (or `ConfigureServices()` on ASP.NET Core).</span></span> <span data-ttu-id="5b878-109">Im folgenden finden Sie ein Beispiel für die Verwendung des SQL Server EF Core Anbieters.</span><span class="sxs-lookup"><span data-stu-id="5b878-109">Here is an example using the SQL Server EF Core provider.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a><span data-ttu-id="5b878-110">Weitere Änderungen</span><span class="sxs-lookup"><span data-stu-id="5b878-110">Other changes</span></span>

<span data-ttu-id="5b878-111">Um zusätzliche Aspekte der Tabelle zu konfigurieren, überschreiben Sie den anbieterspezifischen Dienst, und ersetzen Sie ihn `IHistoryRepository` .</span><span class="sxs-lookup"><span data-stu-id="5b878-111">To configure additional aspects of the table, override and replace the provider-specific `IHistoryRepository` service.</span></span> <span data-ttu-id="5b878-112">Im folgenden finden Sie ein Beispiel für das Ändern des Namens der migrationid-Spalte in " *ID* " in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="5b878-112">Here is an example of changing the MigrationId column name to *Id* on SQL Server.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> <span data-ttu-id="5b878-113">`SqlServerHistoryRepository` befindet sich in einem internen Namespace und kann sich in zukünftigen Versionen ändern.</span><span class="sxs-lookup"><span data-stu-id="5b878-113">`SqlServerHistoryRepository` is inside an internal namespace and may change in future releases.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
