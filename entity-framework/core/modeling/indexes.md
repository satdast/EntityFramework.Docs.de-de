---
title: Indizes-EF Core
description: Konfigurieren von Indizes in einem Entity Framework Core Modell
author: roji
ms.date: 12/16/2019
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: 1ce40a9219dde56478b1ff8891841b53d9ba1934
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619360"
---
# <a name="indexes"></a><span data-ttu-id="12132-103">Indizes</span><span class="sxs-lookup"><span data-stu-id="12132-103">Indexes</span></span>

<span data-ttu-id="12132-104">Indizes sind ein gängiges Konzept in vielen Daten speichern.</span><span class="sxs-lookup"><span data-stu-id="12132-104">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="12132-105">Während Ihre Implementierung im Datenspeicher variieren kann, werden Sie verwendet, um Suchvorgänge auf Grundlage einer Spalte (oder einer Gruppe von Spalten) effizienter zu gestalten.</span><span class="sxs-lookup"><span data-stu-id="12132-105">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

<span data-ttu-id="12132-106">Indizes können nicht mithilfe von Daten Anmerkungen erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="12132-106">Indexes cannot be created using data annotations.</span></span> <span data-ttu-id="12132-107">Sie können die fließende API verwenden, um einen Index für eine einzelne Spalte wie folgt anzugeben:</span><span class="sxs-lookup"><span data-stu-id="12132-107">You can use the Fluent API to specify an index on a single column as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

<span data-ttu-id="12132-108">Sie können einen Index auch über mehr als eine Spalte angeben:</span><span class="sxs-lookup"><span data-stu-id="12132-108">You can also specify an index over more than one column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

> [!NOTE]
> <span data-ttu-id="12132-109">Gemäß der Konvention wird ein Index in jeder Eigenschaft (oder einem Satz von Eigenschaften) erstellt, die als Fremdschlüssel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="12132-109">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>
>
> <span data-ttu-id="12132-110">EF Core unterstützt nur einen Index pro eindeutigem Satz von Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="12132-110">EF Core only supports one index per distinct set of properties.</span></span> <span data-ttu-id="12132-111">Wenn Sie die fließende API verwenden, um einen Index für eine Gruppe von Eigenschaften zu konfigurieren, für die bereits ein Index definiert wurde (entweder durch Konvention oder vorherige Konfiguration), ändern Sie die Definition des Indexes.</span><span class="sxs-lookup"><span data-stu-id="12132-111">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="12132-112">Dies ist hilfreich, wenn Sie einen von der Konvention erstellten Index weiter konfigurieren möchten.</span><span class="sxs-lookup"><span data-stu-id="12132-112">This is useful if you want to further configure an index that was created by convention.</span></span>

## <a name="index-uniqueness"></a><span data-ttu-id="12132-113">Index Eindeutigkeit</span><span class="sxs-lookup"><span data-stu-id="12132-113">Index uniqueness</span></span>

<span data-ttu-id="12132-114">Standardmäßig sind Indizes nicht eindeutig: mehrere Zeilen dürfen für den Spalten Satz des Indexes die gleichen Werte aufweisen.</span><span class="sxs-lookup"><span data-stu-id="12132-114">By default, indexes aren't unique: multiple rows are allowed to have the same value(s) for the index's column set.</span></span> <span data-ttu-id="12132-115">Sie können einen Index wie folgt eindeutig gestalten:</span><span class="sxs-lookup"><span data-stu-id="12132-115">You can make an index unique as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

<span data-ttu-id="12132-116">Wenn Sie versuchen, mehr als eine Entität mit denselben Werten für den Spalten Satz des Indexes einzufügen, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="12132-116">Attempting to insert more than one entity with the same values for the index's column set will cause an exception to be thrown.</span></span>

## <a name="index-name"></a><span data-ttu-id="12132-117">Indexname</span><span class="sxs-lookup"><span data-stu-id="12132-117">Index name</span></span>

<span data-ttu-id="12132-118">Gemäß der Konvention werden Indizes, die in einer relationalen Datenbank erstellt werden, benannt `IX_<type name>_<property name>` .</span><span class="sxs-lookup"><span data-stu-id="12132-118">By convention, indexes created in a relational database are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="12132-119">Wird bei zusammengesetzten Indizes `<property name>` zu einer durch Trennzeichen getrennten Liste von Eigenschaftsnamen.</span><span class="sxs-lookup"><span data-stu-id="12132-119">For composite indexes, `<property name>` becomes an underscore separated list of property names.</span></span>

<span data-ttu-id="12132-120">Mit der flüssigen API können Sie den Namen des Indexes festlegen, der in der Datenbank erstellt wurde:</span><span class="sxs-lookup"><span data-stu-id="12132-120">You can use the Fluent API to set the name of the index created in the database:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

## <a name="index-filter"></a><span data-ttu-id="12132-121">Index Filter</span><span class="sxs-lookup"><span data-stu-id="12132-121">Index filter</span></span>

<span data-ttu-id="12132-122">Einige relationale Datenbanken ermöglichen es Ihnen, einen gefilterten oder partiellen Index anzugeben.</span><span class="sxs-lookup"><span data-stu-id="12132-122">Some relational databases allow you to specify a filtered or partial index.</span></span> <span data-ttu-id="12132-123">Auf diese Weise können Sie nur eine Teilmenge der Spaltenwerte indizieren, die Größe des Indexes verringern und sowohl die Leistung als auch die Speicherplatz Auslastung verbessern.</span><span class="sxs-lookup"><span data-stu-id="12132-123">This allows you to index only a subset of a column's values, reducing the index's size and improving both performance and disk space usage.</span></span> <span data-ttu-id="12132-124">Weitere Informationen zu SQL Server gefilterten Indizes finden Sie in [der Dokumentation](/sql/relational-databases/indexes/create-filtered-indexes)zu.</span><span class="sxs-lookup"><span data-stu-id="12132-124">For more information on SQL Server filtered indexes, [see the documentation](/sql/relational-databases/indexes/create-filtered-indexes).</span></span>

<span data-ttu-id="12132-125">Sie können die fließende API verwenden, um einen Filter für einen Index anzugeben, der als SQL-Ausdruck bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="12132-125">You can use the Fluent API to specify a filter on an index, provided as a SQL expression:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

<span data-ttu-id="12132-126">Bei Verwendung des SQL Server Anbieters fügt EF einen `'IS NOT NULL'` Filter für alle Spalten hinzu, die NULL-Werte zulassen, die Teil eines eindeutigen Indexes sind.</span><span class="sxs-lookup"><span data-stu-id="12132-126">When using the SQL Server provider EF adds an `'IS NOT NULL'` filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="12132-127">Um diese Konvention zu überschreiben, können Sie einen `null` Wert angeben.</span><span class="sxs-lookup"><span data-stu-id="12132-127">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a><span data-ttu-id="12132-128">Eingeschlossene Spalten</span><span class="sxs-lookup"><span data-stu-id="12132-128">Included columns</span></span>

<span data-ttu-id="12132-129">Mit einigen relationalen Datenbanken können Sie eine Gruppe von Spalten konfigurieren, die in den Index aufgenommen werden, aber nicht Teil Ihres "Schlüssels" sind.</span><span class="sxs-lookup"><span data-stu-id="12132-129">Some relational databases allow you to configure a set of columns which get included in the index, but aren't part of its "key".</span></span> <span data-ttu-id="12132-130">Dies kann die Abfrageleistung erheblich verbessern, wenn alle Spalten in der Abfrage in den Index als Schlüssel-oder nicht Schlüssel Spalten eingeschlossen werden, da auf die Tabelle selbst nicht zugegriffen werden muss.</span><span class="sxs-lookup"><span data-stu-id="12132-130">This can significantly improve query performance when all columns in the query are included in the index either as key or nonkey columns, as the table itself doesn't need to be accessed.</span></span> <span data-ttu-id="12132-131">Weitere Informationen zu SQL Server enthaltenen Spalten finden Sie in [der Dokumentation](/sql/relational-databases/indexes/create-indexes-with-included-columns)zu.</span><span class="sxs-lookup"><span data-stu-id="12132-131">For more information on SQL Server included columns, [see the documentation](/sql/relational-databases/indexes/create-indexes-with-included-columns).</span></span>

<span data-ttu-id="12132-132">Im folgenden Beispiel `Url` ist die Spalte Teil des Index Schlüssels, sodass jede Abfrage Filterung für diese Spalte den Index verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="12132-132">In the following example, the `Url` column is part of the index key, so any query filtering on that column can use the index.</span></span> <span data-ttu-id="12132-133">Außerdem müssen Abfragen, die nur auf die `Title` Spalten und zugreifen, `PublishedOn` nicht auf die Tabelle zugreifen und effizienter ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="12132-133">But in addition, queries accessing only the `Title` and `PublishedOn` columns will not need to access the table and will run more efficiently:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
