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
# <a name="spatial-data-in-the-sqlite-ef-core-provider"></a><span data-ttu-id="0e8ee-103">Räumliche Daten im SQLite-EF Core-Anbieter</span><span class="sxs-lookup"><span data-stu-id="0e8ee-103">Spatial Data in the SQLite EF Core Provider</span></span>

<span data-ttu-id="0e8ee-104">Diese Seite enthält zusätzliche Informationen zur Verwendung räumlicher Daten mit dem SQLite-Datenbankanbieter.</span><span class="sxs-lookup"><span data-stu-id="0e8ee-104">This page includes additional information about using spatial data with the SQLite database provider.</span></span> <span data-ttu-id="0e8ee-105">Allgemeine Informationen zur Verwendung räumlicher Daten in EF Core finden Sie in der Haupt Dokumentation zu [räumlichen Daten](xref:core/modeling/spatial) .</span><span class="sxs-lookup"><span data-stu-id="0e8ee-105">For general information about using spatial data in EF Core, see the main [Spatial Data](xref:core/modeling/spatial) documentation.</span></span>

## <a name="installing-spatialite"></a><span data-ttu-id="0e8ee-106">Installieren von spatialite</span><span class="sxs-lookup"><span data-stu-id="0e8ee-106">Installing SpatiaLite</span></span>

<span data-ttu-id="0e8ee-107">Unter Windows wird die native mod_spatialite Bibliothek als eine nuget-Paketabhängigkeit verteilt.</span><span class="sxs-lookup"><span data-stu-id="0e8ee-107">On Windows, the native mod_spatialite library is distributed as a NuGet package dependency.</span></span> <span data-ttu-id="0e8ee-108">Andere Plattformen müssen Sie separat installieren.</span><span class="sxs-lookup"><span data-stu-id="0e8ee-108">Other platforms need to install it separately.</span></span> <span data-ttu-id="0e8ee-109">Dies erfolgt in der Regel mithilfe eines Softwarepaket-Managers.</span><span class="sxs-lookup"><span data-stu-id="0e8ee-109">This is typically done using a software package manager.</span></span> <span data-ttu-id="0e8ee-110">Beispielsweise können Sie apt für Debian und Ubuntu verwenden. und Homebrew unter MacOS.</span><span class="sxs-lookup"><span data-stu-id="0e8ee-110">For example, you can use APT on Debian and Ubuntu; and Homebrew on MacOS.</span></span>

```bash
# Debian/Ubuntu
apt-get install libsqlite3-mod-spatialite

# macOS
brew install libspatialite
```

<span data-ttu-id="0e8ee-111">Leider sind neuere Versionen von proj (eine Abhängigkeit von spatialite) mit dem standardmäßigen [sqlitepclraw-Bundle](/dotnet/standard/data/sqlite/custom-versions#bundles)von EF nicht kompatibel.</span><span class="sxs-lookup"><span data-stu-id="0e8ee-111">Unfortunately, newer versions of PROJ (a dependency of SpatiaLite) are incompatible with EF's default [SQLitePCLRaw bundle](/dotnet/standard/data/sqlite/custom-versions#bundles).</span></span> <span data-ttu-id="0e8ee-112">Sie können dieses Problem umgehen, indem Sie stattdessen die System SQLite-Bibliothek verwenden.</span><span class="sxs-lookup"><span data-stu-id="0e8ee-112">You can work around this by using the system SQLite library instead.</span></span>

```xml
<ItemGroup>
  <!-- Use bundle_sqlite3 instead with SpatiaLite on macOS and Linux -->
  <!--<PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="3.1.0" />-->
  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.Core" Version="3.1.0" />
  <PackageReference Include="SQLitePCLRaw.bundle_sqlite3" Version="2.0.4" />

  <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite" Version="3.1.0" />
</ItemGroup>
```

<span data-ttu-id="0e8ee-113">Unter **macOS**müssen Sie auch eine Umgebungsvariable festlegen, bevor Sie Ihre APP ausführen, sodass Sie die Version von SQLite von Homebrew verwendet.</span><span class="sxs-lookup"><span data-stu-id="0e8ee-113">On **macOS**, you'll also need set an environment variable before running your app so it uses Homebrew's version of SQLite.</span></span> <span data-ttu-id="0e8ee-114">In Visual Studio für Mac können Sie diese **Option Unterprojekt > Projektoptionen > > Konfigurationen ausführen > Standard** Wert festlegen.</span><span class="sxs-lookup"><span data-stu-id="0e8ee-114">In Visual Studio for Mac, you can set this under **Project > Project Options > Run > Configurations > Default**</span></span>

```bash
DYLD_LIBRARY_PATH=/usr/local/opt/sqlite/lib
```

## <a name="configuring-srid"></a><span data-ttu-id="0e8ee-115">Konfigurieren von SRID</span><span class="sxs-lookup"><span data-stu-id="0e8ee-115">Configuring SRID</span></span>

<span data-ttu-id="0e8ee-116">In spatialite müssen Spalten eine SRID pro Spalte angeben.</span><span class="sxs-lookup"><span data-stu-id="0e8ee-116">In SpatiaLite, columns need to specify an SRID per column.</span></span> <span data-ttu-id="0e8ee-117">Der Standard-SRID ist `0` .</span><span class="sxs-lookup"><span data-stu-id="0e8ee-117">The default SRID is `0`.</span></span> <span data-ttu-id="0e8ee-118">Geben Sie einen anderen SRID mithilfe der forsqlitehassrid-Methode an.</span><span class="sxs-lookup"><span data-stu-id="0e8ee-118">Specify a different SRID using the ForSqliteHasSrid method.</span></span>

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .ForSqliteHasSrid(4326);
```

> [!NOTE]
> <span data-ttu-id="0e8ee-119">4326 bezieht sich auf WGS 84, einen in GPS und anderen geografischen Systemen verwendeten Standard.</span><span class="sxs-lookup"><span data-stu-id="0e8ee-119">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

## <a name="dimension"></a><span data-ttu-id="0e8ee-120">Dimension</span><span class="sxs-lookup"><span data-stu-id="0e8ee-120">Dimension</span></span>

<span data-ttu-id="0e8ee-121">Die Standard Dimension (oder Ordinate) einer Spalte ist X und Y. Konfigurieren Sie den Spaltentyp, um zusätzliche Ordinate wie Z oder M zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="0e8ee-121">The default dimension (or ordinates) of a column is X and Y. To enable additional ordinates like Z or M, configure the column type.</span></span>

```csharp
modelBuilder.Entity<City>().Property(c => c.Location)
    .HasColumnType("POINTZ");
```

## <a name="spatial-function-mappings"></a><span data-ttu-id="0e8ee-122">Zuordnungen für räumliche Funktionen</span><span class="sxs-lookup"><span data-stu-id="0e8ee-122">Spatial function mappings</span></span>

<span data-ttu-id="0e8ee-123">Diese Tabelle zeigt, welche [nettopologysuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS)-Member in welche SQL-Funktionen übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="0e8ee-123">This table shows which [NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) members are translated into which SQL functions.</span></span>

<span data-ttu-id="0e8ee-124">.NET</span><span class="sxs-lookup"><span data-stu-id="0e8ee-124">.NET</span></span>                                        | <span data-ttu-id="0e8ee-125">SQL</span><span class="sxs-lookup"><span data-stu-id="0e8ee-125">SQL</span></span>
------------------------------------------- | ---
<span data-ttu-id="0e8ee-126">et. Feld</span><span class="sxs-lookup"><span data-stu-id="0e8ee-126">geometry.Area</span></span>                               | <span data-ttu-id="0e8ee-127">Bereich ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-127">Area(@geometry)</span></span>
<span data-ttu-id="0e8ee-128">et. AsBinary ()</span><span class="sxs-lookup"><span data-stu-id="0e8ee-128">geometry.AsBinary()</span></span>                         | <span data-ttu-id="0e8ee-129">AsBinary ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-129">AsBinary(@geometry)</span></span>
<span data-ttu-id="0e8ee-130">et. AsText ()</span><span class="sxs-lookup"><span data-stu-id="0e8ee-130">geometry.AsText()</span></span>                           | <span data-ttu-id="0e8ee-131">AsText ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-131">AsText(@geometry)</span></span>
<span data-ttu-id="0e8ee-132">et. Begrenzungs</span><span class="sxs-lookup"><span data-stu-id="0e8ee-132">geometry.Boundary</span></span>                           | <span data-ttu-id="0e8ee-133">Grenze ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-133">Boundary(@geometry)</span></span>
<span data-ttu-id="0e8ee-134">et. Puffer (Entfernung)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-134">geometry.Buffer(distance)</span></span>                   | <span data-ttu-id="0e8ee-135">Puffer ( @geometry , @distance )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-135">Buffer(@geometry, @distance)</span></span>
<span data-ttu-id="0e8ee-136">et. Buffer (Distance, quadrantsegments)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-136">geometry.Buffer(distance, quadrantSegments)</span></span> | <span data-ttu-id="0e8ee-137">Puffer ( @geometry , @distance , @quadrantSegments )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-137">Buffer(@geometry, @distance, @quadrantSegments)</span></span>
<span data-ttu-id="0e8ee-138">et. Schwerpunkt</span><span class="sxs-lookup"><span data-stu-id="0e8ee-138">geometry.Centroid</span></span>                           | <span data-ttu-id="0e8ee-139">Centroid ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-139">Centroid(@geometry)</span></span>
<span data-ttu-id="0e8ee-140">et. Enthält (g)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-140">geometry.Contains(g)</span></span>                        | <span data-ttu-id="0e8ee-141">Enthält ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-141">Contains(@geometry, @g)</span></span>
<span data-ttu-id="0e8ee-142">et. "Anvexhull ()"</span><span class="sxs-lookup"><span data-stu-id="0e8ee-142">geometry.ConvexHull()</span></span>                       | <span data-ttu-id="0e8ee-143">"Anvexhull ( @geometry )"</span><span class="sxs-lookup"><span data-stu-id="0e8ee-143">ConvexHull(@geometry)</span></span>
<span data-ttu-id="0e8ee-144">et. Coveredby (g)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-144">geometry.CoveredBy(g)</span></span>                       | <span data-ttu-id="0e8ee-145">Coveredby ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-145">CoveredBy(@geometry, @g)</span></span>
<span data-ttu-id="0e8ee-146">et. Cover (g)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-146">geometry.Covers(g)</span></span>                          | <span data-ttu-id="0e8ee-147">Deckt ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-147">Covers(@geometry, @g)</span></span>
<span data-ttu-id="0e8ee-148">et. Kreuze (g)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-148">geometry.Crosses(g)</span></span>                         | <span data-ttu-id="0e8ee-149">Kreuze ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-149">Crosses(@geometry, @g)</span></span>
<span data-ttu-id="0e8ee-150">et. Differenz (sonstige)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-150">geometry.Difference(other)</span></span>                  | <span data-ttu-id="0e8ee-151">Differenz ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-151">Difference(@geometry, @other)</span></span>
<span data-ttu-id="0e8ee-152">et. Maß</span><span class="sxs-lookup"><span data-stu-id="0e8ee-152">geometry.Dimension</span></span>                          | <span data-ttu-id="0e8ee-153">Dimension ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-153">Dimension(@geometry)</span></span>
<span data-ttu-id="0e8ee-154">et. Disjoint (g)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-154">geometry.Disjoint(g)</span></span>                        | <span data-ttu-id="0e8ee-155">Disjoint ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-155">Disjoint(@geometry, @g)</span></span>
<span data-ttu-id="0e8ee-156">et. Abstand (g)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-156">geometry.Distance(g)</span></span>                        | <span data-ttu-id="0e8ee-157">Abstand ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-157">Distance(@geometry, @g)</span></span>
<span data-ttu-id="0e8ee-158">et. Versendet</span><span class="sxs-lookup"><span data-stu-id="0e8ee-158">geometry.Envelope</span></span>                           | <span data-ttu-id="0e8ee-159">Umschlag ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-159">Envelope(@geometry)</span></span>
<span data-ttu-id="0e8ee-160">et. Equalstopologisch (g)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-160">geometry.EqualsTopologically(g)</span></span>             | <span data-ttu-id="0e8ee-161">Ist gleich( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-161">Equals(@geometry, @g)</span></span>
<span data-ttu-id="0e8ee-162">et. Geometrytype</span><span class="sxs-lookup"><span data-stu-id="0e8ee-162">geometry.GeometryType</span></span>                       | <span data-ttu-id="0e8ee-163">Geometrytype ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-163">GeometryType(@geometry)</span></span>
<span data-ttu-id="0e8ee-164">et. Getgeometryn (n)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-164">geometry.GetGeometryN(n)</span></span>                    | <span data-ttu-id="0e8ee-165">Geometryn ( @geometry , @n + 1)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-165">GeometryN(@geometry, @n + 1)</span></span>
<span data-ttu-id="0e8ee-166">et. Interiorpoint</span><span class="sxs-lookup"><span data-stu-id="0e8ee-166">geometry.InteriorPoint</span></span>                      | <span data-ttu-id="0e8ee-167">Pointonsurface ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-167">PointOnSurface(@geometry)</span></span>
<span data-ttu-id="0e8ee-168">et. Schnittmenge (sonstige)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-168">geometry.Intersection(other)</span></span>                | <span data-ttu-id="0e8ee-169">Schnittmenge ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-169">Intersection(@geometry, @other)</span></span>
<span data-ttu-id="0e8ee-170">et. Schnittpunkte (g)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-170">geometry.Intersects(g)</span></span>                      | <span data-ttu-id="0e8ee-171">Überschneidet ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-171">Intersects(@geometry, @g)</span></span>
<span data-ttu-id="0e8ee-172">et. IsEmpty</span><span class="sxs-lookup"><span data-stu-id="0e8ee-172">geometry.IsEmpty</span></span>                            | <span data-ttu-id="0e8ee-173">IsEmpty ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-173">IsEmpty(@geometry)</span></span>
<span data-ttu-id="0e8ee-174">et. IsSimple</span><span class="sxs-lookup"><span data-stu-id="0e8ee-174">geometry.IsSimple</span></span>                           | <span data-ttu-id="0e8ee-175">IsSimple ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-175">IsSimple(@geometry)</span></span>
<span data-ttu-id="0e8ee-176">et. IsValid</span><span class="sxs-lookup"><span data-stu-id="0e8ee-176">geometry.IsValid</span></span>                            | <span data-ttu-id="0e8ee-177">IsValid ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-177">IsValid(@geometry)</span></span>
<span data-ttu-id="0e8ee-178">et. Iswithindistance (Geom, Distance)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-178">geometry.IsWithinDistance(geom, distance)</span></span>   | <span data-ttu-id="0e8ee-179">Distance ( @geometry , @geom ) <= @distance</span><span class="sxs-lookup"><span data-stu-id="0e8ee-179">Distance(@geometry, @geom) <= @distance</span></span>
<span data-ttu-id="0e8ee-180">et. Füll</span><span class="sxs-lookup"><span data-stu-id="0e8ee-180">geometry.Length</span></span>                             | <span data-ttu-id="0e8ee-181">GLength ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-181">GLength(@geometry)</span></span>
<span data-ttu-id="0e8ee-182">et. Numgeometries</span><span class="sxs-lookup"><span data-stu-id="0e8ee-182">geometry.NumGeometries</span></span>                      | <span data-ttu-id="0e8ee-183">Numgeometries ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-183">NumGeometries(@geometry)</span></span>
<span data-ttu-id="0e8ee-184">et. NumPoints</span><span class="sxs-lookup"><span data-stu-id="0e8ee-184">geometry.NumPoints</span></span>                          | <span data-ttu-id="0e8ee-185">NumPoints ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-185">NumPoints(@geometry)</span></span>
<span data-ttu-id="0e8ee-186">et. Ogcgeometrytype</span><span class="sxs-lookup"><span data-stu-id="0e8ee-186">geometry.OgcGeometryType</span></span>                    | <span data-ttu-id="0e8ee-187">Fall geometrytype ( @geometry ), wenn ' Point ' dann 1... Schließlich</span><span class="sxs-lookup"><span data-stu-id="0e8ee-187">CASE GeometryType(@geometry) WHEN 'POINT' THEN 1 ... END</span></span>
<span data-ttu-id="0e8ee-188">et. Überlappungen (g)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-188">geometry.Overlaps(g)</span></span>                        | <span data-ttu-id="0e8ee-189">Überlappung ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-189">Overlaps(@geometry, @g)</span></span>
<span data-ttu-id="0e8ee-190">et. Pointonsurface</span><span class="sxs-lookup"><span data-stu-id="0e8ee-190">geometry.PointOnSurface</span></span>                     | <span data-ttu-id="0e8ee-191">Pointonsurface ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-191">PointOnSurface(@geometry)</span></span>
<span data-ttu-id="0e8ee-192">et. Beziehung (g, intersectionpattern)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-192">geometry.Relate(g, intersectionPattern)</span></span>     | <span data-ttu-id="0e8ee-193">Beziehung ( @geometry , @g , @intersectionPattern )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-193">Relate(@geometry, @g, @intersectionPattern)</span></span>
<span data-ttu-id="0e8ee-194">et. Umkehren ()</span><span class="sxs-lookup"><span data-stu-id="0e8ee-194">geometry.Reverse()</span></span>                          | <span data-ttu-id="0e8ee-195">ST_Reverse ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-195">ST_Reverse(@geometry)</span></span>
<span data-ttu-id="0e8ee-196">et. SRID</span><span class="sxs-lookup"><span data-stu-id="0e8ee-196">geometry.SRID</span></span>                               | <span data-ttu-id="0e8ee-197">SRID ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-197">SRID(@geometry)</span></span>
<span data-ttu-id="0e8ee-198">et. Symmetricdifference (sonstige)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-198">geometry.SymmetricDifference(other)</span></span>         | <span data-ttu-id="0e8ee-199">Symdifference ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-199">SymDifference(@geometry, @other)</span></span>
<span data-ttu-id="0e8ee-200">et. "$ Binary ()"</span><span class="sxs-lookup"><span data-stu-id="0e8ee-200">geometry.ToBinary()</span></span>                         | <span data-ttu-id="0e8ee-201">AsBinary ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-201">AsBinary(@geometry)</span></span>
<span data-ttu-id="0e8ee-202">et. "-Text ()"</span><span class="sxs-lookup"><span data-stu-id="0e8ee-202">geometry.ToText()</span></span>                           | <span data-ttu-id="0e8ee-203">AsText ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-203">AsText(@geometry)</span></span>
<span data-ttu-id="0e8ee-204">et. Berührungen (g)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-204">geometry.Touches(g)</span></span>                         | <span data-ttu-id="0e8ee-205">Berührt ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-205">Touches(@geometry, @g)</span></span>
<span data-ttu-id="0e8ee-206">et. Union ()</span><span class="sxs-lookup"><span data-stu-id="0e8ee-206">geometry.Union()</span></span>                            | <span data-ttu-id="0e8ee-207">Unaryunion ( @geometry )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-207">UnaryUnion(@geometry)</span></span>
<span data-ttu-id="0e8ee-208">et. Union (sonstige)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-208">geometry.Union(other)</span></span>                       | <span data-ttu-id="0e8ee-209">Gunion ( @geometry , @other )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-209">GUnion(@geometry, @other)</span></span>
<span data-ttu-id="0e8ee-210">et. In (g)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-210">geometry.Within(g)</span></span>                          | <span data-ttu-id="0e8ee-211">In ( @geometry , @g )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-211">Within(@geometry, @g)</span></span>
<span data-ttu-id="0e8ee-212">GeometryCollection [i]</span><span class="sxs-lookup"><span data-stu-id="0e8ee-212">geometryCollection[i]</span></span>                       | <span data-ttu-id="0e8ee-213">Geometryn ( @geometryCollection , @i + 1)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-213">GeometryN(@geometryCollection, @i + 1)</span></span>
<span data-ttu-id="0e8ee-214">GeometryCollection. Count</span><span class="sxs-lookup"><span data-stu-id="0e8ee-214">geometryCollection.Count</span></span>                    | <span data-ttu-id="0e8ee-215">Numgeometries ( @geometryCollection )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-215">NumGeometries(@geometryCollection)</span></span>
<span data-ttu-id="0e8ee-216">LineString. Count</span><span class="sxs-lookup"><span data-stu-id="0e8ee-216">lineString.Count</span></span>                            | <span data-ttu-id="0e8ee-217">NumPoints ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-217">NumPoints(@lineString)</span></span>
<span data-ttu-id="0e8ee-218">LineString. EndPoint</span><span class="sxs-lookup"><span data-stu-id="0e8ee-218">lineString.EndPoint</span></span>                         | <span data-ttu-id="0e8ee-219">Endpunkt ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-219">EndPoint(@lineString)</span></span>
<span data-ttu-id="0e8ee-220">LineString. getpointn (n)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-220">lineString.GetPointN(n)</span></span>                     | <span data-ttu-id="0e8ee-221">Pointn ( @lineString , @n + 1)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-221">PointN(@lineString, @n + 1)</span></span>
<span data-ttu-id="0e8ee-222">LineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="0e8ee-222">lineString.IsClosed</span></span>                         | <span data-ttu-id="0e8ee-223">IsClosed ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-223">IsClosed(@lineString)</span></span>
<span data-ttu-id="0e8ee-224">LineString. isring</span><span class="sxs-lookup"><span data-stu-id="0e8ee-224">lineString.IsRing</span></span>                           | <span data-ttu-id="0e8ee-225">Isring ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-225">IsRing(@lineString)</span></span>
<span data-ttu-id="0e8ee-226">LineString. StartPoint</span><span class="sxs-lookup"><span data-stu-id="0e8ee-226">lineString.StartPoint</span></span>                       | <span data-ttu-id="0e8ee-227">StartPoint ( @lineString )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-227">StartPoint(@lineString)</span></span>
<span data-ttu-id="0e8ee-228">MultiLineString. IsClosed</span><span class="sxs-lookup"><span data-stu-id="0e8ee-228">multiLineString.IsClosed</span></span>                    | <span data-ttu-id="0e8ee-229">IsClosed ( @multiLineString )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-229">IsClosed(@multiLineString)</span></span>
<span data-ttu-id="0e8ee-230">Punkt. 800</span><span class="sxs-lookup"><span data-stu-id="0e8ee-230">point.M</span></span>                                     | <span data-ttu-id="0e8ee-231">M ( @point )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-231">M(@point)</span></span>
<span data-ttu-id="0e8ee-232">Punkt. Stuben</span><span class="sxs-lookup"><span data-stu-id="0e8ee-232">point.X</span></span>                                     | <span data-ttu-id="0e8ee-233">X ( @point )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-233">X(@point)</span></span>
<span data-ttu-id="0e8ee-234">Punkt. Teenie</span><span class="sxs-lookup"><span data-stu-id="0e8ee-234">point.Y</span></span>                                     | <span data-ttu-id="0e8ee-235">J ( @point )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-235">Y(@point)</span></span>
<span data-ttu-id="0e8ee-236">Punkt. Z</span><span class="sxs-lookup"><span data-stu-id="0e8ee-236">point.Z</span></span>                                     | <span data-ttu-id="0e8ee-237">Z ( @point )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-237">Z(@point)</span></span>
<span data-ttu-id="0e8ee-238">Polygon. ExteriorRing</span><span class="sxs-lookup"><span data-stu-id="0e8ee-238">polygon.ExteriorRing</span></span>                        | <span data-ttu-id="0e8ee-239">ExteriorRing ( @polygon )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-239">ExteriorRing(@polygon)</span></span>
<span data-ttu-id="0e8ee-240">Polygon. Getinteriorringn (n)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-240">polygon.GetInteriorRingN(n)</span></span>                 | <span data-ttu-id="0e8ee-241">Interiorringn ( @polygon , @n + 1)</span><span class="sxs-lookup"><span data-stu-id="0e8ee-241">InteriorRingN(@polygon, @n + 1)</span></span>
<span data-ttu-id="0e8ee-242">Polygon. Numinteriorrings</span><span class="sxs-lookup"><span data-stu-id="0e8ee-242">polygon.NumInteriorRings</span></span>                    | <span data-ttu-id="0e8ee-243">Numinteriorring ( @polygon )</span><span class="sxs-lookup"><span data-stu-id="0e8ee-243">NumInteriorRing(@polygon)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e8ee-244">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="0e8ee-244">Additional resources</span></span>

* [<span data-ttu-id="0e8ee-245">Spatialite-Homepage</span><span class="sxs-lookup"><span data-stu-id="0e8ee-245">SpatiaLite Homepage</span></span>](https://www.gaia-gis.it/fossil/libspatialite)
* [<span data-ttu-id="0e8ee-246">Nettopologysuite-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="0e8ee-246">NetTopologySuite Docs</span></span>](https://nettopologysuite.github.io/NetTopologySuite/)
