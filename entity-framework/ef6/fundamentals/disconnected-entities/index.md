---
title: 'Arbeiten mit getrennten Entitäten: EF6'
description: Arbeiten mit getrennten Entitäten in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 12138003-a373-4817-b1b7-724130202f5f
uid: ef6/fundamentals/disconnected-entities/index
ms.openlocfilehash: 5465ba3c800e4f55d1f787cc3d3e22b55e9a68b0
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616120"
---
# <a name="working-with-disconnected-entities"></a>Arbeiten mit getrennten Entitäten

In einer Anwendung, die auf Entity Framework basiert, ist eine Kontextklasse für das Erfassen von Änderungen zuständig, die an nachverfolgten Entitäten vorgenommen werden. Durch Aufruf der SaveChanges-Methode werden vom Kontext nachverfolgte Änderungen in der Datenbank gespeichert. Bei der Arbeit mit n-schichtigen Anwendungen werden Entitätsobjekte normalerweise modifiziert, wenn keine Verbindung zum Kontext besteht. Sie müssen dann entscheiden, wie Änderungen nachverfolgt und an den Kontext zurückgemeldet werden. In diesem Artikel wird beschrieben, welche Möglichkeiten Sie haben, wenn Sie Entity Framework mit getrennten Entitäten verwenden.

## <a name="web-service-frameworks"></a>Webdienstframeworks

Webdiensttechnologien unterstützen normalerweise Muster, mit denen Sie Änderungen an einzelnen getrennten Objekten übernehmen können. Mit der ASP.NET-Web-API können Sie z.B. Controlleraktionen (wie etwa Aufrufe an EF zum Übernehmen von Änderungen an Objekten in einer Datenbank) codieren. Mit den Tools der Web-API in Visual Studio können Sie leicht einen Web-API-Controller über das Entity Framework 6-Modell entwerfen. Weitere Informationen finden Sie unter [Using Web API 2 with Entity Framework 6 (Verwenden der Web-API mit Entity Framework 6)](/aspnet/web-api/overview/data/using-web-api-with-entity-framework/).

Es gab schon immer mehrere andere Webdiensttechnologien, die eine Integration in Entity Framework ermöglicht haben, wie z.B. [WCF Data Services](/dotnet/framework/data/wcf/create-a-data-service-using-an-adonet-ef-data-wcf) und [RIA Services](/previous-versions/dotnet/wcf-ria/ee707344(v=vs.91)).

## <a name="low-level-ef-apis"></a>EF-APIs auf niedriger Ebene

Wenn Sie keine bereits vorhandene n-schichtige Lösung verwenden möchten oder wenn Sie Controlleraktionen in einem Web-API-Dienst anpassen möchten, stellt Entity Framework auch APIs bereit, mit denen Sie Änderungen übernehmen können, die an einer getrennten Schicht vorgenommen wurden. Weitere Informationen finden Sie im Artikel zum [Hinzufügen, Anhängen und zu Entitätszuständen](xref:ef6/saving/change-tracking/entity-state).  

## <a name="self-tracking-entities"></a>Entitäten mit Selbstnachverfolgung  

Das Nachverfolgen von Änderungen an beliebigen Entitätsdiagrammen, die vom EF-Kontext getrennt sind, ist ein schwierig zu lösendes Problem. Eine Möglichkeit zur Lösung ist die Codegenerierungsvorlage für Entitäten mit Selbstnachverfolgung. Diese Vorlage generiert Entitätsklassen, die Logik zum Nachverfolgen von an getrennten Schichten vorgenommenen Änderungen als Zustände in den Entitäten selbst enthält. Außerdem werden mehrere Erweiterungsmethoden generiert, die diese Änderungen auf einen Kontext anwenden.

Diese Vorlage kann nur mit Modellen verwendet werden, die mit dem EF-Designer erstellt wurden, und nicht mit Code First-Modellen. Weitere Informationen finden Sie unter [Self-Tracking Entities (Entitäten mit Selbstnachverfolgung)](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index).  

> [!IMPORTANT]
> Es wird nicht mehr empfohlen, die Vorlage für Entitäten mit Selbstnachverfolgung zu verwenden. Die Vorlage ist nur für die Unterstützung vorhandener Anwendungen weiterhin verfügbar. Wenn für Ihre Anwendung die Arbeit mit getrennten Diagrammen von Entitäten erforderlich ist, sollten Sie daher Alternativen erwägen, wie z.B. [nachverfolgbare Entitäten](https://trackableentities.github.io/). Diese Technologie ähnelt den Entitäten mit Selbstnachverfolgung und wird von der Community aktiver entwickelt. Alternativ dazu können Sie auch benutzerdefinierten Code mithilfe von APIs auf niedriger Ebene zur Änderungsnachverfolgung schreiben.
