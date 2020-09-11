---
title: 'Erstellen eines Modells: EF6'
description: Erstellen eines Modells in Entity Framework 6
author: divega
ms.date: 07/05/2018
ms.assetid: 4890228E-CEA1-4595-B8AD-CA81253F8767
uid: ef6/modeling/index
ms.openlocfilehash: 417671d5313bb95bb97d5de85b25b09d687c5c3d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620338"
---
# <a name="creating-a-model"></a>Erstellen eines Modells

Ein EF-Modell speichert die Details zur Zuordnung von Anwendungsklassen und -eigenschaften zu Datenbanktabellen und-spalten. Es gibt zwei Hauptmöglichkeiten, ein EF-Modell zu erstellen:

- **Verwenden von Code First:** Der Entwickler schreibt Code, um ein Modell zu entwerfen. EF generiert die Modelle und Zuordnungen zur Laufzeit basierend auf Entitätsklassen und zusätzlichen Modellkonfigurationen, die vom Entwickler bereitgestellt werden.

- **Verwenden des EF-Designers:** Der Entwickler zeichnet Felder und Linien, um das Modell mit dem EF-Designer zu entwerfen. Das daraus entstehende Modell wird als XML in einer Datei mit der Erweiterung EDMX gespeichert. Die Domänenobjekte der Anwendung werden normalerweise automatisch über das konzeptionelle Modell erzeugt.

## <a name="ef-workflows"></a>EF-Workflows

Beide Ansätze können für die Ausrichtung auf eine vorhandene Datenbank oder die Erstellung einer neuen Datenbank verwendet werden, sodass es vier verschiedene Workflows gibt.
Entscheiden Sie sich für das Modell, das für Sie am geeignetsten ist:  

|                                           | Ich möchte Code schreiben...                                                                                                                   | Ich möchte einen Designer verwenden...                                                                                                                        |
|:------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------|
| **Ich erstelle eine neue Datenbank**          | [Verwenden Sie **Code First**, um Ihr Modell mit Code zu definieren und dann eine Datenbank zu erstellen.](xref:ef6/modeling/code-first/workflows/new-database)           | [Verwenden Sie **Model First**, um Ihr Modell mit Feldern und Linien zu definieren und dann eine Datenbank zu erstellen.](xref:ef6/modeling/designer/workflows/model-first)   |
| **Ich muss auch eine vorhandene Daten zugreifen** | [Verwenden Sie **Code First**, um ein codebasiertes Modell zu erstellen, das eine Zuordnung zu einer bereits vorhandenen Datenbank herstellt.](xref:ef6/modeling/code-first/workflows/existing-database) | [Verwenden Sie **Database First**, um Felder und Linien zu erstellen, die eine Zuordnung zu einer bereits vorhandenen Datenbank herstellen.](xref:ef6/modeling/designer/workflows/database-first) |

### <a name="watch-the-video-what-ef-workflow-should-i-use"></a>Sehen Sie sich ein Video an: Welcher EF-Workflow eignet sich am besten für mich?

In diesem kurzen Video werden die Unterschiede beschrieben, und Sie erfahren, wie Sie sich für den passenden Workflow entscheiden.

**Präsentation:**[Rowan Miller](https://romiller.com/)

![Which Workflow Thumb](../media/whichworkflow-thumb.png) [WMV](https://download.microsoft.com/download/8/F/8/8F81F4CD-3678-4229-8D79-0C63FFA3C595/HDI_ITPro_Technet_winvideo_ChoseYourWorkflow.wmv) | [MP4](https://download.microsoft.com/download/8/F/8/8F81F4CD-3678-4229-8D79-0C63FFA3C595/HDI_ITPro_Technet_mp4video_ChoseYourWorkflow.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/8/8F81F4CD-3678-4229-8D79-0C63FFA3C595/HDI_ITPro_Technet_winvideo_ChoseYourWorkflow.zip)

Wenn Sie sich das Video angesehen haben und sich dennoch nicht entscheiden können, ob Sie den EF-Designer oder Code First verwenden möchten, können Sie auch einfach beides lernen.

## <a name="a-look-under-the-hood"></a>Einblick in die Hintergründe

Egal, ob Sie Code First oder den EF-Designer verwenden, besteht ein EF-Modell immer aus mehreren Komponenten:

- Die Domänenobjekte der Anwendung oder die Entitätstypen der Anwendung. Dies wird häufig als Objektebene bezeichnet.

- Ein konzeptionelles Modell besteht aus domänenspezifischen Entitätstypen und -beziehungen, die mit [Entity Data Model](xref:ef6/resources/glossary#entity-data-model) beschrieben werden. Diese Ebene wird häufig als Ebene C (Englisch: _Conceptual_) bezeichnet.

- Ein Speichermodell, das Tabellen, Spalten und Beziehungen darstellt, wie in der Datenbank definiert. Diese Ebene wird häufig als Ebene S (Englisch: _Storage_) bezeichnet.  

- Eine Zuordnung zwischen dem konzeptionellen Modell und dem Datenbankschema. Diese Zuordnung wird häufig als C-S-Zuordnung bezeichnet.

Die EF-Zuordnungs-Engine nutzt die C-S-Zuordnung zum Transformieren von Vorgängen mit Entitäten (z.B. das Erstellen, Lesen, Aktualisieren und Löschen) in entsprechende Vorgänge mit Tabellen in Datenbanken.

Die Zuordnung zwischen dem konzeptionellen Modell und den Objekten der Anwendung wird häufig als O-C-Zuordnung bezeichnet. Im Gegensatz zur C-S-Zuordnung erfolgt die O-C-Zuordnung implizit und 1:1. Entitäten, Eigenschaften und Beziehungen, die im konzeptionellen Modell definiert wurden, müssen mit den Formen und Typen der .NET-Objekte übereinstimmen. Ab EF 4 kann die Objektebene aus einfachen Objekten mit Eigenschaften bestehen, ohne dass Abhängigkeiten von EF vorhanden sind. Diese werden oft als POCOs (Plain Old CLR Objects) bezeichnet, und die Zuordnung von Typen und Eigenschaften erfolgt basierend auf Konventionen bezüglich der Übereinstimmung von Namen. In EF 3.5 gab es spezifische Einschränkungen der Objektebene: Entitäten mussten von der Klasse EntityObject abgeleitet werden und EF-Attribute aufweisen, damit die O-C-Zuordnung implementiert werden konnte.
