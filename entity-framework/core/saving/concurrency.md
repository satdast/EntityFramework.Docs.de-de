---
title: Handhaben von Nebenläufigkeitskonflikten
description: Umgang mit Konflikten bei gleichzeitiger Aktualisierung derselben Daten in Entity Framework Core
author: rowanmiller
ms.date: 03/03/2018
uid: core/saving/concurrency
ms.openlocfilehash: 7e3781879b39e6c30a0c981b5e0b74baf2b2863b
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617308"
---
# <a name="handling-concurrency-conflicts"></a><span data-ttu-id="ff30e-103">Behandlung von Parallelitätskonflikten</span><span class="sxs-lookup"><span data-stu-id="ff30e-103">Handling Concurrency Conflicts</span></span>

> [!NOTE]
> <span data-ttu-id="ff30e-104">Auf dieser Seite wird erläutert, wie Parallelität in EF Core funktioniert und wie sich Nebenläufigkeitskonflikte in Ihrer Anwendung handhaben lassen.</span><span class="sxs-lookup"><span data-stu-id="ff30e-104">This page documents how concurrency works in EF Core and how to handle concurrency conflicts in your application.</span></span> <span data-ttu-id="ff30e-105">Weitere Informationen zum Konfigurieren von Parallelitätstoken in Ihrem Modell finden Sie unter [Parallelitätstoken](xref:core/modeling/concurrency).</span><span class="sxs-lookup"><span data-stu-id="ff30e-105">See [Concurrency Tokens](xref:core/modeling/concurrency) for details on how to configure concurrency tokens in your model.</span></span>

> [!TIP]
> <span data-ttu-id="ff30e-106">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Concurrency/) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="ff30e-106">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Concurrency/) on GitHub.</span></span>

<span data-ttu-id="ff30e-107">_Datenbankparallelität_ ist gegeben, wenn mehrere Prozesse oder Benutzer gleichzeitig auf dieselben Daten in einer Datenbank zugreifen oder diese ändern.</span><span class="sxs-lookup"><span data-stu-id="ff30e-107">_Database concurrency_ refers to situations in which multiple processes or users access or change the same data in a database at the same time.</span></span> <span data-ttu-id="ff30e-108">Mit _Parallelitätssteuerung_ sind bestimmte Mechanismen gemeint, mit denen die Datenkonsistenz bei gleichzeitigen Änderungen sichergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="ff30e-108">_Concurrency control_ refers to specific mechanisms used to ensure data consistency in presence of concurrent changes.</span></span>

<span data-ttu-id="ff30e-109">EF Core implementiert eine _optimistische Parallelitätssteuerung_: Mehrere Prozesse oder Benutzer können also unabhängig voneinander Änderungen vornehmen, ohne dass der Mehraufwand einer Synchronisierung oder Sperrung anfällt.</span><span class="sxs-lookup"><span data-stu-id="ff30e-109">EF Core implements _optimistic concurrency control_, meaning that it will let multiple processes or users make changes independently without the overhead of synchronization or locking.</span></span> <span data-ttu-id="ff30e-110">Im Idealfall wirken sich diese Änderungen nicht aufeinander aus und werden daher übernommen.</span><span class="sxs-lookup"><span data-stu-id="ff30e-110">In the ideal situation, these changes will not interfere with each other and therefore will be able to succeed.</span></span> <span data-ttu-id="ff30e-111">Im schlechtesten Fall versuchen zwei oder mehr Prozesse, in Konflikt stehende Änderungen vorzunehmen, und nur einer davon ist erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="ff30e-111">In the worst case scenario, two or more processes will attempt to make conflicting changes, and only one of them should succeed.</span></span>

## <a name="how-concurrency-control-works-in-ef-core"></a><span data-ttu-id="ff30e-112">Funktionsweise der Parallelitätssteuerung in EF Core</span><span class="sxs-lookup"><span data-stu-id="ff30e-112">How concurrency control works in EF Core</span></span>

<span data-ttu-id="ff30e-113">Mithilfe von Eigenschaften, die als Parallelitätstoken konfiguriert werden, wird die optimistische Parallelitätssteuerung implementiert: Immer wenn während `SaveChanges` ein Update- oder Löschvorgang ausgeführt wird, wird der Wert des Parallelitätstokens der Datenbank mit dem ursprünglichen Wert verglichen, der von EF Core gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="ff30e-113">Properties configured as concurrency tokens are used to implement optimistic concurrency control: whenever an update or delete operation is performed during `SaveChanges`, the value of the concurrency token on the database is compared against the original value read by EF Core.</span></span>

- <span data-ttu-id="ff30e-114">Falls die Werte übereinstimmen, kann der Vorgang abgeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="ff30e-114">If the values match, the operation can complete.</span></span>
- <span data-ttu-id="ff30e-115">Ist dies jedoch nicht der Fall, geht EF Core davon aus, dass ein anderer Benutzer einen in Konflikt stehenden Vorgang ausgeführt hat, und bricht die aktuelle Transaktion ab.</span><span class="sxs-lookup"><span data-stu-id="ff30e-115">If the values do not match, EF Core assumes that another user has performed a conflicting operation and aborts the current transaction.</span></span>

<span data-ttu-id="ff30e-116">Wenn ein anderer Benutzer einen in Konflikt stehenden Vorgang ausgeführt hat, spricht man von einem _Nebenläufigkeitskonflikt_.</span><span class="sxs-lookup"><span data-stu-id="ff30e-116">The situation when another user has performed an operation that conflicts with the current operation is known as _concurrency conflict_.</span></span>

<span data-ttu-id="ff30e-117">Die Implementierung des Vergleichs der Parallelitätstokenwerte ist Aufgabe der Datenbankanbieter.</span><span class="sxs-lookup"><span data-stu-id="ff30e-117">Database providers are responsible for implementing the comparison of concurrency token values.</span></span>

<span data-ttu-id="ff30e-118">Für relationale Datenbanken lässt sich in EF Core eine Überprüfung des Parallelitätstokenwerts in der `WHERE`-Klausel einer jeden `UPDATE`- oder `DELETE`-Anweisung ausführen.</span><span class="sxs-lookup"><span data-stu-id="ff30e-118">On relational databases EF Core includes a check for the value of the concurrency token in the `WHERE` clause of any `UPDATE` or `DELETE` statements.</span></span> <span data-ttu-id="ff30e-119">Nach dem Ausführen der Anweisungen liest EF Core die Anzahl der Zeilen, die betroffen sind.</span><span class="sxs-lookup"><span data-stu-id="ff30e-119">After executing the statements, EF Core reads the number of rows that were affected.</span></span>

<span data-ttu-id="ff30e-120">Wenn keine Zeilen betroffen sind, wird ein Nebenläufigkeitskonflikt erkannt, und EF Core löst `DbUpdateConcurrencyException` aus.</span><span class="sxs-lookup"><span data-stu-id="ff30e-120">If no rows are affected, a concurrency conflict is detected, and EF Core throws `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="ff30e-121">Angenommen, wir möchten `LastName` als Parallelitätstoken von `Person` konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="ff30e-121">For example, we may want to configure `LastName` on `Person` to be a concurrency token.</span></span> <span data-ttu-id="ff30e-122">Alle Updatevorgänge von „Person“ enthalten dann eine Parallelitätsüberprüfung in der `WHERE`-Klausel:</span><span class="sxs-lookup"><span data-stu-id="ff30e-122">Then any update operation on Person will include the concurrency check in the `WHERE` clause:</span></span>

``` sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="resolving-concurrency-conflicts"></a><span data-ttu-id="ff30e-123">Beheben von Nebenläufigkeitskonflikten</span><span class="sxs-lookup"><span data-stu-id="ff30e-123">Resolving concurrency conflicts</span></span>

<span data-ttu-id="ff30e-124">Setzen wir das vorherige Beispiel fort: Wenn ein Benutzer versucht, Änderungen an `Person` zu speichern, ein anderer Benutzer `LastName` jedoch bereits geändert hat, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="ff30e-124">Continuing with the previous example, if one user tries to save some changes to a `Person`, but another user has already changed the `LastName`, then an exception will be thrown.</span></span>

<span data-ttu-id="ff30e-125">Die Anwendung sollte den Benutzer einfach darüber informieren, dass das Update aufgrund von in Konflikt stehenden Änderungen nicht durchgeführt werden konnte war, und fortfahren.</span><span class="sxs-lookup"><span data-stu-id="ff30e-125">At this point, the application could simply inform the user that the update was not successful due to conflicting changes and move on.</span></span> <span data-ttu-id="ff30e-126">Es kann sich jedoch auch empfehlen, den Benutzer aufzufordern, sicherzustellen, dass dieser Datensatz weiterhin dieselbe Person darstellt, und den Vorgang zu wiederholen.</span><span class="sxs-lookup"><span data-stu-id="ff30e-126">But it may be desirable to prompt the user to ensure this record still represents the same actual person and to retry the operation.</span></span>

<span data-ttu-id="ff30e-127">So lässt sich z.B. ein _Nebenläufigkeitskonflikt beheben_.</span><span class="sxs-lookup"><span data-stu-id="ff30e-127">This process is an example of _resolving a concurrency conflict_.</span></span>

<span data-ttu-id="ff30e-128">Dazu müssen Sie die ausstehenden Änderungen aus dem aktuellen `DbContext` mit den Werten in der Datenbank zusammenführen.</span><span class="sxs-lookup"><span data-stu-id="ff30e-128">Resolving a concurrency conflict involves merging the pending changes from the current `DbContext` with the values in the database.</span></span> <span data-ttu-id="ff30e-129">Welche Werte zusammengeführt werden, hängt von der jeweiligen Anwendung ab und kann durch die Benutzereingabe gesteuert sein.</span><span class="sxs-lookup"><span data-stu-id="ff30e-129">What values get merged will vary based on the application and may be directed by user input.</span></span>

<span data-ttu-id="ff30e-130">**Nebenläufigkeitskonflikte können mit drei verschiedenen Wertetypen gelöst werden:**</span><span class="sxs-lookup"><span data-stu-id="ff30e-130">**There are three sets of values available to help resolve a concurrency conflict:**</span></span>

- <span data-ttu-id="ff30e-131">**Aktuelle Werte** sind die Werte, die die Anwendung in die Datenbank schreiben wollte.</span><span class="sxs-lookup"><span data-stu-id="ff30e-131">**Current values** are the values that the application was attempting to write to the database.</span></span>
- <span data-ttu-id="ff30e-132">**Ursprüngliche Werte** sind die Werte, die vor den Änderungen aus der Datenbank abgerufen wurden.</span><span class="sxs-lookup"><span data-stu-id="ff30e-132">**Original values** are the values that were originally retrieved from the database, before any edits were made.</span></span>
- <span data-ttu-id="ff30e-133">**Datenbankwerte** sind die Werte, die derzeit in der Datenbank gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="ff30e-133">**Database values** are the values currently stored in the database.</span></span>

<span data-ttu-id="ff30e-134">Nebenläufigkeitskonflikte werden im Allgemeinen folgendermaßen behoben:</span><span class="sxs-lookup"><span data-stu-id="ff30e-134">The general approach to handle a concurrency conflicts is:</span></span>

1. <span data-ttu-id="ff30e-135">Fangen Sie `DbUpdateConcurrencyException` während `SaveChanges` ab.</span><span class="sxs-lookup"><span data-stu-id="ff30e-135">Catch `DbUpdateConcurrencyException` during `SaveChanges`.</span></span>
2. <span data-ttu-id="ff30e-136">Bereiten Sie mit `DbUpdateConcurrencyException.Entries` neue Änderungen für die betroffenen Entitäten vor.</span><span class="sxs-lookup"><span data-stu-id="ff30e-136">Use `DbUpdateConcurrencyException.Entries` to prepare a new set of changes for the affected entities.</span></span>
3. <span data-ttu-id="ff30e-137">Aktualisieren Sie die ursprünglichen Werte des Parallelitätstokens, sodass sie mit den aktuellen Werten in der Datenbank übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="ff30e-137">Refresh the original values of the concurrency token to reflect the current values in the database.</span></span>
4. <span data-ttu-id="ff30e-138">Wiederholen Sie den Prozess, bis keine Konflikte mehr auftreten.</span><span class="sxs-lookup"><span data-stu-id="ff30e-138">Retry the process until no conflicts occur.</span></span>

<span data-ttu-id="ff30e-139">Im folgenden Beispiel werden `Person.FirstName` und `Person.LastName` als Parallelitätstoken eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="ff30e-139">In the following example, `Person.FirstName` and `Person.LastName` are set up as concurrency tokens.</span></span> <span data-ttu-id="ff30e-140">Dort, wo Sie die anwendungsspezifische Logik platzieren, nach der der zu speichernde Wert ausgewählt wird, befindet sich ein `// TODO:`-Kommentar.</span><span class="sxs-lookup"><span data-stu-id="ff30e-140">There is a `// TODO:` comment in the location where you include application specific logic to choose the value to be saved.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Concurrency/Sample.cs?name=ConcurrencyHandlingCode&highlight=34-35)]
