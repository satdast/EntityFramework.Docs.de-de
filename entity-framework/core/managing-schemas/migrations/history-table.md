---
title: Tabelle "benutzerdefinierte Migrations Verlauf"-EF Core
description: Anpassen einer Verlaufs Tabelle zur Verwendung für Migrationen mit Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/history-table
ms.openlocfilehash: deed86b2023557036eb14ca5a66f04ab5bb4139f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617972"
---
# <a name="custom-migrations-history-table"></a><span data-ttu-id="49021-103">Benutzerdefinierte Migrations Verlaufs Tabelle</span><span class="sxs-lookup"><span data-stu-id="49021-103">Custom Migrations History Table</span></span>

<span data-ttu-id="49021-104">Standardmäßig wird von EF Core nachverfolgt, welche Migrationen auf die Datenbank angewendet wurden, indem Sie in einer Tabelle mit dem Namen aufgezeichnet werden `__EFMigrationsHistory` .</span><span class="sxs-lookup"><span data-stu-id="49021-104">By default, EF Core keeps track of which migrations have been applied to the database by recording them in a table named `__EFMigrationsHistory`.</span></span> <span data-ttu-id="49021-105">Aus verschiedenen Gründen kann es sinnvoll sein, diese Tabelle anzupassen, damit Sie Ihren Anforderungen besser entspricht.</span><span class="sxs-lookup"><span data-stu-id="49021-105">For various reasons, you may want to customize this table to better suit your needs.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="49021-106">Wenn Sie die Migrations Verlaufs Tabelle *nach* dem Anwenden von Migrationen anpassen, sind Sie dafür verantwortlich, die vorhandene Tabelle in der Datenbank zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="49021-106">If you customize the Migrations history table *after* applying migrations, you are responsible for updating the existing table in the database.</span></span>

## <a name="schema-and-table-name"></a><span data-ttu-id="49021-107">Schema-und Tabellenname</span><span class="sxs-lookup"><span data-stu-id="49021-107">Schema and table name</span></span>

<span data-ttu-id="49021-108">Sie können das Schema und den Tabellennamen mithilfe der- `MigrationsHistoryTable()` Methode in `OnConfiguring()` (oder `ConfigureServices()` auf ASP.net Core) ändern.</span><span class="sxs-lookup"><span data-stu-id="49021-108">You can change the schema and table name using the `MigrationsHistoryTable()` method in `OnConfiguring()` (or `ConfigureServices()` on ASP.NET Core).</span></span> <span data-ttu-id="49021-109">Im folgenden finden Sie ein Beispiel für die Verwendung des SQL Server EF Core Anbieters.</span><span class="sxs-lookup"><span data-stu-id="49021-109">Here is an example using the SQL Server EF Core provider.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a><span data-ttu-id="49021-110">Weitere Änderungen</span><span class="sxs-lookup"><span data-stu-id="49021-110">Other changes</span></span>

<span data-ttu-id="49021-111">Um zusätzliche Aspekte der Tabelle zu konfigurieren, überschreiben Sie den anbieterspezifischen Dienst, und ersetzen Sie ihn `IHistoryRepository` .</span><span class="sxs-lookup"><span data-stu-id="49021-111">To configure additional aspects of the table, override and replace the provider-specific `IHistoryRepository` service.</span></span> <span data-ttu-id="49021-112">Im folgenden finden Sie ein Beispiel für das Ändern des Namens der migrationid-Spalte in " *ID* " in SQL Server.</span><span class="sxs-lookup"><span data-stu-id="49021-112">Here is an example of changing the MigrationId column name to *Id* on SQL Server.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> <span data-ttu-id="49021-113">`SqlServerHistoryRepository` befindet sich in einem internen Namespace und kann sich in zukünftigen Versionen ändern.</span><span class="sxs-lookup"><span data-stu-id="49021-113">`SqlServerHistoryRepository` is inside an internal namespace and may change in future releases.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
