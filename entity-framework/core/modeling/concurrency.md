---
title: Parallelitäts Token-EF Core
description: Konfigurieren von Parallelitäts Token für die Steuerung optimistischer Parallelität in einem Entity Framework Core Modell
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: bc8b1cb0-befe-4b67-8004-26e6c5f69385
uid: core/modeling/concurrency
ms.openlocfilehash: 57718a1b0146f437bb4126524889c81ae0dfd895
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617543"
---
# <a name="concurrency-tokens"></a>Parallelitätstoken

> [!NOTE]
> Auf dieser Seite wird das Konfigurieren von Parallelitäts Token dokumentiert. Eine ausführliche Erläuterung der Funktionsweise der Parallelitäts Steuerung in EF Core und Beispielen zur Behandlung von Parallelitäts Konflikten in der Anwendung finden Sie unter Behandeln von Parallelitäts [Konflikten](xref:core/saving/concurrency) .

Als Parallelitäts Token konfigurierte Eigenschaften werden verwendet, um die Steuerung der vollständigen Parallelität zu implementieren.

## <a name="configuration"></a>Konfiguration

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Concurrency.cs?name=Concurrency&highlight=5)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Concurrency.cs?name=Concurrency&highlight=5)]

***

## <a name="timestamprowversion"></a>Timestamp/rowversion

Ein Timestamp/rowversion-Objekt ist eine Eigenschaft, für die ein neuer Wert automatisch von der Datenbank generiert wird, wenn eine Zeile eingefügt oder aktualisiert wird. Die-Eigenschaft wird auch als Parallelitäts Token behandelt, um sicherzustellen, dass Sie eine Ausnahme erhalten, wenn sich eine Zeile, die Sie aktualisieren, geändert hat, seit Sie Sie abgefragt haben. Die genauen Details hängen vom verwendeten Datenbankanbieter ab. für SQL Server wird normalerweise eine *Byte []* -Eigenschaft verwendet, die als *rowversion* -Spalte in der Datenbank eingerichtet wird.

Sie können eine Eigenschaft wie folgt als timestamp/rowversion konfigurieren:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Timestamp.cs?name=Timestamp&highlight=7)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Timestamp.cs?name=Timestamp&highlight=9,17)]

***
