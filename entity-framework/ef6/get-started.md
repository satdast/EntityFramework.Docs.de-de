---
title: Erste Schritte mit Entity Framework 6 – EF6
description: Erste Schritte mit Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/get-started
ms.openlocfilehash: 825fe3716ade947f1660c8131100a1c3acba5a1d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072134"
---
# <a name="get-started-with-entity-framework-6"></a>Erste Schritte mit Entity Framework 6

Dieser Leitfaden enthält Links zu ausgewählten Dokumentationsartikeln, exemplarischen Vorgehensweisen und Videos, die Ihnen beim schnellen Einstieg helfen können.

## <a name="fundamentals"></a>Grundlagen

* [Abrufen von Entity Framework](xref:ef6/fundamentals/install)

  Hier erfahren Sie, wie Sie Entity Framework zu Ihren Anwendungen hinzufügen können. Wenn Sie den EF-Designer verwenden möchten, dann installieren Sie ihn in Visual Studio.

* [Erstellen eines Modells: Code First, der EF-Designer und die EF-Workflows](xref:ef6/modeling/index)

  Möchten Sie Ihr EF-Modell lieber durch Schreiben von Code oder Zeichnen von Feldern und Linien entwerfen?
Werden Sie EF verwenden, um Ihre Objekte einer vorhandenen Datenbank zuzuweisen oder soll EF eine Datenbank erstellen, die perfekt auf Ihre Objekte abgestimmt ist?
Hier erfahren Sie mehr zu den zwei Ansätzen zur Verwendung von EF6: EF-Designer und Code First.
Verfolgen Sie die Diskussion, und sehen Sie sich das Video zu den Unterschieden an.

* [Arbeiten mit DbContext](xref:ef6/fundamentals/working-with-dbcontext)

  DBContext ist der erste und auch wichtigste EF-Typ, dessen Verwendung Sie kennen müssen. Er dient als Launchpad für Datenbankabfragen und verfolgt Änderungen, die Sie an Objekten vornehmen, damit sie in die Datenbank zurückgespeichert werden können.

* [Frage stellen](xref:ef6/resources/get-help)

  Finden Sie heraus, wie Sie Hilfe von Experten erhalten und mit Ihren eigenen Antworten zur Community beitragen können.

* [Mitwirken](https://github.com/aspnet/EntityFramework6/)

  Entity Framework 6 verwendet ein offenes Entwicklungsmodell. Erfahren Sie im GitHub-Repository, wie Sie EF noch besser machen können.

## <a name="code-first-resources"></a>Code First-Ressourcen

  - [Code First mit einer vorhandenen Datenbank](xref:ef6/modeling/code-first/workflows/existing-database)
  - [Code First für eine neue Datenbank](xref:ef6/modeling/code-first/workflows/new-database)
  - [Unterstützung von Enumerationen – Code First](xref:ef6/modeling/code-first/data-types/enums)
  - [Mapping Spatial Types Using Code First (Zuordnen von räumlichen Typen mit Code First)](xref:ef6/modeling/code-first/data-types/spatial)
  - [Benutzerdefinierte Code First-Konventionen](xref:ef6/modeling/code-first/conventions/custom)
  - [Using Code First Fluent Configuration with Visual Basic (Verwenden der Fluent-Konfiguration von Code First für Visual Basic)](xref:ef6/modeling/code-first/fluent/vb)
  - [Code First-Migrationen](xref:ef6/modeling/code-first/migrations/index)
  - [Code First Migrations in Team Environments (Code First-Migrationen in Teamumgebungen)](xref:ef6/modeling/code-first/migrations/teams)
  - [Automatische Code First-Migrationen](xref:ef6/modeling/code-first/migrations/automatic) (wird nicht länger empfohlen)

## <a name="ef-designer-resources"></a>EF Designer-Ressourcen
  - [Database First-Workflow](xref:ef6/modeling/designer/workflows/database-first)
  - [Model First-Workflow](xref:ef6/modeling/designer/workflows/model-first)
  - [Mapping Enums (Zuordnen von Enumerationen)](xref:ef6/modeling/designer/data-types/enums)
  - [Mapping Spatial Types (Zuordnen von räumlichen Typen)](xref:ef6/modeling/designer/data-types/spatial)
  - [Table-Per Hierarchy Inheritance Mapping (Vererbungszuordnung einer Tabelle pro Hierarchie)](xref:ef6/modeling/designer/inheritance/tph)
  - [Table-Per Type Inheritance Mapping („Tabelle pro Typ“-Vererbungszuordnung)](xref:ef6/modeling/designer/inheritance/tpt)
  - [Stored Procedure Mapping for Updates (Zuordnung einer gespeicherten Prozedur für Updates)](xref:ef6/modeling/designer/stored-procedures/cud)
  - [Stored Procedure Mapping for Query (Zuordnung einer gespeicherten Prozedur für die Abfrage)](xref:ef6/modeling/designer/stored-procedures/query)
  - [Entitätsaufteilung](xref:ef6/modeling/designer/entity-splitting)
  - [Tabellenaufteilung](xref:ef6/modeling/designer/table-splitting)
  - [Definieren der Abfrage](xref:ef6/modeling/designer/advanced/defining-query) (erweitert)
  - [Tabellenwertfunktionen](xref:ef6/modeling/designer/advanced/tvfs) (erweitert)

## <a name="other-resources"></a>Weitere Ressourcen
  - [Asynchrone Abfragen und Speichern](xref:ef6/fundamentals/async)
  - [Datenbindung mit Windows Forms](xref:ef6/fundamentals/databinding/winforms)
  - [Datenbindung mit WPF](xref:ef6/fundamentals/databinding/wpf)
  - [Disconnected scenarios with Self-Tracking Entities (Szenarios mit getrennten Verbindungen mit Entitäten mit Selbstnachverfolgung)](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (wird nicht länger empfohlen)
