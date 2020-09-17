---
title: Getrennte Entitäten – EF Core
description: Kontextinstanzenübergreifendes Arbeiten mit getrennten, nicht nachverfolgten Entitäten in Entity Framework Core
author: ajcvickers
ms.author: avickers
ms.date: 10/27/2016
uid: core/saving/disconnected-entities
ms.openlocfilehash: d9b6d2d2c0761dd6f670ba51f935c866e10460b7
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070886"
---
# <a name="disconnected-entities"></a><span data-ttu-id="1bffe-103">Getrennte Entitäten</span><span class="sxs-lookup"><span data-stu-id="1bffe-103">Disconnected entities</span></span>

<span data-ttu-id="1bffe-104">Eine DbContext-Instanz verfolgt automatisch Entitäten nach, die von der Datenbank zurückgegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="1bffe-104">A DbContext instance will automatically track entities returned from the database.</span></span> <span data-ttu-id="1bffe-105">An diesen Entitäten vorgenommene Änderungen werden nach dem Aufrufen von SaveChanges erkannt, und die Datenbank wird ggf. aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="1bffe-105">Changes made to these entities will then be detected when SaveChanges is called and the database will be updated as needed.</span></span> <span data-ttu-id="1bffe-106">Weitere Einzelheiten finden Sie unter [Grundlegendes zum Speichern](xref:core/saving/basic) und [Zugehörige Daten](xref:core/saving/related-data).</span><span class="sxs-lookup"><span data-stu-id="1bffe-106">See [Basic Save](xref:core/saving/basic) and [Related Data](xref:core/saving/related-data) for details.</span></span>

<span data-ttu-id="1bffe-107">Entitäten werden jedoch manchmal mit einer Kontextinstanz abgefragt und anschließend mit einer anderen Instanz gespeichert.</span><span class="sxs-lookup"><span data-stu-id="1bffe-107">However, sometimes entities are queried using one context instance and then saved using a different instance.</span></span> <span data-ttu-id="1bffe-108">Dies geschieht häufig in „getrennten“ Szenarios, wie z.B. einer Webanwendung, in welcher die Entitäten abgefragt werden, an den Client gesendet werden, geändert werden, in einer Anforderung zurück an den Server gesendet werden und anschließend gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="1bffe-108">This often happens in "disconnected" scenarios such as a web application where the entities are queried, sent to the client, modified, sent back to the server in a request, and then saved.</span></span> <span data-ttu-id="1bffe-109">In diesem Fall muss der zweiten Kontextinstanz bekannt sein, ob die Entitäten neu (Einfügung erforderlich) oder bereits vorhanden (Aktualisierung erforderlich) sind.</span><span class="sxs-lookup"><span data-stu-id="1bffe-109">In this case, the second context instance needs to know whether the entities are new (should be inserted) or existing (should be updated).</span></span>

<!-- markdownlint-disable MD028 -->
> [!TIP]
> <span data-ttu-id="1bffe-110">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="1bffe-110">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/) on GitHub.</span></span>

> [!TIP]
> <span data-ttu-id="1bffe-111">EF Core kann nur eine Instanz einer Entität mit einem bestimmten primären Schlüsselwert nachverfolgen.</span><span class="sxs-lookup"><span data-stu-id="1bffe-111">EF Core can only track one instance of any entity with a given primary key value.</span></span> <span data-ttu-id="1bffe-112">Dass dies ein Problem darstellt, kann verhindert werden, indem für die einzelnen Arbeitseinheiten kurzlebiger Kontext verwendet wird, wie z.B. dass der Kontext leer beginnt, über angefügte Entitäten verfügt, diese Entitäten speichert und der Kontext anschließend verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="1bffe-112">The best way to avoid this being an issue is to use a short-lived context for each unit-of-work such that the context starts empty, has entities attached to it, saves those entities, and then the context is disposed and discarded.</span></span>
<!-- markdownlint-enable MD028 -->

## <a name="identifying-new-entities"></a><span data-ttu-id="1bffe-113">Identifizieren neuer Entitäten</span><span class="sxs-lookup"><span data-stu-id="1bffe-113">Identifying new entities</span></span>

### <a name="client-identifies-new-entities"></a><span data-ttu-id="1bffe-114">Client identifiziert neue Entitäten</span><span class="sxs-lookup"><span data-stu-id="1bffe-114">Client identifies new entities</span></span>

<span data-ttu-id="1bffe-115">Am einfachsten ist, wenn der Client den Server darüber informiert, ob die Entität neu oder vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1bffe-115">The simplest case to deal with is when the client informs the server whether the entity is new or existing.</span></span> <span data-ttu-id="1bffe-116">Die Anforderung zum Einfügen einer neuen Entität unterscheidet sich beispielsweise häufig von der Anforderung zum Aktualisieren einer vorhandenen Entität.</span><span class="sxs-lookup"><span data-stu-id="1bffe-116">For example, often the request to insert a new entity is different from the request to update an existing entity.</span></span>

<span data-ttu-id="1bffe-117">Im restlichen Teil dieses Abschnitts werden die Fälle behandelt, bei denen auf andere Weise bestimmt werden muss, ob eine Einfügung oder ein Update erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="1bffe-117">The remainder of this section covers the cases where it necessary to determine in some other way whether to insert or update.</span></span>

### <a name="with-auto-generated-keys"></a><span data-ttu-id="1bffe-118">Mit automatisch generierten Schlüsseln</span><span class="sxs-lookup"><span data-stu-id="1bffe-118">With auto-generated keys</span></span>

<span data-ttu-id="1bffe-119">Mit dem Wert eines automatisch generierten Schlüssels kann häufig bestimmt werden, ob eine Entität eingefügt oder aktualisiert werden muss.</span><span class="sxs-lookup"><span data-stu-id="1bffe-119">The value of an automatically generated key can often be used to determine whether an entity needs to be inserted or updated.</span></span> <span data-ttu-id="1bffe-120">Wenn der Schlüssel nicht festgelegt wurde (d.h. der CLR-Standardwert ist noch NULL, 0 (Null) etc.), ist davon auszugehen, dass die Entität neu ist und eingefügt werden muss.</span><span class="sxs-lookup"><span data-stu-id="1bffe-120">If the key has not been set (that is, it still has the CLR default value of null, zero, etc.), then the entity must be new and needs inserting.</span></span> <span data-ttu-id="1bffe-121">Wenn der Schlüsselwert bereits festgelegt worden ist, muss er andererseits zuvor gespeichert worden sein und nun aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="1bffe-121">On the other hand, if the key value has been set, then it must have already been previously saved and now needs updating.</span></span> <span data-ttu-id="1bffe-122">Das heißt, wenn der Schlüssel einen Wert aufweist, wurde die Entität abgefragt, an den Client gesendet und nun für ein Update zurückgesendet.</span><span class="sxs-lookup"><span data-stu-id="1bffe-122">In other words, if the key has a value, then the entity was queried, sent to the client, and has now come back to be updated.</span></span>

<span data-ttu-id="1bffe-123">Wenn der Entitätstyp bekannt ist, kann ohne großen Aufwand überprüft werden, ob ein nicht festgelegter Schlüssel vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="1bffe-123">It is easy to check for an unset key when the entity type is known:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewSimple)]

<span data-ttu-id="1bffe-124">EF verfügt jedoch auch über eine integrierte Möglichkeit, diesen Vorgang für einen beliebigen Entitäts- und Schlüsseltyp durchzuführen:</span><span class="sxs-lookup"><span data-stu-id="1bffe-124">However, EF also has a built-in way to do this for any entity type and key type:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]  
> <span data-ttu-id="1bffe-125">Schlüssel werden festgelegt, sobald Entitäten vom Kontext nachverfolgt werden, selbst dann, wenn die Entität den Status „Hinzugefügt“ aufweist.</span><span class="sxs-lookup"><span data-stu-id="1bffe-125">Keys are set as soon as entities are tracked by the context, even if the entity is in the Added state.</span></span> <span data-ttu-id="1bffe-126">Dies ist hilfreich, wenn ein Graph mit Entitäten durchlaufen wird und entschieden wird, wie mit den einzelnen Entitäten umgegangen werden soll, z.B. bei der Verwendung der TrackGraph-API.</span><span class="sxs-lookup"><span data-stu-id="1bffe-126">This helps when traversing a graph of entities and deciding what to do with each, such as when using the TrackGraph API.</span></span> <span data-ttu-id="1bffe-127">Der Schlüsselwert sollte nur auf die hier dargestellte Weise verwendet werden, _bevor_ ein Aufruf zum Nachverfolgen der Entität durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="1bffe-127">The key value should only be used in the way shown here _before_ any call is made to track the entity.</span></span>

### <a name="with-other-keys"></a><span data-ttu-id="1bffe-128">Mit anderen Schlüsseln</span><span class="sxs-lookup"><span data-stu-id="1bffe-128">With other keys</span></span>

<span data-ttu-id="1bffe-129">Zum Identifizieren neuer Entitäten sind einige andere Mechanismen erforderlich, wenn Schlüsselwerte nicht automatisch generiert werden.</span><span class="sxs-lookup"><span data-stu-id="1bffe-129">Some other mechanism is needed to identify new entities when key values are not generated automatically.</span></span> <span data-ttu-id="1bffe-130">Hierfür gibt es zwei allgemeine Ansätze:</span><span class="sxs-lookup"><span data-stu-id="1bffe-130">There are two general approaches to this:</span></span>

* <span data-ttu-id="1bffe-131">Abfrage für die Entität</span><span class="sxs-lookup"><span data-stu-id="1bffe-131">Query for the entity</span></span>
* <span data-ttu-id="1bffe-132">Übergeben eines Flags vom Client</span><span class="sxs-lookup"><span data-stu-id="1bffe-132">Pass a flag from the client</span></span>

<span data-ttu-id="1bffe-133">Verwenden Sie für eine Abfrage für die Entität einfach die Find-Methode:</span><span class="sxs-lookup"><span data-stu-id="1bffe-133">To query for the entity, just use the Find method:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewQuery)]

<span data-ttu-id="1bffe-134">Die Anzeige des vollständigen Codes für die Übergabe eines Flags von einem Client ist nicht Gegenstand dieses Dokuments.</span><span class="sxs-lookup"><span data-stu-id="1bffe-134">It is beyond the scope of this document to show the full code for passing a flag from a client.</span></span> <span data-ttu-id="1bffe-135">In einer Web-App bedeutet dies in der Regel, dass für verschiedene Aktionen unterschiedliche Anforderungen durchgeführt werden, oder dass einige Status in der Anforderung übergeben und anschließend im Controller extrahiert werden.</span><span class="sxs-lookup"><span data-stu-id="1bffe-135">In a web app, it usually means making different requests for different actions, or passing some state in the request then extracting it in the controller.</span></span>

## <a name="saving-single-entities"></a><span data-ttu-id="1bffe-136">Speichern einzelner Entitäten</span><span class="sxs-lookup"><span data-stu-id="1bffe-136">Saving single entities</span></span>

<span data-ttu-id="1bffe-137">Wenn bekannt ist, ob eine Einfügung oder ein Update erforderlich ist, kann entsprechend die Add- oder die Update-Methode verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="1bffe-137">If it is known whether or not an insert or update is needed, then either Add or Update can be used appropriately:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

<span data-ttu-id="1bffe-138">Wenn die Entität automatisch generierte Schlüsselwerte verwendet, kann die Update-Methode in beiden Fällen verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="1bffe-138">However, if the entity uses auto-generated key values, then the Update method can be used for both cases:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

<span data-ttu-id="1bffe-139">Die Update-Methode markiert die Entität normalerweise für das Update, nicht für die Einfügung.</span><span class="sxs-lookup"><span data-stu-id="1bffe-139">The Update method normally marks the entity for update, not insert.</span></span> <span data-ttu-id="1bffe-140">Wenn die Entität über einen automatisch generierten Schlüssel verfügt und kein Schlüsselwert festgelegt wurde, wird die Entität jedoch stattdessen für eine Einfügung markiert.</span><span class="sxs-lookup"><span data-stu-id="1bffe-140">However, if the entity has a auto-generated key, and no key value has been set, then the entity is instead automatically marked for insert.</span></span>

> [!TIP]  
> <span data-ttu-id="1bffe-141">Dieses Verhalten wurde in EF Core 2.0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="1bffe-141">This behavior was introduced in EF Core 2.0.</span></span> <span data-ttu-id="1bffe-142">Bei früheren Releases muss immer explizit die Add- oder die Update-Methode ausgewählt werden.</span><span class="sxs-lookup"><span data-stu-id="1bffe-142">For earlier releases it is always necessary to explicitly choose either Add or Update.</span></span>

<span data-ttu-id="1bffe-143">Wenn die Entität keine automatisch generierten Schlüssel verwendet, muss die Anwendung entscheiden, ob die Entität eingefügt oder aktualisiert werden sollte. Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1bffe-143">If the entity is not using auto-generated keys, then the application must decide whether the entity should be inserted or updated: For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

<span data-ttu-id="1bffe-144">Folgende Schritte müssen ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="1bffe-144">The steps here are:</span></span>

* <span data-ttu-id="1bffe-145">Wenn die Find-Methode NULL zurückgibt, enthält die Datenbank den Blog mit dieser ID noch nicht. Daher wird die Add-Methode aufgerufen, um die Datenbank für eine Einfügung zu markieren.</span><span class="sxs-lookup"><span data-stu-id="1bffe-145">If Find returns null, then the database doesn't already contain the blog with this ID, so we call Add mark it for insertion.</span></span>
* <span data-ttu-id="1bffe-146">Wenn die Find-Methode eine Entität zurückgibt, ist diese in der Datenbank vorhanden, und der Kontext verfolgt nun die vorhandene Entität nach</span><span class="sxs-lookup"><span data-stu-id="1bffe-146">If Find returns an entity, then it exists in the database and the context is now tracking the existing entity</span></span>
  * <span data-ttu-id="1bffe-147">Anschließend werden die Werte für sämtliche Eigenschaften dieser Entität mit der SetValues-Methode auf die vom Client stammenden Werte festgelegt.</span><span class="sxs-lookup"><span data-stu-id="1bffe-147">We then use SetValues to set the values for all properties on this entity to those that came from the client.</span></span>
  * <span data-ttu-id="1bffe-148">Beim SetValues-Aufruf wird die Entität entsprechend ihrer Markierung nach Bedarf aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="1bffe-148">The SetValues call will mark the entity to be updated as needed.</span></span>

> [!TIP]  
> <span data-ttu-id="1bffe-149">SetValues markiert nur die Eigenschaften als geändert, die andere Werte aufweisen als die Eigenschaften in der verfolgten Entität.</span><span class="sxs-lookup"><span data-stu-id="1bffe-149">SetValues will only mark as modified the properties that have different values to those in the tracked entity.</span></span> <span data-ttu-id="1bffe-150">Das heißt, wenn das Update gesendet wird, werden nur die Spalten aktualisiert, die tatsächlich geändert wurden.</span><span class="sxs-lookup"><span data-stu-id="1bffe-150">This means that when the update is sent, only those columns that have actually changed will be updated.</span></span> <span data-ttu-id="1bffe-151">(Und wenn keine Änderungen vorgenommen wurden, wird gar kein Update gesendet.)</span><span class="sxs-lookup"><span data-stu-id="1bffe-151">(And if nothing has changed, then no update will be sent at all.)</span></span>

## <a name="working-with-graphs"></a><span data-ttu-id="1bffe-152">Arbeiten mit Graphen</span><span class="sxs-lookup"><span data-stu-id="1bffe-152">Working with graphs</span></span>

### <a name="identity-resolution"></a><span data-ttu-id="1bffe-153">Identitätsauflösung</span><span class="sxs-lookup"><span data-stu-id="1bffe-153">Identity resolution</span></span>

<span data-ttu-id="1bffe-154">Wie oben bereits erwähnt, kann EF Core nur eine Instanz einer Entität mit einem bestimmten primären Schlüsselwert nachverfolgen.</span><span class="sxs-lookup"><span data-stu-id="1bffe-154">As noted above, EF Core can only track one instance of any entity with a given primary key value.</span></span> <span data-ttu-id="1bffe-155">Bei der Arbeit mit Graphen sollte der Graph idealerweise so erstellt werden, dass diese Invariante beibehalten wird. Zudem sollte der Kontext nur für eine Arbeitseinheit verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1bffe-155">When working with graphs the graph should ideally be created such that this invariant is maintained, and the context should be used for only one unit-of-work.</span></span> <span data-ttu-id="1bffe-156">Wenn der Graph Duplikate enthält, muss dieser verarbeitet werden, bevor er an EF gesendet wird, um mehrere Instanzen in eine zu konsolidieren.</span><span class="sxs-lookup"><span data-stu-id="1bffe-156">If the graph does contain duplicates, then it will be necessary to process the graph before sending it to EF to consolidate multiple instances into one.</span></span> <span data-ttu-id="1bffe-157">Dies ist möglicherweise nicht einfach, wenn Instanzen in Konflikt stehende Werte und Beziehungen aufweisen. Zur Vermeidung einer Konfliktauflösung sollte die Konsolidierung von Duplikaten folglich so schnell wie möglich in Ihrer Anwendungspipeline erfolgen.</span><span class="sxs-lookup"><span data-stu-id="1bffe-157">This may not be trivial where instances have conflicting values and relationships, so consolidating duplicates should be done as soon as possible in your application pipeline to avoid conflict resolution.</span></span>

### <a name="all-newall-existing-entities"></a><span data-ttu-id="1bffe-158">Alle neuen/alle vorhandenen Entitäten</span><span class="sxs-lookup"><span data-stu-id="1bffe-158">All new/all existing entities</span></span>

<span data-ttu-id="1bffe-159">Ein Beispiel für die Arbeit mit Graphen besteht in einer Einfügung oder Aktualisierung eines Blogs zusammen mit der zugehörigen Sammlung zugehöriger Beiträge.</span><span class="sxs-lookup"><span data-stu-id="1bffe-159">An example of working with graphs is inserting or updating a blog together with its collection of associated posts.</span></span> <span data-ttu-id="1bffe-160">Wenn alle Entitäten im Graph eingefügt werden sollen, oder wenn alle Entitäten aktualisiert werden sollen, entspricht der Prozess dem oben beschriebenen Prozess für einzelne Entitäten.</span><span class="sxs-lookup"><span data-stu-id="1bffe-160">If all the entities in the graph should be inserted, or all should be updated, then the process is the same as described above for single entities.</span></span> <span data-ttu-id="1bffe-161">Beispiel: Ein wie folgt erstellter Graph mit Blogs und Beiträgen:</span><span class="sxs-lookup"><span data-stu-id="1bffe-161">For example, a graph of blogs and posts created like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

<span data-ttu-id="1bffe-162">kann wie folgt eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="1bffe-162">can be inserted like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertGraph)]

<span data-ttu-id="1bffe-163">Beim Aufruf zum Hinzufügen werden der Blog und sämtliche Beiträge für eine Einfügung markiert.</span><span class="sxs-lookup"><span data-stu-id="1bffe-163">The call to Add will mark the blog and all the posts to be inserted.</span></span>

<span data-ttu-id="1bffe-164">Gleichermaßen kann die Update-Methode verwendet werden, wenn sämtliche Entitäten in einem Graph aktualisiert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="1bffe-164">Likewise, if all the entities in a graph need to be updated, then Update can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#UpdateGraph)]

<span data-ttu-id="1bffe-165">Der Blog und alle zugehörigen Beiträge werden für ein Update markiert.</span><span class="sxs-lookup"><span data-stu-id="1bffe-165">The blog and all its posts will be marked to be updated.</span></span>

### <a name="mix-of-new-and-existing-entities"></a><span data-ttu-id="1bffe-166">Mischung aus neuen und vorhandenen Entitäten</span><span class="sxs-lookup"><span data-stu-id="1bffe-166">Mix of new and existing entities</span></span>

<span data-ttu-id="1bffe-167">Bei automatisch generierten Schlüsseln kann erneut für Einfügungen und Updates die Update-Methode verwendet werden. Dies gilt auch dann, wenn der Graph eine Mischung aus einzufügenden und zu aktualisierenden Entitäten enthält:</span><span class="sxs-lookup"><span data-stu-id="1bffe-167">With auto-generated keys, Update can again be used for both inserts and updates, even if the graph contains a mix of entities that require inserting and those that require updating:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

<span data-ttu-id="1bffe-168">Die Update-Methode markiert eine beliebige Entität im Graph, Blog oder Beitrag für eine Einfügung, wenn diese keinen festgelegten Schlüsselwert aufweist, während alle anderen Entitäten für eine Aktualisierung markiert werden.</span><span class="sxs-lookup"><span data-stu-id="1bffe-168">Update will mark any entity in the graph, blog or post, for insertion if it does not have a key value set, while all other entities are marked for update.</span></span>

<span data-ttu-id="1bffe-169">Wie bisher können eine Abfrage und eine Verarbeitungsschritte verwendet werden, wenn keine automatisch generierten Schlüssel verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="1bffe-169">As before, when not using auto-generated keys, a query and some processing can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a><span data-ttu-id="1bffe-170">Behandlung von Löschvorgängen</span><span class="sxs-lookup"><span data-stu-id="1bffe-170">Handling deletes</span></span>

<span data-ttu-id="1bffe-171">Die Behandlung von Löschvorgängen kann kompliziert sein, da die Abwesenheit einer Entität häufig bedeutet, dass diese gelöscht werden sollte.</span><span class="sxs-lookup"><span data-stu-id="1bffe-171">Delete can be tricky to handle since often the absence of an entity means that it should be deleted.</span></span> <span data-ttu-id="1bffe-172">Eine Möglichkeit für den Umgang hiermit besteht in der Verwendung von „vorläufigen Löschvorgängen“. Dabei wird die Entität als gelöscht markiert, statt tatsächlich gelöscht zu werden.</span><span class="sxs-lookup"><span data-stu-id="1bffe-172">One way to deal with this is to use "soft deletes" such that the entity is marked as deleted rather than actually being deleted.</span></span> <span data-ttu-id="1bffe-173">Löschvorgänge entsprechen anschließend Updates.</span><span class="sxs-lookup"><span data-stu-id="1bffe-173">Deletes then becomes the same as updates.</span></span> <span data-ttu-id="1bffe-174">Vorläufige Löschvorgänge können mit [Abfragefiltern](xref:core/querying/filters) implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="1bffe-174">Soft deletes can be implemented in using [query filters](xref:core/querying/filters).</span></span>

<span data-ttu-id="1bffe-175">Bei Löschvorgängen mit dem Wert „TRUE“ wird häufig eine Erweiterung des auszuführenden Abfragemusters verwendet. Dies ist im Grunde genommen eine GraphDiff-Methode.</span><span class="sxs-lookup"><span data-stu-id="1bffe-175">For true deletes, a common pattern is to use an extension of the query pattern to perform what is essentially a graph diff.</span></span> <span data-ttu-id="1bffe-176">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1bffe-176">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a><span data-ttu-id="1bffe-177">TrackGraph</span><span class="sxs-lookup"><span data-stu-id="1bffe-177">TrackGraph</span></span>

<span data-ttu-id="1bffe-178">Die Add-, Attach- und Update-Methoden verwenden intern einen Diagrammdurchlauf, in dem bestimmt wird, ob die einzelnen Entitäten als „Hinzugefügt“ (für Einfügung), „Geändert“ (für Update), „Unverändert“ (nichts unternehmen) oder als „Gelöscht“ (für Löschung) markiert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="1bffe-178">Internally, Add, Attach, and Update use graph-traversal with a determination made for each entity as to whether it should be marked as Added (to insert), Modified (to update), Unchanged (do nothing), or Deleted (to delete).</span></span> <span data-ttu-id="1bffe-179">Dieser Mechanismus wird über die TrackGraph-API verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="1bffe-179">This mechanism is exposed via the TrackGraph API.</span></span> <span data-ttu-id="1bffe-180">Angenommen beispielsweise, der Client sendet einen Graph mit Entitäten zurück und legt für jede Entität ein Flag fest, das angibt, wie die Entität behandelt werden soll.</span><span class="sxs-lookup"><span data-stu-id="1bffe-180">For example, let's assume that when the client sends back a graph of entities it sets some flag on each entity indicating how it should be handled.</span></span> <span data-ttu-id="1bffe-181">Anschließend kann dieses Flag mit TrackGraph verarbeitet werden:</span><span class="sxs-lookup"><span data-stu-id="1bffe-181">TrackGraph can then be used to process this flag:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#TrackGraph)]

<span data-ttu-id="1bffe-182">Zur Vereinfachung des Beispiels werden die Flags nur als Teil der Entität angezeigt.</span><span class="sxs-lookup"><span data-stu-id="1bffe-182">The flags are only shown as part of the entity for simplicity of the example.</span></span> <span data-ttu-id="1bffe-183">Normalerweise wären die Flags Teil eines DTO oder eines anderen in der Anforderung enthaltenen Status.</span><span class="sxs-lookup"><span data-stu-id="1bffe-183">Typically the flags would be part of a DTO or some other state included in the request.</span></span>
