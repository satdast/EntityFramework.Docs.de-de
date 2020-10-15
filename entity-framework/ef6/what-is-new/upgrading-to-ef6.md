---
title: Upgrade auf Entity Framework 6 EF6
description: Upgrade auf Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/what-is-new/upgrading-to-ef6
ms.openlocfilehash: 14cd0e259832377332f77a199502699d75982bb4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064249"
---
# <a name="upgrading-to-entity-framework-6"></a>Upgrade auf Entity Framework 6

In früheren Versionen von EF wurde der Code zwischen Kernbibliotheken (Primär System.Data.Entity.dll) aufgeteilt, die als Teil der .NET Framework und Out-of-Band-Bibliotheken (OOB) ausgeliefert wurden (primär EntityFramework.dll), die in einem nuget-Paket ausgeliefert wurden. EF6 übernimmt den Code aus den Kernbibliotheken und bindet ihn in die OOB-Bibliotheken ein. Dies war notwendig, damit EF als Open Source-fähig gemacht werden kann, damit es in der Lage ist, sich von .NET Framework in einem anderen Tempo zu entwickeln. Dies hat zur Folge, dass Anwendungen für die verschoten Typen neu erstellt werden müssen.

Dies sollte für Anwendungen, die dbcontext verwenden, als in EF 4,1 und höher ausgeliefert werden, einfach sein. Ein wenig mehr Arbeit ist für Anwendungen erforderlich, die ObjectContext verwenden, aber es ist immer noch nicht schwierig.

Hier finden Sie eine Prüfliste der Dinge, die Sie zum Aktualisieren einer vorhandenen Anwendung auf EF6 benötigen.

## <a name="1-install-the-ef6-nuget-package"></a>1. Installieren Sie das nuget-Paket EF6.

Sie müssen ein Upgrade auf die neue Laufzeit von Entity Framework 6 durchführen.

1. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **nuget-Pakete verwalten aus.**  
2. Wählen **Sie auf der** Registerkarte " **Online** " **EntityFramework** aus, und klicken  
   > [!NOTE]
   > Wenn eine frühere Version des nuget-Pakets von EntityFramework installiert wurde, wird Sie auf EF6 aktualisiert.

Alternativ können Sie den folgenden Befehl über die Paket-Manager-Konsole ausführen:

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a>2. Stellen Sie sicher, dass Assemblyverweise auf System.Data.Entity.dll entfernt werden

Wenn Sie das nuget-Paket "EF6" installieren, werden automatisch alle Verweise auf "System. Data. Entity" aus dem Projekt entfernt.

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a>3. Austauschen von EF Designer-Modellen (edmx) zur Verwendung der EF 6. x-Codegenerierung

Wenn Sie Modelle mit dem EF-Designer erstellt haben, müssen Sie die Code Generierungs Vorlagen aktualisieren, um EF6 kompatiblen Code zu generieren.

> [!NOTE]
> Zurzeit sind nur EF 6. x dbcontext Generator-Vorlagen für Visual Studio 2012 und 2013 verfügbar.

1. Löschen vorhandener Code Generierungs Vorlagen. Diese Dateien heißen in der Regel ** \<edmx_file_name\> . tt** und ** \<edmx_file_name\> . Context.tt** und werden unter der EDMX-Datei in Projektmappen-Explorer. Sie können die Vorlagen in Projektmappen-Explorer auswählen und die ENTF **-Taste drücken** , um Sie zu löschen.  
   > [!NOTE]
   > In Website Projekten werden die Vorlagen nicht in der EDMX-Datei, sondern in Projektmappen-Explorer aufgeführt.  

   > [!NOTE]
   > In VB.NET-Projekten müssen Sie "alle Dateien anzeigen" aktivieren, damit die Dateien der Dateien in der Liste angezeigt werden können.
2. Fügen Sie die entsprechende EF 6. x-Code Generierungs Vorlage hinzu. Öffnen Sie das Modell im EF-Designer, klicken Sie mit der rechten Maustaste auf die Entwurfs Oberfläche, und wählen Sie **Code Generierungs Element hinzufügen aus.**
    - Wenn Sie die dbcontext-API verwenden (empfohlen), ist der **dbcontext-Generator von EF 6. x** auf der Registerkarte " **Daten** " verfügbar.  
      > [!NOTE]
      > Wenn Sie Visual Studio 2012 verwenden, müssen Sie die EF 6-Tools installieren, um diese Vorlage zu verwenden. Weitere Informationen finden [Sie unter Get Entity Framework](xref:ef6/fundamentals/install) .  

    - Wenn Sie die ObjectContext-API verwenden, müssen Sie die Registerkarte **Online** auswählen und nach **EF 6. x EntityObject Generator**suchen.  
3. Wenn Sie Anpassungen auf die Code Generierungs Vorlagen angewendet haben, müssen Sie Sie erneut auf die aktualisierten Vorlagen anwenden.

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a>4. Aktualisieren von Namespaces für alle verwendeten Core EF-Typen

Die Namespaces für dbcontext und Code First Typen wurden nicht geändert. Dies bedeutet, dass für viele Anwendungen, die EF 4,1 oder höher verwenden, nichts geändert werden muss.

Typen wie ObjectContext, die zuvor in System.Data.Entity.dll waren, wurden in neue Namespaces verschoben. Dies bedeutet, dass Sie ggf. Ihre *using* -oder *Import* -Direktiven aktualisieren müssen, um für EF6 zu erstellen

Die allgemeine Regel für Änderungen am Namespace besteht darin, dass alle Typen in System. Data. * in System. Data. Entity. Core. * verschoben werden. Mit anderen Worten: Fügen Sie einfach **Entity. Core ein.** nach "System. Data". Beispiel:

- System. Data. EntityException => System. Data. **Entity. Core**. EntityException  
- System. Data. Objects. ObjectContext => System. Data. **Entity. Core**. Objects. ObjectContext  
- System. Data. Objects. DataClasses. RelationshipManager => System. Data. **Entity. Core**. Objects. DataClasses. RelationshipManager  

Diese Typen befinden sich in *Core* den Kernnamespaces, da Sie für die meisten dbcontext-basierten Anwendungen nicht direkt verwendet werden. Einige Typen, die Teil System.Data.Entity.dll waren, werden weiterhin häufig und direkt für dbcontext-basierte Anwendungen verwendet und daher nicht in die *Kernnamespaces* verschoben. Dies sind:

- System. Data. EntityState => System. Data. **Entität**. EntityState  
- System. Data. Objects. DataClasses. EdmFunctionAttribute => System. Data. **Entity. dbfunctionattribute**  
  > [!NOTE]
  > Diese Klasse wurde umbenannt. eine Klasse mit dem alten Namen ist noch vorhanden und funktioniert, ist aber jetzt als veraltet markiert.  
- System. Data. Objects. EntityFunctions => System. Data. **Entity. dbfunctions**  
  > [!NOTE]
  > Diese Klasse wurde umbenannt. eine Klasse mit dem alten Namen ist noch vorhanden und funktioniert, ist aber jetzt als veraltet markiert.)  
- Räumliche Klassen (z. b. dbgeography, dbgeometry) wurden aus System. Data. Spatial => System. Data verschoben. **Entität**. Räumliche

> [!NOTE]
> Einige Typen im System. Data-Namespace befinden sich in System.Data.dll, bei dem es sich nicht um eine EF-Assembly handelt. Diese Typen wurden nicht verschoben, sodass Ihre Namespaces unverändert bleiben.
