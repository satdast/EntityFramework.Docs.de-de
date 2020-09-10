---
title: Vergleichen von EF6 und EF Core
description: Leitfaden zur Wahl zwischen EF6 und EF Core.
author: ajcvickers
ms.date: 01/23/2019
ms.assetid: a6b9cd22-6803-4c6c-a4d4-21147c0a81cb
uid: efcore-and-ef6/index
ms.openlocfilehash: 8e99cd9863d9f206ef3ae4d68226283019db835f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619749"
---
# <a name="compare-ef-core--ef6"></a>Vergleichen von EF Core und EF 6

## <a name="ef-core"></a>EF Core

Entity Framework Core ([EF Core](xref:core/index)) ist ein moderner Objekt-Datenbank-Mapper für .NET. Er unterstützt LINQ-Abfragen, Änderungsnachverfolgung, Updates und Schemamigrationen.

EF Core funktioniert mit SQL Server/SQL Azure, SQLite, Azure Cosmos DB, MySQL, PostgreSQL und vielen weiteren Datenbanken über ein [Datenbankanbieter-Plug-In-Modell](xref:core/providers/index).

## <a name="ef6"></a>EF6

Entity Framework 6 ([EF6](xref:ef6/index)) ist ein objektrelationaler Mapper, der für .NET Framework konzipiert ist, jedoch mit Unterstützung für .NET Core. EF6 ist ein stabiles, unterstütztes Produkt, das jedoch nicht mehr weiterentwickelt wird.

## <a name="feature-comparison"></a>Funktionsvergleiche

EF Core bietet neue Features, die in EF6 nicht implementiert werden. Allerdings sind derzeit nicht alle EF6-Features in EF Core implementiert.

In den folgenden Tabellen werden die in EF Core und EF6 verfügbaren Funktionen verglichen. Das ist ein allgemeiner Vergleich, in dem nicht alle Funktionen aufgelistet und nicht alle Unterschiede zwischen der gleichen Funktion in verschiedenen EF-Versionen erläutert sind.

Die Spalte „EF Core“ zeigt die Produktversion, in der die Funktion zum ersten Mal eingeführt wurde.

### <a name="creating-a-model"></a>Erstellen eines Modells

| **Feature**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Grundlegende Klassenzuordnung                                   | Ja      | 1.0                                   |
| Konstruktoren mit Parametern                          |          | 2.1                                   |
| Konvertierungen von Eigenschaftswerten                            |          | 2.1                                   |
| Zugeordnete Typen ohne Schlüssel                             |          | 2.1                                   |
| Konventionen                                           | Ja      | 1.0                                   |
| Benutzerdefinierte Konventionen                                    | Ja      | 1.0 (partiell; [#214](https://github.com/dotnet/efcore/issues/214)) |
| Datenanmerkungen                                      | Ja      | 1.0                                   |
| Fluent-API                                            | Ja      | 1.0                                   |
| Vererbung: Tabelle pro Hierarchie (TPH)                | Ja      | 1.0                                   |
| Vererbung: Tabelle pro Typ (TPT)                     | Ja      | Geplant für 5.0 ([#2266](https://github.com/dotnet/efcore/issues/2266)) |
| Vererbung: Tabelle pro konkrete Klasse (TPC)           | Ja      | Stretch für 5.0 ([#3170](https://github.com/dotnet/efcore/issues/3170)) <sup>(1)</sup> |
| Eigenschaften im Schattenzustand                               |          | 1.0                                   |
| Alternative Schlüssel                                        |          | 1.0                                   |
| M:n-Navigationen                              | Ja      | Geplant für 5.0 ([#19003](https://github.com/dotnet/efcore/issues/19003)) |
| m:n ohne Joinentität                      | Ja      | Im Rückstand ([#1368](https://github.com/dotnet/efcore/issues/1368)) |
| Schlüsselgenerierung: Datenbank                              | Ja      | 1.0                                   |
| Schlüsselgenerierung: Client                                |          | 1.0                                   |
| Complex-/Owned-Typen                                   | Ja      | 2.0                                   |
| Räumliche Daten                                          | Ja      | 2.2                                   |
| Modellformat: Code                                    | Ja      | 1.0                                   |
| Modell aus Datenbank erstellen: Befehlszeile              | Ja      | 1.0                                   |
| Modell aus Datenbank aktualisieren                            | Partial  | Im Rückstand ([#831](https://github.com/dotnet/efcore/issues/831)) |
| Globale Abfragefilter                                  |          | 2.0                                   |
| Tabellenaufteilung                                       | Ja      | 2.0                                   |
| Entitätsaufteilung                                      | Ja      | Stretch für 5.0 ([#620](https://github.com/dotnet/efcore/issues/620)) <sup>(1)</sup> |
| Zuordnung von Skalarfunktionen für Datenbanken                      | Schlecht     | 2.0                                   |
| Feldzuordnung                                         |          | 1.1                                   |
| Nullwerte zulassende Verweistypen (C# 8.0)                     |          | 3.0                                   |
| Grafische Visualisierung eines Modells                      | Ja      | Keine Unterstützung geplant <sup>(2)</sup>     |
| Grafischer Modell-Editor                                | Ja      | Keine Unterstützung geplant <sup>(2)</sup>     |
| Modellformat: EDMX (XML)                              | Ja      | Keine Unterstützung geplant <sup>(2)</sup>     |
| Modell aus Datenbank erstellen: VS-Assistent                 | Ja      | Keine Unterstützung geplant <sup>(2)</sup>     |

### <a name="querying-data"></a>Abfragen von Daten

| **Feature**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| LINQ-Abfragen                                          | Ja      | 1.0                                   |
| Lesbares generiertes SQL                                | Schlecht     | 1.0                                   |
| GroupBy-Übersetzung                                   | Ja      | 2.1                                   |
| Laden zugehöriger Daten: Eager                           | Ja      | 1.0                                   |
| Laden von verknüpften Daten: Eager Loading für abgeleitete Typen |          | 2.1                                   |
| Laden zugehöriger Daten: Lazy                            | Ja      | 2.1                                   |
| Laden zugehöriger Daten: Explizit                        | Ja      | 1.1                                   |
| RAW-SQL-Abfragen: Entitätstypen                         | Ja      | 1.0                                   |
| Unformatierte SQL-Abfragen: Schlüssellose Entitätstypen                 | Ja      | 2.1                                   |
| Unformatierte SQL-Abfragen: Erstellung mit LINQ                  |          | 1.0                                   |
| Explizit kompilierte Abfragen                           | Schlecht     | 2.0                                   |
| await foreach (C# 8.0)                                |          | 3.0                                   |
| Textbasierte Abfragesprache (Entity SQL)                | Ja      | Keine Unterstützung geplant <sup>(2)</sup>     |

### <a name="saving-data"></a>Speichern von Daten

| **Feature**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Änderungsnachverfolgung: Momentaufnahme                             | Ja      | 1.0                                   |
| Änderungsnachverfolgung: Benachrichtigung                         | Ja      | 1.0                                   |
| Änderungsnachverfolgung: Proxys                              | Ja      | Zusammengeführt für 5.0 ([#10949](https://github.com/dotnet/efcore/issues/10949)) |
| Zugriff auf den erfassten Zustand                               | Ja      | 1.0                                   |
| Optimistische Nebenläufigkeit                                | Ja      | 1.0                                   |
| Transaktionen                                          | Ja      | 1.0                                   |
| Anweisungsbatches                                |          | 1.0                                   |
| Mapping der gespeicherten Prozedur                              | Ja      | Im Rückstand ([#245](https://github.com/dotnet/efcore/issues/245)) |
| Abgekoppelter Graph: Lowlevel-APIs                     | Schlecht     | 1.0                                   |
| Abgekoppelter Graph: End-to-End                         |          | 1.0 (partiell; [#5536](https://github.com/dotnet/efcore/issues/5536)) |

### <a name="other-features"></a>Andere Funktionen

| **Feature**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Migrationen                                            | Ja      | 1.0                                   |
| APIs zum Erstellen/Löschen von Datenbanken                       | Ja      | 1.0                                   |
| Ausgangswerte                                             | Ja      | 2.1                                   |
| Verbindungsstabilität                                 | Ja      | 1.1                                   |
| Interceptors                                          | Ja      | 3.0                                   |
| Events                                                | Ja      | 3.0 (partiell; [#626](https://github.com/dotnet/efcore/issues/626)) |
| Einfache Protokollierung („Database.Log“)                         | Ja      | Zusammengeführt für 5.0 ([#1199](https://github.com/dotnet/efcore/issues/1199)) |
| DbContext-Pooling                                     |          | 2.0                                   |

### <a name="database-providers-sup3sup"></a>Datenbankanbieter <sup>(3)</sup>

| **Feature**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| SQL Server                                            | Ja      | 1.0                                   |
| MySQL                                                 | Ja      | 1.0                                   |
| PostgreSQL                                            | Ja      | 1.0                                   |
| Oracle                                                | Ja      | 1.0                                   |
| SQLite                                                | Ja      | 1.0                                   |
| SQL Server Compact                                    | Ja      | 1.0 <sup>(4)</sup>                    |
| DB2                                                   | Ja      | 1.0                                   |
| Firebird                                              | Ja      | 2.0                                   |
| Jet (Microsoft Access)                                |          | 2.0 <sup>(4)</sup>                    |
| Azure Cosmos DB                                       |          | 3.0                                   |
| In-Memory (für Tests)                               |          | 1.0                                   |

<sup>1</sup> „Stretch Goals“ werden für ein bestimmtes Release wahrscheinlich nicht erreicht. Wenn es jedoch gut geht, werden wir versuchen, sie einzubeziehen.

<sup>2</sup> Einige EF6-Features werden in EF Core nicht implementiert. Diese Features hängen von dem EF6 zugrunde liegenden Entity Data Model (EDM) ab und/oder sind komplexe Features mit relativ geringer Rendite. Wir freuen uns immer über Feedback, aber obwohl EF Core viele Dinge ermöglicht, die in EF6 nicht möglich sind, kann EF Core umgekehrt nicht alle Features von EF6 unterstützen.

<sup>3</sup> Von Drittanbietern implementierte EF Core-Datenbankanbieter führen die Aktualisierung auf neue Hauptversionen von EF Core vielleicht verzögert durch. Weitere Informationen finden Sie unter [Datenbankanbieter](xref:core/providers/index).

<sup>4</sup> Die SQL Server Compact- und Jet-Anbieter funktionieren nur für .NET Framework (nicht für .NET Core).

### <a name="supported-platforms"></a>Unterstützte Plattformen

EF Core 3.1 wird unter .NET Core und .NET Framework durch die Verwendung von .NET Standard 2.0 ausgeführt. EF Core 5.0 kann jedoch nicht auf .NET Framework ausgeführt werden. Weitere Informationen finden Sie unter [Plattformen](xref:core/platforms/index).

EF6.4 wird unter .NET Core und .NET Framework durch die Festlegung von Zielversionen ausgeführt.

## <a name="guidance-for-new-applications"></a>Leitfaden für neue Anwendungen

Verwenden Sie EF Core auf .NET Core für alle neuen Anwendungen, es sei denn, die App benötigt etwas, das [nur auf .NET Framework unterstützt wird](/dotnet/standard/choosing-core-framework-server).

## <a name="guidance-for-existing-ef6-applications"></a>Leitfaden für vorhandene EF6-Anwendungen

EF Core ist kein direkter Ersatz für EF6. Für den Wechsel von EF6 zu EF Core sind wahrscheinlich Änderungen an Ihrer Anwendung erforderlich.

Beim Verschieben einer EF6-App zu .NET Core:

* Verwenden Sie EF6 weiterhin, wenn der Datenzugriffscode stabil ist und wahrscheinlich nicht weiterentwickelt wird oder neue Funktionen benötigt.
* Portieren Sie nach EF Core, wenn der Datenzugriffscode weiterentwickelt wird oder die neue App Funktionen benötigt, die nur in EF Core verfügbar sind.
* Das Portieren auf EF Core wird häufig auch um der Leistung willen durchgeführt. Aber weil nicht alle Szenarien schneller sind, sollten Sie zunächst eine Profilerstellung durchführen.

Weitere Informationen finden Sie unter [Portieren von EF6 auf EF Core](xref:efcore-and-ef6/porting/index).
