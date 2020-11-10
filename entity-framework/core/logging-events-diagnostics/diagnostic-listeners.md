---
title: Verwenden von diagnoselistener-EF Core
description: Verwenden von diagnosticlistener für den globalen Verbrauch von EF Core Diagnostics
author: ajcvickers
ms.date: 10/16/2020
uid: core/logging-events-diagnostics/diagnostic-listeners
ms.openlocfilehash: dba82a910e2b551e692f37d721d41968981849cf
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431254"
---
# <a name="using-diagnostic-listeners-in-ef-core"></a>Verwenden von diagnoselistener in EF Core

> [!TIP]  
> Sie können [das Beispiel dieses Artikels](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) von GitHub herunterladen.

Diagnoselistener ermöglichen das Lauschen auf EF Core Ereignis, das im aktuellen .NET-Prozess auftritt. Die- <xref:System.Diagnostics.DiagnosticListener> Klasse ist Teil eines [allgemeinen Mechanismus in .net](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) zum Abrufen von Diagnoseinformationen von ausgelaufenden Anwendungen.

Diagnoselistener sind nicht geeignet, um Ereignisse aus einer einzelnen dbcontext-Instanz zu erhalten. EF Core [Interceptors](xref:core/logging-events-diagnostics/interceptors) ermöglichen den Zugriff auf dieselben Ereignisse mit pro-Kontext-Registrierung.

Diagnoselistener sind nicht für die Protokollierung konzipiert. Verwenden Sie für die Protokollierung die [einfache Protokollierung](xref:core/logging-events-diagnostics/simple-logging) oder [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging) .

## <a name="example-observing-diagnostic-events"></a>Beispiel: beobachten von Diagnose Ereignissen

Das Auflösen von EF Core Ereignissen ist ein zweistufiger Prozess. Zuerst muss ein [Beobachter](/dotnet/standard/events/observer-design-pattern) für `DiagnosticListener` sich selbst erstellt werden:

<!--
public class DiagnosticObserver : IObserver<DiagnosticListener>
{
    public void OnCompleted() 
        => throw new NotImplementedException();
    
    public void OnError(Exception error) 
        => throw new NotImplementedException();

    public void OnNext(DiagnosticListener value)
    {
        if (value.Name == DbLoggerCategory.Name) // "Microsoft.EntityFrameworkCore"
        {
            value.Subscribe(new KeyValueObserver());
        }
    }
}
-->
[!code-csharp[DiagnosticObserver](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=DiagnosticObserver)]

Die- `OnNext` Methode sucht nach dem diagnosticlistener, der aus EF Core stammt. Dieser Listener hat den Namen "Microsoft. entityframeworkcore", der wie gezeigt aus der Klasse abgerufen werden kann <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> .

Dieser Beobachter muss dann Global registriert werden, z. b. in der-Methode der Anwendung `Main` :

<!--
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
-->
[!code-csharp[RegisterDiagnosticListener](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=RegisterDiagnosticListener)]

Zweitens wird ein neuer Schlüsselwert Beobachter erstellt, um die eigentlichen EF Core Ereignisse zu abonnieren, sobald der EF Core diagnosticlistener gefunden wurde. Beispiel:

<!--
public class KeyValueObserver : IObserver<KeyValuePair<string, object>>
{
    public void OnCompleted() 
        => throw new NotImplementedException();
    
    public void OnError(Exception error) 
        => throw new NotImplementedException();

    public void OnNext(KeyValuePair<string, object> value)
    {
        if (value.Key == CoreEventId.ContextInitialized.Name)
        {
            var payload = (ContextInitializedEventData)value.Value;
            Console.WriteLine($"EF is initializing {payload.Context.GetType().Name} ");
        }

        if (value.Key == RelationalEventId.ConnectionOpening.Name)
        {
            var payload = (ConnectionEventData)value.Value;
            Console.WriteLine($"EF is opening a connection to {payload.Connection.ConnectionString} ");
        }
    }
}
-->
[!code-csharp[KeyValueObserver](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=KeyValueObserver)]

Die- `OnNext` Methode wird diesmal mit einem Schlüssel-Wert-Paar für jedes EF Core Ereignis aufgerufen. Der Schlüssel ist der Name des Ereignisses, das von einem der folgenden Ergebnisse abgerufen werden kann:

* <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> für Ereignisse, die für alle EF Core Datenbankanbieter üblich sind
* <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> für Ereignisse, die allen relationalen Datenbankanbietern gemeinsam sind
* Eine ähnliche Klasse für Ereignisse, die für den aktuellen Datenbankanbieter spezifisch sind. Beispielsweise <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> für den SQL Server-Anbieter.

Der Wert des Schlüssel-Wert-Paars ist ein für das ereignisspezifischer Nutz Lasttyp. Der Typ der zu erwartenden Nutzlast wird für jedes in diesen Ereignis Klassen definierte Ereignis dokumentiert.

Der obige Code verarbeitet z <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> . b. das-Ereignis und das- <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> Ereignis. Für den ersten Wert ist die Nutzlast <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData> . Der zweite Wert ist <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData> .

> [!TIP]
> ToString wird in jeder EF Core Ereignisdaten Klasse überschrieben, um die entsprechende Protokollmeldung für das Ereignis zu generieren. Durch Aufrufen von wird z `ContextInitializedEventData.ToString` . b. "Entity Framework Core 5.0.0-RC. 2.20475.6 initialisierte ' blogscontext ' mithilfe des Anbieters ' Microsoft. entityframeworkcore. sqlite ' mit den Optionen: None" generiert.

Das [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) enthält eine einfache Konsolenanwendung, die Änderungen an der Blog Datenbank vornimmt und die gefundenen Diagnose Ereignisse ausgibt.

<!--
    public static void Main()
    {
        #region RegisterDiagnosticListener
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
        #endregion
        
        using (var context = new BlogsContext())
        {
            context.Database.EnsureDeleted();
            context.Database.EnsureCreated();
            
            context.Add(
                new Blog
                {
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Title = "EF Core 3.1!" },
                        new Post { Title = "EF Core 5.0!" }
                    }
                });

            context.SaveChanges();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Title = "EF Core 6.0!" });

            context.SaveChanges();
        }
        #endregion
    }
-->
[!code-csharp[Program](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=Program)]

Die Ausgabe dieses Codes zeigt die erkannten Ereignisse:

```output
EF is initializing BlogsContext
EF is opening a connection to Data Source=blogs.db;Mode=ReadOnly
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to Data Source=blogs.db;Mode=ReadOnly
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
EF is initializing BlogsContext
EF is opening a connection to DataSource=blogs.db
EF is opening a connection to DataSource=blogs.db
```
