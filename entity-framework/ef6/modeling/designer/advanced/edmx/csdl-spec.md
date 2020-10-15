---
title: CSDL-Spezifikation-EF6
description: CSDL-Spezifikation in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/csdl-spec
ms.openlocfilehash: 9fdd8fc5ed16f7ba7d11e79a9449f120f5d579c2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066355"
---
# <a name="csdl-specification"></a><span data-ttu-id="27830-103">CSDL-Spezifikation</span><span class="sxs-lookup"><span data-stu-id="27830-103">CSDL Specification</span></span>
<span data-ttu-id="27830-104">Die konzeptionelle Schemadefinitionssprache (CSDL) ist eine XML-basierte Sprache, die die Entitäten, Beziehungen und Funktionen beschreibt, die ein konzeptionelles Modell einer datengesteuerten Anwendung bilden.</span><span class="sxs-lookup"><span data-stu-id="27830-104">Conceptual schema definition language (CSDL) is an XML-based language that describes the entities, relationships, and functions that make up a conceptual model of a data-driven application.</span></span> <span data-ttu-id="27830-105">Dieses konzeptionelle Modell kann von der Entity Framework oder WCF Data Services verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-105">This conceptual model can be used by the Entity Framework or WCF Data Services.</span></span> <span data-ttu-id="27830-106">Die Metadaten, die mit CSDL beschrieben werden, werden vom-Entity Framework verwendet, um Entitäten und Beziehungen, die in einem konzeptionellen Modell definiert sind, einer-Datenquelle zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="27830-106">The metadata that is described with CSDL is used by the Entity Framework to map entities and relationships that are defined in a conceptual model to a data source.</span></span> <span data-ttu-id="27830-107">Weitere Informationen finden Sie unter [SSDL-Spezifikation](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) und [MSL-Spezifikation](xref:ef6/modeling/designer/advanced/edmx/msl-spec).</span><span class="sxs-lookup"><span data-stu-id="27830-107">For more information, see [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) and [MSL Specification](xref:ef6/modeling/designer/advanced/edmx/msl-spec).</span></span>

<span data-ttu-id="27830-108">CSDL ist die Implementierung des Entity Data Model der Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="27830-108">CSDL is the Entity Framework's implementation of the Entity Data Model.</span></span>

<span data-ttu-id="27830-109">In einer Entity Framework-Anwendung werden Metadaten des konzeptionellen Modells aus einer CSDL-Datei (geschrieben in CSDL) in eine Instanz der System. Data. Metadata. Edm. EdmItemCollection geladen und können mithilfe von Methoden in der System. Data. Metadata. Edm. MetadataWorkspace-Klasse aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="27830-109">In an Entity Framework application, conceptual model metadata is loaded from a .csdl file (written in CSDL) into an instance of the System.Data.Metadata.Edm.EdmItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="27830-110">Entity Framework verwendet Metadaten des konzeptionellen Modells, um Abfragen für das konzeptionelle Modell in Datenquellen spezifische Befehle zu übersetzen.</span><span class="sxs-lookup"><span data-stu-id="27830-110">Entity Framework uses conceptual model metadata to translate queries against the conceptual model to data source-specific commands.</span></span>

<span data-ttu-id="27830-111">Der EF-Designer speichert Informationen über das konzeptionelle Modell in einer EDMX-Datei zur Entwurfszeit.</span><span class="sxs-lookup"><span data-stu-id="27830-111">The EF Designer stores conceptual model information in an .edmx file at design time.</span></span> <span data-ttu-id="27830-112">Zur Erstellungszeit verwendet der EF-Designer Informationen in einer EDMX-Datei, um die CSDL-Datei zu erstellen, die zur Laufzeit von Entity Framework benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="27830-112">At build time, the EF Designer uses information in an .edmx file to create the .csdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="27830-113">Die verschiedenen Versionen von CSDL werden von XML-Namespaces unterschieden.</span><span class="sxs-lookup"><span data-stu-id="27830-113">Versions of CSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="27830-114">CSDL-Version</span><span class="sxs-lookup"><span data-stu-id="27830-114">CSDL Version</span></span> | <span data-ttu-id="27830-115">XML-Namespace</span><span class="sxs-lookup"><span data-stu-id="27830-115">XML Namespace</span></span>                                |
|:-------------|:---------------------------------------------|
| <span data-ttu-id="27830-116">CSDL v1</span><span class="sxs-lookup"><span data-stu-id="27830-116">CSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm |
| <span data-ttu-id="27830-117">CSDL v2</span><span class="sxs-lookup"><span data-stu-id="27830-117">CSDL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/edm |
| <span data-ttu-id="27830-118">CSDL v3</span><span class="sxs-lookup"><span data-stu-id="27830-118">CSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a><span data-ttu-id="27830-119">Zuordnungselement (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-119">Association Element (CSDL)</span></span>

<span data-ttu-id="27830-120">Ein **Association** -Element definiert eine Beziehung zwischen zwei Entitäts Typen.</span><span class="sxs-lookup"><span data-stu-id="27830-120">An **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="27830-121">Eine Zuordnung muss die Entitätstypen, die in der Beziehung enthalten sind, und die mögliche Anzahl von Entitätstypen an den Enden der Beziehung angeben, die auch als Multiplizität bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="27830-121">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="27830-122">Die Multiplizität eines Zuordnungs Endes kann über einen Wert von eins (1), NULL oder eins (0.. 1) oder viele ( \* ) verfügen.</span><span class="sxs-lookup"><span data-stu-id="27830-122">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="27830-123">Diese Informationen werden in zwei untergeordneten End-Elementen angegeben.</span><span class="sxs-lookup"><span data-stu-id="27830-123">This information is specified in two child End elements.</span></span>

<span data-ttu-id="27830-124">Auf Entitätstypinstanzen an einem Ende einer Zuordnung kann über Navigationseigenschaften oder Fremdschlüssel zugegriffen werden, sofern sie für einen Entitätstyp verfügbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="27830-124">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys, if they are exposed on an entity type.</span></span>

<span data-ttu-id="27830-125">In einer Anwendung stellt eine Instanz einer Zuordnung eine bestimmte Zuordnung zwischen Instanzen von Entitätstypen dar.</span><span class="sxs-lookup"><span data-stu-id="27830-125">In an application, an instance of an association represents a specific association between instances of entity types.</span></span> <span data-ttu-id="27830-126">Zuordnungsinstanzen werden logisch in einem Zuordnungssatz gruppiert.</span><span class="sxs-lookup"><span data-stu-id="27830-126">Association instances are logically grouped in an association set.</span></span>

<span data-ttu-id="27830-127">Ein **Association** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-127">An **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-128">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-128">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="27830-129">End (genau 2 Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-129">End (exactly 2 elements)</span></span>
-   <span data-ttu-id="27830-130">ReferentialConstraint (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-130">ReferentialConstraint (zero or one element)</span></span>
-   <span data-ttu-id="27830-131">Anmerkungselemente (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-131">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-132">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-132">Applicable Attributes</span></span>

<span data-ttu-id="27830-133">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Association** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-133">The table below describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="27830-134">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-134">Attribute Name</span></span> | <span data-ttu-id="27830-135">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-135">Is Required</span></span> | <span data-ttu-id="27830-136">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-136">Value</span></span>                        |
|:---------------|:------------|:-----------------------------|
| <span data-ttu-id="27830-137">**Name**</span><span class="sxs-lookup"><span data-stu-id="27830-137">**Name**</span></span>       | <span data-ttu-id="27830-138">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-138">Yes</span></span>         | <span data-ttu-id="27830-139">Der Name der Zuordnung.</span><span class="sxs-lookup"><span data-stu-id="27830-139">The name of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-140">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Association** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-140">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="27830-141">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-141">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-142">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-142">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-143">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-143">Example</span></span>

<span data-ttu-id="27830-144">Das folgende Beispiel zeigt ein **Association** -Element, das die Zuordnung **CustomerOrders** definiert, wenn Fremdschlüssel für die Entitäts Typen **Customer** und **Order** nicht verfügbar gemacht wurden.</span><span class="sxs-lookup"><span data-stu-id="27830-144">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have not been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="27830-145">Die **Multiplicity** multiplizitätswerte für jedes **Ende** der Zuordnung geben an, dass viele **Bestellungen** einem **Kunden**zugeordnet werden können, aber nur ein **Kunde** kann einem **Auftrag**zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-145">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="27830-146">Außerdem gibt das **OnDelete** -Element an, dass alle **Bestellungen** , die mit einem bestimmten **Kunden** verknüpft sind und in den ObjectContext geladen wurden, gelöscht werden, wenn der **Kunde** gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="27830-146">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

<span data-ttu-id="27830-147">Das folgende Beispiel zeigt ein **Association** -Element, das die Zuordnung " **CustomerOrders** " definiert, wenn Fremdschlüssel für die Entitäts Typen " **Customer** " und " **Order** " verfügbar gemacht wurden.</span><span class="sxs-lookup"><span data-stu-id="27830-147">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="27830-148">Wenn Fremdschlüssel verfügbar gemacht werden, wird die Beziehung zwischen den Entitäten mit einem **referentialeinschränkung** -Element verwaltet.</span><span class="sxs-lookup"><span data-stu-id="27830-148">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element.</span></span> <span data-ttu-id="27830-149">Ein entsprechendes AssociationSetMapping-Element ist zur Zuordnung dieser Zuweisung zur Datenquelle nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="27830-149">A corresponding AssociationSetMapping element is not necessary to map this association to the data source.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
   <ReferentialConstraint>
        <Principal Role="Customer">
            <PropertyRef Name="Id" />
        </Principal>
        <Dependent Role="Order">
             <PropertyRef Name="CustomerId" />
         </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="associationset-element-csdl"></a><span data-ttu-id="27830-150">AssociationSet-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-150">AssociationSet Element (CSDL)</span></span>

<span data-ttu-id="27830-151">Das **AssociationSet** -Element in konzeptioneller Schema Definitions Sprache (CSDL) ist ein logischer Container für Zuordnungs Instanzen desselben Typs.</span><span class="sxs-lookup"><span data-stu-id="27830-151">The **AssociationSet** element in conceptual schema definition language (CSDL) is a logical container for association instances of the same type.</span></span> <span data-ttu-id="27830-152">Ein Zuordnungssatz stellt eine Definition zum Gruppieren von Zuordnungsinstanzen bereit, sodass sie einer Datenquelle zugeordnet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-152">An association set provides a definition for grouping association instances so that they can be mapped to a data source.</span></span>  

<span data-ttu-id="27830-153">Das **AssociationSet** -Element kann über die folgenden untergeordneten Elemente verfügen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-153">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-154">Dokumentation (kein (null) oder ein Element zugelassen)</span><span class="sxs-lookup"><span data-stu-id="27830-154">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="27830-155">End (genau zwei Elemente erforderlich)</span><span class="sxs-lookup"><span data-stu-id="27830-155">End (exactly two elements required)</span></span>
-   <span data-ttu-id="27830-156">Anmerkungselemente (kein (null) Element oder mehrere Elemente zugelassen)</span><span class="sxs-lookup"><span data-stu-id="27830-156">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="27830-157">Das **Association** -Attribut gibt den Zuordnungstyp an, der in einem Zuordnungs Satz enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="27830-157">The **Association** attribute specifies the type of association that an association set contains.</span></span> <span data-ttu-id="27830-158">Die Entitätenmengen, die die Enden eines Zuordnungs Satzes bilden, werden mit genau **End** zwei untergeordneten Endelementen angegeben.</span><span class="sxs-lookup"><span data-stu-id="27830-158">The entity sets that make up the ends of an association set are specified with exactly two child **End** elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-159">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-159">Applicable Attributes</span></span>

<span data-ttu-id="27830-160">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **AssociationSet** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-160">The table below describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="27830-161">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-161">Attribute Name</span></span>  | <span data-ttu-id="27830-162">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-162">Is Required</span></span> | <span data-ttu-id="27830-163">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-163">Value</span></span>                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-164">**Name**</span><span class="sxs-lookup"><span data-stu-id="27830-164">**Name**</span></span>        | <span data-ttu-id="27830-165">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-165">Yes</span></span>         | <span data-ttu-id="27830-166">Der Name des Entitätssatzes.</span><span class="sxs-lookup"><span data-stu-id="27830-166">The name of the entity set.</span></span> <span data-ttu-id="27830-167">Der Wert des **Name** -Attributs darf nicht mit dem Wert des **Association** -Attributs identisch sein.</span><span class="sxs-lookup"><span data-stu-id="27830-167">The value of the **Name** attribute cannot be the same as the value of the **Association** attribute.</span></span>                                 |
| <span data-ttu-id="27830-168">**Zuordnung**</span><span class="sxs-lookup"><span data-stu-id="27830-168">**Association**</span></span> | <span data-ttu-id="27830-169">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-169">Yes</span></span>         | <span data-ttu-id="27830-170">Der vollqualifizierte Name der Zuordnung, von der der Zuordnungssatz Instanzen enthält.</span><span class="sxs-lookup"><span data-stu-id="27830-170">The fully-qualified name of the association that the association set contains instances of.</span></span> <span data-ttu-id="27830-171">Die Zuordnung muss sich im gleichen Namespace wie der Zuordnungssatz befinden.</span><span class="sxs-lookup"><span data-stu-id="27830-171">The association must be in the same namespace as the association set.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-172">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **AssociationSet** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-172">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="27830-173">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-173">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-174">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-174">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-175">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-175">Example</span></span>

<span data-ttu-id="27830-176">Das folgende Beispiel zeigt ein **EntityContainer** -Element mit zwei **AssociationSet** -Elementen:</span><span class="sxs-lookup"><span data-stu-id="27830-176">The following example shows an **EntityContainer** element with two **AssociationSet** elements:</span></span>

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="collectiontype-element-csdl"></a><span data-ttu-id="27830-177">CollectionType-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-177">CollectionType Element (CSDL)</span></span>

<span data-ttu-id="27830-178">Das **CollectionType** -Element in der konzeptionellen Schema Definitions Sprache (CSDL) gibt an, dass ein Funktionsparameter oder Funktions Rückgabetyp eine Auflistung ist.</span><span class="sxs-lookup"><span data-stu-id="27830-178">The **CollectionType** element in conceptual schema definition language (CSDL) specifies that a function parameter or function return type is a collection.</span></span> <span data-ttu-id="27830-179">Das **CollectionType** -Element kann ein untergeordnetes Element des Parameter-Elements oder des ReturnType (Function)-Elements sein.</span><span class="sxs-lookup"><span data-stu-id="27830-179">The **CollectionType** element can be a child of the Parameter element or the ReturnType (Function) element.</span></span> <span data-ttu-id="27830-180">Der Typ der Auflistung kann entweder mit dem **Type** -Attribut oder einem der folgenden untergeordneten Elemente angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="27830-180">The type of collection can be specified by using either the **Type** attribute or one of the following child elements:</span></span>

-   <span data-ttu-id="27830-181">**CollectionType**</span><span class="sxs-lookup"><span data-stu-id="27830-181">**CollectionType**</span></span>
-   <span data-ttu-id="27830-182">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="27830-182">ReferenceType</span></span>
-   <span data-ttu-id="27830-183">RowType</span><span class="sxs-lookup"><span data-stu-id="27830-183">RowType</span></span>
-   <span data-ttu-id="27830-184">TypeRef</span><span class="sxs-lookup"><span data-stu-id="27830-184">TypeRef</span></span>

> [!NOTE]
> <span data-ttu-id="27830-185">Ein Modell überprüft nicht, ob der Typ einer Auflistung sowohl mit dem **Type** -Attribut als auch mit einem untergeordneten Element angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="27830-185">A model will not validate if the type of a collection is specified with both the **Type** attribute and a child element.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="27830-186">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-186">Applicable Attributes</span></span>

<span data-ttu-id="27830-187">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **CollectionType** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-187">The following table describes the attributes that can be applied to the **CollectionType** element.</span></span> <span data-ttu-id="27830-188">Beachten Sie, dass die Attribute **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**und **COLLATIONS** nur auf Auflistungen von **edmsimpletypes**anwendbar sind.</span><span class="sxs-lookup"><span data-stu-id="27830-188">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to collections of **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="27830-189">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-189">Attribute Name</span></span>                                                          | <span data-ttu-id="27830-190">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-190">Is Required</span></span> | <span data-ttu-id="27830-191">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-191">Value</span></span>                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-192">**Typ**</span><span class="sxs-lookup"><span data-stu-id="27830-192">**Type**</span></span>                                                                | <span data-ttu-id="27830-193">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-193">No</span></span>          | <span data-ttu-id="27830-194">Der Typ der Auflistung.</span><span class="sxs-lookup"><span data-stu-id="27830-194">The type of the collection.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="27830-195">**NULL zulassen**</span><span class="sxs-lookup"><span data-stu-id="27830-195">**Nullable**</span></span>                                                            | <span data-ttu-id="27830-196">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-196">No</span></span>          | <span data-ttu-id="27830-197">**True** (Standardwert) oder **False** abhängig davon, ob die Eigenschaft einen NULL-Wert haben kann.</span><span class="sxs-lookup"><span data-stu-id="27830-197">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                 |
| <span data-ttu-id="27830-198">> in CSDL v1 muss eine Eigenschaft eines komplexen Typs über verfügen `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="27830-198">> In the CSDL v1, a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                  |
| <span data-ttu-id="27830-199">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="27830-199">**DefaultValue**</span></span>                                                        | <span data-ttu-id="27830-200">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-200">No</span></span>          | <span data-ttu-id="27830-201">Der Standardwert der Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="27830-201">The default value of the property.</span></span>                                                                                                                                                                                               |
| <span data-ttu-id="27830-202">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="27830-202">**MaxLength**</span></span>                                                           | <span data-ttu-id="27830-203">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-203">No</span></span>          | <span data-ttu-id="27830-204">Maximale Länge des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-204">The maximum length of the property value.</span></span>                                                                                                                                                                                        |
| <span data-ttu-id="27830-205">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="27830-205">**FixedLength**</span></span>                                                         | <span data-ttu-id="27830-206">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-206">No</span></span>          | <span data-ttu-id="27830-207">**True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Zeichenfolge mit fester Länge gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="27830-207">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                           |
| <span data-ttu-id="27830-208">**Genauigkeit**</span><span class="sxs-lookup"><span data-stu-id="27830-208">**Precision**</span></span>                                                           | <span data-ttu-id="27830-209">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-209">No</span></span>          | <span data-ttu-id="27830-210">Die Genauigkeit des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-210">The precision of the property value.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="27830-211">**Skalierung**</span><span class="sxs-lookup"><span data-stu-id="27830-211">**Scale**</span></span>                                                               | <span data-ttu-id="27830-212">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-212">No</span></span>          | <span data-ttu-id="27830-213">Die Skalierung des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-213">The scale of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="27830-214">**SRID**</span><span class="sxs-lookup"><span data-stu-id="27830-214">**SRID**</span></span>                                                                | <span data-ttu-id="27830-215">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-215">No</span></span>          | <span data-ttu-id="27830-216">Verweis Bezeichner für räumliche Systeme.</span><span class="sxs-lookup"><span data-stu-id="27830-216">Spatial System Reference Identifier.</span></span> <span data-ttu-id="27830-217">Nur für Eigenschaften räumlicher Typen gültig.</span><span class="sxs-lookup"><span data-stu-id="27830-217">Valid only for properties of spatial types.</span></span><span data-ttu-id="27830-218">Weitere Informationen finden Sie unter [SRID](https://en.wikipedia.org/wiki/SRID) und [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx) .</span><span class="sxs-lookup"><span data-stu-id="27830-218">   For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span></span> |
| <span data-ttu-id="27830-219">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="27830-219">**Unicode**</span></span>                                                             | <span data-ttu-id="27830-220">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-220">No</span></span>          | <span data-ttu-id="27830-221">**True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Unicode-Zeichenfolge gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="27830-221">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                                |
| <span data-ttu-id="27830-222">**Sortierung**</span><span class="sxs-lookup"><span data-stu-id="27830-222">**Collation**</span></span>                                                           | <span data-ttu-id="27830-223">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-223">No</span></span>          | <span data-ttu-id="27830-224">Eine Zeichenfolge, die angibt, welche Sortierreihenfolge in der Datenquelle verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="27830-224">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                    |

 

> [!NOTE]
> <span data-ttu-id="27830-225">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **CollectionType** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-225">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="27830-226">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-226">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-227">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-227">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-228">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-228">Example</span></span>

<span data-ttu-id="27830-229">Das folgende Beispiel zeigt eine Modell definierte Funktion, die ein **CollectionType** -Element verwendet, um anzugeben, dass die Funktion eine Auflistung von **Person** -Entitäts Typen zurückgibt (wie mit dem **Element Type** -Attribut angegeben).</span><span class="sxs-lookup"><span data-stu-id="27830-229">The following example shows a model-defined function that that uses a **CollectionType** element to specify that the function returns a collection of **Person** entity types (as specified with the **ElementType** attribute).</span></span>

``` xml
 <Function Name="LastNamesAfter">
        <Parameter Name="someString" Type="Edm.String"/>
        <ReturnType>
             <CollectionType  ElementType="SchoolModel.Person"/>
        </ReturnType>
        <DefiningExpression>
             SELECT VALUE p
             FROM SchoolEntities.People AS p
             WHERE p.LastName >= someString
        </DefiningExpression>
 </Function>
```
 

<span data-ttu-id="27830-230">Das folgende Beispiel zeigt eine Modell definierte Funktion, die ein **CollectionType** -Element verwendet, um anzugeben, dass die Funktion eine Auflistung von Zeilen zurückgibt (wie im **RowType** -Element angegeben).</span><span class="sxs-lookup"><span data-stu-id="27830-230">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```
 

<span data-ttu-id="27830-231">Das folgende Beispiel zeigt eine Modell definierte Funktion, die das **CollectionType** -Element verwendet, um anzugeben, dass die Funktion als Parameter eine Auflistung von **Abteilungs** Entitäts Typen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="27830-231">The following example shows a model-defined function that uses the **CollectionType** element to specify that the function accepts as a parameter a collection of **Department** entity types.</span></span>

``` xml
 <Function Name="GetAvgBudget">
      <Parameter Name="Departments">
          <CollectionType>
             <TypeRef Type="SchoolModel.Department"/>
          </CollectionType>
           </Parameter>
       <ReturnType Type="Collection(Edm.Decimal)"/>
       <DefiningExpression>
             SELECT VALUE AVG(d.Budget) FROM Departments AS d
       </DefiningExpression>
 </Function>
```
 

 

## <a name="complextype-element-csdl"></a><span data-ttu-id="27830-232">ComplexType-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-232">ComplexType Element (CSDL)</span></span>

<span data-ttu-id="27830-233">Ein **complexType** -Element definiert eine Datenstruktur, die aus **edmsimpletype** -Eigenschaften oder anderen komplexen Typen besteht.</span><span class="sxs-lookup"><span data-stu-id="27830-233">A **ComplexType** element defines a data structure composed of **EdmSimpleType** properties or other complex types.</span></span><span data-ttu-id="27830-234">Ein komplexer Typ kann eine Eigenschaft eines Entitätstyps oder eines anderen komplexen Typs sein.</span><span class="sxs-lookup"><span data-stu-id="27830-234">  A complex type can be a property of an entity type or another complex type.</span></span> <span data-ttu-id="27830-235">Ein komplexer Typ entspricht einem Entitätstyp, in dem von einem komplexen Typ Daten definiert werden.</span><span class="sxs-lookup"><span data-stu-id="27830-235">A complex type is similar to an entity type in that a complex type defines data.</span></span> <span data-ttu-id="27830-236">Es gibt jedoch einige Hauptunterschiede zwischen komplexen Typen und Entitätstypen:</span><span class="sxs-lookup"><span data-stu-id="27830-236">However, there are some key differences between complex types and entity types:</span></span>

-   <span data-ttu-id="27830-237">Komplexe Typen weisen keine Identitäten (oder Schlüssel) auf und können daher nicht unabhängig sein.</span><span class="sxs-lookup"><span data-stu-id="27830-237">Complex types do not have identities (or keys) and therefore cannot exist independently.</span></span> <span data-ttu-id="27830-238">Komplexe Typen können nur Eigenschaften von Entitätstypen oder anderen komplexen Typen sein.</span><span class="sxs-lookup"><span data-stu-id="27830-238">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="27830-239">Komplexe Typen können nicht Teile von Zuordnungen sein.</span><span class="sxs-lookup"><span data-stu-id="27830-239">Complex types cannot participate in associations.</span></span> <span data-ttu-id="27830-240">Die Enden einer Zuordnung können kein komplexer Typ sein, daher können Navigationseigenschaften nicht für komplexe Typen definiert werden.</span><span class="sxs-lookup"><span data-stu-id="27830-240">Neither end of an association can be a complex type, and therefore navigation properties cannot be defined for complex types.</span></span>
-   <span data-ttu-id="27830-241">Einer komplexen Typeigenschaft kann kein NULL-Wert zugewiesen werden, obwohl jede skalare Eigenschaft eines komplexen Typs auf NULL festgelegt werden kann.</span><span class="sxs-lookup"><span data-stu-id="27830-241">A complex type property cannot have a null value, though the scalar properties of a complex type may each be set to null.</span></span>

<span data-ttu-id="27830-242">Ein **complexType** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-242">A **ComplexType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-243">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-243">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="27830-244">Property (0 (null) oder mehr Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-244">Property (zero or more elements)</span></span>
-   <span data-ttu-id="27830-245">Anmerkungselemente (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-245">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="27830-246">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **complexType** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-246">The table below describes the attributes that can be applied to the **ComplexType** element.</span></span>

| <span data-ttu-id="27830-247">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-247">Attribute Name</span></span>                                                                                                 | <span data-ttu-id="27830-248">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-248">Is Required</span></span> | <span data-ttu-id="27830-249">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-249">Value</span></span>                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-250">Name</span><span class="sxs-lookup"><span data-stu-id="27830-250">Name</span></span>                                                                                                           | <span data-ttu-id="27830-251">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-251">Yes</span></span>         | <span data-ttu-id="27830-252">Der Name des komplexen Typs.</span><span class="sxs-lookup"><span data-stu-id="27830-252">The name of the complex type.</span></span> <span data-ttu-id="27830-253">Der Name eines komplexen Typs darf nicht dem Namen anderer komplexer Typen, Entitätstypen oder Zuordnungen entsprechen, die sich innerhalb des Bereichs des Modells befinden.</span><span class="sxs-lookup"><span data-stu-id="27830-253">The name of a complex type cannot be the same as the name of another complex type, entity type, or association that is within the scope of the model.</span></span> |
| <span data-ttu-id="27830-254">BaseType</span><span class="sxs-lookup"><span data-stu-id="27830-254">BaseType</span></span>                                                                                                       | <span data-ttu-id="27830-255">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-255">No</span></span>          | <span data-ttu-id="27830-256">Der Name eines anderen komplexen Typs, der der Basistyp des zu definierenden komplexen Typs ist.</span><span class="sxs-lookup"><span data-stu-id="27830-256">The name of another complex type that is the base type of the complex type that is being defined.</span></span> <br/> [!NOTE]                                                                     |
| <span data-ttu-id="27830-257">> dieses Attribut ist in CSDL v1 nicht anwendbar.</span><span class="sxs-lookup"><span data-stu-id="27830-257">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="27830-258">Für komplexe Typen wird Vererbung in dieser Version nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="27830-258">Inheritance for complex types is not supported in that version.</span></span> |             |                                                                                                                                                                                     |
| <span data-ttu-id="27830-259">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="27830-259">Abstract</span></span>                                                                                                       | <span data-ttu-id="27830-260">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-260">No</span></span>          | <span data-ttu-id="27830-261">**True** oder **false** (der Standardwert), abhängig davon, ob der komplexe Typ ein abstrakter Typ ist.</span><span class="sxs-lookup"><span data-stu-id="27830-261">**True** or **False** (the default value) depending on whether the complex type is an abstract type.</span></span> <br/> [!NOTE]                                                                  |
| <span data-ttu-id="27830-262">> dieses Attribut ist in CSDL v1 nicht anwendbar.</span><span class="sxs-lookup"><span data-stu-id="27830-262">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="27830-263">Komplexe Typen in dieser Version können keine abstrakten Typen sein.</span><span class="sxs-lookup"><span data-stu-id="27830-263">Complex types in that version cannot be abstract types.</span></span>         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="27830-264">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **complexType** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-264">Any number of annotation attributes (custom XML attributes) may be applied to the **ComplexType** element.</span></span> <span data-ttu-id="27830-265">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-265">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-266">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-266">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-267">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-267">Example</span></span>

<span data-ttu-id="27830-268">Das folgende Beispiel zeigt einen komplexen Typ, eine **Adresse**mit den **edmsimpletype** -Eigenschaften " **StreetAddress**", " **City**", " **StateOrProvince**", " **Country**" und " **PostalCode**".</span><span class="sxs-lookup"><span data-stu-id="27830-268">The following example shows a complex type, **Address**, with the **EdmSimpleType** properties **StreetAddress**, **City**, **StateOrProvince**, **Country**, and **PostalCode**.</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

<span data-ttu-id="27830-269">Um die **Adresse** des komplexen Typs (oben) als Eigenschaft eines Entitäts Typs zu definieren, müssen Sie den Eigenschaftentyp in der Entitätstyp Definition deklarieren.</span><span class="sxs-lookup"><span data-stu-id="27830-269">To define the complex type **Address** (above) as a property of an entity type, you must declare the property type in the entity type definition.</span></span> <span data-ttu-id="27830-270">Das folgende Beispiel zeigt die **Address** -Eigenschaft als komplexen Typ für einen Entitätstyp (**Publisher**):</span><span class="sxs-lookup"><span data-stu-id="27830-270">The following example shows the **Address** property as a complex type on an entity type (**Publisher**):</span></span>

``` xml
 <EntityType Name="Publisher">
       <Key>
         <PropertyRef Name="Id" />
       </Key>
       <Property Type="Int32" Name="Id" Nullable="false" />
       <Property Type="String" Name="Name" Nullable="false" />
       <Property Type="BooksModel.Address" Name="Address" Nullable="false" />
       <NavigationProperty Name="Books" Relationship="BooksModel.PublishedBy"
                           FromRole="Publisher" ToRole="Book" />
     </EntityType>
```
 

 

## <a name="definingexpression-element-csdl"></a><span data-ttu-id="27830-271">DefiningExpression-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-271">DefiningExpression Element (CSDL)</span></span>

<span data-ttu-id="27830-272">Das **DefiningExpression** -Element in konzeptioneller Schema Definitions Sprache (CSDL) enthält einen Entity SQL Ausdruck, der eine Funktion im konzeptionellen Modell definiert.</span><span class="sxs-lookup"><span data-stu-id="27830-272">The **DefiningExpression** element in conceptual schema definition language (CSDL) contains an Entity SQL expression that defines a function in the conceptual model.</span></span>  

> [!NOTE]
> <span data-ttu-id="27830-273">Zu Überprüfungszwecken kann ein **DefiningExpression** -Element beliebigen Inhalt enthalten.</span><span class="sxs-lookup"><span data-stu-id="27830-273">For validation purposes, a **DefiningExpression** element can contain arbitrary content.</span></span> <span data-ttu-id="27830-274">Entity Framework wird jedoch zur Laufzeit eine Ausnahme auslösen, wenn ein **DefiningExpression** -Element keine gültigen Entity SQL enthält.</span><span class="sxs-lookup"><span data-stu-id="27830-274">However, Entity Framework will throw an exception at runtime if a **DefiningExpression** element does not contain valid Entity SQL.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="27830-275">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-275">Applicable Attributes</span></span>

<span data-ttu-id="27830-276">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **DefiningExpression** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-276">Any number of annotation attributes (custom XML attributes) may be applied to the **DefiningExpression** element.</span></span> <span data-ttu-id="27830-277">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-277">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-278">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-278">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="27830-279">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-279">Example</span></span>

<span data-ttu-id="27830-280">Im folgenden Beispiel wird ein **DefiningExpression** -Element verwendet, um eine Funktion zu definieren, die die Anzahl der Jahre seit der Veröffentlichung eines Buchs zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="27830-280">The following example uses a **DefiningExpression** element to define a function that returns the number of years since a book was published.</span></span> <span data-ttu-id="27830-281">Der Inhalt des **DefiningExpression** -Elements wird in Entity SQL geschrieben.</span><span class="sxs-lookup"><span data-stu-id="27830-281">The content of the **DefiningExpression** element is written in Entity SQL.</span></span>

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a><span data-ttu-id="27830-282">Dependent-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-282">Dependent Element (CSDL)</span></span>

<span data-ttu-id="27830-283">Das **abhängige** Element in konzeptioneller Schema Definitions Sprache (CSDL) ist ein untergeordnetes Element des referentialeinschränkung-Elements und definiert das abhängige Ende einer referenziellen Einschränkung.</span><span class="sxs-lookup"><span data-stu-id="27830-283">The **Dependent** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element and defines the dependent end of a referential constraint.</span></span> <span data-ttu-id="27830-284">Ein **referentialeinschränkung** -Element definiert Funktionen, die einer Einschränkung der referenziellen Integrität in einer relationalen Datenbank ähneln.</span><span class="sxs-lookup"><span data-stu-id="27830-284">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="27830-285">So wie eine Spalte (oder Spalten) einer Datenbanktabelle auf den Primärschlüssel einer anderen Tabelle verweisen kann, kann eine Eigenschaft (oder Eigenschaften) eines Entitätstyps auf den Entitätsschlüssel eines anderen Entitätstyps verweisen.</span><span class="sxs-lookup"><span data-stu-id="27830-285">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="27830-286">Der Entitätstyp, auf den verwiesen wird, wird als *Prinzipal Ende* der Einschränkung bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="27830-286">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="27830-287">Der Entitätstyp, der auf das Prinzipal Ende verweist, wird als *abhängiges Ende* der Einschränkung bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="27830-287">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="27830-288">**PropertyRef** -Elemente werden verwendet, um anzugeben, welche Schlüssel auf das Prinzipal Ende verweisen.</span><span class="sxs-lookup"><span data-stu-id="27830-288">**PropertyRef** elements are used to specify which keys reference the principal end.</span></span>

<span data-ttu-id="27830-289">Das **abhängige** Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-289">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-290">PropertyRef (mindestens ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-290">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="27830-291">Anmerkungselemente (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-291">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-292">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-292">Applicable Attributes</span></span>

<span data-ttu-id="27830-293">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **abhängige** Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-293">The table below describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="27830-294">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-294">Attribute Name</span></span> | <span data-ttu-id="27830-295">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-295">Is Required</span></span> | <span data-ttu-id="27830-296">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-296">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="27830-297">**Rolle**</span><span class="sxs-lookup"><span data-stu-id="27830-297">**Role**</span></span>       | <span data-ttu-id="27830-298">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-298">Yes</span></span>         | <span data-ttu-id="27830-299">Der Name des Entitätstyps am abhängigen Ende der Zuordnung.</span><span class="sxs-lookup"><span data-stu-id="27830-299">The name of the entity type on the dependent end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-300">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **abhängige** Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-300">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="27830-301">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-301">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-302">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-302">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-303">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-303">Example</span></span>

<span data-ttu-id="27830-304">Das folgende Beispiel zeigt ein **referenentialeinschränkungs** -Element, das als Teil der Definition der **PublishedBy** -Zuordnung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="27830-304">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="27830-305">Die **PublisherID-** Eigenschaft des **Book** -Entitäts Typs bildet das abhängige Ende der referenziellen Einschränkung.</span><span class="sxs-lookup"><span data-stu-id="27830-305">The **PublisherId** property of the **Book** entity type makes up the dependent end of the referential constraint.</span></span>

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="documentation-element-csdl"></a><span data-ttu-id="27830-306">Documentation-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-306">Documentation Element (CSDL)</span></span>

<span data-ttu-id="27830-307">Das **Documentation** -Element in der konzeptionellen Schema Definitions Sprache (CSDL) kann verwendet werden, um Informationen zu einem Objekt bereitzustellen, das in einem übergeordneten Element definiert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-307">The **Documentation** element in conceptual schema definition language (CSDL) can be used to provide information about an object that is defined in a parent element.</span></span> <span data-ttu-id="27830-308">Wenn in einer EDMX-Datei das **Documentation** -Element ein untergeordnetes Element eines Elements ist, das als Objekt auf der Entwurfs Oberfläche des EF-Designers angezeigt wird (z. b. eine Entität, eine Zuordnung oder eine Eigenschaft), wird der Inhalt des **Dokumentations** Elements im Visual Studio- **Eigenschaften** Fenster für das Objekt angezeigt.</span><span class="sxs-lookup"><span data-stu-id="27830-308">In an .edmx file, when the **Documentation** element is a child of an element that appears as an object on the design surface of the EF Designer  (such as an entity, association, or property), the contents of the **Documentation** element will appear in the Visual Studio **Properties** window for the object.</span></span>

<span data-ttu-id="27830-309">Das **Documentation** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-309">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-310">**Zusammenfassung**: eine kurze Beschreibung des übergeordneten Elements.</span><span class="sxs-lookup"><span data-stu-id="27830-310">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="27830-311">(kein (Null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-311">(zero or one element)</span></span>
-   <span data-ttu-id="27830-312">**LongDescription**: eine umfassende Beschreibung des übergeordneten Elements.</span><span class="sxs-lookup"><span data-stu-id="27830-312">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="27830-313">(kein (Null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-313">(zero or one element)</span></span>
-   <span data-ttu-id="27830-314">Anmerkungselemente.</span><span class="sxs-lookup"><span data-stu-id="27830-314">Annotation elements.</span></span> <span data-ttu-id="27830-315">(keine (Null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-315">(zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-316">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-316">Applicable Attributes</span></span>

<span data-ttu-id="27830-317">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Documentation** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-317">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="27830-318">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-318">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-319">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-319">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="27830-320">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-320">Example</span></span>

<span data-ttu-id="27830-321">Das folgende Beispiel zeigt das **Documentation** -Element als untergeordnetes Element eines EntityType-Elements.</span><span class="sxs-lookup"><span data-stu-id="27830-321">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span> <span data-ttu-id="27830-322">Wenn sich der folgende Code Ausschnitt im CSDL-Inhalt einer EDMX-Datei befindet, werden die Inhalte der Elemente **Summary** und **LongDescription** im Visual Studio- **Eigenschaften** Fenster angezeigt, wenn Sie auf den `Customer` Entitätstyp klicken.</span><span class="sxs-lookup"><span data-stu-id="27830-322">If the snippet below were in the CSDL content of an .edmx file, the contents of the **Summary** and **LongDescription** elements would appear in the Visual Studio **Properties** window when you click on the `Customer` entity type.</span></span>

``` xml
 <EntityType Name="Customer">
    <Documentation>
      <Summary>Summary here.</Summary>
      <LongDescription>Long description here.</LongDescription>
    </Documentation>
    <Key>
      <PropertyRef Name="CustomerId" />
    </Key>
    <Property Type="Int32" Name="CustomerId" Nullable="false" />
    <Property Type="String" Name="Name" Nullable="false" />
 </EntityType>
```
 

 

## <a name="end-element-csdl"></a><span data-ttu-id="27830-323">End-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-323">End Element (CSDL)</span></span>

<span data-ttu-id="27830-324">Das **End** -Element in konzeptioneller Schema Definitions Sprache (CSDL) kann ein untergeordnetes Element des Association-Elements oder des AssociationSet-Elements sein.</span><span class="sxs-lookup"><span data-stu-id="27830-324">The **End** element in conceptual schema definition language (CSDL) can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="27830-325">In jedem Fall ist die Rolle des **End** -Elements anders, und die anwendbaren Attribute unterscheiden sich.</span><span class="sxs-lookup"><span data-stu-id="27830-325">In each case, the role of the **End** element is different and the applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="27830-326">Das End-Element als untergeordnetes Objekt des Association-Elements</span><span class="sxs-lookup"><span data-stu-id="27830-326">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="27830-327">Ein **End** -Element (als untergeordnetes Element des **Association** -Elements) identifiziert den Entitätstyp an einem Ende einer Zuordnung und die Anzahl der Entitätstyp Instanzen, die an diesem Ende einer Zuordnung vorhanden sein können.</span><span class="sxs-lookup"><span data-stu-id="27830-327">An **End** element (as a child of the **Association** element) identifies the entity type on one end of an association and the number of entity type instances that can exist at that end of an association.</span></span> <span data-ttu-id="27830-328">Zuordnungsenden werden als Teil einer Zuordnung definiert. Eine Zuordnung muss genau zwei Zuordnungsenden aufweisen.</span><span class="sxs-lookup"><span data-stu-id="27830-328">Association ends are defined as part of an association; an association must have exactly two association ends.</span></span> <span data-ttu-id="27830-329">Auf Entitätstypinstanzen an einem Ende einer Zuordnung kann über Navigationseigenschaften oder Fremdschlüssel zugegriffen werden, sofern sie für einen Entitätstyp verfügbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="27830-329">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys if they are exposed on an entity type.</span></span>  

<span data-ttu-id="27830-330">Ein **Endelement** kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-330">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-331">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-331">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="27830-332">OnDelete (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-332">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="27830-333">Anmerkungselemente (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-333">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="27830-334">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-334">Applicable Attributes</span></span>

<span data-ttu-id="27830-335">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **End** -Element angewendet werden können, wenn es sich um das untergeordnete Element eines **Association** -Elements handelt.</span><span class="sxs-lookup"><span data-stu-id="27830-335">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="27830-336">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-336">Attribute Name</span></span>   | <span data-ttu-id="27830-337">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-337">Is Required</span></span> | <span data-ttu-id="27830-338">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-338">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-339">**Typ**</span><span class="sxs-lookup"><span data-stu-id="27830-339">**Type**</span></span>         | <span data-ttu-id="27830-340">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-340">Yes</span></span>         | <span data-ttu-id="27830-341">Der Name des Entitätstyps an einem Ende der Zuordnung.</span><span class="sxs-lookup"><span data-stu-id="27830-341">The name of the entity type at one end of the association.</span></span>                                                                                                                                                                                                                                                                                                                                                         |
| <span data-ttu-id="27830-342">**Rolle**</span><span class="sxs-lookup"><span data-stu-id="27830-342">**Role**</span></span>         | <span data-ttu-id="27830-343">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-343">No</span></span>          | <span data-ttu-id="27830-344">Der Name für das Zuordnungsende.</span><span class="sxs-lookup"><span data-stu-id="27830-344">A name for the association end.</span></span> <span data-ttu-id="27830-345">Wird kein Name angegeben, wird der Name des Entitätstyps am Zuordnungsende verwendet.</span><span class="sxs-lookup"><span data-stu-id="27830-345">If no name is provided, the name of the entity type on the association end will be used.</span></span>                                                                                                                                                                                                                                                                                           |
| <span data-ttu-id="27830-346">**Multiplizität**</span><span class="sxs-lookup"><span data-stu-id="27830-346">**Multiplicity**</span></span> | <span data-ttu-id="27830-347">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-347">Yes</span></span>         | <span data-ttu-id="27830-348">**1**, **0.. 1**oder, **\*** abhängig von der Anzahl der Entitätstyp Instanzen, die sich am Ende der Zuordnung befinden können.</span><span class="sxs-lookup"><span data-stu-id="27830-348">**1**, **0..1**, or **\*** depending on the number of entity type instances that can be at the end of the association.</span></span> <br/> <span data-ttu-id="27830-349">der Wert **1** gibt an, dass genau eine Entitätstyp Instanz am Zuordnungs Ende vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="27830-349">**1** indicates that exactly one entity type instance exists at the association end.</span></span> <br/> <span data-ttu-id="27830-350">**0.. 1** gibt an, dass keine oder nur eine Entitätstyp Instanz am Zuordnungs Ende vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="27830-350">**0..1** indicates that zero or one entity type instances exist at the association end.</span></span> <br/> <span data-ttu-id="27830-351">**\*** Gibt an, dass keine, eine oder mehrere Entitätstyp Instanzen am Zuordnungs Ende vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="27830-351">**\*** indicates that zero, one, or more entity type instances exist at the association end.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-352">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **End** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-352">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="27830-353">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-353">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-354">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-354">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="27830-355">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-355">Example</span></span>

<span data-ttu-id="27830-356">Das folgende Beispiel zeigt ein **Association** -Element, das die Zuordnung **CustomerOrders** definiert.</span><span class="sxs-lookup"><span data-stu-id="27830-356">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="27830-357">Die **Multiplicity** multiplizitätswerte für jedes **Ende** der Zuordnung geben an, dass viele **Bestellungen** einem **Kunden**zugeordnet werden können, aber nur ein **Kunde** kann einem **Auftrag**zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-357">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="27830-358">Außerdem gibt das **OnDelete** -Element an, dass alle **Bestellungen** , die mit einem bestimmten **Kunden** verknüpft sind und in den ObjectContext geladen wurden, gelöscht werden, wenn der **Kunde** gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="27830-358">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and that have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="27830-359">Das End-Element als untergeordnetes Objekt des AssociationSet-Elements</span><span class="sxs-lookup"><span data-stu-id="27830-359">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="27830-360">Das **End** -Element gibt ein Ende eines Zuordnungs Satzes an.</span><span class="sxs-lookup"><span data-stu-id="27830-360">The **End** element specifies one end of an association set.</span></span> <span data-ttu-id="27830-361">Das **AssociationSet** -Element muss zwei **Endelemente** enthalten.</span><span class="sxs-lookup"><span data-stu-id="27830-361">The **AssociationSet** element must contain two **End** elements.</span></span> <span data-ttu-id="27830-362">Die in einem **End** -Element enthaltenen Informationen werden für die Zuordnung eines Zuordnungs Satzes zu einer Datenquelle verwendet.</span><span class="sxs-lookup"><span data-stu-id="27830-362">The information contained in an **End** element is used in mapping an association set to a data source.</span></span>

<span data-ttu-id="27830-363">Ein **Endelement** kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-363">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-364">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-364">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="27830-365">Anmerkungselemente (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-365">Annotation elements (zero or more elements)</span></span>

> [!NOTE]
> <span data-ttu-id="27830-366">Anmerkungselemente müssen an alle anderen untergeordneten Elemente angereiht werden.</span><span class="sxs-lookup"><span data-stu-id="27830-366">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="27830-367">Annotation-Elemente sind nur in CSDL v2 und höher zulässig.</span><span class="sxs-lookup"><span data-stu-id="27830-367">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="27830-368">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-368">Applicable Attributes</span></span>

<span data-ttu-id="27830-369">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **End** -Element angewendet werden können, wenn es sich um das untergeordnete Element eines **AssociationSet** -Elements handelt.</span><span class="sxs-lookup"><span data-stu-id="27830-369">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="27830-370">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-370">Attribute Name</span></span> | <span data-ttu-id="27830-371">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-371">Is Required</span></span> | <span data-ttu-id="27830-372">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-372">Value</span></span>                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-373">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="27830-373">**EntitySet**</span></span>  | <span data-ttu-id="27830-374">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-374">Yes</span></span>         | <span data-ttu-id="27830-375">Der Name des **EntitySet** -Elements, das ein Ende des übergeordneten **AssociationSet** -Elements definiert.</span><span class="sxs-lookup"><span data-stu-id="27830-375">The name of the **EntitySet** element that defines one end of the parent **AssociationSet** element.</span></span> <span data-ttu-id="27830-376">Das **EntitySet** -Element muss im gleichen Entitäts Container wie das übergeordnete **AssociationSet** -Element definiert werden.</span><span class="sxs-lookup"><span data-stu-id="27830-376">The **EntitySet** element must be defined in the same entity container as the parent **AssociationSet** element.</span></span> |
| <span data-ttu-id="27830-377">**Rolle**</span><span class="sxs-lookup"><span data-stu-id="27830-377">**Role**</span></span>       | <span data-ttu-id="27830-378">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-378">No</span></span>          | <span data-ttu-id="27830-379">Der Name des Endes des Zuordnungssatzes.</span><span class="sxs-lookup"><span data-stu-id="27830-379">The name of the association set end.</span></span> <span data-ttu-id="27830-380">Wenn das **Role** -Attribut nicht verwendet wird, ist der Name des Zuordnungs Satzes Ende der Name der Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="27830-380">If the **Role** attribute is not used, the name of the association set end will be the name of the entity set.</span></span>                                                                   |

 

> [!NOTE]
> <span data-ttu-id="27830-381">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **End** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-381">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="27830-382">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-382">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-383">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-383">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="27830-384">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-384">Example</span></span>

<span data-ttu-id="27830-385">Das folgende Beispiel zeigt ein **EntityContainer** -Element mit zwei **AssociationSet** -Elementen, die jeweils zwei **Endelemente** haben:</span><span class="sxs-lookup"><span data-stu-id="27830-385">The following example shows an **EntityContainer** element with two **AssociationSet** elements, each with two **End** elements:</span></span>

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entitycontainer-element-csdl"></a><span data-ttu-id="27830-386">EntityContainer-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-386">EntityContainer Element (CSDL)</span></span>

<span data-ttu-id="27830-387">Das **EntityContainer** -Element in konzeptioneller Schema Definitions Sprache (CSDL) ist ein logischer Container für Entitätenmengen, Zuordnungs Sätze und Funktions Importe.</span><span class="sxs-lookup"><span data-stu-id="27830-387">The **EntityContainer** element in conceptual schema definition language (CSDL) is a logical container for entity sets, association sets, and function imports.</span></span> <span data-ttu-id="27830-388">Einem Speichermodell-Entitätscontainer wird durch das EntityContainerMapping-Element ein konzeptioneller Modellentitätscontainer zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="27830-388">A conceptual model entity container maps to a storage model entity container through the EntityContainerMapping element.</span></span> <span data-ttu-id="27830-389">Ein Speichermodell-Entitätscontainer beschreibt die Struktur der Datenbank: Entitätssätze beschreiben Tabellen, Zuordnungssätze beschreiben Fremdschlüsseleinschränkungen, und Funktionsimporte beschreiben gespeicherte Prozeduren in einer Datenbank.</span><span class="sxs-lookup"><span data-stu-id="27830-389">A storage model entity container describes the structure of the database: entity sets describe tables, association sets describe foreign key constraints, and function imports describe stored procedures in a database.</span></span>

<span data-ttu-id="27830-390">Ein **EntityContainer** -Element kann über 0 (null) oder ein Dokumentations Element verfügen.</span><span class="sxs-lookup"><span data-stu-id="27830-390">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="27830-391">Wenn ein **Documentation** -Element vorhanden ist, muss es allen **EntitySet**-, **AssociationSet**-und **FunctionImport** -Elementen vorangestellt sein.</span><span class="sxs-lookup"><span data-stu-id="27830-391">If a **Documentation** element is present, it must precede all **EntitySet**, **AssociationSet**, and **FunctionImport** elements.</span></span>

<span data-ttu-id="27830-392">Ein **EntityContainer** -Element kann über 0 (null) oder mehrere der folgenden untergeordneten Elemente verfügen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-392">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-393">EntitySet</span><span class="sxs-lookup"><span data-stu-id="27830-393">EntitySet</span></span>
-   <span data-ttu-id="27830-394">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="27830-394">AssociationSet</span></span>
-   <span data-ttu-id="27830-395">FunctionImport</span><span class="sxs-lookup"><span data-stu-id="27830-395">FunctionImport</span></span>
-   <span data-ttu-id="27830-396">Anmerkungselemente</span><span class="sxs-lookup"><span data-stu-id="27830-396">Annotation elements</span></span>

<span data-ttu-id="27830-397">Sie können ein **EntityContainer** -Element erweitern, um den Inhalt eines anderen **EntityContainer** -Elements einzubeziehen, das sich im selben Namespace befindet.</span><span class="sxs-lookup"><span data-stu-id="27830-397">You can extend an **EntityContainer** element to include the contents of another **EntityContainer** that is within the same namespace.</span></span> <span data-ttu-id="27830-398">Um den Inhalt eines anderen **EntityContainer**einzuschließen, legen Sie im verweisenden **EntityContainer** -Element den Wert des Attributs " **erweitert** " auf den Namen des **EntityContainer** -Elements fest, das Sie einschließen möchten.</span><span class="sxs-lookup"><span data-stu-id="27830-398">To include the contents of another **EntityContainer**, in the referencing **EntityContainer** element, set the value of the **Extends** attribute to the name of the **EntityContainer** element that you want to include.</span></span> <span data-ttu-id="27830-399">Alle untergeordneten Elemente des enthaltenen **EntityContainer** -Elements werden als untergeordnete Elemente des verweisenden **EntityContainer** -Elements behandelt.</span><span class="sxs-lookup"><span data-stu-id="27830-399">All child elements of the included **EntityContainer** element will be treated as child elements of the referencing **EntityContainer** element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-400">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-400">Applicable Attributes</span></span>

<span data-ttu-id="27830-401">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **using** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-401">The table below describes the attributes that can be applied to the **Using** element.</span></span>

| <span data-ttu-id="27830-402">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-402">Attribute Name</span></span> | <span data-ttu-id="27830-403">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-403">Is Required</span></span> | <span data-ttu-id="27830-404">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-404">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="27830-405">**Name**</span><span class="sxs-lookup"><span data-stu-id="27830-405">**Name**</span></span>       | <span data-ttu-id="27830-406">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-406">Yes</span></span>         | <span data-ttu-id="27830-407">Der Name des Entitätscontainers.</span><span class="sxs-lookup"><span data-stu-id="27830-407">The name of the entity container.</span></span>                               |
| <span data-ttu-id="27830-408">**Erweitern**</span><span class="sxs-lookup"><span data-stu-id="27830-408">**Extends**</span></span>    | <span data-ttu-id="27830-409">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-409">No</span></span>          | <span data-ttu-id="27830-410">Der Name eines anderen Entitätscontainers innerhalb des gleichen Namespaces.</span><span class="sxs-lookup"><span data-stu-id="27830-410">The name of another entity container within the same namespace.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-411">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **EntityContainer** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-411">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="27830-412">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-412">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-413">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-413">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-414">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-414">Example</span></span>

<span data-ttu-id="27830-415">Das folgende Beispiel zeigt ein **EntityContainer** -Element, das drei Entitätenmengen und zwei Zuordnungs Sätze definiert.</span><span class="sxs-lookup"><span data-stu-id="27830-415">The following example shows an **EntityContainer** element that defines three entity sets and two association sets.</span></span>

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entityset-element-csdl"></a><span data-ttu-id="27830-416">EntitySet-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-416">EntitySet Element (CSDL)</span></span>

<span data-ttu-id="27830-417">Das **EntitySet** -Element in der konzeptionellen Schema Definitions Sprache ist ein logischer Container für Instanzen eines Entitäts Typs und Instanzen eines beliebigen Typs, der von diesem Entitätstyp abgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="27830-417">The **EntitySet** element in conceptual schema definition language is a logical container for instances of an entity type and instances of any type that is derived from that entity type.</span></span> <span data-ttu-id="27830-418">Die Beziehung zwischen einem Entitätstyp und einem Entitätssatz ist zur Beziehung zwischen einer Zeile und einer Tabelle in einer relationalen Datenbank analog.</span><span class="sxs-lookup"><span data-stu-id="27830-418">The relationship between an entity type and an entity set is analogous to the relationship between a row and a table in a relational database.</span></span> <span data-ttu-id="27830-419">Wie eine Zeile definiert ein Entitätstyp einen Satz verknüpfter Daten, und ebenso wie eine Tabelle enthält ein Entitätssatz Instanzen dieser Definition.</span><span class="sxs-lookup"><span data-stu-id="27830-419">Like a row, an entity type defines a set of related data, and, like a table, an entity set contains instances of that definition.</span></span> <span data-ttu-id="27830-420">Ein Entitätssatz stellt ein Konstrukt zum Gruppieren von Entitätstypinstanzen bereit, damit diese verwandten Datenstrukturen in einer Datenquelle zugeordnet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-420">An entity set provides a construct for grouping entity type instances so that they can be mapped to related data structures in a data source.</span></span>  

<span data-ttu-id="27830-421">Für einen bestimmten Entitätstyp kann mindestens ein Entitätssatz definiert werden.</span><span class="sxs-lookup"><span data-stu-id="27830-421">More than one entity set for a particular entity type may be defined.</span></span>

> [!NOTE]
> <span data-ttu-id="27830-422">Der EF-Designer unterstützt keine konzeptionellen Modelle, die mehrere Entitätenmengen pro Typ enthalten.</span><span class="sxs-lookup"><span data-stu-id="27830-422">The EF Designer does not support conceptual models that contain multiple entity sets per type.</span></span>

 

<span data-ttu-id="27830-423">Das **EntitySet** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-423">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-424">Dokumentationselement (kein (null) oder ein Element zugelassen)</span><span class="sxs-lookup"><span data-stu-id="27830-424">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="27830-425">Anmerkungselemente (kein (null) Element oder mehrere Elemente zugelassen)</span><span class="sxs-lookup"><span data-stu-id="27830-425">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-426">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-426">Applicable Attributes</span></span>

<span data-ttu-id="27830-427">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **EntitySet** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-427">The table below describes the attributes that can be applied to the **EntitySet** element.</span></span>

| <span data-ttu-id="27830-428">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-428">Attribute Name</span></span> | <span data-ttu-id="27830-429">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-429">Is Required</span></span> | <span data-ttu-id="27830-430">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-430">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-431">**Name**</span><span class="sxs-lookup"><span data-stu-id="27830-431">**Name**</span></span>       | <span data-ttu-id="27830-432">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-432">Yes</span></span>         | <span data-ttu-id="27830-433">Der Name des Entitätssatzes.</span><span class="sxs-lookup"><span data-stu-id="27830-433">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="27830-434">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="27830-434">**EntityType**</span></span> | <span data-ttu-id="27830-435">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-435">Yes</span></span>         | <span data-ttu-id="27830-436">Der vollqualifizierte Name des Entitätstyps, für den der Entitätssatz Instanzen enthält.</span><span class="sxs-lookup"><span data-stu-id="27830-436">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-437">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **EntitySet** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-437">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="27830-438">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-438">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-439">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-439">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-440">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-440">Example</span></span>

<span data-ttu-id="27830-441">Das folgende Beispiel zeigt ein **EntityContainer** -Element mit drei **EntitySet** -Elementen:</span><span class="sxs-lookup"><span data-stu-id="27830-441">The following example shows an **EntityContainer** element with three **EntitySet** elements:</span></span>

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

<span data-ttu-id="27830-442">Pro Typ (MEST) können mehrere Entitätssätze definiert werden.</span><span class="sxs-lookup"><span data-stu-id="27830-442">It is possible to define multiple entity sets per type (MEST).</span></span> <span data-ttu-id="27830-443">Im folgenden Beispiel wird ein Entitäts Container mit zwei Entitätenmengen für den **Book** -Entitätstyp definiert:</span><span class="sxs-lookup"><span data-stu-id="27830-443">The following example defines an entity container with two entity sets for the **Book** entity type:</span></span>

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="FictionBooks" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="BookAuthor" Association="BooksModel.BookAuthor">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entitytype-element-csdl"></a><span data-ttu-id="27830-444">EntityType-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-444">EntityType Element (CSDL)</span></span>

<span data-ttu-id="27830-445">Das **EntityType** -Element stellt die Struktur eines Konzepts der obersten Ebene, z. b. einen Kunden oder eine Bestellung, in einem konzeptionellen Modell dar.</span><span class="sxs-lookup"><span data-stu-id="27830-445">The **EntityType** element represents the structure of a top-level concept, such as a customer or order, in a conceptual model.</span></span> <span data-ttu-id="27830-446">Ein Entitätstyp ist eine Vorlage für Instanzen von Entitätstypen in einer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="27830-446">An entity type is a template for instances of entity types in an application.</span></span> <span data-ttu-id="27830-447">Jede Vorlage enthält die folgenden Informationen:</span><span class="sxs-lookup"><span data-stu-id="27830-447">Each template contains the following information:</span></span>

-   <span data-ttu-id="27830-448">Eine eindeutige Bezeichnung.</span><span class="sxs-lookup"><span data-stu-id="27830-448">A unique name.</span></span> <span data-ttu-id="27830-449">(Erforderlich.)</span><span class="sxs-lookup"><span data-stu-id="27830-449">(Required.)</span></span>
-   <span data-ttu-id="27830-450">Ein Entitätsschlüssel, der von einem oder mehreren Eigenschaften definiert wird.</span><span class="sxs-lookup"><span data-stu-id="27830-450">An entity key that is defined by one or more properties.</span></span> <span data-ttu-id="27830-451">(Erforderlich.)</span><span class="sxs-lookup"><span data-stu-id="27830-451">(Required.)</span></span>
-   <span data-ttu-id="27830-452">Eigenschaften für enthaltene Daten.</span><span class="sxs-lookup"><span data-stu-id="27830-452">Properties for containing data.</span></span> <span data-ttu-id="27830-453">(Optional.)</span><span class="sxs-lookup"><span data-stu-id="27830-453">(Optional.)</span></span>
-   <span data-ttu-id="27830-454">Navigationseigenschaften, die die Navigation von einem Ende einer Zuordnung zum anderen Ende ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="27830-454">Navigation properties that allow for navigation from one end of an association to the other end.</span></span> <span data-ttu-id="27830-455">(Optional.)</span><span class="sxs-lookup"><span data-stu-id="27830-455">(Optional.)</span></span>

<span data-ttu-id="27830-456">In einer Anwendung stellt eine Instanz eines Entitätstyps ein spezielles Objekt dar, wie etwa einen bestimmten Kunden oder eine Bestellung.</span><span class="sxs-lookup"><span data-stu-id="27830-456">In an application, an instance of an entity type represents a specific object (such as a specific customer or order).</span></span> <span data-ttu-id="27830-457">Jede Instanz eines Entitätstyps muss über einen eindeutigen Entitätsschlüssel innerhalb eines Entitätssatzes verfügen.</span><span class="sxs-lookup"><span data-stu-id="27830-457">Each instance of an entity type must have a unique entity key within an entity set.</span></span>

<span data-ttu-id="27830-458">Zwei Instanzen eines Entitätstyps werden nur dann als gleich betrachtet, wenn sie vom selben Typ sind und die Werte ihrer Entitätsschlüssel übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-458">Two entity type instances are considered equal only if they are of the same type and the values of their entity keys are the same.</span></span>

<span data-ttu-id="27830-459">Ein **EntityType** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-459">An **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-460">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-460">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="27830-461">Key (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-461">Key (zero or one element)</span></span>
-   <span data-ttu-id="27830-462">Property (0 (null) oder mehr Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-462">Property (zero or more elements)</span></span>
-   <span data-ttu-id="27830-463">NavigationProperty (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-463">NavigationProperty (zero or more elements)</span></span>
-   <span data-ttu-id="27830-464">Anmerkungselemente (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-464">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-465">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-465">Applicable Attributes</span></span>

<span data-ttu-id="27830-466">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **EntityType** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-466">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="27830-467">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-467">Attribute Name</span></span>                                                                                                                                  | <span data-ttu-id="27830-468">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-468">Is Required</span></span> | <span data-ttu-id="27830-469">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-469">Value</span></span>                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-470">**Name**</span><span class="sxs-lookup"><span data-stu-id="27830-470">**Name**</span></span>                                                                                                                                        | <span data-ttu-id="27830-471">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-471">Yes</span></span>         | <span data-ttu-id="27830-472">Der Name des Entitätstyps.</span><span class="sxs-lookup"><span data-stu-id="27830-472">The name of the entity type.</span></span>                                                                     |
| <span data-ttu-id="27830-473">**BaseType**</span><span class="sxs-lookup"><span data-stu-id="27830-473">**BaseType**</span></span>                                                                                                                                    | <span data-ttu-id="27830-474">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-474">No</span></span>          | <span data-ttu-id="27830-475">Der Name eines anderen Entitätstyps, der der Basistyp des Entitätstyps ist, der definiert wird.</span><span class="sxs-lookup"><span data-stu-id="27830-475">The name of another entity type that is the base type of the entity type that is being defined.</span></span>  |
| <span data-ttu-id="27830-476">**Kter**</span><span class="sxs-lookup"><span data-stu-id="27830-476">**Abstract**</span></span>                                                                                                                                    | <span data-ttu-id="27830-477">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-477">No</span></span>          | <span data-ttu-id="27830-478">**True** oder **false**, abhängig davon, ob der Entitätstyp ein abstrakter Typ ist.</span><span class="sxs-lookup"><span data-stu-id="27830-478">**True** or **False**, depending on whether the entity type is an abstract type.</span></span>                 |
| <span data-ttu-id="27830-479">**OpenType**</span><span class="sxs-lookup"><span data-stu-id="27830-479">**OpenType**</span></span>                                                                                                                                    | <span data-ttu-id="27830-480">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-480">No</span></span>          | <span data-ttu-id="27830-481">**True** oder **false** , abhängig davon, ob der Entitätstyp ein offener Entitätstyp ist.</span><span class="sxs-lookup"><span data-stu-id="27830-481">**True** or **False** depending on whether the entity type is an open entity type.</span></span> <br/> [!NOTE] |
| <span data-ttu-id="27830-482">> das **OpenType** -Attribut gilt nur für Entitäts Typen, die in konzeptionellen Modellen definiert sind, die mit ADO.NET-Data Services verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-482">> The **OpenType** attribute is only applicable to entity types that are defined in conceptual models that are used with ADO.NET Data Services.</span></span> |             |                                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="27830-483">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **EntityType** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-483">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="27830-484">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-484">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-485">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-485">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-486">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-486">Example</span></span>

<span data-ttu-id="27830-487">Das folgende Beispiel zeigt ein **EntityType** -Element mit drei **Eigenschafts** Elementen und zwei **NavigationProperty** -Elementen:</span><span class="sxs-lookup"><span data-stu-id="27830-487">The following example shows an **EntityType** element with three **Property** elements and two **NavigationProperty** elements:</span></span>

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

 

## <a name="enumtype-element-csdl"></a><span data-ttu-id="27830-488">EnumType-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-488">EnumType Element (CSDL)</span></span>

<span data-ttu-id="27830-489">Das **enumType** -Element stellt einen enumerierten Typ dar.</span><span class="sxs-lookup"><span data-stu-id="27830-489">The **EnumType** element represents an enumerated type.</span></span>

<span data-ttu-id="27830-490">Ein **enumType** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-490">An **EnumType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-491">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-491">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="27830-492">Member (0 (null) oder mehr Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-492">Member (zero or more elements)</span></span>
-   <span data-ttu-id="27830-493">Anmerkungselemente (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-493">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-494">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-494">Applicable Attributes</span></span>

<span data-ttu-id="27830-495">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **enumType** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-495">The table below describes the attributes that can be applied to the **EnumType** element.</span></span>

| <span data-ttu-id="27830-496">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-496">Attribute Name</span></span>     | <span data-ttu-id="27830-497">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-497">Is Required</span></span> | <span data-ttu-id="27830-498">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-498">Value</span></span>                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-499">**Name**</span><span class="sxs-lookup"><span data-stu-id="27830-499">**Name**</span></span>           | <span data-ttu-id="27830-500">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-500">Yes</span></span>         | <span data-ttu-id="27830-501">Der Name des Entitätstyps.</span><span class="sxs-lookup"><span data-stu-id="27830-501">The name of the entity type.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="27830-502">**IsFlags**</span><span class="sxs-lookup"><span data-stu-id="27830-502">**IsFlags**</span></span>        | <span data-ttu-id="27830-503">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-503">No</span></span>          | <span data-ttu-id="27830-504">**True** oder **false**, abhängig davon, ob der Enumeration-Typ als Satz von Flags verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="27830-504">**True** or **False**, depending on whether the enum type can be used as a set of flags.</span></span> <span data-ttu-id="27830-505">Der Standardwert ist **false.**</span><span class="sxs-lookup"><span data-stu-id="27830-505">The default value is **False.**.</span></span>                                                                     |
| <span data-ttu-id="27830-506">**Underlyingtype**</span><span class="sxs-lookup"><span data-stu-id="27830-506">**UnderlyingType**</span></span> | <span data-ttu-id="27830-507">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-507">No</span></span>          | <span data-ttu-id="27830-508">**Edm. Byte**, **Edm. Int16**, **Edm. Int32**, **Edm. Int64** oder **Edm. SByte** , das den Wertebereich des Typs definiert.</span><span class="sxs-lookup"><span data-stu-id="27830-508">**Edm.Byte**, **Edm.Int16**, **Edm.Int32**, **Edm.Int64** or **Edm.SByte** defining the range of values of the type.</span></span> <span data-ttu-id="27830-509">Der Standard zugrunde liegende Typ der Enumerationselemente ist **Edm. Int32.**.</span><span class="sxs-lookup"><span data-stu-id="27830-509">  The default underlying type of enumeration elements is **Edm.Int32.**.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-510">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **enumType** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-510">Any number of annotation attributes (custom XML attributes) may be applied to the **EnumType** element.</span></span> <span data-ttu-id="27830-511">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-511">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-512">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-512">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-513">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-513">Example</span></span>

<span data-ttu-id="27830-514">Das folgende Beispiel zeigt ein **enumType** -Element mit drei **Member** -Elementen:</span><span class="sxs-lookup"><span data-stu-id="27830-514">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a><span data-ttu-id="27830-515">Function-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-515">Function Element (CSDL)</span></span>

<span data-ttu-id="27830-516">Das **Function** -Element in der konzeptionellen Schema Definitions Sprache (CSDL) wird verwendet, um Funktionen im konzeptionellen Modell zu definieren oder zu deklarieren.</span><span class="sxs-lookup"><span data-stu-id="27830-516">The **Function** element in conceptual schema definition language (CSDL) is used to define or declare functions in the conceptual model.</span></span> <span data-ttu-id="27830-517">Eine Funktion wird mit einem DefiningExpression-Element definiert.</span><span class="sxs-lookup"><span data-stu-id="27830-517">A function is defined by using a DefiningExpression element.</span></span>  

<span data-ttu-id="27830-518">Ein **Function** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-518">A **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-519">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-519">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="27830-520">Parameter (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-520">Parameter (zero or more elements)</span></span>
-   <span data-ttu-id="27830-521">DefiningExpression (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-521">DefiningExpression (zero or one element)</span></span>
-   <span data-ttu-id="27830-522">ReturnType (Funktion) (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-522">ReturnType (Function) (zero or one element)</span></span>
-   <span data-ttu-id="27830-523">Anmerkungselemente (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-523">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="27830-524">Ein Rückgabetyp für eine Funktion muss entweder mit dem **returnType** -Element (Function) oder dem **returnType** -Attribut angegeben werden (siehe unten), aber nicht mit beiden.</span><span class="sxs-lookup"><span data-stu-id="27830-524">A return type for a function must be specified with either the **ReturnType** (Function) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="27830-525">Die möglichen Rückgabetypen sind alle EdmSimpleType-Typen, Entitätstypen, komplexe Typen, Zeilentypen (oder eine Auflistung eines dieser Typen) oder Ref-Typen.</span><span class="sxs-lookup"><span data-stu-id="27830-525">The possible return types are any EdmSimpleType, entity type, complex type, row type, or ref type (or a collection of one of these types).</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-526">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-526">Applicable Attributes</span></span>

<span data-ttu-id="27830-527">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Function** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-527">The table below describes the attributes that can be applied to the **Function** element.</span></span>

| <span data-ttu-id="27830-528">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-528">Attribute Name</span></span> | <span data-ttu-id="27830-529">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-529">Is Required</span></span> | <span data-ttu-id="27830-530">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-530">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="27830-531">**Name**</span><span class="sxs-lookup"><span data-stu-id="27830-531">**Name**</span></span>       | <span data-ttu-id="27830-532">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-532">Yes</span></span>         | <span data-ttu-id="27830-533">Der Name der Funktion.</span><span class="sxs-lookup"><span data-stu-id="27830-533">The name of the function.</span></span>          |
| <span data-ttu-id="27830-534">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="27830-534">**ReturnType**</span></span> | <span data-ttu-id="27830-535">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-535">No</span></span>          | <span data-ttu-id="27830-536">Der von der Funktion zurückgegebene Typ.</span><span class="sxs-lookup"><span data-stu-id="27830-536">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-537">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Function** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-537">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="27830-538">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-538">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-539">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-539">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-540">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-540">Example</span></span>

<span data-ttu-id="27830-541">Im folgenden Beispiel wird ein **Function** -Element verwendet, um eine Funktion zu definieren, die die Anzahl der Jahre zurückgibt, seit ein Dozenten eingestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="27830-541">The following example uses a **Function** element to define a function that returns the number of years since an instructor was hired.</span></span>

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a><span data-ttu-id="27830-542">FunctionImport-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-542">FunctionImport Element (CSDL)</span></span>

<span data-ttu-id="27830-543">Das **FunctionImport** -Element in konzeptioneller Schema Definitions Sprache (CSDL) stellt eine Funktion dar, die in der Datenquelle definiert ist, aber für-Objekte über das konzeptionelle Modell verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="27830-543">The **FunctionImport** element in conceptual schema definition language (CSDL) represents a function that is defined in the data source but available to objects through the conceptual model.</span></span> <span data-ttu-id="27830-544">Ein Function-Element im Speichermodell kann z. B. verwendet werden, um eine gespeicherte Prozedur in einer Datenbank darzustellen.</span><span class="sxs-lookup"><span data-stu-id="27830-544">For example, a Function element in the storage model can be used to represent a stored procedure in a database.</span></span> <span data-ttu-id="27830-545">Ein **FunctionImport** -Element im konzeptionellen Modell stellt die entsprechende Funktion in einer Entity Framework Anwendung dar und wird mithilfe des FunctionImportMapping-Elements der Speichermodell Funktion zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="27830-545">A **FunctionImport** element in the conceptual model represents the corresponding function in an Entity Framework application and is mapped to the storage model function by using the FunctionImportMapping element.</span></span> <span data-ttu-id="27830-546">Wird die Funktion in der Anwendung aufgerufen, wird die entsprechende gespeicherte Prozedur in der Datenbank ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="27830-546">When the function is called in the application, the corresponding stored procedure is executed in the database.</span></span>

<span data-ttu-id="27830-547">Das **FunctionImport** -Element kann über die folgenden untergeordneten Elemente verfügen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-547">The **FunctionImport** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-548">Dokumentation (kein (null) oder ein Element zugelassen)</span><span class="sxs-lookup"><span data-stu-id="27830-548">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="27830-549">Parameter (kein Element (null) oder mehrere Elemente zugelassen)</span><span class="sxs-lookup"><span data-stu-id="27830-549">Parameter (zero or more elements allowed)</span></span>
-   <span data-ttu-id="27830-550">Anmerkungselemente (kein (null) Element oder mehrere Elemente zugelassen)</span><span class="sxs-lookup"><span data-stu-id="27830-550">Annotation elements (zero or more elements allowed)</span></span>
-   <span data-ttu-id="27830-551">ReturnType (FunctionImport) (0 (null) oder mehr Elemente zulässig)</span><span class="sxs-lookup"><span data-stu-id="27830-551">ReturnType (FunctionImport) (zero or more elements allowed)</span></span>

<span data-ttu-id="27830-552">Für jeden Parameter, den die Funktion akzeptiert, muss ein **Parameter** Element definiert werden.</span><span class="sxs-lookup"><span data-stu-id="27830-552">One **Parameter** element should be defined for each parameter that the function accepts.</span></span>

<span data-ttu-id="27830-553">Ein Rückgabetyp für eine Funktion muss entweder mit dem **returnType** -Element (FunctionImport) oder dem **returnType** -Attribut angegeben werden (siehe unten), aber nicht mit beiden.</span><span class="sxs-lookup"><span data-stu-id="27830-553">A return type for a function must be specified with either the **ReturnType** (FunctionImport) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="27830-554">Der Rückgabetyp Wert muss eine Auflistung von edmsimpletype, EntityType oder complexType sein.</span><span class="sxs-lookup"><span data-stu-id="27830-554">The return type value must be a  collection of EdmSimpleType, EntityType, or ComplexType.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-555">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-555">Applicable Attributes</span></span>

<span data-ttu-id="27830-556">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **FunctionImport** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-556">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="27830-557">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-557">Attribute Name</span></span>   | <span data-ttu-id="27830-558">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-558">Is Required</span></span> | <span data-ttu-id="27830-559">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-559">Value</span></span>                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-560">**Name**</span><span class="sxs-lookup"><span data-stu-id="27830-560">**Name**</span></span>         | <span data-ttu-id="27830-561">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-561">Yes</span></span>         | <span data-ttu-id="27830-562">Der Name der importierten Funktion.</span><span class="sxs-lookup"><span data-stu-id="27830-562">The name of the imported function.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="27830-563">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="27830-563">**ReturnType**</span></span>   | <span data-ttu-id="27830-564">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-564">No</span></span>          | <span data-ttu-id="27830-565">Der Typ, den die Funktion zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="27830-565">The type that the function returns.</span></span> <span data-ttu-id="27830-566">Verwenden Sie dieses Attribut nicht, wenn die Funktion keinen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="27830-566">Do not use this attribute if the function does not return a value.</span></span> <span data-ttu-id="27830-567">Andernfalls muss der Wert eine Auflistung von complexType, EntityType oder edmsimpletype sein.</span><span class="sxs-lookup"><span data-stu-id="27830-567">Otherwise, the value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>        |
| <span data-ttu-id="27830-568">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="27830-568">**EntitySet**</span></span>    | <span data-ttu-id="27830-569">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-569">No</span></span>          | <span data-ttu-id="27830-570">Wenn die Funktion eine Auflistung von Entitäts Typen zurückgibt, muss der Wert von **EntitySet** der Entitätenmenge angehören, zu der die Auflistung gehört.</span><span class="sxs-lookup"><span data-stu-id="27830-570">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="27830-571">Andernfalls darf das Attribut **EntitySet** nicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-571">Otherwise, the **EntitySet** attribute must not be used.</span></span> |
| <span data-ttu-id="27830-572">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="27830-572">**IsComposable**</span></span> | <span data-ttu-id="27830-573">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-573">No</span></span>          | <span data-ttu-id="27830-574">Wenn der Wert auf true festgelegt ist, ist die Funktion zusammensetzbar (Tabellenwert Funktion) und kann in einer LINQ-Abfrage verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-574">If the value is set to true, the function is composable (Table-valued Function) and can be used in a LINQ query.</span></span><span data-ttu-id="27830-575">Die Standardeinstellung ist **false**.</span><span class="sxs-lookup"><span data-stu-id="27830-575">  The default is **false**.</span></span>                                                           |

 

> [!NOTE]
> <span data-ttu-id="27830-576">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **FunctionImport** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-576">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="27830-577">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-577">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-578">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-578">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-579">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-579">Example</span></span>

<span data-ttu-id="27830-580">Das folgende Beispiel zeigt ein **FunctionImport** -Element, das einen Parameter akzeptiert und eine Auflistung von Entitäts Typen zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="27830-580">The following example shows a **FunctionImport** element that accepts one parameter and returns a collection of entity types:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a><span data-ttu-id="27830-581">Key-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-581">Key Element (CSDL)</span></span>

<span data-ttu-id="27830-582">Das **Key** -Element ist ein untergeordnetes Element des EntityType-Elements und definiert einen *Entitäts Schlüssel* (eine Eigenschaft oder einen Satz von Eigenschaften eines Entitäts Typs, der die Identität bestimmt).</span><span class="sxs-lookup"><span data-stu-id="27830-582">The **Key** element is a child element of the EntityType element and defines an *entity key* (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="27830-583">Die Eigenschaften, die einen Entitätsschlüssel bilden, werden zur Entwurfszeit ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="27830-583">The properties that make up an entity key are chosen at design time.</span></span> <span data-ttu-id="27830-584">Die Werte von Entitätsschlüsseleigenschaften müssen zur Laufzeit eindeutig eine Entitätstypinstanz innerhalb eines Entitätssatzes identifizieren.</span><span class="sxs-lookup"><span data-stu-id="27830-584">The values of entity key properties must uniquely identify an entity type instance within an entity set at run time.</span></span> <span data-ttu-id="27830-585">Die Eigenschaften, die einen Entitätsschlüssel bilden, sollten so ausgewählt werden, dass die Eindeutigkeit von Instanzen in einem Entitätssatz gewährleistet ist.</span><span class="sxs-lookup"><span data-stu-id="27830-585">The properties that make up an entity key should be chosen to guarantee uniqueness of instances in an entity set.</span></span> <span data-ttu-id="27830-586">Das **Key** -Element definiert einen Entitäts Schlüssel, indem auf eine oder mehrere Eigenschaften eines Entitäts Typs verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="27830-586">The **Key** element defines an entity key by referencing one or more of the properties of an entity type.</span></span>

<span data-ttu-id="27830-587">Das **Key** -Element kann die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="27830-587">The **Key** element can have the following child elements:</span></span>

-   <span data-ttu-id="27830-588">PropertyRef (mindestens ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-588">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="27830-589">Anmerkungselemente (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-589">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-590">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-590">Applicable Attributes</span></span>

<span data-ttu-id="27830-591">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Schlüssel** Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-591">Any number of annotation attributes (custom XML attributes) may be applied to the **Key** element.</span></span> <span data-ttu-id="27830-592">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-592">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-593">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-593">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="27830-594">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-594">Example</span></span>

<span data-ttu-id="27830-595">Im folgenden Beispiel wird ein Entitätstyp mit dem Namen **Book**definiert.</span><span class="sxs-lookup"><span data-stu-id="27830-595">The example below defines an entity type named **Book**.</span></span> <span data-ttu-id="27830-596">Der Entitäts Schlüssel wird definiert, indem auf die **ISBN** -Eigenschaft des Entitäts Typs verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="27830-596">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

<span data-ttu-id="27830-597">Die **ISBN** -Eigenschaft ist eine gute Wahl für den Entitäts Schlüssel, weil eine internationale Standard Buchnummer (ISBN) ein Buch eindeutig identifiziert.</span><span class="sxs-lookup"><span data-stu-id="27830-597">The **ISBN** property is a good choice for the entity key because an International Standard Book Number (ISBN) uniquely identifies a book.</span></span>

<span data-ttu-id="27830-598">Das folgende Beispiel zeigt einen Entitätstyp (**Author**), der über einen Entitäts Schlüssel verfügt, der aus zwei Eigenschaften, **Name** und **Adresse**besteht.</span><span class="sxs-lookup"><span data-stu-id="27830-598">The following example shows an entity type (**Author**) that has an entity key that consists of two properties, **Name** and **Address**.</span></span>

``` xml
 <EntityType Name="Author">
   <Key>
     <PropertyRef Name="Name" />
     <PropertyRef Name="Address" />
   </Key>
   <Property Type="String" Name="Name" Nullable="false" />
   <Property Type="String" Name="Address" Nullable="false" />
   <NavigationProperty Name="Books" Relationship="BooksModel.WrittenBy"
                       FromRole="Author" ToRole="Book" />
 </EntityType>
```
 

<span data-ttu-id="27830-599">Die Verwendung von **Name** und **Adresse** für den Entitäts Schlüssel ist eine sinnvolle Wahl, da es unwahrscheinlich ist, dass sich zwei Autoren mit demselben Namen an derselben Adresse befinden.</span><span class="sxs-lookup"><span data-stu-id="27830-599">Using **Name** and **Address** for the entity key is a reasonable choice, because two authors of the same name are unlikely to live at the same address.</span></span> <span data-ttu-id="27830-600">Dieser Entitätsschlüssel garantiert jedoch nicht absolut eindeutige Entitätsschlüssel in einem Entitätssatz.</span><span class="sxs-lookup"><span data-stu-id="27830-600">However, this choice for an entity key does not absolutely guarantee unique entity keys in an entity set.</span></span> <span data-ttu-id="27830-601">In diesem Fall empfiehlt es sich, eine Eigenschaft hinzuzufügen, z. **b. Autorität**, die zur eindeutigen Identifizierung eines Autors verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="27830-601">Adding a property, such as **AuthorId**, that could be used to uniquely identify an author would be recommended in this case.</span></span>

 

## <a name="member-element-csdl"></a><span data-ttu-id="27830-602">Member-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-602">Member Element (CSDL)</span></span>

<span data-ttu-id="27830-603">Das **Member** -Element ist ein untergeordnetes Element des enumType-Elements und definiert einen Member des enumerierten Typs.</span><span class="sxs-lookup"><span data-stu-id="27830-603">The **Member** element is a child element of the EnumType element and defines a member of the enumerated type.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-604">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-604">Applicable Attributes</span></span>

<span data-ttu-id="27830-605">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **FunctionImport** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-605">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="27830-606">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-606">Attribute Name</span></span> | <span data-ttu-id="27830-607">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-607">Is Required</span></span> | <span data-ttu-id="27830-608">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-608">Value</span></span>                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-609">**Name**</span><span class="sxs-lookup"><span data-stu-id="27830-609">**Name**</span></span>       | <span data-ttu-id="27830-610">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-610">Yes</span></span>         | <span data-ttu-id="27830-611">Der Name des Members.</span><span class="sxs-lookup"><span data-stu-id="27830-611">The name of the member.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="27830-612">**Wert**</span><span class="sxs-lookup"><span data-stu-id="27830-612">**Value**</span></span>      | <span data-ttu-id="27830-613">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-613">No</span></span>          | <span data-ttu-id="27830-614">Der Wert des Members.</span><span class="sxs-lookup"><span data-stu-id="27830-614">The value of the member.</span></span> <span data-ttu-id="27830-615">Standardmäßig hat der erste Member den Wert 0, und der Wert jedes nachfolgenden Enumerators wird um 1 erhöht.</span><span class="sxs-lookup"><span data-stu-id="27830-615">By default, the first member has the value 0, and the value of each successive enumerator is incremented by 1.</span></span> <span data-ttu-id="27830-616">Es können mehrere Member mit denselben Werten vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="27830-616">Multiple members with the same values may exist.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-617">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **FunctionImport** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-617">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="27830-618">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-618">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-619">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-619">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-620">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-620">Example</span></span>

<span data-ttu-id="27830-621">Das folgende Beispiel zeigt ein **enumType** -Element mit drei **Member** -Elementen:</span><span class="sxs-lookup"><span data-stu-id="27830-621">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a><span data-ttu-id="27830-622">NavigationProperty-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-622">NavigationProperty Element (CSDL)</span></span>

<span data-ttu-id="27830-623">Ein **NavigationProperty** -Element definiert eine Navigations Eigenschaft, die einen Verweis auf das andere Ende einer Zuordnung bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="27830-623">A **NavigationProperty** element defines a navigation property, which provides a reference to the other end of an association.</span></span> <span data-ttu-id="27830-624">Im Gegensatz zu mit dem Property-Element definierten Eigenschaften werden von Navigationseigenschaften Form und Eigenschaften von Daten nicht definiert.</span><span class="sxs-lookup"><span data-stu-id="27830-624">Unlike properties defined with the Property element, navigation properties do not define the shape and characteristics of data.</span></span> <span data-ttu-id="27830-625">Sie bieten eine Möglichkeit, eine Zuordnung zwischen zwei Entitätstypen zu navigieren.</span><span class="sxs-lookup"><span data-stu-id="27830-625">They provide a way to navigate an association between two entity types.</span></span>

<span data-ttu-id="27830-626">Beachten Sie, dass Navigationseigenschaften für beide Entitätstypen an den Enden einer Zuordnung optional sind.</span><span class="sxs-lookup"><span data-stu-id="27830-626">Note that navigation properties are optional on both entity types at the ends of an association.</span></span> <span data-ttu-id="27830-627">Wenn Sie für einen Entitätstyp am Ende einer Zuordnung eine Navigationseigenschaft definieren, muss keine Navigationseigenschaft für den Entitätstyp am anderen Ende der Zuordnung definiert werden.</span><span class="sxs-lookup"><span data-stu-id="27830-627">If you define a navigation property on one entity type at the end of an association, you do not have to define a navigation property on the entity type at the other end of the association.</span></span>

<span data-ttu-id="27830-628">Der von einer Navigationseigenschaft zurückgegebene Datentyp wird von der Multiplizität des Remotezuordnungsendes bestimmt.</span><span class="sxs-lookup"><span data-stu-id="27830-628">The data type returned by a navigation property is determined by the multiplicity of its remote association end.</span></span> <span data-ttu-id="27830-629">Angenommen, eine Navigations Eigenschaft, **ordersnavprop**, ist für einen **Customer** -Entitätstyp vorhanden und navigiert zu einer 1: n-Zuordnung zwischen **Customer** und **Order**.</span><span class="sxs-lookup"><span data-stu-id="27830-629">For example, suppose a navigation property, **OrdersNavProp**, exists on a **Customer** entity type and navigates a one-to-many association between **Customer** and **Order**.</span></span> <span data-ttu-id="27830-630">Da das Remote Zuordnungs Ende für die Navigations Eigenschaft eine Multiplizität many ( \* ) aufweist, ist sein Datentyp eine Auflistung (der **Reihenfolge**).</span><span class="sxs-lookup"><span data-stu-id="27830-630">Because the remote association end for the navigation property has multiplicity many (\*), its data type is a collection (of **Order**).</span></span> <span data-ttu-id="27830-631">Wenn eine Navigations Eigenschaft, **customernavprop**, auf dem **Order** -Entitätstyp vorhanden ist, wäre der Datentyp " **Customer** ", da die Multiplizität des Remote Endes "eins" (1) ist.</span><span class="sxs-lookup"><span data-stu-id="27830-631">Similarly, if a navigation property, **CustomerNavProp**, exists on the **Order** entity type, its data type would be **Customer** since the multiplicity of the remote end is one (1).</span></span>

<span data-ttu-id="27830-632">Ein **NavigationProperty** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-632">A **NavigationProperty** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-633">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-633">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="27830-634">Anmerkungselemente (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-634">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-635">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-635">Applicable Attributes</span></span>

<span data-ttu-id="27830-636">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **NavigationProperty** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-636">The table below describes the attributes that can be applied to the **NavigationProperty** element.</span></span>

| <span data-ttu-id="27830-637">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-637">Attribute Name</span></span>   | <span data-ttu-id="27830-638">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-638">Is Required</span></span> | <span data-ttu-id="27830-639">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-639">Value</span></span>                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-640">**Name**</span><span class="sxs-lookup"><span data-stu-id="27830-640">**Name**</span></span>         | <span data-ttu-id="27830-641">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-641">Yes</span></span>         | <span data-ttu-id="27830-642">Der Name der Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="27830-642">The name of the navigation property.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="27830-643">**Relationship**</span><span class="sxs-lookup"><span data-stu-id="27830-643">**Relationship**</span></span> | <span data-ttu-id="27830-644">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-644">Yes</span></span>         | <span data-ttu-id="27830-645">Der Name einer Zuordnung, die sich innerhalb des Bereichs des Modells befindet.</span><span class="sxs-lookup"><span data-stu-id="27830-645">The name of an association that is within the scope of the model.</span></span>                                                                                                                                                                                |
| <span data-ttu-id="27830-646">**ToRole**</span><span class="sxs-lookup"><span data-stu-id="27830-646">**ToRole**</span></span>       | <span data-ttu-id="27830-647">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-647">Yes</span></span>         | <span data-ttu-id="27830-648">Das Ende der Zuordnung, an dem die Navigation endet.</span><span class="sxs-lookup"><span data-stu-id="27830-648">The end of the association at which navigation ends.</span></span> <span data-ttu-id="27830-649">Der Wert des Attributs " **Tor** " muss mit dem Wert eines der **Rollen** Attribute übereinstimmen, die für eine der Zuordnungs enden definiert sind (definiert im AssociationEnd-Element).</span><span class="sxs-lookup"><span data-stu-id="27830-649">The value of the **ToRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span>       |
| <span data-ttu-id="27830-650">**FromRole**</span><span class="sxs-lookup"><span data-stu-id="27830-650">**FromRole**</span></span>     | <span data-ttu-id="27830-651">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-651">Yes</span></span>         | <span data-ttu-id="27830-652">Das Ende der Zuordnung, an dem die Navigation beginnt.</span><span class="sxs-lookup"><span data-stu-id="27830-652">The end of the association from which navigation begins.</span></span> <span data-ttu-id="27830-653">Der Wert des **FromRole** -Attributs muss mit dem Wert eines der **Rollen** Attribute übereinstimmen, die für eine der Zuordnungs enden definiert sind (definiert im AssociationEnd-Element).</span><span class="sxs-lookup"><span data-stu-id="27830-653">The value of the **FromRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-654">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **NavigationProperty** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-654">Any number of annotation attributes (custom XML attributes) may be applied to the **NavigationProperty** element.</span></span> <span data-ttu-id="27830-655">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-655">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-656">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-656">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-657">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-657">Example</span></span>

<span data-ttu-id="27830-658">Im folgenden Beispiel wird ein Entitätstyp (**Book**) mit zwei Navigations Eigenschaften (**PublishedBy** und **Schreibweise**) definiert:</span><span class="sxs-lookup"><span data-stu-id="27830-658">The following example defines an entity type (**Book**) with two navigation properties (**PublishedBy** and **WrittenBy**):</span></span>

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

 

## <a name="ondelete-element-csdl"></a><span data-ttu-id="27830-659">OnDelete-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-659">OnDelete Element (CSDL)</span></span>

<span data-ttu-id="27830-660">Das **OnDelete** -Element in konzeptioneller Schema Definitions Sprache (CSDL) definiert das Verhalten, das mit einer Zuordnung verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="27830-660">The **OnDelete** element in conceptual schema definition language (CSDL) defines behavior that is connected with an association.</span></span> <span data-ttu-id="27830-661">Wenn das **Action** -Attribut an einem Ende einer Zuordnung auf **Cascade** festgelegt ist, werden verwandte Entitäts Typen am anderen Ende der Zuordnung gelöscht, wenn der Entitätstyp am ersten Ende gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="27830-661">If the **Action** attribute is set to **Cascade** on one end of an association, related entity types on the other end of the association are deleted when the entity type on the first end is deleted.</span></span> <span data-ttu-id="27830-662">Wenn die Zuordnung zwischen zwei Entitäts Typen eine Primärschlüssel-zu-Primärschlüssel-Beziehung ist, wird ein geladenes abhängiges Objekt gelöscht, wenn das Prinzipal Objekt am anderen Ende der Zuordnung unabhängig von der **OnDelete** -Spezifikation gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="27830-662">If the association between two entity types is a primary key-to-primary key relationship, then a loaded dependent object is deleted when the principal object on the other end of the association is deleted regardless of the **OnDelete** specification.</span></span>  

> [!NOTE]
> <span data-ttu-id="27830-663">Das **OnDelete** -Element wirkt sich nur auf das Laufzeitverhalten einer Anwendung aus. Dies wirkt sich nicht auf das Verhalten in der Datenquelle aus.</span><span class="sxs-lookup"><span data-stu-id="27830-663">The **OnDelete** element only affects the runtime behavior of an application; it does not affect behavior in the data source.</span></span> <span data-ttu-id="27830-664">Das in der Datenquelle definierte Verhalten sollte dem in der Anwendung definierten Verhalten entsprechen.</span><span class="sxs-lookup"><span data-stu-id="27830-664">The behavior defined in the data source should be the same as the behavior defined in the application.</span></span>

 

<span data-ttu-id="27830-665">Ein **OnDelete** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-665">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-666">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-666">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="27830-667">Anmerkungselemente (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-667">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-668">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-668">Applicable Attributes</span></span>

<span data-ttu-id="27830-669">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **OnDelete** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-669">The table below describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="27830-670">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-670">Attribute Name</span></span> | <span data-ttu-id="27830-671">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-671">Is Required</span></span> | <span data-ttu-id="27830-672">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-672">Value</span></span>                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-673">**Aktion**</span><span class="sxs-lookup"><span data-stu-id="27830-673">**Action**</span></span>     | <span data-ttu-id="27830-674">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-674">Yes</span></span>         | <span data-ttu-id="27830-675">**Cascade** oder **None**.</span><span class="sxs-lookup"><span data-stu-id="27830-675">**Cascade** or **None**.</span></span> <span data-ttu-id="27830-676">Wenn **Cascade**verwendet wird, werden abhängige Entitäts Typen gelöscht, wenn der Prinzipal Entitätstyp gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="27830-676">If **Cascade**, dependent entity types will be deleted when the principal entity type is deleted.</span></span> <span data-ttu-id="27830-677">Wenn **keine**, werden abhängige Entitäts Typen nicht gelöscht, wenn der Prinzipal Entitätstyp gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="27830-677">If **None**, dependent entity types will not be deleted when the principal entity type is deleted.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-678">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Association** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-678">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="27830-679">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-679">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-680">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-680">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-681">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-681">Example</span></span>

<span data-ttu-id="27830-682">Das folgende Beispiel zeigt ein **Association** -Element, das die Zuordnung **CustomerOrders** definiert.</span><span class="sxs-lookup"><span data-stu-id="27830-682">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="27830-683">Das **OnDelete** -Element gibt an, dass alle **Bestellungen** , die mit einem bestimmten **Kunden** verknüpft sind und in den ObjectContext geladen wurden, gelöscht werden, wenn der **Kunde** gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="27830-683">The **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted when the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a><span data-ttu-id="27830-684">Parameter-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-684">Parameter Element (CSDL)</span></span>

<span data-ttu-id="27830-685">Das **Parameter** -Element in konzeptioneller Schema Definitions Sprache (CSDL) kann ein untergeordnetes Element des FunctionImport-Elements oder des Function-Elements sein.</span><span class="sxs-lookup"><span data-stu-id="27830-685">The **Parameter** element in conceptual schema definition language (CSDL) can be a child of the FunctionImport element or the Function element.</span></span>

### <a name="functionimport-element-application"></a><span data-ttu-id="27830-686">FunctionImport-Element-Anwendung</span><span class="sxs-lookup"><span data-stu-id="27830-686">FunctionImport Element Application</span></span>

<span data-ttu-id="27830-687">Ein **Parameter** Element (als untergeordnetes Element des **FunctionImport** -Elements) wird verwendet, um Eingabe-und Ausgabeparameter für Funktions Importe zu definieren, die in CSDL deklariert werden.</span><span class="sxs-lookup"><span data-stu-id="27830-687">A **Parameter** element (as a child of the **FunctionImport** element) is used to define input and output parameters for function imports that are declared in CSDL.</span></span>

<span data-ttu-id="27830-688">Das **Parameter** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-688">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-689">Dokumentation (kein (null) oder ein Element zugelassen)</span><span class="sxs-lookup"><span data-stu-id="27830-689">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="27830-690">Anmerkungselemente (kein (null) Element oder mehrere Elemente zugelassen)</span><span class="sxs-lookup"><span data-stu-id="27830-690">Annotation elements (zero or more elements allowed)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="27830-691">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-691">Applicable Attributes</span></span>

<span data-ttu-id="27830-692">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Parameter** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-692">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="27830-693">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-693">Attribute Name</span></span> | <span data-ttu-id="27830-694">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-694">Is Required</span></span> | <span data-ttu-id="27830-695">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-695">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-696">**Name**</span><span class="sxs-lookup"><span data-stu-id="27830-696">**Name**</span></span>       | <span data-ttu-id="27830-697">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-697">Yes</span></span>         | <span data-ttu-id="27830-698">Der Name des Parameters.</span><span class="sxs-lookup"><span data-stu-id="27830-698">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="27830-699">**Typ**</span><span class="sxs-lookup"><span data-stu-id="27830-699">**Type**</span></span>       | <span data-ttu-id="27830-700">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-700">Yes</span></span>         | <span data-ttu-id="27830-701">Der Parametertyp.</span><span class="sxs-lookup"><span data-stu-id="27830-701">The parameter type.</span></span> <span data-ttu-id="27830-702">Der Wert muss ein **EDMSimpleType** oder komplexer Typ sein, der im Gültigkeitsbereich des Modells liegt.</span><span class="sxs-lookup"><span data-stu-id="27830-702">The value must be an **EDMSimpleType** or a complex type that is within the scope of the model.</span></span>                                                                                                             |
| <span data-ttu-id="27830-703">**Mode**</span><span class="sxs-lookup"><span data-stu-id="27830-703">**Mode**</span></span>       | <span data-ttu-id="27830-704">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-704">No</span></span>          | <span data-ttu-id="27830-705">**In**, out oder **INOUT** , je nachdem, ob der Parameter ein Eingabe-, Ausgabe-oder Eingabe- **/Ausgabeparameter**ist.</span><span class="sxs-lookup"><span data-stu-id="27830-705">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="27830-706">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="27830-706">**MaxLength**</span></span>  | <span data-ttu-id="27830-707">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-707">No</span></span>          | <span data-ttu-id="27830-708">Die maximal zulässige Länge des Parameters.</span><span class="sxs-lookup"><span data-stu-id="27830-708">The maximum allowed length of the parameter.</span></span>                                                                                                                                                                                    |
| <span data-ttu-id="27830-709">**Genauigkeit**</span><span class="sxs-lookup"><span data-stu-id="27830-709">**Precision**</span></span>  | <span data-ttu-id="27830-710">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-710">No</span></span>          | <span data-ttu-id="27830-711">Die Genauigkeit des Parameters.</span><span class="sxs-lookup"><span data-stu-id="27830-711">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="27830-712">**Skalierung**</span><span class="sxs-lookup"><span data-stu-id="27830-712">**Scale**</span></span>      | <span data-ttu-id="27830-713">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-713">No</span></span>          | <span data-ttu-id="27830-714">Der Maßstab des Parameters.</span><span class="sxs-lookup"><span data-stu-id="27830-714">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="27830-715">**SRID**</span><span class="sxs-lookup"><span data-stu-id="27830-715">**SRID**</span></span>       | <span data-ttu-id="27830-716">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-716">No</span></span>          | <span data-ttu-id="27830-717">Verweis Bezeichner für räumliche Systeme.</span><span class="sxs-lookup"><span data-stu-id="27830-717">Spatial System Reference Identifier.</span></span> <span data-ttu-id="27830-718">Nur für Parameter räumlicher Typen gültig.</span><span class="sxs-lookup"><span data-stu-id="27830-718">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="27830-719">Weitere Informationen finden Sie unter [SRID](https://en.wikipedia.org/wiki/SRID) und [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="27830-719">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-720">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Parameter** Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-720">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="27830-721">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-721">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-722">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-722">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="27830-723">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-723">Example</span></span>

<span data-ttu-id="27830-724">Das folgende Beispiel zeigt ein **FunctionImport** -Element mit einem untergeordneten **Parameter** -Element.</span><span class="sxs-lookup"><span data-stu-id="27830-724">The following example shows a **FunctionImport** element with one **Parameter** child element.</span></span> <span data-ttu-id="27830-725">Die Funktion akzeptiert einen Eingabeparameter und gibt eine Auflistung von Entitätstypen zurück.</span><span class="sxs-lookup"><span data-stu-id="27830-725">The function accepts one input parameter and returns a collection of entity types.</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a><span data-ttu-id="27830-726">Function-Element-Anwendung</span><span class="sxs-lookup"><span data-stu-id="27830-726">Function Element Application</span></span>

<span data-ttu-id="27830-727">Ein **Parameter** Element (als untergeordnetes Element des **Function** -Elements) definiert Parameter für Funktionen, die in einem konzeptionellen Modell definiert oder deklariert werden.</span><span class="sxs-lookup"><span data-stu-id="27830-727">A **Parameter** element (as a child of the **Function** element) defines parameters for functions that are defined or declared in a conceptual model.</span></span>

<span data-ttu-id="27830-728">Das **Parameter** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-728">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-729">Documentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-729">Documentation (zero or one elements)</span></span>
-   <span data-ttu-id="27830-730">CollectionType (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-730">CollectionType (zero or one elements)</span></span>
-   <span data-ttu-id="27830-731">ReferenceType (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-731">ReferenceType (zero or one elements)</span></span>
-   <span data-ttu-id="27830-732">RowType (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-732">RowType (zero or one elements)</span></span>

> [!NOTE]
> <span data-ttu-id="27830-733">Nur eines der **CollectionType**-, **ReferenceType**-oder **RowType** -Elemente kann ein untergeordnetes Element eines **Property** -Elements sein.</span><span class="sxs-lookup"><span data-stu-id="27830-733">Only one of the **CollectionType**, **ReferenceType**, or **RowType** elements can be a child element of a **Property** element.</span></span>

 

-   <span data-ttu-id="27830-734">Anmerkungselemente (kein (null) Element oder mehrere Elemente zugelassen)</span><span class="sxs-lookup"><span data-stu-id="27830-734">Annotation elements (zero or more elements allowed)</span></span>

> [!NOTE]
> <span data-ttu-id="27830-735">Anmerkungselemente müssen an alle anderen untergeordneten Elemente angereiht werden.</span><span class="sxs-lookup"><span data-stu-id="27830-735">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="27830-736">Annotation-Elemente sind nur in CSDL v2 und höher zulässig.</span><span class="sxs-lookup"><span data-stu-id="27830-736">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="27830-737">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-737">Applicable Attributes</span></span>

<span data-ttu-id="27830-738">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Parameter** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-738">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="27830-739">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-739">Attribute Name</span></span>   | <span data-ttu-id="27830-740">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-740">Is Required</span></span> | <span data-ttu-id="27830-741">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-741">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-742">**Name**</span><span class="sxs-lookup"><span data-stu-id="27830-742">**Name**</span></span>         | <span data-ttu-id="27830-743">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-743">Yes</span></span>         | <span data-ttu-id="27830-744">Der Name des Parameters.</span><span class="sxs-lookup"><span data-stu-id="27830-744">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="27830-745">**Typ**</span><span class="sxs-lookup"><span data-stu-id="27830-745">**Type**</span></span>         | <span data-ttu-id="27830-746">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-746">No</span></span>          | <span data-ttu-id="27830-747">Der Parametertyp.</span><span class="sxs-lookup"><span data-stu-id="27830-747">The parameter type.</span></span> <span data-ttu-id="27830-748">Ein Parameter kann einer der folgenden Typen (oder Auflistungen dieser Typen) sein:</span><span class="sxs-lookup"><span data-stu-id="27830-748">A parameter can be any of the following types (or collections of these types):</span></span> <br/> <span data-ttu-id="27830-749">**EdmSimpleType**</span><span class="sxs-lookup"><span data-stu-id="27830-749">**EdmSimpleType**</span></span> <br/> <span data-ttu-id="27830-750">Entitätstyp</span><span class="sxs-lookup"><span data-stu-id="27830-750">entity type</span></span> <br/> <span data-ttu-id="27830-751">Komplexer Typ</span><span class="sxs-lookup"><span data-stu-id="27830-751">complex type</span></span> <br/> <span data-ttu-id="27830-752">Zeilentyp</span><span class="sxs-lookup"><span data-stu-id="27830-752">row type</span></span> <br/> <span data-ttu-id="27830-753">Verweistyp</span><span class="sxs-lookup"><span data-stu-id="27830-753">reference type</span></span>                             |
| <span data-ttu-id="27830-754">**NULL zulassen**</span><span class="sxs-lookup"><span data-stu-id="27830-754">**Nullable**</span></span>     | <span data-ttu-id="27830-755">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-755">No</span></span>          | <span data-ttu-id="27830-756">**True** (Standardwert) oder **false** , abhängig davon, ob die Eigenschaft einen **null** -Wert aufweisen kann.</span><span class="sxs-lookup"><span data-stu-id="27830-756">**True** (the default value) or **False** depending on whether the property can have a **null** value.</span></span>                                                                                                                          |
| <span data-ttu-id="27830-757">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="27830-757">**DefaultValue**</span></span> | <span data-ttu-id="27830-758">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-758">No</span></span>          | <span data-ttu-id="27830-759">Der Standardwert der Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="27830-759">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="27830-760">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="27830-760">**MaxLength**</span></span>    | <span data-ttu-id="27830-761">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-761">No</span></span>          | <span data-ttu-id="27830-762">Maximale Länge des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-762">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="27830-763">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="27830-763">**FixedLength**</span></span>  | <span data-ttu-id="27830-764">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-764">No</span></span>          | <span data-ttu-id="27830-765">**True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Zeichenfolge mit fester Länge gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="27830-765">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="27830-766">**Genauigkeit**</span><span class="sxs-lookup"><span data-stu-id="27830-766">**Precision**</span></span>    | <span data-ttu-id="27830-767">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-767">No</span></span>          | <span data-ttu-id="27830-768">Die Genauigkeit des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-768">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="27830-769">**Skalierung**</span><span class="sxs-lookup"><span data-stu-id="27830-769">**Scale**</span></span>        | <span data-ttu-id="27830-770">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-770">No</span></span>          | <span data-ttu-id="27830-771">Die Skalierung des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-771">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="27830-772">**SRID**</span><span class="sxs-lookup"><span data-stu-id="27830-772">**SRID**</span></span>         | <span data-ttu-id="27830-773">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-773">No</span></span>          | <span data-ttu-id="27830-774">Verweis Bezeichner für räumliche Systeme.</span><span class="sxs-lookup"><span data-stu-id="27830-774">Spatial System Reference Identifier.</span></span> <span data-ttu-id="27830-775">Nur für Eigenschaften räumlicher Typen gültig.</span><span class="sxs-lookup"><span data-stu-id="27830-775">Valid only for properties of spatial types.</span></span> <span data-ttu-id="27830-776">Weitere Informationen finden Sie unter [SRID](https://en.wikipedia.org/wiki/SRID) und [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="27830-776">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="27830-777">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="27830-777">**Unicode**</span></span>      | <span data-ttu-id="27830-778">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-778">No</span></span>          | <span data-ttu-id="27830-779">**True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Unicode-Zeichenfolge gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="27830-779">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="27830-780">**Sortierung**</span><span class="sxs-lookup"><span data-stu-id="27830-780">**Collation**</span></span>    | <span data-ttu-id="27830-781">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-781">No</span></span>          | <span data-ttu-id="27830-782">Eine Zeichenfolge, die angibt, welche Sortierreihenfolge in der Datenquelle verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="27830-782">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="27830-783">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Parameter** Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-783">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="27830-784">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-784">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-785">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-785">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="27830-786">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-786">Example</span></span>

<span data-ttu-id="27830-787">Das folgende Beispiel zeigt ein **Function** -Element, das ein untergeordnetes **Parameter** Element verwendet, um einen Funktions Parameter zu definieren.</span><span class="sxs-lookup"><span data-stu-id="27830-787">The following example shows a **Function** element that uses one **Parameter** child element to define a function parameter.</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a><span data-ttu-id="27830-788">Principal-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-788">Principal Element (CSDL)</span></span>

<span data-ttu-id="27830-789">Das **Principal** -Element in konzeptioneller Schema Definitions Sprache (CSDL) ist ein untergeordnetes Element für das referentialeinschränkung-Element, das das Prinzipal Ende einer referenziellen Einschränkung definiert.</span><span class="sxs-lookup"><span data-stu-id="27830-789">The **Principal** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element that defines the principal end of a referential constraint.</span></span> <span data-ttu-id="27830-790">Ein **referentialeinschränkung** -Element definiert Funktionen, die einer Einschränkung der referenziellen Integrität in einer relationalen Datenbank ähneln.</span><span class="sxs-lookup"><span data-stu-id="27830-790">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="27830-791">So wie eine Spalte (oder Spalten) einer Datenbanktabelle auf den Primärschlüssel einer anderen Tabelle verweisen kann, kann eine Eigenschaft (oder Eigenschaften) eines Entitätstyps auf den Entitätsschlüssel eines anderen Entitätstyps verweisen.</span><span class="sxs-lookup"><span data-stu-id="27830-791">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="27830-792">Der Entitätstyp, auf den verwiesen wird, wird als *Prinzipal Ende* der Einschränkung bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="27830-792">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="27830-793">Der Entitätstyp, der auf das Prinzipal Ende verweist, wird als *abhängiges Ende* der Einschränkung bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="27830-793">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="27830-794">**PropertyRef** -Elemente werden verwendet, um anzugeben, auf welche Schlüssel vom abhängigen Ende verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="27830-794">**PropertyRef** elements are used to specify which keys are referenced by the dependent end.</span></span>

<span data-ttu-id="27830-795">Das **Principal** -Element kann über die folgenden untergeordneten Elemente verfügen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-795">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-796">PropertyRef (mindestens ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-796">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="27830-797">Anmerkungselemente (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-797">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-798">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-798">Applicable Attributes</span></span>

<span data-ttu-id="27830-799">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Principal** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-799">The table below describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="27830-800">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-800">Attribute Name</span></span> | <span data-ttu-id="27830-801">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-801">Is Required</span></span> | <span data-ttu-id="27830-802">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-802">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="27830-803">**Rolle**</span><span class="sxs-lookup"><span data-stu-id="27830-803">**Role**</span></span>       | <span data-ttu-id="27830-804">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-804">Yes</span></span>         | <span data-ttu-id="27830-805">Der Name des Entitätstyps am Prinzipalende der Zuordnung.</span><span class="sxs-lookup"><span data-stu-id="27830-805">The name of the entity type on the principal end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-806">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Principal** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-806">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="27830-807">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-807">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-808">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-808">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-809">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-809">Example</span></span>

<span data-ttu-id="27830-810">Das folgende Beispiel zeigt ein **referentialeinschränkungs** -Element, das Teil der Definition der **PublishedBy** -Zuordnung ist.</span><span class="sxs-lookup"><span data-stu-id="27830-810">The following example shows a **ReferentialConstraint** element that is part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="27830-811">Die **ID** -Eigenschaft des **Verleger** Entitäts Typs bildet das Prinzipal Ende der referenziellen Einschränkung.</span><span class="sxs-lookup"><span data-stu-id="27830-811">The **Id** property of the **Publisher** entity type makes up the principal end of the referential constraint.</span></span>

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="property-element-csdl"></a><span data-ttu-id="27830-812">Property-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-812">Property Element (CSDL)</span></span>

<span data-ttu-id="27830-813">Das **Property** -Element in konzeptioneller Schema Definitions Sprache (CSDL) kann ein untergeordnetes Element des EntityType-Elements, das complexType-Element oder das RowType-Element sein.</span><span class="sxs-lookup"><span data-stu-id="27830-813">The **Property** element in conceptual schema definition language (CSDL) can be a child of the EntityType element, the ComplexType element, or the RowType element.</span></span>

### <a name="entitytype-and-complextype-element-applications"></a><span data-ttu-id="27830-814">EntityType- und ComplexType-Element-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="27830-814">EntityType and ComplexType Element Applications</span></span>

<span data-ttu-id="27830-815">Eigenschaften Elemente (als unter **geordnete Elemente von** **EntityType** -oder **complexType** -Elementen) definieren die Form und die Eigenschaften der Daten, die eine Entitätstyp Instanz oder eine komplexe Typinstanz enthalten wird.</span><span class="sxs-lookup"><span data-stu-id="27830-815">**Property** elements (as children of **EntityType** or **ComplexType** elements) define the shape and characteristics of data that an entity type instance or complex type instance will contain.</span></span> <span data-ttu-id="27830-816">Eigenschaften in einem konzeptionellen Modell sind analog zu den Eigenschaften, die für eine Klasse definiert werden.</span><span class="sxs-lookup"><span data-stu-id="27830-816">Properties in a conceptual model are analogous to properties that are defined on a class.</span></span> <span data-ttu-id="27830-817">So wie Eigenschaften die Form einer Klasse definieren und Informationen zu Objekten enthalten definieren Eigenschaften in einem konzeptionellen Modell die Form eines Entitätstyps und enthalten Informationen zu Entitätstypinstanzen.</span><span class="sxs-lookup"><span data-stu-id="27830-817">In the same way that properties on a class define the shape of the class and carry information about objects, properties in a conceptual model define the shape of an entity type and carry information about entity type instances.</span></span>

<span data-ttu-id="27830-818">Das **Property** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-818">The **Property** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-819">Dokumentationselement (kein (null) oder ein Element zugelassen)</span><span class="sxs-lookup"><span data-stu-id="27830-819">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="27830-820">Anmerkungselemente (kein (null) Element oder mehrere Elemente zugelassen)</span><span class="sxs-lookup"><span data-stu-id="27830-820">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="27830-821">Die folgenden Facetten können auf ein **Property** -Element angewendet werden **: Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **COLLATIONS**,-zustandcymode. **ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="27830-821">The following facets can be applied to a **Property** element: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **Collation**, **ConcurrencyMode**.</span></span> <span data-ttu-id="27830-822">Facets sind XML-Attribute, die Informationen über die Speicherung von Eigenschaftswerten im Datenspeicher bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="27830-822">Facets are XML attributes that provide information about how property values are stored in the data store.</span></span>

> [!NOTE]
> <span data-ttu-id="27830-823">Facetten können nur auf Eigenschaften vom Typ " **edmsimpletype**" angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-823">Facets can only be applied to properties of type **EDMSimpleType**.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="27830-824">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-824">Applicable Attributes</span></span>

<span data-ttu-id="27830-825">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Property** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-825">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="27830-826">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-826">Attribute Name</span></span>                                                         | <span data-ttu-id="27830-827">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-827">Is Required</span></span> | <span data-ttu-id="27830-828">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-828">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-829">**Name**</span><span class="sxs-lookup"><span data-stu-id="27830-829">**Name**</span></span>                                                               | <span data-ttu-id="27830-830">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-830">Yes</span></span>         | <span data-ttu-id="27830-831">Der Name der Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="27830-831">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="27830-832">**Typ**</span><span class="sxs-lookup"><span data-stu-id="27830-832">**Type**</span></span>                                                               | <span data-ttu-id="27830-833">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-833">Yes</span></span>         | <span data-ttu-id="27830-834">Der Typ des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-834">The type of the property value.</span></span> <span data-ttu-id="27830-835">Der Typ des Eigenschaftswerts muss ein **EDMSimpleType** oder ein komplexer Typ sein (erkennbar am vollqualifizierten Namen), der im Gültigkeitsbereich des Modells liegt.</span><span class="sxs-lookup"><span data-stu-id="27830-835">The property value type must be an **EDMSimpleType** or a complex type (indicated by a fully-qualified name) that is within scope of the model.</span></span>                                                 |
| <span data-ttu-id="27830-836">**NULL zulassen**</span><span class="sxs-lookup"><span data-stu-id="27830-836">**Nullable**</span></span>                                                           | <span data-ttu-id="27830-837">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-837">No</span></span>          | <span data-ttu-id="27830-838">**True** (Standardwert) oder <strong>False</strong> abhängig davon, ob die Eigenschaft einen NULL-Wert haben kann.</span><span class="sxs-lookup"><span data-stu-id="27830-838">**True** (the default value) or <strong>False</strong> depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                   |
| <span data-ttu-id="27830-839">> in CSDL v1 muss eine komplexe Typeigenschaft über verfügen `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="27830-839">> In the CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="27830-840">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="27830-840">**DefaultValue**</span></span>                                                       | <span data-ttu-id="27830-841">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-841">No</span></span>          | <span data-ttu-id="27830-842">Der Standardwert der Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="27830-842">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="27830-843">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="27830-843">**MaxLength**</span></span>                                                          | <span data-ttu-id="27830-844">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-844">No</span></span>          | <span data-ttu-id="27830-845">Maximale Länge des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-845">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="27830-846">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="27830-846">**FixedLength**</span></span>                                                        | <span data-ttu-id="27830-847">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-847">No</span></span>          | <span data-ttu-id="27830-848">**True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Zeichenfolge mit fester Länge gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="27830-848">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="27830-849">**Genauigkeit**</span><span class="sxs-lookup"><span data-stu-id="27830-849">**Precision**</span></span>                                                          | <span data-ttu-id="27830-850">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-850">No</span></span>          | <span data-ttu-id="27830-851">Die Genauigkeit des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-851">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="27830-852">**Skalierung**</span><span class="sxs-lookup"><span data-stu-id="27830-852">**Scale**</span></span>                                                              | <span data-ttu-id="27830-853">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-853">No</span></span>          | <span data-ttu-id="27830-854">Die Skalierung des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-854">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="27830-855">**SRID**</span><span class="sxs-lookup"><span data-stu-id="27830-855">**SRID**</span></span>                                                               | <span data-ttu-id="27830-856">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-856">No</span></span>          | <span data-ttu-id="27830-857">Verweis Bezeichner für räumliche Systeme.</span><span class="sxs-lookup"><span data-stu-id="27830-857">Spatial System Reference Identifier.</span></span> <span data-ttu-id="27830-858">Nur für Eigenschaften räumlicher Typen gültig.</span><span class="sxs-lookup"><span data-stu-id="27830-858">Valid only for properties of spatial types.</span></span> <span data-ttu-id="27830-859">Weitere Informationen finden Sie unter [SRID](https://en.wikipedia.org/wiki/SRID) und [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="27830-859">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="27830-860">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="27830-860">**Unicode**</span></span>                                                            | <span data-ttu-id="27830-861">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-861">No</span></span>          | <span data-ttu-id="27830-862">**True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Unicode-Zeichenfolge gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="27830-862">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="27830-863">**Sortierung**</span><span class="sxs-lookup"><span data-stu-id="27830-863">**Collation**</span></span>                                                          | <span data-ttu-id="27830-864">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-864">No</span></span>          | <span data-ttu-id="27830-865">Eine Zeichenfolge, die angibt, welche Sortierreihenfolge in der Datenquelle verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="27830-865">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="27830-866">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="27830-866">**ConcurrencyMode**</span></span>                                                    | <span data-ttu-id="27830-867">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-867">No</span></span>          | <span data-ttu-id="27830-868">**None** (Standardwert) oder **Fixed**.</span><span class="sxs-lookup"><span data-stu-id="27830-868">**None** (the default value) or **Fixed**.</span></span> <span data-ttu-id="27830-869">Wenn der Wert auf **Fixed**festgelegt ist, wird der Wert der Eigenschaft zu Überprüfungen auf vollständige Parallelität verwendet.</span><span class="sxs-lookup"><span data-stu-id="27830-869">If the value is set to **Fixed**, the property value will be used in optimistic concurrency checks.</span></span>                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="27830-870">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Property** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-870">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="27830-871">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-871">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-872">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-872">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="27830-873">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-873">Example</span></span>

<span data-ttu-id="27830-874">Das folgende Beispiel zeigt ein **EntityType** -Element mit drei **Eigenschafts** Elementen:</span><span class="sxs-lookup"><span data-stu-id="27830-874">The following example shows an **EntityType** element with three **Property** elements:</span></span>

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

<span data-ttu-id="27830-875">Das folgende Beispiel zeigt ein **complexType** -Element mit fünf **Eigenschafts** Elementen:</span><span class="sxs-lookup"><span data-stu-id="27830-875">The following example shows a **ComplexType** element with five **Property** elements:</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a><span data-ttu-id="27830-876">RowType-Element-Anwendung</span><span class="sxs-lookup"><span data-stu-id="27830-876">RowType Element Application</span></span>

<span data-ttu-id="27830-877">**Eigenschafts** Elemente (als untergeordnete Elemente eines **RowType** -Elements) definieren die Form und die Eigenschaften von Daten, die an eine Modell definierte Funktion übermittelt oder von dieser zurückgegeben werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-877">**Property** elements (as the children of a **RowType** element) define the shape and characteristics of data that can be passed to or returned from a model-defined function.</span></span>  

<span data-ttu-id="27830-878">Das **Property** -Element kann genau eines der folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="27830-878">The **Property** element can have exactly one of the following child elements:</span></span>

-   <span data-ttu-id="27830-879">CollectionType</span><span class="sxs-lookup"><span data-stu-id="27830-879">CollectionType</span></span>
-   <span data-ttu-id="27830-880">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="27830-880">ReferenceType</span></span>
-   <span data-ttu-id="27830-881">RowType</span><span class="sxs-lookup"><span data-stu-id="27830-881">RowType</span></span>

<span data-ttu-id="27830-882">Das **Property** -Element kann eine beliebige Anzahl untergeordneter Anmerkung-Elemente aufweisen.</span><span class="sxs-lookup"><span data-stu-id="27830-882">The **Property** element can have any number child annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="27830-883">Annotation-Elemente sind nur in CSDL v2 und höher zulässig.</span><span class="sxs-lookup"><span data-stu-id="27830-883">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="27830-884">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-884">Applicable Attributes</span></span>

<span data-ttu-id="27830-885">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Property** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-885">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="27830-886">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-886">Attribute Name</span></span>                                                     | <span data-ttu-id="27830-887">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-887">Is Required</span></span> | <span data-ttu-id="27830-888">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-888">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-889">**Name**</span><span class="sxs-lookup"><span data-stu-id="27830-889">**Name**</span></span>                                                           | <span data-ttu-id="27830-890">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-890">Yes</span></span>         | <span data-ttu-id="27830-891">Der Name der Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="27830-891">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="27830-892">**Typ**</span><span class="sxs-lookup"><span data-stu-id="27830-892">**Type**</span></span>                                                           | <span data-ttu-id="27830-893">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-893">Yes</span></span>         | <span data-ttu-id="27830-894">Der Typ des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-894">The type of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="27830-895">**NULL zulassen**</span><span class="sxs-lookup"><span data-stu-id="27830-895">**Nullable**</span></span>                                                       | <span data-ttu-id="27830-896">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-896">No</span></span>          | <span data-ttu-id="27830-897">**True** (Standardwert) oder **False** abhängig davon, ob die Eigenschaft einen NULL-Wert haben kann.</span><span class="sxs-lookup"><span data-stu-id="27830-897">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="27830-898">> in CSDL v1 muss eine komplexe Typeigenschaft über verfügen `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="27830-898">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="27830-899">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="27830-899">**DefaultValue**</span></span>                                                   | <span data-ttu-id="27830-900">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-900">No</span></span>          | <span data-ttu-id="27830-901">Der Standardwert der Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="27830-901">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="27830-902">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="27830-902">**MaxLength**</span></span>                                                      | <span data-ttu-id="27830-903">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-903">No</span></span>          | <span data-ttu-id="27830-904">Maximale Länge des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-904">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="27830-905">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="27830-905">**FixedLength**</span></span>                                                    | <span data-ttu-id="27830-906">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-906">No</span></span>          | <span data-ttu-id="27830-907">**True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Zeichenfolge mit fester Länge gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="27830-907">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="27830-908">**Genauigkeit**</span><span class="sxs-lookup"><span data-stu-id="27830-908">**Precision**</span></span>                                                      | <span data-ttu-id="27830-909">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-909">No</span></span>          | <span data-ttu-id="27830-910">Die Genauigkeit des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-910">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="27830-911">**Skalierung**</span><span class="sxs-lookup"><span data-stu-id="27830-911">**Scale**</span></span>                                                          | <span data-ttu-id="27830-912">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-912">No</span></span>          | <span data-ttu-id="27830-913">Die Skalierung des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-913">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="27830-914">**SRID**</span><span class="sxs-lookup"><span data-stu-id="27830-914">**SRID**</span></span>                                                           | <span data-ttu-id="27830-915">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-915">No</span></span>          | <span data-ttu-id="27830-916">Verweis Bezeichner für räumliche Systeme.</span><span class="sxs-lookup"><span data-stu-id="27830-916">Spatial System Reference Identifier.</span></span> <span data-ttu-id="27830-917">Nur für Eigenschaften räumlicher Typen gültig.</span><span class="sxs-lookup"><span data-stu-id="27830-917">Valid only for properties of spatial types.</span></span> <span data-ttu-id="27830-918">Weitere Informationen finden Sie unter [SRID](https://en.wikipedia.org/wiki/SRID) und [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="27830-918">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="27830-919">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="27830-919">**Unicode**</span></span>                                                        | <span data-ttu-id="27830-920">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-920">No</span></span>          | <span data-ttu-id="27830-921">**True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Unicode-Zeichenfolge gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="27830-921">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="27830-922">**Sortierung**</span><span class="sxs-lookup"><span data-stu-id="27830-922">**Collation**</span></span>                                                      | <span data-ttu-id="27830-923">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-923">No</span></span>          | <span data-ttu-id="27830-924">Eine Zeichenfolge, die angibt, welche Sortierreihenfolge in der Datenquelle verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="27830-924">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="27830-925">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Property** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-925">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="27830-926">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-926">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-927">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-927">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="27830-928">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-928">Example</span></span>

<span data-ttu-id="27830-929">Das folgende Beispiel zeigt **Eigenschafts** Elemente, die verwendet werden, um die Form des Rückgabe Typs einer Modell definierten Funktion zu definieren.</span><span class="sxs-lookup"><span data-stu-id="27830-929">The following example shows **Property** elements used to define the shape of the return type of a model-defined function.</span></span>

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```
 

 

## <a name="propertyref-element-csdl"></a><span data-ttu-id="27830-930">PropertyRef-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-930">PropertyRef Element (CSDL)</span></span>

<span data-ttu-id="27830-931">Das **PropertyRef** -Element in konzeptioneller Schema Definitions Sprache (CSDL) verweist auf eine Eigenschaft eines Entitäts Typs, um anzugeben, dass die-Eigenschaft eine der folgenden Rollen ausführt:</span><span class="sxs-lookup"><span data-stu-id="27830-931">The **PropertyRef** element in conceptual schema definition language (CSDL) references a property of an entity type to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="27830-932">Ein Teil des Entitätsschlüssels (eine Eigenschaft oder ein Satz von Eigenschaften eines Entitätstyps, der die Identität bestimmt).</span><span class="sxs-lookup"><span data-stu-id="27830-932">Part of the entity's key (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="27830-933">Ein oder mehrere **PropertyRef** -Elemente können verwendet werden, um einen Entitäts Schlüssel zu definieren.</span><span class="sxs-lookup"><span data-stu-id="27830-933">One or more **PropertyRef** elements can be used to define an entity key.</span></span>
-   <span data-ttu-id="27830-934">Das abhängige Ende oder Prinzipalende einer referenziellen Einschränkung.</span><span class="sxs-lookup"><span data-stu-id="27830-934">The dependent or principal end of a referential constraint.</span></span>

<span data-ttu-id="27830-935">Das **PropertyRef** -Element kann nur Anmerkung-Elemente (0 (null) oder mehr) als untergeordnete Elemente aufweisen.</span><span class="sxs-lookup"><span data-stu-id="27830-935">The **PropertyRef** element can only have annotation elements (zero or more) as child elements.</span></span>

> [!NOTE]
> <span data-ttu-id="27830-936">Annotation-Elemente sind nur in CSDL v2 und höher zulässig.</span><span class="sxs-lookup"><span data-stu-id="27830-936">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="27830-937">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-937">Applicable Attributes</span></span>

<span data-ttu-id="27830-938">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **PropertyRef** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-938">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="27830-939">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-939">Attribute Name</span></span> | <span data-ttu-id="27830-940">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-940">Is Required</span></span> | <span data-ttu-id="27830-941">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-941">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="27830-942">**Name**</span><span class="sxs-lookup"><span data-stu-id="27830-942">**Name**</span></span>       | <span data-ttu-id="27830-943">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-943">Yes</span></span>         | <span data-ttu-id="27830-944">Der Name der referenzierten Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="27830-944">The name of the referenced property.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-945">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **PropertyRef** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-945">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="27830-946">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-946">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-947">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-947">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-948">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-948">Example</span></span>

<span data-ttu-id="27830-949">Im folgenden Beispiel wird ein Entitätstyp (**Book**) definiert.</span><span class="sxs-lookup"><span data-stu-id="27830-949">The example below defines an entity type (**Book**).</span></span> <span data-ttu-id="27830-950">Der Entitäts Schlüssel wird definiert, indem auf die **ISBN** -Eigenschaft des Entitäts Typs verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="27830-950">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

<span data-ttu-id="27830-951">Im nächsten Beispiel werden zwei **PropertyRef** -Elemente verwendet, um anzugeben, dass zwei Eigenschaften (**ID** und **PublisherID**) das Prinzipal Ende und das abhängige Ende einer referenziellen Einschränkung sind.</span><span class="sxs-lookup"><span data-stu-id="27830-951">In the next example, two **PropertyRef** elements are used to indicate that two properties (**Id** and **PublisherId**) are the principal and dependent ends of a referential constraint.</span></span>

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="referencetype-element-csdl"></a><span data-ttu-id="27830-952">ReferenceType-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-952">ReferenceType Element (CSDL)</span></span>

<span data-ttu-id="27830-953">Das **ReferenceType** -Element in konzeptioneller Schema Definitions Sprache (CSDL) gibt einen Verweis auf einen Entitätstyp an.</span><span class="sxs-lookup"><span data-stu-id="27830-953">The **ReferenceType** element in conceptual schema definition language (CSDL) specifies a reference to an entity type.</span></span> <span data-ttu-id="27830-954">Das **ReferenceType** -Element kann ein untergeordnetes Element der folgenden Elemente sein:</span><span class="sxs-lookup"><span data-stu-id="27830-954">The **ReferenceType** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="27830-955">ReturnType (Funktion)</span><span class="sxs-lookup"><span data-stu-id="27830-955">ReturnType (Function)</span></span>
-   <span data-ttu-id="27830-956">Parameter</span><span class="sxs-lookup"><span data-stu-id="27830-956">Parameter</span></span>
-   <span data-ttu-id="27830-957">CollectionType</span><span class="sxs-lookup"><span data-stu-id="27830-957">CollectionType</span></span>

<span data-ttu-id="27830-958">Das **ReferenceType** -Element wird verwendet, wenn ein Parameter oder ein Rückgabetyp für eine Funktion definiert wird.</span><span class="sxs-lookup"><span data-stu-id="27830-958">The **ReferenceType** element is used when defining a parameter or return type for a function.</span></span>

<span data-ttu-id="27830-959">Ein **ReferenceType** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-959">A **ReferenceType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-960">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-960">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="27830-961">Anmerkungselemente (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-961">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-962">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-962">Applicable Attributes</span></span>

<span data-ttu-id="27830-963">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **ReferenceType** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-963">The table below describes the attributes that can be applied to the **ReferenceType** element.</span></span>

| <span data-ttu-id="27830-964">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-964">Attribute Name</span></span> | <span data-ttu-id="27830-965">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-965">Is Required</span></span> | <span data-ttu-id="27830-966">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-966">Value</span></span>                                         |
|:---------------|:------------|:----------------------------------------------|
| <span data-ttu-id="27830-967">**Typ**</span><span class="sxs-lookup"><span data-stu-id="27830-967">**Type**</span></span>       | <span data-ttu-id="27830-968">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-968">Yes</span></span>         | <span data-ttu-id="27830-969">Der Name des Entitätstyps, auf den verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="27830-969">The name of the entity type being referenced.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-970">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **ReferenceType** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-970">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferenceType** element.</span></span> <span data-ttu-id="27830-971">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-971">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-972">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-972">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-973">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-973">Example</span></span>

<span data-ttu-id="27830-974">Das folgende Beispiel zeigt das **ReferenceType** -Element, das als untergeordnetes Element eines **Parameter** -Elements in einer Modell definierten Funktion verwendet wird, die einen Verweis auf den Entitätstyp **Person** akzeptiert:</span><span class="sxs-lookup"><span data-stu-id="27830-974">The following example shows the **ReferenceType** element used as a child of a **Parameter** element in a model-defined function that accepts a reference to a **Person** entity type:</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
   <Parameter Name="instructor">
     <ReferenceType Type="SchoolModel.Person" />
   </Parameter>
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

<span data-ttu-id="27830-975">Das folgende Beispiel zeigt das **ReferenceType** -Element, das als untergeordnetes Element eines **returnType** (Function)-Elements in einer Modell definierten Funktion verwendet wird, die einen Verweis auf einen **Person** -Entitätstyp zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="27830-975">The following example shows the **ReferenceType** element used as a child of a **ReturnType** (Function) element in a model-defined function that returns a reference to a **Person** entity type:</span></span>

``` xml
 <Function Name="GetPersonReference">
     <Parameter Name="p" Type="SchoolModel.Person" />
     <ReturnType>
         <ReferenceType Type="SchoolModel.Person" />
     </ReturnType>
     <DefiningExpression>
           REF(p)
     </DefiningExpression>
 </Function>
```
 

 

## <a name="referentialconstraint-element-csdl"></a><span data-ttu-id="27830-976">ReferentialConstraint-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-976">ReferentialConstraint Element (CSDL)</span></span>

<span data-ttu-id="27830-977">Ein **referentialeinschränkungs** -Element in konzeptioneller Schema Definitions Sprache (CSDL) definiert Funktionen, die einer Einschränkung der referenziellen Integrität in einer relationalen Datenbank ähneln.</span><span class="sxs-lookup"><span data-stu-id="27830-977">A **ReferentialConstraint** element in conceptual schema definition language (CSDL) defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="27830-978">So wie eine Spalte (oder Spalten) einer Datenbanktabelle auf den Primärschlüssel einer anderen Tabelle verweisen kann, kann eine Eigenschaft (oder Eigenschaften) eines Entitätstyps auf den Entitätsschlüssel eines anderen Entitätstyps verweisen.</span><span class="sxs-lookup"><span data-stu-id="27830-978">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="27830-979">Der Entitätstyp, auf den verwiesen wird, wird als *Prinzipal Ende* der Einschränkung bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="27830-979">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="27830-980">Der Entitätstyp, der auf das Prinzipal Ende verweist, wird als *abhängiges Ende* der Einschränkung bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="27830-980">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span>

<span data-ttu-id="27830-981">Wenn ein Fremdschlüssel, der auf einem Entitätstyp verfügbar gemacht wird, auf eine Eigenschaft eines anderen Entitäts Typs verweist, definiert das **referentialeinschränkung** -Element eine Zuordnung zwischen den beiden Entitäts Typen.</span><span class="sxs-lookup"><span data-stu-id="27830-981">If a foreign key that is exposed on one entity type references a property on another entity type, the **ReferentialConstraint** element defines an association between the two entity types.</span></span> <span data-ttu-id="27830-982">Da das **referentialeinschränkung** -Element Informationen über die Beziehung zweier Entitäts Typen bereitstellt, ist in der Mapping-Spezifikationssprache (MSL) kein entsprechendes AssociationSetMapping-Element erforderlich.</span><span class="sxs-lookup"><span data-stu-id="27830-982">Because the **ReferentialConstraint** element provides information about how two entity types are related, no corresponding AssociationSetMapping element is necessary in the mapping specification language (MSL).</span></span> <span data-ttu-id="27830-983">Eine Zuordnung zwischen zwei Entitäts Typen, für die keine Fremdschlüssel verfügbar gemacht werden, muss über ein entsprechendes **AssociationSetMapping** -Element verfügen, um der Datenquelle Zuordnungs Informationen zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="27830-983">An association between two entity types that do not have foreign keys exposed must have a corresponding **AssociationSetMapping** element in order to map association information to the data source.</span></span>

<span data-ttu-id="27830-984">Wenn ein Fremdschlüssel nicht für einen Entitätstyp verfügbar gemacht wird, kann das **referentialeinschränkungs** -Element nur eine Primärschlüssel-zu-Primärschlüssel-Einschränkung zwischen dem Entitätstyp und einem anderen Entitätstyp definieren.</span><span class="sxs-lookup"><span data-stu-id="27830-984">If a foreign key is not exposed on an entity type, the **ReferentialConstraint** element can only define a primary key-to-primary key constraint between the entity type and another entity type.</span></span>

<span data-ttu-id="27830-985">Ein **referentialeinschränkung** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-985">A **ReferentialConstraint** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-986">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-986">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="27830-987">Prinzipal (genau ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-987">Principal (exactly one element)</span></span>
-   <span data-ttu-id="27830-988">Abhängig (genau ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-988">Dependent (exactly one element)</span></span>
-   <span data-ttu-id="27830-989">Anmerkungselemente (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-989">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-990">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-990">Applicable Attributes</span></span>

<span data-ttu-id="27830-991">Das **referentialeinschränkungs** -Element kann über eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) verfügen.</span><span class="sxs-lookup"><span data-stu-id="27830-991">The **ReferentialConstraint** element can have any number of annotation attributes (custom XML attributes).</span></span> <span data-ttu-id="27830-992">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-992">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-993">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-993">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="27830-994">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-994">Example</span></span>

<span data-ttu-id="27830-995">Das folgende Beispiel zeigt ein **referenentialeinschränkungs** -Element, das als Teil der Definition der **PublishedBy** -Zuordnung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="27830-995">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span>

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="returntype-function-element-csdl"></a><span data-ttu-id="27830-996">ReturnType (Function)-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-996">ReturnType (Function) Element (CSDL)</span></span>

<span data-ttu-id="27830-997">Das **returnType** (Function)-Element in konzeptioneller Schema Definitions Sprache (CSDL) gibt den Rückgabetyp für eine Funktion an, die in einem Function-Element definiert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-997">The **ReturnType** (Function) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a Function element.</span></span> <span data-ttu-id="27830-998">Ein Funktions Rückgabetyp kann auch mit einem **returnType** -Attribut angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="27830-998">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="27830-999">Rückgabe Typen können alle **edmsimpletype**-Typen, Entitäts Typen, komplexe Typen, Zeilen Typen, Ref-Typen oder eine Auflistung von einem dieser Typen sein.</span><span class="sxs-lookup"><span data-stu-id="27830-999">Return types can be any **EdmSimpleType**, entity type, complex type, row type, ref type, or a collection of one of these types.</span></span>

<span data-ttu-id="27830-1000">Der Rückgabetyp einer Funktion kann entweder mit dem **Type** -Attribut des **returnType** (Function)-Elements oder mit einem der folgenden untergeordneten Elemente angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="27830-1000">The return type of a function can be specified with either the **Type** attribute of the **ReturnType** (Function) element, or with one of the following child elements:</span></span>

-   <span data-ttu-id="27830-1001">CollectionType</span><span class="sxs-lookup"><span data-stu-id="27830-1001">CollectionType</span></span>
-   <span data-ttu-id="27830-1002">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="27830-1002">ReferenceType</span></span>
-   <span data-ttu-id="27830-1003">RowType</span><span class="sxs-lookup"><span data-stu-id="27830-1003">RowType</span></span>

> [!NOTE]
> <span data-ttu-id="27830-1004">Ein Modell wird nicht überprüft, wenn Sie einen Funktions Rückgabetyp mit dem **Type** -Attribut des **returnType** (Function)-Elements und einem der untergeordneten Elemente angeben.</span><span class="sxs-lookup"><span data-stu-id="27830-1004">A model will not validate if you specify a function return type with both the **Type** attribute of the **ReturnType** (Function) element and one of the child elements.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="27830-1005">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-1005">Applicable Attributes</span></span>

<span data-ttu-id="27830-1006">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **returnType** (Function)-Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-1006">The following table describes the attributes that can be applied to the **ReturnType** (Function) element.</span></span>

| <span data-ttu-id="27830-1007">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-1007">Attribute Name</span></span> | <span data-ttu-id="27830-1008">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-1008">Is Required</span></span> | <span data-ttu-id="27830-1009">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-1009">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="27830-1010">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="27830-1010">**ReturnType**</span></span> | <span data-ttu-id="27830-1011">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1011">No</span></span>          | <span data-ttu-id="27830-1012">Der von der Funktion zurückgegebene Typ.</span><span class="sxs-lookup"><span data-stu-id="27830-1012">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-1013">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **returnType** (Function)-Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-1013">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (Function) element.</span></span> <span data-ttu-id="27830-1014">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-1014">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-1015">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-1015">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-1016">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-1016">Example</span></span>

<span data-ttu-id="27830-1017">Im folgenden Beispiel wird ein **Function** -Element verwendet, um eine Funktion zu definieren, die die Anzahl der Jahre zurückgibt, in denen ein Buch gedruckt wurde.</span><span class="sxs-lookup"><span data-stu-id="27830-1017">The following example uses a **Function** element to define a function that returns the number of years a book has been in print.</span></span> <span data-ttu-id="27830-1018">Beachten Sie, dass der Rückgabetyp durch das **Type** -Attribut eines **returnType** (Function)-Elements angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="27830-1018">Note that the return type is specified by the **Type** attribute of a **ReturnType** (Function) element.</span></span>

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a><span data-ttu-id="27830-1019">ReturnType (FunctionImport)-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-1019">ReturnType (FunctionImport) Element (CSDL)</span></span>

<span data-ttu-id="27830-1020">Das **returnType** (FunctionImport)-Element in konzeptioneller Schema Definitions Sprache (CSDL) gibt den Rückgabetyp für eine Funktion an, die in einem FunctionImport-Element definiert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-1020">The **ReturnType** (FunctionImport) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a FunctionImport element.</span></span> <span data-ttu-id="27830-1021">Ein Funktions Rückgabetyp kann auch mit einem **returnType** -Attribut angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="27830-1021">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="27830-1022">Rückgabe Typen können eine beliebige Auflistung von Entitäts Typen, komplexen Typen oder **edmsimpletype**sein.</span><span class="sxs-lookup"><span data-stu-id="27830-1022">Return types can be any collection of entity type, complex type,or **EdmSimpleType**,</span></span>

<span data-ttu-id="27830-1023">Der Rückgabetyp einer Funktion wird mit dem **Type** -Attribut des **returnType** (FunctionImport)-Elements angegeben.</span><span class="sxs-lookup"><span data-stu-id="27830-1023">The return type of a function is specified with the **Type** attribute of the **ReturnType** (FunctionImport) element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-1024">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-1024">Applicable Attributes</span></span>

<span data-ttu-id="27830-1025">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **returnType** (FunctionImport)-Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-1025">The following table describes the attributes that can be applied to the **ReturnType** (FunctionImport) element.</span></span>

| <span data-ttu-id="27830-1026">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-1026">Attribute Name</span></span> | <span data-ttu-id="27830-1027">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-1027">Is Required</span></span> | <span data-ttu-id="27830-1028">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-1028">Value</span></span>                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-1029">**Typ**</span><span class="sxs-lookup"><span data-stu-id="27830-1029">**Type**</span></span>       | <span data-ttu-id="27830-1030">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1030">No</span></span>          | <span data-ttu-id="27830-1031">Der Typ, den die Funktion zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="27830-1031">The type that the function returns.</span></span> <span data-ttu-id="27830-1032">Der Wert muss eine Auflistung von complexType, EntityType oder edmsimpletype sein.</span><span class="sxs-lookup"><span data-stu-id="27830-1032">The value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>                                                                                      |
| <span data-ttu-id="27830-1033">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="27830-1033">**EntitySet**</span></span>  | <span data-ttu-id="27830-1034">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1034">No</span></span>          | <span data-ttu-id="27830-1035">Wenn die Funktion eine Auflistung von Entitäts Typen zurückgibt, muss der Wert von **EntitySet** der Entitätenmenge angehören, zu der die Auflistung gehört.</span><span class="sxs-lookup"><span data-stu-id="27830-1035">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="27830-1036">Andernfalls darf das Attribut **EntitySet** nicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-1036">Otherwise, the **EntitySet** attribute must not be used.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-1037">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **returnType** (FunctionImport)-Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-1037">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (FunctionImport) element.</span></span> <span data-ttu-id="27830-1038">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-1038">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-1039">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-1039">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-1040">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-1040">Example</span></span>

<span data-ttu-id="27830-1041">Im folgenden Beispiel wird ein **FunctionImport** -verwendet, der Bücher und Verleger zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="27830-1041">The following example uses a **FunctionImport** that returns books and publishers.</span></span> <span data-ttu-id="27830-1042">Beachten Sie, dass die Funktion zwei Resultsets zurückgibt. Daher werden zwei **returnType** (FunctionImport)-Elemente angegeben.</span><span class="sxs-lookup"><span data-stu-id="27830-1042">Note that the function returns two result sets and therefore two **ReturnType** (FunctionImport) elements are specified.</span></span>

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a><span data-ttu-id="27830-1043">RowType-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-1043">RowType Element (CSDL)</span></span>

<span data-ttu-id="27830-1044">Ein **RowType** -Element in der konzeptionellen Schema Definitions Sprache (CSDL) definiert eine unbenannte Struktur als Parameter oder Rückgabetyp für eine Funktion, die im konzeptionellen Modell definiert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-1044">A **RowType** element in conceptual schema definition language (CSDL) defines an unnamed structure as a parameter or return type for a function defined in the conceptual model.</span></span>

<span data-ttu-id="27830-1045">Ein **RowType** -Element kann das untergeordnete Element der folgenden Elemente sein:</span><span class="sxs-lookup"><span data-stu-id="27830-1045">A **RowType** element can be the child of the following elements:</span></span>

-   <span data-ttu-id="27830-1046">CollectionType</span><span class="sxs-lookup"><span data-stu-id="27830-1046">CollectionType</span></span>
-   <span data-ttu-id="27830-1047">Parameter</span><span class="sxs-lookup"><span data-stu-id="27830-1047">Parameter</span></span>
-   <span data-ttu-id="27830-1048">ReturnType (Funktion)</span><span class="sxs-lookup"><span data-stu-id="27830-1048">ReturnType (Function)</span></span>

<span data-ttu-id="27830-1049">Ein **RowType** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-1049">A **RowType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-1050">Eigenschaft (ein oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-1050">Property (one or more)</span></span>
-   <span data-ttu-id="27830-1051">Anmerkungselemente (kein (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-1051">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-1052">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-1052">Applicable Attributes</span></span>

<span data-ttu-id="27830-1053">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **RowType** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-1053">Any number of annotation attributes (custom XML attributes) may be applied to the **RowType** element.</span></span> <span data-ttu-id="27830-1054">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-1054">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-1055">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-1055">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="27830-1056">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-1056">Example</span></span>

<span data-ttu-id="27830-1057">Das folgende Beispiel zeigt eine Modell definierte Funktion, die ein **CollectionType** -Element verwendet, um anzugeben, dass die Funktion eine Auflistung von Zeilen zurückgibt (wie im **RowType** -Element angegeben).</span><span class="sxs-lookup"><span data-stu-id="27830-1057">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```

## <a name="schema-element-csdl"></a><span data-ttu-id="27830-1058">Schema-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-1058">Schema Element (CSDL)</span></span>

<span data-ttu-id="27830-1059">Das **Schema** -Element ist das Stamm Element einer konzeptionellen Modell Definition.</span><span class="sxs-lookup"><span data-stu-id="27830-1059">The **Schema** element is the root element of a conceptual model definition.</span></span> <span data-ttu-id="27830-1060">Es enthält Definitionen für die Objekte, Funktionen und Container, die ein konzeptionelles Modell bilden.</span><span class="sxs-lookup"><span data-stu-id="27830-1060">It contains definitions for the objects, functions, and containers that make up a conceptual model.</span></span>

<span data-ttu-id="27830-1061">Das **Schema** Element kann NULL oder mehr der folgenden untergeordneten Elemente enthalten:</span><span class="sxs-lookup"><span data-stu-id="27830-1061">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="27830-1062">Verwenden</span><span class="sxs-lookup"><span data-stu-id="27830-1062">Using</span></span>
-   <span data-ttu-id="27830-1063">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="27830-1063">EntityContainer</span></span>
-   <span data-ttu-id="27830-1064">EntityType</span><span class="sxs-lookup"><span data-stu-id="27830-1064">EntityType</span></span>
-   <span data-ttu-id="27830-1065">EnumType</span><span class="sxs-lookup"><span data-stu-id="27830-1065">EnumType</span></span>
-   <span data-ttu-id="27830-1066">Zuordnung</span><span class="sxs-lookup"><span data-stu-id="27830-1066">Association</span></span>
-   <span data-ttu-id="27830-1067">ComplexType</span><span class="sxs-lookup"><span data-stu-id="27830-1067">ComplexType</span></span>
-   <span data-ttu-id="27830-1068">Aufgabe</span><span class="sxs-lookup"><span data-stu-id="27830-1068">Function</span></span>

<span data-ttu-id="27830-1069">Ein **Schema** Element kann NULL oder ein Element mit Anmerkungen enthalten.</span><span class="sxs-lookup"><span data-stu-id="27830-1069">A **Schema** element may contain zero or one Annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="27830-1070">Die Element-und Anmerkung-Elemente von **Funktionen** sind nur in CSDL v2 und höher zulässig.</span><span class="sxs-lookup"><span data-stu-id="27830-1070">The **Function** element and annotation elements are only allowed in CSDL v2 and later.</span></span>

 

<span data-ttu-id="27830-1071">Das **Schema** -Element verwendet das **Namespace** -Attribut, um den Namespace für den Entitätstyp, den komplexen Typ und die Zuordnungs Objekte in einem konzeptionellen Modell zu definieren.</span><span class="sxs-lookup"><span data-stu-id="27830-1071">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type, complex type, and association objects in a conceptual model.</span></span> <span data-ttu-id="27830-1072">Innerhalb eines Namespace müssen alle Objekte eine eindeutige Bezeichnung aufweisen.</span><span class="sxs-lookup"><span data-stu-id="27830-1072">Within a namespace, no two objects can have the same name.</span></span> <span data-ttu-id="27830-1073">Namespaces können mehrere **Schema** Elemente und mehrere CSDL-Dateien umfassen.</span><span class="sxs-lookup"><span data-stu-id="27830-1073">Namespaces can span multiple **Schema** elements and multiple .csdl files.</span></span>

<span data-ttu-id="27830-1074">Ein Namespace des konzeptionellen Modells unterscheidet sich vom XML-Namespace des **Schema** -Elements.</span><span class="sxs-lookup"><span data-stu-id="27830-1074">A conceptual model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="27830-1075">Ein Namespace des konzeptionellen Modells (wie durch das **Namespace** -Attribut definiert) ist ein logischer Container für Entitäts Typen, komplexe Typen und Zuordnungs Typen.</span><span class="sxs-lookup"><span data-stu-id="27830-1075">A conceptual model namespace (as defined by the **Namespace** attribute) is a logical container for entity types, complex types, and association types.</span></span> <span data-ttu-id="27830-1076">Der XML-Namespace (angegeben durch das **xmlns** -Attribut) eines **Schema** -Elements ist der Standard Namespace für untergeordnete Elemente und Attribute des **Schema** -Elements.</span><span class="sxs-lookup"><span data-stu-id="27830-1076">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="27830-1077">XML-Namespaces im Formular https://schemas.microsoft.com/ado/YYYY/MM/edm (wobei yyyy und mm jeweils ein Jahr und einen Monat darstellen) sind für CSDL reserviert.</span><span class="sxs-lookup"><span data-stu-id="27830-1077">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm (where YYYY and MM represent a year and month respectively) are reserved for CSDL.</span></span> <span data-ttu-id="27830-1078">Benutzerdefinierte Elemente und Attribute können nicht in Namespaces mit diesem Format vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="27830-1078">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-1079">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-1079">Applicable Attributes</span></span>

<span data-ttu-id="27830-1080">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Schema** Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-1080">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="27830-1081">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-1081">Attribute Name</span></span> | <span data-ttu-id="27830-1082">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-1082">Is Required</span></span> | <span data-ttu-id="27830-1083">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-1083">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-1084">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="27830-1084">**Namespace**</span></span>  | <span data-ttu-id="27830-1085">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-1085">Yes</span></span>         | <span data-ttu-id="27830-1086">Der Namespace für das konzeptionelle Modell.</span><span class="sxs-lookup"><span data-stu-id="27830-1086">The namespace of the conceptual model.</span></span> <span data-ttu-id="27830-1087">Der Wert des **Namespace** -Attributs wird verwendet, um den voll qualifizierten Namen eines Typs zu bilden.</span><span class="sxs-lookup"><span data-stu-id="27830-1087">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="27830-1088">Wenn sich z. b. ein **EntityType** mit dem Namen *Customer* im Simple. example. Model-Namespace befindet, ist der voll qualifizierte Name des **EntityType** "simpleexamplemodel. Customer".</span><span class="sxs-lookup"><span data-stu-id="27830-1088">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace, then the fully qualified name of the **EntityType** is SimpleExampleModel.Customer.</span></span> <br/> <span data-ttu-id="27830-1089">Die folgenden Zeichen folgen können nicht als Wert für das **Namespace** -Attribut verwendet werden: **System**, **transient**oder **EDM**.</span><span class="sxs-lookup"><span data-stu-id="27830-1089">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="27830-1090">Der Wert für das **Namespace** -Attribut darf nicht mit dem Wert für das **Namespace** -Attribut im SSDL-Schema Element identisch sein.</span><span class="sxs-lookup"><span data-stu-id="27830-1090">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the SSDL Schema element.</span></span> |
| <span data-ttu-id="27830-1091">**Alias**</span><span class="sxs-lookup"><span data-stu-id="27830-1091">**Alias**</span></span>      | <span data-ttu-id="27830-1092">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1092">No</span></span>          | <span data-ttu-id="27830-1093">Ein anstelle der Namespacebezeichnung verwendeter Bezeichner.</span><span class="sxs-lookup"><span data-stu-id="27830-1093">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="27830-1094">Wenn z. b. ein **EntityType** mit dem Namen *Customer* im Simple. example. Model-Namespace und der Wert des **Alias** -Attributs *Model*ist, können Sie Model. Customer als voll qualifizierten Namen von **EntityType verwenden.**</span><span class="sxs-lookup"><span data-stu-id="27830-1094">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace and the value of the **Alias** attribute is *Model*, then you can use Model.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="27830-1095">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Schema** Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-1095">Any number of annotation attributes (custom XML attributes) may be applied to the **Schema** element.</span></span> <span data-ttu-id="27830-1096">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-1096">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-1097">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-1097">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-1098">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-1098">Example</span></span>

<span data-ttu-id="27830-1099">Das folgende Beispiel zeigt ein **Schema** -Element, das ein **EntityContainer** -Element, zwei **EntityType** -Elemente und ein **Association** -Element enthält.</span><span class="sxs-lookup"><span data-stu-id="27830-1099">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

``` xml
 <Schema xmlns="https://schemas.microsoft.com/ado/2009/11/edm"
      xmlns:cg="https://schemas.microsoft.com/ado/2009/11/codegeneration"
      xmlns:store="https://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
       Namespace="ExampleModel" Alias="Self">
         <EntityContainer Name="ExampleModelContainer">
           <EntitySet Name="Customers"
                      EntityType="ExampleModel.Customer" />
           <EntitySet Name="Orders" EntityType="ExampleModel.Order" />
           <AssociationSet
                       Name="CustomerOrder"
                       Association="ExampleModel.CustomerOrders">
             <End Role="Customer" EntitySet="Customers" />
             <End Role="Order" EntitySet="Orders" />
           </AssociationSet>
         </EntityContainer>
         <EntityType Name="Customer">
           <Key>
             <PropertyRef Name="CustomerId" />
           </Key>
           <Property Type="Int32" Name="CustomerId" Nullable="false" />
           <Property Type="String" Name="Name" Nullable="false" />
           <NavigationProperty
                    Name="Orders"
                    Relationship="ExampleModel.CustomerOrders"
                    FromRole="Customer" ToRole="Order" />
         </EntityType>
         <EntityType Name="Order">
           <Key>
             <PropertyRef Name="OrderId" />
           </Key>
           <Property Type="Int32" Name="OrderId" Nullable="false" />
           <Property Type="Int32" Name="ProductId" Nullable="false" />
           <Property Type="Int32" Name="Quantity" Nullable="false" />
           <NavigationProperty
                    Name="Customer"
                    Relationship="ExampleModel.CustomerOrders"
                    FromRole="Order" ToRole="Customer" />
           <Property Type="Int32" Name="CustomerId" Nullable="false" />
         </EntityType>
         <Association Name="CustomerOrders">
           <End Type="ExampleModel.Customer"
                Role="Customer" Multiplicity="1" />
           <End Type="ExampleModel.Order"
                Role="Order" Multiplicity="*" />
           <ReferentialConstraint>
             <Principal Role="Customer">
               <PropertyRef Name="CustomerId" />
             </Principal>
             <Dependent Role="Order">
               <PropertyRef Name="CustomerId" />
             </Dependent>
           </ReferentialConstraint>
         </Association>
       </Schema>
```
 

 

## <a name="typeref-element-csdl"></a><span data-ttu-id="27830-1100">TypeRef-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-1100">TypeRef Element (CSDL)</span></span>

<span data-ttu-id="27830-1101">Das **TypeRef** -Element in konzeptioneller Schema Definitions Sprache (CSDL) stellt einen Verweis auf einen vorhandenen benannten Typ bereit.</span><span class="sxs-lookup"><span data-stu-id="27830-1101">The **TypeRef** element in conceptual schema definition language (CSDL) provides a reference to an existing named type.</span></span> <span data-ttu-id="27830-1102">Das **TypeRef** -Element kann ein untergeordnetes Element des CollectionType-Elements sein, das verwendet wird, um anzugeben, dass eine Funktion eine Auflistung als Parameter oder Rückgabetyp aufweist.</span><span class="sxs-lookup"><span data-stu-id="27830-1102">The **TypeRef** element can be a child of the CollectionType element, which is used to specify that a function has a collection as a parameter or return type.</span></span>

<span data-ttu-id="27830-1103">Ein **TypeRef** -Element kann über die folgenden untergeordneten Elemente verfügen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="27830-1103">A **TypeRef** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="27830-1104">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="27830-1104">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="27830-1105">Anmerkungselemente (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="27830-1105">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-1106">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-1106">Applicable Attributes</span></span>

<span data-ttu-id="27830-1107">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **TypeRef** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-1107">The following table describes the attributes that can be applied to the **TypeRef** element.</span></span> <span data-ttu-id="27830-1108">Beachten Sie, dass die Attribute " **DefaultValue**", " **MaxLength**", " **FixedLength**", " **Precision**", " **Scale**", " **Unicode**" und " **COLLATIONS** " nur auf **edmsimple**</span><span class="sxs-lookup"><span data-stu-id="27830-1108">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="27830-1109">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-1109">Attribute Name</span></span>                                                     | <span data-ttu-id="27830-1110">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-1110">Is Required</span></span> | <span data-ttu-id="27830-1111">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-1111">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-1112">**Typ**</span><span class="sxs-lookup"><span data-stu-id="27830-1112">**Type**</span></span>                                                           | <span data-ttu-id="27830-1113">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1113">No</span></span>          | <span data-ttu-id="27830-1114">Der Name der Typbibliothek, auf die verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="27830-1114">The name of the type being referenced.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="27830-1115">**NULL zulassen**</span><span class="sxs-lookup"><span data-stu-id="27830-1115">**Nullable**</span></span>                                                       | <span data-ttu-id="27830-1116">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1116">No</span></span>          | <span data-ttu-id="27830-1117">**True** (Standardwert) oder **False** abhängig davon, ob die Eigenschaft einen NULL-Wert haben kann.</span><span class="sxs-lookup"><span data-stu-id="27830-1117">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="27830-1118">> in CSDL v1 muss eine komplexe Typeigenschaft über verfügen `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="27830-1118">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="27830-1119">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="27830-1119">**DefaultValue**</span></span>                                                   | <span data-ttu-id="27830-1120">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1120">No</span></span>          | <span data-ttu-id="27830-1121">Der Standardwert der Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="27830-1121">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="27830-1122">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="27830-1122">**MaxLength**</span></span>                                                      | <span data-ttu-id="27830-1123">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1123">No</span></span>          | <span data-ttu-id="27830-1124">Maximale Länge des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-1124">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="27830-1125">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="27830-1125">**FixedLength**</span></span>                                                    | <span data-ttu-id="27830-1126">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1126">No</span></span>          | <span data-ttu-id="27830-1127">**True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Zeichenfolge mit fester Länge gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="27830-1127">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="27830-1128">**Genauigkeit**</span><span class="sxs-lookup"><span data-stu-id="27830-1128">**Precision**</span></span>                                                      | <span data-ttu-id="27830-1129">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1129">No</span></span>          | <span data-ttu-id="27830-1130">Die Genauigkeit des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-1130">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="27830-1131">**Skalierung**</span><span class="sxs-lookup"><span data-stu-id="27830-1131">**Scale**</span></span>                                                          | <span data-ttu-id="27830-1132">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1132">No</span></span>          | <span data-ttu-id="27830-1133">Die Skalierung des Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="27830-1133">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="27830-1134">**SRID**</span><span class="sxs-lookup"><span data-stu-id="27830-1134">**SRID**</span></span>                                                           | <span data-ttu-id="27830-1135">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1135">No</span></span>          | <span data-ttu-id="27830-1136">Verweis Bezeichner für räumliche Systeme.</span><span class="sxs-lookup"><span data-stu-id="27830-1136">Spatial System Reference Identifier.</span></span> <span data-ttu-id="27830-1137">Nur für Eigenschaften räumlicher Typen gültig.</span><span class="sxs-lookup"><span data-stu-id="27830-1137">Valid only for properties of spatial types.</span></span> <span data-ttu-id="27830-1138">Weitere Informationen finden Sie unter [SRID](https://en.wikipedia.org/wiki/SRID) und [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="27830-1138">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="27830-1139">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="27830-1139">**Unicode**</span></span>                                                        | <span data-ttu-id="27830-1140">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1140">No</span></span>          | <span data-ttu-id="27830-1141">**True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Unicode-Zeichenfolge gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="27830-1141">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="27830-1142">**Sortierung**</span><span class="sxs-lookup"><span data-stu-id="27830-1142">**Collation**</span></span>                                                      | <span data-ttu-id="27830-1143">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1143">No</span></span>          | <span data-ttu-id="27830-1144">Eine Zeichenfolge, die angibt, welche Sortierreihenfolge in der Datenquelle verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="27830-1144">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="27830-1145">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **CollectionType** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-1145">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="27830-1146">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-1146">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-1147">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-1147">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-1148">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-1148">Example</span></span>

<span data-ttu-id="27830-1149">Das folgende Beispiel zeigt eine Modell definierte Funktion, die das **TypeRef** -Element verwendet (als untergeordnetes Element eines **CollectionType** -Elements), um anzugeben, dass die Funktion eine Auflistung von **Abteilungs** Entitäts Typen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="27830-1149">The following example shows a model-defined function that uses the **TypeRef** element (as a child of a **CollectionType** element) to specify that the function accepts a collection of **Department** entity types.</span></span>

``` xml
 <Function Name="GetAvgBudget">
      <Parameter Name="Departments">
          <CollectionType>
             <TypeRef Type="SchoolModel.Department"/>
          </CollectionType>
           </Parameter>
       <ReturnType Type="Collection(Edm.Decimal)"/>
       <DefiningExpression>
             SELECT VALUE AVG(d.Budget) FROM Departments AS d
       </DefiningExpression>
 </Function>
```
 

 

## <a name="using-element-csdl"></a><span data-ttu-id="27830-1150">Using-Element (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-1150">Using Element (CSDL)</span></span>

<span data-ttu-id="27830-1151">Das **using** -Element in konzeptioneller Schema Definitions Sprache (CSDL) importiert den Inhalt eines konzeptionellen Modells, das in einem anderen Namespace vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="27830-1151">The **Using** element in conceptual schema definition language (CSDL) imports the contents of a conceptual model that exists in a different namespace.</span></span> <span data-ttu-id="27830-1152">Wenn Sie den Wert des **Namespace** -Attributs festlegen, können Sie auf Entitäts Typen, komplexe Typen und Zuordnungs Typen verweisen, die in einem anderen konzeptionellen Modell definiert sind.</span><span class="sxs-lookup"><span data-stu-id="27830-1152">By setting the value of the **Namespace** attribute, you can refer to entity types, complex types, and association types that are defined in another conceptual model.</span></span> <span data-ttu-id="27830-1153">Mehr als ein **using** -Element kann ein untergeordnetes Element eines **Schema** -Elements sein.</span><span class="sxs-lookup"><span data-stu-id="27830-1153">More than one **Using** element can be a child of a **Schema** element.</span></span>

> [!NOTE]
> <span data-ttu-id="27830-1154">Das **using** -Element in CSDL funktioniert nicht genau wie eine **using** -Anweisung in einer Programmiersprache.</span><span class="sxs-lookup"><span data-stu-id="27830-1154">The **Using** element in CSDL does not function exactly like a **using** statement in a programming language.</span></span> <span data-ttu-id="27830-1155">Wenn Sie einen Namespace mit einer **using** -Anweisung in einer Programmiersprache importieren, wirkt sich dies nicht auf Objekte im ursprünglichen Namespace aus.</span><span class="sxs-lookup"><span data-stu-id="27830-1155">By importing a namespace with a **using** statement in a programming language, you do not affect objects in the original namespace.</span></span> <span data-ttu-id="27830-1156">In CSDL kann ein importierter Namespace einen Entitätstyp enthalten, der von einem Entitätstyp im ursprünglichen Namespace abgeleitet ist.</span><span class="sxs-lookup"><span data-stu-id="27830-1156">In CSDL, an imported namespace can contain an entity type that is derived from an entity type in the original namespace.</span></span> <span data-ttu-id="27830-1157">Dies kann sich auf im ursprünglichen Namespace deklarierte Entitätssätze auswirken.</span><span class="sxs-lookup"><span data-stu-id="27830-1157">This can affect entity sets declared in the original namespace.</span></span>

 

<span data-ttu-id="27830-1158">Das **using** -Element kann die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="27830-1158">The **Using** element can have the following child elements:</span></span>

-   <span data-ttu-id="27830-1159">Dokumentation (kein (null) oder ein Element zugelassen)</span><span class="sxs-lookup"><span data-stu-id="27830-1159">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="27830-1160">Anmerkungselemente (kein (null) Element oder mehrere Elemente zugelassen)</span><span class="sxs-lookup"><span data-stu-id="27830-1160">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="27830-1161">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="27830-1161">Applicable Attributes</span></span>

<span data-ttu-id="27830-1162">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **using** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-1162">The table below describes the attributes can be applied to the **Using** element.</span></span>

| <span data-ttu-id="27830-1163">Attributname</span><span class="sxs-lookup"><span data-stu-id="27830-1163">Attribute Name</span></span> | <span data-ttu-id="27830-1164">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="27830-1164">Is Required</span></span> | <span data-ttu-id="27830-1165">Wert</span><span class="sxs-lookup"><span data-stu-id="27830-1165">Value</span></span>                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="27830-1166">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="27830-1166">**Namespace**</span></span>  | <span data-ttu-id="27830-1167">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-1167">Yes</span></span>         | <span data-ttu-id="27830-1168">Der Name des importierten Namespaces.</span><span class="sxs-lookup"><span data-stu-id="27830-1168">The name of the imported namespace.</span></span>                                                                                                                                                |
| <span data-ttu-id="27830-1169">**Alias**</span><span class="sxs-lookup"><span data-stu-id="27830-1169">**Alias**</span></span>      | <span data-ttu-id="27830-1170">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-1170">Yes</span></span>         | <span data-ttu-id="27830-1171">Ein anstelle der Namespacebezeichnung verwendeter Bezeichner.</span><span class="sxs-lookup"><span data-stu-id="27830-1171">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="27830-1172">Obwohl dieses Attribut erforderlich ist, muss es nicht anstelle des Namespacenamens verwendet wird, um Objektnamen zu qualifizieren.</span><span class="sxs-lookup"><span data-stu-id="27830-1172">Although this attribute is required, it is not required that it be used in place of the namespace name to qualify object names.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="27830-1173">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **using** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="27830-1173">Any number of annotation attributes (custom XML attributes) may be applied to the **Using** element.</span></span> <span data-ttu-id="27830-1174">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-1174">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="27830-1175">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-1175">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="27830-1176">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-1176">Example</span></span>

<span data-ttu-id="27830-1177">Das folgende Beispiel veranschaulicht das **using** -Element, das verwendet wird, um einen Namespace zu importieren, der an anderer Stelle definiert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-1177">The following example demonstrates the **Using** element being used to import a namespace that is defined elsewhere.</span></span> <span data-ttu-id="27830-1178">Beachten Sie, dass der Namespace für das angezeigte **Schema** Element ist `BooksModel` .</span><span class="sxs-lookup"><span data-stu-id="27830-1178">Note that the namespace for the **Schema** element shown is `BooksModel`.</span></span> <span data-ttu-id="27830-1179">Die- `Address` Eigenschaft für den `Publisher` **EntityType** ist ein komplexer Typ, der im- `ExtendedBooksModel` Namespace definiert ist (importiert mit dem **using** -Element).</span><span class="sxs-lookup"><span data-stu-id="27830-1179">The `Address` property on the `Publisher`**EntityType** is a complex type that is defined in the `ExtendedBooksModel` namespace (imported with the **Using** element).</span></span>

``` xml
 <Schema xmlns="https://schemas.microsoft.com/ado/2009/11/edm"
           xmlns:cg="https://schemas.microsoft.com/ado/2009/11/codegeneration"
           xmlns:store="https://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
           Namespace="BooksModel" Alias="Self">

     <Using Namespace="BooksModel.Extended" Alias="BMExt" />

 <EntityContainer Name="BooksContainer" >
       <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
     </EntityContainer>

 <EntityType Name="Publisher">
       <Key>
         <PropertyRef Name="Id" />
       </Key>
       <Property Type="Int32" Name="Id" Nullable="false" />
       <Property Type="String" Name="Name" Nullable="false" />
       <Property Type="BMExt.Address" Name="Address" Nullable="false" />
     </EntityType>

 </Schema>
```
 

 

## <a name="annotation-attributes-csdl"></a><span data-ttu-id="27830-1180">Anmerkungsattribute (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-1180">Annotation Attributes (CSDL)</span></span>

<span data-ttu-id="27830-1181">Anmerkungsattribute in konzeptioneller Schemadefinitionssprache (CSDL) sind benutzerdefinierte XML-Attribute im konzeptionellen Modell.</span><span class="sxs-lookup"><span data-stu-id="27830-1181">Annotation attributes in conceptual schema definition language (CSDL) are custom XML attributes in the conceptual model.</span></span> <span data-ttu-id="27830-1182">Neben dem Vorhandensein einer gültigen XML-Struktur muss für Anmerkungsattribute folgendes zutreffen:</span><span class="sxs-lookup"><span data-stu-id="27830-1182">In addition to having valid XML structure, the following must be true of annotation attributes:</span></span>

-   <span data-ttu-id="27830-1183">Anmerkungsattribute dürfen sich in keinem XML-Namespace befinden, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-1183">Annotation attributes must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="27830-1184">Für ein angegebenes CSDL-Element kann mehr als ein Anmerkungsattribut übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="27830-1184">More than one annotation attribute may be applied to a given CSDL element.</span></span>
-   <span data-ttu-id="27830-1185">Die vollqualifizierten Namen zweier Anmerkungsattribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-1185">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="27830-1186">Anmerkungsattribute können verwendet werden, um zusätzliche Metadaten für die Elemente in einem konzeptionellen Modell bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="27830-1186">Annotation attributes can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="27830-1187">Auf Metadaten, die in Anmerkung-Elementen enthalten sind, kann zur Laufzeit mithilfe von Klassen im System. Data. Metadata. Edm-Namespace zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="27830-1187">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="27830-1188">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-1188">Example</span></span>

<span data-ttu-id="27830-1189">Das folgende Beispiel zeigt ein **EntityType** -Element mit einem Anmerkung-Attribut (**CustomAttribute**).</span><span class="sxs-lookup"><span data-stu-id="27830-1189">The following example shows an **EntityType** element with an annotation attribute (**CustomAttribute**).</span></span> <span data-ttu-id="27830-1190">Das Beispiel zeigt außerdem ein Anmerkungselement, das auf das Entitätstypelement angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="27830-1190">The example also shows an annotation element applied to the entity type element.</span></span>

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="https://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="https://schemas.microsoft.com/ado/2009/11/edm">
   <EntityContainer Name="SchoolEntities" annotation:LazyLoadingEnabled="true">
     <EntitySet Name="People" EntityType="SchoolModel.Person" />
   </EntityContainer>
   <EntityType Name="Person" xmlns:p="http://CustomNamespace.com"
               p:CustomAttribute="Data here.">
     <Key>
       <PropertyRef Name="PersonID" />
     </Key>
     <Property Name="PersonID" Type="Int32" Nullable="false"
               annotation:StoreGeneratedPattern="Identity" />
     <Property Name="LastName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="FirstName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="HireDate" Type="DateTime" />
     <Property Name="EnrollmentDate" Type="DateTime" />
     <p:CustomElement>
       Custom metadata.
     </p:CustomElement>
   </EntityType>
 </Schema>
```
 

<span data-ttu-id="27830-1191">Im folgenden Code werden die Metadaten im Anmerkungsattribut abgerufen und in die Konsole geschrieben:</span><span class="sxs-lookup"><span data-stu-id="27830-1191">The following code retrieves the metadata in the annotation attribute and writes it to the console:</span></span>

``` xml
 EdmItemCollection collection = new EdmItemCollection("School.csdl");
 MetadataWorkspace workspace = new MetadataWorkspace();
 workspace.RegisterItemCollection(collection);
 EdmType contentType;
 workspace.TryGetType("Person", "SchoolModel", DataSpace.CSpace, out contentType);
 if (contentType.MetadataProperties.Contains("http://CustomNamespace.com:CustomAttribute"))
 {
     MetadataProperty annotationProperty =
         contentType.MetadataProperties["http://CustomNamespace.com:CustomAttribute"];
     object annotationValue = annotationProperty.Value;
     Console.WriteLine(annotationValue.ToString());
 }
```
 

<span data-ttu-id="27830-1192">Im Code oben wird davon ausgegangen, dass sich die Datei `School.csdl` im Ausgabeverzeichnis des Projekts befindet und dass Sie dem Projekt die folgenden `Imports`- und `Using`-Anweisungen hinzugefügt haben:</span><span class="sxs-lookup"><span data-stu-id="27830-1192">The code above assumes that the `School.csdl` file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a><span data-ttu-id="27830-1193">Anmerkungselemente (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-1193">Annotation Elements (CSDL)</span></span>

<span data-ttu-id="27830-1194">Anmerkungselemente in konzeptioneller Schemadefinitionssprache (CSDL) sind benutzerdefinierte XML-Elemente im konzeptionellen Modell.</span><span class="sxs-lookup"><span data-stu-id="27830-1194">Annotation elements in conceptual schema definition language (CSDL) are custom XML elements in the conceptual model.</span></span> <span data-ttu-id="27830-1195">Neben dem Vorhandensein einer gültigen XML-Struktur muss für Anmerkungselemente Folgendes zutreffen:</span><span class="sxs-lookup"><span data-stu-id="27830-1195">In addition to having valid XML structure, the following must be true of annotation elements:</span></span>

-   <span data-ttu-id="27830-1196">Anmerkungselemente dürfen sich in keinem XML-Namespace befinden, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="27830-1196">Annotation elements must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="27830-1197">Mehrere Anmerkungselemente können untergeordnete Elemente eines angegebenen CSDL-Elements sein.</span><span class="sxs-lookup"><span data-stu-id="27830-1197">More than one annotation element may be a child of a given CSDL element.</span></span>
-   <span data-ttu-id="27830-1198">Die vollqualifizierten Namen zweier Anmerkungselemente dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="27830-1198">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="27830-1199">Anmerkungselemente müssen nach allen anderen untergeordneten Elementen eines angegebenen CSDL-Elements angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="27830-1199">Annotation elements must appear after all other child elements of a given CSDL element.</span></span>

<span data-ttu-id="27830-1200">Anmerkungselemente können verwendet werden, um zusätzliche Metadaten für die Elemente in einem konzeptionellen Modell bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="27830-1200">Annotation elements can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="27830-1201">Ab Version 4 von .NET Framework können mithilfe von Klassen im Namespace "System. Data. Metadata. Edm" zur Laufzeit auf Metadaten zugegriffen werden, die in Anmerkung-Elementen enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="27830-1201">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="27830-1202">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-1202">Example</span></span>

<span data-ttu-id="27830-1203">Das folgende Beispiel zeigt ein **EntityType** -Element mit einem Anmerkung-Element (**customelement**).</span><span class="sxs-lookup"><span data-stu-id="27830-1203">The following example shows an **EntityType** element with an annotation element (**CustomElement**).</span></span> <span data-ttu-id="27830-1204">Das Beispiel zeigt außerdem ein Anmerkungsattribut, das auf das Entitätstypelement angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="27830-1204">The example also show an annotation attribute applied to the entity type element.</span></span>

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="https://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="https://schemas.microsoft.com/ado/2009/11/edm">
   <EntityContainer Name="SchoolEntities" annotation:LazyLoadingEnabled="true">
     <EntitySet Name="People" EntityType="SchoolModel.Person" />
   </EntityContainer>
   <EntityType Name="Person" xmlns:p="http://CustomNamespace.com"
               p:CustomAttribute="Data here.">
     <Key>
       <PropertyRef Name="PersonID" />
     </Key>
     <Property Name="PersonID" Type="Int32" Nullable="false"
               annotation:StoreGeneratedPattern="Identity" />
     <Property Name="LastName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="FirstName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="HireDate" Type="DateTime" />
     <Property Name="EnrollmentDate" Type="DateTime" />
     <p:CustomElement>
       Custom metadata.
     </p:CustomElement>
   </EntityType>
 </Schema>
```
 

<span data-ttu-id="27830-1205">Im folgenden Code werden die Metadaten im Anmerkungselement abgerufen und in die Konsole geschrieben:</span><span class="sxs-lookup"><span data-stu-id="27830-1205">The following code retrieves the metadata in the annotation element and writes it to the console:</span></span>

``` csharp
 EdmItemCollection collection = new EdmItemCollection("School.csdl");
 MetadataWorkspace workspace = new MetadataWorkspace();
 workspace.RegisterItemCollection(collection);
 EdmType contentType;
 workspace.TryGetType("Person", "SchoolModel", DataSpace.CSpace, out contentType);
 if (contentType.MetadataProperties.Contains("http://CustomNamespace.com:CustomElement"))
 {
     MetadataProperty annotationProperty =
         contentType.MetadataProperties["http://CustomNamespace.com:CustomElement"];
     object annotationValue = annotationProperty.Value;
     Console.WriteLine(annotationValue.ToString());
 }
```
 

<span data-ttu-id="27830-1206">Im Code oben wird davon ausgegangen, dass sich die Datei School.csdl im Ausgabeverzeichnis des Projekts befindet, und dass Sie dem Projekt die folgenden `Imports`- und `Using`-Anweisungen hinzugefügt haben:</span><span class="sxs-lookup"><span data-stu-id="27830-1206">The code above assumes that the School.csdl file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a><span data-ttu-id="27830-1207">Konzeptionelle Modelltypen (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-1207">Conceptual Model Types (CSDL)</span></span>

<span data-ttu-id="27830-1208">Die konzeptionelle Schema Definitions Sprache (CSDL) unterstützt einen Satz abstrakter primitiver Datentypen namens **edmsimpletypes**, die Eigenschaften in einem konzeptionellen Modell definieren.</span><span class="sxs-lookup"><span data-stu-id="27830-1208">Conceptual schema definition language (CSDL) supports a set of abstract primitive data types, called **EDMSimpleTypes**, that define properties in a conceptual model.</span></span> <span data-ttu-id="27830-1209">**Edmsimpletypes** sind Proxys für primitive Datentypen, die in der Speicher-oder Hostingumgebung unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="27830-1209">**EDMSimpleTypes** are proxies for primitive data types that are supported in the storage or hosting environment.</span></span>

<span data-ttu-id="27830-1210">In der nachfolgenden Tabelle werden die von CSDL unterstützten primitiven Datentypen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="27830-1210">The table below lists the primitive data types that are supported by CSDL.</span></span> <span data-ttu-id="27830-1211">In der Tabelle werden auch die Facetten aufgelistet, die auf jeden **edmsimpletype**angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="27830-1211">The table also lists the facets that can be applied to each **EDMSimpleType**.</span></span>

| <span data-ttu-id="27830-1212">EDMSimpleType</span><span class="sxs-lookup"><span data-stu-id="27830-1212">EDMSimpleType</span></span>                    | <span data-ttu-id="27830-1213">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="27830-1213">Description</span></span>                                                | <span data-ttu-id="27830-1214">Anwendbare Facets</span><span class="sxs-lookup"><span data-stu-id="27830-1214">Applicable Facets</span></span>                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| <span data-ttu-id="27830-1215">**Edm.Binary**</span><span class="sxs-lookup"><span data-stu-id="27830-1215">**Edm.Binary**</span></span>                   | <span data-ttu-id="27830-1216">Enthält Binärdaten.</span><span class="sxs-lookup"><span data-stu-id="27830-1216">Contains binary data.</span></span>                                      | <span data-ttu-id="27830-1217">MaxLength, FixedLength, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="27830-1217">MaxLength, FixedLength, Nullable, Default</span></span>                                |
| <span data-ttu-id="27830-1218">**Edm.Boolean**</span><span class="sxs-lookup"><span data-stu-id="27830-1218">**Edm.Boolean**</span></span>                  | <span data-ttu-id="27830-1219">Enthält den Wert **true** oder **false**.</span><span class="sxs-lookup"><span data-stu-id="27830-1219">Contains the value **true** or **false**.</span></span>                  | <span data-ttu-id="27830-1220">Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="27830-1220">Nullable, Default</span></span>                                                        |
| <span data-ttu-id="27830-1221">**Edm.Byte**</span><span class="sxs-lookup"><span data-stu-id="27830-1221">**Edm.Byte**</span></span>                     | <span data-ttu-id="27830-1222">Enthält einen 8-Bit-Ganzzahlwert ohne Vorzeichen.</span><span class="sxs-lookup"><span data-stu-id="27830-1222">Contains an unsigned 8-bit integer value.</span></span>                  | <span data-ttu-id="27830-1223">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="27830-1223">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="27830-1224">**Edm.DateTime**</span><span class="sxs-lookup"><span data-stu-id="27830-1224">**Edm.DateTime**</span></span>                 | <span data-ttu-id="27830-1225">Stellt ein Datum und eine Uhrzeit dar.</span><span class="sxs-lookup"><span data-stu-id="27830-1225">Represents a date and time.</span></span>                                | <span data-ttu-id="27830-1226">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="27830-1226">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="27830-1227">**Edm.DateTimeOffset**</span><span class="sxs-lookup"><span data-stu-id="27830-1227">**Edm.DateTimeOffset**</span></span>           | <span data-ttu-id="27830-1228">Enthält ein Datum und eine Uhrzeit als Offset in Minuten von GMT.</span><span class="sxs-lookup"><span data-stu-id="27830-1228">Contains a date and time as an offset in minutes from GMT.</span></span> | <span data-ttu-id="27830-1229">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="27830-1229">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="27830-1230">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="27830-1230">**Edm.Decimal**</span></span>                  | <span data-ttu-id="27830-1231">Enthält einen numerischen Wert mit fester Genauigkeit und festen Dezimalstellen.</span><span class="sxs-lookup"><span data-stu-id="27830-1231">Contains a numeric value with fixed precision and scale.</span></span>   | <span data-ttu-id="27830-1232">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="27830-1232">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="27830-1233">**Edm.Double**</span><span class="sxs-lookup"><span data-stu-id="27830-1233">**Edm.Double**</span></span>                   | <span data-ttu-id="27830-1234">Enthält eine Gleit Komma Zahl mit einer Genauigkeit von 15 Ziffern.</span><span class="sxs-lookup"><span data-stu-id="27830-1234">Contains a floating point number with 15-digit precision</span></span>   | <span data-ttu-id="27830-1235">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="27830-1235">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="27830-1236">**EDM. float**</span><span class="sxs-lookup"><span data-stu-id="27830-1236">**Edm.Float**</span></span>                    | <span data-ttu-id="27830-1237">Enthält eine Gleitkommazahl mit einer Genauigkeit von 7 Stellen.</span><span class="sxs-lookup"><span data-stu-id="27830-1237">Contains a floating point number with 7-digit precision.</span></span>   | <span data-ttu-id="27830-1238">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="27830-1238">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="27830-1239">**Edm.Guid**</span><span class="sxs-lookup"><span data-stu-id="27830-1239">**Edm.Guid**</span></span>                     | <span data-ttu-id="27830-1240">Enthält einen eindeutigen 16-Byte-Bezeichner.</span><span class="sxs-lookup"><span data-stu-id="27830-1240">Contains a 16-byte unique identifier.</span></span>                      | <span data-ttu-id="27830-1241">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="27830-1241">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="27830-1242">**Edm.Int16**</span><span class="sxs-lookup"><span data-stu-id="27830-1242">**Edm.Int16**</span></span>                    | <span data-ttu-id="27830-1243">Enthält einen 16-Bit-Ganzzahlwert mit Vorzeichen.</span><span class="sxs-lookup"><span data-stu-id="27830-1243">Contains a signed 16-bit integer value.</span></span>                    | <span data-ttu-id="27830-1244">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="27830-1244">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="27830-1245">**Edm.Int32**</span><span class="sxs-lookup"><span data-stu-id="27830-1245">**Edm.Int32**</span></span>                    | <span data-ttu-id="27830-1246">Enthält einen 32-Bit-Ganzzahlwert mit Vorzeichen.</span><span class="sxs-lookup"><span data-stu-id="27830-1246">Contains a signed 32-bit integer value.</span></span>                    | <span data-ttu-id="27830-1247">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="27830-1247">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="27830-1248">**Edm.Int64**</span><span class="sxs-lookup"><span data-stu-id="27830-1248">**Edm.Int64**</span></span>                    | <span data-ttu-id="27830-1249">Enthält einen 64-Bit-Ganzzahlwert mit Vorzeichen.</span><span class="sxs-lookup"><span data-stu-id="27830-1249">Contains a signed 64-bit integer value.</span></span>                    | <span data-ttu-id="27830-1250">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="27830-1250">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="27830-1251">**Edm.SByte**</span><span class="sxs-lookup"><span data-stu-id="27830-1251">**Edm.SByte**</span></span>                    | <span data-ttu-id="27830-1252">Enthält einen 8-Bit-Ganzzahlwert mit Vorzeichen.</span><span class="sxs-lookup"><span data-stu-id="27830-1252">Contains a signed 8-bit integer value.</span></span>                     | <span data-ttu-id="27830-1253">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="27830-1253">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="27830-1254">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="27830-1254">**Edm.String**</span></span>                   | <span data-ttu-id="27830-1255">Enthält Zeichendaten.</span><span class="sxs-lookup"><span data-stu-id="27830-1255">Contains character data.</span></span>                                   | <span data-ttu-id="27830-1256">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="27830-1256">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span></span> |
| <span data-ttu-id="27830-1257">**Edm.Time**</span><span class="sxs-lookup"><span data-stu-id="27830-1257">**Edm.Time**</span></span>                     | <span data-ttu-id="27830-1258">Enthält eine Uhrzeit.</span><span class="sxs-lookup"><span data-stu-id="27830-1258">Contains a time of day.</span></span>                                    | <span data-ttu-id="27830-1259">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="27830-1259">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="27830-1260">**EDM. geography**</span><span class="sxs-lookup"><span data-stu-id="27830-1260">**Edm.Geography**</span></span>                |                                                            | <span data-ttu-id="27830-1261">Nullable, Standard, SRID</span><span class="sxs-lookup"><span data-stu-id="27830-1261">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="27830-1262">**Edm.GeographyPoint**</span><span class="sxs-lookup"><span data-stu-id="27830-1262">**Edm.GeographyPoint**</span></span>           |                                                            | <span data-ttu-id="27830-1263">Nullable, Standard, SRID</span><span class="sxs-lookup"><span data-stu-id="27830-1263">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="27830-1264">**EDM. geographylinestring**</span><span class="sxs-lookup"><span data-stu-id="27830-1264">**Edm.GeographyLineString**</span></span>      |                                                            | <span data-ttu-id="27830-1265">Nullable, Standard, SRID</span><span class="sxs-lookup"><span data-stu-id="27830-1265">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="27830-1266">**EDM. geographypolygon**</span><span class="sxs-lookup"><span data-stu-id="27830-1266">**Edm.GeographyPolygon**</span></span>         |                                                            | <span data-ttu-id="27830-1267">Nullable, Standard, SRID</span><span class="sxs-lookup"><span data-stu-id="27830-1267">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="27830-1268">**EDM. geographymultipoint**</span><span class="sxs-lookup"><span data-stu-id="27830-1268">**Edm.GeographyMultiPoint**</span></span>      |                                                            | <span data-ttu-id="27830-1269">Nullable, Standard, SRID</span><span class="sxs-lookup"><span data-stu-id="27830-1269">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="27830-1270">**EDM. geographymultilinestring**</span><span class="sxs-lookup"><span data-stu-id="27830-1270">**Edm.GeographyMultiLineString**</span></span> |                                                            | <span data-ttu-id="27830-1271">Nullable, Standard, SRID</span><span class="sxs-lookup"><span data-stu-id="27830-1271">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="27830-1272">**EDM. geographymultipolygon**</span><span class="sxs-lookup"><span data-stu-id="27830-1272">**Edm.GeographyMultiPolygon**</span></span>    |                                                            | <span data-ttu-id="27830-1273">Nullable, Standard, SRID</span><span class="sxs-lookup"><span data-stu-id="27830-1273">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="27830-1274">**EDM. geographycollection**</span><span class="sxs-lookup"><span data-stu-id="27830-1274">**Edm.GeographyCollection**</span></span>      |                                                            | <span data-ttu-id="27830-1275">Nullable, Standard, SRID</span><span class="sxs-lookup"><span data-stu-id="27830-1275">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="27830-1276">**EDM. Geometry**</span><span class="sxs-lookup"><span data-stu-id="27830-1276">**Edm.Geometry**</span></span>                 |                                                            | <span data-ttu-id="27830-1277">Nullable, Standard, SRID</span><span class="sxs-lookup"><span data-stu-id="27830-1277">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="27830-1278">**EDM. geometryPoint**</span><span class="sxs-lookup"><span data-stu-id="27830-1278">**Edm.GeometryPoint**</span></span>            |                                                            | <span data-ttu-id="27830-1279">Nullable, Standard, SRID</span><span class="sxs-lookup"><span data-stu-id="27830-1279">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="27830-1280">**EDM. geometrylinestring**</span><span class="sxs-lookup"><span data-stu-id="27830-1280">**Edm.GeometryLineString**</span></span>       |                                                            | <span data-ttu-id="27830-1281">Nullable, Standard, SRID</span><span class="sxs-lookup"><span data-stu-id="27830-1281">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="27830-1282">**EDM. geometrypolygon**</span><span class="sxs-lookup"><span data-stu-id="27830-1282">**Edm.GeometryPolygon**</span></span>          |                                                            | <span data-ttu-id="27830-1283">Nullable, Standard, SRID</span><span class="sxs-lookup"><span data-stu-id="27830-1283">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="27830-1284">**EDM. geometrymultipoint**</span><span class="sxs-lookup"><span data-stu-id="27830-1284">**Edm.GeometryMultiPoint**</span></span>       |                                                            | <span data-ttu-id="27830-1285">Nullable, Standard, SRID</span><span class="sxs-lookup"><span data-stu-id="27830-1285">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="27830-1286">**EDM. geometryMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="27830-1286">**Edm.GeometryMultiLineString**</span></span>  |                                                            | <span data-ttu-id="27830-1287">Nullable, Standard, SRID</span><span class="sxs-lookup"><span data-stu-id="27830-1287">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="27830-1288">**EDM. geometrymultipolygon**</span><span class="sxs-lookup"><span data-stu-id="27830-1288">**Edm.GeometryMultiPolygon**</span></span>     |                                                            | <span data-ttu-id="27830-1289">Nullable, Standard, SRID</span><span class="sxs-lookup"><span data-stu-id="27830-1289">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="27830-1290">**EDM. GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="27830-1290">**Edm.GeometryCollection**</span></span>       |                                                            | <span data-ttu-id="27830-1291">Nullable, Standard, SRID</span><span class="sxs-lookup"><span data-stu-id="27830-1291">Nullable, Default, SRID</span></span>                                                  |

## <a name="facets-csdl"></a><span data-ttu-id="27830-1292">Facets (CSDL)</span><span class="sxs-lookup"><span data-stu-id="27830-1292">Facets (CSDL)</span></span>

<span data-ttu-id="27830-1293">Facets in konzeptioneller Schemadefinitionssprache (CSDL) stellen Einschränkungen für Eigenschaften von Entitätstypen und komplexen Typen dar.</span><span class="sxs-lookup"><span data-stu-id="27830-1293">Facets in conceptual schema definition language (CSDL) represent constraints on properties of entity types and complex types.</span></span> <span data-ttu-id="27830-1294">Facets werden in den folgenden CSDL-Elementen als XML-Attribute angezeigt:</span><span class="sxs-lookup"><span data-stu-id="27830-1294">Facets appear as XML attributes on the following CSDL elements:</span></span>

-   <span data-ttu-id="27830-1295">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="27830-1295">Property</span></span>
-   <span data-ttu-id="27830-1296">TypeRef</span><span class="sxs-lookup"><span data-stu-id="27830-1296">TypeRef</span></span>
-   <span data-ttu-id="27830-1297">Parameter</span><span class="sxs-lookup"><span data-stu-id="27830-1297">Parameter</span></span>

<span data-ttu-id="27830-1298">In der folgenden Tabelle werden die in CSDL unterstützten Facets beschrieben.</span><span class="sxs-lookup"><span data-stu-id="27830-1298">The following table describes the facets that are supported in CSDL.</span></span> <span data-ttu-id="27830-1299">Alle Facets sind optional.</span><span class="sxs-lookup"><span data-stu-id="27830-1299">All facets are optional.</span></span> <span data-ttu-id="27830-1300">Einige der unten aufgeführten Aspekte werden vom Entity Framework beim Erstellen einer Datenbank aus einem konzeptionellen Modell verwendet.</span><span class="sxs-lookup"><span data-stu-id="27830-1300">Some facets listed below are used by the Entity Framework when generating a database from a conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="27830-1301">Informationen zu Datentypen in einem konzeptionellen Modell finden Sie unter konzeptionelle Modelltypen (CSDL).</span><span class="sxs-lookup"><span data-stu-id="27830-1301">For information about data types in a conceptual model, see Conceptual Model Types (CSDL).</span></span>

| <span data-ttu-id="27830-1302">Facet</span><span class="sxs-lookup"><span data-stu-id="27830-1302">Facet</span></span>               | <span data-ttu-id="27830-1303">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="27830-1303">Description</span></span>                                                                                                                                                                                                                                                   | <span data-ttu-id="27830-1304">Gilt für:</span><span class="sxs-lookup"><span data-stu-id="27830-1304">Applies to</span></span>                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="27830-1305">Wird für die Datenbankgenerierung verwendet</span><span class="sxs-lookup"><span data-stu-id="27830-1305">Used for the database generation</span></span> | <span data-ttu-id="27830-1306">Wird von der Laufzeit verwendet</span><span class="sxs-lookup"><span data-stu-id="27830-1306">Used by the runtime</span></span> |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| <span data-ttu-id="27830-1307">**Sortierung**</span><span class="sxs-lookup"><span data-stu-id="27830-1307">**Collation**</span></span>       | <span data-ttu-id="27830-1308">Gibt die bei Vergleich- und Sortiervorgängen zu verwendende Sortierreihenfolge für die Werte der Eigenschaft an.</span><span class="sxs-lookup"><span data-stu-id="27830-1308">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                               | <span data-ttu-id="27830-1309">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="27830-1309">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="27830-1310">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-1310">Yes</span></span>                              | <span data-ttu-id="27830-1311">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1311">No</span></span>                  |
| <span data-ttu-id="27830-1312">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="27830-1312">**ConcurrencyMode**</span></span> | <span data-ttu-id="27830-1313">Gibt an, dass der Eigenschaftswert für Prüfungen der vollständigen Parallelität verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="27830-1313">Indicates that the value of the property should be used for optimistic concurrency checks.</span></span>                                                                                                                                                                    | <span data-ttu-id="27830-1314">Alle **edmsimpletype** -Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="27830-1314">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="27830-1315">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1315">No</span></span>                               | <span data-ttu-id="27830-1316">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-1316">Yes</span></span>                 |
| <span data-ttu-id="27830-1317">**Standard**</span><span class="sxs-lookup"><span data-stu-id="27830-1317">**Default**</span></span>         | <span data-ttu-id="27830-1318">Gibt den Standardwert der Eigenschaft an, wenn bei der Instanziierung kein Wert angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="27830-1318">Specifies the default value of the property if no value is supplied upon instantiation.</span></span>                                                                                                                                                                       | <span data-ttu-id="27830-1319">Alle **edmsimpletype** -Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="27830-1319">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="27830-1320">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-1320">Yes</span></span>                              | <span data-ttu-id="27830-1321">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-1321">Yes</span></span>                 |
| <span data-ttu-id="27830-1322">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="27830-1322">**FixedLength**</span></span>     | <span data-ttu-id="27830-1323">Gibt an, ob sich die Länge des Eigenschaftswerts ändern kann.</span><span class="sxs-lookup"><span data-stu-id="27830-1323">Specifies whether the length of the property value can vary.</span></span>                                                                                                                                                                                                  | <span data-ttu-id="27830-1324">**Edm. Binary**, **Edm. String**</span><span class="sxs-lookup"><span data-stu-id="27830-1324">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="27830-1325">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-1325">Yes</span></span>                              | <span data-ttu-id="27830-1326">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1326">No</span></span>                  |
| <span data-ttu-id="27830-1327">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="27830-1327">**MaxLength**</span></span>       | <span data-ttu-id="27830-1328">Gibt die maximale Länge des Eigenschaftswerts an.</span><span class="sxs-lookup"><span data-stu-id="27830-1328">Specifies the maximum length of the property value.</span></span>                                                                                                                                                                                                           | <span data-ttu-id="27830-1329">**Edm. Binary**, **Edm. String**</span><span class="sxs-lookup"><span data-stu-id="27830-1329">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="27830-1330">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-1330">Yes</span></span>                              | <span data-ttu-id="27830-1331">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1331">No</span></span>                  |
| <span data-ttu-id="27830-1332">**NULL zulassen**</span><span class="sxs-lookup"><span data-stu-id="27830-1332">**Nullable**</span></span>        | <span data-ttu-id="27830-1333">Gibt an, ob die Eigenschaft einen **null** -Wert aufweisen kann.</span><span class="sxs-lookup"><span data-stu-id="27830-1333">Specifies whether the property can have a **null** value.</span></span>                                                                                                                                                                                                     | <span data-ttu-id="27830-1334">Alle **edmsimpletype** -Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="27830-1334">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="27830-1335">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-1335">Yes</span></span>                              | <span data-ttu-id="27830-1336">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-1336">Yes</span></span>                 |
| <span data-ttu-id="27830-1337">**Genauigkeit**</span><span class="sxs-lookup"><span data-stu-id="27830-1337">**Precision**</span></span>       | <span data-ttu-id="27830-1338">Gibt bei Eigenschaften vom Typ **Decimal**die Anzahl der Ziffern an, die ein Eigenschafts Wert aufweisen kann.</span><span class="sxs-lookup"><span data-stu-id="27830-1338">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="27830-1339">Bei Eigenschaften vom Typ **time**, **DateTime**und **DateTimeOffset**wird die Anzahl der Ziffern für den Bruchteil der Sekunden des Eigenschafts Werts angegeben.</span><span class="sxs-lookup"><span data-stu-id="27830-1339">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the property value.</span></span> | <span data-ttu-id="27830-1340">**Edm. DateTime**, **Edm. DateTimeOffset**, **Edm. Decimal**, **Edm. Time**</span><span class="sxs-lookup"><span data-stu-id="27830-1340">**Edm.DateTime**, **Edm.DateTimeOffset**, **Edm.Decimal**, **Edm.Time**</span></span>                                                                                                                                                                                                                                                                                                              | <span data-ttu-id="27830-1341">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-1341">Yes</span></span>                              | <span data-ttu-id="27830-1342">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1342">No</span></span>                  |
| <span data-ttu-id="27830-1343">**Skalierung**</span><span class="sxs-lookup"><span data-stu-id="27830-1343">**Scale**</span></span>           | <span data-ttu-id="27830-1344">Gibt die Anzahl der Dezimalstellen für den Eigenschaftswert an.</span><span class="sxs-lookup"><span data-stu-id="27830-1344">Specifies the number of digits to the right of the decimal point for the property value.</span></span>                                                                                                                                                                      | <span data-ttu-id="27830-1345">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="27830-1345">**Edm.Decimal**</span></span>                                                                                                                                                                                                                                                                                                                                                                      | <span data-ttu-id="27830-1346">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-1346">Yes</span></span>                              | <span data-ttu-id="27830-1347">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1347">No</span></span>                  |
| <span data-ttu-id="27830-1348">**SRID**</span><span class="sxs-lookup"><span data-stu-id="27830-1348">**SRID**</span></span>            | <span data-ttu-id="27830-1349">Gibt die System-ID des räumlichen System Verweises an.</span><span class="sxs-lookup"><span data-stu-id="27830-1349">Specifies the Spatial System Reference System ID.</span></span> <span data-ttu-id="27830-1350">Weitere Informationen finden Sie unter [SRID](https://en.wikipedia.org/wiki/SRID) und [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="27830-1350">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span>                                                              | <span data-ttu-id="27830-1351">**EDM. Geography, EDM. geographypoint, EDM. geographylinestring, EDM. geographypolygon, EDM. geographymultipoint, EDM. geographymultilinestring, EDM. geographymultipolygon, EDM. geographycollection, EDM. Geometry, EDM. geometryPoint, EDM. geometrylinestring, EDM. geometrypolygon, EDM. geometrymultipoint, EDM. geometryMultiLineString, EDM. geometrymultipolygon, EDM. GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="27830-1351">**Edm.Geography, Edm.GeographyPoint, Edm.GeographyLineString, Edm.GeographyPolygon, Edm.GeographyMultiPoint, Edm.GeographyMultiLineString, Edm.GeographyMultiPolygon, Edm.GeographyCollection, Edm.Geometry, Edm.GeometryPoint, Edm.GeometryLineString, Edm.GeometryPolygon, Edm.GeometryMultiPoint, Edm.GeometryMultiLineString, Edm.GeometryMultiPolygon, Edm.GeometryCollection**</span></span> | <span data-ttu-id="27830-1352">Nein</span><span class="sxs-lookup"><span data-stu-id="27830-1352">No</span></span>                               | <span data-ttu-id="27830-1353">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-1353">Yes</span></span>                 |
| <span data-ttu-id="27830-1354">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="27830-1354">**Unicode**</span></span>         | <span data-ttu-id="27830-1355">Gibt an, ob der Eigenschaftswert als Unicode gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="27830-1355">Indicates whether the property value is stored as Unicode.</span></span>                                                                                                                                                                                                    | <span data-ttu-id="27830-1356">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="27830-1356">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="27830-1357">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-1357">Yes</span></span>                              | <span data-ttu-id="27830-1358">Ja</span><span class="sxs-lookup"><span data-stu-id="27830-1358">Yes</span></span>                 |

>[!NOTE]
> <span data-ttu-id="27830-1359">Beim Generieren einer Datenbank aus einem konzeptionellen Modell erkennt der Assistent zum Generieren von Datenbanken den Wert des **StoreGeneratedPattern** -Attributs für ein **Eigenschafts** Element, wenn er sich im folgenden Namespace befindet: https://schemas.microsoft.com/ado/2009/02/edm/annotation .</span><span class="sxs-lookup"><span data-stu-id="27830-1359">When generating a database from a conceptual model, the Generate Database Wizard will recognize the value of the **StoreGeneratedPattern** attribute on a **Property** element if it is in the following namespace: https://schemas.microsoft.com/ado/2009/02/edm/annotation.</span></span> <span data-ttu-id="27830-1360">Die unterstützten Werte für das Attribut sind **Identity** und **berechnete**Werte.</span><span class="sxs-lookup"><span data-stu-id="27830-1360">The supported values for the attribute are **Identity** and **Computed**.</span></span> <span data-ttu-id="27830-1361">Bei **einem Identitäts Wert wird eine** Daten Bank Spalte mit einem Identitäts Wert erstellt, der in der Datenbank generiert wird.</span><span class="sxs-lookup"><span data-stu-id="27830-1361">A value of **Identity** will produce a database column with an identity value that is generated in the database.</span></span> <span data-ttu-id="27830-1362">Der **berechnete** Wert erstellt eine Spalte mit einem Wert, der in der Datenbank berechnet wird.</span><span class="sxs-lookup"><span data-stu-id="27830-1362">A value of **Computed** will produce a column with a value that is computed in the database.</span></span>

### <a name="example"></a><span data-ttu-id="27830-1363">Beispiel</span><span class="sxs-lookup"><span data-stu-id="27830-1363">Example</span></span>

<span data-ttu-id="27830-1364">Das folgende Beispiel zeigt für die Eigenschaften eines Entitätstyps übernommene Facets:</span><span class="sxs-lookup"><span data-stu-id="27830-1364">The following example shows facets applied to the properties of an entity type:</span></span>

``` xml
 <EntityType Name="Product">
   <Key>
     <PropertyRef Name="ProductId" />
   </Key>
   <Property Type="Int32"
             Name="ProductId" Nullable="false"
             a:StoreGeneratedPattern="Identity"
    xmlns:a="https://schemas.microsoft.com/ado/2009/02/edm/annotation" />
   <Property Type="String"
             Name="ProductName"
             Nullable="false"
             MaxLength="50" />
   <Property Type="String"
             Name="Location"
             Nullable="true"
             MaxLength="25" />
 </EntityType>
```
