---
title: Eager Loading zugehöriger Daten – EF Core
description: Eager Loading zugehöriger Daten mit Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: bd9c9045c1c2707d69ee4070bea59ad8066789f3
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430104"
---
# <a name="eager-loading-of-related-data"></a>Eager Loading zugehöriger Daten

## <a name="eager-loading"></a>Eager Loading

Sie können mit der `Include`-Methode zugehörige Daten angeben, die in den Abfrageergebnissen enthalten sein sollen. Im folgenden Beispiel weisen die in den Ergebnissen zurückgegebenen Blogs ihre `Posts`-Eigenschaft auf, die mit den zugehörigen Beiträgen aufgefüllt wurden.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> Entity Framework Core korrigiert automatisch Navigationseigenschaften zur Korrektur für alle anderen Entitäten, die zuvor in die Kontextinstanz geladen wurden. Auch wenn Sie die Daten für eine Navigationseigenschaft nicht explizit eingeschlossen haben, kann die Eigenschaft folglich immer noch aufgefüllt werden, wenn einige oder alle zugehörigen Entitäten zuvor geladen wurden.

Sie können zugehörigen Daten aus mehreren Beziehungen in einer einzelnen Abfrage einschließen.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

> [!CAUTION]
> Eager Loading einer Sammlungsnavigation in einer einzelnen Abfrage kann zu Leistungsproblemen führen. Weitere Informationen finden Sie unter [Vergleich von Einzel- und geteilten Abfragen](xref:core/querying/single-split-queries).

## <a name="including-multiple-levels"></a>Einschließen mehrerer Ebenen

Sie können mit der `ThenInclude`-Methode einen Drilldown für Beziehungen ausführen, um mehrere Ebenen zugehöriger Daten einzuschließen. Im folgenden Beispiel werden sämtliche Blogs, die zugehörigen Beiträge und die Autoren der einzelnen Beiträge geladen.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

Sie können mehrere Aufrufe mit `ThenInclude` verbinden, um mit dem Einschließen weiterer Ebenen zugehöriger Daten fortzufahren.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

Sie können alle diese Aufrufe kombinieren, um eine gemeinsame Abfrage für verknüpfte Daten aus mehreren Ebenen und Stämmen auszuführen.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

Für eine der Entitäten, die eingeschlossen wird, sollten Sie mehrere zugehörige Entitäten einschließen. Beispiel: Beim Abfragen von `Blogs` schließen Sie `Posts` ein und möchten anschließend `Author` und `Tags` von `Posts` einschließen. Hierzu müssen Sie die einzelnen Include-Pfade beginnend beim Stamm angeben. Beispiel: `Blog -> Posts -> Author` und `Blog -> Posts -> Tags`. Dies bedeutet nicht, dass Sie redundante Verknüpfungen erhalten. In den meisten Fällen kombiniert EF die Verknüpfungen beim Generieren von SQL-Code.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

## <a name="filtered-include"></a>Gefilterte Include-Funktion

> [!NOTE]
> Diese Funktion wird in EF Core 5.0 eingeführt.

Wenn Sie „Include“ zum Laden zugehöriger Daten anwenden, können Sie bestimmte aufzählbare Vorgänge zur enthaltenen Sammlungsnavigation hinzufügen, mit denen Sie die Ergebnisse filtern und sortieren können.

Folgende Vorgänge werden unterstützt: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip` und `Take`.

Solche Vorgänge sollten auf die Sammlungsnavigation in der Lambdafunktion angewendet werden, die an die Include-Methode übergeben wird, wie im folgenden Beispiel gezeigt:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

Jede per „Include“ eingeschlossene Navigation ermöglicht nur einen eindeutigen Satz von Filtervorgängen. In Fällen, in denen mehrere Include-Vorgänge für eine bestimmte Sammlungsnavigation angewendet werden (`blog.Posts` in den folgenden Beispielen), können Filtervorgänge nur für einen von ihnen angegeben werden:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

Alternativ können identische Vorgänge für jede Navigation angewendet werden, die mehrmals über „Include“ eingeschlossen wird:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> Bei der Nachverfolgung von Abfragen können aufgrund einer [Navigationskorrektur](xref:core/querying/tracking) unerwartete Ergebnisse für gefilterte Include-Abfragen auftreten. Alle relevanten Entitäten, die zuvor abgefragt und in der Änderungsnachverfolgung gespeichert wurden, werden in den Ergebnissen der gefilterten Include-Abfrage angezeigt, auch wenn sie die Anforderungen des Filters nicht erfüllen. Verwenden Sie `NoTracking`-Abfragen, oder erstellen Sie DbContext neu, wenn Sie gefilterte Include-Abfragen in diesen Situationen nutzen.

Beispiel:

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

> [!NOTE]
> Bei Nachverfolgungsabfragen gilt die Navigation, auf die die gefilterte Include-Funktion angewendet wurde, als geladen. Das bedeutet, dass EF Core nicht versucht, seine Werte mit [explizitem Laden](xref:core/querying/related-data/explicit) oder [verzögertem Laden](xref:core/querying/related-data/lazy) neu zu laden, auch wenn einige Elemente noch fehlen könnten.

## <a name="include-on-derived-types"></a>Einschließen in abgeleiteten Typen

Mit `Include` und `ThenInclude` können Sie verknüpfte Daten aus einer Navigation einschließen, die nur für einen abgeleiteten Typ definiert wurde.

Betrachten Sie das folgende Modell:

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

Inhalte der `School`-Navigation für alle Personen, die Studenten sind, können mit einer Reihe von Mustern vorzeitig geladen werden:

* Mit cast

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* Mit dem Operator `as`

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* Mit einer Überladung von `Include`, die Parameter vom Typ `string` akzeptiert

  ```csharp
  context.People.Include("School").ToList()
  ```
