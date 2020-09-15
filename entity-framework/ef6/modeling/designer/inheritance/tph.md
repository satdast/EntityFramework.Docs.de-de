---
title: Designer-TPH-Vererbung-EF6
description: Designer-TPH-Vererbung in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tph
ms.openlocfilehash: 39675f9533dfef0ddad1867e3b70cd13b30708ea
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073395"
---
# <a name="designer-tph-inheritance"></a><span data-ttu-id="69e77-103">Designer-TPH-Vererbung</span><span class="sxs-lookup"><span data-stu-id="69e77-103">Designer TPH Inheritance</span></span>
<span data-ttu-id="69e77-104">Diese schrittweise exemplarische Vorgehensweise veranschaulicht, wie Sie die "Tabelle pro Hierarchie"-Vererbung (TPH) im konzeptionellen Modell mit dem Entity Framework Designer (EF-Designer) implementieren.</span><span class="sxs-lookup"><span data-stu-id="69e77-104">This step-by-step walkthrough shows how to implement table-per-hierarchy (TPH) inheritance in your conceptual model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="69e77-105">Die TPH-Vererbung verwendet eine Datenbanktabelle, um Daten für alle Entitäts Typen in einer Vererbungs Hierarchie zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="69e77-105">TPH inheritance uses one database table to maintain data for all of the entity types in an inheritance hierarchy.</span></span>

<span data-ttu-id="69e77-106">In dieser exemplarischen Vorgehensweise ordnen wir die Person-Tabelle drei Entitäts Typen zu: Person (der Basistyp), Student (abgeleitet von Person) und Instructor (abgeleitet von Person).</span><span class="sxs-lookup"><span data-stu-id="69e77-106">In this walkthrough we will map the Person table to three entity types: Person (the base type), Student (derives from Person), and Instructor (derives from Person).</span></span> <span data-ttu-id="69e77-107">Wir erstellen ein konzeptionelles Modell aus der Datenbank (Database First) und ändern dann das Modell, um die TPH-Vererbung mit dem EF-Designer zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="69e77-107">We'll create a conceptual model from the database (Database First) and then alter the model to implement the TPH inheritance using the EF Designer.</span></span>

<span data-ttu-id="69e77-108">Es ist möglich, eine TPH-Vererbung mithilfe Model First zuzuordnen, aber Sie müssen einen komplexen Daten Bank Generierungs Workflow schreiben, der Komplex ist.</span><span class="sxs-lookup"><span data-stu-id="69e77-108">It is possible to map to a TPH inheritance using Model First but you would have to write your own database generation workflow which is complex.</span></span> <span data-ttu-id="69e77-109">Anschließend weisen Sie diesen Workflow der Eigenschaft **Daten Bank Generierungs Workflow** im EF-Designer zu.</span><span class="sxs-lookup"><span data-stu-id="69e77-109">You would then assign this workflow to the **Database Generation Workflow** property in the EF Designer.</span></span> <span data-ttu-id="69e77-110">Eine einfachere Alternative ist die Verwendung von Code First.</span><span class="sxs-lookup"><span data-stu-id="69e77-110">An easier alternative is to use Code First.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="69e77-111">Andere Vererbungs Optionen</span><span class="sxs-lookup"><span data-stu-id="69e77-111">Other Inheritance Options</span></span>

<span data-ttu-id="69e77-112">Tabelle pro Typ (TPT) ist eine andere Art von Vererbung, bei der separate Tabellen in der Datenbank Entitäten zugeordnet werden, die an der Vererbung teilnehmen.</span><span class="sxs-lookup"><span data-stu-id="69e77-112">Table-per-Type (TPT) is another type of inheritance in which separate tables in the database are mapped to entities that participate in the inheritance.</span></span> <span data-ttu-id="69e77-113">Informationen zum Zuordnen der "Tabelle pro Typ"-Vererbung mit dem EF-Designer finden Sie unter [EF-Designer-TPT-Vererbung](xref:ef6/modeling/designer/inheritance/tpt).</span><span class="sxs-lookup"><span data-stu-id="69e77-113"> For information about how to map Table-per-Type inheritance with the EF Designer, see [EF Designer TPT Inheritance](xref:ef6/modeling/designer/inheritance/tpt).</span></span>

<span data-ttu-id="69e77-114">"Tabelle pro konkrete Typvererbung" (TPC) und gemischte Vererbungs Modelle werden von der Entity Framework Runtime unterstützt, jedoch nicht vom EF-Designer unterstützt.</span><span class="sxs-lookup"><span data-stu-id="69e77-114">Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="69e77-115">Wenn Sie TPC oder gemischte Vererbung verwenden möchten, haben Sie zwei Möglichkeiten: Verwenden Sie Code First, oder bearbeiten Sie die EDMX-Datei manuell.</span><span class="sxs-lookup"><span data-stu-id="69e77-115">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="69e77-116">Wenn Sie sich für die Arbeit mit der EDMX-Datei entscheiden, wird das Fenster Mappingdetails in den "abgesicherten Modus" versetzt, und Sie können den Designer nicht verwenden, um die Zuordnungen zu ändern.</span><span class="sxs-lookup"><span data-stu-id="69e77-116">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="69e77-117">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="69e77-117">Prerequisites</span></span>

<span data-ttu-id="69e77-118">Um die exemplarische Vorgehensweise nachzuvollziehen, benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="69e77-118">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="69e77-119">Eine aktuelle Version von Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="69e77-119">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="69e77-120">Die [Beispieldatenbank "School](xref:ef6/resources/school-database)".</span><span class="sxs-lookup"><span data-stu-id="69e77-120">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="69e77-121">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="69e77-121">Set up the Project</span></span>

-   <span data-ttu-id="69e77-122">Öffnen Sie Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="69e77-122">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="69e77-123">Wählen Sie **Datei- &gt; neu- &gt; Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="69e77-123">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="69e77-124">Klicken Sie im linken Bereich auf \*\*Visual C \# \*\*, und wählen Sie dann die **Konsolen** Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="69e77-124">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="69e77-125">Geben Sie **tphdbfirstsample**   als Name ein.</span><span class="sxs-lookup"><span data-stu-id="69e77-125">Enter **TPHDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="69e77-126">Wählen Sie **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="69e77-126">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="69e77-127">Modellerstellung</span><span class="sxs-lookup"><span data-stu-id="69e77-127">Create a Model</span></span>

-   <span data-ttu-id="69e77-128">Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen, und wählen Sie **Add- &gt; New Item**aus.</span><span class="sxs-lookup"><span data-stu-id="69e77-128">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="69e77-129">Wählen Sie im linken Menü **Daten** aus, und wählen Sie dann im Bereich Vorlagen die Option **ADO.NET Entity Data Model** aus.</span><span class="sxs-lookup"><span data-stu-id="69e77-129">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="69e77-130">Geben Sie als Dateiname **tphmodel. edmx** ein, und klicken Sie dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="69e77-130">Enter **TPHModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="69e77-131">Wählen Sie im Dialogfeld Modell Inhalte auswählen die Option **aus Datenbank generieren aus**, und klicken Sie dann auf **weiter**.</span><span class="sxs-lookup"><span data-stu-id="69e77-131">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="69e77-132">Klicken Sie auf **neue Verbindung**.</span><span class="sxs-lookup"><span data-stu-id="69e77-132">Click **New Connection**.</span></span>
    <span data-ttu-id="69e77-133">Geben Sie im Dialogfeld Verbindungs Eigenschaften den Servernamen ein (z. b. **(localdb) \\ mssqllocaldb**), wählen Sie die Authentifizierungsmethode aus, geben Sie **School**   als Datenbanknamen School ein, und klicken Sie dann auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="69e77-133">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="69e77-134">Das Dialogfeld Wählen Sie Ihre Datenverbindung aus wird mit Ihrer Daten bankverbindungs Einstellung aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="69e77-134">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="69e77-135">Wählen Sie im Dialogfeld Datenbankobjekte auswählen unter dem Knoten Tabellen die **Person** -Tabelle aus.</span><span class="sxs-lookup"><span data-stu-id="69e77-135">In the Choose Your Database Objects dialog box, under the Tables node, select the **Person** table.</span></span>
-   <span data-ttu-id="69e77-136">Klicken Sie auf **Finish**.</span><span class="sxs-lookup"><span data-stu-id="69e77-136">Click **Finish**.</span></span>

<span data-ttu-id="69e77-137">Die Entity Designer, die eine Entwurfs Oberfläche zum Bearbeiten des Modells bereitstellt, wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="69e77-137">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="69e77-138">Alle Objekte, die Sie im Dialogfeld Wählen Sie Ihre Datenbankobjekte ausgewählt haben, werden dem Modell hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="69e77-138">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

<span data-ttu-id="69e77-139">So sucht die **Person** -Tabelle in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="69e77-139">That is how the **Person** table looks in the database.</span></span>

![Person-Tabelle](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a><span data-ttu-id="69e77-141">Implementieren der "Tabelle pro Hierarchie"-Vererbung</span><span class="sxs-lookup"><span data-stu-id="69e77-141">Implement Table-per-Hierarchy Inheritance</span></span>

<span data-ttu-id="69e77-142">Die **Person** -Tabelle verfügt über die **diskriminatorspalte** , die einen von zwei Werten aufweisen kann: "Student" und "Instructor".</span><span class="sxs-lookup"><span data-stu-id="69e77-142">The **Person** table has the **Discriminator** column, which can have one of two values: “Student” and “Instructor”.</span></span> <span data-ttu-id="69e77-143">Abhängig vom Wert wird die **Person** -Tabelle der **Student** -Entität oder der **Instructor** -Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="69e77-143">Depending on the value the **Person** table will be mapped to the **Student** entity or the **Instructor** entity.</span></span> <span data-ttu-id="69e77-144">Die **Person** -Tabelle verfügt auch über zwei Spalten, **HireDate**   und **registrimentdate**, die **NULL-Werte** zulassen müssen, da eine Person nicht gleichzeitig Student und Dozenten sein kann (zumindest nicht in dieser exemplarischen Vorgehensweise).</span><span class="sxs-lookup"><span data-stu-id="69e77-144">The **Person** table also has two columns, **HireDate** and **EnrollmentDate**, which must be **nullable** because a person cannot be a student and an instructor at the same time (at least not in this walkthrough).</span></span>

### <a name="add-new-entities"></a><span data-ttu-id="69e77-145">Neue Entitäten hinzufügen</span><span class="sxs-lookup"><span data-stu-id="69e77-145">Add new Entities</span></span>

-   <span data-ttu-id="69e77-146">Fügen Sie eine neue Entität hinzu.</span><span class="sxs-lookup"><span data-stu-id="69e77-146">Add a new entity.</span></span>
    <span data-ttu-id="69e77-147">Klicken Sie dazu mit der rechten Maustaste auf einen leeren Bereich der Entwurfs Oberfläche der Entity Framework Designer, und wählen Sie **Add- &gt; Entity**aus.</span><span class="sxs-lookup"><span data-stu-id="69e77-147">To do this, right-click on an empty space of the design surface of the Entity Framework Designer, and select **Add-&gt;Entity**.</span></span>
-   <span data-ttu-id="69e77-148">Geben Sie **Instructor**   als **Entitäts Name**ein,   und wählen Sie in **Person**   der Dropdown Liste für den **Basistyp**Person aus.</span><span class="sxs-lookup"><span data-stu-id="69e77-148">Type **Instructor** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>
-   <span data-ttu-id="69e77-149">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="69e77-149">Click **OK**.</span></span>
-   <span data-ttu-id="69e77-150">Fügen Sie eine neue Entität hinzu.</span><span class="sxs-lookup"><span data-stu-id="69e77-150">Add another new entity.</span></span> <span data-ttu-id="69e77-151">Geben Sie **Student**   als **Entitäts Name**ein,   und wählen Sie in **Person**   der Dropdown Liste für den **Basistyp**Person aus.</span><span class="sxs-lookup"><span data-stu-id="69e77-151">Type **Student** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>

<span data-ttu-id="69e77-152">Der Entwurfs Oberfläche wurden zwei neue Entitäts Typen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="69e77-152">Two new entity types were added to the design surface.</span></span> <span data-ttu-id="69e77-153">Ein Pfeil zeigt von den neuen Entitäts Typen auf **Person**den   Entitätstyp Person. Dies deutet darauf hin, dass **Person**   der Basistyp für die neuen Entitäts Typen ist.</span><span class="sxs-lookup"><span data-stu-id="69e77-153">An arrow points from the new entity types to the **Person** entity type; this indicates that **Person** is the base type for the new entity types.</span></span>

-   <span data-ttu-id="69e77-154">Klicken Sie mit der rechten Maustaste auf die Eigenschaft **HireDate**   der **Person**-   Entität.</span><span class="sxs-lookup"><span data-stu-id="69e77-154">Right-click the **HireDate** property of the **Person** entity.</span></span> <span data-ttu-id="69e77-155">Wählen Sie **Ausschneiden** (oder drücken Sie STRG + X).</span><span class="sxs-lookup"><span data-stu-id="69e77-155">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="69e77-156">Klicken Sie mit der **Instructor**rechten Maustaste auf die   Entität Instructor, und wählen Sie **Einfügen** aus (oder drücken Sie STRG + V).</span><span class="sxs-lookup"><span data-stu-id="69e77-156">Right-click the **Instructor** entity and select **Paste** (or use the Ctrl-V key).</span></span>
-   <span data-ttu-id="69e77-157">Klicken Sie mit der rechten Maustaste auf die Eigenschaft **HireDate**,   und wählen Sie **Eigenschaften**.</span><span class="sxs-lookup"><span data-stu-id="69e77-157">Right-click the **HireDate** property and select **Properties**.</span></span>
-   <span data-ttu-id="69e77-158"> **Properties**   Legen Sie im Fenster Eigenschaften die Eigenschaft **Nullable**   auf **false**fest.</span><span class="sxs-lookup"><span data-stu-id="69e77-158">In the **Properties** window, set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="69e77-159">Klicken Sie mit der rechten Maustaste auf die Eigenschaft **registrimentdate**   der **Person**-   Entität.</span><span class="sxs-lookup"><span data-stu-id="69e77-159">Right-click the **EnrollmentDate** property of the **Person** entity.</span></span> <span data-ttu-id="69e77-160">Wählen Sie **Ausschneiden** (oder drücken Sie STRG + X).</span><span class="sxs-lookup"><span data-stu-id="69e77-160">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="69e77-161">Klicken Sie mit der rechten Maustaste auf die Entität **Student** , und wählen Sie **Einfügen aus (oder drücken Sie STRG + V).**</span><span class="sxs-lookup"><span data-stu-id="69e77-161">Right-click the **Student** entity and select **Paste(or use the Ctrl-V key).**</span></span>
-   <span data-ttu-id="69e77-162">Wählen Sie die Eigenschaft **registrimentdate**aus   , und legen Sie die Eigenschaft **Nullable**   auf **false**fest.</span><span class="sxs-lookup"><span data-stu-id="69e77-162">Select the **EnrollmentDate** property and set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="69e77-163">Wählen Sie **Person**den   Entitätstyp Person aus.</span><span class="sxs-lookup"><span data-stu-id="69e77-163">Select the **Person** entity type.</span></span> <span data-ttu-id="69e77-164"> **Properties**   Legen Sie im Fenster Eigenschaften die **abstrakte**   Eigenschaft auf **true**fest.</span><span class="sxs-lookup"><span data-stu-id="69e77-164">In the **Properties** window, set its **Abstract** property to **true**.</span></span>
-   <span data-ttu-id="69e77-165">Löschen Sie die **diskriminatoreigenschaft** von **Person**.</span><span class="sxs-lookup"><span data-stu-id="69e77-165">Delete the **Discriminator** property from **Person**.</span></span> <span data-ttu-id="69e77-166">Der Grund für das Löschen sollte im folgenden Abschnitt erläutert werden.</span><span class="sxs-lookup"><span data-stu-id="69e77-166">The reason it should be deleted is explained in the following section.</span></span>

### <a name="map-the-entities"></a><span data-ttu-id="69e77-167">Zuordnen der Entitäten</span><span class="sxs-lookup"><span data-stu-id="69e77-167">Map the entities</span></span>

-   <span data-ttu-id="69e77-168">Klicken Sie mit der rechten Maustaste auf den **Dozenten** und wählen Sie **Tabellen Zuordnung.**</span><span class="sxs-lookup"><span data-stu-id="69e77-168">Right-click the **Instructor** and select **Table Mapping.**</span></span>
    <span data-ttu-id="69e77-169">Die Entität "Instructor" ist im Fenster "Mappingdetails" ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="69e77-169">The Instructor entity is selected in the Mapping Details window.</span></span>
-   <span data-ttu-id="69e77-170">Klicken Sie im Fenster Mappingdetails auf \*\* &lt; Tabelle oder Sicht &gt; Hinzufügen\*\*    **Mapping Details**   .</span><span class="sxs-lookup"><span data-stu-id="69e77-170">Click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
    <span data-ttu-id="69e77-171">Das Feld \*\* &lt; Tabelle oder Sicht &gt; Hinzufügen\*\*   wird zu einer Dropdown Liste von Tabellen oder Sichten, denen die ausgewählte Entität zugeordnet werden kann.</span><span class="sxs-lookup"><span data-stu-id="69e77-171">The **&lt;Add a Table or View&gt;** field becomes a drop-down list of tables or views to which the selected entity can be mapped.</span></span>
-   <span data-ttu-id="69e77-172">Wählen **Person**Sie in   der Dropdown Liste Person aus.</span><span class="sxs-lookup"><span data-stu-id="69e77-172">Select **Person** from the drop-down list.</span></span>
-   <span data-ttu-id="69e77-173">Das Fenster **Mappingdetails**   wird mit Standard Spalten Zuordnungen und einer Option zum Hinzufügen einer Bedingung aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="69e77-173">The **Mapping Details** window is updated with default column mappings and an option for adding a condition.</span></span>
-   <span data-ttu-id="69e77-174">Klicken Sie auf \*\* &lt; Bedingung &gt; Hinzufügen\*\*.</span><span class="sxs-lookup"><span data-stu-id="69e77-174">Click on **&lt;Add a Condition&gt;**.</span></span>
    <span data-ttu-id="69e77-175">Das Feld \*\* &lt; Bedingung &gt; Hinzufügen\*\*   wird zu einer Dropdown Liste von Spalten, für die Bedingungen festgelegt werden können.</span><span class="sxs-lookup"><span data-stu-id="69e77-175">The **&lt;Add a Condition&gt;** field becomes a drop-down list of columns for which conditions can be set.</span></span>
-   <span data-ttu-id="69e77-176">Wählen Sie in der Dropdown Liste die Option **Diskriminator**   aus.</span><span class="sxs-lookup"><span data-stu-id="69e77-176">Select **Discriminator** from the drop-down list.</span></span>
-   <span data-ttu-id="69e77-177">Wählen Sie in der Spalte **Operator**   des Fensters **Mappingdetails**in   der Dropdown Liste die Option = aus.</span><span class="sxs-lookup"><span data-stu-id="69e77-177">In the **Operator** column of the **Mapping Details** window, select = from the drop-down list.</span></span>
-   <span data-ttu-id="69e77-178">Geben Sie in der Spalte **Wert/Eigenschaft den Wert**  **Instructor**ein.</span><span class="sxs-lookup"><span data-stu-id="69e77-178">In the **Value/Property** column, type **Instructor**.</span></span> <span data-ttu-id="69e77-179">Das Endergebnis sollte wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="69e77-179">The end result should look like this:</span></span>

    ![Mappingdetails](~/ef6/media/mappingdetails2.png)

-   <span data-ttu-id="69e77-181">Wiederholen Sie diese Schritte **Student**für den   Entitätstyp "Student", aber legen Sie den Wert für " **Student** " fest.</span><span class="sxs-lookup"><span data-stu-id="69e77-181">Repeat these steps for the **Student** entity type, but make the condition equal to **Student** value.</span></span>  
    <span data-ttu-id="69e77-182">*Der Grund, warum wir die **diskriminatoreigenschaft** entfernen wollten, liegt darin, dass Sie eine Tabellenspalte nicht mehrmals zuordnen können. Diese Spalte wird für die bedingte Zuordnung verwendet und kann daher nicht auch für die Eigenschaften Zuordnung verwendet werden. Die einzige Möglichkeit, die für beides verwendet werden kann, wenn eine Bedingung einen **is NULL**-   oder **is not NULL**-   Vergleich verwendet.*</span><span class="sxs-lookup"><span data-stu-id="69e77-182">*The reason we wanted to remove the **Discriminator** property, is because you cannot map a table column more than once. This column will be used for conditional mapping, so it cannot be used for property mapping as well. The only way it can be used for both, if a condition uses an **Is Null** or **Is Not Null** comparison.*</span></span>

<span data-ttu-id="69e77-183">Die "Tabelle pro Hierarchie"-Vererbung ist jetzt implementiert.</span><span class="sxs-lookup"><span data-stu-id="69e77-183">Table-per-hierarchy inheritance is now implemented.</span></span>

![Endgültige TPH](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a><span data-ttu-id="69e77-185">Verwenden des Modells</span><span class="sxs-lookup"><span data-stu-id="69e77-185">Use the Model</span></span>

<span data-ttu-id="69e77-186">Öffnen Sie die Datei **Program.cs** , in der die **Main** -Methode definiert ist.</span><span class="sxs-lookup"><span data-stu-id="69e77-186">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="69e77-187">Fügen Sie den folgenden Code in die **Main** -Funktion ein.</span><span class="sxs-lookup"><span data-stu-id="69e77-187">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="69e77-188">Der Code führt drei Abfragen aus.</span><span class="sxs-lookup"><span data-stu-id="69e77-188">The code executes three queries.</span></span> <span data-ttu-id="69e77-189">Die erste Abfrage führt alle **Person** -Objekte zurück.</span><span class="sxs-lookup"><span data-stu-id="69e77-189">The first query brings back all **Person** objects.</span></span> <span data-ttu-id="69e77-190">Die zweite Abfrage verwendet die **OfType** -Methode, um **Instructor** -Objekte zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="69e77-190">The second query uses the **OfType** method to return **Instructor** objects.</span></span> <span data-ttu-id="69e77-191">Die dritte Abfrage verwendet die **OfType** -Methode, um **Student** -Objekte zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="69e77-191">The third query uses the **OfType** method to return **Student** objects.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        Console.WriteLine("All people:");
        foreach (var person in context.People)
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Instructors only: ");
        foreach (var person in context.People.OfType<Instructor>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Students only: ");
        foreach (var person in context.People.OfType<Student>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }
    }
```
