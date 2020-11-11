---
title: Abfragen von Daten – EF Core
description: Informationsübersicht zu Abfragen in Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: da5177dda4f2df6537ee9133edf4f1240a4b5e94
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430117"
---
# <a name="querying-data"></a><span data-ttu-id="62cb0-103">Abfrage von Daten</span><span class="sxs-lookup"><span data-stu-id="62cb0-103">Querying Data</span></span>

<span data-ttu-id="62cb0-104">Entity Framework Core verwendet Language Integrated Query (LINQ), um Daten aus der Datenbank abzufragen.</span><span class="sxs-lookup"><span data-stu-id="62cb0-104">Entity Framework Core uses Language-Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="62cb0-105">LINQ ermöglicht Ihnen, mit C# (oder Ihrer bevorzugten .NET-Sprache) stark typisierte Abfragen zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="62cb0-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries.</span></span> <span data-ttu-id="62cb0-106">Dabei werden der abgeleitete Kontext und Entitätsklassen verwendet, um auf Datenbankobjekte zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="62cb0-106">It uses your derived context and entity classes to reference database objects.</span></span> <span data-ttu-id="62cb0-107">EF Core übergibt eine Darstellung der LINQ-Abfrage an den Datenbankanbieter.</span><span class="sxs-lookup"><span data-stu-id="62cb0-107">EF Core passes a representation of the LINQ query to the database provider.</span></span> <span data-ttu-id="62cb0-108">Die Datenbankanbieter übersetzen diese dann in die datenbankspezifische Abfragesprache, z. B. SQL für relationale Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="62cb0-108">Database providers in turn translate it to database-specific query language (for example, SQL for a relational database).</span></span> <span data-ttu-id="62cb0-109">Abfragen werden immer für die Datenbank ausgeführt, auch wenn die im Ergebnis zurückgegebenen Entitäten bereits im Kontext vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="62cb0-109">Queries are always executed against the database even if the entities returned in the result already exist in the context.</span></span>

> [!TIP]
> <span data-ttu-id="62cb0-110">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="62cb0-110">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) on GitHub.</span></span>

<span data-ttu-id="62cb0-111">Auf den folgenden Ausschnitten werden einige Beispiele für das Ausführen gängiger Aufgaben mit EF Core dargestellt.</span><span class="sxs-lookup"><span data-stu-id="62cb0-111">The following snippets show a few examples of how to achieve common tasks with Entity Framework Core.</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="62cb0-112">Laden aller Daten</span><span class="sxs-lookup"><span data-stu-id="62cb0-112">Loading all data</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a><span data-ttu-id="62cb0-113">Laden einer einzelnen Entität</span><span class="sxs-lookup"><span data-stu-id="62cb0-113">Loading a single entity</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingSingleEntity)]

## <a name="filtering"></a><span data-ttu-id="62cb0-114">Filtern</span><span class="sxs-lookup"><span data-stu-id="62cb0-114">Filtering</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#Filtering)]

## <a name="further-readings"></a><span data-ttu-id="62cb0-115">Weiterführende Themen</span><span class="sxs-lookup"><span data-stu-id="62cb0-115">Further readings</span></span>

- <span data-ttu-id="62cb0-116">Weitere Informationen zu [LINQ-Abfrageausdrücken](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="62cb0-116">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
- <span data-ttu-id="62cb0-117">Ausführlichere Informationen zur Verarbeitung einer Abfrage in EF Core finden Sie unter [Funktionsweise von Abfragen](xref:core/querying/how-query-works).</span><span class="sxs-lookup"><span data-stu-id="62cb0-117">For more detailed information on how a query is processed in EF Core, see [How queries Work](xref:core/querying/how-query-works).</span></span>
