---
title: Neue Funktionen in Entity Framework Core 3.0
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/index
ms.openlocfilehash: ab61716fbf9cd4a256903a99540b6ecea2b5d37c
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526835"
---
# <a name="new-features-in-entity-framework-core-30"></a><span data-ttu-id="4282b-102">Neue Funktionen in Entity Framework Core 3.0</span><span class="sxs-lookup"><span data-stu-id="4282b-102">New features in Entity Framework Core 3.0</span></span>

<span data-ttu-id="4282b-103">Die folgende Liste enthält die wichtigsten neuen Features in EF Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="4282b-103">The following list includes the major new features in EF Core 3.0.</span></span>

<span data-ttu-id="4282b-104">EF Core 3.0 ist eine Hauptversion und enthält damit zahlreiche [Breaking Changes](xref:core/what-is-new/ef-core-3.0/breaking-changes). Hierbei handelt es sich um Verbesserungen der API, die einen negativen Einfluss auf vorhandene Anwendungen haben können.</span><span class="sxs-lookup"><span data-stu-id="4282b-104">As a major release, EF Core 3.0 also contains several [breaking changes](xref:core/what-is-new/ef-core-3.0/breaking-changes), which are API improvements that may have negative impact on existing applications.</span></span>  

## <a name="linq-overhaul"></a><span data-ttu-id="4282b-105">LINQ-Überarbeitung</span><span class="sxs-lookup"><span data-stu-id="4282b-105">LINQ overhaul</span></span>

<span data-ttu-id="4282b-106">Mit LINQ können Sie Datenbankabfragen in einer beliebigen .NET-Sprache schreiben. Dabei werden Rich-Type-Informationen genutzt, um IntelliSense und Typprüfung zur Kompilierungszeit bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="4282b-106">LINQ enables you to write database queries using your .NET language of choice, taking advantage of rich type information to offer IntelliSense and compile-time type checking.</span></span>
<span data-ttu-id="4282b-107">LINQ ermöglicht es Ihnen darüber hinaus, eine unbegrenzte Anzahl komplizierter Abfragen zu schreiben, die beliebige Ausdrücke enthalten (Methodenaufrufe oder Operationen).</span><span class="sxs-lookup"><span data-stu-id="4282b-107">But LINQ also enables you to write an unlimited number of complicated queries containing arbitrary expressions (method calls or operations).</span></span>
<span data-ttu-id="4282b-108">Die Behandlung dieser Kombinationen ist die größte Herausforderung für LINQ-Anbieter.</span><span class="sxs-lookup"><span data-stu-id="4282b-108">How to handle all those combinations is the main challenge for LINQ providers.</span></span>

<span data-ttu-id="4282b-109">In EF Core 3.0 wurde der LINQ-Anbieter neu strukturiert, damit mehr Abfragemuster in SQL übersetzt werden können, in mehr Fällen effiziente Abfragen erzeugt werden können und ineffiziente Abfragen erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="4282b-109">In EF Core 3.0, we rearchitected our LINQ provider to enable translating more query patterns into SQL, generating efficient queries in more cases, and preventing inefficient queries from going undetected.</span></span> <span data-ttu-id="4282b-110">Der neue LINQ-Anbieter ist die Grundlage, dank derer wir in zukünftigen Versionen neue Abfragefunktionen und Leistungsverbesserungen anbieten können, ohne die Funktionsfähigkeit vorhandener Anwendungen und Datenanbieter zu beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="4282b-110">The new LINQ provider is the foundation over which we'll be able to offer new query capabilities and performance improvements in future releases, without breaking existing applications and data providers.</span></span>

### <a name="restricted-client-evaluation"></a><span data-ttu-id="4282b-111">Eingeschränkte Clientauswertung</span><span class="sxs-lookup"><span data-stu-id="4282b-111">Restricted client evaluation</span></span>

<span data-ttu-id="4282b-112">Die wichtigste Änderung hat mit der Behandlung von LINQ-Ausdrücken zu tun, die nicht in Parameter konvertiert oder in SQL übersetzt werden können.</span><span class="sxs-lookup"><span data-stu-id="4282b-112">The most important design change has to do with how we handle LINQ expressions that cannot be converted to parameters or translated to SQL.</span></span>

<span data-ttu-id="4282b-113">In vorherigen Versionen ermittelte EF Core einfach, welche Teile einer Abfrage in SQL übersetzt werden konnten, und führte den Rest der Abfrage auf dem Client aus.</span><span class="sxs-lookup"><span data-stu-id="4282b-113">In previous versions, EF Core identified what portions of a query could be translated to SQL, and executed the rest of the query on the client.</span></span>
<span data-ttu-id="4282b-114">Diese Art von clientseitiger Ausführung kann in einigen Situationen wünschenswert sein, aber in vielen anderen Fällen kann sie zu ineffizienten Abfragen führen.</span><span class="sxs-lookup"><span data-stu-id="4282b-114">This type of client-side execution is desirable in some situations, but in many other cases it can result in inefficient queries.</span></span>

<span data-ttu-id="4282b-115">Wenn beispielsweise EF Core 2.2 ein Prädikat in einem `Where()`-Aufruf nicht übersetzen konnte, wurde eine SQL-Anweisung ohne Filter ausgeführt, alle Zeilen aus der Datenbank wurden übertragen und dann im Arbeitsspeicher gefiltert:</span><span class="sxs-lookup"><span data-stu-id="4282b-115">For example, if EF Core 2.2 couldn't translate a predicate in a `Where()` call, it executed an SQL statement without a filter, transferred all the rows from the database, and then filtered them in-memory:</span></span>

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n) && IsSpecialCustomer(c));
```

<span data-ttu-id="4282b-116">Dies kann akzeptabel sein, wenn die Datenbank nur wenige Zeilen enthält, kann aber auch zu erheblichen Leistungsproblemen oder sogar dem Ausfall der Anwendung führen, wenn eine Datenbank eine große Anzahl von Zeilen enthält.</span><span class="sxs-lookup"><span data-stu-id="4282b-116">That may be acceptable if the database contains a small number of rows but can result in significant performance issues or even application failure if the database contains a large number of rows.</span></span>

<span data-ttu-id="4282b-117">In EF Core 3.0 haben wir die Clientauswertung auf die Projektion der obersten Ebene (den letzten Aufruf von `Select()`) eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="4282b-117">In EF Core 3.0, we've restricted client evaluation to only happen on the top-level projection (essentially, the last call to `Select()`).</span></span>
<span data-ttu-id="4282b-118">Wenn EF Core 3.0 an irgendeiner anderen Stelle in der Abfrage Ausdrücke erkennt, die nicht übersetzt werden können, wird eine Laufzeitausnahme ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="4282b-118">When EF Core 3.0 detects expressions that can't be translated anywhere else in the query, it throws a runtime exception.</span></span>

<span data-ttu-id="4282b-119">Um eine Prädikatbedingung auf dem Client wie im vorherigen Beispiel auszuwerten, müssen Entwickler jetzt die Auswertung der Abfrage explizit auf LINQ to Objects umstellen:</span><span class="sxs-lookup"><span data-stu-id="4282b-119">To evaluate a predicate condition on the client as in the previous example, developers now need to explicitly switch evaluation of the query to LINQ to Objects:</span></span>

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n))
    .AsEnumerable() // switches to LINQ to Objects
    .Where(c => IsSpecialCustomer(c));
```

<span data-ttu-id="4282b-120">Weitere Informationen zu den Auswirkungen auf vorhandene Anwendungen finden Sie in der [Dokumentation zu Breaking Changes](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client).</span><span class="sxs-lookup"><span data-stu-id="4282b-120">See the [breaking changes documentation](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client) for more details about how this can affect existing applications.</span></span>

### <a name="single-sql-statement-per-linq-query"></a><span data-ttu-id="4282b-121">Einzelne SQL-Anweisung pro LINQ-Abfrage</span><span class="sxs-lookup"><span data-stu-id="4282b-121">Single SQL statement per LINQ query</span></span>

<span data-ttu-id="4282b-122">Ein weiterer Aspekt des geänderten Designs in Version 3.0 ist die Tatsache, dass jetzt immer eine einzelne SQL-Anweisung pro LINQ-Abfrage generiert wird.</span><span class="sxs-lookup"><span data-stu-id="4282b-122">Another aspect of the design that changed significantly in 3.0 is that we now always generate a single SQL statement per LINQ query.</span></span> <span data-ttu-id="4282b-123">In früheren Versionen wurden in bestimmten Fällen mehrere SQL-Anweisungen generiert, z. B. um `Include()`-Aufrufe in Auflistungs-Navigationseigenschaften oder Abfragen zu übersetzen, die bestimmten Mustern mit Unterabfragen entsprachen.</span><span class="sxs-lookup"><span data-stu-id="4282b-123">In previous versions, we used to generate multiple SQL statements in certain cases, translated `Include()` calls on collection navigation properties and translated queries that followed certain patterns with subqueries.</span></span> <span data-ttu-id="4282b-124">Obwohl dies in einigen Fällen praktisch war und für `Include()` sogar verhinderte, dass redundante Daten über das Netzwerk gesendet wurden, war die Implementierung komplex und führte zu einigen äußerst ineffizienten Verhaltensweisen (N+1-Abfragen).</span><span class="sxs-lookup"><span data-stu-id="4282b-124">Although this was in some cases convenient, and for `Include()` it even helped avoid sending redundant data over the wire, the implementation was complex, and it resulted in some extremely inefficient behaviors (N+1 queries).</span></span> <span data-ttu-id="4282b-125">In einigen Situationen waren die Daten, die über mehrere Abfragen zurückgegeben werden, inkonsistent.</span><span class="sxs-lookup"><span data-stu-id="4282b-125">There were situations in which the data returned across multiple queries was potentially inconsistent.</span></span>

<span data-ttu-id="4282b-126">Ähnlich wie bei der Clientauswertung wird in EF Core 3.0 eine Laufzeitausnahme ausgelöst, wenn eine LINQ-Abfrage nicht in eine einzelne SQL-Anweisung übersetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="4282b-126">Similarly to client evaluation, if EF Core 3.0 can't translate a LINQ query into a single SQL statement, it throws a runtime exception.</span></span> <span data-ttu-id="4282b-127">Mit EF Core können aber einige der gängigen Muster, mit denen mehrere Abfragen generiert wurden, in eine einzelne Abfrage mit JOINs übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="4282b-127">But we made EF Core capable of translating many of the common patterns that used to generate multiple queries to a single query with JOINs.</span></span>

## <a name="cosmos-db-support"></a><span data-ttu-id="4282b-128">Cosmos DB-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="4282b-128">Cosmos DB support</span></span>

<span data-ttu-id="4282b-129">Der Cosmos DB-Anbieter für EF Core ermöglicht es Entwicklern, die mit dem EF-Programmiermodell vertraut sind, Azure Cosmos DB problemlos als Anwendungsdatenbank-Ziel einzusetzen.</span><span class="sxs-lookup"><span data-stu-id="4282b-129">The Cosmos DB provider for EF Core enables developers familiar with the EF programing model to easily target Azure Cosmos DB as an application database.</span></span> <span data-ttu-id="4282b-130">Ziel ist es, einige der Vorteile von Cosmos DB – beispielsweise die globale Verteilung, Always On-Verfügbarkeit, elastische Skalierbarkeit und geringe Latenz – noch besser für .NET-Entwickler zugänglich zu machen.</span><span class="sxs-lookup"><span data-stu-id="4282b-130">The goal is to make some of the advantages of Cosmos DB, like global distribution, "always on" availability, elastic scalability, and low latency, even more accessible to .NET developers.</span></span> <span data-ttu-id="4282b-131">Der Anbieter unterstützt die meisten EF Core-Features, darunter die automatische Änderungsnachverfolgung, LINQ und Wertekonvertierungen anhand der SQL-API in Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="4282b-131">The provider enables most EF Core features, like automatic change tracking, LINQ, and value conversions, against the SQL API in Cosmos DB.</span></span>

<span data-ttu-id="4282b-132">Weitere Informationen finden Sie in der [Dokumentation zum Cosmos DB-Anbieter](xref:core/providers/cosmos/index).</span><span class="sxs-lookup"><span data-stu-id="4282b-132">See the [Cosmos DB provider documentation](xref:core/providers/cosmos/index) for more details.</span></span>

## <a name="c-80-support"></a><span data-ttu-id="4282b-133">C# 8.0-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="4282b-133">C# 8.0 support</span></span>

<span data-ttu-id="4282b-134">EF Core 3.0 nutzt einige der [neuen Funktionen in C# 8.0](/dotnet/csharp/whats-new/csharp-8):</span><span class="sxs-lookup"><span data-stu-id="4282b-134">EF Core 3.0 takes advantage of a couple of the [new features in C# 8.0](/dotnet/csharp/whats-new/csharp-8):</span></span>

### <a name="asynchronous-streams"></a><span data-ttu-id="4282b-135">Asynchrone Streams</span><span class="sxs-lookup"><span data-stu-id="4282b-135">Asynchronous streams</span></span>

<span data-ttu-id="4282b-136">Asynchrone Abfrageergebnisse werden jetzt mithilfe der neuen `IAsyncEnumerable<T>`-Standardschnittelle verfügbar gemacht und können mithilfe von `await foreach` konsumiert werden.</span><span class="sxs-lookup"><span data-stu-id="4282b-136">Asynchronous query results are now exposed using the new standard `IAsyncEnumerable<T>` interface and can be consumed using `await foreach`.</span></span>

``` csharp
var orders =
    from o in context.Orders
    where o.Status == OrderStatus.Pending
    select o;

await foreach(var o in orders.AsAsyncEnumerable())
{
    Process(o);
}
```

<span data-ttu-id="4282b-137">Weitere Informationen finden Sie in der [C#-Dokumentation zu asynchronen Streams](/dotnet/csharp/whats-new/csharp-8#asynchronous-streams).</span><span class="sxs-lookup"><span data-stu-id="4282b-137">See the [asynchronous streams in the C# documentation](/dotnet/csharp/whats-new/csharp-8#asynchronous-streams) for more details.</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="4282b-138">Nullwerte zulassende Verweistypen</span><span class="sxs-lookup"><span data-stu-id="4282b-138">Nullable reference types</span></span>

<span data-ttu-id="4282b-139">Wenn diese neue Funktion in Ihrem Code aktiviert ist, prüft EF Core die NULL-Zulässigkeit von Verweistypeigenschaften und wendet sie auf die entsprechenden Spalten und Beziehungen in der Datenbank an: Eigenschaften von Verweistypen, die keine NULL-Werte zulassen, werden so behandelt, als wiesen sie das Datenanmerkungsattribut `[Required]` auf.</span><span class="sxs-lookup"><span data-stu-id="4282b-139">When this new feature is enabled in your code, EF Core examines the nullability of reference type properties and applies it to corresponding columns and relationships in the database: properties of non-nullable references types are treated as if they had the `[Required]` data annotation attribute.</span></span>

<span data-ttu-id="4282b-140">In der folgenden Klasse werden z. B. Eigenschaften mit dem Typ `string?` als optional konfiguriert, während `string` als erforderlich konfiguriert wird:</span><span class="sxs-lookup"><span data-stu-id="4282b-140">For example, in the following class, properties marked as of type `string?` will be configured as optional, whereas `string` will be configured as required:</span></span>

``` csharp
public class Customer
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string? MiddleName { get; set; }
}
```

<span data-ttu-id="4282b-141">Weitere Informationen finden Sie in der [EF Core-Dokumentation zum Arbeiten mit Verweistypen, die NULL-Werte zulassen](xref:core/miscellaneous/nullable-reference-types).</span><span class="sxs-lookup"><span data-stu-id="4282b-141">See [Working with nullable reference types](xref:core/miscellaneous/nullable-reference-types) in the EF Core documentation for more details.</span></span>

## <a name="interception-of-database-operations"></a><span data-ttu-id="4282b-142">Abfangen von Datenbankvorgängen</span><span class="sxs-lookup"><span data-stu-id="4282b-142">Interception of database operations</span></span>

<span data-ttu-id="4282b-143">Durch die neue Abfang-API in EF Core 3.0 kann benutzerdefinierte Logik automatisch aufgerufen werden, wenn Datenbankvorgänge auf niedriger Ebene im Rahmen des normalen Betriebs von EF Core auftreten.</span><span class="sxs-lookup"><span data-stu-id="4282b-143">The new interception API in EF Core 3.0 allows providing custom logic to be invoked automatically whenever low-level database operations occur as part of the normal operation of EF Core.</span></span> <span data-ttu-id="4282b-144">Hierzu zählen z. B. das Öffnen von Verbindungen, Committen von Transaktionen oder Ausführen von Befehlen.</span><span class="sxs-lookup"><span data-stu-id="4282b-144">For example, when opening connections, committing transactions, or executing commands.</span></span>

<span data-ttu-id="4282b-145">Ähnlich wie bei den Abfangfunktionen in EF 6 können Sie mit den Abfangfunktionen Vorgänge vor oder nach dem Auftreten abfangen.</span><span class="sxs-lookup"><span data-stu-id="4282b-145">Similarly to the interception features that existed in EF 6, interceptors allow you to intercept operations before or after they happen.</span></span> <span data-ttu-id="4282b-146">Wenn Sie sie vorher abfangen, können Sie die Ausführung umgehen und alternative Ergebnisse aus der Abfanglogik angeben.</span><span class="sxs-lookup"><span data-stu-id="4282b-146">When you intercept them before they happen, you are allowed to by-pass execution and supply alternate results from the interception logic.</span></span>

<span data-ttu-id="4282b-147">Zum Bearbeiten von Befehlstext können Sie beispielsweise `DbCommandInterceptor` erstellen:</span><span class="sxs-lookup"><span data-stu-id="4282b-147">For example, to manipulate command text, you can create a `DbCommandInterceptor`:</span></span>

``` csharp
public class HintCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult<DbDataReader> ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result)
    {
        // Manipulate the command text, etc. here...
        command.CommandText += " OPTION (OPTIMIZE FOR UNKNOWN)";
        return result;
    }
}
```

<span data-ttu-id="4282b-148">Und bei  `DbContext` registrieren:</span><span class="sxs-lookup"><span data-stu-id="4282b-148">And register it with your `DbContext`:</span></span>

``` csharp
services.AddDbContext(b => b
    .UseSqlServer(connectionString)
    .AddInterceptors(new HintCommandInterceptor()));
```

## <a name="reverse-engineering-of-database-views"></a><span data-ttu-id="4282b-149">Reverse Engineering (Zurückentwicklung) von Datenbanksichten</span><span class="sxs-lookup"><span data-stu-id="4282b-149">Reverse engineering of database views</span></span>

<span data-ttu-id="4282b-150">Abfragetypen, die Daten darstellen, die aus der Datenbank gelesen, aber nicht aktualisiert werden können, wurden in [schlüssellose Entitätstypen](xref:core/modeling/keyless-entity-types) umbenannt.</span><span class="sxs-lookup"><span data-stu-id="4282b-150">Query types, which represent data that can be read from the database but not updated, have been renamed to [keyless entity types](xref:core/modeling/keyless-entity-types).</span></span>
<span data-ttu-id="4282b-151">Da sie in den meisten Szenarien ideal für die Zuordnung von Datenbankansichten sind, erstellt EF Core jetzt automatisch schlüssellose Entitätstypen beim Reverse Engineering von Datenbankansichten.</span><span class="sxs-lookup"><span data-stu-id="4282b-151">As they are an excellent fit for mapping database views in most scenarios, EF Core now automatically creates keyless entity types when reverse engineering database views.</span></span>

<span data-ttu-id="4282b-152">Mit dem [dotnet ef-Befehlszeilenprogramm](xref:core/miscellaneous/cli/dotnet) können Sie z. B. Folgendes eingeben:</span><span class="sxs-lookup"><span data-stu-id="4282b-152">For example, using the [dotnet ef command-line tool](xref:core/miscellaneous/cli/dotnet) you can type:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="4282b-153">Das Tool erstellt dann automatisch einen Gerüsttyp für Ansichten und Tabellen ohne Schlüssel:</span><span class="sxs-lookup"><span data-stu-id="4282b-153">And the tool will now automatically scaffold types for views and tables without keys:</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Names>(entity =>
    {
        entity.HasNoKey();
        entity.ToView("Names");
    });

    modelBuilder.Entity<Things>(entity =>
    {
        entity.HasNoKey();
    });
}
```

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="4282b-154">Abhängige Entitäten, die die Tabelle gemeinsam mit dem Prinzipal verwenden, sind jetzt optional</span><span class="sxs-lookup"><span data-stu-id="4282b-154">Dependent entities sharing the table with the principal are now optional</span></span>

<span data-ttu-id="4282b-155">Ab EF Core 3.0 gibt es die Möglichkeit, wenn `OrderDetails` im Besitz von `Order` ist oder explizit derselben Tabelle zugeordnet ist, dass `Order` ohne `OrderDetails` hinzugefügt werden kann, und alle `OrderDetails`-Eigenschaften, mit Ausnahme des primären Schlüssels, werden Spalten zugeordnet, die NULL-Werte zulassen.</span><span class="sxs-lookup"><span data-stu-id="4282b-155">Starting with EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table, it will be possible to add an `Order` without an `OrderDetails` and all of the `OrderDetails` properties, except the primary key will be mapped to nullable columns.</span></span>

<span data-ttu-id="4282b-156">Bei Abfragen von EF Core wird `OrderDetails` auf `null` festgelegt, wenn eine der erforderlichen Eigenschaften keinen Wert aufweist oder keine erforderlichen Eigenschaften außer dem primären Schlüssel vorhanden und alle Eigenschaften `null` sind.</span><span class="sxs-lookup"><span data-stu-id="4282b-156">When querying, EF Core will set `OrderDetails` to `null` if any of its required properties doesn't have a value, or if it has no required properties besides the primary key and all properties are `null`.</span></span>

``` csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

## <a name="ef-63-on-net-core"></a><span data-ttu-id="4282b-157">EF 6.3 unter .NET Core</span><span class="sxs-lookup"><span data-stu-id="4282b-157">EF 6.3 on .NET Core</span></span>

<span data-ttu-id="4282b-158">Dies ist nicht wirklich eine EF Core 3.0-Funktion, sie ist aber wichtig für viele unserer derzeitigen Kunden.</span><span class="sxs-lookup"><span data-stu-id="4282b-158">This isn't really an EF Core 3.0 feature, but we think it is important to many of our current customers.</span></span>

<span data-ttu-id="4282b-159">Uns ist bewusst, dass viele bestehende Anwendungen frühere Versionen von EF verwenden und dass die Portierung auf EF Core nur zur Nutzung von .NET Core unter Umständen mit einem erheblichen Aufwand verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="4282b-159">We understand that many existing applications use previous versions of EF, and that porting them to EF Core only to take advantage of .NET Core can require a significant effort.</span></span>
<span data-ttu-id="4282b-160">Aus diesem Grund haben wir die neueste Version von EF 6 portiert, sodass sie unter .NET Core 3.0 ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="4282b-160">For that reason, we decided to port the newest version of EF 6 to run on .NET Core 3.0.</span></span>

<span data-ttu-id="4282b-161">Weitere Informationen finden Sie im [Artikel zu Neuerungen in EF 6](xref:ef6/what-is-new/index).</span><span class="sxs-lookup"><span data-stu-id="4282b-161">For more details, see [what's new in EF 6](xref:ef6/what-is-new/index).</span></span>

## <a name="postponed-features"></a><span data-ttu-id="4282b-162">Verschobene Features</span><span class="sxs-lookup"><span data-stu-id="4282b-162">Postponed features</span></span>

<span data-ttu-id="4282b-163">Einige Features, die ursprünglich für EF Core 3.0 geplant waren, wurden auf zukünftige Versionen verschoben:</span><span class="sxs-lookup"><span data-stu-id="4282b-163">Some features originally planned for EF Core 3.0 were postponed to future releases:</span></span>

- <span data-ttu-id="4282b-164">Die Fähigkeit, Teile eines Modells in Migrationen zu ignorieren, nachverfolgt als [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).</span><span class="sxs-lookup"><span data-stu-id="4282b-164">Ability to ignore parts of a model in migrations, tracked as [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).</span></span>
- <span data-ttu-id="4282b-165">Entitäten von Eigenschaftenbehältern, die in zwei separaten Problemen nachverfolgt werden: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) über Entitäten von gemeinsam genutzten Typen und [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) über Unterstützung der Zuordnung von indizierten Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="4282b-165">Property bag entities, tracked as two separate issues: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) about shared-type entities and [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) about indexed property mapping support.</span></span>
