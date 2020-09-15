---
title: Benutzerdefinierte Code First Konventionen-EF6
description: Benutzerdefinierte Code First Konventionen in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/custom
ms.openlocfilehash: 19f22d311438b8c7d3ac9d270017f3ea4907efda
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90074028"
---
# <a name="custom-code-first-conventions"></a><span data-ttu-id="691db-103">Benutzerdefinierte Code First Konventionen</span><span class="sxs-lookup"><span data-stu-id="691db-103">Custom Code First Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="691db-104">**Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="691db-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="691db-105">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="691db-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="691db-106">Wenn Sie Code First verwenden, wird das Modell aus ihren Klassen mithilfe eines Satzes von Konventionen berechnet.</span><span class="sxs-lookup"><span data-stu-id="691db-106">When using Code First your model is calculated from your classes using a set of conventions.</span></span> <span data-ttu-id="691db-107">Die standardmäßigen [Code First Konventionen](xref:ef6/modeling/code-first/conventions/built-in) bestimmen Dinge, wie z. b. welche Eigenschaft zum Primärschlüssel einer Entität wird, den Namen der Tabelle, der eine Entität zugeordnet ist, und welche Genauigkeit und Dezimalstellen eine Dezimal Spalte Standardmäßig besitzt.</span><span class="sxs-lookup"><span data-stu-id="691db-107">The default [Code First Conventions](xref:ef6/modeling/code-first/conventions/built-in) determine things like which property becomes the primary key of an entity, the name of the table an entity maps to, and what precision and scale a decimal column has by default.</span></span>

<span data-ttu-id="691db-108">Manchmal sind diese Standard Konventionen nicht ideal für Ihr Modell, und Sie müssen Sie umgehen, indem Sie viele einzelne Entitäten mithilfe von Daten Anmerkungen oder der flüssigen API konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="691db-108">Sometimes these default conventions are not ideal for your model, and you have to work around them by configuring many individual entities using Data Annotations or the Fluent API.</span></span> <span data-ttu-id="691db-109">Mit benutzerdefinierten Code First Konventionen können Sie eigene Konventionen definieren, die Konfigurations Standardwerte für das Modell bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="691db-109">Custom Code First Conventions let you define your own conventions that provide configuration defaults for your model.</span></span> <span data-ttu-id="691db-110">In dieser exemplarischen Vorgehensweise werden die unterschiedlichen Typen von benutzerdefinierten Konventionen erläutert und erläutert, wie diese erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="691db-110">In this walkthrough, we will explore the different types of custom conventions and how to create each of them.</span></span>


## <a name="model-based-conventions"></a><span data-ttu-id="691db-111">Modellbasierte Konventionen</span><span class="sxs-lookup"><span data-stu-id="691db-111">Model-Based Conventions</span></span>

<span data-ttu-id="691db-112">Auf dieser Seite wird die dbmodelbuilder-API für benutzerdefinierte Konventionen behandelt.</span><span class="sxs-lookup"><span data-stu-id="691db-112">This page covers the DbModelBuilder API for custom conventions.</span></span> <span data-ttu-id="691db-113">Diese API sollte für die Erstellung der meisten benutzerdefinierten Konventionen ausreichen.</span><span class="sxs-lookup"><span data-stu-id="691db-113">This API should be sufficient for authoring most custom conventions.</span></span> <span data-ttu-id="691db-114">Es gibt jedoch auch die Möglichkeit, modellbasierte Konventionen zu verfassen, die das endgültige Modell nach der Erstellung verändern, um erweiterte Szenarien zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="691db-114">However, there is also the ability to author model-based conventions - conventions that manipulate the final model once it is created - to handle advanced scenarios.</span></span> <span data-ttu-id="691db-115">Weitere Informationen finden Sie unter [modellbasierte Konventionen](xref:ef6/modeling/code-first/conventions/model).</span><span class="sxs-lookup"><span data-stu-id="691db-115">For more information, see [Model-Based Conventions](xref:ef6/modeling/code-first/conventions/model).</span></span>

 

## <a name="our-model"></a><span data-ttu-id="691db-116">Unser Modell</span><span class="sxs-lookup"><span data-stu-id="691db-116">Our Model</span></span>

<span data-ttu-id="691db-117">Zunächst definieren wir ein einfaches Modell, das wir mit unseren Konventionen verwenden können.</span><span class="sxs-lookup"><span data-stu-id="691db-117">Let's start by defining a simple model that we can use with our conventions.</span></span> <span data-ttu-id="691db-118">Fügen Sie dem Projekt die folgenden Klassen hinzu.</span><span class="sxs-lookup"><span data-stu-id="691db-118">Add the following classes to your project.</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;

    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }
    }

    public class Product
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public decimal? Price { get; set; }
        public DateTime? ReleaseDate { get; set; }
        public ProductCategory Category { get; set; }
    }

    public class ProductCategory
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public List<Product> Products { get; set; }
    }
```

 

## <a name="introducing-custom-conventions"></a><span data-ttu-id="691db-119">Einführung in benutzerdefinierte Konventionen</span><span class="sxs-lookup"><span data-stu-id="691db-119">Introducing Custom Conventions</span></span>

<span data-ttu-id="691db-120">Wir schreiben eine Konvention, die jede Eigenschaft mit dem Namen Key als Primärschlüssel für den Entitätstyp konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="691db-120">Let’s write a convention that configures any property named Key to be the primary key for its entity type.</span></span>

<span data-ttu-id="691db-121">Konventionen sind für den Modell-Generator aktiviert, auf den durch das Überschreiben von onmodelcreating im Kontext zugegriffen werden kann.</span><span class="sxs-lookup"><span data-stu-id="691db-121">Conventions are enabled on the model builder, which can be accessed by overriding OnModelCreating in the context.</span></span> <span data-ttu-id="691db-122">Aktualisieren Sie die productcontext-Klasse wie folgt:</span><span class="sxs-lookup"><span data-stu-id="691db-122">Update the ProductContext class as follows:</span></span>

``` csharp
    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Properties()
                        .Where(p => p.Name == "Key")
                        .Configure(p => p.IsKey());
        }
    }
```

<span data-ttu-id="691db-123">Nun wird jede Eigenschaft in unserem Modell mit dem Namen Key als Primärschlüssel einer beliebigen Entität konfiguriert, von der Sie gehört.</span><span class="sxs-lookup"><span data-stu-id="691db-123">Now, any property in our model named Key will be configured as the primary key of whatever entity its part of.</span></span>

<span data-ttu-id="691db-124">Wir könnten unsere Konventionen auch spezifischer gestalten, indem wir nach dem Typ der Eigenschaft filtern, die wir konfigurieren werden:</span><span class="sxs-lookup"><span data-stu-id="691db-124">We could also make our conventions more specific by filtering on the type of property that we are going to configure:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

<span data-ttu-id="691db-125">Dadurch werden alle Eigenschaften, die als "Key" bezeichnet werden, als Primärschlüssel der Entität konfiguriert, aber nur, wenn es sich um eine ganze Zahl handelt.</span><span class="sxs-lookup"><span data-stu-id="691db-125">This will configure all properties called Key to be the primary key of their entity, but only if they are an integer.</span></span>

<span data-ttu-id="691db-126">Ein interessantes Feature der IsKey-Methode ist, dass es Additiv ist.</span><span class="sxs-lookup"><span data-stu-id="691db-126">An interesting feature of the IsKey method is that it is additive.</span></span> <span data-ttu-id="691db-127">Dies bedeutet, wenn Sie "IsKey" für mehrere Eigenschaften anrufen und alle Teil eines zusammengesetzten Schlüssels werden.</span><span class="sxs-lookup"><span data-stu-id="691db-127">Which means that if you call IsKey on multiple properties and they will all become part of a composite key.</span></span> <span data-ttu-id="691db-128">Der einzige Nachteil hierfür ist, dass Sie beim Angeben mehrerer Eigenschaften für einen Schlüssel auch eine Bestellung für diese Eigenschaften angeben müssen.</span><span class="sxs-lookup"><span data-stu-id="691db-128">The one caveat for this is that when you specify multiple properties for a key you must also specify an order for those properties.</span></span> <span data-ttu-id="691db-129">Dies können Sie erreichen, indem Sie die hascolumnorder-Methode wie folgt aufrufen:</span><span class="sxs-lookup"><span data-stu-id="691db-129">You can do this by calling the HasColumnOrder method like below:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

<span data-ttu-id="691db-130">Dieser Code konfiguriert die Typen in unserem Modell so, dass Sie über einen zusammengesetzten Schlüssel verfügen, der aus der int-Schlüssel Spalte und der Zeichen folgen Name-Spalte besteht.</span><span class="sxs-lookup"><span data-stu-id="691db-130">This code will configure the types in our model to have a composite key consisting of the int Key column and the string Name column.</span></span> <span data-ttu-id="691db-131">Wenn das Modell im Designer angezeigt wird, sieht es wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="691db-131">If we view the model in the designer it would look like this:</span></span>

![zusammengesetzter Schlüssel](~/ef6/media/compositekey.png)

<span data-ttu-id="691db-133">Ein weiteres Beispiel für Eigenschafts Konventionen besteht darin, alle DateTime-Eigenschaften in meinem Modell so zu konfigurieren, dass Sie dem datetime2-Typ in SQL Server anstelle von DateTime zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="691db-133">Another example of property conventions is to configure all DateTime properties in my model to map to the datetime2 type in SQL Server instead of datetime.</span></span> <span data-ttu-id="691db-134">Dies kann mit folgendem erreicht werden:</span><span class="sxs-lookup"><span data-stu-id="691db-134">You can achieve this with the following:</span></span>

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a><span data-ttu-id="691db-135">Konventionen-Klassen</span><span class="sxs-lookup"><span data-stu-id="691db-135">Convention Classes</span></span>

<span data-ttu-id="691db-136">Eine andere Möglichkeit zum Definieren von Konventionen besteht darin, eine Konvention-Klasse zu verwenden, um Ihre Konvention zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="691db-136">Another way of defining conventions is to use a Convention Class to encapsulate your convention.</span></span> <span data-ttu-id="691db-137">Wenn Sie eine Konventionen-Klasse verwenden, erstellen Sie einen Typ, der von der Konvention-Klasse im Namespace System. Data. Entity. modelconfiguration. Conventions erbt.</span><span class="sxs-lookup"><span data-stu-id="691db-137">When using a Convention Class then you create a type that inherits from the Convention class in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>

<span data-ttu-id="691db-138">Wir können eine Konvention-Klasse mit der datetime2-Konvention erstellen, die wir zuvor gezeigt haben, indem wir die folgenden Schritte durchführen:</span><span class="sxs-lookup"><span data-stu-id="691db-138">We can create a Convention Class with the datetime2 convention that we showed earlier by doing the following:</span></span>

``` csharp
    public class DateTime2Convention : Convention
    {
        public DateTime2Convention()
        {
            this.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));        
        }
    }
```

<span data-ttu-id="691db-139">Damit EF diese Konvention verwenden kann, fügen Sie es der Conventions-Auflistung in onmodelcreating hinzu. Wenn Sie die exemplarische Vorgehensweise verwendet haben, sieht die exemplarische Vorgehensweise wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="691db-139">To tell EF to use this convention you add it to the Conventions collection in OnModelCreating, which if you’ve been following along with the walkthrough will look like this:</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

<span data-ttu-id="691db-140">Wie Sie sehen können, fügen wir der Konventionen-Auflistung eine Instanz der Konvention hinzu.</span><span class="sxs-lookup"><span data-stu-id="691db-140">As you can see we add an instance of our convention to the conventions collection.</span></span> <span data-ttu-id="691db-141">Die Vererbung von der Konvention stellt eine bequeme Möglichkeit dar, Konventionen zwischen Teams und Projekten zu gruppieren und zu teilen.</span><span class="sxs-lookup"><span data-stu-id="691db-141">Inheriting from Convention provides a convenient way of grouping and sharing conventions across teams or projects.</span></span> <span data-ttu-id="691db-142">Sie könnten z. b. über eine Klassenbibliothek mit einem gemeinsamen Satz von Konventionen verfügen, die von all ihren Organisationen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="691db-142">You could, for example, have a class library with a common set of conventions that all of your organizations projects use.</span></span>

 

## <a name="custom-attributes"></a><span data-ttu-id="691db-143">Benutzerdefinierte Attribute</span><span class="sxs-lookup"><span data-stu-id="691db-143">Custom Attributes</span></span>

<span data-ttu-id="691db-144">Eine weitere großartige Verwendung von Konventionen besteht darin, neue Attribute für die Konfiguration eines Modells zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="691db-144">Another great use of conventions is to enable new attributes to be used when configuring a model.</span></span> <span data-ttu-id="691db-145">Um dies zu veranschaulichen, erstellen wir ein Attribut, das zum Markieren von Zeichen folgen Eigenschaften als nicht-Unicode verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="691db-145">To illustrate this, let’s create an attribute that we can use to mark String properties as non-Unicode.</span></span>

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

<span data-ttu-id="691db-146">Nun erstellen wir eine Konvention zum Anwenden dieses Attributs auf unser Modell:</span><span class="sxs-lookup"><span data-stu-id="691db-146">Now, let’s create a convention to apply this attribute to our model:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

<span data-ttu-id="691db-147">Mit dieser Konvention können wir das nonUnicode-Attribut zu einer unserer Zeichen folgen Eigenschaften hinzufügen. Dies bedeutet, dass die Spalte in der Datenbank als VARCHAR anstelle von nvarchar gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="691db-147">With this convention we can add the NonUnicode attribute to any of our string properties, which means the column in the database will be stored as varchar instead of nvarchar.</span></span>

<span data-ttu-id="691db-148">Diese Konvention sollte beachtet werden, wenn Sie das nicht-Unicode-Attribut nicht auf eine Zeichen folgen Eigenschaft setzen, sondern eine Ausnahme auslöst.</span><span class="sxs-lookup"><span data-stu-id="691db-148">One thing to note about this convention is that if you put the NonUnicode attribute on anything other than a string property then it will throw an exception.</span></span> <span data-ttu-id="691db-149">Dies liegt daran, dass isunicode nicht für einen anderen Typ als eine Zeichenfolge konfiguriert werden kann.</span><span class="sxs-lookup"><span data-stu-id="691db-149">It does this because you cannot configure IsUnicode on any type other than a string.</span></span> <span data-ttu-id="691db-150">Wenn dies der Fall ist, können Sie Ihre Konvention spezifischer gestalten, damit alles, was keine Zeichenfolge ist, herausgefiltert wird.</span><span class="sxs-lookup"><span data-stu-id="691db-150">If this happens, then you can make your convention more specific, so that it filters out anything that isn’t a string.</span></span>

<span data-ttu-id="691db-151">Obwohl die obige Konvention zum Definieren von benutzerdefinierten Attributen funktioniert, gibt es eine andere API, die viel einfacher zu verwenden ist, insbesondere, wenn Sie Eigenschaften aus der Attribut Klasse verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="691db-151">While the above convention works for defining custom attributes there is another API that can be much easier to use, especially when you want to use properties from the attribute class.</span></span>

<span data-ttu-id="691db-152">In diesem Beispiel aktualisieren wir das Attribut und ändern es in ein isunicode-Attribut. Daher sieht es wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="691db-152">For this example we are going to update our attribute and change it to an IsUnicode attribute, so it looks like this:</span></span>

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    internal class IsUnicode : Attribute
    {
        public bool Unicode { get; set; }

        public IsUnicode(bool isUnicode)
        {
            Unicode = isUnicode;
        }
    }
```

<span data-ttu-id="691db-153">Nachdem dies geschehen ist, können wir einen booleschen Wert für unser Attribut festlegen, um der Konvention mitzuteilen, ob eine Eigenschaft Unicode sein soll.</span><span class="sxs-lookup"><span data-stu-id="691db-153">Once we have this, we can set a bool on our attribute to tell the convention whether or not a property should be Unicode.</span></span> <span data-ttu-id="691db-154">Wir könnten dies in der Konvention durchführen, indem wir wie folgt auf die clrproperty der Konfigurations Klasse zugreifen:</span><span class="sxs-lookup"><span data-stu-id="691db-154">We could do this in the convention we have already by accessing the ClrProperty of the configuration class like this:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

<span data-ttu-id="691db-155">Dies ist leicht genug, aber es gibt eine kompaktere Möglichkeit, dies mit der-Methode der Conventions-API zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="691db-155">This is easy enough, but there is a more succinct way of achieving this by using the Having method of the conventions API.</span></span> <span data-ttu-id="691db-156">Die "has"-Methode hat einen Parameter vom Typ "Func &lt; PropertyInfo", "T", der &gt; PropertyInfo akzeptiert, identisch mit der Where-Methode, erwartet jedoch, dass ein-Objekt zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="691db-156">The Having method has a parameter of type Func&lt;PropertyInfo, T&gt; which accepts the PropertyInfo the same as the Where method, but is expected to return an object.</span></span> <span data-ttu-id="691db-157">Wenn das zurückgegebene Objekt NULL ist, wird die Eigenschaft nicht konfiguriert, d. h., Sie können Eigenschaften wie in der Where-Methode filtern, aber Sie unterscheiden sich darin, dass auch das zurückgegebene Objekt erfasst und an die Configure-Methode übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="691db-157">If the returned object is null then the property will not be configured, which means you can filter out properties with it just like Where, but it is different in that it will also capture the returned object and pass it to the Configure method.</span></span> <span data-ttu-id="691db-158">Dies funktioniert wie folgt:</span><span class="sxs-lookup"><span data-stu-id="691db-158">This works like the following:</span></span>

``` csharp
    modelBuilder.Properties()
                .Having(x =>x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

<span data-ttu-id="691db-159">Benutzerdefinierte Attribute sind nicht der einzige Grund für die Verwendung der Methode, die Sie bei der Konfiguration ihrer Typen oder Eigenschaften durch Filtern müssen.</span><span class="sxs-lookup"><span data-stu-id="691db-159">Custom attributes are not the only reason to use the Having method, it is useful anywhere that you need to reason about something that you are filtering on when configuring your types or properties.</span></span>

 

## <a name="configuring-types"></a><span data-ttu-id="691db-160">Konfigurieren von Typen</span><span class="sxs-lookup"><span data-stu-id="691db-160">Configuring Types</span></span>

<span data-ttu-id="691db-161">Bisher waren alle Konventionen für Eigenschaften vorgesehen, aber es gibt einen weiteren Bereich der Konventionen-API für die Konfiguration der Typen in Ihrem Modell.</span><span class="sxs-lookup"><span data-stu-id="691db-161">So far all of our conventions have been for properties, but there is another area of the conventions API for configuring the types in your model.</span></span> <span data-ttu-id="691db-162">Die Erfahrung ähnelt den Konventionen, die bisher aufgetreten sind, aber die Optionen in configure befinden sich in der Entität anstelle der Eigenschafts Ebene.</span><span class="sxs-lookup"><span data-stu-id="691db-162">The experience is similar to the conventions we have seen so far, but the options inside configure will be at the entity instead of property level.</span></span>

<span data-ttu-id="691db-163">Eine der Dinge, mit denen typebenenkonventionen sehr nützlich sein können, ist die Änderung der Benennungs Konvention für Tabellen, entweder für die Zuordnung zu einem vorhandenen Schema, das von EF default abweicht, oder um eine neue Datenbank mit einer anderen Benennungs Konvention zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="691db-163">One of the things that Type level conventions can be really useful for is changing the table naming convention, either to map to an existing schema that differs from the EF default or to create a new database with a different naming convention.</span></span> <span data-ttu-id="691db-164">Zu diesem Zweck benötigen wir zuerst eine Methode, die die TypeInfo für einen Typ in unserem Modell akzeptieren kann und den Tabellennamen für diesen Typ zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="691db-164">To do this we first need a method that can accept the TypeInfo for a type in our model and return what the table name for that type should be:</span></span>

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

<span data-ttu-id="691db-165">Diese Methode nimmt einen-Typ an und gibt eine Zeichenfolge zurück, die Kleinbuchstaben mit unterstrichen anstelle von "CamelCase" verwendet.</span><span class="sxs-lookup"><span data-stu-id="691db-165">This method takes a type and returns a string that uses lower case with underscores instead of CamelCase.</span></span> <span data-ttu-id="691db-166">In unserem Modell bedeutet dies, dass die ProductCategory-Klasse der Tabelle Product \_ Category anstelle von productcategories zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="691db-166">In our model this means that the ProductCategory class will be mapped to a table called product\_category instead of ProductCategories.</span></span>

<span data-ttu-id="691db-167">Sobald wir diese Methode haben, können wir Sie in einer Konvention wie der folgenden nennen:</span><span class="sxs-lookup"><span data-stu-id="691db-167">Once we have that method we can call it in a convention like this:</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

<span data-ttu-id="691db-168">Diese Konvention konfiguriert jeden Typ in unserem Modell so, dass er dem Tabellennamen zugeordnet wird, der von der GetTableName-Methode zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="691db-168">This convention configures every type in our model to map to the table name that is returned from our GetTableName method.</span></span> <span data-ttu-id="691db-169">Diese Konvention entspricht dem Aufrufen der ToTable-Methode für jede Entität im Modell mithilfe der flüssigen API.</span><span class="sxs-lookup"><span data-stu-id="691db-169">This convention is the equivalent to calling the ToTable method for each entity in the model using the Fluent API.</span></span>

<span data-ttu-id="691db-170">Beachten Sie hierbei Folgendes: Wenn Sie "ToTable" verwenden, wird die Zeichenfolge, die Sie als exakten Tabellennamen angeben, ohne die Pluralisierung übernommen, die normalerweise beim Bestimmen von Tabellennamen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="691db-170">One thing to note about this is that when you call ToTable EF will take the string that you provide as the exact table name, without any of the pluralization that it would normally do when determining table names.</span></span> <span data-ttu-id="691db-171">Aus diesem Grund ist der Tabellenname aus unserer Konvention die Produkt \_ Kategorie anstelle von Produkt \_ Kategorien.</span><span class="sxs-lookup"><span data-stu-id="691db-171">This is why the table name from our convention is product\_category instead of product\_categories.</span></span> <span data-ttu-id="691db-172">Wir können dies in unserer Konvention auflösen, indem wir den pluralisierungs Dienst selbst anrufen.</span><span class="sxs-lookup"><span data-stu-id="691db-172">We can resolve that in our convention by making a call to the pluralization service ourselves.</span></span>

<span data-ttu-id="691db-173">Im folgenden Code verwenden wir die Funktion zur [Abhängigkeitsauflösung](xref:ef6/fundamentals/configuring/dependency-resolution) , die in EF6 hinzugefügt wird, um den pluralisierungs Dienst abzurufen, den EF verwendet hätte, und den Tabellennamen zu pluralisieren.</span><span class="sxs-lookup"><span data-stu-id="691db-173">In the following code we will use the [Dependency Resolution](xref:ef6/fundamentals/configuring/dependency-resolution) feature added in EF6 to retrieve the pluralization service that EF would have used and pluralize our table name.</span></span>

``` csharp
    private string GetTableName(Type type)
    {
        var pluralizationService = DbConfiguration.DependencyResolver.GetService<IPluralizationService>();

        var result = pluralizationService.Pluralize(type.Name);

        result = Regex.Replace(result, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

> [!NOTE]
> <span data-ttu-id="691db-174">Bei der generischen Version von GetService handelt es sich um eine Erweiterungsmethode im System. Data. Entity. Infrastructure. dependencyresolution-Namespace. Sie müssen ihrem Kontext eine using-Anweisung hinzufügen, um Sie zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="691db-174">The generic version of GetService is an extension method in the System.Data.Entity.Infrastructure.DependencyResolution namespace, you will need to add a using statement to your context in order to use it.</span></span>

### <a name="totable-and-inheritance"></a><span data-ttu-id="691db-175">Zu Tabelle und Vererbung</span><span class="sxs-lookup"><span data-stu-id="691db-175">ToTable and Inheritance</span></span>

<span data-ttu-id="691db-176">Ein weiterer wichtiger Aspekt bei der Inhalts Zuordnung besteht darin, dass Sie die von EF verwendete Zuordnungs Strategie ändern können, wenn Sie einen Typ explizit einer bestimmten Tabelle zuordnen.</span><span class="sxs-lookup"><span data-stu-id="691db-176">Another important aspect of ToTable is that if you explicitly map a type to a given table, then you can alter the mapping strategy that EF will use.</span></span> <span data-ttu-id="691db-177">Wenn Sie für jeden Typ in einer Vererbungs Hierarchie "detable" aufzurufen und den Typnamen wie oben beschrieben als Namen der Tabelle übergeben, ändern Sie die standardmäßige Tabelle pro Hierarchie (TPH)-Mapping-Strategie in "Tabelle pro Typ" (TPT).</span><span class="sxs-lookup"><span data-stu-id="691db-177">If you call ToTable for every type in an inheritance hierarchy, passing the type name as the name of the table like we did above, then you will change the default Table-Per-Hierarchy (TPH) mapping strategy to Table-Per-Type (TPT).</span></span> <span data-ttu-id="691db-178">Die beste Möglichkeit, dies zu beschreiben, ist ein konkretes Beispiel:</span><span class="sxs-lookup"><span data-stu-id="691db-178">The best way to describe this is whith a concrete example:</span></span>

``` csharp
    public class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }

    public class Manager : Employee
    {
        public string SectionManaged { get; set; }
    }
```

<span data-ttu-id="691db-179">Standardmäßig werden sowohl der Employee als auch der Manager derselben Tabelle (Employees) in der Datenbank zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="691db-179">By default both employee and manager are mapped to the same table (Employees) in the database.</span></span> <span data-ttu-id="691db-180">Die Tabelle enthält sowohl Mitarbeiter als auch Manager mit einer diskriminatorspalte, die Aufschluss darüber gibt, welcher Instanztyp in den einzelnen Zeilen gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="691db-180">The table will contain both employees and managers with a discriminator column that will tell you what type of instance is stored in each row.</span></span> <span data-ttu-id="691db-181">Dies ist die TPH-Zuordnung, da eine einzelne Tabelle für die Hierarchie vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="691db-181">This is TPH mapping as there is a single table for the hierarchy.</span></span> <span data-ttu-id="691db-182">Wenn Sie jedoch "detable" für beide "Classe" aufzurufen, wird jeder Typ stattdessen einer eigenen Tabelle zugeordnet, auch als "TPT" bezeichnet, da jeder Typ eine eigene Tabelle hat.</span><span class="sxs-lookup"><span data-stu-id="691db-182">However, if you call ToTable on both classe then each type will instead be mapped to its own table, also known as TPT since each type has its own table.</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

<span data-ttu-id="691db-183">Der obige Code wird einer Tabellenstruktur zugeordnet, die wie folgt aussieht:</span><span class="sxs-lookup"><span data-stu-id="691db-183">The code above will map to a table structure that looks like the following:</span></span>

![TPT-Beispiel](~/ef6/media/tptexample.jpg)

<span data-ttu-id="691db-185">Sie können dies vermeiden und die standardmäßige TPH-Zuordnung auf verschiedene Weise beibehalten:</span><span class="sxs-lookup"><span data-stu-id="691db-185">You can avoid this, and maintain the default TPH mapping, in a couple ways:</span></span>

1.  <span data-ttu-id="691db-186">Ruft die Tabelle mit demselben Tabellennamen für jeden Typ in der Hierarchie auf.</span><span class="sxs-lookup"><span data-stu-id="691db-186">Call ToTable with the same table name for each type in the hierarchy.</span></span>
2.  <span data-ttu-id="691db-187">Wird nur für die Basisklasse der Hierarchie aufgerufen, in unserem Beispiel Mitarbeiter.</span><span class="sxs-lookup"><span data-stu-id="691db-187">Call ToTable only on the base class of the hierarchy, in our example that would be employee.</span></span>

 

## <a name="execution-order"></a><span data-ttu-id="691db-188">Ausführungsreihenfolge</span><span class="sxs-lookup"><span data-stu-id="691db-188">Execution Order</span></span>

<span data-ttu-id="691db-189">Konventionen wirken sich auf die letzte WINS-Weise aus, die mit der flüssigen API identisch ist.</span><span class="sxs-lookup"><span data-stu-id="691db-189">Conventions operate in a last wins manner, the same as the Fluent API.</span></span> <span data-ttu-id="691db-190">Dies bedeutet, dass beim Schreiben von zwei Konventionen, die dieselbe Option der gleichen Eigenschaft konfigurieren, der letzte, der ausgeführt werden muss, gewinnt.</span><span class="sxs-lookup"><span data-stu-id="691db-190">What this means is that if you write two conventions that configure the same option of the same property, then the last one to execute wins.</span></span> <span data-ttu-id="691db-191">Im folgenden Code wird beispielsweise die maximale Länge aller Zeichen folgen auf 500 festgelegt, aber wir konfigurieren alle Eigenschaften namens Name im Modell so, dass Sie eine maximale Länge von 250 haben.</span><span class="sxs-lookup"><span data-stu-id="691db-191">As an example, in the code below the max length of all strings is set to 500 but we then configure all properties called Name in the model to have a max length of 250.</span></span>

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

<span data-ttu-id="691db-192">Da die Konvention, die maximale Länge auf 250 festzulegen, hinter der Konvention liegt, die alle Zeichen folgen auf 500 festlegt, haben alle Eigenschaften namens Name in unserem Modell einen maxLength-Wert von 250, während alle anderen Zeichen folgen, wie z. b. Beschreibungen, 500 sind.</span><span class="sxs-lookup"><span data-stu-id="691db-192">Because the convention to set max length to 250 is after the one that sets all strings to 500, all the properties called Name in our model will have a MaxLength of 250 while any other strings, such as descriptions, would be 500.</span></span> <span data-ttu-id="691db-193">Die Verwendung von Konventionen auf diese Weise bedeutet, dass Sie eine allgemeine Konvention für Typen oder Eigenschaften im Modell bereitstellen und diese dann für unterschiedliche Teilmengen überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="691db-193">Using conventions in this way means that you can provide a general convention for types or properties in your model and then overide them for subsets that are different.</span></span>

<span data-ttu-id="691db-194">Die fließende API und Daten Anmerkungen können auch verwendet werden, um eine Konvention in bestimmten Fällen zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="691db-194">The Fluent API and Data Annotations can also be used to override a convention in specific cases.</span></span> <span data-ttu-id="691db-195">Wenn wir in unserem obigen Beispiel die überflüssige API verwendet haben, um die maximale Länge einer Eigenschaft festzulegen, haben wir Sie möglicherweise vor oder nach der Konvention eingefügt, da die spezifischere, fließende API die allgemeinere Konfigurations Konvention gewinnt.</span><span class="sxs-lookup"><span data-stu-id="691db-195">In our example above if we had used the Fluent API to set the max length of a property then we could have put it before or after the convention, because the more specific Fluent API will win over the more general Configuration Convention.</span></span>

 

## <a name="built-in-conventions"></a><span data-ttu-id="691db-196">Integrierte Konventionen</span><span class="sxs-lookup"><span data-stu-id="691db-196">Built-in Conventions</span></span>

<span data-ttu-id="691db-197">Da benutzerdefinierte Konventionen durch die standardmäßigen Code First Konventionen beeinträchtigt werden können, kann es hilfreich sein, Konventionen hinzuzufügen, die vor oder nach einer anderen Konvention ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="691db-197">Because custom conventions could be affected by the default Code First conventions, it can be useful to add conventions to run before or after another convention.</span></span> <span data-ttu-id="691db-198">Zu diesem Zweck können Sie die AddBefore-Methode und die AddAfter-Methode der Conventions-Auflistung in Ihrem abgeleiteten dbcontext verwenden.</span><span class="sxs-lookup"><span data-stu-id="691db-198">To do this you can use the AddBefore and AddAfter methods of the Conventions collection on your derived DbContext.</span></span> <span data-ttu-id="691db-199">Der folgende Code fügt die zuvor erstellte Konventionen-Klasse hinzu, sodass Sie vor der integrierten Schlüssel Ermittlungs Konvention ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="691db-199">The following code would add the convention class we created earlier so that it will run before the built in key discovery convention.</span></span>

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

<span data-ttu-id="691db-200">Dies wird beim Hinzufügen von Konventionen, die vor oder nach den integrierten Konventionen ausgeführt werden müssen, am meisten verwendet. eine Liste der integrierten Konventionen finden Sie hier: [System. Data. Entity. modelconfiguration. Conventions-Namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span><span class="sxs-lookup"><span data-stu-id="691db-200">This is going to be of the most use when adding conventions that need to run before or after the built in conventions, a list of the built in conventions can be found here: [System.Data.Entity.ModelConfiguration.Conventions Namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>

<span data-ttu-id="691db-201">Sie können auch Konventionen entfernen, die nicht auf das Modell angewendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="691db-201">You can also remove conventions that you do not want applied to your model.</span></span> <span data-ttu-id="691db-202">Verwenden Sie zum Entfernen einer Konvention die Remove-Methode.</span><span class="sxs-lookup"><span data-stu-id="691db-202">To remove a convention, use the Remove method.</span></span> <span data-ttu-id="691db-203">Im folgenden finden Sie ein Beispiel für das Entfernen von pluralizingtablenameconvention.</span><span class="sxs-lookup"><span data-stu-id="691db-203">Here is an example of removing the PluralizingTableNameConvention.</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
