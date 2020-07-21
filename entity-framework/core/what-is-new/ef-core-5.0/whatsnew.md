---
title: Neuerungen in EF Core 5.0
description: Übersicht über neue Features in EF Core 5.0
author: ajcvickers
ms.date: 06/02/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 304ed74fe344b43177525113c70b7be7bb0ac5ed
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238332"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="e8351-103">Neuerungen in EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="e8351-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="e8351-104">EF Core 5.0 befindet sich derzeit in der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="e8351-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="e8351-105">Diese Seite bietet eine Übersicht über interessante Änderungen in den einzelnen Vorschauversionen.</span><span class="sxs-lookup"><span data-stu-id="e8351-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="e8351-106">Auf dieser Seite wird der [Plan für EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan) nicht erneut aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="e8351-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="e8351-107">Der Plan beschreibt die allgemeinen Themen für EF Core 5.0 einschließlich sämtlicher Features, die wir vor Auslieferung des finalen Releases integrieren möchten.</span><span class="sxs-lookup"><span data-stu-id="e8351-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="e8351-108">Wir werden an dieser Stelle Links zur offiziellen Dokumentation einfügen, sobald diese veröffentlicht ist.</span><span class="sxs-lookup"><span data-stu-id="e8351-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-6"></a><span data-ttu-id="e8351-109">Vorschau 6</span><span class="sxs-lookup"><span data-stu-id="e8351-109">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="e8351-110">Aufteilen von Abfragen für ähnliche Sammlungen</span><span class="sxs-lookup"><span data-stu-id="e8351-110">Split queries for related collections</span></span>

<span data-ttu-id="e8351-111">Ab EF Core 3.0 generiert EF Core für jede LINQ-Abfrage eine einzelne SQL-Abfrage.</span><span class="sxs-lookup"><span data-stu-id="e8351-111">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="e8351-112">Dadurch wird sichergestellt, dass die zurückgegebenen Daten im Rahmen der Einschränkungen des verwendeten Transaktionsmodus konsistent sind.</span><span class="sxs-lookup"><span data-stu-id="e8351-112">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="e8351-113">Wenn die Abfrage `Include` oder eine Projektion verwendet, um mehrere ähnliche Sammlungen zurückzugeben, kann die Ausführung jedoch sehr lange dauern.</span><span class="sxs-lookup"><span data-stu-id="e8351-113">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="e8351-114">In EF Core 5.0 ist es jetzt möglich, eine einzelne LINQ-Abfrage und die zugehörigen Sammlungen auf mehrere SQL-Abfragen aufzuteilen.</span><span class="sxs-lookup"><span data-stu-id="e8351-114">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="e8351-115">Die Leistung kann dadurch zwar erheblich verbessert werden, doch bei den zurückgegebenen Ergebnissen kann es zu Inkonsistenzen kommen, wenn die Daten sich zwischen den beiden Abfragen ändern.</span><span class="sxs-lookup"><span data-stu-id="e8351-115">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="e8351-116">Serialisierbare Transaktionen oder Momentaufnahmentransaktionen können verwendet werden, um das zu verhindern und Konsistenz bei aufgeteilten Abfragen zu erzielen. Dadurch kann jedoch die Leistung beeinträchtigt werden, und das Verhalten kann sich unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="e8351-116">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="e8351-117">Aufteilen von Abfragen mit Include</span><span class="sxs-lookup"><span data-stu-id="e8351-117">Split queries with Include</span></span>

<span data-ttu-id="e8351-118">Gehen Sie von einer Abfrage aus, die mithilfe von `Include` zwei Ebenen ähnlicher Sammlungen einschließt:</span><span class="sxs-lookup"><span data-stu-id="e8351-118">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="e8351-119">Standardmäßig generiert EF Core den folgenden SQL-Wert, wenn der SQLite-Anbieter verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="e8351-119">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

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

<span data-ttu-id="e8351-120">Die neue `AsSplitQuery`-API kann verwendet werden, um dieses Verhalten zu ändern.</span><span class="sxs-lookup"><span data-stu-id="e8351-120">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="e8351-121">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-121">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="e8351-122">AsSplitQuery ist für alle relationalen Datenbankanbieter verfügbar und kann genau wie AsNoTracking an beliebiger Stelle in der Abfrage verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-122">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="e8351-123">In EF Core werden nun die folgenden drei SQL-Abfragen generiert:</span><span class="sxs-lookup"><span data-stu-id="e8351-123">EF Core will now generate the following three SQL queries:</span></span>

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

<span data-ttu-id="e8351-124">Alle Vorgänge für den Abfragestamm werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e8351-124">All operations on the query root are supported.</span></span> <span data-ttu-id="e8351-125">Das schließt OrderBy-, Skip-, Take-, Join-, FirstOrDefault-Vorgänge und ähnliche Vorgänge ein, die ein einzelnes Ergebnis auswählen.</span><span class="sxs-lookup"><span data-stu-id="e8351-125">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="e8351-126">Beachten Sie, dass nach OrderBy, Skip und Take gefilterte Include-Abfragen in Preview 6 nicht unterstützt werden, aber in den täglichen Builds und zukünftig in Preview 7 enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="e8351-126">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="e8351-127">Aufteilen von Abfragen mit Sammlungsprojektionen</span><span class="sxs-lookup"><span data-stu-id="e8351-127">Split queries with collection projections</span></span>

<span data-ttu-id="e8351-128">`AsSplitQuery` kann auch verwendet werden, wenn Sammlungen in Projektionen geladen werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-128">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="e8351-129">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-129">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="e8351-130">Diese LINQ-Abfrage generiert die folgenden zwei SQL-Abfragen, wenn der SQLite-Anbieter verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="e8351-130">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="e8351-131">Beachten Sie, dass nur die Materialisierung der Sammlung unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="e8351-131">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="e8351-132">Jede Komposition nach `e.Albums` im obigen Fall führt nicht zu einer aufgeteilten Abfrage.</span><span class="sxs-lookup"><span data-stu-id="e8351-132">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="e8351-133">Die Verbesserungen in diesem Bereich werden in [Issue 21234](https://github.com/dotnet/efcore/issues/21234) nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="e8351-133">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="e8351-134">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="e8351-134">IndexAttribute</span></span>

<span data-ttu-id="e8351-135">Die neue IndexAttribute-Klasse kann für einen Entitätstyp verwendet werden, um einen Index für eine einzelne Spalte anzugeben.</span><span class="sxs-lookup"><span data-stu-id="e8351-135">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="e8351-136">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-136">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }
    
    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="e8351-137">Bei SQL Server generieren Migrationen dann folgenden SQL-Code:</span><span class="sxs-lookup"><span data-stu-id="e8351-137">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="e8351-138">IndexAttribute kann auch verwendet werden, um einen Index anzugeben, der mehrere Spalten umfasst.</span><span class="sxs-lookup"><span data-stu-id="e8351-138">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="e8351-139">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-139">For example:</span></span>

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

<span data-ttu-id="e8351-140">Für SQL Server ergibt sich Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e8351-140">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="e8351-141">Die Dokumentation finden Sie im [Issue 2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span><span class="sxs-lookup"><span data-stu-id="e8351-141">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="e8351-142">Verbesserte Ausnahmen bei der Abfrageübersetzung</span><span class="sxs-lookup"><span data-stu-id="e8351-142">Improved query translation exceptions</span></span>

<span data-ttu-id="e8351-143">Wir arbeiten weiter an der Verbesserung der Ausnahmemeldungen, die generiert werden, wenn bei der Abfrageübersetzung ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="e8351-143">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="e8351-144">Diese Abfrage verwendet z. B. die nicht zugeordnete Eigenschaft `IsSigned`:</span><span class="sxs-lookup"><span data-stu-id="e8351-144">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="e8351-145">EF Core löst die folgende Ausnahme aus, die angibt, dass bei der Übersetzung ein Fehler aufgetreten ist, da `IsSigned` nicht zugeordnet ist:</span><span class="sxs-lookup"><span data-stu-id="e8351-145">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="e8351-146">Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="e8351-146">Unhandled exception.</span></span> <span data-ttu-id="e8351-147">System.InvalidOperationException: Der LINQ-Ausdruck „DbSet<Artist>() .Where(a => a.IsSigned)“ kann nicht übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-147">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="e8351-148">Zusätzliche Informationen: Bei der Übersetzung des Members IsSigned für den Entitätstyp Artist ist ein Fehler aufgetreten.</span><span class="sxs-lookup"><span data-stu-id="e8351-148">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="e8351-149">Möglicherweise ist der angegebene Member nicht zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="e8351-149">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="e8351-150">Schreiben Sie die Abfrage in einem übersetzbaren Format neu, oder wechseln Sie explizit zur Clientauswertung, indem Sie einen Aufruf von AsEnumerable(), AsAsyncEnumerable(), ToList() oder ToListAsync() einfügen.</span><span class="sxs-lookup"><span data-stu-id="e8351-150">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="e8351-151">Weitere Informationen finden Sie unter https://go.microsoft.com/fwlink/?linkid=2101038.</span><span class="sxs-lookup"><span data-stu-id="e8351-151">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="e8351-152">Zudem werden jetzt bessere Ausnahmemeldungen generiert, wenn Sie versuchen, Zeichenfolgenvergleiche mit kulturabhängiger Semantik zu übersetzen.</span><span class="sxs-lookup"><span data-stu-id="e8351-152">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="e8351-153">Diese Abfrage versucht beispielsweise, `StringComparison.CurrentCulture` zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="e8351-153">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="e8351-154">In EF Core wird nun die folgende Ausnahme ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="e8351-154">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="e8351-155">Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="e8351-155">Unhandled exception.</span></span> <span data-ttu-id="e8351-156">System.InvalidOperationException: Der LINQ-Ausdruck „DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))“ kann nicht übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-156">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="e8351-157">Zusätzliche Informationen: Die Übersetzung der Methode string.Equals, die das Argument StringComparison verwendet, wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e8351-157">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="e8351-158">Weitere Informationen finden Sie unter https://go.microsoft.com/fwlink/?linkid=2129535.</span><span class="sxs-lookup"><span data-stu-id="e8351-158">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="e8351-159">Schreiben Sie die Abfrage in einem übersetzbaren Format neu, oder wechseln Sie explizit zur Clientauswertung, indem Sie einen Aufruf von AsEnumerable(), AsAsyncEnumerable(), ToList() oder ToListAsync() einfügen.</span><span class="sxs-lookup"><span data-stu-id="e8351-159">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="e8351-160">Weitere Informationen finden Sie unter https://go.microsoft.com/fwlink/?linkid=2101038.</span><span class="sxs-lookup"><span data-stu-id="e8351-160">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="e8351-161">Angeben der Transaktions-ID</span><span class="sxs-lookup"><span data-stu-id="e8351-161">Specify transaction ID</span></span>

<span data-ttu-id="e8351-162">Dieses Feature stammt vom Communitymitglied [@Marusyk](https://github.com/Marusyk).</span><span class="sxs-lookup"><span data-stu-id="e8351-162">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="e8351-163">Danke für diesen Beitrag!</span><span class="sxs-lookup"><span data-stu-id="e8351-163">Many thanks for the contribution!</span></span>

<span data-ttu-id="e8351-164">EF Core macht eine Transaktions-ID für die Korrelation von aufrufübergreifenden Transaktionen verfügbar.</span><span class="sxs-lookup"><span data-stu-id="e8351-164">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="e8351-165">Diese ID wird in der Regel durch EF Core festgelegt, wenn eine Transaktion gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="e8351-165">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="e8351-166">Wenn die Anwendung stattdessen die Transaktion startet, ermöglicht dieses Feature der Anwendung, die Transaktions-ID explizit festzulegen, damit sie überall ordnungsgemäß korreliert, wo sie verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e8351-166">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="e8351-167">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-167">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="e8351-168">IPAddress-Zuordnung</span><span class="sxs-lookup"><span data-stu-id="e8351-168">IPAddress mapping</span></span>

<span data-ttu-id="e8351-169">Dieses Feature stammt vom Communitymitglied [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span><span class="sxs-lookup"><span data-stu-id="e8351-169">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="e8351-170">Danke für diesen Beitrag!</span><span class="sxs-lookup"><span data-stu-id="e8351-170">Many thanks for the contribution!</span></span>

<span data-ttu-id="e8351-171">Die neue .NET Standard-Klasse [IPAddress](/dotnet/api/system.net.ipaddress) wird jetzt automatisch einer Zeichenfolgenspalte für Datenbanken zugeordnet, die noch nicht nativ unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-171">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="e8351-172">Sehen Sie sich beispielsweise die Zuordnung dieses Entitätstyps an:</span><span class="sxs-lookup"><span data-stu-id="e8351-172">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="e8351-173">In SQL Server führt dies zu Migrationen, die die folgende Tabelle erstellen:</span><span class="sxs-lookup"><span data-stu-id="e8351-173">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
``` 

<span data-ttu-id="e8351-174">Entitäten können dann wie gewohnt hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="e8351-174">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
``` 

<span data-ttu-id="e8351-175">Die resultierende SQL-Abfrage fügt die normalisierte IPv4- oder IPv6-Adresse ein:</span><span class="sxs-lookup"><span data-stu-id="e8351-175">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="e8351-176">Ausschließen von OnConfiguring beim Gerüstbau</span><span class="sxs-lookup"><span data-stu-id="e8351-176">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="e8351-177">Wenn das Gerüst für DbContext aus einer vorhandenen Datenbank erstellt wird, erstellt EF Core standardmäßig eine OnConfiguring-Überladung mit einer Verbindungszeichenfolge, damit der Kontext sofort einsatzbereit ist.</span><span class="sxs-lookup"><span data-stu-id="e8351-177">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="e8351-178">Das ist jedoch nicht nützlich, wenn Sie bereits eine partielle Klasse mit OnConfiguring besitzen oder den Kontext auf andere Weise konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="e8351-178">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="e8351-179">Die Gerüstbaubefehle können jetzt so geändert werden, dass OnConfiguring nicht generiert wird, um das Problem zu beheben.</span><span class="sxs-lookup"><span data-stu-id="e8351-179">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="e8351-180">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-180">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="e8351-181">In der Paket-Manager-Konsole:</span><span class="sxs-lookup"><span data-stu-id="e8351-181">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring 
``` 

<span data-ttu-id="e8351-182">Die Verwendung einer [benannten Verbindungszeichenfolge und eines sicheren Speichers wie Benutzergeheimnisse](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets) wird empfohlen.</span><span class="sxs-lookup"><span data-stu-id="e8351-182">Note that we recommend using [a named connection string and secure storage like User Secrets](/core/managing-schemas/scaffolding?tabs=vs#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="e8351-183">Übersetzungen für FirstOrDefault in Zeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="e8351-183">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="e8351-184">Dieses Feature stammt vom Communitymitglied [@dvoreckyaa](https://github.com/dvoreckyaa).</span><span class="sxs-lookup"><span data-stu-id="e8351-184">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="e8351-185">Danke für diesen Beitrag!</span><span class="sxs-lookup"><span data-stu-id="e8351-185">Many thanks for the contribution!</span></span>

<span data-ttu-id="e8351-186">FirstOrDefault und ähnliche Operatoren für Verbindungszeichenfolgen werden jetzt übersetzt.</span><span class="sxs-lookup"><span data-stu-id="e8351-186">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="e8351-187">Nehmen Sie diese LINQ-Abfrage als Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-187">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="e8351-188">Sie wird jetzt in folgenden SQL-Code übersetzt, wenn Sie SQL Server verwenden:</span><span class="sxs-lookup"><span data-stu-id="e8351-188">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="e8351-189">Vereinfachen von CASE-Blöcken</span><span class="sxs-lookup"><span data-stu-id="e8351-189">Simplify case blocks</span></span>

<span data-ttu-id="e8351-190">EF Core generiert nun bessere Abfragen, wenn CASE-Blöcke verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-190">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="e8351-191">Nehmen Sie diese LINQ-Abfrage als Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-191">For example, this LINQ query:</span></span> 

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="e8351-192">In SQL Server wurde sie zuvor in Folgendes übersetzt:</span><span class="sxs-lookup"><span data-stu-id="e8351-192">Was on SQL Server formally translated to:</span></span>

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

<span data-ttu-id="e8351-193">Jetzt wird sie folgendermaßen übersetzt:</span><span class="sxs-lookup"><span data-stu-id="e8351-193">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
``` 

## <a name="preview-5"></a><span data-ttu-id="e8351-194">Preview 5</span><span class="sxs-lookup"><span data-stu-id="e8351-194">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="e8351-195">Datenbanksortierungen</span><span class="sxs-lookup"><span data-stu-id="e8351-195">Database collations</span></span>

<span data-ttu-id="e8351-196">Die Standardsortierung einer Datenbank kann nun im EF-Modell angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-196">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="e8351-197">Diese durchläuft generierte Migrationen, um die Sortierung beim Erstellen der Datenbank festzulegen.</span><span class="sxs-lookup"><span data-stu-id="e8351-197">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="e8351-198">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-198">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="e8351-199">Die Migrationen generieren dann Folgendes, um die Datenbank in SQL Server zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="e8351-199">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="e8351-200">Die Sortierung für spezifische Datenbankspalten kann ebenfalls angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-200">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="e8351-201">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-201">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="e8351-202">Für diejenigen, die keine Migrationen verwenden, wird für Sortierungen nun ein Reverse Engineering von der Datenbank aus durchgeführt, wenn der Gerüstbau für eine DbContext-Instanz durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="e8351-202">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="e8351-203">Schließlich lässt `EF.Functions.Collate()` Ad-hoc-Abfragen mit verschiedenen Sortierungen zu.</span><span class="sxs-lookup"><span data-stu-id="e8351-203">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="e8351-204">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-204">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="e8351-205">Dadurch wird die folgende Abfrage für SQL Server generiert:</span><span class="sxs-lookup"><span data-stu-id="e8351-205">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="e8351-206">Beachten Sie, dass Ad-Hoc-Sortierungen mit Bedacht verwendet werden sollten, da sie sich negativ auf die Datenbankleistung auswirken können.</span><span class="sxs-lookup"><span data-stu-id="e8351-206">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="e8351-207">Die Dokumentation finden Sie im [Issue 2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span><span class="sxs-lookup"><span data-stu-id="e8351-207">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="e8351-208">Übergeben von Argumenten in IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="e8351-208">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="e8351-209">Argumente können nun über die Befehlszeile in die `CreateDbContext`-Methode von [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1) übertragen werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-209">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1).</span></span> <span data-ttu-id="e8351-210">Sie können beispielsweise angeben, dass es sich um einen Entwicklungsbuild handelt, indem Sie in der Befehlszeile ein benutzerdefiniertes Argument (z. B. `dev`) übergeben:</span><span class="sxs-lookup"><span data-stu-id="e8351-210">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
``` 

<span data-ttu-id="e8351-211">Dieses Argument wird dann an die Factory weitergeleitet, wo es zum Steuern der Erstellung und Initialisierung des Kontexts verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="e8351-211">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="e8351-212">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-212">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="e8351-213">Die Dokumentation finden Sie im [Issue 2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span><span class="sxs-lookup"><span data-stu-id="e8351-213">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="e8351-214">Abfragen ohne Nachverfolgung mit Identitätsauflösung</span><span class="sxs-lookup"><span data-stu-id="e8351-214">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="e8351-215">Abfragen ohne Nachverfolgung können nun zum Durchführen von Identitätsauflösungen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-215">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="e8351-216">Die folgende Abfrage erstellt beispielsweise eine neue Blog-Instanz für alle Post-Entitäten, selbst wenn alle Blog-Instanzen über denselben Primärschlüssel verfügen.</span><span class="sxs-lookup"><span data-stu-id="e8351-216">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span> 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="e8351-217">Diese Abfrage kann angepasst werden, um sicherzustellen, dass nur eine einzige Blog-Instanz erstellt wird. Dies ist jedoch in der Regel ein wenig langsamer und nutzt mehr Arbeitsspeicher:</span><span class="sxs-lookup"><span data-stu-id="e8351-217">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="e8351-218">Beachten Sie, dass dies nur für Abfragen ohne Nachverfolgung nützlich ist, weil alle Abfragen mit Nachverfolgung dieses Verhalten bereits aufweisen.</span><span class="sxs-lookup"><span data-stu-id="e8351-218">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="e8351-219">Nach einer Überprüfung der API wird auch die `PerformIdentityResolution`-Syntax geändert.</span><span class="sxs-lookup"><span data-stu-id="e8351-219">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="e8351-220">Weitere Informationen dazu finden Sie im [Issue 19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span><span class="sxs-lookup"><span data-stu-id="e8351-220">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="e8351-221">Die Dokumentation finden Sie im [Issue 1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span><span class="sxs-lookup"><span data-stu-id="e8351-221">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="e8351-222">Gespeicherte (persistente) berechnete Spalten</span><span class="sxs-lookup"><span data-stu-id="e8351-222">Stored (persisted) computed columns</span></span>

<span data-ttu-id="e8351-223">Die meisten Datenbanken lassen zu, dass berechnete Spaltenwerte nach der Berechnung gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-223">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="e8351-224">Zwar wird dadurch Speicherplatz belegt, jedoch wird die berechnete Spalte nur einmal pro Update berechnet, anstatt jedes Mal, wenn der Wert abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="e8351-224">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="e8351-225">Dies ermöglicht auch die Indizierung der Spalte für einige Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="e8351-225">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="e8351-226">EF Core 5.0 ermöglicht die Konfiguration von berechneten Spalten als gespeichert.</span><span class="sxs-lookup"><span data-stu-id="e8351-226">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="e8351-227">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-227">For example:</span></span>
 
```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="e8351-228">Berechnete Spalten in SQLite</span><span class="sxs-lookup"><span data-stu-id="e8351-228">SQLite computed columns</span></span>

<span data-ttu-id="e8351-229">EF Core unterstützt nun berechnete Spalten in SQLite-Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="e8351-229">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="e8351-230">Preview 4</span><span class="sxs-lookup"><span data-stu-id="e8351-230">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="e8351-231">Konfigurieren von Datenbankgenauigkeit/Skalierung im Modell</span><span class="sxs-lookup"><span data-stu-id="e8351-231">Configure database precision/scale in model</span></span>

<span data-ttu-id="e8351-232">Genauigkeit und Skalierung einer Eigenschaft können jetzt mit dem Modellgenerator angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-232">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="e8351-233">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-233">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="e8351-234">Genauigkeit und Skalierung können weiterhin über den vollständigen Datenbanktyp festgelegt werden, z. B. „decimal(16,4)“.</span><span class="sxs-lookup"><span data-stu-id="e8351-234">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span> 

<span data-ttu-id="e8351-235">Die Dokumentation finden Sie im Issue [527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span><span class="sxs-lookup"><span data-stu-id="e8351-235">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="e8351-236">Angeben des Standardfüllfaktors für SQL Server-Indizes</span><span class="sxs-lookup"><span data-stu-id="e8351-236">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="e8351-237">Der Füllfaktor kann nun beim Erstellen eines Indexes auf SQL Server angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-237">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="e8351-238">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-238">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="e8351-239">Preview 3</span><span class="sxs-lookup"><span data-stu-id="e8351-239">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="e8351-240">Gefilterte Include-Funktion</span><span class="sxs-lookup"><span data-stu-id="e8351-240">Filtered Include</span></span>

<span data-ttu-id="e8351-241">Die Include-Methode unterstützt jetzt das Filtern der enthaltenen Entitäten.</span><span class="sxs-lookup"><span data-stu-id="e8351-241">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="e8351-242">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-242">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="e8351-243">Mit dieser Abfrage werden Blogs zusammen mit den jeweiligen Beiträgen zurückgegeben, jedoch nur, wenn der Titel „Cheese“ enthält.</span><span class="sxs-lookup"><span data-stu-id="e8351-243">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="e8351-244">Skip und Take können auch verwendet werden, um die Anzahl der enthaltenen Entitäten zu verringern.</span><span class="sxs-lookup"><span data-stu-id="e8351-244">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="e8351-245">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-245">For example:</span></span>
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="e8351-246">Mit dieser Abfrage werden Blogs mit höchstens fünf Beiträgen zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="e8351-246">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="e8351-247">Ausführliche Informationen finden Sie in der [Dokumentation zu Include](xref:core/querying/related-data#filtered-include).</span><span class="sxs-lookup"><span data-stu-id="e8351-247">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="e8351-248">Neue ModelBuilder-API für Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="e8351-248">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="e8351-249">Navigationseigenschaften werden in erster Linie beim [Definieren von Beziehungen](xref:core/modeling/relationships) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="e8351-249">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="e8351-250">Die neue `Navigation`-Methode kann jedoch in den Fällen verwendet werden, in denen Navigationseigenschaften eine zusätzliche Konfiguration erfordern.</span><span class="sxs-lookup"><span data-stu-id="e8351-250">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="e8351-251">So können Sie z. B. ein Unterstützungsfeld für die Navigation festlegen, wenn das Feld nach Konvention nicht gefunden wird:</span><span class="sxs-lookup"><span data-stu-id="e8351-251">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="e8351-252">Beachten Sie, dass die `Navigation`-API die Beziehungskonfiguration nicht ersetzt.</span><span class="sxs-lookup"><span data-stu-id="e8351-252">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="e8351-253">Stattdessen ermöglicht sie eine zusätzliche Konfiguration der Navigationseigenschaften in bereits ermittelten oder definierten Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="e8351-253">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="e8351-254">Weitere Informationen finden Sie unter [Konfigurieren von Navigationseigenschaften](xref:core/modeling/relationships#configuring-navigation-properties).</span><span class="sxs-lookup"><span data-stu-id="e8351-254">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="e8351-255">Neue Befehlszeilenparameter für Namespaces und Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="e8351-255">New command-line parameters for namespaces and connection strings</span></span> 

<span data-ttu-id="e8351-256">Bei Migrationen und Gerüstbau können Namespaces jetzt in der Befehlszeile angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-256">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="e8351-257">So können Sie beispielsweise ein Reverse Engineering für eine Datenbank durchführen, um die Kontext- und Modellklassen in verschiedenen Namespaces zu platzieren:</span><span class="sxs-lookup"><span data-stu-id="e8351-257">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span> 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="e8351-258">Ausführliche Informationen finden Sie in den Dokumentationen zu [Migrationen](xref:core/managing-schemas/migrations/index#namespaces) und [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces).</span><span class="sxs-lookup"><span data-stu-id="e8351-258">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="e8351-259">Eine Verbindungszeichenfolge kann nun auch an den `database-update`-Befehl übermittelt werden:</span><span class="sxs-lookup"><span data-stu-id="e8351-259">Also, a connection string can now be passed to the `database-update` command:</span></span>

```
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="e8351-260">Ausführliche Informationen finden Sie in der [Entity Framework Core Tools-Referenz – .NET-CLI](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update).</span><span class="sxs-lookup"><span data-stu-id="e8351-260">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="e8351-261">Gleichwertige Parameter wurden den PowerShell-Befehlen hinzugefügt, die in der Paket-Manager-Konsole in Visual Studio verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-261">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="e8351-262">EnableDetailedErrors wieder verfügbar</span><span class="sxs-lookup"><span data-stu-id="e8351-262">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="e8351-263">Aus Leistungsgründen führt EF keine zusätzlichen NULL-Überprüfungen durch, wenn Werte aus der Datenbank gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-263">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="e8351-264">Dadurch können Ausnahmen ausgelöst werden, deren Ursache schwer zu ermitteln ist, wenn ein unerwarteter NULL-Wert auftritt.</span><span class="sxs-lookup"><span data-stu-id="e8351-264">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="e8351-265">Durch die Verwendung von `EnableDetailedErrors` wird eine zusätzliche NULL-Überprüfung auf Abfragen hinzugefügt, sodass diese Fehler mit geringem Leistungsaufwand einfacher auf ihre Ursache zurückzuführen sind.</span><span class="sxs-lookup"><span data-stu-id="e8351-265">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>  

<span data-ttu-id="e8351-266">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-266">For example:</span></span>
```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors 
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="e8351-267">Die Dokumentation finden Sie im Issue [955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span><span class="sxs-lookup"><span data-stu-id="e8351-267">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="e8351-268">Cosmos-Partitionsschlüssel</span><span class="sxs-lookup"><span data-stu-id="e8351-268">Cosmos partition keys</span></span>

<span data-ttu-id="e8351-269">Der Partitionsschlüssel, der für eine bestimmte Abfrage verwendet werden soll, kann jetzt in der Abfrage angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-269">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="e8351-270">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-270">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="e8351-271">Die Dokumentation finden Sie im Issue [2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span><span class="sxs-lookup"><span data-stu-id="e8351-271">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="e8351-272">Unterstützung für die DATALENGTH-Funktion von SQL Server</span><span class="sxs-lookup"><span data-stu-id="e8351-272">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="e8351-273">Auf diese kann mit der neuen `EF.Functions.DataLength`-Methode zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-273">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="e8351-274">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-274">For example:</span></span>
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a><span data-ttu-id="e8351-275">Vorschau 2</span><span class="sxs-lookup"><span data-stu-id="e8351-275">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="e8351-276">Verwenden eines C#-Attributs zum Angeben eines Eigenschaftsunterstützungsfelds</span><span class="sxs-lookup"><span data-stu-id="e8351-276">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="e8351-277">Es kann jetzt ein C#-Attribut verwendet werden, um das Unterstützungsfeld für eine Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="e8351-277">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="e8351-278">Dieses Attribut ermöglicht es EF Core, wie sonst auch in das Unterstützungsfeld zu schreiben und daraus zu lesen, auch wenn das Unterstützungsfeld nicht automatisch gefunden werden kann.</span><span class="sxs-lookup"><span data-stu-id="e8351-278">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="e8351-279">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-279">For example:</span></span>

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

<span data-ttu-id="e8351-280">Die Dokumentation finden Sie im Issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span><span class="sxs-lookup"><span data-stu-id="e8351-280">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="e8351-281">Vollständige Diskriminatorzuordnung</span><span class="sxs-lookup"><span data-stu-id="e8351-281">Complete discriminator mapping</span></span>

<span data-ttu-id="e8351-282">EF Core verwendet eine Diskriminatorspalte für die [TPH-Zuordnung (Tabelle pro Hierarchie) von Vererbungshierarchien](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="e8351-282">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span> <span data-ttu-id="e8351-283">Es sind einige Leistungsverbesserungen möglich, solange EF Core alle möglichen Werte für den Diskriminator kennt.</span><span class="sxs-lookup"><span data-stu-id="e8351-283">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="e8351-284">Diese Verbesserungen sind nun in EF Core 5.0 implementiert.</span><span class="sxs-lookup"><span data-stu-id="e8351-284">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="e8351-285">Frühere Versionen von EF Core generierten beispielsweise für Abfragen immer einen SQL-Code wie den folgenden, in dem alle Typen in einer Hierarchie zurückgegeben wurden:</span><span class="sxs-lookup"><span data-stu-id="e8351-285">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="e8351-286">EF Core 5.0 generiert nun Folgendes, wenn eine vollständige Diskriminatorzuordnung konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="e8351-286">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="e8351-287">Ab Vorschauversion 3 wird dies das Standardverhalten.</span><span class="sxs-lookup"><span data-stu-id="e8351-287">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="e8351-288">Leistungsverbesserungen in Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="e8351-288">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="e8351-289">Es wurden zwei Leistungsverbesserungen für SQLite vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="e8351-289">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="e8351-290">Das Abrufen von Binär- und Zeichenfolgendaten mit GetBytes, GetChars und GetTextReader ist nun effizienter durch die Verwendung von SqliteBlob und Datenströmen.</span><span class="sxs-lookup"><span data-stu-id="e8351-290">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="e8351-291">Die Initialisierung von SqliteConnection ist nun verzögert.</span><span class="sxs-lookup"><span data-stu-id="e8351-291">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="e8351-292">Diese Verbesserungen wurden am ADO.NET-Anbieter Microsoft.Data.Sqlite vorgenommen und führen daher auch zu Leistungsverbesserungen außerhalb von EF Core.</span><span class="sxs-lookup"><span data-stu-id="e8351-292">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="e8351-293">Vorschauversion 1</span><span class="sxs-lookup"><span data-stu-id="e8351-293">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="e8351-294">Einfache Protokollierung</span><span class="sxs-lookup"><span data-stu-id="e8351-294">Simple logging</span></span>

<span data-ttu-id="e8351-295">Dieses Feature fügt eine Funktion hinzu, die `Database.Log` in EF6 ähnelt.</span><span class="sxs-lookup"><span data-stu-id="e8351-295">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="e8351-296">Es bietet eine einfache Möglichkeit zum Abrufen von Protokollen aus EF Core, ohne ein externes Protokollierungsframework konfigurieren zu müssen.</span><span class="sxs-lookup"><span data-stu-id="e8351-296">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="e8351-297">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 5. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="e8351-297">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="e8351-298">Zusätzliche Dokumentation finden Sie im Issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span><span class="sxs-lookup"><span data-stu-id="e8351-298">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="e8351-299">Einfache Möglichkeit zum Abrufen von generiertem SQL-Code</span><span class="sxs-lookup"><span data-stu-id="e8351-299">Simple way to get generated SQL</span></span>

<span data-ttu-id="e8351-300">In EF Core 5.0 wird die Erweiterungsmethode `ToQueryString` eingeführt, die den von EF Core beim Ausführen einer LINQ-Abfrage generierten SQL-Code zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="e8351-300">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="e8351-301">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 9. Januar 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="e8351-301">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="e8351-302">Zusätzliche Dokumentation finden Sie im Issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span><span class="sxs-lookup"><span data-stu-id="e8351-302">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="e8351-303">Verwenden eines C#-Attributs zum Angeben des Fehlens eines Schlüssels einer Entität</span><span class="sxs-lookup"><span data-stu-id="e8351-303">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="e8351-304">Ein Entitätstyp kann nun mithilfe des neuen Attributs `KeylessAttribute` als schlüssellos konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-304">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="e8351-305">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-305">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="e8351-306">Die Dokumentation finden Sie im Issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span><span class="sxs-lookup"><span data-stu-id="e8351-306">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="e8351-307">Ändern von Verbindung oder Verbindungszeichenfolge in initialisiertem DbContext möglich</span><span class="sxs-lookup"><span data-stu-id="e8351-307">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="e8351-308">Es ist jetzt einfacher, eine DbContext-Instanz ohne Verbindung oder Verbindungszeichenfolge zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="e8351-308">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="e8351-309">Die Verbindung oder Verbindungszeichenfolge kann jetzt auch in der Kontextinstanz verändert werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-309">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="e8351-310">Mithilfe dieses Features kann ein und dieselbe Kontextinstanz dynamisch eine Verbindung mit verschiedenen Datenbanken herstellen.</span><span class="sxs-lookup"><span data-stu-id="e8351-310">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="e8351-311">Die Dokumentation finden Sie im Issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span><span class="sxs-lookup"><span data-stu-id="e8351-311">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="e8351-312">Proxys zur Änderungsnachverfolgung</span><span class="sxs-lookup"><span data-stu-id="e8351-312">Change-tracking proxies</span></span>

<span data-ttu-id="e8351-313">EF Core kann jetzt Runtimeproxys generieren, die automatisch [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) und [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) implementieren.</span><span class="sxs-lookup"><span data-stu-id="e8351-313">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span> <span data-ttu-id="e8351-314">Diese melden dann Wertänderungen bei Entitätseigenschaften direkt an EF Core, sodass die Notwendigkeit einer Überprüfung auf Änderungen entfällt.</span><span class="sxs-lookup"><span data-stu-id="e8351-314">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="e8351-315">Für Proxys gelten jedoch bestimmte Einschränkungen, daher eignen sie sich nicht in jedem Fall.</span><span class="sxs-lookup"><span data-stu-id="e8351-315">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="e8351-316">Die Dokumentation finden Sie im Issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span><span class="sxs-lookup"><span data-stu-id="e8351-316">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="e8351-317">Erweiterte Debugansichten</span><span class="sxs-lookup"><span data-stu-id="e8351-317">Enhanced debug views</span></span>

<span data-ttu-id="e8351-318">Debugansichten sind eine einfache Möglichkeit, beim Debuggen von Problemen interne Vorgänge in EF Core zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="e8351-318">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="e8351-319">Ein Debugansicht für das Modell wurde bereits vor einiger Zeit implementiert.</span><span class="sxs-lookup"><span data-stu-id="e8351-319">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="e8351-320">Für EF Core 5.0 haben wir die Modellansicht vereinfacht und im Zustands-Manager eine neue Debugansicht für nachverfolgte Entitäten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e8351-320">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="e8351-321">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 12. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="e8351-321">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="e8351-322">Zusätzliche Dokumentation finden Sie im Issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span><span class="sxs-lookup"><span data-stu-id="e8351-322">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="e8351-323">Verbesserte Verarbeitung von NULL-Semantik in der Datenbank</span><span class="sxs-lookup"><span data-stu-id="e8351-323">Improved handling of database null semantics</span></span>

<span data-ttu-id="e8351-324">Relationale Datenbanken behandeln NULL in der Regel als unbekannten Wert und daher als ungleich jedem anderen NULL-Wert.</span><span class="sxs-lookup"><span data-stu-id="e8351-324">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="e8351-325">C# hingegen behandelt NULL als definierten Wert, der mit jedem anderen NULL-Wert übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="e8351-325">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="e8351-326">EF Core übersetzt Abfragen standardmäßig so, dass sie die NULL-Semantik von C# verwenden.</span><span class="sxs-lookup"><span data-stu-id="e8351-326">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="e8351-327">In EF Core 5.0 wurde die Effizienz dieser Übersetzungen erheblich verbessert.</span><span class="sxs-lookup"><span data-stu-id="e8351-327">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="e8351-328">Die Dokumentation finden Sie im Issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span><span class="sxs-lookup"><span data-stu-id="e8351-328">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="e8351-329">Indexereigenschaften</span><span class="sxs-lookup"><span data-stu-id="e8351-329">Indexer properties</span></span>

<span data-ttu-id="e8351-330">EF Core 5.0 unterstützt die Zuordnung von C#-Indexereigenschaften.</span><span class="sxs-lookup"><span data-stu-id="e8351-330">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="e8351-331">Durch diese Eigenschaften können Entitäten als Eigenschaftenbehälter fungieren, wobei Spalten benannten Eigenschaften im Behälter zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="e8351-331">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="e8351-332">Die Dokumentation finden Sie im Issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span><span class="sxs-lookup"><span data-stu-id="e8351-332">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="e8351-333">Generieren von CHECK-Einschränkungen für Enumerationszuordnungen</span><span class="sxs-lookup"><span data-stu-id="e8351-333">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="e8351-334">EF Core 5.0-Migrationen können jetzt CHECK-Einschränkungen aus Zuordnungen der enum-Eigenschaft generieren.</span><span class="sxs-lookup"><span data-stu-id="e8351-334">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="e8351-335">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-335">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="e8351-336">Die Dokumentation finden Sie im Issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span><span class="sxs-lookup"><span data-stu-id="e8351-336">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="e8351-337">IsRelational</span><span class="sxs-lookup"><span data-stu-id="e8351-337">IsRelational</span></span>

<span data-ttu-id="e8351-338">Zusätzlich zu den existierenden `IsSqlServer`-, `IsSqlite`- und `IsInMemory`-Methoden wurde eine neue `IsRelational`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e8351-338">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="e8351-339">Diese Methode kann verwendet werden, um zu testen, ob DbContext einen Anbieter einer relationalen Datenbank verwendet.</span><span class="sxs-lookup"><span data-stu-id="e8351-339">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="e8351-340">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-340">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="e8351-341">Die Dokumentation finden Sie im Issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span><span class="sxs-lookup"><span data-stu-id="e8351-341">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="e8351-342">Optimistische Nebenläufigkeit in Cosmos mit Entitätstags (ETags)</span><span class="sxs-lookup"><span data-stu-id="e8351-342">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="e8351-343">Der Azure Cosmos DB-Datenbankanbieter unterstützt nun optimistische Nebenläufigkeit mithilfe von ETags.</span><span class="sxs-lookup"><span data-stu-id="e8351-343">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="e8351-344">Verwenden Sie das Modellerstellungstool in OnModelCreating, um ein ETag zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="e8351-344">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="e8351-345">SaveChanges löst dann eine `DbUpdateConcurrencyException`-Ausnahme für einen Nebenläufigkeitskonflikt aus, die [verarbeitet werden kann](https://docs.microsoft.com/ef/core/saving/concurrency), z. B. um Wiederholungsversuche zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="e8351-345">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="e8351-346">Die Dokumentation finden Sie im Issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span><span class="sxs-lookup"><span data-stu-id="e8351-346">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="e8351-347">Abfrageübersetzungen für weitere DateTime-Konstrukte</span><span class="sxs-lookup"><span data-stu-id="e8351-347">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="e8351-348">Abfragen mit neuer DateTime-Konstruktion werden jetzt übersetzt.</span><span class="sxs-lookup"><span data-stu-id="e8351-348">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="e8351-349">Außerdem werden nun die folgenden SQL Server-Funktionen zugeordnet:</span><span class="sxs-lookup"><span data-stu-id="e8351-349">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="e8351-350">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="e8351-350">DateDiffWeek</span></span>
* <span data-ttu-id="e8351-351">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="e8351-351">DateFromParts</span></span>

<span data-ttu-id="e8351-352">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-352">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="e8351-353">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="e8351-353">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="e8351-354">Abfrageübersetzungen für weitere Bytearraykonstrukte</span><span class="sxs-lookup"><span data-stu-id="e8351-354">Query translations for more byte array constructs</span></span>

<span data-ttu-id="e8351-355">Abfragen, die Contains, Length, SequenceEqual usw. in byte[]-Eigenschaften verwenden, werden jetzt in SQL übersetzt.</span><span class="sxs-lookup"><span data-stu-id="e8351-355">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="e8351-356">Eine vorläufige Dokumentation ist im [Wöchentlichen Status von EF vom 5. Dezember 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863) (in englischer Sprache) enthalten.</span><span class="sxs-lookup"><span data-stu-id="e8351-356">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="e8351-357">Zusätzliche Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="e8351-357">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="e8351-358">Abfrageübersetzung für Reverse</span><span class="sxs-lookup"><span data-stu-id="e8351-358">Query translation for Reverse</span></span>

<span data-ttu-id="e8351-359">Abfragen, die `Reverse` verwenden, werden jetzt übersetzt.</span><span class="sxs-lookup"><span data-stu-id="e8351-359">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="e8351-360">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-360">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="e8351-361">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="e8351-361">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="e8351-362">Abfrageübersetzung für bitweise Operatoren</span><span class="sxs-lookup"><span data-stu-id="e8351-362">Query translation for bitwise operators</span></span>

<span data-ttu-id="e8351-363">Abfragen, die bitweise Operatoren verwenden, werden jetzt in mehr Fällen übersetzt. Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e8351-363">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="e8351-364">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="e8351-364">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="e8351-365">Abfrageübersetzung für Zeichenfolgen in Cosmos</span><span class="sxs-lookup"><span data-stu-id="e8351-365">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="e8351-366">Abfragen, die die Zeichenfolgenmethoden Contains, StartsWith und EndsWith verwenden, werden jetzt übersetzt, wenn der Azure Cosmos DB-Anbieter verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e8351-366">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="e8351-367">Die Dokumentation finden Sie im Issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span><span class="sxs-lookup"><span data-stu-id="e8351-367">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
