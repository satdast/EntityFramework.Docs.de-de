---
title: Neuigkeiten in EF Core 1.1 – EF Core
description: Änderungen und Verbesserungen an Entity Framework Core 1.1
author: divega
ms.date: 10/27/2016
ms.assetid: C7FE8C85-445A-4F0C-97EC-CC3F7F1D6F5E
uid: core/what-is-new/ef-core-1.1
ms.openlocfilehash: 89dfe4b4772e9e6dc232860f38f07c94654af437
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618808"
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
