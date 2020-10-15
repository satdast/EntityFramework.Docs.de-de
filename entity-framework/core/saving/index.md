---
title: Speichern von Daten – EF Core
description: Übersicht über das Speichern von Daten mit Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/index
ms.openlocfilehash: 7a31f92aff20f4026b984065e53a2dcc77ed46e7
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063542"
---
# <a name="saving-data"></a><span data-ttu-id="809d6-103">Speichern von Daten</span><span class="sxs-lookup"><span data-stu-id="809d6-103">Saving Data</span></span>

<span data-ttu-id="809d6-104">Jede Kontextinstanz verfügt über ein `ChangeTracker`-Objekt, das für die Nachverfolgung von in die Datenbank zu schreibenden Änderungen zuständig ist.</span><span class="sxs-lookup"><span data-stu-id="809d6-104">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="809d6-105">Wenn Sie Änderungen an Instanzen Ihrer Entitätsklassen vornehmen, werden diese Änderungen in `ChangeTracker` erfasst und dann beim Aufrufen von `SaveChanges` in die Datenbank geschrieben.</span><span class="sxs-lookup"><span data-stu-id="809d6-105">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="809d6-106">Der Datenbankanbieter ist für die Übersetzung der Änderungen in datenbankspezifische Vorgänge zuständig (z.B. die Befehle `INSERT`, `UPDATE` und `DELETE` für eine relationale Datenbank).</span><span class="sxs-lookup"><span data-stu-id="809d6-106">The database provider is responsible for translating the changes into database-specific operations (for example, `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>
