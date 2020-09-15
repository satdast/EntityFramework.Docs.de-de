---
title: Abfragetags – EF Core
description: Verwenden von Abfragetags zum Identifizieren bestimmter Abfragen in von Entity Framework Core ausgegebenen Protokollmeldungen
author: divega
ms.date: 11/14/2018
ms.assetid: 73C7A627-C8E9-452D-9CD5-AFCC8FEFE395
uid: core/querying/tags
ms.openlocfilehash: bf80057d29160bd5ef6ae26ee6d447fc3c3f0fec
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617485"
---
# <a name="query-tags"></a>Abfragetags

> [!NOTE]
> Dieses Feature ist neu in EF Core 2.2.

Diese Funktion ermöglicht das Korrelieren zwischen LINQ-Abfragen im Code und generierten SQL-Abfragen, die in Protokollen erfasst werden.
Sie können eine LINQ-Abfrage mithilfe der neuen `TagWith()`-Methode kommentieren:

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith("This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

Diese LINQ-Abfrage wird in die folgende SQL-Anweisung übersetzt:

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

`TagWith()` kann mehrmals in der gleichen Abfrage aufgerufen werden.
Abfragetags sind kumulativ.
Betrachten Sie beispielsweise die folgenden Methoden:

``` csharp
IQueryable<Friend> GetNearestFriends(Point myLocation) =>
    from f in context.Friends.TagWith("GetNearestFriends")
    orderby f.Location.Distance(myLocation) descending
    select f;

IQueryable<T> Limit<T>(IQueryable<T> source, int limit) =>
    source.TagWith("Limit").Take(limit);
```

Diese Abfrage

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).ToList();
```

wird übersetzt in

``` sql
-- GetNearestFriends

-- Limit

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

Es ist auch möglich, Multi-Linienzeichenfolgen als Abfragetags zu verwenden.
Beispiel:

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).TagWith(
@"This is a multi-line
string").ToList();
```

Dies generiert diese SQL-Anweisung:

``` sql
-- GetNearestFriends

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a>Bekannte Einschränkungen

**Abfragetags lassen sich nicht parametrisieren.** EF Core behandelt Abfragetags in LINQ-Abfragen immer als Zeichenfolgenliterale, die in der generierten SQL-Anweisung enthalten sind.
Kompilierte Abfragen, die Abfragetags als Parameter verwenden, sind nicht zulässig.
