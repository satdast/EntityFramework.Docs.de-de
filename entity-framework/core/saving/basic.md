---
title: 'Grundlegender Speichervorgang: EF Core'
description: Grundlegende Informationen zum Hinzufügen, Aktualisieren und Entfernen von Daten mit Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/basic
ms.openlocfilehash: 8c98f95dee85641f11af716290333b47f340bff8
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129108"
---
# <a name="basic-save"></a><span data-ttu-id="4b1b7-103">Grundlegender Speichervorgang</span><span class="sxs-lookup"><span data-stu-id="4b1b7-103">Basic Save</span></span>

<span data-ttu-id="4b1b7-104">Erfahren Sie, wie Sie Daten mithilfe Ihrer Kontext- und Entitätsklassen hinzufügen, ändern und entfernen können.</span><span class="sxs-lookup"><span data-stu-id="4b1b7-104">Learn how to add, modify, and remove data using your context and entity classes.</span></span>

> [!TIP]
> <span data-ttu-id="4b1b7-105">Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Basics/) finden Sie auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="4b1b7-105">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Basics/) on GitHub.</span></span>

## <a name="adding-data"></a><span data-ttu-id="4b1b7-106">Hinzufügen von Daten</span><span class="sxs-lookup"><span data-stu-id="4b1b7-106">Adding Data</span></span>

<span data-ttu-id="4b1b7-107">Verwenden Sie die Methode *DbSet.Add*, um Ihren Entitätsklassen neue Instanzen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="4b1b7-107">Use the *DbSet.Add* method to add new instances of your entity classes.</span></span> <span data-ttu-id="4b1b7-108">Die Daten werden in die Datenbank eingefügt, wenn Sie *SaveChanges* aufrufen.</span><span class="sxs-lookup"><span data-stu-id="4b1b7-108">The data will be inserted in the database when you call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Add)]

> [!TIP]
> <span data-ttu-id="4b1b7-109">Die Methoden Add, Attach und Update wirken sich auf den vollständigen Graph an Entitäten aus, die an sie übergeben werden, wie im Abschnitt [Relevante Daten](xref:core/saving/related-data) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="4b1b7-109">The Add, Attach, and Update methods all work on the full graph of entities passed to them, as described in the [Related Data](xref:core/saving/related-data) section.</span></span> <span data-ttu-id="4b1b7-110">Die Eigenschaft EntityEntry.State kann alternativ zum Festlegen des Zustands einer einzelnen Entität verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4b1b7-110">Alternately, the EntityEntry.State property can be used to set the state of just a single entity.</span></span> <span data-ttu-id="4b1b7-111">Beispiel: `context.Entry(blog).State = EntityState.Modified`.</span><span class="sxs-lookup"><span data-stu-id="4b1b7-111">For example, `context.Entry(blog).State = EntityState.Modified`.</span></span>

## <a name="updating-data"></a><span data-ttu-id="4b1b7-112">Aktualisieren von Daten</span><span class="sxs-lookup"><span data-stu-id="4b1b7-112">Updating Data</span></span>

<span data-ttu-id="4b1b7-113">EF erkennt automatisch Änderungen an einer vorhandenen Entität, die nach dem Kontext nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="4b1b7-113">EF will automatically detect changes made to an existing entity that is tracked by the context.</span></span> <span data-ttu-id="4b1b7-114">Dazu gehören Entitäten, die Sie aus der Datenbank laden oder abfragen, und Entitäten, die vorher der Datenbank hinzugefügt und darin gespeichert wurden.</span><span class="sxs-lookup"><span data-stu-id="4b1b7-114">This includes entities that you load/query from the database, and entities that were previously added and saved to the database.</span></span>

<span data-ttu-id="4b1b7-115">Ändern Sie einfach die Werte der Eigenschaften, und rufen Sie dann *SaveChanges* auf.</span><span class="sxs-lookup"><span data-stu-id="4b1b7-115">Simply modify the values assigned to properties and then call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a><span data-ttu-id="4b1b7-116">Löschen von Daten</span><span class="sxs-lookup"><span data-stu-id="4b1b7-116">Deleting Data</span></span>

<span data-ttu-id="4b1b7-117">Verwenden Sie die Methode *DbSet.Remove*, um Instanzen Ihrer Entitätsklassen zu löschen.</span><span class="sxs-lookup"><span data-stu-id="4b1b7-117">Use the *DbSet.Remove* method to delete instances of your entity classes.</span></span>

<span data-ttu-id="4b1b7-118">Wenn die Entität bereits in der Datenbank vorhanden ist, wird sie während des Aufrufs von *SaveChanges* gelöscht.</span><span class="sxs-lookup"><span data-stu-id="4b1b7-118">If the entity already exists in the database, it will be deleted during *SaveChanges*.</span></span> <span data-ttu-id="4b1b7-119">Wenn die Entität noch nicht in der Datenbank gespeichert wurde (d.h. sie wird als „hinzugefügt“ nachverfolgt), wird sie aus dem Kontext entfernt und nicht mehr eingefügt, wenn *SaveChanges* aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="4b1b7-119">If the entity has not yet been saved to the database (that is, it is tracked as added) then it will be removed from the context and will no longer be inserted when *SaveChanges* is called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a><span data-ttu-id="4b1b7-120">Mehrere Vorgänge in einem Aufruf von SaveChanges</span><span class="sxs-lookup"><span data-stu-id="4b1b7-120">Multiple Operations in a single SaveChanges</span></span>

<span data-ttu-id="4b1b7-121">Sie können Hinzufügungs-, Update- und Entfernungsvorgänge in einem einzelnen Aufruf von *SaveChanges* kombinieren.</span><span class="sxs-lookup"><span data-stu-id="4b1b7-121">You can combine multiple Add/Update/Remove operations into a single call to *SaveChanges*.</span></span>

> [!NOTE]
> <span data-ttu-id="4b1b7-122">Für die meisten Datenbankanbieter ist *SaveChanges* transaktional.</span><span class="sxs-lookup"><span data-stu-id="4b1b7-122">For most database providers, *SaveChanges* is transactional.</span></span> <span data-ttu-id="4b1b7-123">Das bedeutet, dass alle Vorgänge entweder erfolgreich oder gar nicht abgeschlossen werden und niemals nur teilweise angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="4b1b7-123">This means  all the operations will either succeed or fail and the operations will never be left partially applied.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#MultipleOperations)]
