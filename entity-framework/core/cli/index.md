---
title: Entity Framework Core Tools-Referenz – EF Core
description: Referenzleitfaden zur Entity Framework Core-CLI und zur Paket-Manager-Konsole in Visual Studio
author: bricelam
ms.date: 09/19/2018
uid: core/cli/index
ms.openlocfilehash: 1ffc773cb8ed30516d682b90bbd9accef634ae6a
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635379"
---
# <a name="entity-framework-core-tools-reference"></a>Entity Framework Core Tools-Referenz

Die Entity Framework Core Tools helfen zur Entwurfszeit bei Entwicklungsaufgaben. Diese werden hauptsächlich verwendet, um Migrationen zu verwalten und ein Gerüst für `DbContext` und Entitätstypen durch Reverse Engineering eines Datenbankschemas zu erstellen.

Beide folgenden Tools können installiert werden, da beide dieselbe Funktionalität bieten:

* Die [EF Core Paket-Manager-Konsolentools](xref:core/cli/powershell) werden in der [Paket-Manager-Konsole](/nuget/tools/package-manager-console) in Visual Studio ausgeführt. Diese Tools werden für die Entwicklung in Visual Studio empfohlen, da sie eine integriertere Oberfläche bieten.

* Die [EF Core .NET-Befehlszeilentools (CLI-Tools)](xref:core/cli/dotnet) stellen eine Erweiterung der plattformübergreifenden [.NET Core CLI-Tools](/dotnet/core/tools/) dar. Für diese Tools ist ein .NET Core-SDK-Projekt (mit `Sdk="Microsoft.NET.Sdk"` oder einem ähnlichen SDK in der Projektdatei) erforderlich.

## <a name="next-steps"></a>Nächste Schritte

* [EF Core-Paket-Manager-Konsolentools – Referenz](xref:core/cli/powershell)
* [EF Core .NET CLI-Tools – Referenz](xref:core/cli/dotnet)
