---
title: Microsoft SQL Server Datenbankanbieter-Azure SQL-Daten Bankoptionen-EF Core
description: Angeben der Dienst Ebene und Leistungsstufe für die Azure SQL-Datenbank mit dem SQL Server Entity Framework Core-Datenbankanbieter
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/azure-sql-database
ms.openlocfilehash: 9b2cc5e0790beb41295dcea6ae30c4ad15b537fa
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065367"
---
# <a name="specifying-azure-sql-database-options"></a><span data-ttu-id="f12ef-103">Angeben von Azure SQL-Datenbank-Optionen</span><span class="sxs-lookup"><span data-stu-id="f12ef-103">Specifying Azure SQL Database Options</span></span>

>[!NOTE]
> <span data-ttu-id="f12ef-104">Diese API ist neu in EF Core 3,1.</span><span class="sxs-lookup"><span data-stu-id="f12ef-104">This API is new in EF Core 3.1.</span></span>

<span data-ttu-id="f12ef-105">Azure SQL-Datenbank bietet [eine Vielzahl von Preisoptionen](https://azure.microsoft.com/pricing/details/sql-database/single/) , die normalerweise über das Azure-Portal konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="f12ef-105">Azure SQL Database provides [a variety of pricing options](https://azure.microsoft.com/pricing/details/sql-database/single/) that are usually configured through the Azure Portal.</span></span> <span data-ttu-id="f12ef-106">Wenn Sie das Schema jedoch mit [EF Core Migrationen](xref:core/managing-schemas/migrations/index) verwalten, können Sie die gewünschten Optionen im Modell selbst angeben.</span><span class="sxs-lookup"><span data-stu-id="f12ef-106">However if you are managing the schema using [EF Core migrations](xref:core/managing-schemas/migrations/index) you can specify the desired options in the model itself.</span></span>

<span data-ttu-id="f12ef-107">Sie können die Dienst Ebene der Datenbank (Edition) mithilfe von [hasservicetier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier)angeben:</span><span class="sxs-lookup"><span data-stu-id="f12ef-107">You can specify the service tier of the database (EDITION) using [HasServiceTier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier):</span></span>

[!code-csharp[HasServiceTier](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasServiceTier)]

<span data-ttu-id="f12ef-108">Sie können die maximale Größe der Datenbank mit [hasdatabasemaxsize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize)angeben:</span><span class="sxs-lookup"><span data-stu-id="f12ef-108">You can specify the maximum size of the database using [HasDatabaseMaxSize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize):</span></span>

[!code-csharp[HasDatabaseMaxSize](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasDatabaseMaxSize)]

<span data-ttu-id="f12ef-109">Sie können die Leistungsstufe der Datenbank (SERVICE_OBJECTIVE) mithilfe von [hasperformancelevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel)angeben:</span><span class="sxs-lookup"><span data-stu-id="f12ef-109">You can specify the performance level of the database (SERVICE_OBJECTIVE) using [HasPerformanceLevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel):</span></span>

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevel)]

<span data-ttu-id="f12ef-110">Verwenden Sie [hasperformancelevelsql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql) zum Konfigurieren des Pools für elastische Datenbanken, da der Wert kein Zeichenfolgenliteralwert ist:</span><span class="sxs-lookup"><span data-stu-id="f12ef-110">Use [HasPerformanceLevelSql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql) to configure the elastic pool, since the value is not a string literal:</span></span>

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevelSql)]

>[!TIP]
> <span data-ttu-id="f12ef-111">Alle unterstützten Werte finden Sie in der [Dokumentation zu Alter Database](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).</span><span class="sxs-lookup"><span data-stu-id="f12ef-111">You can find all the supported values in the [ALTER DATABASE documentation](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).</span></span>
