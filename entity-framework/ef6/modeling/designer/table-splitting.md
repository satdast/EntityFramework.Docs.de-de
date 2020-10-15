---
title: Aufteilung der Designer Tabelle-EF6
description: Aufteilen von Designer Tabellen in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/table-splitting
ms.openlocfilehash: 8f6db22913fb3ac8a4254c29b5f986eb6e14f18e
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066043"
---
# <a name="designer-table-splitting"></a><span data-ttu-id="61416-103">Aufteilen von Designer Tabellen</span><span class="sxs-lookup"><span data-stu-id="61416-103">Designer Table Splitting</span></span>
<span data-ttu-id="61416-104">In dieser exemplarischen Vorgehensweise wird veranschaulicht, wie Sie mehrere Entitäts Typen einer einzelnen Tabelle zuordnen, indem Sie ein Modell mit dem Entity Framework Designer (EF-Designer) ändern.</span><span class="sxs-lookup"><span data-stu-id="61416-104">This walkthrough shows how to map multiple entity types to a single table by modifying a model with the Entity Framework Designer (EF Designer).</span></span>

<span data-ttu-id="61416-105">Ein Grund, warum Sie die Tabellen Aufteilung verwenden möchten, verzögert das Laden einiger Eigenschaften, wenn Lazy Loading zum Laden der Objekte verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="61416-105">One reason you may want to use table splitting is delaying the loading of some properties when using lazy loading to load your objects.</span></span><span data-ttu-id="61416-106">Sie können die Eigenschaften, die möglicherweise sehr große Datenmengen enthalten, in eine separate Entität aufteilen und Sie nur bei Bedarf laden.</span><span class="sxs-lookup"><span data-stu-id="61416-106"> You can separate the properties that might contain very large amount of data into a separate entity and only load it when required.</span></span>

<span data-ttu-id="61416-107">Die folgende Abbildung zeigt die Hauptfenster, die bei der Arbeit mit dem EF-Designer verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="61416-107">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF-Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a><span data-ttu-id="61416-109">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="61416-109">Prerequisites</span></span>

<span data-ttu-id="61416-110">Um die exemplarische Vorgehensweise nachzuvollziehen, benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="61416-110">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="61416-111">Eine aktuelle Version von Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="61416-111">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="61416-112">Die [Beispieldatenbank "School](xref:ef6/resources/school-database)".</span><span class="sxs-lookup"><span data-stu-id="61416-112">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="61416-113">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="61416-113">Set up the Project</span></span>

<span data-ttu-id="61416-114">In dieser exemplarischen Vorgehensweise wird Visual Studio 2012 verwendet.</span><span class="sxs-lookup"><span data-stu-id="61416-114">This walkthrough is using Visual Studio 2012.</span></span>

-   <span data-ttu-id="61416-115">Öffnen Sie Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="61416-115">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="61416-116">Zeigen Sie im Menü **Datei** auf **Neu**, und klicken Sie dann auf **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="61416-116">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="61416-117">Klicken Sie im linken Bereich auf Visual C \# , und wählen Sie dann die Vorlage Konsolenanwendung aus.</span><span class="sxs-lookup"><span data-stu-id="61416-117">In the left pane, click Visual C\#, and then select the Console Application template.</span></span>
-   <span data-ttu-id="61416-118">Geben Sie **tablesplittingsample** als Namen für das Projekt ein, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="61416-118">Enter **TableSplittingSample** as the name of the project and click **OK**.</span></span>

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="61416-119">Erstellen eines Modells auf der Grundlage der Datenbank "School"</span><span class="sxs-lookup"><span data-stu-id="61416-119">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="61416-120">Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen, zeigen Sie auf **Hinzufügen**, und klicken Sie dann auf **Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="61416-120">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="61416-121">Wählen Sie im linken Menü **Daten** aus, und wählen Sie dann im Bereich Vorlagen die Option **ADO.NET Entity Data Model** aus.</span><span class="sxs-lookup"><span data-stu-id="61416-121">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="61416-122">Geben Sie als Dateiname **tablesplittingmodel. edmx** ein, und klicken Sie dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="61416-122">Enter **TableSplittingModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="61416-123">Wählen Sie im Dialogfeld Modell Inhalte auswählen die Option **aus Datenbank generieren aus**, und klicken Sie dann auf **Weiter.**</span><span class="sxs-lookup"><span data-stu-id="61416-123">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next.**</span></span>
-   <span data-ttu-id="61416-124">Klicken Sie auf neue Verbindung.</span><span class="sxs-lookup"><span data-stu-id="61416-124">Click New Connection.</span></span> <span data-ttu-id="61416-125">Geben Sie im Dialogfeld Verbindungs Eigenschaften den Servernamen ein (z. b. **(localdb) \\ mssqllocaldb**), wählen Sie die Authentifizierungsmethode aus, geben Sie **School**   als Datenbanknamen School ein, und klicken Sie dann auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="61416-125">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="61416-126">Das Dialogfeld Wählen Sie Ihre Datenverbindung aus wird mit Ihrer Daten bankverbindungs Einstellung aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="61416-126">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="61416-127">Erweitern Sie im Dialogfeld Wählen Sie Ihre Datenbankobjekte aus den Knoten **Tabellen**,   und überprüfen Sie die Tabelle **Person** .</span><span class="sxs-lookup"><span data-stu-id="61416-127">In the Choose Your Database Objects dialog box, unfold the **Tables** node and check the **Person** table.</span></span> <span data-ttu-id="61416-128">Dadurch wird die angegebene Tabelle dem Modell " **School** " hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="61416-128">This will add the specified table to the **School** model.</span></span>
-   <span data-ttu-id="61416-129">Klicken Sie auf **Finish**.</span><span class="sxs-lookup"><span data-stu-id="61416-129">Click **Finish**.</span></span>

<span data-ttu-id="61416-130">Die Entity Designer, die eine Entwurfs Oberfläche zum Bearbeiten des Modells bereitstellt, wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="61416-130">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="61416-131">Alle Objekte, die Sie im Dialogfeld **Wählen Sie Ihre Datenbankobjekte**ausgewählt   haben, werden dem Modell hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="61416-131">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>

## <a name="map-two-entities-to-a-single-table"></a><span data-ttu-id="61416-132">Ordnen Sie zwei Entitäten einer einzelnen Tabelle zu.</span><span class="sxs-lookup"><span data-stu-id="61416-132">Map Two Entities to a Single Table</span></span>

<span data-ttu-id="61416-133">In diesem Abschnitt teilen Sie die Entität **Person** in zwei Entitäten auf und ordnen Sie dann einer einzelnen Tabelle zu.</span><span class="sxs-lookup"><span data-stu-id="61416-133">In this section you will split the **Person** entity into two entities and then map them to a single table.</span></span>

> [!NOTE]
> <span data-ttu-id="61416-134">Die **Person** -Entität enthält keine Eigenschaften, die möglicherweise eine große Datenmenge enthalten. Sie wird nur als Beispiel verwendet.</span><span class="sxs-lookup"><span data-stu-id="61416-134">The **Person** entity does not contain any properties that may contain large amount of data; it is just used as an example.</span></span>

-   <span data-ttu-id="61416-135">Klicken Sie mit der rechten Maustaste auf einen leeren Bereich der Entwurfs Oberfläche, zeigen Sie auf **Neu hinzufügen**, und klicken Sie auf **Entität**.</span><span class="sxs-lookup"><span data-stu-id="61416-135">Right-click an empty area of the design surface, point to **Add New**, and click **Entity**.</span></span>
    <span data-ttu-id="61416-136">Das Dialogfeld **neue Entität**wird   angezeigt.</span><span class="sxs-lookup"><span data-stu-id="61416-136">The **New Entity** dialog box appears.</span></span>
-   <span data-ttu-id="61416-137">Geben **Sie**   als **Entitäts Name** und **PersonID** als Name der **Schlüsseleigenschaft** hireinfo ein.</span><span class="sxs-lookup"><span data-stu-id="61416-137">Type **HireInfo** for the **Entity name** and **PersonID** for the **Key Property** name.</span></span>
-   <span data-ttu-id="61416-138">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="61416-138">Click **OK**.</span></span>
-   <span data-ttu-id="61416-139">Ein neuer Entitätstyp wird erstellt und auf der Entwurfsoberfläche angezeigt.</span><span class="sxs-lookup"><span data-stu-id="61416-139">A new entity type is created and displayed on the design surface.</span></span>
-   <span data-ttu-id="61416-140">Wählen Sie die **HireDate**   -Eigenschaft des Entitäts Typs **Person**aus,   und drücken Sie **STRG + X** -Taste.</span><span class="sxs-lookup"><span data-stu-id="61416-140">Select the **HireDate** property of the **Person** entity type and press **Ctrl+X** keys.</span></span>
-   <span data-ttu-id="61416-141">Wählen Sie die **hireinfo**   -Entität aus, und drücken Sie **STRG + V** Tasten.</span><span class="sxs-lookup"><span data-stu-id="61416-141">Select the **HireInfo** entity and press **Ctrl+V** keys.</span></span>
-   <span data-ttu-id="61416-142">Erstellen Sie eine Zuordnung zwischen **Person** und **hireinfo**.</span><span class="sxs-lookup"><span data-stu-id="61416-142">Create an association between **Person** and **HireInfo**.</span></span> <span data-ttu-id="61416-143">Klicken Sie hierzu mit der rechten Maustaste auf einen leeren Bereich der Entwurfs Oberfläche, zeigen Sie auf **Neu hinzufügen**, **und klicken Sie dann auf Zuordnung**.</span><span class="sxs-lookup"><span data-stu-id="61416-143">To do this, right-click an empty area of the design surface, point to **Add New**, and click **Association**.</span></span>
-   <span data-ttu-id="61416-144">Das Dialogfeld Zuordnung **Hinzufügen**wird   angezeigt.</span><span class="sxs-lookup"><span data-stu-id="61416-144">The **Add Association** dialog box appears.</span></span> <span data-ttu-id="61416-145">Der **personhireinfo** -Name wird standardmäßig angegeben.</span><span class="sxs-lookup"><span data-stu-id="61416-145">The **PersonHireInfo** name is given by default.</span></span>
-   <span data-ttu-id="61416-146">Geben Sie die Multiplizität **1 (eins)** an beiden Enden der Beziehung an.</span><span class="sxs-lookup"><span data-stu-id="61416-146">Specify multiplicity **1(One)** on both ends of the relationship.</span></span>
-   <span data-ttu-id="61416-147">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="61416-147">Press **OK**.</span></span>

<span data-ttu-id="61416-148">Der nächste Schritt erfordert das Fenster " **Mappingdetails**"   .</span><span class="sxs-lookup"><span data-stu-id="61416-148">The next step requires the **Mapping Details** window.</span></span> <span data-ttu-id="61416-149">Wenn dieses Fenster nicht angezeigt wird, klicken Sie mit der rechten Maustaste auf die Entwurfs Oberfläche, und wählen Sie **Mappingdetails**.</span><span class="sxs-lookup"><span data-stu-id="61416-149">If you cannot see this window, right-click the design surface and select **Mapping Details**.</span></span>

-   <span data-ttu-id="61416-150">Wählen Sie den **hireinfo**   -Entitätstyp aus, und klicken Sie im Fenster Mappingdetails auf \*\* &lt; Tabelle oder Sicht &gt; Hinzufügen\*\*    **Mapping Details**   .</span><span class="sxs-lookup"><span data-stu-id="61416-150">Select the **HireInfo** entity type and click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="61416-151">Wählen Sie in der Dropdown Liste \*\* &lt; Tabelle oder &gt; Sicht hinzufügen\*\*die Option **Person** aus   .</span><span class="sxs-lookup"><span data-stu-id="61416-151">Select **Person** from the **&lt;Add a Table or View&gt;** field drop-down list.</span></span> <span data-ttu-id="61416-152">Die Liste enthält Tabellen oder Sichten, denen die ausgewählte Entität zugeordnet werden kann.</span><span class="sxs-lookup"><span data-stu-id="61416-152">The list contains tables or views to which the selected entity can be mapped.</span></span>
    <span data-ttu-id="61416-153">Die entsprechenden Eigenschaften sollten standardmäßig zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="61416-153">The appropriate properties should be mapped by default.</span></span>

    ![Mapping-Eigenschaften](~/ef6/media/mapping.png)

-   <span data-ttu-id="61416-155">Wählen Sie auf der Entwurfs Oberfläche die Zuordnung **personhireinfo** aus.</span><span class="sxs-lookup"><span data-stu-id="61416-155">Select the **PersonHireInfo** association on the design surface.</span></span>
-   <span data-ttu-id="61416-156">Klicken Sie mit der rechten Maustaste auf die Zuordnung der Entwurfs Oberfläche, und wählen Sie **Eigenschaften**aus.</span><span class="sxs-lookup"><span data-stu-id="61416-156">Right-click the association on the design surface and select **Properties**.</span></span>
-   <span data-ttu-id="61416-157">Wählen Sie im Fenster **Eigenschaften** die Eigenschaft **Referenzielle Einschränkungen** aus, und klicken Sie auf die Schaltfläche mit den Auslassungs Punkten.</span><span class="sxs-lookup"><span data-stu-id="61416-157">In the **Properties** window, select the **Referential Constraints** property and click the ellipses button.</span></span>
-   <span data-ttu-id="61416-158">Wählen Sie in der Dropdown Liste **Prinzipal** die Option **Person** aus.</span><span class="sxs-lookup"><span data-stu-id="61416-158">Select **Person** from the **Principal** drop-down list.</span></span>
-   <span data-ttu-id="61416-159">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="61416-159">Press **OK**.</span></span>

 

## <a name="use-the-model"></a><span data-ttu-id="61416-160">Verwenden des Modells</span><span class="sxs-lookup"><span data-stu-id="61416-160">Use the Model</span></span>

-   <span data-ttu-id="61416-161">Fügen Sie den folgenden Code in die Main-Methode ein.</span><span class="sxs-lookup"><span data-stu-id="61416-161">Paste the following code in the Main method.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        Person person = new Person()
        {
            FirstName = "Kimberly",
            LastName = "Morgan",
            Discriminator = "Instructor",
        };

        person.HireInfo = new HireInfo()
        {
            HireDate = DateTime.Now
        };

        // Add the new person to the context.
        context.People.Add(person);

        // Insert a row into the Person table.  
        context.SaveChanges();

        // Execute a query against the Person table.
        // The query returns columns that map to the Person entity.
        var existingPerson = context.People.FirstOrDefault();

        // Execute a query against the Person table.
        // The query returns columns that map to the Instructor entity.
        var hireInfo = existingPerson.HireInfo;

        Console.WriteLine("{0} was hired on {1}",
            existingPerson.LastName, hireInfo.HireDate);
    }
```
-   <span data-ttu-id="61416-162">Kompilieren Sie die Anwendung, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="61416-162">Compile and run the application.</span></span>

<span data-ttu-id="61416-163">Die folgenden T-SQL-Anweisungen wurden als Ergebnis der Ausführung dieser Anwendung für die Datenbank " **School** " ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="61416-163">The following T-SQL statements were executed against the **School** database as a result of running this application.</span></span> 

-   <span data-ttu-id="61416-164">Der folgende **Insert-Vorgang** wurde als Ergebnis der Ausführung des Kontexts ausgeführt. SaveChanges () und kombiniert Daten aus den Entitäten **Person** und **hireinfo** .</span><span class="sxs-lookup"><span data-stu-id="61416-164">The following **INSERT** was executed as a result of executing context.SaveChanges() and combines data from the **Person** and **HireInfo** entities</span></span>

    ![Einfügen von Person-und hireinfo-Daten](~/ef6/media/insert.png)

-   <span data-ttu-id="61416-166">Die folgende **Select** -Anweisung wurde als Ergebnis der Ausführung des Kontexts ausgeführt. People. FirstOrDefault () und wählt nur die Spalten aus, die **Person** zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="61416-166">The following **SELECT** was executed as a result of executing context.People.FirstOrDefault() and selects just the columns mapped to **Person**</span></span>

    ![Wählen Sie 1 aus.](~/ef6/media/select1.png)

-   <span data-ttu-id="61416-168">Die folgende **Select** -Anweisung wurde als Ergebnis des Zugriffs auf die Navigations Eigenschaft existingperson. Instructor ausgeführt und wählt nur die Spalten aus, die **hireinfo** zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="61416-168">The following **SELECT** was executed as a result of accessing the navigation property existingPerson.Instructor and selects just the columns mapped to **HireInfo**</span></span>

    ![2 auswählen](~/ef6/media/select2.png)
