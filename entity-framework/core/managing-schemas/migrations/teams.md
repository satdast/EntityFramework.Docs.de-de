---
title: Migrationen in Team Umgebungen-EF Core
description: Bewährte Methoden zum Verwalten von Migrationen und Auflösen von Konflikten in Team Umgebungen mit Entity Framework Core
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/teams
ms.openlocfilehash: 90549b369624301bc183e5a8a3cc1d6a92bb7008
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062502"
---
# <a name="migrations-in-team-environments"></a><span data-ttu-id="9add6-103">Migrationen in Teamumgebungen</span><span class="sxs-lookup"><span data-stu-id="9add6-103">Migrations in Team Environments</span></span>

<span data-ttu-id="9add6-104">Wenn Sie mit Migrationen in Team Umgebungen arbeiten, achten Sie besonders auf die Modell Momentaufnahme-Datei.</span><span class="sxs-lookup"><span data-stu-id="9add6-104">When working with Migrations in team environments, pay extra attention to the model snapshot file.</span></span> <span data-ttu-id="9add6-105">Diese Datei kann Ihnen mitteilen, ob die Migration Ihres Teamkollegen ordnungsgemäß mit Ihrem zusammengeführt wird oder ob Sie einen Konflikt auflösen müssen, indem Sie die Migration neu erstellen, bevor Sie Sie freigeben.</span><span class="sxs-lookup"><span data-stu-id="9add6-105">This file can tell you if your teammate's migration merges cleanly with yours or if you need to resolve a conflict by re-creating your migration before sharing it.</span></span>

## <a name="merging"></a><span data-ttu-id="9add6-106">Zusammenführen</span><span class="sxs-lookup"><span data-stu-id="9add6-106">Merging</span></span>

<span data-ttu-id="9add6-107">Wenn Sie Migrationen von ihren Teamkollegen zusammenführen, treten möglicherweise Konflikte in der Modell Momentaufnahme-Datei auf.</span><span class="sxs-lookup"><span data-stu-id="9add6-107">When you merge migrations from your teammates, you may get conflicts in your model snapshot file.</span></span> <span data-ttu-id="9add6-108">Wenn beide Änderungen nicht miteinander verbunden sind, ist die Zusammenführung trivial, und die beiden Migrationen können nebeneinander bestehen.</span><span class="sxs-lookup"><span data-stu-id="9add6-108">If both changes are unrelated, the merge is trivial and the two migrations can coexist.</span></span> <span data-ttu-id="9add6-109">Beispielsweise erhalten Sie möglicherweise einen Mergekonflikt in der Customer-Entitätstyp Konfiguration, der wie folgt aussieht:</span><span class="sxs-lookup"><span data-stu-id="9add6-109">For example, you may get a merge conflict in the customer entity type configuration that looks like this:</span></span>

```output
<<<<<<< Mine
b.Property<bool>("Deactivated");
=======
b.Property<int>("LoyaltyPoints");
>>>>>>> Theirs
```

<span data-ttu-id="9add6-110">Da diese beiden Eigenschaften im endgültigen Modell vorhanden sein müssen, müssen Sie die Zusammenführung durch Hinzufügen beider Eigenschaften vervollständigen.</span><span class="sxs-lookup"><span data-stu-id="9add6-110">Since both of these properties need to exist in the final model, complete the merge by adding both properties.</span></span> <span data-ttu-id="9add6-111">In vielen Fällen kann Ihr Versionskontrollsystem solche Änderungen automatisch zusammenführen.</span><span class="sxs-lookup"><span data-stu-id="9add6-111">In many cases, your version control system may automatically merge such changes for you.</span></span>

```csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

<span data-ttu-id="9add6-112">In diesen Fällen sind die Migration und die Migration Ihres Teamkollegen voneinander unabhängig.</span><span class="sxs-lookup"><span data-stu-id="9add6-112">In these cases, your migration and your teammate's migration are independent of each other.</span></span> <span data-ttu-id="9add6-113">Da eine der beiden Optionen zuerst angewendet werden kann, müssen Sie keine weiteren Änderungen an der Migration vornehmen, bevor Sie Sie für Ihr Team freigeben.</span><span class="sxs-lookup"><span data-stu-id="9add6-113">Since either of them could be applied first, you don't need to make any additional changes to your migration before sharing it with your team.</span></span>

## <a name="resolving-conflicts"></a><span data-ttu-id="9add6-114">Beheben von Konflikten</span><span class="sxs-lookup"><span data-stu-id="9add6-114">Resolving conflicts</span></span>

<span data-ttu-id="9add6-115">Manchmal tritt beim Zusammenführen des Modell Momentaufnahme-Modells ein echter Konflikt auf.</span><span class="sxs-lookup"><span data-stu-id="9add6-115">Sometimes you encounter a true conflict when merging the model snapshot model.</span></span> <span data-ttu-id="9add6-116">Beispielsweise können Sie und Ihr Teamkollegen die gleiche Eigenschaft umbenennen.</span><span class="sxs-lookup"><span data-stu-id="9add6-116">For example, you and your teammate may each have renamed the same property.</span></span>

```output
<<<<<<< Mine
b.Property<string>("Username");
=======
b.Property<string>("Alias");
>>>>>>> Theirs
```

<span data-ttu-id="9add6-117">Wenn diese Art von Konflikt auftritt, beheben Sie diese, indem Sie die Migration neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9add6-117">If you encounter this kind of conflict, resolve it by re-creating your migration.</span></span> <span data-ttu-id="9add6-118">Folgen Sie diesen Schritten:</span><span class="sxs-lookup"><span data-stu-id="9add6-118">Follow these steps:</span></span>

1. <span data-ttu-id="9add6-119">Abbrechen des Merge und Zurücksetzen auf das Arbeitsverzeichnis vor dem Merge</span><span class="sxs-lookup"><span data-stu-id="9add6-119">Abort the merge and rollback to your working directory before the merge</span></span>
2. <span data-ttu-id="9add6-120">Entfernen der Migration (behalten Sie jedoch Ihre Modelländerungen bei)</span><span class="sxs-lookup"><span data-stu-id="9add6-120">Remove your migration (but keep your model changes)</span></span>
3. <span data-ttu-id="9add6-121">Zusammenführen der Änderungen Ihres Teamkollegen in Ihrem Arbeitsverzeichnis</span><span class="sxs-lookup"><span data-stu-id="9add6-121">Merge your teammate's changes into your working directory</span></span>
4. <span data-ttu-id="9add6-122">Erneutes Hinzufügen der Migration</span><span class="sxs-lookup"><span data-stu-id="9add6-122">Re-add your migration</span></span>

<span data-ttu-id="9add6-123">Anschließend können die beiden Migrationen in der richtigen Reihenfolge angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="9add6-123">After doing this, the two migrations can be applied in the correct order.</span></span> <span data-ttu-id="9add6-124">Die Migration wird zuerst angewendet, und die Spalte wird in *Alias*umbenannt. Anschließend wird Sie von der Migration in *username*umbenannt.</span><span class="sxs-lookup"><span data-stu-id="9add6-124">Their migration is applied first, renaming the column to *Alias*, thereafter your migration renames it to *Username*.</span></span>

<span data-ttu-id="9add6-125">Ihre Migration kann für den Rest des Teams sicher freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9add6-125">Your migration can safely be shared with the rest of the team.</span></span>
