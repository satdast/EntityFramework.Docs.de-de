---
title: Neuerungen in EF Core 5.0
description: Übersicht über neue Features in EF Core 5.0
author: ajcvickers
ms.date: 07/20/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 3a1f5c7d44ad0e4d648492c4edcf14678c73538e
ms.sourcegitcommit: 6f7af3f138bf7c724cbdda261f97e5cf7035e8d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88847592"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="9d0c4-103">Neuerungen in EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="9d0c4-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="9d0c4-104">EF Core 5.0 befindet sich derzeit in der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="9d0c4-105">Diese Seite bietet eine Übersicht über interessante Änderungen in den einzelnen Vorschauversionen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="9d0c4-106">Auf dieser Seite wird der [Plan für EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan) nicht erneut aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="9d0c4-107">Der Plan beschreibt die allgemeinen Themen für EF Core 5.0 einschließlich sämtlicher Features, die wir vor Auslieferung des finalen Releases integrieren möchten.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="9d0c4-108">Wir werden an dieser Stelle Links zur offiziellen Dokumentation einfügen, sobald diese veröffentlicht ist.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-8"></a><span data-ttu-id="9d0c4-109">Preview 8</span><span class="sxs-lookup"><span data-stu-id="9d0c4-109">Preview 8</span></span>

## <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="9d0c4-110">TPT-Zuordnung (Tabelle pro Typ)</span><span class="sxs-lookup"><span data-stu-id="9d0c4-110">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="9d0c4-111">EF Core ordnet standardmäßig eine Vererbungshierarchie von .NET-Typen einer einzelnen Datenbanktabelle zu.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-111">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="9d0c4-112">Dies wird als TPH-Zuordnung (Tabelle pro Hierarchie) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-112">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="9d0c4-113">EF Core 5.0 ermöglicht außerdem das Zuordnen der einzelnen .NET-Typen in einer Vererbungshierarchie zu einer anderen Datenbanktabelle. Dies wird als TPT-Zuordnung (Tabelle pro Typ) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-113">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="9d0c4-114">Stellen Sie sich beispielsweise das folgende Modell mit einer zugeordneten Hierarchie vor:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-114">For example, consider this model with a mapped hierarchy:</span></span>

```c#
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

public class Pet : Animal
{
    public string Name { get; set; }
}

public class Cat : Pet
{
    public string EdcuationLevel { get; set; }
}

public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="9d0c4-115">EF Core ordnet dieses standardmäßig einer einzelnen Tabelle zu:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-115">By default, EF Core will map this to a single table:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [Name] nvarchar(max) NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);
```

<span data-ttu-id="9d0c4-116">Allerdings resultiert die Zuordnung aller Entitätstypen zu anderen Tabellen zu einer Tabelle pro Typ:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-116">However, mapping each entity type to a different table will instead result in one table per type:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Pets] (
    [Id] int NOT NULL,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Pets] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Pets_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Cats_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Dogs_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);
```

<span data-ttu-id="9d0c4-117">Beachten Sie, dass die Erstellung der oben gezeigten Fremdschlüsseleinschränkungen nach der Verzweigung des Codes für Preview 8 hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-117">Note that creation of the foreign key constraints shown above were added after branching the code for preview 8.</span></span>

<span data-ttu-id="9d0c4-118">Entitätstypen können mithilfe von Zuordnungsattributen verschiedenen Tabellen zugeordnet werden:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-118">Entity types can be mapped to different tables using mapping attributes:</span></span>

```c#
[Table("Animals")]
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

[Table("Pets")]
public class Pet : Animal
{
    public string Name { get; set; }
}

[Table("Cats")]
public class Cat : Pet
{
    public string EdcuationLevel { get; set; }
}

[Table("Dogs")]
public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="9d0c4-119">Alternativ kann die `ModelBuilder`-Konfiguration verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-119">Or using `ModelBuilder` configuration:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

<span data-ttu-id="9d0c4-120">Die Dokumentation finden Sie im [Issue 1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-120">Documentation is tracked by issue [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span></span>

### <a name="migrations-rebuild-sqlite-tables"></a><span data-ttu-id="9d0c4-121">Migrationen: Neuerstellen von SQLite-Tabellen</span><span class="sxs-lookup"><span data-stu-id="9d0c4-121">Migrations: Rebuild SQLite tables</span></span>

<span data-ttu-id="9d0c4-122">Im Vergleich zu anderen Datenbanken ist SQLite in seinen Schemabearbeitungsfunktionen relativ eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-122">Compared to other database, SQLite is relatively limited in its schema manipulation capabilities.</span></span> <span data-ttu-id="9d0c4-123">Wenn Sie beispielsweise eine Spalte aus einer vorhandenen Tabelle löschen möchten, muss die gesamte Tabelle gelöscht und neu erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-123">For example, dropping a column from an existing table requires that the entire table be dropped and re-created.</span></span> <span data-ttu-id="9d0c4-124">EF Core 5.0-Migrationen unterstützen nun die automatische Neuerstellung der Tabelle für Schemaänderungen, die diese erfordern.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-124">EF Core 5.0 Migrations now supports automatic rebuilding the table for schema changes that require it.</span></span>

<span data-ttu-id="9d0c4-125">Angenommen, Sie verfügen über eine `Unicorns`-Tabelle, die für den Entitätstyp `Unicorn` erstellt wurde:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-125">For example, imagine we have a `Unicorns` table created for a `Unicorn` entity type:</span></span>

```c#
public class Unicorn
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

```sql
CREATE TABLE "Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "Age" INTEGER NOT NULL
);
```

<span data-ttu-id="9d0c4-126">Dann erfahren Sie jedoch, dass das Speichern des Alters eines Einhorns sehr unhöflich ist. Daher entfernen Sie diese Eigenschaft, fügen eine neue Migration hinzu und aktualisieren die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-126">We then learn that storing the age of a unicorn is considered very rude, so let's remove that property, add a new migration, and update the database.</span></span> <span data-ttu-id="9d0c4-127">Dieses Update schlägt bei Verwendung von EF Core 3.1 fehl, da die Spalte nicht gelöscht werden kann.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-127">This update will fail when using EF Core 3.1 because the column cannot be dropped.</span></span> <span data-ttu-id="9d0c4-128">In EF Core 5.0 wird die Tabelle durch Migrationen stattdessen neu erstellt:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-128">In EF Core 5.0, Migrations will instead rebuild the table:</span></span>

```sql
CREATE TABLE "ef_temp_Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL
);

INSERT INTO "ef_temp_Unicorns" ("Id", "Name")
SELECT "Id", "Name"
FROM Unicorns;

PRAGMA foreign_keys = 0;

DROP TABLE "Unicorns";

ALTER TABLE "ef_temp_Unicorns" RENAME TO "Unicorns";

PRAGMA foreign_keys = 1;
```

<span data-ttu-id="9d0c4-129">Beachten Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-129">Notice that:</span></span>
* <span data-ttu-id="9d0c4-130">Eine temporäre Tabelle wird mit dem gewünschten Schema für die neue Tabelle erstellt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-130">A temporary table is created with the desired schema for the new table</span></span>
* <span data-ttu-id="9d0c4-131">Daten werden aus der aktuellen Tabelle in die temporäre Tabelle kopiert.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-131">Data is copied from the current table into the temporary table</span></span>
* <span data-ttu-id="9d0c4-132">Die Fremdschlüsselerzwingung wird deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-132">Foreign key enforcement is switched off</span></span>
* <span data-ttu-id="9d0c4-133">Die aktuelle Tabelle wird gelöscht.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-133">The current table is dropped</span></span>
* <span data-ttu-id="9d0c4-134">Die temporäre Tabelle wird umbenannt, sodass sie als neue Tabelle fungiert.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-134">The temporary table is renamed to be the new table</span></span>

<span data-ttu-id="9d0c4-135">Die Dokumentation finden Sie im [Issue 2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-135">Documentation is tracked by issue [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span></span>

### <a name="table-valued-functions"></a><span data-ttu-id="9d0c4-136">Tabellenwertfunktionen</span><span class="sxs-lookup"><span data-stu-id="9d0c4-136">Table-valued functions</span></span>

<span data-ttu-id="9d0c4-137">Dieses Feature stammt vom Communitymitglied [@pmiddleton](https://github.com/pmiddleton).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-137">This feature was contributed from the community by [@pmiddleton](https://github.com/pmiddleton).</span></span> <span data-ttu-id="9d0c4-138">Danke für diesen Beitrag!</span><span class="sxs-lookup"><span data-stu-id="9d0c4-138">Many thanks for the contribution!</span></span>

<span data-ttu-id="9d0c4-139">EF Core 5.0 umfasst erstklassige Unterstützung für die Zuordnung von .NET-Methoden zu Tabellenwertfunktionen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-139">EF Core 5.0 includes first-class support for mapping .NET methods to table-valued functions (TVFs).</span></span> <span data-ttu-id="9d0c4-140">Diese Funktionen können dann in LINQ-Abfragen verwendet werden, wobei eine zusätzliche Komposition der Ergebnisse der Funktion ebenfalls in SQL übersetzt wird.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-140">These functions can then be used in LINQ queries where additional composition on the results of the function will also be translated to SQL.</span></span>

<span data-ttu-id="9d0c4-141">Sehen Sie sich das folgende Beispiel an, bei dem die Tabellenwertfunktionen in einer SQL Server-Datenbank definiert wurden:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-141">For example, consider this TVF defined in a SQL Server database:</span></span>

```sql
CREATE FUNCTION GetReports(@employeeId int)
RETURNS @reports TABLE
(
    Name nvarchar(50) NOT NULL,
    IsDeveloper bit NOT NULL
)
AS
BEGIN
    WITH cteEmployees AS
    (
        SELECT Id, Name, ManagerId, IsDeveloper
        FROM Employees
        WHERE Id = @employeeId
        UNION ALL
        SELECT e.Id, e.Name, e.ManagerId, e.IsDeveloper
        FROM Employees e
        INNER JOIN cteEmployees cteEmp ON cteEmp.Id = e.ManagerId
    )
    INSERT INTO @reports
    SELECT Name, IsDeveloper
    FROM cteEmployees
    WHERE Id != @employeeId

    RETURN
END
```

<span data-ttu-id="9d0c4-142">Das EF Core-Modell erfordert zwei Entitätstypen, um diese Tabellenwertfunktionen zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-142">The EF Core model requires two entity types to use this TVF:</span></span>
* <span data-ttu-id="9d0c4-143">Ein `Employee`-Typ, der auf normale Weise der Tabelle „Employees“ zugeordnet wird</span><span class="sxs-lookup"><span data-stu-id="9d0c4-143">An `Employee` type that maps to the Employees table in the normal way</span></span>
* <span data-ttu-id="9d0c4-144">Ein `Report`-Typ, der dem von der Tabellenwertfunktion zurückgegebenen Format entspricht</span><span class="sxs-lookup"><span data-stu-id="9d0c4-144">A `Report` type that matches the shape returned by the TVF</span></span>

```c#
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }

    public int? ManagerId { get; set; }
    public virtual Employee Manager { get; set; }
}
```

```c#
public class Report
{
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }
}
```

<span data-ttu-id="9d0c4-145">Diese Typen müssen im EF Core-Modell enthalten sein:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-145">These types must be included in the EF Core model:</span></span>

```c#
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

<span data-ttu-id="9d0c4-146">Beachten Sie, dass `Report` über keinen Primärschlüssel verfügt und daher entsprechend konfiguriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-146">Notice that `Report` has no primary key and so must be configured as such.</span></span>

<span data-ttu-id="9d0c4-147">Schließlich muss eine .NET-Methode der Tabellenwertfunktion in der Datenbank zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-147">Finally, a .NET method must be mapped to the TVF in the database.</span></span> <span data-ttu-id="9d0c4-148">Diese Methode kann mithilfe der neuen `FromExpression`-Methode in DbContext definiert werden:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-148">This method can be defined on the DbContext using the new `FromExpression` method:</span></span>

```c#
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

<span data-ttu-id="9d0c4-149">Diese Methode verwendet einen Parameter und Rückgabetyp, die mit der oben definierten Tabellenwertfunktion übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-149">This method uses a parameter and return type that match the TVF defined above.</span></span> <span data-ttu-id="9d0c4-150">Die Methode wird dann zum EF Core-Modell in OnModelCreating hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-150">The method is then added to the EF Core model in OnModelCreating:</span></span>

```c#
modelBuilder.HasDbFunction(() => GetReports(default));
```

<span data-ttu-id="9d0c4-151">(Die Verwendung eines Lambdaausdrucks ist hier eine einfache Methode, um `MethodInfo` an EF Core zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-151">(Using a lambda here is an easy way to pass the `MethodInfo` to EF Core.</span></span> <span data-ttu-id="9d0c4-152">Die an die Methode übergebenen Argumente werden ignoriert.)</span><span class="sxs-lookup"><span data-stu-id="9d0c4-152">The arguments passed to the method are ignored.)</span></span>

<span data-ttu-id="9d0c4-153">Sie können nun Abfragen schreiben, die `GetReports` aufrufen und die Ergebnisse überschreiben.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-153">We can now write queries that call `GetReports` and compose over the results.</span></span> <span data-ttu-id="9d0c4-154">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-154">For example:</span></span>

```c#
from e in context.Employees
from rc in context.GetReports(e.Id)
where rc.IsDeveloper == true
select new
{
  ManagerName = e.Name,
  EmployeeName = rc.Name,
})
```

<span data-ttu-id="9d0c4-155">In SQL Server funktioniert dies wie folgt:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-155">On SQL Server, this translates to:</span></span>

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

<span data-ttu-id="9d0c4-156">Beachten Sie, dass der in der `Employees`-Tabelle verankerte SQL-Code `GetReports` aufruft und dann eine zusätzliche WHERE-Klausel für die Ergebnisse der Funktion hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-156">Notice that the SQL is rooted in the `Employees` table, calls `GetReports`, and then adds an additional WHERE clause on the results of the function.</span></span>

### <a name="flexible-queryupdate-mapping"></a><span data-ttu-id="9d0c4-157">Flexible Zuordnung von Abfragen und Updates</span><span class="sxs-lookup"><span data-stu-id="9d0c4-157">Flexible query/update mapping</span></span>

<span data-ttu-id="9d0c4-158">EF Core 5.0 ermöglicht das Zuordnen desselben Entitätstyps zu verschiedenen Datenbankobjekten.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-158">EF Core 5.0 allows mapping the same entity type to different database objects.</span></span> <span data-ttu-id="9d0c4-159">Bei diesen Objekten kann es sich um Tabellen, Ansichten oder Funktionen handeln.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-159">These objects may be tables, views, or functions.</span></span>

<span data-ttu-id="9d0c4-160">Ein Entitätstyp kann beispielsweise sowohl einer Datenbankansicht als auch einer Datenbanktabelle zugeordnet werden:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-160">For example, an entity type can be mapped to both a database view and a database table:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

<span data-ttu-id="9d0c4-161">EF Core führt Abfragen dann standardmäßig über die Ansicht aus und sendet Updates an die Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-161">By default, EF Core will then query from the view and send updates to the table.</span></span> <span data-ttu-id="9d0c4-162">Angenommen, der folgende Code wird ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-162">For example, executing the following code:</span></span>

```c#
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

<span data-ttu-id="9d0c4-163">Dies resultiert in einer Abfrage der Ansicht und einem anschließenden Update der Tabelle:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-163">Results in a query against the view, and then an update to the table:</span></span>

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a><span data-ttu-id="9d0c4-164">Kontextbezogene Konfiguration von geteilten Abfragen</span><span class="sxs-lookup"><span data-stu-id="9d0c4-164">Context-wide split-query configuration</span></span>

<span data-ttu-id="9d0c4-165">Geteilte Abfragen (siehe unten) können nun als Standardabfragen konfiguriert werden, die von DbContext ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-165">Split queries (see below) can now be configured as the default for any query executed by the DbContext.</span></span> <span data-ttu-id="9d0c4-166">Diese Konfiguration ist nur für relationale Anbieter verfügbar und muss daher im Rahmen der `UseProvider`-Konfiguration festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-166">This configuration is only available for relational providers, and so must be specified as part of the `UseProvider` configuration.</span></span> <span data-ttu-id="9d0c4-167">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-167">For example:</span></span>

```c#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

<span data-ttu-id="9d0c4-168">Die Dokumentation finden Sie im [Issue 2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-168">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="physicaladdress-mapping"></a><span data-ttu-id="9d0c4-169">Zuordnung von PhysicalAddress</span><span class="sxs-lookup"><span data-stu-id="9d0c4-169">PhysicalAddress mapping</span></span>

<span data-ttu-id="9d0c4-170">Dieses Feature stammt vom Communitymitglied [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-170">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="9d0c4-171">Danke für diesen Beitrag!</span><span class="sxs-lookup"><span data-stu-id="9d0c4-171">Many thanks for the contribution!</span></span>

<span data-ttu-id="9d0c4-172">Die neue .NET Standard-Klasse [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) wird jetzt automatisch einer Zeichenfolgenspalte für Datenbanken zugeordnet, die noch nicht nativ unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-172">The standard .NET [PhysicalAddress class](/dotnet/api/system.net.networkinformation.physicaladdress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="9d0c4-173">Weitere Informationen finden Sie unten in den Beispielen zu `IPAddress`.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-173">For more information, see the examples for `IPAddress` below.</span></span>

## <a name="preview-7"></a><span data-ttu-id="9d0c4-174">Preview 7</span><span class="sxs-lookup"><span data-stu-id="9d0c4-174">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="9d0c4-175">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="9d0c4-175">DbContextFactory</span></span>

<span data-ttu-id="9d0c4-176">EF Core 5.0 führt `AddDbContextFactory` und `AddPooledDbContextFactory` ein, um eine Zuordnungsinstanz zum Erstellen von DbContext-Instanzen im Abhängigkeitsinjektionscontainer der Anwendung zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-176">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="9d0c4-177">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-177">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="9d0c4-178">Anwendungsdienste wie ASP.NET Core-Controller können dann von `IDbContextFactory<TContext>` im Dienstkonstruktor abhängen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-178">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="9d0c4-179">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-179">For example:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

<span data-ttu-id="9d0c4-180">DbContext-Instanzen können dann nach Bedarf erstellt und verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-180">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="9d0c4-181">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-181">For example:</span></span>

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

<span data-ttu-id="9d0c4-182">Beachten Sie, dass die DbContext-Instanzen, die auf diese Weise erstellt werden, _nicht_ vom Dienstanbieter der Anwendung verwaltet werden, weshalb diese von der Anwendung gelöscht werden müssen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-182">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="9d0c4-183">Diese Entkopplung ist für Blazor-Anwendungen sehr nützlich, bei denen die Verwendung von `IDbContextFactory` empfohlen wird. Sie kann sich aber auch in anderen Szenarios als nützlich erweisen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-183">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="9d0c4-184">DbContext-Instanzen können durch Aufrufe von `AddPooledDbContextFactory` gepoolt werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-184">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="9d0c4-185">Dieses Pooling funktioniert auf dieselbe Weise wie `AddDbContextPool` und weist dieselben Einschränkungen auf.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-185">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="9d0c4-186">Die Dokumentation finden Sie im [Issue 2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-186">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="9d0c4-187">Zurücksetzen des DbContext-Zustands</span><span class="sxs-lookup"><span data-stu-id="9d0c4-187">Reset DbContext state</span></span>

<span data-ttu-id="9d0c4-188">EF Core 5.0 führt `ChangeTracker.Clear()` ein, wodurch die DbContext-Instanzen aller überwachten Entitäten gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-188">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="9d0c4-189">Dies sollte normalerweise nicht erforderlich sein, wenn die bewährte Methode zum Erstellen einer neuen kurzlebigen Kontextinstanz für jede Arbeitseinheit angewandt wird.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-189">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="9d0c4-190">Allerdings ist die Verwendung der neuen `Clear()`-Methode leistungsfähiger und robuster als das Trennen aller Entitäten in Massen, wenn der Zustand einer DbContext-Instanz nicht zurückgesetzt werden muss.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-190">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>

<span data-ttu-id="9d0c4-191">Die Dokumentation finden Sie im [Issue 2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-191">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="9d0c4-192">Neues Muster für vom Speicher generierte Standardwerte</span><span class="sxs-lookup"><span data-stu-id="9d0c4-192">New pattern for store-generated defaults</span></span>

<span data-ttu-id="9d0c4-193">EF Core lässt das Festlegen eines expliziten Werts für eine Spalte zu, die auch eine Standardwerteinschränkung aufweisen kann.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-193">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="9d0c4-194">EF Core verwendet hierfür den CLR-Standardwert für den Eigenschaftentyp als Schutzmechanismus. Wenn es sich bei dem Wert nicht um den CLR-Standardwert handelt, wird dieser eingefügt, andernfalls wird der Standardwert der Datenbank verwendet.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-194">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="9d0c4-195">Dies führt zu Problemen bei Typen, bei denen der CLR-Standardwert kein guter Schutzmechanismus ist. Insbesondere `bool`-Eigenschaften sind dafür bekannt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-195">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="9d0c4-196">EF Core 5.0 lässt nun zu, dass das Unterstützungsfeld in solchen Fällen NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-196">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="9d0c4-197">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-197">For example:</span></span>

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

<span data-ttu-id="9d0c4-198">Beachten Sie, dass das Unterstützungsfeld NULL-Werte zulässt, die öffentlich zur Verfügung gestellte Eigenschaft jedoch nicht.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-198">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="9d0c4-199">Daher kann der Sentinelwert `null` sein, ohne dass sich dies auf die öffentliche Oberfläche des Entitätstyps auswirkt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-199">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="9d0c4-200">Wenn `IsValid` in diesem Fall nie festgelegt wird, wird der Standardwert der Datenbank verwendet, da das Unterstützungsfeld weiterhin NULL ist.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-200">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="9d0c4-201">Wenn weder `true` noch `false` festgelegt werden, wird dieser Wert explizit in der Datenbank gespeichert.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-201">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="9d0c4-202">Die Dokumentation finden Sie im [Issue 2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-202">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="9d0c4-203">Cosmos-Partitionsschlüssel</span><span class="sxs-lookup"><span data-stu-id="9d0c4-203">Cosmos partition keys</span></span>

<span data-ttu-id="9d0c4-204">EF Core lässt das Einschließen des Cosmos-Partitionsschlüssels im EF-Modell zu.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-204">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="9d0c4-205">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-205">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="9d0c4-206">Ab Preview 7 ist der Partitionsschlüssel im primären Schlüssel des Entitätstyps enthalten und wird zur Verbesserung der Leistung einiger Abfragen verwendet.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-206">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="9d0c4-207">Die Dokumentation finden Sie im [Issue 2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-207">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="9d0c4-208">Cosmos-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="9d0c4-208">Cosmos configuration</span></span>

<span data-ttu-id="9d0c4-209">EF Core 5.0 verbessert die Konfiguration von Cosmos und Cosmos-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-209">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="9d0c4-210">Zuvor erforderte EF Core, dass der Endpunkt und der Schlüssel explizit festgelegt werden, wenn eine Verbindung mit einer Cosmos-Datenbank hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-210">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="9d0c4-211">EF Core 5.0 erlaubt stattdessen die Verwendung einer Verbindungszeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-211">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="9d0c4-212">Darüber hinaus ermöglicht EF Core 5.0 das explizite Festlegen der WebProxy-Instanz.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-212">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="9d0c4-213">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-213">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="9d0c4-214">Viele andere Timeoutwerte, Grenzwerte und mehr können nun ebenfalls konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-214">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="9d0c4-215">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-215">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

<span data-ttu-id="9d0c4-216">Schließlich ist `ConnectionMode.Gateway` nun der Standardverbindungsmodus, der im Allgemeinen kompatibler ist.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-216">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="9d0c4-217">Die Dokumentation finden Sie im [Issue 2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-217">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="9d0c4-218">Scaffold-DbContext nutzt nun die Singularform</span><span class="sxs-lookup"><span data-stu-id="9d0c4-218">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="9d0c4-219">Beim Gerüstbau einer DbContext-Instanz anhand einer vorhandenen Datenbank hat EF Core zuvor Entitätstypnamen erstellt, die mit den Tabellennamen in der Datenbank übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-219">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="9d0c4-220">Beispielsweise führten die Tabellen `People` und `Addresses` zu Entitätstypen mit den Namen `People` und `Addresses`.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-220">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="9d0c4-221">In früheren Releases konnte dieses Verhalten über die Registrierung eines Pluralisierungsdiensts konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-221">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="9d0c4-222">In EF Core 5.0 wird nun das Paket [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) als Standardpluralisierungsdienst verwendet.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-222">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="9d0c4-223">Das bedeutet, die Tabellen `People` und `Addresses` werden nun so rekonstruiert, dass Entitätstypen mit den Namen `Person` und `Address` erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-223">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="9d0c4-224">Sicherungspunkte</span><span class="sxs-lookup"><span data-stu-id="9d0c4-224">Savepoints</span></span>

<span data-ttu-id="9d0c4-225">EF Core unterstützt nun [Sicherungspunkte](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks), um mehr Kontrolle über Transaktionen zu bieten, die mehrere Vorgänge ausführen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-225">EF Core now supports [savepoints](/SQL/t-sql/language-elements/save-transaction-transact-sql?view=sql-server-ver15#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="9d0c4-226">Sicherungspunkte können manuell erstellt, veröffentlicht und zurückgesetzt werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-226">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="9d0c4-227">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-227">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

<span data-ttu-id="9d0c4-228">Darüber hinaus führt EF Core nun ein Rollback auf den letzten Sicherungspunkt aus, wenn die Ausführung von `SaveChanges` fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-228">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="9d0c4-229">Dies ermöglicht das erneute Ausführen von SaveChanges, ohne dass die gesamte Transaktion wiederholt werden muss.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-229">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="9d0c4-230">Die Dokumentation finden Sie im [Issue 2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-230">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="9d0c4-231">Vorschau 6</span><span class="sxs-lookup"><span data-stu-id="9d0c4-231">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="9d0c4-232">Aufteilen von Abfragen für ähnliche Sammlungen</span><span class="sxs-lookup"><span data-stu-id="9d0c4-232">Split queries for related collections</span></span>

<span data-ttu-id="9d0c4-233">Ab EF Core 3.0 generiert EF Core für jede LINQ-Abfrage eine einzelne SQL-Abfrage.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-233">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="9d0c4-234">Dadurch wird sichergestellt, dass die zurückgegebenen Daten im Rahmen der Einschränkungen des verwendeten Transaktionsmodus konsistent sind.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-234">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="9d0c4-235">Wenn die Abfrage `Include` oder eine Projektion verwendet, um mehrere ähnliche Sammlungen zurückzugeben, kann die Ausführung jedoch sehr lange dauern.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-235">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="9d0c4-236">In EF Core 5.0 ist es jetzt möglich, eine einzelne LINQ-Abfrage und die zugehörigen Sammlungen auf mehrere SQL-Abfragen aufzuteilen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-236">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="9d0c4-237">Die Leistung kann dadurch zwar erheblich verbessert werden, doch bei den zurückgegebenen Ergebnissen kann es zu Inkonsistenzen kommen, wenn die Daten sich zwischen den beiden Abfragen ändern.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-237">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="9d0c4-238">Serialisierbare Transaktionen oder Momentaufnahmentransaktionen können verwendet werden, um das zu verhindern und Konsistenz bei aufgeteilten Abfragen zu erzielen. Dadurch kann jedoch die Leistung beeinträchtigt werden, und das Verhalten kann sich unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-238">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="9d0c4-239">Aufteilen von Abfragen mit Include</span><span class="sxs-lookup"><span data-stu-id="9d0c4-239">Split queries with Include</span></span>

<span data-ttu-id="9d0c4-240">Gehen Sie von einer Abfrage aus, die mithilfe von `Include` zwei Ebenen ähnlicher Sammlungen einschließt:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-240">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="9d0c4-241">Standardmäßig generiert EF Core den folgenden SQL-Wert, wenn der SQLite-Anbieter verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-241">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

<span data-ttu-id="9d0c4-242">Die neue `AsSplitQuery`-API kann verwendet werden, um dieses Verhalten zu ändern.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-242">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="9d0c4-243">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-243">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="9d0c4-244">AsSplitQuery ist für alle relationalen Datenbankanbieter verfügbar und kann genau wie AsNoTracking an beliebiger Stelle in der Abfrage verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-244">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="9d0c4-245">In EF Core werden nun die folgenden drei SQL-Abfragen generiert:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-245">EF Core will now generate the following three SQL queries:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

<span data-ttu-id="9d0c4-246">Alle Vorgänge für den Abfragestamm werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-246">All operations on the query root are supported.</span></span> <span data-ttu-id="9d0c4-247">Das schließt OrderBy-, Skip-, Take-, Join-, FirstOrDefault-Vorgänge und ähnliche Vorgänge ein, die ein einzelnes Ergebnis auswählen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-247">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="9d0c4-248">Beachten Sie, dass nach OrderBy, Skip und Take gefilterte Include-Abfragen in Preview 6 nicht unterstützt werden, aber in den täglichen Builds und zukünftig in Preview 7 enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-248">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="9d0c4-249">Aufteilen von Abfragen mit Sammlungsprojektionen</span><span class="sxs-lookup"><span data-stu-id="9d0c4-249">Split queries with collection projections</span></span>

<span data-ttu-id="9d0c4-250">`AsSplitQuery` kann auch verwendet werden, wenn Sammlungen in Projektionen geladen werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-250">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="9d0c4-251">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-251">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="9d0c4-252">Diese LINQ-Abfrage generiert die folgenden zwei SQL-Abfragen, wenn der SQLite-Anbieter verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-252">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="9d0c4-253">Beachten Sie, dass nur die Materialisierung der Sammlung unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-253">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="9d0c4-254">Jede Komposition nach `e.Albums` im obigen Fall führt nicht zu einer aufgeteilten Abfrage.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-254">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="9d0c4-255">Die Verbesserungen in diesem Bereich werden in [Issue 21234](https://github.com/dotnet/efcore/issues/21234) nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-255">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="9d0c4-256">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="9d0c4-256">IndexAttribute</span></span>

<span data-ttu-id="9d0c4-257">Die neue IndexAttribute-Klasse kann für einen Entitätstyp verwendet werden, um einen Index für eine einzelne Spalte anzugeben.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-257">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="9d0c4-258">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-258">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="9d0c4-259">Bei SQL Server generieren Migrationen dann folgenden SQL-Code:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-259">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="9d0c4-260">IndexAttribute kann auch verwendet werden, um einen Index anzugeben, der mehrere Spalten umfasst.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-260">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="9d0c4-261">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-261">For example:</span></span>

```CSharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

<span data-ttu-id="9d0c4-262">Für SQL Server ergibt sich Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-262">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="9d0c4-263">Die Dokumentation finden Sie im [Issue 2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-263">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="9d0c4-264">Verbesserte Ausnahmen bei der Abfrageübersetzung</span><span class="sxs-lookup"><span data-stu-id="9d0c4-264">Improved query translation exceptions</span></span>

<span data-ttu-id="9d0c4-265">Wir arbeiten weiter an der Verbesserung der Ausnahmemeldungen, die generiert werden, wenn bei der Abfrageübersetzung ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-265">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="9d0c4-266">Diese Abfrage verwendet z. B. die nicht zugeordnete Eigenschaft `IsSigned`:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-266">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="9d0c4-267">EF Core löst die folgende Ausnahme aus, die angibt, dass bei der Übersetzung ein Fehler aufgetreten ist, da `IsSigned` nicht zugeordnet ist:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-267">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="9d0c4-268">Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-268">Unhandled exception.</span></span> <span data-ttu-id="9d0c4-269">System.InvalidOperationException: Der LINQ-Ausdruck „DbSet<Artist>() .Where(a => a.IsSigned)“ kann nicht übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-269">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="9d0c4-270">Zusätzliche Informationen: Bei der Übersetzung des Members IsSigned für den Entitätstyp Artist ist ein Fehler aufgetreten.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-270">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="9d0c4-271">Möglicherweise ist der angegebene Member nicht zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-271">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="9d0c4-272">Schreiben Sie die Abfrage in einem übersetzbaren Format neu, oder wechseln Sie explizit zur Clientauswertung, indem Sie einen Aufruf von AsEnumerable(), AsAsyncEnumerable(), ToList() oder ToListAsync() einfügen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-272">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="9d0c4-273">Weitere Informationen finden Sie unter https://go.microsoft.com/fwlink/?linkid=2101038.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-273">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="9d0c4-274">Zudem werden jetzt bessere Ausnahmemeldungen generiert, wenn Sie versuchen, Zeichenfolgenvergleiche mit kulturabhängiger Semantik zu übersetzen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-274">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="9d0c4-275">Diese Abfrage versucht beispielsweise, `StringComparison.CurrentCulture` zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-275">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="9d0c4-276">In EF Core wird nun die folgende Ausnahme ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-276">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="9d0c4-277">Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-277">Unhandled exception.</span></span> <span data-ttu-id="9d0c4-278">System.InvalidOperationException: Der LINQ-Ausdruck „DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))“ kann nicht übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-278">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="9d0c4-279">Zusätzliche Informationen: Die Übersetzung der Methode string.Equals, die das Argument StringComparison verwendet, wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-279">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="9d0c4-280">Weitere Informationen finden Sie unter https://go.microsoft.com/fwlink/?linkid=2129535.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-280">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="9d0c4-281">Schreiben Sie die Abfrage in einem übersetzbaren Format neu, oder wechseln Sie explizit zur Clientauswertung, indem Sie einen Aufruf von AsEnumerable(), AsAsyncEnumerable(), ToList() oder ToListAsync() einfügen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-281">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="9d0c4-282">Weitere Informationen finden Sie unter https://go.microsoft.com/fwlink/?linkid=2101038.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-282">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="9d0c4-283">Angeben der Transaktions-ID</span><span class="sxs-lookup"><span data-stu-id="9d0c4-283">Specify transaction ID</span></span>

<span data-ttu-id="9d0c4-284">Dieses Feature stammt vom Communitymitglied [@Marusyk](https://github.com/Marusyk).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-284">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="9d0c4-285">Danke für diesen Beitrag!</span><span class="sxs-lookup"><span data-stu-id="9d0c4-285">Many thanks for the contribution!</span></span>

<span data-ttu-id="9d0c4-286">EF Core macht eine Transaktions-ID für die Korrelation von aufrufübergreifenden Transaktionen verfügbar.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-286">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="9d0c4-287">Diese ID wird in der Regel durch EF Core festgelegt, wenn eine Transaktion gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-287">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="9d0c4-288">Wenn die Anwendung stattdessen die Transaktion startet, ermöglicht dieses Feature der Anwendung, die Transaktions-ID explizit festzulegen, damit sie überall ordnungsgemäß korreliert, wo sie verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-288">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="9d0c4-289">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-289">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="9d0c4-290">IPAddress-Zuordnung</span><span class="sxs-lookup"><span data-stu-id="9d0c4-290">IPAddress mapping</span></span>

<span data-ttu-id="9d0c4-291">Dieses Feature stammt vom Communitymitglied [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-291">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="9d0c4-292">Danke für diesen Beitrag!</span><span class="sxs-lookup"><span data-stu-id="9d0c4-292">Many thanks for the contribution!</span></span>

<span data-ttu-id="9d0c4-293">Die neue .NET Standard-Klasse [IPAddress](/dotnet/api/system.net.ipaddress) wird jetzt automatisch einer Zeichenfolgenspalte für Datenbanken zugeordnet, die noch nicht nativ unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-293">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="9d0c4-294">Sehen Sie sich beispielsweise die Zuordnung dieses Entitätstyps an:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-294">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="9d0c4-295">In SQL Server führt dies zu Migrationen, die die folgende Tabelle erstellen:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-295">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

<span data-ttu-id="9d0c4-296">Entitäten können dann wie gewohnt hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-296">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

<span data-ttu-id="9d0c4-297">Die resultierende SQL-Abfrage fügt die normalisierte IPv4- oder IPv6-Adresse ein:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-297">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="9d0c4-298">Ausschließen von OnConfiguring beim Gerüstbau</span><span class="sxs-lookup"><span data-stu-id="9d0c4-298">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="9d0c4-299">Wenn das Gerüst für DbContext aus einer vorhandenen Datenbank erstellt wird, erstellt EF Core standardmäßig eine OnConfiguring-Überladung mit einer Verbindungszeichenfolge, damit der Kontext sofort einsatzbereit ist.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-299">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="9d0c4-300">Das ist jedoch nicht nützlich, wenn Sie bereits eine partielle Klasse mit OnConfiguring besitzen oder den Kontext auf andere Weise konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-300">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="9d0c4-301">Die Gerüstbaubefehle können jetzt so geändert werden, dass OnConfiguring nicht generiert wird, um das Problem zu beheben.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-301">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="9d0c4-302">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-302">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="9d0c4-303">In der Paket-Manager-Konsole:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-303">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

<span data-ttu-id="9d0c4-304">Die Verwendung einer [benannten Verbindungszeichenfolge und eines sicheren Speichers wie Benutzergeheimnisse](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets) wird empfohlen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-304">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="9d0c4-305">Übersetzungen für FirstOrDefault in Zeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="9d0c4-305">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="9d0c4-306">Dieses Feature stammt vom Communitymitglied [@dvoreckyaa](https://github.com/dvoreckyaa).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-306">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="9d0c4-307">Danke für diesen Beitrag!</span><span class="sxs-lookup"><span data-stu-id="9d0c4-307">Many thanks for the contribution!</span></span>

<span data-ttu-id="9d0c4-308">FirstOrDefault und ähnliche Operatoren für Verbindungszeichenfolgen werden jetzt übersetzt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-308">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="9d0c4-309">Nehmen Sie diese LINQ-Abfrage als Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-309">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="9d0c4-310">Sie wird jetzt in folgenden SQL-Code übersetzt, wenn Sie SQL Server verwenden:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-310">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="9d0c4-311">Vereinfachen von CASE-Blöcken</span><span class="sxs-lookup"><span data-stu-id="9d0c4-311">Simplify case blocks</span></span>

<span data-ttu-id="9d0c4-312">EF Core generiert nun bessere Abfragen, wenn CASE-Blöcke verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-312">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="9d0c4-313">Nehmen Sie diese LINQ-Abfrage als Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-313">For example, this LINQ query:</span></span>

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="9d0c4-314">In SQL Server wurde sie zuvor in Folgendes übersetzt:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-314">Was on SQL Server formally translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

<span data-ttu-id="9d0c4-315">Jetzt wird sie folgendermaßen übersetzt:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-315">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a><span data-ttu-id="9d0c4-316">Preview 5</span><span class="sxs-lookup"><span data-stu-id="9d0c4-316">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="9d0c4-317">Datenbanksortierungen</span><span class="sxs-lookup"><span data-stu-id="9d0c4-317">Database collations</span></span>

<span data-ttu-id="9d0c4-318">Die Standardsortierung einer Datenbank kann nun im EF-Modell angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-318">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="9d0c4-319">Diese durchläuft generierte Migrationen, um die Sortierung beim Erstellen der Datenbank festzulegen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-319">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="9d0c4-320">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-320">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="9d0c4-321">Die Migrationen generieren dann Folgendes, um die Datenbank in SQL Server zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-321">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="9d0c4-322">Die Sortierung für spezifische Datenbankspalten kann ebenfalls angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-322">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="9d0c4-323">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-323">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="9d0c4-324">Für diejenigen, die keine Migrationen verwenden, wird für Sortierungen nun ein Reverse Engineering von der Datenbank aus durchgeführt, wenn der Gerüstbau für eine DbContext-Instanz durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-324">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="9d0c4-325">Schließlich lässt `EF.Functions.Collate()` Ad-hoc-Abfragen mit verschiedenen Sortierungen zu.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-325">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="9d0c4-326">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-326">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="9d0c4-327">Dadurch wird die folgende Abfrage für SQL Server generiert:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-327">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="9d0c4-328">Beachten Sie, dass Ad-Hoc-Sortierungen mit Bedacht verwendet werden sollten, da sie sich negativ auf die Datenbankleistung auswirken können.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-328">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="9d0c4-329">Die Dokumentation finden Sie im [Issue 2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-329">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="9d0c4-330">Übergeben von Argumenten in IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="9d0c4-330">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="9d0c4-331">Argumente können nun über die Befehlszeile in die `CreateDbContext`-Methode von [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1) übertragen werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-331">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="9d0c4-332">Sie können beispielsweise angeben, dass es sich um einen Entwicklungsbuild handelt, indem Sie in der Befehlszeile ein benutzerdefiniertes Argument (z. B. `dev`) übergeben:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-332">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
```

<span data-ttu-id="9d0c4-333">Dieses Argument wird dann an die Factory weitergeleitet, wo es zum Steuern der Erstellung und Initialisierung des Kontexts verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-333">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="9d0c4-334">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-334">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="9d0c4-335">Die Dokumentation finden Sie im [Issue 2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-335">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="9d0c4-336">Abfragen ohne Nachverfolgung mit Identitätsauflösung</span><span class="sxs-lookup"><span data-stu-id="9d0c4-336">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="9d0c4-337">Abfragen ohne Nachverfolgung können nun zum Durchführen von Identitätsauflösungen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-337">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="9d0c4-338">Die folgende Abfrage erstellt beispielsweise eine neue Blog-Instanz für alle Post-Entitäten, selbst wenn alle Blog-Instanzen über denselben Primärschlüssel verfügen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-338">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span>

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="9d0c4-339">Diese Abfrage kann angepasst werden, um sicherzustellen, dass nur eine einzige Blog-Instanz erstellt wird. Dies ist jedoch in der Regel ein wenig langsamer und nutzt mehr Arbeitsspeicher:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-339">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="9d0c4-340">Beachten Sie, dass dies nur für Abfragen ohne Nachverfolgung nützlich ist, weil alle Abfragen mit Nachverfolgung dieses Verhalten bereits aufweisen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-340">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="9d0c4-341">Nach einer Überprüfung der API wird auch die `PerformIdentityResolution`-Syntax geändert.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-341">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="9d0c4-342">Weitere Informationen dazu finden Sie im [Issue 19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-342">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="9d0c4-343">Die Dokumentation finden Sie im [Issue 1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-343">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="9d0c4-344">Gespeicherte (persistente) berechnete Spalten</span><span class="sxs-lookup"><span data-stu-id="9d0c4-344">Stored (persisted) computed columns</span></span>

<span data-ttu-id="9d0c4-345">Die meisten Datenbanken lassen zu, dass berechnete Spaltenwerte nach der Berechnung gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-345">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="9d0c4-346">Zwar wird dadurch Speicherplatz belegt, jedoch wird die berechnete Spalte nur einmal pro Update berechnet, anstatt jedes Mal, wenn der Wert abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-346">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="9d0c4-347">Dies ermöglicht auch die Indizierung der Spalte für einige Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-347">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="9d0c4-348">EF Core 5.0 ermöglicht die Konfiguration von berechneten Spalten als gespeichert.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-348">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="9d0c4-349">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-349">For example:</span></span>

```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="9d0c4-350">Berechnete Spalten in SQLite</span><span class="sxs-lookup"><span data-stu-id="9d0c4-350">SQLite computed columns</span></span>

<span data-ttu-id="9d0c4-351">EF Core unterstützt nun berechnete Spalten in SQLite-Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-351">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="9d0c4-352">Preview 4</span><span class="sxs-lookup"><span data-stu-id="9d0c4-352">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="9d0c4-353">Konfigurieren von Datenbankgenauigkeit/Skalierung im Modell</span><span class="sxs-lookup"><span data-stu-id="9d0c4-353">Configure database precision/scale in model</span></span>

<span data-ttu-id="9d0c4-354">Genauigkeit und Skalierung einer Eigenschaft können jetzt mit dem Modellgenerator angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-354">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="9d0c4-355">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-355">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="9d0c4-356">Genauigkeit und Skalierung können weiterhin über den vollständigen Datenbanktyp festgelegt werden, z. B. „decimal(16,4)“.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-356">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span>

<span data-ttu-id="9d0c4-357">Die Dokumentation finden Sie im Issue [527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-357">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="9d0c4-358">Angeben des Standardfüllfaktors für SQL Server-Indizes</span><span class="sxs-lookup"><span data-stu-id="9d0c4-358">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="9d0c4-359">Der Füllfaktor kann nun beim Erstellen eines Indexes auf SQL Server angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-359">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="9d0c4-360">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-360">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="9d0c4-361">Preview 3</span><span class="sxs-lookup"><span data-stu-id="9d0c4-361">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="9d0c4-362">Gefilterte Include-Funktion</span><span class="sxs-lookup"><span data-stu-id="9d0c4-362">Filtered Include</span></span>

<span data-ttu-id="9d0c4-363">Die Include-Methode unterstützt jetzt das Filtern der enthaltenen Entitäten.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-363">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="9d0c4-364">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-364">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="9d0c4-365">Mit dieser Abfrage werden Blogs zusammen mit den jeweiligen Beiträgen zurückgegeben, jedoch nur, wenn der Titel „Cheese“ enthält.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-365">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="9d0c4-366">Skip und Take können auch verwendet werden, um die Anzahl der enthaltenen Entitäten zu verringern.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-366">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="9d0c4-367">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-367">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="9d0c4-368">Mit dieser Abfrage werden Blogs mit höchstens fünf Beiträgen zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-368">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="9d0c4-369">Ausführliche Informationen finden Sie in der [Dokumentation zu Include](xref:core/querying/related-data#filtered-include).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-369">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="9d0c4-370">Neue ModelBuilder-API für Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="9d0c4-370">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="9d0c4-371">Navigationseigenschaften werden in erster Linie beim [Definieren von Beziehungen](xref:core/modeling/relationships) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-371">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="9d0c4-372">Die neue `Navigation`-Methode kann jedoch in den Fällen verwendet werden, in denen Navigationseigenschaften eine zusätzliche Konfiguration erfordern.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-372">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="9d0c4-373">So können Sie z. B. ein Unterstützungsfeld für die Navigation festlegen, wenn das Feld nach Konvention nicht gefunden wird:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-373">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="9d0c4-374">Beachten Sie, dass die `Navigation`-API die Beziehungskonfiguration nicht ersetzt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-374">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="9d0c4-375">Stattdessen ermöglicht sie eine zusätzliche Konfiguration der Navigationseigenschaften in bereits ermittelten oder definierten Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-375">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="9d0c4-376">Weitere Informationen finden Sie unter [Konfigurieren von Navigationseigenschaften](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-376">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="9d0c4-377">Neue Befehlszeilenparameter für Namespaces und Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="9d0c4-377">New command-line parameters for namespaces and connection strings</span></span>

<span data-ttu-id="9d0c4-378">Bei Migrationen und Gerüstbau können Namespaces jetzt in der Befehlszeile angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-378">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="9d0c4-379">So können Sie beispielsweise ein Reverse Engineering für eine Datenbank durchführen, um die Kontext- und Modellklassen in verschiedenen Namespaces zu platzieren:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-379">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span>

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="9d0c4-380">Ausführliche Informationen finden Sie in den Dokumentationen zu [Migrationen](xref:core/managing-schemas/migrations/index#namespaces) und [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-380">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="9d0c4-381">Eine Verbindungszeichenfolge kann nun auch an den `database-update`-Befehl übermittelt werden:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-381">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="9d0c4-382">Ausführliche Informationen finden Sie in der [Entity Framework Core Tools-Referenz – .NET-CLI](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-382">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="9d0c4-383">Gleichwertige Parameter wurden den PowerShell-Befehlen hinzugefügt, die in der Paket-Manager-Konsole in Visual Studio verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-383">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="9d0c4-384">EnableDetailedErrors wieder verfügbar</span><span class="sxs-lookup"><span data-stu-id="9d0c4-384">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="9d0c4-385">Aus Leistungsgründen führt EF keine zusätzlichen NULL-Überprüfungen durch, wenn Werte aus der Datenbank gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-385">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="9d0c4-386">Dadurch können Ausnahmen ausgelöst werden, deren Ursache schwer zu ermitteln ist, wenn ein unerwarteter NULL-Wert auftritt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-386">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="9d0c4-387">Durch die Verwendung von `EnableDetailedErrors` wird eine zusätzliche NULL-Überprüfung auf Abfragen hinzugefügt, sodass diese Fehler mit geringem Leistungsaufwand einfacher auf ihre Ursache zurückzuführen sind.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-387">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>

<span data-ttu-id="9d0c4-388">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-388">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="9d0c4-389">Die Dokumentation finden Sie im Issue [955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-389">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="9d0c4-390">Cosmos-Partitionsschlüssel</span><span class="sxs-lookup"><span data-stu-id="9d0c4-390">Cosmos partition keys</span></span>

<span data-ttu-id="9d0c4-391">Der Partitionsschlüssel, der für eine bestimmte Abfrage verwendet werden soll, kann jetzt in der Abfrage angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-391">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="9d0c4-392">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-392">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="9d0c4-393">Die Dokumentation finden Sie im Issue [2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-393">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="9d0c4-394">Unterstützung für die DATALENGTH-Funktion von SQL Server</span><span class="sxs-lookup"><span data-stu-id="9d0c4-394">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="9d0c4-395">Auf diese kann mit der neuen `EF.Functions.DataLength`-Methode zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-395">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="9d0c4-396">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-396">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a><span data-ttu-id="9d0c4-397">Vorschau 2</span><span class="sxs-lookup"><span data-stu-id="9d0c4-397">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="9d0c4-398">Verwenden eines C#-Attributs zum Angeben eines Eigenschaftsunterstützungsfelds</span><span class="sxs-lookup"><span data-stu-id="9d0c4-398">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="9d0c4-399">Es kann jetzt ein C#-Attribut verwendet werden, um das Unterstützungsfeld für eine Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-399">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="9d0c4-400">Dieses Attribut ermöglicht es EF Core, wie sonst auch in das Unterstützungsfeld zu schreiben und daraus zu lesen, auch wenn das Unterstützungsfeld nicht automatisch gefunden werden kann.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-400">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="9d0c4-401">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-401">For example:</span></span>

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

<span data-ttu-id="9d0c4-402">Die Dokumentation finden Sie im Issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-402">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="9d0c4-403">Vollständige Diskriminatorzuordnung</span><span class="sxs-lookup"><span data-stu-id="9d0c4-403">Complete discriminator mapping</span></span>

<span data-ttu-id="9d0c4-404">EF Core verwendet eine Diskriminatorspalte für die [TPH-Zuordnung (Tabelle pro Hierarchie) von Vererbungshierarchien](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-404">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="9d0c4-405">Es sind einige Leistungsverbesserungen möglich, solange EF Core alle möglichen Werte für den Diskriminator kennt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-405">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="9d0c4-406">Diese Verbesserungen sind nun in EF Core 5.0 implementiert.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-406">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="9d0c4-407">Frühere Versionen von EF Core generierten beispielsweise für Abfragen immer einen SQL-Code wie den folgenden, in dem alle Typen in einer Hierarchie zurückgegeben wurden:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-407">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="9d0c4-408">EF Core 5.0 generiert nun Folgendes, wenn eine vollständige Diskriminatorzuordnung konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-408">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="9d0c4-409">Ab Vorschauversion 3 wird dies das Standardverhalten.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-409">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="9d0c4-410">Leistungsverbesserungen in Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="9d0c4-410">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="9d0c4-411">Es wurden zwei Leistungsverbesserungen für SQLite vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-411">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="9d0c4-412">Das Abrufen von Binär- und Zeichenfolgendaten mit GetBytes, GetChars und GetTextReader ist nun effizienter durch die Verwendung von SqliteBlob und Datenströmen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-412">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="9d0c4-413">Die Initialisierung von SqliteConnection ist nun verzögert.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-413">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="9d0c4-414">Diese Verbesserungen wurden am ADO.NET-Anbieter Microsoft.Data.Sqlite vorgenommen und führen daher auch zu Leistungsverbesserungen außerhalb von EF Core.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-414">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="9d0c4-415">Vorschauversion 1</span><span class="sxs-lookup"><span data-stu-id="9d0c4-415">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="9d0c4-416">Einfache Protokollierung</span><span class="sxs-lookup"><span data-stu-id="9d0c4-416">Simple logging</span></span>

<span data-ttu-id="9d0c4-417">Dieses Feature fügt eine Funktion hinzu, die `Database.Log` in EF6 ähnelt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-417">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="9d0c4-418">Es bietet eine einfache Möglichkeit zum Abrufen von Protokollen aus EF Core, ohne ein externes Protokollierungsframework konfigurieren zu müssen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-418">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="9d0c4-419">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 5. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-419">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="9d0c4-420">Zusätzliche Dokumentation finden Sie im Issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-420">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="9d0c4-421">Einfache Möglichkeit zum Abrufen von generiertem SQL-Code</span><span class="sxs-lookup"><span data-stu-id="9d0c4-421">Simple way to get generated SQL</span></span>

<span data-ttu-id="9d0c4-422">In EF Core 5.0 wird die Erweiterungsmethode `ToQueryString` eingeführt, die den von EF Core beim Ausführen einer LINQ-Abfrage generierten SQL-Code zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-422">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="9d0c4-423">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 9. Januar 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-423">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="9d0c4-424">Zusätzliche Dokumentation finden Sie im Issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-424">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="9d0c4-425">Verwenden eines C#-Attributs zum Angeben des Fehlens eines Schlüssels einer Entität</span><span class="sxs-lookup"><span data-stu-id="9d0c4-425">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="9d0c4-426">Ein Entitätstyp kann nun mithilfe des neuen Attributs `KeylessAttribute` als schlüssellos konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-426">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="9d0c4-427">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-427">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="9d0c4-428">Die Dokumentation finden Sie im Issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-428">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="9d0c4-429">Ändern von Verbindung oder Verbindungszeichenfolge in initialisiertem DbContext möglich</span><span class="sxs-lookup"><span data-stu-id="9d0c4-429">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="9d0c4-430">Es ist jetzt einfacher, eine DbContext-Instanz ohne Verbindung oder Verbindungszeichenfolge zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-430">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="9d0c4-431">Die Verbindung oder Verbindungszeichenfolge kann jetzt auch in der Kontextinstanz verändert werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-431">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="9d0c4-432">Mithilfe dieses Features kann ein und dieselbe Kontextinstanz dynamisch eine Verbindung mit verschiedenen Datenbanken herstellen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-432">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="9d0c4-433">Die Dokumentation finden Sie im Issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-433">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="9d0c4-434">Proxys zur Änderungsnachverfolgung</span><span class="sxs-lookup"><span data-stu-id="9d0c4-434">Change-tracking proxies</span></span>

<span data-ttu-id="9d0c4-435">EF Core kann jetzt Runtimeproxys generieren, die automatisch [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) und [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) implementieren.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-435">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="9d0c4-436">Diese melden dann Wertänderungen bei Entitätseigenschaften direkt an EF Core, sodass die Notwendigkeit einer Überprüfung auf Änderungen entfällt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-436">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="9d0c4-437">Für Proxys gelten jedoch bestimmte Einschränkungen, daher eignen sie sich nicht in jedem Fall.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-437">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="9d0c4-438">Die Dokumentation finden Sie im Issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-438">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="9d0c4-439">Erweiterte Debugansichten</span><span class="sxs-lookup"><span data-stu-id="9d0c4-439">Enhanced debug views</span></span>

<span data-ttu-id="9d0c4-440">Debugansichten sind eine einfache Möglichkeit, beim Debuggen von Problemen interne Vorgänge in EF Core zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-440">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="9d0c4-441">Ein Debugansicht für das Modell wurde bereits vor einiger Zeit implementiert.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-441">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="9d0c4-442">Für EF Core 5.0 haben wir die Modellansicht vereinfacht und im Zustands-Manager eine neue Debugansicht für nachverfolgte Entitäten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-442">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="9d0c4-443">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 12. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-443">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="9d0c4-444">Zusätzliche Dokumentation finden Sie im Issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-444">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="9d0c4-445">Verbesserte Verarbeitung von NULL-Semantik in der Datenbank</span><span class="sxs-lookup"><span data-stu-id="9d0c4-445">Improved handling of database null semantics</span></span>

<span data-ttu-id="9d0c4-446">Relationale Datenbanken behandeln NULL in der Regel als unbekannten Wert und daher als ungleich jedem anderen NULL-Wert.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-446">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="9d0c4-447">C# hingegen behandelt NULL als definierten Wert, der mit jedem anderen NULL-Wert übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-447">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="9d0c4-448">EF Core übersetzt Abfragen standardmäßig so, dass sie die NULL-Semantik von C# verwenden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-448">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="9d0c4-449">In EF Core 5.0 wurde die Effizienz dieser Übersetzungen erheblich verbessert.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-449">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="9d0c4-450">Die Dokumentation finden Sie im Issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-450">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="9d0c4-451">Indexereigenschaften</span><span class="sxs-lookup"><span data-stu-id="9d0c4-451">Indexer properties</span></span>

<span data-ttu-id="9d0c4-452">EF Core 5.0 unterstützt die Zuordnung von C#-Indexereigenschaften.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-452">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="9d0c4-453">Durch diese Eigenschaften können Entitäten als Eigenschaftenbehälter fungieren, wobei Spalten benannten Eigenschaften im Behälter zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-453">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="9d0c4-454">Die Dokumentation finden Sie im Issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-454">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="9d0c4-455">Generieren von CHECK-Einschränkungen für Enumerationszuordnungen</span><span class="sxs-lookup"><span data-stu-id="9d0c4-455">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="9d0c4-456">EF Core 5.0-Migrationen können jetzt CHECK-Einschränkungen aus Zuordnungen der enum-Eigenschaft generieren.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-456">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="9d0c4-457">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-457">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="9d0c4-458">Die Dokumentation finden Sie im Issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-458">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="9d0c4-459">IsRelational</span><span class="sxs-lookup"><span data-stu-id="9d0c4-459">IsRelational</span></span>

<span data-ttu-id="9d0c4-460">Zusätzlich zu den existierenden `IsSqlServer`-, `IsSqlite`- und `IsInMemory`-Methoden wurde eine neue `IsRelational`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-460">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="9d0c4-461">Diese Methode kann verwendet werden, um zu testen, ob DbContext einen Anbieter einer relationalen Datenbank verwendet.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-461">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="9d0c4-462">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-462">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="9d0c4-463">Die Dokumentation finden Sie im Issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-463">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="9d0c4-464">Optimistische Nebenläufigkeit in Cosmos mit Entitätstags (ETags)</span><span class="sxs-lookup"><span data-stu-id="9d0c4-464">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="9d0c4-465">Der Azure Cosmos DB-Datenbankanbieter unterstützt nun optimistische Nebenläufigkeit mithilfe von ETags.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-465">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="9d0c4-466">Verwenden Sie das Modellerstellungstool in OnModelCreating, um ein ETag zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-466">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="9d0c4-467">SaveChanges löst dann eine `DbUpdateConcurrencyException`-Ausnahme für einen Nebenläufigkeitskonflikt aus, die [verarbeitet werden kann](/ef/core/saving/concurrency), z. B. um Wiederholungsversuche zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-467">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="9d0c4-468">Die Dokumentation finden Sie im Issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-468">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="9d0c4-469">Abfrageübersetzungen für weitere DateTime-Konstrukte</span><span class="sxs-lookup"><span data-stu-id="9d0c4-469">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="9d0c4-470">Abfragen mit neuer DateTime-Konstruktion werden jetzt übersetzt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-470">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="9d0c4-471">Außerdem werden nun die folgenden SQL Server-Funktionen zugeordnet:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-471">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="9d0c4-472">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="9d0c4-472">DateDiffWeek</span></span>
* <span data-ttu-id="9d0c4-473">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="9d0c4-473">DateFromParts</span></span>

<span data-ttu-id="9d0c4-474">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-474">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="9d0c4-475">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-475">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="9d0c4-476">Abfrageübersetzungen für weitere Bytearraykonstrukte</span><span class="sxs-lookup"><span data-stu-id="9d0c4-476">Query translations for more byte array constructs</span></span>

<span data-ttu-id="9d0c4-477">Abfragen, die Contains, Length, SequenceEqual usw. in byte[]-Eigenschaften verwenden, werden jetzt in SQL übersetzt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-477">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="9d0c4-478">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 5. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-478">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="9d0c4-479">Zusätzliche Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-479">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="9d0c4-480">Abfrageübersetzung für Reverse</span><span class="sxs-lookup"><span data-stu-id="9d0c4-480">Query translation for Reverse</span></span>

<span data-ttu-id="9d0c4-481">Abfragen, die `Reverse` verwenden, werden jetzt übersetzt.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-481">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="9d0c4-482">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-482">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="9d0c4-483">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-483">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="9d0c4-484">Abfrageübersetzung für bitweise Operatoren</span><span class="sxs-lookup"><span data-stu-id="9d0c4-484">Query translation for bitwise operators</span></span>

<span data-ttu-id="9d0c4-485">Abfragen, die bitweise Operatoren verwenden, werden jetzt in mehr Fällen übersetzt. Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9d0c4-485">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="9d0c4-486">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-486">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="9d0c4-487">Abfrageübersetzung für Zeichenfolgen in Cosmos</span><span class="sxs-lookup"><span data-stu-id="9d0c4-487">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="9d0c4-488">Abfragen, die die Zeichenfolgenmethoden Contains, StartsWith und EndsWith verwenden, werden jetzt übersetzt, wenn der Azure Cosmos DB-Anbieter verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9d0c4-488">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="9d0c4-489">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="9d0c4-489">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
