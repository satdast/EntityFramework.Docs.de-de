---
title: Kaskadierendes Delete – EF Core
description: In diesem Artikel finden Sie Informationen zur Konfiguration des kaskadierenden Verhaltens, das ausgelöst wird, wenn eine Entität gelöscht oder vom Prinzipal bzw. der übergeordneten Entität getrennt wird.
author: ajcvickers
ms.date: 01/07/2021
uid: core/saving/cascade-delete
ms.openlocfilehash: 27ba84fa5d7e0d72e66ccbd96df9b6a5008791fb
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983338"
---
# <a name="cascade-delete"></a>Kaskadierendes Delete

Entity Framework Core (EF Core) stellt Beziehungen mithilfe von Fremdschlüsseln dar. Eine Entität mit einem Fremdschlüssel ist die untergeordnete oder abhängige Entität in der Beziehung. Der Fremdschlüsselwert der Entität muss mit dem Primärschlüsselwert (oder einem alternativen Schlüsselwert) des verwandten Prinzipals bzw. der übergeordneten Entität übereinstimmen.

Wenn der Prinzipal bzw. die übergeordnete Entität gelöscht wird, stimmen die Fremdschlüsselwerte der abhängigen/untergeordneten Entitäten nicht mehr mit dem Primärschlüssel oder alternativen Schlüssel eines _beliebigen_ Prinzipals bzw. einer übergeordneten Entität überein. Dabei handelt es sich um einen ungültigen Zustand, der bei den meisten Datenbanken zu einem Verstoß gegen eine referenzielle Einschränkung führt.

Es gibt zwei Möglichkeiten, diesen Verstoß gegen eine referenzielle Einschränkung zu umgehen:

1. Legen Sie die FK-Werte auf NULL fest.
2. Löschen Sie auch die abhängigen/untergeordneten Entitäten.

Die erste Option ist nur für optionale Beziehungen zulässig, bei denen die Fremdschlüsseleigenschaft (und die Datenbankspalte, der sie zugeordnet ist) NULL-Werte zulassen muss.

Die zweite Option ist für jede Art von Beziehung zulässig und wird als „kaskadierendes Delete“ bezeichnet.

> [!TIP]
> In dieser Dokumentation wird das kaskadierende Delete (und das Löschen verwaister Entitäten) aus der Perspektive von Updates der Datenbank beschrieben. Dabei werden die in [Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) und [Ändern von Fremdschlüsseln und Navigationen](xref:core/change-tracking/relationship-changes) vorgestellten Konzepte ausgiebig verwendet. Stellen Sie sicher, dass Sie diese Konzepte vollständig verstehen, bevor Sie sich mit dem Inhalt dieses Artikels beschäftigen.

> [!TIP]  
> Sie können den gesamten Code in dieser Dokumentation ausführen und debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/CascadeDeletes).

## <a name="when-cascading-behaviors-happen"></a>Wann kaskadierendes Verhalten auftritt

Kaskadierende Deletes sind erforderlich, wenn eine abhängige/untergeordnete Entität nicht mehr dem Prinzipal bzw. der übergeordneten Entität zugeordnet werden kann. Dieser Fall kann auftreten, wenn der Prinzipal bzw. die übergeordnete Entität gelöscht wird oder die abhängige/untergeordnete Entität nicht mehr zugeordnet ist.

### <a name="deleting-a-principalparent"></a>Löschen eines Prinzipal bzw. einer übergeordneten Entität

Ziehen Sie dieses einfache Modell in Betracht, bei dem `Blog` der Prinzipal bzw. die übergeordnete Entität in einer Beziehung mit der abhängigen/untergeordneten Entität `Post` ist. `Post.BlogId` ist eine Fremdschlüsseleigenschaft, deren Wert mit dem Primärschlüssel `Post.Id` des Beitrags übereinstimmen muss, zu dem der Blog gehört.

<!--
    public class Blog
    {
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Model)]

Gemäß der Konventionen wird diese Beziehung als erforderlich konfiguriert, da die Fremdschlüsseleigenschaft `Post.BlogId` keine NULL-Werte zulässt. Erforderliche Beziehungen werden standardmäßig zur Verwendung von kaskadierenden Deletes konfiguriert. Weitere Informationen zum Modellieren von Beziehungen finden Sie unter [Beziehungen](xref:core/modeling/relationships).

Beim Löschen eines Blogs werden alle Beiträge durch kaskadierende Deletes gelöscht. Beispiel:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Deleting_principal_parent_1)]

SaveChanges generiert beispielsweise den folgenden SQL-Code mithilfe von SQL Server:

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="severing-a-relationship"></a>Aufheben einer Beziehung

Anstatt den Blog zu löschen, können Sie auch die Beziehung zwischen jedem Beitrag und dem zugehörigen Blog löschen. Hierzu legen Sie die Referenznavigation `Post.Blog` für jeden Beitrag auf NULL fest:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_1)]

Die Beziehung kann auch durch Entfernen aller Beiträge aus der Sammlungsnavigation `Blog.Posts` aufgehoben werden:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Severing_a_relationship_2)]

In beiden Fällen ist das Ergebnis identisch: der Blog wird nicht gelöscht, aber die Beiträge werden gelöscht, die keinem Blog mehr zugeordnet sind:

```sql
-- Executed DbCommand (1ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p0='2'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Posts]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

Das Löschen von Entitäten, die nicht mehr einem Prinzipal bzw. einer abhängigen Entität zugeordnet sind, wird als „Löschen verwaister Entitäten“ bezeichnet.

> [!TIP]
> Das kaskadierende Delete und das Löschen verwaister Entitäten sind eng verwandt. Beide führen dazu, dass abhängige/untergeordnete Entitäten gelöscht werden, wenn die Beziehung zum erforderlichen Prinzipal bzw. der übergeordneten Entität getrennt wird. Beim kaskadierenden Delete erfolgt diese Trennung, weil der Prinzipal bzw. die übergeordnete Entität selbst gelöscht wird. Bei verwaisten Entitäten ist der Prinzipal bzw. die übergeordnete Entität weiterhin vorhanden, jedoch besteht die Beziehung zur abhängigen/untergeordneten Entität nicht mehr.  

## <a name="where-cascading-behaviors-happen"></a>Wo kaskadierendes Verhalten auftritt

Kaskadierendes Verhalten kann in folgenden Szenarios angewendet werden:

- Entitäten, die durch die aktuelle <xref:Microsoft.EntityFrameworkCore.DbContext>-Klasse überwacht werden
- Entitäten in der Datenbank, die nicht in den Kontext geladen wurden

### <a name="cascade-delete-of-tracked-entities"></a>Kaskadierendes Delete für überwachte Entitäten

EF Core wendet kaskadierendes Verhalten immer auf überwachte Entitäten an. Das bedeutet, dass kaskadierende Verhaltensweisen unabhängig von der Datenbankkonfiguration immer ordnungsgemäß angewendet werden, wenn die Anwendung alle relevanten abhängigen/untergeordneten Entitäten in die DbContext-Klasse lädt (siehe Beispiel oben).

> [!TIP]
> Das genaue Timing für die Anwendung kaskadierender Verhaltensweisen auf überwachte Entitäten kann mit <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> gesteuert werden. Weitere Informationen finden Sie unter [Ändern von Fremdschlüsseln und Navigationen](xref:core/change-tracking/relationship-changes).

### <a name="cascade-delete-in-the-database"></a>Kaskadierendes Delete in der Datenbank

Viele Datenbanksysteme bieten auch kaskadierende Verhalten, die ausgelöst werden, wenn eine Entität in der Datenbank gelöscht wird. EF Core konfiguriert diese Verhalten anhand der Verhaltensweise des kaskadierenden Deletes im EF Core-Modell, wenn eine Datenbank mit <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> oder EF Core-Migrationen erstellt wird. Bei Verwendung des obigen Modells wird beispielsweise die folgende Tabelle für Beiträge erstellt, wenn SQL Server verwendet wird:

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    [BlogId] int NOT NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Posts_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([Id]) ON DELETE CASCADE
);
```

Beachten Sie, dass die Einschränkung für Fremdschlüssel, die die Beziehung zwischen Blogs und Beiträgen definieren, mit `ON DELETE CASCADE` konfiguriert ist.

Wenn Sie wissen, dass die Datenbank diese Konfiguration aufweist, können Sie einen Blog löschen, _ohne zuerst die Beiträge zu laden_. Die Datenbank übernimmt dann das Löschen aller Beiträge, die mit dem Blog verwandt waren. Beispiel:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Where_cascading_behaviors_happen_1](../../../samples/core/CascadeDeletes/IntroRequiredSamples.cs?name=Where_cascading_behaviors_happen_1)]

Beachten Sie, dass es keine `Include`-Anweisung für Beiträge gibt. Diese werden also nicht geladen. In diesem Fall wird mit SaveChanges nur der Blog gelöscht, da dieser die einzige Entität darstellt, die überwacht wird:

```sql
-- Executed DbCommand (6ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;
```

Dies würde zu einer Ausnahme führen, wenn die Fremdschlüsseleinschränkung in der Datenbank nicht für kaskadierende Deletes konfiguriert ist. In diesem Fall werden die Beiträge von der Datenbank gelöscht, da sie bei der Erstellung mit `ON DELETE CASCADE` konfiguriert wurde.

> [!NOTE]
> Datenbanken verfügen normalerweise über keine Möglichkeit zum automatischen Löschen von verwaisten Entitäten. Das liegt daran, dass Datenbanken nur über Fremdschlüssel und über keine Navigationen verfügen, obwohl EF Core Beziehungen mithilfe von Navigationen und Fremdschlüsseln darstellt. Das bedeutet, dass es in der Regel nicht möglich ist, eine Beziehung zu trennen, ohne beide Seiten in die DbContext-Klasse zu laden.

> [!NOTE]
> Die In-Memory-Datenbank von EF Core unterstützt kaskadierende Deletes in der Datenbank derzeit nicht.

> [!WARNING]
> Konfigurieren Sie beim vorläufigen Löschen von Entitäten kein kaskadierendes Delete in der Datenbank. Dies kann dazu führen, dass Entitäten versehentlich tatsächlich gelöscht werden, anstatt nur vorläufig gelöscht zu werden.

### <a name="database-cascade-limitations"></a>Einschränkungen bei kaskadierendem Verhalten in Datenbanken

Einige Datenbanken, insbesondere SQL Server, weisen Einschränkungen bei den kaskadierenden Verhaltensweisen auf, die Zyklen bilden. Betrachten Sie beispielsweise das folgende Modell:

<!--
    public class Blog
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
        
        public int OwnerId { get; set; }
        public Person Owner { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
        
        public int AuthorId { get; set; }
        public Person Author { get; set; }
    }

    public class Person
    {
        public int Id { get; set; }
        public string Name { get; set; }
        
        public IList<Post> Posts { get; } = new List<Post>();

        public Blog OwnedBlog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Model)]

Dieses Modell umfasst drei Beziehungen, die alle erforderlich sind und daher konventionsbedingt für kaskadierende Deletes konfiguriert sind:

- Das Löschen eines Blogs führt zu einem kaskadierenden Delete aller zugehöriger Beiträge.
- Das Löschen des Erstellers von Beiträgen führt zu einem kaskadierenden Delete dieser Beiträge.
- Das Löschen des Besitzers eines Blogs führt zu einem kaskadierenden Delete des Blogs.

Dieses Verhalten ist sinnvoll (und höchstens streng im Kontext von Blogverwaltungsrichtlinien), jedoch könnte ein Versuch, eine SQL Server-Datenbank mit diesen kaskadierenden Deletes zu erstellen, die folgende Ausnahme auslösen:

> Microsoft.Data.SqlClient.SqlException (0x80131904): Das Einführen der FOREIGN KEY-Einschränkung 'FK_Posts_Person_AuthorId' für die 'Posts'-Tabelle kann Schleifen oder mehrere kaskadierende Pfade verursachen. Geben Sie ON DELETE NO ACTION oder ON UPDATE NO ACTION an, oder ändern Sie andere FOREIGN KEY-Einschränkungen.

Es gibt zwei Möglichkeiten, diese Situation zu beheben:

1. Ändern Sie mindestens eine der Beziehungen so, dass kein kaskadierendes Delete durchgeführt wird.
2. Konfigurieren Sie die Datenbanken ohne diese kaskadierenden Deletes, und stellen Sie dann sicher, dass alle abhängigen Entitäten geladen werden, damit EF Core das kaskadierende Verhalten durchführen kann.

Wenn Sie beispielsweise den ersten Ansatz im Beispiel anwenden, könnten Sie die Blogbesitzerbeziehung als optional einrichten, indem Sie diese mit einer NULL-Werte zulassenden Fremdschlüsseleigenschaft versehen.

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

Eine optionale Beziehung ermöglicht, dass der Blog ohne Besitzer vorhanden ist, was bedeutet, dass das kaskadierende Delete nicht mehr standardmäßig konfiguriert ist. Aus diesem Grund besteht der Zyklus aus kaskadierenden Aktionen nicht mehr, und die Datenbank kann ohne Fehler in SQL Server erstellt werden.

Wenn Sie hingegen den zweiten Ansatz anwenden, können Sie die Blogbesitzerbeziehung als erforderlich und mit der Konfiguration für ein kaskadierendes Delete beibehalten, aber die Konfiguration so einrichten, dass sie nur auf überwachte Entitäten, anstelle der gesamten Datenbank angewendet wird:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

Was geschieht jedoch nun, wenn sowohl eine Person als auch der sich im Besitz dieser Person befindende Blog geladen werden und dann die Person gelöscht wird?

<!--
            using var context = new BlogsContext();

            var owner = context.People.Single(e => e.Name == "ajcvickers");
            var blog = context.Blogs.Single(e => e.Owner == owner);

            context.Remove(owner);
            
            context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_1](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_1)]

EF Core führt ein kaskadierendes Delete für den Besitzer durch, sodass der Blog ebenfalls gelöscht wird:

```sql
-- Executed DbCommand (8ms) [Parameters=[@p0='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p0;
SELECT @@ROWCOUNT;

-- Executed DbCommand (2ms) [Parameters=[@p1='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [People]
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

Wenn der Blog jedoch nicht geladen ist, wenn der Besitzer gelöscht wird:

<!--
                using var context = new BlogsContext();

                var owner = context.People.Single(e => e.Name == "ajcvickers");

                context.Remove(owner);
            
                context.SaveChanges();
-->
[!code-csharp[Database_cascade_limitations_2](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=Database_cascade_limitations_2)]

Dann wird eine Ausnahme aufgrund des Verstoßes gegen die Fremdschlüsseleinschränkung in der Datenbank ausgelöst:

> Microsoft.Data.SqlClient.SqlException: Die DELETE-Anweisung steht in Konflikt mit der REFERENCE-Einschränkung „FK_Blogs_People_OwnerId“. Der Konflikt ist in der Spalte „OwnerId“ der Tabelle „dbo.Blogs“ in der Datenbank „Scratch“ aufgetreten.
Die Anweisung wurde beendet.

## <a name="cascading-nulls"></a>Kaskadierende NULL-Werte

Optionale Beziehungen umfassen NULL-Werte zulassende Fremdschlüsseleigenschaften, die NULL-Werte zulassenden Datenbankspalten zugeordnet sind. Das bedeutet, dass der Fremdschlüsselwert auf NULL festgelegt werden kann, wenn der aktuelle Prinzipal bzw. die übergeordnete Entität gelöscht oder von der abhängigen/untergeordneten Entität getrennt wird.

Im Folgenden werden die Beispiele aus dem Abschnitt [Wann kaskadierendes Verhalten auftritt](#when-cascading-behaviors-happen) noch mal untersucht, jedoch verfügen sie diesmal über eine optionale Beziehung, die von einer NULL-Werte zulassenden `Post.BlogId`-Fremdschlüsseleigenschaft dargestellt wird:

<!--
            public int? BlogId { get; set; }
-->
[!code-csharp[NullableBlogId](../../../samples/core/CascadeDeletes/OptionalDependentsSamples.cs?name=NullableBlogId)]

Diese Fremdschlüsseleigenschaft wird für jeden Beitrag auf NULL festgelegt, wenn der zugehörige Blog gelöscht wird. Ein Beispiel hierfür ist der folgende Code, der identisch mit dem vorherigen ist:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            context.Remove(blog);
            
            context.SaveChanges();
-->
[!code-csharp[Deleting_principal_parent_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Deleting_principal_parent_1b)]

Der Code resultiert nun in den folgenden Updates der Datenbank, wenn SaveChanges aufgerufen wird:

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (1ms) [Parameters=[@p2='1'], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
DELETE FROM [Blogs]
WHERE [Id] = @p2;
SELECT @@ROWCOUNT;
```

Dies gilt auch, wenn die Beziehung mit einem der oben genannten Beispiele getrennt wird:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            foreach (var post in blog.Posts)
            {
                post.Blog = null;
            }
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_1b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_1b)]

Oder:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.OrderBy(e => e.Name).Include(e => e.Posts).First();

            blog.Posts.Clear();
            
            context.SaveChanges();
-->
[!code-csharp[Severing_a_relationship_2b](../../../samples/core/CascadeDeletes/IntroOptionalSamples.cs?name=Severing_a_relationship_2b)]

Dann werden die Beiträge mit NULL-Fremdschlüsselwerten aktualisiert, wenn SaveChanges aufgerufen wird:

```sql
-- Executed DbCommand (2ms) [Parameters=[@p1='1', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;

-- Executed DbCommand (0ms) [Parameters=[@p1='2', @p0=NULL (DbType = Int32)], CommandType='Text', CommandTimeout='30']
SET NOCOUNT ON;
UPDATE [Posts] SET [BlogId] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

Weitere Informationen zur EF Core-Verwaltung von Fremdschlüsseln und Navigationen bei Änderungen ihrer Werte finden Sie unter [Ändern von Fremdschlüsseln und Navigationen](xref:core/change-tracking/relationship-changes).

> [!NOTE]
> Die Behebung von Beziehungen wie dieser stellt seit der ersten Version von Entity Framework in 2008 das Standardverhalten dar. Vor EF Core gab es keinen Namen für dieses Verhalten und es konnte nicht geändert werden. Nun ist es wie im nächsten Abschnitt beschrieben als `ClientSetNull` bekannt.

Datenbanken können auf diese Weise auch für kaskadierende NULL-Werte konfiguriert werden, wenn ein Prinzipal bzw. eine übergeordnete Entität in einer optionalen Beziehung gelöscht wird. Allerdings ist dieser Fall weitaus seltener als die Verwendung von kaskadierenden Deletes in der Datenbank. Die gleichzeitige Verwendung von kaskadierenden Deletes und kaskadierenden NULL-Werten in der Datenbank führt fast immer zu Beziehungszyklen, wenn Sie SQL Server verwenden. Weitere Informationen zur Konfiguration von kaskadierenden NULL-Werten finden Sie im nächsten Abschnitt.

## <a name="configuring-cascading-behaviors"></a>Konfigurieren von kaskadierendem Verhalten

> [!TIP]
> Lesen Sie unbedingt die vorherigen Abschnitte, bevor Sie mit diesem Abschnitt beginnen. Sie werden die hier erläuterten Konfigurationsoptionen vermutlich nicht verstehen, wenn Sie die vorherigen Informationen nicht verinnerlicht haben.

Kaskadierende Verhalten werden mithilfe der <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.ReferenceCollectionBuilder.OnDelete%2A>-Methode in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> pro Beziehung konfiguriert. Beispiel:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .Entity<Blog>()
                .HasOne(e => e.Owner)
                .WithOne(e => e.OwnedBlog)
                .OnDelete(DeleteBehavior.ClientCascade);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/CascadeDeletes/WithDatabaseCycleSamples.cs?name=OnModelCreating)]

Weitere Informationen zum Konfigurieren von Beziehungen zwischen Entitätstypen finden Sie unter [Beziehungen](xref:core/modeling/relationships).

`OnDelete` akzeptiert einen Wert aus der <xref:Microsoft.EntityFrameworkCore.DeleteBehavior>-Enumeration (was zugegebenermaßen verwirrend sein kann). Diese Enumeration definiert sowohl das Verhalten von EF Core für überwachte Entitäten und die Konfiguration von kaskadierenden Deletes in der Datenbank, wenn Entity Framework zum Erstellen des Schemas verwendet wird.

### <a name="impact-on-database-schema"></a>Auswirkungen auf das Datenbankschema

In der folgenden Tabelle werden die Ergebnisse der einzelnen `OnDelete`-Werte für die Fremdschlüsseleinschränkung aufgeführt, die von EF Core-Migrationen oder <xref:Microsoft.EntityFrameworkCore.Infrastructure.DatabaseFacade.EnsureCreated%2A> erstellt wird.

| DeleteBehavior        | Auswirkungen auf das Datenbankschema
|:----------------------|--------------------------
| Cascade               | ON DELETE CASCADE
| Einschränken              | ON DELETE NO ACTION
| NoAction              | Datenbankstandard
| SetNull               | ON DELETE SET NULL
| ClientSetNull         | ON DELETE NO ACTION
| ClientCascade         | ON DELETE NO ACTION
| ClientNoAction        | Datenbankstandard

> [!NOTE]
> Da diese Tabelle verwirrend sein kann, wird sie in einem zukünftigen Release überarbeitet. Weitere Informationen finden Sie unter [GitHub-Issue #21252](https://github.com/dotnet/efcore/issues/21252).

Die Verhaltensweisen von `ON DELETE NO ACTION` und `ON DELETE RESTRICT` in relationalen Datenbanken sind in der Regel entweder identisch oder sehr ähnlich. Unabhängig davon, was von `NO ACTION` impliziert wird, führen beide dieser Optionen zu referenziellen Einschränkungen. Sofern es einen Unterschied gibt, besteht dieser darin, _wann_ die Datenbank die Einschränkungen überprüft.  Überprüfen Sie die Dokumentation Ihrer Datenbank auf spezifische Unterschiede zwischen `ON DELETE NO ACTION` und `ON DELETE RESTRICT` für Ihr Datenbanksystem.

Die einzigen Werte, die kaskadierendes Verhalten in der Datenbank auslösen, sind `Cascade` und `SetNull`. Alle anderen Werte konfigurieren die Datenbank so, dass keine kaskadierenden Änderungen auftreten.

### <a name="impact-on-savechanges-behavior"></a>Auswirkung auf das Verhalten von SaveChanges

Die Tabellen in den folgenden Abschnitten befassen sich damit, was mit den abhängigen/untergeordneten Entitäten geschieht, wenn der Prinzipal bzw. die übergeordnete Entität gelöscht oder die Beziehung zu den abhängigen/untergeordneten Entitäten getrennt wird. Jede Tabelle umfasst:

- Optionale (NULL-Werte zulassende Fremdschlüssel) und erforderliche (NULL-Werte nicht zulassende Fremdschlüssel) Beziehungen
- Wann abhängige/untergeordnete Entitäten geladen und von der DbContext-Klasse überwacht werden, sowie wann sie nur in der Datenbank vorhanden sind

#### <a name="required-relationship-with-dependentschildren-loaded"></a>Erforderliche Beziehung mit geladenen abhängigen/untergeordneten Entitäten

| DeleteBehavior    | Bei Löschung des Prinzipals bzw. der übergeordneten Entität             | Bei Trennung der Beziehung zum Prinzipal bzw. zur übergeordneten Entität
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | Abhängige Entitäten werden von EF Core gelöscht            | Abhängige Entitäten werden von EF Core gelöscht
| Einschränken          | `InvalidOperationException`              | `InvalidOperationException`
| NoAction          | `InvalidOperationException`              | `InvalidOperationException`
| SetNull           | `SqlException` bei Erstellung der Datenbank      | `SqlException` bei Erstellung der Datenbank
| ClientSetNull     | `InvalidOperationException`              | `InvalidOperationException`
| ClientCascade     | Abhängige Entitäten werden von EF Core gelöscht            | Abhängige Entitäten werden von EF Core gelöscht
| ClientNoAction    | `DbUpdateException`                      | `InvalidOperationException`

Hinweise:

- Der Standardwert für erforderliche Beziehungen wie diese lautet: `Cascade`.
- Die Verwendung anderer Optionen als das kaskadierende Delete für erforderliche Beziehungen führt zu einer Ausnahme, wenn SaveChanges aufgerufen wird.
  - Hierbei handelt es sich in der Regel um eine `InvalidOperationException`-Ausnahme von EF Core, da der ungültige Zustand in den geladenen untergeordneten/abhängigen Entitäten erkannt wird.
  - `ClientNoAction` zwingt EF Core dazu, abhängige Behebungen zu überprüfen, bevor sie an die Datenbank übermittelt werden. Daher löst die Datenbank in diesem Fall eine Ausnahme aus, die dann von SaveChanges mit einer `DbUpdateException`-Ausnahme umschlossen wird.
  - `SetNull` wird beim Erstellen der Datenbank nicht zugelassen, da die Fremdschlüsselspalte keine NULL-Werte zulässt.
- Da abhängige/untergeordnete Entitäten geladen werden, werden sie immer von EF Core gelöscht. Sie werden nie von der Datenbank gelöscht.

#### <a name="required-relationship-with-dependentschildren-not-loaded"></a>Erforderliche Beziehung mit nicht geladenen abhängigen/untergeordneten Entitäten

| DeleteBehavior    | Bei Löschung des Prinzipals bzw. der übergeordneten Entität             | Bei Trennung der Beziehung zum Prinzipal bzw. zur übergeordneten Entität
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | Abhängige Entitäten werden von der Datenbank gelöscht           | –
| Einschränken          | `DbUpdateException`                      | –
| NoAction          | `DbUpdateException`                      | –
| SetNull           | `SqlException` bei Erstellung der Datenbank      | –
| ClientSetNull     | `DbUpdateException`                      | –
| ClientCascade     | `DbUpdateException`                      | –
| ClientNoAction    | `DbUpdateException`                      | –

Hinweise:

- Das Trennen einer Beziehung ist hier nicht zulässig, da die abhängigen/untergeordneten Entitäten nicht geladen werden.
- Der Standardwert für erforderliche Beziehungen wie diese lautet: `Cascade`.
- Die Verwendung anderer Optionen als das kaskadierende Delete für erforderliche Beziehungen führt zu einer Ausnahme, wenn SaveChanges aufgerufen wird.
  - In der Regel handelt es sich dabei um eine `DbUpdateException`-Ausnahme, da die abhängigen/untergeordneten Entitäten nicht geladen werden. Aus diesem Grund kann der ungültige Zustand nur von der Datenbank erkannt werden. Anschließend umschließt SaveChanges die Datenbankausnahme in `DbUpdateException`.
  - `SetNull` wird beim Erstellen der Datenbank nicht zugelassen, da die Fremdschlüsselspalte keine NULL-Werte zulässt.

#### <a name="optional-relationship-with-dependentschildren-loaded"></a>Optionale Beziehung mit geladenen abhängigen/untergeordneten Entitäten

| DeleteBehavior    | Bei Löschung des Prinzipals bzw. der übergeordneten Entität             | Bei Trennung der Beziehung zum Prinzipal bzw. zur übergeordneten Entität
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | Abhängige Entitäten werden von EF Core gelöscht            | Abhängige Entitäten werden von EF Core gelöscht
| Einschränken          | Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt     | Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt
| NoAction          | Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt     | Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt
| SetNull           | Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt     | Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt
| ClientSetNull     | Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt     | Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt
| ClientCascade     | Abhängige Entitäten werden von EF Core gelöscht            | Abhängige Entitäten werden von EF Core gelöscht
| ClientNoAction    | `DbUpdateException`                      | Abhängige Fremdschlüssel werden von EF Core auf NULL festgelegt

Hinweise:

- Der Standardwert für optionale Beziehungen wie diese lautet: `ClientSetNull`.
- Abhängige/untergeordnete Entitäten werden nie gelöscht, es sei denn, `Cascade` oder `ClientCascade` ist konfiguriert.
- Alle anderen Werte führen dazu, dass die abhängigen Fremdschlüssel von EF Core auf NULL festgelegt werden.
  - Eine Ausnahme hierfür ist der Wert `ClientNoAction`, der EF Core anweist, keine Änderungen an den Fremdschlüsseln von abhängigen/untergeordneten Entitäten vorzunehmen, wenn das Prinzipal bzw. die übergeordnete Entität gelöscht wird. Die Datenbank löst daher eine Ausnahme aus, die von SaveChanges in `DbUpdateException` umschlossen wird.

#### <a name="optional-relationship-with-dependentschildren-not-loaded"></a>Optionale Beziehung mit nicht geladenen abhängigen/untergeordneten Entitäten

| DeleteBehavior    | Bei Löschung des Prinzipals bzw. der übergeordneten Entität             | Bei Trennung der Beziehung zum Prinzipal bzw. zur übergeordneten Entität
|:------------------|------------------------------------------|----------------------------------------
| Cascade           | Abhängige Entitäten werden von der Datenbank gelöscht           | –
| Einschränken          | `DbUpdateException`                      | –
| NoAction          | `DbUpdateException`                      | –
| SetNull           | Abhängige Fremdschlüssel werden von der Datenbank auf NULL festgelegt    | –
| ClientSetNull     | `DbUpdateException`                      | –
| ClientCascade     | `DbUpdateException`                      | –
| ClientNoAction    | `DbUpdateException`                      | –

Hinweise:

- Das Trennen einer Beziehung ist hier nicht zulässig, da die abhängigen/untergeordneten Entitäten nicht geladen werden.
- Der Standardwert für optionale Beziehungen wie diese lautet: `ClientSetNull`.
- Abhängige/untergeordnete Entitäten müssen geladen werden, um eine Datenbankausnahme zu vermeiden, es sei denn, die Datenbank wurde für kaskadierende Deletes oder NULL-Werte konfiguriert.
