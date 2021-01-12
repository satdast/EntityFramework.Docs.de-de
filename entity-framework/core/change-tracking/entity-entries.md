---
title: Zugreifen auf nach verfolgte Entitäten-EF Core
description: Verwenden von entityentry, dbcontext. Entries und dbset. local für den Zugriff auf nach verfolgte Entitäten
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/entity-entries
ms.openlocfilehash: f385016aba61535f33e34c622dd43ce6dc823fc5
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129593"
---
# <a name="accessing-tracked-entities"></a>Zugreifen auf verfolgte Entitäten

Es gibt vier Haupt-APIs für den Zugriff auf Entitäten, die von verfolgt werden <xref:Microsoft.EntityFrameworkCore.DbContext> :

- <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> gibt eine- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> Instanz für eine angegebene Entitäts Instanz zurück.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType> gibt- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> Instanzen für alle nach verfolgten Entitäten oder für alle nach verfolgten Entitäten eines bestimmten Typs zurück.
- <xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> Suchen nach einer einzelnen Entität nach Primärschlüssel, suchen zuerst nach überwachten Entitäten und dann nach Bedarf eine Abfrage der Datenbank.
- <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> gibt tatsächliche Entitäten (keine entityentry-Instanzen) für Entitäten des Entitäts Typs zurück, der durch das dbset dargestellt wird.

Diese werden in den folgenden Abschnitten ausführlicher beschrieben.

> [!TIP]
> In diesem Dokument wird davon ausgegangen, dass die Entitäts Zustände und die Grundlagen EF Core Änderungs Nachverfolgung verstanden werden. Weitere Informationen zu diesen Themen finden Sie [unter Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) .

> [!TIP]
> Sie können den gesamten Code in diesem Dokument ausführen und Debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AccessingTrackedEntities).

## <a name="using-dbcontextentry-and-entityentry-instances"></a>Verwenden von dbcontext. Entry-und entityentry-Instanzen

Für jede überwachte Entität verfolgt Entity Framework Core (EF Core) Folgendes:

- Der Gesamtzustand der Entität. Dies ist eine von `Unchanged` , `Modified` , `Added` oder `Deleted` . Weitere Informationen finden Sie [unter Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) .
- Die Beziehungen zwischen überwachten Entitäten. Beispielsweise der Blog, zu dem ein Beitrag gehört.
- Die "aktuellen Werte" von Eigenschaften.
- Die "ursprünglichen Werte" der Eigenschaften, wenn diese Informationen verfügbar sind. Ursprüngliche Werte sind die Eigenschaftswerte, die vorhanden waren, als die Entität von der Datenbank abgefragt wurde.
- Welche Eigenschaftswerte geändert wurden, seit Sie abgefragt wurden.
- Weitere Informationen zu Eigenschafts Werten, z. b. ob der Wert [temporär](xref:core/change-tracking/miscellaneous#temporary-values)ist oder nicht.

Das übergeben einer Entitäts Instanz an führt dazu, <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> dass ein Zugriff auf diese Informationen für die angegebene Entität bereitstellt. Beispiel:

<!--
        using var context = new BlogsContext();

        var blog = context.Blogs.Single(e => e.Id == 1);
        var entityEntry = context.Entry(blog);

-->
[!code-csharp[Using_DbContext_Entry_and_EntityEntry_instances_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbContext_Entry_and_EntityEntry_instances_1)]

In den folgenden Abschnitten wird gezeigt, wie ein entityentry verwendet wird, um auf den Entitäts Status zuzugreifen und ihn zu bearbeiten, sowie den Zustand der Eigenschaften und Navigationen der Entität.

### <a name="working-with-the-entity"></a>Arbeiten mit der Entität

Die häufigste Verwendung von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> ist der Zugriff auf den aktuellen <xref:Microsoft.EntityFrameworkCore.EntityState> einer Entität. Beispiel:

<!--
        var currentState = context.Entry(blog).State;
        if (currentState == EntityState.Unchanged)
        {
            context.Entry(blog).State = EntityState.Modified;
        }
-->
[!code-csharp[Work_with_the_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_1)]

Die Entry-Methode kann auch für Entitäten verwendet werden, die noch nicht nachverfolgt wurden. Dadurch _wird die Nachverfolgung der Entität nicht gestartet_. der Zustand der Entität ist weiterhin `Detatched` . Der zurückgegebene entityentry kann jedoch verwendet werden, um den Entitäts Status zu ändern. an diesem Punkt wird die Entität im angegebenen Zustand nachverfolgt. Mit dem folgenden Code wird beispielsweise die Nachverfolgung einer Blog Instanz wie folgt gestartet `Added` :

<!--
        var newBlog = new Blog();
        Debug.Assert(context.Entry(newBlog).State == EntityState.Detached);

        context.Entry(newBlog).State = EntityState.Added;
        Debug.Assert(context.Entry(newBlog).State == EntityState.Added);
-->
[!code-csharp[Work_with_the_entity_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_2)]

> [!TIP]
> Anders als bei EF6 führt das Festlegen des Zustands einer einzelnen Entität nicht dazu, dass alle verbundenen Entitäten nachverfolgt werden. Dies bewirkt, dass der Zustand auf die gleiche Weise als ein Vorgang auf niedrigerer Ebene festgelegt wird, der `Add` `Attach` `Update` auf ein gesamtes Diagramm von Entitäten angewendet wird.

In der folgenden Tabelle sind die Möglichkeiten für die Verwendung eines entityentry zum Arbeiten mit einer gesamten Entität zusammengefasst:

| Entityentry-Member                                                                                         | BESCHREIBUNG
|:-----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.State?displayProperty=nameWithType>         | Ruft den der Entität ab und legt ihn fest <xref:Microsoft.EntityFrameworkCore.EntityState> .
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Entity?displayProperty=nameWithType>        | Ruft die Entitäts Instanz ab.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Context?displayProperty=nameWithType>       | Der <xref:Microsoft.EntityFrameworkCore.DbContext> , der diese Entität nachverfolgt.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Metadata?displayProperty=nameWithType>      | <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> Metadaten für den Entitätstyp.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.IsKeySet?displayProperty=nameWithType>      | Gibt an, ob für die Entität der Schlüsselwert festgelegt wurde.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload?displayProperty=nameWithType>        | Überschreibt Eigenschaftswerte mit aus der Datenbank gelesenen Werten.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> | Erzwingt nur das Erkennen von Änderungen für diese Entität. siehe [Änderungs Erkennung und Benachrichtigungen](xref:core/change-tracking/change-detection).

### <a name="working-with-a-single-property"></a>Arbeiten mit einer einzelnen Eigenschaft

Mehrere über Ladungen von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> ermöglichen den Zugriff auf Informationen über eine einzelne Eigenschaft einer Entität. Verwenden Sie beispielsweise eine stark typisierte, fließend ähnliche API:

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property(e => e.Name);
-->
[!code-csharp[Work_with_a_single_property_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1a)]

Der Eigenschaftsname kann stattdessen als Zeichenfolge übermittelt werden. Beispiel:

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property<string>("Name");
-->
[!code-csharp[Work_with_a_single_property_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1b)]

Der zurückgegebene <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> kann dann verwendet werden, um auf Informationen über die Eigenschaft zuzugreifen. Beispielsweise kann Sie verwendet werden, um den aktuellen Wert der-Eigenschaft für diese Entität zu erhalten und festzulegen:

<!--
            string currentValue = context.Entry(blog).Property(e => e.Name).CurrentValue;
            context.Entry(blog).Property(e => e.Name).CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1d)]

Beide oben verwendeten Eigenschaften Methoden geben eine stark typisierte generische Instanz zurück <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> . Die Verwendung dieses generischen Typs wird bevorzugt, da Sie den Zugriff auf Eigenschaftswerte ohne [Boxing-Werttypen](/dotnet/csharp/programming-guide/types/boxing-and-unboxing)zulässt. Wenn der Typ der Entität oder Eigenschaft jedoch zur Kompilierzeit nicht bekannt ist, kann stattdessen ein nicht generischer Wert <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> abgerufen werden:

<!--
            var propertyEntry = context.Entry(blog).Property("Name");
-->
[!code-csharp[Work_with_a_single_property_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1c)]

Dies ermöglicht den Zugriff auf Eigenschafts Informationen für jede Eigenschaft, unabhängig vom Typ, auf der Kosten der Boxing-Werttypen. Beispiel:

<!--
            object blog = context.Blogs.Single(e => e.Id == 1);

            object currentValue = context.Entry(blog).Property("Name").CurrentValue;
            context.Entry(blog).Property("Name").CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1e)]

In der folgenden Tabelle werden die von PropertyEntry verfügbar gemachten Eigenschaften Informationen zusammengefasst:

| PropertyEntry-Member                               | BESCHREIBUNG
|:-------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.CurrentValue?displayProperty=nameWithType>  | Ruft den aktuellen Wert der-Eigenschaft ab und legt diesen fest.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.OriginalValue?displayProperty=nameWithType> | Ruft den ursprünglichen Wert der Eigenschaft ab, sofern verfügbar, oder legt ihn fest.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.EntityEntry?displayProperty=nameWithType>   | Ein Rück Verweis auf die <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> für die Entität.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.Metadata?displayProperty=nameWithType>          | <xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> Metadaten für die Eigenschaft.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified?displayProperty=nameWithType>        | Gibt an, ob diese Eigenschaft als geändert markiert ist, und ermöglicht es, diesen Zustand zu ändern.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary?displayProperty=nameWithType>       | Gibt an, ob diese Eigenschaft als [temporär](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values)markiert ist, und ermöglicht es, diesen Zustand zu ändern.

Hinweise:

- Der ursprüngliche Wert einer Eigenschaft ist der Wert, den die Eigenschaft enthielt, als die Entität von der Datenbank abgefragt wurde. Ursprüngliche Werte sind jedoch nicht verfügbar, wenn die Entität getrennt und dann explizit an einen anderen dbcontext angefügt wurde, `Attach` z `Update` . b. mit oder. In diesem Fall ist der ursprüngliche Wert, der zurückgegeben wird, mit dem aktuellen Wert identisch.
- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aktualisiert nur Eigenschaften, die als geändert markiert sind. Legen Sie <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified> EF Core diese Eigenschaft auf "true" fest, um EF Core zu erzwingen, dass ein bestimmter Eigenschafts Wert aktualisiert wird.
- [Temporäre Werte](xref:core/change-tracking/miscellaneous) werden in der Regel von EF Core [Wert Generatoren](xref:core/modeling/generated-properties)generiert. Wenn Sie den aktuellen Wert einer Eigenschaft festlegen, wird der temporäre Wert durch den angegebenen Wert ersetzt, und die Eigenschaft wird als nicht temporär markiert. Legen Sie diese Einstellung auf "true" fest <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary> , um zu erzwingen, dass ein Wert temporär ist, auch wenn er explizit

### <a name="working-with-a-single-navigation"></a>Arbeiten mit einer einzelnen Navigation

Mehrere über Ladungen von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> ermöglichen den Zugriff auf Informationen über eine einzelne Navigation.

Der Zugriff auf die Verweis Navigation zu einer einzelnen verknüpften Entität erfolgt über die- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> Methoden. Die Verweis Navigation verweist auf die 1-Seite der 1: n-Beziehungen und beide Seiten von 1: eins-Beziehungen. Beispiel:

<!--
        ReferenceEntry<Post, Blog> referenceEntry1 = context.Entry(post).Reference(e => e.Blog);
        ReferenceEntry<Post, Blog> referenceEntry2 = context.Entry(post).Reference<Blog>("Blog");
        ReferenceEntry referenceEntry3 = context.Entry(post).Reference("Blog");
-->
[!code-csharp[Work_with_a_single_navigation_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_1)]

Navigationen können auch Auflistungen verwandter Entitäten sein, wenn Sie für die "vielen" Seiten von 1: n-und m:n-Beziehungen verwendet werden. Die- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> Methoden werden für den Zugriff auf Auflistungs Navigation verwendet. Beispiel:

<!--
        CollectionEntry<Blog, Post> collectionEntry1 = context.Entry(blog).Collection(e => e.Posts);
        CollectionEntry<Blog, Post> collectionEntry2 = context.Entry(blog).Collection<Post>("Posts");
        CollectionEntry collectionEntry3 = context.Entry(blog).Collection("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2a)]

Einige Vorgänge sind für alle Navigationen üblich. Auf diese kann über die-Methode sowohl für Verweis-als auch Auflistungs Navigation zugegriffen werden <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> . Beachten Sie, dass nur nicht generischer Zugriff verfügbar ist, wenn Sie auf alle Navigations Zugriffen zugreifen. Beispiel:

<!--
        NavigationEntry navigationEntry = context.Entry(blog).Navigation("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2b)]

In der folgenden Tabelle sind die Verwendungsmöglichkeiten von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602> , und zusammengefasst <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> :

| Navigationentry-Member                                                                                    | BESCHREIBUNG
|:----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.MemberEntry.CurrentValue?displayProperty=nameWithType> | Ruft den aktuellen Wert der Navigation ab und legt diesen fest. Dies ist die gesamte Sammlung für Auflistungs Navigation.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Metadata?displayProperty=nameWithType> | <xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> Metadaten für die Navigation.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.IsLoaded?displayProperty=nameWithType> | Dient zum Abrufen oder Festlegen eines Werts, der angibt, ob die verknüpfte Entität oder Auflistung vollständig aus der Datenbank geladen wurde.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Load?displayProperty=nameWithType>     | Lädt die verknüpfte Entität oder Auflistung aus der Datenbank. siehe [Explizites Laden verwandter Daten](xref:core/querying/related-data/explicit).
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Query?displayProperty=nameWithType>    | Die Abfrage EF Core würde verwenden, um diese Navigation als zu laden `IQueryable` , der weiter zusammengesetzt werden kann. siehe [Explizites Laden](xref:core/querying/related-data/explicit)verknüpfter Daten.

### <a name="working-with-all-properties-of-an-entity"></a>Arbeiten mit allen Eigenschaften einer Entität

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType> gibt eine <xref:System.Collections.Generic.IEnumerable%601> von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> für jede Eigenschaft der Entität zurück. Dies kann verwendet werden, um eine Aktion für jede Eigenschaft der Entität auszuführen. So legen Sie beispielsweise eine beliebige DateTime-Eigenschaft auf fest `DateTime.Now` :

<!--
        foreach (var propertyEntry in context.Entry(blog).Properties)
        {
            if (propertyEntry.Metadata.ClrType == typeof(DateTime))
            {
                propertyEntry.CurrentValue = DateTime.Now;
            }
        }
-->
[!code-csharp[Work_with_all_properties_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_1)]

Außerdem enthält entityentry mehrere Methoden, um alle Eigenschaftswerte gleichzeitig zu erhalten und festzulegen. Diese Methoden verwenden die <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> -Klasse, die eine Auflistung von Eigenschaften und deren Werten darstellt. PropertyValues können für die aktuellen oder ursprünglichen Werte oder für die Werte abgerufen werden, die derzeit in der Datenbank gespeichert sind. Beispiel:

<!--
        var currentValues = context.Entry(blog).CurrentValues;
        var originalValues = context.Entry(blog).OriginalValues;
        var databaseValues = context.Entry(blog).GetDatabaseValues();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2a)]

Diese propertyValues-Objekte sind selbst nicht sehr nützlich. Allerdings können Sie kombiniert werden, um allgemeine Vorgänge auszuführen, die beim Bearbeiten von Entitäten erforderlich sind. Dies ist nützlich, wenn Sie mit Datenübertragungs Objekten arbeiten und [Konflikte mit optimistischer](xref:core/saving/concurrency)Parallelität lösen. In den folgenden Abschnitten werden einige Beispiele gezeigt.

#### <a name="setting-current-or-original-values-from-an-entity-or-dto"></a>Festlegen aktueller oder ursprünglicher Werte aus einer Entität oder einem DTO

Die aktuellen oder ursprünglichen Werte einer Entität können aktualisiert werden, indem Werte aus einem anderen Objekt kopiert werden. Angenommen, ein `BlogDto` Datenübertragungs Objekt (Data Transfer Object, dto) mit denselben Eigenschaften wie der Entitätstyp:

<!--
public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
-->
[!code-csharp[BlogDto](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=BlogDto)]

Dies kann verwendet werden, um die aktuellen Werte einer nach verfolgten Entität mit festzulegen <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> :

<!--
        var blogDto = new BlogDto { Id = 1, Name = "1unicorn2" };

        context.Entry(blog).CurrentValues.SetValues(blogDto);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2b)]

Diese Technik wird manchmal beim Aktualisieren einer Entität mit Werten verwendet, die von einem Dienst-oder Client in einer n-Tier-Anwendung abgerufen werden. Beachten Sie, dass das verwendete Objekt nicht denselben Typ wie die Entität aufweisen muss, solange es über Eigenschaften verfügt, deren Namen mit denen der Entität übereinstimmen. Im obigen Beispiel wird eine Instanz des DTO-Werts `BlogDto` verwendet, um die aktuellen Werte einer nach verfolgten `Blog` Entität festzulegen.

Beachten Sie, dass Eigenschaften nur als geändert gekennzeichnet werden, wenn sich der festgelegte Wert vom aktuellen Wert unterscheidet.

#### <a name="setting-current-or-original-values-from-a-dictionary"></a>Festlegen aktueller oder ursprünglicher Werte aus einem Wörterbuch

Im vorherigen Beispiel wurden Werte aus einer Entität oder einer DTO-Instanz festgelegt. Das gleiche Verhalten ist verfügbar, wenn Eigenschaftswerte als Name-Wert-Paare in einem Wörterbuch gespeichert werden. Beispiel:

<!--
        var blogDictionary = new Dictionary<string, object>
        {
            ["Id"] = 1,
            ["Name"] = "1unicorn2"
        };

        context.Entry(blog).CurrentValues.SetValues(blogDictionary);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2d)]

#### <a name="setting-current-or-original-values-from-the-database"></a>Festlegen aktueller oder ursprünglicher Werte aus der Datenbank

Die aktuellen oder ursprünglichen Werte einer Entität können mit den neuesten Werten aus der Datenbank aktualisiert werden, indem <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> oder aufgerufen <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> und das zurückgegebene-Objekt verwendet wird, um aktuelle oder ursprüngliche Werte festzulegen, oder beides. Beispiel:

<!--
        var databaseValues = context.Entry(blog).GetDatabaseValues();
        context.Entry(blog).CurrentValues.SetValues(databaseValues);
        context.Entry(blog).OriginalValues.SetValues(databaseValues);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2c)]

#### <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a>Erstellen eines geklonten Objekts mit aktuellen, ursprünglichen oder Daten bankwerten

Das propertyValues-Objekt, das von CurrentValues, OriginalValues oder getdatabasevalues zurückgegeben wird, kann verwendet werden, um mithilfe von einen Klon der Entität zu erstellen <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType> . Beispiel:

<!--
var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2e)]

Beachten Sie, dass `ToObject` eine neue-Instanz zurückgibt, die nicht von dbcontext nachverfolgt wird. Für das zurückgegebene Objekt sind auch keine Beziehungen auf andere Entitäten festgelegt.

Das geklonte Objekt kann nützlich sein, um Probleme im Zusammenhang mit gleichzeitigen Aktualisierungen der Datenbank zu beheben, insbesondere bei der Datenbindung an Objekte eines bestimmten Typs. Weitere [Informationen finden Sie](xref:core/saving/concurrency) unter vollständige Parallelität.

### <a name="working-with-all-navigations-of-an-entity"></a>Arbeiten mit allen Navigationen einer Entität

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType> Gibt einen <xref:System.Collections.Generic.IEnumerable%601> von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> für jede Navigation der Entität zurück. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> führen Sie dasselbe aus, aber beschränkt auf Verweis-bzw. Sammlungs Navigation. Dies kann verwendet werden, um eine Aktion für jede Navigation der Entität auszuführen. So erzwingen Sie z. b. das Laden aller zugehörigen Entitäten:

<!--
        foreach (var navigationEntry in context.Entry(blog).Navigations)
        {
            navigationEntry.Load();
        }
-->
[!code-csharp[Work_with_all_navigations_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_navigations_of_an_entity_1)]

### <a name="working-with-all-members-of-an-entity"></a>Arbeiten mit allen Membern einer Entität

Reguläre Eigenschaften und Navigations Eigenschaften haben unterschiedliche Zustände und Verhalten. Es ist daher üblich, Navigationen und nicht-Navigationen separat zu verarbeiten, wie in den obigen Abschnitten gezeigt. Manchmal kann es jedoch nützlich sein, etwas mit einem beliebigen Member der Entität durchzuführen, unabhängig davon, ob es sich um eine reguläre Eigenschaft oder Navigation handelt. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType> werden zu diesem Zweck bereitgestellt. Beispiel:

<!--
        foreach (var memberEntry in context.Entry(blog).Members)
        {
            Console.WriteLine(
                $"Member {memberEntry.Metadata.Name} is of type {memberEntry.Metadata.ClrType.ShortDisplayName()} and has value {memberEntry.CurrentValue}");
        }
-->
[!code-csharp[Work_with_all_members_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_members_of_an_entity_1)]

Wenn Sie diesen Code in einem Blog aus dem Beispiel ausführen, wird die folgende Ausgabe generiert:

```output
Member Id is of type int and has value 1
Member Name is of type string and has value .NET Blog
Member Posts is of type IList<Post> and has value System.Collections.Generic.List`1[Post]
```

> [!TIP]
> In der [Debugansicht Änderungs nach](xref:core/change-tracking/debug-views) Verfolgung werden Informationen wie diese angezeigt. Die Debugansicht für die gesamte Änderungs Nachverfolgung wird von der einzelnen einzelnen nach <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> verfolgten Entität generiert.

## <a name="find-and-findasync"></a>Suchen und FindAsync

<xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> sind für die effiziente Suche einer einzelnen Entität konzipiert, wenn der primäre Schlüssel bekannt ist. Sucht zuerst nach der Überprüfung, ob die Entität bereits nachverfolgt wird, und gibt dann die Entität sofort zurück Eine Datenbankabfrage wird nur vorgenommen, wenn die Entität nicht lokal nachverfolgt wird. Sehen Sie sich beispielsweise den folgenden Code an, der die Suche zweimal für dieselbe Entität aufruft:

<!--
        using var context = new BlogsContext();

        Console.WriteLine("First call to Find...");
        var blog1 = context.Blogs.Find(1);

        Console.WriteLine($"...found blog {blog1.Name}");

        Console.WriteLine();
        Console.WriteLine("Second call to Find...");
        var blog2 = context.Blogs.Find(1);
        Debug.Assert(blog1 == blog2);

        Console.WriteLine("...returned the same instance without executing a query.");
-->
[!code-csharp[Find_and_FindAsync_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_1)]

Die Ausgabe dieses Codes (einschließlich EF Core Protokollierung) bei Verwendung von SQLite lautet wie folgt:

```output
First call to Find...
info: 12/29/2020 07:45:53.682 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (1ms) [Parameters=[@__p_0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
      SELECT "b"."Id", "b"."Name"
      FROM "Blogs" AS "b"
      WHERE "b"."Id" = @__p_0
      LIMIT 1
...found blog .NET Blog

Second call to Find...
...returned the same instance without executing a query.
```

Beachten Sie, dass der erste-Befehl die Entität nicht lokal findet und somit eine Datenbankabfrage ausführt. Umgekehrt gibt der zweite-Befehl dieselbe Instanz zurück, ohne die Datenbank abzufragen, weil Sie bereits nachverfolgt wird.

Find gibt NULL zurück, wenn eine Entität mit dem angegebenen Schlüssel nicht lokal nachverfolgt wird und nicht in der Datenbank vorhanden ist.

### <a name="composite-keys"></a>Zusammengesetzte Schlüssel

Find kann auch mit zusammengesetzten Schlüsseln verwendet werden. Stellen Sie sich beispielsweise eine `OrderLine` Entität mit einem zusammengesetzten Schlüssel vor, der aus der Bestell-ID und der Produkt-ID besteht:

<!--
public class OrderLine
{
    public int OrderId { get; set; }
    public int ProductId { get; set; }

    //...
}
-->
[!code-csharp[OrderLine](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OrderLine)]

Der zusammengesetzte Schlüssel muss in konfiguriert werden <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> , um die Schlüsselteile _und deren Reihenfolge_ zu definieren. Beispiel:

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<OrderLine>()
            .HasKey(e => new { e.OrderId, e.ProductId });
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OnModelCreating)]

Beachten Sie, dass `OrderId` der erste Teil des Schlüssels und `ProductId` der zweite Teil des Schlüssels ist. Diese Reihenfolge muss beim Übergeben von Schlüsselwerten für die Suche verwendet werden. Beispiel:

<!--
        var orderline = context.OrderLines.Find(orderId, productId);
-->
[!code-csharp[Find_and_FindAsync_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_2)]

## <a name="using-changetrackerentries-to-access-all-tracked-entities"></a>Verwenden von ChangeTracker. Entries für den Zugriff auf alle nach verfolgten Entitäten

Bisher haben wir nur jeweils ein einzelnes-Paar aufgerufen <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> . <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> Gibt einen entityentry für jede Entität zurück, die derzeit von dbcontext nachverfolgt wird. Beispiel:

<!--
        using var context = new BlogsContext();
        var blogs = context.Blogs.Include(e => e.Posts).ToList();

        foreach (var entityEntry in context.ChangeTracker.Entries())
        {
            Console.WriteLine($"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property("Id").CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a)]

Dieser Code generiert die folgende Ausgabe:

```output
Found Blog entity with ID 1
Found Post entity with ID 1
Found Post entity with ID 2
```

Beachten Sie, dass Einträge für beide Blogs und Beiträge zurückgegeben werden. Die Ergebnisse können stattdessen mithilfe der generischen Überladung zu einem bestimmten Entitätstyp gefiltert werden <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> :

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<Post>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b)]

Die Ausgabe dieses Codes zeigt, dass nur Beiträge zurückgegeben werden:

```output
Found Post entity with ID 1
Found Post entity with ID 2
```

Außerdem gibt die generische Überladung generische <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> Instanzen zurück. Dies ermöglicht es, dass in diesem Beispiel ein fließend ähnlicher Zugriff auf die-Eigenschaft möglich ist `Id` .

Der zum Filtern verwendete generische Typ muss kein zugeordneter Entitätstyp sein. Stattdessen kann ein nicht zugeordneter Basistyp oder eine nicht zugeordnete Schnittstelle verwendet werden Wenn z. b. alle Entitäts Typen im Modell eine Schnittstelle implementieren, die ihre Schlüsseleigenschaft definiert:

<!--
public interface IEntityWithKey
{
    int Id { get; set; }
}
-->
[!code-csharp[IEntityWithKey](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=IEntityWithKey)]

Diese Schnittstelle kann dann verwendet werden, um mit dem Schlüssel einer nach verfolgten Entität in einer stark typisierten Weise zu arbeiten. Beispiel:

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<IEntityWithKey>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c)]

## <a name="using-dbsetlocal-to-query-tracked-entities"></a>Abfragen von nach verfolgten Entitäten mithilfe von "dbset. local"

EF Core Abfragen werden stets für die Datenbank ausgeführt, und es werden nur Entitäten zurückgegeben, die in der Datenbank gespeichert wurden. <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> stellt einen Mechanismus zum Abfragen von dbcontext für lokale, nach verfolgte Entitäten bereit.

Da `DbSet.Local` verwendet wird, um nach verfolgte Entitäten abzufragen, ist es typisch, Entitäten in den dbcontext zu laden und dann mit diesen geladenen Entitäten zu arbeiten. Dies gilt insbesondere für die Datenbindung, kann aber auch in anderen Situationen nützlich sein. Im folgenden Code wird die Datenbank z. b. zuerst nach allen Blogs und Beiträgen abgefragt. Die- <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A> Erweiterungsmethode wird verwendet, um diese Abfrage mit den vom Kontext nach verfolgten Ergebnissen auszuführen, ohne direkt an die Anwendung zurückgegeben zu werden. (Die Verwendung `ToList` von oder ähnlichem hat denselben Effekt, aber mit dem Aufwand zum Erstellen der zurückgegebenen Liste, der hier nicht benötigt wird.) Im Beispiel wird dann verwendet `DbSet.Local` , um auf die lokal überwachten Entitäten zuzugreifen:

<!--
        using var context = new BlogsContext();

        context.Blogs.Include(e => e.Posts).Load();

        foreach (var blog in context.Blogs.Local)
        {
            Console.WriteLine($"Blog: {blog.Name}");
        }

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_1)]

Beachten Sie, dass im Gegensatz zu <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> `DbSet.Local` Entitäts Instanzen direkt zurückgibt. Ein entityentry kann natürlich immer für die zurückgegebene Entität abgerufen werden, indem aufgerufen wird <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> .

### <a name="the-local-view"></a>Die lokale Ansicht

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> gibt eine Ansicht der lokal überwachten Entitäten zurück, die die aktuellen <xref:Microsoft.EntityFrameworkCore.EntityState> dieser Entitäten widerspiegeln. Dies bedeutet insbesondere Folgendes:

- `Added` Entitäten sind eingeschlossen. Beachten Sie, dass dies für normale EF Core Abfragen nicht der Fall ist, da `Added` Entitäten noch nicht in der Datenbank vorhanden sind und daher nie von einer Datenbankabfrage zurückgegeben werden.
- `Deleted` Entitäten werden ausgeschlossen. Beachten Sie, dass dies für normale EF Core Abfragen noch nicht der Fall ist, da `Deleted` Entitäten in der Datenbank noch vorhanden sind und von Datenbankabfragen zurückgegeben _werden_ .

Dies bedeutet, dass `DbSet.Local` die Daten anzeigen, die den aktuellen konzeptionellen Zustand des Entitäts Diagramms widerspiegeln, wobei `Added` enthaltene Entitäten und `Deleted` Entitäten ausgeschlossen sind. Dies entspricht dem Namen des Daten Bank Zustands, der nach dem Aufruf von SaveChanges erwartet wird.

Dies ist in der Regel die ideale Ansicht für die Datenbindung, da dem Benutzer die Daten entsprechend den von der Anwendung vorgenommenen Änderungen angezeigt werden.

Der folgende Code zeigt, wie ich einen Beitrag als markiere `Deleted` und dann einen neuen Beitrag zufüge. `Added`

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Remove(posts[1]);

        context.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_2)]

Die Ausgabe dieses Codes lautet wie folgt:

```output
Local view after loading posts:
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing F# 5
  Post: Announcing .NET 5.0
Local view after adding and deleting posts:
  Post: What’s next for System.Text.Json?
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing .NET 5.0
```

Beachten Sie, dass der gelöschte Beitrag aus der lokalen Ansicht entfernt wird und der hinzugefügte Beitrag enthalten ist.

### <a name="using-local-to-add-and-remove-entities"></a>Verwenden von local zum Hinzufügen und Entfernen von Entitäten

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> gibt eine Instanz von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601> zurück. Dies ist eine Implementierung von <xref:System.Collections.Generic.ICollection%601> , die Benachrichtigungen generiert und antwortet, wenn Entitäten hinzugefügt und aus der Auflistung entfernt werden. (Dies ist das gleiche Konzept wie <xref:System.Collections.ObjectModel.ObservableCollection%601> , wird jedoch als Projektion über vorhandene EF Core Änderungs nach Verfolgungs Einträge implementiert, nicht als unabhängige Sammlung.)

Die Benachrichtigungen der lokalen Ansicht werden mit der dbcontext-Änderungs Nachverfolgung verknüpft, sodass die lokale Ansicht mit dbcontext synchron bleibt. Dies gilt insbesondere in folgenden Fällen:

- Wenn eine neue Entität hinzugefügt `DbSet.Local` wird, wird Sie von dbcontext nachverfolgt, in der Regel im- `Added` Zustand. (Wenn die Entität bereits einen generierten Schlüsselwert aufweist, wird Sie stattdessen als nachverfolgt `Unchanged` .)
- Wenn eine Entität aus entfernt `DbSet.Local` wird, wird Sie als gekennzeichnet `Deleted` .
- Eine Entität, die von dbcontext nachverfolgt wird, wird automatisch in der Auflistung angezeigt `DbSet.Local` . Wenn Sie z. b. eine Abfrage ausführen, um weitere Entitäten zu aktivieren, wird die lokale Ansicht aktualisiert.
- Eine Entität, die als markiert ist, `Deleted` wird automatisch aus der lokalen Auflistung entfernt.

Dies bedeutet, dass die lokale Ansicht verwendet werden kann, um überwachte Entitäten einfach durch Hinzufügen und entfernen aus der Auflistung zu bearbeiten. Beispielsweise können Sie den vorherigen Beispielcode ändern, um Beiträge in der lokalen Sammlung hinzuzufügen und zu entfernen:

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Posts.Local.Remove(posts[1]);

        context.Posts.Local.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_3](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_3)]

Die Ausgabe bleibt unverändert im vorherigen Beispiel, da an der lokalen Ansicht vorgenommene Änderungen mit dbcontext synchronisiert werden.

### <a name="using-the-local-view-for-windows-forms-or-wpf-data-binding"></a>Verwenden der lokalen Ansicht für Windows Forms-oder WPF-Datenbindung

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> bildet die Basis für die Datenbindung an EF Core Entitäten. Allerdings funktionieren sowohl Windows Forms als auch WPF am besten, wenn Sie mit dem spezifischen Typ der Benachrichtigungs Sammlung verwendet werden, die Sie erwarten. Die lokale Ansicht unterstützt das Erstellen dieser speziellen Sammlungs Typen:

- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType> Gibt einen <xref:System.Collections.ObjectModel.ObservableCollection%601> für die WPF-Datenbindung zurück.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType> Gibt einen <xref:System.ComponentModel.BindingList%601> für Windows Forms Datenbindung zurück.

Beispiel:

<!--
        ObservableCollection<Post> observableCollection = context.Posts.Local.ToObservableCollection();
        BindingList<Post> bindingList = context.Posts.Local.ToBindingList();
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_4](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_4)]

Weitere Informationen zur WPF-Datenbindung mit EF Core finden [Sie unter Get Started with WPF](xref:core/get-started/wpf) .

> [!TIP]
> Die lokale Ansicht für eine bestimmte dbset-Instanz wird verzögert erstellt, wenn zuerst darauf zugegriffen und dann zwischengespeichert wird. Die localview-Erstellung selbst ist schnell und verwendet keinen signifikanten Arbeitsspeicher. Es wird jedoch " [DetectChanges](xref:core/change-tracking/change-detection)" aufgerufen, was für eine große Anzahl von Entitäten langsam sein kann. Die von und erstellten `ToObservableCollection` Sammlungen `ToBindingList` werden ebenfalls verzögert und dann zwischengespeichert. Beide Methoden erstellen neue Auflistungen, die langsam sein können und viel Arbeitsspeicher nutzen, wenn Tausende von Entitäten beteiligt sind.
