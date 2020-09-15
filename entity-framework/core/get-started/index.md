---
title: Erste Schritte – EF Core
description: Tutorial zu den ersten Schritten mit Entity Framework Core
author: rick-anderson
ms.date: 09/17/2019
uid: core/get-started/index
ms.openlocfilehash: 9f0bb1eb99cb7f4cb7542c444ad86480917bdd0f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071978"
---
# <a name="getting-started-with-ef-core"></a>Erste Schritte mit EF Core

In diesem Tutorial erstellen Sie eine .NET Core-Konsolen-App, die Datenzugriff auf eine SQLite-Datenbank mithilfe von Entity Framework Core ausführt.

Sie können das Tutorial mit Visual Studio unter Windows oder mithilfe von .NET Core-CLI unter Windows, MacOS oder Linux nachvollziehen.

[Sehen Sie sich das Beispiel aus diesem Artikel auf GitHub an](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie die folgenden Softwarekomponenten:

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

* [.NET Core SDK](https://www.microsoft.com/net/download/core).

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 Version 16.3 oder höher ](https://www.visualstudio.com/downloads/) mit dieser Workload:
  * **Plattformübergreifende .NET Core-Entwicklung** (unter **Andere Toolsets**)

---

## <a name="create-a-new-project"></a>Erstellt ein neues Projekt

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Öffnen Sie Visual Studio.
* Klicken Sie auf **Neues Projekt erstellen**.
* Wählen Sie **Konsolen-App (.NET Core)** mit dem Tag **C#** aus, und klicken Sie auf **Weiter**.
* Geben Sie als Namen **EFGetStarted** ein, und klicken Sie dann auf **Erstellen**.

---

## <a name="install-entity-framework-core"></a>Installieren von Entity Framework Core

Installieren Sie das Paket für den (oder die) gewünschten EF Core-Datenbankanbieter, um EF Core zu installieren. In diesem Tutorial wird SQLite verwendet, da es auf allen Plattformen ausgeführt werden kann, die .NET Core unterstützt. Eine Liste der verfügbaren Anbieter finden Sie unter [Datenbankanbieter](xref:core/providers/index).

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Wählen Sie **Tools > NuGet-Paket-Manager > Paket-Manager-Konsole** aus.
* Führen Sie die folgenden Befehle aus:

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

Tipp: Sie können Pakete auch installieren, indem Sie mit der rechten Maustaste auf das Projekt klicken und dann **NuGet-Pakete verwalten** auswählen.

---

## <a name="create-the-model"></a>Erstellen des Modells

Definieren Sie eine Kontextklasse und Entitätsklassen für das Modell.

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

* Erstellen Sie im Projektverzeichnis die Datei **Model.cs** mit dem folgenden Code.

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen > Klasse** aus.
* Geben Sie als Namen **Model.cs** ein, und klicken Sie dann auf **Hinzufügen**.
* Ersetzen Sie den Inhalt der Datei durch den folgenden Code.

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

EF Core kann auch [Reverse Engineering](xref:core/managing-schemas/scaffolding) eines Modells aus einer vorhandenen Datenbank ausführen.

Tipp: Bei dieser Anwendung wurden einige Vorgänge absichtlich vereinfacht, um mehr Klarheit zu bieten. [Verbindungszeichenfolgen](xref:core/miscellaneous/connection-strings) sollten bei Produktionsanwendungen niemals im Code gespeichert werden. Es empfiehlt sich auch, die C#-Klassen in eigene Dateien aufzuteilen.

## <a name="create-the-database"></a>Erstellen der Datenbank

Die folgenden Schritte verwenden [Migrationen](xref:core/managing-schemas/migrations/index), um eine Datenbank zu erstellen.

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

* Führen Sie die folgenden Befehle aus:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  Hierdurch werden [dotnet ef](xref:core/miscellaneous/cli/dotnet) und das Entwurfspaket installiert, das zum Ausführen des Befehls für ein Projekt erforderlich ist. Der Befehl `migrations` richtet per Gerüstbau eine Migration ein und erstellt den anfänglichen Tabellensatz für das Modell. Der Befehl `database update` erstellt die Datenbank und wendet die neue Migration auf sie an.

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Führen Sie die folgenden Befehle in der **Paket-Manager-Konsole (PMC)** aus.

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  Dadurch werden die [PMC-Tools für EF Core](xref:core/miscellaneous/cli/powershell) installiert. Der Befehl `Add-Migration` richtet per Gerüstbau eine Migration ein und erstellt den anfänglichen Tabellensatz für das Modell. Der Befehl `Update-Database` erstellt die Datenbank und wendet die neue Migration auf sie an.

---

## <a name="create-read-update--delete"></a>Erstellen, Lesen, Aktualisieren und Löschen

* Öffnen Sie die Datei *Program.cs*, und ersetzen Sie den Inhalt durch den folgenden Code:

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a>Ausführen der App

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio verwendet beim Ausführen von .NET Core-Konsolen-Apps ein inkonsistentes Arbeitsverzeichnis. (Siehe [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619).) Dies führt dazu, dass eine Ausnahme ausgelöst wird: *no such table: Blogs* (Tabelle nicht vorhanden: Blogs). So aktualisieren Sie das Arbeitsverzeichnis:

* Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Projektdatei bearbeiten** aus.
* Fügen Sie direkt unterhalb der Eigenschaft *TargetFramework* Folgendes hinzu:

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* Speichern Sie die Datei.

Nun können Sie die App ausführen:

* Klicken Sie auf **Debuggen > Ohne Debuggen starten**.

---

## <a name="next-steps"></a>Nächste Schritte

* Befolgen Sie das [ASP.NET Core-Tutorial](/aspnet/core/data/ef-rp/intro), um EF Core in einer Web-App zu verwenden.
* Weitere Informationen zu [LINQ-Abfrageausdrücken](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)
* [Konfigurieren Sie das Modell](xref:core/modeling/index), um Aspekte wie [required](xref:core/modeling/entity-properties#required-and-optional-properties) (erforderlich) und [maximum length](xref:core/modeling/entity-properties#maximum-length) (maximale Länge) anzugeben.
* Verwenden Sie [Migrationen](xref:core/managing-schemas/migrations/index) zum Aktualisieren des Datenbankschemas nach dem Ändern des Modells.
