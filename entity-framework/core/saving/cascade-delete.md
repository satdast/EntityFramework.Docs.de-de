---
title: Kaskadierendes Delete – EF Core
description: Konfigurieren von Löschverhalten für verknüpfte Entitäten bei Löschung einer Prinzipalentität
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ee8e14ec-2158-4c9c-96b5-118715e2ed9e
uid: core/saving/cascade-delete
ms.openlocfilehash: cf17e6586b89376b2d7fcc0f9cbfc8e1c4f6ba58
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617402"
---
# <a name="cascade-delete"></a><span data-ttu-id="78d1e-103">Kaskadierendes Delete</span><span class="sxs-lookup"><span data-stu-id="78d1e-103">Cascade Delete</span></span>

<span data-ttu-id="78d1e-104">Kaskadierendes Delete wird in der Datenbankterminologie häufig für die Beschreibung eines Merkmals verwendet, durch das beim Löschen einer Zeile automatisch das Löschen verknüpfter Zeilen ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="78d1e-104">Cascade delete is commonly used in database terminology to describe a characteristic that allows the deletion of a row to automatically trigger the deletion of related rows.</span></span> <span data-ttu-id="78d1e-105">Ein eng damit verbundenes Löschverhalten, das ebenfalls von EF Core abgedeckt wird, besteht im automatischen Löschen einer untergeordneten Entität, wenn ihre Beziehung zu einer übergeordneten Entität getrennt wurde. Dies ist allgemein bekannt unter „Löschen verwaister Entitäten“.</span><span class="sxs-lookup"><span data-stu-id="78d1e-105">A closely related concept also covered by EF Core delete behaviors is the automatic deletion of a child entity when it's relationship to a parent has been severed--this is commonly known as "deleting orphans".</span></span>

<span data-ttu-id="78d1e-106">EF Core implementiert mehrere unterschiedliche Verhaltensweisen zum Löschen und ermöglicht die Konfiguration dieser Verhaltensweisen für einzelne Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="78d1e-106">EF Core implements several different delete behaviors and allows for the configuration of the delete behaviors of individual relationships.</span></span> <span data-ttu-id="78d1e-107">Darüber hinaus implementiert EF Core Konventionen, mit denen basierend auf der [Erforderlichkeit der Beziehung](xref:core/modeling/relationships#required-and-optional-relationships) automatisch hilfreiches Standardlöschverhalten für die einzelnen Beziehungen konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="78d1e-107">EF Core also implements conventions that automatically configure useful default delete behaviors for each relationship based on the [requiredness of the relationship](xref:core/modeling/relationships#required-and-optional-relationships).</span></span>

## <a name="delete-behaviors"></a><span data-ttu-id="78d1e-108">Löschverhalten</span><span class="sxs-lookup"><span data-stu-id="78d1e-108">Delete behaviors</span></span>

<span data-ttu-id="78d1e-109">Das Löschverhalten wird im Enumeratortyp *DeleteBehavior* definiert und kann an die Fluent-API *OnDelete* übergeben werden, um zu steuern, ob das Löschen einer Prinzipalentität/übergeordneten Entität oder die Trennung der Beziehung zu abhängigen/untergeordneten Entitäten eine Nebenwirkung auf die abhängigen/untergeordneten Entitäten haben soll.</span><span class="sxs-lookup"><span data-stu-id="78d1e-109">Delete behaviors are defined in the *DeleteBehavior* enumerator type and can be passed to the *OnDelete* fluent API to control whether the deletion of a principal/parent entity or the severing of the relationship to dependent/child entities should have a side effect on the dependent/child entities.</span></span>

<span data-ttu-id="78d1e-110">EF kann drei Aktionen ausführen, wenn eine Prinzipalentität/übergeordnete Entität gelöscht oder die Beziehung zur untergeordneten Entität gelöscht wird:</span><span class="sxs-lookup"><span data-stu-id="78d1e-110">There are three actions EF can take when a principal/parent entity is deleted or the relationship to the child is severed:</span></span>

* <span data-ttu-id="78d1e-111">Die untergeordnete/abhängige Entität kann gelöscht werden</span><span class="sxs-lookup"><span data-stu-id="78d1e-111">The child/dependent can be deleted</span></span>
* <span data-ttu-id="78d1e-112">Die Fremdschlüsselwerte der untergeordneten Entität können auf NULL festgelegt werden</span><span class="sxs-lookup"><span data-stu-id="78d1e-112">The child's foreign key values can be set to null</span></span>
* <span data-ttu-id="78d1e-113">Die untergeordnete Entität bleibt unverändert</span><span class="sxs-lookup"><span data-stu-id="78d1e-113">The child remains unchanged</span></span>

> [!NOTE]  
> <span data-ttu-id="78d1e-114">Das im EF Core-Modell konfigurierte Löschverhalten wird nur angewendet, wenn die Prinzipalentität mit EF Core gelöscht wird und die abhängigen Entitäten in den Speicher geladen werden (d.h. für nachverfolgte abhängige Entitäten).</span><span class="sxs-lookup"><span data-stu-id="78d1e-114">The delete behavior configured in the EF Core model is only applied when the principal entity is deleted using EF Core and the dependent entities are loaded in memory (that is, for tracked dependents).</span></span> <span data-ttu-id="78d1e-115">In der Datenbank muss ein entsprechendes kaskadierendes Verhalten eingerichtet werden, um sicherzustellen, dass auf Daten, die nicht vom Kontext nachverfolgt werden, die erforderliche Aktion angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="78d1e-115">A corresponding cascade behavior needs to be setup in the database to ensure data that is not being tracked by the context has the necessary action applied.</span></span> <span data-ttu-id="78d1e-116">Wenn Sie die Datenbank mit EF Core erstellen, wird dieses kaskadierende Verhalten für Sie eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="78d1e-116">If you use EF Core to create the database, this cascade behavior will be setup for you.</span></span>

<span data-ttu-id="78d1e-117">Bei der zweiten, oben aufgeführten Aktion ist das Festlegen eines Fremdschlüsselwerts nicht gültig, wenn der Fremdschlüssel keine NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="78d1e-117">For the second action above, setting a foreign key value to null is not valid if foreign key is not nullable.</span></span> <span data-ttu-id="78d1e-118">(Ein Fremdschlüssel, der keine NULL-Werte zulässt, entspricht einer erforderlichen Beziehung.) In diesen Fällen verfolgt EF Core nach, ob die Fremdschlüsseleigenschaft bis zum Aufrufen von SaveChanges mit NULL markiert wurde. Zu diesem Zeitpunkt wird eine Ausnahme ausgelöst, da die Änderung in der Datenbank nicht beibehalten werden kann.</span><span class="sxs-lookup"><span data-stu-id="78d1e-118">(A non-nullable foreign key is equivalent to a required relationship.) In these cases, EF Core tracks that the foreign key property has been marked as null until SaveChanges is called, at which time an exception is thrown because the change cannot be persisted to the database.</span></span> <span data-ttu-id="78d1e-119">Dies ist vergleichbar mit dem Abrufen einer Einschränkungsverletzung aus der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="78d1e-119">This is similar to getting a constraint violation from the database.</span></span>

<span data-ttu-id="78d1e-120">Es gibt vier Verhaltensweisen zum Löschen, die in der nachfolgenden Tabelle aufgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="78d1e-120">There are four delete behaviors, as listed in the tables below.</span></span>

### <a name="optional-relationships"></a><span data-ttu-id="78d1e-121">Optionale Beziehungen</span><span class="sxs-lookup"><span data-stu-id="78d1e-121">Optional relationships</span></span>

<span data-ttu-id="78d1e-122">Bei optionalen Beziehungen (NULL-Werte zulassender Fremdschlüssel) _kann_ ein NULL-Fremdschlüsselwert gespeichert werden. Dies hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="78d1e-122">For optional relationships (nullable foreign key) it _is_ possible to save a null foreign key value, which results in the following effects:</span></span>

| <span data-ttu-id="78d1e-123">Name des Verhaltens</span><span class="sxs-lookup"><span data-stu-id="78d1e-123">Behavior Name</span></span>               | <span data-ttu-id="78d1e-124">Auswirkung auf abhängige/untergeordnete Entität im Speicher</span><span class="sxs-lookup"><span data-stu-id="78d1e-124">Effect on dependent/child in memory</span></span>    | <span data-ttu-id="78d1e-125">Auswirkung auf abhängige/untergeordnete Entität in der Datenbank</span><span class="sxs-lookup"><span data-stu-id="78d1e-125">Effect on dependent/child in database</span></span>  |
|:----------------------------|:---------------------------------------|:---------------------------------------|
| <span data-ttu-id="78d1e-126">**Cascade**</span><span class="sxs-lookup"><span data-stu-id="78d1e-126">**Cascade**</span></span>                 | <span data-ttu-id="78d1e-127">Entitäten werden gelöscht</span><span class="sxs-lookup"><span data-stu-id="78d1e-127">Entities are deleted</span></span>                   | <span data-ttu-id="78d1e-128">Entitäten werden gelöscht</span><span class="sxs-lookup"><span data-stu-id="78d1e-128">Entities are deleted</span></span>                   |
| <span data-ttu-id="78d1e-129">**ClientSetNull** (Standard)</span><span class="sxs-lookup"><span data-stu-id="78d1e-129">**ClientSetNull** (Default)</span></span> | <span data-ttu-id="78d1e-130">Fremdschlüsseleigenschaften werden auf NULL festgelegt</span><span class="sxs-lookup"><span data-stu-id="78d1e-130">Foreign key properties are set to null</span></span> | <span data-ttu-id="78d1e-131">Keine</span><span class="sxs-lookup"><span data-stu-id="78d1e-131">None</span></span>                                   |
| <span data-ttu-id="78d1e-132">**SetNull**</span><span class="sxs-lookup"><span data-stu-id="78d1e-132">**SetNull**</span></span>                 | <span data-ttu-id="78d1e-133">Fremdschlüsseleigenschaften werden auf NULL festgelegt</span><span class="sxs-lookup"><span data-stu-id="78d1e-133">Foreign key properties are set to null</span></span> | <span data-ttu-id="78d1e-134">Fremdschlüsseleigenschaften werden auf NULL festgelegt</span><span class="sxs-lookup"><span data-stu-id="78d1e-134">Foreign key properties are set to null</span></span> |
| <span data-ttu-id="78d1e-135">**Restrict**</span><span class="sxs-lookup"><span data-stu-id="78d1e-135">**Restrict**</span></span>                | <span data-ttu-id="78d1e-136">Keine</span><span class="sxs-lookup"><span data-stu-id="78d1e-136">None</span></span>                                   | <span data-ttu-id="78d1e-137">Keine</span><span class="sxs-lookup"><span data-stu-id="78d1e-137">None</span></span>                                   |

### <a name="required-relationships"></a><span data-ttu-id="78d1e-138">Erforderliche Beziehungen</span><span class="sxs-lookup"><span data-stu-id="78d1e-138">Required relationships</span></span>

<span data-ttu-id="78d1e-139">Bei erforderlichen Beziehungen (keine NULL-Werte zulassender Fremdschlüssel) kann _kein_ NULL-Fremdschlüsselwert gespeichert werden. Dies hat folgende Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="78d1e-139">For required relationships (non-nullable foreign key) it is _not_ possible to save a null foreign key value, which results in the following effects:</span></span>

| <span data-ttu-id="78d1e-140">Name des Verhaltens</span><span class="sxs-lookup"><span data-stu-id="78d1e-140">Behavior Name</span></span>         | <span data-ttu-id="78d1e-141">Auswirkung auf abhängige/untergeordnete Entität im Speicher</span><span class="sxs-lookup"><span data-stu-id="78d1e-141">Effect on dependent/child in memory</span></span> | <span data-ttu-id="78d1e-142">Auswirkung auf abhängige/untergeordnete Entität in der Datenbank</span><span class="sxs-lookup"><span data-stu-id="78d1e-142">Effect on dependent/child in database</span></span> |
|:----------------------|:------------------------------------|:--------------------------------------|
| <span data-ttu-id="78d1e-143">**Cascade** (Standard)</span><span class="sxs-lookup"><span data-stu-id="78d1e-143">**Cascade** (Default)</span></span> | <span data-ttu-id="78d1e-144">Entitäten werden gelöscht</span><span class="sxs-lookup"><span data-stu-id="78d1e-144">Entities are deleted</span></span>                | <span data-ttu-id="78d1e-145">Entitäten werden gelöscht</span><span class="sxs-lookup"><span data-stu-id="78d1e-145">Entities are deleted</span></span>                  |
| <span data-ttu-id="78d1e-146">**ClientSetNull**</span><span class="sxs-lookup"><span data-stu-id="78d1e-146">**ClientSetNull**</span></span>     | <span data-ttu-id="78d1e-147">Auslösung durch SaveChanges</span><span class="sxs-lookup"><span data-stu-id="78d1e-147">SaveChanges throws</span></span>                  | <span data-ttu-id="78d1e-148">Keine</span><span class="sxs-lookup"><span data-stu-id="78d1e-148">None</span></span>                                  |
| <span data-ttu-id="78d1e-149">**SetNull**</span><span class="sxs-lookup"><span data-stu-id="78d1e-149">**SetNull**</span></span>           | <span data-ttu-id="78d1e-150">Auslösung durch SaveChanges</span><span class="sxs-lookup"><span data-stu-id="78d1e-150">SaveChanges throws</span></span>                  | <span data-ttu-id="78d1e-151">Auslösung durch SaveChanges</span><span class="sxs-lookup"><span data-stu-id="78d1e-151">SaveChanges throws</span></span>                    |
| <span data-ttu-id="78d1e-152">**Restrict**</span><span class="sxs-lookup"><span data-stu-id="78d1e-152">**Restrict**</span></span>          | <span data-ttu-id="78d1e-153">Keine</span><span class="sxs-lookup"><span data-stu-id="78d1e-153">None</span></span>                                | <span data-ttu-id="78d1e-154">Keine</span><span class="sxs-lookup"><span data-stu-id="78d1e-154">None</span></span>                                  |

<span data-ttu-id="78d1e-155">In den obigen Tabellen kann *Keiner* zu einer Einschränkungsverletzung führen.</span><span class="sxs-lookup"><span data-stu-id="78d1e-155">In the tables above, *None* can result in a constraint violation.</span></span> <span data-ttu-id="78d1e-156">Wenn beispielsweise eine Prinzipalentität/untergeordnete Entität gelöscht wird, jedoch keine Maßnahmen zum Ändern des Fremdschlüssels einer abhängigen/untergeordneten Entität ergriffen werden, ist die Wahrscheinlichkeit groß, dass es in der Datenbank aufgrund einer Einschränkungsverletzung zu einer Auslösung durch SaveChanges kommt.</span><span class="sxs-lookup"><span data-stu-id="78d1e-156">For example, if a principal/child entity is deleted but no action is taken to change the foreign key of a dependent/child, then the database will likely throw on SaveChanges due to a foreign constraint violation.</span></span>

<span data-ttu-id="78d1e-157">Allgemeines:</span><span class="sxs-lookup"><span data-stu-id="78d1e-157">At a high level:</span></span>

* <span data-ttu-id="78d1e-158">Verwenden Sie *Cascade*, wenn es Entitäten gibt, die ohne übergeordnete Entität nicht vorhanden sein können und EF die untergeordnete Entität automatisch löschen soll.</span><span class="sxs-lookup"><span data-stu-id="78d1e-158">If you have entities that cannot exist without a parent, and you want EF to take care for deleting the children automatically, then use *Cascade*.</span></span>
  * <span data-ttu-id="78d1e-159">Entitäten, die ohne übergeordnete Entität nicht vorhanden sein können, verwenden in der Regel erforderliche Beziehungen. Hierfür gilt *Cascade* als Standard.</span><span class="sxs-lookup"><span data-stu-id="78d1e-159">Entities that cannot exist without a parent usually make use of required relationships, for which *Cascade* is the default.</span></span>
* <span data-ttu-id="78d1e-160">Verwenden Sie *ClientSetNull*, wenn es Entitäten gibt, die möglicherweise über eine übergeordnete Entität verfügen und EF den Fremdschlüssel für Sie auf NULL festlegen soll</span><span class="sxs-lookup"><span data-stu-id="78d1e-160">If you have entities that may or may not have a parent, and you want EF to take care of nulling out the foreign key for you, then use *ClientSetNull*</span></span>
  * <span data-ttu-id="78d1e-161">Entitäten, die ohne übergeordnete Entität vorhanden sein können, verwenden in der Regel optionale Beziehungen. Hierfür gilt *ClientSetNull* als Standard.</span><span class="sxs-lookup"><span data-stu-id="78d1e-161">Entities that can exist without a parent usually make use of optional relationships, for which *ClientSetNull* is the default.</span></span>
  * <span data-ttu-id="78d1e-162">Verwenden Sie *SetNull*, wenn in der Datenbank auch versucht werden soll, NULL-Werte an untergeordnete Fremdschlüssel zu verteilen, selbst wenn die untergeordnete Entität nicht geladen wurde.</span><span class="sxs-lookup"><span data-stu-id="78d1e-162">If you want the database to also try to propagate null values to child foreign keys even when the child entity is not loaded, then use *SetNull*.</span></span> <span data-ttu-id="78d1e-163">Beachten Sie jedoch, dass dieser Vorgang von der Datenbank unterstützt werden muss. Zudem kann eine derartige Konfiguration der Datenbank zu anderen Einschränkungen führen. Dadurch erweist sich diese Option in der Praxis häufig als unpraktisch.</span><span class="sxs-lookup"><span data-stu-id="78d1e-163">However, note that the database must support this, and configuring the database like this can result in other restrictions, which in practice often makes this option impractical.</span></span> <span data-ttu-id="78d1e-164">Aus diesem Grund ist *SetNull* nicht der Standard.</span><span class="sxs-lookup"><span data-stu-id="78d1e-164">This is why *SetNull* is not the default.</span></span>
* <span data-ttu-id="78d1e-165">Verwenden Sie *Restrict*, wenn EF Core eine Entität niemals automatisch löschen oder den Fremdschlüssel automatisch auf NULL festlegen soll.</span><span class="sxs-lookup"><span data-stu-id="78d1e-165">If you don't want EF Core to ever delete an entity automatically or null out the foreign key automatically, then use *Restrict*.</span></span> <span data-ttu-id="78d1e-166">Beachten Sie, dass untergeordnete Entitäten und die zugehörigen Fremdschlüsselwerte hierfür in Ihrem Code manuell synchron gehalten werden müssen. Andernfalls werden Einschränkungsausnahmen ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="78d1e-166">Note that this requires that your code keep child entities and their foreign key values in sync manually otherwise constraint exceptions will be thrown.</span></span>

> [!NOTE]
> <span data-ttu-id="78d1e-167">Anders als bei EF 6 treten kaskadierende Effekte in EF Core nicht automatisch auf, sondern nur, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="78d1e-167">In EF Core, unlike EF6, cascading effects do not happen immediately, but instead only when SaveChanges is called.</span></span>

> [!NOTE]  
> <span data-ttu-id="78d1e-168">**Änderungen in EF Core 2.0:** In vorherigen Releases hat *Restrict* dazu geführt, dass Eigenschaften optionaler Fremdschlüssel in nachverfolgten abhängigen Entitäten auf NULL festgelegt wurden. Dies war das Standardverhalten für optionale Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="78d1e-168">**Changes in EF Core 2.0:** In previous releases, *Restrict* would cause optional foreign key properties in tracked dependent entities to be set to null, and was the default delete behavior for optional relationships.</span></span> <span data-ttu-id="78d1e-169">In EF Core 2.0 wurde zur Darstellung dieses Verhaltens *ClientSetNull* eingeführt und als Standard für optionale Beziehungen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="78d1e-169">In EF Core 2.0, the *ClientSetNull* was introduced to represent that behavior and became the default for optional relationships.</span></span> <span data-ttu-id="78d1e-170">Das Verhalten von *Restrict* wurde so angepasst, dass niemals Nebenwirkungen bei abhängigen Entitäten auftreten.</span><span class="sxs-lookup"><span data-stu-id="78d1e-170">The behavior of *Restrict* was adjusted to never have any side effects on dependent entities.</span></span>

## <a name="entity-deletion-examples"></a><span data-ttu-id="78d1e-171">Beispiele für das Löschen von Entitäten</span><span class="sxs-lookup"><span data-stu-id="78d1e-171">Entity deletion examples</span></span>

<span data-ttu-id="78d1e-172">Der folgende Code ist Teil eines [Beispiels](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/), das heruntergeladen und ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="78d1e-172">The code below is part of a [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/) that can be downloaded and run.</span></span> <span data-ttu-id="78d1e-173">Das Beispiel zeigt, was bei jedem Löschverhalten bei optionalen und erforderlichen Beziehungen geschieht, wenn eine übergeordnete Entität gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="78d1e-173">The sample shows what happens for each delete behavior for both optional and required relationships when a parent entity is deleted.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteBehaviorVariations)]

<span data-ttu-id="78d1e-174">Zum Verständnis werden die einzelnen Variationen im Folgenden ausführlich betrachtet.</span><span class="sxs-lookup"><span data-stu-id="78d1e-174">Let's walk through each variation to understand what is happening.</span></span>

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a><span data-ttu-id="78d1e-175">DeleteBehavior.Cascade mit erforderlicher oder optionaler Beziehung</span><span class="sxs-lookup"><span data-stu-id="78d1e-175">DeleteBehavior.Cascade with required or optional relationship</span></span>

```console
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1

  After SaveChanges:
    Blog '1' is in state Detached with 2 posts referenced.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
      Post '2' is in state Detached with FK '1' and no reference to a blog.
```

* <span data-ttu-id="78d1e-176">Blog ist als gelöscht markiert</span><span class="sxs-lookup"><span data-stu-id="78d1e-176">Blog is marked as Deleted</span></span>
* <span data-ttu-id="78d1e-177">Beiträge bleiben anfangs unverändert, da es erst durch SaveChanges zu kaskadierendem Verhalten kommt</span><span class="sxs-lookup"><span data-stu-id="78d1e-177">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="78d1e-178">SaveChanges sendet Löschvorgänge für abhängige/untergeordnete Entitäten (Beiträge) und anschließend für die Prinzipalentität/übergeordnete Entität (Blog)</span><span class="sxs-lookup"><span data-stu-id="78d1e-178">SaveChanges sends deletes for both dependents/children (posts) and then the principal/parent (blog)</span></span>
* <span data-ttu-id="78d1e-179">Nach dem Speichern werden alle Entitäten getrennt, da sie jetzt aus der Datenbank gelöscht wurden</span><span class="sxs-lookup"><span data-stu-id="78d1e-179">After saving, all entities are detached since they have now been deleted from the database</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a><span data-ttu-id="78d1e-180">DeleteBehavior.ClientSetNull oder DeleteBehavior.SetNull mit erforderlicher Beziehung</span><span class="sxs-lookup"><span data-stu-id="78d1e-180">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with required relationship</span></span>

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

  SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* <span data-ttu-id="78d1e-181">Blog ist als gelöscht markiert</span><span class="sxs-lookup"><span data-stu-id="78d1e-181">Blog is marked as Deleted</span></span>
* <span data-ttu-id="78d1e-182">Beiträge bleiben anfangs unverändert, da es erst durch SaveChanges zu kaskadierendem Verhalten kommt</span><span class="sxs-lookup"><span data-stu-id="78d1e-182">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="78d1e-183">SaveChanges versucht, den FS des Beitrags auf NULL festzulegen. Dieser Vorgang schlägt jedoch fehl, da der FS nicht auf NULL festgelegt werden kann</span><span class="sxs-lookup"><span data-stu-id="78d1e-183">SaveChanges attempts to set the post FK to null, but this fails because the FK is not nullable</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a><span data-ttu-id="78d1e-184">DeleteBehavior.ClientSetNull oder DeleteBehavior.SetNull mit optionaler Beziehung</span><span class="sxs-lookup"><span data-stu-id="78d1e-184">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with optional relationship</span></span>

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1

  After SaveChanges:
    Blog '1' is in state Detached with 2 posts referenced.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
      Post '2' is in state Unchanged with FK 'null' and no reference to a blog.
```

* <span data-ttu-id="78d1e-185">Blog ist als gelöscht markiert</span><span class="sxs-lookup"><span data-stu-id="78d1e-185">Blog is marked as Deleted</span></span>
* <span data-ttu-id="78d1e-186">Beiträge bleiben anfangs unverändert, da es erst durch SaveChanges zu kaskadierendem Verhalten kommt</span><span class="sxs-lookup"><span data-stu-id="78d1e-186">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="78d1e-187">SaveChanges versucht, den FS von abhängigen/untergeordneten Entitäten (Beiträgen) vor dem Löschen der Prinzipalentität/übergeordneten Entität (Blog) auf NULL festzulegen</span><span class="sxs-lookup"><span data-stu-id="78d1e-187">SaveChanges attempts sets the FK of both dependents/children (posts) to null before deleting the principal/parent (blog)</span></span>
* <span data-ttu-id="78d1e-188">Nach dem Speichern wird die Prinzipalentität/übergeordnete Entität (Blog) gelöscht, die abhängigen/untergeordneten Entitäten (Beiträge) werden jedoch weiter nachverfolgt</span><span class="sxs-lookup"><span data-stu-id="78d1e-188">After saving, the principal/parent (blog) is deleted, but the dependents/children (posts) are still tracked</span></span>
* <span data-ttu-id="78d1e-189">Die nachverfolgten abhängigen/untergeordneten Entitäten (Beiträge) verfügen jetzt über NULL-FS-Werte und ihr Verweis auf die gelöschte Prinzipalentität/übergeordnete Entität (Blog) wurde entfernt</span><span class="sxs-lookup"><span data-stu-id="78d1e-189">The tracked dependents/children (posts) now have null FK values and their reference to the deleted principal/parent (blog) has been removed</span></span>

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a><span data-ttu-id="78d1e-190">DeleteBehavior.Restrict mit erforderlicher oder optionaler Beziehung</span><span class="sxs-lookup"><span data-stu-id="78d1e-190">DeleteBehavior.Restrict with required or optional relationship</span></span>

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After deleting blog '1':
    Blog '1' is in state Deleted with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  Saving changes:
  SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* <span data-ttu-id="78d1e-191">Blog ist als gelöscht markiert</span><span class="sxs-lookup"><span data-stu-id="78d1e-191">Blog is marked as Deleted</span></span>
* <span data-ttu-id="78d1e-192">Beiträge bleiben anfangs unverändert, da es erst durch SaveChanges zu kaskadierendem Verhalten kommt</span><span class="sxs-lookup"><span data-stu-id="78d1e-192">Posts initially remain Unchanged since cascades do not happen until SaveChanges</span></span>
* <span data-ttu-id="78d1e-193">Da *Restrict* EF die Anweisung gibt, den FS nicht automatisch auf NULL festzulegen, bleibt der Wert ungleich NULL; es kommt zu einer Auslösung durch SaveChanges ohne Speichern</span><span class="sxs-lookup"><span data-stu-id="78d1e-193">Since *Restrict* tells EF to not automatically set the FK to null, it remains non-null and SaveChanges throws without saving</span></span>

## <a name="delete-orphans-examples"></a><span data-ttu-id="78d1e-194">Beispiele für das Löschen verwaister Entitäten</span><span class="sxs-lookup"><span data-stu-id="78d1e-194">Delete orphans examples</span></span>

<span data-ttu-id="78d1e-195">Der folgende Code ist Teil eines [Beispiels](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/), das heruntergeladen und ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="78d1e-195">The code below is part of a [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/) that can be downloaded and run.</span></span> <span data-ttu-id="78d1e-196">Im Beispiel wird gezeigt, was bei jedem Löschverhalten bei optionalen und erforderlichen Beziehungen geschieht, wenn die Beziehung zwischen einer Prinzipalentität/übergeordneten Entität und den zugehörigen abhängigen/untergeordneten Entitäten getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="78d1e-196">The sample shows what happens for each delete behavior for both optional and required relationships when the relationship between a parent/principal and its children/dependents is severed.</span></span> <span data-ttu-id="78d1e-197">In diesem Beispiel wird die Beziehung durch Entfernen der abhängigen/untergeordneten Entitäten (Beiträge) aus der Navigationseigenschaft der Sammlung in der Prinzipalentität/übergeordneten Entität (Blog) getrennt.</span><span class="sxs-lookup"><span data-stu-id="78d1e-197">In this example, the relationship is severed by removing the dependents/children (posts) from the collection navigation property on the principal/parent (blog).</span></span> <span data-ttu-id="78d1e-198">Das Verhalten ist jedoch identisch, wenn der Verweis von abhängigen/untergeordneten Entitäten auf die Prinzipalentität/übergeordnete Entität stattdessen auf NULL festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="78d1e-198">However, the behavior is the same if the reference from dependent/child to principal/parent is instead nulled out.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteOrphansVariations)]

<span data-ttu-id="78d1e-199">Zum Verständnis werden die einzelnen Variationen im Folgenden ausführlich betrachtet.</span><span class="sxs-lookup"><span data-stu-id="78d1e-199">Let's walk through each variation to understand what is happening.</span></span>

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a><span data-ttu-id="78d1e-200">DeleteBehavior.Cascade mit erforderlicher oder optionaler Beziehung</span><span class="sxs-lookup"><span data-stu-id="78d1e-200">DeleteBehavior.Cascade with required or optional relationship</span></span>

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK '1' and no reference to a blog.
      Post '2' is in state Modified with FK '1' and no reference to a blog.

  Saving changes:
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2

  After SaveChanges:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Detached with FK '1' and no reference to a blog.
      Post '2' is in state Detached with FK '1' and no reference to a blog.
```

* <span data-ttu-id="78d1e-201">Beiträge werden als „Geändert“ markiert, da der FS durch die Trennung der Beziehung als NULL markiert wurde</span><span class="sxs-lookup"><span data-stu-id="78d1e-201">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="78d1e-202">Wenn der FS nicht auf NULL festgelegt werden kann, wird der eigentliche Wert nicht geändert, auch wenn er als NULL markiert ist</span><span class="sxs-lookup"><span data-stu-id="78d1e-202">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="78d1e-203">SaveChanges sendet Löschvorgänge für abhängige/untergeordnete Entitäten (Beiträge)</span><span class="sxs-lookup"><span data-stu-id="78d1e-203">SaveChanges sends deletes for dependents/children (posts)</span></span>
* <span data-ttu-id="78d1e-204">Nach dem Speichern werden die abhängigen/untergeordneten Entitäten (Beiträge) getrennt, da sie jetzt aus der Datenbank gelöscht wurden</span><span class="sxs-lookup"><span data-stu-id="78d1e-204">After saving, the dependents/children (posts) are detached since they have now been deleted from the database</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a><span data-ttu-id="78d1e-205">DeleteBehavior.ClientSetNull oder DeleteBehavior.SetNull mit erforderlicher Beziehung</span><span class="sxs-lookup"><span data-stu-id="78d1e-205">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with required relationship</span></span>

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.
      Post '2' is in state Modified with FK 'null' and no reference to a blog.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

  SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* <span data-ttu-id="78d1e-206">Beiträge werden als „Geändert“ markiert, da der FS durch die Trennung der Beziehung als NULL markiert wurde</span><span class="sxs-lookup"><span data-stu-id="78d1e-206">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="78d1e-207">Wenn der FS nicht auf NULL festgelegt werden kann, wird der eigentliche Wert nicht geändert, auch wenn er als NULL markiert ist</span><span class="sxs-lookup"><span data-stu-id="78d1e-207">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="78d1e-208">SaveChanges versucht, den FS des Beitrags auf NULL festzulegen. Dieser Vorgang schlägt jedoch fehl, da der FS nicht auf NULL festgelegt werden kann</span><span class="sxs-lookup"><span data-stu-id="78d1e-208">SaveChanges attempts to set the post FK to null, but this fails because the FK is not nullable</span></span>

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a><span data-ttu-id="78d1e-209">DeleteBehavior.ClientSetNull oder DeleteBehavior.SetNull mit optionaler Beziehung</span><span class="sxs-lookup"><span data-stu-id="78d1e-209">DeleteBehavior.ClientSetNull or DeleteBehavior.SetNull with optional relationship</span></span>

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK 'null' and no reference to a blog.
      Post '2' is in state Modified with FK 'null' and no reference to a blog.

  Saving changes:
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
    UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2

  After SaveChanges:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
      Post '2' is in state Unchanged with FK 'null' and no reference to a blog.
```

* <span data-ttu-id="78d1e-210">Beiträge werden als „Geändert“ markiert, da der FS durch die Trennung der Beziehung als NULL markiert wurde</span><span class="sxs-lookup"><span data-stu-id="78d1e-210">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="78d1e-211">Wenn der FS nicht auf NULL festgelegt werden kann, wird der eigentliche Wert nicht geändert, auch wenn er als NULL markiert ist</span><span class="sxs-lookup"><span data-stu-id="78d1e-211">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="78d1e-212">SaveChanges legt den FS von abhängigen/untergeordneten Entitäten auf NULL fest</span><span class="sxs-lookup"><span data-stu-id="78d1e-212">SaveChanges sets the FK of both dependents/children (posts) to null</span></span>
* <span data-ttu-id="78d1e-213">Nach dem Speichern verfügen die nachverfolgten abhängigen/untergeordneten Entitäten (Beiträge) jetzt über NULL-FS-Werte und ihr Verweis auf die gelöschte Prinzipalentität/übergeordnete Entität (Blog) wurde entfernt</span><span class="sxs-lookup"><span data-stu-id="78d1e-213">After saving, the dependents/children (posts) now have null FK values and their reference to the deleted principal/parent (blog) has been removed</span></span>

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a><span data-ttu-id="78d1e-214">DeleteBehavior.Restrict mit erforderlicher oder optionaler Beziehung</span><span class="sxs-lookup"><span data-stu-id="78d1e-214">DeleteBehavior.Restrict with required or optional relationship</span></span>

``` output
  After loading entities:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
      Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

  After making posts orphans:
    Blog '1' is in state Unchanged with 2 posts referenced.
      Post '1' is in state Modified with FK '1' and no reference to a blog.
      Post '2' is in state Modified with FK '1' and no reference to a blog.

  Saving changes:
  SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* <span data-ttu-id="78d1e-215">Beiträge werden als „Geändert“ markiert, da der FS durch die Trennung der Beziehung als NULL markiert wurde</span><span class="sxs-lookup"><span data-stu-id="78d1e-215">Posts are marked as Modified because severing the relationship caused the FK to be marked as null</span></span>
  * <span data-ttu-id="78d1e-216">Wenn der FS nicht auf NULL festgelegt werden kann, wird der eigentliche Wert nicht geändert, auch wenn er als NULL markiert ist</span><span class="sxs-lookup"><span data-stu-id="78d1e-216">If the FK is not nullable, then the actual value will not change even though it is marked as null</span></span>
* <span data-ttu-id="78d1e-217">Da *Restrict* EF die Anweisung gibt, den FS nicht automatisch auf NULL festzulegen, bleibt der Wert ungleich NULL; es kommt zu einer Auslösung durch SaveChanges ohne Speichern</span><span class="sxs-lookup"><span data-stu-id="78d1e-217">Since *Restrict* tells EF to not automatically set the FK to null, it remains non-null and SaveChanges throws without saving</span></span>

## <a name="cascading-to-untracked-entities"></a><span data-ttu-id="78d1e-218">Weitergabe an nicht verfolgte Entitäten</span><span class="sxs-lookup"><span data-stu-id="78d1e-218">Cascading to untracked entities</span></span>

<span data-ttu-id="78d1e-219">Beim Aufruf von *SaveChanges* werden die Regeln des kaskadierenden Delete auf alle Entitäten angewendet, die vom Kontext nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="78d1e-219">When you call *SaveChanges*, the cascade delete rules will be applied to any entities that are being tracked by the context.</span></span> <span data-ttu-id="78d1e-220">Dies ist in allen oben aufgeführten Beispielen der Fall. Deshalb wurde SQL generiert, um die Prinzipalentität/übergeordnete Entität (Blog) und sämtliche abhängigen/untergeordneten Entitäten (Beiträge) zu löschen:</span><span class="sxs-lookup"><span data-stu-id="78d1e-220">This is the situation in all the examples shown above, which is why SQL was generated to delete both the principal/parent (blog) and all the dependents/children (posts):</span></span>

```sql
    DELETE FROM [Posts] WHERE [PostId] = 1
    DELETE FROM [Posts] WHERE [PostId] = 2
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

<span data-ttu-id="78d1e-221">Wenn nur die Prinzipalentität geladen wird – beispielsweise beim Ausführen einer Abfrage für einen Blog ohne `Include(b => b.Posts)` zum Einbeziehen von Beiträgen – generiert SaveChanges SQL nur zum Löschen der Prinzipalentität/übergeordneten Entität:</span><span class="sxs-lookup"><span data-stu-id="78d1e-221">If only the principal is loaded--for example, when a query is made for a blog without an `Include(b => b.Posts)` to also include posts--then SaveChanges will only generate SQL to delete the principal/parent:</span></span>

```sql
    DELETE FROM [Blogs] WHERE [BlogId] = 1
```

<span data-ttu-id="78d1e-222">Die abhängigen/untergeordneten Entitäten (Beiträge) werden nur gelöscht, wenn in der Datenbank ein entsprechendes kaskadierendes Verhalten konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="78d1e-222">The dependents/children (posts) will only be deleted if the database has a corresponding cascade behavior configured.</span></span> <span data-ttu-id="78d1e-223">Wenn Sie die Datenbank mit EF erstellen, wird dieses kaskadierende Verhalten für Sie eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="78d1e-223">If you use EF to create the database, this cascade behavior will be setup for you.</span></span>
