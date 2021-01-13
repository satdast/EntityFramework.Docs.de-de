---
title: 'Zuordnung benutzerdefinierter Funktionen: EF Core'
description: Hier erhalten Sie Informationen zum Zuordnen benutzerdefinierter Funktionen zu Datenbankfunktionen.
author: maumar
ms.date: 11/23/2020
uid: core/user-defined-function-mapping
ms.openlocfilehash: ba60abdc9c81b34b8f4ed8f501cf2f7e52ba9d7d
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657697"
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
