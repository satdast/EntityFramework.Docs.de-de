---
title: 'Globale Abfragefilter: EF Core'
description: Verwenden von globalen Abfragefiltern zum Filtern von Ergebnissen mit Entity Framework Core
author: maumar
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 8a9eabd7e86864c9ebb4b1dc4a06bf7fc207d496
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062606"
---
# <a name="global-query-filters"></a>Globale Abfragefilter

Globale Abfragefilter sind LINQ-Abfrageprädikate, die auf Entitätstypen im Metadatenmodell (normalerweise in `OnModelCreating`) angewendet werden. Bei einem Abfrageprädikat handelt es sich um einen booleschen Ausdruck, der in der Regel an den LINQ-Abfrageoperator `Where` (Wo) übergeben wird.  EF Core wendet solche Filter automatisch auf alle LINQ-Abfragen an, die diese Entitätstypen einschließen.  EF Core wendet sie auch auf Entitätstypen an, auf die indirekt durch Verwendung der Include-Eigenschaft oder Navigationseigenschaft verwiesen wird. Zu den häufigsten Anwendungsfällen dieses Features zählen Folgende:

* **Vorläufiges Löschen:** Ein Entitätstyp definiert eine `IsDeleted`-Eigenschaft.
* **Mehrinstanzenfähigkeit:** Ein Entitätstyp definiert eine `TenantId`-Eigenschaft.

## <a name="example"></a>Beispiel

Im folgenden Beispiel wird in einem einfachen Blogmodell dargestellt, wie globale Abfragefilter zum Implementieren des Abfrageverhaltens für das vorläufige Löschen und die Mehrinstanzenfähigkeit verwendet werden.

> [!TIP]
> Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/QueryFilters) finden Sie auf GitHub.

Definieren Sie zunächst die Entitäten:

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Entities.cs#Entities)]

Beachten Sie die Deklaration eines `_tenantId`-Felds in der Entität `Blog`. Dieses Feld wird dazu verwendet, jede Bloginstanz einem bestimmten Mandanten zuzuordnen. Außerdem wird eine `IsDeleted`-Eigenschaft auf dem Entitätstyp `Post` definiert. Mit dieser Eigenschaft wird nachverfolgt, ob eine Post-Instanz „vorläufig gelöscht“ wurde. Das heißt, die Instanz wird als gelöscht gekennzeichnet, ohne dass zugrunde liegende Daten physisch entfernt werden.

Konfigurieren Sie als nächstes die Abfragefilter in `OnModelCreating` mithilfe der `HasQueryFilter`-API.

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/BloggingContext.cs#FilterConfiguration)]

Die Prädikatausdrücke, die an `HasQueryFilter` weitergegeben werden, werden nun automatisch auf alle LINQ-Abfragen dieser Typen angewendet.

> [!TIP]
> Beachten Sie die Verwendung eines DbContext-Instanzfelds: `_tenantId` wird zum Festlegen des aktuellen Mandanten verwendet. Filter auf Modellebene verwenden den Wert der korrekten Kontextinstanz, d.h. der Instanz, die die Abfrage ausführt.

> [!NOTE]
> Es ist derzeit nicht möglich, mehrere Abfragefilter für dieselbe Entität zu definieren, nur der letzte wird angewendet. Mithilfe des logischen `AND`-Operators ([`&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)) können jedoch einen einzelnen Filter mit mehreren Bedingungen definieren.

## <a name="use-of-navigations"></a>Verwenden von Navigationselementen

Navigationselemente können beim Definieren lokaler Abfragefilter verwendet werden. Das Verwenden von Navigationselementen in Abfragefiltern führt dazu, dass Abfragefilter rekursiv angewendet werden. Wenn EF Core die in Navigationselementen verwendeten Abfragefilter erweitert, werden auch die für die Entitäten, auf die verwiesen wird, definierten Abfragefilter angewendet.

> [!NOTE]
> Derzeit ermittelt EF Core keine Zyklen in globalen Abfragefilterdefinitionen. Daher sollten Sie bei der Definition vorsichtig vorgehen. Wenn diese fehlerhaft angegeben wird, können Endlosschleifen bei der Abfrageübersetzung auftreten.

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a>Zugreifen auf Entitäten mit Abfragefilter mithilfe erforderlicher Navigationselemente

> [!CAUTION]
> Die Verwendung erforderlicher Navigationselemente für den Zugriff auf eine Entität mit einem definierten globalen Abfragefilter kann zu unerwarteten Ergebnissen führen.

Die erforderlichen Navigationselemente erwarten, dass die zugehörige Entität immer vorhanden ist. Wenn erforderliche verwandte Entitäten vom Abfragefilter herausgefiltert werden, würde die übergeordnete Entität ebenso nicht im Ergebnis ausgegeben werden. Daher erhalten Sie möglicherweise weniger Elemente als erwartet.

Zur Veranschaulichung dieses Problems können die oben angegebenen Entitäten `Blog` und `Post` mit der `OnModelCreating`-Methode verwendet werden:

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#IncorrectFilter)]

Das Modell kann mit den folgenden Daten eingerichtet werden:

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#SeedData)]

Das Problem tritt auf, wenn zwei Abfragen ausgeführt werden:

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#Queries)]

Bei diesem Setup gibt die erste Abfrage alle sechs `Post`-Anfragen zurück. Die zweite Abfrage gibt jedoch nur drei zurück. Das liegt daran, dass die `Include`-Methode in der zweiten Abfrage die zugehörigen `Blog`-Entitäten lädt. Das die Navigation zwischen `Blog` und `Post` erforderlich ist, nutzt EF Core `INNER JOIN` beim Erstellen der Abfrage:

```sql
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Posts] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE [b].[Url] LIKE N'%fish%'
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

Bei Verwendung von `INNER JOIN` werden alle `Post`-Vorgänge herausgefiltert, deren zugehörigen `Blog`-Entitäten von einem globalen Abfragefilter entfernt wurden.

Dieses Problem können Sie mithilfe optionaler Navigationselemente anstelle erforderlicher umgehen.
Auf diese Weise bleibt die erste Abfrage unverändert, die zweite Abfrage generiert nun jedoch `LEFT JOIN` und gibt sechs Ergebnisse zurück.

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#OptionalNavigation)]

Der alternative Ansatz besteht darin, konsistente Filter für die beiden Entitäten `Blog` und `Post` festzulegen.
Auf diese Weise werden entsprechende Filter auf `Blog` und `Post` angewendet. `Post`-Entitäten, die einen unerwarteten Status aufweisen könnten, werden entfernt und beide Abfragen geben drei Ergebnisse zurück.

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/FilteredBloggingContextRequired.cs#MatchingFilters)]

## <a name="disabling-filters"></a>Deaktivieren von Filtern

Filter können für einzelne LINQ-Abfragen mit dem <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.IgnoreQueryFilters%2A>-Operator deaktiviert werden.

[!code-csharp[Main](../../../samples/core/Querying/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>Einschränkungen

Globale Abfragefilter unterliegen den folgenden Einschränkungen:

* Filter können nur für den Stammentitätstyp einer Vererbungshierarchie definiert werden.
