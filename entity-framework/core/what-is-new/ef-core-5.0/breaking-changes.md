---
title: 'EF Core: Breaking Changes in EF Core 5.0'
description: Vollständige Liste der in Entity Framework Core 5.0 eingeführten Breaking Changes
author: bricelam
ms.date: 09/08/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: bc6db48edcd7406b31ec2b4369cabf5d55fb4578
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618677"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="59d0e-103">Breaking Changes in EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="59d0e-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="59d0e-104">Die folgenden API-Änderungen und Behavior Changes können dazu führen, dass vorhandene Anwendungen nach einem Upgrade auf EF Core 5.0.0 nicht mehr funktionieren.</span><span class="sxs-lookup"><span data-stu-id="59d0e-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="59d0e-105">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="59d0e-105">Summary</span></span>

| <span data-ttu-id="59d0e-106">**Wichtige Änderung**</span><span class="sxs-lookup"><span data-stu-id="59d0e-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="59d0e-107">**Auswirkungen**</span><span class="sxs-lookup"><span data-stu-id="59d0e-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="59d0e-108">Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität</span><span class="sxs-lookup"><span data-stu-id="59d0e-108">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="59d0e-109">Medium</span><span class="sxs-lookup"><span data-stu-id="59d0e-109">Medium</span></span>     |
| [<span data-ttu-id="59d0e-110">Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt</span><span class="sxs-lookup"><span data-stu-id="59d0e-110">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="59d0e-111">Niedrig</span><span class="sxs-lookup"><span data-stu-id="59d0e-111">Low</span></span>        |
| [<span data-ttu-id="59d0e-112">Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird</span><span class="sxs-lookup"><span data-stu-id="59d0e-112">Value generators are called when the entity state is changed from Detached to Unchanged, Updated or Deleted</span></span>](#non-added-generation)  | <span data-ttu-id="59d0e-113">Niedrig</span><span class="sxs-lookup"><span data-stu-id="59d0e-113">Low</span></span>        |
| [<span data-ttu-id="59d0e-114">IMigrationsModelDiffer verwendet jetzt IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="59d0e-114">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="59d0e-115">Niedrig</span><span class="sxs-lookup"><span data-stu-id="59d0e-115">Low</span></span>        |
| [<span data-ttu-id="59d0e-116">Diskriminatoren sind schreibgeschützt</span><span class="sxs-lookup"><span data-stu-id="59d0e-116">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="59d0e-117">Niedrig</span><span class="sxs-lookup"><span data-stu-id="59d0e-117">Low</span></span>        |

<a name="geometric-sqlite"></a>
### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="59d0e-118">Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt.</span><span class="sxs-lookup"><span data-stu-id="59d0e-118">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="59d0e-119">Issue 14257</span><span class="sxs-lookup"><span data-stu-id="59d0e-119">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="59d0e-120">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="59d0e-120">**Old behavior**</span></span>

<span data-ttu-id="59d0e-121">Die HasGeometricDimension-Methode wurde dazu verwendet, zusätzliche Maße (Z und M) für Geometriespalten zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="59d0e-121">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="59d0e-122">Dies hat sich jedoch immer nur auf die Datenbankerstellung ausgewirkt.</span><span class="sxs-lookup"><span data-stu-id="59d0e-122">However, it only ever affected database creation.</span></span> <span data-ttu-id="59d0e-123">Das Angeben dieser Methode zum Abfragen von Werten mit zusätzlichen Maßen war nicht notwendig.</span><span class="sxs-lookup"><span data-stu-id="59d0e-123">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="59d0e-124">Außerdem hat die Methode nicht ordnungsgemäß funktioniert, wenn Werte mit zusätzlichen Maßen eingefügt oder geändert wurden (siehe [Issue 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="59d0e-124">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="59d0e-125">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="59d0e-125">**New behavior**</span></span>

<span data-ttu-id="59d0e-126">Zum Ermöglichen des Einfügens und Änderns von Geometriewerten mit zusätzlichen Maßen (Z und M) muss das jeweilige Maß als Teil des Spaltentypnamens angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="59d0e-126">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="59d0e-127">Dies entspricht eher dem zugrunde liegenden Verhalten der AddGeometryColumn-Funktion von SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="59d0e-127">This more closely matches the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="59d0e-128">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="59d0e-128">**Why**</span></span>

<span data-ttu-id="59d0e-129">Die Verwendung der HasGeometricDimension-Methode, nachdem das Maß im Spaltentyp festgelegt wurde, ist redundant und nicht notwendig. Daher wurde die gesamte HasGeometricDimension-Methode entfernt.</span><span class="sxs-lookup"><span data-stu-id="59d0e-129">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="59d0e-130">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="59d0e-130">**Mitigations**</span></span>

<span data-ttu-id="59d0e-131">Verwenden Sie `HasColumnType` zum Festlegen der Maße:</span><span class="sxs-lookup"><span data-stu-id="59d0e-131">Use `HasColumnType` to specify the dimension:</span></span>

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
### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="59d0e-132">Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität</span><span class="sxs-lookup"><span data-stu-id="59d0e-132">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="59d0e-133">Tracking Issue 17286</span><span class="sxs-lookup"><span data-stu-id="59d0e-133">Tracking Issue #17286</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

<span data-ttu-id="59d0e-134">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="59d0e-134">**Old behavior**</span></span>

<span data-ttu-id="59d0e-135">Nur die Navigation zum Prinzipal konnte als erforderlich konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="59d0e-135">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="59d0e-136">Daher führte die Verwendung von `RequiredAttribute` für die Navigation zur abhängigen Entität (der Entität, die den Fremdschlüssel enthält) stattdessen zur Erstellung des Fremdschlüssels für den definierenden Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="59d0e-136">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

<span data-ttu-id="59d0e-137">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="59d0e-137">**New behavior**</span></span>

<span data-ttu-id="59d0e-138">Dank der hinzugefügten Unterstützung von erforderlichen abhängigen Entitäten ist es nun möglich, jede beliebige Verweisnavigation als erforderlich zu markieren. Dies bedeutet, dass im obigen Fall der Fremdschlüssel auf der anderen Seite der Beziehung definiert wird und die Eigenschaften nicht als erforderlich markiert werden.</span><span class="sxs-lookup"><span data-stu-id="59d0e-138">With the added support for required dependents it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="59d0e-139">Das Aufrufen von `IsRequired` vor der Angabe der abhängigen Entität ist nun mehrdeutig:</span><span class="sxs-lookup"><span data-stu-id="59d0e-139">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

<span data-ttu-id="59d0e-140">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="59d0e-140">**Why**</span></span>

<span data-ttu-id="59d0e-141">Das neue Verhalten ist erforderlich, um die Unterstützung von erforderlichen abhängigen Entitäten zu ermöglichen ([siehe 12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="59d0e-141">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

<span data-ttu-id="59d0e-142">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="59d0e-142">**Mitigations**</span></span>

<span data-ttu-id="59d0e-143">Entfernen Sie `RequiredAttribute` aus der Navigation zur abhängigen Entität, und verwenden Sie „RequiredAttribute“ stattdessen in der Navigation zum Prinzipal, oder konfigurieren Sie die Beziehung in `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="59d0e-143">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="non-added-generation"></a>
### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="59d0e-144">Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird</span><span class="sxs-lookup"><span data-stu-id="59d0e-144">Value generators are called when the entity state is changed from Detached to Unchanged, Updated or Deleted</span></span>

[<span data-ttu-id="59d0e-145">Tracking Issue 15289</span><span class="sxs-lookup"><span data-stu-id="59d0e-145">Tracking Issue #15289</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

<span data-ttu-id="59d0e-146">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="59d0e-146">**Old behavior**</span></span>

<span data-ttu-id="59d0e-147">Wert-Generatoren wurden nur aufgerufen, wenn der Entitätszustand in „Hinzugefügt“ geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="59d0e-147">Value generators were only called when the entity state changed to Added.</span></span>

<span data-ttu-id="59d0e-148">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="59d0e-148">**New behavior**</span></span>

<span data-ttu-id="59d0e-149">Wert-Generatoren werden jetzt aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird und die Eigenschaft die Standardwerte enthält.</span><span class="sxs-lookup"><span data-stu-id="59d0e-149">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated or Deleted and the property contains the default values.</span></span>

<span data-ttu-id="59d0e-150">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="59d0e-150">**Why**</span></span>

<span data-ttu-id="59d0e-151">Diese Änderung war erforderlich, um die Verwendung von Eigenschaften zu verbessern, die nicht dauerhaft im Datenspeicher gespeichert sind und deren Wert immer auf dem Client generiert wird.</span><span class="sxs-lookup"><span data-stu-id="59d0e-151">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

<span data-ttu-id="59d0e-152">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="59d0e-152">**Mitigations**</span></span>

<span data-ttu-id="59d0e-153">Weisen Sie der Eigenschaft vor der Änderung des Status einen anderen Wert als den Standardwert zu, um zu verhindern, dass der Wert-Generator aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="59d0e-153">To prevent the value generator from being called assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>
### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="59d0e-154">IMigrationsModelDiffer verwendet jetzt IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="59d0e-154">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="59d0e-155">Tracking Issue 20305</span><span class="sxs-lookup"><span data-stu-id="59d0e-155">Tracking Issue #20305</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

<span data-ttu-id="59d0e-156">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="59d0e-156">**Old behavior**</span></span>

<span data-ttu-id="59d0e-157">Die `IMigrationsModelDiffer`-API wurde mithilfe von `IModel` definiert.</span><span class="sxs-lookup"><span data-stu-id="59d0e-157">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

<span data-ttu-id="59d0e-158">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="59d0e-158">**New behavior**</span></span>

<span data-ttu-id="59d0e-159">Die `IMigrationsModelDiffer`-API verwendet jetzt `IRelationalModel`.</span><span class="sxs-lookup"><span data-stu-id="59d0e-159">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="59d0e-160">Die Modellmomentaufnahme enthält jedoch weiterhin nur `IModel`, da dieser Code Teil der Anwendung ist und Entity Framework ihn nicht ohne einen größeren Breaking Change ändern kann.</span><span class="sxs-lookup"><span data-stu-id="59d0e-160">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

<span data-ttu-id="59d0e-161">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="59d0e-161">**Why**</span></span>

<span data-ttu-id="59d0e-162">`IRelationalModel` ist eine neu hinzugefügte Darstellung des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="59d0e-162">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="59d0e-163">Die Verwendung dieser Darstellung zum Ermitteln von Unterschieden ist schneller und präziser.</span><span class="sxs-lookup"><span data-stu-id="59d0e-163">Using it to find differences is faster and more accurate.</span></span>

<span data-ttu-id="59d0e-164">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="59d0e-164">**Mitigations**</span></span>

<span data-ttu-id="59d0e-165">Verwenden Sie den folgenden Code, um das Modell von `snapshot` mit dem Modell von `context` zu vergleichen:</span><span class="sxs-lookup"><span data-stu-id="59d0e-165">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="59d0e-166">Eine Verbesserung dieser Funktion in Version 6.0 ist geplant ([siehe 22031](https://github.com/dotnet/efcore/issues/22031)).</span><span class="sxs-lookup"><span data-stu-id="59d0e-166">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>
### <a name="discriminators-are-read-only"></a><span data-ttu-id="59d0e-167">Diskriminatoren sind schreibgeschützt</span><span class="sxs-lookup"><span data-stu-id="59d0e-167">Discriminators are read-only</span></span>

[<span data-ttu-id="59d0e-168">Tracking Issue 21154</span><span class="sxs-lookup"><span data-stu-id="59d0e-168">Tracking Issue #21154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

<span data-ttu-id="59d0e-169">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="59d0e-169">**Old behavior**</span></span>

<span data-ttu-id="59d0e-170">Der Diskriminatorwert konnte vor dem Aufrufen von `SaveChanges` geändert werden.</span><span class="sxs-lookup"><span data-stu-id="59d0e-170">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

<span data-ttu-id="59d0e-171">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="59d0e-171">**New behavior**</span></span>

<span data-ttu-id="59d0e-172">Im obigen Fall wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="59d0e-172">An exception will be throws in the above case.</span></span>

<span data-ttu-id="59d0e-173">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="59d0e-173">**Why**</span></span>

<span data-ttu-id="59d0e-174">EF erwartet nicht, dass der Entitätstyp geändert wird, während er noch nachverfolgt wird, sodass das Ändern des Diskriminatorwerts zu einem inkonsistenten Kontext führt. Dies hat möglicherweise ein unerwartetes Verhalten zur Folge.</span><span class="sxs-lookup"><span data-stu-id="59d0e-174">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state which might result in unexpected behavior.</span></span>

<span data-ttu-id="59d0e-175">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="59d0e-175">**Mitigations**</span></span>

<span data-ttu-id="59d0e-176">Wenn eine Änderung des Diskriminatorwerts erforderlich ist und der Kontext direkt nach dem Aufruf von `SaveChanges` verworfen wird, kann der Diskriminator als änderbar festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="59d0e-176">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges` the discriminator can be made mutable:</span></span>

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```
