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
# <a name="breaking-changes-in-ef-core-50"></a>Breaking Changes in EF Core 5.0

Die folgenden API-Änderungen und Behavior Changes können dazu führen, dass vorhandene Anwendungen nach einem Upgrade auf EF Core 5.0.0 nicht mehr funktionieren.

## <a name="summary"></a>Zusammenfassung

| **Wichtige Änderung**                                                                                                                   | **Auswirkungen** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität](#required-dependent)                                 | Medium     |
| [Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt](#geometric-sqlite)                                                   | Niedrig        |
| [Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird](#non-added-generation)  | Niedrig        |
| [IMigrationsModelDiffer verwendet jetzt IRelationalModel](#relational-model)                                                                 | Niedrig        |
| [Diskriminatoren sind schreibgeschützt](#read-only-discriminators)                                                                             | Niedrig        |

<a name="geometric-sqlite"></a>
### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt.

[Issue 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**Altes Verhalten**

Die HasGeometricDimension-Methode wurde dazu verwendet, zusätzliche Maße (Z und M) für Geometriespalten zu ermöglichen. Dies hat sich jedoch immer nur auf die Datenbankerstellung ausgewirkt. Das Angeben dieser Methode zum Abfragen von Werten mit zusätzlichen Maßen war nicht notwendig. Außerdem hat die Methode nicht ordnungsgemäß funktioniert, wenn Werte mit zusätzlichen Maßen eingefügt oder geändert wurden (siehe [Issue 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).

**Neues Verhalten**

Zum Ermöglichen des Einfügens und Änderns von Geometriewerten mit zusätzlichen Maßen (Z und M) muss das jeweilige Maß als Teil des Spaltentypnamens angegeben werden. Dies entspricht eher dem zugrunde liegenden Verhalten der AddGeometryColumn-Funktion von SpatiaLite.

**Hintergründe**

Die Verwendung der HasGeometricDimension-Methode, nachdem das Maß im Spaltentyp festgelegt wurde, ist redundant und nicht notwendig. Daher wurde die gesamte HasGeometricDimension-Methode entfernt.

**Vorbeugende Maßnahmen**

Verwenden Sie `HasColumnType` zum Festlegen der Maße:

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
### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a>Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität

[Tracking Issue 17286](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

**Altes Verhalten**

Nur die Navigation zum Prinzipal konnte als erforderlich konfiguriert werden. Daher führte die Verwendung von `RequiredAttribute` für die Navigation zur abhängigen Entität (der Entität, die den Fremdschlüssel enthält) stattdessen zur Erstellung des Fremdschlüssels für den definierenden Entitätstyp.

**Neues Verhalten**

Dank der hinzugefügten Unterstützung von erforderlichen abhängigen Entitäten ist es nun möglich, jede beliebige Verweisnavigation als erforderlich zu markieren. Dies bedeutet, dass im obigen Fall der Fremdschlüssel auf der anderen Seite der Beziehung definiert wird und die Eigenschaften nicht als erforderlich markiert werden.

Das Aufrufen von `IsRequired` vor der Angabe der abhängigen Entität ist nun mehrdeutig:

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

**Hintergründe**

Das neue Verhalten ist erforderlich, um die Unterstützung von erforderlichen abhängigen Entitäten zu ermöglichen ([siehe 12100](https://github.com/dotnet/efcore/issues/12100)).

**Vorbeugende Maßnahmen**

Entfernen Sie `RequiredAttribute` aus der Navigation zur abhängigen Entität, und verwenden Sie „RequiredAttribute“ stattdessen in der Navigation zum Prinzipal, oder konfigurieren Sie die Beziehung in `OnModelCreating`:

```cs
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="non-added-generation"></a>
### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a>Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird

[Tracking Issue 15289](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

**Altes Verhalten**

Wert-Generatoren wurden nur aufgerufen, wenn der Entitätszustand in „Hinzugefügt“ geändert wurde.

**Neues Verhalten**

Wert-Generatoren werden jetzt aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird und die Eigenschaft die Standardwerte enthält.

**Hintergründe**

Diese Änderung war erforderlich, um die Verwendung von Eigenschaften zu verbessern, die nicht dauerhaft im Datenspeicher gespeichert sind und deren Wert immer auf dem Client generiert wird.

**Vorbeugende Maßnahmen**

Weisen Sie der Eigenschaft vor der Änderung des Status einen anderen Wert als den Standardwert zu, um zu verhindern, dass der Wert-Generator aufgerufen wird.

<a name="relational-model"></a>
### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a>IMigrationsModelDiffer verwendet jetzt IRelationalModel

[Tracking Issue 20305](https://github.com/aspnet/EntityFrameworkCore/issues/20305)

**Altes Verhalten**

Die `IMigrationsModelDiffer`-API wurde mithilfe von `IModel` definiert.

**Neues Verhalten**

Die `IMigrationsModelDiffer`-API verwendet jetzt `IRelationalModel`. Die Modellmomentaufnahme enthält jedoch weiterhin nur `IModel`, da dieser Code Teil der Anwendung ist und Entity Framework ihn nicht ohne einen größeren Breaking Change ändern kann.

**Hintergründe**

`IRelationalModel` ist eine neu hinzugefügte Darstellung des Datenbankschemas. Die Verwendung dieser Darstellung zum Ermitteln von Unterschieden ist schneller und präziser.

**Vorbeugende Maßnahmen**

Verwenden Sie den folgenden Code, um das Modell von `snapshot` mit dem Modell von `context` zu vergleichen:

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

Eine Verbesserung dieser Funktion in Version 6.0 ist geplant ([siehe 22031](https://github.com/dotnet/efcore/issues/22031)).

<a name="read-only-discriminators"></a>
### <a name="discriminators-are-read-only"></a>Diskriminatoren sind schreibgeschützt

[Tracking Issue 21154](https://github.com/aspnet/EntityFrameworkCore/issues/21154)

**Altes Verhalten**

Der Diskriminatorwert konnte vor dem Aufrufen von `SaveChanges` geändert werden.

**Neues Verhalten**

Im obigen Fall wird eine Ausnahme ausgelöst.

**Hintergründe**

EF erwartet nicht, dass der Entitätstyp geändert wird, während er noch nachverfolgt wird, sodass das Ändern des Diskriminatorwerts zu einem inkonsistenten Kontext führt. Dies hat möglicherweise ein unerwartetes Verhalten zur Folge.

**Vorbeugende Maßnahmen**

Wenn eine Änderung des Diskriminatorwerts erforderlich ist und der Kontext direkt nach dem Aufruf von `SaveChanges` verworfen wird, kann der Diskriminator als änderbar festgelegt werden:

```cs
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```
