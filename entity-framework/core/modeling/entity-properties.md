---
title: Entitäts Eigenschaften-EF Core
description: Konfigurieren und Zuordnen von Entitäts Eigenschaften mithilfe von Entity Framework Core
author: roji
ms.date: 05/27/2020
uid: core/modeling/entity-properties
ms.openlocfilehash: fe6dd2c24b8f8ffffa8e0101f69966b7b7c74036
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2020
ms.locfileid: "97635548"
---
# <a name="entity-properties"></a>Entitätseigenschaften

Jeder Entitätstyp in Ihrem Modell verfügt über eine Reihe von Eigenschaften, die EF Core aus der Datenbank lesen und schreiben. Wenn Sie eine relationale Datenbank verwenden, werden die Entitäts Eigenschaften Tabellen Spalten zugeordnet.

## <a name="included-and-excluded-properties"></a>Enthaltene und ausgeschlossene Eigenschaften

Gemäß der Konvention werden alle öffentlichen Eigenschaften mit einem Getter und einem Setter in das Modell eingeschlossen.

Bestimmte Eigenschaften können wie folgt ausgeschlossen werden:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a>Spaltennamen

Gemäß der Konvention werden bei Verwendung einer relationalen Datenbank Entitäts Eigenschaften Tabellen Spalten mit dem gleichen Namen wie die Eigenschaft zugeordnet.

Wenn Sie Ihre Spalten lieber mit unterschiedlichen Namen konfigurieren möchten, können Sie dies wie folgt ausführen:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

**_

## <a name="column-data-types"></a>Spaltendatentypen

Wenn Sie eine relationale Datenbank verwenden, wählt der Datenbankanbieter einen Datentyp aus, der auf dem .NET-Typ der Eigenschaft basiert. Es berücksichtigt auch andere Metadaten, z. b. die konfigurierte [Maximale Länge](#maximum-length), ob die Eigenschaft Teil eines Primärschlüssels ist usw.

SQL Server z `DateTime` `datetime2(7)` . b. Eigenschaften Spalten und `string` Eigenschaften zu `nvarchar(max)` Spalten (oder `nvarchar(450)` für Eigenschaften, die als Schlüssel verwendet werden).

Sie können auch die Spalten so konfigurieren, dass Sie einen exakten Datentyp für eine Spalte angeben. Der folgende Code konfiguriert z. b. `Url` als nicht-Unicode-Zeichenfolge mit einer maximalen Länge von `200` und `Rating` als Dezimal Länge mit einer Genauigkeit von `5` und einer Skala von `2` :

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

_*_

### <a name="maximum-length"></a>Maximale Länge

Das Konfigurieren einer maximalen Länge gibt dem Datenbankanbieter einen Hinweis zum entsprechenden Spaltendatentyp, der für eine bestimmte Eigenschaft ausgewählt werden soll. Die maximale Länge gilt nur für Array Datentypen, `string` z `byte[]` . b. und.

> [!NOTE]
> Entity Framework führt keine Überprüfung der maximalen Länge durch, bevor Daten an den Anbieter übergeben werden. Der Anbieter oder Datenspeicher muss ggf. überprüft werden. Wenn Sie z. b. auf SQL Server abzielen, führt das Überschreiten der maximalen Länge zu einer Ausnahme, da der Datentyp der zugrunde liegenden Spalte nicht zulässt, dass überschüssige Daten gespeichert werden.

Im folgenden Beispiel bewirkt das Konfigurieren einer maximalen Länge von 500, dass eine Spalte vom Typ `nvarchar(500)` auf SQL Server erstellt wird:

#### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

_*_

### <a name="precision-and-scale"></a>Genauigkeit und Skalierung

Ab efcore 5,0 können Sie die überflüssige API verwenden, um Genauigkeit und Skalierung zu konfigurieren. Er teilt dem Datenbankanbieter mit, wie viel Speicherplatz für eine bestimmte Spalte benötigt wird. Dies gilt nur für Datentypen, bei denen der Anbieter die Genauigkeit und die Skalierung unterscheiden kann, in der Regel `decimal` und `DateTime` .

Bei `decimal` Eigenschaften definiert Genauigkeit die maximale Anzahl von Ziffern, die erforderlich sind, um einen Wert auszudrücken, den die Spalte enthalten wird, und die Dezimalstellen definieren die maximale Anzahl erforderlicher Dezimalstellen. Bei `DateTime` Eigenschaften definiert Genauigkeit die maximale Anzahl von Ziffern, die zum Ausdrücken von Sekundenbruchteilen benötigt werden, und die Skalierung wird nicht verwendet.

> [!NOTE]
> Entity Framework führt keine Validierung der Genauigkeit oder der Skalierung durch, bevor Daten an den Anbieter übergeben werden. Es ist für den Anbieter oder den Datenspeicher erforderlich, nach Bedarf zu validieren. Wenn Sie z. b. auf SQL Server abzielen, lässt eine Spalte des-Datentyps `datetime` nicht zu, dass die Genauigkeit festgelegt wird, wohingegen eine eine `datetime2` Genauigkeit zwischen 0 und 7 einschließlich aufweisen kann.

Im folgenden Beispiel führt die Konfiguration der `Score` -Eigenschaft für die Genauigkeit 14 und die Skala 2 dazu, dass eine Spalte vom Typ `decimal(14,2)` auf SQL Server erstellt wird, und die Konfiguration der- `LastUpdated` Eigenschaft mit der Genauigkeit 3 bewirkt eine Spalte vom Typ `datetime2(3)` :

#### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

Genauigkeit und Skalierung können zurzeit nicht über Daten Anmerkungen konfiguriert werden.

#### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/PrecisionAndScale.cs?name=PrecisionAndScale&highlight=3-9)]

> [!NOTE]
> Die Skalierung wird nie definiert, ohne dass zuerst die Genauigkeit definiert wird, daher ist die fließende API zum Definieren der Skala `HasPrecision(precision, scale)` .

_*_

## <a name="required-and-optional-properties"></a>Erforderliche und optionale Eigenschaften

Eine Eigenschaft wird als optional eingestuft, wenn Sie gültig ist `null` . Wenn `null` kein gültiger Wert ist, der einer Eigenschaft zugewiesen werden soll, wird er als erforderliche Eigenschaft betrachtet. Bei der Zuordnung zu einem relationalen Datenbankschema werden erforderliche Eigenschaften als Spalten erstellt, die keine NULL-Werte zulassen, und optionale Eigenschaften werden als Spalten erstellt, die NULL-Werte zulassen.

### <a name="conventions"></a>Konventionen

Gemäß der Konvention wird eine Eigenschaft, deren .NET-Typ NULL enthalten kann, als optional konfiguriert, wohingegen Eigenschaften, deren .NET-Typ keinen NULL-Wert enthalten darf, als erforderlich konfiguriert werden. Beispielsweise werden alle Eigenschaften mit .net-Werttypen ( `int` , `decimal` , `bool` usw.) als erforderlich konfiguriert, und alle Eigenschaften mit .net-Werttypen, die NULL-Werte zulassen ( `int?` , `decimal?` , `bool?` usw.) werden als optional konfiguriert.

In c# 8 wurde ein neues Feature namens " [Werte zulässt Reference Types (NRT)](/dotnet/csharp/tutorials/nullable-reference-types)" eingeführt, mit dem Verweis Typen mit Anmerkungen versehen werden können. Dies gibt an, ob es zulässig ist, dass NULL-Werte enthalten sind. Diese Funktion ist standardmäßig deaktiviert und wirkt sich auf das Verhalten EF Core wie folgt aus:

_ Wenn Verweis Typen, die NULL-Werte zulassen, deaktiviert sind (Standardeinstellung), werden alle Eigenschaften mit .net-Verweis Typen gemäß der Konvention (z. b.) als optional konfiguriert `string` .
* Wenn Verweis Typen, die NULL-Werte zulassen, aktiviert sind, werden die Eigenschaften basierend auf der c#-NULL-Zulässigkeit ihres .net-Typs konfiguriert: `string?` wird als optional konfiguriert, `string` wird jedoch als erforderlich konfiguriert.

Das folgende Beispiel zeigt einen Entitätstyp mit erforderlichen und optionalen Eigenschaften, wobei die Verweis Funktion NULL-Werte ist deaktiviert (Standard) und aktiviert ist:

#### <a name="without-nrt-default"></a>[Ohne NRT (Standard)](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nrt"></a>[Mit NRT](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

Die Verwendung von Verweis Typen, die NULL-Werte zulassen, wird empfohlen, da Sie die in c#-Code ausgedrückte NULL-Zulässigkeit in das Modell von EF Core und die Datenbank übermittelt und die Verwendung der flüssigen API oder Daten Anmerkungen zum doppelten Ausdrücken des gleichen Konzepts überflüssig macht.

> [!NOTE]
> Vorsicht beim Aktivieren von Verweis Typen, die NULL-Werte zulassen, für ein vorhandenes Projekt: Verweistyp Eigenschaften, die zuvor als optional konfiguriert wurden, werden nun als erforderlich konfiguriert, es sei denn, Sie sind explizit mit Anmerkungen versehen, die NULL-Werte zulassen. Wenn Sie ein relationales Datenbankschema verwalten, kann dies dazu führen, dass Migrationen generiert werden, die die NULL-Zulässigkeit der Daten Bank Spalte ändern.

Weitere Informationen zu Verweis Typen, die NULL-Werte zulassen, und deren Verwendung mit EF Core finden Sie auf [der dedizierten Dokumentationsseite für dieses Feature](xref:core/miscellaneous/nullable-reference-types).

### <a name="explicit-configuration"></a>Explizite Konfiguration

Eine Eigenschaft, die gemäß der Konvention optional ist, kann so konfiguriert werden, dass Sie wie folgt erforderlich ist:

#### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

**_

## <a name="column-collations"></a>Spalten Sortierungen

> [!NOTE]
> Dieses Feature wurde in EF Core 5.0 eingeführt.

Eine Sortierung kann für Textspalten definiert werden, um zu bestimmen, wie Sie verglichen und sortiert werden. Der folgende Code Ausschnitt konfiguriert z. b. eine SQL Server Spalte so, dass die Groß-/Kleinschreibung nicht beachtet wird:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

Wenn alle Spalten in einer Datenbank eine bestimmte Sortierung verwenden müssen, definieren Sie stattdessen die Sortierung auf Datenbankebene.

Allgemeine Informationen zur EF Core Unterstützung für-Sortierungen finden Sie auf der [Dokumentationsseite der Sortierung](xref:core/miscellaneous/collations-and-case-sensitivity).

## <a name="column-comments"></a>Spalten Kommentare

Sie können einen beliebigen Textkommentar festlegen, der für die Daten Bank Spalte festgelegt wird, sodass Sie das Schema in der Datenbank dokumentieren können:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

> [!NOTE]
> Das Festlegen von Kommentaren über Daten Anmerkungen wurde in EF Core 5,0 eingeführt.

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnComment.cs?name=ColumnComment&highlight=4)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnComment.cs?name=ColumnComment&highlight=5)]

_**
