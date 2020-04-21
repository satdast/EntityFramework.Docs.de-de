---
title: Unterstützte .NET-Implementierungen – EF Core
author: bricelam
ms.date: 03/03/2020
uid: core/platforms/index
ms.openlocfilehash: 2738645596a325a1a43fd5b2164e370dd8c5893e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434122"
---
# <a name="net-implementations-supported-by-ef-core"></a><span data-ttu-id="a6931-102">Von EF Core unterstützte .NET-Implementierungen</span><span class="sxs-lookup"><span data-stu-id="a6931-102">.NET implementations supported by EF Core</span></span>

<span data-ttu-id="a6931-103">Wir möchten, dass EF Core für Entwickler auf allen modernen.NET-Implementierungen zur Verfügung steht, und wir arbeiten immer noch an diesem Ziel.</span><span class="sxs-lookup"><span data-stu-id="a6931-103">We want EF Core to be available to developers on all modern .NET implementations, and we're still working towards that goal.</span></span> <span data-ttu-id="a6931-104">Obwohl die EF Core-Unterstützung für .NET Core durch automatisierte Tests abgedeckt wird und viele Clientanwendungen diese erwiesenermaßen erfolgreich verwendet haben, treten bei Mono, Xamarin und der UWP einige Probleme auf.</span><span class="sxs-lookup"><span data-stu-id="a6931-104">While EF Core's support on .NET Core is covered by automated testing and many applications known to be using it successfully, Mono, Xamarin and UWP have some issues.</span></span>

## <a name="overview"></a><span data-ttu-id="a6931-105">Übersicht</span><span class="sxs-lookup"><span data-stu-id="a6931-105">Overview</span></span>

<span data-ttu-id="a6931-106">Die folgende Tabelle enthält Anweisungen zu den einzelnen .NET-Implementierungen:</span><span class="sxs-lookup"><span data-stu-id="a6931-106">The following table provides guidance for each .NET implementation:</span></span>

| <span data-ttu-id="a6931-107">EF Core</span><span class="sxs-lookup"><span data-stu-id="a6931-107">EF Core</span></span>                       | <span data-ttu-id="a6931-108">2.1 und 3.1</span><span class="sxs-lookup"><span data-stu-id="a6931-108">2.1 and 3.1</span></span> |
|:------------------------------|:------------|
| <span data-ttu-id="a6931-109">.NET-Standard</span><span class="sxs-lookup"><span data-stu-id="a6931-109">.NET Standard</span></span>                 | <span data-ttu-id="a6931-110">2.0</span><span class="sxs-lookup"><span data-stu-id="a6931-110">2.0</span></span>         |
| <span data-ttu-id="a6931-111">.NET Core</span><span class="sxs-lookup"><span data-stu-id="a6931-111">.NET Core</span></span>                     | <span data-ttu-id="a6931-112">2.0</span><span class="sxs-lookup"><span data-stu-id="a6931-112">2.0</span></span>         |
| <span data-ttu-id="a6931-113">.NET Framework<sup>(1)</sup></span><span class="sxs-lookup"><span data-stu-id="a6931-113">.NET Framework<sup>(1)</sup></span></span>  | <span data-ttu-id="a6931-114">4.7.2</span><span class="sxs-lookup"><span data-stu-id="a6931-114">4.7.2</span></span>       |
| <span data-ttu-id="a6931-115">Mono</span><span class="sxs-lookup"><span data-stu-id="a6931-115">Mono</span></span>                          | <span data-ttu-id="a6931-116">5.4</span><span class="sxs-lookup"><span data-stu-id="a6931-116">5.4</span></span>         |
| <span data-ttu-id="a6931-117">Xamarin.iOS<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="a6931-117">Xamarin.iOS<sup>(2)</sup></span></span>     | <span data-ttu-id="a6931-118">10.14</span><span class="sxs-lookup"><span data-stu-id="a6931-118">10.14</span></span>       |
| <span data-ttu-id="a6931-119">Xamarin.Android<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="a6931-119">Xamarin.Android<sup>(2)</sup></span></span> | <span data-ttu-id="a6931-120">8.0</span><span class="sxs-lookup"><span data-stu-id="a6931-120">8.0</span></span>         |
| <span data-ttu-id="a6931-121">UWP<sup>(3)</sup></span><span class="sxs-lookup"><span data-stu-id="a6931-121">UWP<sup>(3)</sup></span></span>             | <span data-ttu-id="a6931-122">10.0.16299</span><span class="sxs-lookup"><span data-stu-id="a6931-122">10.0.16299</span></span>  |
| <span data-ttu-id="a6931-123">Unity<sup>(4)</sup></span><span class="sxs-lookup"><span data-stu-id="a6931-123">Unity<sup>(4)</sup></span></span>           | <span data-ttu-id="a6931-124">2018.1</span><span class="sxs-lookup"><span data-stu-id="a6931-124">2018.1</span></span>      |

<span data-ttu-id="a6931-125"><sup>1</sup> Weitere Informationen finden Sie unten im Abschnitt [.NET Framework](#net-framework).</span><span class="sxs-lookup"><span data-stu-id="a6931-125"><sup>(1)</sup> See the [.NET Framework](#net-framework) section below.</span></span>

<span data-ttu-id="a6931-126"><sup>(2)</sup> Bei Xamarin wurden Probleme und bekannte Einschränkungen verzeichnet, die die Funktionsweise einiger mit EF Core entwickelten Anwendungen beeinträchtigen können.</span><span class="sxs-lookup"><span data-stu-id="a6931-126"><sup>(2)</sup> There are issues and known limitations with Xamarin which may prevent some applications developed using EF Core from working correctly.</span></span> <span data-ttu-id="a6931-127">Überprüfen Sie die Liste der [aktiven Probleme](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) zur Problemumgehung.</span><span class="sxs-lookup"><span data-stu-id="a6931-127">Check the list of [active issues](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) for workarounds.</span></span>

<span data-ttu-id="a6931-128"><sup>(3)</sup> EF Core 2.0.1 oder höher wird empfohlen.</span><span class="sxs-lookup"><span data-stu-id="a6931-128"><sup>(3)</sup> EF Core 2.0.1 and newer recommended.</span></span> <span data-ttu-id="a6931-129">Installieren Sie das [.NET Core UWP 6.x-Paket](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/).</span><span class="sxs-lookup"><span data-stu-id="a6931-129">Install the [.NET Core UWP 6.x package](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/).</span></span> <span data-ttu-id="a6931-130">Weitere Informationen finden Sie in diesem Artikel im Abschnitt [Universelle Windows-Plattform](#universal-windows-platform).</span><span class="sxs-lookup"><span data-stu-id="a6931-130">See the [Universal Windows Platform](#universal-windows-platform) section of this article.</span></span>

<span data-ttu-id="a6931-131"><sup>(4)</sup> Es gibt Probleme und bekannte Einschränkungen bei Unity.</span><span class="sxs-lookup"><span data-stu-id="a6931-131"><sup>(4)</sup> There are issues and known limitations with Unity.</span></span> <span data-ttu-id="a6931-132">Überprüfen Sie die Liste der [aktiven Probleme](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).</span><span class="sxs-lookup"><span data-stu-id="a6931-132">Check the list of [active issues](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).</span></span>

## <a name="net-framework"></a><span data-ttu-id="a6931-133">.NET Framework</span><span class="sxs-lookup"><span data-stu-id="a6931-133">.NET Framework</span></span>

<span data-ttu-id="a6931-134">.NET Framework-Anwendungen müssen ggf. angepasst werden, um mit der .NET Standard-Bibliothek zu funktionieren:</span><span class="sxs-lookup"><span data-stu-id="a6931-134">Applications that target .NET Framework may need changes to work with .NET Standard libraries:</span></span>

<span data-ttu-id="a6931-135">Bearbeiten Sie die Projektdatei, und stellen Sie sicher, dass der folgende Eintrag in der initialen Eigenschaftengruppe angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="a6931-135">Edit the project file and make sure the following entry appears in the initial property group:</span></span>

``` xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

<span data-ttu-id="a6931-136">Achten Sie bei Testprojekten auch darauf, dass der folgende Eintrag vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="a6931-136">For test projects, also make sure the following entry is present:</span></span>

``` xml
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

<span data-ttu-id="a6931-137">Wenn Sie eine ältere Version von Visual Studio verwenden möchten, stellen Sie sicher, dass Sie ein [Upgrade des NuGet-Clients auf Version 3.6.0 durchführen](https://www.nuget.org/downloads), damit dieser mit .NET Standard 2.0-Bibliotheken funktioniert.</span><span class="sxs-lookup"><span data-stu-id="a6931-137">If you want to use an older version of Visual Studio, make sure you [upgrade the NuGet client to version 3.6.0](https://www.nuget.org/downloads) to work with .NET Standard 2.0 libraries.</span></span>

<span data-ttu-id="a6931-138">Wir empfehlen außerdem die Migration des NuGet-Pakets „packages.config“ zu „PackageReference“, wenn möglich.</span><span class="sxs-lookup"><span data-stu-id="a6931-138">We also recommend migrating from NuGet packages.config to PackageReference if possible.</span></span> <span data-ttu-id="a6931-139">Fügen Sie der Projektdatei die folgende Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="a6931-139">Add the following property to your project file:</span></span>

``` xml
<RestoreProjectStyle>PackageReference</RestoreProjectStyle>
```

## <a name="universal-windows-platform"></a><span data-ttu-id="a6931-140">Universelle Windows-Plattform</span><span class="sxs-lookup"><span data-stu-id="a6931-140">Universal Windows Platform</span></span>

<span data-ttu-id="a6931-141">Bei älteren Versionen der EF Core- und .NET-UWP sind zahlreiche Kompatibilitätsprobleme aufgetreten. Dies galt besonders für Anwendungen, die mit der .NET Native-Toolkette kompiliert wurden.</span><span class="sxs-lookup"><span data-stu-id="a6931-141">Earlier versions of EF Core and .NET UWP had numerous compatibility issues, especially with applications compiled with the .NET Native toolchain.</span></span> <span data-ttu-id="a6931-142">Die neue .NET-UWP-Version bietet Unterstützung für .NET Standard 2.0 und .NET Native 2.0, die einen Großteil der zuvor erwähnten Kompatibilitätsprobleme behebt.</span><span class="sxs-lookup"><span data-stu-id="a6931-142">The new .NET UWP version adds support for .NET Standard 2.0 and contains .NET Native 2.0, which fixes most of the compatibility issues previously reported.</span></span> <span data-ttu-id="a6931-143">EF Core 2.0.1 wurde mit der UWP gründlicher getestet, die Tests sind jedoch nicht automatisiert.</span><span class="sxs-lookup"><span data-stu-id="a6931-143">EF Core 2.0.1 has been tested more thoroughly with UWP but testing is not automated.</span></span>

<span data-ttu-id="a6931-144">Bei Verwendung von EF Core unter UWP:</span><span class="sxs-lookup"><span data-stu-id="a6931-144">When using EF Core on UWP:</span></span>

* <span data-ttu-id="a6931-145">Um die Abfrageleistung zu optimieren, vermeiden Sie anonyme Typen in LINQ-Abfragen.</span><span class="sxs-lookup"><span data-stu-id="a6931-145">To optimize query performance, avoid anonymous types in LINQ queries.</span></span> <span data-ttu-id="a6931-146">Das Bereitstellen einer UWP-Anwendung im App Store erfordert die Kompilierung einer Anwendung mit .NET Native.</span><span class="sxs-lookup"><span data-stu-id="a6931-146">Deploying a UWP application to the app store requires an application to be compiled with .NET Native.</span></span> <span data-ttu-id="a6931-147">Abfragen mit anonymen Typen zeigen in .NET Native eine unzureichende Leistung.</span><span class="sxs-lookup"><span data-stu-id="a6931-147">Queries with anonymous types have worse performance on .NET Native.</span></span>

* <span data-ttu-id="a6931-148">Zur Optimierung der `SaveChanges()`-Leistung verwenden Sie [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy), und implementieren Sie [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx) und [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) in Ihren Entitätstypen.</span><span class="sxs-lookup"><span data-stu-id="a6931-148">To optimize `SaveChanges()` performance, use [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) and implement [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx), and [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) in your entity types.</span></span>

## <a name="report-issues"></a><span data-ttu-id="a6931-149">Melden von Problemen</span><span class="sxs-lookup"><span data-stu-id="a6931-149">Report issues</span></span>

<span data-ttu-id="a6931-150">Für alle Kombinationen, die nicht wie erwartet funktionieren, empfehlen wir, neue Issues in der [EF Core-Issueverfolgung](https://github.com/aspnet/entityframeworkcore/issues/new) zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a6931-150">For any combination that doesn't work as expected, we encourage creating new issues on the [EF Core issue tracker](https://github.com/aspnet/entityframeworkcore/issues/new).</span></span> <span data-ttu-id="a6931-151">Verwenden Sie bei Xamarin-bezogenen Problemen [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) oder [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new).</span><span class="sxs-lookup"><span data-stu-id="a6931-151">For Xamarin-specific issues use the issue tracker for [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) or [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new).</span></span>
