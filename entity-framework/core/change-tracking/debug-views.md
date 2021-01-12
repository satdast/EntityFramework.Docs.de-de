---
title: Änderungs Nachverfolgung-Debugging-EF Core
description: Verwenden von ChangeTracker DebugView und Log Messages zum Debuggen EF Core Änderungs Nachverfolgung
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/debug-views
ms.openlocfilehash: 76108120cf7f532d085fef12121bb639b065add0
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129591"
---
# <a name="change-tracker-debugging"></a>Änderungs nach Verfolgungs Debugging

Der Änderungs Tracker Entity Framework Core (EF Core) generiert zwei Arten von Ausgaben, die beim Debuggen hilfreich sind:

- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType>Stellt eine lesbare Ansicht aller Entitäten bereit, die nachverfolgt werden.
- Protokollmeldungen auf Debugebene werden generiert, wenn die Änderungs Nachverfolgung den Status erkennt und Beziehungen korrigiert.

> [!TIP]
> In diesem Dokument wird davon ausgegangen, dass die Entitäts Zustände und die Grundlagen EF Core Änderungs Nachverfolgung verstanden werden. Weitere Informationen zu diesen Themen finden Sie [unter Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) .

> [!TIP]
> Sie können den gesamten Code in diesem Dokument ausführen und Debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackerDebugging).

## <a name="change-tracker-debug-view"></a>Debug-Ansicht für Änderungs Nachverfolgung

Sie können im Debugger Ihrer IDE auf die Debugansicht für die Änderungs Nachverfolgung zugreifen. Beispielsweise mit Visual Studio:

![Zugreifen auf die Debugansicht der Änderungs Nachverfolgung über den Visual Studio-Debugger](_static/debug-view.png)

Sie können auch direkt über den Code darauf zugreifen, z. b. um die Debugansicht an die Konsole zu senden:

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.ShortView);
-->
[!code-csharp[Change_tracker_debug_view_1b](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1b)]

Die Debugansicht hat eine Kurzform und eine lange Form. In der Kurzform werden nach verfolgte Entitäten, deren Status und Schlüsselwerte angezeigt. Die Long-Form umfasst auch alle Eigenschafts-und Navigations Werte und den Status.

### <a name="the-short-view"></a>Die kurze Ansicht

Sehen wir uns ein Beispiel für eine Debugansicht an, das das am Ende dieses Dokuments gezeigte Modell verwendet. Zunächst werden einige Entitäten nachverfolgt und in verschiedenen Zuständen abgelegt, damit wir gute Änderungs nach Verfolgungs Daten anzeigen können:

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts).ThenInclude(e => e.Tags)
            .Include(e => e.Assets)
            .ToList();

        // Mark something Added
        blogs[0].Posts.Add(
            new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many new features and..."
            });

        // Mark something Deleted
        blogs[1].Posts.Remove(blogs[1].Posts[1]);

        // Make something Modified
        blogs[0].Name = ".NET Blog (All new!)";

        context.ChangeTracker.DetectChanges();
-->
[!code-csharp[Change_tracker_debug_view_1a](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1a)]

Das Drucken der kurzen Ansicht an dieser Stelle, wie oben gezeigt, führt zur folgenden Ausgabe:

```output
Blog {Id: 1} Modified AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}
Blog {Id: 2} Unchanged AK {AssetsId: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
BlogAssets {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65} Unchanged FK {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
BlogAssets {Id: ed727978-1ffe-4709-baee-73913e8e44a0} Unchanged FK {Id: ed727978-1ffe-4709-baee-73913e8e44a0}
Post {Id: -2147482643} Added FK {BlogId: 1}
Post {Id: 1} Unchanged FK {BlogId: 1}
Post {Id: 2} Unchanged FK {BlogId: 1}
Post {Id: 3} Unchanged FK {BlogId: 2}
Post {Id: 4} Deleted FK {BlogId: 2}
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 1} Unchanged FK {PostsId: 1} FK {TagsId: 1}
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 3} Unchanged FK {PostsId: 1} FK {TagsId: 3}
PostTag (Dictionary<string, object>) {PostsId: 2, TagsId: 1} Unchanged FK {PostsId: 2} FK {TagsId: 1}
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 2} Unchanged FK {PostsId: 3} FK {TagsId: 2}
PostTag (Dictionary<string, object>) {PostsId: 4, TagsId: 2} Deleted FK {PostsId: 4} FK {TagsId: 2}
Tag {Id: 1} Unchanged
Tag {Id: 2} Unchanged
Tag {Id: 3} Unchanged
```

Beachten Sie Folgendes:

- Jede nach verfolgte Entität wird mit ihrem Primärschlüssel Wert (PK) aufgelistet. Beispiel: `Blog {Id: 1}`.
- Wenn es sich bei der Entität um einen Entitätstyp mit gemeinsam genutzter Entität handelt, wird auch der CLR-Typ angezeigt. Beispiel: `PostTag (Dictionary<string, object>)`.
- Der <xref:Microsoft.EntityFrameworkCore.EntityState> wird als nächstes angezeigt. Dabei handelt es sich um eine von `Unchanged` , `Added` , `Modified` oder `Deleted` .
- Die Werte für beliebige Alternative Schlüssel (AKS) werden als nächstes angezeigt. Beispiel: `AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}`.
- Schließlich werden die Werte für alle Fremdschlüssel (Key Keys, f) angezeigt. Beispiel: `FK {PostsId: 4} FK {TagsId: 2}`.

### <a name="the-long-view"></a>Die lange Ansicht

Die Ansicht Long kann auf die gleiche Weise wie in der kurzen Ansicht an die Konsole gesendet werden:

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracker_debug_view_1c](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1c)]

Die Ausgabe für denselben Zustand wie die kurze Ansicht oben ist:

```output
Blog {Id: 1} Modified
  Id: 1 PK
  AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK
  Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'
  Assets: {Id: ed727978-1ffe-4709-baee-73913e8e44a0}
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  AssetsId: '3a54b880-2b9d-486b-9403-dc2e52d36d65' AK
  Name: 'Visual Studio Blog'
  Assets: {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
  Posts: [{Id: 3}]
BlogAssets {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65} Unchanged
  Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK
  Banner: <null>
  Blog: {Id: 2}
BlogAssets {Id: ed727978-1ffe-4709-baee-73913e8e44a0} Unchanged
  Id: 'ed727978-1ffe-4709-baee-73913e8e44a0' PK FK
  Banner: <null>
  Blog: {Id: 1}
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many new fe...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
  Tags: []
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: [{Id: 1}, {Id: 3}]
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: [{Id: 1}]
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: [{Id: 2}]
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: [{Id: 2}]
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 1} Unchanged
  PostsId: 1 PK FK
  TagsId: 1 PK FK
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 3} Unchanged
  PostsId: 1 PK FK
  TagsId: 3 PK FK
PostTag (Dictionary<string, object>) {PostsId: 2, TagsId: 1} Unchanged
  PostsId: 2 PK FK
  TagsId: 1 PK FK
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 2} Unchanged
  PostsId: 3 PK FK
  TagsId: 2 PK FK
PostTag (Dictionary<string, object>) {PostsId: 4, TagsId: 2} Deleted
  PostsId: 4 PK FK
  TagsId: 2 PK FK
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 1}, {Id: 2}]
Tag {Id: 2} Unchanged
  Id: 2 PK
  Text: 'Visual Studio'
  Posts: [{Id: 3}, {Id: 4}]
Tag {Id: 3} Unchanged
  Id: 3 PK
  Text: 'EF Core'
  Posts: [{Id: 1}]
```

Jede nach verfolgte Entität und Ihr Status werden wie zuvor angezeigt. In der Long-Ansicht werden jedoch auch Eigenschaften-und Navigations Werte angezeigt.

#### <a name="property-values"></a>Eigenschaftswerte

Für jede Eigenschaft wird in der langen Ansicht angezeigt, ob die Eigenschaft Teil eines Primärschlüssels (PK), eines alternativen Schlüssels (AK) oder eines fremd Schlüssels (FK) ist. Beispiel:

- `Blog.Id` ist eine Primärschlüssel Eigenschaft: `Id: 1 PK`
- `Blog.AssetsId` ist eine Alternative Schlüsseleigenschaft: `AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK`
- `Post.BlogId` ist eine Fremdschlüssel Eigenschaft: `BlogId: 2 FK`
- `BlogAssets.Id` ist sowohl eine Primärschlüssel-als auch eine Fremdschlüssel Eigenschaft: `Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK`

Eigenschaftswerte, die geändert wurden, werden als solche gekennzeichnet, und der ursprüngliche Wert der-Eigenschaft wird ebenfalls angezeigt. Beispiel: `Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'`.

Schließlich `Added` geben Entitäten mit temporären Schlüsselwerten an, dass der Wert temporär ist. Beispiel: `Id: -2147482643 PK Temporary`.

#### <a name="navigation-values"></a>Navigations Werte

Navigations Werte werden mithilfe der Primärschlüssel Werte der Entitäten angezeigt, auf die der Navigations Verweis verweist. In der obigen Ausgabe bezieht sich z. b. Post 3 auf Blog 2. Dies bedeutet, dass die `Post.Blog` Navigation auf die `Blog` Instanz mit der ID 2 verweist. Dies wird als dargestellt `Blog: {Id: 2}` .

Dasselbe gilt für Auflistungs Navigationen, mit dem Unterschied, dass es in diesem Fall mehrere Verwandte Entitäten geben kann. Beispielsweise enthält die Auflistungs Navigation `Blog.Posts` drei Entitäten mit den Schlüsselwerten 1, 2 und-2147482643. Dies wird als dargestellt `[{Id: 1}, {Id: 2}, {Id: -2147482643}]` .

## <a name="change-tracker-logging"></a>Protokollierung protokollieren

Der Change Tracker protokolliert Nachrichten bei, `Debug` <xref:Microsoft.Extensions.Logging.LogLevel> Wenn [Änderungen an der Eigenschaft oder Navigation](xref:core/change-tracking/debug-views)erkannt werden. Wenn z. b. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> im Code am Anfang dieses Dokuments aufgerufen wird und die [Debugprotokollierung aktiviert ist](xref:core/logging-events-diagnostics/index), werden die folgenden Protokolle generiert:

```output
dbug: 12/30/2020 13:52:44.815 CoreEventId.DetectChangesStarting[10800] (Microsoft.EntityFrameworkCore.ChangeTracking)
      DetectChanges starting for 'BlogsContext'.
dbug: 12/30/2020 13:52:44.818 CoreEventId.PropertyChangeDetected[10802] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The unchanged property 'Blog.Name' was detected as changed from '.NET Blog' to '.NET Blog (All new!)' and will be marked as modified for entity with key '{Id: 1}'.
dbug: 12/30/2020 13:52:44.820 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Blog' entity with key '{Id: 1}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Modified'.
dbug: 12/30/2020 13:52:44.821 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      1 entities were added and 0 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 1}'.
dbug: 12/30/2020 13:52:44.822 CoreEventId.ValueGenerated[10808] (Microsoft.EntityFrameworkCore.ChangeTracking)
      'BlogsContext' generated temporary value '-2147482638' for the property 'Id.Post'.
dbug: 12/30/2020 13:52:44.822 CoreEventId.StartedTracking[10806] (Microsoft.EntityFrameworkCore.ChangeTracking)
      Context 'BlogsContext' started tracking 'Post' entity with key '{Id: -2147482638}'.
dbug: 12/30/2020 13:52:44.827 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      0 entities were added and 1 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 2}'.
dbug: 12/30/2020 13:52:44.827 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Post' entity with key '{Id: 4}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Modified'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.CascadeDeleteOrphan[10003] (Microsoft.EntityFrameworkCore.Update)
      An entity of type 'Post' with key '{Id: 4}' changed to 'Deleted' state due to severed required relationship to its parent entity of type 'Blog'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Post' entity with key '{Id: 4}' tracked by 'BlogsContext' changed state from 'Modified' to 'Deleted'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      0 entities were added and 1 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 2}'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.CascadeDelete[10002] (Microsoft.EntityFrameworkCore.Update)
      A cascade state change of an entity of type 'PostTag' with key '{PostsId: 4, TagsId: 2}' to 'Deleted' occurred due to the deletion of its parent entity of type 'Post' with key '{Id: 4}'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'PostTag' entity with key '{PostsId: 4, TagsId: 2}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Deleted'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.DetectChangesCompleted[10801] (Microsoft.EntityFrameworkCore.ChangeTracking)
      DetectChanges completed for 'BlogsContext'.
```

In der folgenden Tabelle werden die Protokollierungs Meldungen der Änderungs Protokollierung zusammengefasst

| Ereignis-ID                                                                                                               | BESCHREIBUNG
|:-----------------------------------------------------------------------------------------------------------------------|----
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesStarting?displayProperty=nameWithType>        | <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> wird gestartet
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesCompleted?displayProperty=nameWithType>       | <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> wurde abgeschlossen.
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.PropertyChangeDetected?displayProperty=nameWithType>       | Ein normaler Eigenschafts Wert wurde geändert.
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ForeignKeyChangeDetected?displayProperty=nameWithType>     | Ein Fremdschlüssel-Eigenschafts Wert wurde geändert.
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.CollectionChangeDetected?displayProperty=nameWithType>     | In einer Auflistungs Navigation ohne überspringen wurden Verwandte Entitäten hinzugefügt oder entfernt.
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ReferenceChangeDetected?displayProperty=nameWithType>      | Eine Verweis Navigation wurde geändert, um auf eine andere Entität zu zeigen, oder auf NULL festgelegt.
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StartedTracking?displayProperty=nameWithType>              | EF Core die Nachverfolgung einer Entität gestartet
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StateChanged?displayProperty=nameWithType>                 | Der einer <xref:Microsoft.EntityFrameworkCore.EntityState> Entität hat sich geändert.
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ValueGenerated?displayProperty=nameWithType>               | Für eine Eigenschaft wurde ein Wert generiert.
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.SkipCollectionChangeDetected?displayProperty=nameWithType> | In einer Navigation zum Überspringen der Auflistung wurden Verwandte Entitäten hinzugefügt oder entfernt

## <a name="the-model"></a>Die Model-Komponente

Das Modell, das für die obigen Beispiele verwendet wird, enthält die folgenden Entitäts Typen:

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public Guid AssetsId { get; set; } // Alternate key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public Guid Id { get; set; } // Primary key and foreign key
    public byte[] Banner { get; set; }

    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; } // Foreign key
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
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Model)]

Das Modell wird größtenteils gemäß der Konvention konfiguriert, mit nur wenigen Zeilen in "onmodelcreating":

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<Blog>()
            .Property(e => e.AssetsId)
            .ValueGeneratedOnAdd();

        modelBuilder
            .Entity<BlogAssets>()
            .HasOne(e => e.Blog)
            .WithOne(e => e.Assets)
            .HasForeignKey<BlogAssets>(e => e.Id)
            .HasPrincipalKey<Blog>(e => e.AssetsId);
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=OnModelCreating)]
