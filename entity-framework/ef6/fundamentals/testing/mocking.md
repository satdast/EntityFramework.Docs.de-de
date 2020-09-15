---
title: Testen mit einem Simulations Framework EF6
description: Testen mit einem Simulations Framework in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/testing/mocking
ms.openlocfilehash: ebd57ca2127bab32c94d806f8213739a6e0b4f0a
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070483"
---
# <a name="testing-with-a-mocking-framework"></a>Testen mit einem Frameworks
> [!NOTE]
> **Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.  

Beim Schreiben von Tests für Ihre Anwendung ist es häufig wünschenswert, das Erreichen der Datenbank zu vermeiden.  Entity Framework ermöglicht es Ihnen, dies zu erreichen, indem Sie einen Kontext – mit von den Tests definiertem Verhalten – erstellen, der Daten im Arbeitsspeicher nutzt.  

## <a name="options-for-creating-test-doubles"></a>Optionen zum Erstellen von Test Doubles  

Es gibt zwei verschiedene Ansätze, die verwendet werden können, um eine in-Memory-Version Ihres Kontexts zu erstellen.  

- **Erstellen Sie eigene Test Doubles** – diese Vorgehensweise umfasst das Schreiben einer eigenen in-Memory-Implementierung Ihres Kontexts und von dbsets. Dadurch haben Sie viele Kontrolle darüber, wie sich die Klassen Verhalten, aber Sie können das Schreiben und das Besitz einer angemessenen Menge an Code einschließen.  
- **Verwenden eines kontextbasierten Frameworks zum Erstellen von Test Doubles** – mithilfe eines kontextbasierten Frameworks (wie z. b. von muq) können Sie die in-Memory-Implementierungen Ihres Kontexts und die festgelegt werden, die für Sie dynamisch zur Laufzeit erstellt werden.  

In diesem Artikel wird die Verwendung eines-Frameworks behandelt. Informationen zum Erstellen eigener Test Doubles finden Sie [unter Testen mit ihren eigenen Test Doubles](xref:ef6/fundamentals/testing/writing-test-doubles).  

Um die Verwendung von EF mit einem-Frameworks zu veranschaulichen, verwenden wir "muq". Die einfachste Möglichkeit zum Aufrufen von "muq" ist die Installation des " [muq"-Pakets von nuget](https://nuget.org/packages/Moq/).  

## <a name="testing-with-pre-ef6-versions"></a>Testen mit Pre-EF6-Versionen  

Das in diesem Artikel gezeigte Szenario ist abhängig von einigen Änderungen, die wir an dbset in EF6 vorgenommen haben. Informationen zu Tests mit EF5 und einer früheren Version finden Sie [untertests mit einem gefälschten Kontext](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).  

## <a name="limitations-of-ef-in-memory-test-doubles"></a>Einschränkungen von EF-in-Memory-Test Doubles  

In-Memory-Test Doubles können eine gute Möglichkeit zum Bereitstellen von Komponenten Testebene für Bits Ihrer Anwendung sein, die EF verwenden. Dabei verwenden Sie jedoch LINQ to Objects, um Abfragen für in-Memory-Daten auszuführen. Dies kann zu einem anderen Verhalten führen als die Verwendung des LINQ-Anbieters (LINQ to Entities) von EF, um Abfragen in SQL zu übersetzen, die für die Datenbank ausgeführt werden.  

Ein Beispiel für einen solchen Unterschied besteht darin, verknüpfte Daten zu laden. Wenn Sie eine Reihe von Blogs erstellen, die jeweils über verwandte Beiträge verfügen, werden bei der Verwendung von in-Memory-Daten die zugehörigen Beiträge immer für jeden Blog geladen. Wenn Sie jedoch für eine Datenbank ausführen, werden die Daten nur geladen, wenn Sie die Include-Methode verwenden.  

Aus diesem Grund empfiehlt es sich, immer einen gewissen Grad an End-to-End-Tests (zusätzlich zu den Komponententests) einzubeziehen, um sicherzustellen, dass Ihre Anwendung für eine Datenbank ordnungsgemäß funktioniert.  

## <a name="following-along-with-this-article"></a>Im Anschluss an diesen Artikel  

Dieser Artikel enthält umfassende Code Auflistungen, die Sie in Visual Studio kopieren können, wenn Sie möchten. Es ist am einfachsten, ein Komponenten **Test Projekt** zu erstellen, und Sie müssen **.NET Framework 4,5** als Ziel verwenden, um die Abschnitte mit "Async" zu vervollständigen.  

## <a name="the-ef-model"></a>Das EF-Modell  

Der zu testende Dienst nutzt ein EF-Modell, das aus dem bloggingcontext und den Blog-und Post-Klassen besteht. Dieser Code wurde möglicherweise vom EF-Designer generiert oder ist ein Code First Modell.  

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

### <a name="virtual-dbset-properties-with-ef-designer"></a>Eigenschaften von virtuellen dbsets mit dem EF-Designer  

Beachten Sie, dass die dbset-Eigenschaften für den Kontext als virtuell markiert sind. Dadurch kann das Simulation Framework aus unserem Kontext abgeleitet werden und diese Eigenschaften mit einer simulierte Implementierung überschreiben.  

Wenn Sie Code First verwenden, können Sie die Klassen direkt bearbeiten. Wenn Sie den EF-Designer verwenden, müssen Sie die T4-Vorlage bearbeiten, mit der ihr Kontext generiert wird. Öffnen Sie die \<model_name\> . Context.tt-Datei, die in der EDMX-Datei gespeichert ist, suchen Sie das folgende Code Fragment, und fügen Sie das Virtual-Schlüsselwort wie gezeigt hinzu.  

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

## <a name="service-to-be-tested"></a>Zu testender Dienst  

Um Tests mit in-Memory-Test Doubles zu veranschaulichen, schreiben wir einige Tests für einen Blogservice. Der Dienst ist in der Lage, neue Blogs (addblog) zu erstellen und alle Blogs nach Namen (getallblogs) nach Namen zurückzugeben. Zusätzlich zu getallblogs haben wir auch eine Methode bereitgestellt, die asynchron alle Blogs geordnet nach Namen (getallblogsasync) erhält.  

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

## <a name="testing-non-query-scenarios"></a>Testen von nicht-Abfrage Szenarios  

Das ist alles, was wir tun müssen, um nicht-Abfrage Methoden zu testen. Im folgenden Test wird "muq" verwendet, um einen Kontext zu erstellen. Anschließend wird ein dbset erstellt \<Blog\> , das aus der Blogs-Eigenschaft des Kontexts zurückgegeben wird. Im nächsten Schritt wird der Kontext verwendet, um einen neuen Blog Dienst zu erstellen, der dann verwendet wird, um mithilfe der addblog-Methode einen neuen Blog zu erstellen –. Schließlich wird mit dem Test überprüft, ob der Dienst einen neuen Blog hinzugefügt und "SaveChanges" für den Kontext aufgerufen hat.  

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

## <a name="testing-query-scenarios"></a>Testen von Abfrage Szenarios  

Um Abfragen für unseren dbset-Test Double ausführen zu können, müssen wir eine Implementierung von iquervable einrichten. Der erste Schritt besteht darin, einige Daten im Arbeitsspeicher zu erstellen – wir verwenden eine Liste \<Blog\> . Als Nächstes erstellen wir einen Kontext und dbset und \<Blog\> Verknüpfen dann die iquerable-Implementierung für das dbset – Sie delegieren lediglich an den LINQ to Objects-Anbieter, der mit List arbeitet \<T\> .  

Wir können dann basierend auf den Test Doubles einen BlogService erstellen und sicherstellen, dass die Daten, die wir von getallblogs erhalten, nach dem Namen geordnet sind.  

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

### <a name="testing-with-async-queries"></a>Testen mit asynchronen Abfragen

In Entity Framework 6 wurde eine Reihe von Erweiterungs Methoden eingeführt, die verwendet werden können, um eine Abfrage asynchron auszuführen. Beispiele für diese Methoden sind z. b. "delistasync", "firstasync", "foreachasync" und  

Da Entity Framework Abfragen LINQ verwenden, werden die Erweiterungs Methoden für iquervable und IEnumerable definiert. Da Sie jedoch nur für die Verwendung mit Entity Framework entworfen wurden, erhalten Sie möglicherweise die folgende Fehlermeldung, wenn Sie versuchen, Sie in einer LINQ-Abfrage zu verwenden, die keine Entity Framework Abfrage ist:

> Die iquervable-Quelle implementiert idbasyncenumerable nicht {0} . Nur Quellen, die idbasyncenumerable implementieren, können für Entity Framework asynchronen Vorgängen verwendet werden. Weitere Informationen finden Sie unter [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068) .  

Obwohl die asynchronen Methoden nur bei der Ausführung für eine EF-Abfrage unterstützt werden, können Sie Sie in Ihrem Komponenten Test verwenden, wenn Sie für einen Test Double-Wert im Arbeitsspeicher eines dbsets ausgeführt werden.  

Um die Async-Methoden zu verwenden, müssen wir einen in-Memory-dbasyncqueryprovider erstellen, um die asynchrone Abfrage zu verarbeiten. Obwohl es möglich wäre, mithilfe von "muq" einen Abfrage Anbieter einzurichten, ist es viel einfacher, eine Test-Double-Implementierung im Code zu erstellen. Der Code für diese Implementierung lautet wie folgt:  

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

Nun, da wir einen Async-Abfrage Anbieter haben, können wir einen Komponenten Test für unsere neue getallblogsasync-Methode schreiben.  

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
