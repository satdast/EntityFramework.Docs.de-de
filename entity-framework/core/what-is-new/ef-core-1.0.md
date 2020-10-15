---
title: Neuigkeiten in EF Core 1.0 – EF Core
description: Änderungen und Verbesserungen in Entity Framework Core 1.0
author: ajcvickers
ms.date: 10/27/2016
uid: core/what-is-new/ef-core-1.0
ms.openlocfilehash: 4066dced67a54770664b9b1348adb6ac2622ce1a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062931"
---
# <a name="features-included-in-ef-core-10"></a>Features in EF Core 1.0

## <a name="platforms"></a>Plattformen

### <a name="net-framework-451"></a>.NET Framework 4.5.1

Dies schließt die Konsole, WPF, WinForms, ASP.NET 4 usw. ein.

### <a name="net-standard-13"></a>.NET Standard 1.3

Einschließlich ASP.NET Core für .NET Framework und .NET Core unter Windows, OSX und Linux.

## <a name="modelling"></a>Modellierung

### <a name="basic-modelling"></a>Grundlegende Modellierung

Basierend auf POCO-Entitäten mit get/set-Eigenschaften gängiger skalarer Typen (`int`, `string` usw.).

### <a name="relationships-and-navigation-properties"></a>Beziehungen und Navigationseigenschaften

Beziehungen vom Typ „1:n“ und „1:0 oder 1“ können im Modell basierend auf einem Fremdschlüssel angegeben werden. Navigationseigenschaften einfacher Sammlungs- oder Verweistypen können diesen Beziehungen zugeordnet werden.

### <a name="built-in-conventions"></a>Integrierte Konventionen

Diese bilden ein erstes Modell, das auf der Form der Entitätsklassen basiert.

### <a name="fluent-api"></a>Fluent-API

Ermöglicht es Ihnen, die `OnModelCreating`-Methode für Ihren Kontext zu überschreiben, um das per Konvention erkannte Modell weiter zu konfigurieren.

### <a name="data-annotations"></a>Datenanmerkungen

Dies sind Attribute, die zu Ihren Entitätsklassen/Eigenschaften hinzugefügt werden können und das EF-Modell beeinflussen. Wenn Sie z.B. `[Required]` hinzufügen, informiert dies EF darüber, dass eine Eigenschaft erforderlich ist.

### <a name="relational-table-mapping"></a>Zuordnung in relationalen Tabellen

Ermöglicht das Zuordnen von Entitäten zu Tabellen/Spalten.

### <a name="key-value-generation"></a>Generieren von Schlüsselwerten

Dies schließt das clientseitige Generieren und die Datenbankgenerierung ein.

### <a name="database-generated-values"></a>Von der Datenbank generierte Werte

Ermöglicht es der Datenbank, beim Einfügen (Standardwerte) oder beim Aktualisieren (berechnete Spalten) Werte zu generieren.

### <a name="sequences-in-sql-server"></a>Sequenzen in SQL Server

Ermöglicht das Definieren von Sequenzobjekten im Modell.

### <a name="unique-constraints"></a>UNIQUE-Einschränkungen

Ermöglicht das Definieren alternativer Schlüssel sowie die Definition von Beziehungen, die diesen Schlüssel als Ziel verwenden.

### <a name="indexes"></a>Indizes

Durch das Definieren von Indizes im Modell werden automatisch Indizes in der Datenbank eingeführt. UNIQUE-Indizes werden ebenfalls unterstützt.

### <a name="shadow-state-properties"></a>Eigenschaften im Schattenzustand

Ermöglicht das Definieren von Eigenschaften im Modell, die nicht deklariert und nicht in der .NET-Klasse gespeichert sind, aber durch EF Core nachverfolgt und aktualisiert werden können. Diese Eigenschaften werden häufig für Fremdschlüsseleigenschaften verwendet, wenn das Offenlegen dieser Eigenschaften im Objekt nicht gewünscht wird.

### <a name="table-per-hierarchy-inheritance-pattern"></a>TPH-Vererbungsmuster (Tabelle pro Hierarchie)

Ermöglicht es, Entitäten in einer Vererbungshierarchie in einer einzigen Tabelle zu speichern. Hierbei wird eine Diskriminatorspalte verwendet, um den Entitätstyp für einen bestimmten Datensatz in der Datenbank zu identifizieren.

### <a name="model-validation"></a>Modellvalidierung

Erkennt ungültige Muster im Modell und stellt nützliche Fehlermeldungen bereit.

## <a name="change-tracking"></a>Änderungsnachverfolgung

### <a name="snapshot-change-tracking"></a>Änderungsnachverfolgung für Momentaufnahmen

Ermöglicht die automatische Erkennung von Änderungen in Entitäten durch den Vergleich des aktuellen Zustands mit einer Kopie (Momentaufnahme) des ursprünglichen Zustands.

### <a name="notification-change-tracking"></a>Benachrichtigung der Änderungsnachverfolgung

Ermöglicht es Ihren Entitäten, die Änderungsnachverfolgung zu benachrichtigen, wenn der Wert einer Eigenschaft geändert wurde.

### <a name="accessing-tracked-state"></a>Zugriff auf den erfassten Zustand

Über `DbContext.Entry` und `DbContext.ChangeTracker`.

### <a name="attaching-detached-entitiesgraphs"></a>Anfügen abgekoppelter Entitäten/Graphen

Die neue `DbContext.AttachGraph`-API ermöglicht es, Entitäten erneut an einen Kontext anzufügen, um neue/geänderte Entitäten zu speichern.

## <a name="saving-data"></a>Speichern von Daten

### <a name="basic-save-functionality"></a>Grundlegende Speicherfunktionalität

Ermöglicht das Persistieren von Änderungen an Entitätsinstanzen in der Datenbank.

### <a name="optimistic-concurrency"></a>Optimistische Nebenläufigkeit

Schützt vor dem Überschreiben von Änderungen, die seit dem Abruf von Daten aus der Datenbank von einem anderen Benutzer vorgenommen wurden.

### <a name="async-savechanges"></a>Asynchrones SaveChanges

Der aktuelle Thread kann freigegeben werden, um andere Anforderungen zu verarbeiten, während die Datenbank die über `SaveChanges` ausgeführten Befehle verarbeitet.

### <a name="database-transactions"></a>Datenbanktransaktionen

Bedeutet, dass `SaveChanges` immer unteilbar ist (entweder ist der Vorgang als Ganzes erfolgreich, oder es werden keine Änderungen an der Datenbank vorgenommen). Es gibt auch transaktionsbezogene APIs, die eine gemeinsame Verwendung von Transaktionen durch Kontextinstanzen usw. ermöglicht.

### <a name="relational-batching-of-statements"></a>Relational: Anweisungsbatches

Bietet bessere Leistung durch das Zusammenfassen mehrerer INSERT/UPDATE/DELETE-Befehle in einem einzigen Roundtrip an die Datenbank.

## <a name="query"></a>Abfrage

### <a name="basic-linq-support"></a>Grundlegende LINQ-Unterstützung

Bietet die Möglichkeit, LINQ zum Abrufen von Daten aus der Datenbank zu verwenden.

### <a name="mixed-clientserver-evaluation"></a>Gemischte Client-/Serverauswertung

Abfragen können Logik enthalten, die nicht in der Datenbank ausgewertet werden kann und deshalb eine Auswertung der Daten nach dem Abruf in den Speicher erfordern.

### <a name="notracking"></a>NoTracking

Abfragen können schneller ausgeführt werden, wenn der Kontext nicht überwachen muss, ob Änderungen an den Entitätsinstanzen durchgeführt wurden (das ist nützlich, wenn die Ergebnisse schreibgeschützt sind).

### <a name="eager-loading"></a>Eager Loading

Stellt die Methoden `Include` und `ThenInclude` bereit, um bezogene Daten zu identifizieren, die bei der Abfrage ebenfalls abgerufen werden sollten.

### <a name="async-query"></a>Asynchrone Abfrage

Der aktuelle Thread (und zugeordnete Ressourcen) kann freigegeben werden, um andere Anforderungen zu verarbeiten, während die Datenbank die Abfrage verarbeitet.

### <a name="raw-sql-queries"></a>Unformatierte SQL-Abfragen

Stellt die `DbSet.FromSql`-Methode zur Verwendung von unformatierten SQL-Abfragen zum Abrufen von Daten bereit. Diese Abfragen können auch mit LINQ erstellt werden.

## <a name="database-schema-management"></a>Verwaltung des Datenbankschemas

### <a name="database-creationdeletion-apis"></a>APIs zum Erstellen/Löschen von Datenbanken

Sind hauptsächlich für Tests gedacht, bei denen Sie die Datenbank schnell erstellen/löschen möchten, ohne Migrationen zu verwenden.

### <a name="relational-database-migrations"></a>Migrationen relationaler Datenbanken

Hiermit werden spätere Änderungen an einem relationalen Datenbankschema ermöglicht, wenn sich Ihr Modell ändert.

### <a name="reverse-engineer-from-database"></a>Reverse Engineering aus der Datenbank

Richtet per Gerüstbau ein EF-Modell basierend auf einem vorhandenen relationalen Datenbankschema ein.

## <a name="database-providers"></a>Datenbankanbieter

### <a name="sql-server"></a>SQL Server

Stellt eine Verbindung mit Microsoft SQL Server 2008 und höher her.

### <a name="sqlite"></a>SQLite

Stellt eine Verbindung mit einer SQLite 3-Datenbank her.

### <a name="in-memory"></a>In-Memory

Ist für das einfache Testen ohne Verbindung mit einer echten Datenbank konzipiert.

### <a name="3rd-party-providers"></a>Datenbankanbieter von Drittanbietern

Es stehen verschiedene Anbieter für andere Datenbank-Engines zur Verfügung. Eine vollständige Liste finden Sie unter [Datenbankanbieter](xref:core/providers/index).
