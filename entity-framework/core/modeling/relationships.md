---
title: Beziehungen-EF Core
description: Konfigurieren von Beziehungen zwischen Entitäts Typen bei Verwendung von Entity Framework Core
author: AndriySvyryd
ms.date: 11/21/2019
uid: core/modeling/relationships
ms.openlocfilehash: 8d3df109f34c2a77305db1e2be2eea1694d7ad6b
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672764"
---
# <a name="relationships"></a><span data-ttu-id="ca1e1-103">Beziehungen</span><span class="sxs-lookup"><span data-stu-id="ca1e1-103">Relationships</span></span>

<span data-ttu-id="ca1e1-104">Eine Beziehung definiert, wie zwei Entitäten miteinander in Beziehung stehen.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-104">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="ca1e1-105">In einer relationalen Datenbank wird dies durch eine FOREIGN KEY-Einschränkung repräsentiert.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-105">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="ca1e1-106">Die meisten Beispiele in diesem Artikel verwenden eine 1: n-Beziehung, um die Konzepte zu veranschaulichen.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-106">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="ca1e1-107">Beispiele für 1:1-und m:n-Beziehungen finden Sie im Abschnitt [andere Beziehungsmuster](#other-relationship-patterns) am Ende des Artikels.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-107">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="ca1e1-108">Definition von Begriffen</span><span class="sxs-lookup"><span data-stu-id="ca1e1-108">Definition of terms</span></span>

<span data-ttu-id="ca1e1-109">Es gibt eine Reihe von Begriffen, die zum Beschreiben von Beziehungen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-109">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="ca1e1-110">**Abhängige Entität:** Dies ist die Entität, die die Fremdschlüssel Eigenschaften enthält.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-110">**Dependent entity:** This is the entity that contains the foreign key properties.</span></span> <span data-ttu-id="ca1e1-111">Wird manchmal auch als ' Child ' der Beziehung bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-111">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="ca1e1-112">**Prinzipal Entität:** Dies ist die Entität, die die Eigenschaften des primären/Alternativen Schlüssels enthält.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-112">**Principal entity:** This is the entity that contains the primary/alternate key properties.</span></span> <span data-ttu-id="ca1e1-113">Wird manchmal auch als "übergeordnetes Element" der Beziehung bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-113">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="ca1e1-114">**Prinzipal Schlüssel:** Die Eigenschaften, die die Prinzipal Entität eindeutig identifizieren.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-114">**Principal key:** The properties that uniquely identify the principal entity.</span></span> <span data-ttu-id="ca1e1-115">Hierbei kann es sich um den Primärschlüssel oder einen alternativen Schlüssel handeln.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="ca1e1-116">**Fremdschlüssel:** Die Eigenschaften in der abhängigen Entität, die zum Speichern der Prinzipal Schlüsselwerte für die verknüpfte Entität verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-116">**Foreign key:** The properties in the dependent entity that are used to store the principal key values for the related entity.</span></span>

* <span data-ttu-id="ca1e1-117">**Navigations Eigenschaft:** Eine für die Prinzipal-und/oder abhängige Entität definierte Eigenschaft, die auf die verknüpfte Entität verweist.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-117">**Navigation property:** A property defined on the principal and/or dependent entity that references the related entity.</span></span>

  * <span data-ttu-id="ca1e1-118">Auflistungs **Navigations Eigenschaft:** Eine Navigations Eigenschaft, die Verweise auf viele Verwandte Entitäten enthält.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-118">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="ca1e1-119">**Verweis Navigations Eigenschaft:** Eine Navigations Eigenschaft, die einen Verweis auf eine einzelne verknüpfte Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-119">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="ca1e1-120">**Umgekehrte Navigations Eigenschaft:** Bei der Erörterung einer bestimmten Navigations Eigenschaft bezieht sich dieser Begriff auf die Navigations Eigenschaft am anderen Ende der Beziehung.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-120">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>
  
* <span data-ttu-id="ca1e1-121">**Selbst verweisende Beziehung:** Eine Beziehung, in der die abhängigen und die Prinzipal Entitäts Typen identisch sind.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-121">**Self-referencing relationship:** A relationship in which the dependent and the principal entity types are the same.</span></span>

<span data-ttu-id="ca1e1-122">Der folgende Code zeigt eine 1: n-Beziehung zwischen `Blog` und.`Post`</span><span class="sxs-lookup"><span data-stu-id="ca1e1-122">The following code shows a one-to-many relationship between `Blog` and `Post`</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* <span data-ttu-id="ca1e1-123">`Post`ist die abhängige Entität</span><span class="sxs-lookup"><span data-stu-id="ca1e1-123">`Post` is the dependent entity</span></span>

* <span data-ttu-id="ca1e1-124">`Blog`ist die Prinzipal Entität</span><span class="sxs-lookup"><span data-stu-id="ca1e1-124">`Blog` is the principal entity</span></span>

* <span data-ttu-id="ca1e1-125">`Blog.BlogId`ist der Prinzipal Schlüssel (in diesem Fall handelt es sich um einen Primärschlüssel anstelle eines alternativen Schlüssels)</span><span class="sxs-lookup"><span data-stu-id="ca1e1-125">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="ca1e1-126">`Post.BlogId`ist der Fremdschlüssel</span><span class="sxs-lookup"><span data-stu-id="ca1e1-126">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="ca1e1-127">`Post.Blog`ist eine Verweis Navigations Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-127">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="ca1e1-128">`Blog.Posts`ist eine Auflistungs Navigations Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-128">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="ca1e1-129">`Post.Blog`ist die umgekehrte Navigations Eigenschaft von `Blog.Posts` (und umgekehrt).</span><span class="sxs-lookup"><span data-stu-id="ca1e1-129">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

## <a name="conventions"></a><span data-ttu-id="ca1e1-130">Konventionen</span><span class="sxs-lookup"><span data-stu-id="ca1e1-130">Conventions</span></span>

<span data-ttu-id="ca1e1-131">Standardmäßig wird eine Beziehung erstellt, wenn eine Navigations Eigenschaft für einen Typ erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-131">By default, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="ca1e1-132">Eine Eigenschaft wird als Navigations Eigenschaft betrachtet, wenn der Typ, auf den Sie verweist, nicht vom aktuellen Datenbankanbieter als Skalartyp zugeordnet werden kann.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-132">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="ca1e1-133">Die von der Konvention ermittelten Beziehungen richten sich immer nach dem Primärschlüssel der Prinzipal Entität.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-133">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="ca1e1-134">Um einen alternativen Schlüssel als Ziel zu verwenden, muss mithilfe der flüssigen API eine zusätzliche Konfiguration durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-134">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="ca1e1-135">Vollständig definierte Beziehungen</span><span class="sxs-lookup"><span data-stu-id="ca1e1-135">Fully defined relationships</span></span>

<span data-ttu-id="ca1e1-136">Das gängigste Muster für Beziehungen besteht darin, dass für beide Enden der Beziehung Navigations Eigenschaften und eine Fremdschlüssel Eigenschaft definiert sind, die in der abhängigen Entitäts Klasse definiert ist.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-136">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="ca1e1-137">Wenn zwischen zwei Typen ein paar Navigations Eigenschaften gefunden wird, werden diese als umgekehrte Navigations Eigenschaften derselben Beziehung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-137">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="ca1e1-138">Wenn die abhängige Entität eine Eigenschaft mit einem Namen enthält, der mit einem dieser Muster übereinstimmt, wird Sie als Fremdschlüssel konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="ca1e1-138">If the dependent entity contains a property with a name matching one of these patterns then it will be configured as the foreign key:</span></span>
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

<span data-ttu-id="ca1e1-139">In diesem Beispiel werden die markierten Eigenschaften zum Konfigurieren der Beziehung verwendet.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-139">In this example the highlighted properties will be used to configure the relationship.</span></span>

> [!NOTE]
> <span data-ttu-id="ca1e1-140">Wenn die Eigenschaft der Primärschlüssel oder ein Typ ist, der nicht mit dem Prinzipal Schlüssel kompatibel ist, wird Sie nicht als Fremdschlüssel konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-140">If the property is the primary key or is of a type not compatible with the principal key then it won't be configured as the foreign key.</span></span>

> [!NOTE]
> <span data-ttu-id="ca1e1-141">Vor EF Core 3,0 wurde die Eigenschaft mit dem gleichen Namen wie die Prinzipal Schlüsseleigenschaft [ebenfalls als Fremdschlüssel abgeglichen](https://github.com/aspnet/EntityFrameworkCore/issues/13274) .</span><span class="sxs-lookup"><span data-stu-id="ca1e1-141">Before EF Core 3.0 the property named exactly the same as the principal key property [was also matched as the foreign key](https://github.com/aspnet/EntityFrameworkCore/issues/13274)</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="ca1e1-142">Keine Fremdschlüssel Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="ca1e1-142">No foreign key property</span></span>

<span data-ttu-id="ca1e1-143">Es wird empfohlen, eine Fremdschlüssel Eigenschaft in der abhängigen Entitäts Klasse zu definieren, die jedoch nicht erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-143">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="ca1e1-144">Wenn keine Fremdschlüssel Eigenschaft gefunden wird, wird eine [Schatten-Fremdschlüssel Eigenschaft](shadow-properties.md) mit dem Namen `<navigation property name><principal key property name>` oder, `<principal entity name><principal key property name>` Wenn keine Navigation für den abhängigen Typ vorhanden ist, eingefügt.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-144">If no foreign key property is found, a [shadow foreign key property](shadow-properties.md) will be introduced with the name `<navigation property name><principal key property name>` or `<principal entity name><principal key property name>` if no navigation is present on the dependent type.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

<span data-ttu-id="ca1e1-145">In diesem Beispiel ist der Schatten Fremdschlüssel, weil der vorangestellt wird, `BlogId` dass der Navigations Name redundant ist.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-145">In this example the shadow foreign key is `BlogId` because prepending the navigation name would be redundant.</span></span>

> [!NOTE]
> <span data-ttu-id="ca1e1-146">Wenn bereits eine Eigenschaft mit demselben Namen vorhanden ist, wird dem Namen der Schatten Eigenschaft eine Zahl angehängt.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-146">If a property with the same name already exists then the shadow property name will be suffixed with a number.</span></span>

### <a name="single-navigation-property"></a><span data-ttu-id="ca1e1-147">Einzelne Navigations Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="ca1e1-147">Single navigation property</span></span>

<span data-ttu-id="ca1e1-148">Das Einschließen von nur einer Navigations Eigenschaft (keine umgekehrte Navigation und keine Fremdschlüssel Eigenschaft) reicht aus, um eine Beziehung gemäß der Konvention zu definieren.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-148">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="ca1e1-149">Sie können auch eine einzelne Navigations Eigenschaft und eine Fremdschlüssel Eigenschaft haben.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-149">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a><span data-ttu-id="ca1e1-150">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="ca1e1-150">Limitations</span></span>

<span data-ttu-id="ca1e1-151">Wenn mehrere Navigations Eigenschaften zwischen zwei Typen definiert sind (d. h. mehr als nur ein Navigations Paar, das aufeinander zeigen), sind die Beziehungen, die von den Navigations Eigenschaften dargestellt werden, mehrdeutig.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-151">When there are multiple navigation properties defined between two types (that is, more than just one pair of navigations that point to each other) the relationships represented by the navigation properties are ambiguous.</span></span> <span data-ttu-id="ca1e1-152">Sie müssen Sie manuell konfigurieren, um die Mehrdeutigkeit aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-152">You will need to manually configure them to resolve the ambiguity.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="ca1e1-153">Kaskadierendes Delete</span><span class="sxs-lookup"><span data-stu-id="ca1e1-153">Cascade delete</span></span>

<span data-ttu-id="ca1e1-154">Gemäß der Konvention wird CASCADE DELETE für erforderliche Beziehungen und *clientsetnull* für optionale Beziehungen auf *Cascade* festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-154">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="ca1e1-155">*Cascade* bedeutet, dass abhängige Entitäten ebenfalls gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-155">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="ca1e1-156">*Clientsetnull* bedeutet, dass abhängige Entitäten, die nicht in den Arbeitsspeicher geladen werden, unverändert bleiben und manuell gelöscht oder aktualisiert werden müssen, um auf eine gültige Prinzipal Entität zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-156">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="ca1e1-157">Bei Entitäten, die in den Arbeitsspeicher geladen werden, wird EF Core versucht, die Fremdschlüssel Eigenschaften auf NULL festzulegen.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-157">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="ca1e1-158">Den Unterschied zwischen erforderlichen und optionalen Beziehungen finden Sie im Abschnitt [erforderliche und optionale Beziehungen](#required-and-optional-relationships) .</span><span class="sxs-lookup"><span data-stu-id="ca1e1-158">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="ca1e1-159">Weitere Informationen zu den verschiedenen Lösch Verhalten und den von der Konvention verwendeten Standardwerten finden Sie unter [Cascade Delete](../saving/cascade-delete.md) .</span><span class="sxs-lookup"><span data-stu-id="ca1e1-159">See [Cascade Delete](../saving/cascade-delete.md) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="manual-configuration"></a><span data-ttu-id="ca1e1-160">Manuelle Konfiguration</span><span class="sxs-lookup"><span data-stu-id="ca1e1-160">Manual configuration</span></span>

### <a name="fluent-api"></a>[<span data-ttu-id="ca1e1-161">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="ca1e1-161">Fluent API</span></span>](#tab/fluent-api)

<span data-ttu-id="ca1e1-162">Um eine Beziehung in der fließenden API zu konfigurieren, müssen Sie zunächst die Navigations Eigenschaften ermitteln, die die Beziehung bilden.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-162">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="ca1e1-163">`HasOne`oder `HasMany` identifiziert die Navigations Eigenschaft für den Entitätstyp, für den Sie mit der Konfiguration beginnen.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-163">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="ca1e1-164">Anschließend verketten Sie einen Aufrufen von `WithOne` oder `WithMany` , um die umgekehrte Navigation zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-164">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="ca1e1-165">`HasOne`/`WithOne`werden für Verweis Navigations Eigenschaften verwendet und für Auflistungs `HasMany` / `WithMany` Navigations Eigenschaften verwendet.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-165">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotations"></a>[<span data-ttu-id="ca1e1-166">Datenanmerkungen</span><span class="sxs-lookup"><span data-stu-id="ca1e1-166">Data annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="ca1e1-167">Mit den Daten Anmerkungen können Sie konfigurieren, wie Navigations Eigenschaften für die abhängigen und Prinzipal Entitäten gekoppelt werden.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-167">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="ca1e1-168">Dies erfolgt in der Regel, wenn zwischen zwei Entitäts Typen mehr als ein paar Navigations Eigenschaften vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-168">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> <span data-ttu-id="ca1e1-169">Sie können für die Eigenschaften der abhängigen Entität nur [Required] verwenden, um die Eignung der Beziehung zu beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-169">You can only use [Required] on properties on the dependent entity to impact the requiredness of the relationship.</span></span> <span data-ttu-id="ca1e1-170">[Erforderlich] die Navigation von der Prinzipal Entität wird normalerweise ignoriert, kann jedoch dazu führen, dass die Entität zu einer abhängigen Entität wird.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-170">[Required] on the navigation from the principal entity is usually ignored, but it may cause the entity to become the dependent one.</span></span>

> [!NOTE]
> <span data-ttu-id="ca1e1-171">Die Daten Anmerkungen `[ForeignKey]` und `[InverseProperty]` sind im- `System.ComponentModel.DataAnnotations.Schema` Namespace verfügbar.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-171">The data annotations `[ForeignKey]` and `[InverseProperty]` are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span> <span data-ttu-id="ca1e1-172">`[Required]`ist im- `System.ComponentModel.DataAnnotations` Namespace verfügbar.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-172">`[Required]` is available in the `System.ComponentModel.DataAnnotations` namespace.</span></span>

---

### <a name="single-navigation-property"></a><span data-ttu-id="ca1e1-173">Einzelne Navigations Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="ca1e1-173">Single navigation property</span></span>

<span data-ttu-id="ca1e1-174">Wenn Sie nur über eine Navigations Eigenschaft verfügen, gibt es Parameter lose über Ladungen von `WithOne` und `WithMany` .</span><span class="sxs-lookup"><span data-stu-id="ca1e1-174">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="ca1e1-175">Dies gibt an, dass ein Verweis oder eine Auflistung am anderen Ende der Beziehung konzeptionell ist, aber in der Entitäts Klasse ist keine Navigations Eigenschaft enthalten.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-175">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

---

### <a name="configuring-navigation-properties"></a><span data-ttu-id="ca1e1-176">Konfigurieren von Navigations Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="ca1e1-176">Configuring navigation properties</span></span>

<span data-ttu-id="ca1e1-177">Nachdem die Navigations Eigenschaft erstellt wurde, müssen Sie Sie möglicherweise weiter konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-177">After the navigation property has been created, you may need to further configure it.</span></span> <span data-ttu-id="ca1e1-178">In efcore 5,0 wird eine neue, fließende API hinzugefügt, mit der Sie diese Konfiguration durchführen können.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-178">In EFCore 5.0 new Fluent API is added to allow you perform that configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NavigationConfiguration.cs?name=NavigationConfiguration&highlight=7-9)]

[!NOTE] <span data-ttu-id="ca1e1-179">Dieser Befehl kann nicht zum Erstellen einer Navigations Eigenschaft verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-179">This call cannot be used to create a navigation property.</span></span> <span data-ttu-id="ca1e1-180">Sie wird nur verwendet, um eine Navigations Eigenschaft zu konfigurieren, die zuvor durch Definieren einer Beziehung oder einer Konvention erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-180">It is only used to configure a navigation property which has been previously created by defining a relationship or from a convention.</span></span>

### <a name="foreign-key"></a><span data-ttu-id="ca1e1-181">Fremdschlüssel</span><span class="sxs-lookup"><span data-stu-id="ca1e1-181">Foreign key</span></span>

#### <a name="fluent-api-simple-key"></a>[<span data-ttu-id="ca1e1-182">Fließende API (einfacher Schlüssel)</span><span class="sxs-lookup"><span data-stu-id="ca1e1-182">Fluent API (simple key)</span></span>](#tab/fluent-api-simple-key)

<span data-ttu-id="ca1e1-183">Sie können die fließende API verwenden, um zu konfigurieren, welche Eigenschaft als Fremdschlüssel Eigenschaft für eine bestimmte Beziehung verwendet werden soll:</span><span class="sxs-lookup"><span data-stu-id="ca1e1-183">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-key"></a>[<span data-ttu-id="ca1e1-184">Fließende API (zusammengesetzter Schlüssel)</span><span class="sxs-lookup"><span data-stu-id="ca1e1-184">Fluent API (composite key)</span></span>](#tab/fluent-api-composite-key)

<span data-ttu-id="ca1e1-185">Mit der fließend-API können Sie konfigurieren, welche Eigenschaften als zusammengesetzte Fremdschlüssel Eigenschaften für eine bestimmte Beziehung verwendet werden sollen:</span><span class="sxs-lookup"><span data-stu-id="ca1e1-185">You can use the Fluent API to configure which properties should be used as the composite foreign key properties for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-key"></a>[<span data-ttu-id="ca1e1-186">Daten Anmerkungen (einfacher Schlüssel)</span><span class="sxs-lookup"><span data-stu-id="ca1e1-186">Data annotations (simple key)</span></span>](#tab/data-annotations-simple-key)

<span data-ttu-id="ca1e1-187">Mit den Daten Anmerkungen können Sie konfigurieren, welche Eigenschaft als Fremdschlüssel Eigenschaft für eine bestimmte Beziehung verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-187">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="ca1e1-188">Dies erfolgt in der Regel, wenn die Fremdschlüssel Eigenschaft nicht gemäß der Konvention ermittelt wird:</span><span class="sxs-lookup"><span data-stu-id="ca1e1-188">This is typically done when the foreign key property is not discovered by convention:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]  
> <span data-ttu-id="ca1e1-189">Die-Anmerkung `[ForeignKey]` kann für jede Navigations Eigenschaft in der Beziehung platziert werden.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-189">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="ca1e1-190">Die Navigations Eigenschaft in der abhängigen Entitäts Klasse muss nicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-190">It does not need to go on the navigation property in the dependent entity class.</span></span>

> [!NOTE]
> <span data-ttu-id="ca1e1-191">Die Eigenschaft, die mithilfe `[ForeignKey]` von für eine Navigations Eigenschaft angegeben wurde, muss nicht den abhängigen Typ enthalten.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-191">The property specified using `[ForeignKey]` on a navigation property doesn't need to exist the the dependent type.</span></span> <span data-ttu-id="ca1e1-192">In diesem Fall wird der angegebene Name verwendet, um einen Schatten Fremdschlüssel zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-192">In this case the specified name will be used to create a shadow foreign key.</span></span>

---

#### <a name="shadow-foreign-key"></a><span data-ttu-id="ca1e1-193">Schatten-Fremdschlüssel</span><span class="sxs-lookup"><span data-stu-id="ca1e1-193">Shadow foreign key</span></span>

<span data-ttu-id="ca1e1-194">Sie können die Zeichen folgen Überladung von verwenden `HasForeignKey(...)` , um eine Schatten Eigenschaft als Fremdschlüssel zu konfigurieren (Weitere Informationen finden Sie unter [Schatten Eigenschaften](shadow-properties.md) ).</span><span class="sxs-lookup"><span data-stu-id="ca1e1-194">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](shadow-properties.md) for more information).</span></span> <span data-ttu-id="ca1e1-195">Es wird empfohlen, die Schatten Eigenschaft explizit dem Modell hinzuzufügen, bevor Sie als Fremdschlüssel verwendet wird (wie unten gezeigt).</span><span class="sxs-lookup"><span data-stu-id="ca1e1-195">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a><span data-ttu-id="ca1e1-196">Name der FOREIGN KEY-Einschränkung</span><span class="sxs-lookup"><span data-stu-id="ca1e1-196">Foreign key constraint name</span></span>

<span data-ttu-id="ca1e1-197">Gemäß der Konvention werden Foreign Key-Einschränkungen, wenn Sie eine relationale Datenbank als Ziel haben, FK_ benannt <dependent type name> _<principal type name>_ <foreign key property name> .</span><span class="sxs-lookup"><span data-stu-id="ca1e1-197">By convention, when targeting a relational database, foreign key constraints are named FK_<dependent type name>_<principal type name>_<foreign key property name>.</span></span> <span data-ttu-id="ca1e1-198">Für zusammengesetzte Fremdschlüssel <foreign key property name> wird zu einer durch Trennzeichen getrennten Liste von Fremdschlüssel Eigenschaftsnamen.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-198">For composite foreign keys <foreign key property name> becomes an underscore separated list of foreign key property names.</span></span>

<span data-ttu-id="ca1e1-199">Sie können den Einschränkungs Namen auch wie folgt konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="ca1e1-199">You can also configure the constraint name as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a><span data-ttu-id="ca1e1-200">Ohne Navigations Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="ca1e1-200">Without navigation property</span></span>

<span data-ttu-id="ca1e1-201">Sie müssen nicht unbedingt eine Navigations Eigenschaft bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-201">You don't necessarily need to provide a navigation property.</span></span> <span data-ttu-id="ca1e1-202">Sie können auf einer Seite der Beziehung einfach einen Fremdschlüssel bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-202">You can simply provide a foreign key on one side of the relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a><span data-ttu-id="ca1e1-203">Prinzipal Schlüssel</span><span class="sxs-lookup"><span data-stu-id="ca1e1-203">Principal key</span></span>

<span data-ttu-id="ca1e1-204">Wenn Sie möchten, dass der Fremdschlüssel auf eine andere Eigenschaft als den Primärschlüssel verweist, können Sie mit der fließend-API die Prinzipal Schlüsseleigenschaft für die Beziehung konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-204">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="ca1e1-205">Die Eigenschaft, die Sie als Prinzipal Schlüssel konfigurieren, wird automatisch als [alternativer Schlüssel](alternate-keys.md)eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-205">The property that you configure as the principal key will automatically be setup as an [alternate key](alternate-keys.md).</span></span>

#### <a name="simple-key"></a>[<span data-ttu-id="ca1e1-206">Einfache Taste</span><span class="sxs-lookup"><span data-stu-id="ca1e1-206">Simple key</span></span>](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-key"></a>[<span data-ttu-id="ca1e1-207">Zusammengesetzte Schlüssel</span><span class="sxs-lookup"><span data-stu-id="ca1e1-207">Composite key</span></span>](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]  
> <span data-ttu-id="ca1e1-208">Die Reihenfolge, in der Sie die Prinzipal Schlüsseleigenschaften angeben, muss mit der Reihenfolge identisch sein, in der Sie für den Fremdschlüssel angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-208">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

---

### <a name="required-and-optional-relationships"></a><span data-ttu-id="ca1e1-209">Erforderliche und optionale Beziehungen</span><span class="sxs-lookup"><span data-stu-id="ca1e1-209">Required and optional relationships</span></span>

<span data-ttu-id="ca1e1-210">Mit der fließend-API können Sie konfigurieren, ob die Beziehung erforderlich oder optional ist.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-210">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="ca1e1-211">Letztendlich steuert dies, ob die Fremdschlüssel Eigenschaft erforderlich oder optional ist.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-211">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="ca1e1-212">Dies ist besonders nützlich, wenn Sie einen Schatten Zustands-Fremdschlüssel verwenden.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-212">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="ca1e1-213">Wenn Sie über eine Fremdschlüssel Eigenschaft in der Entitäts Klasse verfügen, wird die Eignung der Beziehung abhängig davon bestimmt, ob die Fremdschlüssel Eigenschaft erforderlich oder optional ist (Weitere Informationen finden Sie unter [erforderliche und optionale Eigenschaften](required-optional.md) ).</span><span class="sxs-lookup"><span data-stu-id="ca1e1-213">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](required-optional.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> <span data-ttu-id="ca1e1-214">Durch `IsRequired(false)` das Aufrufen von wird auch die Fremdschlüssel Eigenschaft optional, sofern Sie nicht anderweitig konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-214">Calling `IsRequired(false)` also makes the foreign key property optional unless it's configured otherwise.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="ca1e1-215">Kaskadierendes Delete</span><span class="sxs-lookup"><span data-stu-id="ca1e1-215">Cascade delete</span></span>

<span data-ttu-id="ca1e1-216">Sie können die fließende API verwenden, um das kaskadierte Lösch Verhalten für eine bestimmte Beziehung explizit zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-216">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="ca1e1-217">Eine ausführliche Erläuterung der einzelnen Optionen finden Sie unter [Cascade Delete](../saving/cascade-delete.md) .</span><span class="sxs-lookup"><span data-stu-id="ca1e1-217">See [Cascade Delete](../saving/cascade-delete.md) for a detailed discussion of each option.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a><span data-ttu-id="ca1e1-218">Andere Beziehungsmuster</span><span class="sxs-lookup"><span data-stu-id="ca1e1-218">Other relationship patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="ca1e1-219">1:1</span><span class="sxs-lookup"><span data-stu-id="ca1e1-219">One-to-one</span></span>

<span data-ttu-id="ca1e1-220">Eine zu 1 Beziehung hat eine Verweis Navigations Eigenschaft auf beiden Seiten.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-220">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="ca1e1-221">Sie folgen denselben Konventionen wie 1: n-Beziehungen, aber es wird ein eindeutiger Index für die Fremdschlüssel Eigenschaft eingeführt, um sicherzustellen, dass nur ein abhängiger mit den einzelnen Prinzipalen verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-221">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]  
> <span data-ttu-id="ca1e1-222">EF wählt eine der Entitäten aus, die abhängig von der Fähigkeit, eine Fremdschlüssel Eigenschaft zu erkennen, abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-222">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="ca1e1-223">Wenn die falsche Entität als abhängig ausgewählt wird, können Sie diese mithilfe der flüssigen API korrigieren.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-223">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="ca1e1-224">Beim Konfigurieren der Beziehung mit der flüssigen API verwenden Sie die `HasOne` -Methode und die- `WithOne` Methode.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-224">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="ca1e1-225">Wenn Sie den Fremdschlüssel konfigurieren, müssen Sie den abhängigen Entitätstyp angeben. Beachten Sie den generischen Parameter, der `HasForeignKey` in der nachfolgenden Auflistung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-225">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="ca1e1-226">In einer 1: n-Beziehung ist klar, dass die Entität mit der Verweis Navigation die abhängige ist und die mit der Auflistung der Prinzipal ist.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-226">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="ca1e1-227">Dies ist jedoch in einer eins-zu-eins-Beziehung nicht der Fall, daher muss Sie explizit definiert werden.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-227">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

### <a name="many-to-many"></a><span data-ttu-id="ca1e1-228">n:n</span><span class="sxs-lookup"><span data-stu-id="ca1e1-228">Many-to-many</span></span>

<span data-ttu-id="ca1e1-229">M:n-Beziehungen ohne Entitäts Klasse zur Darstellung der jointabelle werden noch nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-229">Many-to-many relationships without an entity class to represent the join table are not yet supported.</span></span> <span data-ttu-id="ca1e1-230">Sie können jedoch eine m:n-Beziehung darstellen, indem Sie eine Entitäts Klasse für die jointabelle einschließen und zwei separate 1: n-Beziehungen Mapping.</span><span class="sxs-lookup"><span data-stu-id="ca1e1-230">However, you can represent a many-to-many relationship by including an entity class for the join table and mapping two separate one-to-many relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11-14,16-19,39-46)]
