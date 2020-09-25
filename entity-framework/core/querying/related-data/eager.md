---
title: Eager Loading zugehöriger Daten – EF Core
description: Eager Loading zugehöriger Daten mit Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: f7e33895293e1d7b8b32a135d60b58dd1f050621
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078868"
---
# <a name="eager-loading-of-related-data"></a>Eager Loading zugehöriger Daten

## <a name="eager-loading"></a>Eager Loading

Sie können mit der `Include`-Methode zugehörige Daten angeben, die in den Abfrageergebnissen enthalten sein sollen. Im folgenden Beispiel weisen die in den Ergebnissen zurückgegebenen Blogs ihre `Posts`-Eigenschaft auf, die mit den zugehörigen Beiträgen aufgefüllt wurden.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]
> Entity Framework Core korrigiert automatisch Navigationseigenschaften zur Korrektur für alle anderen Entitäten, die zuvor in die Kontextinstanz geladen wurden. Auch wenn Sie die Daten für eine Navigationseigenschaft nicht explizit eingeschlossen haben, kann die Eigenschaft folglich immer noch aufgefüllt werden, wenn einige oder alle zugehörigen Entitäten zuvor geladen wurden.

Sie können zugehörigen Daten aus mehreren Beziehungen in einer einzelnen Abfrage einschließen.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

## <a name="including-multiple-levels"></a>Einschließen mehrerer Ebenen

Sie können mit der `ThenInclude`-Methode einen Drilldown für Beziehungen ausführen, um mehrere Ebenen zugehöriger Daten einzuschließen. Im folgenden Beispiel werden sämtliche Blogs, die zugehörigen Beiträge und die Autoren der einzelnen Beiträge geladen.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

Sie können mehrere Aufrufe mit `ThenInclude` verbinden, um mit dem Einschließen weiterer Ebenen zugehöriger Daten fortzufahren.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

Sie können alle diese Aufrufe kombinieren, um eine gemeinsame Abfrage für verknüpfte Daten aus mehreren Ebenen und Stämmen auszuführen.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

Für eine der Entitäten, die eingeschlossen wird, sollten Sie mehrere zugehörige Entitäten einschließen. Beispiel: Beim Abfragen von `Blogs` schließen Sie `Posts` ein und möchten anschließend `Author` und `Tags` von `Posts` einschließen. Hierzu müssen Sie die einzelnen Include-Pfade beginnend beim Stamm angeben. Beispiel: `Blog -> Posts -> Author` und `Blog -> Posts -> Tags`. Dies bedeutet nicht, dass Sie redundante Verknüpfungen erhalten. In den meisten Fällen kombiniert EF die Verknüpfungen beim Generieren von SQL-Code.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

## <a name="single-and-split-queries"></a>Einzelne und geteilte Abfragen

### <a name="single-queries"></a>Einzelne Abfragen

In relationalen Datenbanken werden alle zugehörigen Entitäten standardmäßig durch Einführung von JOINs geladen:

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

Wenn ein typischer Blog mehrere zugehörige Beiträge enthält, duplizieren die Zeilen für diese Beiträge die Informationen des Blogs, was zum so genannten „Problem der kartesischen Explosion“ führt. Wenn weitere 1:n-Beziehungen geladen werden, wächst die Menge an duplizierten Daten weiter und beeinträchtigt die Leistung Ihrer Anwendung. Standardmäßig gibt EF Core eine Warnung aus, wenn Abfragen erkannt werden, die Include-Vorgänge für Sammlungen laden, die zu Leistungsproblemen führen können.

### <a name="split-queries"></a>Geteilte Abfragen

> [!NOTE]
> Diese Funktion wird in EF Core 5.0 eingeführt.

In EF können Sie angeben, dass eine bestimmte LINQ-Abfrage in auf mehrere SQL-Abfragen *aufgeteilt* werden soll. Anstelle von JOINs führen aufgeteilte Abfragen eine zusätzliche SQL-Abfrage für jede enthaltene 1:n-Navigation durch:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

Dabei wird der folgende SQL-Code erzeugt:

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> Entitäten mit 1:1-Beziehung werden immer über Verknüpfungen in dieselbe Abfrage geladen, da dies keine Auswirkungen auf die Leistung hat.

### <a name="enabling-split-queries-globally"></a>Globales Aktivieren von geteilten Abfragen

Sie können auch geteilte Abfragen als Standardeinstellung für den Kontext Ihrer Anwendung konfigurieren:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

Wenn geteilte Abfragen als Standardeinstellung konfiguriert werden, ist es dennoch möglich, bestimmte Abfragen so zu konfigurieren, dass sie als einzelne Abfragen ausgeführt werden:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

Wenn der Abfrageteilungsmodus nicht explizit festgelegt ist, weder global noch für die Abfrage, und EF Core erkennt, dass eine einzelne Abfrage mehrere Include-Vorgänge für Sammlungen lädt, wird eine Warnung ausgegeben, um auf die möglichen daraus resultierenden Leistungsprobleme hinzuweisen. Das Festlegen des Abfragemodus auf SingleQuery führt dazu, dass diese Warnung nicht generiert wird.

### <a name="characteristics-of-split-queries"></a>Merkmale von geteilten Abfragen

Zwar werden bei geteilten Abfragen die Leistungsprobleme im Zusammenhang mit Verknüpfungen und der kartesischen Explosion vermieden, allerdings weist dieser Modus auch einige Nachteile auf:

* Die meisten Datenbanken garantieren Datenkonsistenz bei einzelnen Abfragen. Bei mehreren Abfragen gibt es eine solche Garantie nicht. Wenn die Datenbank während dem Ausführen der Abfragen aktualisiert wird, sind die resultierenden Daten möglicherweise nicht konsistent. Dieses Problem können Sie minimieren, indem Sie die Abfragen mit einer serialisierbaren Transaktion oder Momentaufnahmentransaktion umschließen. Allerdings kann auch dieses Vorgehen Leistungsprobleme nach sich ziehen. Weitere Informationen finden Sie in der Dokumentation Ihrer Datenbank.
* Jede Abfrage impliziert derzeit einen zusätzlichen Netzwerkroundtrip zu Ihrer Datenbank. Durch mehrere Netzwerkroundtrips kann die Leistung beeinträchtigt werden, insbesondere, wenn die Latenz bei der Datenbank hoch ist (z. B. bei Clouddiensten).
* Zwar erlauben einige Datenbanken die gleichzeitige Nutzung der Ergebnisse mehrerer Abfragen (SQL Server mit MARS, Sqlite), aber in den meisten darf zu jedem Zeitpunkt immer nur eine Abfrage aktiv sein. Folglich müssen alle Ergebnisse früherer Abfragen im Arbeitsspeicher der Anwendung gepuffert werden, bevor spätere Abfragen ausgeführt werden. Dadurch steigen die Arbeitsspeicheranforderungen.

Leider gibt es nicht die eine perfekte Strategie zum Laden von zugehörigen Entitäten, die in allen Szenarien passt. Erwägen Sie sehr sorgfältig die Vor- und Nachteile von einzelnen und geteilten Abfragen, und wählen Sie die Variante aus, die sich für Ihre Anforderungen besser eignet.

## <a name="filtered-include"></a>Gefilterte Include-Funktion

> [!NOTE]
> Diese Funktion wird in EF Core 5.0 eingeführt.

Wenn Sie „Include“ zum Laden von zugehörigen Daten anwenden, können Sie bestimmte aufzählbare Vorgänge auf die enthaltene Sammlungsnavigation anwenden, mit denen Sie die Ergebnisse filtern und sortieren können.

Folgende Vorgänge werden unterstützt: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip` und `Take`.

Solche Vorgänge sollten auf die Sammlungsnavigation in der Lambdafunktion angewendet werden, die an die Include-Methode übergeben wird, wie im folgenden Beispiel gezeigt:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

Jede per „Include“ eingeschlossene Navigation ermöglicht nur einen eindeutigen Satz von Filtervorgängen. In Fällen, in denen mehrere Include-Vorgänge für eine bestimmte Sammlungsnavigation angewendet werden (`blog.Posts` in den folgenden Beispielen), können Filtervorgänge nur für einen von ihnen angegeben werden:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

Alternativ können identische Vorgänge für jede Navigation angewendet werden, die mehrmals über „Include“ eingeschlossen wird:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> Bei der Nachverfolgung von Abfragen können aufgrund einer [Navigationskorrektur](xref:core/querying/tracking) unerwartete Ergebnisse für gefilterte Include-Abfragen auftreten. Alle relevanten Entitäten, die zuvor abgefragt und in der Änderungsnachverfolgung gespeichert wurden, werden in den Ergebnissen der gefilterten Include-Abfrage angezeigt, auch wenn sie die Anforderungen des Filters nicht erfüllen. Verwenden Sie `NoTracking`-Abfragen, oder erstellen Sie DbContext neu, wenn Sie gefilterte Include-Abfragen in diesen Situationen nutzen.

Beispiel:

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

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
  