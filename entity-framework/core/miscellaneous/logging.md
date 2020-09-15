---
title: Protokollierung-EF Core
description: Konfigurieren der Protokollierung mit Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: core/miscellaneous/logging
ms.openlocfilehash: 0fd1c83f01989095a813727390179db2327b610d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071666"
---
# <a name="logging"></a><span data-ttu-id="2b943-103">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="2b943-103">Logging</span></span>

> [!TIP]  
> <span data-ttu-id="2b943-104">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="2b943-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="2b943-105">ASP.NET Core-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="2b943-105">ASP.NET Core applications</span></span>

<span data-ttu-id="2b943-106">EF Core wird automatisch in die Protokollierungsmechanismen von ASP.net Core integriert, wenn `AddDbContext` oder `AddDbContextPool` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2b943-106">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="2b943-107">Wenn Sie ASP.net Core verwenden, sollte die Protokollierung daher wie in der [ASP.net Core-Dokumentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)beschrieben konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="2b943-107">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="2b943-108">Andere Anwendungen</span><span class="sxs-lookup"><span data-stu-id="2b943-108">Other applications</span></span>

<span data-ttu-id="2b943-109">EF Core Protokollierung erfordert eine iloggerfactory, die selbst mit einem oder mehreren Protokollierungs Anbietern konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="2b943-109">EF Core logging requires an ILoggerFactory which is itself configured with one or more logging providers.</span></span> <span data-ttu-id="2b943-110">Allgemeine Anbieter werden in den folgenden Paketen ausgeliefert:</span><span class="sxs-lookup"><span data-stu-id="2b943-110">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="2b943-111">[Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): eine einfache Konsolen Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="2b943-111">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="2b943-112">[Microsoft. Extensions. Logging. azureappservices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): unterstützt Azure-App Services-Funktionen "Diagnoseprotokolle" und "Log Stream".</span><span class="sxs-lookup"><span data-stu-id="2b943-112">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="2b943-113">[Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): protokolliert mithilfe von System. Diagnostics. Debug. Write-ine () einen Debugger-Monitor.</span><span class="sxs-lookup"><span data-stu-id="2b943-113">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="2b943-114">[Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): protokolliert im Windows-Ereignisprotokoll.</span><span class="sxs-lookup"><span data-stu-id="2b943-114">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="2b943-115">[Microsoft. Extensions. Logging. eventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): unterstützt eventSource/EventListener.</span><span class="sxs-lookup"><span data-stu-id="2b943-115">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="2b943-116">[Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): protokolliert mithilfe von an einen Ablaufverfolgungslistener `System.Diagnostics.TraceSource.TraceEvent()` .</span><span class="sxs-lookup"><span data-stu-id="2b943-116">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using `System.Diagnostics.TraceSource.TraceEvent()`.</span></span>

<span data-ttu-id="2b943-117">Nachdem Sie die entsprechenden Pakete installiert haben, sollte die Anwendung eine Singleton-/globale Instanz einer loggerfactory erstellen.</span><span class="sxs-lookup"><span data-stu-id="2b943-117">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="2b943-118">Beispielsweise mithilfe der Konsolen Protokollierung:</span><span class="sxs-lookup"><span data-stu-id="2b943-118">For example, using the console logger:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="2b943-119">Version 3.x</span><span class="sxs-lookup"><span data-stu-id="2b943-119">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="2b943-120">Version 2.x</span><span class="sxs-lookup"><span data-stu-id="2b943-120">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="2b943-121">Im folgenden Codebeispiel wird ein `ConsoleLoggerProvider` Konstruktor verwendet, der in Version 2,2 veraltet ist und in 3,0 ersetzt wurde.</span><span class="sxs-lookup"><span data-stu-id="2b943-121">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="2b943-122">Es ist sicher, dass die Warnungen bei Verwendung von 2,2 ignoriert und unterdrückt werden.</span><span class="sxs-lookup"><span data-stu-id="2b943-122">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

<span data-ttu-id="2b943-123">Diese Singleton-/globale Instanz sollte dann bei EF Core auf dem registriert werden `DbContextOptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2b943-123">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="2b943-124">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2b943-124">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="2b943-125">Es ist sehr wichtig, dass Anwendungen für jede Kontext Instanz keine neue iloggerfactory-Instanz erstellen.</span><span class="sxs-lookup"><span data-stu-id="2b943-125">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="2b943-126">Dies führt zu einem Speichermangel und einer unzureichenden Leistung.</span><span class="sxs-lookup"><span data-stu-id="2b943-126">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="2b943-127">Filtern der protokollierten Elemente</span><span class="sxs-lookup"><span data-stu-id="2b943-127">Filtering what is logged</span></span>

<span data-ttu-id="2b943-128">Die Anwendung kann steuern, was protokolliert wird, indem ein Filter für den iloggerprovider konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="2b943-128">The application can control what is logged by configuring a filter on the ILoggerProvider.</span></span> <span data-ttu-id="2b943-129">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2b943-129">For example:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="2b943-130">Version 3.x</span><span class="sxs-lookup"><span data-stu-id="2b943-130">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="2b943-131">Version 2.x</span><span class="sxs-lookup"><span data-stu-id="2b943-131">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="2b943-132">Im folgenden Codebeispiel wird ein `ConsoleLoggerProvider` Konstruktor verwendet, der in Version 2,2 veraltet ist und in 3,0 ersetzt wurde.</span><span class="sxs-lookup"><span data-stu-id="2b943-132">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="2b943-133">Es ist sicher, dass die Warnungen bei Verwendung von 2,2 ignoriert und unterdrückt werden.</span><span class="sxs-lookup"><span data-stu-id="2b943-133">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

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

<span data-ttu-id="2b943-134">In diesem Beispiel wird das Protokoll so gefiltert, dass nur Meldungen zurückgegeben werden:</span><span class="sxs-lookup"><span data-stu-id="2b943-134">In this example, the log is filtered to only return messages:</span></span>

* <span data-ttu-id="2b943-135">in der Kategorie "Microsoft. entityframeworkcore. Database. Command"</span><span class="sxs-lookup"><span data-stu-id="2b943-135">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
* <span data-ttu-id="2b943-136">auf der Ebene "Informationen"</span><span class="sxs-lookup"><span data-stu-id="2b943-136">at the 'Information' level</span></span>

<span data-ttu-id="2b943-137">Bei EF Core werden Protokollierungs Kategorien in der- `DbLoggerCategory` Klasse definiert, um das Auffinden von Kategorien zu vereinfachen. Diese werden jedoch in einfache Zeichen folgen aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="2b943-137">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="2b943-138">Weitere Informationen zur zugrunde liegenden Protokollierungs Infrastruktur finden Sie in der [Dokumentation zur ASP.net Core Protokollierung](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span><span class="sxs-lookup"><span data-stu-id="2b943-138">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
