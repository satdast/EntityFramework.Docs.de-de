---
title: Räumliche Daten-EF Core
description: Verwenden räumlicher Daten in einem Entity Framework Core Modell
author: bricelam
ms.date: 10/02/2020
uid: core/modeling/spatial
ms.openlocfilehash: fa9cf30ddb4291d96486934544b568b67b126846
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430520"
---
# <a name="spatial-data"></a><span data-ttu-id="193e7-103">Räumliche Daten</span><span class="sxs-lookup"><span data-stu-id="193e7-103">Spatial Data</span></span>

> [!NOTE]
> <span data-ttu-id="193e7-104">Diese Funktion wurde in EF Core 2,2 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="193e7-104">This feature was added in EF Core 2.2.</span></span>

<span data-ttu-id="193e7-105">Räumliche Daten repräsentieren den physischen Speicherort und die Form von Objekten.</span><span class="sxs-lookup"><span data-stu-id="193e7-105">Spatial data represents the physical location and the shape of objects.</span></span> <span data-ttu-id="193e7-106">Viele Datenbanken unterstützen diese Art von Daten, damit Sie zusammen mit anderen Daten indiziert und abgefragt werden können.</span><span class="sxs-lookup"><span data-stu-id="193e7-106">Many databases provide support for this type of data so it can be indexed and queried alongside other data.</span></span> <span data-ttu-id="193e7-107">Häufige Szenarien umfassen das Abfragen von Objekten in einer bestimmten Entfernung von einem Speicherort oder das Auswählen des Objekts, dessen Rahmen eine bestimmte Position enthält.</span><span class="sxs-lookup"><span data-stu-id="193e7-107">Common scenarios include querying for objects within a given distance from a location, or selecting the object whose border contains a given location.</span></span> <span data-ttu-id="193e7-108">EF Core unterstützt die Zuordnung räumlicher Datentypen mithilfe der räumlichen Bibliothek nettopologysuite.</span><span class="sxs-lookup"><span data-stu-id="193e7-108">EF Core supports mapping to spatial data types using the NetTopologySuite spatial library.</span></span>

## <a name="installing"></a><span data-ttu-id="193e7-109">Installation</span><span class="sxs-lookup"><span data-stu-id="193e7-109">Installing</span></span>

<span data-ttu-id="193e7-110">Um räumliche Daten mit EF Core verwenden zu können, müssen Sie das entsprechende unterstützende nuget-Paket installieren.</span><span class="sxs-lookup"><span data-stu-id="193e7-110">In order to use spatial data with EF Core, you need to install the appropriate supporting NuGet package.</span></span> <span data-ttu-id="193e7-111">Welches Paket installiert werden muss, hängt vom verwendeten Anbieter ab.</span><span class="sxs-lookup"><span data-stu-id="193e7-111">Which package you need to install depends on the provider you're using.</span></span>

<span data-ttu-id="193e7-112">EF Core Anbieter</span><span class="sxs-lookup"><span data-stu-id="193e7-112">EF Core Provider</span></span>                        | <span data-ttu-id="193e7-113">Räumliches nuget-Paket</span><span class="sxs-lookup"><span data-stu-id="193e7-113">Spatial NuGet Package</span></span>
--------------------------------------- | ---------------------
<span data-ttu-id="193e7-114">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="193e7-114">Microsoft.EntityFrameworkCore.SqlServer</span></span> | [<span data-ttu-id="193e7-115">Microsoft. entityframeworkcore. SqlServer. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="193e7-115">Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite)
<span data-ttu-id="193e7-116">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="193e7-116">Microsoft.EntityFrameworkCore.Sqlite</span></span>    | [<span data-ttu-id="193e7-117">Microsoft. entityframeworkcore. sqlite. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="193e7-117">Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite)
<span data-ttu-id="193e7-118">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="193e7-118">Microsoft.EntityFrameworkCore.InMemory</span></span>  | [<span data-ttu-id="193e7-119">Nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="193e7-119">NetTopologySuite</span></span>](https://www.nuget.org/packages/NetTopologySuite)
<span data-ttu-id="193e7-120">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="193e7-120">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>   | [<span data-ttu-id="193e7-121">Npgsql. entityframeworkcore. PostgreSQL. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="193e7-121">Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite)
<span data-ttu-id="193e7-122">Pomelo.EntityFrameworkCore.MySql</span><span class="sxs-lookup"><span data-stu-id="193e7-122">Pomelo.EntityFrameworkCore.MySql</span></span>        | [<span data-ttu-id="193e7-123">Pomelo. entityframeworkcore. MySQL. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="193e7-123">Pomelo.EntityFrameworkCore.MySql.NetTopologySuite</span></span>](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite)
<span data-ttu-id="193e7-124">Devart.Data.MySql.EFCore</span><span class="sxs-lookup"><span data-stu-id="193e7-124">Devart.Data.MySql.EFCore</span></span>                | [<span data-ttu-id="193e7-125">Devart. Data. MySQL. efcore. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="193e7-125">Devart.Data.MySql.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.MySql.EFCore.NetTopologySuite)
<span data-ttu-id="193e7-126">Devart.Data.PostgreSql.EFCore</span><span class="sxs-lookup"><span data-stu-id="193e7-126">Devart.Data.PostgreSql.EFCore</span></span>           | [<span data-ttu-id="193e7-127">Devart. Data. PostgreSQL. efcore. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="193e7-127">Devart.Data.PostgreSql.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore.NetTopologySuite)
<span data-ttu-id="193e7-128">Devart.Data.SQLite.EFCore</span><span class="sxs-lookup"><span data-stu-id="193e7-128">Devart.Data.SQLite.EFCore</span></span>               | [<span data-ttu-id="193e7-129">Devart. Data. sqlite. efcore. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="193e7-129">Devart.Data.SQLite.EFCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore.NetTopologySuite)
<span data-ttu-id="193e7-130">Teradata.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="193e7-130">Teradata.EntityFrameworkCore</span></span>            | [<span data-ttu-id="193e7-131">Teradata. entityframeworkcore. nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="193e7-131">Teradata.EntityFrameworkCore.NetTopologySuite</span></span>](https://www.nuget.org/packages/Teradata.EntityFrameworkCore.NetTopologySuite)

## <a name="nettopologysuite"></a><span data-ttu-id="193e7-132">Nettopologysuite</span><span class="sxs-lookup"><span data-stu-id="193e7-132">NetTopologySuite</span></span>

<span data-ttu-id="193e7-133">[Nettopologysuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) ist eine räumliche Bibliothek für .net.</span><span class="sxs-lookup"><span data-stu-id="193e7-133">[NetTopologySuite](https://nettopologysuite.github.io/NetTopologySuite/) (NTS) is a spatial library for .NET.</span></span> <span data-ttu-id="193e7-134">EF Core ermöglicht die Zuordnung von räumlichen Datentypen in der Datenbank mithilfe von NTS-Typen in Ihrem Modell.</span><span class="sxs-lookup"><span data-stu-id="193e7-134">EF Core enables mapping to spatial data types in the database by using NTS types in your model.</span></span>

<span data-ttu-id="193e7-135">Um die Zuordnung räumlicher Typen über NTS zu ermöglichen, müssen Sie die usenettopologysuite-Methode für den dbcontext Options-Generator des Anbieters abrufen.</span><span class="sxs-lookup"><span data-stu-id="193e7-135">To enable mapping to spatial types via NTS, call the UseNetTopologySuite method on the provider's DbContext options builder.</span></span> <span data-ttu-id="193e7-136">Beispielsweise können Sie mit SQL Server wie folgt bezeichnen.</span><span class="sxs-lookup"><span data-stu-id="193e7-136">For example, with SQL Server you'd call it like this.</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/WideWorldImportersContext.cs?name=snippet_UseNetTopologySuite)]

<span data-ttu-id="193e7-137">Es gibt mehrere räumliche Datentypen.</span><span class="sxs-lookup"><span data-stu-id="193e7-137">There are several spatial data types.</span></span> <span data-ttu-id="193e7-138">Welcher Typ Sie verwenden, hängt von den Formen der Formen ab, die Sie zulassen möchten.</span><span class="sxs-lookup"><span data-stu-id="193e7-138">Which type you use depends on the types of shapes you want to allow.</span></span> <span data-ttu-id="193e7-139">Hier ist die Hierarchie der NTS-Typen, die Sie für Eigenschaften im Modell verwenden können.</span><span class="sxs-lookup"><span data-stu-id="193e7-139">Here is the hierarchy of NTS types that you can use for properties in your model.</span></span> <span data-ttu-id="193e7-140">Sie befinden sich im- `NetTopologySuite.Geometries` Namespace.</span><span class="sxs-lookup"><span data-stu-id="193e7-140">They're located within the `NetTopologySuite.Geometries` namespace.</span></span>

* <span data-ttu-id="193e7-141">Geometrie</span><span class="sxs-lookup"><span data-stu-id="193e7-141">Geometry</span></span>
  * <span data-ttu-id="193e7-142">Point</span><span class="sxs-lookup"><span data-stu-id="193e7-142">Point</span></span>
  * <span data-ttu-id="193e7-143">LineString</span><span class="sxs-lookup"><span data-stu-id="193e7-143">LineString</span></span>
  * <span data-ttu-id="193e7-144">Polygon</span><span class="sxs-lookup"><span data-stu-id="193e7-144">Polygon</span></span>
  * <span data-ttu-id="193e7-145">GeometryCollection</span><span class="sxs-lookup"><span data-stu-id="193e7-145">GeometryCollection</span></span>
    * <span data-ttu-id="193e7-146">MultiPoint</span><span class="sxs-lookup"><span data-stu-id="193e7-146">MultiPoint</span></span>
    * <span data-ttu-id="193e7-147">MultiLineString</span><span class="sxs-lookup"><span data-stu-id="193e7-147">MultiLineString</span></span>
    * <span data-ttu-id="193e7-148">MultiPolygon</span><span class="sxs-lookup"><span data-stu-id="193e7-148">MultiPolygon</span></span>

> [!WARNING]
> <span data-ttu-id="193e7-149">Circularstring, compoundcurve und curepolygon werden von NTS nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="193e7-149">CircularString, CompoundCurve, and CurePolygon aren't supported by NTS.</span></span>

<span data-ttu-id="193e7-150">Mit dem basisgeometry-Typ kann jeder Typ von Form von der-Eigenschaft angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="193e7-150">Using the base Geometry type allows any type of shape to be specified by the property.</span></span>

## <a name="longitude-and-latitude"></a><span data-ttu-id="193e7-151">Längen-und Breitengrad</span><span class="sxs-lookup"><span data-stu-id="193e7-151">Longitude and Latitude</span></span>

<span data-ttu-id="193e7-152">Die Koordinaten in den NTS sind X-und Y-Werte.</span><span class="sxs-lookup"><span data-stu-id="193e7-152">Coordinates in NTS are in terms of X and Y values.</span></span> <span data-ttu-id="193e7-153">Um Längen-und Breitengrad darzustellen, verwenden Sie X für Längengrad und Y für Breitengrad.</span><span class="sxs-lookup"><span data-stu-id="193e7-153">To represent longitude and latitude, use X for longitude and Y for latitude.</span></span> <span data-ttu-id="193e7-154">Beachten Sie, dass **backwards** dies nicht das `latitude, longitude` Format hat, in dem normalerweise diese Werte angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="193e7-154">Note that this is **backwards** from the `latitude, longitude` format in which you typically see these values.</span></span>

## <a name="querying-data"></a><span data-ttu-id="193e7-155">Abfrage von Daten</span><span class="sxs-lookup"><span data-stu-id="193e7-155">Querying Data</span></span>

<span data-ttu-id="193e7-156">Die folgenden Entitäts Klassen können verwendet werden, um Tabellen in der [Beispieldatenbank Wide World Importern](https://go.microsoft.com/fwlink/?LinkID=800630)zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="193e7-156">The following entity classes could be used to map to tables in the [Wide World Importers sample database](https://go.microsoft.com/fwlink/?LinkID=800630).</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/City.cs?name=snippet_City)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Models/Country.cs?name=snippet_Country)]

<span data-ttu-id="193e7-157">In LINQ werden die NTS-Methoden und-Eigenschaften, die als Datenbankfunktionen verfügbar sind, in SQL übersetzt.</span><span class="sxs-lookup"><span data-stu-id="193e7-157">In LINQ, the NTS methods and properties available as database functions will be translated to SQL.</span></span> <span data-ttu-id="193e7-158">Beispielsweise werden die Distance-Methode und die-Methode in den folgenden Abfragen übersetzt.</span><span class="sxs-lookup"><span data-stu-id="193e7-158">For example, the Distance and Contains methods are translated in the following queries.</span></span> <span data-ttu-id="193e7-159">Informationen zu den unterstützten Methoden finden Sie in der Dokumentation des Anbieters.</span><span class="sxs-lookup"><span data-stu-id="193e7-159">See your provider's documentation for which methods are supported.</span></span>

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Distance)]

[!code-csharp[](../../../samples/core/Spatial/SqlServer/Program.cs?name=snippet_Contains)]

## <a name="reverse-engineering"></a><span data-ttu-id="193e7-160">Reverse Engineering</span><span class="sxs-lookup"><span data-stu-id="193e7-160">Reverse engineering</span></span>

<span data-ttu-id="193e7-161">Die räumlichen nuget-Pakete ermöglichen auch das [Reverse Engineering](xref:core/managing-schemas/scaffolding) von Modellen mit räumlichen Eigenschaften. Sie müssen jedoch das Paket installieren \* *_vor_* _ Ausführen von `Scaffold-DbContext` oder `dotnet ef dbcontext scaffold` .</span><span class="sxs-lookup"><span data-stu-id="193e7-161">The spatial NuGet packages also enable [reverse engineering](xref:core/managing-schemas/scaffolding) models with spatial properties, but you need to install the package \* *_before_* _ running `Scaffold-DbContext` or `dotnet ef dbcontext scaffold`.</span></span> <span data-ttu-id="193e7-162">Wenn Sie dies nicht tun, erhalten Sie Warnungen, wenn Sie keine Typzuordnungen für die Spalten finden und die Spalten übersprungen werden.</span><span class="sxs-lookup"><span data-stu-id="193e7-162">If you don't, you'll receive warnings about not finding type mappings for the columns and the columns will be skipped.</span></span>

## <a name="srid-ignored-during-client-operations"></a><span data-ttu-id="193e7-163">SRID wird bei Client Vorgängen ignoriert.</span><span class="sxs-lookup"><span data-stu-id="193e7-163">SRID Ignored during client operations</span></span>

<span data-ttu-id="193e7-164">NTS ignoriert SRID-Werte während des Vorgangs.</span><span class="sxs-lookup"><span data-stu-id="193e7-164">NTS ignores SRID values during operations.</span></span> <span data-ttu-id="193e7-165">Es wird von einem planaren Koordinatensystem ausgegangen.</span><span class="sxs-lookup"><span data-stu-id="193e7-165">It assumes a planar coordinate system.</span></span> <span data-ttu-id="193e7-166">Dies bedeutet Folgendes: Wenn Sie Koordinaten in Bezug auf Längengrad und Breitengrad angeben, werden einige vom Client ausgewertete Werte wie "Distance", "length" und "Area" in Grad und nicht in "Meter" angegeben.</span><span class="sxs-lookup"><span data-stu-id="193e7-166">This means that if you specify coordinates in terms of longitude and latitude, some client-evaluated values like distance, length, and area will be in degrees, not meters.</span></span> <span data-ttu-id="193e7-167">Wenn Sie aussagekräftigere Werte benötigen, müssen Sie die Koordinaten zunächst mithilfe einer Bibliothek wie [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) auf ein anderes Koordinatensystem projizieren, bevor Sie diese Werte berechnen.</span><span class="sxs-lookup"><span data-stu-id="193e7-167">For more meaningful values, you first need to project the coordinates to another coordinate system using a library like [ProjNet4GeoAPI](https://github.com/NetTopologySuite/ProjNet4GeoAPI) before calculating these values.</span></span>

<span data-ttu-id="193e7-168">Wenn ein Vorgang vom Server durch EF Core über SQL ausgewertet wird, wird die Einheit des Ergebnisses von der Datenbank bestimmt.</span><span class="sxs-lookup"><span data-stu-id="193e7-168">If an operation is server-evaluated by EF Core via SQL, the result's unit will be determined by the database.</span></span>

<span data-ttu-id="193e7-169">Im folgenden finden Sie ein Beispiel für die Verwendung von ProjNet4GeoAPI, um den Abstand zwischen zwei Städten zu berechnen.</span><span class="sxs-lookup"><span data-stu-id="193e7-169">Here is an example of using ProjNet4GeoAPI to calculate the distance between two cities.</span></span>

[!code-csharp[](../../../samples/core/Spatial/Projections/GeometryExtensions.cs?name=snippet_GeometryExtensions)]

[!code-csharp[](../../../samples/core/Spatial/Projections/Program.cs?name=snippet_ProjectTo)]

> [!NOTE]
> <span data-ttu-id="193e7-170">4326 bezieht sich auf WGS 84, einen in GPS und anderen geografischen Systemen verwendeten Standard.</span><span class="sxs-lookup"><span data-stu-id="193e7-170">4326 refers to WGS 84, a standard used in GPS and other geographic systems.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="193e7-171">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="193e7-171">Additional resources</span></span>

### <a name="database-specific-information"></a><span data-ttu-id="193e7-172">Datenbankspezifische Informationen</span><span class="sxs-lookup"><span data-stu-id="193e7-172">Database-specific information</span></span>

<span data-ttu-id="193e7-173">Lesen Sie unbedingt die Dokumentation Ihres Anbieters, um weitere Informationen zum Arbeiten mit räumlichen Daten zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="193e7-173">Be sure to read your provider's documentation for additional information on working with spatial data.</span></span>

<span data-ttu-id="193e7-174">_ [Räumliche Daten im SQL Server-Anbieter](xref:core/providers/sql-server/spatial)</span><span class="sxs-lookup"><span data-stu-id="193e7-174">_ [Spatial Data in the SQL Server Provider](xref:core/providers/sql-server/spatial)</span></span>
* [<span data-ttu-id="193e7-175">Räumliche Daten im SQLite-Anbieter</span><span class="sxs-lookup"><span data-stu-id="193e7-175">Spatial Data in the SQLite Provider</span></span>](xref:core/providers/sqlite/spatial)
* [<span data-ttu-id="193e7-176">Räumliche Daten im npgsql-Anbieter</span><span class="sxs-lookup"><span data-stu-id="193e7-176">Spatial Data in the Npgsql Provider</span></span>](https://www.npgsql.org/efcore/mapping/nts.html)

### <a name="other-resources"></a><span data-ttu-id="193e7-177">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="193e7-177">Other resources</span></span>

* [<span data-ttu-id="193e7-178">Nettopologysuite-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="193e7-178">NetTopologySuite Docs</span></span>](https://nettopologysuite.github.io/NetTopologySuite/)
* <span data-ttu-id="193e7-179">[EF Core Community Standup-Sitzung](https://www.youtube.com/watch?v=IHslY5rrxD0&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=15)mit Schwerpunkt auf räumliche Daten und nettopologysuite.</span><span class="sxs-lookup"><span data-stu-id="193e7-179">[EF Core Community Standup session](https://www.youtube.com/watch?v=IHslY5rrxD0&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=15), focusing on spatial data and NetTopologySuite.</span></span>
