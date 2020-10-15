---
title: Definieren von Query-EF-Designer-EF6
description: Definieren von Query-EF-Designer in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/defining-query
ms.openlocfilehash: 3640156a2ce97dccab563a18d3236aad3dc72ee0
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066381"
---
# <a name="defining-query---ef-designer"></a><span data-ttu-id="bf734-103">Definieren einer Abfrage (EF-Designer)</span><span class="sxs-lookup"><span data-stu-id="bf734-103">Defining Query - EF Designer</span></span>
<span data-ttu-id="bf734-104">In dieser exemplarischen Vorgehensweise wird veranschaulicht, wie mit dem EF-Designer eine definierende Abfrage und ein entsprechender Entitätstyp zu einem Modell hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="bf734-104">This walkthrough demonstrates how to add a defining query and a corresponding entity type to a model using the EF Designer.</span></span> <span data-ttu-id="bf734-105">Eine definierende Abfrage wird häufig verwendet, um ähnliche Funktionen wie in einer Daten Bank Ansicht bereitzustellen, aber die Sicht wird im Modell und nicht in der Datenbank definiert.</span><span class="sxs-lookup"><span data-stu-id="bf734-105">A defining query is commonly used to provide functionality similar to that provided by a database view, but the view is defined in the model, not the database.</span></span> <span data-ttu-id="bf734-106">Eine definierende Abfrage ermöglicht das Ausführen einer SQL-Anweisung, die im **DefiningQuery**-   Element einer EDMX-Datei angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="bf734-106">A defining query allows you to execute a SQL statement that is specified in the **DefiningQuery** element of an .edmx file.</span></span> <span data-ttu-id="bf734-107">Weitere Informationen finden Sie unter **DefiningQuery** in der [SSDL-Spezifikation](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span><span class="sxs-lookup"><span data-stu-id="bf734-107">For more information, see **DefiningQuery** in the [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span></span>

<span data-ttu-id="bf734-108">Wenn Sie die Definition von Abfragen verwenden, müssen Sie auch einen Entitätstyp in Ihrem Modell definieren.</span><span class="sxs-lookup"><span data-stu-id="bf734-108">When using defining queries, you also have to define an entity type in your model.</span></span> <span data-ttu-id="bf734-109">Der Entitätstyp wird verwendet, um von der definierenden Abfrage verfügbar gemachte Daten zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="bf734-109">The entity type is used to surface data exposed by the defining query.</span></span> <span data-ttu-id="bf734-110">Beachten Sie, dass Daten, die über diesen Entitätstyp über gestellt werden, schreibgeschützt sind.</span><span class="sxs-lookup"><span data-stu-id="bf734-110">Note that data surfaced through this entity type is read-only.</span></span>

<span data-ttu-id="bf734-111">Parametrisierte Abfragen können nicht als definierende Abfragen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="bf734-111">Parameterized queries cannot be executed as defining queries.</span></span> <span data-ttu-id="bf734-112">Die Daten können jedoch aktualisiert werden, indem die Insert-, Update- und Delete-Funktionen des Entitätstyps, der die Daten zugänglich macht, gespeicherten Prozeduren zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="bf734-112">However, the data can be updated by mapping the insert, update, and delete functions of the entity type that surfaces the data to stored procedures.</span></span> <span data-ttu-id="bf734-113">Weitere Informationen finden Sie unter [Einfügen, aktualisieren und Löschen mit gespeicherten Prozeduren](xref:ef6/modeling/designer/stored-procedures/cud).</span><span class="sxs-lookup"><span data-stu-id="bf734-113">For more information, see [Insert, Update, and Delete with Stored Procedures](xref:ef6/modeling/designer/stored-procedures/cud).</span></span>

<span data-ttu-id="bf734-114">In diesem Thema wird gezeigt, wie die folgenden Aufgaben ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="bf734-114">This topic shows how to perform the following tasks.</span></span>

-   <span data-ttu-id="bf734-115">Hinzufügen einer definierenden Abfrage</span><span class="sxs-lookup"><span data-stu-id="bf734-115">Add a Defining Query</span></span>
-   <span data-ttu-id="bf734-116">Hinzufügen eines Entitäts Typs zum Modell</span><span class="sxs-lookup"><span data-stu-id="bf734-116">Add an Entity Type to the Model</span></span>
-   <span data-ttu-id="bf734-117">Zuordnen der definierenden Abfrage zum Entitätstyp</span><span class="sxs-lookup"><span data-stu-id="bf734-117">Map the Defining Query to the Entity Type</span></span>

## <a name="prerequisites"></a><span data-ttu-id="bf734-118">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="bf734-118">Prerequisites</span></span>

<span data-ttu-id="bf734-119">Um die exemplarische Vorgehensweise nachzuvollziehen, benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="bf734-119">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="bf734-120">Eine aktuelle Version von Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bf734-120">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="bf734-121">Die [Beispieldatenbank "School](xref:ef6/resources/school-database)".</span><span class="sxs-lookup"><span data-stu-id="bf734-121">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="bf734-122">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="bf734-122">Set up the Project</span></span>

<span data-ttu-id="bf734-123">In dieser exemplarischen Vorgehensweise wird Visual Studio 2012 oder höher verwendet.</span><span class="sxs-lookup"><span data-stu-id="bf734-123">This walkthrough is using Visual Studio 2012 or newer.</span></span>

-   <span data-ttu-id="bf734-124">Öffnen Sie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bf734-124">Open Visual Studio.</span></span>
-   <span data-ttu-id="bf734-125">Zeigen Sie im Menü **Datei** auf **Neu**, und klicken Sie dann auf **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="bf734-125">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="bf734-126">Klicken Sie im linken Bereich auf \*\*Visual C \# \*\*, und wählen Sie dann die Vorlage **Konsolenanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="bf734-126">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="bf734-127">Geben Sie **definingquerysample** als Namen für das Projekt ein, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="bf734-127">Enter **DefiningQuerySample** as the name of the project and click **OK**.</span></span>

 

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="bf734-128">Erstellen eines Modells auf der Grundlage der Datenbank "School"</span><span class="sxs-lookup"><span data-stu-id="bf734-128">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="bf734-129">Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen, zeigen Sie auf **Hinzufügen**, und klicken Sie dann auf **Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="bf734-129">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="bf734-130">Wählen Sie im linken Menü **Daten** aus, und wählen Sie dann im Bereich Vorlagen die Option **ADO.NET Entity Data Model** aus.</span><span class="sxs-lookup"><span data-stu-id="bf734-130">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="bf734-131">Geben Sie **definingquerymodel. edmx** als Dateiname ein, und klicken Sie dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="bf734-131">Enter **DefiningQueryModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="bf734-132">Wählen Sie im Dialogfeld Modell Inhalte auswählen die Option **aus Datenbank generieren aus**, und klicken Sie dann auf **weiter**.</span><span class="sxs-lookup"><span data-stu-id="bf734-132">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="bf734-133">Klicken Sie auf neue Verbindung.</span><span class="sxs-lookup"><span data-stu-id="bf734-133">Click New Connection.</span></span> <span data-ttu-id="bf734-134">Geben Sie im Dialogfeld Verbindungs Eigenschaften den Servernamen ein (z. b. **(localdb) \\ mssqllocaldb**), wählen Sie die Authentifizierungsmethode aus, geben Sie **School**   als Datenbanknamen School ein, und klicken Sie dann auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="bf734-134">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="bf734-135">Das Dialogfeld Wählen Sie Ihre Datenverbindung aus wird mit Ihrer Daten bankverbindungs Einstellung aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="bf734-135">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="bf734-136">Überprüfen Sie im Dialogfeld Wählen Sie Ihre Datenbankobjekte aus den Knoten **Tabellen**   .</span><span class="sxs-lookup"><span data-stu-id="bf734-136">In the Choose Your Database Objects dialog box, check the **Tables** node.</span></span> <span data-ttu-id="bf734-137">Dadurch werden alle Tabellen dem Modell " **School** " hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="bf734-137">This will add all the tables to the **School** model.</span></span>
-   <span data-ttu-id="bf734-138">Klicken Sie auf **Finish**.</span><span class="sxs-lookup"><span data-stu-id="bf734-138">Click **Finish**.</span></span>
-   <span data-ttu-id="bf734-139">Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf die Datei **definingquerymodel. edmx** , und wählen Sie **Öffnen mit...** aus.</span><span class="sxs-lookup"><span data-stu-id="bf734-139">In Solution Explorer, right-click the **DefiningQueryModel.edmx** file and select **Open With…**.</span></span>
-   <span data-ttu-id="bf734-140">Wählen Sie den **XML-Editor (Text)** aus.</span><span class="sxs-lookup"><span data-stu-id="bf734-140">Select **XML (Text) Editor**.</span></span>

    ![XML-Editor](~/ef6/media/xmleditor.png)

-   <span data-ttu-id="bf734-142">Klicken Sie auf **Ja** , wenn Sie mit der folgenden Meldung aufgefordert werden:</span><span class="sxs-lookup"><span data-stu-id="bf734-142">Click **Yes** if prompted with the following message:</span></span>

    ![Warnung 2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a><span data-ttu-id="bf734-144">Hinzufügen einer definierenden Abfrage</span><span class="sxs-lookup"><span data-stu-id="bf734-144">Add a Defining Query</span></span>

<span data-ttu-id="bf734-145">In diesem Schritt wird der XML-Editor verwendet, um dem SSDL-Abschnitt der EDMX-Datei eine definierende Abfrage und einen Entitätstyp hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="bf734-145">In this step we will use the XML Editor to add a defining query and an entity type to the SSDL section of the .edmx file.</span></span> 

-   <span data-ttu-id="bf734-146">Fügen Sie **EntitySet**   dem SSDL-Abschnitt der EDMX-Datei ein EntitySet-Element hinzu (Zeile 5 bis 13).</span><span class="sxs-lookup"><span data-stu-id="bf734-146">Add an **EntitySet** element to the SSDL section of the .edmx file (line 5 thru 13).</span></span> <span data-ttu-id="bf734-147">Geben Sie Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="bf734-147">Specify the following:</span></span>
    -   <span data-ttu-id="bf734-148">Es werden nur die Attribute **Name**   und **EntityType**   des **EntitySet**-   Elements angegeben.</span><span class="sxs-lookup"><span data-stu-id="bf734-148">Only the **Name** and **EntityType** attributes of the **EntitySet** element are specified.</span></span>
    -   <span data-ttu-id="bf734-149">Der voll qualifizierte Name des Entitäts Typs wird im **EntityType**-   Attribut verwendet.</span><span class="sxs-lookup"><span data-stu-id="bf734-149">The fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="bf734-150">Die auszuführende SQL-Anweisung wird im **DefiningQuery**-   Element angegeben.</span><span class="sxs-lookup"><span data-stu-id="bf734-150">The SQL statement to be executed is specified in the **DefiningQuery** element.</span></span>

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

-   <span data-ttu-id="bf734-151">Fügen Sie das **EntityType** -Element dem SSDL-Abschnitt der EDMX-Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="bf734-151">Add the **EntityType** element to the SSDL section of the .edmx.</span></span> <span data-ttu-id="bf734-152">Datei wie unten gezeigt.</span><span class="sxs-lookup"><span data-stu-id="bf734-152">file as shown below.</span></span> <span data-ttu-id="bf734-153">Beachten Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="bf734-153">Note the following:</span></span>
    -   <span data-ttu-id="bf734-154">Der Wert des Attributs " **Name** " entspricht dem Wert des Attributs " **EntityType** " im obigen **EntitySet** -Element, obwohl der voll qualifizierte Name des Entitäts Typs im **EntityType** -Attribut verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bf734-154">The value of the **Name** attribute corresponds to the value of the **EntityType** attribute in the **EntitySet** element above, although the fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="bf734-155">Die Eigenschaftsnamen entsprechen den Spaltennamen, die von der SQL-Anweisung im **DefiningQuery** -Element (oben) zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="bf734-155">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element (above).</span></span>
    -   <span data-ttu-id="bf734-156">In diesem Beispiel wird durch einen aus drei Eigenschaften bestehenden Entitätsschlüssel sichergestellt, dass der Schlüsselwert eindeutig ist.</span><span class="sxs-lookup"><span data-stu-id="bf734-156">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

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
> <span data-ttu-id="bf734-157">Wenn Sie später den **Modellaktualisierungs-Assistenten** ausführen, werden alle Änderungen, die am Speichermodell vorgenommen werden, einschließlich der Definition von Abfragen, überschrieben.</span><span class="sxs-lookup"><span data-stu-id="bf734-157">If later you run the **Update Model Wizard** dialog, any changes made to the storage model, including defining queries, will be overwritten.</span></span>

 

## <a name="add-an-entity-type-to-the-model"></a><span data-ttu-id="bf734-158">Hinzufügen eines Entitäts Typs zum Modell</span><span class="sxs-lookup"><span data-stu-id="bf734-158">Add an Entity Type to the Model</span></span>

<span data-ttu-id="bf734-159">In diesem Schritt fügen wir den Entitätstyp mit dem EF-Designer dem konzeptionellen Modell hinzu.</span><span class="sxs-lookup"><span data-stu-id="bf734-159">In this step we will add the entity type to the conceptual model using the EF Designer.</span></span> <span data-ttu-id="bf734-160">Beachten Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="bf734-160"> Note the following:</span></span>

-   <span data-ttu-id="bf734-161">Der **Name** der Entität entspricht dem Wert des Attributs **EntityType** im obigen **EntitySet** -Element.</span><span class="sxs-lookup"><span data-stu-id="bf734-161">The **Name** of the entity corresponds to the value of the **EntityType** attribute in the **EntitySet** element above.</span></span>
-   <span data-ttu-id="bf734-162">Die Eigenschaftsnamen entsprechen den Spaltennamen, die von der SQL-Anweisung im obigen **DefiningQuery** -Element zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="bf734-162">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element above.</span></span>
-   <span data-ttu-id="bf734-163">In diesem Beispiel wird durch einen aus drei Eigenschaften bestehenden Entitätsschlüssel sichergestellt, dass der Schlüsselwert eindeutig ist.</span><span class="sxs-lookup"><span data-stu-id="bf734-163">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

<span data-ttu-id="bf734-164">Öffnen Sie das Modell im EF-Designer.</span><span class="sxs-lookup"><span data-stu-id="bf734-164">Open the model in the EF Designer.</span></span>

-   <span data-ttu-id="bf734-165">Doppelklicken Sie auf definingquerymodel. edmx.</span><span class="sxs-lookup"><span data-stu-id="bf734-165">Double-click the DefiningQueryModel.edmx.</span></span>
-   <span data-ttu-id="bf734-166">Sagen Sie **Ja** , dass die folgende Meldung angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="bf734-166">Say **Yes** to the following message:</span></span>

    ![Warnung 2](~/ef6/media/warning2.png)

 

<span data-ttu-id="bf734-168">Die Entity Designer, die eine Entwurfs Oberfläche zum Bearbeiten des Modells bereitstellt, wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="bf734-168">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

-   <span data-ttu-id="bf734-169">Klicken Sie mit der rechten Maustaste auf die Designer Oberfläche, und wählen Sie **neue** - &gt; **Entität**hinzufügen aus.</span><span class="sxs-lookup"><span data-stu-id="bf734-169">Right-click the designer surface and select **Add New**-&gt;**Entity…**.</span></span>
-   <span data-ttu-id="bf734-170">Geben Sie für den Entitäts Namen und den **CourseID** für die **Schlüsseleigenschaft**den Wert **gradereport** an.</span><span class="sxs-lookup"><span data-stu-id="bf734-170">Specify **GradeReport** for the entity name and **CourseID** for the **Key Property**.</span></span>
-   <span data-ttu-id="bf734-171">Klicken Sie mit der rechten Maustaste auf die Entität **gradereport** , und wählen Sie **neue** - &gt; **skalare Eigenschaft**hinzufügen</span><span class="sxs-lookup"><span data-stu-id="bf734-171">Right-click the **GradeReport** entity and select **Add New**-&gt; **Scalar Property**.</span></span>
-   <span data-ttu-id="bf734-172">Ändern Sie den Standardnamen der Eigenschaft in **FirstName**.</span><span class="sxs-lookup"><span data-stu-id="bf734-172">Change the default name of the property to **FirstName**.</span></span>
-   <span data-ttu-id="bf734-173">Fügen Sie eine weitere skalare Eigenschaft hinzu, und geben Sie **LastName** als Name an.</span><span class="sxs-lookup"><span data-stu-id="bf734-173">Add another scalar property and specify **LastName** for the name.</span></span>
-   <span data-ttu-id="bf734-174">Fügen Sie eine weitere skalare Eigenschaft hinzu, und geben Sie **Grade** für den Namen an.</span><span class="sxs-lookup"><span data-stu-id="bf734-174">Add another scalar property and specify **Grade** for the name.</span></span>
-   <span data-ttu-id="bf734-175">Ändern Sie im **Eigenschaften** Fenster die **Type** -Eigenschaft der **Klasse**in **Decimal**.</span><span class="sxs-lookup"><span data-stu-id="bf734-175">In the **Properties** window, change the **Grade**’s **Type** property to **Decimal**.</span></span>
-   <span data-ttu-id="bf734-176">Wählen Sie die Eigenschaften **FirstName** und **LastName** aus.</span><span class="sxs-lookup"><span data-stu-id="bf734-176">Select the **FirstName** and **LastName** properties.</span></span>
-   <span data-ttu-id="bf734-177">Ändern Sie im **Eigenschaften** Fenster den Wert der **EntityKey** -Eigenschaft in **true**.</span><span class="sxs-lookup"><span data-stu-id="bf734-177">In the **Properties** window, change the **EntityKey** property value to **True**.</span></span>

<span data-ttu-id="bf734-178">Folglich wurden dem **CSDL** -Abschnitt der EDMX-Datei die folgenden Elemente hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="bf734-178">As a result, the following elements were added to the **CSDL** section of the .edmx file.</span></span>

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a><span data-ttu-id="bf734-179">Zuordnen der definierenden Abfrage zum Entitätstyp</span><span class="sxs-lookup"><span data-stu-id="bf734-179">Map the Defining Query to the Entity Type</span></span>

<span data-ttu-id="bf734-180">In diesem Schritt wird das Fenster Mappingdetails verwendet, um die konzeptionellen Entitäts Typen und Speicher Entitäts Typen zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="bf734-180">In this step, we will use the Mapping Details window to map the conceptual and storage entity types.</span></span>

-   <span data-ttu-id="bf734-181">Klicken Sie mit der rechten Maustaste auf die Entität **gradereport** auf der Entwurfs Oberfläche, und wählen Sie **Tabellen Zuordnung**.</span><span class="sxs-lookup"><span data-stu-id="bf734-181">Right-click the **GradeReport** entity on the design surface and select **Table Mapping**.</span></span>  
    <span data-ttu-id="bf734-182">Das Fenster **Mappingdetails** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="bf734-182">The **Mapping Details** window is displayed.</span></span>
-   <span data-ttu-id="bf734-183">Wählen Sie in der Dropdown Liste \*\* &lt; Tabelle oder &gt; Sicht hinzufügen\*\* den Wert **gradereport** aus (befindet sich unter **Tabelle**s).</span><span class="sxs-lookup"><span data-stu-id="bf734-183">Select **GradeReport** from the **&lt;Add a Table or View&gt;** dropdown list (located under **Table**s).</span></span>  
    <span data-ttu-id="bf734-184">Standard Zuordnungen zwischen dem Entitätstyp **GradeReport** "konzeptionell" und "Speicher" werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="bf734-184">Default mappings between the conceptual and storage **GradeReport** entity type appear.</span></span>  
    <span data-ttu-id="bf734-185">![Mapping Details3](~/ef6/media/mappingdetails.png)</span><span class="sxs-lookup"><span data-stu-id="bf734-185">![Mapping Details3](~/ef6/media/mappingdetails.png)</span></span>

<span data-ttu-id="bf734-186">Folglich wird das **EntitySetMapping**-   Element dem Mapping-Abschnitt der EDMX-Datei hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="bf734-186">As a result, the **EntitySetMapping** element is added to the mapping section of the .edmx file.</span></span> 

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

-   <span data-ttu-id="bf734-187">Kompilieren Sie die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="bf734-187">Compile the application.</span></span>

 

## <a name="call-the-defining-query-in-your-code"></a><span data-ttu-id="bf734-188">Die definierende Abfrage im Code aufzurufen</span><span class="sxs-lookup"><span data-stu-id="bf734-188">Call the Defining Query in your Code</span></span>

<span data-ttu-id="bf734-189">Sie können jetzt die definierende Abfrage ausführen, indem Sie den Entitätstyp **gradereportierung** verwenden.</span><span class="sxs-lookup"><span data-stu-id="bf734-189">You can now execute the defining query by using the **GradeReport** entity type.</span></span> 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
