---
title: Breaking Changes in EF Core 3.x – EF Core
description: Vollständige Liste der in Entity Framework Core 3.x eingeführten Breaking Changes
author: ajcvickers
ms.date: 09/05/2020
uid: core/what-is-new/ef-core-3.x/breaking-changes
ms.openlocfilehash: 644e61994dab4e9993c6a78792ff584c57fbe48a
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620679"
---
# <a name="breaking-changes-included-in-ef-core-3x"></a><span data-ttu-id="7a5c6-103">Breaking Changes in EF Core 3.x</span><span class="sxs-lookup"><span data-stu-id="7a5c6-103">Breaking changes included in EF Core 3.x</span></span>

<span data-ttu-id="7a5c6-104">Die folgenden API-Änderungen und Behavior Changes können dazu führen, dass vorhandene Anwendungen nach einem Upgrade auf 3.x nicht mehr funktionieren.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-104">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.x.</span></span>
<span data-ttu-id="7a5c6-105">Änderungen, die sich voraussichtlich nur auf Datenbankanbieter auswirken, sind unter [Änderungen mit Auswirkungen auf den Anbieter](xref:core/providers/provider-log) dokumentiert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-105">Changes that we expect to only impact database providers are documented under [provider changes](xref:core/providers/provider-log).</span></span>

## <a name="summary"></a><span data-ttu-id="7a5c6-106">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="7a5c6-106">Summary</span></span>

| <span data-ttu-id="7a5c6-107">**Wichtige Änderung**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-107">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="7a5c6-108">**Auswirkungen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-108">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="7a5c6-109">LINQ-Abfragen werden nicht mehr auf dem Client ausgewertet</span><span class="sxs-lookup"><span data-stu-id="7a5c6-109">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="7a5c6-110">Hoch</span><span class="sxs-lookup"><span data-stu-id="7a5c6-110">High</span></span>       |
| [<span data-ttu-id="7a5c6-111">EF Core 3.0 zielt auf .NET Standard 2.1 und nicht auf .NET Standard 2.0 ab</span><span class="sxs-lookup"><span data-stu-id="7a5c6-111">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>](#netstandard21) | <span data-ttu-id="7a5c6-112">Hoch</span><span class="sxs-lookup"><span data-stu-id="7a5c6-112">High</span></span>      |
| [<span data-ttu-id="7a5c6-113">Das EF Core-Befehlszeilentool (dotnet ef) ist nicht mehr Bestandteil des .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="7a5c6-113">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="7a5c6-114">Hoch</span><span class="sxs-lookup"><span data-stu-id="7a5c6-114">High</span></span>      |
| [<span data-ttu-id="7a5c6-115">DetectChanges berücksichtigt vom Speicher generierte Schlüsselwerte</span><span class="sxs-lookup"><span data-stu-id="7a5c6-115">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="7a5c6-116">Hoch</span><span class="sxs-lookup"><span data-stu-id="7a5c6-116">High</span></span>      |
| [<span data-ttu-id="7a5c6-117">FromSql, ExecuteSql und ExecuteSqlAsync wurden umbenannt</span><span class="sxs-lookup"><span data-stu-id="7a5c6-117">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="7a5c6-118">Hoch</span><span class="sxs-lookup"><span data-stu-id="7a5c6-118">High</span></span>      |
| [<span data-ttu-id="7a5c6-119">Abfragetypen werden mit Entitätstypen zusammengeführt</span><span class="sxs-lookup"><span data-stu-id="7a5c6-119">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="7a5c6-120">Hoch</span><span class="sxs-lookup"><span data-stu-id="7a5c6-120">High</span></span>      |
| [<span data-ttu-id="7a5c6-121">Entity Framework Core ist nicht mehr Teil des gemeinsam verwendeten ASP.NET Core-Frameworks</span><span class="sxs-lookup"><span data-stu-id="7a5c6-121">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="7a5c6-122">Medium</span><span class="sxs-lookup"><span data-stu-id="7a5c6-122">Medium</span></span>      |
| [<span data-ttu-id="7a5c6-123">Kaskadierende Deletes werden standardmäßig sofort ausgeführt</span><span class="sxs-lookup"><span data-stu-id="7a5c6-123">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="7a5c6-124">Medium</span><span class="sxs-lookup"><span data-stu-id="7a5c6-124">Medium</span></span>      |
| [<span data-ttu-id="7a5c6-125">Eager Loading von verwandten Entitäten erfolgt nun in einer einzelnen Abfrage</span><span class="sxs-lookup"><span data-stu-id="7a5c6-125">Eager loading of related entities now happens in a single query</span></span>](#eager-loading-single-query) | <span data-ttu-id="7a5c6-126">Medium</span><span class="sxs-lookup"><span data-stu-id="7a5c6-126">Medium</span></span>      |
| [<span data-ttu-id="7a5c6-127">DeleteBehavior.Restrict verfügt über eine übersichtlichere Semantik</span><span class="sxs-lookup"><span data-stu-id="7a5c6-127">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="7a5c6-128">Medium</span><span class="sxs-lookup"><span data-stu-id="7a5c6-128">Medium</span></span>      |
| [<span data-ttu-id="7a5c6-129">Die Konfigurations-API für Beziehungen abhängiger (owned) Typen wurde geändert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-129">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="7a5c6-130">Medium</span><span class="sxs-lookup"><span data-stu-id="7a5c6-130">Medium</span></span>      |
| [<span data-ttu-id="7a5c6-131">Für jede Eigenschaft wird separat ein ganzzahliger speicherinterner Schlüssel generiert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-131">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="7a5c6-132">Medium</span><span class="sxs-lookup"><span data-stu-id="7a5c6-132">Medium</span></span>      |
| [<span data-ttu-id="7a5c6-133">Abfragen ohne Nachverfolgung führen keine Identitätsauflösung mehr durch</span><span class="sxs-lookup"><span data-stu-id="7a5c6-133">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="7a5c6-134">Medium</span><span class="sxs-lookup"><span data-stu-id="7a5c6-134">Medium</span></span>      |
| [<span data-ttu-id="7a5c6-135">Metadaten-API-Änderungen</span><span class="sxs-lookup"><span data-stu-id="7a5c6-135">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="7a5c6-136">Medium</span><span class="sxs-lookup"><span data-stu-id="7a5c6-136">Medium</span></span>      |
| [<span data-ttu-id="7a5c6-137">Anbieterspezifische Metadaten-API-Änderungen</span><span class="sxs-lookup"><span data-stu-id="7a5c6-137">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="7a5c6-138">Medium</span><span class="sxs-lookup"><span data-stu-id="7a5c6-138">Medium</span></span>      |
| [<span data-ttu-id="7a5c6-139">UseRowNumberForPaging wurde entfernt.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-139">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="7a5c6-140">Medium</span><span class="sxs-lookup"><span data-stu-id="7a5c6-140">Medium</span></span>      |
| [<span data-ttu-id="7a5c6-141">FromSql-Methode kann nicht zusammengesetzt werden, wenn sie mit einer gespeicherten Prozedur verwendet wird</span><span class="sxs-lookup"><span data-stu-id="7a5c6-141">FromSql method when used with stored procedure cannot be composed</span></span>](#fromsqlsproc) | <span data-ttu-id="7a5c6-142">Medium</span><span class="sxs-lookup"><span data-stu-id="7a5c6-142">Medium</span></span>      |
| [<span data-ttu-id="7a5c6-143">FromSql-Methoden können nur für die Stammelemente der Abfrage angegeben werden</span><span class="sxs-lookup"><span data-stu-id="7a5c6-143">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="7a5c6-144">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-144">Low</span></span>      |
| [<span data-ttu-id="7a5c6-145">~~Die Abfrageausführung wird auf Debugebene protokolliert~~ – zurückgesetzt</span><span class="sxs-lookup"><span data-stu-id="7a5c6-145">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | <span data-ttu-id="7a5c6-146">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-146">Low</span></span>      |
| [<span data-ttu-id="7a5c6-147">Temporäre Schlüsselwerte werden nicht mehr Entitätsinstanzen zugewiesen</span><span class="sxs-lookup"><span data-stu-id="7a5c6-147">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="7a5c6-148">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-148">Low</span></span>      |
| [<span data-ttu-id="7a5c6-149">Abhängige Entitäten, die die Tabelle gemeinsam mit dem Prinzipal verwenden, sind jetzt optional</span><span class="sxs-lookup"><span data-stu-id="7a5c6-149">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="7a5c6-150">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-150">Low</span></span>      |
| [<span data-ttu-id="7a5c6-151">Alle Entitäten, die eine Tabelle mit einer Spalte für das Parallelitätstoken gemeinsam verwenden, müssen diese einer Eigenschaft zuordnen</span><span class="sxs-lookup"><span data-stu-id="7a5c6-151">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="7a5c6-152">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-152">Low</span></span>      |
| [<span data-ttu-id="7a5c6-153">Benutzereigene Entitäten können nicht abgefragt werden, ohne dass der Besitzer eine Nachverfolgungsabfrage verwendet</span><span class="sxs-lookup"><span data-stu-id="7a5c6-153">Owned entities cannot be queried without the owner using a tracking query</span></span>](#owned-query) | <span data-ttu-id="7a5c6-154">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-154">Low</span></span>      |
| [<span data-ttu-id="7a5c6-155">Geerbte Eigenschaften von nicht zugeordneten Typen sind nun einer einzelnen Spalte für alle abgeleiteten Typen zugeordnet</span><span class="sxs-lookup"><span data-stu-id="7a5c6-155">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="7a5c6-156">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-156">Low</span></span>      |
| [<span data-ttu-id="7a5c6-157">Die Konvention zur Fremdschlüsseleigenschaft entspricht nicht mehr dem Namen der Prinzipaleigenschaft</span><span class="sxs-lookup"><span data-stu-id="7a5c6-157">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="7a5c6-158">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-158">Low</span></span>      |
| [<span data-ttu-id="7a5c6-159">Die Datenbankverbindung wird jetzt geschlossen, wenn sie nicht mehr verwendet wird, bevor TransactionScope abgeschlossen wurde</span><span class="sxs-lookup"><span data-stu-id="7a5c6-159">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="7a5c6-160">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-160">Low</span></span>      |
| [<span data-ttu-id="7a5c6-161">Unterstützungsfelder werden standardmäßig verwendet</span><span class="sxs-lookup"><span data-stu-id="7a5c6-161">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="7a5c6-162">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-162">Low</span></span>      |
| [<span data-ttu-id="7a5c6-163">Eine Ausnahme wird ausgelöst, wenn mehrere kompatible Unterstützungsfelder gefunden werden</span><span class="sxs-lookup"><span data-stu-id="7a5c6-163">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="7a5c6-164">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-164">Low</span></span>      |
| [<span data-ttu-id="7a5c6-165">„Nur-Feld“-Eigenschaftsnamen sollten dem Feldnamen entsprechen</span><span class="sxs-lookup"><span data-stu-id="7a5c6-165">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="7a5c6-166">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-166">Low</span></span>      |
| [<span data-ttu-id="7a5c6-167">Von AddDbContext/AddDbContextPool werden AddLogging und AddMemoryCache nicht mehr aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-167">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="7a5c6-168">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-168">Low</span></span>      |
| [<span data-ttu-id="7a5c6-169">AddEntityFramework\* fügt IMemoryCache mit einer Größenbeschränkung hinzu</span><span class="sxs-lookup"><span data-stu-id="7a5c6-169">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>](#addentityframework-adds-imemorycache-with-a-size-limit) | <span data-ttu-id="7a5c6-170">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-170">Low</span></span>      |
| [<span data-ttu-id="7a5c6-171">DbContext.Entry erkennt nun mit DetectChanges lokale Änderungen</span><span class="sxs-lookup"><span data-stu-id="7a5c6-171">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="7a5c6-172">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-172">Low</span></span>      |
| [<span data-ttu-id="7a5c6-173">Schlüssel aus Zeichenfolgen und Bytearrays werden standardmäßig nicht vom Client generiert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-173">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="7a5c6-174">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-174">Low</span></span>      |
| [<span data-ttu-id="7a5c6-175">ILoggerFactory ist nun ein bereichsbezogener Dienst</span><span class="sxs-lookup"><span data-stu-id="7a5c6-175">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="7a5c6-176">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-176">Low</span></span>      |
| [<span data-ttu-id="7a5c6-177">Für Proxys mit Lazy Loading (trägem Laden) gilt nicht mehr die Annahme, dass Navigationseigenschaften vollständig geladen sind</span><span class="sxs-lookup"><span data-stu-id="7a5c6-177">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="7a5c6-178">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-178">Low</span></span>      |
| [<span data-ttu-id="7a5c6-179">Die Erstellung zu vieler interner Dienstanbieter führt standardmäßig zu einem Fehler</span><span class="sxs-lookup"><span data-stu-id="7a5c6-179">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="7a5c6-180">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-180">Low</span></span>      |
| [<span data-ttu-id="7a5c6-181">Neues Verhalten für HasOne/HasMany bei Aufruf mit einer einzelnen Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="7a5c6-181">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="7a5c6-182">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-182">Low</span></span>      |
| [<span data-ttu-id="7a5c6-183">Der Rückgabetyp für mehrere asynchrone Methoden wurde von Task in ValueTask geändert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-183">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="7a5c6-184">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-184">Low</span></span>      |
| [<span data-ttu-id="7a5c6-185">Die Anmerkung Relational:TypeMapping heißt nun nur noch TypeMapping</span><span class="sxs-lookup"><span data-stu-id="7a5c6-185">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="7a5c6-186">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-186">Low</span></span>      |
| [<span data-ttu-id="7a5c6-187">ToTable löst für einen abgeleiteten Typ eine Ausnahme aus</span><span class="sxs-lookup"><span data-stu-id="7a5c6-187">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="7a5c6-188">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-188">Low</span></span>      |
| [<span data-ttu-id="7a5c6-189">EF Core sendet keine PRAGMA-Anweisungen mehr, um Fremdschlüssel in SQLite zu erzwingen</span><span class="sxs-lookup"><span data-stu-id="7a5c6-189">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="7a5c6-190">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-190">Low</span></span>      |
| [<span data-ttu-id="7a5c6-191">SQLitePCLRaw.bundle_e_sqlite3 ist nun eine Abhängigkeit von Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="7a5c6-191">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="7a5c6-192">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-192">Low</span></span>      |
| [<span data-ttu-id="7a5c6-193">GUID-Werte werden jetzt als TEXT in SQLite gespeichert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-193">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="7a5c6-194">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-194">Low</span></span>      |
| [<span data-ttu-id="7a5c6-195">Char-Werte werden jetzt als TEXT in SQLite gespeichert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-195">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="7a5c6-196">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-196">Low</span></span>      |
| [<span data-ttu-id="7a5c6-197">Migrations-IDs werden jetzt mit dem Kalender der invarianten Kultur generiert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-197">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="7a5c6-198">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-198">Low</span></span>      |
| [<span data-ttu-id="7a5c6-199">Erweiterungsinformationen und Metadaten aus IDbContextOptionsExtension entfernt</span><span class="sxs-lookup"><span data-stu-id="7a5c6-199">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="7a5c6-200">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-200">Low</span></span>      |
| [<span data-ttu-id="7a5c6-201">LogQueryPossibleExceptionWithAggregateOperator wurde umbenannt</span><span class="sxs-lookup"><span data-stu-id="7a5c6-201">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="7a5c6-202">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-202">Low</span></span>      |
| [<span data-ttu-id="7a5c6-203">Verdeutlichen der API für Einschränkungsnamen von Fremdschlüsseln</span><span class="sxs-lookup"><span data-stu-id="7a5c6-203">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="7a5c6-204">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-204">Low</span></span>      |
| [<span data-ttu-id="7a5c6-205">„IRelationalDatabaseCreator.HasTables/HasTablesAsync“ ist jetzt öffentlich</span><span class="sxs-lookup"><span data-stu-id="7a5c6-205">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="7a5c6-206">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-206">Low</span></span>      |
| [<span data-ttu-id="7a5c6-207">Microsoft.EntityFrameworkCore.Design ist jetzt ein DevelopmentDependency-Paket</span><span class="sxs-lookup"><span data-stu-id="7a5c6-207">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="7a5c6-208">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-208">Low</span></span>      |
| [<span data-ttu-id="7a5c6-209">SQLitePCL.raw zu Version 2.0.0 aktualisiert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-209">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="7a5c6-210">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-210">Low</span></span>      |
| [<span data-ttu-id="7a5c6-211">NetTopologySuite auf Version 2.0.0 aktualisiert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-211">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="7a5c6-212">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-212">Low</span></span>      |
| [<span data-ttu-id="7a5c6-213">Microsoft.Data.SqlClient wird statt System.Data.SqlClient verwendet</span><span class="sxs-lookup"><span data-stu-id="7a5c6-213">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>](#SqlClient) | <span data-ttu-id="7a5c6-214">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-214">Low</span></span>      |
| [<span data-ttu-id="7a5c6-215">Beziehungen mit mehreren mehrdeutigen Selbstverweisen müssen nun konfiguriert werden</span><span class="sxs-lookup"><span data-stu-id="7a5c6-215">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="7a5c6-216">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-216">Low</span></span>      |
| [<span data-ttu-id="7a5c6-217">DbFunction.Schema ist NULL oder eine leere Zeichenfolge und ist deshalb so konfiguriert, dass es im Standardschema des Modells ist</span><span class="sxs-lookup"><span data-stu-id="7a5c6-217">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>](#udf-empty-string) | <span data-ttu-id="7a5c6-218">Niedrig</span><span class="sxs-lookup"><span data-stu-id="7a5c6-218">Low</span></span>      |

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="7a5c6-219">LINQ-Abfragen werden nicht mehr auf dem Client ausgewertet</span><span class="sxs-lookup"><span data-stu-id="7a5c6-219">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="7a5c6-220">[Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Siehe auch Issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="7a5c6-220">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="7a5c6-221">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-221">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-222">Vor Version 3.0 wurden in EF Core automatisch Ausdrücke auf dem Client ausgewertet, wenn diese Teil einer Abfrage waren und nicht in SQL oder in einen Parameter konvertiert werden konnten.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-222">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="7a5c6-223">Wenn potenziell aufwendige Ausdrücke auf dem Client ausgewertet werden sollten, wurde standardmäßig nur eine Warnung ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-223">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="7a5c6-224">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-224">**New behavior**</span></span>

<span data-ttu-id="7a5c6-225">Ab Version 3.0 können in EF Core nur Ausdrücke der obersten Projektion (dem letzten `Select()`-Aufruf in der Abfrage) auf dem Client ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-225">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="7a5c6-226">Wenn Ausdrücke in einem anderen Teil der Abfrage nicht in SQL oder in einen Parameter konvertiert werden können, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-226">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="7a5c6-227">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-227">**Why**</span></span>

<span data-ttu-id="7a5c6-228">Die automatische Auswertung von Abfragen auf Clients hat zur Folge, dass viele Abfragen auch dann ausgeführt werden, wenn wichtige Teile nicht übersetzt werden können.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-228">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="7a5c6-229">Dieses Verhalten kann zu unerwartetem und potenziell schädlichem Verhalten führen, das möglicherweise erst in der Produktionsphase erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-229">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="7a5c6-230">Eine nicht übersetzbare Bedingung in einem `Where()`-Aufruf kann sich beispielsweise so auswirken, dass alle Zeilen einer Tabelle vom Datenbankserver übertragen werden und der Filter auf dem Client angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-230">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="7a5c6-231">Wenn die Tabelle in der Entwicklungsphase nur wenige Zeilen enthält, ist es gut möglich, dass diese Situation unerkannt bleibt. Wenn die Anwendung jedoch in die Produktionsumgebung überführt wird, enthält die Tabelle möglicherweise Millionen von Zeilen, was zu schwerwiegenden Konsequenzen führen kann.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-231">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="7a5c6-232">Wie die Erfahrung in der Entwicklungsphase gezeigt hat, können auch Warnungen, die bei Auswertungen auf dem Client ausgelöst werden, dieses Problem nicht lösen, da sie sich leicht ignorieren lassen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-232">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="7a5c6-233">Die automatische Auswertung auf Clients kann darüber hinaus zu Problemen führen, bei denen die Verbesserung der Abfrageübersetzung für bestimmte Ausdrücke zu unbeabsichtigten Breaking Changes zwischen Releases führt.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-233">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="7a5c6-234">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-234">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-235">Wenn sich eine Abfrage nicht vollständig übersetzen lässt, habe Sie zwei Möglichkeiten: Schreiben Sie sie entweder um, oder setzen Sie alternativ `AsEnumerable()`, `ToList()` oder ähnliche Methoden ein, um Daten wieder zurück an den Client zu übertragen, wo sie mit LINQ to Objects weiterverarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-235">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="netstandard21"></a>
### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20"></a><span data-ttu-id="7a5c6-236">EF Core 3.0 zielt auf .NET Standard 2.1 und nicht auf .NET Standard 2.0 ab</span><span class="sxs-lookup"><span data-stu-id="7a5c6-236">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>

[<span data-ttu-id="7a5c6-237">Issue #15498</span><span class="sxs-lookup"><span data-stu-id="7a5c6-237">Tracking Issue #15498</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15498)

> [!IMPORTANT] 
> <span data-ttu-id="7a5c6-238">.NET Standard 2.0 kann wieder für EF Core 3.1 verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-238">EF Core 3.1 targets .NET Standard 2.0 again.</span></span> <span data-ttu-id="7a5c6-239">Dadurch wird das .NET Framework wieder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-239">This brings back support for .NET Framework.</span></span>

<span data-ttu-id="7a5c6-240">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-240">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-241">Vor 3.0 zielte EF Core auf .NET Standard 2.0 ab und wurde auf allen Plattformen ausgeführt, die diesen Standard unterstützen, einschließlich .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-241">Before 3.0, EF Core targeted .NET Standard 2.0 and would run on all platforms that support that standard, including .NET Framework.</span></span>

<span data-ttu-id="7a5c6-242">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-242">**New behavior**</span></span>

<span data-ttu-id="7a5c6-243">Ab 3.0 zielt EF Core auf .NET Standard 2.1 ab und wird auf allen Plattformen ausgeführt, die diesen Standard unterstützen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-243">Starting with 3.0, EF Core targets .NET Standard 2.1 and will run on all platforms that support this standard.</span></span> <span data-ttu-id="7a5c6-244">Dies schließt .NET Framework nicht ein.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-244">This does not include .NET Framework.</span></span>

<span data-ttu-id="7a5c6-245">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-245">**Why**</span></span>

<span data-ttu-id="7a5c6-246">Dies ist ein Teil der strategischen Entscheidung bezüglich .NET-Technologien, um den Fokus auf .NET Core und andere moderne .NET-Plattformen (z. B. Xamarin) zu legen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-246">This is part of a strategic decision across .NET technologies to focus energy on .NET Core and other modern .NET platforms, such as Xamarin.</span></span>

<span data-ttu-id="7a5c6-247">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-247">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-248">Verwenden von EF Core 3.1</span><span class="sxs-lookup"><span data-stu-id="7a5c6-248">Use EF Core 3.1.</span></span>

<a name="no-longer"></a>
### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="7a5c6-249">Entity Framework Core ist nicht mehr Teil des gemeinsam verwendeten ASP.NET Core-Frameworks</span><span class="sxs-lookup"><span data-stu-id="7a5c6-249">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="7a5c6-250">Issueankündigungen #325</span><span class="sxs-lookup"><span data-stu-id="7a5c6-250">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="7a5c6-251">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-251">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-252">Vor Version 3.0 von ASP.NET Core wurden EF Core und einige der zugehörigen Datenanbieter wie SQL Server eingeschlossen, wenn `Microsoft.AspNetCore.App` oder `Microsoft.AspNetCore.All` ein Paketverweis hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-252">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="7a5c6-253">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-253">**New behavior**</span></span>

<span data-ttu-id="7a5c6-254">Ab Version 3.0 enthält das gemeinsam verwendete ASP.NET Core-Framework weder EF Core noch zugehörige Datenanbieter.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-254">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="7a5c6-255">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-255">**Why**</span></span>

<span data-ttu-id="7a5c6-256">Vor dieser Änderung konnte EF Core auf unterschiedliche Arten bezogen werden. Diese richteten sich danach, ob ASP.NET Core und SQL Server oder ein anderes Zielframework für die Anwendung vorgesehen war.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-256">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="7a5c6-257">Bei einem Upgrade von ASP.NET Core wurde zudem ein Upgrade von EF Core und des SQL Server-Anbieters erzwungen, was nicht in allen Fällen gewünscht war.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-257">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="7a5c6-258">Durch die eingeführte Änderung wird EF Core unter allen Anbietern, unterstützten .NET-Implementierungen und Anwendungstypen auf dieselbe Weise bezogen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-258">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="7a5c6-259">Entwickler können nun außerdem genau festlegen, wann für EF Core und zugehörige Datenanbieter ein Upgrade durchgeführt werden soll.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-259">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="7a5c6-260">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-260">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-261">Wenn Sie EF Core in einer Anwendung unter ASP.NET Core 3.0 oder in einer anderen unterstützten Anwendung verwenden möchten, müssen Sie dem EF Core-Datenbankanbieter, der für die Anwendung genutzt werden soll, explizit einen Paketverweis hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-261">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>
### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="7a5c6-262">Das EF Core-Befehlszeilentool (dotnet ef) ist nicht länger Bestandteil des .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-262">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="7a5c6-263">Issue #14016</span><span class="sxs-lookup"><span data-stu-id="7a5c6-263">Tracking Issue #14016</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

<span data-ttu-id="7a5c6-264">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-264">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-265">Vor Version 3.0 war das `dotnet ef`-Tool im .NET Core SDK enthalten und konnte von der Befehlszeile aus ohne zusätzliche Schritte in einem beliebigen Projekt verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-265">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span> 

<span data-ttu-id="7a5c6-266">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-266">**New behavior**</span></span>

<span data-ttu-id="7a5c6-267">Ab Version 3.0 ist das `dotnet ef`-Tool nicht mehr im .NET SDK enthalten und muss deshalb vor der Verwendung explizit als lokales oder globales Tool installiert werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-267">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span> 

<span data-ttu-id="7a5c6-268">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-268">**Why**</span></span>

<span data-ttu-id="7a5c6-269">Durch diese Änderung kann `dotnet ef` als reguläres .NET-CLI-Tool für NuGet verteilt und aktualisiert werden und entspricht damit dem üblichen Verfahren, dass EF Core 3.0 ebenfalls immer als NuGet-Paket verteilt wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-269">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

<span data-ttu-id="7a5c6-270">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-270">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-271">Um Migrationen zu verwalten oder ein Gerüst für `DbContext` zu erstellen, installieren Sie `dotnet-ef` als globales Tool:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-271">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

  ``` console
    $ dotnet tool install --global dotnet-ef
  ```

<span data-ttu-id="7a5c6-272">Eine Verwendung als lokales Tool ist ebenfalls möglich, wenn Sie die Abhängigkeiten eines Projekts wiederherstellen, das das Tool mithilfe einer [Toolmanifestdatei](https://github.com/dotnet/cli/issues/10288) als Toolabhängigkeit deklariert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-272">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](https://github.com/dotnet/cli/issues/10288).</span></span>

<a name="fromsql"></a>
### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="7a5c6-273">FromSql, ExecuteSql und ExecuteSqlAsync wurden umbenannt</span><span class="sxs-lookup"><span data-stu-id="7a5c6-273">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="7a5c6-274">Issue #10996</span><span class="sxs-lookup"><span data-stu-id="7a5c6-274">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="7a5c6-275">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-275">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-276">Vor Version 3.0 wurden in EF Core diese Methodennamen überladen, um entweder mit einer normalen Zeichenfolge oder einer Zeichenfolge, die in SQL und Parameter interpoliert werden sollte, zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-276">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

<span data-ttu-id="7a5c6-277">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-277">**New behavior**</span></span>

<span data-ttu-id="7a5c6-278">Ab Version 3.0 verwenden Sie in EF Core `FromSqlRaw`, `ExecuteSqlRaw` und `ExecuteSqlRawAsync`, um eine parametrisierte Abfrage zu erstellen, bei der die Parameter einzeln aus der Abfragezeichenfolge übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-278">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="7a5c6-279">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-279">For example:</span></span>

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="7a5c6-280">Verwenden Sie `FromSqlInterpolated`, `ExecuteSqlInterpolated` und `ExecuteSqlInterpolatedAsync`, um eine parametrisierte Abfrage zu erstellen, bei der die Parameter als Teil einer interpolierten Abfragezeichenfolge übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-280">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="7a5c6-281">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-281">For example:</span></span>

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="7a5c6-282">Beachten Sie, dass die beiden oben genannten Abfragen dieselbe parametrisierte SQL mit denselben SQL-Parametern erzeugen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-282">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

<span data-ttu-id="7a5c6-283">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-283">**Why**</span></span>

<span data-ttu-id="7a5c6-284">Bei Methodenüberladungen wie dieser wird sehr leicht versehentlich die Methode für unformatierte Zeichenfolgen aufgerufen, wenn eigentlich beabsichtigt war, die Methode für interpolierte Zeichenfolgen aufzurufen (und umgekehrt).</span><span class="sxs-lookup"><span data-stu-id="7a5c6-284">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="7a5c6-285">Dadurch werden Abfragen möglicherweise nicht parametrisiert, obwohl dies der Fall sein sollte.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-285">This could result in queries not being parameterized when they should have been.</span></span>

<span data-ttu-id="7a5c6-286">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-286">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-287">Verwenden Sie ab sofort die neuen Methodennamen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-287">Switch to use the new method names.</span></span>

<a name="fromsqlsproc"></a>
### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a><span data-ttu-id="7a5c6-288">FromSql-Methode kann nicht zusammengesetzt werden, wenn sie mit einer gespeicherten Prozedur verwendet wird</span><span class="sxs-lookup"><span data-stu-id="7a5c6-288">FromSql method when used with stored procedure cannot be composed</span></span>

[<span data-ttu-id="7a5c6-289">Issue #15392</span><span class="sxs-lookup"><span data-stu-id="7a5c6-289">Tracking Issue #15392</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15392)

<span data-ttu-id="7a5c6-290">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-290">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-291">Vor EF Core 3.0 versuchte die FromSql-Methode, zu ermitteln, ob das übergebene SQL zusammengesetzt werden konnte.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-291">Before EF Core 3.0, FromSql method tried to detect if the passed SQL can be composed upon.</span></span> <span data-ttu-id="7a5c6-292">Eine Clientauswertung wurde durchgeführt, wenn das SQL wie eine gespeicherte Prozedur nicht zusammensetzbar war.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-292">It did client evaluation when the SQL was non-composable like a stored procedure.</span></span> <span data-ttu-id="7a5c6-293">Die folgende Abfrage funktionierte, indem die gespeicherte Prozedur auf dem Server und die FirstOrDefault-Methode auf dem Client ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-293">The following query worked by running the stored procedure on the server and doing FirstOrDefault on the client side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

<span data-ttu-id="7a5c6-294">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-294">**New behavior**</span></span>

<span data-ttu-id="7a5c6-295">Ab EF Core 3.0 versucht EF Core nicht mehr, das SQL zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-295">Starting with EF Core 3.0, EF Core will not try to parse the SQL.</span></span> <span data-ttu-id="7a5c6-296">Wenn Sie also nach FromSqlRaw/FromSqlInterpolated zusammensetzen, setzt EF Core das SQL zusammen, indem eine Unterabfrage ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-296">So if you are composing after FromSqlRaw/FromSqlInterpolated, then EF Core will compose the SQL by causing sub query.</span></span> <span data-ttu-id="7a5c6-297">Wenn Sie eine gespeicherte Prozedur mit Zusammensetzung verwenden, wird eine Ausnahme für ungültige SQL-Syntax ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-297">So if you are using a stored procedure with composition then you will get an exception for invalid SQL syntax.</span></span>

<span data-ttu-id="7a5c6-298">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-298">**Why**</span></span>

<span data-ttu-id="7a5c6-299">EF Core 3.0 unterstützt die automatische Clientauswertung nicht, da diese wie [hier](#linq-queries-are-no-longer-evaluated-on-the-client) erläutert fehleranfällig war.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-299">EF Core 3.0 does not support automatic client evaluation, since it was error prone as explained [here](#linq-queries-are-no-longer-evaluated-on-the-client).</span></span>

<span data-ttu-id="7a5c6-300">**Abhilfe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-300">**Mitigation**</span></span>

<span data-ttu-id="7a5c6-301">Wenn Sie eine gespeicherte Prozedur in FromSqlRaw/FromSqlInterpolated verwenden, wissen Sie, dass diese nicht zusammengesetzt werden kann. Daher können Sie __AsEnumerable/AsAsyncEnumerable__ direkt nach dem FromSql-Methodenaufruf hinzufügen, um die Zusammensetzung auf dem Server zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-301">If you are using a stored procedure in FromSqlRaw/FromSqlInterpolated, you know that it cannot be composed upon, so you can add __AsEnumerable/AsAsyncEnumerable__ right after the FromSql method call to avoid any composition on server side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="7a5c6-302">FromSql-Methoden können nur für die Stammelemente der Abfrage angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-302">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="7a5c6-303">Issue #15704</span><span class="sxs-lookup"><span data-stu-id="7a5c6-303">Tracking Issue #15704</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

<span data-ttu-id="7a5c6-304">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-304">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-305">Vor EF Core 3.0 konnte die `FromSql`-Methode an einer beliebigen Stelle in der Abfrage angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-305">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

<span data-ttu-id="7a5c6-306">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-306">**New behavior**</span></span>

<span data-ttu-id="7a5c6-307">Ab EF Core 3.0 können die neuen Methoden `FromSqlRaw` und `FromSqlInterpolated` (durch die `FromSql` ersetzt wird) nur für Stammelemente von Abfragen angegeben werden, d.h. direkt für das `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-307">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="7a5c6-308">Der Versuch, sie an anderer Stelle anzugeben, führt zu einem Kompilierungsfehler.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-308">Attempting to specify them anywhere else will result in a compilation error.</span></span>

<span data-ttu-id="7a5c6-309">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-309">**Why**</span></span>

<span data-ttu-id="7a5c6-310">Die Angabe von `FromSql` an einer anderen Stelle als für ein `DbSet` erbrachte keine zusätzliche Bedeutung und keinen Mehrwert, sondern konnte in bestimmten Szenarien zu Mehrdeutigkeiten führen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-310">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

<span data-ttu-id="7a5c6-311">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-311">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-312">`FromSql` Aufrufe sollten so verschoben, dass sie direkt für das zugehörige `DbSet` gelten.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-312">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>
### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="7a5c6-313">Abfragen ohne Nachverfolgung führen keine Identitätsauflösung mehr durch</span><span class="sxs-lookup"><span data-stu-id="7a5c6-313">No-tracking queries no longer perform identity resolution</span></span>

[<span data-ttu-id="7a5c6-314">Issue #13518</span><span class="sxs-lookup"><span data-stu-id="7a5c6-314">Tracking Issue #13518</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13518)

<span data-ttu-id="7a5c6-315">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-315">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-316">Vor EF Core 3.0 wurde dieselbe Entitätsinstanz für jedes Vorkommen einer Entität mit einem bestimmten Typ und einer bestimmten ID verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-316">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="7a5c6-317">Dies entspricht dem Verhalten von Abfragen zu Nachverfolgungen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-317">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="7a5c6-318">Diese Abfrage zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-318">For example, this query:</span></span>

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```
<span data-ttu-id="7a5c6-319">gibt dieselbe `Category`-Instanz für jedes `Product` zurück, das der bestimmten Kategorie zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-319">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

<span data-ttu-id="7a5c6-320">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-320">**New behavior**</span></span>

<span data-ttu-id="7a5c6-321">Ab EF Core 3.0 werden unterschiedliche Entitätsinstanzen erstellt, wenn eine Entität mit einem bestimmten Typ und einer bestimmten ID an verschiedenen Stellen im zurückgegebenen Diagramm gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-321">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="7a5c6-322">Die Abfrage oben gibt beispielsweise nun eine neue `Category`-Instanz für jedes `Product` zurück, auch wenn zwei Produkte derselben Kategorie zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-322">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

<span data-ttu-id="7a5c6-323">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-323">**Why**</span></span>

<span data-ttu-id="7a5c6-324">Die Identitätsauflösung (d. h. Feststellen, dass eine Entität über denselben Typ und die dieselbe ID wie die zuvor aufgetretene Entität verfügt) fügt zusätzlichen Aufwand für Leistung und Arbeitsspeicher hinzu.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-324">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="7a5c6-325">Dies widerspricht normalerweise dem Grund, warum in erster Linie Abfragen ohne Nachverfolgung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-325">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="7a5c6-326">Obwohl die Identitätsauflösung manchmal nützlich sein kann, wird sie nicht benötigt, wenn die Entitäten serialisiert und an den Client gesendet werden, was manchmal für Abfragen ohne Nachverfolgung der Fall ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-326">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

<span data-ttu-id="7a5c6-327">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-327">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-328">Verwenden Sie eine Nachverfolgungsabfrage, falls die Auflösung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-328">Use a tracking query if identity resolution is required.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="7a5c6-329">~~Die Abfrageausführung wird auf Debugebene protokolliert~~ – zurückgesetzt</span><span class="sxs-lookup"><span data-stu-id="7a5c6-329">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="7a5c6-330">Issue #14523</span><span class="sxs-lookup"><span data-stu-id="7a5c6-330">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="7a5c6-331">Wir haben diese Änderung zurückgesetzt, weil die neue Konfiguration in EF Core 3.0 die Angabe des Protokolliergrads für jedes Ereignis durch die Anwendung ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-331">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="7a5c6-332">Um z.B. die Protokollierung von SQL zu `Debug` zu ändern, konfigurieren Sie den Grad explizit in `OnConfiguring` oder `AddDbContext`:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-332">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>
```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="7a5c6-333">Temporäre Schlüsselwerte werden nicht mehr Entitätsinstanzen zugewiesen</span><span class="sxs-lookup"><span data-stu-id="7a5c6-333">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="7a5c6-334">Issue #12378</span><span class="sxs-lookup"><span data-stu-id="7a5c6-334">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="7a5c6-335">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-335">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-336">Vor Version 3.0 wurden in EF Core allen Schlüsseleigenschaften temporäre Werte zugewiesen. Später wurden den Eigenschaften dann die tatsächlichen Werte zugewiesen, die von der Datenbank generiert wurden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-336">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="7a5c6-337">Die temporären Werte waren in der Regel große negative Zahlen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-337">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="7a5c6-338">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-338">**New behavior**</span></span>

<span data-ttu-id="7a5c6-339">Ab Version 3.0 wird in EF Core der temporäre Wert als Teil der Überwachungsinformationen der Entität gespeichert. Die Schlüsseleigenschaft selbst bleibt unverändert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-339">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="7a5c6-340">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-340">**Why**</span></span>

<span data-ttu-id="7a5c6-341">Diese Änderung wurde vorgenommen, damit temporäre Schlüsselwerte nicht fälschlicherweise dauerhaft gespeichert werden, wenn eine Entität, die vorher von einer `DbContext`-Instanz überwacht wurde, in eine andere `DbContext`-Instanz verschoben wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-341">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="7a5c6-342">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-342">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-343">Anwendungen, die Primärschlüsselwerte Fremdschlüsseln zuweisen, um Zuordnungen zwischen Entitäten zu erstellen, können vom alten Verhalten abhängig sein, wenn Primärschlüssel vom Speicher generiert werden und zu Entitäten im `Added`-Zustand gehören.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-343">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="7a5c6-344">Sie können dies wie folgt vermeiden:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-344">This can be avoided by:</span></span>
* <span data-ttu-id="7a5c6-345">Verwenden Sie keine vom Speicher generierten Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-345">Not using store-generated keys.</span></span>
* <span data-ttu-id="7a5c6-346">Legen Sie zum Erstellen von Zuordnungen keine Fremdschlüsselwerte, sondern Navigationseigenschaften fest.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-346">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="7a5c6-347">Rufen Sie die tatsächlichen temporären Schlüsselwerte aus den Überwachungsinformationen der Entität ab.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-347">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="7a5c6-348">`context.Entry(blog).Property(e => e.Id).CurrentValue` gibt beispielsweise den temporären Wert zurück, obwohl `blog.Id` noch nicht festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-348">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="7a5c6-349">DetectChanges berücksichtigt vom Speicher generierte Schlüsselwerte</span><span class="sxs-lookup"><span data-stu-id="7a5c6-349">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="7a5c6-350">Issue #14616</span><span class="sxs-lookup"><span data-stu-id="7a5c6-350">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="7a5c6-351">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-351">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-352">Vor Version 3.0 wurde in EF Core eine nicht überwachte Entität, die von `DetectChanges` gefunden wurde, im `Added`-Zustand überwacht und als neue Zeile eingefügt, sobald `SaveChanges` aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-352">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="7a5c6-353">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-353">**New behavior**</span></span>

<span data-ttu-id="7a5c6-354">Ab Version 3.0 wird in EF Core die Entität im `Modified`-Zustand überwacht, wenn für diese generierte Schlüsselwerte verwendet werden und ein Schlüsselwert festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-354">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="7a5c6-355">Es wird also davon ausgegangen, dass eine Zeile für die Entität vorhanden ist. Wenn `SaveChanges` aufgerufen wird, wird die Zeile außerdem aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-355">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="7a5c6-356">Falls kein Schlüsselwert festgelegt ist oder der Entitätstyp keine generierten Schlüssel verwendet, wird die neue Entität wie in früheren Version weiterhin im `Added`-Zustand überwacht.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-356">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="7a5c6-357">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-357">**Why**</span></span>

<span data-ttu-id="7a5c6-358">Diese Änderung wurde vorgenommen, um bei der Verwendung von speichergenerierten Schlüsseln die Arbeit mit nicht verbundenen Entitätsgraphen einfacher und konsistenter zu gestalten.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-358">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="7a5c6-359">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-359">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-360">Diese Änderung kann dazu führen, dass eine Anwendung nicht mehr funktioniert. Dieser Fall tritt ein, wenn für Entitätstypen generierte Schlüssel vorgesehen sind, dann jedoch explizit Schlüsselwerte für neue Instanzen festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-360">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="7a5c6-361">Sie können dieses Problem vermeiden, indem Sie explizit festlegen, dass für Schlüsseleigenschaften keine generierten Werte verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-361">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="7a5c6-362">Dies ist beispielsweise mit der Fluent-API möglich:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-362">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="7a5c6-363">Alternativ bieten sich auch Datenanmerkungen an:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-363">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```
<a name="cascade"></a>
### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="7a5c6-364">Kaskadierende Deletes werden standardmäßig sofort ausgeführt</span><span class="sxs-lookup"><span data-stu-id="7a5c6-364">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="7a5c6-365">Issue #10114</span><span class="sxs-lookup"><span data-stu-id="7a5c6-365">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="7a5c6-366">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-366">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-367">Vor Version 3.0 wurden in EF Core kaskadierenden Aktionen (Löschen von abhängigen Entitäten nach dem Löschen eines erforderlichen Prinzipals oder nach dem Aufheben einer Beziehung zum erforderlichen Prinzipal) erst ausgeführt, wenn SaveChanges aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-367">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="7a5c6-368">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-368">**New behavior**</span></span>

<span data-ttu-id="7a5c6-369">Ab Version 3.0 werden in EF Core kaskadierende Aktionen angewendet, sobald die auslösende Bedingung erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-369">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="7a5c6-370">Wenn beispielsweise `context.Remove()` zum Löschen einer Prinzipalentität aufgerufen wird, führt das dazu, dass auch alle zugehörigen erforderlichen Entitäten, die überwacht werden, sofort auf `Deleted` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-370">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="7a5c6-371">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-371">**Why**</span></span>

<span data-ttu-id="7a5c6-372">Diese Änderung wurde vorgenommen, um die Arbeit mit Datenbindungen und Überwachungsszenarios zu vereinfachen, in denen bekannt sein muss, welche Entitäten _vor dem Aufruf von_  `SaveChanges` gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-372">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="7a5c6-373">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-373">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-374">Sie können das vorherige Verhalten wiederherstellen, indem Sie für `context.ChangeTracker` die entsprechenden Einstellungen vornehmen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-374">The previous behavior can be restored through settings on `context.ChangeTracker`.</span></span>
<span data-ttu-id="7a5c6-375">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-375">For example:</span></span>

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```
<a name="eager-loading-single-query"></a>
### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a><span data-ttu-id="7a5c6-376">Eager Loading von verwandten Entitäten erfolgt nun in einer einzelnen Abfrage</span><span class="sxs-lookup"><span data-stu-id="7a5c6-376">Eager loading of related entities now happens in a single query</span></span>

[<span data-ttu-id="7a5c6-377">Issue #18022</span><span class="sxs-lookup"><span data-stu-id="7a5c6-377">Tracking issue #18022</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18022)

<span data-ttu-id="7a5c6-378">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-378">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-379">Vor Version 3.0 löste Eager Loading von Sammlungsnavigationen über `Include`-Operatoren die Generierung mehrerer Abfragen auf der relationalen Datenbank aus. Eine Abfrage für jeden verwandten Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-379">Before 3.0, eagerly loading collection navigations via `Include` operators caused multiple queries to be generated on relational database, one for each related entity type.</span></span>

<span data-ttu-id="7a5c6-380">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-380">**New behavior**</span></span>

<span data-ttu-id="7a5c6-381">Ab Version 3.0 generiert EF Core eine einzelne Abfrage mit JOINs für relationale Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-381">Starting with 3.0, EF Core generates a single query with JOINs on relational databases.</span></span>

<span data-ttu-id="7a5c6-382">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-382">**Why**</span></span>

<span data-ttu-id="7a5c6-383">Das Ausgeben mehrerer Abfragen zum Implementieren einer einzelnen LINQ-Abfrage führte zu einer Vielzahl von Problemen, darunter Leistungsbeeinträchtigungen, da mehrere Datenbankroundtrips erforderlich waren, und Datenkohärenzprobleme, da jede Abfrage einen anderen Zustand der Datenbank beobachten könnte.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-383">Issuing multiple queries to implement a single LINQ query caused numerous issues, including negative performance as multiple database roundtrips were necessary, and data coherency issues as each query could observe a different state of the database.</span></span>

<span data-ttu-id="7a5c6-384">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-384">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-385">Zwar ist dies technisch gesehen kein Breaking Change, jedoch könnte es erhebliche Auswirkungen auf die Leistung der Anwendung haben, wenn eine einzelne Abfrage eine große Anzahl von `Include`-Operatoren für Sammlungsnavigationen enthält.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-385">While technically this is not a breaking change, it could have a considerable effect on application performance when a single query contains a large number of `Include` operator on collection navigations.</span></span> <span data-ttu-id="7a5c6-386">Weitere Informationen sowie Informationen zum Umschreiben von Abfragen auf eine effizientere Weise finden Sie [in diesem Kommentar](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085).</span><span class="sxs-lookup"><span data-stu-id="7a5c6-386">[See this comment](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) for more information and for rewriting queries in a more efficient way.</span></span>

**

<a name="deletebehavior"></a>
### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="7a5c6-387">DeleteBehavior.Restrict verfügt über eine übersichtlichere Semantik</span><span class="sxs-lookup"><span data-stu-id="7a5c6-387">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="7a5c6-388">Issue #12661</span><span class="sxs-lookup"><span data-stu-id="7a5c6-388">Tracking Issue #12661</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

<span data-ttu-id="7a5c6-389">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-389">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-390">Vor Version 3.0 wurden mit `DeleteBehavior.Restrict` Fremdschlüssel in der Datenbank mit `Restrict`-Semantik erstellt, es wurden aber auch unvorhergesehen interne Fixups geändert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-390">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

<span data-ttu-id="7a5c6-391">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-391">**New behavior**</span></span>

<span data-ttu-id="7a5c6-392">Ab Version 3.0 wird mit `DeleteBehavior.Restrict` sichergestellt, dass Fremdschlüssel mit `Restrict`-Semantik erstellt werden – d. h. ohne Überlappungen; ausgenommen bei Einschränkungsverletzungen – und ohne Auswirkungen auf EF-interne Fixups.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-392">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

<span data-ttu-id="7a5c6-393">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-393">**Why**</span></span>

<span data-ttu-id="7a5c6-394">Diese Änderung wurde vorgenommen, um die Benutzerfreundlichkeit bei intuitiver Verwendung von `DeleteBehavior` ohne unerwartete Nebeneffekte zu verbessern.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-394">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

<span data-ttu-id="7a5c6-395">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-395">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-396">Sie können das vorherige Verhalten wiederherstellen, indem Sie `DeleteBehavior.ClientNoAction` verwenden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-396">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>
### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="7a5c6-397">Abfragetypen werden mit Entitätstypen zusammengeführt</span><span class="sxs-lookup"><span data-stu-id="7a5c6-397">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="7a5c6-398">Issue #14194</span><span class="sxs-lookup"><span data-stu-id="7a5c6-398">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="7a5c6-399">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-399">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-400">Vor Version 3.0 wurden in EF Core [Abfragetypen](xref:core/modeling/keyless-entity-types) dazu verwendet, Daten abzufragen, in denen kein Primärschlüssel auf strukturierte Weise festgelegt war.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-400">Before EF Core 3.0, [query types](xref:core/modeling/keyless-entity-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="7a5c6-401">Abfragetypen wurden also für die Zuordnung von Entitätstypen ohne Schlüssel verwendet (in der Regel in einer Sicht, in einigen Fällen aber auch in einer Tabelle), während reguläre Entitätstypen für einen verfügbaren Schlüssel verwendet wurden (in der Regel in einer Tabelle, in einigen Fällen aber auch in einer Sicht).</span><span class="sxs-lookup"><span data-stu-id="7a5c6-401">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="7a5c6-402">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-402">**New behavior**</span></span>

<span data-ttu-id="7a5c6-403">Aus einem Abfragetyp wird nun einfach ein Entitätstyp ohne Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-403">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="7a5c6-404">Entitätstypen ohne Schlüssel besitzen die gleiche Funktionalität wie Abfragetypen in früheren Versionen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-404">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="7a5c6-405">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-405">**Why**</span></span>

<span data-ttu-id="7a5c6-406">Diese Änderung wurde vorgenommen, um Unklarheiten bei der Verwendung von Abfragetypen zu beseitigen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-406">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="7a5c6-407">Abfragetypen sind schlüssellose Entitätstypen, die grundsätzlich schreibgeschützt sind. Sie sollten allerdings nicht allein wegen des Schreibschutzes verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-407">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="7a5c6-408">Des Weiteren werden sie oft Sichten zugeordnet, was aber nur daran liegt, dass für Letztere häufig keine Schlüssel definiert werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-408">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="7a5c6-409">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-409">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-410">Die folgenden Teile der API sind durch die Änderungen veraltet:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-410">The following parts of the API are now obsolete:</span></span>

* <span data-ttu-id="7a5c6-411">**`ModelBuilder.Query<>()`** : Rufen Sie stattdessen `ModelBuilder.Entity<>().HasNoKey()` auf, um einen schlüssellosen Entitätstyp festzulegen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-411">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="7a5c6-412">Dieses Verhalten wird nach wie vor nicht konventionsgemäß festgelegt, um Fehlkonfigurationen zu vermeiden, wenn ein Primärschlüssel erwartet wird, jedoch nicht mit der Konvention übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-412">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="7a5c6-413">**`DbQuery<>`** : Verwenden Sie stattdessen `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-413">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="7a5c6-414">**`DbContext.Query<>()`** : Verwenden Sie stattdessen `DbContext.Set<>()`.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-414">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>
* <span data-ttu-id="7a5c6-415">**`IQueryTypeConfiguration<TQuery>`** : Verwenden Sie stattdessen `IEntityTypeConfiguration<TEntity>`\*\*.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-415">**`IQueryTypeConfiguration<TQuery>`** - Instead `IEntityTypeConfiguration<TEntity>`\*\* should be used.</span></span>

<a name="config"></a>
### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="7a5c6-416">Die Konfigurations-API für Beziehungen abhängiger (owned) Typen wurde geändert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-416">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="7a5c6-417">[Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="7a5c6-417">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="7a5c6-418">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-418">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-419">Vor Version 3.0 wurde in EF Core die abhängige Beziehung direkt nach dem Aufruf von `OwnsOne` oder `OwnsMany` konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-419">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="7a5c6-420">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-420">**New behavior**</span></span>

<span data-ttu-id="7a5c6-421">Ab Version 3.0 von EF Core ist eine Fluent-API verfügbar, mit der über `WithOwner()` eine Navigationseigenschaft für den Besitzer konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-421">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="7a5c6-422">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-422">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="7a5c6-423">Die Konfiguration für die Beziehung zwischen Besitzertyp und abhängigem Typ sollte nun ähnlich wie bei der Konfiguration anderer Beziehungen nach `WithOwner()` verkettet werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-423">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="7a5c6-424">Die Konfiguration für den abhängigen Typ wird jedoch weiterhin nach `OwnsOne()/OwnsMany()` verkettet.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-424">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="7a5c6-425">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-425">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details, eb =>
    {
        eb.WithOwner()
            .HasForeignKey(e => e.AlternateId)
            .HasConstraintName("FK_OrderDetails");
            
        eb.ToTable("OrderDetails");
        eb.HasKey(e => e.AlternateId);
        eb.HasIndex(e => e.Id);

        eb.HasOne(e => e.Customer).WithOne();

        eb.HasData(
            new OrderDetails
            {
                AlternateId = 1,
                Id = -1
            });
    });
```

<span data-ttu-id="7a5c6-426">Wenn Sie zusätzlich `Entity()`, `HasOne()`, oder `Set()` für das Ziel eines abhängigen Typs aufrufen, wird keine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-426">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="7a5c6-427">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-427">**Why**</span></span>

<span data-ttu-id="7a5c6-428">Diese Änderung wurde vorgenommen, um eine deutlichere Trennung zwischen der Konfiguration des abhängigen Typs und der _Beziehung zum abhängigen Typ_ zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-428">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="7a5c6-429">Dadurch werden für Methoden wie `HasForeignKey` Mehrdeutigkeiten und Unklarheiten beseitigt.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-429">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="7a5c6-430">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-430">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-431">Passen Sie für die Beziehungen von abhängigen Typen die Konfiguration so an, dass die neue Fluent-API wie im obigen Beispiel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-431">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="7a5c6-432">Abhängige Entitäten, die die Tabelle gemeinsam mit dem Prinzipal verwenden, sind jetzt optional</span><span class="sxs-lookup"><span data-stu-id="7a5c6-432">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="7a5c6-433">Issue #9005</span><span class="sxs-lookup"><span data-stu-id="7a5c6-433">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="7a5c6-434">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-434">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-435">Sehen Sie sich das folgende Modell an:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-435">Consider the following model:</span></span>
```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```
<span data-ttu-id="7a5c6-436">Wenn `OrderDetails` im Besitz von `Order` ist oder explizit derselben Tabelle zugeordnet ist, war vor Version 3.0 in EF Core immer eine `OrderDetails`-Instanz beim Hinzufügen eines neuen `Order`-Objekts erforderlich.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-436">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


<span data-ttu-id="7a5c6-437">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-437">**New behavior**</span></span>

<span data-ttu-id="7a5c6-438">Ab Version 3.0 bietet EF Core die Möglichkeit, `Order` ohne `OrderDetails` hinzuzufügen, und alle `OrderDetails`-Eigenschaften, mit Ausnahme des primären Schlüssels, werden Spalten zugeordnet, die NULL-Werte zulassen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-438">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="7a5c6-439">Bei Abfragen von EF Core wird `OrderDetails` auf `null` festgelegt, wenn eine der erforderlichen Eigenschaften keinen Wert aufweist oder keine erforderlichen Eigenschaften außer dem primären Schlüssel vorhanden und alle Eigenschaften `null` sind.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-439">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

<span data-ttu-id="7a5c6-440">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-440">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-441">Wenn Ihr Modell von der gemeinsamen Nutzung einer Tabelle mit allen optionalen Spalten abhängig ist, aber die Navigation, die darauf zeigt, wahrscheinlich nicht `null` ist, sollte die Anwendung für die Handhabung von Fällen geändert werden, in denen die Navigation `null` ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-441">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="7a5c6-442">Wenn das nicht möglich ist, sollte dem Entitätstyp eine erforderliche Eigenschaft hinzugefügt werden oder mindestens einer Eigenschaft sollte ein Wert ungleich `null` zugewiesen sein.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-442">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="7a5c6-443">Alle Entitäten, die eine Tabelle mit einer Spalte für das Parallelitätstoken gemeinsam verwenden, müssen diese einer Eigenschaft zuordnen</span><span class="sxs-lookup"><span data-stu-id="7a5c6-443">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="7a5c6-444">Issue #14154</span><span class="sxs-lookup"><span data-stu-id="7a5c6-444">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="7a5c6-445">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-445">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-446">Sehen Sie sich das folgende Modell an:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-446">Consider the following model:</span></span>
```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public byte[] Version { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .Property(o => o.Version).IsRowVersion().HasColumnName("Version");
}
```
<span data-ttu-id="7a5c6-447">Wenn `OrderDetails` im Besitz von `Order` ist oder explizit derselben Tabelle zugeordnet ist, wird vor Version 3.0 in EF Core durch das alleinige Aktualisieren von `OrderDetails` der `Version`-Wert auf dem Client nicht aktualisiert, und das nächste Update schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-447">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


<span data-ttu-id="7a5c6-448">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-448">**New behavior**</span></span>

<span data-ttu-id="7a5c6-449">Ab Version 3.0 gibt EF Core den neuen `Version`-Wert an `Order` weiter, wenn dies Besitzer von `OrderDetails` ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-449">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="7a5c6-450">Andernfalls wird eine Ausnahme während der Modellvalidierung ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-450">Otherwise an exception is thrown during model validation.</span></span>

<span data-ttu-id="7a5c6-451">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-451">**Why**</span></span>

<span data-ttu-id="7a5c6-452">Diese Änderung wurde vorgenommen, um einen veralteten Wert für ein Parallelitätstoken zu vermeiden, wenn nur eine der Entitäten, die derselben Tabelle zugeordnet sind, aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-452">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="7a5c6-453">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-453">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-454">Alle Entitäten, die die Tabelle gemeinsam nutzen, müssen eine Eigenschaft enthalten, die der Spalte für das Parallelitätstoken zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-454">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="7a5c6-455">Es ist möglich, eine solche im Schattenzustand zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-455">It's possible the create one in shadow-state:</span></span>
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a><span data-ttu-id="7a5c6-456">Benutzereigene Entitäten können nicht abgefragt werden, ohne dass der Besitzer eine Nachverfolgungsabfrage verwendet</span><span class="sxs-lookup"><span data-stu-id="7a5c6-456">Owned entities cannot be queried without the owner using a tracking query</span></span>

[<span data-ttu-id="7a5c6-457">Issue 18876</span><span class="sxs-lookup"><span data-stu-id="7a5c6-457">Tracking Issue #18876</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18876)

<span data-ttu-id="7a5c6-458">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-458">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-459">Vor EF Core 3.0 konnten die benutzereigenen Entitäten wie jedes andere Navigationselement abgefragt werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-459">Before EF Core 3.0, the owned entities could be queried as any other navigation.</span></span>

```csharp
context.People.Select(p => p.Address);
```

<span data-ttu-id="7a5c6-460">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-460">**New behavior**</span></span>

<span data-ttu-id="7a5c6-461">Ab EF Core 3.0 wird eine Ausnahme ausgelöst, wenn eine Nachverfolgungsabfrage eine benutzereigene Entität ohne den Besitzer projiziert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-461">Starting with 3.0, EF Core will throw if a tracking query projects an owned entity without the owner.</span></span>

<span data-ttu-id="7a5c6-462">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-462">**Why**</span></span>

<span data-ttu-id="7a5c6-463">Benutzereigene Entitäten können ohne den Besitzer nicht bearbeitet werden, daher handelt es sich in den meisten Fällen um einen Fehler, wenn sie auf diese Weise abgefragt werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-463">Owned entities cannot be manipulated without the owner, so in the vast majority of cases querying them in this way is an error.</span></span>

<span data-ttu-id="7a5c6-464">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-464">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-465">Wenn die benutzereigene Entität nachverfolgt werden soll, damit sie später Änderungen an ihr vorgenommen werden können, sollte der Besitzer in der Abfrage enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-465">If the owned entity should be tracked to be modified in any way later then the owner should be included in the query.</span></span>

<span data-ttu-id="7a5c6-466">Fügen Sie andernfalls einen `AsNoTracking()`-Aufruf hinzu:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-466">Otherwise add an `AsNoTracking()` call:</span></span>

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="7a5c6-467">Geerbte Eigenschaften von nicht zugeordneten Typen sind nun einer einzelnen Spalte für alle abgeleiteten Typen zugeordnet</span><span class="sxs-lookup"><span data-stu-id="7a5c6-467">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="7a5c6-468">Issue #13998</span><span class="sxs-lookup"><span data-stu-id="7a5c6-468">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="7a5c6-469">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-469">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-470">Sehen Sie sich das folgende Modell an:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-470">Consider the following model:</span></span>
```csharp
public abstract class EntityBase
{
    public int Id { get; set; }
}

public abstract class OrderBase : EntityBase
{
    public int ShippingAddress { get; set; }
}

public class BulkOrder : OrderBase
{
}

public class Order : OrderBase
{
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>();
    modelBuilder.Entity<Order>();
}
```

<span data-ttu-id="7a5c6-471">Vor Version 3.0 wurde in EF Core die Eigenschaft `ShippingAddress` standardmäßig separaten Spalten für `BulkOrder` und `Order` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-471">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

<span data-ttu-id="7a5c6-472">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-472">**New behavior**</span></span>

<span data-ttu-id="7a5c6-473">Ab Version 3.0 erstellt EF Core nur eine Spalte für `ShippingAddress`.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-473">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

<span data-ttu-id="7a5c6-474">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-474">**Why**</span></span>

<span data-ttu-id="7a5c6-475">Der alte Verhalten war unerwartet.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-475">The old behavoir was unexpected.</span></span>

<span data-ttu-id="7a5c6-476">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-476">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-477">Die Eigenschaft kann noch immer explizit einer separaten Spalte für die abgeleiteten Typen zugeordnet werden:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-477">The property can still be explicitly mapped to separate column on the derived types:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>()
        .Property(o => o.ShippingAddress).HasColumnName("BulkShippingAddress");
    modelBuilder.Entity<Order>()
        .Property(o => o.ShippingAddress).HasColumnName("ShippingAddress");
}
```

<a name="fkp"></a>

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="7a5c6-478">Die Konvention zur Fremdschlüsseleigenschaft entspricht nicht mehr dem Namen der Prinzipaleigenschaft</span><span class="sxs-lookup"><span data-stu-id="7a5c6-478">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="7a5c6-479">Issue #13274</span><span class="sxs-lookup"><span data-stu-id="7a5c6-479">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="7a5c6-480">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-480">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-481">Sehen Sie sich das folgende Modell an:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-481">Consider the following model:</span></span>
```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```
<span data-ttu-id="7a5c6-482">Vor Version 3.0 wurde in EF Core gemäß der Konvention die `CustomerId`-Eigenschaft für den Fremdschlüssel verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-482">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="7a5c6-483">Wenn `Order` jedoch ein abhängiger Typ ist, wäre `CustomerId` der Primärschlüssel, was normalerweise nicht der Erwartungshaltung entspricht.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-483">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="7a5c6-484">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-484">**New behavior**</span></span>

<span data-ttu-id="7a5c6-485">Ab Version 3.0 werden in EF Core konventionsgemäß keine Eigenschaften mehr für Fremdschlüssel verwendet, wenn diese denselben Namen wie die Prinzipaleigenschaft besitzen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-485">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="7a5c6-486">Die Muster für den Namen des Prinzipaltyps, der mit dem Namen der Prinzipaleigenschaft verkettet wird, und für den Navigationsnamen, der mit dem Namen der Prinzipaleigenschaft verkettet wird, werden jedoch weiterhin abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-486">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="7a5c6-487">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-487">For example:</span></span>

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int BuyerId { get; set; }
    public Customer Buyer { get; set; }
}
```

<span data-ttu-id="7a5c6-488">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-488">**Why**</span></span>

<span data-ttu-id="7a5c6-489">Diese Änderung wurde vorgenommen, um zu vermeiden, dass versehentlich eine Primärschlüsseleigenschaft für den abhängigen Typ definiert wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-489">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="7a5c6-490">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-490">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-491">Wenn die Eigenschaft als Fremdschlüssel und daher Teil des Primärschlüssels vorgesehen war, müssen Sie diese explizit als solche festlegen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-491">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="7a5c6-492">Die Datenbankverbindung wird jetzt geschlossen, wenn sie nicht mehr verwendet wird, bevor TransactionScope abgeschlossen wurde</span><span class="sxs-lookup"><span data-stu-id="7a5c6-492">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="7a5c6-493">Issue #14218</span><span class="sxs-lookup"><span data-stu-id="7a5c6-493">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="7a5c6-494">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-494">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-495">Wenn vor Version 3.0 in EF Core der Kontext die Verbindung in einem `TransactionScope` öffnet, bleibt die Verbindung geöffnet, während der aktuelle `TransactionScope` aktiv ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-495">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        // Old behavior: Connection is still open at this point

        var categories = context.ProductCategories().ToList();
    }
}
```

<span data-ttu-id="7a5c6-496">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-496">**New behavior**</span></span>

<span data-ttu-id="7a5c6-497">Ab Version 3.0 schließt EF Core die Verbindung, sobald sie nicht mehr verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-497">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

<span data-ttu-id="7a5c6-498">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-498">**Why**</span></span>

<span data-ttu-id="7a5c6-499">Diese Änderung ermöglicht die Verwendung mehrerer Kontexte in demselben `TransactionScope`.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-499">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="7a5c6-500">Das neue Verhalten entspricht außerdem EF6.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-500">The new behavior also matches EF6.</span></span>

<span data-ttu-id="7a5c6-501">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-501">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-502">Wenn die Verbindung geöffnet bleiben muss, wird durch einen expliziten Aufruf von `OpenConnection()` sichergestellt, dass EF Core diese nicht vorzeitig schließt:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-502">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.Database.OpenConnection();
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        var categories = context.ProductCategories().ToList();
        context.Database.CloseConnection();
    }
}
```

<a name="each"></a>

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="7a5c6-503">Für jede Eigenschaft wird separat ein ganzzahliger speicherinterner Schlüssel generiert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-503">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="7a5c6-504">Issue #6872</span><span class="sxs-lookup"><span data-stu-id="7a5c6-504">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="7a5c6-505">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-505">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-506">Vor Version 3.0 wurde in EF Core ein gemeinsam verwendeter Wertegenerator für alle speicherinternen ganzzahligen Schlüsseleigenschaften verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-506">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="7a5c6-507">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-507">**New behavior**</span></span>

<span data-ttu-id="7a5c6-508">Ab Version 3.0 von EF Core wird für jede ganzzahlige Schlüsseleigenschaft ein eigener Wertegenerator bei der Verwendung der In-Memory Database verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-508">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="7a5c6-509">Wenn die Datenbank gelöscht wird, wird die Schlüsselgenerierung für alle Tabellen zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-509">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="7a5c6-510">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-510">**Why**</span></span>

<span data-ttu-id="7a5c6-511">Diese Änderung wurde vorgenommen, um die speicherinterne Schlüsselgenerierung enger mit der Schlüsselgenerierung für echte Datenbanken abzustimmen. Außerdem sollen bei Verwendung der In-Memory Database Tests leichter voneinander isoliert werden können.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-511">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="7a5c6-512">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-512">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-513">Wenn Anwendungen von bestimmten speicherinternen Schlüsselwerten abhängig sind, funktionieren Erstere durch die eingeführte Änderung möglicherweise nicht mehr.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-513">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="7a5c6-514">Versuchen Sie, Abhängigkeiten von bestimmten Schlüsselwerten zu vermeiden, oder passen Sie die Anwendung an das neue Verhalten an.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-514">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="7a5c6-515">Unterstützungsfelder werden standardmäßig verwendet</span><span class="sxs-lookup"><span data-stu-id="7a5c6-515">Backing fields are used by default</span></span>

[<span data-ttu-id="7a5c6-516">Issue #12430</span><span class="sxs-lookup"><span data-stu-id="7a5c6-516">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="7a5c6-517">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-517">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-518">Vor Version 3.0 wurde in EF Core mithilfe der Getter- und Settermethoden standardmäßig der Eigenschaftswert gelesen und geschrieben. Das war selbst dann der Fall, wenn das Unterstützungsfeld für eine Eigenschaft nicht bekannt war.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-518">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="7a5c6-519">Eine Ausnahme war die Abfrageausführung, bei der das Unterstützungsfeld direkt festgelegt wurde, wenn es bekannt war.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-519">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="7a5c6-520">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-520">**New behavior**</span></span>

<span data-ttu-id="7a5c6-521">Ab Version 3.0 wird in EF Core die Eigenschaft mithilfe des Unterstützungsfelds gelesen und geschrieben, wenn dieses für eine Eigenschaft bekannt ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-521">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="7a5c6-522">Dies kann dazu führen, dass eine Anwendung nicht mehr funktioniert, wenn sie auf zusätzliches Verhalten angewiesen ist, das im Code der Getter- und Settermethoden definiert ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-522">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="7a5c6-523">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-523">**Why**</span></span>

<span data-ttu-id="7a5c6-524">Diese Änderung wurde vorgenommen, um zu verhindern, dass in EF Core fälschlicherweise immer dann Geschäftslogik ausgelöst wird, wenn Datenbankvorgänge für Entitäten durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-524">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="7a5c6-525">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-525">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-526">Sie können das Verhalten von vor Version 3.0 wiederherstellen, indem Sie in `ModelBuilder` den Zugriffsmodus für die Eigenschaft konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-526">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="7a5c6-527">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-527">For example:</span></span>

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="7a5c6-528">Eine Ausnahme wird ausgelöst, wenn mehrere kompatible Unterstützungsfelder gefunden werden</span><span class="sxs-lookup"><span data-stu-id="7a5c6-528">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="7a5c6-529">Issue #12523</span><span class="sxs-lookup"><span data-stu-id="7a5c6-529">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="7a5c6-530">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-530">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-531">Vor Version 3.0 wurde in EF Core ein Unterstützungsfeld auf Grundlage einer Rangfolge ausgewählt, wenn die Regeln zum Suchen eines Eigenschaftsfelds auf mehrere Felder zutrafen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-531">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="7a5c6-532">Dabei konnte es vorkommen, dass in mehrdeutigen Fällen das falsche Feld verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-532">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="7a5c6-533">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-533">**New behavior**</span></span>

<span data-ttu-id="7a5c6-534">Ab Version 3.0 wird in EF Core eine Ausnahme ausgelöst, wenn sich mehrere Felder auf dieselbe Eigenschaft beziehen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-534">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="7a5c6-535">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-535">**Why**</span></span>

<span data-ttu-id="7a5c6-536">Diese Änderung wurde vorgenommen, um zu vermeiden, dass automatisch ein falsches Feld verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-536">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="7a5c6-537">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-537">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-538">Für Eigenschaften mit mehrdeutigen Unterstützungsfeldern muss das zu verwendende Feld explizit festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-538">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="7a5c6-539">Mit der Fluent-API ist dies beispielsweise wie folgt möglich:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-539">For example, using the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="7a5c6-540">„Nur-Feld“-Eigenschaftsnamen sollten dem Feldnamen entsprechen</span><span class="sxs-lookup"><span data-stu-id="7a5c6-540">Field-only property names should match the field name</span></span>

<span data-ttu-id="7a5c6-541">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-541">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-542">Vor Version 3.0 konnte in EF Core eine Eigenschaft durch einen Zeichenfolgenwert angegeben werden, und wenn keine Eigenschaft mit diesem Namen im .NET-Typ gefunden wurde, versuchte EF Core, mithilfe von Konventionsregeln ein übereinstimmendes Feld zu finden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-542">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the .NET type then EF Core would try to match it to a field using convention rules.</span></span>

```csharp
private class Blog
{
    private int _id;
    public string Name { get; set; }
}
```

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id");
```

<span data-ttu-id="7a5c6-543">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-543">**New behavior**</span></span>

<span data-ttu-id="7a5c6-544">Ab Version 3.0 muss in EF Core eine „Nur-Feld“-Eigenschaft mit dem Namen des Felds genau übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-544">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

<span data-ttu-id="7a5c6-545">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-545">**Why**</span></span>

<span data-ttu-id="7a5c6-546">Diese Änderung wurde vorgenommen, um zu vermeiden, dass das gleiche Feld für zwei Eigenschaften mit ähnlichem Namen verwendet wird. Durch die Änderung entsprechen nun auch die Übereinstimmungsregeln für „Nur-Feld“-Eigenschaften den Regeln für Eigenschaften, die CLR-Eigenschaften zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-546">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

<span data-ttu-id="7a5c6-547">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-547">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-548">„Nur-Feld“-Eigenschaften müssen so benannt werden wie das Feld, dem sie zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-548">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="7a5c6-549">In einer kommenden Version von EF Core nach 3.0 soll das explizite Konfigurieren eines Feldnamens, der sich vom Eigenschaftsnamen unterscheidet, erneut aktiviert werden (siehe Problem [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):</span><span class="sxs-lookup"><span data-stu-id="7a5c6-549">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="7a5c6-550">Von AddDbContext/AddDbContextPool werden AddLogging und AddMemoryCache nicht mehr aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-550">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="7a5c6-551">Issue #14756</span><span class="sxs-lookup"><span data-stu-id="7a5c6-551">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="7a5c6-552">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-552">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-553">Vor EF Core 3.0 wurden beim Aufruf von `AddDbContext` oder `AddDbContextPool` durch Aufrufe von [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) und [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) auch Protokollierungs- und Arbeitsspeichercachingdienste bei DI registriert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-553">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with DI through calls to [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="7a5c6-554">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-554">**New behavior**</span></span>

<span data-ttu-id="7a5c6-555">Ab EF Core 3.0 werden diese Dienste durch `AddDbContext` und `AddDbContextPool` nicht mehr bei Dependency Injection (DI) registriert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-555">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="7a5c6-556">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-556">**Why**</span></span>

<span data-ttu-id="7a5c6-557">Für EF Core 3.0 ist es nicht erforderlich, dass diese Dienste im DI-Container der Anwendung enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-557">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="7a5c6-558">Wenn `ILoggerFactory` jedoch im DI-Container der Anwendung registriert ist, wird sie nach wie vor von EF Core verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-558">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="7a5c6-559">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-559">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-560">Wenn Ihre Anwendung diese Dienste benötigt, registrieren Sie sie explizit mit [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) oder [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) beim DI-Container.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-560">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a><span data-ttu-id="7a5c6-561">AddEntityFramework\* fügt IMemoryCache mit einer Größenbeschränkung hinzu</span><span class="sxs-lookup"><span data-stu-id="7a5c6-561">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>

[<span data-ttu-id="7a5c6-562">Issue 12905</span><span class="sxs-lookup"><span data-stu-id="7a5c6-562">Tracking Issue #12905</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12905)

<span data-ttu-id="7a5c6-563">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-563">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-564">Vor EF Core 3.0 wurden bei Aufrufen von `AddEntityFramework*`-Methoden auch Arbeitsspeichercachingdienste ohne eine Größenbeschränkung bei DI registriert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-564">Before EF Core 3.0, calling `AddEntityFramework*` methods would also register memory caching services with DI without a size limit.</span></span>

<span data-ttu-id="7a5c6-565">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-565">**New behavior**</span></span>

<span data-ttu-id="7a5c6-566">Ab EF Core 3.0 registriert `AddEntityFramework*` einen IMemoryCache-Dienst mit einer Größenbeschränkung.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-566">Starting with EF Core 3.0, `AddEntityFramework*` will register an IMemoryCache service with a size limit.</span></span> <span data-ttu-id="7a5c6-567">Wenn danach weitere Dienste hinzugefügt werden, die von IMemoryCache abhängig sind, können diese schnell den Standardgrenzwert erreichen, was zu Ausnahmen und Leistungsbeeinträchtigungen führt.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-567">If any other services added afterwards depend on IMemoryCache they can quickly reach the default limit causing exceptions or degraded performance.</span></span>

<span data-ttu-id="7a5c6-568">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-568">**Why**</span></span>

<span data-ttu-id="7a5c6-569">Die Verwendung von IMemoryCache ohne Einschränkung kann zu unkontrollierter Arbeitsspeicherauslastung führen, wenn ein Fehler in die Abfragecachelogik auftritt oder wenn die Abfragen dynamisch generiert werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-569">Using IMemoryCache without a limit could result in uncontrolled memory usage if there is a bug in query caching logic or the queries are generated dynamically.</span></span> <span data-ttu-id="7a5c6-570">Durch eine Standardeinschränkung können potenzielle DoS-Angriffe verhindert werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-570">Having a default limit mitigates a potential DoS attack.</span></span>

<span data-ttu-id="7a5c6-571">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-571">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-572">In den meisten Fällen ist das Aufrufen von `AddEntityFramework*` nicht erforderlich, wenn `AddDbContext` oder `AddDbContextPool` ebenfalls aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-572">In most cases calling `AddEntityFramework*` is not necessary if `AddDbContext` or `AddDbContextPool` is called as well.</span></span> <span data-ttu-id="7a5c6-573">Daher besteht die beste Entschärfung darin, den `AddEntityFramework*`-Aufruf zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-573">Therefore, the best mitigation is to remove the `AddEntityFramework*` call.</span></span>

<span data-ttu-id="7a5c6-574">Wenn Ihre Anwendung diese Dienste erfordert, registrieren Sie im Voraus eine IMemoryCache-Implementierung explizit mit dem DI-Container mithilfe von [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="7a5c6-574">If your application needs these services, then register a IMemoryCache implementation explicitly with the DI container beforehand using [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="7a5c6-575">DbContext.Entry erkennt nun mit DetectChanges lokale Änderungen</span><span class="sxs-lookup"><span data-stu-id="7a5c6-575">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="7a5c6-576">Issue #13552</span><span class="sxs-lookup"><span data-stu-id="7a5c6-576">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="7a5c6-577">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-577">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-578">Vor Version 3.0 führte in EF Core ein Aufruf von `DbContext.Entry` dazu, dass Änderungen an allen überwachten Entitäten erkannt wurden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-578">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="7a5c6-579">Dadurch wurde sichergestellt, dass der Zustand in `EntityEntry` immer aktuell war.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-579">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="7a5c6-580">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-580">**New behavior**</span></span>

<span data-ttu-id="7a5c6-581">Ab Version 3.0 werden in EF Core Änderungen bei einem Aufruf von `DbContext.Entry` nur in der angegebenen Entität und in allen überwachten Prinzipalentitäten erkannt.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-581">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="7a5c6-582">Änderungen an anderer Stelle werden daher durch den Aufruf dieser Methode möglicherweise nicht erkannt, was sich auf den Anwendungszustand auswirken kann.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-582">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="7a5c6-583">Beachten Sie, dass selbst die Erkennung lokaler Änderungen deaktiviert wird, wenn `ChangeTracker.AutoDetectChangesEnabled` auf `false` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-583">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="7a5c6-584">Andere Methoden wie `ChangeTracker.Entries` und `SaveChanges`, die eine Änderungserkennung auslösen, führen nach wie vor zu einem vollständigen `DetectChanges`-Vorgang für alle überwachten Entitäten.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-584">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="7a5c6-585">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-585">**Why**</span></span>

<span data-ttu-id="7a5c6-586">Diese Änderung wurde vorgenommen, um die Leistung bei der standardmäßigen Verwendung von `context.Entry` zu verbessern.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-586">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="7a5c6-587">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-587">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-588">Rufen Sie `ChangeTracker.DetectChanges()` explizit vor dem Aufruf von `Entry` auf, wenn das Verhalten vor Version 3.0 beibehalten werden soll.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-588">Call `ChangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="7a5c6-589">Schlüssel aus Zeichenfolgen und Bytearrays werden standardmäßig nicht vom Client generiert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-589">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="7a5c6-590">Issue #14617</span><span class="sxs-lookup"><span data-stu-id="7a5c6-590">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="7a5c6-591">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-591">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-592">Vor Version 3.0 konnten in EF Core `string`- und `byte[]`-Schlüsseleigenschaften verwendet werden, ohne dass explizit ein anderer Wert als NULL festgelegt werden musste.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-592">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="7a5c6-593">In diesem Fall wurde der Schlüsselwert auf dem Client als GUID generiert und für `byte[]` in Bytes serialisiert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-593">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="7a5c6-594">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-594">**New behavior**</span></span>

<span data-ttu-id="7a5c6-595">Ab Version 3.0 wird in EF Core eine Ausnahme ausgelöst, wenn kein Schlüsselwert festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-595">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="7a5c6-596">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-596">**Why**</span></span>

<span data-ttu-id="7a5c6-597">Diese Änderung wurde vorgenommen, da vom Client generierte `string`/`byte[]`-Werte in der Regel nicht sinnvoll sind. Das Standardverhalten führte außerdem zu kaum nachvollziehbaren generierten Schlüsselwerten.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-597">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="7a5c6-598">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-598">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-599">Wenn Sie das Verhalten vor Version 3.0 nutzen möchten, müssen Sie explizit festlegen, dass für die Schlüsseleigenschaften generierte Werte verwendet sollen, wenn kein anderer Nicht-NULL-Wert festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-599">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="7a5c6-600">Dies ist beispielsweise mit der Fluent-API möglich:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-600">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="7a5c6-601">Alternativ bieten sich auch Datenanmerkungen an:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-601">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="7a5c6-602">ILoggerFactory ist nun ein bereichsbezogener Dienst</span><span class="sxs-lookup"><span data-stu-id="7a5c6-602">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="7a5c6-603">Issue #14698</span><span class="sxs-lookup"><span data-stu-id="7a5c6-603">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="7a5c6-604">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-604">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-605">Vor Version 3.0 wurde `ILoggerFactory` in EF Core als Singletondienst registriert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-605">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="7a5c6-606">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-606">**New behavior**</span></span>

<span data-ttu-id="7a5c6-607">Ab Version 3.0 wird in EF Core `ILoggerFactory` als bereichsbezogener Dienst registriert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-607">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="7a5c6-608">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-608">**Why**</span></span>

<span data-ttu-id="7a5c6-609">Diese Änderung wurde vorgenommen, um einer `DbContext`-Instanz eine Protokollierung zuordnen zu können. Dadurch werden weitere Funktionen bereitgestellt. In einigen Fällen wird außerdem schädliches Verhalten wie etwa die schnelle Zunahme von internen Dienstanbietern beseitigt.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-609">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="7a5c6-610">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-610">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-611">Diese Änderung wirkt sich nur dann auf den Anwendungscode aus, wenn benutzerdefinierte Dienste auf dem internen Dienstanbieter von EF Core registriert und verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-611">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="7a5c6-612">Dies ist nicht üblich.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-612">This isn't common.</span></span>
<span data-ttu-id="7a5c6-613">In einem derartigen Fall funktioniert fast alles wie vorher. Singletondienste, die von `ILoggerFactory` abhängig sind, müssen jedoch so angepasst werden, dass `ILoggerFactory` auf andere Weise abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-613">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="7a5c6-614">Erstellen Sie in diesen Situationen bitte im [GitHub-Issuetracker für EF Core](https://github.com/aspnet/EntityFrameworkCore/issues) ein Issue, in dem Sie beschreiben, wie Sie `ILoggerFactory` verwenden. So können wir in Zukunft erneute Breaking Changes vermeiden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-614">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="7a5c6-615">Für Proxys mit Lazy Loading (trägem Laden) gilt nicht mehr die Annahme, dass Navigationseigenschaften vollständig geladen sind</span><span class="sxs-lookup"><span data-stu-id="7a5c6-615">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="7a5c6-616">Issue #12780</span><span class="sxs-lookup"><span data-stu-id="7a5c6-616">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="7a5c6-617">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-617">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-618">Vor Version 3.0 gab es in EF Core nach der Freigabe eines `DbContext`-Objekts keine Möglichkeit, zu ermitteln, ob eine aus dem Kontext abgerufene Navigationseigenschaft für eine Entität vollständig geladen war.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-618">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="7a5c6-619">Stattdessen galt für Proxys die Annahme, dass eine Verweisnavigation geladen ist, falls für diese ein anderer Wert als NULL vorliegt. Außerdem wurde davon ausgegangen, dass eine Sammlungsnavigation geladen ist, wenn sie nicht leer ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-619">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="7a5c6-620">In diesen Fällen entsprach das Lazy Loading einer Nulloperation („No-Op“).</span><span class="sxs-lookup"><span data-stu-id="7a5c6-620">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="7a5c6-621">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-621">**New behavior**</span></span>

<span data-ttu-id="7a5c6-622">Ab Version 3.0 überwachen in EF Core Proxys, ob eine Navigationseigenschaft geladen ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-622">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="7a5c6-623">Wenn also auf eine Navigationseigenschaft zugegriffen werden soll, die nach der Freigabe des Kontexts geladen wird, ist dies immer eine No-Op-Operation. Das ist selbst dann der Fall, wenn die geladene Navigation leer oder NULL ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-623">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="7a5c6-624">Wenn umgekehrt auf eine nicht geladene Navigationseigenschaft zugegriffen werden soll und der Kontext bereits freigegeben wurde, wird eine Ausnahme ausgelöst. Dieser Fall tritt auch dann ein, wenn die Navigationseigenschaft eine nicht leere Sammlung ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-624">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="7a5c6-625">In dieser Situation wird also vom Anwendungscode versucht, zu einem ungültigen Zeitpunkt Lazy Loading durchzuführen. Die Anwendung sollte so angepasst werden, dass dieser Vorgang vermieden wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-625">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="7a5c6-626">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-626">**Why**</span></span>

<span data-ttu-id="7a5c6-627">Diese Änderung wurde vorgenommen, um das Verhalten beim Lazy Loading einer freigegebenen `DbContext`-Instanz konsistent und korrekt zu gestalten.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-627">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="7a5c6-628">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-628">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-629">Aktualisieren Sie den Anwendungscode so, dass kein Lazy Loading durchgeführt wird, nachdem der Kontext freigegeben wurde. Alternativ können Sie auch, wie in der Ausnahmemeldung beschrieben wird, eine No-Op-Operation festlegen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-629">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="7a5c6-630">Die Erstellung zu vieler interner Dienstanbieter führt standardmäßig zu einem Fehler</span><span class="sxs-lookup"><span data-stu-id="7a5c6-630">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="7a5c6-631">Issue #10236</span><span class="sxs-lookup"><span data-stu-id="7a5c6-631">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="7a5c6-632">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-632">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-633">Vor Version 3.0 wurde in EF Core eine Warnung für eine Anwendung protokolliert, die unverhältnismäßig viele interne Dienstanbieter erstellte.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-633">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="7a5c6-634">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-634">**New behavior**</span></span>

<span data-ttu-id="7a5c6-635">Ab Version 3.0 wird diese Warnung in EF Core als Fehler betrachtet, und eine Ausnahme wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-635">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="7a5c6-636">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-636">**Why**</span></span>

<span data-ttu-id="7a5c6-637">Diese Änderung wurde vorgenommen, damit auf den oben beschriebenen Fall explizit hingewiesen wird. So soll die Entwicklung von besserem Anwendungscode erleichtert werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-637">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="7a5c6-638">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-638">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-639">Wenn dieser Fehler auftritt, müssen Sie die Grundursache ermitteln und die Erstellung vieler interner Dienstanbieter unterbinden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-639">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="7a5c6-640">Sie können den Fehler allerdings auch wieder in eine Warnung konvertierten (oder ihn ignorieren), indem Sie `DbContextOptionsBuilder` konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-640">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="7a5c6-641">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-641">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="7a5c6-642">Neues Verhalten für HasOne/HasMany bei Aufruf mit einer einzelnen Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="7a5c6-642">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="7a5c6-643">Issue #9171</span><span class="sxs-lookup"><span data-stu-id="7a5c6-643">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="7a5c6-644">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-644">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-645">Vor EF Core 3.0 wurde Code, der `HasOne` oder `HasMany` mit einer einzelnen Zeichenfolge aufruft, auf irritierende Weise interpretiert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-645">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="7a5c6-646">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-646">For example:</span></span>
```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="7a5c6-647">Der Code scheint `Samurai` mit einem anderen Entitätstyp über die `Entrance`-Navigationseigenschaft zu verknüpfen, die möglicherweise privat ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-647">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="7a5c6-648">Tatsächlich versucht dieser Code, eine Beziehung zu einem Entitätstyp namens `Entrance` ohne Navigationseigenschaft herzustellen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-648">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="7a5c6-649">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-649">**New behavior**</span></span>

<span data-ttu-id="7a5c6-650">Ab EF Core 3.0 führt der obige Code jetzt die erwartete Aktion durch.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-650">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="7a5c6-651">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-651">**Why**</span></span>

<span data-ttu-id="7a5c6-652">Das alte Verhalten war sehr verwirrend, vor allem beim Lesen des Konfigurationscodes und bei der Suche nach Fehlern.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-652">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="7a5c6-653">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-653">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-654">Dies führt nur zu Fehlern in Anwendungen, die Beziehungen explizit unter Verwendung von Zeichenfolgen für Typnamen und ohne explizite Angabe der Navigationseigenschaft konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-654">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="7a5c6-655">Dies ist nicht üblich.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-655">This is not common.</span></span>
<span data-ttu-id="7a5c6-656">Das vorherige Verhalten kann durch explizite Übergabe von `null` für den Namen der Navigationseigenschaft erzielt werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-656">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="7a5c6-657">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-657">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="7a5c6-658">Der Rückgabetyp für mehrere asynchrone Methoden wurde von Task in ValueTask geändert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-658">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="7a5c6-659">Issue #15184</span><span class="sxs-lookup"><span data-stu-id="7a5c6-659">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="7a5c6-660">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-660">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-661">Die folgenden asynchronen Methoden gaben zuvor `Task<T>` zurück:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-661">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="7a5c6-662">`ValueGenerator.NextValueAsync()` (und abgeleitete Klassen)</span><span class="sxs-lookup"><span data-stu-id="7a5c6-662">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

<span data-ttu-id="7a5c6-663">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-663">**New behavior**</span></span>

<span data-ttu-id="7a5c6-664">Die oben genannten Methoden geben nun `ValueTask<T>` über dieselbe `T` wie zuvor zurück.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-664">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

<span data-ttu-id="7a5c6-665">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-665">**Why**</span></span>

<span data-ttu-id="7a5c6-666">Durch diese Änderung verringert sich die Anzahl der Heapzuordnungen, die beim Aufrufen dieser Methoden entstehen, und dies führt zu einer Verbesserung der allgemeinen Leistung.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-666">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

<span data-ttu-id="7a5c6-667">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-667">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-668">Anwendungen, die einfach die oben genannten APIs erwarten, müssen nur neu kompiliert werden – Quelländerungen sind nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-668">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="7a5c6-669">Eine komplexere Verwendung (z.B. Übergeben der zurückgegebenen `Task` an `Task.WhenAny()`) erfordern in der Regel, dass die zurückgegebene `ValueTask<T>` durch einen Aufruf von `AsTask()` in `Task<T>` konvertiert wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-669">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="7a5c6-670">Beachten Sie, dass dadurch die mit dieser Änderung verbundene Verringerung der Zuordnungen aufgehoben wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-670">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="7a5c6-671">Die Anmerkung Relational:TypeMapping heißt nun nur noch TypeMapping</span><span class="sxs-lookup"><span data-stu-id="7a5c6-671">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="7a5c6-672">Issue #9913</span><span class="sxs-lookup"><span data-stu-id="7a5c6-672">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="7a5c6-673">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-673">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-674">Der Anmerkungsname für Typzuordnungsanmerkungen war bisher Relational:TypeMapping.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-674">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="7a5c6-675">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-675">**New behavior**</span></span>

<span data-ttu-id="7a5c6-676">Der Anmerkungsname für Typzuordnungsanmerkungen lautet nun TypeMapping.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-676">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="7a5c6-677">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-677">**Why**</span></span>

<span data-ttu-id="7a5c6-678">Typzuordnungen werden nicht mehr ausschließlich für Anbieter relationaler Datenbanken verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-678">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="7a5c6-679">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-679">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-680">Diese Änderung ist nur dann ein Breaking Change, wenn Anwendungen direkt über eine Anmerkung auf die Typzuordnung zugreifen. Dieses Szenario ist jedoch unüblich.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-680">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="7a5c6-681">Verzichten Sie daher darauf, die Anmerkung direkt zu verwenden, und greifen Sie stattdessen über die Fluent-API auf Typzuordnungen zu, um das Problem zu beheben.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-681">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="7a5c6-682">ToTable löst für einen abgeleiteten Typ eine Ausnahme aus</span><span class="sxs-lookup"><span data-stu-id="7a5c6-682">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="7a5c6-683">Issue #11811</span><span class="sxs-lookup"><span data-stu-id="7a5c6-683">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="7a5c6-684">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-684">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-685">Vor Version 3.0 wurde in EF Core der Aufruf von `ToTable()` für einen abgeleiteten Typ ignoriert, da TPH die einzige Strategie zur Vererbungszuordnung war und diese in unzulässigen Fällen angewendet wurde.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-685">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="7a5c6-686">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-686">**New behavior**</span></span>

<span data-ttu-id="7a5c6-687">Ab Version 3.0 wird in EF Core eine Ausnahme ausgelöst, um später eine unerwartete Zuordnung zu vermeiden, wenn `ToTable()` für einen abgeleiteten Typ aufgerufen wird. Dieser Schritt dient auch als Vorbereitung für die TPT- und TPC-Unterstützung, die in einem künftigen Release folgen wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-687">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="7a5c6-688">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-688">**Why**</span></span>

<span data-ttu-id="7a5c6-689">Derzeit ist es nicht zulässig, einen abgeleiteten Typ einer anderen Tabelle zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-689">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="7a5c6-690">Durch diese Änderung werden Breaking Changes vermieden, wenn der beschriebene Vorgang in Zukunft zulässig wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-690">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="7a5c6-691">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-691">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-692">Entfernen Sie alle Zuordnungen von abgeleiteten Typen zu anderen Tabellen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-692">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="7a5c6-693">ForSqlServerHasIndex wurde durch HasIndex ersetzt</span><span class="sxs-lookup"><span data-stu-id="7a5c6-693">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="7a5c6-694">Issue #12366</span><span class="sxs-lookup"><span data-stu-id="7a5c6-694">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="7a5c6-695">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-695">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-696">Vor Version 3.0 konnten in EF Core mit `ForSqlServerHasIndex().ForSqlServerInclude()` Spalten konfiguriert werden, die mit `INCLUDE` verwendet wurden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-696">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="7a5c6-697">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-697">**New behavior**</span></span>

<span data-ttu-id="7a5c6-698">Ab Version 3.0 wird in EF Core die Nutzung von `Include` für einen Index auf der relationalen Ebene unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-698">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="7a5c6-699">Verwenden Sie `HasIndex().ForSqlServerInclude()`.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-699">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="7a5c6-700">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-700">**Why**</span></span>

<span data-ttu-id="7a5c6-701">Diese Änderung wurde vorgenommen, um die API für Indizes mit `Include` an einer zentralen Stelle für alle Datenbankanbieter zusammenzuführen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-701">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

<span data-ttu-id="7a5c6-702">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-702">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-703">Verwenden Sie wie oben beschrieben die neue API.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-703">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="7a5c6-704">Metadaten-API-Änderungen</span><span class="sxs-lookup"><span data-stu-id="7a5c6-704">Metadata API changes</span></span>

[<span data-ttu-id="7a5c6-705">Issue #214</span><span class="sxs-lookup"><span data-stu-id="7a5c6-705">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="7a5c6-706">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-706">**New behavior**</span></span>

<span data-ttu-id="7a5c6-707">Die folgenden Eigenschaften wurden in Erweiterungsmethoden konvertiert:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-707">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

<span data-ttu-id="7a5c6-708">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-708">**Why**</span></span>

<span data-ttu-id="7a5c6-709">Mit dieser Änderung wird die Implementierung der oben genannten Schnittstellen vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-709">This change simplifies the implementation of the aforementioned interfaces.</span></span>

<span data-ttu-id="7a5c6-710">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-710">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-711">Verwenden Sie die neuen Erweiterungsmethoden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-711">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="7a5c6-712">Anbieterspezifische Metadaten-API-Änderungen</span><span class="sxs-lookup"><span data-stu-id="7a5c6-712">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="7a5c6-713">Issue #214</span><span class="sxs-lookup"><span data-stu-id="7a5c6-713">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="7a5c6-714">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-714">**New behavior**</span></span>

<span data-ttu-id="7a5c6-715">Die anbieterspezifischen Erweiterungsmethoden werden vereinfacht:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-715">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

<span data-ttu-id="7a5c6-716">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-716">**Why**</span></span>

<span data-ttu-id="7a5c6-717">Mit dieser Änderung wird die Implementierung der oben genannten Erweiterungsmethoden vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-717">This change simplifies the implementation of the aforementioned extension methods.</span></span>

<span data-ttu-id="7a5c6-718">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-718">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-719">Verwenden Sie die neuen Erweiterungsmethoden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-719">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="7a5c6-720">EF Core sendet keine PRAGMA-Anweisungen mehr, um Fremdschlüssel in SQLite zu erzwingen</span><span class="sxs-lookup"><span data-stu-id="7a5c6-720">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="7a5c6-721">Issue #12151</span><span class="sxs-lookup"><span data-stu-id="7a5c6-721">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="7a5c6-722">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-722">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-723">Vor Version 3.0 wurden in EF Core `PRAGMA foreign_keys = 1`-Anweisungen gesendet, wenn eine Verbindung mit SQLite hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-723">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="7a5c6-724">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-724">**New behavior**</span></span>

<span data-ttu-id="7a5c6-725">Ab Version 3.0 werden in EF Core keine `PRAGMA foreign_keys = 1`-Anweisungen mehr gesendet, wenn eine Verbindung mit SQLite hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-725">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="7a5c6-726">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-726">**Why**</span></span>

<span data-ttu-id="7a5c6-727">Diese Änderung wurde vorgenommen, da von EF Core standardmäßig `SQLitePCLRaw.bundle_e_sqlite3` verwendet wird. Dadurch ist die Erzwingung von Fremdschlüsseln standardmäßig aktiviert und muss nicht jedes Mal explizit aktiviert werden, wenn eine Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-727">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="7a5c6-728">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-728">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-729">Fremdschlüssel sind in SQLitePCLRaw.bundle_e_sqlite3 standardmäßig aktiviert. Dieses Bundle wird standardmäßig von EF Core verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-729">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="7a5c6-730">In anderen Fällen können Fremdschlüssel durch die Angabe `Foreign Keys=True` in der Verbindungszeichenfolge aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-730">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="7a5c6-731">SQLitePCLRaw.bundle_e_sqlite3 ist nun eine Abhängigkeit von Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="7a5c6-731">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="7a5c6-732">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-732">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-733">Vor Version 3.0 wurde von EF Core `SQLitePCLRaw.bundle_green` verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-733">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="7a5c6-734">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-734">**New behavior**</span></span>

<span data-ttu-id="7a5c6-735">Ab Version 3.0 wird von EF Core `SQLitePCLRaw.bundle_e_sqlite3` verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-735">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="7a5c6-736">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-736">**Why**</span></span>

<span data-ttu-id="7a5c6-737">Diese Änderung wurde vorgenommen, damit die unter iOS verwendete Version von SQLite sich konsistent zu Versionen auf anderen Plattformen verhält.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-737">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="7a5c6-738">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-738">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-739">Konfigurieren Sie `Microsoft.Data.Sqlite` so, dass ein anderes `SQLitePCLRaw`-Bundle verwendet wird, um die native SQLite-Version unter iOS zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-739">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="7a5c6-740">GUID-Werte werden jetzt als TEXT in SQLite gespeichert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-740">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="7a5c6-741">Issue #15078</span><span class="sxs-lookup"><span data-stu-id="7a5c6-741">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="7a5c6-742">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-742">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-743">GUID-Werte wurden in der Vergangenheit in SQLite als BLOB-Werte gespeichert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-743">Guid values were previously stored as BLOB values on SQLite.</span></span>

<span data-ttu-id="7a5c6-744">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-744">**New behavior**</span></span>

<span data-ttu-id="7a5c6-745">GUID-Werte werden jetzt als TEXT gespeichert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-745">Guid values are now stored as TEXT.</span></span>

<span data-ttu-id="7a5c6-746">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-746">**Why**</span></span>

<span data-ttu-id="7a5c6-747">Das Binärformat der GUIDs ist nicht standardisiert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-747">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="7a5c6-748">Das Speichern der Werte als TEXT führt dazu, dass die Datenbank mit anderen Technologien eher kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-748">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="7a5c6-749">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-749">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-750">Sie können vorhandene Datenbanken zum neuen Format migrieren, indem Sie SQL-Code wie den folgenden ausführen:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-750">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET GuidColumn = hex(substr(GuidColumn, 4, 1)) ||
                 hex(substr(GuidColumn, 3, 1)) ||
                 hex(substr(GuidColumn, 2, 1)) ||
                 hex(substr(GuidColumn, 1, 1)) || '-' ||
                 hex(substr(GuidColumn, 6, 1)) ||
                 hex(substr(GuidColumn, 5, 1)) || '-' ||
                 hex(substr(GuidColumn, 8, 1)) ||
                 hex(substr(GuidColumn, 7, 1)) || '-' ||
                 hex(substr(GuidColumn, 9, 2)) || '-' ||
                 hex(substr(GuidColumn, 11, 6))
WHERE typeof(GuidColumn) == 'blob';
```

<span data-ttu-id="7a5c6-751">Sie können in EF Core auch weiterhin das alte Verhalten verwenden, indem Sie einen Wertkonverter für diese Eigenschaften konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-751">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="7a5c6-752">Microsoft.Data.Sqlite kann weiterhin GUID-Werte aus BLOB- und TEXT-Spalten lesen. Da sich jedoch das Standardformat für Parameter und Konstanten geändert hat, müssen Sie wahrscheinlich Aktionen für die meisten GUID-Szenarios vornehmen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-752">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="7a5c6-753">Char-Werte werden jetzt als TEXT in SQLite gespeichert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-753">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="7a5c6-754">Issue #15020</span><span class="sxs-lookup"><span data-stu-id="7a5c6-754">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="7a5c6-755">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-755">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-756">Char-Werte wurden in der Vergangenheit in SQLite als INTEGER-Werte gespeichert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-756">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="7a5c6-757">Der Char-Wert *A* wurde z.B. als INTEGER-Wert 65 gespeichert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-757">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="7a5c6-758">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-758">**New behavior**</span></span>

<span data-ttu-id="7a5c6-759">Char-Werte werden jetzt als TEXT gespeichert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-759">Char values are now stored as TEXT.</span></span>

<span data-ttu-id="7a5c6-760">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-760">**Why**</span></span>

<span data-ttu-id="7a5c6-761">Das Speichern der Werte als TEXT ist naheliegender und führt dazu, dass die Datenbank mit anderen Technologien eher kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-761">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="7a5c6-762">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-762">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-763">Sie können vorhandene Datenbanken zum neuen Format migrieren, indem Sie SQL-Code wie den folgenden ausführen:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-763">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="7a5c6-764">Sie können in EF Core auch weiterhin das alte Verhalten verwenden, indem Sie einen Wertkonverter für diese Eigenschaften konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-764">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="7a5c6-765">Microsoft.Data.Sqlite kann auch weiterhin Zeichenwerte aus INTEGER- und TEXT-Spalten lesen, weshalb es sein kann, dass in einigen Szenarios keine weiteren Maßnahmen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-765">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="7a5c6-766">Migrations-IDs werden jetzt mit dem Kalender der invarianten Kultur generiert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-766">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="7a5c6-767">Issue #12978</span><span class="sxs-lookup"><span data-stu-id="7a5c6-767">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="7a5c6-768">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-768">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-769">Migrations-IDs wurden versehentlich mit dem Kalender der aktuellen Kultur generiert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-769">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

<span data-ttu-id="7a5c6-770">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-770">**New behavior**</span></span>

<span data-ttu-id="7a5c6-771">Migrations-IDs werden jetzt immer mit dem Kalender der invarianten Kultur generiert (gregorianischer Kalender).</span><span class="sxs-lookup"><span data-stu-id="7a5c6-771">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="7a5c6-772">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-772">**Why**</span></span>

<span data-ttu-id="7a5c6-773">Die Reihenfolge der Migrationen ist beim Aktualisieren einer Datenbank oder Auflösen von Mergekonflikten wesentlich.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-773">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="7a5c6-774">Wenn der invariante Kalender verwendet wird, werden Probleme bei der Reihenfolge vermieden, die entstehen, wenn Teammitglieder unterschiedliche Systemkalender verwenden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-774">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="7a5c6-775">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-775">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-776">Diese Änderung betrifft jeden Benutzer, der einen Kalender verwendet, der nicht gregorianisch ist und bei dem die Jahreszahl höher als die des gregorianischen Kalenders ist (wie z.B. in der buddhistischen Zeitrechnung).</span><span class="sxs-lookup"><span data-stu-id="7a5c6-776">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="7a5c6-777">Vorhandene Migrations-IDs müssen aktualisiert werden, damit neue Migrationen in der Reihenfolge hinter vorhandenen Migrationen eingeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-777">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="7a5c6-778">Sie können die Migrations-ID im Migration-Attribut in der Designerdatei der Migration finden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-778">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="7a5c6-779">Die Tabelle mit dem Migrationsverlauf muss auch aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-779">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="7a5c6-780">UseRowNumberForPaging wurde entfernt.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-780">UseRowNumberForPaging has been removed</span></span>

[<span data-ttu-id="7a5c6-781">Issue #16400</span><span class="sxs-lookup"><span data-stu-id="7a5c6-781">Tracking Issue #16400</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16400)

<span data-ttu-id="7a5c6-782">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-782">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-783">Vor EF Core 3.0 konnte `UseRowNumberForPaging` zum Generieren von SQL-Code für die Paginierung verwendet werden, der mit SQL Server 2008 kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-783">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

<span data-ttu-id="7a5c6-784">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-784">**New behavior**</span></span>

<span data-ttu-id="7a5c6-785">Ab EF Core 3.0 generiert EF nur noch SQL-Code für die Paginierung, die mit höheren SQL Server-Versionen kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-785">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span> 

<span data-ttu-id="7a5c6-786">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-786">**Why**</span></span>

<span data-ttu-id="7a5c6-787">Wir führen diese Änderung ein, weil [SQL Server 2008 kein unterstütztes Produkt mehr ist](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) und weil die Aktualisierung dieses Features auf die in EF Core 3.0 vorgenommenen Änderungen an Abfragen ein wichtiger Schritt ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-787">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

<span data-ttu-id="7a5c6-788">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-788">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-789">Es empfiehlt sich, eine Update auf eine neuere Version von SQL Server durchzuführen oder einen höheren Kompatibilitätsgrad zu verwenden, damit der generierte SQL-Code unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-789">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="7a5c6-790">Wenn dies bei Ihnen nicht möglich ist, fügen Sie einen [Kommentar zum Issue](https://github.com/aspnet/EntityFrameworkCore/issues/16400) ein, und geben Sie alle relevanten Informationen an.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-790">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/aspnet/EntityFrameworkCore/issues/16400) with details.</span></span> <span data-ttu-id="7a5c6-791">Je nach Feedback der Benutzer wird diese Entscheidung möglicherweise noch einmal überprüft.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-791">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="7a5c6-792">Erweiterungsinformationen und Metadaten aus IDbContextOptionsExtension entfernt</span><span class="sxs-lookup"><span data-stu-id="7a5c6-792">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

[<span data-ttu-id="7a5c6-793">Issue #16119</span><span class="sxs-lookup"><span data-stu-id="7a5c6-793">Tracking Issue #16119</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16119)

<span data-ttu-id="7a5c6-794">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-794">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-795">`IDbContextOptionsExtension` enthielt Methoden zum Bereitstellen von Metadaten zur Erweiterung.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-795">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

<span data-ttu-id="7a5c6-796">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-796">**New behavior**</span></span>

<span data-ttu-id="7a5c6-797">Diese Methoden wurden in eine neue abstrakte `DbContextOptionsExtensionInfo`-Basisklasse verschoben, die von einer neuen `IDbContextOptionsExtension.Info`-Eigenschaft zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-797">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

<span data-ttu-id="7a5c6-798">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-798">**Why**</span></span>

<span data-ttu-id="7a5c6-799">In den Releases zwischen 2.0 und 3.0 mussten wir diese Methoden mehrmals ergänzen oder ändern.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-799">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="7a5c6-800">Durch die Bereitstellung dieser Methoden in einer neuen abstrakten Basisklasse lassen sich solche Änderungen einfacher einführen, ohne dass es zu Konflikten mit vorhandenen Erweiterungen kommt.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-800">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

<span data-ttu-id="7a5c6-801">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-801">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-802">Erweiterungen wurden aktualisiert und verwenden das neue Muster.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-802">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="7a5c6-803">Beispiele finden sich in den vielen Implementierungen von `IDbContextOptionsExtension` für verschiedene Arten von Erweiterungen im EF Core-Quellcode.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-803">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="7a5c6-804">LogQueryPossibleExceptionWithAggregateOperator wurde umbenannt</span><span class="sxs-lookup"><span data-stu-id="7a5c6-804">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="7a5c6-805">Issue #10985</span><span class="sxs-lookup"><span data-stu-id="7a5c6-805">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="7a5c6-806">**Änderung**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-806">**Change**</span></span>

<span data-ttu-id="7a5c6-807">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` wurde umbenannt in `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-807">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

<span data-ttu-id="7a5c6-808">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-808">**Why**</span></span>

<span data-ttu-id="7a5c6-809">Die Benennung dieses Warnereignisses wurde an allen anderen Warnereignissen ausgerichtet.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-809">Aligns the naming of this warning event with all other warning events.</span></span>

<span data-ttu-id="7a5c6-810">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-810">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-811">Verwenden Sie den neuen Namen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-811">Use the new name.</span></span> <span data-ttu-id="7a5c6-812">(Beachten Sie, dass sich die Ereignis-ID-Nummer nicht geändert hat.)</span><span class="sxs-lookup"><span data-stu-id="7a5c6-812">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="7a5c6-813">Verdeutlichen der API für Einschränkungsnamen von Fremdschlüsseln</span><span class="sxs-lookup"><span data-stu-id="7a5c6-813">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="7a5c6-814">Issue #10730</span><span class="sxs-lookup"><span data-stu-id="7a5c6-814">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="7a5c6-815">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-815">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-816">Vor EF Core 3.0 wurden Einschränkungsnamen von Fremdschlüsseln einfach als „Namen“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-816">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="7a5c6-817">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-817">For example:</span></span>

```csharp
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="7a5c6-818">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-818">**New behavior**</span></span>

<span data-ttu-id="7a5c6-819">Ab EF Core 3.0 werden Einschränkungsnamen von Fremdschlüsseln nun als „Einschränkungsnamen“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-819">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="7a5c6-820">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-820">For example:</span></span>

```csharp
var constraintName = myForeignKey.ConstraintName;
```

<span data-ttu-id="7a5c6-821">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-821">**Why**</span></span>

<span data-ttu-id="7a5c6-822">Durch diese Änderung wird Konsistenz für Benennungen in diesem Bereich gewährleistet, und es wird verdeutlicht, dass es sich dabei um den Namen der Fremdschlüsseleinschränkung handelt und nicht um den Spalten- oder Eigenschaftennamen, auf deren Grundlage der Fremdschlüssel definiert ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-822">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

<span data-ttu-id="7a5c6-823">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-823">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-824">Verwenden Sie den neuen Namen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-824">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="7a5c6-825">„IRelationalDatabaseCreator.HasTables/HasTablesAsync“ ist jetzt öffentlich</span><span class="sxs-lookup"><span data-stu-id="7a5c6-825">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

[<span data-ttu-id="7a5c6-826">Issue #15997</span><span class="sxs-lookup"><span data-stu-id="7a5c6-826">Tracking Issue #15997</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15997)

<span data-ttu-id="7a5c6-827">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-827">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-828">Vor EF Core 3.0 waren diese Methoden geschützt.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-828">Before EF Core 3.0, these methods were protected.</span></span>

<span data-ttu-id="7a5c6-829">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-829">**New behavior**</span></span>

<span data-ttu-id="7a5c6-830">Ab EF Core 3.0 sind diese Methoden öffentlich.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-830">Starting with EF Core 3.0, these methods are public.</span></span>

<span data-ttu-id="7a5c6-831">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-831">**Why**</span></span>

<span data-ttu-id="7a5c6-832">Diese Methoden werden von EF verwendet, um zu ermitteln, ob eine Datenbank erstellt wurde, aber leer ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-832">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="7a5c6-833">Dies kann auch außerhalb von EF nützlich sein, um zu ermitteln, ob Migrationen angewendet werden sollen oder nicht.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-833">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

<span data-ttu-id="7a5c6-834">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-834">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-835">Der Zugriff auf Überschreibungen wurde geändert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-835">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="7a5c6-836">Microsoft.EntityFrameworkCore.Design ist jetzt ein DevelopmentDependency-Paket</span><span class="sxs-lookup"><span data-stu-id="7a5c6-836">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

[<span data-ttu-id="7a5c6-837">Issue #11506</span><span class="sxs-lookup"><span data-stu-id="7a5c6-837">Tracking Issue #11506</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11506)

<span data-ttu-id="7a5c6-838">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-838">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-839">Vor EF Core 3.0 war Microsoft.EntityFrameworkCore.Design ein reguläres NuGet-Paket, auf dessen Assembly von Projekten verwiesen werden kann, die davon abhängig sind.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-839">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

<span data-ttu-id="7a5c6-840">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-840">**New behavior**</span></span>

<span data-ttu-id="7a5c6-841">Ab EF Core 3.0 ist es ein DevelopmentDependency-Paket.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-841">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="7a5c6-842">Das bedeutet, dass die Abhängigkeit nicht transitiv auf andere Projekte übertragen wird und Sie nicht mehr standardmäßig auf die Assembly verweisen können.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-842">This means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

<span data-ttu-id="7a5c6-843">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-843">**Why**</span></span>

<span data-ttu-id="7a5c6-844">Dieses Paket ist nur für die Verwendung zur Entwurfszeit konzipiert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-844">This package is only intended to be used at design time.</span></span> <span data-ttu-id="7a5c6-845">Bereitgestellte Anwendungen sollten nicht darauf verweisen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-845">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="7a5c6-846">Die Kennzeichnung des Pakets als DevelopmentDependency unterstreicht diese Empfehlung.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-846">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

<span data-ttu-id="7a5c6-847">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-847">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-848">Wenn Sie auf dieses Paket verweisen müssen, um das Verhalten von EF Core zur Entwurfszeit zu überschreiben, können Sie die Metadaten des PackageReference-Elements in Ihrem Projekt aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-848">If you need to reference this package to override EF Core's design-time behavior, then you can update PackageReference item metadata in your project.</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="7a5c6-849">Wenn transitiv über via Microsoft.EntityFrameworkCore.Tools auf das Paket verwiesen wird, müssen Sie dem Paket eine explizite PackageReference hinzufügen, um seine Metadaten zu ändern.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-849">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span> <span data-ttu-id="7a5c6-850">Ein solch expliziter Verweis muss jedem Projekt hinzugefügt werden, für das die Typen aus den Paketen benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-850">Such an explicit reference must be added to any project where the types from the package are needed.</span></span>

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="7a5c6-851">SQLitePCL.raw zu Version 2.0.0 aktualisiert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-851">SQLitePCL.raw updated to version 2.0.0</span></span>

[<span data-ttu-id="7a5c6-852">Issue #14824</span><span class="sxs-lookup"><span data-stu-id="7a5c6-852">Tracking Issue #14824</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14824)

<span data-ttu-id="7a5c6-853">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-853">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-854">Microsoft.EntityFrameworkCore.Sqlite war früher von Version 1.1.12 von SQLitePCL.raw abhängig.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-854">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

<span data-ttu-id="7a5c6-855">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-855">**New behavior**</span></span>

<span data-ttu-id="7a5c6-856">Wir haben unser Paket aktualisiert, so dass es jetzt von Version 2.0.0 abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-856">We've updated our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="7a5c6-857">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-857">**Why**</span></span>

<span data-ttu-id="7a5c6-858">Version 2.0.0 von SQLitePCL.raw zielt auf .NET Standard 2.0 ab.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-858">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="7a5c6-859">Zuvor war .NET Standard 1.1 das Ziel, hierfür war für ein umfassender Funktionsabschluss von transitiven Paketen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-859">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

<span data-ttu-id="7a5c6-860">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-860">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-861">SQLitePCL.raw, Version 2.0.0, enthält einige Breaking Changes.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-861">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="7a5c6-862">Weitere Informationen finden Sie in den [Versionshinweisen](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md).</span><span class="sxs-lookup"><span data-stu-id="7a5c6-862">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="7a5c6-863">NetTopologySuite auf Version 2.0.0 aktualisiert</span><span class="sxs-lookup"><span data-stu-id="7a5c6-863">NetTopologySuite updated to version 2.0.0</span></span>

[<span data-ttu-id="7a5c6-864">Issue #14825</span><span class="sxs-lookup"><span data-stu-id="7a5c6-864">Tracking Issue #14825</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14825)

<span data-ttu-id="7a5c6-865">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-865">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-866">Die Pakete mit räumlichen Daten waren bisher von Version 1.15.1 der NetTopologySuite abhängig.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-866">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

<span data-ttu-id="7a5c6-867">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-867">**New behavior**</span></span>

<span data-ttu-id="7a5c6-868">Wir haben unser Paket aktualisiert, so dass es jetzt von Version 2.0.0 abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-868">We've update our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="7a5c6-869">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-869">**Why**</span></span>

<span data-ttu-id="7a5c6-870">Version 2.0.0 der NetTopologySuite behebt verschiedene Verwendungsprobleme, die von EF Core-Benutzern gemeldet wurden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-870">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

<span data-ttu-id="7a5c6-871">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-871">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-872">NetTopologySuite 2.0.0 umfasst einige Breaking Changes.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-872">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="7a5c6-873">Weitere Informationen finden Sie in den [Versionshinweisen](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001).</span><span class="sxs-lookup"><span data-stu-id="7a5c6-873">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a><span data-ttu-id="7a5c6-874">Microsoft.Data.SqlClient wird statt System.Data.SqlClient verwendet</span><span class="sxs-lookup"><span data-stu-id="7a5c6-874">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>

[<span data-ttu-id="7a5c6-875">Issue #15636</span><span class="sxs-lookup"><span data-stu-id="7a5c6-875">Tracking Issue #15636</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15636)

<span data-ttu-id="7a5c6-876">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-876">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-877">Microsoft.EntityFrameworkCore.SqlServer war zuvor von System.Data.SqlClient abhängig.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-877">Microsoft.EntityFrameworkCore.SqlServer previously depended on System.Data.SqlClient.</span></span>

<span data-ttu-id="7a5c6-878">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-878">**New behavior**</span></span>

<span data-ttu-id="7a5c6-879">Das Paket wurde so aktualisiert, dass es jetzt von Microsoft.Data.SqlClient abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-879">We've updated our package to depend on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="7a5c6-880">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-880">**Why**</span></span>

<span data-ttu-id="7a5c6-881">Microsoft SQL Server ist zukünftig der Haupttreiber für den Datenzugriff für SQL Server, und System.Data.SqlClient wird nicht länger der Fokus bei der Entwicklung sein.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-881">Microsoft.Data.SqlClient is the flagship data access driver for SQL Server going forward, and System.Data.SqlClient no longer be the focus of development.</span></span>
<span data-ttu-id="7a5c6-882">Einige wichtige Features wie Always Encrypted sind nur in Microsoft.Data.SqlClient verfügbar.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-882">Some important features, such as Always Encrypted, are only available on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="7a5c6-883">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-883">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-884">Wenn Ihr Code eine direkte Abhängigkeit von System.Data.SqlClient hat, müssen Sie diesen ändern, damit er stattdessen auf Microsoft.Data.SqlClient verweist. Da die beiden Pakete einen hohen Grad an API-Kompatibilität vorweisen, sollte dafür nur eine einfache Paket- und Namespaceänderung erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-884">If your code takes a direct dependency on System.Data.SqlClient, you must change it to reference Microsoft.Data.SqlClient instead; as the two packages maintain a very high degree of API compatibility, this should only be a simple package and namespace change.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="7a5c6-885">Beziehungen mit mehreren mehrdeutigen Selbstverweisen müssen nun konfiguriert werden</span><span class="sxs-lookup"><span data-stu-id="7a5c6-885">Multiple ambiguous self-referencing relationships must be configured</span></span> 

[<span data-ttu-id="7a5c6-886">Issue #13573</span><span class="sxs-lookup"><span data-stu-id="7a5c6-886">Tracking Issue #13573</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13573)

<span data-ttu-id="7a5c6-887">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-887">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-888">Ein Entitätstyp, der über unidirektionale Navigationseigenschaften mit mehreren Selbstverweisen und über übereinstimmende Fremdschlüssel verfügte, wurde bislang fälschlicherweise als einfache Beziehung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-888">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="7a5c6-889">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-889">For example:</span></span>

```csharp
public class User 
{
        public Guid Id { get; set; }
        public User CreatedBy { get; set; }
        public User UpdatedBy { get; set; }
        public Guid CreatedById { get; set; }
        public Guid? UpdatedById { get; set; }
}
```

<span data-ttu-id="7a5c6-890">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-890">**New behavior**</span></span>

<span data-ttu-id="7a5c6-891">Dieses Szenario wird nun beim Erstellen von Modellen erkannt. Außerdem wird eine Ausnahme ausgelöst, in der darauf hingewiesen wird, dass das Modell mehrdeutig ist.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-891">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

<span data-ttu-id="7a5c6-892">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-892">**Why**</span></span>

<span data-ttu-id="7a5c6-893">Das resultierende Modell war mehrdeutig und führte in diesem Fall üblicherweise zu falschen Ergebnissen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-893">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

<span data-ttu-id="7a5c6-894">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-894">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-895">Konfigurieren Sie die Beziehung vollständig.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-895">Use full configuration of the relationship.</span></span> <span data-ttu-id="7a5c6-896">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7a5c6-896">For example:</span></span>

```csharp
modelBuilder
     .Entity<User>()
     .HasOne(e => e.CreatedBy)
     .WithMany();
 
 modelBuilder
     .Entity<User>()
     .HasOne(e => e.UpdatedBy)
     .WithMany();
```

<a name="udf-empty-string"></a>
### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a><span data-ttu-id="7a5c6-897">DbFunction.Schema ist NULL oder eine leere Zeichenfolge und ist deshalb so konfiguriert, dass es im Standardschema des Modells ist</span><span class="sxs-lookup"><span data-stu-id="7a5c6-897">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>

[<span data-ttu-id="7a5c6-898">Issue #12757</span><span class="sxs-lookup"><span data-stu-id="7a5c6-898">Tracking Issue #12757</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12757)

<span data-ttu-id="7a5c6-899">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-899">**Old behavior**</span></span>

<span data-ttu-id="7a5c6-900">Eine mit dem Schema als leere Zeichenfolge konfigurierte DbFunction wurde als integrierte Funktion ohne Schema behandelt.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-900">A DbFunction configured with schema as an empty string was treated as built-in function without a schema.</span></span> <span data-ttu-id="7a5c6-901">Der folgende Code ordnet z. B. die CLR-Funktion `DatePart` der integrierten Funktion `DATEPART` auf SqlServer zu.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-901">For example following code will map `DatePart` CLR function to `DATEPART` built-in function on SqlServer.</span></span>

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

<span data-ttu-id="7a5c6-902">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-902">**New behavior**</span></span>

<span data-ttu-id="7a5c6-903">Alle DbFunction-Zuordnungen werden als einer benutzerdefinierten Funktion zugeordnet betrachtet.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-903">All DbFunction mappings are considered to be mapped to user defined functions.</span></span> <span data-ttu-id="7a5c6-904">Daher würde die Funktion durch einen leeren Zeichenfolgenwert in das Standardschema für das Modell eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-904">Hence empty string value would put the function inside the default schema for the model.</span></span> <span data-ttu-id="7a5c6-905">Dies könnte das Schema sein, das explizit über eine fließende `modelBuilder.HasDefaultSchema()`- oder anderweitig über eine `dbo`-API konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-905">Which could be the schema configured explicitly via fluent API `modelBuilder.HasDefaultSchema()` or `dbo` otherwise.</span></span>

<span data-ttu-id="7a5c6-906">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-906">**Why**</span></span>

<span data-ttu-id="7a5c6-907">Weil das Schema vorher leer war, konnte man erreichen, dass die Funktion integriert, aber die Logik nur für SqlServer anwendbar war, wo integrierte Funktionen nicht zu einem Schema gehören.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-907">Previously schema being empty was a way to treat that function is built-in but that logic is only applicable for SqlServer where built-in functions do not belong to any schema.</span></span>

<span data-ttu-id="7a5c6-908">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="7a5c6-908">**Mitigations**</span></span>

<span data-ttu-id="7a5c6-909">Konfigurieren Sie die Übersetzung von DbFunction manuell, um Sie einer integrierten Funktion zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="7a5c6-909">Configure DbFunction's translation manually to map it to a built-in function.</span></span>

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```
