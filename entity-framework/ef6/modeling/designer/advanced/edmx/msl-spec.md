---
title: MSL-Spezifikation-EF6
description: MSL-Spezifikation in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 13ae7bc1-74b4-4ee4-8d73-c337be841467
uid: ef6/modeling/designer/advanced/edmx/msl-spec
ms.openlocfilehash: 4ec43f48f82b83bd85b47fe234be143d0e4d2560
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620621"
---
# <a name="msl-specification"></a><span data-ttu-id="9735a-103">MSL-Spezifikation</span><span class="sxs-lookup"><span data-stu-id="9735a-103">MSL Specification</span></span>
<span data-ttu-id="9735a-104">Bei der Mapping-Spezifikationssprache (MSL) handelt es sich um eine XML-basierte Sprache, die die Zuordnung zwischen dem konzeptionellen Modell und dem Speichermodell einer Entity Framework Anwendung beschreibt.</span><span class="sxs-lookup"><span data-stu-id="9735a-104">Mapping specification language (MSL) is an XML-based language that describes the mapping between the conceptual model and storage model of an Entity Framework application.</span></span>

<span data-ttu-id="9735a-105">In einer Entity Framework Anwendung werden die Mapping-Metadaten zur Buildzeit aus einer MSL-Datei (geschrieben in MSL) geladen.</span><span class="sxs-lookup"><span data-stu-id="9735a-105">In an Entity Framework application, mapping metadata is loaded from an .msl file (written in MSL) at build time.</span></span> <span data-ttu-id="9735a-106">Entity Framework verwendet zur Laufzeit Mapping-Metadaten, um Abfragen für das konzeptionelle Modell in Speicher spezifische Befehle zu übersetzen.</span><span class="sxs-lookup"><span data-stu-id="9735a-106">Entity Framework uses mapping metadata at runtime to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="9735a-107">Der Entity Framework Designer (EF-Designer) speichert Zuordnungsinformationen in einer EDMX-Datei zur Entwurfszeit.</span><span class="sxs-lookup"><span data-stu-id="9735a-107">The Entity Framework Designer (EF Designer) stores mapping information in an .edmx file at design time.</span></span> <span data-ttu-id="9735a-108">Zur Buildzeit verwendet die Entity Designer Informationen in einer EDMX-Datei, um die MSL-Datei zu erstellen, die zur Laufzeit von Entity Framework benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-108">At build time, the Entity Designer uses information in an .edmx file to create the .msl file that is needed by Entity Framework at runtime</span></span>

<span data-ttu-id="9735a-109">Namen aller konzeptionellen oder Speichermodelltypen, auf denen in MSL verwiesen wird, müssen mit dem jeweiligen Namespacenamen qualifiziert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-109">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="9735a-110">Weitere Informationen zum Namespace Namen des konzeptionellen Modells finden Sie unter [CSDL-Spezifikation](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span><span class="sxs-lookup"><span data-stu-id="9735a-110">For information about the conceptual model namespace name, see [CSDL Specification](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span></span> <span data-ttu-id="9735a-111">Weitere Informationen zum Namespace Namen des Speicher Modells finden Sie unter [SSDL-Spezifikation](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span><span class="sxs-lookup"><span data-stu-id="9735a-111">For information about the storage model namespace name, see [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span></span>

<span data-ttu-id="9735a-112">MSL-Versionen unterscheiden sich von XML-Namespaces.</span><span class="sxs-lookup"><span data-stu-id="9735a-112">Versions of MSL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="9735a-113">MSL-Version</span><span class="sxs-lookup"><span data-stu-id="9735a-113">MSL Version</span></span> | <span data-ttu-id="9735a-114">XML-Namespace</span><span class="sxs-lookup"><span data-stu-id="9735a-114">XML Namespace</span></span>                                        |
|:------------|:-----------------------------------------------------|
| <span data-ttu-id="9735a-115">MSL v1</span><span class="sxs-lookup"><span data-stu-id="9735a-115">MSL v1</span></span>      | <span data-ttu-id="9735a-116">urn: Schemas-Microsoft-com: Windows: Storage: Mapping: CS</span><span class="sxs-lookup"><span data-stu-id="9735a-116">urn:schemas-microsoft-com:windows:storage:mapping:CS</span></span> |
| <span data-ttu-id="9735a-117">MSL v2</span><span class="sxs-lookup"><span data-stu-id="9735a-117">MSL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/mapping/cs |
| <span data-ttu-id="9735a-118">MSL v3</span><span class="sxs-lookup"><span data-stu-id="9735a-118">MSL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a><span data-ttu-id="9735a-119">Alias-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-119">Alias Element (MSL)</span></span>

<span data-ttu-id="9735a-120">Das **Alias** -Element in der Mapping-Spezifikationssprache (MSL) ist ein untergeordnetes Element des Mapping-Elements, das zum Definieren von Aliasen für das konzeptionelle Modell und die Namespaces des Speicher Modells verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-120">The **Alias** element in mapping specification language (MSL) is a child of the Mapping element that is used to define aliases for conceptual model and storage model namespaces.</span></span> <span data-ttu-id="9735a-121">Namen aller konzeptionellen oder Speichermodelltypen, auf denen in MSL verwiesen wird, müssen mit dem jeweiligen Namespacenamen qualifiziert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-121">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="9735a-122">Weitere Informationen zum Namespace Namen des konzeptionellen Modells finden Sie unter Schema-Element (CSDL).</span><span class="sxs-lookup"><span data-stu-id="9735a-122">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="9735a-123">Weitere Informationen zum Namespace Namen des Speicher Modells finden Sie unter Schema-Element (SSDL).</span><span class="sxs-lookup"><span data-stu-id="9735a-123">For information about the storage model namespace name, see Schema Element (SSDL).</span></span>

<span data-ttu-id="9735a-124">Das **Alias** -Element darf keine untergeordneten Elemente aufweisen.</span><span class="sxs-lookup"><span data-stu-id="9735a-124">The **Alias** element cannot have child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-125">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-125">Applicable Attributes</span></span>

<span data-ttu-id="9735a-126">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Alias** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="9735a-126">The table below describes the attributes that can be applied to the **Alias** element.</span></span>

| <span data-ttu-id="9735a-127">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-127">Attribute Name</span></span> | <span data-ttu-id="9735a-128">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-128">Is Required</span></span> | <span data-ttu-id="9735a-129">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-129">Value</span></span>                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| <span data-ttu-id="9735a-130">**Schlüssel**</span><span class="sxs-lookup"><span data-stu-id="9735a-130">**Key**</span></span>        | <span data-ttu-id="9735a-131">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-131">Yes</span></span>         | <span data-ttu-id="9735a-132">Der Alias für den Namespace, der durch das **value** -Attribut angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-132">The alias for the namespace that is specified by the **Value** attribute.</span></span> |
| <span data-ttu-id="9735a-133">**Wert**</span><span class="sxs-lookup"><span data-stu-id="9735a-133">**Value**</span></span>      | <span data-ttu-id="9735a-134">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-134">Yes</span></span>         | <span data-ttu-id="9735a-135">Der Namespace, für den der Wert des **Schlüssel** Elements ein Alias ist.</span><span class="sxs-lookup"><span data-stu-id="9735a-135">The namespace for which the value of the **Key** element is an alias.</span></span>     |

### <a name="example"></a><span data-ttu-id="9735a-136">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-136">Example</span></span>

<span data-ttu-id="9735a-137">Das folgende Beispiel zeigt ein **Alias** Element, das einen Alias für Typen definiert, die `c` im konzeptionellen Modell definiert sind.</span><span class="sxs-lookup"><span data-stu-id="9735a-137">The following example shows an **Alias** element that defines an alias, `c`, for types that are defined in the conceptual model.</span></span>

``` xml
 <Mapping Space="C-S"
          xmlns="https://schemas.microsoft.com/ado/2009/11/mapping/cs">
   <Alias Key="c" Value="SchoolModel"/>
   <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                           CdmEntityContainer="SchoolModelEntities">
     <EntitySetMapping Name="Courses">
       <EntityTypeMapping TypeName="c.Course">
         <MappingFragment StoreEntitySet="Course">
           <ScalarProperty Name="CourseID" ColumnName="CourseID" />
           <ScalarProperty Name="Title" ColumnName="Title" />
           <ScalarProperty Name="Credits" ColumnName="Credits" />
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
     <EntitySetMapping Name="Departments">
       <EntityTypeMapping TypeName="c.Department">
         <MappingFragment StoreEntitySet="Department">
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
           <ScalarProperty Name="Name" ColumnName="Name" />
           <ScalarProperty Name="Budget" ColumnName="Budget" />
           <ScalarProperty Name="StartDate" ColumnName="StartDate" />
           <ScalarProperty Name="Administrator" ColumnName="Administrator" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
   </EntityContainerMapping>
 </Mapping>
```

## <a name="associationend-element-msl"></a><span data-ttu-id="9735a-138">AssociationEnd-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-138">AssociationEnd Element (MSL)</span></span>

<span data-ttu-id="9735a-139">Das **AssociationEnd** -Element in der Mapping-Spezifikationssprache (MSL) wird verwendet, wenn die Änderungs Funktionen eines Entitäts Typs im konzeptionellen Modell gespeicherten Prozeduren in der zugrunde liegenden Datenbank zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-139">The **AssociationEnd** element in mapping specification language (MSL) is used when the modification functions of an entity type in the conceptual model are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="9735a-140">Wenn eine gespeicherte Änderungs Prozedur einen Parameter annimmt, dessen Wert in einer Association-Eigenschaft enthalten ist, ordnet das **AssociationEnd** -Element den-Eigenschafts Wert dem-Parameter zu.</span><span class="sxs-lookup"><span data-stu-id="9735a-140">If a modification stored procedure takes a parameter whose value is held in an association property, the **AssociationEnd** element maps the property value to the parameter.</span></span> <span data-ttu-id="9735a-141">Weitere Informationen finden Sie im untenstehenden Beispiel.</span><span class="sxs-lookup"><span data-stu-id="9735a-141">For more information, see the example below.</span></span>

<span data-ttu-id="9735a-142">Weitere Informationen zum Mapping von Änderungs Funktionen von Entitäts Typen zu gespeicherten Prozeduren finden Sie unter ModificationFunctionMapping-Element (MSL) und Exemplarische Vorgehensweise: Mapping einer Entität zu gespeicherten Prozeduren.</span><span class="sxs-lookup"><span data-stu-id="9735a-142">For more information about mapping modification functions of entity types to stored procedures, see ModificationFunctionMapping Element (MSL) and Walkthrough: Mapping an Entity to Stored Procedures.</span></span>

<span data-ttu-id="9735a-143">Das **AssociationEnd** -Element kann die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="9735a-143">The **AssociationEnd** element can have the following child elements:</span></span>

-   <span data-ttu-id="9735a-144">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="9735a-144">ScalarProperty</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-145">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-145">Applicable Attributes</span></span>

<span data-ttu-id="9735a-146">In der folgenden Tabelle werden die Attribute beschrieben, die für das **AssociationEnd** -Element anwendbar sind.</span><span class="sxs-lookup"><span data-stu-id="9735a-146">The following table describes the attributes that are applicable to the **AssociationEnd** element.</span></span>

| <span data-ttu-id="9735a-147">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-147">Attribute Name</span></span>     | <span data-ttu-id="9735a-148">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-148">Is Required</span></span> | <span data-ttu-id="9735a-149">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-149">Value</span></span>                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-150">**AssociationSet**</span><span class="sxs-lookup"><span data-stu-id="9735a-150">**AssociationSet**</span></span> | <span data-ttu-id="9735a-151">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-151">Yes</span></span>         | <span data-ttu-id="9735a-152">Der Name der Zuordnung, die zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-152">The name of the association that is being mapped.</span></span>                                                                                                                                 |
| <span data-ttu-id="9735a-153">**From**</span><span class="sxs-lookup"><span data-stu-id="9735a-153">**From**</span></span>           | <span data-ttu-id="9735a-154">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-154">Yes</span></span>         | <span data-ttu-id="9735a-155">Der Wert des **FromRole** -Attributs der Navigations Eigenschaft, die der zugeordneten Zuordnung entspricht.</span><span class="sxs-lookup"><span data-stu-id="9735a-155">The value of the **FromRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="9735a-156">Weitere Informationen finden Sie unter NavigationProperty-Element (CSDL).</span><span class="sxs-lookup"><span data-stu-id="9735a-156">For more information, see NavigationProperty Element (CSDL).</span></span> |
| <span data-ttu-id="9735a-157">**An**</span><span class="sxs-lookup"><span data-stu-id="9735a-157">**To**</span></span>             | <span data-ttu-id="9735a-158">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-158">Yes</span></span>         | <span data-ttu-id="9735a-159">Der Wert des Attributs " **Tor** " der Navigations Eigenschaft, die der Zuordnung entspricht, die zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-159">The value of the **ToRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="9735a-160">Weitere Informationen finden Sie unter NavigationProperty-Element (CSDL).</span><span class="sxs-lookup"><span data-stu-id="9735a-160">For more information, see NavigationProperty Element (CSDL).</span></span>   |

### <a name="example"></a><span data-ttu-id="9735a-161">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-161">Example</span></span>

<span data-ttu-id="9735a-162">Betrachten Sie den folgenden Entitätstyp des konzeptionellen Modells:</span><span class="sxs-lookup"><span data-stu-id="9735a-162">Consider the following conceptual model entity type:</span></span>

``` xml
 <EntityType Name="Course">
   <Key>
     <PropertyRef Name="CourseID" />
   </Key>
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" MaxLength="100"
             FixedLength="false" Unicode="true" />
   <Property Type="Int32" Name="Credits" Nullable="false" />
   <NavigationProperty Name="Department"
                       Relationship="SchoolModel.FK_Course_Department"
                       FromRole="Course" ToRole="Department" />
 </EntityType>
```

<span data-ttu-id="9735a-163">Betrachten Sie auch die folgende gespeicherte Prozedur:</span><span class="sxs-lookup"><span data-stu-id="9735a-163">Also consider the following stored procedure:</span></span>

``` SQL
 CREATE PROCEDURE [dbo].[UpdateCourse]
                                @CourseID int,
                                @Title nvarchar(50),
                                @Credits int,
                                @DepartmentID int
                                AS
                                UPDATE Course SET Title=@Title,
                                                              Credits=@Credits,
                                                              DepartmentID=@DepartmentID
                                WHERE CourseID=@CourseID;
```

<span data-ttu-id="9735a-164">Um die Aktualisierungs Funktion der `Course` Entität dieser gespeicherten Prozedur zuzuordnen, müssen Sie einen Wert für den **DepartmentID** -Parameter angeben.</span><span class="sxs-lookup"><span data-stu-id="9735a-164">In order to map the update function of the `Course` entity to this stored procedure, you must supply a value to the **DepartmentID** parameter.</span></span> <span data-ttu-id="9735a-165">Der Wert für `DepartmentID` entspricht keiner Eigenschaft des Entitätstyps; er ist vielmehr in einer unabhängigen Zuordnung enthalten, deren Zuordnung hier angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="9735a-165">The value for `DepartmentID` does not correspond to a property on the entity type; it is contained in an independent association whose mapping is shown here:</span></span>

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
 </AssociationSetMapping>
```

<span data-ttu-id="9735a-166">Der folgende Code zeigt das **AssociationEnd** -Element, das verwendet wird, um die **DepartmentID** -Eigenschaft der **FK- \_ Kurs \_ Abteilungs** Zuordnung der gespeicherten Prozedur **updatecourse** zuzuordnen (in der die Update-Funktion des **Course** -Entitäts Typs zugeordnet ist):</span><span class="sxs-lookup"><span data-stu-id="9735a-166">The following code shows the **AssociationEnd** element used to map the **DepartmentID** property of the **FK\_Course\_Department** association to the **UpdateCourse** stored procedure (to which the update function of the **Course** entity type is mapped):</span></span>

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <ModificationFunctionMapping>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdateCourse">
         <AssociationEnd AssociationSet="FK_Course_Department"
                         From="Course" To="Department">
           <ScalarProperty Name="DepartmentID"
                           ParameterName="DepartmentID"
                           Version="Current" />
         </AssociationEnd>
         <ScalarProperty Name="Credits" ParameterName="Credits"
                         Version="Current" />
         <ScalarProperty Name="Title" ParameterName="Title"
                         Version="Current" />
         <ScalarProperty Name="CourseID" ParameterName="CourseID"
                         Version="Current" />
       </UpdateFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="associationsetmapping-element-msl"></a><span data-ttu-id="9735a-167">AssociationSetMapping-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-167">AssociationSetMapping Element (MSL)</span></span>

<span data-ttu-id="9735a-168">Das **AssociationSetMapping** -Element in der Mapping-Spezifikationssprache (Mapping Specification Language, MSL) definiert die Zuordnung zwischen einer Zuordnung im konzeptionellen Modell und den Tabellen Spalten in der zugrunde liegenden Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9735a-168">The **AssociationSetMapping** element in mapping specification language (MSL) defines the mapping between an association in the conceptual model and table columns in the underlying database.</span></span>

<span data-ttu-id="9735a-169">Zuordnungen im konzeptionellen Modell sind Typen, deren Eigenschaften Primär- und Fremdschlüsselspalten in der zugrunde liegenden Datenbank darstellen.</span><span class="sxs-lookup"><span data-stu-id="9735a-169">Associations in the conceptual model are types whose properties represent primary and foreign key columns in the underlying database.</span></span> <span data-ttu-id="9735a-170">Das **AssociationSetMapping** -Element verwendet zwei EndProperty-Elemente, um die Zuordnungen zwischen Zuordnungstyp Eigenschaften und Spalten in der Datenbank zu definieren.</span><span class="sxs-lookup"><span data-stu-id="9735a-170">The **AssociationSetMapping** element uses two EndProperty elements to define the mappings between association type properties and columns in the database.</span></span> <span data-ttu-id="9735a-171">Sie können mit dem Condition-Element Bedingungen für diese Zuordnungen festlegen.</span><span class="sxs-lookup"><span data-stu-id="9735a-171">You can place conditions on these mappings with the Condition element.</span></span> <span data-ttu-id="9735a-172">Mit dem ModificationFunctionMapping-Element ordnen Sie Einfüge-, Update- und Löschfunktionen für Zuordnungen gespeicherten Prozeduren in der Datenbank zu.</span><span class="sxs-lookup"><span data-stu-id="9735a-172">Map the insert, update, and delete functions for associations to stored procedures in the database with the ModificationFunctionMapping element.</span></span> <span data-ttu-id="9735a-173">Definieren Sie schreibgeschützte Zuordnungen zwischen Zuordnungen und Tabellen Spalten, indem Sie eine Entity SQL Zeichenfolge in einem QueryView-Element verwenden.</span><span class="sxs-lookup"><span data-stu-id="9735a-173">Define read-only mappings between associations and table columns by using an Entity SQL string in a QueryView element.</span></span>

> [!NOTE]
> <span data-ttu-id="9735a-174">Wenn eine referenzielle Einschränkung für eine Zuordnung im konzeptionellen Modell definiert ist, muss die Zuordnung nicht mit einem **AssociationSetMapping** -Element zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-174">If a referential constraint is defined for an association in the conceptual model, the association does not need to be mapped with an **AssociationSetMapping** element.</span></span> <span data-ttu-id="9735a-175">Wenn ein **AssociationSetMapping** -Element für eine Zuordnung vorhanden ist, die eine referenzielle Einschränkung aufweist, werden die im **AssociationSetMapping** -Element definierten Zuordnungen ignoriert.</span><span class="sxs-lookup"><span data-stu-id="9735a-175">If an **AssociationSetMapping** element is present for an association that has a referential constraint, the mappings defined in the **AssociationSetMapping** element will be ignored.</span></span> <span data-ttu-id="9735a-176">Weitere Informationen finden Sie unter referentialeinschränkungs-Element (CSDL).</span><span class="sxs-lookup"><span data-stu-id="9735a-176">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="9735a-177">Das **AssociationSetMapping** -Element kann die folgenden untergeordneten Elemente aufweisen.</span><span class="sxs-lookup"><span data-stu-id="9735a-177">The **AssociationSetMapping** element can have the following child elements</span></span>

-   <span data-ttu-id="9735a-178">QueryView (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="9735a-178">QueryView (zero or one)</span></span>
-   <span data-ttu-id="9735a-179">EndProperty (kein (null) oder zwei Elemente)</span><span class="sxs-lookup"><span data-stu-id="9735a-179">EndProperty (zero or two)</span></span>
-   <span data-ttu-id="9735a-180">Condition (0 (null) oder mehr Elemente)</span><span class="sxs-lookup"><span data-stu-id="9735a-180">Condition (zero or more)</span></span>
-   <span data-ttu-id="9735a-181">ModificationFunctionMapping (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="9735a-181">ModificationFunctionMapping (zero or one)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-182">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-182">Applicable Attributes</span></span>

<span data-ttu-id="9735a-183">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **AssociationSetMapping** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="9735a-183">The following table describes the attributes that can be applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="9735a-184">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-184">Attribute Name</span></span>     | <span data-ttu-id="9735a-185">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-185">Is Required</span></span> | <span data-ttu-id="9735a-186">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-186">Value</span></span>                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-187">**Name**</span><span class="sxs-lookup"><span data-stu-id="9735a-187">**Name**</span></span>           | <span data-ttu-id="9735a-188">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-188">Yes</span></span>         | <span data-ttu-id="9735a-189">Der Name des konzeptionellen Modell-Zuordnungssatzes, der zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-189">The name of the conceptual model association set that is being mapped.</span></span>                      |
| <span data-ttu-id="9735a-190">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="9735a-190">**TypeName**</span></span>       | <span data-ttu-id="9735a-191">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-191">No</span></span>          | <span data-ttu-id="9735a-192">Der mit einem Namespace qualifizierte Name des konzeptionellen Modell-Zuordnungstyps, der zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-192">The namespace-qualified name of the conceptual model association type that is being mapped.</span></span> |
| <span data-ttu-id="9735a-193">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="9735a-193">**StoreEntitySet**</span></span> | <span data-ttu-id="9735a-194">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-194">No</span></span>          | <span data-ttu-id="9735a-195">Der Name der Tabelle, die zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-195">The name of the table that is being mapped.</span></span>                                                 |

### <a name="example"></a><span data-ttu-id="9735a-196">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-196">Example</span></span>

<span data-ttu-id="9735a-197">Das folgende Beispiel zeigt ein **AssociationSetMapping** -Element, in dem der im konzeptionellen Modell festgelegte **FK- \_ Kurs \_ Abteilungs** Verbund der **Course** -Tabelle in der-Datenbank zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-197">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association set in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="9735a-198">Zuordnungen zwischen Zuordnungstyp Eigenschaften und Tabellen Spalten werden in untergeordneten **EndProperty** -Elementen angegeben.</span><span class="sxs-lookup"><span data-stu-id="9735a-198">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
 </AssociationSetMapping>
```

## <a name="complexproperty-element-msl"></a><span data-ttu-id="9735a-199">ComplexProperty-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-199">ComplexProperty Element (MSL)</span></span>

<span data-ttu-id="9735a-200">Ein **ComplexProperty** -Element in der Mapping-Spezifikationssprache (MSL) definiert die Zuordnung zwischen einer komplexen Typeigenschaft in einem Entitätstyp des konzeptionellen Modells und Tabellen Spalten in der zugrunde liegenden Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9735a-200">A **ComplexProperty** element in mapping specification language (MSL) defines the mapping between a complex type property on a conceptual model entity type and table columns in the underlying database.</span></span> <span data-ttu-id="9735a-201">Die Zuordnungen zu Eigenschaftenspalten sind in einem untergeordneten ScalarProperty-Element angegeben.</span><span class="sxs-lookup"><span data-stu-id="9735a-201">The property-column mappings are specified in child ScalarProperty elements.</span></span>

<span data-ttu-id="9735a-202">Das **complexType** -Eigenschafts Element kann die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="9735a-202">The **ComplexType** property element can have the following child elements:</span></span>

-   <span data-ttu-id="9735a-203">ScalarProperty (0 (null) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-203">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="9735a-204">**ComplexProperty** (0 (null) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-204">**ComplexProperty** (zero or more)</span></span>
-   <span data-ttu-id="9735a-205">ComplextTypeMapping (kein (null) oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="9735a-205">ComplextTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="9735a-206">Condition (0 (null) oder mehr Elemente)</span><span class="sxs-lookup"><span data-stu-id="9735a-206">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-207">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-207">Applicable Attributes</span></span>

<span data-ttu-id="9735a-208">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **ComplexProperty** -Element anwendbar sind:</span><span class="sxs-lookup"><span data-stu-id="9735a-208">The following table describes the attributes that are applicable to the **ComplexProperty** element:</span></span>

| <span data-ttu-id="9735a-209">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-209">Attribute Name</span></span> | <span data-ttu-id="9735a-210">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-210">Is Required</span></span> | <span data-ttu-id="9735a-211">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-211">Value</span></span>                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-212">**Name**</span><span class="sxs-lookup"><span data-stu-id="9735a-212">**Name**</span></span>       | <span data-ttu-id="9735a-213">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-213">Yes</span></span>         | <span data-ttu-id="9735a-214">Der Name der komplexen Eigenschaft eines Entitätstyps im konzeptionellen Modell, die zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-214">The name of the complex property of an entity type in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="9735a-215">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="9735a-215">**TypeName**</span></span>   | <span data-ttu-id="9735a-216">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-216">No</span></span>          | <span data-ttu-id="9735a-217">Der mit einem Namespace qualifizierte Name des Eigenschaftentyps im konzeptionellen Modell.</span><span class="sxs-lookup"><span data-stu-id="9735a-217">The namespace-qualified name of the conceptual model property type.</span></span>                              |

### <a name="example"></a><span data-ttu-id="9735a-218">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-218">Example</span></span>

<span data-ttu-id="9735a-219">Das folgende Beispiel beruht auf dem School-Modell.</span><span class="sxs-lookup"><span data-stu-id="9735a-219">The following example is based on the School model.</span></span> <span data-ttu-id="9735a-220">Dem konzeptionellen Modell wurde der folgende komplexe Typ hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="9735a-220">The following complex type has been added to the conceptual model:</span></span>

``` xml
 <ComplexType Name="FullName">
   <Property Type="String" Name="LastName"
             Nullable="false" MaxLength="50"
             FixedLength="false" Unicode="true" />
   <Property Type="String" Name="FirstName"
             Nullable="false" MaxLength="50"
             FixedLength="false" Unicode="true" />
 </ComplexType>
```

<span data-ttu-id="9735a-221">Die Eigenschaften " **LastName** " und " **FirstName** " des Entitäts Typs " **Person** " wurden durch eine komplexe Eigenschaft **namens "Name**" ersetzt:</span><span class="sxs-lookup"><span data-stu-id="9735a-221">The **LastName** and **FirstName** properties of the **Person** entity type have been replaced with one complex property, **Name**:</span></span>

``` xml
 <EntityType Name="Person">
   <Key>
     <PropertyRef Name="PersonID" />
   </Key>
   <Property Name="PersonID" Type="Int32" Nullable="false"
             annotation:StoreGeneratedPattern="Identity" />
   <Property Name="HireDate" Type="DateTime" />
   <Property Name="EnrollmentDate" Type="DateTime" />
   <Property Name="Name" Type="SchoolModel.FullName" Nullable="false" />
 </EntityType>
```

<span data-ttu-id="9735a-222">Das folgende MSL zeigt das **ComplexProperty** -Element, das verwendet wird, um die **Name** -Eigenschaft Spalten in der zugrunde liegenden Datenbank zuzuordnen:</span><span class="sxs-lookup"><span data-stu-id="9735a-222">The following MSL shows the **ComplexProperty** element used to map the **Name** property to columns in the underlying database:</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate" ColumnName="EnrollmentDate" />
       <ComplexProperty Name="Name" TypeName="SchoolModel.FullName">
         <ScalarProperty Name="FirstName" ColumnName="FirstName" />
         <ScalarProperty Name="LastName" ColumnName="LastName" />  
       </ComplexProperty>
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="complextypemapping-element-msl"></a><span data-ttu-id="9735a-223">ComplexTypeMapping-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-223">ComplexTypeMapping Element (MSL)</span></span>

<span data-ttu-id="9735a-224">Das **complextypemapping** -Element in der Mapping-Spezifikationssprache (MSL) ist ein untergeordnetes Element des resultmapping-Elements und definiert die Zuordnung zwischen einem Funktions Import im konzeptionellen Modell und einer gespeicherten Prozedur in der zugrunde liegenden Datenbank, wenn Folgendes zutrifft:</span><span class="sxs-lookup"><span data-stu-id="9735a-224">The **ComplexTypeMapping** element in mapping specification language (MSL) is a child of the ResultMapping element and defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="9735a-225">Der Funktionsimport gibt einen konzeptionellen komplexen Typ zurück.</span><span class="sxs-lookup"><span data-stu-id="9735a-225">The function import returns a conceptual complex type.</span></span>
-   <span data-ttu-id="9735a-226">Die Namen der Spalten, die von der gespeicherten Prozedur zurückgegeben werden, entsprechen nicht genau den Namen der Eigenschaften für den komplexen Typ.</span><span class="sxs-lookup"><span data-stu-id="9735a-226">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the complex type.</span></span>

<span data-ttu-id="9735a-227">Standardmäßig basiert die Zuordnung der von einer gespeicherten Prozedur zurückgegebenen Spalten zu einem komplexen Typ auf den Spalten- und Eigenschaftennamen.</span><span class="sxs-lookup"><span data-stu-id="9735a-227">By default, the mapping between the columns returned by a stored procedure and a complex type is based on column and property names.</span></span> <span data-ttu-id="9735a-228">Wenn Spaltennamen nicht exakt mit Eigenschaftsnamen übereinstimmen, müssen Sie das **complextypemapping** -Element verwenden, um die Zuordnung zu definieren.</span><span class="sxs-lookup"><span data-stu-id="9735a-228">If column names do not exactly match property names, you must use the **ComplexTypeMapping** element to define the mapping.</span></span> <span data-ttu-id="9735a-229">Ein Beispiel für die Standard Zuordnung finden Sie unter FunctionImportMapping-Element (MSL).</span><span class="sxs-lookup"><span data-stu-id="9735a-229">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="9735a-230">Das **complextypemapping** -Element kann die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="9735a-230">The **ComplexTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="9735a-231">ScalarProperty (0 (null) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-231">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-232">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-232">Applicable Attributes</span></span>

<span data-ttu-id="9735a-233">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **complextypemapping** -Element anwendbar sind.</span><span class="sxs-lookup"><span data-stu-id="9735a-233">The following table describes the attributes that are applicable to the **ComplexTypeMapping** element.</span></span>

| <span data-ttu-id="9735a-234">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-234">Attribute Name</span></span> | <span data-ttu-id="9735a-235">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-235">Is Required</span></span> | <span data-ttu-id="9735a-236">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-236">Value</span></span>                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| <span data-ttu-id="9735a-237">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="9735a-237">**TypeName**</span></span>   | <span data-ttu-id="9735a-238">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-238">Yes</span></span>         | <span data-ttu-id="9735a-239">Der namespacequalifizierte Name des komplexen Typs, der zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-239">The namespace-qualified name of the complex type that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="9735a-240">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-240">Example</span></span>

<span data-ttu-id="9735a-241">Sehen Sie sich die folgende gespeicherte Prozedur an:</span><span class="sxs-lookup"><span data-stu-id="9735a-241">Consider the following stored procedure:</span></span>

``` SQL
 CREATE PROCEDURE [dbo].[GetGrades]
             @student_Id int
             AS
             SELECT     EnrollmentID as enroll_id,
                                                                             Grade as grade,
                                                                             CourseID as course_id,
                                                                             StudentID as student_id
                                               FROM dbo.StudentGrade
             WHERE StudentID = @student_Id
```

<span data-ttu-id="9735a-242">Betrachten Sie auch den folgenden komplexen Typ des konzeptionellen Modells:</span><span class="sxs-lookup"><span data-stu-id="9735a-242">Also consider the following conceptual model complex type:</span></span>

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

<span data-ttu-id="9735a-243">Um einen Funktions Import zu erstellen, der Instanzen des vorherigen komplexen Typs zurückgibt, muss die Zuordnung zwischen den Spalten, die von der gespeicherten Prozedur zurückgegeben werden, und dem Entitätstyp in einem **complextypemapping** -Element definiert werden:</span><span class="sxs-lookup"><span data-stu-id="9735a-243">In order to create a function import that returns instances of the previous complex type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ComplexTypeMapping** element:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetGrades"
                        FunctionName="SchoolModel.Store.GetGrades" >
   <ResultMapping>
     <ComplexTypeMapping TypeName="SchoolModel.GradeInfo">
       <ScalarProperty Name="EnrollmentID" ColumnName="enroll_id"/>
       <ScalarProperty Name="CourseID" ColumnName="course_id"/>
       <ScalarProperty Name="StudentID" ColumnName="student_id"/>
       <ScalarProperty Name="Grade" ColumnName="grade"/>
     </ComplexTypeMapping>
   </ResultMapping>
 </FunctionImportMapping>
```

## <a name="condition-element-msl"></a><span data-ttu-id="9735a-244">Condition-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-244">Condition Element (MSL)</span></span>

<span data-ttu-id="9735a-245">Das **Condition** -Element in der Mapping-Spezifikationssprache (Mapping Specification Language, MSL) ordnet Bedingungen für Zuordnungen zwischen dem konzeptionellen Modell und der zugrunde liegenden Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9735a-245">The **Condition** element in mapping specification language (MSL) places conditions on mappings between the conceptual model and the underlying database.</span></span> <span data-ttu-id="9735a-246">Die Zuordnung, die in einem XML-Knoten definiert ist, ist gültig, wenn alle Bedingungen, die in den untergeordneten **Bedingungs Elementen angegeben** sind, erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-246">The mapping that is defined within an XML node is valid if all conditions, as specified in child **Condition** elements, are met.</span></span> <span data-ttu-id="9735a-247">Andernfalls ist die Zuordnung ungültig.</span><span class="sxs-lookup"><span data-stu-id="9735a-247">Otherwise, the mapping is not valid.</span></span> <span data-ttu-id="9735a-248">Wenn ein MappingFragment-Element z. b. ein oder **mehrere unter** geordnete Bedingungs Elemente enthält, ist die im Knoten **MappingFragment** definierte Zuordnung nur gültig, wenn alle Bedingungen der untergeordneten **Bedingungs Elemente erfüllt** sind.</span><span class="sxs-lookup"><span data-stu-id="9735a-248">For example, if a MappingFragment element contains one or more **Condition** child elements, the mapping defined within the **MappingFragment** node will only be valid if all the conditions of the child **Condition** elements are met.</span></span>

<span data-ttu-id="9735a-249">Jede Bedingung kann entweder auf einen **Namen** (den Namen einer Entitäts Eigenschaft des konzeptionellen Modells, der durch das **Name** -Attribut festgelegt ist) oder auf einen **ColumnName** (der Name einer Spalte in der Datenbank, die durch das **ColumnName** -Attribut angegeben ist) angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-249">Each condition can apply to either a **Name** (the name of a conceptual model entity property, specified by the **Name** attribute), or a **ColumnName** (the name of a column in the database, specified by the **ColumnName** attribute).</span></span> <span data-ttu-id="9735a-250">Wenn das **Name** -Attribut festgelegt ist, wird die Bedingung anhand eines Entitäts Eigenschafts Werts überprüft.</span><span class="sxs-lookup"><span data-stu-id="9735a-250">When the **Name** attribute is set, the condition is checked against an entity property value.</span></span> <span data-ttu-id="9735a-251">Wenn das **ColumnName** -Attribut festgelegt ist, wird die Bedingung anhand eines Spaltenwerts überprüft.</span><span class="sxs-lookup"><span data-stu-id="9735a-251">When the **ColumnName** attribute is set, the condition is checked against a column value.</span></span> <span data-ttu-id="9735a-252">Nur eines der Attribute " **Name** " oder " **ColumnName** " kann in einem **Condition** -Element angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-252">Only one of the **Name** or **ColumnName** attribute can be specified in a **Condition** element.</span></span>

> [!NOTE]
> <span data-ttu-id="9735a-253">Wenn das **Condition** -Element innerhalb eines FunctionImportMapping-Elements verwendet wird, ist nur das **Name** -Attribut anwendbar.</span><span class="sxs-lookup"><span data-stu-id="9735a-253">When the **Condition** element is used within a FunctionImportMapping element, only the **Name** attribute is not applicable.</span></span>

<span data-ttu-id="9735a-254">Das **Condition** -Element kann ein untergeordnetes Element der folgenden Elemente sein:</span><span class="sxs-lookup"><span data-stu-id="9735a-254">The **Condition** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="9735a-255">AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="9735a-255">AssociationSetMapping</span></span>
-   <span data-ttu-id="9735a-256">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="9735a-256">ComplexProperty</span></span>
-   <span data-ttu-id="9735a-257">EntitySetMapping</span><span class="sxs-lookup"><span data-stu-id="9735a-257">EntitySetMapping</span></span>
-   <span data-ttu-id="9735a-258">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="9735a-258">MappingFragment</span></span>
-   <span data-ttu-id="9735a-259">EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="9735a-259">EntityTypeMapping</span></span>

<span data-ttu-id="9735a-260">Das **Condition** -Element kann keine untergeordneten Elemente aufweisen.</span><span class="sxs-lookup"><span data-stu-id="9735a-260">The **Condition** element can have no child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-261">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-261">Applicable Attributes</span></span>

<span data-ttu-id="9735a-262">In der folgenden Tabelle werden die Attribute beschrieben, die für das **Condition** -Element anwendbar sind:</span><span class="sxs-lookup"><span data-stu-id="9735a-262">The following table describes the attributes that are applicable to the **Condition** element:</span></span>

| <span data-ttu-id="9735a-263">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-263">Attribute Name</span></span> | <span data-ttu-id="9735a-264">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-264">Is Required</span></span> | <span data-ttu-id="9735a-265">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-265">Value</span></span>                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-266">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="9735a-266">**ColumnName**</span></span> | <span data-ttu-id="9735a-267">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-267">No</span></span>          | <span data-ttu-id="9735a-268">Der Name der Tabellenspalte, deren Wert zur Auswertung der Bedingung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-268">The name of the table column whose value is used to evaluate the condition.</span></span>                                                                                                                                                                                                                   |
| <span data-ttu-id="9735a-269">**IsNull**</span><span class="sxs-lookup"><span data-stu-id="9735a-269">**IsNull**</span></span>     | <span data-ttu-id="9735a-270">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-270">No</span></span>          | <span data-ttu-id="9735a-271">**True** und **False**.</span><span class="sxs-lookup"><span data-stu-id="9735a-271">**True** or **False**.</span></span> <span data-ttu-id="9735a-272">Wenn der Wert **true** und der Spaltenwert **null**ist, oder wenn der Wert **false** ist und der Spaltenwert nicht **null**ist, ist die Bedingung true.</span><span class="sxs-lookup"><span data-stu-id="9735a-272">If the value is **True** and the column value is **null**, or if the value is **False** and the column value is not **null**, the condition is true.</span></span> <span data-ttu-id="9735a-273">Andernfalls ist die Bedingung nicht erfüllt (false).</span><span class="sxs-lookup"><span data-stu-id="9735a-273">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="9735a-274">Die Attribute " **IsNull** " und " **value** " können nicht gleichzeitig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-274">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span> |
| <span data-ttu-id="9735a-275">**Wert**</span><span class="sxs-lookup"><span data-stu-id="9735a-275">**Value**</span></span>      | <span data-ttu-id="9735a-276">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-276">No</span></span>          | <span data-ttu-id="9735a-277">Der Wert, mit dem der Spaltenwert verglichen werden soll.</span><span class="sxs-lookup"><span data-stu-id="9735a-277">The value with which the column value is compared.</span></span> <span data-ttu-id="9735a-278">Wenn die Werte gleich sind, wird die Bedingung erfüllt (true).</span><span class="sxs-lookup"><span data-stu-id="9735a-278">If the values are the same, the condition is true.</span></span> <span data-ttu-id="9735a-279">Andernfalls ist die Bedingung nicht erfüllt (false).</span><span class="sxs-lookup"><span data-stu-id="9735a-279">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="9735a-280">Die Attribute " **IsNull** " und " **value** " können nicht gleichzeitig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-280">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span>                                                                       |
| <span data-ttu-id="9735a-281">**Name**</span><span class="sxs-lookup"><span data-stu-id="9735a-281">**Name**</span></span>       | <span data-ttu-id="9735a-282">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-282">No</span></span>          | <span data-ttu-id="9735a-283">Der Name der Entitätseigenschaft im konzeptionellen Modell, deren Wert zur Auswertung der Bedingung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-283">The name of the conceptual model entity property whose value is used to evaluate the condition.</span></span> <br/> <span data-ttu-id="9735a-284">Dieses Attribut ist nicht anwendbar, wenn das **Condition** -Element innerhalb eines FunctionImportMapping-Elements verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-284">This attribute is not applicable if the **Condition** element is used within a FunctionImportMapping element.</span></span>                                                                           |

### <a name="example"></a><span data-ttu-id="9735a-285">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-285">Example</span></span>

<span data-ttu-id="9735a-286">Das folgende **Beispiel zeigt Bedingungs** Elemente als untergeordnete Elemente der **MappingFragment** -Elemente.</span><span class="sxs-lookup"><span data-stu-id="9735a-286">The following example shows **Condition** elements as children of **MappingFragment** elements.</span></span> <span data-ttu-id="9735a-287">Wenn **HireDate** nicht NULL ist und " **registrimentdate** " den Wert NULL hat, werden Daten zwischen dem Typ " **SchoolModel. Instructor** " und den Spalten **PersonID** und **HireDate** der **Person** -Tabelle zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="9735a-287">When **HireDate** is not null and **EnrollmentDate** is null, data is mapped between the **SchoolModel.Instructor** type and the **PersonID** and **HireDate** columns of the **Person** table.</span></span> <span data-ttu-id="9735a-288">Wenn **registrimentdate** nicht NULL und **HireDate** den Wert NULL hat, werden Daten zwischen dem Typ **SchoolModel. Student** **und den Spalten** **PersonID** und Registrierung der **Person** -Tabelle zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="9735a-288">When **EnrollmentDate** is not null and **HireDate** is null, data is mapped between the **SchoolModel.Student** type and the **PersonID** and **Enrollment** columns of the **Person** table.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Person)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Instructor)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <Condition ColumnName="HireDate" IsNull="false" />
       <Condition ColumnName="EnrollmentDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Student)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
       <Condition ColumnName="EnrollmentDate" IsNull="false" />
       <Condition ColumnName="HireDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="deletefunction-element-msl"></a><span data-ttu-id="9735a-289">DeleteFunction-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-289">DeleteFunction Element (MSL)</span></span>

<span data-ttu-id="9735a-290">Das **DeleteFunction** -Element in der Mapping-Spezifikationssprache (Mapping Specification Language, MSL) ordnet die Delete-Funktion eines Entitäts Typs oder einer Zuordnung im konzeptionellen Modell einer gespeicherten Prozedur in der zugrunde liegenden Datenbank zu.</span><span class="sxs-lookup"><span data-stu-id="9735a-290">The **DeleteFunction** element in mapping specification language (MSL) maps the delete function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="9735a-291">Gespeicherte Prozeduren, denen Änderungsfunktionen zugeordnet werden, müssen im Speichermodell deklariert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-291">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="9735a-292">Weitere Informationen finden Sie unter Function-Element (SSDL).</span><span class="sxs-lookup"><span data-stu-id="9735a-292">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="9735a-293">Wenn Sie nicht alle drei Einfüge-, Aktualisierungs-oder Löschvorgänge eines Entitäts Typs gespeicherten Prozeduren zuordnen, schlagen die nicht zugeordneten Vorgänge fehl, wenn Sie zur Laufzeit ausgeführt werden und eine Update Exception ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-293">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

### <a name="deletefunction-applied-to-entitytypemapping"></a><span data-ttu-id="9735a-294">DeleteFunction angewendet auf EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="9735a-294">DeleteFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="9735a-295">Wenn das **DeleteFunction** -Element auf das EntityTypeMapping-Element angewendet wird, ordnet es die Delete-Funktion eines Entitäts Typs im konzeptionellen Modell einer gespeicherten Prozedur zu.</span><span class="sxs-lookup"><span data-stu-id="9735a-295">When applied to the EntityTypeMapping element, the **DeleteFunction** element maps the delete function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="9735a-296">Das **DeleteFunction** -Element kann die folgenden untergeordneten Elemente aufweisen, wenn es auf ein **EntityTypeMapping** -Element angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="9735a-296">The **DeleteFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="9735a-297">AssociationEnd (null (0) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-297">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="9735a-298">ComplexProperty (0 (null) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-298">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="9735a-299">ScalarProperty (null (0) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-299">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="9735a-300">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-300">Applicable Attributes</span></span>

<span data-ttu-id="9735a-301">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **DeleteFunction** -Element angewendet werden können, wenn es auf ein **EntityTypeMapping** -Element angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-301">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="9735a-302">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-302">Attribute Name</span></span>            | <span data-ttu-id="9735a-303">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-303">Is Required</span></span> | <span data-ttu-id="9735a-304">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-304">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-305">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="9735a-305">**FunctionName**</span></span>          | <span data-ttu-id="9735a-306">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-306">Yes</span></span>         | <span data-ttu-id="9735a-307">Der mit einem Namespace qualifizierte Name der gespeicherten Prozedur, der die Löschfunktion zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-307">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="9735a-308">Die gespeicherte Prozedur muss im Speichermodell deklariert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-308">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="9735a-309">**Rowsaffectedparameter**</span><span class="sxs-lookup"><span data-stu-id="9735a-309">**RowsAffectedParameter**</span></span> | <span data-ttu-id="9735a-310">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-310">No</span></span>          | <span data-ttu-id="9735a-311">Der Name des Ausgabeparameters, der die Anzahl der betroffenen Zeilen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="9735a-311">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="9735a-312">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-312">Example</span></span>

<span data-ttu-id="9735a-313">Das folgende Beispiel basiert auf dem Modell "School" und zeigt das **DeleteFunction** -Element, das die Delete-Funktion des Entitäts Typs **Person** der gespeicherten Prozedur **DeletePerson** entspricht.</span><span class="sxs-lookup"><span data-stu-id="9735a-313">The following example is based on the School model and shows the **DeleteFunction** element mapping the delete function of the **Person** entity type to the **DeletePerson** stored procedure.</span></span> <span data-ttu-id="9735a-314">Die gespeicherte Prozedur **DeletePerson** wird im Speichermodell deklariert.</span><span class="sxs-lookup"><span data-stu-id="9735a-314">The **DeletePerson** stored procedure is declared in the storage model.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="deletefunction-applied-to-associationsetmapping"></a><span data-ttu-id="9735a-315">DeleteFunction angewendet auf AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="9735a-315">DeleteFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="9735a-316">Wenn das **DeleteFunction** -Element auf das AssociationSetMapping-Element angewendet wird, ordnet es die Delete-Funktion einer Zuordnung im konzeptionellen Modell einer gespeicherten Prozedur zu.</span><span class="sxs-lookup"><span data-stu-id="9735a-316">When applied to the AssociationSetMapping element, the **DeleteFunction** element maps the delete function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="9735a-317">Das **DeleteFunction** -Element kann die folgenden untergeordneten Elemente aufweisen, wenn es auf das **AssociationSetMapping** -Element angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="9735a-317">The **DeleteFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="9735a-318">EndProperty</span><span class="sxs-lookup"><span data-stu-id="9735a-318">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="9735a-319">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-319">Applicable Attributes</span></span>

<span data-ttu-id="9735a-320">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **DeleteFunction** -Element angewendet werden können, wenn es auf das **AssociationSetMapping** -Element angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-320">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="9735a-321">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-321">Attribute Name</span></span>            | <span data-ttu-id="9735a-322">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-322">Is Required</span></span> | <span data-ttu-id="9735a-323">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-323">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-324">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="9735a-324">**FunctionName**</span></span>          | <span data-ttu-id="9735a-325">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-325">Yes</span></span>         | <span data-ttu-id="9735a-326">Der mit einem Namespace qualifizierte Name der gespeicherten Prozedur, der die Löschfunktion zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-326">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="9735a-327">Die gespeicherte Prozedur muss im Speichermodell deklariert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-327">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="9735a-328">**Rowsaffectedparameter**</span><span class="sxs-lookup"><span data-stu-id="9735a-328">**RowsAffectedParameter**</span></span> | <span data-ttu-id="9735a-329">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-329">No</span></span>          | <span data-ttu-id="9735a-330">Der Name des Ausgabeparameters, der die Anzahl der betroffenen Zeilen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="9735a-330">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="9735a-331">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-331">Example</span></span>

<span data-ttu-id="9735a-332">Das folgende Beispiel basiert auf dem Modell "School" und zeigt das **DeleteFunction** -Element, das verwendet wird, um die Delete-Funktion der " **CourseInstructor** "-Zuordnung der gespeicherten Prozedur " **deletecourseinstructor** " zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="9735a-332">The following example is based on the School model and shows the **DeleteFunction** element used to map delete function of the **CourseInstructor** association to the **DeleteCourseInstructor** stored procedure.</span></span> <span data-ttu-id="9735a-333">Die gespeicherte Prozedur **deletecourseinstructor** ist im Speichermodell deklariert.</span><span class="sxs-lookup"><span data-stu-id="9735a-333">The **DeleteCourseInstructor** stored procedure is declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="endproperty-element-msl"></a><span data-ttu-id="9735a-334">EndProperty-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-334">EndProperty Element (MSL)</span></span>

<span data-ttu-id="9735a-335">Das **EndProperty** -Element in der Mapping-Spezifikationssprache (Mapping Specification Language, MSL) definiert die Zuordnung zwischen einer End-oder einer Änderungs Funktion einer konzeptionellen Modell Zuordnung und der zugrunde liegenden Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9735a-335">The **EndProperty** element in mapping specification language (MSL) defines the mapping between an end or a modification function of a conceptual model association and the underlying database.</span></span> <span data-ttu-id="9735a-336">Die Zuordnung zur Eigenschaftenspalte ist in einem untergeordneten ScalarProperty-Element angegeben.</span><span class="sxs-lookup"><span data-stu-id="9735a-336">The property-column mapping is specified in a child ScalarProperty element.</span></span>

<span data-ttu-id="9735a-337">Wenn ein **EndProperty** -Element verwendet wird, um die Zuordnung für das Ende einer konzeptionellen Modell Zuordnung zu definieren, ist es ein untergeordnetes Element eines AssociationSetMapping-Elements.</span><span class="sxs-lookup"><span data-stu-id="9735a-337">When an **EndProperty** element is used to define the mapping for the end of a conceptual model association, it is a child of an AssociationSetMapping element.</span></span> <span data-ttu-id="9735a-338">Wenn das **EndProperty** -Element verwendet wird, um die Zuordnung für eine Änderungs Funktion einer konzeptionellen Modell Zuordnung zu definieren, ist es ein untergeordnetes Element eines InsertFunction-Elements oder eines DeleteFunction-Elements.</span><span class="sxs-lookup"><span data-stu-id="9735a-338">When the **EndProperty** element is used to define the mapping for a modification function of a conceptual model association, it is a child of an InsertFunction element or DeleteFunction element.</span></span>

<span data-ttu-id="9735a-339">Das **EndProperty** -Element kann die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="9735a-339">The **EndProperty** element can have the following child elements:</span></span>

-   <span data-ttu-id="9735a-340">ScalarProperty (0 (null) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-340">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-341">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-341">Applicable Attributes</span></span>

<span data-ttu-id="9735a-342">In der folgenden Tabelle werden die Attribute beschrieben, die für das **EndProperty** -Element anwendbar sind:</span><span class="sxs-lookup"><span data-stu-id="9735a-342">The following table describes the attributes that are applicable to the **EndProperty** element:</span></span>

| <span data-ttu-id="9735a-343">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-343">Attribute Name</span></span> | <span data-ttu-id="9735a-344">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-344">Is Required</span></span> | <span data-ttu-id="9735a-345">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-345">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="9735a-346">Name</span><span class="sxs-lookup"><span data-stu-id="9735a-346">Name</span></span>           | <span data-ttu-id="9735a-347">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-347">Yes</span></span>         | <span data-ttu-id="9735a-348">Der Name des Zuordnungsendes, das zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-348">The name of the association end that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="9735a-349">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-349">Example</span></span>

<span data-ttu-id="9735a-350">Das folgende Beispiel zeigt ein **AssociationSetMapping** -Element, in dem die Zuordnung der **FK- \_ Kurs \_ Abteilung** im konzeptionellen Modell der **Course** -Tabelle in der-Datenbank zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="9735a-350">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="9735a-351">Zuordnungen zwischen Zuordnungstyp Eigenschaften und Tabellen Spalten werden in untergeordneten **EndProperty** -Elementen angegeben.</span><span class="sxs-lookup"><span data-stu-id="9735a-351">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
 </AssociationSetMapping>
```

### <a name="example"></a><span data-ttu-id="9735a-352">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-352">Example</span></span>

<span data-ttu-id="9735a-353">Das folgende Beispiel zeigt das **EndProperty** -Element, das die INSERT-und DELETE-Funktionen einer Association (**CourseInstructor**) zu gespeicherten Prozeduren in der zugrunde liegenden Datenbank zuordnet.</span><span class="sxs-lookup"><span data-stu-id="9735a-353">The following example shows the **EndProperty** element mapping the insert and delete functions of an association (**CourseInstructor**) to stored procedures in the underlying database.</span></span> <span data-ttu-id="9735a-354">Die Funktionen, denen sie zugeordnet werden, sind im Speichermodell deklariert.</span><span class="sxs-lookup"><span data-stu-id="9735a-354">The functions that are mapped to are declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="entitycontainermapping-element-msl"></a><span data-ttu-id="9735a-355">EntityContainerMapping-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-355">EntityContainerMapping Element (MSL)</span></span>

<span data-ttu-id="9735a-356">Das **EntityContainerMapping** -Element in der Mapping-Spezifikationssprache (Mapping Specification Language, MSL) ordnet den Entitäts Container im konzeptionellen Modell dem Entitäts Container im Speichermodell zu.</span><span class="sxs-lookup"><span data-stu-id="9735a-356">The **EntityContainerMapping** element in mapping specification language (MSL) maps the entity container in the conceptual model to the entity container in the storage model.</span></span> <span data-ttu-id="9735a-357">Das **EntityContainerMapping** -Element ist ein untergeordnetes Element des Mapping-Elements.</span><span class="sxs-lookup"><span data-stu-id="9735a-357">The **EntityContainerMapping** element is a child of the Mapping element.</span></span>

<span data-ttu-id="9735a-358">Das **EntityContainerMapping** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):</span><span class="sxs-lookup"><span data-stu-id="9735a-358">The **EntityContainerMapping** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9735a-359">EntitySetMapping (0 (null) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-359">EntitySetMapping (zero or more)</span></span>
-   <span data-ttu-id="9735a-360">AssociationSetMapping (0 (null) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-360">AssociationSetMapping (zero or more)</span></span>
-   <span data-ttu-id="9735a-361">FunctionImportMapping (0 (null) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-361">FunctionImportMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-362">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-362">Applicable Attributes</span></span>

<span data-ttu-id="9735a-363">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **EntityContainerMapping** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="9735a-363">The following table describes the attributes that can be applied to the **EntityContainerMapping** element.</span></span>

| <span data-ttu-id="9735a-364">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-364">Attribute Name</span></span>            | <span data-ttu-id="9735a-365">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-365">Is Required</span></span> | <span data-ttu-id="9735a-366">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-366">Value</span></span>                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-367">**Storagemodelcontainer**</span><span class="sxs-lookup"><span data-stu-id="9735a-367">**StorageModelContainer**</span></span> | <span data-ttu-id="9735a-368">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-368">Yes</span></span>         | <span data-ttu-id="9735a-369">Der Name des Entitätscontainers im Speichermodell, der zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-369">The name of the storage model entity container that is being mapped.</span></span>                                                                                                                                                                                     |
| <span data-ttu-id="9735a-370">**CdmEntityContainer**</span><span class="sxs-lookup"><span data-stu-id="9735a-370">**CdmEntityContainer**</span></span>    | <span data-ttu-id="9735a-371">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-371">Yes</span></span>         | <span data-ttu-id="9735a-372">Der Name des Entitätscontainers im konzeptionellen Modell, der zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-372">The name of the conceptual model entity container that is being mapped.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="9735a-373">**Generateupdateviews**</span><span class="sxs-lookup"><span data-stu-id="9735a-373">**GenerateUpdateViews**</span></span>   | <span data-ttu-id="9735a-374">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-374">No</span></span>          | <span data-ttu-id="9735a-375">**True** und **False**.</span><span class="sxs-lookup"><span data-stu-id="9735a-375">**True** or **False**.</span></span> <span data-ttu-id="9735a-376">**False**gibt an, dass keine Update Sichten generiert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-376">If **False**, no update views are generated.</span></span> <span data-ttu-id="9735a-377">Dieses Attribut sollte auf **false** festgelegt werden, wenn Sie eine schreibgeschützte Zuordnung haben, die ungültig wäre, da die Daten möglicherweise nicht erfolgreich abgerundet werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-377">This attribute should be set to **False** when you have a read-only mapping that would be invalid because data may not round-trip successfully.</span></span> <br/> <span data-ttu-id="9735a-378">Der Standardwert ist **True**.</span><span class="sxs-lookup"><span data-stu-id="9735a-378">The default value is **True**.</span></span> |

### <a name="example"></a><span data-ttu-id="9735a-379">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-379">Example</span></span>

<span data-ttu-id="9735a-380">Das folgende Beispiel zeigt ein **EntityContainerMapping** -Element, das den Container **schoolmodelentities** (der Entitäts Container des konzeptionellen Modells) dem Container **schoolmodelstorecontainer** (dem Entitätencontainer des Speicher Modells) zuordnet:</span><span class="sxs-lookup"><span data-stu-id="9735a-380">The following example shows an **EntityContainerMapping** element that maps the **SchoolModelEntities** container (the conceptual model entity container) to the **SchoolModelStoreContainer** container (the storage model entity container):</span></span>

``` xml
 <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                         CdmEntityContainer="SchoolModelEntities">
   <EntitySetMapping Name="Courses">
     <EntityTypeMapping TypeName="c.Course">
       <MappingFragment StoreEntitySet="Course">
         <ScalarProperty Name="CourseID" ColumnName="CourseID" />
         <ScalarProperty Name="Title" ColumnName="Title" />
         <ScalarProperty Name="Credits" ColumnName="Credits" />
         <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
       </MappingFragment>
     </EntityTypeMapping>
   </EntitySetMapping>
   <EntitySetMapping Name="Departments">
     <EntityTypeMapping TypeName="c.Department">
       <MappingFragment StoreEntitySet="Department">
         <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         <ScalarProperty Name="Name" ColumnName="Name" />
         <ScalarProperty Name="Budget" ColumnName="Budget" />
         <ScalarProperty Name="StartDate" ColumnName="StartDate" />
         <ScalarProperty Name="Administrator" ColumnName="Administrator" />
       </MappingFragment>
     </EntityTypeMapping>
   </EntitySetMapping>
 </EntityContainerMapping>
```

## <a name="entitysetmapping-element-msl"></a><span data-ttu-id="9735a-381">EntitySetMapping-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-381">EntitySetMapping Element (MSL)</span></span>

<span data-ttu-id="9735a-382">Das **EntitySetMapping** -Element in der Mapping-Spezifikationssprache (MSL) ordnet alle Typen in einer Entitätenmenge eines konzeptionellen Modells Entitätenmengen im Speichermodell zu.</span><span class="sxs-lookup"><span data-stu-id="9735a-382">The **EntitySetMapping** element in mapping specification language (MSL) maps all types in a conceptual model entity set to entity sets in the storage model.</span></span> <span data-ttu-id="9735a-383">Eine Entitätenmenge im konzeptionellen Modell ist ein logischer Container für Instanzen der Entitäten des gleichen Typs (und abgeleiteter Typen).</span><span class="sxs-lookup"><span data-stu-id="9735a-383">An entity set in the conceptual model is a logical container for instances of entities of the same type (and derived types).</span></span> <span data-ttu-id="9735a-384">Eine Entitätenmenge im Speichermodell stellt eine Tabelle oder eine Ansicht in der zugrunde liegenden Datenbank dar.</span><span class="sxs-lookup"><span data-stu-id="9735a-384">An entity set in the storage model represents a table or view in the underlying database.</span></span> <span data-ttu-id="9735a-385">Die Entitätenmenge des konzeptionellen Modells wird durch den Wert des **Name** -Attributs des **EntitySetMapping** -Elements angegeben.</span><span class="sxs-lookup"><span data-stu-id="9735a-385">The conceptual model entity set is specified by the value of the **Name** attribute of the **EntitySetMapping** element.</span></span> <span data-ttu-id="9735a-386">Die Tabelle oder Sicht, die zugeordnet ist, wird durch das **StoreEntitySet** -Attribut in jedem untergeordneten MappingFragment-Element oder im **EntitySetMapping** -Element selbst angegeben.</span><span class="sxs-lookup"><span data-stu-id="9735a-386">The mapped-to table or view is specified by the **StoreEntitySet** attribute in each child MappingFragment element or in the **EntitySetMapping** element itself.</span></span>

<span data-ttu-id="9735a-387">Das **EntitySetMapping** -Element kann die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="9735a-387">The **EntitySetMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="9735a-388">EntityTypeMapping (0 (null) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-388">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="9735a-389">QueryView (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="9735a-389">QueryView (zero or one)</span></span>
-   <span data-ttu-id="9735a-390">MappingFragment (null oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-390">MappingFragment (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-391">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-391">Applicable Attributes</span></span>

<span data-ttu-id="9735a-392">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **EntitySetMapping** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="9735a-392">The following table describes the attributes that can be applied to the **EntitySetMapping** element.</span></span>

| <span data-ttu-id="9735a-393">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-393">Attribute Name</span></span>           | <span data-ttu-id="9735a-394">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-394">Is Required</span></span> | <span data-ttu-id="9735a-395">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-395">Value</span></span>                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-396">**Name**</span><span class="sxs-lookup"><span data-stu-id="9735a-396">**Name**</span></span>                 | <span data-ttu-id="9735a-397">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-397">Yes</span></span>         | <span data-ttu-id="9735a-398">Der Name der Entitätenmenge im konzeptionellen Modell, die zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-398">The name of the conceptual model entity set that is being mapped.</span></span>                                                                                                                                                             |
| <span data-ttu-id="9735a-399">**Typname** **1**</span><span class="sxs-lookup"><span data-stu-id="9735a-399">**TypeName** **1**</span></span>       | <span data-ttu-id="9735a-400">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-400">No</span></span>          | <span data-ttu-id="9735a-401">Der Name des Entitätstyp im konzeptionellen Modell, der zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-401">The name of the conceptual model entity type that is being mapped.</span></span>                                                                                                                                                            |
| <span data-ttu-id="9735a-402">**StoreEntitySet** **1**</span><span class="sxs-lookup"><span data-stu-id="9735a-402">**StoreEntitySet** **1**</span></span> | <span data-ttu-id="9735a-403">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-403">No</span></span>          | <span data-ttu-id="9735a-404">Der Name der Entitätenmenge im Speichermodell, die zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-404">The name of the storage model entity set that is being mapped to.</span></span>                                                                                                                                                             |
| <span data-ttu-id="9735a-405">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="9735a-405">**MakeColumnsDistinct**</span></span>  | <span data-ttu-id="9735a-406">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-406">No</span></span>          | <span data-ttu-id="9735a-407">**True** oder **false** , abhängig davon, ob nur unterschiedliche Zeilen zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-407">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="9735a-408">Wenn dieses Attribut auf **true**festgelegt ist, muss das **generateupdateviews** -Attribut des EntityContainerMapping-Elements auf **false**festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-408">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

 

<span data-ttu-id="9735a-409">**1** das **tykame** -Attribut und das **StoreEntitySet** -Attribut können anstelle der untergeordneten EntityTypeMapping-und MappingFragment-Elemente verwendet werden, um einen einzelnen Entitätstyp einer einzelnen Tabelle zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="9735a-409">**1** The **TypeName** and **StoreEntitySet** attributes can be used in place of the EntityTypeMapping and MappingFragment child elements to map a single entity type to a single table.</span></span>

### <a name="example"></a><span data-ttu-id="9735a-410">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-410">Example</span></span>

<span data-ttu-id="9735a-411">Das folgende Beispiel zeigt ein **EntitySetMapping** -Element, das drei Typen (einen Basistyp und zwei abgeleitete Typen) in **der Entitätenmenge des konzeptionellen** Modells drei verschiedenen Tabellen in der zugrunde liegenden Datenbank zuordnet.</span><span class="sxs-lookup"><span data-stu-id="9735a-411">The following example shows an **EntitySetMapping** element that maps three types (a base type and two derived types) in the **Courses** entity set of the conceptual model to three different tables in the underlying database.</span></span> <span data-ttu-id="9735a-412">Die Tabellen werden durch das **StoreEntitySet** -Attribut in jedem **MappingFragment** -Element angegeben.</span><span class="sxs-lookup"><span data-stu-id="9735a-412">The tables are specified by the **StoreEntitySet** attribute in each **MappingFragment** element.</span></span>

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.Course)">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="Title" ColumnName="Title" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.OnlineCourse)">
     <MappingFragment StoreEntitySet="OnlineCourse">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="URL" ColumnName="URL" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.OnsiteCourse)">
     <MappingFragment StoreEntitySet="OnsiteCourse">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Time" ColumnName="Time" />
       <ScalarProperty Name="Days" ColumnName="Days" />
       <ScalarProperty Name="Location" ColumnName="Location" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="entitytypemapping-element-msl"></a><span data-ttu-id="9735a-413">EntityTypeMapping-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-413">EntityTypeMapping Element (MSL)</span></span>

<span data-ttu-id="9735a-414">Das **EntityTypeMapping** -Element in der Mapping-Spezifikationssprache (Mapping Specification Language, MSL) definiert die Zuordnung zwischen einem Entitätstyp im konzeptionellen Modell und den Tabellen oder Sichten in der zugrunde liegenden Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9735a-414">The **EntityTypeMapping** element in mapping specification language (MSL) defines the mapping between an entity type in the conceptual model and tables or views in the underlying database.</span></span> <span data-ttu-id="9735a-415">Informationen zu den Entitätstypen des konzeptionellen Modells und den zugrunde liegenden Datenbanktabellen oder Ansichten finden Sie in EntityType-Element (CSDL) und EntitySet-Element (SSDL).</span><span class="sxs-lookup"><span data-stu-id="9735a-415">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="9735a-416">Der Entitätstyp des konzeptionellen Modells, der zugeordnet wird, wird durch das **tykame** -Attribut des **EntityTypeMapping** -Elements angegeben.</span><span class="sxs-lookup"><span data-stu-id="9735a-416">The conceptual model entity type that is being mapped is specified by the **TypeName** attribute of the **EntityTypeMapping** element.</span></span> <span data-ttu-id="9735a-417">Die Tabelle oder Sicht, die zugeordnet wird, wird durch das **StoreEntitySet** -Attribut des untergeordneten MappingFragment-Elements angegeben.</span><span class="sxs-lookup"><span data-stu-id="9735a-417">The table or view that is being mapped is specified by the **StoreEntitySet** attribute of the child MappingFragment element.</span></span>

<span data-ttu-id="9735a-418">Das untergeordnete ModificationFunctionMapping-Element kann verwendet werden, um gespeicherten Prozeduren in der Datenbank die Einfüge-, Aktualisierungs- oder Löschfunktionen von Entitätstypen zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="9735a-418">The ModificationFunctionMapping child element can be used to map the insert, update, or delete functions of entity types to stored procedures in the database.</span></span>

<span data-ttu-id="9735a-419">Das **EntityTypeMapping** -Element kann die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="9735a-419">The **EntityTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="9735a-420">MappingFragment (null oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-420">MappingFragment (zero or more)</span></span>
-   <span data-ttu-id="9735a-421">ModificationFunctionMapping (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="9735a-421">ModificationFunctionMapping (zero or one)</span></span>
-   <span data-ttu-id="9735a-422">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="9735a-422">ScalarProperty</span></span>
-   <span data-ttu-id="9735a-423">Bedingung</span><span class="sxs-lookup"><span data-stu-id="9735a-423">Condition</span></span>

> [!NOTE]
> <span data-ttu-id="9735a-424">**MappingFragment** -und **ModificationFunctionMapping** -Elemente können nicht gleichzeitig untergeordnete Elemente des **EntityTypeMapping** -Elements sein.</span><span class="sxs-lookup"><span data-stu-id="9735a-424">**MappingFragment** and **ModificationFunctionMapping** elements cannot be child elements of the **EntityTypeMapping** element at the same time.</span></span>


> [!NOTE]
> <span data-ttu-id="9735a-425">Die **ScalarProperty** -und **Condition** -Elemente können nur untergeordnete Elemente des **EntityTypeMapping** -Elements sein, wenn Sie in einem FunctionImportMapping-Element verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-425">The **ScalarProperty** and **Condition** elements can only be child elements of the **EntityTypeMapping** element when it is used within a FunctionImportMapping element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-426">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-426">Applicable Attributes</span></span>

<span data-ttu-id="9735a-427">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **EntityTypeMapping** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="9735a-427">The following table describes the attributes that can be applied to the **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="9735a-428">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-428">Attribute Name</span></span> | <span data-ttu-id="9735a-429">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-429">Is Required</span></span> | <span data-ttu-id="9735a-430">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-430">Value</span></span>                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-431">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="9735a-431">**TypeName**</span></span>   | <span data-ttu-id="9735a-432">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-432">Yes</span></span>         | <span data-ttu-id="9735a-433">Der mit einem Namespace qualifizierte Name des Entitätstyps des konzeptionellen Modells, der zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-433">The namespace-qualified name of the conceptual model entity type that is being mapped.</span></span> <br/> <span data-ttu-id="9735a-434">Wenn der Typ abstrakt oder ein abgeleiteter Typ ist, muss der Wert `IsOfType(Namespace-qualified_type_name)` lauten.</span><span class="sxs-lookup"><span data-stu-id="9735a-434">If the type is abstract or a derived type, the value must be `IsOfType(Namespace-qualified_type_name)`.</span></span> |

### <a name="example"></a><span data-ttu-id="9735a-435">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-435">Example</span></span>

<span data-ttu-id="9735a-436">Das folgende Beispiel zeigt ein EntitySetMapping-Element mit zwei untergeordneten **EntityTypeMapping** -Elementen.</span><span class="sxs-lookup"><span data-stu-id="9735a-436">The following example shows an EntitySetMapping element with two child **EntityTypeMapping** elements.</span></span> <span data-ttu-id="9735a-437">Im ersten **EntityTypeMapping** -Element wird der " **School Model. Person** "-Entitätstyp der **Person** -Tabelle zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="9735a-437">In the first **EntityTypeMapping** element, the **SchoolModel.Person** entity type is mapped to the **Person** table.</span></span> <span data-ttu-id="9735a-438">Im zweiten **EntityTypeMapping** -Element wird die Aktualisierungs Funktion des **SchoolModel. Person** -Typs der gespeicherten Prozedur **updateperson**in der-Datenbank zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="9735a-438">In the second **EntityTypeMapping** element, the update functionality of the **SchoolModel.Person** type is mapped to a stored procedure, **UpdatePerson**, in the database.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate" ColumnName="EnrollmentDate" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate" ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a><span data-ttu-id="9735a-439">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-439">Example</span></span>

<span data-ttu-id="9735a-440">Im nächsten Beispiel wird die Zuordnung einer Typhierarchie, in der der Stammtyp abstrakt ist, veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="9735a-440">The next example shows the mapping of a type hierarchy in which the root type is abstract.</span></span> <span data-ttu-id="9735a-441">Beachten Sie die Verwendung der `IsOfType` Syntax für die **tygtame** -Attribute.</span><span class="sxs-lookup"><span data-stu-id="9735a-441">Note the use of the `IsOfType` syntax for the **TypeName** attributes.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Person)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Instructor)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <Condition ColumnName="HireDate" IsNull="false" />
       <Condition ColumnName="EnrollmentDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Student)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
       <Condition ColumnName="EnrollmentDate" IsNull="false" />
       <Condition ColumnName="HireDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="functionimportmapping-element-msl"></a><span data-ttu-id="9735a-442">FunctionImportMapping-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-442">FunctionImportMapping Element (MSL)</span></span>

<span data-ttu-id="9735a-443">Das **FunctionImportMapping** -Element in der Mapping-Spezifikationssprache (Mapping Specification Language, MSL) definiert die Zuordnung zwischen einem Funktions Import im konzeptionellen Modell und einer gespeicherten Prozedur oder Funktion in der zugrunde liegenden Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9735a-443">The **FunctionImportMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure or function in the underlying database.</span></span> <span data-ttu-id="9735a-444">Funktionsimporte müssen im konzeptionellen Modell und gespeicherte Prozeduren müssen im Speichermodell deklariert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-444">Function imports must be declared in the conceptual model and stored procedures must be declared in the storage model.</span></span> <span data-ttu-id="9735a-445">Weitere Informationen finden Sie unter FunctionImport-Element (CSDL) und Function-Element (SSDL).</span><span class="sxs-lookup"><span data-stu-id="9735a-445">For more information, see FunctionImport Element (CSDL) and Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="9735a-446">Wenn ein Funktionsimport einen Entitätstyp des konzeptionellen Modells oder einen komplexen Typ zurückgibt, dann müssen standardmäßig die Namen der Spalten, die von der zugrunde liegenden gespeicherten Prozedur zurückgegeben werden, exakt den Namen der Eigenschaften des konzeptionellen Modelltyps entsprechen.</span><span class="sxs-lookup"><span data-stu-id="9735a-446">By default, if a function import returns a conceptual model entity type or complex type, then the names of the columns returned by the underlying stored procedure must exactly match the names of the properties on the conceptual model type.</span></span> <span data-ttu-id="9735a-447">Wenn die Spaltennamen nicht exakt mit den Eigenschaftsnamen übereinstimmen, muss die Zuordnung in einem resultmapping-Element definiert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-447">If the column names do not exactly match the property names, the mapping must be defined in a ResultMapping element.</span></span>

<span data-ttu-id="9735a-448">Das **FunctionImportMapping** -Element kann die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="9735a-448">The **FunctionImportMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="9735a-449">Resultmapping (0 (null) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-449">ResultMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-450">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-450">Applicable Attributes</span></span>

<span data-ttu-id="9735a-451">In der folgenden Tabelle werden die Attribute beschrieben, die für das **FunctionImportMapping** -Element anwendbar sind:</span><span class="sxs-lookup"><span data-stu-id="9735a-451">The following table describes the attributes that are applicable to the **FunctionImportMapping** element:</span></span>

| <span data-ttu-id="9735a-452">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-452">Attribute Name</span></span>         | <span data-ttu-id="9735a-453">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-453">Is Required</span></span> | <span data-ttu-id="9735a-454">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-454">Value</span></span>                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-455">**FunctionImportName**</span><span class="sxs-lookup"><span data-stu-id="9735a-455">**FunctionImportName**</span></span> | <span data-ttu-id="9735a-456">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-456">Yes</span></span>         | <span data-ttu-id="9735a-457">Der Name des Funktionsimports im konzeptionellen Modell, der zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-457">The name of the function import in the conceptual model that is being mapped.</span></span>           |
| <span data-ttu-id="9735a-458">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="9735a-458">**FunctionName**</span></span>       | <span data-ttu-id="9735a-459">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-459">Yes</span></span>         | <span data-ttu-id="9735a-460">Der mit einem Namespace qualifizierte Name der Funktion im Speichermodell, die zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-460">The namespace-qualified name of the function in the storage model that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="9735a-461">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-461">Example</span></span>

<span data-ttu-id="9735a-462">Das folgende Beispiel beruht auf dem School-Modell.</span><span class="sxs-lookup"><span data-stu-id="9735a-462">The following example is based on the School model.</span></span> <span data-ttu-id="9735a-463">Betrachten Sie die folgende Funktion im Speichermodell:</span><span class="sxs-lookup"><span data-stu-id="9735a-463">Consider the following function in the storage model:</span></span>

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

<span data-ttu-id="9735a-464">Beachten Sie auch diesen Funktionsimport im konzeptionellen Modell:</span><span class="sxs-lookup"><span data-stu-id="9735a-464">Also consider this function import in the conceptual model:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

<span data-ttu-id="9735a-465">Das folgende Beispiel zeigt ein **FunctionImportMapping** -Element, das verwendet wird, um den Funktions-und Funktions Import einander zuzuordnen:</span><span class="sxs-lookup"><span data-stu-id="9735a-465">The following example show a **FunctionImportMapping** element used to map the function and function import above to each other:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a><span data-ttu-id="9735a-466">InsertFunction-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-466">InsertFunction Element (MSL)</span></span>

<span data-ttu-id="9735a-467">Das **InsertFunction** -Element in der Mapping-Spezifikationssprache (Mapping Specification Language, MSL) ordnet die INSERT-Funktion eines Entitäts Typs oder einer Zuordnung im konzeptionellen Modell einer gespeicherten Prozedur in der zugrunde liegenden Datenbank zu.</span><span class="sxs-lookup"><span data-stu-id="9735a-467">The **InsertFunction** element in mapping specification language (MSL) maps the insert function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="9735a-468">Gespeicherte Prozeduren, denen Änderungsfunktionen zugeordnet werden, müssen im Speichermodell deklariert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-468">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="9735a-469">Weitere Informationen finden Sie unter Function-Element (SSDL).</span><span class="sxs-lookup"><span data-stu-id="9735a-469">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="9735a-470">Wenn Sie nicht alle drei Einfüge-, Aktualisierungs-oder Löschvorgänge eines Entitäts Typs gespeicherten Prozeduren zuordnen, schlagen die nicht zugeordneten Vorgänge fehl, wenn Sie zur Laufzeit ausgeführt werden und eine Update Exception ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-470">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="9735a-471">Das **InsertFunction** -Element kann ein untergeordnetes Element des ModificationFunctionMapping-Elements sein und auf das EntityTypeMapping-Element oder das AssociationSetMapping-Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-471">The **InsertFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

### <a name="insertfunction-applied-to-entitytypemapping"></a><span data-ttu-id="9735a-472">InsertFunction angewendet auf EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="9735a-472">InsertFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="9735a-473">Wenn das **InsertFunction** -Element auf das EntityTypeMapping-Element angewendet wird, ordnet es die INSERT-Funktion eines Entitäts Typs im konzeptionellen Modell einer gespeicherten Prozedur zu.</span><span class="sxs-lookup"><span data-stu-id="9735a-473">When applied to the EntityTypeMapping element, the **InsertFunction** element maps the insert function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="9735a-474">Das **InsertFunction** -Element kann die folgenden untergeordneten Elemente aufweisen, wenn es auf ein **EntityTypeMapping** -Element angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="9735a-474">The **InsertFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="9735a-475">AssociationEnd (null (0) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-475">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="9735a-476">ComplexProperty (0 (null) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-476">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="9735a-477">ResultBinding (0 (null) oder einer)</span><span class="sxs-lookup"><span data-stu-id="9735a-477">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="9735a-478">ScalarProperty (null (0) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-478">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="9735a-479">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-479">Applicable Attributes</span></span>

<span data-ttu-id="9735a-480">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **InsertFunction** -Element angewendet werden können, wenn es auf ein **EntityTypeMapping** -Element angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-480">The following table describes the attributes that can be applied to the **InsertFunction** element when applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="9735a-481">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-481">Attribute Name</span></span>            | <span data-ttu-id="9735a-482">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-482">Is Required</span></span> | <span data-ttu-id="9735a-483">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-483">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-484">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="9735a-484">**FunctionName**</span></span>          | <span data-ttu-id="9735a-485">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-485">Yes</span></span>         | <span data-ttu-id="9735a-486">Der mit einem Namespace qualifizierte Name der gespeicherten Prozedur, der die Einfügefunktion zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-486">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="9735a-487">Die gespeicherte Prozedur muss im Speichermodell deklariert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-487">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="9735a-488">**Rowsaffectedparameter**</span><span class="sxs-lookup"><span data-stu-id="9735a-488">**RowsAffectedParameter**</span></span> | <span data-ttu-id="9735a-489">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-489">No</span></span>          | <span data-ttu-id="9735a-490">Der Name des Ausgabeparameters, der die Anzahl der betroffenen Zeilen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="9735a-490">The name of the output parameter that returns the number of affected rows.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="9735a-491">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-491">Example</span></span>

<span data-ttu-id="9735a-492">Das folgende Beispiel basiert auf dem Modell "School" und zeigt das **InsertFunction** -Element, das verwendet wird, um die Einfügefunktion des Entitäts Typs "Person" der gespeicherten Prozedur **InsertPerson** zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="9735a-492">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the Person entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="9735a-493">Die gespeicherte Prozedur **InsertPerson** wird im Speichermodell deklariert.</span><span class="sxs-lookup"><span data-stu-id="9735a-493">The **InsertPerson** stored procedure is declared in the storage model.</span></span>

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```
### <a name="insertfunction-applied-to-associationsetmapping"></a><span data-ttu-id="9735a-494">InsertFunction angewendet auf AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="9735a-494">InsertFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="9735a-495">Wenn das **InsertFunction** -Element auf das AssociationSetMapping-Element angewendet wird, ordnet es die INSERT-Funktion einer Zuordnung im konzeptionellen Modell einer gespeicherten Prozedur zu.</span><span class="sxs-lookup"><span data-stu-id="9735a-495">When applied to the AssociationSetMapping element, the **InsertFunction** element maps the insert function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="9735a-496">Das **InsertFunction** -Element kann die folgenden untergeordneten Elemente aufweisen, wenn es auf das **AssociationSetMapping** -Element angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="9735a-496">The **InsertFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="9735a-497">EndProperty</span><span class="sxs-lookup"><span data-stu-id="9735a-497">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="9735a-498">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-498">Applicable Attributes</span></span>

<span data-ttu-id="9735a-499">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **InsertFunction** -Element angewendet werden können, wenn es auf das **AssociationSetMapping** -Element angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-499">The following table describes the attributes that can be applied to the **InsertFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="9735a-500">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-500">Attribute Name</span></span>            | <span data-ttu-id="9735a-501">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-501">Is Required</span></span> | <span data-ttu-id="9735a-502">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-502">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-503">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="9735a-503">**FunctionName**</span></span>          | <span data-ttu-id="9735a-504">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-504">Yes</span></span>         | <span data-ttu-id="9735a-505">Der mit einem Namespace qualifizierte Name der gespeicherten Prozedur, der die Einfügefunktion zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-505">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="9735a-506">Die gespeicherte Prozedur muss im Speichermodell deklariert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-506">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="9735a-507">**Rowsaffectedparameter**</span><span class="sxs-lookup"><span data-stu-id="9735a-507">**RowsAffectedParameter**</span></span> | <span data-ttu-id="9735a-508">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-508">No</span></span>          | <span data-ttu-id="9735a-509">Der Name des Ausgabeparameters, der die Anzahl der betroffenen Zeilen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="9735a-509">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="9735a-510">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-510">Example</span></span>

<span data-ttu-id="9735a-511">Das folgende Beispiel basiert auf dem Modell "School" und zeigt das **InsertFunction** -Element, das verwendet wird, um die INSERT-Funktion der " **CourseInstructor** "-Zuordnung der gespeicherten Prozedur **insertcourseinstructor** zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="9735a-511">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the **CourseInstructor** association to the **InsertCourseInstructor** stored procedure.</span></span> <span data-ttu-id="9735a-512">Die gespeicherte Prozedur **insertcourseinstructor** ist im Speichermodell deklariert.</span><span class="sxs-lookup"><span data-stu-id="9735a-512">The **InsertCourseInstructor** stored procedure is declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="mapping-element-msl"></a><span data-ttu-id="9735a-513">Mapping-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-513">Mapping Element (MSL)</span></span>

<span data-ttu-id="9735a-514">Das **Mapping** -Element in der Mapping-Spezifikationssprache (Mapping Specification Language, MSL) enthält Informationen zum Mapping von Objekten, die in einem konzeptionellen Modell definiert sind, zu einer Datenbank (wie in einem Speichermodell beschrieben).</span><span class="sxs-lookup"><span data-stu-id="9735a-514">The **Mapping** element in mapping specification language (MSL) contains information for mapping objects that are defined in a conceptual model to a database (as described in a storage model).</span></span> <span data-ttu-id="9735a-515">Weitere Informationen finden Sie unter CSDL-Spezifikation und SSDL-Spezifikation.</span><span class="sxs-lookup"><span data-stu-id="9735a-515">For more information, see CSDL Specification and SSDL Specification.</span></span>

<span data-ttu-id="9735a-516">Das **Mapping** -Element ist das Stamm Element für eine Mappingspezifikation.</span><span class="sxs-lookup"><span data-stu-id="9735a-516">The **Mapping** element is the root element for a mapping specification.</span></span> <span data-ttu-id="9735a-517">Der XML-Namespace für Mappingspezifikationen ist https://schemas.microsoft.com/ado/2009/11/mapping/cs .</span><span class="sxs-lookup"><span data-stu-id="9735a-517">The XML namespace for mapping specifications is https://schemas.microsoft.com/ado/2009/11/mapping/cs.</span></span>

<span data-ttu-id="9735a-518">Das Mapping-Element kann die folgenden untergeordneten Elemente aufweisen (der vorliegenden Reihenfolge entsprechend):</span><span class="sxs-lookup"><span data-stu-id="9735a-518">The mapping element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="9735a-519">Alias (null (0) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-519">Alias (zero or more)</span></span>
-   <span data-ttu-id="9735a-520">EntityContainerMapping (genau ein Element)</span><span class="sxs-lookup"><span data-stu-id="9735a-520">EntityContainerMapping (exactly one)</span></span>

<span data-ttu-id="9735a-521">Die Namen aller Typen des konzeptionellen Modells und Typen des Speichermodells, auf die in MSL verwiesen wird, müssen mit dem jeweiligen Namespacenamen qualifiziert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-521">Names of conceptual and storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="9735a-522">Weitere Informationen zum Namespace Namen des konzeptionellen Modells finden Sie unter Schema-Element (CSDL).</span><span class="sxs-lookup"><span data-stu-id="9735a-522">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="9735a-523">Weitere Informationen zum Namespace Namen des Speicher Modells finden Sie unter Schema-Element (SSDL).</span><span class="sxs-lookup"><span data-stu-id="9735a-523">For information about the storage model namespace name, see Schema Element (SSDL).</span></span> <span data-ttu-id="9735a-524">Aliasnamen für Namespaces, die in MSL verwendet werden, können mit dem Alias-Element definiert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-524">Aliases for namespaces that are used in MSL can be defined with the Alias element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-525">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-525">Applicable Attributes</span></span>

<span data-ttu-id="9735a-526">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Mapping** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="9735a-526">The table below describes the attributes that can be applied to the **Mapping** element.</span></span>

| <span data-ttu-id="9735a-527">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-527">Attribute Name</span></span> | <span data-ttu-id="9735a-528">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-528">Is Required</span></span> | <span data-ttu-id="9735a-529">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-529">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="9735a-530">**BRaum**</span><span class="sxs-lookup"><span data-stu-id="9735a-530">**Space**</span></span>      | <span data-ttu-id="9735a-531">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-531">Yes</span></span>         | <span data-ttu-id="9735a-532">**C-S**.</span><span class="sxs-lookup"><span data-stu-id="9735a-532">**C-S**.</span></span> <span data-ttu-id="9735a-533">Dies ist ein fester Wert, der nicht geändert werden kann.</span><span class="sxs-lookup"><span data-stu-id="9735a-533">This is a fixed value and cannot be changed.</span></span> |

### <a name="example"></a><span data-ttu-id="9735a-534">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-534">Example</span></span>

<span data-ttu-id="9735a-535">Das folgende Beispiel zeigt ein **Mapping** -Element, das auf einem Teil des Modells "School" basiert.</span><span class="sxs-lookup"><span data-stu-id="9735a-535">The following example shows a **Mapping** element that is based on part of the School model.</span></span> <span data-ttu-id="9735a-536">Weitere Informationen zum Modell "School" finden Sie unter Schnellstart (Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="9735a-536">For more information about the School model, see Quickstart (Entity Framework):</span></span>

``` xml
 <Mapping Space="C-S"
          xmlns="https://schemas.microsoft.com/ado/2009/11/mapping/cs">
   <Alias Key="c" Value="SchoolModel"/>
   <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                           CdmEntityContainer="SchoolModelEntities">
     <EntitySetMapping Name="Courses">
       <EntityTypeMapping TypeName="c.Course">
         <MappingFragment StoreEntitySet="Course">
           <ScalarProperty Name="CourseID" ColumnName="CourseID" />
           <ScalarProperty Name="Title" ColumnName="Title" />
           <ScalarProperty Name="Credits" ColumnName="Credits" />
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
     <EntitySetMapping Name="Departments">
       <EntityTypeMapping TypeName="c.Department">
         <MappingFragment StoreEntitySet="Department">
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
           <ScalarProperty Name="Name" ColumnName="Name" />
           <ScalarProperty Name="Budget" ColumnName="Budget" />
           <ScalarProperty Name="StartDate" ColumnName="StartDate" />
           <ScalarProperty Name="Administrator" ColumnName="Administrator" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
   </EntityContainerMapping>
 </Mapping>
```

## <a name="mappingfragment-element-msl"></a><span data-ttu-id="9735a-537">MappingFragment-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-537">MappingFragment Element (MSL)</span></span>

<span data-ttu-id="9735a-538">Das **MappingFragment** -Element in der Mapping-Spezifikationssprache (Mapping Specification Language, MSL) definiert die Zuordnung zwischen den Eigenschaften eines Entitäts Typs des konzeptionellen Modells und einer Tabelle oder Sicht in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9735a-538">The **MappingFragment** element in mapping specification language (MSL) defines the mapping between the properties of a conceptual model entity type and a table or view in the database.</span></span> <span data-ttu-id="9735a-539">Informationen zu den Entitätstypen des konzeptionellen Modells und den zugrunde liegenden Datenbanktabellen oder Ansichten finden Sie in EntityType-Element (CSDL) und EntitySet-Element (SSDL).</span><span class="sxs-lookup"><span data-stu-id="9735a-539">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="9735a-540">Das **MappingFragment** kann ein untergeordnetes Element des EntityTypeMapping-Elements oder des EntitySetMapping-Elements sein.</span><span class="sxs-lookup"><span data-stu-id="9735a-540">The **MappingFragment** can be a child element of the EntityTypeMapping element or the EntitySetMapping element.</span></span>

<span data-ttu-id="9735a-541">Das **MappingFragment** -Element kann die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="9735a-541">The **MappingFragment** element can have the following child elements:</span></span>

-   <span data-ttu-id="9735a-542">ComplexType (null oder mehrere Elemente)</span><span class="sxs-lookup"><span data-stu-id="9735a-542">ComplexType (zero or more)</span></span>
-   <span data-ttu-id="9735a-543">ScalarProperty (0 (null) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-543">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="9735a-544">Condition (0 (null) oder mehr Elemente)</span><span class="sxs-lookup"><span data-stu-id="9735a-544">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-545">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-545">Applicable Attributes</span></span>

<span data-ttu-id="9735a-546">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **MappingFragment** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="9735a-546">The following table describes the attributes that can be applied to the **MappingFragment** element.</span></span>

| <span data-ttu-id="9735a-547">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-547">Attribute Name</span></span>          | <span data-ttu-id="9735a-548">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-548">Is Required</span></span> | <span data-ttu-id="9735a-549">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-549">Value</span></span>                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-550">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="9735a-550">**StoreEntitySet**</span></span>      | <span data-ttu-id="9735a-551">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-551">Yes</span></span>         | <span data-ttu-id="9735a-552">Der Name der Tabelle oder Ansicht, die zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-552">The name of the table or view that is being mapped.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="9735a-553">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="9735a-553">**MakeColumnsDistinct**</span></span> | <span data-ttu-id="9735a-554">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-554">No</span></span>          | <span data-ttu-id="9735a-555">**True** oder **false** , abhängig davon, ob nur unterschiedliche Zeilen zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-555">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="9735a-556">Wenn dieses Attribut auf **true**festgelegt ist, muss das **generateupdateviews** -Attribut des EntityContainerMapping-Elements auf **false**festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-556">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

### <a name="example"></a><span data-ttu-id="9735a-557">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-557">Example</span></span>

<span data-ttu-id="9735a-558">Das folgende Beispiel zeigt ein **MappingFragment** -Element als untergeordnetes Element eines **EntityTypeMapping** -Elements.</span><span class="sxs-lookup"><span data-stu-id="9735a-558">The following example shows a **MappingFragment** element as the child of an **EntityTypeMapping** element.</span></span> <span data-ttu-id="9735a-559">In diesem Beispiel werden Eigenschaften des **Course** -Typs im konzeptionellen Modell den Spalten der **Course** -Tabelle in der-Datenbank zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="9735a-559">In this example, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a><span data-ttu-id="9735a-560">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-560">Example</span></span>

<span data-ttu-id="9735a-561">Das folgende Beispiel zeigt ein **MappingFragment** -Element als untergeordnetes Element eines **EntitySetMapping** -Elements.</span><span class="sxs-lookup"><span data-stu-id="9735a-561">The following example shows a **MappingFragment** element as the child of an **EntitySetMapping** element.</span></span> <span data-ttu-id="9735a-562">Wie im obigen Beispiel werden Eigenschaften des **Course** -Typs im konzeptionellen Modell den Spalten der **Course** -Tabelle in der-Datenbank zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="9735a-562">As in the example above, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

``` xml
 <EntitySetMapping Name="Courses" TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
     </MappingFragment>
 </EntitySetMapping>
```

## <a name="modificationfunctionmapping-element-msl"></a><span data-ttu-id="9735a-563">ModificationFunctionMapping-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-563">ModificationFunctionMapping Element (MSL)</span></span>

<span data-ttu-id="9735a-564">Das **ModificationFunctionMapping** -Element in der Mapping-Spezifikationssprache (MSL) ordnet die INSERT-, Update-und DELETE-Funktionen eines Entitäts Typs des konzeptionellen Modells gespeicherten Prozeduren in der zugrunde liegenden Datenbank zu.</span><span class="sxs-lookup"><span data-stu-id="9735a-564">The **ModificationFunctionMapping** element in mapping specification language (MSL) maps the insert, update, and delete functions of a conceptual model entity type to stored procedures in the underlying database.</span></span> <span data-ttu-id="9735a-565">Das **ModificationFunctionMapping** -Element kann auch die INSERT-und DELETE-Funktionen für m:n-Zuordnungen im konzeptionellen Modell zu gespeicherten Prozeduren in der zugrunde liegenden Datenbank zuordnen.</span><span class="sxs-lookup"><span data-stu-id="9735a-565">The **ModificationFunctionMapping** element can also map the insert and delete functions for many-to-many associations in the conceptual model to stored procedures in the underlying database.</span></span> <span data-ttu-id="9735a-566">Gespeicherte Prozeduren, denen Änderungsfunktionen zugeordnet werden, müssen im Speichermodell deklariert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-566">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="9735a-567">Weitere Informationen finden Sie unter Function-Element (SSDL).</span><span class="sxs-lookup"><span data-stu-id="9735a-567">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="9735a-568">Wenn Sie nicht alle drei Einfüge-, Aktualisierungs-oder Löschvorgänge eines Entitäts Typs gespeicherten Prozeduren zuordnen, schlagen die nicht zugeordneten Vorgänge fehl, wenn Sie zur Laufzeit ausgeführt werden und eine Update Exception ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-568">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>


> [!NOTE]
> <span data-ttu-id="9735a-569">Wenn die Änderungsfunktionen für eine Entität in einer Vererbungshierarchie gespeicherten Prozeduren zugeordnet werden, dann müssen die Änderungsfunktionen für alle Typen in der Hierarchie gespeicherten Prozeduren zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-569">If the modification functions for one entity in an inheritance hierarchy are mapped to stored procedures, then modification functions for all types in the hierarchy must be mapped to stored procedures.</span></span>

<span data-ttu-id="9735a-570">Das **ModificationFunctionMapping** -Element kann ein untergeordnetes Element des EntityTypeMapping-Elements oder des AssociationSetMapping-Elements sein.</span><span class="sxs-lookup"><span data-stu-id="9735a-570">The **ModificationFunctionMapping** element can be a child of the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

<span data-ttu-id="9735a-571">Das **ModificationFunctionMapping** -Element kann die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="9735a-571">The **ModificationFunctionMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="9735a-572">DeleteFunction (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="9735a-572">DeleteFunction (zero or one)</span></span>
-   <span data-ttu-id="9735a-573">InsertFunction (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="9735a-573">InsertFunction (zero or one)</span></span>
-   <span data-ttu-id="9735a-574">UpdateFunction (kein (null) oder ein Element)</span><span class="sxs-lookup"><span data-stu-id="9735a-574">UpdateFunction (zero or one)</span></span>

<span data-ttu-id="9735a-575">Für das **ModificationFunctionMapping** -Element sind keine Attribute anwendbar.</span><span class="sxs-lookup"><span data-stu-id="9735a-575">No attributes are applicable to the **ModificationFunctionMapping** element.</span></span>

### <a name="example"></a><span data-ttu-id="9735a-576">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-576">Example</span></span>

<span data-ttu-id="9735a-577">Das folgende Beispiel zeigt die entitätenmengenzuordnung für die **People** -Entitätenmenge im Modell "School".</span><span class="sxs-lookup"><span data-stu-id="9735a-577">The following example shows the entity set mapping for the **People** entity set in the School model.</span></span> <span data-ttu-id="9735a-578">Zusätzlich zur Spalten Zuordnung für den Entitätstyp **Person** wird die Zuordnung der INSERT-, Update-und DELETE-Funktionen des **Person** -Typs angezeigt.</span><span class="sxs-lookup"><span data-stu-id="9735a-578">In addition to the column mapping for the **Person** entity type, the mapping of the insert, update, and delete functions of the **Person** type are shown.</span></span> <span data-ttu-id="9735a-579">Die Funktionen, denen sie zugeordnet werden, sind im Speichermodell deklariert.</span><span class="sxs-lookup"><span data-stu-id="9735a-579">The functions that are mapped to are declared in the storage model.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a><span data-ttu-id="9735a-580">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-580">Example</span></span>

<span data-ttu-id="9735a-581">Das folgende Beispiel zeigt die Zuordnung der Zuordnungs Sätze für den im Modell "School" festgelegten **CourseInstructor** -Zuordnungs Satz.</span><span class="sxs-lookup"><span data-stu-id="9735a-581">The following example shows the association set mapping for the **CourseInstructor** association set in the School model.</span></span> <span data-ttu-id="9735a-582">Zusätzlich zur Spalten Zuordnung für die " **CourseInstructor** "-Zuordnung wird die Zuordnung der INSERT-und DELETE-Funktionen der " **CourseInstructor** "-Zuordnung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="9735a-582">In addition to the column mapping for the **CourseInstructor** association, the mapping of the insert and delete functions of the **CourseInstructor** association are shown.</span></span> <span data-ttu-id="9735a-583">Die Funktionen, denen sie zugeordnet werden, sind im Speichermodell deklariert.</span><span class="sxs-lookup"><span data-stu-id="9735a-583">The functions that are mapped to are declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```
 

 

## <a name="queryview-element-msl"></a><span data-ttu-id="9735a-584">QueryView-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-584">QueryView Element (MSL)</span></span>

<span data-ttu-id="9735a-585">Das **QueryView** -Element in der Mapping-Spezifikationssprache (Mapping Specification Language, MSL) definiert eine schreibgeschützte Zuordnung zwischen einem Entitätstyp oder einer Zuordnung im konzeptionellen Modell und einer Tabelle in der zugrunde liegenden Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9735a-585">The **QueryView** element in mapping specification language (MSL) defines a read-only mapping between an entity type or association in the conceptual model and a table in the underlying database.</span></span> <span data-ttu-id="9735a-586">Die Zuordnung wird mit einer Entity SQL Abfrage definiert, die im Speichermodell ausgewertet wird, und Sie können das Resultset in Bezug auf eine Entität oder eine Zuordnung im konzeptionellen Modell Ausdrücken.</span><span class="sxs-lookup"><span data-stu-id="9735a-586">The mapping is defined with an Entity SQL query that is evaluated against the storage model, and you express the result set in terms of an entity or association in the conceptual model.</span></span> <span data-ttu-id="9735a-587">Da Abfragesichten schreibgeschützt sind, können die durch Abfragesichten definierten Typen nicht mit herkömmlichen Aktualisierungsbefehlen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-587">Because query views are read-only, you cannot use standard update commands to update types that are defined by query views.</span></span> <span data-ttu-id="9735a-588">Diese Typen können mithilfe von Änderungsfunktionen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-588">You can make updates to these types by using modification functions.</span></span> <span data-ttu-id="9735a-589">Weitere Informationen finden Sie unter Gewusst wie: Zuordnen von Änderungs Funktionen zu gespeicherten Prozeduren.</span><span class="sxs-lookup"><span data-stu-id="9735a-589">For more information, see How to: Map Modification Functions to Stored Procedures.</span></span>

> [!NOTE]
> <span data-ttu-id="9735a-590">Im **QueryView** -Element werden Entity SQL Ausdrücke, die **GroupBy**, Gruppen Aggregate oder Navigations Eigenschaften enthalten, nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9735a-590">In the **QueryView** element, Entity SQL expressions that contain **GroupBy**, group aggregates, or navigation properties are not supported.</span></span>

 

<span data-ttu-id="9735a-591">Das **QueryView** -Element kann ein untergeordnetes Element des EntitySetMapping-Elements oder des AssociationSetMapping-Elements sein.</span><span class="sxs-lookup"><span data-stu-id="9735a-591">The **QueryView** element can be a child of the EntitySetMapping element or the AssociationSetMapping element.</span></span> <span data-ttu-id="9735a-592">Im ersten Fall definiert die Abfrageansicht eine schreibgeschützte Zuordnung für eine Entität im konzeptionellen Modell.</span><span class="sxs-lookup"><span data-stu-id="9735a-592">In the former case, the query view defines a read-only mapping for an entity in the conceptual model.</span></span> <span data-ttu-id="9735a-593">Im zweiten Fall definiert die Abfrageansicht eine schreibgeschützte Zuordnung für eine Zuordnung im konzeptionellen Modell.</span><span class="sxs-lookup"><span data-stu-id="9735a-593">In the latter case, the query view defines a read-only mapping for an association in the conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="9735a-594">Wenn das **AssociationSetMapping** -Element für eine Zuordnung zu einer referenziellen Einschränkung vorgesehen ist, wird das **AssociationSetMapping** -Element ignoriert.</span><span class="sxs-lookup"><span data-stu-id="9735a-594">If the **AssociationSetMapping** element is for an association with a referential constraint, the **AssociationSetMapping** element is ignored.</span></span> <span data-ttu-id="9735a-595">Weitere Informationen finden Sie unter referentialeinschränkungs-Element (CSDL).</span><span class="sxs-lookup"><span data-stu-id="9735a-595">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="9735a-596">Das **QueryView** -Element darf keine untergeordneten Elemente aufweisen.</span><span class="sxs-lookup"><span data-stu-id="9735a-596">The **QueryView** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-597">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-597">Applicable Attributes</span></span>

<span data-ttu-id="9735a-598">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **QueryView** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="9735a-598">The following table describes the attributes that can be applied to the **QueryView** element.</span></span>

| <span data-ttu-id="9735a-599">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-599">Attribute Name</span></span> | <span data-ttu-id="9735a-600">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-600">Is Required</span></span> | <span data-ttu-id="9735a-601">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-601">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-602">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="9735a-602">**TypeName**</span></span>   | <span data-ttu-id="9735a-603">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-603">No</span></span>          | <span data-ttu-id="9735a-604">Der Name des konzeptionellen Modelltyps, der durch die Abfrageansicht zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-604">The name of the conceptual model type that is being mapped by the query view.</span></span> |

### <a name="example"></a><span data-ttu-id="9735a-605">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-605">Example</span></span>

<span data-ttu-id="9735a-606">Das folgende Beispiel zeigt das **QueryView** -Element als untergeordnetes Element des **EntitySetMapping** -Elements und definiert eine Abfrage Ansichts Zuordnung für den **Department** -Entitätstyp im Modell "School".</span><span class="sxs-lookup"><span data-stu-id="9735a-606">The following example shows the **QueryView** element as a child of the **EntitySetMapping** element and defines a query view mapping for the **Department** entity type in the School Model.</span></span>

``` xml
 <EntitySetMapping Name="Departments" >
   <QueryView>
     SELECT VALUE SchoolModel.Department(d.DepartmentID,
                                         d.Name,
                                         d.Budget,
                                         d.StartDate)
     FROM SchoolModelStoreContainer.Department AS d
     WHERE d.Budget > 150000
   </QueryView>
 </EntitySetMapping>
```

<span data-ttu-id="9735a-607">Da die Abfrage nur eine Teilmenge der Member des **Abteilungs** Typs im Speichermodell zurückgibt, wurde der **Abteilungs** Typ im Modell "School" wie folgt auf der Grundlage dieser Zuordnung geändert:</span><span class="sxs-lookup"><span data-stu-id="9735a-607">Because the query only returns a subset of the members of the **Department** type in the storage model, the **Department** type in the School model has been modified based on this mapping as follows:</span></span>

``` xml
 <EntityType Name="Department">
   <Key>
     <PropertyRef Name="DepartmentID" />
   </Key>
   <Property Type="Int32" Name="DepartmentID" Nullable="false" />
   <Property Type="String" Name="Name" Nullable="false"
             MaxLength="50" FixedLength="false" Unicode="true" />
   <Property Type="Decimal" Name="Budget" Nullable="false"
             Precision="19" Scale="4" />
   <Property Type="DateTime" Name="StartDate" Nullable="false" />
   <NavigationProperty Name="Courses"
                       Relationship="SchoolModel.FK_Course_Department"
                       FromRole="Department" ToRole="Course" />
 </EntityType>
```

### <a name="example"></a><span data-ttu-id="9735a-608">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-608">Example</span></span>

<span data-ttu-id="9735a-609">Das nächste Beispiel zeigt das **QueryView** -Element als untergeordnetes Element eines **AssociationSetMapping** -Elements und definiert eine schreibgeschützte Zuordnung für die Zuordnung `FK_Course_Department` im Modell "School".</span><span class="sxs-lookup"><span data-stu-id="9735a-609">The next example shows the **QueryView** element as the child of an **AssociationSetMapping** element and defines a read-only mapping for the `FK_Course_Department` association in the School model.</span></span>

``` xml
 <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                         CdmEntityContainer="SchoolEntities">
   <EntitySetMapping Name="Courses" >
     <QueryView>
       SELECT VALUE SchoolModel.Course(c.CourseID,
                                       c.Title,
                                       c.Credits)
       FROM SchoolModelStoreContainer.Course AS c
     </QueryView>
   </EntitySetMapping>
   <EntitySetMapping Name="Departments" >
     <QueryView>
       SELECT VALUE SchoolModel.Department(d.DepartmentID,
                                           d.Name,
                                           d.Budget,
                                           d.StartDate)
       FROM SchoolModelStoreContainer.Department AS d
       WHERE d.Budget > 150000
     </QueryView>
   </EntitySetMapping>
   <AssociationSetMapping Name="FK_Course_Department" >
     <QueryView>
       SELECT VALUE SchoolModel.FK_Course_Department(
         CREATEREF(SchoolEntities.Departments, row(c.DepartmentID), SchoolModel.Department),
         CREATEREF(SchoolEntities.Courses, row(c.CourseID)) )
       FROM SchoolModelStoreContainer.Course AS c
     </QueryView>
   </AssociationSetMapping>
 </EntityContainerMapping>
```
 
### <a name="comments"></a><span data-ttu-id="9735a-610">Kommentare</span><span class="sxs-lookup"><span data-stu-id="9735a-610">Comments</span></span>

<span data-ttu-id="9735a-611">Sie können Abfragesichten definieren, um die folgenden Szenarien zu ermöglichen:</span><span class="sxs-lookup"><span data-stu-id="9735a-611">You can define query views to enable the following scenarios:</span></span>

-   <span data-ttu-id="9735a-612">Definieren einer Entität im konzeptionellen Modell, die nicht alle Eigenschaften der Entität im Speichermodell einschließt.</span><span class="sxs-lookup"><span data-stu-id="9735a-612">Define an entity in the conceptual model that doesn't include all the properties of the entity in the storage model.</span></span> <span data-ttu-id="9735a-613">Dies schließt Eigenschaften ein, die keine Standardwerte aufweisen und keine **null** -Werte unterstützen.</span><span class="sxs-lookup"><span data-stu-id="9735a-613">This includes properties that do not have default values and do not support **null** values.</span></span>
-   <span data-ttu-id="9735a-614">Zuordnen von berechneten Spalten im Speichermodell zu Eigenschaften von Entitätstypen im konzeptionellen Modell.</span><span class="sxs-lookup"><span data-stu-id="9735a-614">Map computed columns in the storage model to properties of entity types in the conceptual model.</span></span>
-   <span data-ttu-id="9735a-615">Definieren eines Mappings, bei dem die Bedingungen, die für die Partitionierung von Entitäten im konzeptionellen Modell verwendet werden, nicht auf Gleichheit basieren.</span><span class="sxs-lookup"><span data-stu-id="9735a-615">Define a mapping where conditions used to partition entities in the conceptual model are not based on equality.</span></span> <span data-ttu-id="9735a-616">Wenn Sie eine bedingte Zuordnung mit dem **Condition** -Element angeben, muss die angegebene Bedingung dem angegebenen Wert entsprechen.</span><span class="sxs-lookup"><span data-stu-id="9735a-616">When you specify a conditional mapping using the **Condition** element, the supplied condition must equal the specified value.</span></span> <span data-ttu-id="9735a-617">Weitere Informationen finden Sie unter Condition-Element (MSL).</span><span class="sxs-lookup"><span data-stu-id="9735a-617">For more information, see Condition Element (MSL).</span></span>
-   <span data-ttu-id="9735a-618">Zuordnen derselben Spalte im Speichermodell zu mehreren Typen im konzeptionellen Modell.</span><span class="sxs-lookup"><span data-stu-id="9735a-618">Map the same column in the storage model to multiple types in the conceptual model.</span></span>
-   <span data-ttu-id="9735a-619">Zuordnen mehrerer Typen zu derselben Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9735a-619">Map multiple types to the same table.</span></span>
-   <span data-ttu-id="9735a-620">Definieren von Zuordnungen im konzeptionellen Modell, die nicht auf Fremdschlüsseln im relationalen Schema basieren.</span><span class="sxs-lookup"><span data-stu-id="9735a-620">Define associations in the conceptual model that are not based on foreign keys in the relational schema.</span></span>
-   <span data-ttu-id="9735a-621">Verwenden benutzerdefinierter Geschäftslogik, um die Werte von Eigenschaften im konzeptionellen Modell festzulegen.</span><span class="sxs-lookup"><span data-stu-id="9735a-621">Use custom business logic to set the value of properties in the conceptual model.</span></span> <span data-ttu-id="9735a-622">Beispielsweise können Sie den Zeichen folgen Wert "T" in der Datenquelle einem Wert von **true**, einem booleschen Wert im konzeptionellen Modell zuordnen.</span><span class="sxs-lookup"><span data-stu-id="9735a-622">For example, you could map the string value "T" in the data source to a value of **true**, a Boolean, in the conceptual model.</span></span>
-   <span data-ttu-id="9735a-623">Definieren bedingter Filter für Abfrageergebnisse.</span><span class="sxs-lookup"><span data-stu-id="9735a-623">Define conditional filters for query results.</span></span>
-   <span data-ttu-id="9735a-624">Erzwingen von geringeren Dateneinschränkungen im konzeptionellen Modell als im Speichermodell.</span><span class="sxs-lookup"><span data-stu-id="9735a-624">Enforce fewer restrictions on data in the conceptual model than in the storage model.</span></span> <span data-ttu-id="9735a-625">Beispielsweise können Sie festlegen, dass eine Eigenschaft im konzeptionellen Modell NULL-Werte zulässt, auch wenn die Spalte, der Sie zugeordnet ist, keine **null**-Werte unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9735a-625">For example, you could make a property in the conceptual model nullable even if the column to which it is mapped does not support **null**values.</span></span>

<span data-ttu-id="9735a-626">Die folgenden Aspekte gelten, wenn Sie Abfragesichten für Entitäten definieren:</span><span class="sxs-lookup"><span data-stu-id="9735a-626">The following considerations apply when you define query views for entities:</span></span>

-   <span data-ttu-id="9735a-627">Abfragesichten sind schreibgeschützt.</span><span class="sxs-lookup"><span data-stu-id="9735a-627">Query views are read-only.</span></span> <span data-ttu-id="9735a-628">Sie können Entitäten nur mit Änderungsfunktionen aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="9735a-628">You can only make updates to entities by using modification functions.</span></span>
-   <span data-ttu-id="9735a-629">Wenn Sie eine Entität durch eine Abfragesicht definieren, müssen Sie auch alle verknüpften Entitäten durch Abfragesichten definieren.</span><span class="sxs-lookup"><span data-stu-id="9735a-629">When you define an entity type by a query view, you must also define all related entities by query views.</span></span>
-   <span data-ttu-id="9735a-630">Wenn Sie einer Entität im Speichermodell, das eine Verknüpfungs Tabelle im relationalen Schema darstellt, eine m:n-Zuordnung zuordnen, müssen Sie im **AssociationSetMapping** -Element für diese Link Tabelle ein **QueryView** -Element definieren.</span><span class="sxs-lookup"><span data-stu-id="9735a-630">When you map a many-to-many association to an entity in the storage model that represents a link table in the relational schema, you must define a **QueryView** element in the **AssociationSetMapping** element for this link table.</span></span>
-   <span data-ttu-id="9735a-631">Abfragesichten müssen für alle Typen in einer Typhierarchie definiert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-631">Query views must be defined for all types in a type hierarchy.</span></span> <span data-ttu-id="9735a-632">Dazu stehen Ihnen folgende Möglichkeiten zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="9735a-632">You can do this in the following ways:</span></span>
-   -   <span data-ttu-id="9735a-633">Mit einem einzelnen **QueryView** -Element, das eine einzelne Entity SQL Abfrage angibt, die eine Vereinigung aller Entitäts Typen in der Hierarchie zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="9735a-633">With a single **QueryView** element that specifies a single Entity SQL query that returns a union of all of the entity types in the hierarchy.</span></span>
    -   <span data-ttu-id="9735a-634">Mit einem einzelnen **QueryView** -Element, das eine einzelne Entity SQL Abfrage angibt, die den Case-Operator verwendet, um einen bestimmten Entitätstyp in der Hierarchie auf der Grundlage einer bestimmten Bedingung zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="9735a-634">With a single **QueryView** element that specifies a single Entity SQL query that uses the CASE operator to return a specific entity type in the hierarchy based on a specific condition.</span></span>
    -   <span data-ttu-id="9735a-635">Mit einem zusätzlichen **QueryView** -Element für einen bestimmten Typ in der Hierarchie.</span><span class="sxs-lookup"><span data-stu-id="9735a-635">With an additional **QueryView** element for a specific type in the hierarchy.</span></span> <span data-ttu-id="9735a-636">Verwenden Sie in diesem Fall das **tykame** -Attribut des **QueryView** -Elements, um den Entitätstyp für jede Ansicht anzugeben.</span><span class="sxs-lookup"><span data-stu-id="9735a-636">In this case, use the **TypeName** attribute of the **QueryView** element to specify the entity type for each view.</span></span>
-   <span data-ttu-id="9735a-637">Wenn eine Abfrage Sicht definiert ist, können Sie das **StorageSetName** -Attribut nicht im **EntitySetMapping** -Element angeben.</span><span class="sxs-lookup"><span data-stu-id="9735a-637">When a query view is defined, you cannot specify the **StorageSetName** attribute on the **EntitySetMapping** element.</span></span>
-   <span data-ttu-id="9735a-638">Wenn eine Abfrage Sicht definiert ist, kann das **EntitySetMapping**-Element nicht auch **Eigenschaften** Zuordnungen enthalten.</span><span class="sxs-lookup"><span data-stu-id="9735a-638">When a query view is defined, the **EntitySetMapping**element cannot also contain **Property** mappings.</span></span>

## <a name="resultbinding-element-msl"></a><span data-ttu-id="9735a-639">ResultBinding-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-639">ResultBinding Element (MSL)</span></span>

<span data-ttu-id="9735a-640">Das **ResultBinding** -Element in der Mapping-Spezifikationssprache (Mapping Specification Language, MSL) ordnet Spaltenwerte, die von gespeicherten Prozeduren zurückgegeben werden, den Entitäts Eigenschaften im konzeptionellen Modell zu.</span><span class="sxs-lookup"><span data-stu-id="9735a-640">The **ResultBinding** element in mapping specification language (MSL) maps column values that are returned by stored procedures to entity properties in the conceptual model when entity type modification functions are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="9735a-641">Wenn z. b. der Wert einer Identitäts Spalte von einer gespeicherten INSERT-Prozedur zurückgegeben wird, ordnet das **ResultBinding** -Element den zurückgegebenen Wert einer Entitätstyp Eigenschaft im konzeptionellen Modell zu.</span><span class="sxs-lookup"><span data-stu-id="9735a-641">For example, when the value of an identity column is returned by an insert stored procedure, the **ResultBinding** element maps the returned value to an entity type property in the conceptual model.</span></span>

<span data-ttu-id="9735a-642">Das **ResultBinding** -Element kann ein untergeordnetes Element des InsertFunction-Elements oder des UpdateFunction-Elements sein.</span><span class="sxs-lookup"><span data-stu-id="9735a-642">The **ResultBinding** element can be child of the InsertFunction element or the UpdateFunction element.</span></span>

<span data-ttu-id="9735a-643">Das **ResultBinding** -Element darf keine untergeordneten Elemente aufweisen.</span><span class="sxs-lookup"><span data-stu-id="9735a-643">The **ResultBinding** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-644">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-644">Applicable Attributes</span></span>

<span data-ttu-id="9735a-645">In der folgenden Tabelle werden die Attribute beschrieben, die für das **ResultBinding** -Element anwendbar sind:</span><span class="sxs-lookup"><span data-stu-id="9735a-645">The following table describes the attributes that are applicable to the **ResultBinding** element:</span></span>

| <span data-ttu-id="9735a-646">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-646">Attribute Name</span></span> | <span data-ttu-id="9735a-647">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-647">Is Required</span></span> | <span data-ttu-id="9735a-648">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-648">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-649">**Name**</span><span class="sxs-lookup"><span data-stu-id="9735a-649">**Name**</span></span>       | <span data-ttu-id="9735a-650">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-650">Yes</span></span>         | <span data-ttu-id="9735a-651">Der Name der Entitätseigenschaft im konzeptionellen Modell, die zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-651">The name of the entity property in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="9735a-652">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="9735a-652">**ColumnName**</span></span> | <span data-ttu-id="9735a-653">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-653">Yes</span></span>         | <span data-ttu-id="9735a-654">Der Name der Spalte, die zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-654">The name of the column being mapped.</span></span>                                          |

### <a name="example"></a><span data-ttu-id="9735a-655">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-655">Example</span></span>

<span data-ttu-id="9735a-656">Das folgende Beispiel basiert auf dem Modell "School" und zeigt ein **InsertFunction** -Element, das verwendet wird, um die Einfügefunktion des Entitäts Typs " **Person** " der gespeicherten Prozedur **InsertPerson** zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="9735a-656">The following example is based on the School model and shows an **InsertFunction** element used to map the insert function of the **Person** entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="9735a-657">(Die gespeicherte Prozedur **InsertPerson** wird im folgenden dargestellt und im Speichermodell deklariert.) Ein **ResultBinding** -Element wird verwendet, um einen Spaltenwert, der von der gespeicherten Prozedur (**NewPersonID**) zurückgegeben wird, einer Entitätstyp Eigenschaft (**PersonID**) zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="9735a-657">(The **InsertPerson** stored procedure is shown below and is declared in the storage model.) A **ResultBinding** element is used to map a column value that is returned by the stored procedure (**NewPersonID**) to an entity type property (**PersonID**).</span></span>

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```

<span data-ttu-id="9735a-658">Das folgende Transact-SQL beschreibt die gespeicherte Prozedur **InsertPerson** :</span><span class="sxs-lookup"><span data-stu-id="9735a-658">The following Transact-SQL describes the **InsertPerson** stored procedure:</span></span>

``` SQL
 CREATE PROCEDURE [dbo].[InsertPerson]
                                @LastName nvarchar(50),
                                @FirstName nvarchar(50),
                                @HireDate datetime,
                                @EnrollmentDate datetime
                                AS
                                INSERT INTO dbo.Person (LastName,
                                                                             FirstName,
                                                                             HireDate,
                                                                             EnrollmentDate)
                                VALUES (@LastName,
                                               @FirstName,
                                               @HireDate,
                                               @EnrollmentDate);
                                SELECT SCOPE_IDENTITY() as NewPersonID;
```

## <a name="resultmapping-element-msl"></a><span data-ttu-id="9735a-659">ResultMapping-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-659">ResultMapping Element (MSL)</span></span>

<span data-ttu-id="9735a-660">Das **resultmapping** -Element in der Mapping-Spezifikationssprache (Mapping Specification Language, MSL) definiert die Zuordnung zwischen einem Funktions Import im konzeptionellen Modell und einer gespeicherten Prozedur in der zugrunde liegenden Datenbank, wenn Folgendes zutrifft:</span><span class="sxs-lookup"><span data-stu-id="9735a-660">The **ResultMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="9735a-661">Der Funktionsimport gibt einen Entitätstyp des konzeptionellen Modells oder einen komplexen Typ zurück.</span><span class="sxs-lookup"><span data-stu-id="9735a-661">The function import returns a conceptual model entity type or complex type.</span></span>
-   <span data-ttu-id="9735a-662">Die Namen der Spalten, die von der gespeicherten Prozedur zurückgegeben werden, entsprechen nicht exakt den Namen der Eigenschaften für den Entitätstyp oder den komplexem Typ.</span><span class="sxs-lookup"><span data-stu-id="9735a-662">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the entity type or complex type.</span></span>

<span data-ttu-id="9735a-663">Standardmäßig basiert die Zuordnung der von einer gespeicherten Prozedur zurückgegebenen Spalten zu einem Entitätstyp oder einem komplexen Typ auf den Spalten- und Eigenschaftennamen.</span><span class="sxs-lookup"><span data-stu-id="9735a-663">By default, the mapping between the columns returned by a stored procedure and an entity type or complex type is based on column and property names.</span></span> <span data-ttu-id="9735a-664">Wenn Spaltennamen nicht exakt mit Eigenschaftsnamen übereinstimmen, müssen Sie das **resultmapping** -Element verwenden, um die Zuordnung zu definieren.</span><span class="sxs-lookup"><span data-stu-id="9735a-664">If column names do not exactly match property names, you must use the **ResultMapping** element to define the mapping.</span></span> <span data-ttu-id="9735a-665">Ein Beispiel für die Standard Zuordnung finden Sie unter FunctionImportMapping-Element (MSL).</span><span class="sxs-lookup"><span data-stu-id="9735a-665">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="9735a-666">Das **resultmapping** -Element ist ein untergeordnetes Element des FunctionImportMapping-Elements.</span><span class="sxs-lookup"><span data-stu-id="9735a-666">The **ResultMapping** element is a child element of the FunctionImportMapping element.</span></span>

<span data-ttu-id="9735a-667">Das **resultmapping** -Element kann die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="9735a-667">The **ResultMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="9735a-668">EntityTypeMapping (0 (null) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-668">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="9735a-669">ComplexTypeMapping</span><span class="sxs-lookup"><span data-stu-id="9735a-669">ComplexTypeMapping</span></span>

<span data-ttu-id="9735a-670">Für das **resultmapping** -Element sind keine Attribute anwendbar.</span><span class="sxs-lookup"><span data-stu-id="9735a-670">No attributes are applicable to the **ResultMapping** Element.</span></span>

### <a name="example"></a><span data-ttu-id="9735a-671">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-671">Example</span></span>

<span data-ttu-id="9735a-672">Sehen Sie sich die folgende gespeicherte Prozedur an:</span><span class="sxs-lookup"><span data-stu-id="9735a-672">Consider the following stored procedure:</span></span>

``` SQL
 CREATE PROCEDURE [dbo].[GetGrades]
             @student_Id int
             AS
             SELECT     EnrollmentID as enroll_id,
                                                                             Grade as grade,
                                                                             CourseID as course_id,
                                                                             StudentID as student_id
                                               FROM dbo.StudentGrade
             WHERE StudentID = @student_Id
```

<span data-ttu-id="9735a-673">Betrachten Sie auch den folgenden Entitätstyp des konzeptionellen Modells:</span><span class="sxs-lookup"><span data-stu-id="9735a-673">Also consider the following conceptual model entity type:</span></span>

``` xml
 <EntityType Name="StudentGrade">
   <Key>
     <PropertyRef Name="EnrollmentID" />
   </Key>
   <Property Name="EnrollmentID" Type="Int32" Nullable="false"
             annotation:StoreGeneratedPattern="Identity" />
   <Property Name="CourseID" Type="Int32" Nullable="false" />
   <Property Name="StudentID" Type="Int32" Nullable="false" />
   <Property Name="Grade" Type="Decimal" Precision="3" Scale="2" />
 </EntityType>
```

<span data-ttu-id="9735a-674">Um einen Funktions Import zu erstellen, der Instanzen des vorherigen Entitäts Typs zurückgibt, muss die Zuordnung zwischen den Spalten, die von der gespeicherten Prozedur zurückgegeben werden, und dem Entitätstyp in einem **resultmapping** -Element definiert werden:</span><span class="sxs-lookup"><span data-stu-id="9735a-674">In order to create a function import that returns instances of the previous entity type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ResultMapping** element:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetGrades"
                        FunctionName="SchoolModel.Store.GetGrades" >
   <ResultMapping>
     <EntityTypeMapping TypeName="SchoolModel.StudentGrade">
       <ScalarProperty Name="EnrollmentID" ColumnName="enroll_id"/>
       <ScalarProperty Name="CourseID" ColumnName="course_id"/>
       <ScalarProperty Name="StudentID" ColumnName="student_id"/>
       <ScalarProperty Name="Grade" ColumnName="grade"/>
     </EntityTypeMapping>
   </ResultMapping>
 </FunctionImportMapping>
```

## <a name="scalarproperty-element-msl"></a><span data-ttu-id="9735a-675">ScalarProperty-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-675">ScalarProperty Element (MSL)</span></span>

<span data-ttu-id="9735a-676">Das **ScalarProperty** -Element in der Mapping-Spezifikationssprache (Mapping Specification Language, MSL) ordnet eine Eigenschaft einem Entitätstyp des konzeptionellen Modells, einem komplexen Typ oder einer Zuordnung zu einer Tabellenspalte oder einem Parameter einer gespeicherten Prozedur in der zugrunde liegenden Datenbank zu</span><span class="sxs-lookup"><span data-stu-id="9735a-676">The **ScalarProperty** element in mapping specification language (MSL) maps a property on a conceptual model entity type, complex type, or association to a table column or stored procedure parameter in the underlying database.</span></span>

> [!NOTE]
> <span data-ttu-id="9735a-677">Gespeicherte Prozeduren, denen Änderungsfunktionen zugeordnet werden, müssen im Speichermodell deklariert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-677">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="9735a-678">Weitere Informationen finden Sie unter Function-Element (SSDL).</span><span class="sxs-lookup"><span data-stu-id="9735a-678">For more information, see Function Element (SSDL).</span></span>

<span data-ttu-id="9735a-679">Das **ScalarProperty** -Element kann ein untergeordnetes Element der folgenden Elemente sein:</span><span class="sxs-lookup"><span data-stu-id="9735a-679">The **ScalarProperty** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="9735a-680">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="9735a-680">MappingFragment</span></span>
-   <span data-ttu-id="9735a-681">InsertFunction</span><span class="sxs-lookup"><span data-stu-id="9735a-681">InsertFunction</span></span>
-   <span data-ttu-id="9735a-682">UpdateFunction</span><span class="sxs-lookup"><span data-stu-id="9735a-682">UpdateFunction</span></span>
-   <span data-ttu-id="9735a-683">DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="9735a-683">DeleteFunction</span></span>
-   <span data-ttu-id="9735a-684">EndProperty</span><span class="sxs-lookup"><span data-stu-id="9735a-684">EndProperty</span></span>
-   <span data-ttu-id="9735a-685">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="9735a-685">ComplexProperty</span></span>
-   <span data-ttu-id="9735a-686">ResultMapping</span><span class="sxs-lookup"><span data-stu-id="9735a-686">ResultMapping</span></span>

<span data-ttu-id="9735a-687">Als untergeordnetes Element des Elements **MappingFragment**, **ComplexProperty**oder **EndProperty** ordnet das **ScalarProperty** -Element eine Eigenschaft im konzeptionellen Modell einer Spalte in der Datenbank zu.</span><span class="sxs-lookup"><span data-stu-id="9735a-687">As a child of the **MappingFragment**, **ComplexProperty**, or **EndProperty** element, the **ScalarProperty** element maps a property in the conceptual model to a column in the database.</span></span> <span data-ttu-id="9735a-688">Als untergeordnetes Element des Elements **InsertFunction**, **UpdateFunction**oder **DeleteFunction** ordnet das **ScalarProperty** -Element eine Eigenschaft im konzeptionellen Modell einem Parameter für gespeicherte Prozeduren zu.</span><span class="sxs-lookup"><span data-stu-id="9735a-688">As a child of the **InsertFunction**, **UpdateFunction**, or **DeleteFunction** element, the **ScalarProperty** element maps a property in the conceptual model to a stored procedure parameter.</span></span>

<span data-ttu-id="9735a-689">Das **ScalarProperty** -Element darf keine untergeordneten Elemente aufweisen.</span><span class="sxs-lookup"><span data-stu-id="9735a-689">The **ScalarProperty** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-690">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-690">Applicable Attributes</span></span>

<span data-ttu-id="9735a-691">Die Attribute, die auf das **ScalarProperty** -Element angewendet werden, unterscheiden sich abhängig von der Rolle des Elements.</span><span class="sxs-lookup"><span data-stu-id="9735a-691">The attributes that apply to the **ScalarProperty** element differ depending on the role of the element.</span></span>

<span data-ttu-id="9735a-692">In der folgenden Tabelle werden die Attribute beschrieben, die anwendbar sind, wenn das **ScalarProperty** -Element verwendet wird, um einer Spalte in der Datenbank eine Eigenschaft eines konzeptionellen Modells zuzuordnen:</span><span class="sxs-lookup"><span data-stu-id="9735a-692">The following table describes the attributes that are applicable when the **ScalarProperty** element is used to map a conceptual model property to a column in the database:</span></span>

| <span data-ttu-id="9735a-693">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-693">Attribute Name</span></span> | <span data-ttu-id="9735a-694">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-694">Is Required</span></span> | <span data-ttu-id="9735a-695">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-695">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="9735a-696">**Name**</span><span class="sxs-lookup"><span data-stu-id="9735a-696">**Name**</span></span>       | <span data-ttu-id="9735a-697">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-697">Yes</span></span>         | <span data-ttu-id="9735a-698">Der Name der Eigenschaft im konzeptionellen Modell, die zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-698">The name of the conceptual model property that is being mapped.</span></span> |
| <span data-ttu-id="9735a-699">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="9735a-699">**ColumnName**</span></span> | <span data-ttu-id="9735a-700">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-700">Yes</span></span>         | <span data-ttu-id="9735a-701">Der Name der Tabellenspalte, die zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-701">The name of the table column that is being mapped.</span></span>              |

<span data-ttu-id="9735a-702">In der folgenden Tabelle werden die Attribute beschrieben, die für das **ScalarProperty** -Element gelten, wenn es verwendet wird, um eine Eigenschaft des konzeptionellen Modells einem Parameter für gespeicherte Prozeduren zuzuordnen:</span><span class="sxs-lookup"><span data-stu-id="9735a-702">The following table describes the attributes that are applicable to the **ScalarProperty** element when it is used to map a conceptual model property to a stored procedure parameter:</span></span>

| <span data-ttu-id="9735a-703">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-703">Attribute Name</span></span>    | <span data-ttu-id="9735a-704">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-704">Is Required</span></span> | <span data-ttu-id="9735a-705">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-705">Value</span></span>                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-706">**Name**</span><span class="sxs-lookup"><span data-stu-id="9735a-706">**Name**</span></span>          | <span data-ttu-id="9735a-707">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-707">Yes</span></span>         | <span data-ttu-id="9735a-708">Der Name der Eigenschaft im konzeptionellen Modell, die zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-708">The name of the conceptual model property that is being mapped.</span></span>                                                                                 |
| <span data-ttu-id="9735a-709">**Parameter Name**</span><span class="sxs-lookup"><span data-stu-id="9735a-709">**ParameterName**</span></span> | <span data-ttu-id="9735a-710">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-710">Yes</span></span>         | <span data-ttu-id="9735a-711">Der Name des Parameters, der zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-711">The name of the parameter that is being mapped.</span></span>                                                                                                 |
| <span data-ttu-id="9735a-712">**Version**</span><span class="sxs-lookup"><span data-stu-id="9735a-712">**Version**</span></span>       | <span data-ttu-id="9735a-713">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-713">No</span></span>          | <span data-ttu-id="9735a-714">**Current** oder **Original** , abhängig davon, ob der aktuelle Wert oder der ursprüngliche Wert der-Eigenschaft für Parallelitäts Überprüfungen verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="9735a-714">**Current** or **Original** depending on whether the current value or the original value of the property should be used for concurrency checks.</span></span> |

### <a name="example"></a><span data-ttu-id="9735a-715">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-715">Example</span></span>

<span data-ttu-id="9735a-716">Das folgende Beispiel zeigt das **ScalarProperty** -Element, das auf zweierlei Weise verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="9735a-716">The following example shows the **ScalarProperty** element used in two ways:</span></span>

-   <span data-ttu-id="9735a-717">, Um die Eigenschaften des Entitäts Typs **Person** den Spalten der **Person**-Tabelle zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="9735a-717">To map the properties of the **Person** entity type to the columns of the **Person**table.</span></span>
-   <span data-ttu-id="9735a-718">, Um die Eigenschaften des Entitäts Typs **Person** den Parametern der gespeicherten Prozedur **updateperson** zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="9735a-718">To map the properties of the **Person** entity type to the parameters of the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="9735a-719">Die gespeicherten Prozeduren werden im Speichermodell deklariert.</span><span class="sxs-lookup"><span data-stu-id="9735a-719">The stored procedures are declared in the storage model.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a><span data-ttu-id="9735a-720">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-720">Example</span></span>

<span data-ttu-id="9735a-721">Das nächste Beispiel zeigt das **ScalarProperty** -Element, das verwendet wird, um die INSERT-und DELETE-Funktionen einer konzeptionellen Modell Zuordnung gespeicherten Prozeduren in der Datenbank zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="9735a-721">The next example shows the **ScalarProperty** element used to map the insert and delete functions of a conceptual model association to stored procedures in the database.</span></span> <span data-ttu-id="9735a-722">Die gespeicherten Prozeduren werden im Speichermodell deklariert.</span><span class="sxs-lookup"><span data-stu-id="9735a-722">The stored procedures are declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="updatefunction-element-msl"></a><span data-ttu-id="9735a-723">UpdateFunction-Element (MSL)</span><span class="sxs-lookup"><span data-stu-id="9735a-723">UpdateFunction Element (MSL)</span></span>

<span data-ttu-id="9735a-724">Das **UpdateFunction** -Element in der Mapping-Spezifikationssprache (Mapping Specification Language, MSL) ordnet die Aktualisierungs Funktion eines Entitäts Typs im konzeptionellen Modell einer gespeicherten Prozedur in der zugrunde liegenden Datenbank zu.</span><span class="sxs-lookup"><span data-stu-id="9735a-724">The **UpdateFunction** element in mapping specification language (MSL) maps the update function of an entity type in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="9735a-725">Gespeicherte Prozeduren, denen Änderungsfunktionen zugeordnet werden, müssen im Speichermodell deklariert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-725">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="9735a-726">Weitere Informationen finden Sie unter Function-Element (SSDL).</span><span class="sxs-lookup"><span data-stu-id="9735a-726">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
>  <span data-ttu-id="9735a-727">Wenn Sie nicht alle drei Einfüge-, Aktualisierungs-oder Löschvorgänge eines Entitäts Typs gespeicherten Prozeduren zuordnen, schlagen die nicht zugeordneten Vorgänge fehl, wenn Sie zur Laufzeit ausgeführt werden und eine Update Exception ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-727">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="9735a-728">Das **UpdateFunction** -Element kann ein untergeordnetes Element des ModificationFunctionMapping-Elements sein und auf das EntityTypeMapping-Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-728">The **UpdateFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element.</span></span>

<span data-ttu-id="9735a-729">Das **UpdateFunction** -Element kann die folgenden untergeordneten Elemente aufweisen:</span><span class="sxs-lookup"><span data-stu-id="9735a-729">The **UpdateFunction** element can have the following child elements:</span></span>

-   <span data-ttu-id="9735a-730">AssociationEnd (null (0) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-730">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="9735a-731">ComplexProperty (0 (null) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-731">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="9735a-732">ResultBinding (0 (null) oder einer)</span><span class="sxs-lookup"><span data-stu-id="9735a-732">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="9735a-733">ScalarProperty (null (0) oder mehr)</span><span class="sxs-lookup"><span data-stu-id="9735a-733">ScarlarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="9735a-734">Anwendbare Attribute</span><span class="sxs-lookup"><span data-stu-id="9735a-734">Applicable Attributes</span></span>

<span data-ttu-id="9735a-735">In der folgenden Tabelle werden die Attribute beschrieben, die auf das **UpdateFunction** -Element angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="9735a-735">The following table describes the attributes that can be applied to the **UpdateFunction** element.</span></span>

| <span data-ttu-id="9735a-736">Attributname</span><span class="sxs-lookup"><span data-stu-id="9735a-736">Attribute Name</span></span>            | <span data-ttu-id="9735a-737">Ist erforderlich</span><span class="sxs-lookup"><span data-stu-id="9735a-737">Is Required</span></span> | <span data-ttu-id="9735a-738">Wert</span><span class="sxs-lookup"><span data-stu-id="9735a-738">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9735a-739">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="9735a-739">**FunctionName**</span></span>          | <span data-ttu-id="9735a-740">Ja</span><span class="sxs-lookup"><span data-stu-id="9735a-740">Yes</span></span>         | <span data-ttu-id="9735a-741">Der mit einem Namespace qualifizierte Name der gespeicherten Prozedur, der die Aktualisierungsfunktion zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="9735a-741">The namespace-qualified name of the stored procedure to which the update function is mapped.</span></span> <span data-ttu-id="9735a-742">Die gespeicherte Prozedur muss im Speichermodell deklariert werden.</span><span class="sxs-lookup"><span data-stu-id="9735a-742">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="9735a-743">**Rowsaffectedparameter**</span><span class="sxs-lookup"><span data-stu-id="9735a-743">**RowsAffectedParameter**</span></span> | <span data-ttu-id="9735a-744">Nein</span><span class="sxs-lookup"><span data-stu-id="9735a-744">No</span></span>          | <span data-ttu-id="9735a-745">Der Name des Ausgabeparameters, der die Anzahl der betroffenen Zeilen zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="9735a-745">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

### <a name="example"></a><span data-ttu-id="9735a-746">Beispiel</span><span class="sxs-lookup"><span data-stu-id="9735a-746">Example</span></span>

<span data-ttu-id="9735a-747">Das folgende Beispiel basiert auf dem Modell "School" und zeigt das **UpdateFunction** -Element, das verwendet wird, um die Aktualisierungs Funktion des Entitäts Typs " **Person** " der gespeicherten Prozedur " **updateperson** " zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="9735a-747">The following example is based on the School model and shows the **UpdateFunction** element used to map update function of the **Person** entity type to the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="9735a-748">Die gespeicherte Prozedur **updateperson** wird im Speichermodell deklariert.</span><span class="sxs-lookup"><span data-stu-id="9735a-748">The **UpdatePerson** stored procedure is declared in the storage model.</span></span>

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```
