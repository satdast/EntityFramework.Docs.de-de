---
title: Definieren von Query-EF-Designer-EF6
description: Definieren von Query-EF-Designer in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: e52a297e-85aa-42f6-a922-ba960f8a4b22
uid: ef6/modeling/designer/advanced/defining-query
ms.openlocfilehash: e1962a80c3a94fb17bacf28969629ce949b117a0
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616840"
---
# <a name="defining-query---ef-designer"></a>Definieren einer Abfrage (EF-Designer)
In dieser exemplarischen Vorgehensweise wird veranschaulicht, wie mit dem EF-Designer eine definierende Abfrage und ein entsprechender Entitätstyp zu einem Modell hinzugefügt werden. Eine definierende Abfrage wird häufig verwendet, um ähnliche Funktionen wie in einer Daten Bank Ansicht bereitzustellen, aber die Sicht wird im Modell und nicht in der Datenbank definiert. Eine definierende Abfrage ermöglicht das Ausführen einer SQL-Anweisung, die im **DefiningQuery**-   Element einer EDMX-Datei angegeben ist. Weitere Informationen finden Sie unter **DefiningQuery** in der [SSDL-Spezifikation](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).

Wenn Sie die Definition von Abfragen verwenden, müssen Sie auch einen Entitätstyp in Ihrem Modell definieren. Der Entitätstyp wird verwendet, um von der definierenden Abfrage verfügbar gemachte Daten zu übernehmen. Beachten Sie, dass Daten, die über diesen Entitätstyp über gestellt werden, schreibgeschützt sind.

Parametrisierte Abfragen können nicht als definierende Abfragen ausgeführt werden. Die Daten können jedoch aktualisiert werden, indem die Insert-, Update- und Delete-Funktionen des Entitätstyps, der die Daten zugänglich macht, gespeicherten Prozeduren zugeordnet werden. Weitere Informationen finden Sie unter [Einfügen, aktualisieren und Löschen mit gespeicherten Prozeduren](xref:ef6/modeling/designer/stored-procedures/cud).

In diesem Thema wird gezeigt, wie die folgenden Aufgaben ausgeführt werden.

-   Hinzufügen einer definierenden Abfrage
-   Hinzufügen eines Entitäts Typs zum Modell
-   Zuordnen der definierenden Abfrage zum Entitätstyp

## <a name="prerequisites"></a>Voraussetzungen

Um die exemplarische Vorgehensweise nachzuvollziehen, benötigen Sie Folgendes:

- Eine aktuelle Version von Visual Studio.
- Die [Beispieldatenbank "School](xref:ef6/resources/school-database)".

## <a name="set-up-the-project"></a>Einrichten des Projekts

In dieser exemplarischen Vorgehensweise wird Visual Studio 2012 oder höher verwendet.

-   Öffnen Sie Visual Studio.
-   Zeigen Sie im Menü **Datei** auf **Neu**, und klicken Sie dann auf **Projekt**.
-   Klicken Sie im linken Bereich auf **Visual C \# **, und wählen Sie dann die Vorlage **Konsolenanwendung** aus.
-   Geben Sie **definingquerysample** als Namen für das Projekt ein, und klicken Sie auf **OK**.

 

## <a name="create-a-model-based-on-the-school-database"></a>Erstellen eines Modells auf der Grundlage der Datenbank "School"

-   Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen, zeigen Sie auf **Hinzufügen**, und klicken Sie dann auf **Neues Element**.
-   Wählen Sie im linken Menü **Daten** aus, und wählen Sie dann im Bereich Vorlagen die Option **ADO.NET Entity Data Model** aus.
-   Geben Sie **definingquerymodel. edmx** als Dateiname ein, und klicken Sie dann auf **Hinzufügen**.
-   Wählen Sie im Dialogfeld Modell Inhalte auswählen die Option **aus Datenbank generieren aus**, und klicken Sie dann auf **weiter**.
-   Klicken Sie auf neue Verbindung. Geben Sie im Dialogfeld Verbindungs Eigenschaften den Servernamen ein (z. b. **(localdb) \\ mssqllocaldb**), wählen Sie die Authentifizierungsmethode aus, geben Sie **School**   als Datenbanknamen School ein, und klicken Sie dann auf **OK**.
    Das Dialogfeld Wählen Sie Ihre Datenverbindung aus wird mit Ihrer Daten bankverbindungs Einstellung aktualisiert.
-   Überprüfen Sie im Dialogfeld Wählen Sie Ihre Datenbankobjekte aus den Knoten **Tabellen**   . Dadurch werden alle Tabellen dem Modell " **School** " hinzugefügt.
-   Klicken Sie auf **Finish**.
-   Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf die Datei **definingquerymodel. edmx** , und wählen Sie **Öffnen mit...** aus.
-   Wählen Sie den **XML-Editor (Text)** aus.

    ![XML-Editor](~/ef6/media/xmleditor.png)

-   Klicken Sie auf **Ja** , wenn Sie mit der folgenden Meldung aufgefordert werden:

    ![Warnung 2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a>Hinzufügen einer definierenden Abfrage

In diesem Schritt wird der XML-Editor verwendet, um dem SSDL-Abschnitt der EDMX-Datei eine definierende Abfrage und einen Entitätstyp hinzuzufügen. 

-   Fügen Sie **EntitySet**   dem SSDL-Abschnitt der EDMX-Datei ein EntitySet-Element hinzu (Zeile 5 bis 13). Geben Sie Folgendes an:
    -   Es werden nur die Attribute **Name**   und **EntityType**   des **EntitySet**-   Elements angegeben.
    -   Der voll qualifizierte Name des Entitäts Typs wird im **EntityType**-   Attribut verwendet.
    -   Die auszuführende SQL-Anweisung wird im **DefiningQuery**-   Element angegeben.

``` xml
    <!-- SSDL content -->
    <edmx:StorageModels>
      <Schema Namespace="SchoolModel.Store" Alias="Self" Provider="System.Data.SqlClient" ProviderManifestToken="2008" xmlns:store="http://schemas.microsoft.com/ado/2007/12/edm/EntityStoreSchemaGenerator" xmlns="http://schemas.microsoft.com/ado/2009/11/edm/ssdl">
        <EntityContainer Name="SchoolModelStoreContainer">
           <EntitySet Name="GradeReport" EntityType="SchoolModel.Store.GradeReport">
              <DefiningQuery>
                SELECT CourseID, Grade, FirstName, LastName
                FROM StudentGrade
                JOIN
                (SELECT * FROM Person WHERE EnrollmentDate IS NOT NULL) AS p
                ON StudentID = p.PersonID
              </DefiningQuery>
          </EntitySet>
          <EntitySet Name="Course" EntityType="SchoolModel.Store.Course" store:Type="Tables" Schema="dbo" />
```

-   Fügen Sie das **EntityType** -Element dem SSDL-Abschnitt der EDMX-Datei hinzu. Datei wie unten gezeigt. Beachten Sie Folgendes:
    -   Der Wert des Attributs " **Name** " entspricht dem Wert des Attributs " **EntityType** " im obigen **EntitySet** -Element, obwohl der voll qualifizierte Name des Entitäts Typs im **EntityType** -Attribut verwendet wird.
    -   Die Eigenschaftsnamen entsprechen den Spaltennamen, die von der SQL-Anweisung im **DefiningQuery** -Element (oben) zurückgegeben werden.
    -   In diesem Beispiel wird durch einen aus drei Eigenschaften bestehenden Entitätsschlüssel sichergestellt, dass der Schlüsselwert eindeutig ist.

``` xml
    <EntityType Name="GradeReport">
      <Key>
        <PropertyRef Name="CourseID" />
        <PropertyRef Name="FirstName" />
        <PropertyRef Name="LastName" />
      </Key>
      <Property Name="CourseID"
                Type="int"
                Nullable="false" />
      <Property Name="Grade"
                Type="decimal"
                Precision="3"
                Scale="2" />
      <Property Name="FirstName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
      <Property Name="LastName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
    </EntityType>
```

>[!NOTE]
> Wenn Sie später den **Modellaktualisierungs-Assistenten** ausführen, werden alle Änderungen, die am Speichermodell vorgenommen werden, einschließlich der Definition von Abfragen, überschrieben.

 

## <a name="add-an-entity-type-to-the-model"></a>Hinzufügen eines Entitäts Typs zum Modell

In diesem Schritt fügen wir den Entitätstyp mit dem EF-Designer dem konzeptionellen Modell hinzu. Beachten Sie Folgendes:

-   Der **Name** der Entität entspricht dem Wert des Attributs **EntityType** im obigen **EntitySet** -Element.
-   Die Eigenschaftsnamen entsprechen den Spaltennamen, die von der SQL-Anweisung im obigen **DefiningQuery** -Element zurückgegeben werden.
-   In diesem Beispiel wird durch einen aus drei Eigenschaften bestehenden Entitätsschlüssel sichergestellt, dass der Schlüsselwert eindeutig ist.

Öffnen Sie das Modell im EF-Designer.

-   Doppelklicken Sie auf definingquerymodel. edmx.
-   Sagen Sie **Ja** , dass die folgende Meldung angezeigt wird:

    ![Warnung 2](~/ef6/media/warning2.png)

 

Die Entity Designer, die eine Entwurfs Oberfläche zum Bearbeiten des Modells bereitstellt, wird angezeigt.

-   Klicken Sie mit der rechten Maustaste auf die Designer Oberfläche, und wählen Sie **neue** - &gt; **Entität**hinzufügen aus.
-   Geben Sie für den Entitäts Namen und den **CourseID** für die **Schlüsseleigenschaft**den Wert **gradereport** an.
-   Klicken Sie mit der rechten Maustaste auf die Entität **gradereport** , und wählen Sie **neue** - &gt; **skalare Eigenschaft**hinzufügen
-   Ändern Sie den Standardnamen der Eigenschaft in **FirstName**.
-   Fügen Sie eine weitere skalare Eigenschaft hinzu, und geben Sie **LastName** als Name an.
-   Fügen Sie eine weitere skalare Eigenschaft hinzu, und geben Sie **Grade** für den Namen an.
-   Ändern Sie im **Eigenschaften** Fenster die **Type** -Eigenschaft der **Klasse**in **Decimal**.
-   Wählen Sie die Eigenschaften **FirstName** und **LastName** aus.
-   Ändern Sie im **Eigenschaften** Fenster den Wert der **EntityKey** -Eigenschaft in **true**.

Folglich wurden dem **CSDL** -Abschnitt der EDMX-Datei die folgenden Elemente hinzugefügt.

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a>Zuordnen der definierenden Abfrage zum Entitätstyp

In diesem Schritt wird das Fenster Mappingdetails verwendet, um die konzeptionellen Entitäts Typen und Speicher Entitäts Typen zuzuordnen.

-   Klicken Sie mit der rechten Maustaste auf die Entität **gradereport** auf der Entwurfs Oberfläche, und wählen Sie **Tabellen Zuordnung**.  
    Das Fenster **Mappingdetails** wird angezeigt.
-   Wählen Sie in der Dropdown Liste ** &lt; Tabelle oder &gt; Sicht hinzufügen** den Wert **gradereport** aus (befindet sich unter **Tabelle**s).  
    Standard Zuordnungen zwischen dem Entitätstyp **GradeReport** "konzeptionell" und "Speicher" werden angezeigt.  
    ![Mapping Details3](~/ef6/media/mappingdetails.png)

Folglich wird das **EntitySetMapping**-   Element dem Mapping-Abschnitt der EDMX-Datei hinzugefügt. 

``` xml
    <EntitySetMapping Name="GradeReports">
      <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.GradeReport)">
        <MappingFragment StoreEntitySet="GradeReport">
          <ScalarProperty Name="LastName" ColumnName="LastName" />
          <ScalarProperty Name="FirstName" ColumnName="FirstName" />
          <ScalarProperty Name="Grade" ColumnName="Grade" />
          <ScalarProperty Name="CourseID" ColumnName="CourseID" />
        </MappingFragment>
      </EntityTypeMapping>
    </EntitySetMapping>
```

-   Kompilieren Sie die Anwendung.

 

## <a name="call-the-defining-query-in-your-code"></a>Die definierende Abfrage im Code aufzurufen

Sie können jetzt die definierende Abfrage ausführen, indem Sie den Entitätstyp **gradereportierung** verwenden. 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
