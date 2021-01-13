---
title: 'EF Core: Breaking Changes in EF Core 5.0'
description: Vollständige Liste der in Entity Framework Core 5.0 eingeführten Breaking Changes
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 7a13c9a6f6bd299991c379ec490480e1fbb4ba46
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635470"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="432b3-103">Breaking Changes in EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="432b3-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="432b3-104">Die folgenden API-Änderungen und Behavior Changes können dazu führen, dass vorhandene Anwendungen nach einem Upgrade auf EF Core 5.0.0 nicht mehr funktionieren.</span><span class="sxs-lookup"><span data-stu-id="432b3-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="432b3-105">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="432b3-105">Summary</span></span>

| <span data-ttu-id="432b3-106">**Wichtige Änderung**</span><span class="sxs-lookup"><span data-stu-id="432b3-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="432b3-107">**Auswirkungen**</span><span class="sxs-lookup"><span data-stu-id="432b3-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="432b3-108">Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität</span><span class="sxs-lookup"><span data-stu-id="432b3-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="432b3-109">Medium</span><span class="sxs-lookup"><span data-stu-id="432b3-109">Medium</span></span>     |
| [<span data-ttu-id="432b3-110">Die definierende Abfrage wurde durch anbieterspezifische Methoden ersetzt</span><span class="sxs-lookup"><span data-stu-id="432b3-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="432b3-111">Medium</span><span class="sxs-lookup"><span data-stu-id="432b3-111">Medium</span></span>     |
| [<span data-ttu-id="432b3-112">Navigationen ohne Nullverweis werden von Abfragen nicht überschrieben</span><span class="sxs-lookup"><span data-stu-id="432b3-112">Non-null reference navigations are not overwritten by queries</span></span>](#nonnullreferences)                                                   | <span data-ttu-id="432b3-113">Medium</span><span class="sxs-lookup"><span data-stu-id="432b3-113">Medium</span></span>     |
| [<span data-ttu-id="432b3-114">Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt</span><span class="sxs-lookup"><span data-stu-id="432b3-114">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="432b3-115">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-115">Low</span></span>        |
| [<span data-ttu-id="432b3-116">Cosmos: Der Partitionsschlüssel wird nun dem Primärschlüssel hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="432b3-116">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="432b3-117">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-117">Low</span></span>        |
| [<span data-ttu-id="432b3-118">Cosmos: Die `id`-Eigenschaft wurde umbenannt in `__id`</span><span class="sxs-lookup"><span data-stu-id="432b3-118">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="432b3-119">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-119">Low</span></span>        |
| <span data-ttu-id="432b3-120">[Cosmos: byte[] wird nun als Base64-Zeichenfolge statt als Zahlenarray gespeichert](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="432b3-120">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="432b3-121">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-121">Low</span></span>        |
| [<span data-ttu-id="432b3-122">Cosmos: GetPropertyName und SetPropertyName wurden umbenannt</span><span class="sxs-lookup"><span data-stu-id="432b3-122">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="432b3-123">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-123">Low</span></span>        |
| [<span data-ttu-id="432b3-124">Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird</span><span class="sxs-lookup"><span data-stu-id="432b3-124">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="432b3-125">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-125">Low</span></span>        |
| [<span data-ttu-id="432b3-126">IMigrationsModelDiffer verwendet jetzt IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="432b3-126">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="432b3-127">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-127">Low</span></span>        |
| [<span data-ttu-id="432b3-128">ToView() wird von Migrationen anders verarbeitet</span><span class="sxs-lookup"><span data-stu-id="432b3-128">ToView() is treated differently by migrations</span></span>](#toview)                                                                              | <span data-ttu-id="432b3-129">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-129">Low</span></span>        |
| [<span data-ttu-id="432b3-130">ToTable(null) markiert den Entitätstyp als nicht einer Tabelle zugeordnet</span><span class="sxs-lookup"><span data-stu-id="432b3-130">ToTable(null) marks the entity type as not mapped to a table</span></span>](#totable)                                                              | <span data-ttu-id="432b3-131">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-131">Low</span></span>        |
| [<span data-ttu-id="432b3-132">Diskriminatoren sind schreibgeschützt</span><span class="sxs-lookup"><span data-stu-id="432b3-132">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="432b3-133">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-133">Low</span></span>        |
| [<span data-ttu-id="432b3-134">Anbieterspezifische EF.Functions-Methoden werden für InMemory-Anbieter ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="432b3-134">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="432b3-135">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-135">Low</span></span>        |
| [<span data-ttu-id="432b3-136">IProperty.GetColumnName() gilt ab sofort als veraltet</span><span class="sxs-lookup"><span data-stu-id="432b3-136">IProperty.GetColumnName() is now obsolete</span></span>](#getcolumnname-obsolete)                                                                  | <span data-ttu-id="432b3-137">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-137">Low</span></span>        |
| [<span data-ttu-id="432b3-138">IndexBuilder.HasName ist mittlerweile veraltet</span><span class="sxs-lookup"><span data-stu-id="432b3-138">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="432b3-139">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-139">Low</span></span>        |
| [<span data-ttu-id="432b3-140">Ein Pluralizer ist nun für den Gerüstbau für Modelle mit Reverse Engineering enthalten</span><span class="sxs-lookup"><span data-stu-id="432b3-140">A pluralizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="432b3-141">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-141">Low</span></span>        |
| [<span data-ttu-id="432b3-142">INavigationBase ersetzt INavigation in einigen APIs zur Unterstützung überspringender Navigationen</span><span class="sxs-lookup"><span data-stu-id="432b3-142">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>](#inavigationbase)                                     | <span data-ttu-id="432b3-143">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-143">Low</span></span>        |
| [<span data-ttu-id="432b3-144">Einige Abfragen mit korrelierter Sammlung, die ebenfalls `Distinct` oder `GroupBy` verwenden, werden nicht mehr unterstützt</span><span class="sxs-lookup"><span data-stu-id="432b3-144">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>](#collection-distinct-groupby) | <span data-ttu-id="432b3-145">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-145">Low</span></span>        |
| [<span data-ttu-id="432b3-146">Die Verwendung einer Sammlung abfragbarer Typen in der Projektion wird nicht unterstützt</span><span class="sxs-lookup"><span data-stu-id="432b3-146">Using a collection of Queryable type in projection is not supported</span></span>](#queryable-projection)                                          | <span data-ttu-id="432b3-147">Niedrig</span><span class="sxs-lookup"><span data-stu-id="432b3-147">Low</span></span>        |

## <a name="medium-impact-changes"></a><span data-ttu-id="432b3-148">Änderungen mit mittlerer Auswirkung</span><span class="sxs-lookup"><span data-stu-id="432b3-148">Medium-impact changes</span></span>

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="432b3-149">Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität</span><span class="sxs-lookup"><span data-stu-id="432b3-149">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="432b3-150">Tracking Issue 17286</span><span class="sxs-lookup"><span data-stu-id="432b3-150">Tracking Issue #17286</span></span>](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-151">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-151">Old behavior</span></span>

<span data-ttu-id="432b3-152">Nur die Navigation zum Prinzipal konnte als erforderlich konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="432b3-152">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="432b3-153">Daher führte die Verwendung von `RequiredAttribute` für die Navigation zur abhängigen Entität (der Entität, die den Fremdschlüssel enthält) stattdessen zur Erstellung des Fremdschlüssels für den definierenden Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="432b3-153">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-154">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-154">New behavior</span></span>

<span data-ttu-id="432b3-155">Dank der hinzugefügten Unterstützung von erforderlichen abhängigen Entitäten ist es nun möglich, jede beliebige Verweisnavigation als erforderlich zu markieren. Dies bedeutet, dass im obigen Fall der Fremdschlüssel auf der anderen Seite der Beziehung definiert wird und die Eigenschaften nicht als erforderlich markiert werden.</span><span class="sxs-lookup"><span data-stu-id="432b3-155">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="432b3-156">Das Aufrufen von `IsRequired` vor der Angabe der abhängigen Entität ist nun mehrdeutig:</span><span class="sxs-lookup"><span data-stu-id="432b3-156">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a><span data-ttu-id="432b3-157">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-157">Why</span></span>

<span data-ttu-id="432b3-158">Das neue Verhalten ist erforderlich, um die Unterstützung von erforderlichen abhängigen Entitäten zu ermöglichen ([siehe 12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="432b3-158">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-159">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-159">Mitigations</span></span>

<span data-ttu-id="432b3-160">Entfernen Sie `RequiredAttribute` aus der Navigation zur abhängigen Entität, und verwenden Sie „RequiredAttribute“ stattdessen in der Navigation zum Prinzipal, oder konfigurieren Sie die Beziehung in `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="432b3-160">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="432b3-161">Die definierende Abfrage wurde durch anbieterspezifische Methoden ersetzt</span><span class="sxs-lookup"><span data-stu-id="432b3-161">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="432b3-162">Issue 18903</span><span class="sxs-lookup"><span data-stu-id="432b3-162">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-163">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-163">Old behavior</span></span>

<span data-ttu-id="432b3-164">Entitätstypen wurden zum Definieren von Abfragen auf der Kernebene zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="432b3-164">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="432b3-165">Jedes Mal, wenn der Entitätstyp im Abfragestamm des Entitätstyps verwendet wurde, wurde er bei allen Anbietern durch die definierende Abfrage ersetzt.</span><span class="sxs-lookup"><span data-stu-id="432b3-165">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-166">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-166">New behavior</span></span>

<span data-ttu-id="432b3-167">APIs für definierende Abfragen sind veraltet.</span><span class="sxs-lookup"><span data-stu-id="432b3-167">APIs for defining query are deprecated.</span></span> <span data-ttu-id="432b3-168">Es wurden neue anbieterspezifische APIs eingeführt.</span><span class="sxs-lookup"><span data-stu-id="432b3-168">New provider-specific APIs were introduced.</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-169">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-169">Why</span></span>

<span data-ttu-id="432b3-170">Während definierende Abfragen als Ersatzabfragen implementiert wurden, wenn in der Abfrage der Abfragestamm verwendet wird, sind einige Probleme aufgetreten:</span><span class="sxs-lookup"><span data-stu-id="432b3-170">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="432b3-171">Wenn eine definierende Abfrage einen Entitätstyp mit `new { ... }` in die `Select`-Methode projiziert, war für die Identifizierung dessen als Entität zusätzliche Arbeit erforderlich. Zudem sorgte dieses Vorgehen für Inkonsistenzen in Bezug darauf, wie EF Core nominale Typen in der Abfrage behandelt.</span><span class="sxs-lookup"><span data-stu-id="432b3-171">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="432b3-172">Bei relationalen Anbietern ist `FromSql` weiterhin erforderlich, um die SQL-Zeichenfolge im LINQ-Ausdrucksformat zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="432b3-172">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="432b3-173">Zunächst wurden definierende Abfragen als clientseitige Sichten zur Verwendung mit In-Memory-Anbietern bei schlüssellosen Entitäten eingeführt (ähnlich wie bei Datenbanksichten in relationalen Datenbanken).</span><span class="sxs-lookup"><span data-stu-id="432b3-173">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="432b3-174">Eine solche Definition erleichtert das Testen der Anwendung mit In-Memory-Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="432b3-174">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="432b3-175">Später wurden sie allgemein anwendbar, was zwar nützlich war, aber ein inkonsistentes und schwer verständliches Verhalten mit sich brachte.</span><span class="sxs-lookup"><span data-stu-id="432b3-175">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="432b3-176">Daher haben wir uns entschieden, das Konzept zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="432b3-176">So we decided to simplify the concept.</span></span> <span data-ttu-id="432b3-177">Wir stellen die LINQ-basierte definierende Abfrage nur noch für In-Memory-Anbieter bereit und behandeln sie anders.</span><span class="sxs-lookup"><span data-stu-id="432b3-177">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="432b3-178">Weitere Informationen finden Sie in [diesem Thema](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="432b3-178">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-179">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-179">Mitigations</span></span>

<span data-ttu-id="432b3-180">Verwenden Sie für relationale Anbieter die `ToSqlQuery`-Methode in `OnModelCreating`, und übergeben Sie eine SQL-Zeichenfolge zur Verwendung für den Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="432b3-180">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="432b3-181">Verwenden Sie für den In-Memory-Anbieter die `ToInMemoryQuery`-Methode in `OnModelCreating`, und übergeben Sie eine LINQ-Abfrage zur Verwendung für den Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="432b3-181">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a><span data-ttu-id="432b3-182">Navigationen ohne Nullverweis werden von Abfragen nicht überschrieben</span><span class="sxs-lookup"><span data-stu-id="432b3-182">Non-null reference navigations are not overwritten by queries</span></span>

[<span data-ttu-id="432b3-183">Nachverfolgung von Issue 2693</span><span class="sxs-lookup"><span data-stu-id="432b3-183">Tracking Issue #2693</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-184">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-184">Old behavior</span></span>

<span data-ttu-id="432b3-185">In EF Core 3.1 wurden Verweisnavigationen, die frühzeitig mit Werten ungleich NULL initialisiert wurden, mitunter von Entitätsinstanzen aus der Datenbank überschrieben, und zwar unabhängig davon, ob Schlüsselwerte übereinstimmten oder nicht.</span><span class="sxs-lookup"><span data-stu-id="432b3-185">In EF Core 3.1, reference navigations eagerly initialized to non-null values would sometimes be overwritten by entity instances from the database, regardless of whether or not key values matched.</span></span> <span data-ttu-id="432b3-186">In anderen Fällen hat EF Core 3.1 jedoch das Gegenteil bewirkt und den vorhandenen Wert ungleich NULL beibehalten.</span><span class="sxs-lookup"><span data-stu-id="432b3-186">However, in other cases, EF Core 3.1 would do the opposite and leave the existing non-null value.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-187">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-187">New behavior</span></span>

<span data-ttu-id="432b3-188">Ab EF Core 5.0 werden Verweisnavigationen ungleich NULL nicht mehr durch Instanzen überschrieben, die von einer Abfrage zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="432b3-188">Starting with EF Core 5.0, non-null reference navigations are never overwritten by instances returned from a query.</span></span>

<span data-ttu-id="432b3-189">Beachten Sie, dass die frühzeitige Initialisierung einer _Sammlungsnavigation_ als leere Sammlung nach wie vor unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="432b3-189">Note that eager initialization of a _collection_ navigation to an empty collection is still supported.</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-190">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-190">Why</span></span>

<span data-ttu-id="432b3-191">Die Initialisierung einer Verweisnavigationseigenschaft als „leere“ Entitätsinstanz führt zu einem uneindeutigen Zustand.</span><span class="sxs-lookup"><span data-stu-id="432b3-191">Initialization of a reference navigation property to an "empty" entity instance results in an ambiguous state.</span></span> <span data-ttu-id="432b3-192">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="432b3-192">For example:</span></span>

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

<span data-ttu-id="432b3-193">Normalerweise erstellt eine Abfrage von Blogs und Autoren zunächst `Blog`-Instanzen. Dann werden die entsprechenden `Author`-Instanzen auf Grundlage der aus der Datenbank zurückgegebenen Daten festgelegt.</span><span class="sxs-lookup"><span data-stu-id="432b3-193">Normally a query for Blogs and Authors will first create `Blog` instances and then set the appropriate `Author` instances based on the data returned from the database.</span></span> <span data-ttu-id="432b3-194">In diesem Fall ist jedoch jede `Blog.Author`-Eigenschaft bereits als leere `Author`-Instanz initialisiert.</span><span class="sxs-lookup"><span data-stu-id="432b3-194">However, in this case every `Blog.Author` property is already initialized to an empty `Author`.</span></span> <span data-ttu-id="432b3-195">Allerdings kann EF Core nicht wissen, dass diese Instanz „leer“ ist.</span><span class="sxs-lookup"><span data-stu-id="432b3-195">Except EF Core has no way to know that this instance is "empty".</span></span> <span data-ttu-id="432b3-196">Das Überschreiben dieser Instanz könnte also unbemerkt eine gültige `Author`-Instanz auslösen.</span><span class="sxs-lookup"><span data-stu-id="432b3-196">So overwriting this instance could potentially silently throw away a valid `Author`.</span></span> <span data-ttu-id="432b3-197">Daher überschreibt EF Core 5.0 jetzt konsequent keine Navigation, die bereits initialisiert ist.</span><span class="sxs-lookup"><span data-stu-id="432b3-197">Therefore, EF Core 5.0 now consistently does not overwrite a navigation that is already initialized.</span></span>

<span data-ttu-id="432b3-198">Dieses neue Verhalten stimmt in den meisten Fällen auch mit dem Verhalten von EF6 überein, obwohl wir bei Untersuchungen auch einige Fälle von Inkonsistenz in EF6 gefunden haben.</span><span class="sxs-lookup"><span data-stu-id="432b3-198">This new behavior also aligns with the behavior of EF6 in most cases, although upon investigation we also found some cases of inconsistency in EF6.</span></span>  

#### <a name="mitigations"></a><span data-ttu-id="432b3-199">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-199">Mitigations</span></span>

<span data-ttu-id="432b3-200">Wenn dieses Problem auftritt, besteht die Lösung darin, die vorzeitige Initialisierung der Verweisnavigationseigenschaften zu beenden.</span><span class="sxs-lookup"><span data-stu-id="432b3-200">If this break is encountered, then the fix is to stop eagerly initializing reference navigation properties.</span></span>

## <a name="low-impact-changes"></a><span data-ttu-id="432b3-201">Änderungen mit geringer Auswirkung</span><span class="sxs-lookup"><span data-stu-id="432b3-201">Low-impact changes</span></span>

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="432b3-202">Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt.</span><span class="sxs-lookup"><span data-stu-id="432b3-202">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="432b3-203">Issue 14257</span><span class="sxs-lookup"><span data-stu-id="432b3-203">Tracking Issue #14257</span></span>](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-204">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-204">Old behavior</span></span>

<span data-ttu-id="432b3-205">Die HasGeometricDimension-Methode wurde dazu verwendet, zusätzliche Maße (Z und M) für Geometriespalten zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="432b3-205">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="432b3-206">Dies hat sich jedoch immer nur auf die Datenbankerstellung ausgewirkt.</span><span class="sxs-lookup"><span data-stu-id="432b3-206">However, it only ever affected database creation.</span></span> <span data-ttu-id="432b3-207">Das Angeben dieser Methode zum Abfragen von Werten mit zusätzlichen Maßen war nicht notwendig.</span><span class="sxs-lookup"><span data-stu-id="432b3-207">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="432b3-208">Außerdem hat die Methode nicht ordnungsgemäß funktioniert, wenn Werte mit zusätzlichen Maßen eingefügt oder geändert wurden (siehe [Issue 14257](https://github.com/dotnet/efcore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="432b3-208">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/dotnet/efcore/issues/14257)).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-209">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-209">New behavior</span></span>

<span data-ttu-id="432b3-210">Zum Ermöglichen des Einfügens und Änderns von Geometriewerten mit zusätzlichen Maßen (Z und M) muss das jeweilige Maß als Teil des Spaltentypnamens angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="432b3-210">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="432b3-211">Diese API entspricht eher dem zugrunde liegenden Verhalten der AddGeometryColumn-Funktion von SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="432b3-211">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-212">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-212">Why</span></span>

<span data-ttu-id="432b3-213">Die Verwendung der HasGeometricDimension-Methode, nachdem das Maß im Spaltentyp festgelegt wurde, ist redundant und nicht notwendig. Daher wurde die gesamte HasGeometricDimension-Methode entfernt.</span><span class="sxs-lookup"><span data-stu-id="432b3-213">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-214">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-214">Mitigations</span></span>

<span data-ttu-id="432b3-215">Verwenden Sie `HasColumnType` zum Festlegen der Maße:</span><span class="sxs-lookup"><span data-stu-id="432b3-215">Use `HasColumnType` to specify the dimension:</span></span>

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

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="432b3-216">Cosmos: Der Partitionsschlüssel wird nun dem Primärschlüssel hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="432b3-216">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="432b3-217">Tracking Issue 15289</span><span class="sxs-lookup"><span data-stu-id="432b3-217">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-218">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-218">Old behavior</span></span>

<span data-ttu-id="432b3-219">Die Partitionsschlüsseleigenschaft wurde nur dem alternativen Schlüssel hinzugefügt, der `id` enthält.</span><span class="sxs-lookup"><span data-stu-id="432b3-219">The partition key property was only added to the alternate key that includes `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-220">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-220">New behavior</span></span>

<span data-ttu-id="432b3-221">Die Partitionsschlüsseleigenschaft wird gemäß Konvention jetzt auch dem Primärschlüssel hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="432b3-221">The partition key property is now also added to the primary key by convention.</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-222">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-222">Why</span></span>

<span data-ttu-id="432b3-223">Durch diese Änderung ist das Modell besser auf die Azure Cosmos DB-Semantik abgestimmt. Zudem verbessert sich dadurch die Leistung von `Find` und einigen Abfragen.</span><span class="sxs-lookup"><span data-stu-id="432b3-223">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-224">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-224">Mitigations</span></span>

<span data-ttu-id="432b3-225">Um zu verhindern, dass die Partitionsschlüsseleigenschaft dem Primärschlüssel hinzugefügt wird, konfigurieren Sie sie in `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="432b3-225">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="432b3-226">Cosmos: Die `id`-Eigenschaft wurde umbenannt in `__id`</span><span class="sxs-lookup"><span data-stu-id="432b3-226">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="432b3-227">Issue 17751</span><span class="sxs-lookup"><span data-stu-id="432b3-227">Tracking Issue #17751</span></span>](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-228">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-228">Old behavior</span></span>

<span data-ttu-id="432b3-229">Die der JSON-Eigenschaft `id` zugeordnete Schatteneigenschaft hatte ebenfalls den Namen `id`.</span><span class="sxs-lookup"><span data-stu-id="432b3-229">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-230">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-230">New behavior</span></span>

<span data-ttu-id="432b3-231">Die gemäß Konvention erstellte Schatteneigenschaft trägt nun den Namen `__id`.</span><span class="sxs-lookup"><span data-stu-id="432b3-231">The shadow property created by convention is now named `__id`.</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-232">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-232">Why</span></span>

<span data-ttu-id="432b3-233">Durch diese Änderung ist es weniger wahrscheinlich, dass die Eigenschaft `id` mit einer vorhandenen Eigenschaft des Entitätstyps kollidiert.</span><span class="sxs-lookup"><span data-stu-id="432b3-233">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-234">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-234">Mitigations</span></span>

<span data-ttu-id="432b3-235">Wenn Sie zum Verhalten der Version 3.x zurückkehren möchten, konfigurieren Sie die Eigenschaft `id` in `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="432b3-235">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="432b3-236">Cosmos: byte[] wird nun als Base64-Zeichenfolge statt als Zahlenarray gespeichert</span><span class="sxs-lookup"><span data-stu-id="432b3-236">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="432b3-237">Issue 17306</span><span class="sxs-lookup"><span data-stu-id="432b3-237">Tracking Issue #17306</span></span>](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-238">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-238">Old behavior</span></span>

<span data-ttu-id="432b3-239">Eigenschaften vom Typ „byte[]“ wurden als Zahlenarray gespeichert.</span><span class="sxs-lookup"><span data-stu-id="432b3-239">Properties of type byte[] were stored as a number array.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-240">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-240">New behavior</span></span>

<span data-ttu-id="432b3-241">Eigenschaften vom Typ „byte[]“ werden nun als Base64-Zeichenfolge gespeichert.</span><span class="sxs-lookup"><span data-stu-id="432b3-241">Properties of type byte[] are now stored as a base64 string.</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-242">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-242">Why</span></span>

<span data-ttu-id="432b3-243">Diese Darstellung von „byte[]“ entspricht eher den Erwartungen. Zudem handelt es sich hier um das Standardverhalten der wichtigen JSON-Serialisierungsbibliotheken.</span><span class="sxs-lookup"><span data-stu-id="432b3-243">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-244">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-244">Mitigations</span></span>

<span data-ttu-id="432b3-245">Als Zahlenarrays gespeicherte, bereits vorhandene Daten werden weiterhin ordnungsgemäß abgefragt. Derzeit wird jedoch keine Möglichkeit unterstützt, das Einfügeverhalten zu ändern.</span><span class="sxs-lookup"><span data-stu-id="432b3-245">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="432b3-246">Wenn Ihr Szenario durch diese Einschränkung blockiert wird, kommentieren Sie dieses [Problem](https://github.com/dotnet/efcore/issues/17306).</span><span class="sxs-lookup"><span data-stu-id="432b3-246">If this limitation is blocking your scenario, comment on [this issue](https://github.com/dotnet/efcore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="432b3-247">Cosmos: GetPropertyName und SetPropertyName wurden umbenannt</span><span class="sxs-lookup"><span data-stu-id="432b3-247">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="432b3-248">Issue 17874</span><span class="sxs-lookup"><span data-stu-id="432b3-248">Tracking Issue #17874</span></span>](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-249">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-249">Old behavior</span></span>

<span data-ttu-id="432b3-250">Bisher wurden die Erweiterungsmethoden `GetPropertyName` und `SetPropertyName` genannt.</span><span class="sxs-lookup"><span data-stu-id="432b3-250">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-251">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-251">New behavior</span></span>

<span data-ttu-id="432b3-252">Die alte API wurde entfernt, und es wurden die neuen Methoden `GetJsonPropertyName` und `SetJsonPropertyName` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="432b3-252">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-253">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-253">Why</span></span>

<span data-ttu-id="432b3-254">Durch diese Änderung wird die Mehrdeutigkeit um die Konfiguration durch diese Methoden beseitigt.</span><span class="sxs-lookup"><span data-stu-id="432b3-254">This change removes the ambiguity around what these methods are configuring.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-255">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-255">Mitigations</span></span>

<span data-ttu-id="432b3-256">Verwenden Sie die neue API.</span><span class="sxs-lookup"><span data-stu-id="432b3-256">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="432b3-257">Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird</span><span class="sxs-lookup"><span data-stu-id="432b3-257">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="432b3-258">Tracking Issue 15289</span><span class="sxs-lookup"><span data-stu-id="432b3-258">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-259">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-259">Old behavior</span></span>

<span data-ttu-id="432b3-260">Wert-Generatoren wurden nur aufgerufen, wenn der Entitätszustand in „Hinzugefügt“ geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="432b3-260">Value generators were only called when the entity state changed to Added.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-261">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-261">New behavior</span></span>

<span data-ttu-id="432b3-262">Wert-Generatoren werden jetzt aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird und die Eigenschaft die Standardwerte enthält.</span><span class="sxs-lookup"><span data-stu-id="432b3-262">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-263">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-263">Why</span></span>

<span data-ttu-id="432b3-264">Diese Änderung war erforderlich, um die Verwendung von Eigenschaften zu verbessern, die nicht dauerhaft im Datenspeicher gespeichert sind und deren Wert immer auf dem Client generiert wird.</span><span class="sxs-lookup"><span data-stu-id="432b3-264">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-265">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-265">Mitigations</span></span>

<span data-ttu-id="432b3-266">Weisen Sie der Eigenschaft vor der Änderung des Status einen anderen Wert als den Standardwert zu, um zu verhindern, dass der Wert-Generator aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="432b3-266">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="432b3-267">IMigrationsModelDiffer verwendet jetzt IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="432b3-267">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="432b3-268">Tracking Issue 20305</span><span class="sxs-lookup"><span data-stu-id="432b3-268">Tracking Issue #20305</span></span>](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-269">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-269">Old behavior</span></span>

<span data-ttu-id="432b3-270">Die `IMigrationsModelDiffer`-API wurde mithilfe von `IModel` definiert.</span><span class="sxs-lookup"><span data-stu-id="432b3-270">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-271">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-271">New behavior</span></span>

<span data-ttu-id="432b3-272">Die `IMigrationsModelDiffer`-API verwendet jetzt `IRelationalModel`.</span><span class="sxs-lookup"><span data-stu-id="432b3-272">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="432b3-273">Die Modellmomentaufnahme enthält jedoch weiterhin nur `IModel`, da dieser Code Teil der Anwendung ist und Entity Framework ihn nicht ohne einen größeren Breaking Change ändern kann.</span><span class="sxs-lookup"><span data-stu-id="432b3-273">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-274">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-274">Why</span></span>

<span data-ttu-id="432b3-275">`IRelationalModel` ist eine neu hinzugefügte Darstellung des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="432b3-275">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="432b3-276">Die Verwendung dieser Darstellung zum Ermitteln von Unterschieden ist schneller und präziser.</span><span class="sxs-lookup"><span data-stu-id="432b3-276">Using it to find differences is faster and more accurate.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-277">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-277">Mitigations</span></span>

<span data-ttu-id="432b3-278">Verwenden Sie den folgenden Code, um das Modell von `snapshot` mit dem Modell von `context` zu vergleichen:</span><span class="sxs-lookup"><span data-stu-id="432b3-278">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="432b3-279">Eine Verbesserung dieser Funktion in Version 6.0 ist geplant ([siehe 22031](https://github.com/dotnet/efcore/issues/22031)).</span><span class="sxs-lookup"><span data-stu-id="432b3-279">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="toview"></a>

### <a name="toview-is-treated-differently-by-migrations"></a><span data-ttu-id="432b3-280">ToView() wird von Migrationen anders verarbeitet</span><span class="sxs-lookup"><span data-stu-id="432b3-280">ToView() is treated differently by migrations</span></span>

[<span data-ttu-id="432b3-281">Nachverfolgung von Issue #2725</span><span class="sxs-lookup"><span data-stu-id="432b3-281">Tracking Issue #2725</span></span>](https://github.com/dotnet/efcore/issues/2725)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-282">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-282">Old behavior</span></span>

<span data-ttu-id="432b3-283">Das Aufrufen von `ToView(string)` hat dazu geführt, dass Migrationen den Entitätstyp ignorieren und ihn einer Ansicht zuordnen.</span><span class="sxs-lookup"><span data-stu-id="432b3-283">Calling `ToView(string)` made the migrations ignore the entity type in addition to mapping it to a view.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-284">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-284">New behavior</span></span>

<span data-ttu-id="432b3-285">`ToView(string)` markiert den Entitätstyp nun als nicht einer Tabelle zugeordnet und ordnet ihn einer Ansicht zu.</span><span class="sxs-lookup"><span data-stu-id="432b3-285">Now `ToView(string)` marks the entity type as not mapped to a table in addition to mapping it to a view.</span></span> <span data-ttu-id="432b3-286">Dies führt dazu, dass bei der ersten Migration nach einem Upgrade zu EF Core 5 versucht wird, die Standardtabelle für diesen Entitätstyp zu löschen, da er nicht mehr ignoriert wird.</span><span class="sxs-lookup"><span data-stu-id="432b3-286">This results in the first migration after upgrading to EF Core 5 to try to drop the default table for this entity type as it's not longer ignored.</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-287">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-287">Why</span></span>

<span data-ttu-id="432b3-288">EF Core ermöglicht es nun, dass ein Entitätstyp gleichzeitig sowohl einer Tabelle als auch einer Ansicht zugeordnet werden kann. `ToView` ist also kein gültiger Indikator dafür mehr, dass der Typ von Migrationen ignoriert werden soll.</span><span class="sxs-lookup"><span data-stu-id="432b3-288">EF Core now allows an entity type to be mapped to both a table and a view simultaneously, so `ToView` is no longer a valid indicator that it should be ignored by migrations.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-289">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-289">Mitigations</span></span>

<span data-ttu-id="432b3-290">Verwenden Sie den folgenden Code, um die zugeordnete Tabelle als von Migrationen ausgeschlossen zu markieren:</span><span class="sxs-lookup"><span data-stu-id="432b3-290">Use the following code to mark the mapped table as excluded from migrations:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().ToTable("UserView", t => t.ExcludeFromMigrations());
}
```

<a name="totable"></a>

### <a name="totablenull-marks-the-entity-type-as-not-mapped-to-a-table"></a><span data-ttu-id="432b3-291">ToTable(null) markiert den Entitätstyp als nicht einer Tabelle zugeordnet</span><span class="sxs-lookup"><span data-stu-id="432b3-291">ToTable(null) marks the entity type as not mapped to a table</span></span>

[<span data-ttu-id="432b3-292">Nachverfolgung von Issue #21172</span><span class="sxs-lookup"><span data-stu-id="432b3-292">Tracking Issue #21172</span></span>](https://github.com/dotnet/efcore/issues/21172)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-293">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-293">Old behavior</span></span>

<span data-ttu-id="432b3-294">`ToTable(null)` hat den Tabellennamen auf den Standardwert zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="432b3-294">`ToTable(null)` would reset the table name to the default.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-295">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-295">New behavior</span></span>

<span data-ttu-id="432b3-296">`ToTable(null)` markiert den Entitätstyp nun als nicht einer Tabelle zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="432b3-296">`ToTable(null)` now marks the entity type as not mapped to any table.</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-297">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-297">Why</span></span>

<span data-ttu-id="432b3-298">EF Core ermöglicht es nun, dass ein Entitätstyp gleichzeitig sowohl einer Tabelle als auch einer Ansicht zugeordnet werden kann. `ToTable(null)` wird also verwendet, um anzugeben, dass der Typ keiner Tabelle zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="432b3-298">EF Core now allows an entity type to be mapped to both a table and a view simultaneously, so `ToTable(null)` is used to indicate that it isn't mapped to any table.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-299">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-299">Mitigations</span></span>

<span data-ttu-id="432b3-300">Verwenden Sie den folgenden Code, um den Tabellennamen in den Standardwert zurückzusetzen, wenn keine Zuordnung zu einer Ansicht oder einer DbFunction-Klasse vorliegt:</span><span class="sxs-lookup"><span data-stu-id="432b3-300">Use the following code to reset the table name to the default if it's not mapped to a view or a DbFunction:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().Metadata.RemoveAnnotation(RelationalAnnotationNames.TableName);
}
```

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="432b3-301">Diskriminatoren sind schreibgeschützt</span><span class="sxs-lookup"><span data-stu-id="432b3-301">Discriminators are read-only</span></span>

[<span data-ttu-id="432b3-302">Tracking Issue 21154</span><span class="sxs-lookup"><span data-stu-id="432b3-302">Tracking Issue #21154</span></span>](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-303">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-303">Old behavior</span></span>

<span data-ttu-id="432b3-304">Der Diskriminatorwert konnte vor dem Aufrufen von `SaveChanges` geändert werden.</span><span class="sxs-lookup"><span data-stu-id="432b3-304">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-305">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-305">New behavior</span></span>

<span data-ttu-id="432b3-306">Im obigen Fall wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="432b3-306">An exception will be throws in the above case.</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-307">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-307">Why</span></span>

<span data-ttu-id="432b3-308">EF erwartet nicht, dass der Entitätstyp geändert wird, während er noch nachverfolgt wird, sodass das Ändern des Diskriminatorwerts zu einem inkonsistenten Kontext führt. Dies hat möglicherweise ein unerwartetes Verhalten zur Folge.</span><span class="sxs-lookup"><span data-stu-id="432b3-308">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-309">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-309">Mitigations</span></span>

<span data-ttu-id="432b3-310">Wenn eine Änderung des Diskriminatorwerts erforderlich ist und der Kontext direkt nach dem Aufruf von `SaveChanges` verworfen wird, kann der Diskriminator als änderbar festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="432b3-310">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="432b3-311">Anbieterspezifische EF.Functions-Methoden werden für InMemory-Anbieter ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="432b3-311">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="432b3-312">Issue 20294</span><span class="sxs-lookup"><span data-stu-id="432b3-312">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-313">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-313">Old behavior</span></span>

<span data-ttu-id="432b3-314">Anbieterspezifische EF.Functions-Methoden enthielten eine Implementierung für die Clientausführung, sodass sie für den InMemory-Anbieter ausgeführt werden konnten.</span><span class="sxs-lookup"><span data-stu-id="432b3-314">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="432b3-315">`EF.Functions.DateDiffDay` ist beispielsweise eine SQL Server-spezifische Methode, die für InMemory-Anbieter galt.</span><span class="sxs-lookup"><span data-stu-id="432b3-315">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-316">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-316">New behavior</span></span>

<span data-ttu-id="432b3-317">Anbieterspezifische Methoden wurden aktualisiert, sodass sie im Methodentext eine Ausnahme auslösen, um ihre Auswertung auf Clientseite zu blockieren.</span><span class="sxs-lookup"><span data-stu-id="432b3-317">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-318">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-318">Why</span></span>

<span data-ttu-id="432b3-319">Anbieterspezifische Methoden sind einer Datenbankfunktion zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="432b3-319">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="432b3-320">Das Berechnungsergebnis der zugeordneten Datenbankfunktion kann auf Clientseite in LINQ nicht immer repliziert werden.</span><span class="sxs-lookup"><span data-stu-id="432b3-320">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="432b3-321">Dies kann dazu führen, dass sich das Ergebnis von dem des Servers unterscheidet, wenn dieselbe Methode auf dem Client ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="432b3-321">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="432b3-322">Da diese Methoden in LINQ verwendet werden, um Übersetzungen in bestimmte Datenbankfunktionen durchzuführen, müssen sie nicht auf Clientseite ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="432b3-322">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="432b3-323">Da der InMemory-Anbieter eine andere *Datenbank* ist, sind die Methoden für diesen Anbieter nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="432b3-323">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="432b3-324">Wenn Sie versuchen, sie für den InMemory-Anbieter oder einen anderen Anbieter auszuführen, der diese Methoden nicht übersetzt, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="432b3-324">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-325">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-325">Mitigations</span></span>

<span data-ttu-id="432b3-326">Da es keine Möglichkeit gibt, das Verhalten von Datenbankfunktionen exakt zu imitieren, sollten Sie die Abfragen, die diese Methoden enthalten, mit demselben Datenbanktyp testen, der auch in der Produktionsumgebung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="432b3-326">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="getcolumnname-obsolete"></a>

### <a name="ipropertygetcolumnname-is-now-obsolete"></a><span data-ttu-id="432b3-327">IProperty.GetColumnName() gilt ab sofort als veraltet</span><span class="sxs-lookup"><span data-stu-id="432b3-327">IProperty.GetColumnName() is now obsolete</span></span>

[<span data-ttu-id="432b3-328">Nachverfolgung von Issue #2266</span><span class="sxs-lookup"><span data-stu-id="432b3-328">Tracking Issue #2266</span></span>](https://github.com/dotnet/efcore/issues/2266)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-329">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-329">Old behavior</span></span>

<span data-ttu-id="432b3-330">`GetColumnName()` hat den Namen der Spalte zurückgegeben, der die Eigenschaft zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="432b3-330">`GetColumnName()` returned the name of the column that a property is mapped to.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-331">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-331">New behavior</span></span>

<span data-ttu-id="432b3-332">`GetColumnName()` gibt weiterhin den Namen der Spalte zurück, der eine Eigenschaft zugeordnet ist. Dieses Verhalten ist nun jedoch mehrdeutig, da EF Core 5 die TPT-Methode (Tabelle pro Typ) und gleichzeitiges Zuordnen zu einer Ansicht oder einer Funktion unterstützt, wenn diese Zuordnungen verschiedene Spaltennamen für dieselbe Eigenschaft verwenden könnten.</span><span class="sxs-lookup"><span data-stu-id="432b3-332">`GetColumnName()` still returns the name of a column that a property is mapped to, but this behavior is now ambiguous since EF Core 5 supports TPT and simultaneous mapping to a view or a function where these mappings could use different column names for the same property.</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-333">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-333">Why</span></span>

<span data-ttu-id="432b3-334">Diese Methode wurde als veraltet gekennzeichnet, um Benutzern nahezulegen, eine exaktere Überladung zu verwenden: <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)>.</span><span class="sxs-lookup"><span data-stu-id="432b3-334">We marked this method as obsolete to guide users to a more accurate overload - <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)>.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-335">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-335">Mitigations</span></span>

<span data-ttu-id="432b3-336">Verwenden Sie den folgenden Code, um den Spaltennamen für eine bestimmte Tabelle abzurufen:</span><span class="sxs-lookup"><span data-stu-id="432b3-336">Use the following code to get the column name for a specific table:</span></span>

```csharp
var columnName = property.GetColumnName(StoreObjectIdentifier.Table("Users", null)));
```

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="432b3-337">IndexBuilder.HasName ist mittlerweile veraltet</span><span class="sxs-lookup"><span data-stu-id="432b3-337">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="432b3-338">Nachverfolgung von Issue 21089</span><span class="sxs-lookup"><span data-stu-id="432b3-338">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-339">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-339">Old behavior</span></span>

<span data-ttu-id="432b3-340">Bisher konnte nur ein Index für eine bestimmte Gruppe von Eigenschaften definiert werden.</span><span class="sxs-lookup"><span data-stu-id="432b3-340">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="432b3-341">Der Datenbankname eines Indexes wurde mithilfe von IndexBuilder.HasName konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="432b3-341">The database name of an index was configured using IndexBuilder.HasName.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-342">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-342">New behavior</span></span>

<span data-ttu-id="432b3-343">Für dieselbe Gruppe oder dieselben Eigenschaften sind jetzt mehrere Indizes zulässig.</span><span class="sxs-lookup"><span data-stu-id="432b3-343">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="432b3-344">Diese Indizes unterscheiden sich nun durch einen Namen im Modell.</span><span class="sxs-lookup"><span data-stu-id="432b3-344">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="432b3-345">Gemäß der Konvention wird der Modellname als Datenbankname verwendet. Er kann jedoch auch unabhängig mit HasDatabaseName konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="432b3-345">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-346">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-346">Why</span></span>

<span data-ttu-id="432b3-347">In Zukunft möchten wir sowohl aufsteigende als auch absteigende Indizes oder Indizes mit unterschiedlichen Sortierungen für denselben Satz von Eigenschaften aktivieren.</span><span class="sxs-lookup"><span data-stu-id="432b3-347">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="432b3-348">Diese Änderung ist ein weiterer Schritt in diese Richtung.</span><span class="sxs-lookup"><span data-stu-id="432b3-348">This change moves us another step in that direction.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-349">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-349">Mitigations</span></span>

<span data-ttu-id="432b3-350">Sämtlicher Code, der zuvor IndexBuilder.HasName aufgerufen hat, sollte so aktualisiert werden, dass stattdessen HasDatabaseName aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="432b3-350">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="432b3-351">Wenn das Projekt Migrationen enthält, die vor EF Core Version 2.0.0 generiert wurden, können Sie die Warnung in diesen Dateien problemlos ignorieren und unterdrücken, indem Sie `#pragma warning disable 612, 618` hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="432b3-351">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="432b3-352">Ein Pluralizer ist nun für den Gerüstbau für Modelle mit Reverse Engineering enthalten</span><span class="sxs-lookup"><span data-stu-id="432b3-352">A pluralizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="432b3-353">Nachverfolgung von Issue 11160</span><span class="sxs-lookup"><span data-stu-id="432b3-353">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-354">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-354">Old behavior</span></span>

<span data-ttu-id="432b3-355">Bislang mussten Sie beim Gerüstbau für einen Datenbankkontext und Entitätstypen durch Reverse Engineering eines Datenbankschemas ein separates Pluralizer-Paket installieren, um DbSet- und Sammlungsnavigationsnamen in die Pluralform und Tabellennamen in die Singularform umzuwandeln.</span><span class="sxs-lookup"><span data-stu-id="432b3-355">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffolding a DbContext and entity types by reverse engineering a database schema.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-356">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-356">New behavior</span></span>

<span data-ttu-id="432b3-357">EF Core enthält jetzt einen Pluarlizer, der die [Humanizer](https://github.com/Humanizr/Humanizer)-Bibliothek verwendet.</span><span class="sxs-lookup"><span data-stu-id="432b3-357">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="432b3-358">Dies ist dieselbe Bibliothek, die Visual Studio verwendet, um Variablennamen zu empfehlen.</span><span class="sxs-lookup"><span data-stu-id="432b3-358">This is the same library Visual Studio uses to recommend variable names.</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-359">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-359">Why</span></span>

<span data-ttu-id="432b3-360">Die Verwendung von Pluralformen von Wörtern für Sammlungseigenschaften und Singularformen für Typen und Verweiseigenschaften ist in .NET idiomatisch.</span><span class="sxs-lookup"><span data-stu-id="432b3-360">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-361">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-361">Mitigations</span></span>

<span data-ttu-id="432b3-362">Um den Pluralizer zu deaktivieren, verwenden Sie die Option `--no-pluralize` auf `dotnet ef dbcontext scaffold` oder den `-NoPluralize`-Switch auf `Scaffold-DbContext`.</span><span class="sxs-lookup"><span data-stu-id="432b3-362">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a><span data-ttu-id="432b3-363">INavigationBase ersetzt INavigation in einigen APIs zur Unterstützung überspringender Navigationen</span><span class="sxs-lookup"><span data-stu-id="432b3-363">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>

[<span data-ttu-id="432b3-364">Nachverfolgung von Issue 2568</span><span class="sxs-lookup"><span data-stu-id="432b3-364">Tracking Issue #2568</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a><span data-ttu-id="432b3-365">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-365">Old behavior</span></span>

<span data-ttu-id="432b3-366">Bis Version 5.0 unterstützte EF Core nur eine Form der Navigationseigenschaft, die durch die `INavigation`-Schnittstelle dargestellt wird.</span><span class="sxs-lookup"><span data-stu-id="432b3-366">EF Core prior to 5.0 supported only one form of navigation property, represented by the `INavigation` interface.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="432b3-367">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="432b3-367">New behavior</span></span>

<span data-ttu-id="432b3-368">EF Core 5.0 führt m:n-Beziehungen ein, die „überspringende Navigationen“ verwenden.</span><span class="sxs-lookup"><span data-stu-id="432b3-368">EF Core 5.0 introduces many-to-many relationships which use "skip navigations".</span></span> <span data-ttu-id="432b3-369">Diese werden durch die `ISkipNavigation`-Schnittstelle dargestellt, und der größte Teil der Funktionalität von `INavigation` wurde auf die gemeinsame Basisschnittstelle `INavigationBase` verlagert.</span><span class="sxs-lookup"><span data-stu-id="432b3-369">These are represented by the `ISkipNavigation` interface, and most of the functionality of `INavigation` has been pushed down to a common base interface: `INavigationBase`.</span></span>

#### <a name="why"></a><span data-ttu-id="432b3-370">Warum?</span><span class="sxs-lookup"><span data-stu-id="432b3-370">Why</span></span>

<span data-ttu-id="432b3-371">Die meisten Funktionen sind bei normalen und überspringenden Navigationen identisch.</span><span class="sxs-lookup"><span data-stu-id="432b3-371">Most of the functionality between normal and skip navigations is the same.</span></span> <span data-ttu-id="432b3-372">Allerdings haben überspringende Navigationen eine andere Beziehung zu Fremdschlüsseln als normale Navigationen, da sich die beteiligten Fremdschlüssel nicht direkt an beiden Enden der Beziehung befinden, sondern in der JOIN-Entität.</span><span class="sxs-lookup"><span data-stu-id="432b3-372">However, skip navigations have a different relationship to foreign keys than normal navigations, since the FKs involved are not directly on either end of the relationship, but rather in the join entity.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="432b3-373">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="432b3-373">Mitigations</span></span>

<span data-ttu-id="432b3-374">In vielen Fällen können Anwendungen ohne weitere Änderungen zur Verwendung der neuen Basisschnittstelle übergehen.</span><span class="sxs-lookup"><span data-stu-id="432b3-374">In many cases applications can switch to using the new base interface with no other changes.</span></span> <span data-ttu-id="432b3-375">In Fällen jedoch, in denen die Navigation für den Zugriff auf Fremdschlüsseleigenschaften verwendet wird, sollte der Code der Anwendung entweder nur auf normale Navigationen beschränkt oder so aktualisiert werden, dass er sowohl bei normalen als auch bei überspringenden Navigationen das Richtige tut.</span><span class="sxs-lookup"><span data-stu-id="432b3-375">However, in cases where the navigation is used to access foreign key properties, application code should either be constrained to only normal navigations, or updated to do the appropriate thing for both normal and skip navigations.</span></span>

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a><span data-ttu-id="432b3-376">Einige Abfragen mit korrelierter Sammlung, die ebenfalls `Distinct` oder`GroupBy` verwenden, werden nicht mehr unterstützt</span><span class="sxs-lookup"><span data-stu-id="432b3-376">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>

[<span data-ttu-id="432b3-377">Nachverfolgung von Issue 15873</span><span class="sxs-lookup"><span data-stu-id="432b3-377">Tracking Issue #15873</span></span>](https://github.com/dotnet/efcore/issues/15873)

<span data-ttu-id="432b3-378">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="432b3-378">**Old behavior**</span></span>

<span data-ttu-id="432b3-379">Zuvor haben wir die Ausführung von Abfragen mit korrelierten Sammlungen, gefolgt von `GroupBy`, sowie einige Abfragen mit `Distinct` erlaubt.</span><span class="sxs-lookup"><span data-stu-id="432b3-379">Previously, queries involving correlated collections followed by `GroupBy`, as well as some queries using `Distinct` we allowed to execute.</span></span>

<span data-ttu-id="432b3-380">Beispiel für GroupBy:</span><span class="sxs-lookup"><span data-stu-id="432b3-380">GroupBy example:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

<span data-ttu-id="432b3-381">Beispiel für `Distinct`: insbesondere `Distinct`-Abfragen, bei denen die innere Sammlungsprojektion nicht den Primärschlüssel enthält:</span><span class="sxs-lookup"><span data-stu-id="432b3-381">`Distinct` example - specifically `Distinct` queries where inner collection projection doesn't contain the primary key:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

<span data-ttu-id="432b3-382">Diese Abfragen konnten falsche Ergebnisse liefern, wenn die innere Sammlung Duplikate enthielt, funktionierten aber einwandfrei, wenn alle Elemente in der inneren Sammlung eindeutig waren.</span><span class="sxs-lookup"><span data-stu-id="432b3-382">These queries could return incorrect results if the inner collection contained any duplicates, but worked correctly if all the elements in the inner collection were unique.</span></span>

<span data-ttu-id="432b3-383">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="432b3-383">**New behavior**</span></span>

<span data-ttu-id="432b3-384">Diese Abfragen werden nicht mehr unterstützt.</span><span class="sxs-lookup"><span data-stu-id="432b3-384">These queries are no longer supported.</span></span> <span data-ttu-id="432b3-385">Es wird eine Ausnahme ausgelöst, die darauf hinweist, dass wir nicht genügend Informationen haben, um die Ergebnisse ordnungsgemäß zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="432b3-385">Exception is thrown indicating that we don't have enough information to correctly build the results.</span></span>

<span data-ttu-id="432b3-386">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="432b3-386">**Why**</span></span>

<span data-ttu-id="432b3-387">Für Szenarien mit korrelierten Sammlungen müssen wir den Primärschlüssel der Entitäten kennen, um die Sammlungsentitäten dem richtigen übergeordneten Element zuweisen zu können.</span><span class="sxs-lookup"><span data-stu-id="432b3-387">For correlated collection scenarios we need to know entity's primary key in order to assign collection entities to the correct parent.</span></span> <span data-ttu-id="432b3-388">Wenn die innere Sammlung `GroupBy` oder `Distinct` nicht verwendet, kann der fehlende Primärschlüssel einfach zur Projektion hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="432b3-388">When inner collection doesn't use `GroupBy` or `Distinct`, the missing primary key can simply be added to the projection.</span></span> <span data-ttu-id="432b3-389">Im Falle von `GroupBy` und `Distinct` ist dies jedoch nicht möglich, weil es das Ergebnis des Vorgangs `GroupBy` oder `Distinct` ändern würde.</span><span class="sxs-lookup"><span data-stu-id="432b3-389">However in case of `GroupBy` and `Distinct` it can't be done because it would change the result of `GroupBy` or `Distinct` operation.</span></span>

<span data-ttu-id="432b3-390">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="432b3-390">**Mitigations**</span></span>

<span data-ttu-id="432b3-391">Schreiben Sie die Abfrage so um, dass die Vorgänge `GroupBy` oder `Distinct` nicht auf die innere Sammlung angewendet werden. Führen Sie diese Vorgänge stattdessen auf dem Client aus.</span><span class="sxs-lookup"><span data-stu-id="432b3-391">Rewrite the query to not use `GroupBy` or `Distinct` operations on the inner collection, and perform these operations on the client instead.</span></span>

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

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a><span data-ttu-id="432b3-392">Die Verwendung einer Sammlung abfragbarer Typen in der Projektion wird nicht unterstützt</span><span class="sxs-lookup"><span data-stu-id="432b3-392">Using a collection of Queryable type in projection is not supported</span></span>

[<span data-ttu-id="432b3-393">Nachverfolgung von Issue 16314</span><span class="sxs-lookup"><span data-stu-id="432b3-393">Tracking Issue #16314</span></span>](https://github.com/dotnet/efcore/issues/16314)

<span data-ttu-id="432b3-394">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="432b3-394">**Old behavior**</span></span>

<span data-ttu-id="432b3-395">Bislang war es in einigen Fällen möglich, eine Sammlung eines abfragbaren Typs innerhalb der Projektion zu verwenden, z. B. als Argument für einen `List<T>`-Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="432b3-395">Previously, it was possible to use collection of a Queryable type inside the projection in some cases, for example as an argument to a `List<T>` constructor:</span></span>

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

<span data-ttu-id="432b3-396">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="432b3-396">**New behavior**</span></span>

<span data-ttu-id="432b3-397">Diese Abfragen werden nicht mehr unterstützt.</span><span class="sxs-lookup"><span data-stu-id="432b3-397">These queries are no longer supported.</span></span> <span data-ttu-id="432b3-398">Eine Ausnahme wird ausgelöst, die darauf hinweist, dass wir kein Objekt des abfragbaren Typs erstellen können, und vorschlägt, wie dies korrigiert werden könnte.</span><span class="sxs-lookup"><span data-stu-id="432b3-398">Exception is thrown indicating that we can't create an object of Queryable type and suggesting how this could be fixed.</span></span>

<span data-ttu-id="432b3-399">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="432b3-399">**Why**</span></span>

<span data-ttu-id="432b3-400">Wir können ein Objekt eines abfragbaren Typs nicht materialisieren. Daher würde es stattdessen automatisch mit dem Typ `List<T>` erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="432b3-400">We can't materialize an object of a Queryable type, so they would automatically be created using `List<T>` type instead.</span></span> <span data-ttu-id="432b3-401">Dies führte oft zu einer Ausnahme aufgrund nicht übereinstimmender Typen, was nicht sehr klar wurde und für einige Benutzer überraschend sein konnte.</span><span class="sxs-lookup"><span data-stu-id="432b3-401">This would often cause an exception due to type mismatch which was not very clear and could be surprising to some users.</span></span> <span data-ttu-id="432b3-402">Wir haben uns entschieden, das Muster zu erkennen und eine aussagekräftigere Ausnahme auszulösen.</span><span class="sxs-lookup"><span data-stu-id="432b3-402">We decided to recognize the pattern and throw a more meaningful exception.</span></span>

<span data-ttu-id="432b3-403">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="432b3-403">**Mitigations**</span></span>

<span data-ttu-id="432b3-404">Fügen Sie hinter dem abfragbaren Objekt in der Projektion einen Aufruf von `ToList()` hinzu:</span><span class="sxs-lookup"><span data-stu-id="432b3-404">Add `ToList()` call after the Queryable object in the projection:</span></span>

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
