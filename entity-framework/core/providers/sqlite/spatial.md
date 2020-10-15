---
title: 'SQLite-Datenbankanbieter: räumliche Daten-EF Core'
description: Verwenden räumlicher Daten mit dem Entity Framework Core SQLite-Datenbankanbieter
author: bricelam
ms.date: 10/02/2020
uid: core/providers/sqlite/spatial
ms.openlocfilehash: 3296955dc046b91b53a1dcb09c51b340bc853b4a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066526"
---
# <a name="spatial-data-in-the-sqlite-ef-core-provider"></a>Räumliche Daten im SQLite-EF Core-Anbieter

Diese Seite enthält zusätzliche Informationen zur Verwendung räumlicher Daten mit dem SQLite-Datenbankanbieter. Allgemeine Informationen zur Verwendung räumlicher Daten in EF Core finden Sie in der Haupt Dokumentation zu [räumlichen Daten](xref:core/modeling/spatial) .

## <a name="installing-spatialite"></a>Installieren von spatialite

Unter Windows wird die native mod_spatialite Bibliothek als eine nuget-Paketabhängigkeit verteilt. Andere Plattformen müssen Sie separat installieren. Dies erfolgt in der Regel mithilfe eines Softwarepaket-Managers. Beispielsweise können Sie apt für Debian und Ubuntu verwenden. und Homebrew unter MacOS.

```bash
# Debian/Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

Leider sind neuere Versionen von proj (eine Abhängigkeit von spatialite) mit dem standardmäßigen [sqlitepclraw-Bundle](/dotnet/standard/data/sqlite/custom-versions#bundles)von EF nicht kompatibel. Sie können dieses Problem umgehen, indem Sie stattdessen die System SQLite-Bibliothek verwenden.

```xml
<ItemGroup>
  <!-- Use bundle_sqlite3 instead with SpatiaLite on macOS and Linux -->
  <!--<PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="3.1.0" />-->
  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.Core" Version="3.1.0" />
  <PackageReference Include="SQLitePCLRaw.bundle_sqlite3" Version="2.0.4" />

  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite" Version="3.1.0" />
</ItemGroup>
```

Unter **macOS**müssen Sie auch eine Umgebungsvariable festlegen, bevor Sie Ihre APP ausführen, sodass Sie die Version von SQLite von Homebrew verwendet. In Visual Studio für Mac können Sie diese **Option Unterprojekt > Projektoptionen > > Konfigurationen ausführen > Standard** Wert festlegen.

```bash
DYLD_LIBRARY_PATH=/usr/local/opt/sqlite/lib
```

## <a name="configuring-srid"></a>Konfigurieren von SRID

In spatialite müssen Spalten eine SRID pro Spalte angeben. Der Standard-SRID ist `0` . Geben Sie einen anderen SRID mithilfe der forsqlitehassrid-Methode an.

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

> [!NOTE]
> 4326 bezieht sich auf WGS 84, einen in GPS und anderen geografischen Systemen verwendeten Standard.

## <a name="dimension"></a>Dimension

Die Standard Dimension (oder Ordinate) einer Spalte ist X und Y. Konfigurieren Sie den Spaltentyp, um zusätzliche Ordinate wie Z oder M zu aktivieren.

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasColumnType("POINTZ");
```

## <a name="spatial-function-mappings"></a>Zuordnungen für räumliche Funktionen

Diese Tabelle zeigt, welche [nettopologysuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS)-Member in welche SQL-Funktionen übersetzt werden.

.NET                                        | SQL
------------------------------------------- | ---
et. Feld                               | Bereich ( @geometry )
et. AsBinary ()                         | AsBinary ( @geometry )
et. AsText ()                           | AsText ( @geometry )
et. Begrenzungs                           | Grenze ( @geometry )
et. Puffer (Entfernung)                   | Puffer ( @geometry , @distance )
et. Buffer (Distance, quadrantsegments) | Puffer ( @geometry , @distance , @quadrantSegments )
et. Schwerpunkt                           | Centroid ( @geometry )
et. Enthält (g)                        | Enthält ( @geometry , @g )
et. "Anvexhull ()"                       | "Anvexhull ( @geometry )"
et. Coveredby (g)                       | Coveredby ( @geometry , @g )
et. Cover (g)                          | Deckt ( @geometry , @g )
et. Kreuze (g)                         | Kreuze ( @geometry , @g )
et. Differenz (sonstige)                  | Differenz ( @geometry , @other )
et. Maß                          | Dimension ( @geometry )
et. Disjoint (g)                        | Disjoint ( @geometry , @g )
et. Abstand (g)                        | Abstand ( @geometry , @g )
et. Versendet                           | Umschlag ( @geometry )
et. Equalstopologisch (g)             | Ist gleich( @geometry , @g )
et. Geometrytype                       | Geometrytype ( @geometry )
et. Getgeometryn (n)                    | Geometryn ( @geometry , @n + 1)
et. Interiorpoint                      | Pointonsurface ( @geometry )
et. Schnittmenge (sonstige)                | Schnittmenge ( @geometry , @other )
et. Schnittpunkte (g)                      | Überschneidet ( @geometry , @g )
et. IsEmpty                            | IsEmpty ( @geometry )
et. IsSimple                           | IsSimple ( @geometry )
et. IsValid                            | IsValid ( @geometry )
et. Iswithindistance (Geom, Distance)   | Distance ( @geometry , @geom ) <= @distance
et. Füll                             | GLength ( @geometry )
et. Numgeometries                      | Numgeometries ( @geometry )
et. NumPoints                          | NumPoints ( @geometry )
et. Ogcgeometrytype                    | Fall geometrytype ( @geometry ), wenn ' Point ' dann 1... Schließlich
et. Überlappungen (g)                        | Überlappung ( @geometry , @g )
et. Pointonsurface                     | Pointonsurface ( @geometry )
et. Beziehung (g, intersectionpattern)     | Beziehung ( @geometry , @g , @intersectionPattern )
et. Umkehren ()                          | ST_Reverse ( @geometry )
et. SRID                               | SRID ( @geometry )
et. Symmetricdifference (sonstige)         | Symdifference ( @geometry , @other )
et. "$ Binary ()"                         | AsBinary ( @geometry )
et. "-Text ()"                           | AsText ( @geometry )
et. Berührungen (g)                         | Berührt ( @geometry , @g )
et. Union ()                            | Unaryunion ( @geometry )
et. Union (sonstige)                       | Gunion ( @geometry , @other )
et. In (g)                          | In ( @geometry , @g )
GeometryCollection [i]                       | Geometryn ( @geometryCollection , @i + 1)
GeometryCollection. Count                    | Numgeometries ( @geometryCollection )
LineString. Count                            | NumPoints ( @lineString )
LineString. EndPoint                         | Endpunkt ( @lineString )
LineString. getpointn (n)                     | Pointn ( @lineString , @n + 1)
LineString. IsClosed                         | IsClosed ( @lineString )
LineString. isring                           | Isring ( @lineString )
LineString. StartPoint                       | StartPoint ( @lineString )
MultiLineString. IsClosed                    | IsClosed ( @multiLineString )
Punkt. 800                                     | M ( @point )
Punkt. Stuben                                     | X ( @point )
Punkt. Teenie                                     | J ( @point )
Punkt. Z                                     | Z ( @point )
Polygon. ExteriorRing                        | ExteriorRing ( @polygon )
Polygon. Getinteriorringn (n)                 | Interiorringn ( @polygon , @n + 1)
Polygon. Numinteriorrings                    | Numinteriorring ( @polygon )

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Spatialite-Homepage](https://www.gaia-gis.it/fossil/libspatialite)
* [Nettopologysuite-Dokumentation](https://nettopologysuite.github.io/NetTopologySuite/)
