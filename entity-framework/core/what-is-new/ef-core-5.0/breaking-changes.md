---
title: 'EF Core: Breaking Changes in EF Core 5.0'
description: Vollständige Liste der in Entity Framework Core 5.0 eingeführten Breaking Changes
author: bricelam
ms.date: 09/09/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 8e9df4e2ff81e20cf5a36855247c5aff89ea2394
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210366"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="4ef3f-103">Breaking Changes in EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="4ef3f-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="4ef3f-104">Die folgenden API-Änderungen und Behavior Changes können dazu führen, dass vorhandene Anwendungen nach einem Upgrade auf EF Core 5.0.0 nicht mehr funktionieren.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="4ef3f-105">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="4ef3f-105">Summary</span></span>

| <span data-ttu-id="4ef3f-106">**Wichtige Änderung**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="4ef3f-107">**Auswirkungen**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="4ef3f-108">Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität</span><span class="sxs-lookup"><span data-stu-id="4ef3f-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="4ef3f-109">Medium</span><span class="sxs-lookup"><span data-stu-id="4ef3f-109">Medium</span></span>     |
| [<span data-ttu-id="4ef3f-110">Die definierende Abfrage wurde durch anbieterspezifische Methoden ersetzt</span><span class="sxs-lookup"><span data-stu-id="4ef3f-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="4ef3f-111">Medium</span><span class="sxs-lookup"><span data-stu-id="4ef3f-111">Medium</span></span>     |
| [<span data-ttu-id="4ef3f-112">Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt</span><span class="sxs-lookup"><span data-stu-id="4ef3f-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="4ef3f-113">Niedrig</span><span class="sxs-lookup"><span data-stu-id="4ef3f-113">Low</span></span>        |
| [<span data-ttu-id="4ef3f-114">Cosmos: Der Partitionsschlüssel wird nun dem Primärschlüssel hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="4ef3f-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="4ef3f-115">Niedrig</span><span class="sxs-lookup"><span data-stu-id="4ef3f-115">Low</span></span>        |
| [<span data-ttu-id="4ef3f-116">Cosmos: Die `id`-Eigenschaft wurde umbenannt in `__id`</span><span class="sxs-lookup"><span data-stu-id="4ef3f-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="4ef3f-117">Niedrig</span><span class="sxs-lookup"><span data-stu-id="4ef3f-117">Low</span></span>        |
| <span data-ttu-id="4ef3f-118">[Cosmos: byte[] wird nun als Base64-Zeichenfolge statt als Zahlenarray gespeichert](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="4ef3f-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="4ef3f-119">Niedrig</span><span class="sxs-lookup"><span data-stu-id="4ef3f-119">Low</span></span>        |
| [<span data-ttu-id="4ef3f-120">Cosmos: GetPropertyName und SetPropertyName wurden umbenannt</span><span class="sxs-lookup"><span data-stu-id="4ef3f-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="4ef3f-121">Niedrig</span><span class="sxs-lookup"><span data-stu-id="4ef3f-121">Low</span></span>        |
| [<span data-ttu-id="4ef3f-122">Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird</span><span class="sxs-lookup"><span data-stu-id="4ef3f-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="4ef3f-123">Niedrig</span><span class="sxs-lookup"><span data-stu-id="4ef3f-123">Low</span></span>        |
| [<span data-ttu-id="4ef3f-124">IMigrationsModelDiffer verwendet jetzt IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="4ef3f-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="4ef3f-125">Niedrig</span><span class="sxs-lookup"><span data-stu-id="4ef3f-125">Low</span></span>        |
| [<span data-ttu-id="4ef3f-126">Diskriminatoren sind schreibgeschützt</span><span class="sxs-lookup"><span data-stu-id="4ef3f-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="4ef3f-127">Niedrig</span><span class="sxs-lookup"><span data-stu-id="4ef3f-127">Low</span></span>        |
| [<span data-ttu-id="4ef3f-128">Anbieterspezifische EF.Functions-Methoden werden für InMemory-Anbieter ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-128">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="4ef3f-129">Niedrig</span><span class="sxs-lookup"><span data-stu-id="4ef3f-129">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="4ef3f-130">Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-130">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="4ef3f-131">Issue 14257</span><span class="sxs-lookup"><span data-stu-id="4ef3f-131">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="4ef3f-132">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-132">**Old behavior**</span></span>

<span data-ttu-id="4ef3f-133">Die HasGeometricDimension-Methode wurde dazu verwendet, zusätzliche Maße (Z und M) für Geometriespalten zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-133">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="4ef3f-134">Dies hat sich jedoch immer nur auf die Datenbankerstellung ausgewirkt.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-134">However, it only ever affected database creation.</span></span> <span data-ttu-id="4ef3f-135">Das Angeben dieser Methode zum Abfragen von Werten mit zusätzlichen Maßen war nicht notwendig.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-135">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="4ef3f-136">Außerdem hat die Methode nicht ordnungsgemäß funktioniert, wenn Werte mit zusätzlichen Maßen eingefügt oder geändert wurden (siehe [Issue 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="4ef3f-136">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="4ef3f-137">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-137">**New behavior**</span></span>

<span data-ttu-id="4ef3f-138">Zum Ermöglichen des Einfügens und Änderns von Geometriewerten mit zusätzlichen Maßen (Z und M) muss das jeweilige Maß als Teil des Spaltentypnamens angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-138">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="4ef3f-139">Diese API entspricht eher dem zugrunde liegenden Verhalten der AddGeometryColumn-Funktion von SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-139">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="4ef3f-140">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-140">**Why**</span></span>

<span data-ttu-id="4ef3f-141">Die Verwendung der HasGeometricDimension-Methode, nachdem das Maß im Spaltentyp festgelegt wurde, ist redundant und nicht notwendig. Daher wurde die gesamte HasGeometricDimension-Methode entfernt.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-141">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="4ef3f-142">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-142">**Mitigations**</span></span>

<span data-ttu-id="4ef3f-143">Verwenden Sie `HasColumnType` zum Festlegen der Maße:</span><span class="sxs-lookup"><span data-stu-id="4ef3f-143">Use `HasColumnType` to specify the dimension:</span></span>

```cs
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

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="4ef3f-144">Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität</span><span class="sxs-lookup"><span data-stu-id="4ef3f-144">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="4ef3f-145">Tracking Issue 17286</span><span class="sxs-lookup"><span data-stu-id="4ef3f-145">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="4ef3f-146">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-146">**Old behavior**</span></span>

<span data-ttu-id="4ef3f-147">Nur die Navigation zum Prinzipal konnte als erforderlich konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-147">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="4ef3f-148">Daher führte die Verwendung von `RequiredAttribute` für die Navigation zur abhängigen Entität (der Entität, die den Fremdschlüssel enthält) stattdessen zur Erstellung des Fremdschlüssels für den definierenden Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-148">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="4ef3f-149">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-149">**New behavior**</span></span>

<span data-ttu-id="4ef3f-150">Dank der hinzugefügten Unterstützung von erforderlichen abhängigen Entitäten ist es nun möglich, jede beliebige Verweisnavigation als erforderlich zu markieren. Dies bedeutet, dass im obigen Fall der Fremdschlüssel auf der anderen Seite der Beziehung definiert wird und die Eigenschaften nicht als erforderlich markiert werden.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-150">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="4ef3f-151">Das Aufrufen von `IsRequired` vor der Angabe der abhängigen Entität ist nun mehrdeutig:</span><span class="sxs-lookup"><span data-stu-id="4ef3f-151">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="4ef3f-152">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-152">**Why**</span></span>

<span data-ttu-id="4ef3f-153">Das neue Verhalten ist erforderlich, um die Unterstützung von erforderlichen abhängigen Entitäten zu ermöglichen ([siehe 12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="4ef3f-153">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="4ef3f-154">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-154">**Mitigations**</span></span>

<span data-ttu-id="4ef3f-155">Entfernen Sie `RequiredAttribute` aus der Navigation zur abhängigen Entität, und verwenden Sie „RequiredAttribute“ stattdessen in der Navigation zum Prinzipal, oder konfigurieren Sie die Beziehung in `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="4ef3f-155">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="4ef3f-156">Cosmos: Der Partitionsschlüssel wird nun dem Primärschlüssel hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="4ef3f-156">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="4ef3f-157">Tracking Issue 15289</span><span class="sxs-lookup"><span data-stu-id="4ef3f-157">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="4ef3f-158">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-158">**Old behavior**</span></span>

<span data-ttu-id="4ef3f-159">Die Partitionsschlüsseleigenschaft wurde nur dem alternativen Schlüssel hinzugefügt, der `id` enthält.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-159">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="4ef3f-160">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-160">**New behavior**</span></span>

<span data-ttu-id="4ef3f-161">Die Partitionsschlüsseleigenschaft wird gemäß Konvention jetzt auch dem Primärschlüssel hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-161">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="4ef3f-162">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-162">**Why**</span></span>

<span data-ttu-id="4ef3f-163">Durch diese Änderung ist das Modell besser auf die Azure Cosmos DB-Semantik abgestimmt. Zudem verbessert sich dadurch die Leistung von `Find` und einigen Abfragen.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-163">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="4ef3f-164">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-164">**Mitigations**</span></span>

<span data-ttu-id="4ef3f-165">Um zu verhindern, dass die Partitionsschlüsseleigenschaft dem Primärschlüssel hinzugefügt wird, konfigurieren Sie sie in `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-165">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="4ef3f-166">Cosmos: Die `id`-Eigenschaft wurde umbenannt in `__id`</span><span class="sxs-lookup"><span data-stu-id="4ef3f-166">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="4ef3f-167">Issue 17751</span><span class="sxs-lookup"><span data-stu-id="4ef3f-167">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="4ef3f-168">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-168">**Old behavior**</span></span>

<span data-ttu-id="4ef3f-169">Die der JSON-Eigenschaft `id` zugeordnete Schatteneigenschaft hatte ebenfalls den Namen `id`.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-169">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="4ef3f-170">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-170">**New behavior**</span></span>

<span data-ttu-id="4ef3f-171">Die gemäß Konvention erstellte Schatteneigenschaft trägt nun den Namen `__id`.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-171">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="4ef3f-172">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-172">**Why**</span></span>

<span data-ttu-id="4ef3f-173">Durch diese Änderung ist es weniger wahrscheinlich, dass die Eigenschaft `id` mit einer vorhandenen Eigenschaft des Entitätstyps kollidiert.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-173">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="4ef3f-174">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-174">**Mitigations**</span></span>

<span data-ttu-id="4ef3f-175">Wenn Sie zum Verhalten der Version 3.x zurückkehren möchten, konfigurieren Sie die Eigenschaft `id` in `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-175">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="4ef3f-176">Cosmos: byte[] wird nun als Base64-Zeichenfolge statt als Zahlenarray gespeichert</span><span class="sxs-lookup"><span data-stu-id="4ef3f-176">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="4ef3f-177">Issue 17306</span><span class="sxs-lookup"><span data-stu-id="4ef3f-177">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="4ef3f-178">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-178">**Old behavior**</span></span>

<span data-ttu-id="4ef3f-179">Eigenschaften vom Typ „byte[]“ wurden als Zahlenarray gespeichert.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-179">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="4ef3f-180">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-180">**New behavior**</span></span>

<span data-ttu-id="4ef3f-181">Eigenschaften vom Typ „byte[]“ werden nun als Base64-Zeichenfolge gespeichert.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-181">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="4ef3f-182">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-182">**Why**</span></span>

<span data-ttu-id="4ef3f-183">Diese Darstellung von „byte[]“ entspricht eher den Erwartungen. Zudem handelt es sich hier um das Standardverhalten der wichtigen JSON-Serialisierungsbibliotheken.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-183">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="4ef3f-184">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-184">**Mitigations**</span></span>

<span data-ttu-id="4ef3f-185">Als Zahlenarrays gespeicherte, bereits vorhandene Daten werden weiterhin ordnungsgemäß abgefragt. Derzeit wird jedoch keine Möglichkeit unterstützt, das Einfügeverhalten zu ändern.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-185">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="4ef3f-186">Wenn Ihr Szenario durch diese Einschränkung blockiert wird, kommentieren Sie dieses [Problem](https://github.com/aspnet/EntityFrameworkCore/issues/17306).</span><span class="sxs-lookup"><span data-stu-id="4ef3f-186">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="4ef3f-187">Cosmos: GetPropertyName und SetPropertyName wurden umbenannt</span><span class="sxs-lookup"><span data-stu-id="4ef3f-187">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="4ef3f-188">Issue 17874</span><span class="sxs-lookup"><span data-stu-id="4ef3f-188">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="4ef3f-189">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-189">**Old behavior**</span></span>

<span data-ttu-id="4ef3f-190">Bisher wurden die Erweiterungsmethoden `GetPropertyName` und `SetPropertyName` genannt.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-190">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="4ef3f-191">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-191">**New behavior**</span></span>

<span data-ttu-id="4ef3f-192">Die alte API wurde entfernt, und es wurden die neuen Methoden `GetJsonPropertyName` und `SetJsonPropertyName` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-192">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="4ef3f-193">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-193">**Why**</span></span>

<span data-ttu-id="4ef3f-194">Durch diese Änderung wird die Mehrdeutigkeit um die Konfiguration durch diese Methoden beseitigt.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-194">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="4ef3f-195">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-195">**Mitigations**</span></span>

<span data-ttu-id="4ef3f-196">Verwenden Sie die neue API.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-196">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="4ef3f-197">Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird</span><span class="sxs-lookup"><span data-stu-id="4ef3f-197">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="4ef3f-198">Tracking Issue 15289</span><span class="sxs-lookup"><span data-stu-id="4ef3f-198">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="4ef3f-199">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-199">**Old behavior**</span></span>

<span data-ttu-id="4ef3f-200">Wert-Generatoren wurden nur aufgerufen, wenn der Entitätszustand in „Hinzugefügt“ geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-200">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="4ef3f-201">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-201">**New behavior**</span></span>

<span data-ttu-id="4ef3f-202">Wert-Generatoren werden jetzt aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird und die Eigenschaft die Standardwerte enthält.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-202">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="4ef3f-203">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-203">**Why**</span></span>

<span data-ttu-id="4ef3f-204">Diese Änderung war erforderlich, um die Verwendung von Eigenschaften zu verbessern, die nicht dauerhaft im Datenspeicher gespeichert sind und deren Wert immer auf dem Client generiert wird.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-204">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="4ef3f-205">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-205">**Mitigations**</span></span>

<span data-ttu-id="4ef3f-206">Weisen Sie der Eigenschaft vor der Änderung des Status einen anderen Wert als den Standardwert zu, um zu verhindern, dass der Wert-Generator aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-206">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="4ef3f-207">IMigrationsModelDiffer verwendet jetzt IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="4ef3f-207">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="4ef3f-208">Tracking Issue 20305</span><span class="sxs-lookup"><span data-stu-id="4ef3f-208">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="4ef3f-209">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-209">**Old behavior**</span></span>

<span data-ttu-id="4ef3f-210">Die `IMigrationsModelDiffer`-API wurde mithilfe von `IModel` definiert.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-210">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="4ef3f-211">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-211">**New behavior**</span></span>

<span data-ttu-id="4ef3f-212">Die `IMigrationsModelDiffer`-API verwendet jetzt `IRelationalModel`.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-212">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="4ef3f-213">Die Modellmomentaufnahme enthält jedoch weiterhin nur `IModel`, da dieser Code Teil der Anwendung ist und Entity Framework ihn nicht ohne einen größeren Breaking Change ändern kann.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-213">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="4ef3f-214">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-214">**Why**</span></span>

<span data-ttu-id="4ef3f-215">`IRelationalModel` ist eine neu hinzugefügte Darstellung des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-215">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="4ef3f-216">Die Verwendung dieser Darstellung zum Ermitteln von Unterschieden ist schneller und präziser.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-216">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="4ef3f-217">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-217">**Mitigations**</span></span>

<span data-ttu-id="4ef3f-218">Verwenden Sie den folgenden Code, um das Modell von `snapshot` mit dem Modell von `context` zu vergleichen:</span><span class="sxs-lookup"><span data-stu-id="4ef3f-218">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

```cs
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

<span data-ttu-id="4ef3f-219">Eine Verbesserung dieser Funktion in Version 6.0 ist geplant ([siehe 22031](https://github.com/dotnet/efcore/issues/22031)).</span><span class="sxs-lookup"><span data-stu-id="4ef3f-219">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="4ef3f-220">Diskriminatoren sind schreibgeschützt</span><span class="sxs-lookup"><span data-stu-id="4ef3f-220">Discriminators are read-only</span></span>

[<span data-ttu-id="4ef3f-221">Tracking Issue 21154</span><span class="sxs-lookup"><span data-stu-id="4ef3f-221">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="4ef3f-222">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-222">**Old behavior**</span></span>

<span data-ttu-id="4ef3f-223">Der Diskriminatorwert konnte vor dem Aufrufen von `SaveChanges` geändert werden.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-223">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="4ef3f-224">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-224">**New behavior**</span></span>

<span data-ttu-id="4ef3f-225">Im obigen Fall wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-225">An exception will be throws in the above case.</span></span>

<span data-ttu-id="4ef3f-226">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-226">**Why**</span></span>

<span data-ttu-id="4ef3f-227">EF erwartet nicht, dass der Entitätstyp geändert wird, während er noch nachverfolgt wird, sodass das Ändern des Diskriminatorwerts zu einem inkonsistenten Kontext führt. Dies hat möglicherweise ein unerwartetes Verhalten zur Folge.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-227">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="4ef3f-228">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-228">**Mitigations**</span></span>

<span data-ttu-id="4ef3f-229">Wenn eine Änderung des Diskriminatorwerts erforderlich ist und der Kontext direkt nach dem Aufruf von `SaveChanges` verworfen wird, kann der Diskriminator als änderbar festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="4ef3f-229">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="4ef3f-230">Die definierende Abfrage wurde durch anbieterspezifische Methoden ersetzt</span><span class="sxs-lookup"><span data-stu-id="4ef3f-230">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="4ef3f-231">Issue 18903</span><span class="sxs-lookup"><span data-stu-id="4ef3f-231">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="4ef3f-232">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-232">**Old behavior**</span></span>

<span data-ttu-id="4ef3f-233">Entitätstypen wurden zum Definieren von Abfragen auf der Kernebene zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-233">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="4ef3f-234">Jedes Mal, wenn der Entitätstyp im Abfragestamm des Entitätstyps verwendet wurde, wurde er bei allen Anbietern durch die definierende Abfrage ersetzt.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-234">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="4ef3f-235">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-235">**New behavior**</span></span>

<span data-ttu-id="4ef3f-236">APIs für definierende Abfragen sind veraltet.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-236">APIs for defining query are deprecated.</span></span> <span data-ttu-id="4ef3f-237">Es wurden neue anbieterspezifische APIs eingeführt.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-237">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="4ef3f-238">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-238">**Why**</span></span>

<span data-ttu-id="4ef3f-239">Während definierende Abfragen als Ersatzabfragen implementiert wurden, wenn in der Abfrage der Abfragestamm verwendet wird, sind einige Probleme aufgetreten:</span><span class="sxs-lookup"><span data-stu-id="4ef3f-239">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="4ef3f-240">Wenn eine definierende Abfrage einen Entitätstyp mit `new { ... }` in die `Select`-Methode projiziert, war für die Identifizierung dessen als Entität zusätzliche Arbeit erforderlich. Zudem sorgte dieses Vorgehen für Inkonsistenzen in Bezug darauf, wie EF Core nominale Typen in der Abfrage behandelt.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-240">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="4ef3f-241">Bei relationalen Anbietern ist `FromSql` weiterhin erforderlich, um die SQL-Zeichenfolge im LINQ-Ausdrucksformat zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-241">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="4ef3f-242">Zunächst wurden definierende Abfragen als clientseitige Sichten zur Verwendung mit In-Memory-Anbietern bei schlüssellosen Entitäten eingeführt (ähnlich wie bei Datenbanksichten in relationalen Datenbanken).</span><span class="sxs-lookup"><span data-stu-id="4ef3f-242">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="4ef3f-243">Eine solche Definition erleichtert das Testen der Anwendung mit In-Memory-Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-243">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="4ef3f-244">Später wurden sie allgemein anwendbar, was zwar nützlich war, aber ein inkonsistentes und schwer verständliches Verhalten mit sich brachte.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-244">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="4ef3f-245">Daher haben wir uns entschieden, das Konzept zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-245">So we decided to simplify the concept.</span></span> <span data-ttu-id="4ef3f-246">Wir stellen die LINQ-basierte definierende Abfrage nur noch für In-Memory-Anbieter bereit und behandeln sie anders.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-246">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="4ef3f-247">Weitere Informationen finden Sie in [diesem Thema](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="4ef3f-247">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="4ef3f-248">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-248">**Mitigations**</span></span>

<span data-ttu-id="4ef3f-249">Verwenden Sie für relationale Anbieter die `ToSqlQuery`-Methode in `OnModelCreating`, und übergeben Sie eine SQL-Zeichenfolge zur Verwendung für den Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-249">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="4ef3f-250">Verwenden Sie für den In-Memory-Anbieter die `ToInMemoryQuery`-Methode in `OnModelCreating`, und übergeben Sie eine LINQ-Abfrage zur Verwendung für den Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-250">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="4ef3f-251">Anbieterspezifische EF.Functions-Methoden werden für InMemory-Anbieter ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-251">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="4ef3f-252">Issue 20294</span><span class="sxs-lookup"><span data-stu-id="4ef3f-252">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

<span data-ttu-id="4ef3f-253">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-253">**Old behavior**</span></span>

<span data-ttu-id="4ef3f-254">Anbieterspezifische EF.Functions-Methoden enthielten eine Implementierung für die Clientausführung, sodass sie für den InMemory-Anbieter ausgeführt werden konnten.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-254">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="4ef3f-255">`EF.Functions.DateDiffDay` ist beispielsweise eine SQL Server-spezifische Methode, die für InMemory-Anbieter galt.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-255">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

<span data-ttu-id="4ef3f-256">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-256">**New behavior**</span></span>

<span data-ttu-id="4ef3f-257">Anbieterspezifische Methoden wurden aktualisiert, sodass sie im Methodentext eine Ausnahme auslösen, um ihre Auswertung auf Clientseite zu blockieren.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-257">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

<span data-ttu-id="4ef3f-258">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-258">**Why**</span></span>

<span data-ttu-id="4ef3f-259">Anbieterspezifische Methoden sind einer Datenbankfunktion zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-259">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="4ef3f-260">Das Berechnungsergebnis der zugeordneten Datenbankfunktion kann auf Clientseite in LINQ nicht immer repliziert werden.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-260">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="4ef3f-261">Dies kann dazu führen, dass sich das Ergebnis von dem des Servers unterscheidet, wenn dieselbe Methode auf dem Client ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-261">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="4ef3f-262">Da diese Methoden in LINQ verwendet werden, um Übersetzungen in bestimmte Datenbankfunktionen durchzuführen, müssen sie nicht auf Clientseite ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-262">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="4ef3f-263">Da der InMemory-Anbieter eine andere *Datenbank* ist, sind die Methoden für diesen Anbieter nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-263">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="4ef3f-264">Wenn Sie versuchen, sie für den InMemory-Anbieter oder einen anderen Anbieter auszuführen, der diese Methoden nicht übersetzt, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-264">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

<span data-ttu-id="4ef3f-265">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="4ef3f-265">**Mitigations**</span></span>

<span data-ttu-id="4ef3f-266">Da es keine Möglichkeit gibt, das Verhalten von Datenbankfunktionen exakt zu imitieren, sollten Sie die Abfragen, die diese Methoden enthalten, mit demselben Datenbanktyp testen, der auch in der Produktionsumgebung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="4ef3f-266">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>
