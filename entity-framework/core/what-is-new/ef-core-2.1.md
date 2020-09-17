---
title: Neue Features in EF Core 2.1 – EF Core
description: Änderungen und Verbesserungen in Entity Framework Core 2.1
author: divega
ms.date: 02/20/2018
uid: core/what-is-new/ef-core-2.1
ms.openlocfilehash: b3d44fe344155df1d814e189b533010673754089
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072316"
---
# <a name="new-features-in-ef-core-21"></a>Neue Features in EF Core 2.1

Neben zahlreichen Fehlerbehebungen und kleinen Funktions- und Leistungsverbesserungen umfasst EF Core 2.1 einige interessante neue Features:

## <a name="lazy-loading"></a>Lazy Loading

EF Core enthält nun die erforderlichen Bausteine für Benutzer, die Entitätsklassen zum bedarfsgesteuerten Laden ihrer Navigationseigenschaften erstellen möchten. Wir haben zudem das neue Paket „Microsoft.EntityFrameworkCore.Proxies“ erstellt, das mithilfe dieser Bausteine Proxyklassen zum Lazy Loading auf Grundlage minimal geänderter Entitätsklassen (z.B. Klassen mit virtuellen Navigationseigenschaften) erzeugt.

Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zum Lazy Loading](xref:core/querying/related-data#lazy-loading).

## <a name="parameters-in-entity-constructors"></a>Parameter in Entitätskonstruktoren

Als einer der erforderlichen Bausteine zum Lazy Loading haben wir die Erstellung von Entitäten ermöglicht, die Parameter in ihren Konstruktoren akzeptieren. Sie können mithilfe von Parametern Eigenschaftswerte, Delegaten zum Lazy Loading und Dienste einfügen.

Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zum Entitätskonstruktor mit Parametern](xref:core/modeling/constructors).

## <a name="value-conversions"></a>Wertkonvertierungen

Bislang konnte EF Core nur Eigenschaften von Typen zuordnen, die vom zugrunde liegenden Datenbankanbieter nativ unterstützt wurden. Werte wurden zwischen Spalten und Eigenschaften ohne jegliche Transformation hin und her kopiert. Ab EF Core 2.1 können Wertkonvertierungen angewendet werden, um die aus Spalten abgerufenen Werte vor der Anwendung auf Eigenschaften zu transformieren (und umgekehrt). Wir verfügen über eine Reihe von Konvertierungen, die der Konvention entsprechend bei Bedarf angewendet werden können, sowie über eine explizite Konfigurations-API, die die Registrierung von benutzerdefinierten Konvertierungen zwischen Spalten und Eigenschaften ermöglicht. Einige Anwendungsgebiete für dieses Feature sind z.B. Folgende:

- Speichern von Enumerationen als Zeichenfolgen
- Zuordnen von ganzen Zahlen ohne Vorzeichen mit SQL Server
- Automatische Ver- und Entschlüsselung von Eigenschaftswerten

Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zu Wertkonvertierungen](xref:core/modeling/value-conversions).  

## <a name="linq-groupby-translation"></a>LINQ-GroupBy-Übersetzung

Vor Version 2.1 wurde der LINQ-Operator „GroupBy“ in EF Core immer im Arbeitsspeicher ausgewertet. Nun bieten wir in den meisten Fällen Unterstützung für dessen Übersetzung in die GROUP BY-SQL-Klausel.

Dieses Beispiel zeigt eine Abfrage mit GroupBy zum Berechnen verschiedener Aggregatfunktionen:

``` csharp
var query = context.Orders
    .GroupBy(o => new { o.CustomerId, o.EmployeeId })
    .Select(g => new
        {
          g.Key.CustomerId,
          g.Key.EmployeeId,
          Sum = g.Sum(o => o.Amount),
          Min = g.Min(o => o.Amount),
          Max = g.Max(o => o.Amount),
          Avg = g.Average(o => o.Amount)
        });
```

Die entsprechende SQL-Übersetzung sieht wie folgt aus:

``` SQL
SELECT [o].[CustomerId], [o].[EmployeeId],
    SUM([o].[Amount]), MIN([o].[Amount]), MAX([o].[Amount]), AVG([o].[Amount])
FROM [Orders] AS [o]
GROUP BY [o].[CustomerId], [o].[EmployeeId];
```

## <a name="data-seeding"></a>Datenseeding

Mit dem neuen Release ist es möglich, die ursprünglichen Daten zum Auffüllen einer Datenbank anzugeben. Im Gegensatz zu EF 6 wird das Datenseeding mit einem Entitätstyp als Teil der Modellkonfiguration verknüpft. EF Core-Migrationen können dann automatisch berechnen, welche Einfüge-, Aktualisierungs- oder Löschvorgänge beim Durchführen eines Upgrades für die Datenbank auf eine neue Modellversion angewendet werden müssen.

Beispielsweise können Sie damit Seeddaten für ein Post-Element in `OnModelCreating` konfigurieren:

``` csharp
modelBuilder.Entity<Post>().HasData(new Post{ Id = 1, Text = "Hello World!" });
```

Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zum Datenseeding](xref:core/modeling/data-seeding).  

## <a name="query-types"></a>Abfragetypen

Ein EF Core-Modell kann nun Abfragetypen einschließen. Im Gegensatz zu Entitätstypen werden für Abfragetypen keine Schlüssel definiert, und sie können nicht eingefügt, gelöscht oder aktualisiert werden (d.h., sie sind schreibgeschützt). Sie können jedoch direkt von Abfragen zurückgegeben werden. Einige Verwendungsszenarien für Abfragetypen sind z.B. Folgende:

- Zuordnen mit Ansichten ohne Primärschlüssel
- Zuordnen mit Tabellen ohne Primärschlüssel
- Zuordnen mit im Modell definierten Abfragen
- Verwendung als Rückgabetyp für `FromSql()`-Abfragen

Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zu Abfragetypen](xref:core/modeling/keyless-entity-types).

## <a name="include-for-derived-types"></a>Einschließen für abgeleitete Typen

Nun ist es möglich, beim Schreiben von Ausdrücken für die `Include`-Methode nur für abgeleitete Typen definierte Navigationseigenschaften anzugeben. Für die stark typisierte `Include`-Version unterstützen wir entweder die Verwendung einer expliziten Umwandlung oder des `as`-Operators. Nun bieten wir auch Unterstützung für den Verweis der für abgeleitete Typen definierten Namen der Navigationseigenschaften in der Zeichenfolgenversion `Include`:

``` csharp
var option1 = context.People.Include(p => ((Student)p).School);
var option2 = context.People.Include(p => (p as Student).School);
var option3 = context.People.Include("School");
```

Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zum Einschließen mit abgeleiteten Typen](xref:core/querying/related-data#include-on-derived-types).

## <a name="systemtransactions-support"></a>System.Transactions-Unterstützung

Wir haben die Möglichkeit zum Arbeiten mit System.Transactions-Features wie TransactionScope hinzugefügt. Dieses funktioniert in .NET Framework und .NET Core bei der Verwendung von Datenbankanbietern, die Unterstützung hierfür bieten.

Weitere Informationen zu diesem Thema finden Sie im [Abschnitt zu System.Transactions](xref:core/saving/transactions#using-systemtransactions).

## <a name="better-column-ordering-in-initial-migration"></a>Bessere Spaltensortierung bei der anfänglichen Migration

Basierend auf dem Feedback von Kunden haben wir Migrationen dahingehend aktualisiert, dass Spalten für Tabellen zunächst in der gleichen Reihenfolge wie die Deklaration von Eigenschaften in Klassen generiert werden. Beachten Sie, dass EF Core nicht die Reihenfolge ändern kann, wenn nach der Erstellung der ersten Tabelle neue Member hinzugefügt werden.

## <a name="optimization-of-correlated-subqueries"></a>Optimierung von korrelierten Unterabfragen

Wir haben unsere Abfrageübersetzung dahingehend verbessert, dass die Ausführung von „N+1“-SQL-Abfragen in vielen gängigen Szenarien verhindert wird, in denen die Verwendung einer Navigationseigenschaft in der Projektion zum Verknüpfen von Daten aus der Stammabfrage mit Daten aus einer korrelierten Unterabfrage führt. Für die Optimierung ist das Puffern der Ergebnisse aus der Unterabfrage erforderlich. Zudem ist es erforderlich, die Abfrage zum Auswählen des neuen Verhaltens zu ändern.

Die folgende Abfrage wird z.B. normalerweise in eine Abfrage für Kunden plus N separate Abfragen für Aufträge übersetzt („N“ steht hierbei für die Anzahl der zurückgegebenen Kunden):

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount));
```

Durch Einschließen von `ToList()` an der richtigen Stelle geben Sie an, dass eine Pufferung für die Aufträge, die die Optimierung ermöglichen, angemessen ist:

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount).ToList());
```

Beachten Sie, dass diese Abfrage nur in zwei SQL-Abfragen übersetzt wird: eine für Kunden und die nächste für Aufträge.

## <a name="owned-attribute"></a>Das [Owned]-Attribut

Nun können [Entitätstypen im Besitz](xref:core/modeling/owned-entities) konfiguriert werden, indem einfach der Typ mit `[Owned]` kommentiert und dann sichergestellt wird, dass die Besitzerentität zum Modell hinzugefügt wird:

``` csharp
[Owned]
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}
```

## <a name="command-line-tool-dotnet-ef-included-in-net-core-sdk"></a>dotnet-ef-Befehle des Befehlszeilentools im .NET Core SDK

Die _dotnet-ef_-Befehle sind nun im .NET Core SDK enthalten. Deshalb ist es nicht mehr erforderlich, „DotNetCliToolReference“ für Migrationen oder den DbContext-Gerüstbau aus einer vorhandenen Datenbank im Projekt zu verwenden.

Weitere Details zum Aktivieren der Befehlszeilentools für verschiedene Versionen des .NET Core SDK und von EF Core finden Sie im Abschnitt zur [Installation der Tools](xref:core/miscellaneous/cli/dotnet#installing-the-tools).

## <a name="microsoftentityframeworkcoreabstractions-package"></a>Paket „Microsoft.EntityFrameworkCore.Abstractions“

Das neue Paket enthält Attribute und Schnittstellen, mit denen Sie EF Core-Features in Ihren Projekten aktivieren können, ohne eine Abhängigkeit von EF Core insgesamt einzugehen. Das [Owned]-Attribut und die ILazyLoader-Schnittstelle sind beispielsweise enthalten.

## <a name="state-change-events"></a>Zustandsänderungsereignisse

Die neuen Ereignisse `Tracked` und `StateChanged` für `ChangeTracker` können verwendet werden, um Logik zur Reaktion auf Entitäten zu schreiben, die in den DbContext wechseln oder ihren Zustand ändern.

## <a name="raw-sql-parameter-analyzer"></a>Raw-SQL-Parameteranalyse

EF Core umfasst ein neues Tool für die Codeanalyse, mit der potenziell unsichere Verwendungen unserer Raw-SQL-APIs erkannt werden, z.B. `FromSql` oder `ExecuteSqlCommand`. Für die folgende Abfrage wird beispielweise eine Warnung angezeigt, weil _minAge_ nicht parametrisiert ist:

``` csharp
var sql = $"SELECT * FROM People WHERE Age > {minAge}";
var query = context.People.FromSql(sql);
```

## <a name="database-provider-compatibility"></a>Kompatibilität mit Datenbankanbietern

Es wird empfohlen, EF Core 2.1 mit Anbietern zu verwenden, die ein Update auf EF Core 2.1 durchgeführt oder EF Core 2.1 bereits getestet haben.

> [!TIP]
> Falls unerwarteterweise keine Kompatibilität besteht oder Probleme mit den neuen Features auftreten, oder falls Sie uns Ihre Feedback mitteilen möchten, melden Sie uns dies über [unsere Problemverfolgung](https://github.com/aspnet/EntityFrameworkCore/issues/new).
