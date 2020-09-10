---
title: Testen mit ihren eigenen Test Doubles-EF6
description: Testen mit ihren eigenen Test Doubles in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 16a8b7c0-2d23-47f4-9cc0-e2eb2e738ca3
uid: ef6/fundamentals/testing/writing-test-doubles
ms.openlocfilehash: 5c8e50247c0e6ed4081e2403ba60e0738051312b
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618233"
---
# <a name="testing-with-your-own-test-doubles"></a><span data-ttu-id="28d66-103">Testen mit ihren eigenen Test Doubles</span><span class="sxs-lookup"><span data-stu-id="28d66-103">Testing with your own test doubles</span></span>
> [!NOTE]
> <span data-ttu-id="28d66-104">**Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="28d66-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="28d66-105">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="28d66-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="28d66-106">Beim Schreiben von Tests für Ihre Anwendung ist es häufig wünschenswert, das Erreichen der Datenbank zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="28d66-106">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="28d66-107">Entity Framework ermöglicht es Ihnen, dies zu erreichen, indem Sie einen Kontext – mit von den Tests definiertem Verhalten – erstellen, der Daten im Arbeitsspeicher nutzt.</span><span class="sxs-lookup"><span data-stu-id="28d66-107">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="28d66-108">Optionen zum Erstellen von Test Doubles</span><span class="sxs-lookup"><span data-stu-id="28d66-108">Options for creating test doubles</span></span>  

<span data-ttu-id="28d66-109">Es gibt zwei verschiedene Ansätze, die verwendet werden können, um eine in-Memory-Version Ihres Kontexts zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="28d66-109">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="28d66-110">**Erstellen Sie eigene Test Doubles** – diese Vorgehensweise umfasst das Schreiben einer eigenen in-Memory-Implementierung Ihres Kontexts und von dbsets.</span><span class="sxs-lookup"><span data-stu-id="28d66-110">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="28d66-111">Dadurch haben Sie viele Kontrolle darüber, wie sich die Klassen Verhalten, aber Sie können das Schreiben und das Besitz einer angemessenen Menge an Code einschließen.</span><span class="sxs-lookup"><span data-stu-id="28d66-111">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="28d66-112">**Verwenden eines kontextbasierten Frameworks zum Erstellen von Test Doubles** – mithilfe eines kontextbasierten Frameworks (wie z. b. von muq) können Sie die in-Memory-Implementierungen Ihres Kontexts und Sätze dynamisch zur Laufzeit erstellen.</span><span class="sxs-lookup"><span data-stu-id="28d66-112">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of you context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="28d66-113">In diesem Artikel wird das Erstellen eines eigenen Test Double behandelt.</span><span class="sxs-lookup"><span data-stu-id="28d66-113">This article will deal with creating your own test double.</span></span> <span data-ttu-id="28d66-114">Weitere Informationen zur Verwendung eines-Frameworks finden Sie unter [Testen mit einem](xref:ef6/fundamentals/testing/mocking)-Frameworks.</span><span class="sxs-lookup"><span data-stu-id="28d66-114">For information on using a mocking framework see [Testing with a Mocking Framework](xref:ef6/fundamentals/testing/mocking).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="28d66-115">Testen mit Pre-EF6-Versionen</span><span class="sxs-lookup"><span data-stu-id="28d66-115">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="28d66-116">Der in diesem Artikel gezeigte Code ist kompatibel mit EF6.</span><span class="sxs-lookup"><span data-stu-id="28d66-116">The code shown in this article is compatible with EF6.</span></span> <span data-ttu-id="28d66-117">Informationen zu Tests mit EF5 und einer früheren Version finden Sie [untertests mit einem gefälschten Kontext](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span><span class="sxs-lookup"><span data-stu-id="28d66-117">For testing with EF5 and earlier version see [Testing with a Fake Context](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="28d66-118">Einschränkungen von EF-in-Memory-Test Doubles</span><span class="sxs-lookup"><span data-stu-id="28d66-118">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="28d66-119">In-Memory-Test Doubles können eine gute Möglichkeit zum Bereitstellen von Komponenten Testebene für Bits Ihrer Anwendung sein, die EF verwenden.</span><span class="sxs-lookup"><span data-stu-id="28d66-119">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="28d66-120">Dabei verwenden Sie jedoch LINQ to Objects, um Abfragen für in-Memory-Daten auszuführen.</span><span class="sxs-lookup"><span data-stu-id="28d66-120">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="28d66-121">Dies kann zu einem anderen Verhalten führen als die Verwendung des LINQ-Anbieters (LINQ to Entities) von EF, um Abfragen in SQL zu übersetzen, die für die Datenbank ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="28d66-121">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="28d66-122">Ein Beispiel für einen solchen Unterschied besteht darin, verknüpfte Daten zu laden.</span><span class="sxs-lookup"><span data-stu-id="28d66-122">One example of such a difference is loading related data.</span></span> <span data-ttu-id="28d66-123">Wenn Sie eine Reihe von Blogs erstellen, die jeweils über verwandte Beiträge verfügen, werden bei der Verwendung von in-Memory-Daten die zugehörigen Beiträge immer für jeden Blog geladen.</span><span class="sxs-lookup"><span data-stu-id="28d66-123">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="28d66-124">Wenn Sie jedoch für eine Datenbank ausführen, werden die Daten nur geladen, wenn Sie die Include-Methode verwenden.</span><span class="sxs-lookup"><span data-stu-id="28d66-124">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="28d66-125">Aus diesem Grund empfiehlt es sich, immer einen gewissen Grad an End-to-End-Tests (zusätzlich zu den Komponententests) einzubeziehen, um sicherzustellen, dass Ihre Anwendung für eine Datenbank ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="28d66-125">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="28d66-126">Im Anschluss an diesen Artikel</span><span class="sxs-lookup"><span data-stu-id="28d66-126">Following along with this article</span></span>  

<span data-ttu-id="28d66-127">Dieser Artikel enthält umfassende Code Auflistungen, die Sie in Visual Studio kopieren können, wenn Sie möchten.</span><span class="sxs-lookup"><span data-stu-id="28d66-127">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="28d66-128">Es ist am einfachsten, ein Komponenten **Test Projekt** zu erstellen, und Sie müssen **.NET Framework 4,5** als Ziel verwenden, um die Abschnitte mit "Async" zu vervollständigen.</span><span class="sxs-lookup"><span data-stu-id="28d66-128">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="creating-a-context-interface"></a><span data-ttu-id="28d66-129">Erstellen einer Kontext Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="28d66-129">Creating a context interface</span></span>  

<span data-ttu-id="28d66-130">Wir sehen uns nun an, wie wir einen Dienst testen, der ein EF-Modell verwendet.</span><span class="sxs-lookup"><span data-stu-id="28d66-130">We're going to look at testing a service that makes use of an EF model.</span></span> <span data-ttu-id="28d66-131">Um unseren EF-Kontext durch eine in-Memory-Version für Tests ersetzen zu können, definieren wir eine Schnittstelle, die der EF-Kontext (und der in-Memory-Double) implementiert.</span><span class="sxs-lookup"><span data-stu-id="28d66-131">In order to be able to replace our EF context with an in-memory version for testing, we'll define an interface that our EF context (and it's in-memory double) will implement.</span></span>

<span data-ttu-id="28d66-132">Der Dienst, den wir testen werden, fragt und ändert Daten mithilfe der dbset-Eigenschaften unseres Kontexts und ruft auch SaveChanges auf, um Änderungen an die Datenbank zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="28d66-132">The service we are going to test will query and modify data using the DbSet properties of our context and also call SaveChanges to push changes to the database.</span></span> <span data-ttu-id="28d66-133">Daher fügen wir diese Member in die Schnittstelle ein.</span><span class="sxs-lookup"><span data-stu-id="28d66-133">So we're including these members on the interface.</span></span>  

``` csharp
using System.Data.Entity;

namespace TestingDemo
{
    public interface IBloggingContext
    {
        DbSet<Blog> Blogs { get; }
        DbSet<Post> Posts { get; }
        int SaveChanges();
    }
}
```  

## <a name="the-ef-model"></a><span data-ttu-id="28d66-134">Das EF-Modell</span><span class="sxs-lookup"><span data-stu-id="28d66-134">The EF model</span></span>  

<span data-ttu-id="28d66-135">Der zu testende Dienst nutzt ein EF-Modell, das aus dem bloggingcontext und den Blog-und Post-Klassen besteht.</span><span class="sxs-lookup"><span data-stu-id="28d66-135">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="28d66-136">Dieser Code wurde möglicherweise vom EF-Designer generiert oder ist ein Code First Modell.</span><span class="sxs-lookup"><span data-stu-id="28d66-136">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;

namespace TestingDemo
{
    public class BloggingContext : DbContext, IBloggingContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }
        public string Url { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
}
```  

### <a name="implementing-the-context-interface-with-the-ef-designer"></a><span data-ttu-id="28d66-137">Implementieren der Kontext Schnittstelle mit dem EF-Designer</span><span class="sxs-lookup"><span data-stu-id="28d66-137">Implementing the context interface with the EF Designer</span></span>  

<span data-ttu-id="28d66-138">Beachten Sie, dass unser Kontext die ibloggingcontext-Schnittstelle implementiert.</span><span class="sxs-lookup"><span data-stu-id="28d66-138">Note that our context implements the IBloggingContext interface.</span></span>  

<span data-ttu-id="28d66-139">Wenn Sie Code First verwenden, können Sie den Kontext direkt bearbeiten, um die-Schnittstelle zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="28d66-139">If you are using Code First then you can edit your context directly to implement the interface.</span></span> <span data-ttu-id="28d66-140">Wenn Sie den EF-Designer verwenden, müssen Sie die T4-Vorlage bearbeiten, mit der ihr Kontext generiert wird.</span><span class="sxs-lookup"><span data-stu-id="28d66-140">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="28d66-141">Öffnen Sie die \<model_name\> . Context.tt-Datei, die unter der EDMX-Datei gespeichert ist, das folgende Code Fragment suchen und wie gezeigt in der-Schnittstelle hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="28d66-141">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the interface as shown.</span></span>  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
```  

## <a name="service-to-be-tested"></a><span data-ttu-id="28d66-142">Zu testender Dienst</span><span class="sxs-lookup"><span data-stu-id="28d66-142">Service to be tested</span></span>  

<span data-ttu-id="28d66-143">Um Tests mit in-Memory-Test Doubles zu veranschaulichen, schreiben wir einige Tests für einen Blogservice.</span><span class="sxs-lookup"><span data-stu-id="28d66-143">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="28d66-144">Der Dienst ist in der Lage, neue Blogs (addblog) zu erstellen und alle Blogs nach Namen (getallblogs) nach Namen zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="28d66-144">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="28d66-145">Zusätzlich zu getallblogs haben wir auch eine Methode bereitgestellt, die asynchron alle Blogs geordnet nach Namen (getallblogsasync) erhält.</span><span class="sxs-lookup"><span data-stu-id="28d66-145">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class BlogService
    {
        private IBloggingContext _context;

        public BlogService(IBloggingContext context)
        {
            _context = context;
        }

        public Blog AddBlog(string name, string url)
        {
            var blog = new Blog { Name = name, Url = url };
            _context.Blogs.Add(blog);
            _context.SaveChanges();

            return blog;
        }

        public List<Blog> GetAllBlogs()
        {
            var query = from b in _context.Blogs
                        orderby b.Name
                        select b;

            return query.ToList();
        }

        public async Task<List<Blog>> GetAllBlogsAsync()
        {
            var query = from b in _context.Blogs
                        orderby b.Name
                        select b;

            return await query.ToListAsync();
        }
    }
}
```

## <a name="creating-the-in-memory-test-doubles"></a><span data-ttu-id="28d66-146">Erstellen der Test Double-in-Memory-Tests</span><span class="sxs-lookup"><span data-stu-id="28d66-146">Creating the in-memory test doubles</span></span>  

<span data-ttu-id="28d66-147">Nachdem wir nun über das echte EF-Modell und den Dienst verfügen, die es verwenden können, ist es an der Zeit, den in-Memory-Test Double zu erstellen, den wir für Tests verwenden können.</span><span class="sxs-lookup"><span data-stu-id="28d66-147">Now that we have the real EF model and the service that can use it, it's time to create the in-memory test double that we can use for testing.</span></span> <span data-ttu-id="28d66-148">Wir haben einen TestContext-Test Double für den Kontext erstellt.</span><span class="sxs-lookup"><span data-stu-id="28d66-148">We've created a TestContext test double for our context.</span></span> <span data-ttu-id="28d66-149">In Test Doubles wählen wir das gewünschte Verhalten aus, um die Tests zu unterstützen, die wir ausführen möchten.</span><span class="sxs-lookup"><span data-stu-id="28d66-149">In test doubles we get to choose the behavior we want in order to support the tests we are going to run.</span></span> <span data-ttu-id="28d66-150">In diesem Beispiel erfassen wir einfach, wie oft SaveChanges aufgerufen wird, aber Sie können die erforderliche Logik zum Überprüfen des Szenarios einschließen, das Sie testen.</span><span class="sxs-lookup"><span data-stu-id="28d66-150">In this example we're just capturing the number of times SaveChanges is called, but you can include whatever logic is needed to verify the scenario you are testing.</span></span>  

<span data-ttu-id="28d66-151">Wir haben auch ein testdbset erstellt, das eine in-Memory-Implementierung von dbset bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="28d66-151">We've also created a TestDbSet that provides an in-memory implementation of DbSet.</span></span> <span data-ttu-id="28d66-152">Wir haben eine vollständige Implementierung für alle Methoden in dbset (mit Ausnahme von Find) bereitgestellt, Sie müssen jedoch nur die Member implementieren, die in Ihrem Testszenario verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="28d66-152">We've provided a complete implemention for all the methods on DbSet (except for Find), but you only need to implement the members that your test scenario will use.</span></span>  

<span data-ttu-id="28d66-153">Testdbset nutzt einige andere Infrastruktur Klassen, die wir enthalten haben, um sicherzustellen, dass asynchrone Abfragen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="28d66-153">TestDbSet makes use of some other infrastructure classes that we've included to ensure that async queries can be processed.</span></span>  

``` csharp
using System;
using System.Collections.Generic;
using System.Collections.ObjectModel;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Linq.Expressions;
using System.Threading;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class TestContext : IBloggingContext
    {
        public TestContext()
        {
            this.Blogs = new TestDbSet<Blog>();
            this.Posts = new TestDbSet<Post>();
        }

        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
        public int SaveChangesCount { get; private set; }
        public int SaveChanges()
        {
            this.SaveChangesCount++;
            return 1;
        }
    }

    public class TestDbSet<TEntity> : DbSet<TEntity>, IQueryable, IEnumerable<TEntity>, IDbAsyncEnumerable<TEntity>
        where TEntity : class
    {
        ObservableCollection<TEntity> _data;
        IQueryable _query;

        public TestDbSet()
        {
            _data = new ObservableCollection<TEntity>();
            _query = _data.AsQueryable();
        }

        public override TEntity Add(TEntity item)
        {
            _data.Add(item);
            return item;
        }

        public override TEntity Remove(TEntity item)
        {
            _data.Remove(item);
            return item;
        }

        public override TEntity Attach(TEntity item)
        {
            _data.Add(item);
            return item;
        }

        public override TEntity Create()
        {
            return Activator.CreateInstance<TEntity>();
        }

        public override TDerivedEntity Create<TDerivedEntity>()
        {
            return Activator.CreateInstance<TDerivedEntity>();
        }

        public override ObservableCollection<TEntity> Local
        {
            get { return _data; }
        }

        Type IQueryable.ElementType
        {
            get { return _query.ElementType; }
        }

        Expression IQueryable.Expression
        {
            get { return _query.Expression; }
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new TestDbAsyncQueryProvider<TEntity>(_query.Provider); }
        }

        System.Collections.IEnumerator System.Collections.IEnumerable.GetEnumerator()
        {
            return _data.GetEnumerator();
        }

        IEnumerator<TEntity> IEnumerable<TEntity>.GetEnumerator()
        {
            return _data.GetEnumerator();
        }

        IDbAsyncEnumerator<TEntity> IDbAsyncEnumerable<TEntity>.GetAsyncEnumerator()
        {
            return new TestDbAsyncEnumerator<TEntity>(_data.GetEnumerator());
        }
    }

    internal class TestDbAsyncQueryProvider<TEntity> : IDbAsyncQueryProvider
    {
        private readonly IQueryProvider _inner;

        internal TestDbAsyncQueryProvider(IQueryProvider inner)
        {
            _inner = inner;
        }

        public IQueryable CreateQuery(Expression expression)
        {
            return new TestDbAsyncEnumerable<TEntity>(expression);
        }

        public IQueryable<TElement> CreateQuery<TElement>(Expression expression)
        {
            return new TestDbAsyncEnumerable<TElement>(expression);
        }

        public object Execute(Expression expression)
        {
            return _inner.Execute(expression);
        }

        public TResult Execute<TResult>(Expression expression)
        {
            return _inner.Execute<TResult>(expression);
        }

        public Task<object> ExecuteAsync(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute(expression));
        }

        public Task<TResult> ExecuteAsync<TResult>(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute<TResult>(expression));
        }
    }

    internal class TestDbAsyncEnumerable<T> : EnumerableQuery<T>, IDbAsyncEnumerable<T>, IQueryable<T>
    {
        public TestDbAsyncEnumerable(IEnumerable<T> enumerable)
            : base(enumerable)
        { }

        public TestDbAsyncEnumerable(Expression expression)
            : base(expression)
        { }

        public IDbAsyncEnumerator<T> GetAsyncEnumerator()
        {
            return new TestDbAsyncEnumerator<T>(this.AsEnumerable().GetEnumerator());
        }

        IDbAsyncEnumerator IDbAsyncEnumerable.GetAsyncEnumerator()
        {
            return GetAsyncEnumerator();
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new TestDbAsyncQueryProvider<T>(this); }
        }
    }

    internal class TestDbAsyncEnumerator<T> : IDbAsyncEnumerator<T>
    {
        private readonly IEnumerator<T> _inner;

        public TestDbAsyncEnumerator(IEnumerator<T> inner)
        {
            _inner = inner;
        }

        public void Dispose()
        {
            _inner.Dispose();
        }

        public Task<bool> MoveNextAsync(CancellationToken cancellationToken)
        {
            return Task.FromResult(_inner.MoveNext());
        }

        public T Current
        {
            get { return _inner.Current; }
        }

        object IDbAsyncEnumerator.Current
        {
            get { return Current; }
        }
    }
}
```  

### <a name="implementing-find"></a><span data-ttu-id="28d66-154">Implementieren der Suche</span><span class="sxs-lookup"><span data-stu-id="28d66-154">Implementing Find</span></span>  

<span data-ttu-id="28d66-155">Die Find-Methode ist in einer generischen Weise schwierig zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="28d66-155">The Find method is difficult to implement in a generic fashion.</span></span> <span data-ttu-id="28d66-156">Wenn Sie Code testen müssen, der die Find-Methode verwendet, ist es am einfachsten, ein Test-dbset für jeden Entitätstyp zu erstellen, der die Suche unterstützen muss.</span><span class="sxs-lookup"><span data-stu-id="28d66-156">If you need to test code that makes use of the Find method it is easiest to create a test DbSet for each of the entity types that need to support find.</span></span> <span data-ttu-id="28d66-157">Anschließend können Sie die Logik zum Auffinden dieses bestimmten Entitäts Typs schreiben, wie unten gezeigt.</span><span class="sxs-lookup"><span data-stu-id="28d66-157">You can then write logic to find that particular type of entity, as shown below.</span></span>  

``` csharp
using System.Linq;

namespace TestingDemo
{
    class TestBlogDbSet : TestDbSet<Blog>
    {
        public override Blog Find(params object[] keyValues)
        {
            var id = (int)keyValues.Single();
            return this.SingleOrDefault(b => b.BlogId == id);
        }
    }
}
```  

## <a name="writing-some-tests"></a><span data-ttu-id="28d66-158">Schreiben von Tests</span><span class="sxs-lookup"><span data-stu-id="28d66-158">Writing some tests</span></span>  

<span data-ttu-id="28d66-159">Das ist alles, was wir tun müssen, um den Test zu starten.</span><span class="sxs-lookup"><span data-stu-id="28d66-159">That’s all we need to do to start testing.</span></span> <span data-ttu-id="28d66-160">Der folgende Test erstellt ein TestContext und dann einen Dienst, der auf diesem Kontext basiert.</span><span class="sxs-lookup"><span data-stu-id="28d66-160">The following test creates a TestContext and then a service based on this context.</span></span> <span data-ttu-id="28d66-161">Der Dienst wird dann verwendet, um mithilfe der addblog-Methode einen neuen Blog – zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="28d66-161">The service is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="28d66-162">Schließlich überprüft der Test, ob der Dienst einen neuen Blog zur Blogs-Eigenschaft des Kontexts hinzugefügt hat und "SaveChanges" im Kontext heißt.</span><span class="sxs-lookup"><span data-stu-id="28d66-162">Finally, the test verifies that the service added a new Blog to the context's Blogs property and called SaveChanges on the context.</span></span>  

<span data-ttu-id="28d66-163">Dies ist nur ein Beispiel für die Art der Dinge, die Sie mit einem in-Memory-Test Double testen können, und Sie können die Logik der Test Doubles und die Überprüfung an Ihre Anforderungen anpassen.</span><span class="sxs-lookup"><span data-stu-id="28d66-163">This is just an example of the types of things you can test with an in-memory test double and you can adjust the logic of the test doubles and the verification to meet your requirements.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Linq;

namespace TestingDemo
{
    [TestClass]
    public class NonQueryTests
    {
        [TestMethod]
        public void CreateBlog_saves_a_blog_via_context()
        {
            var context = new TestContext();

            var service = new BlogService(context);
            service.AddBlog("ADO.NET Blog", "http://blogs.msdn.com/adonet");

            Assert.AreEqual(1, context.Blogs.Count());
            Assert.AreEqual("ADO.NET Blog", context.Blogs.Single().Name);
            Assert.AreEqual("http://blogs.msdn.com/adonet", context.Blogs.Single().Url);
            Assert.AreEqual(1, context.SaveChangesCount);
        }
    }
}
```  

<span data-ttu-id="28d66-164">Im folgenden finden Sie ein weiteres Beispiel für einen Test. dieser Zeitpunkt führt eine Abfrage aus.</span><span class="sxs-lookup"><span data-stu-id="28d66-164">Here is another example of a test - this time one that performs a query.</span></span> <span data-ttu-id="28d66-165">Der Test beginnt mit dem Erstellen eines Test Kontexts mit einigen Daten in seiner Blog-Eigenschaft. Beachten Sie, dass die Daten nicht in alphabetischer Reihenfolge vorliegen.</span><span class="sxs-lookup"><span data-stu-id="28d66-165">The test starts by creating a test context with some data in its Blog property - note that the data is not in alphabetical order.</span></span> <span data-ttu-id="28d66-166">Wir können dann basierend auf dem Test Kontext einen BlogService erstellen und sicherstellen, dass die Daten, die wir von getallblogs erhalten, nach dem Namen geordnet sind.</span><span class="sxs-lookup"><span data-stu-id="28d66-166">We can then create a BlogService based on our test context and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace TestingDemo
{
    [TestClass]
    public class QueryTests
    {
        [TestMethod]
        public void GetAllBlogs_orders_by_name()
        {
            var context = new TestContext();
            context.Blogs.Add(new Blog { Name = "BBB" });
            context.Blogs.Add(new Blog { Name = "ZZZ" });
            context.Blogs.Add(new Blog { Name = "AAA" });

            var service = new BlogService(context);
            var blogs = service.GetAllBlogs();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

<span data-ttu-id="28d66-167">Schließlich schreiben wir einen weiteren Test, der unsere Async-Methode verwendet, um sicherzustellen, dass die in [testdbset](#creating-the-in-memory-test-doubles) enthaltene Async-Infrastruktur funktioniert.</span><span class="sxs-lookup"><span data-stu-id="28d66-167">Finally, we'll write one more test that uses our async method to ensure that the async infrastructure we included in [TestDbSet](#creating-the-in-memory-test-doubles) is working.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    [TestClass]
    public class AsyncQueryTests
    {
        [TestMethod]
        public async Task GetAllBlogsAsync_orders_by_name()
        {
            var context = new TestContext();
            context.Blogs.Add(new Blog { Name = "BBB" });
            context.Blogs.Add(new Blog { Name = "ZZZ" });
            context.Blogs.Add(new Blog { Name = "AAA" });

            var service = new BlogService(context);
            var blogs = await service.GetAllBlogsAsync();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  
