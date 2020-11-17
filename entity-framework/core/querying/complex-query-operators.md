---
title: Komplexe Abfrageoperatoren – EF Core
description: Umfassende Informationen zu komplexeren LINQ-Abfrageoperatoren bei Verwendung von Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/complex-query-operators
ms.openlocfilehash: 84c2518972355d31cf5a6a7bafc57b44162412c8
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430481"
---
# <a name="complex-query-operators"></a>Komplexe Abfrageoperatoren

LINQ (Language Integrated Query) enthält viele komplexe Operatoren, die mehrere Datenquellen kombinieren oder komplexe Verarbeitungen durchführen. Nicht für alle LINQ-Operatoren gibt es geeignete Übersetzungen auf der Serverseite. Manchmal wird eine Abfrage in einem Format für den Server übersetzt, aber in einem anderen Format nicht, obwohl das Ergebnis gleich ist. Auf dieser Seite werden einige der komplexen Operatoren und ihre unterstützten Variationen beschrieben. Möglicherweise werden in zukünftigen Releases mehr Muster erkannt und deren entsprechenden Übersetzungen hinzugefügt. Es ist auch wichtig, zu beachten, dass die Übersetzungsunterstützung je nach Anbieter variiert. Eine bestimmte Abfrage, die für SQL Server übersetzt wird, funktioniert möglicherweise nicht für SQLite-Datenbanken.

> [!TIP]
> Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/ComplexQuery) finden Sie auf GitHub.

## <a name="join"></a>Join

Mit dem LINQ-Join-Operator können Sie zwei Datenquellen anhand des Schlüsselselektors für die jeweilige Quelle verbinden und ein Tupel von Werten erstellen, wenn der Schlüssel übereinstimmt. Dies wird für relationale Datenbanken natürlich in `INNER JOIN` übersetzt. Während der LINQ-Join-Operator äußere und innere Schlüsselselektoren aufweist, erfordert die Datenbank eine einzelne Verknüpfungsbedingung. Daher generiert EF Core eine Verknüpfungsbedingung mithilfe eines Vergleichs der Gleichheit des äußeren und inneren Schlüsselselektors.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#Join)]

```sql
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

Des Weiteren generiert EF Core eine Verknüpfungsbedingung, um die Gleichheit der Komponenten zu vergleichen, wenn die Schlüsselselektoren einen anonymen Typ aufweisen.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#JoinComposite)]

```sql
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON ([p0].[PersonPhotoId] = [p].[PhotoId] AND ([p0].[Caption] = N'SN'))
```

## <a name="groupjoin"></a>GroupJoin

Mit dem LINQ-GroupJoin-Operator können Sie zwei Datenquellen ähnlich wie mit dem Join-Operator verbinden, jedoch wird dabei eine Gruppe innerer Werte zum Vergleichen äußerer Elemente erstellt. Das Ausführen einer Abfrage ähnlich dem folgenden Beispiel führt zum Ergebnis: `Blog` & `IEnumerable<Post>`. Da Datenbanken (insbesondere relationale Datenbanken) keine Möglichkeit zum Darstellen einer Sammlung von clientseitigen Objekten aufweisen, können GroupJoin-Operatoren in vielen Fällen nicht auf den Server übersetzt werden. Hierzu ist erforderlich, dass Sie alle Daten für GroupJoin ohne einen speziellen Selektor vom Server abrufen (die erste Abfrage unten). Wenn der Selektor jedoch die Auswahl von Daten einschränkt und dann alle Daten vom Server abruft, können Leistungsprobleme auftreten (die zweite Abfrage unten). Daher übersetzt EF Core GroupJoin nicht.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupJoinComposed)]

## <a name="selectmany"></a>SelectMany

Mit dem LINQ-SelectMany-Operator können Sie einen Sammlungsselektor für äußere Elemente aufzählen und Tupel von Werten aus einzelnen Datenquellen generieren. Auf gewisse Zeit ist dies ein Join, der jedoch keine Bedingungen aufweist, sodass alle äußeren Elemente mit einem Element aus der Sammlungsquelle verknüpft werden. Je nachdem, wie der Sammlungsselektor im Bezug mit der äußeren Datenquelle steht, kann SelectMany in verschiedene Abfragen auf der Serverseite übersetzt werden.

### <a name="collection-selector-doesnt-reference-outer"></a>Sammlungsselektor referenziert nicht die äußere Quelle

Wenn der Sammlungsselektor nichts von der äußeren Quelle referenziert, ist das Ergebnis ein kartesisches Produkt beider Datenquellen. Dies wird für relationale Datenbanken in `CROSS JOIN` übersetzt.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToCrossJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a>Sammlungsselektor referenziert die äußere Datenquelle in einer WHERE-Klausel

Wenn der Sammlungsselektor eine WHERE-Klausel enthält, die das äußere Element referenziert, dann übersetzt EF Core sie in einen Join der Datenbank und verwendet das Prädikat als Joinbedingung. Normalerweise tritt dieser Fall auf, wenn die Sammlungsnavigation im äußeren Element als Sammlungsselektor verwendet wird. Wenn die Sammlung für ein äußeres Element leer ist, werden keine Ergebnisse für dieses generiert. Wenn `DefaultIfEmpty` jedoch auf den Sammlungsselektor angewendet wird, wird das äußere Element mit einem Standardwert des inneren Elements verknüpft. Aufgrund dieser Unterscheidung wird diese Art von Abfrage bei Abwesenheit von `DefaultIfEmpty` in `INNER JOIN` übersetzt und in `LEFT JOIN`, wenn `DefaultIfEmpty` angewendet wurde.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a>Sammlungsselektor referenziert die äußere Datenquelle in einem Fall ohne WHERE-Klausel

Wenn der Sammlungsselektor das äußere Element referenziert, das sich nicht in einer WHERE-Klausel befindet (wie im obigen Fall), wird es nicht in einen Join der Datenbank übersetzt. Daher muss der Sammlungsselektor für alle äußeren Elemente ausgewertet werden. Für viele relationale Datenbanken wird dies in `APPLY`-Vorgänge übersetzt. Wenn die Sammlung für ein äußeres Element leer ist, werden keine Ergebnisse für dieses generiert. Wenn `DefaultIfEmpty` jedoch auf den Sammlungsselektor angewendet wird, wird das äußere Element mit einem Standardwert des inneren Elements verknüpft. Aufgrund dieser Unterscheidung wird diese Art von Abfrage bei Abwesenheit von `DefaultIfEmpty` in `CROSS APPLY` übersetzt und in `OUTER APPLY`, wenn `DefaultIfEmpty` angewendet wurde. Bestimmte Datenbanken wie SQLite unterstützen `APPLY`-Operatoren nicht. Daher wird diese Art von Abfrage möglicherweise nicht übersetzt.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#SelectManyConvertedToApply)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a>GroupBy

LINQ-GroupBy-Operatoren erstellen ein Ergebnis vom Typ `IGrouping<TKey, TElement>`, wobei `TKey` und `TElement` einen beliebigen arbiträren Typ aufweisen können. Außerdem wird `IEnumerable<TElement>` von `IGrouping` implementiert, d. h. Sie können es nach der Gruppierung mithilfe eines beliebigen LINQ-Operators zusammensetzen. Da keine Datenbankstruktur eine `IGrouping`-Schnittstelle darstellen kann, gibt es in den meisten Fällen keine Übersetzung für GroupBy-Operatoren. Wenn ein Aggregate-Operator auf alle Gruppen angewendet wird, die einen Skalarwert zurückgeben, kann dieser für relationale Datenbanken in die SQL-Anweisung `GROUP BY` übersetzt werden. Die SQL-Anweisung `GROUP BY` ist ebenfalls einschränkend. Sie erfordert, dass Sie nur nach Skalarwerten gruppieren. Die Projektion kann nur gruppierende Schlüsselspalten oder auf eine Spalte angewendete Aggregate-Operatoren enthalten. EF Core identifiziert dieses Muster und übersetzt es wie im folgenden Beispiel für den Server:

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupBy)]

```sql
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

EF Core übersetzt Abfragen auch, wenn ein Aggregate-Operator für die Gruppierung in einem WHERE- oder OrderBy-LINQ-Operator (oder in anderen Order-Operatoren) vorliegt. Für die WHERE-Klausel wird in SQL eine `HAVING`-Klausel verwendet. Der Teil der Abfrage, bevor der GroupBy-Operator angewendet wird, kann einer beliebigen komplexen Abfrage entsprechen, solange diese für den Server übersetzt werden kann. Sobald Sie Aggregate-Operatoren in einer Gruppierungsabfrage anwenden, um Gruppierungen aus der resultierenden Quelle zu entfernen, können Sie sie außerdem wie jede andere Abfrage zusammensetzen.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#GroupByFilter)]

```sql
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

Die von EF Core unterstützten Aggregate-Operatoren lauten wie folgt:

- Average
- Anzahl
- LongCount
- Max
- Min
- Sum

## <a name="left-join"></a>Left Join

„Left Join“ ist zwar kein LINQ-Operator, jedoch verfügen relationale Datenbanken über das Konzept von „Left Join“, das häufig in Abfragen verwendet wird. Ein bestimmtes Muster in LINQ-Abfragen führt zu demselben Ergebnis wie ein `LEFT JOIN` auf dem Server. EF Core identifiziert solche Muster und generiert den entsprechenden `LEFT JOIN` auf dem Server. Das Muster umfasst die Erstellung eines GroupJoin-Operators auf beiden Datenquellen sowie die anschließende Vereinfachung der Gruppierung mithilfe des SelectMany-Operators mit DefaultIfEmpty für die Gruppierungsquelle, um eine Übereinstimmung mit NULL zu suchen, wenn die innere Quelle kein zugehöriges Element aufweist. Im folgenden Beispiel wird das Muster und dessen Ergebnis veranschaulicht.

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Program.cs#LeftJoin)]

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

Das obige Muster erstellt eine komplexe Struktur in der Ausdrucksbaumstruktur. Daher erfordert EF Core, dass Sie die Gruppierungsergebnisse des GroupJoin-Operators in dem Schritt vereinfachen, der direkt auf den Operator folgt. Selbst wenn GroupJoin-DefaultIfEmpty-SelectMany in einem anderen Muster verwendet wird, wird dies möglicherweise nicht als Left Join identifiziert.
