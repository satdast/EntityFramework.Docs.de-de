---
title: Unterstützte .NET-Implementierungen – EF Core
description: Informationen zu unterstützten Plattformen in Entity Framework Core-Versionen
author: bricelam
ms.date: 06/26/2020
uid: core/miscellaneous/platforms
ms.openlocfilehash: 6b888843d491805e735fd903253d7f10c028dd9f
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431247"
---
# <a name="net-implementations-supported-by-ef-core"></a><span data-ttu-id="d6246-103">Von EF Core unterstützte .NET-Implementierungen</span><span class="sxs-lookup"><span data-stu-id="d6246-103">.NET implementations supported by EF Core</span></span>

<span data-ttu-id="d6246-104">Wir möchten, dass EF Core für Entwickler auf allen modernen.NET-Implementierungen zur Verfügung steht, und wir arbeiten immer noch an diesem Ziel.</span><span class="sxs-lookup"><span data-stu-id="d6246-104">We want EF Core to be available to developers on all modern .NET implementations, and we're still working towards that goal.</span></span> <span data-ttu-id="d6246-105">Obwohl die EF Core-Unterstützung für .NET Core durch automatisierte Tests abgedeckt wird und viele Clientanwendungen diese erwiesenermaßen erfolgreich verwendet haben, treten bei Mono, Xamarin und der UWP einige Probleme auf.</span><span class="sxs-lookup"><span data-stu-id="d6246-105">While EF Core's support on .NET Core is covered by automated testing and many applications known to be using it successfully, Mono, Xamarin and UWP have some issues.</span></span>

## <a name="overview"></a><span data-ttu-id="d6246-106">Übersicht</span><span class="sxs-lookup"><span data-stu-id="d6246-106">Overview</span></span>

<span data-ttu-id="d6246-107">Die folgende Tabelle enthält Anweisungen zu den einzelnen .NET-Implementierungen:</span><span class="sxs-lookup"><span data-stu-id="d6246-107">The following table provides guidance for each .NET implementation:</span></span>

| <span data-ttu-id="d6246-108">EF Core</span><span class="sxs-lookup"><span data-stu-id="d6246-108">EF Core</span></span>                       | <span data-ttu-id="d6246-109">2.1 und 3.1</span><span class="sxs-lookup"><span data-stu-id="d6246-109">2.1 and 3.1</span></span> | <span data-ttu-id="d6246-110">5.0</span><span class="sxs-lookup"><span data-stu-id="d6246-110">5.0</span></span>             |
|:------------------------------|:------------|:----------------|
| <span data-ttu-id="d6246-111">.NET-Standard</span><span class="sxs-lookup"><span data-stu-id="d6246-111">.NET Standard</span></span>                 | <span data-ttu-id="d6246-112">2.0</span><span class="sxs-lookup"><span data-stu-id="d6246-112">2.0</span></span>         | <span data-ttu-id="d6246-113">2.1</span><span class="sxs-lookup"><span data-stu-id="d6246-113">2.1</span></span>             |
| <span data-ttu-id="d6246-114">.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6246-114">.NET Core</span></span>                     | <span data-ttu-id="d6246-115">2.0</span><span class="sxs-lookup"><span data-stu-id="d6246-115">2.0</span></span>         | <span data-ttu-id="d6246-116">3.0</span><span class="sxs-lookup"><span data-stu-id="d6246-116">3.0</span></span>             |
| <span data-ttu-id="d6246-117">.NET Framework<sup>(1)</sup></span><span class="sxs-lookup"><span data-stu-id="d6246-117">.NET Framework<sup>(1)</sup></span></span>  | <span data-ttu-id="d6246-118">4.7.2</span><span class="sxs-lookup"><span data-stu-id="d6246-118">4.7.2</span></span>       | <span data-ttu-id="d6246-119">(Nicht unterstützt)</span><span class="sxs-lookup"><span data-stu-id="d6246-119">(not supported)</span></span> |
| <span data-ttu-id="d6246-120">Mono</span><span class="sxs-lookup"><span data-stu-id="d6246-120">Mono</span></span>                          | <span data-ttu-id="d6246-121">5.4</span><span class="sxs-lookup"><span data-stu-id="d6246-121">5.4</span></span>         | <span data-ttu-id="d6246-122">6.4</span><span class="sxs-lookup"><span data-stu-id="d6246-122">6.4</span></span>             |
| <span data-ttu-id="d6246-123">Xamarin.iOS<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="d6246-123">Xamarin.iOS<sup>(2)</sup></span></span>     | <span data-ttu-id="d6246-124">10.14</span><span class="sxs-lookup"><span data-stu-id="d6246-124">10.14</span></span>       | <span data-ttu-id="d6246-125">12.16</span><span class="sxs-lookup"><span data-stu-id="d6246-125">12.16</span></span>           |
| <span data-ttu-id="d6246-126">Xamarin.Mac<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="d6246-126">Xamarin.Mac<sup>(2)</sup></span></span>     | <span data-ttu-id="d6246-127">3.8</span><span class="sxs-lookup"><span data-stu-id="d6246-127">3.8</span></span>         | <span data-ttu-id="d6246-128">5.16</span><span class="sxs-lookup"><span data-stu-id="d6246-128">5.16</span></span>            |
| <span data-ttu-id="d6246-129">Xamarin.Android<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="d6246-129">Xamarin.Android<sup>(2)</sup></span></span> | <span data-ttu-id="d6246-130">8.0</span><span class="sxs-lookup"><span data-stu-id="d6246-130">8.0</span></span>         | <span data-ttu-id="d6246-131">10.0</span><span class="sxs-lookup"><span data-stu-id="d6246-131">10.0</span></span>            |
| <span data-ttu-id="d6246-132">UWP<sup>(3)</sup></span><span class="sxs-lookup"><span data-stu-id="d6246-132">UWP<sup>(3)</sup></span></span>             | <span data-ttu-id="d6246-133">10.0.16299</span><span class="sxs-lookup"><span data-stu-id="d6246-133">10.0.16299</span></span>  | <span data-ttu-id="d6246-134">Wird nachgeliefert.</span><span class="sxs-lookup"><span data-stu-id="d6246-134">TBD</span></span>             |
| <span data-ttu-id="d6246-135">Unity<sup>(4)</sup></span><span class="sxs-lookup"><span data-stu-id="d6246-135">Unity<sup>(4)</sup></span></span>           | <span data-ttu-id="d6246-136">2018.1</span><span class="sxs-lookup"><span data-stu-id="d6246-136">2018.1</span></span>      | <span data-ttu-id="d6246-137">Wird nachgeliefert.</span><span class="sxs-lookup"><span data-stu-id="d6246-137">TBD</span></span>             |

<span data-ttu-id="d6246-138"><sup>1</sup> Weitere Informationen finden Sie unten im Abschnitt [.NET Framework](#net-framework).</span><span class="sxs-lookup"><span data-stu-id="d6246-138"><sup>(1)</sup> See the [.NET Framework](#net-framework) section below.</span></span>

<span data-ttu-id="d6246-139"><sup>(2)</sup> Bei Xamarin wurden Probleme und bekannte Einschränkungen verzeichnet, die die Funktionsweise einiger mit EF Core entwickelten Anwendungen beeinträchtigen können.</span><span class="sxs-lookup"><span data-stu-id="d6246-139"><sup>(2)</sup> There are issues and known limitations with Xamarin which may prevent some applications developed using EF Core from working correctly.</span></span> <span data-ttu-id="d6246-140">Überprüfen Sie die Liste der [aktiven Probleme](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) zur Problemumgehung.</span><span class="sxs-lookup"><span data-stu-id="d6246-140">Check the list of [active issues](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) for workarounds.</span></span>

<span data-ttu-id="d6246-141"><sup>(3)</sup> EF Core 2.0.1 oder höher wird empfohlen.</span><span class="sxs-lookup"><span data-stu-id="d6246-141"><sup>(3)</sup> EF Core 2.0.1 and newer recommended.</span></span> <span data-ttu-id="d6246-142">Installieren Sie das [.NET Core UWP 6.x-Paket](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/).</span><span class="sxs-lookup"><span data-stu-id="d6246-142">Install the [.NET Core UWP 6.x package](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/).</span></span> <span data-ttu-id="d6246-143">Weitere Informationen finden Sie in diesem Artikel im Abschnitt [Universelle Windows-Plattform](#universal-windows-platform).</span><span class="sxs-lookup"><span data-stu-id="d6246-143">See the [Universal Windows Platform](#universal-windows-platform) section of this article.</span></span>

<span data-ttu-id="d6246-144"><sup>(4)</sup> Es gibt Probleme und bekannte Einschränkungen bei Unity.</span><span class="sxs-lookup"><span data-stu-id="d6246-144"><sup>(4)</sup> There are issues and known limitations with Unity.</span></span> <span data-ttu-id="d6246-145">Überprüfen Sie die Liste der [aktiven Probleme](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).</span><span class="sxs-lookup"><span data-stu-id="d6246-145">Check the list of [active issues](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).</span></span>

## <a name="net-framework"></a><span data-ttu-id="d6246-146">.NET Framework</span><span class="sxs-lookup"><span data-stu-id="d6246-146">.NET Framework</span></span>

<span data-ttu-id="d6246-147">.NET Framework-Anwendungen müssen ggf. angepasst werden, um mit der .NET Standard-Bibliothek zu funktionieren:</span><span class="sxs-lookup"><span data-stu-id="d6246-147">Applications that target .NET Framework may need changes to work with .NET Standard libraries:</span></span>

<span data-ttu-id="d6246-148">Bearbeiten Sie die Projektdatei, und stellen Sie sicher, dass der folgende Eintrag in der initialen Eigenschaftengruppe angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="d6246-148">Edit the project file and make sure the following entry appears in the initial property group:</span></span>

```xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

<span data-ttu-id="d6246-149">Achten Sie bei Testprojekten auch darauf, dass der folgende Eintrag vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="d6246-149">For test projects, also make sure the following entry is present:</span></span>

```xml
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

<span data-ttu-id="d6246-150">Wenn Sie eine ältere Version von Visual Studio verwenden möchten, stellen Sie sicher, dass Sie ein [Upgrade des NuGet-Clients auf Version 3.6.0 durchführen](https://www.nuget.org/downloads), damit dieser mit .NET Standard 2.0-Bibliotheken funktioniert.</span><span class="sxs-lookup"><span data-stu-id="d6246-150">If you want to use an older version of Visual Studio, make sure you [upgrade the NuGet client to version 3.6.0](https://www.nuget.org/downloads) to work with .NET Standard 2.0 libraries.</span></span>

<span data-ttu-id="d6246-151">Wir empfehlen außerdem die Migration des NuGet-Pakets „packages.config“ zu „PackageReference“, wenn möglich.</span><span class="sxs-lookup"><span data-stu-id="d6246-151">We also recommend migrating from NuGet packages.config to PackageReference if possible.</span></span> <span data-ttu-id="d6246-152">Fügen Sie der Projektdatei die folgende Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="d6246-152">Add the following property to your project file:</span></span>

```xml
<RestoreProjectStyle>PackageReference</RestoreProjectStyle>
```

## <a name="universal-windows-platform"></a><span data-ttu-id="d6246-153">Universelle Windows-Plattform</span><span class="sxs-lookup"><span data-stu-id="d6246-153">Universal Windows Platform</span></span>

<span data-ttu-id="d6246-154">Bei älteren Versionen der EF Core- und .NET-UWP sind zahlreiche Kompatibilitätsprobleme aufgetreten. Dies galt besonders für Anwendungen, die mit der .NET Native-Toolkette kompiliert wurden.</span><span class="sxs-lookup"><span data-stu-id="d6246-154">Earlier versions of EF Core and .NET UWP had numerous compatibility issues, especially with applications compiled with the .NET Native toolchain.</span></span> <span data-ttu-id="d6246-155">Die neue .NET-UWP-Version bietet Unterstützung für .NET Standard 2.0 und .NET Native 2.0, die einen Großteil der zuvor erwähnten Kompatibilitätsprobleme behebt.</span><span class="sxs-lookup"><span data-stu-id="d6246-155">The new .NET UWP version adds support for .NET Standard 2.0 and contains .NET Native 2.0, which fixes most of the compatibility issues previously reported.</span></span> <span data-ttu-id="d6246-156">EF Core 2.0.1 wurde mit der UWP gründlicher getestet, die Tests sind jedoch nicht automatisiert.</span><span class="sxs-lookup"><span data-stu-id="d6246-156">EF Core 2.0.1 has been tested more thoroughly with UWP but testing is not automated.</span></span>

<span data-ttu-id="d6246-157">Bei Verwendung von EF Core unter UWP:</span><span class="sxs-lookup"><span data-stu-id="d6246-157">When using EF Core on UWP:</span></span>

* <span data-ttu-id="d6246-158">Um die Abfrageleistung zu optimieren, vermeiden Sie anonyme Typen in LINQ-Abfragen.</span><span class="sxs-lookup"><span data-stu-id="d6246-158">To optimize query performance, avoid anonymous types in LINQ queries.</span></span> <span data-ttu-id="d6246-159">Das Bereitstellen einer UWP-Anwendung im App Store erfordert die Kompilierung einer Anwendung mit .NET Native.</span><span class="sxs-lookup"><span data-stu-id="d6246-159">Deploying a UWP application to the app store requires an application to be compiled with .NET Native.</span></span> <span data-ttu-id="d6246-160">Abfragen mit anonymen Typen zeigen in .NET Native eine unzureichende Leistung.</span><span class="sxs-lookup"><span data-stu-id="d6246-160">Queries with anonymous types have worse performance on .NET Native.</span></span>

* <span data-ttu-id="d6246-161">Zur Optimierung der `SaveChanges()`-Leistung verwenden Sie [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy), und implementieren Sie [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx) und [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) in Ihren Entitätstypen.</span><span class="sxs-lookup"><span data-stu-id="d6246-161">To optimize `SaveChanges()` performance, use [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) and implement [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx), and [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) in your entity types.</span></span>

## <a name="report-issues"></a><span data-ttu-id="d6246-162">Melden von Problemen</span><span class="sxs-lookup"><span data-stu-id="d6246-162">Report issues</span></span>

<span data-ttu-id="d6246-163">Für alle Kombinationen, die nicht wie erwartet funktionieren, empfehlen wir, neue Issues in der [EF Core-Issueverfolgung](https://github.com/dotnet/efcore/issues/new) zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d6246-163">For any combination that doesn't work as expected, we encourage creating new issues on the [EF Core issue tracker](https://github.com/dotnet/efcore/issues/new).</span></span> <span data-ttu-id="d6246-164">Verwenden Sie bei Xamarin-bezogenen Problemen [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) oder [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new).</span><span class="sxs-lookup"><span data-stu-id="d6246-164">For Xamarin-specific issues use the issue tracker for [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) or [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new).</span></span>