---
title: Definieren von dbsets-EF6
description: Definieren von dbsets in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 4528a509-ace7-4dfb-8065-1b833f5e03a0
uid: ef6/modeling/code-first/dbsets
ms.openlocfilehash: 55742c507e0d6b898a0cac18f34af84e736d771d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617060"
---
# <a name="defining-dbsets"></a>Definieren von dbsets
Wenn Sie mit dem Code First-Workflow entwickeln, definieren Sie einen abgeleiteten dbcontext, der Ihre Sitzung mit der Datenbank repräsentiert und ein dbset für jeden Typ im Modell verfügbar macht. In diesem Thema werden die verschiedenen Möglichkeiten beschrieben, wie Sie die dbset-Eigenschaften definieren können.  

## <a name="dbcontext-with-dbset-properties"></a>Dbcontext mit dbset-Eigenschaften  

Der gängige Fall, der in Code First Beispielen gezeigt wird, besteht darin, einen dbcontext mit öffentlichen automatischen dbset-Eigenschaften für die Entitäts Typen des Modells zu erhalten. Beispiel:  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

Bei der Verwendung im Code First Modus werden Blogs und Beiträge als Entitäts Typen konfiguriert und andere Typen konfiguriert, die von diesen verwendet werden können. Außerdem ruft dbcontext automatisch den Setter für jede dieser Eigenschaften auf, um eine Instanz des entsprechenden dbsets festzulegen.  

## <a name="dbcontext-with-idbset-properties"></a>Dbcontext mit idbset-Eigenschaften  

Es gibt Situationen, z. b. beim Erstellen von Pseudo-oder Fakes, bei denen es nützlicher ist, die festgelegten Eigenschaften mithilfe einer Schnittstelle zu deklarieren. In solchen Fällen kann die idbset-Schnittstelle anstelle von dbset verwendet werden. Beispiel:  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

Dieser Kontext funktioniert auf genau dieselbe Weise wie der Kontext, der die dbset-Klasse für seine Set-Eigenschaften verwendet.  

## <a name="dbcontext-with-read-only-set-properties"></a>Dbcontext mit schreibgeschützten Set-Eigenschaften  

Wenn Sie keine öffentlichen Setter für die dbset-oder idbset-Eigenschaften verfügbar machen möchten, können Sie stattdessen schreibgeschützte Eigenschaften erstellen und die Set-Instanzen selbst erstellen. Beispiel:  

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

Beachten Sie, dass dbcontext die Instanz von dbset zwischenspeichert, die von der Set-Methode zurückgegeben wird, sodass jede dieser Eigenschaften bei jedem Aufruf dieselbe Instanz zurückgibt.  

Die Ermittlung von Entitäts Typen für Code First funktioniert auf dieselbe Weise wie bei Eigenschaften mit öffentlichen Getter und Settern.  
