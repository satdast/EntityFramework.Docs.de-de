---
title: 'Verwalten von Migrationen: EF Core'
description: Hinzufügen, entfernen und Verwalten von Datenbankschema Migrationen mit Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: ef5e1b9bb10d6f1cd428db2fee327ec513c3f528
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "95003275"
---
# <a name="managing-migrations"></a>Verwalten von Migrationen

Wenn das Modell geändert wird, werden Migrationen im Rahmen der normalen Entwicklung hinzugefügt und entfernt, und die Migrations Dateien werden in die Quell Code Verwaltung Ihres Projekts eingecheckt. Zum Verwalten von Migrationen müssen Sie zunächst die [EF Core Befehlszeilen Tools](xref:core/cli/index)installieren.

> [!TIP]
> Wenn sich `DbContext` in einer anderen Assembly als das Startprojekt befindet, können Sie die Ziel- und Startprojekte explizit in den Tools für die [Paket-Manager-Konsole](xref:core/cli/powershell#target-and-startup-project) oder die [.NET Core-CLI](xref:core/cli/dotnet#target-project-and-startup-project) angeben.

## <a name="add-a-migration"></a>Hinzufügen einer Migration

Nachdem das Modell geändert wurde, können Sie für diese Änderung eine Migration hinzufügen:

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

***

Der Migrationsname kann wie eine Commitnachricht in einem Versionskontrollsystem verwendet werden. Sie können z. b. einen Namen wie *addblogkreatedtimestamp* auswählen, wenn die Änderung eine neue `CreatedTimestamp` Eigenschaft in der `Blog` Entität ist.

Drei Dateien werden Ihrem Projekt im Verzeichnis **Migrationen** hinzugefügt:

* **XXXXXXXXXXXXXX_AddCreatedTimestamp. cs**-die Haupt Migrations Datei. Enthält die Vorgänge, die zum Durchführen der Migration (in `Up`) und Rückgängigmachen (in `Down`) erforderlich sind.
* **XXXXXXXXXXXXXX_AddCreatedTimestamp. Designer. cs**-die Migrations Metadatendatei. Enthält Informationen, die vom EF verwendet werden.
* **MyContextModelSnapshot.cs** – Eine Momentaufnahme des aktuellen Modells. Diese wird verwendet, um festzustellen, was sich beim Hinzufügen der nächsten Migration geändert hat.

Mit dem Zeitstempel im Dateinamen können Migrationen chronologisch angeordnet werden, sodass Sie den Fortschritt der Änderungen mitverfolgen können.

### <a name="namespaces"></a>Namespaces

Es steht Ihnen frei, Migrationsdateien zu verschieben und ihren Namespace manuell zu ändern. Neue Migrationen werden als gleichgeordnete Elemente der letzten Migration erstellt. Alternativ können Sie das Verzeichnis zum Zeitpunkt der Generierung wie folgt angeben:

#### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --output-dir Your/Directory
```

> [!NOTE]
> In EF Core 5,0 können Sie den Namespace auch unabhängig vom Verzeichnis mithilfe von ändern `--namespace` .

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration InitialCreate -OutputDir Your\Directory
```

> [!NOTE]
> In EF Core 5,0 können Sie den Namespace auch unabhängig vom Verzeichnis mithilfe von ändern `-Namespace` .

***

## <a name="customize-migration-code"></a>Anpassen des Migrationscodes

Während EF Core in der Regel exakte Migrationen erstellt, sollten Sie den Code immer überprüfen und sicherstellen, dass er der gewünschten Änderung entspricht. in einigen Fällen ist dies sogar erforderlich.

### <a name="column-renames"></a>Spalten Umbenennungen

Ein wichtiges Beispiel, bei dem das Anpassen von Migrationen erforderlich ist, ist das Umbenennen einer Eigenschaft. Wenn Sie z. b. eine Eigenschaft von `Name` in umbenennen `FullName` , generiert EF Core die folgende Migration:

```csharp
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

EF Core kann im Allgemeinen nicht wissen, wann eine Spalte gelöscht und ein neues erstellt werden soll (zwei separate Änderungen) und wann eine Spalte umbenannt werden sollte. Wenn die oben genannte Migration unverändert angewendet wird, gehen alle Ihre Kundennamen verloren. Zum Umbenennen einer Spalte ersetzen Sie die oben generierte Migration durch Folgendes:

```csharp
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> Der Einrüstvorgang der Migration warnt, wenn bei einem Vorgang Datenverlust eintreten kann (etwa beim Verwerfen einer Spalte). Wenn diese Warnung angezeigt wird, überprüfen Sie den Migrationscode besonders gründlich auf Genauigkeit.

### <a name="adding-raw-sql"></a>Hinzufügen von RAW SQL

Das Umbenennen einer Spalte kann über eine integrierte API erreicht werden, in vielen Fällen ist dies jedoch nicht möglich. Beispielsweise möchten wir eventuell vorhandene `FirstName` -und- `LastName` Eigenschaften durch eine einzelne neue Eigenschaft ersetzen `FullName` . Die von EF Core generierte Migration sieht wie folgt aus:

```csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);
```

Wie zuvor würde dies zu unerwünschten Datenverlusten führen. Um die Daten aus den alten Spalten zu übertragen, ordnen wir die Migrationen neu an und führen einen unformatierten SQL-Vorgang wie folgt ein:

```csharp
migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET FullName = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

### <a name="arbitrary-changes-via-raw-sql"></a>Beliebige Änderungen über RAW SQL

Unformatierte SQL-Daten können auch zum Verwalten von Datenbankobjekten verwendet werden, die EF Core nicht bekannt sind. Fügen Sie zu diesem Zweck eine Migration hinzu, ohne eine Modell Änderung vorzunehmen. Es wird eine leere Migration generiert, die Sie dann mit unformatierten SQL-Vorgängen auffüllen können.

Beispielsweise wird mit der folgenden Migration eine gespeicherte Prozedur SQL Server erstellt:

```csharp
migrationBuilder.Sql(
@"
    EXEC ('CREATE PROCEDURE getFullName
        @LastName nvarchar(50),
        @FirstName nvarchar(50)
    AS
        RETURN @LastName + @FirstName;')");
```

> [!TIP]
> `EXEC` wird verwendet, wenn eine Anweisung der erste oder einzige in einem SQL-Batch sein muss. Sie kann auch verwendet werden, um Parserfehler in idempotenten Migrations Skripts zu umgehen, die auftreten können, wenn Spalten, auf die verwiesen wird, in einer Tabelle zurzeit nicht

Dies kann verwendet werden, um einen beliebigen Aspekt der Datenbank zu verwalten, einschließlich:

* Gespeicherte Prozeduren
* Volltextsuche
* Funktionen
* Auslöser
* Ansichten

In den meisten Fällen wird jede Migration beim Anwenden von Migrationen von EF Core automatisch in der eigenen Transaktion umschlossen. Leider können einige Migrations Vorgänge nicht innerhalb einer Transaktion in einigen Datenbanken ausgeführt werden. in diesen Fällen können Sie die Transaktion ablehnen, indem Sie an übergeben `suppressTransaction: true` `migrationBuilder.Sql` .

Wenn sich `DbContext` in einer anderen Assembly als das Startprojekt befindet, können Sie die Ziel- und Startprojekte explizit in den Tools für die [Paket-Manager-Konsole](xref:core/cli/powershell#target-and-startup-project) oder die [.NET Core-CLI](xref:core/cli/dotnet#target-project-and-startup-project) angeben.

## <a name="remove-a-migration"></a>Entfernen einer Migration

Es kann vorkommen, dass Sie eine Migration hinzufügen und feststellen, dass Sie zusätzliche Änderungen an Ihrem EF Core-Modell vornehmen müssen, bevor Sie diese durchführen. Um die letzte Migration zu entfernen, verwenden Sie diesen Befehl.

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Remove-Migration
```

***

Nachdem Sie die Migration entfernt haben, können Sie die zusätzlichen Modelländerungen vornehmen und sie erneut hinzufügen.

> [!WARNING]
> Achten Sie darauf, keine Migrationen zu entfernen, die bereits auf Produktionsdatenbanken angewendet wurden. Wenn Sie dies nicht tun, wird verhindert, dass Sie Sie wiederherstellen können, und die Annahmen, die durch nachfolgende Migrationen vorgenommen werden, können unterbrechen

## <a name="listing-migrations"></a>Auflisten von Migrationen

Sie können alle vorhandenen Migrationen wie folgt auflisten:

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations list
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

> [!NOTE]
> Dieser Befehl wurde in EF Core 5,0 eingeführt.

```powershell
Get-Migration
```

***

## <a name="resetting-all-migrations"></a>Zurücksetzen aller Migrationen

In einigen Extremfällen ist es möglicherweise erforderlich, alle Migrationen zu entfernen und zu beginnen. Dies kann problemlos durch Löschen Ihres **Migrations** Ordners und Löschen der Datenbank erfolgen. an diesem Punkt können Sie eine neue anfängliche Migration erstellen, die das gesamte aktuelle Schema enthält.

Es ist auch möglich, alle Migrationen zurückzusetzen und eine einzelne zu erstellen, ohne die Daten zu verlieren. Dies wird mitunter als "Squashing" bezeichnet und umfasst einige manuelle Aufgaben:

* **Migrations** Ordner löschen
* Erstellen einer neuen Migration und Generieren eines SQL-Skripts für dieses
* Löschen Sie in der Datenbank alle Zeilen aus der Migrations Verlaufs Tabelle.
* Fügen Sie eine einzelne Zeile in den Migrations Verlauf ein, um aufzuzeichnen, dass die erste Migration bereits angewendet wurde, da Ihre Tabellen bereits vorhanden sind. Die SQL-Einfügung ist der letzte Vorgang im oben generierten SQL-Skript.

> [!WARNING]
> Alle [benutzerdefinierten Migrations Codes](#customize-migration-code) gehen verloren, wenn der **Migrations** Ordner gelöscht wird.  Alle Anpassungen müssen manuell auf die neue anfängliche Migration angewendet werden, damit Sie beibehalten werden.
