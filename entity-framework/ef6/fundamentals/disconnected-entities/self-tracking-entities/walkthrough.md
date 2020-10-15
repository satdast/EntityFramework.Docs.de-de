---
title: 'Exemplarische Vorgehensweise zu Self-Tracking Entitäten: EF6'
description: Exemplarische Vorgehensweise für Entitäten mit selbst Nachverfolgung für Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough
ms.openlocfilehash: 7c50bbc9fad10a474728b03e79b685c549cf675d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065601"
---
# <a name="self-tracking-entities-walkthrough"></a><span data-ttu-id="73d2a-103">Exemplarische Vorgehensweise zu Self-Tracking Entities</span><span class="sxs-lookup"><span data-stu-id="73d2a-103">Self-Tracking Entities Walkthrough</span></span>
> [!IMPORTANT]
> <span data-ttu-id="73d2a-104">Es wird nicht mehr empfohlen, die Vorlage für Entitäten mit Selbstnachverfolgung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="73d2a-104">We no longer recommend using the self-tracking-entities template.</span></span> <span data-ttu-id="73d2a-105">Die Vorlage ist nur für die Unterstützung vorhandener Anwendungen weiterhin verfügbar.</span><span class="sxs-lookup"><span data-stu-id="73d2a-105">It will only continue to be available to support existing applications.</span></span> <span data-ttu-id="73d2a-106">Wenn für Ihre Anwendung die Arbeit mit getrennten Diagrammen von Entitäten erforderlich ist, sollten Sie daher Alternativen erwägen, wie z.B. [nachverfolgbare Entitäten](https://trackableentities.github.io/). Diese Technologie ähnelt den Entitäten mit Selbstnachverfolgung und wird von der Community aktiver entwickelt. Alternativ dazu können Sie auch benutzerdefinierten Code mithilfe von APIs auf niedriger Ebene zur Änderungsnachverfolgung schreiben.</span><span class="sxs-lookup"><span data-stu-id="73d2a-106">If your application requires working with disconnected graphs of entities, consider other alternatives such as [Trackable Entities](https://trackableentities.github.io/), which is a technology similar to Self-Tracking-Entities that is more actively developed by the community, or writing custom code using the low-level change tracking APIs.</span></span>

<span data-ttu-id="73d2a-107">Diese exemplarische Vorgehensweise veranschaulicht das Szenario, in dem ein Windows Communication Foundation (WCF)-Dienst einen Vorgang verfügbar macht, der ein Entitäts Diagramm zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="73d2a-107">This walkthrough demonstrates the scenario in which a Windows Communication Foundation (WCF) service exposes an operation that returns an entity graph.</span></span> <span data-ttu-id="73d2a-108">Anschließend bearbeitet eine Client Anwendung dieses Diagramm und übermittelt die Änderungen an einen Dienst Vorgang, der die Updates in einer Datenbank mithilfe Entity Framework überprüft und speichert.</span><span class="sxs-lookup"><span data-stu-id="73d2a-108">Next, a client application manipulates that graph and submits the modifications to a service operation that validates and saves the updates to a database using Entity Framework.</span></span>

<span data-ttu-id="73d2a-109">Bevor Sie diese exemplarische Vorgehensweise durcharbeiten, sollten Sie die Seite [Entitäten mit selbst Nachverfolgung](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index) lesen.</span><span class="sxs-lookup"><span data-stu-id="73d2a-109">Before completing this walkthrough make sure you read the [Self-Tracking Entities](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index) page.</span></span>

<span data-ttu-id="73d2a-110">Diese exemplarische Vorgehensweise umfasst die folgenden Aktionen:</span><span class="sxs-lookup"><span data-stu-id="73d2a-110">This walkthrough completes the following actions:</span></span>

-   <span data-ttu-id="73d2a-111">Erstellt eine Datenbank, auf die zugegriffen werden soll.</span><span class="sxs-lookup"><span data-stu-id="73d2a-111">Creates a database to access.</span></span>
-   <span data-ttu-id="73d2a-112">Erstellt eine Klassenbibliothek, die das Modell enthält.</span><span class="sxs-lookup"><span data-stu-id="73d2a-112">Creates a class library that contains the model.</span></span>
-   <span data-ttu-id="73d2a-113">Tauscht die Vorlage für den Self-Tracking Entity Generator aus.</span><span class="sxs-lookup"><span data-stu-id="73d2a-113">Swaps to the Self-Tracking Entity Generator template.</span></span>
-   <span data-ttu-id="73d2a-114">Verschiebt die Entitäts Klassen in ein separates Projekt.</span><span class="sxs-lookup"><span data-stu-id="73d2a-114">Moves the entity classes to a separate project.</span></span>
-   <span data-ttu-id="73d2a-115">Erstellt einen WCF-Dienst, der Vorgänge zum Abfragen und Speichern von Entitäten verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="73d2a-115">Creates a WCF service that exposes operations to query and save entities.</span></span>
-   <span data-ttu-id="73d2a-116">Erstellt Client Anwendungen (Konsole und WPF), die den-Dienst nutzen.</span><span class="sxs-lookup"><span data-stu-id="73d2a-116">Creates client applications (Console and WPF) that consume the service.</span></span>

<span data-ttu-id="73d2a-117">Wir verwenden Database First in dieser exemplarischen Vorgehensweise, aber dieselben Verfahren gelten auch für Model First.</span><span class="sxs-lookup"><span data-stu-id="73d2a-117">We'll use Database First in this walkthrough but the same techniques apply equally to Model First.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="73d2a-118">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="73d2a-118">Pre-Requisites</span></span>

<span data-ttu-id="73d2a-119">Um diese exemplarische Vorgehensweise abzuschließen, benötigen Sie eine aktuelle Version von Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="73d2a-119">To complete this walkthrough you will need a recent version of Visual Studio.</span></span>

## <a name="create-a-database"></a><span data-ttu-id="73d2a-120">Erstellen einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="73d2a-120">Create a Database</span></span>

<span data-ttu-id="73d2a-121">Der Datenbankserver, der mit Visual Studio installiert wird, unterscheidet sich abhängig von der installierten Version von Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="73d2a-121">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="73d2a-122">Wenn Sie Visual Studio 2012 verwenden, erstellen Sie eine localdb-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="73d2a-122">If you are using Visual Studio 2012 then you'll be creating a LocalDB database.</span></span>
-   <span data-ttu-id="73d2a-123">Wenn Sie Visual Studio 2010 verwenden, erstellen Sie eine SQL Express-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="73d2a-123">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>

<span data-ttu-id="73d2a-124">Nun generieren wir die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="73d2a-124">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="73d2a-125">Öffnen Sie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="73d2a-125">Open Visual Studio</span></span>
-   <span data-ttu-id="73d2a-126">**Ansicht- &gt; Server-Explorer**</span><span class="sxs-lookup"><span data-stu-id="73d2a-126">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="73d2a-127">Klicken Sie mit der rechten Maustaste auf **Datenverbindungen- &gt; Verbindung hinzufügen...**</span><span class="sxs-lookup"><span data-stu-id="73d2a-127">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="73d2a-128">Wenn Sie über Server-Explorer keine Verbindung mit einer Datenbank hergestellt haben, müssen Sie **Microsoft SQL Server** als Datenquelle auswählen.</span><span class="sxs-lookup"><span data-stu-id="73d2a-128">If you haven’t connected to a database from Server Explorer before you’ll need to select **Microsoft SQL Server** as the data source</span></span>
-   <span data-ttu-id="73d2a-129">Stellen Sie eine Verbindung mit localdb oder SQL Express her, je nachdem, welche installiert wurde.</span><span class="sxs-lookup"><span data-stu-id="73d2a-129">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>
-   <span data-ttu-id="73d2a-130">Geben Sie **stesample** als Datenbanknamen ein.</span><span class="sxs-lookup"><span data-stu-id="73d2a-130">Enter **STESample** as the database name</span></span>
-   <span data-ttu-id="73d2a-131">Wählen Sie **OK** aus, und Sie werden gefragt, ob Sie eine neue Datenbank erstellen möchten, und wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="73d2a-131">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>
-   <span data-ttu-id="73d2a-132">Die neue Datenbank wird nun in Server-Explorer</span><span class="sxs-lookup"><span data-stu-id="73d2a-132">The new database will now appear in Server Explorer</span></span>
-   <span data-ttu-id="73d2a-133">Wenn Sie Visual Studio 2012 verwenden</span><span class="sxs-lookup"><span data-stu-id="73d2a-133">If you are using Visual Studio 2012</span></span>
    -   <span data-ttu-id="73d2a-134">Klicken Sie im Server-Explorer mit der rechten Maustaste auf die Datenbank, und wählen Sie **Neue Abfrage** aus.</span><span class="sxs-lookup"><span data-stu-id="73d2a-134">Right-click on the database in Server Explorer and select **New Query**</span></span>
    -   <span data-ttu-id="73d2a-135">Kopieren Sie den folgenden SQL-Befehl in die neue Abfrage, klicken Sie mit der rechten Maustaste auf die Abfrage, und wählen Sie **Ausführen** .</span><span class="sxs-lookup"><span data-stu-id="73d2a-135">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>
-   <span data-ttu-id="73d2a-136">Wenn Sie Visual Studio 2010 verwenden</span><span class="sxs-lookup"><span data-stu-id="73d2a-136">If you are using Visual Studio 2010</span></span>
    -   <span data-ttu-id="73d2a-137">Wählen Sie **Data- &gt; Transact SQL-Editor- &gt; neue Abfrage Verbindung...**</span><span class="sxs-lookup"><span data-stu-id="73d2a-137">Select **Data -&gt; Transact SQL Editor -&gt; New Query Connection...**</span></span>
    -   <span data-ttu-id="73d2a-138">Geben Sie ein **. \\ SQLExpress** als Servername, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="73d2a-138">Enter **.\\SQLEXPRESS** as the server name and click **OK**</span></span>
    -   <span data-ttu-id="73d2a-139">Wählen Sie in der Dropdown-Datei am oberen Rand des Abfrage-Editors die **stesample** -Datenbank aus.</span><span class="sxs-lookup"><span data-stu-id="73d2a-139">Select the **STESample** database from the drop down at the top of the query editor</span></span>
    -   <span data-ttu-id="73d2a-140">Kopieren Sie das folgende SQL in die neue Abfrage, klicken Sie mit der rechten Maustaste auf die Abfrage, und wählen Sie **SQL ausführen** aus.</span><span class="sxs-lookup"><span data-stu-id="73d2a-140">Copy the following SQL into the new query, then right-click on the query and select **Execute SQL**</span></span>

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

    SET IDENTITY_INSERT [dbo].[Blogs] ON
    INSERT INTO [dbo].[Blogs] ([BlogId], [Name], [Url]) VALUES (1, N'ADO.NET Blog', N'blogs.msdn.com/adonet')
    SET IDENTITY_INSERT [dbo].[Blogs] OFF
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'Intro to EF', N'Interesting stuff...', 1)
    INSERT INTO [dbo].[Posts] ([Title], [Content], [BlogId]) VALUES (N'What is New', N'More interesting stuff...', 1)
```

## <a name="create-the-model"></a><span data-ttu-id="73d2a-141">Erstellen des Modells</span><span class="sxs-lookup"><span data-stu-id="73d2a-141">Create the Model</span></span>

<span data-ttu-id="73d2a-142">Zuerst wird ein Projekt benötigt, in das das Modell eingefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="73d2a-142">First up, we need a project to put the model in.</span></span>

-   <span data-ttu-id="73d2a-143">**Datei- &gt; New- &gt; Project...**</span><span class="sxs-lookup"><span data-stu-id="73d2a-143">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="73d2a-144">Wählen Sie im linken Bereich \*\*Visual C \# \*\* und dann **Klassenbibliothek** aus.</span><span class="sxs-lookup"><span data-stu-id="73d2a-144">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="73d2a-145">Geben Sie **stesample** als Name ein, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="73d2a-145">Enter **STESample** as the name and click **OK**</span></span>

<span data-ttu-id="73d2a-146">Im EF-Designer erstellen wir nun ein einfaches Modell für den Zugriff auf unsere Datenbank:</span><span class="sxs-lookup"><span data-stu-id="73d2a-146">Now we'll create a simple model in the EF Designer to access our database:</span></span>

-   <span data-ttu-id="73d2a-147">**Project- &gt; Neues Element hinzufügen...**</span><span class="sxs-lookup"><span data-stu-id="73d2a-147">**Project -&gt; Add New Item...**</span></span>
-   <span data-ttu-id="73d2a-148">Wählen Sie im linken Bereich **Daten** aus, und klicken Sie dann auf **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="73d2a-148">Select **Data** from the left pane and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="73d2a-149">Geben Sie als Name **bloggingmodel** ein, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="73d2a-149">Enter **BloggingModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="73d2a-150">Wählen Sie **aus Datenbank generieren aus** , und klicken Sie auf **weiter**</span><span class="sxs-lookup"><span data-stu-id="73d2a-150">Select **Generate from database** and click **Next**</span></span>
-   <span data-ttu-id="73d2a-151">Geben Sie die Verbindungsinformationen für die Datenbank ein, die Sie im vorherigen Abschnitt erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="73d2a-151">Enter the connection information for the database that you created in the previous section</span></span>
-   <span data-ttu-id="73d2a-152">Geben Sie **bloggingcontext** als Namen für die Verbindungs Zeichenfolge ein, und klicken Sie auf **weiter** .</span><span class="sxs-lookup"><span data-stu-id="73d2a-152">Enter **BloggingContext** as the name for the connection string and click **Next**</span></span>
-   <span data-ttu-id="73d2a-153">Aktivieren Sie das Kontrollkästchen neben **Tabellen** und klicken Sie auf **Fertig** stellen.</span><span class="sxs-lookup"><span data-stu-id="73d2a-153">Check the box next to **Tables** and click **Finish**</span></span>

## <a name="swap-to-ste-code-generation"></a><span data-ttu-id="73d2a-154">An ste-Code Generierung austauschen</span><span class="sxs-lookup"><span data-stu-id="73d2a-154">Swap to STE Code Generation</span></span>

<span data-ttu-id="73d2a-155">Nun müssen Sie die Standard Codegenerierung und den Austausch in Self-Tracking Entitäten deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="73d2a-155">Now we need to disable the default code generation and swap to Self-Tracking Entities.</span></span>

### <a name="if-you-are-using-visual-studio-2012"></a><span data-ttu-id="73d2a-156">Wenn Sie Visual Studio 2012 verwenden</span><span class="sxs-lookup"><span data-stu-id="73d2a-156">If you are using Visual Studio 2012</span></span>

-   <span data-ttu-id="73d2a-157">Erweitern Sie in **Projektmappen-Explorer** den **Eintrag bloggingmodel. edmx** , und löschen Sie die **BloggingModel.tt** und **BloggingModel.Context.tt** 
     *dadurch wird die Standard Codegenerierung deaktiviert* .</span><span class="sxs-lookup"><span data-stu-id="73d2a-157">Expand **BloggingModel.edmx** in **Solution Explorer** and delete the **BloggingModel.tt** and **BloggingModel.Context.tt**
*This will disable the default code generation*</span></span>
-   <span data-ttu-id="73d2a-158">Klicken Sie mit der rechten Maustaste auf einen leeren Bereich auf der EF-Designer Oberfläche, und wählen Sie **Code Generierungs Element hinzufügen aus.**</span><span class="sxs-lookup"><span data-stu-id="73d2a-158">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="73d2a-159">Wählen Sie im linken Bereich **Online** aus, und suchen Sie nach **ste Generator** .</span><span class="sxs-lookup"><span data-stu-id="73d2a-159">Select **Online** from the left pane and search for **STE Generator**</span></span>
-   <span data-ttu-id="73d2a-160">Wählen Sie die Vorlage **ste Generator \# für C aus** , geben Sie **stetemplate** als Namen ein, und klicken Sie auf **Hinzufügen**</span><span class="sxs-lookup"><span data-stu-id="73d2a-160">Select the **STE Generator for C\#** template, enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="73d2a-161">Die Dateien **STETemplate.tt** und **STETemplate.Context.tt** werden unter der Datei bloggingmodel. edmx hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="73d2a-161">The **STETemplate.tt** and **STETemplate.Context.tt** files are added nested under the BloggingModel.edmx file</span></span>

### <a name="if-you-are-using-visual-studio-2010"></a><span data-ttu-id="73d2a-162">Wenn Sie Visual Studio 2010 verwenden</span><span class="sxs-lookup"><span data-stu-id="73d2a-162">If you are using Visual Studio 2010</span></span>

-   <span data-ttu-id="73d2a-163">Klicken Sie mit der rechten Maustaste auf einen leeren Bereich auf der EF-Designer Oberfläche, und wählen Sie **Code Generierungs Element hinzufügen aus.**</span><span class="sxs-lookup"><span data-stu-id="73d2a-163">Right-click an empty area on the EF Designer surface and select **Add Code Generation Item...**</span></span>
-   <span data-ttu-id="73d2a-164">Wählen Sie im linken Bereich **Code** aus, und klicken Sie dann auf **ADO.net Self-Tracking Entity Generator**</span><span class="sxs-lookup"><span data-stu-id="73d2a-164">Select **Code** from the left pane and then **ADO.NET Self-Tracking Entity Generator**</span></span>
-   <span data-ttu-id="73d2a-165">Geben Sie **stetemplate** als Name ein, und klicken Sie auf **Hinzufügen**</span><span class="sxs-lookup"><span data-stu-id="73d2a-165">Enter **STETemplate** as the name and click **Add**</span></span>
-   <span data-ttu-id="73d2a-166">Die Dateien **STETemplate.tt** und **STETemplate.Context.tt** werden direkt dem Projekt hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="73d2a-166">The **STETemplate.tt** and **STETemplate.Context.tt** files are added directly to your project</span></span>

## <a name="move-entity-types-into-separate-project"></a><span data-ttu-id="73d2a-167">Verschieben von Entitäts Typen in ein separates Projekt</span><span class="sxs-lookup"><span data-stu-id="73d2a-167">Move Entity Types into Separate Project</span></span>

<span data-ttu-id="73d2a-168">Um Self-Tracking Entitäten verwenden zu können, benötigt unsere Client Anwendung Zugriff auf die vom Modell generierten Entitäts Klassen.</span><span class="sxs-lookup"><span data-stu-id="73d2a-168">To use Self-Tracking Entities our client application needs access to the entity classes generated from our model.</span></span> <span data-ttu-id="73d2a-169">Da wir das gesamte Modell nicht für die Client Anwendung verfügbar machen möchten, verschieben wir die Entitäts Klassen in ein separates Projekt.</span><span class="sxs-lookup"><span data-stu-id="73d2a-169">Because we don't want to expose the whole model to the client application we're going to move the entity classes into a separate project.</span></span>

<span data-ttu-id="73d2a-170">Im ersten Schritt wird das Erstellen von Entitäts Klassen im vorhandenen Projekt beendet:</span><span class="sxs-lookup"><span data-stu-id="73d2a-170">The first step is to stop generating entity classes in the existing project:</span></span>

-   <span data-ttu-id="73d2a-171">Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf **STETemplate.tt** , und wählen Sie **Eigenschaften**</span><span class="sxs-lookup"><span data-stu-id="73d2a-171">Right-click on **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="73d2a-172">Löschen Sie im **Eigenschaften** Fenster **TextTemplatingFileGenerator** aus der **CustomTool** -Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="73d2a-172">In the **Properties** window clear **TextTemplatingFileGenerator** from the **CustomTool** property</span></span>
-   <span data-ttu-id="73d2a-173">Erweitern Sie in **Projektmappen-Explorer** den Eintrag **STETemplate.tt** , und löschen Sie alle Dateien, die darin gespeichert sind</span><span class="sxs-lookup"><span data-stu-id="73d2a-173">Expand **STETemplate.tt** in **Solution Explorer** and delete all files nested under it</span></span>

<span data-ttu-id="73d2a-174">Als Nächstes fügen wir ein neues Projekt hinzu und generieren die Entitäts Klassen darin.</span><span class="sxs-lookup"><span data-stu-id="73d2a-174">Next, we are going to add a new project and generate the entity classes in it</span></span>

-   <span data-ttu-id="73d2a-175">**Datei- &gt; Add- &gt; Project...**</span><span class="sxs-lookup"><span data-stu-id="73d2a-175">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="73d2a-176">Wählen Sie im linken Bereich \*\*Visual C \# \*\* und dann **Klassenbibliothek** aus.</span><span class="sxs-lookup"><span data-stu-id="73d2a-176">Select **Visual C\#** from the left pane and then **Class Library**</span></span>
-   <span data-ttu-id="73d2a-177">Geben Sie **stesample. Entities** als Name ein, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="73d2a-177">Enter **STESample.Entities** as the name and click **OK**</span></span>
-   <span data-ttu-id="73d2a-178">**Project- &gt; Vorhandenes Element hinzufügen...**</span><span class="sxs-lookup"><span data-stu-id="73d2a-178">**Project -&gt; Add Existing Item...**</span></span>
-   <span data-ttu-id="73d2a-179">Navigieren Sie zum Projektordner " **stesample** ".</span><span class="sxs-lookup"><span data-stu-id="73d2a-179">Navigate to the **STESample** project folder</span></span>
-   <span data-ttu-id="73d2a-180">Wählen Sie diese Option aus, um \*\*alle Dateien anzuzeigen \* . \* \*\*</span><span class="sxs-lookup"><span data-stu-id="73d2a-180">Select to view **All Files (\*.\*)**</span></span>
-   <span data-ttu-id="73d2a-181">Wählen Sie die Datei **STETemplate.tt** aus.</span><span class="sxs-lookup"><span data-stu-id="73d2a-181">Select the **STETemplate.tt** file</span></span>
-   <span data-ttu-id="73d2a-182">Klicken Sie auf den Dropdown Pfeil neben der Schaltfläche **Hinzufügen** , und wählen Sie **als Link hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="73d2a-182">Click on the drop down arrow next to the **Add** button and select **Add As Link**</span></span>

    ![Verknüpfte Vorlage hinzufügen](~/ef6/media/addlinkedtemplate.png)

<span data-ttu-id="73d2a-184">Wir stellen auch sicher, dass die Entitäts Klassen im gleichen Namespace wie der Kontext generiert werden.</span><span class="sxs-lookup"><span data-stu-id="73d2a-184">We're also going to make sure the entity classes get generated in the same namespace as the context.</span></span> <span data-ttu-id="73d2a-185">Dadurch wird lediglich die Anzahl der using-Anweisungen reduziert, die wir in der gesamten Anwendung hinzufügen müssen.</span><span class="sxs-lookup"><span data-stu-id="73d2a-185">This just reduces the number of using statements we need to add throughout our application.</span></span>

-   <span data-ttu-id="73d2a-186">Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf die verknüpfte **STETemplate.tt** , und wählen Sie **Eigenschaften**</span><span class="sxs-lookup"><span data-stu-id="73d2a-186">Right-click on the linked **STETemplate.tt** in **Solution Explorer** and select **Properties**</span></span>
-   <span data-ttu-id="73d2a-187">Legen Sie im **Eigenschaften** Fenster **benutzerdefinierter Tool Namespace** auf **stesample** fest.</span><span class="sxs-lookup"><span data-stu-id="73d2a-187">In the **Properties** window set **Custom Tool Namespace** to **STESample**</span></span>

<span data-ttu-id="73d2a-188">Der von der ste-Vorlage generierte Code benötigt einen Verweis auf " **System. Runtime. Serialization** ", damit er kompiliert werden kann.</span><span class="sxs-lookup"><span data-stu-id="73d2a-188">The code generated by the STE template will need a reference to **System.Runtime.Serialization** in order to compile.</span></span> <span data-ttu-id="73d2a-189">Diese Bibliothek wird für die WCF **DataContract** -und **DataMember** -Attribute benötigt, die für die serialisierbaren Entitäts Typen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="73d2a-189">This library is needed for the WCF **DataContract** and **DataMember** attributes that are used on the serializable entity types.</span></span>

-   <span data-ttu-id="73d2a-190">Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **stesample. Entities** , und wählen Sie **Verweis hinzufügen...**</span><span class="sxs-lookup"><span data-stu-id="73d2a-190">Right click on the **STESample.Entities** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="73d2a-191">Aktivieren Sie in Visual Studio 2012 das Kontrollkästchen neben **System. Runtime. Serialization** , und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="73d2a-191">In Visual Studio 2012 - check the box next to **System.Runtime.Serialization** and click **OK**</span></span>
    -   <span data-ttu-id="73d2a-192">Wählen Sie in Visual Studio 2010 die Option **System. Runtime. Serialization** aus, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="73d2a-192">In Visual Studio 2010 - select **System.Runtime.Serialization** and click **OK**</span></span>

<span data-ttu-id="73d2a-193">Schließlich benötigt das Projekt mit unserem Kontext darin einen Verweis auf die Entitäts Typen.</span><span class="sxs-lookup"><span data-stu-id="73d2a-193">Finally, the project with our context in it will need a reference to the entity types.</span></span>

-   <span data-ttu-id="73d2a-194">Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **stesample** , und wählen Sie **Verweis hinzufügen aus.**</span><span class="sxs-lookup"><span data-stu-id="73d2a-194">Right click on the **STESample** project in **Solution Explorer** and select **Add Reference...**</span></span>
    -   <span data-ttu-id="73d2a-195">Wählen Sie in Visual Studio 2012 im linken Bereich Projekt Mappe aus, **Aktivieren Sie das** Kontrollkästchen neben **stesample. Entities** , und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="73d2a-195">In Visual Studio 2012 - select **Solution** from the left pane, check the box next to **STESample.Entities** and click **OK**</span></span>
    -   <span data-ttu-id="73d2a-196">Wählen Sie in Visual Studio 2010 die Registerkarte **Projekte** aus, wählen Sie **stesample. Entities** aus, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="73d2a-196">In Visual Studio 2010 - select the **Projects** tab, select **STESample.Entities** and click **OK**</span></span>

>[!NOTE]
> <span data-ttu-id="73d2a-197">Eine weitere Möglichkeit zum Verschieben der Entitäts Typen in ein separates Projekt besteht darin, die Vorlagen Datei zu verschieben, anstatt Sie von Ihrem Standard Speicherort zu verknüpfen.</span><span class="sxs-lookup"><span data-stu-id="73d2a-197">Another option for moving the entity types to a separate project is to move the template file, rather than linking it from its default location.</span></span> <span data-ttu-id="73d2a-198">Wenn Sie dies tun, müssen Sie die Variable " **inputFile** " in der Vorlage aktualisieren, um den relativen Pfad zur EDMX-Datei bereitzustellen (in diesem Beispiel: **. \\ Bloggingmodel. edmx**).</span><span class="sxs-lookup"><span data-stu-id="73d2a-198">If you do this, you will need to update the **inputFile** variable in the template to provide the relative path to the edmx file (in this example that would be **..\\BloggingModel.edmx**).</span></span>

## <a name="create-a-wcf-service"></a><span data-ttu-id="73d2a-199">Erstellen eines WCF-Dienstanbieter</span><span class="sxs-lookup"><span data-stu-id="73d2a-199">Create a WCF Service</span></span>

<span data-ttu-id="73d2a-200">Nun ist es an der Zeit, einen WCF-Dienst hinzuzufügen, um unsere Daten verfügbar zu machen. wir beginnen mit der Erstellung des Projekts.</span><span class="sxs-lookup"><span data-stu-id="73d2a-200">Now it's time to add a WCF Service to expose our data, we'll start by creating the project.</span></span>

-   <span data-ttu-id="73d2a-201">**Datei- &gt; Add- &gt; Project...**</span><span class="sxs-lookup"><span data-stu-id="73d2a-201">**File -&gt; Add -&gt; Project...**</span></span>
-   <span data-ttu-id="73d2a-202">Wählen Sie im linken Bereich \*\*Visual C \# \*\* und dann **WCF-Dienst Anwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="73d2a-202">Select **Visual C\#** from the left pane and then **WCF Service Application**</span></span>
-   <span data-ttu-id="73d2a-203">Geben Sie **stesample. Service** als Name ein, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="73d2a-203">Enter **STESample.Service** as the name and click **OK**</span></span>
-   <span data-ttu-id="73d2a-204">Fügen Sie einen Verweis auf die **System. Data. Entity** -Assembly hinzu.</span><span class="sxs-lookup"><span data-stu-id="73d2a-204">Add a reference to the **System.Data.Entity** assembly</span></span>
-   <span data-ttu-id="73d2a-205">Hinzufügen eines Verweises auf die Projekte " **stesample** " und " **stesample. Entities** "</span><span class="sxs-lookup"><span data-stu-id="73d2a-205">Add a reference to the **STESample** and **STESample.Entities** projects</span></span>

<span data-ttu-id="73d2a-206">Wir müssen die EF-Verbindungs Zeichenfolge in dieses Projekt kopieren, damit Sie zur Laufzeit gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="73d2a-206">We need to copy the EF connection string to this project so that it is found at runtime.</span></span>

-   <span data-ttu-id="73d2a-207">Öffnen Sie die **App.Config** -Datei für das **stesample **-Projekt, und kopieren Sie das **connectionStrings** -Element.</span><span class="sxs-lookup"><span data-stu-id="73d2a-207">Open the **App.Config** file for the **STESample **project and copy the **connectionStrings** element</span></span>
-   <span data-ttu-id="73d2a-208">Fügen Sie das **connectionStrings** -Element als untergeordnetes Element des **Configuration** -Elements der **Web.Config** Datei im **stesample. Service** -Projekt ein.</span><span class="sxs-lookup"><span data-stu-id="73d2a-208">Paste the **connectionStrings** element as a child element of the **configuration** element of the **Web.Config** file in the **STESample.Service** project</span></span>

<span data-ttu-id="73d2a-209">Nun ist es an der Zeit, den eigentlichen Dienst zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="73d2a-209">Now it's time to implement the actual service.</span></span>

-   <span data-ttu-id="73d2a-210">Öffnen Sie **IService1.cs** , und ersetzen Sie den Inhalt durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="73d2a-210">Open **IService1.cs** and replace the contents with the following code</span></span>

``` csharp
    using System.Collections.Generic;
    using System.ServiceModel;

    namespace STESample.Service
    {
        [ServiceContract]
        public interface IService1
        {
            [OperationContract]
            List<Blog> GetBlogs();

            [OperationContract]
            void UpdateBlog(Blog blog);
        }
    }
```

-   <span data-ttu-id="73d2a-211">Öffnen Sie **Service1. svc** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="73d2a-211">Open **Service1.svc** and replace the contents with the following code</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.Linq;

    namespace STESample.Service
    {
        public class Service1 : IService1
        {
            /// <summary>
            /// Gets all the Blogs and related Posts.
            /// </summary>
            public List<Blog> GetBlogs()
            {
                using (BloggingContext context = new BloggingContext())
                {
                    return context.Blogs.Include("Posts").ToList();
                }
            }

            /// <summary>
            /// Updates Blog and its related Posts.
            /// </summary>
            public void UpdateBlog(Blog blog)
            {
                using (BloggingContext context = new BloggingContext())
                {
                    try
                    {
                        // TODO: Perform validation on the updated order before applying the changes.

                        // The ApplyChanges method examines the change tracking information
                        // contained in the graph of self-tracking entities to infer the set of operations
                        // that need to be performed to reflect the changes in the database.
                        context.Blogs.ApplyChanges(blog);
                        context.SaveChanges();

                    }
                    catch (UpdateException)
                    {
                        // To avoid propagating exception messages that contain sensitive data to the client tier
                        // calls to ApplyChanges and SaveChanges should be wrapped in exception handling code.
                        throw new InvalidOperationException("Failed to update. Try your request again.");
                    }
                }
            }        
        }
    }
```

## <a name="consume-the-service-from-a-console-application"></a><span data-ttu-id="73d2a-212">Nutzen des dienstanwendungs aus einer Konsolenanwendung</span><span class="sxs-lookup"><span data-stu-id="73d2a-212">Consume the Service from a Console Application</span></span>

<span data-ttu-id="73d2a-213">Wir erstellen eine Konsolenanwendung, die unseren Dienst verwendet.</span><span class="sxs-lookup"><span data-stu-id="73d2a-213">Let's create a console application that uses our service.</span></span>

-   <span data-ttu-id="73d2a-214">**Datei- &gt; New- &gt; Project...**</span><span class="sxs-lookup"><span data-stu-id="73d2a-214">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="73d2a-215">Wählen Sie im linken Bereich \*\*Visual C \# \*\* und dann **Konsolenanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="73d2a-215">Select **Visual C\#** from the left pane and then **Console Application**</span></span>
-   <span data-ttu-id="73d2a-216">Geben Sie **stesample. consoletest** als Name ein, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="73d2a-216">Enter **STESample.ConsoleTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="73d2a-217">Hinzufügen eines Verweises auf das Projekt " **stesample. Entities** "</span><span class="sxs-lookup"><span data-stu-id="73d2a-217">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="73d2a-218">Wir benötigen einen Dienst Verweis auf den WCF-Dienst.</span><span class="sxs-lookup"><span data-stu-id="73d2a-218">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="73d2a-219">Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **stesample. consoletest** , und wählen Sie **Dienstverweis hinzufügen...**</span><span class="sxs-lookup"><span data-stu-id="73d2a-219">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="73d2a-220">Klicken Sie auf **Ermitteln.**</span><span class="sxs-lookup"><span data-stu-id="73d2a-220">Click **Discover**</span></span>
-   <span data-ttu-id="73d2a-221">Geben Sie **bloggingservice** als Namespace ein, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="73d2a-221">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="73d2a-222">Nun können wir Code schreiben, um den Dienst zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="73d2a-222">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="73d2a-223">Öffnen Sie **Program.cs** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="73d2a-223">Open **Program.cs** and replace the contents with the following code.</span></span>

``` csharp
    using STESample.ConsoleTest.BloggingService;
    using System;
    using System.Linq;

    namespace STESample.ConsoleTest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Print out the data before we change anything
                Console.WriteLine("Initial Data:");
                DisplayBlogsAndPosts();

                // Add a new Blog and some Posts
                AddBlogAndPost();
                Console.WriteLine("After Adding:");
                DisplayBlogsAndPosts();

                // Modify the Blog and one of its Posts
                UpdateBlogAndPost();
                Console.WriteLine("After Update:");
                DisplayBlogsAndPosts();

                // Delete the Blog and its Posts
                DeleteBlogAndPost();
                Console.WriteLine("After Delete:");
                DisplayBlogsAndPosts();

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            static void DisplayBlogsAndPosts()
            {
                using (var service = new Service1Client())
                {
                    // Get all Blogs (and Posts) from the service
                    // and print them to the console
                    var blogs = service.GetBlogs();
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(blog.Name);
                        foreach (var post in blog.Posts)
                        {
                            Console.WriteLine(" - {0}", post.Title);
                        }
                    }
                }

                Console.WriteLine();
                Console.WriteLine();
            }

            static void AddBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Create a new Blog with a couple of Posts
                    var newBlog = new Blog
                    {
                        Name = "The New Blog",
                        Posts =
                        {
                            new Post { Title = "Welcome to the new blog"},
                            new Post { Title = "What's new on the new blog"}
                        }
                    };

                    // Save the changes using the service
                    service.UpdateBlog(newBlog);
                }
            }

            static void UpdateBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The New Blog
                    var blog = blogs.First(b => b.Name == "The New Blog");

                    // Update the Blogs name
                    blog.Name = "The Not-So-New Blog";

                    // Update one of the related posts
                    blog.Posts.First().Content = "Some interesting content...";

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }

            static void DeleteBlogAndPost()
            {
                using (var service = new Service1Client())
                {
                    // Get all the Blogs
                    var blogs = service.GetBlogs();

                    // Use LINQ to Objects to find The Not-So-New Blog
                    var blog = blogs.First(b => b.Name == "The Not-So-New Blog");

                    // Mark all related Posts for deletion
                    // We need to call ToList because each Post will be removed from the
                    // Posts collection when we call MarkAsDeleted
                    foreach (var post in blog.Posts.ToList())
                    {
                        post.MarkAsDeleted();
                    }

                    // Mark the Blog for deletion
                    blog.MarkAsDeleted();

                    // Save the changes using the service
                    service.UpdateBlog(blog);
                }
            }
        }
    }
```

<span data-ttu-id="73d2a-224">Sie können die Anwendung jetzt ausführen, um sie in Aktion zu sehen.</span><span class="sxs-lookup"><span data-stu-id="73d2a-224">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="73d2a-225">Klicken Sie mit der rechten Maustaste auf das Projekt **stesample. consoletest** in **Projektmappen-Explorer** und wählen Sie **Debuggen &gt; neue Instanz starten** aus.</span><span class="sxs-lookup"><span data-stu-id="73d2a-225">Right-click the **STESample.ConsoleTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>

<span data-ttu-id="73d2a-226">Die folgende Ausgabe wird angezeigt, wenn die Anwendung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="73d2a-226">You'll see the following output when the application executes.</span></span>

```console
Initial Data:
ADO.NET Blog
- Intro to EF
- What is New

After Adding:
ADO.NET Blog
- Intro to EF
- What is New
The New Blog
- Welcome to the new blog
- What's new on the new blog

After Update:
ADO.NET Blog
- Intro to EF
- What is New
The Not-So-New Blog
- Welcome to the new blog
- What's new on the new blog

After Delete:
ADO.NET Blog
- Intro to EF
- What is New

Press any key to exit...
```

## <a name="consume-the-service-from-a-wpf-application"></a><span data-ttu-id="73d2a-227">Nutzen des dienstanwendungs aus einer WPF-Anwendung</span><span class="sxs-lookup"><span data-stu-id="73d2a-227">Consume the Service from a WPF Application</span></span>

<span data-ttu-id="73d2a-228">Erstellen Sie eine WPF-Anwendung, die unseren Dienst verwendet.</span><span class="sxs-lookup"><span data-stu-id="73d2a-228">Let's create a WPF application that uses our service.</span></span>

-   <span data-ttu-id="73d2a-229">**Datei- &gt; New- &gt; Project...**</span><span class="sxs-lookup"><span data-stu-id="73d2a-229">**File -&gt; New -&gt; Project...**</span></span>
-   <span data-ttu-id="73d2a-230">Wählen Sie im linken Bereich \*\*Visual C \# \*\* und dann **WPF-Anwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="73d2a-230">Select **Visual C\#** from the left pane and then **WPF Application**</span></span>
-   <span data-ttu-id="73d2a-231">Geben Sie **stesample. wpftest** als Name ein, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="73d2a-231">Enter **STESample.WPFTest** as the name and click **OK**</span></span>
-   <span data-ttu-id="73d2a-232">Hinzufügen eines Verweises auf das Projekt " **stesample. Entities** "</span><span class="sxs-lookup"><span data-stu-id="73d2a-232">Add a reference to the **STESample.Entities** project</span></span>

<span data-ttu-id="73d2a-233">Wir benötigen einen Dienst Verweis auf den WCF-Dienst.</span><span class="sxs-lookup"><span data-stu-id="73d2a-233">We need a service reference to our WCF service</span></span>

-   <span data-ttu-id="73d2a-234">Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **stesample. wpftest** , und wählen Sie **Dienstverweis hinzufügen...**</span><span class="sxs-lookup"><span data-stu-id="73d2a-234">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Add Service Reference...**</span></span>
-   <span data-ttu-id="73d2a-235">Klicken Sie auf **Ermitteln.**</span><span class="sxs-lookup"><span data-stu-id="73d2a-235">Click **Discover**</span></span>
-   <span data-ttu-id="73d2a-236">Geben Sie **bloggingservice** als Namespace ein, und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="73d2a-236">Enter **BloggingService** as the namespace and click **OK**</span></span>

<span data-ttu-id="73d2a-237">Nun können wir Code schreiben, um den Dienst zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="73d2a-237">Now we can write some code to consume the service.</span></span>

-   <span data-ttu-id="73d2a-238">Öffnen Sie " **MainWindow. XAML** ", und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="73d2a-238">Open **MainWindow.xaml** and replace the contents with the following code.</span></span>

``` xaml
    <Window
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:STESample="clr-namespace:STESample;assembly=STESample.Entities"
        mc:Ignorable="d" x:Class="STESample.WPFTest.MainWindow"
        Title="MainWindow" Height="350" Width="525" Loaded="Window_Loaded">

        <Window.Resources>
            <CollectionViewSource
                x:Key="blogViewSource"
                d:DesignSource="{d:DesignInstance {x:Type STESample:Blog}, CreateList=True}"/>
            <CollectionViewSource
                x:Key="blogPostsViewSource"
                Source="{Binding Posts, Source={StaticResource blogViewSource}}"/>
        </Window.Resources>

        <Grid DataContext="{StaticResource blogViewSource}">
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding}" Margin="10,10,10,179">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding BlogId}" Header="Id" Width="Auto" IsReadOnly="True" />
                    <DataGridTextColumn Binding="{Binding Name}" Header="Name" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Url}" Header="Url" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <DataGrid AutoGenerateColumns="False" EnableRowVirtualization="True"
                      ItemsSource="{Binding Source={StaticResource blogPostsViewSource}}" Margin="10,145,10,38">
                <DataGrid.Columns>
                    <DataGridTextColumn Binding="{Binding PostId}" Header="Id" Width="Auto"  IsReadOnly="True"/>
                    <DataGridTextColumn Binding="{Binding Title}" Header="Title" Width="Auto"/>
                    <DataGridTextColumn Binding="{Binding Content}" Header="Content" Width="Auto"/>
                </DataGrid.Columns>
            </DataGrid>
            <Button Width="68" Height="23" HorizontalAlignment="Right" VerticalAlignment="Bottom"
                    Margin="0,0,10,10" Click="buttonSave_Click">Save</Button>
        </Grid>
    </Window>
```

-   <span data-ttu-id="73d2a-239">Öffnen Sie den Code Behind für MainWindow (**MainWindow.XAML.cs**), und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="73d2a-239">Open the code behind for MainWindow (**MainWindow.xaml.cs**) and replace the contents with the following code</span></span>

``` csharp
    using STESample.WPFTest.BloggingService;
    using System.Collections.Generic;
    using System.Linq;
    using System.Windows;
    using System.Windows.Data;

    namespace STESample.WPFTest
    {
        public partial class MainWindow : Window
        {
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Find the view source for Blogs and populate it with all Blogs (and related Posts)
                    // from the Service. The default editing functionality of WPF will allow the objects
                    // to be manipulated on the screen.
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                using (var service = new Service1Client())
                {
                    // Get the blogs that are bound to the screen
                    var blogsViewSource = (CollectionViewSource)this.FindResource("blogViewSource");
                    var blogs = (List<Blog>)blogsViewSource.Source;

                    // Save all Blogs and related Posts
                    foreach (var blog in blogs)
                    {
                        service.UpdateBlog(blog);
                    }

                    // Re-query for data to get database-generated keys etc.
                    blogsViewSource.Source = service.GetBlogs().ToList();
                }
            }
        }
    }
```

<span data-ttu-id="73d2a-240">Sie können die Anwendung jetzt ausführen, um sie in Aktion zu sehen.</span><span class="sxs-lookup"><span data-stu-id="73d2a-240">You can now run the application to see it in action.</span></span>

-   <span data-ttu-id="73d2a-241">Klicken Sie mit der rechten Maustaste auf das Projekt **stesample. wpftest** in **Projektmappen-Explorer** und wählen Sie **Debuggen &gt; neue Instanz starten** aus.</span><span class="sxs-lookup"><span data-stu-id="73d2a-241">Right-click the **STESample.WPFTest** project in **Solution Explorer** and select **Debug -&gt; Start new instance**</span></span>
-   <span data-ttu-id="73d2a-242">Sie können die Daten mithilfe des Bildschirms bearbeiten und mithilfe der Schaltfläche " **Speichern** " über den Dienst speichern.</span><span class="sxs-lookup"><span data-stu-id="73d2a-242">You can manipulate the data using the screen and save it via the service using the **Save** button</span></span>

![WPF-Hauptfenster](~/ef6/media/wpf.png)
