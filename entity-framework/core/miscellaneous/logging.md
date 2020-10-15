---
title: Protokollierung-EF Core
description: Konfigurieren der Protokollierung mit Entity Framework Core
author: ajcvickers
ms.date: 10/06/2020
uid: core/miscellaneous/logging
ms.openlocfilehash: 389834b3822aeeaefb8c085538bc6359ccfa7094
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063009"
---
# <a name="logging"></a>Protokollierung

> [!TIP]
> Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) finden Sie auf GitHub.

## <a name="simple-logging"></a>Einfache Protokollierung

> [!NOTE]
> Diese Funktion wurde in EF Core 5,0 hinzugefügt.

Entity Framework Core (EF Core) generiert Protokollmeldungen für Vorgänge wie das Ausführen einer Abfrage oder das Speichern von Änderungen in der Datenbank. Auf diese kann von jedem Anwendungstyp aus mithilfe von [logto](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) zugegriffen werden. <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> beim [Konfigurieren einer dbcontext-Instanz](xref:core/miscellaneous/configuring-dbcontext). Diese Konfiguration erfolgt in der Regel in einer außer Kraft Setzung von <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> . Beispiel:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

Dieses Konzept ähnelt <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.

Weitere Informationen finden Sie unter [einfache Protokollierung](xref:core/miscellaneous/events/simple-logging) .

## <a name="aspnet-core-applications"></a>ASP.NET Core-Anwendungen

EF Core wird automatisch in die Protokollierungsmechanismen von ASP.net Core integriert, wenn `AddDbContext` oder `AddDbContextPool` verwendet wird. Wenn Sie ASP.net Core verwenden, sollte die Protokollierung daher wie in der [ASP.net Core-Dokumentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)beschrieben konfiguriert werden.

## <a name="other-applications"></a>Andere Anwendungen

EF Core Protokollierung erfordert eine iloggerfactory, die selbst mit einem oder mehreren Protokollierungs Anbietern konfiguriert ist. Allgemeine Anbieter werden in den folgenden Paketen ausgeliefert:

* [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): eine einfache Konsolen Protokollierung.
* [Microsoft. Extensions. Logging. azureappservices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): unterstützt Azure-App Services-Funktionen "Diagnoseprotokolle" und "Log Stream".
* [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): protokolliert mithilfe von System. Diagnostics. Debug. Write-ine () einen Debugger-Monitor.
* [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): protokolliert im Windows-Ereignisprotokoll.
* [Microsoft. Extensions. Logging. eventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): unterstützt eventSource/EventListener.
* [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): protokolliert mithilfe von an einen Ablaufverfolgungslistener `System.Diagnostics.TraceSource.TraceEvent()` .

Nachdem Sie die entsprechenden Pakete installiert haben, sollte die Anwendung eine Singleton-/globale Instanz einer loggerfactory erstellen. Beispielsweise mithilfe der Konsolen Protokollierung:

### <a name="version-3x"></a>[Version 3.x](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[Version 2.x](#tab/v2)

> [!NOTE]
> Im folgenden Codebeispiel wird ein `ConsoleLoggerProvider` Konstruktor verwendet, der in Version 2,2 veraltet ist und in 3,0 ersetzt wurde. Es ist sicher, dass die Warnungen bei Verwendung von 2,2 ignoriert und unterdrückt werden.

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

Diese Singleton-/globale Instanz sollte dann bei EF Core auf dem registriert werden `DbContextOptionsBuilder` . Beispiel:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> Es ist sehr wichtig, dass Anwendungen für jede Kontext Instanz keine neue iloggerfactory-Instanz erstellen. Dies führt zu einem Speichermangel und einer unzureichenden Leistung.

## <a name="filtering-what-is-logged"></a>Filtern der protokollierten Elemente

Die Anwendung kann steuern, was protokolliert wird, indem ein Filter für den iloggerprovider konfiguriert wird. Beispiel:

### <a name="version-3x"></a>[Version 3.x](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[Version 2.x](#tab/v2)

> [!NOTE]
> Im folgenden Codebeispiel wird ein `ConsoleLoggerProvider` Konstruktor verwendet, der in Version 2,2 veraltet ist und in 3,0 ersetzt wurde. Es ist sicher, dass die Warnungen bei Verwendung von 2,2 ignoriert und unterdrückt werden.

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[]
    {
        new ConsoleLoggerProvider((category, level)
            => category == DbLoggerCategory.Database.Command.Name
               && level == LogLevel.Information, true)
    });
```

***

In diesem Beispiel wird das Protokoll so gefiltert, dass nur Meldungen zurückgegeben werden:

* in der Kategorie "Microsoft. entityframeworkcore. Database. Command"
* auf der Ebene "Informationen"

Bei EF Core werden Protokollierungs Kategorien in der- `DbLoggerCategory` Klasse definiert, um das Auffinden von Kategorien zu vereinfachen. Diese werden jedoch in einfache Zeichen folgen aufgelöst.

Weitere Informationen zur zugrunde liegenden Protokollierungs Infrastruktur finden Sie in der [Dokumentation zur ASP.net Core Protokollierung](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).
