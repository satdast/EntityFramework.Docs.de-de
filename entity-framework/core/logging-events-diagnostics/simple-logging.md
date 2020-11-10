---
title: Einfache Protokollierung-EF Core
description: Protokollieren von einem EF Core dbcontext mithilfe von logto
author: ajcvickers
ms.date: 10/03/2020
uid: core/logging-events-diagnostics/simple-logging
ms.openlocfilehash: 49619cc10ea098e39e71dde347e00bbc3c39b13a
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431250"
---
# <a name="simple-logging"></a>Einfache Protokollierung

> [!NOTE]
> Dieses Feature wurde in EF Core 5.0 hinzugefügt.

> [!TIP]  
> Sie können [das Beispiel dieses Artikels](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SimpleLogging) von GitHub herunterladen.

Die einfache Protokollierung von Entity Framework Core (EF Core) kann verwendet werden, um beim entwickeln und Debuggen von Anwendungen problemlos Protokolle zu erhalten. Für diese Art der Protokollierung ist eine minimale Konfiguration und keine zusätzliche nuget-Pakete erforderlich.

> [!TIP]
> EF Core auch in [Microsoft. Extensions. Logging](xref:core/logging-events-diagnostics/extensions-logging)integriert, was eine höhere Konfiguration erfordert, aber häufig für die Protokollierung in Produktionsanwendungen geeignet ist.

## <a name="configuration"></a>Konfiguration

Auf EF Core Protokolle können von jedem Anwendungstyp aus mithilfe von [logto](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) zugegriffen werden. <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> beim [Konfigurieren einer DbContext-Instanz](xref:core/dbcontext-configuration/index) möglich. Diese Konfiguration erfolgt in der Regel durch ein Überschreiben von <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>. Beispiel:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

Alternativ `LogTo` kann auch als Teil von oder aufgerufen werden, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> Wenn eine-Instanz erstellt wird <xref:Microsoft.EntityFrameworkCore.DbContextOptions> , die an den-Konstruktor übergeben werden soll `DbContext` .

> [!TIP]
> On-Konfiguration wird immer noch aufgerufen, wenn adddbcontext verwendet wird oder eine dbcontextoptions-Instanz an den dbcontext-Konstruktor übergeben wird. Dies ist der ideale Ort zum Anwenden der Kontext Konfiguration, unabhängig davon, wie der dbcontext erstellt wird.

## <a name="directing-the-logs"></a>Leiten der Protokolle

### <a name="logging-to-the-console"></a>Protokollierung an der Konsole

`LogTo` erfordert einen Delegaten <xref:System.Action%601> , der eine Zeichenfolge akzeptiert. EF Core wird diesen Delegaten mit einer Zeichenfolge für jede generierte Protokollnachricht aufruft. Anschließend wird der Delegat zum Ausführen von etwas mit der angegebenen Meldung angezeigt.

Die- <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> Methode wird häufig für diesen Delegaten verwendet, wie oben gezeigt. Dies führt dazu, dass jede Protokollmeldung in die Konsole geschrieben wird.

### <a name="logging-to-the-debug-window"></a>Protokollierung im Debugfenster

<xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> kann verwendet werden, um die Ausgabe an das Debugfenster in Visual Studio oder anderen IDEs zu senden. Die [Lambda-Syntax](/dotnet/csharp/language-reference/operators/lambda-expressions) muss in diesem Fall verwendet werden, da die- `Debug` Klasse aus Releasebuilds kompiliert wird. Beispiel:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a>Protokollierung in einer Datei

Zum Schreiben in eine Datei muss ein <xref:System.IO.StreamWriter> oder ein ähnliches für die Datei erstellt werden. Die- <xref:System.IO.StreamWriter.WriteLine%2A> Methode kann dann wie in den anderen Beispielen oben verwendet werden. Stellen Sie sicher, dass die Datei ordnungsgemäß geschlossen wird, indem Sie den Writer verwerfen, wenn der Kontext verworfen wird. Beispiel:

<!--
    private readonly StreamWriter _logStream = new StreamWriter("mylog.txt", append: true); 

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(_logStream.WriteLine);

    public override void Dispose()
    {
        base.Dispose();
        _logStream.Dispose();
    }
    
    public override async ValueTask DisposeAsync()
    {
        await base.DisposeAsync();
        await _logStream.DisposeAsync();
    }
-->
[!code-csharp[LogToFile](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToFile)]

> [!TIP]
> Verwenden Sie [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) zum Protokollieren in Dateien in Produktionsanwendungen.

## <a name="getting-detailed-messages"></a>Ausführliche Meldungen werden erhalten.

### <a name="sensitive-data"></a>Sensible Daten

Standardmäßig werden in EF Core die Werte von Daten in Ausnahme Meldungen nicht eingeschlossen. Dies liegt daran, dass solche Daten möglicherweise vertraulich sind und in der Produktionsumgebung verwendet werden können, wenn eine Ausnahme nicht behandelt wird.

Allerdings kann das Wissen von Datenwerten, insbesondere bei Schlüsseln, beim Debuggen sehr hilfreich sein. Dies kann in EF Core aktiviert werden, indem aufgerufen wird <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> . Beispiel:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a>Ausführliche Abfrage Ausnahmen

Aus Leistungsgründen wird von EF Core nicht jeder aufzurufende Befehl zum Lesen eines Werts vom Datenbankanbieter in einem try-catch-Block eingeschlossen. Dies führt jedoch manchmal zu Ausnahmen, die schwer zu diagnostizieren sind, insbesondere dann, wenn die Datenbank einen NULL-Wert zurückgibt, wenn Sie vom Modell nicht zugelassen wird.

Das aktivieren <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> führt dazu, dass EF diese try-catch-Blöcke einführt und somit ausführlichere Fehler bereitstellt. Beispiel:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a>Filtern

### <a name="log-levels"></a>Protokollebenen

Jede EF Core Log-Nachricht wird einer Ebene zugewiesen, die von der-Aufzählung definiert wird <xref:Microsoft.Extensions.Logging.LogLevel> . Standardmäßig enthält EF Core einfache Protokollierung jede Nachricht auf der `Debug` Ebene oder höher. `LogTo` kann ein höheres Mindestwert für das Herausfiltern von Nachrichten weitergegeben werden. Wenn z. b `Information` . die Übergabe von zu einem minimalen Satz von Protokollen durchführt, ist dies auf den Datenbankzugriff und einige in der

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a>Bestimmte Nachrichten

Jeder Protokollmeldung wird eine zugewiesen <xref:Microsoft.Extensions.Logging.EventId> . Auf diese IDs kann von der- <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> Klasse oder der- <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> Klasse für relationale spezifische Nachrichten zugegriffen werden. Ein Datenbankanbieter kann auch anbieterspezifische IDs in einer ähnlichen Klasse aufweisen. Beispielsweise <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> für den SQL Server-Anbieter.

`LogTo` kann so konfiguriert werden, dass nur die einer oder mehreren Ereignis-IDs zugeordneten Nachrichten protokolliert werden. Zum Protokollieren von Meldungen für den Kontext, der initialisiert oder verworfen wird, z. b.:  

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a>Meldungskategorien

Jede Protokollmeldung wird einer benannten hierarchischen Protokollierungs Kategorie zugewiesen. Die Kategorien lauten:

| Category                                             | Meldungen
|:-----------------------------------------------------|-------------------------------------------------
| Microsoft.EntityFrameworkCore                        | Alle EF Core Meldungen
| Microsoft. entityframeworkcore. Database               | Alle Daten Bank Interaktionen
| Microsoft. entityframeworkcore. Database. Connection    | Verwendung einer Datenbankverbindung
| Microsoft. entityframeworkcore. Database. Command       | Verwendung eines Daten Bank Befehls
| Microsoft. entityframeworkcore. Database. Transaction   | Verwendung einer Datenbanktransaktion
| Microsoft. entityframeworkcore. Update                 | Speichern von Entitäten, Ausschließen von Daten Bank Interaktionen
| Microsoft. entityframeworkcore. Model                  | Alle Modell-und metadateninteraktionen
| Microsoft. entityframeworkcore. Model. Validation       | Modellvalidierung
| Microsoft. entityframeworkcore. Query                  | Abfragen, ausgenommen Daten Bank Interaktionen
| Microsoft. entityframeworkcore. Infrastructure         | Allgemeine Ereignisse, z. b. die Erstellung von Kontexten
| Microsoft. entityframeworkcore. Gerüstbau            | Daten Bank Reverse Engineering
| Microsoft. entityframeworkcore. Migrationen             | Migrationen
| Microsoft. entityframeworkcore. changeTracking         | Interaktionen mit Änderungs Nachverfolgung

`LogTo` kann so konfiguriert werden, dass nur die Nachrichten aus einer oder mehreren Kategorien protokolliert werden. Beispielsweise, um nur Daten Bank Interaktionen zu protokollieren:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

Beachten Sie, dass die- <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> Klasse eine hierarchische API zum Suchen einer Kategorie bereitstellt und die Notwendigkeit, Zeichen folgen hart zu codieren, vermeidet.

Da Kategorien hierarchisch sind, beinhaltet dieses Beispiel mit der `Database` Kategorie alle Nachrichten für die Unterkategorien `Database.Connection` , `Database.Command` und `Database.Transaction` .

### <a name="custom-filters"></a>Benutzerdefinierte Filter

`LogTo` ermöglicht die Verwendung eines benutzerdefinierten Filters in Fällen, in denen keine der oben genannten Filteroptionen ausreicht. Zum Protokollieren von Nachrichten auf der Ebene `Information` oder höher sowie Nachrichten zum Öffnen und Schließen einer Verbindung:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(
                Console.WriteLine,
                (eventId, logLevel) => logLevel >= LogLevel.Information
                                       || eventId == RelationalEventId.ConnectionOpened
                                       || eventId == RelationalEventId.ConnectionClosed);
-->
[!code-csharp[CustomFilter](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=CustomFilter)]

> [!TIP]
> Das Filtern mit benutzerdefinierten Filtern oder das Verwenden einer der hier gezeigten anderen Optionen ist effizienter als das Filtern im logto-Delegaten. Dies liegt daran, dass die Protokollmeldung nicht einmal erstellt wird, wenn der Filter bestimmt, dass die Nachricht nicht protokolliert werden soll.

## <a name="configuration-for-specific-messages"></a>Konfiguration für bestimmte Nachrichten

Die EF Core- <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API ermöglicht es Anwendungen, zu ändern, was geschieht, wenn ein bestimmtes Ereignis auftritt. Dies kann für Folgendes verwendet werden:

* Ändern Sie die Protokollebene, bei der das Ereignis protokolliert wird.
* Protokollierung des Ereignisses überspringen
* Löst eine Ausnahme aus, wenn das Ereignis eintritt.

### <a name="changing-the-log-level-for-an-event"></a>Ändern der Protokollebene für ein Ereignis

Im vorherigen Beispiel wurde ein benutzerdefinierter Filter verwendet, um jede Nachricht bei `LogLevel.Information` und zwei für definierte Ereignisse zu protokollieren `LogLevel.Debug` . Dies kann erreicht werden, indem die Protokollebene der beiden `Debug` Ereignisse in geändert wird `Information` :

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Log(
                (RelationalEventId.ConnectionOpened, LogLevel.Information),
                (RelationalEventId.ConnectionClosed, LogLevel.Information)))
            .LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a>Protokollierung eines Ereignisses unterdrücken

Auf ähnliche Weise kann ein einzelnes Ereignis aus der Protokollierung unterdrückt werden. Dies ist besonders nützlich, wenn eine Warnung ignoriert werden soll, die überprüft und verstanden wurde. Beispiel:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a>Auslösen für ein Ereignis

Zum Schluss können EF Core so konfiguriert werden, dass für ein bestimmtes Ereignis ausgelöst wird. Dies ist besonders nützlich, um eine Warnung in einen Fehler zu ändern. (Dies war tatsächlich der ursprüngliche Zweck der- `ConfigureWarnings` Methode, daher der Name.) Zum Beispiel:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Throw(RelationalEventId.MultipleCollectionIncludeWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ThrowForEvent)]

## <a name="message-contents-and-formatting"></a>Nachrichten Inhalt und-Formatierung

Der Standard Inhalt von `LogTo` wird über mehrere Zeilen formatiert. Die erste Zeile enthält Nachrichten Metadaten:

* Das <xref:Microsoft.Extensions.Logging.LogLevel> -Zeichen als vier-Zeichen-Präfix.
* Ein lokaler Zeitstempel, der für die aktuelle Kultur formatiert ist.
* Das <xref:Microsoft.Extensions.Logging.EventId> in der Form, das kopiert/eingefügt werden kann, um den Member aus <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> oder eine der anderen Klassen sowie den unformatierten ID-Wert zu erhalten `EventId` .
* Die Ereignis Kategorie, wie oben beschrieben.

Beispiel:

```output
info: 10/6/2020 10:52:45.581 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 10/6/2020 10:52:45.582 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committing transaction.
dbug: 10/6/2020 10:52:45.585 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committed transaction.
```

Dieser Inhalt kann durch Übergeben von Werten von [dbcontextloggeroptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15) angepasst werden. <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> -->, wie in den folgenden Abschnitten dargestellt.

> [!TIP]
> Verwenden Sie [Microsoft. Extensions. Logging](/aspnet/core/fundamentals/logging) , um die Protokoll Formatierung besser steuern zu können.

### <a name="using-utc-time"></a>Verwenden der UTC-Zeit

Standardmäßig sind timestamnps für den lokalen Verbrauch während des Debuggens konzipiert. Verwenden `DbContextLoggerOptions.DefaultWithUtcTime` Sie stattdessen, um die Kultur unabhängigen UTC-Zeitstempel zu verwenden, aber behalten Sie alle anderen Werte bei. Beispiel:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

In diesem Beispiel wird die folgende Protokoll Formatierung angezeigt:

```output
info: 2020-10-06T17:55:39.0333701Z RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 2020-10-06T17:55:39.0333892Z RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committing transaction.
dbug: 2020-10-06T17:55:39.0351684Z RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committed transaction.
```

### <a name="single-line-logging"></a>Einzeilige Protokollierung

Manchmal ist es hilfreich, genau eine Zeile pro Protokollnachricht zu erhalten. Dies kann durch aktiviert werden `DbContextLoggerOptions.SingleLine` . Beispiel:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

In diesem Beispiel wird die folgende Protokoll Formatierung angezeigt:

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a>Weitere Inhalts Optionen

Andere Flags in [dbcontextloggeroptions](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/Diagnostics/DbContextLoggerOptions.cs#L15) <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> --> kann verwendet werden, um die Menge der im Protokoll enthaltenen Metadaten zu kürzen. Dies kann in Verbindung mit der einzeiligen Protokollierung nützlich sein. Beispiel:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

In diesem Beispiel wird die folgende Protokoll Formatierung angezeigt:

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a>Verschieben von EF6

EF Core einfache Protokollierung unterscheidet <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> sich von in EF6 auf zwei Arten:

* Protokollmeldungen sind nicht auf Daten Bank Interaktionen beschränkt.
* Die Protokollierung muss zum Zeitpunkt der kontextinitialisierung konfiguriert werden.

Für den ersten Unterschied kann die oben beschriebene Filterung verwendet werden, um einzuschränken, welche Nachrichten protokolliert werden.

Der zweite Unterschied ist eine absichtliche Änderung, um die Leistung zu verbessern, indem keine Protokollmeldungen erzeugt werden, wenn Sie nicht benötigt werden Es ist jedoch weiterhin möglich, ein ähnliches Verhalten zu EF6 zu erzielen, indem Sie eine `Log` -Eigenschaft auf Ihrem erstellen `DbContext` und diese dann nur dann verwenden, wenn Sie festgelegt wurde. Beispiel:

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
