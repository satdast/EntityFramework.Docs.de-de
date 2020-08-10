---
title: 'Erste Schritte mit WPF: EF Core'
author: jeremylikness
ms.author: jeliknes
ms.date: 07/24/2020
uid: core/get-started/wpf
ms.openlocfilehash: 958fc579a72a9bf3c97c5551d55777df6c32293f
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535583"
---
# <a name="getting-started-with-wpf"></a><span data-ttu-id="83575-102">Erste Schritte mit WPF</span><span class="sxs-lookup"><span data-stu-id="83575-102">Getting Started with WPF</span></span>

<span data-ttu-id="83575-103">In dieser schrittweisen exemplarischen Vorgehensweise wird gezeigt, wie POCO-Typen an WPF-Steuerelemente in einem „main-detail“-Formular gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="83575-103">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a "main-detail" form.</span></span> <span data-ttu-id="83575-104">Die Anwendung verwendet die Entity Framework-APIs zum Auffüllen von Objekten mit Daten aus der Datenbank, zum Nachverfolgen von Änderungen und zum persistenten Speichern von Daten in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="83575-104">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="83575-105">Das Modell definiert zwei Typen, die eine 1:n-Beziehung eingehen: **Category** (principal\\main) und **Product** (dependent\\detail).</span><span class="sxs-lookup"><span data-stu-id="83575-105">The model defines two types that participate in one-to-many relationship: **Category** (principal\\main) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="83575-106">Das WPF-Datenbindungsframework ermöglicht die Navigation zwischen verbundenen Objekten: durch Auswählen von Zeilen in der Masteransicht wird die Detailansicht mit den entsprechenden untergeordneten Daten aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="83575-106">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="83575-107">Die Screenshots und Codeauflistungen in dieser exemplarischen Vorgehensweise stammen aus Visual Studio 2019 16.6.5.</span><span class="sxs-lookup"><span data-stu-id="83575-107">The screen shots and code listings in this walkthrough are taken from Visual Studio 2019 16.6.5.</span></span>

> [!TIP]
> <span data-ttu-id="83575-108">Das in diesem Artikel verwendete [Beispiel finden Sie auf GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span><span class="sxs-lookup"><span data-stu-id="83575-108">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="83575-109">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="83575-109">Pre-Requisites</span></span>

* <span data-ttu-id="83575-110">Um diese exemplarische Vorgehensweise nachvollziehen zu können, müssen Sie Visual Studio 2019 16.3 oder höher mit ausgewählter **.NET-Desktopworkload** installiert haben.</span><span class="sxs-lookup"><span data-stu-id="83575-110">You need to have Visual Studio 2019 16.3 or later installed with the **.NET desktop workload** selected to complete this walkthrough.</span></span>
    
    <span data-ttu-id="83575-111">Weitere Informationen zur Installation der neuesten Version von Visual Studio finden Sie unter [Installieren von Visual Studio](/visualstudio/install/install-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="83575-111">For more information about installing the latest version of Visual Studio, see [Install Visual Studio](/visualstudio/install/install-visual-studio).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="83575-112">Erstellen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="83575-112">Create the Application</span></span>

1. <span data-ttu-id="83575-113">Öffnen Sie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="83575-113">Open Visual Studio</span></span>
2. <span data-ttu-id="83575-114">Wählen Sie im Startfenster **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="83575-114">On the start window, choose **Create new project**.</span></span>
3. <span data-ttu-id="83575-115">Suchen Sie nach „WPF“, wählen Sie **WPF-App (.NET Core)** aus, und wählen Sie dann **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="83575-115">Search for "WPF," choose **WPF App (.NET Core)** and then choose **Next**.</span></span>
4. <span data-ttu-id="83575-116">Geben Sie dem Projekt auf dem nächsten Bildschirm einen Namen (z. B. **GetStartedWPF**), und wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="83575-116">At the next screen, give the project a name, for example, **GetStartedWPF**, and choose **Create.**</span></span>

## <a name="install-the-entity-framework-nuget-packages"></a><span data-ttu-id="83575-117">Installieren des Entity Framework-NuGet-Pakets</span><span class="sxs-lookup"><span data-stu-id="83575-117">Install the Entity Framework NuGet packages</span></span>

1. <span data-ttu-id="83575-118">Klicken Sie mit der rechten Maustaste auf die Projektmappe, und wählen Sie **NuGet-Pakete für Projektmappe verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="83575-118">Right-click on the solution and choose **Manage NuGet Packages for Solution...**</span></span>

    ![NuGet-Pakete verwalten](_static/wpf-tutorial-nuget.jpg)

1. <span data-ttu-id="83575-120">Geben Sie im Suchfeld als Suchbegriff `entityframeworkcore.sqlite` ein.</span><span class="sxs-lookup"><span data-stu-id="83575-120">Type `entityframeworkcore.sqlite` in the search box.</span></span>
1. <span data-ttu-id="83575-121">Wählen Sie das Paket **Microsoft.EntityFrameworkCore.Sqlite** aus.</span><span class="sxs-lookup"><span data-stu-id="83575-121">Select the **Microsoft.EntityFrameworkCore.Sqlite** package.</span></span>
1. <span data-ttu-id="83575-122">Wählen Sie das Projekt im rechten Bereich aus, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="83575-122">Check the project in the right pane and click **Install**</span></span>

    ![SQLite-Paket](_static/wpf-tutorial-sqlite.jpg)

1. <span data-ttu-id="83575-124">Wiederholen Sie die Schritte, um nach `entityframeworkcore.proxies` zu suchen und **Microsoft.EntityFrameworkCore.Proxies** zu installieren.</span><span class="sxs-lookup"><span data-stu-id="83575-124">Repeat the steps to search for `entityframeworkcore.proxies` and install **Microsoft.EntityFrameworkCore.Proxies**.</span></span>

> [!NOTE]
> <span data-ttu-id="83575-125">Als Sie das SQLite-Paket installiert haben, wurde das zugehörige **Microsoft.EntityFrameworkCore**-Basispaket automatisch heruntergeladen.</span><span class="sxs-lookup"><span data-stu-id="83575-125">When you installed the Sqlite package, it automatically pulled down the related **Microsoft.EntityFrameworkCore** base package.</span></span> <span data-ttu-id="83575-126">Das **Microsoft. EntityFrameworkCore.Proxies**-Paket unterstützt „Lazy Loading“ von Daten.</span><span class="sxs-lookup"><span data-stu-id="83575-126">The **Microsoft.EntityFrameworkCore.Proxies** package provides support for "lazy-loading" data.</span></span> <span data-ttu-id="83575-127">Dies bedeutet, dass bei Entitäten mit untergeordneten Entitäten nur die übergeordneten Elemente beim anfänglichen Laden abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="83575-127">This means when you have entities with child entities, only the parents are fetched on the initial load.</span></span> <span data-ttu-id="83575-128">Die Proxys erkennen, wenn versucht wird, auf die untergeordneten Entitäten zuzugreifen. Diese werden bei Bedarf automatisch geladen.</span><span class="sxs-lookup"><span data-stu-id="83575-128">The proxies detect when an attempt to access the child entities is made and automatically loads them on demand.</span></span> 

## <a name="define-a-model"></a><span data-ttu-id="83575-129">Definieren eines Modells</span><span class="sxs-lookup"><span data-stu-id="83575-129">Define a Model</span></span>

<span data-ttu-id="83575-130">In dieser exemplarischen Vorgehensweise implementieren Sie ein Modell mit „Code First“.</span><span class="sxs-lookup"><span data-stu-id="83575-130">In this walkthrough you will implement a model using "code first."</span></span> <span data-ttu-id="83575-131">Dies bedeutet, dass EF Core die Datenbanktabellen und das Schema basierend auf den von Ihnen definierten C#-Klassen erstellt.</span><span class="sxs-lookup"><span data-stu-id="83575-131">This means that EF Core will create the database tables and schema based on the C# classes you define.</span></span>

<span data-ttu-id="83575-132">Fügen Sie eine neue Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="83575-132">Add a new class.</span></span> <span data-ttu-id="83575-133">Geben Sie ihr den Namen `Product.cs`, und füllen Sie sie wie folgt mit Daten auf:</span><span class="sxs-lookup"><span data-stu-id="83575-133">Give it the name: `Product.cs` and populate it like this:</span></span>

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

<span data-ttu-id="83575-134">Fügen Sie dann eine Klasse mit dem Namen `Category.cs` hinzu, und füllen Sie sie mit folgendem Code auf:</span><span class="sxs-lookup"><span data-stu-id="83575-134">Next, add a class named `Category.cs` and populate it with the following code:</span></span>

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

<span data-ttu-id="83575-135">Die **Products**-Eigenschaft für die **Category**-Klasse und die **Category**-Eigenschaft für die **Product**-Klasse sind Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="83575-135">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="83575-136">Im Entity Framework bieten Navigationseigenschaften eine Möglichkeit, in einer Beziehung zwischen zwei Entitätstypen zu navigieren.</span><span class="sxs-lookup"><span data-stu-id="83575-136">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="83575-137">Zusätzlich zur Definition von Entitäten müssen Sie eine Klasse definieren, die von DbContext abgeleitet ist und DbSet&lt;TEntity&gt;-Eigenschaften bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="83575-137">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="83575-138">Die DbSet&lt;TEntity&gt;-Eigenschaften informieren den Kontext darüber, welche Typen Sie in das Modell einbeziehen möchten.</span><span class="sxs-lookup"><span data-stu-id="83575-138">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="83575-139">Eine Instanz des von DbContext abgeleiteten Typs verwaltet die Entitätsobjekte während der Laufzeit, was das Auffüllen der Objekte mit Daten aus einer Datenbank, die Änderungsnachverfolgung und das persistente Speichern von Daten in der Datenbank umfasst.</span><span class="sxs-lookup"><span data-stu-id="83575-139">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="83575-140">Fügen Sie dem Projekt eine neue `ProductContext.cs`-Klasse mit der folgenden Definition hinzu:</span><span class="sxs-lookup"><span data-stu-id="83575-140">Add a new `ProductContext.cs` class to the project with the following definition:</span></span>

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* <span data-ttu-id="83575-141">`DbSet` informiert EF Core, welche C#-Entitäten der Datenbank zugeordnet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="83575-141">The `DbSet` informs EF Core what C# entities should be mapped to the database.</span></span>
* <span data-ttu-id="83575-142">Es gibt eine Vielzahl von Möglichkeiten, `DbContext` von EF Core zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="83575-142">There are a variety of ways to configure the EF Core `DbContext`.</span></span> <span data-ttu-id="83575-143">Informationen zu diesem Thema finden Sie hier: [Konfigurieren eines DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="83575-143">You can read about them in: [Configuring a DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span></span>
* <span data-ttu-id="83575-144">In diesem Beispiel wird die `OnConfiguring`-Überschreibung verwendet, um eine SQLite-Datendatei anzugeben.</span><span class="sxs-lookup"><span data-stu-id="83575-144">This example uses the `OnConfiguring` override to specify a Sqlite data file.</span></span>
* <span data-ttu-id="83575-145">Der `UseLazyLoadingProxies`-Aufruf informiert EF Core, Lazy Loading zu implementieren, damit untergeordnete Entitäten automatisch geladen werden, wenn der Zugriff über das übergeordnete Element erfolgt.</span><span class="sxs-lookup"><span data-stu-id="83575-145">The `UseLazyLoadingProxies` call tells EF Core to implement lazy-loading, so child entities are automatically loaded when accessed from the parent.</span></span>

<span data-ttu-id="83575-146">Drücken Sie **STRG+UMSCHALT+B**, oder navigieren Sie zu **Build &gt; Projektmappe erstellen**, um das Projekt zu kompilieren.</span><span class="sxs-lookup"><span data-stu-id="83575-146">Press **CTRL+SHIFT+B** or navigate to **Build &gt; Build Solution** to compile the project.</span></span>

> [!TIP]
> <span data-ttu-id="83575-147">Informieren Sie sich über die verschiedenen Möglichkeiten, die Datenbank und EF Core-Modelle synchron zu halten: [Verwalten von Datenbankschemas](/ef/core/managing-schemas).</span><span class="sxs-lookup"><span data-stu-id="83575-147">Learn about the different was to keep your database and EF Core models in sync: [Managing Database Schemas](/ef/core/managing-schemas).</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="83575-148">Lazy Loading</span><span class="sxs-lookup"><span data-stu-id="83575-148">Lazy Loading</span></span>

<span data-ttu-id="83575-149">Die **Products**-Eigenschaft für die **Category**-Klasse und die **Category**-Eigenschaft für die **Product**-Klasse sind Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="83575-149">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="83575-150">In Entity Framework Core bieten Navigationseigenschaften eine Möglichkeit, in einer Beziehung zwischen zwei Entitätstypen zu navigieren.</span><span class="sxs-lookup"><span data-stu-id="83575-150">In Entity Framework Core, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="83575-151">EF Core bietet Ihnen die Möglichkeit, verwandte Entitäten aus der Datenbank automatisch zu laden, wenn Sie zum ersten Mal auf die Navigationseigenschaft zugreifen.</span><span class="sxs-lookup"><span data-stu-id="83575-151">EF Core gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="83575-152">Beachten Sie bei dieser Art von Laden (das als „Lazy Loading“ bezeichnet wird), dass beim ersten Zugriff auf jede Navigationseigenschaft eine separate Abfrage für die Datenbank ausgeführt wird, wenn sich die Inhalte nicht bereits im Kontext befinden.</span><span class="sxs-lookup"><span data-stu-id="83575-152">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="83575-153">Bei der Verwendung von POCO-Entitätstypen („Plain Old C# Object“) erreicht EF Core Lazy Loading, indem während der Laufzeit Instanzen abgeleiteter Proxytypen erstellt und dann virtuelle Eigenschaften in Ihren Klassen überschrieben werden, um den Ladehook hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="83575-153">When using "Plain Old C# Object" (POCO) entity types, EF Core achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="83575-154">Um Lazy Loading von verwandten Objekten zu erzielen, müssen Sie Navigationseigenschaftsgetter als **public** und **virtual** deklarieren (**Overridable** in Visual Basic), und Ihre Klasse darf nicht **sealed** (**NotOverridable** in Visual Basic) sein.</span><span class="sxs-lookup"><span data-stu-id="83575-154">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and your class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="83575-155">Wenn Database First verwendet wird, werden Navigationseigenschaften automatisch als virtuell festgelegt, um Lazy Loading zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="83575-155">When using Database First, navigation properties are automatically made virtual to enable lazy loading.</span></span> 

## <a name="bind-object-to-controls"></a><span data-ttu-id="83575-156">Binden von Objekten an Steuerelemente</span><span class="sxs-lookup"><span data-stu-id="83575-156">Bind Object to Controls</span></span>

<span data-ttu-id="83575-157">Fügen Sie die Klassen, die im Modell definiert sind, als Datenquellen für diese WPF-Anwendung hinzu.</span><span class="sxs-lookup"><span data-stu-id="83575-157">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

1. <span data-ttu-id="83575-158">Doppelklicken Sie im Projektmappen-Explorer auf **MainWindow.xaml**, um das Hauptformular zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="83575-158">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
1. <span data-ttu-id="83575-159">Wählen Sie die Registerkarte **XAML** aus, um den XAML-Code zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="83575-159">Choose the **XAML** tab to edit the XAML.</span></span>
1. <span data-ttu-id="83575-160">Fügen Sie direkt nach dem öffnenden `Window`-Tag die folgenden Quellen hinzu, um eine Verbindung mit den EF Core-Entitäten herzustellen.</span><span class="sxs-lookup"><span data-stu-id="83575-160">Immediately after the opening `Window` tag, add the following sources to connect to the EF Core entities.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. <span data-ttu-id="83575-161">Dies legt die Quelle für die „übergeordneten“ Kategorien und die zweite Quelle für die „detail“-Produkte fest.</span><span class="sxs-lookup"><span data-stu-id="83575-161">This sets up source for the "parent" categories, and second source for the "detail" products.</span></span>
1. <span data-ttu-id="83575-162">Fügen Sie dem XAML dann das folgende Markup nach dem schließenden `Window.Resources`-Tag hinzu.</span><span class="sxs-lookup"><span data-stu-id="83575-162">Next, add the following markup to your XAML after the closing `Window.Resources` tag.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. <span data-ttu-id="83575-163">Beachten Sie, dass `CategoryId` auf `ReadOnly` festgelegt ist, da die ID von der Datenbank zugewiesen wird und nicht geändert werden kann.</span><span class="sxs-lookup"><span data-stu-id="83575-163">Note that the `CategoryId` is set to `ReadOnly` because it is assigned by the database and cannot be changed.</span></span>

## <a name="adding-a-details-grid"></a><span data-ttu-id="83575-164">Hinzufügen eines Detailrasters</span><span class="sxs-lookup"><span data-stu-id="83575-164">Adding a Details Grid</span></span>

<span data-ttu-id="83575-165">Da das Raster nun zum Anzeigen von Kategorien vorhanden ist, kann das Detailraster zum Anzeigen von Produkten hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="83575-165">Now that the grid exists to display categories, the details grid can be added to show products.</span></span>

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

<span data-ttu-id="83575-166">Fügen Sie schließlich eine Schaltfläche `Save` hinzu, und verbinden Sie das click-Ereignis mit `Button_Click`.</span><span class="sxs-lookup"><span data-stu-id="83575-166">Finally, add a `Save` button and wire in the click event to `Button_Click`.</span></span>

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

<span data-ttu-id="83575-167">Die Entwurfsansicht sollte wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="83575-167">Your design view should look like this:</span></span>

![Screenshot des WPF-Designers](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="83575-169">Hinzufügen von Code, der die Dateninteraktion verarbeitet</span><span class="sxs-lookup"><span data-stu-id="83575-169">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="83575-170">Es ist an der Zeit, dem Hauptfenster einige Ereignishandler hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="83575-170">It's time to add some event handlers to the main window.</span></span>

1. <span data-ttu-id="83575-171">Klicken Sie im XAML-Fenster auf das Fenster **&lt;Window&gt;** -Element, um das Hauptfenster auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="83575-171">In the XAML window, click on the **&lt;Window&gt;** element, to select the main window.</span></span>
1. <span data-ttu-id="83575-172">Wählen Sie im Fenster **Eigenschaften** oben rechts **Ereignisse** aus, und doppelklicken Sie dann auf das Textfeld rechts neben der Bezeichnung **Geladen**.</span><span class="sxs-lookup"><span data-stu-id="83575-172">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label.</span></span>

    ![Eigenschaften des Hauptfensters](_static/wpf-tutorial-loaded.jpg)

<span data-ttu-id="83575-174">Damit gelangen Sie zum Code Behind für das Formular. Nun bearbeiten wir den Code so, dass `ProductContext` für den Datenzugriff verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="83575-174">This brings you to the code behind for the form, we'll now edit the code to use the `ProductContext` to perform data access.</span></span> <span data-ttu-id="83575-175">Aktualisieren Sie den Code wie unten gezeigt.</span><span class="sxs-lookup"><span data-stu-id="83575-175">Update the code as shown below.</span></span>

<span data-ttu-id="83575-176">Der Code deklariert eine Instanz von `ProductContext` mit langer Ausführungszeit.</span><span class="sxs-lookup"><span data-stu-id="83575-176">The code declares a long-running instance of `ProductContext`.</span></span> <span data-ttu-id="83575-177">Das `ProductContext`-Objekt wird verwendet, um Daten abzufragen und in der Datenbank zu speichern.</span><span class="sxs-lookup"><span data-stu-id="83575-177">The `ProductContext` object is used to query and save data to the database.</span></span> <span data-ttu-id="83575-178">Die `Dispose()`-Methode für die `ProductContext`-Instanz wird dann von der überschriebenen `OnClosing`-Methode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="83575-178">The `Dispose()` method on the `ProductContext` instance is then called from the overridden `OnClosing` method.</span></span><span data-ttu-id="83575-179">Die Codekommentare erläutern die Funktionsweise der einzelnen Schritte.</span><span class="sxs-lookup"><span data-stu-id="83575-179"> The code comments explain what each step does.</span></span>

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> <span data-ttu-id="83575-180">Der Code verwendet einen Aufruf von `EnsureCreated()` zum Erstellen der Datenbank bei der ersten Ausführung.</span><span class="sxs-lookup"><span data-stu-id="83575-180">The code uses a call to `EnsureCreated()` to build the database on the first run.</span></span> <span data-ttu-id="83575-181">Dies ist für Demos akzeptabel, aber in Produktions-Apps sollten Sie [Migrationen](/ef/core/managing-schemas/migrations/) verwenden, um Ihr Schema zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="83575-181">This is acceptable for demos, but in production apps you should look at [migrations](/ef/core/managing-schemas/migrations/) to manage your schema.</span></span> <span data-ttu-id="83575-182">Der Code wird auch synchron ausgeführt, da er eine lokale SQLite-Datenbank verwendet.</span><span class="sxs-lookup"><span data-stu-id="83575-182">The code also executes synchronously because it uses a local SQLite database.</span></span> <span data-ttu-id="83575-183">In Produktionsszenarien, in denen normalerweise ein Remoteserver verwendet wird, sollten Sie die Verwendung der asynchronen Versionen der Methoden `Load` und `SaveChanges` in Erwägung ziehen.</span><span class="sxs-lookup"><span data-stu-id="83575-183">For production scenarios that typically involve a remote server, consider using the asynchronous versions of the `Load` and `SaveChanges` methods.</span></span>

## <a name="test-the-wpf-application"></a><span data-ttu-id="83575-184">Testen der WPF-Anwendung</span><span class="sxs-lookup"><span data-stu-id="83575-184">Test the WPF Application</span></span>

<span data-ttu-id="83575-185">Kompilieren Sie die Anwendung, und führen Sie sie aus, indem Sie **F5** drücken oder **Debuggen &gt; Debuggen starten** auswählen.</span><span class="sxs-lookup"><span data-stu-id="83575-185">Compile and run the application by pressing **F5** or choosing **Debug &gt; Start Debugging**.</span></span> <span data-ttu-id="83575-186">Die Datenbank sollte automatisch mit einer Datei namens `products.db` erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="83575-186">The database should be automatically created with a file named `products.db`.</span></span> <span data-ttu-id="83575-187">Geben Sie einen Kategorienamen ein, und drücken Sie die EINGABETASTE. Fügen Sie dann dem unteren Raster Produkte hinzu.</span><span class="sxs-lookup"><span data-stu-id="83575-187">Enter a category name and hit enter, then add products to the lower grid.</span></span> <span data-ttu-id="83575-188">Klicken Sie auf „Speichern“, und beobachten Sie, wie das Raster mit den von der Datenbank bereitgestellten IDs aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="83575-188">Click save and watch the grid refresh with the database provided ids.</span></span> <span data-ttu-id="83575-189">Markieren Sie eine Zeile, und klicken Sie auf **Löschen**, um die Zeile zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="83575-189">Highlight a row and hit **Delete** to remove the row.</span></span> <span data-ttu-id="83575-190">Die Entität wird gelöscht, wenn Sie auf **Speichern** klicken.</span><span class="sxs-lookup"><span data-stu-id="83575-190">The entity will be deleted when you click **Save**.</span></span>

![Ausgeführte Anwendung](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a><span data-ttu-id="83575-192">Benachrichtigung der Eigenschaftenänderung</span><span class="sxs-lookup"><span data-stu-id="83575-192">Property Change Notification</span></span>

<span data-ttu-id="83575-193">Dieses Beispiel basiert auf vier Schritten, um die Entitäten mit der Benutzeroberfläche zu synchronisieren.</span><span class="sxs-lookup"><span data-stu-id="83575-193">This example relies on four steps to synchronize the entities with the UI.</span></span> 

1. <span data-ttu-id="83575-194">Der anfängliche Aufruf von `_context.Categories.Load()` lädt die Kategoriedaten.</span><span class="sxs-lookup"><span data-stu-id="83575-194">The initial call `_context.Categories.Load()` loads the categories data.</span></span>
1. <span data-ttu-id="83575-195">Die Lazy Loading-Proxys laden die Daten der abhängigen Produkte.</span><span class="sxs-lookup"><span data-stu-id="83575-195">The lazy-loading proxies load the dependent products data.</span></span>
1. <span data-ttu-id="83575-196">Die integrierte Änderungsnachverfolgung von EF Core nimmt die erforderlichen Änderungen an Entitäten vor, einschließlich Einfügungen und Löschungen, wenn `_context.SaveChanges()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="83575-196">EF Core's built-in change tracking makes the necessary modifications to entities, including insertions and deletions, when `_context.SaveChanges()` is called.</span></span>
1. <span data-ttu-id="83575-197">Die Aufrufe von `DataGridView.Items.Refresh()` erzwingen einen erneuten Ladevorgang mit den neu generierten IDs.</span><span class="sxs-lookup"><span data-stu-id="83575-197">The calls to `DataGridView.Items.Refresh()` force a reload with the newly generated ids.</span></span>

<span data-ttu-id="83575-198">Dies funktioniert für unser Beispiel mit ersten Schritten, aber Sie benötigen möglicherweise zusätzlichen Code für andere Szenarien.</span><span class="sxs-lookup"><span data-stu-id="83575-198">This works for our getting started sample, but you may require additional code for other scenarios.</span></span> <span data-ttu-id="83575-199">WPF-Steuerelemente rendern die Benutzeroberfläche, indem sie die Felder und Eigenschaften für Ihre Entitäten lesen.</span><span class="sxs-lookup"><span data-stu-id="83575-199">WPF controls render the UI by reading the fields and properties on your entities.</span></span> <span data-ttu-id="83575-200">Wenn Sie einen Wert in der Benutzeroberfläche (User Interface, UI) bearbeiten, wird dieser Wert an Ihre Entität übergeben.</span><span class="sxs-lookup"><span data-stu-id="83575-200">When you edit a value in the user interface (UI), that value is passed to your entity.</span></span> <span data-ttu-id="83575-201">Wenn Sie den Wert einer Eigenschaft direkt für Ihre Entität ändern, z. B. das Laden aus der Datenbank, spiegelt WPF die Änderungen nicht sofort in der Benutzeroberfläche wider.</span><span class="sxs-lookup"><span data-stu-id="83575-201">When you change the value of a property directly on your entity, such as loading it from the database, WPF will not immediately reflect the changes in the UI.</span></span> <span data-ttu-id="83575-202">Die Rendering-Engine muss über die Änderungen benachrichtigt werden.</span><span class="sxs-lookup"><span data-stu-id="83575-202">The rendering engine must be notified of the changes.</span></span> <span data-ttu-id="83575-203">Das Projekt hat dies durch manuelles Aufrufen von `Refresh()` erreicht.</span><span class="sxs-lookup"><span data-stu-id="83575-203">The project did this by manually calling `Refresh()`.</span></span> <span data-ttu-id="83575-204">Eine einfache Möglichkeit, diese Benachrichtigung zu automatisieren, besteht darin, die [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged)-Schnittstelle zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="83575-204">An easy way to automate this notification is by implementing the [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) interface.</span></span> <span data-ttu-id="83575-205">WPF-Komponenten erkennen die Schnittstelle automatisch und registrieren sich für Änderungsereignisse.</span><span class="sxs-lookup"><span data-stu-id="83575-205">WPF components will automatically detect the interface and register for change events.</span></span> <span data-ttu-id="83575-206">Die Entität ist für das Auslösen dieser Ereignisse verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="83575-206">The entity is responsible for raising these events.</span></span> 

> [!TIP]
> <span data-ttu-id="83575-207">Weitere Informationen zum Verarbeiten von Änderungen finden Sie unter: [Implementieren von Benachrichtigungen bei Eigenschaftenänderungen](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span><span class="sxs-lookup"><span data-stu-id="83575-207">To learn more about how to handle changes, read: [How to implement property change notification](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span></span>

## <a name="next-steps"></a><span data-ttu-id="83575-208">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="83575-208">Next Steps</span></span>

<span data-ttu-id="83575-209">Weitere Informationen zum [Konfigurieren eines DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="83575-209">Learn more about [Configuring a DbContext](/ef/core/miscellaneous/configuring-dbcontext).</span></span>