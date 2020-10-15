---
title: Laden zugehöriger Daten – EF Core
description: Verschiedene Strategien für das Laden verknüpfter Daten mit Entity Framework Core
author: roji
ms.date: 9/11/2020
uid: core/querying/related-data
ms.openlocfilehash: a86cae91dbfbf5ebcf820c6afb72d812e278dc66
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063672"
---
# <a name="loading-related-data"></a>Laden zugehöriger Daten

Mit Entity Framework Core können Sie die Navigationseigenschaften in Ihrem Modell zum Laden zugehöriger Entitäten verwenden. Es gibt drei allgemeine O/RM-Muster für das Laden zugehöriger Daten.

* **[Eager Loading](xref:core/querying/related-data/eager)** bedeutet, dass die zugehörigen Daten als Teil der ersten Abfrage aus der Datenbank geladen werden.
* **[Explizites Laden](xref:core/querying/related-data/explicit)** bedeutet, dass die zugehörigen Daten zu einem späteren Zeitpunkt explizit aus der Datenbank geladen werden.
* **[Lazy Loading](xref:core/querying/related-data/lazy)** bedeutet, dass die zugehörigen Daten bei Zugriff auf die Navigationseigenschaft transparent aus der Datenbank geladen werden.

> [!TIP]
> Sie können die [Beispiele](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RelatedData) in diesem Abschnitt auf GitHub anzeigen.
