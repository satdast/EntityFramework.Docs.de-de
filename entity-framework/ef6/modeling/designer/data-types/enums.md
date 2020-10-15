---
title: Aufzählungs Unterstützung-EF-Designer-EF6
description: Aufzählungs Unterstützung-EF-Designer in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/data-types/enums
ms.openlocfilehash: 42508b533249bf01c76939f864a23adc86a08822
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064925"
---
# <a name="enum-support---ef-designer"></a><span data-ttu-id="a89b6-103">Aufzählungs Unterstützung-EF-Designer</span><span class="sxs-lookup"><span data-stu-id="a89b6-103">Enum Support - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="a89b6-104">**Nur EF5** : die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 5 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="a89b6-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="a89b6-105">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="a89b6-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="a89b6-106">In diesem Video und der schrittweisen exemplarischen Vorgehensweise wird die Verwendung von Enumerationstypen mit dem Entity Framework Designer erläutert.</span><span class="sxs-lookup"><span data-stu-id="a89b6-106">This video and step-by-step walkthrough shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="a89b6-107">Außerdem wird veranschaulicht, wie Sie-auf--Aufstände in einer LINQ-Abfrage verwenden.</span><span class="sxs-lookup"><span data-stu-id="a89b6-107">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="a89b6-108">In dieser exemplarischen Vorgehensweise wird Model First verwendet, um eine neue Datenbank zu erstellen, aber der EF-Designer kann auch mit dem [Database First](xref:ef6/modeling/designer/workflows/database-first) Workflow verwendet werden, um eine Zuordnung zu einer vorhandenen Datenbank herzustellen.</span><span class="sxs-lookup"><span data-stu-id="a89b6-108">This walkthrough will use Model First to create a new database, but the EF Designer can also be used with the [Database First](xref:ef6/modeling/designer/workflows/database-first) workflow to map to an existing database.</span></span>

<span data-ttu-id="a89b6-109">Die Aufzählungs Unterstützung wurde in Entity Framework 5 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="a89b6-109">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="a89b6-110">Wenn Sie die neuen Funktionen wie Enumerationstypen, räumliche Datentypen und Tabellenwert Funktionen verwenden möchten, müssen Sie .NET Framework 4,5-Zielversion verwenden.</span><span class="sxs-lookup"><span data-stu-id="a89b6-110">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="a89b6-111">Visual Studio 2012 hat standardmäßig .NET 4,5 als Ziel.</span><span class="sxs-lookup"><span data-stu-id="a89b6-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="a89b6-112">In Entity Framework kann eine Enumeration die folgenden zugrunde liegenden Typen aufweisen: **Byte**, **Int16**, **Int32**, **Int64** oder **SByte**.</span><span class="sxs-lookup"><span data-stu-id="a89b6-112">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="a89b6-113">Video ansehen</span><span class="sxs-lookup"><span data-stu-id="a89b6-113">Watch the Video</span></span>
<span data-ttu-id="a89b6-114">In diesem Video wird gezeigt, wie Enumerationstypen mit dem Entity Framework Designer verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a89b6-114">This video shows how to use enum types with the Entity Framework Designer.</span></span> <span data-ttu-id="a89b6-115">Außerdem wird veranschaulicht, wie Sie-auf--Aufstände in einer LINQ-Abfrage verwenden.</span><span class="sxs-lookup"><span data-stu-id="a89b6-115">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="a89b6-116">**Präsentiert von**: Julia kornich</span><span class="sxs-lookup"><span data-stu-id="a89b6-116">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="a89b6-117">**Video**: [WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv)  |  [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span><span class="sxs-lookup"><span data-stu-id="a89b6-117">**Video**: [WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv) | [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="a89b6-118">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="a89b6-118">Pre-Requisites</span></span>

<span data-ttu-id="a89b6-119">Sie müssen Visual Studio 2012, Ultimate, Premium, Professional oder Web Express Edition installiert haben, um diese exemplarische Vorgehensweise abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="a89b6-119">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="a89b6-120">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="a89b6-120">Set up the Project</span></span>

1.  <span data-ttu-id="a89b6-121">Öffnen Sie Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="a89b6-121">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="a89b6-122">Zeigen Sie im Menü **Datei** auf **neu**, und klicken Sie dann auf **Projekt** .</span><span class="sxs-lookup"><span data-stu-id="a89b6-122">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="a89b6-123">Klicken Sie im linken Bereich auf \*\*Visual C \# \*\*, und wählen Sie dann die **Konsolen** Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="a89b6-123">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="a89b6-124">Geben Sie **enumef Designer** als Namen für das Projekt ein, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="a89b6-124">Enter **EnumEFDesigner** as the name of the project and click **OK**</span></span>

## <a name="create-a-new-model-using-the-ef-designer"></a><span data-ttu-id="a89b6-125">Erstellen eines neuen Modells mit dem EF-Designer</span><span class="sxs-lookup"><span data-stu-id="a89b6-125">Create a New Model using the EF Designer</span></span>

1.  <span data-ttu-id="a89b6-126">Klicken Sie mit der rechten Maustaste auf den Projektnamen Projektmappen-Explorer, zeigen Sie auf **Hinzufügen**, und klicken Sie dann auf **Neues Element**</span><span class="sxs-lookup"><span data-stu-id="a89b6-126">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="a89b6-127">Wählen Sie im linken Menü **Daten** aus, und wählen Sie dann im Bereich Vorlagen die Option **ADO.NET Entity Data Model** aus.</span><span class="sxs-lookup"><span data-stu-id="a89b6-127">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane</span></span>
3.  <span data-ttu-id="a89b6-128">Geben Sie als Dateiname **enumtestmodel. edmx** ein, und klicken Sie dann auf **Hinzufügen** .</span><span class="sxs-lookup"><span data-stu-id="a89b6-128">Enter **EnumTestModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="a89b6-129">Wählen Sie auf der Seite des Entity Data Model-Assistenten im Dialogfeld Modell Inhalte auswählen die Option **leeres Modell** aus.</span><span class="sxs-lookup"><span data-stu-id="a89b6-129">On the Entity Data Model Wizard page, select **Empty Model** in the Choose Model Contents dialog box</span></span>
5.  <span data-ttu-id="a89b6-130">Klicken Sie auf **Fertig stellen**.</span><span class="sxs-lookup"><span data-stu-id="a89b6-130">Click **Finish**</span></span>

<span data-ttu-id="a89b6-131">Die Entity Designer, die eine Entwurfs Oberfläche zum Bearbeiten des Modells bereitstellt, wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a89b6-131">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

<span data-ttu-id="a89b6-132">Der Assistent führt die folgenden Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="a89b6-132">The wizard performs the following actions:</span></span>

-   <span data-ttu-id="a89b6-133">Generiert die Datei "enumtestmodel. edmx", die das konzeptionelle Modell, das Speichermodell und die Zuordnung zwischen Ihnen definiert.</span><span class="sxs-lookup"><span data-stu-id="a89b6-133">Generates the EnumTestModel.edmx file that defines the conceptual model, the storage model, and the mapping between them.</span></span> <span data-ttu-id="a89b6-134">Legt die metadatenartefaktverarbeitungs-Eigenschaft der EDMX-Datei fest, die in die Ausgabeassembly eingebettet werden soll, sodass die generierten Metadatendateien in die Assembly eingebettet werden</span><span class="sxs-lookup"><span data-stu-id="a89b6-134">Sets the Metadata Artifact Processing property of the .edmx file to Embed in Output Assembly so the generated metadata files get embedded into the assembly.</span></span>
-   <span data-ttu-id="a89b6-135">Fügt einen Verweis auf die folgenden Assemblys hinzu: EntityFramework, System. ComponentModel. DataAnnotations und System. Data. Entity.</span><span class="sxs-lookup"><span data-stu-id="a89b6-135">Adds a reference to the following assemblies: EntityFramework, System.ComponentModel.DataAnnotations, and System.Data.Entity.</span></span>
-   <span data-ttu-id="a89b6-136">Erstellt EnumTestModel.TT-und EnumTestModel.Context.tt-Dateien und fügt Sie der EDMX-Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="a89b6-136">Creates EnumTestModel.tt and EnumTestModel.Context.tt files and adds them under the .edmx file.</span></span> <span data-ttu-id="a89b6-137">Diese T4-Vorlagen Dateien generieren den Code, der den von dbcontext abgeleiteten Typ und poco-Typen definiert, die den Entitäten im edmx-Modell zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="a89b6-137">These T4 template files generate the code that defines the DbContext derived type and POCO types that map to the entities in the .edmx model.</span></span>

## <a name="add-a-new-entity-type"></a><span data-ttu-id="a89b6-138">Neuen Entitätstyp hinzufügen</span><span class="sxs-lookup"><span data-stu-id="a89b6-138">Add a New Entity Type</span></span>

1.  <span data-ttu-id="a89b6-139">Klicken Sie mit der rechten Maustaste auf einen leeren Bereich der Entwurfs Oberfläche, und wählen Sie **Add- &gt; Entity**, das Dialogfeld neue Entität wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a89b6-139">Right-click an empty area of the design surface, select **Add -&gt; Entity**, the New Entity dialog box appears</span></span>
2.  <span data-ttu-id="a89b6-140">Geben Sie die **Abteilung** für den Typnamen an, und geben Sie **DepartmentID** als Schlüssel Eigenschaftsnamen an, belassen Sie den Typ als **Int32** .</span><span class="sxs-lookup"><span data-stu-id="a89b6-140">Specify **Department** for the type name and specify **DepartmentID** for the key property name, leave the type as **Int32**</span></span>
3.  <span data-ttu-id="a89b6-141">Klicken Sie auf **OK**</span><span class="sxs-lookup"><span data-stu-id="a89b6-141">Click **OK**</span></span>
4.  <span data-ttu-id="a89b6-142">Klicken Sie mit der rechten Maustaste auf die Entität, und wählen Sie **Add New- &gt; Scalar**</span><span class="sxs-lookup"><span data-stu-id="a89b6-142">Right-click the entity and select **Add New -&gt; Scalar Property**</span></span>
5.  <span data-ttu-id="a89b6-143">Benennen Sie die neue Eigenschaft in **Name** um.</span><span class="sxs-lookup"><span data-stu-id="a89b6-143">Rename the new property to **Name**</span></span>
6.  <span data-ttu-id="a89b6-144">Ändern Sie den Typ der neuen Eigenschaft in **Int32** (Standardmäßig ist die neue Eigenschaft vom Typ String), um den Typ zu ändern, öffnen Sie die Eigenschaftenfenster, und ändern Sie die Type-Eigenschaft in **Int32** .</span><span class="sxs-lookup"><span data-stu-id="a89b6-144">Change the type of the new property to **Int32** (by default, the new property is of String type) To change the type, open the Properties window and change the Type property to **Int32**</span></span>
7.  <span data-ttu-id="a89b6-145">Fügen Sie eine weitere skalare Eigenschaft hinzu, und benennen Sie Sie in **Budget**um. ändern Sie den Typ in **Decimal**</span><span class="sxs-lookup"><span data-stu-id="a89b6-145">Add another scalar property and rename it to **Budget**, change the type to **Decimal**</span></span>

## <a name="add-an-enum-type"></a><span data-ttu-id="a89b6-146">Hinzufügen eines Aufzählungs Typs</span><span class="sxs-lookup"><span data-stu-id="a89b6-146">Add an Enum Type</span></span>

1.  <span data-ttu-id="a89b6-147">Klicken Sie im Entity Framework Designer mit der rechten Maustaste auf die Eigenschaft Name, und wählen Sie in **Enumeration konvertieren** aus.</span><span class="sxs-lookup"><span data-stu-id="a89b6-147">In the Entity Framework Designer, right-click the Name property, select **Convert to enum**</span></span>

    ![In Aufzählung konvertieren](~/ef6/media/converttoenum.png)

2.  <span data-ttu-id="a89b6-149">Geben Sie im Dialogfeld Enumeration **Hinzufügen** den Namen **departmentnames** für den Enumerationstypnamen ein, ändern Sie den zugrunde liegenden Typ in **Int32**, und fügen Sie dann die folgenden Member zum-Typ hinzu: Englisch, Math und Economics.</span><span class="sxs-lookup"><span data-stu-id="a89b6-149">In the **Add Enum** dialog box type **DepartmentNames** for the Enum Type Name, change the Underlying Type to **Int32**, and then add the following members to the type: English, Math, and Economics</span></span>

    ![Umerationstyp hinzufügen](~/ef6/media/addenumtype.png)

3.  <span data-ttu-id="a89b6-151">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="a89b6-151">Press **OK**</span></span>
4.  <span data-ttu-id="a89b6-152">Speichern Sie das Modell, und erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="a89b6-152">Save the model and build the project</span></span>
    > [!NOTE]
    > <span data-ttu-id="a89b6-153">Wenn Sie erstellen, werden möglicherweise Warnungen zu nicht zugeordneten Entitäten und Zuordnungen in der Fehlerliste angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a89b6-153">When you build, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="a89b6-154">Sie können diese Warnungen ignorieren, da die Fehler nach dem Generieren der Datenbank aus dem Modell entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="a89b6-154">You can ignore these warnings because after we choose to generate the database from the model, the errors will go away.</span></span>

<span data-ttu-id="a89b6-155">Wenn Sie die Eigenschaftenfenster betrachten, werden Sie feststellen, dass der Typ der Name-Eigenschaft in **departmentnames** geändert wurde und der neu hinzugefügte Enumerationstyp der Liste der Typen hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="a89b6-155">If you look at the Properties window, you will notice that the type of the Name property was changed to **DepartmentNames** and the newly added enum type was added to the list of types.</span></span>

<span data-ttu-id="a89b6-156">Wenn Sie zum Fenstermodell Browser wechseln, sehen Sie, dass der Typ auch dem Knoten Enumerationstypen hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="a89b6-156">If you switch to the Model Browser window, you will see that the type was also added to the Enum Types node.</span></span>

![Modell Browser](~/ef6/media/modelbrowser.png)

>[!NOTE]
> <span data-ttu-id="a89b6-158">Sie können in diesem Fenster auch neue Enumerationstypen hinzufügen, indem Sie auf die Rechte Maustaste klicken und **Enumerationstyp hinzufügen**auswählen.</span><span class="sxs-lookup"><span data-stu-id="a89b6-158">You can also add new enum types from this window by clicking the right mouse button and selecting **Add Enum Type**.</span></span> <span data-ttu-id="a89b6-159">Nachdem der Typ erstellt wurde, wird er in der Liste der Typen angezeigt, und Sie können einer Eigenschaft zuordnen.</span><span class="sxs-lookup"><span data-stu-id="a89b6-159">Once the type is created it will appear in the list of types and you would be able to associate with a property</span></span>

## <a name="generate-database-from-model"></a><span data-ttu-id="a89b6-160">Datenbank aus Modell generieren</span><span class="sxs-lookup"><span data-stu-id="a89b6-160">Generate Database from Model</span></span>

<span data-ttu-id="a89b6-161">Nun können wir eine Datenbank generieren, die auf dem Modell basiert.</span><span class="sxs-lookup"><span data-stu-id="a89b6-161">Now we can generate a database that is based on the model.</span></span>

1.  <span data-ttu-id="a89b6-162">Klicken Sie mit der rechten Maustaste auf einen leeren Bereich auf der Entity Designer Oberfläche, und wählen Sie **Datenbank aus Modell generieren** .</span><span class="sxs-lookup"><span data-stu-id="a89b6-162">Right-click an empty space on the Entity Designer surface and select **Generate Database from Model**</span></span>
2.  <span data-ttu-id="a89b6-163">Das Dialog Feld Wählen Sie Ihre Datenverbindung aus des Assistenten zum Generieren von Datenbanken wird angezeigt. Klicken Sie auf die Schaltfläche **neue Verbindung** **, und geben** Sie für den Servernamen **(localdb) \\ mssqllocaldb** an, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="a89b6-163">The Choose Your Data Connection Dialog Box of the Generate Database Wizard is displayed Click the **New Connection** button Specify **(localdb)\\mssqllocaldb** for the server name and **EnumTest** for the database and click **OK**</span></span>
3.  <span data-ttu-id="a89b6-164">Wenn Sie in einem Dialogfeld gefragt werden, ob Sie eine neue Datenbank erstellen möchten, klicken Sie auf **Ja**.</span><span class="sxs-lookup"><span data-stu-id="a89b6-164">A dialog asking if you want to create a new database will pop up, click **Yes**.</span></span>
4.  <span data-ttu-id="a89b6-165">Klicken Sie auf **weiter** , und der Assistent zum Erstellen einer Datenbank generiert die Datendefinitionssprache (DDL) zum Erstellen einer Datenbank. die generierte DDL wird im Dialog Feld Zusammenfassung und Einstellungen angezeigt. Hinweis: die DDL enthält keine Definition für eine Tabelle, die dem Enumerationstyp zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="a89b6-165">Click **Next** and the Create Database Wizard generates data definition language (DDL) for creating a database The generated DDL is displayed in the Summary and Settings Dialog Box Note, that the DDL does not contain a definition for a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="a89b6-166">Klicken Sie auf **Fertig** stellen klicken Sie das DDL-Skript nicht aus.</span><span class="sxs-lookup"><span data-stu-id="a89b6-166">Click **Finish** Clicking Finish does not execute the DDL script.</span></span>
6.  <span data-ttu-id="a89b6-167">Der Assistent zum Erstellen einer Datenbank führt Folgendes aus: öffnet die Datei " **enumtest. edmx. SQL** " im T-SQL-Editor, generiert die Speicher Schema-und Zuordnungsabschnitte der EDMX-Datei und fügt der App.config</span><span class="sxs-lookup"><span data-stu-id="a89b6-167">The Create Database Wizard does the following: Opens the **EnumTest.edmx.sql** in T-SQL Editor Generates the store schema and mapping sections of the EDMX file Adds connection string information to the App.config file</span></span>
7.  <span data-ttu-id="a89b6-168">Klicken Sie im T-SQL-Editor auf die Rechte Maustaste, und wählen Sie das Dialogfeld Verbindung mit Server herstellen aus, und **Geben Sie** die **Connect** Verbindungsinformationen aus Schritt 2 ein.</span><span class="sxs-lookup"><span data-stu-id="a89b6-168">Click the right mouse button in T-SQL Editor and select **Execute** The Connect to Server dialog appears, enter the connection information from step 2 and click **Connect**</span></span>
8.  <span data-ttu-id="a89b6-169">Um das generierte Schema anzuzeigen, klicken Sie in SQL Server-Objekt-Explorer mit der rechten Maustaste auf den Datenbanknamen, und wählen Sie **Aktualisieren** .</span><span class="sxs-lookup"><span data-stu-id="a89b6-169">To view the generated schema, right-click on the database name in SQL Server Object Explorer and select **Refresh**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="a89b6-170">Persistenz und Abrufen von Daten</span><span class="sxs-lookup"><span data-stu-id="a89b6-170">Persist and Retrieve Data</span></span>

<span data-ttu-id="a89b6-171">Öffnen Sie die Datei Program.cs, in der die Main-Methode definiert ist.</span><span class="sxs-lookup"><span data-stu-id="a89b6-171">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="a89b6-172">Fügen Sie der Main-Funktion den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="a89b6-172">Add the following code into the Main function.</span></span> <span data-ttu-id="a89b6-173">Der Code fügt dem Kontext ein neues Abteilungs Objekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="a89b6-173">The code adds a new Department object to the context.</span></span> <span data-ttu-id="a89b6-174">Anschließend werden die Daten gespeichert.</span><span class="sxs-lookup"><span data-stu-id="a89b6-174">It then saves the data.</span></span> <span data-ttu-id="a89b6-175">Der Code führt außerdem eine LINQ-Abfrage aus, die eine Abteilung zurückgibt, in der der Name departmentnames. English lautet.</span><span class="sxs-lookup"><span data-stu-id="a89b6-175">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

``` csharp
using (var context = new EnumTestModelContainer())
{
    context.Departments.Add(new Department{ Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} and Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

<span data-ttu-id="a89b6-176">Kompilieren Sie die Anwendung, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="a89b6-176">Compile and run the application.</span></span> <span data-ttu-id="a89b6-177">Das Programm erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="a89b6-177">The program produces the following output:</span></span>

```console
DepartmentID: 1 Name: English
```

<span data-ttu-id="a89b6-178">Zum Anzeigen von Daten in der Datenbank klicken Sie in SQL Server-Objekt-Explorer mit der rechten Maustaste auf den Datenbanknamen, und wählen Sie **Aktualisieren**aus.</span><span class="sxs-lookup"><span data-stu-id="a89b6-178">To view data in the database, right-click on the database name in SQL Server Object Explorer and select **Refresh**.</span></span> <span data-ttu-id="a89b6-179">Klicken Sie dann auf die Rechte Maustaste in der Tabelle, und wählen Sie **Daten anzeigen**aus.</span><span class="sxs-lookup"><span data-stu-id="a89b6-179">Then, click the right mouse button on the table and select **View Data**.</span></span>

## <a name="summary"></a><span data-ttu-id="a89b6-180">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="a89b6-180">Summary</span></span>

<span data-ttu-id="a89b6-181">In dieser exemplarischen Vorgehensweise wurde erläutert, wie Enumerationstypen mithilfe der Entity Framework Designer zugeordnet werden und wie Enumerationen im Code verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a89b6-181">In this walkthrough we looked at how to map enum types using the Entity Framework Designer and how to use enums in code.</span></span> 
