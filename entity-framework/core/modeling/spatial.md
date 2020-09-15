---
title: Räumliche Daten-EF Core
description: Verwenden räumlicher Daten in einem Entity Framework Core Modell
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
uid: core/modeling/spatial
ms.openlocfilehash: 8c08835f2d6211e6be5852b58b35f003f823bded
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071432"
---
# <a name="spatial-data"></a>Räumliche Daten

> [!NOTE]
> Diese Funktion wurde in EF Core 2,2 hinzugefügt.

Räumliche Daten repräsentieren den physischen Speicherort und die Form von Objekten. Viele Datenbanken unterstützen diese Art von Daten, damit Sie zusammen mit anderen Daten indiziert und abgefragt werden können. Häufige Szenarien umfassen das Abfragen von Objekten in einer bestimmten Entfernung von einem Speicherort oder das Auswählen des Objekts, dessen Rahmen eine bestimmte Position enthält. EF Core unterstützt die Zuordnung räumlicher Datentypen mithilfe der räumlichen Bibliothek [nettopologysuite](https://github.com/NetTopologySuite/NetTopologySuite) .

## <a name="installing"></a>Installation

Um räumliche Daten mit EF Core verwenden zu können, müssen Sie das entsprechende unterstützende nuget-Paket installieren. Welches Paket installiert werden muss, hängt vom verwendeten Anbieter ab.

EF Core Anbieter                        | Räumliches nuget-Paket
--------------------------------------- | ---------------------
Microsoft.EntityFrameworkCore.SqlServer | [Microsoft. entityframeworkcore. SqlServer. nettopologysuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
Microsoft.EntityFrameworkCore.Sqlite    | [Microsoft. entityframeworkcore. sqlite. nettopologysuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
Microsoft.EntityFrameworkCore.InMemory  | [Nettopologysuite](https://www.nuget.org/packages/NetTopologySuite)
Npgsql.EntityFrameworkCore.PostgreSQL   | [Npgsql. entityframeworkcore. PostgreSQL. nettopologysuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a>Reverse Engineering

Die räumlichen nuget-Pakete ermöglichen auch die [Reverse Engineering](xref:core/managing-schemas/scaffolding) von Modellen mit räumlichen Eigenschaften. Sie müssen das Paket jedoch ***vor*** dem Ausführen von `Scaffold-DbContext` oder installieren `dotnet ef dbcontext scaffold` . Wenn Sie dies nicht tun, erhalten Sie Warnungen, wenn Sie keine Typzuordnungen für die Spalten finden und die Spalten übersprungen werden.

## <a name="nettopologysuite-nts"></a>Nettopologysuite (NTS)

Nettopologysuite ist eine räumliche Bibliothek für .net. EF Core ermöglicht die Zuordnung von räumlichen Datentypen in der Datenbank mithilfe von NTS-Typen in Ihrem Modell.

Um die Zuordnung räumlicher Typen über NTS zu ermöglichen, müssen Sie die usenettopologysuite-Methode für den dbcontext Options-Generator des Anbieters abrufen. Beispielsweise können Sie mit SQL Server wie folgt bezeichnen.

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

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

Die folgenden Entitäts Klassen können verwendet werden, um Tabellen in der [Beispieldatenbank Wide World Importern](https://go.microsoft.com/fwlink/?LinkID=800630)zuzuordnen.

``` csharp
[Table("Cities", Schema = "Application"))]
class City
{
    public int CityID { get; set; }

    public string CityName { get; set; }

    public Point Location { get; set; }
}

[Table("Countries", Schema = "Application"))]
class Country
{
    public int CountryID { get; set; }

    public string CountryName { get; set; }

    // Database includes both Polygon and MultiPolygon values
    public Geometry Border { get; set; }
}
```

### <a name="creating-values"></a>Erstellen von Werten

Sie können Konstruktoren zum Erstellen von Geometry-Objekten verwenden. Allerdings empfiehlt es sich, stattdessen eine Geometry-Factory zu verwenden. Auf diese Weise können Sie ein Standardmäßiges SRID angeben (das von den Koordinaten verwendete räumliche Verweis System) und Ihnen die Kontrolle über erweiterte Dinge wie das Genauigkeits Modell (verwendet während der Berechnungen) und die Koordinaten Sequenz (bestimmt, welche ordinates--Dimensionen und Measures--verfügbar sind).

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> 4326 bezieht sich auf WGS 84, einen in GPS und anderen geografischen Systemen verwendeten Standard.

### <a name="longitude-and-latitude"></a>Längen-und Breitengrad

Die Koordinaten in den NTS sind X-und Y-Werte. Um Längen-und Breitengrad darzustellen, verwenden Sie X für Längengrad und Y für Breitengrad. Beachten Sie, dass **backwards** dies nicht das `latitude, longitude` Format hat, in dem normalerweise diese Werte angezeigt werden.

### <a name="srid-ignored-during-client-operations"></a>SRID wird bei Client Vorgängen ignoriert.

NTS ignoriert SRID-Werte während des Vorgangs. Es wird von einem planaren Koordinatensystem ausgegangen. Dies bedeutet Folgendes: Wenn Sie Koordinaten in Bezug auf Längengrad und Breitengrad angeben, werden einige vom Client ausgewertete Werte wie "Distance", "length" und "Area" in Grad und nicht in "Meter" angegeben. Wenn Sie aussagekräftigere Werte benötigen, müssen Sie die Koordinaten zunächst mithilfe einer Bibliothek wie [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) auf ein anderes Koordinatensystem projizieren, bevor Sie diese Werte berechnen.

Wenn ein Vorgang vom Server durch EF Core über SQL ausgewertet wird, wird die Einheit des Ergebnisses von der Datenbank bestimmt.

Im folgenden finden Sie ein Beispiel für die Verwendung von ProjNet4GeoAPI, um den Abstand zwischen zwei Städten zu berechnen.

``` csharp
static class GeometryExtensions
{
    static readonly CoordinateSystemServices _coordinateSystemServices
        = new CoordinateSystemServices(
            new CoordinateSystemFactory(),
            new CoordinateTransformationFactory(),
            new Dictionary<int, string>
            {
                // Coordinate systems:

                [4326] = GeographicCoordinateSystem.WGS84.WKT,

                // This coordinate system covers the area of our data.
                // Different data requires a different coordinate system.
                [2855] =
                @"
                    PROJCS[""NAD83(HARN) / Washington North"",
                        GEOGCS[""NAD83(HARN)"",
                            DATUM[""NAD83_High_Accuracy_Regional_Network"",
                                SPHEROID[""GRS 1980"",6378137,298.257222101,
                                    AUTHORITY[""EPSG"",""7019""]],
                                AUTHORITY[""EPSG"",""6152""]],
                            PRIMEM[""Greenwich"",0,
                                AUTHORITY[""EPSG"",""8901""]],
                            UNIT[""degree"",0.01745329251994328,
                                AUTHORITY[""EPSG"",""9122""]],
                            AUTHORITY[""EPSG"",""4152""]],
                        PROJECTION[""Lambert_Conformal_Conic_2SP""],
                        PARAMETER[""standard_parallel_1"",48.73333333333333],
                        PARAMETER[""standard_parallel_2"",47.5],
                        PARAMETER[""latitude_of_origin"",47],
                        PARAMETER[""central_meridian"",-120.8333333333333],
                        PARAMETER[""false_easting"",500000],
                        PARAMETER[""false_northing"",0],
                        UNIT[""metre"",1,
                            AUTHORITY[""EPSG"",""9001""]],
                        AUTHORITY[""EPSG"",""2855""]]
                "
            });

    public static Geometry ProjectTo(this Geometry geometry, int srid)
    {
        var transformation = _coordinateSystemServices.CreateTransformation(geometry.SRID, srid);

        var result = geometry.Copy();
        result.Apply(new MathTransformFilter(transformation.MathTransform));

        return result;
    }

    class MathTransformFilter : ICoordinateSequenceFilter
    {
        readonly MathTransform _transform;

        public MathTransformFilter(MathTransform transform)
            => _transform = transform;

        public bool Done => false;
        public bool GeometryChanged => true;

        public void Filter(CoordinateSequence seq, int i)
        {
            var result = _transform.Transform(
                new[]
                {
                    seq.GetOrdinate(i, Ordinate.X),
                    seq.GetOrdinate(i, Ordinate.Y)
                });
            seq.SetOrdinate(i, Ordinate.X, result[0]);
            seq.SetOrdinate(i, Ordinate.Y, result[1]);
        }
    }
}
```

``` csharp
var seattle = new Point(-122.333056, 47.609722) { SRID = 4326 };
var redmond = new Point(-122.123889, 47.669444) { SRID = 4326 };

var distance = seattle.ProjectTo(2855).Distance(redmond.ProjectTo(2855));
```

## <a name="querying-data"></a>Abfrage von Daten

In LINQ werden die NTS-Methoden und-Eigenschaften, die als Datenbankfunktionen verfügbar sind, in SQL übersetzt. Beispielsweise werden die Distance-Methode und die-Methode in den folgenden Abfragen übersetzt. Die Tabelle am Ende dieses Artikels zeigt, welche Member von verschiedenen EF Core Anbietern unterstützt werden.

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a>SQL Server

Wenn Sie SQL Server verwenden, müssen Sie einige zusätzliche Punkte beachten.

### <a name="geography-or-geometry"></a>Geography oder Geometry

Standardmäßig sind räumliche Eigenschaften `geography` Spalten in SQL Server zugeordnet. Konfigurieren Sie `geometry` [den Spaltentyp](xref:core/modeling/entity-properties#column-data-types) in Ihrem Modell, um zu verwenden.

### <a name="geography-polygon-rings"></a>Polygon-Polygon Ringe

Wenn der `geography` Spaltentyp verwendet wird, werden von SQL Server zusätzliche Anforderungen an den äußeren Ring (oder die Shell) und die inneren Ringe (oder Löcher) auferlegt. Der äußere Ring muss gegen den Uhrzeigersinn und die inneren Ringe im Uhrzeigersinn ausgerichtet werden. NTS überprüft dies, bevor Werte an die Datenbank gesendet werden.

### <a name="fullglobe"></a>FullGlobe

SQL Server weist einen nicht standardmäßigen Geometry-Typ auf, der den vollständigen Globus darstellt, wenn der Spaltentyp verwendet wird `geography` . Außerdem bietet es eine Möglichkeit, Polygone auf der ganzen Welt (ohne äußeren Ring) darzustellen. Keines dieser beiden wird von NTS unterstützt.

> [!WARNING]
> Fullglobe und Polygone, die darauf basieren, werden von NTS nicht unterstützt.

## <a name="sqlite"></a>SQLite

Hier finden Sie einige zusätzliche Informationen für diejenigen, die SQLite verwenden.

### <a name="installing-spatialite"></a>Installieren von spatialite

Unter Windows wird die native mod_spatialite Bibliothek als eine nuget-Paketabhängigkeit verteilt. Andere Plattformen müssen Sie separat installieren. Dies erfolgt in der Regel mithilfe eines Softwarepaket-Managers. Beispielsweise können Sie apt unter Ubuntu und Homebrew unter MacOS verwenden.

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

Leider sind neuere Versionen von proj (eine Abhängigkeit von spatialite) mit dem standardmäßigen [sqlitepclraw-Bundle](/dotnet/standard/data/sqlite/custom-versions#bundles)von EF nicht kompatibel. Sie können dieses Problem umgehen, indem Sie entweder einen benutzerdefinierten [sqlitepclraw-Anbieter](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) erstellen, der die SQLite-Bibliothek des Systems verwendet, oder Sie können einen benutzerdefinierten Build von spatialite zum Deaktivieren der proj-Unterstützung installieren.

``` sh
curl https://www.gaia-gis.it/gaia-sins/libspatialite-4.3.0a.tar.gz | tar -xz
cd libspatialite-4.3.0a

if [[ `uname -s` == Darwin* ]]; then
    # Mac OS requires some minor patching
    sed -i "" "s/shrext_cmds='\`test \\.\$module = .yes && echo .so \\|\\| echo \\.dylib\`'/shrext_cmds='.dylib'/g" configure
fi

./configure --disable-proj
make
make install
```

### <a name="configuring-srid"></a>Konfigurieren von SRID

In spatialite müssen Spalten eine SRID pro Spalte angeben. Der Standard-SRID ist `0` . Geben Sie einen anderen SRID mithilfe der forsqlitehassrid-Methode an.

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a>Dimension

Ähnlich wie SRID wird auch die Dimension (oder ordinates) einer Spalte als Teil der Spalte angegeben. Die Standard Ordinate sind X und Y. Aktivieren Sie zusätzliche Ordinate (Z und M) mithilfe der forsqlitehasdimension-Methode.

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a>Übersetzte Vorgänge

Diese Tabelle zeigt, welche NTS-Elemente von den einzelnen EF Core Anbietern in SQL übersetzt werden.

Nettopologysuite | SQL Server (Geometrie) | SQL Server (Geografie) | SQLite | Npgsql
--- |:---:|:---:|:---:|:---:
Geometry. Area | ✔ | ✔ | ✔ | ✔
Geometry. AsBinary () | ✔ | ✔ | ✔ | ✔
Geometry. AsText () | ✔ | ✔ | ✔ | ✔
Geometry. Boundary | ✔ | | ✔ | ✔
Geometry. Buffer (Double) | ✔ | ✔ | ✔ | ✔
Geometry. Buffer (Double, int) | | | ✔ | ✔
Geometry. Centroid | ✔ | | ✔ | ✔
Geometry. enthält (Geometrie) | ✔ | ✔ | ✔ | ✔
Geometry. config-Hülle () | ✔ | ✔ | ✔ | ✔
Geometry. coveredby (Geometrie) | | | ✔ | ✔
Geometry. Cover (Geometrie) | | | ✔ | ✔
Geometry. Kreuze (Geometrie) | ✔ | | ✔ | ✔
Geometry. Difference (Geometrie) | ✔ | ✔ | ✔ | ✔
Geometry. Dimension | ✔ | ✔ | ✔ | ✔
Geometry. disjoint (Geometrie) | ✔ | ✔ | ✔ | ✔
Geometry. distance (Geometrie) | ✔ | ✔ | ✔ | ✔
Geometry. Umschlag | ✔ | | ✔ | ✔
Geometry. EqualsExact (Geometrie) | | | | ✔
Geometry. equalstopologisch (Geometrie) | ✔ | ✔ | ✔ | ✔
Geometry. geometrytype | ✔ | ✔ | ✔ | ✔
Geometry. getgeometryn (int) | ✔ | | ✔ | ✔
Geometry. interiorpoint | ✔ | | ✔ | ✔
Geometry. Schnittmenge (Geometrie) | ✔ | ✔ | ✔ | ✔
Geometry. intersekten (Geometrie) | ✔ | ✔ | ✔ | ✔
Geometry. IsEmpty | ✔ | ✔ | ✔ | ✔
Geometry. IsSimple | ✔ | | ✔ | ✔
Geometry. IsValid | ✔ | ✔ | ✔ | ✔
Geometry. iswithindistance (Geometry, Double) | ✔ | | ✔ | ✔
Geometry. length | ✔ | ✔ | ✔ | ✔
Geometry. numgeometries | ✔ | ✔ | ✔ | ✔
Geometry. NumPoints | ✔ | ✔ | ✔ | ✔
Geometry. ogcgeometrytype | ✔ | ✔ | ✔ | ✔
Geometry. Überlappung (Geometrie) | ✔ | ✔ | ✔ | ✔
Geometry. pointonsurface | ✔ | | ✔ | ✔
Geometry. in Beziehung (Geometrie, Zeichenfolge) | ✔ | | ✔ | ✔
Geometry. Reverse () | | | ✔ | ✔
Geometry. SRID | ✔ | ✔ | ✔ | ✔
Geometry. symmetricdifference (Geometrie) | ✔ | ✔ | ✔ | ✔
Geometry. $ Binary () | ✔ | ✔ | ✔ | ✔
Geometry.-Text () | ✔ | ✔ | ✔ | ✔
Geometry. touches (Geometrie) | ✔ | | ✔ | ✔
Geometry. Union () | | | ✔ | ✔
Geometry. Union (Geometrie) | ✔ | ✔ | ✔ | ✔
Geometry. within (Geometry) | ✔ | ✔ | ✔ | ✔
GeometryCollection. Count | ✔ | ✔ | ✔ | ✔
GeometryCollection [int] | ✔ | ✔ | ✔ | ✔
LineString. Count | ✔ | ✔ | ✔ | ✔
LineString. EndPoint | ✔ | ✔ | ✔ | ✔
LineString. getpointn (int) | ✔ | ✔ | ✔ | ✔
LineString. IsClosed | ✔ | ✔ | ✔ | ✔
LineString. isring | ✔ | | ✔ | ✔
LineString. StartPoint | ✔ | ✔ | ✔ | ✔
MultiLineString. IsClosed | ✔ | ✔ | ✔ | ✔
Punkt. M | ✔ | ✔ | ✔ | ✔
Point. X | ✔ | ✔ | ✔ | ✔
Punkt. Y | ✔ | ✔ | ✔ | ✔
Punkt. Z | ✔ | ✔ | ✔ | ✔
Polygon. ExteriorRing | ✔ | ✔ | ✔ | ✔
Polygon. getinteriorringn (int) | ✔ | ✔ | ✔ | ✔
Polygon. numinteriorrings | ✔ | ✔ | ✔ | ✔

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Räumliche Daten in SQL Server](/sql/relational-databases/spatial/spatial-data-sql-server)
* [Spatialite-Homepage](https://www.gaia-gis.it/fossil/libspatialite)
* [Räumliche Dokumentation zu npgsql](https://www.npgsql.org/efcore/mapping/nts.html)
* [PostGIS-Dokumentation](https://postgis.net/documentation/)
