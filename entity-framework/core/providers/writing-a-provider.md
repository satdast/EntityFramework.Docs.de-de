---
title: Schreiben eines Datenbankanbieters-EF Core
description: Informationen zum Schreiben eines neuen Entity Framework Core Anbieters
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/writing-a-provider
ms.openlocfilehash: e66c5b94d826e35bb5148d57897a1081de4e9736
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128419"
---
# <a name="writing-a-database-provider"></a>Schreiben eines Datenbankanbieters

Weitere Informationen zum Schreiben eines Entity Framework Core Datenbankanbieters finden [Sie unter so können Sie einen EF Core Anbieter](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) von [Arthur Vickers](https://github.com/ajcvickers)schreiben.

> [!NOTE]
> Diese Beiträge wurden seit EF Core 1,1 nicht aktualisiert, und seit dieser Zeit wurden bedeutende Änderungen vorgenommen.
[Problem 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) ist das Nachverfolgen von Aktualisierungen für diese Dokumentation.

Die EF Core Codebasis ist Open Source und enthält mehrere Datenbankanbieter, die als Referenz verwendet werden können. Den Quellcode finden Sie unter <https://github.com/dotnet/efcore> . Es kann auch hilfreich sein, sich den Code für häufig verwendete Drittanbieter Anbieter anzusehen, wie z. b. [npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)und [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact). Vor allem werden diese Projekte so eingerichtet, dass Sie von und in nuget veröffentlichte Funktionstests erweitern und ausführen können. Diese Art von Setup wird dringend empfohlen.

## <a name="keeping-up-to-date-with-provider-changes"></a>Beibehalten der aktuellen Änderungen an Anbietern

Beginnend mit der Arbeit nach Version 2,1 haben wir ein [Protokoll der Änderungen](xref:core/providers/provider-log) erstellt, die ggf. entsprechende Änderungen im Anbieter Code erfordern. Dies soll beim Aktualisieren eines vorhandenen Anbieters helfen, um mit einer neuen Version von EF Core Arbeiten zu können.

Vor 2,1 haben wir die [`providers-beware`](https://github.com/dotnet/efcore/labels/providers-beware) [`providers-fyi`](https://github.com/dotnet/efcore/labels/providers-fyi) Bezeichnungen und in unseren GitHub-Problemen und Pull Requests für einen ähnlichen Zweck verwendet. Wir werden diese Bezeichnungen für Probleme verwenden, um anzugeben, welche Arbeitsaufgaben in einem bestimmten Release möglicherweise auch die Arbeit in Anbietern erfordern. Eine `providers-beware` Bezeichnung bedeutet in der Regel, dass die Implementierung eines Arbeits Elements die Anbieter unterbrechen kann, während eine `providers-fyi` Bezeichnung in der Regel bedeutet, dass die Anbieter nicht beschädigt werden, der Code jedoch möglicherweise trotzdem geändert werden muss, z. b., um neue Funktionen zu aktivieren.

## <a name="suggested-naming-of-third-party-providers"></a>Empfohlene Benennung von Drittanbieter Anbietern

Wir empfehlen, die folgenden Namen für nuget-Pakete zu verwenden. Dies stimmt mit den Namen von Paketen überein, die vom EF Core Team geliefert werden.

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

Beispiel:

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
