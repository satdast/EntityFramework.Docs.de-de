---
title: Speichern von Daten – EF Core
description: Übersicht über das Speichern von Daten mit Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: core/saving/index
ms.openlocfilehash: 98a23fd4956a8646e9760f9ecb0b48166ad6caa1
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072472"
---
# <a name="saving-data"></a><span data-ttu-id="d9960-103">Speichern von Daten</span><span class="sxs-lookup"><span data-stu-id="d9960-103">Saving Data</span></span>

<span data-ttu-id="d9960-104">Jede Kontextinstanz verfügt über ein `ChangeTracker`-Objekt, das für die Nachverfolgung von in die Datenbank zu schreibenden Änderungen zuständig ist.</span><span class="sxs-lookup"><span data-stu-id="d9960-104">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="d9960-105">Wenn Sie Änderungen an Instanzen Ihrer Entitätsklassen vornehmen, werden diese Änderungen in `ChangeTracker` erfasst und dann beim Aufrufen von `SaveChanges` in die Datenbank geschrieben.</span><span class="sxs-lookup"><span data-stu-id="d9960-105">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="d9960-106">Der Datenbankanbieter ist für die Übersetzung der Änderungen in datenbankspezifische Vorgänge zuständig (z.B. die Befehle `INSERT`, `UPDATE` und `DELETE` für eine relationale Datenbank).</span><span class="sxs-lookup"><span data-stu-id="d9960-106">The database provider is responsible for translating the changes into database-specific operations (for example, `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>
