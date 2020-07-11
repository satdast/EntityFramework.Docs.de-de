---
title: 'Verwalten von Migrationen: EF Core'
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: e52d3680360a1e83e05f04650c735c5a67680094
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238724"
---
# <a name="managing-migrations"></a><span data-ttu-id="f1ee5-102">Verwalten von Migrationen</span><span class="sxs-lookup"><span data-stu-id="f1ee5-102">Managing Migrations</span></span>

<span data-ttu-id="f1ee5-103">Wenn das Modell geändert wird, werden Migrationen im Rahmen der normalen Entwicklung hinzugefügt und entfernt, und die Migrations Dateien werden in die Quell Code Verwaltung Ihres Projekts eingecheckt.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-103">As your model changes, migrations are added and removed as part of normal development, and the migration files are checked into your project's source control.</span></span> <span data-ttu-id="f1ee5-104">Zum Verwalten von Migrationen müssen Sie zunächst die [EF Core Befehlszeilen Tools](xref:core/miscellaneous/cli/index)installieren.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-104">To manage migrations, you must first install the [EF Core command-line tools](xref:core/miscellaneous/cli/index).</span></span>

> [!TIP]
> <span data-ttu-id="f1ee5-105">Wenn sich `DbContext` in einer anderen Assembly als das Startprojekt befindet, können Sie die Ziel- und Startprojekte explizit in den Tools für die [Paket-Manager-Konsole](xref:core/miscellaneous/cli/powershell#target-and-startup-project) oder die [.NET Core-CLI](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project) angeben.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-105">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="f1ee5-106">Hinzufügen einer Migration</span><span class="sxs-lookup"><span data-stu-id="f1ee5-106">Add a migration</span></span>

<span data-ttu-id="f1ee5-107">Nachdem das Modell geändert wurde, können Sie für diese Änderung eine Migration hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="f1ee5-107">After your model has been changed, you can add a migration for that change:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f1ee5-108">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="f1ee5-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[<span data-ttu-id="f1ee5-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1ee5-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="f1ee5-110">Der Migrationsname kann wie eine Commitnachricht in einem Versionskontrollsystem verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-110">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="f1ee5-111">Sie können z. b. einen Namen wie *addblogkreatedtimestamp* auswählen, wenn die Änderung eine neue `CreatedTimestamp` Eigenschaft in der `Blog` Entität ist.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-111">For example, you might choose a name like *AddBlogCreatedTimestamp* if the change is a new `CreatedTimestamp` property on your `Blog` entity.</span></span>

<span data-ttu-id="f1ee5-112">Drei Dateien werden Ihrem Projekt im Verzeichnis **Migrationen** hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="f1ee5-112">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="f1ee5-113">**XXXXXXXXXXXXXX_AddCreatedTimestamp. cs**-die Haupt Migrations Datei.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-113">**XXXXXXXXXXXXXX_AddCreatedTimestamp.cs**--The main migrations file.</span></span> <span data-ttu-id="f1ee5-114">Enthält die Vorgänge, die zum Durchführen der Migration (in `Up`) und Rückgängigmachen (in `Down`) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-114">Contains the operations necessary to apply the migration (in `Up`) and to revert it (in `Down`).</span></span>
* <span data-ttu-id="f1ee5-115">**XXXXXXXXXXXXXX_AddCreatedTimestamp. Designer. cs**-die Migrations Metadatendatei.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-115">**XXXXXXXXXXXXXX_AddCreatedTimestamp.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="f1ee5-116">Enthält Informationen, die vom EF verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-116">Contains information used by EF.</span></span>
* <span data-ttu-id="f1ee5-117">**MyContextModelSnapshot.cs** – Eine Momentaufnahme des aktuellen Modells.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-117">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="f1ee5-118">Diese wird verwendet, um festzustellen, was sich beim Hinzufügen der nächsten Migration geändert hat.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-118">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="f1ee5-119">Mit dem Zeitstempel im Dateinamen können Migrationen chronologisch angeordnet werden, sodass Sie den Fortschritt der Änderungen mitverfolgen können.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-119">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

### <a name="namespaces"></a><span data-ttu-id="f1ee5-120">Namespaces</span><span class="sxs-lookup"><span data-stu-id="f1ee5-120">Namespaces</span></span>

<span data-ttu-id="f1ee5-121">Es steht Ihnen frei, Migrationsdateien zu verschieben und ihren Namespace manuell zu ändern.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-121">You are free to move Migrations files and change their namespace manually.</span></span> <span data-ttu-id="f1ee5-122">Neue Migrationen werden als gleichgeordnete Elemente der letzten Migration erstellt.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-122">New migrations are created as siblings of the last migration.</span></span> <span data-ttu-id="f1ee5-123">Alternativ können Sie den Namespace zum Zeitpunkt der Generierung wie folgt angeben:</span><span class="sxs-lookup"><span data-stu-id="f1ee5-123">Alternatively, you can specify the namespace at generation time as follows:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f1ee5-124">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="f1ee5-124">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --namespace Your.Namespace
```

### <a name="visual-studio"></a>[<span data-ttu-id="f1ee5-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1ee5-125">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate -Namespace Your.Namespace
```

***

## <a name="customize-migration-code"></a><span data-ttu-id="f1ee5-126">Anpassen des Migrationscodes</span><span class="sxs-lookup"><span data-stu-id="f1ee5-126">Customize migration code</span></span>

<span data-ttu-id="f1ee5-127">Während EF Core in der Regel exakte Migrationen erstellt, sollten Sie den Code immer überprüfen und sicherstellen, dass er der gewünschten Änderung entspricht. in einigen Fällen ist dies sogar erforderlich.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-127">While EF Core generally creates accurate migrations, you should always review the code and make sure it corresponds to the desired change; in some cases, it is even necessary to do so.</span></span>

### <a name="column-renames"></a><span data-ttu-id="f1ee5-128">Spalten Umbenennungen</span><span class="sxs-lookup"><span data-stu-id="f1ee5-128">Column renames</span></span>

<span data-ttu-id="f1ee5-129">Ein wichtiges Beispiel, bei dem das Anpassen von Migrationen erforderlich ist, ist das Umbenennen einer Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-129">One notable example where customizing migrations is required is when renaming a property.</span></span> <span data-ttu-id="f1ee5-130">Wenn Sie z. b. eine Eigenschaft von `Name` in umbenennen `FullName` , generiert EF Core die folgende Migration:</span><span class="sxs-lookup"><span data-stu-id="f1ee5-130">For example, if you rename a property from `Name` to `FullName`, EF Core will generate the following migration:</span></span>

```c#
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

<span data-ttu-id="f1ee5-131">EF Core kann im Allgemeinen nicht wissen, wann eine Spalte gelöscht und ein neues erstellt werden soll (zwei separate Änderungen) und wann eine Spalte umbenannt werden sollte.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-131">EF Core is generally unable to know when the intention is to drop a column and create a new one (two separate changes), and when a column should be renamed.</span></span> <span data-ttu-id="f1ee5-132">Wenn die oben genannte Migration unverändert angewendet wird, gehen alle Ihre Kundennamen verloren.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-132">If the above migration is applied as-is, all your customer names will be lost.</span></span> <span data-ttu-id="f1ee5-133">Zum Umbenennen einer Spalte ersetzen Sie die oben generierte Migration durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f1ee5-133">To rename a column, replace the above generated migration with the following:</span></span>

```c#
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> <span data-ttu-id="f1ee5-134">Der Einrüstvorgang der Migration warnt, wenn bei einem Vorgang Datenverlust eintreten kann (etwa beim Verwerfen einer Spalte).</span><span class="sxs-lookup"><span data-stu-id="f1ee5-134">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="f1ee5-135">Wenn diese Warnung angezeigt wird, überprüfen Sie den Migrationscode besonders gründlich auf Genauigkeit.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-135">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

### <a name="adding-raw-sql"></a><span data-ttu-id="f1ee5-136">Hinzufügen von RAW SQL</span><span class="sxs-lookup"><span data-stu-id="f1ee5-136">Adding raw SQL</span></span>

<span data-ttu-id="f1ee5-137">Das Umbenennen einer Spalte kann über eine integrierte API erreicht werden, in vielen Fällen ist dies jedoch nicht möglich.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-137">While renaming a column can be achieved via a built-in API, in many cases that is not possible.</span></span> <span data-ttu-id="f1ee5-138">Beispielsweise möchten wir eventuell vorhandene `FirstName` -und- `LastColumn` Eigenschaften durch eine einzelne neue Eigenschaft ersetzen `FullName` .</span><span class="sxs-lookup"><span data-stu-id="f1ee5-138">For example, we may want to replace existing `FirstName` and `LastColumn` properties with a single, new `FullName` property.</span></span> <span data-ttu-id="f1ee5-139">Die von EF Core generierte Migration sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="f1ee5-139">The migration generated by EF Core will be the following:</span></span>

``` csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);
```

<span data-ttu-id="f1ee5-140">Wie zuvor würde dies zu unerwünschten Datenverlusten führen.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-140">As before, this would cause unwanted data loss.</span></span> <span data-ttu-id="f1ee5-141">Um die Daten aus den alten Spalten zu übertragen, ordnen wir die Migrationen neu an und führen einen unformatierten SQL-Vorgang wie folgt ein:</span><span class="sxs-lookup"><span data-stu-id="f1ee5-141">To transfer the data from the old columns, we rearrange the migrations and introduce a raw SQL operation as follows:</span></span>

``` csharp
migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET Name = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

### <a name="arbitrary-changes-via-raw-sql"></a><span data-ttu-id="f1ee5-142">Beliebige Änderungen über RAW SQL</span><span class="sxs-lookup"><span data-stu-id="f1ee5-142">Arbitrary changes via raw SQL</span></span>

<span data-ttu-id="f1ee5-143">Unformatierte SQL-Daten können auch zum Verwalten von Datenbankobjekten verwendet werden, die EF Core nicht bekannt sind.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-143">Raw SQL can also be used to manage database objects that EF Core isn't aware of.</span></span> <span data-ttu-id="f1ee5-144">Fügen Sie zu diesem Zweck eine Migration hinzu, ohne eine Modell Änderung vorzunehmen. Es wird eine leere Migration generiert, die Sie dann mit unformatierten SQL-Vorgängen auffüllen können.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-144">To do this, add a migration without making any model change; an empty migration will be generated, which you can then populate with raw SQL operations.</span></span>

<span data-ttu-id="f1ee5-145">Beispielsweise wird mit der folgenden Migration eine gespeicherte Prozedur SQL Server erstellt:</span><span class="sxs-lookup"><span data-stu-id="f1ee5-145">For example, the following migration creates a SQL Server stored procedure:</span></span>

```c#
migrationBuilder.Sql(
@"
    EXEC ('CREATE PROCEDURE getFullName
        @LastName nvarchar(50),
        @FirstName nvarchar(50)
    AS
        RETURN @LastName + @FirstName;')");
```

<span data-ttu-id="f1ee5-146">Dies kann verwendet werden, um einen beliebigen Aspekt der Datenbank zu verwalten, einschließlich:</span><span class="sxs-lookup"><span data-stu-id="f1ee5-146">This can be used to manage any aspect of your database, including:</span></span>

* <span data-ttu-id="f1ee5-147">Gespeicherte Prozeduren</span><span class="sxs-lookup"><span data-stu-id="f1ee5-147">Stored procedures</span></span>
* <span data-ttu-id="f1ee5-148">Volltextsuche</span><span class="sxs-lookup"><span data-stu-id="f1ee5-148">Full-Text Search</span></span>
* <span data-ttu-id="f1ee5-149">Funktionen</span><span class="sxs-lookup"><span data-stu-id="f1ee5-149">Functions</span></span>
* <span data-ttu-id="f1ee5-150">Trigger</span><span class="sxs-lookup"><span data-stu-id="f1ee5-150">Triggers</span></span>
* <span data-ttu-id="f1ee5-151">Ansichten</span><span class="sxs-lookup"><span data-stu-id="f1ee5-151">Views</span></span>

<span data-ttu-id="f1ee5-152">In den meisten Fällen wird jede Migration beim Anwenden von Migrationen von EF Core automatisch in der eigenen Transaktion umschlossen.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-152">In most cases, EF Core will automatically wrap each migration in its own transaction when applying migrations.</span></span> <span data-ttu-id="f1ee5-153">Leider können einige Migrations Vorgänge nicht innerhalb einer Transaktion in einigen Datenbanken ausgeführt werden. in diesen Fällen können Sie die Transaktion ablehnen, indem Sie an übergeben `suppressTransaction: true` `migrationBuilder.Sql` .</span><span class="sxs-lookup"><span data-stu-id="f1ee5-153">Unfortunately, some migrations operations cannot be performed within a transaction in some databases; for these cases, you may opt out of the transaction by passing `suppressTransaction: true` to `migrationBuilder.Sql`.</span></span>

<span data-ttu-id="f1ee5-154">Wenn sich `DbContext` in einer anderen Assembly als das Startprojekt befindet, können Sie die Ziel- und Startprojekte explizit in den Tools für die [Paket-Manager-Konsole](xref:core/miscellaneous/cli/powershell#target-and-startup-project) oder die [.NET Core-CLI](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project) angeben.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-154">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="remove-a-migration"></a><span data-ttu-id="f1ee5-155">Entfernen einer Migration</span><span class="sxs-lookup"><span data-stu-id="f1ee5-155">Remove a migration</span></span>

<span data-ttu-id="f1ee5-156">Es kann vorkommen, dass Sie eine Migration hinzufügen und feststellen, dass Sie zusätzliche Änderungen an Ihrem EF Core-Modell vornehmen müssen, bevor Sie diese durchführen.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-156">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="f1ee5-157">Um die letzte Migration zu entfernen, verwenden Sie diesen Befehl.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-157">To remove the last migration, use this command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f1ee5-158">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="f1ee5-158">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[<span data-ttu-id="f1ee5-159">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1ee5-159">Visual Studio</span></span>](#tab/vs)

``` powershell
Remove-Migration
```

***

<span data-ttu-id="f1ee5-160">Nachdem Sie die Migration entfernt haben, können Sie die zusätzlichen Modelländerungen vornehmen und sie erneut hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-160">After removing the migration, you can make the additional model changes and add it again.</span></span>

> [!WARNING]
> <span data-ttu-id="f1ee5-161">Achten Sie darauf, keine Migrationen zu entfernen, die bereits auf Produktionsdatenbanken angewendet wurden.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-161">Take care not to remove any migrations which are already applied to production databases.</span></span> <span data-ttu-id="f1ee5-162">Wenn Sie dies nicht tun, wird verhindert, dass Sie Sie wiederherstellen können, und die Annahmen, die durch nachfolgende Migrationen vorgenommen werden, können unterbrechen</span><span class="sxs-lookup"><span data-stu-id="f1ee5-162">Not doing so will prevent you from being able to revert it, and may break the assumptions made by subsequent migrations.</span></span>

## <a name="listing-migrations"></a><span data-ttu-id="f1ee5-163">Auflisten von Migrationen</span><span class="sxs-lookup"><span data-stu-id="f1ee5-163">Listing migrations</span></span>

<span data-ttu-id="f1ee5-164">Sie können alle vorhandenen Migrationen wie folgt auflisten:</span><span class="sxs-lookup"><span data-stu-id="f1ee5-164">You can list all existing migrations as follows:</span></span>

```dotnetcli
dotnet ef migrations list
```

## <a name="resetting-all-migrations"></a><span data-ttu-id="f1ee5-165">Zurücksetzen aller Migrationen</span><span class="sxs-lookup"><span data-stu-id="f1ee5-165">Resetting all migrations</span></span>

<span data-ttu-id="f1ee5-166">In einigen Extremfällen ist es möglicherweise erforderlich, alle Migrationen zu entfernen und zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-166">In some extreme cases, it may be necessary to remove all migrations and start over.</span></span> <span data-ttu-id="f1ee5-167">Dies kann problemlos durch Löschen Ihres **Migrations** Ordners und Löschen der Datenbank erfolgen. an diesem Punkt können Sie eine neue anfängliche Migration erstellen, die das gesamte aktuelle Schema enthält.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-167">This can be easily done by deleting your **Migrations** folder and dropping your database; at that point you can create a new initial migration, which will contain you entire current schema.</span></span>

<span data-ttu-id="f1ee5-168">Es ist auch möglich, alle Migrationen zurückzusetzen und eine einzelne zu erstellen, ohne die Daten zu verlieren.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-168">It's also possible to reset all migrations and create a single one without losing your data.</span></span> <span data-ttu-id="f1ee5-169">Dies wird mitunter als "Squashing" bezeichnet und umfasst einige manuelle Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="f1ee5-169">This is sometimes called "squashing", and involves some manual work:</span></span>

* <span data-ttu-id="f1ee5-170">**Migrations** Ordner löschen</span><span class="sxs-lookup"><span data-stu-id="f1ee5-170">Delete your **Migrations** folder</span></span>
* <span data-ttu-id="f1ee5-171">Erstellen einer neuen Migration und Generieren eines SQL-Skripts für dieses</span><span class="sxs-lookup"><span data-stu-id="f1ee5-171">Create a new migration and generate a SQL script for it</span></span>
* <span data-ttu-id="f1ee5-172">Löschen Sie in der Datenbank alle Zeilen aus der Migrations Verlaufs Tabelle.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-172">In your database, delete all rows from the migrations history table</span></span>
* <span data-ttu-id="f1ee5-173">Fügen Sie eine einzelne Zeile in den Migrations Verlauf ein, um aufzuzeichnen, dass die erste Migration bereits angewendet wurde, da Ihre Tabellen bereits vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-173">Insert a single row into the migrations history, to record that the first migration has already been applied, since your tables are already there.</span></span> <span data-ttu-id="f1ee5-174">Die INSERT-Abfrage ist der letzte Vorgang im oben generierten SQL-Skript.</span><span class="sxs-lookup"><span data-stu-id="f1ee5-174">The insert SEQL is the last operation in the SQL script generated above.</span></span>
