---
title: Zurücksetzen von ObjectContext in Entity Framework Designer-EF6
description: Zurücksetzen von ObjectContext in Entity Framework Designer in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/codegen/legacy-objectcontext
ms.openlocfilehash: 563c1defd12a72836a158d9c2398202c421dd6f3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064938"
---
# <a name="reverting-to-objectcontext-in-entity-framework-designer"></a>Zurücksetzen auf ObjectContext in Entity Framework Designer
Mit einer früheren Version von Entity Framework ein mit dem EF-Designer erstelltes Modell einen Kontext generieren, der von ObjectContext und Entitäts Klassen abgeleitet wurde, die von EntityObject abgeleitet wurden.

Ab EF 4.1 empfehlen wir das austauschen zu einer Vorlage für die Codegenerierung, die einen Kontext generiert, der von dbcontext-und poco-Entitäts Klassen abgeleitet wird.

In Visual Studio 2012 erhalten Sie dbcontext-Code, der standardmäßig für alle neuen Modelle generiert wird, die mit dem EF-Designer erstellt wurden. Vorhandene Modelle generieren weiterhin ObjectContext-basierten Code, es sei denn, Sie entscheiden sich für den Wechsel zum dbcontext-basierten Code-Generator.

## <a name="reverting-back-to-objectcontext-code-generation"></a>Zurücksetzen auf die ObjectContext-Code Generierung

### <a name="1-disable-dbcontext-code-generation"></a>1. Deaktivieren der dbcontext-Code Generierung

Die Generierung der abgeleiteten dbcontext-und poco-Klassen wird von zwei TT-Dateien in Ihrem Projekt behandelt. Wenn Sie die EDMX-Datei im Projektmappen-Explorer erweitern, werden diese Dateien angezeigt. Löschen Sie beide Dateien aus dem Projekt.

![Code-gen-Dateien](~/ef6/media/codegenfiles.png)

Wenn Sie VB.NET verwenden, müssen Sie die Schaltfläche **alle Dateien anzeigen** auswählen, um die schaltenden Dateien anzuzeigen.

![Alle Dateien anzeigen](~/ef6/media/showallfiles.png)

### <a name="2-re-enable-objectcontext-code-generation"></a>2. Re-Enable ObjectContext-Code Generierung

Öffnen Sie das Modell im EF-Designer, klicken Sie mit der rechten Maustaste auf einen leeren Bereich der Entwurfs Oberfläche, und wählen Sie **Eigenschaften**aus.

Ändern Sie im Eigenschaftenfenster die **Code Generierungs Strategie** von **keine** in **Standard**.

![Code-gen-Strategie](~/ef6/media/codegenstrategy.png)
