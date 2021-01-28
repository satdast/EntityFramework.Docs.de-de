---
title: Generierte Werte-EF Core
description: Konfigurieren der Wert Generierung für Eigenschaften bei Verwendung von Entity Framework Core
author: AndriySvyryd
ms.date: 1/10/2021
uid: core/modeling/generated-properties
ms.openlocfilehash: 76fa4454c88a5ef7afb9864c2a4b1063ac75e37e
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983546"
---
# <a name="generated-values"></a>Generierte Werte

Die Werte von Daten Bank Spalten können auf verschiedene Arten generiert werden: Primärschlüssel Spalten sind häufig automatisch inkrementierende Ganzzahlen, andere Spalten haben Standardwerte oder berechnete Werte usw. Auf dieser Seite werden verschiedene Muster für die Generierung von Konfigurations Werten mit EF Core erläutert.

## <a name="default-values"></a>Standardwerte

Bei relationalen Datenbanken kann eine Spalte mit einem Standardwert konfiguriert werden. Wenn eine Zeile ohne einen Wert für diese Spalte eingefügt wird, wird der Standardwert verwendet.

Sie können einen Standardwert für eine Eigenschaft konfigurieren:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

Sie können auch ein SQL-Fragment angeben, das zum Berechnen des Standardwerts verwendet wird:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

## <a name="computed-columns"></a>Berechnete Spalten

Bei den meisten relationalen Datenbanken kann eine Spalte so konfiguriert werden, dass ihr Wert in der Datenbank berechnet wird, in der Regel mit einem Ausdruck, der auf andere Spalten verweist:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=DefaultComputedColumn&highlight=3)]

Im obigen Beispiel wird eine *virtuelle* berechnete Spalte erstellt, deren Wert jedes Mal berechnet wird, wenn Sie aus der Datenbank abgerufen wird. Sie können auch angeben, dass eine berechnete Spalte *gespeichert* werden soll (manchmal als *persistent bezeichnet)*, was bedeutet, dass Sie bei jedem Update der Zeile berechnet wird und auf dem Datenträger neben regulären Spalten gespeichert wird:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=StoredComputedColumn&highlight=3)]

> [!NOTE]
> Unterstützung für das Erstellen gespeicherter berechneter Spalten wurde in EF Core 5,0 hinzugefügt.

## <a name="primary-keys"></a>Primärschlüssel

Gemäß der Konvention sind nicht zusammengesetzte Primärschlüssel vom Typ Short, int, Long oder GUID so eingerichtet, dass Werte für eingefügte Entitäten generiert werden, wenn ein Wert nicht von der Anwendung bereitgestellt wird. Ihr Datenbankanbieter übernimmt in der Regel die erforderliche Konfiguration. Beispielsweise wird ein numerischer Primärschlüssel in SQL Server automatisch als Identitäts Spalte festgelegt.

Weitere Informationen finden Sie in [der Dokumentation zu Schlüsseln](xref:core/modeling/keys).

## <a name="explicitly-configuring-value-generation"></a>Explizites Konfigurieren der Wert Generierung

Wir haben oben gesehen, dass EF Core automatisch die Wert Generierung für Primärschlüssel festlegt, aber wir möchten dies möglicherweise für nicht Schlüsseleigenschaften verwenden. Sie können jede Eigenschaft so konfigurieren, dass ihr Wert für eingefügte Entitäten wie folgt generiert wird:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

Ebenso kann eine Eigenschaft so konfiguriert werden, dass ihr Wert beim Hinzufügen oder aktualisieren generiert wird:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

**_

> [!WARNING]
> Anders als bei Standardwerten oder berechneten Spalten geben wir nicht an, _how * die Werte generiert werden sollen. Das hängt vom verwendeten Datenbankanbieter ab. Datenbankanbieter richten möglicherweise automatisch die Wert Generierung für einige Eigenschafts Typen ein, bei anderen müssen Sie jedoch möglicherweise manuell festlegen, wie der Wert generiert wird.
>
> Wenn beispielsweise auf SQL Server eine GUID-Eigenschaft als Wert, der beim Hinzufügen generiert wird, konfiguriert ist, führt der Anbieter automatisch die Client seitige Wert Generierung aus, wobei ein Algorithmus verwendet wird, um optimale sequenzielle GUID-Werte zu generieren. `ValueGeneratedOnAdd()`Die Angabe von für eine DateTime-Eigenschaft hat jedoch keine Auswirkung (Weitere Informationen[finden Sie im nachfolgenden Abschnitt für die DateTime-Wert Generierung](#datetime-value-generation)).
>
> Ebenso werden Byte []-Eigenschaften, die beim Hinzufügen oder aktualisieren als generiert und als Parallelitäts Token gekennzeichnet sind, mit dem rowversion-Datentyp eingerichtet, sodass Werte automatisch in der Datenbank generiert werden. Das Angeben von `ValueGeneratedOnAddOrUpdate()` hat jedoch keine Auswirkungen.
>
> [!NOTE]
> Abhängig vom verwendeten Datenbankanbieter können die Werte Client seitig von EF oder in der Datenbank generiert werden. Wenn der Wert von der Datenbank generiert wird, kann EF einen temporären Wert zuweisen, wenn Sie die Entität zum Kontext hinzufügen. Dieser temporäre Wert wird dann durch den von der Datenbank generierten Wert ersetzt `SaveChanges()` . Weitere Informationen finden Sie [in der Dokumentation zu temporären Werten](xref:core/change-tracking/explicit-tracking#temporary-values).

## <a name="datetime-value-generation"></a>Datums-/uhrzeitwertgenerierung

Eine häufige Anforderung besteht darin, eine Daten Bank Spalte zu haben, die das Datum und die Uhrzeit für den ersten Einfügevorgang der Spalte (beim hinzufügen generierten Wert) oder für den Zeitpunkt der letzten Aktualisierung (beim Hinzufügen oder aktualisieren generierten Wert) enthält. Wie es verschiedene Strategien gibt, legen EF Core Anbieter in der Regel keine Wert Generierung für Datums-/Uhrzeitspalten fest. Dies müssen Sie selbst konfigurieren.

### <a name="creation-timestamp"></a>Erstellungszeit Stempel

Das Konfigurieren einer Datums-/uhrzeitspalte mit dem Erstellungszeit Stempel der Zeile ist in der Regel eine Frage, wie ein Standardwert mit der entsprechenden SQL-Funktion konfiguriert wird. Beispielsweise können Sie auf SQL Server Folgendes verwenden:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

Stellen Sie sicher, dass Sie die entsprechende Funktion auswählen, da mehrere vorhanden sein können (z. b. `GETDATE()` vs `GETUTCDATE()` ).

### <a name="update-timestamp"></a>Zeitstempel aktualisieren

Obwohl gespeicherte berechnete Spalten eine gute Lösung für die Verwaltung der zuletzt aktualisierten Zeitstempel sind, können Datenbanken in der Regel keine Funktionen wie `GETDATE()` in einer berechneten Spalte angeben. Als Alternative können Sie einen Daten Bank Auslösers einrichten, um denselben Effekt zu erzielen:

```sql
CREATE TRIGGER [dbo].[Blogs_UPDATE] ON [dbo].[Blogs]
    AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    IF ((SELECT TRIGGER_NESTLEVEL()) > 1) RETURN;

    DECLARE @Id INT

    SELECT @Id = INSERTED.BlogId
    FROM INSERTED

    UPDATE dbo.Blogs
    SET LastUpdated = GETDATE()
    WHERE BlogId = @Id
END
```

Weitere Informationen zum Erstellen von Triggern [finden Sie in der Dokumentation zur Verwendung von Rohdaten in SQL bei Migrationen](xref:core/managing-schemas/migrations/managing#adding-raw-sql).

## <a name="overriding-value-generation"></a>Überschreiben der Wert Generierung

Obwohl eine Eigenschaft für die Generierung von Werten konfiguriert ist, können Sie in vielen Fällen trotzdem explizit einen Wert angeben. Ob dies tatsächlich funktioniert, hängt vom spezifischen Wert Generierungs Mechanismus ab, der konfiguriert wurde. Obwohl Sie einen expliziten Wert angeben dürfen, anstatt den Standardwert einer Spalte zu verwenden, kann dies nicht mit berechneten Spalten erfolgen.

Um die Wert Generierung mit einem expliziten Wert zu überschreiben, legen Sie einfach die-Eigenschaft auf einen beliebigen Wert fest, der nicht der CLR-Standardwert für den Eigenschaftentyp ist ( `null` für, `string` für, `0` `int` `Guid.Empty` `Guid` usw.).

> [!NOTE]
> Der Versuch, explizite Werte in SQL Server Identität einzufügen, schlägt standardmäßig fehl. [eine Problem Umgehung finden](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns)Sie in diesen Dokumentationen.

Um einen expliziten Wert für Eigenschaften bereitzustellen, die als Wert konfiguriert wurden, der beim Hinzufügen oder aktualisieren generiert wurde, müssen Sie auch die-Eigenschaft wie folgt konfigurieren:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior.cs?name=ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior&highlight=5)]

## <a name="no-value-generation"></a>Keine Wert Generierung

Abgesehen von bestimmten Szenarien, wie z. b. den oben beschriebenen, haben Eigenschaften in der Regel keine konfigurierte Wert Generierung. Dies bedeutet, dass die Anwendung immer einen Wert bereitstellen muss, der in der Datenbank gespeichert werden soll. Dieser Wert muss neuen Entitäten zugewiesen werden, bevor Sie dem Kontext hinzugefügt werden.

In einigen Fällen möchten Sie jedoch möglicherweise die durch Konvention festgelegte Wert Generierung deaktivieren. Beispielsweise wird ein Primärschlüssel vom Typ int in der Regel implizit als Value-generated-on-Add (z. b. Identitäts Spalte auf SQL Server) konfiguriert. Dies kann über Folgendes deaktiviert werden:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
