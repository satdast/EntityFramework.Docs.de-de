---
title: Code First gespeicherte Prozeduren INSERT, Update und DELETE-EF6
description: Code First gespeicherte Prozeduren INSERT, Update und DELETE in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/cud-stored-procedures
ms.openlocfilehash: 749bcc9faaf92ae6555c37facb8de31d877d1260
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065185"
---
# <a name="code-first-insert-update-and-delete-stored-procedures"></a><span data-ttu-id="3fff3-103">Code First gespeicherte Prozeduren INSERT, Update und DELETE</span><span class="sxs-lookup"><span data-stu-id="3fff3-103">Code First Insert, Update, and Delete Stored Procedures</span></span>
> [!NOTE]
> <span data-ttu-id="3fff3-104">**Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="3fff3-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="3fff3-105">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="3fff3-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="3fff3-106">Standardmäßig konfiguriert Code First alle Entitäten so, dass INSERT-, Update-und DELETE-Befehle mithilfe des direkten Tabellen Zugriffs durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="3fff3-106">By default, Code First will configure all entities to perform insert, update and delete commands using direct table access.</span></span> <span data-ttu-id="3fff3-107">Beginnend mit EF6 können Sie das Code First Modell so konfigurieren, dass es gespeicherte Prozeduren für einige oder alle Entitäten im Modell verwendet.</span><span class="sxs-lookup"><span data-stu-id="3fff3-107">Starting in EF6 you can configure your Code First model to use stored procedures for some or all entities in your model.</span></span>  

## <a name="basic-entity-mapping"></a><span data-ttu-id="3fff3-108">Grundlegende Entitäts Zuordnung</span><span class="sxs-lookup"><span data-stu-id="3fff3-108">Basic Entity Mapping</span></span>  

<span data-ttu-id="3fff3-109">Sie können gespeicherte Prozeduren für INSERT, Update und DELETE mithilfe der fließend-API verwenden.</span><span class="sxs-lookup"><span data-stu-id="3fff3-109">You can opt into using stored procedures for insert, update and delete using the Fluent API.</span></span>  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

<span data-ttu-id="3fff3-110">Dies führt dazu, dass Code First einige Konventionen verwenden, um die erwartete Form der gespeicherten Prozeduren in der Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="3fff3-110">Doing this will cause Code First to use some conventions to build the expected shape of the stored procedures in the database.</span></span>  

- <span data-ttu-id="3fff3-111">Drei gespeicherte Prozeduren namens \*\* \<type_name\> _INSERT**, \*\* \<type_name\> _Update** und \*\* \<type_name\> _Delete\*\* (z. b. Blog_Insert, Blog_Update und Blog_Delete).</span><span class="sxs-lookup"><span data-stu-id="3fff3-111">Three stored procedures named **\<type_name\>_Insert**, **\<type_name\>_Update** and **\<type_name\>_Delete** (for example, Blog_Insert, Blog_Update and Blog_Delete).</span></span>  
- <span data-ttu-id="3fff3-112">Parameter Namen entsprechen den Eigenschaftsnamen.</span><span class="sxs-lookup"><span data-stu-id="3fff3-112">Parameter names correspond to the property names.</span></span>  
  > [!NOTE]
  > <span data-ttu-id="3fff3-113">Wenn Sie hascolumnname () oder das Column-Attribut verwenden, um die Spalte für eine bestimmte Eigenschaft umzubenennen, wird dieser Name für Parameter anstelle des Eigenschafts namens verwendet.</span><span class="sxs-lookup"><span data-stu-id="3fff3-113">If you use HasColumnName() or the Column attribute to rename the column for a given property then this name is used for parameters instead of the property name.</span></span>  
- <span data-ttu-id="3fff3-114">**Die gespeicherte Prozedur INSERT** verfügt über einen Parameter für jede Eigenschaft, mit Ausnahme derjenigen, die als vom Store generiert (Identität oder berechnet) gekennzeichnet sind.</span><span class="sxs-lookup"><span data-stu-id="3fff3-114">**The insert stored procedure** will have a parameter for every property, except for those marked as store generated (identity or computed).</span></span> <span data-ttu-id="3fff3-115">Die gespeicherte Prozedur sollte ein Resultset mit einer Spalte für jede vom Speicher generierte Eigenschaft zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="3fff3-115">The stored procedure should return a result set with a column for each store generated property.</span></span>  
- <span data-ttu-id="3fff3-116">**Die gespeicherte Update Prozedur** verfügt über einen Parameter für jede Eigenschaft, mit Ausnahme derjenigen, die mit einem vom Speicher generierten Muster "berechnet" gekennzeichnet sind.</span><span class="sxs-lookup"><span data-stu-id="3fff3-116">**The update stored procedure** will have a parameter for every property, except for those marked with a store generated pattern of 'Computed'.</span></span> <span data-ttu-id="3fff3-117">Einige Parallelitäts Token erfordern einen Parameter für den ursprünglichen Wert. Weitere Informationen finden Sie im Abschnitt Parallelitäts *Token* weiter unten.</span><span class="sxs-lookup"><span data-stu-id="3fff3-117">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span> <span data-ttu-id="3fff3-118">Die gespeicherte Prozedur sollte ein Resultset mit einer Spalte für jede berechnete Eigenschaft zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="3fff3-118">The stored procedure should return a result set with a column for each computed property.</span></span>  
- <span data-ttu-id="3fff3-119">**Die gespeicherte DELETE-Prozedur** muss über einen Parameter für den Schlüsselwert der Entität verfügen (oder mehrere Parameter, wenn die Entität über einen zusammengesetzten Schlüssel verfügt).</span><span class="sxs-lookup"><span data-stu-id="3fff3-119">**The delete stored procedure** should have a parameter for the key value of the entity (or multiple parameters if the entity has a composite key).</span></span> <span data-ttu-id="3fff3-120">Außerdem sollte die Delete-Prozedur auch über Parameter für alle unabhängigen Zuordnungs Fremdschlüssel in der Ziel Tabelle verfügen (Beziehungen, die keine entsprechenden Fremdschlüssel Eigenschaften haben, die in der Entität deklariert sind).</span><span class="sxs-lookup"><span data-stu-id="3fff3-120">Additionally, the delete procedure should also have parameters for any independent association foreign keys on the target table (relationships that do not have corresponding foreign key properties declared in the entity).</span></span> <span data-ttu-id="3fff3-121">Einige Parallelitäts Token erfordern einen Parameter für den ursprünglichen Wert. Weitere Informationen finden Sie im Abschnitt Parallelitäts *Token* weiter unten.</span><span class="sxs-lookup"><span data-stu-id="3fff3-121">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span>  

<span data-ttu-id="3fff3-122">Verwenden Sie die folgende Klasse als Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3fff3-122">Using the following class as an example:</span></span>  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

<span data-ttu-id="3fff3-123">Die gespeicherten Standard Prozeduren lauten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="3fff3-123">The default stored procedures would be:</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="3fff3-124">Überschreiben der Standardwerte</span><span class="sxs-lookup"><span data-stu-id="3fff3-124">Overriding the Defaults</span></span>  

<span data-ttu-id="3fff3-125">Sie können einen Teil oder alle der standardmäßig konfigurierten Einstellungen außer Kraft setzen.</span><span class="sxs-lookup"><span data-stu-id="3fff3-125">You can override part or all of what was configured by default.</span></span>  

<span data-ttu-id="3fff3-126">Sie können den Namen einer oder mehrerer gespeicherter Prozeduren ändern.</span><span class="sxs-lookup"><span data-stu-id="3fff3-126">You can change the name of one or more stored procedures.</span></span> <span data-ttu-id="3fff3-127">In diesem Beispiel wird nur die gespeicherte Update Prozedur umbenannt.</span><span class="sxs-lookup"><span data-stu-id="3fff3-127">This example renames the update stored procedure only.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

<span data-ttu-id="3fff3-128">In diesem Beispiel werden alle drei gespeicherten Prozeduren umbenannt.</span><span class="sxs-lookup"><span data-stu-id="3fff3-128">This example renames all three stored procedures.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

<span data-ttu-id="3fff3-129">In diesen Beispielen werden die Aufrufe miteinander verkettet, aber Sie können auch die Lambda-Block Syntax verwenden.</span><span class="sxs-lookup"><span data-stu-id="3fff3-129">In these examples the calls are chained together, but you can also use lambda block syntax.</span></span>  

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

<span data-ttu-id="3fff3-130">In diesem Beispiel wird der-Parameter für die BlogId-Eigenschaft der gespeicherten Update-Prozedur umbenannt.</span><span class="sxs-lookup"><span data-stu-id="3fff3-130">This example renames the parameter for the BlogId property on the update stored procedure.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

<span data-ttu-id="3fff3-131">Diese Aufrufe sind alle verkettbar und zusammensetzbar.</span><span class="sxs-lookup"><span data-stu-id="3fff3-131">These calls are all chainable and composable.</span></span> <span data-ttu-id="3fff3-132">Es folgt ein Beispiel, das alle drei gespeicherten Prozeduren und ihre Parameter umbenennt.</span><span class="sxs-lookup"><span data-stu-id="3fff3-132">Here is an example that renames all three stored procedures and their parameters.</span></span>  

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

<span data-ttu-id="3fff3-133">Sie können auch den Namen der Spalten im Resultset ändern, das von der Datenbank generierte Werte enthält.</span><span class="sxs-lookup"><span data-stu-id="3fff3-133">You can also change the name of the columns in the result set that contains database generated values.</span></span>  

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

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a><span data-ttu-id="3fff3-134">Beziehungen ohne Fremdschlüssel in der Klasse (unabhängige Zuordnungen)</span><span class="sxs-lookup"><span data-stu-id="3fff3-134">Relationships Without a Foreign Key in the Class (Independent Associations)</span></span>  

<span data-ttu-id="3fff3-135">Wenn eine Fremdschlüssel Eigenschaft in der Klassendefinition enthalten ist, kann der entsprechende Parameter auf dieselbe Weise wie jede andere Eigenschaft umbenannt werden.</span><span class="sxs-lookup"><span data-stu-id="3fff3-135">When a foreign key property is included in the class definition, the corresponding parameter can be renamed in the same way as any other property.</span></span> <span data-ttu-id="3fff3-136">Wenn eine Beziehung ohne Fremdschlüssel Eigenschaft in der-Klasse vorhanden ist, lautet der Standardparameter Name \*\* \<navigation_property_name\> _ \<primary_key_name\> \*\*.</span><span class="sxs-lookup"><span data-stu-id="3fff3-136">When a relationship exists without a foreign key property in the class, the default parameter name is **\<navigation_property_name\>_\<primary_key_name\>**.</span></span>  

<span data-ttu-id="3fff3-137">Beispielsweise würden die folgenden Klassendefinitionen dazu führen, dass in den gespeicherten Prozeduren ein Blog_BlogId-Parameter erwartet wird, um Beiträge einzufügen und zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="3fff3-137">For example, the following class definitions would result in a Blog_BlogId parameter being expected in the stored procedures to insert and update Posts.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="3fff3-138">Überschreiben der Standardwerte</span><span class="sxs-lookup"><span data-stu-id="3fff3-138">Overriding the Defaults</span></span>  

<span data-ttu-id="3fff3-139">Sie können Parameter für Fremdschlüssel, die nicht in der-Klasse enthalten sind, ändern, indem Sie der-Parameter Methode den Pfad zur Primary Key-Eigenschaft bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="3fff3-139">You can change parameters for foreign keys that are not included in the class by supplying the path to the primary key property to the Parameter method.</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

<span data-ttu-id="3fff3-140">Wenn Sie nicht über eine Navigations Eigenschaft für die abhängige Entität verfügen (d. h.</span><span class="sxs-lookup"><span data-stu-id="3fff3-140">If you don’t have a navigation property on the dependent entity (i.e</span></span> <span data-ttu-id="3fff3-141">No Post. Blog-Eigenschaft) Anschließend können Sie die Association-Methode verwenden, um das andere Ende der Beziehung zu identifizieren und dann die Parameter zu konfigurieren, die den einzelnen Schlüsseleigenschaften entsprechen.</span><span class="sxs-lookup"><span data-stu-id="3fff3-141">no Post.Blog property) then you can use the Association method to identify the other end of the relationship and then configure the parameters that correspond to each of the key property(s).</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a><span data-ttu-id="3fff3-142">Parallelitätstoken</span><span class="sxs-lookup"><span data-stu-id="3fff3-142">Concurrency Tokens</span></span>  

<span data-ttu-id="3fff3-143">Die gespeicherten Prozeduren Update und DELETE müssen möglicherweise auch die Parallelität behandeln:</span><span class="sxs-lookup"><span data-stu-id="3fff3-143">Update and delete stored procedures may also need to deal with concurrency:</span></span>  

- <span data-ttu-id="3fff3-144">Wenn die Entität Parallelitäts Token enthält, kann die gespeicherte Prozedur optional über einen Output-Parameter verfügen, der die Anzahl der aktualisierten/gelöschten Zeilen (betroffene Zeilen) zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="3fff3-144">If the entity contains concurrency tokens, the stored procedure can optionally have an output parameter that returns the number of rows updated/deleted (rows affected).</span></span> <span data-ttu-id="3fff3-145">Ein solcher Parameter muss mithilfe der rowsaffectedparameter-Methode konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="3fff3-145">Such a parameter must be configured using the RowsAffectedParameter method.</span></span>  
<span data-ttu-id="3fff3-146">Standardmäßig verwendet EF den Rückgabewert von ExecuteNonQuery, um zu bestimmen, wie viele Zeilen betroffen sind.</span><span class="sxs-lookup"><span data-stu-id="3fff3-146">By default EF uses the return value from ExecuteNonQuery to determine how many rows were affected.</span></span> <span data-ttu-id="3fff3-147">Das Angeben eines betroffenen Rows-Ausgabe Parameters ist nützlich, wenn Sie eine Logik in Ihrem SPROC ausführen, die dazu führt, dass der Rückgabewert von ExecuteNonQuery beim Ende der Ausführung falsch ist (aus EF-Sicht).</span><span class="sxs-lookup"><span data-stu-id="3fff3-147">Specifying a rows affected output parameter is useful if you perform any logic in your sproc that would result in the return value of ExecuteNonQuery being incorrect (from EF's perspective) at the end of execution.</span></span>  
- <span data-ttu-id="3fff3-148">Für jedes Parallelitäts Token gibt es einen Parameter mit dem Namen \*\* \<property_name\> _Original\*\* (z. b. Timestamp_Original).</span><span class="sxs-lookup"><span data-stu-id="3fff3-148">For each concurrency token there will be a parameter named **\<property_name\>_Original** (for example, Timestamp_Original ).</span></span> <span data-ttu-id="3fff3-149">Diesem wird der ursprüngliche Wert dieser Eigenschaft – der Wert, wenn er aus der Datenbank abgefragt wird.</span><span class="sxs-lookup"><span data-stu-id="3fff3-149">This will be passed the original value of this property – the value when queried from the database.</span></span>  
    - <span data-ttu-id="3fff3-150">Parallelitäts Token, die von der Datenbank berechnet werden – z. b. Zeitstempel – verfügen nur über einen ursprünglichen value-Parameter.</span><span class="sxs-lookup"><span data-stu-id="3fff3-150">Concurrency tokens that are computed by the database – such as timestamps – will only have an original value parameter.</span></span>  
    - <span data-ttu-id="3fff3-151">Nicht berechnete Eigenschaften, die als Parallelitäts Token festgelegt werden, verfügen ebenfalls über einen Parameter für den neuen Wert in der Aktualisierungs Prozedur.</span><span class="sxs-lookup"><span data-stu-id="3fff3-151">Non-computed properties that are set as concurrency tokens will also have a parameter for the new value in the update procedure.</span></span> <span data-ttu-id="3fff3-152">Dabei werden die Benennungs Konventionen verwendet, die bereits für neue Werte erläutert wurden.</span><span class="sxs-lookup"><span data-stu-id="3fff3-152">This uses the naming conventions already discussed for new values.</span></span> <span data-ttu-id="3fff3-153">Ein Beispiel für ein solches Token wäre die Verwendung einer Blog-URL als Parallelitäts Token. der neue Wert ist erforderlich, da dies durch Ihren Code auf einen neuen Wert aktualisiert werden kann (im Gegensatz zu einem Timestamp-Token, das nur von der Datenbank aktualisiert wird).</span><span class="sxs-lookup"><span data-stu-id="3fff3-153">An example of such a token would be using a Blog's URL as a concurrency token, the new value is required because this can be updated to a new value by your code (unlike a Timestamp token which is only updated by the database).</span></span>  

<span data-ttu-id="3fff3-154">Dies ist eine Beispiel Klasse und eine gespeicherte Aktualisierungs Prozedur mit einem Zeitstempel-Parallelitäts Token.</span><span class="sxs-lookup"><span data-stu-id="3fff3-154">This is an example class and update stored procedure with a timestamp concurrency token.</span></span>  

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

<span data-ttu-id="3fff3-155">Im folgenden finden Sie eine Beispiel Klasse und eine gespeicherte Aktualisierungs Prozedur mit einem nicht berechneten Parallelitäts Token.</span><span class="sxs-lookup"><span data-stu-id="3fff3-155">Here is an example class and update stored procedure with non-computed concurrency token.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="3fff3-156">Überschreiben der Standardwerte</span><span class="sxs-lookup"><span data-stu-id="3fff3-156">Overriding the Defaults</span></span>  

<span data-ttu-id="3fff3-157">Optional können Sie den Parameter "betroffene Zeilen" einführen.</span><span class="sxs-lookup"><span data-stu-id="3fff3-157">You can optionally introduce a rows affected parameter.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

<span data-ttu-id="3fff3-158">Für berechnete Parallelitäts Token für die Datenbank – Wenn nur der ursprüngliche Wert übergeben wird – können Sie einfach den Standardparameter Umbenennungs Mechanismus verwenden, um den Parameter für den ursprünglichen Wert umzubenennen.</span><span class="sxs-lookup"><span data-stu-id="3fff3-158">For database computed concurrency tokens – where only the original value is passed – you can just use the standard parameter renaming mechanism to rename the parameter for the original value.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

<span data-ttu-id="3fff3-159">Bei nicht berechneten Parallelitäts Token – wobei sowohl der ursprüngliche als auch der neue Wert übergeben werden – können Sie eine Überladung des Parameters verwenden, mit der Sie für jeden Parameter einen Namen angeben können.</span><span class="sxs-lookup"><span data-stu-id="3fff3-159">For non-computed concurrency tokens – where both the original and new value are passed – you can use an overload of Parameter that allows you to supply a name for each parameter.</span></span>  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a><span data-ttu-id="3fff3-160">m:n-Beziehungen</span><span class="sxs-lookup"><span data-stu-id="3fff3-160">Many to Many Relationships</span></span>  

<span data-ttu-id="3fff3-161">Wir verwenden die folgenden Klassen als Beispiel in diesem Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="3fff3-161">We’ll use the following classes as an example in this section.</span></span>  

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

<span data-ttu-id="3fff3-162">Viele zu viele Beziehungen können gespeicherten Prozeduren mit der folgenden Syntax zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="3fff3-162">Many to many relationships can be mapped to stored procedures with the following syntax.</span></span>  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

<span data-ttu-id="3fff3-163">Wenn keine andere Konfiguration bereitgestellt wird, wird standardmäßig die folgende Form der gespeicherten Prozedur verwendet.</span><span class="sxs-lookup"><span data-stu-id="3fff3-163">If no other configuration is supplied then the following stored procedure shape is used by default.</span></span>  

- <span data-ttu-id="3fff3-164">Zwei gespeicherte Prozeduren namens \*\* \<type_one\> \<type_two\> _INSERT\*\* und \*\* \<type_one\> \<type_two\> _Delete\*\* (z. b. PostTag_Insert und PostTag_Delete).</span><span class="sxs-lookup"><span data-stu-id="3fff3-164">Two stored procedures named **\<type_one\>\<type_two\>_Insert** and **\<type_one\>\<type_two\>_Delete** (for example, PostTag_Insert and PostTag_Delete).</span></span>  
- <span data-ttu-id="3fff3-165">Bei den Parametern handelt es sich um die Schlüsselwerte für jeden Typ.</span><span class="sxs-lookup"><span data-stu-id="3fff3-165">The parameters will be the key value(s) for each type.</span></span> <span data-ttu-id="3fff3-166">Der Name jedes Parameters, der \*\* \<type_name\> _ \<property_name\> \*\* ist (z. b. Post_PostId und Tag_TagId).</span><span class="sxs-lookup"><span data-stu-id="3fff3-166">The name of each parameter being **\<type_name\>_\<property_name\>** (for example, Post_PostId and Tag_TagId).</span></span>

<span data-ttu-id="3fff3-167">Im folgenden finden Sie Beispiel gespeicherte Prozeduren zum Einfügen und aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="3fff3-167">Here are example insert and update stored procedures.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="3fff3-168">Überschreiben der Standardwerte</span><span class="sxs-lookup"><span data-stu-id="3fff3-168">Overriding the Defaults</span></span>  

<span data-ttu-id="3fff3-169">Die Prozedur-und Parameternamen können auf ähnliche Weise wie gespeicherte Prozeduren für Entitäten konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="3fff3-169">The procedure and parameter names can be configured in a similar way to entity stored procedures.</span></span>  

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
