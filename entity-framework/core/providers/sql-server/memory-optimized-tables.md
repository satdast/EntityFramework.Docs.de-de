---
title: Microsoft SQL Server Datenbankanbieter-Memory-Optimized Tabellen-EF Core
description: Verwenden von Memory-Optimized Tabellen mit dem SQL Server Entity Framework Core Datenbankanbieter
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/memory-optimized-tables
ms.openlocfilehash: 42b2a2ffafb2234e1ce7a6d0844234509a4b5b94
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063919"
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a><span data-ttu-id="7b9cb-103">Unterstützung von Memory-Optimized Tabellen in SQL Server EF Core Datenbankanbieter</span><span class="sxs-lookup"><span data-stu-id="7b9cb-103">Memory-Optimized Tables support in SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="7b9cb-104">[Speicher optimierte Tabellen](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) sind eine Funktion von SQL Server, in der sich die gesamte Tabelle im Arbeitsspeicher befindet.</span><span class="sxs-lookup"><span data-stu-id="7b9cb-104">[Memory-Optimized Tables](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) are a feature of SQL Server where the entire table resides in memory.</span></span> <span data-ttu-id="7b9cb-105">Eine zweite Kopie der Tabellendaten wird auf Festplatte gespeichert, aber nur zu Dauerhaftigkeitszwecken.</span><span class="sxs-lookup"><span data-stu-id="7b9cb-105">A second copy of the table data is maintained on disk, but only for durability purposes.</span></span> <span data-ttu-id="7b9cb-106">Daten in speicheroptimierten Tabellen werden nur während der Datenbankwiederherstellung vom Datenträger gelesen.</span><span class="sxs-lookup"><span data-stu-id="7b9cb-106">Data in memory-optimized tables is only read from disk during database recovery.</span></span> <span data-ttu-id="7b9cb-107">Dies ist beispielsweise nach einem Neustart des Servers der Fall.</span><span class="sxs-lookup"><span data-stu-id="7b9cb-107">For example, after a server restart.</span></span>

## <a name="configuring-a-memory-optimized-table"></a><span data-ttu-id="7b9cb-108">Konfigurieren einer Speicher optimierten Tabelle</span><span class="sxs-lookup"><span data-stu-id="7b9cb-108">Configuring a memory-optimized table</span></span>

<span data-ttu-id="7b9cb-109">Sie können angeben, dass es sich bei der einer Entität zugeordneten Tabelle um eine speicheroptimierte Tabelle handelt.</span><span class="sxs-lookup"><span data-stu-id="7b9cb-109">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="7b9cb-110">Wenn Sie EF Core verwenden, um eine Datenbank basierend auf Ihrem Modell zu erstellen und zu verwalten (entweder mit [Migrationen](xref:core/managing-schemas/migrations/index) oder [ensucreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)), wird eine Speicher optimierte Tabelle für diese Entitäten erstellt.</span><span class="sxs-lookup"><span data-stu-id="7b9cb-110">When using EF Core to create and maintain a database based on your model (either with [migrations](xref:core/managing-schemas/migrations/index) or [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)), a memory-optimized table will be created for these entities.</span></span>

[!code-csharp[IsMemoryOptimized](../../../../samples/core/SqlServer/InMemory/InMemoryContext.cs?name=IsMemoryOptimized)]
