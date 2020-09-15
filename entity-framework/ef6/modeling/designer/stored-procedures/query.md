---
title: Gespeicherte Prozeduren für Designer Abfragen EF6
description: Gespeicherte Prozeduren für Designer Abfragen in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/stored-procedures/query
ms.openlocfilehash: 4976c7d4f788e6e9524055a508f9c906b47c9ebb
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073200"
---
# <a name="designer-query-stored-procedures"></a><span data-ttu-id="cba93-103">Gespeicherte Prozeduren für Designer Abfragen</span><span class="sxs-lookup"><span data-stu-id="cba93-103">Designer Query Stored Procedures</span></span>
<span data-ttu-id="cba93-104">In dieser schrittweisen exemplarischen Vorgehensweise wird veranschaulicht, wie Sie mit dem Entity Framework Designer (EF-Designer) gespeicherte Prozeduren in ein Modell importieren und dann die importierten gespeicherten Prozeduren aufrufen, um Ergebnisse abzurufen.</span><span class="sxs-lookup"><span data-stu-id="cba93-104">This step-by-step walkthrough show how to use the Entity Framework Designer (EF Designer) to import stored procedures into a model and then call the imported stored procedures to retrieve results.</span></span> 

<span data-ttu-id="cba93-105">Beachten Sie, dass Code First keine Zuordnung zu gespeicherten Prozeduren oder Funktionen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="cba93-105">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="cba93-106">Allerdings können Sie gespeicherte Prozeduren oder Funktionen mithilfe der System. Data. Entity. dbset. sqlQuery-Methode abrufen.</span><span class="sxs-lookup"><span data-stu-id="cba93-106">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="cba93-107">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cba93-107">For example:</span></span>
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a><span data-ttu-id="cba93-108">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="cba93-108">Prerequisites</span></span>

<span data-ttu-id="cba93-109">Um die exemplarische Vorgehensweise nachzuvollziehen, benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="cba93-109">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="cba93-110">Eine aktuelle Version von Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cba93-110">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="cba93-111">Die [Beispieldatenbank "School](xref:ef6/resources/school-database)".</span><span class="sxs-lookup"><span data-stu-id="cba93-111">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="cba93-112">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="cba93-112">Set up the Project</span></span>

-   <span data-ttu-id="cba93-113">Öffnen Sie Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="cba93-113">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="cba93-114">Wählen Sie **Datei- &gt; neu- &gt; Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="cba93-114">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="cba93-115">Klicken Sie im linken Bereich auf \*\*Visual C \# \*\*, und wählen Sie dann die **Konsolen** Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="cba93-115">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="cba93-116">Geben Sie **efwithsprocssample**   als Name ein.</span><span class="sxs-lookup"><span data-stu-id="cba93-116">Enter **EFwithSProcsSample** as the name.</span></span>
-   <span data-ttu-id="cba93-117">Wählen Sie **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="cba93-117">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="cba93-118">Modellerstellung</span><span class="sxs-lookup"><span data-stu-id="cba93-118">Create a Model</span></span>

-   <span data-ttu-id="cba93-119">Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Add- &gt; New Item**.</span><span class="sxs-lookup"><span data-stu-id="cba93-119">Right-click the project in Solution Explorer and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="cba93-120">Wählen Sie im linken Menü **Daten** aus, und wählen Sie dann im Bereich Vorlagen die Option **ADO.NET Entity Data Model** aus.</span><span class="sxs-lookup"><span data-stu-id="cba93-120">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="cba93-121">Geben Sie als Dateiname **efwithsprocsmodel. edmx** ein, und klicken Sie dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="cba93-121">Enter **EFwithSProcsModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="cba93-122">Wählen Sie im Dialogfeld Modell Inhalte auswählen die Option **aus Datenbank generieren aus**, und klicken Sie dann auf **weiter**.</span><span class="sxs-lookup"><span data-stu-id="cba93-122">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="cba93-123">Klicken Sie auf **neue Verbindung**.</span><span class="sxs-lookup"><span data-stu-id="cba93-123">Click **New Connection**.</span></span>  
    <span data-ttu-id="cba93-124">Geben Sie im Dialogfeld Verbindungs Eigenschaften den Servernamen ein (z. b. **(localdb) \\ mssqllocaldb**), wählen Sie die Authentifizierungsmethode aus, geben Sie **School**   als Datenbanknamen School ein, und klicken Sie dann auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="cba93-124">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>  
    <span data-ttu-id="cba93-125">Das Dialogfeld Wählen Sie Ihre Datenverbindung aus wird mit Ihrer Daten bankverbindungs Einstellung aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="cba93-125">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="cba93-126">Aktivieren Sie im Dialogfeld Wählen Sie Ihre Datenbankobjekte aus das Kontrollkästchen **Tabellen**,   um alle Tabellen auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="cba93-126">In the Choose Your Database Objects dialog box, check the **Tables** checkbox to select all the tables.</span></span>  
    <span data-ttu-id="cba93-127">Wählen Sie außerdem die folgenden gespeicherten Prozeduren unter dem Knoten **gespeicherte Prozeduren und Funktionen** aus: **GetStudentGrades** und **GetDepartmentName**.</span><span class="sxs-lookup"><span data-stu-id="cba93-127">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **GetStudentGrades** and **GetDepartmentName**.</span></span> 

    ![Gespeicherte Prozeduren importieren](~/ef6/media/import.jpg)

    <span data-ttu-id="cba93-129">*Ab Visual Studio 2012 unterstützt der EF-Designer den Massen Import gespeicherter Prozeduren. Die **ausgewählten gespeicherten Prozeduren und Funktionen in das Entity Model importieren** werden standardmäßig aktiviert.*</span><span class="sxs-lookup"><span data-stu-id="cba93-129">*Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures. The **Import selected stored procedures and functions into theentity model** is checked by default.*</span></span>
-   <span data-ttu-id="cba93-130">Klicken Sie auf **Finish**.</span><span class="sxs-lookup"><span data-stu-id="cba93-130">Click **Finish**.</span></span>

<span data-ttu-id="cba93-131">Standardmäßig wird die Ergebnis Form jeder importierten gespeicherten Prozedur oder Funktion, die mehr als eine Spalte zurückgibt, automatisch zu einem neuen komplexen Typ.</span><span class="sxs-lookup"><span data-stu-id="cba93-131">By default, the result shape of each imported stored procedure or function that returns more than one column will automatically become a new complex type.</span></span> <span data-ttu-id="cba93-132">In diesem Beispiel möchten wir die Ergebnisse der Funktion " **GetStudentGrades** " der Entität " **StudentGrade** " und die Ergebnisse von " **GetDepartmentName** " zu " **None** " zuordnen ("**None** " ist der Standardwert).</span><span class="sxs-lookup"><span data-stu-id="cba93-132">In this example we want to map the results of the **GetStudentGrades** function to the **StudentGrade** entity and the results of the **GetDepartmentName** to **none** (**none** is the default value).</span></span>

<span data-ttu-id="cba93-133">Damit ein Funktions Import einen Entitätstyp zurückgibt, müssen die von der entsprechenden gespeicherten Prozedur zurückgegebenen Spalten genau mit den skalaren Eigenschaften des zurückgegebenen Entitäts Typs übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="cba93-133">For a function import to return an entity type, the columns returned by the corresponding stored procedure must exactly match the scalar properties of the returned entity type.</span></span> <span data-ttu-id="cba93-134">Ein Funktions Import kann auch Auflistungen von einfachen Typen, komplexen Typen oder keinen Wert zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="cba93-134">A function import can also return collections of simple types, complex types, or no value.</span></span>

-   <span data-ttu-id="cba93-135">Klicken Sie mit der rechten Maustaste auf die Entwurfs Oberfläche, und wählen Sie **Modell Browser**</span><span class="sxs-lookup"><span data-stu-id="cba93-135">Right-click the design surface and select **Model Browser**.</span></span>
-   <span data-ttu-id="cba93-136">Wählen Sie im **Modell Browser** **Funktions Importe**aus, und doppelklicken Sie dann auf die Funktion **GetStudentGrades** .</span><span class="sxs-lookup"><span data-stu-id="cba93-136">In **Model Browser**, select **Function Imports**, and then double-click the **GetStudentGrades** function.</span></span>
-   <span data-ttu-id="cba93-137">Wählen Sie im Dialogfeld Funktions Import bearbeiten die Option **Entitäten**aus,   und wählen Sie **StudentGrade**aus.</span><span class="sxs-lookup"><span data-stu-id="cba93-137">In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**.</span></span>  
    <span data-ttu-id="cba93-138">*Das Kontrollkästchen **Funktions Import ist zusammensetzbar** am oberen Rand des Dialog Felds **Funktions Importe** ermöglicht es Ihnen, Zusammensetz Bare Funktionen zuzuordnen. Wenn Sie dieses Kontrollkästchen aktivieren, werden nur Zusammensetz Bare Funktionen (Tabellenwert Funktionen) in der Dropdown Liste **Name der gespeicherten Prozedur/Funktion** angezeigt. Wenn Sie dieses Kontrollkästchen nicht aktivieren, werden nur nicht Zusammensetz Bare Funktionen in der Liste angezeigt.*</span><span class="sxs-lookup"><span data-stu-id="cba93-138">*The **Function Import is composable** checkbox at the top of the **Function Imports** dialog will let you map to composable functions. If you do check this box, only composable functions (Table-valued Functions) will appear in the **Stored Procedure / Function Name** drop-down list. If you do not check this box, only non-composable functions will be shown in the list.*</span></span>

## <a name="use-the-model"></a><span data-ttu-id="cba93-139">Verwenden des Modells</span><span class="sxs-lookup"><span data-stu-id="cba93-139">Use the Model</span></span>

<span data-ttu-id="cba93-140">Öffnen Sie die Datei **Program.cs** , in der die **Main** -Methode definiert ist.</span><span class="sxs-lookup"><span data-stu-id="cba93-140">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="cba93-141">Fügen Sie der Main-Funktion den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="cba93-141">Add the following code into the Main function.</span></span>

<span data-ttu-id="cba93-142">Der Code ruft zwei gespeicherte Prozeduren auf: **GetStudentGrades** (gibt **studentgrades** für die angegebene *Studentin TID*zurück) und **GetDepartmentName** (gibt den Namen der Abteilung im Output-Parameter zurück).</span><span class="sxs-lookup"><span data-stu-id="cba93-142">The code calls two stored procedures: **GetStudentGrades** (returns **StudentGrades** for the specified *StudentId*) and **GetDepartmentName** (returns the name of the department in the output parameter).</span></span>  

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        // Specify the Student ID.
        int studentId = 2;

        // Call GetStudentGrades and iterate through the returned collection.
        foreach (StudentGrade grade in context.GetStudentGrades(studentId))
        {
            Console.WriteLine("StudentID: {0}\tSubject={1}", studentId, grade.Subject);
            Console.WriteLine("Student grade: " + grade.Grade);
        }

        // Call GetDepartmentName.
        // Declare the name variable that will contain the value returned by the output parameter.
        ObjectParameter name = new ObjectParameter("Name", typeof(String));
        context.GetDepartmentName(1, name);
        Console.WriteLine("The department name is {0}", name.Value);

    }
```

<span data-ttu-id="cba93-143">Kompilieren Sie die Anwendung, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="cba93-143">Compile and run the application.</span></span> <span data-ttu-id="cba93-144">Das Programm erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="cba93-144">The program produces the following output:</span></span>

```console
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a><span data-ttu-id="cba93-145">Ausgabeparameter</span><span class="sxs-lookup"><span data-stu-id="cba93-145">Output Parameters</span></span>
-----------------

<span data-ttu-id="cba93-146">Wenn Output-Parameter verwendet werden, sind ihre Werte erst verfügbar, wenn die Ergebnisse vollständig gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="cba93-146">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="cba93-147">Dies ist auf das zugrunde liegende Verhalten von DbDataReader zurückzuführen. Weitere Informationen finden [Sie unter Abrufen von Daten mithilfe eines DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) .</span><span class="sxs-lookup"><span data-stu-id="cba93-147">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>
