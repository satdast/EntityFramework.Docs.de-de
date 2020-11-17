---
title: 'EF Core: Breaking Changes in EF Core 5.0'
description: Vollständige Liste der in Entity Framework Core 5.0 eingeführten Breaking Changes
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e2537dbc1d5dba48450bd0fea7712054ba2fa622
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503175"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="0bf7c-103">Breaking Changes in EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="0bf7c-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="0bf7c-104">Die folgenden API-Änderungen und Behavior Changes können dazu führen, dass vorhandene Anwendungen nach einem Upgrade auf EF Core 5.0.0 nicht mehr funktionieren.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="0bf7c-105">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="0bf7c-105">Summary</span></span>

| <span data-ttu-id="0bf7c-106">**Wichtige Änderung**</span><span class="sxs-lookup"><span data-stu-id="0bf7c-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="0bf7c-107">**Auswirkungen**</span><span class="sxs-lookup"><span data-stu-id="0bf7c-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="0bf7c-108">Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität</span><span class="sxs-lookup"><span data-stu-id="0bf7c-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="0bf7c-109">Medium</span><span class="sxs-lookup"><span data-stu-id="0bf7c-109">Medium</span></span>     |
| [<span data-ttu-id="0bf7c-110">Die definierende Abfrage wurde durch anbieterspezifische Methoden ersetzt</span><span class="sxs-lookup"><span data-stu-id="0bf7c-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="0bf7c-111">Medium</span><span class="sxs-lookup"><span data-stu-id="0bf7c-111">Medium</span></span>     |
| [<span data-ttu-id="0bf7c-112">Navigationen ohne Nullverweis werden von Abfragen nicht überschrieben</span><span class="sxs-lookup"><span data-stu-id="0bf7c-112">Non-null reference navigations are not overwritten by queries</span></span>](#nonnullreferences)                                                   | <span data-ttu-id="0bf7c-113">Medium</span><span class="sxs-lookup"><span data-stu-id="0bf7c-113">Medium</span></span>     |
| [<span data-ttu-id="0bf7c-114">Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt</span><span class="sxs-lookup"><span data-stu-id="0bf7c-114">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="0bf7c-115">Niedrig</span><span class="sxs-lookup"><span data-stu-id="0bf7c-115">Low</span></span>        |
| [<span data-ttu-id="0bf7c-116">Cosmos: Der Partitionsschlüssel wird nun dem Primärschlüssel hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="0bf7c-116">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="0bf7c-117">Niedrig</span><span class="sxs-lookup"><span data-stu-id="0bf7c-117">Low</span></span>        |
| [<span data-ttu-id="0bf7c-118">Cosmos: Die `id`-Eigenschaft wurde umbenannt in `__id`</span><span class="sxs-lookup"><span data-stu-id="0bf7c-118">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="0bf7c-119">Niedrig</span><span class="sxs-lookup"><span data-stu-id="0bf7c-119">Low</span></span>        |
| <span data-ttu-id="0bf7c-120">[Cosmos: byte[] wird nun als Base64-Zeichenfolge statt als Zahlenarray gespeichert](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="0bf7c-120">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="0bf7c-121">Niedrig</span><span class="sxs-lookup"><span data-stu-id="0bf7c-121">Low</span></span>        |
| [<span data-ttu-id="0bf7c-122">Cosmos: GetPropertyName und SetPropertyName wurden umbenannt</span><span class="sxs-lookup"><span data-stu-id="0bf7c-122">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="0bf7c-123">Niedrig</span><span class="sxs-lookup"><span data-stu-id="0bf7c-123">Low</span></span>        |
| [<span data-ttu-id="0bf7c-124">Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird</span><span class="sxs-lookup"><span data-stu-id="0bf7c-124">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="0bf7c-125">Niedrig</span><span class="sxs-lookup"><span data-stu-id="0bf7c-125">Low</span></span>        |
| [<span data-ttu-id="0bf7c-126">IMigrationsModelDiffer verwendet jetzt IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="0bf7c-126">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="0bf7c-127">Niedrig</span><span class="sxs-lookup"><span data-stu-id="0bf7c-127">Low</span></span>        |
| [<span data-ttu-id="0bf7c-128">Diskriminatoren sind schreibgeschützt</span><span class="sxs-lookup"><span data-stu-id="0bf7c-128">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="0bf7c-129">Niedrig</span><span class="sxs-lookup"><span data-stu-id="0bf7c-129">Low</span></span>        |
| [<span data-ttu-id="0bf7c-130">Anbieterspezifische EF.Functions-Methoden werden für InMemory-Anbieter ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-130">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="0bf7c-131">Niedrig</span><span class="sxs-lookup"><span data-stu-id="0bf7c-131">Low</span></span>        |
| [<span data-ttu-id="0bf7c-132">IndexBuilder.HasName ist mittlerweile veraltet</span><span class="sxs-lookup"><span data-stu-id="0bf7c-132">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="0bf7c-133">Niedrig</span><span class="sxs-lookup"><span data-stu-id="0bf7c-133">Low</span></span>        |
| [<span data-ttu-id="0bf7c-134">Ein Pluarlizer ist nun für den Gerüstbau zurückentwickelter Modelle enthalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-134">A pluarlizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="0bf7c-135">Niedrig</span><span class="sxs-lookup"><span data-stu-id="0bf7c-135">Low</span></span>        |
| [<span data-ttu-id="0bf7c-136">INavigationBase ersetzt INavigation in einigen APIs zur Unterstützung überspringender Navigationen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-136">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>](#inavigationbase)                                     | <span data-ttu-id="0bf7c-137">Niedrig</span><span class="sxs-lookup"><span data-stu-id="0bf7c-137">Low</span></span>        |
| [<span data-ttu-id="0bf7c-138">Einige Abfragen mit korrelierter Sammlung, die ebenfalls `Distinct` oder `GroupBy` verwenden, werden nicht mehr unterstützt</span><span class="sxs-lookup"><span data-stu-id="0bf7c-138">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>](#collection-distinct-groupby) | <span data-ttu-id="0bf7c-139">Niedrig</span><span class="sxs-lookup"><span data-stu-id="0bf7c-139">Low</span></span>        |
| [<span data-ttu-id="0bf7c-140">Die Verwendung einer Sammlung abfragbarer Typen in der Projektion wird nicht unterstützt</span><span class="sxs-lookup"><span data-stu-id="0bf7c-140">Using a collection of Queryable type in projection is not supported</span></span>](#queryable-projection)                                          | <span data-ttu-id="0bf7c-141">Niedrig</span><span class="sxs-lookup"><span data-stu-id="0bf7c-141">Low</span></span>        |

## <a name="medium-impact-changes"></a><span data-ttu-id="0bf7c-142">Änderungen mit mittlerer Auswirkung</span><span class="sxs-lookup"><span data-stu-id="0bf7c-142">Medium-impact changes</span></span>

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="0bf7c-143">Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität</span><span class="sxs-lookup"><span data-stu-id="0bf7c-143">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="0bf7c-144">Tracking Issue 17286</span><span class="sxs-lookup"><span data-stu-id="0bf7c-144">Tracking Issue #17286</span></span>](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a><span data-ttu-id="0bf7c-145">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-145">Old behavior</span></span>

<span data-ttu-id="0bf7c-146">Nur die Navigation zum Prinzipal konnte als erforderlich konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-146">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="0bf7c-147">Daher führte die Verwendung von `RequiredAttribute` für die Navigation zur abhängigen Entität (der Entität, die den Fremdschlüssel enthält) stattdessen zur Erstellung des Fremdschlüssels für den definierenden Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-147">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0bf7c-148">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-148">New behavior</span></span>

<span data-ttu-id="0bf7c-149">Dank der hinzugefügten Unterstützung von erforderlichen abhängigen Entitäten ist es nun möglich, jede beliebige Verweisnavigation als erforderlich zu markieren. Dies bedeutet, dass im obigen Fall der Fremdschlüssel auf der anderen Seite der Beziehung definiert wird und die Eigenschaften nicht als erforderlich markiert werden.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-149">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="0bf7c-150">Das Aufrufen von `IsRequired` vor der Angabe der abhängigen Entität ist nun mehrdeutig:</span><span class="sxs-lookup"><span data-stu-id="0bf7c-150">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a><span data-ttu-id="0bf7c-151">Warum?</span><span class="sxs-lookup"><span data-stu-id="0bf7c-151">Why</span></span>

<span data-ttu-id="0bf7c-152">Das neue Verhalten ist erforderlich, um die Unterstützung von erforderlichen abhängigen Entitäten zu ermöglichen ([siehe 12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="0bf7c-152">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0bf7c-153">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-153">Mitigations</span></span>

<span data-ttu-id="0bf7c-154">Entfernen Sie `RequiredAttribute` aus der Navigation zur abhängigen Entität, und verwenden Sie „RequiredAttribute“ stattdessen in der Navigation zum Prinzipal, oder konfigurieren Sie die Beziehung in `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="0bf7c-154">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="0bf7c-155">Die definierende Abfrage wurde durch anbieterspezifische Methoden ersetzt</span><span class="sxs-lookup"><span data-stu-id="0bf7c-155">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="0bf7c-156">Issue 18903</span><span class="sxs-lookup"><span data-stu-id="0bf7c-156">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a><span data-ttu-id="0bf7c-157">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-157">Old behavior</span></span>

<span data-ttu-id="0bf7c-158">Entitätstypen wurden zum Definieren von Abfragen auf der Kernebene zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-158">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="0bf7c-159">Jedes Mal, wenn der Entitätstyp im Abfragestamm des Entitätstyps verwendet wurde, wurde er bei allen Anbietern durch die definierende Abfrage ersetzt.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-159">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0bf7c-160">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-160">New behavior</span></span>

<span data-ttu-id="0bf7c-161">APIs für definierende Abfragen sind veraltet.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-161">APIs for defining query are deprecated.</span></span> <span data-ttu-id="0bf7c-162">Es wurden neue anbieterspezifische APIs eingeführt.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-162">New provider-specific APIs were introduced.</span></span>

#### <a name="why"></a><span data-ttu-id="0bf7c-163">Warum?</span><span class="sxs-lookup"><span data-stu-id="0bf7c-163">Why</span></span>

<span data-ttu-id="0bf7c-164">Während definierende Abfragen als Ersatzabfragen implementiert wurden, wenn in der Abfrage der Abfragestamm verwendet wird, sind einige Probleme aufgetreten:</span><span class="sxs-lookup"><span data-stu-id="0bf7c-164">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="0bf7c-165">Wenn eine definierende Abfrage einen Entitätstyp mit `new { ... }` in die `Select`-Methode projiziert, war für die Identifizierung dessen als Entität zusätzliche Arbeit erforderlich. Zudem sorgte dieses Vorgehen für Inkonsistenzen in Bezug darauf, wie EF Core nominale Typen in der Abfrage behandelt.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-165">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="0bf7c-166">Bei relationalen Anbietern ist `FromSql` weiterhin erforderlich, um die SQL-Zeichenfolge im LINQ-Ausdrucksformat zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-166">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="0bf7c-167">Zunächst wurden definierende Abfragen als clientseitige Sichten zur Verwendung mit In-Memory-Anbietern bei schlüssellosen Entitäten eingeführt (ähnlich wie bei Datenbanksichten in relationalen Datenbanken).</span><span class="sxs-lookup"><span data-stu-id="0bf7c-167">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="0bf7c-168">Eine solche Definition erleichtert das Testen der Anwendung mit In-Memory-Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-168">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="0bf7c-169">Später wurden sie allgemein anwendbar, was zwar nützlich war, aber ein inkonsistentes und schwer verständliches Verhalten mit sich brachte.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-169">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="0bf7c-170">Daher haben wir uns entschieden, das Konzept zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-170">So we decided to simplify the concept.</span></span> <span data-ttu-id="0bf7c-171">Wir stellen die LINQ-basierte definierende Abfrage nur noch für In-Memory-Anbieter bereit und behandeln sie anders.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-171">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="0bf7c-172">Weitere Informationen finden Sie in [diesem Thema](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="0bf7c-172">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0bf7c-173">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-173">Mitigations</span></span>

<span data-ttu-id="0bf7c-174">Verwenden Sie für relationale Anbieter die `ToSqlQuery`-Methode in `OnModelCreating`, und übergeben Sie eine SQL-Zeichenfolge zur Verwendung für den Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-174">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="0bf7c-175">Verwenden Sie für den In-Memory-Anbieter die `ToInMemoryQuery`-Methode in `OnModelCreating`, und übergeben Sie eine LINQ-Abfrage zur Verwendung für den Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-175">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a><span data-ttu-id="0bf7c-176">Navigationen ohne Nullverweis werden von Abfragen nicht überschrieben</span><span class="sxs-lookup"><span data-stu-id="0bf7c-176">Non-null reference navigations are not overwritten by queries</span></span>

[<span data-ttu-id="0bf7c-177">Nachverfolgung von Issue 2693</span><span class="sxs-lookup"><span data-stu-id="0bf7c-177">Tracking Issue #2693</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a><span data-ttu-id="0bf7c-178">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-178">Old behavior</span></span>

<span data-ttu-id="0bf7c-179">In EF Core 3.1 wurden Verweisnavigationen, die frühzeitig mit Werten ungleich NULL initialisiert wurden, mitunter von Entitätsinstanzen aus der Datenbank überschrieben, und zwar unabhängig davon, ob Schlüsselwerte übereinstimmten oder nicht.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-179">In EF Core 3.1, reference navigations eagerly initialized to non-null values would sometimes be overwritten by entity instances from the database, regardless of whether or not key values matched.</span></span> <span data-ttu-id="0bf7c-180">In anderen Fällen hat EF Core 3.1 jedoch das Gegenteil bewirkt und den vorhandenen Wert ungleich NULL beibehalten.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-180">However, in other cases, EF Core 3.1 would do the opposite and leave the existing non-null value.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0bf7c-181">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-181">New behavior</span></span>

<span data-ttu-id="0bf7c-182">Ab EF Core 5.0 werden Verweisnavigationen ungleich NULL nicht mehr durch Instanzen überschrieben, die von einer Abfrage zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-182">Starting with EF Core 5.0, non-null reference navigations are never overwritten by instances returned from a query.</span></span>

<span data-ttu-id="0bf7c-183">Beachten Sie, dass die frühzeitige Initialisierung einer _Sammlungsnavigation_ als leere Sammlung nach wie vor unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-183">Note that eager initialization of a _collection_ navigation to an empty collection is still supported.</span></span>

#### <a name="why"></a><span data-ttu-id="0bf7c-184">Warum?</span><span class="sxs-lookup"><span data-stu-id="0bf7c-184">Why</span></span>

<span data-ttu-id="0bf7c-185">Die Initialisierung einer Verweisnavigationseigenschaft als „leere“ Entitätsinstanz führt zu einem uneindeutigen Zustand.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-185">Initialization of a reference navigation property to an "empty" entity instance results in an ambiguous state.</span></span> <span data-ttu-id="0bf7c-186">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0bf7c-186">For example:</span></span>

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

<span data-ttu-id="0bf7c-187">Normalerweise erstellt eine Abfrage von Blogs und Autoren zunächst `Blog`-Instanzen. Dann werden die entsprechenden `Author`-Instanzen auf Grundlage der aus der Datenbank zurückgegebenen Daten festgelegt.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-187">Normally a query for Blogs and Authors will first create `Blog` instances and then set the appropriate `Author` instances based on the data returned from the database.</span></span> <span data-ttu-id="0bf7c-188">In diesem Fall ist jedoch jede `Blog.Author`-Eigenschaft bereits als leere `Author`-Instanz initialisiert.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-188">However, in this case every `Blog.Author` property is already initialized to an empty `Author`.</span></span> <span data-ttu-id="0bf7c-189">Allerdings kann EF Core nicht wissen, dass diese Instanz „leer“ ist.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-189">Except EF Core has no way to know that this instance is "empty".</span></span> <span data-ttu-id="0bf7c-190">Das Überschreiben dieser Instanz könnte also unbemerkt eine gültige `Author`-Instanz auslösen.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-190">So overwriting this instance could potentially silently throw away a valid `Author`.</span></span> <span data-ttu-id="0bf7c-191">Daher überschreibt EF Core 5.0 jetzt konsequent keine Navigation, die bereits initialisiert ist.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-191">Therefore, EF Core 5.0 now consistently does not overwrite a navigation that is already initialized.</span></span>

<span data-ttu-id="0bf7c-192">Dieses neue Verhalten stimmt in den meisten Fällen auch mit dem Verhalten von EF6 überein, obwohl wir bei Untersuchungen auch einige Fälle von Inkonsistenz in EF6 gefunden haben.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-192">This new behavior also aligns with the behavior of EF6 in most cases, although upon investigation we also found some cases of inconsistency in EF6.</span></span>  

#### <a name="mitigations"></a><span data-ttu-id="0bf7c-193">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-193">Mitigations</span></span>

<span data-ttu-id="0bf7c-194">Wenn dieses Problem auftritt, besteht die Lösung darin, die vorzeitige Initialisierung der Verweisnavigationseigenschaften zu beenden.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-194">If this break is encountered, then the fix is to stop eagerly initializing reference navigation properties.</span></span>

## <a name="low-impact-changes"></a><span data-ttu-id="0bf7c-195">Änderungen mit geringer Auswirkung</span><span class="sxs-lookup"><span data-stu-id="0bf7c-195">Low-impact changes</span></span>

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="0bf7c-196">Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-196">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="0bf7c-197">Issue 14257</span><span class="sxs-lookup"><span data-stu-id="0bf7c-197">Tracking Issue #14257</span></span>](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a><span data-ttu-id="0bf7c-198">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-198">Old behavior</span></span>

<span data-ttu-id="0bf7c-199">Die HasGeometricDimension-Methode wurde dazu verwendet, zusätzliche Maße (Z und M) für Geometriespalten zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-199">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="0bf7c-200">Dies hat sich jedoch immer nur auf die Datenbankerstellung ausgewirkt.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-200">However, it only ever affected database creation.</span></span> <span data-ttu-id="0bf7c-201">Das Angeben dieser Methode zum Abfragen von Werten mit zusätzlichen Maßen war nicht notwendig.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-201">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="0bf7c-202">Außerdem hat die Methode nicht ordnungsgemäß funktioniert, wenn Werte mit zusätzlichen Maßen eingefügt oder geändert wurden (siehe [Issue 14257](https://github.com/dotnet/efcore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="0bf7c-202">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/dotnet/efcore/issues/14257)).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0bf7c-203">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-203">New behavior</span></span>

<span data-ttu-id="0bf7c-204">Zum Ermöglichen des Einfügens und Änderns von Geometriewerten mit zusätzlichen Maßen (Z und M) muss das jeweilige Maß als Teil des Spaltentypnamens angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-204">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="0bf7c-205">Diese API entspricht eher dem zugrunde liegenden Verhalten der AddGeometryColumn-Funktion von SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-205">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

#### <a name="why"></a><span data-ttu-id="0bf7c-206">Warum?</span><span class="sxs-lookup"><span data-stu-id="0bf7c-206">Why</span></span>

<span data-ttu-id="0bf7c-207">Die Verwendung der HasGeometricDimension-Methode, nachdem das Maß im Spaltentyp festgelegt wurde, ist redundant und nicht notwendig. Daher wurde die gesamte HasGeometricDimension-Methode entfernt.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-207">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0bf7c-208">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-208">Mitigations</span></span>

<span data-ttu-id="0bf7c-209">Verwenden Sie `HasColumnType` zum Festlegen der Maße:</span><span class="sxs-lookup"><span data-stu-id="0bf7c-209">Use `HasColumnType` to specify the dimension:</span></span>

```csharp
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="0bf7c-210">Cosmos: Der Partitionsschlüssel wird nun dem Primärschlüssel hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="0bf7c-210">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="0bf7c-211">Tracking Issue 15289</span><span class="sxs-lookup"><span data-stu-id="0bf7c-211">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="0bf7c-212">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-212">Old behavior</span></span>

<span data-ttu-id="0bf7c-213">Die Partitionsschlüsseleigenschaft wurde nur dem alternativen Schlüssel hinzugefügt, der `id` enthält.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-213">The partition key property was only added to the alternate key that includes `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0bf7c-214">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-214">New behavior</span></span>

<span data-ttu-id="0bf7c-215">Die Partitionsschlüsseleigenschaft wird gemäß Konvention jetzt auch dem Primärschlüssel hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-215">The partition key property is now also added to the primary key by convention.</span></span>

#### <a name="why"></a><span data-ttu-id="0bf7c-216">Warum?</span><span class="sxs-lookup"><span data-stu-id="0bf7c-216">Why</span></span>

<span data-ttu-id="0bf7c-217">Durch diese Änderung ist das Modell besser auf die Azure Cosmos DB-Semantik abgestimmt. Zudem verbessert sich dadurch die Leistung von `Find` und einigen Abfragen.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-217">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0bf7c-218">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-218">Mitigations</span></span>

<span data-ttu-id="0bf7c-219">Um zu verhindern, dass die Partitionsschlüsseleigenschaft dem Primärschlüssel hinzugefügt wird, konfigurieren Sie sie in `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-219">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="0bf7c-220">Cosmos: Die `id`-Eigenschaft wurde umbenannt in `__id`</span><span class="sxs-lookup"><span data-stu-id="0bf7c-220">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="0bf7c-221">Issue 17751</span><span class="sxs-lookup"><span data-stu-id="0bf7c-221">Tracking Issue #17751</span></span>](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a><span data-ttu-id="0bf7c-222">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-222">Old behavior</span></span>

<span data-ttu-id="0bf7c-223">Die der JSON-Eigenschaft `id` zugeordnete Schatteneigenschaft hatte ebenfalls den Namen `id`.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-223">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0bf7c-224">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-224">New behavior</span></span>

<span data-ttu-id="0bf7c-225">Die gemäß Konvention erstellte Schatteneigenschaft trägt nun den Namen `__id`.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-225">The shadow property created by convention is now named `__id`.</span></span>

#### <a name="why"></a><span data-ttu-id="0bf7c-226">Warum?</span><span class="sxs-lookup"><span data-stu-id="0bf7c-226">Why</span></span>

<span data-ttu-id="0bf7c-227">Durch diese Änderung ist es weniger wahrscheinlich, dass die Eigenschaft `id` mit einer vorhandenen Eigenschaft des Entitätstyps kollidiert.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-227">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0bf7c-228">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-228">Mitigations</span></span>

<span data-ttu-id="0bf7c-229">Wenn Sie zum Verhalten der Version 3.x zurückkehren möchten, konfigurieren Sie die Eigenschaft `id` in `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-229">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="0bf7c-230">Cosmos: byte[] wird nun als Base64-Zeichenfolge statt als Zahlenarray gespeichert</span><span class="sxs-lookup"><span data-stu-id="0bf7c-230">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="0bf7c-231">Issue 17306</span><span class="sxs-lookup"><span data-stu-id="0bf7c-231">Tracking Issue #17306</span></span>](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a><span data-ttu-id="0bf7c-232">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-232">Old behavior</span></span>

<span data-ttu-id="0bf7c-233">Eigenschaften vom Typ „byte[]“ wurden als Zahlenarray gespeichert.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-233">Properties of type byte[] were stored as a number array.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0bf7c-234">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-234">New behavior</span></span>

<span data-ttu-id="0bf7c-235">Eigenschaften vom Typ „byte[]“ werden nun als Base64-Zeichenfolge gespeichert.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-235">Properties of type byte[] are now stored as a base64 string.</span></span>

#### <a name="why"></a><span data-ttu-id="0bf7c-236">Warum?</span><span class="sxs-lookup"><span data-stu-id="0bf7c-236">Why</span></span>

<span data-ttu-id="0bf7c-237">Diese Darstellung von „byte[]“ entspricht eher den Erwartungen. Zudem handelt es sich hier um das Standardverhalten der wichtigen JSON-Serialisierungsbibliotheken.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-237">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0bf7c-238">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-238">Mitigations</span></span>

<span data-ttu-id="0bf7c-239">Als Zahlenarrays gespeicherte, bereits vorhandene Daten werden weiterhin ordnungsgemäß abgefragt. Derzeit wird jedoch keine Möglichkeit unterstützt, das Einfügeverhalten zu ändern.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-239">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="0bf7c-240">Wenn Ihr Szenario durch diese Einschränkung blockiert wird, kommentieren Sie dieses [Problem](https://github.com/dotnet/efcore/issues/17306).</span><span class="sxs-lookup"><span data-stu-id="0bf7c-240">If this limitation is blocking your scenario, comment on [this issue](https://github.com/dotnet/efcore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="0bf7c-241">Cosmos: GetPropertyName und SetPropertyName wurden umbenannt</span><span class="sxs-lookup"><span data-stu-id="0bf7c-241">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="0bf7c-242">Issue 17874</span><span class="sxs-lookup"><span data-stu-id="0bf7c-242">Tracking Issue #17874</span></span>](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a><span data-ttu-id="0bf7c-243">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-243">Old behavior</span></span>

<span data-ttu-id="0bf7c-244">Bisher wurden die Erweiterungsmethoden `GetPropertyName` und `SetPropertyName` genannt.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-244">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0bf7c-245">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-245">New behavior</span></span>

<span data-ttu-id="0bf7c-246">Die alte API wurde entfernt, und es wurden die neuen Methoden `GetJsonPropertyName` und `SetJsonPropertyName` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-246">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

#### <a name="why"></a><span data-ttu-id="0bf7c-247">Warum?</span><span class="sxs-lookup"><span data-stu-id="0bf7c-247">Why</span></span>

<span data-ttu-id="0bf7c-248">Durch diese Änderung wird die Mehrdeutigkeit um die Konfiguration durch diese Methoden beseitigt.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-248">This change removes the ambiguity around what these methods are configuring.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0bf7c-249">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-249">Mitigations</span></span>

<span data-ttu-id="0bf7c-250">Verwenden Sie die neue API.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-250">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="0bf7c-251">Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird</span><span class="sxs-lookup"><span data-stu-id="0bf7c-251">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="0bf7c-252">Tracking Issue 15289</span><span class="sxs-lookup"><span data-stu-id="0bf7c-252">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="0bf7c-253">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-253">Old behavior</span></span>

<span data-ttu-id="0bf7c-254">Wert-Generatoren wurden nur aufgerufen, wenn der Entitätszustand in „Hinzugefügt“ geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-254">Value generators were only called when the entity state changed to Added.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0bf7c-255">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-255">New behavior</span></span>

<span data-ttu-id="0bf7c-256">Wert-Generatoren werden jetzt aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird und die Eigenschaft die Standardwerte enthält.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-256">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

#### <a name="why"></a><span data-ttu-id="0bf7c-257">Warum?</span><span class="sxs-lookup"><span data-stu-id="0bf7c-257">Why</span></span>

<span data-ttu-id="0bf7c-258">Diese Änderung war erforderlich, um die Verwendung von Eigenschaften zu verbessern, die nicht dauerhaft im Datenspeicher gespeichert sind und deren Wert immer auf dem Client generiert wird.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-258">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0bf7c-259">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-259">Mitigations</span></span>

<span data-ttu-id="0bf7c-260">Weisen Sie der Eigenschaft vor der Änderung des Status einen anderen Wert als den Standardwert zu, um zu verhindern, dass der Wert-Generator aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-260">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="0bf7c-261">IMigrationsModelDiffer verwendet jetzt IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="0bf7c-261">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="0bf7c-262">Tracking Issue 20305</span><span class="sxs-lookup"><span data-stu-id="0bf7c-262">Tracking Issue #20305</span></span>](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a><span data-ttu-id="0bf7c-263">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-263">Old behavior</span></span>

<span data-ttu-id="0bf7c-264">Die `IMigrationsModelDiffer`-API wurde mithilfe von `IModel` definiert.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-264">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0bf7c-265">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-265">New behavior</span></span>

<span data-ttu-id="0bf7c-266">Die `IMigrationsModelDiffer`-API verwendet jetzt `IRelationalModel`.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-266">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="0bf7c-267">Die Modellmomentaufnahme enthält jedoch weiterhin nur `IModel`, da dieser Code Teil der Anwendung ist und Entity Framework ihn nicht ohne einen größeren Breaking Change ändern kann.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-267">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

#### <a name="why"></a><span data-ttu-id="0bf7c-268">Warum?</span><span class="sxs-lookup"><span data-stu-id="0bf7c-268">Why</span></span>

<span data-ttu-id="0bf7c-269">`IRelationalModel` ist eine neu hinzugefügte Darstellung des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-269">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="0bf7c-270">Die Verwendung dieser Darstellung zum Ermitteln von Unterschieden ist schneller und präziser.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-270">Using it to find differences is faster and more accurate.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0bf7c-271">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-271">Mitigations</span></span>

<span data-ttu-id="0bf7c-272">Verwenden Sie den folgenden Code, um das Modell von `snapshot` mit dem Modell von `context` zu vergleichen:</span><span class="sxs-lookup"><span data-stu-id="0bf7c-272">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

```csharp
var dependencies = context.GetService<ProviderConventionSetBuilderDependencies>();
var relationalDependencies = context.GetService<RelationalConventionSetBuilderDependencies>();

var typeMappingConvention = new TypeMappingConvention(dependencies);
typeMappingConvention.ProcessModelFinalizing(((IConventionModel)modelSnapshot.Model).Builder, null);

var relationalModelConvention = new RelationalModelConvention(dependencies, relationalDependencies);
var sourceModel = relationalModelConvention.ProcessModelFinalized(snapshot.Model);

var modelDiffer = context.GetService<IMigrationsModelDiffer>();
var hasDifferences = modelDiffer.HasDifferences(
    ((IMutableModel)sourceModel).FinalizeModel().GetRelationalModel(),
    context.Model.GetRelationalModel());
```

<span data-ttu-id="0bf7c-273">Eine Verbesserung dieser Funktion in Version 6.0 ist geplant ([siehe 22031](https://github.com/dotnet/efcore/issues/22031)).</span><span class="sxs-lookup"><span data-stu-id="0bf7c-273">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="0bf7c-274">Diskriminatoren sind schreibgeschützt</span><span class="sxs-lookup"><span data-stu-id="0bf7c-274">Discriminators are read-only</span></span>

[<span data-ttu-id="0bf7c-275">Tracking Issue 21154</span><span class="sxs-lookup"><span data-stu-id="0bf7c-275">Tracking Issue #21154</span></span>](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a><span data-ttu-id="0bf7c-276">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-276">Old behavior</span></span>

<span data-ttu-id="0bf7c-277">Der Diskriminatorwert konnte vor dem Aufrufen von `SaveChanges` geändert werden.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-277">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0bf7c-278">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-278">New behavior</span></span>

<span data-ttu-id="0bf7c-279">Im obigen Fall wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-279">An exception will be throws in the above case.</span></span>

#### <a name="why"></a><span data-ttu-id="0bf7c-280">Warum?</span><span class="sxs-lookup"><span data-stu-id="0bf7c-280">Why</span></span>

<span data-ttu-id="0bf7c-281">EF erwartet nicht, dass der Entitätstyp geändert wird, während er noch nachverfolgt wird, sodass das Ändern des Diskriminatorwerts zu einem inkonsistenten Kontext führt. Dies hat möglicherweise ein unerwartetes Verhalten zur Folge.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-281">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0bf7c-282">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-282">Mitigations</span></span>

<span data-ttu-id="0bf7c-283">Wenn eine Änderung des Diskriminatorwerts erforderlich ist und der Kontext direkt nach dem Aufruf von `SaveChanges` verworfen wird, kann der Diskriminator als änderbar festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="0bf7c-283">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="0bf7c-284">Anbieterspezifische EF.Functions-Methoden werden für InMemory-Anbieter ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-284">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="0bf7c-285">Issue 20294</span><span class="sxs-lookup"><span data-stu-id="0bf7c-285">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a><span data-ttu-id="0bf7c-286">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-286">Old behavior</span></span>

<span data-ttu-id="0bf7c-287">Anbieterspezifische EF.Functions-Methoden enthielten eine Implementierung für die Clientausführung, sodass sie für den InMemory-Anbieter ausgeführt werden konnten.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-287">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="0bf7c-288">`EF.Functions.DateDiffDay` ist beispielsweise eine SQL Server-spezifische Methode, die für InMemory-Anbieter galt.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-288">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0bf7c-289">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-289">New behavior</span></span>

<span data-ttu-id="0bf7c-290">Anbieterspezifische Methoden wurden aktualisiert, sodass sie im Methodentext eine Ausnahme auslösen, um ihre Auswertung auf Clientseite zu blockieren.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-290">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

#### <a name="why"></a><span data-ttu-id="0bf7c-291">Warum?</span><span class="sxs-lookup"><span data-stu-id="0bf7c-291">Why</span></span>

<span data-ttu-id="0bf7c-292">Anbieterspezifische Methoden sind einer Datenbankfunktion zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-292">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="0bf7c-293">Das Berechnungsergebnis der zugeordneten Datenbankfunktion kann auf Clientseite in LINQ nicht immer repliziert werden.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-293">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="0bf7c-294">Dies kann dazu führen, dass sich das Ergebnis von dem des Servers unterscheidet, wenn dieselbe Methode auf dem Client ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-294">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="0bf7c-295">Da diese Methoden in LINQ verwendet werden, um Übersetzungen in bestimmte Datenbankfunktionen durchzuführen, müssen sie nicht auf Clientseite ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-295">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="0bf7c-296">Da der InMemory-Anbieter eine andere *Datenbank* ist, sind die Methoden für diesen Anbieter nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-296">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="0bf7c-297">Wenn Sie versuchen, sie für den InMemory-Anbieter oder einen anderen Anbieter auszuführen, der diese Methoden nicht übersetzt, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-297">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0bf7c-298">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-298">Mitigations</span></span>

<span data-ttu-id="0bf7c-299">Da es keine Möglichkeit gibt, das Verhalten von Datenbankfunktionen exakt zu imitieren, sollten Sie die Abfragen, die diese Methoden enthalten, mit demselben Datenbanktyp testen, der auch in der Produktionsumgebung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-299">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="0bf7c-300">IndexBuilder.HasName ist mittlerweile veraltet</span><span class="sxs-lookup"><span data-stu-id="0bf7c-300">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="0bf7c-301">Nachverfolgung von Issue 21089</span><span class="sxs-lookup"><span data-stu-id="0bf7c-301">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a><span data-ttu-id="0bf7c-302">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-302">Old behavior</span></span>

<span data-ttu-id="0bf7c-303">Bisher konnte nur ein Index für eine bestimmte Gruppe von Eigenschaften definiert werden.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-303">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="0bf7c-304">Der Datenbankname eines Indexes wurde mithilfe von IndexBuilder.HasName konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-304">The database name of an index was configured using IndexBuilder.HasName.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0bf7c-305">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-305">New behavior</span></span>

<span data-ttu-id="0bf7c-306">Für dieselbe Gruppe oder dieselben Eigenschaften sind jetzt mehrere Indizes zulässig.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-306">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="0bf7c-307">Diese Indizes unterscheiden sich nun durch einen Namen im Modell.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-307">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="0bf7c-308">Gemäß der Konvention wird der Modellname als Datenbankname verwendet. Er kann jedoch auch unabhängig mit HasDatabaseName konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-308">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

#### <a name="why"></a><span data-ttu-id="0bf7c-309">Warum?</span><span class="sxs-lookup"><span data-stu-id="0bf7c-309">Why</span></span>

<span data-ttu-id="0bf7c-310">In Zukunft möchten wir sowohl aufsteigende als auch absteigende Indizes oder Indizes mit unterschiedlichen Sortierungen für denselben Satz von Eigenschaften aktivieren.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-310">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="0bf7c-311">Diese Änderung ist ein weiterer Schritt in diese Richtung.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-311">This change moves us another step in that direction.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0bf7c-312">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-312">Mitigations</span></span>

<span data-ttu-id="0bf7c-313">Sämtlicher Code, der zuvor IndexBuilder.HasName aufgerufen hat, sollte so aktualisiert werden, dass stattdessen HasDatabaseName aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-313">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="0bf7c-314">Wenn das Projekt Migrationen enthält, die vor EF Core Version 2.0.0 generiert wurden, können Sie die Warnung in diesen Dateien problemlos ignorieren und unterdrücken, indem Sie `#pragma warning disable 612, 618` hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-314">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="0bf7c-315">Ein Pluralizer ist nun für den Gerüstbau für Modelle mit Reverse Engineering enthalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-315">A pluralizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="0bf7c-316">Nachverfolgung von Issue 11160</span><span class="sxs-lookup"><span data-stu-id="0bf7c-316">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a><span data-ttu-id="0bf7c-317">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-317">Old behavior</span></span>

<span data-ttu-id="0bf7c-318">Bislang mussten Sie beim Gerüstbau für einen Datenbankkontext und Entitätstypen durch Reverse Engineering eines Datenbankschemas ein separates Pluralizer-Paket installieren, um DbSet- und Sammlungsnavigationsnamen in die Pluralform und Tabellennamen in die Singularform umzuwandeln.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-318">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffolding a DbContext and entity types by reverse engineering a database schema.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0bf7c-319">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-319">New behavior</span></span>

<span data-ttu-id="0bf7c-320">EF Core enthält jetzt einen Pluarlizer, der die [Humanizer](https://github.com/Humanizr/Humanizer)-Bibliothek verwendet.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-320">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="0bf7c-321">Dies ist dieselbe Bibliothek, die Visual Studio verwendet, um Variablennamen zu empfehlen.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-321">This is the same library Visual Studio uses to recommend variable names.</span></span>

#### <a name="why"></a><span data-ttu-id="0bf7c-322">Warum?</span><span class="sxs-lookup"><span data-stu-id="0bf7c-322">Why</span></span>

<span data-ttu-id="0bf7c-323">Die Verwendung von Pluralformen von Wörtern für Sammlungseigenschaften und Singularformen für Typen und Verweiseigenschaften ist in .NET idiomatisch.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-323">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0bf7c-324">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-324">Mitigations</span></span>

<span data-ttu-id="0bf7c-325">Um den Pluralizer zu deaktivieren, verwenden Sie die Option `--no-pluralize` auf `dotnet ef dbcontext scaffold` oder den `-NoPluralize`-Switch auf `Scaffold-DbContext`.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-325">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a><span data-ttu-id="0bf7c-326">INavigationBase ersetzt INavigation in einigen APIs zur Unterstützung überspringender Navigationen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-326">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>

[<span data-ttu-id="0bf7c-327">Nachverfolgung von Issue 2568</span><span class="sxs-lookup"><span data-stu-id="0bf7c-327">Tracking Issue #2568</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a><span data-ttu-id="0bf7c-328">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-328">Old behavior</span></span>

<span data-ttu-id="0bf7c-329">Bis Version 5.0 unterstützte EF Core nur eine Form der Navigationseigenschaft, die durch die `INavigation`-Schnittstelle dargestellt wird.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-329">EF Core prior to 5.0 supported only one form of navigation property, represented by the `INavigation` interface.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="0bf7c-330">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="0bf7c-330">New behavior</span></span>

<span data-ttu-id="0bf7c-331">EF Core 5.0 führt m:n-Beziehungen ein, die „überspringende Navigationen“ verwenden.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-331">EF Core 5.0 introduces many-to-many relationships which use "skip navigations".</span></span> <span data-ttu-id="0bf7c-332">Diese werden durch die `ISkipNavigation`-Schnittstelle dargestellt, und der größte Teil der Funktionalität von `INavigation` wurde auf die gemeinsame Basisschnittstelle `INavigationBase` verlagert.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-332">These are represented by the `ISkipNavigation` interface, and most of the functionality of `INavigation` has been pushed down to a common base interface: `INavigationBase`.</span></span>

#### <a name="why"></a><span data-ttu-id="0bf7c-333">Warum?</span><span class="sxs-lookup"><span data-stu-id="0bf7c-333">Why</span></span>

<span data-ttu-id="0bf7c-334">Die meisten Funktionen sind bei normalen und überspringenden Navigationen identisch.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-334">Most of the functionality between normal and skip navigations is the same.</span></span> <span data-ttu-id="0bf7c-335">Allerdings haben überspringende Navigationen eine andere Beziehung zu Fremdschlüsseln als normale Navigationen, da sich die beteiligten Fremdschlüssel nicht direkt an beiden Enden der Beziehung befinden, sondern in der JOIN-Entität.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-335">However, skip navigations have a different relationship to foreign keys than normal navigations, since the FKs involved are not directly on either end of the relationship, but rather in the join entity.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="0bf7c-336">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="0bf7c-336">Mitigations</span></span>

<span data-ttu-id="0bf7c-337">In vielen Fällen können Anwendungen ohne weitere Änderungen zur Verwendung der neuen Basisschnittstelle übergehen.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-337">In many cases applications can switch to using the new base interface with no other changes.</span></span> <span data-ttu-id="0bf7c-338">In Fällen jedoch, in denen die Navigation für den Zugriff auf Fremdschlüsseleigenschaften verwendet wird, sollte der Code der Anwendung entweder nur auf normale Navigationen beschränkt oder so aktualisiert werden, dass er sowohl bei normalen als auch bei überspringenden Navigationen das Richtige tut.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-338">However, in cases where the navigation is used to access foreign key properties, application code should either be constrained to only normal navigations, or updated to do the appropriate thing for both normal and skip navigations.</span></span>

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a><span data-ttu-id="0bf7c-339">Einige Abfragen mit korrelierter Sammlung, die ebenfalls `Distinct` oder`GroupBy` verwenden, werden nicht mehr unterstützt</span><span class="sxs-lookup"><span data-stu-id="0bf7c-339">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>

[<span data-ttu-id="0bf7c-340">Nachverfolgung von Issue 15873</span><span class="sxs-lookup"><span data-stu-id="0bf7c-340">Tracking Issue #15873</span></span>](https://github.com/dotnet/efcore/issues/15873)

<span data-ttu-id="0bf7c-341">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="0bf7c-341">**Old behavior**</span></span>

<span data-ttu-id="0bf7c-342">Zuvor haben wir die Ausführung von Abfragen mit korrelierten Sammlungen, gefolgt von `GroupBy`, sowie einige Abfragen mit `Distinct` erlaubt.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-342">Previously, queries involving correlated collections followed by `GroupBy`, as well as some queries using `Distinct` we allowed to execute.</span></span>

<span data-ttu-id="0bf7c-343">Beispiel für GroupBy:</span><span class="sxs-lookup"><span data-stu-id="0bf7c-343">GroupBy example:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

<span data-ttu-id="0bf7c-344">Beispiel für `Distinct`: insbesondere `Distinct`-Abfragen, bei denen die innere Sammlungsprojektion nicht den Primärschlüssel enthält:</span><span class="sxs-lookup"><span data-stu-id="0bf7c-344">`Distinct` example - specifically `Distinct` queries where inner collection projection doesn't contain the primary key:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

<span data-ttu-id="0bf7c-345">Diese Abfragen konnten falsche Ergebnisse liefern, wenn die innere Sammlung Duplikate enthielt, funktionierten aber einwandfrei, wenn alle Elemente in der inneren Sammlung eindeutig waren.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-345">These queries could return incorrect results if the inner collection contained any duplicates, but worked correctly if all the elements in the inner collection were unique.</span></span>

<span data-ttu-id="0bf7c-346">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="0bf7c-346">**New behavior**</span></span>

<span data-ttu-id="0bf7c-347">Diese Abfragen werden nicht mehr unterstützt.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-347">These queries are no longer supported.</span></span> <span data-ttu-id="0bf7c-348">Es wird eine Ausnahme ausgelöst, die darauf hinweist, dass wir nicht genügend Informationen haben, um die Ergebnisse ordnungsgemäß zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-348">Exception is thrown indicating that we don't have enough information to correctly build the results.</span></span>

<span data-ttu-id="0bf7c-349">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="0bf7c-349">**Why**</span></span>

<span data-ttu-id="0bf7c-350">Für Szenarien mit korrelierten Sammlungen müssen wir den Primärschlüssel der Entitäten kennen, um die Sammlungsentitäten dem richtigen übergeordneten Element zuweisen zu können.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-350">For correlated collection scenarios we need to know entity's primary key in order to assign collection entities to the correct parent.</span></span> <span data-ttu-id="0bf7c-351">Wenn die innere Sammlung `GroupBy` oder `Distinct` nicht verwendet, kann der fehlende Primärschlüssel einfach zur Projektion hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-351">When inner collection doesn't use `GroupBy` or `Distinct`, the missing primary key can simply be added to the projection.</span></span> <span data-ttu-id="0bf7c-352">Im Falle von `GroupBy` und `Distinct` ist dies jedoch nicht möglich, weil es das Ergebnis des Vorgangs `GroupBy` oder `Distinct` ändern würde.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-352">However in case of `GroupBy` and `Distinct` it can't be done because it would change the result of `GroupBy` or `Distinct` operation.</span></span>

<span data-ttu-id="0bf7c-353">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="0bf7c-353">**Mitigations**</span></span>

<span data-ttu-id="0bf7c-354">Schreiben Sie die Abfrage so um, dass die Vorgänge `GroupBy` oder `Distinct` nicht auf die innere Sammlung angewendet werden. Führen Sie diese Vorgänge stattdessen auf dem Client aus.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-354">Rewrite the query to not use `GroupBy` or `Distinct` operations on the inner collection, and perform these operations on the client instead.</span></span>

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.GroupBy(c => c).Select(g => g.Key))
```

```csharp
context.Parents
    .Select(p => p.Children.Select(c => c.School))
    .ToList()
    .Select(x => x.Distinct())
```

<a name="queryable-projection"></a>

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a><span data-ttu-id="0bf7c-355">Die Verwendung einer Sammlung abfragbarer Typen in der Projektion wird nicht unterstützt</span><span class="sxs-lookup"><span data-stu-id="0bf7c-355">Using a collection of Queryable type in projection is not supported</span></span>

[<span data-ttu-id="0bf7c-356">Nachverfolgung von Issue 16314</span><span class="sxs-lookup"><span data-stu-id="0bf7c-356">Tracking Issue #16314</span></span>](https://github.com/dotnet/efcore/issues/16314)

<span data-ttu-id="0bf7c-357">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="0bf7c-357">**Old behavior**</span></span>

<span data-ttu-id="0bf7c-358">Bislang war es in einigen Fällen möglich, eine Sammlung eines abfragbaren Typs innerhalb der Projektion zu verwenden, z. B. als Argument für einen `List<T>`-Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="0bf7c-358">Previously, it was possible to use collection of a Queryable type inside the projection in some cases, for example as an argument to a `List<T>` constructor:</span></span>

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

<span data-ttu-id="0bf7c-359">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="0bf7c-359">**New behavior**</span></span>

<span data-ttu-id="0bf7c-360">Diese Abfragen werden nicht mehr unterstützt.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-360">These queries are no longer supported.</span></span> <span data-ttu-id="0bf7c-361">Eine Ausnahme wird ausgelöst, die darauf hinweist, dass wir kein Objekt des abfragbaren Typs erstellen können, und vorschlägt, wie dies korrigiert werden könnte.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-361">Exception is thrown indicating that we can't create an object of Queryable type and suggesting how this could be fixed.</span></span>

<span data-ttu-id="0bf7c-362">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="0bf7c-362">**Why**</span></span>

<span data-ttu-id="0bf7c-363">Wir können ein Objekt eines abfragbaren Typs nicht materialisieren. Daher würde es stattdessen automatisch mit dem Typ `List<T>` erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-363">We can't materialize an object of a Queryable type, so they would automatically be created using `List<T>` type instead.</span></span> <span data-ttu-id="0bf7c-364">Dies führte oft zu einer Ausnahme aufgrund nicht übereinstimmender Typen, was nicht sehr klar wurde und für einige Benutzer überraschend sein konnte.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-364">This would often cause an exception due to type mismatch which was not very clear and could be surprising to some users.</span></span> <span data-ttu-id="0bf7c-365">Wir haben uns entschieden, das Muster zu erkennen und eine aussagekräftigere Ausnahme auszulösen.</span><span class="sxs-lookup"><span data-stu-id="0bf7c-365">We decided to recognize the pattern and throw a more meaningful exception.</span></span>

<span data-ttu-id="0bf7c-366">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="0bf7c-366">**Mitigations**</span></span>

<span data-ttu-id="0bf7c-367">Fügen Sie hinter dem abfragbaren Objekt in der Projektion einen Aufruf von `ToList()` hinzu:</span><span class="sxs-lookup"><span data-stu-id="0bf7c-367">Add `ToList()` call after the Queryable object in the projection:</span></span>

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
