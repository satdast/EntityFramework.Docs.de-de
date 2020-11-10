---
title: 'Verwalten von Migrationen: EF Core'
description: Hinzufügen, entfernen und Verwalten von Datenbankschema Migrationen mit Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: 81f7cec54510d95b1e2432d56ff95110224fd9bf
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429850"
---
# <a name="managing-migrations"></a><span data-ttu-id="fcbd8-103">Verwalten von Migrationen</span><span class="sxs-lookup"><span data-stu-id="fcbd8-103">Managing Migrations</span></span>

<span data-ttu-id="fcbd8-104">Wenn das Modell geändert wird, werden Migrationen im Rahmen der normalen Entwicklung hinzugefügt und entfernt, und die Migrations Dateien werden in die Quell Code Verwaltung Ihres Projekts eingecheckt.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-104">As your model changes, migrations are added and removed as part of normal development, and the migration files are checked into your project's source control.</span></span> <span data-ttu-id="fcbd8-105">Zum Verwalten von Migrationen müssen Sie zunächst die [EF Core Befehlszeilen Tools](xref:core/cli/index)installieren.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-105">To manage migrations, you must first install the [EF Core command-line tools](xref:core/cli/index).</span></span>

> [!TIP]
> <span data-ttu-id="fcbd8-106">Wenn sich `DbContext` in einer anderen Assembly als das Startprojekt befindet, können Sie die Ziel- und Startprojekte explizit in den Tools für die [Paket-Manager-Konsole](xref:core/cli/powershell#target-and-startup-project) oder die [.NET Core-CLI](xref:core/cli/dotnet#target-project-and-startup-project) angeben.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-106">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="fcbd8-107">Hinzufügen einer Migration</span><span class="sxs-lookup"><span data-stu-id="fcbd8-107">Add a migration</span></span>

<span data-ttu-id="fcbd8-108">Nachdem das Modell geändert wurde, können Sie für diese Änderung eine Migration hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="fcbd8-108">After your model has been changed, you can add a migration for that change:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="fcbd8-109">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="fcbd8-109">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[<span data-ttu-id="fcbd8-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcbd8-110">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="fcbd8-111">Der Migrationsname kann wie eine Commitnachricht in einem Versionskontrollsystem verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-111">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="fcbd8-112">Sie können z. b. einen Namen wie *addblogkreatedtimestamp* auswählen, wenn die Änderung eine neue `CreatedTimestamp` Eigenschaft in der `Blog` Entität ist.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-112">For example, you might choose a name like *AddBlogCreatedTimestamp* if the change is a new `CreatedTimestamp` property on your `Blog` entity.</span></span>

<span data-ttu-id="fcbd8-113">Drei Dateien werden Ihrem Projekt im Verzeichnis **Migrationen** hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="fcbd8-113">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="fcbd8-114">**XXXXXXXXXXXXXX_AddCreatedTimestamp. cs** -die Haupt Migrations Datei.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-114">**XXXXXXXXXXXXXX_AddCreatedTimestamp.cs** --The main migrations file.</span></span> <span data-ttu-id="fcbd8-115">Enthält die Vorgänge, die zum Durchführen der Migration (in `Up`) und Rückgängigmachen (in `Down`) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-115">Contains the operations necessary to apply the migration (in `Up`) and to revert it (in `Down`).</span></span>
* <span data-ttu-id="fcbd8-116">**XXXXXXXXXXXXXX_AddCreatedTimestamp. Designer. cs** -die Migrations Metadatendatei.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-116">**XXXXXXXXXXXXXX_AddCreatedTimestamp.Designer.cs** --The migrations metadata file.</span></span> <span data-ttu-id="fcbd8-117">Enthält Informationen, die vom EF verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-117">Contains information used by EF.</span></span>
* <span data-ttu-id="fcbd8-118">**MyContextModelSnapshot.cs** – Eine Momentaufnahme des aktuellen Modells.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-118">**MyContextModelSnapshot.cs** --A snapshot of your current model.</span></span> <span data-ttu-id="fcbd8-119">Diese wird verwendet, um festzustellen, was sich beim Hinzufügen der nächsten Migration geändert hat.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-119">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="fcbd8-120">Mit dem Zeitstempel im Dateinamen können Migrationen chronologisch angeordnet werden, sodass Sie den Fortschritt der Änderungen mitverfolgen können.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-120">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

### <a name="namespaces"></a><span data-ttu-id="fcbd8-121">Namespaces</span><span class="sxs-lookup"><span data-stu-id="fcbd8-121">Namespaces</span></span>

<span data-ttu-id="fcbd8-122">Es steht Ihnen frei, Migrationsdateien zu verschieben und ihren Namespace manuell zu ändern.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-122">You are free to move Migrations files and change their namespace manually.</span></span> <span data-ttu-id="fcbd8-123">Neue Migrationen werden als gleichgeordnete Elemente der letzten Migration erstellt.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-123">New migrations are created as siblings of the last migration.</span></span> <span data-ttu-id="fcbd8-124">Alternativ können Sie das Verzeichnis zum Zeitpunkt der Generierung wie folgt angeben:</span><span class="sxs-lookup"><span data-stu-id="fcbd8-124">Alternatively, you can specify the directory at generation time as follows:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="fcbd8-125">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="fcbd8-125">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --output-dir Your/Directory
```

> [!NOTE]
> <span data-ttu-id="fcbd8-126">In EF Core 5,0 können Sie den Namespace auch unabhängig vom Verzeichnis mithilfe von ändern `--namespace` .</span><span class="sxs-lookup"><span data-stu-id="fcbd8-126">In EF Core 5.0, you can also change the namespace independently of the directory using `--namespace`.</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="fcbd8-127">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcbd8-127">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -OutputDir Your\Directory
```

> [!NOTE]
> <span data-ttu-id="fcbd8-128">In EF Core 5,0 können Sie den Namespace auch unabhängig vom Verzeichnis mithilfe von ändern `-Namespace` .</span><span class="sxs-lookup"><span data-stu-id="fcbd8-128">In EF Core 5.0, you can also change the namespace independently of the directory using `-Namespace`.</span></span>

<span data-ttu-id="fcbd8-129">\*\*_</span><span class="sxs-lookup"><span data-stu-id="fcbd8-129">\*\*_</span></span>

## <a name="customize-migration-code"></a><span data-ttu-id="fcbd8-130">Anpassen des Migrationscodes</span><span class="sxs-lookup"><span data-stu-id="fcbd8-130">Customize migration code</span></span>

<span data-ttu-id="fcbd8-131">Während EF Core in der Regel exakte Migrationen erstellt, sollten Sie den Code immer überprüfen und sicherstellen, dass er der gewünschten Änderung entspricht. in einigen Fällen ist dies sogar erforderlich.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-131">While EF Core generally creates accurate migrations, you should always review the code and make sure it corresponds to the desired change; in some cases, it is even necessary to do so.</span></span>

### <a name="column-renames"></a><span data-ttu-id="fcbd8-132">Spalten Umbenennungen</span><span class="sxs-lookup"><span data-stu-id="fcbd8-132">Column renames</span></span>

<span data-ttu-id="fcbd8-133">Ein wichtiges Beispiel, bei dem das Anpassen von Migrationen erforderlich ist, ist das Umbenennen einer Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-133">One notable example where customizing migrations is required is when renaming a property.</span></span> <span data-ttu-id="fcbd8-134">Wenn Sie z. b. eine Eigenschaft von `Name` in umbenennen `FullName` , generiert EF Core die folgende Migration:</span><span class="sxs-lookup"><span data-stu-id="fcbd8-134">For example, if you rename a property from `Name` to `FullName`, EF Core will generate the following migration:</span></span>

```csharp
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

<span data-ttu-id="fcbd8-135">EF Core kann im Allgemeinen nicht wissen, wann eine Spalte gelöscht und ein neues erstellt werden soll (zwei separate Änderungen) und wann eine Spalte umbenannt werden sollte.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-135">EF Core is generally unable to know when the intention is to drop a column and create a new one (two separate changes), and when a column should be renamed.</span></span> <span data-ttu-id="fcbd8-136">Wenn die oben genannte Migration unverändert angewendet wird, gehen alle Ihre Kundennamen verloren.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-136">If the above migration is applied as-is, all your customer names will be lost.</span></span> <span data-ttu-id="fcbd8-137">Zum Umbenennen einer Spalte ersetzen Sie die oben generierte Migration durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="fcbd8-137">To rename a column, replace the above generated migration with the following:</span></span>

```csharp
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> <span data-ttu-id="fcbd8-138">Der Einrüstvorgang der Migration warnt, wenn bei einem Vorgang Datenverlust eintreten kann (etwa beim Verwerfen einer Spalte).</span><span class="sxs-lookup"><span data-stu-id="fcbd8-138">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="fcbd8-139">Wenn diese Warnung angezeigt wird, überprüfen Sie den Migrationscode besonders gründlich auf Genauigkeit.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-139">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

### <a name="adding-raw-sql"></a><span data-ttu-id="fcbd8-140">Hinzufügen von RAW SQL</span><span class="sxs-lookup"><span data-stu-id="fcbd8-140">Adding raw SQL</span></span>

<span data-ttu-id="fcbd8-141">Das Umbenennen einer Spalte kann über eine integrierte API erreicht werden, in vielen Fällen ist dies jedoch nicht möglich.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-141">While renaming a column can be achieved via a built-in API, in many cases that is not possible.</span></span> <span data-ttu-id="fcbd8-142">Beispielsweise möchten wir eventuell vorhandene `FirstName` -und- `LastName` Eigenschaften durch eine einzelne neue Eigenschaft ersetzen `FullName` .</span><span class="sxs-lookup"><span data-stu-id="fcbd8-142">For example, we may want to replace existing `FirstName` and `LastName` properties with a single, new `FullName` property.</span></span> <span data-ttu-id="fcbd8-143">Die von EF Core generierte Migration sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="fcbd8-143">The migration generated by EF Core will be the following:</span></span>

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

<span data-ttu-id="fcbd8-144">Wie zuvor würde dies zu unerwünschten Datenverlusten führen.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-144">As before, this would cause unwanted data loss.</span></span> <span data-ttu-id="fcbd8-145">Um die Daten aus den alten Spalten zu übertragen, ordnen wir die Migrationen neu an und führen einen unformatierten SQL-Vorgang wie folgt ein:</span><span class="sxs-lookup"><span data-stu-id="fcbd8-145">To transfer the data from the old columns, we rearrange the migrations and introduce a raw SQL operation as follows:</span></span>

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

### <a name="arbitrary-changes-via-raw-sql"></a><span data-ttu-id="fcbd8-146">Beliebige Änderungen über RAW SQL</span><span class="sxs-lookup"><span data-stu-id="fcbd8-146">Arbitrary changes via raw SQL</span></span>

<span data-ttu-id="fcbd8-147">Unformatierte SQL-Daten können auch zum Verwalten von Datenbankobjekten verwendet werden, die EF Core nicht bekannt sind.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-147">Raw SQL can also be used to manage database objects that EF Core isn't aware of.</span></span> <span data-ttu-id="fcbd8-148">Fügen Sie zu diesem Zweck eine Migration hinzu, ohne eine Modell Änderung vorzunehmen. Es wird eine leere Migration generiert, die Sie dann mit unformatierten SQL-Vorgängen auffüllen können.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-148">To do this, add a migration without making any model change; an empty migration will be generated, which you can then populate with raw SQL operations.</span></span>

<span data-ttu-id="fcbd8-149">Beispielsweise wird mit der folgenden Migration eine gespeicherte Prozedur SQL Server erstellt:</span><span class="sxs-lookup"><span data-stu-id="fcbd8-149">For example, the following migration creates a SQL Server stored procedure:</span></span>

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
> <span data-ttu-id="fcbd8-150">`EXEC` wird verwendet, wenn eine Anweisung der erste oder einzige in einem SQL-Batch sein muss.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-150">`EXEC` is used when a statement must be the first or only one in a SQL batch.</span></span> <span data-ttu-id="fcbd8-151">Sie kann auch verwendet werden, um Parserfehler in idempotenten Migrations Skripts zu umgehen, die auftreten können, wenn Spalten, auf die verwiesen wird, in einer Tabelle zurzeit nicht</span><span class="sxs-lookup"><span data-stu-id="fcbd8-151">It can also be used to work around parser errors in idempotent migration scripts that can occur when referenced columns don't currently exist on a table.</span></span>

<span data-ttu-id="fcbd8-152">Dies kann verwendet werden, um einen beliebigen Aspekt der Datenbank zu verwalten, einschließlich:</span><span class="sxs-lookup"><span data-stu-id="fcbd8-152">This can be used to manage any aspect of your database, including:</span></span>

<span data-ttu-id="fcbd8-153">_ Gespeicherte Prozeduren</span><span class="sxs-lookup"><span data-stu-id="fcbd8-153">_ Stored procedures</span></span>
* <span data-ttu-id="fcbd8-154">Volltextsuche</span><span class="sxs-lookup"><span data-stu-id="fcbd8-154">Full-Text Search</span></span>
* <span data-ttu-id="fcbd8-155">Funktionen</span><span class="sxs-lookup"><span data-stu-id="fcbd8-155">Functions</span></span>
* <span data-ttu-id="fcbd8-156">Trigger</span><span class="sxs-lookup"><span data-stu-id="fcbd8-156">Triggers</span></span>
* <span data-ttu-id="fcbd8-157">Ansichten</span><span class="sxs-lookup"><span data-stu-id="fcbd8-157">Views</span></span>

<span data-ttu-id="fcbd8-158">In den meisten Fällen wird jede Migration beim Anwenden von Migrationen von EF Core automatisch in der eigenen Transaktion umschlossen.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-158">In most cases, EF Core will automatically wrap each migration in its own transaction when applying migrations.</span></span> <span data-ttu-id="fcbd8-159">Leider können einige Migrations Vorgänge nicht innerhalb einer Transaktion in einigen Datenbanken ausgeführt werden. in diesen Fällen können Sie die Transaktion ablehnen, indem Sie an übergeben `suppressTransaction: true` `migrationBuilder.Sql` .</span><span class="sxs-lookup"><span data-stu-id="fcbd8-159">Unfortunately, some migrations operations cannot be performed within a transaction in some databases; for these cases, you may opt out of the transaction by passing `suppressTransaction: true` to `migrationBuilder.Sql`.</span></span>

<span data-ttu-id="fcbd8-160">Wenn sich `DbContext` in einer anderen Assembly als das Startprojekt befindet, können Sie die Ziel- und Startprojekte explizit in den Tools für die [Paket-Manager-Konsole](xref:core/cli/powershell#target-and-startup-project) oder die [.NET Core-CLI](xref:core/cli/dotnet#target-project-and-startup-project) angeben.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-160">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="remove-a-migration"></a><span data-ttu-id="fcbd8-161">Entfernen einer Migration</span><span class="sxs-lookup"><span data-stu-id="fcbd8-161">Remove a migration</span></span>

<span data-ttu-id="fcbd8-162">Es kann vorkommen, dass Sie eine Migration hinzufügen und feststellen, dass Sie zusätzliche Änderungen an Ihrem EF Core-Modell vornehmen müssen, bevor Sie diese durchführen.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-162">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="fcbd8-163">Um die letzte Migration zu entfernen, verwenden Sie diesen Befehl.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-163">To remove the last migration, use this command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="fcbd8-164">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="fcbd8-164">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[<span data-ttu-id="fcbd8-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcbd8-165">Visual Studio</span></span>](#tab/vs)

```powershell
Remove-Migration
```

***

<span data-ttu-id="fcbd8-166">Nachdem Sie die Migration entfernt haben, können Sie die zusätzlichen Modelländerungen vornehmen und sie erneut hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-166">After removing the migration, you can make the additional model changes and add it again.</span></span>

> [!WARNING]
> <span data-ttu-id="fcbd8-167">Achten Sie darauf, keine Migrationen zu entfernen, die bereits auf Produktionsdatenbanken angewendet wurden.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-167">Take care not to remove any migrations which are already applied to production databases.</span></span> <span data-ttu-id="fcbd8-168">Wenn Sie dies nicht tun, wird verhindert, dass Sie Sie wiederherstellen können, und die Annahmen, die durch nachfolgende Migrationen vorgenommen werden, können unterbrechen</span><span class="sxs-lookup"><span data-stu-id="fcbd8-168">Not doing so will prevent you from being able to revert it, and may break the assumptions made by subsequent migrations.</span></span>

## <a name="listing-migrations"></a><span data-ttu-id="fcbd8-169">Auflisten von Migrationen</span><span class="sxs-lookup"><span data-stu-id="fcbd8-169">Listing migrations</span></span>

<span data-ttu-id="fcbd8-170">Sie können alle vorhandenen Migrationen wie folgt auflisten:</span><span class="sxs-lookup"><span data-stu-id="fcbd8-170">You can list all existing migrations as follows:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="fcbd8-171">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="fcbd8-171">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations list
```

### <a name="visual-studio"></a>[<span data-ttu-id="fcbd8-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fcbd8-172">Visual Studio</span></span>](#tab/vs)

> [!NOTE]
> <span data-ttu-id="fcbd8-173">Dieser Befehl wurde in EF Core 5,0 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-173">This command was added in EF Core 5.0.</span></span>

```powershell
Get-Migration
```

<span data-ttu-id="fcbd8-174">\*\*_</span><span class="sxs-lookup"><span data-stu-id="fcbd8-174">\*\*_</span></span>

## <a name="resetting-all-migrations"></a><span data-ttu-id="fcbd8-175">Zurücksetzen aller Migrationen</span><span class="sxs-lookup"><span data-stu-id="fcbd8-175">Resetting all migrations</span></span>

<span data-ttu-id="fcbd8-176">In einigen Extremfällen ist es möglicherweise erforderlich, alle Migrationen zu entfernen und zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-176">In some extreme cases, it may be necessary to remove all migrations and start over.</span></span> <span data-ttu-id="fcbd8-177">Dies lässt sich problemlos erreichen, indem der Ordner _ *Migrationen* \* gelöscht und die Datenbank gelöscht wird. an diesem Punkt können Sie eine neue anfängliche Migration erstellen, die das gesamte aktuelle Schema enthält.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-177">This can be easily done by deleting your _ *Migrations* \* folder and dropping your database; at that point you can create a new initial migration, which will contain you entire current schema.</span></span>

<span data-ttu-id="fcbd8-178">Es ist auch möglich, alle Migrationen zurückzusetzen und eine einzelne zu erstellen, ohne die Daten zu verlieren.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-178">It's also possible to reset all migrations and create a single one without losing your data.</span></span> <span data-ttu-id="fcbd8-179">Dies wird mitunter als "Squashing" bezeichnet und umfasst einige manuelle Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="fcbd8-179">This is sometimes called "squashing", and involves some manual work:</span></span>

* <span data-ttu-id="fcbd8-180">**Migrations** Ordner löschen</span><span class="sxs-lookup"><span data-stu-id="fcbd8-180">Delete your **Migrations** folder</span></span>
* <span data-ttu-id="fcbd8-181">Erstellen einer neuen Migration und Generieren eines SQL-Skripts für dieses</span><span class="sxs-lookup"><span data-stu-id="fcbd8-181">Create a new migration and generate a SQL script for it</span></span>
* <span data-ttu-id="fcbd8-182">Löschen Sie in der Datenbank alle Zeilen aus der Migrations Verlaufs Tabelle.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-182">In your database, delete all rows from the migrations history table</span></span>
* <span data-ttu-id="fcbd8-183">Fügen Sie eine einzelne Zeile in den Migrations Verlauf ein, um aufzuzeichnen, dass die erste Migration bereits angewendet wurde, da Ihre Tabellen bereits vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-183">Insert a single row into the migrations history, to record that the first migration has already been applied, since your tables are already there.</span></span> <span data-ttu-id="fcbd8-184">Die SQL-Einfügung ist der letzte Vorgang im oben generierten SQL-Skript.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-184">The insert SQL is the last operation in the SQL script generated above.</span></span>

> [!WARNING]
> <span data-ttu-id="fcbd8-185">Alle [benutzerdefinierten Migrations Codes](#customize-migration-code) gehen verloren, wenn der **Migrations** Ordner gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-185">Any [custom migration code](#customize-migration-code) will be lost when the **Migrations** folder is deleted.</span></span>  <span data-ttu-id="fcbd8-186">Alle Anpassungen müssen manuell auf die neue anfängliche Migration angewendet werden, damit Sie beibehalten werden.</span><span class="sxs-lookup"><span data-stu-id="fcbd8-186">Any customizations must be applied to the new initial migration manually in order to be preserved.</span></span>
