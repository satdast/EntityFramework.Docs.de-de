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
# <a name="using-diagnostic-listeners-in-ef-core"></a><span data-ttu-id="98515-103">Verwenden von diagnoselistener in EF Core</span><span class="sxs-lookup"><span data-stu-id="98515-103">Using Diagnostic Listeners in EF Core</span></span>

> [!TIP]  
> <span data-ttu-id="98515-104">Sie können [das Beispiel dieses Artikels](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) von GitHub herunterladen.</span><span class="sxs-lookup"><span data-stu-id="98515-104">You can [download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) from GitHub.</span></span>

<span data-ttu-id="98515-105">Diagnoselistener ermöglichen das Lauschen auf EF Core Ereignis, das im aktuellen .NET-Prozess auftritt.</span><span class="sxs-lookup"><span data-stu-id="98515-105">Diagnostic listeners allow listening for any EF Core event that occurs in the current .NET process.</span></span> <span data-ttu-id="98515-106">Die- <xref:System.Diagnostics.DiagnosticListener> Klasse ist Teil eines [allgemeinen Mechanismus in .net](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) zum Abrufen von Diagnoseinformationen von ausgelaufenden Anwendungen.</span><span class="sxs-lookup"><span data-stu-id="98515-106">The <xref:System.Diagnostics.DiagnosticListener> class is a part of a [common mechanism across .NET](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) for obtaining diagnostic information from running applications.</span></span>

<span data-ttu-id="98515-107">Diagnoselistener sind nicht geeignet, um Ereignisse aus einer einzelnen dbcontext-Instanz zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="98515-107">Diagnostic listeners are not suitable for getting events from a single DbContext instance.</span></span> <span data-ttu-id="98515-108">EF Core [Interceptors](xref:core/logging-events-diagnostics/interceptors) ermöglichen den Zugriff auf dieselben Ereignisse mit pro-Kontext-Registrierung.</span><span class="sxs-lookup"><span data-stu-id="98515-108">EF Core [interceptors](xref:core/logging-events-diagnostics/interceptors) provide access to the same events with per-context registration.</span></span>

<span data-ttu-id="98515-109">Diagnoselistener sind nicht für die Protokollierung konzipiert.</span><span class="sxs-lookup"><span data-stu-id="98515-109">Diagnostic listeners are not designed for logging.</span></span> <span data-ttu-id="98515-110">Verwenden Sie für die Protokollierung die [einfache Protokollierung](xref:core/logging-events-diagnostics/simple-logging) oder [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging) .</span><span class="sxs-lookup"><span data-stu-id="98515-110">Consider using [simple logging](xref:core/logging-events-diagnostics/simple-logging) or [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) for logging.</span></span>

## <a name="example-observing-diagnostic-events"></a><span data-ttu-id="98515-111">Beispiel: beobachten von Diagnose Ereignissen</span><span class="sxs-lookup"><span data-stu-id="98515-111">Example: Observing diagnostic events</span></span>

<span data-ttu-id="98515-112">Das Auflösen von EF Core Ereignissen ist ein zweistufiger Prozess.</span><span class="sxs-lookup"><span data-stu-id="98515-112">Resolving EF Core events is a two-step process.</span></span> <span data-ttu-id="98515-113">Zuerst muss ein [Beobachter](/dotnet/standard/events/observer-design-pattern) für `DiagnosticListener` sich selbst erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="98515-113">First, an [observer](/dotnet/standard/events/observer-design-pattern) for `DiagnosticListener` itself must be created:</span></span>

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

<span data-ttu-id="98515-114">Die- `OnNext` Methode sucht nach dem diagnosticlistener, der aus EF Core stammt.</span><span class="sxs-lookup"><span data-stu-id="98515-114">The `OnNext` method looks for the DiagnosticListener that comes from EF Core.</span></span> <span data-ttu-id="98515-115">Dieser Listener hat den Namen "Microsoft. entityframeworkcore", der wie gezeigt aus der Klasse abgerufen werden kann <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> .</span><span class="sxs-lookup"><span data-stu-id="98515-115">This listener has the name "Microsoft.EntityFrameworkCore", which can be obtained from the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class as shown.</span></span>

<span data-ttu-id="98515-116">Dieser Beobachter muss dann Global registriert werden, z. b. in der-Methode der Anwendung `Main` :</span><span class="sxs-lookup"><span data-stu-id="98515-116">This observer must then be registered globally, for example in the application's `Main` method:</span></span>

<!--
        DiagnosticListener.AllListeners.Subscribe(new DiagnosticObserver());
-->
[!code-csharp[RegisterDiagnosticListener](../../../samples/core/Miscellaneous/DiagnosticListeners/Program.cs?name=RegisterDiagnosticListener)]

<span data-ttu-id="98515-117">Zweitens wird ein neuer Schlüsselwert Beobachter erstellt, um die eigentlichen EF Core Ereignisse zu abonnieren, sobald der EF Core diagnosticlistener gefunden wurde.</span><span class="sxs-lookup"><span data-stu-id="98515-117">Second, once the EF Core DiagnosticListener is found, a new key-value observer is created to subscribe to the actual EF Core events.</span></span> <span data-ttu-id="98515-118">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="98515-118">For example:</span></span>

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

<span data-ttu-id="98515-119">Die- `OnNext` Methode wird diesmal mit einem Schlüssel-Wert-Paar für jedes EF Core Ereignis aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="98515-119">The `OnNext` method is this time called with a key/value pair for each EF Core event.</span></span> <span data-ttu-id="98515-120">Der Schlüssel ist der Name des Ereignisses, das von einem der folgenden Ergebnisse abgerufen werden kann:</span><span class="sxs-lookup"><span data-stu-id="98515-120">The key is the name of the event, which can be obtained from one of:</span></span>

* <span data-ttu-id="98515-121"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> für Ereignisse, die für alle EF Core Datenbankanbieter üblich sind</span><span class="sxs-lookup"><span data-stu-id="98515-121"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> for events common to all EF Core database providers</span></span>
* <span data-ttu-id="98515-122"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> für Ereignisse, die allen relationalen Datenbankanbietern gemeinsam sind</span><span class="sxs-lookup"><span data-stu-id="98515-122"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> for events common to all relational database providers</span></span>
* <span data-ttu-id="98515-123">Eine ähnliche Klasse für Ereignisse, die für den aktuellen Datenbankanbieter spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="98515-123">A similar class for events specific to the current database provider.</span></span> <span data-ttu-id="98515-124">Beispielsweise <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> für den SQL Server-Anbieter.</span><span class="sxs-lookup"><span data-stu-id="98515-124">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="98515-125">Der Wert des Schlüssel-Wert-Paars ist ein für das ereignisspezifischer Nutz Lasttyp.</span><span class="sxs-lookup"><span data-stu-id="98515-125">The value of the key/value pair is a payload type specific to the event.</span></span> <span data-ttu-id="98515-126">Der Typ der zu erwartenden Nutzlast wird für jedes in diesen Ereignis Klassen definierte Ereignis dokumentiert.</span><span class="sxs-lookup"><span data-stu-id="98515-126">The type of payload to expect is documented on each event defined in these event classes.</span></span>

<span data-ttu-id="98515-127">Der obige Code verarbeitet z <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> . b. das-Ereignis und das- <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> Ereignis.</span><span class="sxs-lookup"><span data-stu-id="98515-127">For example, the code above handles the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ContextInitialized> and the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId.ConnectionOpening> events.</span></span> <span data-ttu-id="98515-128">Für den ersten Wert ist die Nutzlast <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData> .</span><span class="sxs-lookup"><span data-stu-id="98515-128">For the first of these, the payload is <xref:Microsoft.EntityFrameworkCore.Diagnostics.ContextInitializedEventData>.</span></span> <span data-ttu-id="98515-129">Der zweite Wert ist <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData> .</span><span class="sxs-lookup"><span data-stu-id="98515-129">For the second, it is <xref:Microsoft.EntityFrameworkCore.Diagnostics.ConnectionEventData>.</span></span>

> [!TIP]
> <span data-ttu-id="98515-130">ToString wird in jeder EF Core Ereignisdaten Klasse überschrieben, um die entsprechende Protokollmeldung für das Ereignis zu generieren.</span><span class="sxs-lookup"><span data-stu-id="98515-130">ToString is overridden in every EF Core event data class to generate the equivalent log message for the event.</span></span> <span data-ttu-id="98515-131">Durch Aufrufen von wird z `ContextInitializedEventData.ToString` . b. "Entity Framework Core 5.0.0-RC. 2.20475.6 initialisierte ' blogscontext ' mithilfe des Anbieters ' Microsoft. entityframeworkcore. sqlite ' mit den Optionen: None" generiert.</span><span class="sxs-lookup"><span data-stu-id="98515-131">For example, calling `ContextInitializedEventData.ToString` generates "Entity Framework Core 5.0.0-rc.2.20475.6 initialized 'BlogsContext' using provider 'Microsoft.EntityFrameworkCore.Sqlite' with options: None".</span></span>

<span data-ttu-id="98515-132">Das [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) enthält eine einfache Konsolenanwendung, die Änderungen an der Blog Datenbank vornimmt und die gefundenen Diagnose Ereignisse ausgibt.</span><span class="sxs-lookup"><span data-stu-id="98515-132">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/DiagnosticListeners) contains a simple console application that makes changes to the blogging database and prints out the diagnostic events encountered.</span></span>

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

<span data-ttu-id="98515-133">Die Ausgabe dieses Codes zeigt die erkannten Ereignisse:</span><span class="sxs-lookup"><span data-stu-id="98515-133">The output from this code shows the events detected:</span></span>

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
