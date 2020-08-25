---
title: Code First Daten Anmerkungen-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
ms.openlocfilehash: 17370b83aee9974161b1771e8a1dc99c2de27f0f
ms.sourcegitcommit: 6f7af3f138bf7c724cbdda261f97e5cf7035e8d7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88847617"
---
# <a name="code-first-data-annotations"></a><span data-ttu-id="fa5c9-102">Code First Data Annotations</span><span class="sxs-lookup"><span data-stu-id="fa5c9-102">Code First Data Annotations</span></span>
> [!NOTE]
> <span data-ttu-id="fa5c9-103">**EF 4.1** und höher: die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 4,1 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-103">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="fa5c9-104">Wenn Sie eine frühere Version verwenden, sind einige oder alle dieser Informationen nicht anwendbar.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-104">If you are using an earlier version, some or all of this information does not apply.</span></span>

<span data-ttu-id="fa5c9-105">Der Inhalt auf dieser Seite wird von einem Artikel angepasst, der ursprünglich von Julie Lerman () geschrieben wurde \<http://thedatafarm.com> .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-105">The content on this page is adapted from an article originally written by Julie Lerman (\<http://thedatafarm.com>).</span></span>

<span data-ttu-id="fa5c9-106">Entity Framework Code First ermöglicht Ihnen die Verwendung ihrer eigenen Domänen Klassen, um das Modell darzustellen, das EF zum Ausführen von Abfragen, Änderungs Nachverfolgung und Aktualisierungs Funktionen verwendet.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-106">Entity Framework Code First allows you to use your own domain classes to represent the model that EF relies on to perform querying, change tracking, and updating functions.</span></span> <span data-ttu-id="fa5c9-107">Code First nutzt ein Programmier Muster, das als "Konvention over Configuration" bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-107">Code First leverages a programming pattern referred to as 'convention over configuration.'</span></span> <span data-ttu-id="fa5c9-108">Code First geht davon aus, dass die Klassen den Konventionen Entity Framework entsprechen und in diesem Fall automatisch herausfinden, wie der Auftrag ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-108">Code First will assume that your classes follow the conventions of Entity Framework, and in that case, will automatically work out how to perform its job.</span></span> <span data-ttu-id="fa5c9-109">Wenn Ihre Klassen diesen Konventionen jedoch nicht folgen, können Sie Ihren Klassen Konfigurationen hinzufügen, um EF die erforderlichen Informationen bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-109">However, if your classes do not follow those conventions, you have the ability to add configurations to your classes to provide EF with the requisite information.</span></span>

<span data-ttu-id="fa5c9-110">Code First bietet Ihnen zwei Möglichkeiten, diese Konfigurationen ihren Klassen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-110">Code First gives you two ways to add these configurations to your classes.</span></span> <span data-ttu-id="fa5c9-111">Eine Methode verwendet einfache Attribute namens DataAnnotations, die zweite verwendet die fließende API Code First, die Ihnen eine Möglichkeit bietet, Konfigurationen im Code Imperativ zu beschreiben.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-111">One is using simple attributes called DataAnnotations, and the second is using Code First’s Fluent API, which provides you with a way to describe configurations imperatively, in code.</span></span>

<span data-ttu-id="fa5c9-112">Der Schwerpunkt dieses Artikels liegt auf der Verwendung von DataAnnotations (im Namespace System. ComponentModel. DataAnnotations) zum Konfigurieren der Klassen – Hervorhebung der am häufigsten benötigten Konfigurationen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-112">This article will focus on using DataAnnotations (in the System.ComponentModel.DataAnnotations namespace) to configure your classes – highlighting the most commonly needed configurations.</span></span> <span data-ttu-id="fa5c9-113">DataAnnotations werden auch von einer Reihe von .NET-Anwendungen, wie z. b. ASP.NET MVC, interpretiert, sodass diese Anwendungen die gleichen Anmerkungen für Client seitige Überprüfungen nutzen können.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-113">DataAnnotations are also understood by a number of .NET applications, such as ASP.NET MVC which allows these applications to leverage the same annotations for client-side validations.</span></span>


## <a name="the-model"></a><span data-ttu-id="fa5c9-114">Die Model-Komponente</span><span class="sxs-lookup"><span data-stu-id="fa5c9-114">The model</span></span>

<span data-ttu-id="fa5c9-115">Ich zeige Ihnen Code First DataAnnotations mit einem einfachen paar von Klassen: Blog und Post.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-115">I’ll demonstrate Code First DataAnnotations with a simple pair of classes: Blog and Post.</span></span>

``` csharp
    public class Blog
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public DateTime DateCreated { get; set; }
        public string Content { get; set; }
        public int BlogId { get; set; }
        public ICollection<Comment> Comments { get; set; }
    }
```

<span data-ttu-id="fa5c9-116">Wie Sie sind, befolgen die Blog-und Post-Klassen die Code First-Konvention und erfordern keine Anpassungen, um die EF-Kompatibilität zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-116">As they are, the Blog and Post classes conveniently follow code first convention and require no tweaks to enable EF compatability.</span></span> <span data-ttu-id="fa5c9-117">Allerdings können Sie mit den Anmerkungen auch weitere Informationen zu den Klassen und der Datenbank, denen Sie zugeordnet sind, für EF bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-117">However, you can also use the annotations to provide more information to EF about the classes and the database to which they map.</span></span>

 

## <a name="key"></a><span data-ttu-id="fa5c9-118">Schlüssel</span><span class="sxs-lookup"><span data-stu-id="fa5c9-118">Key</span></span>

<span data-ttu-id="fa5c9-119">Entity Framework stützt sich auf jede Entität, die einen Schlüsselwert aufweist, der für die Entitäts Verfolgung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-119">Entity Framework relies on every entity having a key value that is used for entity tracking.</span></span> <span data-ttu-id="fa5c9-120">Eine Code First Konvention sind implizite Schlüsseleigenschaften. Code First suchen nach einer Eigenschaft mit dem Namen "ID" oder einer Kombination aus Klassenname und ID, z. b. "BlogId".</span><span class="sxs-lookup"><span data-stu-id="fa5c9-120">One convention of Code First is implicit key properties; Code First will look for a property named “Id”, or a combination of class name and “Id”, such as “BlogId”.</span></span> <span data-ttu-id="fa5c9-121">Diese Eigenschaft wird einer Primärschlüssel Spalte in der Datenbank zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-121">This property will map to a primary key column in the database.</span></span>

<span data-ttu-id="fa5c9-122">Die Blog-und Post-Klassen befolgen diese Konvention.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-122">The Blog and Post classes both follow this convention.</span></span> <span data-ttu-id="fa5c9-123">Was geschieht, wenn Sie nicht?</span><span class="sxs-lookup"><span data-stu-id="fa5c9-123">What if they didn’t?</span></span> <span data-ttu-id="fa5c9-124">Was passiert, wenn der Blog stattdessen den Namen *primarytrackingkey* oder sogar *foo*verwendet hat?</span><span class="sxs-lookup"><span data-stu-id="fa5c9-124">What if Blog used the name *PrimaryTrackingKey* instead, or even *foo*?</span></span> <span data-ttu-id="fa5c9-125">Wenn Code First keine Eigenschaft findet, die dieser Konvention entspricht, löst er eine Ausnahme aus, da die Anforderung des Entity Framework eine Schlüsseleigenschaft aufweisen muss.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-125">If code first does not find a property that matches this convention it will throw an exception because of Entity Framework’s requirement that you must have a key property.</span></span> <span data-ttu-id="fa5c9-126">Sie können die Schlüssel Anmerkung verwenden, um anzugeben, welche Eigenschaft als EntityKey verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-126">You can use the key annotation to specify which property is to be used as the EntityKey.</span></span>

``` csharp
    public class Blog
    {
        [Key]
        public int PrimaryTrackingKey { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }
```

<span data-ttu-id="fa5c9-127">Wenn Sie das Feature zur Datenbankgenerierung von Code First verwenden, verfügt die Tabelle "Blog" über eine Primärschlüssel Spalte mit dem Namen "primarytrackingkey", die standardmäßig auch als Identität definiert ist.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-127">If you are using code first’s database generation feature, the Blog table will have a primary key column named PrimaryTrackingKey, which is also defined as Identity by default.</span></span>

![Blog Tabelle mit Primärschlüssel](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a><span data-ttu-id="fa5c9-129">Zusammengesetzte Schlüssel</span><span class="sxs-lookup"><span data-stu-id="fa5c9-129">Composite keys</span></span>

<span data-ttu-id="fa5c9-130">Entity Framework unterstützt zusammengesetzte Schlüssel-Primärschlüssel, die aus mehr als einer Eigenschaft bestehen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-130">Entity Framework supports composite keys - primary keys that consist of more than one property.</span></span> <span data-ttu-id="fa5c9-131">Beispielsweise können Sie über eine Passport-Klasse verfügen, deren Primärschlüssel eine Kombination aus Passport Number und issuingcountry ist.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-131">For example, you could have a Passport class whose primary key is a combination of PassportNumber and IssuingCountry.</span></span>

``` csharp
    public class Passport
    {
        [Key]
        public int PassportNumber { get; set; }
        [Key]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

<span data-ttu-id="fa5c9-132">Wenn Sie versuchen, die oben genannte Klasse in Ihrem EF-Modell zu verwenden, ergibt sich Folgendes `InvalidOperationException` :</span><span class="sxs-lookup"><span data-stu-id="fa5c9-132">Attempting to use the above class in your EF model would result in an `InvalidOperationException`:</span></span>

<span data-ttu-id="fa5c9-133">*Die zusammengesetzte Primärschlüssel Anordnung für den Typ "Passport" kann nicht bestimmt werden. Verwenden Sie die ColumnAttribute-Methode oder die Haskey-Methode, um eine Reihenfolge für zusammengesetzte Primärschlüssel anzugeben.*</span><span class="sxs-lookup"><span data-stu-id="fa5c9-133">*Unable to determine composite primary key ordering for type 'Passport'. Use the ColumnAttribute or the HasKey method to specify an order for composite primary keys.*</span></span>

<span data-ttu-id="fa5c9-134">Um zusammengesetzte Schlüssel verwenden zu können, müssen Entity Framework eine Reihenfolge für die Schlüsseleigenschaften definieren.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-134">In order to use composite keys, Entity Framework requires you to define an order for the key properties.</span></span> <span data-ttu-id="fa5c9-135">Hierfür können Sie mithilfe der Spalten Anmerkung eine Bestellung angeben.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-135">You can do this by using the Column annotation to specify an order.</span></span>

>[!NOTE]
> <span data-ttu-id="fa5c9-136">Der ORDER-Wert ist relativ (nicht Index basiert), sodass alle Werte verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-136">The order value is relative (rather than index based) so any values can be used.</span></span> <span data-ttu-id="fa5c9-137">Beispielsweise sind 100 und 200 anstelle von 1 und 2 zulässig.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-137">For example, 100 and 200 would be acceptable in place of 1 and 2.</span></span>

``` csharp
    public class Passport
    {
        [Key]
        [Column(Order=1)]
        public int PassportNumber { get; set; }
        [Key]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

<span data-ttu-id="fa5c9-138">Wenn Sie über Entitäten mit zusammengesetzten Fremdschlüsseln verfügen, müssen Sie die gleiche Spalten Reihenfolge angeben, die Sie für die entsprechenden Primärschlüssel Eigenschaften verwendet haben.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-138">If you have entities with composite foreign keys, then you must specify the same column ordering that you used for the corresponding primary key properties.</span></span>

<span data-ttu-id="fa5c9-139">Nur die relative Reihenfolge in den Fremdschlüssel Eigenschaften muss identisch sein. die genauen Werte, die der **Bestellung** zugewiesen sind, müssen nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-139">Only the relative ordering within the foreign key properties needs to be the same, the exact values assigned to **Order** do not need to match.</span></span> <span data-ttu-id="fa5c9-140">In der folgenden Klasse können z. b. 3 und 4 anstelle von 1 und 2 verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-140">For example, in the following class, 3 and 4 could be used in place of 1 and 2.</span></span>

``` csharp
    public class PassportStamp
    {
        [Key]
        public int StampId { get; set; }
        public DateTime Stamped { get; set; }
        public string StampingCountry { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 1)]
        public int PassportNumber { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }

        public Passport Passport { get; set; }
    }
```

## <a name="required"></a><span data-ttu-id="fa5c9-141">Erforderlich</span><span class="sxs-lookup"><span data-stu-id="fa5c9-141">Required</span></span>

<span data-ttu-id="fa5c9-142">Die- `Required` Anmerkung weist EF an, dass eine bestimmte Eigenschaft erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-142">The `Required` annotation tells EF that a particular property is required.</span></span>

<span data-ttu-id="fa5c9-143">Durch das Hinzufügen von erforderlich für die Title-Eigenschaft wird EF (und MVC) gezwungen, sicherzustellen, dass die Eigenschaft über Daten verfügt.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-143">Adding Required to the Title property will force EF (and MVC) to ensure that the property has data in it.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="fa5c9-144">Ohne zusätzlichen Code oder Markup Änderungen in der Anwendung führt eine MVC-Anwendung eine Client seitige Validierung durch, wobei eine Nachricht auch dynamisch mit den Namen der Eigenschaft und der Anmerkung erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-144">With no additional code or markup changes in the application, an MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![Fehler beim Erstellen einer Seite mit dem Titel "erforderlich".](~/ef6/media/jj591583-figure02.png)

<span data-ttu-id="fa5c9-146">Das erforderliche Attribut wirkt sich auch auf die generierte Datenbank aus, indem die zugeordnete Eigenschaft nicht auf NULL festgelegt werden kann.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-146">The Required attribute will also affect the generated database by making the mapped property non-nullable.</span></span> <span data-ttu-id="fa5c9-147">Beachten Sie, dass sich das Feld "Title" in "not NULL" geändert hat.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-147">Notice that the Title field has changed to “not null”.</span></span>

>[!NOTE]
> <span data-ttu-id="fa5c9-148">In einigen Fällen ist es möglicherweise nicht möglich, dass die Spalte in der Datenbank keine NULL-Werte zulässt, auch wenn die-Eigenschaft erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-148">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="fa5c9-149">Wenn z. b. eine TPH-Vererbungs Strategie verwendet wird, werden Daten für mehrere Typen in einer einzelnen Tabelle gespeichert.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-149">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="fa5c9-150">Wenn ein abgeleiteter Typ eine erforderliche Eigenschaft enthält, kann die Spalte nicht auf NULL festleg Bare Werte festgelegt werden, da nicht alle Typen in der Hierarchie über diese Eigenschaft verfügen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-150">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>

 

![Tabelle "Blogs"](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a><span data-ttu-id="fa5c9-152">MaxLength und minLength</span><span class="sxs-lookup"><span data-stu-id="fa5c9-152">MaxLength and MinLength</span></span>

<span data-ttu-id="fa5c9-153">`MaxLength`Mit dem-Attribut und dem- `MinLength` Attribut können Sie zusätzliche Eigenschaften Überprüfungen angeben, wie Sie dies durchgeführt haben `Required` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-153">The `MaxLength` and `MinLength` attributes allow you to specify additional property validations, just as you did with `Required`.</span></span>

<span data-ttu-id="fa5c9-154">Im folgenden finden Sie den Blog Name mit Längen Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-154">Here is the BloggerName with length requirements.</span></span> <span data-ttu-id="fa5c9-155">Das Beispiel veranschaulicht auch das Kombinieren von Attributen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-155">The example also demonstrates how to combine attributes.</span></span>

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="fa5c9-156">Die MaxLength-Anmerkung wirkt sich auf die Datenbank aus, indem die Länge der Eigenschaft auf 10 festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-156">The MaxLength annotation will impact the database by setting the property’s length to 10.</span></span>

![Tabelle "Blogs" mit der maximalen Länge für die bloggername-Spalte](~/ef6/media/jj591583-figure04.png)

<span data-ttu-id="fa5c9-158">Bei der Client seitigen MVC-Anmerkung und der serverseitigen EF 4,1-Anmerkung wird diese Validierung berücksichtigt, und es wird erneut dynamisch eine Fehlermeldung erstellt: "das Feld" bloggername "muss eine Zeichenfolge oder ein Arraytyp mit einer maximalen Länge von" 10 "sein. Diese Nachricht ist ein wenig lang.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-158">MVC client-side annotation and EF 4.1 server-side annotation will both honor this validation, again dynamically building an error message: “The field BloggerName must be a string or array type with a maximum length of '10'.” That message is a little long.</span></span> <span data-ttu-id="fa5c9-159">Viele Anmerkungen ermöglichen es Ihnen, eine Fehlermeldung mit dem ErrorMessage-Attribut anzugeben.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-159">Many annotations let you specify an error message with the ErrorMessage attribute.</span></span>

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="fa5c9-160">Sie können auch ErrorMessage in der erforderlichen Anmerkung angeben.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-160">You can also specify ErrorMessage in the Required annotation.</span></span>

![Seite mit benutzerdefinierter Fehlermeldung erstellen](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a><span data-ttu-id="fa5c9-162">Nicht zugeordnet</span><span class="sxs-lookup"><span data-stu-id="fa5c9-162">NotMapped</span></span>

<span data-ttu-id="fa5c9-163">Die Code First-Konvention legt fest, dass jede Eigenschaft mit einem unterstützten Datentyp in der Datenbank dargestellt wird.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-163">Code first convention dictates that every property that is of a supported data type is represented in the database.</span></span> <span data-ttu-id="fa5c9-164">Dies ist jedoch nicht immer der Fall in Ihren Anwendungen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-164">But this isn’t always the case in your applications.</span></span> <span data-ttu-id="fa5c9-165">Beispielsweise könnten Sie über eine Eigenschaft in der Blog-Klasse verfügen, die einen Code auf Grundlage der Felder Titel und bloggername erstellt.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-165">For example you might have a property in the Blog class that creates a code based on the Title and BloggerName fields.</span></span> <span data-ttu-id="fa5c9-166">Diese Eigenschaft kann dynamisch erstellt werden und muss nicht gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-166">That property can be created dynamically and does not need to be stored.</span></span> <span data-ttu-id="fa5c9-167">Sie können alle Eigenschaften, die nicht der Datenbank zugeordnet sind, mit der Anmerkung "notmapping" markieren, z. b. diese blogcode-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-167">You can mark any properties that do not map to the database with the NotMapped annotation such as this BlogCode property.</span></span>

``` csharp
    [NotMapped]
    public string BlogCode
    {
        get
        {
            return Title.Substring(0, 1) + ":" + BloggerName.Substring(0, 1);
        }
    }
```

 

## <a name="complextype"></a><span data-ttu-id="fa5c9-168">ComplexType</span><span class="sxs-lookup"><span data-stu-id="fa5c9-168">ComplexType</span></span>

<span data-ttu-id="fa5c9-169">Es ist nicht ungewöhnlich, dass Sie Ihre Domänen Entitäten über einen Satz von Klassen hinweg beschreiben und dann diese Klassen auf die Beschreibung einer kompletten Entität umschichten.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-169">It’s not uncommon to describe your domain entities across a set of classes and then layer those classes to describe a complete entity.</span></span> <span data-ttu-id="fa5c9-170">Beispielsweise können Sie dem Modell eine Klasse mit dem Namen blogdetails hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-170">For example, you may add a class called BlogDetails to your model.</span></span>

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="fa5c9-171">Beachten Sie, dass keinen `BlogDetails` Typ von Schlüsseleigenschaft hat.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-171">Notice that `BlogDetails` does not have any type of key property.</span></span> <span data-ttu-id="fa5c9-172">Im Domänen gestützten Design `BlogDetails` wird als Wertobjekt bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-172">In domain driven design, `BlogDetails` is referred to as a value object.</span></span> <span data-ttu-id="fa5c9-173">Entity Framework bezieht sich auf Wert Objekte als komplexe Typen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-173">Entity Framework refers to value objects as complex types.</span></span><span data-ttu-id="fa5c9-174">Komplexe Typen können nicht selbst nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-174">  Complex types cannot be tracked on their own.</span></span>

<span data-ttu-id="fa5c9-175">Als Eigenschaft in der- `Blog` Klasse wird jedoch `BlogDetails` als Teil eines-Objekts nachverfolgt `Blog` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-175">However as a property in the `Blog` class, `BlogDetails` will be tracked as part of a `Blog` object.</span></span> <span data-ttu-id="fa5c9-176">Damit Code zuerst dies erkennt, müssen Sie die `BlogDetails` Klasse als markieren `ComplexType` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-176">In order for code first to recognize this, you must mark the `BlogDetails` class as a `ComplexType`.</span></span>

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="fa5c9-177">Nun können Sie in der-Klasse eine Eigenschaft hinzufügen `Blog` , die `BlogDetails` für diesen Blog darstellt.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-177">Now you can add a property in the `Blog` class to represent the `BlogDetails` for that blog.</span></span>

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

<span data-ttu-id="fa5c9-178">In der-Datenbank `Blog` enthält die Tabelle alle Eigenschaften des Blogs einschließlich der Eigenschaften, die in der-Eigenschaft enthalten sind `BlogDetail` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-178">In the database, the `Blog` table will contain all of the properties of the blog including the properties contained in its `BlogDetail` property.</span></span> <span data-ttu-id="fa5c9-179">Standardmäßig wird jedem der Name des komplexen Typs "blogdetail" vorangestellt.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-179">By default, each one is preceded with the name of the complex type, "BlogDetail".</span></span>

![Blog Tabelle mit komplexem Typ](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a><span data-ttu-id="fa5c9-181">ConcurrencyCheck</span><span class="sxs-lookup"><span data-stu-id="fa5c9-181">ConcurrencyCheck</span></span>

<span data-ttu-id="fa5c9-182">Mit der-Anmerkung `ConcurrencyCheck` können Sie eine oder mehrere Eigenschaften markieren, die für die Parallelitäts Überprüfung in der Datenbank verwendet werden sollen, wenn ein Benutzer eine Entität bearbeitet oder löscht.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-182">The `ConcurrencyCheck` annotation allows you to flag one or more properties to be used for concurrency checking in the database when a user edits or deletes an entity.</span></span> <span data-ttu-id="fa5c9-183">Wenn Sie mit dem EF-Designer gearbeitet haben, entspricht dies dem Festlegen der Eigenschaft `ConcurrencyMode` auf `Fixed` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-183">If you've been working with the EF Designer, this aligns with setting a property's `ConcurrencyMode` to `Fixed`.</span></span>

<span data-ttu-id="fa5c9-184">Sehen wir uns `ConcurrencyCheck` an, wie funktioniert, indem es der-Eigenschaft hinzugefügt wird `BloggerName` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-184">Let’s see how `ConcurrencyCheck` works by adding it to the `BloggerName` property.</span></span>

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="fa5c9-185">Wenn `SaveChanges` aufgerufen wird, `ConcurrencyCheck` `BloggerName` wird der ursprüngliche Wert dieser Eigenschaft in der Aktualisierung verwendet, aufgrund der-Anmerkung für das Feld.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-185">When `SaveChanges` is called, because of the `ConcurrencyCheck` annotation on the `BloggerName` field, the original value of that property will be used in the update.</span></span> <span data-ttu-id="fa5c9-186">Der Befehl versucht, die richtige Zeile zu suchen, indem nicht nur der Schlüsselwert, sondern auch der ursprüngliche Wert von gefiltert wird `BloggerName` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-186">The command will attempt to locate the correct row by filtering not only on the key value but also on the original value of `BloggerName`.</span></span><span data-ttu-id="fa5c9-187">Hier sind die wichtigen Teile des Update-Befehls, die an die Datenbank gesendet werden. in diesem Fall können Sie sehen, dass der Befehl die Zeile mit dem `PrimaryTrackingKey` Wert "1" und `BloggerName` "Julie" aktualisiert. Dies war der ursprüngliche Wert, als dieser Blog aus der Datenbank abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-187">  Here are the critical parts of the UPDATE command sent to the database, where you can see the command will update the row that has a `PrimaryTrackingKey` is 1 and a `BloggerName` of “Julie” which was the original value when that blog was retrieved from the database.</span></span>

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

<span data-ttu-id="fa5c9-188">Wenn jemand den Blogger-Namen für diesen Blog in der Zwischenzeit geändert hat, schlägt dieses Update fehl, und Sie erhalten eine **dbupdatecon-cyexception** , die Sie behandeln müssen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-188">If someone has changed the blogger name for that blog in the meantime, this update will fail and you’ll get a **DbUpdateConcurrencyException** that you'll need to handle.</span></span>

 

## <a name="timestamp"></a><span data-ttu-id="fa5c9-189">TimeStamp</span><span class="sxs-lookup"><span data-stu-id="fa5c9-189">TimeStamp</span></span>

<span data-ttu-id="fa5c9-190">Es ist häufiger, rowversion-oder Zeitstempel-Felder für die Parallelitäts Überprüfung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-190">It's more common to use rowversion or timestamp fields for concurrency checking.</span></span> <span data-ttu-id="fa5c9-191">Anstatt die-Anmerkung `ConcurrencyCheck` zu verwenden, können Sie jedoch die spezifischere Anmerkung verwenden, solange `TimeStamp` der Typ der Eigenschaft ein Bytearray ist.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-191">But rather than using the `ConcurrencyCheck` annotation, you can use the more specific `TimeStamp` annotation as long as the type of the property is byte array.</span></span> <span data-ttu-id="fa5c9-192">Code First behandelt `Timestamp` Eigenschaften wie `ConcurrencyCheck` Eigenschaften, aber es wird auch sichergestellt, dass das Datenbankfeld, das von Code First generiert wird, keine NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-192">Code first will treat `Timestamp` properties the same as `ConcurrencyCheck` properties, but it will also ensure that the database field that code first generates is non-nullable.</span></span> <span data-ttu-id="fa5c9-193">In einer bestimmten Klasse kann nur eine Zeitstempel-Eigenschaft vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-193">You can only have one timestamp property in a given class.</span></span>

<span data-ttu-id="fa5c9-194">Fügen Sie der Blog-Klasse die folgende Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="fa5c9-194">Adding the following property to the Blog class:</span></span>

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

<span data-ttu-id="fa5c9-195">führt dazu, dass Code zuerst in der Datenbanktabelle eine Zeitstempel-Spalte erstellt, die keine NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-195">results in code first creating a non-nullable timestamp column in the database table.</span></span>

![Tabelle "Blogs" mit Zeitstempel Spalte](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a><span data-ttu-id="fa5c9-197">Tabelle und Spalte</span><span class="sxs-lookup"><span data-stu-id="fa5c9-197">Table and Column</span></span>

<span data-ttu-id="fa5c9-198">Wenn Sie Code First die Datenbank erstellen möchten, können Sie den Namen der zu erstellenden Tabellen und Spalten ändern.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-198">If you are letting Code First create the database, you may want to change the name of the tables and columns it is creating.</span></span> <span data-ttu-id="fa5c9-199">Sie können auch Code First mit einer vorhandenen Datenbank verwenden.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-199">You can also use Code First with an existing database.</span></span> <span data-ttu-id="fa5c9-200">Es ist jedoch nicht immer der Fall, dass die Namen der Klassen und Eigenschaften in der Domäne den Namen der Tabellen und Spalten in der Datenbank entsprechen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-200">But it's not always the case that the names of the classes and properties in your domain match the names of the tables and columns in your database.</span></span>

<span data-ttu-id="fa5c9-201">Meine Klasse wird benannt `Blog` , und Code First geht davon aus, dass dies einer Tabelle mit dem Namen zugeordnet wird `Blogs` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-201">My class is named `Blog` and by convention, code first presumes this will map to a table named `Blogs`.</span></span> <span data-ttu-id="fa5c9-202">Wenn dies nicht der Fall ist, können Sie den Namen der Tabelle mit dem- `Table` Attribut angeben.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-202">If that's not the case you can specify the name of the table with the `Table` attribute.</span></span> <span data-ttu-id="fa5c9-203">In diesem Beispiel gibt die-Anmerkung an, dass der Tabellenname **internalblogs**ist.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-203">Here for example, the annotation is specifying that the table name is **InternalBlogs**.</span></span>

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

<span data-ttu-id="fa5c9-204">Die-Anmerkung `Column` ist eine bessere Angabe der Attribute einer zugeordneten Spalte.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-204">The `Column` annotation is a more adept in specifying the attributes of a mapped column.</span></span> <span data-ttu-id="fa5c9-205">Sie können einen Namen, einen Datentyp oder sogar die Reihenfolge festlegen, in der eine Spalte in der Tabelle angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-205">You can stipulate a name, data type or even the order in which a column appears in the table.</span></span> <span data-ttu-id="fa5c9-206">Im folgenden finden Sie ein Beispiel für das- `Column` Attribut.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-206">Here is an example of the `Column` attribute.</span></span>

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

<span data-ttu-id="fa5c9-207">Verwechseln Sie `TypeName` das Attribut der Spalte nicht mit dem DataType-DataAnnotation.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-207">Don’t confuse Column’s `TypeName` attribute with the DataType DataAnnotation.</span></span> <span data-ttu-id="fa5c9-208">DataType ist eine Anmerkung, die für die Benutzeroberfläche verwendet wird, und wird von Code First ignoriert.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-208">DataType is an annotation used for the UI and is ignored by Code First.</span></span>

<span data-ttu-id="fa5c9-209">Dies ist die Tabelle, nachdem Sie neu generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-209">Here is the table after it’s been regenerated.</span></span> <span data-ttu-id="fa5c9-210">Der Tabellenname wurde in **internalblogs** geändert, und `Description` die Spalte aus dem komplexen Typ lautet jetzt `BlogDescription` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-210">The table name has changed to **InternalBlogs** and `Description` column from the complex type is now `BlogDescription`.</span></span> <span data-ttu-id="fa5c9-211">Da der Name in der-Anmerkung angegeben wurde, verwendet Code First nicht die Konvention zum Starten des Spaltennamens mit dem Namen des komplexen Typs.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-211">Because the name was specified in the annotation, code first will not use the convention of starting the column name with the name of the complex type.</span></span>

![Umbenannte Blogs-Tabelle und-Spalte](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a><span data-ttu-id="fa5c9-213">Databasegenerated</span><span class="sxs-lookup"><span data-stu-id="fa5c9-213">DatabaseGenerated</span></span>

<span data-ttu-id="fa5c9-214">Wichtige Datenbankfunktionen sind die Möglichkeit, berechnete Eigenschaften zu haben.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-214">An important database features is the ability to have computed properties.</span></span> <span data-ttu-id="fa5c9-215">Wenn Sie die Code First Klassen Tabellen zuordnet, die berechnete Spalten enthalten, möchten Sie Entity Framework nicht versuchen, diese Spalten zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-215">If you're mapping your Code First classes to tables that contain computed columns, you don't want Entity Framework to try to update those columns.</span></span> <span data-ttu-id="fa5c9-216">Sie möchten jedoch, dass EF diese Werte aus der Datenbank zurückgibt, nachdem Sie Daten eingefügt oder aktualisiert haben.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-216">But you do want EF to return those values from the database after you've inserted or updated data.</span></span> <span data-ttu-id="fa5c9-217">Mit der-Anmerkung können Sie `DatabaseGenerated` diese Eigenschaften in der-Klasse zusammen mit der-Enumeration markieren `Computed` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-217">You can use the `DatabaseGenerated` annotation to flag those properties in your class along with the `Computed` enum.</span></span> <span data-ttu-id="fa5c9-218">Andere Aufstände sind `None` und `Identity` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-218">Other enums are `None` and `Identity`.</span></span>

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

<span data-ttu-id="fa5c9-219">Sie können eine Datenbank verwenden, die für Byte-oder Zeitstempel-Spalten generiert wird, wenn Code First die Datenbank generiert. andernfalls sollten Sie diese nur verwenden, wenn Sie auf vorhandene Datenbanken verweisen, da Code First die Formel für die berechnete Spalte nicht ermitteln kann.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-219">You can use database generated on byte or timestamp columns when code first is generating the database, otherwise you should only use this when pointing to existing databases because code first won't be able to determine the formula for the computed column.</span></span>

<span data-ttu-id="fa5c9-220">Sie haben darüber hinaus gelesen, dass eine Schlüsseleigenschaft, die eine ganze Zahl ist, standardmäßig ein Identitätsschlüssel in der Datenbank wird.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-220">You read above that by default, a key property that is an integer will become an identity key in the database.</span></span> <span data-ttu-id="fa5c9-221">Das wäre dasselbe wie das Festlegen `DatabaseGenerated` von auf `DatabaseGeneratedOption.Identity` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-221">That would be the same as setting `DatabaseGenerated` to `DatabaseGeneratedOption.Identity`.</span></span> <span data-ttu-id="fa5c9-222">Wenn Sie nicht möchten, dass es sich um einen Identitätsschlüssel handelt, können Sie den Wert auf festlegen `DatabaseGeneratedOption.None` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-222">If you do not want it to be an identity key, you can set the value to `DatabaseGeneratedOption.None`.</span></span>

 

## <a name="index"></a><span data-ttu-id="fa5c9-223">Index</span><span class="sxs-lookup"><span data-stu-id="fa5c9-223">Index</span></span>

> [!NOTE]
> <span data-ttu-id="fa5c9-224">**Nur EF 6.1** : das `Index` Attribut wurde in Entity Framework 6,1 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-224">**EF6.1 Onwards Only** - The `Index` attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="fa5c9-225">Wenn Sie eine frühere Version verwenden, gelten die Informationen in diesem Abschnitt nicht.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-225">If you are using an earlier version the information in this section does not apply.</span></span>

<span data-ttu-id="fa5c9-226">Mithilfe von **Indexattribute**können Sie einen Index für eine oder mehrere Spalten erstellen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-226">You can create an index on one or more columns using the **IndexAttribute**.</span></span> <span data-ttu-id="fa5c9-227">Das Hinzufügen des-Attributs zu einer oder mehreren Eigenschaften bewirkt, dass EF den entsprechenden Index in der Datenbank erstellt, wenn die Datenbank erstellt wird, oder ein Gerüst für die entsprechenden Aufrufe von " **anateindex** " erstellt, wenn Sie Code First-Migrationen verwenden.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-227">Adding the attribute to one or more properties will cause EF to create the corresponding index in the database when it creates the database, or scaffold the corresponding **CreateIndex** calls if you are using Code First Migrations.</span></span>

<span data-ttu-id="fa5c9-228">Der folgende Code führt z. b. dazu, dass ein Index für die- `Rating` Spalte der- `Posts` Tabelle in der-Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-228">For example, the following code will result in an index being created on the `Rating` column of the `Posts` table in the database.</span></span>

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index]
        public int Rating { get; set; }
        public int BlogId { get; set; }
    }
```

<span data-ttu-id="fa5c9-229">Standardmäßig erhält der Index den Namen " \*\*IX \_ &lt; Property Name &gt; \*\* " (IX- \_ Bewertung im obigen Beispiel).</span><span class="sxs-lookup"><span data-stu-id="fa5c9-229">By default, the index will be named **IX\_&lt;property name&gt;** (IX\_Rating in the above example).</span></span> <span data-ttu-id="fa5c9-230">Sie können jedoch auch einen Namen für den Index angeben.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-230">You can also specify a name for the index though.</span></span> <span data-ttu-id="fa5c9-231">Im folgenden Beispiel wird angegeben, dass der Index benannt werden soll `PostRatingIndex` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-231">The following example specifies that the index should be named `PostRatingIndex`.</span></span>

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

<span data-ttu-id="fa5c9-232">Standardmäßig sind Indizes nicht eindeutig, Sie können jedoch den `IsUnique` benannten Parameter verwenden, um anzugeben, dass ein Index eindeutig sein muss.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-232">By default, indexes are non-unique, but you can use the `IsUnique` named parameter to specify that an index should be unique.</span></span> <span data-ttu-id="fa5c9-233">Im folgenden Beispiel wird ein eindeutiger Index für `User` den Anmelde Namen eines s eingeführt.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-233">The following example introduces a unique index on a `User`'s login name.</span></span>

``` csharp
    public class User
    {
        public int UserId { get; set; }

        [Index(IsUnique = true)]
        [StringLength(200)]
        public string Username { get; set; }

        public string DisplayName { get; set; }
    }
```

### <a name="multiple-column-indexes"></a><span data-ttu-id="fa5c9-234">Indizes mit mehreren Spalten</span><span class="sxs-lookup"><span data-stu-id="fa5c9-234">Multiple-Column Indexes</span></span>

<span data-ttu-id="fa5c9-235">Indizes, die sich über mehrere Spalten erstrecken, werden mit dem gleichen Namen in mehreren Index Anmerkungen für eine bestimmte Tabelle angegeben.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-235">Indexes that span multiple columns are specified by using the same name in multiple Index annotations for a given table.</span></span> <span data-ttu-id="fa5c9-236">Wenn Sie mehrspaltige Indizes erstellen, müssen Sie eine Reihenfolge für die Spalten im Index angeben.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-236">When you create multi-column indexes, you need to specify an order for the columns in the index.</span></span> <span data-ttu-id="fa5c9-237">Der folgende Code erstellt z. b. einen mehrspaltigen Index für `Rating` und den `BlogId` Namen " **IX \_ blogidandrating**".</span><span class="sxs-lookup"><span data-stu-id="fa5c9-237">For example, the following code creates a multi-column index on `Rating` and `BlogId` called **IX\_BlogIdAndRating**.</span></span> <span data-ttu-id="fa5c9-238">`BlogId` ist die erste Spalte im Index und `Rating` ist die zweite.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-238">`BlogId` is the first column in the index and `Rating` is the second.</span></span>

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index("IX_BlogIdAndRating", 2)]
        public int Rating { get; set; }
        [Index("IX_BlogIdAndRating", 1)]
        public int BlogId { get; set; }
    }
```

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a><span data-ttu-id="fa5c9-239">Beziehungs Attribute: invergenproperty und Fremdschlüssel</span><span class="sxs-lookup"><span data-stu-id="fa5c9-239">Relationship Attributes: InverseProperty and ForeignKey</span></span>

> [!NOTE]
> <span data-ttu-id="fa5c9-240">Diese Seite enthält Informationen zum Einrichten von Beziehungen in Ihrem Code First-Modell mithilfe von Daten Anmerkungen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-240">This page provides information about setting up relationships in your Code First model using Data Annotations.</span></span> <span data-ttu-id="fa5c9-241">Allgemeine Informationen zu Beziehungen in EF und zum Zugreifen auf und Bearbeiten von Daten mithilfe von Beziehungen finden Sie unter [Beziehungen & Navigations Eigenschaften](~/ef6/fundamentals/relationships.md). \*</span><span class="sxs-lookup"><span data-stu-id="fa5c9-241">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).\*</span></span>

<span data-ttu-id="fa5c9-242">Die Code First-Konvention kümmert sich um die gängigsten Beziehungen in Ihrem Modell. es gibt jedoch einige Fälle, in denen Sie Hilfe benötigen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-242">Code first convention will take care of the most common relationships in your model, but there are some cases where it needs help.</span></span>

<span data-ttu-id="fa5c9-243">Wenn Sie den Namen der Schlüsseleigenschaft in der `Blog` Klasse ändern, wurde ein Problem mit der Beziehung zu hergestellt `Post` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-243">Changing the name of the key property in the `Blog` class created a problem with its relationship to `Post`.</span></span> 

<span data-ttu-id="fa5c9-244">Beim Erstellen der Datenbank wird von Code zuerst die `BlogId` -Eigenschaft in der Post-Klasse angezeigt, und Sie wird von der Konvention erkannt, dass Sie mit einem Klassennamen Plus **ID**als Fremdschlüssel für die-Klasse übereinstimmt `Blog` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-244">When generating the database, code first sees the `BlogId` property in the Post class and recognizes it, by the convention that it matches a class name plus **Id**, as a foreign key to the `Blog` class.</span></span> <span data-ttu-id="fa5c9-245">In der Blog-Klasse gibt es jedoch keine- `BlogId` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-245">But there is no `BlogId` property in the blog class.</span></span> <span data-ttu-id="fa5c9-246">Die Lösung hierfür ist das Erstellen einer Navigations Eigenschaft im `Post` und die Verwendung der `ForeignKey` DataAnnotation, um Code zuerst zu helfen, zu verstehen, wie die Beziehung zwischen den beiden Klassen erstellt wird (mithilfe der- `Post.BlogId` Eigenschaft) und wie Einschränkungen in der Datenbank angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-246">The solution for this is to create a navigation property in the `Post` and use the `ForeignKey` DataAnnotation to help code first understand how to build the relationship between the two classes (using the `Post.BlogId` property) as well as how to specify constraints in the database.</span></span>

``` csharp
    public class Post
    {
            public int Id { get; set; }
            public string Title { get; set; }
            public DateTime DateCreated { get; set; }
            public string Content { get; set; }
            public int BlogId { get; set; }
            [ForeignKey("BlogId")]
            public Blog Blog { get; set; }
            public ICollection<Comment> Comments { get; set; }
    }
```

<span data-ttu-id="fa5c9-247">Die-Einschränkung in der-Datenbank zeigt eine Beziehung zwischen `InternalBlogs.PrimaryTrackingKey` und `Posts.BlogId` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-247">The constraint in the database shows a relationship between `InternalBlogs.PrimaryTrackingKey` and `Posts.BlogId`.</span></span> 

![Beziehung zwischen internalblogs. primarytrackingkey und Posts. BlogId](~/ef6/media/jj591583-figure09.png)

<span data-ttu-id="fa5c9-249">`InverseProperty`Wird verwendet, wenn mehrere Beziehungen zwischen Klassen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-249">The `InverseProperty` is used when you have multiple relationships between classes.</span></span>

<span data-ttu-id="fa5c9-250">In der- `Post` Klasse sollten Sie nachverfolgen können, wer einen Blogbeitrag geschrieben hat und wer ihn bearbeitet hat.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-250">In the `Post` class, you may want to keep track of who wrote a blog post as well as who edited it.</span></span> <span data-ttu-id="fa5c9-251">Hier sind zwei neue Navigations Eigenschaften für die Post-Klasse.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-251">Here are two new navigation properties for the Post class.</span></span>

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

<span data-ttu-id="fa5c9-252">Sie müssen auch in der-Klasse hinzufügen, auf die `Person` von diesen Eigenschaften verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-252">You’ll also need to add in the `Person` class referenced by these properties.</span></span> <span data-ttu-id="fa5c9-253">Die `Person` -Klasse verfügt über Navigations Eigenschaften zurück zum `Post` , eine für alle von der Person geschriebenen Beiträge und eine für alle von dieser Person aktualisierten Beiträge.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-253">The `Person` class has navigation properties back to the `Post`, one for all of the posts written by the person and one for all of the posts updated by that person.</span></span>

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

<span data-ttu-id="fa5c9-254">Code First kann die Eigenschaften in den beiden Klassen nicht alleine zuordnen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-254">Code first is not able to match up the properties in the two classes on its own.</span></span> <span data-ttu-id="fa5c9-255">Die Datenbanktabelle für `Posts` sollte über einen Fremdschlüssel für die `CreatedBy` Person und einen für die `UpdatedBy` Person verfügen, aber Code First erstellt vier Fremdschlüssel Eigenschaften: **Person \_ ID**, **Person \_ Id1**, **kreatedby \_ ID** und **updatedby \_ ID**.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-255">The database table for `Posts` should have one foreign key for the `CreatedBy` person and one for the `UpdatedBy` person but code first will create four foreign key properties: **Person\_Id**, **Person\_Id1**, **CreatedBy\_Id** and **UpdatedBy\_Id**.</span></span>

![Postet eine Tabelle mit zusätzlichen Fremdschlüsseln.](~/ef6/media/jj591583-figure10.png)

<span data-ttu-id="fa5c9-257">Um diese Probleme zu beheben, können Sie die-Anmerkung verwenden, `InverseProperty` um die Ausrichtung der Eigenschaften anzugeben.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-257">To fix these problems, you can use the `InverseProperty` annotation to specify the alignment of the properties.</span></span>

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

<span data-ttu-id="fa5c9-258">Da die- `PostsWritten` Eigenschaft in Person weiß, dass dies auf den- `Post` Typ verweist, wird die Beziehung zu erstellt `Post.CreatedBy` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-258">Because the `PostsWritten` property in Person knows that this refers to the `Post` type, it will build the relationship to `Post.CreatedBy`.</span></span> <span data-ttu-id="fa5c9-259">Ebenso `PostsUpdated` wird eine Verbindung mit hergestellt `Post.UpdatedBy` .</span><span class="sxs-lookup"><span data-stu-id="fa5c9-259">Similarly, `PostsUpdated` will be connected to `Post.UpdatedBy`.</span></span> <span data-ttu-id="fa5c9-260">Und Code First erstellt nicht die zusätzlichen Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-260">And code first will not create the extra foreign keys.</span></span>

![Postet Tabelle ohne zusätzliche Fremdschlüssel](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a><span data-ttu-id="fa5c9-262">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="fa5c9-262">Summary</span></span>

<span data-ttu-id="fa5c9-263">Mit DataAnnotations können Sie nicht nur die Client-und serverseitige Validierung in Ihren Code First-Klassen beschreiben, sondern Sie können auch die Annahmen, die Code First über Ihre Klassen auf der Grundlage seiner Konventionen treffen wird, verbessern und sogar korrigieren.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-263">DataAnnotations not only let you describe client and server side validation in your code first classes, but they also allow you to enhance and even correct the assumptions that code first will make about your classes based on its conventions.</span></span> <span data-ttu-id="fa5c9-264">Mit DataAnnotations können Sie nicht nur die Generierung von Datenbankschemas steuern, sondern auch die Code First-Klassen einer bereits vorhandenen Datenbank zuordnen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-264">With DataAnnotations you can not only drive database schema generation, but you can also map your code first classes to a pre-existing database.</span></span>

<span data-ttu-id="fa5c9-265">Obwohl Sie sehr flexibel sind, beachten Sie, dass DataAnnotations nur die am häufigsten benötigten Konfigurationsänderungen bereitstellen, die Sie an Ihren Code First-Klassen vornehmen können.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-265">While they are very flexible, keep in mind that DataAnnotations provide only the most commonly needed configuration changes you can make on your code first classes.</span></span> <span data-ttu-id="fa5c9-266">Um die Klassen für einige der edgefälle zu konfigurieren, sollten Sie sich den alternativen Konfigurations Mechanismus Code First der fließend-API ansehen.</span><span class="sxs-lookup"><span data-stu-id="fa5c9-266">To configure your classes for some of the edge cases, you should look to the alternate configuration mechanism, Code First’s Fluent API .</span></span>
