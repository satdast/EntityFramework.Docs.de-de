---
title: Neuigkeiten in EF Core 1.1 – EF Core
description: Änderungen und Verbesserungen an Entity Framework Core 1.1
author: ajcvickers
ms.date: 10/27/2016
uid: core/what-is-new/ef-core-1.1
ms.openlocfilehash: a865270d0b1b690ed2596e7ed550463ab0877bca
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063464"
---
# <a name="new-features-in-ef-core-11"></a>Neue Features in EF Core 1.1

## <a name="modeling"></a>Modellierung

### <a name="field-mapping"></a>Feldzuordnung

Ermöglicht Ihnen das Konfigurieren eines Unterstützungsfelds für eine Eigenschaft. Dies kann für schreibgeschützte Eigenschaften oder Daten nützlich sein, die anstelle einer Eigenschaft Get/Set-Methoden verwenden.

### <a name="mapping-to-memory-optimized-tables-in-sql-server"></a>Zuordnung zu speicheroptimierten Tabellen in SQL Server

Sie können angeben, dass es sich bei der einer Entität zugeordneten Tabelle um eine speicheroptimierte Tabelle handelt. Beim Einsatz von EF Core zum Erstellen und Verwalten einer auf Ihrem Modell basierenden Datenbank (entweder mit Migrationen oder `Database.EnsureCreated()`) wird eine speicheroptimierte Tabelle für diese Entitäten erstellt.

## <a name="change-tracking"></a>Änderungsnachverfolgung

### <a name="additional-change-tracking-apis-from-ef6"></a>Zusätzliche APIs für die Änderungsnachverfolgung aus EF6

Hierzu gehören beispielsweise `Reload`, `GetModifiedProperties`, `GetDatabaseValues` usw.

## <a name="query"></a>Abfrage

### <a name="explicit-loading"></a>Explizites Laden

Ermöglicht es Ihnen, das Auffüllen einer Navigationseigenschaft oder einer Entität auszulösen, die zuvor aus der Datenbank geladen wurde.

### <a name="dbsetfind"></a>DbSet.Find

Bietet eine einfache Möglichkeit zum Abrufen einer Entität basierend auf dem Wert ihres Primärschlüssels.

## <a name="other"></a>Andere

### <a name="connection-resiliency"></a>Verbindungsstabilität

Für nicht erfolgreich ausgeführte Datenbankbefehle werden automatisch Neuversuche ausgeführt. Dies ist insbesondere bei der Verbindungsherstellung mit SQL Azure nützlich, weil hier häufiger vorübergehende Fehler auftreten.

### <a name="simplified-service-replacement"></a>Vereinfachte Dienstersetzung

Es ist jetzt einfacher, von EF verwendete interne Dienste zu ersetzen.
