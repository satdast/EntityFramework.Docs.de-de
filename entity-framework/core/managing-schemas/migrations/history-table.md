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
# <a name="custom-migrations-history-table"></a>Benutzerdefinierte Migrations Verlaufs Tabelle

Standardmäßig wird von EF Core nachverfolgt, welche Migrationen auf die Datenbank angewendet wurden, indem Sie in einer Tabelle mit dem Namen aufgezeichnet werden `__EFMigrationsHistory` . Aus verschiedenen Gründen kann es sinnvoll sein, diese Tabelle anzupassen, damit Sie Ihren Anforderungen besser entspricht.

> [!IMPORTANT]
> Wenn Sie die Migrations Verlaufs Tabelle *nach* dem Anwenden von Migrationen anpassen, sind Sie dafür verantwortlich, die vorhandene Tabelle in der Datenbank zu aktualisieren.

## <a name="schema-and-table-name"></a>Schema-und Tabellenname

Sie können das Schema und den Tabellennamen mithilfe der- `MigrationsHistoryTable()` Methode in `OnConfiguring()` (oder `ConfigureServices()` auf ASP.net Core) ändern. Im folgenden finden Sie ein Beispiel für die Verwendung des SQL Server EF Core Anbieters.

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a>Weitere Änderungen

Um zusätzliche Aspekte der Tabelle zu konfigurieren, überschreiben Sie den anbieterspezifischen Dienst, und ersetzen Sie ihn `IHistoryRepository` . Im folgenden finden Sie ein Beispiel für das Ändern des Namens der migrationid-Spalte in " *ID* " in SQL Server.

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> `SqlServerHistoryRepository` befindet sich in einem internen Namespace und kann sich in zukünftigen Versionen ändern.

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
