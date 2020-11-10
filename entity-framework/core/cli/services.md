---
title: Entwurfszeit Dienste-EF Core
description: Informationen zu Entity Framework Core Entwurfszeit Diensten
author: bricelam
ms.date: 10/22/2020
uid: core/cli/services
ms.openlocfilehash: 07fc8a39fb106f357e135a17f6867ab338621910
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431260"
---
# <a name="design-time-services"></a>Entwurfszeitdienste

Einige Dienste, die von den-Tools verwendet werden, werden nur zur Entwurfszeit verwendet. Diese Dienste werden getrennt von den Lauf Zeit Diensten EF Core verwaltet, um zu verhindern, dass Sie mit Ihrer APP bereitgestellt werden. Um einen dieser Dienste (z. b. den-Dienst zum Generieren von Migrations Dateien) zu überschreiben, fügen Sie dem Startprojekt eine Implementierung von hinzu `IDesignTimeServices` .

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs#DesignTimeServices)]

## <a name="referencing-microsoftentityframeworkcoredesign"></a>Verweisen auf "Microsoft. entityframeworkcore. Design"

Microsoft. entityframeworkcore. Design ist ein developmentdepenket-Paket. Dies bedeutet, dass die Abhängigkeit nicht transitiv in andere Projekte fließt und dass Sie standardmäßig nicht auf die Typen verweisen können.

Um auf die Typen zu verweisen und Entwurfszeit Dienste zu überschreiben, aktualisieren Sie die Metadaten des packagereferenzierungselements in der Projektdatei.

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.9">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

Wenn auf das Paket transitiv über Microsoft. entityframeworkcore. Tools verwiesen wird, müssen Sie dem Paket eine explizite packagereferenzierung hinzufügen und die zugehörigen Metadaten ändern.

## <a name="list-of-services"></a>Liste der Dienste

Im folgenden finden Sie eine Liste der Entwurfszeit Dienste.

Dienst                                                                              | BESCHREIBUNG
------------------------------------------------------------------------------------ | -----------
<xref:Microsoft.EntityFrameworkCore.Design.IAnnotationCodeGenerator>                 | Generiert den Code für entsprechende Modell Anmerkungen.
<xref:Microsoft.EntityFrameworkCore.Design.ICSharpHelper>                            | Unterstützt die Erstellung von c#-Code.
<xref:Microsoft.EntityFrameworkCore.Design.IPluralizer>                              | Pluralisiert und singularisiert Wörter.
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsCodeGenerator>      | Generiert Code für eine Migration.
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsScaffolder>         | Die Hauptklasse für die Verwaltung von Migrations Dateien.
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IDatabaseModelFactory>               | Erstellt ein Datenbankmodell aus einer Datenbank.
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IModelCodeGenerator>                 | Generiert Code für ein Modell.
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IProviderConfigurationCodeGenerator> | Generiert den onkonfigurierungscode.
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IReverseEngineerScaffolder>          | Die Hauptklasse für das Gerüstbau von rückwärts entwickelten Modellen.

## <a name="using-services"></a>Verwenden von Diensten

Diese Dienste können auch zum Erstellen eigener Tools nützlich sein. Beispielsweise, wenn Sie einen Teil des Entwurfszeit Workflows automatisieren möchten.

Mithilfe der Erweiterungs Methoden addentityframeworkdesigntimeservices und adddbcontextdesigntimeservices können Sie einen Dienstanbieter erstellen, der diese Dienste enthält.

[!code-csharp[](../../../samples/core/Miscellaneous/CommandLine/CustomTools.cs#CustomTools)]
