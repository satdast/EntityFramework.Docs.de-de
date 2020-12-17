---
title: Arbeiten mit Proxys EF6
description: Arbeiten mit Proxys in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/proxies
ms.openlocfilehash: de44e891c074a8811e466d040f6451bea0f950f4
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635847"
---
# <a name="working-with-proxies"></a><span data-ttu-id="d4655-103">Arbeiten mit Proxys</span><span class="sxs-lookup"><span data-stu-id="d4655-103">Working with proxies</span></span>
<span data-ttu-id="d4655-104">Beim Erstellen von Instanzen von poco-Entitäts Typen erstellt Entity Framework häufig Instanzen eines dynamisch generierten abgeleiteten Typs, der als Proxy für die Entität fungiert.</span><span class="sxs-lookup"><span data-stu-id="d4655-104">When creating instances of POCO entity types, Entity Framework often creates instances of a dynamically generated derived type that acts as a proxy for the entity.</span></span> <span data-ttu-id="d4655-105">Dieser Proxy überschreibt einige virtuelle Eigenschaften der Entität, um beim Zugriff auf die Eigenschaft automatisch Hooks zum Ausführen von Aktionen einzufügen.</span><span class="sxs-lookup"><span data-stu-id="d4655-105">This proxy overrides some virtual properties of the entity to insert hooks for performing actions automatically when the property is accessed.</span></span> <span data-ttu-id="d4655-106">Dieser Mechanismus wird z. b. verwendet, um Lazy Loading von Beziehungen zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="d4655-106">For example, this mechanism is used to support lazy loading of relationships.</span></span> <span data-ttu-id="d4655-107">Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="d4655-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="disabling-proxy-creation"></a><span data-ttu-id="d4655-108">Proxy Erstellung wird deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="d4655-108">Disabling proxy creation</span></span>  

<span data-ttu-id="d4655-109">Manchmal ist es hilfreich, die Erstellung von Proxy Instanzen durch Entity Framework zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="d4655-109">Sometimes it is useful to prevent Entity Framework from creating proxy instances.</span></span> <span data-ttu-id="d4655-110">Beispielsweise ist das Serialisieren von nicht-Proxy Instanzen erheblich einfacher als das Serialisieren von Proxy Instanzen.</span><span class="sxs-lookup"><span data-stu-id="d4655-110">For example, serializing non-proxy instances is considerably easier than serializing proxy instances.</span></span> <span data-ttu-id="d4655-111">Die Proxy Erstellung kann deaktiviert werden, indem das-Flag gelöscht wird `ProxyCreationEnabled` .</span><span class="sxs-lookup"><span data-stu-id="d4655-111">Proxy creation can be turned off by clearing the `ProxyCreationEnabled` flag.</span></span> <span data-ttu-id="d4655-112">Ein Ort, an dem Sie dies vornehmen können, ist der Konstruktor Ihres Kontexts.</span><span class="sxs-lookup"><span data-stu-id="d4655-112">One place you could do this is in the constructor of your context.</span></span> <span data-ttu-id="d4655-113">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d4655-113">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.ProxyCreationEnabled = false;
    }  

    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="d4655-114">Beachten Sie, dass EF keine Proxys für Typen erstellt, bei denen es für den Proxy nichts gibt.</span><span class="sxs-lookup"><span data-stu-id="d4655-114">Note that the EF will not create proxies for types where there is nothing for the proxy to do.</span></span> <span data-ttu-id="d4655-115">Dies bedeutet, dass Sie Proxys auch vermeiden können, wenn Sie über Typen verfügen, die versiegelt sind und/oder keine virtuellen Eigenschaften haben.</span><span class="sxs-lookup"><span data-stu-id="d4655-115">This means that you can also avoid proxies by having types that are sealed and/or have no virtual properties.</span></span>  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a><span data-ttu-id="d4655-116">Explizites Erstellen einer Instanz eines Proxys</span><span class="sxs-lookup"><span data-stu-id="d4655-116">Explicitly creating an instance of a proxy</span></span>  

<span data-ttu-id="d4655-117">Eine Proxy Instanz wird nicht erstellt, wenn Sie mithilfe des new-Operators eine Instanz einer Entität erstellen.</span><span class="sxs-lookup"><span data-stu-id="d4655-117">A proxy instance will not be created if you create an instance of an entity using the new operator.</span></span> <span data-ttu-id="d4655-118">Dies stellt möglicherweise kein Problem dar. Wenn Sie jedoch eine Proxy Instanz erstellen müssen (z. b., damit Lazy Loading oder Proxy Änderungs Nachverfolgung funktioniert), können Sie dies mit der- `Create` Methode von tun `DbSet` .</span><span class="sxs-lookup"><span data-stu-id="d4655-118">This may not be a problem, but if you need to create a proxy instance (for example, so that lazy loading or proxy change tracking will work) then you can do so using the `Create` method of `DbSet`.</span></span> <span data-ttu-id="d4655-119">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d4655-119">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

<span data-ttu-id="d4655-120">Die generische Version von `Create` kann verwendet werden, wenn Sie eine Instanz eines abgeleiteten Entitäts Typs erstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="d4655-120">The generic version of `Create` can be used if you want to create an instance of a derived entity type.</span></span> <span data-ttu-id="d4655-121">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d4655-121">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

<span data-ttu-id="d4655-122">Beachten Sie, dass die- `Create` Methode die erstellte Entität nicht zum Kontext hinzufügt oder anfügt.</span><span class="sxs-lookup"><span data-stu-id="d4655-122">Note that the `Create` method does not add or attach the created entity to the context.</span></span>  

<span data-ttu-id="d4655-123">Beachten Sie, dass die- `Create` Methode nur eine Instanz des Entitäts Typs selbst erstellt, wenn die Erstellung eines Proxytyps für die Entität keinen Wert hätte, weil Sie nichts unternimmt.</span><span class="sxs-lookup"><span data-stu-id="d4655-123">Note that the `Create` method will just create an instance of the entity type itself if creating a proxy type for the entity would have no value because it would not do anything.</span></span> <span data-ttu-id="d4655-124">Wenn der Entitätstyp z. b. versiegelt ist und/oder keine virtuellen Eigenschaften aufweist, `Create` wird nur eine Instanz des Entitäts Typs erstellt.</span><span class="sxs-lookup"><span data-stu-id="d4655-124">For example, if the entity type is sealed and/or has no virtual properties then `Create` will just create an instance of the entity type.</span></span>  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a><span data-ttu-id="d4655-125">Der tatsächliche Entitätstyp wird von einem Proxytyp erhalten.</span><span class="sxs-lookup"><span data-stu-id="d4655-125">Getting the actual entity type from a proxy type</span></span>  

<span data-ttu-id="d4655-126">Proxy Typen haben Namen, die in etwa wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="d4655-126">Proxy types have names that look something like this:</span></span>  

```
System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6
```

<span data-ttu-id="d4655-127">Der Entitätstyp für diesen Proxytyp kann mithilfe der- `GetObjectType` Methode von gefunden werden `ObjectContext` .</span><span class="sxs-lookup"><span data-stu-id="d4655-127">You can find the entity type for this proxy type using the `GetObjectType` method from `ObjectContext`.</span></span> <span data-ttu-id="d4655-128">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d4655-128">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

<span data-ttu-id="d4655-129">Beachten Sie Folgendes: Wenn der an weiter `GetObjectType` gegebene Typ eine Instanz eines Entitäts Typs ist, der kein Proxytyp ist, wird der Entitätstyp weiterhin zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="d4655-129">Note that if the type passed to `GetObjectType` is an instance of an entity type that is not a proxy type then the type of entity is still returned.</span></span> <span data-ttu-id="d4655-130">Dies bedeutet, dass Sie immer diese Methode verwenden können, um den eigentlichen Entitätstyp ohne andere zu überprüfen, um festzustellen, ob es sich bei dem Typ um einen Proxytyp handelt.</span><span class="sxs-lookup"><span data-stu-id="d4655-130">This means you can always use this method to get the actual entity type without any other checking to see if the type is a proxy type or not.</span></span>  
