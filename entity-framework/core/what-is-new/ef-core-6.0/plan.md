---
title: Plan für Entity Framework Core 6.0
description: Designs und Features, die für die EF Core 6.0 geplant sind
author: ajcvickers
ms.date: 01/12/2021
uid: core/what-is-new/ef-core-6.0/plan
ms.openlocfilehash: 612461bc6ad30778baa5c6d10dda5cabac91dcb2
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129539"
---
# <a name="plan-for-entity-framework-core-60"></a><span data-ttu-id="c9b11-103">Plan für Entity Framework Core 6.0</span><span class="sxs-lookup"><span data-stu-id="c9b11-103">Plan for Entity Framework Core 6.0</span></span>

<span data-ttu-id="c9b11-104">Wie im [Artikel zum Planungsprozess](xref:core/what-is-new/release-planning) beschrieben, wurde aus dem Feedback der Beteiligten dieser Plan für das Release von Entity Framework Core 6.0 (EF Core) erstellt.</span><span class="sxs-lookup"><span data-stu-id="c9b11-104">As described in the [planning process](xref:core/what-is-new/release-planning), we have gathered input from stakeholders into a plan for the Entity Framework Core (EF Core) 6.0 release.</span></span>

<span data-ttu-id="c9b11-105">Im Gegensatz zu früheren Releases versucht dieser Plan nicht, die gesamte Arbeit für das 6.0-Release abzudecken.</span><span class="sxs-lookup"><span data-stu-id="c9b11-105">Unlike previous releases, this plan does not attempt to cover all work for the 6.0 release.</span></span> <span data-ttu-id="c9b11-106">Stattdessen gibt er an, an welchen Stellen und auf welche Weise in dieses Release investiert werden soll. Dabei wird auf ausreichend Flexibilität geachtet, um den Anwendungsbereich anzupassen oder neue Arbeit hinzuzufügen, während Feedback und neue Erkenntnisse bei der Arbeit am Release gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="c9b11-106">Instead, it indicates where and how we intend to invest in this release, but with flexibility to adjust scope or pull in new work as we gather feedback and learn while working on the release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c9b11-107">Dieser Plan ist nicht bindend.</span><span class="sxs-lookup"><span data-stu-id="c9b11-107">This plan is not a commitment.</span></span> <span data-ttu-id="c9b11-108">Er dient als Ausgangspunkt und wird auf Grundlage von neuen Erkenntnissen weiterentwickelt.</span><span class="sxs-lookup"><span data-stu-id="c9b11-108">It is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="c9b11-109">Es kann also sein, dass derzeit nicht geplante Funktionen in Release 6.0 aufgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="c9b11-109">Some things not currently planned for 6.0 may get pulled in.</span></span> <span data-ttu-id="c9b11-110">Im Gegenzug können aktuell geplante Funktionen auch aus Release 6.0 gestrichen werden.</span><span class="sxs-lookup"><span data-stu-id="c9b11-110">Some things currently planned for 6.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="c9b11-111">Allgemeine Informationen</span><span class="sxs-lookup"><span data-stu-id="c9b11-111">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="c9b11-112">Versionsnummer und Veröffentlichungsdatum</span><span class="sxs-lookup"><span data-stu-id="c9b11-112">Version number and release date</span></span>

<span data-ttu-id="c9b11-113">EF Core 6.0 ist das nächste Release nach EF Core 5.0. Die Veröffentlichung ist derzeit für den November 2021, also für den gleichen Termin wie die von .NET 6 geplant.</span><span class="sxs-lookup"><span data-stu-id="c9b11-113">EF Core 6.0 is the next release after EF Core 5.0 and is currently scheduled for release in November 2021 at the same time as .NET 6.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="c9b11-114">Unterstützte Plattformen</span><span class="sxs-lookup"><span data-stu-id="c9b11-114">Supported platforms</span></span>

<span data-ttu-id="c9b11-115">EF Core 6.0 ist derzeit auf .NET 5 ausgerichtet.</span><span class="sxs-lookup"><span data-stu-id="c9b11-115">EF Core 6.0 currently targets .NET 5.</span></span> <span data-ttu-id="c9b11-116">Wahrscheinlich erfolgt eine Neuausrichtung auf .NET 6, je näher das Release rückt.</span><span class="sxs-lookup"><span data-stu-id="c9b11-116">This will likely be updated to .NET 6 as we near the release.</span></span> <span data-ttu-id="c9b11-117">EF Core 6.0 ist auf keine Version von .NET Standard ausgerichtet. Weitere Informationen finden Sie unter [Die Zukunft von .NET Standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span><span class="sxs-lookup"><span data-stu-id="c9b11-117">EF Core 6.0 does not target any .NET Standard version; for more information see [the future of .NET Standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span></span>

<span data-ttu-id="c9b11-118">EF Core 6.0 kann nicht im .NET Framework ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="c9b11-118">EF Core 6.0 will not run on .NET Framework.</span></span>

<span data-ttu-id="c9b11-119">EF Core 6.0 ist als [Long-Term-Support-Release (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) an .NET 6 ausgerichtet.</span><span class="sxs-lookup"><span data-stu-id="c9b11-119">EF Core 6.0 will align with .NET 6 as a [long-term support (LTS) release](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="c9b11-120">Aktuelle Änderungen</span><span class="sxs-lookup"><span data-stu-id="c9b11-120">Breaking changes</span></span>

<span data-ttu-id="c9b11-121">EF Core 6.0 enthält einige [Breaking Changes](xref:core/what-is-new/ef-core-6.0/breaking-changes), da sowohl EF Core als auch die .NET-Plattform weiterentwickelt werden.</span><span class="sxs-lookup"><span data-stu-id="c9b11-121">EF Core 6.0 will contain a small number of [breaking changes](xref:core/what-is-new/ef-core-6.0/breaking-changes) as we continue to evolve both EF Core and the .NET platform.</span></span> <span data-ttu-id="c9b11-122">Unser Ziel ist es, die Mehrzahl der Anwendungen zu aktualisieren, ohne dass Breaking Changes auftreten.</span><span class="sxs-lookup"><span data-stu-id="c9b11-122">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

## <a name="themes"></a><span data-ttu-id="c9b11-123">Designs</span><span class="sxs-lookup"><span data-stu-id="c9b11-123">Themes</span></span>

<span data-ttu-id="c9b11-124">Die folgenden Bereiche bilden die Grundlage für die umfangreichen Investitionen in EF Core 6.0.</span><span class="sxs-lookup"><span data-stu-id="c9b11-124">The following areas will form the basis for the large investments in EF Core 6.0.</span></span>

## <a name="highly-requested-features"></a><span data-ttu-id="c9b11-125">Features mit starker Nachfrage</span><span class="sxs-lookup"><span data-stu-id="c9b11-125">Highly requested features</span></span>

<span data-ttu-id="c9b11-126">Eine der wichtigsten Feedbackquellen für den [Planungsprozess](xref:core/what-is-new/release-planning) sind wie immer die [Abstimmungen (👍) über Features auf GitHub](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="c9b11-126">As always, a major input into the [planning process](xref:core/what-is-new/release-planning) comes from the [voting (👍) for features on GitHub](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc).</span></span> <span data-ttu-id="c9b11-127">Für EF Core 6.0 sind die folgenden stark nachgefragten Features geplant:</span><span class="sxs-lookup"><span data-stu-id="c9b11-127">For EF Core 6.0 we plan to work on the following highly requested features:</span></span>

### <a name="sql-server-temporal-tables"></a><span data-ttu-id="c9b11-128">Temporäre SQL Server-Tabellen</span><span class="sxs-lookup"><span data-stu-id="c9b11-128">SQL Server temporal tables</span></span>

<span data-ttu-id="c9b11-129">Nachverfolgbar über [Issue 4693](https://github.com/dotnet/efcore/issues/4693)</span><span class="sxs-lookup"><span data-stu-id="c9b11-129">Tracked by [#4693](https://github.com/dotnet/efcore/issues/4693)</span></span>

<span data-ttu-id="c9b11-130">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-130">Status: Not started</span></span>

<span data-ttu-id="c9b11-131">T-Shirt-Größe: Large</span><span class="sxs-lookup"><span data-stu-id="c9b11-131">T-shirt size: Large</span></span>

<span data-ttu-id="c9b11-132">Temporäre Tabellen unterstützen Abfragen für Daten, die zu einem _beliebigen Zeitpunkt_ in der Tabelle gespeichert wurden, anstelle der zuletzt gespeicherten Daten wie bei normalen Tabellen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-132">Temporal tables support queries for data stored in the table at _any point in time_, as opposed to only the most recent data stored as is the case for normal tables.</span></span> <span data-ttu-id="c9b11-133">EF Core 6.0 ermöglicht das Erstellen temporärer Tabellen über Migrationen sowie das Zugreifen auf die Daten über LINQ-Abfragen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-133">EF Core 6.0 will allow temporal tables to be created via Migrations, as well as allowing access to the data through LINQ queries.</span></span>

<span data-ttu-id="c9b11-134">Der Arbeitsumfang wurde zu Beginn [in diesem Issue](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="c9b11-134">This work is initially scoped as [described on the issue](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974).</span></span> <span data-ttu-id="c9b11-135">Unter Umständen wird während der Veröffentlichung auf Grundlage von Feedback zusätzliche Unterstützung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c9b11-135">We may pull in additional support based on feedback during the release.</span></span>

### <a name="json-columns"></a><span data-ttu-id="c9b11-136">JSON-Spalten</span><span class="sxs-lookup"><span data-stu-id="c9b11-136">JSON columns</span></span>

<span data-ttu-id="c9b11-137">Nachverfolgbar über [Issue 4021](https://github.com/dotnet/efcore/issues/4021)</span><span class="sxs-lookup"><span data-stu-id="c9b11-137">Tracked by [#4021](https://github.com/dotnet/efcore/issues/4021)</span></span>

<span data-ttu-id="c9b11-138">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-138">Status: Not started</span></span>

<span data-ttu-id="c9b11-139">T-Shirt-Größe: Mittel</span><span class="sxs-lookup"><span data-stu-id="c9b11-139">T-shirt size: Medium</span></span>

<span data-ttu-id="c9b11-140">Dieses Feature führt einen gängigen Mechanismus sowie Muster für die JSON-Unterstützung ein, die von jedem Datenbankanbieter implementiert werden können.</span><span class="sxs-lookup"><span data-stu-id="c9b11-140">This feature will introduce a common mechanism and patterns for JSON support that can be implemented by any database provider.</span></span> <span data-ttu-id="c9b11-141">Wir arbeiten mit der Community zusammen, um vorhandene Implementierungen von [Npgsql](https://github.com/npgsql/efcore.pg) und [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) abzugleichen und zudem Unterstützung für SQL Server und SQLite hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-141">We will work with the community to align existing implementations for [Npgsql](https://github.com/npgsql/efcore.pg) and [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and also add support for SQL Server and SQLite.</span></span>

### <a name="columnattributeorder"></a><span data-ttu-id="c9b11-142">ColumnAttribute.Order</span><span class="sxs-lookup"><span data-stu-id="c9b11-142">ColumnAttribute.Order</span></span>

<span data-ttu-id="c9b11-143">Nachverfolgbar über [Issue 10059](https://github.com/dotnet/efcore/issues/10059)</span><span class="sxs-lookup"><span data-stu-id="c9b11-143">Tracked by [#10059](https://github.com/dotnet/efcore/issues/10059)</span></span>

<span data-ttu-id="c9b11-144">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-144">Status: Not started</span></span>

<span data-ttu-id="c9b11-145">T-Shirt-Größe: Small</span><span class="sxs-lookup"><span data-stu-id="c9b11-145">T-shirt size: Small</span></span>

<span data-ttu-id="c9b11-146">Dieses Feature ermöglicht eine beliebige Reihenfolge von Spalten beim **Erstellen einer Tabelle** mit Migrationen oder `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="c9b11-146">This feature will allow arbitrary ordering of columns when **creating a table** with Migrations or `EnsureCreated`.</span></span> <span data-ttu-id="c9b11-147">Beachten Sie, dass eine vorhandenen Tabelle neu erstellt werden muss, wenn Sie die Spaltenreihenfolge darin ändern möchten, und dass Unterstützung für dieses Vorgehen bisher in keinem EF Core-Release vorgesehen ist.</span><span class="sxs-lookup"><span data-stu-id="c9b11-147">Note that changing the order of columns in an existing tables requires that the table be rebuilt, and this is not something that we plan to support in any EF Core release.</span></span>

## <a name="performance"></a><span data-ttu-id="c9b11-148">Leistung</span><span class="sxs-lookup"><span data-stu-id="c9b11-148">Performance</span></span>

<span data-ttu-id="c9b11-149">Obwohl EF Core in der Regel schneller als EF6 ist, gibt es immer noch Bereiche, in denen bedeutende Leistungsverbesserungen möglich sind.</span><span class="sxs-lookup"><span data-stu-id="c9b11-149">While EF Core is generally faster than EF6, there are still areas where significant improvements in performance are possible.</span></span> <span data-ttu-id="c9b11-150">Es ist geplant, mehrere dieser Bereiche in EF Core 6.0 anzugehen und gleichzeitig die Leistungsinfrastruktur und -tests zu verbessern.</span><span class="sxs-lookup"><span data-stu-id="c9b11-150">We plan to tackle several of these areas in EF Core 6.0, while also improving our perf infrastructure and testing.</span></span>

<span data-ttu-id="c9b11-151">Dieses Design wird viele iterative Untersuchungen umfassen, die beeinflussen, worauf Ressourcen konzentriert werden.</span><span class="sxs-lookup"><span data-stu-id="c9b11-151">This theme will involve a lot of iterative investigation, which will inform where we focus resources.</span></span> <span data-ttu-id="c9b11-152">Geplant ist, mit folgenden Features zu beginnen:</span><span class="sxs-lookup"><span data-stu-id="c9b11-152">We plan to begin with:</span></span>

### <a name="performance-infrastructure-and-new-tests"></a><span data-ttu-id="c9b11-153">Leistungsinfrastruktur und neue Tests</span><span class="sxs-lookup"><span data-stu-id="c9b11-153">Performance infrastructure and new tests</span></span>

<span data-ttu-id="c9b11-154">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-154">Status: Not started</span></span>

<span data-ttu-id="c9b11-155">T-Shirt-Größe: Mittel</span><span class="sxs-lookup"><span data-stu-id="c9b11-155">T-shirt size: Medium</span></span>

<span data-ttu-id="c9b11-156">Die Codebasis von EF Core enthält bereits einige Leistungsbenchmarks, die täglich ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="c9b11-156">The EF Core codebase already contains a set of performance benchmarks that are executed every day.</span></span> <span data-ttu-id="c9b11-157">Für EF Core 6.0 ist geplant, sowohl die Infrastruktur für diese Tests zu verbessern als auch neue Tests hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-157">For 6.0, we plan to improve the infrastructure for these tests as well as adding new tests.</span></span> <span data-ttu-id="c9b11-158">Außerdem wird ein Profil für die grundlegenden Leistungsszenarios erstellt, und alle mit geringem Aufwand zu korrigierenden Probleme werden behoben.</span><span class="sxs-lookup"><span data-stu-id="c9b11-158">We will also profile mainline perf scenarios and fix any low-hanging fruit found.</span></span>

### <a name="compiled-models"></a><span data-ttu-id="c9b11-159">Kompilierte Modelle</span><span class="sxs-lookup"><span data-stu-id="c9b11-159">Compiled models</span></span>

<span data-ttu-id="c9b11-160">Nachverfolgbar über [Issue 1906](https://github.com/dotnet/efcore/issues/1906)</span><span class="sxs-lookup"><span data-stu-id="c9b11-160">Tracked by [#1906](https://github.com/dotnet/efcore/issues/1906)</span></span>

<span data-ttu-id="c9b11-161">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-161">Status: Not started</span></span>

<span data-ttu-id="c9b11-162">T-Shirt-Größe: X-Large</span><span class="sxs-lookup"><span data-stu-id="c9b11-162">T-shirt size: X-Large</span></span>

<span data-ttu-id="c9b11-163">Mit kompilierten Modellen kann eine kompilierte Version des EF-Modells generiert werden.</span><span class="sxs-lookup"><span data-stu-id="c9b11-163">Compiled models will allow the generation of a compiled form of the EF model.</span></span> <span data-ttu-id="c9b11-164">Dadurch wird sowohl die Startleistung als auch eine allgemein bessere Leistung beim Zugriff auf das Modell erzielt.</span><span class="sxs-lookup"><span data-stu-id="c9b11-164">This will provide both better startup performance, as well as generally better performance when accessing the model.</span></span>

### <a name="techempower-fortunes"></a><span data-ttu-id="c9b11-165">TechEmpower – Fortunes</span><span class="sxs-lookup"><span data-stu-id="c9b11-165">TechEmpower Fortunes</span></span>

<span data-ttu-id="c9b11-166">Nachverfolgbar über [Issue 23611](https://github.com/dotnet/efcore/issues/23611)</span><span class="sxs-lookup"><span data-stu-id="c9b11-166">Tracked by [#23611](https://github.com/dotnet/efcore/issues/23611)</span></span>

<span data-ttu-id="c9b11-167">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-167">Status: Not started</span></span>

<span data-ttu-id="c9b11-168">T-Shirt-Größe: X-Large</span><span class="sxs-lookup"><span data-stu-id="c9b11-168">T-shirt size: X-Large</span></span>

<span data-ttu-id="c9b11-169">Seit mehreren Jahren werden die [TechEmpower-Benchmarks](https://www.techempower.com/benchmarks/), die den Branchenstandard darstellen, unter .NET für eine PostgreSQL-Datenbank ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c9b11-169">We have been running the industry standard [TechEmpower benchmarks](https://www.techempower.com/benchmarks/) on .NET against a PostgreSQL database for several years.</span></span> <span data-ttu-id="c9b11-170">Der [Fortunes-Benchmark](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune) ist besonders relevant für EF-Szenarios.</span><span class="sxs-lookup"><span data-stu-id="c9b11-170">The [Fortunes benchmark](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune) is particularly relevant to EF scenarios.</span></span> <span data-ttu-id="c9b11-171">Es gibt mehrere Variationen dieses Benchmarks, einschließlich:</span><span class="sxs-lookup"><span data-stu-id="c9b11-171">We have multiple variations of this benchmark, including:</span></span>

- <span data-ttu-id="c9b11-172">Einer Implementierung, die ADO.NET direkt verwendet.</span><span class="sxs-lookup"><span data-stu-id="c9b11-172">An implementation that uses ADO.NET directly.</span></span> <span data-ttu-id="c9b11-173">Dies ist die schnellste Implementierung der drei hier aufgelisteten.</span><span class="sxs-lookup"><span data-stu-id="c9b11-173">This is the fastest implementation of the three listed here.</span></span>
- <span data-ttu-id="c9b11-174">Einer Implementierung, die [Dapper](https://www.nuget.org/packages/Dapper/) verwendet.</span><span class="sxs-lookup"><span data-stu-id="c9b11-174">An implementation that uses [Dapper](https://www.nuget.org/packages/Dapper/).</span></span> <span data-ttu-id="c9b11-175">Dieses Vorgehen ist langsamer als die direkte Verwendung von ADO.NET, aber immer noch schnell.</span><span class="sxs-lookup"><span data-stu-id="c9b11-175">This is slower than using ADO.NET directly, but still fast.</span></span>
- <span data-ttu-id="c9b11-176">Einer Implementierung, die EF Core verwendet.</span><span class="sxs-lookup"><span data-stu-id="c9b11-176">An implementation that uses EF Core.</span></span> <span data-ttu-id="c9b11-177">Dieses Vorgehen stellt derzeit die langsamste Implementierung der drei Optionen dar.</span><span class="sxs-lookup"><span data-stu-id="c9b11-177">This is currently the slowest implementation of the three.</span></span>

<span data-ttu-id="c9b11-178">Das Ziel von EF Core 6.0 besteht darin, die Leistung von EF Core an die von Dapper im TechEmpower-Fortunes-Benchmark anzugleichen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-178">The goal for EF Core 6.0 is to get the EF Core performance to match that of Dapper on the TechEmpower Fortunes benchmark.</span></span> <span data-ttu-id="c9b11-179">(Dies ist eine große Herausforderung, aber Microsoft wird alles daran setzen, die bestmögliche Lösung zu entwickeln.)</span><span class="sxs-lookup"><span data-stu-id="c9b11-179">(This is a significant challenge but we will do our best to get as close as we can.)</span></span>

### <a name="linkeraot"></a><span data-ttu-id="c9b11-180">Linker/AOT</span><span class="sxs-lookup"><span data-stu-id="c9b11-180">Linker/AOT</span></span>

<span data-ttu-id="c9b11-181">Nachverfolgbar über [Issue 10963](https://github.com/dotnet/efcore/issues/10963)</span><span class="sxs-lookup"><span data-stu-id="c9b11-181">Tracked by [#10963](https://github.com/dotnet/efcore/issues/10963)</span></span>

<span data-ttu-id="c9b11-182">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-182">Status: Not started</span></span>

<span data-ttu-id="c9b11-183">T-Shirt-Größe: Mittel</span><span class="sxs-lookup"><span data-stu-id="c9b11-183">T-shirt size: Medium</span></span>

<span data-ttu-id="c9b11-184">EF Core erzeugt zur Laufzeit große Mengen von Code.</span><span class="sxs-lookup"><span data-stu-id="c9b11-184">EF Core performs large amounts of runtime code generation.</span></span> <span data-ttu-id="c9b11-185">Dies ist eine Herausforderung für App-Modelle wie Xamarin und Blazor, die auf Tree Shaking mithilfe des Linkers angewiesen sind, sowie für Plattformen, die keine dynamische Kompilierung zulassen (z. B. iOS).</span><span class="sxs-lookup"><span data-stu-id="c9b11-185">This is challenging for app models that depend on linker tree shaking, such as Xamarin and Blazor, and platforms that don't allow dynamic compilation, such as iOS.</span></span> <span data-ttu-id="c9b11-186">Dieser Bereich wird im Rahmen von EF Core 6.0 weiterhin untersucht, und es werden, wenn möglich, strategische Verbesserungen vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-186">We will continue investigating in this space as part of EF Core 6.0 and make targeted improvements as we can.</span></span> <span data-ttu-id="c9b11-187">Es ist jedoch nicht zu erwarten, dass die Lücke im Zeitrahmen des 6.0-Releases vollständig geschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="c9b11-187">However, we do not expect to fully close the gap in the 6.0 time frame.</span></span>

## <a name="migrations-and-deployment"></a><span data-ttu-id="c9b11-188">Migrationen und Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="c9b11-188">Migrations and deployment</span></span>

<span data-ttu-id="c9b11-189">Aufbauend auf den [Untersuchungen, die für EF Core 5.0 durchgeführt wurden](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience), ist geplant, die verbesserte Unterstützung für die Verwaltung von Migrationen und das Bereitstellen von Datenbanken einzuführen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-189">Following on from the [investigations done for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience), we plan to introduce improved support for managing migrations and deploying databases.</span></span> <span data-ttu-id="c9b11-190">Diese Verbesserung betrifft zwei Hauptbereiche:</span><span class="sxs-lookup"><span data-stu-id="c9b11-190">This includes two major areas:</span></span>

### <a name="migrations-bundles"></a><span data-ttu-id="c9b11-191">Migrationsbundles</span><span class="sxs-lookup"><span data-stu-id="c9b11-191">Migrations bundles</span></span>

<span data-ttu-id="c9b11-192">Nachverfolgbar über [Issue 19693](https://github.com/dotnet/efcore/issues/19693)</span><span class="sxs-lookup"><span data-stu-id="c9b11-192">Tracked by [#19693](https://github.com/dotnet/efcore/issues/19693)</span></span>

<span data-ttu-id="c9b11-193">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-193">Status: Not started</span></span>

<span data-ttu-id="c9b11-194">T-Shirt-Größe: Mittel</span><span class="sxs-lookup"><span data-stu-id="c9b11-194">T-shirt size: Medium</span></span>

<span data-ttu-id="c9b11-195">Ein Migrationsbundle ist eine eigenständiges ausführbares Paket, das Migrationen auf eine Produktionsdatenbank anwendet.</span><span class="sxs-lookup"><span data-stu-id="c9b11-195">A migrations bundle is a self-contained executable that applies migrations to a production database.</span></span> <span data-ttu-id="c9b11-196">Das Verhalten entspricht `dotnet ef database update`, sollte die Bereitstellung von SSH/Docker/PowerShell jedoch erheblich vereinfachen, da alle erforderlichen Ressourcen in dem einzelnen ausführbaren Paket enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="c9b11-196">The behavior will match `dotnet ef database update`, but should make SSH/Docker/PowerShell deployment much easier, since everything needed is contained in the single executable.</span></span>

### <a name="managing-migrations"></a><span data-ttu-id="c9b11-197">Verwalten von Migrationen</span><span class="sxs-lookup"><span data-stu-id="c9b11-197">Managing migrations</span></span>

<span data-ttu-id="c9b11-198">Nachverfolgbar über [Issue 22945](https://github.com/dotnet/efcore/issues/22945)</span><span class="sxs-lookup"><span data-stu-id="c9b11-198">Tracked by [#22945](https://github.com/dotnet/efcore/issues/22945)</span></span>

<span data-ttu-id="c9b11-199">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-199">Status: Not started</span></span>

<span data-ttu-id="c9b11-200">T-Shirt-Größe: Large</span><span class="sxs-lookup"><span data-stu-id="c9b11-200">T-shirt size: Large</span></span>

<span data-ttu-id="c9b11-201">Die Anzahl der für eine Anwendung erstellten Migrationen kann zu einer Belastung werden.</span><span class="sxs-lookup"><span data-stu-id="c9b11-201">The number of migrations created for an application can grow to become a burden.</span></span> <span data-ttu-id="c9b11-202">Außerdem werden diese Migrationen häufig mit der Anwendung bereitgestellt, selbst wenn dies nicht erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="c9b11-202">In addition, these migrations are frequently deployed with the application even when this is not needed.</span></span> <span data-ttu-id="c9b11-203">In EF Core 6.0 ist geplant, dies durch bessere Tools und eine verbesserte Projekt-/Assemblyverwaltung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="c9b11-203">In EF Core 6.0, we plan to improve this through better tooling and project/assembly management.</span></span> <span data-ttu-id="c9b11-204">Zwei konkrete Issues, die angegangen werden sollen, sind das zum [Hineinzwängen vieler Migrationen in eine einzige](https://github.com/dotnet/efcore/issues/2174) sowie zum [erneuten Generieren einer sauberen Modellmomentaufnahme](https://github.com/dotnet/efcore/issues/18557).</span><span class="sxs-lookup"><span data-stu-id="c9b11-204">Two specific issues we plan to address are [squash many migrations into one](https://github.com/dotnet/efcore/issues/2174) and [regenerate a clean model snapshot](https://github.com/dotnet/efcore/issues/18557).</span></span>

## <a name="improve-existing-features-and-fix-bugs"></a><span data-ttu-id="c9b11-205">Verbessern bestehender Features und Beheben von Fehlern</span><span class="sxs-lookup"><span data-stu-id="c9b11-205">Improve existing features and fix bugs</span></span>

<span data-ttu-id="c9b11-206">Alle [Fehler, die dem 6.0.0-Meilenstein zugeordnet sind](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0), sind zurzeit für dieses Release eingeplant.</span><span class="sxs-lookup"><span data-stu-id="c9b11-206">Any [issue or bug assigned to the 6.0.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0) is currently planned for this release.</span></span> <span data-ttu-id="c9b11-207">Dazu zählen viele kleine Erweiterungen und Fehlerfixes.</span><span class="sxs-lookup"><span data-stu-id="c9b11-207">This includes many small enhancements and bug fixes.</span></span>

### <a name="ef6-query-parity"></a><span data-ttu-id="c9b11-208">EF6-Abfrageparität</span><span class="sxs-lookup"><span data-stu-id="c9b11-208">EF6 query parity</span></span>

<span data-ttu-id="c9b11-209">Nachverfolgbar über [Issues, die mit „ef6-parity“ gekennzeichnet und im 6.0-Meilenstein enthalten sind](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)</span><span class="sxs-lookup"><span data-stu-id="c9b11-209">Tracked by [issues labeled with 'ef6-parity' and in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)</span></span>

<span data-ttu-id="c9b11-210">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-210">Status: Not started</span></span>

<span data-ttu-id="c9b11-211">T-Shirt-Größe: Large</span><span class="sxs-lookup"><span data-stu-id="c9b11-211">T-shirt size: Large</span></span>

<span data-ttu-id="c9b11-212">EF Core 5.0 unterstützt neben Mustern, die in EF6 nicht unterstützt werden, auch die meisten von EF6 unterstützten Abfragemuster.</span><span class="sxs-lookup"><span data-stu-id="c9b11-212">EF Core 5.0 supports most query patterns supported by EF6, in addition to patterns not supported in EF6.</span></span> <span data-ttu-id="c9b11-213">In EF Core 6.0 ist geplant, diese Lücke zu schließen, sodass unterstützte EF6-Abfragen u. a. in den unterstützten EF Core-Abfragen inbegriffen sind.</span><span class="sxs-lookup"><span data-stu-id="c9b11-213">For EF Core 6.0, we plan to close the gap and make supported EF Core queries a true superset of supported EF6 queries.</span></span> <span data-ttu-id="c9b11-214">Diese Verbesserung basiert auf der Untersuchung der Lücken und umfasst bereits GroupBy-Issues wie das zum [Übersetzen von GroupBy gefolgt von FirstOrDefault](https://github.com/dotnet/efcore/issues/12088) und zur Unterstützung unformatierter SQL-Abfragen für [primitive](https://github.com/dotnet/efcore/issues/11624) und [nicht zugeordnete](https://github.com/dotnet/efcore/issues/10753) Typen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-214">This will be driven by investigation of the gaps, but already includes GroupBy issues such as [translate GroupBy followed by FirstOrDefault](https://github.com/dotnet/efcore/issues/12088), and raw SQL queries for [primitive](https://github.com/dotnet/efcore/issues/11624) and [unmapped](https://github.com/dotnet/efcore/issues/10753) types.</span></span>  

### <a name="value-objects"></a><span data-ttu-id="c9b11-215">Wertobjekte</span><span class="sxs-lookup"><span data-stu-id="c9b11-215">Value objects</span></span>

<span data-ttu-id="c9b11-216">Nachverfolgbar über [Issue 9906](https://github.com/dotnet/efcore/issues/9906)</span><span class="sxs-lookup"><span data-stu-id="c9b11-216">Tracked by [#9906](https://github.com/dotnet/efcore/issues/9906)</span></span>

<span data-ttu-id="c9b11-217">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-217">Status: Not started</span></span>

<span data-ttu-id="c9b11-218">T-Shirt-Größe: Mittel</span><span class="sxs-lookup"><span data-stu-id="c9b11-218">T-shirt size: Medium</span></span>

<span data-ttu-id="c9b11-219">Früher war das Entwicklungsteam der Ansicht, dass nicht eigenständige, für die [Aggregatunterstützung](https://www.martinfowler.com/bliki/DDD_Aggregate.html) vorgesehene Entitäten auch eine angemessene Näherung an [Wertobjekte](https://www.martinfowler.com/bliki/ValueObject.html) darstellen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-219">It was previously the team view that owned entities, intended for [aggregate support](https://www.martinfowler.com/bliki/DDD_Aggregate.html), would also be a reasonable approximation to [value objects](https://www.martinfowler.com/bliki/ValueObject.html).</span></span> <span data-ttu-id="c9b11-220">Die Erfahrung hat jedoch gezeigt, dass dies nicht der Fall ist.</span><span class="sxs-lookup"><span data-stu-id="c9b11-220">Experience has shown this not to be the case.</span></span> <span data-ttu-id="c9b11-221">Daher ist für EF Core 6.0 geplant, eine bessere Leistung zu erzielen, die sich im domänengesteuerten Design auf die Anforderungen von Wertobjekten konzentriert.</span><span class="sxs-lookup"><span data-stu-id="c9b11-221">Therefore, in EF Core 6.0 we plan to introduce a better experience focused on the needs of value objects in domain-driven design.</span></span> <span data-ttu-id="c9b11-222">Dieser Ansatz wird auf Wertkonvertern anstatt auf nicht eigenständigen Entitäten basieren.</span><span class="sxs-lookup"><span data-stu-id="c9b11-222">This approach will be based on value converters rather than owned entities.</span></span>

<span data-ttu-id="c9b11-223">Bisher ist vorgesehen, dass [Wertkonverter zugelassen werden sollen, die mehreren Spalten zugeordnet sind](https://github.com/dotnet/efcore/issues/13947).</span><span class="sxs-lookup"><span data-stu-id="c9b11-223">This work is initially scoped to allow [value converters which map to multiple columns](https://github.com/dotnet/efcore/issues/13947).</span></span> <span data-ttu-id="c9b11-224">Unter Umständen wird während der Veröffentlichung auf Grundlage von Feedback zusätzliche Unterstützung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c9b11-224">We may pull in additional support based on feedback during the release.</span></span>

### <a name="cosmos-database-provider"></a><span data-ttu-id="c9b11-225">Cosmos-Datenbankanbieter</span><span class="sxs-lookup"><span data-stu-id="c9b11-225">Cosmos database provider</span></span>

<span data-ttu-id="c9b11-226">Nachverfolgbar über [Issues, die mit „area-cosmos“ gekennzeichnet und im 6.0-Meilenstein enthalten sind](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)</span><span class="sxs-lookup"><span data-stu-id="c9b11-226">Tracked by [issues labeled with 'area-cosmos' and in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)</span></span>

<span data-ttu-id="c9b11-227">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-227">Status: Not started</span></span>

<span data-ttu-id="c9b11-228">T-Shirt-Größe: Large</span><span class="sxs-lookup"><span data-stu-id="c9b11-228">T-shirt size: Large</span></span>

<span data-ttu-id="c9b11-229">Es wird aktiv Feedback zu den gewünschten Verbesserungen am Cosmos-Anbieter in EF Core 6.0 gesammelt.</span><span class="sxs-lookup"><span data-stu-id="c9b11-229">We are actively gathering feedback on which improvements to make to the Cosmos provider in EF Core 6.0.</span></span> <span data-ttu-id="c9b11-230">Dieses Dokument wird den eingehenden Rückmeldungen entsprechend aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="c9b11-230">We will update this document as we learn more.</span></span> <span data-ttu-id="c9b11-231">Vorerst sollten Sie unbedingt für die Cosmos-Features abstimmen (👍), die Sie benötigen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-231">For now, please make sure to vote (👍) for the Cosmos features that you need.</span></span>

### <a name="expose-model-building-conventions-to-applications"></a><span data-ttu-id="c9b11-232">Verfügbarmachen von Konventionen zur Modellerstellung für Anwendungen</span><span class="sxs-lookup"><span data-stu-id="c9b11-232">Expose model building conventions to applications</span></span>

<span data-ttu-id="c9b11-233">Nachverfolgbar über [Issue 214](https://github.com/dotnet/efcore/issues/214)</span><span class="sxs-lookup"><span data-stu-id="c9b11-233">Tracked by [#214](https://github.com/dotnet/efcore/issues/214)</span></span>

<span data-ttu-id="c9b11-234">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-234">Status: Not started</span></span>

<span data-ttu-id="c9b11-235">T-Shirt-Größe: Medium</span><span class="sxs-lookup"><span data-stu-id="c9b11-235">T-shirt size: medium</span></span>

<span data-ttu-id="c9b11-236">EF Core verwendet verschiedene Konventionen zum Erstellen von Modellen aus .NET-Typen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-236">EF Core uses a set of conventions for building a model from .NET types.</span></span> <span data-ttu-id="c9b11-237">Diese Konventionen werden zurzeit vom Datenbankanbieter vorgegeben.</span><span class="sxs-lookup"><span data-stu-id="c9b11-237">These conventions are currently controlled by the database provider.</span></span> <span data-ttu-id="c9b11-238">In EF Core 6.0 sollen Anwendungen in der Lage sein, diese Konventionen zu ändern.</span><span class="sxs-lookup"><span data-stu-id="c9b11-238">In EF Core 6.0, we intend to allow applications to hook into and change these conventions.</span></span>

### <a name="zero-bug-balance-zbb"></a><span data-ttu-id="c9b11-239">Null-Fehler-Bilanz</span><span class="sxs-lookup"><span data-stu-id="c9b11-239">Zero bug balance (ZBB)</span></span>

<span data-ttu-id="c9b11-240">Nachverfolgbar über [Issues, die im 6.0-Meilenstein mit `type-bug` gekennzeichnet sind](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)</span><span class="sxs-lookup"><span data-stu-id="c9b11-240">Tracked by [issues labeled with `type-bug` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="c9b11-241">Status: In Bearbeitung</span><span class="sxs-lookup"><span data-stu-id="c9b11-241">Status: In-progress</span></span>

<span data-ttu-id="c9b11-242">T-Shirt-Größe: Large</span><span class="sxs-lookup"><span data-stu-id="c9b11-242">T-shirt size: Large</span></span>

<span data-ttu-id="c9b11-243">Es ist geplant, alle ausstehenden Fehlerbehebungen im Zeitrahmen von EF Core 6.0 zu erledigen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-243">We plan to fix all outstanding bugs during the EF Core 6.0 time frame.</span></span> <span data-ttu-id="c9b11-244">Folgendes sollte beachtet werden:</span><span class="sxs-lookup"><span data-stu-id="c9b11-244">Some things to keep in mind:</span></span>

- <span data-ttu-id="c9b11-245">Dies gilt insbesondere für Issues, die mit dem Tag [type-bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug) gekennzeichnet sind.</span><span class="sxs-lookup"><span data-stu-id="c9b11-245">This specifically applies to issues labeled [type-bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug).</span></span>
- <span data-ttu-id="c9b11-246">Es werden Ausnahmen gemacht, z. B. wenn für eine ordnungsgemäße Fehlerbehebung eine Entwurfsänderung oder ein neues Feature erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="c9b11-246">There will be exceptions, such as when the bug requires a design change or new feature to fix properly.</span></span> <span data-ttu-id="c9b11-247">Diese Issues werden mit der Bezeichnung `blocked` gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="c9b11-247">These issues will be marked with the `blocked` label.</span></span>
- <span data-ttu-id="c9b11-248">Je näher das GA-/RTM-Release rückt, werden Fehler wie üblich bei Bedarf auf Grundlage des Risikos priorisiert, das sie darstellen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-248">We will punt bugs based on risk when needed as is normal as we get close to a GA/RTM release.</span></span>

### <a name="miscellaneous-features"></a><span data-ttu-id="c9b11-249">Verschiedene Features</span><span class="sxs-lookup"><span data-stu-id="c9b11-249">Miscellaneous features</span></span>

<span data-ttu-id="c9b11-250">Nachverfolgbar über [Issues, die im 6.0-Meilenstein mit `type-enhancement` gekennzeichnet sind](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)</span><span class="sxs-lookup"><span data-stu-id="c9b11-250">Tracked by [issues labeled with `type-enhancement` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)</span></span>

<span data-ttu-id="c9b11-251">Status: In Bearbeitung</span><span class="sxs-lookup"><span data-stu-id="c9b11-251">Status: In-progress</span></span>

<span data-ttu-id="c9b11-252">T-Shirt-Größe: Large</span><span class="sxs-lookup"><span data-stu-id="c9b11-252">T-shirt size: Large</span></span>

<span data-ttu-id="c9b11-253">Zu den verschiedenen für EF 6.0 geplanten Features gehören u. a.:</span><span class="sxs-lookup"><span data-stu-id="c9b11-253">Miscellaneous features planned for EF 6.0 include, but are not limited to:</span></span>

- [<span data-ttu-id="c9b11-254">Aufgeteilte Abfragen für andere Sammlungen als Navigationssammlungen</span><span class="sxs-lookup"><span data-stu-id="c9b11-254">Split query for non-navigation collections</span></span>](https://github.com/dotnet/efcore/issues/21234)
- [<span data-ttu-id="c9b11-255">Erkennen einfacher Jointabellen beim Reverse Engineering und Erstellen von m:n-Beziehungen</span><span class="sxs-lookup"><span data-stu-id="c9b11-255">Detect simple join tables in reverse engineering and create many-to-many relationships</span></span>](https://github.com/dotnet/efcore/issues/22475)
- [<span data-ttu-id="c9b11-256">Vervollständigen der Volltext-/Freitextsuche in SQLite und SQL Server</span><span class="sxs-lookup"><span data-stu-id="c9b11-256">Complete full/free-text search on SQLite and SQL Server</span></span>](https://github.com/dotnet/efcore/issues/4823)
- [<span data-ttu-id="c9b11-257">Räumliche Indizes in SQL Server</span><span class="sxs-lookup"><span data-stu-id="c9b11-257">SQL Server spatial indexes</span></span>](https://github.com/dotnet/efcore/issues/12538)
- [<span data-ttu-id="c9b11-258">Mechanismus/API zum Angeben einer Standardkonvertierung für eine beliebige Eigenschaft eines bestimmten Typs im Modell</span><span class="sxs-lookup"><span data-stu-id="c9b11-258">Mechanism/API to specify a default conversion for any property of a given type in the model</span></span>](https://github.com/dotnet/efcore/issues/10784)
- [<span data-ttu-id="c9b11-259">Verwenden der neuen Batchverarbeitungs-API von ADO.NET</span><span class="sxs-lookup"><span data-stu-id="c9b11-259">Use the new batching API from ADO.NET</span></span>](https://github.com/dotnet/efcore/issues/18990)

## <a name="net-integration"></a><span data-ttu-id="c9b11-260">.NET-Integration</span><span class="sxs-lookup"><span data-stu-id="c9b11-260">.NET integration</span></span>

<span data-ttu-id="c9b11-261">Das EF Core-Team arbeitet auch an mehreren verwandten, aber voneinander unabhängigen Technologien.</span><span class="sxs-lookup"><span data-stu-id="c9b11-261">The EF Core team also works on several related but independent technologies.</span></span> <span data-ttu-id="c9b11-262">Insbesondere ist die Arbeit an Folgendem geplant:</span><span class="sxs-lookup"><span data-stu-id="c9b11-262">In particular, we plan to work on:</span></span>

### <a name="enhancements-to-systemdata"></a><span data-ttu-id="c9b11-263">Verbesserungen an System.Data</span><span class="sxs-lookup"><span data-stu-id="c9b11-263">Enhancements to System.Data</span></span>

<span data-ttu-id="c9b11-264">Nachverfolgbar über [Issues im Repository dotnet\runtime, die im 6.0-Meilenstein mit `area-System.Data` gekennzeichnet sind](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)</span><span class="sxs-lookup"><span data-stu-id="c9b11-264">Tracked by [issues in the dotnet\runtime repo labeled with `area-System.Data` in the 6.0 milestone](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)</span></span>

<span data-ttu-id="c9b11-265">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-265">Status: Not started</span></span>

<span data-ttu-id="c9b11-266">T-Shirt-Größe: Large</span><span class="sxs-lookup"><span data-stu-id="c9b11-266">T-shirt size: Large</span></span>

<span data-ttu-id="c9b11-267">Diese Arbeit umfasst Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c9b11-267">This work includes:</span></span>

- <span data-ttu-id="c9b11-268">Implementierung der neuen [Batchverarbeitungs-API](https://github.com/dotnet/runtime/issues/28633)</span><span class="sxs-lookup"><span data-stu-id="c9b11-268">Implementation of the new [batching API](https://github.com/dotnet/runtime/issues/28633).</span></span>
- <span data-ttu-id="c9b11-269">Kontinuierliche Zusammenarbeit mit anderen .NET-Teams und der Community, um ADO.NET besser zu verstehen und weiterzuentwickeln</span><span class="sxs-lookup"><span data-stu-id="c9b11-269">Continued work with other .NET teams and the community to understand and evolve ADO.NET.</span></span>
- <span data-ttu-id="c9b11-270">[Standardisieren von DiagnosticSource für die Ablaufverfolgung in Komponenten von System.Data.\*](https://github.com/dotnet/runtime/issues/22336)</span><span class="sxs-lookup"><span data-stu-id="c9b11-270">[Standardize on DiagnosticSource for tracing in System.Data.\* components](https://github.com/dotnet/runtime/issues/22336).</span></span>

### <a name="enhancements-to-microsoftdatasqlite"></a><span data-ttu-id="c9b11-271">Verbesserungen an Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="c9b11-271">Enhancements to Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="c9b11-272">Nachverfolgbar über [Issues, die im 6.0-Meilenstein mit `type-enhancement` und `area-adonet-sqlite` gekennzeichnet sind](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)</span><span class="sxs-lookup"><span data-stu-id="c9b11-272">Tracked by [issues labeled with `type-enhancement` and `area-adonet-sqlite` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)</span></span>

<span data-ttu-id="c9b11-273">Status: In Bearbeitung</span><span class="sxs-lookup"><span data-stu-id="c9b11-273">Status: In-progress</span></span>

<span data-ttu-id="c9b11-274">T-Shirt-Größe: Mittel</span><span class="sxs-lookup"><span data-stu-id="c9b11-274">T-shirt size: Medium</span></span>

<span data-ttu-id="c9b11-275">Es sind einige kleine Verbesserungen für Microsoft.Data.Sqlite geplant, einschließlich [Verbindungspooling](https://github.com/dotnet/efcore/issues/13837) und [vorbereiteten Anweisungen](https://github.com/dotnet/efcore/issues/14044) für eine bessere Leistung.</span><span class="sxs-lookup"><span data-stu-id="c9b11-275">Several small improvements are planned for the Microsoft.Data.Sqlite, including [connection pooling](https://github.com/dotnet/efcore/issues/13837) and [prepared statements](https://github.com/dotnet/efcore/issues/14044) for performance.</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="c9b11-276">Nullwerte zulassende Verweistypen</span><span class="sxs-lookup"><span data-stu-id="c9b11-276">Nullable reference types</span></span>

<span data-ttu-id="c9b11-277">Nachverfolgbar über [Issue 14150](https://github.com/dotnet/efcore/issues/14150)</span><span class="sxs-lookup"><span data-stu-id="c9b11-277">Tracked by [#14150](https://github.com/dotnet/efcore/issues/14150)</span></span>

<span data-ttu-id="c9b11-278">Status: In Bearbeitung</span><span class="sxs-lookup"><span data-stu-id="c9b11-278">Status: In-progress</span></span>

<span data-ttu-id="c9b11-279">T-Shirt-Größe: Large</span><span class="sxs-lookup"><span data-stu-id="c9b11-279">T-shirt size: Large</span></span>

<span data-ttu-id="c9b11-280">Der EF Core-Code wird mit Anmerkungen versehen, damit [Nullwerte zulassende Verweistypen verwendet werden](/dotnet/csharp/nullable-references).</span><span class="sxs-lookup"><span data-stu-id="c9b11-280">We will annotate the EF Core code to use [nullable reference types](/dotnet/csharp/nullable-references).</span></span>

## <a name="experiments-and-investigations"></a><span data-ttu-id="c9b11-281">Experimente und Untersuchungen</span><span class="sxs-lookup"><span data-stu-id="c9b11-281">Experiments and investigations</span></span>

<span data-ttu-id="c9b11-282">Das EF-Team plant, während des Zeitrahmens von EF Core 6.0 in zwei Bereichen Zeit in Untersuchungen und Experimente zu investieren.</span><span class="sxs-lookup"><span data-stu-id="c9b11-282">The EF team is planning to invest time during the EF Core 6.0 timeframe experimenting and investigating in two areas.</span></span> <span data-ttu-id="c9b11-283">Dabei handelt es sich um einen Lernprozess. Aus diesem Grund wurden keine konkreten Pflichtergebnisse für Release 6.0 verabredet.</span><span class="sxs-lookup"><span data-stu-id="c9b11-283">This is a learning process and as such no concrete deliverables are planned for the 6.0 release.</span></span>

### <a name="sqlservercore"></a><span data-ttu-id="c9b11-284">SqlServer.Core</span><span class="sxs-lookup"><span data-stu-id="c9b11-284">SqlServer.Core</span></span>

<span data-ttu-id="c9b11-285">Nachverfolgbar im [Repository „.NET Data Lab“](https://github.com/dotnet/datalab/)</span><span class="sxs-lookup"><span data-stu-id="c9b11-285">Tracked in the [.NET Data Lab repo](https://github.com/dotnet/datalab/)</span></span>

<span data-ttu-id="c9b11-286">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-286">Status: Not started</span></span>

<span data-ttu-id="c9b11-287">T-Shirt-Größe: Laufend</span><span class="sxs-lookup"><span data-stu-id="c9b11-287">T-shirt size: Ongoing</span></span>

<span data-ttu-id="c9b11-288">[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) ist ein ADO.NET-Datenbankanbieter mit vollem Featureumfang für SQL Server.</span><span class="sxs-lookup"><span data-stu-id="c9b11-288">[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) is a fully-featured ADO.NET database provider for SQL Server.</span></span> <span data-ttu-id="c9b11-289">Er unterstützt eine breite Palette von SQL Server-Features, sowohl in .NET Core als auch im .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="c9b11-289">It supports a broad range of SQL Server features on both .NET Core and .NET Framework.</span></span> <span data-ttu-id="c9b11-290">Microsoft.Data.SqlClient ist jedoch auch eine große und alte Codebasis mit vielen komplexen Interaktionen zwischen ihren verschiedenen Verhaltensweisen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-290">However, it is also a large and old codebase with many complex interactions between its behaviors.</span></span> <span data-ttu-id="c9b11-291">Dies erschwert die Ermittlung der möglichen Vorteile, die durch neuere .NET Core-Features erzielt werden könnten.</span><span class="sxs-lookup"><span data-stu-id="c9b11-291">This makes it difficult to investigate the potential gains the could be made using newer .NET Core features.</span></span> <span data-ttu-id="c9b11-292">Aus diesem Grund wird ein Experiment in Zusammenarbeit mit der Community eingeleitet, das das Potenzial eines SQL Server-Hochleistungstreibers für .NET bestimmen soll.</span><span class="sxs-lookup"><span data-stu-id="c9b11-292">Therefore, we are starting an experiment in collaboration with the community to determine what potential there is for a highly performing SQL Server driver for .NET.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c9b11-293">Dies hat keinen Einfluss auf das Engagement für Microsoft.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="c9b11-293">Investment in Microsoft.Data.SqlClient is not changing.</span></span> <span data-ttu-id="c9b11-294">Microsoft.Data.SqlClient bleibt weiterhin die empfohlene Methode zum Herstellen einer Verbindung mit SQL Server und SQL Azure, sowohl mit als auch ohne EF Core.</span><span class="sxs-lookup"><span data-stu-id="c9b11-294">It will continue to be the recommended way to connect to SQL Server and SQL Azure, both with and without EF Core.</span></span> <span data-ttu-id="c9b11-295">Außerdem ist für neue SQL Server-Features weiterhin ab dem Zeitpunkt ihrer Einführung Unterstützung verfügbar.</span><span class="sxs-lookup"><span data-stu-id="c9b11-295">It will continue to support new SQL Server features as they are introduced.</span></span>

### <a name="graphql"></a><span data-ttu-id="c9b11-296">GraphQL</span><span class="sxs-lookup"><span data-stu-id="c9b11-296">GraphQL</span></span>

<span data-ttu-id="c9b11-297">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-297">Status: Not started</span></span>

<span data-ttu-id="c9b11-298">T-Shirt-Größe: Laufend</span><span class="sxs-lookup"><span data-stu-id="c9b11-298">T-shirt size: Ongoing</span></span>

<span data-ttu-id="c9b11-299">[GraphQL](https://graphql.org/) hat in den letzten Jahren auf einer Vielzahl von Plattformen an Zugkraft gewonnen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-299">[GraphQL](https://graphql.org/) has been gaining traction over the last few years across a variety of platforms.</span></span> <span data-ttu-id="c9b11-300">Es ist geplant, die Möglichkeiten von GraphQL weiter auszuloten und Wege zu finden, die Nutzung mit .NET zu verbessern.</span><span class="sxs-lookup"><span data-stu-id="c9b11-300">We plan to investigate the space and find ways to improve the experience with .NET.</span></span> <span data-ttu-id="c9b11-301">Zu diesem Zweck erfolgt eine Zusammenarbeit mit der Community, um das bestehende Ökosystem besser zu verstehen und unterstützen zu können.</span><span class="sxs-lookup"><span data-stu-id="c9b11-301">This will involve working with the community on understanding and supporting the existing ecosystem.</span></span> <span data-ttu-id="c9b11-302">Unter Umständen sind auch bestimmte Investitionen von Microsoft erforderlich, entweder in Form von Beiträgen zu vorhandenen Arbeiten oder bei der Entwicklung von Ergänzungen des Microsoft-Stapels.</span><span class="sxs-lookup"><span data-stu-id="c9b11-302">It may also involve specific investment from Microsoft, either in the form of contributions to existing work or in developing complimentary pieces in the Microsoft stack.</span></span>

### <a name="dataverse-formerly-common-data-services"></a><span data-ttu-id="c9b11-303">DataVerse (ehemals Common Data Services)</span><span class="sxs-lookup"><span data-stu-id="c9b11-303">DataVerse (formerly Common Data Services)</span></span>

<span data-ttu-id="c9b11-304">Status: Nicht begonnen</span><span class="sxs-lookup"><span data-stu-id="c9b11-304">Status: Not started</span></span>

<span data-ttu-id="c9b11-305">T-Shirt-Größe: Laufend</span><span class="sxs-lookup"><span data-stu-id="c9b11-305">T-shirt size: Ongoing</span></span>

<span data-ttu-id="c9b11-306">[DataVerse](/powerapps/maker/data-platform/data-platform-intro) ist ein spaltenbasierter Datenspeicher, der für die schnelle Entwicklung von Geschäftsanwendungen konzipiert wurde.</span><span class="sxs-lookup"><span data-stu-id="c9b11-306">[DataVerse](/powerapps/maker/data-platform/data-platform-intro) is a column-based data store designed for rapid development of business applications.</span></span> <span data-ttu-id="c9b11-307">DataVerse verarbeitet automatisch komplexe Datentypen wie binäre Objekte (Binary Large Objects, Blobs) und verfügt über integrierte Entitäten und Beziehungen wie Organisationen und Kontakte.</span><span class="sxs-lookup"><span data-stu-id="c9b11-307">It automatically handles complex data types like binary objects (BLOBs) and has built-in entities and relationships like organizations and contacts.</span></span> <span data-ttu-id="c9b11-308">Es ist zwar ein SDK vorhanden, für Entwickler kann ein EF Core-Anbieter dennoch von Vorteil sein, um erweiterte LINQ-Abfragen zu verwenden, Arbeitseinheiten zu nutzen und über eine konsistente Datenzugriffs-API zu verfügen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-308">An SDK exists but developers may benefit from having an EF Core provider to use advanced LINQ queries, take advantage of unit of work and have a consistent data access API.</span></span> <span data-ttu-id="c9b11-309">Das Team wird verschiedene Möglichkeiten in Betracht ziehen, um die Benutzerfreundlichkeit der Verbindung mit DataVerse für .NET-Entwickler zu verbessern.</span><span class="sxs-lookup"><span data-stu-id="c9b11-309">The team will consider different ways to improve the .NET developer experience connecting to DataVerse.</span></span>

## <a name="below-the-cut-line"></a><span data-ttu-id="c9b11-310">Nicht berücksichtigte Fehler</span><span class="sxs-lookup"><span data-stu-id="c9b11-310">Below-the-cut-line</span></span>

<span data-ttu-id="c9b11-311">Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `consider-for-current-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)</span><span class="sxs-lookup"><span data-stu-id="c9b11-311">Tracked by [issues labeled with `consider-for-current-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)</span></span>

<span data-ttu-id="c9b11-312">Dabei handelt es sich um Fehlerbehebungen und Verbesserungen, die derzeit **nicht** in Release 6.0 eingeplant wurden, jedoch je nach Feedback während des Releases und dem Fortschritt der oben beschriebenen Arbeiten unter Umständen in Zukunft angegangen werden.</span><span class="sxs-lookup"><span data-stu-id="c9b11-312">These are bug fixes and enhancements that are **not** currently scheduled for the 6.0 release, but we will look at based on feedback throughout the release together with progress made on the work above.</span></span> <span data-ttu-id="c9b11-313">Diese Issues können in EF Core 6.0 integriert und automatisch zu Kandidaten für das nächste Release werden.</span><span class="sxs-lookup"><span data-stu-id="c9b11-313">These issues may be pulled in to EF Core 6.0, and automatically become candidates for the next release.</span></span>

<span data-ttu-id="c9b11-314">Zusätzlich werden bei der Planung auch immer die [Probleme mit den meisten Stimmen](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="c9b11-314">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="c9b11-315">Bekannte Probleme erst in einem späteren Release zu beheben ist nicht schön, aber nötig, um die Auslastung der vorhandenen Ressourcen auf ein realistisches Maß zu beschränken.</span><span class="sxs-lookup"><span data-stu-id="c9b11-315">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span> <span data-ttu-id="c9b11-316">Vergewissern Sie sich, dass Sie für die Features abgestimmt haben (👍), die Sie benötigen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-316">Make sure you have voted (👍) for the features you need.</span></span>

## <a name="suggestions"></a><span data-ttu-id="c9b11-317">Vorschläge</span><span class="sxs-lookup"><span data-stu-id="c9b11-317">Suggestions</span></span>

<span data-ttu-id="c9b11-318">Ihr Feedback zur Planung ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="c9b11-318">Your feedback on planning is important.</span></span> <span data-ttu-id="c9b11-319">Sie können für ein Issue auf GitHub abstimmen (👍) und so angeben, dass es wichtig ist.</span><span class="sxs-lookup"><span data-stu-id="c9b11-319">The best way to indicate the importance of an issue is to vote (👍) for that issue on GitHub.</span></span> <span data-ttu-id="c9b11-320">Diese Daten werden dann in den [Planungsprozess](xref:core/what-is-new/release-planning) für das nächste Release aufgenommen.</span><span class="sxs-lookup"><span data-stu-id="c9b11-320">This data will then feed into the [planning process](xref:core/what-is-new/release-planning) for the next release.</span></span>
