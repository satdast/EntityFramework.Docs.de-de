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
# <a name="logging"></a><span data-ttu-id="12a53-103">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="12a53-103">Logging</span></span>

> [!TIP]
> <span data-ttu-id="12a53-104">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="12a53-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="simple-logging"></a><span data-ttu-id="12a53-105">Einfache Protokollierung</span><span class="sxs-lookup"><span data-stu-id="12a53-105">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="12a53-106">Diese Funktion wurde in EF Core 5,0 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="12a53-106">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="12a53-107">Entity Framework Core (EF Core) generiert Protokollmeldungen für Vorgänge wie das Ausführen einer Abfrage oder das Speichern von Änderungen in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="12a53-107">Entity Framework Core (EF Core) generates log messages for operations such as executing a query or saving changes to the database.</span></span> <span data-ttu-id="12a53-108">Auf diese kann von jedem Anwendungstyp aus mithilfe von [logto](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="12a53-108">These can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="12a53-109">beim [Konfigurieren einer dbcontext-Instanz](xref:core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="12a53-109">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="12a53-110">Diese Konfiguration erfolgt in der Regel in einer außer Kraft Setzung von <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="12a53-110">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="12a53-111">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="12a53-111">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="12a53-112">Dieses Konzept ähnelt <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span><span class="sxs-lookup"><span data-stu-id="12a53-112">This concept is similar to <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span></span>

<span data-ttu-id="12a53-113">Weitere Informationen finden Sie unter [einfache Protokollierung](xref:core/miscellaneous/events/simple-logging) .</span><span class="sxs-lookup"><span data-stu-id="12a53-113">See [Simple Logging](xref:core/miscellaneous/events/simple-logging) for more information.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="12a53-114">ASP.NET Core-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="12a53-114">ASP.NET Core applications</span></span>

<span data-ttu-id="12a53-115">EF Core wird automatisch in die Protokollierungsmechanismen von ASP.net Core integriert, wenn `AddDbContext` oder `AddDbContextPool` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="12a53-115">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="12a53-116">Wenn Sie ASP.net Core verwenden, sollte die Protokollierung daher wie in der [ASP.net Core-Dokumentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)beschrieben konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="12a53-116">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="12a53-117">Andere Anwendungen</span><span class="sxs-lookup"><span data-stu-id="12a53-117">Other applications</span></span>

<span data-ttu-id="12a53-118">EF Core Protokollierung erfordert eine iloggerfactory, die selbst mit einem oder mehreren Protokollierungs Anbietern konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="12a53-118">EF Core logging requires an ILoggerFactory which is itself configured with one or more logging providers.</span></span> <span data-ttu-id="12a53-119">Allgemeine Anbieter werden in den folgenden Paketen ausgeliefert:</span><span class="sxs-lookup"><span data-stu-id="12a53-119">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="12a53-120">[Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): eine einfache Konsolen Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="12a53-120">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="12a53-121">[Microsoft. Extensions. Logging. azureappservices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): unterstützt Azure-App Services-Funktionen "Diagnoseprotokolle" und "Log Stream".</span><span class="sxs-lookup"><span data-stu-id="12a53-121">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="12a53-122">[Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): protokolliert mithilfe von System. Diagnostics. Debug. Write-ine () einen Debugger-Monitor.</span><span class="sxs-lookup"><span data-stu-id="12a53-122">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="12a53-123">[Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): protokolliert im Windows-Ereignisprotokoll.</span><span class="sxs-lookup"><span data-stu-id="12a53-123">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="12a53-124">[Microsoft. Extensions. Logging. eventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): unterstützt eventSource/EventListener.</span><span class="sxs-lookup"><span data-stu-id="12a53-124">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="12a53-125">[Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): protokolliert mithilfe von an einen Ablaufverfolgungslistener `System.Diagnostics.TraceSource.TraceEvent()` .</span><span class="sxs-lookup"><span data-stu-id="12a53-125">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using `System.Diagnostics.TraceSource.TraceEvent()`.</span></span>

<span data-ttu-id="12a53-126">Nachdem Sie die entsprechenden Pakete installiert haben, sollte die Anwendung eine Singleton-/globale Instanz einer loggerfactory erstellen.</span><span class="sxs-lookup"><span data-stu-id="12a53-126">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="12a53-127">Beispielsweise mithilfe der Konsolen Protokollierung:</span><span class="sxs-lookup"><span data-stu-id="12a53-127">For example, using the console logger:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="12a53-128">Version 3.x</span><span class="sxs-lookup"><span data-stu-id="12a53-128">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="12a53-129">Version 2.x</span><span class="sxs-lookup"><span data-stu-id="12a53-129">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="12a53-130">Im folgenden Codebeispiel wird ein `ConsoleLoggerProvider` Konstruktor verwendet, der in Version 2,2 veraltet ist und in 3,0 ersetzt wurde.</span><span class="sxs-lookup"><span data-stu-id="12a53-130">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="12a53-131">Es ist sicher, dass die Warnungen bei Verwendung von 2,2 ignoriert und unterdrückt werden.</span><span class="sxs-lookup"><span data-stu-id="12a53-131">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

<span data-ttu-id="12a53-132">Diese Singleton-/globale Instanz sollte dann bei EF Core auf dem registriert werden `DbContextOptionsBuilder` .</span><span class="sxs-lookup"><span data-stu-id="12a53-132">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="12a53-133">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="12a53-133">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="12a53-134">Es ist sehr wichtig, dass Anwendungen für jede Kontext Instanz keine neue iloggerfactory-Instanz erstellen.</span><span class="sxs-lookup"><span data-stu-id="12a53-134">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="12a53-135">Dies führt zu einem Speichermangel und einer unzureichenden Leistung.</span><span class="sxs-lookup"><span data-stu-id="12a53-135">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="12a53-136">Filtern der protokollierten Elemente</span><span class="sxs-lookup"><span data-stu-id="12a53-136">Filtering what is logged</span></span>

<span data-ttu-id="12a53-137">Die Anwendung kann steuern, was protokolliert wird, indem ein Filter für den iloggerprovider konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="12a53-137">The application can control what is logged by configuring a filter on the ILoggerProvider.</span></span> <span data-ttu-id="12a53-138">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="12a53-138">For example:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="12a53-139">Version 3.x</span><span class="sxs-lookup"><span data-stu-id="12a53-139">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="12a53-140">Version 2.x</span><span class="sxs-lookup"><span data-stu-id="12a53-140">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="12a53-141">Im folgenden Codebeispiel wird ein `ConsoleLoggerProvider` Konstruktor verwendet, der in Version 2,2 veraltet ist und in 3,0 ersetzt wurde.</span><span class="sxs-lookup"><span data-stu-id="12a53-141">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="12a53-142">Es ist sicher, dass die Warnungen bei Verwendung von 2,2 ignoriert und unterdrückt werden.</span><span class="sxs-lookup"><span data-stu-id="12a53-142">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

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

<span data-ttu-id="12a53-143">In diesem Beispiel wird das Protokoll so gefiltert, dass nur Meldungen zurückgegeben werden:</span><span class="sxs-lookup"><span data-stu-id="12a53-143">In this example, the log is filtered to only return messages:</span></span>

* <span data-ttu-id="12a53-144">in der Kategorie "Microsoft. entityframeworkcore. Database. Command"</span><span class="sxs-lookup"><span data-stu-id="12a53-144">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
* <span data-ttu-id="12a53-145">auf der Ebene "Informationen"</span><span class="sxs-lookup"><span data-stu-id="12a53-145">at the 'Information' level</span></span>

<span data-ttu-id="12a53-146">Bei EF Core werden Protokollierungs Kategorien in der- `DbLoggerCategory` Klasse definiert, um das Auffinden von Kategorien zu vereinfachen. Diese werden jedoch in einfache Zeichen folgen aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="12a53-146">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="12a53-147">Weitere Informationen zur zugrunde liegenden Protokollierungs Infrastruktur finden Sie in der [Dokumentation zur ASP.net Core Protokollierung](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span><span class="sxs-lookup"><span data-stu-id="12a53-147">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
