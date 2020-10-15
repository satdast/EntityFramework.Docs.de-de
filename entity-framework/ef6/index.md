---
title: Überblick über Entity Framework 6 (EF6)
description: Überblick über Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/index
ms.openlocfilehash: 09b7cb9e0141e9739b15d6c9789286c2e47018cc
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065393"
---
# <a name="entity-framework-6"></a>Entity Framework 6
Entity Framework 6 (EF6) ist ein bewährtes Framework für objektrelationale Abbildung (Object-Relational Mapping, ORM) für .NET, dessen Features im Laufe der Zeit immer weiter optimiert und stabilisiert wurden.

Als ORM-Framework senkt EF6 die Inkongruenz zwischen dem relationalen und dem objektorientierten Bereich. Dadurch können Entwickler Anwendungen schreiben, die mit Daten interagieren, die in relationalen Datenbanken mit stark typisierten .NET-Objekten gespeichert wurden, die die Anwendungsdomäne darstellen. Außerdem ist es durch EF6 nicht mehr erforderlich, dass ein großer Teil der Daten auf Grundlagencode zugreifen muss, den sie normalerweise schreiben müssen.

EF6 implementiert viele beliebte ORM-Features:
- Zuordnung von [POCO](xref:ef6/resources/glossary#poco)-Entitätsklassen, die nicht von EF-Typen abhängen
- Automatische Änderungsnachverfolgung
- Identitätsauflösung und Arbeitseinheit
- Eager und Lazy Loading sowie explizites Laden
- Übersetzung stark typisierter Abfragen mit [LINQ (Language Integrated Query)](https://aka.ms/AA6hsvu)
- Umfangreiche Mappingfunktionen, u.a. Unterstützung für:
  - 1:1-, 1:n- und m:n-Beziehungen
  - Vererbung (Tabelle pro Hierarchie, Tabelle pro Typ und Tabelle pro konkreter Klasse)
  - Komplexe Typen
  - Gespeicherte Prozeduren
- Ein visueller Designer zum Erstellen von Entitätsmodellen.
- Ein Code First-Prozess für das Erstellen von Entitätsmodellen durch das Schreiben von Code.
- Modelle können entweder anhand vorhandener Datenbanken erstellt und dann manuell bearbeitet werden, oder Sie können sie komplett neu erstellen und dann zum Erstellen neuer Datenbanken verwenden.
- Integration in .NET Framework-Anwendungsmodelle, ASP.NET eingeschlossen, und anhand von Datenbindung mit WPF und WinForms.
- Datenbankkonnektivität basierend auf ADO.NET und zahlreichen anderen [Anbietern](xref:ef6/fundamentals/providers/index), die eine Verbindung mit SQL Server, Oracle, MySQL, SQLite, PostgreSQL, DB2 usw. herstellen.

## <a name="should-i-use-ef6-or-ef-core"></a>Sollte ich EF6 oder EF Core verwenden?

EF Core ist eine modernere, einfache und erweiterbare Version von Entity Framework mit ähnlichen Funktionen wie EF6 und einigen Vorteilen.
EF Core wurde vollständig neu geschrieben und enthält viele neue Features, die es in EF6 nicht gibt. Andererseits fehlen die komplexesten Mappingfeatures, die in EF6 vorhanden sind.
Erwägen Sie die Verwendung von EF Core in neuen Anwendungen, wenn die enthaltenen Features Ihren Anforderungen entsprechen.
Unter [Vergleichen von EF Core und EF 6](xref:efcore-and-ef6/index) wird darauf ausführlicher eingegangen.

## <a name="get-started"></a>[Erste Schritte](xref:ef6/get-started)

Fügen Sie Ihrem Projekt das EntityFramework-NuGet-Paket hinzu, oder installieren Sie [Entity Framework Tools für Visual Studio](https://aka.ms/AA6i8c5). Sehen Sie sich anschließend Videos an, lesen Sie Tutorials und weiterführende Dokumentationsartikel, sodass Sie die Funktionen von Entity Framework 6 optimal nutzen können.

## <a name="past-entity-framework-versions"></a>Frühere Versionen von Entity Framework

Dies ist die Dokumentation für die aktuelle Version von Entity Framework 6. Viele der Punkte gelten jedoch auch für ältere Releases.
Unter [Neues](xref:ef6/what-is-new/index) und [Frühere Releases](xref:ef6/what-is-new/past-releases) finden Sie eine vollständige Liste der EF-Releases mit dazugehörigen neuen Features.
