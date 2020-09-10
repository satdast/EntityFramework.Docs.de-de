---
title: Migrationen in Team Umgebungen-EF Core
description: Bewährte Methoden zum Verwalten von Migrationen und Auflösen von Konflikten in Team Umgebungen mit Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/teams
ms.openlocfilehash: 1fbb7173a52218a4d00780ebc76e33600f3558c1
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619204"
---
# <a name="migrations-in-team-environments"></a>Migrationen in Teamumgebungen

Wenn Sie mit Migrationen in Team Umgebungen arbeiten, achten Sie besonders auf die Modell Momentaufnahme-Datei. Diese Datei kann Ihnen mitteilen, ob die Migration Ihres Teamkollegen ordnungsgemäß mit Ihrem zusammengeführt wird oder ob Sie einen Konflikt auflösen müssen, indem Sie die Migration neu erstellen, bevor Sie Sie freigeben.

## <a name="merging"></a>Zusammenführen

Wenn Sie Migrationen von ihren Teamkollegen zusammenführen, treten möglicherweise Konflikte in der Modell Momentaufnahme-Datei auf. Wenn beide Änderungen nicht miteinander verbunden sind, ist die Zusammenführung trivial, und die beiden Migrationen können nebeneinander bestehen. Beispielsweise erhalten Sie möglicherweise einen Mergekonflikt in der Customer-Entitätstyp Konfiguration, der wie folgt aussieht:

``` output
<<<<<<< Mine
b.Property<bool>("Deactivated");
=======
b.Property<int>("LoyaltyPoints");
>>>>>>> Theirs
```

Da diese beiden Eigenschaften im endgültigen Modell vorhanden sein müssen, müssen Sie die Zusammenführung durch Hinzufügen beider Eigenschaften vervollständigen. In vielen Fällen kann Ihr Versionskontrollsystem solche Änderungen automatisch zusammenführen.

``` csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

In diesen Fällen sind die Migration und die Migration Ihres Teamkollegen voneinander unabhängig. Da eine der beiden Optionen zuerst angewendet werden kann, müssen Sie keine weiteren Änderungen an der Migration vornehmen, bevor Sie Sie für Ihr Team freigeben.

## <a name="resolving-conflicts"></a>Beheben von Konflikten

Manchmal tritt beim Zusammenführen des Modell Momentaufnahme-Modells ein echter Konflikt auf. Beispielsweise können Sie und Ihr Teamkollegen die gleiche Eigenschaft umbenennen.

``` output
<<<<<<< Mine
b.Property<string>("Username");
=======
b.Property<string>("Alias");
>>>>>>> Theirs
```

Wenn diese Art von Konflikt auftritt, beheben Sie diese, indem Sie die Migration neu erstellen. Folgen Sie diesen Schritten:

1. Abbrechen des Merge und Zurücksetzen auf das Arbeitsverzeichnis vor dem Merge
2. Entfernen der Migration (behalten Sie jedoch Ihre Modelländerungen bei)
3. Zusammenführen der Änderungen Ihres Teamkollegen in Ihrem Arbeitsverzeichnis
4. Erneutes Hinzufügen der Migration

Anschließend können die beiden Migrationen in der richtigen Reihenfolge angewendet werden. Die Migration wird zuerst angewendet, und die Spalte wird in *Alias*umbenannt. Anschließend wird Sie von der Migration in *username*umbenannt.

Ihre Migration kann für den Rest des Teams sicher freigegeben werden.
