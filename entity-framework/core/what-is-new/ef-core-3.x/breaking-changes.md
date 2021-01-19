---
title: Breaking Changes in EF Core 3.x – EF Core
description: Vollständige Liste der in Entity Framework Core 3.x eingeführten Breaking Changes
author: ajcvickers
ms.date: 09/05/2020
uid: core/what-is-new/ef-core-3.x/breaking-changes
ms.openlocfilehash: 8c0be4193c79e838e40bfc2dc10c9d12b01381cd
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128770"
---
# <a name="breaking-changes-included-in-ef-core-3x"></a><span data-ttu-id="3a074-103">Breaking Changes in EF Core 3.x</span><span class="sxs-lookup"><span data-stu-id="3a074-103">Breaking changes included in EF Core 3.x</span></span>

<span data-ttu-id="3a074-104">Die folgenden API-Änderungen und Behavior Changes können dazu führen, dass vorhandene Anwendungen nach einem Upgrade auf 3.x nicht mehr funktionieren.</span><span class="sxs-lookup"><span data-stu-id="3a074-104">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.x.</span></span>
<span data-ttu-id="3a074-105">Änderungen, die sich voraussichtlich nur auf Datenbankanbieter auswirken, sind unter [Änderungen mit Auswirkungen auf den Anbieter](xref:core/providers/provider-log) dokumentiert.</span><span class="sxs-lookup"><span data-stu-id="3a074-105">Changes that we expect to only impact database providers are documented under [provider changes](xref:core/providers/provider-log).</span></span>

## <a name="summary"></a><span data-ttu-id="3a074-106">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="3a074-106">Summary</span></span>

| <span data-ttu-id="3a074-107">**Wichtige Änderung**</span><span class="sxs-lookup"><span data-stu-id="3a074-107">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="3a074-108">**Auswirkungen**</span><span class="sxs-lookup"><span data-stu-id="3a074-108">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="3a074-109">LINQ-Abfragen werden nicht mehr auf dem Client ausgewertet</span><span class="sxs-lookup"><span data-stu-id="3a074-109">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="3a074-110">Hoch</span><span class="sxs-lookup"><span data-stu-id="3a074-110">High</span></span>       |
| [<span data-ttu-id="3a074-111">Das EF Core-Befehlszeilentool (dotnet ef) ist nicht mehr Bestandteil des .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="3a074-111">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="3a074-112">Hoch</span><span class="sxs-lookup"><span data-stu-id="3a074-112">High</span></span>      |
| [<span data-ttu-id="3a074-113">DetectChanges berücksichtigt vom Speicher generierte Schlüsselwerte</span><span class="sxs-lookup"><span data-stu-id="3a074-113">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="3a074-114">Hoch</span><span class="sxs-lookup"><span data-stu-id="3a074-114">High</span></span>      |
| [<span data-ttu-id="3a074-115">FromSql, ExecuteSql und ExecuteSqlAsync wurden umbenannt</span><span class="sxs-lookup"><span data-stu-id="3a074-115">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="3a074-116">Hoch</span><span class="sxs-lookup"><span data-stu-id="3a074-116">High</span></span>      |
| [<span data-ttu-id="3a074-117">Abfragetypen werden mit Entitätstypen zusammengeführt</span><span class="sxs-lookup"><span data-stu-id="3a074-117">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="3a074-118">Hoch</span><span class="sxs-lookup"><span data-stu-id="3a074-118">High</span></span>      |
| [<span data-ttu-id="3a074-119">Entity Framework Core ist nicht mehr Teil des gemeinsam verwendeten ASP.NET Core-Frameworks</span><span class="sxs-lookup"><span data-stu-id="3a074-119">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="3a074-120">Medium</span><span class="sxs-lookup"><span data-stu-id="3a074-120">Medium</span></span>      |
| [<span data-ttu-id="3a074-121">Kaskadierende Deletes werden standardmäßig sofort ausgeführt</span><span class="sxs-lookup"><span data-stu-id="3a074-121">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="3a074-122">Medium</span><span class="sxs-lookup"><span data-stu-id="3a074-122">Medium</span></span>      |
| [<span data-ttu-id="3a074-123">Eager Loading von verwandten Entitäten erfolgt nun in einer einzelnen Abfrage</span><span class="sxs-lookup"><span data-stu-id="3a074-123">Eager loading of related entities now happens in a single query</span></span>](#eager-loading-single-query) | <span data-ttu-id="3a074-124">Medium</span><span class="sxs-lookup"><span data-stu-id="3a074-124">Medium</span></span>      |
| [<span data-ttu-id="3a074-125">DeleteBehavior.Restrict verfügt über eine übersichtlichere Semantik</span><span class="sxs-lookup"><span data-stu-id="3a074-125">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="3a074-126">Medium</span><span class="sxs-lookup"><span data-stu-id="3a074-126">Medium</span></span>      |
| [<span data-ttu-id="3a074-127">Die Konfigurations-API für Beziehungen abhängiger (owned) Typen wurde geändert</span><span class="sxs-lookup"><span data-stu-id="3a074-127">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="3a074-128">Medium</span><span class="sxs-lookup"><span data-stu-id="3a074-128">Medium</span></span>      |
| [<span data-ttu-id="3a074-129">Für jede Eigenschaft wird separat ein ganzzahliger speicherinterner Schlüssel generiert</span><span class="sxs-lookup"><span data-stu-id="3a074-129">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="3a074-130">Medium</span><span class="sxs-lookup"><span data-stu-id="3a074-130">Medium</span></span>      |
| [<span data-ttu-id="3a074-131">Abfragen ohne Nachverfolgung führen keine Identitätsauflösung mehr durch</span><span class="sxs-lookup"><span data-stu-id="3a074-131">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="3a074-132">Medium</span><span class="sxs-lookup"><span data-stu-id="3a074-132">Medium</span></span>      |
| [<span data-ttu-id="3a074-133">Metadaten-API-Änderungen</span><span class="sxs-lookup"><span data-stu-id="3a074-133">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="3a074-134">Medium</span><span class="sxs-lookup"><span data-stu-id="3a074-134">Medium</span></span>      |
| [<span data-ttu-id="3a074-135">Anbieterspezifische Metadaten-API-Änderungen</span><span class="sxs-lookup"><span data-stu-id="3a074-135">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="3a074-136">Medium</span><span class="sxs-lookup"><span data-stu-id="3a074-136">Medium</span></span>      |
| [<span data-ttu-id="3a074-137">UseRowNumberForPaging wurde entfernt.</span><span class="sxs-lookup"><span data-stu-id="3a074-137">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="3a074-138">Medium</span><span class="sxs-lookup"><span data-stu-id="3a074-138">Medium</span></span>      |
| [<span data-ttu-id="3a074-139">FromSql-Methode kann nicht zusammengesetzt werden, wenn sie mit einer gespeicherten Prozedur verwendet wird</span><span class="sxs-lookup"><span data-stu-id="3a074-139">FromSql method when used with stored procedure cannot be composed</span></span>](#fromsqlsproc) | <span data-ttu-id="3a074-140">Medium</span><span class="sxs-lookup"><span data-stu-id="3a074-140">Medium</span></span>      |
| [<span data-ttu-id="3a074-141">FromSql-Methoden können nur für die Stammelemente der Abfrage angegeben werden</span><span class="sxs-lookup"><span data-stu-id="3a074-141">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="3a074-142">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-142">Low</span></span>      |
| [<span data-ttu-id="3a074-143">Temporäre Schlüsselwerte werden nicht mehr Entitätsinstanzen zugewiesen</span><span class="sxs-lookup"><span data-stu-id="3a074-143">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="3a074-144">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-144">Low</span></span>      |
| [<span data-ttu-id="3a074-145">Abhängige Entitäten, die die Tabelle gemeinsam mit dem Prinzipal verwenden, sind jetzt optional</span><span class="sxs-lookup"><span data-stu-id="3a074-145">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="3a074-146">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-146">Low</span></span>      |
| [<span data-ttu-id="3a074-147">Alle Entitäten, die eine Tabelle mit einer Spalte für das Parallelitätstoken gemeinsam verwenden, müssen diese einer Eigenschaft zuordnen</span><span class="sxs-lookup"><span data-stu-id="3a074-147">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="3a074-148">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-148">Low</span></span>      |
| [<span data-ttu-id="3a074-149">Benutzereigene Entitäten können nicht abgefragt werden, ohne dass der Besitzer eine Nachverfolgungsabfrage verwendet</span><span class="sxs-lookup"><span data-stu-id="3a074-149">Owned entities cannot be queried without the owner using a tracking query</span></span>](#owned-query) | <span data-ttu-id="3a074-150">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-150">Low</span></span>      |
| [<span data-ttu-id="3a074-151">Geerbte Eigenschaften von nicht zugeordneten Typen sind nun einer einzelnen Spalte für alle abgeleiteten Typen zugeordnet</span><span class="sxs-lookup"><span data-stu-id="3a074-151">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="3a074-152">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-152">Low</span></span>      |
| [<span data-ttu-id="3a074-153">Die Konvention zur Fremdschlüsseleigenschaft entspricht nicht mehr dem Namen der Prinzipaleigenschaft</span><span class="sxs-lookup"><span data-stu-id="3a074-153">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="3a074-154">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-154">Low</span></span>      |
| [<span data-ttu-id="3a074-155">Die Datenbankverbindung wird jetzt geschlossen, wenn sie nicht mehr verwendet wird, bevor TransactionScope abgeschlossen wurde</span><span class="sxs-lookup"><span data-stu-id="3a074-155">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="3a074-156">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-156">Low</span></span>      |
| [<span data-ttu-id="3a074-157">Unterstützungsfelder werden standardmäßig verwendet</span><span class="sxs-lookup"><span data-stu-id="3a074-157">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="3a074-158">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-158">Low</span></span>      |
| [<span data-ttu-id="3a074-159">Eine Ausnahme wird ausgelöst, wenn mehrere kompatible Unterstützungsfelder gefunden werden</span><span class="sxs-lookup"><span data-stu-id="3a074-159">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="3a074-160">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-160">Low</span></span>      |
| [<span data-ttu-id="3a074-161">„Nur-Feld“-Eigenschaftsnamen sollten dem Feldnamen entsprechen</span><span class="sxs-lookup"><span data-stu-id="3a074-161">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="3a074-162">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-162">Low</span></span>      |
| [<span data-ttu-id="3a074-163">Von AddDbContext/AddDbContextPool werden AddLogging und AddMemoryCache nicht mehr aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="3a074-163">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="3a074-164">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-164">Low</span></span>      |
| [<span data-ttu-id="3a074-165">AddEntityFramework\* fügt IMemoryCache mit einer Größenbeschränkung hinzu</span><span class="sxs-lookup"><span data-stu-id="3a074-165">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>](#addentityframework-adds-imemorycache-with-a-size-limit) | <span data-ttu-id="3a074-166">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-166">Low</span></span>      |
| [<span data-ttu-id="3a074-167">DbContext.Entry erkennt nun mit DetectChanges lokale Änderungen</span><span class="sxs-lookup"><span data-stu-id="3a074-167">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="3a074-168">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-168">Low</span></span>      |
| [<span data-ttu-id="3a074-169">Schlüssel aus Zeichenfolgen und Bytearrays werden standardmäßig nicht vom Client generiert</span><span class="sxs-lookup"><span data-stu-id="3a074-169">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="3a074-170">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-170">Low</span></span>      |
| [<span data-ttu-id="3a074-171">ILoggerFactory ist nun ein bereichsbezogener Dienst</span><span class="sxs-lookup"><span data-stu-id="3a074-171">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="3a074-172">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-172">Low</span></span>      |
| [<span data-ttu-id="3a074-173">Für Proxys mit Lazy Loading (trägem Laden) gilt nicht mehr die Annahme, dass Navigationseigenschaften vollständig geladen sind</span><span class="sxs-lookup"><span data-stu-id="3a074-173">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="3a074-174">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-174">Low</span></span>      |
| [<span data-ttu-id="3a074-175">Die Erstellung zu vieler interner Dienstanbieter führt standardmäßig zu einem Fehler</span><span class="sxs-lookup"><span data-stu-id="3a074-175">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="3a074-176">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-176">Low</span></span>      |
| [<span data-ttu-id="3a074-177">Neues Verhalten für HasOne/HasMany bei Aufruf mit einer einzelnen Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="3a074-177">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="3a074-178">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-178">Low</span></span>      |
| [<span data-ttu-id="3a074-179">Der Rückgabetyp für mehrere asynchrone Methoden wurde von Task in ValueTask geändert</span><span class="sxs-lookup"><span data-stu-id="3a074-179">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="3a074-180">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-180">Low</span></span>      |
| [<span data-ttu-id="3a074-181">Die Anmerkung Relational:TypeMapping heißt nun nur noch TypeMapping</span><span class="sxs-lookup"><span data-stu-id="3a074-181">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="3a074-182">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-182">Low</span></span>      |
| [<span data-ttu-id="3a074-183">ToTable löst für einen abgeleiteten Typ eine Ausnahme aus</span><span class="sxs-lookup"><span data-stu-id="3a074-183">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="3a074-184">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-184">Low</span></span>      |
| [<span data-ttu-id="3a074-185">EF Core sendet keine PRAGMA-Anweisungen mehr, um Fremdschlüssel in SQLite zu erzwingen</span><span class="sxs-lookup"><span data-stu-id="3a074-185">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="3a074-186">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-186">Low</span></span>      |
| [<span data-ttu-id="3a074-187">SQLitePCLRaw.bundle_e_sqlite3 ist nun eine Abhängigkeit von Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="3a074-187">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="3a074-188">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-188">Low</span></span>      |
| [<span data-ttu-id="3a074-189">GUID-Werte werden jetzt als TEXT in SQLite gespeichert</span><span class="sxs-lookup"><span data-stu-id="3a074-189">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="3a074-190">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-190">Low</span></span>      |
| [<span data-ttu-id="3a074-191">Char-Werte werden jetzt als TEXT in SQLite gespeichert</span><span class="sxs-lookup"><span data-stu-id="3a074-191">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="3a074-192">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-192">Low</span></span>      |
| [<span data-ttu-id="3a074-193">Migrations-IDs werden jetzt mit dem Kalender der invarianten Kultur generiert</span><span class="sxs-lookup"><span data-stu-id="3a074-193">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="3a074-194">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-194">Low</span></span>      |
| [<span data-ttu-id="3a074-195">Erweiterungsinformationen und Metadaten aus IDbContextOptionsExtension entfernt</span><span class="sxs-lookup"><span data-stu-id="3a074-195">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="3a074-196">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-196">Low</span></span>      |
| [<span data-ttu-id="3a074-197">LogQueryPossibleExceptionWithAggregateOperator wurde umbenannt</span><span class="sxs-lookup"><span data-stu-id="3a074-197">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="3a074-198">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-198">Low</span></span>      |
| [<span data-ttu-id="3a074-199">Verdeutlichen der API für Einschränkungsnamen von Fremdschlüsseln</span><span class="sxs-lookup"><span data-stu-id="3a074-199">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="3a074-200">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-200">Low</span></span>      |
| [<span data-ttu-id="3a074-201">„IRelationalDatabaseCreator.HasTables/HasTablesAsync“ ist jetzt öffentlich</span><span class="sxs-lookup"><span data-stu-id="3a074-201">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="3a074-202">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-202">Low</span></span>      |
| [<span data-ttu-id="3a074-203">Microsoft.EntityFrameworkCore.Design ist jetzt ein DevelopmentDependency-Paket</span><span class="sxs-lookup"><span data-stu-id="3a074-203">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="3a074-204">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-204">Low</span></span>      |
| [<span data-ttu-id="3a074-205">SQLitePCL.raw zu Version 2.0.0 aktualisiert</span><span class="sxs-lookup"><span data-stu-id="3a074-205">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="3a074-206">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-206">Low</span></span>      |
| [<span data-ttu-id="3a074-207">NetTopologySuite auf Version 2.0.0 aktualisiert</span><span class="sxs-lookup"><span data-stu-id="3a074-207">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="3a074-208">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-208">Low</span></span>      |
| [<span data-ttu-id="3a074-209">Microsoft.Data.SqlClient wird statt System.Data.SqlClient verwendet</span><span class="sxs-lookup"><span data-stu-id="3a074-209">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>](#SqlClient) | <span data-ttu-id="3a074-210">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-210">Low</span></span>      |
| [<span data-ttu-id="3a074-211">Beziehungen mit mehreren mehrdeutigen Selbstverweisen müssen nun konfiguriert werden</span><span class="sxs-lookup"><span data-stu-id="3a074-211">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="3a074-212">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-212">Low</span></span>      |
| [<span data-ttu-id="3a074-213">DbFunction.Schema ist NULL oder eine leere Zeichenfolge und ist deshalb so konfiguriert, dass es im Standardschema des Modells ist</span><span class="sxs-lookup"><span data-stu-id="3a074-213">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>](#udf-empty-string) | <span data-ttu-id="3a074-214">Niedrig</span><span class="sxs-lookup"><span data-stu-id="3a074-214">Low</span></span>      |
| [<span data-ttu-id="3a074-215">~~EF Core 3.0 zielt auf .NET Standard 2.1 und nicht auf .NET Standard 2.0 ab~~ Zurückgesetzt</span><span class="sxs-lookup"><span data-stu-id="3a074-215">~~EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0~~ Reverted</span></span>](#netstandard21) | |
| [<span data-ttu-id="3a074-216">~~Die Abfrageausführung wird auf Debugebene protokolliert~~ – zurückgesetzt</span><span class="sxs-lookup"><span data-stu-id="3a074-216">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | |

## <a name="high-impact-changes"></a><span data-ttu-id="3a074-217">Änderungen mit hoher Auswirkung</span><span class="sxs-lookup"><span data-stu-id="3a074-217">High-impact changes</span></span>

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="3a074-218">LINQ-Abfragen werden nicht mehr auf dem Client ausgewertet</span><span class="sxs-lookup"><span data-stu-id="3a074-218">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="3a074-219">[Issue #14935](https://github.com/dotnet/efcore/issues/14935)
[Siehe auch Issue #12795](https://github.com/dotnet/efcore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="3a074-219">[Tracking Issue #14935](https://github.com/dotnet/efcore/issues/14935)
[Also see issue #12795](https://github.com/dotnet/efcore/issues/12795)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="3a074-220">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-220">Old behavior</span></span>

<span data-ttu-id="3a074-221">Vor Version 3.0 wurden in EF Core automatisch Ausdrücke auf dem Client ausgewertet, wenn diese Teil einer Abfrage waren und nicht in SQL oder in einen Parameter konvertiert werden konnten.</span><span class="sxs-lookup"><span data-stu-id="3a074-221">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="3a074-222">Wenn potenziell aufwendige Ausdrücke auf dem Client ausgewertet werden sollten, wurde standardmäßig nur eine Warnung ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="3a074-222">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-223">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-223">New behavior</span></span>

<span data-ttu-id="3a074-224">Ab Version 3.0 können in EF Core nur Ausdrücke der obersten Projektion (dem letzten `Select()`-Aufruf in der Abfrage) auf dem Client ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-224">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="3a074-225">Wenn Ausdrücke in einem anderen Teil der Abfrage nicht in SQL oder in einen Parameter konvertiert werden können, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="3a074-225">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-226">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-226">Why</span></span>

<span data-ttu-id="3a074-227">Die automatische Auswertung von Abfragen auf Clients hat zur Folge, dass viele Abfragen auch dann ausgeführt werden, wenn wichtige Teile nicht übersetzt werden können.</span><span class="sxs-lookup"><span data-stu-id="3a074-227">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="3a074-228">Dieses Verhalten kann zu unerwartetem und potenziell schädlichem Verhalten führen, das möglicherweise erst in der Produktionsphase erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-228">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="3a074-229">Eine nicht übersetzbare Bedingung in einem `Where()`-Aufruf kann sich beispielsweise so auswirken, dass alle Zeilen einer Tabelle vom Datenbankserver übertragen werden und der Filter auf dem Client angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-229">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="3a074-230">Wenn die Tabelle in der Entwicklungsphase nur wenige Zeilen enthält, ist es gut möglich, dass diese Situation unerkannt bleibt. Wenn die Anwendung jedoch in die Produktionsumgebung überführt wird, enthält die Tabelle möglicherweise Millionen von Zeilen, was zu schwerwiegenden Konsequenzen führen kann.</span><span class="sxs-lookup"><span data-stu-id="3a074-230">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="3a074-231">Wie die Erfahrung in der Entwicklungsphase gezeigt hat, können auch Warnungen, die bei Auswertungen auf dem Client ausgelöst werden, dieses Problem nicht lösen, da sie sich leicht ignorieren lassen.</span><span class="sxs-lookup"><span data-stu-id="3a074-231">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="3a074-232">Die automatische Auswertung auf Clients kann darüber hinaus zu Problemen führen, bei denen die Verbesserung der Abfrageübersetzung für bestimmte Ausdrücke zu unbeabsichtigten Breaking Changes zwischen Releases führt.</span><span class="sxs-lookup"><span data-stu-id="3a074-232">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-233">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-233">Mitigations</span></span>

<span data-ttu-id="3a074-234">Wenn sich eine Abfrage nicht vollständig übersetzen lässt, habe Sie zwei Möglichkeiten: Schreiben Sie sie entweder um, oder setzen Sie alternativ `AsEnumerable()`, `ToList()` oder ähnliche Methoden ein, um Daten wieder zurück an den Client zu übertragen, wo sie mit LINQ to Objects weiterverarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="3a074-234">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="no-longer"></a>

## <a name="medium-impact-changes"></a><span data-ttu-id="3a074-235">Änderungen mit mittlerer Auswirkung</span><span class="sxs-lookup"><span data-stu-id="3a074-235">Medium-impact changes</span></span>

### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="3a074-236">Entity Framework Core ist nicht mehr Teil des gemeinsam verwendeten ASP.NET Core-Frameworks</span><span class="sxs-lookup"><span data-stu-id="3a074-236">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="3a074-237">Issueankündigungen #325</span><span class="sxs-lookup"><span data-stu-id="3a074-237">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-238">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-238">Old behavior</span></span>

<span data-ttu-id="3a074-239">Vor Version 3.0 von ASP.NET Core wurden EF Core und einige der zugehörigen Datenanbieter wie SQL Server eingeschlossen, wenn `Microsoft.AspNetCore.App` oder `Microsoft.AspNetCore.All` ein Paketverweis hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="3a074-239">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-240">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-240">New behavior</span></span>

<span data-ttu-id="3a074-241">Ab Version 3.0 enthält das gemeinsam verwendete ASP.NET Core-Framework weder EF Core noch zugehörige Datenanbieter.</span><span class="sxs-lookup"><span data-stu-id="3a074-241">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-242">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-242">Why</span></span>

<span data-ttu-id="3a074-243">Vor dieser Änderung konnte EF Core auf unterschiedliche Arten bezogen werden. Diese richteten sich danach, ob ASP.NET Core und SQL Server oder ein anderes Zielframework für die Anwendung vorgesehen war.</span><span class="sxs-lookup"><span data-stu-id="3a074-243">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span>
<span data-ttu-id="3a074-244">Bei einem Upgrade von ASP.NET Core wurde zudem ein Upgrade von EF Core und des SQL Server-Anbieters erzwungen, was nicht in allen Fällen gewünscht war.</span><span class="sxs-lookup"><span data-stu-id="3a074-244">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="3a074-245">Durch die eingeführte Änderung wird EF Core unter allen Anbietern, unterstützten .NET-Implementierungen und Anwendungstypen auf dieselbe Weise bezogen.</span><span class="sxs-lookup"><span data-stu-id="3a074-245">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="3a074-246">Entwickler können nun außerdem genau festlegen, wann für EF Core und zugehörige Datenanbieter ein Upgrade durchgeführt werden soll.</span><span class="sxs-lookup"><span data-stu-id="3a074-246">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-247">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-247">Mitigations</span></span>

<span data-ttu-id="3a074-248">Wenn Sie EF Core in einer Anwendung unter ASP.NET Core 3.0 oder in einer anderen unterstützten Anwendung verwenden möchten, müssen Sie dem EF Core-Datenbankanbieter, der für die Anwendung genutzt werden soll, explizit einen Paketverweis hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="3a074-248">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>

### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="3a074-249">Das EF Core-Befehlszeilentool (dotnet ef) ist nicht länger Bestandteil des .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="3a074-249">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="3a074-250">Issue #14016</span><span class="sxs-lookup"><span data-stu-id="3a074-250">Tracking Issue #14016</span></span>](https://github.com/dotnet/efcore/issues/14016)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-251">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-251">Old behavior</span></span>

<span data-ttu-id="3a074-252">Vor Version 3.0 war das `dotnet ef`-Tool im .NET Core SDK enthalten und konnte von der Befehlszeile aus ohne zusätzliche Schritte in einem beliebigen Projekt verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-252">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-253">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-253">New behavior</span></span>

<span data-ttu-id="3a074-254">Ab Version 3.0 ist das `dotnet ef`-Tool nicht mehr im .NET SDK enthalten und muss deshalb vor der Verwendung explizit als lokales oder globales Tool installiert werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-254">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-255">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-255">Why</span></span>

<span data-ttu-id="3a074-256">Durch diese Änderung kann `dotnet ef` als reguläres .NET-CLI-Tool für NuGet verteilt und aktualisiert werden und entspricht damit dem üblichen Verfahren, dass EF Core 3.0 ebenfalls immer als NuGet-Paket verteilt wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-256">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-257">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-257">Mitigations</span></span>

<span data-ttu-id="3a074-258">Um Migrationen zu verwalten oder ein Gerüst für `DbContext` zu erstellen, installieren Sie `dotnet-ef` als globales Tool:</span><span class="sxs-lookup"><span data-stu-id="3a074-258">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="3a074-259">Eine Verwendung als lokales Tool ist ebenfalls möglich, wenn Sie die Abhängigkeiten eines Projekts wiederherstellen, das das Tool mithilfe einer [Toolmanifestdatei](/dotnet/core/tools/global-tools#install-a-local-tool) als Toolabhängigkeit deklariert.</span><span class="sxs-lookup"><span data-stu-id="3a074-259">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](/dotnet/core/tools/global-tools#install-a-local-tool).</span></span>

## <a name="low-impact-changes"></a><span data-ttu-id="3a074-260">Änderungen mit geringer Auswirkung</span><span class="sxs-lookup"><span data-stu-id="3a074-260">Low-impact changes</span></span>

<a name="fromsql"></a>

### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="3a074-261">FromSql, ExecuteSql und ExecuteSqlAsync wurden umbenannt</span><span class="sxs-lookup"><span data-stu-id="3a074-261">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="3a074-262">Issue #10996</span><span class="sxs-lookup"><span data-stu-id="3a074-262">Tracking Issue #10996</span></span>](https://github.com/dotnet/efcore/issues/10996)

> [!IMPORTANT]
> <span data-ttu-id="3a074-263">`ExecuteSqlCommand` und `ExecuteSqlCommandAsync` sind veraltet.</span><span class="sxs-lookup"><span data-stu-id="3a074-263">`ExecuteSqlCommand` and `ExecuteSqlCommandAsync` are deprecated.</span></span> <span data-ttu-id="3a074-264">Verwenden Sie diese Methoden stattdessen.</span><span class="sxs-lookup"><span data-stu-id="3a074-264">Use these methods instead.</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="3a074-265">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-265">Old behavior</span></span>

<span data-ttu-id="3a074-266">Vor Version 3.0 wurden in EF Core diese Methodennamen überladen, um entweder mit einer normalen Zeichenfolge oder einer Zeichenfolge, die in SQL und Parameter interpoliert werden sollte, zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="3a074-266">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-267">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-267">New behavior</span></span>

<span data-ttu-id="3a074-268">Ab Version 3.0 verwenden Sie in EF Core `FromSqlRaw`, `ExecuteSqlRaw` und `ExecuteSqlRawAsync`, um eine parametrisierte Abfrage zu erstellen, bei der die Parameter einzeln aus der Abfragezeichenfolge übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-268">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="3a074-269">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a074-269">For example:</span></span>

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="3a074-270">Verwenden Sie `FromSqlInterpolated`, `ExecuteSqlInterpolated` und `ExecuteSqlInterpolatedAsync`, um eine parametrisierte Abfrage zu erstellen, bei der die Parameter als Teil einer interpolierten Abfragezeichenfolge übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-270">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="3a074-271">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a074-271">For example:</span></span>

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="3a074-272">Beachten Sie, dass die beiden oben genannten Abfragen dieselbe parametrisierte SQL mit denselben SQL-Parametern erzeugen.</span><span class="sxs-lookup"><span data-stu-id="3a074-272">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-273">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-273">Why</span></span>

<span data-ttu-id="3a074-274">Bei Methodenüberladungen wie dieser wird sehr leicht versehentlich die Methode für unformatierte Zeichenfolgen aufgerufen, wenn eigentlich beabsichtigt war, die Methode für interpolierte Zeichenfolgen aufzurufen (und umgekehrt).</span><span class="sxs-lookup"><span data-stu-id="3a074-274">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="3a074-275">Dadurch werden Abfragen möglicherweise nicht parametrisiert, obwohl dies der Fall sein sollte.</span><span class="sxs-lookup"><span data-stu-id="3a074-275">This could result in queries not being parameterized when they should have been.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-276">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-276">Mitigations</span></span>

<span data-ttu-id="3a074-277">Verwenden Sie ab sofort die neuen Methodennamen.</span><span class="sxs-lookup"><span data-stu-id="3a074-277">Switch to use the new method names.</span></span>

<a name="fromsqlsproc"></a>

### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a><span data-ttu-id="3a074-278">FromSql-Methode kann nicht zusammengesetzt werden, wenn sie mit einer gespeicherten Prozedur verwendet wird</span><span class="sxs-lookup"><span data-stu-id="3a074-278">FromSql method when used with stored procedure cannot be composed</span></span>

[<span data-ttu-id="3a074-279">Issue #15392</span><span class="sxs-lookup"><span data-stu-id="3a074-279">Tracking Issue #15392</span></span>](https://github.com/dotnet/efcore/issues/15392)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-280">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-280">Old behavior</span></span>

<span data-ttu-id="3a074-281">Vor EF Core 3.0 versuchte die FromSql-Methode, zu ermitteln, ob das übergebene SQL zusammengesetzt werden konnte.</span><span class="sxs-lookup"><span data-stu-id="3a074-281">Before EF Core 3.0, FromSql method tried to detect if the passed SQL can be composed upon.</span></span> <span data-ttu-id="3a074-282">Eine Clientauswertung wurde durchgeführt, wenn das SQL wie eine gespeicherte Prozedur nicht zusammensetzbar war.</span><span class="sxs-lookup"><span data-stu-id="3a074-282">It did client evaluation when the SQL was non-composable like a stored procedure.</span></span> <span data-ttu-id="3a074-283">Die folgende Abfrage funktionierte, indem die gespeicherte Prozedur auf dem Server und die FirstOrDefault-Methode auf dem Client ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="3a074-283">The following query worked by running the stored procedure on the server and doing FirstOrDefault on the client side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

#### <a name="new-behavior"></a><span data-ttu-id="3a074-284">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-284">New behavior</span></span>

<span data-ttu-id="3a074-285">Ab EF Core 3.0 versucht EF Core nicht mehr, das SQL zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="3a074-285">Starting with EF Core 3.0, EF Core will not try to parse the SQL.</span></span> <span data-ttu-id="3a074-286">Wenn Sie also nach FromSqlRaw/FromSqlInterpolated zusammensetzen, setzt EF Core das SQL zusammen, indem eine Unterabfrage ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-286">So if you are composing after FromSqlRaw/FromSqlInterpolated, then EF Core will compose the SQL by causing sub query.</span></span> <span data-ttu-id="3a074-287">Wenn Sie eine gespeicherte Prozedur mit Zusammensetzung verwenden, wird eine Ausnahme für ungültige SQL-Syntax ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="3a074-287">So if you are using a stored procedure with composition then you will get an exception for invalid SQL syntax.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-288">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-288">Why</span></span>

<span data-ttu-id="3a074-289">EF Core 3.0 unterstützt die automatische Clientauswertung nicht, da diese wie [hier](#linq-queries-are-no-longer-evaluated-on-the-client) erläutert fehleranfällig war.</span><span class="sxs-lookup"><span data-stu-id="3a074-289">EF Core 3.0 does not support automatic client evaluation, since it was error prone as explained [here](#linq-queries-are-no-longer-evaluated-on-the-client).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-290">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-290">Mitigations</span></span>

<span data-ttu-id="3a074-291">Wenn Sie eine gespeicherte Prozedur in FromSqlRaw/FromSqlInterpolated verwenden, wissen Sie, dass diese nicht zusammengesetzt werden kann. Daher können Sie __AsEnumerable/AsAsyncEnumerable__ direkt nach dem FromSql-Methodenaufruf hinzufügen, um die Zusammensetzung auf dem Server zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="3a074-291">If you are using a stored procedure in FromSqlRaw/FromSqlInterpolated, you know that it cannot be composed upon, so you can add __AsEnumerable/AsAsyncEnumerable__ right after the FromSql method call to avoid any composition on server side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="3a074-292">FromSql-Methoden können nur für die Stammelemente der Abfrage angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-292">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="3a074-293">Issue #15704</span><span class="sxs-lookup"><span data-stu-id="3a074-293">Tracking Issue #15704</span></span>](https://github.com/dotnet/efcore/issues/15704)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-294">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-294">Old behavior</span></span>

<span data-ttu-id="3a074-295">Vor EF Core 3.0 konnte die `FromSql`-Methode an einer beliebigen Stelle in der Abfrage angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-295">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-296">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-296">New behavior</span></span>

<span data-ttu-id="3a074-297">Ab EF Core 3.0 können die neuen Methoden `FromSqlRaw` und `FromSqlInterpolated` (durch die `FromSql` ersetzt wird) nur für Stammelemente von Abfragen angegeben werden, d.h. direkt für das `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="3a074-297">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="3a074-298">Der Versuch, sie an anderer Stelle anzugeben, führt zu einem Kompilierungsfehler.</span><span class="sxs-lookup"><span data-stu-id="3a074-298">Attempting to specify them anywhere else will result in a compilation error.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-299">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-299">Why</span></span>

<span data-ttu-id="3a074-300">Die Angabe von `FromSql` an einer anderen Stelle als für ein `DbSet` erbrachte keine zusätzliche Bedeutung und keinen Mehrwert, sondern konnte in bestimmten Szenarien zu Mehrdeutigkeiten führen.</span><span class="sxs-lookup"><span data-stu-id="3a074-300">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-301">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-301">Mitigations</span></span>

<span data-ttu-id="3a074-302">`FromSql` Aufrufe sollten so verschoben, dass sie direkt für das zugehörige `DbSet` gelten.</span><span class="sxs-lookup"><span data-stu-id="3a074-302">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>

### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="3a074-303">Abfragen ohne Nachverfolgung führen keine Identitätsauflösung mehr durch</span><span class="sxs-lookup"><span data-stu-id="3a074-303">No-tracking queries no longer perform identity resolution</span></span>

[<span data-ttu-id="3a074-304">Issue #13518</span><span class="sxs-lookup"><span data-stu-id="3a074-304">Tracking Issue #13518</span></span>](https://github.com/dotnet/efcore/issues/13518)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-305">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-305">Old behavior</span></span>

<span data-ttu-id="3a074-306">Vor EF Core 3.0 wurde dieselbe Entitätsinstanz für jedes Vorkommen einer Entität mit einem bestimmten Typ und einer bestimmten ID verwendet.</span><span class="sxs-lookup"><span data-stu-id="3a074-306">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="3a074-307">Dies entspricht dem Verhalten von Abfragen zu Nachverfolgungen.</span><span class="sxs-lookup"><span data-stu-id="3a074-307">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="3a074-308">Diese Abfrage zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a074-308">For example, this query:</span></span>

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```

<span data-ttu-id="3a074-309">gibt dieselbe `Category`-Instanz für jedes `Product` zurück, das der bestimmten Kategorie zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-309">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-310">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-310">New behavior</span></span>

<span data-ttu-id="3a074-311">Ab EF Core 3.0 werden unterschiedliche Entitätsinstanzen erstellt, wenn eine Entität mit einem bestimmten Typ und einer bestimmten ID an verschiedenen Stellen im zurückgegebenen Diagramm gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-311">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="3a074-312">Die Abfrage oben gibt beispielsweise nun eine neue `Category`-Instanz für jedes `Product` zurück, auch wenn zwei Produkte derselben Kategorie zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="3a074-312">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-313">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-313">Why</span></span>

<span data-ttu-id="3a074-314">Die Identitätsauflösung (d. h. Feststellen, dass eine Entität über denselben Typ und die dieselbe ID wie die zuvor aufgetretene Entität verfügt) fügt zusätzlichen Aufwand für Leistung und Arbeitsspeicher hinzu.</span><span class="sxs-lookup"><span data-stu-id="3a074-314">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="3a074-315">Dies widerspricht normalerweise dem Grund, warum in erster Linie Abfragen ohne Nachverfolgung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-315">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="3a074-316">Obwohl die Identitätsauflösung manchmal nützlich sein kann, wird sie nicht benötigt, wenn die Entitäten serialisiert und an den Client gesendet werden, was manchmal für Abfragen ohne Nachverfolgung der Fall ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-316">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-317">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-317">Mitigations</span></span>

<span data-ttu-id="3a074-318">Verwenden Sie eine Nachverfolgungsabfrage, falls die Auflösung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-318">Use a tracking query if identity resolution is required.</span></span>

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="3a074-319">Temporäre Schlüsselwerte werden nicht mehr Entitätsinstanzen zugewiesen</span><span class="sxs-lookup"><span data-stu-id="3a074-319">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="3a074-320">Issue #12378</span><span class="sxs-lookup"><span data-stu-id="3a074-320">Tracking Issue #12378</span></span>](https://github.com/dotnet/efcore/issues/12378)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-321">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-321">Old behavior</span></span>

<span data-ttu-id="3a074-322">Vor Version 3.0 wurden in EF Core allen Schlüsseleigenschaften temporäre Werte zugewiesen. Später wurden den Eigenschaften dann die tatsächlichen Werte zugewiesen, die von der Datenbank generiert wurden.</span><span class="sxs-lookup"><span data-stu-id="3a074-322">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="3a074-323">Die temporären Werte waren in der Regel große negative Zahlen.</span><span class="sxs-lookup"><span data-stu-id="3a074-323">Usually these temporary values were large negative numbers.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-324">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-324">New behavior</span></span>

<span data-ttu-id="3a074-325">Ab Version 3.0 wird in EF Core der temporäre Wert als Teil der Überwachungsinformationen der Entität gespeichert. Die Schlüsseleigenschaft selbst bleibt unverändert.</span><span class="sxs-lookup"><span data-stu-id="3a074-325">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-326">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-326">Why</span></span>

<span data-ttu-id="3a074-327">Diese Änderung wurde vorgenommen, damit temporäre Schlüsselwerte nicht fälschlicherweise dauerhaft gespeichert werden, wenn eine Entität, die vorher von einer `DbContext`-Instanz überwacht wurde, in eine andere `DbContext`-Instanz verschoben wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-327">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-328">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-328">Mitigations</span></span>

<span data-ttu-id="3a074-329">Anwendungen, die Primärschlüsselwerte Fremdschlüsseln zuweisen, um Zuordnungen zwischen Entitäten zu erstellen, können vom alten Verhalten abhängig sein, wenn Primärschlüssel vom Speicher generiert werden und zu Entitäten im `Added`-Zustand gehören.</span><span class="sxs-lookup"><span data-stu-id="3a074-329">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="3a074-330">Sie können dies wie folgt vermeiden:</span><span class="sxs-lookup"><span data-stu-id="3a074-330">This can be avoided by:</span></span>

* <span data-ttu-id="3a074-331">Verwenden Sie keine vom Speicher generierten Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="3a074-331">Not using store-generated keys.</span></span>
* <span data-ttu-id="3a074-332">Legen Sie zum Erstellen von Zuordnungen keine Fremdschlüsselwerte, sondern Navigationseigenschaften fest.</span><span class="sxs-lookup"><span data-stu-id="3a074-332">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="3a074-333">Rufen Sie die tatsächlichen temporären Schlüsselwerte aus den Überwachungsinformationen der Entität ab.</span><span class="sxs-lookup"><span data-stu-id="3a074-333">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="3a074-334">`context.Entry(blog).Property(e => e.Id).CurrentValue` gibt beispielsweise den temporären Wert zurück, obwohl `blog.Id` noch nicht festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="3a074-334">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="3a074-335">DetectChanges berücksichtigt vom Speicher generierte Schlüsselwerte</span><span class="sxs-lookup"><span data-stu-id="3a074-335">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="3a074-336">Issue #14616</span><span class="sxs-lookup"><span data-stu-id="3a074-336">Tracking Issue #14616</span></span>](https://github.com/dotnet/efcore/issues/14616)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-337">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-337">Old behavior</span></span>

<span data-ttu-id="3a074-338">Vor Version 3.0 wurde in EF Core eine nicht überwachte Entität, die von `DetectChanges` gefunden wurde, im `Added`-Zustand überwacht und als neue Zeile eingefügt, sobald `SaveChanges` aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="3a074-338">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-339">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-339">New behavior</span></span>

<span data-ttu-id="3a074-340">Ab Version 3.0 wird in EF Core die Entität im `Modified`-Zustand überwacht, wenn für diese generierte Schlüsselwerte verwendet werden und ein Schlüsselwert festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-340">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="3a074-341">Es wird also davon ausgegangen, dass eine Zeile für die Entität vorhanden ist. Wenn `SaveChanges` aufgerufen wird, wird die Zeile außerdem aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="3a074-341">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="3a074-342">Falls kein Schlüsselwert festgelegt ist oder der Entitätstyp keine generierten Schlüssel verwendet, wird die neue Entität wie in früheren Version weiterhin im `Added`-Zustand überwacht.</span><span class="sxs-lookup"><span data-stu-id="3a074-342">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-343">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-343">Why</span></span>

<span data-ttu-id="3a074-344">Diese Änderung wurde vorgenommen, um bei der Verwendung von speichergenerierten Schlüsseln die Arbeit mit nicht verbundenen Entitätsgraphen einfacher und konsistenter zu gestalten.</span><span class="sxs-lookup"><span data-stu-id="3a074-344">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-345">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-345">Mitigations</span></span>

<span data-ttu-id="3a074-346">Diese Änderung kann dazu führen, dass eine Anwendung nicht mehr funktioniert. Dieser Fall tritt ein, wenn für Entitätstypen generierte Schlüssel vorgesehen sind, dann jedoch explizit Schlüsselwerte für neue Instanzen festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-346">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="3a074-347">Sie können dieses Problem vermeiden, indem Sie explizit festlegen, dass für Schlüsseleigenschaften keine generierten Werte verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="3a074-347">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="3a074-348">Dies ist beispielsweise mit der Fluent-API möglich:</span><span class="sxs-lookup"><span data-stu-id="3a074-348">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="3a074-349">Alternativ bieten sich auch Datenanmerkungen an:</span><span class="sxs-lookup"><span data-stu-id="3a074-349">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

<a name="cascade"></a>

### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="3a074-350">Kaskadierende Deletes werden standardmäßig sofort ausgeführt</span><span class="sxs-lookup"><span data-stu-id="3a074-350">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="3a074-351">Issue #10114</span><span class="sxs-lookup"><span data-stu-id="3a074-351">Tracking Issue #10114</span></span>](https://github.com/dotnet/efcore/issues/10114)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-352">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-352">Old behavior</span></span>

<span data-ttu-id="3a074-353">Vor Version 3.0 wurden in EF Core kaskadierenden Aktionen (Löschen von abhängigen Entitäten nach dem Löschen eines erforderlichen Prinzipals oder nach dem Aufheben einer Beziehung zum erforderlichen Prinzipal) erst ausgeführt, wenn SaveChanges aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="3a074-353">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-354">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-354">New behavior</span></span>

<span data-ttu-id="3a074-355">Ab Version 3.0 werden in EF Core kaskadierende Aktionen angewendet, sobald die auslösende Bedingung erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-355">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="3a074-356">Wenn beispielsweise `context.Remove()` zum Löschen einer Prinzipalentität aufgerufen wird, führt das dazu, dass auch alle zugehörigen erforderlichen Entitäten, die überwacht werden, sofort auf `Deleted` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-356">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-357">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-357">Why</span></span>

<span data-ttu-id="3a074-358">Diese Änderung wurde vorgenommen, um die Arbeit mit Datenbindungen und Überwachungsszenarios zu vereinfachen, in denen bekannt sein muss, welche Entitäten _vor dem Aufruf von_  `SaveChanges` gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-358">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-359">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-359">Mitigations</span></span>

<span data-ttu-id="3a074-360">Sie können das vorherige Verhalten wiederherstellen, indem Sie für `context.ChangeTracker` die entsprechenden Einstellungen vornehmen.</span><span class="sxs-lookup"><span data-stu-id="3a074-360">The previous behavior can be restored through settings on `context.ChangeTracker`.</span></span>
<span data-ttu-id="3a074-361">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a074-361">For example:</span></span>

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

<a name="eager-loading-single-query"></a>

### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a><span data-ttu-id="3a074-362">Eager Loading von verwandten Entitäten erfolgt nun in einer einzelnen Abfrage</span><span class="sxs-lookup"><span data-stu-id="3a074-362">Eager loading of related entities now happens in a single query</span></span>

[<span data-ttu-id="3a074-363">Issue #18022</span><span class="sxs-lookup"><span data-stu-id="3a074-363">Tracking issue #18022</span></span>](https://github.com/dotnet/efcore/issues/18022)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-364">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-364">Old behavior</span></span>

<span data-ttu-id="3a074-365">Vor Version 3.0 löste Eager Loading von Sammlungsnavigationen über `Include`-Operatoren die Generierung mehrerer Abfragen auf der relationalen Datenbank aus. Eine Abfrage für jeden verwandten Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="3a074-365">Before 3.0, eagerly loading collection navigations via `Include` operators caused multiple queries to be generated on relational database, one for each related entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-366">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-366">New behavior</span></span>

<span data-ttu-id="3a074-367">Ab Version 3.0 generiert EF Core eine einzelne Abfrage mit JOINs für relationale Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="3a074-367">Starting with 3.0, EF Core generates a single query with JOINs on relational databases.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-368">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-368">Why</span></span>

<span data-ttu-id="3a074-369">Das Ausgeben mehrerer Abfragen zum Implementieren einer einzelnen LINQ-Abfrage führte zu einer Vielzahl von Problemen, darunter Leistungsbeeinträchtigungen, da mehrere Datenbankroundtrips erforderlich waren, und Datenkohärenzprobleme, da jede Abfrage einen anderen Zustand der Datenbank beobachten könnte.</span><span class="sxs-lookup"><span data-stu-id="3a074-369">Issuing multiple queries to implement a single LINQ query caused numerous issues, including negative performance as multiple database roundtrips were necessary, and data coherency issues as each query could observe a different state of the database.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-370">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-370">Mitigations</span></span>

<span data-ttu-id="3a074-371">Zwar ist dies technisch gesehen kein Breaking Change, jedoch könnte es erhebliche Auswirkungen auf die Leistung der Anwendung haben, wenn eine einzelne Abfrage eine große Anzahl von `Include`-Operatoren für Sammlungsnavigationen enthält.</span><span class="sxs-lookup"><span data-stu-id="3a074-371">While technically this is not a breaking change, it could have a considerable effect on application performance when a single query contains a large number of `Include` operator on collection navigations.</span></span> <span data-ttu-id="3a074-372">Weitere Informationen sowie Informationen zum Umschreiben von Abfragen auf eine effizientere Weise finden Sie [in diesem Kommentar](https://github.com/dotnet/efcore/issues/18022#issuecomment-542397085).</span><span class="sxs-lookup"><span data-stu-id="3a074-372">[See this comment](https://github.com/dotnet/efcore/issues/18022#issuecomment-542397085) for more information and for rewriting queries in a more efficient way.</span></span>

**

<a name="deletebehavior"></a>

### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="3a074-373">DeleteBehavior.Restrict verfügt über eine übersichtlichere Semantik</span><span class="sxs-lookup"><span data-stu-id="3a074-373">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="3a074-374">Issue #12661</span><span class="sxs-lookup"><span data-stu-id="3a074-374">Tracking Issue #12661</span></span>](https://github.com/dotnet/efcore/issues/12661)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-375">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-375">Old behavior</span></span>

<span data-ttu-id="3a074-376">Vor Version 3.0 wurden mit `DeleteBehavior.Restrict` Fremdschlüssel in der Datenbank mit `Restrict`-Semantik erstellt, es wurden aber auch unvorhergesehen interne Fixups geändert.</span><span class="sxs-lookup"><span data-stu-id="3a074-376">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-377">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-377">New behavior</span></span>

<span data-ttu-id="3a074-378">Ab Version 3.0 wird mit `DeleteBehavior.Restrict` sichergestellt, dass Fremdschlüssel mit `Restrict`-Semantik erstellt werden – d. h. ohne Überlappungen; ausgenommen bei Einschränkungsverletzungen – und ohne Auswirkungen auf EF-interne Fixups.</span><span class="sxs-lookup"><span data-stu-id="3a074-378">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-379">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-379">Why</span></span>

<span data-ttu-id="3a074-380">Diese Änderung wurde vorgenommen, um die Benutzerfreundlichkeit bei intuitiver Verwendung von `DeleteBehavior` ohne unerwartete Nebeneffekte zu verbessern.</span><span class="sxs-lookup"><span data-stu-id="3a074-380">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-381">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-381">Mitigations</span></span>

<span data-ttu-id="3a074-382">Sie können das vorherige Verhalten wiederherstellen, indem Sie `DeleteBehavior.ClientNoAction` verwenden.</span><span class="sxs-lookup"><span data-stu-id="3a074-382">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>

### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="3a074-383">Abfragetypen werden mit Entitätstypen zusammengeführt</span><span class="sxs-lookup"><span data-stu-id="3a074-383">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="3a074-384">Issue #14194</span><span class="sxs-lookup"><span data-stu-id="3a074-384">Tracking Issue #14194</span></span>](https://github.com/dotnet/efcore/issues/14194)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-385">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-385">Old behavior</span></span>

<span data-ttu-id="3a074-386">Vor Version 3.0 wurden in EF Core [Abfragetypen](xref:core/modeling/keyless-entity-types) dazu verwendet, Daten abzufragen, in denen kein Primärschlüssel auf strukturierte Weise festgelegt war.</span><span class="sxs-lookup"><span data-stu-id="3a074-386">Before EF Core 3.0, [query types](xref:core/modeling/keyless-entity-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="3a074-387">Abfragetypen wurden also für die Zuordnung von Entitätstypen ohne Schlüssel verwendet (in der Regel in einer Sicht, in einigen Fällen aber auch in einer Tabelle), während reguläre Entitätstypen für einen verfügbaren Schlüssel verwendet wurden (in der Regel in einer Tabelle, in einigen Fällen aber auch in einer Sicht).</span><span class="sxs-lookup"><span data-stu-id="3a074-387">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-388">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-388">New behavior</span></span>

<span data-ttu-id="3a074-389">Aus einem Abfragetyp wird nun einfach ein Entitätstyp ohne Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="3a074-389">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="3a074-390">Entitätstypen ohne Schlüssel besitzen die gleiche Funktionalität wie Abfragetypen in früheren Versionen.</span><span class="sxs-lookup"><span data-stu-id="3a074-390">Keyless entity types have the same functionality as query types in previous versions.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-391">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-391">Why</span></span>

<span data-ttu-id="3a074-392">Diese Änderung wurde vorgenommen, um Unklarheiten bei der Verwendung von Abfragetypen zu beseitigen.</span><span class="sxs-lookup"><span data-stu-id="3a074-392">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="3a074-393">Abfragetypen sind schlüssellose Entitätstypen, die grundsätzlich schreibgeschützt sind. Sie sollten allerdings nicht allein wegen des Schreibschutzes verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-393">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="3a074-394">Des Weiteren werden sie oft Sichten zugeordnet, was aber nur daran liegt, dass für Letztere häufig keine Schlüssel definiert werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-394">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-395">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-395">Mitigations</span></span>

<span data-ttu-id="3a074-396">Die folgenden Teile der API sind durch die Änderungen veraltet:</span><span class="sxs-lookup"><span data-stu-id="3a074-396">The following parts of the API are now obsolete:</span></span>

* <span data-ttu-id="3a074-397">**`ModelBuilder.Query<>()`** : Rufen Sie stattdessen `ModelBuilder.Entity<>().HasNoKey()` auf, um einen schlüssellosen Entitätstyp festzulegen.</span><span class="sxs-lookup"><span data-stu-id="3a074-397">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="3a074-398">Dieses Verhalten wird nach wie vor nicht konventionsgemäß festgelegt, um Fehlkonfigurationen zu vermeiden, wenn ein Primärschlüssel erwartet wird, jedoch nicht mit der Konvention übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="3a074-398">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="3a074-399">**`DbQuery<>`** : Verwenden Sie stattdessen `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="3a074-399">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="3a074-400">**`DbContext.Query<>()`** : Verwenden Sie stattdessen `DbContext.Set<>()`.</span><span class="sxs-lookup"><span data-stu-id="3a074-400">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>
* <span data-ttu-id="3a074-401">**`IQueryTypeConfiguration<TQuery>`** : Verwenden Sie stattdessen `IEntityTypeConfiguration<TEntity>`.</span><span class="sxs-lookup"><span data-stu-id="3a074-401">**`IQueryTypeConfiguration<TQuery>`** - Instead `IEntityTypeConfiguration<TEntity>` should be used.</span></span>

> [!NOTE]
> <span data-ttu-id="3a074-402">Aufgrund [eines Problems in Version 3.x](https://github.com/dotnet/efcore/issues/19537) beim Abfragen von schlüssellosen Entitäten, bei denen alle Eigenschaften auf `null` festgelegt sind, wird anstelle einer Entität ein `null` zurückgegeben. Wenn dieses Problem auf Ihr Szenario zutrifft, fügen Sie außerdem eine Logik zur Verarbeitung von `null` in Ergebnissen hinzu.</span><span class="sxs-lookup"><span data-stu-id="3a074-402">Due to [an issue in 3.x](https://github.com/dotnet/efcore/issues/19537) when querying keyless entities that have all properties set to `null` a `null` will be returned instead of an entity, if this issue is applicable to your scenario also add logic to handle `null` in results.</span></span>

<a name="config"></a>

### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="3a074-403">Die Konfigurations-API für Beziehungen abhängiger (owned) Typen wurde geändert</span><span class="sxs-lookup"><span data-stu-id="3a074-403">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="3a074-404">[Issue #12444](https://github.com/dotnet/efcore/issues/12444)
[Issue #9148](https://github.com/dotnet/efcore/issues/9148)
[Issue #14153](https://github.com/dotnet/efcore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="3a074-404">[Tracking Issue #12444](https://github.com/dotnet/efcore/issues/12444)
[Tracking Issue #9148](https://github.com/dotnet/efcore/issues/9148)
[Tracking Issue #14153](https://github.com/dotnet/efcore/issues/14153)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="3a074-405">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-405">Old behavior</span></span>

<span data-ttu-id="3a074-406">Vor Version 3.0 wurde in EF Core die abhängige Beziehung direkt nach dem Aufruf von `OwnsOne` oder `OwnsMany` konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="3a074-406">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-407">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-407">New behavior</span></span>

<span data-ttu-id="3a074-408">Ab Version 3.0 von EF Core ist eine Fluent-API verfügbar, mit der über `WithOwner()` eine Navigationseigenschaft für den Besitzer konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="3a074-408">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="3a074-409">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a074-409">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="3a074-410">Die Konfiguration für die Beziehung zwischen Besitzertyp und abhängigem Typ sollte nun ähnlich wie bei der Konfiguration anderer Beziehungen nach `WithOwner()` verkettet werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-410">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="3a074-411">Die Konfiguration für den abhängigen Typ wird jedoch weiterhin nach `OwnsOne()/OwnsMany()` verkettet.</span><span class="sxs-lookup"><span data-stu-id="3a074-411">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="3a074-412">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a074-412">For example:</span></span>

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

<span data-ttu-id="3a074-413">Wenn Sie zusätzlich `Entity()`, `HasOne()`, oder `Set()` für das Ziel eines abhängigen Typs aufrufen, wird keine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="3a074-413">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-414">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-414">Why</span></span>

<span data-ttu-id="3a074-415">Diese Änderung wurde vorgenommen, um eine deutlichere Trennung zwischen der Konfiguration des abhängigen Typs und der _Beziehung zum abhängigen Typ_ zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="3a074-415">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="3a074-416">Dadurch werden für Methoden wie `HasForeignKey` Mehrdeutigkeiten und Unklarheiten beseitigt.</span><span class="sxs-lookup"><span data-stu-id="3a074-416">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-417">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-417">Mitigations</span></span>

<span data-ttu-id="3a074-418">Passen Sie für die Beziehungen von abhängigen Typen die Konfiguration so an, dass die neue Fluent-API wie im obigen Beispiel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-418">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="3a074-419">Abhängige Entitäten, die die Tabelle gemeinsam mit dem Prinzipal verwenden, sind jetzt optional</span><span class="sxs-lookup"><span data-stu-id="3a074-419">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="3a074-420">Issue #9005</span><span class="sxs-lookup"><span data-stu-id="3a074-420">Tracking Issue #9005</span></span>](https://github.com/dotnet/efcore/issues/9005)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-421">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-421">Old behavior</span></span>

<span data-ttu-id="3a074-422">Sehen Sie sich das folgende Modell an:</span><span class="sxs-lookup"><span data-stu-id="3a074-422">Consider the following model:</span></span>

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

<span data-ttu-id="3a074-423">Wenn `OrderDetails` im Besitz von `Order` ist oder explizit derselben Tabelle zugeordnet ist, war vor Version 3.0 in EF Core immer eine `OrderDetails`-Instanz beim Hinzufügen eines neuen `Order`-Objekts erforderlich.</span><span class="sxs-lookup"><span data-stu-id="3a074-423">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-424">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-424">New behavior</span></span>

<span data-ttu-id="3a074-425">Ab Version 3.0 bietet EF Core die Möglichkeit, `Order` ohne `OrderDetails` hinzuzufügen, und alle `OrderDetails`-Eigenschaften, mit Ausnahme des primären Schlüssels, werden Spalten zugeordnet, die NULL-Werte zulassen.</span><span class="sxs-lookup"><span data-stu-id="3a074-425">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="3a074-426">Bei Abfragen von EF Core wird `OrderDetails` auf `null` festgelegt, wenn eine der erforderlichen Eigenschaften keinen Wert aufweist oder keine erforderlichen Eigenschaften außer dem primären Schlüssel vorhanden und alle Eigenschaften `null` sind.</span><span class="sxs-lookup"><span data-stu-id="3a074-426">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-427">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-427">Mitigations</span></span>

<span data-ttu-id="3a074-428">Wenn Ihr Modell von der gemeinsamen Nutzung einer Tabelle mit allen optionalen Spalten abhängig ist, aber die Navigation, die darauf zeigt, wahrscheinlich nicht `null` ist, sollte die Anwendung für die Handhabung von Fällen geändert werden, in denen die Navigation `null` ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-428">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="3a074-429">Wenn das nicht möglich ist, sollte dem Entitätstyp eine erforderliche Eigenschaft hinzugefügt werden oder mindestens einer Eigenschaft sollte ein Wert ungleich `null` zugewiesen sein.</span><span class="sxs-lookup"><span data-stu-id="3a074-429">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="3a074-430">Alle Entitäten, die eine Tabelle mit einer Spalte für das Parallelitätstoken gemeinsam verwenden, müssen diese einer Eigenschaft zuordnen</span><span class="sxs-lookup"><span data-stu-id="3a074-430">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="3a074-431">Issue #14154</span><span class="sxs-lookup"><span data-stu-id="3a074-431">Tracking Issue #14154</span></span>](https://github.com/dotnet/efcore/issues/14154)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-432">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-432">Old behavior</span></span>

<span data-ttu-id="3a074-433">Sehen Sie sich das folgende Modell an:</span><span class="sxs-lookup"><span data-stu-id="3a074-433">Consider the following model:</span></span>

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

<span data-ttu-id="3a074-434">Wenn `OrderDetails` im Besitz von `Order` ist oder explizit derselben Tabelle zugeordnet ist, wird vor Version 3.0 in EF Core durch das alleinige Aktualisieren von `OrderDetails` der `Version`-Wert auf dem Client nicht aktualisiert, und das nächste Update schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="3a074-434">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-435">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-435">New behavior</span></span>

<span data-ttu-id="3a074-436">Ab Version 3.0 gibt EF Core den neuen `Version`-Wert an `Order` weiter, wenn dies Besitzer von `OrderDetails` ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-436">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="3a074-437">Andernfalls wird eine Ausnahme während der Modellvalidierung ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="3a074-437">Otherwise an exception is thrown during model validation.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-438">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-438">Why</span></span>

<span data-ttu-id="3a074-439">Diese Änderung wurde vorgenommen, um einen veralteten Wert für ein Parallelitätstoken zu vermeiden, wenn nur eine der Entitäten, die derselben Tabelle zugeordnet sind, aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-439">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-440">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-440">Mitigations</span></span>

<span data-ttu-id="3a074-441">Alle Entitäten, die die Tabelle gemeinsam nutzen, müssen eine Eigenschaft enthalten, die der Spalte für das Parallelitätstoken zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-441">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="3a074-442">Es ist möglich, eine solche im Schattenzustand zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="3a074-442">It's possible the create one in shadow-state:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a><span data-ttu-id="3a074-443">Benutzereigene Entitäten können nicht abgefragt werden, ohne dass der Besitzer eine Nachverfolgungsabfrage verwendet</span><span class="sxs-lookup"><span data-stu-id="3a074-443">Owned entities cannot be queried without the owner using a tracking query</span></span>

[<span data-ttu-id="3a074-444">Issue 18876</span><span class="sxs-lookup"><span data-stu-id="3a074-444">Tracking Issue #18876</span></span>](https://github.com/dotnet/efcore/issues/18876)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-445">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-445">Old behavior</span></span>

<span data-ttu-id="3a074-446">Vor EF Core 3.0 konnten die benutzereigenen Entitäten wie jedes andere Navigationselement abgefragt werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-446">Before EF Core 3.0, the owned entities could be queried as any other navigation.</span></span>

```csharp
context.People.Select(p => p.Address);
```

#### <a name="new-behavior"></a><span data-ttu-id="3a074-447">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-447">New behavior</span></span>

<span data-ttu-id="3a074-448">Ab EF Core 3.0 wird eine Ausnahme ausgelöst, wenn eine Nachverfolgungsabfrage eine benutzereigene Entität ohne den Besitzer projiziert.</span><span class="sxs-lookup"><span data-stu-id="3a074-448">Starting with 3.0, EF Core will throw if a tracking query projects an owned entity without the owner.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-449">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-449">Why</span></span>

<span data-ttu-id="3a074-450">Benutzereigene Entitäten können ohne den Besitzer nicht bearbeitet werden, daher handelt es sich in den meisten Fällen um einen Fehler, wenn sie auf diese Weise abgefragt werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-450">Owned entities cannot be manipulated without the owner, so in the vast majority of cases querying them in this way is an error.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-451">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-451">Mitigations</span></span>

<span data-ttu-id="3a074-452">Wenn die benutzereigene Entität nachverfolgt werden soll, damit sie später Änderungen an ihr vorgenommen werden können, sollte der Besitzer in der Abfrage enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="3a074-452">If the owned entity should be tracked to be modified in any way later then the owner should be included in the query.</span></span>

<span data-ttu-id="3a074-453">Fügen Sie andernfalls einen `AsNoTracking()`-Aufruf hinzu:</span><span class="sxs-lookup"><span data-stu-id="3a074-453">Otherwise add an `AsNoTracking()` call:</span></span>

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="3a074-454">Geerbte Eigenschaften von nicht zugeordneten Typen sind nun einer einzelnen Spalte für alle abgeleiteten Typen zugeordnet</span><span class="sxs-lookup"><span data-stu-id="3a074-454">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="3a074-455">Issue #13998</span><span class="sxs-lookup"><span data-stu-id="3a074-455">Tracking Issue #13998</span></span>](https://github.com/dotnet/efcore/issues/13998)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-456">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-456">Old behavior</span></span>

<span data-ttu-id="3a074-457">Sehen Sie sich das folgende Modell an:</span><span class="sxs-lookup"><span data-stu-id="3a074-457">Consider the following model:</span></span>

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

<span data-ttu-id="3a074-458">Vor Version 3.0 wurde in EF Core die Eigenschaft `ShippingAddress` standardmäßig separaten Spalten für `BulkOrder` und `Order` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="3a074-458">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-459">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-459">New behavior</span></span>

<span data-ttu-id="3a074-460">Ab Version 3.0 erstellt EF Core nur eine Spalte für `ShippingAddress`.</span><span class="sxs-lookup"><span data-stu-id="3a074-460">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-461">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-461">Why</span></span>

<span data-ttu-id="3a074-462">Der alte Verhalten war unerwartet.</span><span class="sxs-lookup"><span data-stu-id="3a074-462">The old behavoir was unexpected.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-463">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-463">Mitigations</span></span>

<span data-ttu-id="3a074-464">Die Eigenschaft kann noch immer explizit einer separaten Spalte für die abgeleiteten Typen zugeordnet werden:</span><span class="sxs-lookup"><span data-stu-id="3a074-464">The property can still be explicitly mapped to separate column on the derived types:</span></span>

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

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="3a074-465">Die Konvention zur Fremdschlüsseleigenschaft entspricht nicht mehr dem Namen der Prinzipaleigenschaft</span><span class="sxs-lookup"><span data-stu-id="3a074-465">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="3a074-466">Issue #13274</span><span class="sxs-lookup"><span data-stu-id="3a074-466">Tracking Issue #13274</span></span>](https://github.com/dotnet/efcore/issues/13274)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-467">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-467">Old behavior</span></span>

<span data-ttu-id="3a074-468">Sehen Sie sich das folgende Modell an:</span><span class="sxs-lookup"><span data-stu-id="3a074-468">Consider the following model:</span></span>

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

<span data-ttu-id="3a074-469">Vor Version 3.0 wurde in EF Core gemäß der Konvention die `CustomerId`-Eigenschaft für den Fremdschlüssel verwendet.</span><span class="sxs-lookup"><span data-stu-id="3a074-469">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="3a074-470">Wenn `Order` jedoch ein abhängiger Typ ist, wäre `CustomerId` der Primärschlüssel, was normalerweise nicht der Erwartungshaltung entspricht.</span><span class="sxs-lookup"><span data-stu-id="3a074-470">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-471">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-471">New behavior</span></span>

<span data-ttu-id="3a074-472">Ab Version 3.0 werden in EF Core konventionsgemäß keine Eigenschaften mehr für Fremdschlüssel verwendet, wenn diese denselben Namen wie die Prinzipaleigenschaft besitzen.</span><span class="sxs-lookup"><span data-stu-id="3a074-472">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="3a074-473">Die Muster für den Namen des Prinzipaltyps, der mit dem Namen der Prinzipaleigenschaft verkettet wird, und für den Navigationsnamen, der mit dem Namen der Prinzipaleigenschaft verkettet wird, werden jedoch weiterhin abgeglichen.</span><span class="sxs-lookup"><span data-stu-id="3a074-473">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="3a074-474">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a074-474">For example:</span></span>

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

#### <a name="why"></a><span data-ttu-id="3a074-475">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-475">Why</span></span>

<span data-ttu-id="3a074-476">Diese Änderung wurde vorgenommen, um zu vermeiden, dass versehentlich eine Primärschlüsseleigenschaft für den abhängigen Typ definiert wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-476">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-477">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-477">Mitigations</span></span>

<span data-ttu-id="3a074-478">Wenn die Eigenschaft als Fremdschlüssel und daher Teil des Primärschlüssels vorgesehen war, müssen Sie diese explizit als solche festlegen.</span><span class="sxs-lookup"><span data-stu-id="3a074-478">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="3a074-479">Die Datenbankverbindung wird jetzt geschlossen, wenn sie nicht mehr verwendet wird, bevor TransactionScope abgeschlossen wurde</span><span class="sxs-lookup"><span data-stu-id="3a074-479">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="3a074-480">Issue #14218</span><span class="sxs-lookup"><span data-stu-id="3a074-480">Tracking Issue #14218</span></span>](https://github.com/dotnet/efcore/issues/14218)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-481">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-481">Old behavior</span></span>

<span data-ttu-id="3a074-482">Wenn vor Version 3.0 in EF Core der Kontext die Verbindung in einem `TransactionScope` öffnet, bleibt die Verbindung geöffnet, während der aktuelle `TransactionScope` aktiv ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-482">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

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

#### <a name="new-behavior"></a><span data-ttu-id="3a074-483">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-483">New behavior</span></span>

<span data-ttu-id="3a074-484">Ab Version 3.0 schließt EF Core die Verbindung, sobald sie nicht mehr verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-484">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-485">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-485">Why</span></span>

<span data-ttu-id="3a074-486">Diese Änderung ermöglicht die Verwendung mehrerer Kontexte in demselben `TransactionScope`.</span><span class="sxs-lookup"><span data-stu-id="3a074-486">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="3a074-487">Das neue Verhalten entspricht außerdem EF6.</span><span class="sxs-lookup"><span data-stu-id="3a074-487">The new behavior also matches EF6.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-488">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-488">Mitigations</span></span>

<span data-ttu-id="3a074-489">Wenn die Verbindung geöffnet bleiben muss, wird durch einen expliziten Aufruf von `OpenConnection()` sichergestellt, dass EF Core diese nicht vorzeitig schließt:</span><span class="sxs-lookup"><span data-stu-id="3a074-489">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

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

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="3a074-490">Für jede Eigenschaft wird separat ein ganzzahliger speicherinterner Schlüssel generiert</span><span class="sxs-lookup"><span data-stu-id="3a074-490">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="3a074-491">Issue #6872</span><span class="sxs-lookup"><span data-stu-id="3a074-491">Tracking Issue #6872</span></span>](https://github.com/dotnet/efcore/issues/6872)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-492">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-492">Old behavior</span></span>

<span data-ttu-id="3a074-493">Vor Version 3.0 wurde in EF Core ein gemeinsam verwendeter Wertegenerator für alle speicherinternen ganzzahligen Schlüsseleigenschaften verwendet.</span><span class="sxs-lookup"><span data-stu-id="3a074-493">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-494">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-494">New behavior</span></span>

<span data-ttu-id="3a074-495">Ab Version 3.0 von EF Core wird für jede ganzzahlige Schlüsseleigenschaft ein eigener Wertegenerator bei der Verwendung der In-Memory Database verwendet.</span><span class="sxs-lookup"><span data-stu-id="3a074-495">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="3a074-496">Wenn die Datenbank gelöscht wird, wird die Schlüsselgenerierung für alle Tabellen zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="3a074-496">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-497">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-497">Why</span></span>

<span data-ttu-id="3a074-498">Diese Änderung wurde vorgenommen, um die speicherinterne Schlüsselgenerierung enger mit der Schlüsselgenerierung für echte Datenbanken abzustimmen. Außerdem sollen bei Verwendung der In-Memory Database Tests leichter voneinander isoliert werden können.</span><span class="sxs-lookup"><span data-stu-id="3a074-498">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-499">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-499">Mitigations</span></span>

<span data-ttu-id="3a074-500">Wenn Anwendungen von bestimmten speicherinternen Schlüsselwerten abhängig sind, funktionieren Erstere durch die eingeführte Änderung möglicherweise nicht mehr.</span><span class="sxs-lookup"><span data-stu-id="3a074-500">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="3a074-501">Versuchen Sie, Abhängigkeiten von bestimmten Schlüsselwerten zu vermeiden, oder passen Sie die Anwendung an das neue Verhalten an.</span><span class="sxs-lookup"><span data-stu-id="3a074-501">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="3a074-502">Unterstützungsfelder werden standardmäßig verwendet</span><span class="sxs-lookup"><span data-stu-id="3a074-502">Backing fields are used by default</span></span>

[<span data-ttu-id="3a074-503">Issue #12430</span><span class="sxs-lookup"><span data-stu-id="3a074-503">Tracking Issue #12430</span></span>](https://github.com/dotnet/efcore/issues/12430)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-504">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-504">Old behavior</span></span>

<span data-ttu-id="3a074-505">Vor Version 3.0 wurde in EF Core mithilfe der Getter- und Settermethoden standardmäßig der Eigenschaftswert gelesen und geschrieben. Das war selbst dann der Fall, wenn das Unterstützungsfeld für eine Eigenschaft nicht bekannt war.</span><span class="sxs-lookup"><span data-stu-id="3a074-505">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="3a074-506">Eine Ausnahme war die Abfrageausführung, bei der das Unterstützungsfeld direkt festgelegt wurde, wenn es bekannt war.</span><span class="sxs-lookup"><span data-stu-id="3a074-506">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-507">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-507">New behavior</span></span>

<span data-ttu-id="3a074-508">Ab Version 3.0 wird in EF Core die Eigenschaft mithilfe des Unterstützungsfelds gelesen und geschrieben, wenn dieses für eine Eigenschaft bekannt ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-508">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="3a074-509">Dies kann dazu führen, dass eine Anwendung nicht mehr funktioniert, wenn sie auf zusätzliches Verhalten angewiesen ist, das im Code der Getter- und Settermethoden definiert ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-509">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-510">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-510">Why</span></span>

<span data-ttu-id="3a074-511">Diese Änderung wurde vorgenommen, um zu verhindern, dass in EF Core fälschlicherweise immer dann Geschäftslogik ausgelöst wird, wenn Datenbankvorgänge für Entitäten durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-511">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-512">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-512">Mitigations</span></span>

<span data-ttu-id="3a074-513">Sie können das Verhalten von vor Version 3.0 wiederherstellen, indem Sie in `ModelBuilder` den Zugriffsmodus für die Eigenschaft konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="3a074-513">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="3a074-514">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a074-514">For example:</span></span>

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="3a074-515">Eine Ausnahme wird ausgelöst, wenn mehrere kompatible Unterstützungsfelder gefunden werden</span><span class="sxs-lookup"><span data-stu-id="3a074-515">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="3a074-516">Issue #12523</span><span class="sxs-lookup"><span data-stu-id="3a074-516">Tracking Issue #12523</span></span>](https://github.com/dotnet/efcore/issues/12523)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-517">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-517">Old behavior</span></span>

<span data-ttu-id="3a074-518">Vor Version 3.0 wurde in EF Core ein Unterstützungsfeld auf Grundlage einer Rangfolge ausgewählt, wenn die Regeln zum Suchen eines Eigenschaftsfelds auf mehrere Felder zutrafen.</span><span class="sxs-lookup"><span data-stu-id="3a074-518">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="3a074-519">Dabei konnte es vorkommen, dass in mehrdeutigen Fällen das falsche Feld verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="3a074-519">This could cause the wrong field to be used in ambiguous cases.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-520">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-520">New behavior</span></span>

<span data-ttu-id="3a074-521">Ab Version 3.0 wird in EF Core eine Ausnahme ausgelöst, wenn sich mehrere Felder auf dieselbe Eigenschaft beziehen.</span><span class="sxs-lookup"><span data-stu-id="3a074-521">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-522">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-522">Why</span></span>

<span data-ttu-id="3a074-523">Diese Änderung wurde vorgenommen, um zu vermeiden, dass automatisch ein falsches Feld verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-523">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-524">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-524">Mitigations</span></span>

<span data-ttu-id="3a074-525">Für Eigenschaften mit mehrdeutigen Unterstützungsfeldern muss das zu verwendende Feld explizit festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-525">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="3a074-526">Mit der Fluent-API ist dies beispielsweise wie folgt möglich:</span><span class="sxs-lookup"><span data-stu-id="3a074-526">For example, using the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="3a074-527">„Nur-Feld“-Eigenschaftsnamen sollten dem Feldnamen entsprechen</span><span class="sxs-lookup"><span data-stu-id="3a074-527">Field-only property names should match the field name</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="3a074-528">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-528">Old behavior</span></span>

<span data-ttu-id="3a074-529">Vor Version 3.0 konnte in EF Core eine Eigenschaft durch einen Zeichenfolgenwert angegeben werden, und wenn keine Eigenschaft mit diesem Namen im .NET-Typ gefunden wurde, versuchte EF Core, mithilfe von Konventionsregeln ein übereinstimmendes Feld zu finden.</span><span class="sxs-lookup"><span data-stu-id="3a074-529">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the .NET type then EF Core would try to match it to a field using convention rules.</span></span>

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

#### <a name="new-behavior"></a><span data-ttu-id="3a074-530">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-530">New behavior</span></span>

<span data-ttu-id="3a074-531">Ab Version 3.0 muss in EF Core eine „Nur-Feld“-Eigenschaft mit dem Namen des Felds genau übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="3a074-531">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

#### <a name="why"></a><span data-ttu-id="3a074-532">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-532">Why</span></span>

<span data-ttu-id="3a074-533">Diese Änderung wurde vorgenommen, um zu vermeiden, dass das gleiche Feld für zwei Eigenschaften mit ähnlichem Namen verwendet wird. Durch die Änderung entsprechen nun auch die Übereinstimmungsregeln für „Nur-Feld“-Eigenschaften den Regeln für Eigenschaften, die CLR-Eigenschaften zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="3a074-533">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-534">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-534">Mitigations</span></span>

<span data-ttu-id="3a074-535">„Nur-Feld“-Eigenschaften müssen so benannt werden wie das Feld, dem sie zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="3a074-535">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="3a074-536">In einer kommenden Version von EF Core nach 3.0 soll das explizite Konfigurieren eines Feldnamens, der sich vom Eigenschaftsnamen unterscheidet, erneut aktiviert werden (siehe Problem [#15307](https://github.com/dotnet/efcore/issues/15307)):</span><span class="sxs-lookup"><span data-stu-id="3a074-536">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/dotnet/efcore/issues/15307)):</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="3a074-537">Von AddDbContext/AddDbContextPool werden AddLogging und AddMemoryCache nicht mehr aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="3a074-537">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="3a074-538">Issue #14756</span><span class="sxs-lookup"><span data-stu-id="3a074-538">Tracking Issue #14756</span></span>](https://github.com/dotnet/efcore/issues/14756)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-539">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-539">Old behavior</span></span>

<span data-ttu-id="3a074-540">Vor EF Core 3.0 wurden beim Aufruf von `AddDbContext` oder `AddDbContextPool` durch Aufrufe von [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) und [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) auch Protokollierungs- und Arbeitsspeichercachingdienste bei DI registriert.</span><span class="sxs-lookup"><span data-stu-id="3a074-540">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with DI through calls to [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-541">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-541">New behavior</span></span>

<span data-ttu-id="3a074-542">Ab EF Core 3.0 werden diese Dienste durch `AddDbContext` und `AddDbContextPool` nicht mehr bei Dependency Injection (DI) registriert.</span><span class="sxs-lookup"><span data-stu-id="3a074-542">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-543">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-543">Why</span></span>

<span data-ttu-id="3a074-544">Für EF Core 3.0 ist es nicht erforderlich, dass diese Dienste im DI-Container der Anwendung enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="3a074-544">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="3a074-545">Wenn `ILoggerFactory` jedoch im DI-Container der Anwendung registriert ist, wird sie nach wie vor von EF Core verwendet.</span><span class="sxs-lookup"><span data-stu-id="3a074-545">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-546">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-546">Mitigations</span></span>

<span data-ttu-id="3a074-547">Wenn Ihre Anwendung diese Dienste benötigt, registrieren Sie sie explizit mit [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) oder [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) beim DI-Container.</span><span class="sxs-lookup"><span data-stu-id="3a074-547">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a><span data-ttu-id="3a074-548">AddEntityFramework\* fügt IMemoryCache mit einer Größenbeschränkung hinzu</span><span class="sxs-lookup"><span data-stu-id="3a074-548">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>

[<span data-ttu-id="3a074-549">Issue 12905</span><span class="sxs-lookup"><span data-stu-id="3a074-549">Tracking Issue #12905</span></span>](https://github.com/dotnet/efcore/issues/12905)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-550">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-550">Old behavior</span></span>

<span data-ttu-id="3a074-551">Vor EF Core 3.0 wurden bei Aufrufen von `AddEntityFramework*`-Methoden auch Arbeitsspeichercachingdienste ohne eine Größenbeschränkung bei DI registriert.</span><span class="sxs-lookup"><span data-stu-id="3a074-551">Before EF Core 3.0, calling `AddEntityFramework*` methods would also register memory caching services with DI without a size limit.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-552">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-552">New behavior</span></span>

<span data-ttu-id="3a074-553">Ab EF Core 3.0 registriert `AddEntityFramework*` einen IMemoryCache-Dienst mit einer Größenbeschränkung.</span><span class="sxs-lookup"><span data-stu-id="3a074-553">Starting with EF Core 3.0, `AddEntityFramework*` will register an IMemoryCache service with a size limit.</span></span> <span data-ttu-id="3a074-554">Wenn danach weitere Dienste hinzugefügt werden, die von IMemoryCache abhängig sind, können diese schnell den Standardgrenzwert erreichen, was zu Ausnahmen und Leistungsbeeinträchtigungen führt.</span><span class="sxs-lookup"><span data-stu-id="3a074-554">If any other services added afterwards depend on IMemoryCache they can quickly reach the default limit causing exceptions or degraded performance.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-555">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-555">Why</span></span>

<span data-ttu-id="3a074-556">Die Verwendung von IMemoryCache ohne Einschränkung kann zu unkontrollierter Arbeitsspeicherauslastung führen, wenn ein Fehler in die Abfragecachelogik auftritt oder wenn die Abfragen dynamisch generiert werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-556">Using IMemoryCache without a limit could result in uncontrolled memory usage if there is a bug in query caching logic or the queries are generated dynamically.</span></span> <span data-ttu-id="3a074-557">Durch eine Standardeinschränkung können potenzielle DoS-Angriffe verhindert werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-557">Having a default limit mitigates a potential DoS attack.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-558">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-558">Mitigations</span></span>

<span data-ttu-id="3a074-559">In den meisten Fällen ist das Aufrufen von `AddEntityFramework*` nicht erforderlich, wenn `AddDbContext` oder `AddDbContextPool` ebenfalls aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-559">In most cases calling `AddEntityFramework*` is not necessary if `AddDbContext` or `AddDbContextPool` is called as well.</span></span> <span data-ttu-id="3a074-560">Daher besteht die beste Entschärfung darin, den `AddEntityFramework*`-Aufruf zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="3a074-560">Therefore, the best mitigation is to remove the `AddEntityFramework*` call.</span></span>

<span data-ttu-id="3a074-561">Wenn Ihre Anwendung diese Dienste erfordert, registrieren Sie im Voraus eine IMemoryCache-Implementierung explizit mit dem DI-Container mithilfe von [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="3a074-561">If your application needs these services, then register a IMemoryCache implementation explicitly with the DI container beforehand using [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="3a074-562">DbContext.Entry erkennt nun mit DetectChanges lokale Änderungen</span><span class="sxs-lookup"><span data-stu-id="3a074-562">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="3a074-563">Issue #13552</span><span class="sxs-lookup"><span data-stu-id="3a074-563">Tracking Issue #13552</span></span>](https://github.com/dotnet/efcore/issues/13552)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-564">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-564">Old behavior</span></span>

<span data-ttu-id="3a074-565">Vor Version 3.0 führte in EF Core ein Aufruf von `DbContext.Entry` dazu, dass Änderungen an allen überwachten Entitäten erkannt wurden.</span><span class="sxs-lookup"><span data-stu-id="3a074-565">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="3a074-566">Dadurch wurde sichergestellt, dass der Zustand in `EntityEntry` immer aktuell war.</span><span class="sxs-lookup"><span data-stu-id="3a074-566">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-567">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-567">New behavior</span></span>

<span data-ttu-id="3a074-568">Ab Version 3.0 werden in EF Core Änderungen bei einem Aufruf von `DbContext.Entry` nur in der angegebenen Entität und in allen überwachten Prinzipalentitäten erkannt.</span><span class="sxs-lookup"><span data-stu-id="3a074-568">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="3a074-569">Änderungen an anderer Stelle werden daher durch den Aufruf dieser Methode möglicherweise nicht erkannt, was sich auf den Anwendungszustand auswirken kann.</span><span class="sxs-lookup"><span data-stu-id="3a074-569">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="3a074-570">Beachten Sie, dass selbst die Erkennung lokaler Änderungen deaktiviert wird, wenn `ChangeTracker.AutoDetectChangesEnabled` auf `false` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-570">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="3a074-571">Andere Methoden wie `ChangeTracker.Entries` und `SaveChanges`, die eine Änderungserkennung auslösen, führen nach wie vor zu einem vollständigen `DetectChanges`-Vorgang für alle überwachten Entitäten.</span><span class="sxs-lookup"><span data-stu-id="3a074-571">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-572">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-572">Why</span></span>

<span data-ttu-id="3a074-573">Diese Änderung wurde vorgenommen, um die Leistung bei der standardmäßigen Verwendung von `context.Entry` zu verbessern.</span><span class="sxs-lookup"><span data-stu-id="3a074-573">This change was made to improve the default performance of using `context.Entry`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-574">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-574">Mitigations</span></span>

<span data-ttu-id="3a074-575">Rufen Sie `ChangeTracker.DetectChanges()` explizit vor dem Aufruf von `Entry` auf, wenn das Verhalten vor Version 3.0 beibehalten werden soll.</span><span class="sxs-lookup"><span data-stu-id="3a074-575">Call `ChangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="3a074-576">Schlüssel aus Zeichenfolgen und Bytearrays werden standardmäßig nicht vom Client generiert</span><span class="sxs-lookup"><span data-stu-id="3a074-576">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="3a074-577">Issue #14617</span><span class="sxs-lookup"><span data-stu-id="3a074-577">Tracking Issue #14617</span></span>](https://github.com/dotnet/efcore/issues/14617)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-578">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-578">Old behavior</span></span>

<span data-ttu-id="3a074-579">Vor Version 3.0 konnten in EF Core `string`- und `byte[]`-Schlüsseleigenschaften verwendet werden, ohne dass explizit ein anderer Wert als NULL festgelegt werden musste.</span><span class="sxs-lookup"><span data-stu-id="3a074-579">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="3a074-580">In diesem Fall wurde der Schlüsselwert auf dem Client als GUID generiert und für `byte[]` in Bytes serialisiert.</span><span class="sxs-lookup"><span data-stu-id="3a074-580">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-581">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-581">New behavior</span></span>

<span data-ttu-id="3a074-582">Ab Version 3.0 wird in EF Core eine Ausnahme ausgelöst, wenn kein Schlüsselwert festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-582">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-583">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-583">Why</span></span>

<span data-ttu-id="3a074-584">Diese Änderung wurde vorgenommen, da vom Client generierte `string`/`byte[]`-Werte in der Regel nicht sinnvoll sind. Das Standardverhalten führte außerdem zu kaum nachvollziehbaren generierten Schlüsselwerten.</span><span class="sxs-lookup"><span data-stu-id="3a074-584">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-585">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-585">Mitigations</span></span>

<span data-ttu-id="3a074-586">Wenn Sie das Verhalten vor Version 3.0 nutzen möchten, müssen Sie explizit festlegen, dass für die Schlüsseleigenschaften generierte Werte verwendet sollen, wenn kein anderer Nicht-NULL-Wert festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-586">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="3a074-587">Dies ist beispielsweise mit der Fluent-API möglich:</span><span class="sxs-lookup"><span data-stu-id="3a074-587">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="3a074-588">Alternativ bieten sich auch Datenanmerkungen an:</span><span class="sxs-lookup"><span data-stu-id="3a074-588">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="3a074-589">ILoggerFactory ist nun ein bereichsbezogener Dienst</span><span class="sxs-lookup"><span data-stu-id="3a074-589">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="3a074-590">Issue #14698</span><span class="sxs-lookup"><span data-stu-id="3a074-590">Tracking Issue #14698</span></span>](https://github.com/dotnet/efcore/issues/14698)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-591">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-591">Old behavior</span></span>

<span data-ttu-id="3a074-592">Vor Version 3.0 wurde `ILoggerFactory` in EF Core als Singletondienst registriert.</span><span class="sxs-lookup"><span data-stu-id="3a074-592">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-593">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-593">New behavior</span></span>

<span data-ttu-id="3a074-594">Ab Version 3.0 wird in EF Core `ILoggerFactory` als bereichsbezogener Dienst registriert.</span><span class="sxs-lookup"><span data-stu-id="3a074-594">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-595">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-595">Why</span></span>

<span data-ttu-id="3a074-596">Diese Änderung wurde vorgenommen, um einer `DbContext`-Instanz eine Protokollierung zuordnen zu können. Dadurch werden weitere Funktionen bereitgestellt. In einigen Fällen wird außerdem schädliches Verhalten wie etwa die schnelle Zunahme von internen Dienstanbietern beseitigt.</span><span class="sxs-lookup"><span data-stu-id="3a074-596">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-597">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-597">Mitigations</span></span>

<span data-ttu-id="3a074-598">Diese Änderung wirkt sich nur dann auf den Anwendungscode aus, wenn benutzerdefinierte Dienste auf dem internen Dienstanbieter von EF Core registriert und verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-598">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="3a074-599">Dies ist nicht üblich.</span><span class="sxs-lookup"><span data-stu-id="3a074-599">This isn't common.</span></span>
<span data-ttu-id="3a074-600">In einem derartigen Fall funktioniert fast alles wie vorher. Singletondienste, die von `ILoggerFactory` abhängig sind, müssen jedoch so angepasst werden, dass `ILoggerFactory` auf andere Weise abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-600">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="3a074-601">Erstellen Sie in diesen Situationen bitte im [GitHub-Issuetracker für EF Core](https://github.com/dotnet/efcore/issues) ein Issue, in dem Sie beschreiben, wie Sie `ILoggerFactory` verwenden. So können wir in Zukunft erneute Breaking Changes vermeiden.</span><span class="sxs-lookup"><span data-stu-id="3a074-601">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/dotnet/efcore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="3a074-602">Für Proxys mit Lazy Loading (trägem Laden) gilt nicht mehr die Annahme, dass Navigationseigenschaften vollständig geladen sind</span><span class="sxs-lookup"><span data-stu-id="3a074-602">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="3a074-603">Issue #12780</span><span class="sxs-lookup"><span data-stu-id="3a074-603">Tracking Issue #12780</span></span>](https://github.com/dotnet/efcore/issues/12780)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-604">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-604">Old behavior</span></span>

<span data-ttu-id="3a074-605">Vor Version 3.0 gab es in EF Core nach der Freigabe eines `DbContext`-Objekts keine Möglichkeit, zu ermitteln, ob eine aus dem Kontext abgerufene Navigationseigenschaft für eine Entität vollständig geladen war.</span><span class="sxs-lookup"><span data-stu-id="3a074-605">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="3a074-606">Stattdessen galt für Proxys die Annahme, dass eine Verweisnavigation geladen ist, falls für diese ein anderer Wert als NULL vorliegt. Außerdem wurde davon ausgegangen, dass eine Sammlungsnavigation geladen ist, wenn sie nicht leer ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-606">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="3a074-607">In diesen Fällen entsprach das Lazy Loading einer Nulloperation („No-Op“).</span><span class="sxs-lookup"><span data-stu-id="3a074-607">In these cases, attempting to lazy-load would be a no-op.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-608">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-608">New behavior</span></span>

<span data-ttu-id="3a074-609">Ab Version 3.0 überwachen in EF Core Proxys, ob eine Navigationseigenschaft geladen ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-609">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="3a074-610">Wenn also auf eine Navigationseigenschaft zugegriffen werden soll, die nach der Freigabe des Kontexts geladen wird, ist dies immer eine No-Op-Operation. Das ist selbst dann der Fall, wenn die geladene Navigation leer oder NULL ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-610">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="3a074-611">Wenn umgekehrt auf eine nicht geladene Navigationseigenschaft zugegriffen werden soll und der Kontext bereits freigegeben wurde, wird eine Ausnahme ausgelöst. Dieser Fall tritt auch dann ein, wenn die Navigationseigenschaft eine nicht leere Sammlung ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-611">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="3a074-612">In dieser Situation wird also vom Anwendungscode versucht, zu einem ungültigen Zeitpunkt Lazy Loading durchzuführen. Die Anwendung sollte so angepasst werden, dass dieser Vorgang vermieden wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-612">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-613">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-613">Why</span></span>

<span data-ttu-id="3a074-614">Diese Änderung wurde vorgenommen, um das Verhalten beim Lazy Loading einer freigegebenen `DbContext`-Instanz konsistent und korrekt zu gestalten.</span><span class="sxs-lookup"><span data-stu-id="3a074-614">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-615">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-615">Mitigations</span></span>

<span data-ttu-id="3a074-616">Aktualisieren Sie den Anwendungscode so, dass kein Lazy Loading durchgeführt wird, nachdem der Kontext freigegeben wurde. Alternativ können Sie auch, wie in der Ausnahmemeldung beschrieben wird, eine No-Op-Operation festlegen.</span><span class="sxs-lookup"><span data-stu-id="3a074-616">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="3a074-617">Die Erstellung zu vieler interner Dienstanbieter führt standardmäßig zu einem Fehler</span><span class="sxs-lookup"><span data-stu-id="3a074-617">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="3a074-618">Issue #10236</span><span class="sxs-lookup"><span data-stu-id="3a074-618">Tracking Issue #10236</span></span>](https://github.com/dotnet/efcore/issues/10236)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-619">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-619">Old behavior</span></span>

<span data-ttu-id="3a074-620">Vor Version 3.0 wurde in EF Core eine Warnung für eine Anwendung protokolliert, die unverhältnismäßig viele interne Dienstanbieter erstellte.</span><span class="sxs-lookup"><span data-stu-id="3a074-620">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-621">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-621">New behavior</span></span>

<span data-ttu-id="3a074-622">Ab Version 3.0 wird diese Warnung in EF Core als Fehler betrachtet, und eine Ausnahme wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="3a074-622">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-623">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-623">Why</span></span>

<span data-ttu-id="3a074-624">Diese Änderung wurde vorgenommen, damit auf den oben beschriebenen Fall explizit hingewiesen wird. So soll die Entwicklung von besserem Anwendungscode erleichtert werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-624">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-625">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-625">Mitigations</span></span>

<span data-ttu-id="3a074-626">Wenn dieser Fehler auftritt, müssen Sie die Grundursache ermitteln und die Erstellung vieler interner Dienstanbieter unterbinden.</span><span class="sxs-lookup"><span data-stu-id="3a074-626">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="3a074-627">Sie können den Fehler allerdings auch wieder in eine Warnung konvertierten (oder ihn ignorieren), indem Sie `DbContextOptionsBuilder` konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="3a074-627">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="3a074-628">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a074-628">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="3a074-629">Neues Verhalten für HasOne/HasMany bei Aufruf mit einer einzelnen Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="3a074-629">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="3a074-630">Issue #9171</span><span class="sxs-lookup"><span data-stu-id="3a074-630">Tracking Issue #9171</span></span>](https://github.com/dotnet/efcore/issues/9171)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-631">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-631">Old behavior</span></span>

<span data-ttu-id="3a074-632">Vor EF Core 3.0 wurde Code, der `HasOne` oder `HasMany` mit einer einzelnen Zeichenfolge aufruft, auf irritierende Weise interpretiert.</span><span class="sxs-lookup"><span data-stu-id="3a074-632">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="3a074-633">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a074-633">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="3a074-634">Der Code scheint `Samurai` mit einem anderen Entitätstyp über die `Entrance`-Navigationseigenschaft zu verknüpfen, die möglicherweise privat ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-634">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="3a074-635">Tatsächlich versucht dieser Code, eine Beziehung zu einem Entitätstyp namens `Entrance` ohne Navigationseigenschaft herzustellen.</span><span class="sxs-lookup"><span data-stu-id="3a074-635">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-636">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-636">New behavior</span></span>

<span data-ttu-id="3a074-637">Ab EF Core 3.0 führt der obige Code jetzt die erwartete Aktion durch.</span><span class="sxs-lookup"><span data-stu-id="3a074-637">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-638">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-638">Why</span></span>

<span data-ttu-id="3a074-639">Das alte Verhalten war sehr verwirrend, vor allem beim Lesen des Konfigurationscodes und bei der Suche nach Fehlern.</span><span class="sxs-lookup"><span data-stu-id="3a074-639">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-640">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-640">Mitigations</span></span>

<span data-ttu-id="3a074-641">Dies führt nur zu Fehlern in Anwendungen, die Beziehungen explizit unter Verwendung von Zeichenfolgen für Typnamen und ohne explizite Angabe der Navigationseigenschaft konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="3a074-641">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="3a074-642">Dies ist nicht üblich.</span><span class="sxs-lookup"><span data-stu-id="3a074-642">This is not common.</span></span>
<span data-ttu-id="3a074-643">Das vorherige Verhalten kann durch explizite Übergabe von `null` für den Namen der Navigationseigenschaft erzielt werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-643">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="3a074-644">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a074-644">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="3a074-645">Der Rückgabetyp für mehrere asynchrone Methoden wurde von Task in ValueTask geändert</span><span class="sxs-lookup"><span data-stu-id="3a074-645">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="3a074-646">Issue #15184</span><span class="sxs-lookup"><span data-stu-id="3a074-646">Tracking Issue #15184</span></span>](https://github.com/dotnet/efcore/issues/15184)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-647">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-647">Old behavior</span></span>

<span data-ttu-id="3a074-648">Die folgenden asynchronen Methoden gaben zuvor `Task<T>` zurück:</span><span class="sxs-lookup"><span data-stu-id="3a074-648">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="3a074-649">`ValueGenerator.NextValueAsync()` (und abgeleitete Klassen)</span><span class="sxs-lookup"><span data-stu-id="3a074-649">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-650">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-650">New behavior</span></span>

<span data-ttu-id="3a074-651">Die oben genannten Methoden geben nun `ValueTask<T>` über dieselbe `T` wie zuvor zurück.</span><span class="sxs-lookup"><span data-stu-id="3a074-651">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-652">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-652">Why</span></span>

<span data-ttu-id="3a074-653">Durch diese Änderung verringert sich die Anzahl der Heapzuordnungen, die beim Aufrufen dieser Methoden entstehen, und dies führt zu einer Verbesserung der allgemeinen Leistung.</span><span class="sxs-lookup"><span data-stu-id="3a074-653">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-654">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-654">Mitigations</span></span>

<span data-ttu-id="3a074-655">Anwendungen, die einfach die oben genannten APIs erwarten, müssen nur neu kompiliert werden – Quelländerungen sind nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="3a074-655">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="3a074-656">Eine komplexere Verwendung (z.B. Übergeben der zurückgegebenen `Task` an `Task.WhenAny()`) erfordern in der Regel, dass die zurückgegebene `ValueTask<T>` durch einen Aufruf von `AsTask()` in `Task<T>` konvertiert wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-656">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="3a074-657">Beachten Sie, dass dadurch die mit dieser Änderung verbundene Verringerung der Zuordnungen aufgehoben wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-657">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="3a074-658">Die Anmerkung Relational:TypeMapping heißt nun nur noch TypeMapping</span><span class="sxs-lookup"><span data-stu-id="3a074-658">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="3a074-659">Issue #9913</span><span class="sxs-lookup"><span data-stu-id="3a074-659">Tracking Issue #9913</span></span>](https://github.com/dotnet/efcore/issues/9913)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-660">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-660">Old behavior</span></span>

<span data-ttu-id="3a074-661">Der Anmerkungsname für Typzuordnungsanmerkungen war bisher Relational:TypeMapping.</span><span class="sxs-lookup"><span data-stu-id="3a074-661">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-662">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-662">New behavior</span></span>

<span data-ttu-id="3a074-663">Der Anmerkungsname für Typzuordnungsanmerkungen lautet nun TypeMapping.</span><span class="sxs-lookup"><span data-stu-id="3a074-663">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-664">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-664">Why</span></span>

<span data-ttu-id="3a074-665">Typzuordnungen werden nicht mehr ausschließlich für Anbieter relationaler Datenbanken verwendet.</span><span class="sxs-lookup"><span data-stu-id="3a074-665">Type mappings are now used for more than just relational database providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-666">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-666">Mitigations</span></span>

<span data-ttu-id="3a074-667">Diese Änderung ist nur dann ein Breaking Change, wenn Anwendungen direkt über eine Anmerkung auf die Typzuordnung zugreifen. Dieses Szenario ist jedoch unüblich.</span><span class="sxs-lookup"><span data-stu-id="3a074-667">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="3a074-668">Verzichten Sie daher darauf, die Anmerkung direkt zu verwenden, und greifen Sie stattdessen über die Fluent-API auf Typzuordnungen zu, um das Problem zu beheben.</span><span class="sxs-lookup"><span data-stu-id="3a074-668">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="3a074-669">ToTable löst für einen abgeleiteten Typ eine Ausnahme aus</span><span class="sxs-lookup"><span data-stu-id="3a074-669">ToTable on a derived type throws an exception</span></span>

[<span data-ttu-id="3a074-670">Issue #11811</span><span class="sxs-lookup"><span data-stu-id="3a074-670">Tracking Issue #11811</span></span>](https://github.com/dotnet/efcore/issues/11811)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-671">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-671">Old behavior</span></span>

<span data-ttu-id="3a074-672">Vor Version 3.0 wurde in EF Core der Aufruf von `ToTable()` für einen abgeleiteten Typ ignoriert, da TPH die einzige Strategie zur Vererbungszuordnung war und diese in unzulässigen Fällen angewendet wurde.</span><span class="sxs-lookup"><span data-stu-id="3a074-672">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-673">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-673">New behavior</span></span>

<span data-ttu-id="3a074-674">Ab Version 3.0 wird in EF Core eine Ausnahme ausgelöst, um später eine unerwartete Zuordnung zu vermeiden, wenn `ToTable()` für einen abgeleiteten Typ aufgerufen wird. Dieser Schritt dient auch als Vorbereitung für die TPT- und TPC-Unterstützung, die in einem künftigen Release folgen wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-674">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-675">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-675">Why</span></span>

<span data-ttu-id="3a074-676">Derzeit ist es nicht zulässig, einen abgeleiteten Typ einer anderen Tabelle zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="3a074-676">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="3a074-677">Durch diese Änderung werden Breaking Changes vermieden, wenn der beschriebene Vorgang in Zukunft zulässig wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-677">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-678">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-678">Mitigations</span></span>

<span data-ttu-id="3a074-679">Entfernen Sie alle Zuordnungen von abgeleiteten Typen zu anderen Tabellen.</span><span class="sxs-lookup"><span data-stu-id="3a074-679">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="3a074-680">ForSqlServerHasIndex wurde durch HasIndex ersetzt</span><span class="sxs-lookup"><span data-stu-id="3a074-680">ForSqlServerHasIndex replaced with HasIndex</span></span>

[<span data-ttu-id="3a074-681">Issue #12366</span><span class="sxs-lookup"><span data-stu-id="3a074-681">Tracking Issue #12366</span></span>](https://github.com/dotnet/efcore/issues/12366)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-682">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-682">Old behavior</span></span>

<span data-ttu-id="3a074-683">Vor Version 3.0 konnten in EF Core mit `ForSqlServerHasIndex().ForSqlServerInclude()` Spalten konfiguriert werden, die mit `INCLUDE` verwendet wurden.</span><span class="sxs-lookup"><span data-stu-id="3a074-683">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-684">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-684">New behavior</span></span>

<span data-ttu-id="3a074-685">Ab Version 3.0 wird in EF Core die Nutzung von `Include` für einen Index auf der relationalen Ebene unterstützt.</span><span class="sxs-lookup"><span data-stu-id="3a074-685">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="3a074-686">Verwenden Sie `HasIndex().ForSqlServerInclude()`.</span><span class="sxs-lookup"><span data-stu-id="3a074-686">Use `HasIndex().ForSqlServerInclude()`.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-687">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-687">Why</span></span>

<span data-ttu-id="3a074-688">Diese Änderung wurde vorgenommen, um die API für Indizes mit `Include` an einer zentralen Stelle für alle Datenbankanbieter zusammenzuführen.</span><span class="sxs-lookup"><span data-stu-id="3a074-688">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-689">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-689">Mitigations</span></span>

<span data-ttu-id="3a074-690">Verwenden Sie wie oben beschrieben die neue API.</span><span class="sxs-lookup"><span data-stu-id="3a074-690">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="3a074-691">Metadaten-API-Änderungen</span><span class="sxs-lookup"><span data-stu-id="3a074-691">Metadata API changes</span></span>

[<span data-ttu-id="3a074-692">Issue #214</span><span class="sxs-lookup"><span data-stu-id="3a074-692">Tracking Issue #214</span></span>](https://github.com/dotnet/efcore/issues/214)

#### <a name="new-behavior"></a><span data-ttu-id="3a074-693">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-693">New behavior</span></span>

<span data-ttu-id="3a074-694">Die folgenden Eigenschaften wurden in Erweiterungsmethoden konvertiert:</span><span class="sxs-lookup"><span data-stu-id="3a074-694">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

#### <a name="why"></a><span data-ttu-id="3a074-695">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-695">Why</span></span>

<span data-ttu-id="3a074-696">Mit dieser Änderung wird die Implementierung der oben genannten Schnittstellen vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="3a074-696">This change simplifies the implementation of the aforementioned interfaces.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-697">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-697">Mitigations</span></span>

<span data-ttu-id="3a074-698">Verwenden Sie die neuen Erweiterungsmethoden.</span><span class="sxs-lookup"><span data-stu-id="3a074-698">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="3a074-699">Anbieterspezifische Metadaten-API-Änderungen</span><span class="sxs-lookup"><span data-stu-id="3a074-699">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="3a074-700">Issue #214</span><span class="sxs-lookup"><span data-stu-id="3a074-700">Tracking Issue #214</span></span>](https://github.com/dotnet/efcore/issues/214)

#### <a name="new-behavior"></a><span data-ttu-id="3a074-701">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-701">New behavior</span></span>

<span data-ttu-id="3a074-702">Die anbieterspezifischen Erweiterungsmethoden werden vereinfacht:</span><span class="sxs-lookup"><span data-stu-id="3a074-702">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

#### <a name="why"></a><span data-ttu-id="3a074-703">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-703">Why</span></span>

<span data-ttu-id="3a074-704">Mit dieser Änderung wird die Implementierung der oben genannten Erweiterungsmethoden vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="3a074-704">This change simplifies the implementation of the aforementioned extension methods.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-705">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-705">Mitigations</span></span>

<span data-ttu-id="3a074-706">Verwenden Sie die neuen Erweiterungsmethoden.</span><span class="sxs-lookup"><span data-stu-id="3a074-706">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="3a074-707">EF Core sendet keine PRAGMA-Anweisungen mehr, um Fremdschlüssel in SQLite zu erzwingen</span><span class="sxs-lookup"><span data-stu-id="3a074-707">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="3a074-708">Issue #12151</span><span class="sxs-lookup"><span data-stu-id="3a074-708">Tracking Issue #12151</span></span>](https://github.com/dotnet/efcore/issues/12151)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-709">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-709">Old behavior</span></span>

<span data-ttu-id="3a074-710">Vor Version 3.0 wurden in EF Core `PRAGMA foreign_keys = 1`-Anweisungen gesendet, wenn eine Verbindung mit SQLite hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="3a074-710">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-711">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-711">New behavior</span></span>

<span data-ttu-id="3a074-712">Ab Version 3.0 werden in EF Core keine `PRAGMA foreign_keys = 1`-Anweisungen mehr gesendet, wenn eine Verbindung mit SQLite hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-712">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-713">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-713">Why</span></span>

<span data-ttu-id="3a074-714">Diese Änderung wurde vorgenommen, da von EF Core standardmäßig `SQLitePCLRaw.bundle_e_sqlite3` verwendet wird. Dadurch ist die Erzwingung von Fremdschlüsseln standardmäßig aktiviert und muss nicht jedes Mal explizit aktiviert werden, wenn eine Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-714">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-715">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-715">Mitigations</span></span>

<span data-ttu-id="3a074-716">Fremdschlüssel sind in SQLitePCLRaw.bundle_e_sqlite3 standardmäßig aktiviert. Dieses Bundle wird standardmäßig von EF Core verwendet.</span><span class="sxs-lookup"><span data-stu-id="3a074-716">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="3a074-717">In anderen Fällen können Fremdschlüssel durch die Angabe `Foreign Keys=True` in der Verbindungszeichenfolge aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-717">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="3a074-718">SQLitePCLRaw.bundle_e_sqlite3 ist nun eine Abhängigkeit von Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="3a074-718">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="3a074-719">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-719">Old behavior</span></span>

<span data-ttu-id="3a074-720">Vor Version 3.0 wurde von EF Core `SQLitePCLRaw.bundle_green` verwendet.</span><span class="sxs-lookup"><span data-stu-id="3a074-720">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-721">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-721">New behavior</span></span>

<span data-ttu-id="3a074-722">Ab Version 3.0 wird von EF Core `SQLitePCLRaw.bundle_e_sqlite3` verwendet.</span><span class="sxs-lookup"><span data-stu-id="3a074-722">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-723">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-723">Why</span></span>

<span data-ttu-id="3a074-724">Diese Änderung wurde vorgenommen, damit die unter iOS verwendete Version von SQLite sich konsistent zu Versionen auf anderen Plattformen verhält.</span><span class="sxs-lookup"><span data-stu-id="3a074-724">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-725">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-725">Mitigations</span></span>

<span data-ttu-id="3a074-726">Konfigurieren Sie `Microsoft.Data.Sqlite` so, dass ein anderes `SQLitePCLRaw`-Bundle verwendet wird, um die native SQLite-Version unter iOS zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="3a074-726">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="3a074-727">GUID-Werte werden jetzt als TEXT in SQLite gespeichert</span><span class="sxs-lookup"><span data-stu-id="3a074-727">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="3a074-728">Issue #15078</span><span class="sxs-lookup"><span data-stu-id="3a074-728">Tracking Issue #15078</span></span>](https://github.com/dotnet/efcore/issues/15078)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-729">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-729">Old behavior</span></span>

<span data-ttu-id="3a074-730">GUID-Werte wurden in der Vergangenheit in SQLite als BLOB-Werte gespeichert.</span><span class="sxs-lookup"><span data-stu-id="3a074-730">Guid values were previously stored as BLOB values on SQLite.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-731">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-731">New behavior</span></span>

<span data-ttu-id="3a074-732">GUID-Werte werden jetzt als TEXT gespeichert.</span><span class="sxs-lookup"><span data-stu-id="3a074-732">Guid values are now stored as TEXT.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-733">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-733">Why</span></span>

<span data-ttu-id="3a074-734">Das Binärformat der GUIDs ist nicht standardisiert.</span><span class="sxs-lookup"><span data-stu-id="3a074-734">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="3a074-735">Das Speichern der Werte als TEXT führt dazu, dass die Datenbank mit anderen Technologien eher kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-735">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-736">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-736">Mitigations</span></span>

<span data-ttu-id="3a074-737">Sie können vorhandene Datenbanken zum neuen Format migrieren, indem Sie SQL-Code wie den folgenden ausführen:</span><span class="sxs-lookup"><span data-stu-id="3a074-737">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

```sql
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

<span data-ttu-id="3a074-738">Sie können in EF Core auch weiterhin das alte Verhalten verwenden, indem Sie einen Wertkonverter für diese Eigenschaften konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="3a074-738">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="3a074-739">Microsoft.Data.Sqlite kann weiterhin GUID-Werte aus BLOB- und TEXT-Spalten lesen. Da sich jedoch das Standardformat für Parameter und Konstanten geändert hat, müssen Sie wahrscheinlich Aktionen für die meisten GUID-Szenarios vornehmen.</span><span class="sxs-lookup"><span data-stu-id="3a074-739">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="3a074-740">Char-Werte werden jetzt als TEXT in SQLite gespeichert</span><span class="sxs-lookup"><span data-stu-id="3a074-740">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="3a074-741">Issue #15020</span><span class="sxs-lookup"><span data-stu-id="3a074-741">Tracking Issue #15020</span></span>](https://github.com/dotnet/efcore/issues/15020)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-742">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-742">Old behavior</span></span>

<span data-ttu-id="3a074-743">Char-Werte wurden in der Vergangenheit in SQLite als INTEGER-Werte gespeichert.</span><span class="sxs-lookup"><span data-stu-id="3a074-743">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="3a074-744">Der Char-Wert *A* wurde z.B. als INTEGER-Wert 65 gespeichert.</span><span class="sxs-lookup"><span data-stu-id="3a074-744">For example, a char value of *A* was stored as the integer value 65.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-745">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-745">New behavior</span></span>

<span data-ttu-id="3a074-746">Char-Werte werden jetzt als TEXT gespeichert.</span><span class="sxs-lookup"><span data-stu-id="3a074-746">Char values are now stored as TEXT.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-747">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-747">Why</span></span>

<span data-ttu-id="3a074-748">Das Speichern der Werte als TEXT ist naheliegender und führt dazu, dass die Datenbank mit anderen Technologien eher kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-748">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-749">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-749">Mitigations</span></span>

<span data-ttu-id="3a074-750">Sie können vorhandene Datenbanken zum neuen Format migrieren, indem Sie SQL-Code wie den folgenden ausführen:</span><span class="sxs-lookup"><span data-stu-id="3a074-750">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

```sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="3a074-751">Sie können in EF Core auch weiterhin das alte Verhalten verwenden, indem Sie einen Wertkonverter für diese Eigenschaften konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="3a074-751">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="3a074-752">Microsoft.Data.Sqlite kann auch weiterhin Zeichenwerte aus INTEGER- und TEXT-Spalten lesen, weshalb es sein kann, dass in einigen Szenarios keine weiteren Maßnahmen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="3a074-752">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="3a074-753">Migrations-IDs werden jetzt mit dem Kalender der invarianten Kultur generiert</span><span class="sxs-lookup"><span data-stu-id="3a074-753">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="3a074-754">Issue #12978</span><span class="sxs-lookup"><span data-stu-id="3a074-754">Tracking Issue #12978</span></span>](https://github.com/dotnet/efcore/issues/12978)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-755">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-755">Old behavior</span></span>

<span data-ttu-id="3a074-756">Migrations-IDs wurden versehentlich mit dem Kalender der aktuellen Kultur generiert.</span><span class="sxs-lookup"><span data-stu-id="3a074-756">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-757">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-757">New behavior</span></span>

<span data-ttu-id="3a074-758">Migrations-IDs werden jetzt immer mit dem Kalender der invarianten Kultur generiert (gregorianischer Kalender).</span><span class="sxs-lookup"><span data-stu-id="3a074-758">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-759">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-759">Why</span></span>

<span data-ttu-id="3a074-760">Die Reihenfolge der Migrationen ist beim Aktualisieren einer Datenbank oder Auflösen von Mergekonflikten wesentlich.</span><span class="sxs-lookup"><span data-stu-id="3a074-760">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="3a074-761">Wenn der invariante Kalender verwendet wird, werden Probleme bei der Reihenfolge vermieden, die entstehen, wenn Teammitglieder unterschiedliche Systemkalender verwenden.</span><span class="sxs-lookup"><span data-stu-id="3a074-761">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-762">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-762">Mitigations</span></span>

<span data-ttu-id="3a074-763">Diese Änderung betrifft jeden Benutzer, der einen Kalender verwendet, der nicht gregorianisch ist und bei dem die Jahreszahl höher als die des gregorianischen Kalenders ist (wie z.B. in der buddhistischen Zeitrechnung).</span><span class="sxs-lookup"><span data-stu-id="3a074-763">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="3a074-764">Vorhandene Migrations-IDs müssen aktualisiert werden, damit neue Migrationen in der Reihenfolge hinter vorhandenen Migrationen eingeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-764">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="3a074-765">Sie können die Migrations-ID im Migration-Attribut in der Designerdatei der Migration finden.</span><span class="sxs-lookup"><span data-stu-id="3a074-765">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

```diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="3a074-766">Die Tabelle mit dem Migrationsverlauf muss auch aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-766">The Migrations history table also needs to be updated.</span></span>

```sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="3a074-767">UseRowNumberForPaging wurde entfernt.</span><span class="sxs-lookup"><span data-stu-id="3a074-767">UseRowNumberForPaging has been removed</span></span>

[<span data-ttu-id="3a074-768">Issue #16400</span><span class="sxs-lookup"><span data-stu-id="3a074-768">Tracking Issue #16400</span></span>](https://github.com/dotnet/efcore/issues/16400)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-769">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-769">Old behavior</span></span>

<span data-ttu-id="3a074-770">Vor EF Core 3.0 konnte `UseRowNumberForPaging` zum Generieren von SQL-Code für die Paginierung verwendet werden, der mit SQL Server 2008 kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-770">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-771">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-771">New behavior</span></span>

<span data-ttu-id="3a074-772">Ab EF Core 3.0 generiert EF nur noch SQL-Code für die Paginierung, die mit höheren SQL Server-Versionen kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-772">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-773">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-773">Why</span></span>

<span data-ttu-id="3a074-774">Wir führen diese Änderung ein, weil [SQL Server 2008 kein unterstütztes Produkt mehr ist](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) und weil die Aktualisierung dieses Features auf die in EF Core 3.0 vorgenommenen Änderungen an Abfragen ein wichtiger Schritt ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-774">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-775">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-775">Mitigations</span></span>

<span data-ttu-id="3a074-776">Es empfiehlt sich, eine Update auf eine neuere Version von SQL Server durchzuführen oder einen höheren Kompatibilitätsgrad zu verwenden, damit der generierte SQL-Code unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="3a074-776">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="3a074-777">Wenn dies bei Ihnen nicht möglich ist, fügen Sie einen [Kommentar zum Issue](https://github.com/dotnet/efcore/issues/16400) ein, und geben Sie alle relevanten Informationen an.</span><span class="sxs-lookup"><span data-stu-id="3a074-777">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/dotnet/efcore/issues/16400) with details.</span></span> <span data-ttu-id="3a074-778">Je nach Feedback der Benutzer wird diese Entscheidung möglicherweise noch einmal überprüft.</span><span class="sxs-lookup"><span data-stu-id="3a074-778">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="3a074-779">Erweiterungsinformationen und Metadaten aus IDbContextOptionsExtension entfernt</span><span class="sxs-lookup"><span data-stu-id="3a074-779">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

[<span data-ttu-id="3a074-780">Issue #16119</span><span class="sxs-lookup"><span data-stu-id="3a074-780">Tracking Issue #16119</span></span>](https://github.com/dotnet/efcore/issues/16119)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-781">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-781">Old behavior</span></span>

<span data-ttu-id="3a074-782">`IDbContextOptionsExtension` enthielt Methoden zum Bereitstellen von Metadaten zur Erweiterung.</span><span class="sxs-lookup"><span data-stu-id="3a074-782">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-783">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-783">New behavior</span></span>

<span data-ttu-id="3a074-784">Diese Methoden wurden in eine neue abstrakte `DbContextOptionsExtensionInfo`-Basisklasse verschoben, die von einer neuen `IDbContextOptionsExtension.Info`-Eigenschaft zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-784">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-785">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-785">Why</span></span>

<span data-ttu-id="3a074-786">In den Releases zwischen 2.0 und 3.0 mussten wir diese Methoden mehrmals ergänzen oder ändern.</span><span class="sxs-lookup"><span data-stu-id="3a074-786">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="3a074-787">Durch die Bereitstellung dieser Methoden in einer neuen abstrakten Basisklasse lassen sich solche Änderungen einfacher einführen, ohne dass es zu Konflikten mit vorhandenen Erweiterungen kommt.</span><span class="sxs-lookup"><span data-stu-id="3a074-787">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-788">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-788">Mitigations</span></span>

<span data-ttu-id="3a074-789">Erweiterungen wurden aktualisiert und verwenden das neue Muster.</span><span class="sxs-lookup"><span data-stu-id="3a074-789">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="3a074-790">Beispiele finden sich in den vielen Implementierungen von `IDbContextOptionsExtension` für verschiedene Arten von Erweiterungen im EF Core-Quellcode.</span><span class="sxs-lookup"><span data-stu-id="3a074-790">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="3a074-791">LogQueryPossibleExceptionWithAggregateOperator wurde umbenannt</span><span class="sxs-lookup"><span data-stu-id="3a074-791">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="3a074-792">Issue #10985</span><span class="sxs-lookup"><span data-stu-id="3a074-792">Tracking Issue #10985</span></span>](https://github.com/dotnet/efcore/issues/10985)

#### <a name="change"></a><span data-ttu-id="3a074-793">Change</span><span class="sxs-lookup"><span data-stu-id="3a074-793">Change</span></span>

<span data-ttu-id="3a074-794">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` wurde umbenannt in `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span><span class="sxs-lookup"><span data-stu-id="3a074-794">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-795">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-795">Why</span></span>

<span data-ttu-id="3a074-796">Die Benennung dieses Warnereignisses wurde an allen anderen Warnereignissen ausgerichtet.</span><span class="sxs-lookup"><span data-stu-id="3a074-796">Aligns the naming of this warning event with all other warning events.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-797">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-797">Mitigations</span></span>

<span data-ttu-id="3a074-798">Verwenden Sie den neuen Namen.</span><span class="sxs-lookup"><span data-stu-id="3a074-798">Use the new name.</span></span> <span data-ttu-id="3a074-799">(Beachten Sie, dass sich die Ereignis-ID-Nummer nicht geändert hat.)</span><span class="sxs-lookup"><span data-stu-id="3a074-799">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="3a074-800">Verdeutlichen der API für Einschränkungsnamen von Fremdschlüsseln</span><span class="sxs-lookup"><span data-stu-id="3a074-800">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="3a074-801">Issue #10730</span><span class="sxs-lookup"><span data-stu-id="3a074-801">Tracking Issue #10730</span></span>](https://github.com/dotnet/efcore/issues/10730)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-802">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-802">Old behavior</span></span>

<span data-ttu-id="3a074-803">Vor EF Core 3.0 wurden Einschränkungsnamen von Fremdschlüsseln einfach als „Namen“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="3a074-803">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="3a074-804">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a074-804">For example:</span></span>

```csharp
var constraintName = myForeignKey.Name;
```

#### <a name="new-behavior"></a><span data-ttu-id="3a074-805">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-805">New behavior</span></span>

<span data-ttu-id="3a074-806">Ab EF Core 3.0 werden Einschränkungsnamen von Fremdschlüsseln nun als „Einschränkungsnamen“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="3a074-806">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="3a074-807">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a074-807">For example:</span></span>

```csharp
var constraintName = myForeignKey.ConstraintName;
```

#### <a name="why"></a><span data-ttu-id="3a074-808">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-808">Why</span></span>

<span data-ttu-id="3a074-809">Durch diese Änderung wird Konsistenz für Benennungen in diesem Bereich gewährleistet, und es wird verdeutlicht, dass es sich dabei um den Namen der Fremdschlüsseleinschränkung handelt und nicht um den Spalten- oder Eigenschaftennamen, auf deren Grundlage der Fremdschlüssel definiert ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-809">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-810">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-810">Mitigations</span></span>

<span data-ttu-id="3a074-811">Verwenden Sie den neuen Namen.</span><span class="sxs-lookup"><span data-stu-id="3a074-811">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="3a074-812">„IRelationalDatabaseCreator.HasTables/HasTablesAsync“ ist jetzt öffentlich</span><span class="sxs-lookup"><span data-stu-id="3a074-812">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

[<span data-ttu-id="3a074-813">Issue #15997</span><span class="sxs-lookup"><span data-stu-id="3a074-813">Tracking Issue #15997</span></span>](https://github.com/dotnet/efcore/issues/15997)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-814">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-814">Old behavior</span></span>

<span data-ttu-id="3a074-815">Vor EF Core 3.0 waren diese Methoden geschützt.</span><span class="sxs-lookup"><span data-stu-id="3a074-815">Before EF Core 3.0, these methods were protected.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-816">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-816">New behavior</span></span>

<span data-ttu-id="3a074-817">Ab EF Core 3.0 sind diese Methoden öffentlich.</span><span class="sxs-lookup"><span data-stu-id="3a074-817">Starting with EF Core 3.0, these methods are public.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-818">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-818">Why</span></span>

<span data-ttu-id="3a074-819">Diese Methoden werden von EF verwendet, um zu ermitteln, ob eine Datenbank erstellt wurde, aber leer ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-819">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="3a074-820">Dies kann auch außerhalb von EF nützlich sein, um zu ermitteln, ob Migrationen angewendet werden sollen oder nicht.</span><span class="sxs-lookup"><span data-stu-id="3a074-820">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-821">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-821">Mitigations</span></span>

<span data-ttu-id="3a074-822">Der Zugriff auf Überschreibungen wurde geändert.</span><span class="sxs-lookup"><span data-stu-id="3a074-822">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="3a074-823">Microsoft.EntityFrameworkCore.Design ist jetzt ein DevelopmentDependency-Paket</span><span class="sxs-lookup"><span data-stu-id="3a074-823">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

[<span data-ttu-id="3a074-824">Issue #11506</span><span class="sxs-lookup"><span data-stu-id="3a074-824">Tracking Issue #11506</span></span>](https://github.com/dotnet/efcore/issues/11506)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-825">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-825">Old behavior</span></span>

<span data-ttu-id="3a074-826">Vor EF Core 3.0 war Microsoft.EntityFrameworkCore.Design ein reguläres NuGet-Paket, auf dessen Assembly von Projekten verwiesen werden kann, die davon abhängig sind.</span><span class="sxs-lookup"><span data-stu-id="3a074-826">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-827">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-827">New behavior</span></span>

<span data-ttu-id="3a074-828">Ab EF Core 3.0 ist es ein DevelopmentDependency-Paket.</span><span class="sxs-lookup"><span data-stu-id="3a074-828">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="3a074-829">Das bedeutet, dass die Abhängigkeit nicht transitiv auf andere Projekte übertragen wird und Sie nicht mehr standardmäßig auf die Assembly verweisen können.</span><span class="sxs-lookup"><span data-stu-id="3a074-829">This means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-830">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-830">Why</span></span>

<span data-ttu-id="3a074-831">Dieses Paket ist nur für die Verwendung zur Entwurfszeit konzipiert.</span><span class="sxs-lookup"><span data-stu-id="3a074-831">This package is only intended to be used at design time.</span></span> <span data-ttu-id="3a074-832">Bereitgestellte Anwendungen sollten nicht darauf verweisen.</span><span class="sxs-lookup"><span data-stu-id="3a074-832">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="3a074-833">Die Kennzeichnung des Pakets als DevelopmentDependency unterstreicht diese Empfehlung.</span><span class="sxs-lookup"><span data-stu-id="3a074-833">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-834">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-834">Mitigations</span></span>

<span data-ttu-id="3a074-835">Wenn Sie auf dieses Paket verweisen müssen, um das Verhalten von EF Core zur Entwurfszeit zu überschreiben, können Sie die Metadaten des PackageReference-Elements in Ihrem Projekt aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="3a074-835">If you need to reference this package to override EF Core's design-time behavior, then you can update PackageReference item metadata in your project.</span></span>

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="3a074-836">Wenn transitiv über via Microsoft.EntityFrameworkCore.Tools auf das Paket verwiesen wird, müssen Sie dem Paket eine explizite PackageReference hinzufügen, um seine Metadaten zu ändern.</span><span class="sxs-lookup"><span data-stu-id="3a074-836">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span> <span data-ttu-id="3a074-837">Ein solch expliziter Verweis muss jedem Projekt hinzugefügt werden, für das die Typen aus den Paketen benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-837">Such an explicit reference must be added to any project where the types from the package are needed.</span></span>

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="3a074-838">SQLitePCL.raw zu Version 2.0.0 aktualisiert</span><span class="sxs-lookup"><span data-stu-id="3a074-838">SQLitePCL.raw updated to version 2.0.0</span></span>

[<span data-ttu-id="3a074-839">Issue #14824</span><span class="sxs-lookup"><span data-stu-id="3a074-839">Tracking Issue #14824</span></span>](https://github.com/dotnet/efcore/issues/14824)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-840">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-840">Old behavior</span></span>

<span data-ttu-id="3a074-841">Microsoft.EntityFrameworkCore.Sqlite war früher von Version 1.1.12 von SQLitePCL.raw abhängig.</span><span class="sxs-lookup"><span data-stu-id="3a074-841">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-842">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-842">New behavior</span></span>

<span data-ttu-id="3a074-843">Wir haben unser Paket aktualisiert, so dass es jetzt von Version 2.0.0 abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-843">We've updated our package to depend on version 2.0.0.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-844">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-844">Why</span></span>

<span data-ttu-id="3a074-845">Version 2.0.0 von SQLitePCL.raw zielt auf .NET Standard 2.0 ab.</span><span class="sxs-lookup"><span data-stu-id="3a074-845">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="3a074-846">Zuvor war .NET Standard 1.1 das Ziel, hierfür war für ein umfassender Funktionsabschluss von transitiven Paketen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="3a074-846">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-847">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-847">Mitigations</span></span>

<span data-ttu-id="3a074-848">SQLitePCL.raw, Version 2.0.0, enthält einige Breaking Changes.</span><span class="sxs-lookup"><span data-stu-id="3a074-848">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="3a074-849">Weitere Informationen finden Sie in den [Versionshinweisen](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md).</span><span class="sxs-lookup"><span data-stu-id="3a074-849">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="3a074-850">NetTopologySuite auf Version 2.0.0 aktualisiert</span><span class="sxs-lookup"><span data-stu-id="3a074-850">NetTopologySuite updated to version 2.0.0</span></span>

[<span data-ttu-id="3a074-851">Issue #14825</span><span class="sxs-lookup"><span data-stu-id="3a074-851">Tracking Issue #14825</span></span>](https://github.com/dotnet/efcore/issues/14825)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-852">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-852">Old behavior</span></span>

<span data-ttu-id="3a074-853">Die Pakete mit räumlichen Daten waren bisher von Version 1.15.1 der NetTopologySuite abhängig.</span><span class="sxs-lookup"><span data-stu-id="3a074-853">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-854">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-854">New behavior</span></span>

<span data-ttu-id="3a074-855">Wir haben unser Paket aktualisiert, so dass es jetzt von Version 2.0.0 abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-855">We've update our package to depend on version 2.0.0.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-856">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-856">Why</span></span>

<span data-ttu-id="3a074-857">Version 2.0.0 der NetTopologySuite behebt verschiedene Verwendungsprobleme, die von EF Core-Benutzern gemeldet wurden.</span><span class="sxs-lookup"><span data-stu-id="3a074-857">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-858">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-858">Mitigations</span></span>

<span data-ttu-id="3a074-859">NetTopologySuite 2.0.0 umfasst einige Breaking Changes.</span><span class="sxs-lookup"><span data-stu-id="3a074-859">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="3a074-860">Weitere Informationen finden Sie in den [Versionshinweisen](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001).</span><span class="sxs-lookup"><span data-stu-id="3a074-860">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a><span data-ttu-id="3a074-861">Microsoft.Data.SqlClient wird statt System.Data.SqlClient verwendet</span><span class="sxs-lookup"><span data-stu-id="3a074-861">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>

[<span data-ttu-id="3a074-862">Issue #15636</span><span class="sxs-lookup"><span data-stu-id="3a074-862">Tracking Issue #15636</span></span>](https://github.com/dotnet/efcore/issues/15636)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-863">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-863">Old behavior</span></span>

<span data-ttu-id="3a074-864">Microsoft.EntityFrameworkCore.SqlServer war zuvor von System.Data.SqlClient abhängig.</span><span class="sxs-lookup"><span data-stu-id="3a074-864">Microsoft.EntityFrameworkCore.SqlServer previously depended on System.Data.SqlClient.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="3a074-865">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-865">New behavior</span></span>

<span data-ttu-id="3a074-866">Das Paket wurde so aktualisiert, dass es jetzt von Microsoft.Data.SqlClient abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-866">We've updated our package to depend on Microsoft.Data.SqlClient.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-867">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-867">Why</span></span>

<span data-ttu-id="3a074-868">Microsoft SQL Server ist zukünftig der Haupttreiber für den Datenzugriff für SQL Server, und System.Data.SqlClient wird nicht länger der Fokus bei der Entwicklung sein.</span><span class="sxs-lookup"><span data-stu-id="3a074-868">Microsoft.Data.SqlClient is the flagship data access driver for SQL Server going forward, and System.Data.SqlClient no longer be the focus of development.</span></span>
<span data-ttu-id="3a074-869">Einige wichtige Features wie Always Encrypted sind nur in Microsoft.Data.SqlClient verfügbar.</span><span class="sxs-lookup"><span data-stu-id="3a074-869">Some important features, such as Always Encrypted, are only available on Microsoft.Data.SqlClient.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-870">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-870">Mitigations</span></span>

<span data-ttu-id="3a074-871">Wenn Ihr Code eine direkte Abhängigkeit von System.Data.SqlClient hat, müssen Sie diesen ändern, damit er stattdessen auf Microsoft.Data.SqlClient verweist. Da die beiden Pakete einen hohen Grad an API-Kompatibilität vorweisen, sollte dafür nur eine einfache Paket- und Namespaceänderung erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="3a074-871">If your code takes a direct dependency on System.Data.SqlClient, you must change it to reference Microsoft.Data.SqlClient instead; as the two packages maintain a very high degree of API compatibility, this should only be a simple package and namespace change.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="3a074-872">Beziehungen mit mehreren mehrdeutigen Selbstverweisen müssen nun konfiguriert werden</span><span class="sxs-lookup"><span data-stu-id="3a074-872">Multiple ambiguous self-referencing relationships must be configured</span></span>

[<span data-ttu-id="3a074-873">Issue #13573</span><span class="sxs-lookup"><span data-stu-id="3a074-873">Tracking Issue #13573</span></span>](https://github.com/dotnet/efcore/issues/13573)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-874">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-874">Old behavior</span></span>

<span data-ttu-id="3a074-875">Ein Entitätstyp, der über unidirektionale Navigationseigenschaften mit mehreren Selbstverweisen und über übereinstimmende Fremdschlüssel verfügte, wurde bislang fälschlicherweise als einfache Beziehung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="3a074-875">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="3a074-876">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a074-876">For example:</span></span>

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

#### <a name="new-behavior"></a><span data-ttu-id="3a074-877">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-877">New behavior</span></span>

<span data-ttu-id="3a074-878">Dieses Szenario wird nun beim Erstellen von Modellen erkannt. Außerdem wird eine Ausnahme ausgelöst, in der darauf hingewiesen wird, dass das Modell mehrdeutig ist.</span><span class="sxs-lookup"><span data-stu-id="3a074-878">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-879">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-879">Why</span></span>

<span data-ttu-id="3a074-880">Das resultierende Modell war mehrdeutig und führte in diesem Fall üblicherweise zu falschen Ergebnissen.</span><span class="sxs-lookup"><span data-stu-id="3a074-880">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-881">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-881">Mitigations</span></span>

<span data-ttu-id="3a074-882">Konfigurieren Sie die Beziehung vollständig.</span><span class="sxs-lookup"><span data-stu-id="3a074-882">Use full configuration of the relationship.</span></span> <span data-ttu-id="3a074-883">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a074-883">For example:</span></span>

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

### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a><span data-ttu-id="3a074-884">DbFunction.Schema ist NULL oder eine leere Zeichenfolge und ist deshalb so konfiguriert, dass es im Standardschema des Modells ist</span><span class="sxs-lookup"><span data-stu-id="3a074-884">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>

[<span data-ttu-id="3a074-885">Issue #12757</span><span class="sxs-lookup"><span data-stu-id="3a074-885">Tracking Issue #12757</span></span>](https://github.com/dotnet/efcore/issues/12757)

#### <a name="old-behavior"></a><span data-ttu-id="3a074-886">Altes Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-886">Old behavior</span></span>

<span data-ttu-id="3a074-887">Eine mit dem Schema als leere Zeichenfolge konfigurierte DbFunction wurde als integrierte Funktion ohne Schema behandelt.</span><span class="sxs-lookup"><span data-stu-id="3a074-887">A DbFunction configured with schema as an empty string was treated as built-in function without a schema.</span></span> <span data-ttu-id="3a074-888">Der folgende Code ordnet z. B. die CLR-Funktion `DatePart` der integrierten Funktion `DATEPART` auf SqlServer zu.</span><span class="sxs-lookup"><span data-stu-id="3a074-888">For example following code will map `DatePart` CLR function to `DATEPART` built-in function on SqlServer.</span></span>

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

#### <a name="new-behavior"></a><span data-ttu-id="3a074-889">Neues Verhalten</span><span class="sxs-lookup"><span data-stu-id="3a074-889">New behavior</span></span>

<span data-ttu-id="3a074-890">Alle DbFunction-Zuordnungen werden als einer benutzerdefinierten Funktion zugeordnet betrachtet.</span><span class="sxs-lookup"><span data-stu-id="3a074-890">All DbFunction mappings are considered to be mapped to user defined functions.</span></span> <span data-ttu-id="3a074-891">Daher würde die Funktion durch einen leeren Zeichenfolgenwert in das Standardschema für das Modell eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="3a074-891">Hence empty string value would put the function inside the default schema for the model.</span></span> <span data-ttu-id="3a074-892">Dies könnte das Schema sein, das explizit über eine fließende `modelBuilder.HasDefaultSchema()`- oder anderweitig über eine `dbo`-API konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="3a074-892">Which could be the schema configured explicitly via fluent API `modelBuilder.HasDefaultSchema()` or `dbo` otherwise.</span></span>

#### <a name="why"></a><span data-ttu-id="3a074-893">Warum?</span><span class="sxs-lookup"><span data-stu-id="3a074-893">Why</span></span>

<span data-ttu-id="3a074-894">Weil das Schema vorher leer war, konnte man erreichen, dass die Funktion integriert, aber die Logik nur für SqlServer anwendbar war, wo integrierte Funktionen nicht zu einem Schema gehören.</span><span class="sxs-lookup"><span data-stu-id="3a074-894">Previously schema being empty was a way to treat that function is built-in but that logic is only applicable for SqlServer where built-in functions do not belong to any schema.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="3a074-895">Gegenmaßnahmen</span><span class="sxs-lookup"><span data-stu-id="3a074-895">Mitigations</span></span>

<span data-ttu-id="3a074-896">Konfigurieren Sie die Übersetzung von DbFunction manuell, um Sie einer integrierten Funktion zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="3a074-896">Configure DbFunction's translation manually to map it to a built-in function.</span></span>

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```

<a name="netstandard21"></a>

### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20-reverted"></a><span data-ttu-id="3a074-897">~~EF Core 3.0 zielt auf .NET Standard 2.1 und nicht auf .NET Standard 2.0 ab~~ Zurückgesetzt</span><span class="sxs-lookup"><span data-stu-id="3a074-897">~~EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0~~ Reverted</span></span>

[<span data-ttu-id="3a074-898">Issue #15498</span><span class="sxs-lookup"><span data-stu-id="3a074-898">Tracking Issue #15498</span></span>](https://github.com/dotnet/efcore/issues/15498)

<span data-ttu-id="3a074-899">EF Core 3.0 zielt auf .NET Standard 2.1 ab, was ein Breaking Change ist, der .NET Framework-Anwendungen ausschließt.</span><span class="sxs-lookup"><span data-stu-id="3a074-899">EF Core 3.0 targets .NET Standard 2.1, which is a breaking change which excludes .NET Framework applications.</span></span> <span data-ttu-id="3a074-900">EF Core 3.1 hat dies zurückgesetzt und zielt wieder auf .NET Standard 2.0 ab.</span><span class="sxs-lookup"><span data-stu-id="3a074-900">EF Core 3.1 reverted this and targets .NET Standard 2.0 again.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="3a074-901">~~Die Abfrageausführung wird auf Debugebene protokolliert~~ – zurückgesetzt</span><span class="sxs-lookup"><span data-stu-id="3a074-901">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="3a074-902">Issue #14523</span><span class="sxs-lookup"><span data-stu-id="3a074-902">Tracking Issue #14523</span></span>](https://github.com/dotnet/efcore/issues/14523)

<span data-ttu-id="3a074-903">Wir haben diese Änderung zurückgesetzt, weil die neue Konfiguration in EF Core 3.0 die Angabe des Protokolliergrads für jedes Ereignis durch die Anwendung ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="3a074-903">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="3a074-904">Um z.B. die Protokollierung von SQL zu `Debug` zu ändern, konfigurieren Sie den Grad explizit in `OnConfiguring` oder `AddDbContext`:</span><span class="sxs-lookup"><span data-stu-id="3a074-904">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```
