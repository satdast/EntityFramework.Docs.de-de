---
title: Microsoft SQL Server Datenbankanbieter-Azure SQL-Daten Bankoptionen-EF Core
description: Angeben der Dienst Ebene und Leistungsstufe für die Azure SQL-Datenbank mit dem SQL Server Entity Framework Core-Datenbankanbieter
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/azure-sql-database
ms.openlocfilehash: ad202336c2c2efdfe17776952f2a65e98222ecc0
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003587"
---
# <a name="specifying-azure-sql-database-options"></a>Angeben von Azure SQL-Datenbank-Optionen

>[!NOTE]
> Diese API wurde in EF Core 3,1 eingeführt.

Azure SQL-Datenbank bietet [eine Vielzahl von Preisoptionen](https://azure.microsoft.com/pricing/details/sql-database/single/) , die normalerweise über das Azure-Portal konfiguriert werden. Wenn Sie das Schema jedoch mit [EF Core Migrationen](xref:core/managing-schemas/migrations/index) verwalten, können Sie die gewünschten Optionen im Modell selbst angeben.

Sie können die Dienst Ebene der Datenbank (Edition) mithilfe von [hasservicetier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier)angeben:

[!code-csharp[HasServiceTier](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasServiceTier)]

Sie können die maximale Größe der Datenbank mit [hasdatabasemaxsize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize)angeben:

[!code-csharp[HasDatabaseMaxSize](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasDatabaseMaxSize)]

Sie können die Leistungsstufe der Datenbank (SERVICE_OBJECTIVE) mithilfe von [hasperformancelevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel)angeben:

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevel)]

Verwenden Sie [hasperformancelevelsql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql) zum Konfigurieren des Pools für elastische Datenbanken, da der Wert kein Zeichenfolgenliteralwert ist:

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevelSql)]

>[!TIP]
> Alle unterstützten Werte finden Sie in der [Dokumentation zu Alter Database](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true).
