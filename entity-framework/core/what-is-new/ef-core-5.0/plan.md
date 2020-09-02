---
title: Plan für Entity Framework Core 5.0
author: ajcvickers
ms.date: 08/22/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: a0d41d6df844c9ca2c8a2dc8ba50ca669e23dced
ms.sourcegitcommit: 6f7af3f138bf7c724cbdda261f97e5cf7035e8d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88847578"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="afbbb-102">Plan für Entity Framework Core 5.0</span><span class="sxs-lookup"><span data-stu-id="afbbb-102">Plan for Entity Framework Core 5.0</span></span>

<span data-ttu-id="afbbb-103">Wie im [Artikel zum Planungsprozess](xref:core/what-is-new/release_planning) beschrieben, haben wir aus dem Feedback der Stakeholder diesen vorläufigen Plan für EF Core 5.0 erstellt.</span><span class="sxs-lookup"><span data-stu-id="afbbb-103">As described in the [planning process](xref:core/what-is-new/release_planning), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="afbbb-104">Er befindet sich aktuell in der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="afbbb-104">This plan is still a work-in-progress.</span></span> <span data-ttu-id="afbbb-105">Sein Inhalt ist nicht verbindlich.</span><span class="sxs-lookup"><span data-stu-id="afbbb-105">Nothing here is a commitment.</span></span> <span data-ttu-id="afbbb-106">Er dient als Ausgangspunkt und wird durch neues Feedback weiterentwickelt.</span><span class="sxs-lookup"><span data-stu-id="afbbb-106">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="afbbb-107">Es kann also sein, dass die Version 5.0 Funktionen enthalten wird, die bisher nicht geplant waren.</span><span class="sxs-lookup"><span data-stu-id="afbbb-107">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="afbbb-108">Im Gegenzug kann es jedoch auch passieren, dass Funktionen gestrichen werden, die bisher für die Version 5.0 geplant waren.</span><span class="sxs-lookup"><span data-stu-id="afbbb-108">Some things currently planned for 5.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="afbbb-109">Allgemeine Informationen</span><span class="sxs-lookup"><span data-stu-id="afbbb-109">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="afbbb-110">Versionsnummer und Veröffentlichungsdatum</span><span class="sxs-lookup"><span data-stu-id="afbbb-110">Version number and release date</span></span>

<span data-ttu-id="afbbb-111">EF Core 5.0 soll [zusammen mit .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/) veröffentlicht werden.</span><span class="sxs-lookup"><span data-stu-id="afbbb-111">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="afbbb-112">Deshalb wurde auch „5.0“ als Versionsnummer gewählt.</span><span class="sxs-lookup"><span data-stu-id="afbbb-112">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="afbbb-113">Unterstützte Plattformen</span><span class="sxs-lookup"><span data-stu-id="afbbb-113">Supported platforms</span></span>

<span data-ttu-id="afbbb-114">EF Core 5.0 soll auf jeder .NET Standard 2.1-Plattform einschließlich .NET 5.0 ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="afbbb-114">EF Core 5.0 is planned to run on any .NET Standard 2.1 platform, including .NET 5.0.</span></span> <span data-ttu-id="afbbb-115">Dies ist Teil der allgemeineren .NET-übergreifenden [Plattformzusammenführung zu .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span><span class="sxs-lookup"><span data-stu-id="afbbb-115">This is part of the more general .NET wide [convergence of platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span>

<span data-ttu-id="afbbb-116">EF Core 5.0 kann nicht auf .NET Framework ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="afbbb-116">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="afbbb-117">Breaking Changes</span><span class="sxs-lookup"><span data-stu-id="afbbb-117">Breaking changes</span></span>

<span data-ttu-id="afbbb-118">EF Core 5.0 wird einige [Breaking Changes](xref:core/what-is-new/ef-core-5.0/breaking-changes) enthalten. Diese werden jedoch weniger gravierende Auswirkungen als bei EF Core 3.0 haben.</span><span class="sxs-lookup"><span data-stu-id="afbbb-118">EF Core 5.0 will contain some [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="afbbb-119">Unser Ziel ist es, die Mehrzahl der Anwendungen zu aktualisieren, ohne dass Breaking Changes auftreten.</span><span class="sxs-lookup"><span data-stu-id="afbbb-119">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="afbbb-120">Dennoch gehen wir davon, dass Datenbankanbieter auf einige Breaking Changes stoßen werden, besonders im Bereich TPT-Unterstützung.</span><span class="sxs-lookup"><span data-stu-id="afbbb-120">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="afbbb-121">Wir gehen jedoch davon aus, dass der Arbeitsaufwand für das Aktualisieren eines Providers auf 5.0 geringer ausfällt als bei 3.0.</span><span class="sxs-lookup"><span data-stu-id="afbbb-121">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="afbbb-122">Designs</span><span class="sxs-lookup"><span data-stu-id="afbbb-122">Themes</span></span>

<span data-ttu-id="afbbb-123">Wir haben einige wichtige Bereiche/Themen bestimmt, die die Grundlage für die großen Änderungen in EF Core 5.0 bilden.</span><span class="sxs-lookup"><span data-stu-id="afbbb-123">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a><span data-ttu-id="afbbb-124">Vollständig transparente m:n-Zuordnung nach Konvention</span><span class="sxs-lookup"><span data-stu-id="afbbb-124">Fully transparent many-to-many mapping by convention</span></span>

<span data-ttu-id="afbbb-125">Leitende Entwickler: @smitpatel, @AndriySvyryd und @lajones</span><span class="sxs-lookup"><span data-stu-id="afbbb-125">Lead developers: @smitpatel, @AndriySvyryd, and @lajones</span></span>

<span data-ttu-id="afbbb-126">Nachverfolgbar über [Issue 10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span><span class="sxs-lookup"><span data-stu-id="afbbb-126">Tracked by [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span></span>

<span data-ttu-id="afbbb-127">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="afbbb-127">T-shirt size: L</span></span>

<span data-ttu-id="afbbb-128">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="afbbb-128">Status: Done</span></span>

<span data-ttu-id="afbbb-129">„m:n“ ist das am [meisten geforderte Feature](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (ca. 506 Stimmen) im GitHub-Backlog.</span><span class="sxs-lookup"><span data-stu-id="afbbb-129">Many-to-many is the [most requested feature](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (~506 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="afbbb-130">Die Unterstützung für m:n-Beziehungen kann in drei Hauptbereiche aufgeteilt werden:</span><span class="sxs-lookup"><span data-stu-id="afbbb-130">Support for many-to-many relationships can be broken down into three major areas:</span></span>

* <span data-ttu-id="afbbb-131">Das Überspringen von Navigationseigenschaften wird im nächsten Artikel behandelt.</span><span class="sxs-lookup"><span data-stu-id="afbbb-131">Skip navigation properties--covered by the next theme.</span></span>
* <span data-ttu-id="afbbb-132">Entitätstypen für Eigenschaftenbehälter.</span><span class="sxs-lookup"><span data-stu-id="afbbb-132">Property-bag entity types.</span></span> <span data-ttu-id="afbbb-133">Dank dieser Entitätstypen kann für Entitätsinstanzen ein Standard-CLR-Typ wie `Dictionary` verwendet werden. So ist für einen Entitätstyp kein expliziter CLR-Typ mehr notwendig.</span><span class="sxs-lookup"><span data-stu-id="afbbb-133">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="afbbb-134">Nachverfolgbar über [Issue 9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)</span><span class="sxs-lookup"><span data-stu-id="afbbb-134">Tracked by [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).</span></span>
* <span data-ttu-id="afbbb-135">Sugar für die einfache Konfiguration von m:n-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="afbbb-135">Sugar for easy configuration of many-to-many relationships.</span></span>

<span data-ttu-id="afbbb-136">Nicht nur das Überspringen von Navigationseigenschaften wird unterstützt, sondern auch folgende weitere Bereiche von m:n werden in EF Core 5.0 integriert, damit Sie diese Option vollständig nutzen können.</span><span class="sxs-lookup"><span data-stu-id="afbbb-136">In addition to the skip navigation support, we are now pulling these other areas of many-to-many into EF Core 5.0 so as to provide a complete experience.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="afbbb-137">M:n-Navigationseigenschaften (auch bekannt als „Navigationseigenschaften überspringen“)</span><span class="sxs-lookup"><span data-stu-id="afbbb-137">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="afbbb-138">Leitende Entwickler: @smitpatel und @AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="afbbb-138">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="afbbb-139">Nachverfolgbar über [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span><span class="sxs-lookup"><span data-stu-id="afbbb-139">Tracked by [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span></span>

<span data-ttu-id="afbbb-140">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="afbbb-140">T-shirt size: L</span></span>

<span data-ttu-id="afbbb-141">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="afbbb-141">Status: Done</span></span>

<span data-ttu-id="afbbb-142">Wie im ersten Artikel beschrieben wurde, hat die m:n-Unterstützung mehrere Aspekte.</span><span class="sxs-lookup"><span data-stu-id="afbbb-142">As described in the first theme, many-to-many support has multiple aspects.</span></span>
<span data-ttu-id="afbbb-143">In diesem Artikel wird insbesondere die Verwendung des Features zum Überspringen von Navigationseigenschaften nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="afbbb-143">This theme specifically tracks use of skip navigations.</span></span>
<span data-ttu-id="afbbb-144">Unserer Meinung nach liegt der Wunsch nach m:n-Unterstützung hauptsächlich daran, dass bei Geschäftslogik wie Queries bisher keine Möglichkeit besteht, die „natürlichen“ Beziehungen zu verwenden, ohne auf die Jointabelle verweisen zu müssen.</span><span class="sxs-lookup"><span data-stu-id="afbbb-144">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span>
<span data-ttu-id="afbbb-145">Der Entitätstyp „Jointabelle“ existiert zwar noch, sollte in Geschäftslogik aber nicht mehr verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="afbbb-145">The join table entity type may still exist, but it should not get in the way of business logic.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="afbbb-146">Tabelle-pro-Typ-Vererbungszuordnung (TPT)</span><span class="sxs-lookup"><span data-stu-id="afbbb-146">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="afbbb-147">Leitende Entwickler: @AndriySvyryd und @smitpatel</span><span class="sxs-lookup"><span data-stu-id="afbbb-147">Lead developer: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="afbbb-148">Nachverfolgbar über [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span><span class="sxs-lookup"><span data-stu-id="afbbb-148">Tracked by [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span></span>

<span data-ttu-id="afbbb-149">T-Shirt-Größe: XL</span><span class="sxs-lookup"><span data-stu-id="afbbb-149">T-shirt size: XL</span></span>

<span data-ttu-id="afbbb-150">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="afbbb-150">Status: Done</span></span>

<span data-ttu-id="afbbb-151">Wir arbeiten an der TPT-Unterstützung, weil dieses Feature sehr stark nachgefragt wurde (ca. 289 Stimmen, 3. Platz insgesamt) und dafür nur wenige spezifische Änderungen erforderlich sind, die den allgemeinen Grundlagen des .NET 5-Plans entsprechen.</span><span class="sxs-lookup"><span data-stu-id="afbbb-151">We're doing TPT because it is both a highly requested feature (~289 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="afbbb-152">Datenbankanbieter werden deshalb höchstwahrscheinlich Breaking Changes bemerken. Diese erfordern jedoch viel weniger Arbeit als bei der Umstellung auf Version 3.0.</span><span class="sxs-lookup"><span data-stu-id="afbbb-152">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="afbbb-153">Gefilterte Include-Funktion</span><span class="sxs-lookup"><span data-stu-id="afbbb-153">Filtered Include</span></span>

<span data-ttu-id="afbbb-154">Leitender Entwickler: @maumar</span><span class="sxs-lookup"><span data-stu-id="afbbb-154">Lead developer: @maumar</span></span>

<span data-ttu-id="afbbb-155">Nachverfolgbar über [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span><span class="sxs-lookup"><span data-stu-id="afbbb-155">Tracked by [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span></span>

<span data-ttu-id="afbbb-156">T-Shirt-Größe: M</span><span class="sxs-lookup"><span data-stu-id="afbbb-156">T-shirt size: M</span></span>

<span data-ttu-id="afbbb-157">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="afbbb-157">Status: Done</span></span>

<span data-ttu-id="afbbb-158">Gefilterte Include-Abfragen wurden sehr oft gewünscht (ca. 376 Stimmen, 2. Platz insgesamt). Sie bedürfen nicht viel Arbeit und ermöglichen bzw. vereinfachen viele Szenarios, für die bisher Filter auf Modellebene oder komplexere Abfragen nötig sind.</span><span class="sxs-lookup"><span data-stu-id="afbbb-158">Filtered Include is a highly-requested feature (~376 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="split-include"></a><span data-ttu-id="afbbb-159">Aufgeteilte Include-Abfragen</span><span class="sxs-lookup"><span data-stu-id="afbbb-159">Split Include</span></span>

<span data-ttu-id="afbbb-160">Leitender Entwickler: @smitpatel</span><span class="sxs-lookup"><span data-stu-id="afbbb-160">Lead developer: @smitpatel</span></span>

<span data-ttu-id="afbbb-161">Nachverfolgbar über [Issue 20892](https://github.com/dotnet/efcore/issues/20892)</span><span class="sxs-lookup"><span data-stu-id="afbbb-161">Tracked by [#20892](https://github.com/dotnet/efcore/issues/20892)</span></span>

<span data-ttu-id="afbbb-162">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="afbbb-162">T-shirt size: L</span></span>

<span data-ttu-id="afbbb-163">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="afbbb-163">Status: Done</span></span>

<span data-ttu-id="afbbb-164">In EF Core 3.0 wurde das Standardverhalten für das Erstellen einer einzelnen SQL-Abfrage für eine bestimmte LINQ-Abfrage geändert.</span><span class="sxs-lookup"><span data-stu-id="afbbb-164">EF Core 3.0 changed the default behavior to create a single SQL query for a given LINQ query.</span></span>
<span data-ttu-id="afbbb-165">Dadurch kam es zu enormen Leistungsregressionen für Abfragen, die Include für mehrere Sammlungen verwenden.</span><span class="sxs-lookup"><span data-stu-id="afbbb-165">This caused large performance regressions for queries that use Include for multiple collections.</span></span>

<span data-ttu-id="afbbb-166">In EF Core 5.0 wird das neue Standardverhalten beibehalten.</span><span class="sxs-lookup"><span data-stu-id="afbbb-166">In EF Core 5.0, we are retaining the new default behavior.</span></span>
<span data-ttu-id="afbbb-167">EF Core 5.0 lässt jedoch jetzt die Generierung mehrerer Include-Abfragen für Sammlungen zu, bei denen eine einzelne Abfrage zu Leistungsproblemen führen würde.</span><span class="sxs-lookup"><span data-stu-id="afbbb-167">However, EF Core 5.0 will now allow generation of multiple queries for collection Includes where having a single query is causing bad performance.</span></span>

## <a name="required-one-to-one-dependents"></a><span data-ttu-id="afbbb-168">Erforderliche Elemente mit 1:1-Abhängigkeit</span><span class="sxs-lookup"><span data-stu-id="afbbb-168">Required one-to-one dependents</span></span>

<span data-ttu-id="afbbb-169">Leitende Entwickler: @AndriySvyryd und @smitpatel</span><span class="sxs-lookup"><span data-stu-id="afbbb-169">Lead developers: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="afbbb-170">Nachverfolgbar über [Issue 12100](https://github.com/dotnet/efcore/issues/12100)</span><span class="sxs-lookup"><span data-stu-id="afbbb-170">Tracked by [#12100](https://github.com/dotnet/efcore/issues/12100)</span></span>

<span data-ttu-id="afbbb-171">T-Shirt-Größe: M</span><span class="sxs-lookup"><span data-stu-id="afbbb-171">T-shirt size: M</span></span>

<span data-ttu-id="afbbb-172">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="afbbb-172">Status: Done</span></span>

<span data-ttu-id="afbbb-173">In EF Core 3.0 sind alle abhängigen Elemente optional, einschließlich nicht eigenständiger Typen (Person.Address kann beispielsweise NULL sein).</span><span class="sxs-lookup"><span data-stu-id="afbbb-173">In EF Core 3.0, all dependents, including owned types are optional (e.g. Person.Address can be null).</span></span> <span data-ttu-id="afbbb-174">In EF Core 5.0 können abhängige Elemente als „erforderlich“ konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="afbbb-174">In EF Core 5.0, dependents can be configured as required.</span></span>

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="afbbb-175">Rationalisieren von „ToTable“, „ToQuery“, „ToView“, „FromSQL“, usw.</span><span class="sxs-lookup"><span data-stu-id="afbbb-175">Rationalize ToTable, ToQuery, ToView, FromSql, etc</span></span>

<span data-ttu-id="afbbb-176">Leitende Entwickler: @AndriySvyryd und @smitpatel</span><span class="sxs-lookup"><span data-stu-id="afbbb-176">Lead developers: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="afbbb-177">Nachverfolgbar über [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span><span class="sxs-lookup"><span data-stu-id="afbbb-177">Tracked by [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span></span>

<span data-ttu-id="afbbb-178">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="afbbb-178">T-shirt size: L</span></span>

<span data-ttu-id="afbbb-179">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="afbbb-179">Status: Done</span></span>

<span data-ttu-id="afbbb-180">Bereits in früheren Versionen wurden Schritte zur Unterstützung von unformatierten SQL-Abfragen, schlüssellosen Typen sowie damit zusammenhängenden Dingen unternommen.</span><span class="sxs-lookup"><span data-stu-id="afbbb-180">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="afbbb-181">Beim Zusammenspiel des Ganzen bestehen jedoch noch Inkonsistenzen und Verbesserungsbedarf.</span><span class="sxs-lookup"><span data-stu-id="afbbb-181">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="afbbb-182">Ziel in Version 5.0 ist es, daran zu arbeiten und den Benutzern das Definieren, Migrieren und Verwenden verschiedener Entitätstypen sowie der dazugehörigen Abfragen und Datenbankartefakte zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="afbbb-182">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="afbbb-183">Dafür sind ggf. auch Aktualisierungen der kompilierten Abfrage-API erforderlich.</span><span class="sxs-lookup"><span data-stu-id="afbbb-183">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="afbbb-184">Dadurch kann es jedoch zu Breaking Changes auf Anwendungsebene kommen, da manche der aktuell verfügbaren Funktionen den Benutzern zu viel ermöglichen und dadurch Fehler provoziert werden.</span><span class="sxs-lookup"><span data-stu-id="afbbb-184">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="afbbb-185">Deshalb werden später wahrscheinliche einige dieser Funktionen blockiert sein. Sie erhalten dann stattdessen eine Anleitung mit einer alternativen Lösung.</span><span class="sxs-lookup"><span data-stu-id="afbbb-185">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="afbbb-186">Allgemeine Abfrageverbesserungen</span><span class="sxs-lookup"><span data-stu-id="afbbb-186">General query enhancements</span></span>

<span data-ttu-id="afbbb-187">Leitende Entwickler: @smitpatel und @maumar</span><span class="sxs-lookup"><span data-stu-id="afbbb-187">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="afbbb-188">Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `area-query`](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="afbbb-188">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="afbbb-189">T-Shirt-Größe: XL</span><span class="sxs-lookup"><span data-stu-id="afbbb-189">T-shirt size: XL</span></span>

<span data-ttu-id="afbbb-190">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="afbbb-190">Status: Done</span></span>

<span data-ttu-id="afbbb-191">Für EF Core 3.0 wurde der Code für die Abfrageübersetzung umfassend umgeschrieben.</span><span class="sxs-lookup"><span data-stu-id="afbbb-191">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="afbbb-192">Deshalb ist er allgemein viel robuster.</span><span class="sxs-lookup"><span data-stu-id="afbbb-192">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="afbbb-193">Bei Version 5.0 sind neben den für die TPT-Unterstützung und das Überspringen von Navigationseigenschaften erforderlichen Änderungen keine großen Anpassungen an der Abfrageübersetzung vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="afbbb-193">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="afbbb-194">Es bestehen jedoch noch einige technische Altlasten aus dem Wechsel zu Version 3.0, für die noch viel Arbeit erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="afbbb-194">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="afbbb-195">Zusätzlich werden viele Fehler behoben und kleine Verbesserungen eingeführt, wodurch die Benutzer Abfragen noch besser ausführen können.</span><span class="sxs-lookup"><span data-stu-id="afbbb-195">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="afbbb-196">Migration und Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="afbbb-196">Migrations and deployment experience</span></span>

<span data-ttu-id="afbbb-197">Leitender Entwickler: @bricelam</span><span class="sxs-lookup"><span data-stu-id="afbbb-197">Lead developers: @bricelam</span></span>

<span data-ttu-id="afbbb-198">Nachverfolgbar über [#19587](https://github.com/dotnet/efcore/issues/19587)</span><span class="sxs-lookup"><span data-stu-id="afbbb-198">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="afbbb-199">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="afbbb-199">T-shirt size: L</span></span>

<span data-ttu-id="afbbb-200">Status: Zugeordnet/Erledigt</span><span class="sxs-lookup"><span data-stu-id="afbbb-200">Status: Scoped/Done</span></span>

<span data-ttu-id="afbbb-201">Eingrenzung: Das [Migrationsbundlefeature](https://github.com/dotnet/efcore/issues/19693) wurde bis auf einen Zeitpunkt nach dem EF Core 5.0-Release verschoben.</span><span class="sxs-lookup"><span data-stu-id="afbbb-201">Scoping: The [migrations bundles feature](https://github.com/dotnet/efcore/issues/19693) has been deferred until after the EF Core 5.0 release.</span></span> <span data-ttu-id="afbbb-202">Einige andere [Verbesserungen für Migrationen](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460) sind jedoch im EF Core 5.0-Release enthalten.</span><span class="sxs-lookup"><span data-stu-id="afbbb-202">However, several other [targeted improvements related to migrations](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460) will be included in EF Core 5.0</span></span>

<span data-ttu-id="afbbb-203">Derzeit migrieren viele Entwickler ihre Datenbanken beim Start der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="afbbb-203">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="afbbb-204">Das ist einfach, empfiehlt sich jedoch aus folgenden Gründen nicht:</span><span class="sxs-lookup"><span data-stu-id="afbbb-204">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="afbbb-205">Mehrere Threads/Prozesse/Server versuchen möglicherweise gleichzeitig, die Datenbank zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="afbbb-205">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="afbbb-206">Anwendungen versuchen möglicherweise, den Zustand „inkonsistent“ zu erreichen, während dies geschieht.</span><span class="sxs-lookup"><span data-stu-id="afbbb-206">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="afbbb-207">Normalerweise sollte die Berechtigung, das Schema zu modifizieren, nicht bei Ausführung der Anwendung erteilt werden.</span><span class="sxs-lookup"><span data-stu-id="afbbb-207">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="afbbb-208">Wenn etwas schiefgeht, ist es schwer, wieder einen fehlerfreien Zustand zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="afbbb-208">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="afbbb-209">Wir möchten dafür sorgen, dass es einfach ist, die Datenbank zum Zeitpunkt der Bereitstellung zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="afbbb-209">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="afbbb-210">Dafür haben wir uns folgende Ziele gesetzt:</span><span class="sxs-lookup"><span data-stu-id="afbbb-210">This should:</span></span>

* <span data-ttu-id="afbbb-211">Gemeinsame Lösung für Linux, macOS und Windows</span><span class="sxs-lookup"><span data-stu-id="afbbb-211">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="afbbb-212">Unkompliziert über die Befehlszeile ausführbar</span><span class="sxs-lookup"><span data-stu-id="afbbb-212">Be a good experience on the command line</span></span>
* <span data-ttu-id="afbbb-213">Unterstützung von Szenarios mit Containern</span><span class="sxs-lookup"><span data-stu-id="afbbb-213">Support scenarios with containers</span></span>
* <span data-ttu-id="afbbb-214">Kombinierbar mit häufig verwendeten Bereitstellungstools-/-flows</span><span class="sxs-lookup"><span data-stu-id="afbbb-214">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="afbbb-215">Integration in Visual Studio (mindestens)</span><span class="sxs-lookup"><span data-stu-id="afbbb-215">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="afbbb-216">Daraus werden sich wahrscheinlich viele kleine Verbesserungen bei EF Core ergeben, z. B. bessere Migrationen bei SQLite. Kombiniert mit Leitfäden und einer langfristigen Zusammenarbeit mit anderen Teams entstehen ganzheitliche Verbesserungen, nicht nur bei EF.</span><span class="sxs-lookup"><span data-stu-id="afbbb-216">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="afbbb-217">EF Core-Plattformen</span><span class="sxs-lookup"><span data-stu-id="afbbb-217">EF Core platforms experience</span></span>

<span data-ttu-id="afbbb-218">Leitende Entwickler: @roji und @bricelam</span><span class="sxs-lookup"><span data-stu-id="afbbb-218">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="afbbb-219">Nachverfolgbar über [#19588](https://github.com/dotnet/efcore/issues/19588)</span><span class="sxs-lookup"><span data-stu-id="afbbb-219">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="afbbb-220">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="afbbb-220">T-shirt size: L</span></span>

<span data-ttu-id="afbbb-221">Status: Zugeordnet/Erledigt</span><span class="sxs-lookup"><span data-stu-id="afbbb-221">Status: Scope/Done</span></span>

<span data-ttu-id="afbbb-222">Eingrenzung: Ein Plattformleitfaden und Beispiele werden für Blazor, Xamarin, WinForms und WPF veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="afbbb-222">Scoping: Platform guidance and samples are published for Blazor, Xamarin, WinForms, and WPF.</span></span> <span data-ttu-id="afbbb-223">Xamarin- sowie weitere AOT-/Linker-Verbesserungen sind für EF Core 6.0 geplant.</span><span class="sxs-lookup"><span data-stu-id="afbbb-223">Xamarin and other AOT/linker work is now planned for EF Core 6.0.</span></span>

<span data-ttu-id="afbbb-224">Wir haben gute Leitfäden für die Verwendung von EF Core in traditionellen, MVC ähnelnden Webanwendungen.</span><span class="sxs-lookup"><span data-stu-id="afbbb-224">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="afbbb-225">Die Leitfäden für andere Plattformen und Anwendungsmodelle sind entweder veraltet oder fehlen ganz.</span><span class="sxs-lookup"><span data-stu-id="afbbb-225">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="afbbb-226">Für EF Core 5.0 werden wir untersuchen, wie gut sich EF-Core mit den folgenden Dingen kombinieren lässt. Die Ergebnisse werden dokumentiert, und wir versuchen, weitere Verbesserungen zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="afbbb-226">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="afbbb-227">Blazor</span><span class="sxs-lookup"><span data-stu-id="afbbb-227">Blazor</span></span>
* <span data-ttu-id="afbbb-228">Xamarin, einschließlich der Verwendung von AOT/Linker-Story</span><span class="sxs-lookup"><span data-stu-id="afbbb-228">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="afbbb-229">WinForms/WPF/WinUI und möglicherweise andere UI</span><span class="sxs-lookup"><span data-stu-id="afbbb-229">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="afbbb-230">frameworks</span><span class="sxs-lookup"><span data-stu-id="afbbb-230">frameworks</span></span>

<span data-ttu-id="afbbb-231">Daraus werden sich wahrscheinlich viele kleine Verbesserungen bei EF Core ergeben. Kombiniert mit Leitfäden und einer langfristigen Zusammenarbeit mit anderen Teams entstehen ganzheitliche Verbesserungen, nicht nur bei EF.</span><span class="sxs-lookup"><span data-stu-id="afbbb-231">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="afbbb-232">Bestimmte Bereiche, die wir uns ansehen möchten, sind:</span><span class="sxs-lookup"><span data-stu-id="afbbb-232">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="afbbb-233">Bereitstellung, einschließlich der Verwendung von EF-Tools, z. B. für Migrationen</span><span class="sxs-lookup"><span data-stu-id="afbbb-233">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="afbbb-234">Anwendungsmodelle, einschließlich Xamarin, Blazer und gegebenenfalls weitere</span><span class="sxs-lookup"><span data-stu-id="afbbb-234">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="afbbb-235">SQLite-Funktionen, einschließlich räumlicher Daten und Neuerstellung von Tabellen</span><span class="sxs-lookup"><span data-stu-id="afbbb-235">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="afbbb-236">AOT und Verknüpfungen</span><span class="sxs-lookup"><span data-stu-id="afbbb-236">AOT and linking experiences</span></span>
* <span data-ttu-id="afbbb-237">Integration von Diagnosefunktionen, einschließlich Leistungsindikatoren</span><span class="sxs-lookup"><span data-stu-id="afbbb-237">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="afbbb-238">Leistung</span><span class="sxs-lookup"><span data-stu-id="afbbb-238">Performance</span></span>

<span data-ttu-id="afbbb-239">Leitender Entwickler: @roji</span><span class="sxs-lookup"><span data-stu-id="afbbb-239">Lead developer: @roji</span></span>

<span data-ttu-id="afbbb-240">Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `area-perf`](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="afbbb-240">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="afbbb-241">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="afbbb-241">T-shirt size: L</span></span>

<span data-ttu-id="afbbb-242">Status: Zugeordnet/Erledigt</span><span class="sxs-lookup"><span data-stu-id="afbbb-242">Status: Scoped/Done</span></span>

<span data-ttu-id="afbbb-243">Eingrenzung: Wichtige Leistungsverbesserungen beim Npgsql-Anbieter wurden abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="afbbb-243">Scoping: Major performance improvements in the Npgsql provider are complete.</span></span> <span data-ttu-id="afbbb-244">Weitere Leistungsverbesserungen sind für EF Core 6.0 geplant.</span><span class="sxs-lookup"><span data-stu-id="afbbb-244">Other performance work is now planned for EF Core 6.0.</span></span>

<span data-ttu-id="afbbb-245">In EF Core möchten wir die vorhandenen Leistungsbenchmarks verbessern und gezielt Leistungsverbesserungen an der Runtime vornehmen.</span><span class="sxs-lookup"><span data-stu-id="afbbb-245">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="afbbb-246">Außerdem ist geplant, die Arbeit an der neuen ADO.NET-API für Batchverarbeitung abzuschließen, die während des Veröffentlichungszyklus der Version 3.0 als Prototyp vorgestellt worden war.</span><span class="sxs-lookup"><span data-stu-id="afbbb-246">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="afbbb-247">Zusätzlich möchten wir auf der ADO.NET-Ebene zusätzliche Leistungsverbesserungen bei Npgsql vornehmen.</span><span class="sxs-lookup"><span data-stu-id="afbbb-247">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="afbbb-248">Des Weiteren möchten wir bei Bedarf ADO.NET/EF Core-Leistungsindikatoren und andere Diagnosefunktionen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="afbbb-248">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="afbbb-249">Dokumentation zu Architektur und Mitwirkenden</span><span class="sxs-lookup"><span data-stu-id="afbbb-249">Architectural/contributor documentation</span></span>

<span data-ttu-id="afbbb-250">Für die Dokumentation verantwortliche Person: @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="afbbb-250">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="afbbb-251">Nachverfolgbar über [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span><span class="sxs-lookup"><span data-stu-id="afbbb-251">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="afbbb-252">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="afbbb-252">T-shirt size: L</span></span>

<span data-ttu-id="afbbb-253">Status: Ausschneiden</span><span class="sxs-lookup"><span data-stu-id="afbbb-253">Status: Cut</span></span>

<span data-ttu-id="afbbb-254">Ziel ist es, verständlicher zu machen, was in EF Core passiert.</span><span class="sxs-lookup"><span data-stu-id="afbbb-254">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="afbbb-255">Dies kann für alle Personen nützlich sein, die EF Core verwenden. Das Hauptziel ist jedoch, externen Personen Folgendes zu erleichtern:</span><span class="sxs-lookup"><span data-stu-id="afbbb-255">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="afbbb-256">Mitarbeit am EFF Core-Code</span><span class="sxs-lookup"><span data-stu-id="afbbb-256">Contribute to the EF Core code</span></span>
* <span data-ttu-id="afbbb-257">Erstellen von Datenbankanbietern</span><span class="sxs-lookup"><span data-stu-id="afbbb-257">Create database providers</span></span>
* <span data-ttu-id="afbbb-258">Erstellen anderer Erweiterungen</span><span class="sxs-lookup"><span data-stu-id="afbbb-258">Build other extensions</span></span>

<span data-ttu-id="afbbb-259">Update: Leider war dieser Plan zu ambitioniert.</span><span class="sxs-lookup"><span data-stu-id="afbbb-259">Update: Unfortunately, this plan was too ambitious.</span></span>
<span data-ttu-id="afbbb-260">Wir halten das Feature weiterhin für wichtig, dennoch kann es nicht in EF Core 5.0 aufgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="afbbb-260">We still believe this is important, but unfortunately it won't land with EF Core 5.0.</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="afbbb-261">Dokumentation zu Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="afbbb-261">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="afbbb-262">Für die Dokumentation verantwortliche Person: @bricelam</span><span class="sxs-lookup"><span data-stu-id="afbbb-262">Lead documenter: @bricelam</span></span>

<span data-ttu-id="afbbb-263">Nachverfolgbar über [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span><span class="sxs-lookup"><span data-stu-id="afbbb-263">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="afbbb-264">T-Shirt-Größe: M</span><span class="sxs-lookup"><span data-stu-id="afbbb-264">T-shirt size: M</span></span>

<span data-ttu-id="afbbb-265">Status: Abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="afbbb-265">Status: Completed.</span></span> <span data-ttu-id="afbbb-266">Die neue Dokumentation finden Sie [hier](/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span><span class="sxs-lookup"><span data-stu-id="afbbb-266">The new documentation is [live on the Microsoft docs site](/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="afbbb-267">Das EF-Team ist auch Eigentümer des Microsoft.Data.Sqlite ADO.NET-Anbieters.</span><span class="sxs-lookup"><span data-stu-id="afbbb-267">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="afbbb-268">Wir planen, als Teil der Veröffentlichungen für EF Core 5.0, eine vollständige Dokumentation zu diesem Anbieter bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="afbbb-268">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="afbbb-269">Allgemeine Dokumentation</span><span class="sxs-lookup"><span data-stu-id="afbbb-269">General documentation</span></span>

<span data-ttu-id="afbbb-270">Für die Dokumentation verantwortliche Person: @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="afbbb-270">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="afbbb-271">Nachverfolgbar über [Docs-Repository im 5.0-Meilenstein](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="afbbb-271">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="afbbb-272">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="afbbb-272">T-shirt size: L</span></span>

<span data-ttu-id="afbbb-273">Status: In Bearbeitung</span><span class="sxs-lookup"><span data-stu-id="afbbb-273">Status: In-progress</span></span>

<span data-ttu-id="afbbb-274">Die Aktualisierung der bestehenden Dokumentation für die Releases 3.0 und 3.1 läuft bereits.</span><span class="sxs-lookup"><span data-stu-id="afbbb-274">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="afbbb-275">Zusätzlich arbeiten wir an Folgendem:</span><span class="sxs-lookup"><span data-stu-id="afbbb-275">We are also working on:</span></span>

* <span data-ttu-id="afbbb-276">Überarbeitung der Einsteigerdokumentation, um sie ansprechender und leichter verständlich zu machen</span><span class="sxs-lookup"><span data-stu-id="afbbb-276">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
* <span data-ttu-id="afbbb-277">Neuanordnung von Dokumenten, um sie leichter auffindbar zu machen und um Querverweise hinzufügen zu können</span><span class="sxs-lookup"><span data-stu-id="afbbb-277">Reorganization of docs to make things easier to find and to add cross-references</span></span>
* <span data-ttu-id="afbbb-278">Hinzufügen von ausführlicheren Details und Erläuterungen zu vorhandenen Dokumenten</span><span class="sxs-lookup"><span data-stu-id="afbbb-278">Adding more details and clarifications to existing docs</span></span>
* <span data-ttu-id="afbbb-279">Aktualisieren bestehender Beispiele und Hinzufügen zusätzlicher Beispiele</span><span class="sxs-lookup"><span data-stu-id="afbbb-279">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="afbbb-280">Beheben von Fehlern</span><span class="sxs-lookup"><span data-stu-id="afbbb-280">Fixing bugs</span></span>

<span data-ttu-id="afbbb-281">Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `type-bug`](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span><span class="sxs-lookup"><span data-stu-id="afbbb-281">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="afbbb-282">Entwickler: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="afbbb-282">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="afbbb-283">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="afbbb-283">T-shirt size: L</span></span>

<span data-ttu-id="afbbb-284">Status: In Bearbeitung</span><span class="sxs-lookup"><span data-stu-id="afbbb-284">Status: In-progress</span></span>

<span data-ttu-id="afbbb-285">Zum Zeitpunkt der Entstehung dieses Artikels haben wir 135 Fehler kategorisiert, die in Version 5.0 behoben werden sollen. Davon sind 62 bereits behoben. Hier besteht jedoch eine starke Überschneidung mit den weiter oben beschriebenen _allgemeinen Abfrageverbesserungen_.</span><span class="sxs-lookup"><span data-stu-id="afbbb-285">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="afbbb-286">Im Laufe des Releases 3.0 wurden pro Monat etwa 23 Probleme in Meilensteine aufgenommen.</span><span class="sxs-lookup"><span data-stu-id="afbbb-286">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="afbbb-287">Davon müssen jedoch nicht alle in Version 5.0 behoben werden.</span><span class="sxs-lookup"><span data-stu-id="afbbb-287">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="afbbb-288">Grob geschätzt sollen im Zeitraum von Version 5.0 zusätzlich 150 Probleme behoben werden.</span><span class="sxs-lookup"><span data-stu-id="afbbb-288">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="afbbb-289">Kleinere Verbesserungen</span><span class="sxs-lookup"><span data-stu-id="afbbb-289">Small enhancements</span></span>

<span data-ttu-id="afbbb-290">Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `type-enhancement`](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span><span class="sxs-lookup"><span data-stu-id="afbbb-290">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="afbbb-291">Entwickler: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="afbbb-291">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="afbbb-292">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="afbbb-292">T-shirt size: L</span></span>

<span data-ttu-id="afbbb-293">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="afbbb-293">Status: Done</span></span>

<span data-ttu-id="afbbb-294">Zusätzlich zu den oben beschriebenen größeren Funktionen sind in Version 5.0 auch viele kleinere Verbesserungen geplant, mit denen Probleme behoben werden, die als unkritisch gelten.</span><span class="sxs-lookup"><span data-stu-id="afbbb-294">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="afbbb-295">Viele dieser Verbesserungen sind auch durch die oben beschriebenen, allgemeineren Themen abgedeckt.</span><span class="sxs-lookup"><span data-stu-id="afbbb-295">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="afbbb-296">Verschoben auf nächste Version</span><span class="sxs-lookup"><span data-stu-id="afbbb-296">Below-the-line</span></span>

<span data-ttu-id="afbbb-297">Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span><span class="sxs-lookup"><span data-stu-id="afbbb-297">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="afbbb-298">Dabei handelt es sich um Fehlerbehebungen und Verbesserungen, die aktuell **nicht** für das Release 5.0 vorgesehen sind, aber je nach Fortschritt bei den oben beschriebenen Zielen zusätzlich angegangen werden.</span><span class="sxs-lookup"><span data-stu-id="afbbb-298">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="afbbb-299">Zusätzlich werden bei der Planung auch immer die [Probleme mit den meisten Stimmen](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="afbbb-299">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="afbbb-300">Bekannte Probleme erst in einem späteren Release zu beheben ist nicht schön, aber nötig, um die Auslastung der vorhandenen Ressourcen auf ein realistisches Maß zu beschränken.</span><span class="sxs-lookup"><span data-stu-id="afbbb-300">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="suggestions"></a><span data-ttu-id="afbbb-301">Vorschläge</span><span class="sxs-lookup"><span data-stu-id="afbbb-301">Suggestions</span></span>

<span data-ttu-id="afbbb-302">Ihr Feedback zur Planung ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="afbbb-302">Your feedback on planning is important.</span></span> <span data-ttu-id="afbbb-303">Sie können für ein Problem auf GitHub abstimmen (Daumen hoch) und so angeben, dass dieses Problem wichtig ist.</span><span class="sxs-lookup"><span data-stu-id="afbbb-303">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="afbbb-304">Diese Daten werden dann in den [Planungsprozess](xref:core/what-is-new/release_planning) für das nächste Release aufgenommen.</span><span class="sxs-lookup"><span data-stu-id="afbbb-304">This data will then feed into the [planning process](xref:core/what-is-new/release_planning) for the next release.</span></span>
