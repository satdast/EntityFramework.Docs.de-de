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
# <a name="using-ef-core-and-ef6-in-the-same-application"></a>Verwenden von EF Core und EF6 in derselben Anwendung

Es ist möglich, EF Core und EF6 in ein und derselben Anwendung oder Bibliothek zu verwenden, indem beide NuGet-Pakete installiert werden.

Einige Typen haben in EF Core und EF6 die gleichen Namen und unterscheiden sich nur durch den Namensraum, dies kann die Verwendung von EF Core und EF6 in derselben Codedatei erschweren. Die Mehrdeutigkeit lässt sich mithilfe von Anweisungen für Namespacealiase leicht beseitigen. Beispiel:

```csharp
using Microsoft.EntityFrameworkCore; // use DbContext for EF Core
using EF6 = System.Data.Entity; // use EF6.DbContext for the EF6 version
```

Wenn Sie eine vorhandene Anwendung mit mehreren EF-Modellen portieren, können Sie einige von ihnen selektiv auf EF Core portieren und für die anderen weiterhin EF6 verwenden.
