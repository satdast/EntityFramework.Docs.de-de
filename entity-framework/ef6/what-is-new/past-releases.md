---
title: Frühere Releases von Entity Framework-EF6
description: Frühere Releases von Entity Framework
author: divega
ms.date: 09/12/2019
uid: ef6/what-is-new/past-releases
ms.openlocfilehash: 97ad8015f5302adbe98a63132f1b34009aa68468
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073642"
---
# <a name="past-releases-of-entity-framework"></a>Frühere Releases von Entity Framework

Die erste Version von Entity Framework wurde in 2008 als Teil von .NET Framework 3,5 SP1 und Visual Studio 2008 SP1 veröffentlicht.

Beginnend mit der EF 4.1-Version wurde sie als das [nuget-Paket "EntityFramework](https://www.nuget.org/packages/EntityFramework/) " ausgeliefert (derzeit eines der beliebtesten Pakete auf NuGet.org).

Zwischen den Versionen 4,1 und 5,0 hat das nuget-Paket "EntityFramework" die EF-Bibliotheken erweitert, die als Teil von .NET Framework ausgeliefert wurden.

Ab Version 6 wurde EF zu einem Open Source-Projekt und wurde auch vollständig aus der .NET Framework entfernt.
Das heißt, wenn Sie das nuget-Paket "EntityFramework Version 6" zu einer Anwendung hinzufügen, erhalten Sie eine vollständige Kopie der EF-Bibliothek, die nicht von den EF-Bits abhängig ist, die als Teil von .NET Framework ausgeliefert werden.
Dadurch wurde das Tempo der Entwicklung und Bereitstellung neuer Features etwas beschleunigt.

Im Juni 2016 haben wir EF Core 1,0 veröffentlicht. EF Core basiert auf einer neuen Codebasis und ist als eine einfachere und erweiterbare Version von EF konzipiert.
Derzeit ist EF Core der Hauptschwerpunkt der Entwicklung für das Entity Framework Team bei Microsoft.
Dies bedeutet, dass für EF6 keine neuen Haupt Features geplant sind. EF6 wird jedoch weiterhin als Open-Source-Projekt und als unterstütztes Microsoft-Produkt verwaltet.

Im folgenden finden Sie die Liste der früheren Releases in umgekehrter chronologischer Reihenfolge mit Informationen zu den neuen Features, die in den einzelnen Releases eingeführt wurden.

## <a name="ef-tools-update-in-visual-studio-2017-157"></a>Update für die EF-Tools in Visual Studio 2017 15.7
Im Mai 2018 haben wir eine aktualisierte Version der EF-Tools als Teil von Visual Studio 2017 15.7 veröffentlicht.
Sie enthält Verbesserungen für einige übliche Problempunkte:

- Korrekturen für einige Fehler beim Benutzeroberflächenzugriff
- Problemumgehung für die SQL Server-Leistungsregression beim Generieren von Modellen aus vorhandenen Datenbanken [#4](https://github.com/aspnet/entityframework6/issues/4)
- Unterstützung zum Aktualisieren von Modellen für größere Modelle in SQL Server [#185](https://github.com/aspnet/EntityFramework6/issues/185)

Eine weitere Verbesserung dieser neuen Version der EF-Tools besteht darin, dass die EF 6.2-Runtime installiert wird, wenn ein Modell in einem neuen Projekt erstellt wird. Bei älteren Versionen von Visual Studio ist es möglich, die EF 6.2-Runtime (sowie alle früheren Version von Entity Framework) zu verwenden, indem Sie die entsprechende Version des NuGet-Pakets installieren.

## <a name="ef-620"></a>EF 6.2.0
Die EF 6.2-Runtime wurde im Januar 2017 für NuGet veröffentlicht.
Es ist vor allem der Verdienst unserer Community von Open-Source-Mitwirkenden, dass EF 6.2 zahlreiche [Fehlerbehebungen](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-bug) und [Produktverbesserungen](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-enhancement%20) enthält.

Hier ist eine kurze Liste der wichtigsten Änderungen, die die EF 6.2-Runtime betreffen:

- Startzeit reduzieren, indem abgeschlossene Code First-Modelle aus einem beständigen Cache geladen werden [#275](https://github.com/aspnet/EntityFramework6/issues/275)
- Fluent-API zum Definieren von Indizes [#274](https://github.com/aspnet/EntityFramework6/issues/274)
- DbFunctions.Like() hinzufügen, um das Schreiben von LINQ-Abfragen zu aktivieren, die in SQL als LIKE übersetzt werden [#241](https://github.com/aspnet/EntityFramework6/issues/241)
- Migrate.exe unterstützt jetzt die Option „-script“ [#240](https://github.com/aspnet/EntityFramework6/issues/240)
- EF6 funktioniert jetzt mit Schlüsselwerten, die von einer Sequenz in SQL Server generiert wurden [#165](https://github.com/aspnet/EntityFramework6/issues/165)
- Updateliste der vorübergehenden Fehler für die SQL Azure-Ausführungsstrategie [#83](https://github.com/aspnet/EntityFramework6/issues/83)
- Fehler: Wiederholen von Abfragen oder SQL-Befehlen schlägt mit folgendem Hinweis fehl: „The SqlParameter is already contained by another SqlParameterCollection“ (Der SqlParameter ist bereits in einer anderen SqlParameterCollection enthalten) [#81](https://github.com/aspnet/EntityFramework6/issues/81)
- Fehler: Bei der Auswertung von DbQuery.ToString() tritt häufig ein Timeout im Debugger auf [#73](https://github.com/aspnet/EntityFramework6/issues/73)

## <a name="ef-613"></a>EF 6.1.3
Die EF 6.1.3 Runtime wurde im Oktober von 2015 für nuget freigegeben.
Diese Version enthält nur Korrekturen für Fehler mit hoher Priorität und Regressionen, die in der 6.1.2-Version gemeldet werden.
Die Korrekturen umfassen Folgendes:

- Query: Regression in EF 6.1.2: OUTER APPLY führte und komplexere Abfragen für 1:1-Beziehungen und "Let"-Klausel ein.
- TPT-Problem beim Ausblenden der Basisklassen Eigenschaft in der geerbten Klasse
- Dbmigration. SQL schlägt fehl, wenn das Wort "Go" im Text enthalten ist.
- Create Compatibility-Flag für die Unterstützung von UnionAll und INTERSECT-Unterstützung
- Abfragen mit mehreren includes funktionieren in 6.1.2 nicht (arbeiten in 6.1.1).
- "Ausnahme Fehler in der SQL-Syntax" nach dem Upgrade von EF 6.1.1 auf 6.1.2

## <a name="ef-612"></a>EF 6.1.2
Die EF 6.1.2 Runtime wurde im Dezember von 2014 für nuget freigegeben.
Bei dieser Version geht es hauptsächlich um Fehlerbehebungen. Wir haben auch einige wichtige Änderungen von Mitgliedern der Community akzeptiert:
- **Abfragecache Parameter können aus der APP/web.config-urationsdatei konfiguriert werden.**
    ``` xml
    <entityFramework>
      <queryCache size='1000' cleaningIntervalInSeconds='-1'/>
    </entityFramework>
    ```
- Mit den **sqlfile-und sqlresource-Methoden in dbmigration** können Sie ein SQL-Skript ausführen, das als Datei oder eingebettete Ressource gespeichert ist.

## <a name="ef-611"></a>EF 6.1.1
Die EF 6.1.1 Runtime wurde im Juni von 2014 für nuget freigegeben.
Diese Version enthält Fehlerbehebungen für Probleme, die eine Reihe von Personen gefunden hat. Unter anderem:
- Designer: Fehler beim Öffnen von EF5 edmx mit dezimaler Genauigkeit im EF6-Designer
- Die Standard Instanz-Erkennungs Logik für localdb funktioniert nicht mit SQL Server 2014

## <a name="ef-610"></a>EF 6.1.0
Die EF 6.1.0 Runtime wurde im März von 2014 für nuget freigegeben.
Dieses geringfügige Update umfasst eine beträchtliche Anzahl neuer Features:

- Die Tool **Konsolidierung** bietet eine konsistente Möglichkeit zum Erstellen eines neuen EF-Modells. Dieses Feature [erweitert den ADO.NET Entity Data Model-Assistenten, um das Erstellen von Code First Modellen](xref:ef6/modeling/code-first/workflows/existing-database), einschließlich Reverse Engineering aus einer vorhandenen Datenbank, zu unterstützen. Diese Features waren zuvor in den EF Power Tools in der Beta Qualität verfügbar.
- Die **[Behandlung von transaktionscommitfehlern](xref:ef6/fundamentals/connection-resiliency/commit-failures)** bietet commitfailurehandler, der die neu eingeführte Möglichkeit zum Abfangen von Transaktions Vorgängen nutzt. Commitfailurehandler ermöglicht die automatische Wiederherstellung nach Verbindungsfehlern beim Ausführen eines Commits für eine Transaktion.
- **[Indexattribute](xref:ef6/modeling/code-first/data-annotations)** ermöglicht das Angeben von Indizes durch Platzieren eines `[Index]` Attributs für eine Eigenschaft (oder Eigenschaften) im Code First Modell. Code First erstellt dann einen entsprechenden Index in der Datenbank.
- **Die öffentliche Mapping-API** bietet Zugriff auf die Informationen, die EF für die Zuordnung von Eigenschaften und Typen zu Spalten und Tabellen in der Datenbank bietet. In früheren Versionen war diese API intern.
- Die **[Möglichkeit, Interceptors über die app/Web.config-Datei zu konfigurieren](xref:ef6/fundamentals/configuring/config-file)** , ermöglicht das Hinzufügen von Interceptors, ohne die Anwendung neu kompilieren zu müssen.
- **System. Data. Entity. Infrastructure. intercep. databaselogger**ist ein neuer Interceptor, mit dem Sie problemlos alle Daten Bank Vorgänge in einer Datei protokollieren können. In Kombination mit der vorherigen Funktion können Sie auf diese Weise problemlos [auf die Protokollierung von Daten Bank Vorgängen für eine bereitgestellte Anwendung umstellen](xref:ef6/fundamentals/configuring/config-file), ohne dass eine erneute Kompilierung erforderlich ist.
- Die **migrationsmodell-Änderungs Erkennung** wurde verbessert, sodass das Gerüst für Migrationen genauer ist. die Leistung des Änderungs Erkennungsprozesses wurde ebenfalls verbessert.
- **Leistungsverbesserungen** einschließlich reduzierter Daten Bank Vorgänge während der Initialisierung, Optimierungen für den NULL-Gleichheits Vergleich in LINQ-Abfragen, schnellere Ansichts Generierung (Modell Erstellung) in weiteren Szenarien und effizientere Materialisierung von nach verfolgten Entitäten mit mehreren Zuordnungen.

## <a name="ef-602"></a>EF 6.0.2
Die EF 6.0.2 Runtime wurde im Dezember von 2013 für nuget freigegeben.
Diese Patchversion ist auf das Beheben von Problemen beschränkt, die in der EF6-Version (Regressionen in Leistung/Verhalten seit EF5) eingeführt wurden.

## <a name="ef-601"></a>EF 6.0.1
Die EF 6.0.1 Runtime wurde für nuget im Oktober von 2013 gleichzeitig mit EF 6.0.0 freigegeben, da letztere in eine Version von Visual Studio eingebettet war, die einige Monate vor gesperrt war.
Diese Patchversion ist auf das Beheben von Problemen beschränkt, die in der EF6-Version (Regressionen in Leistung/Verhalten seit EF5) eingeführt wurden.
Die bemerkenswertesten Änderungen waren die Behebung einiger Leistungsprobleme während der Aufwärmphase von EF-Modellen.
Dies war wichtig, da die Aufwärm Leistung in EF6 ein Schwerpunktbereich war. diese Probleme stellten einige der anderen Leistungssteigerungen in EF6 dar.

## <a name="ef-60"></a>EF 6,0
Die EF 6.0.0 Runtime wurde im Oktober von 2013 für nuget freigegeben.
Dies ist die erste Version, bei der eine vollständige EF-Laufzeit in das [nuget-Paket "EntityFramework](https://www.nuget.org/packages/EntityFramework/) " eingefügt wird, das nicht von den EF-Bits abhängt, die Teil der .NET Framework sind.
Das Verschieben der restlichen Teile der Laufzeit in das nuget-Paket erforderte eine Reihe von Breaking Change für vorhandenen Code.
Weitere Informationen zu den manuellen Schritten, die für das Upgrade erforderlich sind, finden Sie im Abschnitt zum [Upgrade auf Entity Framework 6](xref:ef6/what-is-new/upgrading-to-ef6) .

Diese Version enthält zahlreiche neue Features.
Die folgenden Features funktionieren für Modelle, die mit Code First oder dem EF-Designer erstellt wurden:

- Die asynchrone **[Abfrage und Speicherung](xref:ef6/fundamentals/async)** bietet Unterstützung für die aufgabenbasierten asynchronen Muster, die in .NET 4,5 eingeführt wurden.
- **[Verbindungsresilienz](xref:ef6/fundamentals/connection-resiliency/retry-logic)** ermöglicht die automatische Wiederherstellung nach vorübergehenden Verbindungsfehlern.
- Mit der **[Code basierten Konfiguration](xref:ef6/fundamentals/configuring/code-based)** haben Sie die Möglichkeit, Konfigurations – auszuführen, die normalerweise in einer Konfigurationsdatei – im Code ausgeführt wurde.
- Die **[Abhängigkeitsauflösung](xref:ef6/fundamentals/configuring/dependency-resolution)** führt die Unterstützung für das dienstlocatormuster ein, und wir haben einige Funktionen erläutert, die durch benutzerdefinierte Implementierungen ersetzt werden können.
- Das **[abfangen bzw. die SQL-Protokollierung](xref:ef6/fundamentals/logging-and-interception)** bietet auf niedriger Ebene Bausteine für die Abfang Funktion von EF-Vorgängen mit einfacher, auf der Grundlage von
- Durch **Verbesserungen der Prüfbarkeit** ist es einfacher, Test Doubles für dbcontext und dbset zu erstellen, wenn Sie [ein Frameworks verwenden](xref:ef6/fundamentals/testing/mocking) oder [eigene Test Doubles schreiben](xref:ef6/fundamentals/testing/writing-test-doubles).
- **[Dbcontext kann nun mit einer bereits geöffneten DbConnection erstellt werden](xref:ef6/fundamentals/connection-management)** . Dies ermöglicht Szenarien, in denen es hilfreich wäre, wenn die Verbindung beim Erstellen des Kontexts geöffnet werden könnte (z. b. durch Freigeben einer Verbindung zwischen Komponenten, bei denen der Status der Verbindung nicht garantiert werden kann).
- Die **[verbesserte Transaktionsunterstützung](xref:ef6/saving/transactions)** bietet Unterstützung für eine Transaktion außerhalb des Frameworks sowie verbesserte Möglichkeiten, eine Transaktion innerhalb des Frameworks zu erstellen.
- Enumerationen **, räumliche und bessere Leistung in .NET 4,0** : indem die Kernkomponenten, die in der .NET Framework verwendet wurden, in das EF-nuget-Paket verschoben werden, bieten wir nun Unterstützung für Enumerationen, räumliche Datentypen und Leistungsverbesserungen von EF5 auf .NET 4,0.
- **Verbesserte Leistung von Enumerable. enthält in LINQ-Abfragen**.
- **Verbesserte Aufwärmzeit (Ansichts Generierung)**, insbesondere bei großen Modellen.
- **Austauschbare Pluralisierung &amp; Der singularisierungsdienst**.
- **Benutzerdefinierte Implementierungen von "Gleichheits-oder GetHashCode** " für Entitäts Klassen werden jetzt unterstützt.
- **Dbset. AddRange/RemoveRange** bietet eine optimierte Möglichkeit zum Hinzufügen oder Entfernen mehrerer Entitäten aus einer Menge.
- **Dbchangetracker. HasChanges** bietet eine einfache und effiziente Möglichkeit, um festzustellen, ob ausstehende Änderungen in der Datenbank gespeichert werden müssen.
- **Sqlcefunctions** bietet eine SQL Compact-Entsprechung zu SqlFunctions.

Die folgenden Funktionen gelten nur für Code First:

- Mithilfe von **[benutzerdefinierten Code First Konventionen](xref:ef6/modeling/code-first/conventions/custom)** können Sie eigene Konventionen schreiben, um eine wiederkehrende Konfiguration zu vermeiden. Wir stellen eine einfache API für einfache Konventionen und einige komplexere Bausteine bereit, damit Sie kompliziertere Konventionen erstellen können.
- **[Code First Zuordnung zu gespeicherten Prozeduren INSERT/UPDATE/DELETE](xref:ef6/modeling/code-first/fluent/cud-stored-procedures)** wird jetzt unterstützt.
- **[Idempotente Migrations Skripts](xref:ef6/modeling/code-first/migrations/index)** ermöglichen es Ihnen, ein SQL-Skript zu generieren, das eine Datenbank auf eine beliebige Version bis zur neuesten Version aktualisieren kann.
- Mithilfe der **[konfigurierbaren Migrations](xref:ef6/modeling/code-first/migrations/history-customization)** Verlaufs Tabelle können Sie die Definition der Migrations Verlaufs Tabelle anpassen. Dies ist besonders nützlich für Datenbankanbieter, die die entsprechenden Datentypen benötigen usw., um anzugeben, dass die Migrations Verlaufs Tabelle ordnungsgemäß funktioniert.
- **Mehrere Kontexte pro Datenbank** entfernen bei der Verwendung von Migrationen oder bei der automatischen Erstellung der Datenbank durch Code First die vorherige Einschränkung von einem Code First Modell pro Datenbank.
- **[Dbmodelbuilder. hasdefaultschema](xref:ef6/modeling/code-first/fluent/types-and-properties)** ist eine neue Code First-API, mit der das standardmäßige Datenbankschema für ein Code First Modell an einem Ort konfiguriert werden kann. Zuvor war das Code First Standardschema für dbo hart codiert, &quot; &quot; und die einzige Möglichkeit zum Konfigurieren des Schemas, zu dem eine Tabelle gehörte, war die-API-API.
- **DbModelBuilder.Configurationen. Mit der addfromassembly-Methode** können Sie problemlos alle Konfigurations Klassen hinzufügen, die in einer Assembly definiert sind, wenn Sie Konfigurations Klassen mit der Code First fließend-API verwenden.
- Mithilfe von **[benutzerdefinierten Migrations Vorgängen](https://romiller.com/2013/02/27/ef6-writing-your-own-code-first-migration-operations/)** können Sie zusätzliche Vorgänge hinzufügen, die in Ihren Code basierten Migrationen verwendet werden.
- **Die standardmäßige Transaktions Isolationsstufe wird in READ_COMMITTED_SNAPSHOT** für Datenbanken geändert, die mit Code First erstellt wurden, was eine höhere Skalierbarkeit und weniger Deadlocks ermöglicht.
- **Entitäts Typen und komplexe Typen können jetzt geschaltenende Klassen sein**.

## <a name="ef-50"></a>EF 5,0
Die EF 5.0.0 Runtime wurde im August von 2012 für nuget veröffentlicht.
In dieser Version werden einige neue Features eingeführt, u.a. enumerationsunterstützung, Tabellenwert Funktionen, räumliche Datentypen und verschiedene Leistungsverbesserungen.

Der Entity Framework Designer in Visual Studio 2012 bietet auch Unterstützung für mehrere Diagramme pro Modell, die Farbgebung von Formen auf der Entwurfs Oberfläche und den Batch Import gespeicherter Prozeduren.

Im folgenden finden Sie eine Liste von Inhalten, die wir speziell für die EF 5-Version eingefügt haben:

-   [EF 5-releasebeitrag](https://blogs.msdn.com/b/adonet/archive/2012/08/15/ef5-released.aspx)
-   Neue Features in EF5
    -   [Aufzählungs Unterstützung in Code First](xref:ef6/modeling/code-first/data-types/enums)
    -   [Aufzählungs Unterstützung im EF-Designer](xref:ef6/modeling/designer/data-types/enums)
    -   [Räumliche Datentypen in Code First](xref:ef6/modeling/code-first/data-types/spatial)
    -   [Räumliche Datentypen im EF-Designer](xref:ef6/modeling/designer/data-types/spatial)
    -   [Anbieter Unterstützung für räumliche Typen](xref:ef6/fundamentals/providers/spatial-support)
    -   [Tabellenwertfunktionen](xref:ef6/modeling/designer/advanced/tvfs)
    -   [Mehrere Diagramme pro Modell](xref:ef6/modeling/designer/multiple-diagrams)
-   Einrichten des Modells
    -   [Erstellen eines Modells](xref:ef6/modeling/index)
    -   [Verbindungen und Modelle](xref:ef6/fundamentals/configuring/connection-strings)
    -   [Leistungsaspekte](xref:ef6/fundamentals/performance/perf-whitepaper)
    -   [Arbeiten mit Microsoft SQL Azure](xref:ef6/fundamentals/connection-resiliency/retry-logic)
    -   [Konfigurationsdatei Einstellungen](xref:ef6/fundamentals/configuring/config-file)
    -   [Glossar](xref:ef6/resources/glossary)
    -   Code First
        -   [Code First einer neuen Datenbank (Exemplarische Vorgehensweise und Video)](xref:ef6/modeling/code-first/workflows/new-database)
        -   [Code First einer vorhandenen Datenbank (Exemplarische Vorgehensweise und Video)](xref:ef6/modeling/code-first/workflows/existing-database)
        -   [Konventionen](xref:ef6/modeling/code-first/conventions/built-in)
        -   [Daten Anmerkungen](xref:ef6/modeling/code-first/data-annotations)
        -   [Fließende Eigenschaften der API-Konfiguration/Zuordnung & Typen](xref:ef6/modeling/code-first/fluent/types-and-properties)
        -   [Fließende API: Konfigurieren von Beziehungen](xref:ef6/modeling/code-first/fluent/relationships)
        -   [Fließende API mit VB.net](xref:ef6/modeling/code-first/fluent/vb)
        -   [Code First-Migrationen](xref:ef6/modeling/code-first/migrations/index)
        -   [Automatisches Code First-Migrationen](xref:ef6/modeling/code-first/migrations/automatic)
        -   [Migrate.exe](xref:ef6/modeling/code-first/migrations/migrate-exe)
        -   [Definieren von dbsets](xref:ef6/modeling/code-first/dbsets)
    -   EF-Designer
        -   [Model First (Exemplarische Vorgehensweise und Video)](xref:ef6/modeling/designer/workflows/model-first)
        -   [Database First (Exemplarische Vorgehensweise und Video)](xref:ef6/modeling/designer/workflows/database-first)
        -   [Komplexe Typen](xref:ef6/modeling/designer/data-types/complex-types)
        -   [Zuordnungen/Beziehungen](xref:ef6/modeling/designer/relationships)
        -   [TPT-Vererbungsmuster](xref:ef6/modeling/designer/inheritance/tpt)
        -   [TPH-Vererbungsmuster](xref:ef6/modeling/designer/inheritance/tph)
        -   [Abfragen mit gespeicherten Prozeduren](xref:ef6/modeling/designer/stored-procedures/query)
        -   [Gespeicherte Prozeduren mit mehreren Resultsets](xref:ef6/modeling/designer/advanced/multiple-result-sets)
        -   [Einfügen, aktualisieren & löschen mit gespeicherten Prozeduren](xref:ef6/modeling/designer/stored-procedures/cud)
        -   [Zuordnen einer Entität zu mehreren Tabellen (Entitäts Aufteilung)](xref:ef6/modeling/designer/entity-splitting)
        -   [Zuordnen mehrerer Entitäten zu einer Tabelle (Tabellen Aufteilung)](xref:ef6/modeling/designer/table-splitting)
        -   [Definieren von Abfragen](xref:ef6/modeling/designer/advanced/defining-query)
        -   [Code Generierungs Vorlagen](xref:ef6/modeling/designer/codegen/index)
        -   [Zurücksetzen auf ObjectContext](xref:ef6/modeling/designer/codegen/legacy-objectcontext)
-   Verwenden des Modells
    -   [Arbeiten mit DbContext](xref:ef6/fundamentals/working-with-dbcontext)
    -   [Abfragen/suchen von Entitäten](xref:ef6/querying/index)
    -   [Arbeiten mit Beziehungen](xref:ef6/fundamentals/relationships)
    -   [Laden verwandter Entitäten](xref:ef6/querying/related-data)
    -   [Arbeiten mit lokalen Daten](xref:ef6/querying/local-data)
    -   [N-Tier-Anwendungen](xref:ef6/fundamentals/disconnected-entities/index)
    -   [RAW-SQL-Abfragen](xref:ef6/querying/raw-sql)
    -   [Muster für optimistische Parallelität](xref:ef6/saving/concurrency)
    -   [Arbeiten mit Proxys](xref:ef6/fundamentals/proxies)
    -   [Automatisches Erkennen von Änderungen](xref:ef6/saving/change-tracking/auto-detect-changes)
    -   [Abfragen ohne Nachverfolgung](xref:ef6/querying/no-tracking)
    -   [Load-Methode](xref:ef6/querying/load-method)
    -   [Hinzufügen/anfügen und Entitäts Zustände](xref:ef6/saving/change-tracking/entity-state)
    -   [Arbeiten mit Eigenschafts Werten](xref:ef6/saving/change-tracking/property-values)
    -   [Datenbindung mit WPF (Windows Presentation Foundation)](xref:ef6/fundamentals/databinding/wpf)
    -   [Datenbindung mit WinForms (Windows Forms)](xref:ef6/fundamentals/databinding/winforms)

## <a name="ef-431"></a>EF 4.3.1
Die EF 4.3.1 Runtime wurde im Februar 2012 kurz nach EF 4.3.0 für nuget veröffentlicht.
Diese Patchversion enthielt einige Fehlerbehebungen für die EF 4,3-Version und führte zu einer besseren localdb-Unterstützung für Kunden, die EF 4,3 mit Visual Studio 2012 verwenden.

Im folgenden finden Sie eine Liste von Inhalten, die wir speziell für die EF 4.3.1-Version eingefügt haben. die meisten Inhalte, die für EF 4,1 bereitgestellt werden, gelten weiterhin auch für EF 4,3:

-   [EF 4.3.1 Release-Blog Beitrag](https://blogs.msdn.com/b/adonet/archive/2012/02/29/ef4-3-1-and-ef5-beta-1-available-on-nuget.aspx)

## <a name="ef-43"></a>EF 4,3
EF 4.3.0 Runtime wurde im Februar von 2012 für nuget freigegeben.
Diese Version enthielt das neue Code First-Migrationen Feature, mit dem eine Datenbank, die von Code First erstellt wurde, inkrementell geändert werden kann, wenn sich das Code First Modell entwickelt.

Im folgenden finden Sie eine Liste von Inhalten, die wir speziell für die EF 4,3-Version eingefügt haben. die meisten Inhalte, die für EF 4,1 bereitgestellt werden, gelten weiterhin auch für EF 4,3:
-   [EF 4,3-releasebeitrag](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-released.aspx)
-   [Exemplarische Vorgehensweise zu EF 4,3-Code basierten Migrationen](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-code-based-migrations-walkthrough.aspx)
-   [Exemplarische Vorgehensweise zu EF 4,3 Automatic Migrationen](https://blogs.msdn.com/b/adonet/archive/2012/02/09/ef-4-3-automatic-migrations-walkthrough.aspx)

## <a name="ef-42"></a>EF 4,2
Die EF 4.2.0-Runtime wurde im November von 2011 für nuget freigegeben.
Diese Version enthält Fehlerbehebungen für die EF 4.1.1-Version.
Da diese Version nur Fehlerbehebungen enthielt, war es möglicherweise die Patch-Version EF 4.1.2, aber wir haben uns entschieden, zu 4,2 zu wechseln, um uns von den in den Versionen 4.1. x verwendeten datumsbasierten patchversions Nummern zu übernehmen und den [semantischen Versionsstandard](https://semver.org) für die semantische Versionsverwaltung zu übernehmen.

Im folgenden finden Sie eine Liste von Inhalten, die wir speziell für die EF 4,2-Version bereitgestellt haben. die Inhalte für EF 4,1 gelten weiterhin auch für EF 4,2:

-   [EF 4,2-releasebeitrag](https://blogs.msdn.com/b/adonet/archive/2011/11/01/ef-4-2-released.aspx)
-   [Exemplarische Vorgehensweise Code First](https://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-code-first-walkthrough.aspx)
-   [Exemplarische Vorgehensweise für Modell & Database First](https://blogs.msdn.com/b/adonet/archive/2011/09/28/ef-4-2-model-amp-database-first-walkthrough.aspx)

## <a name="ef-411"></a>EF 4.1.1
Die EF 4.1.10715 Runtime wurde im Juli von 2011 für nuget freigegeben.
Zusätzlich zu Fehlerbehebungen wurden in dieser Patchversion einige Komponenten eingeführt, um die Entwurfszeit Tools für das Arbeiten mit einem Code First Modell zu vereinfachen.
Diese Komponenten werden von Code First-Migrationen (enthalten in EF 4,3) und EF Power Tools verwendet.

Sie werden feststellen, dass die seltsame Versionsnummer 4.1.10715 des Pakets ist.
Wir haben verwendet, um datumsbasierte Patchversionen zu verwenden, bevor wir beschlossen haben, eine [semantische Versions](https://semver.org)Verwaltung einzuführen.
Stellen Sie sich diese Version als EF 4,1 Patch 1 (oder EF 4.1.1) vor.

Im folgenden finden Sie eine Liste von Inhalten, die wir für die Version 4.1.1 eingefügt haben:

-   [EF 4.1.1-releasebeitrag](https://blogs.msdn.com/b/adonet/archive/2011/07/25/ef-4-1-update-1-released.aspx)

## <a name="ef-41"></a>EF 4,1
Die EF 4.1.10331 Runtime war die erste, die auf nuget veröffentlicht werden soll, im April von 2011.
Diese Version enthielt die vereinfachte dbcontext-API und den Code First-Workflow.

Sie werden feststellen, dass die ungewöhnliche Versionsnummer, 4.1.10331, tatsächlich 4,1 sein sollte. Außerdem gibt es eine 4.1.10311-Version, die "4.1.0-RC" lauten muss ("RC" steht für "Release Candidate").
Wir haben verwendet, um datumsbasierte Patchversionen zu verwenden, bevor wir beschlossen haben, eine [semantische Versions](https://semver.org)Verwaltung einzuführen.

Im folgenden finden Sie eine Liste von Inhalten, die wir für die Version 4,1 von Microsoft eingefügt haben. Viele davon gelten weiterhin für spätere Releases von Entity Framework:

-   [EF 4,1-releasebeitrag](https://blogs.msdn.com/b/adonet/archive/2011/04/11/ef-4-1-released.aspx)
-   [Exemplarische Vorgehensweise Code First](https://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-code-first-walkthrough.aspx)
-   [Exemplarische Vorgehensweise für Modell & Database First](https://blogs.msdn.com/b/adonet/archive/2011/03/15/ef-4-1-model-amp-database-first-walkthrough.aspx)
-   [SQL Azure Verbunde und die Entity Framework](https://blogs.msdn.com/b/adonet/archive/2012/01/10/sql-azure-federations-and-the-entity-framework.aspx)

## <a name="ef-40"></a>EF 4,0
Diese Version war in .NET Framework 4 und Visual Studio 2010 im April 2010 enthalten.
Zu den wichtigen neuen Features in dieser Version gehörten poco-Unterstützung, Fremdschlüssel Zuordnung, Lazy Loading, Testability-Verbesserungen, anpassbare Codegenerierung und der Model First Workflow.

Obwohl es sich um die zweite Version von Entity Framework handelt, hieß es EF 4, um die .NET Framework Version auszurichten, mit der es ausgeliefert wurde.
Nach dieser Version haben wir begonnen, Entity Framework für nuget verfügbar zu machen und die semantische Versionsverwaltung zu übernehmen, da wir nicht mehr an die .NET Framework Version gebunden waren.

Beachten Sie, dass einige nachfolgende Versionen von .NET Framework mit wichtigen Aktualisierungen der enthaltenen EF-Bits ausgeliefert wurden.
Tatsächlich wurden viele der neuen Features von EF 5,0 als Verbesserungen an diesen Bits implementiert.
Um die Versionsgeschichte von EF zu rationalisieren, verweisen wir jedoch weiterhin auf die EF-Bits, die Teil der .NET Framework als EF 4,0-Laufzeit sind, während alle neueren Versionen aus dem [nuget-Paket "EntityFramework](https://www.nuget.org/packages/EntityFramework/)" bestehen.

## <a name="ef-35"></a>EF 3,5
Die ursprüngliche Version von Entity Framework war in .NET 3,5 Service Pack 1 und Visual Studio 2008 SP1 enthalten, die im August von 2008 veröffentlicht wurde.
In dieser Version wurde eine grundlegende O/RM-Unterstützung mit dem Database First Workflow bereitgestellt
