---
title: Testen mit ihren eigenen Test Doubles-EF6
description: Testen mit ihren eigenen Test Doubles in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/testing/writing-test-doubles
ms.openlocfilehash: 575007c5568049e6508d1bd6435597a1acd843ab
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070431"
---
# <a name="testing-with-your-own-test-doubles"></a>Testen mit ihren eigenen Test Doubles
> [!NOTE]
> **Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.  

Beim Schreiben von Tests für Ihre Anwendung ist es häufig wünschenswert, das Erreichen der Datenbank zu vermeiden.  Entity Framework ermöglicht es Ihnen, dies zu erreichen, indem Sie einen Kontext – mit von den Tests definiertem Verhalten – erstellen, der Daten im Arbeitsspeicher nutzt.  

## <a name="options-for-creating-test-doubles"></a>Optionen zum Erstellen von Test Doubles  

Es gibt zwei verschiedene Ansätze, die verwendet werden können, um eine in-Memory-Version Ihres Kontexts zu erstellen.  

- **Erstellen Sie eigene Test Doubles** – diese Vorgehensweise umfasst das Schreiben einer eigenen in-Memory-Implementierung Ihres Kontexts und von dbsets. Dadurch haben Sie viele Kontrolle darüber, wie sich die Klassen Verhalten, aber Sie können das Schreiben und das Besitz einer angemessenen Menge an Code einschließen.  
- **Verwenden eines kontextbasierten Frameworks zum Erstellen von Test Doubles** – mithilfe eines kontextbasierten Frameworks (wie z. b. von muq) können Sie die in-Memory-Implementierungen Ihres Kontexts und Sätze dynamisch zur Laufzeit erstellen.  

In diesem Artikel wird das Erstellen eines eigenen Test Double behandelt. Weitere Informationen zur Verwendung eines-Frameworks finden Sie unter [Testen mit einem](xref:ef6/fundamentals/testing/mocking)-Frameworks.  

## <a name="testing-with-pre-ef6-versions"></a>Testen mit Pre-EF6-Versionen  

Der in diesem Artikel gezeigte Code ist kompatibel mit EF6. Informationen zu Tests mit EF5 und einer früheren Version finden Sie [untertests mit einem gefälschten Kontext](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).  

## <a name="limitations-of-ef-in-memory-test-doubles"></a>Einschränkungen von EF-in-Memory-Test Doubles  

In-Memory-Test Doubles können eine gute Möglichkeit zum Bereitstellen von Komponenten Testebene für Bits Ihrer Anwendung sein, die EF verwenden. Dabei verwenden Sie jedoch LINQ to Objects, um Abfragen für in-Memory-Daten auszuführen. Dies kann zu einem anderen Verhalten führen als die Verwendung des LINQ-Anbieters (LINQ to Entities) von EF, um Abfragen in SQL zu übersetzen, die für die Datenbank ausgeführt werden.  

Ein Beispiel für einen solchen Unterschied besteht darin, verknüpfte Daten zu laden. Wenn Sie eine Reihe von Blogs erstellen, die jeweils über verwandte Beiträge verfügen, werden bei der Verwendung von in-Memory-Daten die zugehörigen Beiträge immer für jeden Blog geladen. Wenn Sie jedoch für eine Datenbank ausführen, werden die Daten nur geladen, wenn Sie die Include-Methode verwenden.  

Aus diesem Grund empfiehlt es sich, immer einen gewissen Grad an End-to-End-Tests (zusätzlich zu den Komponententests) einzubeziehen, um sicherzustellen, dass Ihre Anwendung für eine Datenbank ordnungsgemäß funktioniert.  

## <a name="following-along-with-this-article"></a>Im Anschluss an diesen Artikel  

Dieser Artikel enthält umfassende Code Auflistungen, die Sie in Visual Studio kopieren können, wenn Sie möchten. Es ist am einfachsten, ein Komponenten **Test Projekt** zu erstellen, und Sie müssen **.NET Framework 4,5** als Ziel verwenden, um die Abschnitte mit "Async" zu vervollständigen.  

## <a name="creating-a-context-interface"></a>Erstellen einer Kontext Schnittstelle  

Wir sehen uns nun an, wie wir einen Dienst testen, der ein EF-Modell verwendet. Um unseren EF-Kontext durch eine in-Memory-Version für Tests ersetzen zu können, definieren wir eine Schnittstelle, die der EF-Kontext (und der in-Memory-Double) implementiert.

Der Dienst, den wir testen werden, fragt und ändert Daten mithilfe der dbset-Eigenschaften unseres Kontexts und ruft auch SaveChanges auf, um Änderungen an die Datenbank zu übernehmen. Daher fügen wir diese Member in die Schnittstelle ein.  

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

## <a name="the-ef-model"></a>Das EF-Modell  

Der zu testende Dienst nutzt ein EF-Modell, das aus dem bloggingcontext und den Blog-und Post-Klassen besteht. Dieser Code wurde möglicherweise vom EF-Designer generiert oder ist ein Code First Modell.  

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

### <a name="implementing-the-context-interface-with-the-ef-designer"></a>Implementieren der Kontext Schnittstelle mit dem EF-Designer  

Beachten Sie, dass unser Kontext die ibloggingcontext-Schnittstelle implementiert.  

Wenn Sie Code First verwenden, können Sie den Kontext direkt bearbeiten, um die-Schnittstelle zu implementieren. Wenn Sie den EF-Designer verwenden, müssen Sie die T4-Vorlage bearbeiten, mit der ihr Kontext generiert wird. Öffnen Sie die \<model_name\> . Context.tt-Datei, die unter der EDMX-Datei gespeichert ist, das folgende Code Fragment suchen und wie gezeigt in der-Schnittstelle hinzufügen.  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
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

## <a name="creating-the-in-memory-test-doubles"></a>Erstellen der Test Double-in-Memory-Tests  

Nachdem wir nun über das echte EF-Modell und den Dienst verfügen, die es verwenden können, ist es an der Zeit, den in-Memory-Test Double zu erstellen, den wir für Tests verwenden können. Wir haben einen TestContext-Test Double für den Kontext erstellt. In Test Doubles wählen wir das gewünschte Verhalten aus, um die Tests zu unterstützen, die wir ausführen möchten. In diesem Beispiel erfassen wir einfach, wie oft SaveChanges aufgerufen wird, aber Sie können die erforderliche Logik zum Überprüfen des Szenarios einschließen, das Sie testen.  

Wir haben auch ein testdbset erstellt, das eine in-Memory-Implementierung von dbset bereitstellt. Wir haben eine vollständige Implementierung für alle Methoden in dbset (mit Ausnahme von Find) bereitgestellt, Sie müssen jedoch nur die Member implementieren, die in Ihrem Testszenario verwendet werden.  

Testdbset nutzt einige andere Infrastruktur Klassen, die wir enthalten haben, um sicherzustellen, dass asynchrone Abfragen verarbeitet werden können.  

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

### <a name="implementing-find"></a>Implementieren der Suche  

Die Find-Methode ist in einer generischen Weise schwierig zu implementieren. Wenn Sie Code testen müssen, der die Find-Methode verwendet, ist es am einfachsten, ein Test-dbset für jeden Entitätstyp zu erstellen, der die Suche unterstützen muss. Anschließend können Sie die Logik zum Auffinden dieses bestimmten Entitäts Typs schreiben, wie unten gezeigt.  

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

## <a name="writing-some-tests"></a>Schreiben von Tests  

Das ist alles, was wir tun müssen, um den Test zu starten. Der folgende Test erstellt ein TestContext und dann einen Dienst, der auf diesem Kontext basiert. Der Dienst wird dann verwendet, um mithilfe der addblog-Methode einen neuen Blog – zu erstellen. Schließlich überprüft der Test, ob der Dienst einen neuen Blog zur Blogs-Eigenschaft des Kontexts hinzugefügt hat und "SaveChanges" im Kontext heißt.  

Dies ist nur ein Beispiel für die Art der Dinge, die Sie mit einem in-Memory-Test Double testen können, und Sie können die Logik der Test Doubles und die Überprüfung an Ihre Anforderungen anpassen.  

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

Im folgenden finden Sie ein weiteres Beispiel für einen Test. dieser Zeitpunkt führt eine Abfrage aus. Der Test beginnt mit dem Erstellen eines Test Kontexts mit einigen Daten in seiner Blog-Eigenschaft. Beachten Sie, dass die Daten nicht in alphabetischer Reihenfolge vorliegen. Wir können dann basierend auf dem Test Kontext einen BlogService erstellen und sicherstellen, dass die Daten, die wir von getallblogs erhalten, nach dem Namen geordnet sind.  

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

Schließlich schreiben wir einen weiteren Test, der unsere Async-Methode verwendet, um sicherzustellen, dass die in [testdbset](#creating-the-in-memory-test-doubles) enthaltene Async-Infrastruktur funktioniert.  

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
