---
title: Neuerungen – EF6
description: Neues in Entity Framework 6
author: ajcvickers
ms.date: 09/12/2019
uid: ef6/what-is-new/index
ms.openlocfilehash: 3c588475268ea063433f55fdb2f994d70f8c51ae
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064301"
---
# <a name="whats-new-in-ef6"></a>Neuerungen in EF6

Es wird dringend empfohlen, dass Sie die neueste veröffentlichte Version von Entity Framework verwenden. So wird sichergestellt, dass Sie die neuesten Features nutzen und die höchste Stabilität erhalten.
Uns ist jedoch klar, dass Sie möglicherweise eine frühere Version verwenden müssen oder die neuen Verbesserungen für die aktuelle Vorabversion ausprobieren möchten.
Wie Sie bestimmte Versionen von EF installieren, erfahren Sie unter [Get Entity Framework (Beziehen von Entity Framework)](xref:ef6/fundamentals/install).

## <a name="ef-640"></a>EF 6.4.0

Die EF 6.4.0-Runtime wurde im Dezember 2019 für NuGet veröffentlicht. Das Hauptziel von EF 6.4 ist es, die Features und Szenarios von EF 6.3 zu optimieren. Auf GitHub finden Sie eine [Liste wichtiger Fixes](https://github.com/dotnet/ef6/milestone/14?closed=1).

## <a name="ef-630"></a>EF 6.3.0

Die EF 6.3.0-Runtime wurde im September 2019 für NuGet veröffentlicht. Das Hauptziel dieses Release bestand darin, die Migration vorhandener Anwendungen, die EF 6 verwenden, zu . NET Core 3.0 zu ermöglichen. Die Community hat außerdem verschiedene Fehlerbehebungen und Verbesserungen beigetragen. Einzelheiten zu den gelösten Problemen finden Sie in den [Meilensteinen](https://github.com/aspnet/EntityFramework6/milestones?state=closed) zu Version 6.3.0. In Folgenden werden einige der wichtigsten aufgeführt:

- Unterstützung für .NET Core 3.0
  - Das EntityFramework-Paket verwendet nun .NET Standard 2.1 zusätzlich zu .NET Framework 4.x als Ziel.
  - Dies bedeutet, dass EF 6.3 plattformübergreifend ist und auf anderen Betriebssystemen als Windows unterstützt wird, wie z. B. Linux und macOS.
  - Die Migrationsbefehle wurden so umgeschrieben, dass Sie außerhalb des Prozesses ausgeführt werden und mit Projekten im SDK-Stil funktionieren.
- Unterstützung für HierarchyId von SQL Server.
- Verbesserte Kompatibilität mit PackageReference von Roslyn und NuGet.
- Das `ef6.exe`-Dienstprogramm wurde zum Aktivieren, Hinzufügen, Skripten und Anwenden von Migrationen aus Assemblys hinzugefügt. Hierdurch wird `migrate.exe` ersetzt.

### <a name="ef-designer-support"></a>Unterstützung des EF-Designers

Es gibt derzeit keine Unterstützung für die direkte Verwendung des EF-Designers in .NET Core- oder .NET Standard-Projekten oder für SDK-ähnliche .NET Framework-Projekte. 

Sie können diese Einschränkung umgehen, indem Sie die EDMX-Datei und die generierten Klassen für die Entitäten und DbContext als verknüpfte Dateien zu einem .NET Core 3.0- oder .NET Standard 2.1-Projekt in derselben Projektmappe hinzufügen.

Die verknüpften Dateien sehen in der Projektdatei wie folgt aus:

``` csproj 
<ItemGroup>
  <EntityDeploy Include="..\EdmxDesignHost\Entities.edmx" Link="Model\Entities.edmx" />
  <Compile Include="..\EdmxDesignHost\Entities.Context.cs" Link="Model\Entities.Context.cs" />
  <Compile Include="..\EdmxDesignHost\Thing.cs" Link="Model\Thing.cs" />
  <Compile Include="..\EdmxDesignHost\Person.cs" Link="Model\Person.cs" />
</ItemGroup>
```

Beachten Sie, dass die EDMX-Datei mit dem EntityDeploy-Buildvorgang verknüpft ist. Dabei handelt es sich um eine spezielle MSBuild-Aufgabe (die jetzt im EF 6.3-Paket enthalten ist), mit der das EF-Modell in der Zielassembly als eingebettete Ressourcen hinzugefügt wird (oder als Dateien in den Ausgabeordner kopiert wird, abhängig von der Einstellung für die Verarbeitung von Metadatenartefakten in der EDMX-Datei). Weitere Informationen zur Einrichtung finden Sie im [EDMX-Beispiel zu .NET Core](https://aka.ms/EdmxDotNetCoreSample).

Warnung: Achten Sie darauf, dass das alte .NET Framework-Projekt (d. h. nicht SDK-ähnlich) die „echte“ EDMX-Datei definiert, _bevor_ das Projekt den Link innerhalb der SLN-Datei definiert. Andernfalls wird beim Öffnen der EDMX-Datei im Designer die Fehlermeldung „Das Entity Framework ist im derzeit für dieses Projekt angegebenen Zielframework nicht verfügbar. Sie können das Zielframework des Projekts ändern oder das Modell in XmlEditor bearbeiten.“ angezeigt.

## <a name="past-releases"></a>Frühere Releases

Auf der Seite [Frühere Releases](xref:ef6/what-is-new/past-releases) finden Sie ein Archiv aller früheren Versionen von EF und die wichtigsten Features, die in den einzelnen Releases eingeführt wurden.
