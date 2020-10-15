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
# <a name="csdl-specification"></a>CSDL-Spezifikation
Die konzeptionelle Schemadefinitionssprache (CSDL) ist eine XML-basierte Sprache, die die Entitäten, Beziehungen und Funktionen beschreibt, die ein konzeptionelles Modell einer datengesteuerten Anwendung bilden. Dieses konzeptionelle Modell kann von der Entity Framework oder WCF Data Services verwendet werden. Die Metadaten, die mit CSDL beschrieben werden, werden vom-Entity Framework verwendet, um Entitäten und Beziehungen, die in einem konzeptionellen Modell definiert sind, einer-Datenquelle zuzuordnen. Weitere Informationen finden Sie unter [SSDL-Spezifikation](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) und [MSL-Spezifikation](xref:ef6/modeling/designer/advanced/edmx/msl-spec).

CSDL ist die Implementierung des Entity Data Model der Entity Framework.

In einer Entity Framework-Anwendung werden Metadaten des konzeptionellen Modells aus einer CSDL-Datei (geschrieben in CSDL) in eine Instanz der System. Data. Metadata. Edm. EdmItemCollection geladen und können mithilfe von Methoden in der System. Data. Metadata. Edm. MetadataWorkspace-Klasse aufgerufen werden. Entity Framework verwendet Metadaten des konzeptionellen Modells, um Abfragen für das konzeptionelle Modell in Datenquellen spezifische Befehle zu übersetzen.

Der EF-Designer speichert Informationen über das konzeptionelle Modell in einer EDMX-Datei zur Entwurfszeit. Zur Erstellungszeit verwendet der EF-Designer Informationen in einer EDMX-Datei, um die CSDL-Datei zu erstellen, die zur Laufzeit von Entity Framework benötigt wird.

Die verschiedenen Versionen von CSDL werden von XML-Namespaces unterschieden.

| CSDL-Version | XML-Namespace                                |
|:-------------|:---------------------------------------------|
| CSDL v1      | https://schemas.microsoft.com/ado/2006/04/edm |
| CSDL v2      | https://schemas.microsoft.com/ado/2008/09/edm |
| CSDL v3      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a>Zuordnungselement (CSDL)

Ein **Association** -Element definiert eine Beziehung zwischen zwei Entitäts Typen. Eine Zuordnung muss die Entitätstypen, die in der Beziehung enthalten sind, und die mögliche Anzahl von Entitätstypen an den Enden der Beziehung angeben, die auch als Multiplizität bezeichnet wird. Die Multiplizität eines Zuordnungs Endes kann über einen Wert von eins (1), NULL oder eins (0.. 1) oder viele ( \* ) verfügen. Diese Informationen werden in zwei untergeordneten End-Elementen angegeben.

Auf Entitätstypinstanzen an einem Ende einer Zuordnung kann über Navigationseigenschaften oder Fremdschlüssel zugegriffen werden, sofern sie für einen Entitätstyp verfügbar gemacht werden.

In einer Anwendung stellt eine Instanz einer Zuordnung eine bestimmte Zuordnung zwischen Instanzen von Entitätstypen dar. Zuordnungsinstanzen werden logisch in einem Zuordnungssatz gruppiert.

Ein **Association** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   Dokumentation (kein (null) oder ein Element)
-   End (genau 2 Elemente)
-   ReferentialConstraint (kein (null) oder ein Element)
-   Anmerkungselemente (kein Element (null) oder mehrere Elemente)

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Association** -Element angewendet werden können.

| Attributname | Ist erforderlich | Wert                        |
|:---------------|:------------|:-----------------------------|
| **Name**       | Ja         | Der Name der Zuordnung. |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Association** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **Association** -Element, das die Zuordnung **CustomerOrders** definiert, wenn Fremdschlüssel für die Entitäts Typen **Customer** und **Order** nicht verfügbar gemacht wurden. Die **Multiplicity** multiplizitätswerte für jedes **Ende** der Zuordnung geben an, dass viele **Bestellungen** einem **Kunden**zugeordnet werden können, aber nur ein **Kunde** kann einem **Auftrag**zugeordnet werden. Außerdem gibt das **OnDelete** -Element an, dass alle **Bestellungen** , die mit einem bestimmten **Kunden** verknüpft sind und in den ObjectContext geladen wurden, gelöscht werden, wenn der **Kunde** gelöscht wird.

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

Das folgende Beispiel zeigt ein **Association** -Element, das die Zuordnung " **CustomerOrders** " definiert, wenn Fremdschlüssel für die Entitäts Typen " **Customer** " und " **Order** " verfügbar gemacht wurden. Wenn Fremdschlüssel verfügbar gemacht werden, wird die Beziehung zwischen den Entitäten mit einem **referentialeinschränkung** -Element verwaltet. Ein entsprechendes AssociationSetMapping-Element ist zur Zuordnung dieser Zuweisung zur Datenquelle nicht erforderlich.

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
 

 

## <a name="associationset-element-csdl"></a>AssociationSet-Element (CSDL)

Das **AssociationSet** -Element in konzeptioneller Schema Definitions Sprache (CSDL) ist ein logischer Container für Zuordnungs Instanzen desselben Typs. Ein Zuordnungssatz stellt eine Definition zum Gruppieren von Zuordnungsinstanzen bereit, sodass sie einer Datenquelle zugeordnet werden können.  

Das **AssociationSet** -Element kann über die folgenden untergeordneten Elemente verfügen (in der angegebenen Reihenfolge):

-   Dokumentation (kein (null) oder ein Element zugelassen)
-   End (genau zwei Elemente erforderlich)
-   Anmerkungselemente (kein (null) Element oder mehrere Elemente zugelassen)

Das **Association** -Attribut gibt den Zuordnungstyp an, der in einem Zuordnungs Satz enthalten ist. Die Entitätenmengen, die die Enden eines Zuordnungs Satzes bilden, werden mit genau **End** zwei untergeordneten Endelementen angegeben.

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **AssociationSet** -Element angewendet werden können.

| Attributname  | Ist erforderlich | Wert                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**        | Ja         | Der Name des Entitätssatzes. Der Wert des **Name** -Attributs darf nicht mit dem Wert des **Association** -Attributs identisch sein.                                 |
| **Zuordnung** | Ja         | Der vollqualifizierte Name der Zuordnung, von der der Zuordnungssatz Instanzen enthält. Die Zuordnung muss sich im gleichen Namespace wie der Zuordnungssatz befinden. |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **AssociationSet** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **EntityContainer** -Element mit zwei **AssociationSet** -Elementen:

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
 

 

## <a name="collectiontype-element-csdl"></a>CollectionType-Element (CSDL)

Das **CollectionType** -Element in der konzeptionellen Schema Definitions Sprache (CSDL) gibt an, dass ein Funktionsparameter oder Funktions Rückgabetyp eine Auflistung ist. Das **CollectionType** -Element kann ein untergeordnetes Element des Parameter-Elements oder des ReturnType (Function)-Elements sein. Der Typ der Auflistung kann entweder mit dem **Type** -Attribut oder einem der folgenden untergeordneten Elemente angegeben werden:

-   **CollectionType**
-   ReferenceType
-   RowType
-   TypeRef

> [!NOTE]
> Ein Modell überprüft nicht, ob der Typ einer Auflistung sowohl mit dem **Type** -Attribut als auch mit einem untergeordneten Element angegeben wird.

 

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **CollectionType** -Element angewendet werden können. Beachten Sie, dass die Attribute **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**und **COLLATIONS** nur auf Auflistungen von **edmsimpletypes**anwendbar sind.

| Attributname                                                          | Ist erforderlich | Wert                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Typ**                                                                | Nein          | Der Typ der Auflistung.                                                                                                                                                                                                      |
| **NULL zulassen**                                                            | Nein          | **True** (Standardwert) oder **False** abhängig davon, ob die Eigenschaft einen NULL-Wert haben kann. <br/> [!NOTE]                                                                                                                 |
| > in CSDL v1 muss eine Eigenschaft eines komplexen Typs über verfügen `Nullable="False"` . |             |                                                                                                                                                                                                                                  |
| **DefaultValue**                                                        | Nein          | Der Standardwert der Eigenschaft.                                                                                                                                                                                               |
| **MaxLength**                                                           | Nein          | Maximale Länge des Eigenschaftswerts.                                                                                                                                                                                        |
| **FixedLength**                                                         | Nein          | **True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Zeichenfolge mit fester Länge gespeichert wird.                                                                                                                           |
| **Genauigkeit**                                                           | Nein          | Die Genauigkeit des Eigenschaftswerts.                                                                                                                                                                                             |
| **Skalierung**                                                               | Nein          | Die Skalierung des Eigenschaftswerts.                                                                                                                                                                                                 |
| **SRID**                                                                | Nein          | Verweis Bezeichner für räumliche Systeme. Nur für Eigenschaften räumlicher Typen gültig.Weitere Informationen finden Sie unter [SRID](https://en.wikipedia.org/wiki/SRID) und [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx) . |
| **Unicode**                                                             | Nein          | **True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Unicode-Zeichenfolge gespeichert wird.                                                                                                                                |
| **Sortierung**                                                           | Nein          | Eine Zeichenfolge, die angibt, welche Sortierreihenfolge in der Datenquelle verwendet wird.                                                                                                                                                    |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **CollectionType** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt eine Modell definierte Funktion, die ein **CollectionType** -Element verwendet, um anzugeben, dass die Funktion eine Auflistung von **Person** -Entitäts Typen zurückgibt (wie mit dem **Element Type** -Attribut angegeben).

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
 

Das folgende Beispiel zeigt eine Modell definierte Funktion, die ein **CollectionType** -Element verwendet, um anzugeben, dass die Funktion eine Auflistung von Zeilen zurückgibt (wie im **RowType** -Element angegeben).

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
 

Das folgende Beispiel zeigt eine Modell definierte Funktion, die das **CollectionType** -Element verwendet, um anzugeben, dass die Funktion als Parameter eine Auflistung von **Abteilungs** Entitäts Typen akzeptiert.

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
 

 

## <a name="complextype-element-csdl"></a>ComplexType-Element (CSDL)

Ein **complexType** -Element definiert eine Datenstruktur, die aus **edmsimpletype** -Eigenschaften oder anderen komplexen Typen besteht.Ein komplexer Typ kann eine Eigenschaft eines Entitätstyps oder eines anderen komplexen Typs sein. Ein komplexer Typ entspricht einem Entitätstyp, in dem von einem komplexen Typ Daten definiert werden. Es gibt jedoch einige Hauptunterschiede zwischen komplexen Typen und Entitätstypen:

-   Komplexe Typen weisen keine Identitäten (oder Schlüssel) auf und können daher nicht unabhängig sein. Komplexe Typen können nur Eigenschaften von Entitätstypen oder anderen komplexen Typen sein.
-   Komplexe Typen können nicht Teile von Zuordnungen sein. Die Enden einer Zuordnung können kein komplexer Typ sein, daher können Navigationseigenschaften nicht für komplexe Typen definiert werden.
-   Einer komplexen Typeigenschaft kann kein NULL-Wert zugewiesen werden, obwohl jede skalare Eigenschaft eines komplexen Typs auf NULL festgelegt werden kann.

Ein **complexType** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   Dokumentation (kein (null) oder ein Element)
-   Property (0 (null) oder mehr Elemente)
-   Anmerkungselemente (kein Element (null) oder mehrere Elemente)

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **complexType** -Element angewendet werden können.

| Attributname                                                                                                 | Ist erforderlich | Wert                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Name                                                                                                           | Ja         | Der Name des komplexen Typs. Der Name eines komplexen Typs darf nicht dem Namen anderer komplexer Typen, Entitätstypen oder Zuordnungen entsprechen, die sich innerhalb des Bereichs des Modells befinden. |
| BaseType                                                                                                       | Nein          | Der Name eines anderen komplexen Typs, der der Basistyp des zu definierenden komplexen Typs ist. <br/> [!NOTE]                                                                     |
| > dieses Attribut ist in CSDL v1 nicht anwendbar. Für komplexe Typen wird Vererbung in dieser Version nicht unterstützt. |             |                                                                                                                                                                                     |
| Zusammenfassung                                                                                                       | Nein          | **True** oder **false** (der Standardwert), abhängig davon, ob der komplexe Typ ein abstrakter Typ ist. <br/> [!NOTE]                                                                  |
| > dieses Attribut ist in CSDL v1 nicht anwendbar. Komplexe Typen in dieser Version können keine abstrakten Typen sein.         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **complexType** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt einen komplexen Typ, eine **Adresse**mit den **edmsimpletype** -Eigenschaften " **StreetAddress**", " **City**", " **StateOrProvince**", " **Country**" und " **PostalCode**".

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

Um die **Adresse** des komplexen Typs (oben) als Eigenschaft eines Entitäts Typs zu definieren, müssen Sie den Eigenschaftentyp in der Entitätstyp Definition deklarieren. Das folgende Beispiel zeigt die **Address** -Eigenschaft als komplexen Typ für einen Entitätstyp (**Publisher**):

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
 

 

## <a name="definingexpression-element-csdl"></a>DefiningExpression-Element (CSDL)

Das **DefiningExpression** -Element in konzeptioneller Schema Definitions Sprache (CSDL) enthält einen Entity SQL Ausdruck, der eine Funktion im konzeptionellen Modell definiert.  

> [!NOTE]
> Zu Überprüfungszwecken kann ein **DefiningExpression** -Element beliebigen Inhalt enthalten. Entity Framework wird jedoch zur Laufzeit eine Ausnahme auslösen, wenn ein **DefiningExpression** -Element keine gültigen Entity SQL enthält.

 

### <a name="applicable-attributes"></a>Anwendbare Attribute

Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **DefiningExpression** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird ein **DefiningExpression** -Element verwendet, um eine Funktion zu definieren, die die Anzahl der Jahre seit der Veröffentlichung eines Buchs zurückgibt. Der Inhalt des **DefiningExpression** -Elements wird in Entity SQL geschrieben.

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a>Dependent-Element (CSDL)

Das **abhängige** Element in konzeptioneller Schema Definitions Sprache (CSDL) ist ein untergeordnetes Element des referentialeinschränkung-Elements und definiert das abhängige Ende einer referenziellen Einschränkung. Ein **referentialeinschränkung** -Element definiert Funktionen, die einer Einschränkung der referenziellen Integrität in einer relationalen Datenbank ähneln. So wie eine Spalte (oder Spalten) einer Datenbanktabelle auf den Primärschlüssel einer anderen Tabelle verweisen kann, kann eine Eigenschaft (oder Eigenschaften) eines Entitätstyps auf den Entitätsschlüssel eines anderen Entitätstyps verweisen. Der Entitätstyp, auf den verwiesen wird, wird als *Prinzipal Ende* der Einschränkung bezeichnet. Der Entitätstyp, der auf das Prinzipal Ende verweist, wird als *abhängiges Ende* der Einschränkung bezeichnet. **PropertyRef** -Elemente werden verwendet, um anzugeben, welche Schlüssel auf das Prinzipal Ende verweisen.

Das **abhängige** Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   PropertyRef (mindestens ein Element)
-   Anmerkungselemente (kein Element (null) oder mehrere Elemente)

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **abhängige** Element angewendet werden können.

| Attributname | Ist erforderlich | Wert                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| **Rolle**       | Ja         | Der Name des Entitätstyps am abhängigen Ende der Zuordnung. |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **abhängige** Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **referenentialeinschränkungs** -Element, das als Teil der Definition der **PublishedBy** -Zuordnung verwendet wird. Die **PublisherID-** Eigenschaft des **Book** -Entitäts Typs bildet das abhängige Ende der referenziellen Einschränkung.

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
 

 

## <a name="documentation-element-csdl"></a>Documentation-Element (CSDL)

Das **Documentation** -Element in der konzeptionellen Schema Definitions Sprache (CSDL) kann verwendet werden, um Informationen zu einem Objekt bereitzustellen, das in einem übergeordneten Element definiert ist. Wenn in einer EDMX-Datei das **Documentation** -Element ein untergeordnetes Element eines Elements ist, das als Objekt auf der Entwurfs Oberfläche des EF-Designers angezeigt wird (z. b. eine Entität, eine Zuordnung oder eine Eigenschaft), wird der Inhalt des **Dokumentations** Elements im Visual Studio- **Eigenschaften** Fenster für das Objekt angezeigt.

Das **Documentation** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   **Zusammenfassung**: eine kurze Beschreibung des übergeordneten Elements. (kein (Null) oder ein Element)
-   **LongDescription**: eine umfassende Beschreibung des übergeordneten Elements. (kein (Null) oder ein Element)
-   Anmerkungselemente. (keine (Null) oder mehrere Elemente)

### <a name="applicable-attributes"></a>Anwendbare Attribute

Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Documentation** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt das **Documentation** -Element als untergeordnetes Element eines EntityType-Elements. Wenn sich der folgende Code Ausschnitt im CSDL-Inhalt einer EDMX-Datei befindet, werden die Inhalte der Elemente **Summary** und **LongDescription** im Visual Studio- **Eigenschaften** Fenster angezeigt, wenn Sie auf den `Customer` Entitätstyp klicken.

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
 

 

## <a name="end-element-csdl"></a>End-Element (CSDL)

Das **End** -Element in konzeptioneller Schema Definitions Sprache (CSDL) kann ein untergeordnetes Element des Association-Elements oder des AssociationSet-Elements sein. In jedem Fall ist die Rolle des **End** -Elements anders, und die anwendbaren Attribute unterscheiden sich.

### <a name="end-element-as-a-child-of-the-association-element"></a>Das End-Element als untergeordnetes Objekt des Association-Elements

Ein **End** -Element (als untergeordnetes Element des **Association** -Elements) identifiziert den Entitätstyp an einem Ende einer Zuordnung und die Anzahl der Entitätstyp Instanzen, die an diesem Ende einer Zuordnung vorhanden sein können. Zuordnungsenden werden als Teil einer Zuordnung definiert. Eine Zuordnung muss genau zwei Zuordnungsenden aufweisen. Auf Entitätstypinstanzen an einem Ende einer Zuordnung kann über Navigationseigenschaften oder Fremdschlüssel zugegriffen werden, sofern sie für einen Entitätstyp verfügbar gemacht werden.  

Ein **Endelement** kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   Dokumentation (kein (null) oder ein Element)
-   OnDelete (kein (null) oder ein Element)
-   Anmerkungselemente (kein Element (null) oder mehrere Elemente)

#### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **End** -Element angewendet werden können, wenn es sich um das untergeordnete Element eines **Association** -Elements handelt.

| Attributname   | Ist erforderlich | Wert                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Typ**         | Ja         | Der Name des Entitätstyps an einem Ende der Zuordnung.                                                                                                                                                                                                                                                                                                                                                         |
| **Rolle**         | Nein          | Der Name für das Zuordnungsende. Wird kein Name angegeben, wird der Name des Entitätstyps am Zuordnungsende verwendet.                                                                                                                                                                                                                                                                                           |
| **Multiplizität** | Ja         | **1**, **0.. 1**oder, **\*** abhängig von der Anzahl der Entitätstyp Instanzen, die sich am Ende der Zuordnung befinden können. <br/> der Wert **1** gibt an, dass genau eine Entitätstyp Instanz am Zuordnungs Ende vorhanden ist. <br/> **0.. 1** gibt an, dass keine oder nur eine Entitätstyp Instanz am Zuordnungs Ende vorhanden ist. <br/> **\*** Gibt an, dass keine, eine oder mehrere Entitätstyp Instanzen am Zuordnungs Ende vorhanden sind. |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **End** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

#### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **Association** -Element, das die Zuordnung **CustomerOrders** definiert. Die **Multiplicity** multiplizitätswerte für jedes **Ende** der Zuordnung geben an, dass viele **Bestellungen** einem **Kunden**zugeordnet werden können, aber nur ein **Kunde** kann einem **Auftrag**zugeordnet werden. Außerdem gibt das **OnDelete** -Element an, dass alle **Bestellungen** , die mit einem bestimmten **Kunden** verknüpft sind und in den ObjectContext geladen wurden, gelöscht werden, wenn der **Kunde** gelöscht wird.

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a>Das End-Element als untergeordnetes Objekt des AssociationSet-Elements

Das **End** -Element gibt ein Ende eines Zuordnungs Satzes an. Das **AssociationSet** -Element muss zwei **Endelemente** enthalten. Die in einem **End** -Element enthaltenen Informationen werden für die Zuordnung eines Zuordnungs Satzes zu einer Datenquelle verwendet.

Ein **Endelement** kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   Dokumentation (kein (null) oder ein Element)
-   Anmerkungselemente (kein Element (null) oder mehrere Elemente)

> [!NOTE]
> Anmerkungselemente müssen an alle anderen untergeordneten Elemente angereiht werden. Annotation-Elemente sind nur in CSDL v2 und höher zulässig.

 

#### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **End** -Element angewendet werden können, wenn es sich um das untergeordnete Element eines **AssociationSet** -Elements handelt.

| Attributname | Ist erforderlich | Wert                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **EntitySet**  | Ja         | Der Name des **EntitySet** -Elements, das ein Ende des übergeordneten **AssociationSet** -Elements definiert. Das **EntitySet** -Element muss im gleichen Entitäts Container wie das übergeordnete **AssociationSet** -Element definiert werden. |
| **Rolle**       | Nein          | Der Name des Endes des Zuordnungssatzes. Wenn das **Role** -Attribut nicht verwendet wird, ist der Name des Zuordnungs Satzes Ende der Name der Entitätenmenge.                                                                   |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **End** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

#### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **EntityContainer** -Element mit zwei **AssociationSet** -Elementen, die jeweils zwei **Endelemente** haben:

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
 

 

## <a name="entitycontainer-element-csdl"></a>EntityContainer-Element (CSDL)

Das **EntityContainer** -Element in konzeptioneller Schema Definitions Sprache (CSDL) ist ein logischer Container für Entitätenmengen, Zuordnungs Sätze und Funktions Importe. Einem Speichermodell-Entitätscontainer wird durch das EntityContainerMapping-Element ein konzeptioneller Modellentitätscontainer zugeordnet. Ein Speichermodell-Entitätscontainer beschreibt die Struktur der Datenbank: Entitätssätze beschreiben Tabellen, Zuordnungssätze beschreiben Fremdschlüsseleinschränkungen, und Funktionsimporte beschreiben gespeicherte Prozeduren in einer Datenbank.

Ein **EntityContainer** -Element kann über 0 (null) oder ein Dokumentations Element verfügen. Wenn ein **Documentation** -Element vorhanden ist, muss es allen **EntitySet**-, **AssociationSet**-und **FunctionImport** -Elementen vorangestellt sein.

Ein **EntityContainer** -Element kann über 0 (null) oder mehrere der folgenden untergeordneten Elemente verfügen (in der angegebenen Reihenfolge):

-   EntitySet
-   AssociationSet
-   FunctionImport
-   Anmerkungselemente

Sie können ein **EntityContainer** -Element erweitern, um den Inhalt eines anderen **EntityContainer** -Elements einzubeziehen, das sich im selben Namespace befindet. Um den Inhalt eines anderen **EntityContainer**einzuschließen, legen Sie im verweisenden **EntityContainer** -Element den Wert des Attributs " **erweitert** " auf den Namen des **EntityContainer** -Elements fest, das Sie einschließen möchten. Alle untergeordneten Elemente des enthaltenen **EntityContainer** -Elements werden als untergeordnete Elemente des verweisenden **EntityContainer** -Elements behandelt.

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **using** -Element angewendet werden können.

| Attributname | Ist erforderlich | Wert                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| **Name**       | Ja         | Der Name des Entitätscontainers.                               |
| **Erweitern**    | Nein          | Der Name eines anderen Entitätscontainers innerhalb des gleichen Namespaces. |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **EntityContainer** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **EntityContainer** -Element, das drei Entitätenmengen und zwei Zuordnungs Sätze definiert.

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
 

 

## <a name="entityset-element-csdl"></a>EntitySet-Element (CSDL)

Das **EntitySet** -Element in der konzeptionellen Schema Definitions Sprache ist ein logischer Container für Instanzen eines Entitäts Typs und Instanzen eines beliebigen Typs, der von diesem Entitätstyp abgeleitet wird. Die Beziehung zwischen einem Entitätstyp und einem Entitätssatz ist zur Beziehung zwischen einer Zeile und einer Tabelle in einer relationalen Datenbank analog. Wie eine Zeile definiert ein Entitätstyp einen Satz verknüpfter Daten, und ebenso wie eine Tabelle enthält ein Entitätssatz Instanzen dieser Definition. Ein Entitätssatz stellt ein Konstrukt zum Gruppieren von Entitätstypinstanzen bereit, damit diese verwandten Datenstrukturen in einer Datenquelle zugeordnet werden können.  

Für einen bestimmten Entitätstyp kann mindestens ein Entitätssatz definiert werden.

> [!NOTE]
> Der EF-Designer unterstützt keine konzeptionellen Modelle, die mehrere Entitätenmengen pro Typ enthalten.

 

Das **EntitySet** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   Dokumentationselement (kein (null) oder ein Element zugelassen)
-   Anmerkungselemente (kein (null) Element oder mehrere Elemente zugelassen)

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **EntitySet** -Element angewendet werden können.

| Attributname | Ist erforderlich | Wert                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| **Name**       | Ja         | Der Name des Entitätssatzes.                                                              |
| **EntityType** | Ja         | Der vollqualifizierte Name des Entitätstyps, für den der Entitätssatz Instanzen enthält. |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **EntitySet** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **EntityContainer** -Element mit drei **EntitySet** -Elementen:

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
 

Pro Typ (MEST) können mehrere Entitätssätze definiert werden. Im folgenden Beispiel wird ein Entitäts Container mit zwei Entitätenmengen für den **Book** -Entitätstyp definiert:

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
 

 

## <a name="entitytype-element-csdl"></a>EntityType-Element (CSDL)

Das **EntityType** -Element stellt die Struktur eines Konzepts der obersten Ebene, z. b. einen Kunden oder eine Bestellung, in einem konzeptionellen Modell dar. Ein Entitätstyp ist eine Vorlage für Instanzen von Entitätstypen in einer Anwendung. Jede Vorlage enthält die folgenden Informationen:

-   Eine eindeutige Bezeichnung. (Erforderlich.)
-   Ein Entitätsschlüssel, der von einem oder mehreren Eigenschaften definiert wird. (Erforderlich.)
-   Eigenschaften für enthaltene Daten. (Optional.)
-   Navigationseigenschaften, die die Navigation von einem Ende einer Zuordnung zum anderen Ende ermöglichen. (Optional.)

In einer Anwendung stellt eine Instanz eines Entitätstyps ein spezielles Objekt dar, wie etwa einen bestimmten Kunden oder eine Bestellung. Jede Instanz eines Entitätstyps muss über einen eindeutigen Entitätsschlüssel innerhalb eines Entitätssatzes verfügen.

Zwei Instanzen eines Entitätstyps werden nur dann als gleich betrachtet, wenn sie vom selben Typ sind und die Werte ihrer Entitätsschlüssel übereinstimmen.

Ein **EntityType** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   Dokumentation (kein (null) oder ein Element)
-   Key (kein (null) oder ein Element)
-   Property (0 (null) oder mehr Elemente)
-   NavigationProperty (kein Element (null) oder mehrere Elemente)
-   Anmerkungselemente (kein Element (null) oder mehrere Elemente)

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **EntityType** -Element angewendet werden können.

| Attributname                                                                                                                                  | Ist erforderlich | Wert                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| **Name**                                                                                                                                        | Ja         | Der Name des Entitätstyps.                                                                     |
| **BaseType**                                                                                                                                    | Nein          | Der Name eines anderen Entitätstyps, der der Basistyp des Entitätstyps ist, der definiert wird.  |
| **Kter**                                                                                                                                    | Nein          | **True** oder **false**, abhängig davon, ob der Entitätstyp ein abstrakter Typ ist.                 |
| **OpenType**                                                                                                                                    | Nein          | **True** oder **false** , abhängig davon, ob der Entitätstyp ein offener Entitätstyp ist. <br/> [!NOTE] |
| > das **OpenType** -Attribut gilt nur für Entitäts Typen, die in konzeptionellen Modellen definiert sind, die mit ADO.NET-Data Services verwendet werden. |             |                                                                                                  |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **EntityType** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **EntityType** -Element mit drei **Eigenschafts** Elementen und zwei **NavigationProperty** -Elementen:

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
 

 

## <a name="enumtype-element-csdl"></a>EnumType-Element (CSDL)

Das **enumType** -Element stellt einen enumerierten Typ dar.

Ein **enumType** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   Dokumentation (kein (null) oder ein Element)
-   Member (0 (null) oder mehr Elemente)
-   Anmerkungselemente (kein Element (null) oder mehrere Elemente)

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **enumType** -Element angewendet werden können.

| Attributname     | Ist erforderlich | Wert                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**           | Ja         | Der Name des Entitätstyps.                                                                                                                                                                  |
| **IsFlags**        | Nein          | **True** oder **false**, abhängig davon, ob der Enumeration-Typ als Satz von Flags verwendet werden kann. Der Standardwert ist **false.**                                                                     |
| **Underlyingtype** | Nein          | **Edm. Byte**, **Edm. Int16**, **Edm. Int32**, **Edm. Int64** oder **Edm. SByte** , das den Wertebereich des Typs definiert. Der Standard zugrunde liegende Typ der Enumerationselemente ist **Edm. Int32.**. |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **enumType** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **enumType** -Element mit drei **Member** -Elementen:

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a>Function-Element (CSDL)

Das **Function** -Element in der konzeptionellen Schema Definitions Sprache (CSDL) wird verwendet, um Funktionen im konzeptionellen Modell zu definieren oder zu deklarieren. Eine Funktion wird mit einem DefiningExpression-Element definiert.  

Ein **Function** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   Dokumentation (kein (null) oder ein Element)
-   Parameter (kein Element (null) oder mehrere Elemente)
-   DefiningExpression (kein (null) oder ein Element)
-   ReturnType (Funktion) (kein (null) oder ein Element)
-   Anmerkungselemente (kein Element (null) oder mehrere Elemente)

Ein Rückgabetyp für eine Funktion muss entweder mit dem **returnType** -Element (Function) oder dem **returnType** -Attribut angegeben werden (siehe unten), aber nicht mit beiden. Die möglichen Rückgabetypen sind alle EdmSimpleType-Typen, Entitätstypen, komplexe Typen, Zeilentypen (oder eine Auflistung eines dieser Typen) oder Ref-Typen.

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Function** -Element angewendet werden können.

| Attributname | Ist erforderlich | Wert                              |
|:---------------|:------------|:-----------------------------------|
| **Name**       | Ja         | Der Name der Funktion.          |
| **ReturnType** | Nein          | Der von der Funktion zurückgegebene Typ. |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Function** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird ein **Function** -Element verwendet, um eine Funktion zu definieren, die die Anzahl der Jahre zurückgibt, seit ein Dozenten eingestellt wurde.

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a>FunctionImport-Element (CSDL)

Das **FunctionImport** -Element in konzeptioneller Schema Definitions Sprache (CSDL) stellt eine Funktion dar, die in der Datenquelle definiert ist, aber für-Objekte über das konzeptionelle Modell verfügbar ist. Ein Function-Element im Speichermodell kann z. B. verwendet werden, um eine gespeicherte Prozedur in einer Datenbank darzustellen. Ein **FunctionImport** -Element im konzeptionellen Modell stellt die entsprechende Funktion in einer Entity Framework Anwendung dar und wird mithilfe des FunctionImportMapping-Elements der Speichermodell Funktion zugeordnet. Wird die Funktion in der Anwendung aufgerufen, wird die entsprechende gespeicherte Prozedur in der Datenbank ausgeführt.

Das **FunctionImport** -Element kann über die folgenden untergeordneten Elemente verfügen (in der angegebenen Reihenfolge):

-   Dokumentation (kein (null) oder ein Element zugelassen)
-   Parameter (kein Element (null) oder mehrere Elemente zugelassen)
-   Anmerkungselemente (kein (null) Element oder mehrere Elemente zugelassen)
-   ReturnType (FunctionImport) (0 (null) oder mehr Elemente zulässig)

Für jeden Parameter, den die Funktion akzeptiert, muss ein **Parameter** Element definiert werden.

Ein Rückgabetyp für eine Funktion muss entweder mit dem **returnType** -Element (FunctionImport) oder dem **returnType** -Attribut angegeben werden (siehe unten), aber nicht mit beiden. Der Rückgabetyp Wert muss eine Auflistung von edmsimpletype, EntityType oder complexType sein.

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **FunctionImport** -Element angewendet werden können.

| Attributname   | Ist erforderlich | Wert                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**         | Ja         | Der Name der importierten Funktion.                                                                                                                                                                    |
| **ReturnType**   | Nein          | Der Typ, den die Funktion zurückgibt. Verwenden Sie dieses Attribut nicht, wenn die Funktion keinen Wert zurückgibt. Andernfalls muss der Wert eine Auflistung von complexType, EntityType oder edmsimpletype sein.        |
| **EntitySet**    | Nein          | Wenn die Funktion eine Auflistung von Entitäts Typen zurückgibt, muss der Wert von **EntitySet** der Entitätenmenge angehören, zu der die Auflistung gehört. Andernfalls darf das Attribut **EntitySet** nicht verwendet werden. |
| **IsComposable** | Nein          | Wenn der Wert auf true festgelegt ist, ist die Funktion zusammensetzbar (Tabellenwert Funktion) und kann in einer LINQ-Abfrage verwendet werden.Die Standardeinstellung ist **false**.                                                           |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **FunctionImport** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **FunctionImport** -Element, das einen Parameter akzeptiert und eine Auflistung von Entitäts Typen zurückgibt:

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a>Key-Element (CSDL)

Das **Key** -Element ist ein untergeordnetes Element des EntityType-Elements und definiert einen *Entitäts Schlüssel* (eine Eigenschaft oder einen Satz von Eigenschaften eines Entitäts Typs, der die Identität bestimmt). Die Eigenschaften, die einen Entitätsschlüssel bilden, werden zur Entwurfszeit ausgewählt. Die Werte von Entitätsschlüsseleigenschaften müssen zur Laufzeit eindeutig eine Entitätstypinstanz innerhalb eines Entitätssatzes identifizieren. Die Eigenschaften, die einen Entitätsschlüssel bilden, sollten so ausgewählt werden, dass die Eindeutigkeit von Instanzen in einem Entitätssatz gewährleistet ist. Das **Key** -Element definiert einen Entitäts Schlüssel, indem auf eine oder mehrere Eigenschaften eines Entitäts Typs verwiesen wird.

Das **Key** -Element kann die folgenden untergeordneten Elemente aufweisen:

-   PropertyRef (mindestens ein Element)
-   Anmerkungselemente (kein Element (null) oder mehrere Elemente)

### <a name="applicable-attributes"></a>Anwendbare Attribute

Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Schlüssel** Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird ein Entitätstyp mit dem Namen **Book**definiert. Der Entitäts Schlüssel wird definiert, indem auf die **ISBN** -Eigenschaft des Entitäts Typs verwiesen wird.

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
 

Die **ISBN** -Eigenschaft ist eine gute Wahl für den Entitäts Schlüssel, weil eine internationale Standard Buchnummer (ISBN) ein Buch eindeutig identifiziert.

Das folgende Beispiel zeigt einen Entitätstyp (**Author**), der über einen Entitäts Schlüssel verfügt, der aus zwei Eigenschaften, **Name** und **Adresse**besteht.

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
 

Die Verwendung von **Name** und **Adresse** für den Entitäts Schlüssel ist eine sinnvolle Wahl, da es unwahrscheinlich ist, dass sich zwei Autoren mit demselben Namen an derselben Adresse befinden. Dieser Entitätsschlüssel garantiert jedoch nicht absolut eindeutige Entitätsschlüssel in einem Entitätssatz. In diesem Fall empfiehlt es sich, eine Eigenschaft hinzuzufügen, z. **b. Autorität**, die zur eindeutigen Identifizierung eines Autors verwendet werden kann.

 

## <a name="member-element-csdl"></a>Member-Element (CSDL)

Das **Member** -Element ist ein untergeordnetes Element des enumType-Elements und definiert einen Member des enumerierten Typs.

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **FunctionImport** -Element angewendet werden können.

| Attributname | Ist erforderlich | Wert                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**       | Ja         | Der Name des Members.                                                                                                                                                                  |
| **Wert**      | Nein          | Der Wert des Members. Standardmäßig hat der erste Member den Wert 0, und der Wert jedes nachfolgenden Enumerators wird um 1 erhöht. Es können mehrere Member mit denselben Werten vorhanden sein. |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **FunctionImport** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **enumType** -Element mit drei **Member** -Elementen:

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a>NavigationProperty-Element (CSDL)

Ein **NavigationProperty** -Element definiert eine Navigations Eigenschaft, die einen Verweis auf das andere Ende einer Zuordnung bereitstellt. Im Gegensatz zu mit dem Property-Element definierten Eigenschaften werden von Navigationseigenschaften Form und Eigenschaften von Daten nicht definiert. Sie bieten eine Möglichkeit, eine Zuordnung zwischen zwei Entitätstypen zu navigieren.

Beachten Sie, dass Navigationseigenschaften für beide Entitätstypen an den Enden einer Zuordnung optional sind. Wenn Sie für einen Entitätstyp am Ende einer Zuordnung eine Navigationseigenschaft definieren, muss keine Navigationseigenschaft für den Entitätstyp am anderen Ende der Zuordnung definiert werden.

Der von einer Navigationseigenschaft zurückgegebene Datentyp wird von der Multiplizität des Remotezuordnungsendes bestimmt. Angenommen, eine Navigations Eigenschaft, **ordersnavprop**, ist für einen **Customer** -Entitätstyp vorhanden und navigiert zu einer 1: n-Zuordnung zwischen **Customer** und **Order**. Da das Remote Zuordnungs Ende für die Navigations Eigenschaft eine Multiplizität many ( \* ) aufweist, ist sein Datentyp eine Auflistung (der **Reihenfolge**). Wenn eine Navigations Eigenschaft, **customernavprop**, auf dem **Order** -Entitätstyp vorhanden ist, wäre der Datentyp " **Customer** ", da die Multiplizität des Remote Endes "eins" (1) ist.

Ein **NavigationProperty** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   Dokumentation (kein (null) oder ein Element)
-   Anmerkungselemente (kein Element (null) oder mehrere Elemente)

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **NavigationProperty** -Element angewendet werden können.

| Attributname   | Ist erforderlich | Wert                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**         | Ja         | Der Name der Navigationseigenschaft.                                                                                                                                                                                                             |
| **Relationship** | Ja         | Der Name einer Zuordnung, die sich innerhalb des Bereichs des Modells befindet.                                                                                                                                                                                |
| **ToRole**       | Ja         | Das Ende der Zuordnung, an dem die Navigation endet. Der Wert des Attributs " **Tor** " muss mit dem Wert eines der **Rollen** Attribute übereinstimmen, die für eine der Zuordnungs enden definiert sind (definiert im AssociationEnd-Element).       |
| **FromRole**     | Ja         | Das Ende der Zuordnung, an dem die Navigation beginnt. Der Wert des **FromRole** -Attributs muss mit dem Wert eines der **Rollen** Attribute übereinstimmen, die für eine der Zuordnungs enden definiert sind (definiert im AssociationEnd-Element). |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **NavigationProperty** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird ein Entitätstyp (**Book**) mit zwei Navigations Eigenschaften (**PublishedBy** und **Schreibweise**) definiert:

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
 

 

## <a name="ondelete-element-csdl"></a>OnDelete-Element (CSDL)

Das **OnDelete** -Element in konzeptioneller Schema Definitions Sprache (CSDL) definiert das Verhalten, das mit einer Zuordnung verbunden ist. Wenn das **Action** -Attribut an einem Ende einer Zuordnung auf **Cascade** festgelegt ist, werden verwandte Entitäts Typen am anderen Ende der Zuordnung gelöscht, wenn der Entitätstyp am ersten Ende gelöscht wird. Wenn die Zuordnung zwischen zwei Entitäts Typen eine Primärschlüssel-zu-Primärschlüssel-Beziehung ist, wird ein geladenes abhängiges Objekt gelöscht, wenn das Prinzipal Objekt am anderen Ende der Zuordnung unabhängig von der **OnDelete** -Spezifikation gelöscht wird.  

> [!NOTE]
> Das **OnDelete** -Element wirkt sich nur auf das Laufzeitverhalten einer Anwendung aus. Dies wirkt sich nicht auf das Verhalten in der Datenquelle aus. Das in der Datenquelle definierte Verhalten sollte dem in der Anwendung definierten Verhalten entsprechen.

 

Ein **OnDelete** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   Dokumentation (kein (null) oder ein Element)
-   Anmerkungselemente (kein Element (null) oder mehrere Elemente)

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **OnDelete** -Element angewendet werden können.

| Attributname | Ist erforderlich | Wert                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Aktion**     | Ja         | **Cascade** oder **None**. Wenn **Cascade**verwendet wird, werden abhängige Entitäts Typen gelöscht, wenn der Prinzipal Entitätstyp gelöscht wird. Wenn **keine**, werden abhängige Entitäts Typen nicht gelöscht, wenn der Prinzipal Entitätstyp gelöscht wird. |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Association** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **Association** -Element, das die Zuordnung **CustomerOrders** definiert. Das **OnDelete** -Element gibt an, dass alle **Bestellungen** , die mit einem bestimmten **Kunden** verknüpft sind und in den ObjectContext geladen wurden, gelöscht werden, wenn der **Kunde** gelöscht wird.

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a>Parameter-Element (CSDL)

Das **Parameter** -Element in konzeptioneller Schema Definitions Sprache (CSDL) kann ein untergeordnetes Element des FunctionImport-Elements oder des Function-Elements sein.

### <a name="functionimport-element-application"></a>FunctionImport-Element-Anwendung

Ein **Parameter** Element (als untergeordnetes Element des **FunctionImport** -Elements) wird verwendet, um Eingabe-und Ausgabeparameter für Funktions Importe zu definieren, die in CSDL deklariert werden.

Das **Parameter** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   Dokumentation (kein (null) oder ein Element zugelassen)
-   Anmerkungselemente (kein (null) Element oder mehrere Elemente zugelassen)

#### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Parameter** -Element angewendet werden können.

| Attributname | Ist erforderlich | Wert                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**       | Ja         | Der Name des Parameters.                                                                                                                                                                                                      |
| **Typ**       | Ja         | Der Parametertyp. Der Wert muss ein **EDMSimpleType** oder komplexer Typ sein, der im Gültigkeitsbereich des Modells liegt.                                                                                                             |
| **Mode**       | Nein          | **In**, out oder **INOUT** , je nachdem, ob der Parameter ein Eingabe-, Ausgabe-oder Eingabe- **/Ausgabeparameter**ist.                                                                                                                |
| **MaxLength**  | Nein          | Die maximal zulässige Länge des Parameters.                                                                                                                                                                                    |
| **Genauigkeit**  | Nein          | Die Genauigkeit des Parameters.                                                                                                                                                                                                 |
| **Skalierung**      | Nein          | Der Maßstab des Parameters.                                                                                                                                                                                                     |
| **SRID**       | Nein          | Verweis Bezeichner für räumliche Systeme. Nur für Parameter räumlicher Typen gültig. Weitere Informationen finden Sie unter [SRID](https://en.wikipedia.org/wiki/SRID) und [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Parameter** Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

#### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **FunctionImport** -Element mit einem untergeordneten **Parameter** -Element. Die Funktion akzeptiert einen Eingabeparameter und gibt eine Auflistung von Entitätstypen zurück.

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a>Function-Element-Anwendung

Ein **Parameter** Element (als untergeordnetes Element des **Function** -Elements) definiert Parameter für Funktionen, die in einem konzeptionellen Modell definiert oder deklariert werden.

Das **Parameter** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   Documentation (kein (null) oder ein Element)
-   CollectionType (kein (null) oder ein Element)
-   ReferenceType (kein (null) oder ein Element)
-   RowType (kein (null) oder ein Element)

> [!NOTE]
> Nur eines der **CollectionType**-, **ReferenceType**-oder **RowType** -Elemente kann ein untergeordnetes Element eines **Property** -Elements sein.

 

-   Anmerkungselemente (kein (null) Element oder mehrere Elemente zugelassen)

> [!NOTE]
> Anmerkungselemente müssen an alle anderen untergeordneten Elemente angereiht werden. Annotation-Elemente sind nur in CSDL v2 und höher zulässig.

 

#### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Parameter** -Element angewendet werden können.

| Attributname   | Ist erforderlich | Wert                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**         | Ja         | Der Name des Parameters.                                                                                                                                                                                                      |
| **Typ**         | Nein          | Der Parametertyp. Ein Parameter kann einer der folgenden Typen (oder Auflistungen dieser Typen) sein: <br/> **EdmSimpleType** <br/> Entitätstyp <br/> Komplexer Typ <br/> Zeilentyp <br/> Verweistyp                             |
| **NULL zulassen**     | Nein          | **True** (Standardwert) oder **false** , abhängig davon, ob die Eigenschaft einen **null** -Wert aufweisen kann.                                                                                                                          |
| **DefaultValue** | Nein          | Der Standardwert der Eigenschaft.                                                                                                                                                                                              |
| **MaxLength**    | Nein          | Maximale Länge des Eigenschaftswerts.                                                                                                                                                                                       |
| **FixedLength**  | Nein          | **True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Zeichenfolge mit fester Länge gespeichert wird.                                                                                                                          |
| **Genauigkeit**    | Nein          | Die Genauigkeit des Eigenschaftswerts.                                                                                                                                                                                            |
| **Skalierung**        | Nein          | Die Skalierung des Eigenschaftswerts.                                                                                                                                                                                                |
| **SRID**         | Nein          | Verweis Bezeichner für räumliche Systeme. Nur für Eigenschaften räumlicher Typen gültig. Weitere Informationen finden Sie unter [SRID](https://en.wikipedia.org/wiki/SRID) und [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **Unicode**      | Nein          | **True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Unicode-Zeichenfolge gespeichert wird.                                                                                                                               |
| **Sortierung**    | Nein          | Eine Zeichenfolge, die angibt, welche Sortierreihenfolge in der Datenquelle verwendet wird.                                                                                                                                                   |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Parameter** Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

#### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **Function** -Element, das ein untergeordnetes **Parameter** Element verwendet, um einen Funktions Parameter zu definieren.

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a>Principal-Element (CSDL)

Das **Principal** -Element in konzeptioneller Schema Definitions Sprache (CSDL) ist ein untergeordnetes Element für das referentialeinschränkung-Element, das das Prinzipal Ende einer referenziellen Einschränkung definiert. Ein **referentialeinschränkung** -Element definiert Funktionen, die einer Einschränkung der referenziellen Integrität in einer relationalen Datenbank ähneln. So wie eine Spalte (oder Spalten) einer Datenbanktabelle auf den Primärschlüssel einer anderen Tabelle verweisen kann, kann eine Eigenschaft (oder Eigenschaften) eines Entitätstyps auf den Entitätsschlüssel eines anderen Entitätstyps verweisen. Der Entitätstyp, auf den verwiesen wird, wird als *Prinzipal Ende* der Einschränkung bezeichnet. Der Entitätstyp, der auf das Prinzipal Ende verweist, wird als *abhängiges Ende* der Einschränkung bezeichnet. **PropertyRef** -Elemente werden verwendet, um anzugeben, auf welche Schlüssel vom abhängigen Ende verwiesen wird.

Das **Principal** -Element kann über die folgenden untergeordneten Elemente verfügen (in der angegebenen Reihenfolge):

-   PropertyRef (mindestens ein Element)
-   Anmerkungselemente (kein Element (null) oder mehrere Elemente)

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Principal** -Element angewendet werden können.

| Attributname | Ist erforderlich | Wert                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| **Rolle**       | Ja         | Der Name des Entitätstyps am Prinzipalende der Zuordnung. |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Principal** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **referentialeinschränkungs** -Element, das Teil der Definition der **PublishedBy** -Zuordnung ist. Die **ID** -Eigenschaft des **Verleger** Entitäts Typs bildet das Prinzipal Ende der referenziellen Einschränkung.

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
 

 

## <a name="property-element-csdl"></a>Property-Element (CSDL)

Das **Property** -Element in konzeptioneller Schema Definitions Sprache (CSDL) kann ein untergeordnetes Element des EntityType-Elements, das complexType-Element oder das RowType-Element sein.

### <a name="entitytype-and-complextype-element-applications"></a>EntityType- und ComplexType-Element-Anwendungen

Eigenschaften Elemente (als unter **geordnete Elemente von** **EntityType** -oder **complexType** -Elementen) definieren die Form und die Eigenschaften der Daten, die eine Entitätstyp Instanz oder eine komplexe Typinstanz enthalten wird. Eigenschaften in einem konzeptionellen Modell sind analog zu den Eigenschaften, die für eine Klasse definiert werden. So wie Eigenschaften die Form einer Klasse definieren und Informationen zu Objekten enthalten definieren Eigenschaften in einem konzeptionellen Modell die Form eines Entitätstyps und enthalten Informationen zu Entitätstypinstanzen.

Das **Property** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   Dokumentationselement (kein (null) oder ein Element zugelassen)
-   Anmerkungselemente (kein (null) Element oder mehrere Elemente zugelassen)

Die folgenden Facetten können auf ein **Property** -Element angewendet werden **: Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **COLLATIONS**,-zustandcymode. **ConcurrencyMode** Facets sind XML-Attribute, die Informationen über die Speicherung von Eigenschaftswerten im Datenspeicher bereitstellen.

> [!NOTE]
> Facetten können nur auf Eigenschaften vom Typ " **edmsimpletype**" angewendet werden.

 

#### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Property** -Element angewendet werden können.

| Attributname                                                         | Ist erforderlich | Wert                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**                                                               | Ja         | Der Name der Eigenschaft.                                                                                                                                                                                                       |
| **Typ**                                                               | Ja         | Der Typ des Eigenschaftswerts. Der Typ des Eigenschaftswerts muss ein **EDMSimpleType** oder ein komplexer Typ sein (erkennbar am vollqualifizierten Namen), der im Gültigkeitsbereich des Modells liegt.                                                 |
| **NULL zulassen**                                                           | Nein          | **True** (Standardwert) oder <strong>False</strong> abhängig davon, ob die Eigenschaft einen NULL-Wert haben kann. <br/> [!NOTE]                                                                                                   |
| > in CSDL v1 muss eine komplexe Typeigenschaft über verfügen `Nullable="False"` . |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                       | Nein          | Der Standardwert der Eigenschaft.                                                                                                                                                                                              |
| **MaxLength**                                                          | Nein          | Maximale Länge des Eigenschaftswerts.                                                                                                                                                                                       |
| **FixedLength**                                                        | Nein          | **True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Zeichenfolge mit fester Länge gespeichert wird.                                                                                                                          |
| **Genauigkeit**                                                          | Nein          | Die Genauigkeit des Eigenschaftswerts.                                                                                                                                                                                            |
| **Skalierung**                                                              | Nein          | Die Skalierung des Eigenschaftswerts.                                                                                                                                                                                                |
| **SRID**                                                               | Nein          | Verweis Bezeichner für räumliche Systeme. Nur für Eigenschaften räumlicher Typen gültig. Weitere Informationen finden Sie unter [SRID](https://en.wikipedia.org/wiki/SRID) und [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **Unicode**                                                            | Nein          | **True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Unicode-Zeichenfolge gespeichert wird.                                                                                                                               |
| **Sortierung**                                                          | Nein          | Eine Zeichenfolge, die angibt, welche Sortierreihenfolge in der Datenquelle verwendet wird.                                                                                                                                                   |
| **ConcurrencyMode**                                                    | Nein          | **None** (Standardwert) oder **Fixed**. Wenn der Wert auf **Fixed**festgelegt ist, wird der Wert der Eigenschaft zu Überprüfungen auf vollständige Parallelität verwendet.                                                                                  |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Property** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

#### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **EntityType** -Element mit drei **Eigenschafts** Elementen:

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
 

Das folgende Beispiel zeigt ein **complexType** -Element mit fünf **Eigenschafts** Elementen:

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a>RowType-Element-Anwendung

**Eigenschafts** Elemente (als untergeordnete Elemente eines **RowType** -Elements) definieren die Form und die Eigenschaften von Daten, die an eine Modell definierte Funktion übermittelt oder von dieser zurückgegeben werden können.  

Das **Property** -Element kann genau eines der folgenden untergeordneten Elemente aufweisen:

-   CollectionType
-   ReferenceType
-   RowType

Das **Property** -Element kann eine beliebige Anzahl untergeordneter Anmerkung-Elemente aufweisen.

> [!NOTE]
> Annotation-Elemente sind nur in CSDL v2 und höher zulässig.

 

#### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Property** -Element angewendet werden können.

| Attributname                                                     | Ist erforderlich | Wert                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**                                                           | Ja         | Der Name der Eigenschaft.                                                                                                                                                                                                       |
| **Typ**                                                           | Ja         | Der Typ des Eigenschaftswerts.                                                                                                                                                                                                 |
| **NULL zulassen**                                                       | Nein          | **True** (Standardwert) oder **False** abhängig davon, ob die Eigenschaft einen NULL-Wert haben kann. <br/> [!NOTE]                                                                                                                |
| > in CSDL v1 muss eine komplexe Typeigenschaft über verfügen `Nullable="False"` . |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                   | Nein          | Der Standardwert der Eigenschaft.                                                                                                                                                                                              |
| **MaxLength**                                                      | Nein          | Maximale Länge des Eigenschaftswerts.                                                                                                                                                                                       |
| **FixedLength**                                                    | Nein          | **True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Zeichenfolge mit fester Länge gespeichert wird.                                                                                                                          |
| **Genauigkeit**                                                      | Nein          | Die Genauigkeit des Eigenschaftswerts.                                                                                                                                                                                            |
| **Skalierung**                                                          | Nein          | Die Skalierung des Eigenschaftswerts.                                                                                                                                                                                                |
| **SRID**                                                           | Nein          | Verweis Bezeichner für räumliche Systeme. Nur für Eigenschaften räumlicher Typen gültig. Weitere Informationen finden Sie unter [SRID](https://en.wikipedia.org/wiki/SRID) und [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **Unicode**                                                        | Nein          | **True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Unicode-Zeichenfolge gespeichert wird.                                                                                                                               |
| **Sortierung**                                                      | Nein          | Eine Zeichenfolge, die angibt, welche Sortierreihenfolge in der Datenquelle verwendet wird.                                                                                                                                                   |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Property** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

#### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt **Eigenschafts** Elemente, die verwendet werden, um die Form des Rückgabe Typs einer Modell definierten Funktion zu definieren.

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
 

 

## <a name="propertyref-element-csdl"></a>PropertyRef-Element (CSDL)

Das **PropertyRef** -Element in konzeptioneller Schema Definitions Sprache (CSDL) verweist auf eine Eigenschaft eines Entitäts Typs, um anzugeben, dass die-Eigenschaft eine der folgenden Rollen ausführt:

-   Ein Teil des Entitätsschlüssels (eine Eigenschaft oder ein Satz von Eigenschaften eines Entitätstyps, der die Identität bestimmt). Ein oder mehrere **PropertyRef** -Elemente können verwendet werden, um einen Entitäts Schlüssel zu definieren.
-   Das abhängige Ende oder Prinzipalende einer referenziellen Einschränkung.

Das **PropertyRef** -Element kann nur Anmerkung-Elemente (0 (null) oder mehr) als untergeordnete Elemente aufweisen.

> [!NOTE]
> Annotation-Elemente sind nur in CSDL v2 und höher zulässig.

 

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **PropertyRef** -Element angewendet werden können.

| Attributname | Ist erforderlich | Wert                                |
|:---------------|:------------|:-------------------------------------|
| **Name**       | Ja         | Der Name der referenzierten Eigenschaft. |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **PropertyRef** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird ein Entitätstyp (**Book**) definiert. Der Entitäts Schlüssel wird definiert, indem auf die **ISBN** -Eigenschaft des Entitäts Typs verwiesen wird.

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
 

Im nächsten Beispiel werden zwei **PropertyRef** -Elemente verwendet, um anzugeben, dass zwei Eigenschaften (**ID** und **PublisherID**) das Prinzipal Ende und das abhängige Ende einer referenziellen Einschränkung sind.

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
 

 

## <a name="referencetype-element-csdl"></a>ReferenceType-Element (CSDL)

Das **ReferenceType** -Element in konzeptioneller Schema Definitions Sprache (CSDL) gibt einen Verweis auf einen Entitätstyp an. Das **ReferenceType** -Element kann ein untergeordnetes Element der folgenden Elemente sein:

-   ReturnType (Funktion)
-   Parameter
-   CollectionType

Das **ReferenceType** -Element wird verwendet, wenn ein Parameter oder ein Rückgabetyp für eine Funktion definiert wird.

Ein **ReferenceType** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   Dokumentation (kein (null) oder ein Element)
-   Anmerkungselemente (kein Element (null) oder mehrere Elemente)

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **ReferenceType** -Element angewendet werden können.

| Attributname | Ist erforderlich | Wert                                         |
|:---------------|:------------|:----------------------------------------------|
| **Typ**       | Ja         | Der Name des Entitätstyps, auf den verwiesen wird. |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **ReferenceType** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt das **ReferenceType** -Element, das als untergeordnetes Element eines **Parameter** -Elements in einer Modell definierten Funktion verwendet wird, die einen Verweis auf den Entitätstyp **Person** akzeptiert:

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
 

Das folgende Beispiel zeigt das **ReferenceType** -Element, das als untergeordnetes Element eines **returnType** (Function)-Elements in einer Modell definierten Funktion verwendet wird, die einen Verweis auf einen **Person** -Entitätstyp zurückgibt:

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
 

 

## <a name="referentialconstraint-element-csdl"></a>ReferentialConstraint-Element (CSDL)

Ein **referentialeinschränkungs** -Element in konzeptioneller Schema Definitions Sprache (CSDL) definiert Funktionen, die einer Einschränkung der referenziellen Integrität in einer relationalen Datenbank ähneln. So wie eine Spalte (oder Spalten) einer Datenbanktabelle auf den Primärschlüssel einer anderen Tabelle verweisen kann, kann eine Eigenschaft (oder Eigenschaften) eines Entitätstyps auf den Entitätsschlüssel eines anderen Entitätstyps verweisen. Der Entitätstyp, auf den verwiesen wird, wird als *Prinzipal Ende* der Einschränkung bezeichnet. Der Entitätstyp, der auf das Prinzipal Ende verweist, wird als *abhängiges Ende* der Einschränkung bezeichnet.

Wenn ein Fremdschlüssel, der auf einem Entitätstyp verfügbar gemacht wird, auf eine Eigenschaft eines anderen Entitäts Typs verweist, definiert das **referentialeinschränkung** -Element eine Zuordnung zwischen den beiden Entitäts Typen. Da das **referentialeinschränkung** -Element Informationen über die Beziehung zweier Entitäts Typen bereitstellt, ist in der Mapping-Spezifikationssprache (MSL) kein entsprechendes AssociationSetMapping-Element erforderlich. Eine Zuordnung zwischen zwei Entitäts Typen, für die keine Fremdschlüssel verfügbar gemacht werden, muss über ein entsprechendes **AssociationSetMapping** -Element verfügen, um der Datenquelle Zuordnungs Informationen zuzuordnen.

Wenn ein Fremdschlüssel nicht für einen Entitätstyp verfügbar gemacht wird, kann das **referentialeinschränkungs** -Element nur eine Primärschlüssel-zu-Primärschlüssel-Einschränkung zwischen dem Entitätstyp und einem anderen Entitätstyp definieren.

Ein **referentialeinschränkung** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   Dokumentation (kein (null) oder ein Element)
-   Prinzipal (genau ein Element)
-   Abhängig (genau ein Element)
-   Anmerkungselemente (kein Element (null) oder mehrere Elemente)

### <a name="applicable-attributes"></a>Anwendbare Attribute

Das **referentialeinschränkungs** -Element kann über eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) verfügen. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **referenentialeinschränkungs** -Element, das als Teil der Definition der **PublishedBy** -Zuordnung verwendet wird.

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
 

 

## <a name="returntype-function-element-csdl"></a>ReturnType (Function)-Element (CSDL)

Das **returnType** (Function)-Element in konzeptioneller Schema Definitions Sprache (CSDL) gibt den Rückgabetyp für eine Funktion an, die in einem Function-Element definiert ist. Ein Funktions Rückgabetyp kann auch mit einem **returnType** -Attribut angegeben werden.

Rückgabe Typen können alle **edmsimpletype**-Typen, Entitäts Typen, komplexe Typen, Zeilen Typen, Ref-Typen oder eine Auflistung von einem dieser Typen sein.

Der Rückgabetyp einer Funktion kann entweder mit dem **Type** -Attribut des **returnType** (Function)-Elements oder mit einem der folgenden untergeordneten Elemente angegeben werden:

-   CollectionType
-   ReferenceType
-   RowType

> [!NOTE]
> Ein Modell wird nicht überprüft, wenn Sie einen Funktions Rückgabetyp mit dem **Type** -Attribut des **returnType** (Function)-Elements und einem der untergeordneten Elemente angeben.

 

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **returnType** (Function)-Element angewendet werden können.

| Attributname | Ist erforderlich | Wert                              |
|:---------------|:------------|:-----------------------------------|
| **ReturnType** | Nein          | Der von der Funktion zurückgegebene Typ. |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **returnType** (Function)-Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird ein **Function** -Element verwendet, um eine Funktion zu definieren, die die Anzahl der Jahre zurückgibt, in denen ein Buch gedruckt wurde. Beachten Sie, dass der Rückgabetyp durch das **Type** -Attribut eines **returnType** (Function)-Elements angegeben wird.

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a>ReturnType (FunctionImport)-Element (CSDL)

Das **returnType** (FunctionImport)-Element in konzeptioneller Schema Definitions Sprache (CSDL) gibt den Rückgabetyp für eine Funktion an, die in einem FunctionImport-Element definiert ist. Ein Funktions Rückgabetyp kann auch mit einem **returnType** -Attribut angegeben werden.

Rückgabe Typen können eine beliebige Auflistung von Entitäts Typen, komplexen Typen oder **edmsimpletype**sein.

Der Rückgabetyp einer Funktion wird mit dem **Type** -Attribut des **returnType** (FunctionImport)-Elements angegeben.

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **returnType** (FunctionImport)-Element angewendet werden können.

| Attributname | Ist erforderlich | Wert                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Typ**       | Nein          | Der Typ, den die Funktion zurückgibt. Der Wert muss eine Auflistung von complexType, EntityType oder edmsimpletype sein.                                                                                      |
| **EntitySet**  | Nein          | Wenn die Funktion eine Auflistung von Entitäts Typen zurückgibt, muss der Wert von **EntitySet** der Entitätenmenge angehören, zu der die Auflistung gehört. Andernfalls darf das Attribut **EntitySet** nicht verwendet werden. |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **returnType** (FunctionImport)-Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird ein **FunctionImport** -verwendet, der Bücher und Verleger zurückgibt. Beachten Sie, dass die Funktion zwei Resultsets zurückgibt. Daher werden zwei **returnType** (FunctionImport)-Elemente angegeben.

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a>RowType-Element (CSDL)

Ein **RowType** -Element in der konzeptionellen Schema Definitions Sprache (CSDL) definiert eine unbenannte Struktur als Parameter oder Rückgabetyp für eine Funktion, die im konzeptionellen Modell definiert ist.

Ein **RowType** -Element kann das untergeordnete Element der folgenden Elemente sein:

-   CollectionType
-   Parameter
-   ReturnType (Funktion)

Ein **RowType** -Element kann die folgenden untergeordneten Elemente aufweisen (in der angegebenen Reihenfolge):

-   Eigenschaft (ein oder mehrere Elemente)
-   Anmerkungselemente (kein (null) oder mehrere Elemente)

### <a name="applicable-attributes"></a>Anwendbare Attribute

Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **RowType** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt eine Modell definierte Funktion, die ein **CollectionType** -Element verwendet, um anzugeben, dass die Funktion eine Auflistung von Zeilen zurückgibt (wie im **RowType** -Element angegeben).

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

## <a name="schema-element-csdl"></a>Schema-Element (CSDL)

Das **Schema** -Element ist das Stamm Element einer konzeptionellen Modell Definition. Es enthält Definitionen für die Objekte, Funktionen und Container, die ein konzeptionelles Modell bilden.

Das **Schema** Element kann NULL oder mehr der folgenden untergeordneten Elemente enthalten:

-   Verwenden
-   EntityContainer
-   EntityType
-   EnumType
-   Zuordnung
-   ComplexType
-   Aufgabe

Ein **Schema** Element kann NULL oder ein Element mit Anmerkungen enthalten.

> [!NOTE]
> Die Element-und Anmerkung-Elemente von **Funktionen** sind nur in CSDL v2 und höher zulässig.

 

Das **Schema** -Element verwendet das **Namespace** -Attribut, um den Namespace für den Entitätstyp, den komplexen Typ und die Zuordnungs Objekte in einem konzeptionellen Modell zu definieren. Innerhalb eines Namespace müssen alle Objekte eine eindeutige Bezeichnung aufweisen. Namespaces können mehrere **Schema** Elemente und mehrere CSDL-Dateien umfassen.

Ein Namespace des konzeptionellen Modells unterscheidet sich vom XML-Namespace des **Schema** -Elements. Ein Namespace des konzeptionellen Modells (wie durch das **Namespace** -Attribut definiert) ist ein logischer Container für Entitäts Typen, komplexe Typen und Zuordnungs Typen. Der XML-Namespace (angegeben durch das **xmlns** -Attribut) eines **Schema** -Elements ist der Standard Namespace für untergeordnete Elemente und Attribute des **Schema** -Elements. XML-Namespaces im Formular https://schemas.microsoft.com/ado/YYYY/MM/edm (wobei yyyy und mm jeweils ein Jahr und einen Monat darstellen) sind für CSDL reserviert. Benutzerdefinierte Elemente und Attribute können nicht in Namespaces mit diesem Format vorhanden sein.

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **Schema** Element angewendet werden können.

| Attributname | Ist erforderlich | Wert                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Namespace**  | Ja         | Der Namespace für das konzeptionelle Modell. Der Wert des **Namespace** -Attributs wird verwendet, um den voll qualifizierten Namen eines Typs zu bilden. Wenn sich z. b. ein **EntityType** mit dem Namen *Customer* im Simple. example. Model-Namespace befindet, ist der voll qualifizierte Name des **EntityType** "simpleexamplemodel. Customer". <br/> Die folgenden Zeichen folgen können nicht als Wert für das **Namespace** -Attribut verwendet werden: **System**, **transient**oder **EDM**. Der Wert für das **Namespace** -Attribut darf nicht mit dem Wert für das **Namespace** -Attribut im SSDL-Schema Element identisch sein. |
| **Alias**      | Nein          | Ein anstelle der Namespacebezeichnung verwendeter Bezeichner. Wenn z. b. ein **EntityType** mit dem Namen *Customer* im Simple. example. Model-Namespace und der Wert des **Alias** -Attributs *Model*ist, können Sie Model. Customer als voll qualifizierten Namen von **EntityType verwenden.**                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **Schema** Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **Schema** -Element, das ein **EntityContainer** -Element, zwei **EntityType** -Elemente und ein **Association** -Element enthält.

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
 

 

## <a name="typeref-element-csdl"></a>TypeRef-Element (CSDL)

Das **TypeRef** -Element in konzeptioneller Schema Definitions Sprache (CSDL) stellt einen Verweis auf einen vorhandenen benannten Typ bereit. Das **TypeRef** -Element kann ein untergeordnetes Element des CollectionType-Elements sein, das verwendet wird, um anzugeben, dass eine Funktion eine Auflistung als Parameter oder Rückgabetyp aufweist.

Ein **TypeRef** -Element kann über die folgenden untergeordneten Elemente verfügen (in der angegebenen Reihenfolge):

-   Dokumentation (kein (null) oder ein Element)
-   Anmerkungselemente (kein Element (null) oder mehrere Elemente)

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **TypeRef** -Element angewendet werden können. Beachten Sie, dass die Attribute " **DefaultValue**", " **MaxLength**", " **FixedLength**", " **Precision**", " **Scale**", " **Unicode**" und " **COLLATIONS** " nur auf **edmsimple**

| Attributname                                                     | Ist erforderlich | Wert                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Typ**                                                           | Nein          | Der Name der Typbibliothek, auf die verwiesen wird.                                                                                                                                                                                          |
| **NULL zulassen**                                                       | Nein          | **True** (Standardwert) oder **False** abhängig davon, ob die Eigenschaft einen NULL-Wert haben kann. <br/> [!NOTE]                                                                                                                |
| > in CSDL v1 muss eine komplexe Typeigenschaft über verfügen `Nullable="False"` . |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                   | Nein          | Der Standardwert der Eigenschaft.                                                                                                                                                                                              |
| **MaxLength**                                                      | Nein          | Maximale Länge des Eigenschaftswerts.                                                                                                                                                                                       |
| **FixedLength**                                                    | Nein          | **True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Zeichenfolge mit fester Länge gespeichert wird.                                                                                                                          |
| **Genauigkeit**                                                      | Nein          | Die Genauigkeit des Eigenschaftswerts.                                                                                                                                                                                            |
| **Skalierung**                                                          | Nein          | Die Skalierung des Eigenschaftswerts.                                                                                                                                                                                                |
| **SRID**                                                           | Nein          | Verweis Bezeichner für räumliche Systeme. Nur für Eigenschaften räumlicher Typen gültig. Weitere Informationen finden Sie unter [SRID](https://en.wikipedia.org/wiki/SRID) und [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **Unicode**                                                        | Nein          | **True** oder **false** , abhängig davon, ob der Eigenschafts Wert als Unicode-Zeichenfolge gespeichert wird.                                                                                                                               |
| **Sortierung**                                                      | Nein          | Eine Zeichenfolge, die angibt, welche Sortierreihenfolge in der Datenquelle verwendet wird.                                                                                                                                                   |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **CollectionType** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt eine Modell definierte Funktion, die das **TypeRef** -Element verwendet (als untergeordnetes Element eines **CollectionType** -Elements), um anzugeben, dass die Funktion eine Auflistung von **Abteilungs** Entitäts Typen akzeptiert.

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
 

 

## <a name="using-element-csdl"></a>Using-Element (CSDL)

Das **using** -Element in konzeptioneller Schema Definitions Sprache (CSDL) importiert den Inhalt eines konzeptionellen Modells, das in einem anderen Namespace vorhanden ist. Wenn Sie den Wert des **Namespace** -Attributs festlegen, können Sie auf Entitäts Typen, komplexe Typen und Zuordnungs Typen verweisen, die in einem anderen konzeptionellen Modell definiert sind. Mehr als ein **using** -Element kann ein untergeordnetes Element eines **Schema** -Elements sein.

> [!NOTE]
> Das **using** -Element in CSDL funktioniert nicht genau wie eine **using** -Anweisung in einer Programmiersprache. Wenn Sie einen Namespace mit einer **using** -Anweisung in einer Programmiersprache importieren, wirkt sich dies nicht auf Objekte im ursprünglichen Namespace aus. In CSDL kann ein importierter Namespace einen Entitätstyp enthalten, der von einem Entitätstyp im ursprünglichen Namespace abgeleitet ist. Dies kann sich auf im ursprünglichen Namespace deklarierte Entitätssätze auswirken.

 

Das **using** -Element kann die folgenden untergeordneten Elemente aufweisen:

-   Dokumentation (kein (null) oder ein Element zugelassen)
-   Anmerkungselemente (kein (null) Element oder mehrere Elemente zugelassen)

### <a name="applicable-attributes"></a>Anwendbare Attribute

In der folgenden Tabelle werden die Attribute beschrieben, die auf das **using** -Element angewendet werden können.

| Attributname | Ist erforderlich | Wert                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Namespace**  | Ja         | Der Name des importierten Namespaces.                                                                                                                                                |
| **Alias**      | Ja         | Ein anstelle der Namespacebezeichnung verwendeter Bezeichner. Obwohl dieses Attribut erforderlich ist, muss es nicht anstelle des Namespacenamens verwendet wird, um Objektnamen zu qualifizieren. |

 

> [!NOTE]
> Eine beliebige Anzahl von Anmerkung-Attributen (benutzerdefinierte XML-Attribute) kann auf das **using** -Element angewendet werden. Benutzerdefinierte Attribute dürfen jedoch zu keinem XML-Namespace gehören, der für CSDL reserviert ist. Die vollqualifizierten Namen für zwei benutzerdefinierte Attribute dürfen nicht übereinstimmen.

 

### <a name="example"></a>Beispiel

Das folgende Beispiel veranschaulicht das **using** -Element, das verwendet wird, um einen Namespace zu importieren, der an anderer Stelle definiert ist. Beachten Sie, dass der Namespace für das angezeigte **Schema** Element ist `BooksModel` . Die- `Address` Eigenschaft für den `Publisher` **EntityType** ist ein komplexer Typ, der im- `ExtendedBooksModel` Namespace definiert ist (importiert mit dem **using** -Element).

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
 

 

## <a name="annotation-attributes-csdl"></a>Anmerkungsattribute (CSDL)

Anmerkungsattribute in konzeptioneller Schemadefinitionssprache (CSDL) sind benutzerdefinierte XML-Attribute im konzeptionellen Modell. Neben dem Vorhandensein einer gültigen XML-Struktur muss für Anmerkungsattribute folgendes zutreffen:

-   Anmerkungsattribute dürfen sich in keinem XML-Namespace befinden, der für CSDL reserviert ist.
-   Für ein angegebenes CSDL-Element kann mehr als ein Anmerkungsattribut übernommen werden.
-   Die vollqualifizierten Namen zweier Anmerkungsattribute dürfen nicht übereinstimmen.

Anmerkungsattribute können verwendet werden, um zusätzliche Metadaten für die Elemente in einem konzeptionellen Modell bereitzustellen. Auf Metadaten, die in Anmerkung-Elementen enthalten sind, kann zur Laufzeit mithilfe von Klassen im System. Data. Metadata. Edm-Namespace zugegriffen werden.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **EntityType** -Element mit einem Anmerkung-Attribut (**CustomAttribute**). Das Beispiel zeigt außerdem ein Anmerkungselement, das auf das Entitätstypelement angewendet wird.

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
 

Im folgenden Code werden die Metadaten im Anmerkungsattribut abgerufen und in die Konsole geschrieben:

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
 

Im Code oben wird davon ausgegangen, dass sich die Datei `School.csdl` im Ausgabeverzeichnis des Projekts befindet und dass Sie dem Projekt die folgenden `Imports`- und `Using`-Anweisungen hinzugefügt haben:

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a>Anmerkungselemente (CSDL)

Anmerkungselemente in konzeptioneller Schemadefinitionssprache (CSDL) sind benutzerdefinierte XML-Elemente im konzeptionellen Modell. Neben dem Vorhandensein einer gültigen XML-Struktur muss für Anmerkungselemente Folgendes zutreffen:

-   Anmerkungselemente dürfen sich in keinem XML-Namespace befinden, der für CSDL reserviert ist.
-   Mehrere Anmerkungselemente können untergeordnete Elemente eines angegebenen CSDL-Elements sein.
-   Die vollqualifizierten Namen zweier Anmerkungselemente dürfen nicht übereinstimmen.
-   Anmerkungselemente müssen nach allen anderen untergeordneten Elementen eines angegebenen CSDL-Elements angezeigt werden.

Anmerkungselemente können verwendet werden, um zusätzliche Metadaten für die Elemente in einem konzeptionellen Modell bereitzustellen. Ab Version 4 von .NET Framework können mithilfe von Klassen im Namespace "System. Data. Metadata. Edm" zur Laufzeit auf Metadaten zugegriffen werden, die in Anmerkung-Elementen enthalten sind.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt ein **EntityType** -Element mit einem Anmerkung-Element (**customelement**). Das Beispiel zeigt außerdem ein Anmerkungsattribut, das auf das Entitätstypelement angewendet wird.

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
 

Im folgenden Code werden die Metadaten im Anmerkungselement abgerufen und in die Konsole geschrieben:

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
 

Im Code oben wird davon ausgegangen, dass sich die Datei School.csdl im Ausgabeverzeichnis des Projekts befindet, und dass Sie dem Projekt die folgenden `Imports`- und `Using`-Anweisungen hinzugefügt haben:

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a>Konzeptionelle Modelltypen (CSDL)

Die konzeptionelle Schema Definitions Sprache (CSDL) unterstützt einen Satz abstrakter primitiver Datentypen namens **edmsimpletypes**, die Eigenschaften in einem konzeptionellen Modell definieren. **Edmsimpletypes** sind Proxys für primitive Datentypen, die in der Speicher-oder Hostingumgebung unterstützt werden.

In der nachfolgenden Tabelle werden die von CSDL unterstützten primitiven Datentypen aufgeführt. In der Tabelle werden auch die Facetten aufgelistet, die auf jeden **edmsimpletype**angewendet werden können.

| EDMSimpleType                    | Beschreibung                                                | Anwendbare Facets                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| **Edm.Binary**                   | Enthält Binärdaten.                                      | MaxLength, FixedLength, Nullable, Default                                |
| **Edm.Boolean**                  | Enthält den Wert **true** oder **false**.                  | Nullable, Default                                                        |
| **Edm.Byte**                     | Enthält einen 8-Bit-Ganzzahlwert ohne Vorzeichen.                  | Precision, Nullable, Default                                             |
| **Edm.DateTime**                 | Stellt ein Datum und eine Uhrzeit dar.                                | Precision, Nullable, Default                                             |
| **Edm.DateTimeOffset**           | Enthält ein Datum und eine Uhrzeit als Offset in Minuten von GMT. | Precision, Nullable, Default                                             |
| **Edm.Decimal**                  | Enthält einen numerischen Wert mit fester Genauigkeit und festen Dezimalstellen.   | Precision, Nullable, Default                                             |
| **Edm.Double**                   | Enthält eine Gleit Komma Zahl mit einer Genauigkeit von 15 Ziffern.   | Precision, Nullable, Default                                             |
| **EDM. float**                    | Enthält eine Gleitkommazahl mit einer Genauigkeit von 7 Stellen.   | Precision, Nullable, Default                                             |
| **Edm.Guid**                     | Enthält einen eindeutigen 16-Byte-Bezeichner.                      | Precision, Nullable, Default                                             |
| **Edm.Int16**                    | Enthält einen 16-Bit-Ganzzahlwert mit Vorzeichen.                    | Precision, Nullable, Default                                             |
| **Edm.Int32**                    | Enthält einen 32-Bit-Ganzzahlwert mit Vorzeichen.                    | Precision, Nullable, Default                                             |
| **Edm.Int64**                    | Enthält einen 64-Bit-Ganzzahlwert mit Vorzeichen.                    | Precision, Nullable, Default                                             |
| **Edm.SByte**                    | Enthält einen 8-Bit-Ganzzahlwert mit Vorzeichen.                     | Precision, Nullable, Default                                             |
| **Edm.String**                   | Enthält Zeichendaten.                                   | Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default |
| **Edm.Time**                     | Enthält eine Uhrzeit.                                    | Precision, Nullable, Default                                             |
| **EDM. geography**                |                                                            | Nullable, Standard, SRID                                                  |
| **Edm.GeographyPoint**           |                                                            | Nullable, Standard, SRID                                                  |
| **EDM. geographylinestring**      |                                                            | Nullable, Standard, SRID                                                  |
| **EDM. geographypolygon**         |                                                            | Nullable, Standard, SRID                                                  |
| **EDM. geographymultipoint**      |                                                            | Nullable, Standard, SRID                                                  |
| **EDM. geographymultilinestring** |                                                            | Nullable, Standard, SRID                                                  |
| **EDM. geographymultipolygon**    |                                                            | Nullable, Standard, SRID                                                  |
| **EDM. geographycollection**      |                                                            | Nullable, Standard, SRID                                                  |
| **EDM. Geometry**                 |                                                            | Nullable, Standard, SRID                                                  |
| **EDM. geometryPoint**            |                                                            | Nullable, Standard, SRID                                                  |
| **EDM. geometrylinestring**       |                                                            | Nullable, Standard, SRID                                                  |
| **EDM. geometrypolygon**          |                                                            | Nullable, Standard, SRID                                                  |
| **EDM. geometrymultipoint**       |                                                            | Nullable, Standard, SRID                                                  |
| **EDM. geometryMultiLineString**  |                                                            | Nullable, Standard, SRID                                                  |
| **EDM. geometrymultipolygon**     |                                                            | Nullable, Standard, SRID                                                  |
| **EDM. GeometryCollection**       |                                                            | Nullable, Standard, SRID                                                  |

## <a name="facets-csdl"></a>Facets (CSDL)

Facets in konzeptioneller Schemadefinitionssprache (CSDL) stellen Einschränkungen für Eigenschaften von Entitätstypen und komplexen Typen dar. Facets werden in den folgenden CSDL-Elementen als XML-Attribute angezeigt:

-   Eigenschaft
-   TypeRef
-   Parameter

In der folgenden Tabelle werden die in CSDL unterstützten Facets beschrieben. Alle Facets sind optional. Einige der unten aufgeführten Aspekte werden vom Entity Framework beim Erstellen einer Datenbank aus einem konzeptionellen Modell verwendet.

> [!NOTE]
> Informationen zu Datentypen in einem konzeptionellen Modell finden Sie unter konzeptionelle Modelltypen (CSDL).

| Facet               | Beschreibung                                                                                                                                                                                                                                                   | Gilt für:                                                                                                                                                                                                                                                                                                                                                                           | Wird für die Datenbankgenerierung verwendet | Wird von der Laufzeit verwendet |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| **Sortierung**       | Gibt die bei Vergleich- und Sortiervorgängen zu verwendende Sortierreihenfolge für die Werte der Eigenschaft an.                                                                                                               | **Edm.String**                                                                                                                                                                                                                                                                                                                                                                       | Ja                              | Nein                  |
| **ConcurrencyMode** | Gibt an, dass der Eigenschaftswert für Prüfungen der vollständigen Parallelität verwendet werden soll.                                                                                                                                                                    | Alle **edmsimpletype** -Eigenschaften                                                                                                                                                                                                                                                                                                                                                     | Nein                               | Ja                 |
| **Standard**         | Gibt den Standardwert der Eigenschaft an, wenn bei der Instanziierung kein Wert angegeben wird.                                                                                                                                                                       | Alle **edmsimpletype** -Eigenschaften                                                                                                                                                                                                                                                                                                                                                     | Ja                              | Ja                 |
| **FixedLength**     | Gibt an, ob sich die Länge des Eigenschaftswerts ändern kann.                                                                                                                                                                                                  | **Edm. Binary**, **Edm. String**                                                                                                                                                                                                                                                                                                                                                       | Ja                              | Nein                  |
| **MaxLength**       | Gibt die maximale Länge des Eigenschaftswerts an.                                                                                                                                                                                                           | **Edm. Binary**, **Edm. String**                                                                                                                                                                                                                                                                                                                                                       | Ja                              | Nein                  |
| **NULL zulassen**        | Gibt an, ob die Eigenschaft einen **null** -Wert aufweisen kann.                                                                                                                                                                                                     | Alle **edmsimpletype** -Eigenschaften                                                                                                                                                                                                                                                                                                                                                     | Ja                              | Ja                 |
| **Genauigkeit**       | Gibt bei Eigenschaften vom Typ **Decimal**die Anzahl der Ziffern an, die ein Eigenschafts Wert aufweisen kann. Bei Eigenschaften vom Typ **time**, **DateTime**und **DateTimeOffset**wird die Anzahl der Ziffern für den Bruchteil der Sekunden des Eigenschafts Werts angegeben. | **Edm. DateTime**, **Edm. DateTimeOffset**, **Edm. Decimal**, **Edm. Time**                                                                                                                                                                                                                                                                                                              | Ja                              | Nein                  |
| **Skalierung**           | Gibt die Anzahl der Dezimalstellen für den Eigenschaftswert an.                                                                                                                                                                      | **Edm.Decimal**                                                                                                                                                                                                                                                                                                                                                                      | Ja                              | Nein                  |
| **SRID**            | Gibt die System-ID des räumlichen System Verweises an. Weitere Informationen finden Sie unter [SRID](https://en.wikipedia.org/wiki/SRID) und [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).                                                              | **EDM. Geography, EDM. geographypoint, EDM. geographylinestring, EDM. geographypolygon, EDM. geographymultipoint, EDM. geographymultilinestring, EDM. geographymultipolygon, EDM. geographycollection, EDM. Geometry, EDM. geometryPoint, EDM. geometrylinestring, EDM. geometrypolygon, EDM. geometrymultipoint, EDM. geometryMultiLineString, EDM. geometrymultipolygon, EDM. GeometryCollection** | Nein                               | Ja                 |
| **Unicode**         | Gibt an, ob der Eigenschaftswert als Unicode gespeichert wird.                                                                                                                                                                                                    | **Edm.String**                                                                                                                                                                                                                                                                                                                                                                       | Ja                              | Ja                 |

>[!NOTE]
> Beim Generieren einer Datenbank aus einem konzeptionellen Modell erkennt der Assistent zum Generieren von Datenbanken den Wert des **StoreGeneratedPattern** -Attributs für ein **Eigenschafts** Element, wenn er sich im folgenden Namespace befindet: https://schemas.microsoft.com/ado/2009/02/edm/annotation . Die unterstützten Werte für das Attribut sind **Identity** und **berechnete**Werte. Bei **einem Identitäts Wert wird eine** Daten Bank Spalte mit einem Identitäts Wert erstellt, der in der Datenbank generiert wird. Der **berechnete** Wert erstellt eine Spalte mit einem Wert, der in der Datenbank berechnet wird.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt für die Eigenschaften eines Entitätstyps übernommene Facets:

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
