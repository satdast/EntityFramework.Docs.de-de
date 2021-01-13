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
# <a name="breaking-changes-in-ef-core-50"></a>Breaking Changes in EF Core 5.0

Die folgenden API-Änderungen und Behavior Changes können dazu führen, dass vorhandene Anwendungen nach einem Upgrade auf EF Core 5.0.0 nicht mehr funktionieren.

## <a name="summary"></a>Zusammenfassung

| **Wichtige Änderung**                                                                                                                   | **Auswirkungen** |
|:--------------------------------------------------------------------------------------------------------------------------------------|------------|
| [Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität](#required-dependent)                                 | Medium     |
| [Die definierende Abfrage wurde durch anbieterspezifische Methoden ersetzt](#defining-query)                                                          | Medium     |
| [Navigationen ohne Nullverweis werden von Abfragen nicht überschrieben](#nonnullreferences)                                                   | Medium     |
| [Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt](#geometric-sqlite)                                                   | Niedrig        |
| [Cosmos: Der Partitionsschlüssel wird nun dem Primärschlüssel hinzugefügt](#cosmos-partition-key)                                                        | Niedrig        |
| [Cosmos: Die `id`-Eigenschaft wurde umbenannt in `__id`](#cosmos-id)                                                                                 | Niedrig        |
| [Cosmos: byte[] wird nun als Base64-Zeichenfolge statt als Zahlenarray gespeichert](#cosmos-byte)                                             | Niedrig        |
| [Cosmos: GetPropertyName und SetPropertyName wurden umbenannt](#cosmos-metadata)                                                          | Niedrig        |
| [Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird](#non-added-generation) | Niedrig        |
| [IMigrationsModelDiffer verwendet jetzt IRelationalModel](#relational-model)                                                                 | Niedrig        |
| [ToView() wird von Migrationen anders verarbeitet](#toview)                                                                              | Niedrig        |
| [ToTable(null) markiert den Entitätstyp als nicht einer Tabelle zugeordnet](#totable)                                                              | Niedrig        |
| [Diskriminatoren sind schreibgeschützt](#read-only-discriminators)                                                                             | Niedrig        |
| [Anbieterspezifische EF.Functions-Methoden werden für InMemory-Anbieter ausgelöst.](#no-client-methods)                                              | Niedrig        |
| [IProperty.GetColumnName() gilt ab sofort als veraltet](#getcolumnname-obsolete)                                                                  | Niedrig        |
| [IndexBuilder.HasName ist mittlerweile veraltet](#index-obsolete)                                                                               | Niedrig        |
| [Ein Pluralizer ist nun für den Gerüstbau für Modelle mit Reverse Engineering enthalten](#pluralizer)                                                 | Niedrig        |
| [INavigationBase ersetzt INavigation in einigen APIs zur Unterstützung überspringender Navigationen](#inavigationbase)                                     | Niedrig        |
| [Einige Abfragen mit korrelierter Sammlung, die ebenfalls `Distinct` oder `GroupBy` verwenden, werden nicht mehr unterstützt](#collection-distinct-groupby) | Niedrig        |
| [Die Verwendung einer Sammlung abfragbarer Typen in der Projektion wird nicht unterstützt](#queryable-projection)                                          | Niedrig        |

## <a name="medium-impact-changes"></a>Änderungen mit mittlerer Auswirkung

<a name="required-dependent"></a>

### <a name="required-on-the-navigation-from-principal-to-dependent-has-different-semantics"></a>Andere Semantik der Kennzeichnung als erforderlich für die Navigation von Prinzipal zu abhängiger Entität

[Tracking Issue 17286](https://github.com/dotnet/efcore/issues/17286)

#### <a name="old-behavior"></a>Altes Verhalten

Nur die Navigation zum Prinzipal konnte als erforderlich konfiguriert werden. Daher führte die Verwendung von `RequiredAttribute` für die Navigation zur abhängigen Entität (der Entität, die den Fremdschlüssel enthält) stattdessen zur Erstellung des Fremdschlüssels für den definierenden Entitätstyp.

#### <a name="new-behavior"></a>Neues Verhalten

Dank der hinzugefügten Unterstützung von erforderlichen abhängigen Entitäten ist es nun möglich, jede beliebige Verweisnavigation als erforderlich zu markieren. Dies bedeutet, dass im obigen Fall der Fremdschlüssel auf der anderen Seite der Beziehung definiert wird und die Eigenschaften nicht als erforderlich markiert werden.

Das Aufrufen von `IsRequired` vor der Angabe der abhängigen Entität ist nun mehrdeutig:

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .IsRequired()
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
```

#### <a name="why"></a>Warum?

Das neue Verhalten ist erforderlich, um die Unterstützung von erforderlichen abhängigen Entitäten zu ermöglichen ([siehe 12100](https://github.com/dotnet/efcore/issues/12100)).

#### <a name="mitigations"></a>Gegenmaßnahmen

Entfernen Sie `RequiredAttribute` aus der Navigation zur abhängigen Entität, und verwenden Sie „RequiredAttribute“ stattdessen in der Navigation zum Prinzipal, oder konfigurieren Sie die Beziehung in `OnModelCreating`:

```csharp
modelBuilder.Entity<Blog>()
    .HasOne(b => b.BlogImage)
    .WithOne(i => i.Blog)
    .HasForeignKey<BlogImage>(b => b.BlogForeignKey)
    .IsRequired();
```

<a name="defining-query"></a>

### <a name="defining-query-is-replaced-with-provider-specific-methods"></a>Die definierende Abfrage wurde durch anbieterspezifische Methoden ersetzt

[Issue 18903](https://github.com/dotnet/efcore/issues/18903)

#### <a name="old-behavior"></a>Altes Verhalten

Entitätstypen wurden zum Definieren von Abfragen auf der Kernebene zugeordnet. Jedes Mal, wenn der Entitätstyp im Abfragestamm des Entitätstyps verwendet wurde, wurde er bei allen Anbietern durch die definierende Abfrage ersetzt.

#### <a name="new-behavior"></a>Neues Verhalten

APIs für definierende Abfragen sind veraltet. Es wurden neue anbieterspezifische APIs eingeführt.

#### <a name="why"></a>Warum?

Während definierende Abfragen als Ersatzabfragen implementiert wurden, wenn in der Abfrage der Abfragestamm verwendet wird, sind einige Probleme aufgetreten:

- Wenn eine definierende Abfrage einen Entitätstyp mit `new { ... }` in die `Select`-Methode projiziert, war für die Identifizierung dessen als Entität zusätzliche Arbeit erforderlich. Zudem sorgte dieses Vorgehen für Inkonsistenzen in Bezug darauf, wie EF Core nominale Typen in der Abfrage behandelt.
- Bei relationalen Anbietern ist `FromSql` weiterhin erforderlich, um die SQL-Zeichenfolge im LINQ-Ausdrucksformat zu übergeben.

Zunächst wurden definierende Abfragen als clientseitige Sichten zur Verwendung mit In-Memory-Anbietern bei schlüssellosen Entitäten eingeführt (ähnlich wie bei Datenbanksichten in relationalen Datenbanken). Eine solche Definition erleichtert das Testen der Anwendung mit In-Memory-Datenbanken. Später wurden sie allgemein anwendbar, was zwar nützlich war, aber ein inkonsistentes und schwer verständliches Verhalten mit sich brachte. Daher haben wir uns entschieden, das Konzept zu vereinfachen. Wir stellen die LINQ-basierte definierende Abfrage nur noch für In-Memory-Anbieter bereit und behandeln sie anders. Weitere Informationen finden Sie in [diesem Thema](https://github.com/dotnet/efcore/issues/20023).

#### <a name="mitigations"></a>Gegenmaßnahmen

Verwenden Sie für relationale Anbieter die `ToSqlQuery`-Methode in `OnModelCreating`, und übergeben Sie eine SQL-Zeichenfolge zur Verwendung für den Entitätstyp.
Verwenden Sie für den In-Memory-Anbieter die `ToInMemoryQuery`-Methode in `OnModelCreating`, und übergeben Sie eine LINQ-Abfrage zur Verwendung für den Entitätstyp.

<a name="nonnullreferences"></a>

### <a name="non-null-reference-navigations-are-not-overwritten-by-queries"></a>Navigationen ohne Nullverweis werden von Abfragen nicht überschrieben

[Nachverfolgung von Issue 2693](https://github.com/dotnet/EntityFramework.Docs/issues/2693)

#### <a name="old-behavior"></a>Altes Verhalten

In EF Core 3.1 wurden Verweisnavigationen, die frühzeitig mit Werten ungleich NULL initialisiert wurden, mitunter von Entitätsinstanzen aus der Datenbank überschrieben, und zwar unabhängig davon, ob Schlüsselwerte übereinstimmten oder nicht. In anderen Fällen hat EF Core 3.1 jedoch das Gegenteil bewirkt und den vorhandenen Wert ungleich NULL beibehalten.

#### <a name="new-behavior"></a>Neues Verhalten

Ab EF Core 5.0 werden Verweisnavigationen ungleich NULL nicht mehr durch Instanzen überschrieben, die von einer Abfrage zurückgegeben werden.

Beachten Sie, dass die frühzeitige Initialisierung einer _Sammlungsnavigation_ als leere Sammlung nach wie vor unterstützt wird.

#### <a name="why"></a>Warum?

Die Initialisierung einer Verweisnavigationseigenschaft als „leere“ Entitätsinstanz führt zu einem uneindeutigen Zustand. Zum Beispiel:

```csharp
public class Blog
{
     public int Id { get; set; }
     public Author Author { get; set; ) = new Author();
}
```

Normalerweise erstellt eine Abfrage von Blogs und Autoren zunächst `Blog`-Instanzen. Dann werden die entsprechenden `Author`-Instanzen auf Grundlage der aus der Datenbank zurückgegebenen Daten festgelegt. In diesem Fall ist jedoch jede `Blog.Author`-Eigenschaft bereits als leere `Author`-Instanz initialisiert. Allerdings kann EF Core nicht wissen, dass diese Instanz „leer“ ist. Das Überschreiben dieser Instanz könnte also unbemerkt eine gültige `Author`-Instanz auslösen. Daher überschreibt EF Core 5.0 jetzt konsequent keine Navigation, die bereits initialisiert ist.

Dieses neue Verhalten stimmt in den meisten Fällen auch mit dem Verhalten von EF6 überein, obwohl wir bei Untersuchungen auch einige Fälle von Inkonsistenz in EF6 gefunden haben.  

#### <a name="mitigations"></a>Gegenmaßnahmen

Wenn dieses Problem auftritt, besteht die Lösung darin, die vorzeitige Initialisierung der Verweisnavigationseigenschaften zu beenden.

## <a name="low-impact-changes"></a>Änderungen mit geringer Auswirkung

<a name="geometric-sqlite"></a>

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt.

[Issue 14257](https://github.com/dotnet/efcore/issues/14257)

#### <a name="old-behavior"></a>Altes Verhalten

Die HasGeometricDimension-Methode wurde dazu verwendet, zusätzliche Maße (Z und M) für Geometriespalten zu ermöglichen. Dies hat sich jedoch immer nur auf die Datenbankerstellung ausgewirkt. Das Angeben dieser Methode zum Abfragen von Werten mit zusätzlichen Maßen war nicht notwendig. Außerdem hat die Methode nicht ordnungsgemäß funktioniert, wenn Werte mit zusätzlichen Maßen eingefügt oder geändert wurden (siehe [Issue 14257](https://github.com/dotnet/efcore/issues/14257)).

#### <a name="new-behavior"></a>Neues Verhalten

Zum Ermöglichen des Einfügens und Änderns von Geometriewerten mit zusätzlichen Maßen (Z und M) muss das jeweilige Maß als Teil des Spaltentypnamens angegeben werden. Diese API entspricht eher dem zugrunde liegenden Verhalten der AddGeometryColumn-Funktion von SpatiaLite.

#### <a name="why"></a>Warum?

Die Verwendung der HasGeometricDimension-Methode, nachdem das Maß im Spaltentyp festgelegt wurde, ist redundant und nicht notwendig. Daher wurde die gesamte HasGeometricDimension-Methode entfernt.

#### <a name="mitigations"></a>Gegenmaßnahmen

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

<a name="cosmos-partition-key"></a>

### <a name="cosmos-partition-key-is-now-added-to-the-primary-key"></a>Cosmos: Der Partitionsschlüssel wird nun dem Primärschlüssel hinzugefügt

[Tracking Issue 15289](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a>Altes Verhalten

Die Partitionsschlüsseleigenschaft wurde nur dem alternativen Schlüssel hinzugefügt, der `id` enthält.

#### <a name="new-behavior"></a>Neues Verhalten

Die Partitionsschlüsseleigenschaft wird gemäß Konvention jetzt auch dem Primärschlüssel hinzugefügt.

#### <a name="why"></a>Warum?

Durch diese Änderung ist das Modell besser auf die Azure Cosmos DB-Semantik abgestimmt. Zudem verbessert sich dadurch die Leistung von `Find` und einigen Abfragen.

#### <a name="mitigations"></a>Gegenmaßnahmen

Um zu verhindern, dass die Partitionsschlüsseleigenschaft dem Primärschlüssel hinzugefügt wird, konfigurieren Sie sie in `OnModelCreating`.

```csharp
modelBuilder.Entity<Blog>()
    .HasKey(b => b.Id);
```

<a name="cosmos-id"></a>

### <a name="cosmos-id-property-renamed-to-__id"></a>Cosmos: Die `id`-Eigenschaft wurde umbenannt in `__id`

[Issue 17751](https://github.com/dotnet/efcore/issues/17751)

#### <a name="old-behavior"></a>Altes Verhalten

Die der JSON-Eigenschaft `id` zugeordnete Schatteneigenschaft hatte ebenfalls den Namen `id`.

#### <a name="new-behavior"></a>Neues Verhalten

Die gemäß Konvention erstellte Schatteneigenschaft trägt nun den Namen `__id`.

#### <a name="why"></a>Warum?

Durch diese Änderung ist es weniger wahrscheinlich, dass die Eigenschaft `id` mit einer vorhandenen Eigenschaft des Entitätstyps kollidiert.

#### <a name="mitigations"></a>Gegenmaßnahmen

Wenn Sie zum Verhalten der Version 3.x zurückkehren möchten, konfigurieren Sie die Eigenschaft `id` in `OnModelCreating`.

```csharp
modelBuilder.Entity<Blog>()
    .Property<string>("id")
    .ToJsonProperty("id");
```

<a name="cosmos-byte"></a>

### <a name="cosmos-byte-is-now-stored-as-a-base64-string-instead-of-a-number-array"></a>Cosmos: byte[] wird nun als Base64-Zeichenfolge statt als Zahlenarray gespeichert

[Issue 17306](https://github.com/dotnet/efcore/issues/17306)

#### <a name="old-behavior"></a>Altes Verhalten

Eigenschaften vom Typ „byte[]“ wurden als Zahlenarray gespeichert.

#### <a name="new-behavior"></a>Neues Verhalten

Eigenschaften vom Typ „byte[]“ werden nun als Base64-Zeichenfolge gespeichert.

#### <a name="why"></a>Warum?

Diese Darstellung von „byte[]“ entspricht eher den Erwartungen. Zudem handelt es sich hier um das Standardverhalten der wichtigen JSON-Serialisierungsbibliotheken.

#### <a name="mitigations"></a>Gegenmaßnahmen

Als Zahlenarrays gespeicherte, bereits vorhandene Daten werden weiterhin ordnungsgemäß abgefragt. Derzeit wird jedoch keine Möglichkeit unterstützt, das Einfügeverhalten zu ändern. Wenn Ihr Szenario durch diese Einschränkung blockiert wird, kommentieren Sie dieses [Problem](https://github.com/dotnet/efcore/issues/17306).

<a name="cosmos-metadata"></a>

### <a name="cosmos-getpropertyname-and-setpropertyname-were-renamed"></a>Cosmos: GetPropertyName und SetPropertyName wurden umbenannt

[Issue 17874](https://github.com/dotnet/efcore/issues/17874)

#### <a name="old-behavior"></a>Altes Verhalten

Bisher wurden die Erweiterungsmethoden `GetPropertyName` und `SetPropertyName` genannt.

#### <a name="new-behavior"></a>Neues Verhalten

Die alte API wurde entfernt, und es wurden die neuen Methoden `GetJsonPropertyName` und `SetJsonPropertyName` hinzugefügt.

#### <a name="why"></a>Warum?

Durch diese Änderung wird die Mehrdeutigkeit um die Konfiguration durch diese Methoden beseitigt.

#### <a name="mitigations"></a>Gegenmaßnahmen

Verwenden Sie die neue API.

<a name="non-added-generation"></a>

### <a name="value-generators-are-called-when-the-entity-state-is-changed-from-detached-to-unchanged-updated-or-deleted"></a>Wert-Generatoren werden aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird

[Tracking Issue 15289](https://github.com/dotnet/efcore/issues/15289)

#### <a name="old-behavior"></a>Altes Verhalten

Wert-Generatoren wurden nur aufgerufen, wenn der Entitätszustand in „Hinzugefügt“ geändert wurde.

#### <a name="new-behavior"></a>Neues Verhalten

Wert-Generatoren werden jetzt aufgerufen, wenn der Entitätszustand von „Getrennt“ in „Unverändert“, „Aktualisiert“ oder „Gelöscht“ geändert wird und die Eigenschaft die Standardwerte enthält.

#### <a name="why"></a>Warum?

Diese Änderung war erforderlich, um die Verwendung von Eigenschaften zu verbessern, die nicht dauerhaft im Datenspeicher gespeichert sind und deren Wert immer auf dem Client generiert wird.

#### <a name="mitigations"></a>Gegenmaßnahmen

Weisen Sie der Eigenschaft vor der Änderung des Status einen anderen Wert als den Standardwert zu, um zu verhindern, dass der Wert-Generator aufgerufen wird.

<a name="relational-model"></a>

### <a name="imigrationsmodeldiffer-now-uses-irelationalmodel"></a>IMigrationsModelDiffer verwendet jetzt IRelationalModel

[Tracking Issue 20305](https://github.com/dotnet/efcore/issues/20305)

#### <a name="old-behavior"></a>Altes Verhalten

Die `IMigrationsModelDiffer`-API wurde mithilfe von `IModel` definiert.

#### <a name="new-behavior"></a>Neues Verhalten

Die `IMigrationsModelDiffer`-API verwendet jetzt `IRelationalModel`. Die Modellmomentaufnahme enthält jedoch weiterhin nur `IModel`, da dieser Code Teil der Anwendung ist und Entity Framework ihn nicht ohne einen größeren Breaking Change ändern kann.

#### <a name="why"></a>Warum?

`IRelationalModel` ist eine neu hinzugefügte Darstellung des Datenbankschemas. Die Verwendung dieser Darstellung zum Ermitteln von Unterschieden ist schneller und präziser.

#### <a name="mitigations"></a>Gegenmaßnahmen

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

<a name="toview"></a>

### <a name="toview-is-treated-differently-by-migrations"></a>ToView() wird von Migrationen anders verarbeitet

[Nachverfolgung von Issue #2725](https://github.com/dotnet/efcore/issues/2725)

#### <a name="old-behavior"></a>Altes Verhalten

Das Aufrufen von `ToView(string)` hat dazu geführt, dass Migrationen den Entitätstyp ignorieren und ihn einer Ansicht zuordnen.

#### <a name="new-behavior"></a>Neues Verhalten

`ToView(string)` markiert den Entitätstyp nun als nicht einer Tabelle zugeordnet und ordnet ihn einer Ansicht zu. Dies führt dazu, dass bei der ersten Migration nach einem Upgrade zu EF Core 5 versucht wird, die Standardtabelle für diesen Entitätstyp zu löschen, da er nicht mehr ignoriert wird.

#### <a name="why"></a>Warum?

EF Core ermöglicht es nun, dass ein Entitätstyp gleichzeitig sowohl einer Tabelle als auch einer Ansicht zugeordnet werden kann. `ToView` ist also kein gültiger Indikator dafür mehr, dass der Typ von Migrationen ignoriert werden soll.

#### <a name="mitigations"></a>Gegenmaßnahmen

Verwenden Sie den folgenden Code, um die zugeordnete Tabelle als von Migrationen ausgeschlossen zu markieren:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().ToTable("UserView", t => t.ExcludeFromMigrations());
}
```

<a name="totable"></a>

### <a name="totablenull-marks-the-entity-type-as-not-mapped-to-a-table"></a>ToTable(null) markiert den Entitätstyp als nicht einer Tabelle zugeordnet

[Nachverfolgung von Issue #21172](https://github.com/dotnet/efcore/issues/21172)

#### <a name="old-behavior"></a>Altes Verhalten

`ToTable(null)` hat den Tabellennamen auf den Standardwert zurückgesetzt.

#### <a name="new-behavior"></a>Neues Verhalten

`ToTable(null)` markiert den Entitätstyp nun als nicht einer Tabelle zugeordnet.

#### <a name="why"></a>Warum?

EF Core ermöglicht es nun, dass ein Entitätstyp gleichzeitig sowohl einer Tabelle als auch einer Ansicht zugeordnet werden kann. `ToTable(null)` wird also verwendet, um anzugeben, dass der Typ keiner Tabelle zugeordnet ist.

#### <a name="mitigations"></a>Gegenmaßnahmen

Verwenden Sie den folgenden Code, um den Tabellennamen in den Standardwert zurückzusetzen, wenn keine Zuordnung zu einer Ansicht oder einer DbFunction-Klasse vorliegt:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<User>().Metadata.RemoveAnnotation(RelationalAnnotationNames.TableName);
}
```

<a name="read-only-discriminators"></a>

### <a name="discriminators-are-read-only"></a>Diskriminatoren sind schreibgeschützt

[Tracking Issue 21154](https://github.com/dotnet/efcore/issues/21154)

#### <a name="old-behavior"></a>Altes Verhalten

Der Diskriminatorwert konnte vor dem Aufrufen von `SaveChanges` geändert werden.

#### <a name="new-behavior"></a>Neues Verhalten

Im obigen Fall wird eine Ausnahme ausgelöst.

#### <a name="why"></a>Warum?

EF erwartet nicht, dass der Entitätstyp geändert wird, während er noch nachverfolgt wird, sodass das Ändern des Diskriminatorwerts zu einem inkonsistenten Kontext führt. Dies hat möglicherweise ein unerwartetes Verhalten zur Folge.

#### <a name="mitigations"></a>Gegenmaßnahmen

Wenn eine Änderung des Diskriminatorwerts erforderlich ist und der Kontext direkt nach dem Aufruf von `SaveChanges` verworfen wird, kann der Diskriminator als änderbar festgelegt werden:

```csharp
modelBuilder.Entity<BaseEntity>()
    .Property<string>("Discriminator")
    .Metadata.SetAfterSaveBehavior(PropertySaveBehavior.Save);
```

<a name="no-client-methods"></a>

### <a name="provider-specific-effunctions-methods-throw-for-inmemory-provider"></a>Anbieterspezifische EF.Functions-Methoden werden für InMemory-Anbieter ausgelöst.

[Issue 20294](https://github.com/dotnet/efcore/issues/20294)

#### <a name="old-behavior"></a>Altes Verhalten

Anbieterspezifische EF.Functions-Methoden enthielten eine Implementierung für die Clientausführung, sodass sie für den InMemory-Anbieter ausgeführt werden konnten. `EF.Functions.DateDiffDay` ist beispielsweise eine SQL Server-spezifische Methode, die für InMemory-Anbieter galt.

#### <a name="new-behavior"></a>Neues Verhalten

Anbieterspezifische Methoden wurden aktualisiert, sodass sie im Methodentext eine Ausnahme auslösen, um ihre Auswertung auf Clientseite zu blockieren.

#### <a name="why"></a>Warum?

Anbieterspezifische Methoden sind einer Datenbankfunktion zugeordnet. Das Berechnungsergebnis der zugeordneten Datenbankfunktion kann auf Clientseite in LINQ nicht immer repliziert werden. Dies kann dazu führen, dass sich das Ergebnis von dem des Servers unterscheidet, wenn dieselbe Methode auf dem Client ausgeführt wird. Da diese Methoden in LINQ verwendet werden, um Übersetzungen in bestimmte Datenbankfunktionen durchzuführen, müssen sie nicht auf Clientseite ausgewertet werden. Da der InMemory-Anbieter eine andere *Datenbank* ist, sind die Methoden für diesen Anbieter nicht verfügbar. Wenn Sie versuchen, sie für den InMemory-Anbieter oder einen anderen Anbieter auszuführen, der diese Methoden nicht übersetzt, wird eine Ausnahme ausgelöst.

#### <a name="mitigations"></a>Gegenmaßnahmen

Da es keine Möglichkeit gibt, das Verhalten von Datenbankfunktionen exakt zu imitieren, sollten Sie die Abfragen, die diese Methoden enthalten, mit demselben Datenbanktyp testen, der auch in der Produktionsumgebung verwendet wird.

<a name="getcolumnname-obsolete"></a>

### <a name="ipropertygetcolumnname-is-now-obsolete"></a>IProperty.GetColumnName() gilt ab sofort als veraltet

[Nachverfolgung von Issue #2266](https://github.com/dotnet/efcore/issues/2266)

#### <a name="old-behavior"></a>Altes Verhalten

`GetColumnName()` hat den Namen der Spalte zurückgegeben, der die Eigenschaft zugeordnet ist.

#### <a name="new-behavior"></a>Neues Verhalten

`GetColumnName()` gibt weiterhin den Namen der Spalte zurück, der eine Eigenschaft zugeordnet ist. Dieses Verhalten ist nun jedoch mehrdeutig, da EF Core 5 die TPT-Methode (Tabelle pro Typ) und gleichzeitiges Zuordnen zu einer Ansicht oder einer Funktion unterstützt, wenn diese Zuordnungen verschiedene Spaltennamen für dieselbe Eigenschaft verwenden könnten.

#### <a name="why"></a>Warum?

Diese Methode wurde als veraltet gekennzeichnet, um Benutzern nahezulegen, eine exaktere Überladung zu verwenden: <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)>.

#### <a name="mitigations"></a>Gegenmaßnahmen

Verwenden Sie den folgenden Code, um den Spaltennamen für eine bestimmte Tabelle abzurufen:

```csharp
var columnName = property.GetColumnName(StoreObjectIdentifier.Table("Users", null)));
```

<a name="index-obsolete"></a>

### <a name="indexbuilderhasname-is-now-obsolete"></a>IndexBuilder.HasName ist mittlerweile veraltet

[Nachverfolgung von Issue 21089](https://github.com/dotnet/efcore/issues/21089)

#### <a name="old-behavior"></a>Altes Verhalten

Bisher konnte nur ein Index für eine bestimmte Gruppe von Eigenschaften definiert werden. Der Datenbankname eines Indexes wurde mithilfe von IndexBuilder.HasName konfiguriert.

#### <a name="new-behavior"></a>Neues Verhalten

Für dieselbe Gruppe oder dieselben Eigenschaften sind jetzt mehrere Indizes zulässig. Diese Indizes unterscheiden sich nun durch einen Namen im Modell. Gemäß der Konvention wird der Modellname als Datenbankname verwendet. Er kann jedoch auch unabhängig mit HasDatabaseName konfiguriert werden.

#### <a name="why"></a>Warum?

In Zukunft möchten wir sowohl aufsteigende als auch absteigende Indizes oder Indizes mit unterschiedlichen Sortierungen für denselben Satz von Eigenschaften aktivieren. Diese Änderung ist ein weiterer Schritt in diese Richtung.

#### <a name="mitigations"></a>Gegenmaßnahmen

Sämtlicher Code, der zuvor IndexBuilder.HasName aufgerufen hat, sollte so aktualisiert werden, dass stattdessen HasDatabaseName aufgerufen wird.

Wenn das Projekt Migrationen enthält, die vor EF Core Version 2.0.0 generiert wurden, können Sie die Warnung in diesen Dateien problemlos ignorieren und unterdrücken, indem Sie `#pragma warning disable 612, 618` hinzufügen.

<a name="pluralizer"></a>

### <a name="a-pluralizer-is-now-included-for-scaffolding-reverse-engineered-models"></a>Ein Pluralizer ist nun für den Gerüstbau für Modelle mit Reverse Engineering enthalten

[Nachverfolgung von Issue 11160](https://github.com/dotnet/efcore/issues/11160)

#### <a name="old-behavior"></a>Altes Verhalten

Bislang mussten Sie beim Gerüstbau für einen Datenbankkontext und Entitätstypen durch Reverse Engineering eines Datenbankschemas ein separates Pluralizer-Paket installieren, um DbSet- und Sammlungsnavigationsnamen in die Pluralform und Tabellennamen in die Singularform umzuwandeln.

#### <a name="new-behavior"></a>Neues Verhalten

EF Core enthält jetzt einen Pluarlizer, der die [Humanizer](https://github.com/Humanizr/Humanizer)-Bibliothek verwendet. Dies ist dieselbe Bibliothek, die Visual Studio verwendet, um Variablennamen zu empfehlen.

#### <a name="why"></a>Warum?

Die Verwendung von Pluralformen von Wörtern für Sammlungseigenschaften und Singularformen für Typen und Verweiseigenschaften ist in .NET idiomatisch.

#### <a name="mitigations"></a>Gegenmaßnahmen

Um den Pluralizer zu deaktivieren, verwenden Sie die Option `--no-pluralize` auf `dotnet ef dbcontext scaffold` oder den `-NoPluralize`-Switch auf `Scaffold-DbContext`.

<a name="inavigationbase"></a>

### <a name="inavigationbase-replaces-inavigation-in-some-apis-to-support-skip-navigations"></a>INavigationBase ersetzt INavigation in einigen APIs zur Unterstützung überspringender Navigationen

[Nachverfolgung von Issue 2568](https://github.com/dotnet/EntityFramework.Docs/issues/2568)

#### <a name="old-behavior"></a>Altes Verhalten

Bis Version 5.0 unterstützte EF Core nur eine Form der Navigationseigenschaft, die durch die `INavigation`-Schnittstelle dargestellt wird.

#### <a name="new-behavior"></a>Neues Verhalten

EF Core 5.0 führt m:n-Beziehungen ein, die „überspringende Navigationen“ verwenden. Diese werden durch die `ISkipNavigation`-Schnittstelle dargestellt, und der größte Teil der Funktionalität von `INavigation` wurde auf die gemeinsame Basisschnittstelle `INavigationBase` verlagert.

#### <a name="why"></a>Warum?

Die meisten Funktionen sind bei normalen und überspringenden Navigationen identisch. Allerdings haben überspringende Navigationen eine andere Beziehung zu Fremdschlüsseln als normale Navigationen, da sich die beteiligten Fremdschlüssel nicht direkt an beiden Enden der Beziehung befinden, sondern in der JOIN-Entität.

#### <a name="mitigations"></a>Gegenmaßnahmen

In vielen Fällen können Anwendungen ohne weitere Änderungen zur Verwendung der neuen Basisschnittstelle übergehen. In Fällen jedoch, in denen die Navigation für den Zugriff auf Fremdschlüsseleigenschaften verwendet wird, sollte der Code der Anwendung entweder nur auf normale Navigationen beschränkt oder so aktualisiert werden, dass er sowohl bei normalen als auch bei überspringenden Navigationen das Richtige tut.

<a name="collection-distinct-groupby"></a>

### <a name="some-queries-with-correlated-collection-that-also-use-distinct-or-groupby-are-no-longer-supported"></a>Einige Abfragen mit korrelierter Sammlung, die ebenfalls `Distinct` oder`GroupBy` verwenden, werden nicht mehr unterstützt

[Nachverfolgung von Issue 15873](https://github.com/dotnet/efcore/issues/15873)

**Altes Verhalten**

Zuvor haben wir die Ausführung von Abfragen mit korrelierten Sammlungen, gefolgt von `GroupBy`, sowie einige Abfragen mit `Distinct` erlaubt.

Beispiel für GroupBy:

```csharp
context.Parents
    .Select(p => p.Children
        .GroupBy(c => c.School)
        .Select(g => g.Key))
```

Beispiel für `Distinct`: insbesondere `Distinct`-Abfragen, bei denen die innere Sammlungsprojektion nicht den Primärschlüssel enthält:

```csharp
context.Parents
    .Select(p => p.Children
        .Select(c => c.School)
        .Distinct())
```

Diese Abfragen konnten falsche Ergebnisse liefern, wenn die innere Sammlung Duplikate enthielt, funktionierten aber einwandfrei, wenn alle Elemente in der inneren Sammlung eindeutig waren.

**Neues Verhalten**

Diese Abfragen werden nicht mehr unterstützt. Es wird eine Ausnahme ausgelöst, die darauf hinweist, dass wir nicht genügend Informationen haben, um die Ergebnisse ordnungsgemäß zu erstellen.

**Hintergründe**

Für Szenarien mit korrelierten Sammlungen müssen wir den Primärschlüssel der Entitäten kennen, um die Sammlungsentitäten dem richtigen übergeordneten Element zuweisen zu können. Wenn die innere Sammlung `GroupBy` oder `Distinct` nicht verwendet, kann der fehlende Primärschlüssel einfach zur Projektion hinzugefügt werden. Im Falle von `GroupBy` und `Distinct` ist dies jedoch nicht möglich, weil es das Ergebnis des Vorgangs `GroupBy` oder `Distinct` ändern würde.

**Vorbeugende Maßnahmen**

Schreiben Sie die Abfrage so um, dass die Vorgänge `GroupBy` oder `Distinct` nicht auf die innere Sammlung angewendet werden. Führen Sie diese Vorgänge stattdessen auf dem Client aus.

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

### <a name="using-a-collection-of-queryable-type-in-projection-is-not-supported"></a>Die Verwendung einer Sammlung abfragbarer Typen in der Projektion wird nicht unterstützt

[Nachverfolgung von Issue 16314](https://github.com/dotnet/efcore/issues/16314)

**Altes Verhalten**

Bislang war es in einigen Fällen möglich, eine Sammlung eines abfragbaren Typs innerhalb der Projektion zu verwenden, z. B. als Argument für einen `List<T>`-Konstruktor:

```csharp
context.Blogs
    .Select(b => new List<Post>(context.Posts.Where(p => p.BlogId == b.Id)))
```

**Neues Verhalten**

Diese Abfragen werden nicht mehr unterstützt. Eine Ausnahme wird ausgelöst, die darauf hinweist, dass wir kein Objekt des abfragbaren Typs erstellen können, und vorschlägt, wie dies korrigiert werden könnte.

**Hintergründe**

Wir können ein Objekt eines abfragbaren Typs nicht materialisieren. Daher würde es stattdessen automatisch mit dem Typ `List<T>` erstellt werden. Dies führte oft zu einer Ausnahme aufgrund nicht übereinstimmender Typen, was nicht sehr klar wurde und für einige Benutzer überraschend sein konnte. Wir haben uns entschieden, das Muster zu erkennen und eine aussagekräftigere Ausnahme auszulösen.

**Vorbeugende Maßnahmen**

Fügen Sie hinter dem abfragbaren Objekt in der Projektion einen Aufruf von `ToList()` hinzu:

```csharp
context.Blogs.Select(b => context.Posts.Where(p => p.BlogId == b.Id).ToList())
```
