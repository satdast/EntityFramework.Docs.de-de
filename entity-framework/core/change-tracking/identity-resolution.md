---
title: Identitäts Auflösung-EF Core
description: Auflösen mehrerer Entitäts Instanzen in eine einzelne Instanz mithilfe von Primärschlüssel Werten
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/identity-resolution
ms.openlocfilehash: f94b61371dcead27853799719dabc7849500d466
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129604"
---
# <a name="identity-resolution-in-ef-core"></a>Identitäts Auflösung in EF Core

Ein <xref:Microsoft.EntityFrameworkCore.DbContext> kann nur eine Entitäts Instanz mit einem beliebigen Primärschlüssel Wert verfolgen. Dies bedeutet, dass mehrere Instanzen einer Entität mit demselben Schlüsselwert in eine einzelne Instanz aufgelöst werden müssen. Dies wird als "Identitäts Auflösung" bezeichnet. Die Identitäts Auflösung stellt sicher, dass Entity Framework Core (EF Core) ein konsistentes Diagramm ohne Mehrdeutigkeiten zu den Beziehungen oder Eigenschafts Werten der Entitäten nachverfolgt.

> [!TIP]
> In diesem Dokument wird davon ausgegangen, dass die Entitäts Zustände und die Grundlagen EF Core Änderungs Nachverfolgung verstanden werden. Weitere Informationen zu diesen Themen finden Sie [unter Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) .

> [!TIP]
> Sie können den gesamten Code in diesem Dokument ausführen und Debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/IdentityResolutionInEFCore).

## <a name="introduction"></a>Einführung

Der folgende Code fragt eine Entität ab und versucht dann, eine andere Instanz mit demselben Primärschlüssel Wert anzufügen:

<!--
            using var context = new BlogsContext();

            var blogA = context.Blogs.Single(e => e.Id == 1);
            var blogB = new Blog { Id = 1, Name = ".NET Blog (All new!)" };

            try
            {
                context.Update(blogB); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Identity_Resolution_in_EF_Core_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Identity_Resolution_in_EF_Core_1)]

Das Ausführen dieses Codes führt zu folgender Ausnahme:

> System. InvalidOperationException: die Instanz des Entitäts Typs "Blog" kann nicht nachverfolgt werden, da bereits eine andere Instanz mit dem Schlüsselwert "{ID: 1}" nachverfolgt wird. Wenn Sie vorhandene Entitäten anfügen, stellen Sie sicher, dass nur eine Entitäts Instanz mit einem angegebenen Schlüsselwert angefügt ist.

EF Core eine einzelne Instanz benötigt, weil Folgendes erforderlich ist:

- Eigenschaftswerte können sich zwischen mehreren Instanzen unterscheiden. Wenn Sie die Datenbank aktualisieren, muss EF Core wissen, welche Eigenschaftswerte verwendet werden sollen.
- Beziehungen mit anderen Entitäten können sich zwischen mehreren Instanzen unterscheiden. Beispielsweise kann "bloga" mit einer anderen Sammlung von Beiträgen als "blogb" verknüpft werden.

Die obige Ausnahme wird häufig in folgenden Situationen auftreten:

- Beim Versuch, eine Entität zu aktualisieren
- Beim Versuch, ein serialisiertes Diagramm von Entitäten zu verfolgen
- Wenn kein Schlüsselwert festgelegt wird, der nicht automatisch generiert wird
- Bei Wiederverwendung einer dbcontext-Instanz für mehrere Arbeitseinheiten

Jede dieser Situationen wird in den folgenden Abschnitten erläutert.

## <a name="updating-an-entity"></a>Aktualisieren einer Entität

Es gibt verschiedene Methoden, um eine Entität mit neuen Werten zu aktualisieren, wie in [Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) und [expliziten Nachverfolgen von Entitäten](xref:core/change-tracking/explicit-tracking)beschrieben. Diese Vorgehensweisen sind unten im Kontext der Identitäts Auflösung aufgeführt. Ein wichtiger Punkt ist, dass jeder der Ansätze entweder eine Abfrage oder einen-aufrufswert von `Update` oder `Attach` , aber **_nie beides_** verwendet.

### <a name="call-update"></a>Update aktualisieren

Häufig wird die zu Aktualisier Ende Entität nicht aus einer Abfrage für den dbcontext stammen, die wir für SaveChanges verwenden werden. Beispielsweise kann in einer Webanwendung eine Entitäts Instanz aus den Informationen in einer POST-Anforderung erstellt werden. Die einfachste Möglichkeit, dies zu behandeln, ist die Verwendung von <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> oder <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType> . Beispiel:

<!--
    public static void UpdateFromHttpPost1(Blog blog)
    {
        using var context = new BlogsContext();

        context.Update(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_1)]

In diesem Fall:

- Es wird nur eine einzelne Instanz der Entität erstellt.
- Die Entitäts Instanz wird **nicht** im Rahmen der Aktualisierung von der Datenbank abgefragt.
- Alle Eigenschaftswerte werden in der Datenbank aktualisiert, unabhängig davon, ob Sie tatsächlich geändert wurden oder nicht.
- Ein Daten Bank Roundtrip wird durchgeführt.

### <a name="query-then-apply-changes"></a>Abfrage dann Anwenden von Änderungen

In der Regel ist nicht bekannt, welche Eigenschaftswerte tatsächlich geändert wurden, wenn eine Entität aus Informationen in einer POST-Anforderung oder ähnlich erstellt wurde. Häufig ist es in Ordnung, alle Werte in der Datenbank zu aktualisieren, wie im vorherigen Beispiel gezeigt. Wenn die Anwendung jedoch viele Entitäten verarbeitet und nur eine kleine Anzahl von tatsächlichen Änderungen vorhanden ist, kann es hilfreich sein, die gesendeten Updates einzuschränken. Dies kann erreicht werden, indem eine Abfrage ausgeführt wird, mit der die Entitäten nachverfolgt werden, die derzeit in der Datenbank vorhanden sind, und dann Änderungen an diesen überwachten Entitäten angewendet werden Beispiel:

<!--
    public static void UpdateFromHttpPost2(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        trackedBlog.Name = blog.Name;
        trackedBlog.Summary = blog.Summary;

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_2)]

In diesem Fall:

- Es wird nur eine einzelne Instanz der Entität nachverfolgt. der, der von der Abfrage von der Datenbank zurückgegeben wird `Find` .
- `Update`, `Attach` usw. werden **nicht** verwendet.
- Nur Eigenschaftswerte, die tatsächlich geändert wurden, werden in der Datenbank aktualisiert.
- Es werden zwei Roundtrips für die Datenbank durchgeführt.

EF Core verfügt über einige Hilfsprogramme zum Übertragen von Eigenschafts Werten wie this. Beispielsweise <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> kopiert alle Werte aus dem angegebenen Objekt und legt Sie für das nach verfolgte Objekt fest:

<!--
    public static void UpdateFromHttpPost3(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_3)]

`SetValues` akzeptiert verschiedene Objekttypen, einschließlich DTOs (Data Transfer Objects) mit Eigenschaftsnamen, die den Eigenschaften des Entitäts Typs entsprechen. Beispiel:

<!--
    public static void UpdateFromHttpPost4(BlogDto dto)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(dto.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(dto);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_4)]

Oder ein Wörterbuch mit Name-Wert-Einträgen für die Eigenschaftswerte:

<!--
    public static void UpdateFromHttpPost5(Dictionary<string, object> propertyValues)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(propertyValues["Id"]);

        context.Entry(trackedBlog).CurrentValues.SetValues(propertyValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_5)]

Weitere Informationen zum Arbeiten mit Eigenschafts Werten wie diesem finden Sie unter Zugreifen auf nach [verfolgte Entitäten](xref:core/change-tracking/entity-entries) .

### <a name="use-original-values"></a>Ursprüngliche Werte verwenden

Bisher hat jeder Ansatz entweder eine Abfrage vor dem Update ausgeführt oder alle Eigenschaftswerte aktualisiert, unabhängig davon, ob Sie geändert wurden. Um nur Werte zu aktualisieren, die ohne Abfragen als Teil des Updates geändert wurden, müssen bestimmte Informationen darüber, welche Eigenschaftswerte geändert wurden, geändert werden. Eine gängige Methode, um diese Informationen zu erhalten, besteht darin, sowohl die aktuellen als auch die ursprünglichen Werte im HTTP-Post oder ähnlich zu senden. Beispiel:

<!--
    public static void UpdateFromHttpPost6(Blog blog, Dictionary<string, object> originalValues)
    {
        using var context = new BlogsContext();

        context.Attach(blog);
        context.Entry(blog).OriginalValues.SetValues(originalValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_6](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_6)]

In diesem Code wird die Entität mit geänderten Werten zum ersten Mal angefügt. Dies bewirkt EF Core, dass die Entität im Zustand nachverfolgt wird, d `Unchanged` . h., wenn keine Eigenschaftswerte als geändert markiert sind. Das Wörterbuch der ursprünglichen Werte wird dann auf diese nach verfolgte Entität angewendet. Dies wird als geänderte Eigenschaften mit anderen aktuellen und ursprünglichen Werten markiert. Eigenschaften, die die gleichen aktuellen und ursprünglichen Werte aufweisen, werden nicht als geändert markiert.

In diesem Fall:

- Nur eine einzelne Instanz der Entität wird mithilfe von Attach nachverfolgt.
- Die Entitäts Instanz wird **nicht** im Rahmen der Aktualisierung von der Datenbank abgefragt.
- Durch das Anwenden der ursprünglichen Werte wird sichergestellt, dass nur Eigenschaftswerte, die tatsächlich geändert wurden, in der Datenbank aktualisiert werden.
- Ein Daten Bank Roundtrip wird durchgeführt.

Wie bei den Beispielen im vorherigen Abschnitt müssen die ursprünglichen Werte nicht als Wörterbuch weitergegeben werden. eine Entitäts Instanz oder DTO funktioniert ebenfalls.

> [!TIP]
> Obwohl dieser Ansatz ansprechende Merkmale hat, ist es erforderlich, die ursprünglichen Werte der Entität an den und vom WebClient zu senden. Berücksichtigen Sie sorgfältig, ob diese zusätzliche Komplexität die Vorteile bietet. bei vielen Anwendungen ist ein einfacherer Ansatz pragmatischer.

## <a name="attaching-a-serialized-graph"></a>Anfügen eines serialisierten Diagramms

EF Core funktioniert mit Diagrammen von Entitäten, die über Fremdschlüssel und Navigations Eigenschaften verbunden sind, wie unter [Ändern von Fremdschlüsseln und Navigationen](xref:core/change-tracking/relationship-changes)beschrieben. Wenn diese Diagramme außerhalb der EF Core mithilfe von erstellt werden, z. b. aus einer JSON-Datei, können Sie mehrere Instanzen derselben Entität haben. Diese Duplikate müssen in einzelne Instanzen aufgelöst werden, bevor das Diagramm nachverfolgt werden kann.

### <a name="graphs-with-no-duplicates"></a>Diagramme ohne Duplikate

Bevor Sie fortfahren, ist es wichtig, Folgendes zu erkennen:

- Serialisierungssoren verfügen häufig über Optionen zum Verarbeiten von Schleifen und doppelten Instanzen im Diagramm.
- Die Auswahl des Objekts, das als Graph-Stamm verwendet wird, kann häufig zum reduzieren oder Entfernen von Duplikaten beitragen.

Verwenden Sie nach Möglichkeit Serialisierungsoptionen, und wählen Sie Stämme aus, die keine Duplikate ergeben. Im folgenden Code wird z. b. [JSON.net](https://www.nuget.org/packages/Newtonsoft.Json/) verwendet, um eine Liste von Blogs mit den zugehörigen Beiträgen zu serialisieren:

<!--
            using var context = new BlogsContext();

            var blogs = context.Blogs.Include(e => e.Posts).ToList();

            var serialized = JsonConvert.SerializeObject(
                blogs,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_1a](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1a)]

Der aus diesem Code generierte JSON-Code lautet wie folgt:

```json
[
  {
    "Id": 1,
    "Name": ".NET Blog",
    "Summary": "Posts about .NET",
    "Posts": [
      {
        "Id": 1,
        "Title": "Announcing the Release of EF Core 5.0",
        "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
        "BlogId": 1
      },
      {
        "Id": 2,
        "Title": "Announcing F# 5",
        "Content": "F# 5 is the latest version of F#, the functional programming language...",
        "BlogId": 1
      }
    ]
  },
  {
    "Id": 2,
    "Name": "Visual Studio Blog",
    "Summary": "Posts about Visual Studio",
    "Posts": [
      {
        "Id": 3,
        "Title": "Disassembly improvements for optimized managed debugging",
        "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
        "BlogId": 2
      },
      {
        "Id": 4,
        "Title": "Database Profiling with Visual Studio",
        "Content": "Examine when database queries were executed and measure how long the take using...",
        "BlogId": 2
      }
    ]
  }
]
```

Beachten Sie, dass keine doppelten Blogs oder Beiträge in der JSON vorhanden sind. Dies bedeutet, dass einfache Aufrufe von `Update` funktionieren, um diese Entitäten in der Datenbank zu aktualisieren:

<!--
        public static void UpdateBlogsFromJson(string json)
        {
            using var context = new BlogsContext();

            var blogs = JsonConvert.DeserializeObject<List<Blog>>(json);

            foreach (var blog in blogs)
            {
                context.Update(blog);
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_1b](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1b)]

### <a name="handling-duplicates"></a>Verarbeiten von Duplikaten

Der Code im vorherigen Beispiel hat jeden Blog mit den zugehörigen Beiträgen serialisiert. Wenn dies geändert wird, um jeden Beitrag mit dem zugehörigen Blog zu serialisieren, werden Duplikate in den serialisierten JSON-Code eingefügt. Beispiel:

<!--
            using var context = new BlogsContext();

            var posts = context.Posts.Include(e => e.Blog).ToList();

            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_2)]

Der serialisierte JSON-Code sieht nun wie folgt aus:

```json
[
  {
    "Id": 1,
    "Title": "Announcing the Release of EF Core 5.0",
    "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 2,
          "Title": "Announcing F# 5",
          "Content": "F# 5 is the latest version of F#, the functional programming language...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 2,
    "Title": "Announcing F# 5",
    "Content": "F# 5 is the latest version of F#, the functional programming language...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 1,
          "Title": "Announcing the Release of EF Core 5.0",
          "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 3,
    "Title": "Disassembly improvements for optimized managed debugging",
    "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 4,
          "Title": "Database Profiling with Visual Studio",
          "Content": "Examine when database queries were executed and measure how long the take using...",
          "BlogId": 2
        }
      ]
    }
  },
  {
    "Id": 4,
    "Title": "Database Profiling with Visual Studio",
    "Content": "Examine when database queries were executed and measure how long the take using...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 3,
          "Title": "Disassembly improvements for optimized managed debugging",
          "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
          "BlogId": 2
        }
      ]
    }
  }
]
```

Beachten Sie, dass das Diagramm nun mehrere Blog Instanzen mit demselben Schlüsselwert sowie mehrere Post-Instanzen mit demselben Schlüsselwert enthält. Wenn Sie versuchen, dieses Diagramm wie im vorherigen Beispiel zu verfolgen, wird Folgendes ausgelöst:

> System. InvalidOperationException: die Instanz des Entitäts Typs "Post" kann nicht nachverfolgt werden, da bereits eine andere Instanz mit dem Schlüsselwert "{ID: 2}" nachverfolgt wird. Wenn Sie vorhandene Entitäten anfügen, stellen Sie sicher, dass nur eine Entitäts Instanz mit einem angegebenen Schlüsselwert angefügt ist.

Dies kann auf zwei Arten behoben werden:

- Verwenden von JSON-Serialisierungsoptionen, die Verweise beibehalten
- Durchführen der Identitäts Auflösung während der Nachverfolgung des Diagramms

#### <a name="preserve-references"></a>Beibehalten von Verweisen

JSON.net bietet die `PreserveReferencesHandling` Option zum Behandeln dieses. Beispiel:

<!--
            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    PreserveReferencesHandling = PreserveReferencesHandling.All,
                    Formatting = Formatting.Indented
                });
-->
[!code-csharp[Attaching_a_serialized_graph_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_3)]

Der resultierende JSON-Code sieht nun wie folgt aus:

```json
{
  "$id": "1",
  "$values": [
    {
      "$id": "2",
      "Id": 1,
      "Title": "Announcing the Release of EF Core 5.0",
      "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
      "BlogId": 1,
      "Blog": {
        "$id": "3",
        "Id": 1,
        "Name": ".NET Blog",
        "Summary": "Posts about .NET",
        "Posts": [
          {
            "$ref": "2"
          },
          {
            "$id": "4",
            "Id": 2,
            "Title": "Announcing F# 5",
            "Content": "F# 5 is the latest version of F#, the functional programming language...",
            "BlogId": 1,
            "Blog": {
              "$ref": "3"
            }
          }
        ]
      }
    },
    {
      "$ref": "4"
    },
    {
      "$id": "5",
      "Id": 3,
      "Title": "Disassembly improvements for optimized managed debugging",
      "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
      "BlogId": 2,
      "Blog": {
        "$id": "6",
        "Id": 2,
        "Name": "Visual Studio Blog",
        "Summary": "Posts about Visual Studio",
        "Posts": [
          {
            "$ref": "5"
          },
          {
            "$id": "7",
            "Id": 4,
            "Title": "Database Profiling with Visual Studio",
            "Content": "Examine when database queries were executed and measure how long the take using...",
            "BlogId": 2,
            "Blog": {
              "$ref": "6"
            }
          }
        ]
      }
    },
    {
      "$ref": "7"
    }
  ]
}
```

Beachten Sie, dass dieser JSON-Code Duplikate durch Verweise wie ersetzt hat `"$ref": "5"` , die auf die bereits vorhandene Instanz im Diagramm verweisen. Dieses Diagramm kann wieder mit den einfachen Aufrufen von nachverfolgt werden `Update` , wie oben gezeigt.

Die <xref:System.Text.Json> Unterstützung in den .net-Basisklassen Bibliotheken (BCL) verfügt über eine ähnliche Option, die das gleiche Ergebnis liefert. Beispiel:

<!--
            var serialized = System.Text.Json.JsonSerializer.Serialize(posts, new System.Text.Json.JsonSerializerOptions
            {
                ReferenceHandler = ReferenceHandler.Preserve,
                WriteIndented = true
            });
-->
[!code-csharp[Attaching_a_serialized_graph_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_4)]

#### <a name="resolve-duplicates"></a>Duplikate auflösen

Wenn es nicht möglich ist, Duplikate im Serialisierungsprozess auszuschließen, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> bietet eine Möglichkeit, diese zu verarbeiten. Trackgraph funktioniert wie `Add` , `Attach` und `Update` mit der Ausnahme, dass ein Rückruf für jede Entitäts Instanz generiert wird, bevor Sie nachverfolgt wird. Dieser Rückruf kann verwendet werden, um die Entität zu verfolgen oder zu ignorieren. Beispiel:

<!--
        public static void UpdatePostsFromJsonWithIdentityResolution(string json)
        {
            using var context = new BlogsContext();

            var posts = JsonConvert.DeserializeObject<List<Post>>(json);

            foreach (var post in posts)
            {
                context.ChangeTracker.TrackGraph(
                    post, node =>
                        {
                            var keyValue = node.Entry.Property("Id").CurrentValue;
                            var entityType = node.Entry.Metadata;

                            var existingEntity = node.Entry.Context.ChangeTracker.Entries()
                                .FirstOrDefault(
                                    e => Equals(e.Metadata, entityType)
                                         && Equals(e.Property("Id").CurrentValue, keyValue));

                            if (existingEntity == null)
                            {
                                Console.WriteLine($"Tracking {entityType} entity with key value {keyValue}");

                                node.Entry.State = EntityState.Modified;
                            }
                            else
                            {
                                Console.WriteLine($"Discarding duplicate {entityType} entity with key value {keyValue}");
                            }
                        });
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_5)]

Für jede Entität im Diagramm führt dieser Code Folgendes aus:

- Suchen des Entitäts Typs und des Schlüssel Werts der Entität
- Suchen der Entität mit diesem Schlüssel in der Änderungs Nachverfolgung
  - Wenn die Entität gefunden wird, wird keine weitere Aktion durchgeführt, da die Entität ein Duplikat ist.
  - Wenn die Entität nicht gefunden wird, wird Sie nachverfolgt, indem der Status auf festgelegt wird. `Modified`

Die Ausgabe aus dem Ausführen dieses Codes lautet wie folgt:

```output
Tracking EntityType: Post entity with key value 1
Tracking EntityType: Blog entity with key value 1
Tracking EntityType: Post entity with key value 2
Discarding duplicate EntityType: Post entity with key value 2
Tracking EntityType: Post entity with key value 3
Tracking EntityType: Blog entity with key value 2
Tracking EntityType: Post entity with key value 4
Discarding duplicate EntityType: Post entity with key value 4
```

> [!IMPORTANT]
> Bei diesem Code **wird davon ausgegangen, dass alle Duplikate identisch sind**. Dadurch ist es sicher, dass Sie beliebig eines der Duplikate auswählen, das beim Verwerfen der anderen nachverfolgt werden soll. Wenn sich die Duplikate unterscheiden können, muss der Code entscheiden, wie bestimmt werden soll, welcher Wert verwendet werden soll, und wie Eigenschafts-und Navigations Werte zusammen kombiniert werden.

> [!NOTE]
> Der Einfachheit halber geht dieser Code davon aus, dass jede Entität über eine Primärschlüssel Eigenschaft namens verfügt `Id` . Dies könnte in eine abstrakte Basisklasse oder Schnittstelle cogeschrieben werden. Alternativ können die Primärschlüssel Eigenschaft oder die Eigenschaften aus den Metadaten abgerufen werden <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> , sodass dieser Code mit jedem beliebigen Entitätstyp funktionieren würde.

## <a name="failing-to-set-key-values"></a>Fehler beim Festlegen von Schlüsselwerten.

Entitäts Typen sind häufig so konfiguriert, dass [automatisch generierte Schlüsselwerte](xref:core/modeling/generated-properties)verwendet werden. Dies ist die Standardeinstellung für Integer-und GUID-Eigenschaften von nicht zusammengesetzten Schlüsseln. Wenn der Entitätstyp jedoch nicht für die Verwendung automatisch generierter Schlüsselwerte konfiguriert ist, muss ein expliziter Schlüsselwert festgelegt werden, bevor die Entität nachverfolgt wird. Verwenden Sie beispielsweise den folgenden Entitätstyp:

<!--
    public class Pet
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }
    }
-->
[!code-csharp[Pet](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Pet)]

Angenommen, Sie versuchen, zwei neue Entitäts Instanzen zu überprüfen, ohne Schlüsselwerte festzulegen:

<!--
            using var context = new BlogsContext();

            context.Add(new Pet { Name = "Smokey" });

            try
            {
                context.Add(new Pet { Name = "Clippy" }); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Failing_to_set_key_values_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Failing_to_set_key_values_1)]

Dieser Code löst Folgendes aus:

> System. InvalidOperationException: die Instanz des Entitäts Typs "Pet" kann nicht nachverfolgt werden, da bereits eine andere Instanz mit dem Schlüsselwert "{ID: 0}" nachverfolgt wird. Wenn Sie vorhandene Entitäten anfügen, stellen Sie sicher, dass nur eine Entitäts Instanz mit einem angegebenen Schlüsselwert angefügt ist.

Die Korrektur hierfür besteht darin, entweder Schlüsselwerte explizit festzulegen oder die Schlüsseleigenschaft für die Verwendung generierter Schlüsselwerte zu konfigurieren. Weitere Informationen finden Sie unter [generierte Werte](xref:core/modeling/generated-properties) .

## <a name="overusing-a-single-dbcontext-instance"></a>Über Verwenden einer einzelnen dbcontext-Instanz

<xref:Microsoft.EntityFrameworkCore.DbContext> ist so konzipiert, dass es eine kurzlebige Arbeitseinheit darstellt, wie unter [dbcontext-Initialisierung und-Konfiguration](xref:core/dbcontext-configuration/index)beschrieben und in [Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index)ausführlich erläutert. Wenn Sie dieser Anleitung nicht folgen, können Sie problemlos in Situationen eintreten, in denen versucht wird, mehrere Instanzen derselben Entität zu verfolgen. Typische Beispiele:

- Verwenden Sie dieselbe dbcontext-Instanz, um den Testzustand einzurichten, und führen Sie dann den Test aus. Dies führt häufig dazu, dass dbcontext nach wie vor eine Entitäts Instanz aus dem Test Setup nachverfolgt, während versucht wird, eine neue Instanz im Test ordnungsgemäß anzufügen. Verwenden Sie stattdessen eine andere dbcontext-Instanz zum Einrichten des Test Zustands und des richtigen Testcodes.
- Verwenden einer freigegebenen dbcontext-Instanz in einem Repository oder einem ähnlichen Code. Stellen Sie stattdessen sicher, dass Ihr Repository für jede Arbeitseinheit eine einzelne dbcontext-Instanz verwendet.

## <a name="identity-resolution-and-queries"></a>Identitäts Auflösung und-Abfragen

Die Identitäts Auflösung erfolgt automatisch, wenn Entitäten aus einer Abfrage nachverfolgt werden. Dies bedeutet Folgendes: Wenn eine Entitäts Instanz mit einem angegebenen Schlüsselwert bereits nachverfolgt wird, wird diese vorhandene überwachte Instanz anstelle der Erstellung einer neuen Instanz verwendet. Dies hat eine wichtige Folge: Wenn sich die Daten in der Datenbank geändert haben, wird dies in den Ergebnissen der Abfrage nicht wiedergegeben. Dies ist ein anguter Grund für die Verwendung einer neuen dbcontext-Instanz für jede Arbeitseinheit, wie unter [dbcontext-Initialisierung und-Konfiguration](xref:core/dbcontext-configuration/index)beschrieben und in [Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index)ausführlich erläutert.

> [!IMPORTANT]
> Es ist wichtig zu verstehen, dass EF Core immer eine LINQ-Abfrage für ein dbset für die Datenbank ausführt und nur Ergebnisse basierend auf der Datenbank zurückgibt. Bei einer nach Verfolgungs Abfrage werden jedoch, wenn die zurückgegebenen Entitäten bereits nachverfolgt werden, die nach verfolgten Instanzen verwendet, anstatt aus den Daten in der Datenbank eine-Instanz zu erstellen.

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> oder <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> kann verwendet werden, wenn nach verfolgte Entitäten mit den neuesten Daten aus der Datenbank aktualisiert werden müssen. Weitere Informationen finden Sie unter Zugreifen auf nach [verfolgte Entitäten](xref:core/change-tracking/entity-entries) .

Anders als bei der Nachverfolgung von Abfragen führen Abfragen ohne Nachverfolgung keine Identitäts Auflösung durch. Dies bedeutet, dass keine nach Verfolgungs Abfragen wie im zuvor beschriebenen JSON-serialisierungsfall Duplikate zurückgeben können. Dies ist in der Regel kein Problem, wenn die Abfrageergebnisse serialisiert und an den Client gesendet werden.

> [!TIP]
> Führen Sie nicht routinemäßig eine Abfrage ohne Nachverfolgung aus, und fügen Sie dann die zurückgegebenen Entitäten demselben Kontext hinzu. Dies ist langsamer und schwieriger zu erreichen als die Verwendung einer nach Verfolgungs Abfrage.

Keine nach Verfolgungs Abfragen führen keine Identitäts Auflösung durch, da dies Auswirkungen auf die Leistung des Streamings einer großen Anzahl von Entitäten aus einer Abfrage hat. Dies liegt daran, dass bei der Identitäts Auflösung jede Instanz zurückgegeben werden muss, damit Sie verwendet werden kann, anstatt später ein Duplikat zu erstellen.

Ab EF Core 5,0 können Abfragen für die Identitäts Auflösung mithilfe von nicht mehr nachverfolgt werden <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})> . Die Abfrage verfolgt dann zurückgegebene Instanzen nach, ohne Sie auf die normale Weise nachzuverfolgen. Außerdem wird sichergestellt, dass in den Abfrage Ergebnissen keine Duplikate erstellt werden.

## <a name="overriding-object-equality"></a>Überschreiben der Objekt Gleichheit

EF Core verwendet [Verweis Gleichheit](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) beim Vergleichen von Entitäts Instanzen. Dies ist auch dann der Fall, wenn die Entitäts Typen die <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> Objekt Gleichheit überschreiben oder anderweitig ändern. Es gibt jedoch eine Stelle, an der sich das Überschreiben von Gleichheit auf EF Core Verhalten auswirken kann: Wenn Sammlungs Navigation die überschriebene Gleichheit anstelle der Verweis Gleichheit verwendet und daher mehrere Instanzen als identisch gemeldet werden.

Aus diesem Grund empfiehlt es sich, das Überschreiben der Entitäts Gleichheit zu vermeiden. Wenn Sie verwendet wird, stellen Sie sicher, dass Sie Sammlungs Navigation erstellen, die Verweis Gleichheit erzwingen. Erstellen Sie z. b. einen Gleichheits Vergleich, der Verweis Gleichheit verwendet:

<!--
    public sealed class ReferenceEqualityComparer : IEqualityComparer<object>
    {
        private ReferenceEqualityComparer()
        {
        }

        public static ReferenceEqualityComparer Instance { get; } = new ReferenceEqualityComparer();

        bool IEqualityComparer<object>.Equals(object x, object y) => x == y;

        int IEqualityComparer<object>.GetHashCode(object obj) => RuntimeHelpers.GetHashCode(obj);
    }
-->
[!code-csharp[ReferenceEqualityComparer](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/ReferenceEqualityComparer.cs?name=ReferenceEqualityComparer)]

(Ab .net 5 ist diese in der BCL als enthalten <xref:System.Collections.Generic.ReferenceEqualityComparer> .)

Dieser Vergleich kann dann beim Erstellen von Auflistungs Navigationen verwendet werden. Beispiel:

<!--
        public ICollection<Order> Orders { get; set; }
            = new HashSet<Order>(ReferenceEqualityComparer.Instance);
-->
[!code-csharp[OrdersCollection](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=OrdersCollection)]

### <a name="comparing-key-properties"></a>Vergleichen von Schlüsseleigenschaften

Neben Gleichheits vergleichen müssen Schlüsselwerte auch geordnet werden. Dies ist wichtig, um Deadlocks zu vermeiden, wenn mehrere Entitäten in einem einzelnen "SaveChanges"-Befehl aktualisiert werden. Alle Typen, die für die Eigenschaften Primary, Alternate oder Foreign Key sowie für eindeutige Indizes verwendet werden, müssen <xref:System.IComparable%601> und implementieren <xref:System.IEquatable%601> . Typen, die normalerweise als Schlüssel (int, Guid, String usw.) verwendet werden, unterstützen diese Schnittstellen bereits. Benutzerdefinierte Schlüsseltypen können diese Schnittstellen hinzufügen.
