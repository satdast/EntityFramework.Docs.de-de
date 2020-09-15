---
title: Aufzählungs Unterstützung-Code First-EF6
description: Aufzählungs Unterstützung-Code First in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-types/enums
ms.openlocfilehash: e9e1412afb097cc4c944be63130dba0496a80f14
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073976"
---
# <a name="enum-support---code-first"></a><span data-ttu-id="0e4a0-103">Aufzählungs Unterstützung-Code First</span><span class="sxs-lookup"><span data-stu-id="0e4a0-103">Enum Support - Code First</span></span>
> [!NOTE]
> <span data-ttu-id="0e4a0-104">**Nur EF5** : die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 5 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="0e4a0-105">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="0e4a0-106">In diesem Video und der schrittweisen exemplarischen Vorgehensweise wird die Verwendung von Enumerationstypen mit Entity Framework Code First erläutert.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-106">This video and step-by-step walkthrough shows how to use enum types with Entity Framework Code First.</span></span> <span data-ttu-id="0e4a0-107">Außerdem wird veranschaulicht, wie Sie-auf--Aufstände in einer LINQ-Abfrage verwenden.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-107">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="0e4a0-108">In dieser exemplarischen Vorgehensweise wird Code First verwendet, um eine neue Datenbank zu erstellen, aber Sie können Code First auch verwenden, um eine Zuordnung [zu einer vorhandenen Datenbank](xref:ef6/modeling/code-first/workflows/existing-database)herzustellen.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-108">This walkthrough will use Code First to create a new database, but you can also use [Code First to map to an existing database](xref:ef6/modeling/code-first/workflows/existing-database).</span></span>

<span data-ttu-id="0e4a0-109">Die Aufzählungs Unterstützung wurde in Entity Framework 5 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-109">Enum support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="0e4a0-110">Wenn Sie die neuen Funktionen wie Enumerationstypen, räumliche Datentypen und Tabellenwert Funktionen verwenden möchten, müssen Sie .NET Framework 4,5-Zielversion verwenden.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-110">To use the new features like enums, spatial data types, and table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="0e4a0-111">Visual Studio 2012 hat standardmäßig .NET 4,5 als Ziel.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="0e4a0-112">In Entity Framework kann eine Enumeration die folgenden zugrunde liegenden Typen aufweisen: **Byte**, **Int16**, **Int32**, **Int64** oder **SByte**.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-112">In Entity Framework, an enumeration can have the following underlying types: **Byte**, **Int16**, **Int32**, **Int64** , or **SByte**.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="0e4a0-113">Video ansehen</span><span class="sxs-lookup"><span data-stu-id="0e4a0-113">Watch the video</span></span>
<span data-ttu-id="0e4a0-114">In diesem Video wird gezeigt, wie Enumerationstypen mit Entity Framework Code First verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-114">This video shows how to use enum types with Entity Framework Code First.</span></span> <span data-ttu-id="0e4a0-115">Außerdem wird veranschaulicht, wie Sie-auf--Aufstände in einer LINQ-Abfrage verwenden.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-115">It also demonstrates how to use enums in a LINQ query.</span></span>

<span data-ttu-id="0e4a0-116">**Präsentiert von**: Julia kornich</span><span class="sxs-lookup"><span data-stu-id="0e4a0-116">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="0e4a0-117">**Video**: [WMV](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv)  |  [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="0e4a0-117">**Video**: [WMV](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv) | [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="0e4a0-118">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="0e4a0-118">Pre-Requisites</span></span>

<span data-ttu-id="0e4a0-119">Sie müssen Visual Studio 2012, Ultimate, Premium, Professional oder Web Express Edition installiert haben, um diese exemplarische Vorgehensweise abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-119">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

 

## <a name="set-up-the-project"></a><span data-ttu-id="0e4a0-120">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="0e4a0-120">Set up the Project</span></span>

1.  <span data-ttu-id="0e4a0-121">Öffnen Sie Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="0e4a0-121">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="0e4a0-122">Zeigen Sie im Menü **Datei** auf **neu**, und klicken Sie dann auf **Projekt** .</span><span class="sxs-lookup"><span data-stu-id="0e4a0-122">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="0e4a0-123">Klicken Sie im linken Bereich auf \*\*Visual C \# \*\*, und wählen Sie dann die **Konsolen** Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-123">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="0e4a0-124">Geben Sie " **enumcodefirst** " als Namen des Projekts ein, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="0e4a0-124">Enter **EnumCodeFirst** as the name of the project and click **OK**</span></span>

## <a name="define-a-new-model-using-code-first"></a><span data-ttu-id="0e4a0-125">Definieren eines neuen Modells mit Code First</span><span class="sxs-lookup"><span data-stu-id="0e4a0-125">Define a New Model using Code First</span></span>

<span data-ttu-id="0e4a0-126">Wenn Sie Code First Entwicklung verwenden, schreiben Sie in der Regel .NET Framework Klassen, die ihr konzeptionelles (Domänen-) Modell definieren.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-126">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="0e4a0-127">Der folgende Code definiert die Department-Klasse.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-127">The code below defines the Department class.</span></span>

<span data-ttu-id="0e4a0-128">Der Code definiert auch die departmentnames-Enumeration.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-128">The code also defines the DepartmentNames enumeration.</span></span> <span data-ttu-id="0e4a0-129">Standardmäßig ist die Enumeration vom Typ **int** .</span><span class="sxs-lookup"><span data-stu-id="0e4a0-129">By default, the enumeration is of **int** type.</span></span> <span data-ttu-id="0e4a0-130">Die Name-Eigenschaft für die Department-Klasse ist vom Typ "departmentnames".</span><span class="sxs-lookup"><span data-stu-id="0e4a0-130">The Name property on the Department class is of the DepartmentNames type.</span></span>

<span data-ttu-id="0e4a0-131">Öffnen Sie die Datei Program.cs, und fügen Sie die folgenden Klassendefinitionen ein.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-131">Open the Program.cs file and paste the following class definitions.</span></span>

``` csharp
public enum DepartmentNames
{
    English,
    Math,
    Economics
}     

public partial class Department
{
    public int DepartmentID { get; set; }
    public DepartmentNames Name { get; set; }
    public decimal Budget { get; set; }
}
```
 

## <a name="define-the-dbcontext-derived-type"></a><span data-ttu-id="0e4a0-132">Definieren des abgeleiteten dbcontext-Typs</span><span class="sxs-lookup"><span data-stu-id="0e4a0-132">Define the DbContext Derived Type</span></span>

<span data-ttu-id="0e4a0-133">Zusätzlich zur Definition von Entitäten müssen Sie eine Klasse definieren, die von DbContext abgeleitet ist und DbSet&lt;TEntity&gt;-Eigenschaften bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-133">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="0e4a0-134">Die DbSet&lt;TEntity&gt;-Eigenschaften informieren den Kontext darüber, welche Typen Sie in das Modell einbeziehen möchten.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-134">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="0e4a0-135">Eine Instanz des von DbContext abgeleiteten Typs verwaltet die Entitätsobjekte während der Laufzeit, was das Auffüllen der Objekte mit Daten aus einer Datenbank, die Änderungsnachverfolgung und das persistente Speichern von Daten in der Datenbank umfasst.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-135">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="0e4a0-136">Die Typen "dbcontext" und "dbset" werden in der EntityFramework-Assembly definiert.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-136">The DbContext and DbSet types are defined in the EntityFramework assembly.</span></span> <span data-ttu-id="0e4a0-137">Wir fügen mit dem nuget-Paket "EntityFramework" einen Verweis auf diese dll hinzu.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-137">We will add a reference to this DLL by using the EntityFramework NuGet package.</span></span>

1.  <span data-ttu-id="0e4a0-138">Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-138">In Solution Explorer, right-click on the project name.</span></span>
2.  <span data-ttu-id="0e4a0-139">Wählen Sie **nuget-Pakete verwalten... aus.**</span><span class="sxs-lookup"><span data-stu-id="0e4a0-139">Select **Manage NuGet Packages…**</span></span>
3.  <span data-ttu-id="0e4a0-140">Wählen Sie im Dialogfeld nuget-Pakete verwalten die Registerkarte **Online** aus, und wählen Sie das Paket **EntityFramework** aus.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-140">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package.</span></span>
4.  <span data-ttu-id="0e4a0-141">Klicken Sie auf **Install** (Installieren).</span><span class="sxs-lookup"><span data-stu-id="0e4a0-141">Click **Install**</span></span>

<span data-ttu-id="0e4a0-142">Beachten Sie, dass neben der EntityFramework-Assembly auch Verweise auf System. ComponentModel. DataAnnotations und System. Data. Entity-Assemblys hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-142">Note, that in addition to the EntityFramework  assembly, references to System.ComponentModel.DataAnnotations and System.Data.Entity assemblies are added as well.</span></span>

<span data-ttu-id="0e4a0-143">Fügen Sie am Anfang der Program.cs-Datei die folgende using-Anweisung hinzu:</span><span class="sxs-lookup"><span data-stu-id="0e4a0-143">At the top of the Program.cs file, add the following using statement:</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="0e4a0-144">Fügen Sie in Program.cs die Kontext Definition hinzu.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-144">In the Program.cs add the context definition.</span></span> 

``` csharp
public partial class EnumTestContext : DbContext
{
    public DbSet<Department> Departments { get; set; }
}
```
 

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="0e4a0-145">Persistenz und Abrufen von Daten</span><span class="sxs-lookup"><span data-stu-id="0e4a0-145">Persist and Retrieve Data</span></span>

<span data-ttu-id="0e4a0-146">Öffnen Sie die Datei Program.cs, in der die Main-Methode definiert ist.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-146">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="0e4a0-147">Fügen Sie der Main-Funktion den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-147">Add the following code into the Main function.</span></span> <span data-ttu-id="0e4a0-148">Der Code fügt dem Kontext ein neues Abteilungs Objekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-148">The code adds a new Department object to the context.</span></span> <span data-ttu-id="0e4a0-149">Anschließend werden die Daten gespeichert.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-149">It then saves the data.</span></span> <span data-ttu-id="0e4a0-150">Der Code führt außerdem eine LINQ-Abfrage aus, die eine Abteilung zurückgibt, in der der Name departmentnames. English lautet.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-150">The code also executes a LINQ query that returns a Department where the name is DepartmentNames.English.</span></span>

``` csharp
using (var context = new EnumTestContext())
{
    context.Departments.Add(new Department { Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

<span data-ttu-id="0e4a0-151">Kompilieren Sie die Anwendung, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-151">Compile and run the application.</span></span> <span data-ttu-id="0e4a0-152">Das Programm erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="0e4a0-152">The program produces the following output:</span></span>

``` csharp
DepartmentID: 1 Name: English
```
 

## <a name="view-the-generated-database"></a><span data-ttu-id="0e4a0-153">Anzeigen der generierten Datenbank</span><span class="sxs-lookup"><span data-stu-id="0e4a0-153">View the Generated Database</span></span>

<span data-ttu-id="0e4a0-154">Wenn Sie die Anwendung zum ersten Mal ausführen, erstellt die Entity Framework eine Datenbank für Sie.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-154">When you run the application the first time, the Entity Framework creates a database for you.</span></span> <span data-ttu-id="0e4a0-155">Da Visual Studio 2012 installiert ist, wird die Datenbank auf der localdb-Instanz erstellt.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-155">Because we have Visual Studio 2012 installed, the database will be created on the LocalDB instance.</span></span> <span data-ttu-id="0e4a0-156">Standardmäßig benennt der Entity Framework die Datenbank nach dem voll qualifizierten Namen des abgeleiteten Kontexts (in diesem Beispiel **enumcodefirst. enumtestcontext**).</span><span class="sxs-lookup"><span data-stu-id="0e4a0-156">By default, the Entity Framework names the database after the fully qualified name of the derived context (for this example that is **EnumCodeFirst.EnumTestContext**).</span></span> <span data-ttu-id="0e4a0-157">Nachfolgend wird die vorhandene Datenbank verwendet.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-157">The subsequent times the existing database will be used.</span></span>  

<span data-ttu-id="0e4a0-158">Beachten Sie Folgendes: Wenn Sie Änderungen am Modell vornehmen, nachdem die Datenbank erstellt wurde, sollten Sie Code First-Migrationen zum Aktualisieren des Datenbankschemas verwenden.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-158">Note, that if you make any changes to your model after the database has been created, you should use Code First Migrations to update the database schema.</span></span> <span data-ttu-id="0e4a0-159">Ein Beispiel für die Verwendung von Migrationen finden Sie [unter Code First einer neuen Datenbank](xref:ef6/modeling/code-first/workflows/new-database) .</span><span class="sxs-lookup"><span data-stu-id="0e4a0-159">See [Code First to a New Database](xref:ef6/modeling/code-first/workflows/new-database) for an example of using Migrations.</span></span>

<span data-ttu-id="0e4a0-160">Gehen Sie folgendermaßen vor, um die Datenbank und die Daten anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="0e4a0-160">To view the database and data, do the following:</span></span>

1.  <span data-ttu-id="0e4a0-161">Wählen Sie im Hauptmenü von Visual Studio 2012 die Option SQL Server-Objekt-Explorer **anzeigen** aus  - &gt; **SQL Server Object Explorer**.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-161">In the Visual Studio 2012 main menu, select **View** -&gt; **SQL Server Object Explorer**.</span></span>
2.  <span data-ttu-id="0e4a0-162">Wenn localdb nicht in der Liste der Server enthalten ist, klicken Sie auf **SQL Server** mit der rechten Maustaste, und wählen Sie **Hinzufügen** aus, um eine Verbindung mit der localdb-Instanz herzustellen SQL Server die Standard **Authentifizierung** zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-162">If LocalDB is not in the list of servers, click the right mouse button on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the LocalDB instance</span></span>
3.  <span data-ttu-id="0e4a0-163">Erweitern Sie den Knoten localdb.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-163">Expand the LocalDB node</span></span>
4.  <span data-ttu-id="0e4a0-164">Erweitern Sie den Ordner **Datenbanken** , um die neue Datenbank anzuzeigen, und navigieren Sie zu der **Abteilungs** Tabelle, Code First keine Tabelle erstellt, die dem Enumerationstyp zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-164">Unfold the **Databases** folder to see the new database and browse to the **Department** table Note, that Code First does not create a table that maps to the enumeration type</span></span>
5.  <span data-ttu-id="0e4a0-165">Um Daten anzuzeigen, klicken Sie mit der rechten Maustaste auf die Tabelle, und wählen Sie **Daten anzeigen**</span><span class="sxs-lookup"><span data-stu-id="0e4a0-165">To view data, right-click on the table and select **View Data**</span></span>

## <a name="summary"></a><span data-ttu-id="0e4a0-166">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="0e4a0-166">Summary</span></span>

<span data-ttu-id="0e4a0-167">In dieser exemplarischen Vorgehensweise haben wir uns mit der Verwendung von Enumerationstypen mit Entity Framework Code First beschäftigt.</span><span class="sxs-lookup"><span data-stu-id="0e4a0-167">In this walkthrough we looked at how to use enum types with Entity Framework Code First.</span></span> 
