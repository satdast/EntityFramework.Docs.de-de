---
title: Entitäts Typen mit Konstruktoren-EF Core
description: Verwenden von Konstruktoren zum Binden von Daten mit Entity Framework Core Modell
author: ajcvickers
ms.date: 02/23/2018
uid: core/modeling/constructors
ms.openlocfilehash: 06d18f173275599ad1e547193363e13c48fc8dcf
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071588"
---
# <a name="entity-types-with-constructors"></a><span data-ttu-id="638dd-103">Entitäts Typen mit Konstruktoren</span><span class="sxs-lookup"><span data-stu-id="638dd-103">Entity types with constructors</span></span>

> [!NOTE]  
> <span data-ttu-id="638dd-104">Dieses Feature ist neu in EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="638dd-104">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="638dd-105">Ab EF Core 2,1 ist es möglich, einen Konstruktor mit Parametern zu definieren, und EF Core diesen Konstruktor aufzurufen, wenn eine Instanz der Entität erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="638dd-105">Starting with EF Core 2.1, it is now possible to define a constructor with parameters and have EF Core call this constructor when creating an instance of the entity.</span></span> <span data-ttu-id="638dd-106">Die Konstruktorparameter können an zugeordnete Eigenschaften oder an verschiedene Arten von Diensten gebunden werden, um Verhalten wie Lazy Load zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="638dd-106">The constructor parameters can be bound to mapped properties, or to various kinds of services to facilitate behaviors like lazy-loading.</span></span>

> [!NOTE]  
> <span data-ttu-id="638dd-107">Ab EF Core 2,1 ist die gesamte konstruktorbindung gemäß der Konvention.</span><span class="sxs-lookup"><span data-stu-id="638dd-107">As of EF Core 2.1, all constructor binding is by convention.</span></span> <span data-ttu-id="638dd-108">Die Konfiguration bestimmter zu verwendende Konstruktoren ist für eine zukünftige Version geplant.</span><span class="sxs-lookup"><span data-stu-id="638dd-108">Configuration of specific constructors to use is planned for a future release.</span></span>

## <a name="binding-to-mapped-properties"></a><span data-ttu-id="638dd-109">Binden an zugeordnete Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="638dd-109">Binding to mapped properties</span></span>

<span data-ttu-id="638dd-110">Ein typisches Blog-/Post-Modell:</span><span class="sxs-lookup"><span data-stu-id="638dd-110">Consider a typical Blog/Post model:</span></span>

``` csharp
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="638dd-111">Wenn EF Core Instanzen dieser Typen erstellt, z. b. für die Ergebnisse einer Abfrage, wird zuerst der standardmäßige Parameter lose Konstruktor aufgerufen und dann jede Eigenschaft auf den Wert aus der Datenbank festgelegt.</span><span class="sxs-lookup"><span data-stu-id="638dd-111">When EF Core creates instances of these types, such as for the results of a query, it will first call the default parameterless constructor and then set each property to the value from the database.</span></span> <span data-ttu-id="638dd-112">Wenn EF Core jedoch einen parametrisierten Konstruktor mit Parameternamen und-Typen findet, die mit den zugeordneten Eigenschaften identisch sind, wird stattdessen der parametrisierte Konstruktor mit Werten für diese Eigenschaften aufgerufen, und jede Eigenschaft wird nicht explizit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="638dd-112">However, if EF Core finds a parameterized constructor with parameter names and types that match those of mapped properties, then it will instead call the parameterized constructor with values for those properties and will not set each property explicitly.</span></span> <span data-ttu-id="638dd-113">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="638dd-113">For example:</span></span>

``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="638dd-114">Hinweise, die Sie beachten sollten:</span><span class="sxs-lookup"><span data-stu-id="638dd-114">Some things to note:</span></span>

* <span data-ttu-id="638dd-115">Nicht alle Eigenschaften müssen über Konstruktorparameter verfügen.</span><span class="sxs-lookup"><span data-stu-id="638dd-115">Not all properties need to have constructor parameters.</span></span> <span data-ttu-id="638dd-116">Beispielsweise wird die Post. Content-Eigenschaft nicht von einem Konstruktorparameter festgelegt, sodass EF Core Sie nach dem Aufrufen des Konstruktors auf die normale Weise festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="638dd-116">For example, the Post.Content property is not set by any constructor parameter, so EF Core will set it after calling the constructor in the normal way.</span></span>
* <span data-ttu-id="638dd-117">Die Parametertypen und-Namen müssen mit den Eigenschafts Typen und-Namen identisch sein, mit dem Unterschied, dass die Eigenschaften in Pascal-Schreibweise angegeben werden können, während die Parameter</span><span class="sxs-lookup"><span data-stu-id="638dd-117">The parameter types and names must match property types and names, except that properties can be Pascal-cased while the parameters are camel-cased.</span></span>
* <span data-ttu-id="638dd-118">EF Core können keine Navigations Eigenschaften (z. b. Blog oder Beiträge oben) mithilfe eines Konstruktors festlegen.</span><span class="sxs-lookup"><span data-stu-id="638dd-118">EF Core cannot set navigation properties (such as Blog or Posts above) using a constructor.</span></span>
* <span data-ttu-id="638dd-119">Der Konstruktor kann öffentlich oder privat sein oder über andere Zugriffsmöglichkeiten verfügen.</span><span class="sxs-lookup"><span data-stu-id="638dd-119">The constructor can be public, private, or have any other accessibility.</span></span> <span data-ttu-id="638dd-120">Zum verzögerten Laden von Proxys ist es jedoch erforderlich, dass der Konstruktor von der vererbenden Proxy Klasse zugänglich ist.</span><span class="sxs-lookup"><span data-stu-id="638dd-120">However, lazy-loading proxies require that the constructor is accessible from the inheriting proxy class.</span></span> <span data-ttu-id="638dd-121">Dies bedeutet in der Regel, dass Sie entweder öffentlich oder geschützt ist.</span><span class="sxs-lookup"><span data-stu-id="638dd-121">Usually this means making it either public or protected.</span></span>

### <a name="read-only-properties"></a><span data-ttu-id="638dd-122">Schreibgeschützte Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="638dd-122">Read-only properties</span></span>

<span data-ttu-id="638dd-123">Nachdem Eigenschaften über den Konstruktor festgelegt wurden, kann es sinnvoll sein, einige davon als schreibgeschützt zu deklarimachen.</span><span class="sxs-lookup"><span data-stu-id="638dd-123">Once properties are being set via the constructor it can make sense to make some of them read-only.</span></span> <span data-ttu-id="638dd-124">EF Core unterstützt dies, aber es gibt einige Dinge, die Sie nachschlagen sollten:</span><span class="sxs-lookup"><span data-stu-id="638dd-124">EF Core supports this, but there are some things to look out for:</span></span>

* <span data-ttu-id="638dd-125">Eigenschaften ohne Setter werden nicht anhand der Konvention zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="638dd-125">Properties without setters are not mapped by convention.</span></span> <span data-ttu-id="638dd-126">(Dies bewirkt tendenziell, dass Eigenschaften zugeordnet werden, die nicht zugeordnet werden sollten, z. b. berechnete Eigenschaften.)</span><span class="sxs-lookup"><span data-stu-id="638dd-126">(Doing so tends to map properties that should not be mapped, such as computed properties.)</span></span>
* <span data-ttu-id="638dd-127">Die Verwendung automatisch generierter Schlüsselwerte erfordert eine Schlüsseleigenschaft mit Lese-/Schreibzugriff, da der Schlüsselwert beim Einfügen neuer Entitäten vom Schlüssel Generator festgelegt werden muss.</span><span class="sxs-lookup"><span data-stu-id="638dd-127">Using automatically generated key values requires a key property that is read-write, since the key value needs to be set by the key generator when inserting new entities.</span></span>

<span data-ttu-id="638dd-128">Eine einfache Möglichkeit, diese Dinge zu vermeiden, ist die Verwendung privater Setter.</span><span class="sxs-lookup"><span data-stu-id="638dd-128">An easy way to avoid these things is to use private setters.</span></span> <span data-ttu-id="638dd-129">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="638dd-129">For example:</span></span>

``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; private set; }

    public string Name { get; private set; }
    public string Author { get; private set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; private set; }

    public string Title { get; private set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; private set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="638dd-130">EF Core eine Eigenschaft mit einem privaten Setter als Lese-/Schreibzugriff sehen, bedeutet dies, dass alle Eigenschaften wie zuvor zugeordnet sind und der Schlüssel weiterhin vom Speicher generiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="638dd-130">EF Core sees a property with a private setter as read-write, which means that all properties are mapped as before and the key can still be store-generated.</span></span>

<span data-ttu-id="638dd-131">Eine Alternative zur Verwendung von privaten Setter besteht darin, dass Eigenschaften wirklich schreibgeschützt sind und in onmodelcreating eine explizite Zuordnung hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="638dd-131">An alternative to using private setters is to make properties really read-only and add more explicit mapping in OnModelCreating.</span></span> <span data-ttu-id="638dd-132">Ebenso können einige Eigenschaften vollständig entfernt und durch nur Felder ersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="638dd-132">Likewise, some properties can be removed completely and replaced with only fields.</span></span> <span data-ttu-id="638dd-133">Beachten Sie z. b. die folgenden Entitäts Typen:</span><span class="sxs-lookup"><span data-stu-id="638dd-133">For example, consider these entity types:</span></span>

``` csharp
public class Blog
{
    private int _id;

    public Blog(string name, string author)
    {
        Name = name;
        Author = author;
    }

    public string Name { get; }
    public string Author { get; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    private int _id;

    public Post(string title, DateTime postedOn)
    {
        Title = title;
        PostedOn = postedOn;
    }

    public string Title { get; }
    public string Content { get; set; }
    public DateTime PostedOn { get; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="638dd-134">Und diese Konfiguration in onmodelcreating:</span><span class="sxs-lookup"><span data-stu-id="638dd-134">And this configuration in OnModelCreating:</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Author);
            b.Property(e => e.Name);
        });

    modelBuilder.Entity<Post>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Title);
            b.Property(e => e.PostedOn);
        });
}
```

<span data-ttu-id="638dd-135">Hinweise:</span><span class="sxs-lookup"><span data-stu-id="638dd-135">Things to note:</span></span>

* <span data-ttu-id="638dd-136">Der Schlüssel "Property" ist jetzt ein Feld.</span><span class="sxs-lookup"><span data-stu-id="638dd-136">The key "property" is now a field.</span></span> <span data-ttu-id="638dd-137">Es handelt sich hierbei nicht `readonly` um ein Feld, sodass vom Speicher generierte Schlüssel verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="638dd-137">It is not a `readonly` field so that store-generated keys can be used.</span></span>
* <span data-ttu-id="638dd-138">Die anderen Eigenschaften sind schreibgeschützte Eigenschaften, die nur im Konstruktor festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="638dd-138">The other properties are read-only properties set only in the constructor.</span></span>
* <span data-ttu-id="638dd-139">Wenn der Primärschlüssel Wert nur von EF festgelegt oder aus der Datenbank gelesen wird, muss er nicht in den Konstruktor eingeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="638dd-139">If the primary key value is only ever set by EF or read from the database, then there is no need to include it in the constructor.</span></span> <span data-ttu-id="638dd-140">Dadurch bleibt der Schlüssel "Property" als einfaches Feld, und es wird klar, dass er beim Erstellen neuer Blogs oder Beiträge nicht explizit festgelegt werden sollte.</span><span class="sxs-lookup"><span data-stu-id="638dd-140">This leaves the key "property" as a simple field and makes it clear that it should not be set explicitly when creating new blogs or posts.</span></span>

> [!NOTE]  
> <span data-ttu-id="638dd-141">Dieser Code führt zu einer Compilerwarnung "169", die angibt, dass das Feld niemals verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="638dd-141">This code will result in compiler warning '169' indicating that the field is never used.</span></span> <span data-ttu-id="638dd-142">Dies kann ignoriert werden, da EF Core das Feld in der Praxis auf extralweise verwendet.</span><span class="sxs-lookup"><span data-stu-id="638dd-142">This can be ignored since in reality EF Core is using the field in an extralinguistic manner.</span></span>

## <a name="injecting-services"></a><span data-ttu-id="638dd-143">Einschleusen von Diensten</span><span class="sxs-lookup"><span data-stu-id="638dd-143">Injecting services</span></span>

<span data-ttu-id="638dd-144">EF Core können auch "Dienste" in den Konstruktor eines Entitäts Typs einfügen.</span><span class="sxs-lookup"><span data-stu-id="638dd-144">EF Core can also inject "services" into an entity type's constructor.</span></span> <span data-ttu-id="638dd-145">Beispielsweise kann Folgendes eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="638dd-145">For example, the following can be injected:</span></span>

* <span data-ttu-id="638dd-146">`DbContext` -die aktuelle Kontext Instanz, die auch als abgeleiteter dbcontext-Typ typisiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="638dd-146">`DbContext` - the current context instance, which can also be typed as your derived DbContext type</span></span>
* <span data-ttu-id="638dd-147">`ILazyLoader` -der Dienst für verzögertes Laden: Weitere Informationen finden Sie in der Dokumentation zum verzögerten [Laden](xref:core/querying/related-data) .</span><span class="sxs-lookup"><span data-stu-id="638dd-147">`ILazyLoader` - the lazy-loading service--see the [lazy-loading documentation](xref:core/querying/related-data) for more details</span></span>
* <span data-ttu-id="638dd-148">`Action<object, string>` -einen Delegaten verzögert laden: Weitere Informationen finden Sie in der [Dokumentation Lazy Load](xref:core/querying/related-data) .</span><span class="sxs-lookup"><span data-stu-id="638dd-148">`Action<object, string>` - a lazy-loading delegate--see the [lazy-loading documentation](xref:core/querying/related-data) for more details</span></span>
* <span data-ttu-id="638dd-149">`IEntityType` -Die EF Core Metadaten, die diesem Entitätstyp zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="638dd-149">`IEntityType` - the EF Core metadata associated with this entity type</span></span>

> [!NOTE]  
> <span data-ttu-id="638dd-150">Ab EF Core 2,1 können nur von EF Core bekannte Dienste eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="638dd-150">As of EF Core 2.1, only services known by EF Core can be injected.</span></span> <span data-ttu-id="638dd-151">Die Unterstützung für das Einfügen von Anwendungsdiensten wird für eine zukünftige Version berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="638dd-151">Support for injecting application services is being considered for a future release.</span></span>

<span data-ttu-id="638dd-152">Beispielsweise kann ein eingefügter dbcontext zum selektiven zugreifen auf die Datenbank verwendet werden, um Informationen zu verknüpften Entitäten abzurufen, ohne Sie zu laden.</span><span class="sxs-lookup"><span data-stu-id="638dd-152">For example, an injected DbContext can be used to selectively access the database to obtain information about related entities without loading them all.</span></span> <span data-ttu-id="638dd-153">Im folgenden Beispiel wird dies verwendet, um die Anzahl von Beiträgen in einem Blog abzurufen, ohne die Beiträge zu laden:</span><span class="sxs-lookup"><span data-stu-id="638dd-153">In the example below this is used to obtain the number of posts in a blog without loading the posts:</span></span>

``` csharp
public class Blog
{
    public Blog()
    {
    }

    private Blog(BloggingContext context)
    {
        Context = context;
    }

    private BloggingContext Context { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; set; }

    public int PostsCount
        => Posts?.Count
           ?? Context?.Set<Post>().Count(p => Id == EF.Property<int?>(p, "BlogId"))
           ?? 0;
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="638dd-154">Hier sind einige Punkte zu beachten:</span><span class="sxs-lookup"><span data-stu-id="638dd-154">A few things to notice about this:</span></span>

* <span data-ttu-id="638dd-155">Der Konstruktor ist privat, da er nur von EF Core aufgerufen wird und es einen anderen öffentlichen Konstruktor für die allgemeine Verwendung gibt.</span><span class="sxs-lookup"><span data-stu-id="638dd-155">The constructor is private, since it is only ever called by EF Core, and there is another public constructor for general use.</span></span>
* <span data-ttu-id="638dd-156">Der Code, der den injizierten Dienst (d. h. den Kontext) verwendet, ist für die Behandlung `null` von Fällen zuständig, in denen EF Core die Instanz nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="638dd-156">The code using the injected service (that is, the context) is defensive against it being `null` to handle cases where EF Core is not creating the instance.</span></span>
* <span data-ttu-id="638dd-157">Da der Dienst in einer Lese-/Schreibeigenschaft gespeichert wird, wird er zurückgesetzt, wenn die Entität an eine neue Kontext Instanz angefügt wird.</span><span class="sxs-lookup"><span data-stu-id="638dd-157">Because service is stored in a read/write property it will be reset when the entity is attached to a new context instance.</span></span>

> [!WARNING]  
> <span data-ttu-id="638dd-158">Das Einfügen von dbcontext wie diesem wird häufig als Antimuster betrachtet, da die Entitäts Typen direkt an EF Core gekoppelt werden.</span><span class="sxs-lookup"><span data-stu-id="638dd-158">Injecting the DbContext like this is often considered an anti-pattern since it couples your entity types directly to EF Core.</span></span> <span data-ttu-id="638dd-159">Berücksichtigen Sie alle Optionen sorgfältig, bevor Sie Service Injection wie folgt verwenden.</span><span class="sxs-lookup"><span data-stu-id="638dd-159">Carefully consider all options before using service injection like this.</span></span>
