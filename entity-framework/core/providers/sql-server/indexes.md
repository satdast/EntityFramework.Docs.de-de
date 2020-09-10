---
title: Microsoft SQL Server Datenbankanbieter-Indizes-EF Core
description: Spezifische Index Features für den Entity Framework Core SQL Server-Anbieter
author: roji
ms.author: shrojans
ms.date: 9/1/2020
uid: core/providers/sql-server/indexes
ms.openlocfilehash: 3830377562fcc6a59239cd2c09d1419bbd00922e
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620703"
---
# <a name="index-features-specific-to-the-entity-framework-core-sql-server-provider"></a>Spezifische Index Features für den Entity Framework Core SQL Server-Anbieter

Auf dieser Seite werden die Index Konfigurationsoptionen beschrieben, die für den SQL Server-Anbieter spezifisch sind.

## <a name="clustering"></a>Clustering

Gruppierte Indizes sortieren und speichern die Datenzeilen in der Tabelle oder Sicht basierend auf ihren Schlüsselwerten. Wenn Sie den richtigen gruppierten Index für die Tabelle erstellen, kann die Geschwindigkeit Ihrer Abfragen erheblich verbessert werden, da die Daten bereits in der optimalen Reihenfolge angeordnet sind. Pro Tabelle kann nur ein gruppierter Index vorhanden sein, da die Datenzeilen nur in einer Reihenfolge gespeichert werden können. Weitere Informationen finden Sie [in der SQL Server-Dokumentation zu gruppierten und nicht gruppierten Indizes](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).

Standardmäßig wird die Primärschlüssel Spalte einer Tabelle implizit durch einen gruppierten Index gestützt, und alle anderen Indizes sind nicht gruppiert.

Sie können einen Index oder Schlüssel so konfigurieren, dass er wie folgt gruppiert wird:

[!code-csharp[ClusteredIndex](../../../../samples/core/SqlServer/Indexes/ClusteredIndexContext.cs?name=ClusteredIndex)]

## <a name="fill-factor"></a>Füllfaktor

> [!NOTE]
> Diese Funktion wird in EF Core 5.0 eingeführt.

Die Füllfaktor Option Index wird für die Feinabstimmung der Speicherung und Leistung von Indexdaten bereitgestellt. Weitere Informationen finden [Sie in der SQL Server-Dokumentation zum Füllfaktor](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).

Sie können den Füllfaktor eines Indexes wie folgt konfigurieren:

[!code-csharp[IndexFillFactor](../../../../samples/core/SqlServer/Indexes/IndexFillFactorContext.cs?name=IndexFillFactor)]

## <a name="online-creation"></a>Online Erstellung

Die Online-Option ermöglicht gleichzeitigen Benutzern den Zugriff auf die zugrunde liegenden Tabellen-oder gruppierten Indexdaten und alle zugehörigen nicht gruppierten Indizes während der Indexerstellung, sodass Benutzer die zugrunde liegenden Daten weiterhin aktualisieren und Abfragen können. Wenn Sie DDL-Vorgänge (Datendefinitionssprache) wie das Erstellen oder Neuerstellen eines gruppierten Indexes offline ausführen, richten diese Vorgänge exklusive Sperren für die dem Index zugrunde liegenden Daten und damit verbundene Indizes ein. Weitere Informationen finden Sie [in der SQL Server-Dokumentation der Online Index Option](/sql/relational-databases/indexes/perform-index-operations-online).

Sie können einen Index mit der Online-Option wie folgt konfigurieren:

[!code-csharp[IndexOnline](../../../../samples/core/SqlServer/Indexes/IndexOnlineContext.cs?name=IndexOnline)]
