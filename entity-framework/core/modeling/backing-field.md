---
title: Sicherungsfelder - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: d6faf7c0a07c7b855c1ce402ae29e19c80b9d3b4
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434174"
---
# <a name="backing-fields"></a>Unterstützungsfelder

Mit Sicherungsfeldern kann EF anstelle einer Eigenschaft in ein Feld lesen und/oder schreiben. Dies kann nützlich sein, wenn die Kapselung in der Klasse verwendet wird, um die Verwendung und/oder Verbesserung der Semantik rund um den Zugriff auf die Daten durch Anwendungscode einzuschränken, aber der Wert sollte aus der Datenbank gelesen und/oder in die Datenbank geschrieben werden, ohne diese Einschränkungen/Verbesserungen zu verwenden.

## <a name="basic-configuration"></a>Basiskonfiguration

Gemäß der Konvention werden die folgenden Felder als Sicherungsfelder für eine bestimmte Eigenschaft (in Derrangreihenfolge aufgeführt) ermittelt. 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

Im folgenden Beispiel `Url` wird die Eigenschaft `_url` so konfiguriert, dass sie als Sicherungsfeld verfügt:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

Beachten Sie, dass Sicherungsfelder nur für Eigenschaften ermittelt werden, die im Modell enthalten sind. Weitere Informationen dazu, welche Eigenschaften im Modell enthalten sind, finden Sie unter [Einschließen & Ausschließen von Eigenschaften](included-properties.md).

Sie können Sicherungsfelder auch mithilfe einer Datenanmerkung oder der Fluent-API konfigurieren, z. B. wenn der Feldname nicht den oben genannten Konventionen entspricht:

### <a name="data-annotations"></a>[Datenanmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a>Feld- und Grundstückszugang

Standardmäßig liest und schreibt EF immer in das Sicherungsfeld - vorausgesetzt, es wurde ordnungsgemäß konfiguriert - und verwendet die Eigenschaft nie. EF unterstützt jedoch auch andere Zugriffsmuster. Im folgenden Beispiel wird EF beispielsweise angewiesen, nur während der Materialisierung in das Sicherungsfeld zu schreiben und die Eigenschaft in allen anderen Fällen zu verwenden:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

Der vollständige Satz der unterstützten Optionen finden Sie in der [PropertyAccessMode-Enumere.](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)

> [!NOTE]
> Mit EF Core 3.0 wurde der `PreferFieldDuringConstruction` Standardzugriffsmodus für Eigenschaften von in `PreferField`geändert.

## <a name="field-only-properties"></a>Nur-Feldeigenschaften

Sie können auch eine konzeptionelle Eigenschaft in Ihrem Modell erstellen, die keine entsprechende CLR-Eigenschaft in der Entitätsklasse besitzt, sondern stattdessen ein Feld zum Speichern der Daten in der Entität verwendet. Dies unterscheidet sich von [Shadow Properties](shadow-properties.md), bei dem die Daten im Änderungstracker und nicht im CLR-Typ der Entität gespeichert werden. Nur-Feld-Eigenschaften werden häufig verwendet, wenn die Entitätsklasse Methoden anstelle von Eigenschaften verwendet, um Werte ab/set zu machen, oder wenn Felder im Domänenmodell überhaupt nicht verfügbar gemacht werden sollten (z. B. Primärschlüssel).

Sie können eine reine Feldeigenschaft konfigurieren, `Property(...)` indem Sie einen Namen in der API bereitstellen:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

EF versucht, eine CLR-Eigenschaft mit dem angegebenen Namen oder ein Feld zu finden, wenn keine Eigenschaft gefunden wird. Wenn weder eine Eigenschaft noch ein Feld gefunden werden, wird stattdessen eine Schatteneigenschaft eingerichtet.

Möglicherweise müssen Sie auf eine reine Feldeigenschaft aus LINQ-Abfragen verweisen, aber solche Felder sind in der Regel privat. Sie können `EF.Property(...)` die Methode in einer LINQ-Abfrage verwenden, um auf das Feld zu verweisen:

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
