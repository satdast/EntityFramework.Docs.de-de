---
title: Entity Framework Core Tools-Referenz – EF Core
description: Referenzleitfaden zur Entity Framework Core-CLI und zur Paket-Manager-Konsole in Visual Studio
author: bricelam
ms.date: 09/19/2018
uid: core/miscellaneous/cli/index
ms.openlocfilehash: 90e8d799de8fa9426de6ac3d286bcd5458a43f0d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061995"
---
# <a name="entity-framework-core-tools-reference"></a>Entity Framework Core Tools-Referenz

Die Entity Framework Core Tools helfen zur Entwurfszeit bei Entwicklungsaufgaben. Diese werden hauptsächlich verwendet, um Migrationen zu verwalten und ein Gerüst für `DbContext` und Entitätstypen durch Reverse Engineering eines Datenbankschemas zu erstellen.

* Die [EF Core Paket-Manager-Konsolentools](xref:core/miscellaneous/cli/powershell) werden in der [Paket-Manager-Konsole](/nuget/tools/package-manager-console) in Visual Studio ausgeführt.

* Die [EF Core .NET-Befehlszeilentools (CLI-Tools)](xref:core/miscellaneous/cli/dotnet) stellen eine Erweiterung der plattformübergreifenden [.NET Core CLI-Tools](/dotnet/core/tools/) dar. Für diese Tools ist ein .NET Core-SDK-Projekt (mit `Sdk="Microsoft.NET.Sdk"` oder einem ähnlichen SDK in der Projektdatei) erforderlich.

Beide Tools bieten dieselbe Funktionalität. Für die Entwicklung in Visual Studio empfehlen wir die Verwendung von **Paket-Manager-Konsolentools**, da diese ein besseres ganzheitliches Erlebnis bieten.

## <a name="next-steps"></a>Nächste Schritte

* [EF Core-Paket-Manager-Konsolentools – Referenz](xref:core/miscellaneous/cli/powershell)
* [EF Core .NET CLI-Tools – Referenz](xref:core/miscellaneous/cli/dotnet)
