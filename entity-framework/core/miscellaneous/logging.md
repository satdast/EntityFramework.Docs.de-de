---
title: Protokollierung-EF Core
description: Konfigurieren der Protokollierung mit Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: 56d0e3f8c5ecf56ee6bd6ddf0c9668bb8e3ac12f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617743"
---
# <a name="logging"></a>Protokollierung

> [!TIP]  
> Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) finden Sie auf GitHub.

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

``` csharp
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

``` csharp
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
