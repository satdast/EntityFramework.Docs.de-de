---
title: Neuerungen in EF Core 5.0
description: Übersicht über neue Features in EF Core 5.0
author: ajcvickers
ms.date: 03/30/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: c902988920e3b1a6039808fe0658fc19dee2728a
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103073"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="8f911-103">Neuerungen in EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="8f911-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="8f911-104">EF Core 5.0 befindet sich derzeit in der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="8f911-104">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="8f911-105">Diese Seite bietet eine Übersicht über interessante Änderungen in den einzelnen Vorschauversionen.</span><span class="sxs-lookup"><span data-stu-id="8f911-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="8f911-106">Auf dieser Seite wird der [Plan für EF Core 5.0](plan.md) nicht erneut aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="8f911-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="8f911-107">Der Plan beschreibt die allgemeinen Themen für EF Core 5.0 einschließlich sämtlicher Features, die wir vor Auslieferung des finalen Releases integrieren möchten.</span><span class="sxs-lookup"><span data-stu-id="8f911-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="8f911-108">Wir werden an dieser Stelle Links zur offiziellen Dokumentation einfügen, sobald diese veröffentlicht ist.</span><span class="sxs-lookup"><span data-stu-id="8f911-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-3"></a><span data-ttu-id="8f911-109">Preview 3</span><span class="sxs-lookup"><span data-stu-id="8f911-109">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="8f911-110">Gefilterte Include-Funktion</span><span class="sxs-lookup"><span data-stu-id="8f911-110">Filtered Include</span></span>

<span data-ttu-id="8f911-111">Die Include-Methode unterstützt jetzt das Filtern der enthaltenen Entitäten.</span><span class="sxs-lookup"><span data-stu-id="8f911-111">The Include method now supports filtering of the entities included.</span></span>
<span data-ttu-id="8f911-112">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8f911-112">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="8f911-113">Mit dieser Abfrage werden Blogs zusammen mit den jeweiligen Beiträgen zurückgegeben, jedoch nur, wenn der Titel „Cheese“ enthält.</span><span class="sxs-lookup"><span data-stu-id="8f911-113">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="8f911-114">Skip und Take können auch verwendet werden, um die Anzahl der enthaltenen Entitäten zu verringern.</span><span class="sxs-lookup"><span data-stu-id="8f911-114">Skip and Take can also be used to reduce the number of included entities.</span></span>
<span data-ttu-id="8f911-115">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8f911-115">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="8f911-116">Mit dieser Abfrage werden Blogs mit höchstens fünf Beiträgen zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="8f911-116">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="8f911-117">Ausführliche Informationen finden Sie in der [Dokumentation zu Include](xref:core/querying/related-data#filtered-include).</span><span class="sxs-lookup"><span data-stu-id="8f911-117">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="8f911-118">Neue ModelBuilder-API für Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="8f911-118">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="8f911-119">Navigationseigenschaften werden in erster Linie beim [Definieren von Beziehungen](xref:core/modeling/relationships) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="8f911-119">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span>
<span data-ttu-id="8f911-120">Die neue `Navigation`-Methode kann jedoch in den Fällen verwendet werden, in denen Navigationseigenschaften eine zusätzliche Konfiguration erfordern.</span><span class="sxs-lookup"><span data-stu-id="8f911-120">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span>
<span data-ttu-id="8f911-121">So können Sie z. B. ein Unterstützungsfeld für die Navigation festlegen, wenn das Feld nach Konvention nicht gefunden wird:</span><span class="sxs-lookup"><span data-stu-id="8f911-121">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="8f911-122">Beachten Sie, dass die `Navigation`-API die Beziehungskonfiguration nicht ersetzt.</span><span class="sxs-lookup"><span data-stu-id="8f911-122">Note that the `Navigation` API does not replace relationship configuration.</span></span>
<span data-ttu-id="8f911-123">Stattdessen ermöglicht sie eine zusätzliche Konfiguration der Navigationseigenschaften in bereits ermittelten oder definierten Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="8f911-123">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="8f911-124">Die Dokumentation finden Sie im Issue [2302](https://github.com/dotnet/EntityFramework.Docs/issues/2302).</span><span class="sxs-lookup"><span data-stu-id="8f911-124">Documentation is tracked by issue [#2302](https://github.com/dotnet/EntityFramework.Docs/issues/2302).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="8f911-125">Neue Befehlszeilenparameter für Namespaces und Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="8f911-125">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="8f911-126">Bei Migrationen und Gerüstbau können Namespaces jetzt in der Befehlszeile angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="8f911-126">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span>
<span data-ttu-id="8f911-127">So können Sie beispielsweise ein Reverse Engineering für eine Datenbank durchführen, um die Kontext- und Modellklassen in verschiedenen Namespaces zu platzieren:</span><span class="sxs-lookup"><span data-stu-id="8f911-127">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="8f911-128">Eine Verbindungszeichenfolge kann nun auch an den `database-update`-Befehl übermittelt werden:</span><span class="sxs-lookup"><span data-stu-id="8f911-128">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="8f911-129">Gleichwertige Parameter wurden den PowerShell-Befehlen hinzugefügt, die in der Paket-Manager-Konsole in Visual Studio verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8f911-129">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

<span data-ttu-id="8f911-130">Die Dokumentation finden Sie im Issue [2303](https://github.com/dotnet/EntityFramework.Docs/issues/2303).</span><span class="sxs-lookup"><span data-stu-id="8f911-130">Documentation is tracked by issue [#2303](https://github.com/dotnet/EntityFramework.Docs/issues/2303).</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="8f911-131">EnableDetailedErrors wieder verfügbar</span><span class="sxs-lookup"><span data-stu-id="8f911-131">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="8f911-132">Aus Leistungsgründen führt EF keine zusätzlichen NULL-Überprüfungen durch, wenn Werte aus der Datenbank gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="8f911-132">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span>
<span data-ttu-id="8f911-133">Dadurch können Ausnahmen ausgelöst werden, deren Ursache schwer zu ermitteln ist, wenn ein unerwarteter NULL-Wert auftritt.</span><span class="sxs-lookup"><span data-stu-id="8f911-133">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="8f911-134">Durch die Verwendung von `EnableDetailedErrors` wird eine zusätzliche NULL-Überprüfung auf Abfragen hinzugefügt, sodass diese Fehler mit geringem Leistungsaufwand einfacher auf ihre Ursache zurückzuführen sind.</span><span class="sxs-lookup"><span data-stu-id="8f911-134">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="8f911-135">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8f911-135">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="8f911-136">Die Dokumentation finden Sie im Issue [955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span><span class="sxs-lookup"><span data-stu-id="8f911-136">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="8f911-137">Cosmos-Partitionsschlüssel</span><span class="sxs-lookup"><span data-stu-id="8f911-137">Cosmos partition keys</span></span>

<span data-ttu-id="8f911-138">Der Partitionsschlüssel, der für eine bestimmte Abfrage verwendet werden soll, kann jetzt in der Abfrage angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="8f911-138">The partition key to use for a given query can now be specified in the query.</span></span>
<span data-ttu-id="8f911-139">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8f911-139">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="8f911-140">Die Dokumentation finden Sie im Issue [2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span><span class="sxs-lookup"><span data-stu-id="8f911-140">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="8f911-141">Unterstützung für die DATALENGTH-Funktion von SQL Server</span><span class="sxs-lookup"><span data-stu-id="8f911-141">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="8f911-142">Auf diese kann mit der neuen `EF.Functions.DataLength`-Methode zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="8f911-142">This can be accessed using the new `EF.Functions.DataLength` method.</span></span>
<span data-ttu-id="8f911-143">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8f911-143">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="8f911-144">Vorschau 2</span><span class="sxs-lookup"><span data-stu-id="8f911-144">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="8f911-145">Verwenden eines C#-Attributs zum Angeben eines Eigenschaftsunterstützungsfelds</span><span class="sxs-lookup"><span data-stu-id="8f911-145">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="8f911-146">Es kann jetzt ein C#-Attribut verwendet werden, um das Unterstützungsfeld für eine Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="8f911-146">A C# attribute can now be used to specify the backing field for a property.</span></span>
<span data-ttu-id="8f911-147">Dieses Attribut ermöglicht es EF Core, wie sonst auch in das Unterstützungsfeld zu schreiben und daraus zu lesen, auch wenn das Unterstützungsfeld nicht automatisch gefunden werden kann.</span><span class="sxs-lookup"><span data-stu-id="8f911-147">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span>
<span data-ttu-id="8f911-148">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8f911-148">For example:</span></span>

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

<span data-ttu-id="8f911-149">Die Dokumentation finden Sie im Issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span><span class="sxs-lookup"><span data-stu-id="8f911-149">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="8f911-150">Vollständige Diskriminatorzuordnung</span><span class="sxs-lookup"><span data-stu-id="8f911-150">Complete discriminator mapping</span></span>

<span data-ttu-id="8f911-151">EF Core verwendet eine Diskriminatorspalte für die [TPH-Zuordnung (Tabelle pro Hierarchie) von Vererbungshierarchien](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="8f911-151">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span>
<span data-ttu-id="8f911-152">Es sind einige Leistungsverbesserungen möglich, solange EF Core alle möglichen Werte für den Diskriminator kennt.</span><span class="sxs-lookup"><span data-stu-id="8f911-152">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span>
<span data-ttu-id="8f911-153">Diese Verbesserungen sind nun in EF Core 5.0 implementiert.</span><span class="sxs-lookup"><span data-stu-id="8f911-153">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="8f911-154">Frühere Versionen von EF Core generierten beispielsweise für Abfragen immer einen SQL-Code wie den folgenden, in dem alle Typen in einer Hierarchie zurückgegeben wurden:</span><span class="sxs-lookup"><span data-stu-id="8f911-154">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="8f911-155">EF Core 5.0 generiert nun Folgendes, wenn eine vollständige Diskriminatorzuordnung konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="8f911-155">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="8f911-156">Ab Vorschauversion 3 wird dies das Standardverhalten.</span><span class="sxs-lookup"><span data-stu-id="8f911-156">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="8f911-157">Leistungsverbesserungen in Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="8f911-157">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="8f911-158">Es wurden zwei Leistungsverbesserungen für SQLite vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="8f911-158">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="8f911-159">Das Abrufen von Binär- und Zeichenfolgendaten mit GetBytes, GetChars und GetTextReader ist nun effizienter durch die Verwendung von SqliteBlob und Datenströmen.</span><span class="sxs-lookup"><span data-stu-id="8f911-159">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="8f911-160">Die Initialisierung von SqliteConnection ist nun verzögert.</span><span class="sxs-lookup"><span data-stu-id="8f911-160">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="8f911-161">Diese Verbesserungen wurden am ADO.NET-Anbieter Microsoft.Data.Sqlite vorgenommen und führen daher auch zu Leistungsverbesserungen außerhalb von EF Core.</span><span class="sxs-lookup"><span data-stu-id="8f911-161">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="8f911-162">Vorschauversion 1</span><span class="sxs-lookup"><span data-stu-id="8f911-162">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="8f911-163">Einfache Protokollierung</span><span class="sxs-lookup"><span data-stu-id="8f911-163">Simple logging</span></span>

<span data-ttu-id="8f911-164">Dieses Feature fügt eine Funktion hinzu, die `Database.Log` in EF6 ähnelt.</span><span class="sxs-lookup"><span data-stu-id="8f911-164">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="8f911-165">Es bietet eine einfache Möglichkeit zum Abrufen von Protokollen aus EF Core, ohne ein externes Protokollierungsframework konfigurieren zu müssen.</span><span class="sxs-lookup"><span data-stu-id="8f911-165">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="8f911-166">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 5. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="8f911-166">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="8f911-167">Zusätzliche Dokumentation finden Sie im Issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span><span class="sxs-lookup"><span data-stu-id="8f911-167">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="8f911-168">Einfache Möglichkeit zum Abrufen von generiertem SQL-Code</span><span class="sxs-lookup"><span data-stu-id="8f911-168">Simple way to get generated SQL</span></span>

<span data-ttu-id="8f911-169">In EF Core 5.0 wird die Erweiterungsmethode `ToQueryString` eingeführt, die den von EF Core beim Ausführen einer LINQ-Abfrage generierten SQL-Code zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="8f911-169">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="8f911-170">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 9. Januar 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="8f911-170">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="8f911-171">Zusätzliche Dokumentation finden Sie im Issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span><span class="sxs-lookup"><span data-stu-id="8f911-171">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="8f911-172">Verwenden eines C#-Attributs zum Angeben des Fehlens eines Schlüssels einer Entität</span><span class="sxs-lookup"><span data-stu-id="8f911-172">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="8f911-173">Ein Entitätstyp kann nun mithilfe des neuen Attributs `KeylessAttribute` als schlüssellos konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="8f911-173">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="8f911-174">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8f911-174">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="8f911-175">Die Dokumentation finden Sie im Issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span><span class="sxs-lookup"><span data-stu-id="8f911-175">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="8f911-176">Ändern von Verbindung oder Verbindungszeichenfolge in initialisiertem DbContext möglich</span><span class="sxs-lookup"><span data-stu-id="8f911-176">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="8f911-177">Es ist jetzt einfacher, eine DbContext-Instanz ohne Verbindung oder Verbindungszeichenfolge zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="8f911-177">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="8f911-178">Die Verbindung oder Verbindungszeichenfolge kann jetzt auch in der Kontextinstanz verändert werden.</span><span class="sxs-lookup"><span data-stu-id="8f911-178">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="8f911-179">Mithilfe dieses Features kann ein und dieselbe Kontextinstanz dynamisch eine Verbindung mit verschiedenen Datenbanken herstellen.</span><span class="sxs-lookup"><span data-stu-id="8f911-179">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="8f911-180">Die Dokumentation finden Sie im Issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span><span class="sxs-lookup"><span data-stu-id="8f911-180">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="8f911-181">Proxys zur Änderungsnachverfolgung</span><span class="sxs-lookup"><span data-stu-id="8f911-181">Change-tracking proxies</span></span>

<span data-ttu-id="8f911-182">EF Core kann jetzt Runtimeproxys generieren, die automatisch [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) und [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) implementieren.</span><span class="sxs-lookup"><span data-stu-id="8f911-182">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="8f911-183">Diese melden dann Wertänderungen bei Entitätseigenschaften direkt an EF Core, sodass die Notwendigkeit einer Überprüfung auf Änderungen entfällt.</span><span class="sxs-lookup"><span data-stu-id="8f911-183">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="8f911-184">Für Proxys gelten jedoch bestimmte Einschränkungen, daher eignen sie sich nicht in jedem Fall.</span><span class="sxs-lookup"><span data-stu-id="8f911-184">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="8f911-185">Die Dokumentation finden Sie im Issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span><span class="sxs-lookup"><span data-stu-id="8f911-185">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="8f911-186">Erweiterte Debugansichten</span><span class="sxs-lookup"><span data-stu-id="8f911-186">Enhanced debug views</span></span>

<span data-ttu-id="8f911-187">Debugansichten sind eine einfache Möglichkeit, beim Debuggen von Problemen interne Vorgänge in EF Core zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="8f911-187">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="8f911-188">Ein Debugansicht für das Modell wurde bereits vor einiger Zeit implementiert.</span><span class="sxs-lookup"><span data-stu-id="8f911-188">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="8f911-189">Für EF Core 5.0 haben wir die Modellansicht vereinfacht und im Zustands-Manager eine neue Debugansicht für nachverfolgte Entitäten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8f911-189">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="8f911-190">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 12. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="8f911-190">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="8f911-191">Zusätzliche Dokumentation finden Sie im Issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span><span class="sxs-lookup"><span data-stu-id="8f911-191">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="8f911-192">Verbesserte Verarbeitung von NULL-Semantik in der Datenbank</span><span class="sxs-lookup"><span data-stu-id="8f911-192">Improved handling of database null semantics</span></span>

<span data-ttu-id="8f911-193">Relationale Datenbanken behandeln NULL in der Regel als unbekannten Wert und daher als ungleich jedem anderen NULL-Wert.</span><span class="sxs-lookup"><span data-stu-id="8f911-193">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="8f911-194">C# hingegen behandelt NULL als definierten Wert, der mit jedem anderen NULL-Wert übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="8f911-194">While C# treats null as a defined value, which compares equal to any other null.</span></span>
<span data-ttu-id="8f911-195">EF Core übersetzt Abfragen standardmäßig so, dass sie die NULL-Semantik von C# verwenden.</span><span class="sxs-lookup"><span data-stu-id="8f911-195">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="8f911-196">In EF Core 5.0 wurde die Effizienz dieser Übersetzungen erheblich verbessert.</span><span class="sxs-lookup"><span data-stu-id="8f911-196">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="8f911-197">Die Dokumentation finden Sie im Issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span><span class="sxs-lookup"><span data-stu-id="8f911-197">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="8f911-198">Indexereigenschaften</span><span class="sxs-lookup"><span data-stu-id="8f911-198">Indexer properties</span></span>

<span data-ttu-id="8f911-199">EF Core 5.0 unterstützt die Zuordnung von C#-Indexereigenschaften.</span><span class="sxs-lookup"><span data-stu-id="8f911-199">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="8f911-200">Durch diese Eigenschaften können Entitäten als Eigenschaftenbehälter fungieren, wobei Spalten benannten Eigenschaften im Behälter zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="8f911-200">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="8f911-201">Die Dokumentation finden Sie im Issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span><span class="sxs-lookup"><span data-stu-id="8f911-201">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="8f911-202">Generieren von CHECK-Einschränkungen für Enumerationszuordnungen</span><span class="sxs-lookup"><span data-stu-id="8f911-202">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="8f911-203">EF Core 5.0-Migrationen können jetzt CHECK-Einschränkungen aus Zuordnungen der enum-Eigenschaft generieren.</span><span class="sxs-lookup"><span data-stu-id="8f911-203">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="8f911-204">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8f911-204">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="8f911-205">Die Dokumentation finden Sie im Issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span><span class="sxs-lookup"><span data-stu-id="8f911-205">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="8f911-206">IsRelational</span><span class="sxs-lookup"><span data-stu-id="8f911-206">IsRelational</span></span>

<span data-ttu-id="8f911-207">Zusätzlich zu den existierenden `IsSqlServer`-, `IsSqlite`- und `IsInMemory`-Methoden wurde eine neue `IsRelational`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8f911-207">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="8f911-208">Diese Methode kann verwendet werden, um zu testen, ob DbContext einen Anbieter einer relationalen Datenbank verwendet.</span><span class="sxs-lookup"><span data-stu-id="8f911-208">This method can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="8f911-209">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8f911-209">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="8f911-210">Die Dokumentation finden Sie im Issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span><span class="sxs-lookup"><span data-stu-id="8f911-210">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="8f911-211">Optimistische Nebenläufigkeit in Cosmos mit Entitätstags (ETags)</span><span class="sxs-lookup"><span data-stu-id="8f911-211">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="8f911-212">Der Azure Cosmos DB-Datenbankanbieter unterstützt nun optimistische Nebenläufigkeit mithilfe von ETags.</span><span class="sxs-lookup"><span data-stu-id="8f911-212">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="8f911-213">Verwenden Sie das Modellerstellungstool in OnModelCreating, um ein ETag zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="8f911-213">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="8f911-214">SaveChanges löst dann eine `DbUpdateConcurrencyException`-Ausnahme für einen Nebenläufigkeitskonflikt aus, die [verarbeitet werden kann](https://docs.microsoft.com/ef/core/saving/concurrency), z. B. um Wiederholungsversuche zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="8f911-214">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="8f911-215">Die Dokumentation finden Sie im Issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span><span class="sxs-lookup"><span data-stu-id="8f911-215">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="8f911-216">Abfrageübersetzungen für weitere DateTime-Konstrukte</span><span class="sxs-lookup"><span data-stu-id="8f911-216">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="8f911-217">Abfragen mit neuer DateTime-Konstruktion werden jetzt übersetzt.</span><span class="sxs-lookup"><span data-stu-id="8f911-217">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="8f911-218">Außerdem werden nun die folgenden SQL Server-Funktionen zugeordnet:</span><span class="sxs-lookup"><span data-stu-id="8f911-218">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="8f911-219">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="8f911-219">DateDiffWeek</span></span>
* <span data-ttu-id="8f911-220">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="8f911-220">DateFromParts</span></span>

<span data-ttu-id="8f911-221">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8f911-221">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="8f911-222">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="8f911-222">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="8f911-223">Abfrageübersetzungen für weitere Bytearraykonstrukte</span><span class="sxs-lookup"><span data-stu-id="8f911-223">Query translations for more byte array constructs</span></span>

<span data-ttu-id="8f911-224">Abfragen, die Contains, Length, SequenceEqual usw. in byte[]-Eigenschaften verwenden, werden jetzt in SQL übersetzt.</span><span class="sxs-lookup"><span data-stu-id="8f911-224">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="8f911-225">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 5. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="8f911-225">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="8f911-226">Zusätzliche Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="8f911-226">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="8f911-227">Abfrageübersetzung für Reverse</span><span class="sxs-lookup"><span data-stu-id="8f911-227">Query translation for Reverse</span></span>

<span data-ttu-id="8f911-228">Abfragen, die `Reverse` verwenden, werden jetzt übersetzt.</span><span class="sxs-lookup"><span data-stu-id="8f911-228">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="8f911-229">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8f911-229">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="8f911-230">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="8f911-230">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="8f911-231">Abfrageübersetzung für bitweise Operatoren</span><span class="sxs-lookup"><span data-stu-id="8f911-231">Query translation for bitwise operators</span></span>

<span data-ttu-id="8f911-232">Abfragen, die bitweise Operatoren verwenden, werden jetzt in mehr Fällen übersetzt. Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8f911-232">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="8f911-233">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="8f911-233">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="8f911-234">Abfrageübersetzung für Zeichenfolgen in Cosmos</span><span class="sxs-lookup"><span data-stu-id="8f911-234">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="8f911-235">Abfragen, die die Zeichenfolgenmethoden Contains, StartsWith und EndsWith verwenden, werden jetzt übersetzt, wenn der Azure Cosmos DB-Anbieter verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8f911-235">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="8f911-236">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="8f911-236">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
