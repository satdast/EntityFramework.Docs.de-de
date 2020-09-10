---
title: Microsoft SQL Server Datenbankanbieter-Azure SQL-Daten Bankoptionen-EF Core
description: Angeben der Dienst Ebene und Leistungsstufe für die Azure SQL-Datenbank mit dem SQL Server Entity Framework Core-Datenbankanbieter
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/azure-sql-database
ms.openlocfilehash: 3863a5a224ba26df8cb319844bc2af4158d2497a
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618959"
---
# <a name="specifying-azure-sql-database-options"></a><span data-ttu-id="faf3c-103">Angeben von Azure SQL-Datenbank-Optionen</span><span class="sxs-lookup"><span data-stu-id="faf3c-103">Specifying Azure SQL Database Options</span></span>

>[!NOTE]
> <span data-ttu-id="faf3c-104">Diese API ist neu in EF Core 3,1.</span><span class="sxs-lookup"><span data-stu-id="faf3c-104">This API is new in EF Core 3.1.</span></span>

<span data-ttu-id="faf3c-105">Azure SQL-Datenbank bietet [eine Vielzahl von Preisoptionen](https://azure.microsoft.com/pricing/details/sql-database/single/) , die normalerweise über das Azure-Portal konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="faf3c-105">Azure SQL Database provides [a variety of pricing options](https://azure.microsoft.com/pricing/details/sql-database/single/) that are usually configured through the Azure Portal.</span></span> <span data-ttu-id="faf3c-106">Wenn Sie das Schema jedoch mit [EF Core Migrationen](xref:core/managing-schemas/migrations/index) verwalten, können Sie die gewünschten Optionen im Modell selbst angeben.</span><span class="sxs-lookup"><span data-stu-id="faf3c-106">However if you are managing the schema using [EF Core migrations](xref:core/managing-schemas/migrations/index) you can specify the desired options in the model itself.</span></span>

<span data-ttu-id="faf3c-107">Sie können die Dienst Ebene der Datenbank (Edition) mithilfe von [hasservicetier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier)angeben:</span><span class="sxs-lookup"><span data-stu-id="faf3c-107">You can specify the service tier of the database (EDITION) using [HasServiceTier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier):</span></span>

[!code-csharp[HasServiceTier](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasServiceTier)]

<span data-ttu-id="faf3c-108">Sie können die maximale Größe der Datenbank mit [hasdatabasemaxsize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize)angeben:</span><span class="sxs-lookup"><span data-stu-id="faf3c-108">You can specify the maximum size of the database using [HasDatabaseMaxSize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize):</span></span>

[!code-csharp[HasDatabaseMaxSize](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasDatabaseMaxSize)]

<span data-ttu-id="faf3c-109">Sie können die Leistungsstufe der Datenbank (SERVICE_OBJECTIVE) mithilfe von [hasperformancelevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel)angeben:</span><span class="sxs-lookup"><span data-stu-id="faf3c-109">You can specify the performance level of the database (SERVICE_OBJECTIVE) using [HasPerformanceLevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel):</span></span>

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevel)]

<span data-ttu-id="faf3c-110">Verwenden Sie [hasperformancelevelsql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql) zum Konfigurieren des Pools für elastische Datenbanken, da der Wert kein Zeichenfolgenliteralwert ist:</span><span class="sxs-lookup"><span data-stu-id="faf3c-110">Use [HasPerformanceLevelSql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql) to configure the elastic pool, since the value is not a string literal:</span></span>

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevelSql)]

>[!TIP]
> <span data-ttu-id="faf3c-111">Alle unterstützten Werte finden Sie in der [Dokumentation zu Alter Database](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).</span><span class="sxs-lookup"><span data-stu-id="faf3c-111">You can find all the supported values in the [ALTER DATABASE documentation](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).</span></span>
