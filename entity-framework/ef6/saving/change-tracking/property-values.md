---
title: Arbeiten mit Eigenschafts Werten EF6
description: Arbeiten mit Eigenschafts Werten in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: e3278b4b-9378-4fdb-923d-f64d80aaae70
uid: ef6/saving/change-tracking/property-values
ms.openlocfilehash: 53c0829920c77768eae26d23c3da7d0553f44b62
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619969"
---
# <a name="working-with-property-values"></a><span data-ttu-id="fc1b9-103">Arbeiten mit Eigenschafts Werten</span><span class="sxs-lookup"><span data-stu-id="fc1b9-103">Working with property values</span></span>
<span data-ttu-id="fc1b9-104">In den meisten Fällen übernimmt Entity Framework die Nachverfolgung des Zustands, der ursprünglichen Werte und der aktuellen Werte der Eigenschaften der Entitäts Instanzen.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-104">For the most part Entity Framework will take care of tracking the state, original values, and current values of the properties of your entity instances.</span></span> <span data-ttu-id="fc1b9-105">Es gibt jedoch einige Fälle, z. b. getrennte Szenarios, in denen Sie die Informationen anzeigen oder bearbeiten möchten, die EF über die Eigenschaften verfügt.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-105">However, there may be some cases - such as disconnected scenarios - where you want to view or manipulate the information EF has about the properties.</span></span> <span data-ttu-id="fc1b9-106">Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="fc1b9-107">Entity Framework verfolgt zwei Werte für jede Eigenschaft einer nach verfolgten Entität nach.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-107">Entity Framework keeps track of two values for each property of a tracked entity.</span></span> <span data-ttu-id="fc1b9-108">Der aktuelle Wert ist, wie der Name zeigt, der aktuelle Wert der-Eigenschaft in der Entität.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-108">The current value is, as the name indicates, the current value of the property in the entity.</span></span> <span data-ttu-id="fc1b9-109">Der ursprüngliche Wert ist der Wert, den die Eigenschaft hatte, als die Entität von der Datenbank abgefragt oder an den Kontext angefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-109">The original value is the value that the property had when the entity was queried from the database or attached to the context.</span></span>  

<span data-ttu-id="fc1b9-110">Es gibt zwei allgemeine Mechanismen zum Arbeiten mit Eigenschafts Werten:</span><span class="sxs-lookup"><span data-stu-id="fc1b9-110">There are two general mechanisms for working with property values:</span></span>  

- <span data-ttu-id="fc1b9-111">Der Wert einer einzelnen Eigenschaft kann mithilfe der-Eigenschaften Methode in einer stark typisierten Weise abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-111">The value of a single property can be obtained in a strongly typed way using the Property method.</span></span>  
- <span data-ttu-id="fc1b9-112">Werte für alle Eigenschaften einer Entität können in ein dbpropertyvalues-Objekt eingelesen werden.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-112">Values for all properties of an entity can be read into a DbPropertyValues object.</span></span> <span data-ttu-id="fc1b9-113">Dbpropertyvalues fungiert dann als Wörterbuch ähnliches Objekt, um das Lesen und Festlegen von Eigenschafts Werten zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-113">DbPropertyValues then acts as a dictionary-like object to allow property values to be read and set.</span></span> <span data-ttu-id="fc1b9-114">Die Werte in einem dbpropertyvalues-Objekt können aus Werten in einem anderen dbpropertyvalues-Objekt oder aus Werten in einem anderen Objekt, z. b. einer anderen Kopie der Entität oder einem einfachen Datenübertragungs Objekt (Data Transfer Object, dto), festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-114">The values in a DbPropertyValues object can be set from values in another DbPropertyValues object or from values in some other object, such as another copy of the entity or a simple data transfer object (DTO).</span></span>  

<span data-ttu-id="fc1b9-115">In den folgenden Abschnitten sind Beispiele für die Verwendung der beiden oben genannten Mechanismen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-115">The sections below show examples of using both of the above mechanisms.</span></span>  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a><span data-ttu-id="fc1b9-116">Festlegen und Festlegen des aktuellen oder ursprünglichen Werts einer einzelnen Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="fc1b9-116">Getting and setting the current or original value of an individual property</span></span>  

<span data-ttu-id="fc1b9-117">Im folgenden Beispiel wird gezeigt, wie der aktuelle Wert einer Eigenschaft gelesen und dann auf einen neuen Wert festgelegt werden kann:</span><span class="sxs-lookup"><span data-stu-id="fc1b9-117">The example below shows how the current value of a property can be read and then set to a new value:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(3);

    // Read the current value of the Name property
    string currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;

    // Set the Name property to a new value
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";

    // Read the current value of the Name property using a string for the property name
    object currentName2 = context.Entry(blog).Property("Name").CurrentValue;

    // Set the Name property to a new value using a string for the property name
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

<span data-ttu-id="fc1b9-118">Verwenden Sie die Eigenschaft OriginalValue anstelle der Eigenschaft CurrentValue, um den ursprünglichen Wert zu lesen oder festzulegen.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-118">Use the OriginalValue property instead of the CurrentValue property to read or set the original value.</span></span>  

<span data-ttu-id="fc1b9-119">Beachten Sie, dass der zurückgegebene Wert als "Object" typisiert wird, wenn eine Zeichenfolge verwendet wird, um den Namen der Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-119">Note that the returned value is typed as “object” when a string is used to specify the property name.</span></span> <span data-ttu-id="fc1b9-120">Auf der anderen Seite ist der zurückgegebene Wert stark typisiert, wenn ein Lambda-Ausdruck verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-120">On the other hand, the returned value is strongly typed if a lambda expression is used.</span></span>  

<span data-ttu-id="fc1b9-121">Wenn Sie den Eigenschafts Wert auf diese Weise festlegen, wird die Eigenschaft nur als geändert markiert, wenn der neue Wert vom alten Wert abweicht.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-121">Setting the property value like this will only mark the property as modified if the new value is different from the old value.</span></span>  

<span data-ttu-id="fc1b9-122">Wenn ein Eigenschafts Wert auf diese Weise festgelegt wird, wird die Änderung automatisch erkannt, auch wenn autodetectchanges deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-122">When a property value is set in this way the change is automatically detected even if AutoDetectChanges is turned off.</span></span>  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a><span data-ttu-id="fc1b9-123">Der aktuelle Wert einer nicht zugeordneten Eigenschaft wird erhalten und festgelegt.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-123">Getting and setting the current value of an unmapped property</span></span>  

<span data-ttu-id="fc1b9-124">Der aktuelle Wert einer Eigenschaft, die nicht der Datenbank zugeordnet ist, kann ebenfalls gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-124">The current value of a property that is not mapped to the database can also be read.</span></span> <span data-ttu-id="fc1b9-125">Ein Beispiel für eine nicht zugeordnete Eigenschaft könnte eine rsslink-Eigenschaft im Blog sein.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-125">An example of an unmapped property could be an RssLink property on Blog.</span></span> <span data-ttu-id="fc1b9-126">Dieser Wert kann basierend auf der BlogId berechnet werden und muss daher nicht in der Datenbank gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-126">This value may be calculated based on the BlogId, and therefore doesn't need to be stored in the database.</span></span> <span data-ttu-id="fc1b9-127">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fc1b9-127">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    // Read the current value of an unmapped property
    var rssLink = context.Entry(blog).Property(p => p.RssLink).CurrentValue;

    // Use a string to specify the property name
    var rssLinkAgain = context.Entry(blog).Property("RssLink").CurrentValue;
}
```  

<span data-ttu-id="fc1b9-128">Der aktuelle Wert kann auch festgelegt werden, wenn die-Eigenschaft einen Setter verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-128">The current value can also be set if the property exposes a setter.</span></span>  

<span data-ttu-id="fc1b9-129">Das Lesen der Werte nicht zugeordneter Eigenschaften ist nützlich, wenn Entity Framework Überprüfung nicht zugeordneter Eigenschaften durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-129">Reading the values of unmapped properties is useful when performing Entity Framework validation of unmapped properties.</span></span> <span data-ttu-id="fc1b9-130">Aus demselben Grund können aktuelle Werte gelesen und für Eigenschaften von Entitäten festgelegt werden, die derzeit nicht durch den Kontext verfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-130">For the same reason current values can be read and set for properties of entities that are not currently being tracked by the context.</span></span> <span data-ttu-id="fc1b9-131">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fc1b9-131">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Create an entity that is not being tracked
    var blog = new Blog { Name = "ADO.NET Blog" };

    // Read and set the current value of Name as before
    var currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";
    var currentName2 = context.Entry(blog).Property("Name").CurrentValue;
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

<span data-ttu-id="fc1b9-132">Beachten Sie, dass die ursprünglichen Werte für nicht zugeordnete Eigenschaften oder für Eigenschaften von Entitäten, die nicht vom Kontext nachverfolgt werden, nicht verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-132">Note that original values are not available for unmapped properties or for properties of entities that are not being tracked by the context.</span></span>  

## <a name="checking-whether-a-property-is-marked-as-modified"></a><span data-ttu-id="fc1b9-133">Es wird überprüft, ob eine Eigenschaft als geändert markiert ist.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-133">Checking whether a property is marked as modified</span></span>  

<span data-ttu-id="fc1b9-134">Im folgenden Beispiel wird gezeigt, wie Sie überprüfen können, ob eine einzelne Eigenschaft als geändert gekennzeichnet ist:</span><span class="sxs-lookup"><span data-stu-id="fc1b9-134">The example below shows how to check whether or not an individual property is marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

<span data-ttu-id="fc1b9-135">Die Werte der geänderten Eigenschaften werden als Aktualisierungen an die Datenbank gesendet, wenn SaveChanges aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-135">The values of modified properties are sent as updates to the database when SaveChanges is called.</span></span>  

##  <a name="marking-a-property-as-modified"></a><span data-ttu-id="fc1b9-136">Markieren einer Eigenschaft als geändert</span><span class="sxs-lookup"><span data-stu-id="fc1b9-136">Marking a property as modified</span></span>  

<span data-ttu-id="fc1b9-137">Das folgende Beispiel zeigt, wie Sie erzwingen können, dass eine einzelne Eigenschaft als geändert markiert wird:</span><span class="sxs-lookup"><span data-stu-id="fc1b9-137">The example below shows how to force an individual property to be marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

<span data-ttu-id="fc1b9-138">Wenn eine Eigenschaft als geändert markiert wird, wird ein Update für die-Eigenschaft an die Datenbank gesendet, wenn SaveChanges aufgerufen wird, auch wenn der aktuelle Wert der-Eigenschaft mit dem ursprünglichen Wert übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-138">Marking a property as modified forces an update to be send to the database for the property when SaveChanges is called even if the current value of the property is the same as its original value.</span></span>  

<span data-ttu-id="fc1b9-139">Es ist derzeit nicht möglich, eine einzelne Eigenschaft zurückzusetzen, sodass Sie nicht geändert wird, nachdem Sie als geändert markiert wurde.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-139">It is not currently possible to reset an individual property to be not modified after it has been marked as modified.</span></span> <span data-ttu-id="fc1b9-140">Dies wird in einer zukünftigen Version unterstützt.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-140">This is something we plan to support in a future release.</span></span>  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a><span data-ttu-id="fc1b9-141">Lesen aktueller, ursprünglicher und Daten Bankwerte für alle Eigenschaften einer Entität</span><span class="sxs-lookup"><span data-stu-id="fc1b9-141">Reading current, original, and database values for all properties of an entity</span></span>  

<span data-ttu-id="fc1b9-142">Das folgende Beispiel zeigt, wie die aktuellen Werte, die ursprünglichen Werte und die Werte in der Datenbank für alle zugeordneten Eigenschaften einer Entität gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-142">The example below shows how to read the current values, the original values, and the values actually in the database for all mapped properties of an entity.</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Make a modification to Name in the tracked entity
    blog.Name = "My Cool Blog";

    // Make a modification to Name in the database
    context.Database.SqlCommand("update dbo.Blogs set Name = 'My Boring Blog' where Id = 1");

    // Print out current, original, and database values
    Console.WriteLine("Current values:");
    PrintValues(context.Entry(blog).CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(context.Entry(blog).OriginalValues);

    Console.WriteLine("\nDatabase values:");
    PrintValues(context.Entry(blog).GetDatabaseValues());
}

public static void PrintValues(DbPropertyValues values)
{
    foreach (var propertyName in values.PropertyNames)
    {
        Console.WriteLine("Property {0} has value {1}",
                          propertyName, values[propertyName]);
    }
}
```  

<span data-ttu-id="fc1b9-143">Die aktuellen Werte sind die Werte, die die Eigenschaften der Entität derzeit enthalten.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-143">The current values are the values that the properties of the entity currently contain.</span></span> <span data-ttu-id="fc1b9-144">Bei den ursprünglichen Werten handelt es sich um die Werte, die aus der Datenbank gelesen wurden, als die Entität abgefragt wurde.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-144">The original values are the values that were read from the database when the entity was queried.</span></span> <span data-ttu-id="fc1b9-145">Bei den Daten bankwerten handelt es sich um die Werte, die aktuell in der Datenbank gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-145">The database values are the values as they are currently stored in the database.</span></span> <span data-ttu-id="fc1b9-146">Das übertragen der Daten Bankwerte ist hilfreich, wenn sich die Werte in der Datenbank möglicherweise seit der Abfrage der Entität geändert haben, z. b. Wenn ein anderer Benutzer eine gleichzeitige Bearbeitung der Datenbank durchgeführt hat.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-146">Getting the database values is useful when the values in the database may have changed since the entity was queried such as when a concurrent edit to the database has been made by another user.</span></span>  

## <a name="setting-current-or-original-values-from-another-object"></a><span data-ttu-id="fc1b9-147">Festlegen aktueller oder ursprünglicher Werte aus einem anderen Objekt</span><span class="sxs-lookup"><span data-stu-id="fc1b9-147">Setting current or original values from another object</span></span>  

<span data-ttu-id="fc1b9-148">Die aktuellen oder ursprünglichen Werte einer nach verfolgten Entität können aktualisiert werden, indem Werte aus einem anderen Objekt kopiert werden.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-148">The current or original values of a tracked entity can be updated by copying values from another object.</span></span> <span data-ttu-id="fc1b9-149">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fc1b9-149">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var coolBlog = new Blog { Id = 1, Name = "My Cool Blog" };
    var boringBlog = new BlogDto { Id = 1, Name = "My Boring Blog" };

    // Change the current and original values by copying the values from other objects
    var entry = context.Entry(blog);
    entry.CurrentValues.SetValues(coolBlog);
    entry.OriginalValues.SetValues(boringBlog);

    // Print out current and original values
    Console.WriteLine("Current values:");
    PrintValues(entry.CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(entry.OriginalValues);
}

public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```  

<span data-ttu-id="fc1b9-150">Wenn Sie den obigen Code ausführen, wird Folgendes gedruckt:</span><span class="sxs-lookup"><span data-stu-id="fc1b9-150">Running the code above will print out:</span></span>  

```console
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

<span data-ttu-id="fc1b9-151">Diese Technik wird manchmal beim Aktualisieren einer Entität mit Werten verwendet, die von einem Dienst-oder Client in einer n-Tier-Anwendung abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-151">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="fc1b9-152">Beachten Sie, dass das verwendete Objekt nicht denselben Typ wie die Entität aufweisen muss, solange es über Eigenschaften verfügt, deren Namen mit denen der Entität übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-152">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="fc1b9-153">Im obigen Beispiel wird eine Instanz von blogdto verwendet, um die ursprünglichen Werte zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-153">In the example above, an instance of BlogDTO is used to update the original values.</span></span>  

<span data-ttu-id="fc1b9-154">Beachten Sie, dass nur Eigenschaften, die auf verschiedene Werte festgelegt sind, wenn Sie aus dem anderen Objekt kopiert werden, als geändert gekennzeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-154">Note that only properties that are set to different values when copied from the other object will be marked as modified.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="fc1b9-155">Festlegen aktueller oder ursprünglicher Werte aus einem Wörterbuch</span><span class="sxs-lookup"><span data-stu-id="fc1b9-155">Setting current or original values from a dictionary</span></span>  

<span data-ttu-id="fc1b9-156">Die aktuellen oder ursprünglichen Werte einer nach verfolgten Entität können aktualisiert werden, indem Werte aus einem Wörterbuch oder einer anderen Datenstruktur kopiert werden.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-156">The current or original values of a tracked entity can be updated by copying values from a dictionary or some other data structure.</span></span> <span data-ttu-id="fc1b9-157">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fc1b9-157">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var newValues = new Dictionary\<string, object>
    {
        { "Name", "The New ADO.NET Blog" },
        { "Url", "blogs.msdn.com/adonet" },
    };

    var currentValues = context.Entry(blog).CurrentValues;

    foreach (var propertyName in newValues.Keys)
    {
        currentValues[propertyName] = newValues[propertyName];
    }

    PrintValues(currentValues);
}
```  

<span data-ttu-id="fc1b9-158">Verwenden Sie die OriginalValues-Eigenschaft anstelle der CurrentValues-Eigenschaft, um die ursprünglichen Werte festzulegen.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-158">Use the OriginalValues property instead of the CurrentValues property to set original values.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a><span data-ttu-id="fc1b9-159">Festlegen aktueller oder ursprünglicher Werte aus einem Wörterbuch mithilfe der-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="fc1b9-159">Setting current or original values from a dictionary using Property</span></span>  

<span data-ttu-id="fc1b9-160">Eine Alternative zur Verwendung von CurrentValues oder OriginalValues, wie oben gezeigt, besteht darin, die-Eigenschaften Methode zum Festlegen des Werts der einzelnen Eigenschaften zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-160">An alternative to using CurrentValues or OriginalValues as shown above is to use the Property method to set the value of each property.</span></span> <span data-ttu-id="fc1b9-161">Dies ist vorzuziehen, wenn Sie die Werte komplexer Eigenschaften festlegen müssen.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-161">This can be preferable when you need to set the values of complex properties.</span></span> <span data-ttu-id="fc1b9-162">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fc1b9-162">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    var newValues = new Dictionary\<string, object>
    {
        { "Name", "John Doe" },
        { "Location.City", "Redmond" },
        { "Location.State.Name", "Washington" },
        { "Location.State.Code", "WA" },
    };

    var entry = context.Entry(user);

    foreach (var propertyName in newValues.Keys)
    {
        entry.Property(propertyName).CurrentValue = newValues[propertyName];
    }
}
```  

<span data-ttu-id="fc1b9-163">Im obigen Beispiel erfolgt der Zugriff auf komplexe Eigenschaften mithilfe von punktierten Namen.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-163">In the example above complex properties are accessed using dotted names.</span></span> <span data-ttu-id="fc1b9-164">Weitere Möglichkeiten, um auf komplexe Eigenschaften zuzugreifen, finden Sie in den beiden Abschnitten weiter unten in diesem Thema speziell zu komplexen Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-164">For other ways to access complex properties see the two sections later in this topic specifically about complex properties.</span></span>  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="fc1b9-165">Erstellen eines geklonten Objekts mit aktuellen, ursprünglichen oder Daten bankwerten</span><span class="sxs-lookup"><span data-stu-id="fc1b9-165">Creating a cloned object containing current, original, or database values</span></span>  

<span data-ttu-id="fc1b9-166">Das von CurrentValues, OriginalValues oder getdatabasevalues zurückgegebene dbpropertyvalues-Objekt kann verwendet werden, um einen Klon der Entität zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-166">The DbPropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity.</span></span> <span data-ttu-id="fc1b9-167">Dieser Klon enthält die Eigenschaftswerte aus dem dbpropertyvalues-Objekt, das zur Erstellung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-167">This clone will contain the property values from the DbPropertyValues object used to create it.</span></span> <span data-ttu-id="fc1b9-168">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fc1b9-168">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

<span data-ttu-id="fc1b9-169">Beachten Sie, dass das zurückgegebene Objekt nicht die Entität ist und nicht vom Kontext nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-169">Note that the object returned is not the entity and is not being tracked by the context.</span></span> <span data-ttu-id="fc1b9-170">Für das zurückgegebene Objekt sind auch keine Beziehungen auf andere Objekte festgelegt.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-170">The returned object also does not have any relationships set to other objects.</span></span>  

<span data-ttu-id="fc1b9-171">Das geklonte Objekt kann nützlich sein, um Probleme im Zusammenhang mit gleichzeitigen Aktualisierungen der Datenbank zu beheben. Dies gilt insbesondere, wenn eine Benutzeroberfläche verwendet wird, die eine Datenbindung an Objekte eines bestimmten Typs einschließt.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-171">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially where a UI that involves data binding to objects of a certain type is being used.</span></span>  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a><span data-ttu-id="fc1b9-172">Erhalten und Festlegen der aktuellen oder ursprünglichen Werte komplexer Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="fc1b9-172">Getting and setting the current or original values of complex properties</span></span>  

<span data-ttu-id="fc1b9-173">Der Wert eines gesamten komplexen Objekts kann mithilfe der-Eigenschaften Methode gelesen und festgelegt werden, so wie es für eine primitive Eigenschaft möglich ist.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-173">The value of an entire complex object can be read and set using the Property method just as it can be for a primitive property.</span></span> <span data-ttu-id="fc1b9-174">Außerdem können Sie einen Drilldown in das komplexe Objekt durchführen und die Eigenschaften des Objekts oder sogar ein geschieetes Objekt lesen oder festlegen.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-174">In addition you can drill down into the complex object and read or set properties of that object, or even a nested object.</span></span> <span data-ttu-id="fc1b9-175">Hier sind einige Beispiele:</span><span class="sxs-lookup"><span data-stu-id="fc1b9-175">Here are some examples:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    // Get the Location complex object
    var location = context.Entry(user)
                       .Property(u => u.Location)
                       .CurrentValue;

    // Get the nested State complex object using chained calls
    var state1 = context.Entry(user)
                     .ComplexProperty(u => u.Location)
                     .Property(l => l.State)
                     .CurrentValue;

    // Get the nested State complex object using a single lambda expression
    var state2 = context.Entry(user)
                     .Property(u => u.Location.State)
                     .CurrentValue;

    // Get the nested State complex object using a dotted string
    var state3 = context.Entry(user)
                     .Property("Location.State")
                     .CurrentValue;

    // Get the value of the Name property on the nested State complex object using chained calls
    var name1 = context.Entry(user)
                       .ComplexProperty(u => u.Location)
                       .ComplexProperty(l => l.State)
                       .Property(s => s.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a single lambda expression
    var name2 = context.Entry(user)
                       .Property(u => u.Location.State.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a dotted string
    var name3 = context.Entry(user)
                       .Property("Location.State.Name")
                       .CurrentValue;
}
```  

<span data-ttu-id="fc1b9-176">Verwenden Sie die OriginalValue-Eigenschaft anstelle der CurrentValue-Eigenschaft, um einen ursprünglichen Wert zu erhalten oder festzulegen.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-176">Use the OriginalValue property instead of the CurrentValue property to get or set an original value.</span></span>  

<span data-ttu-id="fc1b9-177">Beachten Sie, dass entweder die-Eigenschaft oder die ComplexProperty-Methode für den Zugriff auf eine komplexe Eigenschaft verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-177">Note that either the Property or the ComplexProperty method can be used to access a complex property.</span></span> <span data-ttu-id="fc1b9-178">Allerdings muss die ComplexProperty-Methode verwendet werden, wenn Sie einen Drilldown in das komplexe Objekt mit zusätzlichen Eigenschafts-oder ComplexProperty-aufrufen durchführen möchten.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-178">However, the ComplexProperty method must be used if you wish to drill down into the complex object with additional Property or ComplexProperty calls.</span></span>  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a><span data-ttu-id="fc1b9-179">Verwenden von dbpropertyvalues zum Zugreifen auf komplexe Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="fc1b9-179">Using DbPropertyValues to access complex properties</span></span>  

<span data-ttu-id="fc1b9-180">Wenn Sie "CurrentValues", "OriginalValues" oder "getdatabasevalues" verwenden, um alle aktuellen, ursprünglichen oder Daten Bankwerte für eine Entität zu erhalten, werden die Werte aller komplexen Eigenschaften als geduckte dbpropertyvalues-Objekte zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-180">When you use CurrentValues, OriginalValues, or GetDatabaseValues to get all the current, original, or database values for an entity, the values of any complex properties are returned as nested DbPropertyValues objects.</span></span> <span data-ttu-id="fc1b9-181">Diese-Objekte können dann verwendet werden, um Werte für das komplexe Objekt zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-181">These nested objects can then be used to get values of the complex object.</span></span> <span data-ttu-id="fc1b9-182">Mit der folgenden Methode werden z. b. die Werte aller Eigenschaften gedruckt, einschließlich der Werte aller komplexen Eigenschaften und der in der Tabelle komplexen komplexen Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="fc1b9-182">For example, the following method will print out the values of all properties, including values of any complex properties and nested complex properties.</span></span>  

``` csharp
public static void WritePropertyValues(string parentPropertyName, DbPropertyValues propertyValues)
{
    foreach (var propertyName in propertyValues.PropertyNames)
    {
        var nestedValues = propertyValues[propertyName] as DbPropertyValues;
        if (nestedValues != null)
        {
            WritePropertyValues(parentPropertyName + propertyName + ".", nestedValues);
        }
        else
        {
            Console.WriteLine("Property {0}{1} has value {2}",
                              parentPropertyName, propertyName,
                              propertyValues[propertyName]);
        }
    }
}
```  

<span data-ttu-id="fc1b9-183">Um alle aktuellen Eigenschaftswerte auszugeben, wird die-Methode wie folgt aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="fc1b9-183">To print out all current property values the method would be called like this:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
