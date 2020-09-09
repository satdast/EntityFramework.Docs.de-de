---
title: Schreiben eines Datenbankanbieters-EF Core
description: Informationen zum Schreiben eines neuen Entity Framework Core Anbieters
author: anmiller
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
uid: core/providers/writing-a-provider
ms.openlocfilehash: 6d7a8a03c612eeda4d65917a2713e4570c671002
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616507"
---
# <a name="writing-a-database-provider"></a><span data-ttu-id="3a958-103">Schreiben eines Datenbankanbieters</span><span class="sxs-lookup"><span data-stu-id="3a958-103">Writing a Database Provider</span></span>

<span data-ttu-id="3a958-104">Weitere Informationen zum Schreiben eines Entity Framework Core Datenbankanbieters finden [Sie unter so können Sie einen EF Core Anbieter](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) von [Arthur Vickers](https://github.com/ajcvickers)schreiben.</span><span class="sxs-lookup"><span data-stu-id="3a958-104">For information about writing an Entity Framework Core database provider, see [So you want to write an EF Core provider](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) by [Arthur Vickers](https://github.com/ajcvickers).</span></span>

> [!NOTE]
> <span data-ttu-id="3a958-105">Diese Beiträge wurden seit EF Core 1,1 nicht aktualisiert, und seit dieser Zeit wurden bedeutende Änderungen vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="3a958-105">These posts have not been updated since EF Core 1.1 and there have been significant changes since that time.</span></span>  
<span data-ttu-id="3a958-106">[Problem 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) ist das Nachverfolgen von Aktualisierungen für diese Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="3a958-106">[Issue 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) is tracking updates to this documentation.</span></span>

<span data-ttu-id="3a958-107">Die EF Core Codebasis ist Open Source und enthält mehrere Datenbankanbieter, die als Referenz verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="3a958-107">The EF Core codebase is open source and contains several database providers that can be used as a reference.</span></span> <span data-ttu-id="3a958-108">Den Quellcode finden Sie unter <https://github.com/aspnet/EntityFrameworkCore> .</span><span class="sxs-lookup"><span data-stu-id="3a958-108">You can find the source code at <https://github.com/aspnet/EntityFrameworkCore>.</span></span> <span data-ttu-id="3a958-109">Es kann auch hilfreich sein, sich den Code für häufig verwendete Drittanbieter Anbieter anzusehen, wie z. b. [npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)und [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span><span class="sxs-lookup"><span data-stu-id="3a958-109">It may also be helpful to look at the code for commonly used third-party providers, such as [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span></span> <span data-ttu-id="3a958-110">Insbesondere sind diese Projekte so eingerichtet, dass Sie von den Funktionstests, die auf nuget veröffentlicht werden, erweitert und ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="3a958-110">In particular, these projects are setup to extend from and run functional tests that we publish on NuGet.</span></span> <span data-ttu-id="3a958-111">Diese Art von Setup wird dringend empfohlen.</span><span class="sxs-lookup"><span data-stu-id="3a958-111">This kind of setup is strongly recommended.</span></span>

## <a name="keeping-up-to-date-with-provider-changes"></a><span data-ttu-id="3a958-112">Beibehalten der aktuellen Änderungen an Anbietern</span><span class="sxs-lookup"><span data-stu-id="3a958-112">Keeping up-to-date with provider changes</span></span>

<span data-ttu-id="3a958-113">Beginnend mit der Arbeit nach Version 2,1 haben wir ein [Protokoll der Änderungen](xref:core/providers/provider-log) erstellt, die ggf. entsprechende Änderungen im Anbieter Code erfordern.</span><span class="sxs-lookup"><span data-stu-id="3a958-113">Starting with work after the 2.1 release, we have created a [log of changes](xref:core/providers/provider-log) that may need corresponding changes in provider code.</span></span> <span data-ttu-id="3a958-114">Dies soll beim Aktualisieren eines vorhandenen Anbieters helfen, um mit einer neuen Version von EF Core Arbeiten zu können.</span><span class="sxs-lookup"><span data-stu-id="3a958-114">This is intended to help when updating an existing provider to work with a new version of EF Core.</span></span>

<span data-ttu-id="3a958-115">Vor 2,1 haben wir die [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) Bezeichnungen und in unseren GitHub-Problemen und Pull Requests für einen ähnlichen Zweck verwendet.</span><span class="sxs-lookup"><span data-stu-id="3a958-115">Prior to 2.1, we used the [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) and [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) labels on our GitHub issues and pull requests for a similar purpose.</span></span> <span data-ttu-id="3a958-116">Wir werden diese Bezeichnungen für Probleme verwenden, um anzugeben, welche Arbeitsaufgaben in einem bestimmten Release möglicherweise auch die Arbeit in Anbietern erfordern.</span><span class="sxs-lookup"><span data-stu-id="3a958-116">We will continiue to use these lables on issues to give an indication which work items in a given release may also require work to be done in providers.</span></span> <span data-ttu-id="3a958-117">Eine `providers-beware` Bezeichnung bedeutet in der Regel, dass die Implementierung eines Arbeits Elements die Anbieter unterbrechen kann, während eine `providers-fyi` Bezeichnung in der Regel bedeutet, dass die Anbieter nicht beschädigt werden, der Code jedoch möglicherweise trotzdem geändert werden muss, z. b., um neue Funktionen zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="3a958-117">A `providers-beware` label typically means that the implementation of an work item may break providers, while a `providers-fyi` label typically means that providers will not be broken, but code may need to be changed anyway, for example, to enable new functionality.</span></span>

## <a name="suggested-naming-of-third-party-providers"></a><span data-ttu-id="3a958-118">Empfohlene Benennung von Drittanbieter Anbietern</span><span class="sxs-lookup"><span data-stu-id="3a958-118">Suggested naming of third party providers</span></span>

<span data-ttu-id="3a958-119">Wir empfehlen, die folgenden Namen für nuget-Pakete zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="3a958-119">We suggest using the following naming for NuGet packages.</span></span> <span data-ttu-id="3a958-120">Dies stimmt mit den Namen von Paketen überein, die vom EF Core Team geliefert werden.</span><span class="sxs-lookup"><span data-stu-id="3a958-120">This is consistent with the names of packages delivered by the EF Core team.</span></span>

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

<span data-ttu-id="3a958-121">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a958-121">For example:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
