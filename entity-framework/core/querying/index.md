---
title: Abfragen von Daten – EF Core
description: Informationsübersicht zu Abfragen in Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: 0bc1ef7d5595d5ed534a118fdc33e1a78648582e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071120"
---
# <a name="querying-data"></a><span data-ttu-id="1d656-103">Abfrage von Daten</span><span class="sxs-lookup"><span data-stu-id="1d656-103">Querying Data</span></span>

<span data-ttu-id="1d656-104">Entity Framework Core verwendet Language Integrated Query (LINQ), um Daten von der Datenbank abzufragen.</span><span class="sxs-lookup"><span data-stu-id="1d656-104">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="1d656-105">LINQ ermöglicht Ihnen, mit C# (oder Ihrer bevorzugten .NET-Sprache) stark typisierte Abfragen zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="1d656-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries.</span></span> <span data-ttu-id="1d656-106">Dabei werden der abgeleitete Kontext und Entitätsklassen verwendet, um auf Datenbankobjekte zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="1d656-106">It uses your derived context and entity classes to reference database objects.</span></span> <span data-ttu-id="1d656-107">EF Core übergibt eine Darstellung der LINQ-Abfrage an den Datenbankanbieter.</span><span class="sxs-lookup"><span data-stu-id="1d656-107">EF Core passes a representation of the LINQ query to the database provider.</span></span> <span data-ttu-id="1d656-108">Die Datenbankanbieter übersetzen diese dann in die datenbankspezifische Abfragesprache, z. B. SQL für relationale Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="1d656-108">Database providers in turn translate it to database-specific query language (for example, SQL for a relational database).</span></span>

> [!TIP]
> <span data-ttu-id="1d656-109">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="1d656-109">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

<span data-ttu-id="1d656-110">Auf den folgenden Ausschnitten werden einige Beispiele für das Ausführen gängiger Aufgaben mit EF Core dargestellt.</span><span class="sxs-lookup"><span data-stu-id="1d656-110">The following snippets show a few examples of how to achieve common tasks with Entity Framework Core.</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="1d656-111">Laden aller Daten</span><span class="sxs-lookup"><span data-stu-id="1d656-111">Loading all data</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a><span data-ttu-id="1d656-112">Laden einer einzelnen Entität</span><span class="sxs-lookup"><span data-stu-id="1d656-112">Loading a single entity</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingSingleEntity)]

## <a name="filtering"></a><span data-ttu-id="1d656-113">Filtern</span><span class="sxs-lookup"><span data-stu-id="1d656-113">Filtering</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#Filtering)]

## <a name="further-readings"></a><span data-ttu-id="1d656-114">Weiterführende Themen</span><span class="sxs-lookup"><span data-stu-id="1d656-114">Further readings</span></span>

- <span data-ttu-id="1d656-115">Weitere Informationen zu [LINQ-Abfrageausdrücken](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="1d656-115">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
- <span data-ttu-id="1d656-116">Ausführlichere Informationen zur Verarbeitung einer Abfrage in EF Core finden Sie unter [Funktionsweise von Abfragen](xref:core/querying/how-query-works).</span><span class="sxs-lookup"><span data-stu-id="1d656-116">For more detailed information on how a query is processed in EF Core, see [How queries Work](xref:core/querying/how-query-works).</span></span>
