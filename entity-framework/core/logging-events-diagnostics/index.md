---
title: 'Überblick über Protokollierung und Abfangfunktionen: EF Core'
description: Übersicht über Protokollierung, Ereignisse, Interceptors und Diagnosefunktionen für EF Core
author: ajcvickers
ms.date: 10/01/2020
uid: core/logging-events-diagnostics/index
ms.openlocfilehash: 5ddbffc8d39e97065f2e06af14443c62b4a9465d
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129225"
---
# <a name="overview-of-logging-and-interception"></a>Übersicht über Protokollierung und Abfangfunktionen

Entity Framework Core (EF Core) enthält mehrere Mechanismen für das Erstellen von Protokollen, Reagieren auf Ereignisse und Erstellen von Diagnosen. Diese Mechanismen sind jeweils auf verschiedene Situationen zugeschnitten, und es ist wichtig, den besten Mechanismus für die betreffende Aufgabe auszuwählen, auch wenn mehrere Mechanismen möglich wären. Ein Datenbankinterceptor könnte beispielsweise für eine SQL-Protokollierung verwendet werden. Besser hierfür eignen sich jedoch protokollierungsspezifische Mechanismen. Auf dieser Seite finden Sie eine Übersicht der verschiedenen Mechanismen sowie Erläuterungen, wann die einzelnen verwendet werden sollten.

## <a name="quick-reference"></a>Kurzreferenz

In der folgenden Tabelle finden Sie eine kurze Übersicht über die Unterschiede zwischen den hier beschriebenen Mechanismen.

| Mechanismus |  Async | `Scope` | Registriert | Beabsichtigte Verwendung
|:----------|--------|-------|------------|-------------
| Einfache Protokollierung | Nein | Pro Kontext | Kontextbezogene Konfiguration | Protokollierung zur Entwicklungszeit
| Microsoft.Extensions.Logging | Nein | Pro Kontext* | Abhängigkeitsinjektion oder kontextbezogene Konfiguration | Produktionsprotokollierung
| Ereignisse | Nein | Pro Kontext | Beliebiger Zeitpunkt | Reagieren auf EF-Ereignisse
| Interceptors | Ja | Pro Kontext | Kontextbezogene Konfiguration | Manipulieren von EF-Vorgängen
| Diagnoselistener | Nein | Prozess | Global | Anwendungsdiagnose

*`Microsoft.Extensions.Logging` wird in der Regel pro Anwendung über Abhängigkeitsinjektion konfiguriert. Auf der EF-Ebene _kann_ jedoch jeder Kontext bei Bedarf mit einer anderen Protokollierung konfiguriert werden.

## <a name="simple-logging"></a>Einfache Protokollierung

> [!NOTE]
> Dieses Feature wurde in EF Core 5.0 eingeführt.

Beim [Konfigurieren einer DbContext-Instanz](xref:core/dbcontext-configuration/index) kann von jedem Anwendungstyp aus mithilfe von <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> auf EF Core-Protokolle zugegriffen werden. Diese Konfiguration erfolgt in der Regel durch ein Überschreiben von <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>. Beispiel:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

Dieses Konzept ähnelt <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.

Weitere Informationen finden Sie unter [Einfache Protokollierung](xref:core/logging-events-diagnostics/simple-logging).

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

[Microsoft.Extensions.Logging](/dotnet/core/extensions/logging) ist ein erweiterbarer Protokollierungsmechanismus mit Plug-In-Anbietern für viele gängige Protokollierungssysteme. EF Core ist vollständig in `Microsoft.Extensions.Logging` integriert, und diese Art von Protokollierung wird standardmäßig für ASP.NET Core-Anwendungen verwendet.

Weitere Informationen finden Sie unter [Verwenden von Microsoft.Extensions.Logging in EF Core](xref:core/logging-events-diagnostics/extensions-logging).

## <a name="events"></a>Ereignisse

> [!NOTE]
> Zusätzliche Ereignisse wurden in EF Core 5.0 eingeführt.

EF Core stellt [.NET-Ereignisse](/dotnet/standard/events/) bereit, die als Rückrufe fungieren, wenn bestimmte Dinge im EF Core-Code auftreten. Ereignisse sind einfacher als Abfangfunktionen und ermöglichen eine flexiblere Registrierung. Sie sind jedoch nur synchron und können daher keine nicht blockierenden asynchronen E/A-Vorgänge durchführen.

Ereignisse werden pro DbContext-Instanz registriert, und diese Registrierung kann zu einem beliebigen Zeitpunkt durchgeführt werden. Verwenden Sie einen [Diagnoselistener](xref:core/logging-events-diagnostics/diagnostic-listeners), um dieselben Informationen abzurufen, jedoch für alle DbContext-Instanzen im Prozess.

Weitere Informationen finden Sie unter [.NET-Ereignisse in EF Core](xref:core/logging-events-diagnostics/events).

## <a name="interception"></a>Interception

> [!NOTE]
> Dieses Feature wurde in EF Core 3.0 eingeführt. In EF Core 5.0 wurden weitere Abfangfunktionen (Interceptors) eingeführt.

EF Core-Abfangfunktionen ermöglichen das Abfangen, Ändern und/oder Unterdrücken von EF Core-Vorgängen. Dies umfasst Datenbankvorgänge auf niedriger Ebene, z. B. das Ausführen eines Befehls, sowie Vorgänge höherer Ebene, z. B. Aufrufe von SaveChanges.

Abfangfunktionen unterscheiden sich von Protokollierung und Diagnose insofern, als sie eine Änderung oder Unterdrückung des abzufangenden Vorgangs ermöglichen. [Einfache Protokollierung](xref:core/logging-events-diagnostics/simple-logging) oder [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) sind bessere Optionen für Protokollierung.

Abfangfunktionen werden pro DbContext-Instanz registriert, wenn der Kontext konfiguriert wird. Verwenden Sie einen [Diagnoselistener](xref:core/logging-events-diagnostics/diagnostic-listeners), um dieselben Informationen abzurufen, jedoch für alle DbContext-Instanzen im Prozess.

Weitere Informationen finden Sie unter [Abfangfunktionen](xref:core/logging-events-diagnostics/interceptors).

## <a name="diagnostic-listeners"></a>Diagnoselistener

Diagnoselistener ermöglichen das Lauschen auf EF Core-Ereignisse, die im aktuellen .NET-Prozess auftreten.

Diagnoselistener sind nicht geeignet, um Ereignisse aus einer einzelnen DbContext-Instanz abzurufen. EF Core-Abfangfunktionen ermöglichen Zugriff auf dieselben Ereignisse mit Pro-Kontext-Registrierung.

Diagnoselistener sind nicht für Protokollierung konzipiert. [Einfache Protokollierung](xref:core/logging-events-diagnostics/simple-logging) oder [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) sind bessere Optionen für Protokollierung.

Weitere Informationen finden Sie unter [Verwenden von Diagnoselistenern in EF Core](xref:core/logging-events-diagnostics/diagnostic-listeners).
