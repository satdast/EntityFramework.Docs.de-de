---
title: Überblick über Entity Framework Core – EF Core
description: Allgemeine Einführungsübersicht für Entity Framework Core
author: ajcvickers
ms.date: 9/20/2020
uid: core/index
ms.openlocfilehash: f2905fc55419e196a6f5b8883cf2a2a76b8a1ab2
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128601"
---
# <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework Core (EF Core) ist eine einfache, erweiterbare und plattformübergreifende [Open Source](https://github.com/dotnet/efcore)-Version der beliebten Entity Framework-Datenzugriffstechnologie.

EF Core kann als objektrelationaler Mapper (O/RM) fungieren, für den Folgendes gilt:

* .NET-Entwickler können mit einer-Datenbank mithilfe von .NET-Objekten arbeiten.
* Der größte Teil des Datenzugriffscodes, der normalerweise geschrieben werden muss, wird überflüssig.

Einzelheiten zu den von EF Core unterstützten Datenbank-Engines finden Sie unter [Datenbankanbieter](xref:core/providers/index).

## <a name="the-model"></a>Die Model-Komponente

Bei EF Core erfolgt der Datenzugriff über ein Modell. Ein Modell setzt sich aus Entitätsklassen und einem Kontextobjekt zusammen, das eine Sitzung mit der Datenbank darstellt. Das Kontextobjekt ermöglicht das Abfragen und Speichern von Daten. Weitere Informationen finden Sie unter [Erstellen eines Modells](xref:core/modeling/index).

EF unterstützt die folgenden Ansätze für Modellentwicklung:

* Generieren eines Modells aus einer vorhandenen Datenbank.
* Manuelles Codieren eines Modells, das der Datenbank entspricht.
* Nachdem ein Modell erstellt wurde, verwenden Sie [EF Migrations](xref:core/managing-schemas/migrations/index), um eine Datenbank aus dem Modell zu erstellen. Migrationen ermöglichen eine Weiterentwicklung der Datenbank, wenn sich das Modell ändert.

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a>Abfragen

Instanzen Ihrer Entitätsklassen werden mit Language [Integrated Query (LINQ)](/dotnet/csharp/programming-guide/concepts/linq/) aus der Datenbank abgerufen. Weitere Informationen finden Sie unter [Abfragen von Daten](xref:core/querying/index).

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a>Speichern von Daten

Daten werden in der Datenbank mithilfe von Instanzen Ihrer Entitätsklassen erstellt, gelöscht und geändert. Weitere Informationen finden Sie unter [Speichern von Daten](xref:core/saving/index).

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="ef-orm-considerations"></a>EF O/RM-Überlegungen

Obwohl EF Core viele Programmierungsdetails gut abstrahieren kann, gibt es einige für jeden O/RM anwendbare bewährte Methoden, die dazu beitragen, häufige Fallstricke in Produktionsanwendungen zu vermeiden:

* Kenntnisse auf mittlerem oder höherem Niveau des zugrundeliegenden Datenbankservers sind für Architektur, Debuggen, Profilerstellung und Datenmigration in Hochleistungsproduktions-Apps unerlässlich. Beispielsweise Kenntnisse zu Primär- und Fremdschlüsseln, Einschränkungen, Indizes, Normalisierung, DML- und DDL-Anweisungen, Datentypen, Profilerstellung usw.
* Funktions-und Integrationstests:  Es ist wichtig, die Produktionsumgebung so genau wie möglich zu replizieren, um Folgendes zu erreichen:
  * Finden von Problemen in der App, die nur bei Verwendung einer bestimmten Version oder Edition des Datenbankservers auftreten.
  * Erfassen von Breaking Changes beim Upgrade von EF Core und anderen Abhängigkeiten. Beispielsweise beim Hinzufügen oder Upgraden von Frameworks wie ASP.NET Core, OData oder AutoMapper. Diese Abhängigkeiten können sich auf EF Core auf unerwartete Weise auswirken.
* Leistungs- und Belastungstests mit repräsentativen Auslastungen. Die naive Verwendung einiger Funktionen ist nicht gut skalierbar. Beispielsweise mehrere Sammlungs-Includes, übermäßige Verwendung von Lazy Loading, bedingte Abfragen für nicht indizierte Spalten, massive Updates und Einfügungen mit vom Store generierten Werten, fehlende Parallelitätsverarbeitung, große Modelle, unzureichende Cacherichtlinien.
* Sicherheitsüberprüfung: Beispielsweise Verarbeitung von Verbindungszeichenfolgen und anderen Geheimnissen, Datenbankberechtigungen für Nicht-Bereitstellungsvorgänge, Eingabevalidierung für Roh-SQL, Verschlüsselung für sensible Daten.
* Stellen Sie sicher, dass Protokollierung und Diagnose ausreichend und verwendbar sind. Beispielsweise geeignete Protokollierungskonfiguration, Abfragetags und Application Insights.
* Wiederherstellung nach Fehlern. Vorbereiten von Eventualitäten für häufige Ausfallszenarien wie Versionsrollback, Fallbackserver, horizontale Skalierung und Lastenausgleich, DoS-Entschärfung und Datensicherungen.
* Anwendungsbereitstellung und -migration. Planen, wie Migrationen während der Bereitstellung angewendet werden sollen. Die Ausführung beim Anwendungsstart kann unter Parallelitätsproblemen leiden und erfordert höhere Berechtigungen, als für den normalen Betrieb erforderlich sind. Verwenden von Staging, um Wiederherstellung nach schwerwiegenden Fehlern während der Migration zu ermöglichen. Weitere Informationen finden Sie unter [Anwenden von Migrationen](xref:core/managing-schemas/migrations/applying).
* Ausführliche Untersuchung und Testen generierter Migrationen. Migrationen sollten gründlich getestet werden, bevor sie auf Produktionsdaten angewendet werden. Die Form des Schemas und die Spaltentypen können nicht einfach geändert werden, sobald die Tabellen Produktionsdaten enthalten. Beispielsweise sind `nvarchar(max)` und `decimal(18, 2)` in SQL Server selten die besten Typen für Spalten, die Zeichenfolgen- und Dezimaleigenschaften zugeordnet sind. Dies sind aber die Standardwerte, die EF aufgrund fehlender Kenntnisse bezüglich Ihres speziellen Szenarios verwendet.

## <a name="next-steps"></a>Nächste Schritte

Einführungstutorials finden Sie unter [Erste Schritte mit Entity Framework Core](xref:core/get-started/overview/first-app).
