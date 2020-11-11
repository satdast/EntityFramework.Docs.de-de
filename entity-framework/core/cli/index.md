---
title: Entity Framework Core Tools-Referenz – EF Core
description: Referenzleitfaden zur Entity Framework Core-CLI und zur Paket-Manager-Konsole in Visual Studio
author: bricelam
ms.date: 09/19/2018
uid: core/cli/index
ms.openlocfilehash: 9a6ae8f945e92453ddfaa089ae1d606d142f725a
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431123"
---
# <a name="entity-framework-core-tools-reference"></a>Entity Framework Core Tools-Referenz

Die Entity Framework Core Tools helfen zur Entwurfszeit bei Entwicklungsaufgaben. Diese werden hauptsächlich verwendet, um Migrationen zu verwalten und ein Gerüst für `DbContext` und Entitätstypen durch Reverse Engineering eines Datenbankschemas zu erstellen.

* Die [EF Core Paket-Manager-Konsolentools](xref:core/cli/powershell) werden in der [Paket-Manager-Konsole](/nuget/tools/package-manager-console) in Visual Studio ausgeführt.

* Die [EF Core .NET-Befehlszeilentools (CLI-Tools)](xref:core/cli/dotnet) stellen eine Erweiterung der plattformübergreifenden [.NET Core CLI-Tools](/dotnet/core/tools/) dar. Für diese Tools ist ein .NET Core-SDK-Projekt (mit `Sdk="Microsoft.NET.Sdk"` oder einem ähnlichen SDK in der Projektdatei) erforderlich.

Beide Tools bieten dieselbe Funktionalität. Für die Entwicklung in Visual Studio empfehlen wir die Verwendung von **Paket-Manager-Konsolentools** , da diese ein besseres ganzheitliches Erlebnis bieten.

## <a name="next-steps"></a>Nächste Schritte

* [EF Core-Paket-Manager-Konsolentools – Referenz](xref:core/cli/powershell)
* [EF Core .NET CLI-Tools – Referenz](xref:core/cli/dotnet)
