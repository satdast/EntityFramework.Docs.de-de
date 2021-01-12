---
title: Änderungs Erkennung und Benachrichtigungen-EF Core
description: Erkennen von Eigenschafts-und Beziehungs Änderungen mithilfe von DetectChanges oder Benachrichtigungen
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/change-detection
ms.openlocfilehash: 39dc66a3ba74be89d3e470cfe788a357401965d1
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129592"
---
# <a name="change-detection-and-notifications"></a>Änderungs Erkennung und Benachrichtigungen

Jede <xref:Microsoft.EntityFrameworkCore.DbContext> Instanz verfolgt Änderungen, die an Entitäten vorgenommen wurden. Diese überwachten Entitäten Steuern wiederum die Änderungen an der Datenbank, wenn <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aufgerufen wird. Dies wird in [Änderungsnachverfolgung in EF Core](xref:core/change-tracking/index)behandelt. in diesem Dokument wird davon ausgegangen, dass Entitäts Zustände und Grundlagen der Entity Framework Core (EF Core)-Änderungs Nachverfolgung verstanden werden.

Für das Nachverfolgen von Eigenschaften-und Beziehungs Änderungen ist es erforderlich, dass dbcontext diese Änderungen erkennen kann. In diesem Dokument wird erläutert, wie diese Erkennung erfolgt. Außerdem wird erläutert, wie Sie Eigenschafts Benachrichtigungen oder Proxys für die Änderungs Nachverfolgung verwenden können, um eine sofortige Erkennung von Änderungen

> [!TIP]
> Sie können den gesamten Code in diesem Dokument ausführen und Debuggen, indem Sie [den Beispielcode von GitHub herunterladen](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeDetectionAndNotifications).

## <a name="snapshot-change-tracking"></a>Änderungsnachverfolgung für Momentaufnahmen

Standardmäßig erstellt EF Core eine Momentaufnahme der Eigenschaftswerte jeder Entität, wenn Sie zuerst von einer dbcontext-Instanz nachverfolgt wird. Die in dieser Momentaufnahme gespeicherten Werte werden dann mit den aktuellen Werten der Entität verglichen, um zu bestimmen, welche Eigenschaftswerte geändert wurden.

Diese Erkennung von Änderungen erfolgt, wenn SaveChanges aufgerufen wird, um sicherzustellen, dass alle geänderten Werte erkannt werden, bevor Updates an die Datenbank gesendet werden. Die Erkennung von Änderungen wird jedoch auch zu anderen Zeiten durchgeführt, um sicherzustellen, dass die Anwendung mit aktuellen Überwachungsinformationen arbeitet. Die Erkennung von Änderungen kann jederzeit durch Aufrufen von erzwungen werden <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> .

### <a name="when-change-detection-is-needed"></a>Wenn die Änderungs Erkennung erforderlich ist

Die Erkennung von Änderungen wird benötigt, wenn eine Eigenschaft oder Navigation geändert wurde _, ohne EF Core zu verwenden, um diese Änderung vorzunehmen_. Sie können z. b. Blogs und Beiträge laden und dann Änderungen an diesen Entitäten vornehmen:

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        blog.Name = ".NET Blog (Updated!)";

        // Add a new entity to a navigation
        blog.Posts.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many..."
        });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_1)]

Wenn Sie die [Debugansicht der Änderungs](xref:core/change-tracking/debug-views) Nachverfolgung vor dem Aufruf von <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> anzeigen, wird angezeigt, dass die vorgenommenen Änderungen nicht erkannt wurden und daher nicht in den Entitäts Zuständen und geänderten Eigenschaften Daten widergespiegelt werden:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, <not found>]
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

Insbesondere ist der Status des Blogeintrags immer noch `Unchanged` , und der neue Beitrag wird nicht als nach verfolgte Entität angezeigt. (Die aKlugen wird bemerken, dass Eigenschaften ihre neuen Werte melden, auch wenn diese Änderungen von EF Core noch nicht erkannt wurden. Dies liegt daran, dass in der Debugansicht aktuelle Werte direkt aus der Entitäts Instanz gelesen werden.)

Vergleichen Sie dies mit der Debugansicht nach dem Aufrufen von "DetectChanges":

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
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
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

Der Blog ist nun ordnungsgemäß als gekennzeichnet, `Modified` und der neue Beitrag wurde erkannt und nachverfolgt `Added` .

Zu Beginn dieses Abschnitts haben wir festgestellt, dass das Erkennen von Änderungen erforderlich ist, wenn nicht _die Verwendung von EF Core zum vornehmen der Änderung_ verwendet wird. Dies geschieht im obigen Code. Das heißt, die Änderungen an der Eigenschaft und der Navigation werden _direkt auf den Entitäts Instanzen_ vorgenommen, nicht mithilfe von EF Core Methoden.

Vergleichen Sie dies mit dem folgenden Code, der die Entitäten auf die gleiche Weise ändert, aber dieses Mal EF Core Methoden verwendet:

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        context.Entry(blog).Property(e => e.Name).CurrentValue = ".NET Blog (Updated!)";

        // Add a new entity to the DbContext
        context.Add(
            new Post
            {
                Blog = blog,
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_2)]

In diesem Fall zeigt die Debugansicht für die Änderungs Nachverfolgung an, dass alle Entitäts Zustände und Eigenschafts Änderungen bekannt sind, auch wenn keine Änderungen vorgenommen wurden. Dies liegt daran <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType> , dass eine EF Core Methode ist, was bedeutet, dass EF Core sofort die von dieser Methode vorgenommene Änderung kennt. Ebenso <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> ermöglicht das Aufrufen von EF Core, sofort über die neue Entität zu informieren und Sie entsprechend zu verfolgen.

> [!TIP]
> Versuchen Sie nicht, Änderungen zu erkennen, indem Sie immer EF Core-Methoden verwenden, um Entitäts Änderungen vorzunehmen. Dies ist oft schwieriger und führt zu einer geringeren Leistung, als Änderungen an Entitäten auf normale Weise vorzunehmen. Das Ziel dieses Dokuments besteht darin, zu informieren, wann Änderungen erforderlich sind und wann dies nicht der Fall ist. Die Absicht besteht nicht darin, die Vermeidung von Änderungs Erkennung zu fördern.

### <a name="methods-that-automatically-detect-changes"></a>Methoden zum automatischen Erkennen von Änderungen

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> wird automatisch von Methoden aufgerufen, bei denen sich dies wahrscheinlich auf die Ergebnisse auswirkt. Diese Methoden werden im Anschluss beschrieben:

- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType> , um sicherzustellen, dass alle Änderungen vor dem Aktualisieren der Datenbank erkannt werden.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> und <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> , um sicherzustellen, dass Entitäts Zustände und geänderte Eigenschaften auf dem neuesten Stand sind.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>, um sicherzustellen, dass das Ergebnis korrekt ist.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>, um korrekte Entitäts Zustände für Prinzipal-/Parent-Entitäten vor dem kaskadieren sicherzustellen.
- <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>, um sicherzustellen, dass das nach verfolgte Diagramm auf dem neuesten Stand ist.

Es gibt auch einige stellen, an denen die Ermittlung von Änderungen nur auf einer einzelnen Entitäts Instanz statt auf dem gesamten Diagramm der nach verfolgten Entitäten erfolgt. Diese Stellen sind:

- Bei Verwendung <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> von, um sicherzustellen, dass der Zustand der Entität und die geänderten Eigenschaften auf dem neuesten Stand sind.
- Wenn Sie <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> Methoden wie `Property` , `Collection` oder verwenden `Reference` `Member` , um zu gewährleisten, dass Eigenschafts Änderungen, aktuelle Werte usw. auf dem neuesten Stand sind.
- Wenn eine abhängige/untergeordnete Entität gelöscht wird, weil eine erforderliche Beziehung getrennt wurde. Dadurch wird erkannt, wenn eine Entität nicht gelöscht werden soll, weil Sie erneut übergeordnet ist.

Die lokale Erkennung von Änderungen für eine einzelne Entität kann explizit durch Aufrufen von ausgelöst werden <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> .

> [!NOTE]
> Lokale Erkennungs Änderungen können einige Änderungen übersehen, die durch eine vollständige Erkennung gefunden werden. Dies geschieht, wenn kaskadierende Aktionen, die sich aus nicht erkannten Änderungen an anderen Entitäten ergeben, Auswirkungen auf die betreffende Entität haben. In solchen Fällen muss die Anwendung möglicherweise eine vollständige Überprüfung aller Entitäten erzwingen, indem explizit aufgerufen wird <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> .

### <a name="disabling-automatic-change-detection"></a>Deaktivieren der automatischen Änderungs Erkennung

Die Leistung beim Erkennen von Änderungen ist für die meisten Anwendungen kein Engpass. Das Erkennen von Änderungen kann jedoch zu einem Leistungsproblem für einige Anwendungen werden, die Tausende von Entitäten nachverfolgen. (Die genaue Anzahl hängt von vielen Faktoren ab, z. b. von der Anzahl der Eigenschaften in der Entität.) Aus diesem Grund kann die automatische Erkennung von Änderungen mithilfe von deaktiviert werden <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType> . Sie sollten z. b. Verknüpfungs Entitäten in einer m:n-Beziehung mit Nutzlasten verarbeiten:

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>()) // Detects changes automatically
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                    entityEntry.Entity.TaggedOn = DateTime.Now;
                }
            }

            try
            {
                ChangeTracker.AutoDetectChangesEnabled = false;
                return base.SaveChanges(); // Avoid automatically detecting changes again here
            }
            finally
            {
                ChangeTracker.AutoDetectChangesEnabled = true;
            }
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=SaveChanges)]

Wie wir im vorherigen Abschnitt wissen, erkennen sowohl als <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> auch <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> automatisch Änderungen. Nach dem Aufrufen von Einträgen führt der Code jedoch keine Änderungen an Entitäts-oder Eigenschafts Zuständen aus. (Das Festlegen normaler Eigenschaftswerte für hinzugefügte Entitäten führt nicht zu Zustandsänderungen.) Der Code deaktiviert daher unnötige automatische Änderungs Erkennung beim Aufrufen der Basis-SaveChanges-Methode. Der Code verwendet auch einen try/after-Block, um sicherzustellen, dass die Standardeinstellung wieder hergestellt wird, auch wenn SaveChanges fehlschlägt.

> [!TIP]
> Gehen Sie nicht davon aus, dass der Code die automatische Änderungs Erkennung in deaktivieren muss, damit Sie gut funktioniert. Dies ist nur erforderlich, wenn die Profilerstellung für eine Anwendung, bei der viele Entitäten nachverfolgt werden, ein Problem auftritt.

### <a name="detecting-changes-and-value-conversions"></a>Erkennen von Änderungen und Wert Konvertierungen

Um die Nachverfolgung von Momentaufnahmen mit einem Entitätstyp zu verwenden, muss EF Core in der Lage sein,

- Erstellen Sie eine Momentaufnahme jedes Eigenschafts Werts, wenn die Entität nachverfolgt wird.
- Vergleicht diesen Wert mit dem aktuellen Wert der Eigenschaft.
- Generieren Sie einen Hashcode für den Wert.

Dies erfolgt automatisch durch EF Core für Typen, die direkt der Datenbank zugeordnet werden können. Wenn jedoch ein [Wert Konverter zum Zuordnen einer Eigenschaft verwendet wird](xref:core/modeling/value-conversions), muss dieser Konverter angeben, wie diese Aktionen durchgeführt werden sollen. Dies wird mit einem Wert Vergleich erreicht und in der Dokumentation zu den [Wert comparern](xref:core/modeling/value-comparers) ausführlich beschrieben.

## <a name="notification-entities"></a>Benachrichtigungs Entitäten

Die Änderungs Nachverfolgung von Momentaufnahmen wird für die meisten Anwendungen empfohlen. Allerdings können Anwendungen, die viele Entitäten nachverfolgen und/oder viele Änderungen an diesen Entitäten vornehmen, von der Implementierung von Entitäten profitieren, die EF Core automatisch benachrichtigen, wenn sich ihre Eigenschaften und Navigations Werte ändern Diese werden als "Benachrichtigungs Entitäten" bezeichnet.

### <a name="implementing-notification-entities"></a>Implementieren von Benachrichtigungs Einheiten

Benachrichtigungs Entitäten verwenden die <xref:System.ComponentModel.INotifyPropertyChanging> -und- <xref:System.ComponentModel.INotifyPropertyChanged> Schnittstellen, die Teil der .net-Basisklassen Bibliothek (Base Class Library, BCL) sind. Diese Schnittstellen definieren Ereignisse, die vor und nach dem Ändern eines Eigenschafts Werts ausgelöst werden müssen. Beispiel:

<!--
    public class Blog : INotifyPropertyChanging, INotifyPropertyChanged
    {
        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private int _id;
        public int Id
        {
            get => _id;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Id)));
                _id = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Id)));
            }
        }

        private string _name;
        public string Name
        {
            get => _name;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Name)));
                _name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Name)));
            }
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationEntitiesSamples.cs?name=Model)]

Darüber hinaus muss jede Auflistungs Navigation implementieren `INotifyCollectionChanged` . im obigen Beispiel wird dies mithilfe eines <xref:System.Collections.ObjectModel.ObservableCollection%601> von Beiträgen erfüllt. EF Core ist auch mit einer <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> Implementierung ausgeliefert, die auf Kosten einer stabilen Reihenfolge effizientere Suchvorgänge bietet.

Der größte Teil dieses Benachrichtigungs Codes wird in der Regel in eine nicht zugeordnete Basisklasse verschoben. Beispiel:

<!--
    public class Blog : NotifyingEntity
    {
        private int _id;
        public int Id
        {
            get => _id;
            set => SetWithNotify(value, out _id);
        }

        private string _name;
        public string Name
        {
            get => _name;
            set => SetWithNotify(value, out _name);
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public abstract class NotifyingEntity : INotifyPropertyChanging, INotifyPropertyChanged
    {
        protected void SetWithNotify<T>(T value, out T field, [CallerMemberName] string propertyName = "")
        {
            NotifyChanging(propertyName);
            field = value;
            NotifyChanged(propertyName);
        }

        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private void NotifyChanged(string propertyName)
            => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));

        private void NotifyChanging(string propertyName)
            => PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(propertyName));
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Model)]

### <a name="configuring-notification-entities"></a>Benachrichtigungs Entitäten konfigurieren

Es gibt keine Möglichkeit, EF Core zu überprüfen, ob `INotifyPropertyChanging` oder `INotifyPropertyChanged` vollständig für die Verwendung mit EF Core implementiert sind. Insbesondere verwenden einige Verwendungsmöglichkeiten dieser Schnittstellen dies nur für bestimmte Eigenschaften, nicht für alle Eigenschaften (einschließlich Navigationen), wie dies für EF Core erforderlich ist. Aus diesem Grund wird EF Core nicht automatisch in diese Ereignisse eingebunden.

Stattdessen müssen EF Core so konfiguriert werden, dass diese Benachrichtigungs Entitäten verwendet werden. Dies erfolgt in der Regel für alle Entitäts Typen durch Aufrufen von <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> . Beispiel:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.HasChangeTrackingStrategy(ChangeTrackingStrategy.ChangingAndChangedNotifications);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=OnModelCreating)]

(Die Strategie kann auch unterschiedlich für verschiedene Entitäts Typen festgelegt werden <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> . Dies ist jedoch in der Regel kontraproduktiv, da DetectChanges für die Typen, die keine Benachrichtigungs Entitäten sind, weiterhin erforderlich ist.)

Bei der vollständigen Benachrichtigungs Änderungs Nachverfolgung müssen sowohl `INotifyPropertyChanging` als auch `INotifyPropertyChanged` implementiert werden. Dies ermöglicht die Speicherung ursprünglicher Werte, kurz bevor der Eigenschafts Wert geändert wird, sodass EF Core keine Momentaufnahme erstellen muss, wenn die Entität nachverfolgt wird. Entitäts Typen, die nur implementieren, `INotifyPropertyChanged` können auch mit EF Core verwendet werden. In diesem Fall erstellt EF immer noch eine Momentaufnahme, wenn eine Entität nachverfolgt wird, um die ursprünglichen Werte nachzuverfolgen. die Benachrichtigungen werden dann jedoch verwendet, um Änderungen sofort zu erkennen, anstatt dass DetectChanges aufgerufen werden müssen.

Die unterschiedlichen <xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy> Werte werden in der folgenden Tabelle zusammengefasst.

| Changetrackingstrategy                              | Benötigte Schnittstellen                                      | DetectChanges erforderlich | Momentaufnahmen ursprünglicher Werte
|:----------------------------------------------------|--------------------------------------------------------|---------------------|--------------------------
| Momentaufnahme                                            | Keine                                                   | Ja                 | Ja
| Changedbenachrichtigungen                                | INotifyPropertyChanged                                 | Nein                  | Ja
| Changingandchangedbenachrichtigungen                     | INotifyPropertyChanged und INotifyPropertyChanging     | Nein                  | Nein
| Changingandchangednotificationswithoriginalvalues   | INotifyPropertyChanged und INotifyPropertyChanging     | Nein                  | Ja

### <a name="using-notification-entities"></a>Mit Benachrichtigungs Entitäten

Benachrichtigungs Entitäten Verhalten sich wie alle anderen Entitäten, mit dem Unterschied, dass das vornehmen von Änderungen an den Entitäts Instanzen keinen <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> Rückruf von erfordert Beispiel:

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Notification_entities_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Notification_entities_2)]

Bei normalen Entitäten zeigte die [Debugansicht der Änderungs nach](xref:core/change-tracking/debug-views) Verfolgung, dass diese Änderungen erst erkannt wurden, wenn DetectChanges aufgerufen wurde. Wenn Sie die Debugansicht anzeigen, wenn Benachrichtigungs Entitäten verwendet werden, zeigt dies, dass diese Änderungen sofort erkannt wurden:

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
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
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

## <a name="change-tracking-proxies"></a>Proxys zur Änderungsnachverfolgung

> [!NOTE]
> Proxys für die Änderungs Nachverfolgung wurden in EF Core 5,0 eingeführt.

EF Core können Proxy Typen, die und implementieren, dynamisch generieren <xref:System.ComponentModel.INotifyPropertyChanging> <xref:System.ComponentModel.INotifyPropertyChanged> . Hierfür müssen Sie das nuget-Paket [Microsoft. entityframeworkcore. Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) installieren und Proxys für die Änderungs Nachverfolgung wie folgt aktivieren <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> :

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.UseChangeTrackingProxies();
-->
[!code-csharp[OnConfiguring](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=OnConfiguring)]

Das Erstellen eines dynamischen Proxys umfasst das Erstellen eines neuen dynamischen .net-Typs (mit der Implementierung von " [Castle. Core](https://www.nuget.org/packages/Castle.Core/) Proxies"), der vom Entitätstyp erbt und dann alle Eigenschaften Setter überschreibt. Entitäts Typen für Proxys müssen daher Typen sein, die von geerbt werden können und über Eigenschaften verfügen müssen, die überschrieben werden können. Außerdem müssen die explizit erstellten Sammlungs Navigation Beispiels <xref:System.Collections.Specialized.INotifyCollectionChanged> Weise Folgendes implementieren:

<!--
    public class Blog
    {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }

        public virtual IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public class Post
    {
        public virtual int Id { get; set; }
        public virtual string Title { get; set; }
        public virtual string Content { get; set; }

        public virtual int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Model)]

Ein wesentlicher Nachteil von Proxys für die Änderungs Nachverfolgung besteht darin, dass EF Core immer Instanzen des Proxys nachverfolgen muss, niemals Instanzen des zugrunde liegenden Entitäts Typs. Dies liegt daran, dass Instanzen des zugrunde liegenden Entitäts Typs keine Benachrichtigungen generieren, was bedeutet, dass an diesen Entitäten vorgenommene Änderungen übersehen werden.

EF Core beim Abfragen der Datenbank automatisch Proxy Instanzen erstellt, ist dieser Nachteil in der Regel auf das Nachverfolgen neuer Entitäts Instanzen beschränkt. Diese Instanzen müssen mithilfe der <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> Erweiterungs Methoden und **nicht** auf die normale Weise mithilfe von erstellt werden `new` . Dies bedeutet, dass der Code aus den vorherigen Beispielen nun Folgendes verwenden muss `CreateProxy` :

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(
                context.CreateProxy<Post>(
                    p =>
                        {
                            p.Title = "What’s next for System.Text.Json?";
                            p.Content = ".NET 5.0 was released recently and has come with many...";
                        }));

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracking_proxies_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Change_tracking_proxies_1)]

## <a name="change-tracking-events"></a>Änderungs nach Verfolgungs Ereignisse

EF Core löst das- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> Ereignis aus, wenn eine Entität zum ersten Mal nachverfolgt wird. Zukünftige Entitäts Zustandsänderungen führen zu <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> Ereignissen. Weitere Informationen finden Sie unter [.NET-Ereignisse in EF Core](xref:core/logging-events-diagnostics/events).

> [!NOTE]
> Das- `StateChanged` Ereignis wird nicht ausgelöst, wenn eine Entität zum ersten Mal nachverfolgt wird, auch wenn sich der Status von `Detached` in einen der anderen Zustände geändert hat. Achten Sie darauf, sowohl auf `StateChanged` -als auch auf `Tracked` Ereignisse zu lauschen, um alle relevanten Benachrichtigungen zu erhalten
