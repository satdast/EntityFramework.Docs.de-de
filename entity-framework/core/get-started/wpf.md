---
title: 'Erste Schritte mit WPF: EF Core'
description: Tutorial zu den ersten Schritten für die Verwendung von WPF mit Entity Framework Core
author: jeremylikness
ms.author: jeliknes
ms.date: 07/24/2020
uid: core/get-started/wpf
ms.openlocfilehash: 1198da5c9564663ca26392b33462c727275a432d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619306"
---
# <a name="getting-started-with-wpf"></a><span data-ttu-id="807bd-103">Erste Schritte mit WPF</span><span class="sxs-lookup"><span data-stu-id="807bd-103">Getting Started with WPF</span></span>

<span data-ttu-id="807bd-104">In dieser schrittweisen exemplarischen Vorgehensweise wird gezeigt, wie POCO-Typen an WPF-Steuerelemente in einem „main-detail“-Formular gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="807bd-104">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a "main-detail" form.</span></span> <span data-ttu-id="807bd-105">Die Anwendung verwendet die Entity Framework-APIs zum Auffüllen von Objekten mit Daten aus der Datenbank, zum Nachverfolgen von Änderungen und zum persistenten Speichern von Daten in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="807bd-105">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="807bd-106">Das Modell definiert zwei Typen, die eine 1:n-Beziehung eingehen: **Category** (principal\\main) und **Product** (dependent\\detail).</span><span class="sxs-lookup"><span data-stu-id="807bd-106">The model defines two types that participate in one-to-many relationship: **Category** (principal\\main) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="807bd-107">Das WPF-Datenbindungsframework ermöglicht die Navigation zwischen verbundenen Objekten: durch Auswählen von Zeilen in der Masteransicht wird die Detailansicht mit den entsprechenden untergeordneten Daten aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="807bd-107">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="807bd-108">Die Screenshots und Codeauflistungen in dieser exemplarischen Vorgehensweise stammen aus Visual Studio 2019 16.6.5.</span><span class="sxs-lookup"><span data-stu-id="807bd-108">The screen shots and code listings in this walkthrough are taken from Visual Studio 2019 16.6.5.</span></span>

> [!TIP]
> <span data-ttu-id="807bd-109">Das in diesem Artikel verwendete [Beispiel finden Sie auf GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span><span class="sxs-lookup"><span data-stu-id="807bd-109">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/WPF).</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="807bd-110">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="807bd-110">Pre-Requisites</span></span>

* <span data-ttu-id="807bd-111">Um diese exemplarische Vorgehensweise nachvollziehen zu können, müssen Sie Visual Studio 2019 16.3 oder höher mit ausgewählter **.NET-Desktopworkload** installiert haben.</span><span class="sxs-lookup"><span data-stu-id="807bd-111">You need to have Visual Studio 2019 16.3 or later installed with the **.NET desktop workload** selected to complete this walkthrough.</span></span>
    
    <span data-ttu-id="807bd-112">Weitere Informationen zur Installation der neuesten Version von Visual Studio finden Sie unter [Installieren von Visual Studio](/visualstudio/install/install-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="807bd-112">For more information about installing the latest version of Visual Studio, see [Install Visual Studio](/visualstudio/install/install-visual-studio).</span></span>

## <a name="create-the-application"></a><span data-ttu-id="807bd-113">Erstellen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="807bd-113">Create the Application</span></span>

1. <span data-ttu-id="807bd-114">Öffnen Sie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="807bd-114">Open Visual Studio</span></span>
2. <span data-ttu-id="807bd-115">Wählen Sie im Startfenster **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="807bd-115">On the start window, choose **Create new project**.</span></span>
3. <span data-ttu-id="807bd-116">Suchen Sie nach „WPF“, wählen Sie **WPF-App (.NET Core)** aus, und wählen Sie dann **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="807bd-116">Search for "WPF," choose **WPF App (.NET Core)** and then choose **Next**.</span></span>
4. <span data-ttu-id="807bd-117">Geben Sie dem Projekt auf dem nächsten Bildschirm einen Namen (z. B. **GetStartedWPF**), und wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="807bd-117">At the next screen, give the project a name, for example, **GetStartedWPF**, and choose **Create.**</span></span>

## <a name="install-the-entity-framework-nuget-packages"></a><span data-ttu-id="807bd-118">Installieren des Entity Framework-NuGet-Pakets</span><span class="sxs-lookup"><span data-stu-id="807bd-118">Install the Entity Framework NuGet packages</span></span>

1. <span data-ttu-id="807bd-119">Klicken Sie mit der rechten Maustaste auf die Projektmappe, und wählen Sie **NuGet-Pakete für Projektmappe verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="807bd-119">Right-click on the solution and choose **Manage NuGet Packages for Solution...**</span></span>

    ![NuGet-Pakete verwalten](_static/wpf-tutorial-nuget.jpg)

1. <span data-ttu-id="807bd-121">Geben Sie im Suchfeld als Suchbegriff `entityframeworkcore.sqlite` ein.</span><span class="sxs-lookup"><span data-stu-id="807bd-121">Type `entityframeworkcore.sqlite` in the search box.</span></span>
1. <span data-ttu-id="807bd-122">Wählen Sie das Paket **Microsoft.EntityFrameworkCore.Sqlite** aus.</span><span class="sxs-lookup"><span data-stu-id="807bd-122">Select the **Microsoft.EntityFrameworkCore.Sqlite** package.</span></span>
1. <span data-ttu-id="807bd-123">Wählen Sie das Projekt im rechten Bereich aus, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="807bd-123">Check the project in the right pane and click **Install**</span></span>

    ![SQLite-Paket](_static/wpf-tutorial-sqlite.jpg)

1. <span data-ttu-id="807bd-125">Wiederholen Sie die Schritte, um nach `entityframeworkcore.proxies` zu suchen und **Microsoft.EntityFrameworkCore.Proxies** zu installieren.</span><span class="sxs-lookup"><span data-stu-id="807bd-125">Repeat the steps to search for `entityframeworkcore.proxies` and install **Microsoft.EntityFrameworkCore.Proxies**.</span></span>

> [!NOTE]
> <span data-ttu-id="807bd-126">Als Sie das SQLite-Paket installiert haben, wurde das zugehörige **Microsoft.EntityFrameworkCore**-Basispaket automatisch heruntergeladen.</span><span class="sxs-lookup"><span data-stu-id="807bd-126">When you installed the Sqlite package, it automatically pulled down the related **Microsoft.EntityFrameworkCore** base package.</span></span> <span data-ttu-id="807bd-127">Das **Microsoft. EntityFrameworkCore.Proxies**-Paket unterstützt „Lazy Loading“ von Daten.</span><span class="sxs-lookup"><span data-stu-id="807bd-127">The **Microsoft.EntityFrameworkCore.Proxies** package provides support for "lazy-loading" data.</span></span> <span data-ttu-id="807bd-128">Dies bedeutet, dass bei Entitäten mit untergeordneten Entitäten nur die übergeordneten Elemente beim anfänglichen Laden abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="807bd-128">This means when you have entities with child entities, only the parents are fetched on the initial load.</span></span> <span data-ttu-id="807bd-129">Die Proxys erkennen, wenn versucht wird, auf die untergeordneten Entitäten zuzugreifen. Diese werden bei Bedarf automatisch geladen.</span><span class="sxs-lookup"><span data-stu-id="807bd-129">The proxies detect when an attempt to access the child entities is made and automatically loads them on demand.</span></span> 

## <a name="define-a-model"></a><span data-ttu-id="807bd-130">Definieren eines Modells</span><span class="sxs-lookup"><span data-stu-id="807bd-130">Define a Model</span></span>

<span data-ttu-id="807bd-131">In dieser exemplarischen Vorgehensweise implementieren Sie ein Modell mit „Code First“.</span><span class="sxs-lookup"><span data-stu-id="807bd-131">In this walkthrough you will implement a model using "code first."</span></span> <span data-ttu-id="807bd-132">Dies bedeutet, dass EF Core die Datenbanktabellen und das Schema basierend auf den von Ihnen definierten C#-Klassen erstellt.</span><span class="sxs-lookup"><span data-stu-id="807bd-132">This means that EF Core will create the database tables and schema based on the C# classes you define.</span></span>

<span data-ttu-id="807bd-133">Fügen Sie eine neue Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="807bd-133">Add a new class.</span></span> <span data-ttu-id="807bd-134">Geben Sie ihr den Namen `Product.cs`, und füllen Sie sie wie folgt mit Daten auf:</span><span class="sxs-lookup"><span data-stu-id="807bd-134">Give it the name: `Product.cs` and populate it like this:</span></span>

**`Product.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Product.cs)]

<span data-ttu-id="807bd-135">Fügen Sie dann eine Klasse mit dem Namen `Category.cs` hinzu, und füllen Sie sie mit folgendem Code auf:</span><span class="sxs-lookup"><span data-stu-id="807bd-135">Next, add a class named `Category.cs` and populate it with the following code:</span></span>

**`Category.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/Category.cs)]

<span data-ttu-id="807bd-136">Die **Products**-Eigenschaft für die **Category**-Klasse und die **Category**-Eigenschaft für die **Product**-Klasse sind Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="807bd-136">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="807bd-137">Im Entity Framework bieten Navigationseigenschaften eine Möglichkeit, in einer Beziehung zwischen zwei Entitätstypen zu navigieren.</span><span class="sxs-lookup"><span data-stu-id="807bd-137">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="807bd-138">Zusätzlich zur Definition von Entitäten müssen Sie eine Klasse definieren, die von DbContext abgeleitet ist und DbSet&lt;TEntity&gt;-Eigenschaften bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="807bd-138">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="807bd-139">Die DbSet&lt;TEntity&gt;-Eigenschaften informieren den Kontext darüber, welche Typen Sie in das Modell einbeziehen möchten.</span><span class="sxs-lookup"><span data-stu-id="807bd-139">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="807bd-140">Eine Instanz des von DbContext abgeleiteten Typs verwaltet die Entitätsobjekte während der Laufzeit, was das Auffüllen der Objekte mit Daten aus einer Datenbank, die Änderungsnachverfolgung und das persistente Speichern von Daten in der Datenbank umfasst.</span><span class="sxs-lookup"><span data-stu-id="807bd-140">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

<span data-ttu-id="807bd-141">Fügen Sie dem Projekt eine neue `ProductContext.cs`-Klasse mit der folgenden Definition hinzu:</span><span class="sxs-lookup"><span data-stu-id="807bd-141">Add a new `ProductContext.cs` class to the project with the following definition:</span></span>

**`ProductContext.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/ProductContext.cs)]

* <span data-ttu-id="807bd-142">`DbSet` informiert EF Core, welche C#-Entitäten der Datenbank zugeordnet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="807bd-142">The `DbSet` informs EF Core what C# entities should be mapped to the database.</span></span>
* <span data-ttu-id="807bd-143">Es gibt eine Vielzahl von Möglichkeiten, `DbContext` von EF Core zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="807bd-143">There are a variety of ways to configure the EF Core `DbContext`.</span></span> <span data-ttu-id="807bd-144">Informationen zu diesem Thema finden Sie hier: [Konfigurieren eines DbContext](xref:core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="807bd-144">You can read about them in: [Configuring a DbContext](xref:core/miscellaneous/configuring-dbcontext).</span></span>
* <span data-ttu-id="807bd-145">In diesem Beispiel wird die `OnConfiguring`-Überschreibung verwendet, um eine SQLite-Datendatei anzugeben.</span><span class="sxs-lookup"><span data-stu-id="807bd-145">This example uses the `OnConfiguring` override to specify a Sqlite data file.</span></span>
* <span data-ttu-id="807bd-146">Der `UseLazyLoadingProxies`-Aufruf informiert EF Core, Lazy Loading zu implementieren, damit untergeordnete Entitäten automatisch geladen werden, wenn der Zugriff über das übergeordnete Element erfolgt.</span><span class="sxs-lookup"><span data-stu-id="807bd-146">The `UseLazyLoadingProxies` call tells EF Core to implement lazy-loading, so child entities are automatically loaded when accessed from the parent.</span></span>

<span data-ttu-id="807bd-147">Drücken Sie **STRG+UMSCHALT+B**, oder navigieren Sie zu **Build &gt; Projektmappe erstellen**, um das Projekt zu kompilieren.</span><span class="sxs-lookup"><span data-stu-id="807bd-147">Press **CTRL+SHIFT+B** or navigate to **Build &gt; Build Solution** to compile the project.</span></span>

> [!TIP]
> <span data-ttu-id="807bd-148">Informieren Sie sich über die verschiedenen Möglichkeiten, die Datenbank und EF Core-Modelle synchron zu halten: [Verwalten von Datenbankschemas](xref:core/managing-schemas/index).</span><span class="sxs-lookup"><span data-stu-id="807bd-148">Learn about the different was to keep your database and EF Core models in sync: [Managing Database Schemas](xref:core/managing-schemas/index).</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="807bd-149">Lazy Loading</span><span class="sxs-lookup"><span data-stu-id="807bd-149">Lazy Loading</span></span>

<span data-ttu-id="807bd-150">Die **Products**-Eigenschaft für die **Category**-Klasse und die **Category**-Eigenschaft für die **Product**-Klasse sind Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="807bd-150">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="807bd-151">In Entity Framework Core bieten Navigationseigenschaften eine Möglichkeit, in einer Beziehung zwischen zwei Entitätstypen zu navigieren.</span><span class="sxs-lookup"><span data-stu-id="807bd-151">In Entity Framework Core, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="807bd-152">EF Core bietet Ihnen die Möglichkeit, verwandte Entitäten aus der Datenbank automatisch zu laden, wenn Sie zum ersten Mal auf die Navigationseigenschaft zugreifen.</span><span class="sxs-lookup"><span data-stu-id="807bd-152">EF Core gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="807bd-153">Beachten Sie bei dieser Art von Laden (das als „Lazy Loading“ bezeichnet wird), dass beim ersten Zugriff auf jede Navigationseigenschaft eine separate Abfrage für die Datenbank ausgeführt wird, wenn sich die Inhalte nicht bereits im Kontext befinden.</span><span class="sxs-lookup"><span data-stu-id="807bd-153">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="807bd-154">Bei der Verwendung von POCO-Entitätstypen („Plain Old C# Object“) erreicht EF Core Lazy Loading, indem während der Laufzeit Instanzen abgeleiteter Proxytypen erstellt und dann virtuelle Eigenschaften in Ihren Klassen überschrieben werden, um den Ladehook hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="807bd-154">When using "Plain Old C# Object" (POCO) entity types, EF Core achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="807bd-155">Um Lazy Loading von verwandten Objekten zu erzielen, müssen Sie Navigationseigenschaftsgetter als **public** und **virtual** deklarieren (**Overridable** in Visual Basic), und Ihre Klasse darf nicht **sealed** (**NotOverridable** in Visual Basic) sein.</span><span class="sxs-lookup"><span data-stu-id="807bd-155">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and your class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="807bd-156">Wenn Database First verwendet wird, werden Navigationseigenschaften automatisch als virtuell festgelegt, um Lazy Loading zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="807bd-156">When using Database First, navigation properties are automatically made virtual to enable lazy loading.</span></span> 

## <a name="bind-object-to-controls"></a><span data-ttu-id="807bd-157">Binden von Objekten an Steuerelemente</span><span class="sxs-lookup"><span data-stu-id="807bd-157">Bind Object to Controls</span></span>

<span data-ttu-id="807bd-158">Fügen Sie die Klassen, die im Modell definiert sind, als Datenquellen für diese WPF-Anwendung hinzu.</span><span class="sxs-lookup"><span data-stu-id="807bd-158">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

1. <span data-ttu-id="807bd-159">Doppelklicken Sie im Projektmappen-Explorer auf **MainWindow.xaml**, um das Hauptformular zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="807bd-159">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
1. <span data-ttu-id="807bd-160">Wählen Sie die Registerkarte **XAML** aus, um den XAML-Code zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="807bd-160">Choose the **XAML** tab to edit the XAML.</span></span>
1. <span data-ttu-id="807bd-161">Fügen Sie direkt nach dem öffnenden `Window`-Tag die folgenden Quellen hinzu, um eine Verbindung mit den EF Core-Entitäten herzustellen.</span><span class="sxs-lookup"><span data-stu-id="807bd-161">Immediately after the opening `Window` tag, add the following sources to connect to the EF Core entities.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=1-13&highlight=9-13)]

1. <span data-ttu-id="807bd-162">Dies legt die Quelle für die „übergeordneten“ Kategorien und die zweite Quelle für die „detail“-Produkte fest.</span><span class="sxs-lookup"><span data-stu-id="807bd-162">This sets up source for the "parent" categories, and second source for the "detail" products.</span></span>
1. <span data-ttu-id="807bd-163">Fügen Sie dem XAML dann das folgende Markup nach dem schließenden `Window.Resources`-Tag hinzu.</span><span class="sxs-lookup"><span data-stu-id="807bd-163">Next, add the following markup to your XAML after the closing `Window.Resources` tag.</span></span>

    [!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=15-26)]

1. <span data-ttu-id="807bd-164">Beachten Sie, dass `CategoryId` auf `ReadOnly` festgelegt ist, da die ID von der Datenbank zugewiesen wird und nicht geändert werden kann.</span><span class="sxs-lookup"><span data-stu-id="807bd-164">Note that the `CategoryId` is set to `ReadOnly` because it is assigned by the database and cannot be changed.</span></span>

## <a name="adding-a-details-grid"></a><span data-ttu-id="807bd-165">Hinzufügen eines Detailrasters</span><span class="sxs-lookup"><span data-stu-id="807bd-165">Adding a Details Grid</span></span>

<span data-ttu-id="807bd-166">Da das Raster nun zum Anzeigen von Kategorien vorhanden ist, kann das Detailraster zum Anzeigen von Produkten hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="807bd-166">Now that the grid exists to display categories, the details grid can be added to show products.</span></span>

**`MainWindow.xaml`**

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=27-40)]

<span data-ttu-id="807bd-167">Fügen Sie schließlich eine Schaltfläche `Save` hinzu, und verbinden Sie das click-Ereignis mit `Button_Click`.</span><span class="sxs-lookup"><span data-stu-id="807bd-167">Finally, add a `Save` button and wire in the click event to `Button_Click`.</span></span>

[!code-xaml[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml?range=41-42)]

<span data-ttu-id="807bd-168">Die Entwurfsansicht sollte wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="807bd-168">Your design view should look like this:</span></span>

![Screenshot des WPF-Designers](_static/wpf-tutorial-designer.jpg)

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="807bd-170">Hinzufügen von Code, der die Dateninteraktion verarbeitet</span><span class="sxs-lookup"><span data-stu-id="807bd-170">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="807bd-171">Es ist an der Zeit, dem Hauptfenster einige Ereignishandler hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="807bd-171">It's time to add some event handlers to the main window.</span></span>

1. <span data-ttu-id="807bd-172">Klicken Sie im XAML-Fenster auf das Fenster **&lt;Window&gt;** -Element, um das Hauptfenster auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="807bd-172">In the XAML window, click on the **&lt;Window&gt;** element, to select the main window.</span></span>
1. <span data-ttu-id="807bd-173">Wählen Sie im Fenster **Eigenschaften** oben rechts **Ereignisse** aus, und doppelklicken Sie dann auf das Textfeld rechts neben der Bezeichnung **Geladen**.</span><span class="sxs-lookup"><span data-stu-id="807bd-173">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label.</span></span>

    ![Eigenschaften des Hauptfensters](_static/wpf-tutorial-loaded.jpg)

<span data-ttu-id="807bd-175">Damit gelangen Sie zum Code Behind für das Formular. Nun bearbeiten wir den Code so, dass `ProductContext` für den Datenzugriff verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="807bd-175">This brings you to the code behind for the form, we'll now edit the code to use the `ProductContext` to perform data access.</span></span> <span data-ttu-id="807bd-176">Aktualisieren Sie den Code wie unten gezeigt.</span><span class="sxs-lookup"><span data-stu-id="807bd-176">Update the code as shown below.</span></span>

<span data-ttu-id="807bd-177">Der Code deklariert eine Instanz von `ProductContext` mit langer Ausführungszeit.</span><span class="sxs-lookup"><span data-stu-id="807bd-177">The code declares a long-running instance of `ProductContext`.</span></span> <span data-ttu-id="807bd-178">Das `ProductContext`-Objekt wird verwendet, um Daten abzufragen und in der Datenbank zu speichern.</span><span class="sxs-lookup"><span data-stu-id="807bd-178">The `ProductContext` object is used to query and save data to the database.</span></span> <span data-ttu-id="807bd-179">Die `Dispose()`-Methode für die `ProductContext`-Instanz wird dann von der überschriebenen `OnClosing`-Methode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="807bd-179">The `Dispose()` method on the `ProductContext` instance is then called from the overridden `OnClosing` method.</span></span><span data-ttu-id="807bd-180">Die Codekommentare erläutern die Funktionsweise der einzelnen Schritte.</span><span class="sxs-lookup"><span data-stu-id="807bd-180"> The code comments explain what each step does.</span></span>

**`MainWindow.xaml.cs`**

[!code-csharp[](../../../samples/core/WPF/GetStartedWPF/GetStartedWPF/MainWindow.xaml.cs)]

> [!NOTE]
> <span data-ttu-id="807bd-181">Der Code verwendet einen Aufruf von `EnsureCreated()` zum Erstellen der Datenbank bei der ersten Ausführung.</span><span class="sxs-lookup"><span data-stu-id="807bd-181">The code uses a call to `EnsureCreated()` to build the database on the first run.</span></span> <span data-ttu-id="807bd-182">Dies ist für Demos akzeptabel, aber in Produktions-Apps sollten Sie [Migrationen](xref:core/managing-schemas/migrations/index) verwenden, um Ihr Schema zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="807bd-182">This is acceptable for demos, but in production apps you should look at [migrations](xref:core/managing-schemas/migrations/index) to manage your schema.</span></span> <span data-ttu-id="807bd-183">Der Code wird auch synchron ausgeführt, da er eine lokale SQLite-Datenbank verwendet.</span><span class="sxs-lookup"><span data-stu-id="807bd-183">The code also executes synchronously because it uses a local SQLite database.</span></span> <span data-ttu-id="807bd-184">In Produktionsszenarien, in denen normalerweise ein Remoteserver verwendet wird, sollten Sie die Verwendung der asynchronen Versionen der Methoden `Load` und `SaveChanges` in Erwägung ziehen.</span><span class="sxs-lookup"><span data-stu-id="807bd-184">For production scenarios that typically involve a remote server, consider using the asynchronous versions of the `Load` and `SaveChanges` methods.</span></span>

## <a name="test-the-wpf-application"></a><span data-ttu-id="807bd-185">Testen der WPF-Anwendung</span><span class="sxs-lookup"><span data-stu-id="807bd-185">Test the WPF Application</span></span>

<span data-ttu-id="807bd-186">Kompilieren Sie die Anwendung, und führen Sie sie aus, indem Sie **F5** drücken oder **Debuggen &gt; Debuggen starten** auswählen.</span><span class="sxs-lookup"><span data-stu-id="807bd-186">Compile and run the application by pressing **F5** or choosing **Debug &gt; Start Debugging**.</span></span> <span data-ttu-id="807bd-187">Die Datenbank sollte automatisch mit einer Datei namens `products.db` erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="807bd-187">The database should be automatically created with a file named `products.db`.</span></span> <span data-ttu-id="807bd-188">Geben Sie einen Kategorienamen ein, und drücken Sie die EINGABETASTE. Fügen Sie dann dem unteren Raster Produkte hinzu.</span><span class="sxs-lookup"><span data-stu-id="807bd-188">Enter a category name and hit enter, then add products to the lower grid.</span></span> <span data-ttu-id="807bd-189">Klicken Sie auf „Speichern“, und beobachten Sie, wie das Raster mit den von der Datenbank bereitgestellten IDs aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="807bd-189">Click save and watch the grid refresh with the database provided ids.</span></span> <span data-ttu-id="807bd-190">Markieren Sie eine Zeile, und klicken Sie auf **Löschen**, um die Zeile zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="807bd-190">Highlight a row and hit **Delete** to remove the row.</span></span> <span data-ttu-id="807bd-191">Die Entität wird gelöscht, wenn Sie auf **Speichern** klicken.</span><span class="sxs-lookup"><span data-stu-id="807bd-191">The entity will be deleted when you click **Save**.</span></span>

![Ausgeführte Anwendung](_static/wpf-tutorial-app.jpg)

## <a name="property-change-notification"></a><span data-ttu-id="807bd-193">Benachrichtigung der Eigenschaftenänderung</span><span class="sxs-lookup"><span data-stu-id="807bd-193">Property Change Notification</span></span>

<span data-ttu-id="807bd-194">Dieses Beispiel basiert auf vier Schritten, um die Entitäten mit der Benutzeroberfläche zu synchronisieren.</span><span class="sxs-lookup"><span data-stu-id="807bd-194">This example relies on four steps to synchronize the entities with the UI.</span></span>

1. <span data-ttu-id="807bd-195">Der anfängliche Aufruf von `_context.Categories.Load()` lädt die Kategoriedaten.</span><span class="sxs-lookup"><span data-stu-id="807bd-195">The initial call `_context.Categories.Load()` loads the categories data.</span></span>
1. <span data-ttu-id="807bd-196">Die Lazy Loading-Proxys laden die Daten der abhängigen Produkte.</span><span class="sxs-lookup"><span data-stu-id="807bd-196">The lazy-loading proxies load the dependent products data.</span></span>
1. <span data-ttu-id="807bd-197">Die integrierte Änderungsnachverfolgung von EF Core nimmt die erforderlichen Änderungen an Entitäten vor, einschließlich Einfügungen und Löschungen, wenn `_context.SaveChanges()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="807bd-197">EF Core's built-in change tracking makes the necessary modifications to entities, including insertions and deletions, when `_context.SaveChanges()` is called.</span></span>
1. <span data-ttu-id="807bd-198">Die Aufrufe von `DataGridView.Items.Refresh()` erzwingen einen erneuten Ladevorgang mit den neu generierten IDs.</span><span class="sxs-lookup"><span data-stu-id="807bd-198">The calls to `DataGridView.Items.Refresh()` force a reload with the newly generated ids.</span></span>

<span data-ttu-id="807bd-199">Dies funktioniert für unser Beispiel mit ersten Schritten, aber Sie benötigen möglicherweise zusätzlichen Code für andere Szenarien.</span><span class="sxs-lookup"><span data-stu-id="807bd-199">This works for our getting started sample, but you may require additional code for other scenarios.</span></span> <span data-ttu-id="807bd-200">WPF-Steuerelemente rendern die Benutzeroberfläche, indem sie die Felder und Eigenschaften für Ihre Entitäten lesen.</span><span class="sxs-lookup"><span data-stu-id="807bd-200">WPF controls render the UI by reading the fields and properties on your entities.</span></span> <span data-ttu-id="807bd-201">Wenn Sie einen Wert in der Benutzeroberfläche (User Interface, UI) bearbeiten, wird dieser Wert an Ihre Entität übergeben.</span><span class="sxs-lookup"><span data-stu-id="807bd-201">When you edit a value in the user interface (UI), that value is passed to your entity.</span></span> <span data-ttu-id="807bd-202">Wenn Sie den Wert einer Eigenschaft direkt für Ihre Entität ändern, z. B. das Laden aus der Datenbank, spiegelt WPF die Änderungen nicht sofort in der Benutzeroberfläche wider.</span><span class="sxs-lookup"><span data-stu-id="807bd-202">When you change the value of a property directly on your entity, such as loading it from the database, WPF will not immediately reflect the changes in the UI.</span></span> <span data-ttu-id="807bd-203">Die Rendering-Engine muss über die Änderungen benachrichtigt werden.</span><span class="sxs-lookup"><span data-stu-id="807bd-203">The rendering engine must be notified of the changes.</span></span> <span data-ttu-id="807bd-204">Das Projekt hat dies durch manuelles Aufrufen von `Refresh()` erreicht.</span><span class="sxs-lookup"><span data-stu-id="807bd-204">The project did this by manually calling `Refresh()`.</span></span> <span data-ttu-id="807bd-205">Eine einfache Möglichkeit, diese Benachrichtigung zu automatisieren, besteht darin, die [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged)-Schnittstelle zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="807bd-205">An easy way to automate this notification is by implementing the [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) interface.</span></span> <span data-ttu-id="807bd-206">WPF-Komponenten erkennen die Schnittstelle automatisch und registrieren sich für Änderungsereignisse.</span><span class="sxs-lookup"><span data-stu-id="807bd-206">WPF components will automatically detect the interface and register for change events.</span></span> <span data-ttu-id="807bd-207">Die Entität ist für das Auslösen dieser Ereignisse verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="807bd-207">The entity is responsible for raising these events.</span></span>

> [!TIP]
> <span data-ttu-id="807bd-208">Weitere Informationen zum Verarbeiten von Änderungen finden Sie unter: [Implementieren von Benachrichtigungen bei Eigenschaftenänderungen](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span><span class="sxs-lookup"><span data-stu-id="807bd-208">To learn more about how to handle changes, read: [How to implement property change notification](/dotnet/framework/wpf/data/how-to-implement-property-change-notification).</span></span>

## <a name="next-steps"></a><span data-ttu-id="807bd-209">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="807bd-209">Next Steps</span></span>

<span data-ttu-id="807bd-210">Weitere Informationen zum [Konfigurieren eines DbContext](xref:core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="807bd-210">Learn more about [Configuring a DbContext](xref:core/miscellaneous/configuring-dbcontext).</span></span>
