---
title: In-Memory Database-Anbieter – EF Core
description: Informationen zum Anbieter für Entity Framework Core-In-Memory-Datenbanken
author: rowanmiller
ms.date: 10/27/2016
uid: core/providers/in-memory/index
ms.openlocfilehash: 9d0a5d14cdb047b80788fbe4d9d34deccdbd4ce1
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071354"
---
# <a name="ef-core-in-memory-database-provider"></a>EF Core-In-Memory Database-Anbieter

Dieser Datenbankanbieter ermöglicht, dass Entity Framework Core mit einer In-Memory Database verwendet wird. Dies kann für Tests nützlich sein, obwohl der SQLite-Anbieter im In-Memory-Modus für Testersatz für relationale Datenbanken besser geeignet sein kann. Dieser Anbieter wird nicht im Rahmen des [Entity Framework Core-Projekts](https://github.com/aspnet/EntityFrameworkCore) verwaltet.

## <a name="install"></a>Installieren

Installieren Sie das [NuGet-Paket „Microsoft.EntityFrameworkCore.InMemory“](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a>Erste Schritte

Die folgenden Ressourcen unterstützen Sie bei den ersten Schritten mit diesem Anbieter:

* [Testen mit InMemory](xref:core/miscellaneous/testing/in-memory)
* [Tests der UnicornStore-Beispielanwendung](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a>Unterstützte Datenbank-Engines

In-Process-Speicherdatenbank (nur für Testzwecke konzipiert)
