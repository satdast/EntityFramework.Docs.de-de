---
title: Testen mit einem Simulations Framework EF6
description: Testen mit einem Simulations Framework in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/testing/mocking
ms.openlocfilehash: 893d9f921adc148465830dee5dbfebca4d4c8f50
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062918"
---
# <a name="testing-with-a-mocking-framework"></a><span data-ttu-id="b62f3-103">Testen mit einem Frameworks</span><span class="sxs-lookup"><span data-stu-id="b62f3-103">Testing with a mocking framework</span></span>
> [!NOTE]
> <span data-ttu-id="b62f3-104">**Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="b62f3-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="b62f3-105">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="b62f3-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="b62f3-106">Beim Schreiben von Tests für Ihre Anwendung ist es häufig wünschenswert, das Erreichen der Datenbank zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="b62f3-106">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="b62f3-107">Entity Framework ermöglicht es Ihnen, dies zu erreichen, indem Sie einen Kontext – mit von den Tests definiertem Verhalten – erstellen, der Daten im Arbeitsspeicher nutzt.</span><span class="sxs-lookup"><span data-stu-id="b62f3-107">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="b62f3-108">Optionen zum Erstellen von Test Doubles</span><span class="sxs-lookup"><span data-stu-id="b62f3-108">Options for creating test doubles</span></span>  

<span data-ttu-id="b62f3-109">Es gibt zwei verschiedene Ansätze, die verwendet werden können, um eine in-Memory-Version Ihres Kontexts zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b62f3-109">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="b62f3-110">**Erstellen Sie eigene Test Doubles** – diese Vorgehensweise umfasst das Schreiben einer eigenen in-Memory-Implementierung Ihres Kontexts und von dbsets.</span><span class="sxs-lookup"><span data-stu-id="b62f3-110">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="b62f3-111">Dadurch haben Sie viele Kontrolle darüber, wie sich die Klassen Verhalten, aber Sie können das Schreiben und das Besitz einer angemessenen Menge an Code einschließen.</span><span class="sxs-lookup"><span data-stu-id="b62f3-111">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="b62f3-112">**Verwenden eines kontextbasierten Frameworks zum Erstellen von Test Doubles** – mithilfe eines kontextbasierten Frameworks (wie z. b. von muq) können Sie die in-Memory-Implementierungen Ihres Kontexts und die festgelegt werden, die für Sie dynamisch zur Laufzeit erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="b62f3-112">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of your context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="b62f3-113">In diesem Artikel wird die Verwendung eines-Frameworks behandelt.</span><span class="sxs-lookup"><span data-stu-id="b62f3-113">This article will deal with using a mocking framework.</span></span> <span data-ttu-id="b62f3-114">Informationen zum Erstellen eigener Test Doubles finden Sie [unter Testen mit ihren eigenen Test Doubles](xref:ef6/fundamentals/testing/writing-test-doubles).</span><span class="sxs-lookup"><span data-stu-id="b62f3-114">For creating your own test doubles see [Testing with Your Own Test Doubles](xref:ef6/fundamentals/testing/writing-test-doubles).</span></span>  

<span data-ttu-id="b62f3-115">Um die Verwendung von EF mit einem-Frameworks zu veranschaulichen, verwenden wir "muq".</span><span class="sxs-lookup"><span data-stu-id="b62f3-115">To demonstrate using EF with a mocking framework we are going to use Moq.</span></span> <span data-ttu-id="b62f3-116">Die einfachste Möglichkeit zum Aufrufen von "muq" ist die Installation des " [muq"-Pakets von nuget](https://nuget.org/packages/Moq/).</span><span class="sxs-lookup"><span data-stu-id="b62f3-116">The easiest way to get Moq is to install the [Moq package from NuGet](https://nuget.org/packages/Moq/).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="b62f3-117">Testen mit Pre-EF6-Versionen</span><span class="sxs-lookup"><span data-stu-id="b62f3-117">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="b62f3-118">Das in diesem Artikel gezeigte Szenario ist abhängig von einigen Änderungen, die wir an dbset in EF6 vorgenommen haben.</span><span class="sxs-lookup"><span data-stu-id="b62f3-118">The scenario shown in this article is dependent on some changes we made to DbSet in EF6.</span></span> <span data-ttu-id="b62f3-119">Informationen zu Tests mit EF5 und einer früheren Version finden Sie [untertests mit einem gefälschten Kontext](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span><span class="sxs-lookup"><span data-stu-id="b62f3-119">For testing with EF5 and earlier version see [Testing with a Fake Context](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="b62f3-120">Einschränkungen von EF-in-Memory-Test Doubles</span><span class="sxs-lookup"><span data-stu-id="b62f3-120">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="b62f3-121">In-Memory-Test Doubles können eine gute Möglichkeit zum Bereitstellen von Komponenten Testebene für Bits Ihrer Anwendung sein, die EF verwenden.</span><span class="sxs-lookup"><span data-stu-id="b62f3-121">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="b62f3-122">Dabei verwenden Sie jedoch LINQ to Objects, um Abfragen für in-Memory-Daten auszuführen.</span><span class="sxs-lookup"><span data-stu-id="b62f3-122">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="b62f3-123">Dies kann zu einem anderen Verhalten führen als die Verwendung des LINQ-Anbieters (LINQ to Entities) von EF, um Abfragen in SQL zu übersetzen, die für die Datenbank ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b62f3-123">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="b62f3-124">Ein Beispiel für einen solchen Unterschied besteht darin, verknüpfte Daten zu laden.</span><span class="sxs-lookup"><span data-stu-id="b62f3-124">One example of such a difference is loading related data.</span></span> <span data-ttu-id="b62f3-125">Wenn Sie eine Reihe von Blogs erstellen, die jeweils über verwandte Beiträge verfügen, werden bei der Verwendung von in-Memory-Daten die zugehörigen Beiträge immer für jeden Blog geladen.</span><span class="sxs-lookup"><span data-stu-id="b62f3-125">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="b62f3-126">Wenn Sie jedoch für eine Datenbank ausführen, werden die Daten nur geladen, wenn Sie die Include-Methode verwenden.</span><span class="sxs-lookup"><span data-stu-id="b62f3-126">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="b62f3-127">Aus diesem Grund empfiehlt es sich, immer einen gewissen Grad an End-to-End-Tests (zusätzlich zu den Komponententests) einzubeziehen, um sicherzustellen, dass Ihre Anwendung für eine Datenbank ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="b62f3-127">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="b62f3-128">Im Anschluss an diesen Artikel</span><span class="sxs-lookup"><span data-stu-id="b62f3-128">Following along with this article</span></span>  

<span data-ttu-id="b62f3-129">Dieser Artikel enthält umfassende Code Auflistungen, die Sie in Visual Studio kopieren können, wenn Sie möchten.</span><span class="sxs-lookup"><span data-stu-id="b62f3-129">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="b62f3-130">Es ist am einfachsten, ein Komponenten **Test Projekt** zu erstellen, und Sie müssen **.NET Framework 4,5** als Ziel verwenden, um die Abschnitte mit "Async" zu vervollständigen.</span><span class="sxs-lookup"><span data-stu-id="b62f3-130">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="the-ef-model"></a><span data-ttu-id="b62f3-131">Das EF-Modell</span><span class="sxs-lookup"><span data-stu-id="b62f3-131">The EF model</span></span>  

<span data-ttu-id="b62f3-132">Der zu testende Dienst nutzt ein EF-Modell, das aus dem bloggingcontext und den Blog-und Post-Klassen besteht.</span><span class="sxs-lookup"><span data-stu-id="b62f3-132">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="b62f3-133">Dieser Code wurde möglicherweise vom EF-Designer generiert oder ist ein Code First Modell.</span><span class="sxs-lookup"><span data-stu-id="b62f3-133">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;

namespace TestingDemo
{
    public class BloggingContext : DbContext
    {
        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }
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

### <a name="virtual-dbset-properties-with-ef-designer"></a><span data-ttu-id="b62f3-134">Eigenschaften von virtuellen dbsets mit dem EF-Designer</span><span class="sxs-lookup"><span data-stu-id="b62f3-134">Virtual DbSet properties with EF Designer</span></span>  

<span data-ttu-id="b62f3-135">Beachten Sie, dass die dbset-Eigenschaften für den Kontext als virtuell markiert sind.</span><span class="sxs-lookup"><span data-stu-id="b62f3-135">Note that the DbSet properties on the context are marked as virtual.</span></span> <span data-ttu-id="b62f3-136">Dadurch kann das Simulation Framework aus unserem Kontext abgeleitet werden und diese Eigenschaften mit einer simulierte Implementierung überschreiben.</span><span class="sxs-lookup"><span data-stu-id="b62f3-136">This will allow the mocking framework to derive from our context and overriding these properties with a mocked implementation.</span></span>  

<span data-ttu-id="b62f3-137">Wenn Sie Code First verwenden, können Sie die Klassen direkt bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="b62f3-137">If you are using Code First then you can edit your classes directly.</span></span> <span data-ttu-id="b62f3-138">Wenn Sie den EF-Designer verwenden, müssen Sie die T4-Vorlage bearbeiten, mit der ihr Kontext generiert wird.</span><span class="sxs-lookup"><span data-stu-id="b62f3-138">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="b62f3-139">Öffnen Sie die \<model_name\> . Context.tt-Datei, die in der EDMX-Datei gespeichert ist, suchen Sie das folgende Code Fragment, und fügen Sie das Virtual-Schlüsselwort wie gezeigt hinzu.</span><span class="sxs-lookup"><span data-stu-id="b62f3-139">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the virtual keyword as shown.</span></span>  

``` csharp
public string DbSet(EntitySet entitySet)
{
    return string.Format(
        CultureInfo.InvariantCulture,
        "{0} virtual DbSet\<{1}> {2} {{ get; set; }}",
        Accessibility.ForReadOnlyProperty(entitySet),
        _typeMapper.GetTypeName(entitySet.ElementType),
        _code.Escape(entitySet));
}
```  

## <a name="service-to-be-tested"></a><span data-ttu-id="b62f3-140">Zu testender Dienst</span><span class="sxs-lookup"><span data-stu-id="b62f3-140">Service to be tested</span></span>  

<span data-ttu-id="b62f3-141">Um Tests mit in-Memory-Test Doubles zu veranschaulichen, schreiben wir einige Tests für einen Blogservice.</span><span class="sxs-lookup"><span data-stu-id="b62f3-141">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="b62f3-142">Der Dienst ist in der Lage, neue Blogs (addblog) zu erstellen und alle Blogs nach Namen (getallblogs) nach Namen zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="b62f3-142">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="b62f3-143">Zusätzlich zu getallblogs haben wir auch eine Methode bereitgestellt, die asynchron alle Blogs geordnet nach Namen (getallblogsasync) erhält.</span><span class="sxs-lookup"><span data-stu-id="b62f3-143">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class BlogService
    {
        private BloggingContext _context;

        public BlogService(BloggingContext context)
        {
            _context = context;
        }

        public Blog AddBlog(string name, string url)
        {
            var blog = _context.Blogs.Add(new Blog { Name = name, Url = url });
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

## <a name="testing-non-query-scenarios"></a><span data-ttu-id="b62f3-144">Testen von nicht-Abfrage Szenarios</span><span class="sxs-lookup"><span data-stu-id="b62f3-144">Testing non-query scenarios</span></span>  

<span data-ttu-id="b62f3-145">Das ist alles, was wir tun müssen, um nicht-Abfrage Methoden zu testen.</span><span class="sxs-lookup"><span data-stu-id="b62f3-145">That’s all we need to do to start testing non-query methods.</span></span> <span data-ttu-id="b62f3-146">Im folgenden Test wird "muq" verwendet, um einen Kontext zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b62f3-146">The following test uses Moq to create a context.</span></span> <span data-ttu-id="b62f3-147">Anschließend wird ein dbset erstellt \<Blog\> , das aus der Blogs-Eigenschaft des Kontexts zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="b62f3-147">It then creates a DbSet\<Blog\> and wires it up to be returned from the context’s Blogs property.</span></span> <span data-ttu-id="b62f3-148">Im nächsten Schritt wird der Kontext verwendet, um einen neuen Blog Dienst zu erstellen, der dann verwendet wird, um mithilfe der addblog-Methode einen neuen Blog zu erstellen –.</span><span class="sxs-lookup"><span data-stu-id="b62f3-148">Next, the context is used to create a new BlogService which is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="b62f3-149">Schließlich wird mit dem Test überprüft, ob der Dienst einen neuen Blog hinzugefügt und "SaveChanges" für den Kontext aufgerufen hat.</span><span class="sxs-lookup"><span data-stu-id="b62f3-149">Finally, the test verifies that the service added a new Blog and called SaveChanges on the context.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Data.Entity;

namespace TestingDemo
{
    [TestClass]
    public class NonQueryTests
    {
        [TestMethod]
        public void CreateBlog_saves_a_blog_via_context()
        {
            var mockSet = new Mock<DbSet<Blog>>();

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(m => m.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            service.AddBlog("ADO.NET Blog", "http://blogs.msdn.com/adonet");

            mockSet.Verify(m => m.Add(It.IsAny<Blog>()), Times.Once());
            mockContext.Verify(m => m.SaveChanges(), Times.Once());
        }
    }
}
```  

## <a name="testing-query-scenarios"></a><span data-ttu-id="b62f3-150">Testen von Abfrage Szenarios</span><span class="sxs-lookup"><span data-stu-id="b62f3-150">Testing query scenarios</span></span>  

<span data-ttu-id="b62f3-151">Um Abfragen für unseren dbset-Test Double ausführen zu können, müssen wir eine Implementierung von iquervable einrichten.</span><span class="sxs-lookup"><span data-stu-id="b62f3-151">In order to be able to execute queries against our DbSet test double we need to setup an implementation of IQueryable.</span></span> <span data-ttu-id="b62f3-152">Der erste Schritt besteht darin, einige Daten im Arbeitsspeicher zu erstellen – wir verwenden eine Liste \<Blog\> .</span><span class="sxs-lookup"><span data-stu-id="b62f3-152">The first step is to create some in-memory data – we’re using a List\<Blog\>.</span></span> <span data-ttu-id="b62f3-153">Als Nächstes erstellen wir einen Kontext und dbset und \<Blog\> Verknüpfen dann die iquerable-Implementierung für das dbset – Sie delegieren lediglich an den LINQ to Objects-Anbieter, der mit List arbeitet \<T\> .</span><span class="sxs-lookup"><span data-stu-id="b62f3-153">Next, we create a context and DBSet\<Blog\> then wire up the IQueryable implementation for the DbSet – they’re just delegating to the LINQ to Objects provider that works with List\<T\>.</span></span>  

<span data-ttu-id="b62f3-154">Wir können dann basierend auf den Test Doubles einen BlogService erstellen und sicherstellen, dass die Daten, die wir von getallblogs erhalten, nach dem Namen geordnet sind.</span><span class="sxs-lookup"><span data-stu-id="b62f3-154">We can then create a BlogService based on our test doubles and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;

namespace TestingDemo
{
    [TestClass]
    public class QueryTests
    {
        [TestMethod]
        public void GetAllBlogs_orders_by_name()
        {
            var data = new List<Blog>
            {
                new Blog { Name = "BBB" },
                new Blog { Name = "ZZZ" },
                new Blog { Name = "AAA" },
            }.AsQueryable();

            var mockSet = new Mock<DbSet<Blog>>();
            mockSet.As<IQueryable<Blog>>().Setup(m => m.Provider).Returns(data.Provider);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.Expression).Returns(data.Expression);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.ElementType).Returns(data.ElementType);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(data.GetEnumerator());

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(c => c.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            var blogs = service.GetAllBlogs();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

### <a name="testing-with-async-queries"></a><span data-ttu-id="b62f3-155">Testen mit asynchronen Abfragen</span><span class="sxs-lookup"><span data-stu-id="b62f3-155">Testing with async queries</span></span>

<span data-ttu-id="b62f3-156">In Entity Framework 6 wurde eine Reihe von Erweiterungs Methoden eingeführt, die verwendet werden können, um eine Abfrage asynchron auszuführen.</span><span class="sxs-lookup"><span data-stu-id="b62f3-156">Entity Framework 6 introduced a set of extension methods that can be used to asynchronously execute a query.</span></span> <span data-ttu-id="b62f3-157">Beispiele für diese Methoden sind z. b. "delistasync", "firstasync", "foreachasync" und</span><span class="sxs-lookup"><span data-stu-id="b62f3-157">Examples of these methods include ToListAsync, FirstAsync, ForEachAsync, etc.</span></span>  

<span data-ttu-id="b62f3-158">Da Entity Framework Abfragen LINQ verwenden, werden die Erweiterungs Methoden für iquervable und IEnumerable definiert.</span><span class="sxs-lookup"><span data-stu-id="b62f3-158">Because Entity Framework queries make use of LINQ, the extension methods are defined on IQueryable and IEnumerable.</span></span> <span data-ttu-id="b62f3-159">Da Sie jedoch nur für die Verwendung mit Entity Framework entworfen wurden, erhalten Sie möglicherweise die folgende Fehlermeldung, wenn Sie versuchen, Sie in einer LINQ-Abfrage zu verwenden, die keine Entity Framework Abfrage ist:</span><span class="sxs-lookup"><span data-stu-id="b62f3-159">However, because they are only designed to be used with Entity Framework you may receive the following error if you try to use them on a LINQ query that isn’t an Entity Framework query:</span></span>

> <span data-ttu-id="b62f3-160">Die iquervable-Quelle implementiert idbasyncenumerable nicht {0} .</span><span class="sxs-lookup"><span data-stu-id="b62f3-160">The source IQueryable doesn't implement IDbAsyncEnumerable{0}.</span></span> <span data-ttu-id="b62f3-161">Nur Quellen, die idbasyncenumerable implementieren, können für Entity Framework asynchronen Vorgängen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b62f3-161">Only sources that implement IDbAsyncEnumerable can be used for Entity Framework asynchronous operations.</span></span> <span data-ttu-id="b62f3-162">Weitere Informationen finden Sie unter [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068) .</span><span class="sxs-lookup"><span data-stu-id="b62f3-162">For more details see [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068).</span></span>  

<span data-ttu-id="b62f3-163">Obwohl die asynchronen Methoden nur bei der Ausführung für eine EF-Abfrage unterstützt werden, können Sie Sie in Ihrem Komponenten Test verwenden, wenn Sie für einen Test Double-Wert im Arbeitsspeicher eines dbsets ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b62f3-163">Whilst the async methods are only supported when running against an EF query, you may want to use them in your unit test when running against an in-memory test double of a DbSet.</span></span>  

<span data-ttu-id="b62f3-164">Um die Async-Methoden zu verwenden, müssen wir einen in-Memory-dbasyncqueryprovider erstellen, um die asynchrone Abfrage zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b62f3-164">In order to use the async methods we need to create an in-memory DbAsyncQueryProvider to process the async query.</span></span> <span data-ttu-id="b62f3-165">Obwohl es möglich wäre, mithilfe von "muq" einen Abfrage Anbieter einzurichten, ist es viel einfacher, eine Test-Double-Implementierung im Code zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b62f3-165">Whilst it would be possible to setup a query provider using Moq, it is much easier to create a test double implementation in code.</span></span> <span data-ttu-id="b62f3-166">Der Code für diese Implementierung lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="b62f3-166">The code for this implementation is as follows:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Linq.Expressions;
using System.Threading;
using System.Threading.Tasks;

namespace TestingDemo
{
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

<span data-ttu-id="b62f3-167">Nun, da wir einen Async-Abfrage Anbieter haben, können wir einen Komponenten Test für unsere neue getallblogsasync-Methode schreiben.</span><span class="sxs-lookup"><span data-stu-id="b62f3-167">Now that we have an async query provider we can write a unit test for our new GetAllBlogsAsync method.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
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

            var data = new List<Blog>
            {
                new Blog { Name = "BBB" },
                new Blog { Name = "ZZZ" },
                new Blog { Name = "AAA" },
            }.AsQueryable();

            var mockSet = new Mock<DbSet<Blog>>();
            mockSet.As<IDbAsyncEnumerable<Blog>>()
                .Setup(m => m.GetAsyncEnumerator())
                .Returns(new TestDbAsyncEnumerator<Blog>(data.GetEnumerator()));

            mockSet.As<IQueryable<Blog>>()
                .Setup(m => m.Provider)
                .Returns(new TestDbAsyncQueryProvider<Blog>(data.Provider));

            mockSet.As<IQueryable<Blog>>().Setup(m => m.Expression).Returns(data.Expression);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.ElementType).Returns(data.ElementType);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(data.GetEnumerator());

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(c => c.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            var blogs = await service.GetAllBlogsAsync();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  
