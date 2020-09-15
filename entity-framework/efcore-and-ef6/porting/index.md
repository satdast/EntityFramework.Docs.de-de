---
title: Portieren von EF6 nach EF Core
description: Allgemeine Informationen zum Portieren einer Anwendung von Entity Framework 6 zu Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/index
ms.openlocfilehash: 62360dda92d951fe9a4f43a2b742a7457ad8692f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073590"
---
# <a name="porting-from-ef6-to-ef-core"></a><span data-ttu-id="570ba-103">Portieren von EF 6 nach EF Core</span><span class="sxs-lookup"><span data-stu-id="570ba-103">Porting from EF6 to EF Core</span></span>

<span data-ttu-id="570ba-104">Aufgrund der wesentlichen Änderungen in EF Core wird nicht empfohlen, eine EF 6-Anwendung auf EF Core umzustellen – es sei denn, es gibt einen zwingenden Grund für eine solche Änderung.</span><span class="sxs-lookup"><span data-stu-id="570ba-104">Because of the fundamental changes in EF Core we do not recommend attempting to move an EF6 application to EF Core unless you have a compelling reason to make the change.</span></span>
<span data-ttu-id="570ba-105">Sie sollten den Wechsel von EF 6 nach EF Core weniger als Upgrade, sondern als eine Portierung betrachten.</span><span class="sxs-lookup"><span data-stu-id="570ba-105">You should view the move from EF6 to EF Core as a port rather than an upgrade.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="570ba-106">Bevor Sie die Portierung starten, müssen Sie überprüfen, ob EF Core den Datenzugriffsanforderungen für Ihre Anwendung entspricht.</span><span class="sxs-lookup"><span data-stu-id="570ba-106">Before you start the porting process it is important to validate that EF Core meets the data access requirements for your application.</span></span>

## <a name="missing-features"></a><span data-ttu-id="570ba-107">Fehlende Features</span><span class="sxs-lookup"><span data-stu-id="570ba-107">Missing features</span></span>

<span data-ttu-id="570ba-108">Stellen Sie sicher, dass EF Core über alle Funktionen verfügt, die Sie in Ihrer Anwendung benötigen.</span><span class="sxs-lookup"><span data-stu-id="570ba-108">Make sure that EF Core has all the features you need to use in your application.</span></span> <span data-ttu-id="570ba-109">Im [Funktionsvergleich](xref:efcore-and-ef6/index) finden Sie einen ausführlichen Vergleich der Funktionen in EF Core mit EF6.</span><span class="sxs-lookup"><span data-stu-id="570ba-109">See [Feature Comparison](xref:efcore-and-ef6/index) for a detailed comparison of how the feature set in EF Core compares to EF6.</span></span> <span data-ttu-id="570ba-110">Wenn erforderliche Features fehlen, stellen Sie vor dem Portieren zu EF Core sicher, dass dies kompensiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="570ba-110">If any required features are missing, ensure that you can compensate for the lack of these features before porting to EF Core.</span></span>

## <a name="behavior-changes"></a><span data-ttu-id="570ba-111">Verhaltensänderungen</span><span class="sxs-lookup"><span data-stu-id="570ba-111">Behavior changes</span></span>

<span data-ttu-id="570ba-112">Dies ist eine nicht vollständige Liste einiger Verhaltensänderungen zwischen EF6 und EF Core.</span><span class="sxs-lookup"><span data-stu-id="570ba-112">This is a non-exhaustive list of some changes in behavior between EF6 and EF Core.</span></span> <span data-ttu-id="570ba-113">Berücksichtigen Sie diese beim Portieren Ihrer Anwendung, da hierdurch das Verhalten der Anwendung geändert wird, dies aber nach der Umstellung auf EF Core nicht als Kompilierungsfehler angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="570ba-113">It is important to keep these in mind as your port your application as they may change the way your application behaves, but will not show up as compilation errors after swapping to EF Core.</span></span>

### <a name="dbsetaddattach-and-graph-behavior"></a><span data-ttu-id="570ba-114">Dbset.Add/Attach und Graph-Verhalten</span><span class="sxs-lookup"><span data-stu-id="570ba-114">DbSet.Add/Attach and graph behavior</span></span>

<span data-ttu-id="570ba-115">In EF6 führt das Aufrufen von `DbSet.Add()` für eine Entität zu einer rekursiven Suche nach allen Entitäten, auf die in den Navigationseigenschaften verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="570ba-115">In EF6, calling `DbSet.Add()` on an entity results in a recursive search for all entities referenced in its navigation properties.</span></span> <span data-ttu-id="570ba-116">Alle Entitäten, die gefunden werden und nicht bereits vom Kontext nachverfolgt werden, werden ebenfalls als hinzugefügt gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="570ba-116">Any entities that are found, and are not already tracked by the context, are also marked as added.</span></span> <span data-ttu-id="570ba-117">`DbSet.Attach()` verhält sich identisch, mit dem Unterschied, dass alle Entitäten als unverändert markiert werden.</span><span class="sxs-lookup"><span data-stu-id="570ba-117">`DbSet.Attach()` behaves the same, except all entities are marked as unchanged.</span></span>

<span data-ttu-id="570ba-118">**EF Core führt eine ähnliche rekursive Suche aus, allerdings mit etwas anderen Regeln.**</span><span class="sxs-lookup"><span data-stu-id="570ba-118">**EF Core performs a similar recursive search, but with some slightly different rules.**</span></span>

*  <span data-ttu-id="570ba-119">Die Stammentität befindet sich immer im angeforderten Zustand (hinzugefügt für `DbSet.Add` und unverändert für `DbSet.Attach`).</span><span class="sxs-lookup"><span data-stu-id="570ba-119">The root entity is always in the requested state (added for `DbSet.Add` and unchanged for `DbSet.Attach`).</span></span>

*  <span data-ttu-id="570ba-120">**Für Entitäten, die während der rekursiven Suche von Navigationseigenschaften gefunden werden:**</span><span class="sxs-lookup"><span data-stu-id="570ba-120">**For entities that are found during the recursive search of navigation properties:**</span></span>

    *  <span data-ttu-id="570ba-121">**Wenn der Primärschlüssel der Entität im Speicher generiert wird**</span><span class="sxs-lookup"><span data-stu-id="570ba-121">**If the primary key of the entity is store generated**</span></span>

        * <span data-ttu-id="570ba-122">Wenn der Primärschlüssel nicht auf einen Wert festgelegt ist, wird der Zustand auf hinzugefügt festgelegt.</span><span class="sxs-lookup"><span data-stu-id="570ba-122">If the primary key is not set to a value, the state is set to added.</span></span> <span data-ttu-id="570ba-123">Der Primärschlüsselwert wird als nicht festgelegt betrachtet, wenn ihm der CLR-Standardwert für den Eigenschaftentyp zugewiesen ist (z. B. `0` für `int`, `null` für `string` usw.).</span><span class="sxs-lookup"><span data-stu-id="570ba-123">The primary key value is considered "not set" if it is assigned the CLR default value for the property type (for example, `0` for `int`, `null` for `string`, etc.).</span></span>

        * <span data-ttu-id="570ba-124">Wenn der Primärschlüssel auf einen Wert festgelegt ist, wird der Zustand auf unverändert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="570ba-124">If the primary key is set to a value, the state is set to unchanged.</span></span>

    *  <span data-ttu-id="570ba-125">Wenn der Primärschlüssel nicht von der Datenbank generiert wird, wird die Entität in denselben Zustand wie der Stamm versetzt.</span><span class="sxs-lookup"><span data-stu-id="570ba-125">If the primary key is not database generated, the entity is put in the same state as the root.</span></span>

### <a name="code-first-database-initialization"></a><span data-ttu-id="570ba-126">Code First-Datenbankinitialisierung</span><span class="sxs-lookup"><span data-stu-id="570ba-126">Code First database initialization</span></span>

<span data-ttu-id="570ba-127">**EF6 verfügt über eine Vielzahl von Kriterien zum Auswählen der Datenbankverbindung und Initialisieren der Datenbank. Zu diesen Regeln gehören:**</span><span class="sxs-lookup"><span data-stu-id="570ba-127">**EF6 has a significant amount of magic it performs around selecting the database connection and initializing the database. Some of these rules include:**</span></span>

* <span data-ttu-id="570ba-128">Wenn keine Konfiguration durchgeführt wird, wählt EF6 eine Datenbank in SQL Express oder LocalDb aus.</span><span class="sxs-lookup"><span data-stu-id="570ba-128">If no configuration is performed, EF6 will select a database on SQL Express or LocalDb.</span></span>

* <span data-ttu-id="570ba-129">Wenn eine Verbindungszeichenfolge mit dem gleichen Namen wie der Kontext in der `App/Web.config`-Datei der Anwendung vorhanden ist, wird diese Verbindung verwendet.</span><span class="sxs-lookup"><span data-stu-id="570ba-129">If a connection string with the same name as the context is in the applications `App/Web.config` file, this connection will be used.</span></span>

* <span data-ttu-id="570ba-130">Wenn die Datenbank noch nicht vorhanden ist, wird sie erstellt.</span><span class="sxs-lookup"><span data-stu-id="570ba-130">If the database does not exist, it is created.</span></span>

* <span data-ttu-id="570ba-131">Wenn keine der Tabellen aus dem Modell in der Datenbank vorhanden ist, wird das Schema für das aktuelle Modell der Datenbank hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="570ba-131">If none of the tables from the model exist in the database, the schema for the current model is added to the database.</span></span> <span data-ttu-id="570ba-132">Wenn Migrationen aktiviert sind, werden sie verwendet, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="570ba-132">If migrations are enabled, then they are used to create the database.</span></span>

* <span data-ttu-id="570ba-133">Wenn die Datenbank vorhanden ist und das Schema bereits von EF6 erstellt wurde, wird das Schema auf Kompatibilität mit dem aktuellen Modell geprüft.</span><span class="sxs-lookup"><span data-stu-id="570ba-133">If the database exists and EF6 had previously created the schema, then the schema is checked for compatibility with the current model.</span></span> <span data-ttu-id="570ba-134">Wenn sich das Modell seit der Erstellung des Schemas geändert hat, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="570ba-134">An exception is thrown if the model has changed since the schema was created.</span></span>

<span data-ttu-id="570ba-135">**EF Core führt diese Aktionen nicht aus.**</span><span class="sxs-lookup"><span data-stu-id="570ba-135">**EF Core does not perform any of this magic.**</span></span>

* <span data-ttu-id="570ba-136">Die Datenbankverbindung muss explizit im Code konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="570ba-136">The database connection must be explicitly configured in code.</span></span>

* <span data-ttu-id="570ba-137">Es wird keine Initialisierung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="570ba-137">No initialization is performed.</span></span> <span data-ttu-id="570ba-138">Sie müssen `DbContext.Database.Migrate()` verwenden, um Migrationen anzuwenden (oder `DbContext.Database.EnsureCreated()` und `EnsureDeleted()`, um die Datenbank ohne Migrationen zu erstellen bzw. zu löschen).</span><span class="sxs-lookup"><span data-stu-id="570ba-138">You must use `DbContext.Database.Migrate()` to apply migrations (or `DbContext.Database.EnsureCreated()` and `EnsureDeleted()` to create/delete the database without using migrations).</span></span>

### <a name="code-first-table-naming-convention"></a><span data-ttu-id="570ba-139">Benennungskonvention für Code First-Tabelle</span><span class="sxs-lookup"><span data-stu-id="570ba-139">Code First table naming convention</span></span>

<span data-ttu-id="570ba-140">EF6 führt den Entitätsklassennamen über einen Pluralisierungsdienst aus, um den Standardtabellennamen zu berechnen, dem die Entität zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="570ba-140">EF6 runs the entity class name through a pluralization service to calculate the default table name that the entity is mapped to.</span></span>

<span data-ttu-id="570ba-141">EF Core verwendet den Namen der `DbSet`-Eigenschaft, in der die Entität für den abgeleiteten Kontext verfügbar gemacht wird.</span><span class="sxs-lookup"><span data-stu-id="570ba-141">EF Core uses the name of the `DbSet` property that the entity is exposed in on the derived context.</span></span> <span data-ttu-id="570ba-142">Wenn die Entität nicht über eine `DbSet`-Eigenschaft verfügt, wird der Klassenname verwendet.</span><span class="sxs-lookup"><span data-stu-id="570ba-142">If the entity does not have a `DbSet` property, then the class name is used.</span></span>
