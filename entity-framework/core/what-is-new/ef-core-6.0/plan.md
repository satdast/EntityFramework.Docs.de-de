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
# <a name="plan-for-entity-framework-core-60"></a>Plan für Entity Framework Core 6.0

Wie im [Artikel zum Planungsprozess](xref:core/what-is-new/release-planning) beschrieben, wurde aus dem Feedback der Beteiligten dieser Plan für das Release von Entity Framework Core 6.0 (EF Core) erstellt.

Im Gegensatz zu früheren Releases versucht dieser Plan nicht, die gesamte Arbeit für das 6.0-Release abzudecken. Stattdessen gibt er an, an welchen Stellen und auf welche Weise in dieses Release investiert werden soll. Dabei wird auf ausreichend Flexibilität geachtet, um den Anwendungsbereich anzupassen oder neue Arbeit hinzuzufügen, während Feedback und neue Erkenntnisse bei der Arbeit am Release gesammelt werden.

> [!IMPORTANT]
> Dieser Plan ist nicht bindend. Er dient als Ausgangspunkt und wird auf Grundlage von neuen Erkenntnissen weiterentwickelt. Es kann also sein, dass derzeit nicht geplante Funktionen in Release 6.0 aufgenommen werden. Im Gegenzug können aktuell geplante Funktionen auch aus Release 6.0 gestrichen werden.

## <a name="general-information"></a>Allgemeine Informationen

### <a name="version-number-and-release-date"></a>Versionsnummer und Veröffentlichungsdatum

EF Core 6.0 ist das nächste Release nach EF Core 5.0. Die Veröffentlichung ist derzeit für den November 2021, also für den gleichen Termin wie die von .NET 6 geplant.

### <a name="supported-platforms"></a>Unterstützte Plattformen

EF Core 6.0 ist derzeit auf .NET 5 ausgerichtet. Wahrscheinlich erfolgt eine Neuausrichtung auf .NET 6, je näher das Release rückt. EF Core 6.0 ist auf keine Version von .NET Standard ausgerichtet. Weitere Informationen finden Sie unter [Die Zukunft von .NET Standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).

EF Core 6.0 kann nicht im .NET Framework ausgeführt werden.

EF Core 6.0 ist als [Long-Term-Support-Release (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) an .NET 6 ausgerichtet.

### <a name="breaking-changes"></a>Aktuelle Änderungen

EF Core 6.0 enthält einige [Breaking Changes](xref:core/what-is-new/ef-core-6.0/breaking-changes), da sowohl EF Core als auch die .NET-Plattform weiterentwickelt werden. Unser Ziel ist es, die Mehrzahl der Anwendungen zu aktualisieren, ohne dass Breaking Changes auftreten.

## <a name="themes"></a>Designs

Die folgenden Bereiche bilden die Grundlage für die umfangreichen Investitionen in EF Core 6.0.

## <a name="highly-requested-features"></a>Features mit starker Nachfrage

Eine der wichtigsten Feedbackquellen für den [Planungsprozess](xref:core/what-is-new/release-planning) sind wie immer die [Abstimmungen (👍) über Features auf GitHub](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc). Für EF Core 6.0 sind die folgenden stark nachgefragten Features geplant:

### <a name="sql-server-temporal-tables"></a>Temporäre SQL Server-Tabellen

Nachverfolgbar über [Issue 4693](https://github.com/dotnet/efcore/issues/4693)

Status: Nicht begonnen

T-Shirt-Größe: Large

Temporäre Tabellen unterstützen Abfragen für Daten, die zu einem _beliebigen Zeitpunkt_ in der Tabelle gespeichert wurden, anstelle der zuletzt gespeicherten Daten wie bei normalen Tabellen. EF Core 6.0 ermöglicht das Erstellen temporärer Tabellen über Migrationen sowie das Zugreifen auf die Daten über LINQ-Abfragen.

Der Arbeitsumfang wurde zu Beginn [in diesem Issue](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974) beschrieben. Unter Umständen wird während der Veröffentlichung auf Grundlage von Feedback zusätzliche Unterstützung hinzugefügt.

### <a name="json-columns"></a>JSON-Spalten

Nachverfolgbar über [Issue 4021](https://github.com/dotnet/efcore/issues/4021)

Status: Nicht begonnen

T-Shirt-Größe: Mittel

Dieses Feature führt einen gängigen Mechanismus sowie Muster für die JSON-Unterstützung ein, die von jedem Datenbankanbieter implementiert werden können. Wir arbeiten mit der Community zusammen, um vorhandene Implementierungen von [Npgsql](https://github.com/npgsql/efcore.pg) und [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) abzugleichen und zudem Unterstützung für SQL Server und SQLite hinzuzufügen.

### <a name="columnattributeorder"></a>ColumnAttribute.Order

Nachverfolgbar über [Issue 10059](https://github.com/dotnet/efcore/issues/10059)

Status: Nicht begonnen

T-Shirt-Größe: Small

Dieses Feature ermöglicht eine beliebige Reihenfolge von Spalten beim **Erstellen einer Tabelle** mit Migrationen oder `EnsureCreated`. Beachten Sie, dass eine vorhandenen Tabelle neu erstellt werden muss, wenn Sie die Spaltenreihenfolge darin ändern möchten, und dass Unterstützung für dieses Vorgehen bisher in keinem EF Core-Release vorgesehen ist.

## <a name="performance"></a>Leistung

Obwohl EF Core in der Regel schneller als EF6 ist, gibt es immer noch Bereiche, in denen bedeutende Leistungsverbesserungen möglich sind. Es ist geplant, mehrere dieser Bereiche in EF Core 6.0 anzugehen und gleichzeitig die Leistungsinfrastruktur und -tests zu verbessern.

Dieses Design wird viele iterative Untersuchungen umfassen, die beeinflussen, worauf Ressourcen konzentriert werden. Geplant ist, mit folgenden Features zu beginnen:

### <a name="performance-infrastructure-and-new-tests"></a>Leistungsinfrastruktur und neue Tests

Status: Nicht begonnen

T-Shirt-Größe: Mittel

Die Codebasis von EF Core enthält bereits einige Leistungsbenchmarks, die täglich ausgeführt werden. Für EF Core 6.0 ist geplant, sowohl die Infrastruktur für diese Tests zu verbessern als auch neue Tests hinzuzufügen. Außerdem wird ein Profil für die grundlegenden Leistungsszenarios erstellt, und alle mit geringem Aufwand zu korrigierenden Probleme werden behoben.

### <a name="compiled-models"></a>Kompilierte Modelle

Nachverfolgbar über [Issue 1906](https://github.com/dotnet/efcore/issues/1906)

Status: Nicht begonnen

T-Shirt-Größe: X-Large

Mit kompilierten Modellen kann eine kompilierte Version des EF-Modells generiert werden. Dadurch wird sowohl die Startleistung als auch eine allgemein bessere Leistung beim Zugriff auf das Modell erzielt.

### <a name="techempower-fortunes"></a>TechEmpower – Fortunes

Nachverfolgbar über [Issue 23611](https://github.com/dotnet/efcore/issues/23611)

Status: Nicht begonnen

T-Shirt-Größe: X-Large

Seit mehreren Jahren werden die [TechEmpower-Benchmarks](https://www.techempower.com/benchmarks/), die den Branchenstandard darstellen, unter .NET für eine PostgreSQL-Datenbank ausgeführt. Der [Fortunes-Benchmark](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune) ist besonders relevant für EF-Szenarios. Es gibt mehrere Variationen dieses Benchmarks, einschließlich:

- Einer Implementierung, die ADO.NET direkt verwendet. Dies ist die schnellste Implementierung der drei hier aufgelisteten.
- Einer Implementierung, die [Dapper](https://www.nuget.org/packages/Dapper/) verwendet. Dieses Vorgehen ist langsamer als die direkte Verwendung von ADO.NET, aber immer noch schnell.
- Einer Implementierung, die EF Core verwendet. Dieses Vorgehen stellt derzeit die langsamste Implementierung der drei Optionen dar.

Das Ziel von EF Core 6.0 besteht darin, die Leistung von EF Core an die von Dapper im TechEmpower-Fortunes-Benchmark anzugleichen. (Dies ist eine große Herausforderung, aber Microsoft wird alles daran setzen, die bestmögliche Lösung zu entwickeln.)

### <a name="linkeraot"></a>Linker/AOT

Nachverfolgbar über [Issue 10963](https://github.com/dotnet/efcore/issues/10963)

Status: Nicht begonnen

T-Shirt-Größe: Mittel

EF Core erzeugt zur Laufzeit große Mengen von Code. Dies ist eine Herausforderung für App-Modelle wie Xamarin und Blazor, die auf Tree Shaking mithilfe des Linkers angewiesen sind, sowie für Plattformen, die keine dynamische Kompilierung zulassen (z. B. iOS). Dieser Bereich wird im Rahmen von EF Core 6.0 weiterhin untersucht, und es werden, wenn möglich, strategische Verbesserungen vorgenommen. Es ist jedoch nicht zu erwarten, dass die Lücke im Zeitrahmen des 6.0-Releases vollständig geschlossen wird.

## <a name="migrations-and-deployment"></a>Migrationen und Bereitstellung

Aufbauend auf den [Untersuchungen, die für EF Core 5.0 durchgeführt wurden](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience), ist geplant, die verbesserte Unterstützung für die Verwaltung von Migrationen und das Bereitstellen von Datenbanken einzuführen. Diese Verbesserung betrifft zwei Hauptbereiche:

### <a name="migrations-bundles"></a>Migrationsbundles

Nachverfolgbar über [Issue 19693](https://github.com/dotnet/efcore/issues/19693)

Status: Nicht begonnen

T-Shirt-Größe: Mittel

Ein Migrationsbundle ist eine eigenständiges ausführbares Paket, das Migrationen auf eine Produktionsdatenbank anwendet. Das Verhalten entspricht `dotnet ef database update`, sollte die Bereitstellung von SSH/Docker/PowerShell jedoch erheblich vereinfachen, da alle erforderlichen Ressourcen in dem einzelnen ausführbaren Paket enthalten sind.

### <a name="managing-migrations"></a>Verwalten von Migrationen

Nachverfolgbar über [Issue 22945](https://github.com/dotnet/efcore/issues/22945)

Status: Nicht begonnen

T-Shirt-Größe: Large

Die Anzahl der für eine Anwendung erstellten Migrationen kann zu einer Belastung werden. Außerdem werden diese Migrationen häufig mit der Anwendung bereitgestellt, selbst wenn dies nicht erforderlich ist. In EF Core 6.0 ist geplant, dies durch bessere Tools und eine verbesserte Projekt-/Assemblyverwaltung zu optimieren. Zwei konkrete Issues, die angegangen werden sollen, sind das zum [Hineinzwängen vieler Migrationen in eine einzige](https://github.com/dotnet/efcore/issues/2174) sowie zum [erneuten Generieren einer sauberen Modellmomentaufnahme](https://github.com/dotnet/efcore/issues/18557).

## <a name="improve-existing-features-and-fix-bugs"></a>Verbessern bestehender Features und Beheben von Fehlern

Alle [Fehler, die dem 6.0.0-Meilenstein zugeordnet sind](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0), sind zurzeit für dieses Release eingeplant. Dazu zählen viele kleine Erweiterungen und Fehlerfixes.

### <a name="ef6-query-parity"></a>EF6-Abfrageparität

Nachverfolgbar über [Issues, die mit „ef6-parity“ gekennzeichnet und im 6.0-Meilenstein enthalten sind](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)

Status: Nicht begonnen

T-Shirt-Größe: Large

EF Core 5.0 unterstützt neben Mustern, die in EF6 nicht unterstützt werden, auch die meisten von EF6 unterstützten Abfragemuster. In EF Core 6.0 ist geplant, diese Lücke zu schließen, sodass unterstützte EF6-Abfragen u. a. in den unterstützten EF Core-Abfragen inbegriffen sind. Diese Verbesserung basiert auf der Untersuchung der Lücken und umfasst bereits GroupBy-Issues wie das zum [Übersetzen von GroupBy gefolgt von FirstOrDefault](https://github.com/dotnet/efcore/issues/12088) und zur Unterstützung unformatierter SQL-Abfragen für [primitive](https://github.com/dotnet/efcore/issues/11624) und [nicht zugeordnete](https://github.com/dotnet/efcore/issues/10753) Typen.  

### <a name="value-objects"></a>Wertobjekte

Nachverfolgbar über [Issue 9906](https://github.com/dotnet/efcore/issues/9906)

Status: Nicht begonnen

T-Shirt-Größe: Mittel

Früher war das Entwicklungsteam der Ansicht, dass nicht eigenständige, für die [Aggregatunterstützung](https://www.martinfowler.com/bliki/DDD_Aggregate.html) vorgesehene Entitäten auch eine angemessene Näherung an [Wertobjekte](https://www.martinfowler.com/bliki/ValueObject.html) darstellen. Die Erfahrung hat jedoch gezeigt, dass dies nicht der Fall ist. Daher ist für EF Core 6.0 geplant, eine bessere Leistung zu erzielen, die sich im domänengesteuerten Design auf die Anforderungen von Wertobjekten konzentriert. Dieser Ansatz wird auf Wertkonvertern anstatt auf nicht eigenständigen Entitäten basieren.

Bisher ist vorgesehen, dass [Wertkonverter zugelassen werden sollen, die mehreren Spalten zugeordnet sind](https://github.com/dotnet/efcore/issues/13947). Unter Umständen wird während der Veröffentlichung auf Grundlage von Feedback zusätzliche Unterstützung hinzugefügt.

### <a name="cosmos-database-provider"></a>Cosmos-Datenbankanbieter

Nachverfolgbar über [Issues, die mit „area-cosmos“ gekennzeichnet und im 6.0-Meilenstein enthalten sind](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)

Status: Nicht begonnen

T-Shirt-Größe: Large

Es wird aktiv Feedback zu den gewünschten Verbesserungen am Cosmos-Anbieter in EF Core 6.0 gesammelt. Dieses Dokument wird den eingehenden Rückmeldungen entsprechend aktualisiert. Vorerst sollten Sie unbedingt für die Cosmos-Features abstimmen (👍), die Sie benötigen.

### <a name="expose-model-building-conventions-to-applications"></a>Verfügbarmachen von Konventionen zur Modellerstellung für Anwendungen

Nachverfolgbar über [Issue 214](https://github.com/dotnet/efcore/issues/214)

Status: Nicht begonnen

T-Shirt-Größe: Medium

EF Core verwendet verschiedene Konventionen zum Erstellen von Modellen aus .NET-Typen. Diese Konventionen werden zurzeit vom Datenbankanbieter vorgegeben. In EF Core 6.0 sollen Anwendungen in der Lage sein, diese Konventionen zu ändern.

### <a name="zero-bug-balance-zbb"></a>Null-Fehler-Bilanz

Nachverfolgbar über [Issues, die im 6.0-Meilenstein mit `type-bug` gekennzeichnet sind](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)

Status: In Bearbeitung

T-Shirt-Größe: Large

Es ist geplant, alle ausstehenden Fehlerbehebungen im Zeitrahmen von EF Core 6.0 zu erledigen. Folgendes sollte beachtet werden:

- Dies gilt insbesondere für Issues, die mit dem Tag [type-bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug) gekennzeichnet sind.
- Es werden Ausnahmen gemacht, z. B. wenn für eine ordnungsgemäße Fehlerbehebung eine Entwurfsänderung oder ein neues Feature erforderlich ist. Diese Issues werden mit der Bezeichnung `blocked` gekennzeichnet.
- Je näher das GA-/RTM-Release rückt, werden Fehler wie üblich bei Bedarf auf Grundlage des Risikos priorisiert, das sie darstellen.

### <a name="miscellaneous-features"></a>Verschiedene Features

Nachverfolgbar über [Issues, die im 6.0-Meilenstein mit `type-enhancement` gekennzeichnet sind](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)

Status: In Bearbeitung

T-Shirt-Größe: Large

Zu den verschiedenen für EF 6.0 geplanten Features gehören u. a.:

- [Aufgeteilte Abfragen für andere Sammlungen als Navigationssammlungen](https://github.com/dotnet/efcore/issues/21234)
- [Erkennen einfacher Jointabellen beim Reverse Engineering und Erstellen von m:n-Beziehungen](https://github.com/dotnet/efcore/issues/22475)
- [Vervollständigen der Volltext-/Freitextsuche in SQLite und SQL Server](https://github.com/dotnet/efcore/issues/4823)
- [Räumliche Indizes in SQL Server](https://github.com/dotnet/efcore/issues/12538)
- [Mechanismus/API zum Angeben einer Standardkonvertierung für eine beliebige Eigenschaft eines bestimmten Typs im Modell](https://github.com/dotnet/efcore/issues/10784)
- [Verwenden der neuen Batchverarbeitungs-API von ADO.NET](https://github.com/dotnet/efcore/issues/18990)

## <a name="net-integration"></a>.NET-Integration

Das EF Core-Team arbeitet auch an mehreren verwandten, aber voneinander unabhängigen Technologien. Insbesondere ist die Arbeit an Folgendem geplant:

### <a name="enhancements-to-systemdata"></a>Verbesserungen an System.Data

Nachverfolgbar über [Issues im Repository dotnet\runtime, die im 6.0-Meilenstein mit `area-System.Data` gekennzeichnet sind](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)

Status: Nicht begonnen

T-Shirt-Größe: Large

Diese Arbeit umfasst Folgendes:

- Implementierung der neuen [Batchverarbeitungs-API](https://github.com/dotnet/runtime/issues/28633)
- Kontinuierliche Zusammenarbeit mit anderen .NET-Teams und der Community, um ADO.NET besser zu verstehen und weiterzuentwickeln
- [Standardisieren von DiagnosticSource für die Ablaufverfolgung in Komponenten von System.Data.*](https://github.com/dotnet/runtime/issues/22336)

### <a name="enhancements-to-microsoftdatasqlite"></a>Verbesserungen an Microsoft.Data.Sqlite

Nachverfolgbar über [Issues, die im 6.0-Meilenstein mit `type-enhancement` und `area-adonet-sqlite` gekennzeichnet sind](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)

Status: In Bearbeitung

T-Shirt-Größe: Mittel

Es sind einige kleine Verbesserungen für Microsoft.Data.Sqlite geplant, einschließlich [Verbindungspooling](https://github.com/dotnet/efcore/issues/13837) und [vorbereiteten Anweisungen](https://github.com/dotnet/efcore/issues/14044) für eine bessere Leistung.

### <a name="nullable-reference-types"></a>Nullwerte zulassende Verweistypen

Nachverfolgbar über [Issue 14150](https://github.com/dotnet/efcore/issues/14150)

Status: In Bearbeitung

T-Shirt-Größe: Large

Der EF Core-Code wird mit Anmerkungen versehen, damit [Nullwerte zulassende Verweistypen verwendet werden](/dotnet/csharp/nullable-references).

## <a name="experiments-and-investigations"></a>Experimente und Untersuchungen

Das EF-Team plant, während des Zeitrahmens von EF Core 6.0 in zwei Bereichen Zeit in Untersuchungen und Experimente zu investieren. Dabei handelt es sich um einen Lernprozess. Aus diesem Grund wurden keine konkreten Pflichtergebnisse für Release 6.0 verabredet.

### <a name="sqlservercore"></a>SqlServer.Core

Nachverfolgbar im [Repository „.NET Data Lab“](https://github.com/dotnet/datalab/)

Status: Nicht begonnen

T-Shirt-Größe: Laufend

[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) ist ein ADO.NET-Datenbankanbieter mit vollem Featureumfang für SQL Server. Er unterstützt eine breite Palette von SQL Server-Features, sowohl in .NET Core als auch im .NET Framework. Microsoft.Data.SqlClient ist jedoch auch eine große und alte Codebasis mit vielen komplexen Interaktionen zwischen ihren verschiedenen Verhaltensweisen. Dies erschwert die Ermittlung der möglichen Vorteile, die durch neuere .NET Core-Features erzielt werden könnten. Aus diesem Grund wird ein Experiment in Zusammenarbeit mit der Community eingeleitet, das das Potenzial eines SQL Server-Hochleistungstreibers für .NET bestimmen soll.

> [!IMPORTANT]
> Dies hat keinen Einfluss auf das Engagement für Microsoft.Data.SqlClient. Microsoft.Data.SqlClient bleibt weiterhin die empfohlene Methode zum Herstellen einer Verbindung mit SQL Server und SQL Azure, sowohl mit als auch ohne EF Core. Außerdem ist für neue SQL Server-Features weiterhin ab dem Zeitpunkt ihrer Einführung Unterstützung verfügbar.

### <a name="graphql"></a>GraphQL

Status: Nicht begonnen

T-Shirt-Größe: Laufend

[GraphQL](https://graphql.org/) hat in den letzten Jahren auf einer Vielzahl von Plattformen an Zugkraft gewonnen. Es ist geplant, die Möglichkeiten von GraphQL weiter auszuloten und Wege zu finden, die Nutzung mit .NET zu verbessern. Zu diesem Zweck erfolgt eine Zusammenarbeit mit der Community, um das bestehende Ökosystem besser zu verstehen und unterstützen zu können. Unter Umständen sind auch bestimmte Investitionen von Microsoft erforderlich, entweder in Form von Beiträgen zu vorhandenen Arbeiten oder bei der Entwicklung von Ergänzungen des Microsoft-Stapels.

### <a name="dataverse-formerly-common-data-services"></a>DataVerse (ehemals Common Data Services)

Status: Nicht begonnen

T-Shirt-Größe: Laufend

[DataVerse](/powerapps/maker/data-platform/data-platform-intro) ist ein spaltenbasierter Datenspeicher, der für die schnelle Entwicklung von Geschäftsanwendungen konzipiert wurde. DataVerse verarbeitet automatisch komplexe Datentypen wie binäre Objekte (Binary Large Objects, Blobs) und verfügt über integrierte Entitäten und Beziehungen wie Organisationen und Kontakte. Es ist zwar ein SDK vorhanden, für Entwickler kann ein EF Core-Anbieter dennoch von Vorteil sein, um erweiterte LINQ-Abfragen zu verwenden, Arbeitseinheiten zu nutzen und über eine konsistente Datenzugriffs-API zu verfügen. Das Team wird verschiedene Möglichkeiten in Betracht ziehen, um die Benutzerfreundlichkeit der Verbindung mit DataVerse für .NET-Entwickler zu verbessern.

## <a name="below-the-cut-line"></a>Nicht berücksichtigte Fehler

Nachverfolgbar über [Liste der Probleme im 5.0-Meilenstein mit der Bezeichnung `consider-for-current-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)

Dabei handelt es sich um Fehlerbehebungen und Verbesserungen, die derzeit **nicht** in Release 6.0 eingeplant wurden, jedoch je nach Feedback während des Releases und dem Fortschritt der oben beschriebenen Arbeiten unter Umständen in Zukunft angegangen werden. Diese Issues können in EF Core 6.0 integriert und automatisch zu Kandidaten für das nächste Release werden.

Zusätzlich werden bei der Planung auch immer die [Probleme mit den meisten Stimmen](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) berücksichtigt. Bekannte Probleme erst in einem späteren Release zu beheben ist nicht schön, aber nötig, um die Auslastung der vorhandenen Ressourcen auf ein realistisches Maß zu beschränken. Vergewissern Sie sich, dass Sie für die Features abgestimmt haben (👍), die Sie benötigen.

## <a name="suggestions"></a>Vorschläge

Ihr Feedback zur Planung ist wichtig. Sie können für ein Issue auf GitHub abstimmen (👍) und so angeben, dass es wichtig ist. Diese Daten werden dann in den [Planungsprozess](xref:core/what-is-new/release-planning) für das nächste Release aufgenommen.
