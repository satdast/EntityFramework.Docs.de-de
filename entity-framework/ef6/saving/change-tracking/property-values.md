---
title: Arbeiten mit Eigenschafts Werten EF6
description: Arbeiten mit Eigenschafts Werten in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/property-values
ms.openlocfilehash: deb110df1dbb2b433b54b98a684bc06e696c4351
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064444"
---
# <a name="working-with-property-values"></a>Arbeiten mit Eigenschafts Werten
In den meisten Fällen übernimmt Entity Framework die Nachverfolgung des Zustands, der ursprünglichen Werte und der aktuellen Werte der Eigenschaften der Entitäts Instanzen. Es gibt jedoch einige Fälle, z. b. getrennte Szenarios, in denen Sie die Informationen anzeigen oder bearbeiten möchten, die EF über die Eigenschaften verfügt. Die in diesem Thema dargestellten Techniken gelten jeweils für Modelle, die mit Code First und dem EF-Designer erstellt wurden.  

Entity Framework verfolgt zwei Werte für jede Eigenschaft einer nach verfolgten Entität nach. Der aktuelle Wert ist, wie der Name zeigt, der aktuelle Wert der-Eigenschaft in der Entität. Der ursprüngliche Wert ist der Wert, den die Eigenschaft hatte, als die Entität von der Datenbank abgefragt oder an den Kontext angefügt wurde.  

Es gibt zwei allgemeine Mechanismen zum Arbeiten mit Eigenschafts Werten:  

- Der Wert einer einzelnen Eigenschaft kann mithilfe der-Eigenschaften Methode in einer stark typisierten Weise abgerufen werden.  
- Werte für alle Eigenschaften einer Entität können in ein dbpropertyvalues-Objekt eingelesen werden. Dbpropertyvalues fungiert dann als Wörterbuch ähnliches Objekt, um das Lesen und Festlegen von Eigenschafts Werten zuzulassen. Die Werte in einem dbpropertyvalues-Objekt können aus Werten in einem anderen dbpropertyvalues-Objekt oder aus Werten in einem anderen Objekt, z. b. einer anderen Kopie der Entität oder einem einfachen Datenübertragungs Objekt (Data Transfer Object, dto), festgelegt werden.  

In den folgenden Abschnitten sind Beispiele für die Verwendung der beiden oben genannten Mechanismen aufgeführt.  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a>Festlegen und Festlegen des aktuellen oder ursprünglichen Werts einer einzelnen Eigenschaft  

Im folgenden Beispiel wird gezeigt, wie der aktuelle Wert einer Eigenschaft gelesen und dann auf einen neuen Wert festgelegt werden kann:  

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

Verwenden Sie die Eigenschaft OriginalValue anstelle der Eigenschaft CurrentValue, um den ursprünglichen Wert zu lesen oder festzulegen.  

Beachten Sie, dass der zurückgegebene Wert als "Object" typisiert wird, wenn eine Zeichenfolge verwendet wird, um den Namen der Eigenschaft anzugeben. Auf der anderen Seite ist der zurückgegebene Wert stark typisiert, wenn ein Lambda-Ausdruck verwendet wird.  

Wenn Sie den Eigenschafts Wert auf diese Weise festlegen, wird die Eigenschaft nur als geändert markiert, wenn der neue Wert vom alten Wert abweicht.  

Wenn ein Eigenschafts Wert auf diese Weise festgelegt wird, wird die Änderung automatisch erkannt, auch wenn autodetectchanges deaktiviert ist.  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a>Der aktuelle Wert einer nicht zugeordneten Eigenschaft wird erhalten und festgelegt.  

Der aktuelle Wert einer Eigenschaft, die nicht der Datenbank zugeordnet ist, kann ebenfalls gelesen werden. Ein Beispiel für eine nicht zugeordnete Eigenschaft könnte eine rsslink-Eigenschaft im Blog sein. Dieser Wert kann basierend auf der BlogId berechnet werden und muss daher nicht in der Datenbank gespeichert werden. Beispiel:  

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

Der aktuelle Wert kann auch festgelegt werden, wenn die-Eigenschaft einen Setter verfügbar macht.  

Das Lesen der Werte nicht zugeordneter Eigenschaften ist nützlich, wenn Entity Framework Überprüfung nicht zugeordneter Eigenschaften durchgeführt wird. Aus demselben Grund können aktuelle Werte gelesen und für Eigenschaften von Entitäten festgelegt werden, die derzeit nicht durch den Kontext verfolgt werden. Beispiel:  

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

Beachten Sie, dass die ursprünglichen Werte für nicht zugeordnete Eigenschaften oder für Eigenschaften von Entitäten, die nicht vom Kontext nachverfolgt werden, nicht verfügbar sind.  

## <a name="checking-whether-a-property-is-marked-as-modified"></a>Es wird überprüft, ob eine Eigenschaft als geändert markiert ist.  

Im folgenden Beispiel wird gezeigt, wie Sie überprüfen können, ob eine einzelne Eigenschaft als geändert gekennzeichnet ist:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

Die Werte der geänderten Eigenschaften werden als Aktualisierungen an die Datenbank gesendet, wenn SaveChanges aufgerufen wird.  

##  <a name="marking-a-property-as-modified"></a>Markieren einer Eigenschaft als geändert  

Das folgende Beispiel zeigt, wie Sie erzwingen können, dass eine einzelne Eigenschaft als geändert markiert wird:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

Wenn eine Eigenschaft als geändert markiert wird, wird ein Update für die-Eigenschaft an die Datenbank gesendet, wenn SaveChanges aufgerufen wird, auch wenn der aktuelle Wert der-Eigenschaft mit dem ursprünglichen Wert übereinstimmt.  

Es ist derzeit nicht möglich, eine einzelne Eigenschaft zurückzusetzen, sodass Sie nicht geändert wird, nachdem Sie als geändert markiert wurde. Dies wird in einer zukünftigen Version unterstützt.  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a>Lesen aktueller, ursprünglicher und Daten Bankwerte für alle Eigenschaften einer Entität  

Das folgende Beispiel zeigt, wie die aktuellen Werte, die ursprünglichen Werte und die Werte in der Datenbank für alle zugeordneten Eigenschaften einer Entität gelesen werden.  

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

Die aktuellen Werte sind die Werte, die die Eigenschaften der Entität derzeit enthalten. Bei den ursprünglichen Werten handelt es sich um die Werte, die aus der Datenbank gelesen wurden, als die Entität abgefragt wurde. Bei den Daten bankwerten handelt es sich um die Werte, die aktuell in der Datenbank gespeichert sind. Das übertragen der Daten Bankwerte ist hilfreich, wenn sich die Werte in der Datenbank möglicherweise seit der Abfrage der Entität geändert haben, z. b. Wenn ein anderer Benutzer eine gleichzeitige Bearbeitung der Datenbank durchgeführt hat.  

## <a name="setting-current-or-original-values-from-another-object"></a>Festlegen aktueller oder ursprünglicher Werte aus einem anderen Objekt  

Die aktuellen oder ursprünglichen Werte einer nach verfolgten Entität können aktualisiert werden, indem Werte aus einem anderen Objekt kopiert werden. Beispiel:  

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

Wenn Sie den obigen Code ausführen, wird Folgendes gedruckt:  

```console
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

Diese Technik wird manchmal beim Aktualisieren einer Entität mit Werten verwendet, die von einem Dienst-oder Client in einer n-Tier-Anwendung abgerufen werden. Beachten Sie, dass das verwendete Objekt nicht denselben Typ wie die Entität aufweisen muss, solange es über Eigenschaften verfügt, deren Namen mit denen der Entität übereinstimmen. Im obigen Beispiel wird eine Instanz von blogdto verwendet, um die ursprünglichen Werte zu aktualisieren.  

Beachten Sie, dass nur Eigenschaften, die auf verschiedene Werte festgelegt sind, wenn Sie aus dem anderen Objekt kopiert werden, als geändert gekennzeichnet werden.  

## <a name="setting-current-or-original-values-from-a-dictionary"></a>Festlegen aktueller oder ursprünglicher Werte aus einem Wörterbuch  

Die aktuellen oder ursprünglichen Werte einer nach verfolgten Entität können aktualisiert werden, indem Werte aus einem Wörterbuch oder einer anderen Datenstruktur kopiert werden. Beispiel:  

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

Verwenden Sie die OriginalValues-Eigenschaft anstelle der CurrentValues-Eigenschaft, um die ursprünglichen Werte festzulegen.  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a>Festlegen aktueller oder ursprünglicher Werte aus einem Wörterbuch mithilfe der-Eigenschaft  

Eine Alternative zur Verwendung von CurrentValues oder OriginalValues, wie oben gezeigt, besteht darin, die-Eigenschaften Methode zum Festlegen des Werts der einzelnen Eigenschaften zu verwenden. Dies ist vorzuziehen, wenn Sie die Werte komplexer Eigenschaften festlegen müssen. Beispiel:  

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

Im obigen Beispiel erfolgt der Zugriff auf komplexe Eigenschaften mithilfe von punktierten Namen. Weitere Möglichkeiten, um auf komplexe Eigenschaften zuzugreifen, finden Sie in den beiden Abschnitten weiter unten in diesem Thema speziell zu komplexen Eigenschaften.  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a>Erstellen eines geklonten Objekts mit aktuellen, ursprünglichen oder Daten bankwerten  

Das von CurrentValues, OriginalValues oder getdatabasevalues zurückgegebene dbpropertyvalues-Objekt kann verwendet werden, um einen Klon der Entität zu erstellen. Dieser Klon enthält die Eigenschaftswerte aus dem dbpropertyvalues-Objekt, das zur Erstellung verwendet wird. Beispiel:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

Beachten Sie, dass das zurückgegebene Objekt nicht die Entität ist und nicht vom Kontext nachverfolgt wird. Für das zurückgegebene Objekt sind auch keine Beziehungen auf andere Objekte festgelegt.  

Das geklonte Objekt kann nützlich sein, um Probleme im Zusammenhang mit gleichzeitigen Aktualisierungen der Datenbank zu beheben. Dies gilt insbesondere, wenn eine Benutzeroberfläche verwendet wird, die eine Datenbindung an Objekte eines bestimmten Typs einschließt.  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a>Erhalten und Festlegen der aktuellen oder ursprünglichen Werte komplexer Eigenschaften  

Der Wert eines gesamten komplexen Objekts kann mithilfe der-Eigenschaften Methode gelesen und festgelegt werden, so wie es für eine primitive Eigenschaft möglich ist. Außerdem können Sie einen Drilldown in das komplexe Objekt durchführen und die Eigenschaften des Objekts oder sogar ein geschieetes Objekt lesen oder festlegen. Im Folgenden finden Sie einige Beispiele:  

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

Verwenden Sie die OriginalValue-Eigenschaft anstelle der CurrentValue-Eigenschaft, um einen ursprünglichen Wert zu erhalten oder festzulegen.  

Beachten Sie, dass entweder die-Eigenschaft oder die ComplexProperty-Methode für den Zugriff auf eine komplexe Eigenschaft verwendet werden kann. Allerdings muss die ComplexProperty-Methode verwendet werden, wenn Sie einen Drilldown in das komplexe Objekt mit zusätzlichen Eigenschafts-oder ComplexProperty-aufrufen durchführen möchten.  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a>Verwenden von dbpropertyvalues zum Zugreifen auf komplexe Eigenschaften  

Wenn Sie "CurrentValues", "OriginalValues" oder "getdatabasevalues" verwenden, um alle aktuellen, ursprünglichen oder Daten Bankwerte für eine Entität zu erhalten, werden die Werte aller komplexen Eigenschaften als geduckte dbpropertyvalues-Objekte zurückgegeben. Diese-Objekte können dann verwendet werden, um Werte für das komplexe Objekt zu erhalten. Mit der folgenden Methode werden z. b. die Werte aller Eigenschaften gedruckt, einschließlich der Werte aller komplexen Eigenschaften und der in der Tabelle komplexen komplexen Eigenschaften.  

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

Um alle aktuellen Eigenschaftswerte auszugeben, wird die-Methode wie folgt aufgerufen:  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
