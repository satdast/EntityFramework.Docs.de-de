---
title: Erste Schritte – EF Core
author: rick-anderson
ms.date: 09/17/2019
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: 7ace80bf326395d3b68f3e745100cd45356d7973
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434096"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="3d40a-102">Erste Schritte mit EF Core</span><span class="sxs-lookup"><span data-stu-id="3d40a-102">Getting Started with EF Core</span></span>

<span data-ttu-id="3d40a-103">In diesem Tutorial erstellen Sie eine .NET Core-Konsolen-App, die Datenzugriff auf eine SQLite-Datenbank mithilfe von Entity Framework Core ausführt.</span><span class="sxs-lookup"><span data-stu-id="3d40a-103">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="3d40a-104">Sie können das Tutorial mit Visual Studio unter Windows oder mithilfe von .NET Core-CLI unter Windows, MacOS oder Linux nachvollziehen.</span><span class="sxs-lookup"><span data-stu-id="3d40a-104">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="3d40a-105">[Sehen Sie sich das Beispiel aus diesem Artikel auf GitHub an](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span><span class="sxs-lookup"><span data-stu-id="3d40a-105">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3d40a-106">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="3d40a-106">Prerequisites</span></span>

<span data-ttu-id="3d40a-107">Installieren Sie die folgenden Softwarekomponenten:</span><span class="sxs-lookup"><span data-stu-id="3d40a-107">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="3d40a-108">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="3d40a-108">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="3d40a-109">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="3d40a-109">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="3d40a-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3d40a-110">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3d40a-111">[Visual Studio 2019 Version 16.3 oder höher ](https://www.visualstudio.com/downloads/) mit dieser Workload:</span><span class="sxs-lookup"><span data-stu-id="3d40a-111">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="3d40a-112">**Plattformübergreifende .NET Core-Entwicklung** (unter **Andere Toolsets**)</span><span class="sxs-lookup"><span data-stu-id="3d40a-112">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="3d40a-113">Erstellt ein neues Projekt</span><span class="sxs-lookup"><span data-stu-id="3d40a-113">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="3d40a-114">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="3d40a-114">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="3d40a-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3d40a-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3d40a-116">Öffnen Sie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3d40a-116">Open Visual Studio</span></span>
* <span data-ttu-id="3d40a-117">Klicken Sie auf **Neues Projekt erstellen**.</span><span class="sxs-lookup"><span data-stu-id="3d40a-117">Click **Create a new project**</span></span>
* <span data-ttu-id="3d40a-118">Wählen Sie **Konsolen-App (.NET Core)** mit dem Tag **C#** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="3d40a-118">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="3d40a-119">Geben Sie als Namen **EFGetStarted** ein, und klicken Sie dann auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="3d40a-119">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="3d40a-120">Installieren von Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="3d40a-120">Install Entity Framework Core</span></span>

<span data-ttu-id="3d40a-121">Installieren Sie das Paket für den (oder die) gewünschten EF Core-Datenbankanbieter, um EF Core zu installieren.</span><span class="sxs-lookup"><span data-stu-id="3d40a-121">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="3d40a-122">In diesem Tutorial wird SQLite verwendet, da es auf allen Plattformen ausgeführt werden kann, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="3d40a-122">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="3d40a-123">Eine Liste der verfügbaren Anbieter finden Sie unter [Datenbankanbieter](../providers/index.md).</span><span class="sxs-lookup"><span data-stu-id="3d40a-123">For a list of available providers, see [Database Providers](../providers/index.md).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="3d40a-124">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="3d40a-124">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="3d40a-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3d40a-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3d40a-126">Wählen Sie **Tools > NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="3d40a-126">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="3d40a-127">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="3d40a-127">Run the following commands:</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="3d40a-128">Tipp: Sie können Pakete auch installieren, indem Sie mit der rechten Maustaste auf das Projekt klicken und dann **NuGet-Pakete verwalten** auswählen.</span><span class="sxs-lookup"><span data-stu-id="3d40a-128">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="3d40a-129">Erstellen des Modells</span><span class="sxs-lookup"><span data-stu-id="3d40a-129">Create the model</span></span>

<span data-ttu-id="3d40a-130">Definieren Sie eine Kontextklasse und Entitätsklassen für das Modell.</span><span class="sxs-lookup"><span data-stu-id="3d40a-130">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="3d40a-131">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="3d40a-131">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="3d40a-132">Erstellen Sie im Projektverzeichnis die Datei **Model.cs** mit dem folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="3d40a-132">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="3d40a-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3d40a-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3d40a-134">Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen > Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="3d40a-134">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="3d40a-135">Geben Sie als Namen **Model.cs** ein, und klicken Sie dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="3d40a-135">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="3d40a-136">Ersetzen Sie den Inhalt der Datei durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="3d40a-136">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="3d40a-137">EF Core kann auch [Reverse Engineering](../managing-schemas/scaffolding.md) eines Modells aus einer vorhandenen Datenbank ausführen.</span><span class="sxs-lookup"><span data-stu-id="3d40a-137">EF Core can also [reverse engineer](../managing-schemas/scaffolding.md) a model from an existing database.</span></span>

<span data-ttu-id="3d40a-138">Tipp: Bei dieser Anwendung wurden einige Vorgänge absichtlich vereinfacht, um mehr Klarheit zu bieten.</span><span class="sxs-lookup"><span data-stu-id="3d40a-138">Tip: This application intentionally keeps things simple for clarity.</span></span> <span data-ttu-id="3d40a-139">[Verbindungszeichenfolgen](../miscellaneous/connection-strings.md) sollten bei Produktionsanwendungen niemals im Code gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="3d40a-139">[Connection strings](../miscellaneous/connection-strings.md) should not be stored in the code for production applications.</span></span> <span data-ttu-id="3d40a-140">Es empfiehlt sich auch, die C#-Klassen in eigene Dateien aufzuteilen.</span><span class="sxs-lookup"><span data-stu-id="3d40a-140">You may also want to split each C# class into it's own file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="3d40a-141">Erstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="3d40a-141">Create the database</span></span>

<span data-ttu-id="3d40a-142">Die folgenden Schritte verwenden [Migrationen](xref:core/managing-schemas/migrations/index), um eine Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="3d40a-142">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="3d40a-143">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="3d40a-143">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="3d40a-144">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="3d40a-144">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="3d40a-145">Hierdurch werden [dotnet ef](../miscellaneous/cli/dotnet.md) und das Entwurfspaket installiert, das zum Ausführen des Befehls für ein Projekt erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="3d40a-145">This installs [dotnet ef](../miscellaneous/cli/dotnet.md) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="3d40a-146">Der Befehl `migrations` richtet per Gerüstbau eine Migration ein und erstellt den anfänglichen Tabellensatz für das Modell.</span><span class="sxs-lookup"><span data-stu-id="3d40a-146">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="3d40a-147">Der Befehl `database update` erstellt die Datenbank und wendet die neue Migration auf sie an.</span><span class="sxs-lookup"><span data-stu-id="3d40a-147">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="3d40a-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3d40a-148">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3d40a-149">Führen Sie die folgenden Befehle in der **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="3d40a-149">Run the following commands in **Package Manager Console**</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="3d40a-150">Dadurch werden die [PMC-Tools für EF Core](../miscellaneous/cli/powershell.md) installiert.</span><span class="sxs-lookup"><span data-stu-id="3d40a-150">This installs the [PMC tools for EF Core](../miscellaneous/cli/powershell.md).</span></span> <span data-ttu-id="3d40a-151">Der Befehl `Add-Migration` richtet per Gerüstbau eine Migration ein und erstellt den anfänglichen Tabellensatz für das Modell.</span><span class="sxs-lookup"><span data-stu-id="3d40a-151">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="3d40a-152">Der Befehl `Update-Database` erstellt die Datenbank und wendet die neue Migration auf sie an.</span><span class="sxs-lookup"><span data-stu-id="3d40a-152">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="3d40a-153">Erstellen, Lesen, Aktualisieren und Löschen</span><span class="sxs-lookup"><span data-stu-id="3d40a-153">Create, read, update & delete</span></span>

* <span data-ttu-id="3d40a-154">Öffnen Sie die Datei *Program.cs*, und ersetzen Sie den Inhalt durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="3d40a-154">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="3d40a-155">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="3d40a-155">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="3d40a-156">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="3d40a-156">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="3d40a-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3d40a-157">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3d40a-158">Visual Studio verwendet beim Ausführen von .NET Core-Konsolen-Apps ein inkonsistentes Arbeitsverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="3d40a-158">Visual Studio uses an inconsistent working directory when running .NET Core console apps.</span></span> <span data-ttu-id="3d40a-159">(Siehe [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619).) Dies führt dazu, dass eine Ausnahme ausgelöst wird: *no such table: Blogs* (Tabelle nicht vorhanden: Blogs).</span><span class="sxs-lookup"><span data-stu-id="3d40a-159">(see [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) This results in an exception being thrown: *no such table: Blogs*.</span></span> <span data-ttu-id="3d40a-160">So aktualisieren Sie das Arbeitsverzeichnis:</span><span class="sxs-lookup"><span data-stu-id="3d40a-160">To update the working directory:</span></span>

* <span data-ttu-id="3d40a-161">Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Projektdatei bearbeiten** aus.</span><span class="sxs-lookup"><span data-stu-id="3d40a-161">Right-click on the project and select **Edit Project File**</span></span>
* <span data-ttu-id="3d40a-162">Fügen Sie direkt unterhalb der Eigenschaft *TargetFramework* Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="3d40a-162">Just below the *TargetFramework* property, add the following:</span></span>

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* <span data-ttu-id="3d40a-163">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="3d40a-163">Save the file</span></span>

<span data-ttu-id="3d40a-164">Nun können Sie die App ausführen:</span><span class="sxs-lookup"><span data-stu-id="3d40a-164">Now you can run the app:</span></span>

* <span data-ttu-id="3d40a-165">Klicken Sie auf **Debuggen > Ohne Debuggen starten**.</span><span class="sxs-lookup"><span data-stu-id="3d40a-165">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="3d40a-166">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="3d40a-166">Next steps</span></span>

* <span data-ttu-id="3d40a-167">Befolgen Sie das [ASP.NET Core-Tutorial](/aspnet/core/data/ef-rp/intro), um EF Core in einer Web-App zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="3d40a-167">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="3d40a-168">Weitere Informationen zu [LINQ-Abfrageausdrücken](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="3d40a-168">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="3d40a-169">[Konfigurieren Sie das Modell](xref:core/modeling/index), um Aspekte wie [required](xref:core/modeling/entity-properties#required-and-optional-properties) (erforderlich) und [maximum length](xref:core/modeling/entity-properties#maximum-length) (maximale Länge) anzugeben.</span><span class="sxs-lookup"><span data-stu-id="3d40a-169">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="3d40a-170">Verwenden Sie [Migrationen](xref:core/managing-schemas/migrations/index) zum Aktualisieren des Datenbankschemas nach dem Ändern des Modells.</span><span class="sxs-lookup"><span data-stu-id="3d40a-170">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
