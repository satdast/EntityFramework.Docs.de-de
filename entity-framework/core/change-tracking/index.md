---
title: Änderungsnachverfolgung – EF Core
description: Übersicht zur Änderungsnachverfolgung für EF Core
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/index
ms.openlocfilehash: 8cfa4590af07ec1715eb48ec0c7acb3426b6a6b4
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983260"
---
# <a name="change-tracking-in-ef-core"></a>Änderungsnachverfolgung in EF Core

Jede <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanz verfolgt Änderungen nach, die an Entitäten vorgenommen wurden. Diese nachverfolgten Entitäten bestimmen wiederum die Änderungen an der Datenbank, wenn <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aufgerufen wird.

Dieses Dokument enthält eine Übersicht über die EF Core-Änderungsnachverfolgung (Entity Framework Core) und deren Beziehung zu Abfragen und Updates.

> [!TIP]
> Sie können den gesamten Code in diesem Dokument ausführen und debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).

> [!TIP]
> Der Einfachheit halber werden in diesem Dokument synchrone Methoden wie z. B. <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> anstatt ihrer asynchronen Entsprechungen wie <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> verwendet und darauf verwiesen. Das Aufrufen und Warten auf die asynchrone Methode kann ersetzt werden, sofern nicht anders angegeben.

## <a name="how-to-track-entities"></a>Vorgehensweise: Nachverfolgen von Entitäten

Entitätsinstanzen werden nachverfolgt, wenn auf sie Folgendes zutrifft:

- Zurückgegeben von einer Abfrage der Datenbank
- Explizit durch `Add`, `Attach`, `Update` oder ähnliche Methoden DbContext angefügt
- Erkannt als neue, mit vorhandenen nachverfolgten Entitäten verbundene Entitäten

Entitätsinstanzen werden in folgenden Fällen nicht mehr nachverfolgt:

- DbContext wurde verworfen
- Die Änderungsnachverfolgung ist deaktiviert (EF Core 5.0 und höher)
- Die Entitäten sind explizit getrennt

DbContext ist dazu konzipiert, eine kurzlebige Arbeitseinheit darzustellen, wie in [DbContext-Lebensdauer, -Konfiguration und -Initialisierung](xref:core/dbcontext-configuration/index) beschrieben. Dies bedeutet, dass das Verwerfen von DbContext _die normale Art und Weise_ ist, die Nachverfolgung von Entitäten zu beenden. Anders ausgedrückt, die Lebensdauer einer DbContext-Instanz sollte so aussehen:

1. Erstellen der DbContext-Instanz
2. Nachverfolgen einiger Entitäten
3. Vornehmen von Änderungen an den Entitäten
4. Aufrufen von SaveChanges zum Aktualisieren der Datenbank
5. Verwerfen der DbContext-Instanz

> [!TIP]
> Es ist nicht erforderlich, die Änderungsnachverfolgung zu löschen oder Entitätsinstanzen explizit zu trennen, wenn Sie derart vorgehen. Wenn Sie jedoch Entitäten trennen müssen, ist das Aufrufen von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> effizienter, als Entitäten einzeln zu trennen.

## <a name="entity-states"></a>Status von Entitäten

Jede Entität ist einem bestimmten <xref:Microsoft.EntityFrameworkCore.EntityState>-Wert zugeordnet:

- `Detached` Entitäten werden von <xref:Microsoft.EntityFrameworkCore.DbContext> nicht nachverfolgt.
- `Added` Entitäten sind neu und wurden noch nicht in die Datenbank eingefügt. Dies bedeutet, dass sie eingefügt werden, wenn <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aufgerufen wird.
- `Unchanged` Entitäten wurden _nicht_ geändert, seit sie von der Datenbank abgefragt wurden. Alle von Abfragen zurückgegebenen Entitäten sind anfänglich in diesem Zustand.
- `Modified` Entitäten wurden geändert, seit sie von der Datenbank abgefragt wurden. Dies bedeutet, dass sie aktualisiert werden, wenn SaveChanges aufgerufen wird.
- `Deleted` Entitäten sind in der Datenbank vorhanden, werden jedoch als gelöscht markiert, wenn SaveChanges aufgerufen wird.

EF Core verfolgt Änderungen auf der Eigenschaftenebene nach. Wenn z. B. nur ein einzelner Eigenschaftswert geändert wird, ändert ein Datenbankupdate nur diesen Wert. Eigenschaften können jedoch nur als geändert gekennzeichnet werden, wenn sich die Entität selbst im Zustand „Geändert“ befindet. (Aus alternativer Sicht bedeutet der Zustand „Geändert“, dass mindestens ein Eigenschaftswert als geändert markiert wurde.)

In der folgenden Tabelle sind die verschiedenen Status zusammengefasst:

| Entitätszustand     | Nachverfolgt von DbContext | In Datenbank vorhanden | Geänderte Eigenschaften | Aktion bei SaveChanges
|:-----------------|----------------------|--------------------|---------------------|-----------------------
| `Detached`       | Nein                   | -                  | -                   | -
| `Added`          | Ja                  | Nein                 | -                   | Einfügen
| `Unchanged`      | Ja                  | Ja                | Nein                  | -
| `Modified`       | Ja                  | Ja                | Ja                 | Aktualisieren
| `Deleted`        | Ja                  | Ja                | -                   | Löschen

> [!NOTE]
> In diesem Text werden zur Verdeutlichung Begriffe für relationale Datenbanken verwendet. NoSQL-Datenbanken unterstützen in der Regel ähnliche Vorgänge, aber möglicherweise mit anderen Namen. Nähere Informationen dazu finden Sie in der Dokumentation Ihres Datenbankanbieters.

## <a name="tracking-from-queries"></a>Nachverfolgung von Abfragen

EF Core-Änderungsnachverfolgung funktioniert am besten, wenn dieselbe <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanz verwendet wird, um Entitäten abzufragen und sie durch Aufrufen von <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> zu aktualisieren. Der Grund hierfür ist, dass EF Core den Status von abgefragten Entitäten automatisch nachverfolgt und dann alle Änderungen erkennt, die an diesen Entitäten vorgenommen werden, wenn SaveChanges aufgerufen wird.

Dieser Ansatz bietet mehrere Vorteile gegenüber der [expliziten Nachverfolgung von Entitätsinstanzen](xref:core/change-tracking/explicit-tracking):

- Er ist einfach. Entitätszustände müssen selten explizit bearbeitet werden, weil EF Core die Zustandsänderungen übernimmt.
- Updates sind auf die Werte beschränkt, die tatsächlich geändert wurden.
- Die Werte von [Schatteneigenschaften](xref:core/modeling/shadow-properties) werden beibehalten und bei Bedarf verwendet. Dies ist besonders relevant, wenn Fremdschlüssel im Schattenzustand gespeichert werden.
- Die ursprünglichen Eigenschaftswerte werden automatisch beibehalten und für effiziente Updates verwendet.

## <a name="simple-query-and-update"></a>Einfache Abfrage und Aktualisierung

Stellen Sie sich beispielsweise ein einfaches Blog/Beitrag-Modell vor:

<!--
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Model)]

Wir können dieses Modell verwenden, um Blogs und Beiträge abzufragen und dann einige Updates der Datenbank vorzunehmen:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            blog.Name = ".NET Blog (Updated!)";

            foreach (var post in blog.Posts.Where(e => !e.Title.Contains("5.0")))
            {
                post.Title = post.Title.Replace("5", "5.0");
            }

            context.SaveChanges();
-->
[!code-csharp[Simple_query_and_update_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_1)]

Das Aufrufen von SaveChanges führt zu den folgenden Datenbankupdates, wobei SQLite als Beispieldatenbank verwendet wird:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0='Announcing F# 5.0' (Size = 17)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "Title" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

Die [Debugansicht der Änderungsnachverfolgung](xref:core/change-tracking/debug-views) ist hervorragend geeignet, um zu visualisieren, welche Entitäten nachverfolgt werden und wie ihre Zustände sind. Fügen Sie beispielsweise den folgenden Code in das obige Beispiel ein, bevor Sie SaveChanges aufrufen:

<!--
                context.ChangeTracker.DetectChanges();
                Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Simple_query_and_update_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_2)]

Generiert folgende Ausgabe:

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'
  Blog: {Id: 1}
```

Beachten Sie insbesondere:

- Die `Blog.Name`-Eigenschaft wird als geändert markiert (`Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'`), und dies führt dazu, dass sich der Blog im `Modified`-Zustand befindet.
- Die `Post.Title`-Eigenschaft von Beitrag 2 wird als geändert markiert (`Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'`), und dies führt dazu, dass sich der Beitrag im `Modified`-Zustand befindet.
- Die anderen Eigenschaftswerte von Beitrag 2 wurden nicht geändert und sind daher nicht als geändert gekennzeichnet. Aus diesem Grund sind diese Werte nicht im Datenbankupdate enthalten.
- Der andere Beitrag wurde in keiner Weise geändert. Der Grund dafür ist, dass er sich noch im `Unchanged`-Zustand befindet und nicht im Datenbankupdate enthalten ist.

## <a name="query-then-insert-update-and-delete"></a>Abfragen und dann einfügen, aktualisieren und löschen

Updates wie im vorherigen Beispiel können mit Einfügungen und Löschungen in derselben Arbeitseinheit kombiniert werden. Beispiel:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Modify property values
            blog.Name = ".NET Blog (Updated!)";

            // Insert a new Post
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            // Mark an existing Post as Deleted
            var postToDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            context.Remove(postToDelete);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[Query_then_insert_update_and_delete_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Query_then_insert_update_and_delete_1)]

In diesem Beispiel:

- Ein Blog und zugehörige Beiträge werden von der Datenbank abgefragt und nachverfolgt.
- Die `Blog.Name`-Eigenschaft wird geändert.
- Der Sammlung vorhandener Beiträge für den Blog wird ein neuer Beitrag hinzugefügt.
- Ein vorhandener Beitrag ist zum Löschen durch Aufrufen von <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> markiert.

Schauen Sie sich die [Debugansicht der Änderungsnachverfolgung](xref:core/change-tracking/debug-views) erneut an, bevor Sie SaveChanges aufrufen, um zu sehen, wie EF Core diese Änderungen nachverfolgt:

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}, {Id: -2147482638}]
Post {Id: -2147482638} Added
  Id: -2147482638 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

Beachten Sie Folgendes:

- Der Blog ist als `Modified` markiert. Dadurch wird ein Datenbankupdate generiert.
- Beitrag 2 wird als `Deleted` gekennzeichnet. Dadurch wird eine Datenbanklöschung generiert.
- Ein neuer Beitrag mit einer temporären ID ist mit Blog 1 verknüpft und wird als `Added` gekennzeichnet. Dadurch wird eine Datenbankeinfügung generiert.

Dies führt zu den folgenden Datenbankbefehlen (bei Verwendung von SQLite), wenn SaveChanges aufgerufen wird:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 was released recently and has come with many...' (Size = 56), @p2='What's next for System.Text.Json?' (Size = 33)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

Weitere Informationen zum Einfügen und Löschen von Entitäten finden Sie unter [Explizites Nachverfolgen von Entitäten](xref:core/change-tracking/explicit-tracking). Weitere Informationen zur automatischen Erkennung von Änderungen wie dieser durch EF Core finden Sie unter [Änderungserkennung und Benachrichtigungen](xref:core/change-tracking/change-detection).

> [!TIP]
> Rufen Sie <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> auf, um zu bestimmen, ob Änderungen vorgenommen wurden, die SaveChanges veranlassen, Datenbankupdates vorzunehmen. Wenn HasChanges „false“ zurückgibt, wird SaveChanges nicht ausgeführt.
