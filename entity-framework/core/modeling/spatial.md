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
# <a name="spatial-data"></a><span data-ttu-id="88d8c-103">Räumliche Daten</span><span class="sxs-lookup"><span data-stu-id="88d8c-103">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="88d8c-104">Diese Funktion wurde in EF Core 2,2 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="88d8c-104">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="88d8c-105">Räumliche Daten repräsentieren den physischen Speicherort und die Form von Objekten.</span><span class="sxs-lookup"><span data-stu-id="88d8c-105">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="88d8c-106">Viele Datenbanken unterstützen diese Art von Daten, damit Sie zusammen mit anderen Daten indiziert und abgefragt werden können.</span><span class="sxs-lookup"><span data-stu-id="88d8c-106">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="88d8c-107">Häufige Szenarien umfassen das Abfragen von Objekten in einer bestimmten Entfernung von einem Speicherort oder das Auswählen des Objekts, dessen Rahmen eine bestimmte Position enthält.</span><span class="sxs-lookup"><span data-stu-id="88d8c-107">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="88d8c-108">EF Core unterstützt die Zuordnung räumlicher Datentypen mithilfe der räumlichen Bibliothek [nettopologysuite](https://github.com/NetTopologySuite/NetTopologySuite) .</span><span class="sxs-lookup"><span data-stu-id="88d8c-108">EF Core supports mapping to spatial data types using the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="88d8c-109">Installation</span><span class="sxs-lookup"><span data-stu-id="88d8c-109">Installing</span></span>

<span data-ttu-id="88d8c-110">Um räumliche Daten mit EF Core verwenden zu können, müssen Sie das entsprechende unterstützende nuget-Paket installieren.</span><span class="sxs-lookup"><span data-stu-id="88d8c-110">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="88d8c-111">Welches Paket installiert werden muss, hängt vom verwendeten Anbieter ab.</span><span class="sxs-lookup"><span data-stu-id="88d8c-111">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="88d8c-112">EF Core Anbieter</span><span class="sxs-lookup"><span data-stu-id="88d8c-112">EF Core Provider</span></span>                        | <span data-ttu-id="88d8c-113">Räumliches nuget-Paket</span><span class="sxs-lookup"><span data-stu-id="88d8c-113">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="88d8c-114">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="88d8c-114">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="88d8c-115">Microsoft. entityframeworkcore. SqlServer. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="88d8c-115">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="88d8c-116">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="88d8c-116">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="88d8c-117">Microsoft. entityframeworkcore. sqlite. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="88d8c-117">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="88d8c-118">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="88d8c-118">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="88d8c-119">Nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="88d8c-119">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="88d8c-120">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="88d8c-120">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="88d8c-121">Npgsql. entityframeworkcore. PostgreSQL. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="88d8c-121">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)

## <a name="reverse-engineering"></a><span data-ttu-id="88d8c-122">Reverse Engineering</span><span class="sxs-lookup"><span data-stu-id="88d8c-122">Reverse engineering</span></span>

<span data-ttu-id="88d8c-123">Die räumlichen nuget-Pakete ermöglichen auch die [Reverse Engineering](xref:core/managing-schemas/scaffolding) von Modellen mit räumlichen Eigenschaften. Sie müssen das Paket jedoch ***vor*** dem Ausführen von `Scaffold-DbContext` oder installieren `dotnet ef dbcontext scaffold` .</span><span class="sxs-lookup"><span data-stu-id="88d8c-123">The spatial NuGet packages also enable [reverse engineering](xref:core/managing-schemas/scaffolding) models with spatial properties, but you need to install the package ***before*** running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="88d8c-124">Wenn Sie dies nicht tun, erhalten Sie Warnungen, wenn Sie keine Typzuordnungen für die Spalten finden und die Spalten übersprungen werden.</span><span class="sxs-lookup"><span data-stu-id="88d8c-124">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="nettopologysuite-nts"></a><span data-ttu-id="88d8c-125">Nettopologysuite (NTS)</span><span class="sxs-lookup"><span data-stu-id="88d8c-125">NetTopologySuite (NTS)</span></span>

<span data-ttu-id="88d8c-126">Nettopologysuite ist eine räumliche Bibliothek für .net.</span><span class="sxs-lookup"><span data-stu-id="88d8c-126">NetTopologySuite is a spatial library for .NET.</span></span> <span data-ttu-id="88d8c-127">EF Core ermöglicht die Zuordnung von räumlichen Datentypen in der Datenbank mithilfe von NTS-Typen in Ihrem Modell.</span><span class="sxs-lookup"><span data-stu-id="88d8c-127">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="88d8c-128">Um die Zuordnung räumlicher Typen über NTS zu ermöglichen, müssen Sie die usenettopologysuite-Methode für den dbcontext Options-Generator des Anbieters abrufen.</span><span class="sxs-lookup"><span data-stu-id="88d8c-128">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="88d8c-129">Beispielsweise können Sie mit SQL Server wie folgt bezeichnen.</span><span class="sxs-lookup"><span data-stu-id="88d8c-129">For example, with SQL Server you'd call it like this.</span></span>

``` csharp
optionsBuilder.UseSqlServer(
    @"Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=WideWorldImporters",
    x => x.UseNetTopologySuite());
```

<span data-ttu-id="88d8c-130">Es gibt mehrere räumliche Datentypen.</span><span class="sxs-lookup"><span data-stu-id="88d8c-130">There are several spatial data types.</span></span> <span data-ttu-id="88d8c-131">Welcher Typ Sie verwenden, hängt von den Formen der Formen ab, die Sie zulassen möchten.</span><span class="sxs-lookup"><span data-stu-id="88d8c-131">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="88d8c-132">Hier ist die Hierarchie der NTS-Typen, die Sie für Eigenschaften im Modell verwenden können.</span><span class="sxs-lookup"><span data-stu-id="88d8c-132">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="88d8c-133">Sie befinden sich im- `NetTopologySuite.Geometries` Namespace.</span><span class="sxs-lookup"><span data-stu-id="88d8c-133">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="88d8c-134">Geometrie</span><span class="sxs-lookup"><span data-stu-id="88d8c-134">Geometry</span></span>
  * <span data-ttu-id="88d8c-135">Point</span><span class="sxs-lookup"><span data-stu-id="88d8c-135">Point</span></span>
  * <span data-ttu-id="88d8c-136">LineString</span><span class="sxs-lookup"><span data-stu-id="88d8c-136">LineString</span></span>
  * <span data-ttu-id="88d8c-137">Polygon</span><span class="sxs-lookup"><span data-stu-id="88d8c-137">Polygon</span></span>
  * <span data-ttu-id="88d8c-138">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="88d8c-138">GeometryCollection</span></span>
    * <span data-ttu-id="88d8c-139">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="88d8c-139">MultiPoint</span></span>
    * <span data-ttu-id="88d8c-140">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="88d8c-140">MultiLineString</span></span>
    * <span data-ttu-id="88d8c-141">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="88d8c-141">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="88d8c-142">Circularstring, compoundcurve und curepolygon werden von NTS nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="88d8c-142">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="88d8c-143">Mit dem basisgeometry-Typ kann jeder Typ von Form von der-Eigenschaft angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="88d8c-143">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

<span data-ttu-id="88d8c-144">Die folgenden Entitäts Klassen können verwendet werden, um Tabellen in der [Beispieldatenbank Wide World Importern](https://go.microsoft.com/fwlink/?LinkID=800630)zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="88d8c-144">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

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

### <a name="creating-values"></a><span data-ttu-id="88d8c-145">Erstellen von Werten</span><span class="sxs-lookup"><span data-stu-id="88d8c-145">Creating values</span></span>

<span data-ttu-id="88d8c-146">Sie können Konstruktoren zum Erstellen von Geometry-Objekten verwenden. Allerdings empfiehlt es sich, stattdessen eine Geometry-Factory zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="88d8c-146">You can use constructors to create geometry objects; however, NTS recommends using a geometry factory instead.</span></span> <span data-ttu-id="88d8c-147">Auf diese Weise können Sie ein Standardmäßiges SRID angeben (das von den Koordinaten verwendete räumliche Verweis System) und Ihnen die Kontrolle über erweiterte Dinge wie das Genauigkeits Modell (verwendet während der Berechnungen) und die Koordinaten Sequenz (bestimmt, welche ordinates--Dimensionen und Measures--verfügbar sind).</span><span class="sxs-lookup"><span data-stu-id="88d8c-147">This lets you specify a default SRID (the spatial reference system used by the coordinates) and gives you control over more advanced things like the precision model (used during calculations) and the coordinate sequence (determines which ordinates--dimensions and measures--are available).</span></span>

``` csharp
var geometryFactory = NtsGeometryServices.Instance.CreateGeometryFactory(srid: 4326);
var currentLocation = geometryFactory.CreatePoint(new Coordinate(-122.121512, 47.6739882));
```

> [!NOTE]
> <span data-ttu-id="88d8c-148">4326 bezieht sich auf WGS 84, einen in GPS und anderen geografischen Systemen verwendeten Standard.</span><span class="sxs-lookup"><span data-stu-id="88d8c-148">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

### <a name="longitude-and-latitude"></a><span data-ttu-id="88d8c-149">Längen-und Breitengrad</span><span class="sxs-lookup"><span data-stu-id="88d8c-149">Longitude and Latitude</span></span>

<span data-ttu-id="88d8c-150">Die Koordinaten in den NTS sind X-und Y-Werte.</span><span class="sxs-lookup"><span data-stu-id="88d8c-150">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="88d8c-151">Um Längen-und Breitengrad darzustellen, verwenden Sie X für Längengrad und Y für Breitengrad.</span><span class="sxs-lookup"><span data-stu-id="88d8c-151">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="88d8c-152">Beachten Sie, dass **backwards** dies nicht das `latitude, longitude` Format hat, in dem normalerweise diese Werte angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="88d8c-152">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

### <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="88d8c-153">SRID wird bei Client Vorgängen ignoriert.</span><span class="sxs-lookup"><span data-stu-id="88d8c-153">SRID Ignored during client operations</span></span>

<span data-ttu-id="88d8c-154">NTS ignoriert SRID-Werte während des Vorgangs.</span><span class="sxs-lookup"><span data-stu-id="88d8c-154">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="88d8c-155">Es wird von einem planaren Koordinatensystem ausgegangen.</span><span class="sxs-lookup"><span data-stu-id="88d8c-155">It assumes a planar coordinate system.</span></span> <span data-ttu-id="88d8c-156">Dies bedeutet Folgendes: Wenn Sie Koordinaten in Bezug auf Längengrad und Breitengrad angeben, werden einige vom Client ausgewertete Werte wie "Distance", "length" und "Area" in Grad und nicht in "Meter" angegeben.</span><span class="sxs-lookup"><span data-stu-id="88d8c-156">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="88d8c-157">Wenn Sie aussagekräftigere Werte benötigen, müssen Sie die Koordinaten zunächst mithilfe einer Bibliothek wie [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) auf ein anderes Koordinatensystem projizieren, bevor Sie diese Werte berechnen.</span><span class="sxs-lookup"><span data-stu-id="88d8c-157">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="88d8c-158">Wenn ein Vorgang vom Server durch EF Core über SQL ausgewertet wird, wird die Einheit des Ergebnisses von der Datenbank bestimmt.</span><span class="sxs-lookup"><span data-stu-id="88d8c-158">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="88d8c-159">Im folgenden finden Sie ein Beispiel für die Verwendung von ProjNet4GeoAPI, um den Abstand zwischen zwei Städten zu berechnen.</span><span class="sxs-lookup"><span data-stu-id="88d8c-159">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

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

## <a name="querying-data"></a><span data-ttu-id="88d8c-160">Abfrage von Daten</span><span class="sxs-lookup"><span data-stu-id="88d8c-160">Querying Data</span></span>

<span data-ttu-id="88d8c-161">In LINQ werden die NTS-Methoden und-Eigenschaften, die als Datenbankfunktionen verfügbar sind, in SQL übersetzt.</span><span class="sxs-lookup"><span data-stu-id="88d8c-161">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="88d8c-162">Beispielsweise werden die Distance-Methode und die-Methode in den folgenden Abfragen übersetzt.</span><span class="sxs-lookup"><span data-stu-id="88d8c-162">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="88d8c-163">Die Tabelle am Ende dieses Artikels zeigt, welche Member von verschiedenen EF Core Anbietern unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="88d8c-163">The table at the end of this article shows which members are supported by various EF Core providers.</span></span>

``` csharp
var nearestCity = db.Cities
    .OrderBy(c => c.Location.Distance(currentLocation))
    .FirstOrDefault();

var currentCountry = db.Countries
    .FirstOrDefault(c => c.Border.Contains(currentLocation));
```

## <a name="sql-server"></a><span data-ttu-id="88d8c-164">SQL Server</span><span class="sxs-lookup"><span data-stu-id="88d8c-164">SQL Server</span></span>

<span data-ttu-id="88d8c-165">Wenn Sie SQL Server verwenden, müssen Sie einige zusätzliche Punkte beachten.</span><span class="sxs-lookup"><span data-stu-id="88d8c-165">If you're using SQL Server, there are some additional things you should be aware of.</span></span>

### <a name="geography-or-geometry"></a><span data-ttu-id="88d8c-166">Geography oder Geometry</span><span class="sxs-lookup"><span data-stu-id="88d8c-166">Geography or geometry</span></span>

<span data-ttu-id="88d8c-167">Standardmäßig sind räumliche Eigenschaften `geography` Spalten in SQL Server zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="88d8c-167">By default, spatial properties are mapped to `geography` columns in SQL Server.</span></span> <span data-ttu-id="88d8c-168">Konfigurieren Sie `geometry` [den Spaltentyp](xref:core/modeling/entity-properties#column-data-types) in Ihrem Modell, um zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="88d8c-168">To use `geometry`, [configure the column type](xref:core/modeling/entity-properties#column-data-types) in your model.</span></span>

### <a name="geography-polygon-rings"></a><span data-ttu-id="88d8c-169">Polygon-Polygon Ringe</span><span class="sxs-lookup"><span data-stu-id="88d8c-169">Geography polygon rings</span></span>

<span data-ttu-id="88d8c-170">Wenn der `geography` Spaltentyp verwendet wird, werden von SQL Server zusätzliche Anforderungen an den äußeren Ring (oder die Shell) und die inneren Ringe (oder Löcher) auferlegt.</span><span class="sxs-lookup"><span data-stu-id="88d8c-170">When using the `geography` column type, SQL Server imposes additional requirements on the exterior ring (or shell) and interior rings (or holes).</span></span> <span data-ttu-id="88d8c-171">Der äußere Ring muss gegen den Uhrzeigersinn und die inneren Ringe im Uhrzeigersinn ausgerichtet werden.</span><span class="sxs-lookup"><span data-stu-id="88d8c-171">The exterior ring must be oriented counterclockwise and the interior rings clockwise.</span></span> <span data-ttu-id="88d8c-172">NTS überprüft dies, bevor Werte an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="88d8c-172">NTS validates this before sending values to the database.</span></span>

### <a name="fullglobe"></a><span data-ttu-id="88d8c-173">FullGlobe</span><span class="sxs-lookup"><span data-stu-id="88d8c-173">FullGlobe</span></span>

<span data-ttu-id="88d8c-174">SQL Server weist einen nicht standardmäßigen Geometry-Typ auf, der den vollständigen Globus darstellt, wenn der Spaltentyp verwendet wird `geography` .</span><span class="sxs-lookup"><span data-stu-id="88d8c-174">SQL Server has a non-standard geometry type to represent the full globe when using the `geography` column type.</span></span> <span data-ttu-id="88d8c-175">Außerdem bietet es eine Möglichkeit, Polygone auf der ganzen Welt (ohne äußeren Ring) darzustellen.</span><span class="sxs-lookup"><span data-stu-id="88d8c-175">It also has a way to represent polygons based on the full globe (without an exterior ring).</span></span> <span data-ttu-id="88d8c-176">Keines dieser beiden wird von NTS unterstützt.</span><span class="sxs-lookup"><span data-stu-id="88d8c-176">Neither of these are supported by NTS.</span></span>

> [!WARNING]
> <span data-ttu-id="88d8c-177">Fullglobe und Polygone, die darauf basieren, werden von NTS nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="88d8c-177">FullGlobe and polygons based on it aren't supported by NTS.</span></span>

## <a name="sqlite"></a><span data-ttu-id="88d8c-178">SQLite</span><span class="sxs-lookup"><span data-stu-id="88d8c-178">SQLite</span></span>

<span data-ttu-id="88d8c-179">Hier finden Sie einige zusätzliche Informationen für diejenigen, die SQLite verwenden.</span><span class="sxs-lookup"><span data-stu-id="88d8c-179">Here is some additional information for those using SQLite.</span></span>

### <a name="installing-spatialite"></a><span data-ttu-id="88d8c-180">Installieren von spatialite</span><span class="sxs-lookup"><span data-stu-id="88d8c-180">Installing SpatiaLite</span></span>

<span data-ttu-id="88d8c-181">Unter Windows wird die native mod_spatialite Bibliothek als eine nuget-Paketabhängigkeit verteilt.</span><span class="sxs-lookup"><span data-stu-id="88d8c-181">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="88d8c-182">Andere Plattformen müssen Sie separat installieren.</span><span class="sxs-lookup"><span data-stu-id="88d8c-182">Other platforms need to install it separately.</span></span> <span data-ttu-id="88d8c-183">Dies erfolgt in der Regel mithilfe eines Softwarepaket-Managers.</span><span class="sxs-lookup"><span data-stu-id="88d8c-183">This is typically done using a software package manager.</span></span> <span data-ttu-id="88d8c-184">Beispielsweise können Sie apt unter Ubuntu und Homebrew unter MacOS verwenden.</span><span class="sxs-lookup"><span data-stu-id="88d8c-184">For example, you can use APT on Ubuntu and Homebrew on MacOS.</span></span>

``` sh
# Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="88d8c-185">Leider sind neuere Versionen von proj (eine Abhängigkeit von spatialite) mit dem standardmäßigen [sqlitepclraw-Bundle](/dotnet/standard/data/sqlite/custom-versions#bundles)von EF nicht kompatibel.</span><span class="sxs-lookup"><span data-stu-id="88d8c-185">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="88d8c-186">Sie können dieses Problem umgehen, indem Sie entweder einen benutzerdefinierten [sqlitepclraw-Anbieter](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) erstellen, der die SQLite-Bibliothek des Systems verwendet, oder Sie können einen benutzerdefinierten Build von spatialite zum Deaktivieren der proj-Unterstützung installieren.</span><span class="sxs-lookup"><span data-stu-id="88d8c-186">You can work around this by either creating a custom [SQLitePCLRaw provider](/dotnet/standard/data/sqlite/custom-versions#sqlitepclraw-providers) that uses the system SQLite library, or you can install a custom build of SpatiaLite disabling PROJ support.</span></span>

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

### <a name="configuring-srid"></a><span data-ttu-id="88d8c-187">Konfigurieren von SRID</span><span class="sxs-lookup"><span data-stu-id="88d8c-187">Configuring SRID</span></span>

<span data-ttu-id="88d8c-188">In spatialite müssen Spalten eine SRID pro Spalte angeben.</span><span class="sxs-lookup"><span data-stu-id="88d8c-188">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="88d8c-189">Der Standard-SRID ist `0` .</span><span class="sxs-lookup"><span data-stu-id="88d8c-189">The default SRID is `0`.</span></span> <span data-ttu-id="88d8c-190">Geben Sie einen anderen SRID mithilfe der forsqlitehassrid-Methode an.</span><span class="sxs-lookup"><span data-stu-id="88d8c-190">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

### <a name="dimension"></a><span data-ttu-id="88d8c-191">Dimension</span><span class="sxs-lookup"><span data-stu-id="88d8c-191">Dimension</span></span>

<span data-ttu-id="88d8c-192">Ähnlich wie SRID wird auch die Dimension (oder ordinates) einer Spalte als Teil der Spalte angegeben.</span><span class="sxs-lookup"><span data-stu-id="88d8c-192">Similar to SRID, a column's dimension (or ordinates) is also specified as part of the column.</span></span> <span data-ttu-id="88d8c-193">Die Standard Ordinate sind X und Y. Aktivieren Sie zusätzliche Ordinate (Z und M) mithilfe der forsqlitehasdimension-Methode.</span><span class="sxs-lookup"><span data-stu-id="88d8c-193">The default ordinates are X and Y. Enable additional ordinates (Z and M) using the ForSqliteHasDimension method.</span></span>

``` csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasDimension(Ordinates.XYZ);
```

## <a name="translated-operations"></a><span data-ttu-id="88d8c-194">Übersetzte Vorgänge</span><span class="sxs-lookup"><span data-stu-id="88d8c-194">Translated Operations</span></span>

<span data-ttu-id="88d8c-195">Diese Tabelle zeigt, welche NTS-Elemente von den einzelnen EF Core Anbietern in SQL übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="88d8c-195">This table shows which NTS members are translated into SQL by each EF Core provider.</span></span>

<span data-ttu-id="88d8c-196">Nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="88d8c-196">NetTopologySuite</span></span> | <span data-ttu-id="88d8c-197">SQL Server (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-197">SQL Server (geometry)</span></span> | <span data-ttu-id="88d8c-198">SQL Server (Geografie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-198">SQL Server (geography)</span></span> | <span data-ttu-id="88d8c-199">SQLite</span><span class="sxs-lookup"><span data-stu-id="88d8c-199">SQLite</span></span> | <span data-ttu-id="88d8c-200">Npgsql</span><span class="sxs-lookup"><span data-stu-id="88d8c-200">Npgsql</span></span>
--- |:---:|:---:|:---:|:---:
<span data-ttu-id="88d8c-201">Geometry. Area</span><span class="sxs-lookup"><span data-stu-id="88d8c-201">Geometry.Area</span></span> | <span data-ttu-id="88d8c-202">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-202">✔</span></span> | <span data-ttu-id="88d8c-203">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-203">✔</span></span> | <span data-ttu-id="88d8c-204">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-204">✔</span></span> | <span data-ttu-id="88d8c-205">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-205">✔</span></span>
<span data-ttu-id="88d8c-206">Geometry. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="88d8c-206">Geometry.AsBinary()</span></span> | <span data-ttu-id="88d8c-207">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-207">✔</span></span> | <span data-ttu-id="88d8c-208">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-208">✔</span></span> | <span data-ttu-id="88d8c-209">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-209">✔</span></span> | <span data-ttu-id="88d8c-210">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-210">✔</span></span>
<span data-ttu-id="88d8c-211">Geometry. AsText ()</span><span class="sxs-lookup"><span data-stu-id="88d8c-211">Geometry.AsText()</span></span> | <span data-ttu-id="88d8c-212">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-212">✔</span></span> | <span data-ttu-id="88d8c-213">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-213">✔</span></span> | <span data-ttu-id="88d8c-214">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-214">✔</span></span> | <span data-ttu-id="88d8c-215">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-215">✔</span></span>
<span data-ttu-id="88d8c-216">Geometry. Boundary</span><span class="sxs-lookup"><span data-stu-id="88d8c-216">Geometry.Boundary</span></span> | <span data-ttu-id="88d8c-217">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-217">✔</span></span> | | <span data-ttu-id="88d8c-218">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-218">✔</span></span> | <span data-ttu-id="88d8c-219">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-219">✔</span></span>
<span data-ttu-id="88d8c-220">Geometry. Buffer (Double)</span><span class="sxs-lookup"><span data-stu-id="88d8c-220">Geometry.Buffer(double)</span></span> | <span data-ttu-id="88d8c-221">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-221">✔</span></span> | <span data-ttu-id="88d8c-222">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-222">✔</span></span> | <span data-ttu-id="88d8c-223">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-223">✔</span></span> | <span data-ttu-id="88d8c-224">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-224">✔</span></span>
<span data-ttu-id="88d8c-225">Geometry. Buffer (Double, int)</span><span class="sxs-lookup"><span data-stu-id="88d8c-225">Geometry.Buffer(double, int)</span></span> | | | <span data-ttu-id="88d8c-226">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-226">✔</span></span> | <span data-ttu-id="88d8c-227">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-227">✔</span></span>
<span data-ttu-id="88d8c-228">Geometry. Centroid</span><span class="sxs-lookup"><span data-stu-id="88d8c-228">Geometry.Centroid</span></span> | <span data-ttu-id="88d8c-229">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-229">✔</span></span> | | <span data-ttu-id="88d8c-230">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-230">✔</span></span> | <span data-ttu-id="88d8c-231">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-231">✔</span></span>
<span data-ttu-id="88d8c-232">Geometry. enthält (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-232">Geometry.Contains(Geometry)</span></span> | <span data-ttu-id="88d8c-233">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-233">✔</span></span> | <span data-ttu-id="88d8c-234">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-234">✔</span></span> | <span data-ttu-id="88d8c-235">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-235">✔</span></span> | <span data-ttu-id="88d8c-236">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-236">✔</span></span>
<span data-ttu-id="88d8c-237">Geometry. config-Hülle ()</span><span class="sxs-lookup"><span data-stu-id="88d8c-237">Geometry.ConvexHull()</span></span> | <span data-ttu-id="88d8c-238">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-238">✔</span></span> | <span data-ttu-id="88d8c-239">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-239">✔</span></span> | <span data-ttu-id="88d8c-240">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-240">✔</span></span> | <span data-ttu-id="88d8c-241">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-241">✔</span></span>
<span data-ttu-id="88d8c-242">Geometry. coveredby (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-242">Geometry.CoveredBy(Geometry)</span></span> | | | <span data-ttu-id="88d8c-243">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-243">✔</span></span> | <span data-ttu-id="88d8c-244">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-244">✔</span></span>
<span data-ttu-id="88d8c-245">Geometry. Cover (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-245">Geometry.Covers(Geometry)</span></span> | | | <span data-ttu-id="88d8c-246">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-246">✔</span></span> | <span data-ttu-id="88d8c-247">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-247">✔</span></span>
<span data-ttu-id="88d8c-248">Geometry. Kreuze (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-248">Geometry.Crosses(Geometry)</span></span> | <span data-ttu-id="88d8c-249">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-249">✔</span></span> | | <span data-ttu-id="88d8c-250">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-250">✔</span></span> | <span data-ttu-id="88d8c-251">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-251">✔</span></span>
<span data-ttu-id="88d8c-252">Geometry. Difference (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-252">Geometry.Difference(Geometry)</span></span> | <span data-ttu-id="88d8c-253">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-253">✔</span></span> | <span data-ttu-id="88d8c-254">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-254">✔</span></span> | <span data-ttu-id="88d8c-255">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-255">✔</span></span> | <span data-ttu-id="88d8c-256">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-256">✔</span></span>
<span data-ttu-id="88d8c-257">Geometry. Dimension</span><span class="sxs-lookup"><span data-stu-id="88d8c-257">Geometry.Dimension</span></span> | <span data-ttu-id="88d8c-258">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-258">✔</span></span> | <span data-ttu-id="88d8c-259">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-259">✔</span></span> | <span data-ttu-id="88d8c-260">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-260">✔</span></span> | <span data-ttu-id="88d8c-261">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-261">✔</span></span>
<span data-ttu-id="88d8c-262">Geometry. disjoint (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-262">Geometry.Disjoint(Geometry)</span></span> | <span data-ttu-id="88d8c-263">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-263">✔</span></span> | <span data-ttu-id="88d8c-264">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-264">✔</span></span> | <span data-ttu-id="88d8c-265">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-265">✔</span></span> | <span data-ttu-id="88d8c-266">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-266">✔</span></span>
<span data-ttu-id="88d8c-267">Geometry. distance (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-267">Geometry.Distance(Geometry)</span></span> | <span data-ttu-id="88d8c-268">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-268">✔</span></span> | <span data-ttu-id="88d8c-269">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-269">✔</span></span> | <span data-ttu-id="88d8c-270">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-270">✔</span></span> | <span data-ttu-id="88d8c-271">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-271">✔</span></span>
<span data-ttu-id="88d8c-272">Geometry. Umschlag</span><span class="sxs-lookup"><span data-stu-id="88d8c-272">Geometry.Envelope</span></span> | <span data-ttu-id="88d8c-273">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-273">✔</span></span> | | <span data-ttu-id="88d8c-274">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-274">✔</span></span> | <span data-ttu-id="88d8c-275">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-275">✔</span></span>
<span data-ttu-id="88d8c-276">Geometry. EqualsExact (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-276">Geometry.EqualsExact(Geometry)</span></span> | | | | <span data-ttu-id="88d8c-277">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-277">✔</span></span>
<span data-ttu-id="88d8c-278">Geometry. equalstopologisch (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-278">Geometry.EqualsTopologically(Geometry)</span></span> | <span data-ttu-id="88d8c-279">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-279">✔</span></span> | <span data-ttu-id="88d8c-280">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-280">✔</span></span> | <span data-ttu-id="88d8c-281">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-281">✔</span></span> | <span data-ttu-id="88d8c-282">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-282">✔</span></span>
<span data-ttu-id="88d8c-283">Geometry. geometrytype</span><span class="sxs-lookup"><span data-stu-id="88d8c-283">Geometry.GeometryType</span></span> | <span data-ttu-id="88d8c-284">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-284">✔</span></span> | <span data-ttu-id="88d8c-285">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-285">✔</span></span> | <span data-ttu-id="88d8c-286">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-286">✔</span></span> | <span data-ttu-id="88d8c-287">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-287">✔</span></span>
<span data-ttu-id="88d8c-288">Geometry. getgeometryn (int)</span><span class="sxs-lookup"><span data-stu-id="88d8c-288">Geometry.GetGeometryN(int)</span></span> | <span data-ttu-id="88d8c-289">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-289">✔</span></span> | | <span data-ttu-id="88d8c-290">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-290">✔</span></span> | <span data-ttu-id="88d8c-291">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-291">✔</span></span>
<span data-ttu-id="88d8c-292">Geometry. interiorpoint</span><span class="sxs-lookup"><span data-stu-id="88d8c-292">Geometry.InteriorPoint</span></span> | <span data-ttu-id="88d8c-293">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-293">✔</span></span> | | <span data-ttu-id="88d8c-294">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-294">✔</span></span> | <span data-ttu-id="88d8c-295">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-295">✔</span></span>
<span data-ttu-id="88d8c-296">Geometry. Schnittmenge (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-296">Geometry.Intersection(Geometry)</span></span> | <span data-ttu-id="88d8c-297">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-297">✔</span></span> | <span data-ttu-id="88d8c-298">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-298">✔</span></span> | <span data-ttu-id="88d8c-299">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-299">✔</span></span> | <span data-ttu-id="88d8c-300">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-300">✔</span></span>
<span data-ttu-id="88d8c-301">Geometry. intersekten (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-301">Geometry.Intersects(Geometry)</span></span> | <span data-ttu-id="88d8c-302">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-302">✔</span></span> | <span data-ttu-id="88d8c-303">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-303">✔</span></span> | <span data-ttu-id="88d8c-304">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-304">✔</span></span> | <span data-ttu-id="88d8c-305">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-305">✔</span></span>
<span data-ttu-id="88d8c-306">Geometry. IsEmpty</span><span class="sxs-lookup"><span data-stu-id="88d8c-306">Geometry.IsEmpty</span></span> | <span data-ttu-id="88d8c-307">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-307">✔</span></span> | <span data-ttu-id="88d8c-308">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-308">✔</span></span> | <span data-ttu-id="88d8c-309">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-309">✔</span></span> | <span data-ttu-id="88d8c-310">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-310">✔</span></span>
<span data-ttu-id="88d8c-311">Geometry. IsSimple</span><span class="sxs-lookup"><span data-stu-id="88d8c-311">Geometry.IsSimple</span></span> | <span data-ttu-id="88d8c-312">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-312">✔</span></span> | | <span data-ttu-id="88d8c-313">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-313">✔</span></span> | <span data-ttu-id="88d8c-314">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-314">✔</span></span>
<span data-ttu-id="88d8c-315">Geometry. IsValid</span><span class="sxs-lookup"><span data-stu-id="88d8c-315">Geometry.IsValid</span></span> | <span data-ttu-id="88d8c-316">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-316">✔</span></span> | <span data-ttu-id="88d8c-317">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-317">✔</span></span> | <span data-ttu-id="88d8c-318">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-318">✔</span></span> | <span data-ttu-id="88d8c-319">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-319">✔</span></span>
<span data-ttu-id="88d8c-320">Geometry. iswithindistance (Geometry, Double)</span><span class="sxs-lookup"><span data-stu-id="88d8c-320">Geometry.IsWithinDistance(Geometry, double)</span></span> | <span data-ttu-id="88d8c-321">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-321">✔</span></span> | | <span data-ttu-id="88d8c-322">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-322">✔</span></span> | <span data-ttu-id="88d8c-323">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-323">✔</span></span>
<span data-ttu-id="88d8c-324">Geometry. length</span><span class="sxs-lookup"><span data-stu-id="88d8c-324">Geometry.Length</span></span> | <span data-ttu-id="88d8c-325">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-325">✔</span></span> | <span data-ttu-id="88d8c-326">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-326">✔</span></span> | <span data-ttu-id="88d8c-327">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-327">✔</span></span> | <span data-ttu-id="88d8c-328">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-328">✔</span></span>
<span data-ttu-id="88d8c-329">Geometry. numgeometries</span><span class="sxs-lookup"><span data-stu-id="88d8c-329">Geometry.NumGeometries</span></span> | <span data-ttu-id="88d8c-330">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-330">✔</span></span> | <span data-ttu-id="88d8c-331">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-331">✔</span></span> | <span data-ttu-id="88d8c-332">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-332">✔</span></span> | <span data-ttu-id="88d8c-333">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-333">✔</span></span>
<span data-ttu-id="88d8c-334">Geometry. NumPoints</span><span class="sxs-lookup"><span data-stu-id="88d8c-334">Geometry.NumPoints</span></span> | <span data-ttu-id="88d8c-335">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-335">✔</span></span> | <span data-ttu-id="88d8c-336">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-336">✔</span></span> | <span data-ttu-id="88d8c-337">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-337">✔</span></span> | <span data-ttu-id="88d8c-338">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-338">✔</span></span>
<span data-ttu-id="88d8c-339">Geometry. ogcgeometrytype</span><span class="sxs-lookup"><span data-stu-id="88d8c-339">Geometry.OgcGeometryType</span></span> | <span data-ttu-id="88d8c-340">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-340">✔</span></span> | <span data-ttu-id="88d8c-341">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-341">✔</span></span> | <span data-ttu-id="88d8c-342">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-342">✔</span></span> | <span data-ttu-id="88d8c-343">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-343">✔</span></span>
<span data-ttu-id="88d8c-344">Geometry. Überlappung (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-344">Geometry.Overlaps(Geometry)</span></span> | <span data-ttu-id="88d8c-345">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-345">✔</span></span> | <span data-ttu-id="88d8c-346">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-346">✔</span></span> | <span data-ttu-id="88d8c-347">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-347">✔</span></span> | <span data-ttu-id="88d8c-348">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-348">✔</span></span>
<span data-ttu-id="88d8c-349">Geometry. pointonsurface</span><span class="sxs-lookup"><span data-stu-id="88d8c-349">Geometry.PointOnSurface</span></span> | <span data-ttu-id="88d8c-350">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-350">✔</span></span> | | <span data-ttu-id="88d8c-351">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-351">✔</span></span> | <span data-ttu-id="88d8c-352">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-352">✔</span></span>
<span data-ttu-id="88d8c-353">Geometry. in Beziehung (Geometrie, Zeichenfolge)</span><span class="sxs-lookup"><span data-stu-id="88d8c-353">Geometry.Relate(Geometry, string)</span></span> | <span data-ttu-id="88d8c-354">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-354">✔</span></span> | | <span data-ttu-id="88d8c-355">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-355">✔</span></span> | <span data-ttu-id="88d8c-356">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-356">✔</span></span>
<span data-ttu-id="88d8c-357">Geometry. Reverse ()</span><span class="sxs-lookup"><span data-stu-id="88d8c-357">Geometry.Reverse()</span></span> | | | <span data-ttu-id="88d8c-358">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-358">✔</span></span> | <span data-ttu-id="88d8c-359">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-359">✔</span></span>
<span data-ttu-id="88d8c-360">Geometry. SRID</span><span class="sxs-lookup"><span data-stu-id="88d8c-360">Geometry.SRID</span></span> | <span data-ttu-id="88d8c-361">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-361">✔</span></span> | <span data-ttu-id="88d8c-362">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-362">✔</span></span> | <span data-ttu-id="88d8c-363">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-363">✔</span></span> | <span data-ttu-id="88d8c-364">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-364">✔</span></span>
<span data-ttu-id="88d8c-365">Geometry. symmetricdifference (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-365">Geometry.SymmetricDifference(Geometry)</span></span> | <span data-ttu-id="88d8c-366">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-366">✔</span></span> | <span data-ttu-id="88d8c-367">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-367">✔</span></span> | <span data-ttu-id="88d8c-368">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-368">✔</span></span> | <span data-ttu-id="88d8c-369">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-369">✔</span></span>
<span data-ttu-id="88d8c-370">Geometry. $ Binary ()</span><span class="sxs-lookup"><span data-stu-id="88d8c-370">Geometry.ToBinary()</span></span> | <span data-ttu-id="88d8c-371">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-371">✔</span></span> | <span data-ttu-id="88d8c-372">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-372">✔</span></span> | <span data-ttu-id="88d8c-373">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-373">✔</span></span> | <span data-ttu-id="88d8c-374">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-374">✔</span></span>
<span data-ttu-id="88d8c-375">Geometry.-Text ()</span><span class="sxs-lookup"><span data-stu-id="88d8c-375">Geometry.ToText()</span></span> | <span data-ttu-id="88d8c-376">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-376">✔</span></span> | <span data-ttu-id="88d8c-377">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-377">✔</span></span> | <span data-ttu-id="88d8c-378">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-378">✔</span></span> | <span data-ttu-id="88d8c-379">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-379">✔</span></span>
<span data-ttu-id="88d8c-380">Geometry. touches (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-380">Geometry.Touches(Geometry)</span></span> | <span data-ttu-id="88d8c-381">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-381">✔</span></span> | | <span data-ttu-id="88d8c-382">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-382">✔</span></span> | <span data-ttu-id="88d8c-383">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-383">✔</span></span>
<span data-ttu-id="88d8c-384">Geometry. Union ()</span><span class="sxs-lookup"><span data-stu-id="88d8c-384">Geometry.Union()</span></span> | | | <span data-ttu-id="88d8c-385">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-385">✔</span></span> | <span data-ttu-id="88d8c-386">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-386">✔</span></span>
<span data-ttu-id="88d8c-387">Geometry. Union (Geometrie)</span><span class="sxs-lookup"><span data-stu-id="88d8c-387">Geometry.Union(Geometry)</span></span> | <span data-ttu-id="88d8c-388">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-388">✔</span></span> | <span data-ttu-id="88d8c-389">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-389">✔</span></span> | <span data-ttu-id="88d8c-390">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-390">✔</span></span> | <span data-ttu-id="88d8c-391">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-391">✔</span></span>
<span data-ttu-id="88d8c-392">Geometry. within (Geometry)</span><span class="sxs-lookup"><span data-stu-id="88d8c-392">Geometry.Within(Geometry)</span></span> | <span data-ttu-id="88d8c-393">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-393">✔</span></span> | <span data-ttu-id="88d8c-394">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-394">✔</span></span> | <span data-ttu-id="88d8c-395">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-395">✔</span></span> | <span data-ttu-id="88d8c-396">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-396">✔</span></span>
<span data-ttu-id="88d8c-397">GeometryCollection. Count</span><span class="sxs-lookup"><span data-stu-id="88d8c-397">GeometryCollection.Count</span></span> | <span data-ttu-id="88d8c-398">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-398">✔</span></span> | <span data-ttu-id="88d8c-399">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-399">✔</span></span> | <span data-ttu-id="88d8c-400">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-400">✔</span></span> | <span data-ttu-id="88d8c-401">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-401">✔</span></span>
<span data-ttu-id="88d8c-402">GeometryCollection [int]</span><span class="sxs-lookup"><span data-stu-id="88d8c-402">GeometryCollection[int]</span></span> | <span data-ttu-id="88d8c-403">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-403">✔</span></span> | <span data-ttu-id="88d8c-404">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-404">✔</span></span> | <span data-ttu-id="88d8c-405">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-405">✔</span></span> | <span data-ttu-id="88d8c-406">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-406">✔</span></span>
<span data-ttu-id="88d8c-407">LineString. Count</span><span class="sxs-lookup"><span data-stu-id="88d8c-407">LineString.Count</span></span> | <span data-ttu-id="88d8c-408">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-408">✔</span></span> | <span data-ttu-id="88d8c-409">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-409">✔</span></span> | <span data-ttu-id="88d8c-410">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-410">✔</span></span> | <span data-ttu-id="88d8c-411">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-411">✔</span></span>
<span data-ttu-id="88d8c-412">LineString. EndPoint</span><span class="sxs-lookup"><span data-stu-id="88d8c-412">LineString.EndPoint</span></span> | <span data-ttu-id="88d8c-413">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-413">✔</span></span> | <span data-ttu-id="88d8c-414">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-414">✔</span></span> | <span data-ttu-id="88d8c-415">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-415">✔</span></span> | <span data-ttu-id="88d8c-416">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-416">✔</span></span>
<span data-ttu-id="88d8c-417">LineString. getpointn (int)</span><span class="sxs-lookup"><span data-stu-id="88d8c-417">LineString.GetPointN(int)</span></span> | <span data-ttu-id="88d8c-418">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-418">✔</span></span> | <span data-ttu-id="88d8c-419">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-419">✔</span></span> | <span data-ttu-id="88d8c-420">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-420">✔</span></span> | <span data-ttu-id="88d8c-421">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-421">✔</span></span>
<span data-ttu-id="88d8c-422">LineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="88d8c-422">LineString.IsClosed</span></span> | <span data-ttu-id="88d8c-423">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-423">✔</span></span> | <span data-ttu-id="88d8c-424">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-424">✔</span></span> | <span data-ttu-id="88d8c-425">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-425">✔</span></span> | <span data-ttu-id="88d8c-426">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-426">✔</span></span>
<span data-ttu-id="88d8c-427">LineString. isring</span><span class="sxs-lookup"><span data-stu-id="88d8c-427">LineString.IsRing</span></span> | <span data-ttu-id="88d8c-428">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-428">✔</span></span> | | <span data-ttu-id="88d8c-429">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-429">✔</span></span> | <span data-ttu-id="88d8c-430">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-430">✔</span></span>
<span data-ttu-id="88d8c-431">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="88d8c-431">LineString.StartPoint</span></span> | <span data-ttu-id="88d8c-432">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-432">✔</span></span> | <span data-ttu-id="88d8c-433">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-433">✔</span></span> | <span data-ttu-id="88d8c-434">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-434">✔</span></span> | <span data-ttu-id="88d8c-435">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-435">✔</span></span>
<span data-ttu-id="88d8c-436">MultiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="88d8c-436">MultiLineString.IsClosed</span></span> | <span data-ttu-id="88d8c-437">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-437">✔</span></span> | <span data-ttu-id="88d8c-438">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-438">✔</span></span> | <span data-ttu-id="88d8c-439">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-439">✔</span></span> | <span data-ttu-id="88d8c-440">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-440">✔</span></span>
<span data-ttu-id="88d8c-441">Punkt. M</span><span class="sxs-lookup"><span data-stu-id="88d8c-441">Point.M</span></span> | <span data-ttu-id="88d8c-442">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-442">✔</span></span> | <span data-ttu-id="88d8c-443">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-443">✔</span></span> | <span data-ttu-id="88d8c-444">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-444">✔</span></span> | <span data-ttu-id="88d8c-445">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-445">✔</span></span>
<span data-ttu-id="88d8c-446">Point. X</span><span class="sxs-lookup"><span data-stu-id="88d8c-446">Point.X</span></span> | <span data-ttu-id="88d8c-447">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-447">✔</span></span> | <span data-ttu-id="88d8c-448">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-448">✔</span></span> | <span data-ttu-id="88d8c-449">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-449">✔</span></span> | <span data-ttu-id="88d8c-450">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-450">✔</span></span>
<span data-ttu-id="88d8c-451">Punkt. Y</span><span class="sxs-lookup"><span data-stu-id="88d8c-451">Point.Y</span></span> | <span data-ttu-id="88d8c-452">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-452">✔</span></span> | <span data-ttu-id="88d8c-453">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-453">✔</span></span> | <span data-ttu-id="88d8c-454">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-454">✔</span></span> | <span data-ttu-id="88d8c-455">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-455">✔</span></span>
<span data-ttu-id="88d8c-456">Punkt. Z</span><span class="sxs-lookup"><span data-stu-id="88d8c-456">Point.Z</span></span> | <span data-ttu-id="88d8c-457">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-457">✔</span></span> | <span data-ttu-id="88d8c-458">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-458">✔</span></span> | <span data-ttu-id="88d8c-459">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-459">✔</span></span> | <span data-ttu-id="88d8c-460">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-460">✔</span></span>
<span data-ttu-id="88d8c-461">Polygon. ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="88d8c-461">Polygon.ExteriorRing</span></span> | <span data-ttu-id="88d8c-462">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-462">✔</span></span> | <span data-ttu-id="88d8c-463">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-463">✔</span></span> | <span data-ttu-id="88d8c-464">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-464">✔</span></span> | <span data-ttu-id="88d8c-465">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-465">✔</span></span>
<span data-ttu-id="88d8c-466">Polygon. getinteriorringn (int)</span><span class="sxs-lookup"><span data-stu-id="88d8c-466">Polygon.GetInteriorRingN(int)</span></span> | <span data-ttu-id="88d8c-467">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-467">✔</span></span> | <span data-ttu-id="88d8c-468">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-468">✔</span></span> | <span data-ttu-id="88d8c-469">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-469">✔</span></span> | <span data-ttu-id="88d8c-470">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-470">✔</span></span>
<span data-ttu-id="88d8c-471">Polygon. numinteriorrings</span><span class="sxs-lookup"><span data-stu-id="88d8c-471">Polygon.NumInteriorRings</span></span> | <span data-ttu-id="88d8c-472">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-472">✔</span></span> | <span data-ttu-id="88d8c-473">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-473">✔</span></span> | <span data-ttu-id="88d8c-474">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-474">✔</span></span> | <span data-ttu-id="88d8c-475">✔</span><span class="sxs-lookup"><span data-stu-id="88d8c-475">✔</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88d8c-476">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="88d8c-476">Additional resources</span></span>

* [<span data-ttu-id="88d8c-477">Räumliche Daten in SQL Server</span><span class="sxs-lookup"><span data-stu-id="88d8c-477">Spatial Data in SQL Server</span></span>](/sql/relational-databases/spatial/spatial-data-sql-server)
* [<span data-ttu-id="88d8c-478">Spatialite-Homepage</span><span class="sxs-lookup"><span data-stu-id="88d8c-478">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="88d8c-479">Räumliche Dokumentation zu npgsql</span><span class="sxs-lookup"><span data-stu-id="88d8c-479">Npgsql Spatial Documentation</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)
* [<span data-ttu-id="88d8c-480">PostGIS-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="88d8c-480">PostGIS Documentation</span></span>](https://postgis.net/documentation/)
