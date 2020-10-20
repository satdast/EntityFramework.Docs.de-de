---
title: 'EF Core: Breaking Changes in EF Core 5.0'
description: Vollständige Liste der in Entity Framework Core 5.0 eingeführten Breaking Changes
author: bricelam
ms.date: 09/24/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: e64f2b387d236e96d0451f3d55b3241daaba32d8
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065640"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="136a6-103">Breaking Changes in EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="136a6-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="136a6-104">Die folgenden API-Änderungen und Behavior Changes können dazu führen, dass vorhandene Anwendungen nach einem Upgrade auf EF Core 5.0.0 nicht mehr funktionieren.</span><span class="sxs-lookup"><span data-stu-id="136a6-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="136a6-105">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="136a6-105">Summary</span></span>

| <span data-ttu-id="136a6-106">**Wichtige Änderung**</span><span class="sxs-lookup"><span data-stu-id="136a6-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="136a6-107">**Auswirkungen**</span><span class="sxs-lookup"><span data-stu-id="136a6-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="136a6-108">Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität</span><span class="sxs-lookup"><span data-stu-id="136a6-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="136a6-109">Medium</span><span class="sxs-lookup"><span data-stu-id="136a6-109">Medium</span></span>     |
| [<span data-ttu-id="136a6-110">Die definierende Abfrage wurde durch anbieterspezifische Methoden ersetzt</span><span class="sxs-lookup"><span data-stu-id="136a6-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="136a6-111">Medium</span><span class="sxs-lookup"><span data-stu-id="136a6-111">Medium</span></span>     |
| [<span data-ttu-id="136a6-112">Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt</span><span class="sxs-lookup"><span data-stu-id="136a6-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="136a6-113">Niedrig</span><span class="sxs-lookup"><span data-stu-id="136a6-113">Low</span></span>        |
| [<span data-ttu-id="136a6-114">Cosmos: Der Partitionsschlüssel wird nun dem Primärschlüssel hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="136a6-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="136a6-115">Niedrig</span><span class="sxs-lookup"><span data-stu-id="136a6-115">Low</span></span>        |
| [<span data-ttu-id="136a6-116">Cosmos: Die `id`-Eigenschaft wurde umbenannt in `__id`</span><span class="sxs-lookup"><span data-stu-id="136a6-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="136a6-117">Niedrig</span><span class="sxs-lookup"><span data-stu-id="136a6-117">Low</span></span>        |
| <span data-ttu-id="136a6-118">[Cosmos: byte[] wird nun als Base64-Zeichenfolge statt als Zahlenarray gespeichert](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="136a6-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="136a6-119">Niedrig</span><span class="sxs-lookup"><span data-stu-id="136a6-119">Low</span></span>        |
| [<span data-ttu-id="136a6-120">Cosmos: GetPropertyName und SetPropertyName wurden umbenannt</span><span class="sxs-lookup"><span data-stu-id="136a6-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="136a6-121">Niedrig</span><span class="sxs-lookup"><span data-stu-id="136a6-121">Low</span></span>        |
| [<span data-ttu-id="136a6-122">Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird</span><span class="sxs-lookup"><span data-stu-id="136a6-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="136a6-123">Niedrig</span><span class="sxs-lookup"><span data-stu-id="136a6-123">Low</span></span>        |
| [<span data-ttu-id="136a6-124">IMigrationsModelDiffer verwendet jetzt IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="136a6-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="136a6-125">Niedrig</span><span class="sxs-lookup"><span data-stu-id="136a6-125">Low</span></span>        |
| [<span data-ttu-id="136a6-126">Diskriminatoren sind schreibgeschützt</span><span class="sxs-lookup"><span data-stu-id="136a6-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="136a6-127">Niedrig</span><span class="sxs-lookup"><span data-stu-id="136a6-127">Low</span></span>        |
| [<span data-ttu-id="136a6-128">Anbieterspezifische EF.Functions-Methoden werden für InMemory-Anbieter ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="136a6-128">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="136a6-129">Niedrig</span><span class="sxs-lookup"><span data-stu-id="136a6-129">Low</span></span>        |
| [<span data-ttu-id="136a6-130">IndexBuilder.HasName ist mittlerweile veraltet</span><span class="sxs-lookup"><span data-stu-id="136a6-130">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="136a6-131">Niedrig</span><span class="sxs-lookup"><span data-stu-id="136a6-131">Low</span></span>        |
| [<span data-ttu-id="136a6-132">Ein Pluarlizer ist nun für den Gerüstbau zurückentwickelter Modelle enthalten</span><span class="sxs-lookup"><span data-stu-id="136a6-132">A pluarlizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="136a6-133">Niedrig</span><span class="sxs-lookup"><span data-stu-id="136a6-133">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="136a6-134">Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt.</span><span class="sxs-lookup"><span data-stu-id="136a6-134">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="136a6-135">Issue 14257</span><span class="sxs-lookup"><span data-stu-id="136a6-135">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="136a6-136">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-136">**Old behavior**</span></span>

<span data-ttu-id="136a6-137">Die HasGeometricDimension-Methode wurde dazu verwendet, zusätzliche Maße (Z und M) für Geometriespalten zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="136a6-137">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="136a6-138">Dies hat sich jedoch immer nur auf die Datenbankerstellung ausgewirkt.</span><span class="sxs-lookup"><span data-stu-id="136a6-138">However, it only ever affected database creation.</span></span> <span data-ttu-id="136a6-139">Das Angeben dieser Methode zum Abfragen von Werten mit zusätzlichen Maßen war nicht notwendig.</span><span class="sxs-lookup"><span data-stu-id="136a6-139">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="136a6-140">Außerdem hat die Methode nicht ordnungsgemäß funktioniert, wenn Werte mit zusätzlichen Maßen eingefügt oder geändert wurden (siehe [Issue 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="136a6-140">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="136a6-141">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-141">**New behavior**</span></span>

<span data-ttu-id="136a6-142">Zum Ermöglichen des Einfügens und Änderns von Geometriewerten mit zusätzlichen Maßen (Z und M) muss das jeweilige Maß als Teil des Spaltentypnamens angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="136a6-142">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="136a6-143">Diese API entspricht eher dem zugrunde liegenden Verhalten der AddGeometryColumn-Funktion von SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="136a6-143">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="136a6-144">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="136a6-144">**Why**</span></span>

<span data-ttu-id="136a6-145">Die Verwendung der HasGeometricDimension-Methode, nachdem das Maß im Spaltentyp festgelegt wurde, ist redundant und nicht notwendig. Daher wurde die gesamte HasGeometricDimension-Methode entfernt.</span><span class="sxs-lookup"><span data-stu-id="136a6-145">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="136a6-146">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="136a6-146">**Mitigations**</span></span>

<span data-ttu-id="136a6-147">Verwenden Sie `HasColumnType` zum Festlegen der Maße:</span><span class="sxs-lookup"><span data-stu-id="136a6-147">Use `HasColumnType` to specify the dimension:</span></span>

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

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="136a6-148">Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität</span><span class="sxs-lookup"><span data-stu-id="136a6-148">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="136a6-149">Tracking Issue 17286</span><span class="sxs-lookup"><span data-stu-id="136a6-149">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="136a6-150">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-150">**Old behavior**</span></span>

<span data-ttu-id="136a6-151">Nur die Navigation zum Prinzipal konnte als erforderlich konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="136a6-151">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="136a6-152">Daher führte die Verwendung von `RequiredAttribute` für die Navigation zur abhängigen Entität (der Entität, die den Fremdschlüssel enthält) stattdessen zur Erstellung des Fremdschlüssels für den definierenden Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="136a6-152">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="136a6-153">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-153">**New behavior**</span></span>

<span data-ttu-id="136a6-154">Dank der hinzugefügten Unterstützung von erforderlichen abhängigen Entitäten ist es nun möglich, jede beliebige Verweisnavigation als erforderlich zu markieren. Dies bedeutet, dass im obigen Fall der Fremdschlüssel auf der anderen Seite der Beziehung definiert wird und die Eigenschaften nicht als erforderlich markiert werden.</span><span class="sxs-lookup"><span data-stu-id="136a6-154">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="136a6-155">Das Aufrufen von `IsRequired` vor der Angabe der abhängigen Entität ist nun mehrdeutig:</span><span class="sxs-lookup"><span data-stu-id="136a6-155">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="136a6-156">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="136a6-156">**Why**</span></span>

<span data-ttu-id="136a6-157">Das neue Verhalten ist erforderlich, um die Unterstützung von erforderlichen abhängigen Entitäten zu ermöglichen ([siehe 12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="136a6-157">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="136a6-158">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="136a6-158">**Mitigations**</span></span>

<span data-ttu-id="136a6-159">Entfernen Sie `RequiredAttribute` aus der Navigation zur abhängigen Entität, und verwenden Sie „RequiredAttribute“ stattdessen in der Navigation zum Prinzipal, oder konfigurieren Sie die Beziehung in `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="136a6-159">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="136a6-160">Cosmos: Der Partitionsschlüssel wird nun dem Primärschlüssel hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="136a6-160">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="136a6-161">Tracking Issue 15289</span><span class="sxs-lookup"><span data-stu-id="136a6-161">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="136a6-162">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-162">**Old behavior**</span></span>

<span data-ttu-id="136a6-163">Die Partitionsschlüsseleigenschaft wurde nur dem alternativen Schlüssel hinzugefügt, der `id` enthält.</span><span class="sxs-lookup"><span data-stu-id="136a6-163">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="136a6-164">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-164">**New behavior**</span></span>

<span data-ttu-id="136a6-165">Die Partitionsschlüsseleigenschaft wird gemäß Konvention jetzt auch dem Primärschlüssel hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="136a6-165">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="136a6-166">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="136a6-166">**Why**</span></span>

<span data-ttu-id="136a6-167">Durch diese Änderung ist das Modell besser auf die Azure Cosmos DB-Semantik abgestimmt. Zudem verbessert sich dadurch die Leistung von `Find` und einigen Abfragen.</span><span class="sxs-lookup"><span data-stu-id="136a6-167">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="136a6-168">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="136a6-168">**Mitigations**</span></span>

<span data-ttu-id="136a6-169">Um zu verhindern, dass die Partitionsschlüsseleigenschaft dem Primärschlüssel hinzugefügt wird, konfigurieren Sie sie in `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="136a6-169">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="136a6-170">Cosmos: Die `id`-Eigenschaft wurde umbenannt in `__id`</span><span class="sxs-lookup"><span data-stu-id="136a6-170">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="136a6-171">Issue 17751</span><span class="sxs-lookup"><span data-stu-id="136a6-171">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="136a6-172">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-172">**Old behavior**</span></span>

<span data-ttu-id="136a6-173">Die der JSON-Eigenschaft `id` zugeordnete Schatteneigenschaft hatte ebenfalls den Namen `id`.</span><span class="sxs-lookup"><span data-stu-id="136a6-173">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="136a6-174">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-174">**New behavior**</span></span>

<span data-ttu-id="136a6-175">Die gemäß Konvention erstellte Schatteneigenschaft trägt nun den Namen `__id`.</span><span class="sxs-lookup"><span data-stu-id="136a6-175">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="136a6-176">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="136a6-176">**Why**</span></span>

<span data-ttu-id="136a6-177">Durch diese Änderung ist es weniger wahrscheinlich, dass die Eigenschaft `id` mit einer vorhandenen Eigenschaft des Entitätstyps kollidiert.</span><span class="sxs-lookup"><span data-stu-id="136a6-177">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="136a6-178">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="136a6-178">**Mitigations**</span></span>

<span data-ttu-id="136a6-179">Wenn Sie zum Verhalten der Version 3.x zurückkehren möchten, konfigurieren Sie die Eigenschaft `id` in `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="136a6-179">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="136a6-180">Cosmos: byte[] wird nun als Base64-Zeichenfolge statt als Zahlenarray gespeichert</span><span class="sxs-lookup"><span data-stu-id="136a6-180">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="136a6-181">Issue 17306</span><span class="sxs-lookup"><span data-stu-id="136a6-181">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="136a6-182">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-182">**Old behavior**</span></span>

<span data-ttu-id="136a6-183">Eigenschaften vom Typ „byte[]“ wurden als Zahlenarray gespeichert.</span><span class="sxs-lookup"><span data-stu-id="136a6-183">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="136a6-184">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-184">**New behavior**</span></span>

<span data-ttu-id="136a6-185">Eigenschaften vom Typ „byte[]“ werden nun als Base64-Zeichenfolge gespeichert.</span><span class="sxs-lookup"><span data-stu-id="136a6-185">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="136a6-186">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="136a6-186">**Why**</span></span>

<span data-ttu-id="136a6-187">Diese Darstellung von „byte[]“ entspricht eher den Erwartungen. Zudem handelt es sich hier um das Standardverhalten der wichtigen JSON-Serialisierungsbibliotheken.</span><span class="sxs-lookup"><span data-stu-id="136a6-187">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="136a6-188">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="136a6-188">**Mitigations**</span></span>

<span data-ttu-id="136a6-189">Als Zahlenarrays gespeicherte, bereits vorhandene Daten werden weiterhin ordnungsgemäß abgefragt. Derzeit wird jedoch keine Möglichkeit unterstützt, das Einfügeverhalten zu ändern.</span><span class="sxs-lookup"><span data-stu-id="136a6-189">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="136a6-190">Wenn Ihr Szenario durch diese Einschränkung blockiert wird, kommentieren Sie dieses [Problem](https://github.com/aspnet/EntityFrameworkCore/issues/17306).</span><span class="sxs-lookup"><span data-stu-id="136a6-190">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="136a6-191">Cosmos: GetPropertyName und SetPropertyName wurden umbenannt</span><span class="sxs-lookup"><span data-stu-id="136a6-191">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="136a6-192">Issue 17874</span><span class="sxs-lookup"><span data-stu-id="136a6-192">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="136a6-193">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-193">**Old behavior**</span></span>

<span data-ttu-id="136a6-194">Bisher wurden die Erweiterungsmethoden `GetPropertyName` und `SetPropertyName` genannt.</span><span class="sxs-lookup"><span data-stu-id="136a6-194">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="136a6-195">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-195">**New behavior**</span></span>

<span data-ttu-id="136a6-196">Die alte API wurde entfernt, und es wurden die neuen Methoden `GetJsonPropertyName` und `SetJsonPropertyName` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="136a6-196">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="136a6-197">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="136a6-197">**Why**</span></span>

<span data-ttu-id="136a6-198">Durch diese Änderung wird die Mehrdeutigkeit um die Konfiguration durch diese Methoden beseitigt.</span><span class="sxs-lookup"><span data-stu-id="136a6-198">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="136a6-199">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="136a6-199">**Mitigations**</span></span>

<span data-ttu-id="136a6-200">Verwenden Sie die neue API.</span><span class="sxs-lookup"><span data-stu-id="136a6-200">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="136a6-201">Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird</span><span class="sxs-lookup"><span data-stu-id="136a6-201">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="136a6-202">Tracking Issue 15289</span><span class="sxs-lookup"><span data-stu-id="136a6-202">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="136a6-203">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-203">**Old behavior**</span></span>

<span data-ttu-id="136a6-204">Wert-Generatoren wurden nur aufgerufen, wenn der Entitätszustand in „Hinzugefügt“ geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="136a6-204">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="136a6-205">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-205">**New behavior**</span></span>

<span data-ttu-id="136a6-206">Wert-Generatoren werden jetzt aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird und die Eigenschaft die Standardwerte enthält.</span><span class="sxs-lookup"><span data-stu-id="136a6-206">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="136a6-207">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="136a6-207">**Why**</span></span>

<span data-ttu-id="136a6-208">Diese Änderung war erforderlich, um die Verwendung von Eigenschaften zu verbessern, die nicht dauerhaft im Datenspeicher gespeichert sind und deren Wert immer auf dem Client generiert wird.</span><span class="sxs-lookup"><span data-stu-id="136a6-208">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="136a6-209">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="136a6-209">**Mitigations**</span></span>

<span data-ttu-id="136a6-210">Weisen Sie der Eigenschaft vor der Änderung des Status einen anderen Wert als den Standardwert zu, um zu verhindern, dass der Wert-Generator aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="136a6-210">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="136a6-211">IMigrationsModelDiffer verwendet jetzt IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="136a6-211">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="136a6-212">Tracking Issue 20305</span><span class="sxs-lookup"><span data-stu-id="136a6-212">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="136a6-213">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-213">**Old behavior**</span></span>

<span data-ttu-id="136a6-214">Die `IMigrationsModelDiffer`-API wurde mithilfe von `IModel` definiert.</span><span class="sxs-lookup"><span data-stu-id="136a6-214">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="136a6-215">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-215">**New behavior**</span></span>

<span data-ttu-id="136a6-216">Die `IMigrationsModelDiffer`-API verwendet jetzt `IRelationalModel`.</span><span class="sxs-lookup"><span data-stu-id="136a6-216">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="136a6-217">Die Modellmomentaufnahme enthält jedoch weiterhin nur `IModel`, da dieser Code Teil der Anwendung ist und Entity Framework ihn nicht ohne einen größeren Breaking Change ändern kann.</span><span class="sxs-lookup"><span data-stu-id="136a6-217">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="136a6-218">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="136a6-218">**Why**</span></span>

<span data-ttu-id="136a6-219">`IRelationalModel` ist eine neu hinzugefügte Darstellung des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="136a6-219">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="136a6-220">Die Verwendung dieser Darstellung zum Ermitteln von Unterschieden ist schneller und präziser.</span><span class="sxs-lookup"><span data-stu-id="136a6-220">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="136a6-221">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="136a6-221">**Mitigations**</span></span>

<span data-ttu-id="136a6-222">Verwenden Sie den folgenden Code, um das Modell von `snapshot` mit dem Modell von `context` zu vergleichen:</span><span class="sxs-lookup"><span data-stu-id="136a6-222">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="136a6-223">Eine Verbesserung dieser Funktion in Version 6.0 ist geplant ([siehe 22031](https://github.com/dotnet/efcore/issues/22031)).</span><span class="sxs-lookup"><span data-stu-id="136a6-223">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="136a6-224">Diskriminatoren sind schreibgeschützt</span><span class="sxs-lookup"><span data-stu-id="136a6-224">Discriminators are read-only</span></span>

[<span data-ttu-id="136a6-225">Tracking Issue 21154</span><span class="sxs-lookup"><span data-stu-id="136a6-225">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="136a6-226">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-226">**Old behavior**</span></span>

<span data-ttu-id="136a6-227">Der Diskriminatorwert konnte vor dem Aufrufen von `SaveChanges` geändert werden.</span><span class="sxs-lookup"><span data-stu-id="136a6-227">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="136a6-228">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-228">**New behavior**</span></span>

<span data-ttu-id="136a6-229">Im obigen Fall wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="136a6-229">An exception will be throws in the above case.</span></span>

<span data-ttu-id="136a6-230">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="136a6-230">**Why**</span></span>

<span data-ttu-id="136a6-231">EF erwartet nicht, dass der Entitätstyp geändert wird, während er noch nachverfolgt wird, sodass das Ändern des Diskriminatorwerts zu einem inkonsistenten Kontext führt. Dies hat möglicherweise ein unerwartetes Verhalten zur Folge.</span><span class="sxs-lookup"><span data-stu-id="136a6-231">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="136a6-232">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="136a6-232">**Mitigations**</span></span>

<span data-ttu-id="136a6-233">Wenn eine Änderung des Diskriminatorwerts erforderlich ist und der Kontext direkt nach dem Aufruf von `SaveChanges` verworfen wird, kann der Diskriminator als änderbar festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="136a6-233">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="136a6-234">Die definierende Abfrage wurde durch anbieterspezifische Methoden ersetzt</span><span class="sxs-lookup"><span data-stu-id="136a6-234">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="136a6-235">Issue 18903</span><span class="sxs-lookup"><span data-stu-id="136a6-235">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="136a6-236">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-236">**Old behavior**</span></span>

<span data-ttu-id="136a6-237">Entitätstypen wurden zum Definieren von Abfragen auf der Kernebene zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="136a6-237">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="136a6-238">Jedes Mal, wenn der Entitätstyp im Abfragestamm des Entitätstyps verwendet wurde, wurde er bei allen Anbietern durch die definierende Abfrage ersetzt.</span><span class="sxs-lookup"><span data-stu-id="136a6-238">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="136a6-239">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-239">**New behavior**</span></span>

<span data-ttu-id="136a6-240">APIs für definierende Abfragen sind veraltet.</span><span class="sxs-lookup"><span data-stu-id="136a6-240">APIs for defining query are deprecated.</span></span> <span data-ttu-id="136a6-241">Es wurden neue anbieterspezifische APIs eingeführt.</span><span class="sxs-lookup"><span data-stu-id="136a6-241">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="136a6-242">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="136a6-242">**Why**</span></span>

<span data-ttu-id="136a6-243">Während definierende Abfragen als Ersatzabfragen implementiert wurden, wenn in der Abfrage der Abfragestamm verwendet wird, sind einige Probleme aufgetreten:</span><span class="sxs-lookup"><span data-stu-id="136a6-243">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="136a6-244">Wenn eine definierende Abfrage einen Entitätstyp mit `new { ... }` in die `Select`-Methode projiziert, war für die Identifizierung dessen als Entität zusätzliche Arbeit erforderlich. Zudem sorgte dieses Vorgehen für Inkonsistenzen in Bezug darauf, wie EF Core nominale Typen in der Abfrage behandelt.</span><span class="sxs-lookup"><span data-stu-id="136a6-244">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="136a6-245">Bei relationalen Anbietern ist `FromSql` weiterhin erforderlich, um die SQL-Zeichenfolge im LINQ-Ausdrucksformat zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="136a6-245">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="136a6-246">Zunächst wurden definierende Abfragen als clientseitige Sichten zur Verwendung mit In-Memory-Anbietern bei schlüssellosen Entitäten eingeführt (ähnlich wie bei Datenbanksichten in relationalen Datenbanken).</span><span class="sxs-lookup"><span data-stu-id="136a6-246">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="136a6-247">Eine solche Definition erleichtert das Testen der Anwendung mit In-Memory-Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="136a6-247">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="136a6-248">Später wurden sie allgemein anwendbar, was zwar nützlich war, aber ein inkonsistentes und schwer verständliches Verhalten mit sich brachte.</span><span class="sxs-lookup"><span data-stu-id="136a6-248">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="136a6-249">Daher haben wir uns entschieden, das Konzept zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="136a6-249">So we decided to simplify the concept.</span></span> <span data-ttu-id="136a6-250">Wir stellen die LINQ-basierte definierende Abfrage nur noch für In-Memory-Anbieter bereit und behandeln sie anders.</span><span class="sxs-lookup"><span data-stu-id="136a6-250">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="136a6-251">Weitere Informationen finden Sie in [diesem Thema](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="136a6-251">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="136a6-252">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="136a6-252">**Mitigations**</span></span>

<span data-ttu-id="136a6-253">Verwenden Sie für relationale Anbieter die `ToSqlQuery`-Methode in `OnModelCreating`, und übergeben Sie eine SQL-Zeichenfolge zur Verwendung für den Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="136a6-253">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="136a6-254">Verwenden Sie für den In-Memory-Anbieter die `ToInMemoryQuery`-Methode in `OnModelCreating`, und übergeben Sie eine LINQ-Abfrage zur Verwendung für den Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="136a6-254">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="136a6-255">Anbieterspezifische EF.Functions-Methoden werden für InMemory-Anbieter ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="136a6-255">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="136a6-256">Issue 20294</span><span class="sxs-lookup"><span data-stu-id="136a6-256">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

<span data-ttu-id="136a6-257">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-257">**Old behavior**</span></span>

<span data-ttu-id="136a6-258">Anbieterspezifische EF.Functions-Methoden enthielten eine Implementierung für die Clientausführung, sodass sie für den InMemory-Anbieter ausgeführt werden konnten.</span><span class="sxs-lookup"><span data-stu-id="136a6-258">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="136a6-259">`EF.Functions.DateDiffDay` ist beispielsweise eine SQL Server-spezifische Methode, die für InMemory-Anbieter galt.</span><span class="sxs-lookup"><span data-stu-id="136a6-259">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

<span data-ttu-id="136a6-260">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-260">**New behavior**</span></span>

<span data-ttu-id="136a6-261">Anbieterspezifische Methoden wurden aktualisiert, sodass sie im Methodentext eine Ausnahme auslösen, um ihre Auswertung auf Clientseite zu blockieren.</span><span class="sxs-lookup"><span data-stu-id="136a6-261">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

<span data-ttu-id="136a6-262">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="136a6-262">**Why**</span></span>

<span data-ttu-id="136a6-263">Anbieterspezifische Methoden sind einer Datenbankfunktion zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="136a6-263">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="136a6-264">Das Berechnungsergebnis der zugeordneten Datenbankfunktion kann auf Clientseite in LINQ nicht immer repliziert werden.</span><span class="sxs-lookup"><span data-stu-id="136a6-264">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="136a6-265">Dies kann dazu führen, dass sich das Ergebnis von dem des Servers unterscheidet, wenn dieselbe Methode auf dem Client ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="136a6-265">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="136a6-266">Da diese Methoden in LINQ verwendet werden, um Übersetzungen in bestimmte Datenbankfunktionen durchzuführen, müssen sie nicht auf Clientseite ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="136a6-266">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="136a6-267">Da der InMemory-Anbieter eine andere *Datenbank* ist, sind die Methoden für diesen Anbieter nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="136a6-267">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="136a6-268">Wenn Sie versuchen, sie für den InMemory-Anbieter oder einen anderen Anbieter auszuführen, der diese Methoden nicht übersetzt, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="136a6-268">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

<span data-ttu-id="136a6-269">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="136a6-269">**Mitigations**</span></span>

<span data-ttu-id="136a6-270">Da es keine Möglichkeit gibt, das Verhalten von Datenbankfunktionen exakt zu imitieren, sollten Sie die Abfragen, die diese Methoden enthalten, mit demselben Datenbanktyp testen, der auch in der Produktionsumgebung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="136a6-270">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="136a6-271">IndexBuilder.HasName ist mittlerweile veraltet</span><span class="sxs-lookup"><span data-stu-id="136a6-271">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="136a6-272">Nachverfolgung von Issue 21089</span><span class="sxs-lookup"><span data-stu-id="136a6-272">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

<span data-ttu-id="136a6-273">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-273">**Old behavior**</span></span>

<span data-ttu-id="136a6-274">Bisher konnte nur ein Index für eine bestimmte Gruppe von Eigenschaften definiert werden.</span><span class="sxs-lookup"><span data-stu-id="136a6-274">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="136a6-275">Der Datenbankname eines Indexes wurde mithilfe von IndexBuilder.HasName konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="136a6-275">The database name of an index was configured using IndexBuilder.HasName.</span></span>

<span data-ttu-id="136a6-276">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-276">**New behavior**</span></span>

<span data-ttu-id="136a6-277">Für dieselbe Gruppe oder dieselben Eigenschaften sind jetzt mehrere Indizes zulässig.</span><span class="sxs-lookup"><span data-stu-id="136a6-277">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="136a6-278">Diese Indizes unterscheiden sich nun durch einen Namen im Modell.</span><span class="sxs-lookup"><span data-stu-id="136a6-278">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="136a6-279">Gemäß der Konvention wird der Modellname als Datenbankname verwendet. Er kann jedoch auch unabhängig mit HasDatabaseName konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="136a6-279">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

<span data-ttu-id="136a6-280">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="136a6-280">**Why**</span></span>

<span data-ttu-id="136a6-281">In Zukunft möchten wir sowohl aufsteigende als auch absteigende Indizes oder Indizes mit unterschiedlichen Sortierungen für denselben Satz von Eigenschaften aktivieren.</span><span class="sxs-lookup"><span data-stu-id="136a6-281">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="136a6-282">Diese Änderung ist ein weiterer Schritt in diese Richtung.</span><span class="sxs-lookup"><span data-stu-id="136a6-282">This change moves us another step in that direction.</span></span>

<span data-ttu-id="136a6-283">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="136a6-283">**Mitigations**</span></span>

<span data-ttu-id="136a6-284">Sämtlicher Code, der zuvor IndexBuilder.HasName aufgerufen hat, sollte so aktualisiert werden, dass stattdessen HasDatabaseName aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="136a6-284">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="136a6-285">Wenn das Projekt Migrationen enthält, die vor EF Core Version 2.0.0 generiert wurden, können Sie die Warnung in diesen Dateien problemlos ignorieren und unterdrücken, indem Sie `#pragma warning disable 612, 618` hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="136a6-285">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluarlizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="136a6-286">Ein Pluarlizer ist nun für den Gerüstbau zurückentwickelter Modelle enthalten</span><span class="sxs-lookup"><span data-stu-id="136a6-286">A pluarlizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="136a6-287">Nachverfolgung von Issue 11160</span><span class="sxs-lookup"><span data-stu-id="136a6-287">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

<span data-ttu-id="136a6-288">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-288">**Old behavior**</span></span>

<span data-ttu-id="136a6-289">Früher mussten Sie beim Gerüstbau für einen DbContext und Entitätstypen durch Reverse Engineering eines Datenbankschemas ein separates Pluralizer-Paket installieren, um DbSet- und Sammlungsnavigationsnamen in die Pluralform und Tabellennamen in die Singularform umzuwandeln.</span><span class="sxs-lookup"><span data-stu-id="136a6-289">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffoding a DbContext and entity types by reverse engineering a database schema.</span></span>

<span data-ttu-id="136a6-290">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="136a6-290">**New behavior**</span></span>

<span data-ttu-id="136a6-291">EF Core enthält jetzt einen Pluarlizer, der die [Humanizer](https://github.com/Humanizr/Humanizer)-Bibliothek verwendet.</span><span class="sxs-lookup"><span data-stu-id="136a6-291">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="136a6-292">Dies ist dieselbe Bibliothek, die Visual Studio verwendet, um Variablennamen zu empfehlen.</span><span class="sxs-lookup"><span data-stu-id="136a6-292">This is the same library Visual Studio uses to recommend variable names.</span></span>

<span data-ttu-id="136a6-293">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="136a6-293">**Why**</span></span>

<span data-ttu-id="136a6-294">Die Verwendung von Pluralformen von Wörtern für Sammlungseigenschaften und Singularformen für Typen und Verweiseigenschaften ist in .NET idiomatisch.</span><span class="sxs-lookup"><span data-stu-id="136a6-294">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

<span data-ttu-id="136a6-295">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="136a6-295">**Mitigations**</span></span>

<span data-ttu-id="136a6-296">Um den Pluralizer zu deaktivieren, verwenden Sie die Option `--no-pluralize` auf `dotnet ef dbcontext scaffold` oder den `-NoPluralize`-Switch auf `Scaffold-DbContext`.</span><span class="sxs-lookup"><span data-stu-id="136a6-296">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>
