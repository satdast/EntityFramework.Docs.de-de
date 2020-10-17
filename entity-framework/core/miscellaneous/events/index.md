---
title: 'Überblick über Protokollierung und Abfangfunktionen: EF Core'
description: Übersicht über Protokollierung, Ereignisse, Interceptors und Diagnosefunktionen für EF Core
author: ajcvickers
ms.date: 10/01/2020
uid: core/miscellaneous/events/index
ms.openlocfilehash: 8c46b4efb205c60be51529a89cd9dd8fb5268156
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066495"
---
# <a name="overview-of-logging-and-interception"></a>Übersicht über Protokollierung und Abfangfunktionen

Entity Framework Core (EF Core) enthält mehrere Mechanismen für das Erstellen von Protokollen, Reagieren auf Ereignisse und Erstellen von Diagnosen. Diese Mechanismen sind jeweils auf verschiedene Situationen zugeschnitten, und es ist wichtig, den besten Mechanismus für eine jeweilige Aufgabe zu wählen, auch wenn mehrere Mechanismen möglich wären. Ein Datenbankinterceptor könnte beispielsweise für eine SQL-Protokollierung verwendet werden. Besser hierfür eignen sich jedoch protokollierungsspezifische Mechanismen. Auf dieser Seite finden Sie eine Übersicht der verschiedenen Mechanismen sowie Erläuterungen, wann die einzelnen verwendet werden sollten.

## <a name="simple-logging"></a>Einfache Protokollierung

> [!NOTE]
> Dieses Feature wurde in EF Core 5.0 hinzugefügt.

Der Zugriff auf EF Core-Protokolle ist über Anwendungen jeglichen Typs über die Verwendung von [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> beim [Konfigurieren einer DbContext-Instanz](xref:core/miscellaneous/configuring-dbcontext) möglich. Diese Konfiguration erfolgt in der Regel durch ein Überschreiben von <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>. Beispiel:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

Dieses Konzept ähnelt <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.

Weitere Informationen finden Sie unter [Einfache Protokollierung](xref:core/miscellaneous/events/simple-logging).
