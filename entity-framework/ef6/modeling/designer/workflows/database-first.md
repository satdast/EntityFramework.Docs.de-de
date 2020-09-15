---
title: Database First-EF6
description: Database First in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/workflows/database-first
ms.openlocfilehash: 412d83b37ed5f3e2b863dca0913746e61ecdef76
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073174"
---
# <a name="database-first"></a><span data-ttu-id="7b97d-103">Database First</span><span class="sxs-lookup"><span data-stu-id="7b97d-103">Database First</span></span>
<span data-ttu-id="7b97d-104">Dieses Video und die schrittweise exemplarische Vorgehensweise bieten eine Einführung in die Database First Entwicklung mit Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="7b97d-104">This video and step-by-step walkthrough provide an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="7b97d-105">Mit Database First können Sie ein Modell aus einer vorhandenen Datenbank umkehren.</span><span class="sxs-lookup"><span data-stu-id="7b97d-105">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="7b97d-106">Das Modell wird in einer EDMX-Datei (edmx-Erweiterung) gespeichert und kann in der Entity Framework Designer angezeigt und bearbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="7b97d-106">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="7b97d-107">Die Klassen, mit denen Sie in Ihrer Anwendung interagieren, werden automatisch aus der EDMX-Datei generiert.</span><span class="sxs-lookup"><span data-stu-id="7b97d-107">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="7b97d-108">Video ansehen</span><span class="sxs-lookup"><span data-stu-id="7b97d-108">Watch the video</span></span>
<span data-ttu-id="7b97d-109">Dieses Video bietet eine Einführung in die Database First Entwicklung mit Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="7b97d-109">This video provides an introduction to Database First development using Entity Framework.</span></span> <span data-ttu-id="7b97d-110">Mit Database First können Sie ein Modell aus einer vorhandenen Datenbank umkehren.</span><span class="sxs-lookup"><span data-stu-id="7b97d-110">Database First allows you to reverse engineer a model from an existing database.</span></span> <span data-ttu-id="7b97d-111">Das Modell wird in einer EDMX-Datei (edmx-Erweiterung) gespeichert und kann in der Entity Framework Designer angezeigt und bearbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="7b97d-111">The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer.</span></span> <span data-ttu-id="7b97d-112">Die Klassen, mit denen Sie in Ihrer Anwendung interagieren, werden automatisch aus der EDMX-Datei generiert.</span><span class="sxs-lookup"><span data-stu-id="7b97d-112">The classes that you interact with in your application are automatically generated from the EDMX file.</span></span>

<span data-ttu-id="7b97d-113">**Präsentation:**[Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="7b97d-113">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="7b97d-114">**Video**: [WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv)  |  [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span><span class="sxs-lookup"><span data-stu-id="7b97d-114">**Video**: [WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv) | [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="7b97d-115">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="7b97d-115">Pre-Requisites</span></span>

<span data-ttu-id="7b97d-116">Sie müssen mindestens Visual Studio 2010 oder Visual Studio 2012 installiert haben, um diese exemplarische Vorgehensweise abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="7b97d-116">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="7b97d-117">Wenn Sie Visual Studio 2010 verwenden, müssen Sie auch [nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installieren.</span><span class="sxs-lookup"><span data-stu-id="7b97d-117">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

 

## <a name="1-create-an-existing-database"></a><span data-ttu-id="7b97d-118">1. Erstellen Sie eine vorhandene Datenbank.</span><span class="sxs-lookup"><span data-stu-id="7b97d-118">1. Create an Existing Database</span></span>

<span data-ttu-id="7b97d-119">Wenn Sie eine vorhandene Datenbank als Ziel haben, wird Sie in der Regel bereits erstellt, aber in dieser exemplarischen Vorgehensweise müssen wir eine Datenbank erstellen, auf die zugegriffen werden kann.</span><span class="sxs-lookup"><span data-stu-id="7b97d-119">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="7b97d-120">Der Datenbankserver, der mit Visual Studio installiert wird, unterscheidet sich abhängig von der installierten Version von Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="7b97d-120">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="7b97d-121">Wenn Sie Visual Studio 2010 verwenden, erstellen Sie eine SQL Express-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="7b97d-121">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="7b97d-122">Wenn Sie Visual Studio 2012 verwenden, erstellen Sie eine [localdb](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) -Datenbank.</span><span class="sxs-lookup"><span data-stu-id="7b97d-122">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) database.</span></span>

 

<span data-ttu-id="7b97d-123">Nun generieren wir die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="7b97d-123">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="7b97d-124">Öffnen Sie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7b97d-124">Open Visual Studio</span></span>
-   <span data-ttu-id="7b97d-125">**Ansicht- &gt; Server-Explorer**</span><span class="sxs-lookup"><span data-stu-id="7b97d-125">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="7b97d-126">Klicken Sie mit der rechten Maustaste auf **Datenverbindungen- &gt; Verbindung hinzufügen...**</span><span class="sxs-lookup"><span data-stu-id="7b97d-126">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="7b97d-127">Wenn Sie über Server-Explorer keine Verbindung mit einer Datenbank hergestellt haben, müssen Sie Microsoft SQL Server als Datenquelle auswählen.</span><span class="sxs-lookup"><span data-stu-id="7b97d-127">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![Datenquelle auswählen](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="7b97d-129">Stellen Sie eine Verbindung mit localdb oder SQL Express her, je nachdem, welche installiert wurde, und geben Sie **databasefirst. blogals** Datenbanknamen ein.</span><span class="sxs-lookup"><span data-stu-id="7b97d-129">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **DatabaseFirst.Blogging** as the database name</span></span>

    ![SQL Express-Verbindung (DF)](~/ef6/media/sqlexpressconnectiondf.png)

    ![Localdb-Verbindung (DF)](~/ef6/media/localdbconnectiondf.png)

-   <span data-ttu-id="7b97d-132">Wählen Sie **OK** aus, und Sie werden gefragt, ob Sie eine neue Datenbank erstellen möchten, und wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="7b97d-132">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![Dialog Feld Create Database](~/ef6/media/createdatabasedialog.png)

-   <span data-ttu-id="7b97d-134">Die neue Datenbank wird nun in Server-Explorer angezeigt, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **neue Abfrage** aus.</span><span class="sxs-lookup"><span data-stu-id="7b97d-134">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="7b97d-135">Kopieren Sie den folgenden SQL-Befehl in die neue Abfrage, klicken Sie mit der rechten Maustaste auf die Abfrage, und wählen Sie **Ausführen** .</span><span class="sxs-lookup"><span data-stu-id="7b97d-135">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
CREATE TABLE [dbo].[Blogs] (
    [BlogId] INT IDENTITY (1, 1) NOT NULL,
    [Name] NVARCHAR (200) NULL,
    [Url]  NVARCHAR (200) NULL,
    CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
);

CREATE TABLE [dbo].[Posts] (
    [PostId] INT IDENTITY (1, 1) NOT NULL,
    [Title] NVARCHAR (200) NULL,
    [Content] NTEXT NULL,
    [BlogId] INT NOT NULL,
    CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
    CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
);
```

## <a name="2-create-the-application"></a><span data-ttu-id="7b97d-136">2. Erstellen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="7b97d-136">2. Create the Application</span></span>

<span data-ttu-id="7b97d-137">Um dies zu gewährleisten, erstellen wir eine einfache Konsolenanwendung, die die Database First für den Datenzugriff verwendet:</span><span class="sxs-lookup"><span data-stu-id="7b97d-137">To keep things simple we’re going to build a basic console application that uses the Database First to perform data access:</span></span>

-   <span data-ttu-id="7b97d-138">Öffnen Sie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7b97d-138">Open Visual Studio</span></span>
-   <span data-ttu-id="7b97d-139">**Datei- &gt; New- &gt; Project...**</span><span class="sxs-lookup"><span data-stu-id="7b97d-139">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="7b97d-140">Wählen Sie im Menü auf der linken Seite und **Konsolenanwendung** **Windows** aus.</span><span class="sxs-lookup"><span data-stu-id="7b97d-140">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="7b97d-141">Geben Sie **databasefirstsample** als Name ein.</span><span class="sxs-lookup"><span data-stu-id="7b97d-141">Enter **DatabaseFirstSample** as the name</span></span>
-   <span data-ttu-id="7b97d-142">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="7b97d-142">Select **OK**</span></span>

 

## <a name="3-reverse-engineer-model"></a><span data-ttu-id="7b97d-143">3. Reverse-Engineering-Modell</span><span class="sxs-lookup"><span data-stu-id="7b97d-143">3. Reverse Engineer Model</span></span>

<span data-ttu-id="7b97d-144">Wir verwenden Entity Framework Designer, die als Teil von Visual Studio enthalten ist, um das Modell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7b97d-144">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="7b97d-145">**Project- &gt; Neues Element hinzufügen...**</span><span class="sxs-lookup"><span data-stu-id="7b97d-145">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="7b97d-146">Wählen Sie im linken Menü **Daten** aus, und klicken Sie dann auf **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="7b97d-146">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="7b97d-147">Geben Sie als Name **bloggingmodel** ein, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="7b97d-147">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="7b97d-148">Dadurch wird der **Entity Data Model-Assistent** gestartet.</span><span class="sxs-lookup"><span data-stu-id="7b97d-148">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="7b97d-149">Wählen Sie **aus Datenbank generieren aus** , und klicken Sie auf **weiter**</span><span class="sxs-lookup"><span data-stu-id="7b97d-149">Select **Generate from Database** and click **Next**</span></span>

    ![Schritt 1 des Assistenten](~/ef6/media/wizardstep1.png)

-   <span data-ttu-id="7b97d-151">Wählen Sie die Verbindung mit der Datenbank aus, die Sie im ersten Abschnitt erstellt haben, geben Sie **bloggingcontext** als Namen der Verbindungs Zeichenfolge ein, und klicken Sie auf **weiter** .</span><span class="sxs-lookup"><span data-stu-id="7b97d-151">Select the connection to the database you created in the first section, enter **BloggingContext** as the name of the connection string and click **Next**</span></span>

    ![Assistent Schritt 2](~/ef6/media/wizardstep2.png)

-   <span data-ttu-id="7b97d-153">Aktivieren Sie das Kontrollkästchen neben "Tables", um alle Tabellen zu importieren, und klicken Sie auf "Fertigstellen"</span><span class="sxs-lookup"><span data-stu-id="7b97d-153">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![Assistent Schritt 3](~/ef6/media/wizardstep3.png)

 

<span data-ttu-id="7b97d-155">Sobald der Reverse-Engineering-Prozess abgeschlossen ist, wird das neue Modell dem Projekt hinzugefügt und geöffnet, damit Sie es im Entity Framework Designer anzeigen können.</span><span class="sxs-lookup"><span data-stu-id="7b97d-155">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="7b97d-156">Außerdem wurde dem Projekt eine App.config Datei mit den Verbindungsdetails für die Datenbank hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="7b97d-156">An App.config file has also been added to your project with the connection details for the database.</span></span>

![Modell anfänglich](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="7b97d-158">Weitere Schritte in Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="7b97d-158">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="7b97d-159">Wenn Sie in Visual Studio 2010 arbeiten, müssen Sie einige zusätzliche Schritte ausführen, um ein Upgrade auf die neueste Version von Entity Framework durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="7b97d-159">If you are working in Visual Studio 2010 there are some additional steps you need to follow to upgrade to the latest version of Entity Framework.</span></span> <span data-ttu-id="7b97d-160">Das Upgrade ist wichtig, da Sie Ihnen Zugriff auf eine verbesserte API-Oberfläche, die viel einfacher zu verwenden ist, sowie auf die neuesten Fehlerbehebungen bietet.</span><span class="sxs-lookup"><span data-stu-id="7b97d-160">Upgrading is important because it gives you access to an improved API surface, that is much easier to use, as well as the latest bug fixes.</span></span>

<span data-ttu-id="7b97d-161">Zuerst müssen wir die neueste Version von Entity Framework von nuget erhalten.</span><span class="sxs-lookup"><span data-stu-id="7b97d-161">First up, we need to get the latest version of Entity Framework from NuGet.</span></span>

-   <span data-ttu-id="7b97d-162">**Project – &gt; Nuget-Pakete verwalten...** 
     *Wenn Sie nicht über die Option " **nuget-Pakete verwalten...** " verfügen, sollten Sie die [neueste Version von nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installieren.*</span><span class="sxs-lookup"><span data-stu-id="7b97d-162">**Project –&gt; Manage NuGet Packages…**
*If you don’t have the **Manage NuGet Packages…** option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)*</span></span>
-   <span data-ttu-id="7b97d-163">Auswählen der Registerkarte " **Online** "</span><span class="sxs-lookup"><span data-stu-id="7b97d-163">Select the **Online** tab</span></span>
-   <span data-ttu-id="7b97d-164">Auswählen des " **EntityFramework** "-Pakets</span><span class="sxs-lookup"><span data-stu-id="7b97d-164">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="7b97d-165">Klicken Sie auf **Install** (Installieren).</span><span class="sxs-lookup"><span data-stu-id="7b97d-165">Click **Install**</span></span>

<span data-ttu-id="7b97d-166">Als nächstes müssen wir das Modell austauschen, um Code zu generieren, der die dbcontext-API nutzt, die in späteren Versionen von Entity Framework eingeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="7b97d-166">Next, we need to swap our model to generate code that makes use of the DbContext API, which was introduced in later versions of Entity Framework.</span></span>

-   <span data-ttu-id="7b97d-167">Klicken Sie mit der rechten Maustaste auf eine leere Stelle des Modells im EF-Designer, und wählen Sie **Code Generierungs Element hinzufügen... aus.**</span><span class="sxs-lookup"><span data-stu-id="7b97d-167">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="7b97d-168">Wählen Sie im linken Menü **Online Vorlagen** aus, und suchen Sie nach **dbcontext** .</span><span class="sxs-lookup"><span data-stu-id="7b97d-168">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="7b97d-169">Wählen Sie den EF **5. x dbcontext Generator für \# C aus**, geben Sie **bloggingmodel** als Name ein, und klicken Sie auf **Hinzufügen** .</span><span class="sxs-lookup"><span data-stu-id="7b97d-169">Select the EF **5.x DbContext Generator for C\#**, enter **BloggingModel** as the name and click **Add**</span></span>

    ![Dbcontext-Vorlage](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a><span data-ttu-id="7b97d-171">4. lesen & schreiben von Daten</span><span class="sxs-lookup"><span data-stu-id="7b97d-171">4. Reading & Writing Data</span></span>

<span data-ttu-id="7b97d-172">Nachdem wir nun über ein Modell verfügen, ist es an der Zeit, es für den Zugriff auf einige Daten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="7b97d-172">Now that we have a model it’s time to use it to access some data.</span></span> <span data-ttu-id="7b97d-173">Die Klassen, die für den Zugriff auf Daten verwendet werden, werden automatisch für Sie basierend auf der EDMX-Datei generiert.</span><span class="sxs-lookup"><span data-stu-id="7b97d-173">The classes we are going to use to access data are being automatically generated for you based on the EDMX file.</span></span>

<span data-ttu-id="7b97d-174">*Dieser Screenshot ist von Visual Studio 2012. Wenn Sie Visual Studio 2010 verwenden, befinden sich die Dateien BloggingModel.TT und BloggingModel.Context.tt direkt unter Ihrem Projekt, anstatt Sie in der EDMX-Datei zu untersuchen.*</span><span class="sxs-lookup"><span data-stu-id="7b97d-174">*This screen shot is from Visual Studio 2012, if you are using Visual Studio 2010 the BloggingModel.tt and BloggingModel.Context.tt files will be directly under your project rather than nested under the EDMX file.*</span></span>

![Generierte Klassen (DF)](~/ef6/media/generatedclassesdf.png)

 

<span data-ttu-id="7b97d-176">Implementieren Sie die Main-Methode in Program.cs, wie unten gezeigt.</span><span class="sxs-lookup"><span data-stu-id="7b97d-176">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="7b97d-177">Mit diesem Code wird eine neue Instanz des Kontexts erstellt und anschließend verwendet, um einen neuen Blog einzufügen.</span><span class="sxs-lookup"><span data-stu-id="7b97d-177">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="7b97d-178">Anschließend wird eine LINQ-Abfrage verwendet, um alle Blogs aus der Datenbank alphabetisch nach Titel abzurufen.</span><span class="sxs-lookup"><span data-stu-id="7b97d-178">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

<span data-ttu-id="7b97d-179">Sie können die Anwendung jetzt ausführen und testen.</span><span class="sxs-lookup"><span data-stu-id="7b97d-179">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
 

## <a name="5-dealing-with-database-changes"></a><span data-ttu-id="7b97d-180">5. Umgang mit Daten Bank Änderungen</span><span class="sxs-lookup"><span data-stu-id="7b97d-180">5. Dealing with Database Changes</span></span>

<span data-ttu-id="7b97d-181">Nun ist es an der Zeit, Änderungen am Datenbankschema vorzunehmen, wenn wir diese Änderungen vornehmen, müssen wir auch unser Modell aktualisieren, um diese Änderungen widerzuspiegeln.</span><span class="sxs-lookup"><span data-stu-id="7b97d-181">Now it’s time to make some changes to our database schema, when we make these changes we also need to update our model to reflect those changes.</span></span>

<span data-ttu-id="7b97d-182">Der erste Schritt besteht darin, einige Änderungen am Datenbankschema vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="7b97d-182">The first step is to make some changes to the database schema.</span></span> <span data-ttu-id="7b97d-183">Wir fügen dem Schema eine Tabelle "Users" (Benutzer) hinzu.</span><span class="sxs-lookup"><span data-stu-id="7b97d-183">We’re going to add a Users table to the schema.</span></span>

-   <span data-ttu-id="7b97d-184">Klicken Sie in Server-Explorer mit der rechten Maustaste auf die Datenbank **databasefirst. Blog,** und wählen Sie **neue Abfrage** aus.</span><span class="sxs-lookup"><span data-stu-id="7b97d-184">Right-click on the **DatabaseFirst.Blogging** database in Server Explorer and select **New Query**</span></span>
-   <span data-ttu-id="7b97d-185">Kopieren Sie den folgenden SQL-Befehl in die neue Abfrage, klicken Sie mit der rechten Maustaste auf die Abfrage, und wählen Sie **Ausführen** .</span><span class="sxs-lookup"><span data-stu-id="7b97d-185">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

<span data-ttu-id="7b97d-186">Nachdem das Schema aktualisiert wurde, ist es an der Zeit, das Modell mit diesen Änderungen zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="7b97d-186">Now that the schema is updated, it’s time to update the model with those changes.</span></span>

-   <span data-ttu-id="7b97d-187">Klicken Sie mit der rechten Maustaste auf eine leere Stelle des Modells im EF-Designer, und wählen Sie "Modell aus Datenbank aktualisieren..." aus. Dadurch wird der Update-Assistent gestartet.</span><span class="sxs-lookup"><span data-stu-id="7b97d-187">Right-click on an empty spot of your model in the EF Designer and select ‘Update Model from Database…’, this will launch the Update Wizard</span></span>
-   <span data-ttu-id="7b97d-188">Aktivieren Sie auf der Registerkarte hinzufügen des Update-Assistenten das Kontrollkästchen neben Tabellen, was bedeutet, dass wir neue Tabellen aus dem Schema hinzufügen möchten.</span><span class="sxs-lookup"><span data-stu-id="7b97d-188">On the Add tab of the Update Wizard check the box next to Tables, this indicates that we want to add any new tables from the schema.</span></span>
    <span data-ttu-id="7b97d-189">*Auf der Registerkarte Aktualisieren werden alle vorhandenen Tabellen im Modell angezeigt, die während des Updates auf Änderungen geprüft werden. Auf den Registerkarten löschen werden alle Tabellen angezeigt, die aus dem Schema entfernt wurden. Sie werden auch als Teil des Updates aus dem Modell entfernt. Die Informationen auf diesen beiden Registerkarten werden automatisch erkannt und dienen nur zu Informationszwecken. Sie können keine Einstellungen ändern.*</span><span class="sxs-lookup"><span data-stu-id="7b97d-189">*The Refresh tab shows any existing tables in the model that will be checked for changes during the update. The Delete tabs show any tables that have been removed from the schema and will also be removed from the model as part of the update. The information on these two tabs is automatically detected and is provided for informational purposes only, you cannot change any settings.*</span></span>

    ![Aktualisierungs-Assistent](~/ef6/media/refreshwizard.png)

-   <span data-ttu-id="7b97d-191">Klicken Sie im Update-Assistenten auf Fertigstellen.</span><span class="sxs-lookup"><span data-stu-id="7b97d-191">Click Finish on the Update Wizard</span></span>

 

<span data-ttu-id="7b97d-192">Das Modell wird nun so aktualisiert, dass es eine neue Benutzer Entität enthält, die der Benutzertabelle zugeordnet ist, die der Datenbank hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="7b97d-192">The model is now updated to include a new User entity that maps to the Users table we added to the database.</span></span>

![Modell aktualisiert](~/ef6/media/modelupdated.png)

## <a name="summary"></a><span data-ttu-id="7b97d-194">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="7b97d-194">Summary</span></span>

<span data-ttu-id="7b97d-195">In dieser exemplarischen Vorgehensweise haben wir uns mit Database First Entwicklung beschäftigt, mit der wir ein Modell im EF-Designer basierend auf einer vorhandenen Datenbank erstellen konnten.</span><span class="sxs-lookup"><span data-stu-id="7b97d-195">In this walkthrough we looked at Database First development, which allowed us to create a model in the EF Designer based on an existing database.</span></span> <span data-ttu-id="7b97d-196">Anschließend haben wir dieses Modell verwendet, um Daten aus der Datenbank zu lesen und zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="7b97d-196">We then used that model to read and write some data from the database.</span></span> <span data-ttu-id="7b97d-197">Schließlich haben wir das Modell aktualisiert, um Änderungen widerzuspiegeln, die wir am Datenbankschema vorgenommen haben.</span><span class="sxs-lookup"><span data-stu-id="7b97d-197">Finally, we updated the model to reflect changes we made to the database schema.</span></span>
