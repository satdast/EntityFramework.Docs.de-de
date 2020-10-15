---
title: Unformatierte SQL-Abfragen – EF Core
description: Verwenden von reinem SQL-Code für Abfragen in Entity Framework Core
author: smitpatel
ms.date: 10/08/2019
uid: core/querying/raw-sql
ms.openlocfilehash: 9c480d13c46c7c84554996bcb581627a1df318dd
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062593"
---
# <a name="raw-sql-queries"></a>Unformatierte SQL-Abfragen

Mit Entity Framework Core können Sie bei der Arbeit mit einer relationalen Datenbank die Struktur unformatierter SQL-Abfragen maximieren. Unformatierte SQL-Abfragen sind nützlich, wenn die gewünschte Abfrage nicht mithilfe von LINQ ausgedrückt werden kann. Unformatierte SQL-Abfragen werden auch verwendet, wenn eine LINQ-Abfrage zu einer ineffizienten SQL-Abfrage führt. Unformatierte SQL-Abfragen können reguläre Entitätstypen oder [schlüssellose Entitätstypen](xref:core/modeling/keyless-entity-types) zurückgeben, die Teil des Modells sind.

> [!TIP]  
> Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RawSQL) finden Sie auf GitHub.

## <a name="basic-raw-sql-queries"></a>Grundlegende unformatierte SQL-Abfragen

Sie können die `FromSqlRaw`-Erweiterungsmethode verwenden, um eine LINQ-Abfrage basierend auf einer unformatierten SQL-Abfrage zu starten. `FromSqlRaw` kann nur für Abfragestämme verwendet werden, die sich direkt in der `DbSet<>`-Klasse befinden.

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRaw)]

Unformatierte SQL-Abfragen können für die Ausführung einer gespeicherten Prozedur verwendet werden.

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRawStoredProcedure)]

## <a name="passing-parameters"></a>Übergeben von Parametern

> [!WARNING]
> **Für unformatierte SQL-Abfragen immer Parametrisierung verwenden**
>
> Wenn Sie von Benutzern bereitgestellte Werte in eine unformatierte SQL-Abfrage einführen, muss darauf geachtet werden, dass Angriffe durch Einschleusung von SQL-Befehlen vermieden werden. Überprüfen Sie nicht nur, ob solche Werte keine ungültigen Zeichen enthalten, sondern verwenden Sie auch immer die Parametrisierung, die die Werte getrennt vom SQL-Text sendet.
>
> Übergeben Sie insbesondere niemals eine verkettete oder interpolierte Zeichenfolge (`$""`) mit nicht validierten und von Benutzern bereitgestellten Werten in `FromSqlRaw` oder `ExecuteSqlRaw`. Dank der `FromSqlInterpolated`- und `ExecuteSqlInterpolated`-Methode kann die Zeichenfolgeninterpolationssyntax so eingesetzt werden, dass sie vor Angriffen durch Einschleusung von SQL-Befehlen schützt.

Im folgenden Beispiel wird ein einzelner Parameter an eine gespeicherte Prozedur übergeben, indem ein Parameterplatzhalter in die SQL-Abfragezeichenfolge eingeschlossen und ein zusätzliches Argument bereitgestellt wird. Diese Syntax sieht zwar aus wie eine `String.Format`-Syntax, jedoch wird der angegebene Wert in einer `DbParameter`-Klasse eingeschlossen und der generierte Parametername wird dort eingefügt, wo der `{0}`-Platzhalter angegeben wurde.

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRawStoredProcedureParameter)]

`FromSqlInterpolated` ähnelt `FromSqlRaw`, lässt jedoch die Verwendung der Zeichenfolgeninterpolationsyntax zu. `FromSqlInterpolated` kann wie `FromSqlRaw` nur für Abfragestämme verwendet werden. Wie im vorherigen Beispiel wird der Wert in eine `DbParameter`-Klasse konvertiert und ist nicht anfällig für die Einschleusung von SQL-Befehlen.

> [!NOTE]
> Vor Version 3.0 waren `FromSqlRaw` und `FromSqlInterpolated` zwei Überladungen namens `FromSql`. Weitere Informationen finden Sie im [Abschnitt zu vorherigen Versionen](#previous-versions).

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlInterpolatedStoredProcedureParameter)]

Sie können auch einen DbParameter erstellen und diesen als Parameterwert bereitstellen. Da anstelle eines Zeichenfolgeplatzhalters ein regulärer SQL-Parameterplatzhalter verwendet wird, kann `FromSqlRaw` sicher verwendet werden:

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRawStoredProcedureSqlParameter)]

Mit `FromSqlRaw` können Sie benannte Parameter in der SQL-Abfragezeichenfolge verwenden. Dies ist nützlich, wenn eine gespeicherte Prozedur optionale Parameter aufweist:

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRawStoredProcedureNamedSqlParameter)]

> [!NOTE]
> **Parameterreihenfolge**: Entity Framework Core übergibt Parameter basierend auf der Reihenfolge des `SqlParameter[]`-Arrays. Beim Übergeben mehrerer `SqlParameter` muss die Reihenfolge in der SQL-Zeichenfolge mit der Reihenfolge der Parameter in der Definition der gespeicherten Prozedur übereinstimmen. Ist dies nicht der Fall, führt dies möglicherweise zu Typkonvertierungsausnahmen und/oder unerwartetem Verhalten beim Ausführen der Prozedur.

## <a name="composing-with-linq"></a>Zusammensetzen mit LINQ

Mithilfe von LINQ-Operatoren können Sie die zunächst unformatierten SQL-Abfragen zusammensetzen. EF Core behandelt diese als Unterabfragen und fügt sie in der Datenbank zusammen. Im folgenden Beispiel wird eine unformatierte SQL-Abfrage verwendet, die aus einer Tabellenwertfunktion auswählt. Anschließend wird sie mithilfe von LINQ zusammengesetzt, um die Filterung und Sortierung durchzuführen.

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlInterpolatedComposed)]

Mit der obigen Abfrage wird folgendes SQL generiert:

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM (
    SELECT * FROM dbo.SearchBlogs(@p0)
) AS [b]
WHERE [b].[Rating] > 3
ORDER BY [b].[Rating] DESC
```

### <a name="including-related-data"></a>Einschließen zugehöriger Daten

Die `Include`-Methode kann verwendet werden, um verwandte Daten einzuschließen, genauso wie bei jeder anderen LINQ-Abfrage:

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlInterpolatedInclude)]

Für die Zusammensetzung mit LINQ muss Ihre unformatierte SQL-Abfrage zusammensetzbar sein, da EF Core das angegebene SQL als Unterabfrage behandelt. Zusammensetzbare SQL-Abfragen beginnen mit dem Schlüsselwort `SELECT`. Außerdem sollte übergebenes SQL keine Zeichen oder Optionen enthalten, die für eine Unterabfrage ungültig sind, zum Beispiel:

- Ein nachfolgendes Semikolon
- Auf SQL Server ein nachfolgender Hinweis auf Abfrageebene, z.B. `OPTION (HASH JOIN)`
- Eine `ORDER BY`-Klausel in SQL Server, die nicht mit `OFFSET 0` ODER `TOP 100 PERCENT` in der `SELECT`-Klausel verwendet wird

SQL Server erlaubt die Zusammensetzung von Aufrufen gespeicherter Prozeduren nicht. Daher resultieren Versuche, zusätzliche Abfrageoperatoren auf solche Aufrufe anzuwenden, in ungültigem SQL. Verwenden Sie die `AsEnumerable`- oder `AsAsyncEnumerable`-Methode direkt nach der `FromSqlRaw`- oder `FromSqlInterpolated`-Methode, um sicherzustellen, dass EF Core nicht ersucht, eine gespeicherte Prozedur zusammenzusetzen.

## <a name="change-tracking"></a>Change Tracking

Abfragen, die die `FromSqlRaw`- oder `FromSqlInterpolated`-Methode verwenden, befolgen genau dieselben Änderungsnachverfolgungsregeln wie jede andere LINQ-Abfrage in EF Core. Wenn beispielsweise die Abfrage Entitätstypen projiziert, werden die Ergebnisse standardmäßig nachverfolgt.

Im folgenden Beispiel wird eine unformatierte SQL-Abfrage verwendet, die aus einer Tabellenwertfunktion (TVF) auswählt und anschließend die Änderungsnachverfolgung mit dem Aufruf auf `AsNoTracking` deaktiviert:

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlInterpolatedAsNoTracking)]

## <a name="limitations"></a>Einschränkungen

Bei der Verwendung unformatierter SQL-Abfragen sind einige wenige Einschränkungen zu beachten:

- Die SQL-Abfrage muss Daten für sämtliche Eigenschaften des Entitätstyps zurückgeben.
- Die Spaltennamen im Resultset müssen mit den Spaltennamen übereinstimmen, denen Eigenschaften zugewiesen sind. Beachten Sie, dass sich dieses Verhalten von EF6 unterscheidet. EF6 ignoriert die Zuordnung von Eigenschaften zu Spalten bei unformatierten SQL-Abfragen, und Spaltennamen von Resultsets mussten mit den Eigenschaftennamen übereinstimmen.
- Die SQL-Abfrage darf keine zugehörigen Daten enthalten. In vielen Fällen können Sie die Abfrage jedoch mit dem Operator `Include` zusammensetzen, damit zugehörige Daten zurückgegeben werden (siehe [Einschließen zugehöriger Daten](#including-related-data)).

## <a name="previous-versions"></a>Vorherige Versionen

In EF Core Version 2.2 und früher gab es zwei Überladungen für eine Methode namens `FromSql`, die sich wie die neueren Methoden `FromSqlRaw` und `FromSqlInterpolated` verhielten. Es war leicht, versehentlich die unformatierte Zeichenfolgenmethode aufzurufen, wenn eigentlich ein Aufruf der interpolierten Zeichenfolgenmethode beabsichtigt war oder umgekehrt. Versehentliche Aufrufe der falschen Überladung konnten dazu führen, dass Abfragen nicht parametrisiert wurden, wenn sie parametrisiert werden sollten.
