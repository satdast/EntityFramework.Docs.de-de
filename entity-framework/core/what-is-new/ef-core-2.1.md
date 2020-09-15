---
title: Neue Features in EF Core 2.1 – EF Core
description: Änderungen und Verbesserungen in Entity Framework Core 2.1
author: divega
ms.date: 02/20/2018
ms.assetid: 585F90A3-4D5A-4DD1-92D8-5243B14E0FEC
uid: core/what-is-new/ef-core-2.1
ms.openlocfilehash: 5aa8aa5d3578b9f69e63adaf731a1642f748c5c2
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618764"
---
# <a name="new-features-in-ef-core-21"></a><span data-ttu-id="d5d75-103">Neue Features in EF Core 2.1</span><span class="sxs-lookup"><span data-stu-id="d5d75-103">New features in EF Core 2.1</span></span>

<span data-ttu-id="d5d75-104">Neben zahlreichen Fehlerbehebungen und kleinen Funktions- und Leistungsverbesserungen umfasst EF Core 2.1 einige interessante neue Features:</span><span class="sxs-lookup"><span data-stu-id="d5d75-104">Besides numerous bug fixes and small functional and performance enhancements, EF Core 2.1 includes some compelling new features:</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="d5d75-105">Lazy Loading</span><span class="sxs-lookup"><span data-stu-id="d5d75-105">Lazy loading</span></span>

<span data-ttu-id="d5d75-106">EF Core enthält nun die erforderlichen Bausteine für Benutzer, die Entitätsklassen zum bedarfsgesteuerten Laden ihrer Navigationseigenschaften erstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="d5d75-106">EF Core now contains the necessary building blocks for anyone to author entity classes that can load their navigation properties on demand.</span></span> <span data-ttu-id="d5d75-107">Wir haben zudem das neue Paket „Microsoft.EntityFrameworkCore.Proxies“ erstellt, das mithilfe dieser Bausteine Proxyklassen zum Lazy Loading auf Grundlage minimal geänderter Entitätsklassen (z.B. Klassen mit virtuellen Navigationseigenschaften) erzeugt.</span><span class="sxs-lookup"><span data-stu-id="d5d75-107">We have also created a new package, Microsoft.EntityFrameworkCore.Proxies, that leverages those building blocks to produce lazy loading proxy classes based on minimally modified entity classes (for example, classes with virtual navigation properties).</span></span>

<span data-ttu-id="d5d75-108">Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zum Lazy Loading](xref:core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="d5d75-108">Read the [section on lazy loading](xref:core/querying/related-data#lazy-loading) for more information about this topic.</span></span>

## <a name="parameters-in-entity-constructors"></a><span data-ttu-id="d5d75-109">Parameter in Entitätskonstruktoren</span><span class="sxs-lookup"><span data-stu-id="d5d75-109">Parameters in entity constructors</span></span>

<span data-ttu-id="d5d75-110">Als einer der erforderlichen Bausteine zum Lazy Loading haben wir die Erstellung von Entitäten ermöglicht, die Parameter in ihren Konstruktoren akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="d5d75-110">As one of the required building blocks for lazy loading, we enabled the creation of entities that take parameters in their constructors.</span></span> <span data-ttu-id="d5d75-111">Sie können mithilfe von Parametern Eigenschaftswerte, Delegaten zum Lazy Loading und Dienste einfügen.</span><span class="sxs-lookup"><span data-stu-id="d5d75-111">You can use parameters to inject property values, lazy loading delegates, and services.</span></span>

<span data-ttu-id="d5d75-112">Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zum Entitätskonstruktor mit Parametern](xref:core/modeling/constructors).</span><span class="sxs-lookup"><span data-stu-id="d5d75-112">Read the [section on entity constructor with parameters](xref:core/modeling/constructors) for more information about this topic.</span></span>

## <a name="value-conversions"></a><span data-ttu-id="d5d75-113">Wertkonvertierungen</span><span class="sxs-lookup"><span data-stu-id="d5d75-113">Value conversions</span></span>

<span data-ttu-id="d5d75-114">Bislang konnte EF Core nur Eigenschaften von Typen zuordnen, die vom zugrunde liegenden Datenbankanbieter nativ unterstützt wurden.</span><span class="sxs-lookup"><span data-stu-id="d5d75-114">Until now, EF Core could only map properties of types natively supported by the underlying database provider.</span></span> <span data-ttu-id="d5d75-115">Werte wurden zwischen Spalten und Eigenschaften ohne jegliche Transformation hin und her kopiert.</span><span class="sxs-lookup"><span data-stu-id="d5d75-115">Values were copied back and forth between columns and properties without any transformation.</span></span> <span data-ttu-id="d5d75-116">Ab EF Core 2.1 können Wertkonvertierungen angewendet werden, um die aus Spalten abgerufenen Werte vor der Anwendung auf Eigenschaften zu transformieren (und umgekehrt).</span><span class="sxs-lookup"><span data-stu-id="d5d75-116">Starting with EF Core 2.1, value conversions can be applied to transform the values obtained from columns before they are applied to properties, and vice versa.</span></span> <span data-ttu-id="d5d75-117">Wir verfügen über eine Reihe von Konvertierungen, die der Konvention entsprechend bei Bedarf angewendet werden können, sowie über eine explizite Konfigurations-API, die die Registrierung von benutzerdefinierten Konvertierungen zwischen Spalten und Eigenschaften ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="d5d75-117">We have a number of conversions that can be applied by convention as necessary, as well as an explicit configuration API that allows registering custom conversions between columns and properties.</span></span> <span data-ttu-id="d5d75-118">Einige Anwendungsgebiete für dieses Feature sind z.B. Folgende:</span><span class="sxs-lookup"><span data-stu-id="d5d75-118">Some of the application of this feature are:</span></span>

- <span data-ttu-id="d5d75-119">Speichern von Enumerationen als Zeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="d5d75-119">Storing enums as strings</span></span>
- <span data-ttu-id="d5d75-120">Zuordnen von ganzen Zahlen ohne Vorzeichen mit SQL Server</span><span class="sxs-lookup"><span data-stu-id="d5d75-120">Mapping unsigned integers with SQL Server</span></span>
- <span data-ttu-id="d5d75-121">Automatische Ver- und Entschlüsselung von Eigenschaftswerten</span><span class="sxs-lookup"><span data-stu-id="d5d75-121">Automatic encryption and decryption of property values</span></span>

<span data-ttu-id="d5d75-122">Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zu Wertkonvertierungen](xref:core/modeling/value-conversions).</span><span class="sxs-lookup"><span data-stu-id="d5d75-122">Read the [section on value conversions](xref:core/modeling/value-conversions) for more information about this topic.</span></span>  

## <a name="linq-groupby-translation"></a><span data-ttu-id="d5d75-123">LINQ-GroupBy-Übersetzung</span><span class="sxs-lookup"><span data-stu-id="d5d75-123">LINQ GroupBy translation</span></span>

<span data-ttu-id="d5d75-124">Vor Version 2.1 wurde der LINQ-Operator „GroupBy“ in EF Core immer im Arbeitsspeicher ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="d5d75-124">Before version 2.1, in EF Core the GroupBy LINQ operator would always be evaluated in memory.</span></span> <span data-ttu-id="d5d75-125">Nun bieten wir in den meisten Fällen Unterstützung für dessen Übersetzung in die GROUP BY-SQL-Klausel.</span><span class="sxs-lookup"><span data-stu-id="d5d75-125">We now support translating it to the SQL GROUP BY clause in most common cases.</span></span>

<span data-ttu-id="d5d75-126">Dieses Beispiel zeigt eine Abfrage mit GroupBy zum Berechnen verschiedener Aggregatfunktionen:</span><span class="sxs-lookup"><span data-stu-id="d5d75-126">This example shows a query with GroupBy used to compute various aggregate functions:</span></span>

``` csharp
var query = context.Orders
    .GroupBy(o => new { o.CustomerId, o.EmployeeId })
    .Select(g => new
        {
          g.Key.CustomerId,
          g.Key.EmployeeId,
          Sum = g.Sum(o => o.Amount),
          Min = g.Min(o => o.Amount),
          Max = g.Max(o => o.Amount),
          Avg = g.Average(o => o.Amount)
        });
```

<span data-ttu-id="d5d75-127">Die entsprechende SQL-Übersetzung sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="d5d75-127">The corresponding SQL translation looks like this:</span></span>

``` SQL
SELECT [o].[CustomerId], [o].[EmployeeId],
    SUM([o].[Amount]), MIN([o].[Amount]), MAX([o].[Amount]), AVG([o].[Amount])
FROM [Orders] AS [o]
GROUP BY [o].[CustomerId], [o].[EmployeeId];
```

## <a name="data-seeding"></a><span data-ttu-id="d5d75-128">Datenseeding</span><span class="sxs-lookup"><span data-stu-id="d5d75-128">Data Seeding</span></span>

<span data-ttu-id="d5d75-129">Mit dem neuen Release ist es möglich, die ursprünglichen Daten zum Auffüllen einer Datenbank anzugeben.</span><span class="sxs-lookup"><span data-stu-id="d5d75-129">With the new release it will be possible to provide initial data to populate a database.</span></span> <span data-ttu-id="d5d75-130">Im Gegensatz zu EF 6 wird das Datenseeding mit einem Entitätstyp als Teil der Modellkonfiguration verknüpft.</span><span class="sxs-lookup"><span data-stu-id="d5d75-130">Unlike in EF6, seeding data is associated to an entity type as part of the model configuration.</span></span> <span data-ttu-id="d5d75-131">EF Core-Migrationen können dann automatisch berechnen, welche Einfüge-, Aktualisierungs- oder Löschvorgänge beim Durchführen eines Upgrades für die Datenbank auf eine neue Modellversion angewendet werden müssen.</span><span class="sxs-lookup"><span data-stu-id="d5d75-131">Then EF Core migrations can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

<span data-ttu-id="d5d75-132">Beispielsweise können Sie damit Seeddaten für ein Post-Element in `OnModelCreating` konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="d5d75-132">As an example, you can use this to configure seed data for a Post in `OnModelCreating`:</span></span>

``` csharp
modelBuilder.Entity<Post>().HasData(new Post{ Id = 1, Text = "Hello World!" });
```

<span data-ttu-id="d5d75-133">Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zum Datenseeding](xref:core/modeling/data-seeding).</span><span class="sxs-lookup"><span data-stu-id="d5d75-133">Read the [section on data seeding](xref:core/modeling/data-seeding) for more information about this topic.</span></span>  

## <a name="query-types"></a><span data-ttu-id="d5d75-134">Abfragetypen</span><span class="sxs-lookup"><span data-stu-id="d5d75-134">Query types</span></span>

<span data-ttu-id="d5d75-135">Ein EF Core-Modell kann nun Abfragetypen einschließen.</span><span class="sxs-lookup"><span data-stu-id="d5d75-135">An EF Core model can now include query types.</span></span> <span data-ttu-id="d5d75-136">Im Gegensatz zu Entitätstypen werden für Abfragetypen keine Schlüssel definiert, und sie können nicht eingefügt, gelöscht oder aktualisiert werden (d.h., sie sind schreibgeschützt). Sie können jedoch direkt von Abfragen zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="d5d75-136">Unlike entity types, query types do not have keys defined on them and cannot be inserted, deleted or updated (that is, they are read-only), but they can be returned directly by queries.</span></span> <span data-ttu-id="d5d75-137">Einige Verwendungsszenarien für Abfragetypen sind z.B. Folgende:</span><span class="sxs-lookup"><span data-stu-id="d5d75-137">Some of the usage scenarios for query types are:</span></span>

- <span data-ttu-id="d5d75-138">Zuordnen mit Ansichten ohne Primärschlüssel</span><span class="sxs-lookup"><span data-stu-id="d5d75-138">Mapping to views without primary keys</span></span>
- <span data-ttu-id="d5d75-139">Zuordnen mit Tabellen ohne Primärschlüssel</span><span class="sxs-lookup"><span data-stu-id="d5d75-139">Mapping to tables without primary keys</span></span>
- <span data-ttu-id="d5d75-140">Zuordnen mit im Modell definierten Abfragen</span><span class="sxs-lookup"><span data-stu-id="d5d75-140">Mapping to queries defined in the model</span></span>
- <span data-ttu-id="d5d75-141">Verwendung als Rückgabetyp für `FromSql()`-Abfragen</span><span class="sxs-lookup"><span data-stu-id="d5d75-141">Serving as the return type for `FromSql()` queries</span></span>

<span data-ttu-id="d5d75-142">Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zu Abfragetypen](xref:core/modeling/keyless-entity-types).</span><span class="sxs-lookup"><span data-stu-id="d5d75-142">Read the [section on query types](xref:core/modeling/keyless-entity-types) for more information about this topic.</span></span>

## <a name="include-for-derived-types"></a><span data-ttu-id="d5d75-143">Einschließen für abgeleitete Typen</span><span class="sxs-lookup"><span data-stu-id="d5d75-143">Include for derived types</span></span>

<span data-ttu-id="d5d75-144">Nun ist es möglich, beim Schreiben von Ausdrücken für die `Include`-Methode nur für abgeleitete Typen definierte Navigationseigenschaften anzugeben.</span><span class="sxs-lookup"><span data-stu-id="d5d75-144">It will be now possible to specify navigation properties only defined on derived types when writing expressions for the `Include` method.</span></span> <span data-ttu-id="d5d75-145">Für die stark typisierte `Include`-Version unterstützen wir entweder die Verwendung einer expliziten Umwandlung oder des `as`-Operators.</span><span class="sxs-lookup"><span data-stu-id="d5d75-145">For the strongly typed version of `Include`, we support using either an explicit cast or the `as` operator.</span></span> <span data-ttu-id="d5d75-146">Nun bieten wir auch Unterstützung für den Verweis der für abgeleitete Typen definierten Namen der Navigationseigenschaften in der Zeichenfolgenversion `Include`:</span><span class="sxs-lookup"><span data-stu-id="d5d75-146">We also now support referencing the names of navigation property defined on derived types in the string version of `Include`:</span></span>

``` csharp
var option1 = context.People.Include(p => ((Student)p).School);
var option2 = context.People.Include(p => (p as Student).School);
var option3 = context.People.Include("School");
```

<span data-ttu-id="d5d75-147">Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zum Einschließen mit abgeleiteten Typen](xref:core/querying/related-data#include-on-derived-types).</span><span class="sxs-lookup"><span data-stu-id="d5d75-147">Read the [section on Include with derived types](xref:core/querying/related-data#include-on-derived-types) for more information about this topic.</span></span>

## <a name="systemtransactions-support"></a><span data-ttu-id="d5d75-148">System.Transactions-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="d5d75-148">System.Transactions support</span></span>

<span data-ttu-id="d5d75-149">Wir haben die Möglichkeit zum Arbeiten mit System.Transactions-Features wie TransactionScope hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d5d75-149">We have added the ability to work with System.Transactions features such as TransactionScope.</span></span> <span data-ttu-id="d5d75-150">Dieses funktioniert in .NET Framework und .NET Core bei der Verwendung von Datenbankanbietern, die Unterstützung hierfür bieten.</span><span class="sxs-lookup"><span data-stu-id="d5d75-150">This will work on both .NET Framework and .NET Core when using database providers that support it.</span></span>

<span data-ttu-id="d5d75-151">Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zu System.Transactions](xref:core/saving/transactions#using-systemtransactions).</span><span class="sxs-lookup"><span data-stu-id="d5d75-151">Read the [section on System.Transactions](xref:core/saving/transactions#using-systemtransactions) for more information about this topic.</span></span>

## <a name="better-column-ordering-in-initial-migration"></a><span data-ttu-id="d5d75-152">Bessere Spaltensortierung bei der anfänglichen Migration</span><span class="sxs-lookup"><span data-stu-id="d5d75-152">Better column ordering in initial migration</span></span>

<span data-ttu-id="d5d75-153">Basierend auf dem Feedback von Kunden haben wir Migrationen dahingehend aktualisiert, dass Spalten für Tabellen zunächst in der gleichen Reihenfolge wie die Deklaration von Eigenschaften in Klassen generiert werden.</span><span class="sxs-lookup"><span data-stu-id="d5d75-153">Based on customer feedback, we have updated migrations to initially generate columns for tables in the same order as properties are declared in classes.</span></span> <span data-ttu-id="d5d75-154">Beachten Sie, dass EF Core nicht die Reihenfolge ändern kann, wenn nach der Erstellung der ersten Tabelle neue Member hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="d5d75-154">Note that EF Core cannot change order when new members are added after the initial table creation.</span></span>

## <a name="optimization-of-correlated-subqueries"></a><span data-ttu-id="d5d75-155">Optimierung von korrelierten Unterabfragen</span><span class="sxs-lookup"><span data-stu-id="d5d75-155">Optimization of correlated subqueries</span></span>

<span data-ttu-id="d5d75-156">Wir haben unsere Abfrageübersetzung dahingehend verbessert, dass die Ausführung von „N+1“-SQL-Abfragen in vielen gängigen Szenarien verhindert wird, in denen die Verwendung einer Navigationseigenschaft in der Projektion zum Verknüpfen von Daten aus der Stammabfrage mit Daten aus einer korrelierten Unterabfrage führt.</span><span class="sxs-lookup"><span data-stu-id="d5d75-156">We have improved our query translation to avoid executing "N + 1" SQL queries in many common scenarios in which the usage of a navigation property in the projection leads to joining data from the root query with data from a correlated subquery.</span></span> <span data-ttu-id="d5d75-157">Für die Optimierung ist das Puffern der Ergebnisse aus der Unterabfrage erforderlich. Zudem ist es erforderlich, die Abfrage zum Auswählen des neuen Verhaltens zu ändern.</span><span class="sxs-lookup"><span data-stu-id="d5d75-157">The optimization requires buffering the results from the subquery, and we require that you modify the query to opt-in the new behavior.</span></span>

<span data-ttu-id="d5d75-158">Die folgende Abfrage wird z.B. normalerweise in eine Abfrage für Kunden plus N separate Abfragen für Aufträge übersetzt („N“ steht hierbei für die Anzahl der zurückgegebenen Kunden):</span><span class="sxs-lookup"><span data-stu-id="d5d75-158">As an example, the following query normally gets translated into one query for Customers, plus N (where "N" is the number of customers returned) separate queries for Orders:</span></span>

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount));
```

<span data-ttu-id="d5d75-159">Durch Einschließen von `ToList()` an der richtigen Stelle geben Sie an, dass eine Pufferung für die Aufträge, die die Optimierung ermöglichen, angemessen ist:</span><span class="sxs-lookup"><span data-stu-id="d5d75-159">By including `ToList()` in the right place, you indicate that buffering is appropriate for the Orders, which enable the optimization:</span></span>

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount).ToList());
```

<span data-ttu-id="d5d75-160">Beachten Sie, dass diese Abfrage nur in zwei SQL-Abfragen übersetzt wird: eine für Kunden und die nächste für Aufträge.</span><span class="sxs-lookup"><span data-stu-id="d5d75-160">Note that this query will be translated to only two SQL queries: One for Customers and the next one for Orders.</span></span>

## <a name="owned-attribute"></a><span data-ttu-id="d5d75-161">Das [Owned]-Attribut</span><span class="sxs-lookup"><span data-stu-id="d5d75-161">[Owned] attribute</span></span>

<span data-ttu-id="d5d75-162">Nun können [Entitätstypen im Besitz](xref:core/modeling/owned-entities) konfiguriert werden, indem einfach der Typ mit `[Owned]` kommentiert und dann sichergestellt wird, dass die Besitzerentität zum Modell hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="d5d75-162">It is now possible to configure [owned entity types](xref:core/modeling/owned-entities) by simply annotating the type with `[Owned]` and then making sure the owner entity is added to the model:</span></span>

``` csharp
[Owned]
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}
```

## <a name="command-line-tool-dotnet-ef-included-in-net-core-sdk"></a><span data-ttu-id="d5d75-163">dotnet-ef-Befehle des Befehlszeilentools im .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="d5d75-163">Command-line tool dotnet-ef included in .NET Core SDK</span></span>

<span data-ttu-id="d5d75-164">Die _dotnet-ef_-Befehle sind nun im .NET Core SDK enthalten. Deshalb ist es nicht mehr erforderlich, „DotNetCliToolReference“ für Migrationen oder den DbContext-Gerüstbau aus einer vorhandenen Datenbank im Projekt zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d5d75-164">The _dotnet-ef_ commands are now part of the .NET Core SDK, therefore it will no longer be necessary to use DotNetCliToolReference in the project to be able to use migrations or to scaffold a DbContext from an existing database.</span></span>

<span data-ttu-id="d5d75-165">Weitere Details zum Aktivieren der Befehlszeilentools für verschiedene Versionen des .NET Core SDK und von EF Core finden Sie im Abschnitt zur [Installation der Tools](xref:core/miscellaneous/cli/dotnet#installing-the-tools).</span><span class="sxs-lookup"><span data-stu-id="d5d75-165">See the section on [installing the tools](xref:core/miscellaneous/cli/dotnet#installing-the-tools) for more details on how to enable command line tools for different versions of the .NET Core SDK and EF Core.</span></span>

## <a name="microsoftentityframeworkcoreabstractions-package"></a><span data-ttu-id="d5d75-166">Paket „Microsoft.EntityFrameworkCore.Abstractions“</span><span class="sxs-lookup"><span data-stu-id="d5d75-166">Microsoft.EntityFrameworkCore.Abstractions package</span></span>

<span data-ttu-id="d5d75-167">Das neue Paket enthält Attribute und Schnittstellen, mit denen Sie EF Core-Features in Ihren Projekten aktivieren können, ohne eine Abhängigkeit von EF Core insgesamt einzugehen.</span><span class="sxs-lookup"><span data-stu-id="d5d75-167">The new package contains attributes and interfaces that you can use in your projects to light up EF Core features without taking a dependency on EF Core as a whole.</span></span> <span data-ttu-id="d5d75-168">Das [Owned]-Attribut und die ILazyLoader-Schnittstelle sind beispielsweise enthalten.</span><span class="sxs-lookup"><span data-stu-id="d5d75-168">For example, the [Owned] attribute and the ILazyLoader interface are located here.</span></span>

## <a name="state-change-events"></a><span data-ttu-id="d5d75-169">Zustandsänderungsereignisse</span><span class="sxs-lookup"><span data-stu-id="d5d75-169">State change events</span></span>

<span data-ttu-id="d5d75-170">Die neuen Ereignisse `Tracked` und `StateChanged` für `ChangeTracker` können verwendet werden, um Logik zur Reaktion auf Entitäten zu schreiben, die in den DbContext wechseln oder ihren Zustand ändern.</span><span class="sxs-lookup"><span data-stu-id="d5d75-170">New `Tracked` And `StateChanged` events on `ChangeTracker` can be used to write logic that reacts to entities entering the DbContext or changing their state.</span></span>

## <a name="raw-sql-parameter-analyzer"></a><span data-ttu-id="d5d75-171">Raw-SQL-Parameteranalyse</span><span class="sxs-lookup"><span data-stu-id="d5d75-171">Raw SQL parameter analyzer</span></span>

<span data-ttu-id="d5d75-172">EF Core umfasst ein neues Tool für die Codeanalyse, mit der potenziell unsichere Verwendungen unserer Raw-SQL-APIs erkannt werden, z.B. `FromSql` oder `ExecuteSqlCommand`.</span><span class="sxs-lookup"><span data-stu-id="d5d75-172">A new code analyzer is included with EF Core that detects potentially unsafe usages of our raw-SQL APIs, like `FromSql` or `ExecuteSqlCommand`.</span></span> <span data-ttu-id="d5d75-173">Für die folgende Abfrage wird beispielweise eine Warnung angezeigt, weil _minAge_ nicht parametrisiert ist:</span><span class="sxs-lookup"><span data-stu-id="d5d75-173">For example, for the following query, you will see a warning because _minAge_ is not parameterized:</span></span>

``` csharp
var sql = $"SELECT * FROM People WHERE Age > {minAge}";
var query = context.People.FromSql(sql);
```

## <a name="database-provider-compatibility"></a><span data-ttu-id="d5d75-174">Kompatibilität mit Datenbankanbietern</span><span class="sxs-lookup"><span data-stu-id="d5d75-174">Database provider compatibility</span></span>

<span data-ttu-id="d5d75-175">Es wird empfohlen, EF Core 2.1 mit Anbietern zu verwenden, die ein Update auf EF Core 2.1 durchgeführt oder EF Core 2.1 bereits getestet haben.</span><span class="sxs-lookup"><span data-stu-id="d5d75-175">It is recommended that you use EF Core 2.1 with providers that have been updated or at least tested to work with EF Core 2.1.</span></span>

> [!TIP]
> <span data-ttu-id="d5d75-176">Falls unerwarteterweise keine Kompatibilität besteht oder Probleme mit den neuen Features auftreten, oder falls Sie uns Ihre Feedback mitteilen möchten, melden Sie uns dies über [unsere Problemverfolgung](https://github.com/aspnet/EntityFrameworkCore/issues/new).</span><span class="sxs-lookup"><span data-stu-id="d5d75-176">If you find any unexpected incompatibility or any issue in the new features, or if you have feedback on them, please report it using [our issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues/new).</span></span>
