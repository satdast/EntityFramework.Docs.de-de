---
title: Schlüssellose Entitätstypen - EF Core
description: Konfigurieren schlüsselloser Entitätstypen mithilfe von Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 496e1e8983ba2d5e15dbee02607ea3f2c861503e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434213"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="d6531-103">Schlüssellose Entitätstypen</span><span class="sxs-lookup"><span data-stu-id="d6531-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="d6531-104">Diese Funktion wurde in EF Core 2.1 unter dem Namen der Abfragetypen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d6531-104">This feature was added in EF Core 2.1 under the name of query types.</span></span> <span data-ttu-id="d6531-105">In EF Core 3.0 wurde das Konzept in schlüssellose Entitätstypen umbenannt.</span><span class="sxs-lookup"><span data-stu-id="d6531-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span>

<span data-ttu-id="d6531-106">Zusätzlich zu den regulären Entitätstypen kann ein EF Core-Modell _schlüssellose Entitätstypen_enthalten, die zum Ausführen von Datenbankabfragen für Daten verwendet werden können, die keine Schlüsselwerte enthalten.</span><span class="sxs-lookup"><span data-stu-id="d6531-106">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="defining-keyless-entity-types"></a><span data-ttu-id="d6531-107">Definieren von Schlüssellosen Entitätstypen</span><span class="sxs-lookup"><span data-stu-id="d6531-107">Defining Keyless entity types</span></span>

<span data-ttu-id="d6531-108">Schlüssellose Entitätstypen können entweder mit der Datenanmerkung oder der Fluent-API definiert werden:</span><span class="sxs-lookup"><span data-stu-id="d6531-108">Keyless entity types can be defined using either the Data Annotation or the Fluent API:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="d6531-109">Datenanmerkungen</span><span class="sxs-lookup"><span data-stu-id="d6531-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="d6531-110">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="d6531-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="d6531-111">Merkmale ohne Schlüsselentitätstypen</span><span class="sxs-lookup"><span data-stu-id="d6531-111">Keyless entity types characteristics</span></span>

<span data-ttu-id="d6531-112">Schlüssellose Entitätstypen unterstützen viele der gleichen Zuordnungsfunktionen wie normale Entitätstypen, z. B. Vererbungszuordnung und Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="d6531-112">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="d6531-113">In relationalen Speichern können sie die Zieldatenbankobjekte und -spalten über fließende API-Methoden oder Datenanmerkungen konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="d6531-113">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="d6531-114">Sie unterscheiden sich jedoch von regulären Entitätstypen dadurch, dass sie:</span><span class="sxs-lookup"><span data-stu-id="d6531-114">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="d6531-115">Es kann kein Schlüssel definiert werden.</span><span class="sxs-lookup"><span data-stu-id="d6531-115">Cannot have a key defined.</span></span>
- <span data-ttu-id="d6531-116">Werden nie nach Änderungen im _DbContext_ nachverfolgt und daher nie in die Datenbank eingefügt, aktualisiert oder gelöscht.</span><span class="sxs-lookup"><span data-stu-id="d6531-116">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="d6531-117">Werden nie durch Konvention entdeckt.</span><span class="sxs-lookup"><span data-stu-id="d6531-117">Are never discovered by convention.</span></span>
- <span data-ttu-id="d6531-118">Unterstützen Sie nur eine Teilmenge der Navigationszuordnungsfunktionen, insbesondere:</span><span class="sxs-lookup"><span data-stu-id="d6531-118">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="d6531-119">Sie dürfen niemals als wichtigstes Ende einer Beziehung fungieren.</span><span class="sxs-lookup"><span data-stu-id="d6531-119">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="d6531-120">Sie verfügen möglicherweise nicht über Navigationsgeräte an eigene Unternehmen.</span><span class="sxs-lookup"><span data-stu-id="d6531-120">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="d6531-121">Sie können nur Referenznavigationseigenschaften enthalten, die auf reguläre Entitäten verweisen.</span><span class="sxs-lookup"><span data-stu-id="d6531-121">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="d6531-122">Entitäten können keine Navigationseigenschaften für schlüssellose Entitätstypen enthalten.</span><span class="sxs-lookup"><span data-stu-id="d6531-122">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="d6531-123">Sie müssen mit einer `[Keyless]` Datenanmerkung oder `.HasNoKey()` einem Methodenaufruf konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="d6531-123">Need to be configured with a `[Keyless]` data annotation or a `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="d6531-124">Kann einer _definierenden Abfrage_zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="d6531-124">May be mapped to a _defining query_.</span></span> <span data-ttu-id="d6531-125">Eine definierende Abfrage ist eine Abfrage, die im Modell deklariert wird und als Datenquelle für einen schlüssellosen Entitätstyp fungiert.</span><span class="sxs-lookup"><span data-stu-id="d6531-125">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="d6531-126">Verwendungsszenarios</span><span class="sxs-lookup"><span data-stu-id="d6531-126">Usage scenarios</span></span>

<span data-ttu-id="d6531-127">Einige der wichtigsten Verwendungsszenarien für schlüssellose Entitätstypen sind:</span><span class="sxs-lookup"><span data-stu-id="d6531-127">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="d6531-128">Dient als Rückgabetyp für [unformatierte SQL-Abfragen](xref:core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="d6531-128">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="d6531-129">Zuordnen zu Datenbankansichten, die keinen Primärschlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="d6531-129">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="d6531-130">Zuordnung zu Tabellen, für die kein Primärschlüssel definiert ist.</span><span class="sxs-lookup"><span data-stu-id="d6531-130">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="d6531-131">Zuordnen zu Abfragen, die im Modell definiert sind.</span><span class="sxs-lookup"><span data-stu-id="d6531-131">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="d6531-132">Zuordnen zu Datenbankobjekten</span><span class="sxs-lookup"><span data-stu-id="d6531-132">Mapping to database objects</span></span>

<span data-ttu-id="d6531-133">Das Zuordnen eines schlüssellosen Entitätstyps `ToTable` zu `ToView` einem Datenbankobjekt wird mithilfe der oder fließenden API erreicht.</span><span class="sxs-lookup"><span data-stu-id="d6531-133">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="d6531-134">Aus der Sicht von EF Core ist das in dieser Methode angegebene Datenbankobjekt eine _Ansicht_, was bedeutet, dass es als schreibgeschützte Abfragequelle behandelt wird und nicht das Ziel von Aktualisierungs-, Einfüge- oder Löschvorgängen sein kann.</span><span class="sxs-lookup"><span data-stu-id="d6531-134">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="d6531-135">Dies bedeutet jedoch nicht, dass das Datenbankobjekt tatsächlich als Datenbankansicht erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="d6531-135">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="d6531-136">Alternativ kann es sich um eine Datenbanktabelle handelt, die als schreibgeschützt behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="d6531-136">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="d6531-137">Umgekehrt geht EF Core bei regulären Entitätstypen davon `ToTable` aus, dass ein in der Methode angegebenes Datenbankobjekt als _Tabelle_behandelt werden kann, was bedeutet, dass es als Abfragequelle verwendet werden kann, aber auch durch Aktualisierungs-, Lösch- und Einfügevorgänge ausgerichtet werden kann.</span><span class="sxs-lookup"><span data-stu-id="d6531-137">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="d6531-138">In der Tat können Sie den Namen `ToTable` einer Datenbankansicht in angeben, und alles sollte einwandfrei funktionieren, solange die Ansicht so konfiguriert ist, dass sie in der Datenbank aufrüstbar ist.</span><span class="sxs-lookup"><span data-stu-id="d6531-138">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="d6531-139">`ToView`geht davon aus, dass das Objekt bereits in der Datenbank vorhanden ist und nicht durch Migrationen erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="d6531-139">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="d6531-140">Beispiel</span><span class="sxs-lookup"><span data-stu-id="d6531-140">Example</span></span>

<span data-ttu-id="d6531-141">Das folgende Beispiel zeigt, wie schlüssellose Entitätstypen zum Abfragen einer Datenbankansicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d6531-141">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="d6531-142">Sie können das [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) dieses Artikels auf GitHub anzeigen.</span><span class="sxs-lookup"><span data-stu-id="d6531-142">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="d6531-143">Zuerst definieren wir ein einfaches Blog- und Post-Modell:</span><span class="sxs-lookup"><span data-stu-id="d6531-143">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="d6531-144">Als Nächstes definieren wir eine einfache Datenbankansicht, die es uns ermöglicht, die Anzahl der Beiträge abzufragen, die jedem Blog zugeordnet sind:</span><span class="sxs-lookup"><span data-stu-id="d6531-144">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="d6531-145">Als Nächstes definieren wir eine Klasse, die das Ergebnis aus der Datenbankansicht enthält:</span><span class="sxs-lookup"><span data-stu-id="d6531-145">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="d6531-146">Als Nächstes konfigurieren wir den schlüssellosen Entitätstyp in _OnModelCreating_ mithilfe der `HasNoKey` API.</span><span class="sxs-lookup"><span data-stu-id="d6531-146">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="d6531-147">Wir verwenden eine fließende Konfigurations-API, um die Zuordnung für den schlüssellosen Entitätstyp zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="d6531-147">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="d6531-148">Als Nächstes konfigurieren `DbContext` wir `DbSet<T>`die, um Folgendes einzuschließen:</span><span class="sxs-lookup"><span data-stu-id="d6531-148">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="d6531-149">Schließlich können wir die Datenbankansicht standardmäßig abfragen:</span><span class="sxs-lookup"><span data-stu-id="d6531-149">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="d6531-150">Beachten Sie, dass wir auch eine Abfrageeigenschaft auf Kontextebene (DbSet) definiert haben, die als Stamm für Abfragen für diesen Typ fungiert.</span><span class="sxs-lookup"><span data-stu-id="d6531-150">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>
