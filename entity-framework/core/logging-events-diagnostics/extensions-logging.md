---
title: Verwenden von Microsoft. Extensions. Logging-EF Core
description: Protokollierung von EF Core mithilfe von "Microsoft. Extensions. Logging" in ASP.net Core und anderen Anwendungs Typen
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/extensions-logging
ms.openlocfilehash: 3732bda0ef29b05672a2dfb83405da802f899191
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431253"
---
# <a name="using-microsoftextensionslogging-in-ef-core"></a>Verwenden von Microsoft. Extensions. Logging in EF Core

[Microsoft. Extensions. Logging](/dotnet/core/extensions/logging) ist ein erweiterbarer Protokollierungs Mechanismus mit Plug-in-Anbietern für viele gängige Protokollierungs Systeme. Sowohl von Microsoft bereitgestellte Plug-ins (z. b. [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)) als auch Drittanbieter-Plug-ins (z. b. [serilog. Extensions. Logging](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) sind als nuget-Pakete verfügbar.

Entity Framework Core (EF Core) ist vollständig in integriert `Microsoft.Extensions.Logging` . Sie sollten jedoch die [einfache Protokollierung](xref:core/logging-events-diagnostics/simple-logging) für eine einfachere Methode zur Protokollierung verwenden, insbesondere für Anwendungen, die keine Abhängigkeitsinjektion verwenden.

## <a name="aspnet-core-applications"></a>ASP.NET Core-Anwendungen

`Microsoft.Extensions.Logging` wird [standardmäßig in ASP.net Core Anwendungen verwendet](/aspnet/core/fundamentals/logging). Aufrufen von <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> oder <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> .

## <a name="other-application-types"></a>Andere Anwendungs Typen

Andere Anwendungs Typen können den [generichost](/dotnet/core/extensions/generic-host) verwenden, um die gleichen Abhängigkeits einschleusungs Muster wie in ASP.net Core zu erhalten. Adddbcontext oder adddbcontextpool kann dann wie in ASP.net Core Anwendungen verwendet werden.

`Microsoft.Extensions.Logging` kann auch für Anwendungen verwendet werden, die keine Abhängigkeitsinjektion verwenden, obwohl die [einfache Protokollierung](xref:core/logging-events-diagnostics/simple-logging) einfacher einzurichten ist.

`Microsoft.Extensions.Logging` erfordert das Erstellen eines <xref:Microsoft.Extensions.Logging.LoggerFactory> . Diese Factory sollte als statische/globale Instanz an einem beliebigen Speicherort gespeichert werden und bei jeder Erstellung eines dbcontext verwendet werden. Beispielsweise ist es üblich, die Protokollierungs-Factory als statische Eigenschaft im dbcontext zu speichern.

### <a name="ef-core-30-and-above"></a>[EF Core 3,0 und höher](#tab/v3)

<!--
        public static readonly ILoggerFactory MyLoggerFactory
            = LoggerFactory.Create(builder => { builder.AddConsole(); });
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="ef-core-21"></a>[EF Core 2.1](#tab/v2)

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

> [!WARNING]
> In EF Core 2,1 ist es sehr wichtig, dass Anwendungen für jede dbcontext-Instanz keine neue loggerfactory-Instanz erstellen. Dies führt zu einem Speichermangel und einer unzureichenden Leistung. Dies wurde in EF Core 3,0 und höher behoben.

***

Diese Singleton-/globale Instanz sollte dann bei EF Core auf dem registriert werden <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> . Beispiel:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .UseLoggerFactory(MyLoggerFactory)
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFLogging;ConnectRetryCount=0");
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

## <a name="getting-detailed-messages"></a>Ausführliche Meldungen werden erhalten.

> [!TIP]
> On-Konfiguration wird immer noch aufgerufen, wenn adddbcontext verwendet wird oder eine dbcontextoptions-Instanz an den dbcontext-Konstruktor übergeben wird. Dies ist der ideale Ort zum Anwenden der Kontext Konfiguration, unabhängig davon, wie der dbcontext erstellt wird.

### <a name="sensitive-data"></a>Sensible Daten

Standardmäßig werden in EF Core die Werte von Daten in Ausnahme Meldungen nicht eingeschlossen. Dies liegt daran, dass solche Daten möglicherweise vertraulich sind und in der Produktionsumgebung verwendet werden können, wenn eine Ausnahme nicht behandelt wird.

Allerdings kann das Wissen von Datenwerten, insbesondere bei Schlüsseln, beim Debuggen sehr hilfreich sein. Dies kann in EF Core aktiviert werden, indem aufgerufen wird <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> . Beispiel:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableSensitiveDataLogging();
-->
[!code-csharp[EnableSensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableSensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a>Ausführliche Abfrage Ausnahmen

Aus Leistungsgründen wird von EF Core nicht jeder aufzurufende Befehl zum Lesen eines Werts vom Datenbankanbieter in einem try-catch-Block eingeschlossen. Dies führt jedoch manchmal zu Ausnahmen, die schwer zu diagnostizieren sind, insbesondere dann, wenn die Datenbank einen NULL-Wert zurückgibt, wenn Sie vom Modell nicht zugelassen wird.

Das aktivieren <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> führt dazu, dass EF diese try-catch-Blöcke einführt und somit ausführlichere Fehler bereitstellt. Beispiel:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableDetailedErrors)]

## <a name="configuration-for-specific-messages"></a>Konfiguration für bestimmte Nachrichten

Die EF Core- <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API ermöglicht es Anwendungen, zu ändern, was geschieht, wenn ein bestimmtes Ereignis auftritt. Dies kann für Folgendes verwendet werden:

* Ändern Sie die Protokollebene, bei der das Ereignis protokolliert wird.
* Protokollierung des Ereignisses überspringen
* Löst eine Ausnahme aus, wenn das Ereignis eintritt.

### <a name="changing-the-log-level-for-an-event"></a>Ändern der Protokollebene für ein Ereignis

Manchmal kann es hilfreich sein, die vordefinierte Protokollebene für ein Ereignis zu ändern. Dies kann z. b. verwendet werden, um zwei weitere Ereignisse von auf heraufzustufen `LogLevel.Debug` `LogLevel.Information` :

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Log(
                    (RelationalEventId.ConnectionOpened, LogLevel.Information),
                    (RelationalEventId.ConnectionClosed, LogLevel.Information)));
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a>Protokollierung eines Ereignisses unterdrücken

Auf ähnliche Weise kann ein einzelnes Ereignis aus der Protokollierung unterdrückt werden. Dies ist besonders nützlich, wenn eine Warnung ignoriert werden soll, die überprüft und verstanden wurde. Beispiel:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning));
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a>Auslösen für ein Ereignis

Zum Schluss können EF Core so konfiguriert werden, dass für ein bestimmtes Ereignis ausgelöst wird. Dies ist besonders nützlich, um eine Warnung in einen Fehler zu ändern. (Dies war tatsächlich der ursprüngliche Zweck der- `ConfigureWarnings` Methode, daher der Name.) Zum Beispiel:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Throw(RelationalEventId.QueryPossibleUnintendedUseOfEqualsWarning));
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ThrowForEvent)]

## <a name="filtering-and-other-configuration"></a>Filterung und andere Konfiguration

Anleitungen zur Protokollfilterung und anderen Konfigurationen finden Sie unter [Protokollierung in .net](/dotnet/core/extensions/logging) .

EF Core Protokollierungs Ereignisse werden in einer der folgenden-Ereignisse definiert:

* <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> für Ereignisse, die für alle EF Core Datenbankanbieter üblich sind
* <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> für Ereignisse, die allen relationalen Datenbankanbietern gemeinsam sind
* Eine ähnliche Klasse für Ereignisse, die für den aktuellen Datenbankanbieter spezifisch sind. Beispielsweise <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> für den SQL Server-Anbieter.

Diese Definitionen enthalten die Ereignis-IDs, die Protokollebene und die Kategorie für jedes Ereignis, wie von verwendet `Microsoft.Extensions.Logging` .
