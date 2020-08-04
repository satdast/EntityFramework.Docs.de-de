---
title: Neue Funktionen in Entity Framework Core 3.0
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/index
ms.openlocfilehash: ab61716fbf9cd4a256903a99540b6ecea2b5d37c
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526835"
---
# <a name="new-features-in-entity-framework-core-30"></a>Neue Funktionen in Entity Framework Core 3.0

Die folgende Liste enthält die wichtigsten neuen Features in EF Core 3.0.

EF Core 3.0 ist eine Hauptversion und enthält damit zahlreiche [Breaking Changes](xref:core/what-is-new/ef-core-3.0/breaking-changes). Hierbei handelt es sich um Verbesserungen der API, die einen negativen Einfluss auf vorhandene Anwendungen haben können.  

## <a name="linq-overhaul"></a>LINQ-Überarbeitung

Mit LINQ können Sie Datenbankabfragen in einer beliebigen .NET-Sprache schreiben. Dabei werden Rich-Type-Informationen genutzt, um IntelliSense und Typprüfung zur Kompilierungszeit bereitzustellen.
LINQ ermöglicht es Ihnen darüber hinaus, eine unbegrenzte Anzahl komplizierter Abfragen zu schreiben, die beliebige Ausdrücke enthalten (Methodenaufrufe oder Operationen).
Die Behandlung dieser Kombinationen ist die größte Herausforderung für LINQ-Anbieter.

In EF Core 3.0 wurde der LINQ-Anbieter neu strukturiert, damit mehr Abfragemuster in SQL übersetzt werden können, in mehr Fällen effiziente Abfragen erzeugt werden können und ineffiziente Abfragen erkannt werden. Der neue LINQ-Anbieter ist die Grundlage, dank derer wir in zukünftigen Versionen neue Abfragefunktionen und Leistungsverbesserungen anbieten können, ohne die Funktionsfähigkeit vorhandener Anwendungen und Datenanbieter zu beeinträchtigen.

### <a name="restricted-client-evaluation"></a>Eingeschränkte Clientauswertung

Die wichtigste Änderung hat mit der Behandlung von LINQ-Ausdrücken zu tun, die nicht in Parameter konvertiert oder in SQL übersetzt werden können.

In vorherigen Versionen ermittelte EF Core einfach, welche Teile einer Abfrage in SQL übersetzt werden konnten, und führte den Rest der Abfrage auf dem Client aus.
Diese Art von clientseitiger Ausführung kann in einigen Situationen wünschenswert sein, aber in vielen anderen Fällen kann sie zu ineffizienten Abfragen führen.

Wenn beispielsweise EF Core 2.2 ein Prädikat in einem `Where()`-Aufruf nicht übersetzen konnte, wurde eine SQL-Anweisung ohne Filter ausgeführt, alle Zeilen aus der Datenbank wurden übertragen und dann im Arbeitsspeicher gefiltert:

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n) && IsSpecialCustomer(c));
```

Dies kann akzeptabel sein, wenn die Datenbank nur wenige Zeilen enthält, kann aber auch zu erheblichen Leistungsproblemen oder sogar dem Ausfall der Anwendung führen, wenn eine Datenbank eine große Anzahl von Zeilen enthält.

In EF Core 3.0 haben wir die Clientauswertung auf die Projektion der obersten Ebene (den letzten Aufruf von `Select()`) eingeschränkt.
Wenn EF Core 3.0 an irgendeiner anderen Stelle in der Abfrage Ausdrücke erkennt, die nicht übersetzt werden können, wird eine Laufzeitausnahme ausgegeben.

Um eine Prädikatbedingung auf dem Client wie im vorherigen Beispiel auszuwerten, müssen Entwickler jetzt die Auswertung der Abfrage explizit auf LINQ to Objects umstellen:

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n))
    .AsEnumerable() // switches to LINQ to Objects
    .Where(c => IsSpecialCustomer(c));
```

Weitere Informationen zu den Auswirkungen auf vorhandene Anwendungen finden Sie in der [Dokumentation zu Breaking Changes](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client).

### <a name="single-sql-statement-per-linq-query"></a>Einzelne SQL-Anweisung pro LINQ-Abfrage

Ein weiterer Aspekt des geänderten Designs in Version 3.0 ist die Tatsache, dass jetzt immer eine einzelne SQL-Anweisung pro LINQ-Abfrage generiert wird. In früheren Versionen wurden in bestimmten Fällen mehrere SQL-Anweisungen generiert, z. B. um `Include()`-Aufrufe in Auflistungs-Navigationseigenschaften oder Abfragen zu übersetzen, die bestimmten Mustern mit Unterabfragen entsprachen. Obwohl dies in einigen Fällen praktisch war und für `Include()` sogar verhinderte, dass redundante Daten über das Netzwerk gesendet wurden, war die Implementierung komplex und führte zu einigen äußerst ineffizienten Verhaltensweisen (N+1-Abfragen). In einigen Situationen waren die Daten, die über mehrere Abfragen zurückgegeben werden, inkonsistent.

Ähnlich wie bei der Clientauswertung wird in EF Core 3.0 eine Laufzeitausnahme ausgelöst, wenn eine LINQ-Abfrage nicht in eine einzelne SQL-Anweisung übersetzt werden kann. Mit EF Core können aber einige der gängigen Muster, mit denen mehrere Abfragen generiert wurden, in eine einzelne Abfrage mit JOINs übersetzt werden.

## <a name="cosmos-db-support"></a>Cosmos DB-Unterstützung

Der Cosmos DB-Anbieter für EF Core ermöglicht es Entwicklern, die mit dem EF-Programmiermodell vertraut sind, Azure Cosmos DB problemlos als Anwendungsdatenbank-Ziel einzusetzen. Ziel ist es, einige der Vorteile von Cosmos DB – beispielsweise die globale Verteilung, Always On-Verfügbarkeit, elastische Skalierbarkeit und geringe Latenz – noch besser für .NET-Entwickler zugänglich zu machen. Der Anbieter unterstützt die meisten EF Core-Features, darunter die automatische Änderungsnachverfolgung, LINQ und Wertekonvertierungen anhand der SQL-API in Cosmos DB.

Weitere Informationen finden Sie in der [Dokumentation zum Cosmos DB-Anbieter](xref:core/providers/cosmos/index).

## <a name="c-80-support"></a>C# 8.0-Unterstützung

EF Core 3.0 nutzt einige der [neuen Funktionen in C# 8.0](/dotnet/csharp/whats-new/csharp-8):

### <a name="asynchronous-streams"></a>Asynchrone Streams

Asynchrone Abfrageergebnisse werden jetzt mithilfe der neuen `IAsyncEnumerable<T>`-Standardschnittelle verfügbar gemacht und können mithilfe von `await foreach` konsumiert werden.

``` csharp
var orders =
    from o in context.Orders
    where o.Status == OrderStatus.Pending
    select o;

await foreach(var o in orders.AsAsyncEnumerable())
{
    Process(o);
}
```

Weitere Informationen finden Sie in der [C#-Dokumentation zu asynchronen Streams](/dotnet/csharp/whats-new/csharp-8#asynchronous-streams).

### <a name="nullable-reference-types"></a>Nullwerte zulassende Verweistypen

Wenn diese neue Funktion in Ihrem Code aktiviert ist, prüft EF Core die NULL-Zulässigkeit von Verweistypeigenschaften und wendet sie auf die entsprechenden Spalten und Beziehungen in der Datenbank an: Eigenschaften von Verweistypen, die keine NULL-Werte zulassen, werden so behandelt, als wiesen sie das Datenanmerkungsattribut `[Required]` auf.

In der folgenden Klasse werden z. B. Eigenschaften mit dem Typ `string?` als optional konfiguriert, während `string` als erforderlich konfiguriert wird:

``` csharp
public class Customer
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string? MiddleName { get; set; }
}
```

Weitere Informationen finden Sie in der [EF Core-Dokumentation zum Arbeiten mit Verweistypen, die NULL-Werte zulassen](xref:core/miscellaneous/nullable-reference-types).

## <a name="interception-of-database-operations"></a>Abfangen von Datenbankvorgängen

Durch die neue Abfang-API in EF Core 3.0 kann benutzerdefinierte Logik automatisch aufgerufen werden, wenn Datenbankvorgänge auf niedriger Ebene im Rahmen des normalen Betriebs von EF Core auftreten. Hierzu zählen z. B. das Öffnen von Verbindungen, Committen von Transaktionen oder Ausführen von Befehlen.

Ähnlich wie bei den Abfangfunktionen in EF 6 können Sie mit den Abfangfunktionen Vorgänge vor oder nach dem Auftreten abfangen. Wenn Sie sie vorher abfangen, können Sie die Ausführung umgehen und alternative Ergebnisse aus der Abfanglogik angeben.

Zum Bearbeiten von Befehlstext können Sie beispielsweise `DbCommandInterceptor` erstellen:

``` csharp
public class HintCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult<DbDataReader> ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result)
    {
        // Manipulate the command text, etc. here...
        command.CommandText += " OPTION (OPTIMIZE FOR UNKNOWN)";
        return result;
    }
}
```

Und bei  `DbContext` registrieren:

``` csharp
services.AddDbContext(b => b
    .UseSqlServer(connectionString)
    .AddInterceptors(new HintCommandInterceptor()));
```

## <a name="reverse-engineering-of-database-views"></a>Reverse Engineering (Zurückentwicklung) von Datenbanksichten

Abfragetypen, die Daten darstellen, die aus der Datenbank gelesen, aber nicht aktualisiert werden können, wurden in [schlüssellose Entitätstypen](xref:core/modeling/keyless-entity-types) umbenannt.
Da sie in den meisten Szenarien ideal für die Zuordnung von Datenbankansichten sind, erstellt EF Core jetzt automatisch schlüssellose Entitätstypen beim Reverse Engineering von Datenbankansichten.

Mit dem [dotnet ef-Befehlszeilenprogramm](xref:core/miscellaneous/cli/dotnet) können Sie z. B. Folgendes eingeben:

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

Das Tool erstellt dann automatisch einen Gerüsttyp für Ansichten und Tabellen ohne Schlüssel:

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Names>(entity =>
    {
        entity.HasNoKey();
        entity.ToView("Names");
    });

    modelBuilder.Entity<Things>(entity =>
    {
        entity.HasNoKey();
    });
}
```

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>Abhängige Entitäten, die die Tabelle gemeinsam mit dem Prinzipal verwenden, sind jetzt optional

Ab EF Core 3.0 gibt es die Möglichkeit, wenn `OrderDetails` im Besitz von `Order` ist oder explizit derselben Tabelle zugeordnet ist, dass `Order` ohne `OrderDetails` hinzugefügt werden kann, und alle `OrderDetails`-Eigenschaften, mit Ausnahme des primären Schlüssels, werden Spalten zugeordnet, die NULL-Werte zulassen.

Bei Abfragen von EF Core wird `OrderDetails` auf `null` festgelegt, wenn eine der erforderlichen Eigenschaften keinen Wert aufweist oder keine erforderlichen Eigenschaften außer dem primären Schlüssel vorhanden und alle Eigenschaften `null` sind.

``` csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

## <a name="ef-63-on-net-core"></a>EF 6.3 unter .NET Core

Dies ist nicht wirklich eine EF Core 3.0-Funktion, sie ist aber wichtig für viele unserer derzeitigen Kunden.

Uns ist bewusst, dass viele bestehende Anwendungen frühere Versionen von EF verwenden und dass die Portierung auf EF Core nur zur Nutzung von .NET Core unter Umständen mit einem erheblichen Aufwand verbunden ist.
Aus diesem Grund haben wir die neueste Version von EF 6 portiert, sodass sie unter .NET Core 3.0 ausgeführt werden kann.

Weitere Informationen finden Sie im [Artikel zu Neuerungen in EF 6](xref:ef6/what-is-new/index).

## <a name="postponed-features"></a>Verschobene Features

Einige Features, die ursprünglich für EF Core 3.0 geplant waren, wurden auf zukünftige Versionen verschoben:

- Die Fähigkeit, Teile eines Modells in Migrationen zu ignorieren, nachverfolgt als [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).
- Entitäten von Eigenschaftenbehältern, die in zwei separaten Problemen nachverfolgt werden: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) über Entitäten von gemeinsam genutzten Typen und [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) über Unterstützung der Zuordnung von indizierten Eigenschaften.
