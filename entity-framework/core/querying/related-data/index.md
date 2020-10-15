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
# <a name="loading-related-data"></a><span data-ttu-id="ec966-103">Laden zugehöriger Daten</span><span class="sxs-lookup"><span data-stu-id="ec966-103">Loading Related Data</span></span>

<span data-ttu-id="ec966-104">Mit Entity Framework Core können Sie die Navigationseigenschaften in Ihrem Modell zum Laden zugehöriger Entitäten verwenden.</span><span class="sxs-lookup"><span data-stu-id="ec966-104">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="ec966-105">Es gibt drei allgemeine O/RM-Muster für das Laden zugehöriger Daten.</span><span class="sxs-lookup"><span data-stu-id="ec966-105">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="ec966-106">**[Eager Loading](xref:core/querying/related-data/eager)** bedeutet, dass die zugehörigen Daten als Teil der ersten Abfrage aus der Datenbank geladen werden.</span><span class="sxs-lookup"><span data-stu-id="ec966-106">**[Eager loading](xref:core/querying/related-data/eager)** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="ec966-107">**[Explizites Laden](xref:core/querying/related-data/explicit)** bedeutet, dass die zugehörigen Daten zu einem späteren Zeitpunkt explizit aus der Datenbank geladen werden.</span><span class="sxs-lookup"><span data-stu-id="ec966-107">**[Explicit loading](xref:core/querying/related-data/explicit)** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="ec966-108">**[Lazy Loading](xref:core/querying/related-data/lazy)** bedeutet, dass die zugehörigen Daten bei Zugriff auf die Navigationseigenschaft transparent aus der Datenbank geladen werden.</span><span class="sxs-lookup"><span data-stu-id="ec966-108">**[Lazy loading](xref:core/querying/related-data/lazy)** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]
> <span data-ttu-id="ec966-109">Sie können die [Beispiele](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RelatedData) in diesem Abschnitt auf GitHub anzeigen.</span><span class="sxs-lookup"><span data-stu-id="ec966-109">You can view the [samples](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RelatedData) under this section on GitHub.</span></span>
