---
title: Lazy Loading zugehöriger Daten – EF Core
description: Lazy Loading zugehöriger Daten mit Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/lazy
ms.openlocfilehash: c42cde469e2be38d53a46cb6c5c252a088978e5c
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078867"
---
# <a name="lazy-loading-of-related-data"></a><span data-ttu-id="a280b-103">Lazy Loading zugehöriger Daten</span><span class="sxs-lookup"><span data-stu-id="a280b-103">Lazy Loading of Related Data</span></span>

## <a name="lazy-loading-with-proxies"></a><span data-ttu-id="a280b-104">Lazy Loading mit Proxys</span><span class="sxs-lookup"><span data-stu-id="a280b-104">Lazy loading with proxies</span></span>

<span data-ttu-id="a280b-105">Die einfachste Möglichkeit für die Verwendung von Lazy Loading besteht in der Installation des Pakets [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) und der Aktivierung dieses Pakets durch Aufrufen von `UseLazyLoadingProxies`.</span><span class="sxs-lookup"><span data-stu-id="a280b-105">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="a280b-106">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a280b-106">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="a280b-107">Oder bei Verwendung von AddDbContext:</span><span class="sxs-lookup"><span data-stu-id="a280b-107">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="a280b-108">EF Core aktiviert Lazy Loading anschließend für beliebige überschreibbare Navigationseigenschaften – diese müssen `virtual` und in einer Klasse enthalten sein, aus der sie geerbt werden können.</span><span class="sxs-lookup"><span data-stu-id="a280b-108">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="a280b-109">In den folgenden Entitäten wird beispielsweise Lazy Loading für die Navigationseigenschaften `Post.Blog` und `Blog.Posts` durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="a280b-109">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```

## <a name="lazy-loading-without-proxies"></a><span data-ttu-id="a280b-110">Lazy Loading ohne Proxys</span><span class="sxs-lookup"><span data-stu-id="a280b-110">Lazy loading without proxies</span></span>

<span data-ttu-id="a280b-111">Lazy Loading-Proxys funktionieren, indem der Dienst `ILazyLoader` in eine Entität eingefügt wird, wie unter [Entitätstypenkonstruktoren](xref:core/modeling/constructors) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="a280b-111">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](xref:core/modeling/constructors).</span></span> <span data-ttu-id="a280b-112">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a280b-112">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="a280b-113">Für diese Methode ist nicht erforderlich, dass aus Entitätstypen vererbt wird oder Navigationseigenschaften „virtual“ sind. Mit `new` erstellte Entitätsinstanzen können Lazy Loading ausführen, sobald sie einem Kontext angefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="a280b-113">This method doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="a280b-114">Erforderlich ist ein Verweis auf den `ILazyLoader`-Dienst, der im [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/)-Paket definiert ist.</span><span class="sxs-lookup"><span data-stu-id="a280b-114">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="a280b-115">Dieses Paket enthält eine minimale Gruppe von Typen, sodass die Abhängigkeit davon kaum Auswirkungen hat.</span><span class="sxs-lookup"><span data-stu-id="a280b-115">This package contains a minimal set of types so that there is little impact in depending on it.</span></span> <span data-ttu-id="a280b-116">Es ist jedoch möglich, die Methode `ILazyLoader.Load` als Delegat einzufügen, um eine Abhängigkeit von EF Core-Paketen in den Entitätstypen gänzlich zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="a280b-116">However, to completely avoid depending on any EF Core packages in the entity types, it's possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="a280b-117">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a280b-117">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="a280b-118">Der oben stehende Code verwendet eine `Load`-Erweiterungsmethode, um die Verwendung des Delegaten sauberer zu gestalten:</span><span class="sxs-lookup"><span data-stu-id="a280b-118">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```

> [!NOTE]
> <span data-ttu-id="a280b-119">Der Konstruktorparameter für den Lazy Loading-Delegaten muss als „lazyLoader“ bezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="a280b-119">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="a280b-120">Eine Konfiguration für die Verwendung eines anderen Namens ist für ein zukünftiges Release geplant.</span><span class="sxs-lookup"><span data-stu-id="a280b-120">Configuration to use a different name than this is planned for a future release.</span></span>
