---
title: Vergleiche mit NULL-Werten in Abfragen
description: Hier erfahren Sie, wie Entity Framework Core NULL-Vergleiche in Abfragen verarbeitet.
author: maumar
ms.date: 11/11/2020
uid: core/querying/null-comparisons
ms.openlocfilehash: fc63d0e0e6aea09e46b1700152312d4b74270219
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983351"
---
# <a name="query-null-semantics"></a>NULL-Semantik in Abfragen

## <a name="introduction"></a>Einführung

Im Gegensatz zur booleschen Logik von C# arbeiten SQL-Datenbanken bei Vergleichen mit einer dreiwertigen Logik (`true`, `false`, `null`). Beim Übersetzen von LINQ-Abfragen in SQL versucht EF Core, den Unterschied zu kompensieren, indem zusätzliche NULL-Überprüfungen für einige Elemente der Abfrage eingeführt werden.
Zur Veranschaulichung wird die folgende Entität definiert:

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsEntity.cs#Entity)]

Außerdem werden mehrere Abfragen ausgegeben:

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#BasicExamples)]

Die ersten beiden Abfragen erzeugen einfache Vergleiche. In der ersten Abfrage sind beide Spalten Non-Nullable, sodass keine NULL-Überprüfungen erforderlich sind. In der zweiten Abfrage kann `NullableInt` den Wert `null` enthalten. `Id` ist jedoch Non-Nullable. Ein Vergleich von `null` mit Nicht-NULL ergibt `null`, wobei das Ergebnis durch den `WHERE`-Vorgang herausgefiltert wird. Es sind also auch keine zusätzlichen Bedingungen erforderlich.

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[Int]

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[NullableInt]
```

Die dritte Abfrage führt eine NULL-Überprüfung ein. Wenn `NullableInt` `null` entspricht, ergibt der Vergleich `Id <> NullableInt` `null`, wobei das Ergebnis durch den `WHERE`-Vorgang herausgefiltert wird. Aus der Perspektive der booleschen Logik sollte dieser Fall jedoch als Teil des Ergebnisses zurückgegeben werden. Daher wird in EF Core die erforderliche Überprüfung hinzugefügt, um dies sicherzustellen.

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[Id] <> [e].[NullableInt]) OR [e].[NullableInt] IS NULL
```

Die vierte und fünfte Abfrage weisen ein Muster auf, in dem beide Spalten Nullwerte zulassen. Beachten Sie, dass der `<>`-Vorgang eine kompliziertere (und potenziell langsamere) Abfrage erzeugt als der `==`-Vorgang.

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] = [e].[String2]) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL)

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE (([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)
```

## <a name="treatment-of-nullable-values-in-functions"></a>Verarbeitung von Nullwerte zulassenden Variablen in Funktionen

Viele Funktionen in SQL können nur dann ein `null`-Ergebnis zurückgeben, wenn einige ihrer Argumente `null` sind. EF Core nutzt dies, um effizientere Abfragen zu erzeugen.
Die folgende Abfrage veranschaulicht die Optimierung:

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#Functions)]

Der generierte SQL-Code lautet wie folgt (die `SUBSTRING`-Funktion muss nicht ausgewertet werden, da sie nur NULL ist, wenn eines ihrer Argumente NULL lautet):

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[String1] IS NULL OR [e].[String2] IS NULL
```

Die Optimierung kann auch für benutzerdefinierte Funktionen verwendet werden. Weitere Informationen finden Sie unter [Zuordnung von benutzerdefinierten Funktionen](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments).

## <a name="writing-performant-queries"></a>Schreiben von leistungsstarken Abfragen

- Das Vergleichen von Non-Nullable-Spalten ist einfacher und schneller als das Vergleichen von Nullwerte zulassenden Spalten. Markieren Sie Spalten, wenn möglich, immer als Non-Nullable.

- Die Überprüfung auf Gleichheit (`==`) ist einfacher und schneller als die Überprüfung auf Ungleichheit (`!=`), da die Abfrage nicht zwischen `null`- und `false`-Ergebnissen unterscheiden muss. Verwenden Sie nach Möglichkeit den Gleichheitsvergleich. Den Vergleich `==` einfach zu negieren ist jedoch identisch mit `!=` und führt nicht zu einer Leistungsverbesserung.

- In einigen Fällen ist es möglich, einen komplexen Vergleich zu vereinfachen, indem `null`-Werte explizit aus einer Spalte herausgefiltert werden, z. B. wenn keine `null`-Werte vorhanden oder diese im Ergebnis nicht relevant sind. Betrachten Sie das folgende Beispiel:

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#ManualOptimization)]

Diese Abfragen erzeugen den folgenden SQL-Code:

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ((([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)) OR ((CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL))

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] IS NOT NULL AND [e].[String2] IS NOT NULL) AND (([e].[String1] <> [e].[String2]) OR (CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)))
```

In der zweiten Abfrage werden `null`-Ergebnisse explizit aus der Spalte `String1` herausgefiltert. EF Core kann die Spalte `String1` beim Vergleich gefahrlos als Non-Nullable behandeln, was zu einer einfacheren Abfrage führt.

## <a name="using-relational-null-semantics"></a>Verwenden der relationalen NULL-Semantik

Es ist möglich, die Kompensierung von NULL-Vergleichen zu deaktivieren und direkt eine relationale NULL-Semantik zu verwenden. Dazu muss die Methode `UseRelationalNulls(true)` für OptionsBuilder innerhalb Methode `OnConfiguring` aufgerufen werden:

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsContext.cs#UseRelationalNulls)]

> [!WARNING]
> Wenn Sie die relationale NULL-Semantik verwenden, haben Ihre LINQ-Abfragen nicht mehr dieselbe Bedeutung wie in C#, und es kann zu unerwarteten Ergebnissen kommen. Gehen Sie umsichtig vor, wenn Sie diesen Modus verwenden.
