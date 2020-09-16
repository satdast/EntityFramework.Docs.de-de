---
title: Laden zugehöriger Daten – EF Core
description: Verschiedene Strategien für das Laden verknüpfter Daten mit Entity Framework Core
author: roji
ms.date: 9/11/2020
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 5c1addac6735de39d5c72f447d5d06280411a21e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078850"
---
# <a name="loading-related-data"></a>Laden zugehöriger Daten

Mit Entity Framework Core können Sie die Navigationseigenschaften in Ihrem Modell zum Laden zugehöriger Entitäten verwenden. Es gibt drei allgemeine O/RM-Muster für das Laden zugehöriger Daten.

* **[Eager Loading](xref:core/querying/related-data/eager)** bedeutet, dass die zugehörigen Daten als Teil der ersten Abfrage aus der Datenbank geladen werden.
* **[Explizites Laden](xref:core/querying/related-data/explicit)** bedeutet, dass die zugehörigen Daten zu einem späteren Zeitpunkt explizit aus der Datenbank geladen werden.
* **[Lazy Loading](xref:core/querying/related-data/lazy)** bedeutet, dass die zugehörigen Daten bei Zugriff auf die Navigationseigenschaft transparent aus der Datenbank geladen werden.

> [!TIP]
> Sie können die [Beispiele](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) in diesem Abschnitt auf GitHub anzeigen.
