---
title: 'EF Core: Breaking Changes in EF Core 5.0'
description: Vollständige Liste der in Entity Framework Core 5.0 eingeführten Breaking Changes
author: bricelam
ms.date: 11/07/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 4a463e785edaceaf5dd96164c39e2cc9b5f86de4
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128744"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="11201-103">Breaking Changes in EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="11201-103">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="11201-104">Die folgenden API-Änderungen und Behavior Changes können dazu führen, dass vorhandene Anwendungen nach einem Upgrade auf EF Core 5.0.0 nicht mehr funktionieren.</span><span class="sxs-lookup"><span data-stu-id="11201-104">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="11201-105">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="11201-105">Summary</span></span>

| <span data-ttu-id="11201-106">**Wichtige Änderung**</span><span class="sxs-lookup"><span data-stu-id="11201-106">**Breaking change**</span></span>                                                                                                                   | <span data-ttu-id="11201-107">**Auswirkungen**</span><span class="sxs-lookup"><span data-stu-id="11201-107">**Impact**</span></span> |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="11201-108">EF Core 5.0 unterstützt .NET Framework nicht</span><span class="sxs-lookup"><span data-stu-id="11201-108">EF Core 5.0 does not support .NET Framework</span></span>](#netstandard21)                                                                         | <span data-ttu-id="11201-109">Medium</span><span class="sxs-lookup"><span data-stu-id="11201-109">Medium</span></span>     |
| [<span data-ttu-id="11201-110">IProperty.GetColumnName() gilt ab sofort als veraltet</span><span class="sxs-lookup"><span data-stu-id="11201-110">IProperty.GetColumnName() is now obsolete</span></span>](#getcolumnname-obsolete)                                                                  | <span data-ttu-id="11201-111">Medium</span><span class="sxs-lookup"><span data-stu-id="11201-111">Medium</span></span>     |
| [<span data-ttu-id="11201-112">Genauigkeit und Skalierung sind für Dezimale erforderlich</span><span class="sxs-lookup"><span data-stu-id="11201-112">Precision and scale are required for decimals</span></span>](#decimals)                                                                            | <span data-ttu-id="11201-113">Medium</span><span class="sxs-lookup"><span data-stu-id="11201-113">Medium</span></span>     |
| [<span data-ttu-id="11201-114">Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität</span><span class="sxs-lookup"><span data-stu-id="11201-114">Required on the navigation from principal to dependent has different semantics</span></span>](#required-dependent)                                 | <span data-ttu-id="11201-115">Medium</span><span class="sxs-lookup"><span data-stu-id="11201-115">Medium</span></span>     |
| [<span data-ttu-id="11201-116">Die definierende Abfrage wurde durch anbieterspezifische Methoden ersetzt</span><span class="sxs-lookup"><span data-stu-id="11201-116">Defining query is replaced with provider-specific methods</span></span>](#defining-query)                                                          | <span data-ttu-id="11201-117">Medium</span><span class="sxs-lookup"><span data-stu-id="11201-117">Medium</span></span>     |
| [<span data-ttu-id="11201-118">Navigationen ohne Nullverweis werden von Abfragen nicht überschrieben</span><span class="sxs-lookup"><span data-stu-id="11201-118">Non-null reference navigations are not overwritten by queries</span></span>](#nonnullreferences)                                                   | <span data-ttu-id="11201-119">Medium</span><span class="sxs-lookup"><span data-stu-id="11201-119">Medium</span></span>     |
| [<span data-ttu-id="11201-120">ToView() wird von Migrationen anders verarbeitet</span><span class="sxs-lookup"><span data-stu-id="11201-120">ToView() is treated differently by migrations</span></span>](#toview)                                                                              | <span data-ttu-id="11201-121">Medium</span><span class="sxs-lookup"><span data-stu-id="11201-121">Medium</span></span>     |
| [<span data-ttu-id="11201-122">ToTable(null) markiert den Entitätstyp als nicht einer Tabelle zugeordnet</span><span class="sxs-lookup"><span data-stu-id="11201-122">ToTable(null) marks the entity type as not mapped to a table</span></span>](#totable)                                                              | <span data-ttu-id="11201-123">Medium</span><span class="sxs-lookup"><span data-stu-id="11201-123">Medium</span></span>     |
| [<span data-ttu-id="11201-124">Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt</span><span class="sxs-lookup"><span data-stu-id="11201-124">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#geometric-sqlite)                                                   | <span data-ttu-id="11201-125">Niedrig</span><span class="sxs-lookup"><span data-stu-id="11201-125">Low</span></span>        |
| [<span data-ttu-id="11201-126">Cosmos: Der Partitionsschlüssel wird nun dem Primärschlüssel hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="11201-126">Cosmos: Partition key is now added to the primary key</span></span>](#cosmos-partition-key)                                                        | <span data-ttu-id="11201-127">Niedrig</span><span class="sxs-lookup"><span data-stu-id="11201-127">Low</span></span>        |
| [<span data-ttu-id="11201-128">Cosmos: Die `id`-Eigenschaft wurde umbenannt in `__id`</span><span class="sxs-lookup"><span data-stu-id="11201-128">Cosmos: `id` property renamed to `__id`</span></span>](#cosmos-id)                                                                                 | <span data-ttu-id="11201-129">Niedrig</span><span class="sxs-lookup"><span data-stu-id="11201-129">Low</span></span>        |
| <span data-ttu-id="11201-130">[Cosmos: byte[] wird nun als Base64-Zeichenfolge statt als Zahlenarray gespeichert](#cosmos-byte)</span><span class="sxs-lookup"><span data-stu-id="11201-130">[Cosmos: byte[] is now stored as a base64 string instead of a number array](#cosmos-byte)</span></span>                                             | <span data-ttu-id="11201-131">Niedrig</span><span class="sxs-lookup"><span data-stu-id="11201-131">Low</span></span>        |
| [<span data-ttu-id="11201-132">Cosmos: GetPropertyName und SetPropertyName wurden umbenannt</span><span class="sxs-lookup"><span data-stu-id="11201-132">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>](#cosmos-metadata)                                                          | <span data-ttu-id="11201-133">Niedrig</span><span class="sxs-lookup"><span data-stu-id="11201-133">Low</span></span>        |
| [<span data-ttu-id="11201-134">Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird</span><span class="sxs-lookup"><span data-stu-id="11201-134">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>](#non-added-generation) | <span data-ttu-id="11201-135">Niedrig</span><span class="sxs-lookup"><span data-stu-id="11201-135">Low</span></span>        |
| [<span data-ttu-id="11201-136">IMigrationsModelDiffer verwendet jetzt IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="11201-136">IMigrationsModelDiffer now uses IRelationalModel</span></span>](#relational-model)                                                                 | <span data-ttu-id="11201-137">Niedrig</span><span class="sxs-lookup"><span data-stu-id="11201-137">Low</span></span>        |
| [<span data-ttu-id="11201-138">Diskriminatoren sind schreibgeschützt</span><span class="sxs-lookup"><span data-stu-id="11201-138">Discriminators are read-only</span></span>](#read-only-discriminators)                                                                             | <span data-ttu-id="11201-139">Niedrig</span><span class="sxs-lookup"><span data-stu-id="11201-139">Low</span></span>        |
| [<span data-ttu-id="11201-140">Anbieterspezifische EF.Functions-Methoden werden für InMemory-Anbieter ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="11201-140">Provider-specific EF.Functions methods throw for InMemory provider</span></span>](#no-client-methods)                                              | <span data-ttu-id="11201-141">Niedrig</span><span class="sxs-lookup"><span data-stu-id="11201-141">Low</span></span>        |
| [<span data-ttu-id="11201-142">IndexBuilder.HasName ist mittlerweile veraltet</span><span class="sxs-lookup"><span data-stu-id="11201-142">IndexBuilder.HasName is now obsolete</span></span>](#index-obsolete)                                                                               | <span data-ttu-id="11201-143">Niedrig</span><span class="sxs-lookup"><span data-stu-id="11201-143">Low</span></span>        |
| [<span data-ttu-id="11201-144">Ein Pluralizer ist nun für den Gerüstbau für Modelle mit Reverse Engineering enthalten</span><span class="sxs-lookup"><span data-stu-id="11201-144">A pluralizer is now included for scaffolding reverse engineered models</span></span>](#pluralizer)                                                 | <span data-ttu-id="11201-145">Niedrig</span><span class="sxs-lookup"><span data-stu-id="11201-145">Low</span></span>        |
| [<span data-ttu-id="11201-146">INavigationBase ersetzt INavigation in einigen APIs zur Unterstützung überspringender Navigationen</span><span class="sxs-lookup"><span data-stu-id="11201-146">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>](#inavigationbase)                                     | <span data-ttu-id="11201-147">Niedrig</span><span class="sxs-lookup"><span data-stu-id="11201-147">Low</span></span>        |
| [<span data-ttu-id="11201-148">Einige Abfragen mit korrelierter Sammlung, die ebenfalls `Distinct` oder `GroupBy` verwenden, werden nicht mehr unterstützt</span><span class="sxs-lookup"><span data-stu-id="11201-148">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>](#collection-distinct-groupby) | <span data-ttu-id="11201-149">Niedrig</span><span class="sxs-lookup"><span data-stu-id="11201-149">Low</span></span>        |
| [<span data-ttu-id="11201-150">Die Verwendung einer Sammlung abfragbarer Typen in der Projektion wird nicht unterstützt</span><span class="sxs-lookup"><span data-stu-id="11201-150">Using a collection of Queryable type in projection is not supported</span></span>](#queryable-projection)                                          | <span data-ttu-id="11201-151">Niedrig</span><span class="sxs-lookup"><span data-stu-id="11201-151">Low</span></span>        |

## <a name="medium-impact-changes"></a><span data-ttu-id="11201-152">Änderungen mit mittlerer Auswirkung</span><span class="sxs-lookup"><span data-stu-id="11201-152">Medium-impact changes</span></span>

<a name="netstandard21"></a>

### <a name="ef-core-50-does-not-support-net-framework"></a><span data-ttu-id="11201-153">EF Core 5.0 unterstützt .NET Framework nicht</span><span class="sxs-lookup"><span data-stu-id="11201-153">EF Core 5.0 does not support .NET Framework</span></span>

[<span data-ttu-id="11201-154">Issue #15498</span><span class="sxs-lookup"><span data-stu-id="11201-154">Tracking Issue #15498</span></span>](https://github.com/dotnet/efcore/issues/15498)

#### <a name="old-behavior"></a><span data-ttu-id="11201-155">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-155">Old behavior</span></span>

<span data-ttu-id="11201-156">EF Core 3.1 ist auf .NET Standard 2.0 ausgelegt, was von .NET Framework unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="11201-156">EF Core 3.1 targets .NET Standard 2.0, which is supported by .NET Framework.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-157">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-157">New behavior</span></span>

<span data-ttu-id="11201-158">EF Core 5.0 ist auf .NET Standard 2.1 ausgelegt, was nicht von .NET Framework unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="11201-158">EF Core 5.0 targets .NET Standard 2.1, which is not supported by .NET Framework.</span></span> <span data-ttu-id="11201-159">Das bedeutet, EF Core 5.0 kann nicht mit .NET Framework-Anwendungen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="11201-159">This means EF Core 5.0 cannot be used with .NET Framework applications.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-160">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-160">Why</span></span>

<span data-ttu-id="11201-161">Hierbei handelt es sich um einen Teil der umfassenderen Bewegung von .NET-Teams, um eine Vereinheitlichung in einem einzelnen .NET-Zielframework zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="11201-161">This is part of the wider movement across .NET teams aimed at unification to a single .NET target framework.</span></span> <span data-ttu-id="11201-162">Weitere Informationen finden Sie unter [The future of .NET Standard (Die Zukunft von .NET Standard)](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span><span class="sxs-lookup"><span data-stu-id="11201-162">For more information see [the future of .NET Standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-163">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-163">Mitigations</span></span>

<span data-ttu-id="11201-164">.NET Framework-Anwendungen können weiterhin EF Core 3.1 verwenden. Dabei handelt es sich um ein [LTS-Release (Long-Term Support, langfristige Unterstützung)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="11201-164">.NET Framework applications can continue to use EF Core 3.1, which is a [long-term support (LTS) release](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="11201-165">Alternativ können Anwendungen zur Verwendung von .NET Core 2.1, .NET Core 3.1 oder .NET 5 aktualisiert werden, um die Unterstützung von .NET Standard 2.1 zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="11201-165">Alternately, applications can be updated to use .NET Core 2.1, .NET Core 3.1, or .NET 5, all of which support .NET Standard 2.1.</span></span>

<a name="getcolumnname-obsolete"></a>

### <a name="ipropertygetcolumnname-is-now-obsolete"></a><span data-ttu-id="11201-166">IProperty.GetColumnName() gilt ab sofort als veraltet</span><span class="sxs-lookup"><span data-stu-id="11201-166">IProperty.GetColumnName() is now obsolete</span></span>

[<span data-ttu-id="11201-167">Nachverfolgung von Issue #2266</span><span class="sxs-lookup"><span data-stu-id="11201-167">Tracking Issue #2266</span></span>](https://github.com/dotnet/efcore/issues/2266)

#### <a name="old-behavior"></a><span data-ttu-id="11201-168">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-168">Old behavior</span></span>

<span data-ttu-id="11201-169">`GetColumnName()` hat den Namen der Spalte zurückgegeben, der die Eigenschaft zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="11201-169">`GetColumnName()` returned the name of the column that a property is mapped to.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-170">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-170">New behavior</span></span>

<span data-ttu-id="11201-171">`GetColumnName()` gibt weiterhin den Namen der Spalte zurück, der eine Eigenschaft zugeordnet ist. Dieses Verhalten ist nun jedoch mehrdeutig, da EF Core 5 die TPT-Methode (Tabelle pro Typ) und gleichzeitiges Zuordnen zu einer Ansicht oder einer Funktion unterstützt, wenn diese Zuordnungen verschiedene Spaltennamen für dieselbe Eigenschaft verwenden könnten.</span><span class="sxs-lookup"><span data-stu-id="11201-171">`GetColumnName()` still returns the name of a column that a property is mapped to, but this behavior is now ambiguous since EF Core 5 supports TPT and simultaneous mapping to a view or a function where these mappings could use different column names for the same property.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-172">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-172">Why</span></span>

<span data-ttu-id="11201-173">Diese Methode wurde als veraltet gekennzeichnet, um Benutzern nahezulegen, eine exaktere Überladung zu verwenden: <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)>.</span><span class="sxs-lookup"><span data-stu-id="11201-173">We marked this method as obsolete to guide users to a more accurate overload - <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)>.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-174">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-174">Mitigations</span></span>

<span data-ttu-id="11201-175">Verwenden Sie den folgenden Code, um den Spaltennamen für eine bestimmte Tabelle abzurufen:</span><span class="sxs-lookup"><span data-stu-id="11201-175">Use the following code to get the column name for a specific table:</span></span>

```csharp
var columnName = property.GetColumnName(StoreObjectIdentifier.Table("Users", null)));
```

<a name="decimals"></a>

### <a name="precision-and-scale-are-required-for-decimals"></a><span data-ttu-id="11201-176">Genauigkeit und Skalierung sind für Dezimale erforderlich</span><span class="sxs-lookup"><span data-stu-id="11201-176">Precision and scale are required for decimals</span></span>

[<span data-ttu-id="11201-177">Nachverfolgung von Issue #19293</span><span class="sxs-lookup"><span data-stu-id="11201-177">Tracking Issue #19293</span></span>](https://github.com/dotnet/efcore/issues/19293)

#### <a name="old-behavior"></a><span data-ttu-id="11201-178">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-178">Old behavior</span></span>

<span data-ttu-id="11201-179">EF Core hat die Genauigkeit und Skalierung von <xref:Microsoft.Data.SqlClient.SqlParameter>-Objekten normalerweise nicht festgelegt.</span><span class="sxs-lookup"><span data-stu-id="11201-179">EF Core did not normally set precision and scale on <xref:Microsoft.Data.SqlClient.SqlParameter> objects.</span></span> <span data-ttu-id="11201-180">Das bedeutet, dass die vollständige Genauigkeit und Skalierung an SQL Server übermittelt wurde, sodass SQL Server anhand der Genauigkeit und Skalierung der Datenbankspalte aufgerundet hat.</span><span class="sxs-lookup"><span data-stu-id="11201-180">This means the full precision and scale was sent to SQL Server, at which point SQL Server would round based on the precision and scale of the database column.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-181">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-181">New behavior</span></span>

<span data-ttu-id="11201-182">Nun legt EF Core die Genauigkeit und Skalierung für Parameter mithilfe der Werte fest, die für Eigenschaften im EF Core-Modell konfiguriert wurden.</span><span class="sxs-lookup"><span data-stu-id="11201-182">EF Core now sets precision and scale on parameters using the values configured for properties in the EF Core model.</span></span> <span data-ttu-id="11201-183">Das bedeutet, dass die Rundung jetzt in SqlClient erfolgt.</span><span class="sxs-lookup"><span data-stu-id="11201-183">This means rounding now happens in SqlClient.</span></span> <span data-ttu-id="11201-184">Demnach kann sich die Rundung ändern, wenn die Genauigkeit und Skalierung nicht mit der Genauigkeit und Skalierung der Datenbank übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="11201-184">Consequentially, if the configured precision and scale do not match the database precision and scale, then the rounding seen may change.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-185">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-185">Why</span></span>

<span data-ttu-id="11201-186">Neuere SQL Server-Features, einschließlich Always Encrypted, erfordern, dass Parameterfacetten vollständig festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="11201-186">Newer SQL Server features, including Always Encrypted, require that parameter facets are fully specified.</span></span> <span data-ttu-id="11201-187">Darüber hinaus hat SqlClient eine Änderung an der Rundung vorgenommen, anstatt Dezimalwerte zu kürzen, was dem Verhalten von SQL Server entspricht.</span><span class="sxs-lookup"><span data-stu-id="11201-187">In addition, SqlClient made a change to round instead of truncate decimal values, thereby matching the SQL Server behavior.</span></span> <span data-ttu-id="11201-188">Dadurch konnte EF Core diese Facetten festlegen, ohne das Verhalten für ordnungsgemäß konfigurierte Dezimale zu ändern.</span><span class="sxs-lookup"><span data-stu-id="11201-188">This made it possible for EF Core to set these facets without changing the behavior for correctly configured decimals.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-189">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-189">Mitigations</span></span>

<span data-ttu-id="11201-190">Ordnen Sie Ihre Dezimaleigenschaften mithilfe eines Typnamens zu, der die Genauigkeit und Skalierung enthält.</span><span class="sxs-lookup"><span data-stu-id="11201-190">Map your decimal properties using a type name that includes precision and scale.</span></span> <span data-ttu-id="11201-191">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="11201-191">For example:</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }

    [Column(TypeName = "decimal(16, 5")]
    public decimal Score { get; set; }
}
```

<span data-ttu-id="11201-192">Alternativ können Sie `HasPrecision` in den APIs für die Modellerstellung verwenden.</span><span class="sxs-lookup"><span data-stu-id="11201-192">Or use `HasPrecision` in the model building APIs.</span></span> <span data-ttu-id="11201-193">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="11201-193">For example:</span></span>

```csharp
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>().Property(e => e.Score).HasPrecision(16, 5);
    }
```

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a><span data-ttu-id="11201-194">Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität</span><span class="sxs-lookup"><span data-stu-id="11201-194">Required on the navigation from principal to dependent has different semantics</span></span>

[<span data-ttu-id="11201-195">Tracking Issue 17286</span><span class="sxs-lookup"><span data-stu-id="11201-195">Tracking Issue #17286</span></span>](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a><span data-ttu-id="11201-196">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-196">Old behavior</span></span>

<span data-ttu-id="11201-197">Nur die Navigation zum Prinzipal konnte als erforderlich konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="11201-197">Only the navigations to principal could be configured as required.</span></span> <span data-ttu-id="11201-198">Daher führte die Verwendung von `RequiredAttribute` für die Navigation zur abhängigen Entität (der Entität, die den Fremdschlüssel enthält) stattdessen zur Erstellung des Fremdschlüssels für den definierenden Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="11201-198">Therefore using `RequiredAttribute` on the navigation to the dependent (the entity containing the foreign key) would instead create the foreign key on the defining entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-199">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-199">New behavior</span></span>

<span data-ttu-id="11201-200">Dank der hinzugefügten Unterstützung von erforderlichen abhängigen Entitäten ist es nun möglich, jede beliebige Verweisnavigation als erforderlich zu markieren. Dies bedeutet, dass im obigen Fall der Fremdschlüssel auf der anderen Seite der Beziehung definiert wird und die Eigenschaften nicht als erforderlich markiert werden.</span><span class="sxs-lookup"><span data-stu-id="11201-200">With the added support for required dependents, it is now possible to mark any reference navigation as required, meaning that in the case shown above the foreign key will be defined on the other side of the relationship and the properties won't be marked as required.</span></span>

<span data-ttu-id="11201-201">Das Aufrufen von `IsRequired` vor der Angabe der abhängigen Entität ist nun mehrdeutig:</span><span class="sxs-lookup"><span data-stu-id="11201-201">Calling `IsRequired` before specifying the dependent end is now ambiguous:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a><span data-ttu-id="11201-202">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-202">Why</span></span>

<span data-ttu-id="11201-203">Das neue Verhalten ist erforderlich, um die Unterstützung von erforderlichen abhängigen Entitäten zu ermöglichen ([siehe 12100](https://github.com/dotnet/efcore/issues/12100)).</span><span class="sxs-lookup"><span data-stu-id="11201-203">The new behavior is necessary to enable support for required dependents ([see #12100](https://github.com/dotnet/efcore/issues/12100)).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-204">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-204">Mitigations</span></span>

<span data-ttu-id="11201-205">Entfernen Sie `RequiredAttribute` aus der Navigation zur abhängigen Entität, und verwenden Sie „RequiredAttribute“ stattdessen in der Navigation zum Prinzipal, oder konfigurieren Sie die Beziehung in `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="11201-205">Remove `RequiredAttribute` from the navigation to the dependent and place it instead on the navigation to the principal or configure the relationship in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a><span data-ttu-id="11201-206">Die definierende Abfrage wurde durch anbieterspezifische Methoden ersetzt</span><span class="sxs-lookup"><span data-stu-id="11201-206">Defining query is replaced with provider-specific methods</span></span>

[<span data-ttu-id="11201-207">Issue 18903</span><span class="sxs-lookup"><span data-stu-id="11201-207">Tracking Issue #18903</span></span>](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a><span data-ttu-id="11201-208">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-208">Old behavior</span></span>

<span data-ttu-id="11201-209">Entitätstypen wurden zum Definieren von Abfragen auf der Kernebene zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="11201-209">Entity types were mapped to defining queries at the Core level.</span></span> <span data-ttu-id="11201-210">Jedes Mal, wenn der Entitätstyp im Abfragestamm des Entitätstyps verwendet wurde, wurde er bei allen Anbietern durch die definierende Abfrage ersetzt.</span><span class="sxs-lookup"><span data-stu-id="11201-210">Anytime the entity type was used in the query root of the entity type was replaced by the defining query for any provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-211">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-211">New behavior</span></span>

<span data-ttu-id="11201-212">APIs für definierende Abfragen sind veraltet.</span><span class="sxs-lookup"><span data-stu-id="11201-212">APIs for defining query are deprecated.</span></span> <span data-ttu-id="11201-213">Es wurden neue anbieterspezifische APIs eingeführt.</span><span class="sxs-lookup"><span data-stu-id="11201-213">New provider-specific APIs were introduced.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-214">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-214">Why</span></span>

<span data-ttu-id="11201-215">Während definierende Abfragen als Ersatzabfragen implementiert wurden, wenn in der Abfrage der Abfragestamm verwendet wird, sind einige Probleme aufgetreten:</span><span class="sxs-lookup"><span data-stu-id="11201-215">While defining queries were implemented as replacement query whenever query root is used in the query, it had a few issues:</span></span>

- <span data-ttu-id="11201-216">Wenn eine definierende Abfrage einen Entitätstyp mit `new { ... }` in die `Select`-Methode projiziert, war für die Identifizierung dessen als Entität zusätzliche Arbeit erforderlich. Zudem sorgte dieses Vorgehen für Inkonsistenzen in Bezug darauf, wie EF Core nominale Typen in der Abfrage behandelt.</span><span class="sxs-lookup"><span data-stu-id="11201-216">If defining query is projecting entity type using `new { ... }` in `Select` method, then identifying that as an entity required additional work and made it inconsistent with how EF Core treats nominal types in the query.</span></span>
- <span data-ttu-id="11201-217">Bei relationalen Anbietern ist `FromSql` weiterhin erforderlich, um die SQL-Zeichenfolge im LINQ-Ausdrucksformat zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="11201-217">For relational providers `FromSql` is still needed to pass the SQL string in LINQ expression form.</span></span>

<span data-ttu-id="11201-218">Zunächst wurden definierende Abfragen als clientseitige Sichten zur Verwendung mit In-Memory-Anbietern bei schlüssellosen Entitäten eingeführt (ähnlich wie bei Datenbanksichten in relationalen Datenbanken).</span><span class="sxs-lookup"><span data-stu-id="11201-218">Initially defining queries were introduced as client-side views to be used with In-Memory provider for keyless entities (similar to database views in relational databases).</span></span> <span data-ttu-id="11201-219">Eine solche Definition erleichtert das Testen der Anwendung mit In-Memory-Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="11201-219">Such definition makes it easy to test application against in-memory database.</span></span> <span data-ttu-id="11201-220">Später wurden sie allgemein anwendbar, was zwar nützlich war, aber ein inkonsistentes und schwer verständliches Verhalten mit sich brachte.</span><span class="sxs-lookup"><span data-stu-id="11201-220">Afterwards they became broadly applicable, which was useful but brought inconsistent and hard to understand behavior.</span></span> <span data-ttu-id="11201-221">Daher haben wir uns entschieden, das Konzept zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="11201-221">So we decided to simplify the concept.</span></span> <span data-ttu-id="11201-222">Wir stellen die LINQ-basierte definierende Abfrage nur noch für In-Memory-Anbieter bereit und behandeln sie anders.</span><span class="sxs-lookup"><span data-stu-id="11201-222">We made LINQ based defining query exclusive to In-Memory provider and treat them differently.</span></span> <span data-ttu-id="11201-223">Weitere Informationen finden Sie in [diesem Thema](https://github.com/dotnet/efcore/issues/20023).</span><span class="sxs-lookup"><span data-stu-id="11201-223">For more information, [see this issue](https://github.com/dotnet/efcore/issues/20023).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-224">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-224">Mitigations</span></span>

<span data-ttu-id="11201-225">Verwenden Sie für relationale Anbieter die `ToSqlQuery`-Methode in `OnModelCreating`, und übergeben Sie eine SQL-Zeichenfolge zur Verwendung für den Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="11201-225">For relational providers, use `ToSqlQuery` method in `OnModelCreating` and pass in a SQL string to use for the entity type.</span></span>
<span data-ttu-id="11201-226">Verwenden Sie für den In-Memory-Anbieter die `ToInMemoryQuery`-Methode in `OnModelCreating`, und übergeben Sie eine LINQ-Abfrage zur Verwendung für den Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="11201-226">For the In-Memory provider, use `ToInMemoryQuery` method in `OnModelCreating` and pass in a LINQ query to use for the entity type.</span></span>

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a><span data-ttu-id="11201-227">Navigationen ohne Nullverweis werden von Abfragen nicht überschrieben</span><span class="sxs-lookup"><span data-stu-id="11201-227">Non-null reference navigations are not overwritten by queries</span></span>

[<span data-ttu-id="11201-228">Nachverfolgung von Issue 2693</span><span class="sxs-lookup"><span data-stu-id="11201-228">Tracking Issue #2693</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a><span data-ttu-id="11201-229">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-229">Old behavior</span></span>

<span data-ttu-id="11201-230">In EF Core 3.1 wurden Verweisnavigationen, die frühzeitig mit Werten ungleich NULL initialisiert wurden, mitunter von Entitätsinstanzen aus der Datenbank überschrieben, und zwar unabhängig davon, ob Schlüsselwerte übereinstimmten oder nicht.</span><span class="sxs-lookup"><span data-stu-id="11201-230">In EF Core 3.1, reference navigations eagerly initialized to non-null values would sometimes be overwritten by entity instances from the database, regardless of whether or not key values matched.</span></span> <span data-ttu-id="11201-231">In anderen Fällen hat EF Core 3.1 jedoch das Gegenteil bewirkt und den vorhandenen Wert ungleich NULL beibehalten.</span><span class="sxs-lookup"><span data-stu-id="11201-231">However, in other cases, EF Core 3.1 would do the opposite and leave the existing non-null value.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-232">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-232">New behavior</span></span>

<span data-ttu-id="11201-233">Ab EF Core 5.0 werden Verweisnavigationen ungleich NULL nicht mehr durch Instanzen überschrieben, die von einer Abfrage zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="11201-233">Starting with EF Core 5.0, non-null reference navigations are never overwritten by instances returned from a query.</span></span>

<span data-ttu-id="11201-234">Beachten Sie, dass die frühzeitige Initialisierung einer _Sammlungsnavigation_ als leere Sammlung nach wie vor unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="11201-234">Note that eager initialization of a _collection_ navigation to an empty collection is still supported.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-235">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-235">Why</span></span>

<span data-ttu-id="11201-236">Die Initialisierung einer Verweisnavigationseigenschaft als „leere“ Entitätsinstanz führt zu einem uneindeutigen Zustand.</span><span class="sxs-lookup"><span data-stu-id="11201-236">Initialization of a reference navigation property to an "empty" entity instance results in an ambiguous state.</span></span> <span data-ttu-id="11201-237">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="11201-237">For example:</span></span>

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

<span data-ttu-id="11201-238">Normalerweise erstellt eine Abfrage von Blogs und Autoren zunächst `Blog`-Instanzen. Dann werden die entsprechenden `Author`-Instanzen auf Grundlage der aus der Datenbank zurückgegebenen Daten festgelegt.</span><span class="sxs-lookup"><span data-stu-id="11201-238">Normally a query for Blogs and Authors will first create `Blog` instances and then set the appropriate `Author` instances based on the data returned from the database.</span></span> <span data-ttu-id="11201-239">In diesem Fall ist jedoch jede `Blog.Author`-Eigenschaft bereits als leere `Author`-Instanz initialisiert.</span><span class="sxs-lookup"><span data-stu-id="11201-239">However, in this case every `Blog.Author` property is already initialized to an empty `Author`.</span></span> <span data-ttu-id="11201-240">Allerdings kann EF Core nicht wissen, dass diese Instanz „leer“ ist.</span><span class="sxs-lookup"><span data-stu-id="11201-240">Except EF Core has no way to know that this instance is "empty".</span></span> <span data-ttu-id="11201-241">Das Überschreiben dieser Instanz könnte also unbemerkt eine gültige `Author`-Instanz auslösen.</span><span class="sxs-lookup"><span data-stu-id="11201-241">So overwriting this instance could potentially silently throw away a valid `Author`.</span></span> <span data-ttu-id="11201-242">Daher überschreibt EF Core 5.0 jetzt konsequent keine Navigation, die bereits initialisiert ist.</span><span class="sxs-lookup"><span data-stu-id="11201-242">Therefore, EF Core 5.0 now consistently does not overwrite a navigation that is already initialized.</span></span>

<span data-ttu-id="11201-243">Dieses neue Verhalten stimmt in den meisten Fällen auch mit dem Verhalten von EF6 überein, obwohl wir bei Untersuchungen auch einige Fälle von Inkonsistenz in EF6 gefunden haben.</span><span class="sxs-lookup"><span data-stu-id="11201-243">This new behavior also aligns with the behavior of EF6 in most cases, although upon investigation we also found some cases of inconsistency in EF6.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-244">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-244">Mitigations</span></span>

<span data-ttu-id="11201-245">Wenn dieses Problem auftritt, besteht die Lösung darin, die vorzeitige Initialisierung der Verweisnavigationseigenschaften zu beenden.</span><span class="sxs-lookup"><span data-stu-id="11201-245">If this break is encountered, then the fix is to stop eagerly initializing reference navigation properties.</span></span>

<a name="toview"></a>

### <a name="toview-is-treated-differently-by-migrations"></a><span data-ttu-id="11201-246">ToView() wird von Migrationen anders verarbeitet</span><span class="sxs-lookup"><span data-stu-id="11201-246">ToView() is treated differently by migrations</span></span>

[<span data-ttu-id="11201-247">Nachverfolgung von Issue #2725</span><span class="sxs-lookup"><span data-stu-id="11201-247">Tracking Issue #2725</span></span>](https://github.com/dotnet/efcore/issues/2725)

#### <a name="old-behavior"></a><span data-ttu-id="11201-248">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-248">Old behavior</span></span>

<span data-ttu-id="11201-249">Das Aufrufen von `ToView(string)` hat dazu geführt, dass Migrationen den Entitätstyp ignorieren und ihn einer Ansicht zuordnen.</span><span class="sxs-lookup"><span data-stu-id="11201-249">Calling `ToView(string)` made the migrations ignore the entity type in addition to mapping it to a view.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-250">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-250">New behavior</span></span>

<span data-ttu-id="11201-251">`ToView(string)` markiert den Entitätstyp nun als nicht einer Tabelle zugeordnet und ordnet ihn einer Ansicht zu.</span><span class="sxs-lookup"><span data-stu-id="11201-251">Now `ToView(string)` marks the entity type as not mapped to a table in addition to mapping it to a view.</span></span> <span data-ttu-id="11201-252">Dies führt dazu, dass bei der ersten Migration nach einem Upgrade zu EF Core 5 versucht wird, die Standardtabelle für diesen Entitätstyp zu löschen, da er nicht mehr ignoriert wird.</span><span class="sxs-lookup"><span data-stu-id="11201-252">This results in the first migration after upgrading to EF Core 5 to try to drop the default table for this entity type as it's not longer ignored.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-253">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-253">Why</span></span>

<span data-ttu-id="11201-254">EF Core ermöglicht es nun, dass ein Entitätstyp gleichzeitig sowohl einer Tabelle als auch einer Ansicht zugeordnet werden kann. `ToView` ist also kein gültiger Indikator dafür mehr, dass der Typ von Migrationen ignoriert werden soll.</span><span class="sxs-lookup"><span data-stu-id="11201-254">EF Core now allows an entity type to be mapped to both a table and a view simultaneously, so `ToView` is no longer a valid indicator that it should be ignored by migrations.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-255">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-255">Mitigations</span></span>

<span data-ttu-id="11201-256">Verwenden Sie den folgenden Code, um die zugeordnete Tabelle als von Migrationen ausgeschlossen zu markieren:</span><span class="sxs-lookup"><span data-stu-id="11201-256">Use the following code to mark the mapped table as excluded from migrations:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().ToTable("UserView", t => t.ExcludeFromMigrations());
}
```

<a name="totable"></a>

### <a name="totablenull-marks-the-entity-type-as-not-mapped-to-a-table"></a><span data-ttu-id="11201-257">ToTable(null) markiert den Entitätstyp als nicht einer Tabelle zugeordnet</span><span class="sxs-lookup"><span data-stu-id="11201-257">ToTable(null) marks the entity type as not mapped to a table</span></span>

[<span data-ttu-id="11201-258">Nachverfolgung von Issue #21172</span><span class="sxs-lookup"><span data-stu-id="11201-258">Tracking Issue #21172</span></span>](https://github.com/dotnet/efcore/issues/21172)

#### <a name="old-behavior"></a><span data-ttu-id="11201-259">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-259">Old behavior</span></span>

<span data-ttu-id="11201-260">`ToTable(null)` hat den Tabellennamen auf den Standardwert zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="11201-260">`ToTable(null)` would reset the table name to the default.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-261">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-261">New behavior</span></span>

<span data-ttu-id="11201-262">`ToTable(null)` markiert den Entitätstyp nun als nicht einer Tabelle zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="11201-262">`ToTable(null)` now marks the entity type as not mapped to any table.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-263">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-263">Why</span></span>

<span data-ttu-id="11201-264">EF Core ermöglicht es nun, dass ein Entitätstyp gleichzeitig sowohl einer Tabelle als auch einer Ansicht zugeordnet werden kann. `ToTable(null)` wird also verwendet, um anzugeben, dass der Typ keiner Tabelle zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="11201-264">EF Core now allows an entity type to be mapped to both a table and a view simultaneously, so `ToTable(null)` is used to indicate that it isn't mapped to any table.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-265">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-265">Mitigations</span></span>

<span data-ttu-id="11201-266">Verwenden Sie den folgenden Code, um den Tabellennamen in den Standardwert zurückzusetzen, wenn keine Zuordnung zu einer Ansicht oder einer DbFunction-Klasse vorliegt:</span><span class="sxs-lookup"><span data-stu-id="11201-266">Use the following code to reset the table name to the default if it's not mapped to a view or a DbFunction:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().Metadata.RemoveAnnotation(RelationalAnnotationNames.TableName);
}
```

## <a name="low-impact-changes"></a><span data-ttu-id="11201-267">Änderungen mit geringer Auswirkung</span><span class="sxs-lookup"><span data-stu-id="11201-267">Low-impact changes</span></span>

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="11201-268">Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt.</span><span class="sxs-lookup"><span data-stu-id="11201-268">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="11201-269">Issue 14257</span><span class="sxs-lookup"><span data-stu-id="11201-269">Tracking Issue #14257</span></span>](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a><span data-ttu-id="11201-270">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-270">Old behavior</span></span>

<span data-ttu-id="11201-271">Die HasGeometricDimension-Methode wurde dazu verwendet, zusätzliche Maße (Z und M) für Geometriespalten zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="11201-271">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="11201-272">Dies hat sich jedoch immer nur auf die Datenbankerstellung ausgewirkt.</span><span class="sxs-lookup"><span data-stu-id="11201-272">However, it only ever affected database creation.</span></span> <span data-ttu-id="11201-273">Das Angeben dieser Methode zum Abfragen von Werten mit zusätzlichen Maßen war nicht notwendig.</span><span class="sxs-lookup"><span data-stu-id="11201-273">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="11201-274">Außerdem hat die Methode nicht ordnungsgemäß funktioniert, wenn Werte mit zusätzlichen Maßen eingefügt oder geändert wurden (siehe [Issue 14257](https://github.com/dotnet/efcore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="11201-274">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/dotnet/efcore/issues/14257)).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-275">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-275">New behavior</span></span>

<span data-ttu-id="11201-276">Zum Ermöglichen des Einfügens und Änderns von Geometriewerten mit zusätzlichen Maßen (Z und M) muss das jeweilige Maß als Teil des Spaltentypnamens angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="11201-276">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="11201-277">Diese API entspricht eher dem zugrunde liegenden Verhalten der AddGeometryColumn-Funktion von SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="11201-277">This API matches more closely to the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-278">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-278">Why</span></span>

<span data-ttu-id="11201-279">Die Verwendung der HasGeometricDimension-Methode, nachdem das Maß im Spaltentyp festgelegt wurde, ist redundant und nicht notwendig. Daher wurde die gesamte HasGeometricDimension-Methode entfernt.</span><span class="sxs-lookup"><span data-stu-id="11201-279">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-280">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-280">Mitigations</span></span>

<span data-ttu-id="11201-281">Verwenden Sie `HasColumnType` zum Festlegen der Maße:</span><span class="sxs-lookup"><span data-stu-id="11201-281">Use `HasColumnType` to specify the dimension:</span></span>

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

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a><span data-ttu-id="11201-282">Cosmos: Der Partitionsschlüssel wird nun dem Primärschlüssel hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="11201-282">Cosmos: Partition key is now added to the primary key</span></span>

[<span data-ttu-id="11201-283">Tracking Issue 15289</span><span class="sxs-lookup"><span data-stu-id="11201-283">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="11201-284">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-284">Old behavior</span></span>

<span data-ttu-id="11201-285">Die Partitionsschlüsseleigenschaft wurde nur dem alternativen Schlüssel hinzugefügt, der `id` enthält.</span><span class="sxs-lookup"><span data-stu-id="11201-285">The partition key property was only added to the alternate key that includes `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-286">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-286">New behavior</span></span>

<span data-ttu-id="11201-287">Die Partitionsschlüsseleigenschaft wird gemäß Konvention jetzt auch dem Primärschlüssel hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="11201-287">The partition key property is now also added to the primary key by convention.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-288">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-288">Why</span></span>

<span data-ttu-id="11201-289">Durch diese Änderung ist das Modell besser auf die Azure Cosmos DB-Semantik abgestimmt. Zudem verbessert sich dadurch die Leistung von `Find` und einigen Abfragen.</span><span class="sxs-lookup"><span data-stu-id="11201-289">This change makes the model better aligned with Azure Cosmos DB semantics and improves the performance of `Find` and some queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-290">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-290">Mitigations</span></span>

<span data-ttu-id="11201-291">Um zu verhindern, dass die Partitionsschlüsseleigenschaft dem Primärschlüssel hinzugefügt wird, konfigurieren Sie sie in `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="11201-291">To prevent the partition key property to be added to the primary key, configure it in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a><span data-ttu-id="11201-292">Cosmos: Die `id`-Eigenschaft wurde umbenannt in `__id`</span><span class="sxs-lookup"><span data-stu-id="11201-292">Cosmos: `id` property renamed to `__id`</span></span>

[<span data-ttu-id="11201-293">Issue 17751</span><span class="sxs-lookup"><span data-stu-id="11201-293">Tracking Issue #17751</span></span>](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a><span data-ttu-id="11201-294">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-294">Old behavior</span></span>

<span data-ttu-id="11201-295">Die der JSON-Eigenschaft `id` zugeordnete Schatteneigenschaft hatte ebenfalls den Namen `id`.</span><span class="sxs-lookup"><span data-stu-id="11201-295">The shadow property mapped to the `id` JSON property was also named `id`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-296">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-296">New behavior</span></span>

<span data-ttu-id="11201-297">Die gemäß Konvention erstellte Schatteneigenschaft trägt nun den Namen `__id`.</span><span class="sxs-lookup"><span data-stu-id="11201-297">The shadow property created by convention is now named `__id`.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-298">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-298">Why</span></span>

<span data-ttu-id="11201-299">Durch diese Änderung ist es weniger wahrscheinlich, dass die Eigenschaft `id` mit einer vorhandenen Eigenschaft des Entitätstyps kollidiert.</span><span class="sxs-lookup"><span data-stu-id="11201-299">This change makes it less likely that the `id` property clashes with an existing property on the entity type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-300">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-300">Mitigations</span></span>

<span data-ttu-id="11201-301">Wenn Sie zum Verhalten der Version 3.x zurückkehren möchten, konfigurieren Sie die Eigenschaft `id` in `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="11201-301">To go back to the 3.x behavior, configure the `id` property in `OnModelCreating`.</span></span>

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a><span data-ttu-id="11201-302">Cosmos: byte[] wird nun als Base64-Zeichenfolge statt als Zahlenarray gespeichert</span><span class="sxs-lookup"><span data-stu-id="11201-302">Cosmos: byte[] is now stored as a base64 string instead of a number array</span></span>

[<span data-ttu-id="11201-303">Issue 17306</span><span class="sxs-lookup"><span data-stu-id="11201-303">Tracking Issue #17306</span></span>](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a><span data-ttu-id="11201-304">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-304">Old behavior</span></span>

<span data-ttu-id="11201-305">Eigenschaften vom Typ „byte[]“ wurden als Zahlenarray gespeichert.</span><span class="sxs-lookup"><span data-stu-id="11201-305">Properties of type byte[] were stored as a number array.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-306">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-306">New behavior</span></span>

<span data-ttu-id="11201-307">Eigenschaften vom Typ „byte[]“ werden nun als Base64-Zeichenfolge gespeichert.</span><span class="sxs-lookup"><span data-stu-id="11201-307">Properties of type byte[] are now stored as a base64 string.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-308">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-308">Why</span></span>

<span data-ttu-id="11201-309">Diese Darstellung von „byte[]“ entspricht eher den Erwartungen. Zudem handelt es sich hier um das Standardverhalten der wichtigen JSON-Serialisierungsbibliotheken.</span><span class="sxs-lookup"><span data-stu-id="11201-309">This representation of byte[] aligns better with expectations and is the default behavior of the major JSON serialization libraries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-310">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-310">Mitigations</span></span>

<span data-ttu-id="11201-311">Als Zahlenarrays gespeicherte, bereits vorhandene Daten werden weiterhin ordnungsgemäß abgefragt. Derzeit wird jedoch keine Möglichkeit unterstützt, das Einfügeverhalten zu ändern.</span><span class="sxs-lookup"><span data-stu-id="11201-311">Existing data stored as number arrays will still be queried correctly, but currently there isn't a supported way to change back the insert behavior.</span></span> <span data-ttu-id="11201-312">Wenn Ihr Szenario durch diese Einschränkung blockiert wird, kommentieren Sie dieses [Problem](https://github.com/dotnet/efcore/issues/17306).</span><span class="sxs-lookup"><span data-stu-id="11201-312">If this limitation is blocking your scenario, comment on [this issue](https://github.com/dotnet/efcore/issues/17306)</span></span>

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a><span data-ttu-id="11201-313">Cosmos: GetPropertyName und SetPropertyName wurden umbenannt</span><span class="sxs-lookup"><span data-stu-id="11201-313">Cosmos: GetPropertyName and SetPropertyName were renamed</span></span>

[<span data-ttu-id="11201-314">Issue 17874</span><span class="sxs-lookup"><span data-stu-id="11201-314">Tracking Issue #17874</span></span>](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a><span data-ttu-id="11201-315">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-315">Old behavior</span></span>

<span data-ttu-id="11201-316">Bisher wurden die Erweiterungsmethoden `GetPropertyName` und `SetPropertyName` genannt.</span><span class="sxs-lookup"><span data-stu-id="11201-316">Previously the extension methods were called `GetPropertyName` and `SetPropertyName`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-317">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-317">New behavior</span></span>

<span data-ttu-id="11201-318">Die alte API wurde entfernt, und es wurden die neuen Methoden `GetJsonPropertyName` und `SetJsonPropertyName` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="11201-318">The old API was removed and new methods added: `GetJsonPropertyName`, `SetJsonPropertyName`</span></span>

#### <a name="why"></a><span data-ttu-id="11201-319">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-319">Why</span></span>

<span data-ttu-id="11201-320">Durch diese Änderung wird die Mehrdeutigkeit um die Konfiguration durch diese Methoden beseitigt.</span><span class="sxs-lookup"><span data-stu-id="11201-320">This change removes the ambiguity around what these methods are configuring.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-321">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-321">Mitigations</span></span>

<span data-ttu-id="11201-322">Verwenden Sie die neue API.</span><span class="sxs-lookup"><span data-stu-id="11201-322">Use the new API.</span></span>

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a><span data-ttu-id="11201-323">Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird</span><span class="sxs-lookup"><span data-stu-id="11201-323">Value generators are called when the entity state is changed from Detached to Unchanged, Updated, or Deleted</span></span>

[<span data-ttu-id="11201-324">Tracking Issue 15289</span><span class="sxs-lookup"><span data-stu-id="11201-324">Tracking Issue #15289</span></span>](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a><span data-ttu-id="11201-325">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-325">Old behavior</span></span>

<span data-ttu-id="11201-326">Wert-Generatoren wurden nur aufgerufen, wenn der Entitätszustand in „Hinzugefügt“ geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="11201-326">Value generators were only called when the entity state changed to Added.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-327">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-327">New behavior</span></span>

<span data-ttu-id="11201-328">Wert-Generatoren werden jetzt aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird und die Eigenschaft die Standardwerte enthält.</span><span class="sxs-lookup"><span data-stu-id="11201-328">Value generators are now called when the entity state is changed from Detached to Unchanged, Updated, or Deleted and the property contains the default values.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-329">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-329">Why</span></span>

<span data-ttu-id="11201-330">Diese Änderung war erforderlich, um die Verwendung von Eigenschaften zu verbessern, die nicht dauerhaft im Datenspeicher gespeichert sind und deren Wert immer auf dem Client generiert wird.</span><span class="sxs-lookup"><span data-stu-id="11201-330">This change was necessary to improve the experience with properties that are not persisted to the data store and have their value generated always on the client.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-331">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-331">Mitigations</span></span>

<span data-ttu-id="11201-332">Weisen Sie der Eigenschaft vor der Änderung des Status einen anderen Wert als den Standardwert zu, um zu verhindern, dass der Wert-Generator aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="11201-332">To prevent the value generator from being called, assign a non-default value to the property before the state is changed.</span></span>

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a><span data-ttu-id="11201-333">IMigrationsModelDiffer verwendet jetzt IRelationalModel</span><span class="sxs-lookup"><span data-stu-id="11201-333">IMigrationsModelDiffer now uses IRelationalModel</span></span>

[<span data-ttu-id="11201-334">Tracking Issue 20305</span><span class="sxs-lookup"><span data-stu-id="11201-334">Tracking Issue #20305</span></span>](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a><span data-ttu-id="11201-335">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-335">Old behavior</span></span>

<span data-ttu-id="11201-336">Die `IMigrationsModelDiffer`-API wurde mithilfe von `IModel` definiert.</span><span class="sxs-lookup"><span data-stu-id="11201-336">`IMigrationsModelDiffer` API was defined using `IModel`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-337">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-337">New behavior</span></span>

<span data-ttu-id="11201-338">Die `IMigrationsModelDiffer`-API verwendet jetzt `IRelationalModel`.</span><span class="sxs-lookup"><span data-stu-id="11201-338">`IMigrationsModelDiffer` API now uses `IRelationalModel`.</span></span> <span data-ttu-id="11201-339">Die Modellmomentaufnahme enthält jedoch weiterhin nur `IModel`, da dieser Code Teil der Anwendung ist und Entity Framework ihn nicht ohne einen größeren Breaking Change ändern kann.</span><span class="sxs-lookup"><span data-stu-id="11201-339">However the model snapshot still contains only `IModel` as this code is part of the application and Entity Framework can't change it without making a bigger breaking change.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-340">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-340">Why</span></span>

<span data-ttu-id="11201-341">`IRelationalModel` ist eine neu hinzugefügte Darstellung des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="11201-341">`IRelationalModel` is a newly added representation of the database schema.</span></span> <span data-ttu-id="11201-342">Die Verwendung dieser Darstellung zum Ermitteln von Unterschieden ist schneller und präziser.</span><span class="sxs-lookup"><span data-stu-id="11201-342">Using it to find differences is faster and more accurate.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-343">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-343">Mitigations</span></span>

<span data-ttu-id="11201-344">Verwenden Sie den folgenden Code, um das Modell von `snapshot` mit dem Modell von `context` zu vergleichen:</span><span class="sxs-lookup"><span data-stu-id="11201-344">Use the following code to compare the model from `snapshot` with the model from `context`:</span></span>

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

<span data-ttu-id="11201-345">Eine Verbesserung dieser Funktion in Version 6.0 ist geplant ([siehe 22031](https://github.com/dotnet/efcore/issues/22031)).</span><span class="sxs-lookup"><span data-stu-id="11201-345">We are planning to improve this experience in 6.0 ([see #22031](https://github.com/dotnet/efcore/issues/22031))</span></span>

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a><span data-ttu-id="11201-346">Diskriminatoren sind schreibgeschützt</span><span class="sxs-lookup"><span data-stu-id="11201-346">Discriminators are read-only</span></span>

[<span data-ttu-id="11201-347">Tracking Issue 21154</span><span class="sxs-lookup"><span data-stu-id="11201-347">Tracking Issue #21154</span></span>](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a><span data-ttu-id="11201-348">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-348">Old behavior</span></span>

<span data-ttu-id="11201-349">Der Diskriminatorwert konnte vor dem Aufrufen von `SaveChanges` geändert werden.</span><span class="sxs-lookup"><span data-stu-id="11201-349">It was possible to change the discriminator value before calling `SaveChanges`</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-350">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-350">New behavior</span></span>

<span data-ttu-id="11201-351">Im obigen Fall wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="11201-351">An exception will be throws in the above case.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-352">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-352">Why</span></span>

<span data-ttu-id="11201-353">EF erwartet nicht, dass der Entitätstyp geändert wird, während er noch nachverfolgt wird, sodass das Ändern des Diskriminatorwerts zu einem inkonsistenten Kontext führt. Dies hat möglicherweise ein unerwartetes Verhalten zur Folge.</span><span class="sxs-lookup"><span data-stu-id="11201-353">EF doesn't expect the entity type to change while it is still being tracked, so changing the discriminator value leaves the context in an inconsistent state, which might result in unexpected behavior.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-354">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-354">Mitigations</span></span>

<span data-ttu-id="11201-355">Wenn eine Änderung des Diskriminatorwerts erforderlich ist und der Kontext direkt nach dem Aufruf von `SaveChanges` verworfen wird, kann der Diskriminator als änderbar festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="11201-355">If changing the discriminator value is necessary and the context will be disposed immediately after calling `SaveChanges`, the discriminator can be made mutable:</span></span>

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a><span data-ttu-id="11201-356">Anbieterspezifische EF.Functions-Methoden werden für InMemory-Anbieter ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="11201-356">Provider-specific EF.Functions methods throw for InMemory provider</span></span>

[<span data-ttu-id="11201-357">Issue 20294</span><span class="sxs-lookup"><span data-stu-id="11201-357">Tracking Issue #20294</span></span>](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a><span data-ttu-id="11201-358">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-358">Old behavior</span></span>

<span data-ttu-id="11201-359">Anbieterspezifische EF.Functions-Methoden enthielten eine Implementierung für die Clientausführung, sodass sie für den InMemory-Anbieter ausgeführt werden konnten.</span><span class="sxs-lookup"><span data-stu-id="11201-359">Provider-specific EF.Functions methods contained implementation for client execution, which allowed them to be executed on the InMemory provider.</span></span> <span data-ttu-id="11201-360">`EF.Functions.DateDiffDay` ist beispielsweise eine SQL Server-spezifische Methode, die für InMemory-Anbieter galt.</span><span class="sxs-lookup"><span data-stu-id="11201-360">For example, `EF.Functions.DateDiffDay` is a Sql Server specific method, which worked on InMemory provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-361">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-361">New behavior</span></span>

<span data-ttu-id="11201-362">Anbieterspezifische Methoden wurden aktualisiert, sodass sie im Methodentext eine Ausnahme auslösen, um ihre Auswertung auf Clientseite zu blockieren.</span><span class="sxs-lookup"><span data-stu-id="11201-362">Provider-specific methods have been updated to throw an exception in their method body to block evaluating them on client side.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-363">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-363">Why</span></span>

<span data-ttu-id="11201-364">Anbieterspezifische Methoden sind einer Datenbankfunktion zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="11201-364">Provider-specific methods map to a database function.</span></span> <span data-ttu-id="11201-365">Das Berechnungsergebnis der zugeordneten Datenbankfunktion kann auf Clientseite in LINQ nicht immer repliziert werden.</span><span class="sxs-lookup"><span data-stu-id="11201-365">The computation done by the mapped database function can't always be replicated on the client side in LINQ.</span></span> <span data-ttu-id="11201-366">Dies kann dazu führen, dass sich das Ergebnis von dem des Servers unterscheidet, wenn dieselbe Methode auf dem Client ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="11201-366">It may cause the result from the server to differ when executing the same method on client.</span></span> <span data-ttu-id="11201-367">Da diese Methoden in LINQ verwendet werden, um Übersetzungen in bestimmte Datenbankfunktionen durchzuführen, müssen sie nicht auf Clientseite ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="11201-367">Since these methods are used in LINQ to translate to specific database functions, they don't need to be evaluated on client side.</span></span> <span data-ttu-id="11201-368">Da der InMemory-Anbieter eine andere *Datenbank* ist, sind die Methoden für diesen Anbieter nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="11201-368">As InMemory provider is a different *database*, these methods aren't available for this provider.</span></span> <span data-ttu-id="11201-369">Wenn Sie versuchen, sie für den InMemory-Anbieter oder einen anderen Anbieter auszuführen, der diese Methoden nicht übersetzt, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="11201-369">Trying to execute them for InMemory provider, or any other provider that doesn't translate these methods, throws an exception.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-370">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-370">Mitigations</span></span>

<span data-ttu-id="11201-371">Da es keine Möglichkeit gibt, das Verhalten von Datenbankfunktionen exakt zu imitieren, sollten Sie die Abfragen, die diese Methoden enthalten, mit demselben Datenbanktyp testen, der auch in der Produktionsumgebung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="11201-371">Since there's no way to mimic behavior of database functions accurately, you should test the queries containing them against same kind of database as in production.</span></span>

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a><span data-ttu-id="11201-372">IndexBuilder.HasName ist mittlerweile veraltet</span><span class="sxs-lookup"><span data-stu-id="11201-372">IndexBuilder.HasName is now obsolete</span></span>

[<span data-ttu-id="11201-373">Nachverfolgung von Issue 21089</span><span class="sxs-lookup"><span data-stu-id="11201-373">Tracking Issue #21089</span></span>](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a><span data-ttu-id="11201-374">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-374">Old behavior</span></span>

<span data-ttu-id="11201-375">Bisher konnte nur ein Index für eine bestimmte Gruppe von Eigenschaften definiert werden.</span><span class="sxs-lookup"><span data-stu-id="11201-375">Previously, only one index could be defined over a given set of properties.</span></span> <span data-ttu-id="11201-376">Der Datenbankname eines Indexes wurde mithilfe von IndexBuilder.HasName konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="11201-376">The database name of an index was configured using IndexBuilder.HasName.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-377">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-377">New behavior</span></span>

<span data-ttu-id="11201-378">Für dieselbe Gruppe oder dieselben Eigenschaften sind jetzt mehrere Indizes zulässig.</span><span class="sxs-lookup"><span data-stu-id="11201-378">Multiple indexes are now allowed on the same set or properties.</span></span> <span data-ttu-id="11201-379">Diese Indizes unterscheiden sich nun durch einen Namen im Modell.</span><span class="sxs-lookup"><span data-stu-id="11201-379">These indexes are now distinguished by a name in the model.</span></span> <span data-ttu-id="11201-380">Gemäß der Konvention wird der Modellname als Datenbankname verwendet. Er kann jedoch auch unabhängig mit HasDatabaseName konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="11201-380">By convention, the model name is used as the database name; however it can also be configured independently using HasDatabaseName.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-381">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-381">Why</span></span>

<span data-ttu-id="11201-382">In Zukunft möchten wir sowohl aufsteigende als auch absteigende Indizes oder Indizes mit unterschiedlichen Sortierungen für denselben Satz von Eigenschaften aktivieren.</span><span class="sxs-lookup"><span data-stu-id="11201-382">In the future, we'd like to enable both ascending and descending indexes or indexes with different collations on the same set of properties.</span></span> <span data-ttu-id="11201-383">Diese Änderung ist ein weiterer Schritt in diese Richtung.</span><span class="sxs-lookup"><span data-stu-id="11201-383">This change moves us another step in that direction.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-384">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-384">Mitigations</span></span>

<span data-ttu-id="11201-385">Sämtlicher Code, der zuvor IndexBuilder.HasName aufgerufen hat, sollte so aktualisiert werden, dass stattdessen HasDatabaseName aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="11201-385">Any code that was previously calling IndexBuilder.HasName should be updated to call HasDatabaseName instead.</span></span>

<span data-ttu-id="11201-386">Wenn das Projekt Migrationen enthält, die vor EF Core Version 2.0.0 generiert wurden, können Sie die Warnung in diesen Dateien problemlos ignorieren und unterdrücken, indem Sie `#pragma warning disable 612, 618` hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="11201-386">If your project includes migrations generated prior to EF Core version 2.0.0, you can safely ignore the warning in those files and suppress it by adding `#pragma warning disable 612, 618`.</span></span>

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a><span data-ttu-id="11201-387">Ein Pluralizer ist nun für den Gerüstbau für Modelle mit Reverse Engineering enthalten</span><span class="sxs-lookup"><span data-stu-id="11201-387">A pluralizer is now included for scaffolding reverse engineered models</span></span>

[<span data-ttu-id="11201-388">Nachverfolgung von Issue 11160</span><span class="sxs-lookup"><span data-stu-id="11201-388">Tracking Issue #11160</span></span>](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a><span data-ttu-id="11201-389">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-389">Old behavior</span></span>

<span data-ttu-id="11201-390">Bislang mussten Sie beim Gerüstbau für einen Datenbankkontext und Entitätstypen durch Reverse Engineering eines Datenbankschemas ein separates Pluralizer-Paket installieren, um DbSet- und Sammlungsnavigationsnamen in die Pluralform und Tabellennamen in die Singularform umzuwandeln.</span><span class="sxs-lookup"><span data-stu-id="11201-390">Previously, you had to install a separate pluralizer package in order to pluralize DbSet and collection navigation names and singularize table names when scaffolding a DbContext and entity types by reverse engineering a database schema.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-391">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-391">New behavior</span></span>

<span data-ttu-id="11201-392">EF Core enthält jetzt einen Pluarlizer, der die [Humanizer](https://github.com/Humanizr/Humanizer)-Bibliothek verwendet.</span><span class="sxs-lookup"><span data-stu-id="11201-392">EF Core now includes a pluralizer that uses the [Humanizer](https://github.com/Humanizr/Humanizer) library.</span></span> <span data-ttu-id="11201-393">Dies ist dieselbe Bibliothek, die Visual Studio verwendet, um Variablennamen zu empfehlen.</span><span class="sxs-lookup"><span data-stu-id="11201-393">This is the same library Visual Studio uses to recommend variable names.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-394">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-394">Why</span></span>

<span data-ttu-id="11201-395">Die Verwendung von Pluralformen von Wörtern für Sammlungseigenschaften und Singularformen für Typen und Verweiseigenschaften ist in .NET idiomatisch.</span><span class="sxs-lookup"><span data-stu-id="11201-395">Using plural forms of words for collection properties and singular forms for types and reference properties is idiomatic in .NET.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-396">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-396">Mitigations</span></span>

<span data-ttu-id="11201-397">Um den Pluralizer zu deaktivieren, verwenden Sie die Option `--no-pluralize` auf `dotnet ef dbcontext scaffold` oder den `-NoPluralize`-Switch auf `Scaffold-DbContext`.</span><span class="sxs-lookup"><span data-stu-id="11201-397">To disable the pluralizer, use the `--no-pluralize` option on `dotnet ef dbcontext scaffold` or the `-NoPluralize` switch on `Scaffold-DbContext`.</span></span>

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a><span data-ttu-id="11201-398">INavigationBase ersetzt INavigation in einigen APIs zur Unterstützung überspringender Navigationen</span><span class="sxs-lookup"><span data-stu-id="11201-398">INavigationBase replaces INavigation in some APIs to support skip navigations</span></span>

[<span data-ttu-id="11201-399">Nachverfolgung von Issue 2568</span><span class="sxs-lookup"><span data-stu-id="11201-399">Tracking Issue #2568</span></span>](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a><span data-ttu-id="11201-400">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-400">Old behavior</span></span>

<span data-ttu-id="11201-401">Bis Version 5.0 unterstützte EF Core nur eine Form der Navigationseigenschaft, die durch die `INavigation`-Schnittstelle dargestellt wird.</span><span class="sxs-lookup"><span data-stu-id="11201-401">EF Core prior to 5.0 supported only one form of navigation property, represented by the `INavigation` interface.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="11201-402">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="11201-402">New behavior</span></span>

<span data-ttu-id="11201-403">EF Core 5.0 führt m:n-Beziehungen ein, die „überspringende Navigationen“ verwenden.</span><span class="sxs-lookup"><span data-stu-id="11201-403">EF Core 5.0 introduces many-to-many relationships which use "skip navigations".</span></span> <span data-ttu-id="11201-404">Diese werden durch die `ISkipNavigation`-Schnittstelle dargestellt, und der größte Teil der Funktionalität von `INavigation` wurde auf die gemeinsame Basisschnittstelle `INavigationBase` verlagert.</span><span class="sxs-lookup"><span data-stu-id="11201-404">These are represented by the `ISkipNavigation` interface, and most of the functionality of `INavigation` has been pushed down to a common base interface: `INavigationBase`.</span></span>

#### <a name="why"></a><span data-ttu-id="11201-405">Warum?</span><span class="sxs-lookup"><span data-stu-id="11201-405">Why</span></span>

<span data-ttu-id="11201-406">Die meisten Funktionen sind bei normalen und überspringenden Navigationen identisch.</span><span class="sxs-lookup"><span data-stu-id="11201-406">Most of the functionality between normal and skip navigations is the same.</span></span> <span data-ttu-id="11201-407">Allerdings haben überspringende Navigationen eine andere Beziehung zu Fremdschlüsseln als normale Navigationen, da sich die beteiligten Fremdschlüssel nicht direkt an beiden Enden der Beziehung befinden, sondern in der JOIN-Entität.</span><span class="sxs-lookup"><span data-stu-id="11201-407">However, skip navigations have a different relationship to foreign keys than normal navigations, since the FKs involved are not directly on either end of the relationship, but rather in the join entity.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="11201-408">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="11201-408">Mitigations</span></span>

<span data-ttu-id="11201-409">In vielen Fällen können Anwendungen ohne weitere Änderungen zur Verwendung der neuen Basisschnittstelle übergehen.</span><span class="sxs-lookup"><span data-stu-id="11201-409">In many cases applications can switch to using the new base interface with no other changes.</span></span> <span data-ttu-id="11201-410">In Fällen jedoch, in denen die Navigation für den Zugriff auf Fremdschlüsseleigenschaften verwendet wird, sollte der Code der Anwendung entweder nur auf normale Navigationen beschränkt oder so aktualisiert werden, dass er sowohl bei normalen als auch bei überspringenden Navigationen das Richtige tut.</span><span class="sxs-lookup"><span data-stu-id="11201-410">However, in cases where the navigation is used to access foreign key properties, application code should either be constrained to only normal navigations, or updated to do the appropriate thing for both normal and skip navigations.</span></span>

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a><span data-ttu-id="11201-411">Einige Abfragen mit korrelierter Sammlung, die ebenfalls `Distinct` oder`GroupBy` verwenden, werden nicht mehr unterstützt</span><span class="sxs-lookup"><span data-stu-id="11201-411">Some queries with correlated collection that also use `Distinct` or `GroupBy` are no longer supported</span></span>

[<span data-ttu-id="11201-412">Nachverfolgung von Issue 15873</span><span class="sxs-lookup"><span data-stu-id="11201-412">Tracking Issue #15873</span></span>](https://github.com/dotnet/efcore/issues/15873)

<span data-ttu-id="11201-413">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="11201-413">**Old behavior**</span></span>

<span data-ttu-id="11201-414">Zuvor haben wir die Ausführung von Abfragen mit korrelierten Sammlungen, gefolgt von `GroupBy`, sowie einige Abfragen mit `Distinct` erlaubt.</span><span class="sxs-lookup"><span data-stu-id="11201-414">Previously, queries involving correlated collections followed by `GroupBy`, as well as some queries using `Distinct` we allowed to execute.</span></span>

<span data-ttu-id="11201-415">Beispiel für GroupBy:</span><span class="sxs-lookup"><span data-stu-id="11201-415">GroupBy example:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

<span data-ttu-id="11201-416">Beispiel für `Distinct`: insbesondere `Distinct`-Abfragen, bei denen die innere Sammlungsprojektion nicht den Primärschlüssel enthält:</span><span class="sxs-lookup"><span data-stu-id="11201-416">`Distinct` example - specifically `Distinct` queries where inner collection projection doesn't contain the primary key:</span></span>

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

<span data-ttu-id="11201-417">Diese Abfragen konnten falsche Ergebnisse liefern, wenn die innere Sammlung Duplikate enthielt, funktionierten aber einwandfrei, wenn alle Elemente in der inneren Sammlung eindeutig waren.</span><span class="sxs-lookup"><span data-stu-id="11201-417">These queries could return incorrect results if the inner collection contained any duplicates, but worked correctly if all the elements in the inner collection were unique.</span></span>

<span data-ttu-id="11201-418">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="11201-418">**New behavior**</span></span>

<span data-ttu-id="11201-419">Diese Abfragen werden nicht mehr unterstützt.</span><span class="sxs-lookup"><span data-stu-id="11201-419">These queries are no longer supported.</span></span> <span data-ttu-id="11201-420">Es wird eine Ausnahme ausgelöst, die darauf hinweist, dass wir nicht genügend Informationen haben, um die Ergebnisse ordnungsgemäß zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="11201-420">Exception is thrown indicating that we don't have enough information to correctly build the results.</span></span>

<span data-ttu-id="11201-421">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="11201-421">**Why**</span></span>

<span data-ttu-id="11201-422">Für Szenarien mit korrelierten Sammlungen müssen wir den Primärschlüssel der Entitäten kennen, um die Sammlungsentitäten dem richtigen übergeordneten Element zuweisen zu können.</span><span class="sxs-lookup"><span data-stu-id="11201-422">For correlated collection scenarios we need to know entity's primary key in order to assign collection entities to the correct parent.</span></span> <span data-ttu-id="11201-423">Wenn die innere Sammlung `GroupBy` oder `Distinct` nicht verwendet, kann der fehlende Primärschlüssel einfach zur Projektion hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="11201-423">When inner collection doesn't use `GroupBy` or `Distinct`, the missing primary key can simply be added to the projection.</span></span> <span data-ttu-id="11201-424">Im Falle von `GroupBy` und `Distinct` ist dies jedoch nicht möglich, weil es das Ergebnis des Vorgangs `GroupBy` oder `Distinct` ändern würde.</span><span class="sxs-lookup"><span data-stu-id="11201-424">However in case of `GroupBy` and `Distinct` it can't be done because it would change the result of `GroupBy` or `Distinct` operation.</span></span>

<span data-ttu-id="11201-425">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="11201-425">**Mitigations**</span></span>

<span data-ttu-id="11201-426">Schreiben Sie die Abfrage so um, dass die Vorgänge `GroupBy` oder `Distinct` nicht auf die innere Sammlung angewendet werden. Führen Sie diese Vorgänge stattdessen auf dem Client aus.</span><span class="sxs-lookup"><span data-stu-id="11201-426">Rewrite the query to not use `GroupBy` or `Distinct` operations on the inner collection, and perform these operations on the client instead.</span></span>

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

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a><span data-ttu-id="11201-427">Die Verwendung einer Sammlung abfragbarer Typen in der Projektion wird nicht unterstützt</span><span class="sxs-lookup"><span data-stu-id="11201-427">Using a collection of Queryable type in projection is not supported</span></span>

[<span data-ttu-id="11201-428">Nachverfolgung von Issue 16314</span><span class="sxs-lookup"><span data-stu-id="11201-428">Tracking Issue #16314</span></span>](https://github.com/dotnet/efcore/issues/16314)

<span data-ttu-id="11201-429">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="11201-429">**Old behavior**</span></span>

<span data-ttu-id="11201-430">Bislang war es in einigen Fällen möglich, eine Sammlung eines abfragbaren Typs innerhalb der Projektion zu verwenden, z. B. als Argument für einen `List<T>`-Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="11201-430">Previously, it was possible to use collection of a Queryable type inside the projection in some cases, for example as an argument to a `List<T>` constructor:</span></span>

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

<span data-ttu-id="11201-431">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="11201-431">**New behavior**</span></span>

<span data-ttu-id="11201-432">Diese Abfragen werden nicht mehr unterstützt.</span><span class="sxs-lookup"><span data-stu-id="11201-432">These queries are no longer supported.</span></span> <span data-ttu-id="11201-433">Eine Ausnahme wird ausgelöst, die darauf hinweist, dass wir kein Objekt des abfragbaren Typs erstellen können, und vorschlägt, wie dies korrigiert werden könnte.</span><span class="sxs-lookup"><span data-stu-id="11201-433">Exception is thrown indicating that we can't create an object of Queryable type and suggesting how this could be fixed.</span></span>

<span data-ttu-id="11201-434">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="11201-434">**Why**</span></span>

<span data-ttu-id="11201-435">Wir können ein Objekt eines abfragbaren Typs nicht materialisieren. Daher würde es stattdessen automatisch mit dem Typ `List<T>` erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="11201-435">We can't materialize an object of a Queryable type, so they would automatically be created using `List<T>` type instead.</span></span> <span data-ttu-id="11201-436">Dies führte oft zu einer Ausnahme aufgrund nicht übereinstimmender Typen, was nicht sehr klar wurde und für einige Benutzer überraschend sein konnte.</span><span class="sxs-lookup"><span data-stu-id="11201-436">This would often cause an exception due to type mismatch which was not very clear and could be surprising to some users.</span></span> <span data-ttu-id="11201-437">Wir haben uns entschieden, das Muster zu erkennen und eine aussagekräftigere Ausnahme auszulösen.</span><span class="sxs-lookup"><span data-stu-id="11201-437">We decided to recognize the pattern and throw a more meaningful exception.</span></span>

<span data-ttu-id="11201-438">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="11201-438">**Mitigations**</span></span>

<span data-ttu-id="11201-439">Fügen Sie hinter dem abfragbaren Objekt in der Projektion einen Aufruf von `ToList()` hinzu:</span><span class="sxs-lookup"><span data-stu-id="11201-439">Add `ToList()` call after the Queryable object in the projection:</span></span>

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
