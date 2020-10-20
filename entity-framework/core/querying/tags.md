---
title: Abfragetags – EF Core
description: Verwenden von Abfragetags zum Identifizieren bestimmter Abfragen in von Entity Framework Core ausgegebenen Protokollmeldungen
author: smitpatel
ms.date: 11/14/2018
uid: core/querying/tags
ms.openlocfilehash: f7cd3558682b1c19e03fc6d04957c7112e870734
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065731"
---
# <a name="query-tags"></a>Abfragetags

Abfragetags ermöglichen das Korrelieren zwischen LINQ-Abfragen im Code und generierten SQL-Abfragen, die in Protokollen erfasst werden.
Sie können eine LINQ-Abfrage mithilfe der neuen `TagWith()`-Methode kommentieren:

> [!TIP]
> Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags) finden Sie auf GitHub.

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

Diese LINQ-Abfrage wird in die folgende SQL-Anweisung übersetzt:

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

`TagWith()` kann mehrmals in der gleichen Abfrage aufgerufen werden.
Abfragetags sind kumulativ.
Betrachten Sie beispielsweise die folgenden Methoden:

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#QueryableMethods)]

Diese Abfrage

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#ChainedQueryTags)]

wird übersetzt in

```sql
-- GetNearestPeople

-- Limit

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

Es ist auch möglich, Multi-Linienzeichenfolgen als Abfragetags zu verwenden.
Beispiel:

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#MultilineQueryTag)]

Dies generiert diese SQL-Anweisung:

```sql
-- GetNearestPeople

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a>Bekannte Einschränkungen

**Abfragetags lassen sich nicht parametrisieren.** EF Core behandelt Abfragetags in LINQ-Abfragen immer als Zeichenfolgenliterale, die in der generierten SQL-Anweisung enthalten sind.
Kompilierte Abfragen, die Abfragetags als Parameter verwenden, sind nicht zulässig.
