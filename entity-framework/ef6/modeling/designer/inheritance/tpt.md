---
title: Designer-TPT-Vererbung-EF6
description: Designer-TPT-Vererbung in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tpt
ms.openlocfilehash: f8e4198278fb44309916d57b44fb9af41d572108
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066173"
---
# <a name="designer-tpt-inheritance"></a><span data-ttu-id="e5aec-103">Designer-TPT-Vererbung</span><span class="sxs-lookup"><span data-stu-id="e5aec-103">Designer TPT Inheritance</span></span>
<span data-ttu-id="e5aec-104">In dieser schrittweisen exemplarischen Vorgehensweise wird veranschaulicht, wie Sie die "Tabelle pro Typ"-Vererbung (TPT) in Ihrem Modell mithilfe der Entity Framework Designer (EF-Designer) implementieren.</span><span class="sxs-lookup"><span data-stu-id="e5aec-104">This step-by-step walkthrough shows how to implement table-per-type (TPT) inheritance in your model using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="e5aec-105">Bei der "Tabelle pro Typ"-Vererbung wird eine separate Tabelle in der Datenbank verwendet, um die Daten für nicht geerbte Eigenschaften und Schlüsseleigenschaften für jeden Typ in der Vererbungshierarchie zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="e5aec-105">Table-per-type inheritance uses a separate table in the database to maintain data for non-inherited properties and key properties for each type in the inheritance hierarchy.</span></span>

<span data-ttu-id="e5aec-106">In dieser exemplarischen Vorgehensweise ordnen wir die Entitäten **Course** (Basistyp), **OnlineCourse** (abgeleitet von Course) und **OnsiteCourse**   (abgeleitet von **Course**) auf Tabellen mit denselben Namen zu.</span><span class="sxs-lookup"><span data-stu-id="e5aec-106">In this walkthrough we will map the **Course** (base type), **OnlineCourse** (derives from Course), and **OnsiteCourse** (derives from **Course**) entities to tables with the same names.</span></span> <span data-ttu-id="e5aec-107">Wir erstellen ein Modell aus der Datenbank und ändern dann das Modell, um die TPT-Vererbung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="e5aec-107">We'll create a model from the database and then alter the model to implement the TPT inheritance.</span></span>

<span data-ttu-id="e5aec-108">Sie können auch mit dem Model First beginnen und dann die Datenbank aus dem Modell generieren.</span><span class="sxs-lookup"><span data-stu-id="e5aec-108">You can also start with the Model First and then generate the database from the model.</span></span> <span data-ttu-id="e5aec-109">Der EF-Designer verwendet standardmäßig die TPT-Strategie, sodass jede Vererbung im Modell separaten Tabellen zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="e5aec-109">The EF Designer uses the TPT strategy by default and so any inheritance in the model will be mapped to separate tables.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="e5aec-110">Andere Vererbungs Optionen</span><span class="sxs-lookup"><span data-stu-id="e5aec-110">Other Inheritance Options</span></span>

<span data-ttu-id="e5aec-111">Tabelle pro Hierarchie (TPH) ist eine andere Art von Vererbung, bei der eine Datenbanktabelle verwendet wird, um Daten für alle Entitäts Typen in einer Vererbungs Hierarchie zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="e5aec-111">Table-per-Hierarchy (TPH) is another type of inheritance in which one database table is used to maintain data for all of the entity types in an inheritance hierarchy.</span></span><span data-ttu-id="e5aec-112">Informationen zum Zuordnen der "Tabelle pro Hierarchie"-Vererbung mit dem Entity Designer finden Sie unter [EF-Designer-TPH-Vererbung](xref:ef6/modeling/designer/inheritance/tph).</span><span class="sxs-lookup"><span data-stu-id="e5aec-112">  For information about how to map Table-per-Hierarchy inheritance with the Entity Designer, see [EF Designer TPH Inheritance](xref:ef6/modeling/designer/inheritance/tph).</span></span> 

<span data-ttu-id="e5aec-113">Beachten Sie, dass die Tabelle pro konkrete Typvererbung (TPC) und gemischte Vererbungs Modelle von der Entity Framework Runtime unterstützt werden, jedoch nicht vom EF-Designer unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="e5aec-113">Note that, the Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="e5aec-114">Wenn Sie TPC oder gemischte Vererbung verwenden möchten, haben Sie zwei Möglichkeiten: Verwenden Sie Code First, oder bearbeiten Sie die EDMX-Datei manuell.</span><span class="sxs-lookup"><span data-stu-id="e5aec-114">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="e5aec-115">Wenn Sie sich für die Arbeit mit der EDMX-Datei entscheiden, wird das Fenster Mappingdetails in den "abgesicherten Modus" versetzt, und Sie können den Designer nicht verwenden, um die Zuordnungen zu ändern.</span><span class="sxs-lookup"><span data-stu-id="e5aec-115">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e5aec-116">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="e5aec-116">Prerequisites</span></span>

<span data-ttu-id="e5aec-117">Um die exemplarische Vorgehensweise nachzuvollziehen, benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e5aec-117">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="e5aec-118">Eine aktuelle Version von Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e5aec-118">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="e5aec-119">Die [Beispieldatenbank "School](xref:ef6/resources/school-database)".</span><span class="sxs-lookup"><span data-stu-id="e5aec-119">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="e5aec-120">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="e5aec-120">Set up the Project</span></span>

-   <span data-ttu-id="e5aec-121">Öffnen Sie Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="e5aec-121">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="e5aec-122">Wählen Sie **Datei- &gt; neu- &gt; Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="e5aec-122">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="e5aec-123">Klicken Sie im linken Bereich auf \*\*Visual C \# \*\*, und wählen Sie dann die **Konsolen** Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="e5aec-123">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="e5aec-124">Geben Sie **tptdbfirstsample**   als Name ein.</span><span class="sxs-lookup"><span data-stu-id="e5aec-124">Enter **TPTDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="e5aec-125">Wählen Sie **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="e5aec-125">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="e5aec-126">Modellerstellung</span><span class="sxs-lookup"><span data-stu-id="e5aec-126">Create a Model</span></span>

-   <span data-ttu-id="e5aec-127">Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Add- &gt; New Item**aus.</span><span class="sxs-lookup"><span data-stu-id="e5aec-127">Right-click the project in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="e5aec-128">Wählen Sie im linken Menü **Daten** aus, und wählen Sie dann im Bereich Vorlagen die Option **ADO.NET Entity Data Model** aus.</span><span class="sxs-lookup"><span data-stu-id="e5aec-128">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="e5aec-129">Geben Sie als Dateiname **tptmodel. edmx** ein, und klicken Sie dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="e5aec-129">Enter **TPTModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="e5aec-130">Wählen Sie im Dialogfeld Modell Inhalte auswählen die Option\*\*   aus Datenbank generieren aus**, und klicken Sie dann auf **weiter\*\*.</span><span class="sxs-lookup"><span data-stu-id="e5aec-130">In the Choose Model Contents dialog box, select** Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="e5aec-131">Klicken Sie auf **neue Verbindung**.</span><span class="sxs-lookup"><span data-stu-id="e5aec-131">Click **New Connection**.</span></span>
    <span data-ttu-id="e5aec-132">Geben Sie im Dialogfeld Verbindungs Eigenschaften den Servernamen ein (z. b. **(localdb) \\ mssqllocaldb**), wählen Sie die Authentifizierungsmethode aus, geben Sie **School**   als Datenbanknamen School ein, und klicken Sie dann auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="e5aec-132">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="e5aec-133">Das Dialogfeld Wählen Sie Ihre Datenverbindung aus wird mit Ihrer Daten bankverbindungs Einstellung aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="e5aec-133">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="e5aec-134">Wählen Sie im Dialogfeld Datenbankobjekte auswählen unter dem Knoten Tabellen die Tabellen " **Department**", " **Course", "OnlineCourse" und "OnsiteCourse** " aus.</span><span class="sxs-lookup"><span data-stu-id="e5aec-134">In the Choose Your Database Objects dialog box, under the Tables node, select the **Department**, **Course, OnlineCourse, and OnsiteCourse** tables.</span></span>
-   <span data-ttu-id="e5aec-135">Klicken Sie auf **Finish**.</span><span class="sxs-lookup"><span data-stu-id="e5aec-135">Click **Finish**.</span></span>

<span data-ttu-id="e5aec-136">Die Entity Designer, die eine Entwurfs Oberfläche zum Bearbeiten des Modells bereitstellt, wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="e5aec-136">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="e5aec-137">Alle Objekte, die Sie im Dialogfeld Wählen Sie Ihre Datenbankobjekte ausgewählt haben, werden dem Modell hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e5aec-137">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

## <a name="implement-table-per-type-inheritance"></a><span data-ttu-id="e5aec-138">"Tabelle pro Typ"-Vererbung implementieren</span><span class="sxs-lookup"><span data-stu-id="e5aec-138">Implement Table-per-Type Inheritance</span></span>

-   <span data-ttu-id="e5aec-139">Klicken Sie auf der Entwurfs Oberfläche mit der rechten Maustaste auf den Entitätstyp **OnlineCourse** , und wählen Sie **Eigenschaften**aus.</span><span class="sxs-lookup"><span data-stu-id="e5aec-139">On the design surface, right-click the **OnlineCourse** entity type and select **Properties**.</span></span>
-   <span data-ttu-id="e5aec-140">Legen Sie im Fenster **Eigenschaften** die Eigenschaft Basistyp auf **Kurs**fest.</span><span class="sxs-lookup"><span data-stu-id="e5aec-140">In the **Properties** window, set the Base Type property to **Course**.</span></span>
-   <span data-ttu-id="e5aec-141">Klicken Sie mit der rechten Maustaste auf den Entitätstyp **OnsiteCourse** und wählen Sie **Eigenschaften**.</span><span class="sxs-lookup"><span data-stu-id="e5aec-141">Right-click the **OnsiteCourse** entity type and select **Properties**.</span></span>
-   <span data-ttu-id="e5aec-142">Legen Sie im Fenster **Eigenschaften** die Eigenschaft Basistyp auf **Kurs**fest.</span><span class="sxs-lookup"><span data-stu-id="e5aec-142">In the **Properties** window, set the Base Type property to **Course**.</span></span>
-   <span data-ttu-id="e5aec-143">Klicken Sie mit der rechten Maustaste auf die Zuordnung (die Linie) zwischen den Entitäts Typen **OnlineCourse** und **Course** .</span><span class="sxs-lookup"><span data-stu-id="e5aec-143">Right-click the association (the line) between the **OnlineCourse** and **Course** entity types.</span></span>
    <span data-ttu-id="e5aec-144">Wählen Sie **aus Modell löschen aus**.</span><span class="sxs-lookup"><span data-stu-id="e5aec-144">Select **Delete from Model**.</span></span>
-   <span data-ttu-id="e5aec-145">Klicken Sie mit der rechten Maustaste auf die Zuordnung zwischen den Entitäts Typen **OnsiteCourse** und **Course** .</span><span class="sxs-lookup"><span data-stu-id="e5aec-145">Right-click the association between the **OnsiteCourse** and **Course** entity types.</span></span>
    <span data-ttu-id="e5aec-146">Wählen Sie **aus Modell löschen aus**.</span><span class="sxs-lookup"><span data-stu-id="e5aec-146">Select **Delete from Model**.</span></span>

<span data-ttu-id="e5aec-147">Wir löschen jetzt die **CourseID** -Eigenschaft aus " **OnlineCourse** " und " **OnsiteCourse** ", da diese Klassen **CourseID** vom **Course** -Basistyp erben.</span><span class="sxs-lookup"><span data-stu-id="e5aec-147">We will now delete the **CourseID** property from **OnlineCourse** and **OnsiteCourse** because these classes inherit **CourseID** from the **Course** base type.</span></span>

-   <span data-ttu-id="e5aec-148">Klicken Sie mit der rechten Maustaste auf die Eigenschaft **CourseID** des Entitäts Typs **OnlineCourse** , und wählen Sie dann **aus Modell löschen aus**.</span><span class="sxs-lookup"><span data-stu-id="e5aec-148">Right-click the **CourseID** property of the **OnlineCourse** entity type, and then select **Delete from Model**.</span></span>
-   <span data-ttu-id="e5aec-149">Klicken Sie mit der rechten Maustaste auf die Eigenschaft **CourseID** des Entitäts Typs **OnsiteCourse** , und wählen Sie dann **aus Modell löschen aus** .</span><span class="sxs-lookup"><span data-stu-id="e5aec-149">Right-click the **CourseID** property of the **OnsiteCourse** entity type, and then select **Delete from Model**</span></span>
-   <span data-ttu-id="e5aec-150">Die "Tabelle pro Typ"-Vererbung ist damit implementiert.</span><span class="sxs-lookup"><span data-stu-id="e5aec-150">Table-per-type inheritance is now implemented.</span></span>

![Tabelle pro Typ](~/ef6/media/tpt.png)

## <a name="use-the-model"></a><span data-ttu-id="e5aec-152">Verwenden des Modells</span><span class="sxs-lookup"><span data-stu-id="e5aec-152">Use the Model</span></span>

<span data-ttu-id="e5aec-153">Öffnen Sie die Datei **Program.cs** , in der die **Main** -Methode definiert ist.</span><span class="sxs-lookup"><span data-stu-id="e5aec-153">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="e5aec-154">Fügen Sie den folgenden Code in die **Main** -Funktion ein.</span><span class="sxs-lookup"><span data-stu-id="e5aec-154">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="e5aec-155">Der Code führt drei Abfragen aus.</span><span class="sxs-lookup"><span data-stu-id="e5aec-155">The code executes three queries.</span></span> <span data-ttu-id="e5aec-156">Die erste Abfrage führt alle **Kurse** zurück, die mit der angegebenen Abteilung verknüpft sind.</span><span class="sxs-lookup"><span data-stu-id="e5aec-156">The first query brings back all **Courses** related to the specified department.</span></span> <span data-ttu-id="e5aec-157">In der zweiten Abfrage wird die **OfType** -Methode verwendet, um **OnlineCourses** zurückzugeben, die mit der angegebenen Abteilung verknüpft sind.</span><span class="sxs-lookup"><span data-stu-id="e5aec-157">The second query uses the **OfType** method to return **OnlineCourses** related to the specified department.</span></span> <span data-ttu-id="e5aec-158">Die dritte Abfrage gibt " **OnsiteCourses**" zurück.</span><span class="sxs-lookup"><span data-stu-id="e5aec-158">The third query returns **OnsiteCourses**.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        foreach (var department in context.Departments)
        {
            Console.WriteLine("The {0} department has the following courses:",
                               department.Name);

            Console.WriteLine("   All courses");
            foreach (var course in department.Courses )
            {
                Console.WriteLine("     {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnlineCourse>())
            {
                Console.WriteLine("   Online - {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnsiteCourse>())
            {
                Console.WriteLine("   Onsite - {0}", course.Title);
            }
        }
    }
```
