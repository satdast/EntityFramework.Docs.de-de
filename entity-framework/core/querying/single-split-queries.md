---
title: 'Vergleich von Einzel- und geteilten Abfragen: EF Core'
description: Übersetzen von Abfragen in einfache und geteilte Abfragen in SQL mit Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/single-split-queries
ms.openlocfilehash: 8615e7ba5247a90a1f980e9f7b1b23c81170971f
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128991"
---
# <a name="split-queries"></a><span data-ttu-id="b680f-103">Geteilte Abfragen</span><span class="sxs-lookup"><span data-stu-id="b680f-103">Split queries</span></span>

## <a name="single-queries"></a><span data-ttu-id="b680f-104">Einzelabfragen</span><span class="sxs-lookup"><span data-stu-id="b680f-104">Single queries</span></span>

<span data-ttu-id="b680f-105">In relationalen Datenbanken werden alle zugehörigen Entitäten standardmäßig durch Einführung von JOIN-Vorgängen in eine Einzelabfrage geladen.</span><span class="sxs-lookup"><span data-stu-id="b680f-105">In relational databases, all related entities are loaded by introducing JOINs in single query.</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="b680f-106">Wenn ein typischer Blog mehrere zugehörige Beiträge enthält, werden in den Zeilen für diese Beiträge die Informationen des Blogs dupliziert.</span><span class="sxs-lookup"><span data-stu-id="b680f-106">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information.</span></span> <span data-ttu-id="b680f-107">Diese Duplizierung führt zum so genannten Problem der „kartesischen Explosion“.</span><span class="sxs-lookup"><span data-stu-id="b680f-107">This duplication leads to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="b680f-108">Wenn weitere 1:n-Beziehungen geladen werden, wächst die Menge an duplizierten Daten weiter und beeinträchtigt die Leistung Ihrer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="b680f-108">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span>

## <a name="split-queries"></a><span data-ttu-id="b680f-109">Geteilte Abfragen</span><span class="sxs-lookup"><span data-stu-id="b680f-109">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="b680f-110">Dieses Feature wurde in EF Core 5.0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="b680f-110">This feature was introduced in EF Core 5.0.</span></span> <span data-ttu-id="b680f-111">Sie funktioniert nur bei Verwenden von `Include`.</span><span class="sxs-lookup"><span data-stu-id="b680f-111">It only works when using `Include`.</span></span> <span data-ttu-id="b680f-112">[Dieses Problem](https://github.com/dotnet/efcore/issues/21234) stellt die Nachverfolgung der Unterstützung für geteilte Abfragen beim Laden zugehöriger Daten in die Projektion ohne `Include` dar.</span><span class="sxs-lookup"><span data-stu-id="b680f-112">[This issue](https://github.com/dotnet/efcore/issues/21234) is tracking support for split query when loading related data in projection without `Include`.</span></span>

<span data-ttu-id="b680f-113">In EF können Sie angeben, dass eine bestimmte LINQ-Abfrage in auf mehrere SQL-Abfragen *aufgeteilt* werden soll.</span><span class="sxs-lookup"><span data-stu-id="b680f-113">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="b680f-114">Anstelle von JOIN-Vorgängen generieren geteilte Abfragen für jede enthaltene Sammlungsnavigation eine zusätzliche SQL-Abfrage:</span><span class="sxs-lookup"><span data-stu-id="b680f-114">Instead of JOINs, split queries generate an additional SQL query for each included collection navigation:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="b680f-115">Dabei wird der folgende SQL-Code erzeugt:</span><span class="sxs-lookup"><span data-stu-id="b680f-115">It will produce the following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> <span data-ttu-id="b680f-116">Entitäten mit 1:1-Beziehung werden stets über JOIN-Vorgänge in dieselbe Abfrage geladen, da dies keine Auswirkungen auf die Leistung hat.</span><span class="sxs-lookup"><span data-stu-id="b680f-116">One-to-one related entities are always loaded via JOINs in the same query, as it has no performance impact.</span></span>

## <a name="enabling-split-queries-globally"></a><span data-ttu-id="b680f-117">Globales Aktivieren von geteilten Abfragen</span><span class="sxs-lookup"><span data-stu-id="b680f-117">Enabling split queries globally</span></span>

<span data-ttu-id="b680f-118">Sie können auch geteilte Abfragen als Standardeinstellung für den Kontext Ihrer Anwendung konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="b680f-118">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="b680f-119">Wenn geteilte Abfragen als Standardeinstellung konfiguriert werden, ist es dennoch möglich, bestimmte Abfragen so zu konfigurieren, dass sie als einzelne Abfragen ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="b680f-119">When split queries are configured as the default, it's still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

<span data-ttu-id="b680f-120">EF Core verwendet beim Fehlen jeglicher Konfiguration standardmäßig den Einzelabfragemodus.</span><span class="sxs-lookup"><span data-stu-id="b680f-120">EF Core uses single query mode by default in the absence of any configuration.</span></span> <span data-ttu-id="b680f-121">Da dies zu Leistungsproblemen führen kann, generiert EF Core immer dann eine Warnung, wenn die folgenden Bedingungen vorliegen:</span><span class="sxs-lookup"><span data-stu-id="b680f-121">Since it may cause performance issues, EF Core generates a warning whenever following conditions are met:</span></span>

- <span data-ttu-id="b680f-122">EF Core erkennt, dass die Abfrage mehrere Sammlungen lädt.</span><span class="sxs-lookup"><span data-stu-id="b680f-122">EF Core detects that the query loads multiple collections.</span></span>
- <span data-ttu-id="b680f-123">Der Benutzer hat den Abfrageteilungsmodus nicht global konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="b680f-123">User hasn't configured query splitting mode globally.</span></span>
- <span data-ttu-id="b680f-124">Der Benutzer hat den Operator `AsSingleQuery`/`AsSplitQuery`nicht auf die Abfrage angewendet.</span><span class="sxs-lookup"><span data-stu-id="b680f-124">User hasn't used `AsSingleQuery`/`AsSplitQuery` operator on the query.</span></span>

<span data-ttu-id="b680f-125">Um die Warnung zu deaktivieren, konfigurieren Sie den Abfrageteilungsmodus global oder auf Abfrageebene mit einem geeigneten Wert.</span><span class="sxs-lookup"><span data-stu-id="b680f-125">To turn off the warning, configure query splitting mode globally or at the query level to an appropriate value.</span></span>

## <a name="characteristics-of-split-queries"></a><span data-ttu-id="b680f-126">Merkmale von geteilten Abfragen</span><span class="sxs-lookup"><span data-stu-id="b680f-126">Characteristics of split queries</span></span>

<span data-ttu-id="b680f-127">Zwar werden bei geteilten Abfragen die Leistungsprobleme im Zusammenhang mit Verknüpfungen und der kartesischen Explosion vermieden, allerdings weist dieser Modus auch einige Nachteile auf:</span><span class="sxs-lookup"><span data-stu-id="b680f-127">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

- <span data-ttu-id="b680f-128">Die meisten Datenbanken garantieren Datenkonsistenz bei einzelnen Abfragen. Bei mehreren Abfragen gibt es eine solche Garantie nicht.</span><span class="sxs-lookup"><span data-stu-id="b680f-128">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="b680f-129">Wenn die Datenbank während dem Ausführen der Abfragen aktualisiert wird, sind die resultierenden Daten möglicherweise nicht konsistent.</span><span class="sxs-lookup"><span data-stu-id="b680f-129">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="b680f-130">Dieses Problem können Sie minimieren, indem Sie die Abfragen mit einer serialisierbaren Transaktion oder Momentaufnahmentransaktion umschließen. Allerdings kann auch dieses Vorgehen Leistungsprobleme nach sich ziehen.</span><span class="sxs-lookup"><span data-stu-id="b680f-130">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="b680f-131">Weitere Informationen finden Sie in der Dokumentation Ihrer Datenbank.</span><span class="sxs-lookup"><span data-stu-id="b680f-131">For more information, see your database's documentation.</span></span>
- <span data-ttu-id="b680f-132">Jede Abfrage impliziert derzeit einen zusätzlichen Netzwerkroundtrip zu Ihrer Datenbank.</span><span class="sxs-lookup"><span data-stu-id="b680f-132">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="b680f-133">Durch mehrere Netzwerkroundtrips kann die Leistung beeinträchtigt werden, insbesondere, wenn die Latenz bei der Datenbank hoch ist (z. B. bei Clouddiensten).</span><span class="sxs-lookup"><span data-stu-id="b680f-133">Multiple network roundtrip can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
- <span data-ttu-id="b680f-134">Zwar erlauben einige Datenbanken die gleichzeitige Nutzung der Ergebnisse mehrerer Abfragen (SQL Server mit MARS, Sqlite), aber in den meisten darf zu jedem Zeitpunkt immer nur eine Abfrage aktiv sein.</span><span class="sxs-lookup"><span data-stu-id="b680f-134">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="b680f-135">Folglich müssen alle Ergebnisse früherer Abfragen im Arbeitsspeicher der Anwendung gepuffert werden, bevor spätere Abfragen ausgeführt werden. Dadurch steigen die Arbeitsspeicheranforderungen.</span><span class="sxs-lookup"><span data-stu-id="b680f-135">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="b680f-136">Leider gibt es nicht die eine perfekte Strategie zum Laden von zugehörigen Entitäten, die in allen Szenarien passt.</span><span class="sxs-lookup"><span data-stu-id="b680f-136">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="b680f-137">Wägen Sie sehr sorgfältig die Vor- und Nachteile einzelner und geteilter Abfragen ab, und wählen Sie die Variante aus, die sich für Ihre Anforderungen besser eignet.</span><span class="sxs-lookup"><span data-stu-id="b680f-137">Carefully consider the advantages and disadvantages of single and split queries to select the one that fits your needs.</span></span>
