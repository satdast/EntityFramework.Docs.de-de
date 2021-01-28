---
title: Explizite Nachverfolgung von Entitäten-EF Core
description: Explizites Nachverfolgen von Entitäten mit dbcontext mithilfe von hinzufügen, anfügen, aktualisieren und entfernen
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/explicit-tracking
ms.openlocfilehash: 1428096b362c8016f7924c72ec9ac3e2f9203ed6
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983273"
---
# <a name="explicitly-tracking-entities"></a>Entitäten explizit nachverfolgen

Jede <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanz verfolgt Änderungen nach, die an Entitäten vorgenommen wurden. Diese nachverfolgten Entitäten bestimmen wiederum die Änderungen an der Datenbank, wenn <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aufgerufen wird.

Entity Framework Core (EF Core)-Änderungs Nachverfolgung funktioniert am besten, wenn die gleiche <xref:Microsoft.EntityFrameworkCore.DbContext> Instanz verwendet wird, um Entitäten abzufragen und durch Aufrufen von zu aktualisieren <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> . Der Grund hierfür ist, dass EF Core den Status von abgefragten Entitäten automatisch nachverfolgt und dann alle Änderungen erkennt, die an diesen Entitäten vorgenommen werden, wenn SaveChanges aufgerufen wird. Diese Vorgehensweise wird in [Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index)behandelt.

> [!TIP]
> In diesem Dokument wird davon ausgegangen, dass die Entitäts Zustände und die Grundlagen EF Core Änderungs Nachverfolgung verstanden werden. Weitere Informationen zu diesen Themen finden Sie [unter Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) .

> [!TIP]
> Sie können den gesamten Code in diesem Dokument ausführen und debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).

> [!TIP]
> Der Einfachheit halber werden in diesem Dokument synchrone Methoden wie z. B. <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> anstatt ihrer asynchronen Entsprechungen wie <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> verwendet und darauf verwiesen. Das Aufrufen und Warten auf die asynchrone Methode kann ersetzt werden, sofern nicht anders angegeben.

## <a name="introduction"></a>Einführung

Entitäten können explizit an einen angefügt werden <xref:Microsoft.EntityFrameworkCore.DbContext> , sodass der Kontext diese Entitäten nachverfolgt. Dies ist in erster Linie nützlich, wenn:

1. Erstellen neuer Entitäten, die in die Datenbank eingefügt werden.
2. Erneutes Anfügen von getrennten Entitäten, die zuvor von einer _anderen_ dbcontext-Instanz abgefragt wurden.

Die ersten werden von den meisten Anwendungen benötigt und werden von den-Methoden als primär behandelt <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> .

Die zweite wird nur von Anwendungen benötigt, die Entitäten oder deren Beziehungen ändern, **_während die Entitäten nicht nachverfolgt werden_**. Beispielsweise kann eine Webanwendung Entitäten an den Webclient senden, an dem der Benutzer Änderungen vornimmt und die Entitäten zurücksendet. Diese Entitäten werden als "getrennt" bezeichnet, da Sie ursprünglich von einem dbcontext abgefragt wurden, aber dann beim Senden an den Client von diesem Kontext getrennt wurden.

Die Webanwendung muss nun diese Entitäten erneut anfügen, damit Sie erneut verfolgt werden, und gibt die Änderungen an, die vorgenommen wurden, <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> um geeignete Updates an der Datenbank vornehmen zu können. Dies wird hauptsächlich von der <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> -Methode und der- <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> Methode behandelt.

> [!TIP]
> Das Anfügen von Entitäten an _dieselbe dbcontext-Instanz_ , aus der Sie abgefragt wurden, sollte normalerweise nicht benötigt werden. Führen Sie nicht routinemäßig eine Abfrage ohne Nachverfolgung aus, und fügen Sie dann die zurückgegebenen Entitäten demselben Kontext hinzu. Dies ist langsamer als die Verwendung einer nach Verfolgungs Abfrage und kann auch zu Problemen führen, wie z. b. fehlende Schatten Eigenschaftswerte, sodass es schwieriger wird, richtig zu werden.

### <a name="generated-verses-explicit-key-values"></a>Generierte Verse, explizite Schlüsselwerte

Standardmäßig sind die Eigenschaften "Integer" und "GUID [Key](xref:core/modeling/keys) " so konfiguriert, dass [automatisch generierte Schlüsselwerte](xref:core/modeling/generated-properties)verwendet werden. Dies hat einen **großen Vorteil für die Änderungs Nachverfolgung: ein nicht fest gelegteter Schlüsselwert gibt an, dass die Entität "New" ist**. "New" bedeutet, dass Sie noch nicht in die Datenbank eingefügt wurde.

In den folgenden Abschnitten werden zwei Modelle verwendet. Der erste ist so konfiguriert, dass **keine** generierten Schlüsselwerte verwendet werden:

<!--
    public class Blog
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Model)]

Nicht generierte (d.h. explizit festgelegte) Schlüsselwerte werden in jedem Beispiel zuerst angezeigt, da alles sehr explizit und leicht zu befolgen ist. Danach folgt ein Beispiel, in dem generierte Schlüsselwerte verwendet werden:

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

Beachten Sie, dass die Schlüsseleigenschaften in diesem Modell hier keine zusätzliche Konfiguration erfordern, da die Verwendung von generierten Schlüsselwerten der [Standardwert für einfache ganzzahlige Schlüssel](xref:core/modeling/generated-properties)ist.

## <a name="inserting-new-entities"></a>Einfügen von neuen Entitäten

### <a name="explicit-key-values"></a>Explizite Schlüsselwerte

Eine Entität muss in dem `Added` Zustand nachverfolgt werden, der von eingefügt werden soll <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> . Entitäten werden in der Regel durch Aufrufen von <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType> oder den entsprechenden Methoden für <xref:Microsoft.EntityFrameworkCore.DbSet%601> in den hinzugefügten Zustand versetzt.

> [!TIP]
> Diese Methoden funktionieren im Kontext der Änderungs Nachverfolgung auf dieselbe Weise. Weitere Informationen finden Sie unter [zusätzliche Änderungsnachverfolgung Features](xref:core/change-tracking/miscellaneous) .

So beginnen Sie z. b. mit der Nachverfolgung eines neuen Blogs:

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Inserting_new_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_1)]

Wenn Sie die [Debugansicht der Änderungs](xref:core/change-tracking/debug-views) Nachverfolgung nach diesem-Befehl überprüfen, wird angezeigt, dass der Kontext die neue Entität im Zustand nachverfolgt `Added` :

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

Die Add-Methoden funktionieren jedoch nicht nur für eine einzelne Entität. Sie beginnen tatsächlich mit der Nachverfolgung eines voll _ständigen Diagramms verwandter Entitäten_ und versetzen alle in den `Added` Zustand. Wenn Sie z. b. einen neuen Blog und zugehörige neue Beiträge einfügen möchten:

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_2)]

Der Kontext verfolgt nun alle diese Entitäten wie folgt `Added` :

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Added
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Added
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

Beachten Sie, dass für die `Id` Schlüsseleigenschaften in den obigen Beispielen explizite Werte festgelegt wurden. Dies liegt daran, dass das Modell hier so konfiguriert wurde, dass explizit festgelegte Schlüsselwerte anstelle von automatisch generierten Schlüsselwerten verwendet werden. Wenn keine generierten Schlüssel verwendet werden, müssen die Schlüsseleigenschaften explizit festgelegt werden, _bevor_ aufgerufen wird `Add` . Diese Schlüsselwerte werden dann eingefügt, wenn SaveChanges aufgerufen wird. Wenn sqlite beispielsweise verwendet wird:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Id", "Name")
VALUES (@p0, @p1);

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String), @p3='1' (DbType = String), @p4='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p5='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p2, @p3, @p4, @p5);

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String), @p1='1' (DbType = String), @p2='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p3='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2, @p3);
```

Alle diese Entitäten werden im `Unchanged` Zustand nachverfolgt, nachdem SaveChanges abgeschlossen ist, da diese Entitäten nun in der Datenbank vorhanden sind:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

### <a name="generated-key-values"></a>Generierte Schlüsselwerte

Wie bereits erwähnt, werden ganzzahlige und GUID- [Schlüsseleigenschaften](xref:core/modeling/keys) standardmäßig für die Verwendung [automatisch generierter Schlüsselwerte](xref:core/modeling/generated-properties) konfiguriert. Dies bedeutet, dass die Anwendung _keinen Schlüsselwert explizit festlegen darf_. Wenn Sie z. b. einen neuen Blog einfügen und alle mit generierten Schlüsselwerten Posten möchten:

<!--
            context.Add(
                new Blog
                {
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Inserting_new_entities_3)]

Wie bei expliziten Schlüsselwerten verfolgt der Kontext jetzt alle folgenden Entitäten wie folgt `Added` :

```output
Blog {Id: -2147482644} Added
  Id: -2147482644 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -2147482637}, {Id: -2147482636}]
Post {Id: -2147482637} Added
  Id: -2147482637 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -2147482644}
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: -2147482644}
```

Beachten Sie, dass in diesem Fall [temporäre Schlüsselwerte](xref:core/change-tracking/miscellaneous#temporary-values) für jede Entität generiert wurden. Diese Werte werden von EF Core verwendet, bis SaveChanges aufgerufen wird, wobei echte Schlüsselwerte aus der Datenbank gelesen werden. Wenn sqlite beispielsweise verwendet wird:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Name")
VALUES (@p0);
SELECT "Id"
FROM "Blogs"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p2='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p3='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p1, @p2, @p3);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

Nachdem SaveChanges abgeschlossen ist, wurden alle Entitäten mit ihren tatsächlichen Schlüsselwerten aktualisiert und im Zustand nachverfolgt, `Unchanged` da Sie nun dem Status in der Datenbank entsprechen:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

Dabei handelt es sich genau um denselben Endzustand wie im vorherigen Beispiel, in dem explizite Schlüsselwerte verwendet wurden.

> [!TIP]
> Ein expliziter Schlüsselwert kann auch dann festgelegt werden, wenn generierte Schlüsselwerte verwendet werden. EF Core wird dann versucht, mit diesem Schlüsselwert einzufügen. Einige Daten Bank Konfigurationen, einschließlich SQL Server mit Identitäts Spalten, unterstützen solche Einfügungen nicht und lösen Sie[aus (Weitere Informationen finden](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns)Sie in diesen Dokumenten).

## <a name="attaching-existing-entities"></a>Anhängen vorhandener Entitäten

### <a name="explicit-key-values"></a>Explizite Schlüsselwerte

Von Abfragen zurückgegebene Entitäten werden im-Zustand nachverfolgt `Unchanged` . Der `Unchanged` Status bedeutet, dass die Entität seit dem Abfragen nicht geändert wurde. Eine nicht verbundene Entität, die möglicherweise von einem Webclient in einer HTTP-Anforderung zurückgegeben wird, kann in diesen Zustand versetzt werden, entweder mithilfe von <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType> oder den entsprechenden Methoden in <xref:Microsoft.EntityFrameworkCore.DbSet%601> . So beginnen Sie z. b. mit der Nachverfolgung eines vorhandenen Blogs:

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Attaching_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_1)]

> [!NOTE]
> In den hier aufgeführten Beispielen werden Entitäten `new` aus Gründen der Einfachheit explizit erstellt. Normalerweise stammen die Entitäts Instanzen aus einer anderen Quelle, z. b. Wenn Sie von einem Client deserialisiert oder aus Daten in einem HTTP Post erstellt werden.

Wenn Sie die [Debugansicht](xref:core/change-tracking/debug-views) für die Änderungs Nachverfolgung nach diesem-Befehl überprüfen, wird angezeigt, dass die Entität im- `Unchanged` Zustand

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

Ebenso wie `Add` `Attach` legt tatsächlich ein gesamtes Diagramm verbundener Entitäten auf den- `Unchanged` Zustand fest. Um beispielsweise einen vorhandenen Blog und zugehörige vorhandene Beiträge anzufügen:

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_2)]

Der Kontext verfolgt nun alle diese Entitäten wie folgt `Unchanged` :

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

Das Aufrufen von SaveChanges an dieser Stelle hat keine Auswirkungen. Alle Entitäten werden als gekennzeichnet `Unchanged` , sodass keine Aktualisierung in der Datenbank durchführt.

### <a name="generated-key-values"></a>Generierte Schlüsselwerte

Wie bereits erwähnt, werden ganzzahlige und GUID- [Schlüsseleigenschaften](xref:core/modeling/keys) standardmäßig für die Verwendung [automatisch generierter Schlüsselwerte](xref:core/modeling/generated-properties) konfiguriert. Dies hat beim Arbeiten mit getrennten Entitäten einen großen Vorteil: ein nicht festgelegter Schlüsselwert gibt an, dass die Entität noch nicht in die Datenbank eingefügt wurde. Dies ermöglicht es dem Änderungs nach Verfolgungs, neue Entitäten automatisch zu erkennen und in den Zustand zu versetzen `Added` . Nehmen Sie beispielsweise an, dieses Diagramm eines Blogs und Beiträge anzufügen:

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        },
                        new Post
                        {
                            Title = "Announcing .NET 5.0",
                            Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                        },
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Attaching_existing_entities_3)]

Der Blog hat den Schlüsselwert 1, was darauf hinweist, dass er bereits in der Datenbank vorhanden ist. Für zwei der Beiträge sind auch Schlüsselwerte festgelegt, das dritte jedoch nicht. EF Core wird dieser Schlüsselwert als 0 angezeigt, der CLR-Standardwert für eine ganze Zahl. Dies führt dazu, dass EF Core die neue Entität als `Added` anstelle von kennzeichnet `Unchanged` :

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482636}]
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
```

Das Aufrufen von SaveChanges an dieser Stelle hat keine Auswirkungen auf die `Unchanged` Entitäten, sondern fügt die neue Entität in die Datenbank ein. Wenn sqlite beispielsweise verwendet wird:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

Wichtig zu beachten ist, dass EF Core mit generierten Schlüsselwerten **automatisch neu von vorhandenen Entitäten in einem getrennten Diagramm unterscheiden** kann. Kurz gesagt: Wenn Sie generierte Schlüssel verwenden, wird EF Core immer eine Entität einfügen, wenn für diese Entität kein Schlüsselwert festgelegt ist.

## <a name="updating-existing-entities"></a>Aktualisieren vorhandener Entitäten

### <a name="explicit-key-values"></a>Explizite Schlüsselwerte

<xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType> und die äquivalenten Methoden in <xref:Microsoft.EntityFrameworkCore.DbSet%601> Verhalten sich genau wie die `Attach` oben beschriebenen Methoden, mit der Ausnahme, dass Entitäten anstelle des-Zustands in den eingefügt werden `Modfied` `Unchanged` . So beginnen Sie beispielsweise mit der Nachverfolgung eines vorhandenen Blogs wie folgt `Modified` :

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Updating_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_1)]

Wenn Sie die [Debugansicht der Änderungs](xref:core/change-tracking/debug-views) Nachverfolgung nach diesem-Befehl überprüfen, wird angezeigt, dass der Kontext diese Entität im `Modified` Zustand verfolgt:

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: []
```

Ebenso wie bei `Add` und `Attach` `Update` kennzeichnet tatsächlich ein _gesamtes Diagramm_ verwandter Entitäten als `Modified` . Fügen Sie z. b. einen vorhandenen Blog und zugehörige vorhandene Beiträge wie folgt an `Modified` :

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_2)]

Der Kontext verfolgt nun alle diese Entitäten wie folgt `Modified` :

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

Wenn Sie an diesem Punkt SaveChanges aufrufen, werden Updates für alle diese Entitäten an die Datenbank gesendet. Wenn sqlite beispielsweise verwendet wird:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();
```

### <a name="generated-key-values"></a>Generierte Schlüsselwerte

Wie bei `Attach` haben generierte Schlüsselwerte denselben größten Vorteil für `Update` : ein nicht festgelegter Schlüsselwert gibt an, dass die Entität neu ist und noch nicht in die Datenbank eingefügt wurde. Wie bei `Attach` ermöglicht es dbcontext, neue Entitäten automatisch zu erkennen und Sie in den- `Added` Zustand zu versetzen. Nehmen Sie beispielsweise an, dass Sie `Update` mit diesem Diagramm eines Blogs und Beiträgen aufrufen:

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Updating_existing_entities_3)]

Wie im `Attach` Beispiel wird der Post ohne Schlüsselwert als neu erkannt und auf den-Zustand festgelegt `Added` . Die anderen Entitäten sind wie folgt gekennzeichnet `Modified` :

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482633}]
Post {Id: -2147482633} Added
  Id: -2147482633 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

`SaveChanges`Wenn Sie an dieser Stelle aufrufen, werden Updates für alle vorhandenen Entitäten an die Datenbank gesendet, während die neue Entität eingefügt wird. Wenn sqlite beispielsweise verwendet wird:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

Dies ist eine sehr einfache Möglichkeit, um Updates und Einfügungen aus einem getrennten Diagramm zu generieren. Dies führt jedoch dazu, dass Updates oder Einfügungen für jede Eigenschaft jeder überwachten Entität an die Datenbank gesendet werden, auch wenn einige Eigenschaftswerte möglicherweise nicht geändert wurden. Das ist nicht zu ängstlich. bei vielen Anwendungen mit kleinen Diagrammen kann dies eine einfache und pragmatische Methode zum Erstellen von Updates sein. Das heißt, dass andere komplexere Muster manchmal zu effizienteren Updates führen können, wie unter [Identitäts Auflösung in EF Core](xref:core/change-tracking/identity-resolution)beschrieben.

## <a name="deleting-existing-entities"></a>Löschen vorhandener Entitäten

Damit eine Entität von SaveChanges gelöscht werden kann, muss Sie im-Zustand nachverfolgt werden `Deleted` . Entitäten werden in der Regel `Deleted` durch Aufrufen einer der <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> Methoden, <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType> oder der entsprechenden Methoden in eingefügt <xref:Microsoft.EntityFrameworkCore.DbSet%601> . So markieren Sie z. b. einen vorhandenen Beitrag wie folgt `Deleted` :

<!--
            context.Remove(
                new Post
                {
                    Id = 2
                });
-->
[!code-csharp[Deleting_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_existing_entities_1)]

Durch Überprüfen der [Debugansicht](xref:core/change-tracking/debug-views) für die Änderungs Nachverfolgung nach diesem Befehl wird angezeigt, dass der Kontext die Entität im Zustand nachverfolgt `Deleted` :

```output
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: <null> FK
  Content: <null>
  Title: <null>
  Blog: <null>
```

Diese Entität wird gelöscht, wenn SaveChanges aufgerufen wird. Wenn sqlite beispielsweise verwendet wird:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

Nachdem SaveChanges abgeschlossen wurde, wird die gelöschte Entität vom dbcontext getrennt, da Sie nicht mehr in der Datenbank vorhanden ist. Die Debugansicht ist daher leer, da keine Entitäten nachverfolgt werden.

## <a name="deleting-dependentchild-entities"></a>Löschen abhängiger/untergeordneter Entitäten

Das Löschen abhängiger/untergeordneter Entitäten aus einem Diagramm ist einfacher als das Löschen von Prinzipal-/Unterordnung- Weitere Informationen finden Sie im nächsten Abschnitt und unter [Ändern von Fremdschlüsseln und Navigationen](xref:core/change-tracking/relationship-changes) .

Es ist ungewöhnlich, für `Remove` eine Entität aufzurufen, die mit erstellt wurde `new` . Außerdem `Add` `Attach` ist es im Gegensatz `Update` zu und nicht üblich, `Remove` für eine Entität aufzurufen, die nicht bereits im-Zustand oder im-Zustand nachverfolgt wird `Unchanged` `Modified` . Stattdessen ist es typisch, eine einzelne Entität oder ein Diagramm verwandter Entitäten zu verfolgen und dann `Remove` für die Entitäten aufzurufen, die gelöscht werden sollen. Dieses Diagramm der nach verfolgten Entitäten wird in der Regel durch Folgendes erstellt:

1. Ausführen einer Abfrage für die Entitäten
2. Verwenden der- `Attach` Methode oder der- `Update` Methode in einem Diagramm von getrennten Entitäten, wie in den vorherigen Abschnitten beschrieben.

Beispielsweise ist der Code im vorherigen Abschnitt wahrscheinlicher, dass er einen Beitrag von einem Client erhält, und dann etwa wie folgt vorgehen:

<!--
            context.Attach(post);
            context.Remove(post);
-->
[!code-csharp[Deleting_dependent_child_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_1)]

Dies verhält sich genau so wie das vorherige Beispiel, da der Aufruf `Remove` von für eine nicht verfolgte Entität bewirkt, dass Sie zuerst angefügt und dann als markiert wird `Deleted` .

In realistischeren Beispielen wird zunächst ein Diagramm mit Entitäten angehängt, und einige dieser Entitäten werden als gelöscht markiert. Beispiel:

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_dependent_child_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_2)]

Alle Entitäten werden als gekennzeichnet `Unchanged` , mit Ausnahme derjenigen, für die `Remove` aufgerufen wurde:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
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

Diese Entität wird gelöscht, wenn SaveChanges aufgerufen wird. Wenn sqlite beispielsweise verwendet wird:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

Nachdem SaveChanges abgeschlossen wurde, wird die gelöschte Entität vom dbcontext getrennt, da Sie nicht mehr in der Datenbank vorhanden ist. Andere Entitäten verbleiben im `Unchanged` Status:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
```

## <a name="deleting-principalparent-entities"></a>Löschen von Principal/Parent-Entitäten

Jede Beziehung, die zwei Entitäts Typen verbindet, verfügt über ein Prinzipal-oder übergeordnetes Ende und ein abhängiges oder untergeordnetes Ende. Die abhängige/untergeordnete Entität ist die, die die Fremdschlüssel Eigenschaft hat. In einer 1: n-Beziehung befindet sich der Prinzipal/das übergeordnete Element auf der Seite "1", und das abhängige/untergeordnete Element befindet sich auf der Seite "Many". Weitere Informationen finden Sie unter [Beziehungen](xref:core/modeling/relationships) .

In den vorangehenden Beispielen haben wir einen Beitrag gelöscht, bei dem es sich um eine abhängige/untergeordnete Entität im Blogbeitrag 1: n-Beziehung handelt. Dies ist relativ unkompliziert, da das Entfernen einer abhängigen/untergeordneten Entität keine Auswirkung auf andere Entitäten hat. Andererseits muss das Löschen einer Prinzipal-/Parent-Entität auch Auswirkungen auf abhängige/untergeordnete Entitäten haben. Wenn Sie dies nicht tun, würde ein Fremdschlüssel Wert, der auf einen Primärschlüssel Wert verweist, nicht mehr vorhanden sein. Dies ist ein ungültiger Modell Status und führt in den meisten Datenbanken zu einem referenziellen Einschränkungs Fehler.

Dieser ungültige Modell Zustand kann auf zwei Arten behandelt werden:

1. Festlegen von FK-Werten auf NULL. Dies gibt an, dass die abhängigen Elemente/untergeordneten Elemente nicht mehr mit einem Prinzipal/übergeordneten Element verknüpft sind. Dies ist die Standardeinstellung für optionale Beziehungen, bei denen der Fremdschlüssel NULL-Werte zulassen muss. Das Festlegen des FK auf NULL ist für erforderliche Beziehungen ungültig, bei denen der Fremdschlüssel in der Regel keine NULL-Werte zulässt.
2. Die abhängigen/untergeordneten Elemente werden gelöscht. Dies ist der Standardwert für erforderliche Beziehungen, der auch für optionale Beziehungen gültig ist.

Ausführliche Informationen zur Änderungs Nachverfolgung und zu Beziehungen finden Sie unter [Ändern von Fremdschlüsseln und Navigationen](xref:core/change-tracking/relationship-changes) .

### <a name="optional-relationships"></a>Optionale Beziehungen

Die `Post.BlogId` Fremdschlüssel Eigenschaft kann im verwendeten Modell auf NULL festgelegt werden. Dies bedeutet, dass die Beziehung optional ist. Daher besteht das Standardverhalten von EF Core darin, `BlogId` Fremdschlüssel Eigenschaften auf NULL festzulegen, wenn der Blog gelöscht wird. Beispiel:

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_principal_parent_entities_1)]

Wenn Sie die [Debugansicht](xref:core/change-tracking/debug-views) für die Änderungs Nachverfolgung nach dem Aufrufen von untersuchen, `Remove` wird angezeigt, dass der Blog wie erwartet nun wie erwartet gekennzeichnet ist `Deleted` :

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

Interessanterweise sind alle zugehörigen Beiträge nun als markiert `Modified` . Dies liegt daran, dass die Fremdschlüssel Eigenschaft in den einzelnen Entitäten auf NULL festgelegt wurde. Wenn Sie SaveChanges aufrufen, wird der Fremdschlüssel Wert für jeden Post in NULL in der Datenbank aktualisiert, bevor der Blog gelöscht wird:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p2;
SELECT changes();
```

Nachdem SaveChanges abgeschlossen wurde, wird die gelöschte Entität vom dbcontext getrennt, da Sie nicht mehr in der Datenbank vorhanden ist. Andere Entitäten sind nun als `Unchanged` mit NULL-Fremdschlüssel Werten gekennzeichnet, die mit dem Status der Datenbank übereinstimmen:

```output
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: <null> FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: <null> FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

### <a name="required-relationships"></a>Erforderliche Beziehungen

Wenn die `Post.BlogId` Fremdschlüssel Eigenschaft keine NULL-Werte zulässt, wird die Beziehung zwischen Blogs und Beiträgen zu "erforderlich". In dieser Situation löschen EF Core standardmäßig abhängige/untergeordnete Entitäten, wenn der Prinzipal/das übergeordnete Element gelöscht wird. Löschen Sie beispielsweise einen Blog mit verwandten Beiträgen wie im vorherigen Beispiel:

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysRequiredSamples.cs?name=Deleting_principal_parent_entities_1)]

Wenn Sie die [Debugansicht](xref:core/change-tracking/debug-views) für die Änderungs Nachverfolgung nach dem Aufrufen von untersuchen, `Remove` wird angezeigt, dass der Blog wie erwartet mit folgendem gekennzeichnet ist `Deleted` :

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Deleted
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

Interessanter ist in diesem Fall, dass alle zugehörigen Beiträge auch als gekennzeichnet wurden `Deleted` . Das Aufrufen von SaveChanges bewirkt, dass der Blog und alle zugehörigen Beiträge aus der Datenbank gelöscht werden:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p1;
```

Nachdem SaveChanges abgeschlossen ist, werden alle gelöschten Entitäten von dbcontext getrennt, da Sie nicht mehr in der Datenbank vorhanden sind. Die Ausgabe der Debugansicht ist daher leer.

> [!NOTE]
> In diesem Dokument wird nur die Oberfläche beim Arbeiten mit Beziehungen in EF Core. Weitere [Informationen zum Aktualisieren](xref:core/change-tracking/relationship-changes) /löschen abhängiger/untergeordneter Entitäten beim Aufrufen von SaveChanges finden Sie unter [Beziehungen](xref:core/modeling/relationships) .

## <a name="custom-tracking-with-trackgraph"></a>Benutzerdefinierte Nachverfolgung mit trackgraph

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> funktioniert wie `Add` , `Attach` und `Update` mit der Ausnahme, dass ein Rückruf für jede Entitäts Instanz generiert wird, bevor Sie nachverfolgt wird. Dadurch kann eine benutzerdefinierte Logik verwendet werden, um zu bestimmen, wie einzelne Entitäten in einem Diagramm nachverfolgt werden.

Nehmen Sie z. b. die Regel EF Core die beim Nachverfolgen von Entitäten mit generierten Schlüsselwerten verwendet: Wenn der Schlüsselwert 0 (null) ist, ist die Entität neu und sollte eingefügt werden. Wir erweitern diese Regel, um zu sagen, dass der Schlüsselwert negativ ist, dann sollte die Entität gelöscht werden. Dies ermöglicht es uns, die Primärschlüssel Werte in Entitäten eines getrennten Diagramms so zu ändern, dass gelöschte Entitäten markiert werden:

<!--
            blog.Posts.Add(
                new Post
                {
                    Title = "Announcing .NET 5.0",
                    Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                }
            );

            var toDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            toDelete.Id = -toDelete.Id;
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1a](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1a)]

Dieses getrennte Diagramm kann dann mithilfe von trackgraph nachverfolgt werden:

<!--
        public static void UpdateBlog(Blog blog)
        {
            using var context = new BlogsContext();

            context.ChangeTracker.TrackGraph(
                blog, node =>
                    {
                        var propertyEntry = node.Entry.Property("Id");
                        var keyValue = (int)propertyEntry.CurrentValue;

                        if (keyValue == 0)
                        {
                            node.Entry.State = EntityState.Added;
                        }
                        else if (keyValue < 0)
                        {
                            propertyEntry.CurrentValue = -keyValue;
                            node.Entry.State = EntityState.Deleted;
                        }
                        else
                        {
                            node.Entry.State = EntityState.Modified;
                        }

                        Console.WriteLine($"Tracking {node.Entry.Metadata.DisplayName()} with key value {keyValue} as {node.Entry.State}");

                    });

            context.SaveChanges();
        }
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1b](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1b)]

Für jede Entität im Diagramm prüft der obige Code den Primärschlüssel Wert, _bevor die Entität nachverfolgt_ wird. Für nicht festgelegte (0) Schlüsselwerte führt der Code die EF Core normalerweise durch. Das heißt, wenn der Schlüssel nicht festgelegt ist, wird die Entität als markiert `Added` . Wenn der Schlüssel festgelegt ist und der Wert nicht negativ ist, wird die Entität als markiert `Modified` . Wenn jedoch ein negativer Schlüsselwert gefunden wird, wird der tatsächliche, nicht negative Wert wieder hergestellt, und die Entität wird als nachverfolgt `Deleted` .

Die Ausgabe aus dem Ausführen dieses Codes lautet wie folgt:

```output
Tracking Blog with key value 1 as Modified
Tracking Post with key value 1 as Modified
Tracking Post with key value -2 as Deleted
Tracking Post with key value 0 as Added
```

> [!NOTE]
> Der Einfachheit halber geht dieser Code davon aus, dass jede Entität über eine ganzzahlige Primärschlüssel `Id` Eigenschaft namens verfügt Dies könnte in eine abstrakte Basisklasse oder Schnittstelle cogeschrieben werden. Alternativ können die Primärschlüssel Eigenschaft oder die Eigenschaften aus den Metadaten abgerufen werden <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> , sodass dieser Code mit jedem beliebigen Entitätstyp funktionieren würde.

Trackgraph verfügt über zwei über Ladungen. In der oben verwendeten einfachen Überladung bestimmt EF Core, wann das Durchlaufen des Diagramms beendet werden soll. Insbesondere werden neue verknüpfte Entitäten aus einer bestimmten Entität nicht mehr besucht, wenn diese Entität entweder bereits nachverfolgt wurde oder wenn der Rückruf nicht mit der Nachverfolgung der Entität beginnt.

Die erweiterte Überladung <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType> verfügt über einen Rückruf, der einen booleschen Wert zurückgibt. Wenn der Rückruf false zurückgibt, wird der Diagramm Durchlauf beendet, andernfalls wird der Vorgang fortgesetzt. Es muss darauf geachtet werden, dass bei Verwendung dieser Überladung unendliche Schleifen vermieden werden.

Die erweiterte Überladung ermöglicht auch das Bereitstellen des Zustands für trackgraph, und dieser Zustand wird dann an jeden Rückruf übergeben.
