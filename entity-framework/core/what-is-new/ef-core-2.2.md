---
title: Neue Features in EF Core 2.2 – EF Core
description: Änderungen und Verbesserungen in Entity Framework Core 2.2
author: ajcvickers
ms.date: 11/14/2018
uid: core/what-is-new/ef-core-2.2
ms.openlocfilehash: ca71c7479254b25fe932e6abf43fe0fd8f1781b3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065692"
---
# <a name="new-features-in-ef-core-22"></a>Neue Features in EF Core 2.2

## <a name="spatial-data-support"></a>Unterstützung räumlicher Daten

Räumliche Daten können verwendet werden, um die physische Position und die Form von Objekten darzustellen.
Viele Datenbanken können räumliche Daten nativ speichern, indizieren und abfragen.
Häufige Szenarien sind das Abfragen von Objekten mit einer bestimmten Distanz und das Testen, ob ein Polygon eine bestimmte Position enthält.
EF Core 2.2 unterstützt nun das Arbeiten mit räumlichen Daten aus verschiedenen Datenbanken mit Typen aus der [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite)-Bibliothek (NTS-Bibliothek).

Die Unterstützung räumlicher Daten wird als eine Reihe von anbieterspezifischen Erweiterungspaketen implementiert.
Jedes dieser Pakete stellt Mappings für NTS-Typen und -Methoden sowie die entsprechenden räumlichen Typen und Funktionen in der Datenbank bereit.
Diese Anbietererweiterungen sind nun für [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/), [SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) und [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/) (aus dem [Npgsql-Projekt](https://www.npgsql.org/)) verfügbar.
Räumliche Typen können direkt mit dem [EF Core-In-Memory-Anbieter](xref:core/providers/in-memory/index) ohne zusätzliche Erweiterungen verwendet werden.

Wenn die Anbietererweiterung installiert ist, können Sie Ihren Entitäten Eigenschaften der unterstützten Typen hinzufügen. Beispiel:

```csharp
using NetTopologySuite.Geometries;

namespace MyApp
{
  public class Friend
  {
    [Key]
    public string Name { get; set; }

    [Required]
    public Point Location { get; set; }
  }
}
```

Dann können Entitäten neben räumlichen Daten bestehen:

```csharp
using (var context = new MyDbContext())
{
    context.Add(
        new Friend
        {
            Name = "Bill",
            Location = new Point(-122.34877, 47.6233355) {SRID = 4326 }
        });
    context.SaveChanges();
}
```

Außerdem können Sie Datenbankabfragen auf Grundlage von räumlichen Daten und Vorgängen ausführen:

```csharp
var nearestFriends =
    (from f in context.Friends
    orderby f.Location.Distance(myLocation) descending
    select f).Take(5).ToList();
```

Weitere Informationen zu diesem Feature finden Sie in der [Dokumentation über räumliche Typen](xref:core/modeling/spatial).

## <a name="collections-of-owned-entities"></a>Sammlungen von nicht eigenständigen Entitäten

EF Core 2.0 bietet nun die Option, die Eigentümerschaft in 1:1-Zuordnungen zu modellieren.
EF Core 2.2 erweitert die Funktion, die Eigentümerschaft in 1:n-Zuordnungen auszudrücken.
Besitzer können einschränken, wie Entitäten verwendet werden.

Nicht eigenständige Entitäten:

- Können immer nur in Navigationseigenschaften anderer Entitätstypen angezeigt werden
- Werden automatisch geladen und können nur von einem „DbContext“-Objekt vom Besitzer verfolgt werden

In relationalen Datenbanken werden Sammlungen mit Besitzern separaten Tabellen des Besitzers zugeordnet, wie normale 1:n-Zuordnungen.
Für dokumentorientierte Datenbanken planen wir jedoch eine Schachtelung nicht eigenständiger Entitäten (in nicht eigenständigen Sammlungen oder Referenzen) innerhalb des dem Besitzer zugeordneten Dokuments.

Sie können die Funktion verwenden, indem Sie die neue OwnsMany()-API aufrufen:

```csharp
modelBuilder.Entity<Customer>().OwnsMany(c => c.Addresses);
```

Weitere Informationen finden Sie in der [aktualisierten Dokumentation über nicht eigenständige Entitäten](xref:core/modeling/owned-entities#collections-of-owned-types).

## <a name="query-tags"></a>Abfragetags

Diese Funktion vereinfacht das Korrelieren zwischen LINQ-Abfragen im Code und generierten SQL-Abfragen, die in Protokollen erfasst werden.

Kommentieren Sie eine LINQ-Abfrage mit der neuen TagWith()-Methode, um Abfragetags zu verwenden.
So sieht die Verwendung der Abfrage nach räumlichen Daten aus einem vorherigen Beispiel aus:

```csharp
var nearestFriends =
    (from f in context.Friends.TagWith(@"This is my spatial query!")
    orderby f.Location.Distance(myLocation) descending
    select f).Take(5).ToList();
```

Diese LINQ-Abfrage erzeugt die folgende SQL-Ausgabe:

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

Weitere Informationen finden Sie in der [Dokumentation über Abfragetags](xref:core/querying/tags).
