---
title: Code First gespeicherte Prozeduren INSERT, Update und DELETE-EF6
description: Code First gespeicherte Prozeduren INSERT, Update und DELETE in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/cud-stored-procedures
ms.openlocfilehash: 2bf2b3d343b70d80c5c56922a31f9b1ece54f60a
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073937"
---
# <a name="code-first-insert-update-and-delete-stored-procedures"></a>Code First gespeicherte Prozeduren INSERT, Update und DELETE
> [!NOTE]
> **Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.  

Standardmäßig konfiguriert Code First alle Entitäten so, dass INSERT-, Update-und DELETE-Befehle mithilfe des direkten Tabellen Zugriffs durchgeführt werden. Beginnend mit EF6 können Sie das Code First Modell so konfigurieren, dass es gespeicherte Prozeduren für einige oder alle Entitäten im Modell verwendet.  

## <a name="basic-entity-mapping"></a>Grundlegende Entitäts Zuordnung  

Sie können gespeicherte Prozeduren für INSERT, Update und DELETE mithilfe der fließend-API verwenden.  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

Dies führt dazu, dass Code First einige Konventionen verwenden, um die erwartete Form der gespeicherten Prozeduren in der Datenbank zu erstellen.  

- Drei gespeicherte Prozeduren namens ** \<type_name\> _INSERT**, ** \<type_name\> _Update** und ** \<type_name\> _Delete** (z. b. Blog_Insert, Blog_Update und Blog_Delete).  
- Parameter Namen entsprechen den Eigenschaftsnamen.  
  > [!NOTE]
  > Wenn Sie hascolumnname () oder das Column-Attribut verwenden, um die Spalte für eine bestimmte Eigenschaft umzubenennen, wird dieser Name für Parameter anstelle des Eigenschafts namens verwendet.  
- **Die gespeicherte Prozedur INSERT** verfügt über einen Parameter für jede Eigenschaft, mit Ausnahme derjenigen, die als vom Store generiert (Identität oder berechnet) gekennzeichnet sind. Die gespeicherte Prozedur sollte ein Resultset mit einer Spalte für jede vom Speicher generierte Eigenschaft zurückgeben.  
- **Die gespeicherte Update Prozedur** verfügt über einen Parameter für jede Eigenschaft, mit Ausnahme derjenigen, die mit einem vom Speicher generierten Muster "berechnet" gekennzeichnet sind. Einige Parallelitäts Token erfordern einen Parameter für den ursprünglichen Wert. Weitere Informationen finden Sie im Abschnitt Parallelitäts *Token* weiter unten. Die gespeicherte Prozedur sollte ein Resultset mit einer Spalte für jede berechnete Eigenschaft zurückgeben.  
- **Die gespeicherte DELETE-Prozedur** muss über einen Parameter für den Schlüsselwert der Entität verfügen (oder mehrere Parameter, wenn die Entität über einen zusammengesetzten Schlüssel verfügt). Außerdem sollte die Delete-Prozedur auch über Parameter für alle unabhängigen Zuordnungs Fremdschlüssel in der Ziel Tabelle verfügen (Beziehungen, die keine entsprechenden Fremdschlüssel Eigenschaften haben, die in der Entität deklariert sind). Einige Parallelitäts Token erfordern einen Parameter für den ursprünglichen Wert. Weitere Informationen finden Sie im Abschnitt Parallelitäts *Token* weiter unten.  

Verwenden Sie die folgende Klasse als Beispiel:  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

Die gespeicherten Standard Prozeduren lauten wie folgt:  

``` SQL
CREATE PROCEDURE [dbo].[Blog_Insert]  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
BEGIN
  INSERT INTO [dbo].[Blogs] ([Name], [Url])
  VALUES (@Name, @Url)

  SELECT SCOPE_IDENTITY() AS BlogId
END
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId;
CREATE PROCEDURE [dbo].[Blog_Delete]  
  @BlogId int  
AS  
  DELETE FROM [dbo].[Blogs]
  WHERE BlogId = @BlogId
```  

### <a name="overriding-the-defaults"></a>Überschreiben der Standardwerte  

Sie können einen Teil oder alle der standardmäßig konfigurierten Einstellungen außer Kraft setzen.  

Sie können den Namen einer oder mehrerer gespeicherter Prozeduren ändern. In diesem Beispiel wird nur die gespeicherte Update Prozedur umbenannt.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

In diesem Beispiel werden alle drei gespeicherten Prozeduren umbenannt.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

In diesen Beispielen werden die Aufrufe miteinander verkettet, aber Sie können auch die Lambda-Block Syntax verwenden.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    {  
      s.Update(u => u.HasName("modify_blog"));  
      s.Delete(d => d.HasName("delete_blog"));  
      s.Insert(i => i.HasName("insert_blog"));  
    });
```  

In diesem Beispiel wird der-Parameter für die BlogId-Eigenschaft der gespeicherten Update-Prozedur umbenannt.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

Diese Aufrufe sind alle verkettbar und zusammensetzbar. Es folgt ein Beispiel, das alle drei gespeicherten Prozeduren und ihre Parameter umbenennt.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")  
                   .Parameter(b => b.BlogId, "blog_id")  
                   .Parameter(b => b.Name, "blog_name")  
                   .Parameter(b => b.Url, "blog_url"))  
     .Delete(d => d.HasName("delete_blog")  
                   .Parameter(b => b.BlogId, "blog_id"))  
     .Insert(i => i.HasName("insert_blog")  
                   .Parameter(b => b.Name, "blog_name")  
                   .Parameter(b => b.Url, "blog_url")));
```  

Sie können auch den Namen der Spalten im Resultset ändern, das von der Datenbank generierte Werte enthält.  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures(s =>
    s.Insert(i => i.Result(b => b.BlogId, "generated_blog_identity")));
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Insert]  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
BEGIN
  INSERT INTO [dbo].[Blogs] ([Name], [Url])
  VALUES (@Name, @Url)

  SELECT SCOPE_IDENTITY() AS generated_blog_id
END
```  

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a>Beziehungen ohne Fremdschlüssel in der Klasse (unabhängige Zuordnungen)  

Wenn eine Fremdschlüssel Eigenschaft in der Klassendefinition enthalten ist, kann der entsprechende Parameter auf dieselbe Weise wie jede andere Eigenschaft umbenannt werden. Wenn eine Beziehung ohne Fremdschlüssel Eigenschaft in der-Klasse vorhanden ist, lautet der Standardparameter Name ** \<navigation_property_name\> _ \<primary_key_name\> **.  

Beispielsweise würden die folgenden Klassendefinitionen dazu führen, dass in den gespeicherten Prozeduren ein Blog_BlogId-Parameter erwartet wird, um Beiträge einzufügen und zu aktualisieren.  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }

  public List<Post> Posts { get; set; }  
}  

public class Post  
{  
  public int PostId { get; set; }  
  public string Title { get; set; }  
  public string Content { get; set; }  

  public Blog Blog { get; set; }  
}
```  

### <a name="overriding-the-defaults"></a>Überschreiben der Standardwerte  

Sie können Parameter für Fremdschlüssel, die nicht in der-Klasse enthalten sind, ändern, indem Sie der-Parameter Methode den Pfad zur Primary Key-Eigenschaft bereitstellen.  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

Wenn Sie nicht über eine Navigations Eigenschaft für die abhängige Entität verfügen (d. h. No Post. Blog-Eigenschaft) Anschließend können Sie die Association-Methode verwenden, um das andere Ende der Beziehung zu identifizieren und dann die Parameter zu konfigurieren, die den einzelnen Schlüsseleigenschaften entsprechen.  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a>Parallelitätstoken  

Die gespeicherten Prozeduren Update und DELETE müssen möglicherweise auch die Parallelität behandeln:  

- Wenn die Entität Parallelitäts Token enthält, kann die gespeicherte Prozedur optional über einen Output-Parameter verfügen, der die Anzahl der aktualisierten/gelöschten Zeilen (betroffene Zeilen) zurückgibt. Ein solcher Parameter muss mithilfe der rowsaffectedparameter-Methode konfiguriert werden.  
Standardmäßig verwendet EF den Rückgabewert von ExecuteNonQuery, um zu bestimmen, wie viele Zeilen betroffen sind. Das Angeben eines betroffenen Rows-Ausgabe Parameters ist nützlich, wenn Sie eine Logik in Ihrem SPROC ausführen, die dazu führt, dass der Rückgabewert von ExecuteNonQuery beim Ende der Ausführung falsch ist (aus EF-Sicht).  
- Für jedes Parallelitäts Token gibt es einen Parameter mit dem Namen ** \<property_name\> _Original** (z. b. Timestamp_Original). Diesem wird der ursprüngliche Wert dieser Eigenschaft – der Wert, wenn er aus der Datenbank abgefragt wird.  
    - Parallelitäts Token, die von der Datenbank berechnet werden – z. b. Zeitstempel – verfügen nur über einen ursprünglichen value-Parameter.  
    - Nicht berechnete Eigenschaften, die als Parallelitäts Token festgelegt werden, verfügen ebenfalls über einen Parameter für den neuen Wert in der Aktualisierungs Prozedur. Dabei werden die Benennungs Konventionen verwendet, die bereits für neue Werte erläutert wurden. Ein Beispiel für ein solches Token wäre die Verwendung einer Blog-URL als Parallelitäts Token. der neue Wert ist erforderlich, da dies durch Ihren Code auf einen neuen Wert aktualisiert werden kann (im Gegensatz zu einem Timestamp-Token, das nur von der Datenbank aktualisiert wird).  

Dies ist eine Beispiel Klasse und eine gespeicherte Aktualisierungs Prozedur mit einem Zeitstempel-Parallelitäts Token.  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
  [Timestamp]
  public byte[] Timestamp { get; set; }
}
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max),
  @Timestamp_Original rowversion  
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId AND [Timestamp] = @Timestamp_Original
```  

Im folgenden finden Sie eine Beispiel Klasse und eine gespeicherte Aktualisierungs Prozedur mit einem nicht berechneten Parallelitäts Token.  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  [ConcurrencyCheck]
  public string Url { get; set; }  
}
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max),
  @Url_Original nvarchar(max),
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId AND [Url] = @Url_Original
```  

### <a name="overriding-the-defaults"></a>Überschreiben der Standardwerte  

Optional können Sie den Parameter "betroffene Zeilen" einführen.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

Für berechnete Parallelitäts Token für die Datenbank – Wenn nur der ursprüngliche Wert übergeben wird – können Sie einfach den Standardparameter Umbenennungs Mechanismus verwenden, um den Parameter für den ursprünglichen Wert umzubenennen.  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

Bei nicht berechneten Parallelitäts Token – wobei sowohl der ursprüngliche als auch der neue Wert übergeben werden – können Sie eine Überladung des Parameters verwenden, mit der Sie für jeden Parameter einen Namen angeben können.  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a>m:n-Beziehungen  

Wir verwenden die folgenden Klassen als Beispiel in diesem Abschnitt.  

``` csharp
public class Post  
{  
  public int PostId { get; set; }  
  public string Title { get; set; }  
  public string Content { get; set; }  

  public List<Tag> Tags { get; set; }  
}  

public class Tag  
{  
  public int TagId { get; set; }  
  public string TagName { get; set; }  

  public List<Post> Posts { get; set; }  
}
```  

Viele zu viele Beziehungen können gespeicherten Prozeduren mit der folgenden Syntax zugeordnet werden.  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

Wenn keine andere Konfiguration bereitgestellt wird, wird standardmäßig die folgende Form der gespeicherten Prozedur verwendet.  

- Zwei gespeicherte Prozeduren namens ** \<type_one\> \<type_two\> _INSERT** und ** \<type_one\> \<type_two\> _Delete** (z. b. PostTag_Insert und PostTag_Delete).  
- Bei den Parametern handelt es sich um die Schlüsselwerte für jeden Typ. Der Name jedes Parameters, der ** \<type_name\> _ \<property_name\> ** ist (z. b. Post_PostId und Tag_TagId).

Im folgenden finden Sie Beispiel gespeicherte Prozeduren zum Einfügen und aktualisieren.  

``` SQL
CREATE PROCEDURE [dbo].[PostTag_Insert]  
  @Post_PostId int,  
  @Tag_TagId int  
AS  
  INSERT INTO [dbo].[Post_Tags] (Post_PostId, Tag_TagId)   
  VALUES (@Post_PostId, @Tag_TagId)
CREATE PROCEDURE [dbo].[PostTag_Delete]  
  @Post_PostId int,  
  @Tag_TagId int  
AS  
  DELETE FROM [dbo].[Post_Tags]    
  WHERE Post_PostId = @Post_PostId AND Tag_TagId = @Tag_TagId
```  

### <a name="overriding-the-defaults"></a>Überschreiben der Standardwerte  

Die Prozedur-und Parameternamen können auf ähnliche Weise wie gespeicherte Prozeduren für Entitäten konfiguriert werden.  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.HasName("add_post_tag")  
                   .LeftKeyParameter(p => p.PostId, "post_id")  
                   .RightKeyParameter(t => t.TagId, "tag_id"))  
     .Delete(d => d.HasName("remove_post_tag")  
                   .LeftKeyParameter(p => p.PostId, "post_id")  
                   .RightKeyParameter(t => t.TagId, "tag_id")));
```  
