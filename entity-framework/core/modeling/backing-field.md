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
# <a name="backing-fields"></a><span data-ttu-id="c5cbd-103">Unterstützungsfelder</span><span class="sxs-lookup"><span data-stu-id="c5cbd-103">Backing Fields</span></span>

<span data-ttu-id="c5cbd-104">Mit Unterstützungs Feldern kann EF anstelle einer Eigenschaft ein Feld lesen und/oder in ein Feld schreiben.</span><span class="sxs-lookup"><span data-stu-id="c5cbd-104">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="c5cbd-105">Dies kann hilfreich sein, wenn die Kapselung in der-Klasse verwendet wird, um die Verwendung von zu beschränken und/oder die Semantik für den Zugriff auf die Daten durch den Anwendungscode zu verbessern. der Wert sollte jedoch aus der Datenbank gelesen und/oder in diese geschrieben werden, ohne dass diese Einschränkungen/Erweiterungen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c5cbd-105">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="basic-configuration"></a><span data-ttu-id="c5cbd-106">Basiskonfiguration</span><span class="sxs-lookup"><span data-stu-id="c5cbd-106">Basic configuration</span></span>

<span data-ttu-id="c5cbd-107">Gemäß der Konvention werden die folgenden Felder als Sicherungs Felder für eine bestimmte Eigenschaft (aufgelistet in der Rangfolge) erkannt.</span><span class="sxs-lookup"><span data-stu-id="c5cbd-107">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

<span data-ttu-id="c5cbd-108">Im folgenden Beispiel wird die- `Url` Eigenschaft so konfiguriert, dass Sie als dahinter liegendes `_url` Feld hat:</span><span class="sxs-lookup"><span data-stu-id="c5cbd-108">In the following sample, the `Url` property is configured to have `_url` as its backing field:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="c5cbd-109">Beachten Sie, dass Unterstützungs Felder nur für Eigenschaften erkannt werden, die im Modell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="c5cbd-109">Note that backing fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="c5cbd-110">Weitere Informationen zu den Eigenschaften, die im Modell enthalten sind, finden Sie unter [einschließen & Ausschließen von Eigenschaften](xref:core/modeling/entity-properties).</span><span class="sxs-lookup"><span data-stu-id="c5cbd-110">For more information on which properties are included in the model, see [Including & Excluding Properties](xref:core/modeling/entity-properties).</span></span>

<span data-ttu-id="c5cbd-111">Sie können auch Unterstützungs Felder konfigurieren, indem Sie eine Daten Anmerkung (verfügbar in efcore 5,0) oder die fließende API verwenden, z. b. wenn der Feldname nicht den obigen Konventionen entspricht:</span><span class="sxs-lookup"><span data-stu-id="c5cbd-111">You can also configure backing fields by using a Data Annotation (available in EFCore 5.0) or the Fluent API, e.g. if the field name doesn't correspond to the above conventions:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="c5cbd-112">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="c5cbd-112">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[<span data-ttu-id="c5cbd-113">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="c5cbd-113">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a><span data-ttu-id="c5cbd-114">Feld-und Eigenschaften Zugriff</span><span class="sxs-lookup"><span data-stu-id="c5cbd-114">Field and property access</span></span>

<span data-ttu-id="c5cbd-115">Standardmäßig liest EF immer das dahinter liegende Feld, wobei angenommen wird, dass ein ordnungsgemäß konfiguriert wurde, und verwendet niemals die-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="c5cbd-115">By default, EF will always read and write to the backing field - assuming one has been properly configured - and will never use the property.</span></span> <span data-ttu-id="c5cbd-116">EF unterstützt jedoch auch andere Zugriffsmuster.</span><span class="sxs-lookup"><span data-stu-id="c5cbd-116">However, EF also supports other access patterns.</span></span> <span data-ttu-id="c5cbd-117">Das folgende Beispiel weist EF z. b. an, nur beim Materialisieren in das Unterstützungs Feld zu schreiben und die-Eigenschaft in allen anderen Fällen zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="c5cbd-117">For example, the following sample instructs EF to write to the backing field only while materializing, and to use the property in all other cases:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

<span data-ttu-id="c5cbd-118">Alle unterstützten Optionen finden Sie in der [propertyaccessmode-Enumeration](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) .</span><span class="sxs-lookup"><span data-stu-id="c5cbd-118">See the [PropertyAccessMode enum](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the complete set of supported options.</span></span>

> [!NOTE]
> <span data-ttu-id="c5cbd-119">Bei EF Core 3,0 wurde der standardmäßige Eigenschaften Zugriffsmodus von `PreferFieldDuringConstruction` in geändert `PreferField` .</span><span class="sxs-lookup"><span data-stu-id="c5cbd-119">With EF Core 3.0, the default property access mode changed from `PreferFieldDuringConstruction` to `PreferField`.</span></span>

## <a name="field-only-properties"></a><span data-ttu-id="c5cbd-120">Feld-only-Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="c5cbd-120">Field-only properties</span></span>

<span data-ttu-id="c5cbd-121">Sie können auch eine konzeptionelle Eigenschaft in Ihrem Modell erstellen, die nicht über eine entsprechende CLR-Eigenschaft in der Entitäts Klasse verfügt, sondern stattdessen ein Feld zum Speichern der Daten in der Entität verwendet.</span><span class="sxs-lookup"><span data-stu-id="c5cbd-121">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="c5cbd-122">Dies unterscheidet sich von den [Schatten Eigenschaften](xref:core/modeling/shadow-properties), bei denen die Daten in der Änderungs Protokollierung und nicht im CLR-Typ der Entität gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="c5cbd-122">This is different from [Shadow Properties](xref:core/modeling/shadow-properties), where the data is stored in the change tracker, rather than in the entity's CLR type.</span></span> <span data-ttu-id="c5cbd-123">Feld-only-Eigenschaften werden häufig verwendet, wenn die Entitäts Klasse Methoden anstelle von Eigenschaften verwendet, um Werte zu erhalten/festzulegen, oder in Fällen, in denen Felder überhaupt nicht im Domänen Modell (z. b. Primärschlüssel) verfügbar gemacht werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c5cbd-123">Field-only properties are commonly used when the entity class uses methods instead of properties to get/set values, or in cases where fields shouldn't be exposed at all in the domain model (e.g. primary keys).</span></span>

<span data-ttu-id="c5cbd-124">Sie können eine nur-Feld-Eigenschaft konfigurieren, indem Sie in der-API einen Namen angeben `Property(...)` :</span><span class="sxs-lookup"><span data-stu-id="c5cbd-124">You can configure a field-only property by providing a name in the `Property(...)` API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="c5cbd-125">EF versucht, eine CLR-Eigenschaft mit dem angegebenen Namen oder einem Feld zu finden, wenn eine Eigenschaft nicht gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="c5cbd-125">EF will attempt to find a CLR property with the given name, or a field if a property isn't found.</span></span> <span data-ttu-id="c5cbd-126">Wenn weder eine Eigenschaft noch ein Feld gefunden wird, wird stattdessen eine Schatten Eigenschaft eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="c5cbd-126">If neither a property nor a field are found, a shadow property will be set up instead.</span></span>

<span data-ttu-id="c5cbd-127">Möglicherweise müssen Sie in LINQ-Abfragen auf eine Eigenschaft vom Typ "Feld" verweisen, diese Felder sind jedoch in der Regel privat.</span><span class="sxs-lookup"><span data-stu-id="c5cbd-127">You may need to refer to a field-only property from LINQ queries, but such fields are typically private.</span></span> <span data-ttu-id="c5cbd-128">Sie können die- `EF.Property(...)` Methode in einer LINQ-Abfrage verwenden, um auf das-Feld zu verweisen:</span><span class="sxs-lookup"><span data-stu-id="c5cbd-128">You can use the `EF.Property(...)` method in a LINQ query to refer to the field:</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
