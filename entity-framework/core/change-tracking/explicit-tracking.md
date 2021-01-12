---
title: Explizite Nachverfolgung von Entitäten-EF Core
description: Explizites Nachverfolgen von Entitäten mit dbcontext mithilfe von hinzufügen, anfügen, aktualisieren und entfernen
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/explicit-tracking
ms.openlocfilehash: 28a6ec3e3c25dad70882b8681f78744a5979efe6
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129597"
---
# <a name="explicitly-tracking-entities"></a><span data-ttu-id="3b05b-103">Entitäten explizit nachverfolgen</span><span class="sxs-lookup"><span data-stu-id="3b05b-103">Explicitly Tracking Entities</span></span>

<span data-ttu-id="3b05b-104">Jede <xref:Microsoft.EntityFrameworkCore.DbContext> Instanz verfolgt Änderungen, die an Entitäten vorgenommen wurden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="3b05b-105">Diese überwachten Entitäten Steuern wiederum die Änderungen an der Datenbank, wenn <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="3b05b-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="3b05b-106">Entity Framework Core (EF Core)-Änderungs Nachverfolgung funktioniert am besten, wenn die gleiche <xref:Microsoft.EntityFrameworkCore.DbContext> Instanz verwendet wird, um Entitäten abzufragen und durch Aufrufen von zu aktualisieren <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> .</span><span class="sxs-lookup"><span data-stu-id="3b05b-106">Entity Framework Core (EF Core) change tracking works best when the same <xref:Microsoft.EntityFrameworkCore.DbContext> instance is used to both query for entities and update them by calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="3b05b-107">Der Grund hierfür ist, dass EF Core den Status von abgefragten Entitäten automatisch nachverfolgt und dann alle Änderungen erkennt, die an diesen Entitäten vorgenommen werden, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="3b05b-107">This is because EF Core automatically tracks the state of queried entities and then detects any changes made to these entities when SaveChanges is called.</span></span> <span data-ttu-id="3b05b-108">Diese Vorgehensweise wird in [Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index)behandelt.</span><span class="sxs-lookup"><span data-stu-id="3b05b-108">This approach is covered in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span>

> [!TIP]
> <span data-ttu-id="3b05b-109">In diesem Dokument wird davon ausgegangen, dass die Entitäts Zustände und die Grundlagen EF Core Änderungs Nachverfolgung verstanden werden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-109">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="3b05b-110">Weitere Informationen zu diesen Themen finden Sie [unter Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index) .</span><span class="sxs-lookup"><span data-stu-id="3b05b-110">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="3b05b-111">Sie können den gesamten Code in diesem Dokument ausführen und Debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span><span class="sxs-lookup"><span data-stu-id="3b05b-111">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span></span>

> [!TIP]
> <span data-ttu-id="3b05b-112">Aus Gründen der Einfachheit verwendet dieses Dokument synchrone Methoden, wie z. b., und verweist auf synchrone Methoden wie <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> z.b..</span><span class="sxs-lookup"><span data-stu-id="3b05b-112">For simplicity, this document uses and references synchronous methods such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> rather their async equivalents such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>.</span></span> <span data-ttu-id="3b05b-113">Das aufrufen und warten auf die asynchrone Methode kann ersetzt werden, sofern nicht anders angegeben.</span><span class="sxs-lookup"><span data-stu-id="3b05b-113">Calling and awaiting the async method can be substituted unless otherwise noted.</span></span>

## <a name="introduction"></a><span data-ttu-id="3b05b-114">Einführung</span><span class="sxs-lookup"><span data-stu-id="3b05b-114">Introduction</span></span>

<span data-ttu-id="3b05b-115">Entitäten können explizit an einen angefügt werden <xref:Microsoft.EntityFrameworkCore.DbContext> , sodass der Kontext diese Entitäten nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="3b05b-115">Entities can be explicitly "attached" to a <xref:Microsoft.EntityFrameworkCore.DbContext> such that the context then tracks those entities.</span></span> <span data-ttu-id="3b05b-116">Dies ist in erster Linie nützlich, wenn:</span><span class="sxs-lookup"><span data-stu-id="3b05b-116">This is primarily useful when:</span></span>

1. <span data-ttu-id="3b05b-117">Erstellen neuer Entitäten, die in die Datenbank eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-117">Creating new entities that will be inserted into the database.</span></span>
2. <span data-ttu-id="3b05b-118">Erneutes Anfügen von getrennten Entitäten, die zuvor von einer _anderen_ dbcontext-Instanz abgefragt wurden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-118">Re-attaching disconnected entities that were previously queried by a _different_ DbContext instance.</span></span>

<span data-ttu-id="3b05b-119">Die ersten werden von den meisten Anwendungen benötigt und werden von den-Methoden als primär behandelt <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="3b05b-119">The first of these will be needed by most applications, and is primary handled by the <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> methods.</span></span>

<span data-ttu-id="3b05b-120">Die zweite wird nur von Anwendungen benötigt, die Entitäten oder deren Beziehungen ändern, **_während die Entitäten nicht nachverfolgt werden_**.</span><span class="sxs-lookup"><span data-stu-id="3b05b-120">The second is only needed by applications that change entities or their relationships **_while the entities are not being tracked_**.</span></span> <span data-ttu-id="3b05b-121">Beispielsweise kann eine Webanwendung Entitäten an den Webclient senden, an dem der Benutzer Änderungen vornimmt und die Entitäten zurücksendet.</span><span class="sxs-lookup"><span data-stu-id="3b05b-121">For example, a web application may send entities to the web client where the user makes changes and sends the entities back.</span></span> <span data-ttu-id="3b05b-122">Diese Entitäten werden als "getrennt" bezeichnet, da Sie ursprünglich von einem dbcontext abgefragt wurden, aber dann beim Senden an den Client von diesem Kontext getrennt wurden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-122">These entities are referred to as "disconnected" since they were originally queried from a DbContext, but were then disconnected from that context when sent to the client.</span></span>

<span data-ttu-id="3b05b-123">Die Webanwendung muss nun diese Entitäten erneut anfügen, damit Sie erneut verfolgt werden, und gibt die Änderungen an, die vorgenommen wurden, <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> um geeignete Updates an der Datenbank vornehmen zu können.</span><span class="sxs-lookup"><span data-stu-id="3b05b-123">The web application must now re-attach these entities so that they are again tracked and indicate the changes that have been made such that <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> can make appropriate updates to the database.</span></span> <span data-ttu-id="3b05b-124">Dies wird hauptsächlich von der <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> -Methode und der- <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> Methode behandelt.</span><span class="sxs-lookup"><span data-stu-id="3b05b-124">This is primarily handled by the <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> methods.</span></span>

> [!TIP]
> <span data-ttu-id="3b05b-125">Das Anfügen von Entitäten an _dieselbe dbcontext-Instanz_ , aus der Sie abgefragt wurden, sollte normalerweise nicht benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-125">Attaching entities to the _same DbContext instance_ that they were queried from should not normally be needed.</span></span> <span data-ttu-id="3b05b-126">Führen Sie nicht routinemäßig eine Abfrage ohne Nachverfolgung aus, und fügen Sie dann die zurückgegebenen Entitäten demselben Kontext hinzu.</span><span class="sxs-lookup"><span data-stu-id="3b05b-126">Do not routinely perform a no-tracking query and then attach the returned entities to the same context.</span></span> <span data-ttu-id="3b05b-127">Dies ist langsamer als die Verwendung einer nach Verfolgungs Abfrage und kann auch zu Problemen führen, wie z. b. fehlende Schatten Eigenschaftswerte, sodass es schwieriger wird, richtig zu werden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-127">This will be slower than using a tracking query, and may also result in issues such as missing shadow property values, making it harder to get right.</span></span>

### <a name="generated-verses-explicit-key-values"></a><span data-ttu-id="3b05b-128">Generierte Verse, explizite Schlüsselwerte</span><span class="sxs-lookup"><span data-stu-id="3b05b-128">Generated verses explicit key values</span></span>

<span data-ttu-id="3b05b-129">Standardmäßig sind die Eigenschaften "Integer" und "GUID [Key](xref:core/modeling/keys) " so konfiguriert, dass [automatisch generierte Schlüsselwerte](xref:core/modeling/generated-properties)verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-129">By default, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="3b05b-130">Dies hat einen **großen Vorteil für die Änderungs Nachverfolgung: ein nicht fest gelegteter Schlüsselwert gibt an, dass die Entität "New" ist**.</span><span class="sxs-lookup"><span data-stu-id="3b05b-130">This has a **major advantage for change tracking: an unset key value indicates that the entity is "new"**.</span></span> <span data-ttu-id="3b05b-131">"New" bedeutet, dass Sie noch nicht in die Datenbank eingefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="3b05b-131">By "new", we mean that it has not yet been inserted into the database.</span></span>

<span data-ttu-id="3b05b-132">In den folgenden Abschnitten werden zwei Modelle verwendet.</span><span class="sxs-lookup"><span data-stu-id="3b05b-132">Two models are used in the following sections.</span></span> <span data-ttu-id="3b05b-133">Der erste ist so konfiguriert, dass **keine** generierten Schlüsselwerte verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="3b05b-133">The first is configured to **not** use generated key values:</span></span>

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

<span data-ttu-id="3b05b-134">Nicht generierte (d.h. explizit festgelegte) Schlüsselwerte werden in jedem Beispiel zuerst angezeigt, da alles sehr explizit und leicht zu befolgen ist.</span><span class="sxs-lookup"><span data-stu-id="3b05b-134">Non-generated (i.e. explicitly set) key values are shown first in each example because everything is very explicit and easy to follow.</span></span> <span data-ttu-id="3b05b-135">Danach folgt ein Beispiel, in dem generierte Schlüsselwerte verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="3b05b-135">This is then followed by an example where generated key values are used:</span></span>

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

<span data-ttu-id="3b05b-136">Beachten Sie, dass die Schlüsseleigenschaften in diesem Modell hier keine zusätzliche Konfiguration erfordern, da die Verwendung von generierten Schlüsselwerten der [Standardwert für einfache ganzzahlige Schlüssel](xref:core/modeling/generated-properties)ist.</span><span class="sxs-lookup"><span data-stu-id="3b05b-136">Notice that the key properties in this model need no additional configuration here since using generated key values is the [default for simple integer keys](xref:core/modeling/generated-properties).</span></span>

## <a name="inserting-new-entities"></a><span data-ttu-id="3b05b-137">Einfügen von neuen Entitäten</span><span class="sxs-lookup"><span data-stu-id="3b05b-137">Inserting new entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="3b05b-138">Explizite Schlüsselwerte</span><span class="sxs-lookup"><span data-stu-id="3b05b-138">Explicit key values</span></span>

<span data-ttu-id="3b05b-139">Eine Entität muss in dem `Added` Zustand nachverfolgt werden, der von eingefügt werden soll <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> .</span><span class="sxs-lookup"><span data-stu-id="3b05b-139">An entity must be tracked in the `Added` state to be inserted by <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="3b05b-140">Entitäten werden in der Regel durch Aufrufen von <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType> oder den entsprechenden Methoden für <xref:Microsoft.EntityFrameworkCore.DbSet%601> in den hinzugefügten Zustand versetzt.</span><span class="sxs-lookup"><span data-stu-id="3b05b-140">Entities are typically put in the Added state by calling one of <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span>

> [!TIP]
> <span data-ttu-id="3b05b-141">Diese Methoden funktionieren im Kontext der Änderungs Nachverfolgung auf dieselbe Weise.</span><span class="sxs-lookup"><span data-stu-id="3b05b-141">These methods all work in the same way in the context of change tracking.</span></span> <span data-ttu-id="3b05b-142">Weitere Informationen finden Sie unter [zusätzliche Änderungsnachverfolgung Features](xref:core/change-tracking/miscellaneous) .</span><span class="sxs-lookup"><span data-stu-id="3b05b-142">See [Additional Change Tracking Features](xref:core/change-tracking/miscellaneous) for more information.</span></span>

<span data-ttu-id="3b05b-143">So beginnen Sie z. b. mit der Nachverfolgung eines neuen Blogs:</span><span class="sxs-lookup"><span data-stu-id="3b05b-143">For example, to start tracking a new blog:</span></span>

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Inserting_new_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_1)]

<span data-ttu-id="3b05b-144">Wenn Sie die [Debugansicht der Änderungs](xref:core/change-tracking/debug-views) Nachverfolgung nach diesem-Befehl überprüfen, wird angezeigt, dass der Kontext die neue Entität im Zustand nachverfolgt `Added` :</span><span class="sxs-lookup"><span data-stu-id="3b05b-144">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking the new entity in the `Added` state:</span></span>

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

<span data-ttu-id="3b05b-145">Die Add-Methoden funktionieren jedoch nicht nur für eine einzelne Entität.</span><span class="sxs-lookup"><span data-stu-id="3b05b-145">However, the Add methods don't just work on an individual entity.</span></span> <span data-ttu-id="3b05b-146">Sie beginnen tatsächlich mit der Nachverfolgung eines voll _ständigen Diagramms verwandter Entitäten_ und versetzen alle in den `Added` Zustand.</span><span class="sxs-lookup"><span data-stu-id="3b05b-146">They actually start tracking an _entire graph of related entities_, putting them all to the `Added` state.</span></span> <span data-ttu-id="3b05b-147">Wenn Sie z. b. einen neuen Blog und zugehörige neue Beiträge einfügen möchten:</span><span class="sxs-lookup"><span data-stu-id="3b05b-147">For example, to insert a new blog and associated new posts:</span></span>

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

<span data-ttu-id="3b05b-148">Der Kontext verfolgt nun alle diese Entitäten wie folgt `Added` :</span><span class="sxs-lookup"><span data-stu-id="3b05b-148">The context is now tracking all these entities as `Added`:</span></span>

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

<span data-ttu-id="3b05b-149">Beachten Sie, dass für die `Id` Schlüsseleigenschaften in den obigen Beispielen explizite Werte festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-149">Notice that explicit values have been set for the `Id` key properties in the examples above.</span></span> <span data-ttu-id="3b05b-150">Dies liegt daran, dass das Modell hier so konfiguriert wurde, dass explizit festgelegte Schlüsselwerte anstelle von automatisch generierten Schlüsselwerten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-150">This is because the model here has been configured to use explicitly set key values, rather than automatically generated key values.</span></span> <span data-ttu-id="3b05b-151">Wenn keine generierten Schlüssel verwendet werden, müssen die Schlüsseleigenschaften explizit festgelegt werden, _bevor_ aufgerufen wird `Add` .</span><span class="sxs-lookup"><span data-stu-id="3b05b-151">When not using generated keys, the key properties must be explicitly set _before_ calling `Add`.</span></span> <span data-ttu-id="3b05b-152">Diese Schlüsselwerte werden dann eingefügt, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="3b05b-152">These key values are then inserted when SaveChanges is called.</span></span> <span data-ttu-id="3b05b-153">Wenn sqlite beispielsweise verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="3b05b-153">For example, when using SQLite:</span></span>

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

<span data-ttu-id="3b05b-154">Alle diese Entitäten werden im `Unchanged` Zustand nachverfolgt, nachdem SaveChanges abgeschlossen ist, da diese Entitäten nun in der Datenbank vorhanden sind:</span><span class="sxs-lookup"><span data-stu-id="3b05b-154">All of these entities are tracked in the `Unchanged` state after SaveChanges completes, since these entities now exist in the database:</span></span>

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

### <a name="generated-key-values"></a><span data-ttu-id="3b05b-155">Generierte Schlüsselwerte</span><span class="sxs-lookup"><span data-stu-id="3b05b-155">Generated key values</span></span>

<span data-ttu-id="3b05b-156">Wie bereits erwähnt, werden ganzzahlige und GUID- [Schlüsseleigenschaften](xref:core/modeling/keys) standardmäßig für die Verwendung [automatisch generierter Schlüsselwerte](xref:core/modeling/generated-properties) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="3b05b-156">As mentioned above, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties) by default.</span></span> <span data-ttu-id="3b05b-157">Dies bedeutet, dass die Anwendung _keinen Schlüsselwert explizit festlegen darf_.</span><span class="sxs-lookup"><span data-stu-id="3b05b-157">This means that the application _must not set any key value explicitly_.</span></span> <span data-ttu-id="3b05b-158">Wenn Sie z. b. einen neuen Blog einfügen und alle mit generierten Schlüsselwerten Posten möchten:</span><span class="sxs-lookup"><span data-stu-id="3b05b-158">For example, to insert a new blog and posts all with generated key values:</span></span>

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

<span data-ttu-id="3b05b-159">Wie bei expliziten Schlüsselwerten verfolgt der Kontext jetzt alle folgenden Entitäten wie folgt `Added` :</span><span class="sxs-lookup"><span data-stu-id="3b05b-159">As with explicit key values, the context is now tracking all these entities as `Added`:</span></span>

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

<span data-ttu-id="3b05b-160">Beachten Sie, dass in diesem Fall [temporäre Schlüsselwerte](xref:core/change-tracking/miscellaneous#temporary-values) für jede Entität generiert wurden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-160">Notice in this case that [temporary key values](xref:core/change-tracking/miscellaneous#temporary-values) have been generated for each entity.</span></span> <span data-ttu-id="3b05b-161">Diese Werte werden von EF Core verwendet, bis SaveChanges aufgerufen wird, wobei echte Schlüsselwerte aus der Datenbank gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-161">These values are used by EF Core until SaveChanges is called, at which point real key values are read back from the database.</span></span> <span data-ttu-id="3b05b-162">Wenn sqlite beispielsweise verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="3b05b-162">For example, when using SQLite:</span></span>

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

<span data-ttu-id="3b05b-163">Nachdem SaveChanges abgeschlossen ist, wurden alle Entitäten mit ihren tatsächlichen Schlüsselwerten aktualisiert und im Zustand nachverfolgt, `Unchanged` da Sie nun dem Status in der Datenbank entsprechen:</span><span class="sxs-lookup"><span data-stu-id="3b05b-163">After SaveChanges completes, all of the entities have been updated with their real key values and are tracked in the `Unchanged` state since they now match the state in the database:</span></span>

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

<span data-ttu-id="3b05b-164">Dabei handelt es sich genau um denselben Endzustand wie im vorherigen Beispiel, in dem explizite Schlüsselwerte verwendet wurden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-164">This is exactly the same end-state as the previous example that used explicit key values.</span></span>

> [!TIP]
> <span data-ttu-id="3b05b-165">Ein expliziter Schlüsselwert kann auch dann festgelegt werden, wenn generierte Schlüsselwerte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-165">An explicit key value can still be set even when using generated key values.</span></span> <span data-ttu-id="3b05b-166">EF Core wird dann versucht, mit diesem Schlüsselwert einzufügen.</span><span class="sxs-lookup"><span data-stu-id="3b05b-166">EF Core will then attempt to insert using this key value.</span></span> <span data-ttu-id="3b05b-167">Einige Daten Bank Konfigurationen, einschließlich SQL Server mit Identitäts Spalten, unterstützen solche Einfügungen nicht und lösen diese aus.</span><span class="sxs-lookup"><span data-stu-id="3b05b-167">Some database configurations, including SQL Server with Identity columns, do not support such inserts and will throw.</span></span>

## <a name="attaching-existing-entities"></a><span data-ttu-id="3b05b-168">Anhängen vorhandener Entitäten</span><span class="sxs-lookup"><span data-stu-id="3b05b-168">Attaching existing entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="3b05b-169">Explizite Schlüsselwerte</span><span class="sxs-lookup"><span data-stu-id="3b05b-169">Explicit key values</span></span>

<span data-ttu-id="3b05b-170">Von Abfragen zurückgegebene Entitäten werden im-Zustand nachverfolgt `Unchanged` .</span><span class="sxs-lookup"><span data-stu-id="3b05b-170">Entities returned from queries are tracked in the `Unchanged` state.</span></span> <span data-ttu-id="3b05b-171">Der `Unchanged` Status bedeutet, dass die Entität seit dem Abfragen nicht geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="3b05b-171">The `Unchanged` state means that the entity has not been modified since it was queried.</span></span> <span data-ttu-id="3b05b-172">Eine nicht verbundene Entität, die möglicherweise von einem Webclient in einer HTTP-Anforderung zurückgegeben wird, kann in diesen Zustand versetzt werden, entweder mithilfe von <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType> oder den entsprechenden Methoden in <xref:Microsoft.EntityFrameworkCore.DbSet%601> .</span><span class="sxs-lookup"><span data-stu-id="3b05b-172">A disconnected entity, perhaps returned from a web client in an HTTP request, can be put into this state using either <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span> <span data-ttu-id="3b05b-173">So beginnen Sie z. b. mit der Nachverfolgung eines vorhandenen Blogs:</span><span class="sxs-lookup"><span data-stu-id="3b05b-173">For example, to start tracking an existing blog:</span></span>

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
> <span data-ttu-id="3b05b-174">In den hier aufgeführten Beispielen werden Entitäten `new` aus Gründen der Einfachheit explizit erstellt.</span><span class="sxs-lookup"><span data-stu-id="3b05b-174">The examples here are creating entities explicitly with `new` for simplicity.</span></span> <span data-ttu-id="3b05b-175">Normalerweise stammen die Entitäts Instanzen aus einer anderen Quelle, z. b. Wenn Sie von einem Client deserialisiert oder aus Daten in einem HTTP Post erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-175">Normally the entity instances will have come from another source, such as being deserialized from a client, or being created from data in an HTTP Post.</span></span>

<span data-ttu-id="3b05b-176">Wenn Sie die [Debugansicht](xref:core/change-tracking/debug-views) für die Änderungs Nachverfolgung nach diesem-Befehl überprüfen, wird angezeigt, dass die Entität im- `Unchanged` Zustand</span><span class="sxs-lookup"><span data-stu-id="3b05b-176">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the entity is tracked in the `Unchanged` state:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

<span data-ttu-id="3b05b-177">Ebenso wie `Add` `Attach` legt tatsächlich ein gesamtes Diagramm verbundener Entitäten auf den- `Unchanged` Zustand fest.</span><span class="sxs-lookup"><span data-stu-id="3b05b-177">Just like `Add`, `Attach` actually sets an entire graph of connected entities to the `Unchanged` state.</span></span> <span data-ttu-id="3b05b-178">Um beispielsweise einen vorhandenen Blog und zugehörige vorhandene Beiträge anzufügen:</span><span class="sxs-lookup"><span data-stu-id="3b05b-178">For example, to attach an existing blog and associated existing posts:</span></span>

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

<span data-ttu-id="3b05b-179">Der Kontext verfolgt nun alle diese Entitäten wie folgt `Unchanged` :</span><span class="sxs-lookup"><span data-stu-id="3b05b-179">The context is now tracking all these entities as `Unchanged`:</span></span>

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

<span data-ttu-id="3b05b-180">Das Aufrufen von SaveChanges an dieser Stelle hat keine Auswirkungen.</span><span class="sxs-lookup"><span data-stu-id="3b05b-180">Calling SaveChanges at this point will have no effect.</span></span> <span data-ttu-id="3b05b-181">Alle Entitäten werden als gekennzeichnet `Unchanged` , sodass keine Aktualisierung in der Datenbank durchführt.</span><span class="sxs-lookup"><span data-stu-id="3b05b-181">All the entities are marked as `Unchanged`, so there is nothing to update in the database.</span></span>

### <a name="generated-key-values"></a><span data-ttu-id="3b05b-182">Generierte Schlüsselwerte</span><span class="sxs-lookup"><span data-stu-id="3b05b-182">Generated key values</span></span>

<span data-ttu-id="3b05b-183">Wie bereits erwähnt, werden ganzzahlige und GUID- [Schlüsseleigenschaften](xref:core/modeling/keys) standardmäßig für die Verwendung [automatisch generierter Schlüsselwerte](xref:core/modeling/generated-properties) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="3b05b-183">As mentioned above, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties) by default.</span></span> <span data-ttu-id="3b05b-184">Dies hat beim Arbeiten mit getrennten Entitäten einen großen Vorteil: ein nicht festgelegter Schlüsselwert gibt an, dass die Entität noch nicht in die Datenbank eingefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="3b05b-184">This has a major advantage when working with disconnected entities: an unset key value indicates that the entity has not yet been inserted into the database.</span></span> <span data-ttu-id="3b05b-185">Dies ermöglicht es dem Änderungs nach Verfolgungs, neue Entitäten automatisch zu erkennen und in den Zustand zu versetzen `Added` .</span><span class="sxs-lookup"><span data-stu-id="3b05b-185">This allows the change tracker to automatically detect new entities and put them in the `Added` state.</span></span> <span data-ttu-id="3b05b-186">Nehmen Sie beispielsweise an, dieses Diagramm eines Blogs und Beiträge anzufügen:</span><span class="sxs-lookup"><span data-stu-id="3b05b-186">For example, consider attaching this graph of a blog and posts:</span></span>

```c#
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
```

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

<span data-ttu-id="3b05b-187">Der Blog hat den Schlüsselwert 1, was darauf hinweist, dass er bereits in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="3b05b-187">The blog has a key value of 1, indicating that it already exists in the database.</span></span> <span data-ttu-id="3b05b-188">Für zwei der Beiträge sind auch Schlüsselwerte festgelegt, das dritte jedoch nicht.</span><span class="sxs-lookup"><span data-stu-id="3b05b-188">Two of the posts also have key values set, but the third does not.</span></span> <span data-ttu-id="3b05b-189">EF Core wird dieser Schlüsselwert als 0 angezeigt, der CLR-Standardwert für eine ganze Zahl.</span><span class="sxs-lookup"><span data-stu-id="3b05b-189">EF Core will see this key value as 0, the CLR default for an integer.</span></span> <span data-ttu-id="3b05b-190">Dies führt dazu, dass EF Core die neue Entität als `Added` anstelle von kennzeichnet `Unchanged` :</span><span class="sxs-lookup"><span data-stu-id="3b05b-190">This results in EF Core marking the new entity as `Added` instead of `Unchanged`:</span></span>

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

<span data-ttu-id="3b05b-191">Das Aufrufen von SaveChanges an dieser Stelle hat keine Auswirkungen auf die `Unchanged` Entitäten, sondern fügt die neue Entität in die Datenbank ein.</span><span class="sxs-lookup"><span data-stu-id="3b05b-191">Calling SaveChanges at this point does nothing with the `Unchanged` entities, but inserts the new entity into the database.</span></span> <span data-ttu-id="3b05b-192">Wenn sqlite beispielsweise verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="3b05b-192">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="3b05b-193">Wichtig zu beachten ist, dass EF Core mit generierten Schlüsselwerten **automatisch neu von vorhandenen Entitäten in einem getrennten Diagramm unterscheiden** kann.</span><span class="sxs-lookup"><span data-stu-id="3b05b-193">The important point to notice here is that, with generated key values, EF Core is able to **automatically distinguish new from existing entities in a disconnected graph**.</span></span> <span data-ttu-id="3b05b-194">Kurz gesagt: Wenn Sie generierte Schlüssel verwenden, wird EF Core immer eine Entität einfügen, wenn für diese Entität kein Schlüsselwert festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="3b05b-194">In a nutshell, when using generated keys, EF Core will always insert an entity when that entity has no key value set.</span></span>

## <a name="updating-existing-entities"></a><span data-ttu-id="3b05b-195">Aktualisieren vorhandener Entitäten</span><span class="sxs-lookup"><span data-stu-id="3b05b-195">Updating existing entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="3b05b-196">Explizite Schlüsselwerte</span><span class="sxs-lookup"><span data-stu-id="3b05b-196">Explicit key values</span></span>

<span data-ttu-id="3b05b-197"><xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType> und die äquivalenten Methoden in <xref:Microsoft.EntityFrameworkCore.DbSet%601> Verhalten sich genau wie die `Attach` oben beschriebenen Methoden, mit der Ausnahme, dass Entitäten anstelle des-Zustands in den eingefügt werden `Modfied` `Unchanged` .</span><span class="sxs-lookup"><span data-stu-id="3b05b-197"><xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType>, and the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601> behave exactly as the `Attach` methods described above, except that entities are put into the `Modfied` instead of the `Unchanged` state.</span></span> <span data-ttu-id="3b05b-198">So beginnen Sie beispielsweise mit der Nachverfolgung eines vorhandenen Blogs wie folgt `Modified` :</span><span class="sxs-lookup"><span data-stu-id="3b05b-198">For example, to start tracking an existing blog as `Modified`:</span></span>

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Updating_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_1)]

<span data-ttu-id="3b05b-199">Wenn Sie die [Debugansicht der Änderungs](xref:core/change-tracking/debug-views) Nachverfolgung nach diesem-Befehl überprüfen, wird angezeigt, dass der Kontext diese Entität im `Modified` Zustand verfolgt:</span><span class="sxs-lookup"><span data-stu-id="3b05b-199">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking this entity in the `Modified` state:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: []
```

<span data-ttu-id="3b05b-200">Ebenso wie bei `Add` und `Attach` `Update` kennzeichnet tatsächlich ein _gesamtes Diagramm_ verwandter Entitäten als `Modified` .</span><span class="sxs-lookup"><span data-stu-id="3b05b-200">Just like with `Add` and `Attach`, `Update` actually marks an _entire graph_ of related entities as `Modified`.</span></span> <span data-ttu-id="3b05b-201">Fügen Sie z. b. einen vorhandenen Blog und zugehörige vorhandene Beiträge wie folgt an `Modified` :</span><span class="sxs-lookup"><span data-stu-id="3b05b-201">For example, to attach an existing blog and associated existing posts as `Modified`:</span></span>

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

<span data-ttu-id="3b05b-202">Der Kontext verfolgt nun alle diese Entitäten wie folgt `Modified` :</span><span class="sxs-lookup"><span data-stu-id="3b05b-202">The context is now tracking all these entities as `Modified`:</span></span>

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

<span data-ttu-id="3b05b-203">Wenn Sie an diesem Punkt SaveChanges aufrufen, werden Updates für alle diese Entitäten an die Datenbank gesendet.</span><span class="sxs-lookup"><span data-stu-id="3b05b-203">Calling SaveChanges at this point will cause updates to be sent to the database for all these entities.</span></span> <span data-ttu-id="3b05b-204">Wenn sqlite beispielsweise verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="3b05b-204">For example, when using SQLite:</span></span>

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

### <a name="generated-key-values"></a><span data-ttu-id="3b05b-205">Generierte Schlüsselwerte</span><span class="sxs-lookup"><span data-stu-id="3b05b-205">Generated key values</span></span>

<span data-ttu-id="3b05b-206">Wie bei `Attach` haben generierte Schlüsselwerte denselben größten Vorteil für `Update` : ein nicht festgelegter Schlüsselwert gibt an, dass die Entität neu ist und noch nicht in die Datenbank eingefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="3b05b-206">As with `Attach`, generated key values have the same major benefit for `Update`: an unset key value indicates that the entity is new and has not yet been inserted into the database.</span></span> <span data-ttu-id="3b05b-207">Wie bei `Attach` ermöglicht es dbcontext, neue Entitäten automatisch zu erkennen und Sie in den- `Added` Zustand zu versetzen.</span><span class="sxs-lookup"><span data-stu-id="3b05b-207">As with `Attach`, this allows the DbContext to automatically detect new entities and put them in the `Added` state.</span></span> <span data-ttu-id="3b05b-208">Nehmen Sie beispielsweise an, dass Sie `Update` mit diesem Diagramm eines Blogs und Beiträgen aufrufen:</span><span class="sxs-lookup"><span data-stu-id="3b05b-208">For example, consider calling `Update` with this graph of a blog and posts:</span></span>

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

<span data-ttu-id="3b05b-209">Wie im `Attach` Beispiel wird der Post ohne Schlüsselwert als neu erkannt und auf den-Zustand festgelegt `Added` .</span><span class="sxs-lookup"><span data-stu-id="3b05b-209">As with the `Attach` example, the post with no key value is detected as new and set to the `Added` state.</span></span> <span data-ttu-id="3b05b-210">Die anderen Entitäten sind wie folgt gekennzeichnet `Modified` :</span><span class="sxs-lookup"><span data-stu-id="3b05b-210">The other entities are marked as `Modified`:</span></span>

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

<span data-ttu-id="3b05b-211">`SaveChanges`Wenn Sie an dieser Stelle aufrufen, werden Updates für alle vorhandenen Entitäten an die Datenbank gesendet, während die neue Entität eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="3b05b-211">Calling `SaveChanges` at this point will cause updates to be sent to the database for all the existing entities, while the new entity is inserted.</span></span> <span data-ttu-id="3b05b-212">Wenn sqlite beispielsweise verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="3b05b-212">For example, when using SQLite:</span></span>

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

<span data-ttu-id="3b05b-213">Dies ist eine sehr einfache Möglichkeit, um Updates und Einfügungen aus einem getrennten Diagramm zu generieren.</span><span class="sxs-lookup"><span data-stu-id="3b05b-213">This is a very easy way to generate updates and inserts from a disconnected graph.</span></span> <span data-ttu-id="3b05b-214">Dies führt jedoch dazu, dass Updates oder Einfügungen für jede Eigenschaft jeder überwachten Entität an die Datenbank gesendet werden, auch wenn einige Eigenschaftswerte möglicherweise nicht geändert wurden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-214">However, it results in updates or inserts being sent to the database for every property of every tracked entity, even when some property values may not have been changed.</span></span> <span data-ttu-id="3b05b-215">Das ist nicht zu ängstlich. bei vielen Anwendungen mit kleinen Diagrammen kann dies eine einfache und pragmatische Methode zum Erstellen von Updates sein.</span><span class="sxs-lookup"><span data-stu-id="3b05b-215">Don't be too scared by this; for many applications with small graphs, this can be an easy and pragmatic way of generating updates.</span></span> <span data-ttu-id="3b05b-216">Das heißt, dass andere komplexere Muster manchmal zu effizienteren Updates führen können, wie unter [Identitäts Auflösung in EF Core](xref:core/change-tracking/identity-resolution)beschrieben.</span><span class="sxs-lookup"><span data-stu-id="3b05b-216">That being said, other more complex patterns can sometimes result in more efficient updates, as described in [Identity Resolution in EF Core](xref:core/change-tracking/identity-resolution).</span></span>

## <a name="deleting-existing-entities"></a><span data-ttu-id="3b05b-217">Löschen vorhandener Entitäten</span><span class="sxs-lookup"><span data-stu-id="3b05b-217">Deleting existing entities</span></span>

<span data-ttu-id="3b05b-218">Damit eine Entität von SaveChanges gelöscht werden kann, muss Sie im-Zustand nachverfolgt werden `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="3b05b-218">For an entity to be deleted by SaveChanges it must be tracked in the `Deleted` state.</span></span> <span data-ttu-id="3b05b-219">Entitäten werden in der Regel `Deleted` durch Aufrufen einer der <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> Methoden, <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType> oder der entsprechenden Methoden in eingefügt <xref:Microsoft.EntityFrameworkCore.DbSet%601> .</span><span class="sxs-lookup"><span data-stu-id="3b05b-219">Entities are typically put in the `Deleted` state by calling one of <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span> <span data-ttu-id="3b05b-220">So markieren Sie z. b. einen vorhandenen Beitrag wie folgt `Deleted` :</span><span class="sxs-lookup"><span data-stu-id="3b05b-220">For example, to mark an existing post as `Deleted`:</span></span>

<!--
            context.Remove(
                new Post
                {
                    Id = 2
                });
-->
[!code-csharp[Deleting_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_existing_entities_1)]

<span data-ttu-id="3b05b-221">Durch Überprüfen der [Debugansicht](xref:core/change-tracking/debug-views) für die Änderungs Nachverfolgung nach diesem Befehl wird angezeigt, dass der Kontext die Entität im Zustand nachverfolgt `Deleted` :</span><span class="sxs-lookup"><span data-stu-id="3b05b-221">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking the entity in the `Deleted` state:</span></span>

```output
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: <null> FK
  Content: <null>
  Title: <null>
  Blog: <null>
```

<span data-ttu-id="3b05b-222">Diese Entität wird gelöscht, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="3b05b-222">This entity will be deleted when SaveChanges is called.</span></span> <span data-ttu-id="3b05b-223">Wenn sqlite beispielsweise verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="3b05b-223">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

<span data-ttu-id="3b05b-224">Nachdem SaveChanges abgeschlossen wurde, wird die gelöschte Entität vom dbcontext getrennt, da Sie nicht mehr in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="3b05b-224">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="3b05b-225">Die Debugansicht ist daher leer, da keine Entitäten nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-225">The debug view is therefore empty because no entities are being tracked.</span></span>

## <a name="deleting-dependentchild-entities"></a><span data-ttu-id="3b05b-226">Löschen abhängiger/untergeordneter Entitäten</span><span class="sxs-lookup"><span data-stu-id="3b05b-226">Deleting dependent/child entities</span></span>

<span data-ttu-id="3b05b-227">Das Löschen abhängiger/untergeordneter Entitäten aus einem Diagramm ist einfacher als das Löschen von Prinzipal-/Unterordnung-</span><span class="sxs-lookup"><span data-stu-id="3b05b-227">Deleting dependent/child entities from a graph is more straightforward than deleting principal/parent entities.</span></span> <span data-ttu-id="3b05b-228">Weitere Informationen finden Sie im nächsten Abschnitt und unter [Ändern von Fremdschlüsseln und Navigationen](xref:core/change-tracking/relationship-changes) .</span><span class="sxs-lookup"><span data-stu-id="3b05b-228">See the next section and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information.</span></span>

<span data-ttu-id="3b05b-229">Es ist ungewöhnlich, für `Remove` eine Entität aufzurufen, die mit erstellt wurde `new` .</span><span class="sxs-lookup"><span data-stu-id="3b05b-229">It is unusual to call `Remove` on an entity created with `new`.</span></span> <span data-ttu-id="3b05b-230">Außerdem `Add` `Attach` ist es im Gegensatz `Update` zu und nicht üblich, `Remove` für eine Entität aufzurufen, die nicht bereits im-Zustand oder im-Zustand nachverfolgt wird `Unchanged` `Modified` .</span><span class="sxs-lookup"><span data-stu-id="3b05b-230">Further, unlike `Add`, `Attach` and `Update`, it is uncommon to call `Remove` on an entity that isn't already tracked in the `Unchanged` or `Modified` state.</span></span> <span data-ttu-id="3b05b-231">Stattdessen ist es typisch, eine einzelne Entität oder ein Diagramm verwandter Entitäten zu verfolgen und dann `Remove` für die Entitäten aufzurufen, die gelöscht werden sollen.</span><span class="sxs-lookup"><span data-stu-id="3b05b-231">Instead it is typical to track a single entity or graph of related entities, and then call `Remove` on the entities that should be deleted.</span></span> <span data-ttu-id="3b05b-232">Dieses Diagramm der nach verfolgten Entitäten wird in der Regel durch Folgendes erstellt:</span><span class="sxs-lookup"><span data-stu-id="3b05b-232">This graph of tracked entities is typically created by either:</span></span>

1. <span data-ttu-id="3b05b-233">Ausführen einer Abfrage für die Entitäten</span><span class="sxs-lookup"><span data-stu-id="3b05b-233">Running a query for the entities</span></span>
2. <span data-ttu-id="3b05b-234">Verwenden der- `Attach` Methode oder der- `Update` Methode in einem Diagramm von getrennten Entitäten, wie in den vorherigen Abschnitten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="3b05b-234">Using the `Attach` or `Update` methods on a graph of disconnected entities, as described in the preceding sections.</span></span>

<span data-ttu-id="3b05b-235">Beispielsweise ist der Code im vorherigen Abschnitt wahrscheinlicher, dass er einen Beitrag von einem Client erhält, und dann etwa wie folgt vorgehen:</span><span class="sxs-lookup"><span data-stu-id="3b05b-235">For example, the code in the previous section is more likely obtain a post from a client and then do something like this:</span></span>

<!--
            context.Attach(post);
            context.Remove(post);
-->
[!code-csharp[Deleting_dependent_child_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_1)]

<span data-ttu-id="3b05b-236">Dies verhält sich genau so wie das vorherige Beispiel, da der Aufruf `Remove` von für eine nicht verfolgte Entität bewirkt, dass Sie zuerst angefügt und dann als markiert wird `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="3b05b-236">This behaves exactly the same way as the previous example, since calling `Remove` on an un-tracked entity causes it to first be attached and then marked as `Deleted`.</span></span>

<span data-ttu-id="3b05b-237">In realistischeren Beispielen wird zunächst ein Diagramm mit Entitäten angehängt, und einige dieser Entitäten werden als gelöscht markiert.</span><span class="sxs-lookup"><span data-stu-id="3b05b-237">In more realistic examples, a graph of entities is first attached, and then some of those entities are marked as deleted.</span></span> <span data-ttu-id="3b05b-238">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3b05b-238">For example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_dependent_child_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_2)]

<span data-ttu-id="3b05b-239">Alle Entitäten werden als gekennzeichnet `Unchanged` , mit Ausnahme derjenigen, für die `Remove` aufgerufen wurde:</span><span class="sxs-lookup"><span data-stu-id="3b05b-239">All entities are marked as `Unchanged`, except the one on which `Remove` was called:</span></span>

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

<span data-ttu-id="3b05b-240">Diese Entität wird gelöscht, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="3b05b-240">This entity will be deleted when SaveChanges is called.</span></span> <span data-ttu-id="3b05b-241">Wenn sqlite beispielsweise verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="3b05b-241">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

<span data-ttu-id="3b05b-242">Nachdem SaveChanges abgeschlossen wurde, wird die gelöschte Entität vom dbcontext getrennt, da Sie nicht mehr in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="3b05b-242">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="3b05b-243">Andere Entitäten verbleiben im `Unchanged` Status:</span><span class="sxs-lookup"><span data-stu-id="3b05b-243">Other entities remain in the `Unchanged` state:</span></span>

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

## <a name="deleting-principalparent-entities"></a><span data-ttu-id="3b05b-244">Löschen von Principal/Parent-Entitäten</span><span class="sxs-lookup"><span data-stu-id="3b05b-244">Deleting principal/parent entities</span></span>

<span data-ttu-id="3b05b-245">Jede Beziehung, die zwei Entitäts Typen verbindet, verfügt über ein Prinzipal-oder übergeordnetes Ende und ein abhängiges oder untergeordnetes Ende.</span><span class="sxs-lookup"><span data-stu-id="3b05b-245">Each relationship that connects two entity types has a principal or parent end, and a dependent or child end.</span></span> <span data-ttu-id="3b05b-246">Die abhängige/untergeordnete Entität ist die, die die Fremdschlüssel Eigenschaft hat.</span><span class="sxs-lookup"><span data-stu-id="3b05b-246">The dependent/child entity is the one with the foreign key property.</span></span> <span data-ttu-id="3b05b-247">In einer 1: n-Beziehung befindet sich der Prinzipal/das übergeordnete Element auf der Seite "1", und das abhängige/untergeordnete Element befindet sich auf der Seite "Many".</span><span class="sxs-lookup"><span data-stu-id="3b05b-247">In a one-to-many relationship, the principal/parent is on the "one" side, and the dependent/child is on the "many" side.</span></span> <span data-ttu-id="3b05b-248">Weitere Informationen finden Sie unter [Beziehungen](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="3b05b-248">See [Relationships](xref:core/modeling/relationships) for more information.</span></span>

<span data-ttu-id="3b05b-249">In den vorangehenden Beispielen haben wir einen Beitrag gelöscht, bei dem es sich um eine abhängige/untergeordnete Entität im Blogbeitrag 1: n-Beziehung handelt.</span><span class="sxs-lookup"><span data-stu-id="3b05b-249">In the preceding examples we were deleting a post, which is a dependent/child entity in the blog-posts one-to-many relationship.</span></span> <span data-ttu-id="3b05b-250">Dies ist relativ unkompliziert, da das Entfernen einer abhängigen/untergeordneten Entität keine Auswirkung auf andere Entitäten hat.</span><span class="sxs-lookup"><span data-stu-id="3b05b-250">This is relatively straightforward since removal of a dependent/child entity does not have any impact on other entities.</span></span> <span data-ttu-id="3b05b-251">Andererseits muss das Löschen einer Prinzipal-/Parent-Entität auch Auswirkungen auf abhängige/untergeordnete Entitäten haben.</span><span class="sxs-lookup"><span data-stu-id="3b05b-251">On the other hand, deleting a principal/parent entity must also impact any dependent/child entities.</span></span> <span data-ttu-id="3b05b-252">Wenn Sie dies nicht tun, würde ein Fremdschlüssel Wert, der auf einen Primärschlüssel Wert verweist, nicht mehr vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="3b05b-252">Not doing so would leave a foreign key value referencing a primary key value that no longer exists.</span></span> <span data-ttu-id="3b05b-253">Dies ist ein ungültiger Modell Status und führt in den meisten Datenbanken zu einem referenziellen Einschränkungs Fehler.</span><span class="sxs-lookup"><span data-stu-id="3b05b-253">This is an invalid model state and results in a referential constraint error in most databases.</span></span>

<span data-ttu-id="3b05b-254">Dieser ungültige Modell Zustand kann auf zwei Arten behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="3b05b-254">This invalid model state can be handled in two ways:</span></span>

1. <span data-ttu-id="3b05b-255">Festlegen von FK-Werten auf NULL.</span><span class="sxs-lookup"><span data-stu-id="3b05b-255">Setting FK values to null.</span></span> <span data-ttu-id="3b05b-256">Dies gibt an, dass die abhängigen Elemente/untergeordneten Elemente nicht mehr mit einem Prinzipal/übergeordneten Element verknüpft sind.</span><span class="sxs-lookup"><span data-stu-id="3b05b-256">This indicates that the dependents/children are no longer related to any principal/parent.</span></span> <span data-ttu-id="3b05b-257">Dies ist die Standardeinstellung für optionale Beziehungen, bei denen der Fremdschlüssel NULL-Werte zulassen muss.</span><span class="sxs-lookup"><span data-stu-id="3b05b-257">This is the default for optional relationships where the foreign key must be nullable.</span></span> <span data-ttu-id="3b05b-258">Das Festlegen des FK auf NULL ist für erforderliche Beziehungen ungültig, bei denen der Fremdschlüssel in der Regel keine NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="3b05b-258">Setting the FK to null is not valid for required relationships, where the foreign key is typically non-nullable.</span></span>
2. <span data-ttu-id="3b05b-259">Die abhängigen/untergeordneten Elemente werden gelöscht.</span><span class="sxs-lookup"><span data-stu-id="3b05b-259">Deleting the the dependents/children.</span></span> <span data-ttu-id="3b05b-260">Dies ist der Standardwert für erforderliche Beziehungen, der auch für optionale Beziehungen gültig ist.</span><span class="sxs-lookup"><span data-stu-id="3b05b-260">This is the default for required relationships, and is also valid for optional relationships.</span></span>

<span data-ttu-id="3b05b-261">Ausführliche Informationen zur Änderungs Nachverfolgung und zu Beziehungen finden Sie unter [Ändern von Fremdschlüsseln und Navigationen](xref:core/change-tracking/relationship-changes) .</span><span class="sxs-lookup"><span data-stu-id="3b05b-261">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for detailed information on change tracking and relationships.</span></span>

### <a name="optional-relationships"></a><span data-ttu-id="3b05b-262">Optionale Beziehungen</span><span class="sxs-lookup"><span data-stu-id="3b05b-262">Optional relationships</span></span>

<span data-ttu-id="3b05b-263">Die `Post.BlogId` Fremdschlüssel Eigenschaft kann im verwendeten Modell auf NULL festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-263">The `Post.BlogId` foreign key property is nullable in the model we have been using.</span></span> <span data-ttu-id="3b05b-264">Dies bedeutet, dass die Beziehung optional ist. Daher besteht das Standardverhalten von EF Core darin, `BlogId` Fremdschlüssel Eigenschaften auf NULL festzulegen, wenn der Blog gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="3b05b-264">This means the relationship is optional, and hence the default behavior of EF Core is to set `BlogId` foreign key properties to null when the blog is deleted.</span></span> <span data-ttu-id="3b05b-265">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3b05b-265">For example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_principal_parent_entities_1)]

<span data-ttu-id="3b05b-266">Wenn Sie die [Debugansicht](xref:core/change-tracking/debug-views) für die Änderungs Nachverfolgung nach dem Aufrufen von untersuchen, `Remove` wird angezeigt, dass der Blog wie erwartet nun wie erwartet gekennzeichnet ist `Deleted` :</span><span class="sxs-lookup"><span data-stu-id="3b05b-266">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following the call to `Remove` shows that, as expected, the blog is now marked as `Deleted`:</span></span>

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

<span data-ttu-id="3b05b-267">Interessanterweise sind alle zugehörigen Beiträge nun als markiert `Modified` .</span><span class="sxs-lookup"><span data-stu-id="3b05b-267">More interestingly, all the related posts are now marked as `Modified`.</span></span> <span data-ttu-id="3b05b-268">Dies liegt daran, dass die Fremdschlüssel Eigenschaft in den einzelnen Entitäten auf NULL festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="3b05b-268">This is because the foreign key property in each entity has been set to null.</span></span> <span data-ttu-id="3b05b-269">Wenn Sie SaveChanges aufrufen, wird der Fremdschlüssel Wert für jeden Post in NULL in der Datenbank aktualisiert, bevor der Blog gelöscht wird:</span><span class="sxs-lookup"><span data-stu-id="3b05b-269">Calling SaveChanges updates the foreign key value for each post to null in the database, before then deleting the blog:</span></span>

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

<span data-ttu-id="3b05b-270">Nachdem SaveChanges abgeschlossen wurde, wird die gelöschte Entität vom dbcontext getrennt, da Sie nicht mehr in der Datenbank vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="3b05b-270">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="3b05b-271">Andere Entitäten sind nun als `Unchanged` mit NULL-Fremdschlüssel Werten gekennzeichnet, die mit dem Status der Datenbank übereinstimmen:</span><span class="sxs-lookup"><span data-stu-id="3b05b-271">Other entities are now marked as `Unchanged` with null foreign key values, which matches the state of the database:</span></span>

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

### <a name="required-relationships"></a><span data-ttu-id="3b05b-272">Erforderliche Beziehungen</span><span class="sxs-lookup"><span data-stu-id="3b05b-272">Required relationships</span></span>

<span data-ttu-id="3b05b-273">Wenn die `Post.BlogId` Fremdschlüssel Eigenschaft keine NULL-Werte zulässt, wird die Beziehung zwischen Blogs und Beiträgen zu "erforderlich".</span><span class="sxs-lookup"><span data-stu-id="3b05b-273">If the `Post.BlogId` foreign key property is non-nullable, then the relationship between blogs and posts becomes "required".</span></span> <span data-ttu-id="3b05b-274">In dieser Situation löschen EF Core standardmäßig abhängige/untergeordnete Entitäten, wenn der Prinzipal/das übergeordnete Element gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="3b05b-274">In this situation, EF Core will, by default, delete dependent/child entities when the principal/parent is deleted.</span></span> <span data-ttu-id="3b05b-275">Löschen Sie beispielsweise einen Blog mit verwandten Beiträgen wie im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3b05b-275">For example, deleting a blog with related posts as in the previous example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysRequiredSamples.cs?name=Deleting_principal_parent_entities_1)]

<span data-ttu-id="3b05b-276">Wenn Sie die [Debugansicht](xref:core/change-tracking/debug-views) für die Änderungs Nachverfolgung nach dem Aufrufen von untersuchen, `Remove` wird angezeigt, dass der Blog wie erwartet mit folgendem gekennzeichnet ist `Deleted` :</span><span class="sxs-lookup"><span data-stu-id="3b05b-276">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following the call to `Remove` shows that, as expected, the blog is again marked as `Deleted`:</span></span>

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

<span data-ttu-id="3b05b-277">Interessanter ist in diesem Fall, dass alle zugehörigen Beiträge auch als gekennzeichnet wurden `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="3b05b-277">More interestingly in this case is that all related posts have also been marked as `Deleted`.</span></span> <span data-ttu-id="3b05b-278">Das Aufrufen von SaveChanges bewirkt, dass der Blog und alle zugehörigen Beiträge aus der Datenbank gelöscht werden:</span><span class="sxs-lookup"><span data-stu-id="3b05b-278">Calling SaveChanges causes the blog and all related posts to be deleted from the database:</span></span>

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

<span data-ttu-id="3b05b-279">Nachdem SaveChanges abgeschlossen ist, werden alle gelöschten Entitäten von dbcontext getrennt, da Sie nicht mehr in der Datenbank vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="3b05b-279">After SaveChanges completes, all the deleted entities are detached from the DbContext since they no longer exist in the database.</span></span> <span data-ttu-id="3b05b-280">Die Ausgabe der Debugansicht ist daher leer.</span><span class="sxs-lookup"><span data-stu-id="3b05b-280">Output from the debug view is therefore empty.</span></span>

> [!NOTE]
> <span data-ttu-id="3b05b-281">In diesem Dokument wird nur die Oberfläche beim Arbeiten mit Beziehungen in EF Core.</span><span class="sxs-lookup"><span data-stu-id="3b05b-281">This document only scratches the surface on working with relationships in EF Core.</span></span> <span data-ttu-id="3b05b-282">Weitere [Informationen zum Aktualisieren](xref:core/change-tracking/relationship-changes) /löschen abhängiger/untergeordneter Entitäten beim Aufrufen von SaveChanges finden Sie unter [Beziehungen](xref:core/modeling/relationships) .</span><span class="sxs-lookup"><span data-stu-id="3b05b-282">See [Relationships](xref:core/modeling/relationships) for more information on modeling relationships, and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information on updating/deleting dependent/child entities when calling SaveChanges.</span></span>

## <a name="custom-tracking-with-trackgraph"></a><span data-ttu-id="3b05b-283">Benutzerdefinierte Nachverfolgung mit trackgraph</span><span class="sxs-lookup"><span data-stu-id="3b05b-283">Custom tracking with TrackGraph</span></span>

<span data-ttu-id="3b05b-284"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> funktioniert wie `Add` , `Attach` und `Update` mit der Ausnahme, dass ein Rückruf für jede Entitäts Instanz generiert wird, bevor Sie nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="3b05b-284"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> works like `Add`, `Attach` and `Update` except that it generates a callback for every entity instance before tracking it.</span></span> <span data-ttu-id="3b05b-285">Dadurch kann eine benutzerdefinierte Logik verwendet werden, um zu bestimmen, wie einzelne Entitäten in einem Diagramm nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-285">This allows custom logic to be used when determining how to track individual entities in a graph.</span></span>

<span data-ttu-id="3b05b-286">Nehmen Sie z. b. die Regel EF Core die beim Nachverfolgen von Entitäten mit generierten Schlüsselwerten verwendet: Wenn der Kye-Wert 0 (null) ist, ist die Entität neu und sollte eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-286">For example, consider the rule EF Core uses when tracking entities with generated key values: if the kye value is zero, then the entity is new and should be inserted.</span></span> <span data-ttu-id="3b05b-287">Wir erweitern diese Regel, um zu sagen, dass der Schlüsselwert negativ ist, dann sollte die Entität gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-287">Let's extend this rule to say if the key value is negative, then the entity should be deleted.</span></span> <span data-ttu-id="3b05b-288">Dies ermöglicht es uns, die Primärschlüssel Werte in Entitäten eines getrennten Diagramms so zu ändern, dass gelöschte Entitäten markiert werden:</span><span class="sxs-lookup"><span data-stu-id="3b05b-288">This allows us to change the primary key values in entities of a disconnected graph to mark deleted entities:</span></span>

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

<span data-ttu-id="3b05b-289">Dieses getrennte Diagramm kann dann mithilfe von trackgraph nachverfolgt werden:</span><span class="sxs-lookup"><span data-stu-id="3b05b-289">This disconnected graph can then be tracked using TrackGraph:</span></span>

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

<span data-ttu-id="3b05b-290">Für jede Entität im Diagramm prüft der obige Code den Primärschlüssel Wert, _bevor die Entität nachverfolgt_ wird.</span><span class="sxs-lookup"><span data-stu-id="3b05b-290">For each entity in the graph, the code above checks the primary key value _before tracking the entity_.</span></span> <span data-ttu-id="3b05b-291">Für nicht festgelegte (0) Schlüsselwerte führt der Code die EF Core normalerweise durch.</span><span class="sxs-lookup"><span data-stu-id="3b05b-291">For unset (zero) key values, the code does what EF Core would normally do.</span></span> <span data-ttu-id="3b05b-292">Das heißt, wenn der Schlüssel nicht festgelegt ist, wird die Entität als markiert `Added` .</span><span class="sxs-lookup"><span data-stu-id="3b05b-292">That is, if the key is not set, then the entity is marked as `Added`.</span></span> <span data-ttu-id="3b05b-293">Wenn der Schlüssel festgelegt ist und der Wert nicht negativ ist, wird die Entität als markiert `Modified` .</span><span class="sxs-lookup"><span data-stu-id="3b05b-293">If the key is set and the value is non-negative, then the entity is marked as `Modified`.</span></span> <span data-ttu-id="3b05b-294">Wenn jedoch ein negativer Schlüsselwert gefunden wird, wird der tatsächliche, nicht negative Wert wieder hergestellt, und die Entität wird als nachverfolgt `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="3b05b-294">However, if a negative key value is found, then its real, non-negative value is restored and the entity is tracked as `Deleted`.</span></span>

<span data-ttu-id="3b05b-295">Die Ausgabe aus dem Ausführen dieses Codes lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="3b05b-295">The output from running this code is:</span></span>

```output
Tracking Blog with key value 1 as Modified
Tracking Post with key value 1 as Modified
Tracking Post with key value -2 as Deleted
Tracking Post with key value 0 as Added
```

> [!NOTE]
> <span data-ttu-id="3b05b-296">Der Einfachheit halber geht dieser Code davon aus, dass jede Entität über eine ganzzahlige Primärschlüssel `Id` Eigenschaft namens verfügt</span><span class="sxs-lookup"><span data-stu-id="3b05b-296">For simplicity, this code assumes each entity has an integer primary key property called `Id`.</span></span> <span data-ttu-id="3b05b-297">Dies könnte in eine abstrakte Basisklasse oder Schnittstelle cogeschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-297">This could be codified into an abstract base class or interface.</span></span> <span data-ttu-id="3b05b-298">Alternativ können die Primärschlüssel Eigenschaft oder die Eigenschaften aus den Metadaten abgerufen werden <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> , sodass dieser Code mit jedem beliebigen Entitätstyp funktionieren würde.</span><span class="sxs-lookup"><span data-stu-id="3b05b-298">Alternately, the primary key property or properties could be obtained from the <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata such that this code would work with any type of entity.</span></span>

<span data-ttu-id="3b05b-299">Trackgraph verfügt über zwei über Ladungen.</span><span class="sxs-lookup"><span data-stu-id="3b05b-299">TrackGraph has two overloads.</span></span> <span data-ttu-id="3b05b-300">In der oben verwendeten einfachen Überladung bestimmt EF Core, wann das Durchlaufen des Diagramms beendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="3b05b-300">In the simple overload used above, EF Core determines when to stop traversing the graph.</span></span> <span data-ttu-id="3b05b-301">Insbesondere werden neue verknüpfte Entitäten aus einer bestimmten Entität nicht mehr besucht, wenn diese Entität entweder bereits nachverfolgt wurde oder wenn der Rückruf nicht mit der Nachverfolgung der Entität beginnt.</span><span class="sxs-lookup"><span data-stu-id="3b05b-301">Specifically, it stops visiting new related entities from a given entity when that entity is either already tracked, or when the callback does not start tracking the entity.</span></span>

<span data-ttu-id="3b05b-302">Die erweiterte Überladung <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType> verfügt über einen Rückruf, der einen booleschen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="3b05b-302">The advanced overload, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType>, has a callback that returns a bool.</span></span> <span data-ttu-id="3b05b-303">Wenn der Rückruf false zurückgibt, wird der Diagramm Durchlauf beendet, andernfalls wird der Vorgang fortgesetzt.</span><span class="sxs-lookup"><span data-stu-id="3b05b-303">If the callback returns false, then graph traversal stops, otherwise it continues.</span></span> <span data-ttu-id="3b05b-304">Es muss darauf geachtet werden, dass bei Verwendung dieser Überladung unendliche Schleifen vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="3b05b-304">Care must be taken to avoid infinite loops when using this overload.</span></span>

<span data-ttu-id="3b05b-305">Die erweiterte Überladung ermöglicht auch das Bereitstellen des Zustands für trackgraph, und dieser Zustand wird dann an jeden Rückruf übergeben.</span><span class="sxs-lookup"><span data-stu-id="3b05b-305">The advanced overload also allows state to be supplied to TrackGraph and this state is then passed to each callback.</span></span>
