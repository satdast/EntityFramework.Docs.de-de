---
title: SSDL-Spezifikation-EF6
description: SSDL-Spezifikation in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/ssdl-spec
ms.openlocfilehash: dbcd4c771e8ca1324a0520f9c8ca13ba4661487b
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070210"
---
# <a name="ssdl-specification"></a><span data-ttu-id="da500-103">SSDL-Spezifikation</span><span class="sxs-lookup"><span data-stu-id="da500-103">SSDL Specification</span></span>
<span data-ttu-id="da500-104">Die Datenspeicherschema-Definitionssprache (Store Schema Definition Language, SSDL) ist eine XML-basierte Sprache, die das Speichermodell einer Entity Framework-Anwendung beschreibt.</span><span class="sxs-lookup"><span data-stu-id="da500-104">Store schema definition language (SSDL) is an XML-based language that describes the storage model of an Entity Framework application.</span></span>

<span data-ttu-id="da500-105">In einer Entity Framework Anwendung werden die Metadaten des Speicher Modells aus einer SSDL-Datei (geschrieben in SSDL) in eine Instanz der System. Data. Metadata. Edm. StoreItemCollection geladen und können mithilfe von Methoden in der System. Data. Metadata. Edm. MetadataWorkspace-Klasse aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="da500-105">In an Entity Framework application, storage model metadata is loaded from a .ssdl file (written in SSDL) into an instance of the System.Data.Metadata.Edm.StoreItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="da500-106">Entity Framework verwendet Metadaten des Speicher Modells, um Abfragen für das konzeptionelle Modell in Speicher spezifische Befehle zu übersetzen.</span><span class="sxs-lookup"><span data-stu-id="da500-106">Entity Framework uses storage model metadata to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="da500-107">Der Entity Framework Designer (EF-Designer) speichert Speichermodell Informationen in einer EDMX-Datei zur Entwurfszeit.</span><span class="sxs-lookup"><span data-stu-id="da500-107">The Entity Framework Designer (EF Designer) stores storage model information in an .edmx file at design time.</span></span> <span data-ttu-id="da500-108">Zum Zeitpunkt der Erstellung verwendet der Entity Designer Informationen in einer EDMX-Datei, um die SSDL-Datei zu erstellen, die zur Laufzeit von Entity Framework benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="da500-108">At build time the Entity Designer uses information in an .edmx file to create the .ssdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="da500-109">Die verschiedenen Versionen von SSDL werden durch XML-Namespaces unterschieden.</span><span class="sxs-lookup"><span data-stu-id="da500-109">Versions of SSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="da500-110">SSDL-Version</span><span class="sxs-lookup"><span data-stu-id="da500-110">SSDL Version</span></span> | <span data-ttu-id="da500-111">XML-Namespace</span><span class="sxs-lookup"><span data-stu-id="da500-111">XML Namespace</span></span>                                     |
|:-------------|:--------------------------------------------------|
| <span data-ttu-id="da500-112">SSDL v1</span><span class="sxs-lookup"><span data-stu-id="da500-112">SSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| <span data-ttu-id="da500-113">SSDL v2</span><span class="sxs-lookup"><span data-stu-id="da500-113">SSDL v2</span></span>      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| <span data-ttu-id="da500-114">SSDL v3</span><span class="sxs-lookup"><span data-stu-id="da500-114">SSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a><span data-ttu-id="da500-115">Zuordnungselement (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-115">Association Element (SSDL)</span></span>

<span data-ttu-id="da500-116">Ein **Association** -Element in der Datenspeicher Schema-Definitions Sprache (SSDL) gibt Tabellen Spalten an, die an einer FOREIGN KEY-Einschränkung in der zugrunde liegenden Datenbank beteiligt sind.</span><span class="sxs-lookup"><span data-stu-id="da500-116">An **Association** element in store schema definition language (SSDL) specifies table columns that participate in a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="da500-117">Zwei erforderliche untergeordnete End-Elemente geben die Tabellen an den Enden der Zuordnung und die Multiplizität an jedem Ende an.</span><span class="sxs-lookup"><span data-stu-id="da500-117">Two required child End elements specify tables at the ends of the association and the multiplicity at each end.</span></span> <span data-ttu-id="da500-118">Ein optionales ReferentialConstraint-Element gibt die Prinzipal- und die abhängigen Enden der Zuordnung sowie die teilnehmenden Spalten an.</span><span class="sxs-lookup"><span data-stu-id="da500-118">An optional ReferentialConstraint element specifies the principal and dependent ends of the association as well as the participating columns.</span></span> <span data-ttu-id="da500-119">Wenn kein **referentialeinschränkungselement** vorhanden ist, muss ein AssociationSetMapping-Element verwendet werden, um die Spalten Zuordnungen für die Zuordnung anzugeben.</span><span class="sxs-lookup"><span data-stu-id="da500-119">If no **ReferentialConstraint** element is present, an AssociationSetMapping element must be used to specify the column mappings for the association.</span></span>

<span data-ttu-id="da500-120">Das **Association** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="da500-120">The **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="da500-121">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-121">Documentation (zero or one)</span></span>
-   <span data-ttu-id="da500-122">End (genau zwei Elemente)</span><span class="sxs-lookup"><span data-stu-id="da500-122">End (exactly two)</span></span>
-   <span data-ttu-id="da500-123">ReferentialConstraint (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-123">ReferentialConstraint (zero or one)</span></span>
-   <span data-ttu-id="da500-124">Anmerkungselemente (kein (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="da500-124">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="da500-125">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-125">Applicable Attributes</span></span>

<span data-ttu-id="da500-126">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Association** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="da500-126">The following table describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="da500-127">Attributname</span><span class="sxs-lookup"><span data-stu-id="da500-127">Attribute Name</span></span> | <span data-ttu-id="da500-128">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="da500-128">Is Required</span></span> | <span data-ttu-id="da500-129">Wert</span><span class="sxs-lookup"><span data-stu-id="da500-129">Value</span></span>                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="da500-130">**Name**</span><span class="sxs-lookup"><span data-stu-id="da500-130">**Name**</span></span>       | <span data-ttu-id="da500-131">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-131">Yes</span></span>         | <span data-ttu-id="da500-132">Der Name der entsprechenden Fremdschlüsseleinschränkung in der zugrunde liegenden Datenbank.</span><span class="sxs-lookup"><span data-stu-id="da500-132">The name of the corresponding foreign key constraint in the underlying database.</span></span> |

> [!NOTE]
> <span data-ttu-id="da500-133">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Association** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-133">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="da500-134">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für SSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-134">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="da500-135">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-135">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="da500-136">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-136">Example</span></span>

<span data-ttu-id="da500-137">Das folgende Beispiel zeigt ein **Association** -Element, das ein **referentialeinschränkungs** -Element verwendet, um die Spalten anzugeben, die an der FOREIGN KEY-Einschränkung von **FK \_ CustomerOrders** beteiligt sind:</span><span class="sxs-lookup"><span data-stu-id="da500-137">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="associationset-element-ssdl"></a><span data-ttu-id="da500-138">AssociationSet-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-138">AssociationSet Element (SSDL)</span></span>

<span data-ttu-id="da500-139">Das **AssociationSet** -Element in der Datenspeicher Schema-Definitions Sprache (SSDL) stellt eine FOREIGN KEY-Einschränkung zwischen zwei Tabellen in der zugrunde liegenden Datenbank dar.</span><span class="sxs-lookup"><span data-stu-id="da500-139">The **AssociationSet** element in store schema definition language (SSDL) represents a foreign key constraint between two tables in the underlying database.</span></span> <span data-ttu-id="da500-140">Die Tabellenspalten, die an der Fremdschlüsseleinschränkung teilnehmen, werden in einem Association-Element angegeben.</span><span class="sxs-lookup"><span data-stu-id="da500-140">The table columns that participate in the foreign key constraint are specified in an Association element.</span></span> <span data-ttu-id="da500-141">Das **Association** -Element, das einem bestimmten **AssociationSet** -Element entspricht, wird im **Association** -Attribut des **AssociationSet** -Elements angegeben.</span><span class="sxs-lookup"><span data-stu-id="da500-141">The **Association** element that corresponds to a given **AssociationSet** element is specified in the **Association** attribute of the **AssociationSet** element.</span></span>

<span data-ttu-id="da500-142">SSDL-Zuordnungssätze werden CSDL-Zuordnungssätzen durch ein AssociationSetMapping-Element zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="da500-142">SSDL association sets are mapped to CSDL association sets by an AssociationSetMapping element.</span></span> <span data-ttu-id="da500-143">Wenn jedoch die CSDL-Zuordnung für einen bestimmten CSDL-Zuordnungs Satz mithilfe eines referentialeinschränkung-Elements definiert wird, ist kein entsprechendes **AssociationSetMapping** -Element erforderlich.</span><span class="sxs-lookup"><span data-stu-id="da500-143">However, if the CSDL association for a given CSDL association set is defined by using a ReferentialConstraint element , no corresponding **AssociationSetMapping** element is necessary.</span></span> <span data-ttu-id="da500-144">Wenn in diesem Fall ein **AssociationSetMapping** -Element vorhanden ist, werden die von ihm definierten Zuordnungen durch das **referentialeinschränkung** -Element überschrieben.</span><span class="sxs-lookup"><span data-stu-id="da500-144">In this case, if an **AssociationSetMapping** element is present, the mappings it defines will be overridden by the **ReferentialConstraint** element.</span></span>

<span data-ttu-id="da500-145">Das **AssociationSet** -Element kann über die folgenden untergeordneten Elemente verfügen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="da500-145">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="da500-146">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-146">Documentation (zero or one)</span></span>
-   <span data-ttu-id="da500-147">End (kein (null) oder zwei Elemente)</span><span class="sxs-lookup"><span data-stu-id="da500-147">End (zero or two)</span></span>
-   <span data-ttu-id="da500-148">Anmerkungselemente (kein (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="da500-148">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="da500-149">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-149">Applicable Attributes</span></span>

<span data-ttu-id="da500-150">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **AssociationSet** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="da500-150">The following table describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="da500-151">Attributname</span><span class="sxs-lookup"><span data-stu-id="da500-151">Attribute Name</span></span>  | <span data-ttu-id="da500-152">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="da500-152">Is Required</span></span> | <span data-ttu-id="da500-153">Wert</span><span class="sxs-lookup"><span data-stu-id="da500-153">Value</span></span>                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="da500-154">**Name**</span><span class="sxs-lookup"><span data-stu-id="da500-154">**Name**</span></span>        | <span data-ttu-id="da500-155">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-155">Yes</span></span>         | <span data-ttu-id="da500-156">Der Name der Fremdschlüsseleinschränkung, die der Zuordnungssatz darstellt.</span><span class="sxs-lookup"><span data-stu-id="da500-156">The name of the foreign key constraint that the association set represents.</span></span>                          |
| <span data-ttu-id="da500-157">**Zuordnung**</span><span class="sxs-lookup"><span data-stu-id="da500-157">**Association**</span></span> | <span data-ttu-id="da500-158">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-158">Yes</span></span>         | <span data-ttu-id="da500-159">Der Name der Zuordnung, die die Spalten definiert, die an der Fremdschlüsseleinschränkung teilnehmen.</span><span class="sxs-lookup"><span data-stu-id="da500-159">The name of the association that defines the columns that participate in the foreign key constraint.</span></span> |

> [!NOTE]
> <span data-ttu-id="da500-160">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **AssociationSet** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-160">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="da500-161">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für SSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-161">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="da500-162">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-162">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="da500-163">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-163">Example</span></span>

<span data-ttu-id="da500-164">Das folgende Beispiel zeigt ein **AssociationSet** -Element, das die `FK_CustomerOrders` Foreign Key-Einschränkung in der zugrunde liegenden Datenbank darstellt:</span><span class="sxs-lookup"><span data-stu-id="da500-164">The following example shows an **AssociationSet** element that represents the `FK_CustomerOrders` foreign key constraint in the underlying database:</span></span>

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a><span data-ttu-id="da500-165">CollectionType-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-165">CollectionType Element (SSDL)</span></span>

<span data-ttu-id="da500-166">Das **CollectionType** -Element in der Speicher Schema-Definitions Sprache (SSDL) gibt an, dass der Rückgabetyp einer Funktion eine Auflistung ist.</span><span class="sxs-lookup"><span data-stu-id="da500-166">The **CollectionType** element in store schema definition language (SSDL) specifies that a function’s return type is a collection.</span></span> <span data-ttu-id="da500-167">Das **CollectionType** -Element ist ein untergeordnetes Element des ReturnType-Elements.</span><span class="sxs-lookup"><span data-stu-id="da500-167">The **CollectionType** element is a child of the ReturnType element.</span></span> <span data-ttu-id="da500-168">Der Sammlungstyp wird mithilfe des untergeordneten RowType-Elements angegeben:</span><span class="sxs-lookup"><span data-stu-id="da500-168">The type of collection is specified by using the RowType child element:</span></span>

> [!NOTE]
> <span data-ttu-id="da500-169">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **CollectionType** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-169">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="da500-170">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für SSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-170">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="da500-171">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-171">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="da500-172">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-172">Example</span></span>

<span data-ttu-id="da500-173">Das folgende Beispiel zeigt eine Funktion, die ein **CollectionType** -Element verwendet, um anzugeben, dass die Funktion eine Auflistung von Zeilen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="da500-173">The following example shows a function that uses a **CollectionType** element to specify that the function returns a collection of rows.</span></span>

``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```

## <a name="commandtext-element-ssdl"></a><span data-ttu-id="da500-174">CommandText-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-174">CommandText Element (SSDL)</span></span>

<span data-ttu-id="da500-175">Das **CommandText** -Element in der Datenspeicher Schema-Definitions Sprache (SSDL) ist ein untergeordnetes Element des Function-Elements, mit dem Sie eine SQL-Anweisung definieren können, die in der Datenbank ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="da500-175">The **CommandText** element in store schema definition language (SSDL) is a child of the Function element that allows you to define a SQL statement that is executed at the database.</span></span> <span data-ttu-id="da500-176">Mit dem **CommandText** -Element können Sie Funktionen hinzufügen, die einer gespeicherten Prozedur in der-Datenbank ähneln, aber Sie definieren das **CommandText** -Element im Speichermodell.</span><span class="sxs-lookup"><span data-stu-id="da500-176">The **CommandText** element allows you to add functionality that is similar to a stored procedure in the database, but you define the **CommandText** element in the storage model.</span></span>

<span data-ttu-id="da500-177">Das **CommandText** -Element darf keine untergeordneten Elemente aufweisen.</span><span class="sxs-lookup"><span data-stu-id="da500-177">The **CommandText** element cannot have child elements.</span></span> <span data-ttu-id="da500-178">Der **Text des CommandText** -Elements muss eine gültige SQL-Anweisung für die zugrunde liegende Datenbank sein.</span><span class="sxs-lookup"><span data-stu-id="da500-178">The body of the **CommandText** element must be a valid SQL statement for the underlying database.</span></span>

<span data-ttu-id="da500-179">Für das **CommandText** -Element sind keine Attribute anwendbar.</span><span class="sxs-lookup"><span data-stu-id="da500-179">No attributes are applicable to the **CommandText** element.</span></span>

### <a name="example"></a><span data-ttu-id="da500-180">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-180">Example</span></span>

<span data-ttu-id="da500-181">Das folgende Beispiel zeigt ein **Function** -Element mit einem untergeordneten **CommandText** -Element.</span><span class="sxs-lookup"><span data-stu-id="da500-181">The following example shows a **Function** element with a child **CommandText** element.</span></span> <span data-ttu-id="da500-182">Machen Sie die Funktion **updateproductinorder** als Methode für ObjectContext verfügbar, indem Sie Sie in das konzeptionelle Modell importieren.</span><span class="sxs-lookup"><span data-stu-id="da500-182">Expose the **UpdateProductInOrder** function as a method on the ObjectContext by importing it into the conceptual model.</span></span>  

``` xml
 <Function Name="UpdateProductInOrder" IsComposable="false">
   <CommandText>
     UPDATE Orders
     SET ProductId = @productId
     WHERE OrderId = @orderId;
   </CommandText>
   <Parameter Name="productId"
              Mode="In"
              Type="int"/>
   <Parameter Name="orderId"
              Mode="In"
              Type="int"/>
 </Function>
```

## <a name="definingquery-element-ssdl"></a><span data-ttu-id="da500-183">DefiningQuery-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-183">DefiningQuery Element (SSDL)</span></span>

<span data-ttu-id="da500-184">Mit dem **DefiningQuery** -Element in der Datenspeicher Schema-Definitions Sprache (SSDL) können Sie eine SQL-Anweisung direkt in der zugrunde liegenden Datenbank ausführen.</span><span class="sxs-lookup"><span data-stu-id="da500-184">The **DefiningQuery** element in store schema definition language (SSDL) allows you to execute a SQL statement directly in the underlying database.</span></span> <span data-ttu-id="da500-185">Das **DefiningQuery** -Element wird häufig wie eine Daten Bank Sicht verwendet, aber die Sicht wird im Speichermodell anstelle der Datenbank definiert.</span><span class="sxs-lookup"><span data-stu-id="da500-185">The **DefiningQuery** element is commonly used like a database view, but the view is defined in the storage model instead of the database.</span></span> <span data-ttu-id="da500-186">Die in einem **DefiningQuery** -Element definierte Sicht kann mithilfe eines EntitySetMapping-Elements einem Entitätstyp im konzeptionellen Modell zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-186">The view defined in a **DefiningQuery** element can be mapped to an entity type in the conceptual model through an EntitySetMapping element.</span></span> <span data-ttu-id="da500-187">Diese Zuordnungen sind schreibgeschützt.</span><span class="sxs-lookup"><span data-stu-id="da500-187">These mappings are read-only.</span></span>  

<span data-ttu-id="da500-188">Die folgende SSDL-Syntax zeigt die Deklaration eines **EntitySet** , gefolgt vom **DefiningQuery** -Element, das eine Abfrage enthält, die zum Abrufen der Sicht verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="da500-188">The following SSDL syntax shows the declaration of an **EntitySet** followed by the **DefiningQuery** element that contains a query used to retrieve the view.</span></span>

``` xml
 <Schema>
     <EntitySet Name="Tables" EntityType="Self.STable">
         <DefiningQuery>
           SELECT  TABLE_CATALOG,
                   'test' as TABLE_SCHEMA,
                   TABLE_NAME
           FROM    INFORMATION_SCHEMA.TABLES
         </DefiningQuery>
     </EntitySet>
 </Schema>
```

<span data-ttu-id="da500-189">Sie können gespeicherte Prozeduren in der Entity Framework verwenden, um Szenarien mit Lese-/Schreibzugriff zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="da500-189">You can use stored procedures in the Entity Framework to enable read-write scenarios over views.</span></span><span data-ttu-id="da500-190">Sie können entweder eine Datenquellen Sicht oder eine Entity SQL Sicht als Basistabelle zum Abrufen von Daten und zur Änderungs Verarbeitung durch gespeicherte Prozeduren verwenden.</span><span class="sxs-lookup"><span data-stu-id="da500-190"> You can use either a data source view or an Entity SQL view as the base table for retrieving data and for change processing by stored procedures.</span></span>

<span data-ttu-id="da500-191">Sie können das **DefiningQuery** -Element verwenden, um Microsoft SQL Server Compact 3,5-Ziel zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="da500-191">You can use the **DefiningQuery** element to target Microsoft SQL Server Compact 3.5.</span></span> <span data-ttu-id="da500-192">Obwohl SQL Server Compact 3,5 keine gespeicherten Prozeduren unterstützt, können Sie eine ähnliche Funktionalität mit dem **DefiningQuery** -Element implementieren.</span><span class="sxs-lookup"><span data-stu-id="da500-192">Though SQL Server Compact 3.5 does not support stored procedures, you can implement similar functionality with the **DefiningQuery** element.</span></span> <span data-ttu-id="da500-193">Es kann auch beim Erstellen gespeicherter Prozeduren nützlich sein, um einen Konflikt zwischen den in der Programmiersprache und den in der Datenquelle verwendeten Datentypen zu lösen.</span><span class="sxs-lookup"><span data-stu-id="da500-193">Another place where it can be useful is in creating stored procedures to overcome a mismatch between the data types used in the programming language and those of the data source.</span></span> <span data-ttu-id="da500-194">Sie könnten eine **DefiningQuery** schreiben, die einen bestimmten Satz von Parametern annimmt, und dann eine gespeicherte Prozedur mit einem anderen Satz von Parametern aufrufen, z. b. eine gespeicherte Prozedur, die Daten löscht.</span><span class="sxs-lookup"><span data-stu-id="da500-194">You could write a **DefiningQuery** that takes a certain set of parameters and then calls a stored procedure with a different set of parameters, for example, a stored procedure that deletes data.</span></span>

## <a name="dependent-element-ssdl"></a><span data-ttu-id="da500-195">Abhängiges Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-195">Dependent Element (SSDL)</span></span>

<span data-ttu-id="da500-196">Das **abhängige** Element in der Speicher Schema-Definitions Sprache (Store Schema Definition Language, SSDL) ist ein untergeordnetes Element des referentialeinschränkung-Elements, das das abhängige Ende einer FOREIGN KEY-Einschränkung definiert (auch referenzielle Einschränkung genannt).</span><span class="sxs-lookup"><span data-stu-id="da500-196">The **Dependent** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the dependent end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="da500-197">Das **abhängige** Element gibt die Spalte (oder Spalten) in einer Tabelle an, die auf eine Primärschlüssel Spalte (oder Spalten) verweisen.</span><span class="sxs-lookup"><span data-stu-id="da500-197">The **Dependent** element specifies the column (or columns) in a table that reference a primary key column (or columns).</span></span> <span data-ttu-id="da500-198">**PropertyRef** -Elemente geben an, auf welche Spalten verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="da500-198">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="da500-199">Das Principal-Element gibt die Primärschlüssel Spalten an, auf die von Spalten verwiesen wird, die im **abhängigen** Element angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="da500-199">The Principal element specifies the primary key columns that are referenced by columns that are specified in the **Dependent** element.</span></span>

<span data-ttu-id="da500-200">Das **abhängige** Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="da500-200">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="da500-201">PropertyRef (ein oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="da500-201">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="da500-202">Anmerkungselemente (kein (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="da500-202">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="da500-203">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-203">Applicable Attributes</span></span>

<span data-ttu-id="da500-204">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **abhängige** Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="da500-204">The following table describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="da500-205">Attributname</span><span class="sxs-lookup"><span data-stu-id="da500-205">Attribute Name</span></span> | <span data-ttu-id="da500-206">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="da500-206">Is Required</span></span> | <span data-ttu-id="da500-207">Wert</span><span class="sxs-lookup"><span data-stu-id="da500-207">Value</span></span>                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="da500-208">**Rolle**</span><span class="sxs-lookup"><span data-stu-id="da500-208">**Role**</span></span>       | <span data-ttu-id="da500-209">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-209">Yes</span></span>         | <span data-ttu-id="da500-210">Der gleiche Wert wie das **Role** -Attribut (sofern verwendet) des entsprechenden End-Elements. andernfalls der Name der Tabelle, die die verweisende Spalte enthält.</span><span class="sxs-lookup"><span data-stu-id="da500-210">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referencing column.</span></span> |

> [!NOTE]
> <span data-ttu-id="da500-211">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **abhängige** Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-211">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="da500-212">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-212">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="da500-213">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-213">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="da500-214">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-214">Example</span></span>

<span data-ttu-id="da500-215">Das folgende Beispiel zeigt ein Association-Element, das ein **referentialeinschränkungs** -Element verwendet, um die Spalten anzugeben, die an der FOREIGN KEY-Einschränkung von **FK \_ CustomerOrders** beteiligt sind.</span><span class="sxs-lookup"><span data-stu-id="da500-215">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="da500-216">Das **abhängige** Element gibt die **CustomerID-** Spalte der **Order** -Tabelle als abhängiges Ende der Einschränkung an.</span><span class="sxs-lookup"><span data-stu-id="da500-216">The **Dependent** element specifies the **CustomerId** column of the **Order** table as the dependent end of the constraint.</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="documentation-element-ssdl"></a><span data-ttu-id="da500-217">Documentation-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-217">Documentation Element (SSDL)</span></span>

<span data-ttu-id="da500-218">Das **Dokumentations** Element in der Datenspeicher Schema-Definitions Sprache (Store Schema Definition Language, SSDL) kann verwendet werden, um Informationen zu einem Objekt bereitzustellen, das in einem übergeordneten Element definiert</span><span class="sxs-lookup"><span data-stu-id="da500-218">The **Documentation** element in store schema definition language (SSDL) can be used to provide information about an object that is defined in a parent element.</span></span>

<span data-ttu-id="da500-219">Das **Documentation** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="da500-219">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="da500-220">**Zusammenfassung**: eine kurze Beschreibung des übergeordneten Elements.</span><span class="sxs-lookup"><span data-stu-id="da500-220">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="da500-221">(kein (Null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-221">(zero or one element)</span></span>
-   <span data-ttu-id="da500-222">**LongDescription**: eine umfassende Beschreibung des übergeordneten Elements.</span><span class="sxs-lookup"><span data-stu-id="da500-222">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="da500-223">(kein (Null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-223">(zero or one element)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="da500-224">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-224">Applicable Attributes</span></span>

<span data-ttu-id="da500-225">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Documentation** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-225">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="da500-226">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-226">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="da500-227">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-227">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="da500-228">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-228">Example</span></span>

<span data-ttu-id="da500-229">Das folgende Beispiel zeigt das **Documentation** -Element als untergeordnetes Element eines EntityType-Elements.</span><span class="sxs-lookup"><span data-stu-id="da500-229">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span>

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="end-element-ssdl"></a><span data-ttu-id="da500-230">End-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-230">End Element (SSDL)</span></span>

<span data-ttu-id="da500-231">Das **End** -Element in der Datenspeicher Schema-Definitions Sprache (Store Schema Definition Language, SSDL) gibt die Tabelle und die Anzahl der Zeilen an einem Ende einer Fremdschlüssel Einschränkung in der zugrunde liegenden Datenbank an.</span><span class="sxs-lookup"><span data-stu-id="da500-231">The **End** element in store schema definition language (SSDL) specifies the table and number of rows at one end of a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="da500-232">Das **End** -Element kann ein untergeordnetes Element des Association-Elements oder des AssociationSet-Elements sein.</span><span class="sxs-lookup"><span data-stu-id="da500-232">The **End** element can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="da500-233">In jedem dieser Fälle sind andere untergeordnete Elemente und anwendbare Attribute die möglich.</span><span class="sxs-lookup"><span data-stu-id="da500-233">In each case, the possible child elements and applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="da500-234">Das End-Element als untergeordnetes Objekt des Association-Elements</span><span class="sxs-lookup"><span data-stu-id="da500-234">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="da500-235">Ein **End** -Element (als untergeordnetes Element des **Association** -Elements) gibt die Tabelle und die Anzahl der Zeilen am Ende einer Foreign **Key-Einschränkung** mit dem **Type** -Attribut und dem Multiplizitätsattribut an.</span><span class="sxs-lookup"><span data-stu-id="da500-235">An **End** element (as a child of the **Association** element) specifies the table and number of rows at the end of a foreign key constraint with the **Type** and **Multiplicity** attributes respectively.</span></span> <span data-ttu-id="da500-236">Die Enden einer Fremdschlüsseleinschränkung sind als Teil einer SSDL-Zuordnung definiert. Eine SSDL-Zuordnung muss genau zwei Enden aufweisen.</span><span class="sxs-lookup"><span data-stu-id="da500-236">Ends of a foreign key constraint are defined as part of an SSDL association; an SSDL association must have exactly two ends.</span></span>

<span data-ttu-id="da500-237">Ein **Endelement** kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="da500-237">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="da500-238">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-238">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="da500-239">OnDelete (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-239">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="da500-240">Anmerkungselemente (kein Element (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="da500-240">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="da500-241">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-241">Applicable Attributes</span></span>

<span data-ttu-id="da500-242">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **End** -Element angewendet werden können, wenn es sich um das untergeordnete Element eines **Association** -Elements handelt.</span><span class="sxs-lookup"><span data-stu-id="da500-242">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="da500-243">Attributname</span><span class="sxs-lookup"><span data-stu-id="da500-243">Attribute Name</span></span>   | <span data-ttu-id="da500-244">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="da500-244">Is Required</span></span> | <span data-ttu-id="da500-245">Wert</span><span class="sxs-lookup"><span data-stu-id="da500-245">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="da500-246">**Typ**</span><span class="sxs-lookup"><span data-stu-id="da500-246">**Type**</span></span>         | <span data-ttu-id="da500-247">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-247">Yes</span></span>         | <span data-ttu-id="da500-248">Der vollqualifizierte Name der SSDL-Entitätenmenge, die sich am Ende der Fremdschlüsseleinschränkung befindet.</span><span class="sxs-lookup"><span data-stu-id="da500-248">The fully qualified name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                                                                                                                                                                                                                                                                          |
| <span data-ttu-id="da500-249">**Rolle**</span><span class="sxs-lookup"><span data-stu-id="da500-249">**Role**</span></span>         | <span data-ttu-id="da500-250">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-250">No</span></span>          | <span data-ttu-id="da500-251">Der Wert des **Role** -Attributs im Prinzipal-oder abhängigen Element des entsprechenden referentialeinschränkung-Elements (sofern verwendet).</span><span class="sxs-lookup"><span data-stu-id="da500-251">The value of the **Role** attribute in either the Principal or Dependent element of the corresponding ReferentialConstraint element (if used).</span></span>                                                                                                                                                                                                                                             |
| <span data-ttu-id="da500-252">**Multiplizität**</span><span class="sxs-lookup"><span data-stu-id="da500-252">**Multiplicity**</span></span> | <span data-ttu-id="da500-253">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-253">Yes</span></span>         | <span data-ttu-id="da500-254">**1**, **0.. 1**oder, **\*** abhängig von der Anzahl der Zeilen, die am Ende der FOREIGN KEY-Einschränkung liegen können.</span><span class="sxs-lookup"><span data-stu-id="da500-254">**1**, **0..1**, or **\*** depending on the number of rows that can be at the end of the foreign key constraint.</span></span> <br/> <span data-ttu-id="da500-255">der Wert **1** gibt an, dass genau eine Zeile am Ende der Fremdschlüssel Einschränkung vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="da500-255">**1** indicates that exactly one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="da500-256">**0.. 1** gibt an, dass keine oder eine Zeile am Ende der Fremdschlüssel Einschränkung vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="da500-256">**0..1** indicates that zero or one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="da500-257">**\*** Gibt an, dass keine, eine oder mehrere Zeilen am Ende der FOREIGN KEY-Einschränkung vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="da500-257">**\*** indicates that zero, one, or more rows exist at the foreign key constraint end.</span></span> |

> [!NOTE]
> <span data-ttu-id="da500-258">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **End** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-258">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="da500-259">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-259">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="da500-260">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-260">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="da500-261">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-261">Example</span></span>

<span data-ttu-id="da500-262">Das folgende Beispiel zeigt ein **Association** -Element, das die FOREIGN KEY-Einschränkung " **FK \_ CustomerOrders** " definiert.</span><span class="sxs-lookup"><span data-stu-id="da500-262">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="da500-263">Die **für** jedes **Endelement** angegebenen multiplizitätswerte geben an, dass viele Zeilen in der **Orders** -Tabelle einer Zeile in der **Customers** -Tabelle zugeordnet werden können, aber nur eine Zeile in der **Customers** -Tabelle kann einer Zeile in der **Orders** -Tabelle zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-263">The **Multiplicity** values specified on each **End** element indicate that many rows in the **Orders** table can be associated with a row in the **Customers** table, but only one row in the **Customers** table can be associated with a row in the **Orders** table.</span></span> <span data-ttu-id="da500-264">Außerdem gibt das **OnDelete** -Element an, dass alle Zeilen in der **Orders** -Tabelle, die auf eine bestimmte Zeile in der **Customers** -Tabelle verweisen, gelöscht werden, wenn die Zeile in der **Customers** -Tabelle gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="da500-264">Additionally, the **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="da500-265">Das End-Element als untergeordnetes Objekt des AssociationSet-Elements</span><span class="sxs-lookup"><span data-stu-id="da500-265">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="da500-266">Das **End** -Element (als untergeordnetes Element des **AssociationSet** -Elements) gibt eine Tabelle an einem Ende einer FOREIGN KEY-Einschränkung in der zugrunde liegenden Datenbank an.</span><span class="sxs-lookup"><span data-stu-id="da500-266">The **End** element (as a child of the **AssociationSet** element) specifies a table at one end of a foreign key constraint in the underlying database.</span></span>

<span data-ttu-id="da500-267">Ein **Endelement** kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="da500-267">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="da500-268">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-268">Documentation (zero or one)</span></span>
-   <span data-ttu-id="da500-269">Anmerkungselemente (kein (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="da500-269">Annotation elements (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="da500-270">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-270">Applicable Attributes</span></span>

<span data-ttu-id="da500-271">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **End** -Element angewendet werden können, wenn es sich um das untergeordnete Element eines **AssociationSet** -Elements handelt.</span><span class="sxs-lookup"><span data-stu-id="da500-271">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="da500-272">Attributname</span><span class="sxs-lookup"><span data-stu-id="da500-272">Attribute Name</span></span> | <span data-ttu-id="da500-273">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="da500-273">Is Required</span></span> | <span data-ttu-id="da500-274">Wert</span><span class="sxs-lookup"><span data-stu-id="da500-274">Value</span></span>                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="da500-275">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="da500-275">**EntitySet**</span></span>  | <span data-ttu-id="da500-276">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-276">Yes</span></span>         | <span data-ttu-id="da500-277">Der Name der SSDL-Entitätenmenge, die sich am Ende der Fremdschlüsseleinschränkung befindet.</span><span class="sxs-lookup"><span data-stu-id="da500-277">The name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                      |
| <span data-ttu-id="da500-278">**Rolle**</span><span class="sxs-lookup"><span data-stu-id="da500-278">**Role**</span></span>       | <span data-ttu-id="da500-279">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-279">No</span></span>          | <span data-ttu-id="da500-280">Der Wert eines der **Rollen** Attribute, der in einem **Endelement** des entsprechenden Association-Elements angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="da500-280">The value of one of the **Role** attributes specified on one **End** element of the corresponding Association element.</span></span> |

> [!NOTE]
> <span data-ttu-id="da500-281">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **End** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-281">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="da500-282">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-282">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="da500-283">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-283">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="da500-284">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-284">Example</span></span>

<span data-ttu-id="da500-285">Das folgende Beispiel zeigt ein **EntityContainer** -Element mit einem **AssociationSet** -Element mit zwei **Endelementen** :</span><span class="sxs-lookup"><span data-stu-id="da500-285">The following example shows an **EntityContainer** element with an **AssociationSet** element with two **End** elements:</span></span>

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entitycontainer-element-ssdl"></a><span data-ttu-id="da500-286">EntityContainer-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-286">EntityContainer Element (SSDL)</span></span>

<span data-ttu-id="da500-287">Ein **EntityContainer** -Element in der Datenspeicher Schema-Definitions Sprache (Store Schema Definition Language, SSDL) beschreibt die Struktur der zugrunde liegenden Datenquelle in einer Entity Framework Anwendung: SSDL-Entitätenmengen (definiert in EntitySet-Elementen) stellen Tabellen in einer Datenbank dar, SSDL-Entitäts Typen (definiert in EntityType-Elementen) stellen Zeilen in einer Tabelle dar, und Zuordnungs Sätze (die in AssociationSet-Elementen definiert sind) stellen Fremdschlüssel Einschränkungen in einer Datenbank dar.</span><span class="sxs-lookup"><span data-stu-id="da500-287">An **EntityContainer** element in store schema definition language (SSDL) describes the structure of the underlying data source in an Entity Framework application: SSDL entity sets (defined in EntitySet elements) represent tables in a database, SSDL entity types (defined in EntityType elements) represent rows in a table, and association sets (defined in AssociationSet elements) represent foreign key constraints in a database.</span></span> <span data-ttu-id="da500-288">Durch das EntityContainerMapping-Element wird einem Speichermodell-Entitätscontainer ein konzeptioneller Modellentitätscontainer zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="da500-288">A storage model entity container maps to a conceptual model entity container through the EntityContainerMapping element.</span></span>

<span data-ttu-id="da500-289">Ein **EntityContainer** -Element kann über 0 (null) oder ein Dokumentations Element verfügen.</span><span class="sxs-lookup"><span data-stu-id="da500-289">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="da500-290">Wenn ein **Documentation** -Element vorhanden ist, muss es allen anderen untergeordneten Elementen vorangestellt werden.</span><span class="sxs-lookup"><span data-stu-id="da500-290">If a **Documentation** element is present, it must precede all other child elements.</span></span>

<span data-ttu-id="da500-291">Ein **EntityContainer** -Element kann über 0 (null) oder mehrere der folgenden untergeordneten Elemente verfügen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="da500-291">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="da500-292">EntitySet</span><span class="sxs-lookup"><span data-stu-id="da500-292">EntitySet</span></span>
-   <span data-ttu-id="da500-293">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="da500-293">AssociationSet</span></span>
-   <span data-ttu-id="da500-294">Anmerkungselemente</span><span class="sxs-lookup"><span data-stu-id="da500-294">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="da500-295">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-295">Applicable Attributes</span></span>

<span data-ttu-id="da500-296">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **EntityContainer** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="da500-296">The table below describes the attributes that can be applied to the **EntityContainer** element.</span></span>

| <span data-ttu-id="da500-297">Attributname</span><span class="sxs-lookup"><span data-stu-id="da500-297">Attribute Name</span></span> | <span data-ttu-id="da500-298">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="da500-298">Is Required</span></span> | <span data-ttu-id="da500-299">Wert</span><span class="sxs-lookup"><span data-stu-id="da500-299">Value</span></span>                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| <span data-ttu-id="da500-300">**Name**</span><span class="sxs-lookup"><span data-stu-id="da500-300">**Name**</span></span>       | <span data-ttu-id="da500-301">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-301">Yes</span></span>         | <span data-ttu-id="da500-302">Der Name des Entitätscontainers.</span><span class="sxs-lookup"><span data-stu-id="da500-302">The name of the entity container.</span></span> <span data-ttu-id="da500-303">Dieser Name darf keine Punkte (.) enthalten.</span><span class="sxs-lookup"><span data-stu-id="da500-303">This name cannot contain periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="da500-304">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **EntityContainer** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-304">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="da500-305">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für SSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-305">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="da500-306">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-306">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="da500-307">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-307">Example</span></span>

<span data-ttu-id="da500-308">Das folgende Beispiel zeigt ein **EntityContainer** -Element, das zwei Entitätenmengen und einen Zuordnungs Satz definiert.</span><span class="sxs-lookup"><span data-stu-id="da500-308">The following example shows an **EntityContainer** element that defines two entity sets and one association set.</span></span> <span data-ttu-id="da500-309">Beachten Sie, dass die Namen des Entitätstyps und des Zuordnungstyps mit dem Namespace des konzeptionellen Modellnamens qualifiziert werden.</span><span class="sxs-lookup"><span data-stu-id="da500-309">Note that entity type and association type names are qualified by the conceptual model namespace name.</span></span>

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entityset-element-ssdl"></a><span data-ttu-id="da500-310">EntitySet-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-310">EntitySet Element (SSDL)</span></span>

<span data-ttu-id="da500-311">Ein **EntitySet** -Element in der Datenspeicher Schema-Definitions Sprache (SSDL) stellt eine Tabelle oder Sicht in der zugrunde liegenden Datenbank dar.</span><span class="sxs-lookup"><span data-stu-id="da500-311">An **EntitySet** element in store schema definition language (SSDL) represents a table or view in the underlying database.</span></span> <span data-ttu-id="da500-312">Ein EntityType-Element in SSDL stellt eine Zeile in der Tabelle oder der Ansicht dar.</span><span class="sxs-lookup"><span data-stu-id="da500-312">An EntityType element in SSDL represents a row in the table or view.</span></span> <span data-ttu-id="da500-313">Das **EntityType** -Attribut eines **EntitySet** -Elements gibt den spezifischen SSDL-Entitätstyp an, der Zeilen in einer SSDL-Entitätenmenge darstellt.</span><span class="sxs-lookup"><span data-stu-id="da500-313">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="da500-314">Die Zuordnung einer CSDL-Entitätenmenge zu einer SSDL-Entitätenmenge wird in einem EntitySetMapping-Element angegeben.</span><span class="sxs-lookup"><span data-stu-id="da500-314">The mapping between a CSDL entity set and an SSDL entity set is specified in an EntitySetMapping element.</span></span>

<span data-ttu-id="da500-315">Das **EntitySet** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="da500-315">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="da500-316">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-316">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="da500-317">DefiningQuery (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-317">DefiningQuery (zero or one element)</span></span>
-   <span data-ttu-id="da500-318">Anmerkungselemente</span><span class="sxs-lookup"><span data-stu-id="da500-318">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="da500-319">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-319">Applicable Attributes</span></span>

<span data-ttu-id="da500-320">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **EntitySet** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="da500-320">The following table describes the attributes that can be applied to the **EntitySet** element.</span></span>

> [!NOTE]
> <span data-ttu-id="da500-321">Einige (hier nicht aufgelistete) Attribute können mit dem **Store** -Alias qualifiziert werden.</span><span class="sxs-lookup"><span data-stu-id="da500-321">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="da500-322">Diese Attribute werden vom Modellaktualisierungs-Assistenten beim Aktualisieren eines Modells verwendet.</span><span class="sxs-lookup"><span data-stu-id="da500-322">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="da500-323">Attributname</span><span class="sxs-lookup"><span data-stu-id="da500-323">Attribute Name</span></span> | <span data-ttu-id="da500-324">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="da500-324">Is Required</span></span> | <span data-ttu-id="da500-325">Wert</span><span class="sxs-lookup"><span data-stu-id="da500-325">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="da500-326">**Name**</span><span class="sxs-lookup"><span data-stu-id="da500-326">**Name**</span></span>       | <span data-ttu-id="da500-327">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-327">Yes</span></span>         | <span data-ttu-id="da500-328">Der Name des Entitätssatzes.</span><span class="sxs-lookup"><span data-stu-id="da500-328">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="da500-329">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="da500-329">**EntityType**</span></span> | <span data-ttu-id="da500-330">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-330">Yes</span></span>         | <span data-ttu-id="da500-331">Der vollqualifizierte Name des Entitätstyps, für den der Entitätssatz Instanzen enthält.</span><span class="sxs-lookup"><span data-stu-id="da500-331">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |
| <span data-ttu-id="da500-332">**Schema**</span><span class="sxs-lookup"><span data-stu-id="da500-332">**Schema**</span></span>     | <span data-ttu-id="da500-333">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-333">No</span></span>          | <span data-ttu-id="da500-334">Das Datenbankschema.</span><span class="sxs-lookup"><span data-stu-id="da500-334">The database schema.</span></span>                                                                     |
| <span data-ttu-id="da500-335">**Table**</span><span class="sxs-lookup"><span data-stu-id="da500-335">**Table**</span></span>      | <span data-ttu-id="da500-336">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-336">No</span></span>          | <span data-ttu-id="da500-337">Die Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="da500-337">The database table.</span></span>                                                                      |

> [!NOTE]
> <span data-ttu-id="da500-338">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **EntitySet** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-338">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="da500-339">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für SSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-339">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="da500-340">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-340">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="da500-341">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-341">Example</span></span>

<span data-ttu-id="da500-342">Das folgende Beispiel zeigt ein **EntityContainer** -Element, das über zwei **EntitySet** -Elemente und ein **AssociationSet** -Element verfügt:</span><span class="sxs-lookup"><span data-stu-id="da500-342">The following example shows an **EntityContainer** element that has two **EntitySet** elements and one **AssociationSet** element:</span></span>

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entitytype-element-ssdl"></a><span data-ttu-id="da500-343">EntityType-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-343">EntityType Element (SSDL)</span></span>

<span data-ttu-id="da500-344">Ein **EntityType** -Element in der Datenspeicher Schema-Definitions Sprache (SSDL) stellt eine Zeile in einer Tabelle oder Sicht der zugrunde liegenden Datenbank dar.</span><span class="sxs-lookup"><span data-stu-id="da500-344">An **EntityType** element in store schema definition language (SSDL) represents a row in a table or view of the underlying database.</span></span> <span data-ttu-id="da500-345">Ein EntitySet-Element in SSDL stellt die Tabelle oder Ansicht dar, in der Zeilen enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="da500-345">An EntitySet element in SSDL represents the table or view in which rows occur.</span></span> <span data-ttu-id="da500-346">Das **EntityType** -Attribut eines **EntitySet** -Elements gibt den spezifischen SSDL-Entitätstyp an, der Zeilen in einer SSDL-Entitätenmenge darstellt.</span><span class="sxs-lookup"><span data-stu-id="da500-346">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="da500-347">Die Zuordnung eines SSDL-Entitätstyps zu einem CSDL-Entitätstyp wird in einem EntityTypeMapping-Element angegeben.</span><span class="sxs-lookup"><span data-stu-id="da500-347">The mapping between an SSDL entity type and a CSDL entity type is specified in an EntityTypeMapping element.</span></span>

<span data-ttu-id="da500-348">Das **EntityType** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="da500-348">The **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="da500-349">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-349">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="da500-350">Key (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-350">Key (zero or one element)</span></span>
-   <span data-ttu-id="da500-351">Anmerkungselemente</span><span class="sxs-lookup"><span data-stu-id="da500-351">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="da500-352">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-352">Applicable Attributes</span></span>

<span data-ttu-id="da500-353">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **EntityType** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="da500-353">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="da500-354">Attributname</span><span class="sxs-lookup"><span data-stu-id="da500-354">Attribute Name</span></span> | <span data-ttu-id="da500-355">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="da500-355">Is Required</span></span> | <span data-ttu-id="da500-356">Wert</span><span class="sxs-lookup"><span data-stu-id="da500-356">Value</span></span>                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="da500-357">**Name**</span><span class="sxs-lookup"><span data-stu-id="da500-357">**Name**</span></span>       | <span data-ttu-id="da500-358">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-358">Yes</span></span>         | <span data-ttu-id="da500-359">Der Name des Entitätstyps.</span><span class="sxs-lookup"><span data-stu-id="da500-359">The name of the entity type.</span></span> <span data-ttu-id="da500-360">Dieser Wert ist normalerweise der gleiche wie der Name der Tabelle, in der der Entitätstyp eine Zeile darstellt.</span><span class="sxs-lookup"><span data-stu-id="da500-360">This value is usually the same as the name of the table in which the entity type represents a row.</span></span> <span data-ttu-id="da500-361">Dieser Wert darf keine Punkte (.) enthalten.</span><span class="sxs-lookup"><span data-stu-id="da500-361">This value can contain no periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="da500-362">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **EntityType** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-362">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="da500-363">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für SSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-363">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="da500-364">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-364">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="da500-365">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-365">Example</span></span>

<span data-ttu-id="da500-366">Das folgende Beispiel zeigt ein **EntityType** -Element mit zwei Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="da500-366">The following example shows an **EntityType** element with two properties:</span></span>

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="function-element-ssdl"></a><span data-ttu-id="da500-367">Function-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-367">Function Element (SSDL)</span></span>

<span data-ttu-id="da500-368">Das **Function** -Element in der Datenspeicher Schema-Definitions Sprache (SSDL) gibt eine gespeicherte Prozedur an, die in der zugrunde liegenden Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="da500-368">The **Function** element in store schema definition language (SSDL) specifies a stored procedure that exists in the underlying database.</span></span>

<span data-ttu-id="da500-369">Das **Function** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="da500-369">The **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="da500-370">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-370">Documentation (zero or one)</span></span>
-   <span data-ttu-id="da500-371">Parameter (0 (null) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="da500-371">Parameter (zero or more)</span></span>
-   <span data-ttu-id="da500-372">CommandText (0 (null) oder 1)</span><span class="sxs-lookup"><span data-stu-id="da500-372">CommandText (zero or one)</span></span>
-   <span data-ttu-id="da500-373">ReturnType (0 (null) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="da500-373">ReturnType (zero or more)</span></span>
-   <span data-ttu-id="da500-374">Anmerkungselemente (kein (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="da500-374">Annotation elements (zero or more)</span></span>

<span data-ttu-id="da500-375">Ein Rückgabetyp für eine Funktion muss entweder mit dem **returnType** -Element oder dem **returnType** -Attribut angegeben werden (siehe unten), aber nicht mit beiden.</span><span class="sxs-lookup"><span data-stu-id="da500-375">A return type for a function must be specified with either the **ReturnType** element or the **ReturnType** attribute (see below), but not both.</span></span>

<span data-ttu-id="da500-376">Gespeicherte Prozeduren, die im Speichermodell angegeben sind, können in das konzeptionelle Modell einer Anwendung importiert werden.</span><span class="sxs-lookup"><span data-stu-id="da500-376">Stored procedures that are specified in the storage model can be imported into the conceptual model of an application.</span></span> <span data-ttu-id="da500-377">Weitere Informationen finden Sie unter [Abfragen mit gespeicherten Prozeduren](xref:ef6/modeling/designer/stored-procedures/query).</span><span class="sxs-lookup"><span data-stu-id="da500-377">For more information, see [Querying with Stored Procedures](xref:ef6/modeling/designer/stored-procedures/query).</span></span> <span data-ttu-id="da500-378">Das **Function** -Element kann auch verwendet werden, um benutzerdefinierte Funktionen im Speichermodell zu definieren.</span><span class="sxs-lookup"><span data-stu-id="da500-378">The **Function** element can also be used to define custom functions in the storage model.</span></span>  

### <a name="applicable-attributes"></a><span data-ttu-id="da500-379">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-379">Applicable Attributes</span></span>

<span data-ttu-id="da500-380">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Function** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="da500-380">The following table describes the attributes that can be applied to the **Function** element.</span></span>

> [!NOTE]
> <span data-ttu-id="da500-381">Einige (hier nicht aufgelistete) Attribute können mit dem **Store** -Alias qualifiziert werden.</span><span class="sxs-lookup"><span data-stu-id="da500-381">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="da500-382">Diese Attribute werden vom Modellaktualisierungs-Assistenten beim Aktualisieren eines Modells verwendet.</span><span class="sxs-lookup"><span data-stu-id="da500-382">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="da500-383">Attributname</span><span class="sxs-lookup"><span data-stu-id="da500-383">Attribute Name</span></span>             | <span data-ttu-id="da500-384">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="da500-384">Is Required</span></span> | <span data-ttu-id="da500-385">Wert</span><span class="sxs-lookup"><span data-stu-id="da500-385">Value</span></span>                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="da500-386">**Name**</span><span class="sxs-lookup"><span data-stu-id="da500-386">**Name**</span></span>                   | <span data-ttu-id="da500-387">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-387">Yes</span></span>         | <span data-ttu-id="da500-388">Name der gespeicherten Prozedur</span><span class="sxs-lookup"><span data-stu-id="da500-388">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="da500-389">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="da500-389">**ReturnType**</span></span>             | <span data-ttu-id="da500-390">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-390">No</span></span>          | <span data-ttu-id="da500-391">Der Rückgabetyp der gespeicherten Prozedur.</span><span class="sxs-lookup"><span data-stu-id="da500-391">The return type of the stored procedure.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="da500-392">**Aggregat**</span><span class="sxs-lookup"><span data-stu-id="da500-392">**Aggregate**</span></span>              | <span data-ttu-id="da500-393">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-393">No</span></span>          | <span data-ttu-id="da500-394">**True** , wenn die gespeicherte Prozedur einen Aggregatwert zurückgibt. andernfalls **false**.</span><span class="sxs-lookup"><span data-stu-id="da500-394">**True** if the stored procedure returns an aggregate value; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="da500-395">**Builtin**</span><span class="sxs-lookup"><span data-stu-id="da500-395">**BuiltIn**</span></span>                | <span data-ttu-id="da500-396">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-396">No</span></span>          | <span data-ttu-id="da500-397">**True** , wenn es sich bei der Funktion um eine integrierte<sup>1</sup> -Funktion handelt. andernfalls **false**.</span><span class="sxs-lookup"><span data-stu-id="da500-397">**True** if the function is a built-in<sup>1</sup> function; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="da500-398">**StoreFunctionName**</span><span class="sxs-lookup"><span data-stu-id="da500-398">**StoreFunctionName**</span></span>      | <span data-ttu-id="da500-399">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-399">No</span></span>          | <span data-ttu-id="da500-400">Name der gespeicherten Prozedur</span><span class="sxs-lookup"><span data-stu-id="da500-400">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="da500-401">**NiladicFunction**</span><span class="sxs-lookup"><span data-stu-id="da500-401">**NiladicFunction**</span></span>        | <span data-ttu-id="da500-402">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-402">No</span></span>          | <span data-ttu-id="da500-403">**True** , wenn es sich bei der Funktion um eine NILADIC<sup>2</sup> -Funktion handelt. Andernfalls **false** .</span><span class="sxs-lookup"><span data-stu-id="da500-403">**True** if the function is a niladic<sup>2</sup> function; **False** otherwise.</span></span>                                                                                                                                   |
| <span data-ttu-id="da500-404">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="da500-404">**IsComposable**</span></span>           | <span data-ttu-id="da500-405">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-405">No</span></span>          | <span data-ttu-id="da500-406">**True** , wenn die Funktion eine Zusammensetz Bare<sup>3</sup> -Funktion ist. Andernfalls **false** .</span><span class="sxs-lookup"><span data-stu-id="da500-406">**True** if the function is a composable<sup>3</sup> function; **False** otherwise.</span></span>                                                                                                                                |
| <span data-ttu-id="da500-407">**Das**</span><span class="sxs-lookup"><span data-stu-id="da500-407">**ParameterTypeSemantics**</span></span> | <span data-ttu-id="da500-408">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-408">No</span></span>          | <span data-ttu-id="da500-409">Die Enumeration, die die Typsemantik definiert, die zum Auflösen von Funktionsüberladungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="da500-409">The enumeration that defines the type semantics used to resolve function overloads.</span></span> <span data-ttu-id="da500-410">Die Enumeration ist im Anbietermanifest für jede Funktionsdefinition definiert.</span><span class="sxs-lookup"><span data-stu-id="da500-410">The enumeration is defined in the provider manifest per function definition.</span></span> <span data-ttu-id="da500-411">Der Standardwert ist " **zuzubemplicitconversion**".</span><span class="sxs-lookup"><span data-stu-id="da500-411">The default value is **AllowImplicitConversion**.</span></span> |
| <span data-ttu-id="da500-412">**Schema**</span><span class="sxs-lookup"><span data-stu-id="da500-412">**Schema**</span></span>                 | <span data-ttu-id="da500-413">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-413">No</span></span>          | <span data-ttu-id="da500-414">Der Name des Schemas, in dem die gespeicherte Prozedur definiert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-414">The name of the schema in which the stored procedure is defined.</span></span>                                                                                                                                                   |

<span data-ttu-id="da500-415"><sup>1</sup> eine integrierte Funktion ist eine Funktion, die in der Datenbank definiert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-415"><sup>1</sup> A built-in function is a function that is defined in the database.</span></span> <span data-ttu-id="da500-416">Informationen zu Funktionen, die im Speichermodell definiert sind, finden Sie unter CommandText-Element (SSDL).</span><span class="sxs-lookup"><span data-stu-id="da500-416">For information about functions that are defined in the storage model, see CommandText Element (SSDL).</span></span>

<span data-ttu-id="da500-417"><sup>2</sup> eine NILADIC-Funktion ist eine Funktion, die keine Parameter akzeptiert und, wenn Sie aufgerufen wird, keine Klammern erfordert.</span><span class="sxs-lookup"><span data-stu-id="da500-417"><sup>2</sup> A niladic function is a function that accepts no parameters and, when called, does not require parentheses.</span></span>

<span data-ttu-id="da500-418"><sup>3</sup> zwei Funktionen sind zusammensetzbar, wenn die Ausgabe einer Funktion als Eingabe für die andere Funktion dienen kann.</span><span class="sxs-lookup"><span data-stu-id="da500-418"><sup>3</sup> Two functions are composable if the output of one function can be the input for the other function.</span></span>

> [!NOTE]
> <span data-ttu-id="da500-419">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Function** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-419">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="da500-420">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für SSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-420">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="da500-421">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-421">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="da500-422">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-422">Example</span></span>

<span data-ttu-id="da500-423">Das folgende Beispiel zeigt ein **Function** -Element, das der gespeicherten Prozedur **updateordermenge** entspricht.</span><span class="sxs-lookup"><span data-stu-id="da500-423">The following example shows a **Function** element that corresponds to the **UpdateOrderQuantity** stored procedure.</span></span> <span data-ttu-id="da500-424">Die gespeicherte Prozedur akzeptiert zwei Parameter und gibt keinen Wert zurück.</span><span class="sxs-lookup"><span data-stu-id="da500-424">The stored procedure accepts two parameters and does not return a value.</span></span>

``` xml
 <Function Name="UpdateOrderQuantity"
           Aggregate="false"
           BuiltIn="false"
           NiladicFunction="false"
           IsComposable="false"
           ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="orderId" Type="int" Mode="In" />
   <Parameter Name="newQuantity" Type="int" Mode="In" />
 </Function>
```

## <a name="key-element-ssdl"></a><span data-ttu-id="da500-425">Key-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-425">Key Element (SSDL)</span></span>

<span data-ttu-id="da500-426">Das **Schlüssel** Element in der Datenspeicher Schema-Definitions Sprache (Store Schema Definition Language, SSDL) stellt den Primärschlüssel einer Tabelle in der zugrunde liegenden Datenbank dar.</span><span class="sxs-lookup"><span data-stu-id="da500-426">The **Key** element in store schema definition language (SSDL) represents the primary key of a table in the underlying database.</span></span> <span data-ttu-id="da500-427">**Key** ist ein untergeordnetes Element eines EntityType-Elements, das eine Zeile in einer Tabelle darstellt.</span><span class="sxs-lookup"><span data-stu-id="da500-427">**Key** is a child element of an EntityType element, which represents a row in a table.</span></span> <span data-ttu-id="da500-428">Der Primärschlüssel wird im **Key** -Element definiert, indem auf ein oder mehrere Eigenschafts Elemente verwiesen wird, die für das **EntityType** -Element definiert sind.</span><span class="sxs-lookup"><span data-stu-id="da500-428">The primary key is defined in the **Key** element by referencing one or more Property elements that are defined on the **EntityType** element.</span></span>

<span data-ttu-id="da500-429">Das **Key** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="da500-429">The **Key** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="da500-430">PropertyRef (ein oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="da500-430">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="da500-431">Anmerkungselemente</span><span class="sxs-lookup"><span data-stu-id="da500-431">Annotation elements</span></span>

<span data-ttu-id="da500-432">Für das **Key** -Element sind keine Attribute anwendbar.</span><span class="sxs-lookup"><span data-stu-id="da500-432">No attributes are applicable to the **Key** element.</span></span>

### <a name="example"></a><span data-ttu-id="da500-433">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-433">Example</span></span>

<span data-ttu-id="da500-434">Das folgende Beispiel zeigt ein **EntityType** -Element mit einem Schlüssel, der auf eine Eigenschaft verweist:</span><span class="sxs-lookup"><span data-stu-id="da500-434">The following example shows an **EntityType** element with a key that references one property:</span></span>

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="ondelete-element-ssdl"></a><span data-ttu-id="da500-435">OnDelete-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-435">OnDelete Element (SSDL)</span></span>

<span data-ttu-id="da500-436">Das **OnDelete** -Element in der Datenspeicher Schema-Definitions Sprache (Store Schema Definition Language, SSDL) spiegelt das Daten Bank Verhalten wider, wenn eine Zeile, die Teil einer FOREIGN KEY-Einschränkung</span><span class="sxs-lookup"><span data-stu-id="da500-436">The **OnDelete** element in store schema definition language (SSDL) reflects the database behavior when a row that participates in a foreign key constraint is deleted.</span></span> <span data-ttu-id="da500-437">Wenn die Aktion auf **Cascade**festgelegt ist, werden Zeilen, die auf eine Zeile verweisen, die gelöscht wird, ebenfalls gelöscht.</span><span class="sxs-lookup"><span data-stu-id="da500-437">If the action is set to **Cascade**, then rows that reference a row that is being deleted will also be deleted.</span></span> <span data-ttu-id="da500-438">Wenn die Aktion auf **None**festgelegt ist, werden Zeilen, die auf eine Zeile verweisen, die gelöscht wird, nicht ebenfalls gelöscht.</span><span class="sxs-lookup"><span data-stu-id="da500-438">If the action is set to **None**, then rows that reference a row that is being deleted are not also deleted.</span></span> <span data-ttu-id="da500-439">Ein **OnDelete** -Element ist ein untergeordnetes Element eines End-Elements.</span><span class="sxs-lookup"><span data-stu-id="da500-439">An **OnDelete** element is a child element of an End element.</span></span>

<span data-ttu-id="da500-440">Ein **OnDelete** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="da500-440">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="da500-441">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-441">Documentation (zero or one)</span></span>
-   <span data-ttu-id="da500-442">Anmerkungselemente (kein (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="da500-442">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="da500-443">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-443">Applicable Attributes</span></span>

<span data-ttu-id="da500-444">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **OnDelete** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="da500-444">The following table describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="da500-445">Attributname</span><span class="sxs-lookup"><span data-stu-id="da500-445">Attribute Name</span></span> | <span data-ttu-id="da500-446">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="da500-446">Is Required</span></span> | <span data-ttu-id="da500-447">Wert</span><span class="sxs-lookup"><span data-stu-id="da500-447">Value</span></span>                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="da500-448">**Aktion**</span><span class="sxs-lookup"><span data-stu-id="da500-448">**Action**</span></span>     | <span data-ttu-id="da500-449">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-449">Yes</span></span>         | <span data-ttu-id="da500-450">**Cascade** oder **None**.</span><span class="sxs-lookup"><span data-stu-id="da500-450">**Cascade** or **None**.</span></span> <span data-ttu-id="da500-451">(Der Wert " **restricted** " ist gültig, hat jedoch das gleiche Verhalten wie " **None**".)</span><span class="sxs-lookup"><span data-stu-id="da500-451">(The value **Restricted** is valid but has the same behavior as **None**.)</span></span> |

> [!NOTE]
> <span data-ttu-id="da500-452">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **OnDelete** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-452">Any number of annotation attributes (custom XML attributes) may be applied to the **OnDelete** element.</span></span> <span data-ttu-id="da500-453">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für SSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-453">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="da500-454">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-454">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="da500-455">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-455">Example</span></span>

<span data-ttu-id="da500-456">Das folgende Beispiel zeigt ein **Association** -Element, das die FOREIGN KEY-Einschränkung " **FK \_ CustomerOrders** " definiert.</span><span class="sxs-lookup"><span data-stu-id="da500-456">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="da500-457">Das **OnDelete** -Element gibt an, dass alle Zeilen in der **Orders** -Tabelle, die auf eine bestimmte Zeile in der **Customers** -Tabelle verweisen, gelöscht werden, wenn die Zeile in der **Customers** -Tabelle gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="da500-457">The **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="parameter-element-ssdl"></a><span data-ttu-id="da500-458">Parameter-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-458">Parameter Element (SSDL)</span></span>

<span data-ttu-id="da500-459">Das **Parameter** -Element in der Datenspeicher Schema-Definitions Sprache (SSDL) ist ein untergeordnetes Element des Function-Elements, das Parameter für eine gespeicherte Prozedur in der Datenbank angibt.</span><span class="sxs-lookup"><span data-stu-id="da500-459">The **Parameter** element in store schema definition language (SSDL) is a child of the Function element that specifies parameters for a stored procedure in the database.</span></span>

<span data-ttu-id="da500-460">Das **Parameter** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="da500-460">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="da500-461">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-461">Documentation (zero or one)</span></span>
-   <span data-ttu-id="da500-462">Anmerkungselemente (kein (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="da500-462">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="da500-463">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-463">Applicable Attributes</span></span>

<span data-ttu-id="da500-464">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Parameter** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="da500-464">The table below describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="da500-465">Attributname</span><span class="sxs-lookup"><span data-stu-id="da500-465">Attribute Name</span></span> | <span data-ttu-id="da500-466">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="da500-466">Is Required</span></span> | <span data-ttu-id="da500-467">Wert</span><span class="sxs-lookup"><span data-stu-id="da500-467">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="da500-468">**Name**</span><span class="sxs-lookup"><span data-stu-id="da500-468">**Name**</span></span>       | <span data-ttu-id="da500-469">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-469">Yes</span></span>         | <span data-ttu-id="da500-470">Der Name des Parameters.</span><span class="sxs-lookup"><span data-stu-id="da500-470">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="da500-471">**Typ**</span><span class="sxs-lookup"><span data-stu-id="da500-471">**Type**</span></span>       | <span data-ttu-id="da500-472">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-472">Yes</span></span>         | <span data-ttu-id="da500-473">Der Parametertyp.</span><span class="sxs-lookup"><span data-stu-id="da500-473">The parameter type.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="da500-474">**Mode**</span><span class="sxs-lookup"><span data-stu-id="da500-474">**Mode**</span></span>       | <span data-ttu-id="da500-475">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-475">No</span></span>          | <span data-ttu-id="da500-476">**In**, out oder **INOUT** , je nachdem, ob der Parameter ein Eingabe-, Ausgabe-oder Eingabe- **/Ausgabeparameter**ist.</span><span class="sxs-lookup"><span data-stu-id="da500-476">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="da500-477">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="da500-477">**MaxLength**</span></span>  | <span data-ttu-id="da500-478">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-478">No</span></span>          | <span data-ttu-id="da500-479">Die maximale Länge des Parameters.</span><span class="sxs-lookup"><span data-stu-id="da500-479">The maximum length of the parameter.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="da500-480">**Genauigkeit**</span><span class="sxs-lookup"><span data-stu-id="da500-480">**Precision**</span></span>  | <span data-ttu-id="da500-481">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-481">No</span></span>          | <span data-ttu-id="da500-482">Die Genauigkeit des Parameters.</span><span class="sxs-lookup"><span data-stu-id="da500-482">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="da500-483">**Skalierung**</span><span class="sxs-lookup"><span data-stu-id="da500-483">**Scale**</span></span>      | <span data-ttu-id="da500-484">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-484">No</span></span>          | <span data-ttu-id="da500-485">Der Maßstab des Parameters.</span><span class="sxs-lookup"><span data-stu-id="da500-485">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="da500-486">**SRID**</span><span class="sxs-lookup"><span data-stu-id="da500-486">**SRID**</span></span>       | <span data-ttu-id="da500-487">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-487">No</span></span>          | <span data-ttu-id="da500-488">Verweis Bezeichner für räumliche Systeme.</span><span class="sxs-lookup"><span data-stu-id="da500-488">Spatial System Reference Identifier.</span></span> <span data-ttu-id="da500-489">Nur für Parameter räumlicher Typen gültig.</span><span class="sxs-lookup"><span data-stu-id="da500-489">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="da500-490">Weitere Informationen finden Sie unter [SRID](https://en.wikipedia.org/wiki/SRID) und [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="da500-490">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

> [!NOTE]
> <span data-ttu-id="da500-491">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Parameter** Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-491">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="da500-492">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für SSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-492">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="da500-493">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-493">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="da500-494">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-494">Example</span></span>

<span data-ttu-id="da500-495">Das folgende Beispiel zeigt ein **Function** -Element, das über zwei **Parameter** Elemente verfügt, die Eingabeparameter angeben:</span><span class="sxs-lookup"><span data-stu-id="da500-495">The following example shows a **Function** element that has two **Parameter** elements that specify input parameters:</span></span>

``` xml
 <Function Name="UpdateOrderQuantity"
           Aggregate="false"
           BuiltIn="false"
           NiladicFunction="false"
           IsComposable="false"
           ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="orderId" Type="int" Mode="In" />
   <Parameter Name="newQuantity" Type="int" Mode="In" />
 </Function>
```

## <a name="principal-element-ssdl"></a><span data-ttu-id="da500-496">Prinzipalelement (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-496">Principal Element (SSDL)</span></span>

<span data-ttu-id="da500-497">Das **Principal** -Element in der Speicher Schema-Definitions Sprache (Store Schema Definition Language, SSDL) ist ein untergeordnetes Element des referentialeinschränkung-Elements, das das Prinzipal Ende einer FOREIGN KEY-Einschränkung definiert (auch referenzielle Einschränkung genannt).</span><span class="sxs-lookup"><span data-stu-id="da500-497">The **Principal** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the principal end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="da500-498">Das **Principal** -Element gibt die Primärschlüssel Spalte (oder Spalten) in einer Tabelle an, auf die von einer anderen Spalte (oder Spalten) verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="da500-498">The **Principal** element specifies the primary key column (or columns) in a table that is referenced by another column (or columns).</span></span> <span data-ttu-id="da500-499">**PropertyRef** -Elemente geben an, auf welche Spalten verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="da500-499">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="da500-500">Das abhängige Element gibt die Spalten an, die auf die Primärschlüssel Spalten verweisen, die im **Principal** -Element angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="da500-500">The Dependent element specifies columns that reference the primary key columns that are specified in the **Principal** element.</span></span>

<span data-ttu-id="da500-501">Das **Principal** -Element kann über die folgenden untergeordneten Elemente verfügen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="da500-501">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="da500-502">PropertyRef (ein oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="da500-502">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="da500-503">Anmerkungselemente (kein (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="da500-503">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="da500-504">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-504">Applicable Attributes</span></span>

<span data-ttu-id="da500-505">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Principal** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="da500-505">The following table describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="da500-506">Attributname</span><span class="sxs-lookup"><span data-stu-id="da500-506">Attribute Name</span></span> | <span data-ttu-id="da500-507">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="da500-507">Is Required</span></span> | <span data-ttu-id="da500-508">Wert</span><span class="sxs-lookup"><span data-stu-id="da500-508">Value</span></span>                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="da500-509">**Rolle**</span><span class="sxs-lookup"><span data-stu-id="da500-509">**Role**</span></span>       | <span data-ttu-id="da500-510">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-510">Yes</span></span>         | <span data-ttu-id="da500-511">Der gleiche Wert wie das **Role** -Attribut (sofern verwendet) des entsprechenden End-Elements. andernfalls der Name der Tabelle, die die Spalte enthält, auf die verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="da500-511">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referenced column.</span></span> |

> [!NOTE]
> <span data-ttu-id="da500-512">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Principal** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-512">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="da500-513">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-513">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="da500-514">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-514">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="da500-515">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-515">Example</span></span>

<span data-ttu-id="da500-516">Das folgende Beispiel zeigt ein Association-Element, das ein **referentialeinschränkungs** -Element verwendet, um die Spalten anzugeben, die an der FOREIGN KEY-Einschränkung von **FK \_ CustomerOrders** beteiligt sind.</span><span class="sxs-lookup"><span data-stu-id="da500-516">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="da500-517">Das **Principal** -Element gibt die **CustomerID-** Spalte der **Customer** -Tabelle als Prinzipal Ende der Einschränkung an.</span><span class="sxs-lookup"><span data-stu-id="da500-517">The **Principal** element specifies the **CustomerId** column of the **Customer** table as the principal end of the constraint.</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="property-element-ssdl"></a><span data-ttu-id="da500-518">Property-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-518">Property Element (SSDL)</span></span>

<span data-ttu-id="da500-519">Das **Property** -Element in der Datenspeicher Schema-Definitions Sprache (SSDL) stellt eine Spalte in einer Tabelle in der zugrunde liegenden Datenbank dar.</span><span class="sxs-lookup"><span data-stu-id="da500-519">The **Property** element in store schema definition language (SSDL) represents a column in a table in the underlying database.</span></span> <span data-ttu-id="da500-520">**Eigenschaften** Elemente sind untergeordnete Elemente von EntityType-Elementen, die Zeilen in einer Tabelle darstellen.</span><span class="sxs-lookup"><span data-stu-id="da500-520">**Property** elements are children of EntityType elements, which represent rows in a table.</span></span> <span data-ttu-id="da500-521">Jedes für ein **EntityType** -Element definierte **Eigenschafts** Element stellt eine Spalte dar.</span><span class="sxs-lookup"><span data-stu-id="da500-521">Each **Property** element defined on an **EntityType** element represents a column.</span></span>

<span data-ttu-id="da500-522">Ein **Property** -Element kann keine untergeordneten Elemente aufweisen.</span><span class="sxs-lookup"><span data-stu-id="da500-522">A **Property** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="da500-523">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-523">Applicable Attributes</span></span>

<span data-ttu-id="da500-524">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Property** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="da500-524">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="da500-525">Attributname</span><span class="sxs-lookup"><span data-stu-id="da500-525">Attribute Name</span></span>            | <span data-ttu-id="da500-526">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="da500-526">Is Required</span></span> | <span data-ttu-id="da500-527">Wert</span><span class="sxs-lookup"><span data-stu-id="da500-527">Value</span></span>                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="da500-528">**Name**</span><span class="sxs-lookup"><span data-stu-id="da500-528">**Name**</span></span>                  | <span data-ttu-id="da500-529">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-529">Yes</span></span>         | <span data-ttu-id="da500-530">Der Name der zugehörigen Spalte.</span><span class="sxs-lookup"><span data-stu-id="da500-530">The name of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="da500-531">**Typ**</span><span class="sxs-lookup"><span data-stu-id="da500-531">**Type**</span></span>                  | <span data-ttu-id="da500-532">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-532">Yes</span></span>         | <span data-ttu-id="da500-533">Der Typ der zugehörigen Spalte.</span><span class="sxs-lookup"><span data-stu-id="da500-533">The type of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="da500-534">**NULL zulassen**</span><span class="sxs-lookup"><span data-stu-id="da500-534">**Nullable**</span></span>              | <span data-ttu-id="da500-535">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-535">No</span></span>          | <span data-ttu-id="da500-536">**True** (Standardwert) oder **false** , abhängig davon, ob die entsprechende Spalte einen NULL-Wert aufweisen kann.</span><span class="sxs-lookup"><span data-stu-id="da500-536">**True** (the default value) or **False** depending on whether the corresponding column can have a null value.</span></span>                                                                                                                  |
| <span data-ttu-id="da500-537">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="da500-537">**DefaultValue**</span></span>          | <span data-ttu-id="da500-538">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-538">No</span></span>          | <span data-ttu-id="da500-539">Der Standardwert der zugehörigen Spalte.</span><span class="sxs-lookup"><span data-stu-id="da500-539">The default value of the corresponding column.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="da500-540">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="da500-540">**MaxLength**</span></span>             | <span data-ttu-id="da500-541">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-541">No</span></span>          | <span data-ttu-id="da500-542">Maximale Länge der zugehörigen Spalte.</span><span class="sxs-lookup"><span data-stu-id="da500-542">The maximum length of the corresponding column.</span></span>                                                                                                                                                                                 |
| <span data-ttu-id="da500-543">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="da500-543">**FixedLength**</span></span>           | <span data-ttu-id="da500-544">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-544">No</span></span>          | <span data-ttu-id="da500-545">**True** oder **false** , abhängig davon, ob der entsprechende Spaltenwert als Zeichenfolge mit fester Länge gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="da500-545">**True** or **False** depending on whether the corresponding column value will be stored as a fixed length string.</span></span>                                                                                                              |
| <span data-ttu-id="da500-546">**Genauigkeit**</span><span class="sxs-lookup"><span data-stu-id="da500-546">**Precision**</span></span>             | <span data-ttu-id="da500-547">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-547">No</span></span>          | <span data-ttu-id="da500-548">Die Genauigkeit der zugehörigen Spalte.</span><span class="sxs-lookup"><span data-stu-id="da500-548">The precision of the corresponding column.</span></span>                                                                                                                                                                                      |
| <span data-ttu-id="da500-549">**Skalierung**</span><span class="sxs-lookup"><span data-stu-id="da500-549">**Scale**</span></span>                 | <span data-ttu-id="da500-550">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-550">No</span></span>          | <span data-ttu-id="da500-551">Die Dezimalstellenanzahl der zugehörigen Spalte.</span><span class="sxs-lookup"><span data-stu-id="da500-551">The scale of the corresponding column.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="da500-552">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="da500-552">**Unicode**</span></span>               | <span data-ttu-id="da500-553">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-553">No</span></span>          | <span data-ttu-id="da500-554">**True** oder **false** , abhängig davon, ob der entsprechende Spaltenwert als Unicode-Zeichenfolge gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="da500-554">**True** or **False** depending on whether the corresponding column value will be stored as a Unicode string.</span></span>                                                                                                                   |
| <span data-ttu-id="da500-555">**Sortierung**</span><span class="sxs-lookup"><span data-stu-id="da500-555">**Collation**</span></span>             | <span data-ttu-id="da500-556">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-556">No</span></span>          | <span data-ttu-id="da500-557">Eine Zeichenfolge, die angibt, welche Sortierreihenfolge in der Datenquelle verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="da500-557">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="da500-558">**SRID**</span><span class="sxs-lookup"><span data-stu-id="da500-558">**SRID**</span></span>                  | <span data-ttu-id="da500-559">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-559">No</span></span>          | <span data-ttu-id="da500-560">Verweis Bezeichner für räumliche Systeme.</span><span class="sxs-lookup"><span data-stu-id="da500-560">Spatial System Reference Identifier.</span></span> <span data-ttu-id="da500-561">Nur für Eigenschaften räumlicher Typen gültig.</span><span class="sxs-lookup"><span data-stu-id="da500-561">Valid only for properties of spatial types.</span></span> <span data-ttu-id="da500-562">Weitere Informationen finden Sie unter [SRID](https://en.wikipedia.org/wiki/SRID) und [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="da500-562">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="da500-563">**StoreGeneratedPattern**</span><span class="sxs-lookup"><span data-stu-id="da500-563">**StoreGeneratedPattern**</span></span> | <span data-ttu-id="da500-564">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-564">No</span></span>          | <span data-ttu-id="da500-565">**None**, **Identity** (wenn der entsprechende Spaltenwert eine in der Datenbank generierte Identität ist) oder **berechnet** (wenn der entsprechende Spaltenwert in der Datenbank berechnet wird).</span><span class="sxs-lookup"><span data-stu-id="da500-565">**None**, **Identity** (if the corresponding column value is an identity that is generated in the database), or **Computed** (if the corresponding column value is computed in the database).</span></span> <span data-ttu-id="da500-566">Ungültig für RowType-Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="da500-566">Not Valid for RowType properties.</span></span> |

> [!NOTE]
> <span data-ttu-id="da500-567">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Property** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-567">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="da500-568">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für SSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-568">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="da500-569">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-569">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="da500-570">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-570">Example</span></span>

<span data-ttu-id="da500-571">Das folgende Beispiel zeigt ein **EntityType** -Element mit zwei untergeordneten **Eigenschaften** Elementen:</span><span class="sxs-lookup"><span data-stu-id="da500-571">The following example shows an **EntityType** element with two child **Property** elements:</span></span>

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="propertyref-element-ssdl"></a><span data-ttu-id="da500-572">PropertyRef-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-572">PropertyRef Element (SSDL)</span></span>

<span data-ttu-id="da500-573">Das **PropertyRef** -Element in der Speicher Schema-Definitions Sprache (Store Schema Definition Language, SSDL) verweist auf eine für ein EntityType-Element definierte Eigenschaft, um anzugeben, dass die-Eigenschaft eine der folgenden Rollen ausführt:</span><span class="sxs-lookup"><span data-stu-id="da500-573">The **PropertyRef** element in store schema definition language (SSDL) references a property defined on an EntityType element to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="da500-574">Ist Teil des Primärschlüssels der Tabelle, die der **EntityType** darstellt.</span><span class="sxs-lookup"><span data-stu-id="da500-574">Be part of the primary key of the table that the **EntityType** represents.</span></span> <span data-ttu-id="da500-575">Ein oder mehrere **PropertyRef** -Elemente können verwendet werden, um einen Primärschlüssel zu definieren.</span><span class="sxs-lookup"><span data-stu-id="da500-575">One or more **PropertyRef** elements can be used to define a primary key.</span></span> <span data-ttu-id="da500-576">Weitere Informationen finden Sie unter Key-Element.</span><span class="sxs-lookup"><span data-stu-id="da500-576">For more information, see Key element.</span></span>
-   <span data-ttu-id="da500-577">Sie ist das abhängige Ende oder das Prinzipalende einer referenziellen Einschränkung.</span><span class="sxs-lookup"><span data-stu-id="da500-577">Be the dependent or principal end of a referential constraint.</span></span> <span data-ttu-id="da500-578">Weitere Informationen finden Sie unter ReferentialConstraint-Element.</span><span class="sxs-lookup"><span data-stu-id="da500-578">For more information, see ReferentialConstraint element.</span></span>

<span data-ttu-id="da500-579">Das **PropertyRef** -Element kann nur die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="da500-579">The **PropertyRef** element can only have the following child elements:</span></span>

-   <span data-ttu-id="da500-580">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-580">Documentation (zero or one)</span></span>
-   <span data-ttu-id="da500-581">Anmerkungselemente</span><span class="sxs-lookup"><span data-stu-id="da500-581">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="da500-582">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-582">Applicable Attributes</span></span>

<span data-ttu-id="da500-583">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **PropertyRef** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="da500-583">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="da500-584">Attributname</span><span class="sxs-lookup"><span data-stu-id="da500-584">Attribute Name</span></span> | <span data-ttu-id="da500-585">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="da500-585">Is Required</span></span> | <span data-ttu-id="da500-586">Wert</span><span class="sxs-lookup"><span data-stu-id="da500-586">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="da500-587">**Name**</span><span class="sxs-lookup"><span data-stu-id="da500-587">**Name**</span></span>       | <span data-ttu-id="da500-588">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-588">Yes</span></span>         | <span data-ttu-id="da500-589">Der Name der referenzierten Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="da500-589">The name of the referenced property.</span></span> |

> [!NOTE]
> <span data-ttu-id="da500-590">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **PropertyRef** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-590">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="da500-591">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-591">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="da500-592">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-592">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="da500-593">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-593">Example</span></span>

<span data-ttu-id="da500-594">Das folgende Beispiel zeigt ein **PropertyRef** -Element, das verwendet wird, um einen Primärschlüssel zu definieren, indem auf eine Eigenschaft verwiesen wird, die für ein **EntityType** -Element definiert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-594">The following example shows a **PropertyRef** element used to define a primary key by referencing a property that is defined on an **EntityType** element.</span></span>

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="referentialconstraint-element-ssdl"></a><span data-ttu-id="da500-595">ReferentialConstraint-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-595">ReferentialConstraint Element (SSDL)</span></span>

<span data-ttu-id="da500-596">Das **referentialeinschränkungs** -Element in der Datenspeicher Schema-Definitions Sprache (SSDL) stellt eine FOREIGN KEY-Einschränkung (auch referenzielle Integritäts Einschränkung genannt) in der zugrunde liegenden Datenbank dar.</span><span class="sxs-lookup"><span data-stu-id="da500-596">The **ReferentialConstraint** element in store schema definition language (SSDL) represents a foreign key constraint (also called a referential integrity constraint) in the underlying database.</span></span> <span data-ttu-id="da500-597">Das Prinzipalende und das abhängige Ende der Einschränkung werden durch das Principal-Element bzw. das Dependent-Element angegeben.</span><span class="sxs-lookup"><span data-stu-id="da500-597">The principal and dependent ends of the constraint are specified by the Principal and Dependent child elements, respectively.</span></span> <span data-ttu-id="da500-598">Auf Spalten, die am Prinzipalende und am abhängigen Enden beteiligt sind, wird mit PropertyRef-Elementen verwiesen.</span><span class="sxs-lookup"><span data-stu-id="da500-598">Columns that participate in the principal and dependent ends are referenced with PropertyRef elements.</span></span>

<span data-ttu-id="da500-599">Das **referentialeinschränkung** -Element ist ein optionales untergeordnetes Element des Association-Elements.</span><span class="sxs-lookup"><span data-stu-id="da500-599">The **ReferentialConstraint** element is an optional child element of the Association element.</span></span> <span data-ttu-id="da500-600">Wenn ein **referentialeinschränkungs** -Element nicht verwendet wird, um die FOREIGN KEY-Einschränkung zuzuordnen, die im **Association** -Element angegeben ist, muss hierfür ein AssociationSetMapping-Element verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-600">If a **ReferentialConstraint** element is not used to map the foreign key constraint that is specified in the **Association** element, an AssociationSetMapping element must be used to do this.</span></span>

<span data-ttu-id="da500-601">Das **referentialeinschränkung** -Element kann die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="da500-601">The **ReferentialConstraint** element can have the following child elements:</span></span>

-   <span data-ttu-id="da500-602">Dokumentation (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-602">Documentation (zero or one)</span></span>
-   <span data-ttu-id="da500-603">Principal (genau ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-603">Principal (exactly one)</span></span>
-   <span data-ttu-id="da500-604">Dependent (genau ein Element)</span><span class="sxs-lookup"><span data-stu-id="da500-604">Dependent (exactly one)</span></span>
-   <span data-ttu-id="da500-605">Anmerkungselemente (kein (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="da500-605">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="da500-606">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-606">Applicable Attributes</span></span>

<span data-ttu-id="da500-607">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **referentialeinschränkung** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-607">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferentialConstraint** element.</span></span> <span data-ttu-id="da500-608">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für SSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-608">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="da500-609">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-609">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="da500-610">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-610">Example</span></span>

<span data-ttu-id="da500-611">Das folgende Beispiel zeigt ein **Association** -Element, das ein **referentialeinschränkungs** -Element verwendet, um die Spalten anzugeben, die an der FOREIGN KEY-Einschränkung von **FK \_ CustomerOrders** beteiligt sind:</span><span class="sxs-lookup"><span data-stu-id="da500-611">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="returntype-element-ssdl"></a><span data-ttu-id="da500-612">ReturnType-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-612">ReturnType Element (SSDL)</span></span>

<span data-ttu-id="da500-613">Das **returnType** -Element in der Speicher Schema-Definitions Sprache (SSDL) gibt den Rückgabetyp für eine Funktion an, die in einem **Function** -Element definiert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-613">The **ReturnType** element in store schema definition language (SSDL) specifies the return type for a function that is defined in a **Function** element.</span></span> <span data-ttu-id="da500-614">Ein Funktions Rückgabetyp kann auch mit einem **returnType** -Attribut angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="da500-614">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="da500-615">Der Rückgabetyp einer Funktion wird mit dem **Type** -Attribut oder dem **returnType** -Element angegeben.</span><span class="sxs-lookup"><span data-stu-id="da500-615">The return type of a function is specified with the **Type** attribute or the **ReturnType** element.</span></span>

<span data-ttu-id="da500-616">Das **returnType** -Element kann die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="da500-616">The **ReturnType** element can have the following child elements:</span></span>

- <span data-ttu-id="da500-617">CollectionType (eins)</span><span class="sxs-lookup"><span data-stu-id="da500-617">CollectionType (one)</span></span>  

> [!NOTE]
> <span data-ttu-id="da500-618">Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **returnType** -Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-618">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** element.</span></span> <span data-ttu-id="da500-619">Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für SSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-619">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="da500-620">Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-620">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="da500-621">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-621">Example</span></span>

<span data-ttu-id="da500-622">Im folgenden Beispiel wird eine **Funktion** verwendet, die eine Auflistung von Zeilen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="da500-622">The following example uses a **Function** that returns a collection of rows.</span></span>

``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```


## <a name="rowtype-element-ssdl"></a><span data-ttu-id="da500-623">RowType-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-623">RowType Element (SSDL)</span></span>

<span data-ttu-id="da500-624">Ein **RowType** -Element in der Speicher Schema-Definitions Sprache (Store Schema Definition Language, SSDL) definiert eine unbenannte Struktur als Rückgabetyp für eine im Speicher definierte Funktion.</span><span class="sxs-lookup"><span data-stu-id="da500-624">A **RowType** element in store schema definition language (SSDL) defines an unnamed structure as a return type for a function defined in the store.</span></span>

<span data-ttu-id="da500-625">Ein **RowType** -Element ist das untergeordnete Element des **CollectionType** -Elements:</span><span class="sxs-lookup"><span data-stu-id="da500-625">A **RowType** element is the child element of **CollectionType** element:</span></span>

<span data-ttu-id="da500-626">Ein **RowType** -Element kann die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="da500-626">A **RowType** element can have the following child elements:</span></span>

- <span data-ttu-id="da500-627">Eigenschaft (ein oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="da500-627">Property (one or more)</span></span>  

### <a name="example"></a><span data-ttu-id="da500-628">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-628">Example</span></span>

<span data-ttu-id="da500-629">Das folgende Beispiel zeigt eine Store-Funktion, die ein **CollectionType** -Element verwendet, um anzugeben, dass die Funktion eine Auflistung von Zeilen zurückgibt (wie im **RowType** -Element angegeben).</span><span class="sxs-lookup"><span data-stu-id="da500-629">The following example shows a store function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>


``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```

## <a name="schema-element-ssdl"></a><span data-ttu-id="da500-630">Schema-Element (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-630">Schema Element (SSDL)</span></span>

<span data-ttu-id="da500-631">Das **Schema** -Element in der Speicher Schema-Definitions Sprache (Store Schema Definition Language, SSDL) ist das Stamm Element einer Speichermodell Definition.</span><span class="sxs-lookup"><span data-stu-id="da500-631">The **Schema** element in store schema definition language (SSDL) is the root element of a storage model definition.</span></span> <span data-ttu-id="da500-632">Es enthält Definitionen für die Objekte, Funktionen und Container, die zusammen ein Speichermodell bilden.</span><span class="sxs-lookup"><span data-stu-id="da500-632">It contains definitions for the objects, functions, and containers that make up a storage model.</span></span>

<span data-ttu-id="da500-633">Das **Schema** Element kann NULL oder mehr der folgenden untergeordneten Elemente enthalten:</span><span class="sxs-lookup"><span data-stu-id="da500-633">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="da500-634">Zuordnung</span><span class="sxs-lookup"><span data-stu-id="da500-634">Association</span></span>
-   <span data-ttu-id="da500-635">EntityType</span><span class="sxs-lookup"><span data-stu-id="da500-635">EntityType</span></span>
-   <span data-ttu-id="da500-636">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="da500-636">EntityContainer</span></span>
-   <span data-ttu-id="da500-637">Funktion</span><span class="sxs-lookup"><span data-stu-id="da500-637">Function</span></span>

<span data-ttu-id="da500-638">Das **Schema** -Element verwendet das **Namespace** -Attribut, um den Namespace für den Entitätstyp und die Zuordnungs Objekte in einem Speichermodell zu definieren.</span><span class="sxs-lookup"><span data-stu-id="da500-638">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type and association objects in a storage model.</span></span> <span data-ttu-id="da500-639">Innerhalb eines Namespace müssen alle Objekte eine eindeutige Bezeichnung aufweisen.</span><span class="sxs-lookup"><span data-stu-id="da500-639">Within a namespace, no two objects can have the same name.</span></span>

<span data-ttu-id="da500-640">Ein Speichermodell-Namespace unterscheidet sich vom XML-Namespace des **Schema** -Elements.</span><span class="sxs-lookup"><span data-stu-id="da500-640">A storage model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="da500-641">Ein Speichermodell-Namespace (wie durch das **Namespace** -Attribut definiert) ist ein logischer Container für Entitäts Typen und Zuordnungs Typen.</span><span class="sxs-lookup"><span data-stu-id="da500-641">A storage model namespace (as defined by the **Namespace** attribute) is a logical container for entity types and association types.</span></span> <span data-ttu-id="da500-642">Der XML-Namespace (angegeben durch das **xmlns** -Attribut) eines **Schema** -Elements ist der Standard Namespace für untergeordnete Elemente und Attribute des **Schema** -Elements.</span><span class="sxs-lookup"><span data-stu-id="da500-642">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="da500-643">XML-Namespaces im Formular https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (wobei yyyy und mm jeweils ein Jahr und einen Monat darstellen) sind für SSDL reserviert.</span><span class="sxs-lookup"><span data-stu-id="da500-643">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (where YYYY and MM represent a year and month respectively) are reserved for SSDL.</span></span> <span data-ttu-id="da500-644">Benutzerdefinierte Elemente und Attribute können nicht in Namespaces mit diesem Format vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="da500-644">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="da500-645">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="da500-645">Applicable Attributes</span></span>

<span data-ttu-id="da500-646">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Schema** Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="da500-646">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="da500-647">Attributname</span><span class="sxs-lookup"><span data-stu-id="da500-647">Attribute Name</span></span>            | <span data-ttu-id="da500-648">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="da500-648">Is Required</span></span> | <span data-ttu-id="da500-649">Wert</span><span class="sxs-lookup"><span data-stu-id="da500-649">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="da500-650">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="da500-650">**Namespace**</span></span>             | <span data-ttu-id="da500-651">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-651">Yes</span></span>         | <span data-ttu-id="da500-652">Der Namespace für das Speichermodell.</span><span class="sxs-lookup"><span data-stu-id="da500-652">The namespace of the storage model.</span></span> <span data-ttu-id="da500-653">Der Wert des **Namespace** -Attributs wird verwendet, um den voll qualifizierten Namen eines Typs zu bilden.</span><span class="sxs-lookup"><span data-stu-id="da500-653">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="da500-654">Wenn sich beispielsweise ein **EntityType** mit dem Namen *Customer* im examplemodel. Store-Namespace befindet, ist der voll qualifizierte Name von **EntityType** examplemodel. Store. Customer.</span><span class="sxs-lookup"><span data-stu-id="da500-654">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace, then the fully qualified name of the **EntityType** is ExampleModel.Store.Customer.</span></span> <br/> <span data-ttu-id="da500-655">Die folgenden Zeichen folgen können nicht als Wert für das **Namespace** -Attribut verwendet werden: **System**, **transient**oder **EDM**.</span><span class="sxs-lookup"><span data-stu-id="da500-655">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="da500-656">Der Wert für das **Namespace** -Attribut darf nicht mit dem Wert für das **Namespace** -Attribut im CSDL-Schema Element identisch sein.</span><span class="sxs-lookup"><span data-stu-id="da500-656">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the CSDL Schema element.</span></span> |
| <span data-ttu-id="da500-657">**Alias**</span><span class="sxs-lookup"><span data-stu-id="da500-657">**Alias**</span></span>                 | <span data-ttu-id="da500-658">Nein</span><span class="sxs-lookup"><span data-stu-id="da500-658">No</span></span>          | <span data-ttu-id="da500-659">Ein anstelle der Namespacebezeichnung verwendeter Bezeichner.</span><span class="sxs-lookup"><span data-stu-id="da500-659">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="da500-660">Wenn sich z. b. ein **EntityType** mit dem Namen *Customer* im examplemodel. Store-Namespace und der Wert des **Alias** -Attributs *storagemodel*befindet, können Sie storagemodel. Customer als voll qualifizierten Namen des **EntityType verwenden.**</span><span class="sxs-lookup"><span data-stu-id="da500-660">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace and the value of the **Alias** attribute is *StorageModel*, then you can use StorageModel.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="da500-661">**Anbieter**</span><span class="sxs-lookup"><span data-stu-id="da500-661">**Provider**</span></span>              | <span data-ttu-id="da500-662">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-662">Yes</span></span>         | <span data-ttu-id="da500-663">Der Datenanbieter.</span><span class="sxs-lookup"><span data-stu-id="da500-663">The data provider.</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="da500-664">**ProviderManifestToken**</span><span class="sxs-lookup"><span data-stu-id="da500-664">**ProviderManifestToken**</span></span> | <span data-ttu-id="da500-665">Ja</span><span class="sxs-lookup"><span data-stu-id="da500-665">Yes</span></span>         | <span data-ttu-id="da500-666">Ein Token, das dem Anbieter angibt, welches Anbietermanifest zurückgegeben werden soll.</span><span class="sxs-lookup"><span data-stu-id="da500-666">A token that indicates to the provider which provider manifest to return.</span></span> <span data-ttu-id="da500-667">Für das Token ist kein Format definiert.</span><span class="sxs-lookup"><span data-stu-id="da500-667">No format for the token is defined.</span></span> <span data-ttu-id="da500-668">Die für das Token möglichen Werte werden vom Anbieter definiert.</span><span class="sxs-lookup"><span data-stu-id="da500-668">Values for the token are defined by the provider.</span></span> <span data-ttu-id="da500-669">Informationen zu SQL Server-Anbieter Manifest-Token finden Sie unter SqlClient for Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="da500-669">For information about SQL Server provider manifest tokens, see SqlClient for Entity Framework.</span></span>                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a><span data-ttu-id="da500-670">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-670">Example</span></span>

<span data-ttu-id="da500-671">Das folgende Beispiel zeigt ein **Schema** -Element, das ein **EntityContainer** -Element, zwei **EntityType** -Elemente und ein **Association** -Element enthält.</span><span class="sxs-lookup"><span data-stu-id="da500-671">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

``` xml
 <Schema Namespace="ExampleModel.Store"
       Alias="Self" Provider="System.Data.SqlClient"
       ProviderManifestToken="2008"
       xmlns="https://schemas.microsoft.com/ado/2009/11/edm/ssdl">
   <EntityContainer Name="ExampleModelStoreContainer">
     <EntitySet Name="Customers"
                EntityType="ExampleModel.Store.Customers"
                Schema="dbo" />
     <EntitySet Name="Orders"
                EntityType="ExampleModel.Store.Orders"
                Schema="dbo" />
     <AssociationSet Name="FK_CustomerOrders"
                     Association="ExampleModel.Store.FK_CustomerOrders">
       <End Role="Customers" EntitySet="Customers" />
       <End Role="Orders" EntitySet="Orders" />
     </AssociationSet>
   </EntityContainer>
   <EntityType Name="Customers">
     <Documentation>
       <Summary>Summary here.</Summary>
       <LongDescription>Long description here.</LongDescription>
     </Documentation>
     <Key>
       <PropertyRef Name="CustomerId" />
     </Key>
     <Property Name="CustomerId" Type="int" Nullable="false" />
     <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
   </EntityType>
   <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
     <Key>
       <PropertyRef Name="OrderId" />
     </Key>
     <Property Name="OrderId" Type="int" Nullable="false"
               c:CustomAttribute="someValue"/>
     <Property Name="ProductId" Type="int" Nullable="false" />
     <Property Name="Quantity" Type="int" Nullable="false" />
     <Property Name="CustomerId" Type="int" Nullable="false" />
     <c:CustomElement>
       Custom data here.
     </c:CustomElement>
   </EntityType>
   <Association Name="FK_CustomerOrders">
     <End Role="Customers"
          Type="ExampleModel.Store.Customers" Multiplicity="1">
       <OnDelete Action="Cascade" />
     </End>
     <End Role="Orders"
          Type="ExampleModel.Store.Orders" Multiplicity="*" />
     <ReferentialConstraint>
       <Principal Role="Customers">
         <PropertyRef Name="CustomerId" />
       </Principal>
       <Dependent Role="Orders">
         <PropertyRef Name="CustomerId" />
       </Dependent>
     </ReferentialConstraint>
   </Association>
   <Function Name="UpdateOrderQuantity"
             Aggregate="false"
             BuiltIn="false"
             NiladicFunction="false"
             IsComposable="false"
             ParameterTypeSemantics="AllowImplicitConversion"
             Schema="dbo">
     <Parameter Name="orderId" Type="int" Mode="In" />
     <Parameter Name="newQuantity" Type="int" Mode="In" />
   </Function>
   <Function Name="UpdateProductInOrder" IsComposable="false">
     <CommandText>
       UPDATE Orders
       SET ProductId = @productId
       WHERE OrderId = @orderId;
     </CommandText>
     <Parameter Name="productId"
                Mode="In"
                Type="int"/>
     <Parameter Name="orderId"
                Mode="In"
                Type="int"/>
   </Function>
 </Schema>
```

## <a name="annotation-attributes"></a><span data-ttu-id="da500-672">Anmerkungsattribute</span><span class="sxs-lookup"><span data-stu-id="da500-672">Annotation Attributes</span></span>

<span data-ttu-id="da500-673">Anmerkungsattribute sind in der Datenspeicherschema-Definitionssprache (Store Schema Definition Language, SSDL) benutzerdefinierte XML-Attribute im Speichermodell, die zusätzliche Metadaten zu den Elementen im Speichermodell bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="da500-673">Annotation attributes in store schema definition language (SSDL) are custom XML attributes in the storage model that provide extra metadata about the elements in the storage model.</span></span> <span data-ttu-id="da500-674">Neben dem Vorhandensein einer gültigen XML-Struktur gelten für Anmerkungsattribute folgende Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="da500-674">In addition to having valid XML structure, the following constraints apply to annotation attributes:</span></span>

-   <span data-ttu-id="da500-675">Anmerkungsattribute dürfen sich in keinem XML-Namespace befinden, der für SSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-675">Annotation attributes must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="da500-676">Die vollqualifizierten Namen zweier Anmerkungsattribute dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-676">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="da500-677">Für ein angegebenes SSDL-Element kann mehr als ein Anmerkungsattribut übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="da500-677">More than one annotation attribute may be applied to a given SSDL element.</span></span> <span data-ttu-id="da500-678">Auf Metadaten, die in Anmerkung-Elementen enthalten sind, kann zur Laufzeit mithilfe von Klassen im System. Data. Metadata. Edm-Namespace zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="da500-678">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="da500-679">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-679">Example</span></span>

<span data-ttu-id="da500-680">Das folgende Beispiel zeigt ein EntityType-Element, das über ein Anmerkung-Attribut verfügt, das auf die **OrderID** -Eigenschaft angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="da500-680">The following example shows an EntityType element that has an annotation attribute applied to the **OrderId** property.</span></span> <span data-ttu-id="da500-681">Das Beispiel zeigt auch ein Anmerkung-Element, das dem **EntityType** -Element hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="da500-681">The example also show an annotation element added to the **EntityType** element.</span></span>

``` xml
 <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
   <Key>
     <PropertyRef Name="OrderId" />
   </Key>
   <Property Name="OrderId" Type="int" Nullable="false"
             c:CustomAttribute="someValue"/>
   <Property Name="ProductId" Type="int" Nullable="false" />
   <Property Name="Quantity" Type="int" Nullable="false" />
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <c:CustomElement>
     Custom data here.
   </c:CustomElement>
 </EntityType>
```

## <a name="annotation-elements-ssdl"></a><span data-ttu-id="da500-682">Anmerkungelemente (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-682">Annotation Elements (SSDL)</span></span>

<span data-ttu-id="da500-683">Anmerkungselemente sind in der Datenspeicherschema-Definitionssprache (Store Schema Definition Language, SSDL) benutzerdefinierte XML-Elemente im Speichermodell, die zusätzliche Metadaten zum Speichermodell bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="da500-683">Annotation elements in store schema definition language (SSDL) are custom XML elements in the storage model that provide extra metadata about the storage model.</span></span> <span data-ttu-id="da500-684">Neben dem Vorhandensein einer gültigen XML-Struktur gelten für Anmerkungselemente folgende Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="da500-684">In addition to having valid XML structure, the following constraints apply to annotation elements:</span></span>

-   <span data-ttu-id="da500-685">Anmerkungselemente dürfen sich in keinem XML-Namespace befinden, der für SSDL reserviert ist.</span><span class="sxs-lookup"><span data-stu-id="da500-685">Annotation elements must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="da500-686">Die vollqualifizierten Namen zweier Anmerkungselemente dürfen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="da500-686">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="da500-687">Anmerkungselemente müssen nach allen anderen untergeordneten Elementen eines angegebenen SSDL-Elements angeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="da500-687">Annotation elements must appear after all other child elements of a given SSDL element.</span></span>

<span data-ttu-id="da500-688">Mehrere Anmerkungselemente können untergeordnete Elemente eines angegebenen SSDL-Elements sein.</span><span class="sxs-lookup"><span data-stu-id="da500-688">More than one annotation element may be a child of a given SSDL element.</span></span> <span data-ttu-id="da500-689">Ab Version 4 von .NET Framework können mithilfe von Klassen im Namespace "System. Data. Metadata. Edm" zur Laufzeit auf Metadaten zugegriffen werden, die in Anmerkung-Elementen enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="da500-689">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="da500-690">Beispiel</span><span class="sxs-lookup"><span data-stu-id="da500-690">Example</span></span>

<span data-ttu-id="da500-691">Das folgende Beispiel zeigt ein EntityType-Element, das über ein Anmerkung-Element (**customelement**) verfügt.</span><span class="sxs-lookup"><span data-stu-id="da500-691">The following example shows an EntityType element that has an annotation element (**CustomElement**).</span></span> <span data-ttu-id="da500-692">Das Beispiel zeigt auch ein Anmerkung-Attribut, das auf die **OrderID** -Eigenschaft angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="da500-692">The example also shows an annotation attribute applied to the **OrderId** property.</span></span>

``` xml
 <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
   <Key>
     <PropertyRef Name="OrderId" />
   </Key>
   <Property Name="OrderId" Type="int" Nullable="false"
             c:CustomAttribute="someValue"/>
   <Property Name="ProductId" Type="int" Nullable="false" />
   <Property Name="Quantity" Type="int" Nullable="false" />
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <c:CustomElement>
     Custom data here.
   </c:CustomElement>
 </EntityType>
```

## <a name="facets-ssdl"></a><span data-ttu-id="da500-693">Facets (SSDL)</span><span class="sxs-lookup"><span data-stu-id="da500-693">Facets (SSDL)</span></span>

<span data-ttu-id="da500-694">Facets stellen in der Datenspeicherschema-Definitionssprache (Store Schema Definition Language, SSDL) Einschränkungen für Spaltentypen dar, die in Eigenschaftenelementen angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="da500-694">Facets in store schema definition language (SSDL) represent constraints on column types that are specified in Property elements.</span></span> <span data-ttu-id="da500-695">Facetten werden als XML-Attribute für **Eigenschaften** Elemente implementiert.</span><span class="sxs-lookup"><span data-stu-id="da500-695">Facets are implemented as XML attributes on **Property** elements.</span></span>

<span data-ttu-id="da500-696">In der folgenden Tabelle werden die in SSDL unterstützten Facets beschrieben:</span><span class="sxs-lookup"><span data-stu-id="da500-696">The following table describes the facets that are supported in SSDL:</span></span>

| <span data-ttu-id="da500-697">Facet</span><span class="sxs-lookup"><span data-stu-id="da500-697">Facet</span></span>           | <span data-ttu-id="da500-698">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="da500-698">Description</span></span>                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="da500-699">**Sortierung**</span><span class="sxs-lookup"><span data-stu-id="da500-699">**Collation**</span></span>   | <span data-ttu-id="da500-700">Gibt die bei Vergleich- und Sortiervorgängen zu verwendende Sortierreihenfolge für die Werte der Eigenschaft an.</span><span class="sxs-lookup"><span data-stu-id="da500-700">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                             |
| <span data-ttu-id="da500-701">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="da500-701">**FixedLength**</span></span> | <span data-ttu-id="da500-702">Gibt an, ob sich die Länge des Spaltenwerts ändern kann.</span><span class="sxs-lookup"><span data-stu-id="da500-702">Specifies whether the length of the column value can vary.</span></span>                                                                                                                                                                                                  |
| <span data-ttu-id="da500-703">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="da500-703">**MaxLength**</span></span>   | <span data-ttu-id="da500-704">Gibt die maximale Länge des Spaltenwerts an.</span><span class="sxs-lookup"><span data-stu-id="da500-704">Specifies the maximum length of the column value.</span></span>                                                                                                                                                                                                           |
| <span data-ttu-id="da500-705">**Genauigkeit**</span><span class="sxs-lookup"><span data-stu-id="da500-705">**Precision**</span></span>   | <span data-ttu-id="da500-706">Gibt bei Eigenschaften vom Typ **Decimal**die Anzahl der Ziffern an, die ein Eigenschafts Wert aufweisen kann.</span><span class="sxs-lookup"><span data-stu-id="da500-706">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="da500-707">Bei Eigenschaften vom Typ **time**, **DateTime**und **DateTimeOffset**wird die Anzahl von Ziffern für die Sekundenbruchteile des Spaltenwerts angegeben.</span><span class="sxs-lookup"><span data-stu-id="da500-707">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the column value.</span></span> |
| <span data-ttu-id="da500-708">**Skalierung**</span><span class="sxs-lookup"><span data-stu-id="da500-708">**Scale**</span></span>       | <span data-ttu-id="da500-709">Gibt die Anzahl der Dezimalstellen für den Spaltenwert an.</span><span class="sxs-lookup"><span data-stu-id="da500-709">Specifies the number of digits to the right of the decimal point for the column value.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="da500-710">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="da500-710">**Unicode**</span></span>     | <span data-ttu-id="da500-711">Gibt an, ob der Spaltenwert als Unicode gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="da500-711">Indicates whether the column value is stored as Unicode.</span></span>                                                                                                                                                                                                    |
