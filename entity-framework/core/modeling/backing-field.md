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
# <a name="backing-fields"></a><span data-ttu-id="e664b-102">Unterstützungsfelder</span><span class="sxs-lookup"><span data-stu-id="e664b-102">Backing Fields</span></span>

<span data-ttu-id="e664b-103">Mit Sicherungsfeldern kann EF anstelle einer Eigenschaft in ein Feld lesen und/oder schreiben.</span><span class="sxs-lookup"><span data-stu-id="e664b-103">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="e664b-104">Dies kann nützlich sein, wenn die Kapselung in der Klasse verwendet wird, um die Verwendung und/oder Verbesserung der Semantik rund um den Zugriff auf die Daten durch Anwendungscode einzuschränken, aber der Wert sollte aus der Datenbank gelesen und/oder in die Datenbank geschrieben werden, ohne diese Einschränkungen/Verbesserungen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="e664b-104">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="basic-configuration"></a><span data-ttu-id="e664b-105">Basiskonfiguration</span><span class="sxs-lookup"><span data-stu-id="e664b-105">Basic configuration</span></span>

<span data-ttu-id="e664b-106">Gemäß der Konvention werden die folgenden Felder als Sicherungsfelder für eine bestimmte Eigenschaft (in Derrangreihenfolge aufgeführt) ermittelt.</span><span class="sxs-lookup"><span data-stu-id="e664b-106">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

<span data-ttu-id="e664b-107">Im folgenden Beispiel `Url` wird die Eigenschaft `_url` so konfiguriert, dass sie als Sicherungsfeld verfügt:</span><span class="sxs-lookup"><span data-stu-id="e664b-107">In the following sample, the `Url` property is configured to have `_url` as its backing field:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="e664b-108">Beachten Sie, dass Sicherungsfelder nur für Eigenschaften ermittelt werden, die im Modell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="e664b-108">Note that backing fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="e664b-109">Weitere Informationen dazu, welche Eigenschaften im Modell enthalten sind, finden Sie unter [Einschließen & Ausschließen von Eigenschaften](included-properties.md).</span><span class="sxs-lookup"><span data-stu-id="e664b-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

<span data-ttu-id="e664b-110">Sie können Sicherungsfelder auch mithilfe einer Datenanmerkung oder der Fluent-API konfigurieren, z. B. wenn der Feldname nicht den oben genannten Konventionen entspricht:</span><span class="sxs-lookup"><span data-stu-id="e664b-110">You can also configure backing fields by using a Data Annotation or the Fluent API, e.g. if the field name doesn't correspond to the above conventions:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="e664b-111">Datenanmerkungen</span><span class="sxs-lookup"><span data-stu-id="e664b-111">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[<span data-ttu-id="e664b-112">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="e664b-112">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a><span data-ttu-id="e664b-113">Feld- und Grundstückszugang</span><span class="sxs-lookup"><span data-stu-id="e664b-113">Field and property access</span></span>

<span data-ttu-id="e664b-114">Standardmäßig liest und schreibt EF immer in das Sicherungsfeld - vorausgesetzt, es wurde ordnungsgemäß konfiguriert - und verwendet die Eigenschaft nie.</span><span class="sxs-lookup"><span data-stu-id="e664b-114">By default, EF will always read and write to the backing field - assuming one has been properly configured - and will never use the property.</span></span> <span data-ttu-id="e664b-115">EF unterstützt jedoch auch andere Zugriffsmuster.</span><span class="sxs-lookup"><span data-stu-id="e664b-115">However, EF also supports other access patterns.</span></span> <span data-ttu-id="e664b-116">Im folgenden Beispiel wird EF beispielsweise angewiesen, nur während der Materialisierung in das Sicherungsfeld zu schreiben und die Eigenschaft in allen anderen Fällen zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="e664b-116">For example, the following sample instructs EF to write to the backing field only while materializing, and to use the property in all other cases:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

<span data-ttu-id="e664b-117">Der vollständige Satz der unterstützten Optionen finden Sie in der [PropertyAccessMode-Enumere.](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)</span><span class="sxs-lookup"><span data-stu-id="e664b-117">See the [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the complete set of supported options.</span></span>

> [!NOTE]
> <span data-ttu-id="e664b-118">Mit EF Core 3.0 wurde der `PreferFieldDuringConstruction` Standardzugriffsmodus für Eigenschaften von in `PreferField`geändert.</span><span class="sxs-lookup"><span data-stu-id="e664b-118">With EF Core 3.0, the default property access mode changed from `PreferFieldDuringConstruction` to `PreferField`.</span></span>

## <a name="field-only-properties"></a><span data-ttu-id="e664b-119">Nur-Feldeigenschaften</span><span class="sxs-lookup"><span data-stu-id="e664b-119">Field-only properties</span></span>

<span data-ttu-id="e664b-120">Sie können auch eine konzeptionelle Eigenschaft in Ihrem Modell erstellen, die keine entsprechende CLR-Eigenschaft in der Entitätsklasse besitzt, sondern stattdessen ein Feld zum Speichern der Daten in der Entität verwendet.</span><span class="sxs-lookup"><span data-stu-id="e664b-120">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="e664b-121">Dies unterscheidet sich von [Shadow Properties](shadow-properties.md), bei dem die Daten im Änderungstracker und nicht im CLR-Typ der Entität gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="e664b-121">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker, rather than in the entity's CLR type.</span></span> <span data-ttu-id="e664b-122">Nur-Feld-Eigenschaften werden häufig verwendet, wenn die Entitätsklasse Methoden anstelle von Eigenschaften verwendet, um Werte ab/set zu machen, oder wenn Felder im Domänenmodell überhaupt nicht verfügbar gemacht werden sollten (z. B. Primärschlüssel).</span><span class="sxs-lookup"><span data-stu-id="e664b-122">Field-only properties are commonly used when the entity class uses methods instead of properties to get/set values, or in cases where fields shouldn't be exposed at all in the domain model (e.g. primary keys).</span></span>

<span data-ttu-id="e664b-123">Sie können eine reine Feldeigenschaft konfigurieren, `Property(...)` indem Sie einen Namen in der API bereitstellen:</span><span class="sxs-lookup"><span data-stu-id="e664b-123">You can configure a field-only property by providing a name in the `Property(...)` API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="e664b-124">EF versucht, eine CLR-Eigenschaft mit dem angegebenen Namen oder ein Feld zu finden, wenn keine Eigenschaft gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="e664b-124">EF will attempt to find a CLR property with the given name, or a field if a property isn't found.</span></span> <span data-ttu-id="e664b-125">Wenn weder eine Eigenschaft noch ein Feld gefunden werden, wird stattdessen eine Schatteneigenschaft eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="e664b-125">If neither a property nor a field are found, a shadow property will be set up instead.</span></span>

<span data-ttu-id="e664b-126">Möglicherweise müssen Sie auf eine reine Feldeigenschaft aus LINQ-Abfragen verweisen, aber solche Felder sind in der Regel privat.</span><span class="sxs-lookup"><span data-stu-id="e664b-126">You may need to refer to a field-only property from LINQ queries, but such fields are typically private.</span></span> <span data-ttu-id="e664b-127">Sie können `EF.Property(...)` die Methode in einer LINQ-Abfrage verwenden, um auf das Feld zu verweisen:</span><span class="sxs-lookup"><span data-stu-id="e664b-127">You can use the `EF.Property(...)` method in a LINQ query to refer to the field:</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
