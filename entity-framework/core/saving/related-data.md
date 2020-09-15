---
title: 'Speichern verwandter Daten: EF Core'
description: Informationen zur Speicherung von Graphen für verknüpfte Entitäten und Verwaltung von Beziehungen in Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 07b6680f-ffcf-412c-9857-f997486b386c
uid: core/saving/related-data
ms.openlocfilehash: 118d5933dd543a03bbe16fd8be1f00b7304e39c4
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618887"
---
# <a name="saving-related-data"></a><span data-ttu-id="f02f7-103">Speichern relevanter Daten</span><span class="sxs-lookup"><span data-stu-id="f02f7-103">Saving Related Data</span></span>

<span data-ttu-id="f02f7-104">Zusätzlich zu isolierten Entitäten können Sie auch die Beziehungen verwenden, die in Ihrem Modell definiert sind.</span><span class="sxs-lookup"><span data-stu-id="f02f7-104">In addition to isolated entities, you can also make use of the relationships defined in your model.</span></span>

> [!TIP]  
> <span data-ttu-id="f02f7-105">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/RelatedData/) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="f02f7-105">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/RelatedData/) on GitHub.</span></span>

## <a name="adding-a-graph-of-new-entities"></a><span data-ttu-id="f02f7-106">Hinzufügen eines Diagramms neuer Entitäten</span><span class="sxs-lookup"><span data-stu-id="f02f7-106">Adding a graph of new entities</span></span>

<span data-ttu-id="f02f7-107">Wenn Sie mehrere neue verknüpfte Entitäten erstellen, und Sie eine dem Kontext hinzufügen, werden die anderen ebenfalls hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="f02f7-107">If you create several new related entities, adding one of them to the context will cause the others to be added too.</span></span>

<span data-ttu-id="f02f7-108">Im folgenden Beispiel werden der Blog und drei relevante Beiträge in die Datenbank eingefügt.</span><span class="sxs-lookup"><span data-stu-id="f02f7-108">In the following example, the blog and three related posts are all inserted into the database.</span></span> <span data-ttu-id="f02f7-109">Die Beiträge werden gefunden und hinzugefügt, da sie über die Navigationseigenschaft `Blog.Posts` erreichbar sind.</span><span class="sxs-lookup"><span data-stu-id="f02f7-109">The posts are found and added, because they are reachable via the `Blog.Posts` navigation property.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingGraphOfEntities)]

> [!TIP]  
> <span data-ttu-id="f02f7-110">Verwenden Sie die Eigenschaft EntityEntry.State, um den Zustand einer einzelnen Entität festzulegen.</span><span class="sxs-lookup"><span data-stu-id="f02f7-110">Use the EntityEntry.State property to set the state of just a single entity.</span></span> <span data-ttu-id="f02f7-111">Beispiel: `context.Entry(blog).State = EntityState.Modified`.</span><span class="sxs-lookup"><span data-stu-id="f02f7-111">For example, `context.Entry(blog).State = EntityState.Modified`.</span></span>

## <a name="adding-a-related-entity"></a><span data-ttu-id="f02f7-112">Hinzufügen einer verknüpften Entität</span><span class="sxs-lookup"><span data-stu-id="f02f7-112">Adding a related entity</span></span>

<span data-ttu-id="f02f7-113">Wenn Sie auf eine neue Entität aus der Navigationseigenschaft einer Entität verweisen, die bereits über den Kontext nachverfolgt wird, wird die Entität erkannt und in die Datenbank eingefügt.</span><span class="sxs-lookup"><span data-stu-id="f02f7-113">If you reference a new entity from the navigation property of an entity that is already tracked by the context, the entity will be discovered and inserted into the database.</span></span>

<span data-ttu-id="f02f7-114">Im folgenden Beispiel wird die Entität `post` eingefügt, da sie der `Posts`-Eigenschaft der `blog`-Entität hinzugefügt wird, die aus der Datenbank abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="f02f7-114">In the following example, the `post` entity is inserted because it is added to the `Posts` property of the `blog` entity which was fetched from the database.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingRelatedEntity)]

## <a name="changing-relationships"></a><span data-ttu-id="f02f7-115">Ändern von Beziehungen</span><span class="sxs-lookup"><span data-stu-id="f02f7-115">Changing relationships</span></span>

<span data-ttu-id="f02f7-116">Wenn Sie die Navigationseigenschaft einer Entität ändern, werden die entsprechenden Änderungen an der Fremdschlüsselspalte in der Datenbank vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="f02f7-116">If you change the navigation property of an entity, the corresponding changes will be made to the foreign key column in the database.</span></span>

<span data-ttu-id="f02f7-117">Im folgenden Beispiel wird die Entität `post` aktualisiert, sodass sie zur neuen `blog`-Entität gehört, da ihre `Blog`-Navigationseigenschaft so festgelegt ist, dass sie auf `blog` zeigt.</span><span class="sxs-lookup"><span data-stu-id="f02f7-117">In the following example, the `post` entity is updated to belong to the new `blog` entity because its `Blog` navigation property is set to point to `blog`.</span></span> <span data-ttu-id="f02f7-118">Beachten Sie, dass `blog` ebenfalls in die Datenbank eingefügt wird, da es eine neue Entität ist, die von der Navigationseigenschaft einer Entität referenziert wird, die bereits über den Kontext (`post`) nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="f02f7-118">Note that `blog` will also be inserted into the database because it is a new entity that is referenced by the navigation property of an entity that is already tracked by the context (`post`).</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#ChangingRelationships)]

## <a name="removing-relationships"></a><span data-ttu-id="f02f7-119">Entfernen von Beziehungen</span><span class="sxs-lookup"><span data-stu-id="f02f7-119">Removing relationships</span></span>

<span data-ttu-id="f02f7-120">Sie können eine Beziehung entfernen, indem Sie eine Verweisnavigation auf `null` festlegen oder die verknüpfte Entität aus einer Auflistungsnavigation entfernen.</span><span class="sxs-lookup"><span data-stu-id="f02f7-120">You can remove a relationship by setting a reference navigation to `null`, or removing the related entity from a collection navigation.</span></span>

<span data-ttu-id="f02f7-121">Das Entfernen einer Beziehung kann Nebeneffekte gemäß dem in der Beziehung konfigurierten Verhalten des kaskadierenden Deletes für die abhängige Entität haben.</span><span class="sxs-lookup"><span data-stu-id="f02f7-121">Removing a relationship can have side effects on the dependent entity, according to the cascade delete behavior configured in the relationship.</span></span>

<span data-ttu-id="f02f7-122">Das Verhalten eines kaskadierenden Deletes wird standardmäßig für erforderliche Beziehungen konfiguriert, und die untergeordnete bzw. abhängige Entität wird aus der Datenbank gelöscht.</span><span class="sxs-lookup"><span data-stu-id="f02f7-122">By default, for required relationships, a cascade delete behavior is configured and the child/dependent entity will be deleted from the database.</span></span> <span data-ttu-id="f02f7-123">Das kaskadierende Delete wird für optionale Beziehungen nicht standardmäßig konfiguriert, aber die Fremdschlüsseleigenschaft wird auf NULL festgelegt.</span><span class="sxs-lookup"><span data-stu-id="f02f7-123">For optional relationships, cascade delete is not configured by default, but the foreign key property will be set to null.</span></span>

<span data-ttu-id="f02f7-124">Informationen darüber, wie die Erforderlichkeit von Beziehungen konfiguriert wird, finden Sie unter [Required and Optional Relationships (Erforderliche und optionale Beziehungen)](xref:core/modeling/relationships#required-and-optional-relationships).</span><span class="sxs-lookup"><span data-stu-id="f02f7-124">See [Required and Optional Relationships](xref:core/modeling/relationships#required-and-optional-relationships) to learn about how the requiredness of relationships can be configured.</span></span>

<span data-ttu-id="f02f7-125">Ausführliche Informationen zur Funktionsweise des kaskadierenden Deletes, wie es explizit konfiguriert werden kann und wie es gemäß der Konvention ausgewählt wird, finden Sie unter [Cascade Delete (Kaskadierendes Delete)](xref:core/saving/cascade-delete).</span><span class="sxs-lookup"><span data-stu-id="f02f7-125">See [Cascade Delete](xref:core/saving/cascade-delete) for more details on how cascade delete behaviors work, how they can be configured explicitly and  how they are selected by convention.</span></span>

<span data-ttu-id="f02f7-126">Im folgenden Beispiel wird das kaskadierende Delete der Beziehung zwischen `Blog` und `Post` konfiguriert, sodass die Entität `post` aus der Datenbank gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="f02f7-126">In the following example, a cascade delete is configured on the relationship between `Blog` and `Post`, so the `post` entity is deleted from the database.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#RemovingRelationships)]
