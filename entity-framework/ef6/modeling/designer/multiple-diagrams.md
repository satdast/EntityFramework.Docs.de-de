---
title: Mehrere Diagramme pro Modell EF6
description: Mehrere Diagramme pro Modell in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: b95db5c8-de8d-43bd-9ccc-5df6a5e25e1b
uid: ef6/modeling/designer/multiple-diagrams
ms.openlocfilehash: 5ecda72af6430222864c62aea24cf85893436f56
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620433"
---
# <a name="multiple-diagrams-per-model"></a>Mehrere Diagramme pro Modell
> [!NOTE]
> **Nur EF5** : die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 5 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.

In diesem Video und dieser Seite wird gezeigt, wie Sie ein Modell mit dem Entity Framework Designer (EF-Designer) in mehrere Diagramme aufteilen. Diese Funktion kann verwendet werden, wenn das Modell zum Anzeigen oder bearbeiten zu groß wird.

In früheren Versionen von EF Designer konnte nur ein Diagramm pro EDMX-Datei vorhanden sein. Ab Visual Studio 2012 können Sie den EF-Designer verwenden, um die EDMX-Datei in mehrere Diagramme aufzuteilen.

## <a name="watch-the-video"></a>Video ansehen
In diesem Video wird gezeigt, wie Sie ein Modell mit dem Entity Framework Designer (EF-Designer) in mehrere Diagramme aufteilen. Diese Funktion kann verwendet werden, wenn das Modell zum Anzeigen oder bearbeiten zu groß wird.

**Präsentiert von**: Julia kornich

**Video**: [WMV](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv)  |  [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)

## <a name="ef-designer-overview"></a>Übersicht über EF-Designer

Wenn Sie ein Modell mithilfe des Entity Data Model-Assistenten des EF-Designers erstellen, wird eine EDMX-Datei erstellt und der Projekt Mappe hinzugefügt. Diese Datei definiert die Form ihrer Entitäten und wie Sie der Datenbank zugeordnet werden.

Der EF-Designer besteht aus den folgenden Komponenten:

-   Eine visuelle Entwurfs Oberfläche zum Bearbeiten des Modells. Entitäten und Zuordnungen können erstellt, geändert oder gelöscht werden.
-   Ein **Modell Browser**   Fenster, das Struktur Ansichten des Modells bereitstellt.Die Entitäten und ihre Zuordnungen befinden sich im Ordner " * \[ modelname \] * ". Die Datenbanktabellen und-Einschränkungen befinden sich unter dem * \[ modelname \] *. Speicherordner.
-   Ein Fenster **Mappingdetails** zum Anzeigen und Bearbeiten von Zuordnungen. Entitätstypen oder Zuordnungen können Datenbanktabellen, Spalten und gespeicherten Prozeduren zugeordnet werden. 

Das Fenster visuelle Entwurfs Oberfläche wird automatisch geöffnet, wenn der Entity Data Model-Assistent abgeschlossen ist. Wenn der Modell Browser nicht angezeigt wird, klicken Sie mit der rechten Maustaste auf die Haupt Entwurfs Oberfläche, und wählen Sie **Modell Browser**aus.

Der folgende Screenshot zeigt eine EDMX-Datei, die im EF-Designer geöffnet ist. Der Screenshot zeigt die visuelle Entwurfs Oberfläche (auf der linken Seite) und das Fenster " **Modell Browser**"   (auf der rechten Seite).

![EF-Designer 2](~/ef6/media/efdesigner2.png)

Um einen im EF-Designer ausgeführten Vorgang rückgängig zu machen, klicken Sie auf STRG + Z.

## <a name="working-with-diagrams"></a>Arbeiten mit Diagrammen

Standardmäßig erstellt der EF-Designer ein Diagramm mit dem Namen Diagramm1. Wenn Sie über ein Diagramm mit einer großen Anzahl von Entitäten und Zuordnungen verfügen, sollten Sie diese logisch aufteilen. Ab Visual Studio 2012 können Sie das konzeptionelle Modell in mehreren Diagrammen anzeigen.   

Wenn Sie neue Diagramme hinzufügen, werden diese im Fenster "Modell Browser" unter dem Ordner "Diagramme" angezeigt. So benennen Sie ein Diagramm um: Wählen Sie das Diagramm im Fenstermodell Browser aus, klicken Sie auf einmal für den Namen, und geben Sie den neuen Namen ein. Sie können auch mit der rechten Maustaste auf den Diagramm Namen klicken und **Umbenennen**auswählen.

Der Diagramm Name wird neben dem Namen der EDMX-Datei im Visual Studio-Editor angezeigt. Beispielsweise Model1. edmx \[ Diagramm1 \] .

![Diagramm Name](~/ef6/media/diagramname.png)

Der Inhalt der Diagramme (Form und Farbe von Entitäten und Zuordnungen) wird in der edmx. Diagram-Datei gespeichert. Um diese Datei anzuzeigen, wählen Sie Projektmappen-Explorer aus, und erweitern Sie die EDMX-Datei. 

![Diagramm Dateien](~/ef6/media/diagramfiles.png)

Sie sollten die EDMX. Diagram-Datei nicht manuell bearbeiten. der Inhalt dieser Datei wird möglicherweise vom EF-Designer überschrieben.
 
## <a name="splitting-entities-and-associations-into-a-new-diagram"></a>Aufteilen von Entitäten und Zuordnungen in ein neues Diagramm

Sie können Entitäten im vorhandenen Diagramm auswählen (halten Sie die UMSCHALTTASTE gedrückt, um mehrere Entitäten auszuwählen). Klicken Sie mit der rechten Maustaste, und wählen Sie **in neues Diagramm verschieben**aus. Das neue Diagramm wird erstellt, und die ausgewählten Entitäten und ihre Zuordnungen werden in das Diagramm verschoben.

Alternativ dazu können Sie im Modell Browser mit der rechten Maustaste auf den Ordner Diagramme klicken und **Neues Diagramm hinzufügen auswählen.** Anschließend können Sie Entitäten aus dem Ordner Entitäts Typen im Modell Browser auf die Entwurfs Oberfläche ziehen und ablegen.

Sie können Entitäten auch Ausschneiden oder kopieren (mit STRG + X oder STRG + C) aus einem Diagramm und Einfügen (mit STRG + V-Taste) in der anderen. Wenn das Diagramm, in das Sie eine Entität einfügen, bereits eine Entität mit demselben Namen enthält, wird eine neue Entität erstellt und dem Modell hinzugefügt.Beispiel: diagram2 enthält die Abteilungs Entität. Anschließend fügen Sie eine weitere Abteilung in diagram2 ein. Die Department1-Entität wird erstellt und dem konzeptionellen Modell hinzugefügt.   

Wenn Sie Verwandte Entitäten in ein Diagramm einschließen möchten, klicken Sie auf die Entität, und wählen Sie **Verwandte einschließen**aus. Dadurch wird eine Kopie der verknüpften Entitäten und Zuordnungen im angegebenen Diagramm erstellt.

## <a name="changing-the-color-of-entities"></a>Ändern der Farbe von Entitäten

Zusätzlich zum Aufteilen eines Modells in mehrere Diagramme können Sie auch die Farben ihrer Entitäten ändern.

Um die Farbe zu ändern, wählen Sie eine Entität (oder mehrere Entitäten) auf der Entwurfs Oberfläche aus. Klicken Sie dann auf die Rechte Maustaste, und wählen Sie **Eigenschaften**aus. Wählen Sie im Eigenschaftenfenster die Eigenschaft **Füllfarbe** aus. Geben Sie die Farbe entweder mithilfe eines gültigen Farbnamens (z. b. rot) oder eines gültigen RGB (z. b. 255, 128, 128) an. 

![Farbe ändern](~/ef6/media/color.png)

## <a name="summary"></a>Zusammenfassung

In diesem Thema wurde erläutert, wie Sie ein Modell in mehrere Diagramme aufteilen und wie Sie eine andere Farbe für eine Entität mithilfe der Entity Framework Designer angeben. 
