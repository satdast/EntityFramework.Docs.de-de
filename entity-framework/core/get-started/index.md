---
title: Erste Schritte – EF Core
description: Tutorial zu den ersten Schritten mit Entity Framework Core
author: rick-anderson
ms.date: 09/17/2019
uid: core/get-started/index
ms.openlocfilehash: 132586aa41916d94ebef156330cc553a789ed5a7
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062112"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="2357a-103">Erste Schritte mit EF Core</span><span class="sxs-lookup"><span data-stu-id="2357a-103">Getting Started with EF Core</span></span>

<span data-ttu-id="2357a-104">In diesem Tutorial erstellen Sie eine .NET Core-Konsolen-App, die Datenzugriff auf eine SQLite-Datenbank mithilfe von Entity Framework Core ausführt.</span><span class="sxs-lookup"><span data-stu-id="2357a-104">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="2357a-105">Sie können das Tutorial mit Visual Studio unter Windows oder mithilfe von .NET Core-CLI unter Windows, MacOS oder Linux nachvollziehen.</span><span class="sxs-lookup"><span data-stu-id="2357a-105">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="2357a-106">[Sehen Sie sich das Beispiel aus diesem Artikel auf GitHub an](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span><span class="sxs-lookup"><span data-stu-id="2357a-106">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2357a-107">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="2357a-107">Prerequisites</span></span>

<span data-ttu-id="2357a-108">Installieren Sie die folgenden Softwarekomponenten:</span><span class="sxs-lookup"><span data-stu-id="2357a-108">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="2357a-109">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="2357a-109">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="2357a-110">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span><span class="sxs-lookup"><span data-stu-id="2357a-110">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="2357a-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2357a-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2357a-112">[Visual Studio 2019 Version 16.3 oder höher ](https://www.visualstudio.com/downloads/) mit dieser Workload:</span><span class="sxs-lookup"><span data-stu-id="2357a-112">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="2357a-113">**Plattformübergreifende .NET Core-Entwicklung** (unter **Andere Toolsets**)</span><span class="sxs-lookup"><span data-stu-id="2357a-113">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="2357a-114">Erstellt ein neues Projekt</span><span class="sxs-lookup"><span data-stu-id="2357a-114">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="2357a-115">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="2357a-115">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="2357a-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2357a-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2357a-117">Öffnen Sie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="2357a-117">Open Visual Studio</span></span>
* <span data-ttu-id="2357a-118">Klicken Sie auf **Neues Projekt erstellen**.</span><span class="sxs-lookup"><span data-stu-id="2357a-118">Click **Create a new project**</span></span>
* <span data-ttu-id="2357a-119">Wählen Sie **Konsolen-App (.NET Core)** mit dem Tag **C#** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="2357a-119">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="2357a-120">Geben Sie als Namen **EFGetStarted** ein, und klicken Sie dann auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="2357a-120">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="2357a-121">Installieren von Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="2357a-121">Install Entity Framework Core</span></span>

<span data-ttu-id="2357a-122">Installieren Sie das Paket für den (oder die) gewünschten EF Core-Datenbankanbieter, um EF Core zu installieren.</span><span class="sxs-lookup"><span data-stu-id="2357a-122">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="2357a-123">In diesem Tutorial wird SQLite verwendet, da es auf allen Plattformen ausgeführt werden kann, die .NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2357a-123">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="2357a-124">Eine Liste der verfügbaren Anbieter finden Sie unter [Datenbankanbieter](xref:core/providers/index).</span><span class="sxs-lookup"><span data-stu-id="2357a-124">For a list of available providers, see [Database Providers](xref:core/providers/index).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="2357a-125">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="2357a-125">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="2357a-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2357a-126">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2357a-127">Wählen Sie **Tools > NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="2357a-127">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="2357a-128">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="2357a-128">Run the following commands:</span></span>

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="2357a-129">Tipp: Sie können Pakete auch installieren, indem Sie mit der rechten Maustaste auf das Projekt klicken und dann **NuGet-Pakete verwalten** auswählen.</span><span class="sxs-lookup"><span data-stu-id="2357a-129">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="2357a-130">Erstellen des Modells</span><span class="sxs-lookup"><span data-stu-id="2357a-130">Create the model</span></span>

<span data-ttu-id="2357a-131">Definieren Sie eine Kontextklasse und Entitätsklassen für das Modell.</span><span class="sxs-lookup"><span data-stu-id="2357a-131">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="2357a-132">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="2357a-132">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="2357a-133">Erstellen Sie im Projektverzeichnis die Datei **Model.cs** mit dem folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="2357a-133">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="2357a-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2357a-134">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2357a-135">Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen > Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="2357a-135">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="2357a-136">Geben Sie als Namen **Model.cs** ein, und klicken Sie dann auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="2357a-136">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="2357a-137">Ersetzen Sie den Inhalt der Datei durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="2357a-137">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="2357a-138">EF Core kann auch [Reverse Engineering](xref:core/managing-schemas/scaffolding) eines Modells aus einer vorhandenen Datenbank ausführen.</span><span class="sxs-lookup"><span data-stu-id="2357a-138">EF Core can also [reverse engineer](xref:core/managing-schemas/scaffolding) a model from an existing database.</span></span>

<span data-ttu-id="2357a-139">Tipp: Bei dieser Anwendung wurden einige Vorgänge absichtlich vereinfacht, um mehr Klarheit zu bieten.</span><span class="sxs-lookup"><span data-stu-id="2357a-139">Tip: This application intentionally keeps things simple for clarity.</span></span> <span data-ttu-id="2357a-140">[Verbindungszeichenfolgen](xref:core/miscellaneous/connection-strings) sollten bei Produktionsanwendungen niemals im Code gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="2357a-140">[Connection strings](xref:core/miscellaneous/connection-strings) should not be stored in the code for production applications.</span></span> <span data-ttu-id="2357a-141">Es empfiehlt sich auch, die C#-Klassen in eigene Dateien aufzuteilen.</span><span class="sxs-lookup"><span data-stu-id="2357a-141">You may also want to split each C# class into its own file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="2357a-142">Erstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="2357a-142">Create the database</span></span>

<span data-ttu-id="2357a-143">Die folgenden Schritte verwenden [Migrationen](xref:core/managing-schemas/migrations/index), um eine Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2357a-143">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="2357a-144">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="2357a-144">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="2357a-145">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="2357a-145">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="2357a-146">Hierdurch werden [dotnet ef](xref:core/miscellaneous/cli/dotnet) und das Entwurfspaket installiert, das zum Ausführen des Befehls für ein Projekt erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="2357a-146">This installs [dotnet ef](xref:core/miscellaneous/cli/dotnet) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="2357a-147">Der Befehl `migrations` richtet per Gerüstbau eine Migration ein und erstellt den anfänglichen Tabellensatz für das Modell.</span><span class="sxs-lookup"><span data-stu-id="2357a-147">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="2357a-148">Der Befehl `database update` erstellt die Datenbank und wendet die neue Migration auf sie an.</span><span class="sxs-lookup"><span data-stu-id="2357a-148">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="2357a-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2357a-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2357a-150">Führen Sie die folgenden Befehle in der **Paket-Manager-Konsole (PMC)** aus.</span><span class="sxs-lookup"><span data-stu-id="2357a-150">Run the following commands in **Package Manager Console (PMC)**</span></span>

  ```powershell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="2357a-151">Dadurch werden die [PMC-Tools für EF Core](xref:core/miscellaneous/cli/powershell) installiert.</span><span class="sxs-lookup"><span data-stu-id="2357a-151">This installs the [PMC tools for EF Core](xref:core/miscellaneous/cli/powershell).</span></span> <span data-ttu-id="2357a-152">Der Befehl `Add-Migration` richtet per Gerüstbau eine Migration ein und erstellt den anfänglichen Tabellensatz für das Modell.</span><span class="sxs-lookup"><span data-stu-id="2357a-152">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="2357a-153">Der Befehl `Update-Database` erstellt die Datenbank und wendet die neue Migration auf sie an.</span><span class="sxs-lookup"><span data-stu-id="2357a-153">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="2357a-154">Erstellen, Lesen, Aktualisieren und Löschen</span><span class="sxs-lookup"><span data-stu-id="2357a-154">Create, read, update & delete</span></span>

* <span data-ttu-id="2357a-155">Öffnen Sie die Datei *Program.cs*, und ersetzen Sie den Inhalt durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="2357a-155">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="2357a-156">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="2357a-156">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="2357a-157">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="2357a-157">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="2357a-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2357a-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2357a-159">Visual Studio verwendet beim Ausführen von .NET Core-Konsolen-Apps ein inkonsistentes Arbeitsverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="2357a-159">Visual Studio uses an inconsistent working directory when running .NET Core console apps.</span></span> <span data-ttu-id="2357a-160">(Siehe [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619).) Dies führt dazu, dass eine Ausnahme ausgelöst wird: *no such table: Blogs* (Tabelle nicht vorhanden: Blogs).</span><span class="sxs-lookup"><span data-stu-id="2357a-160">(see [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) This results in an exception being thrown: *no such table: Blogs*.</span></span> <span data-ttu-id="2357a-161">So aktualisieren Sie das Arbeitsverzeichnis:</span><span class="sxs-lookup"><span data-stu-id="2357a-161">To update the working directory:</span></span>

* <span data-ttu-id="2357a-162">Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Projektdatei bearbeiten** aus.</span><span class="sxs-lookup"><span data-stu-id="2357a-162">Right-click on the project and select **Edit Project File**</span></span>
* <span data-ttu-id="2357a-163">Fügen Sie direkt unterhalb der Eigenschaft *TargetFramework* Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="2357a-163">Just below the *TargetFramework* property, add the following:</span></span>

  ```xml
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* <span data-ttu-id="2357a-164">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="2357a-164">Save the file</span></span>

<span data-ttu-id="2357a-165">Nun können Sie die App ausführen:</span><span class="sxs-lookup"><span data-stu-id="2357a-165">Now you can run the app:</span></span>

* <span data-ttu-id="2357a-166">Klicken Sie auf **Debuggen > Ohne Debuggen starten**.</span><span class="sxs-lookup"><span data-stu-id="2357a-166">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="2357a-167">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="2357a-167">Next steps</span></span>

* <span data-ttu-id="2357a-168">Befolgen Sie das [ASP.NET Core-Tutorial](/aspnet/core/data/ef-rp/intro), um EF Core in einer Web-App zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="2357a-168">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="2357a-169">Weitere Informationen zu [LINQ-Abfrageausdrücken](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="2357a-169">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="2357a-170">[Konfigurieren Sie das Modell](xref:core/modeling/index), um Aspekte wie [required](xref:core/modeling/entity-properties#required-and-optional-properties) (erforderlich) und [maximum length](xref:core/modeling/entity-properties#maximum-length) (maximale Länge) anzugeben.</span><span class="sxs-lookup"><span data-stu-id="2357a-170">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="2357a-171">Verwenden Sie [Migrationen](xref:core/managing-schemas/migrations/index) zum Aktualisieren des Datenbankschemas nach dem Ändern des Modells.</span><span class="sxs-lookup"><span data-stu-id="2357a-171">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
