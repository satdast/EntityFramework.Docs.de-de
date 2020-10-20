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
# <a name="breaking-changes-in-ef-core-50"></a>Breaking Changes in EF Core 5.0

Die folgenden API-Änderungen und Behavior Changes können dazu führen, dass vorhandene Anwendungen nach einem Upgrade auf EF Core 5.0.0 nicht mehr funktionieren.

## <a name="summary"></a>Zusammenfassung

| **Wichtige Änderung**                                                                                                                   | **Auswirkungen** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität](#required-dependent)                                 | Medium     |
| [Die definierende Abfrage wurde durch anbieterspezifische Methoden ersetzt](#defining-query)                                                          | Medium     |
| [Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt](#geometric-sqlite)                                                   | Niedrig        |
| [Cosmos: Der Partitionsschlüssel wird nun dem Primärschlüssel hinzugefügt](#cosmos-partition-key)                                                        | Niedrig        |
| [Cosmos: Die `id`-Eigenschaft wurde umbenannt in `__id`](#cosmos-id)                                                                                 | Niedrig        |
| [Cosmos: byte[] wird nun als Base64-Zeichenfolge statt als Zahlenarray gespeichert](#cosmos-byte)                                             | Niedrig        |
| [Cosmos: GetPropertyName und SetPropertyName wurden umbenannt](#cosmos-metadata)                                                          | Niedrig        |
| [Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird](#non-added-generation) | Niedrig        |
| [IMigrationsModelDiffer verwendet jetzt IRelationalModel](#relational-model)                                                                 | Niedrig        |
| [Diskriminatoren sind schreibgeschützt](#read-only-discriminators)                                                                             | Niedrig        |
| [Anbieterspezifische EF.Functions-Methoden werden für InMemory-Anbieter ausgelöst.](#no-client-methods)                                              | Niedrig        |
| [IndexBuilder.HasName ist mittlerweile veraltet](#index-obsolete)                                                                               | Niedrig        |
| [Ein Pluarlizer ist nun für den Gerüstbau zurückentwickelter Modelle enthalten](#pluralizer)                                                 | Niedrig        |

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt.

[Issue 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**Altes Verhalten**

Die HasGeometricDimension-Methode wurde dazu verwendet, zusätzliche Maße (Z und M) für Geometriespalten zu ermöglichen. Dies hat sich jedoch immer nur auf die Datenbankerstellung ausgewirkt. Das Angeben dieser Methode zum Abfragen von Werten mit zusätzlichen Maßen war nicht notwendig. Außerdem hat die Methode nicht ordnungsgemäß funktioniert, wenn Werte mit zusätzlichen Maßen eingefügt oder geändert wurden (siehe [Issue 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).

**Neues Verhalten**

Zum Ermöglichen des Einfügens und Änderns von Geometriewerten mit zusätzlichen Maßen (Z und M) muss das jeweilige Maß als Teil des Spaltentypnamens angegeben werden. Diese API entspricht eher dem zugrunde liegenden Verhalten der AddGeometryColumn-Funktion von SpatiaLite.

**Hintergründe**

Die Verwendung der HasGeometricDimension-Methode, nachdem das Maß im Spaltentyp festgelegt wurde, ist redundant und nicht notwendig. Daher wurde die gesamte HasGeometricDimension-Methode entfernt.

**Vorbeugende Maßnahmen**

Verwenden Sie `HasColumnType` zum Festlegen der Maße:

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

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a>Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität

[Tracking Issue 17286](https://github.com/aspnet/EntityFrameworkCore/issues/17286)

**Altes Verhalten**

Nur die Navigation zum Prinzipal konnte als erforderlich konfiguriert werden. Daher führte die Verwendung von `RequiredAttribute` für die Navigation zur abhängigen Entität (der Entität, die den Fremdschlüssel enthält) stattdessen zur Erstellung des Fremdschlüssels für den definierenden Entitätstyp.

**Neues Verhalten**

Dank der hinzugefügten Unterstützung von erforderlichen abhängigen Entitäten ist es nun möglich, jede beliebige Verweisnavigation als erforderlich zu markieren. Dies bedeutet, dass im obigen Fall der Fremdschlüssel auf der anderen Seite der Beziehung definiert wird und die Eigenschaften nicht als erforderlich markiert werden.

Das Aufrufen von `IsRequired` vor der Angabe der abhängigen Entität ist nun mehrdeutig:

```csharp
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

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a>Cosmos: Der Partitionsschlüssel wird nun dem Primärschlüssel hinzugefügt

[Tracking Issue 15289](https://github.com/aspnet/EntityFrameworkCore/issues/15289)

**Altes Verhalten**

Die Partitionsschlüsseleigenschaft wurde nur dem alternativen Schlüssel hinzugefügt, der `id` enthält.

**Neues Verhalten**

Die Partitionsschlüsseleigenschaft wird gemäß Konvention jetzt auch dem Primärschlüssel hinzugefügt.

**Hintergründe**

Durch diese Änderung ist das Modell besser auf die Azure Cosmos DB-Semantik abgestimmt. Zudem verbessert sich dadurch die Leistung von `Find` und einigen Abfragen.

**Vorbeugende Maßnahmen**

Um zu verhindern, dass die Partitionsschlüsseleigenschaft dem Primärschlüssel hinzugefügt wird, konfigurieren Sie sie in `OnModelCreating`.

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a>Cosmos: Die `id`-Eigenschaft wurde umbenannt in `__id`

[Issue 17751](https://github.com/aspnet/EntityFrameworkCore/issues/17751)

**Altes Verhalten**

Die der JSON-Eigenschaft `id` zugeordnete Schatteneigenschaft hatte ebenfalls den Namen `id`.

**Neues Verhalten**

Die gemäß Konvention erstellte Schatteneigenschaft trägt nun den Namen `__id`.

**Hintergründe**

Durch diese Änderung ist es weniger wahrscheinlich, dass die Eigenschaft `id` mit einer vorhandenen Eigenschaft des Entitätstyps kollidiert.

**Vorbeugende Maßnahmen**

Wenn Sie zum Verhalten der Version 3.x zurückkehren möchten, konfigurieren Sie die Eigenschaft `id` in `OnModelCreating`.

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a>Cosmos: byte[] wird nun als Base64-Zeichenfolge statt als Zahlenarray gespeichert

[Issue 17306](https://github.com/aspnet/EntityFrameworkCore/issues/17306)

**Altes Verhalten**

Eigenschaften vom Typ „byte[]“ wurden als Zahlenarray gespeichert.

**Neues Verhalten**

Eigenschaften vom Typ „byte[]“ werden nun als Base64-Zeichenfolge gespeichert.

**Hintergründe**

Diese Darstellung von „byte[]“ entspricht eher den Erwartungen. Zudem handelt es sich hier um das Standardverhalten der wichtigen JSON-Serialisierungsbibliotheken.

**Vorbeugende Maßnahmen**

Als Zahlenarrays gespeicherte, bereits vorhandene Daten werden weiterhin ordnungsgemäß abgefragt. Derzeit wird jedoch keine Möglichkeit unterstützt, das Einfügeverhalten zu ändern. Wenn Ihr Szenario durch diese Einschränkung blockiert wird, kommentieren Sie dieses [Problem](https://github.com/aspnet/EntityFrameworkCore/issues/17306).

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a>Cosmos: GetPropertyName und SetPropertyName wurden umbenannt

[Issue 17874](https://github.com/aspnet/EntityFrameworkCore/issues/17874)

**Altes Verhalten**

Bisher wurden die Erweiterungsmethoden `GetPropertyName` und `SetPropertyName` genannt.

**Neues Verhalten**

Die alte API wurde entfernt, und es wurden die neuen Methoden `GetJsonPropertyName` und `SetJsonPropertyName` hinzugefügt.

**Hintergründe**

Durch diese Änderung wird die Mehrdeutigkeit um die Konfiguration durch diese Methoden beseitigt.

**Vorbeugende Maßnahmen**

Verwenden Sie die neue API.

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

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a>Die definierende Abfrage wurde durch anbieterspezifische Methoden ersetzt

[Issue 18903](https://github.com/dotnet/efcore/issues/18903)

**Altes Verhalten**

Entitätstypen wurden zum Definieren von Abfragen auf der Kernebene zugeordnet. Jedes Mal, wenn der Entitätstyp im Abfragestamm des Entitätstyps verwendet wurde, wurde er bei allen Anbietern durch die definierende Abfrage ersetzt.

**Neues Verhalten**

APIs für definierende Abfragen sind veraltet. Es wurden neue anbieterspezifische APIs eingeführt.

**Hintergründe**

Während definierende Abfragen als Ersatzabfragen implementiert wurden, wenn in der Abfrage der Abfragestamm verwendet wird, sind einige Probleme aufgetreten:

- Wenn eine definierende Abfrage einen Entitätstyp mit `new { ... }` in die `Select`-Methode projiziert, war für die Identifizierung dessen als Entität zusätzliche Arbeit erforderlich. Zudem sorgte dieses Vorgehen für Inkonsistenzen in Bezug darauf, wie EF Core nominale Typen in der Abfrage behandelt.
- Bei relationalen Anbietern ist `FromSql` weiterhin erforderlich, um die SQL-Zeichenfolge im LINQ-Ausdrucksformat zu übergeben.

Zunächst wurden definierende Abfragen als clientseitige Sichten zur Verwendung mit In-Memory-Anbietern bei schlüssellosen Entitäten eingeführt (ähnlich wie bei Datenbanksichten in relationalen Datenbanken). Eine solche Definition erleichtert das Testen der Anwendung mit In-Memory-Datenbanken. Später wurden sie allgemein anwendbar, was zwar nützlich war, aber ein inkonsistentes und schwer verständliches Verhalten mit sich brachte. Daher haben wir uns entschieden, das Konzept zu vereinfachen. Wir stellen die LINQ-basierte definierende Abfrage nur noch für In-Memory-Anbieter bereit und behandeln sie anders. Weitere Informationen finden Sie in [diesem Thema](https://github.com/dotnet/efcore/issues/20023).

**Vorbeugende Maßnahmen**

Verwenden Sie für relationale Anbieter die `ToSqlQuery`-Methode in `OnModelCreating`, und übergeben Sie eine SQL-Zeichenfolge zur Verwendung für den Entitätstyp.
Verwenden Sie für den In-Memory-Anbieter die `ToInMemoryQuery`-Methode in `OnModelCreating`, und übergeben Sie eine LINQ-Abfrage zur Verwendung für den Entitätstyp.

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a>Anbieterspezifische EF.Functions-Methoden werden für InMemory-Anbieter ausgelöst.

[Issue 20294](https://github.com/dotnet/efcore/issues/20294)

**Altes Verhalten**

Anbieterspezifische EF.Functions-Methoden enthielten eine Implementierung für die Clientausführung, sodass sie für den InMemory-Anbieter ausgeführt werden konnten. `EF.Functions.DateDiffDay` ist beispielsweise eine SQL Server-spezifische Methode, die für InMemory-Anbieter galt.

**Neues Verhalten**

Anbieterspezifische Methoden wurden aktualisiert, sodass sie im Methodentext eine Ausnahme auslösen, um ihre Auswertung auf Clientseite zu blockieren.

**Hintergründe**

Anbieterspezifische Methoden sind einer Datenbankfunktion zugeordnet. Das Berechnungsergebnis der zugeordneten Datenbankfunktion kann auf Clientseite in LINQ nicht immer repliziert werden. Dies kann dazu führen, dass sich das Ergebnis von dem des Servers unterscheidet, wenn dieselbe Methode auf dem Client ausgeführt wird. Da diese Methoden in LINQ verwendet werden, um Übersetzungen in bestimmte Datenbankfunktionen durchzuführen, müssen sie nicht auf Clientseite ausgewertet werden. Da der InMemory-Anbieter eine andere *Datenbank* ist, sind die Methoden für diesen Anbieter nicht verfügbar. Wenn Sie versuchen, sie für den InMemory-Anbieter oder einen anderen Anbieter auszuführen, der diese Methoden nicht übersetzt, wird eine Ausnahme ausgelöst.

**Vorbeugende Maßnahmen**

Da es keine Möglichkeit gibt, das Verhalten von Datenbankfunktionen exakt zu imitieren, sollten Sie die Abfragen, die diese Methoden enthalten, mit demselben Datenbanktyp testen, der auch in der Produktionsumgebung verwendet wird.

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a>IndexBuilder.HasName ist mittlerweile veraltet

[Nachverfolgung von Issue 21089](https://github.com/dotnet/efcore/issues/21089)

**Altes Verhalten**

Bisher konnte nur ein Index für eine bestimmte Gruppe von Eigenschaften definiert werden. Der Datenbankname eines Indexes wurde mithilfe von IndexBuilder.HasName konfiguriert.

**Neues Verhalten**

Für dieselbe Gruppe oder dieselben Eigenschaften sind jetzt mehrere Indizes zulässig. Diese Indizes unterscheiden sich nun durch einen Namen im Modell. Gemäß der Konvention wird der Modellname als Datenbankname verwendet. Er kann jedoch auch unabhängig mit HasDatabaseName konfiguriert werden.

**Hintergründe**

In Zukunft möchten wir sowohl aufsteigende als auch absteigende Indizes oder Indizes mit unterschiedlichen Sortierungen für denselben Satz von Eigenschaften aktivieren. Diese Änderung ist ein weiterer Schritt in diese Richtung.

**Vorbeugende Maßnahmen**

Sämtlicher Code, der zuvor IndexBuilder.HasName aufgerufen hat, sollte so aktualisiert werden, dass stattdessen HasDatabaseName aufgerufen wird.

Wenn das Projekt Migrationen enthält, die vor EF Core Version 2.0.0 generiert wurden, können Sie die Warnung in diesen Dateien problemlos ignorieren und unterdrücken, indem Sie `#pragma warning disable 612, 618` hinzufügen.

<a name="pluralizer"></a>

### <a name="a-pluarlizer-is-now-included-for-scaffolding-reverse-engineered-models"></a>Ein Pluarlizer ist nun für den Gerüstbau zurückentwickelter Modelle enthalten

[Nachverfolgung von Issue 11160](https://github.com/dotnet/efcore/issues/11160)

**Altes Verhalten**

Früher mussten Sie beim Gerüstbau für einen DbContext und Entitätstypen durch Reverse Engineering eines Datenbankschemas ein separates Pluralizer-Paket installieren, um DbSet- und Sammlungsnavigationsnamen in die Pluralform und Tabellennamen in die Singularform umzuwandeln.

**Neues Verhalten**

EF Core enthält jetzt einen Pluarlizer, der die [Humanizer](https://github.com/Humanizr/Humanizer)-Bibliothek verwendet. Dies ist dieselbe Bibliothek, die Visual Studio verwendet, um Variablennamen zu empfehlen.

**Hintergründe**

Die Verwendung von Pluralformen von Wörtern für Sammlungseigenschaften und Singularformen für Typen und Verweiseigenschaften ist in .NET idiomatisch.

**Vorbeugende Maßnahmen**

Um den Pluralizer zu deaktivieren, verwenden Sie die Option `--no-pluralize` auf `dotnet ef dbcontext scaffold` oder den `-NoPluralize`-Switch auf `Scaffold-DbContext`.
