---
title: Get Entity Framework-EF6
description: Get Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 122c38a2-f9e8-4ecc-9c72-a83bc9af7814
uid: ef6/fundamentals/install
ms.openlocfilehash: bfd016a93de73ada4487a454ec7abd9251aeecfe
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616249"
---
# <a name="get-entity-framework"></a>Abrufen von Entity Framework
Entity Framework besteht aus den EF-Tools für Visual Studio und der EF-Laufzeit.

## <a name="ef-tools-for-visual-studio"></a>EF-Tools für Visual Studio

Die Entity Framework Tools für Visual Studio umfassen den EF-Designer und den EF-Modell-Assistenten und sind für die Daten Bank First-und Model First-Workflows erforderlich. EF-Tools sind in allen neueren Versionen von Visual Studio enthalten. Wenn Sie eine benutzerdefinierte Installation von Visual Studio durchführen, müssen Sie sicherstellen, dass das Element "Entity Framework 6 Tools" ausgewählt ist. Wählen Sie hierzu entweder eine Arbeitsauslastung aus, die es enthält, oder wählen Sie es als einzelne Komponente aus.

Für einige frühere Versionen von Visual Studio stehen aktualisierte EF-Tools als Download zur Verfügung. Unter [Visual Studio-Versionen](xref:ef6/what-is-new/visual-studio) finden Sie Anleitungen zum Bereitstellen der neuesten Version von EF-Tools für Ihre Version von Visual Studio.

## <a name="ef-runtime"></a>EF-Laufzeit

Die neueste Version von Entity Framework ist als das [nuget-Paket "EntityFramework](https://nuget.org/packages/EntityFramework/)" verfügbar. Wenn Sie mit dem nuget-Paket-Manager nicht vertraut sind, empfehlen wir Ihnen, die [Übersicht über nuget](/nuget/consume-packages/overview-and-workflow)zu lesen.

### <a name="installing-the-ef-nuget-package"></a>Installieren des EF-nuget-Pakets

Sie können das Paket "EntityFramework" installieren, indem Sie mit der rechten Maustaste auf den Ordner " **Verweise** " des Projekts klicken und **nuget-Pakete verwalten auswählen.**

![NuGet-Pakete verwalten](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a>Installieren über die Paket-Manager-Konsole

Alternativ können Sie "EntityFramework" installieren, indem Sie den folgenden Befehl in der [Paket-Manager-Konsole](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)ausführen.

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a>Installieren einer bestimmten Version von EF

Ab EF 4,1 wurden neue Versionen der EF-Laufzeit als das [nuget-Paket "EntityFramework](https://www.nuget.org/packages/EntityFramework/)" veröffentlicht. Diese Versionen können einem .NET Framework basierten Projekt hinzugefügt werden, indem Sie den folgenden Befehl in der Paket-Manager- [Konsole](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)von Visual Studio ausführen:

``` powershell
Install-Package EntityFramework -Version <number>
```

Beachten Sie, dass `<number>` die bestimmte zu installierenden EF-Version darstellt. Beispielsweise ist 6.2.0 die Version der Zahl für EF 6,2.   

EF-Laufzeiten vor 4,1 waren Teil .NET Framework und können nicht separat installiert werden.

### <a name="installing-the-latest-preview"></a>Installieren der aktuellen Vorschauversion

Die oben genannten Methoden geben Ihnen die neueste vollständig unterstützte Version von Entity Framework. Häufig sind vorab Versionen von Entity Framework verfügbar, die wir gerne ausprobieren und uns Feedback geben würden.

Um die neueste Vorschau von EntityFramework zu installieren, können Sie im Fenster nuget-Pakete verwalten die Option **Vorabversion einschließen** auswählen. Wenn keine vorab Versionen verfügbar sind, erhalten Sie automatisch die neueste vollständig unterstützte Version von Entity Framework.

![Vorabversion einschließen](~/ef6/media/includeprerelease.png)

Alternativ können Sie den folgenden Befehl in der Paket- [Manager-Konsole](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)ausführen.

``` powershell
Install-Package EntityFramework -Pre
```
