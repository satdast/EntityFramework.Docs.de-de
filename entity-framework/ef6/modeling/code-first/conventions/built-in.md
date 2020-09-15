---
title: Code First Konventionen-EF6
description: Code First Konventionen in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/built-in
ms.openlocfilehash: 71e865d674d6bef7aaf65d95abe1b1add96d890f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072603"
---
# <a name="code-first-conventions"></a><span data-ttu-id="d78d3-103">Code First Konventionen</span><span class="sxs-lookup"><span data-stu-id="d78d3-103">Code First Conventions</span></span>
<span data-ttu-id="d78d3-104">Code First ermöglicht es Ihnen, ein Modell mithilfe von c#-oder Visual Basic .NET-Klassen zu beschreiben.</span><span class="sxs-lookup"><span data-stu-id="d78d3-104">Code First enables you to describe a model by using C# or Visual Basic .NET classes.</span></span> <span data-ttu-id="d78d3-105">Die grundlegende Form des Modells wird mithilfe von Konventionen erkannt.</span><span class="sxs-lookup"><span data-stu-id="d78d3-105">The basic shape of the model is detected by using conventions.</span></span> <span data-ttu-id="d78d3-106">Konventionen sind Sätze von Regeln, die verwendet werden, um ein konzeptionelles Modell, das auf Klassendefinitionen basiert, bei der Arbeit mit Code First automatisch zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="d78d3-106">Conventions are sets of rules that are used to automatically configure a conceptual model based on class definitions when working with Code First.</span></span> <span data-ttu-id="d78d3-107">Die Konventionen werden im Namespace System. Data. Entity. modelconfiguration. Conventions definiert.</span><span class="sxs-lookup"><span data-stu-id="d78d3-107">The conventions are defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>  

<span data-ttu-id="d78d3-108">Sie können Ihr Modell mithilfe von Daten Anmerkungen oder der fließenden API weiter konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="d78d3-108">You can further configure your model by using data annotations or the fluent API.</span></span> <span data-ttu-id="d78d3-109">Die-Konfiguration wird durch die fließende API, gefolgt von Daten Anmerkungen und then-Konventionen, Vorrang eingeräumt.</span><span class="sxs-lookup"><span data-stu-id="d78d3-109">Precedence is given to configuration through the fluent API followed by data annotations and then conventions.</span></span> <span data-ttu-id="d78d3-110">Weitere Informationen finden Sie unter [Daten Anmerkungen](xref:ef6/modeling/code-first/data-annotations), [fließende API-Beziehungen](xref:ef6/modeling/code-first/fluent/relationships), [fließende API-Typen & Eigenschaften](xref:ef6/modeling/code-first/fluent/types-and-properties) und eine [fließende API mit VB.net](xref:ef6/modeling/code-first/fluent/vb).</span><span class="sxs-lookup"><span data-stu-id="d78d3-110">For more information see [Data Annotations](xref:ef6/modeling/code-first/data-annotations), [Fluent API - Relationships](xref:ef6/modeling/code-first/fluent/relationships), [Fluent API - Types & Properties](xref:ef6/modeling/code-first/fluent/types-and-properties) and [Fluent API with VB.NET](xref:ef6/modeling/code-first/fluent/vb).</span></span>  

<span data-ttu-id="d78d3-111">Eine ausführliche Liste der Code First Konventionen finden Sie in der [API-Dokumentation](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span><span class="sxs-lookup"><span data-stu-id="d78d3-111">A detailed list of Code First conventions is available in the [API Documentation](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span> <span data-ttu-id="d78d3-112">Dieses Thema enthält eine Übersicht über die Konventionen, die von Code First verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d78d3-112">This topic provides an overview of the conventions used by Code First.</span></span>  

## <a name="type-discovery"></a><span data-ttu-id="d78d3-113">Typanmittlung</span><span class="sxs-lookup"><span data-stu-id="d78d3-113">Type Discovery</span></span>  

<span data-ttu-id="d78d3-114">Wenn Sie Code First Entwicklung verwenden, schreiben Sie in der Regel .NET Framework Klassen, die ihr konzeptionelles (Domänen-) Modell definieren.</span><span class="sxs-lookup"><span data-stu-id="d78d3-114">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="d78d3-115">Zusätzlich zum Definieren der Klassen müssen Sie **dbcontext** auch darüber informieren, welche Typen Sie in das Modell einschließen möchten.</span><span class="sxs-lookup"><span data-stu-id="d78d3-115">In addition to defining the classes, you also need to let **DbContext** know which types you want to include in the model.</span></span> <span data-ttu-id="d78d3-116">Zu diesem Zweck definieren Sie eine Kontext Klasse, die von **dbcontext** abgeleitet ist und für die Typen, die Sie als Teil des Modells verwenden möchten, **dbset** -Eigenschaften verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="d78d3-116">To do this, you define a context class that derives from **DbContext** and exposes **DbSet** properties for the types that you want to be part of the model.</span></span> <span data-ttu-id="d78d3-117">Code First diese Typen einschließen und auch alle referenzierten Typen abrufen, auch wenn die Typen, auf die verwiesen wird, in einer anderen Assembly definiert sind.</span><span class="sxs-lookup"><span data-stu-id="d78d3-117">Code First will include these types and also will pull in any referenced types, even if the referenced types are defined in a different assembly.</span></span>  

<span data-ttu-id="d78d3-118">Wenn Ihre Typen an einer Vererbungs Hierarchie teilnehmen, genügt es, eine **dbset** -Eigenschaft für die Basisklasse zu definieren, und die abgeleiteten Typen werden automatisch eingeschlossen, wenn Sie sich in derselben Assembly wie die Basisklasse befinden.</span><span class="sxs-lookup"><span data-stu-id="d78d3-118">If your types participate in an inheritance hierarchy, it is enough to define a **DbSet** property for the base class, and the derived types will be automatically included, if they are in the same assembly as the base class.</span></span>  

<span data-ttu-id="d78d3-119">Im folgenden Beispiel wird nur eine **dbset** -Eigenschaft für die **SchoolEntities** -Klasse (**Abteilungen**) definiert.</span><span class="sxs-lookup"><span data-stu-id="d78d3-119">In the following example, there is only one **DbSet** property defined on the **SchoolEntities** class (**Departments**).</span></span> <span data-ttu-id="d78d3-120">Code First verwendet diese Eigenschaft zum Ermitteln und Abrufen aller referenzierten Typen.</span><span class="sxs-lookup"><span data-stu-id="d78d3-120">Code First uses this property to discover and pull in any referenced types.</span></span>  

``` csharp
public class SchoolEntities : DbContext
{
    public DbSet<Department> Departments { get; set; }
}

public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}

public partial class OnlineCourse : Course
{
    public string URL { get; set; }
}

public partial class OnsiteCourse : Course
{
    public string Location { get; set; }
    public string Days { get; set; }
    public System.DateTime Time { get; set; }
}
```  

<span data-ttu-id="d78d3-121">Wenn Sie einen Typ aus dem Modell ausschließen möchten, verwenden Sie das Attribut " **notmapping** " oder die " **dbmodelbuilder. Ignore** "-API.</span><span class="sxs-lookup"><span data-stu-id="d78d3-121">If you want to exclude a type from the model, use the **NotMapped** attribute or the **DbModelBuilder.Ignore** fluent API.</span></span>  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a><span data-ttu-id="d78d3-122">Primärschlüssel Konvention</span><span class="sxs-lookup"><span data-stu-id="d78d3-122">Primary Key Convention</span></span>  

<span data-ttu-id="d78d3-123">Code First ab, dass eine Eigenschaft ein Primärschlüssel ist, wenn eine Eigenschaft in einer Klasse mit dem Namen "ID" (ohne Beachtung der Groß-/Kleinschreibung) oder dem Klassennamen gefolgt von "ID" benannt wird.</span><span class="sxs-lookup"><span data-stu-id="d78d3-123">Code First infers that a property is a primary key if a property on a class is named “ID” (not case sensitive), or the class name followed by "ID".</span></span> <span data-ttu-id="d78d3-124">Wenn der Typ der Primärschlüssel Eigenschaft numerisch oder GUID ist, wird er als Identitäts Spalte konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="d78d3-124">If the type of the primary key property is numeric or GUID it will be configured as an identity column.</span></span>  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a><span data-ttu-id="d78d3-125">Beziehungs Konvention</span><span class="sxs-lookup"><span data-stu-id="d78d3-125">Relationship Convention</span></span>  

<span data-ttu-id="d78d3-126">Im Entity Framework bieten Navigationseigenschaften eine Möglichkeit, in einer Beziehung zwischen zwei Entitätstypen zu navigieren.</span><span class="sxs-lookup"><span data-stu-id="d78d3-126">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span> <span data-ttu-id="d78d3-127">Jedes Objekt kann für jede Beziehung, an der es beteiligt ist, über eine Navigationseigenschaft verfügen.</span><span class="sxs-lookup"><span data-stu-id="d78d3-127">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="d78d3-128">Mithilfe von Navigations Eigenschaften können Sie Beziehungen in beiden Richtungen navigieren und verwalten, wobei entweder ein Verweis Objekt (wenn die Multiplizität entweder 1 oder 0 ist) oder eine Auflistung (wenn die Multiplizität viele ist) zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="d78d3-128">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="d78d3-129">Code First leitet Beziehungen auf der Grundlage der Navigations Eigenschaften ab, die für Ihre Typen definiert sind.</span><span class="sxs-lookup"><span data-stu-id="d78d3-129">Code First infers relationships based on the navigation properties defined on your types.</span></span>  

<span data-ttu-id="d78d3-130">Zusätzlich zu den Navigations Eigenschaften empfiehlt es sich, Fremdschlüssel Eigenschaften für die Typen einzuschließen, die abhängige Objekte darstellen.</span><span class="sxs-lookup"><span data-stu-id="d78d3-130">In addition to navigation properties, we recommend that you include foreign key properties on the types that represent dependent objects.</span></span> <span data-ttu-id="d78d3-131">Jede Eigenschaft mit demselben Datentyp wie die Prinzipal-Primärschlüssel Eigenschaft und mit einem Namen, der einem der folgenden Formate folgt, stellt einen Fremdschlüssel für die Beziehung dar: " \<navigation property name\> \<principal primary key property name\> ", " \<principal class name\> \<primary key property name\> " oder " \<principal primary key property name\> ".</span><span class="sxs-lookup"><span data-stu-id="d78d3-131">Any property with the same data type as the principal primary key property and with a name that follows one of the following formats represents a foreign key for the relationship: '\<navigation property name\>\<principal primary key property name\>', '\<principal class name\>\<primary key property name\>', or '\<principal primary key property name\>'.</span></span> <span data-ttu-id="d78d3-132">Wenn mehrere Übereinstimmungen gefunden werden, wird in der oben aufgeführten Reihenfolge Vorrang eingeräumt.</span><span class="sxs-lookup"><span data-stu-id="d78d3-132">If multiple matches are found then precedence is given in the order listed above.</span></span> <span data-ttu-id="d78d3-133">Bei der Fremdschlüssel Erkennung wird die Groß-/Kleinschreibung</span><span class="sxs-lookup"><span data-stu-id="d78d3-133">Foreign key detection is not case sensitive.</span></span> <span data-ttu-id="d78d3-134">Wenn eine Fremdschlüssel Eigenschaft erkannt wird, leitet Code First die Multiplizität der Beziehung basierend auf der NULL-Zulässigkeit des fremd Schlüssels ab.</span><span class="sxs-lookup"><span data-stu-id="d78d3-134">When a foreign key property is detected, Code First infers the multiplicity of the relationship based on the nullability of the foreign key.</span></span> <span data-ttu-id="d78d3-135">Wenn die Eigenschaft NULL-Werte zulässt, wird die Beziehung als optional registriert. Andernfalls wird die Beziehung nach Bedarf registriert.</span><span class="sxs-lookup"><span data-stu-id="d78d3-135">If the property is nullable then the relationship is registered as optional; otherwise the relationship is registered as required.</span></span>  

<span data-ttu-id="d78d3-136">Wenn ein Fremdschlüssel für die abhängige Entität keine NULL-Werte zulässt, legt Code First den Löschvorgang für die Beziehung fest.</span><span class="sxs-lookup"><span data-stu-id="d78d3-136">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="d78d3-137">Wenn ein Fremdschlüssel für die abhängige Entität NULL-Werte zulässt, legt Code First für die Beziehung keine Lösch Weitergabe fest, und wenn der Prinzipal gelöscht wird, wird der Fremdschlüssel auf NULL festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d78d3-137">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span> <span data-ttu-id="d78d3-138">Das von der Konvention erkannte Multiplizität und Lösch Verhalten können überschrieben werden, indem die fließende API verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="d78d3-138">The multiplicity and cascade delete behavior detected by convention can be overridden by using the fluent API.</span></span>  

<span data-ttu-id="d78d3-139">Im folgenden Beispiel werden die Navigations Eigenschaften und ein Fremdschlüssel verwendet, um die Beziehung zwischen den Klassen "Department" und "Course" zu definieren.</span><span class="sxs-lookup"><span data-stu-id="d78d3-139">In the following example the navigation properties and a foreign key are used to define the relationship between the Department and Course classes.</span></span>  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}
```  

> [!NOTE]
> <span data-ttu-id="d78d3-140">Wenn Sie über mehrere Beziehungen zwischen denselben Typen verfügen (z. b. angenommen, Sie definieren die **Personen** -und **Buch** Klassen, in denen die **Person** -Klasse die Navigations Eigenschaften **reviewedbooks** und **authoredbooks** enthält und die **Book** -Klasse die Navigations Eigenschaften **Author** und **Reviewer** enthält), müssen Sie die Beziehungen mithilfe von Daten Anmerkungen oder der fließenden API manuell konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="d78d3-140">If you have multiple relationships between the same types (for example, suppose you define the **Person** and **Book** classes, where the **Person** class contains the **ReviewedBooks** and **AuthoredBooks** navigation properties and the **Book** class contains the **Author** and **Reviewer** navigation properties) you need to manually configure the relationships by using Data Annotations or the fluent API.</span></span> <span data-ttu-id="d78d3-141">Weitere Informationen finden Sie unter [Daten Anmerkungen-Beziehungen](xref:ef6/modeling/code-first/data-annotations) und [fließende API-Beziehungen](xref:ef6/modeling/code-first/fluent/relationships).</span><span class="sxs-lookup"><span data-stu-id="d78d3-141">For more information, see [Data Annotations - Relationships](xref:ef6/modeling/code-first/data-annotations) and [Fluent API - Relationships](xref:ef6/modeling/code-first/fluent/relationships).</span></span>  

## <a name="complex-types-convention"></a><span data-ttu-id="d78d3-142">Komplexe Typen Konvention</span><span class="sxs-lookup"><span data-stu-id="d78d3-142">Complex Types Convention</span></span>  

<span data-ttu-id="d78d3-143">Wenn Code First eine Klassendefinition ermittelt, in der ein Primärschlüssel nicht abgeleitet werden kann, und kein Primärschlüssel durch Daten Anmerkungen oder die fließende API registriert ist, wird der Typ automatisch als komplexer Typ registriert.</span><span class="sxs-lookup"><span data-stu-id="d78d3-143">When Code First discovers a class definition where a primary key cannot be inferred, and no primary key is registered through data annotations or the fluent API, then the type is automatically registered as a complex type.</span></span> <span data-ttu-id="d78d3-144">Die Erkennung komplexer Typen erfordert auch, dass der Typ keine Eigenschaften hat, die auf Entitäts Typen verweisen, und auf die von einer Auflistungs Eigenschaft eines anderen Typs nicht verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="d78d3-144">Complex type detection also requires that the type does not have properties that reference entity types and is not referenced from a collection property on another type.</span></span> <span data-ttu-id="d78d3-145">Wenn die folgenden Klassendefinitionen angegeben werden, wird Code First abgeleitet, dass die **Details** ein komplexer Typ ist, da er keinen Primärschlüssel aufweist.</span><span class="sxs-lookup"><span data-stu-id="d78d3-145">Given the following class definitions Code First would infer that **Details** is a complex type because it has no primary key.</span></span>  

``` csharp
public partial class OnsiteCourse : Course
{
    public OnsiteCourse()
    {
        Details = new Details();
    }

    public Details Details { get; set; }
}

public class Details
{
    public System.DateTime Time { get; set; }
    public string Location { get; set; }
    public string Days { get; set; }
}
```  

## <a name="connection-string-convention"></a><span data-ttu-id="d78d3-146">Verbindungs Zeichen folgen Konvention</span><span class="sxs-lookup"><span data-stu-id="d78d3-146">Connection String Convention</span></span>  

<span data-ttu-id="d78d3-147">Informationen zu den Konventionen, die dbcontext zum Ermitteln der zu verwendenden Verbindung verwendet, finden Sie unter [Verbindungen und Modelle](xref:ef6/fundamentals/configuring/connection-strings).</span><span class="sxs-lookup"><span data-stu-id="d78d3-147">To learn about the conventions that DbContext uses to discover the connection to use see [Connections and Models](xref:ef6/fundamentals/configuring/connection-strings).</span></span>  

## <a name="removing-conventions"></a><span data-ttu-id="d78d3-148">Entfernen von Konventionen</span><span class="sxs-lookup"><span data-stu-id="d78d3-148">Removing Conventions</span></span>  

<span data-ttu-id="d78d3-149">Sie können alle Konventionen entfernen, die im System. Data. Entity. modelconfiguration. Conventions-Namespace definiert sind.</span><span class="sxs-lookup"><span data-stu-id="d78d3-149">You can remove any of the conventions defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span> <span data-ttu-id="d78d3-150">Im folgenden Beispiel wird **pluralizingtablenameconvention**entfernt.</span><span class="sxs-lookup"><span data-stu-id="d78d3-150">The following example removes **PluralizingTableNameConvention**.</span></span>  

``` csharp
public class SchoolEntities : DbContext
{
     . . .

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        // Configure Code First to ignore PluralizingTableName convention
        // If you keep this convention, the generated tables  
        // will have pluralized names.
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}
```  

## <a name="custom-conventions"></a><span data-ttu-id="d78d3-151">Benutzerdefinierte Konventionen</span><span class="sxs-lookup"><span data-stu-id="d78d3-151">Custom Conventions</span></span>  

<span data-ttu-id="d78d3-152">Benutzerdefinierte Konventionen werden ab EF6 unterstützt.</span><span class="sxs-lookup"><span data-stu-id="d78d3-152">Custom conventions are supported in EF6 onwards.</span></span> <span data-ttu-id="d78d3-153">Weitere Informationen finden Sie unter [benutzerdefinierte Code First Konventionen](xref:ef6/modeling/code-first/conventions/custom).</span><span class="sxs-lookup"><span data-stu-id="d78d3-153">For more information see [Custom Code First Conventions](xref:ef6/modeling/code-first/conventions/custom).</span></span>
