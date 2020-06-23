---
title: Neuerungen in EF Core 5.0
description: Übersicht über neue Features in EF Core 5.0
author: ajcvickers
ms.date: 06/02/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: 45d851a4b08a26dda0c24e20c79f42964fa4fae4
ms.sourcegitcommit: 1f0f93c66b2b50e03fcbed90260e94faa0279c46
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418939"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="32535-103">Neuerungen in EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="32535-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="32535-104">EF Core 5.0 befindet sich derzeit in der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="32535-104">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="32535-105">Diese Seite bietet eine Übersicht über interessante Änderungen in den einzelnen Vorschauversionen.</span><span class="sxs-lookup"><span data-stu-id="32535-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="32535-106">Auf dieser Seite wird der [Plan für EF Core 5.0](plan.md) nicht erneut aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="32535-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="32535-107">Der Plan beschreibt die allgemeinen Themen für EF Core 5.0 einschließlich sämtlicher Features, die wir vor Auslieferung des finalen Releases integrieren möchten.</span><span class="sxs-lookup"><span data-stu-id="32535-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="32535-108">Wir werden an dieser Stelle Links zur offiziellen Dokumentation einfügen, sobald diese veröffentlicht ist.</span><span class="sxs-lookup"><span data-stu-id="32535-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-5"></a><span data-ttu-id="32535-109">Preview 5</span><span class="sxs-lookup"><span data-stu-id="32535-109">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="32535-110">Datenbanksortierungen</span><span class="sxs-lookup"><span data-stu-id="32535-110">Database collations</span></span>

<span data-ttu-id="32535-111">Die Standardsortierung einer Datenbank kann nun im EF-Modell angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="32535-111">The default collation for a database can now be specified in the EF model.</span></span>
<span data-ttu-id="32535-112">Diese durchläuft generierte Migrationen, um die Sortierung beim Erstellen der Datenbank festzulegen.</span><span class="sxs-lookup"><span data-stu-id="32535-112">This will flow through to generated migrations to set the collation when the database is created.</span></span>
<span data-ttu-id="32535-113">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-113">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="32535-114">Die Migrationen generieren dann Folgendes, um die Datenbank in SQL Server zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="32535-114">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="32535-115">Die Sortierung für spezifische Datenbankspalten kann ebenfalls angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="32535-115">The collation to use for specific database columns can also be specified.</span></span>
<span data-ttu-id="32535-116">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-116">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="32535-117">Für diejenigen, die keine Migrationen verwenden, wird für Sortierungen nun ein Reverse Engineering von der Datenbank aus durchgeführt, wenn der Gerüstbau für eine DbContext-Instanz durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="32535-117">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="32535-118">Schließlich lässt `EF.Functions.Collate()` Ad-hoc-Abfragen mit verschiedenen Sortierungen zu.</span><span class="sxs-lookup"><span data-stu-id="32535-118">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span>
<span data-ttu-id="32535-119">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-119">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="32535-120">Dadurch wird die folgende Abfrage für SQL Server generiert:</span><span class="sxs-lookup"><span data-stu-id="32535-120">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="32535-121">Beachten Sie, dass Ad-Hoc-Sortierungen mit Bedacht verwendet werden sollten, da sie sich negativ auf die Datenbankleistung auswirken können.</span><span class="sxs-lookup"><span data-stu-id="32535-121">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="32535-122">Die Dokumentation finden Sie im [Issue 2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span><span class="sxs-lookup"><span data-stu-id="32535-122">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="32535-123">Übergeben von Argumenten in IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="32535-123">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="32535-124">Argumente können nun über die Befehlszeile in die `CreateDbContext`-Methode von [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1) übertragen werden.</span><span class="sxs-lookup"><span data-stu-id="32535-124">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="32535-125">Sie können beispielsweise angeben, dass es sich um ein Entwicklungsbuild handelt, indem Sie ein benutzerdefiniertes Argument (z. B. `dev`) über die Befehlszeile übergeben:</span><span class="sxs-lookup"><span data-stu-id="32535-125">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="32535-126">Dieses Argument wird dann an die Factory weitergeleitet, wo es zum Steuern der Erstellung und Initialisierung des Kontexts verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="32535-126">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span>
<span data-ttu-id="32535-127">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-127">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="32535-128">Die Dokumentation finden Sie im [Issue 2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span><span class="sxs-lookup"><span data-stu-id="32535-128">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="32535-129">Abfragen ohne Nachverfolgung mit Identitätsauflösung</span><span class="sxs-lookup"><span data-stu-id="32535-129">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="32535-130">Abfragen ohne Nachverfolgung können nun zum Durchführen von Identitätsauflösungen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="32535-130">No-tracking queries can now be configured to perform identity resolution.</span></span>
<span data-ttu-id="32535-131">Die folgende Abfrage erstellt beispielsweise eine neue Blog-Instanz für alle Post-Entitäten, selbst wenn alle Blog-Instanzen über denselben Primärschlüssel verfügen.</span><span class="sxs-lookup"><span data-stu-id="32535-131">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="32535-132">Diese Abfrage kann angepasst werden, um sicherzustellen, dass nur eine einzige Blog-Instanz erstellt wird. Dies ist jedoch in der Regel ein wenig langsamer und nutzt mehr Arbeitsspeicher:</span><span class="sxs-lookup"><span data-stu-id="32535-132">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="32535-133">Beachten Sie, dass dies nur für Abfragen ohne Nachverfolgung nützlich ist, weil alle Abfragen mit Nachverfolgung dieses Verhalten bereits aufweisen.</span><span class="sxs-lookup"><span data-stu-id="32535-133">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="32535-134">Nach einer Überprüfung der API wird auch die `PerformIdentityResolution`-Syntax geändert.</span><span class="sxs-lookup"><span data-stu-id="32535-134">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span>
<span data-ttu-id="32535-135">Weitere Informationen dazu finden Sie im [Issue 19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="32535-135">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="32535-136">Die Dokumentation finden Sie im [Issue 1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span><span class="sxs-lookup"><span data-stu-id="32535-136">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="32535-137">Gespeicherte (persistente) berechnete Spalten</span><span class="sxs-lookup"><span data-stu-id="32535-137">Stored (persisted) computed columns</span></span>

<span data-ttu-id="32535-138">Die meisten Datenbanken lassen zu, dass berechnete Spaltenwerte nach der Berechnung gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="32535-138">Most databases allow computed column values to be stored after computation.</span></span>
<span data-ttu-id="32535-139">Zwar wird dadurch Speicherplatz belegt, jedoch wird die berechnete Spalte nur einmal pro Update berechnet, anstatt jedes Mal, wenn der Wert abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="32535-139">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span>
<span data-ttu-id="32535-140">Dies ermöglicht auch die Indizierung der Spalte für einige Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="32535-140">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="32535-141">EF Core 5.0 ermöglicht die Konfiguration von berechneten Spalten als gespeichert.</span><span class="sxs-lookup"><span data-stu-id="32535-141">EF Core 5.0 allows computed columns to be configured as stored.</span></span>
<span data-ttu-id="32535-142">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-142">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="32535-143">Berechnete Spalten in SQLite</span><span class="sxs-lookup"><span data-stu-id="32535-143">SQLite computed columns</span></span>

<span data-ttu-id="32535-144">EF Core unterstützt nun berechnete Spalten in SQLite-Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="32535-144">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="32535-145">Preview 4</span><span class="sxs-lookup"><span data-stu-id="32535-145">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="32535-146">Konfigurieren von Datenbankgenauigkeit/Skalierung im Modell</span><span class="sxs-lookup"><span data-stu-id="32535-146">Configure database precision/scale in model</span></span>

<span data-ttu-id="32535-147">Genauigkeit und Skalierung einer Eigenschaft können jetzt mit dem Modellgenerator angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="32535-147">Precision and scale for a property can now be specified using the model builder.</span></span>
<span data-ttu-id="32535-148">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-148">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="32535-149">Genauigkeit und Skalierung können weiterhin über den vollständigen Datenbanktyp festgelegt werden, z. B. „decimal(16,4)“.</span><span class="sxs-lookup"><span data-stu-id="32535-149">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="32535-150">Die Dokumentation finden Sie im Issue [527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span><span class="sxs-lookup"><span data-stu-id="32535-150">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="32535-151">Angeben des Standardfüllfaktors für SQL Server-Indizes</span><span class="sxs-lookup"><span data-stu-id="32535-151">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="32535-152">Der Füllfaktor kann nun beim Erstellen eines Indexes auf SQL Server angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="32535-152">The fill factor can now be specified when creating an index on SQL Server.</span></span>
<span data-ttu-id="32535-153">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-153">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="32535-154">Preview 3</span><span class="sxs-lookup"><span data-stu-id="32535-154">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="32535-155">Gefilterte Include-Funktion</span><span class="sxs-lookup"><span data-stu-id="32535-155">Filtered Include</span></span>

<span data-ttu-id="32535-156">Die Include-Methode unterstützt jetzt das Filtern der enthaltenen Entitäten.</span><span class="sxs-lookup"><span data-stu-id="32535-156">The Include method now supports filtering of the entities included.</span></span>
<span data-ttu-id="32535-157">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-157">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="32535-158">Mit dieser Abfrage werden Blogs zusammen mit den jeweiligen Beiträgen zurückgegeben, jedoch nur, wenn der Titel „Cheese“ enthält.</span><span class="sxs-lookup"><span data-stu-id="32535-158">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="32535-159">Skip und Take können auch verwendet werden, um die Anzahl der enthaltenen Entitäten zu verringern.</span><span class="sxs-lookup"><span data-stu-id="32535-159">Skip and Take can also be used to reduce the number of included entities.</span></span>
<span data-ttu-id="32535-160">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-160">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="32535-161">Mit dieser Abfrage werden Blogs mit höchstens fünf Beiträgen zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="32535-161">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="32535-162">Ausführliche Informationen finden Sie in der [Dokumentation zu Include](xref:core/querying/related-data#filtered-include).</span><span class="sxs-lookup"><span data-stu-id="32535-162">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="32535-163">Neue ModelBuilder-API für Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="32535-163">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="32535-164">Navigationseigenschaften werden in erster Linie beim [Definieren von Beziehungen](xref:core/modeling/relationships) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="32535-164">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span>
<span data-ttu-id="32535-165">Die neue `Navigation`-Methode kann jedoch in den Fällen verwendet werden, in denen Navigationseigenschaften eine zusätzliche Konfiguration erfordern.</span><span class="sxs-lookup"><span data-stu-id="32535-165">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span>
<span data-ttu-id="32535-166">So können Sie z. B. ein Unterstützungsfeld für die Navigation festlegen, wenn das Feld nach Konvention nicht gefunden wird:</span><span class="sxs-lookup"><span data-stu-id="32535-166">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="32535-167">Beachten Sie, dass die `Navigation`-API die Beziehungskonfiguration nicht ersetzt.</span><span class="sxs-lookup"><span data-stu-id="32535-167">Note that the `Navigation` API does not replace relationship configuration.</span></span>
<span data-ttu-id="32535-168">Stattdessen ermöglicht sie eine zusätzliche Konfiguration der Navigationseigenschaften in bereits ermittelten oder definierten Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="32535-168">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="32535-169">Weitere Informationen finden Sie unter [Konfigurieren von Navigationseigenschaften](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="32535-169">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="32535-170">Neue Befehlszeilenparameter für Namespaces und Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="32535-170">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="32535-171">Bei Migrationen und Gerüstbau können Namespaces jetzt in der Befehlszeile angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="32535-171">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span>
<span data-ttu-id="32535-172">So können Sie beispielsweise ein Reverse Engineering für eine Datenbank durchführen, um die Kontext- und Modellklassen in verschiedenen Namespaces zu platzieren:</span><span class="sxs-lookup"><span data-stu-id="32535-172">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="32535-173">Ausführliche Informationen finden Sie in den Dokumentationen zu [Migrationen](xref:core/managing-schemas/migrations/index#namespaces) und [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span><span class="sxs-lookup"><span data-stu-id="32535-173">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="32535-174">Eine Verbindungszeichenfolge kann nun auch an den `database-update`-Befehl übermittelt werden:</span><span class="sxs-lookup"><span data-stu-id="32535-174">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="32535-175">Ausführliche Informationen finden Sie in der [Entity Framework Core Tools-Referenz – .NET-CLI](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update).</span><span class="sxs-lookup"><span data-stu-id="32535-175">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="32535-176">Gleichwertige Parameter wurden den PowerShell-Befehlen hinzugefügt, die in der Paket-Manager-Konsole in Visual Studio verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="32535-176">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="32535-177">EnableDetailedErrors wieder verfügbar</span><span class="sxs-lookup"><span data-stu-id="32535-177">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="32535-178">Aus Leistungsgründen führt EF keine zusätzlichen NULL-Überprüfungen durch, wenn Werte aus der Datenbank gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="32535-178">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span>
<span data-ttu-id="32535-179">Dadurch können Ausnahmen ausgelöst werden, deren Ursache schwer zu ermitteln ist, wenn ein unerwarteter NULL-Wert auftritt.</span><span class="sxs-lookup"><span data-stu-id="32535-179">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="32535-180">Durch die Verwendung von `EnableDetailedErrors` wird eine zusätzliche NULL-Überprüfung auf Abfragen hinzugefügt, sodass diese Fehler mit geringem Leistungsaufwand einfacher auf ihre Ursache zurückzuführen sind.</span><span class="sxs-lookup"><span data-stu-id="32535-180">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="32535-181">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-181">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="32535-182">Die Dokumentation finden Sie im Issue [955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span><span class="sxs-lookup"><span data-stu-id="32535-182">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="32535-183">Cosmos-Partitionsschlüssel</span><span class="sxs-lookup"><span data-stu-id="32535-183">Cosmos partition keys</span></span>

<span data-ttu-id="32535-184">Der Partitionsschlüssel, der für eine bestimmte Abfrage verwendet werden soll, kann jetzt in der Abfrage angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="32535-184">The partition key to use for a given query can now be specified in the query.</span></span>
<span data-ttu-id="32535-185">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-185">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="32535-186">Die Dokumentation finden Sie im Issue [2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span><span class="sxs-lookup"><span data-stu-id="32535-186">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="32535-187">Unterstützung für die DATALENGTH-Funktion von SQL Server</span><span class="sxs-lookup"><span data-stu-id="32535-187">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="32535-188">Auf diese kann mit der neuen `EF.Functions.DataLength`-Methode zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="32535-188">This can be accessed using the new `EF.Functions.DataLength` method.</span></span>
<span data-ttu-id="32535-189">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-189">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="32535-190">Vorschau 2</span><span class="sxs-lookup"><span data-stu-id="32535-190">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="32535-191">Verwenden eines C#-Attributs zum Angeben eines Eigenschaftsunterstützungsfelds</span><span class="sxs-lookup"><span data-stu-id="32535-191">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="32535-192">Es kann jetzt ein C#-Attribut verwendet werden, um das Unterstützungsfeld für eine Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="32535-192">A C# attribute can now be used to specify the backing field for a property.</span></span>
<span data-ttu-id="32535-193">Dieses Attribut ermöglicht es EF Core, wie sonst auch in das Unterstützungsfeld zu schreiben und daraus zu lesen, auch wenn das Unterstützungsfeld nicht automatisch gefunden werden kann.</span><span class="sxs-lookup"><span data-stu-id="32535-193">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span>
<span data-ttu-id="32535-194">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-194">For example:</span></span>

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

<span data-ttu-id="32535-195">Die Dokumentation finden Sie im Issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span><span class="sxs-lookup"><span data-stu-id="32535-195">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="32535-196">Vollständige Diskriminatorzuordnung</span><span class="sxs-lookup"><span data-stu-id="32535-196">Complete discriminator mapping</span></span>

<span data-ttu-id="32535-197">EF Core verwendet eine Diskriminatorspalte für die [TPH-Zuordnung (Tabelle pro Hierarchie) von Vererbungshierarchien](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="32535-197">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span>
<span data-ttu-id="32535-198">Es sind einige Leistungsverbesserungen möglich, solange EF Core alle möglichen Werte für den Diskriminator kennt.</span><span class="sxs-lookup"><span data-stu-id="32535-198">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span>
<span data-ttu-id="32535-199">Diese Verbesserungen sind nun in EF Core 5.0 implementiert.</span><span class="sxs-lookup"><span data-stu-id="32535-199">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="32535-200">Frühere Versionen von EF Core generierten beispielsweise für Abfragen immer einen SQL-Code wie den folgenden, in dem alle Typen in einer Hierarchie zurückgegeben wurden:</span><span class="sxs-lookup"><span data-stu-id="32535-200">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="32535-201">EF Core 5.0 generiert nun Folgendes, wenn eine vollständige Diskriminatorzuordnung konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="32535-201">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="32535-202">Ab Vorschauversion 3 wird dies das Standardverhalten.</span><span class="sxs-lookup"><span data-stu-id="32535-202">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="32535-203">Leistungsverbesserungen in Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="32535-203">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="32535-204">Es wurden zwei Leistungsverbesserungen für SQLite vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="32535-204">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="32535-205">Das Abrufen von Binär- und Zeichenfolgendaten mit GetBytes, GetChars und GetTextReader ist nun effizienter durch die Verwendung von SqliteBlob und Datenströmen.</span><span class="sxs-lookup"><span data-stu-id="32535-205">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="32535-206">Die Initialisierung von SqliteConnection ist nun verzögert.</span><span class="sxs-lookup"><span data-stu-id="32535-206">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="32535-207">Diese Verbesserungen wurden am ADO.NET-Anbieter Microsoft.Data.Sqlite vorgenommen und führen daher auch zu Leistungsverbesserungen außerhalb von EF Core.</span><span class="sxs-lookup"><span data-stu-id="32535-207">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="32535-208">Vorschauversion 1</span><span class="sxs-lookup"><span data-stu-id="32535-208">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="32535-209">Einfache Protokollierung</span><span class="sxs-lookup"><span data-stu-id="32535-209">Simple logging</span></span>

<span data-ttu-id="32535-210">Dieses Feature fügt eine Funktion hinzu, die `Database.Log` in EF6 ähnelt.</span><span class="sxs-lookup"><span data-stu-id="32535-210">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="32535-211">Es bietet eine einfache Möglichkeit zum Abrufen von Protokollen aus EF Core, ohne ein externes Protokollierungsframework konfigurieren zu müssen.</span><span class="sxs-lookup"><span data-stu-id="32535-211">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="32535-212">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 5. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="32535-212">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="32535-213">Zusätzliche Dokumentation finden Sie im Issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span><span class="sxs-lookup"><span data-stu-id="32535-213">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="32535-214">Einfache Möglichkeit zum Abrufen von generiertem SQL-Code</span><span class="sxs-lookup"><span data-stu-id="32535-214">Simple way to get generated SQL</span></span>

<span data-ttu-id="32535-215">In EF Core 5.0 wird die Erweiterungsmethode `ToQueryString` eingeführt, die den von EF Core beim Ausführen einer LINQ-Abfrage generierten SQL-Code zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="32535-215">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="32535-216">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 9. Januar 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="32535-216">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="32535-217">Zusätzliche Dokumentation finden Sie im Issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span><span class="sxs-lookup"><span data-stu-id="32535-217">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="32535-218">Verwenden eines C#-Attributs zum Angeben des Fehlens eines Schlüssels einer Entität</span><span class="sxs-lookup"><span data-stu-id="32535-218">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="32535-219">Ein Entitätstyp kann nun mithilfe des neuen Attributs `KeylessAttribute` als schlüssellos konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="32535-219">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="32535-220">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-220">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="32535-221">Die Dokumentation finden Sie im Issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span><span class="sxs-lookup"><span data-stu-id="32535-221">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="32535-222">Ändern von Verbindung oder Verbindungszeichenfolge in initialisiertem DbContext möglich</span><span class="sxs-lookup"><span data-stu-id="32535-222">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="32535-223">Es ist jetzt einfacher, eine DbContext-Instanz ohne Verbindung oder Verbindungszeichenfolge zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="32535-223">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="32535-224">Die Verbindung oder Verbindungszeichenfolge kann jetzt auch in der Kontextinstanz verändert werden.</span><span class="sxs-lookup"><span data-stu-id="32535-224">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="32535-225">Mithilfe dieses Features kann ein und dieselbe Kontextinstanz dynamisch eine Verbindung mit verschiedenen Datenbanken herstellen.</span><span class="sxs-lookup"><span data-stu-id="32535-225">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="32535-226">Die Dokumentation finden Sie im Issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span><span class="sxs-lookup"><span data-stu-id="32535-226">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="32535-227">Proxys zur Änderungsnachverfolgung</span><span class="sxs-lookup"><span data-stu-id="32535-227">Change-tracking proxies</span></span>

<span data-ttu-id="32535-228">EF Core kann jetzt Runtimeproxys generieren, die automatisch [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) und [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) implementieren.</span><span class="sxs-lookup"><span data-stu-id="32535-228">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="32535-229">Diese melden dann Wertänderungen bei Entitätseigenschaften direkt an EF Core, sodass die Notwendigkeit einer Überprüfung auf Änderungen entfällt.</span><span class="sxs-lookup"><span data-stu-id="32535-229">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="32535-230">Für Proxys gelten jedoch bestimmte Einschränkungen, daher eignen sie sich nicht in jedem Fall.</span><span class="sxs-lookup"><span data-stu-id="32535-230">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="32535-231">Die Dokumentation finden Sie im Issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span><span class="sxs-lookup"><span data-stu-id="32535-231">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="32535-232">Erweiterte Debugansichten</span><span class="sxs-lookup"><span data-stu-id="32535-232">Enhanced debug views</span></span>

<span data-ttu-id="32535-233">Debugansichten sind eine einfache Möglichkeit, beim Debuggen von Problemen interne Vorgänge in EF Core zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="32535-233">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="32535-234">Ein Debugansicht für das Modell wurde bereits vor einiger Zeit implementiert.</span><span class="sxs-lookup"><span data-stu-id="32535-234">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="32535-235">Für EF Core 5.0 haben wir die Modellansicht vereinfacht und im Zustands-Manager eine neue Debugansicht für nachverfolgte Entitäten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="32535-235">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="32535-236">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 12. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="32535-236">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="32535-237">Zusätzliche Dokumentation finden Sie im Issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span><span class="sxs-lookup"><span data-stu-id="32535-237">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="32535-238">Verbesserte Verarbeitung von NULL-Semantik in der Datenbank</span><span class="sxs-lookup"><span data-stu-id="32535-238">Improved handling of database null semantics</span></span>

<span data-ttu-id="32535-239">Relationale Datenbanken behandeln NULL in der Regel als unbekannten Wert und daher als ungleich jedem anderen NULL-Wert.</span><span class="sxs-lookup"><span data-stu-id="32535-239">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="32535-240">C# hingegen behandelt NULL als definierten Wert, der mit jedem anderen NULL-Wert übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="32535-240">While C# treats null as a defined value, which compares equal to any other null.</span></span>
<span data-ttu-id="32535-241">EF Core übersetzt Abfragen standardmäßig so, dass sie die NULL-Semantik von C# verwenden.</span><span class="sxs-lookup"><span data-stu-id="32535-241">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="32535-242">In EF Core 5.0 wurde die Effizienz dieser Übersetzungen erheblich verbessert.</span><span class="sxs-lookup"><span data-stu-id="32535-242">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="32535-243">Die Dokumentation finden Sie im Issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span><span class="sxs-lookup"><span data-stu-id="32535-243">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="32535-244">Indexereigenschaften</span><span class="sxs-lookup"><span data-stu-id="32535-244">Indexer properties</span></span>

<span data-ttu-id="32535-245">EF Core 5.0 unterstützt die Zuordnung von C#-Indexereigenschaften.</span><span class="sxs-lookup"><span data-stu-id="32535-245">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="32535-246">Durch diese Eigenschaften können Entitäten als Eigenschaftenbehälter fungieren, wobei Spalten benannten Eigenschaften im Behälter zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="32535-246">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="32535-247">Die Dokumentation finden Sie im Issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span><span class="sxs-lookup"><span data-stu-id="32535-247">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="32535-248">Generieren von CHECK-Einschränkungen für Enumerationszuordnungen</span><span class="sxs-lookup"><span data-stu-id="32535-248">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="32535-249">EF Core 5.0-Migrationen können jetzt CHECK-Einschränkungen aus Zuordnungen der enum-Eigenschaft generieren.</span><span class="sxs-lookup"><span data-stu-id="32535-249">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="32535-250">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-250">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="32535-251">Die Dokumentation finden Sie im Issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span><span class="sxs-lookup"><span data-stu-id="32535-251">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="32535-252">IsRelational</span><span class="sxs-lookup"><span data-stu-id="32535-252">IsRelational</span></span>

<span data-ttu-id="32535-253">Zusätzlich zu den existierenden `IsSqlServer`-, `IsSqlite`- und `IsInMemory`-Methoden wurde eine neue `IsRelational`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="32535-253">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="32535-254">Diese Methode kann verwendet werden, um zu testen, ob DbContext einen Anbieter einer relationalen Datenbank verwendet.</span><span class="sxs-lookup"><span data-stu-id="32535-254">This method can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="32535-255">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-255">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="32535-256">Die Dokumentation finden Sie im Issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span><span class="sxs-lookup"><span data-stu-id="32535-256">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="32535-257">Optimistische Nebenläufigkeit in Cosmos mit Entitätstags (ETags)</span><span class="sxs-lookup"><span data-stu-id="32535-257">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="32535-258">Der Azure Cosmos DB-Datenbankanbieter unterstützt nun optimistische Nebenläufigkeit mithilfe von ETags.</span><span class="sxs-lookup"><span data-stu-id="32535-258">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="32535-259">Verwenden Sie das Modellerstellungstool in OnModelCreating, um ein ETag zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="32535-259">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="32535-260">SaveChanges löst dann eine `DbUpdateConcurrencyException`-Ausnahme für einen Nebenläufigkeitskonflikt aus, die [verarbeitet werden kann](https://docs.microsoft.com/ef/core/saving/concurrency), z. B. um Wiederholungsversuche zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="32535-260">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="32535-261">Die Dokumentation finden Sie im Issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span><span class="sxs-lookup"><span data-stu-id="32535-261">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="32535-262">Abfrageübersetzungen für weitere DateTime-Konstrukte</span><span class="sxs-lookup"><span data-stu-id="32535-262">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="32535-263">Abfragen mit neuer DateTime-Konstruktion werden jetzt übersetzt.</span><span class="sxs-lookup"><span data-stu-id="32535-263">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="32535-264">Außerdem werden nun die folgenden SQL Server-Funktionen zugeordnet:</span><span class="sxs-lookup"><span data-stu-id="32535-264">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="32535-265">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="32535-265">DateDiffWeek</span></span>
* <span data-ttu-id="32535-266">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="32535-266">DateFromParts</span></span>

<span data-ttu-id="32535-267">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-267">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="32535-268">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="32535-268">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="32535-269">Abfrageübersetzungen für weitere Bytearraykonstrukte</span><span class="sxs-lookup"><span data-stu-id="32535-269">Query translations for more byte array constructs</span></span>

<span data-ttu-id="32535-270">Abfragen, die Contains, Length, SequenceEqual usw. in byte[]-Eigenschaften verwenden, werden jetzt in SQL übersetzt.</span><span class="sxs-lookup"><span data-stu-id="32535-270">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="32535-271">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 5. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="32535-271">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="32535-272">Zusätzliche Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="32535-272">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="32535-273">Abfrageübersetzung für Reverse</span><span class="sxs-lookup"><span data-stu-id="32535-273">Query translation for Reverse</span></span>

<span data-ttu-id="32535-274">Abfragen, die `Reverse` verwenden, werden jetzt übersetzt.</span><span class="sxs-lookup"><span data-stu-id="32535-274">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="32535-275">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-275">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="32535-276">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="32535-276">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="32535-277">Abfrageübersetzung für bitweise Operatoren</span><span class="sxs-lookup"><span data-stu-id="32535-277">Query translation for bitwise operators</span></span>

<span data-ttu-id="32535-278">Abfragen, die bitweise Operatoren verwenden, werden jetzt in mehr Fällen übersetzt. Beispiel:</span><span class="sxs-lookup"><span data-stu-id="32535-278">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="32535-279">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="32535-279">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="32535-280">Abfrageübersetzung für Zeichenfolgen in Cosmos</span><span class="sxs-lookup"><span data-stu-id="32535-280">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="32535-281">Abfragen, die die Zeichenfolgenmethoden Contains, StartsWith und EndsWith verwenden, werden jetzt übersetzt, wenn der Azure Cosmos DB-Anbieter verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="32535-281">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="32535-282">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="32535-282">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
