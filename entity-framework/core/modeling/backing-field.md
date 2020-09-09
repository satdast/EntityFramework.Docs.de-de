---
title: Unterstützungs Felder-EF Core
description: Konfigurieren von Unterstützungs Feldern für Eigenschaften in einem Entity Framework Core Modell
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: 36fc64efa08f2cdeb7005e3fc6335298f2426bde
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617573"
---
# <a name="backing-fields"></a>Unterstützungsfelder

Mit Unterstützungs Feldern kann EF anstelle einer Eigenschaft ein Feld lesen und/oder in ein Feld schreiben. Dies kann hilfreich sein, wenn die Kapselung in der-Klasse verwendet wird, um die Verwendung von zu beschränken und/oder die Semantik für den Zugriff auf die Daten durch den Anwendungscode zu verbessern. der Wert sollte jedoch aus der Datenbank gelesen und/oder in diese geschrieben werden, ohne dass diese Einschränkungen/Erweiterungen verwendet werden.

## <a name="basic-configuration"></a>Basiskonfiguration

Gemäß der Konvention werden die folgenden Felder als Sicherungs Felder für eine bestimmte Eigenschaft (aufgelistet in der Rangfolge) erkannt. 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

Im folgenden Beispiel wird die- `Url` Eigenschaft so konfiguriert, dass Sie als dahinter liegendes `_url` Feld hat:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

Beachten Sie, dass Unterstützungs Felder nur für Eigenschaften erkannt werden, die im Modell enthalten sind. Weitere Informationen zu den Eigenschaften, die im Modell enthalten sind, finden Sie unter [einschließen & Ausschließen von Eigenschaften](xref:core/modeling/entity-properties).

Sie können auch Unterstützungs Felder konfigurieren, indem Sie eine Daten Anmerkung (verfügbar in efcore 5,0) oder die fließende API verwenden, z. b. wenn der Feldname nicht den obigen Konventionen entspricht:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a>Feld-und Eigenschaften Zugriff

Standardmäßig liest EF immer das dahinter liegende Feld, wobei angenommen wird, dass ein ordnungsgemäß konfiguriert wurde, und verwendet niemals die-Eigenschaft. EF unterstützt jedoch auch andere Zugriffsmuster. Das folgende Beispiel weist EF z. b. an, nur beim Materialisieren in das Unterstützungs Feld zu schreiben und die-Eigenschaft in allen anderen Fällen zu verwenden:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

Alle unterstützten Optionen finden Sie in der [propertyaccessmode-Enumeration](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) .

> [!NOTE]
> Bei EF Core 3,0 wurde der standardmäßige Eigenschaften Zugriffsmodus von `PreferFieldDuringConstruction` in geändert `PreferField` .

## <a name="field-only-properties"></a>Feld-only-Eigenschaften

Sie können auch eine konzeptionelle Eigenschaft in Ihrem Modell erstellen, die nicht über eine entsprechende CLR-Eigenschaft in der Entitäts Klasse verfügt, sondern stattdessen ein Feld zum Speichern der Daten in der Entität verwendet. Dies unterscheidet sich von den [Schatten Eigenschaften](xref:core/modeling/shadow-properties), bei denen die Daten in der Änderungs Protokollierung und nicht im CLR-Typ der Entität gespeichert werden. Feld-only-Eigenschaften werden häufig verwendet, wenn die Entitäts Klasse Methoden anstelle von Eigenschaften verwendet, um Werte zu erhalten/festzulegen, oder in Fällen, in denen Felder überhaupt nicht im Domänen Modell (z. b. Primärschlüssel) verfügbar gemacht werden sollen.

Sie können eine nur-Feld-Eigenschaft konfigurieren, indem Sie in der-API einen Namen angeben `Property(...)` :

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF versucht, eine CLR-Eigenschaft mit dem angegebenen Namen oder einem Feld zu finden, wenn eine Eigenschaft nicht gefunden wird. Wenn weder eine Eigenschaft noch ein Feld gefunden wird, wird stattdessen eine Schatten Eigenschaft eingerichtet.

Möglicherweise müssen Sie in LINQ-Abfragen auf eine Eigenschaft vom Typ "Feld" verweisen, diese Felder sind jedoch in der Regel privat. Sie können die- `EF.Property(...)` Methode in einer LINQ-Abfrage verwenden, um auf das-Feld zu verweisen:

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
