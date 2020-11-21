---
title: Beziehungen-EF Core
description: Konfigurieren von Beziehungen zwischen Entitäts Typen bei Verwendung von Entity Framework Core
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/relationships
ms.openlocfilehash: 716c034bd73d831996b727da18c2c1f83dd55290
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003262"
---
# <a name="relationships"></a><span data-ttu-id="eb07b-103">Beziehungen</span><span class="sxs-lookup"><span data-stu-id="eb07b-103">Relationships</span></span>

<span data-ttu-id="eb07b-104">Eine Beziehung definiert, wie zwei Entitäten miteinander in Beziehung stehen.</span><span class="sxs-lookup"><span data-stu-id="eb07b-104">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="eb07b-105">In einer relationalen Datenbank wird dies durch eine FOREIGN KEY-Einschränkung repräsentiert.</span><span class="sxs-lookup"><span data-stu-id="eb07b-105">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="eb07b-106">Die meisten Beispiele in diesem Artikel verwenden eine 1: n-Beziehung, um die Konzepte zu veranschaulichen.</span><span class="sxs-lookup"><span data-stu-id="eb07b-106">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="eb07b-107">Beispiele für 1:1-und m:n-Beziehungen finden Sie im Abschnitt [andere Beziehungsmuster](#other-relationship-patterns) am Ende des Artikels.</span><span class="sxs-lookup"><span data-stu-id="eb07b-107">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="eb07b-108">Definition von Begriffen</span><span class="sxs-lookup"><span data-stu-id="eb07b-108">Definition of terms</span></span>

<span data-ttu-id="eb07b-109">Es gibt eine Reihe von Begriffen, die zum Beschreiben von Beziehungen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="eb07b-109">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="eb07b-110">**Abhängige Entität:** Dies ist die Entität, die die Fremdschlüssel Eigenschaften enthält.</span><span class="sxs-lookup"><span data-stu-id="eb07b-110">**Dependent entity:** This is the entity that contains the foreign key properties.</span></span> <span data-ttu-id="eb07b-111">Wird manchmal auch als ' Child ' der Beziehung bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="eb07b-111">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="eb07b-112">**Prinzipal Entität:** Dies ist die Entität, die die Eigenschaften des primären/Alternativen Schlüssels enthält.</span><span class="sxs-lookup"><span data-stu-id="eb07b-112">**Principal entity:** This is the entity that contains the primary/alternate key properties.</span></span> <span data-ttu-id="eb07b-113">Wird manchmal auch als "übergeordnetes Element" der Beziehung bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="eb07b-113">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="eb07b-114">**Prinzipal Schlüssel:** Die Eigenschaften, die die Prinzipal Entität eindeutig identifizieren.</span><span class="sxs-lookup"><span data-stu-id="eb07b-114">**Principal key:** The properties that uniquely identify the principal entity.</span></span> <span data-ttu-id="eb07b-115">Hierbei kann es sich um den Primärschlüssel oder einen alternativen Schlüssel handeln.</span><span class="sxs-lookup"><span data-stu-id="eb07b-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="eb07b-116">**Fremdschlüssel:** Die Eigenschaften in der abhängigen Entität, die zum Speichern der Prinzipal Schlüsselwerte für die verknüpfte Entität verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="eb07b-116">**Foreign key:** The properties in the dependent entity that are used to store the principal key values for the related entity.</span></span>

* <span data-ttu-id="eb07b-117">**Navigations Eigenschaft:** Eine für die Prinzipal-und/oder abhängige Entität definierte Eigenschaft, die auf die verknüpfte Entität verweist.</span><span class="sxs-lookup"><span data-stu-id="eb07b-117">**Navigation property:** A property defined on the principal and/or dependent entity that references the related entity.</span></span>

  * <span data-ttu-id="eb07b-118">Auflistungs **Navigations Eigenschaft:** Eine Navigations Eigenschaft, die Verweise auf viele Verwandte Entitäten enthält.</span><span class="sxs-lookup"><span data-stu-id="eb07b-118">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="eb07b-119">**Verweis Navigations Eigenschaft:** Eine Navigations Eigenschaft, die einen Verweis auf eine einzelne verknüpfte Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="eb07b-119">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="eb07b-120">**Umgekehrte Navigations Eigenschaft:** Bei der Erörterung einer bestimmten Navigations Eigenschaft bezieht sich dieser Begriff auf die Navigations Eigenschaft am anderen Ende der Beziehung.</span><span class="sxs-lookup"><span data-stu-id="eb07b-120">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>
  
* <span data-ttu-id="eb07b-121">**Selbst verweisende Beziehung:** Eine Beziehung, in der die abhängigen und die Prinzipal Entitäts Typen identisch sind.</span><span class="sxs-lookup"><span data-stu-id="eb07b-121">**Self-referencing relationship:** A relationship in which the dependent and the principal entity types are the same.</span></span>

<span data-ttu-id="eb07b-122">Der folgende Code zeigt eine 1: n-Beziehung zwischen `Blog` und. `Post`</span><span class="sxs-lookup"><span data-stu-id="eb07b-122">The following code shows a one-to-many relationship between `Blog` and `Post`</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* <span data-ttu-id="eb07b-123">`Post` ist die abhängige Entität</span><span class="sxs-lookup"><span data-stu-id="eb07b-123">`Post` is the dependent entity</span></span>

* <span data-ttu-id="eb07b-124">`Blog` ist die Prinzipal Entität</span><span class="sxs-lookup"><span data-stu-id="eb07b-124">`Blog` is the principal entity</span></span>

* <span data-ttu-id="eb07b-125">`Blog.BlogId` ist der Prinzipal Schlüssel (in diesem Fall handelt es sich um einen Primärschlüssel anstelle eines alternativen Schlüssels)</span><span class="sxs-lookup"><span data-stu-id="eb07b-125">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="eb07b-126">`Post.BlogId` ist der Fremdschlüssel</span><span class="sxs-lookup"><span data-stu-id="eb07b-126">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="eb07b-127">`Post.Blog` ist eine Verweis Navigations Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="eb07b-127">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="eb07b-128">`Blog.Posts` ist eine Auflistungs Navigations Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="eb07b-128">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="eb07b-129">`Post.Blog` ist die umgekehrte Navigations Eigenschaft von `Blog.Posts` (und umgekehrt).</span><span class="sxs-lookup"><span data-stu-id="eb07b-129">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

## <a name="conventions"></a><span data-ttu-id="eb07b-130">Konventionen</span><span class="sxs-lookup"><span data-stu-id="eb07b-130">Conventions</span></span>

<span data-ttu-id="eb07b-131">Standardmäßig wird eine Beziehung erstellt, wenn eine Navigations Eigenschaft für einen Typ erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="eb07b-131">By default, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="eb07b-132">Eine Eigenschaft wird als Navigations Eigenschaft betrachtet, wenn der Typ, auf den Sie verweist, nicht vom aktuellen Datenbankanbieter als Skalartyp zugeordnet werden kann.</span><span class="sxs-lookup"><span data-stu-id="eb07b-132">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="eb07b-133">Die von der Konvention ermittelten Beziehungen richten sich immer nach dem Primärschlüssel der Prinzipal Entität.</span><span class="sxs-lookup"><span data-stu-id="eb07b-133">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="eb07b-134">Um einen alternativen Schlüssel als Ziel zu verwenden, muss mithilfe der flüssigen API eine zusätzliche Konfiguration durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="eb07b-134">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="eb07b-135">Vollständig definierte Beziehungen</span><span class="sxs-lookup"><span data-stu-id="eb07b-135">Fully defined relationships</span></span>

<span data-ttu-id="eb07b-136">Das gängigste Muster für Beziehungen besteht darin, dass für beide Enden der Beziehung Navigations Eigenschaften und eine Fremdschlüssel Eigenschaft definiert sind, die in der abhängigen Entitäts Klasse definiert ist.</span><span class="sxs-lookup"><span data-stu-id="eb07b-136">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="eb07b-137">Wenn zwischen zwei Typen ein paar Navigations Eigenschaften gefunden wird, werden diese als umgekehrte Navigations Eigenschaften derselben Beziehung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="eb07b-137">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="eb07b-138">Wenn die abhängige Entität eine Eigenschaft mit einem Namen enthält, der mit einem dieser Muster übereinstimmt, wird Sie als Fremdschlüssel konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="eb07b-138">If the dependent entity contains a property with a name matching one of these patterns then it will be configured as the foreign key:</span></span>
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

<span data-ttu-id="eb07b-139">In diesem Beispiel werden die markierten Eigenschaften zum Konfigurieren der Beziehung verwendet.</span><span class="sxs-lookup"><span data-stu-id="eb07b-139">In this example the highlighted properties will be used to configure the relationship.</span></span>

> [!NOTE]
> <span data-ttu-id="eb07b-140">Wenn die Eigenschaft der Primärschlüssel oder ein Typ ist, der nicht mit dem Prinzipal Schlüssel kompatibel ist, wird Sie nicht als Fremdschlüssel konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="eb07b-140">If the property is the primary key or is of a type not compatible with the principal key then it won't be configured as the foreign key.</span></span>

> [!NOTE]
> <span data-ttu-id="eb07b-141">Vor EF Core 3,0 wurde die Eigenschaft mit dem gleichen Namen wie die Prinzipal Schlüsseleigenschaft [ebenfalls als Fremdschlüssel abgeglichen](https://github.com/dotnet/efcore/issues/13274) .</span><span class="sxs-lookup"><span data-stu-id="eb07b-141">Before EF Core 3.0 the property named exactly the same as the principal key property [was also matched as the foreign key](https://github.com/dotnet/efcore/issues/13274)</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="eb07b-142">Keine Fremdschlüssel Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="eb07b-142">No foreign key property</span></span>

<span data-ttu-id="eb07b-143">Es wird empfohlen, eine Fremdschlüssel Eigenschaft in der abhängigen Entitäts Klasse zu definieren, die jedoch nicht erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="eb07b-143">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="eb07b-144">Wenn keine Fremdschlüssel Eigenschaft gefunden wird, wird eine [Schatten-Fremdschlüssel Eigenschaft](xref:core/modeling/shadow-properties) mit dem Namen `<navigation property name><principal key property name>` oder, `<principal entity name><principal key property name>` Wenn keine Navigation für den abhängigen Typ vorhanden ist, eingefügt.</span><span class="sxs-lookup"><span data-stu-id="eb07b-144">If no foreign key property is found, a [shadow foreign key property](xref:core/modeling/shadow-properties) will be introduced with the name `<navigation property name><principal key property name>` or `<principal entity name><principal key property name>` if no navigation is present on the dependent type.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

<span data-ttu-id="eb07b-145">In diesem Beispiel ist der Schatten Fremdschlüssel, weil der vorangestellt wird, `BlogId` dass der Navigations Name redundant ist.</span><span class="sxs-lookup"><span data-stu-id="eb07b-145">In this example the shadow foreign key is `BlogId` because prepending the navigation name would be redundant.</span></span>

> [!NOTE]
> <span data-ttu-id="eb07b-146">Wenn bereits eine Eigenschaft mit demselben Namen vorhanden ist, wird dem Namen der Schatten Eigenschaft eine Zahl angehängt.</span><span class="sxs-lookup"><span data-stu-id="eb07b-146">If a property with the same name already exists then the shadow property name will be suffixed with a number.</span></span>

### <a name="single-navigation-property"></a><span data-ttu-id="eb07b-147">Einzelne Navigations Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="eb07b-147">Single navigation property</span></span>

<span data-ttu-id="eb07b-148">Das Einschließen von nur einer Navigations Eigenschaft (keine umgekehrte Navigation und keine Fremdschlüssel Eigenschaft) reicht aus, um eine Beziehung gemäß der Konvention zu definieren.</span><span class="sxs-lookup"><span data-stu-id="eb07b-148">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="eb07b-149">Sie können auch eine einzelne Navigations Eigenschaft und eine Fremdschlüssel Eigenschaft haben.</span><span class="sxs-lookup"><span data-stu-id="eb07b-149">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a><span data-ttu-id="eb07b-150">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="eb07b-150">Limitations</span></span>

<span data-ttu-id="eb07b-151">Wenn mehrere Navigations Eigenschaften zwischen zwei Typen definiert sind (d. h. mehr als nur ein Navigations Paar, das aufeinander zeigen), sind die Beziehungen, die von den Navigations Eigenschaften dargestellt werden, mehrdeutig.</span><span class="sxs-lookup"><span data-stu-id="eb07b-151">When there are multiple navigation properties defined between two types (that is, more than just one pair of navigations that point to each other) the relationships represented by the navigation properties are ambiguous.</span></span> <span data-ttu-id="eb07b-152">Sie müssen Sie manuell konfigurieren, um die Mehrdeutigkeit aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="eb07b-152">You will need to manually configure them to resolve the ambiguity.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="eb07b-153">Kaskadierendes Delete</span><span class="sxs-lookup"><span data-stu-id="eb07b-153">Cascade delete</span></span>

<span data-ttu-id="eb07b-154">Gemäß der Konvention wird CASCADE DELETE für erforderliche Beziehungen und *clientsetnull* für optionale Beziehungen auf *Cascade* festgelegt.</span><span class="sxs-lookup"><span data-stu-id="eb07b-154">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="eb07b-155">*Cascade* bedeutet, dass abhängige Entitäten ebenfalls gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="eb07b-155">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="eb07b-156">*Clientsetnull* bedeutet, dass abhängige Entitäten, die nicht in den Arbeitsspeicher geladen werden, unverändert bleiben und manuell gelöscht oder aktualisiert werden müssen, um auf eine gültige Prinzipal Entität zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="eb07b-156">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="eb07b-157">Bei Entitäten, die in den Arbeitsspeicher geladen werden, wird EF Core versucht, die Fremdschlüssel Eigenschaften auf NULL festzulegen.</span><span class="sxs-lookup"><span data-stu-id="eb07b-157">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="eb07b-158">Den Unterschied zwischen erforderlichen und optionalen Beziehungen finden Sie im Abschnitt [erforderliche und optionale Beziehungen](#required-and-optional-relationships) .</span><span class="sxs-lookup"><span data-stu-id="eb07b-158">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="eb07b-159">Weitere Informationen zu den verschiedenen Lösch Verhalten und den von der Konvention verwendeten Standardwerten finden Sie unter [Cascade Delete](xref:core/saving/cascade-delete) .</span><span class="sxs-lookup"><span data-stu-id="eb07b-159">See [Cascade Delete](xref:core/saving/cascade-delete) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="manual-configuration"></a><span data-ttu-id="eb07b-160">Manuelle Konfiguration</span><span class="sxs-lookup"><span data-stu-id="eb07b-160">Manual configuration</span></span>

### <a name="fluent-api"></a>[<span data-ttu-id="eb07b-161">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="eb07b-161">Fluent API</span></span>](#tab/fluent-api)

<span data-ttu-id="eb07b-162">Um eine Beziehung in der fließenden API zu konfigurieren, müssen Sie zunächst die Navigations Eigenschaften ermitteln, die die Beziehung bilden.</span><span class="sxs-lookup"><span data-stu-id="eb07b-162">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="eb07b-163">`HasOne` oder `HasMany` identifiziert die Navigations Eigenschaft für den Entitätstyp, für den Sie mit der Konfiguration beginnen.</span><span class="sxs-lookup"><span data-stu-id="eb07b-163">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="eb07b-164">Anschließend verketten Sie einen Aufrufen von `WithOne` oder `WithMany` , um die umgekehrte Navigation zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="eb07b-164">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="eb07b-165">`HasOne`/`WithOne`werden für Verweis Navigations Eigenschaften verwendet und für Auflistungs `HasMany` / `WithMany` Navigations Eigenschaften verwendet.</span><span class="sxs-lookup"><span data-stu-id="eb07b-165">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotations"></a>[<span data-ttu-id="eb07b-166">Datenanmerkungen</span><span class="sxs-lookup"><span data-stu-id="eb07b-166">Data annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="eb07b-167">Mit den Daten Anmerkungen können Sie konfigurieren, wie Navigations Eigenschaften für die abhängigen und Prinzipal Entitäten gekoppelt werden.</span><span class="sxs-lookup"><span data-stu-id="eb07b-167">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="eb07b-168">Dies erfolgt in der Regel, wenn zwischen zwei Entitäts Typen mehr als ein paar Navigations Eigenschaften vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="eb07b-168">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> <span data-ttu-id="eb07b-169">Sie können für die Eigenschaften der abhängigen Entität nur [Required] verwenden, um die Eignung der Beziehung zu beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="eb07b-169">You can only use [Required] on properties on the dependent entity to impact the requiredness of the relationship.</span></span> <span data-ttu-id="eb07b-170">[Erforderlich] die Navigation von der Prinzipal Entität wird normalerweise ignoriert, kann jedoch dazu führen, dass die Entität zu einer abhängigen Entität wird.</span><span class="sxs-lookup"><span data-stu-id="eb07b-170">[Required] on the navigation from the principal entity is usually ignored, but it may cause the entity to become the dependent one.</span></span>

> [!NOTE]
> <span data-ttu-id="eb07b-171">Die Daten Anmerkungen `[ForeignKey]` und `[InverseProperty]` sind im- `System.ComponentModel.DataAnnotations.Schema` Namespace verfügbar.</span><span class="sxs-lookup"><span data-stu-id="eb07b-171">The data annotations `[ForeignKey]` and `[InverseProperty]` are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span> <span data-ttu-id="eb07b-172">`[Required]` ist im- `System.ComponentModel.DataAnnotations` Namespace verfügbar.</span><span class="sxs-lookup"><span data-stu-id="eb07b-172">`[Required]` is available in the `System.ComponentModel.DataAnnotations` namespace.</span></span>

---

### <a name="single-navigation-property"></a><span data-ttu-id="eb07b-173">Einzelne Navigations Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="eb07b-173">Single navigation property</span></span>

<span data-ttu-id="eb07b-174">Wenn Sie nur über eine Navigations Eigenschaft verfügen, gibt es Parameter lose über Ladungen von `WithOne` und `WithMany` .</span><span class="sxs-lookup"><span data-stu-id="eb07b-174">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="eb07b-175">Dies gibt an, dass ein Verweis oder eine Auflistung am anderen Ende der Beziehung konzeptionell ist, aber in der Entitäts Klasse ist keine Navigations Eigenschaft enthalten.</span><span class="sxs-lookup"><span data-stu-id="eb07b-175">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

---

### <a name="configuring-navigation-properties"></a><span data-ttu-id="eb07b-176">Konfigurieren von Navigations Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="eb07b-176">Configuring navigation properties</span></span>

> [!NOTE]
> <span data-ttu-id="eb07b-177">Diese Funktion wurde in EF Core 5,0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="eb07b-177">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="eb07b-178">Nachdem die Navigations Eigenschaft erstellt wurde, müssen Sie Sie möglicherweise weiter konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="eb07b-178">After the navigation property has been created, you may need to further configure it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NavigationConfiguration.cs?name=NavigationConfiguration&highlight=7-9)]

> [!NOTE]
> <span data-ttu-id="eb07b-179">Dieser Befehl kann nicht zum Erstellen einer Navigations Eigenschaft verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="eb07b-179">This call cannot be used to create a navigation property.</span></span> <span data-ttu-id="eb07b-180">Sie wird nur verwendet, um eine Navigations Eigenschaft zu konfigurieren, die zuvor durch Definieren einer Beziehung oder einer Konvention erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="eb07b-180">It is only used to configure a navigation property which has been previously created by defining a relationship or from a convention.</span></span>

### <a name="foreign-key"></a><span data-ttu-id="eb07b-181">Fremdschlüssel</span><span class="sxs-lookup"><span data-stu-id="eb07b-181">Foreign key</span></span>

#### <a name="fluent-api-simple-key"></a>[<span data-ttu-id="eb07b-182">Fließende API (einfacher Schlüssel)</span><span class="sxs-lookup"><span data-stu-id="eb07b-182">Fluent API (simple key)</span></span>](#tab/fluent-api-simple-key)

<span data-ttu-id="eb07b-183">Sie können die fließende API verwenden, um zu konfigurieren, welche Eigenschaft als Fremdschlüssel Eigenschaft für eine bestimmte Beziehung verwendet werden soll:</span><span class="sxs-lookup"><span data-stu-id="eb07b-183">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-key"></a>[<span data-ttu-id="eb07b-184">Fließende API (zusammengesetzter Schlüssel)</span><span class="sxs-lookup"><span data-stu-id="eb07b-184">Fluent API (composite key)</span></span>](#tab/fluent-api-composite-key)

<span data-ttu-id="eb07b-185">Mit der fließend-API können Sie konfigurieren, welche Eigenschaften als zusammengesetzte Fremdschlüssel Eigenschaften für eine bestimmte Beziehung verwendet werden sollen:</span><span class="sxs-lookup"><span data-stu-id="eb07b-185">You can use the Fluent API to configure which properties should be used as the composite foreign key properties for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-key"></a>[<span data-ttu-id="eb07b-186">Daten Anmerkungen (einfacher Schlüssel)</span><span class="sxs-lookup"><span data-stu-id="eb07b-186">Data annotations (simple key)</span></span>](#tab/data-annotations-simple-key)

<span data-ttu-id="eb07b-187">Mit den Daten Anmerkungen können Sie konfigurieren, welche Eigenschaft als Fremdschlüssel Eigenschaft für eine bestimmte Beziehung verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="eb07b-187">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="eb07b-188">Dies erfolgt in der Regel, wenn die Fremdschlüssel Eigenschaft nicht gemäß der Konvention ermittelt wird:</span><span class="sxs-lookup"><span data-stu-id="eb07b-188">This is typically done when the foreign key property is not discovered by convention:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]  
> <span data-ttu-id="eb07b-189">Die-Anmerkung `[ForeignKey]` kann für jede Navigations Eigenschaft in der Beziehung platziert werden.</span><span class="sxs-lookup"><span data-stu-id="eb07b-189">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="eb07b-190">Die Navigations Eigenschaft in der abhängigen Entitäts Klasse muss nicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="eb07b-190">It does not need to go on the navigation property in the dependent entity class.</span></span>

> [!NOTE]
> <span data-ttu-id="eb07b-191">Die mit `[ForeignKey]` einer Navigations Eigenschaft angegebene Eigenschaft muss nicht im abhängigen Typ vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="eb07b-191">The property specified using `[ForeignKey]` on a navigation property doesn't need to exist on the dependent type.</span></span> <span data-ttu-id="eb07b-192">In diesem Fall wird der angegebene Name verwendet, um einen Schatten Fremdschlüssel zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="eb07b-192">In this case the specified name will be used to create a shadow foreign key.</span></span>

---

#### <a name="shadow-foreign-key"></a><span data-ttu-id="eb07b-193">Schatten-Fremdschlüssel</span><span class="sxs-lookup"><span data-stu-id="eb07b-193">Shadow foreign key</span></span>

<span data-ttu-id="eb07b-194">Sie können die Zeichen folgen Überladung von verwenden `HasForeignKey(...)` , um eine Schatten Eigenschaft als Fremdschlüssel zu konfigurieren (Weitere Informationen finden Sie unter [Schatten Eigenschaften](xref:core/modeling/shadow-properties) ).</span><span class="sxs-lookup"><span data-stu-id="eb07b-194">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](xref:core/modeling/shadow-properties) for more information).</span></span> <span data-ttu-id="eb07b-195">Es wird empfohlen, die Schatten Eigenschaft explizit dem Modell hinzuzufügen, bevor Sie als Fremdschlüssel verwendet wird (wie unten gezeigt).</span><span class="sxs-lookup"><span data-stu-id="eb07b-195">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a><span data-ttu-id="eb07b-196">Name der FOREIGN KEY-Einschränkung</span><span class="sxs-lookup"><span data-stu-id="eb07b-196">Foreign key constraint name</span></span>

<span data-ttu-id="eb07b-197">Gemäß der Konvention werden Foreign Key-Einschränkungen beim Ziel einer relationalen Datenbank als "FK" benannt \_ \<dependent type name> \_ \<principal type name> \_ \<foreign key property name> .</span><span class="sxs-lookup"><span data-stu-id="eb07b-197">By convention, when targeting a relational database, foreign key constraints are named FK\_\<dependent type name>\_\<principal type name>\_\<foreign key property name>.</span></span> <span data-ttu-id="eb07b-198">Bei zusammengesetzten Fremdschlüsseln \<foreign key property name> wird zu einer durch Trennzeichen getrennten Liste von Fremdschlüssel Eigenschaftsnamen.</span><span class="sxs-lookup"><span data-stu-id="eb07b-198">For composite foreign keys, \<foreign key property name> becomes an underscore separated list of foreign key property names.</span></span>

<span data-ttu-id="eb07b-199">Sie können den Einschränkungs Namen auch wie folgt konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="eb07b-199">You can also configure the constraint name as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a><span data-ttu-id="eb07b-200">Ohne Navigations Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="eb07b-200">Without navigation property</span></span>

<span data-ttu-id="eb07b-201">Sie müssen nicht unbedingt eine Navigations Eigenschaft bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="eb07b-201">You don't necessarily need to provide a navigation property.</span></span> <span data-ttu-id="eb07b-202">Sie können auf einer Seite der Beziehung einfach einen Fremdschlüssel bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="eb07b-202">You can simply provide a foreign key on one side of the relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a><span data-ttu-id="eb07b-203">Prinzipal Schlüssel</span><span class="sxs-lookup"><span data-stu-id="eb07b-203">Principal key</span></span>

<span data-ttu-id="eb07b-204">Wenn Sie möchten, dass der Fremdschlüssel auf eine andere Eigenschaft als den Primärschlüssel verweist, können Sie mit der fließend-API die Prinzipal Schlüsseleigenschaft für die Beziehung konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="eb07b-204">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="eb07b-205">Die Eigenschaft, die Sie als Prinzipal Schlüssel konfigurieren, wird automatisch als [alternativer Schlüssel](xref:core/modeling/keys#alternate-keys)eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="eb07b-205">The property that you configure as the principal key will automatically be setup as an [alternate key](xref:core/modeling/keys#alternate-keys).</span></span>

#### <a name="simple-key"></a>[<span data-ttu-id="eb07b-206">Einfache Taste</span><span class="sxs-lookup"><span data-stu-id="eb07b-206">Simple key</span></span>](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-key"></a>[<span data-ttu-id="eb07b-207">Zusammengesetzte Schlüssel</span><span class="sxs-lookup"><span data-stu-id="eb07b-207">Composite key</span></span>](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]  
> <span data-ttu-id="eb07b-208">Die Reihenfolge, in der Sie die Prinzipal Schlüsseleigenschaften angeben, muss mit der Reihenfolge identisch sein, in der Sie für den Fremdschlüssel angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="eb07b-208">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

---

### <a name="required-and-optional-relationships"></a><span data-ttu-id="eb07b-209">Erforderliche und optionale Beziehungen</span><span class="sxs-lookup"><span data-stu-id="eb07b-209">Required and optional relationships</span></span>

<span data-ttu-id="eb07b-210">Mit der fließend-API können Sie konfigurieren, ob die Beziehung erforderlich oder optional ist.</span><span class="sxs-lookup"><span data-stu-id="eb07b-210">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="eb07b-211">Letztendlich steuert dies, ob die Fremdschlüssel Eigenschaft erforderlich oder optional ist.</span><span class="sxs-lookup"><span data-stu-id="eb07b-211">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="eb07b-212">Dies ist besonders nützlich, wenn Sie einen Schatten Zustands-Fremdschlüssel verwenden.</span><span class="sxs-lookup"><span data-stu-id="eb07b-212">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="eb07b-213">Wenn Sie über eine Fremdschlüssel Eigenschaft in der Entitäts Klasse verfügen, wird die Eignung der Beziehung abhängig davon bestimmt, ob die Fremdschlüssel Eigenschaft erforderlich oder optional ist (Weitere Informationen finden Sie unter [erforderliche und optionale Eigenschaften](xref:core/modeling/entity-properties#required-and-optional-properties) ).</span><span class="sxs-lookup"><span data-stu-id="eb07b-213">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](xref:core/modeling/entity-properties#required-and-optional-properties) for more information).</span></span>

<span data-ttu-id="eb07b-214">Die Fremdschlüssel Eigenschaften befinden sich auf dem abhängigen Entitätstyp. Wenn Sie daher als erforderlich konfiguriert werden, bedeutet dies, dass für jede abhängige Entität eine entsprechende Prinzipal Entität erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="eb07b-214">The foreign key properties are located on the dependent entity type, so if they are configured as required it means that every dependent entity is required to have a corresponding principal entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> <span data-ttu-id="eb07b-215">Durch `IsRequired(false)` das Aufrufen von wird auch die Fremdschlüssel Eigenschaft optional, sofern Sie nicht anderweitig konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="eb07b-215">Calling `IsRequired(false)` also makes the foreign key property optional unless it's configured otherwise.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="eb07b-216">Kaskadierendes Delete</span><span class="sxs-lookup"><span data-stu-id="eb07b-216">Cascade delete</span></span>

<span data-ttu-id="eb07b-217">Sie können die fließende API verwenden, um das kaskadierte Lösch Verhalten für eine bestimmte Beziehung explizit zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="eb07b-217">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="eb07b-218">Eine ausführliche Erläuterung der einzelnen Optionen finden Sie unter [Cascade Delete](xref:core/saving/cascade-delete) .</span><span class="sxs-lookup"><span data-stu-id="eb07b-218">See [Cascade Delete](xref:core/saving/cascade-delete) for a detailed discussion of each option.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a><span data-ttu-id="eb07b-219">Andere Beziehungsmuster</span><span class="sxs-lookup"><span data-stu-id="eb07b-219">Other relationship patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="eb07b-220">1:1</span><span class="sxs-lookup"><span data-stu-id="eb07b-220">One-to-one</span></span>

<span data-ttu-id="eb07b-221">Eine zu 1 Beziehung hat eine Verweis Navigations Eigenschaft auf beiden Seiten.</span><span class="sxs-lookup"><span data-stu-id="eb07b-221">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="eb07b-222">Sie folgen denselben Konventionen wie 1: n-Beziehungen, aber es wird ein eindeutiger Index für die Fremdschlüssel Eigenschaft eingeführt, um sicherzustellen, dass nur ein abhängiger mit den einzelnen Prinzipalen verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="eb07b-222">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]  
> <span data-ttu-id="eb07b-223">EF wählt eine der Entitäten aus, die abhängig von der Fähigkeit, eine Fremdschlüssel Eigenschaft zu erkennen, abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="eb07b-223">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="eb07b-224">Wenn die falsche Entität als abhängig ausgewählt wird, können Sie diese mithilfe der flüssigen API korrigieren.</span><span class="sxs-lookup"><span data-stu-id="eb07b-224">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="eb07b-225">Beim Konfigurieren der Beziehung mit der flüssigen API verwenden Sie die `HasOne` -Methode und die- `WithOne` Methode.</span><span class="sxs-lookup"><span data-stu-id="eb07b-225">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="eb07b-226">Wenn Sie den Fremdschlüssel konfigurieren, müssen Sie den abhängigen Entitätstyp angeben. Beachten Sie den generischen Parameter, der `HasForeignKey` in der nachfolgenden Auflistung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="eb07b-226">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="eb07b-227">In einer 1: n-Beziehung ist klar, dass die Entität mit der Verweis Navigation die abhängige ist und die mit der Auflistung der Prinzipal ist.</span><span class="sxs-lookup"><span data-stu-id="eb07b-227">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="eb07b-228">Dies ist jedoch in einer eins-zu-eins-Beziehung nicht der Fall, daher muss Sie explizit definiert werden.</span><span class="sxs-lookup"><span data-stu-id="eb07b-228">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

<span data-ttu-id="eb07b-229">Die abhängige Seite wird standardmäßig als optional betrachtet, kann jedoch als erforderlich konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="eb07b-229">The dependent side is considered optional by default, but can be configured as required.</span></span> <span data-ttu-id="eb07b-230">EF überprüft jedoch nicht, ob eine abhängige Entität bereitgestellt wurde, sodass diese Konfiguration nur einen Unterschied macht, wenn die Daten Bank Zuordnung die Durchsetzung zulässt.</span><span class="sxs-lookup"><span data-stu-id="eb07b-230">However EF will not validate whether a dependent entity was provided, so this configuration will only make a difference when the database mapping allows it to be enforced.</span></span> <span data-ttu-id="eb07b-231">Ein häufiges Szenario hierfür sind Referenztypen, die standardmäßig Tabellen Aufteilung verwenden.</span><span class="sxs-lookup"><span data-stu-id="eb07b-231">A common scenario for this are reference owned types that use table splitting by default.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=Required&highlight=11-12)]

<span data-ttu-id="eb07b-232">Mit dieser Konfiguration werden die Spalten, die entsprechen, `ShippingAddress` in der Datenbank als nicht auf NULL festlegbar gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="eb07b-232">With this configuration the columns corresponding to `ShippingAddress` will be marked as non-nullable in the database.</span></span>

> [!NOTE]
> <span data-ttu-id="eb07b-233">Wenn Sie Verweis Typen verwenden, die keine [NULL-Werte](/dotnet/csharp/nullable-references) zulassen, ist das Aufrufen von `IsRequired` nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="eb07b-233">If you are using [non-nullable reference types](/dotnet/csharp/nullable-references) calling `IsRequired` is not necessary.</span></span>

> [!NOTE]
> <span data-ttu-id="eb07b-234">Die Möglichkeit, zu konfigurieren, ob die abhängige erforderlich ist, wurde in EF Core 5,0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="eb07b-234">The ability to configure whether the dependent is required was introduced in EF Core 5.0.</span></span>

### <a name="many-to-many"></a><span data-ttu-id="eb07b-235">M:n</span><span class="sxs-lookup"><span data-stu-id="eb07b-235">Many-to-many</span></span>

<span data-ttu-id="eb07b-236">Für m:n-Beziehungen ist auf beiden Seiten eine Auflistungs Navigations Eigenschaft erforderlich.</span><span class="sxs-lookup"><span data-stu-id="eb07b-236">Many to many relationships require a collection navigation property on both sides.</span></span> <span data-ttu-id="eb07b-237">Sie werden gemäß der Konvention wie andere Beziehungstypen erkannt.</span><span class="sxs-lookup"><span data-stu-id="eb07b-237">They will be discovered by convention like other types of relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=ManyToManyShared)]

<span data-ttu-id="eb07b-238">Die Art und Weise, in der diese Beziehung in der Datenbank implementiert wird, ist eine jointabelle, die Fremdschlüssel für `Post` und enthält `Tag` .</span><span class="sxs-lookup"><span data-stu-id="eb07b-238">The way this relationship is implemented in the database is by a join table that contains foreign keys to both `Post` and `Tag`.</span></span> <span data-ttu-id="eb07b-239">Dies ist beispielsweise der Fall, den EF in einer relationalen Datenbank für das obige Modell erstellt.</span><span class="sxs-lookup"><span data-stu-id="eb07b-239">For example this is what EF will create in a relational database for the above model.</span></span>

```sql
CREATE TABLE [Posts] (
    [PostId] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([PostId])
);

CREATE TABLE [Tags] (
    [TagId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_Tags] PRIMARY KEY ([TagId])
);

CREATE TABLE [PostTag] (
    [PostsId] int NOT NULL,
    [TagsId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_PostTag] PRIMARY KEY ([PostsId], [TagsId]),
    CONSTRAINT [FK_PostTag_Posts_PostsId] FOREIGN KEY ([PostsId]) REFERENCES [Posts] ([PostId]) ON DELETE CASCADE,
    CONSTRAINT [FK_PostTag_Tags_TagsId] FOREIGN KEY ([TagsId]) REFERENCES [Tags] ([TagId]) ON DELETE CASCADE
);
```

<span data-ttu-id="eb07b-240">Intern erstellt EF einen Entitätstyp, der die jointabelle darstellt, die als joinentitätstyp bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="eb07b-240">Internally, EF creates an entity type to represent the join table that will be referred to as the join entity type.</span></span> <span data-ttu-id="eb07b-241">Hierfür gibt es keinen spezifischen CLR-Typ, der verwendet werden kann `Dictionary<string, object>` .</span><span class="sxs-lookup"><span data-stu-id="eb07b-241">There is no specific CLR type that can be used for this, so `Dictionary<string, object>` is used.</span></span> <span data-ttu-id="eb07b-242">Mehrere m:n-Beziehungen können im Modell vorhanden sein. Daher muss dem joinentitätstyp ein eindeutiger Name zugewiesen werden, in diesem Fall `PostTag` .</span><span class="sxs-lookup"><span data-stu-id="eb07b-242">More than one many-to-many relationships can exist in the model, therefore the join entity type must be given a unique name, in this case `PostTag`.</span></span> <span data-ttu-id="eb07b-243">Die Funktion, die dies zulässt, wird als Entitätstyp mit gemeinsamer Typbezeichnung bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="eb07b-243">The feature that allows this is called shared-type entity type.</span></span>

<span data-ttu-id="eb07b-244">Die m:n-Navigationen werden als "Skip Navigationen" bezeichnet, da Sie den joinentitätstyp effektiv überspringen.</span><span class="sxs-lookup"><span data-stu-id="eb07b-244">The many to many navigations are called skip navigations as they effectively skip over the join entity type.</span></span> <span data-ttu-id="eb07b-245">Wenn Sie die Massen Konfiguration verwenden, können alle Skip-Navigationen aus abgerufen werden `GetSkipNavigations` .</span><span class="sxs-lookup"><span data-stu-id="eb07b-245">If you are employing bulk configuration all skip navigations can be obtained from `GetSkipNavigations`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Metadata)]

<span data-ttu-id="eb07b-246">Es kommt häufig vor, dass die Konfiguration auf den Join-Entitätstyp angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="eb07b-246">It is common to apply configuration to the join entity type.</span></span> <span data-ttu-id="eb07b-247">Diese Aktion kann mithilfe von durchgeführt werden `UsingEntity` .</span><span class="sxs-lookup"><span data-stu-id="eb07b-247">This action can be accomplished via `UsingEntity`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=SharedConfiguration)]

<span data-ttu-id="eb07b-248">[Modell Ausgangsdaten](xref:core/modeling/data-seeding) können mithilfe anonymer Typen für den Join-Entitätstyp bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="eb07b-248">[Model seed data](xref:core/modeling/data-seeding) can be provided for the join entity type by using anonymous types.</span></span> <span data-ttu-id="eb07b-249">Sie können die modelldebugansicht überprüfen, um die von der Konvention erstellten Eigenschaftsnamen zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="eb07b-249">You can examine the model debug view to determine the property names created by convention.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Seeding)]

<span data-ttu-id="eb07b-250">Zusätzliche Daten können im Join-Entitätstyp gespeichert werden, aber hierfür ist es am besten, einen beständigen CLR-Typ zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="eb07b-250">Additional data can be stored in the join entity type, but for this it's best to create a bespoke CLR type.</span></span> <span data-ttu-id="eb07b-251">Beim Konfigurieren der Beziehung mit einem benutzerdefinierten Join-Entitätstyp müssen beide Fremdschlüssel explizit angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="eb07b-251">When configuring the relationship with a custom join entity type both foreign keys need to be specified explicitly.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyPayload.cs?name=ManyToManyPayload)]

> [!NOTE]
> <span data-ttu-id="eb07b-252">Die Möglichkeit zum Konfigurieren von m:n-Beziehungen wurde in EF Core 5,0 eingeführt. verwenden Sie für die vorherige Version die folgende Vorgehensweise.</span><span class="sxs-lookup"><span data-stu-id="eb07b-252">The ability to configure many-to-many relationships was introduced in EF Core 5.0, for previous version use the following approach.</span></span>

<span data-ttu-id="eb07b-253">Sie können auch eine m:n-Beziehung darstellen, indem Sie einfach den Join-Entitätstyp hinzufügen und zwei separate 1: n-Beziehungen Mapping.</span><span class="sxs-lookup"><span data-stu-id="eb07b-253">You can also represent a many-to-many relationship by just adding the join entity type and mapping two separate one-to-many relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=16-19,21-24)]

> [!NOTE]
> <span data-ttu-id="eb07b-254">Der Gerüstbau für m:n-Beziehungen anhand der Datenbank wird noch nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="eb07b-254">Support for scaffolding many-to-many relationships from the database is not yet added.</span></span> <span data-ttu-id="eb07b-255">Siehe [Nachverfolgung von Issue](https://github.com/dotnet/efcore/issues/22475).</span><span class="sxs-lookup"><span data-stu-id="eb07b-255">See [tracking issue](https://github.com/dotnet/efcore/issues/22475).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb07b-256">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="eb07b-256">Additional resources</span></span>

* <span data-ttu-id="eb07b-257">[EF Core Community Standup-Sitzung](https://www.youtube.com/watch?v=W1sxepfIMRM&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=32)mit einem detaillierten Einblick in m:n-Beziehungen und der zugrunde liegenden Infrastruktur.</span><span class="sxs-lookup"><span data-stu-id="eb07b-257">[EF Core Community Standup session](https://www.youtube.com/watch?v=W1sxepfIMRM&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=32), with a deep dive into Many-to-many and the infrastructure underpinning it.</span></span>
