---
title: 'Zuordnung benutzerdefinierter Funktionen: EF Core'
description: Hier erhalten Sie Informationen zum Zuordnen benutzerdefinierter Funktionen zu Datenbankfunktionen.
author: maumar
ms.date: 11/23/2020
uid: core/querying/user-defined-function-mapping
ms.openlocfilehash: 3e49ed9c49b38b98430128ffdc7ceef0b844b9df
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129121"
---
# <a name="user-defined-function-mapping"></a>Zuordnung benutzerdefinierter Funktionen

EF Core ermöglicht die Verwendung benutzerdefinierter SQL-Funktionen in Abfragen. Dazu müssen die Funktionen während der Modellkonfiguration einer CLR-Methode zugeordnet werden. Beim Übersetzen der LINQ-Abfrage in SQL wird die benutzerdefinierte Funktion anstelle der CLR-Funktion aufgerufen, der sie zugeordnet wurde.

## <a name="mapping-a-method-to-a-sql-function"></a>Zuordnen einer Methode zu einer SQL-Funktion

Damit veranschaulicht werden kann, wie das Zuordnen benutzerdefinierter Funktionen funktioniert, werden hier die folgenden Entitäten definiert:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#Entities)]

Außerdem wird als Beispiel die folgende Modellkonfiguration verwendet:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#EntityConfiguration)]

In Blogs finden sich viele Beiträge. Jeder Beitrag wiederum kann über viele Kommentare verfügen.

Erstellen Sie als Nächstes die benutzerdefinierte Funktion `CommentedPostCountForBlog`. Diese gibt die Anzahl der Beiträge zurück, für die pro Blog – Blog `Id` in diesem Beispiel – mindestens ein Kommentar vorhanden ist:

```sql
CREATE FUNCTION dbo.CommentedPostCountForBlog(@id int)
RETURNS int
AS
BEGIN
    RETURN (SELECT COUNT(*)
        FROM [Posts] AS [p]
        WHERE ([p].[BlogId] = @id) AND ((
            SELECT COUNT(*)
            FROM [Comments] AS [c]
            WHERE [p].[PostId] = [c].[PostId]) > 0));
END
```

Zur Verwendung dieser Funktion in EF Core wird die folgende CLR-Methode definiert, die der benutzerdefinierten Funktion zugeordnet wird:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionDefinition)]

Der Methodenkörper der CLR-Methode spielt keine Rolle. Die Methode wird nur clientseitig aufgerufen, wenn EF Core die dazugehörigen Argumente nicht übersetzen kann. Wenn die Argumente übersetzt werden können, spielt nur die Methodensignatur eine Rolle für EF Core.

> [!NOTE]
> Im Beispiel wird die Methode für `DbContext` definiert. Die Definition als statische Methode innerhalb anderer Klassen ist aber ebenfalls möglich.

Diese Funktionsdefinition kann nun der benutzerdefinierten Funktion in der Modellkonfiguration zugeordnet werden:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#BasicFunctionConfiguration)]

Standardmäßig versucht EF Core, CLR-Funktionen einer benutzerdefinierten Funktion mit demselben Name zuzuordnen. Wenn sich der Name unterscheidet, kann `HasName` verwendet werden, um den richtigen Namen für die benutzerdefinierte Funktion anzugeben, zu der die Zuordnung erfolgen soll.

Als Nächstes wird nun die folgende Abfrage ausgeführt:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#BasicQuery)]

Dies führt zu folgendem SQL-Code:

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE [dbo].[CommentedPostCountForBlog]([b].[BlogId]) > 1
```

## <a name="mapping-a-method-to-a-custom-sql"></a>Zuordnen einer Methode zu einem benutzerdefinierten SQL-Ausdruck

EF Core ermöglicht es auch, benutzerdefinierte Funktionen in einen bestimmten SQL-Ausdruck zu konvertieren. Der SQL-Ausdruck wird mithilfe der `HasTranslation`-Methode während der Konfiguration der benutzerdefinierten Funktion angegeben.

Im Beispiel unten wird eine Funktion erstellt, die den prozentualen Unterschied zwischen zwei Integern berechnet.

Die CLR-Methode lautet wie folgt:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionDefinition)]

Die Funktionsdefinition lautet wie folgt:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#HasTranslationFunctionConfiguration)]

Sobald die Funktion definiert wurde, kann sie in der Abfrage verwendet werden. Anstatt die Datenbankfunktion aufzurufen, übersetzt EF Core den Methodenkörper direkt in die SQL-basierte SQL-Ausdrucksbaumstruktur, die auf Grundlage von HasTranslation erstellt wird. Betrachten Sie die folgende LINQ-Abfrage:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#HasTranslationQuery)]

Dies generiert diese SQL-Anweisung:

```sql
SELECT 100 * (ABS(CAST([p].[BlogId] AS float) - 3) / ((CAST([p].[BlogId] AS float) + 3) / 2))
FROM [Posts] AS [p]
```

## <a name="configuring-nullability-of-user-defined-function-based-on-its-arguments"></a>Konfigurieren der NULL-Zulässigkeit von benutzerdefinierten Funktionen auf Basis von Argumenten

Wenn die benutzerdefinierte Funktion nur `null` zurückgeben kann, wenn mindestens ein Argument `null` ist, kann dies in EF Core angegeben werden. Dies führt zu einem leistungsfähigeren SQL-Code. Dazu muss der Modellkonfiguration des relevanten Funktionsparameters ein `PropagatesNullability()`-Aufruf hinzugefügt werden.

Zur Veranschaulichung wird die Benutzerfunktion `ConcatStrings` definiert:

```sql
CREATE FUNCTION [dbo].[ConcatStrings] (@prm1 nvarchar(max), @prm2 nvarchar(max))
RETURNS nvarchar(max)
AS
BEGIN
    RETURN @prm1 + @prm2;
END
```

Außerdem werden zwei CLR-Methoden definiert, die ihr zugeordnet sind:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationFunctionDefinition)]

Die Modellkonfiguration (innerhalb der Methode `OnModelCreating`) lautet wie folgt:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#NullabilityPropagationModelConfiguration)]

Die erste Funktion wird standardmäßig konfiguriert. Die zweite Funktion ist so konfiguriert, dass sie die Propagierungsoptimierung der NULL-Zulässigkeit nutzt und weitere Informationen zur Funktionsweise der Funktion im Kontext von NULL-Parametern bietet.

Die folgenden Abfragen:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#NullabilityPropagationExamples)]

Ergeben den folgenden SQL-Code:

```sql
SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR [dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) IS NULL

SELECT [b].[BlogId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
WHERE ([dbo].[ConcatStrings]([b].[Url], CONVERT(VARCHAR(11), [b].[Rating])) <> N'Lorem ipsum...') OR ([b].[Url] IS NULL OR [b].[Rating] IS NULL)
```

Die zweite Abfrage muss die Funktion nicht selbst neu auswerten, um deren NULL-Zulässigkeit zu testen.

> [!NOTE]
> Diese Optimierung sollte nur eingesetzt werden, wenn die Funktion nur `null` zurückgeben kann, wenn ihre Parameter `null` sind.

## <a name="mapping-a-queryable-function-to-a-table-valued-function"></a>Zuordnen einer abfragbaren Funktion zu einer Tabellenwertfunktion

EF Core unterstützt auch das Zuordnen zu einer Tabellenwertfunktion mithilfe einer benutzerdefinierten CLR-Methode. Dabei wird eine `IQueryable`-Klasse der Entitätstypen zurückgegeben, was es EF Core ermöglicht, Tabellenwertfunktionen Parameter zuzuordnen. Der Prozess ähnelt dem Zuordnen einer benutzerdefinierten Skalarfunktion zu einer SQL-Funktion: Es wird eine Tabellenwertfunktion in der Datenbank benötigt, eine CLR-Funktion, die in den LINQ-Abfragen verwendet wird, sowie eine Zuordnung zwischen diesen beiden.

Als Beispiel soll eine Tabellenwertfunktion verwendet werden, die alle Beiträge zurückgibt, die über mindestens einen Kommentar verfügen, der einen gesetzten Schwellenwert an „Gefällt mir“-Markierungen erreicht:

```sql
CREATE FUNCTION dbo.PostsWithPopularComments(@likeThreshold int)
RETURNS TABLE
AS
RETURN
(
    SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
    FROM [Posts] AS [p]
    WHERE (
        SELECT COUNT(*)
        FROM [Comments] AS [c]
        WHERE ([p].[PostId] = [c].[PostId]) AND ([c].[Likes] >= @likeThreshold)) > 0
)
```

Die Signatur der CLR-Methode sieht folgendermaßen aus:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionDefinition)]

> [!TIP]
> Der `FromExpression`-Aufruf im CLR-Funktionskörper ermöglicht es, dass die Funktion anstelle der regulären DbSet-Klasse verwendet wird.

Unten sehen Sie die Zuordnung:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Model.cs#QueryableFunctionConfigurationHasDbFunction)]

> [!CAUTION]
> Solange das [Issue 23408](https://github.com/dotnet/efcore/issues/23408) nicht behoben ist, wird bei Zuordnungen zu einer `IQueryable`-Schnittstelle von Entitätstypen die Standardzuordnung zu einer Tabelle für die DbSet-Klasse überschrieben. Falls erforderlich, z. B. wenn die Entität nicht schlüssellos ist, muss die Zuordnung zur Tabelle mithilfe der `ToTable`-Methode explizit angegeben werden.

> [!NOTE]
> Abfragbare Funktionen müssen einer Tabellenwertfunktion zugeordnet werden und können `HasTranslation` nicht verwenden.

Sehen Sie sich die folgende Abfrage an, bei der die Funktion zugeordnet ist:

[!code-csharp[Main](../../../samples/core/Querying/UserDefinedFunctionMapping/Program.cs#TableValuedFunctionQuery)]

Sie führt zu folgender Ausgabe:

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [dbo].[PostsWithPopularComments](@likeThreshold) AS [p]
ORDER BY [p].[Rating]
```
