---
title: Plan für Entity Framework Core 5.0
description: Die für Entity Framework Core 5.0 geplanten Features
author: ajcvickers
ms.date: 08/22/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: 5ac7a48a70959b625b7c602ee48dc08a360bee73
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618663"
---
# <a name="plan-for-entity-framework-core-50"></a>Plan für Entity Framework Core 5.0

Wie im [Artikel zum Planungsprozess](xref:core/what-is-new/release-planning) beschrieben, haben wir aus dem Feedback der Stakeholder diesen vorläufigen Plan für EF Core 5.0 erstellt.

> [!IMPORTANT]
> Er befindet sich aktuell in der Entwicklung. Sein Inhalt ist nicht verbindlich. Er dient als Ausgangspunkt und wird durch neues Feedback weiterentwickelt. Es kann also sein, dass die Version 5.0 Funktionen enthalten wird, die bisher nicht geplant waren. Im Gegenzug kann es jedoch auch passieren, dass Funktionen gestrichen werden, die bisher für die Version 5.0 geplant waren.

## <a name="general-information"></a>Allgemeine Informationen

### <a name="version-number-and-release-date"></a>Versionsnummer und Veröffentlichungsdatum

EF Core 5.0 soll [zusammen mit .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/) veröffentlicht werden. Deshalb wurde auch „5.0“ als Versionsnummer gewählt.

### <a name="supported-platforms"></a>Unterstützte Plattformen

EF Core 5.0 soll auf jeder .NET Standard 2.1-Plattform einschließlich .NET 5.0 ausgeführt werden können. Dies ist Teil der allgemeineren .NET-übergreifenden [Plattformzusammenführung zu .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).

EF Core 5.0 kann nicht auf .NET Framework ausgeführt werden.

### <a name="breaking-changes"></a>Breaking Changes

EF Core 5.0 wird einige [Breaking Changes](xref:core/what-is-new/ef-core-5.0/breaking-changes) enthalten. Diese werden jedoch weniger gravierende Auswirkungen als bei EF Core 3.0 haben. Unser Ziel ist es, die Mehrzahl der Anwendungen zu aktualisieren, ohne dass Breaking Changes auftreten.

Dennoch gehen wir davon, dass Datenbankanbieter auf einige Breaking Changes stoßen werden, besonders im Bereich TPT-Unterstützung. Wir gehen jedoch davon aus, dass der Arbeitsaufwand für das Aktualisieren eines Providers auf 5.0 geringer ausfällt als bei 3.0.

## <a name="themes"></a>Designs

Wir haben einige wichtige Bereiche/Themen bestimmt, die die Grundlage für die großen Änderungen in EF Core 5.0 bilden.

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a>Vollständig transparente m:n-Zuordnung nach Konvention

Leitende Entwickler: @smitpatel, @AndriySvyryd und @lajones

Nachverfolgbar über [Issue 10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)

T-Shirt-Größe: L

Status: Vorgehensweise

„m:n“ ist das am [meisten geforderte Feature](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (ca. 506 Stimmen) im GitHub-Backlog.

Die Unterstützung für m:n-Beziehungen kann in drei Hauptbereiche aufgeteilt werden:

* Das Überspringen von Navigationseigenschaften wird im nächsten Artikel behandelt.
* Entitätstypen für Eigenschaftenbehälter. Dank dieser Entitätstypen kann für Entitätsinstanzen ein Standard-CLR-Typ wie `Dictionary` verwendet werden. So ist für einen Entitätstyp kein expliziter CLR-Typ mehr notwendig. Nachverfolgbar über [Issue 9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)
* Sugar für die einfache Konfiguration von m:n-Beziehungen.

Nicht nur das Überspringen von Navigationseigenschaften wird unterstützt, sondern auch folgende weitere Bereiche von m:n werden in EF Core 5.0 integriert, damit Sie diese Option vollständig nutzen können.

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a>M:n-Navigationseigenschaften (auch bekannt als „Navigationseigenschaften überspringen“)

Leitende Entwickler: @smitpatel und @AndriySvyryd

Nachverfolgbar über [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)

T-Shirt-Größe: L

Status: Vorgehensweise

Wie im ersten Artikel beschrieben wurde, hat die m:n-Unterstützung mehrere Aspekte.
In diesem Artikel wird insbesondere die Verwendung des Features zum Überspringen von Navigationseigenschaften nachverfolgt.
Unserer Meinung nach liegt der Wunsch nach m:n-Unterstützung hauptsächlich daran, dass bei Geschäftslogik wie Queries bisher keine Möglichkeit besteht, die „natürlichen“ Beziehungen zu verwenden, ohne auf die Jointabelle verweisen zu müssen.
Der Entitätstyp „Jointabelle“ existiert zwar noch, sollte in Geschäftslogik aber nicht mehr verwendet werden.

## <a name="table-per-type-tpt-inheritance-mapping"></a>Tabelle-pro-Typ-Vererbungszuordnung (TPT)

Leitende Entwickler: @AndriySvyryd und @smitpatel

Nachverfolgbar über [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)

T-Shirt-Größe: XL

Status: Vorgehensweise

Wir arbeiten an der TPT-Unterstützung, weil dieses Feature sehr stark nachgefragt wurde (ca. 289 Stimmen, 3. Platz insgesamt) und dafür nur wenige spezifische Änderungen erforderlich sind, die den allgemeinen Grundlagen des .NET 5-Plans entsprechen. Datenbankanbieter werden deshalb höchstwahrscheinlich Breaking Changes bemerken. Diese erfordern jedoch viel weniger Arbeit als bei der Umstellung auf Version 3.0.

## <a name="filtered-include"></a>Gefilterte Include-Funktion

Leitender Entwickler: @maumar

Nachverfolgbar über [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)

T-Shirt-Größe: M

Status: Vorgehensweise

Gefilterte Include-Abfragen wurden sehr oft gewünscht (ca. 376 Stimmen, 2. Platz insgesamt). Sie bedürfen nicht viel Arbeit und ermöglichen bzw. vereinfachen viele Szenarios, für die bisher Filter auf Modellebene oder komplexere Abfragen nötig sind.

## <a name="split-include"></a>Aufgeteilte Include-Abfragen

Leitender Entwickler: @smitpatel

Nachverfolgbar über [Issue 20892](https://github.com/dotnet/efcore/issues/20892)

T-Shirt-Größe: L

Status: Vorgehensweise

In EF Core 3.0 wurde das Standardverhalten für das Erstellen einer einzelnen SQL-Abfrage für eine bestimmte LINQ-Abfrage geändert.
Dadurch kam es zu enormen Leistungsregressionen für Abfragen, die Include für mehrere Sammlungen verwenden.

In EF Core 5.0 wird das neue Standardverhalten beibehalten.
EF Core 5.0 lässt jedoch jetzt die Generierung mehrerer Include-Abfragen für Sammlungen zu, bei denen eine einzelne Abfrage zu Leistungsproblemen führen würde.

## <a name="required-one-to-one-dependents"></a>Erforderliche Elemente mit 1:1-Abhängigkeit

Leitende Entwickler: @AndriySvyryd und @smitpatel

Nachverfolgbar über [Issue 12100](https://github.com/dotnet/efcore/issues/12100)

T-Shirt-Größe: M

Status: Vorgehensweise

In EF Core 3.0 sind alle abhängigen Elemente optional, einschließlich nicht eigenständiger Typen (Person.Address kann beispielsweise NULL sein). In EF Core 5.0 können abhängige Elemente als „erforderlich“ konfiguriert werden.

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a>Rationalisieren von „ToTable“, „ToQuery“, „ToView“, „FromSQL“, usw.

Leitende Entwickler: @AndriySvyryd und @smitpatel

Nachverfolgbar über [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)

T-Shirt-Größe: L

Status: Vorgehensweise

Bereits in früheren Versionen wurden Schritte zur Unterstützung von unformatierten SQL-Abfragen, schlüssellosen Typen sowie damit zusammenhängenden Dingen unternommen. Beim Zusammenspiel des Ganzen bestehen jedoch noch Inkonsistenzen und Verbesserungsbedarf. Ziel in Version 5.0 ist es, daran zu arbeiten und den Benutzern das Definieren, Migrieren und Verwenden verschiedener Entitätstypen sowie der dazugehörigen Abfragen und Datenbankartefakte zu erleichtern. Dafür sind ggf. auch Aktualisierungen der kompilierten Abfrage-API erforderlich.

Dadurch kann es jedoch zu Breaking Changes auf Anwendungsebene kommen, da manche der aktuell verfügbaren Funktionen den Benutzern zu viel ermöglichen und dadurch Fehler provoziert werden. Deshalb werden später wahrscheinliche einige dieser Funktionen blockiert sein. Sie erhalten dann stattdessen eine Anleitung mit einer alternativen Lösung.

## <a name="general-query-enhancements"></a>Allgemeine Abfrageverbesserungen

Leitende Entwickler: @smitpatel und @maumar

Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `area-query`](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)

T-Shirt-Größe: XL

Status: Vorgehensweise

Für EF Core 3.0 wurde der Code für die Abfrageübersetzung umfassend umgeschrieben. Deshalb ist er allgemein viel robuster. Bei Version 5.0 sind neben den für die TPT-Unterstützung und das Überspringen von Navigationseigenschaften erforderlichen Änderungen keine großen Anpassungen an der Abfrageübersetzung vorgesehen. Es bestehen jedoch noch einige technische Altlasten aus dem Wechsel zu Version 3.0, für die noch viel Arbeit erforderlich ist. Zusätzlich werden viele Fehler behoben und kleine Verbesserungen eingeführt, wodurch die Benutzer Abfragen noch besser ausführen können.

## <a name="migrations-and-deployment-experience"></a>Migration und Bereitstellung

Leitender Entwickler: @bricelam

Nachverfolgbar über [#19587](https://github.com/dotnet/efcore/issues/19587)

T-Shirt-Größe: L

Status: Zugeordnet/Erledigt

Eingrenzung: Das [Migrationsbundlefeature](https://github.com/dotnet/efcore/issues/19693) wurde bis auf einen Zeitpunkt nach dem EF Core 5.0-Release verschoben. Einige andere [Verbesserungen für Migrationen](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460) sind jedoch im EF Core 5.0-Release enthalten.

Derzeit migrieren viele Entwickler ihre Datenbanken beim Start der Anwendung. Das ist einfach, empfiehlt sich jedoch aus folgenden Gründen nicht:

* Mehrere Threads/Prozesse/Server versuchen möglicherweise gleichzeitig, die Datenbank zu migrieren.
* Anwendungen versuchen möglicherweise, den Zustand „inkonsistent“ zu erreichen, während dies geschieht.
* Normalerweise sollte die Berechtigung, das Schema zu modifizieren, nicht bei Ausführung der Anwendung erteilt werden.
* Wenn etwas schiefgeht, ist es schwer, wieder einen fehlerfreien Zustand zu erreichen.

Wir möchten dafür sorgen, dass es einfach ist, die Datenbank zum Zeitpunkt der Bereitstellung zu migrieren. Dafür haben wir uns folgende Ziele gesetzt:

* Gemeinsame Lösung für Linux, macOS und Windows
* Unkompliziert über die Befehlszeile ausführbar
* Unterstützung von Szenarios mit Containern
* Kombinierbar mit häufig verwendeten Bereitstellungstools-/-flows
* Integration in Visual Studio (mindestens)

Daraus werden sich wahrscheinlich viele kleine Verbesserungen bei EF Core ergeben, z. B. bessere Migrationen bei SQLite. Kombiniert mit Leitfäden und einer langfristigen Zusammenarbeit mit anderen Teams entstehen ganzheitliche Verbesserungen, nicht nur bei EF.

## <a name="ef-core-platforms-experience"></a>EF Core-Plattformen

Leitende Entwickler: @roji und @bricelam

Nachverfolgbar über [#19588](https://github.com/dotnet/efcore/issues/19588)

T-Shirt-Größe: L

Status: Zugeordnet/Erledigt

Eingrenzung: Ein Plattformleitfaden und Beispiele werden für Blazor, Xamarin, WinForms und WPF veröffentlicht. Xamarin- sowie weitere AOT-/Linker-Verbesserungen sind für EF Core 6.0 geplant.

Wir haben gute Leitfäden für die Verwendung von EF Core in traditionellen, MVC ähnelnden Webanwendungen. Die Leitfäden für andere Plattformen und Anwendungsmodelle sind entweder veraltet oder fehlen ganz. Für EF Core 5.0 werden wir untersuchen, wie gut sich EF-Core mit den folgenden Dingen kombinieren lässt. Die Ergebnisse werden dokumentiert, und wir versuchen, weitere Verbesserungen zu erreichen.

* Blazor
* Xamarin, einschließlich der Verwendung von AOT/Linker-Story
* WinForms/WPF/WinUI und möglicherweise andere UI frameworks

Daraus werden sich wahrscheinlich viele kleine Verbesserungen bei EF Core ergeben. Kombiniert mit Leitfäden und einer langfristigen Zusammenarbeit mit anderen Teams entstehen ganzheitliche Verbesserungen, nicht nur bei EF.

Bestimmte Bereiche, die wir uns ansehen möchten, sind:

* Bereitstellung, einschließlich der Verwendung von EF-Tools, z. B. für Migrationen
* Anwendungsmodelle, einschließlich Xamarin, Blazer und gegebenenfalls weitere
* SQLite-Funktionen, einschließlich räumlicher Daten und Neuerstellung von Tabellen
* AOT und Verknüpfungen
* Integration von Diagnosefunktionen, einschließlich Leistungsindikatoren

## <a name="performance"></a>Leistung

Leitender Entwickler: @roji

Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `area-perf`](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)

T-Shirt-Größe: L

Status: Zugeordnet/Erledigt

Eingrenzung: Wichtige Leistungsverbesserungen beim Npgsql-Anbieter wurden abgeschlossen. Weitere Leistungsverbesserungen sind für EF Core 6.0 geplant.

In EF Core möchten wir die vorhandenen Leistungsbenchmarks verbessern und gezielt Leistungsverbesserungen an der Runtime vornehmen. Außerdem ist geplant, die Arbeit an der neuen ADO.NET-API für Batchverarbeitung abzuschließen, die während des Veröffentlichungszyklus der Version 3.0 als Prototyp vorgestellt worden war. Zusätzlich möchten wir auf der ADO.NET-Ebene zusätzliche Leistungsverbesserungen bei Npgsql vornehmen.

Des Weiteren möchten wir bei Bedarf ADO.NET/EF Core-Leistungsindikatoren und andere Diagnosefunktionen hinzufügen.

## <a name="architecturalcontributor-documentation"></a>Dokumentation zu Architektur und Mitwirkenden

Für die Dokumentation verantwortliche Person: @ajcvickers

Nachverfolgbar über [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)

T-Shirt-Größe: L

Status: Ausschneiden

Ziel ist es, verständlicher zu machen, was in EF Core passiert. Dies kann für alle Personen nützlich sein, die EF Core verwenden. Das Hauptziel ist jedoch, externen Personen Folgendes zu erleichtern:

* Mitarbeit am EFF Core-Code
* Erstellen von Datenbankanbietern
* Erstellen anderer Erweiterungen

Update: Leider war dieser Plan zu ambitioniert.
Wir halten das Feature weiterhin für wichtig, dennoch kann es nicht in EF Core 5.0 aufgenommen werden.

## <a name="microsoftdatasqlite-documentation"></a>Dokumentation zu Microsoft.Data.Sqlite

Für die Dokumentation verantwortliche Person: @bricelam

Nachverfolgbar über [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)

T-Shirt-Größe: M

Status: Abgeschlossen. Die neue Dokumentation finden Sie [hier](/dotnet/standard/data/sqlite/?tabs=netcore-cli).

Das EF-Team ist auch Eigentümer des Microsoft.Data.Sqlite ADO.NET-Anbieters. Wir planen, als Teil der Veröffentlichungen für EF Core 5.0, eine vollständige Dokumentation zu diesem Anbieter bereitzustellen.

## <a name="general-documentation"></a>Allgemeine Dokumentation

Für die Dokumentation verantwortliche Person: @ajcvickers

Nachverfolgbar über [Docs-Repository im 5.0-Meilenstein](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)

T-Shirt-Größe: L

Status: In Bearbeitung

Die Aktualisierung der bestehenden Dokumentation für die Releases 3.0 und 3.1 läuft bereits. Zusätzlich arbeiten wir an Folgendem:

* Überarbeitung der Einsteigerdokumentation, um sie ansprechender und leichter verständlich zu machen
* Neuanordnung von Dokumenten, um sie leichter auffindbar zu machen und um Querverweise hinzufügen zu können
* Hinzufügen von ausführlicheren Details und Erläuterungen zu vorhandenen Dokumenten
* Aktualisieren bestehender Beispiele und Hinzufügen zusätzlicher Beispiele

## <a name="fixing-bugs"></a>Beheben von Fehlern

Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `type-bug`](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)

Entwickler: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd @ajcvickers

T-Shirt-Größe: L

Status: In Bearbeitung

Zum Zeitpunkt der Entstehung dieses Artikels haben wir 135 Fehler kategorisiert, die in Version 5.0 behoben werden sollen. Davon sind 62 bereits behoben. Hier besteht jedoch eine starke Überschneidung mit den weiter oben beschriebenen _allgemeinen Abfrageverbesserungen_.

Im Laufe des Releases 3.0 wurden pro Monat etwa 23 Probleme in Meilensteine aufgenommen. Davon müssen jedoch nicht alle in Version 5.0 behoben werden. Grob geschätzt sollen im Zeitraum von Version 5.0 zusätzlich 150 Probleme behoben werden.

## <a name="small-enhancements"></a>Kleinere Verbesserungen

Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `type-enhancement`](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)

Entwickler: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd @ajcvickers

T-Shirt-Größe: L

Status: Vorgehensweise

Zusätzlich zu den oben beschriebenen größeren Funktionen sind in Version 5.0 auch viele kleinere Verbesserungen geplant, mit denen Probleme behoben werden, die als unkritisch gelten. Viele dieser Verbesserungen sind auch durch die oben beschriebenen, allgemeineren Themen abgedeckt.

## <a name="below-the-line"></a>Verschoben auf nächste Version

Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)

Dabei handelt es sich um Fehlerbehebungen und Verbesserungen, die aktuell **nicht** für das Release 5.0 vorgesehen sind, aber je nach Fortschritt bei den oben beschriebenen Zielen zusätzlich angegangen werden.

Zusätzlich werden bei der Planung auch immer die [Probleme mit den meisten Stimmen](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) berücksichtigt. Bekannte Probleme erst in einem späteren Release zu beheben ist nicht schön, aber nötig, um die Auslastung der vorhandenen Ressourcen auf ein realistisches Maß zu beschränken.

## <a name="suggestions"></a>Vorschläge

Ihr Feedback zur Planung ist wichtig. Sie können für ein Problem auf GitHub abstimmen (Daumen hoch) und so angeben, dass dieses Problem wichtig ist. Diese Daten werden dann in den [Planungsprozess](xref:core/what-is-new/release-planning) für das nächste Release aufgenommen.
