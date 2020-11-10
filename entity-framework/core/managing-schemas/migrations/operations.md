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
# <a name="custom-migrations-operations"></a>Benutzerdefinierte Migrations Vorgänge

Die migrationbuilder-API ermöglicht es Ihnen, viele verschiedene Arten von Vorgängen während einer Migration auszuführen, aber es ist weit von der Vollständigkeit her. Die API ist jedoch auch erweiterbar, sodass Sie eigene Vorgänge definieren können. Es gibt zwei Möglichkeiten, die API zu erweitern: mithilfe der- `Sql()` Methode oder durch Definieren von benutzerdefinierten `MigrationOperation` Objekten.

Um dies zu veranschaulichen, betrachten wir die Implementierung eines Vorgangs, der einen Datenbankbenutzer mit jedem Ansatz erstellt. In unseren Migrationen möchten wir das Schreiben des folgenden Codes aktivieren:

```csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a>Verwenden von migrationbuilder. SQL ()

Die einfachste Möglichkeit zum Implementieren eines benutzerdefinierten Vorgangs besteht darin, eine Erweiterungsmethode zu definieren, die aufruft `MigrationBuilder.Sql()` . Es folgt ein Beispiel, das die entsprechende Transact-SQL-Anwendung generiert.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationSql.cs#snippet_CustomOperationSql)]

> [!TIP]
> Verwenden Sie die- `EXEC` Funktion, wenn eine Anweisung der erste oder einzige in einem SQL-Batch sein muss. Es kann auch erforderlich sein, Parserfehler in idempotenten Migrations Skripts zu umgehen, die auftreten können, wenn Spalten, auf die verwiesen wird, derzeit nicht in einer Tabelle vorhanden sind

Wenn Ihre Migrationen mehrere Datenbankanbieter unterstützen müssen, können Sie die- `MigrationBuilder.ActiveProvider` Eigenschaft verwenden. Hier sehen Sie ein Beispiel, das sowohl Microsoft SQL Server als auch PostgreSQL unterstützt.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationMultiSql.cs#snippet_CustomOperationMultiSql)]

Dieser Ansatz funktioniert nur, wenn Sie jeden Anbieter kennen, auf den der benutzerdefinierte Vorgang angewendet wird.

## <a name="using-a-migrationoperation"></a>Verwenden von migrationoperation

Um den benutzerdefinierten Vorgang von SQL zu entkoppeln, können Sie einen eigenen definieren, `MigrationOperation` um ihn zu repräsentieren. Der Vorgang wird dann an den Anbieter weitergegeben, damit er die zu generierende SQL-Datei bestimmen kann.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_CreateUserOperation)]

Bei diesem Ansatz muss die Erweiterungsmethode nur einen dieser Vorgänge hinzufügen `MigrationBuilder.Operations` .

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationBuilderExtension)]

Diese Vorgehensweise erfordert, dass jeder Anbieter weiß, wie SQL für diesen Vorgang in seinem Dienst generiert wird `IMigrationsSqlGenerator` . Hier ist ein Beispiel, das den Generator der SQL Server überschreibt, um den neuen Vorgang zu verarbeiten.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationsSqlGenerator)]

Ersetzen Sie den SQL-Generator-Standard Migrationsdienst durch den aktualisierten Dienst.

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_OnConfiguring)]
