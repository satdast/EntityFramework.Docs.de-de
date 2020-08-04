---
title: Räumliche Daten-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/01/2018
ms.assetid: 2BDE29FC-4161-41A0-841E-69F51CCD9341
uid: core/modeling/spatial
ms.openlocfilehash: 85124b7e252797ccd952d0d332e7309eff97ba56
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526666"
---
# <a name="spatial-data"></a><span data-ttu-id="51b49-102">Räumliche Daten</span><span class="sxs-lookup"><span data-stu-id="51b49-102">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="51b49-103">Diese Funktion wurde in EF Core 2,2 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="51b49-103">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="51b49-104">Räumliche Daten repräsentieren den physischen Speicherort und die Form von Objekten.</span><span class="sxs-lookup"><span data-stu-id="51b49-104">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="51b49-105">Viele Datenbanken unterstützen diese Art von Daten, damit Sie zusammen mit anderen Daten indiziert und abgefragt werden können.</span><span class="sxs-lookup"><span data-stu-id="51b49-105">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="51b49-106">Häufige Szenarien umfassen das Abfragen von Objekten in einer bestimmten Entfernung von einem Speicherort oder das Auswählen des Objekts, dessen Rahmen eine bestimmte Position enthält.</span><span class="sxs-lookup"><span data-stu-id="51b49-106">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="51b49-107">EF Core unterstützt die Zuordnung räumlicher Datentypen mithilfe der räumlichen Bibliothek [nettopologysuite](https://github.com/NetTopologySuite/NetTopologySuite) .</span><span class="sxs-lookup"><span data-stu-id="51b49-107">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="51b49-108">Installation</span><span class="sxs-lookup"><span data-stu-id="51b49-108">Installing</span></span>

<span data-ttu-id="51b49-109">Um räumliche Daten mit EF Core verwenden zu können, müssen Sie das entsprechende unterstützende nuget-Paket installieren.</span><span class="sxs-lookup"><span data-stu-id="51b49-109">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="51b49-110">Welches Paket installiert werden muss, hängt vom verwendeten Anbieter ab.</span><span class="sxs-lookup"><span data-stu-id="51b49-110">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="51b49-111">EF Core Anbieter</span><span class="sxs-lookup"><span data-stu-id="51b49-111">EF Core Provider</span></span>                        | <span data-ttu-id="51b49-112">Räumliches nuget-Paket</span><span class="sxs-lookup"><span data-stu-id="51b49-112">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="51b49-113">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="51b49-113">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="51b49-114">Microsoft. entityframeworkcore. SqlServer. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="51b49-114">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="51b49-115">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="51b49-115">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="51b49-116">Microsoft. entityframeworkcore. sqlite. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="51b49-116">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="51b49-117">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="51b49-117">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="51b49-118">Nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="51b49-118">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="51b49-119">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="51b49-119">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="51b49-120">Npgsql. entityframeworkcore. PostgreSQL. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="51b49-120">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="51b49-121">Reverse Engineering</span><span class="sxs-lookup"><span data-stu-id="51b49-121">Reverse engineering</span></span>

<span data-ttu-id="51b49-122">Die räumlichen nuget-Pakete ermöglichen auch die [Reverse Engineering](../managing-schemas/scaffolding.md) von Modellen mit räumlichen Eigenschaften. Sie müssen das Paket jedoch ***vor*** dem Ausführen von `Scaffold-DbContext` oder installieren `dotnet ef dbcontext scaffold` .</span><span class="sxs-lookup"><span data-stu-id="51b49-122">The spatial NuGet packages also enable [reverse engineering](../managing-schemas/scaffolding.md) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="51b49-123">Wenn Sie dies nicht tun, erhalten Sie Warnungen, wenn Sie keine Typzuordnungen für die Spalten finden und die Spalten übersprungen werden.</span><span class="sxs-lookup"><span data-stu-id="51b49-123">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="51b49-124">Nettopologysuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="51b49-124">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="51b49-125">Nettopologysuite ist eine räumliche Bibliothek für .net.</span><span class="sxs-lookup"><span data-stu-id="51b49-125">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="51b49-126">EF Core ermöglicht die Zuordnung von räumlichen Datentypen in der Datenbank mithilfe von NTS-Typen in Ihrem Modell.</span><span class="sxs-lookup"><span data-stu-id="51b49-126">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="51b49-127">Um die Zuordnung räumlicher Typen über NTS zu ermöglichen, müssen Sie die usenettopologysuite-Methode für den dbcontext Options-Generator des Anbieters abrufen.</span><span class="sxs-lookup"><span data-stu-id="51b49-127">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="51b49-128">Beispielsweise können Sie mit SQL Server wie folgt bezeichnen.</span><span class="sxs-lookup"><span data-stu-id="51b49-128">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="51b49-129">Es gibt mehrere räumliche Datentypen.</span><span class="sxs-lookup"><span data-stu-id="51b49-129">There are several spatial data types.</span></span> <span data-ttu-id="51b49-130">Welcher Typ Sie verwenden, hängt von den Formen der Formen ab, die Sie zulassen möchten.</span><span class="sxs-lookup"><span data-stu-id="51b49-130">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="51b49-131">Hier ist die Hierarchie der NTS-Typen, die Sie für Eigenschaften im Modell verwenden können.</span><span class="sxs-lookup"><span data-stu-id="51b49-131">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="51b49-132">Sie befinden sich im- `NetTopologySuite.Geometries` Namespace.</span><span class="sxs-lookup"><span data-stu-id="51b49-132">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="51b49-133">Geometrie</span><span class="sxs-lookup"><span data-stu-id="51b49-133">Geometry</span></span>
  * <span data-ttu-id="51b49-134">Point</span><span class="sxs-lookup"><span data-stu-id="51b49-134">Point</span></span>
  * <span data-ttu-id="51b49-135">LineString</span><span class="sxs-lookup"><span data-stu-id="51b49-135">LineString</span></span>
  * <span data-ttu-id="51b49-136">Polygon</span><span class="sxs-lookup"><span data-stu-id="51b49-136">Polygon</span></span>
  * <span data-ttu-id="51b49-137">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="51b49-137">GeometryCollection</span></span>
    * <span data-ttu-id="51b49-138">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="51b49-138">MultiPoint</span></span>
    * <span data-ttu-id="51b49-139">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="51b49-139">MultiLineString</span></span>
    * <span data-ttu-id="51b49-140">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="51b49-140">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="51b49-141">Circularstring, compoundcurve und curepolygon werden von NTS nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="51b49-141">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="51b49-142">Mit dem basisgeometry-Typ kann jeder Typ von Form von der-Eigenschaft angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="51b49-142">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="51b49-143">Die folgenden Entitäts Klassen können verwendet werden, um Tabellen in der [Beispieldatenbank Wide World Importern](https://go.microsoft.com/fwlink/?LinkID=800630)zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="51b49-143">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="51b49-144">Erstellen von Werten</span><span class="sxs-lookup"><span data-stu-id="51b49-144">Creating values</span></span>

<span data-ttu-id="51b49-145">Sie können Konstruktoren zum Erstellen von Geometry-Objekten verwenden. Allerdings empfiehlt es sich, stattdessen eine Geometry-Factory zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="51b49-145">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="51b49-146">Auf diese Weise können Sie ein Standardmäßiges SRID angeben (das von den Koordinaten verwendete räumliche Verweis System) und Ihnen die Kontrolle über erweiterte Dinge wie das Genauigkeits Modell (verwendet während der Berechnungen) und die Koordinaten Sequenz (bestimmt, welche ordinates--Dimensionen und Measures--verfügbar sind).</span><span class="sxs-lookup"><span data-stu-id="51b49-146">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> <span data-ttu-id="51b49-147">4326 bezieht sich auf WGS 84, einen in GPS und anderen geografischen Systemen verwendeten Standard.</span><span class="sxs-lookup"><span data-stu-id="51b49-147">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="51b49-148">Längen-und Breitengrad</span><span class="sxs-lookup"><span data-stu-id="51b49-148">Longitude and Latitude</span></span>

<span data-ttu-id="51b49-149">Die Koordinaten in den NTS sind X-und Y-Werte.</span><span class="sxs-lookup"><span data-stu-id="51b49-149">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="51b49-150">Um Längen-und Breitengrad darzustellen, verwenden Sie X für Längengrad und Y für Breitengrad.</span><span class="sxs-lookup"><span data-stu-id="51b49-150">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="51b49-151">Beachten Sie, dass **backwards** dies nicht das `latitude, longitude` Format hat, in dem normalerweise diese Werte angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="51b49-151">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="51b49-152">SRID wird bei Client Vorgängen ignoriert.</span><span class="sxs-lookup"><span data-stu-id="51b49-152">SRID Ignored during client operations</span></span>

<span data-ttu-id="51b49-153">NTS ignoriert SRID-Werte während des Vorgangs.</span><span class="sxs-lookup"><span data-stu-id="51b49-153">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="51b49-154">Es wird von einem planaren Koordinatensystem ausgegangen.</span><span class="sxs-lookup"><span data-stu-id="51b49-154">It assumes a planar coordinate system.</span></span> <span data-ttu-id="51b49-155">Dies bedeutet Folgendes: Wenn Sie Koordinaten in Bezug auf Längengrad und Breitengrad angeben, werden einige vom Client ausgewertete Werte wie "Distance", "length" und "Area" in Grad und nicht in "Meter" angegeben.</span><span class="sxs-lookup"><span data-stu-id="51b49-155">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="51b49-156">Wenn Sie aussagekräftigere Werte benötigen, müssen Sie die Koordinaten zunächst mithilfe einer Bibliothek wie [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) auf ein anderes Koordinatensystem projizieren, bevor Sie diese Werte berechnen.</span><span class="sxs-lookup"><span data-stu-id="51b49-156">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="51b49-157">Wenn ein Vorgang vom Server durch EF Core über SQL ausgewertet wird, wird die Einheit des Ergebnisses von der Datenbank bestimmt.</span><span class="sxs-lookup"><span data-stu-id="51b49-157">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="51b49-158">Im folgenden finden Sie ein Beispiel für die Verwendung von ProjNet4GeoAPI, um den Abstand zwischen zwei Städten zu berechnen.</span><span class="sxs-lookup"><span data-stu-id="51b49-158">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="51b49-159">Abfrage von Daten</span><span class="sxs-lookup"><span data-stu-id="51b49-159">Querying Data</span></span>

<span data-ttu-id="51b49-160">In LINQ werden die NTS-Methoden und-Eigenschaften, die als Datenbankfunktionen verfügbar sind, in SQL übersetzt.</span><span class="sxs-lookup"><span data-stu-id="51b49-160">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="51b49-161">Beispielsweise werden die Distance-Methode und die-Methode in den folgenden Abfragen übersetzt.</span><span class="sxs-lookup"><span data-stu-id="51b49-161">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="51b49-162">Die Tabelle am Ende dieses Artikels zeigt, welche Member von verschiedenen EF Core Anbietern unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="51b49-162">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="51b49-163">SQL Server</span><span class="sxs-lookup"><span data-stu-id="51b49-163">SQL Server</span></span>

<span data-ttu-id="51b49-164">Wenn Sie SQL Server verwenden, müssen Sie einige zusätzliche Punkte beachten.</span><span class="sxs-lookup"><span data-stu-id="51b49-164">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="51b49-165">Geography oder Geometry</span><span class="sxs-lookup"><span data-stu-id="51b49-165">Geography or geometry</span></span>

<span data-ttu-id="51b49-166">Standardmäßig sind räumliche Eigenschaften `geography` Spalten in SQL Server zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="51b49-166">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="51b49-167">Konfigurieren Sie `geometry` [den Spaltentyp](xref:core/modeling/entity-properties#column-data-types) in Ihrem Modell, um zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="51b49-167">To use `geometry`, [configure the column type](xref:core/modeling/entity-properties#column-data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="51b49-168">Polygon-Polygon Ringe</span><span class="sxs-lookup"><span data-stu-id="51b49-168">Geography polygon rings</span></span>

<span data-ttu-id="51b49-169">Wenn der `geography` Spaltentyp verwendet wird, werden von SQL Server zusätzliche Anforderungen an den äußeren Ring (oder die Shell) und die inneren Ringe (oder Löcher) auferlegt.</span><span class="sxs-lookup"><span data-stu-id="51b49-169">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="51b49-170">Der äußere Ring muss gegen den Uhrzeigersinn und die inneren Ringe im Uhrzeigersinn ausgerichtet werden.</span><span class="sxs-lookup"><span data-stu-id="51b49-170">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="51b49-171">NTS überprüft dies, bevor Werte an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="51b49-171">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="51b49-172">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="51b49-172">FullGlobe</span></span>

<span data-ttu-id="51b49-173">SQL Server weist einen nicht standardmäßigen Geometry-Typ auf, der den vollständigen Globus darstellt, wenn der Spaltentyp verwendet wird `geography` .</span><span class="sxs-lookup"><span data-stu-id="51b49-173">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="51b49-174">Außerdem bietet es eine Möglichkeit, Polygone auf der ganzen Welt (ohne äußeren Ring) darzustellen.</span><span class="sxs-lookup"><span data-stu-id="51b49-174">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="51b49-175">Keines dieser beiden wird von NTS unterstützt.</span><span class="sxs-lookup"><span data-stu-id="51b49-175">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="51b49-176">Fullglobe und Polygone, die darauf basieren, werden von NTS nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="51b49-176">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="51b49-177">SQLite</span><span class="sxs-lookup"><span data-stu-id="51b49-177">SQLite</span></span>

<span data-ttu-id="51b49-178">Hier finden Sie einige zusätzliche Informationen für diejenigen, die SQLite verwenden.</span><span class="sxs-lookup"><span data-stu-id="51b49-178">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="51b49-179">Installieren von spatialite</span><span class="sxs-lookup"><span data-stu-id="51b49-179">Installing SpatiaLite</span></span>

<span data-ttu-id="51b49-180">Unter Windows wird die native mod_spatialite Bibliothek als eine nuget-Paketabhängigkeit verteilt.</span><span class="sxs-lookup"><span data-stu-id="51b49-180">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="51b49-181">Andere Plattformen müssen Sie separat installieren.</span><span class="sxs-lookup"><span data-stu-id="51b49-181">Other platforms need to install it separately.</span></span> <span data-ttu-id="51b49-182">Dies erfolgt in der Regel mithilfe eines Softwarepaket-Managers.</span><span class="sxs-lookup"><span data-stu-id="51b49-182">This is typically done using a software package manager.</span></span> <span data-ttu-id="51b49-183">Beispielsweise können Sie apt unter Ubuntu und Homebrew unter MacOS verwenden.</span><span class="sxs-lookup"><span data-stu-id="51b49-183">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="51b49-184">Leider sind neuere Versionen von proj (eine Abhängigkeit von spatialite) mit dem standardmäßigen [sqlitepclraw-Bundle](/dotnet/standard/data/sqlite/custom-versions#bundles)von EF nicht kompatibel.</span><span class="sxs-lookup"><span data-stu-id="51b49-184">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="51b49-185">Sie können dieses Problem umgehen, indem Sie entweder einen benutzerdefinierten [sqlitepclraw-Anbieter](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) erstellen, der die SQLite-Bibliothek des Systems verwendet, oder Sie können einen benutzerdefinierten Build von spatialite zum Deaktivieren der proj-Unterstützung installieren.</span><span class="sxs-lookup"><span data-stu-id="51b49-185">You can work around this by either creating a custom [SQLitePCLRaw provider](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) that uses the system SQLite library, or you can install a custom build of SpatiaLite disabling PROJ support.</span></span>

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

### <a name="configuring-srid"></a><span data-ttu-id="51b49-186">Konfigurieren von SRID</span><span class="sxs-lookup"><span data-stu-id="51b49-186">Configuring SRID</span></span>

<span data-ttu-id="51b49-187">In spatialite müssen Spalten eine SRID pro Spalte angeben.</span><span class="sxs-lookup"><span data-stu-id="51b49-187">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="51b49-188">Der Standard-SRID ist `0` .</span><span class="sxs-lookup"><span data-stu-id="51b49-188">The default SRID is `0`.</span></span> <span data-ttu-id="51b49-189">Geben Sie einen anderen SRID mithilfe der forsqlitehassrid-Methode an.</span><span class="sxs-lookup"><span data-stu-id="51b49-189">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="51b49-190">Dimension</span><span class="sxs-lookup"><span data-stu-id="51b49-190">Dimension</span></span>

<span data-ttu-id="51b49-191">Ähnlich wie SRID wird auch die Dimension (oder ordinates) einer Spalte als Teil der Spalte angegeben.</span><span class="sxs-lookup"><span data-stu-id="51b49-191">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="51b49-192">Die Standard Ordinate sind X und Y. Aktivieren Sie zusätzliche Ordinate (Z und M) mithilfe der forsqlitehasdimension-Methode.</span><span class="sxs-lookup"><span data-stu-id="51b49-192">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="51b49-193">Übersetzte Vorgänge</span><span class="sxs-lookup"><span data-stu-id="51b49-193">Translated Operations</span></span>

<span data-ttu-id="51b49-194">Diese Tabelle zeigt, welche NTS-Elemente von den einzelnen EF Core Anbietern in SQL übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="51b49-194">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="51b49-195">Nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="51b49-195">NetTopologySuite</span></span> | <span data-ttu-id="51b49-196">SQL Server (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="51b49-196">SQL Server (geometry)</span></span> | <span data-ttu-id="51b49-197">SQL Server (Geografie)</span><span class="sxs-lookup"><span data-stu-id="51b49-197">SQL Server (geography)</span></span> | <span data-ttu-id="51b49-198">SQLite</span><span class="sxs-lookup"><span data-stu-id="51b49-198">SQLite</span></span> | <span data-ttu-id="51b49-199">Npgsql</span><span class="sxs-lookup"><span data-stu-id="51b49-199">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="51b49-200">Geometry. Area</span><span class="sxs-lookup"><span data-stu-id="51b49-200">Geometry.Area</span></span> | <span data-ttu-id="51b49-201">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-201">✔</span></span> | <span data-ttu-id="51b49-202">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-202">✔</span></span> | <span data-ttu-id="51b49-203">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-203">✔</span></span> | <span data-ttu-id="51b49-204">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-204">✔</span></span>
<span data-ttu-id="51b49-205">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="51b49-205">Geometry.AsBinary()</span></span> | <span data-ttu-id="51b49-206">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-206">✔</span></span> | <span data-ttu-id="51b49-207">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-207">✔</span></span> | <span data-ttu-id="51b49-208">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-208">✔</span></span> | <span data-ttu-id="51b49-209">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-209">✔</span></span>
<span data-ttu-id="51b49-210">Geometry. AsText ()</span><span class="sxs-lookup"><span data-stu-id="51b49-210">Geometry.AsText()</span></span> | <span data-ttu-id="51b49-211">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-211">✔</span></span> | <span data-ttu-id="51b49-212">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-212">✔</span></span> | <span data-ttu-id="51b49-213">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-213">✔</span></span> | <span data-ttu-id="51b49-214">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-214">✔</span></span>
<span data-ttu-id="51b49-215">Geometry. Boundary</span><span class="sxs-lookup"><span data-stu-id="51b49-215">Geometry.Boundary</span></span> | <span data-ttu-id="51b49-216">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-216">✔</span></span> | | <span data-ttu-id="51b49-217">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-217">✔</span></span> | <span data-ttu-id="51b49-218">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-218">✔</span></span>
<span data-ttu-id="51b49-219">Geometry. Buffer (Double)</span><span class="sxs-lookup"><span data-stu-id="51b49-219">Geometry.Buffer(double)</span></span> | <span data-ttu-id="51b49-220">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-220">✔</span></span> | <span data-ttu-id="51b49-221">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-221">✔</span></span> | <span data-ttu-id="51b49-222">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-222">✔</span></span> | <span data-ttu-id="51b49-223">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-223">✔</span></span>
<span data-ttu-id="51b49-224">Geometry. Buffer (Double, int)</span><span class="sxs-lookup"><span data-stu-id="51b49-224">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="51b49-225">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-225">✔</span></span> | <span data-ttu-id="51b49-226">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-226">✔</span></span>
<span data-ttu-id="51b49-227">Geometry. Centroid</span><span class="sxs-lookup"><span data-stu-id="51b49-227">Geometry.Centroid</span></span> | <span data-ttu-id="51b49-228">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-228">✔</span></span> | | <span data-ttu-id="51b49-229">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-229">✔</span></span> | <span data-ttu-id="51b49-230">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-230">✔</span></span>
<span data-ttu-id="51b49-231">Geometry. enthält (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="51b49-231">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="51b49-232">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-232">✔</span></span> | <span data-ttu-id="51b49-233">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-233">✔</span></span> | <span data-ttu-id="51b49-234">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-234">✔</span></span> | <span data-ttu-id="51b49-235">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-235">✔</span></span>
<span data-ttu-id="51b49-236">Geometry. config-Hülle ()</span><span class="sxs-lookup"><span data-stu-id="51b49-236">Geometry.ConvexHull()</span></span> | <span data-ttu-id="51b49-237">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-237">✔</span></span> | <span data-ttu-id="51b49-238">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-238">✔</span></span> | <span data-ttu-id="51b49-239">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-239">✔</span></span> | <span data-ttu-id="51b49-240">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-240">✔</span></span>
<span data-ttu-id="51b49-241">Geometry. coveredby (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="51b49-241">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="51b49-242">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-242">✔</span></span> | <span data-ttu-id="51b49-243">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-243">✔</span></span>
<span data-ttu-id="51b49-244">Geometry. Cover (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="51b49-244">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="51b49-245">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-245">✔</span></span> | <span data-ttu-id="51b49-246">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-246">✔</span></span>
<span data-ttu-id="51b49-247">Geometry. Kreuze (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="51b49-247">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="51b49-248">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-248">✔</span></span> | | <span data-ttu-id="51b49-249">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-249">✔</span></span> | <span data-ttu-id="51b49-250">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-250">✔</span></span>
<span data-ttu-id="51b49-251">Geometry. Difference (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="51b49-251">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="51b49-252">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-252">✔</span></span> | <span data-ttu-id="51b49-253">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-253">✔</span></span> | <span data-ttu-id="51b49-254">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-254">✔</span></span> | <span data-ttu-id="51b49-255">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-255">✔</span></span>
<span data-ttu-id="51b49-256">Geometry. Dimension</span><span class="sxs-lookup"><span data-stu-id="51b49-256">Geometry.Dimension</span></span> | <span data-ttu-id="51b49-257">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-257">✔</span></span> | <span data-ttu-id="51b49-258">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-258">✔</span></span> | <span data-ttu-id="51b49-259">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-259">✔</span></span> | <span data-ttu-id="51b49-260">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-260">✔</span></span>
<span data-ttu-id="51b49-261">Geometry. disjoint (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="51b49-261">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="51b49-262">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-262">✔</span></span> | <span data-ttu-id="51b49-263">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-263">✔</span></span> | <span data-ttu-id="51b49-264">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-264">✔</span></span> | <span data-ttu-id="51b49-265">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-265">✔</span></span>
<span data-ttu-id="51b49-266">Geometry. distance (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="51b49-266">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="51b49-267">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-267">✔</span></span> | <span data-ttu-id="51b49-268">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-268">✔</span></span> | <span data-ttu-id="51b49-269">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-269">✔</span></span> | <span data-ttu-id="51b49-270">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-270">✔</span></span>
<span data-ttu-id="51b49-271">Geometry. Umschlag</span><span class="sxs-lookup"><span data-stu-id="51b49-271">Geometry.Envelope</span></span> | <span data-ttu-id="51b49-272">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-272">✔</span></span> | | <span data-ttu-id="51b49-273">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-273">✔</span></span> | <span data-ttu-id="51b49-274">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-274">✔</span></span>
<span data-ttu-id="51b49-275">Geometry. EqualsExact (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="51b49-275">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="51b49-276">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-276">✔</span></span>
<span data-ttu-id="51b49-277">Geometry. equalstopologisch (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="51b49-277">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="51b49-278">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-278">✔</span></span> | <span data-ttu-id="51b49-279">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-279">✔</span></span> | <span data-ttu-id="51b49-280">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-280">✔</span></span> | <span data-ttu-id="51b49-281">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-281">✔</span></span>
<span data-ttu-id="51b49-282">Geometry. geometrytype</span><span class="sxs-lookup"><span data-stu-id="51b49-282">Geometry.GeometryType</span></span> | <span data-ttu-id="51b49-283">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-283">✔</span></span> | <span data-ttu-id="51b49-284">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-284">✔</span></span> | <span data-ttu-id="51b49-285">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-285">✔</span></span> | <span data-ttu-id="51b49-286">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-286">✔</span></span>
<span data-ttu-id="51b49-287">Geometry. getgeometryn (int)</span><span class="sxs-lookup"><span data-stu-id="51b49-287">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="51b49-288">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-288">✔</span></span> | | <span data-ttu-id="51b49-289">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-289">✔</span></span> | <span data-ttu-id="51b49-290">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-290">✔</span></span>
<span data-ttu-id="51b49-291">Geometry. interiorpoint</span><span class="sxs-lookup"><span data-stu-id="51b49-291">Geometry.InteriorPoint</span></span> | <span data-ttu-id="51b49-292">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-292">✔</span></span> | | <span data-ttu-id="51b49-293">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-293">✔</span></span> | <span data-ttu-id="51b49-294">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-294">✔</span></span>
<span data-ttu-id="51b49-295">Geometry. Schnittmenge (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="51b49-295">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="51b49-296">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-296">✔</span></span> | <span data-ttu-id="51b49-297">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-297">✔</span></span> | <span data-ttu-id="51b49-298">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-298">✔</span></span> | <span data-ttu-id="51b49-299">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-299">✔</span></span>
<span data-ttu-id="51b49-300">Geometry. intersekten (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="51b49-300">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="51b49-301">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-301">✔</span></span> | <span data-ttu-id="51b49-302">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-302">✔</span></span> | <span data-ttu-id="51b49-303">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-303">✔</span></span> | <span data-ttu-id="51b49-304">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-304">✔</span></span>
<span data-ttu-id="51b49-305">Geometry. IsEmpty</span><span class="sxs-lookup"><span data-stu-id="51b49-305">Geometry.IsEmpty</span></span> | <span data-ttu-id="51b49-306">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-306">✔</span></span> | <span data-ttu-id="51b49-307">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-307">✔</span></span> | <span data-ttu-id="51b49-308">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-308">✔</span></span> | <span data-ttu-id="51b49-309">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-309">✔</span></span>
<span data-ttu-id="51b49-310">Geometry. IsSimple</span><span class="sxs-lookup"><span data-stu-id="51b49-310">Geometry.IsSimple</span></span> | <span data-ttu-id="51b49-311">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-311">✔</span></span> | | <span data-ttu-id="51b49-312">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-312">✔</span></span> | <span data-ttu-id="51b49-313">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-313">✔</span></span>
<span data-ttu-id="51b49-314">Geometry. IsValid</span><span class="sxs-lookup"><span data-stu-id="51b49-314">Geometry.IsValid</span></span> | <span data-ttu-id="51b49-315">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-315">✔</span></span> | <span data-ttu-id="51b49-316">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-316">✔</span></span> | <span data-ttu-id="51b49-317">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-317">✔</span></span> | <span data-ttu-id="51b49-318">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-318">✔</span></span>
<span data-ttu-id="51b49-319">Geometry. iswithindistance (Geometry, Double)</span><span class="sxs-lookup"><span data-stu-id="51b49-319">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="51b49-320">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-320">✔</span></span> | | <span data-ttu-id="51b49-321">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-321">✔</span></span> | <span data-ttu-id="51b49-322">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-322">✔</span></span>
<span data-ttu-id="51b49-323">Geometry. length</span><span class="sxs-lookup"><span data-stu-id="51b49-323">Geometry.Length</span></span> | <span data-ttu-id="51b49-324">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-324">✔</span></span> | <span data-ttu-id="51b49-325">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-325">✔</span></span> | <span data-ttu-id="51b49-326">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-326">✔</span></span> | <span data-ttu-id="51b49-327">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-327">✔</span></span>
<span data-ttu-id="51b49-328">Geometry. numgeometries</span><span class="sxs-lookup"><span data-stu-id="51b49-328">Geometry.NumGeometries</span></span> | <span data-ttu-id="51b49-329">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-329">✔</span></span> | <span data-ttu-id="51b49-330">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-330">✔</span></span> | <span data-ttu-id="51b49-331">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-331">✔</span></span> | <span data-ttu-id="51b49-332">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-332">✔</span></span>
<span data-ttu-id="51b49-333">Geometry. NumPoints</span><span class="sxs-lookup"><span data-stu-id="51b49-333">Geometry.NumPoints</span></span> | <span data-ttu-id="51b49-334">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-334">✔</span></span> | <span data-ttu-id="51b49-335">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-335">✔</span></span> | <span data-ttu-id="51b49-336">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-336">✔</span></span> | <span data-ttu-id="51b49-337">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-337">✔</span></span>
<span data-ttu-id="51b49-338">Geometry. ogcgeometrytype</span><span class="sxs-lookup"><span data-stu-id="51b49-338">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="51b49-339">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-339">✔</span></span> | <span data-ttu-id="51b49-340">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-340">✔</span></span> | <span data-ttu-id="51b49-341">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-341">✔</span></span> | <span data-ttu-id="51b49-342">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-342">✔</span></span>
<span data-ttu-id="51b49-343">Geometry. Überlappung (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="51b49-343">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="51b49-344">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-344">✔</span></span> | <span data-ttu-id="51b49-345">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-345">✔</span></span> | <span data-ttu-id="51b49-346">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-346">✔</span></span> | <span data-ttu-id="51b49-347">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-347">✔</span></span>
<span data-ttu-id="51b49-348">Geometry. pointonsurface</span><span class="sxs-lookup"><span data-stu-id="51b49-348">Geometry.PointOnSurface</span></span> | <span data-ttu-id="51b49-349">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-349">✔</span></span> | | <span data-ttu-id="51b49-350">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-350">✔</span></span> | <span data-ttu-id="51b49-351">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-351">✔</span></span>
<span data-ttu-id="51b49-352">Geometry. in Beziehung (Geometrie, Zeichenfolge)</span><span class="sxs-lookup"><span data-stu-id="51b49-352">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="51b49-353">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-353">✔</span></span> | | <span data-ttu-id="51b49-354">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-354">✔</span></span> | <span data-ttu-id="51b49-355">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-355">✔</span></span>
<span data-ttu-id="51b49-356">Geometry. Reverse ()</span><span class="sxs-lookup"><span data-stu-id="51b49-356">Geometry.Reverse()</span></span> | | | <span data-ttu-id="51b49-357">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-357">✔</span></span> | <span data-ttu-id="51b49-358">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-358">✔</span></span>
<span data-ttu-id="51b49-359">Geometry. SRID</span><span class="sxs-lookup"><span data-stu-id="51b49-359">Geometry.SRID</span></span> | <span data-ttu-id="51b49-360">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-360">✔</span></span> | <span data-ttu-id="51b49-361">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-361">✔</span></span> | <span data-ttu-id="51b49-362">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-362">✔</span></span> | <span data-ttu-id="51b49-363">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-363">✔</span></span>
<span data-ttu-id="51b49-364">Geometry. symmetricdifference (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="51b49-364">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="51b49-365">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-365">✔</span></span> | <span data-ttu-id="51b49-366">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-366">✔</span></span> | <span data-ttu-id="51b49-367">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-367">✔</span></span> | <span data-ttu-id="51b49-368">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-368">✔</span></span>
<span data-ttu-id="51b49-369">Geometry. $ Binary ()</span><span class="sxs-lookup"><span data-stu-id="51b49-369">Geometry.ToBinary()</span></span> | <span data-ttu-id="51b49-370">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-370">✔</span></span> | <span data-ttu-id="51b49-371">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-371">✔</span></span> | <span data-ttu-id="51b49-372">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-372">✔</span></span> | <span data-ttu-id="51b49-373">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-373">✔</span></span>
<span data-ttu-id="51b49-374">Geometry.-Text ()</span><span class="sxs-lookup"><span data-stu-id="51b49-374">Geometry.ToText()</span></span> | <span data-ttu-id="51b49-375">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-375">✔</span></span> | <span data-ttu-id="51b49-376">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-376">✔</span></span> | <span data-ttu-id="51b49-377">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-377">✔</span></span> | <span data-ttu-id="51b49-378">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-378">✔</span></span>
<span data-ttu-id="51b49-379">Geometry. touches (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="51b49-379">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="51b49-380">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-380">✔</span></span> | | <span data-ttu-id="51b49-381">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-381">✔</span></span> | <span data-ttu-id="51b49-382">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-382">✔</span></span>
<span data-ttu-id="51b49-383">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="51b49-383">Geometry.Union()</span></span> | | | <span data-ttu-id="51b49-384">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-384">✔</span></span> | <span data-ttu-id="51b49-385">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-385">✔</span></span>
<span data-ttu-id="51b49-386">Geometry. Union (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="51b49-386">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="51b49-387">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-387">✔</span></span> | <span data-ttu-id="51b49-388">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-388">✔</span></span> | <span data-ttu-id="51b49-389">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-389">✔</span></span> | <span data-ttu-id="51b49-390">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-390">✔</span></span>
<span data-ttu-id="51b49-391">Geometry. within (Geometry)</span><span class="sxs-lookup"><span data-stu-id="51b49-391">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="51b49-392">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-392">✔</span></span> | <span data-ttu-id="51b49-393">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-393">✔</span></span> | <span data-ttu-id="51b49-394">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-394">✔</span></span> | <span data-ttu-id="51b49-395">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-395">✔</span></span>
<span data-ttu-id="51b49-396">GeometryCollection. Count</span><span class="sxs-lookup"><span data-stu-id="51b49-396">GeometryCollection.Count</span></span> | <span data-ttu-id="51b49-397">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-397">✔</span></span> | <span data-ttu-id="51b49-398">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-398">✔</span></span> | <span data-ttu-id="51b49-399">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-399">✔</span></span> | <span data-ttu-id="51b49-400">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-400">✔</span></span>
<span data-ttu-id="51b49-401">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="51b49-401">GeometryCollection[int]</span></span> | <span data-ttu-id="51b49-402">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-402">✔</span></span> | <span data-ttu-id="51b49-403">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-403">✔</span></span> | <span data-ttu-id="51b49-404">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-404">✔</span></span> | <span data-ttu-id="51b49-405">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-405">✔</span></span>
<span data-ttu-id="51b49-406">LineString. Count</span><span class="sxs-lookup"><span data-stu-id="51b49-406">LineString.Count</span></span> | <span data-ttu-id="51b49-407">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-407">✔</span></span> | <span data-ttu-id="51b49-408">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-408">✔</span></span> | <span data-ttu-id="51b49-409">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-409">✔</span></span> | <span data-ttu-id="51b49-410">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-410">✔</span></span>
<span data-ttu-id="51b49-411">LineString. EndPoint</span><span class="sxs-lookup"><span data-stu-id="51b49-411">LineString.EndPoint</span></span> | <span data-ttu-id="51b49-412">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-412">✔</span></span> | <span data-ttu-id="51b49-413">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-413">✔</span></span> | <span data-ttu-id="51b49-414">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-414">✔</span></span> | <span data-ttu-id="51b49-415">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-415">✔</span></span>
<span data-ttu-id="51b49-416">LineString. getpointn (int)</span><span class="sxs-lookup"><span data-stu-id="51b49-416">LineString.GetPointN(int)</span></span> | <span data-ttu-id="51b49-417">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-417">✔</span></span> | <span data-ttu-id="51b49-418">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-418">✔</span></span> | <span data-ttu-id="51b49-419">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-419">✔</span></span> | <span data-ttu-id="51b49-420">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-420">✔</span></span>
<span data-ttu-id="51b49-421">LineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="51b49-421">LineString.IsClosed</span></span> | <span data-ttu-id="51b49-422">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-422">✔</span></span> | <span data-ttu-id="51b49-423">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-423">✔</span></span> | <span data-ttu-id="51b49-424">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-424">✔</span></span> | <span data-ttu-id="51b49-425">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-425">✔</span></span>
<span data-ttu-id="51b49-426">LineString. isring</span><span class="sxs-lookup"><span data-stu-id="51b49-426">LineString.IsRing</span></span> | <span data-ttu-id="51b49-427">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-427">✔</span></span> | | <span data-ttu-id="51b49-428">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-428">✔</span></span> | <span data-ttu-id="51b49-429">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-429">✔</span></span>
<span data-ttu-id="51b49-430">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="51b49-430">LineString.StartPoint</span></span> | <span data-ttu-id="51b49-431">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-431">✔</span></span> | <span data-ttu-id="51b49-432">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-432">✔</span></span> | <span data-ttu-id="51b49-433">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-433">✔</span></span> | <span data-ttu-id="51b49-434">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-434">✔</span></span>
<span data-ttu-id="51b49-435">MultiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="51b49-435">MultiLineString.IsClosed</span></span> | <span data-ttu-id="51b49-436">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-436">✔</span></span> | <span data-ttu-id="51b49-437">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-437">✔</span></span> | <span data-ttu-id="51b49-438">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-438">✔</span></span> | <span data-ttu-id="51b49-439">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-439">✔</span></span>
<span data-ttu-id="51b49-440">Punkt. M</span><span class="sxs-lookup"><span data-stu-id="51b49-440">Point.M</span></span> | <span data-ttu-id="51b49-441">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-441">✔</span></span> | <span data-ttu-id="51b49-442">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-442">✔</span></span> | <span data-ttu-id="51b49-443">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-443">✔</span></span> | <span data-ttu-id="51b49-444">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-444">✔</span></span>
<span data-ttu-id="51b49-445">Point. X</span><span class="sxs-lookup"><span data-stu-id="51b49-445">Point.X</span></span> | <span data-ttu-id="51b49-446">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-446">✔</span></span> | <span data-ttu-id="51b49-447">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-447">✔</span></span> | <span data-ttu-id="51b49-448">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-448">✔</span></span> | <span data-ttu-id="51b49-449">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-449">✔</span></span>
<span data-ttu-id="51b49-450">Punkt. Y</span><span class="sxs-lookup"><span data-stu-id="51b49-450">Point.Y</span></span> | <span data-ttu-id="51b49-451">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-451">✔</span></span> | <span data-ttu-id="51b49-452">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-452">✔</span></span> | <span data-ttu-id="51b49-453">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-453">✔</span></span> | <span data-ttu-id="51b49-454">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-454">✔</span></span>
<span data-ttu-id="51b49-455">Punkt. Z</span><span class="sxs-lookup"><span data-stu-id="51b49-455">Point.Z</span></span> | <span data-ttu-id="51b49-456">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-456">✔</span></span> | <span data-ttu-id="51b49-457">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-457">✔</span></span> | <span data-ttu-id="51b49-458">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-458">✔</span></span> | <span data-ttu-id="51b49-459">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-459">✔</span></span>
<span data-ttu-id="51b49-460">Polygon. ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="51b49-460">Polygon.ExteriorRing</span></span> | <span data-ttu-id="51b49-461">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-461">✔</span></span> | <span data-ttu-id="51b49-462">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-462">✔</span></span> | <span data-ttu-id="51b49-463">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-463">✔</span></span> | <span data-ttu-id="51b49-464">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-464">✔</span></span>
<span data-ttu-id="51b49-465">Polygon. getinteriorringn (int)</span><span class="sxs-lookup"><span data-stu-id="51b49-465">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="51b49-466">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-466">✔</span></span> | <span data-ttu-id="51b49-467">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-467">✔</span></span> | <span data-ttu-id="51b49-468">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-468">✔</span></span> | <span data-ttu-id="51b49-469">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-469">✔</span></span>
<span data-ttu-id="51b49-470">Polygon. numinteriorrings</span><span class="sxs-lookup"><span data-stu-id="51b49-470">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="51b49-471">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-471">✔</span></span> | <span data-ttu-id="51b49-472">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-472">✔</span></span> | <span data-ttu-id="51b49-473">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-473">✔</span></span> | <span data-ttu-id="51b49-474">✔</span><span class="sxs-lookup"><span data-stu-id="51b49-474">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51b49-475">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="51b49-475">Additional resources</span></span>

* [<span data-ttu-id="51b49-476">Räumliche Daten in SQL Server</span><span class="sxs-lookup"><span data-stu-id="51b49-476">Spatial Data in SQL Server</span></span>](/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="51b49-477">Spatialite-Homepage</span><span class="sxs-lookup"><span data-stu-id="51b49-477">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="51b49-478">Räumliche Dokumentation zu npgsql</span><span class="sxs-lookup"><span data-stu-id="51b49-478">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="51b49-479">PostGIS-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="51b49-479">PostGIS Documentation</span></span>](https://postgis.net/documentation/)
