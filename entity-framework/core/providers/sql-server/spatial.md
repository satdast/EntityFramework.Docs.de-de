---
title: 'Microsoft SQL Server Datenbankanbieter: räumliche Daten-EF Core'
description: Verwenden räumlicher Daten mit dem Entity Framework Core Microsoft SQL Server-Datenbankanbieter
author: bricelam
ms.date: 10/02/2020
uid: core/providers/sql-server/spatial
ms.openlocfilehash: 1356d2d86a497f01c4eacca777a8a260f33c0e9b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066530"
---
# <a name="spatial-data-in-the-sql-server-ef-core-provider"></a>Räumliche Daten im SQL Server EF Core-Anbieter

Diese Seite enthält zusätzliche Informationen zur Verwendung räumlicher Daten mit dem Microsoft SQL Server Datenbankanbieter. Allgemeine Informationen zur Verwendung räumlicher Daten in EF Core finden Sie in der Haupt Dokumentation zu [räumlichen Daten](xref:core/modeling/spatial) .

## <a name="geography-or-geometry"></a>Geography oder Geometry

Standardmäßig sind räumliche Eigenschaften `geography` Spalten in SQL Server zugeordnet. Konfigurieren Sie `geometry` [den Spaltentyp](xref:core/modeling/entity-properties#column-data-types) in Ihrem Modell, um zu verwenden.

## <a name="geography-polygon-rings"></a>Polygon-Polygon Ringe

Wenn der `geography` Spaltentyp verwendet wird, werden von SQL Server zusätzliche Anforderungen an den äußeren Ring (oder die Shell) und die inneren Ringe (oder Löcher) auferlegt. Der äußere Ring muss gegen den Uhrzeigersinn und die inneren Ringe im Uhrzeigersinn ausgerichtet werden. [Nettopologysuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) überprüft dies, bevor Werte an die Datenbank gesendet werden.

## <a name="fullglobe"></a>FullGlobe

SQL Server weist einen nicht standardmäßigen Geometry-Typ auf, der den vollständigen Globus darstellt, wenn der Spaltentyp verwendet wird `geography` . Außerdem bietet es eine Möglichkeit, Polygone auf der ganzen Welt (ohne äußeren Ring) darzustellen. Keines dieser beiden wird von NTS unterstützt.

> [!WARNING]
> Fullglobe und Polygone, die darauf basieren, werden von NTS nicht unterstützt.

## <a name="curves"></a>Kurven

Wie in der Dokumentation zu den wichtigsten [räumlichen Daten](xref:core/modeling/spatial) erwähnt, können derzeit keine Kurven dargestellt werden. Dies bedeutet, dass Sie die Werte circularstring, compoundcurve und curepolygon mithilfe der [stcurrvetoline](/sql/t-sql/spatial-geography/stcurvetoline-geography-data-type) -Methode transformieren müssen, bevor Sie Sie in EF Core verwenden.

> [!WARNING]
> Circularstring, compoundcurve und curepolygon werden von NTS nicht unterstützt.

## <a name="spatial-function-mappings"></a>Zuordnungen für räumliche Funktionen

Diese Tabelle zeigt, welche NTS-Member in welche SQL-Funktionen übersetzt werden. Beachten Sie, dass die Übersetzungen variieren, je nachdem, ob die Spalte vom Typ geography oder Geometry ist.

.NET                                      | SQL (geography)                                              | SQL (Geometrie)
----------------------------------------- | ------------------------------------------------------------ | --------------
et. Feld                             | @geometry.STArea()                                           | @geometry.STArea()
et. AsBinary ()                       | @geometry.STAsBinary()                                       | @geometry.STAsBinary()
et. AsText ()                         | @geometry.AsTextZM()                                         | @geometry.AsTextZM()
et. Begrenzungs                         |                                                              | @geometry.STBoundary()
et. Puffer (Entfernung)                 | @geometry.STBuffer(@distance)                                | @geometry.STBuffer(@distance)
et. Schwerpunkt                         |                                                              | @geometry.STCentroid()
et. Enthält (g)                      | @geometry.STContains(@g)                                     | @geometry.STContains(@g)
et. "Anvexhull ()"                     | @geometry.STConvexHull()                                     | @geometry.STConvexHull()
et. Kreuze (g)                       |                                                              | @geometry.STCrosses(@g)
et. Differenz (sonstige)                | @geometry.STDifference(@other)                               | @geometry.STDifference(@other)
et. Maß                        | @geometry.STDimension()                                      | @geometry.STDimension()
et. Disjoint (g)                      | @geometry.STDisjoint(@g)                                     | @geometry.STDisjoint(@g)
et. Abstand (g)                      | @geometry.STDistance(@g)                                     | @geometry.STDistance(@g)
et. Versendet                         |                                                              | @geometry.STEnvelope()
et. Equalstopologisch (g)           | @geometry.STEquals(@g)                                       | @geometry.STEquals(@g)
et. Geometrytype                     | @geometry.STGeometryType()                                   | @geometry.STGeometryType()
et. Getgeometryn (n)                  | @geometry.STGeometryN( @n + 1)                                | @geometry.STGeometryN( @n + 1)
et. Interiorpoint                    |                                                              | @geometry.STPointOnSurface()
et. Schnittmenge (sonstige)              | @geometry.STIntersection(@other)                             | @geometry.STIntersection(@other)
et. Schnittpunkte (g)                    | @geometry.STIntersects(@g)                                   | @geometry.STIntersects(@g)
et. IsEmpty                          | @geometry.STIsEmpty()                                        | @geometry.STIsEmpty()
et. IsSimple                         |                                                              | @geometry.STIsSimple()
et. IsValid                          | @geometry.STIsValid()                                        | @geometry.STIsValid()
et. Iswithindistance (Geom, Distance) | @geometry.STDistance( @geom ) <= @distance                     | @geometry.STDistance( @geom ) <= @distance
et. Füll                           | @geometry.STLength()                                         | @geometry.STLength()
et. Numgeometries                    | @geometry.STNumGeometries()                                  | @geometry.STNumGeometries()
et. NumPoints                        | @geometry.STNumPoints()                                      | @geometry.STNumPoints()
et. Ogcgeometrytype                  | Case @geometry.STGeometryType () bei n ' Point ' then 1... Schließlich | Case @geometry.STGeometryType () bei n ' Point ' then 1... Schließlich
et. Überlappungen (g)                      | @geometry.STOverlaps(@g)                                     | @geometry.STOverlaps(@g)
et. Pointonsurface                   |                                                              | @geometry.STPointOnSurface()
et. Beziehung (g, intersectionpattern)   |                                                              | @geometry.STRelate(@g, @intersectionPattern)
et. SRID                             | @geometry.STSrid                                             | @geometry.STSrid
et. Symmetricdifference (sonstige)       | @geometry.STSymDifference(@other)                            | @geometry.STSymDifference(@other)
et. "$ Binary ()"                       | @geometry.STAsBinary()                                       | @geometry.STAsBinary()
et. "-Text ()"                         | @geometry.AsTextZM()                                         | @geometry.AsTextZM()
et. Berührungen (g)                       |                                                              | @geometry.STTouches(@g)
et. Union (sonstige)                     | @geometry.STUnion(@other)                                    | @geometry.STUnion(@other)
et. In (g)                        | @geometry.STWithin(@g)                                       | @geometry.STWithin(@g)
GeometryCollection [i]                     | @geometryCollection.STGeometryN( @i + 1)                      | @geometryCollection.STGeometryN( @i + 1)
GeometryCollection. Count                  | @geometryCollection.STNumGeometries()                        | @geometryCollection.STNumGeometries()
LineString. Count                          | @lineString.STNumPoints()                                    | @lineString.STNumPoints()
LineString. EndPoint                       | @lineString.STEndPoint()                                     | @lineString.STEndPoint()
LineString. getpointn (n)                   | @lineString.STPointN( @n + 1)                                 | @lineString.STPointN( @n + 1)
LineString. IsClosed                       | @lineString.STIsClosed()                                     | @lineString.STIsClosed()
LineString. isring                         |                                                              | @lineString.IsRing()
LineString. StartPoint                     | @lineString.STStartPoint()                                   | @lineString.STStartPoint()
MultiLineString. IsClosed                  | @multiLineString.STIsClosed()                                | @multiLineString.STIsClosed()
Punkt. 800                                   | @point.M                                                     | @point.M
Punkt. Stuben                                   | @point.Long                                                  | @point.STX
Punkt. Teenie                                   | @point.Lat                                                   | @point.STY
Punkt. Z                                   | @point.Z                                                     | @point.Z
Polygon. ExteriorRing                      | @polygon.RingN1                                            | @polygon.STExteriorRing()
Polygon. Getinteriorringn (n)               | @polygon.RingN( @n + 2)                                       | @polygon.STInteriorRingN( @n + 1)
Polygon. Numinteriorrings                  | @polygon.NumRings()-1                                      | @polygon.STNumInteriorRing()

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Räumliche Daten in SQL Server](/sql/relational-databases/spatial/spatial-data-sql-server)
* [Nettopologysuite-Dokumentation](https://nettopologysuite.github.io/NetTopologySuite/)
