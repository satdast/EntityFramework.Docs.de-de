---
title: .Net-Ereignisse-EF Core
description: Durch EF Core definierte .net-Ereignisse
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/events
ms.openlocfilehash: 21ee65b7a2c5155c4d5b45350f3f47bdcee22921
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431252"
---
# <a name="net-events-in-ef-core"></a>.Net-Ereignisse in EF Core

> [!TIP]  
> Sie können [das Ereignis Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) von GitHub herunterladen.

Entity Framework Core (EF Core) macht [.net-Ereignisse](/dotnet/standard/events/) verfügbar, die als Rückrufe fungieren, wenn bestimmte Dinge im EF Core Code auftreten. Ereignisse sind einfacher als [Interceptors](xref:core/logging-events-diagnostics/interceptors) und ermöglichen eine flexiblere Registrierung. Sie sind jedoch nur synchron und können daher keine nicht blockierende asynchrone e/a-Vorgänge durchführen.

Ereignisse werden pro `DbContext` Instanz registriert. Verwenden Sie einen [diagnoselistener](xref:core/logging-events-diagnostics/diagnostic-listeners) , um dieselben Informationen, aber für alle dbcontext-Instanzen im Prozess zu erhalten.

## <a name="events-raised-by-ef-core"></a>Ereignisse, die von EF Core ausgelöst werden

Die folgenden Ereignisse werden von EF Core ausgelöst:

| Ereignis | Eingeführt in Version | Wenn ausgelöst
|:------|--------------------|-------
| `DbContext.SavingChanges` <!-- Issue #2748 -->| 5.0 | Am Anfang von <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> oder <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| `DbContext.SavedChanges`  <!-- Issue #2748 -->| 5.0 | Am Ende eines erfolgreichen <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> oder <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| `DbContext.SaveChangesFailed`  <!-- Issue #2748 -->| 5.0 | Am Ende einer fehlgeschlagenen <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> oder <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> | 2.1 | Wenn eine Entität vom Kontext nachverfolgt wird
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> | 2.1 | Wenn eine nach verfolgte Entität ihren Zustand ändert

### <a name="example-timestamp-state-changes"></a>Beispiel: Timestamp-Zustandsänderungen

Jede Entität, die von einem dbcontext nachverfolgt wird, hat eine <xref:Microsoft.EntityFrameworkCore.EntityState> Der-Status gibt beispielsweise an `Added` , dass die Entität in die Datenbank eingefügt wird.

In diesem Beispiel werden das <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked> -Ereignis und das- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged> Ereignis verwendet, um zu erkennen, wann sich ein Anschließend wird die Entität mit der aktuellen Zeitstempel versehen, die anzeigt, wann diese Änderung eingetreten ist. Dies führt zu Zeitstempel, die angeben, wann die Entität eingefügt, gelöscht und/oder zuletzt aktualisiert wurde.

Die Entitäts Typen in diesem Beispiel implementieren eine Schnittstelle, die die Zeitstempel-Eigenschaften definiert:

<!--
public interface IHasTimestamps
{
    DateTime? Added { get; set; }
    DateTime? Deleted { get; set; }
    DateTime? Modified { get; set; }
}
-->
[!code-csharp[IHasTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=IHasTimestamps)]

Eine Methode im dbcontext der Anwendung kann dann Zeitstempel für jede Entität festlegen, die diese Schnittstelle implementiert:

<!--
    private static void UpdateTimestamps(object sender, EntityEntryEventArgs e)
    {
        if (e.Entry.Entity is IHasTimestamps entityWithTimestamps)
        {
            switch (e.Entry.State)
            {
                case EntityState.Deleted:
                    entityWithTimestamps.Deleted = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for delete: {e.Entry.Entity}");
                    break;
                case EntityState.Modified:
                    entityWithTimestamps.Modified = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for update: {e.Entry.Entity}");
                    break;
                case EntityState.Added:
                    entityWithTimestamps.Added = DateTime.UtcNow;
                    Console.WriteLine($"Stamped for insert: {e.Entry.Entity}");
                    break;
            }
        }
    }
-->
[!code-csharp[UpdateTimestamps](../../../samples/core/Miscellaneous/Events/Program.cs?name=UpdateTimestamps)]

Diese Methode verfügt über die entsprechende Signatur, die als Ereignishandler für das-Ereignis und das-Ereignis verwendet werden soll `Tracked` `StateChanged` . Der Handler ist für beide Ereignisse im dbcontext-Konstruktor registriert. Beachten Sie, dass Ereignisse jederzeit an einen dbcontext angefügt werden können. Es ist nicht erforderlich, dass dies im kontextkonstruktor auftritt.

<!--
    public BlogsContext()
    {
        ChangeTracker.StateChanged += UpdateTimestamps;
        ChangeTracker.Tracked += UpdateTimestamps;
    }
-->
[!code-csharp[ContextConstructor](../../../samples/core/Miscellaneous/Events/Program.cs?name=ContextConstructor)]

Beide Ereignisse sind erforderlich, da neue Entitäten Ereignisse auslösen, `Tracked` Wenn Sie zum ersten Mal nachverfolgt werden. `StateChanged` Ereignisse werden nur für Entitäten ausgelöst, die den Zustand ändern, während Sie _bereits_ nachverfolgt werden.

Das [Beispiel für dieses](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Events) Beispiel enthält eine einfache Konsolenanwendung, die Änderungen an der Blog Datenbank vornimmt:

<!--
        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();
            
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Id = 1, Title = "EF Core 3.1!" },
                        new Post { Id = 2, Title = "EF Core 5.0!" }
                    }
                });

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Id = 3, Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
-->
[!code-csharp[Demonstration](../../../samples/core/Miscellaneous/Events/Program.cs?name=Demonstration)]

Die Ausgabe dieses Codes zeigt die Zustandsänderungen und die angewendeten Zeitstempel:

```output
Stamped for insert: Blog 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 1 Added on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 2 Added on: 10/15/2020 11:01:26 PM
Stamped for delete: Post 1 Added on: 10/15/2020 11:01:26 PM Deleted on: 10/15/2020 11:01:26 PM
Stamped for update: Blog 1 Added on: 10/15/2020 11:01:26 PM Modified on: 10/15/2020 11:01:26 PM
Stamped for insert: Post 3 Added on: 10/15/2020 11:01:26 PM
```
