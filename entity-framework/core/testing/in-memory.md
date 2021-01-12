---
title: Testen mit der EF In-Memory-Datenbank EF Core
description: Verwenden der EF-in-Memory Database zum Testen einer Entity Framework Core-Anwendung
author: ajcvickers
ms.date: 10/27/2016
uid: core/testing/in-memory
ms.openlocfilehash: 78dcac3d0fd69110986c99a097a864104caa1951
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128809"
---
# <a name="testing-with-the-ef-in-memory-database"></a><span data-ttu-id="a6870-103">Testen mit der EF In-Memory-Datenbank</span><span class="sxs-lookup"><span data-stu-id="a6870-103">Testing with the EF In-Memory Database</span></span>

> [!WARNING]
> <span data-ttu-id="a6870-104">Das EF-in-Memory Database verhält sich häufig anders als relationale Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="a6870-104">The EF in-memory database often behaves differently than relational databases.</span></span>
> <span data-ttu-id="a6870-105">Verwenden Sie nur die EF-in-Memory Database, nachdem Sie sich mit den Problemen und vor-und Nachteile vertraut machen, wie unter [Testen von Code mit EF Core](xref:core/testing/index)erläutert.</span><span class="sxs-lookup"><span data-stu-id="a6870-105">Only use the EF in-memory database after fully understanding the issues and trade-offs involved, as discussed in [Testing code that uses EF Core](xref:core/testing/index).</span></span>

> [!TIP]
> <span data-ttu-id="a6870-106">SQLite ist ein relationaler Anbieter und kann auch in-Memory-Datenbanken verwenden.</span><span class="sxs-lookup"><span data-stu-id="a6870-106">SQLite is a relational provider and can also use in-memory databases.</span></span>
> <span data-ttu-id="a6870-107">Verwenden Sie diese für Tests, um das allgemeine Verhalten von relationalen Datenbanken genauer zu vergleichen.</span><span class="sxs-lookup"><span data-stu-id="a6870-107">Consider using this for testing to more closely match common relational database behaviors.</span></span>
> <span data-ttu-id="a6870-108">Dies wird in [Verwenden von SQLite zum Testen einer EF Core Anwendung](xref:core/testing/sqlite)beschrieben.</span><span class="sxs-lookup"><span data-stu-id="a6870-108">This is covered in [Using SQLite to test an EF Core application](xref:core/testing/sqlite).</span></span>

<span data-ttu-id="a6870-109">Die Informationen auf dieser Seite befinden sich jetzt an anderen Standorten:</span><span class="sxs-lookup"><span data-stu-id="a6870-109">The information on this page now lives in other locations:</span></span>

* <span data-ttu-id="a6870-110">Allgemeine Informationen zum Testen mit dem EF-in-Memory Database finden Sie unter [Testen von Code, der EF Core verwendet](xref:core/testing/index) .</span><span class="sxs-lookup"><span data-stu-id="a6870-110">See [Testing code that uses EF Core](xref:core/testing/index) for general information on testing with the EF in-memory database.</span></span>
* <span data-ttu-id="a6870-111">Im [Beispiel wird gezeigt, wie Sie Anwendungen testen, die EF Core](xref:core/testing/testing-sample) für ein Beispiel mithilfe der EF-in-Memory Database verwenden.</span><span class="sxs-lookup"><span data-stu-id="a6870-111">See [Sample showing how to test applications that use EF Core](xref:core/testing/testing-sample) for a sample using the EF in-memory database.</span></span>
* <span data-ttu-id="a6870-112">Allgemeine Informationen über die EF-in-Memory Database finden Sie [im EF in-Memory Database-Anbieter](xref:core/providers/in-memory/index) .</span><span class="sxs-lookup"><span data-stu-id="a6870-112">See [The EF in-memory database provider](xref:core/providers/in-memory/index) for general information about the EF in-memory database.</span></span>
