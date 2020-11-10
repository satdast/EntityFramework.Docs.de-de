---
title: Generierte Werte-EF Core
description: Konfigurieren der Wert Generierung für Eigenschaften bei Verwendung von Entity Framework Core
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/generated-properties
ms.openlocfilehash: 347cedbf5fdebc985d75c6cad3c28f17d1344993
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429623"
---
# <a name="generated-values"></a>Generierte Werte

## <a name="value-generation-patterns"></a>Muster Generierungs Muster

Es gibt drei Muster Generierungs Muster, die für Eigenschaften verwendet werden können:

* Keine Wert Generierung
* Beim Hinzufügen generierter Wert
* Beim Hinzufügen oder aktualisieren generierter Wert

### <a name="no-value-generation"></a>Keine Wert Generierung

Keine Wert Generierung bedeutet, dass Sie immer einen gültigen Wert angeben, der in der Datenbank gespeichert werden soll. Dieser gültige Wert muss neuen Entitäten zugewiesen werden, bevor Sie dem Kontext hinzugefügt werden.

### <a name="value-generated-on-add"></a>Beim Hinzufügen generierter Wert

Beim Hinzufügen generierter Wert bedeutet, dass für neue Entitäten ein Wert generiert wird.

Abhängig vom verwendeten Datenbankanbieter können die Werte Client seitig von EF oder in der Datenbank generiert werden. Wenn der Wert von der Datenbank generiert wird, kann EF einen temporären Wert zuweisen, wenn Sie die Entität zum Kontext hinzufügen. Dieser temporäre Wert wird dann durch den von der Datenbank generierten Wert ersetzt `SaveChanges()` .

Wenn Sie dem Kontext eine Entität hinzufügen, die über einen Wert verfügt, der der Eigenschaft zugewiesen ist, versucht EF, diesen Wert einzufügen, anstatt einen neuen zu erstellen. Einer Eigenschaft wird ein Wert zugewiesen, wenn ihr kein CLR-Standardwert (für, für, `null` `string` `0` `int` `Guid.Empty` `Guid` usw.) zugewiesen ist. Weitere Informationen finden Sie unter [explizite Werte für generierte Eigenschaften](xref:core/saving/explicit-values-generated-properties).

> [!WARNING]
> Die Art, wie der Wert für hinzugefügte Entitäten generiert wird, hängt vom verwendeten Datenbankanbieter ab. Datenbankanbieter richten möglicherweise automatisch die Wert Generierung für einige Eigenschafts Typen ein, bei anderen müssen Sie jedoch möglicherweise manuell festlegen, wie der Wert generiert wird.
>
> Wenn Sie z. b. SQL Server verwenden, werden Werte automatisch für `GUID` Eigenschaften generiert (mithilfe des SQL Server sequenziellen GUID-Algorithmus). Wenn Sie jedoch angeben, dass eine `DateTime` Eigenschaft beim Hinzufügen generiert wird, müssen Sie eine Möglichkeit einrichten, damit die Werte generiert werden. Eine Möglichkeit, dies zu erreichen, besteht darin, einen Standardwert von zu konfigurieren `GETDATE()` , siehe [Standardwerte](#default-values).

### <a name="value-generated-on-add-or-update"></a>Beim Hinzufügen oder aktualisieren generierter Wert

Der beim Hinzufügen oder aktualisieren generierte Wert bedeutet, dass bei jedem Speichern des Datensatzes (einfügen oder aktualisieren) ein neuer Wert generiert wird.

`value generated on add`Wenn Sie z. b. einen Wert für die-Eigenschaft in einer neu hinzugefügten Instanz einer Entität angeben, wird dieser Wert anstelle eines generierten Werts eingefügt. Es ist auch möglich, bei der Aktualisierung einen expliziten Wert festzulegen. Weitere Informationen finden Sie unter [explizite Werte für generierte Eigenschaften](xref:core/saving/explicit-values-generated-properties).

> [!WARNING]
> Die Art und Weise, wie der Wert für hinzugefügte und aktualisierte Entitäten generiert wird, hängt vom verwendeten Datenbankanbieter ab. Datenbankanbieter können die Wert Generierung für einige Eigenschafts Typen automatisch einrichten, während andere manuell festlegen müssen, wie der Wert generiert wird.
>
> Wenn Sie z. b. SQL Server verwenden, werden Eigenschaften, die `byte[]` beim Hinzufügen oder aktualisieren als generiert und als Parallelitäts Token gekennzeichnet sind, mit dem `rowversion` -Datentyp festgelegt, sodass Werte in der Datenbank generiert werden. Wenn Sie jedoch angeben, dass eine `DateTime` Eigenschaft beim Hinzufügen oder aktualisieren generiert wird, müssen Sie eine Möglichkeit einrichten, damit die Werte generiert werden. Eine Möglichkeit hierfür besteht darin, den Standardwert `GETDATE()` (siehe [Standardwerte](#default-values)) zu konfigurieren, um Werte für neue Zeilen zu generieren. Sie können dann mithilfe eines Daten Bank Auslösers Werte während der Aktualisierung generieren (z. b. im folgenden Beispiel-auslösen).
>
> [!code-sql[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.sql)]

## <a name="value-generated-on-add"></a>Beim Hinzufügen generierter Wert

Gemäß der Konvention sind nicht zusammengesetzte Primärschlüssel vom Typ Short, int, Long oder GUID so eingerichtet, dass Werte für eingefügte Entitäten generiert werden, wenn ein Wert nicht von der Anwendung bereitgestellt wird. Ihr Datenbankanbieter übernimmt in der Regel die erforderliche Konfiguration. Beispielsweise wird ein numerischer Primärschlüssel in SQL Server automatisch als Identitäts Spalte festgelegt.

Sie können jede Eigenschaft so konfigurieren, dass ihr Wert für eingefügte Entitäten wie folgt generiert wird:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

> [!WARNING]
> Dadurch kann EF nur wissen, dass Werte für hinzugefügte Entitäten generiert werden, und es wird nicht garantiert, dass EF den eigentlichen Mechanismus zum Generieren von Werten einrichtet. Weitere Informationen finden [Sie unter im Abschnitt "hinzufügen" generierter Wert](#value-generated-on-add) .

### <a name="default-values"></a>Standardwerte

Bei relationalen Datenbanken kann eine Spalte mit einem Standardwert konfiguriert werden. Wenn eine Zeile ohne einen Wert für diese Spalte eingefügt wird, wird der Standardwert verwendet.

Sie können einen Standardwert für eine Eigenschaft konfigurieren:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

Sie können auch ein SQL-Fragment angeben, das zum Berechnen des Standardwerts verwendet wird:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

Wenn Sie einen Standardwert angeben, wird die Eigenschaft implizit als Wert konfiguriert, der beim Hinzufügen generiert wird.

## <a name="value-generated-on-add-or-update"></a>Beim Hinzufügen oder aktualisieren generierter Wert

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

**_

> [!WARNING]
> Dies ermöglicht EF nur zu wissen, dass Werte für hinzugefügte oder aktualisierte Entitäten generiert werden. es wird nicht garantiert, dass EF den eigentlichen Mechanismus zum Generieren von Werten einrichtet. Weitere Informationen finden [Sie im Abschnitt hinzufügen oder aktualisieren generierter Wert](#value-generated-on-add-or-update) .

### <a name="computed-columns"></a>Berechnete Spalten

Bei den meisten relationalen Datenbanken kann eine Spalte so konfiguriert werden, dass ihr Wert in der Datenbank berechnet wird, in der Regel mit einem Ausdruck, der auf andere Spalten verweist:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=DefaultComputedColumn)]

Im obigen Beispiel wird eine _virtual * berechnete Spalte erstellt, deren Wert jedes Mal berechnet wird, wenn Sie aus der Datenbank abgerufen wird. Sie können auch angeben, dass eine berechnete Spalte *gespeichert* werden soll (manchmal als *persistent bezeichnet)* , was bedeutet, dass Sie bei jedem Update der Zeile berechnet wird und auf dem Datenträger neben regulären Spalten gespeichert wird:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=StoredComputedColumn)]

> [!NOTE]
> Unterstützung für das Erstellen gespeicherter berechneter Spalten wurde in EF Core 5,0 hinzugefügt.

## <a name="no-value-generation"></a>Keine Wert Generierung

Die Deaktivierung der Wert Generierung für eine Eigenschaft ist in der Regel erforderlich, wenn eine Konvention für die Generierung von Werten konfiguriert wird. Wenn Sie z. b. über einen Primärschlüssel vom Typ "int" verfügen, wird dieser implizit als Wert festgelegt, der beim Hinzufügen generiert wird. Dies kann über Folgendes deaktiviert werden:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
