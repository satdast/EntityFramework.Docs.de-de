---
title: Table-Valued Funktionen (TVFs)-EF6
description: Table-Valued Funktionen (TVFs) in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/tvfs
ms.openlocfilehash: 2c699cad3ace253842d17aca4eca4df2318c4795
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066199"
---
# <a name="table-valued-functions-tvfs"></a><span data-ttu-id="8b92e-103">Table-Valued Funktionen (TVFs)</span><span class="sxs-lookup"><span data-stu-id="8b92e-103">Table-Valued Functions (TVFs)</span></span>
> [!NOTE]
> <span data-ttu-id="8b92e-104">**Nur EF5** : die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 5 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="8b92e-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="8b92e-105">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="8b92e-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="8b92e-106">Das Video und die schrittweise exemplarische Vorgehensweise zeigen, wie Sie Tabellenwert Funktionen (TVFs) mithilfe der Entity Framework Designer zuordnen.</span><span class="sxs-lookup"><span data-stu-id="8b92e-106">The video and step-by-step walkthrough shows how to map table-valued functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="8b92e-107">Außerdem wird veranschaulicht, wie eine TVF aus einer LINQ-Abfrage aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="8b92e-107">It also demonstrates how to call a TVF from a LINQ query.</span></span>

<span data-ttu-id="8b92e-108">TVFs werden derzeit nur im Database First Workflow unterstützt.</span><span class="sxs-lookup"><span data-stu-id="8b92e-108">TVFs are currently only supported in the Database First workflow.</span></span>

<span data-ttu-id="8b92e-109">Die TVF-Unterstützung wurde in Entity Framework Version 5 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="8b92e-109">TVF support was introduced in Entity Framework version 5.</span></span> <span data-ttu-id="8b92e-110">Beachten Sie, dass Sie für die Verwendung der neuen Features wie Tabellenwert Funktionen, Enumerationstypen und räumlichen Typen auf .NET Framework 4,5 abzielen müssen.</span><span class="sxs-lookup"><span data-stu-id="8b92e-110">Note that to use the new features like table-valued functions, enums, and spatial types you must target .NET Framework 4.5.</span></span> <span data-ttu-id="8b92e-111">Visual Studio 2012 hat standardmäßig .NET 4,5 als Ziel.</span><span class="sxs-lookup"><span data-stu-id="8b92e-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="8b92e-112">TVFs ähneln gespeicherten Prozeduren mit einem wichtigen Unterschied: das Ergebnis einer TVF ist zusammensetzbar.</span><span class="sxs-lookup"><span data-stu-id="8b92e-112">TVFs are very similar to stored procedures with one key difference: the result of a TVF is composable.</span></span> <span data-ttu-id="8b92e-113">Dies bedeutet, dass die Ergebnisse einer TVF in einer LINQ-Abfrage verwendet werden können, während die Ergebnisse einer gespeicherten Prozedur dies nicht möglich sind.</span><span class="sxs-lookup"><span data-stu-id="8b92e-113">That means the results from a TVF can be used in a LINQ query while the results of a stored procedure cannot.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="8b92e-114">Video ansehen</span><span class="sxs-lookup"><span data-stu-id="8b92e-114">Watch the video</span></span>

<span data-ttu-id="8b92e-115">**Präsentiert von**: Julia kornich</span><span class="sxs-lookup"><span data-stu-id="8b92e-115">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="8b92e-116">[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv)  |  [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span><span class="sxs-lookup"><span data-stu-id="8b92e-116">[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv) | [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="8b92e-117">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="8b92e-117">Pre-Requisites</span></span>

<span data-ttu-id="8b92e-118">Um diese exemplarische Vorgehensweise durchführen zu können, benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8b92e-118">To complete this walkthrough, you need to:</span></span>

- <span data-ttu-id="8b92e-119">Installieren Sie die [Datenbank "School](xref:ef6/resources/school-database)".</span><span class="sxs-lookup"><span data-stu-id="8b92e-119">Install the [School database](xref:ef6/resources/school-database).</span></span>

- <span data-ttu-id="8b92e-120">Aktuelle Version von Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b92e-120">Have a recent version of Visual Studio</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="8b92e-121">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="8b92e-121">Set up the Project</span></span>

1.  <span data-ttu-id="8b92e-122">Öffnen Sie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8b92e-122">Open Visual Studio</span></span>
2.  <span data-ttu-id="8b92e-123">Zeigen Sie im Menü **Datei** auf **neu**, und klicken Sie dann auf **Projekt** .</span><span class="sxs-lookup"><span data-stu-id="8b92e-123">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="8b92e-124">Klicken Sie im linken Bereich auf \*\*Visual C \# \*\*, und wählen Sie dann die **Konsolen** Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="8b92e-124">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="8b92e-125">Geben Sie **TVF** als Namen für das Projekt ein, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="8b92e-125">Enter **TVF** as the name of the project and click **OK**</span></span>

## <a name="add-a-tvf-to-the-database"></a><span data-ttu-id="8b92e-126">Hinzufügen einer TVF zur Datenbank</span><span class="sxs-lookup"><span data-stu-id="8b92e-126">Add a TVF to the Database</span></span>

-   <span data-ttu-id="8b92e-127">Wählen Sie **Ansicht- &gt; SQL Server-Objekt-Explorer**</span><span class="sxs-lookup"><span data-stu-id="8b92e-127">Select **View -&gt; SQL Server Object Explorer**</span></span>
-   <span data-ttu-id="8b92e-128">Wenn localdb nicht in der Liste der Server enthalten ist, klicken Sie mit der rechten Maustaste auf **SQL Server** , und wählen Sie **hinzu SQL Server fügen** aus, um die Verbindung zum localdb-Server mithilfe der Windows-Standard **Authentifizierung** herzustellen</span><span class="sxs-lookup"><span data-stu-id="8b92e-128">If LocalDB is not in the list of servers: Right-click on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the LocalDB server</span></span>
-   <span data-ttu-id="8b92e-129">Erweitern Sie den Knoten localdb.</span><span class="sxs-lookup"><span data-stu-id="8b92e-129">Expand the LocalDB node</span></span>
-   <span data-ttu-id="8b92e-130">Klicken Sie unter dem Knoten Datenbanken mit der rechten Maustaste auf den Datenbankknoten School, und wählen Sie **neue Abfrage aus.**</span><span class="sxs-lookup"><span data-stu-id="8b92e-130">Under the Databases node, right-click the School database node and select **New Query…**</span></span>
-   <span data-ttu-id="8b92e-131">Fügen Sie im T-SQL-Editor die folgende TVF-Definition ein.</span><span class="sxs-lookup"><span data-stu-id="8b92e-131">In T-SQL Editor, paste the following TVF definition</span></span>

``` SQL
CREATE FUNCTION [dbo].[GetStudentGradesForCourse]

(@CourseID INT)

RETURNS TABLE

RETURN
    SELECT [EnrollmentID],
           [CourseID],
           [StudentID],
           [Grade]
    FROM   [dbo].[StudentGrade]
    WHERE  CourseID = @CourseID
```

-   <span data-ttu-id="8b92e-132">Klicken Sie mit der rechten Maustaste auf den T-SQL-Editor, und wählen Sie **Ausführen** aus.</span><span class="sxs-lookup"><span data-stu-id="8b92e-132">Click the right mouse button on the T-SQL editor and select **Execute**</span></span>
-   <span data-ttu-id="8b92e-133">Die Funktion "getstudentgradesforcourse" wird der Datenbank "School" hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8b92e-133">The GetStudentGradesForCourse function is added to the School database</span></span>

 

## <a name="create-a-model"></a><span data-ttu-id="8b92e-134">Modellerstellung</span><span class="sxs-lookup"><span data-stu-id="8b92e-134">Create a Model</span></span>

1.  <span data-ttu-id="8b92e-135">Klicken Sie mit der rechten Maustaste auf den Projektnamen Projektmappen-Explorer, zeigen Sie auf **Hinzufügen**, und klicken Sie dann auf **Neues Element**</span><span class="sxs-lookup"><span data-stu-id="8b92e-135">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**</span></span>
2.  <span data-ttu-id="8b92e-136">Wählen Sie im linken Menü **Daten** aus, und wählen Sie dann im Bereich **Vorlagen** die Option **ADO.NET Entity Data Model** aus.</span><span class="sxs-lookup"><span data-stu-id="8b92e-136">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the **Templates** pane</span></span>
3.  <span data-ttu-id="8b92e-137">Geben Sie als Dateiname **tvfmodel. edmx** ein, und klicken Sie dann auf **Hinzufügen** .</span><span class="sxs-lookup"><span data-stu-id="8b92e-137">Enter **TVFModel.edmx** for the file name, and then click **Add**</span></span>
4.  <span data-ttu-id="8b92e-138">Wählen Sie im Dialogfeld Modell Inhalte auswählen die Option **aus Datenbank generieren aus**, und klicken Sie dann auf **weiter** .</span><span class="sxs-lookup"><span data-stu-id="8b92e-138">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**</span></span>
5.  <span data-ttu-id="8b92e-139">Klicken Sie auf **neue Verbindung** eingeben **(localdb) \\ mssqllocaldb** im Textfeld Server Name geben Sie **School**   für den Datenbanknamen ein, klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="8b92e-139">Click **New Connection** Enter **(localdb)\\mssqllocaldb** in the Server name text box Enter **School** for the database name Click **OK**</span></span>
6.  <span data-ttu-id="8b92e-140">Wählen Sie im Dialogfeld Datenbankobjekte auswählen unter dem Knoten **Tabellen**   die Tabellen **Person**, **StudentGrade**und **Course**aus.  </span><span class="sxs-lookup"><span data-stu-id="8b92e-140">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person**, **StudentGrade**, and **Course** tables</span></span>
7.  <span data-ttu-id="8b92e-141">Wählen Sie im Knoten " **gespeicherte Prozeduren und Funktionen**" die Funktion " **getstudentgradesforcourse** " aus   , die mit der Entity Designer ab Visual Studio 2012 das Batch importieren gespeicherter Prozeduren und Funktionen ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="8b92e-141">Select the **GetStudentGradesForCourse** function located under the **Stored Procedures and Functions** node Note, that starting with Visual Studio 2012, the Entity Designer allows you to batch import your Stored Procedures and Functions</span></span>
8.  <span data-ttu-id="8b92e-142">Klicken auf **Fertig** stellen</span><span class="sxs-lookup"><span data-stu-id="8b92e-142">Click **Finish**</span></span>
9.  <span data-ttu-id="8b92e-143">Die Entity Designer, die eine Entwurfs Oberfläche zum Bearbeiten des Modells bereitstellt, wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="8b92e-143">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="8b92e-144">Alle Objekte, die Sie im Dialogfeld **Wählen Sie Ihre Datenbankobjekte**ausgewählt   haben, werden dem Modell hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8b92e-144">All the objects that you selected in the **Choose Your Database Objects** dialog box are added to the model.</span></span>
10. <span data-ttu-id="8b92e-145">Standardmäßig wird die Ergebnis Form der einzelnen importierten gespeicherten Prozeduren oder Funktionen automatisch zu einem neuen komplexen Typ im Entitäts Modell.</span><span class="sxs-lookup"><span data-stu-id="8b92e-145">By default, the result shape of each imported stored procedure or function will automatically become a new complex type in your entity model.</span></span> <span data-ttu-id="8b92e-146">Wir möchten jedoch die Ergebnisse der Funktion "getstudentgradesforcourse" der Entität "StudentGrade" zuordnen: Klicken Sie mit der rechten Maustaste auf die Entwurfs Oberfläche, und wählen Sie " **Modell Browser** " im Modell Browser aus. Wählen Sie " **Funktions Importe**" aus **, und Doppel**klicken Sie dann im Dialogfeld Funktions Import bearbeiten auf die Funktion " **getstudentgradesforcourse** ".    **StudentGrade**</span><span class="sxs-lookup"><span data-stu-id="8b92e-146">But we want to map the results of the GetStudentGradesForCourse function to the StudentGrade entity: Right-click the design surface and select **Model Browser** In Model Browser, select **Function Imports**, and then double-click the **GetStudentGradesForCourse** function In the Edit Function Import dialog box, select **Entities** and choose **StudentGrade**</span></span>

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="8b92e-147">Persistenz und Abrufen von Daten</span><span class="sxs-lookup"><span data-stu-id="8b92e-147">Persist and Retrieve Data</span></span>

<span data-ttu-id="8b92e-148">Öffnen Sie die Datei, in der die Main-Methode definiert ist.</span><span class="sxs-lookup"><span data-stu-id="8b92e-148">Open the file where the Main method is defined.</span></span> <span data-ttu-id="8b92e-149">Fügen Sie der Main-Funktion den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="8b92e-149">Add the following code into the Main function.</span></span>

<span data-ttu-id="8b92e-150">Der folgende Code veranschaulicht, wie eine Abfrage erstellt wird, die eine Tabellenwert Funktion verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b92e-150">The following code demonstrates how to build a query that uses a Table-valued Function.</span></span> <span data-ttu-id="8b92e-151">Die Abfrage projiziert die Ergebnisse in einen anonymen Typ, der den zugehörigen Kurs Titel und verwandte Studenten mit einem Wert größer oder gleich 3,5 enthält.</span><span class="sxs-lookup"><span data-stu-id="8b92e-151">The query projects the results into an anonymous type that contains the related Course title and related students with a grade greater or equal to 3.5.</span></span>

``` csharp
using (var context = new SchoolEntities())
{
    var CourseID = 4022;
    var Grade = 3.5M;

    // Return all the best students in the Microeconomics class.
    var students = from s in context.GetStudentGradesForCourse(CourseID)
                            where s.Grade >= Grade
                            select new
                            {
                                s.Person,
                                s.Course.Title
                            };

    foreach (var result in students)
    {
        Console.WriteLine(
            "Couse: {0}, Student: {1} {2}",
            result.Title,  
            result.Person.FirstName,  
            result.Person.LastName);
    }
}
```

<span data-ttu-id="8b92e-152">Kompilieren Sie die Anwendung, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="8b92e-152">Compile and run the application.</span></span> <span data-ttu-id="8b92e-153">Das Programm erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="8b92e-153">The program produces the following output:</span></span>

```console
Couse: Microeconomics, Student: Arturo Anand
Couse: Microeconomics, Student: Carson Bryant
```

## <a name="summary"></a><span data-ttu-id="8b92e-154">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="8b92e-154">Summary</span></span>

<span data-ttu-id="8b92e-155">In dieser exemplarischen Vorgehensweise wird erläutert, wie Tabellenwert Funktionen (TVFs) mithilfe der Entity Framework Designer zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="8b92e-155">In this walkthrough we looked at how to map Table-valued Functions (TVFs) using the Entity Framework Designer.</span></span> <span data-ttu-id="8b92e-156">Außerdem wurde veranschaulicht, wie eine TVF aus einer LINQ-Abfrage aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="8b92e-156">It also demonstrated how to call a TVF from a LINQ query.</span></span>
