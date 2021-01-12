---
title: Zusätzliche Änderungsnachverfolgung Features-EF Core
description: Verschiedene Features und Szenarien im Zusammenhang mit EF Core Änderungs Nachverfolgung
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/miscellaneous
ms.openlocfilehash: db1e32948b2a60ad1b85e300bbbccd54d49a84e5
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129595"
---
# <a name="additional-change-tracking-features"></a>Zusätzliche Änderungsnachverfolgung Features

Dieses Dokument behandelt verschiedene Features und Szenarien im Zusammenhang mit der Änderungs Nachverfolgung.

> [!TIP]
> In diesem Dokument wird davon ausgegangen, dass die Entitäts Zustände und die Grundlagen EF Core Änderungs Nachverfolgung verstanden werden. Weitere Informationen zu diesen Themen finden Sie [unter Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) .

> [!TIP]
> Sie können den gesamten Code in diesem Dokument ausführen und Debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures).

## <a name="add-verses-addasync"></a>Verse hinzufügen AddAsync

Entity Framework Core (EF Core) stellt asynchrone Methoden bereit, wenn die Verwendung dieser Methode eine Daten Bank Interaktion verursachen kann. Synchrone Methoden werden ebenfalls bereitgestellt, um den mehr Aufwand bei der Verwendung von Datenbanken zu vermeiden, die keinen asynchronen Hochleistungs Zugriff unterstützen

<xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType> greifen in der Regel nicht auf die Datenbank zu, da diese Methoden grundsätzlich nur die Nachverfolgung von Entitäten starten. Einige Formen der Wert Generierung _können_ jedoch auf die Datenbank zugreifen, um einen Schlüsselwert zu generieren. Der einzige Wert Generator, der diese übernimmt und mit EF Core ausgeliefert wird, ist <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601> . Die Verwendung dieses Generators ist nicht üblich. Er wird nie standardmäßig konfiguriert. Dies bedeutet, dass die große Mehrheit der Anwendungen verwenden sollte `Add` und nicht `AddAsync` .

Andere ähnliche Methoden wie `Update` , `Attach` und `Remove` verfügen nicht über über Ladungen, da Sie nie neue Schlüsselwerte generieren und daher nie auf die Datenbank zugreifen müssen.

## <a name="addrange-updaterange-attachrange-and-removerange"></a>"AddRange", "updaterange", "attachrange" und "RemoveRange"

<xref:Microsoft.EntityFrameworkCore.DbSet%601> und <xref:Microsoft.EntityFrameworkCore.DbContext> stellen Alternative Versionen von `Add` , `Update` , `Attach` und bereit `Remove` , die mehrere Instanzen in einem einzelnen-Befehl akzeptieren. Diese Methoden werden `AddRange` , `UpdateRange` , `AttachRange` `RemoveRange` bzw. genannt.

Diese Methoden werden als praktische Hilfe bereitgestellt. Die Verwendung einer "Range"-Methode hat die gleiche Funktionalität wie mehrere Aufrufe der äquivalenten Non-Range-Methode. Es gibt keinen signifikanten Leistungsunterschied zwischen den beiden Ansätzen.

> [!NOTE]
> Dies unterscheidet sich von EF6, wobei "AddRange" sowohl automatisch als "DetectChanges" bezeichnet wird, der Aufruf von "Add" jedoch mehrmals bewirkt hat, dass "DetectChanges" mehrmals anstatt einmal aufgerufen wird. Dadurch wurde AddRange in EF6 effizienter. In EF Core ruft diese Methode nicht automatisch DetectChanges auf.

## <a name="dbcontext-verses-dbset-methods"></a>Dbcontext Verse dbset-Methoden

Viele Methoden, einschließlich `Add` , `Update` , `Attach` und `Remove` , verfügen über Implementierungen sowohl für <xref:Microsoft.EntityFrameworkCore.DbSet%601> als auch für <xref:Microsoft.EntityFrameworkCore.DbContext> . Diese Methoden weisen _genau das gleiche Verhalten_ für normale Entitäts Typen auf. Dies liegt daran, dass der CLR-Typ der Entität nur einem Entitätstyp im EF Core Modell zugeordnet ist. Der CLR-Typ definiert daher vollständig, wo die Entität in das Modell passt, sodass das zu verwendende dbset implizit bestimmt werden kann.

Eine Ausnahme von dieser Regel ist die Verwendung von Entitäts Typen vom Typ "freigegeben", die in EF Core 5,0 eingeführt wurden, hauptsächlich für m:n-joinentitäten. Wenn Sie einen Entitätstyp vom Typ "Shared" verwenden, muss zunächst ein dbset für den verwendeten EF Core Modelltyp erstellt werden. Methoden wie `Add` , `Update` , `Attach` und `Remove` können dann für das dbset ohne Mehrdeutigkeit verwendet werden, wenn EF Core Modelltyp verwendet wird.

Entitäts Typen mit gemeinsam genutzten Typen werden standardmäßig für die joinentitäten in m:n-Beziehungen verwendet. Ein Entitätstyp vom Typ "freigegeben" kann auch explizit für die Verwendung in einer m:n-Beziehung konfiguriert werden. Der folgende Code konfiguriert z. b. `Dictionary<string, int>` als joinentitätstyp:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .SharedTypeEntity<Dictionary<string, int>>(
                    "PostTag",
                    b =>
                        {
                            b.IndexerProperty<int>("TagId");
                            b.IndexerProperty<int>("PostId");
                        });

            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<Dictionary<string, int>>(
                    "PostTag",
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany());
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=OnModelCreating)]

[Ändern von Fremdschlüsseln und Navigationen](xref:core/change-tracking/relationship-changes) zeigt, wie zwei Entitäten durch Nachverfolgen einer neuen Join-Entitäts Instanz zugeordnet werden. Der folgende Code führt dies für den `Dictionary<string, int>` Entitätstyp "Shared" aus, der für die joinentität verwendet wird:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            var joinEntitySet = context.Set<Dictionary<string, int>>("PostTag");
            var joinEntity = new Dictionary<string, int>
            {
                ["PostId"] = post.Id,
                ["TagId"] = tag.Id
            };
            joinEntitySet.Add(joinEntity);

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[DbContext_verses_DbSet_methods_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=DbContext_verses_DbSet_methods_1)]

Beachten Sie, dass <xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType> verwendet wird, um ein dbset für den `PostTag` Entitätstyp zu erstellen. Dieses dbset kann dann verwendet werden, um `Add` mit der neuen Join-Entitäts Instanz aufzurufen.

> [!IMPORTANT]
> Der CLR-Typ, der für joinentitäts Typen gemäß der Konvention verwendet wird, kann sich in zukünftigen Versionen ändern, um die Leistung Richten Sie sich nicht nach einem bestimmten joinentitätstyp, es sei denn, er wurde explizit so konfiguriert, wie dies für `Dictionary<string, int>` im obigen Code erfolgt ist.

## <a name="property-verses-field-access"></a>Eigenschaften-Verse-Feld Zugriff

Ab EF Core 3,0 verwendet der Zugriff auf Entitäts Eigenschaften standardmäßig das Unterstützungs Feld der-Eigenschaft. Dies ist effizient und vermeidet das Auslösen von Nebeneffekten aus dem Aufruf von Eigenschaften-Getter und-Setter. So kann z. b. das verzögerte Laden vermeiden, dass unendliche Schleifen ausgelöst werden. Weitere Informationen zum Konfigurieren von Unterstützungs Feldern im Modell finden Sie unter dahinter liegende [Felder](xref:core/modeling/backing-field) .

Manchmal kann es wünschenswert sein, dass EF Core Nebeneffekte generiert, wenn Sie Eigenschaftswerte ändert. Wenn z. b. beim Binden von Daten an Entitäten eine Eigenschaft festgelegt wird, werden möglicherweise Benachrichtigungen an U.I. Dies geschieht nicht, wenn das Feld direkt festgelegt wird. Dies kann durch Ändern von für erreicht werden <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> :

- Alle Entitäts Typen im Modell mit <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- Alle Eigenschaften und Navigationen eines bestimmten Entitäts Typs mithilfe von <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- Eine bestimmte Eigenschaft mit <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- Eine bestimmte Navigation mit <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>

Eigenschafts Zugriffs Modi `Field` und `PreferField` bewirken, dass EF Core über das dahinter liegende Feld auf den Eigenschafts Wert zugreift. Ebenso `Property` bewirken und `PreferProperty` EF Core auf den Eigenschafts Wert über seinen Getter und Setter zugreifen.

Wenn `Field` oder `Property` verwendet werden und EF Core nicht auf den Wert über das Feld bzw. die Eigenschaften Getter/Setter zugreifen können, löst EF Core eine Ausnahme aus. Dadurch wird sichergestellt, dass EF Core immer den Feld-/Eigenschaftenzugriff verwendet, wenn Sie den Eindruck haben

Auf der anderen Seite greifen die `PreferField` Modi und auf `PreferProperty` die Verwendung der Eigenschaft bzw. des dahinter liegenden Felds zurück, wenn es nicht möglich ist, den bevorzugten Zugriff zu verwenden. `PreferField` ist der Standardwert von EF Core 3,0 ab. Dies bedeutet, dass EF Core immer dann Felder verwendet, wenn es nicht möglich ist, wenn auf eine Eigenschaft über den zugehörigen Getter oder Setter zugegriffen werden muss.

`FieldDuringConstruction` und `PreferFieldDuringConstruction` Konfigurieren EF Core _nur beim Erstellen von Entitäts Instanzen_ für die Verwendung von Unterstützungs Feldern. Auf diese Weise können Abfragen ohne Getter-und Setter-Nebeneffekte ausgeführt werden, während spätere Eigenschafts Änderungen durch EF Core diese Nebeneffekte verursachen. `PreferFieldDuringConstruction` war der Standardwert vor EF Core 3,0.

Die verschiedenen Eigenschaften Zugriffs Modi werden in der folgenden Tabelle zusammengefasst:

| Propertyaccessmode              | Bevorzu | Einrichtung von Entitäten bevorzugen | Fallback | Fallback beim Erstellen von Entitäten
|:--------------------------------|------------|------------------------------|----------|---------------------------
| `Field`                         | Feld      | Feld                        | Löst aus   | Löst aus
| `Property`                      | Eigenschaft   | Eigenschaft                     | Löst aus   | Löst aus
| `PreferField`                   | Feld      | Feld                        | Eigenschaft | Eigenschaft
| `PreferProperty`                | Eigenschaft   | Eigenschaft                     | Feld    | Feld
| `FieldDuringConstruction`       | Eigenschaft   | Feld                        | Feld    | Löst aus
| `PreferFieldDuringConstruction` | Eigenschaft   | Feld                        | Feld    | Eigenschaft

## <a name="temporary-values"></a>Temporäre Werte

EF Core erstellt temporäre Schlüsselwerte, wenn neue Entitäten nachverfolgt werden, deren echte Schlüsselwerte von der Datenbank generiert werden, wenn SaveChanges aufgerufen wird. Eine Übersicht über die Verwendung dieser temporären Werte finden Sie [unter Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) .

### <a name="accessing-temporary-values"></a>Zugreifen auf temporäre Werte

Ab EF Core 3,0 werden temporäre Werte in der Änderungs Protokollierung gespeichert und nicht direkt auf Entitäts Instanzen festgelegt. Diese temporären Werte werden jedoch verfügbar gemacht, wenn die verschiedenen Mechanismen für den [Zugriff auf verfolgte Entitäten](xref:core/change-tracking/entity-entries)verwendet _werden_ . Beispielsweise greift der folgende Code mithilfe von auf einen temporären Wert zu <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType> :

<!--
        using var context = new BlogsContext();

        var blog = new Blog { Name = ".NET Blog" };

        context.Add(blog);

        Console.WriteLine($"Blog.Id set on entity is {blog.Id}");
        Console.WriteLine($"Blog.Id tracked by EF is {context.Entry(blog).Property(e => e.Id).CurrentValue}");
-->
[!code-csharp[Temporary_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_1)]

Die Ausgabe dieses Codes lautet wie folgt:

```output
Blog.Id set on entity is 0
Blog.Id tracked by EF is -2147482643
```

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> kann verwendet werden, um nach temporären Werten zu suchen.

### <a name="manipulating-temporary-values"></a>Bearbeiten temporärer Werte

Es ist manchmal hilfreich, explizit mit temporären Werten zu arbeiten. Beispielsweise kann eine Auflistung von neuen Entitäten auf einem Webclient erstellt und dann zurück zum Server serialisiert werden. Fremdschlüssel Werte sind eine Möglichkeit zum Einrichten von Beziehungen zwischen diesen Entitäten. Der folgende Code verwendet diesen Ansatz, um ein Diagramm neuer Entitäten nach Fremdschlüssel zuzuordnen, während gleichzeitig beim Aufrufen von SaveChanges echte Schlüsselwerte generiert werden können.

<!--
            var blogs = new List<Blog>
            {
                new Blog { Id = -1, Name = ".NET Blog" },
                new Blog { Id = -2, Name = "Visual Studio Blog" }
            };

            var posts = new List<Post>()
            {
                new Post
                {
                    Id = -1,
                    BlogId = -1,
                    Title = "Announcing the Release of EF Core 5.0",
                    Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                },
                new Post
                {
                    Id = -2,
                    BlogId = -2,
                    Title = "Disassembly improvements for optimized managed debugging",
                    Content = "If you are focused on squeezing out the last bits of performance for your .NET service or..."
                }
            };

            using var context = new BlogsContext();

            foreach (var blog in blogs)
            {
                context.Add(blog).Property(e => e.Id).IsTemporary = true;
            }

            foreach (var post in posts)
            {
                context.Add(post).Property(e => e.Id).IsTemporary = true;
            }

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Temporary_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_2)]

Beachten Sie Folgendes:

- Negative Zahlen werden als temporäre Schlüsselwerte verwendet. Dies ist nicht erforderlich, ist jedoch eine gängige Konvention, um Schlüssel Konflikte zu verhindern.
- Der `Post.BlogId` Eigenschaft "FK" wird der gleiche negative Wert wie der PK des zugehörigen Blogs zugewiesen.
- Die PK-Werte werden als temporär gekennzeichnet, indem Sie festlegen, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> nachdem jede Entität nachverfolgt wird. Dies ist erforderlich, da davon ausgegangen wird, dass jeder von der Anwendung bereitgestellte Schlüsselwert ein echter Schlüsselwert ist.

Wenn Sie vor dem Aufrufen von SaveChanges die [Debugansicht](xref:core/change-tracking/debug-views) für die Änderungs Nachverfolgung anzeigen, sehen Sie, dass die PK-Werte als temporär gekennzeichnet sind und Beiträge mit den richtigen Blogs verknüpft sind

```output
Blog {Id: -2} Added
  Id: -2 PK Temporary
  Name: 'Visual Studio Blog'
  Posts: [{Id: -2}]
Blog {Id: -1} Added
  Id: -1 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -1}]
Post {Id: -2} Added
  Id: -2 PK Temporary
  BlogId: -2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: -2}
  Tags: []
Post {Id: -1} Added
  Id: -1 PK Temporary
  BlogId: -1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -1}
```

Nach <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> dem Aufruf von wurden diese temporären Werte durch von der Datenbank generierte reale Werte ersetzt:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Posts: [{Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
```

## <a name="working-with-default-values"></a>Arbeiten mit Standardwerten

EF Core ermöglicht es einer Eigenschaft, ihren Standardwert aus der Datenbank zu erhalten, wenn <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aufgerufen wird. Wie bei generierten Schlüsselwerten wird EF Core nur einen Standardwert aus der Datenbank verwenden, wenn kein Wert explizit festgelegt wurde. Sehen Sie sich beispielsweise den folgenden Entitätstyp an:

<!--
    public class Token
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime ValidFrom { get; set; }
    }
-->
[!code-csharp[Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Token)]

Die- `ValidFrom` Eigenschaft ist so konfiguriert, dass Sie einen Standardwert aus der Datenbank erhält:

<!--
        modelBuilder
            .Entity<Token>()
            .Property(e => e.ValidFrom)
            .HasDefaultValueSql("CURRENT_TIMESTAMP");
-->
[!code-csharp[OnModelCreating_Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Token)]

Wenn eine Entität dieses Typs eingefügt wird, ermöglicht EF Core der Datenbank, den Wert zu generieren, es sei denn, stattdessen wurde ein expliziter Wert festgelegt. Beispiel:

<!--
            using var context = new BlogsContext();

            context.AddRange(
                new Token { Name = "A" },
                new Token { Name = "B", ValidFrom = new DateTime(1111, 11, 11, 11, 11, 11)});

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Working_with_default_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_1)]

Wenn Sie die [Debugansicht der Änderungs](xref:core/change-tracking/debug-views) Nachverfolgung anzeigen, wird angezeigt, dass das erste Token `ValidFrom` von der Datenbank generiert wurde, während das zweite Token den explizit festgelegten Wert verwendet hat:

```output
Token {Id: 1} Unchanged
  Id: 1 PK
  Name: 'A'
  ValidFrom: '12/30/2020 6:36:06 PM'
Token {Id: 2} Unchanged
  Id: 2 PK
  Name: 'B'
  ValidFrom: '11/11/1111 11:11:11 AM'
```

> [!NOTE]
> Die Verwendung von Daten Bank Standardwerten erfordert, dass für die Daten Bank Spalte eine Standardwert Einschränkung konfiguriert ist. Dies erfolgt automatisch durch EF Core Migrationen, wenn <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> oder verwendet wird <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A> . Stellen Sie sicher, dass Sie die DEFAULT-Einschränkung für die Spalte auf andere Weise erstellen, wenn Sie keine EF Core Migrationen verwenden.

### <a name="using-nullable-properties"></a>Verwenden von Werte zulässt-Eigenschaften

EF Core kann ermitteln, ob eine Eigenschaft festgelegt wurde, indem Sie den Eigenschafts Wert mit dem CLR-Standardwert für den betreffenden Typ vergleicht. Dies funktioniert in den meisten Fällen gut, bedeutet aber, dass die CLR-Standardeinstellung nicht explizit in die Datenbank eingefügt werden kann. Stellen Sie sich beispielsweise eine Entität mit einer ganzzahligen Eigenschaft vor:

<!--
public class Foo1
{
    public int Id { get; set; }
    public int Count { get; set; }
}
-->
[!code-csharp[Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo1)]

Wenn diese Eigenschaft für den Daten Bank Standardwert-1 konfiguriert ist:

<!--
        modelBuilder
            .Entity<Foo1>()
            .Property(e => e.Count)
            .HasDefaultValue(-1);
-->
[!code-csharp[OnModelCreating_Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Foo1)]

Die Absicht ist, dass der Standardwert-1 verwendet wird, wenn kein expliziter Wert festgelegt ist. Das Festlegen des Werts auf 0 (die CLR-Standardeinstellung für ganze Zahlen) kann jedoch nicht unterschieden werden, EF Core kein Wert festgelegt wird. Dies bedeutet, dass es nicht möglich ist, 0 für diese Eigenschaft einzufügen. Beispiel:

<!--
        using var context = new BlogsContext();

        var fooA = new Foo1 { Count = 10 };
        var fooB = new Foo1 { Count = 0 };
        var fooC = new Foo1 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == -1); // Not what we want!
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_2)]

Beachten Sie, dass die-Instanz, bei der `Count` explizit auf 0 festgelegt wurde, immer noch den Standardwert aus der Datenbank erhält. Dies ist nicht beabsichtigt. Eine einfache Möglichkeit, dies zu umgehen, besteht darin, dass die `Count` Eigenschaft NULL-Werte zulässt:

<!--
public class Foo2
{
    public int Id { get; set; }
    public int? Count { get; set; }
}
-->
[!code-csharp[Foo2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo2)]

Dadurch wird der CLR-Standardwert NULL (anstelle von 0), d. h., wenn er explizit festgelegt wird, wird 0 eingefügt:

<!--
        using var context = new BlogsContext();

        var fooA = new Foo2 { Count = 10 };
        var fooB = new Foo2 { Count = 0 };
        var fooC = new Foo2 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == 0);
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_3)]

### <a name="using-nullable-backing-fields"></a>Verwenden von Werte zulässt-Unterstützungs Feldern

> [!NOTE]
> Dieses Unterstützungs Feld Muster, das NULL-Werte zulässt, wird von EF Core 5,0 und höher unterstützt.

Das Problem, dass die Eigenschaft auf NULL festgelegt werden kann, kann im Domänen Modell nicht konzeptionell NULL sein. Wenn Sie erzwingen, dass die Eigenschaft auf NULL festgelegt werden kann, wird das Modell gefährdet

Ab EF Core 5,0 kann die-Eigenschaft nicht auf NULL festgelegt werden, und nur das Unterstützungs Feld lässt NULL-Werte zu. Beispiel:

<!--
public class Foo3
{
    public int Id { get; set; }

    private int? _count;
    public int Count
    {
        get => _count ?? -1;
        set => _count = value;
    }
}
-->
[!code-csharp[Foo3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo3)]

Dadurch kann die CLR-Standardeinstellung (0) eingefügt werden, wenn die-Eigenschaft explizit auf 0 festgelegt ist, während die-Eigenschaft im Domänen Modell nicht als Werte zulässt verfügbar gemacht werden muss. Beispiel:

<!--
            using var context = new BlogsContext();

            var fooA = new Foo3 { Count = 10 };
            var fooB = new Foo3 { Count = 0 };
            var fooC = new Foo3 { };

            context.AddRange(fooA, fooB, fooC);
            context.SaveChanges();

            Debug.Assert(fooA.Count == 10);
            Debug.Assert(fooB.Count == 0);
            Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_4](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_4)]

#### <a name="nullable-backing-fields-for-bool-properties"></a>Auf NULL festleg Bare Unterstützungs Felder für boolesche Eigenschaften

Dieses Muster ist besonders nützlich bei der Verwendung von booleschen Eigenschaften mit vom Speicher generierten Standardwerten. Da der CLR-Standard für den Wert `bool` "false" hat, bedeutet dies, dass "false" nicht explizit mit dem normalen Muster eingefügt werden kann. Nehmen Sie beispielsweise einen `User` Entitätstyp an:

<!--
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }

    private bool? _isAuthorized;
    public bool IsAuthorized
    {
        get => _isAuthorized ?? true;
        set => _isAuthorized = value;
    }
}
-->
[!code-csharp[User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=User)]

Die- `IsAuthorized` Eigenschaft wird mit dem Standardwert "true" der Datenbank konfiguriert:

<!--
        modelBuilder
            .Entity<User>()
            .Property(e => e.IsAuthorized)
            .HasDefaultValue(true);
-->
[!code-csharp[OnModelCreating_User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_User)]

Die `IsAuthorized` Eigenschaft kann vor dem Einfügen explizit auf "true" oder "false" festgelegt werden, oder die Eigenschaft kann nicht festgelegt werden. in diesem Fall wird der Daten Bank Standard verwendet:

<!--
        using var context = new BlogsContext();

        var userA = new User { Name = "Mac" };
        var userB = new User { Name = "Alice", IsAuthorized = true };
        var userC = new User { Name = "Baxter", IsAuthorized = false }; // Always deny Baxter access!

        context.AddRange(userA, userB, userC);

        context.SaveChanges();
-->
[!code-csharp[Working_with_default_values_5](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_5)]

Die Ausgabe von SaveChanges bei der Verwendung von SQLite zeigt, dass der Daten Bank Standard für Mac verwendet wird, während für Alice und Baxter explizite Werte festgelegt sind:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='Mac' (Size = 3)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("Name")
VALUES (@p0);
SELECT "Id", "IsAuthorized"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='True' (DbType = String), @p1='Alice' (Size = 5)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='False' (DbType = String), @p1='Baxter' (Size = 6)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

### <a name="schema-defaults-only"></a>Nur Schema Standardwerte

In manchen Fällen ist es sinnvoll, die Standardeinstellungen des Datenbankschemas zu verwenden, das durch EF Core Migrationen erstellt wurde, EF Core ohne diese Werte für Einfügungen Dies kann erreicht werden, indem Sie die-Eigenschaft wie folgt konfigurieren <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> :

<!--
        modelBuilder
            .Entity<Bar>()
            .Property(e => e.Count)
            .HasDefaultValue(-1)
            .ValueGeneratedNever();
-->
[!code-csharp[OnModelCreating_Bar](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Bar)]
