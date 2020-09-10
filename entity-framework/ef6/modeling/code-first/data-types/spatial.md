---
title: Spatial-Code First-EF6
description: Räumliche Code First in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: d617aed1-15f2-48a9-b187-186991c666e3
uid: ef6/modeling/code-first/data-types/spatial
ms.openlocfilehash: 8c2d47a66979a228fdc4902857df999a21945547
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618157"
---
# <a name="spatial---code-first"></a><span data-ttu-id="173fa-103">Räumliche Code First</span><span class="sxs-lookup"><span data-stu-id="173fa-103">Spatial - Code First</span></span>
> [!NOTE]
> <span data-ttu-id="173fa-104">**Nur EF5** : die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 5 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="173fa-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="173fa-105">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="173fa-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="173fa-106">Das Video und die schrittweise exemplarische Vorgehensweise zeigen, wie räumliche Typen mit Entity Framework Code First zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="173fa-106">The video and step-by-step walkthrough shows how to map spatial types with Entity Framework Code First.</span></span> <span data-ttu-id="173fa-107">Außerdem wird veranschaulicht, wie eine LINQ-Abfrage verwendet wird, um einen Abstand zwischen zwei Standorten zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="173fa-107">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="173fa-108">In dieser exemplarischen Vorgehensweise wird Code First verwendet, um eine neue Datenbank zu erstellen, aber Sie können [Code First auch für eine vorhandene Datenbank](xref:ef6/modeling/code-first/workflows/existing-database)verwenden.</span><span class="sxs-lookup"><span data-stu-id="173fa-108">This walkthrough will use Code First to create a new database, but you can also use [Code First to an existing database](xref:ef6/modeling/code-first/workflows/existing-database).</span></span>

<span data-ttu-id="173fa-109">Die Unterstützung räumlicher Typen wurde in Entity Framework 5 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="173fa-109">Spatial type support was introduced in Entity Framework 5.</span></span> <span data-ttu-id="173fa-110">Beachten Sie, dass Sie für die Verwendung der neuen Funktionen, wie z. b. räumlichem Typ, aufzählen und Tabellenwert Funktionen, auf .NET Framework 4,5 abzielen müssen.</span><span class="sxs-lookup"><span data-stu-id="173fa-110">Note that to use the new features like spatial type, enums, and Table-valued functions, you must target .NET Framework 4.5.</span></span> <span data-ttu-id="173fa-111">Visual Studio 2012 hat standardmäßig .NET 4,5 als Ziel.</span><span class="sxs-lookup"><span data-stu-id="173fa-111">Visual Studio 2012 targets .NET 4.5 by default.</span></span>

<span data-ttu-id="173fa-112">Um räumliche Datentypen zu verwenden, müssen Sie auch einen Entity Framework Anbieter verwenden, der über räumliche Unterstützung verfügt.</span><span class="sxs-lookup"><span data-stu-id="173fa-112">To use spatial data types you must also use an Entity Framework provider that has spatial support.</span></span> <span data-ttu-id="173fa-113">Weitere Informationen finden Sie [unter Anbieter Unterstützung für räumliche Typen](xref:ef6/fundamentals/providers/spatial-support) .</span><span class="sxs-lookup"><span data-stu-id="173fa-113">See [provider support for spatial types](xref:ef6/fundamentals/providers/spatial-support) for more information.</span></span>

<span data-ttu-id="173fa-114">Es gibt zwei Haupt Datentypen für räumliche Daten: Geography und Geometry.</span><span class="sxs-lookup"><span data-stu-id="173fa-114">There are two main spatial data types: geography and geometry.</span></span> <span data-ttu-id="173fa-115">Der geography-Datentyp speichert Ellipsen Daten (z. b. GPS-breiten-und Längenkoordinaten).</span><span class="sxs-lookup"><span data-stu-id="173fa-115">The geography data type stores ellipsoidal data (for example, GPS latitude and longitude coordinates).</span></span> <span data-ttu-id="173fa-116">Der geometry-Datentyp stellt das euklidische (flache) Koordinatensystem dar.</span><span class="sxs-lookup"><span data-stu-id="173fa-116">The geometry data type represents Euclidean (flat) coordinate system.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="173fa-117">Video ansehen</span><span class="sxs-lookup"><span data-stu-id="173fa-117">Watch the video</span></span>
<span data-ttu-id="173fa-118">In diesem Video wird gezeigt, wie räumliche Typen mit Entity Framework Code First zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="173fa-118">This video shows how to map spatial types with Entity Framework Code First.</span></span> <span data-ttu-id="173fa-119">Außerdem wird veranschaulicht, wie eine LINQ-Abfrage verwendet wird, um einen Abstand zwischen zwei Standorten zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="173fa-119">It also demonstrates how to use a LINQ query to find a distance between two locations.</span></span>

<span data-ttu-id="173fa-120">**Präsentiert von**: Julia kornich</span><span class="sxs-lookup"><span data-stu-id="173fa-120">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="173fa-121">**Video**: [WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv)  |  [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="173fa-121">**Video**: [WMV](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.wmv) | [MP4](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-mp4video-spatialwithcodefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/9/1/3/913EA17E-6F97-41D8-A4FE-805A0D83D26A/HDI-ITPro-MSDN-winvideo-spatialwithcodefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="173fa-122">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="173fa-122">Pre-Requisites</span></span>

<span data-ttu-id="173fa-123">Sie müssen Visual Studio 2012, Ultimate, Premium, Professional oder Web Express Edition installiert haben, um diese exemplarische Vorgehensweise abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="173fa-123">You will need to have Visual Studio 2012, Ultimate, Premium, Professional, or Web Express edition installed to complete this walkthrough.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="173fa-124">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="173fa-124">Set up the Project</span></span>

1.  <span data-ttu-id="173fa-125">Öffnen Sie Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="173fa-125">Open Visual Studio 2012</span></span>
2.  <span data-ttu-id="173fa-126">Zeigen Sie im Menü **Datei** auf **neu**, und klicken Sie dann auf **Projekt** .</span><span class="sxs-lookup"><span data-stu-id="173fa-126">On the **File** menu, point to **New**, and then click **Project**</span></span>
3.  <span data-ttu-id="173fa-127">Klicken Sie im linken Bereich auf \*\*Visual C \# \*\*, und wählen Sie dann die **Konsolen** Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="173fa-127">In the left pane, click **Visual C\#**, and then select the **Console** template</span></span>
4.  <span data-ttu-id="173fa-128">Geben Sie **spatialcode First** als Namen für das Projekt ein, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="173fa-128">Enter **SpatialCodeFirst** as the name of the project and click **OK**</span></span>

## <a name="define-a-new-model-using-code-first"></a><span data-ttu-id="173fa-129">Definieren eines neuen Modells mit Code First</span><span class="sxs-lookup"><span data-stu-id="173fa-129">Define a New Model using Code First</span></span>

<span data-ttu-id="173fa-130">Wenn Sie Code First Entwicklung verwenden, schreiben Sie in der Regel .NET Framework Klassen, die ihr konzeptionelles (Domänen-) Modell definieren.</span><span class="sxs-lookup"><span data-stu-id="173fa-130">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="173fa-131">Der folgende Code definiert die University-Klasse.</span><span class="sxs-lookup"><span data-stu-id="173fa-131">The code below defines the University class.</span></span>

<span data-ttu-id="173fa-132">Die University verfügt über die Location-Eigenschaft des dbgeography-Typs.</span><span class="sxs-lookup"><span data-stu-id="173fa-132">The University has the Location property of the DbGeography type.</span></span> <span data-ttu-id="173fa-133">Wenn Sie den dbgeography-Typ verwenden möchten, müssen Sie einen Verweis auf die System. Data. Entity-Assembly hinzufügen und auch die System. Data. Spatial using-Anweisung hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="173fa-133">To use the DbGeography type, you must add a reference to the System.Data.Entity assembly and also add the System.Data.Spatial using statement.</span></span>

<span data-ttu-id="173fa-134">Öffnen Sie die Datei Program.cs, und fügen Sie die folgenden using-Anweisungen am Anfang der Datei ein:</span><span class="sxs-lookup"><span data-stu-id="173fa-134">Open the Program.cs file and paste the following using statements at the top of the file:</span></span>

``` csharp
using System.Data.Spatial;
```

<span data-ttu-id="173fa-135">Fügen Sie der Program.cs-Datei die folgende Definition der University-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="173fa-135">Add the following University class definition to the Program.cs file.</span></span>

``` csharp
public class University  
{
    public int UniversityID { get; set; }
    public string Name { get; set; }
    public DbGeography Location { get; set; }
}
```

## <a name="define-the-dbcontext-derived-type"></a><span data-ttu-id="173fa-136">Definieren des abgeleiteten dbcontext-Typs</span><span class="sxs-lookup"><span data-stu-id="173fa-136">Define the DbContext Derived Type</span></span>

<span data-ttu-id="173fa-137">Zusätzlich zur Definition von Entitäten müssen Sie eine Klasse definieren, die von DbContext abgeleitet ist und DbSet&lt;TEntity&gt;-Eigenschaften bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="173fa-137">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="173fa-138">Die DbSet&lt;TEntity&gt;-Eigenschaften informieren den Kontext darüber, welche Typen Sie in das Modell einbeziehen möchten.</span><span class="sxs-lookup"><span data-stu-id="173fa-138">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="173fa-139">Eine Instanz des von DbContext abgeleiteten Typs verwaltet die Entitätsobjekte während der Laufzeit, was das Auffüllen der Objekte mit Daten aus einer Datenbank, die Änderungsnachverfolgung und das persistente Speichern von Daten in der Datenbank umfasst.</span><span class="sxs-lookup"><span data-stu-id="173fa-139">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="173fa-140">Die Typen "dbcontext" und "dbset" werden in der EntityFramework-Assembly definiert.</span><span class="sxs-lookup"><span data-stu-id="173fa-140">The DbContext and DbSet types are defined in the EntityFramework assembly.</span></span> <span data-ttu-id="173fa-141">Wir fügen mit dem nuget-Paket "EntityFramework" einen Verweis auf diese dll hinzu.</span><span class="sxs-lookup"><span data-stu-id="173fa-141">We will add a reference to this DLL by using the EntityFramework NuGet package.</span></span>

1.  <span data-ttu-id="173fa-142">Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen.</span><span class="sxs-lookup"><span data-stu-id="173fa-142">In Solution Explorer, right-click on the project name.</span></span>
2.  <span data-ttu-id="173fa-143">Wählen Sie **nuget-Pakete verwalten... aus.**</span><span class="sxs-lookup"><span data-stu-id="173fa-143">Select **Manage NuGet Packages…**</span></span>
3.  <span data-ttu-id="173fa-144">Wählen Sie im Dialogfeld nuget-Pakete verwalten die Registerkarte **Online** aus, und wählen Sie das Paket **EntityFramework** aus.</span><span class="sxs-lookup"><span data-stu-id="173fa-144">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package.</span></span>
4.  <span data-ttu-id="173fa-145">Klicken Sie auf **Install** (Installieren).</span><span class="sxs-lookup"><span data-stu-id="173fa-145">Click **Install**</span></span>

<span data-ttu-id="173fa-146">Beachten Sie, dass zusätzlich zur EntityFramework-Assembly auch ein Verweis auf die System. ComponentModel. DataAnnotations-Assembly hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="173fa-146">Note, that in addition to the EntityFramework  assembly, a reference to the System.ComponentModel.DataAnnotations assembly is also added.</span></span>

<span data-ttu-id="173fa-147">Fügen Sie am Anfang der Program.cs-Datei die folgende using-Anweisung hinzu:</span><span class="sxs-lookup"><span data-stu-id="173fa-147">At the top of the Program.cs file, add the following using statement:</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="173fa-148">Fügen Sie in Program.cs die Kontext Definition hinzu.</span><span class="sxs-lookup"><span data-stu-id="173fa-148">In the Program.cs add the context definition.</span></span> 

``` csharp
public partial class UniversityContext : DbContext
{
    public DbSet<University> Universities { get; set; }
}
```

## <a name="persist-and-retrieve-data"></a><span data-ttu-id="173fa-149">Persistenz und Abrufen von Daten</span><span class="sxs-lookup"><span data-stu-id="173fa-149">Persist and Retrieve Data</span></span>

<span data-ttu-id="173fa-150">Öffnen Sie die Datei Program.cs, in der die Main-Methode definiert ist.</span><span class="sxs-lookup"><span data-stu-id="173fa-150">Open the Program.cs file where the Main method is defined.</span></span> <span data-ttu-id="173fa-151">Fügen Sie der Main-Funktion den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="173fa-151">Add the following code into the Main function.</span></span>

<span data-ttu-id="173fa-152">Der Code fügt dem Kontext zwei neue University-Objekte hinzu.</span><span class="sxs-lookup"><span data-stu-id="173fa-152">The code adds two new University objects to the context.</span></span> <span data-ttu-id="173fa-153">Räumliche Eigenschaften werden mithilfe der Methode "dbgeography. fromtext" initialisiert.</span><span class="sxs-lookup"><span data-stu-id="173fa-153">Spatial properties are initialized by using the DbGeography.FromText method.</span></span> <span data-ttu-id="173fa-154">Der als wellknowntext dargestellte geografiepunkt wird an die-Methode übermittelt.</span><span class="sxs-lookup"><span data-stu-id="173fa-154">The geography point represented as WellKnownText is passed to the method.</span></span> <span data-ttu-id="173fa-155">Der Code speichert die Daten dann.</span><span class="sxs-lookup"><span data-stu-id="173fa-155">The code then saves the data.</span></span> <span data-ttu-id="173fa-156">Anschließend wird die LINQ-Abfrage, die ein University-Objekt zurückgibt, dessen Standort dem angegebenen Speicherort am nächsten liegt, erstellt und ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="173fa-156">Then, the LINQ query that that returns a University object where its location is closest to the specified location, is constructed and executed.</span></span>

``` csharp
using (var context = new UniversityContext ())
{
    context.Universities.Add(new University()
        {
            Name = "Graphic Design Institute",
            Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
        });

    context. Universities.Add(new University()
        {
            Name = "School of Fine Art",
            Location = DbGeography.FromText("POINT(-122.335197 47.646711)"),
        });

    context.SaveChanges();

    var myLocation = DbGeography.FromText("POINT(-122.296623 47.640405)");

    var university = (from u in context.Universities
                        orderby u.Location.Distance(myLocation)
                        select u).FirstOrDefault();

    Console.WriteLine(
        "The closest University to you is: {0}.",
        university.Name);
}
```

<span data-ttu-id="173fa-157">Kompilieren Sie die Anwendung, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="173fa-157">Compile and run the application.</span></span> <span data-ttu-id="173fa-158">Das Programm erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="173fa-158">The program produces the following output:</span></span>

```console
The closest University to you is: School of Fine Art.
```

## <a name="view-the-generated-database"></a><span data-ttu-id="173fa-159">Anzeigen der generierten Datenbank</span><span class="sxs-lookup"><span data-stu-id="173fa-159">View the Generated Database</span></span>

<span data-ttu-id="173fa-160">Wenn Sie die Anwendung zum ersten Mal ausführen, erstellt die Entity Framework eine Datenbank für Sie.</span><span class="sxs-lookup"><span data-stu-id="173fa-160">When you run the application the first time, the Entity Framework creates a database for you.</span></span> <span data-ttu-id="173fa-161">Da Visual Studio 2012 installiert ist, wird die Datenbank auf der localdb-Instanz erstellt.</span><span class="sxs-lookup"><span data-stu-id="173fa-161">Because we have Visual Studio 2012 installed, the database will be created on the LocalDB instance.</span></span> <span data-ttu-id="173fa-162">Standardmäßig benennt die-Entity Framework die Datenbank nach dem voll qualifizierten Namen des abgeleiteten Kontexts (in diesem Beispiel " **spatialcodefirst. universitycontext**").</span><span class="sxs-lookup"><span data-stu-id="173fa-162">By default, the Entity Framework names the database after the fully qualified name of the derived context (in this example that is **SpatialCodeFirst.UniversityContext**).</span></span> <span data-ttu-id="173fa-163">Nachfolgend wird die vorhandene Datenbank verwendet.</span><span class="sxs-lookup"><span data-stu-id="173fa-163">The subsequent times the existing database will be used.</span></span>  

<span data-ttu-id="173fa-164">Beachten Sie Folgendes: Wenn Sie Änderungen am Modell vornehmen, nachdem die Datenbank erstellt wurde, sollten Sie Code First-Migrationen zum Aktualisieren des Datenbankschemas verwenden.</span><span class="sxs-lookup"><span data-stu-id="173fa-164">Note, that if you make any changes to your model after the database has been created, you should use Code First Migrations to update the database schema.</span></span> <span data-ttu-id="173fa-165">Ein Beispiel für die Verwendung von Migrationen finden Sie [unter Code First einer neuen Datenbank](xref:ef6/modeling/code-first/workflows/new-database) .</span><span class="sxs-lookup"><span data-stu-id="173fa-165">See [Code First to a New Database](xref:ef6/modeling/code-first/workflows/new-database) for an example of using Migrations.</span></span>

<span data-ttu-id="173fa-166">Gehen Sie folgendermaßen vor, um die Datenbank und die Daten anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="173fa-166">To view the database and data, do the following:</span></span>

1.  <span data-ttu-id="173fa-167">Wählen Sie im Hauptmenü von Visual Studio 2012 die Option SQL Server-Objekt-Explorer **anzeigen** aus  - &gt; **SQL Server Object Explorer**.</span><span class="sxs-lookup"><span data-stu-id="173fa-167">In the Visual Studio 2012 main menu, select **View** -&gt; **SQL Server Object Explorer**.</span></span>
2.  <span data-ttu-id="173fa-168">Wenn localdb nicht in der Liste der Server enthalten ist, klicken Sie auf **SQL Server** mit der rechten Maustaste, und wählen Sie **hinzu SQL Server fügen** aus, um die Verbindung mit der localdb-Instanz mit der standardmäßigen **Windows-Authentifizierung** herzustellen.</span><span class="sxs-lookup"><span data-stu-id="173fa-168">If LocalDB is not in the list of servers, click the right mouse button on **SQL Server** and select **Add SQL Server** Use the default **Windows Authentication** to connect to the the LocalDB instance</span></span>
3.  <span data-ttu-id="173fa-169">Erweitern Sie den Knoten localdb.</span><span class="sxs-lookup"><span data-stu-id="173fa-169">Expand the LocalDB node</span></span>
4.  <span data-ttu-id="173fa-170">Erweitern Sie den Ordner **Datenbanken** , um die neue Datenbank anzuzeigen und zur Tabelle **Universitäten** zu navigieren.</span><span class="sxs-lookup"><span data-stu-id="173fa-170">Unfold the **Databases** folder to see the new database and browse to the **Universities** table</span></span>
5.  <span data-ttu-id="173fa-171">Um Daten anzuzeigen, klicken Sie mit der rechten Maustaste auf die Tabelle, und wählen Sie **Daten anzeigen**</span><span class="sxs-lookup"><span data-stu-id="173fa-171">To view data, right-click on the table and select **View Data**</span></span>

## <a name="summary"></a><span data-ttu-id="173fa-172">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="173fa-172">Summary</span></span>

<span data-ttu-id="173fa-173">In dieser exemplarischen Vorgehensweise wurde erläutert, wie räumliche Typen mit Entity Framework Code First verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="173fa-173">In this walkthrough we looked at how to use spatial types with Entity Framework Code First.</span></span> 
