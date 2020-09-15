---
title: 'Designer-Vorlagen für die Codegenerierung: EF6'
description: Designer-Vorlagen für die Codegenerierung in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/codegen/index
ms.openlocfilehash: 4a8225d0186174ea2e2effbc1241389afcbe3921
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070197"
---
# <a name="designer-code-generation-templates"></a>Designer-Vorlagen für die Codegenerierung
Bei der Erstellung eines Modells mit dem Entity Framework Designer werden die Klassen und der abgeleitete Kontext automatisch für Sie generiert. Zusätzlich zur Standardcodegenerierung bieten wir auch verschiedene Vorlagen, mit denen sich der generierte Code anpassen lässt. Diese Vorlagen werden als T4-Textvorlagen bereitgestellt und sind daher bei Bedarf anpassbar.

Der standardmäßig generierte Code hängt von der Visual Studio-Version ab, in der Sie das Modell erstellen:

-   In Visual Studio 2012 und 2013 erstellte Modelle generieren einfache POCO-Entitätsklassen und einen Kontext, der von DbContext (vereinfacht) abgeleitet ist.
-   In Visual Studio 2010 erstellte Modelle generieren Entitätsklassen, die von EntityObject abgeleitet werden, und einen Kontext, der von ObjectContext abgeleitet wird.
  > [!NOTE]    
  > Es wird empfohlen, zur DbContext Generator-Vorlage zu wechseln, nachdem Sie Ihr Modell hinzugefügt haben.

Auf dieser Seite finden Sie Informationen zu den verfügbaren Vorlagen und eine Anleitung zum Hinzufügen einer Vorlage zu Ihrem Modell.

## <a name="available-templates"></a>Verfügbare Vorlagen

Die folgenden Vorlagen werden vom Entity Framework-Team bereitgestellt:

### <a name="dbcontext-generator"></a>DbContext Generator

Mit dieser Vorlage werden einfache POCO-Entitätsklassen und ein Kontext generiert, der mit EF6 von DbContext abgeleitet wird.
Dies ist die empfohlene Vorlage, es sei denn, Sie haben einen Grund, eine der anderen unten aufgeführten Vorlagen zu verwenden.
Dies ist auch die Codegenerierungsvorlage, die Sie standardmäßig erhalten, wenn Sie neuere Versionen von Visual Studio verwenden (Visual Studio 2013 oder höher): Wenn Sie ein neues Modell erstellen, wird diese Vorlage standardmäßig verwendet, und die T4-Dateien (.tt) werden unter der EDMX-Datei geschachtelt.

#### <a name="older-versions-of-visual-studio"></a>Ältere Visual Studio-Versionen
- **Visual Studio 2012:** Um die **EF 6.x DbContext Generator**-Vorlagen abzurufen, müssen Sie die neuesten **Entity Framework Tools für Visual Studio** installieren. Weitere Informationen finden Sie auf der Seite [Get Entity Framework](xref:ef6/fundamentals/install) (Beziehen von Entity Framework).
- **Visual Studio 2010:** Die **EF 6.x DbContext Generator**-Vorlagen sind für Visual Studio 2010 nicht verfügbar.

#### <a name="dbcontext-generator-for-ef-5x"></a>DbContext Generator für EF 5.x

Wenn Sie eine ältere Version des EntityFramework-NuGet-Pakets verwenden (mit einer Hauptversion 5), müssen Sie die **EF 5.x DbContext Generator**-Vorlage verwenden.

Wenn Sie Visual Studio 2013 oder 2012 verwenden, ist diese Vorlage bereits installiert.

Wenn Sie Visual Studio 2010 verwenden, müssen Sie beim Hinzufügen der Vorlage auf die Registerkarte **Online** klicken, um sie aus dem Visual Studio-Katalog herunterzuladen. Alternativ können Sie die Vorlage direkt aus dem Visual Studio-Katalog vorab installieren. Da die Vorlagen in höheren Versionen von Visual Studio bereits enthalten sind, können die Versionen im Katalog nur in Visual Studio 2010 installiert werden.

- [EF 5.x DbContext Generator für C#](https://visualstudiogallery.msdn.microsoft.com/da740968-02f9-42a9-9ee4-1a9a06d896a2)
- [EF 5.x DbContext Generator für C#-Websites](https://visualstudiogallery.msdn.microsoft.com/5d01a981-91b8-492c-b42c-c771c3f31e03)
- [EF 5.x DbContext Generator für VB.NET](https://visualstudiogallery.msdn.microsoft.com/875c882d-333e-455a-8dae-5353510527dd?src=featured)
- [EF 5.x DbContext Generator für VB.NET-Websites](https://visualstudiogallery.msdn.microsoft.com/d4d7d4cd-c2d0-43e6-8944-12f6ff8f2614)

#### <a name="dbcontext-generator-for-ef-4x"></a>DbContext Generator für EF 4.x

Wenn Sie eine ältere Version des EntityFramework-NuGet-Pakets verwenden (Hauptversion 4.x), müssen Sie die **EF 4.x DbContext Generator**-Vorlage verwenden. Diese finden Sie beim Hinzufügen der Vorlage auf der Registerkarte **Online**. Alternativ können Sie die Vorlage direkt aus dem Visual Studio-Katalog vorab installieren.

- [EF 4.x DbContext Generator für C# ](https://visualstudiogallery.msdn.microsoft.com/7812b04c-db36-4817-8a84-e73c452410a2)
- [EF 4.x DbContext Generator für C#-Websites](https://visualstudiogallery.msdn.microsoft.com/de0e9bc6-e86a-4448-8a2e-a1260a53203e)
- [EF 4.x DbContext Generator für VB.NET](https://visualstudiogallery.msdn.microsoft.com/73679ae5-e358-4e76-a538-c7b5e04ac073)
- [EF 4.x DbContext Generator für VB.NET-Websites](https://visualstudiogallery.msdn.microsoft.com/86f5a660-306e-4831-840c-2e4ee7474a92)

### <a name="entityobject-generator"></a>EntityObject Generator

Mit dieser Vorlage werden Entitätsklassen generiert, die von EntityObject abgeleitet werden, und ein Kontext, der von ObjectContext abgeleitet wird.

> [!NOTE]
> Sie sollten die Verwendung von DbContext Generator in Betracht ziehen.

Der DbContext-Generator ist jetzt die empfohlene Vorlage für neue Anwendungen. Der DbContext-Generator nutzt die einfachere DbContext-API. Der EntityObject-Generator ist weiterhin für die Unterstützung vorhandener Anwendungen verfügbar.

**Visual Studio 2010, 2012 &amp; 2013**

Sie müssen beim Hinzufügen der Vorlage auf die Registerkarte **Online** klicken, um sie aus dem Visual Studio-Katalog herunterzuladen. Alternativ können Sie die Vorlage direkt aus dem Visual Studio-Katalog vorab installieren.

- [EF 6.x EntityObject Generator for C# (EF 6.x EntityObject Generator für C#)](https://visualstudiogallery.msdn.microsoft.com/66612113-549c-4a9e-a14a-f629ceb3f89a)
- [EF 6.x EntityObject Generator for C# Web Sites (EF 6.x EntityObject Generator für C#-Websites)](https://visualstudiogallery.msdn.microsoft.com/076140f3-6dbe-451f-a0e0-16b6d2bd8996)
- [EF 6.x EntityObject Generator for VB.NET (EF 6.x EntityObject Generator für VB.NET)](https://visualstudiogallery.msdn.microsoft.com/ff479d55-2c85-43c5-a4d6-21cd659435ea)
- [EF 6.x EntityObject Generator for VB.NET Web Sites (EF 6.x EntityObject Generator für VB.NET-Websites)](https://visualstudiogallery.msdn.microsoft.com/668e2b92-c142-4da2-8e60-866c6346fc6a)

**EntityObject-Generator für EF 5.x**


Wenn Sie Visual Studio 2012 oder 2013 verwenden, müssen Sie beim Hinzufügen der Vorlage auf die Registerkarte **Online** klicken, um sie aus dem Visual Studio-Katalog herunterzuladen. Alternativ können Sie die Vorlage direkt aus dem Visual Studio-Katalog vorab installieren. Da die Vorlagen in Visual Studio 2010 bereits enthalten sind, können die Versionen im Katalog nur in Visual Studio 2012 &amp; 2013 installiert werden.

- [EF 5.x EntityObject Generator for C# (EF 5.x EntityObject Generator für C#)](https://visualstudiogallery.msdn.microsoft.com/1da40393-b5ec-404a-a000-6a7e6e911339)
- [EF 5.x EntityObject Generator for C# Web Sites (EF 5.x EntityObject Generator für C#-Websites)](https://visualstudiogallery.msdn.microsoft.com/94b48556-fcf0-4b9b-8615-20f9066ae9ac)
- [EF 5.x EntityObject Generator for VB.NET (EF 5.x EntityObject Generator for VB.NET für VB.NET)](https://visualstudiogallery.msdn.microsoft.com/92c0129e-40dc-488c-a836-7e30846dfb30)
- [EF 5.x EntityObject Generator for VB.NET Web Sites (EF 5.x EntityObject Generator für VB.NET-Websites)](https://visualstudiogallery.msdn.microsoft.com/5dd7f75c-8c98-4eb7-b4bc-06f0d0b03b41)

Wenn Sie nur die ObjectContext-Codegenerierung aktivieren möchten, ohne Vorlagen bearbeiten zu müssen, können Sie die [EntityObject-Codegenerierung wiederherstellen](xref:ef6/modeling/designer/codegen/legacy-objectcontext).

Wenn Sie Visual Studio 2010 verwenden, ist diese Vorlage bereits installiert. Wenn Sie ein neues Modell in Visual Studio 2010 erstellen, wird diese Vorlage zwar standardmäßig verwendet, die TT-Dateien sind jedoch nicht in Ihrem Projekt enthalten. Wenn Sie die Vorlage anpassen möchten, müssen Sie sie Ihrem Projekt hinzufügen.

### <a name="self-tracking-entities-ste-generator"></a>Generator für Entitäten mit Selbstnachverfolgung (Self-Tracking Entities, STE)

Mit dieser Vorlage werden Entitätsklassen mit Selbstnachverfolgung und ein Kontext generiert, der von ObjectContext abgeleitet wird. In einer EF-Anwendung ist ein Kontext für das Verfolgen von Änderungen in den Entitäten zuständig. Allerdings steht der Kontext in n-schichtigen Szenarios möglicherweise nicht auf der Ebene zur Verfügung, auf der die Entitäten geändert werden. Mit Entitäten mit Selbstnachverfolgung können Sie Änderungen auf jeder Ebene nachverfolgen. Weitere Informationen finden Sie unter [Self-Tracking Entities (Entitäten mit Selbstnachverfolgung)](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index).

> [!NOTE]
> Eine STE-Vorlage wird nicht empfohlen.

Es wird nicht mehr empfohlen, die STE-Vorlage in neuen Anwendungen zu verwenden. Sie ist jedoch weiterhin verfügbar, um vorhandene Anwendungen zu unterstützen. Lesen Sie den [Artikel zu getrennten Entitäten](xref:ef6/fundamentals/disconnected-entities/index), um weitere Optionen zu n-schichtigen Szenarios zu erfahren, die empfohlen werden.

> [!NOTE]
> Es gibt keine EF 6.x-Version der STE-Vorlage.


> [!NOTE]
> Es gibt keine Visual Studio 2013-Version der STE-Vorlage.

### <a name="visual-studio-2012"></a>Visual Studio 2012

Wenn Sie Visual Studio 2012 verwenden, müssen Sie beim Hinzufügen der Vorlage auf die Registerkarte **Online** klicken, um sie aus dem Visual Studio-Katalog herunterzuladen. Alternativ können Sie die Vorlage direkt aus dem Visual Studio-Katalog vorab installieren. Da die Vorlagen in Visual Studio 2010 bereits enthalten sind, können die Versionen im Katalog nur in Visual Studio 2012 installiert werden.

- [EF 5.x STE Generator für C#](https://visualstudiogallery.msdn.microsoft.com/a3ac10a5-9365-4096-bb58-d9a1ba71db8f)
- [EF 5.x STE Generator für C#-Websites](https://visualstudiogallery.msdn.microsoft.com/1b55ab82-eeb4-47ba-8d35-3c7c8b5f5a8c)
- [EF 5.x STE Generator für VB.NET](https://visualstudiogallery.msdn.microsoft.com/1ba8c6a3-44e9-4e1f-b21e-596f3168474b)
- [EF 5.x STE Generator für VB.NET-Websites](https://visualstudiogallery.msdn.microsoft.com/a9fd5f0a-9af4-4e32-9c09-0e057072152e)

#### <a name="visual-studio-2010"></a>Visual Studio 2010**

Wenn Sie Visual Studio 2010 verwenden, ist diese Vorlage bereits installiert.

### <a name="poco-entity-generator"></a>POCO-Entitätsgenerator

Mit dieser Vorlage werden POCO-Entitätsklassen und ein Kontext generiert, der von ObjectContext abgeleitet wird.

> [!NOTE]
> Sie sollten die Verwendung von DbContext Generator in Betracht ziehen.

Der DbContext-Generator ist jetzt die empfohlene Vorlage zum Generieren von POCO-Klassen in neuen Anwendungen. Der DbContext-Generator nutzt die neue DbContext-API und kann einfachere POCO-Klassen generieren. Der POCO-Entitätsgenerator ist weiterhin für die Unterstützung vorhandener Anwendungen verfügbar.

> [!NOTE]
> Es gibt keine EF 5.x- oder EF 6.x-Version der STE-Vorlage.

> [!NOTE]
> Es gibt keine Visual Studio 2013-Version der POCO-Vorlage.

#### <a name="visual-studio-2012-amp-visual-studio-2010"></a>Visual Studio 2012 &amp; Visual Studio 2010

Sie müssen beim Hinzufügen der Vorlage auf die Registerkarte **Online** klicken, um sie aus dem Visual Studio-Katalog herunterzuladen. Alternativ können Sie die Vorlage direkt aus dem Visual Studio-Katalog vorab installieren.

- [EF 4.x POCO Generator für C#](https://visualstudiogallery.msdn.microsoft.com/23df0450-5677-4926-96cc-173d02752313)
- [EF 4.x POCO Generator für C#-Websites](https://visualstudiogallery.msdn.microsoft.com/fe568da5-aa1a-4178-a2a5-48813c707a7f)
- [EF 4.x POCO Generator für VB.NET](https://visualstudiogallery.msdn.microsoft.com/53ecbded-8936-4299-ab04-1e44e5489752)
- [EF 4.x POCO Generator für VB.NET-Websites](https://visualstudiogallery.msdn.microsoft.com/463c5aca-05ad-4cdb-910b-2e4f83269e34)

### <a name="what-are-the-web-sites-templates"></a>Was sind die „Websites“-Vorlagen?

Die „Websites“-Vorlagen, z.B. **EF 5.x DbContext Generator für C\#-Websites**, werden in Websiteprojekten verwendet, die über **Datei &gt; Neu &gt; Website...** erstellt wurden. Diese unterscheiden sich von Webanwendungen, die über **Datei &gt; Neu &gt; Projekt...** erstellt wurden und die Standardvorlagen verwenden. Wir stellen separate Vorlagen bereit, da das Elementvorlagensystem in Visual Studio diese erfordert.

## <a name="using-a-template"></a>Verwenden einer Vorlage

Um eine Vorlage für die Codegenerierung zu verwenden, klicken Sie mit der rechten Maustaste auf eine leere Stelle auf der Entwurfsoberfläche im EF Designer, und klicken Sie auf **Add Code Generation Item...** (Codegenerierungselement hinzufügen...).

![Hinzufügen eines Codegenerierungselements](~/ef6/media/add-code-gen-item.png)

Wenn Sie die Vorlage, die Sie verwenden möchten, bereits installiert haben (oder sie in Visual Studio enthalten war), ist sie entweder im Abschnitt **Code** oder im Abschnitt **Daten** im linken Menü verfügbar.

![Installierte Vorlage](~/ef6/media/installed.png)

Wenn Sie die Vorlage noch nicht installiert haben, klicken Sie im linken Menü auf **Online**, und suchen Sie nach der gewünschten Vorlage.

![Vorlage suchen](~/ef6/media/search.png) 

Wenn Sie Visual Studio 2012 verwenden, werden die neue TT-Dateien unter der EDMX-Datei geschachtelt.*

> [!NOTE]
> Bei Modellen, die in Visual Studio 2012 erstellt wurden, müssen Sie die für die Standardcodegenerierung verwendeten Vorlagen löschen. Andernfalls werden doppelte Klassen und Kontexte generiert. Die Standarddateien sind **&lt;Modellname&gt;.tt** und **&lt;Modellname&gt;.context.tt**. 

![VS2012-Vorlagen](~/ef6/media/vs2012-templates.png)

Wenn Sie Visual Studio 2010 verwenden, werden die TT-Dateien direkt zu Ihrem Projekt hinzugefügt.  

![VS2010-Vorlagen](~/ef6/media/vs2010-templates.png)
