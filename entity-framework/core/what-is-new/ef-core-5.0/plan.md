---
title: Plan für Entity Framework Core 5.0
description: Die für Entity Framework Core 5.0 geplanten Features
author: ajcvickers
ms.date: 08/22/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: ba56a5f3c79dacc25b3402be114c57067da49433
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129043"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="e3558-103">Plan für Entity Framework Core 5.0</span><span class="sxs-lookup"><span data-stu-id="e3558-103">Plan for Entity Framework Core 5.0</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e3558-104">EF Core 5.0 [wurde veröffentlicht](xref:core/what-is-new/index).</span><span class="sxs-lookup"><span data-stu-id="e3558-104">EF Core 5.0 [has now been released](xref:core/what-is-new/index).</span></span> <span data-ttu-id="e3558-105">Auf dieser Seite finden Sie Informationen zum Verlauf der Planung.</span><span class="sxs-lookup"><span data-stu-id="e3558-105">This page remains as a historical record of the plan.</span></span>

<span data-ttu-id="e3558-106">Wie im [Artikel zum Planungsprozess](xref:core/what-is-new/release-planning) beschrieben, haben wir aus dem Feedback der Stakeholder diesen vorläufigen Plan für EF Core 5.0 erstellt.</span><span class="sxs-lookup"><span data-stu-id="e3558-106">As described in the [planning process](xref:core/what-is-new/release-planning), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e3558-107">Er befindet sich aktuell in der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="e3558-107">This plan is still a work-in-progress.</span></span> <span data-ttu-id="e3558-108">Sein Inhalt ist nicht verbindlich.</span><span class="sxs-lookup"><span data-stu-id="e3558-108">Nothing here is a commitment.</span></span> <span data-ttu-id="e3558-109">Er dient als Ausgangspunkt und wird durch neues Feedback weiterentwickelt.</span><span class="sxs-lookup"><span data-stu-id="e3558-109">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="e3558-110">Es kann also sein, dass die Version 5.0 Funktionen enthalten wird, die bisher nicht geplant waren.</span><span class="sxs-lookup"><span data-stu-id="e3558-110">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="e3558-111">Im Gegenzug kann es jedoch auch passieren, dass Funktionen gestrichen werden, die bisher für die Version 5.0 geplant waren.</span><span class="sxs-lookup"><span data-stu-id="e3558-111">Some things currently planned for 5.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="e3558-112">Allgemeine Informationen</span><span class="sxs-lookup"><span data-stu-id="e3558-112">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="e3558-113">Versionsnummer und Veröffentlichungsdatum</span><span class="sxs-lookup"><span data-stu-id="e3558-113">Version number and release date</span></span>

<span data-ttu-id="e3558-114">EF Core 5.0 soll [zusammen mit .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/) veröffentlicht werden.</span><span class="sxs-lookup"><span data-stu-id="e3558-114">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="e3558-115">Deshalb wurde auch „5.0“ als Versionsnummer gewählt.</span><span class="sxs-lookup"><span data-stu-id="e3558-115">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="e3558-116">Unterstützte Plattformen</span><span class="sxs-lookup"><span data-stu-id="e3558-116">Supported platforms</span></span>

<span data-ttu-id="e3558-117">EF Core 5.0 soll auf jeder .NET Standard 2.1-Plattform einschließlich .NET 5.0 ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="e3558-117">EF Core 5.0 is planned to run on any .NET Standard 2.1 platform, including .NET 5.0.</span></span> <span data-ttu-id="e3558-118">Dies ist Teil der allgemeineren .NET-übergreifenden [Plattformzusammenführung zu .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span><span class="sxs-lookup"><span data-stu-id="e3558-118">This is part of the more general .NET wide [convergence of platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span>

<span data-ttu-id="e3558-119">EF Core 5.0 kann nicht auf .NET Framework ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e3558-119">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="e3558-120">Breaking Changes</span><span class="sxs-lookup"><span data-stu-id="e3558-120">Breaking changes</span></span>

<span data-ttu-id="e3558-121">EF Core 5.0 wird einige [Breaking Changes](xref:core/what-is-new/ef-core-5.0/breaking-changes) enthalten. Diese werden jedoch weniger gravierende Auswirkungen als bei EF Core 3.0 haben.</span><span class="sxs-lookup"><span data-stu-id="e3558-121">EF Core 5.0 will contain some [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="e3558-122">Unser Ziel ist es, die Mehrzahl der Anwendungen zu aktualisieren, ohne dass Breaking Changes auftreten.</span><span class="sxs-lookup"><span data-stu-id="e3558-122">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="e3558-123">Dennoch gehen wir davon, dass Datenbankanbieter auf einige Breaking Changes stoßen werden, besonders im Bereich TPT-Unterstützung.</span><span class="sxs-lookup"><span data-stu-id="e3558-123">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="e3558-124">Wir gehen jedoch davon aus, dass der Arbeitsaufwand für das Aktualisieren eines Providers auf 5.0 geringer ausfällt als bei 3.0.</span><span class="sxs-lookup"><span data-stu-id="e3558-124">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="e3558-125">Designs</span><span class="sxs-lookup"><span data-stu-id="e3558-125">Themes</span></span>

<span data-ttu-id="e3558-126">Wir haben einige wichtige Bereiche/Themen bestimmt, die die Grundlage für die großen Änderungen in EF Core 5.0 bilden.</span><span class="sxs-lookup"><span data-stu-id="e3558-126">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a><span data-ttu-id="e3558-127">Vollständig transparente m:n-Zuordnung nach Konvention</span><span class="sxs-lookup"><span data-stu-id="e3558-127">Fully transparent many-to-many mapping by convention</span></span>

<span data-ttu-id="e3558-128">Leitende Entwickler: @smitpatel, @AndriySvyryd und @lajones</span><span class="sxs-lookup"><span data-stu-id="e3558-128">Lead developers: @smitpatel, @AndriySvyryd, and @lajones</span></span>

<span data-ttu-id="e3558-129">Nachverfolgbar über [Issue 10508](https://github.com/dotnet/efcore/issues/10508)</span><span class="sxs-lookup"><span data-stu-id="e3558-129">Tracked by [#10508](https://github.com/dotnet/efcore/issues/10508)</span></span>

<span data-ttu-id="e3558-130">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="e3558-130">T-shirt size: L</span></span>

<span data-ttu-id="e3558-131">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="e3558-131">Status: Done</span></span>

<span data-ttu-id="e3558-132">„m:n“ ist das am [meisten geforderte Feature](https://github.com/dotnet/efcore/issues/1368) (ca. 506 Stimmen) im GitHub-Backlog.</span><span class="sxs-lookup"><span data-stu-id="e3558-132">Many-to-many is the [most requested feature](https://github.com/dotnet/efcore/issues/1368) (~506 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="e3558-133">Die Unterstützung für m:n-Beziehungen kann in drei Hauptbereiche aufgeteilt werden:</span><span class="sxs-lookup"><span data-stu-id="e3558-133">Support for many-to-many relationships can be broken down into three major areas:</span></span>

* <span data-ttu-id="e3558-134">Das Überspringen von Navigationseigenschaften wird im nächsten Artikel behandelt.</span><span class="sxs-lookup"><span data-stu-id="e3558-134">Skip navigation properties--covered by the next theme.</span></span>
* <span data-ttu-id="e3558-135">Entitätstypen für Eigenschaftenbehälter.</span><span class="sxs-lookup"><span data-stu-id="e3558-135">Property-bag entity types.</span></span> <span data-ttu-id="e3558-136">Dank dieser Entitätstypen kann für Entitätsinstanzen ein Standard-CLR-Typ wie `Dictionary` verwendet werden. So ist für einen Entitätstyp kein expliziter CLR-Typ mehr notwendig.</span><span class="sxs-lookup"><span data-stu-id="e3558-136">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="e3558-137">Nachverfolgbar über [Issue 9914](https://github.com/dotnet/efcore/issues/9914)</span><span class="sxs-lookup"><span data-stu-id="e3558-137">Tracked by [#9914](https://github.com/dotnet/efcore/issues/9914).</span></span>
* <span data-ttu-id="e3558-138">Sugar für die einfache Konfiguration von m:n-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="e3558-138">Sugar for easy configuration of many-to-many relationships.</span></span>

<span data-ttu-id="e3558-139">Nicht nur das Überspringen von Navigationseigenschaften wird unterstützt, sondern auch folgende weitere Bereiche von m:n werden in EF Core 5.0 integriert, damit Sie diese Option vollständig nutzen können.</span><span class="sxs-lookup"><span data-stu-id="e3558-139">In addition to the skip navigation support, we are now pulling these other areas of many-to-many into EF Core 5.0 so as to provide a complete experience.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="e3558-140">M:n-Navigationseigenschaften (auch bekannt als „Navigationseigenschaften überspringen“)</span><span class="sxs-lookup"><span data-stu-id="e3558-140">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="e3558-141">Leitende Entwickler: @smitpatel und @AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="e3558-141">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="e3558-142">Nachverfolgbar über [#19003](https://github.com/dotnet/efcore/issues/19003)</span><span class="sxs-lookup"><span data-stu-id="e3558-142">Tracked by [#19003](https://github.com/dotnet/efcore/issues/19003)</span></span>

<span data-ttu-id="e3558-143">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="e3558-143">T-shirt size: L</span></span>

<span data-ttu-id="e3558-144">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="e3558-144">Status: Done</span></span>

<span data-ttu-id="e3558-145">Wie im ersten Artikel beschrieben wurde, hat die m:n-Unterstützung mehrere Aspekte.</span><span class="sxs-lookup"><span data-stu-id="e3558-145">As described in the first theme, many-to-many support has multiple aspects.</span></span>
<span data-ttu-id="e3558-146">In diesem Artikel wird insbesondere die Verwendung des Features zum Überspringen von Navigationseigenschaften nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="e3558-146">This theme specifically tracks use of skip navigations.</span></span>
<span data-ttu-id="e3558-147">Unserer Meinung nach liegt der Wunsch nach m:n-Unterstützung hauptsächlich daran, dass bei Geschäftslogik wie Queries bisher keine Möglichkeit besteht, die „natürlichen“ Beziehungen zu verwenden, ohne auf die Jointabelle verweisen zu müssen.</span><span class="sxs-lookup"><span data-stu-id="e3558-147">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span>
<span data-ttu-id="e3558-148">Der Entitätstyp „Jointabelle“ existiert zwar noch, sollte in Geschäftslogik aber nicht mehr verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e3558-148">The join table entity type may still exist, but it should not get in the way of business logic.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="e3558-149">Tabelle-pro-Typ-Vererbungszuordnung (TPT)</span><span class="sxs-lookup"><span data-stu-id="e3558-149">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="e3558-150">Leitende Entwickler: @AndriySvyryd und @smitpatel</span><span class="sxs-lookup"><span data-stu-id="e3558-150">Lead developer: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="e3558-151">Nachverfolgbar über [#2266](https://github.com/dotnet/efcore/issues/2266)</span><span class="sxs-lookup"><span data-stu-id="e3558-151">Tracked by [#2266](https://github.com/dotnet/efcore/issues/2266)</span></span>

<span data-ttu-id="e3558-152">T-Shirt-Größe: XL</span><span class="sxs-lookup"><span data-stu-id="e3558-152">T-shirt size: XL</span></span>

<span data-ttu-id="e3558-153">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="e3558-153">Status: Done</span></span>

<span data-ttu-id="e3558-154">Wir arbeiten an der TPT-Unterstützung, weil dieses Feature sehr stark nachgefragt wurde (ca. 289 Stimmen, 3. Platz insgesamt) und dafür nur wenige spezifische Änderungen erforderlich sind, die den allgemeinen Grundlagen des .NET 5-Plans entsprechen.</span><span class="sxs-lookup"><span data-stu-id="e3558-154">We're doing TPT because it is both a highly requested feature (~289 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="e3558-155">Datenbankanbieter werden deshalb höchstwahrscheinlich Breaking Changes bemerken. Diese erfordern jedoch viel weniger Arbeit als bei der Umstellung auf Version 3.0.</span><span class="sxs-lookup"><span data-stu-id="e3558-155">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="e3558-156">Gefilterte Include-Funktion</span><span class="sxs-lookup"><span data-stu-id="e3558-156">Filtered Include</span></span>

<span data-ttu-id="e3558-157">Leitender Entwickler: @maumar</span><span class="sxs-lookup"><span data-stu-id="e3558-157">Lead developer: @maumar</span></span>

<span data-ttu-id="e3558-158">Nachverfolgbar über [#1833](https://github.com/dotnet/efcore/issues/1833)</span><span class="sxs-lookup"><span data-stu-id="e3558-158">Tracked by [#1833](https://github.com/dotnet/efcore/issues/1833)</span></span>

<span data-ttu-id="e3558-159">T-Shirt-Größe: M</span><span class="sxs-lookup"><span data-stu-id="e3558-159">T-shirt size: M</span></span>

<span data-ttu-id="e3558-160">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="e3558-160">Status: Done</span></span>

<span data-ttu-id="e3558-161">Gefilterte Include-Abfragen wurden sehr oft gewünscht (ca. 376 Stimmen, 2. Platz insgesamt). Sie bedürfen nicht viel Arbeit und ermöglichen bzw. vereinfachen viele Szenarios, für die bisher Filter auf Modellebene oder komplexere Abfragen nötig sind.</span><span class="sxs-lookup"><span data-stu-id="e3558-161">Filtered Include is a highly-requested feature (~376 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="split-include"></a><span data-ttu-id="e3558-162">Aufgeteilte Include-Abfragen</span><span class="sxs-lookup"><span data-stu-id="e3558-162">Split Include</span></span>

<span data-ttu-id="e3558-163">Leitender Entwickler: @smitpatel</span><span class="sxs-lookup"><span data-stu-id="e3558-163">Lead developer: @smitpatel</span></span>

<span data-ttu-id="e3558-164">Nachverfolgbar über [Issue 20892](https://github.com/dotnet/efcore/issues/20892)</span><span class="sxs-lookup"><span data-stu-id="e3558-164">Tracked by [#20892](https://github.com/dotnet/efcore/issues/20892)</span></span>

<span data-ttu-id="e3558-165">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="e3558-165">T-shirt size: L</span></span>

<span data-ttu-id="e3558-166">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="e3558-166">Status: Done</span></span>

<span data-ttu-id="e3558-167">In EF Core 3.0 wurde das Standardverhalten für das Erstellen einer einzelnen SQL-Abfrage für eine bestimmte LINQ-Abfrage geändert.</span><span class="sxs-lookup"><span data-stu-id="e3558-167">EF Core 3.0 changed the default behavior to create a single SQL query for a given LINQ query.</span></span>
<span data-ttu-id="e3558-168">Dadurch kam es zu enormen Leistungsregressionen für Abfragen, die Include für mehrere Sammlungen verwenden.</span><span class="sxs-lookup"><span data-stu-id="e3558-168">This caused large performance regressions for queries that use Include for multiple collections.</span></span>

<span data-ttu-id="e3558-169">In EF Core 5.0 wird das neue Standardverhalten beibehalten.</span><span class="sxs-lookup"><span data-stu-id="e3558-169">In EF Core 5.0, we are retaining the new default behavior.</span></span>
<span data-ttu-id="e3558-170">EF Core 5.0 lässt jedoch jetzt die Generierung mehrerer Include-Abfragen für Sammlungen zu, bei denen eine einzelne Abfrage zu Leistungsproblemen führen würde.</span><span class="sxs-lookup"><span data-stu-id="e3558-170">However, EF Core 5.0 will now allow generation of multiple queries for collection Includes where having a single query is causing bad performance.</span></span>

## <a name="required-one-to-one-dependents"></a><span data-ttu-id="e3558-171">Erforderliche Elemente mit 1:1-Abhängigkeit</span><span class="sxs-lookup"><span data-stu-id="e3558-171">Required one-to-one dependents</span></span>

<span data-ttu-id="e3558-172">Leitende Entwickler: @AndriySvyryd und @smitpatel</span><span class="sxs-lookup"><span data-stu-id="e3558-172">Lead developers: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="e3558-173">Nachverfolgbar über [Issue 12100](https://github.com/dotnet/efcore/issues/12100)</span><span class="sxs-lookup"><span data-stu-id="e3558-173">Tracked by [#12100](https://github.com/dotnet/efcore/issues/12100)</span></span>

<span data-ttu-id="e3558-174">T-Shirt-Größe: M</span><span class="sxs-lookup"><span data-stu-id="e3558-174">T-shirt size: M</span></span>

<span data-ttu-id="e3558-175">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="e3558-175">Status: Done</span></span>

<span data-ttu-id="e3558-176">In EF Core 3.0 sind alle abhängigen Elemente optional, einschließlich nicht eigenständiger Typen (Person.Address kann beispielsweise NULL sein).</span><span class="sxs-lookup"><span data-stu-id="e3558-176">In EF Core 3.0, all dependents, including owned types are optional (e.g. Person.Address can be null).</span></span> <span data-ttu-id="e3558-177">In EF Core 5.0 können abhängige Elemente als „erforderlich“ konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="e3558-177">In EF Core 5.0, dependents can be configured as required.</span></span>

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="e3558-178">Rationalisieren von „ToTable“, „ToQuery“, „ToView“, „FromSQL“, usw.</span><span class="sxs-lookup"><span data-stu-id="e3558-178">Rationalize ToTable, ToQuery, ToView, FromSql, etc</span></span>

<span data-ttu-id="e3558-179">Leitende Entwickler: @AndriySvyryd und @smitpatel</span><span class="sxs-lookup"><span data-stu-id="e3558-179">Lead developers: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="e3558-180">Nachverfolgbar über [#17270](https://github.com/dotnet/efcore/issues/17270)</span><span class="sxs-lookup"><span data-stu-id="e3558-180">Tracked by [#17270](https://github.com/dotnet/efcore/issues/17270)</span></span>

<span data-ttu-id="e3558-181">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="e3558-181">T-shirt size: L</span></span>

<span data-ttu-id="e3558-182">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="e3558-182">Status: Done</span></span>

<span data-ttu-id="e3558-183">Bereits in früheren Versionen wurden Schritte zur Unterstützung von unformatierten SQL-Abfragen, schlüssellosen Typen sowie damit zusammenhängenden Dingen unternommen.</span><span class="sxs-lookup"><span data-stu-id="e3558-183">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="e3558-184">Beim Zusammenspiel des Ganzen bestehen jedoch noch Inkonsistenzen und Verbesserungsbedarf.</span><span class="sxs-lookup"><span data-stu-id="e3558-184">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="e3558-185">Ziel in Version 5.0 ist es, daran zu arbeiten und den Benutzern das Definieren, Migrieren und Verwenden verschiedener Entitätstypen sowie der dazugehörigen Abfragen und Datenbankartefakte zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="e3558-185">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="e3558-186">Dafür sind ggf. auch Aktualisierungen der kompilierten Abfrage-API erforderlich.</span><span class="sxs-lookup"><span data-stu-id="e3558-186">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="e3558-187">Dadurch kann es jedoch zu Breaking Changes auf Anwendungsebene kommen, da manche der aktuell verfügbaren Funktionen den Benutzern zu viel ermöglichen und dadurch Fehler provoziert werden.</span><span class="sxs-lookup"><span data-stu-id="e3558-187">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="e3558-188">Deshalb werden später wahrscheinliche einige dieser Funktionen blockiert sein. Sie erhalten dann stattdessen eine Anleitung mit einer alternativen Lösung.</span><span class="sxs-lookup"><span data-stu-id="e3558-188">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="e3558-189">Allgemeine Abfrageverbesserungen</span><span class="sxs-lookup"><span data-stu-id="e3558-189">General query enhancements</span></span>

<span data-ttu-id="e3558-190">Leitende Entwickler: @smitpatel und @maumar</span><span class="sxs-lookup"><span data-stu-id="e3558-190">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="e3558-191">Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `area-query`](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="e3558-191">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="e3558-192">T-Shirt-Größe: XL</span><span class="sxs-lookup"><span data-stu-id="e3558-192">T-shirt size: XL</span></span>

<span data-ttu-id="e3558-193">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="e3558-193">Status: Done</span></span>

<span data-ttu-id="e3558-194">Für EF Core 3.0 wurde der Code für die Abfrageübersetzung umfassend umgeschrieben.</span><span class="sxs-lookup"><span data-stu-id="e3558-194">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="e3558-195">Deshalb ist er allgemein viel robuster.</span><span class="sxs-lookup"><span data-stu-id="e3558-195">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="e3558-196">Bei Version 5.0 sind neben den für die TPT-Unterstützung und das Überspringen von Navigationseigenschaften erforderlichen Änderungen keine großen Anpassungen an der Abfrageübersetzung vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="e3558-196">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="e3558-197">Es bestehen jedoch noch einige technische Altlasten aus dem Wechsel zu Version 3.0, für die noch viel Arbeit erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="e3558-197">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="e3558-198">Zusätzlich werden viele Fehler behoben und kleine Verbesserungen eingeführt, wodurch die Benutzer Abfragen noch besser ausführen können.</span><span class="sxs-lookup"><span data-stu-id="e3558-198">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="e3558-199">Migration und Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="e3558-199">Migrations and deployment experience</span></span>

<span data-ttu-id="e3558-200">Leitender Entwickler: @bricelam</span><span class="sxs-lookup"><span data-stu-id="e3558-200">Lead developers: @bricelam</span></span>

<span data-ttu-id="e3558-201">Nachverfolgbar über [#19587](https://github.com/dotnet/efcore/issues/19587)</span><span class="sxs-lookup"><span data-stu-id="e3558-201">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="e3558-202">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="e3558-202">T-shirt size: L</span></span>

<span data-ttu-id="e3558-203">Status: Zugeordnet/Erledigt</span><span class="sxs-lookup"><span data-stu-id="e3558-203">Status: Scoped/Done</span></span>

<span data-ttu-id="e3558-204">Eingrenzung: Das [Migrationsbundlefeature](https://github.com/dotnet/efcore/issues/19693) wurde bis auf einen Zeitpunkt nach dem EF Core 5.0-Release verschoben.</span><span class="sxs-lookup"><span data-stu-id="e3558-204">Scoping: The [migrations bundles feature](https://github.com/dotnet/efcore/issues/19693) has been deferred until after the EF Core 5.0 release.</span></span> <span data-ttu-id="e3558-205">Einige andere [Verbesserungen für Migrationen](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460) sind jedoch im EF Core 5.0-Release enthalten.</span><span class="sxs-lookup"><span data-stu-id="e3558-205">However, several other [targeted improvements related to migrations](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460) will be included in EF Core 5.0</span></span>

<span data-ttu-id="e3558-206">Derzeit migrieren viele Entwickler ihre Datenbanken beim Start der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="e3558-206">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="e3558-207">Das ist einfach, empfiehlt sich jedoch aus folgenden Gründen nicht:</span><span class="sxs-lookup"><span data-stu-id="e3558-207">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="e3558-208">Mehrere Threads/Prozesse/Server versuchen möglicherweise gleichzeitig, die Datenbank zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="e3558-208">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="e3558-209">Anwendungen versuchen möglicherweise, den Zustand „inkonsistent“ zu erreichen, während dies geschieht.</span><span class="sxs-lookup"><span data-stu-id="e3558-209">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="e3558-210">Normalerweise sollte die Berechtigung, das Schema zu modifizieren, nicht bei Ausführung der Anwendung erteilt werden.</span><span class="sxs-lookup"><span data-stu-id="e3558-210">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="e3558-211">Wenn etwas schiefgeht, ist es schwer, wieder einen fehlerfreien Zustand zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="e3558-211">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="e3558-212">Wir möchten dafür sorgen, dass es einfach ist, die Datenbank zum Zeitpunkt der Bereitstellung zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="e3558-212">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="e3558-213">Dafür haben wir uns folgende Ziele gesetzt:</span><span class="sxs-lookup"><span data-stu-id="e3558-213">This should:</span></span>

* <span data-ttu-id="e3558-214">Gemeinsame Lösung für Linux, macOS und Windows</span><span class="sxs-lookup"><span data-stu-id="e3558-214">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="e3558-215">Unkompliziert über die Befehlszeile ausführbar</span><span class="sxs-lookup"><span data-stu-id="e3558-215">Be a good experience on the command line</span></span>
* <span data-ttu-id="e3558-216">Unterstützung von Szenarios mit Containern</span><span class="sxs-lookup"><span data-stu-id="e3558-216">Support scenarios with containers</span></span>
* <span data-ttu-id="e3558-217">Kombinierbar mit häufig verwendeten Bereitstellungstools-/-flows</span><span class="sxs-lookup"><span data-stu-id="e3558-217">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="e3558-218">Integration in Visual Studio (mindestens)</span><span class="sxs-lookup"><span data-stu-id="e3558-218">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="e3558-219">Daraus werden sich wahrscheinlich viele kleine Verbesserungen bei EF Core ergeben, z. B. bessere Migrationen bei SQLite. Kombiniert mit Leitfäden und einer langfristigen Zusammenarbeit mit anderen Teams entstehen ganzheitliche Verbesserungen, nicht nur bei EF.</span><span class="sxs-lookup"><span data-stu-id="e3558-219">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="e3558-220">EF Core-Plattformen</span><span class="sxs-lookup"><span data-stu-id="e3558-220">EF Core platforms experience</span></span>

<span data-ttu-id="e3558-221">Leitende Entwickler: @roji und @bricelam</span><span class="sxs-lookup"><span data-stu-id="e3558-221">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="e3558-222">Nachverfolgbar über [#19588](https://github.com/dotnet/efcore/issues/19588)</span><span class="sxs-lookup"><span data-stu-id="e3558-222">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="e3558-223">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="e3558-223">T-shirt size: L</span></span>

<span data-ttu-id="e3558-224">Status: Zugeordnet/Erledigt</span><span class="sxs-lookup"><span data-stu-id="e3558-224">Status: Scope/Done</span></span>

<span data-ttu-id="e3558-225">Eingrenzung: Ein Plattformleitfaden und Beispiele werden für Blazor, Xamarin, WinForms und WPF veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="e3558-225">Scoping: Platform guidance and samples are published for Blazor, Xamarin, WinForms, and WPF.</span></span> <span data-ttu-id="e3558-226">Xamarin- sowie weitere AOT-/Linker-Verbesserungen sind für EF Core 6.0 geplant.</span><span class="sxs-lookup"><span data-stu-id="e3558-226">Xamarin and other AOT/linker work is now planned for EF Core 6.0.</span></span>

<span data-ttu-id="e3558-227">Wir haben gute Leitfäden für die Verwendung von EF Core in traditionellen, MVC ähnelnden Webanwendungen.</span><span class="sxs-lookup"><span data-stu-id="e3558-227">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="e3558-228">Die Leitfäden für andere Plattformen und Anwendungsmodelle sind entweder veraltet oder fehlen ganz.</span><span class="sxs-lookup"><span data-stu-id="e3558-228">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="e3558-229">Für EF Core 5.0 werden wir untersuchen, wie gut sich EF-Core mit den folgenden Dingen kombinieren lässt. Die Ergebnisse werden dokumentiert, und wir versuchen, weitere Verbesserungen zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="e3558-229">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="e3558-230">Blazor</span><span class="sxs-lookup"><span data-stu-id="e3558-230">Blazor</span></span>
* <span data-ttu-id="e3558-231">Xamarin, einschließlich der Verwendung von AOT/Linker-Story</span><span class="sxs-lookup"><span data-stu-id="e3558-231">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="e3558-232">WinForms/WPF/WinUI und möglicherweise andere UI</span><span class="sxs-lookup"><span data-stu-id="e3558-232">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="e3558-233">frameworks</span><span class="sxs-lookup"><span data-stu-id="e3558-233">frameworks</span></span>

<span data-ttu-id="e3558-234">Daraus werden sich wahrscheinlich viele kleine Verbesserungen bei EF Core ergeben. Kombiniert mit Leitfäden und einer langfristigen Zusammenarbeit mit anderen Teams entstehen ganzheitliche Verbesserungen, nicht nur bei EF.</span><span class="sxs-lookup"><span data-stu-id="e3558-234">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="e3558-235">Bestimmte Bereiche, die wir uns ansehen möchten, sind:</span><span class="sxs-lookup"><span data-stu-id="e3558-235">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="e3558-236">Bereitstellung, einschließlich der Verwendung von EF-Tools, z. B. für Migrationen</span><span class="sxs-lookup"><span data-stu-id="e3558-236">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="e3558-237">Anwendungsmodelle, einschließlich Xamarin, Blazer und gegebenenfalls weitere</span><span class="sxs-lookup"><span data-stu-id="e3558-237">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="e3558-238">SQLite-Funktionen, einschließlich räumlicher Daten und Neuerstellung von Tabellen</span><span class="sxs-lookup"><span data-stu-id="e3558-238">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="e3558-239">AOT und Verknüpfungen</span><span class="sxs-lookup"><span data-stu-id="e3558-239">AOT and linking experiences</span></span>
* <span data-ttu-id="e3558-240">Integration von Diagnosefunktionen, einschließlich Leistungsindikatoren</span><span class="sxs-lookup"><span data-stu-id="e3558-240">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="e3558-241">Leistung</span><span class="sxs-lookup"><span data-stu-id="e3558-241">Performance</span></span>

<span data-ttu-id="e3558-242">Leitender Entwickler: @roji</span><span class="sxs-lookup"><span data-stu-id="e3558-242">Lead developer: @roji</span></span>

<span data-ttu-id="e3558-243">Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `area-perf`](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="e3558-243">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="e3558-244">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="e3558-244">T-shirt size: L</span></span>

<span data-ttu-id="e3558-245">Status: Zugeordnet/Erledigt</span><span class="sxs-lookup"><span data-stu-id="e3558-245">Status: Scoped/Done</span></span>

<span data-ttu-id="e3558-246">Eingrenzung: Wichtige Leistungsverbesserungen beim Npgsql-Anbieter wurden abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="e3558-246">Scoping: Major performance improvements in the Npgsql provider are complete.</span></span> <span data-ttu-id="e3558-247">Weitere Leistungsverbesserungen sind für EF Core 6.0 geplant.</span><span class="sxs-lookup"><span data-stu-id="e3558-247">Other performance work is now planned for EF Core 6.0.</span></span>

<span data-ttu-id="e3558-248">In EF Core möchten wir die vorhandenen Leistungsbenchmarks verbessern und gezielt Leistungsverbesserungen an der Runtime vornehmen.</span><span class="sxs-lookup"><span data-stu-id="e3558-248">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="e3558-249">Außerdem ist geplant, die Arbeit an der neuen ADO.NET-API für Batchverarbeitung abzuschließen, die während des Veröffentlichungszyklus der Version 3.0 als Prototyp vorgestellt worden war.</span><span class="sxs-lookup"><span data-stu-id="e3558-249">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="e3558-250">Zusätzlich möchten wir auf der ADO.NET-Ebene zusätzliche Leistungsverbesserungen bei Npgsql vornehmen.</span><span class="sxs-lookup"><span data-stu-id="e3558-250">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="e3558-251">Des Weiteren möchten wir bei Bedarf ADO.NET/EF Core-Leistungsindikatoren und andere Diagnosefunktionen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="e3558-251">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="e3558-252">Dokumentation zu Architektur und Mitwirkenden</span><span class="sxs-lookup"><span data-stu-id="e3558-252">Architectural/contributor documentation</span></span>

<span data-ttu-id="e3558-253">Für die Dokumentation verantwortliche Person: @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="e3558-253">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="e3558-254">Nachverfolgbar über [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span><span class="sxs-lookup"><span data-stu-id="e3558-254">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="e3558-255">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="e3558-255">T-shirt size: L</span></span>

<span data-ttu-id="e3558-256">Status: Ausschneiden</span><span class="sxs-lookup"><span data-stu-id="e3558-256">Status: Cut</span></span>

<span data-ttu-id="e3558-257">Ziel ist es, verständlicher zu machen, was in EF Core passiert.</span><span class="sxs-lookup"><span data-stu-id="e3558-257">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="e3558-258">Dies kann für alle Personen nützlich sein, die EF Core verwenden. Das Hauptziel ist jedoch, externen Personen Folgendes zu erleichtern:</span><span class="sxs-lookup"><span data-stu-id="e3558-258">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="e3558-259">Mitarbeit am EFF Core-Code</span><span class="sxs-lookup"><span data-stu-id="e3558-259">Contribute to the EF Core code</span></span>
* <span data-ttu-id="e3558-260">Erstellen von Datenbankanbietern</span><span class="sxs-lookup"><span data-stu-id="e3558-260">Create database providers</span></span>
* <span data-ttu-id="e3558-261">Erstellen anderer Erweiterungen</span><span class="sxs-lookup"><span data-stu-id="e3558-261">Build other extensions</span></span>

<span data-ttu-id="e3558-262">Update: Leider war dieser Plan zu ambitioniert.</span><span class="sxs-lookup"><span data-stu-id="e3558-262">Update: Unfortunately, this plan was too ambitious.</span></span>
<span data-ttu-id="e3558-263">Wir halten das Feature weiterhin für wichtig, dennoch kann es nicht in EF Core 5.0 aufgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="e3558-263">We still believe this is important, but unfortunately it won't land with EF Core 5.0.</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="e3558-264">Dokumentation zu Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="e3558-264">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="e3558-265">Für die Dokumentation verantwortliche Person: @bricelam</span><span class="sxs-lookup"><span data-stu-id="e3558-265">Lead documenter: @bricelam</span></span>

<span data-ttu-id="e3558-266">Nachverfolgbar über [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span><span class="sxs-lookup"><span data-stu-id="e3558-266">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="e3558-267">T-Shirt-Größe: M</span><span class="sxs-lookup"><span data-stu-id="e3558-267">T-shirt size: M</span></span>

<span data-ttu-id="e3558-268">Status: Abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="e3558-268">Status: Completed.</span></span> <span data-ttu-id="e3558-269">Die neue Dokumentation finden Sie [hier](/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span><span class="sxs-lookup"><span data-stu-id="e3558-269">The new documentation is [live on the Microsoft docs site](/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="e3558-270">Das EF-Team ist auch Eigentümer des Microsoft.Data.Sqlite ADO.NET-Anbieters.</span><span class="sxs-lookup"><span data-stu-id="e3558-270">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="e3558-271">Wir planen, als Teil der Veröffentlichungen für EF Core 5.0, eine vollständige Dokumentation zu diesem Anbieter bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="e3558-271">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="e3558-272">Allgemeine Dokumentation</span><span class="sxs-lookup"><span data-stu-id="e3558-272">General documentation</span></span>

<span data-ttu-id="e3558-273">Für die Dokumentation verantwortliche Person: @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="e3558-273">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="e3558-274">Nachverfolgbar über [Docs-Repository im 5.0-Meilenstein](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="e3558-274">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="e3558-275">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="e3558-275">T-shirt size: L</span></span>

<span data-ttu-id="e3558-276">Status: In Bearbeitung</span><span class="sxs-lookup"><span data-stu-id="e3558-276">Status: In-progress</span></span>

<span data-ttu-id="e3558-277">Die Aktualisierung der bestehenden Dokumentation für die Releases 3.0 und 3.1 läuft bereits.</span><span class="sxs-lookup"><span data-stu-id="e3558-277">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="e3558-278">Zusätzlich arbeiten wir an Folgendem:</span><span class="sxs-lookup"><span data-stu-id="e3558-278">We are also working on:</span></span>

* <span data-ttu-id="e3558-279">Überarbeitung der Einsteigerdokumentation, um sie ansprechender und leichter verständlich zu machen</span><span class="sxs-lookup"><span data-stu-id="e3558-279">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
* <span data-ttu-id="e3558-280">Neuanordnung von Dokumenten, um sie leichter auffindbar zu machen und um Querverweise hinzufügen zu können</span><span class="sxs-lookup"><span data-stu-id="e3558-280">Reorganization of docs to make things easier to find and to add cross-references</span></span>
* <span data-ttu-id="e3558-281">Hinzufügen von ausführlicheren Details und Erläuterungen zu vorhandenen Dokumenten</span><span class="sxs-lookup"><span data-stu-id="e3558-281">Adding more details and clarifications to existing docs</span></span>
* <span data-ttu-id="e3558-282">Aktualisieren bestehender Beispiele und Hinzufügen zusätzlicher Beispiele</span><span class="sxs-lookup"><span data-stu-id="e3558-282">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="e3558-283">Beheben von Fehlern</span><span class="sxs-lookup"><span data-stu-id="e3558-283">Fixing bugs</span></span>

<span data-ttu-id="e3558-284">Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `type-bug`](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span><span class="sxs-lookup"><span data-stu-id="e3558-284">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="e3558-285">Entwickler: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="e3558-285">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="e3558-286">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="e3558-286">T-shirt size: L</span></span>

<span data-ttu-id="e3558-287">Status: In Bearbeitung</span><span class="sxs-lookup"><span data-stu-id="e3558-287">Status: In-progress</span></span>

<span data-ttu-id="e3558-288">Zum Zeitpunkt der Entstehung dieses Artikels haben wir 135 Fehler kategorisiert, die in Version 5.0 behoben werden sollen. Davon sind 62 bereits behoben. Hier besteht jedoch eine starke Überschneidung mit den weiter oben beschriebenen _allgemeinen Abfrageverbesserungen_.</span><span class="sxs-lookup"><span data-stu-id="e3558-288">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="e3558-289">Im Laufe des Releases 3.0 wurden pro Monat etwa 23 Probleme in Meilensteine aufgenommen.</span><span class="sxs-lookup"><span data-stu-id="e3558-289">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="e3558-290">Davon müssen jedoch nicht alle in Version 5.0 behoben werden.</span><span class="sxs-lookup"><span data-stu-id="e3558-290">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="e3558-291">Grob geschätzt sollen im Zeitraum von Version 5.0 zusätzlich 150 Probleme behoben werden.</span><span class="sxs-lookup"><span data-stu-id="e3558-291">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="e3558-292">Kleinere Verbesserungen</span><span class="sxs-lookup"><span data-stu-id="e3558-292">Small enhancements</span></span>

<span data-ttu-id="e3558-293">Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `type-enhancement`](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span><span class="sxs-lookup"><span data-stu-id="e3558-293">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="e3558-294">Entwickler: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="e3558-294">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="e3558-295">T-Shirt-Größe: L</span><span class="sxs-lookup"><span data-stu-id="e3558-295">T-shirt size: L</span></span>

<span data-ttu-id="e3558-296">Status: Vorgehensweise</span><span class="sxs-lookup"><span data-stu-id="e3558-296">Status: Done</span></span>

<span data-ttu-id="e3558-297">Zusätzlich zu den oben beschriebenen größeren Funktionen sind in Version 5.0 auch viele kleinere Verbesserungen geplant, mit denen Probleme behoben werden, die als unkritisch gelten.</span><span class="sxs-lookup"><span data-stu-id="e3558-297">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="e3558-298">Viele dieser Verbesserungen sind auch durch die oben beschriebenen, allgemeineren Themen abgedeckt.</span><span class="sxs-lookup"><span data-stu-id="e3558-298">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="e3558-299">Verschoben auf nächste Version</span><span class="sxs-lookup"><span data-stu-id="e3558-299">Below-the-line</span></span>

<span data-ttu-id="e3558-300">Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `consider-for-next-release`](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span><span class="sxs-lookup"><span data-stu-id="e3558-300">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="e3558-301">Dabei handelt es sich um Fehlerbehebungen und Verbesserungen, die aktuell **nicht** für das Release 5.0 vorgesehen sind, aber je nach Fortschritt bei den oben beschriebenen Zielen zusätzlich angegangen werden.</span><span class="sxs-lookup"><span data-stu-id="e3558-301">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="e3558-302">Zusätzlich werden bei der Planung auch immer die [Probleme mit den meisten Stimmen](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="e3558-302">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="e3558-303">Bekannte Probleme erst in einem späteren Release zu beheben ist nicht schön, aber nötig, um die Auslastung der vorhandenen Ressourcen auf ein realistisches Maß zu beschränken.</span><span class="sxs-lookup"><span data-stu-id="e3558-303">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="suggestions"></a><span data-ttu-id="e3558-304">Vorschläge</span><span class="sxs-lookup"><span data-stu-id="e3558-304">Suggestions</span></span>

<span data-ttu-id="e3558-305">Ihr Feedback zur Planung ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="e3558-305">Your feedback on planning is important.</span></span> <span data-ttu-id="e3558-306">Sie können für ein Problem auf GitHub abstimmen (Daumen hoch) und so angeben, dass dieses Problem wichtig ist.</span><span class="sxs-lookup"><span data-stu-id="e3558-306">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="e3558-307">Diese Daten werden dann in den [Planungsprozess](xref:core/what-is-new/release-planning) für das nächste Release aufgenommen.</span><span class="sxs-lookup"><span data-stu-id="e3558-307">This data will then feed into the [planning process](xref:core/what-is-new/release-planning) for the next release.</span></span>
