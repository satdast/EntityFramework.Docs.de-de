---
title: Microsoft SQL Server Datenbankanbieter-Wert Generierung-EF Core
description: Für den SQL Server Entity Framework Core Datenbankanbieter spezifische Wert Generierungs Muster
author: roji
ms.date: 1/10/2020
uid: core/providers/sql-server/value-generation
ms.openlocfilehash: 90608f254a3d20e3c36586ae8325e0a982a7fa27
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987137"
---
# <a name="sql-server-value-generation"></a>SQL Server Wert Generierung

Diese Seite enthält Details zur Konfiguration der Wert Generierung und Muster, die für den SQL Server-Anbieter spezifisch sind. Es wird empfohlen, zuerst [die Seite Allgemein der Wert Generierung](xref:core/modeling/generated-properties)zu lesen.

## <a name="identity-columns"></a>IDENTITY-Spalten

Gemäß der Konvention werden numerische Spalten, die so konfiguriert sind, dass ihre Werte bei Add generiert werden, als [SQL Server Identitäts Spalten](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property)eingerichtet.

### <a name="seed-and-increment"></a>Seed und Inkrement

Standardmäßig beginnen Identitäts Spalten bei 1 (dem Startwert) und werden jedes Mal um 1 erhöht, wenn eine Zeile hinzugefügt wird (das Inkrement). Sie können einen anderen Ausgangs Ausgangs-und Inkrement wie folgt konfigurieren:

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/IdentityOptionsContext.cs?name=IdentityOptions&highlight=5)]

> [!NOTE]
> Die Möglichkeit zum Konfigurieren des Identitäts Ausgangs und-Inkrements wurde in EF Core 3,0 eingeführt.

### <a name="inserting-explicit-values-into-identity-columns"></a>Einfügen von expliziten Werten in Identitäts Spalten

Standardmäßig lässt SQL Server das Einfügen expliziter Werte in Identitäts Spalten nicht zu. Zu diesem Zweck müssen Sie vor dem Aufrufen von manuell aktivieren `IDENTITY_INSERT` `SaveChanges()` , wie im folgenden beschrieben:

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/ExplicitIdentityValues.cs?name=ExplicitIdentityValues)]

> [!NOTE]
> Die Automatisierung dieses Vorgangs im SQL Server-Anbieter ist eine [Featureanforderung](https://github.com/aspnet/EntityFramework/issues/703), die sich im Backlog befindet.

## <a name="sequences"></a>Sequenzen

Als Alternative zu Identitäts Spalten können Standardsequenzen verwendet werden. Dies kann in verschiedenen Szenarien nützlich sein: Beispielsweise kann es sein, dass mehrere Spalten ihre Standardwerte aus einer einzelnen Sequenz zeichnen.

SQL Server ermöglicht das Erstellen von Sequenzen und deren Verwendung, wie [auf der Seite Allgemein in Sequenzen](xref:core/modeling/sequences)beschrieben. Sie müssen ihre Eigenschaften so konfigurieren, dass Sie Sequenzen über verwenden `HasDefaultValueSql()` .
