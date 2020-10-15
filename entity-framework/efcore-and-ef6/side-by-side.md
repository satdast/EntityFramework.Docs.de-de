---
title: EF6 und EF Core – Verwendung in derselben Anwendung
description: Leitfaden zur Verwendung von Entity Framework Core und Entity Framework 6 in derselben Anwendung
author: ajcvickers
ms.date: 01/23/2019
uid: efcore-and-ef6/side-by-side
ms.openlocfilehash: 68549009868a63f50d34ea8829de55574c891d19
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064197"
---
# <a name="using-ef-core-and-ef6-in-the-same-application"></a><span data-ttu-id="d5597-103">Verwenden von EF Core und EF6 in derselben Anwendung</span><span class="sxs-lookup"><span data-stu-id="d5597-103">Using EF Core and EF6 in the Same Application</span></span>

<span data-ttu-id="d5597-104">Es ist möglich, EF Core und EF6 in ein und derselben Anwendung oder Bibliothek zu verwenden, indem beide NuGet-Pakete installiert werden.</span><span class="sxs-lookup"><span data-stu-id="d5597-104">It is possible to use EF Core and EF6 in the same application or library by installing both NuGet packages.</span></span>

<span data-ttu-id="d5597-105">Einige Typen haben in EF Core und EF6 die gleichen Namen und unterscheiden sich nur durch den Namensraum, dies kann die Verwendung von EF Core und EF6 in derselben Codedatei erschweren.</span><span class="sxs-lookup"><span data-stu-id="d5597-105">Some types have the same names in EF Core and EF6 and differ only by namespace, which may complicate using both EF Core and EF6 in the same code file.</span></span> <span data-ttu-id="d5597-106">Die Mehrdeutigkeit lässt sich mithilfe von Anweisungen für Namespacealiase leicht beseitigen.</span><span class="sxs-lookup"><span data-stu-id="d5597-106">The ambiguity can be easily removed using namespace alias directives.</span></span> <span data-ttu-id="d5597-107">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d5597-107">For example:</span></span>

```csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

<span data-ttu-id="d5597-108">Wenn Sie eine vorhandene Anwendung mit mehreren EF-Modellen portieren, können Sie einige von ihnen selektiv auf EF Core portieren und für die anderen weiterhin EF6 verwenden.</span><span class="sxs-lookup"><span data-stu-id="d5597-108">If you are porting an existing application that has multiple EF models, you can choose to selectively port some of them to EF Core, and continue using EF6 for the others.</span></span>
