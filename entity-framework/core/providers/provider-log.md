---
title: Protokoll von Änderungen, die sich auf den Anbieter auswirken-EF Core
description: Ein Protokoll der Änderungen in Entity Framework Core, die Auswirkungen auf Anbieter haben
author: ajcvickers
ms.author: avickers
ms.date: 08/08/2018
ms.assetid: 7CEF496E-A5B0-4F5F-B68E-529609B23EF9
ms.technology: entity-framework-core
uid: core/providers/provider-log
ms.openlocfilehash: b1377df81ad8697a6fc8d5dd5c23198bdf598368
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618977"
---
# <a name="provider-impacting-changes"></a>Änderungen mit Auswirkungen auf den Anbieter

Diese Seite enthält Links zu Pull Requests, die auf dem EF Core-Repository vorgenommen werden, die möglicherweise von Autoren anderer Datenbankanbieter reagiert werden müssen. Die Absicht besteht darin, einen Ausgangspunkt für Autoren vorhandener Datenbankanbieter von Drittanbietern bereitzustellen, wenn der Anbieter auf eine neue Version aktualisiert wird.

Dieses Protokoll wird mit Änderungen von 2,1 bis 2,2 gestartet. Vor 2,1 verwendeten wir die [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) Bezeichnungen und [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) für unsere Probleme und Pull Requests.

## <a name="22-----3x"></a>2,2---> 3. x

Beachten Sie, dass sich viele der wichtigen [Änderungen auf Anwendungsebene](xref:core/what-is-new/ef-core-3.x/breaking-changes) auch auf Anbieter auswirken.

* <https://github.com/aspnet/EntityFrameworkCore/pull/14022>
  * Veraltete veraltete APIs und reduzierte optionale Parameter Überladungen wurden entfernt.
  * Databasecolumschlag. getunderlyingstoretype () wurde entfernt.
* <https://github.com/aspnet/EntityFrameworkCore/pull/14589>
  * Veraltete APIs entfernt
* <https://github.com/aspnet/EntityFrameworkCore/pull/15044>
  * Unterklassen von chartypemapping wurden möglicherweise aufgrund von Verhaltensänderungen, die zum Beheben einiger Fehler in der Basis Implementierung erforderlich sind, beschädigt.
* <https://github.com/aspnet/EntityFrameworkCore/pull/15090>
  * Es wurde eine Basisklasse für idatabasemodelfactory hinzugefügt und zur Verwendung eines Parameter-Objekts aktualisiert, um zukünftige Unterbrechungen zu vermeiden.
* <https://github.com/aspnet/EntityFrameworkCore/pull/15123>
  * Verwenden Sie Parameter Objekte in migrationssqlgenerator, um zukünftige Unterbrechungen zu vermeiden.
* <https://github.com/aspnet/EntityFrameworkCore/pull/14972>
  * Die explizite Konfiguration von Protokolliergraden erforderte einige Änderungen an APIs, die von Anbietern möglicherweise verwendet werden. Insbesondere wenn Anbieter die Protokollierungs Infrastruktur direkt verwenden, kann diese Änderung die Verwendung von unterbrechen. Außerdem müssen Anbieter, die die-Infrastruktur verwenden (die öffentlich sein wird), von oder abgeleitet werden `LoggingDefinitions` `RelationalLoggingDefinitions` . Beispiele finden Sie in den SQL Server-und in-Memory-Anbietern.
* <https://github.com/aspnet/EntityFrameworkCore/pull/15091>
  * Die Ressourcen Zeichenfolgen Core, Relational und Abstraktionen sind jetzt öffentlich.
  * `CoreLoggerExtensions` und `RelationalLoggerExtensions` sind jetzt öffentlich. Anbieter sollten diese APIs verwenden, wenn Sie Ereignisse protokollieren, die auf der Core-oder relationalen Ebene definiert sind. Nicht direkt auf Protokoll Ressourcen zugreifen; Diese sind immer noch intern.
  * `IRawSqlCommandBuilder` hat sich von einem Singleton-Dienst zu einem Bereichs bezogenen Dienst geändert.
  * `IMigrationsSqlGenerator` hat sich von einem Singleton-Dienst zu einem Bereichs bezogenen Dienst geändert.
* <https://github.com/aspnet/EntityFrameworkCore/pull/14706>
  * Die Infrastruktur zum Aufbau von relationalen Befehlen wurde öffentlich gemacht, sodass Sie von Anbietern sicher verwendet und leicht umgestaltet werden kann.
* <https://github.com/aspnet/EntityFrameworkCore/pull/14733>
  * `ILazyLoader` hat sich von einem Bereichs bezogenen Dienst zu einem vorübergehenden Dienst geändert.
* <https://github.com/aspnet/EntityFrameworkCore/pull/14610>
  * `IUpdateSqlGenerator` hat sich von einem Bereichs bezogenen Dienst zu einem Singleton-Dienst geändert.
  * Außerdem wurde `ISingletonUpdateSqlGenerator` entfernt.
* <https://github.com/aspnet/EntityFrameworkCore/pull/15067>
  * Viele interner Codes, die von Anbietern verwendet wurden, wurden nun öffentlich gemacht.
  * Er sollte nicht mehr als notwendig sein, um darauf zu verweisen, `IndentedStringBuilder` da er von den Orten, die ihn verfügbar gemacht haben, berücksichtigt wurde.
  * Die Verwendungen von `NonCapturingLazyInitializer` sollten durch `LazyInitializer` aus der BCL ersetzt werden.
* <https://github.com/aspnet/EntityFrameworkCore/pull/14608>
  * Diese Änderung wird vollständig im Dokument "Änderungen der Anwendungs Änderungen" behandelt. Für Anbieter kann dies eine Beeinträchtigung der Auswirkung haben, da das Testen von EF Core häufig zu diesem Problem führen kann, sodass sich die Testinfrastruktur so geändert hat, dass dies weniger wahrscheinlich ist.
* <https://github.com/aspnet/EntityFrameworkCore/issues/13961>
  * `EntityMaterializerSource` wurde vereinfacht
* <https://github.com/aspnet/EntityFrameworkCore/pull/14895>
  * StartWith Translation hat sich auf eine Weise geändert, die Anbieter möglicherweise benötigen/benötigen.
* <https://github.com/aspnet/EntityFrameworkCore/pull/15168>
  * Die changesetsedienstedienste wurden geändert. Anbieter sollten jetzt entweder von "providerkonventiontionset" oder "relationalkonventiontionset" erben.
  * Anpassungen können über Dienste hinzugefügt werden `IConventionSetCustomizer` . Dies ist jedoch für die Verwendung durch andere Erweiterungen, nicht für Anbieter vorgesehen.
  * Konventionen, die zur Laufzeit verwendet werden, sollten von aufgelöst werden `IConventionSetBuilder` .
* <https://github.com/aspnet/EntityFrameworkCore/pull/15288>
  * Das datenseeding wurde in einer öffentlichen API umgestaltet, um zu vermeiden, dass interne Typen verwendet werden müssen. Dies wirkt sich nur auf nicht relationale Anbieter aus, da das Seeding von der relationalen Basisklasse für alle relationalen Anbieter verarbeitet wird.

## <a name="21-----22"></a>2,1---> 2,2

### <a name="test-only-changes"></a>Nur-Test-Änderungen

* <https://github.com/aspnet/EntityFrameworkCore/pull/12057> -Anpassbare SQL-delimeter in Tests zulassen
  * Test Änderungen, die nicht strikte Gleit Komma Vergleiche in builtindatatypstestbase zulassen
  * Test Änderungen, die die Wiederverwendung von Abfrage Tests mit unterschiedlichen SQL-Trennzeichen zulassen
* <https://github.com/aspnet/EntityFrameworkCore/pull/12072> -Hinzufügen von dbfunction-Tests zu den Tests der relationalen Spezifikation
  * So können diese Tests für alle Datenbankanbieter ausgeführt werden.
* <https://github.com/aspnet/EntityFrameworkCore/pull/12362> -Async-Test Bereinigung
  * Entfernen von `Wait` aufrufen, nicht benötigtes Async und Umbenennen einiger Testmethoden
* <https://github.com/aspnet/EntityFrameworkCore/pull/12666> -Vereinheitlichte Protokollierungs Test-Infrastruktur
  * `CreateListLoggerFactory`Es wurde eine vorherige Protokollierungs Infrastruktur hinzugefügt und entfernt, in der Anbieter, die diese Tests verwenden, reagieren müssen.
* <https://github.com/aspnet/EntityFrameworkCore/pull/12500> -Ausführen von weiteren Abfrage Tests synchron und asynchron
  * Testnamen und Factoring wurden geändert, sodass Anbieter, die diese Tests verwenden, reagieren müssen.
* <https://github.com/aspnet/EntityFrameworkCore/pull/12766> -Umbenennen von Navigationen im complexnavigingmodell
  * Anbieter, die diese Tests verwenden, müssen möglicherweise reagieren
* <https://github.com/aspnet/EntityFrameworkCore/pull/12141> -Den Kontext an den Pool zurückgeben, anstatt Sie in Funktionstests zu verwerfen
  * Diese Änderung umfasst einige Test Umgestaltung, bei der Anbieter möglicherweise reagieren müssen.

### <a name="test-and-product-code-changes"></a>Test-und Produktcode Änderungen

* <https://github.com/aspnet/EntityFrameworkCore/pull/12109> -Konsolidieren von relationaltypemapping. Clone-Methoden
  * Änderungen in 2,1 in relationaltypemapping, die für eine Vereinfachung in abgeleiteten Klassen zulässig sind. Wir glauben nicht, dass dies zu Anbietern gehörte, aber Anbieter können diese Änderung in den abgeleiteten Typmapping-Klassen nutzen.
* <https://github.com/aspnet/EntityFrameworkCore/pull/12069> -Markierte oder benannte Abfragen
  * Fügt eine Infrastruktur zum Markieren von LINQ-Abfragen hinzu, und diese Tags werden als Kommentare in SQL angezeigt. Dies erfordert möglicherweise, dass Anbieter bei der SQL-Generierung reagieren.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13115> -Unterstützung räumlicher Daten über NTS
  * Ermöglicht das Registrieren von Typzuordnungen und Mitglieds Übersetzer außerhalb des Anbieters.
    * Anbieter müssen die Basisklasse aufzurufen. Findmapping () in ihrer itypmappingsource-Implementierung, damit es funktioniert
  * Verwenden Sie dieses Muster, um dem Anbieter räumliche Unterstützung hinzuzufügen, der über mehrere Anbieter hinweg konsistent ist.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13199> -Erweiterte Debuggen für die Erstellung von Dienstanbietern
  * Ermöglicht dbcontextoptionsextensions, eine neue Schnittstelle zu implementieren, mit der Benutzer verstehen können, warum der interne Dienstanbieter neu erstellt wird.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13289> -Fügt die canConnect-API für die Verwendung durch Integritätsprüfungen hinzu
  * Mit diesem PR wird das Konzept hinzugefügt `CanConnect` , das von ASP.net Core Integritätsprüfungen verwendet wird, um zu bestimmen, ob die Datenbank verfügbar ist. Standardmäßig ruft die relationale Implementierung nur auf `Exist` , aber Anbieter können bei Bedarf etwas anderes implementieren. Nicht relationale Anbieter müssen die neue API implementieren, damit die Integritätsprüfung verwendet werden kann.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13306> -Basis relationaltypemapping aktualisieren, sodass die DbParameter-Größe nicht festgelegt wird
  * Legen Sie die Einstellungs Größe standardmäßig fest, da es zu einem Abschneiden führen kann. Anbieter müssen möglicherweise Ihre eigene Logik hinzufügen, wenn die Größe festgelegt werden muss.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13372> -Reveng: Geben Sie immer den Spaltentyp für Dezimal Spalten an.
  * Konfigurieren Sie immer den Spaltentyp für Dezimal Spalten in einem Gerüst Code, anstatt Sie nach Konvention zu konfigurieren.
  * Anbieter sollten keine Änderungen am Ende benötigen.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13469> -Fügt caseexpression zum Erstellen von SQL-Case-Ausdrücken hinzu.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13648> -Fügt die Möglichkeit hinzu, Typzuordnungen für sqlfunctionexpression anzugeben, um die Rückschlüsse von Speichertypen von Argumenten und Ergebnissen zu verbessern.
