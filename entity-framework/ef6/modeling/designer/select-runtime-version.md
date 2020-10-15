---
title: Auswählen Entity Framework Lauf Zeit Version für EF-Designer-Modelle EF6
description: Auswählen Entity Framework Lauf Zeit Version für EF-Designer-Modelle in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
ms.openlocfilehash: dcd06ed9585028b3a318d867b03980254979a32f
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066095"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a>Auswählen Entity Framework Lauf Zeit Version für EF-Designer-Modelle
> [!NOTE]
> **Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.

Beginnend mit EF6 wurde dem EF-Designer der folgende Bildschirm hinzugefügt, damit Sie die Version der Laufzeit auswählen können, die Sie beim Erstellen eines Modells als Ziel auswählen möchten. Der Bildschirm wird angezeigt, wenn die neueste Version von Entity Framework nicht bereits im Projekt installiert ist. Wenn die neueste Version bereits installiert ist, wird Sie standardmäßig verwendet.

![Lauf Zeit Version auswählen](~/ef6/media/screen.png)

## <a name="targeting-ef6x"></a>Ziel EF6. x

Sie können EF6 auf dem Bildschirm "wählen Sie Ihre Version" auswählen, um dem Projekt die EF6-Laufzeit hinzuzufügen. Nachdem Sie EF6 hinzugefügt haben, wird dieser Bildschirm im aktuellen Projekt nicht mehr angezeigt.

EF6 wird deaktiviert, wenn Sie bereits eine ältere Version von EF installiert haben (da Sie nicht mehrere Versionen der Laufzeit aus demselben Projekt ausrichten können). Wenn die EF6-Option hier nicht aktiviert ist, führen Sie die folgenden Schritte aus, um Ihr Projekt auf EF6 zu aktualisieren:

1.  Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **nuget-Pakete verwalten aus.**
2.  **Updates** auswählen
3.  Wählen Sie **EntityFramework** aus (stellen Sie sicher, dass es auf die gewünschte Version aktualisiert wird).
4.  Klicken Sie auf **Aktualisieren**.

 

## <a name="targeting-ef5x"></a>Ziel EF5. x

Sie können EF5 auf dem Bildschirm "wählen Sie Ihre Version" auswählen, um dem Projekt die EF5-Laufzeit hinzuzufügen. Nachdem Sie EF5 hinzugefügt haben, wird der Bildschirm mit deaktivierter EF6-Option angezeigt.

Wenn Sie eine EF4. x-Version der Laufzeit bereits installiert haben, sehen Sie, dass die EF-Version auf dem Bildschirm anstelle von EF5 aufgeführt wird. In dieser Situation können Sie ein Upgrade auf EF5 ausführen, indem Sie die folgenden Schritte ausführen:

1.  **Tools auswählen- &gt; Bibliothekspaket-Manager- &gt; Paket-Manager-Konsole**
2.  Ausführen von " **install-Package" EntityFramework-Version 5.0.0**

 

## <a name="targeting-ef4x"></a>Ziel EF4. x

Mithilfe der folgenden Schritte können Sie die EF4. x-Laufzeit für Ihr Projekt installieren:

1.  **Tools auswählen- &gt; Bibliothekspaket-Manager- &gt; Paket-Manager-Konsole**
2.  Ausführen von " **install-Package" EntityFramework-Version 4.3.0**
