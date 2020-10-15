---
title: Tabellen Aufteilung-EF Core
description: Konfigurieren der Tabellen Aufteilung mithilfe von Entity Framework Core
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/table-splitting
ms.openlocfilehash: 71d332ec3f500f48e12863c71ac44ce4d60699f0
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063035"
---
# <a name="table-splitting"></a>Tabellenaufteilung

EF Core ermöglicht das Zuordnen von zwei oder mehr Entitäten zu einer einzelnen Zeile. Dies wird als _Tabellen Aufteilung_ oder _Tabellen Freigabe_bezeichnet.

## <a name="configuration"></a>Konfiguration

Zur Verwendung der Tabellen Aufteilung müssen die Entitäts Typen derselben Tabelle zugeordnet werden, die Primärschlüssel müssen denselben Spalten zugeordnet werden, und mindestens eine Beziehung muss zwischen dem Primärschlüssel eines Entitäts Typs und einem anderen in derselben Tabelle konfiguriert werden.

Ein häufiges Szenario für die Tabellen Aufteilung ist die Verwendung einer Teilmenge der Spalten in der Tabelle, um eine höhere Leistung oder Kapselung zu erzielen.

In diesem Beispiel `Order` stellt eine Teilmenge von dar `DetailedOrder` .

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

Zusätzlich zur erforderlichen Konfiguration wird aufgerufen `Property(o => o.Status).HasColumnName("Status")` , um `DetailedOrder.Status` derselben Spalte wie zuzuordnen `Order.Status` .

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting)]

> [!TIP]
> Weitere Informationen finden Sie im [vollständigen Beispiel Projekt](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting) .

## <a name="usage"></a>Verwendung

Das Speichern und Abfragen von Entitäten mithilfe von Tabellen Aufteilung erfolgt auf dieselbe Weise wie andere Entitäten:

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="optional-dependent-entity"></a>Optionale abhängige Entität

> [!NOTE]
> Diese Funktion wurde in EF Core 3,0 eingeführt.

Wenn sich alle Spalten, die von einer abhängigen Entität verwendet werden `NULL` , in der Datenbank befinden, wird beim Abfragen keine Instanz für Sie erstellt. Dadurch kann eine optionale abhängige Entität modelliert werden, bei der die Beziehungs Eigenschaft für den Prinzipal NULL ist. Beachten Sie, dass dies auch dann der Fall wäre, wenn alle Eigenschaften der abhängigen Eigenschaft optional sind und auf festgelegt sind `null` , was nicht erwartet wird.

## <a name="concurrency-tokens"></a>Parallelitätstoken

Wenn einer der Entitäts Typen, die eine Tabelle gemeinsam nutzen, über ein Parallelitäts Token verfügt, muss dieser auch in allen anderen Entitäts Typen enthalten sein. Dies ist erforderlich, um einen veralteten Parallelitäts Token-Wert zu vermeiden, wenn nur eine Entität aktualisiert wird, die derselben Tabelle zugeordnet ist.

Um zu vermeiden, dass das Parallelitäts Token für den verarbeitenden Code verfügbar gemacht wird, ist es möglich, eine als [Schatten Eigenschaft](xref:core/modeling/shadow-properties)zu erstellen:

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
