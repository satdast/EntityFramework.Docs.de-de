---
title: Abfragen von Daten – EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 7c65ec3e-46c8-48f8-8232-9e31f96c277b
uid: core/querying/index
ms.openlocfilehash: 82f8a2e8e78e9d82f46f2fc6ced6dd9141329a22
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370338"
---
# <a name="querying-data"></a><span data-ttu-id="10ee0-102">Abfrage von Daten</span><span class="sxs-lookup"><span data-stu-id="10ee0-102">Querying Data</span></span>

<span data-ttu-id="10ee0-103">Entity Framework Core verwendet Language Integrated Query (LINQ), um Daten von der Datenbank abzufragen.</span><span class="sxs-lookup"><span data-stu-id="10ee0-103">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="10ee0-104">LINQ ermöglicht Ihnen, mit C# (oder Ihrer bevorzugten .NET-Sprache) stark typisierte Abfragen zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="10ee0-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries.</span></span> <span data-ttu-id="10ee0-105">Dabei werden der abgeleitete Kontext und Entitätsklassen verwendet, um auf Datenbankobjekte zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="10ee0-105">It uses your derived context and entity classes to reference database objects.</span></span> <span data-ttu-id="10ee0-106">EF Core übergibt eine Darstellung der LINQ-Abfrage an den Datenbankanbieter.</span><span class="sxs-lookup"><span data-stu-id="10ee0-106">EF Core passes a representation of the LINQ query to the database provider.</span></span> <span data-ttu-id="10ee0-107">Die Datenbankanbieter übersetzen diese dann in die datenbankspezifische Abfragesprache, z. B. SQL für relationale Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="10ee0-107">Database providers in turn translate it to database-specific query language (for example, SQL for a relational database).</span></span>

> [!TIP]
> <span data-ttu-id="10ee0-108">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="10ee0-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

<span data-ttu-id="10ee0-109">Auf den folgenden Ausschnitten werden einige Beispiele für das Ausführen gängiger Aufgaben mit EF Core dargestellt.</span><span class="sxs-lookup"><span data-stu-id="10ee0-109">The following snippets show a few examples of how to achieve common tasks with Entity Framework Core.</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="10ee0-110">Laden aller Daten</span><span class="sxs-lookup"><span data-stu-id="10ee0-110">Loading all data</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a><span data-ttu-id="10ee0-111">Laden einer einzelnen Entität</span><span class="sxs-lookup"><span data-stu-id="10ee0-111">Loading a single entity</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingSingleEntity)]

## <a name="filtering"></a><span data-ttu-id="10ee0-112">Filtern</span><span class="sxs-lookup"><span data-stu-id="10ee0-112">Filtering</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#Filtering)]

## <a name="further-readings"></a><span data-ttu-id="10ee0-113">Weiterführende Themen</span><span class="sxs-lookup"><span data-stu-id="10ee0-113">Further readings</span></span>

- <span data-ttu-id="10ee0-114">Weitere Informationen zu [LINQ-Abfrageausdrücken](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span><span class="sxs-lookup"><span data-stu-id="10ee0-114">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
- <span data-ttu-id="10ee0-115">Ausführlichere Informationen zur Verarbeitung einer Abfrage in EF Core finden Sie unter [Funktionsweise von Abfragen](xref:core/querying/how-query-works).</span><span class="sxs-lookup"><span data-stu-id="10ee0-115">For more detailed information on how a query is processed in EF Core, see [How queries Work](xref:core/querying/how-query-works).</span></span>
