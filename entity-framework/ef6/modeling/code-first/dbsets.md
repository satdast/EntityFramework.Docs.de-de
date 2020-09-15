---
title: Definieren von dbsets-EF6
description: Definieren von dbsets in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/dbsets
ms.openlocfilehash: 49ffe3ddb8cd4f7c414d7d45d108405b88a0115e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073950"
---
# <a name="defining-dbsets"></a><span data-ttu-id="b7060-103">Definieren von dbsets</span><span class="sxs-lookup"><span data-stu-id="b7060-103">Defining DbSets</span></span>
<span data-ttu-id="b7060-104">Wenn Sie mit dem Code First-Workflow entwickeln, definieren Sie einen abgeleiteten dbcontext, der Ihre Sitzung mit der Datenbank repräsentiert und ein dbset für jeden Typ im Modell verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="b7060-104">When developing with the Code First workflow you define a derived DbContext that represents your session with the database and exposes a DbSet for each type in your model.</span></span> <span data-ttu-id="b7060-105">In diesem Thema werden die verschiedenen Möglichkeiten beschrieben, wie Sie die dbset-Eigenschaften definieren können.</span><span class="sxs-lookup"><span data-stu-id="b7060-105">This topic covers the various ways you can define the DbSet properties.</span></span>  

## <a name="dbcontext-with-dbset-properties"></a><span data-ttu-id="b7060-106">Dbcontext mit dbset-Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="b7060-106">DbContext with DbSet properties</span></span>  

<span data-ttu-id="b7060-107">Der gängige Fall, der in Code First Beispielen gezeigt wird, besteht darin, einen dbcontext mit öffentlichen automatischen dbset-Eigenschaften für die Entitäts Typen des Modells zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="b7060-107">The common case shown in Code First examples is to have a DbContext with public automatic DbSet properties for the entity types of your model.</span></span> <span data-ttu-id="b7060-108">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b7060-108">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="b7060-109">Bei der Verwendung im Code First Modus werden Blogs und Beiträge als Entitäts Typen konfiguriert und andere Typen konfiguriert, die von diesen verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="b7060-109">When used in Code First mode, this will configure Blogs and Posts as entity types, as well as configuring other types reachable from these.</span></span> <span data-ttu-id="b7060-110">Außerdem ruft dbcontext automatisch den Setter für jede dieser Eigenschaften auf, um eine Instanz des entsprechenden dbsets festzulegen.</span><span class="sxs-lookup"><span data-stu-id="b7060-110">In addition DbContext will automatically call the setter for each of these properties to set an instance of the appropriate DbSet.</span></span>  

## <a name="dbcontext-with-idbset-properties"></a><span data-ttu-id="b7060-111">Dbcontext mit idbset-Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="b7060-111">DbContext with IDbSet properties</span></span>  

<span data-ttu-id="b7060-112">Es gibt Situationen, z. b. beim Erstellen von Pseudo-oder Fakes, bei denen es nützlicher ist, die festgelegten Eigenschaften mithilfe einer Schnittstelle zu deklarieren.</span><span class="sxs-lookup"><span data-stu-id="b7060-112">There are situations, such as when creating mocks or fakes, where it is more useful to declare your set properties using an interface.</span></span> <span data-ttu-id="b7060-113">In solchen Fällen kann die idbset-Schnittstelle anstelle von dbset verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b7060-113">In such cases the IDbSet interface can be used in place of DbSet.</span></span> <span data-ttu-id="b7060-114">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b7060-114">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="b7060-115">Dieser Kontext funktioniert auf genau dieselbe Weise wie der Kontext, der die dbset-Klasse für seine Set-Eigenschaften verwendet.</span><span class="sxs-lookup"><span data-stu-id="b7060-115">This context works in exactly the same way as the context that uses the DbSet class for its set properties.</span></span>  

## <a name="dbcontext-with-read-only-set-properties"></a><span data-ttu-id="b7060-116">Dbcontext mit schreibgeschützten Set-Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="b7060-116">DbContext with read-only set properties</span></span>  

<span data-ttu-id="b7060-117">Wenn Sie keine öffentlichen Setter für die dbset-oder idbset-Eigenschaften verfügbar machen möchten, können Sie stattdessen schreibgeschützte Eigenschaften erstellen und die Set-Instanzen selbst erstellen.</span><span class="sxs-lookup"><span data-stu-id="b7060-117">If you do not wish to expose public setters for your DbSet or IDbSet properties you can instead create read-only properties and create the set instances yourself.</span></span> <span data-ttu-id="b7060-118">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b7060-118">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs
    {
        get { return Set<Blog>(); }
    }

    public DbSet<Post> Posts
    {
        get { return Set<Post>(); }
    }
}
```  

<span data-ttu-id="b7060-119">Beachten Sie, dass dbcontext die Instanz von dbset zwischenspeichert, die von der Set-Methode zurückgegeben wird, sodass jede dieser Eigenschaften bei jedem Aufruf dieselbe Instanz zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="b7060-119">Note that DbContext caches the instance of DbSet returned from the Set method so that each of these properties will return the same instance every time it is called.</span></span>  

<span data-ttu-id="b7060-120">Die Ermittlung von Entitäts Typen für Code First funktioniert auf dieselbe Weise wie bei Eigenschaften mit öffentlichen Getter und Settern.</span><span class="sxs-lookup"><span data-stu-id="b7060-120">Discovery of entity types for Code First works in the same way here as it does for properties with public getters and setters.</span></span>  
