---
title: Breaking Changes in EF Core 3.0
author: ajcvickers
ms.date: 12/03/2019
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: 6e0c17a22b56b206f18e47f678e3e237d5c42375
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413558"
---
# <a name="breaking-changes-included-in-ef-core-30"></a>Breaking Changes in EF Core 3.0

Die folgenden API-Änderungen und Behavior Changes können dazu führen, dass vorhandene Anwendungen beim Upgrade auf 3.0.0 nicht mehr funktionieren.
Änderungen, die sich voraussichtlich nur auf Datenbankanbieter auswirken, sind unter [Änderungen mit Auswirkungen auf den Anbieter](xref:core/providers/provider-log) dokumentiert.

## <a name="summary"></a>Zusammenfassung

| **Wichtige Änderung**                                                                                               | **Auswirkung** |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [LINQ-Abfragen werden nicht mehr auf dem Client ausgewertet](#linq-queries-are-no-longer-evaluated-on-the-client)         | High       |
| [EF Core 3.0 zielt auf .NET Standard 2.1 und nicht auf .NET Standard 2.0 ab](#netstandard21) | High      |
| [Das EF Core-Befehlszeilentool (dotnet ef) ist nicht mehr Bestandteil des .NET Core SDK](#dotnet-ef) | High      |
| [DetectChanges berücksichtigt vom Speicher generierte Schlüsselwerte](#dc) | High      |
| [FromSql, ExecuteSql und ExecuteSqlAsync wurden umbenannt](#fromsql) | High      |
| [Abfragetypen werden mit Entitätstypen zusammengeführt](#qt) | High      |
| [Entity Framework Core ist nicht mehr Bestandteil des gemeinsam verwendeten ASP.NET Core-Frameworks](#no-longer) | Medium      |
| [Kaskadierende DELETE-Anweisungen werden standardmäßig sofort ausgeführt](#cascade) | Medium      |
| [Eager Loading von verwandten Entitäten erfolgt nun in einer einzelnen Abfrage](#eager-loading-single-query) | Medium      |
| [DeleteBehavior.Restrict verfügt über eine übersichtlichere Semantik](#deletebehavior) | Medium      |
| [Die Konfigurations-API für Beziehungen abhängiger (owned) Typen wurde geändert](#config) | Medium      |
| [Für jede Eigenschaft wird separat ein ganzzahliger speicherinterner Schlüssel generiert](#each) | Medium      |
| [Abfragen ohne Nachverfolgung führen keine Identitätsauflösung mehr durch](#notrackingresolution) | Medium      |
| [Metadaten-API-Änderungen](#metadata-api-changes) | Medium      |
| [Anbieterspezifische Metadaten-API-Änderungen](#provider) | Medium      |
| [UseRowNumberForPaging wurde entfernt](#urn) | Medium      |
| [FromSql-Methode kann nicht zusammengesetzt werden, wenn sie mit einer gespeicherten Prozedur verwendet wird](#fromsqlsproc) | Medium      |
| [FromSql-Methoden können nur für die Stammelemente der Abfrage angegeben werden](#fromsql) | Niedrig      |
| [~~Die Abfrageausführung wird auf Debugebene protokolliert~~ – zurückgesetzt](#qe) | Niedrig      |
| [Temporäre Schlüsselwerte werden nicht mehr Entitätsinstanzen zugewiesen](#tkv) | Niedrig      |
| [Abhängige Entitäten, die die Tabelle gemeinsam mit dem Prinzipal verwenden, sind jetzt optional](#de) | Niedrig      |
| [Alle Entitäten, die eine Tabelle mit einer Spalte für das Parallelitätstoken gemeinsam verwenden, müssen diese einer Eigenschaft zuordnen](#aes) | Niedrig      |
| [Benutzereigene Entitäten können nicht abgefragt werden, ohne dass der Besitzer eine Nachverfolgungsabfrage verwendet](#owned-query) | Niedrig      |
| [Geerbte Eigenschaften von nicht zugeordneten Typen sind nun einer einzelnen Spalte für alle abgeleiteten Typen zugeordnet](#ip) | Niedrig      |
| [Die Konvention zur Fremdschlüsseleigenschaft entspricht nicht mehr dem Namen der Prinzipaleigenschaft](#fkp) | Niedrig      |
| [Die Datenbankverbindung wird jetzt getrennt, wenn sie nicht mehr verwendet wird, bevor TransactionScope abgeschlossen wurde](#dbc) | Niedrig      |
| [Unterstützungsfelder werden standardmäßig verwendet](#backing-fields-are-used-by-default) | Niedrig      |
| [Eine Ausnahme wird ausgelöst, wenn mehrere kompatible Unterstützungsfelder gefunden werden](#throw-if-multiple-compatible-backing-fields-are-found) | Niedrig      |
| [„Nur-Feld“-Eigenschaftennamen sollten dem Feldnamen entsprechen](#field-only-property-names-should-match-the-field-name) | Niedrig      |
| [AddLogging und AddMemoryCache werden nicht mehr von AddDbContext/AddDbContextPool aufgerufen](#adddbc) | Niedrig      |
| [AddEntityFramework* fügt IMemoryCache mit einer Größenbeschränkung hinzu](#addentityframework-adds-imemorycache-with-a-size-limit) | Niedrig      |
| [DbContext.Entry erkennt nun mit DetectChanges lokale Änderungen](#dbe) | Niedrig      |
| [Schlüssel aus Zeichenfolgen und Bytearrays werden standardmäßig nicht vom Client generiert](#string-and-byte-array-keys-are-not-client-generated-by-default) | Niedrig      |
| [ILoggerFactory ist nun ein bereichsbezogener Dienst](#ilf) | Niedrig      |
| [Für Proxys mit Lazy Loading (trägem Laden) gilt nicht mehr die Annahme, dass Navigationseigenschaften vollständig geladen sind](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | Niedrig      |
| [Die Erstellung zu vieler interner Dienstanbieter führt standardmäßig zu einem Fehler](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | Niedrig      |
| [Neues Verhalten für HasOne/HasMany bei Aufruf mit einer einzelnen Zeichenfolge](#nbh) | Niedrig      |
| [Der Rückgabetyp für mehrere asynchrone Methoden wurde von Task in ValueTask geändert](#rtnt) | Niedrig      |
| [Die Anmerkung Relational:TypeMapping heißt nun nur noch TypeMapping](#rtt) | Niedrig      |
| [ToTable löst für einen abgeleiteten Typ eine Ausnahme aus](#totable-on-a-derived-type-throws-an-exception) | Niedrig      |
| [EF Core sendet keine PRAGMA-Anweisungen mehr, um Fremdschlüssel in SQLite zu erzwingen](#pragma) | Niedrig      |
| [SQLitePCLRaw.bundle_e_sqlite3 ist nun eine Abhängigkeit von Microsoft.EntityFrameworkCore.Sqlite](#sqlite3) | Niedrig      |
| [GUID-Werte werden jetzt als TEXT in SQLite gespeichert](#guid) | Niedrig      |
| [Char-Werte werden jetzt als TEXT in SQLite gespeichert](#char) | Niedrig      |
| [Migrations-IDs werden jetzt mit dem Kalender der invarianten Kultur generiert](#migid) | Niedrig      |
| [Erweiterungsinformationen und -metadaten wurden aus IDbContextOptionsExtension entfernt](#xinfo) | Niedrig      |
| [LogQueryPossibleExceptionWithAggregateOperator wurde umbenannt](#lqpe) | Niedrig      |
| [Übersichtlichere API für Namen von Fremdschlüsselconstraints](#clarify) | Niedrig      |
| [IRelationalDatabaseCreator.HasTables/HasTablesAsync ist jetzt öffentlich](#irdc2) | Niedrig      |
| [Microsoft.EntityFrameworkCore.Design ist jetzt ein DevelopmentDependency-Paket](#dip) | Niedrig      |
| [SQLitePCL.raw wurde auf Version 2.0.0 aktualisiert](#SQLitePCL) | Niedrig      |
| [NetTopologySuite wurde auf Version 2.0.0 aktualisiert](#NetTopologySuite) | Niedrig      |
| [Microsoft.Data.SqlClient wird statt System.Data.SqlClient verwendet](#SqlClient) | Niedrig      |
| [Beziehungen mit mehreren mehrdeutigen Selbstverweisen müssen nun konfiguriert werden](#mersa) | Niedrig      |
| [DbFunction.Schema ist NULL oder eine leere Zeichenfolge und ist deshalb so konfiguriert, dass es im Standardschema des Modells ist](#udf-empty-string) | Niedrig      |

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a>LINQ-Abfragen werden nicht mehr auf dem Client ausgewertet

[Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Siehe auch Issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

**Altes Verhalten**

Vor Version 3.0 wurden in EF Core automatisch Ausdrücke auf dem Client ausgewertet, wenn diese Teil einer Abfrage waren und nicht in SQL oder in einen Parameter konvertiert werden konnten.
Wenn potenziell aufwendige Ausdrücke auf dem Client ausgewertet werden sollten, wurde standardmäßig nur eine Warnung ausgelöst.

**Neues Verhalten**

Ab Version 3.0 können in EF Core nur Ausdrücke der obersten Projektion (dem letzten `Select()`-Aufruf in der Abfrage) auf dem Client ausgewertet werden.
Wenn Ausdrücke in einem anderen Teil der Abfrage nicht in SQL oder in einen Parameter konvertiert werden können, wird eine Ausnahme ausgelöst.

**Hintergründe**

Die automatische Auswertung von Abfragen auf Clients hat zur Folge, dass viele Abfragen auch dann ausgeführt werden, wenn wichtige Teile nicht übersetzt werden können.
Dieses Verhalten kann zu unerwartetem und potenziell schädlichem Verhalten führen, das möglicherweise erst in der Produktionsphase erkannt wird.
Eine nicht übersetzbare Bedingung in einem `Where()`-Aufruf kann sich beispielsweise so auswirken, dass alle Zeilen einer Tabelle vom Datenbankserver übertragen werden und der Filter auf dem Client angewendet wird.
Wenn die Tabelle in der Entwicklungsphase nur wenige Zeilen enthält, ist es gut möglich, dass diese Situation unerkannt bleibt. Wenn die Anwendung jedoch in die Produktionsumgebung überführt wird, enthält die Tabelle möglicherweise Millionen von Zeilen, was zu schwerwiegenden Konsequenzen führen kann.
Wie die Erfahrung in der Entwicklungsphase gezeigt hat, können auch Warnungen, die bei Auswertungen auf dem Client ausgelöst werden, dieses Problem nicht lösen, da sie sich leicht ignorieren lassen.

Die automatische Auswertung auf Clients kann darüber hinaus zu Problemen führen, bei denen die Verbesserung der Abfrageübersetzung für bestimmte Ausdrücke zu unbeabsichtigten Breaking Changes zwischen Releases führt.

**Gegenmaßnahmen**

Wenn sich eine Abfrage nicht vollständig übersetzen lässt, habe Sie zwei Möglichkeiten: Schreiben Sie sie entweder um, oder setzen Sie alternativ `AsEnumerable()`, `ToList()` oder ähnliche Methoden ein, um Daten wieder zurück an den Client zu übertragen, wo sie mit LINQ to Objects weiterverarbeitet werden können.

<a name="netstandard21"></a>
### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20"></a>EF Core 3.0 zielt auf .NET Standard 2.1 und nicht auf .NET Standard 2.0 ab

[Issue #15498](https://github.com/aspnet/EntityFrameworkCore/issues/15498)

> [!IMPORTANT] 
> .NET Standard 2.0 kann wieder für EF Core 3.1 verwendet werden. Dadurch wird das .NET Framework wieder unterstützt.

**Altes Verhalten**

Vor 3.0 zielte EF Core auf .NET Standard 2.0 ab und wurde auf allen Plattformen ausgeführt, die diesen Standard unterstützen, einschließlich .NET Framework.

**Neues Verhalten**

Ab 3.0 zielt EF Core auf .NET Standard 2.1 ab und wird auf allen Plattformen ausgeführt, die diesen Standard unterstützen. Dies schließt .NET Framework nicht ein.

**Hintergründe**

Dies ist ein Teil der strategischen Entscheidung bezüglich .NET-Technologien, um den Fokus auf .NET Core und andere moderne .NET-Plattformen (z. B. Xamarin) zu legen.

**Gegenmaßnahmen**

Verwenden von EF Core 3.1

<a name="no-longer"></a>
### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a>Entity Framework Core ist nicht mehr Teil des gemeinsam verwendeten ASP.NET Core-Frameworks

[Issueankündigungen #325](https://github.com/aspnet/Announcements/issues/325)

**Altes Verhalten**

Vor Version 3.0 von ASP.NET Core wurden EF Core und einige der zugehörigen Datenanbieter wie SQL Server eingeschlossen, wenn `Microsoft.AspNetCore.App` oder `Microsoft.AspNetCore.All` ein Paketverweis hinzugefügt wurde.

**Neues Verhalten**

Ab Version 3.0 enthält das gemeinsam verwendete ASP.NET Core-Framework weder EF Core noch zugehörige Datenanbieter.

**Hintergründe**

Vor dieser Änderung konnte EF Core auf unterschiedliche Arten bezogen werden. Diese richteten sich danach, ob ASP.NET Core und SQL Server oder ein anderes Zielframework für die Anwendung vorgesehen war. Bei einem Upgrade von ASP.NET Core wurde zudem ein Upgrade von EF Core und des SQL Server-Anbieters erzwungen, was nicht in allen Fällen gewünscht war.

Durch die eingeführte Änderung wird EF Core unter allen Anbietern, unterstützten .NET-Implementierungen und Anwendungstypen auf dieselbe Weise bezogen.
Entwickler können nun außerdem genau festlegen, wann für EF Core und zugehörige Datenanbieter ein Upgrade durchgeführt werden soll.

**Gegenmaßnahmen**

Wenn Sie EF Core in einer Anwendung unter ASP.NET Core 3.0 oder in einer anderen unterstützten Anwendung verwenden möchten, müssen Sie dem EF Core-Datenbankanbieter, der für die Anwendung genutzt werden soll, explizit einen Paketverweis hinzufügen.

<a name="dotnet-ef"></a>
### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a>Das EF Core-Befehlszeilentool (dotnet ef) ist nicht länger Bestandteil des .NET Core SDK.

[Issue #14016](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

**Altes Verhalten**

Vor Version 3.0 war das `dotnet ef`-Tool im .NET Core SDK enthalten und konnte von der Befehlszeile aus ohne zusätzliche Schritte in einem beliebigen Projekt verwendet werden. 

**Neues Verhalten**

Ab Version 3.0 ist das `dotnet ef`-Tool nicht mehr im .NET SDK enthalten und muss deshalb vor der Verwendung explizit als lokales oder globales Tool installiert werden. 

**Hintergründe**

Durch diese Änderung kann `dotnet ef` als reguläres .NET-CLI-Tool für NuGet verteilt und aktualisiert werden und entspricht damit dem üblichen Verfahren, dass EF Core 3.0 ebenfalls immer als NuGet-Paket verteilt wird.

**Gegenmaßnahmen**

Um Migrationen zu verwalten oder ein Gerüst für `DbContext` zu erstellen, installieren Sie `dotnet-ef` als globales Tool:

  ``` console
    $ dotnet tool install --global dotnet-ef
  ```

Eine Verwendung als lokales Tool ist ebenfalls möglich, wenn Sie die Abhängigkeiten eines Projekts wiederherstellen, das das Tool mithilfe einer [Toolmanifestdatei](https://github.com/dotnet/cli/issues/10288) als Toolabhängigkeit deklariert.

<a name="fromsql"></a>
### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a>FromSql, ExecuteSql und ExecuteSqlAsync wurden umbenannt

[Issue #10996](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

**Altes Verhalten**

Vor Version 3.0 wurden in EF Core diese Methodennamen überladen, um entweder mit einer normalen Zeichenfolge oder einer Zeichenfolge, die in SQL und Parameter interpoliert werden sollte, zu arbeiten.

**Neues Verhalten**

Ab Version 3.0 verwenden Sie in EF Core `FromSqlRaw`, `ExecuteSqlRaw` und `ExecuteSqlRawAsync`, um eine parametrisierte Abfrage zu erstellen, bei der die Parameter einzeln aus der Abfragezeichenfolge übergeben werden.
Beispiel:

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

Verwenden Sie `FromSqlInterpolated`, `ExecuteSqlInterpolated` und `ExecuteSqlInterpolatedAsync`, um eine parametrisierte Abfrage zu erstellen, bei der die Parameter als Teil einer interpolierten Abfragezeichenfolge übergeben werden.
Beispiel:

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

Beachten Sie, dass die beiden oben genannten Abfragen dieselbe parametrisierte SQL mit denselben SQL-Parametern erzeugen.

**Hintergründe**

Bei Methodenüberladungen wie dieser wird sehr leicht versehentlich die Methode für unformatierte Zeichenfolgen aufgerufen, wenn eigentlich beabsichtigt war, die Methode für interpolierte Zeichenfolgen aufzurufen (und umgekehrt).
Dadurch werden Abfragen möglicherweise nicht parametrisiert, obwohl dies der Fall sein sollte.

**Gegenmaßnahmen**

Verwenden Sie ab sofort die neuen Methodennamen.

<a name="fromsqlsproc"></a>
### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a>FromSql-Methode kann nicht zusammengesetzt werden, wenn sie mit einer gespeicherten Prozedur verwendet wird

[Issue #15392](https://github.com/aspnet/EntityFrameworkCore/issues/15392)

**Altes Verhalten**

Vor EF Core 3.0 versuchte die FromSql-Methode, zu ermitteln, ob das übergebene SQL zusammengesetzt werden konnte. Eine Clientauswertung wurde durchgeführt, wenn das SQL wie eine gespeicherte Prozedur nicht zusammensetzbar war. Die folgende Abfrage funktionierte, indem die gespeicherte Prozedur auf dem Server und die FirstOrDefault-Methode auf dem Client ausgeführt wurde.

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

**Neues Verhalten**

Ab EF Core 3.0 versucht EF Core nicht mehr, das SQL zu analysieren. Wenn Sie also nach FromSqlRaw/FromSqlInterpolated zusammensetzen, setzt EF Core das SQL zusammen, indem eine Unterabfrage ausgelöst wird. Wenn Sie eine gespeicherte Prozedur mit Zusammensetzung verwenden, wird eine Ausnahme für ungültige SQL-Syntax ausgelöst.

**Hintergründe**

EF Core 3.0 unterstützt die automatische Clientauswertung nicht, da diese wie [hier](#linq-queries-are-no-longer-evaluated-on-the-client) erläutert fehleranfällig war.

**Abhilfe**

Wenn Sie eine gespeicherte Prozedur in FromSqlRaw/FromSqlInterpolated verwenden, wissen Sie, dass diese nicht zusammengesetzt werden kann. Daher können Sie __AsEnumerable/AsAsyncEnumerable__ direkt nach dem FromSql-Methodenaufruf hinzufügen, um die Zusammensetzung auf dem Server zu vermeiden.

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a>FromSql-Methoden können nur für die Stammelemente der Abfrage angegeben werden.

[Issue #15704](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

**Altes Verhalten**

Vor EF Core 3.0 konnte die `FromSql`-Methode an einer beliebigen Stelle in der Abfrage angegeben werden.

**Neues Verhalten**

Ab EF Core 3.0 können die neuen Methoden `FromSqlRaw` und `FromSqlInterpolated` (durch die `FromSql` ersetzt wird) nur für Stammelemente von Abfragen angegeben werden, d.h. direkt für das `DbSet<>`. Der Versuch, sie an anderer Stelle anzugeben, führt zu einem Kompilierungsfehler.

**Hintergründe**

Die Angabe von `FromSql` an einer anderen Stelle als für ein `DbSet` erbrachte keine zusätzliche Bedeutung und keinen Mehrwert, sondern konnte in bestimmten Szenarien zu Mehrdeutigkeiten führen.

**Gegenmaßnahmen**

`FromSql` Aufrufe sollten so verschoben, dass sie direkt für das zugehörige `DbSet` gelten.

<a name="notrackingresolution"></a>
### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a>Abfragen ohne Nachverfolgung führen keine Identitätsauflösung mehr durch

[Issue #13518](https://github.com/aspnet/EntityFrameworkCore/issues/13518)

**Altes Verhalten**

Vor EF Core 3.0 wurde dieselbe Entitätsinstanz für jedes Vorkommen einer Entität mit einem bestimmten Typ und einer bestimmten ID verwendet. Dies entspricht dem Verhalten von Abfragen zu Nachverfolgungen. Die folgende Abfrage:

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```
gibt dieselbe `Category`-Instanz für jedes `Product` zurück, das der bestimmten Kategorie zugeordnet ist.

**Neues Verhalten**

Ab EF Core 3.0 werden unterschiedliche Entitätsinstanzen erstellt, wenn eine Entität mit einem bestimmten Typ und einer bestimmten ID an verschiedenen Stellen im zurückgegebenen Diagramm gefunden wird. Die Abfrage oben gibt beispielsweise nun eine neue `Category`-Instanz für jedes `Product` zurück, auch wenn zwei Produkte derselben Kategorie zugeordnet sind.

**Hintergründe**

Die Identitätsauflösung (d. h. Feststellen, dass eine Entität über denselben Typ und die dieselbe ID wie die zuvor aufgetretene Entität verfügt) fügt zusätzlichen Aufwand für Leistung und Arbeitsspeicher hinzu. Dies widerspricht normalerweise dem Grund, warum in erster Linie Abfragen ohne Nachverfolgung verwendet werden. Obwohl die Identitätsauflösung manchmal nützlich sein kann, wird sie nicht benötigt, wenn die Entitäten serialisiert und an den Client gesendet werden, was manchmal für Abfragen ohne Nachverfolgung der Fall ist.

**Gegenmaßnahmen**

Verwenden Sie eine Nachverfolgungsabfrage, falls die Auflösung erforderlich ist.

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a>~~Die Abfrageausführung wird auf Debugebene protokolliert~~ – zurückgesetzt

[Issue #14523](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

Wir haben diese Änderung zurückgesetzt, weil die neue Konfiguration in EF Core 3.0 die Angabe des Protokolliergrads für jedes Ereignis durch die Anwendung ermöglicht. Um z.B. die Protokollierung von SQL zu `Debug` zu ändern, konfigurieren Sie den Grad explizit in `OnConfiguring` oder `AddDbContext`:
```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a>Temporäre Schlüsselwerte werden nicht mehr Entitätsinstanzen zugewiesen

[Issue #12378](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

**Altes Verhalten**

Vor Version 3.0 wurden in EF Core allen Schlüsseleigenschaften temporäre Werte zugewiesen. Später wurden den Eigenschaften dann die tatsächlichen Werte zugewiesen, die von der Datenbank generiert wurden.
Die temporären Werte waren in der Regel große negative Zahlen.

**Neues Verhalten**

Ab Version 3.0 wird in EF Core der temporäre Wert als Teil der Überwachungsinformationen der Entität gespeichert. Die Schlüsseleigenschaft selbst bleibt unverändert.

**Hintergründe**

Diese Änderung wurde vorgenommen, damit temporäre Schlüsselwerte nicht fälschlicherweise dauerhaft gespeichert werden, wenn eine Entität, die vorher von einer `DbContext`-Instanz überwacht wurde, in eine andere `DbContext`-Instanz verschoben wird. 

**Gegenmaßnahmen**

Anwendungen, die Primärschlüsselwerte Fremdschlüsseln zuweisen, um Zuordnungen zwischen Entitäten zu erstellen, können vom alten Verhalten abhängig sein, wenn Primärschlüssel vom Speicher generiert werden und zu Entitäten im `Added`-Zustand gehören.
Sie können dies wie folgt vermeiden:
* Verwenden Sie keine vom Speicher generierten Schlüssel.
* Legen Sie zum Erstellen von Zuordnungen keine Fremdschlüsselwerte, sondern Navigationseigenschaften fest.
* Rufen Sie die tatsächlichen temporären Schlüsselwerte aus den Überwachungsinformationen der Entität ab.
`context.Entry(blog).Property(e => e.Id).CurrentValue` gibt beispielsweise den temporären Wert zurück, obwohl `blog.Id` noch nicht festgelegt wurde.

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a>DetectChanges berücksichtigt vom Speicher generierte Schlüsselwerte

[Issue #14616](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

**Altes Verhalten**

Vor Version 3.0 wurde in EF Core eine nicht überwachte Entität, die von `DetectChanges` gefunden wurde, im `Added`-Zustand überwacht und als neue Zeile eingefügt, sobald `SaveChanges` aufgerufen wurde.

**Neues Verhalten**

Ab Version 3.0 wird in EF Core die Entität im `Modified`-Zustand überwacht, wenn für diese generierte Schlüsselwerte verwendet werden und ein Schlüsselwert festgelegt wird.
Es wird also davon ausgegangen, dass eine Zeile für die Entität vorhanden ist. Wenn `SaveChanges` aufgerufen wird, wird die Zeile außerdem aktualisiert.
Falls kein Schlüsselwert festgelegt ist oder der Entitätstyp keine generierten Schlüssel verwendet, wird die neue Entität wie in früheren Version weiterhin im `Added`-Zustand überwacht.

**Hintergründe**

Diese Änderung wurde vorgenommen, um bei der Verwendung von speichergenerierten Schlüsseln die Arbeit mit nicht verbundenen Entitätsgraphen einfacher und konsistenter zu gestalten.

**Gegenmaßnahmen**

Diese Änderung kann dazu führen, dass eine Anwendung nicht mehr funktioniert. Dieser Fall tritt ein, wenn für Entitätstypen generierte Schlüssel vorgesehen sind, dann jedoch explizit Schlüsselwerte für neue Instanzen festgelegt werden.
Sie können dieses Problem vermeiden, indem Sie explizit festlegen, dass für Schlüsseleigenschaften keine generierten Werte verwendet werden sollen.
Dies ist beispielsweise mit der Fluent-API möglich:

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

Alternativ bieten sich auch Datenanmerkungen an:

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```
<a name="cascade"></a>
### <a name="cascade-deletions-now-happen-immediately-by-default"></a>Kaskadierende Deletes werden standardmäßig sofort ausgeführt

[Issue #10114](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

**Altes Verhalten**

Vor Version 3.0 wurden in EF Core kaskadierenden Aktionen (Löschen von abhängigen Entitäten nach dem Löschen eines erforderlichen Prinzipals oder nach dem Aufheben einer Beziehung zum erforderlichen Prinzipal) erst ausgeführt, wenn SaveChanges aufgerufen wurde.

**Neues Verhalten**

Ab Version 3.0 werden in EF Core kaskadierende Aktionen angewendet, sobald die auslösende Bedingung erkannt wird.
Wenn beispielsweise `context.Remove()` zum Löschen einer Prinzipalentität aufgerufen wird, führt das dazu, dass auch alle zugehörigen erforderlichen Entitäten, die überwacht werden, sofort auf `Deleted` festgelegt werden.

**Hintergründe**

Diese Änderung wurde vorgenommen, um die Arbeit mit Datenbindungen und Überwachungsszenarios zu vereinfachen, in denen bekannt sein muss, welche Entitäten _vor_ dem Aufruf von `SaveChanges` gelöscht werden.

**Gegenmaßnahmen**

Sie können das vorherige Verhalten wiederherstellen, indem Sie für `context.ChangeTracker` die entsprechenden Einstellungen vornehmen.
Beispiel:

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```
<a name="eager-loading-single-query"></a>
### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a>Eager Loading von verwandten Entitäten erfolgt nun in einer einzelnen Abfrage

[Issue #18022](https://github.com/aspnet/EntityFrameworkCore/issues/18022)

**Altes Verhalten**

Vor Version 3.0 löste Eager Loading von Sammlungsnavigationen über `Include`-Operatoren die Generierung mehrerer Abfragen auf der relationalen Datenbank aus. Eine Abfrage für jeden verwandten Entitätstyp.

**Neues Verhalten**

Ab Version 3.0 generiert EF Core eine einzelne Abfrage mit JOINs für relationale Datenbanken.

**Hintergründe**

Das Ausgeben mehrerer Abfragen zum Implementieren einer einzelnen LINQ-Abfrage führte zu einer Vielzahl von Problemen, darunter Leistungsbeeinträchtigungen, da mehrere Datenbankroundtrips erforderlich waren, und Datenkohärenzprobleme, da jede Abfrage einen anderen Zustand der Datenbank beobachten könnte.

**Gegenmaßnahmen**

Zwar ist dies technisch gesehen kein Breaking Change, jedoch könnte es erhebliche Auswirkungen auf die Leistung der Anwendung haben, wenn eine einzelne Abfrage eine große Anzahl von `Include`-Operatoren für Sammlungsnavigationen enthält. Weitere Informationen sowie Informationen zum Umschreiben von Abfragen auf eine effizientere Weise finden Sie [in diesem Kommentar](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085).

**

<a name="deletebehavior"></a>
### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a>DeleteBehavior.Restrict verfügt über eine übersichtlichere Semantik

[Issue #12661](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

**Altes Verhalten**

Vor Version 3.0 wurden mit `DeleteBehavior.Restrict` Fremdschlüssel in der Datenbank mit `Restrict`-Semantik erstellt, es wurden aber auch unvorhergesehen interne Fixups geändert.

**Neues Verhalten**

Ab Version 3.0 wird mit `DeleteBehavior.Restrict` sichergestellt, dass Fremdschlüssel mit `Restrict`-Semantik erstellt werden – d. h. ohne Überlappungen; ausgenommen bei Einschränkungsverletzungen – und ohne Auswirkungen auf EF-interne Fixups.

**Hintergründe**

Diese Änderung wurde vorgenommen, um die Benutzerfreundlichkeit bei intuitiver Verwendung von `DeleteBehavior` ohne unerwartete Nebeneffekte zu verbessern.

**Gegenmaßnahmen**

Sie können das vorherige Verhalten wiederherstellen, indem Sie `DeleteBehavior.ClientNoAction` verwenden.

<a name="qt"></a>
### <a name="query-types-are-consolidated-with-entity-types"></a>Abfragetypen werden mit Entitätstypen zusammengeführt

[Issue #14194](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

**Altes Verhalten**

Vor Version 3.0 wurden in EF Core [Abfragetypen](xref:core/modeling/keyless-entity-types) dazu verwendet, Daten abzufragen, in denen kein Primärschlüssel auf strukturierte Weise festgelegt war.
Abfragetypen wurden also für die Zuordnung von Entitätstypen ohne Schlüssel verwendet (in der Regel in einer Sicht, in einigen Fällen aber auch in einer Tabelle), während reguläre Entitätstypen für einen verfügbaren Schlüssel verwendet wurden (in der Regel in einer Tabelle, in einigen Fällen aber auch in einer Sicht).

**Neues Verhalten**

Aus einem Abfragetyp wird nun einfach ein Entitätstyp ohne Primärschlüssel.
Entitätstypen ohne Schlüssel besitzen die gleiche Funktionalität wie Abfragetypen in früheren Versionen.

**Hintergründe**

Diese Änderung wurde vorgenommen, um Unklarheiten bei der Verwendung von Abfragetypen zu beseitigen.
Abfragetypen sind schlüssellose Entitätstypen, die grundsätzlich schreibgeschützt sind. Sie sollten allerdings nicht allein wegen des Schreibschutzes verwendet werden.
Des Weiteren werden sie oft Sichten zugeordnet, was aber nur daran liegt, dass für Letztere häufig keine Schlüssel definiert werden.

**Gegenmaßnahmen**

Die folgenden Teile der API sind durch die Änderungen veraltet:
* **`ModelBuilder.Query<>()`** : Rufen Sie stattdessen `ModelBuilder.Entity<>().HasNoKey()` auf, um einen schlüssellosen Entitätstyp festzulegen.
Dieses Verhalten wird nach wie vor nicht konventionsgemäß festgelegt, um Fehlkonfigurationen zu vermeiden, wenn ein Primärschlüssel erwartet wird, jedoch nicht mit der Konvention übereinstimmt.
* **`DbQuery<>`** : Verwenden Sie stattdessen `DbSet<>`.
* **`DbContext.Query<>()`** : Verwenden Sie stattdessen `DbContext.Set<>()`.

<a name="config"></a>
### <a name="configuration-api-for-owned-type-relationships-has-changed"></a>Die Konfigurations-API für Beziehungen abhängiger (owned) Typen wurde geändert

[Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)

**Altes Verhalten**

Vor Version 3.0 wurde in EF Core die abhängige Beziehung direkt nach dem Aufruf von `OwnsOne` oder `OwnsMany` konfiguriert. 

**Neues Verhalten**

Ab Version 3.0 von EF Core ist eine Fluent-API verfügbar, mit der über `WithOwner()` eine Navigationseigenschaft für den Besitzer konfiguriert werden kann.
Beispiel:

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

Die Konfiguration für die Beziehung zwischen Besitzertyp und abhängigem Typ sollte nun ähnlich wie bei der Konfiguration anderer Beziehungen nach `WithOwner()` verkettet werden.
Die Konfiguration für den abhängigen Typ wird jedoch weiterhin nach `OwnsOne()/OwnsMany()` verkettet.
Beispiel:

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details, eb =>
    {
        eb.WithOwner()
            .HasForeignKey(e => e.AlternateId)
            .HasConstraintName("FK_OrderDetails");
            
        eb.ToTable("OrderDetails");
        eb.HasKey(e => e.AlternateId);
        eb.HasIndex(e => e.Id);

        eb.HasOne(e => e.Customer).WithOne();

        eb.HasData(
            new OrderDetails
            {
                AlternateId = 1,
                Id = -1
            });
    });
```

Wenn Sie zusätzlich `Entity()`, `HasOne()`, oder `Set()` für das Ziel eines abhängigen Typs aufrufen, wird keine Ausnahme ausgelöst.

**Hintergründe**

Diese Änderung wurde vorgenommen, um eine deutlichere Trennung zwischen der Konfiguration des abhängigen Typs und der _Beziehung zum abhängigen Typ_ zu ermöglichen.
Dadurch werden für Methoden wie `HasForeignKey` Mehrdeutigkeiten und Unklarheiten beseitigt.

**Gegenmaßnahmen**

Passen Sie für die Beziehungen von abhängigen Typen die Konfiguration so an, dass die neue Fluent-API wie im obigen Beispiel verwendet wird.

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>Abhängige Entitäten, die die Tabelle gemeinsam mit dem Prinzipal verwenden, sind jetzt optional

[Issue #9005](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

**Altes Verhalten**

Sehen Sie sich das folgende Modell an:
```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```
Wenn `OrderDetails` im Besitz von `Order` ist oder explizit derselben Tabelle zugeordnet ist, war vor Version 3.0 in EF Core immer eine `OrderDetails`-Instanz beim Hinzufügen eines neuen `Order`-Objekts erforderlich.


**Neues Verhalten**

Ab Version 3.0 bietet EF Core die Möglichkeit, `Order` ohne `OrderDetails` hinzuzufügen, und alle `OrderDetails`-Eigenschaften, mit Ausnahme des primären Schlüssels, werden Spalten zugeordnet, die NULL-Werte zulassen.
Bei Abfragen von EF Core wird `OrderDetails` auf `null` festgelegt, wenn eine der erforderlichen Eigenschaften keinen Wert aufweist oder keine erforderlichen Eigenschaften außer dem primären Schlüssel vorhanden und alle Eigenschaften `null` sind.

**Gegenmaßnahmen**

Wenn Ihr Modell von der gemeinsamen Nutzung einer Tabelle mit allen optionalen Spalten abhängig ist, aber die Navigation, die darauf zeigt, wahrscheinlich nicht `null` ist, sollte die Anwendung für die Handhabung von Fällen geändert werden, in denen die Navigation `null` ist. Wenn das nicht möglich ist, sollte dem Entitätstyp eine erforderliche Eigenschaft hinzugefügt werden oder mindestens einer Eigenschaft sollte ein Wert ungleich `null` zugewiesen sein.

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a>Alle Entitäten, die eine Tabelle mit einer Spalte für das Parallelitätstoken gemeinsam verwenden, müssen diese einer Eigenschaft zuordnen

[Issue #14154](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

**Altes Verhalten**

Sehen Sie sich das folgende Modell an:
```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public byte[] Version { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .Property(o => o.Version).IsRowVersion().HasColumnName("Version");
}
```
Wenn `OrderDetails` im Besitz von `Order` ist oder explizit derselben Tabelle zugeordnet ist, wird vor Version 3.0 in EF Core durch das alleinige Aktualisieren von `OrderDetails` der `Version`-Wert auf dem Client nicht aktualisiert, und das nächste Update schlägt fehl.


**Neues Verhalten**

Ab Version 3.0 gibt EF Core den neuen `Version`-Wert an `Order` weiter, wenn dies Besitzer von `OrderDetails` ist. Andernfalls wird eine Ausnahme während der Modellvalidierung ausgelöst.

**Hintergründe**

Diese Änderung wurde vorgenommen, um einen veralteten Wert für ein Parallelitätstoken zu vermeiden, wenn nur eine der Entitäten, die derselben Tabelle zugeordnet sind, aktualisiert wird.

**Gegenmaßnahmen**

Alle Entitäten, die die Tabelle gemeinsam nutzen, müssen eine Eigenschaft enthalten, die der Spalte für das Parallelitätstoken zugeordnet ist. Es ist möglich, eine solche im Schattenzustand zu erstellen:
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a>Benutzereigene Entitäten können nicht abgefragt werden, ohne dass der Besitzer eine Nachverfolgungsabfrage verwendet

[Issue 18876](https://github.com/aspnet/EntityFrameworkCore/issues/18876)

**Altes Verhalten**

Vor EF Core 3.0 konnten die benutzereigenen Entitäten wie jedes andere Navigationselement abgefragt werden.

```csharp
context.People.Select(p => p.Address);
```

**Neues Verhalten**

Ab EF Core 3.0 wird eine Ausnahme ausgelöst, wenn eine Nachverfolgungsabfrage eine benutzereigene Entität ohne den Besitzer projiziert.

**Hintergründe**

Benutzereigene Entitäten können ohne den Besitzer nicht bearbeitet werden, daher handelt es sich in den meisten Fällen um einen Fehler, wenn sie auf diese Weise abgefragt werden.

**Gegenmaßnahmen**

Wenn die benutzereigene Entität nachverfolgt werden soll, damit sie später Änderungen an ihr vorgenommen werden können, sollte der Besitzer in der Abfrage enthalten sein.

Fügen Sie andernfalls einen `AsNoTracking()`-Aufruf hinzu:

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a>Geerbte Eigenschaften von nicht zugeordneten Typen sind nun einer einzelnen Spalte für alle abgeleiteten Typen zugeordnet

[Issue #13998](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

**Altes Verhalten**

Sehen Sie sich das folgende Modell an:
```csharp
public abstract class EntityBase
{
    public int Id { get; set; }
}

public abstract class OrderBase : EntityBase
{
    public int ShippingAddress { get; set; }
}

public class BulkOrder : OrderBase
{
}

public class Order : OrderBase
{
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>();
    modelBuilder.Entity<Order>();
}
```

Vor Version 3.0 wurde in EF Core die Eigenschaft `ShippingAddress` standardmäßig separaten Spalten für `BulkOrder` und `Order` zugeordnet.

**Neues Verhalten**

Ab Version 3.0 erstellt EF Core nur eine Spalte für `ShippingAddress`.

**Hintergründe**

Der alte Verhalten war unerwartet.

**Gegenmaßnahmen**

Die Eigenschaft kann noch immer explizit einer separaten Spalte für die abgeleiteten Typen zugeordnet werden:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>()
        .Property(o => o.ShippingAddress).HasColumnName("BulkShippingAddress");
    modelBuilder.Entity<Order>()
        .Property(o => o.ShippingAddress).HasColumnName("ShippingAddress");
}
```

<a name="fkp"></a>

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a>Die Konvention zur Fremdschlüsseleigenschaft entspricht nicht mehr dem Namen der Prinzipaleigenschaft

[Issue #13274](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

**Altes Verhalten**

Sehen Sie sich das folgende Modell an:
```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```
Vor Version 3.0 wurde in EF Core gemäß der Konvention die `CustomerId`-Eigenschaft für den Fremdschlüssel verwendet.
Wenn `Order` jedoch ein abhängiger Typ ist, wäre `CustomerId` der Primärschlüssel, was normalerweise nicht der Erwartungshaltung entspricht.

**Neues Verhalten**

Ab Version 3.0 werden in EF Core konventionsgemäß keine Eigenschaften mehr für Fremdschlüssel verwendet, wenn diese denselben Namen wie die Prinzipaleigenschaft besitzen.
Die Muster für den Namen des Prinzipaltyps, der mit dem Namen der Prinzipaleigenschaft verkettet wird, und für den Navigationsnamen, der mit dem Namen der Prinzipaleigenschaft verkettet wird, werden jedoch weiterhin abgeglichen.
Beispiel:

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int BuyerId { get; set; }
    public Customer Buyer { get; set; }
}
```

**Hintergründe**

Diese Änderung wurde vorgenommen, um zu vermeiden, dass versehentlich eine Primärschlüsseleigenschaft für den abhängigen Typ definiert wird.

**Gegenmaßnahmen**

Wenn die Eigenschaft als Fremdschlüssel und daher Teil des Primärschlüssels vorgesehen war, müssen Sie diese explizit als solche festlegen.

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a>Die Datenbankverbindung wird jetzt geschlossen, wenn sie nicht mehr verwendet wird, bevor TransactionScope abgeschlossen wurde

[Issue #14218](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

**Altes Verhalten**

Wenn vor Version 3.0 in EF Core der Kontext die Verbindung in einem `TransactionScope` öffnet, bleibt die Verbindung geöffnet, während der aktuelle `TransactionScope` aktiv ist.

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        // Old behavior: Connection is still open at this point

        var categories = context.ProductCategories().ToList();
    }
}
```

**Neues Verhalten**

Ab Version 3.0 schließt EF Core die Verbindung, sobald sie nicht mehr verwendet wird.

**Hintergründe**

Diese Änderung ermöglicht die Verwendung mehrerer Kontexte in demselben `TransactionScope`. Das neue Verhalten entspricht außerdem EF6.

**Gegenmaßnahmen**

Wenn die Verbindung geöffnet bleiben muss, wird durch einen expliziten Aufruf von `OpenConnection()` sichergestellt, dass EF Core diese nicht vorzeitig schließt:

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.Database.OpenConnection();
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        var categories = context.ProductCategories().ToList();
        context.Database.CloseConnection();
    }
}
```

<a name="each"></a>

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a>Für jede Eigenschaft wird separat ein ganzzahliger speicherinterner Schlüssel generiert

[Issue #6872](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

**Altes Verhalten**

Vor Version 3.0 wurde in EF Core ein gemeinsam verwendeter Wertegenerator für alle speicherinternen ganzzahligen Schlüsseleigenschaften verwendet.

**Neues Verhalten**

Ab Version 3.0 von EF Core wird für jede ganzzahlige Schlüsseleigenschaft ein eigener Wertegenerator bei der Verwendung der In-Memory Database verwendet.
Wenn die Datenbank gelöscht wird, wird die Schlüsselgenerierung für alle Tabellen zurückgesetzt.

**Hintergründe**

Diese Änderung wurde vorgenommen, um die speicherinterne Schlüsselgenerierung enger mit der Schlüsselgenerierung für echte Datenbanken abzustimmen. Außerdem sollen bei Verwendung der In-Memory Database Tests leichter voneinander isoliert werden können.

**Gegenmaßnahmen**

Wenn Anwendungen von bestimmten speicherinternen Schlüsselwerten abhängig sind, funktionieren Erstere durch die eingeführte Änderung möglicherweise nicht mehr.
Versuchen Sie, Abhängigkeiten von bestimmten Schlüsselwerten zu vermeiden, oder passen Sie die Anwendung an das neue Verhalten an.

### <a name="backing-fields-are-used-by-default"></a>Unterstützungsfelder werden standardmäßig verwendet

[Issue #12430](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

**Altes Verhalten**

Vor Version 3.0 wurde in EF Core mithilfe der Getter- und Settermethoden standardmäßig der Eigenschaftswert gelesen und geschrieben. Das war selbst dann der Fall, wenn das Unterstützungsfeld für eine Eigenschaft nicht bekannt war.
Eine Ausnahme war die Abfrageausführung, bei der das Unterstützungsfeld direkt festgelegt wurde, wenn es bekannt war.

**Neues Verhalten**

Ab Version 3.0 wird in EF Core die Eigenschaft mithilfe des Unterstützungsfelds gelesen und geschrieben, wenn dieses für eine Eigenschaft bekannt ist.
Dies kann dazu führen, dass eine Anwendung nicht mehr funktioniert, wenn sie auf zusätzliches Verhalten angewiesen ist, das im Code der Getter- und Settermethoden definiert ist.

**Hintergründe**

Diese Änderung wurde vorgenommen, um zu verhindern, dass in EF Core fälschlicherweise immer dann Geschäftslogik ausgelöst wird, wenn Datenbankvorgänge für Entitäten durchgeführt werden.

**Gegenmaßnahmen**

Sie können das Verhalten von vor Version 3.0 wiederherstellen, indem Sie in `ModelBuilder` den Zugriffsmodus für die Eigenschaft konfigurieren.
Beispiel:

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a>Eine Ausnahme wird ausgelöst, wenn mehrere kompatible Unterstützungsfelder gefunden werden

[Issue #12523](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

**Altes Verhalten**

Vor Version 3.0 wurde in EF Core ein Unterstützungsfeld auf Grundlage einer Rangfolge ausgewählt, wenn die Regeln zum Suchen eines Eigenschaftsfelds auf mehrere Felder zutrafen.
Dabei konnte es vorkommen, dass in mehrdeutigen Fällen das falsche Feld verwendet wurde.

**Neues Verhalten**

Ab Version 3.0 wird in EF Core eine Ausnahme ausgelöst, wenn sich mehrere Felder auf dieselbe Eigenschaft beziehen.

**Hintergründe**

Diese Änderung wurde vorgenommen, um zu vermeiden, dass automatisch ein falsches Feld verwendet wird.

**Gegenmaßnahmen**

Für Eigenschaften mit mehrdeutigen Unterstützungsfeldern muss das zu verwendende Feld explizit festgelegt werden.
Mit der Fluent-API ist dies beispielsweise wie folgt möglich:

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a>„Nur-Feld“-Eigenschaftsnamen sollten dem Feldnamen entsprechen

**Altes Verhalten**

Vor Version 3.0 konnte in EF Core eine Eigenschaft durch einen Zeichenfolgenwert angegeben werden, und wenn keine Eigenschaft mit diesem Namen im .NET-Typ gefunden wurde, versuchte EF Core, mithilfe von Konventionsregeln ein übereinstimmendes Feld zu finden.

```csharp
private class Blog
{
    private int _id;
    public string Name { get; set; }
}
```

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id");
```

**Neues Verhalten**

Ab Version 3.0 muss in EF Core eine „Nur-Feld“-Eigenschaft mit dem Namen des Felds genau übereinstimmen.

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

**Hintergründe**

Diese Änderung wurde vorgenommen, um zu vermeiden, dass das gleiche Feld für zwei Eigenschaften mit ähnlichem Namen verwendet wird. Durch die Änderung entsprechen nun auch die Übereinstimmungsregeln für „Nur-Feld“-Eigenschaften den Regeln für Eigenschaften, die CLR-Eigenschaften zugeordnet sind.

**Gegenmaßnahmen**

„Nur-Feld“-Eigenschaften müssen so benannt werden wie das Feld, dem sie zugeordnet sind.
In einer kommenden Version von EF Core nach 3.0 soll das explizite Konfigurieren eines Feldnamens, der sich vom Eigenschaftsnamen unterscheidet, erneut aktiviert werden (siehe Problem [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a>Von AddDbContext/AddDbContextPool werden AddLogging und AddMemoryCache nicht mehr aufgerufen.

[Issue #14756](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

**Altes Verhalten**

Vor EF Core 3.0 wurden beim Aufruf von `AddDbContext` oder `AddDbContextPool` durch Aufrufe von [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) und [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) auch Protokollierungs- und Arbeitsspeichercachingdienste bei DI registriert.

**Neues Verhalten**

Ab EF Core 3.0 werden diese Dienste durch `AddDbContext` und `AddDbContextPool` nicht mehr bei Dependency Injection (DI) registriert.

**Hintergründe**

Für EF Core 3.0 ist es nicht erforderlich, dass diese Dienste im DI-Container der Anwendung enthalten sind. Wenn `ILoggerFactory` jedoch im DI-Container der Anwendung registriert ist, wird sie nach wie vor von EF Core verwendet.

**Gegenmaßnahmen**

Wenn Ihre Anwendung diese Dienste benötigt, registrieren Sie sie explizit mit [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) oder [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) beim DI-Container.

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a>AddEntityFramework* fügt IMemoryCache mit einer Größenbeschränkung hinzu

[Issue 12905](https://github.com/aspnet/EntityFrameworkCore/issues/12905)

**Altes Verhalten**

Vor EF Core 3.0 wurden bei Aufrufen von `AddEntityFramework*`-Methoden auch Arbeitsspeichercachingdienste ohne eine Größenbeschränkung bei DI registriert.

**Neues Verhalten**

Ab EF Core 3.0 registriert `AddEntityFramework*` einen IMemoryCache-Dienst mit einer Größenbeschränkung. Wenn danach weitere Dienste hinzugefügt werden, die von IMemoryCache abhängig sind, können diese schnell den Standardgrenzwert erreichen, was zu Ausnahmen und Leistungsbeeinträchtigungen führt.

**Hintergründe**

Die Verwendung von IMemoryCache ohne Einschränkung kann zu unkontrollierter Arbeitsspeicherauslastung führen, wenn ein Fehler in die Abfragecachelogik auftritt oder wenn die Abfragen dynamisch generiert werden. Durch eine Standardeinschränkung können potenzielle DoS-Angriffe verhindert werden.

**Gegenmaßnahmen**

In den meisten Fällen ist das Aufrufen von `AddEntityFramework*` nicht erforderlich, wenn `AddDbContext` oder `AddDbContextPool` ebenfalls aufgerufen werden. Daher besteht die beste Entschärfung darin, den `AddEntityFramework*`-Aufruf zu entfernen.

Wenn Ihre Anwendung diese Dienste erfordert, registrieren Sie im Voraus eine IMemoryCache-Implementierung explizit mit dem DI-Container mithilfe von [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a>DbContext.Entry erkennt nun mit DetectChanges lokale Änderungen

[Issue #13552](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

**Altes Verhalten**

Vor Version 3.0 führte in EF Core ein Aufruf von `DbContext.Entry` dazu, dass Änderungen an allen überwachten Entitäten erkannt wurden.
Dadurch wurde sichergestellt, dass der Zustand in `EntityEntry` immer aktuell war.

**Neues Verhalten**

Ab Version 3.0 werden in EF Core Änderungen bei einem Aufruf von `DbContext.Entry` nur in der angegebenen Entität und in allen überwachten Prinzipalentitäten erkannt.
Änderungen an anderer Stelle werden daher durch den Aufruf dieser Methode möglicherweise nicht erkannt, was sich auf den Anwendungszustand auswirken kann.

Beachten Sie, dass selbst die Erkennung lokaler Änderungen deaktiviert wird, wenn `ChangeTracker.AutoDetectChangesEnabled` auf `false` festgelegt wird.

Andere Methoden wie `ChangeTracker.Entries` und `SaveChanges`, die eine Änderungserkennung auslösen, führen nach wie vor zu einem vollständigen `DetectChanges`-Vorgang für alle überwachten Entitäten.

**Hintergründe**

Diese Änderung wurde vorgenommen, um die Leistung bei der standardmäßigen Verwendung von `context.Entry` zu verbessern.

**Gegenmaßnahmen**

Rufen Sie `ChangeTracker.DetectChanges()` explizit vor dem Aufruf von `Entry` auf, wenn das Verhalten vor Version 3.0 beibehalten werden soll.

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a>Schlüssel aus Zeichenfolgen und Bytearrays werden standardmäßig nicht vom Client generiert

[Issue #14617](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

**Altes Verhalten**

Vor Version 3.0 konnten in EF Core `string`- und `byte[]`-Schlüsseleigenschaften verwendet werden, ohne dass explizit ein anderer Wert als NULL festgelegt werden musste.
In diesem Fall wurde der Schlüsselwert auf dem Client als GUID generiert und für `byte[]` in Bytes serialisiert.

**Neues Verhalten**

Ab Version 3.0 wird in EF Core eine Ausnahme ausgelöst, wenn kein Schlüsselwert festgelegt wird.

**Hintergründe**

Diese Änderung wurde vorgenommen, da vom Client generierte `string`/`byte[]`-Werte in der Regel nicht sinnvoll sind. Das Standardverhalten führte außerdem zu kaum nachvollziehbaren generierten Schlüsselwerten.

**Gegenmaßnahmen**

Wenn Sie das Verhalten vor Version 3.0 nutzen möchten, müssen Sie explizit festlegen, dass für die Schlüsseleigenschaften generierte Werte verwendet sollen, wenn kein anderer Nicht-NULL-Wert festgelegt wird.
Dies ist beispielsweise mit der Fluent-API möglich:

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

Alternativ bieten sich auch Datenanmerkungen an:

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a>ILoggerFactory ist nun ein bereichsbezogener Dienst

[Issue #14698](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

**Altes Verhalten**

Vor Version 3.0 wurde `ILoggerFactory` in EF Core als Singletondienst registriert.

**Neues Verhalten**

Ab Version 3.0 wird in EF Core `ILoggerFactory` als bereichsbezogener Dienst registriert.

**Hintergründe**

Diese Änderung wurde vorgenommen, um einer `DbContext`-Instanz eine Protokollierung zuordnen zu können. Dadurch werden weitere Funktionen bereitgestellt. In einigen Fällen wird außerdem schädliches Verhalten wie etwa die schnelle Zunahme von internen Dienstanbietern beseitigt.

**Gegenmaßnahmen**

Diese Änderung wirkt sich nur dann auf den Anwendungscode aus, wenn benutzerdefinierte Dienste auf dem internen Dienstanbieter von EF Core registriert und verwendet werden.
Ein solches Szenario ist aber unüblich.
In einem derartigen Fall funktioniert fast alles wie vorher. Singletondienste, die von `ILoggerFactory` abhängig sind, müssen jedoch so angepasst werden, dass `ILoggerFactory` auf andere Weise abgerufen wird.

Erstellen Sie in diesen Situationen bitte im [GitHub-Issuetracker für EF Core](https://github.com/aspnet/EntityFrameworkCore/issues) ein Issue, in dem Sie beschreiben, wie Sie `ILoggerFactory` verwenden. So können wir in Zukunft erneute Breaking Changes vermeiden.

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a>Für Proxys mit Lazy Loading (trägem Laden) gilt nicht mehr die Annahme, dass Navigationseigenschaften vollständig geladen sind

[Issue #12780](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

**Altes Verhalten**

Vor Version 3.0 gab es in EF Core nach der Freigabe eines `DbContext`-Objekts keine Möglichkeit, zu ermitteln, ob eine aus dem Kontext abgerufene Navigationseigenschaft für eine Entität vollständig geladen war.
Stattdessen galt für Proxys die Annahme, dass eine Verweisnavigation geladen ist, falls für diese ein anderer Wert als NULL vorliegt. Außerdem wurde davon ausgegangen, dass eine Sammlungsnavigation geladen ist, wenn sie nicht leer ist.
In diesen Fällen entsprach das Lazy Loading einer Nulloperation („No-Op“).

**Neues Verhalten**

Ab Version 3.0 überwachen in EF Core Proxys, ob eine Navigationseigenschaft geladen ist.
Wenn also auf eine Navigationseigenschaft zugegriffen werden soll, die nach der Freigabe des Kontexts geladen wird, ist dies immer eine No-Op-Operation. Das ist selbst dann der Fall, wenn die geladene Navigation leer oder NULL ist.
Wenn umgekehrt auf eine nicht geladene Navigationseigenschaft zugegriffen werden soll und der Kontext bereits freigegeben wurde, wird eine Ausnahme ausgelöst. Dieser Fall tritt auch dann ein, wenn die Navigationseigenschaft eine nicht leere Sammlung ist.
In dieser Situation wird also vom Anwendungscode versucht, zu einem ungültigen Zeitpunkt Lazy Loading durchzuführen. Die Anwendung sollte so angepasst werden, dass dieser Vorgang vermieden wird.

**Hintergründe**

Diese Änderung wurde vorgenommen, um das Verhalten beim Lazy Loading einer freigegebenen `DbContext`-Instanz konsistent und korrekt zu gestalten.

**Gegenmaßnahmen**

Aktualisieren Sie den Anwendungscode so, dass kein Lazy Loading durchgeführt wird, nachdem der Kontext freigegeben wurde. Alternativ können Sie auch, wie in der Ausnahmemeldung beschrieben wird, eine No-Op-Operation festlegen.

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a>Die Erstellung zu vieler interner Dienstanbieter führt standardmäßig zu einem Fehler

[Issue #10236](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

**Altes Verhalten**

Vor Version 3.0 wurde in EF Core eine Warnung für eine Anwendung protokolliert, die unverhältnismäßig viele interne Dienstanbieter erstellte.

**Neues Verhalten**

Ab Version 3.0 wird diese Warnung in EF Core als Fehler betrachtet, und eine Ausnahme wird ausgelöst. 

**Hintergründe**

Diese Änderung wurde vorgenommen, damit auf den oben beschriebenen Fall explizit hingewiesen wird. So soll die Entwicklung von besserem Anwendungscode erleichtert werden.

**Gegenmaßnahmen**

Wenn dieser Fehler auftritt, müssen Sie die Grundursache ermitteln und die Erstellung vieler interner Dienstanbieter unterbinden.
Sie können den Fehler allerdings auch wieder in eine Warnung konvertierten (oder ihn ignorieren), indem Sie `DbContextOptionsBuilder` konfigurieren.
Beispiel:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a>Neues Verhalten für HasOne/HasMany bei Aufruf mit einer einzelnen Zeichenfolge

[Issue #9171](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

**Altes Verhalten**

Vor EF Core 3.0 wurde Code, der `HasOne` oder `HasMany` mit einer einzelnen Zeichenfolge aufruft, auf irritierende Weise interpretiert.
Beispiel:
```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

Der Code scheint `Samurai` mit einem anderen Entitätstyp über die `Entrance`-Navigationseigenschaft zu verknüpfen, die möglicherweise privat ist.

Tatsächlich versucht dieser Code, eine Beziehung zu einem Entitätstyp namens `Entrance` ohne Navigationseigenschaft herzustellen.

**Neues Verhalten**

Ab EF Core 3.0 führt der obige Code jetzt die erwartete Aktion durch.

**Hintergründe**

Das alte Verhalten war sehr verwirrend, vor allem beim Lesen des Konfigurationscodes und bei der Suche nach Fehlern.

**Gegenmaßnahmen**

Dies führt nur zu Fehlern in Anwendungen, die Beziehungen explizit unter Verwendung von Zeichenfolgen für Typnamen und ohne explizite Angabe der Navigationseigenschaft konfigurieren.
Dies ist nicht üblich.
Das vorherige Verhalten kann durch explizite Übergabe von `null` für den Namen der Navigationseigenschaft erzielt werden.
Beispiel:

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a>Der Rückgabetyp für mehrere asynchrone Methoden wurde von Task in ValueTask geändert

[Issue #15184](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

**Altes Verhalten**

Die folgenden asynchronen Methoden gaben zuvor `Task<T>` zurück:

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* `ValueGenerator.NextValueAsync()` (und abgeleitete Klassen)

**Neues Verhalten**

Die oben genannten Methoden geben nun `ValueTask<T>` über dieselbe `T` wie zuvor zurück.

**Hintergründe**

Durch diese Änderung verringert sich die Anzahl der Heapzuordnungen, die beim Aufrufen dieser Methoden entstehen, und dies führt zu einer Verbesserung der allgemeinen Leistung.

**Gegenmaßnahmen**

Anwendungen, die einfach die oben genannten APIs erwarten, müssen nur neu kompiliert werden – Quelländerungen sind nicht erforderlich.
Eine komplexere Verwendung (z.B. Übergeben der zurückgegebenen `Task` an `Task.WhenAny()`) erfordern in der Regel, dass die zurückgegebene `ValueTask<T>` durch einen Aufruf von `AsTask()` in `Task<T>` konvertiert wird.
Beachten Sie, dass dadurch die mit dieser Änderung verbundene Verringerung der Zuordnungen aufgehoben wird.

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a>Die Anmerkung Relational:TypeMapping heißt nun nur noch TypeMapping

[Issue #9913](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

**Altes Verhalten**

Der Anmerkungsname für Typzuordnungsanmerkungen war bisher Relational:TypeMapping.

**Neues Verhalten**

Der Anmerkungsname für Typzuordnungsanmerkungen lautet nun TypeMapping.

**Hintergründe**

Typzuordnungen werden nicht mehr ausschließlich für Anbieter relationaler Datenbanken verwendet.

**Gegenmaßnahmen**

Diese Änderung ist nur dann ein Breaking Change, wenn Anwendungen direkt über eine Anmerkung auf die Typzuordnung zugreifen. Dieses Szenario ist jedoch unüblich.
Verzichten Sie daher darauf, die Anmerkung direkt zu verwenden, und greifen Sie stattdessen über die Fluent-API auf Typzuordnungen zu, um das Problem zu beheben.

### <a name="totable-on-a-derived-type-throws-an-exception"></a>ToTable löst für einen abgeleiteten Typ eine Ausnahme aus 

[Issue #11811](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

**Altes Verhalten**

Vor Version 3.0 wurde in EF Core der Aufruf von `ToTable()` für einen abgeleiteten Typ ignoriert, da TPH die einzige Strategie zur Vererbungszuordnung war und diese in unzulässigen Fällen angewendet wurde. 

**Neues Verhalten**

Ab Version 3.0 wird in EF Core eine Ausnahme ausgelöst, um später eine unerwartete Zuordnung zu vermeiden, wenn `ToTable()` für einen abgeleiteten Typ aufgerufen wird. Dieser Schritt dient auch als Vorbereitung für die TPT- und TPC-Unterstützung, die in einem künftigen Release folgen wird.

**Hintergründe**

Derzeit ist es nicht zulässig, einen abgeleiteten Typ einer anderen Tabelle zuzuordnen.
Durch diese Änderung werden Breaking Changes vermieden, wenn der beschriebene Vorgang in Zukunft zulässig wird.

**Gegenmaßnahmen**

Entfernen Sie alle Zuordnungen von abgeleiteten Typen zu anderen Tabellen.

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a>ForSqlServerHasIndex wurde durch HasIndex ersetzt 

[Issue #12366](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

**Altes Verhalten**

Vor Version 3.0 konnten in EF Core mit `ForSqlServerHasIndex().ForSqlServerInclude()` Spalten konfiguriert werden, die mit `INCLUDE` verwendet wurden.

**Neues Verhalten**

Ab Version 3.0 wird in EF Core die Nutzung von `Include` für einen Index auf der relationalen Ebene unterstützt.
Verwenden Sie `HasIndex().ForSqlServerInclude()`.

**Hintergründe**

Diese Änderung wurde vorgenommen, um die API für Indizes mit `Include` an einer zentralen Stelle für alle Datenbankanbieter zusammenzuführen.

**Gegenmaßnahmen**

Verwenden Sie wie oben beschrieben die neue API.

### <a name="metadata-api-changes"></a>Metadaten-API-Änderungen

[Issue #214](https://github.com/aspnet/EntityFrameworkCore/issues/214)

**Neues Verhalten**

Die folgenden Eigenschaften wurden in Erweiterungsmethoden konvertiert:

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

**Hintergründe**

Mit dieser Änderung wird die Implementierung der oben genannten Schnittstellen vereinfacht.

**Gegenmaßnahmen**

Verwenden Sie die neuen Erweiterungsmethoden.

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a>Anbieterspezifische Metadaten-API-Änderungen

[Issue #214](https://github.com/aspnet/EntityFrameworkCore/issues/214)

**Neues Verhalten**

Die anbieterspezifischen Erweiterungsmethoden werden vereinfacht:

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

**Hintergründe**

Mit dieser Änderung wird die Implementierung der oben genannten Erweiterungsmethoden vereinfacht.

**Gegenmaßnahmen**

Verwenden Sie die neuen Erweiterungsmethoden.

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a>EF Core sendet keine PRAGMA-Anweisungen mehr, um Fremdschlüssel in SQLite zu erzwingen

[Issue #12151](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

**Altes Verhalten**

Vor Version 3.0 wurden in EF Core `PRAGMA foreign_keys = 1`-Anweisungen gesendet, wenn eine Verbindung mit SQLite hergestellt wurde.

**Neues Verhalten**

Ab Version 3.0 werden in EF Core keine `PRAGMA foreign_keys = 1`-Anweisungen mehr gesendet, wenn eine Verbindung mit SQLite hergestellt wird.

**Hintergründe**

Diese Änderung wurde vorgenommen, da von EF Core standardmäßig `SQLitePCLRaw.bundle_e_sqlite3` verwendet wird. Dadurch ist die Erzwingung von Fremdschlüsseln standardmäßig aktiviert und muss nicht jedes Mal explizit aktiviert werden, wenn eine Verbindung hergestellt wird.

**Gegenmaßnahmen**

Fremdschlüssel sind in SQLitePCLRaw.bundle_e_sqlite3 standardmäßig aktiviert. Dieses Bundle wird standardmäßig von EF Core verwendet.
In anderen Fällen können Fremdschlüssel durch die Angabe `Foreign Keys=True` in der Verbindungszeichenfolge aktiviert werden.

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a>SQLitePCLRaw.bundle_e_sqlite3 ist nun eine Abhängigkeit von Microsoft.EntityFrameworkCore.Sqlite

**Altes Verhalten**

Vor Version 3.0 wurde von EF Core `SQLitePCLRaw.bundle_green` verwendet.

**Neues Verhalten**

Ab Version 3.0 wird von EF Core `SQLitePCLRaw.bundle_e_sqlite3` verwendet.

**Hintergründe**

Diese Änderung wurde vorgenommen, damit die unter iOS verwendete Version von SQLite sich konsistent zu Versionen auf anderen Plattformen verhält.

**Gegenmaßnahmen**

Konfigurieren Sie `Microsoft.Data.Sqlite` so, dass ein anderes `SQLitePCLRaw`-Bundle verwendet wird, um die native SQLite-Version unter iOS zu nutzen.

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a>GUID-Werte werden jetzt als TEXT in SQLite gespeichert

[Issue #15078](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

**Altes Verhalten**

GUID-Werte wurden in der Vergangenheit in SQLite als BLOB-Werte gespeichert.

**Neues Verhalten**

GUID-Werte werden jetzt als TEXT gespeichert.

**Hintergründe**

Das Binärformat der GUIDs ist nicht standardisiert. Das Speichern der Werte als TEXT führt dazu, dass die Datenbank mit anderen Technologien eher kompatibel ist.

**Gegenmaßnahmen**

Sie können vorhandene Datenbanken zum neuen Format migrieren, indem Sie SQL-Code wie den folgenden ausführen:

``` sql
UPDATE MyTable
SET GuidColumn = hex(substr(GuidColumn, 4, 1)) ||
                 hex(substr(GuidColumn, 3, 1)) ||
                 hex(substr(GuidColumn, 2, 1)) ||
                 hex(substr(GuidColumn, 1, 1)) || '-' ||
                 hex(substr(GuidColumn, 6, 1)) ||
                 hex(substr(GuidColumn, 5, 1)) || '-' ||
                 hex(substr(GuidColumn, 8, 1)) ||
                 hex(substr(GuidColumn, 7, 1)) || '-' ||
                 hex(substr(GuidColumn, 9, 2)) || '-' ||
                 hex(substr(GuidColumn, 11, 6))
WHERE typeof(GuidColumn) == 'blob';
```

Sie können in EF Core auch weiterhin das alte Verhalten verwenden, indem Sie einen Wertkonverter für diese Eigenschaften konfigurieren.

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

Microsoft.Data.Sqlite kann weiterhin GUID-Werte aus BLOB- und TEXT-Spalten lesen. Da sich jedoch das Standardformat für Parameter und Konstanten geändert hat, müssen Sie wahrscheinlich Aktionen für die meisten GUID-Szenarios vornehmen.

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a>Char-Werte werden jetzt als TEXT in SQLite gespeichert

[Issue #15020](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

**Altes Verhalten**

Char-Werte wurden in der Vergangenheit in SQLite als INTEGER-Werte gespeichert. Der Char-Wert *A* wurde z.B. als INTEGER-Wert 65 gespeichert.

**Neues Verhalten**

Char-Werte werden jetzt als TEXT gespeichert.

**Hintergründe**

Das Speichern der Werte als TEXT ist naheliegender und führt dazu, dass die Datenbank mit anderen Technologien eher kompatibel ist.

**Gegenmaßnahmen**

Sie können vorhandene Datenbanken zum neuen Format migrieren, indem Sie SQL-Code wie den folgenden ausführen:

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

Sie können in EF Core auch weiterhin das alte Verhalten verwenden, indem Sie einen Wertkonverter für diese Eigenschaften konfigurieren.

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

Microsoft.Data.Sqlite kann auch weiterhin Zeichenwerte aus INTEGER- und TEXT-Spalten lesen, weshalb es sein kann, dass in einigen Szenarios keine weiteren Maßnahmen erforderlich sind.

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a>Migrations-IDs werden jetzt mit dem Kalender der invarianten Kultur generiert

[Issue #12978](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

**Altes Verhalten**

Migrations-IDs wurden versehentlich mit dem Kalender der aktuellen Kultur generiert.

**Neues Verhalten**

Migrations-IDs werden jetzt immer mit dem Kalender der invarianten Kultur generiert (gregorianischer Kalender).

**Hintergründe**

Die Reihenfolge der Migrationen ist beim Aktualisieren einer Datenbank oder Auflösen von Mergekonflikten wesentlich. Wenn der invariante Kalender verwendet wird, werden Probleme bei der Reihenfolge vermieden, die entstehen, wenn Teammitglieder unterschiedliche Systemkalender verwenden.

**Gegenmaßnahmen**

Diese Änderung betrifft jeden Benutzer, der einen Kalender verwendet, der nicht gregorianisch ist und bei dem die Jahreszahl höher als die des gregorianischen Kalenders ist (wie z.B. in der buddhistischen Zeitrechnung). Vorhandene Migrations-IDs müssen aktualisiert werden, damit neue Migrationen in der Reihenfolge hinter vorhandenen Migrationen eingeordnet werden.

Sie können die Migrations-ID im Migration-Attribut in der Designerdatei der Migration finden.

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

Die Tabelle mit dem Migrationsverlauf muss auch aktualisiert werden.

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a>UseRowNumberForPaging wurde entfernt.

[Issue #16400](https://github.com/aspnet/EntityFrameworkCore/issues/16400)

**Altes Verhalten**

Vor EF Core 3.0 konnte `UseRowNumberForPaging` zum Generieren von SQL-Code für die Paginierung verwendet werden, der mit SQL Server 2008 kompatibel ist.

**Neues Verhalten**

Ab EF Core 3.0 generiert EF nur noch SQL-Code für die Paginierung, die mit höheren SQL Server-Versionen kompatibel ist. 

**Hintergründe**

Wir führen diese Änderung ein, weil [SQL Server 2008 kein unterstütztes Produkt mehr ist](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) und weil die Aktualisierung dieses Features auf die in EF Core 3.0 vorgenommenen Änderungen an Abfragen ein wichtiger Schritt ist.

**Gegenmaßnahmen**

Es empfiehlt sich, eine Update auf eine neuere Version von SQL Server durchzuführen oder einen höheren Kompatibilitätsgrad zu verwenden, damit der generierte SQL-Code unterstützt wird. Wenn dies bei Ihnen nicht möglich ist, fügen Sie einen [Kommentar zum Issue](https://github.com/aspnet/EntityFrameworkCore/issues/16400) ein, und geben Sie alle relevanten Informationen an. Je nach Feedback der Benutzer wird diese Entscheidung möglicherweise noch einmal überprüft.

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a>Erweiterungsinformationen und Metadaten aus IDbContextOptionsExtension entfernt

[Issue #16119](https://github.com/aspnet/EntityFrameworkCore/issues/16119)

**Altes Verhalten**

`IDbContextOptionsExtension` enthielt Methoden zum Bereitstellen von Metadaten zur Erweiterung.

**Neues Verhalten**

Diese Methoden wurden in eine neue abstrakte `DbContextOptionsExtensionInfo`-Basisklasse verschoben, die von einer neuen `IDbContextOptionsExtension.Info`-Eigenschaft zurückgegeben werden.

**Hintergründe**

In den Releases zwischen 2.0 und 3.0 mussten wir diese Methoden mehrmals ergänzen oder ändern.
Durch die Bereitstellung dieser Methoden in einer neuen abstrakten Basisklasse lassen sich solche Änderungen einfacher einführen, ohne dass es zu Konflikten mit vorhandenen Erweiterungen kommt.

**Gegenmaßnahmen**

Erweiterungen wurden aktualisiert und verwenden das neue Muster.
Beispiele finden sich in den vielen Implementierungen von `IDbContextOptionsExtension` für verschiedene Arten von Erweiterungen im EF Core-Quellcode.

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a>LogQueryPossibleExceptionWithAggregateOperator wurde umbenannt

[Issue #10985](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

**Änderung**

`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` wurde umbenannt in `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.

**Hintergründe**

Die Benennung dieses Warnereignisses wurde an allen anderen Warnereignissen ausgerichtet.

**Gegenmaßnahmen**

Verwenden Sie den neuen Namen. (Beachten Sie, dass sich die Ereignis-ID-Nummer nicht geändert hat.)

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a>Verdeutlichen der API für Einschränkungsnamen von Fremdschlüsseln

[Issue #10730](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

**Altes Verhalten**

Vor EF Core 3.0 wurden Einschränkungsnamen von Fremdschlüsseln einfach als „Namen“ bezeichnet. Beispiel:

```csharp
var constraintName = myForeignKey.Name;
```

**Neues Verhalten**

Ab EF Core 3.0 werden Einschränkungsnamen von Fremdschlüsseln nun als „Einschränkungsnamen“ bezeichnet. Beispiel:

```csharp
var constraintName = myForeignKey.ConstraintName;
```

**Hintergründe**

Durch diese Änderung wird Konsistenz für Benennungen in diesem Bereich gewährleistet, und es wird verdeutlicht, dass es sich dabei um den Namen der Fremdschlüsseleinschränkung handelt und nicht um den Spalten- oder Eigenschaftennamen, auf deren Grundlage der Fremdschlüssel definiert ist.

**Gegenmaßnahmen**

Verwenden Sie den neuen Namen.

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a>„IRelationalDatabaseCreator.HasTables/HasTablesAsync“ ist jetzt öffentlich

[Issue #15997](https://github.com/aspnet/EntityFrameworkCore/issues/15997)

**Altes Verhalten**

Vor EF Core 3.0 waren diese Methoden geschützt.

**Neues Verhalten**

Ab EF Core 3.0 sind diese Methoden öffentlich.

**Hintergründe**

Diese Methoden werden von EF verwendet, um zu ermitteln, ob eine Datenbank erstellt wurde, aber leer ist. Dies kann auch außerhalb von EF nützlich sein, um zu ermitteln, ob Migrationen angewendet werden sollen oder nicht.

**Gegenmaßnahmen**

Der Zugriff auf Überschreibungen wurde geändert.

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a>Microsoft.EntityFrameworkCore.Design ist jetzt ein DevelopmentDependency-Paket

[Issue #11506](https://github.com/aspnet/EntityFrameworkCore/issues/11506)

**Altes Verhalten**

Vor EF Core 3.0 war Microsoft.EntityFrameworkCore.Design ein reguläres NuGet-Paket, auf dessen Assembly von Projekten verwiesen werden kann, die davon abhängig sind.

**Neues Verhalten**

Ab EF Core 3.0 ist es ein DevelopmentDependency-Paket. Das bedeutet, dass die Abhängigkeit nicht transitiv auf andere Projekte übertragen wird und Sie nicht mehr standardmäßig auf die Assembly verweisen können.

**Hintergründe**

Dieses Paket ist nur für die Verwendung zur Entwurfszeit konzipiert. Bereitgestellte Anwendungen sollten nicht darauf verweisen. Die Kennzeichnung des Pakets als DevelopmentDependency unterstreicht diese Empfehlung.

**Gegenmaßnahmen**

Wenn Sie auf dieses Paket verweisen müssen, um das Verhalten von EF Core zur Entwurfszeit zu überschreiben, können Sie die Metadaten des PackageReference-Elements in Ihrem Projekt aktualisieren.

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

Wenn transitiv über via Microsoft.EntityFrameworkCore.Tools auf das Paket verwiesen wird, müssen Sie dem Paket eine explizite PackageReference hinzufügen, um seine Metadaten zu ändern. Ein solch expliziter Verweis muss jedem Projekt hinzugefügt werden, für das die Typen aus den Paketen benötigt werden.

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a>SQLitePCL.raw zu Version 2.0.0 aktualisiert

[Issue #14824](https://github.com/aspnet/EntityFrameworkCore/issues/14824)

**Altes Verhalten**

Microsoft.EntityFrameworkCore.Sqlite war früher von Version 1.1.12 von SQLitePCL.raw abhängig.

**Neues Verhalten**

Wir haben unser Paket aktualisiert, so dass es jetzt von Version 2.0.0 abhängig ist.

**Hintergründe**

Version 2.0.0 von SQLitePCL.raw zielt auf .NET Standard 2.0 ab. Zuvor war .NET Standard 1.1 das Ziel, hierfür war für ein umfassender Funktionsabschluss von transitiven Paketen erforderlich.

**Gegenmaßnahmen**

SQLitePCL.raw, Version 2.0.0, enthält einige Breaking Changes. Weitere Informationen finden Sie in den [Versionshinweisen](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md).

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a>NetTopologySuite auf Version 2.0.0 aktualisiert

[Issue #14825](https://github.com/aspnet/EntityFrameworkCore/issues/14825)

**Altes Verhalten**

Die Pakete mit räumlichen Daten waren bisher von Version 1.15.1 der NetTopologySuite abhängig.

**Neues Verhalten**

Wir haben unser Paket aktualisiert, so dass es jetzt von Version 2.0.0 abhängig ist.

**Hintergründe**

Version 2.0.0 der NetTopologySuite behebt verschiedene Verwendungsprobleme, die von EF Core-Benutzern gemeldet wurden.

**Gegenmaßnahmen**

NetTopologySuite 2.0.0 umfasst einige Breaking Changes. Weitere Informationen finden Sie in den [Versionshinweisen](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001).

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a>Microsoft.Data.SqlClient wird statt System.Data.SqlClient verwendet

[Issue #15636](https://github.com/aspnet/EntityFrameworkCore/issues/15636)

**Altes Verhalten**

Microsoft.EntityFrameworkCore.SqlServer war zuvor von System.Data.SqlClient abhängig.

**Neues Verhalten**

Das Paket wurde so aktualisiert, dass es jetzt von Microsoft.Data.SqlClient abhängig ist.

**Hintergründe**

Microsoft SQL Server ist zukünftig der Haupttreiber für den Datenzugriff für SQL Server, und System.Data.SqlClient wird nicht länger der Fokus bei der Entwicklung sein.
Einige wichtige Features wie Always Encrypted sind nur in Microsoft.Data.SqlClient verfügbar.

**Gegenmaßnahmen**

Wenn Ihr Code eine direkte Abhängigkeit von System.Data.SqlClient hat, müssen Sie diesen ändern, damit er stattdessen auf Microsoft.Data.SqlClient verweist. Da die beiden Pakete einen hohen Grad an API-Kompatibilität vorweisen, sollte dafür nur eine einfache Paket- und Namespaceänderung erforderlich sein.

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a>Beziehungen mit mehreren mehrdeutigen Selbstverweisen müssen nun konfiguriert werden 

[Issue #13573](https://github.com/aspnet/EntityFrameworkCore/issues/13573)

**Altes Verhalten**

Ein Entitätstyp, der über unidirektionale Navigationseigenschaften mit mehreren Selbstverweisen und über übereinstimmende Fremdschlüssel verfügte, wurde bislang fälschlicherweise als einfache Beziehung konfiguriert. Beispiel:

```csharp
public class User 
{
        public Guid Id { get; set; }
        public User CreatedBy { get; set; }
        public User UpdatedBy { get; set; }
        public Guid CreatedById { get; set; }
        public Guid? UpdatedById { get; set; }
}
```

**Neues Verhalten**

Dieses Szenario wird nun beim Erstellen von Modellen erkannt. Außerdem wird eine Ausnahme ausgelöst, in der darauf hingewiesen wird, dass das Modell mehrdeutig ist.

**Hintergründe**

Das resultierende Modell war mehrdeutig und führte in diesem Fall üblicherweise zu falschen Ergebnissen.

**Gegenmaßnahmen**

Konfigurieren Sie die Beziehung vollständig. Beispiel:

```csharp
modelBuilder
     .Entity<User>()
     .HasOne(e => e.CreatedBy)
     .WithMany();
 
 modelBuilder
     .Entity<User>()
     .HasOne(e => e.UpdatedBy)
     .WithMany();
```

<a name="udf-empty-string"></a>
### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a>DbFunction.Schema ist NULL oder eine leere Zeichenfolge und ist deshalb so konfiguriert, dass es im Standardschema des Modells ist

[Issue #12757](https://github.com/aspnet/EntityFrameworkCore/issues/12757)

**Altes Verhalten**

Eine mit dem Schema als leere Zeichenfolge konfigurierte DbFunction wurde als integrierte Funktion ohne Schema behandelt. Der folgende Code ordnet z. B. die CLR-Funktion `DatePart` der integrierten Funktion `DATEPART` auf SqlServer zu.

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

**Neues Verhalten**

Alle DbFunction-Zuordnungen werden als einer benutzerdefinierten Funktion zugeordnet betrachtet. Daher würde die Funktion durch einen leeren Zeichenfolgenwert in das Standardschema für das Modell eingefügt werden. Dies könnte das Schema sein, das explizit über eine fließende `modelBuilder.HasDefaultSchema()`- oder anderweitig über eine `dbo`-API konfiguriert wurde.

**Hintergründe**

Weil das Schema vorher leer war, konnte man erreichen, dass die Funktion integriert, aber die Logik nur für SqlServer anwendbar war, wo integrierte Funktionen nicht zu einem Schema gehören.

**Gegenmaßnahmen**

Konfigurieren Sie die Übersetzung von DbFunction manuell, um Sie einer integrierten Funktion zuzuordnen.

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```
