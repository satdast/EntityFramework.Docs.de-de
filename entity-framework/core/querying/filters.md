---
title: 'Globale Abfragefilter: EF Core'
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 233289b0c50e15f4555b342bc654211ce04c24d3
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370460"
---
# <a name="global-query-filters"></a>Globale Abfragefilter

> [!NOTE]
> Dieses Feature wurde in EF Core 2.0 eingeführt.

Globale Abfragefilter sind LINQ-Abfrageprädikate (ein boolescher Ausdruck, der in der Regel an den LINQ-Abfrageoperator *Where* übergeben wird), die auf Entitätstypen im Metadatenmodell (normalerweise in *OnModelCreating*) angewendet werden. Solche Filter werden automatisch auf alle diese Entitätstypen betreffenden LINQ-Abfragen (z.B. indirekt referenzierte Entitätstypen) angewendet, beispielsweise durch include-Verweise oder direkte Verweise auf Navigationseigenschaften. Zu den häufigsten Anwendungsfällen dieses Features zählen Folgende:

* **Vorläufiges Löschen:** Ein Entitätstyp definiert eine *IsDeleted*-Eigenschaft.
* **Mehrinstanzenfähigkeit:** Ein Entitätstyp definiert eine *TenantId*-Eigenschaft.

## <a name="example"></a>Beispiel

Im folgenden Beispiel wird in einem einfachen Blogmodell dargestellt, wie globale Abfragefilter zum Implementieren des Abfrageverhaltens für das vorläufige Löschen und die Mehrinstanzenfähigkeit verwendet werden.

> [!TIP]
> Sie können sich auf GitHub ein [Beispiel mit mehreren Mandanten](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) und [Beispiele mit Navigationselementen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) ansehen. 

Definieren Sie zunächst die Entitäten:

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

Beachten Sie die Deklaration eines _tenantId_-Felds in der Entität _Blog_. Dies wird dazu verwendet, jede Bloginstanz einem bestimmten Mandanten zuzuordnen. Außerdem wird eine _IsDeleted_-Eigenschaft auf dem Entitätstyp _Post_ definiert. Damit wird nachverfolgt, ob eine _Post_-Instanz „vorläufig gelöscht“ wurde. Das heißt, die Instanz wird als gelöscht gekennzeichnet, ohne dass zugrunde liegende Daten physisch entfernt werden.

Konfigurieren Sie als nächstes die Abfragefilter in _OnModelCreating_ mithilfe der `HasQueryFilter`-API.

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

Die Prädikatausdrücke, die an _HasQueryFilter_ weitergegeben werden, werden nun automatisch auf alle LINQ-Abfragen dieser Typen angewendet.

> [!TIP]
> Beachten Sie die Verwendung eines DbContext-Instanzfelds: `_tenantId` wird zum Festlegen des aktuellen Mandanten verwendet. Filter auf Modellebene verwenden den Wert der korrekten Kontextinstanz, d.h. der Instanz, die die Abfrage ausführt.

> [!NOTE]
> Es ist derzeit nicht möglich, mehrere Abfragefilter für dieselbe Entität zu definieren, nur der letzte wird angewendet. Mithilfe des logischen _AND_-Operators ([`&&` in C#](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)) können jedoch einen einzelnen Filter mit mehreren Bedingungen definieren.

## <a name="use-of-navigations"></a>Verwenden von Navigationselementen

Navigationselemente können beim Definieren lokaler Abfragefilter verwendet werden. Diese werden rekursiv angewendet: Wenn Navigationselemente in Abfragefiltern übersetzt werden, werden die in referenzierten Entitäten definierten Abfragefilter ebenfalls angewendet, wodurch möglicherweise weitere Navigationselemente hinzugefügt werden.

> [!NOTE]
> Derzeit ermittelt EF Core keine Zyklen in globalen Abfragefilterdefinitionen. Daher sollten Sie bei der Definition vorsichtig vorgehen. Wenn diese fehlerhaft angegeben wird, können Endlosschleifen bei der Abfrageübersetzung auftreten.

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a>Zugreifen auf Entitäten mit Abfragefilter mithilfe erforderlicher Navigationselemente

> [!CAUTION]
> Die Verwendung erforderlicher Navigationselemente für den Zugriff auf eine Entität mit einem definierten globalen Abfragefilter kann zu unerwarteten Ergebnissen führen. 

Die erforderlichen Navigationselemente erwarten, dass die zugehörige Entität immer vorhanden ist. Wenn erforderliche verwandte Entitäten vom Abfragefilter herausgefiltert werden, könnte die übergeordnete Entität einen unerwarteten Status aufweisen. Dies kann dazu führen, dass weniger Elemente als erwartet zurückgegeben werden. 

Zur Veranschaulichung dieses Problems können die oben angegebenen Entitäten `Blog` und `Post` mit der _OnModelCreating_-Methode verwendet werden:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

Das Modell kann mit den folgenden Daten eingerichtet werden:

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

Das Problem tritt auf, wenn zwei Abfragen ausgeführt werden:

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

Bei diesem Setup gibt die erste Abfrage alle sechs `Post`-Anfragen zurück. Die zweite Abfrage gibt jedoch nur drei zurück. Das liegt daran, dass die _Include_-Methode in der zweiten Abfrage die zugehörigen `Blog`-Entitäten lädt. Das die Navigation zwischen `Blog` und `Post` erforderlich ist, nutzt EF Core `INNER JOIN` beim Erstellen der Abfrage:

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

Bei Verwendung von `INNER JOIN` werden alle `Post`-Vorgänge herausgefiltert, deren zugehörigen `Blog`-Entitäten von einem globalen Abfragefilter entfernt wurden. 

Dieses Problem können Sie mithilfe optionaler Navigationselemente anstelle erforderlicher umgehen. Auf diese Weise bleibt die erste Abfrage unverändert, die zweite Abfrage generiert nun jedoch `LEFT JOIN` und gibt sechs Ergebnisse zurück.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

Der alternative Ansatz besteht darin, konsistente Filter für die beiden Entitäten `Blog` und `Post` festzulegen.
Auf diese Weise werden entsprechende Filter auf `Blog` und `Post` angewendet. `Post`-Entitäten, die einen unerwarteten Status aufweisen könnten, werden entfernt und beide Abfragen geben drei Ergebnisse zurück. 

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a>Deaktivieren von Filtern

Filter können für einzelne LINQ-Abfragen mit dem `IgnoreQueryFilters()`-Operator deaktiviert werden.

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>Einschränkungen

Globale Abfragefilter unterliegen den folgenden Einschränkungen:

* Filter können nur für den Stammentitätstyp einer Vererbungshierarchie definiert werden.
