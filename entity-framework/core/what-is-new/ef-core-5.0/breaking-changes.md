---
title: 'EF Core: Breaking Changes in EF Core 5.0'
description: Vollständige Liste der in Entity Framework Core 5.0 eingeführten Breaking Changes
author: bricelam
ms.date: 09/09/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 63fd1d1a01b7a72fd34bb9a0130191131306426c
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070795"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="dcf03-103">Breaking Changes in EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="dcf03-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="dcf03-104">Die folgenden API-Änderungen und Behavior Changes können dazu führen, dass vorhandene Anwendungen nach einem Upgrade auf EF Core 5.0.0 nicht mehr funktionieren.</span><span class="sxs-lookup"><span data-stu-id="dcf03-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="dcf03-105">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="dcf03-105">Summary</span></span>

| <span data-ttu-id="dcf03-106">**Wichtige Änderung**</span><span class="sxs-lookup"><span data-stu-id="dcf03-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="dcf03-107">**Auswirkungen**</span><span class="sxs-lookup"><span data-stu-id="dcf03-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="dcf03-108">Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität</span><span class="sxs-lookup"><span data-stu-id="dcf03-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="dcf03-109">Medium</span><span class="sxs-lookup"><span data-stu-id="dcf03-109">Medium</span></span>     |
| [<span data-ttu-id="dcf03-110">Die definierende Abfrage wurde durch anbieterspezifische Methoden ersetzt</span><span class="sxs-lookup"><span data-stu-id="dcf03-110">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="dcf03-111">Medium</span><span class="sxs-lookup"><span data-stu-id="dcf03-111">Medium</span></span>     |
| [<span data-ttu-id="dcf03-112">Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt</span><span class="sxs-lookup"><span data-stu-id="dcf03-112">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="dcf03-113">Niedrig</span><span class="sxs-lookup"><span data-stu-id="dcf03-113">Low</span></span>        |
| [<span data-ttu-id="dcf03-114">Cosmos: Der Partitionsschlüssel wird nun dem Primärschlüssel hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="dcf03-114">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="dcf03-115">Niedrig</span><span class="sxs-lookup"><span data-stu-id="dcf03-115">Low</span></span>        |
| [<span data-ttu-id="dcf03-116">Cosmos: Die `id`-Eigenschaft wurde umbenannt in `__id`</span><span class="sxs-lookup"><span data-stu-id="dcf03-116">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="dcf03-117">Niedrig</span><span class="sxs-lookup"><span data-stu-id="dcf03-117">Low</span></span>        |
| <span data-ttu-id="dcf03-118">[Cosmos: byte[] wird nun als Base64-Zeichenfolge statt als Zahlenarray gespeichert](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="dcf03-118">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="dcf03-119">Niedrig</span><span class="sxs-lookup"><span data-stu-id="dcf03-119">Low</span></span>        |
| [<span data-ttu-id="dcf03-120">Cosmos: GetPropertyName und SetPropertyName wurden umbenannt</span><span class="sxs-lookup"><span data-stu-id="dcf03-120">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="dcf03-121">Niedrig</span><span class="sxs-lookup"><span data-stu-id="dcf03-121">Low</span></span>        |
| [<span data-ttu-id="dcf03-122">Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird</span><span class="sxs-lookup"><span data-stu-id="dcf03-122">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="dcf03-123">Niedrig</span><span class="sxs-lookup"><span data-stu-id="dcf03-123">Low</span></span>        |
| [<span data-ttu-id="dcf03-124">IMigrationsModelDiffer verwendet jetzt IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="dcf03-124">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="dcf03-125">Niedrig</span><span class="sxs-lookup"><span data-stu-id="dcf03-125">Low</span></span>        |
| [<span data-ttu-id="dcf03-126">Diskriminatoren sind schreibgeschützt</span><span class="sxs-lookup"><span data-stu-id="dcf03-126">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="dcf03-127">Niedrig</span><span class="sxs-lookup"><span data-stu-id="dcf03-127">Low</span></span>        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="dcf03-128">Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt.</span><span class="sxs-lookup"><span data-stu-id="dcf03-128">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="dcf03-129">Issue 14257</span><span class="sxs-lookup"><span data-stu-id="dcf03-129">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="dcf03-130">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-130">**Old behavior**</span></span>

<span data-ttu-id="dcf03-131">Die HasGeometricDimension-Methode wurde dazu verwendet, zusätzliche Maße (Z und M) für Geometriespalten zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="dcf03-131">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="dcf03-132">Dies hat sich jedoch immer nur auf die Datenbankerstellung ausgewirkt.</span><span class="sxs-lookup"><span data-stu-id="dcf03-132">However, it only ever affected database creation.</span></span> <span data-ttu-id="dcf03-133">Das Angeben dieser Methode zum Abfragen von Werten mit zusätzlichen Maßen war nicht notwendig.</span><span class="sxs-lookup"><span data-stu-id="dcf03-133">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="dcf03-134">Außerdem hat die Methode nicht ordnungsgemäß funktioniert, wenn Werte mit zusätzlichen Maßen eingefügt oder geändert wurden (siehe [Issue 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="dcf03-134">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="dcf03-135">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-135">**New behavior**</span></span>

<span data-ttu-id="dcf03-136">Zum Ermöglichen des Einfügens und Änderns von Geometriewerten mit zusätzlichen Maßen (Z und M) muss das jeweilige Maß als Teil des Spaltentypnamens angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="dcf03-136">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="dcf03-137">Diese API entspricht eher dem zugrunde liegenden Verhalten der AddGeometryColumn-Funktion von SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="dcf03-137">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="dcf03-138">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="dcf03-138">**Why**</span></span>

<span data-ttu-id="dcf03-139">Die Verwendung der HasGeometricDimension-Methode, nachdem das Maß im Spaltentyp festgelegt wurde, ist redundant und nicht notwendig. Daher wurde die gesamte HasGeometricDimension-Methode entfernt.</span><span class="sxs-lookup"><span data-stu-id="dcf03-139">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="dcf03-140">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="dcf03-140">**Mitigations**</span></span>

<span data-ttu-id="dcf03-141">Verwenden Sie `HasColumnType` zum Festlegen der Maße:</span><span class="sxs-lookup"><span data-stu-id="dcf03-141">Use `HasColumnType` to specify the dimension:</span></span>

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

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="dcf03-142">Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität</span><span class="sxs-lookup"><span data-stu-id="dcf03-142">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="dcf03-143">Tracking Issue 17286</span><span class="sxs-lookup"><span data-stu-id="dcf03-143">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="dcf03-144">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-144">**Old behavior**</span></span>

<span data-ttu-id="dcf03-145">Nur die Navigation zum Prinzipal konnte als erforderlich konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="dcf03-145">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="dcf03-146">Daher führte die Verwendung von `RequiredAttribute` für die Navigation zur abhängigen Entität (der Entität, die den Fremdschlüssel enthält) stattdessen zur Erstellung des Fremdschlüssels für den definierenden Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="dcf03-146">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="dcf03-147">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-147">**New behavior**</span></span>

<span data-ttu-id="dcf03-148">Dank der hinzugefügten Unterstützung von erforderlichen abhängigen Entitäten ist es nun möglich, jede beliebige Verweisnavigation als erforderlich zu markieren. Dies bedeutet, dass im obigen Fall der Fremdschlüssel auf der anderen Seite der Beziehung definiert wird und die Eigenschaften nicht als erforderlich markiert werden.</span><span class="sxs-lookup"><span data-stu-id="dcf03-148">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="dcf03-149">Das Aufrufen von `IsRequired` vor der Angabe der abhängigen Entität ist nun mehrdeutig:</span><span class="sxs-lookup"><span data-stu-id="dcf03-149">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="dcf03-150">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="dcf03-150">**Why**</span></span>

<span data-ttu-id="dcf03-151">Das neue Verhalten ist erforderlich, um die Unterstützung von erforderlichen abhängigen Entitäten zu ermöglichen ([siehe 12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="dcf03-151">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="dcf03-152">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="dcf03-152">**Mitigations**</span></span>

<span data-ttu-id="dcf03-153">Entfernen Sie `RequiredAttribute` aus der Navigation zur abhängigen Entität, und verwenden Sie „RequiredAttribute“ stattdessen in der Navigation zum Prinzipal, oder konfigurieren Sie die Beziehung in `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="dcf03-153">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="dcf03-154">Cosmos: Der Partitionsschlüssel wird nun dem Primärschlüssel hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="dcf03-154">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="dcf03-155">Tracking Issue 15289</span><span class="sxs-lookup"><span data-stu-id="dcf03-155">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="dcf03-156">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-156">**Old behavior**</span></span>

<span data-ttu-id="dcf03-157">Die Partitionsschlüsseleigenschaft wurde nur dem alternativen Schlüssel hinzugefügt, der `id` enthält.</span><span class="sxs-lookup"><span data-stu-id="dcf03-157">The partition key property was only added to the alternate key that includes `id`.</span></span>

<span data-ttu-id="dcf03-158">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-158">**New behavior**</span></span>

<span data-ttu-id="dcf03-159">Die Partitionsschlüsseleigenschaft wird gemäß Konvention jetzt auch dem Primärschlüssel hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="dcf03-159">The partition key property is now also added to the primary key by convention.</span></span>

<span data-ttu-id="dcf03-160">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="dcf03-160">**Why**</span></span>

<span data-ttu-id="dcf03-161">Durch diese Änderung ist das Modell besser auf die Azure Cosmos DB-Semantik abgestimmt. Zudem verbessert sich dadurch die Leistung von `Find` und einigen Abfragen.</span><span class="sxs-lookup"><span data-stu-id="dcf03-161">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

<span data-ttu-id="dcf03-162">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="dcf03-162">**Mitigations**</span></span>

<span data-ttu-id="dcf03-163">Um zu verhindern, dass die Partitionsschlüsseleigenschaft dem Primärschlüssel hinzugefügt wird, konfigurieren Sie sie in `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="dcf03-163">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="dcf03-164">Cosmos: Die `id`-Eigenschaft wurde umbenannt in `__id`</span><span class="sxs-lookup"><span data-stu-id="dcf03-164">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="dcf03-165">Issue 17751</span><span class="sxs-lookup"><span data-stu-id="dcf03-165">Tracking Issue #17751</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

<span data-ttu-id="dcf03-166">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-166">**Old behavior**</span></span>

<span data-ttu-id="dcf03-167">Die der JSON-Eigenschaft `id` zugeordnete Schatteneigenschaft hatte ebenfalls den Namen `id`.</span><span class="sxs-lookup"><span data-stu-id="dcf03-167">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

<span data-ttu-id="dcf03-168">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-168">**New behavior**</span></span>

<span data-ttu-id="dcf03-169">Die gemäß Konvention erstellte Schatteneigenschaft trägt nun den Namen `__id`.</span><span class="sxs-lookup"><span data-stu-id="dcf03-169">The shadow property created by convention is now named `__id`.</span></span>

<span data-ttu-id="dcf03-170">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="dcf03-170">**Why**</span></span>

<span data-ttu-id="dcf03-171">Durch diese Änderung ist es weniger wahrscheinlich, dass die Eigenschaft `id` mit einer vorhandenen Eigenschaft des Entitätstyps kollidiert.</span><span class="sxs-lookup"><span data-stu-id="dcf03-171">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

<span data-ttu-id="dcf03-172">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="dcf03-172">**Mitigations**</span></span>

<span data-ttu-id="dcf03-173">Wenn Sie zum Verhalten der Version 3.x zurückkehren möchten, konfigurieren Sie die Eigenschaft `id` in `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="dcf03-173">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```cs
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="dcf03-174">Cosmos: byte[] wird nun als Base64-Zeichenfolge statt als Zahlenarray gespeichert</span><span class="sxs-lookup"><span data-stu-id="dcf03-174">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="dcf03-175">Issue 17306</span><span class="sxs-lookup"><span data-stu-id="dcf03-175">Tracking Issue #17306</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

<span data-ttu-id="dcf03-176">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-176">**Old behavior**</span></span>

<span data-ttu-id="dcf03-177">Eigenschaften vom Typ „byte[]“ wurden als Zahlenarray gespeichert.</span><span class="sxs-lookup"><span data-stu-id="dcf03-177">Properties of type byte[] were stored as a number array.</span></span>

<span data-ttu-id="dcf03-178">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-178">**New behavior**</span></span>

<span data-ttu-id="dcf03-179">Eigenschaften vom Typ „byte[]“ werden nun als Base64-Zeichenfolge gespeichert.</span><span class="sxs-lookup"><span data-stu-id="dcf03-179">Properties of type byte[] are now stored as a base64 string.</span></span>

<span data-ttu-id="dcf03-180">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="dcf03-180">**Why**</span></span>

<span data-ttu-id="dcf03-181">Diese Darstellung von „byte[]“ entspricht eher den Erwartungen. Zudem handelt es sich hier um das Standardverhalten der wichtigen JSON-Serialisierungsbibliotheken.</span><span class="sxs-lookup"><span data-stu-id="dcf03-181">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

<span data-ttu-id="dcf03-182">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="dcf03-182">**Mitigations**</span></span>

<span data-ttu-id="dcf03-183">Als Zahlenarrays gespeicherte, bereits vorhandene Daten werden weiterhin ordnungsgemäß abgefragt. Derzeit wird jedoch keine Möglichkeit unterstützt, das Einfügeverhalten zu ändern.</span><span class="sxs-lookup"><span data-stu-id="dcf03-183">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="dcf03-184">Wenn Ihr Szenario durch diese Einschränkung blockiert wird, kommentieren Sie dieses [Problem](https://github.com/aspnet/EntityFrameworkCore/issues/17306).</span><span class="sxs-lookup"><span data-stu-id="dcf03-184">If this limitation is blocking your scenario, comment on [this issue](https://github.com/aspnet/EntityFrameworkCore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="dcf03-185">Cosmos: GetPropertyName und SetPropertyName wurden umbenannt</span><span class="sxs-lookup"><span data-stu-id="dcf03-185">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="dcf03-186">Issue 17874</span><span class="sxs-lookup"><span data-stu-id="dcf03-186">Tracking Issue #17874</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

<span data-ttu-id="dcf03-187">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-187">**Old behavior**</span></span>

<span data-ttu-id="dcf03-188">Bisher wurden die Erweiterungsmethoden `GetPropertyName` und `SetPropertyName` genannt.</span><span class="sxs-lookup"><span data-stu-id="dcf03-188">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

<span data-ttu-id="dcf03-189">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-189">**New behavior**</span></span>

<span data-ttu-id="dcf03-190">Die alte API ist veraltet, und es wurden neue Methoden hinzugefügt: `GetJsonPropertyName`, `SetJsonPropertyName`</span><span class="sxs-lookup"><span data-stu-id="dcf03-190">The old API was obsoleted and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

<span data-ttu-id="dcf03-191">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="dcf03-191">**Why**</span></span>

<span data-ttu-id="dcf03-192">Durch diese Änderung wird die Mehrdeutigkeit um die Konfiguration durch diese Methoden beseitigt.</span><span class="sxs-lookup"><span data-stu-id="dcf03-192">This change removes the ambiguity around what these methods are configuring.</span></span>

<span data-ttu-id="dcf03-193">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="dcf03-193">**Mitigations**</span></span>

<span data-ttu-id="dcf03-194">Verwenden Sie die neue API, oder sperren Sie die veralteten Warnungen vorübergehend.</span><span class="sxs-lookup"><span data-stu-id="dcf03-194">Use the new API or temporarily suspend the obsolete warnings.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="dcf03-195">Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird</span><span class="sxs-lookup"><span data-stu-id="dcf03-195">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="dcf03-196">Tracking Issue 15289</span><span class="sxs-lookup"><span data-stu-id="dcf03-196">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="dcf03-197">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-197">**Old behavior**</span></span>

<span data-ttu-id="dcf03-198">Wert-Generatoren wurden nur aufgerufen, wenn der Entitätszustand in „Hinzugefügt“ geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="dcf03-198">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="dcf03-199">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-199">**New behavior**</span></span>

<span data-ttu-id="dcf03-200">Wert-Generatoren werden jetzt aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird und die Eigenschaft die Standardwerte enthält.</span><span class="sxs-lookup"><span data-stu-id="dcf03-200">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="dcf03-201">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="dcf03-201">**Why**</span></span>

<span data-ttu-id="dcf03-202">Diese Änderung war erforderlich, um die Verwendung von Eigenschaften zu verbessern, die nicht dauerhaft im Datenspeicher gespeichert sind und deren Wert immer auf dem Client generiert wird.</span><span class="sxs-lookup"><span data-stu-id="dcf03-202">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="dcf03-203">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="dcf03-203">**Mitigations**</span></span>

<span data-ttu-id="dcf03-204">Weisen Sie der Eigenschaft vor der Änderung des Status einen anderen Wert als den Standardwert zu, um zu verhindern, dass der Wert-Generator aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="dcf03-204">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="dcf03-205">IMigrationsModelDiffer verwendet jetzt IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="dcf03-205">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="dcf03-206">Tracking Issue 20305</span><span class="sxs-lookup"><span data-stu-id="dcf03-206">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="dcf03-207">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-207">**Old behavior**</span></span>

<span data-ttu-id="dcf03-208">Die `IMigrationsModelDiffer`-API wurde mithilfe von `IModel` definiert.</span><span class="sxs-lookup"><span data-stu-id="dcf03-208">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="dcf03-209">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-209">**New behavior**</span></span>

<span data-ttu-id="dcf03-210">Die `IMigrationsModelDiffer`-API verwendet jetzt `IRelationalModel`.</span><span class="sxs-lookup"><span data-stu-id="dcf03-210">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="dcf03-211">Die Modellmomentaufnahme enthält jedoch weiterhin nur `IModel`, da dieser Code Teil der Anwendung ist und Entity Framework ihn nicht ohne einen größeren Breaking Change ändern kann.</span><span class="sxs-lookup"><span data-stu-id="dcf03-211">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="dcf03-212">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="dcf03-212">**Why**</span></span>

<span data-ttu-id="dcf03-213">`IRelationalModel` ist eine neu hinzugefügte Darstellung des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="dcf03-213">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="dcf03-214">Die Verwendung dieser Darstellung zum Ermitteln von Unterschieden ist schneller und präziser.</span><span class="sxs-lookup"><span data-stu-id="dcf03-214">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="dcf03-215">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="dcf03-215">**Mitigations**</span></span>

<span data-ttu-id="dcf03-216">Verwenden Sie den folgenden Code, um das Modell von `snapshot` mit dem Modell von `context` zu vergleichen:</span><span class="sxs-lookup"><span data-stu-id="dcf03-216">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="dcf03-217">Eine Verbesserung dieser Funktion in Version 6.0 ist geplant ([siehe 22031](https://github.com/dotnet/efcore/issues/22031)).</span><span class="sxs-lookup"><span data-stu-id="dcf03-217">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="dcf03-218">Diskriminatoren sind schreibgeschützt</span><span class="sxs-lookup"><span data-stu-id="dcf03-218">Discriminators are read-only</span></span>

[<span data-ttu-id="dcf03-219">Tracking Issue 21154</span><span class="sxs-lookup"><span data-stu-id="dcf03-219">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="dcf03-220">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-220">**Old behavior**</span></span>

<span data-ttu-id="dcf03-221">Der Diskriminatorwert konnte vor dem Aufrufen von `SaveChanges` geändert werden.</span><span class="sxs-lookup"><span data-stu-id="dcf03-221">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="dcf03-222">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-222">**New behavior**</span></span>

<span data-ttu-id="dcf03-223">Im obigen Fall wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="dcf03-223">An exception will be throws in the above case.</span></span>

<span data-ttu-id="dcf03-224">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="dcf03-224">**Why**</span></span>

<span data-ttu-id="dcf03-225">EF erwartet nicht, dass der Entitätstyp geändert wird, während er noch nachverfolgt wird, sodass das Ändern des Diskriminatorwerts zu einem inkonsistenten Kontext führt. Dies hat möglicherweise ein unerwartetes Verhalten zur Folge.</span><span class="sxs-lookup"><span data-stu-id="dcf03-225">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

<span data-ttu-id="dcf03-226">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="dcf03-226">**Mitigations**</span></span>

<span data-ttu-id="dcf03-227">Wenn eine Änderung des Diskriminatorwerts erforderlich ist und der Kontext direkt nach dem Aufruf von `SaveChanges` verworfen wird, kann der Diskriminator als änderbar festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="dcf03-227">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="dcf03-228">Die definierende Abfrage wurde durch anbieterspezifische Methoden ersetzt</span><span class="sxs-lookup"><span data-stu-id="dcf03-228">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="dcf03-229">Issue 18903</span><span class="sxs-lookup"><span data-stu-id="dcf03-229">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

<span data-ttu-id="dcf03-230">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-230">**Old behavior**</span></span>

<span data-ttu-id="dcf03-231">Entitätstypen wurden zum Definieren von Abfragen auf der Kernebene zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="dcf03-231">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="dcf03-232">Jedes Mal, wenn der Entitätstyp im Abfragestamm des Entitätstyps verwendet wurde, wurde er bei allen Anbietern durch die definierende Abfrage ersetzt.</span><span class="sxs-lookup"><span data-stu-id="dcf03-232">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

<span data-ttu-id="dcf03-233">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="dcf03-233">**New behavior**</span></span>

<span data-ttu-id="dcf03-234">APIs für definierende Abfragen sind veraltet.</span><span class="sxs-lookup"><span data-stu-id="dcf03-234">APIs for defining query are deprecated.</span></span> <span data-ttu-id="dcf03-235">Es wurden neue anbieterspezifische APIs eingeführt.</span><span class="sxs-lookup"><span data-stu-id="dcf03-235">New provider-specific APIs were introduced.</span></span>

<span data-ttu-id="dcf03-236">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="dcf03-236">**Why**</span></span>

<span data-ttu-id="dcf03-237">Während definierende Abfragen als Ersatzabfragen implementiert wurden, wenn in der Abfrage der Abfragestamm verwendet wird, sind einige Probleme aufgetreten:</span><span class="sxs-lookup"><span data-stu-id="dcf03-237">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="dcf03-238">Wenn eine definierende Abfrage einen Entitätstyp mit `new { ... }` in die `Select`-Methode projiziert, war für die Identifizierung dessen als Entität zusätzliche Arbeit erforderlich. Zudem sorgte dieses Vorgehen für Inkonsistenzen in Bezug darauf, wie EF Core nominale Typen in der Abfrage behandelt.</span><span class="sxs-lookup"><span data-stu-id="dcf03-238">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="dcf03-239">Bei relationalen Anbietern ist `FromSql` weiterhin erforderlich, um die SQL-Zeichenfolge im LINQ-Ausdrucksformat zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="dcf03-239">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="dcf03-240">Zunächst wurden definierende Abfragen als clientseitige Sichten zur Verwendung mit In-Memory-Anbietern bei schlüssellosen Entitäten eingeführt (ähnlich wie bei Datenbanksichten in relationalen Datenbanken).</span><span class="sxs-lookup"><span data-stu-id="dcf03-240">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="dcf03-241">Eine solche Definition erleichtert das Testen der Anwendung mit In-Memory-Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="dcf03-241">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="dcf03-242">Später wurden sie allgemein anwendbar, was zwar nützlich war, aber ein inkonsistentes und schwer verständliches Verhalten mit sich brachte.</span><span class="sxs-lookup"><span data-stu-id="dcf03-242">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="dcf03-243">Daher haben wir uns entschieden, das Konzept zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="dcf03-243">So we decided to simplify the concept.</span></span> <span data-ttu-id="dcf03-244">Wir stellen die LINQ-basierte definierende Abfrage nur noch für In-Memory-Anbieter bereit und behandeln sie anders.</span><span class="sxs-lookup"><span data-stu-id="dcf03-244">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="dcf03-245">Weitere Informationen finden Sie in [diesem Thema](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="dcf03-245">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

<span data-ttu-id="dcf03-246">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="dcf03-246">**Mitigations**</span></span>

<span data-ttu-id="dcf03-247">Verwenden Sie für relationale Anbieter die `ToSqlQuery`-Methode in `OnModelCreating`, und übergeben Sie eine SQL-Zeichenfolge zur Verwendung für den Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="dcf03-247">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="dcf03-248">Verwenden Sie für den In-Memory-Anbieter die `ToInMemoryQuery`-Methode in `OnModelCreating`, und übergeben Sie eine LINQ-Abfrage zur Verwendung für den Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="dcf03-248">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>
