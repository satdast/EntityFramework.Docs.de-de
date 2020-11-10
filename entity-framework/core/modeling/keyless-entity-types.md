---
title: Typen von Keyless-Entitäten-EF Core
description: Konfigurieren von schlüssellosen Entitäts Typen mithilfe von Entity Framework Core
author: AndriySvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: d1a60e0504b22623b97c1a4963d2e3f70faa365c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429506"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="e0563-103">Schlüssellose Entitätstypen</span><span class="sxs-lookup"><span data-stu-id="e0563-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="e0563-104">Diese Funktion wurde unter dem Namen der Abfrage Typen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e0563-104">This feature was added under the name of query types.</span></span> <span data-ttu-id="e0563-105">In EF Core 3,0 wurde das Konzept in schlüssellose Entitäts Typen umbenannt.</span><span class="sxs-lookup"><span data-stu-id="e0563-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span> <span data-ttu-id="e0563-106">Die `[Keyless]` Daten Anmerkung wurde in efcore 5,0 verfügbar.</span><span class="sxs-lookup"><span data-stu-id="e0563-106">The `[Keyless]` Data Annotation became available in EFCore 5.0.</span></span>

<span data-ttu-id="e0563-107">Zusätzlich zu regulären Entitäts Typen kann ein EF Core Modell _Schlüssel-Entitäts Typen_ enthalten, die verwendet werden können, um Datenbankabfragen für Daten auszuführen, die keine Schlüsselwerte enthalten.</span><span class="sxs-lookup"><span data-stu-id="e0563-107">In addition to regular entity types, an EF Core model can contain _keyless entity types_ , which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="defining-keyless-entity-types"></a><span data-ttu-id="e0563-108">Definieren von schlüssellosen Entitäts Typen</span><span class="sxs-lookup"><span data-stu-id="e0563-108">Defining Keyless entity types</span></span>

<span data-ttu-id="e0563-109">Schlüssellose Entitäts Typen können entweder mithilfe der Daten Anmerkung oder der fließenden API definiert werden:</span><span class="sxs-lookup"><span data-stu-id="e0563-109">Keyless entity types can be defined using either the Data Annotation or the Fluent API:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="e0563-110">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="e0563-110">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="e0563-111">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="e0563-111">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="e0563-112">Merkmale von Keyless-Entitäts Typen</span><span class="sxs-lookup"><span data-stu-id="e0563-112">Keyless entity types characteristics</span></span>

<span data-ttu-id="e0563-113">Schlüssellose Entitäts Typen unterstützen viele der gleichen Zuordnungs Funktionen wie reguläre Entitäts Typen wie Vererbungs Zuordnung und Navigations Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="e0563-113">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="e0563-114">In relationalen speichern können Sie die Ziel Datenbankobjekte und-Spalten überfließende API-Methoden oder Daten Anmerkungen konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="e0563-114">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="e0563-115">Sie unterscheiden sich jedoch von regulären Entitäts Typen darin, dass Sie:</span><span class="sxs-lookup"><span data-stu-id="e0563-115">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="e0563-116">Ein Schlüssel kann nicht definiert werden.</span><span class="sxs-lookup"><span data-stu-id="e0563-116">Cannot have a key defined.</span></span>
- <span data-ttu-id="e0563-117">Werden nie nach Änderungen im _dbcontext_ nachverfolgt und daher nie in der Datenbank eingefügt, aktualisiert oder gelöscht.</span><span class="sxs-lookup"><span data-stu-id="e0563-117">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="e0563-118">Werden nie nach Konvention ermittelt.</span><span class="sxs-lookup"><span data-stu-id="e0563-118">Are never discovered by convention.</span></span>
- <span data-ttu-id="e0563-119">Unterstützt nur eine Teilmenge der Navigations Zuordnungsfunktionen, insbesondere:</span><span class="sxs-lookup"><span data-stu-id="e0563-119">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="e0563-120">Sie werden möglicherweise nie als Prinzipal Ende einer Beziehung fungieren.</span><span class="sxs-lookup"><span data-stu-id="e0563-120">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="e0563-121">Sie haben möglicherweise keine Navigation zu besitzende Entitäten.</span><span class="sxs-lookup"><span data-stu-id="e0563-121">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="e0563-122">Sie können nur Verweis Navigations Eigenschaften enthalten, die auf reguläre Entitäten verweisen.</span><span class="sxs-lookup"><span data-stu-id="e0563-122">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="e0563-123">Entitäten können keine Navigations Eigenschaften für Entitäts Typen ohne Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="e0563-123">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="e0563-124">Muss mit einer `[Keyless]` Daten Anmerkung oder einem Methoden Befehl konfiguriert werden `.HasNoKey()` .</span><span class="sxs-lookup"><span data-stu-id="e0563-124">Need to be configured with a `[Keyless]` data annotation or a `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="e0563-125">Kann einer _definierenden Abfrage_ zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="e0563-125">May be mapped to a _defining query_.</span></span> <span data-ttu-id="e0563-126">Eine definierende Abfrage ist eine im Modell deklarierte Abfrage, die als Datenquelle für einen schlüssellosen Entitätstyp fungiert.</span><span class="sxs-lookup"><span data-stu-id="e0563-126">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="e0563-127">Verwendungsszenarios</span><span class="sxs-lookup"><span data-stu-id="e0563-127">Usage scenarios</span></span>

<span data-ttu-id="e0563-128">Einige der wichtigsten Verwendungs Szenarien für Typen von schlüssellosen Entitäten sind:</span><span class="sxs-lookup"><span data-stu-id="e0563-128">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="e0563-129">Fungieren als Rückgabetyp für unformatierte [SQL-Abfragen](xref:core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="e0563-129">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="e0563-130">Zuordnung zu Daten Bank Sichten, die keinen Primärschlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="e0563-130">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="e0563-131">Zuordnung zu Tabellen, für die kein Primärschlüssel definiert ist.</span><span class="sxs-lookup"><span data-stu-id="e0563-131">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="e0563-132">Zuordnung zu Abfragen, die im Modell definiert sind.</span><span class="sxs-lookup"><span data-stu-id="e0563-132">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="e0563-133">Zuordnung zu Datenbankobjekten</span><span class="sxs-lookup"><span data-stu-id="e0563-133">Mapping to database objects</span></span>

<span data-ttu-id="e0563-134">Die Zuordnung eines Entitäts Typs mit einer anderen Tastatur zu einem Datenbankobjekt erfolgt mithilfe der-oder-über `ToTable` `ToView` flüssigen API.</span><span class="sxs-lookup"><span data-stu-id="e0563-134">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="e0563-135">Aus Sicht der EF Core ist das in dieser Methode angegebene Datenbankobjekt eine _Sicht_. Dies bedeutet, dass es als schreibgeschützte Abfrage Quelle behandelt wird und nicht das Ziel von Aktualisierungs-, Einfüge-oder Lösch Vorgängen sein kann.</span><span class="sxs-lookup"><span data-stu-id="e0563-135">From the perspective of EF Core, the database object specified in this method is a _view_ , meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="e0563-136">Dies bedeutet jedoch nicht, dass das Datenbankobjekt tatsächlich eine Daten Bank Sicht sein muss.</span><span class="sxs-lookup"><span data-stu-id="e0563-136">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="e0563-137">Alternativ kann es sich um eine Datenbanktabelle handeln, die als schreibgeschützt behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="e0563-137">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="e0563-138">Im Gegensatz dazu geht EF Core bei regulären Entitäts Typen davon aus, dass ein in der-Methode angegebenes Datenbankobjekt `ToTable` als _Tabelle_ behandelt werden kann. Dies bedeutet, dass es als Abfrage Quelle, aber auch als Ziel für Update-, DELETE-und INSERT-Vorgänge verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="e0563-138">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_ , meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="e0563-139">In der Tat können Sie den Namen einer Daten Bank Sicht in angeben, `ToTable` und alles sollte einwandfrei funktionieren, solange die Sicht so konfiguriert ist, dass Sie für die Datenbank aktualisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="e0563-139">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="e0563-140">`ToView` geht davon aus, dass das Objekt bereits in der Datenbank vorhanden ist, und wird nicht durch Migrationen erstellt.</span><span class="sxs-lookup"><span data-stu-id="e0563-140">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="e0563-141">Beispiel</span><span class="sxs-lookup"><span data-stu-id="e0563-141">Example</span></span>

<span data-ttu-id="e0563-142">Das folgende Beispiel zeigt, wie Sie schlüssellose Entitäts Typen verwenden, um eine Daten Bank Sicht abzufragen.</span><span class="sxs-lookup"><span data-stu-id="e0563-142">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="e0563-143">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="e0563-143">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="e0563-144">Zunächst definieren wir einen einfachen Blog und ein Post-Modell:</span><span class="sxs-lookup"><span data-stu-id="e0563-144">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="e0563-145">Als nächstes definieren wir eine einfache Daten Bank Ansicht, die es uns ermöglicht, die Anzahl von Beiträgen abzufragen, die mit den einzelnen Blogs verknüpft sind:</span><span class="sxs-lookup"><span data-stu-id="e0563-145">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="e0563-146">Als nächstes definieren wir eine Klasse, die das Ergebnis aus der Daten Bank Sicht enthält:</span><span class="sxs-lookup"><span data-stu-id="e0563-146">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="e0563-147">Als Nächstes konfigurieren wir den Entitätstyp "Schlüssel" in " _onmodelcreating_ " mithilfe der `HasNoKey` API.</span><span class="sxs-lookup"><span data-stu-id="e0563-147">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="e0563-148">Wir verwenden die fließende Konfigurations-API, um die Zuordnung für den Entitätstyp "Schlüssel" zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="e0563-148">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="e0563-149">Als Nächstes konfigurieren wir den so, dass er `DbContext` Folgendes einschließt `DbSet<T>` :</span><span class="sxs-lookup"><span data-stu-id="e0563-149">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="e0563-150">Schließlich können wir die Daten Bank Sicht standardmäßig Abfragen:</span><span class="sxs-lookup"><span data-stu-id="e0563-150">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="e0563-151">Beachten Sie, dass wir auch eine Kontext Stufen-Abfrage Eigenschaft (dbset) definiert haben, die als Stamm für Abfragen dieses Typs fungiert.</span><span class="sxs-lookup"><span data-stu-id="e0563-151">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>

> [!TIP]
> <span data-ttu-id="e0563-152">Um Schlüssel-Entitäts Typen, die Ansichten zugeordnet sind, mithilfe des Speicher internen Anbieters zu testen, ordnen Sie Sie einer Abfrage über zu `ToInMemoryQuery` .</span><span class="sxs-lookup"><span data-stu-id="e0563-152">To test keyless entity types mapped to views using the in-memory provider map them to a query via `ToInMemoryQuery`.</span></span> <span data-ttu-id="e0563-153">Weitere Informationen finden Sie unter [ausführbares Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) mit dieser Technik.</span><span class="sxs-lookup"><span data-stu-id="e0563-153">See a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) using this technique for more details.</span></span>
