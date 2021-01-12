---
title: Indizes-EF Core
description: Konfigurieren von Indizes in einem Entity Framework Core Modell
author: roji
ms.date: 12/16/2019
uid: core/modeling/indexes
ms.openlocfilehash: ab81b108c4ff518cf98b7e835da3553c0c41efed
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128536"
---
# <a name="indexes"></a><span data-ttu-id="9a90b-103">Indizes</span><span class="sxs-lookup"><span data-stu-id="9a90b-103">Indexes</span></span>

<span data-ttu-id="9a90b-104">Indizes sind ein gängiges Konzept in vielen Daten speichern.</span><span class="sxs-lookup"><span data-stu-id="9a90b-104">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="9a90b-105">Während Ihre Implementierung im Datenspeicher variieren kann, werden Sie verwendet, um Suchvorgänge auf Grundlage einer Spalte (oder einer Gruppe von Spalten) effizienter zu gestalten.</span><span class="sxs-lookup"><span data-stu-id="9a90b-105">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span> <span data-ttu-id="9a90b-106">Weitere Informationen zur guten Index Verwendung finden Sie im [Abschnitt "Indizes](xref:core/performance/efficient-querying#use-indexes-properly) " in der Leistungsdokumentation.</span><span class="sxs-lookup"><span data-stu-id="9a90b-106">See the [indexes section](xref:core/performance/efficient-querying#use-indexes-properly) in the performance documentation for more information on good index usage.</span></span>

<span data-ttu-id="9a90b-107">Sie können einen Index für eine Spalte wie folgt angeben:</span><span class="sxs-lookup"><span data-stu-id="9a90b-107">You can specify an index over a column as follows:</span></span>

## <a name="data-annotations"></a>[<span data-ttu-id="9a90b-108">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="9a90b-108">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Index.cs?name=Index&highlight=1)]

> [!NOTE]
> <span data-ttu-id="9a90b-109">Das Konfigurieren von Indizes über Daten Anmerkungen wurde in EF Core 5,0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="9a90b-109">Configuring indexes via Data Annotations has been introduced in EF Core 5.0.</span></span>

## <a name="fluent-api"></a>[<span data-ttu-id="9a90b-110">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="9a90b-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

***

> [!NOTE]
> <span data-ttu-id="9a90b-111">Gemäß der Konvention wird ein Index in jeder Eigenschaft (oder einem Satz von Eigenschaften) erstellt, die als Fremdschlüssel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9a90b-111">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>
>
> <span data-ttu-id="9a90b-112">EF Core unterstützt nur einen Index pro eindeutigem Satz von Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="9a90b-112">EF Core only supports one index per distinct set of properties.</span></span> <span data-ttu-id="9a90b-113">Wenn Sie einen Index für eine Gruppe von Eigenschaften konfigurieren, für die bereits ein Index festgelegt wurde (entweder durch Konvention oder vorherige Konfiguration), ändern Sie die Definition des Indexes.</span><span class="sxs-lookup"><span data-stu-id="9a90b-113">If you configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="9a90b-114">Dies ist hilfreich, wenn Sie einen von der Konvention erstellten Index weiter konfigurieren möchten.</span><span class="sxs-lookup"><span data-stu-id="9a90b-114">This is useful if you want to further configure an index that was created by convention.</span></span>

## <a name="composite-index"></a><span data-ttu-id="9a90b-115">Zusammengesetzter Index</span><span class="sxs-lookup"><span data-stu-id="9a90b-115">Composite index</span></span>

<span data-ttu-id="9a90b-116">Ein Index kann sich auch über mehrere Spalten erstrecken:</span><span class="sxs-lookup"><span data-stu-id="9a90b-116">An index can also span more than one column:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="9a90b-117">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="9a90b-117">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexComposite.cs?name=Composite&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="9a90b-118">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="9a90b-118">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

<span data-ttu-id="9a90b-119">\*\*_</span><span class="sxs-lookup"><span data-stu-id="9a90b-119">\*\*_</span></span>

<span data-ttu-id="9a90b-120">Indizes für mehrere Spalten, auch bekannt als _composite Indizes \*, beschleunigen Abfragen, die nach Index Spalten filtern, aber auch Abfragen, die nur nach den *ersten* Spalten filtern, die vom Index abgedeckt werden.</span><span class="sxs-lookup"><span data-stu-id="9a90b-120">Indexes over multiple columns, also known as _composite indexes\*, speed up queries which filter on index's columns, but also queries which only filter on the *first* columns covered by the index.</span></span> <span data-ttu-id="9a90b-121">Weitere Informationen finden Sie in der Dokumentation zur [Leistung](xref:core/performance/efficient-querying#use-indexes-properly) .</span><span class="sxs-lookup"><span data-stu-id="9a90b-121">See the [performance docs](xref:core/performance/efficient-querying#use-indexes-properly) for more information.</span></span>

## <a name="index-uniqueness"></a><span data-ttu-id="9a90b-122">Index Eindeutigkeit</span><span class="sxs-lookup"><span data-stu-id="9a90b-122">Index uniqueness</span></span>

<span data-ttu-id="9a90b-123">Standardmäßig sind Indizes nicht eindeutig: mehrere Zeilen dürfen für den Spalten Satz des Indexes die gleichen Werte aufweisen.</span><span class="sxs-lookup"><span data-stu-id="9a90b-123">By default, indexes aren't unique: multiple rows are allowed to have the same value(s) for the index's column set.</span></span> <span data-ttu-id="9a90b-124">Sie können einen Index wie folgt eindeutig gestalten:</span><span class="sxs-lookup"><span data-stu-id="9a90b-124">You can make an index unique as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="9a90b-125">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="9a90b-125">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexUnique.cs?name=IndexUnique&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="9a90b-126">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="9a90b-126">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

***

<span data-ttu-id="9a90b-127">Wenn Sie versuchen, mehr als eine Entität mit denselben Werten für den Spalten Satz des Indexes einzufügen, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="9a90b-127">Attempting to insert more than one entity with the same values for the index's column set will cause an exception to be thrown.</span></span>

## <a name="index-name"></a><span data-ttu-id="9a90b-128">Indexname</span><span class="sxs-lookup"><span data-stu-id="9a90b-128">Index name</span></span>

<span data-ttu-id="9a90b-129">Gemäß der Konvention werden Indizes, die in einer relationalen Datenbank erstellt werden, benannt `IX_<type name>_<property name>` .</span><span class="sxs-lookup"><span data-stu-id="9a90b-129">By convention, indexes created in a relational database are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="9a90b-130">Wird bei zusammengesetzten Indizes `<property name>` zu einer durch Trennzeichen getrennten Liste von Eigenschaftsnamen.</span><span class="sxs-lookup"><span data-stu-id="9a90b-130">For composite indexes, `<property name>` becomes an underscore separated list of property names.</span></span>

<span data-ttu-id="9a90b-131">Sie können den Namen des Indexes festlegen, der in der Datenbank erstellt wurde:</span><span class="sxs-lookup"><span data-stu-id="9a90b-131">You can set the name of the index created in the database:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="9a90b-132">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="9a90b-132">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexName.cs?name=IndexName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="9a90b-133">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="9a90b-133">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

***

## <a name="index-filter"></a><span data-ttu-id="9a90b-134">Index Filter</span><span class="sxs-lookup"><span data-stu-id="9a90b-134">Index filter</span></span>

<span data-ttu-id="9a90b-135">Einige relationale Datenbanken ermöglichen es Ihnen, einen gefilterten oder partiellen Index anzugeben.</span><span class="sxs-lookup"><span data-stu-id="9a90b-135">Some relational databases allow you to specify a filtered or partial index.</span></span> <span data-ttu-id="9a90b-136">Auf diese Weise können Sie nur eine Teilmenge der Spaltenwerte indizieren, die Größe des Indexes verringern und sowohl die Leistung als auch die Speicherplatz Auslastung verbessern.</span><span class="sxs-lookup"><span data-stu-id="9a90b-136">This allows you to index only a subset of a column's values, reducing the index's size and improving both performance and disk space usage.</span></span> <span data-ttu-id="9a90b-137">Weitere Informationen zu SQL Server gefilterten Indizes finden Sie in [der Dokumentation](/sql/relational-databases/indexes/create-filtered-indexes)zu.</span><span class="sxs-lookup"><span data-stu-id="9a90b-137">For more information on SQL Server filtered indexes, [see the documentation](/sql/relational-databases/indexes/create-filtered-indexes).</span></span>

<span data-ttu-id="9a90b-138">Sie können die fließende API verwenden, um einen Filter für einen Index anzugeben, der als SQL-Ausdruck bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="9a90b-138">You can use the Fluent API to specify a filter on an index, provided as a SQL expression:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

<span data-ttu-id="9a90b-139">Bei Verwendung des SQL Server Anbieters fügt EF einen `'IS NOT NULL'` Filter für alle Spalten hinzu, die NULL-Werte zulassen, die Teil eines eindeutigen Indexes sind.</span><span class="sxs-lookup"><span data-stu-id="9a90b-139">When using the SQL Server provider EF adds an `'IS NOT NULL'` filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="9a90b-140">Um diese Konvention zu überschreiben, können Sie einen `null` Wert angeben.</span><span class="sxs-lookup"><span data-stu-id="9a90b-140">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a><span data-ttu-id="9a90b-141">Eingeschlossene Spalten</span><span class="sxs-lookup"><span data-stu-id="9a90b-141">Included columns</span></span>

<span data-ttu-id="9a90b-142">Mit einigen relationalen Datenbanken können Sie eine Gruppe von Spalten konfigurieren, die in den Index aufgenommen werden, aber nicht Teil Ihres "Schlüssels" sind.</span><span class="sxs-lookup"><span data-stu-id="9a90b-142">Some relational databases allow you to configure a set of columns which get included in the index, but aren't part of its "key".</span></span> <span data-ttu-id="9a90b-143">Dies kann die Abfrageleistung erheblich verbessern, wenn alle Spalten in der Abfrage in den Index als Schlüssel-oder nicht Schlüssel Spalten eingeschlossen werden, da auf die Tabelle selbst nicht zugegriffen werden muss.</span><span class="sxs-lookup"><span data-stu-id="9a90b-143">This can significantly improve query performance when all columns in the query are included in the index either as key or nonkey columns, as the table itself doesn't need to be accessed.</span></span> <span data-ttu-id="9a90b-144">Weitere Informationen zu SQL Server enthaltenen Spalten finden Sie in [der Dokumentation](/sql/relational-databases/indexes/create-indexes-with-included-columns)zu.</span><span class="sxs-lookup"><span data-stu-id="9a90b-144">For more information on SQL Server included columns, [see the documentation](/sql/relational-databases/indexes/create-indexes-with-included-columns).</span></span>

<span data-ttu-id="9a90b-145">Im folgenden Beispiel `Url` ist die Spalte Teil des Index Schlüssels, sodass jede Abfrage Filterung für diese Spalte den Index verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="9a90b-145">In the following example, the `Url` column is part of the index key, so any query filtering on that column can use the index.</span></span> <span data-ttu-id="9a90b-146">Außerdem müssen Abfragen, die nur auf die `Title` Spalten und zugreifen, `PublishedOn` nicht auf die Tabelle zugreifen und effizienter ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="9a90b-146">But in addition, queries accessing only the `Title` and `PublishedOn` columns will not need to access the table and will run more efficiently:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
