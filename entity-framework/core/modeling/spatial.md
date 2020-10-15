---
title: Räumliche Daten-EF Core
description: Verwenden räumlicher Daten in einem Entity Framework Core Modell
author: bricelam
ms.date: 10/02/2020
uid: core/modeling/spatial
ms.openlocfilehash: 2c0cd7a8acf7e4b58eadf8805afa1fe4a1d6e949
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063932"
---
# <a name="spatial-data"></a>Räumliche Daten

> [!NOTE]
> Diese Funktion wurde in EF Core 2,2 hinzugefügt.

Räumliche Daten repräsentieren den physischen Speicherort und die Form von Objekten. Viele Datenbanken unterstützen diese Art von Daten, damit Sie zusammen mit anderen Daten indiziert und abgefragt werden können. Häufige Szenarien umfassen das Abfragen von Objekten in einer bestimmten Entfernung von einem Speicherort oder das Auswählen des Objekts, dessen Rahmen eine bestimmte Position enthält. EF Core unterstützt die Zuordnung räumlicher Datentypen mithilfe der räumlichen Bibliothek nettopologysuite.

## <a name="installing"></a>Installation

Um räumliche Daten mit EF Core verwenden zu können, müssen Sie das entsprechende unterstützende nuget-Paket installieren. Welches Paket installiert werden muss, hängt vom verwendeten Anbieter ab.

EF Core Anbieter                        | Räumliches nuget-Paket
--------------------------------------- | ---------------------
Microsoft.EntityFrameworkCore.SqlServer | [Microsoft. entityframeworkcore. SqlServer. nettopologysuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
Microsoft.EntityFrameworkCore.Sqlite    | [Microsoft. entityframeworkcore. sqlite. nettopologysuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
Microsoft.EntityFrameworkCore.InMemory  | [Nettopologysuite](https://www.nuget.org/packages/NetTopologySuite)
Npgsql.EntityFrameworkCore.PostgreSQL   | [Npgsql. entityframeworkcore. PostgreSQL. nettopologysuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)
Pomelo.EntityFrameworkCore.MySql        | [Pomelo. entityframeworkcore. MySQL. nettopologysuite](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite)
Devart.Data.MySql.EFCore                | [Devart. Data. MySQL. efcore. nettopologysuite](https://www.nuget.org/packages/Devart.Data.MySql.EFCore.NetTopologySuite)
Devart.Data.PostgreSql.EFCore           | [Devart. Data. PostgreSQL. efcore. nettopologysuite](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore.NetTopologySuite)
Devart.Data.SQLite.EFCore               | [Devart. Data. sqlite. efcore. nettopologysuite](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore.NetTopologySuite)
Teradata.EntityFrameworkCore            | [Teradata. entityframeworkcore. nettopologysuite](https://www.nuget.org/packages/Teradata.EntityFrameworkCore.NetTopologySuite)

## <a name="nettopologysuite"></a>Nettopologysuite

[Nettopologysuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) ist eine räumliche Bibliothek für .net. EF Core ermöglicht die Zuordnung von räumlichen Datentypen in der Datenbank mithilfe von NTS-Typen in Ihrem Modell.

Um die Zuordnung räumlicher Typen über NTS zu ermöglichen, müssen Sie die usenettopologysuite-Methode für den dbcontext Options-Generator des Anbieters abrufen. Beispielsweise können Sie mit SQL Server wie folgt bezeichnen.

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/WideWorldImportersContext.cs?name=snippet_UseNetTopologySuite)]

Es gibt mehrere räumliche Datentypen. Welcher Typ Sie verwenden, hängt von den Formen der Formen ab, die Sie zulassen möchten. Hier ist die Hierarchie der NTS-Typen, die Sie für Eigenschaften im Modell verwenden können. Sie befinden sich im- `NetTopologySuite.Geometries` Namespace.

* Geometrie
  * Point
  * LineString
  * Polygon
  * GeometryCollection
    * MultiPoint
    * MultiLineString
    * MultiPolygon

> [!WARNING]
> Circularstring, compoundcurve und curepolygon werden von NTS nicht unterstützt.

Mit dem basisgeometry-Typ kann jeder Typ von Form von der-Eigenschaft angegeben werden.

## <a name="longitude-and-latitude"></a>Längen-und Breitengrad

Die Koordinaten in den NTS sind X-und Y-Werte. Um Längen-und Breitengrad darzustellen, verwenden Sie X für Längengrad und Y für Breitengrad. Beachten Sie, dass **backwards** dies nicht das `latitude, longitude` Format hat, in dem normalerweise diese Werte angezeigt werden.

## <a name="querying-data"></a>Abfrage von Daten

Die folgenden Entitäts Klassen können verwendet werden, um Tabellen in der [Beispieldatenbank Wide World Importern](https://go.microsoft.com/fwlink/?LinkID=800630)zuzuordnen.

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/City.cs?name=snippet_City)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/Country.cs?name=snippet_Country)]

In LINQ werden die NTS-Methoden und-Eigenschaften, die als Datenbankfunktionen verfügbar sind, in SQL übersetzt. Beispielsweise werden die Distance-Methode und die-Methode in den folgenden Abfragen übersetzt. Informationen zu den unterstützten Methoden finden Sie in der Dokumentation des Anbieters.

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Distance)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Contains)]

## <a name="reverse-engineering"></a>Reverse Engineering

Die räumlichen nuget-Pakete ermöglichen auch die [Reverse Engineering](xref:core/managing-schemas/scaffolding) von Modellen mit räumlichen Eigenschaften. Sie müssen das Paket jedoch ***vor*** dem Ausführen von `Scaffold-DbContext` oder installieren `dotnet ef dbcontext scaffold` . Wenn Sie dies nicht tun, erhalten Sie Warnungen, wenn Sie keine Typzuordnungen für die Spalten finden und die Spalten übersprungen werden.

## <a name="srid-ignored-during-client-operations"></a>SRID wird bei Client Vorgängen ignoriert.

NTS ignoriert SRID-Werte während des Vorgangs. Es wird von einem planaren Koordinatensystem ausgegangen. Dies bedeutet Folgendes: Wenn Sie Koordinaten in Bezug auf Längengrad und Breitengrad angeben, werden einige vom Client ausgewertete Werte wie "Distance", "length" und "Area" in Grad und nicht in "Meter" angegeben. Wenn Sie aussagekräftigere Werte benötigen, müssen Sie die Koordinaten zunächst mithilfe einer Bibliothek wie [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) auf ein anderes Koordinatensystem projizieren, bevor Sie diese Werte berechnen.

Wenn ein Vorgang vom Server durch EF Core über SQL ausgewertet wird, wird die Einheit des Ergebnisses von der Datenbank bestimmt.

Im folgenden finden Sie ein Beispiel für die Verwendung von ProjNet4GeoAPI, um den Abstand zwischen zwei Städten zu berechnen.

[!code-csharp[](../../../samples/core/Spatial/Projections/GeometryExtensions.cs?name=snippet_GeometryExtensions)]

[!code-csharp[](../../../samples/core/Spatial/Projections/Program.cs?name=snippet_ProjectTo)]

> [!NOTE]
> 4326 bezieht sich auf WGS 84, einen in GPS und anderen geografischen Systemen verwendeten Standard.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Lesen Sie unbedingt die Dokumentation Ihres Anbieters, um weitere Informationen zum Arbeiten mit räumlichen Daten zu erhalten.

* [Räumliche Daten im SQL Server-Anbieter](xref:core/providers/sql-server/spatial)
* [Räumliche Daten im SQLite-Anbieter](xref:core/providers/sqlite/spatial)
* [Räumliche Daten im npgsql-Anbieter](https://www.npgsql.org/efcore/mapping/nts.html)
* [Nettopologysuite-Dokumentation](https://nettopologysuite.github.io/NetTopologySuite/)
