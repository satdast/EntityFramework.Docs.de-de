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
# <a name="working-with-proxies"></a>Arbeiten mit Proxys
Beim Erstellen von Instanzen von poco-Entitäts Typen erstellt Entity Framework häufig Instanzen eines dynamisch generierten abgeleiteten Typs, der als Proxy für die Entität fungiert. Dieser Proxy überschreibt einige virtuelle Eigenschaften der Entität, um beim Zugriff auf die Eigenschaft automatisch Hooks zum Ausführen von Aktionen einzufügen. Dieser Mechanismus wird z. b. verwendet, um Lazy Loading von Beziehungen zu unterstützen. Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.  

## <a name="disabling-proxy-creation"></a>Proxy Erstellung wird deaktiviert.  

Manchmal ist es hilfreich, die Erstellung von Proxy Instanzen durch Entity Framework zu verhindern. Beispielsweise ist das Serialisieren von nicht-Proxy Instanzen erheblich einfacher als das Serialisieren von Proxy Instanzen. Die Proxy Erstellung kann deaktiviert werden, indem das-Flag gelöscht wird `ProxyCreationEnabled` . Ein Ort, an dem Sie dies vornehmen können, ist der Konstruktor Ihres Kontexts. Beispiel:  

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

Beachten Sie, dass EF keine Proxys für Typen erstellt, bei denen es für den Proxy nichts gibt. Dies bedeutet, dass Sie Proxys auch vermeiden können, wenn Sie über Typen verfügen, die versiegelt sind und/oder keine virtuellen Eigenschaften haben.  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a>Explizites Erstellen einer Instanz eines Proxys  

Eine Proxy Instanz wird nicht erstellt, wenn Sie mithilfe des new-Operators eine Instanz einer Entität erstellen. Dies stellt möglicherweise kein Problem dar. Wenn Sie jedoch eine Proxy Instanz erstellen müssen (z. b., damit Lazy Loading oder Proxy Änderungs Nachverfolgung funktioniert), können Sie dies mit der- `Create` Methode von tun `DbSet` . Beispiel:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

Die generische Version von `Create` kann verwendet werden, wenn Sie eine Instanz eines abgeleiteten Entitäts Typs erstellen möchten. Beispiel:  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

Beachten Sie, dass die- `Create` Methode die erstellte Entität nicht zum Kontext hinzufügt oder anfügt.  

Beachten Sie, dass die- `Create` Methode nur eine Instanz des Entitäts Typs selbst erstellt, wenn die Erstellung eines Proxytyps für die Entität keinen Wert hätte, weil Sie nichts unternimmt. Wenn der Entitätstyp z. b. versiegelt ist und/oder keine virtuellen Eigenschaften aufweist, `Create` wird nur eine Instanz des Entitäts Typs erstellt.  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a>Der tatsächliche Entitätstyp wird von einem Proxytyp erhalten.  

Proxy Typen haben Namen, die in etwa wie folgt aussehen:  

```
System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6
```

Der Entitätstyp für diesen Proxytyp kann mithilfe der- `GetObjectType` Methode von gefunden werden `ObjectContext` . Beispiel:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

Beachten Sie Folgendes: Wenn der an weiter `GetObjectType` gegebene Typ eine Instanz eines Entitäts Typs ist, der kein Proxytyp ist, wird der Entitätstyp weiterhin zurückgegeben. Dies bedeutet, dass Sie immer diese Methode verwenden können, um den eigentlichen Entitätstyp ohne andere zu überprüfen, um festzustellen, ob es sich bei dem Typ um einen Proxytyp handelt.  
