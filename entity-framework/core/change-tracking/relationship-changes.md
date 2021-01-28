---
title: Ändern von Fremdschlüsseln und Navigationen-EF Core
description: Ändern von Beziehungen zwischen Entitäten durch manipulieren von Fremdschlüsseln und Navigationen
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/relationship-changes
ms.openlocfilehash: b1ebe77ed29291beeef3708b603db026c38bbbec
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983611"
---
# <a name="changing-foreign-keys-and-navigations"></a>Ändern von Fremdschlüsseln und Navigationen

## <a name="overview-of-foreign-keys-and-navigations"></a>Übersicht über Fremdschlüssel und Navigationen

Beziehungen in einem Entity Framework Core (EF Core)-Modell werden mithilfe von Fremdschlüsseln (Key Keys, f) dargestellt. Ein FK besteht aus einer oder mehreren Eigenschaften der abhängigen oder untergeordneten Entität in der Beziehung. Diese abhängige/untergeordnete Entität ist einer angegebenen Prinzipal-/Parent-Entität zugeordnet, wenn die Werte der Fremdschlüssel Eigenschaften des abhängigen/untergeordneten Elements den Werten der alternativen oder Primärschlüssel Eigenschaften (PK) des Prinzipals/übergeordneten Elements entsprechen.

Fremdschlüssel sind eine gute Möglichkeit, Beziehungen in der Datenbank zu speichern und zu bearbeiten. Sie sind jedoch beim Arbeiten mit mehreren verknüpften Entitäten im Anwendungscode nicht sehr benutzerfreundlich. Aus diesem Grund ist die meisten EF Core Modelle auch "Navigationen" über die FK-Darstellung. Navigationen bilden c#-/.net Verweise zwischen Entitäts Instanzen, die die Zuordnungen widerspiegeln, die durch die Übereinstimmung von Fremdschlüssel Werten mit primären oder alternativen Schlüsselwerten

Navigationen können auf beiden Seiten der Beziehung nur auf einer Seite oder gar nicht verwendet werden, wobei nur die FK-Eigenschaft belassen wird. Die FK-Eigenschaft kann ausgeblendet werden, indem Sie eine [Schatten Eigenschaft](xref:core/modeling/shadow-properties)ist. Weitere Informationen zum Modellieren von Beziehungen finden Sie unter [Beziehungen](xref:core/modeling/relationships) .

> [!TIP]
> In diesem Dokument wird davon ausgegangen, dass die Entitäts Zustände und die Grundlagen EF Core Änderungs Nachverfolgung verstanden werden. Weitere Informationen zu diesen Themen finden Sie [unter Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) .

> [!TIP]
> Sie können den gesamten Code in dieser Dokumentation ausführen und debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangingFKsAndNavigations).

### <a name="example-model"></a>Beispielmodell

Das folgende Modell enthält vier Entitäts Typen mit Beziehungen zwischen Ihnen. Die Kommentare im Code geben an, welche Eigenschaften Fremdschlüssel, Primärschlüssel und Navigationen sind.

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public int Id { get; set; } // Primary key
    public byte[] Banner { get; set; }

    public int BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation

    public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
}

public class Tag
{
    public int Id { get; set; } // Primary key
    public string Text { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Model)]

Die drei Beziehungen in diesem Modell lauten:

- Jeder Blog kann über viele Beiträge (1: n) verfügen:
  - `Blog` ist das Prinzipal/übergeordnete Element.
  - `Post` ist das abhängige/untergeordnete Element. Sie enthält die FK-Eigenschaft `Post.BlogId` , deren Wert dem `Blog.Id` PK-Wert des zugehörigen Blogs entsprechen muss.
  - `Post.Blog` ist eine Referenz Navigation von einem Beitrag zum zugehörigen Blog. `Post.Blog` ist die umgekehrte Navigation für `Blog.Posts` .
  - `Blog.Posts` ist eine Auflistungs Navigation von einem Blog zu allen zugeordneten Beiträgen. `Blog.Posts` ist die umgekehrte Navigation für `Post.Blog` .
- Jeder Blog kann über ein Medienobjekt (eins zu eins) verfügen:
  - `Blog` ist das Prinzipal/übergeordnete Element.
  - `BlogAssets` ist das abhängige/untergeordnete Element. Sie enthält die FK-Eigenschaft `BlogAssets.BlogId` , deren Wert dem `Blog.Id` PK-Wert des zugehörigen Blogs entsprechen muss.
  - `BlogAssets.Blog` ist eine Referenz Navigation von den Assets zum zugeordneten Blog. `BlogAssets.Blog` ist die umgekehrte Navigation für `Blog.Assets` .
  - `Blog.Assets` ist eine Referenz Navigation vom Blog zu den zugeordneten Ressourcen. `Blog.Assets` ist die umgekehrte Navigation für `BlogAssets.Blog` .
- Jeder Beitrag kann über viele Tags verfügen, und jedes Tag kann über viele Beiträge verfügen (m:n):
  - M:n-Beziehungen sind eine weitere Schicht über 2 1-zu-viele-Beziehungen. M:n-Beziehungen werden weiter unten in diesem Dokument behandelt.
  - `Post.Tags` ist eine Auflistungs Navigation von einem Beitrag zu allen zugeordneten Tags. `Post.Tags` ist die umgekehrte Navigation für `Tag.Posts` .
  - `Tag.Posts` ist eine Auflistungs Navigation von einem Tag zu allen zugeordneten Beiträgen. `Tag.Posts` ist die umgekehrte Navigation für `Post.Tags` .

Weitere Informationen zum Modellieren und Konfigurieren von Beziehungen finden Sie unter [Beziehungen](xref:core/modeling/relationships) .

## <a name="relationship-fixup"></a>Beziehungs Korrektur

EF Core behält die Navigation mit Fremdschlüssel Werten bei und umgekehrt. Das heißt, wenn sich ein Fremdschlüssel Wert ändert, sodass er nun auf eine andere Prinzipal/übergeordnete Entität verweist, werden die Navigationen aktualisiert, um diese Änderung widerzuspiegeln. Wenn eine Navigation geändert wird, werden die Fremdschlüssel Werte der beteiligten Entitäten entsprechend aktualisiert, um diese Änderung widerzuspiegeln. Dies wird als "Beziehungs Korrektur" bezeichnet.

### <a name="fixup-by-query"></a>Korrektur nach Abfrage

Fixup tritt zuerst auf, wenn Entitäten von der Datenbank abgefragt werden. Die Datenbank verfügt nur über Fremdschlüssel Werte. wenn EF Core eine Entitäts Instanz aus der Datenbank erstellt, verwendet Sie die Fremdschlüssel Werte zum Festlegen der Verweis Navigation und zum Hinzufügen von Entitäten zur Auflistungs Navigation nach Bedarf. Stellen Sie sich beispielsweise eine Abfrage nach Blogs und den zugehörigen Beiträgen und Assets vor:

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .ToList();

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_1)]

Für jeden Blog wird EF Core zuerst eine- `Blog` Instanz erstellen. Wenn dann jeder Beitrag aus der Datenbank geladen wird, `Post.Blog` wird die Verweis Navigation so festgelegt, dass Sie auf den zugehörigen Blog verweist. Entsprechend wird das Posting der Auflistungs `Blog.Posts` Navigation hinzugefügt. Dasselbe geschieht mit `BlogAssets` , außer in diesem Fall sind beide Navigationen Verweise. Die `Blog.Assets` Navigation ist so festgelegt, dass Sie auf die Assets-Instanz verweist, und die `BlogAsserts.Blog` Navigation wird so festgelegt, dass Sie auf die Blog Instanz verweist.

Sehen Sie sich die [Debugansicht](xref:core/change-tracking/debug-views) für die Änderungs Nachverfolgung an, nachdem diese Abfrage zwei Blogs anzeigt, die jeweils ein Objekt und zwei Beiträge nachverfolgt haben:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

In der Debugansicht werden sowohl Schlüsselwerte als auch Navigationen angezeigt. Navigationen werden mithilfe der Primärschlüssel Werte der zugehörigen Entitäten angezeigt. `Posts: [{Id: 1}, {Id: 2}]`In der obigen Ausgabe gibt beispielsweise an, dass die Auflistungs `Blog.Posts` Navigation zwei zugehörige Beiträge mit den primär Schlüsseln 1 und 2 enthält. Entsprechend zeigt die Zeile für jeden Beitrag, der dem ersten Blog zugeordnet ist, an `Blog: {Id: 1}` , dass die `Post.Blog` Navigation auf den Blog mit dem Primärschlüssel 1 verweist.

### <a name="fixup-to-locally-tracked-entities"></a>Reparieren von lokal überwachten Entitäten

Beziehungs Korrektur erfolgt auch zwischen Entitäten, die von einer Überwachungs Abfrage zurückgegeben werden, und Entitäten, die bereits von dbcontext verfolgt wurden. Beispielsweise sollten Sie drei separate Abfragen für Blogs, Beiträge und Assets ausführen:

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var assets = context.Assets.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var posts = context.Posts.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_2)]
Wenn Sie die debugansichten erneut betrachten, werden nur die beiden Blogs nachverfolgt, nachdem die erste Abfrage durchgeführt wurde:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: []
```

Die `Blog.Assets` Referenz Navigation ist NULL, und die Auflistungs `Blog.Posts` Navigation ist leer, da derzeit keine zugeordneten Entitäten vom Kontext nachverfolgt werden.

Nach der zweiten Abfrage wurde die `Blogs.Assets` Verweis Navigation so festgeschrieben, dass Sie auf die neu verfolgten `BlogAsset` Instanzen verweist. `BlogAssets.Blog`Entsprechend sind die Referenz Navigationen so festgelegt, dass Sie auf die entsprechende bereits verfolgte `Blog` Instanz verweisen.

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: []
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
```

Nach der dritten Abfrage enthält die Auflistungs `Blog.Posts` Navigation jetzt alle zugehörigen Beiträge, und die `Post.Blog` Verweise zeigen auf die entsprechende- `Blog` Instanz:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

Dabei handelt es sich um denselben Endzustand wie mit der ursprünglichen einzelnen Abfrage, da EF Core eine aufwärtsnavigation als Entitäten korrigiert haben, auch wenn Sie aus mehreren verschiedenen Abfragen stammen.

> [!NOTE]
> Fixup bewirkt niemals, dass mehr Daten aus der Datenbank zurückgegeben werden. Es werden nur Entitäten verbunden, die bereits von der Abfrage zurückgegeben wurden oder die bereits von dbcontext nachverfolgt wurden. Weitere Informationen zum Verarbeiten von Duplikaten beim Serialisieren von Entitäten finden Sie [unter Identitäts Auflösung in EF Core](xref:core/change-tracking/identity-resolution) .

## <a name="changing-relationships-using-navigations"></a>Ändern von Beziehungen mithilfe von Navigationen

Die einfachste Möglichkeit, die Beziehung zwischen zwei Entitäten zu ändern EF Core, besteht darin, eine Navigation zu ändern, ohne die umgekehrten Navigations-und FK-Werte entsprechend zu fixieren. Dies kann folgendermaßen erfolgen:

- Hinzufügen oder Entfernen einer Entität aus einer Auflistungs Navigation.
- Ändern einer Verweis Navigation, um auf eine andere Entität zu verweisen oder auf NULL festzulegen.

### <a name="adding-or-removing-from-collection-navigations"></a>Hinzufügen oder entfernen aus der Auflistungs Navigation

Verschieben wir z. b. einen der Beiträge aus dem Visual Studio-Blog in den .net-Blog. Hierfür müssen zunächst die Blogs und Beiträge geladen werden, und anschließend wird der Beitrag aus der Navigations Sammlung in einem Blog in die Navigations Sammlung im anderen Blog verschoben:

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");
        var vsBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == "Visual Studio Blog");

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Changing_relationships_using_navigations_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_1)]

> [!TIP]
> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>Hier wird ein Aufrufen von benötigt, da der Zugriff auf die Debugansicht keine [Automatische Erkennung von Änderungen](xref:core/change-tracking/change-detection)bewirkt.

Dies ist die Debugansicht, die nach dem Ausführen des obigen Codes gedruckt wurde:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: [{Id: 4}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

Die `Blog.Posts` Navigation im .net-Blog umfasst nun drei Beiträge ( `Posts: [{Id: 1}, {Id: 2}, {Id: 3}]` ). Ebenso hat die `Blog.Posts` Navigation im Visual Studio-Blog nur einen Beitrag ( `Posts: [{Id: 4}]` ). Dies ist zu erwarten, da der Code diese Auflistungen explizit geändert hat.

Interessanter ist, obwohl der Code die Navigation nicht explizit geändert hat `Post.Blog` , dass er so festgeschrieben wurde, dass er auf den Visual Studio-Blog ( `Blog: {Id: 1}` ) verweist. Außerdem wurde der `Post.BlogId` Fremdschlüssel Wert so aktualisiert, dass er mit dem Primärschlüssel Wert des .net-Blogs identisch ist. Diese Änderung am FK-Wert in wird dann in der Datenbank persistent gespeichert, wenn SaveChanges aufgerufen wird:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='3' (DbType = String), @p0='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

### <a name="changing-reference-navigations"></a>Ändern der Verweis Navigation

Im vorherigen Beispiel wurde ein Beitrag von einem Blog zu einem anderen verschoben, indem die Sammlungs Navigation von Beiträgen in den einzelnen Blogs bearbeitet wurde. Das gleiche kann erreicht werden, indem Sie stattdessen die `Post.Blog` Verweis Navigation ändern, sodass Sie auf den neuen Blog verweist. Beispiel:

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.Blog = dotNetBlog;
-->
[!code-csharp[Changing_relationships_using_navigations_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_2)]

Die Debugansicht ist nach dieser Änderung _exakt identisch_ mit der im vorherigen Beispiel. Dies liegt daran, dass EF Core die Änderung der Verweis Navigation erkannt und die Auflistungs Navigation und den FK-Wert entsprechend korrigiert haben.

## <a name="changing-relationships-using-foreign-key-values"></a>Ändern von Beziehungen mithilfe von Fremdschlüssel Werten

Im vorherigen Abschnitt wurden die Beziehungen durch die Navigation geändert, sodass Fremdschlüssel Werte automatisch aktualisiert werden. Dies ist die empfohlene Vorgehensweise zum Bearbeiten von Beziehungen in EF Core. Es ist jedoch auch möglich, die FK-Werte direkt zu bearbeiten. Beispielsweise können wir einen Beitrag von einem Blog in einen anderen verschieben, indem wir den `Post.BlogId` Fremdschlüssel Wert ändern:

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.BlogId = dotNetBlog.Id;
-->
[!code-csharp[Changing_relationships_using_foreign_key_values_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_foreign_key_values_1)]

Beachten Sie, dass dies dem Ändern der Verweis Navigation sehr ähnlich ist, wie im vorherigen Beispiel gezeigt.

Die Debugansicht, die nach dieser Änderung wieder _identisch_ ist, entspricht der Groß-/Kleinschreibung für die beiden vorherigen Beispiele. Dies liegt daran, dass EF Core erkannt hat, dass sich der FK-Wert geändert hat, und dann den Verweis und die Auflistungs Navigation entsprechend

> [!TIP]
> Schreiben Sie keinen Code, um alle Navigations-und FK-Werte zu bearbeiten, wenn sich eine Beziehung ändert. Ein solcher Code ist komplizierter und muss in jedem Fall konsistente Änderungen an Fremdschlüsseln und Navigationen sicherstellen. Ändern Sie, wenn möglich, nur eine einzelne Navigation oder möglicherweise beide Navigationen. Bearbeiten Sie bei Bedarf nur die FK-Werte. Vermeiden Sie die Bearbeitung von navigationswerten und FK-Werten

## <a name="fixup-for-added-or-deleted-entities"></a>Korrektur für hinzugefügte oder gelöschte Entitäten

### <a name="adding-to-a-collection-navigation"></a>Hinzufügen zu einer Sammlungs Navigation

EF Core führt die folgenden Aktionen aus, wenn erkannt wird [, dass einer](xref:core/change-tracking/change-detection) Auflistungs Navigation eine neue abhängige/untergeordnete Entität hinzugefügt wurde:

- Wenn die Entität nicht nachverfolgt wird, wird Sie nachverfolgt. (Die Entität befindet sich normalerweise im- `Added` Zustand. Wenn der Entitätstyp jedoch für die Verwendung generierter Schlüssel konfiguriert ist und der Primärschlüssel Wert festgelegt ist, wird die Entität im-Zustand nachverfolgt `Unchanged` .)
- Wenn die Entität einem anderen Prinzipal/übergeordneten Element zugeordnet ist, wird diese Beziehung getrennt.
- Die Entität wird dem Prinzipal/übergeordneten Element zugeordnet, das die Auflistungs Navigation besitzt.
- Die Navigations-und Fremdschlüssel Werte werden für alle Beteiligten Entitäten korrigiert.

Auf Grundlage dieser Informationen können wir sehen, dass zum Verschieben eines Beitrags von einem Blog zu einem anderen nicht unbedingt aus der alten Auflistungs Navigation entfernt werden muss, bevor der neue hinzugefügt wird. Daher kann der Code aus dem obigen Beispiel in geändert werden:

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_1)]

in:

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_2)]

EF Core sehen, dass der Beitrag einem neuen Blog hinzugefügt wurde, und entfernt ihn automatisch aus der Sammlung im ersten Blog.

### <a name="removing-from-a-collection-navigation"></a>Entfernen aus einer Sammlungs Navigation

Durch das Entfernen einer abhängigen/untergeordneten Entität aus der Auflistungs Navigation des Prinzipals/übergeordneten Elements wird die Beziehung zu diesem Prinzipal bzw. übergeordneten Element Was als nächstes passiert, hängt davon ab, ob die Beziehung optional oder erforderlich ist.

#### <a name="optional-relationships"></a>Optionale Beziehungen

Standardmäßig wird für optionale Beziehungen der Fremdschlüssel Wert auf NULL festgelegt. Dies bedeutet, dass das abhängige _/untergeordnete Element keinem Prinzipal_ /übergeordneten Element mehr zugeordnet ist. Laden wir beispielsweise einen Blog und Beiträge, und entfernen Sie dann einen der Beiträge aus der `Blog.Posts` Sammlungs Navigation:

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_3)]

Wenn Sie die [Debugansicht der Änderungs Nachverfolgung](xref:core/change-tracking/debug-views) nach dieser Änderung sehen, wird Folgendes angezeigt:

- Der `Post.BlogId` FK wurde auf NULL () festgelegt. `BlogId: <null> FK Modified Originally 1`
- Die `Post.Blog` Verweis Navigation wurde auf NULL () festgelegt. `Blog: <null>`
- Der Beitrag wurde aus der `Blog.Posts` Sammlungs Navigation entfernt ( `Posts: [{Id: 1}]` ).

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

Beachten Sie, dass der Beitrag _nicht_ als gekennzeichnet ist `Deleted` . Er ist als gekennzeichnet `Modified` , sodass der FK-Wert in der Datenbank auf NULL festgelegt wird, wenn SaveChanges aufgerufen wird.

#### <a name="required-relationships"></a>Erforderliche Beziehungen

Das Festlegen des FK-Werts auf NULL ist nicht zulässig (und in der Regel nicht möglich) für erforderliche Beziehungen. Daher bedeutet das Durchlaufen einer erforderlichen Beziehung, dass die abhängige/untergeordnete Entität entweder einem neuen Prinzipal/übergeordneten Element neu übergeordnet werden muss oder aus der Datenbank entfernt werden muss, wenn SaveChanges aufgerufen wird, um eine Verletzung der referenziellen Einschränkung zu vermeiden. Dies wird als "Löschen von Waisen" bezeichnet und ist das Standardverhalten in EF Core für erforderliche Beziehungen.

Nehmen wir beispielsweise an, dass die Beziehung zwischen Blog und Beiträgen erforderlich ist, und führen Sie dann denselben Code wie im vorherigen Beispiel aus:

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_4)]

Wenn Sie sich die debuggingansicht ansehen, zeigt dies Folgendes:

- Der Beitrag wurde so gekennzeichnet `Deleted` , dass er aus der Datenbank gelöscht wird, wenn SaveChanges aufgerufen wird.
- Die `Post.Blog` Verweis Navigation wurde auf NULL () festgelegt `Blog: <null>` .
- Der Beitrag wurde aus der `Blog.Posts` Sammlungs Navigation entfernt ( `Posts: [{Id: 1}]` ).

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

Beachten Sie, dass das `Post.BlogId` unverändert bleibt, da es für eine erforderliche Beziehung nicht auf NULL festgelegt werden kann.

Das Aufrufen von SaveChanges führt dazu, dass der verwaiste Beitrag gelöscht wird:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

#### <a name="delete-orphans-timing-and-re-parenting"></a>Entfernen der zeitlichen Steuerung und erneuten Verarbeitung von Waisen

Standardmäßig wird das Markieren von Waisen Vorgängen durchgeführt, sobald `Deleted` die Beziehungs Änderung [erkannt](xref:core/change-tracking/change-detection)wird. Dieser Prozess kann jedoch verzögert werden, bis SaveChanges tatsächlich aufgerufen wird. Dies kann hilfreich sein, um zu vermeiden, dass verwaiste von Entitäten, die von einem Prinzipal/übergeordneten Element entfernt wurden, mit einem neuen Prinzipal/übergeordneten Element versehen werden, bevor SaveChanges aufgerufen wird. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> wird verwendet, um diese zeitliche Steuerung festzulegen. Beispiel:

<!--
        context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_5)]

Nachdem der Beitrag aus der ersten Auflistung entfernt wurde, ist das Objekt nicht wie `Deleted` im vorherigen Beispiel dargestellt. Stattdessen verfolgt EF Core, dass die Beziehung getrennt wird, _auch wenn dies eine erforderliche Beziehung ist_. (Der FK-Wert wird von EF Core als null betrachtet, obwohl er nicht tatsächlich NULL sein kann, da der Typ keine NULL-Werte zulässt. Dies wird als "konzeptionelle NULL" bezeichnet.)

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
```

Wenn Sie SaveChanges zu diesem Zeitpunkt aufrufen, würde dies dazu führen, dass der verwaiste Beitrag gelöscht wird. Wenn jedoch wie im obigen Beispiel ein Beitrag zu einem neuen Blog zugeordnet ist, bevor SaveChanges aufgerufen wird, wird er entsprechend dem neuen Blog korrigiert und nicht mehr als verwaiste gilt:

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
```

Mit SaveChanges, die an dieser Stelle aufgerufen werden, wird der Beitrag in der Datenbank aktualisiert, anstatt ihn zu löschen.

Es ist auch möglich, das automatische Löschen von Waisen zu deaktivieren. Dies führt zu einer Ausnahme, wenn SaveChanges aufgerufen wird, während ein verwaistes nachverfolgt wird. Der folgende Code zeigt z. b.:

<!--
                var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");

                context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.Never;

                var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
                dotNetBlog.Posts.Remove(post);

                context.SaveChanges(); // Throws
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_6)]

Löst diese Ausnahme aus:

> System. InvalidOperationException: die Zuordnung zwischen den Entitäten "Blog" und "Post" mit dem Schlüsselwert "{BlogId: 1}" wurde getrennt, aber die Beziehung wird entweder als erforderlich markiert oder ist implizit erforderlich, da der Fremdschlüssel nicht auf NULL festgelegt werden kann. Wenn die abhängige/untergeordnete Entität gelöscht werden soll, wenn eine erforderliche Beziehung getrennt wird, konfigurieren Sie die Beziehung für die Verwendung von Cascade-Lösch Vorgängen.

Das Löschen von verwaisten und kaskadierenden Lösch Vorgängen kann jederzeit durch Aufrufen von erzwungen werden <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> . Wenn Sie dies mit dem Festlegen der zeitlichen Steuerung für Löschvorgang auf löschen, wird sichergestellt, dass verwaiste Dateien `Never` nie gelöscht werden, es sei denn, EF Core explizit

### <a name="changing-a-reference-navigation"></a>Ändern einer Referenz Navigation

Das Ändern der Verweis Navigation einer 1: n-Beziehung hat denselben Effekt wie das Ändern der Sammlungs Navigation am anderen Ende der Beziehung. Das Festlegen der Verweis Navigation von Dependent/Child auf Null entspricht dem Entfernen der Entität aus der Auflistungs Navigation des Prinzipals/übergeordneten Elements. Alle Fixup-und Daten Bank Änderungen erfolgen wie im vorherigen Abschnitt beschrieben, einschließlich der Erstellung einer verwaisten Entität, wenn die Beziehung erforderlich ist.

#### <a name="optional-one-to-one-relationships"></a>Optionale 1:1-Beziehungen

Bei 1-zu-eins-Beziehungen bewirkt das Ändern einer Verweis Navigation, dass alle vorherigen Beziehungen getrennt werden. Bei optionalen Beziehungen bedeutet dies, dass der FK-Wert für das zuvor Verwandte abhängige/untergeordnete Element auf NULL festgelegt ist. Beispiel:

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Assets).Single(e => e.Name == ".NET Blog");
        dotNetBlog.Assets = new BlogAssets();

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_7)]

Die Debugansicht vor dem Aufruf von SaveChanges zeigt, dass die neuen Assets die vorhandenen Assets ersetzt haben, die nun `Modified` mit einem NULL-FK-Wert gekennzeichnet sind `BlogAssets.BlogId` :

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482629}
  Posts: []
BlogAssets {Id: -2147482629} Added
  Id: -2147482629 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Modified
  Id: 1 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 1
  Blog: <null>
```

Dies führt zu einem Update und einer Einfügung, wenn SaveChanges aufgerufen wird:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Assets" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2=NULL, @p3='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p2, @p3);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

#### <a name="required-one-to-one-relationships"></a>Erforderliche 1-zu-eins-Beziehung

Wenn Sie denselben Code wie im vorherigen Beispiel ausführen, aber dieses Mal mit einer erforderlichen eins-zu-eins-Beziehung, zeigt, dass der zuvor zugeordnete `BlogAssets` nun als gekennzeichnet ist `Deleted` , da er zu einem verwaisten wird, wenn der neue `BlogAssets` seinen Platz annimmt:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482639}
  Posts: []
BlogAssets {Id: -2147482639} Added
  Id: -2147482639 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Deleted
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: <null>
```

Dies führt dann zum Löschen eines und zum Einfügen, wenn SaveChanges aufgerufen wird:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Assets"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1=NULL, @p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p1, @p2);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

Die zeitliche Steuerung der Markierung von Waisen als gelöscht kann auf die gleiche Weise wie für Sammlungs Navigation geändert werden und hat die gleichen Auswirkungen.

### <a name="deleting-an-entity"></a>Löschen einer Entität

#### <a name="optional-relationships"></a>Optionale Beziehungen

Wenn eine Entität als gekennzeichnet ist `Deleted` , z. b. durch Aufrufen von <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> , werden Verweise auf die gelöschte Entität aus der Navigation anderer Entitäten entfernt. Für optionale Beziehungen werden die FK-Werte in abhängigen Entitäten auf NULL festgelegt.

Als Beispiel markieren wir den Visual Studio-Blog wie folgt `Deleted` :

<!--
        using var context = new BlogsContext();

        var vsBlog = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .Single(e => e.Name == "Visual Studio Blog");

        context.Remove(vsBlog);

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Deleting_an_entity_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Deleting_an_entity_1)]

Wenn Sie die [Debugansicht der Änderungs](xref:core/change-tracking/debug-views) Nachverfolgung anzeigen, bevor Sie SaveChanges aufrufen

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Modified
  Id: 2 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 2
  Blog: <null>
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
Post {Id: 4} Modified
  Id: 4 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: []
```

Beachten Sie Folgendes:

- Der Blog ist als `Deleted` markiert.
- Die Ressourcen im Zusammenhang mit dem gelöschten Blog haben einen NULL-Wert für den FK ( `BlogId: <null> FK Modified Originally 2` ) und eine NULL-Verweis Navigation ( `Blog: <null>` ).
- Jeder Beitrag im Zusammenhang mit dem gelöschten Blog weist einen NULL `BlogId: <null> FK Modified Originally 2` -Wert () und eine NULL-Verweis Navigation () auf. `Blog: <null>`

#### <a name="required-relationships"></a>Erforderliche Beziehungen

Das fixupverhalten für erforderliche Beziehungen ist das gleiche wie bei optionalen Beziehungen, mit dem Unterschied, dass die abhängigen/untergeordneten Entitäten als gekennzeichnet sind, `Deleted` da Sie nicht ohne einen Prinzipal/Parent vorhanden sein können und aus der Datenbank entfernt werden müssen, wenn SaveChanges aufgerufen wird, um eine referenzielle Einschränkungs Ausnahme zu vermeiden. Dies wird als "Cascade Delete" bezeichnet und ist das Standardverhalten in EF Core für erforderliche Beziehungen. Beispielsweise führt die Ausführung desselben Codes, wie im vorherigen Beispiel, aber mit einer erforderlichen Beziehung, zu der folgenden Debugansicht, bevor SaveChanges aufgerufen wird:

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Deleted
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 3} Deleted
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

Erwartungsgemäß werden die abhängigen Elemente/untergeordneten Elemente nun als markiert `Deleted` . Beachten Sie jedoch, dass sich die Navigation in den gelöschten Entitäten _nicht_ geändert hat. Dies mag seltsam erscheinen, vermeidet jedoch das vollständige Aufteilen eines gelöschten Diagramms von Entitäten, indem alle Navigationen gelöscht werden. Das heißt, dass der Blog, das Asset und die Beiträge auch nach dem Löschen eines Diagramms von Entitäten vorhanden sind. Dies erleichtert das Aufheben der Löschung eines Diagramms von Entitäten, als dies in EF6 der Fall war, in dem das Diagramm zerrochen wurde.

#### <a name="cascade-delete-timing-and-re-parenting"></a>Löschen der zeitlichen Steuerung und erneute Verarbeitung

Standardmäßig erfolgt das Löschen von Lösch Vorgängen, sobald der übergeordnete/Prinzipal als gekennzeichnet ist `Deleted` . Dies ist das gleiche wie für das Löschen von Waisen, wie zuvor beschrieben. Wie beim Löschen von verwaisten, kann dieser Prozess verzögert werden, bis SaveChanges aufgerufen oder sogar vollständig deaktiviert wird, indem entsprechend festgelegt wird <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> . Dies ist auf die gleiche Weise wie beim Löschen von Waisen, einschließlich der erneuten untergeordneten Elemente/abhängigen Elemente nach dem Löschen eines Prinzipals/übergeordneten Elements.

Das Löschen von Lösch Vorgängen sowie das Löschen von Waisen können jederzeit erzwungen werden, indem aufgerufen wird <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> . Durch die Kombination dieser mit dem Festlegen der Zeitüberschreitungen für Löschvorgänge auf `Never` wird sichergestellt, dass Löschvorgänge nie durchgeführt werden, es sei denn, EF Core

> [!TIP]
> Das kaskadierende Delete und das Löschen verwaister Entitäten sind eng verwandt. Beide führen dazu, dass abhängige/untergeordnete Entitäten gelöscht werden, wenn die Beziehung zum erforderlichen Prinzipal bzw. der übergeordneten Entität getrennt wird. Beim kaskadierenden Delete erfolgt diese Trennung, weil der Prinzipal bzw. die übergeordnete Entität selbst gelöscht wird. Bei verwaisten Entitäten ist der Prinzipal bzw. die übergeordnete Entität weiterhin vorhanden, jedoch besteht die Beziehung zur abhängigen/untergeordneten Entität nicht mehr.

## <a name="many-to-many-relationships"></a>M:n-Beziehungen

M:n-Beziehungen in EF Core werden mithilfe einer JOIN-Entität implementiert. Jede Seite: die m:n-Beziehung bezieht sich auf diese joinentität mit einer 1: n-Beziehung. Vor EF Core 5,0 musste diese Verknüpfungs Entität explizit definiert und zugeordnet werden. Ab EF Core 5,0 können Sie implizit und ausgeblendet erstellt werden. In beiden Fällen ist das zugrunde liegende Verhalten jedoch identisch. Wir betrachten dieses zugrunde liegende Verhalten zunächst, um zu verstehen, wie die Nachverfolgung von m:n-Beziehungen funktioniert.

### <a name="how-many-to-many-relationships-work"></a>Funktionsweise von m:n-Beziehungen

Beachten Sie dieses EF Core Modell, das eine m:n-Beziehung zwischen Beiträgen und Tags mithilfe eines explizit definierten joinentitäts Typs erstellt:

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
    -->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Model)]

Beachten Sie, dass der `PostTag` Join-Entitätstyp zwei Fremdschlüssel Eigenschaften enthält. In diesem Modell muss eine posttag-joinentität vorhanden sein, damit ein Post mit einem Tag verknüpft werden kann, wobei der `PostTag.PostId` Fremdschlüssel Wert mit dem Wert des `Post.Id` Primärschlüssels übereinstimmt und der `PostTag.TagId` Fremdschlüssel Wert mit dem Primärschlüssel Wert übereinstimmt `Tag.Id` . Beispiel:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_1)]

Wenn Sie die [Debugansicht Änderungs](xref:core/change-tracking/debug-views) Nachverfolgung nach dem Ausführen dieses Codes anzeigen, zeigt dies, dass der Beitrag und das Tag mit der neuen `PostTag` joinentität verknüpft sind:

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
```

Beachten Sie, dass die Auflistungs Navigation für `Post` und `Tag` korrigiert wurden, wie Sie die Referenz Navigation auf aufweisen `PostTag` . Diese Beziehungen können von Navigationen anstelle von FK-Werten, wie in allen vorherigen Beispielen, manipuliert werden. Der obige Code kann z. b. so geändert werden, dass die Beziehung durch Festlegen der Verweis Navigation für die joinentität hinzugefügt wird:

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_2)]

Dies führt zu genau denselben Änderungen an Fert und Navigationen wie im vorherigen Beispiel.

### <a name="skip-navigations"></a>Navigation überspringen

> [!NOTE]
> Die Navigation überspringen wurde in EF Core 5,0 eingeführt.

Eine manuelle Bearbeitung der jointabelle kann mühsam sein. Beginnend mit EF Core 5,0 können m:n-Beziehungen direkt mithilfe spezieller Sammlungs Navigation, die die joinentität überspringen, manipuliert werden. Beispielsweise können zwei Skip-Navigationen zum obigen Modell hinzugefügt werden. eine von Post zu Tags und die andere von Tag zu Beiträgen:

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Model)]

Für diese m:n-Beziehung ist die folgende Konfiguration erforderlich, um sicherzustellen, dass die Skip-Navigation und die normale Navigation für dieselbe m:n-Beziehung verwendet werden:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne(t => t.Tag).WithMany(p => p.PostTags),
                    j => j.HasOne(t => t.Post).WithMany(p => p.PostTags));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=OnModelCreating)]

Weitere Informationen zum Mapping von m:n-Beziehungen finden Sie unter [Beziehungen](xref:core/modeling/relationships) .

Navigation überspringen und Verhalten sich wie normale Sammlungs Navigation. Die Art und Weise, wie Sie mit Fremdschlüssel Werten arbeiten, unterscheidet sich jedoch. Wir ordnen einem Tag einen Beitrag zu, aber dieses Mal wird eine Skip-Navigation verwendet:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_3)]

Beachten Sie, dass dieser Code die joinentität nicht verwendet. Stattdessen wird eine Entität nur auf dieselbe Weise wie bei einer 1: n-Beziehung zu einer Navigations Auflistung hinzugefügt. Die resultierende Debugansicht ist im Wesentlichen identisch mit der folgenden:

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
  Posts: [{Id: 3}]
```

Beachten Sie, dass eine Instanz der `PostTag` joinentität automatisch erstellt wurde, wobei die FK-Werte auf die PK-Werte des Tags und des Beitrags festgelegt sind, die jetzt zugeordnet sind. Alle normalen Verweise und Auflistungs Navigation wurden so korrigiert, dass Sie diesen FK-Werten entsprechen. Da dieses Modell außerdem Skip-Navigationen enthält, wurden diese ebenfalls korrigiert. Insbesondere, obwohl wir das Tag zur `Post.Tags` Navigation überspringen hinzugefügt haben, wurde die `Tag.Posts` umgekehrte Skip-Navigation auf der anderen Seite dieser Beziehung ebenfalls so eingerichtet, dass Sie den zugehörigen Beitrag enthält.

Beachten Sie, dass die zugrunde liegenden m:n-Beziehungen weiterhin direkt bearbeitet werden können, auch wenn die über sprunnavigationen oben angeordnet wurden. Beispielsweise könnten Tag und Post wie folgt verknüpft werden, bevor Skip-Navigationen eingeführt werden:

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_4)]

Oder mithilfe von FK-Werten:

<!--
            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });
-->
[!code-csharp[Many_to_many_relationships_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_5)]

Dies führt dennoch dazu, dass die Skip-Navigation ordnungsgemäß korrigiert wird, was zur Ausgabe der Ausgabe der Debugansicht führt wie im vorherigen Beispiel.

### <a name="skip-navigations-only"></a>Nur Navigation überspringen

Im vorherigen Abschnitt haben wir _zusätzlich zum_ vollständigen definieren der beiden zugrunde liegenden 1: n-Beziehungen die Skip-Navigation hinzugefügt. Dies ist hilfreich, um zu veranschaulichen, was mit den FK-Werten passiert, aber häufig unnötig ist. Stattdessen kann die m:n-Beziehung nur mithilfe von Skip- _Navigationen_ definiert werden. Auf diese Weise wird die m:n-Beziehung im Modell am Anfang dieses Dokuments definiert. Mit diesem Modell können wir erneut einen Beitrag und ein Tag zuordnen, indem wir der Navigations Navigation einen Beitrag hinzufügen `Tag.Posts` (oder alternativ ein Tag zur Navigation überspringen hinzufügen `Post.Tags` ):

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Many_to_many_relationships_6)]

Wenn Sie die Debugansicht nach dieser Änderung betrachten, wird angezeigt, dass EF Core eine Instanz von erstellt hat `Dictionary<string, object>` , die die joinentität darstellt. Diese joinentität enthält sowohl `PostsId` die-als auch die- `TagsId` Fremdschlüssel Eigenschaften, die so festgelegt wurden, dass Sie mit den PK-Werten des zugewiesenen Beitrags und des zugehörigen Tags

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 1} Added
  PostsId: 3 PK FK
  TagsId: 1 PK FK
```

Weitere Informationen zu impliziten joinentitäten und zur Verwendung von Entitäts Typen finden Sie unter [Beziehungen](xref:core/modeling/relationships) `Dictionary<string, object>` .

> [!IMPORTANT]
> Der CLR-Typ, der für joinentitäts Typen gemäß der Konvention verwendet wird, kann sich in zukünftigen Versionen ändern, um die Leistung Verlassen Sie sich nicht auf den Jointyp, `Dictionary<string, object>` Wenn dies nicht explizit konfiguriert wurde.

### <a name="join-entities-with-payloads"></a>Verknüpfen von Entitäten mit Nutzlasten

Bisher haben alle Beispiele einen Join-Entitätstyp (ob explizit oder implizit) verwendet, der nur die beiden Fremdschlüssel Eigenschaften enthält, die für die m:n-Beziehung erforderlich sind. Keine dieser FK-Werte muss explizit von der Anwendung festgelegt werden, wenn Beziehungen bearbeitet werden, da ihre Werte aus den Primärschlüssel Eigenschaften der verknüpften Entitäten stammen. Dadurch können EF Core Instanzen der joinentität erstellen, ohne dass Daten fehlen.

#### <a name="payloads-with-generated-values"></a>Nutzlasten mit generierten Werten

EF Core unterstützt das Hinzufügen zusätzlicher Eigenschaften zum joinentitätstyp. Dies wird als "Nutzlast" der joinentität bezeichnet. Fügen Sie z. b. `TaggedOn` der joinentität eine-Eigenschaft hinzu `PostTag` :

<!--
    public class PostTag
    {
        public int PostId { get; set; } // Foreign key to Post
        public int TagId { get; set; } // Foreign key to Tag

        public DateTime TaggedOn { get; set; } // Payload
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Model)]

Diese Nutz Last Eigenschaft wird nicht festgelegt, wenn EF Core eine Join-Entitäts Instanz erstellt. Die gängigste Methode, damit umzugehen, ist die Verwendung von Nutz Last Eigenschaften mit automatisch generierten Werten. Die-Eigenschaft kann z. b. `TaggedOn` so konfiguriert werden, dass ein vom Speicher generierter Zeitstempel verwendet wird, wenn jede neue Entität eingefügt wird:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany(),
                    j => j.Property(e => e.TaggedOn).HasDefaultValueSql("CURRENT_TIMESTAMP"));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=OnModelCreating)]

Ein Beitrag kann jetzt auf die gleiche Weise wie zuvor markiert werden:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Many_to_many_relationships_7)]

Wenn Sie die [Debugansicht Änderungs](xref:core/change-tracking/debug-views) Nachverfolgung nach dem Aufruf von SaveChanges anzeigen, wird angezeigt, dass die Payload-Eigenschaft entsprechend festgelegt wurde

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Unchanged
  PostId: 3 PK FK
  TagId: 1 PK FK
  TaggedOn: '12/29/2020 8:13:21 PM'
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
```

#### <a name="explicitly-setting-payload-values"></a>Explizit festlegen von Nutz Last Werten

Im Anschluss an das vorherige Beispiel fügen wir eine Nutz Last Eigenschaft hinzu, die keinen automatisch generierten Wert verwendet:

<!--
    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public DateTime TaggedOn { get; set; } // Auto-generated payload property
        public string TaggedBy { get; set; } // Not-generated payload property
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Model)]

Ein Beitrag kann jetzt auf die gleiche Weise wie zuvor gekennzeichnet werden, und die joinentität wird immer noch automatisch erstellt. Auf diese Entität kann dann mithilfe eines der unter Zugreifen auf über [wachte Entitäten](xref:core/change-tracking/entity-entries)beschriebenen Mechanismen zugegriffen werden. Der folgende Code verwendet beispielsweise <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> , um auf die Join-Entitäts Instanz zuzugreifen:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();

            var joinEntity = context.Set<PostTag>().Find(post.Id, tag.Id);

            joinEntity.TaggedBy = "ajcvickers";

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_8](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_8)]

Nachdem die verknüpfte Entität gefunden wurde, kann Sie auf die normale Weise manipuliert werden, in diesem Beispiel, um die `TaggedBy` Nutz Last Eigenschaft vor dem Aufrufen von SaveChanges festzulegen.

> [!NOTE]
> Beachten Sie, dass hier ein-Befehl <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> erforderlich ist, um EF Core die Möglichkeit zu haben, die Änderung der Navigations Eigenschaft zu erkennen und die Join-Entitäts Instanz zu erstellen, bevor `Find` verwendet wird. Weitere Informationen finden Sie unter [Änderungs Erkennung und Benachrichtigungen](xref:core/change-tracking/change-detection) .

Alternativ kann die joinentität explizit erstellt werden, um einem Tag einen Beitrag zuzuordnen. Beispiel:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag()
            {
                PostId = post.Id,
                TagId = tag.Id,
                TaggedBy = "ajcvickers"
            });

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_9](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_9)]

Eine weitere Möglichkeit zum Festlegen von Nutzlastdaten besteht darin, <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> das-Ereignis zu überschreiben oder <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> zu verwenden, um Entitäten vor dem Aktualisieren der Datenbank zu verarbeiten. Beispiel:

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>())
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                }
            }

            return base.SaveChanges();
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=SaveChanges)]
