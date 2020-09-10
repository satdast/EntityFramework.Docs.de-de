---
title: Fließende API-Beziehungen EF6
description: Fließende API-Beziehungen in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: fd73b4f8-16d5-40f1-9640-885ceafe67a1
uid: ef6/modeling/code-first/fluent/relationships
ms.openlocfilehash: 8cc56f7341df6da7f60f649308ea7042ef23b537
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617000"
---
# <a name="fluent-api---relationships"></a><span data-ttu-id="16f35-103">Fließende API-Beziehungen</span><span class="sxs-lookup"><span data-stu-id="16f35-103">Fluent API - Relationships</span></span>
> [!NOTE]
> <span data-ttu-id="16f35-104">Diese Seite enthält Informationen zum Einrichten von Beziehungen in Ihrem Code First-Modell mithilfe der fließend-API.</span><span class="sxs-lookup"><span data-stu-id="16f35-104">This page provides information about setting up relationships in your Code First model using the fluent API.</span></span> <span data-ttu-id="16f35-105">Allgemeine Informationen zu Beziehungen in EF und zum Zugreifen auf und Bearbeiten von Daten mithilfe von Beziehungen finden Sie unter [Beziehungen & Navigations Eigenschaften](xref:ef6/fundamentals/relationships).</span><span class="sxs-lookup"><span data-stu-id="16f35-105">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](xref:ef6/fundamentals/relationships).</span></span>  

<span data-ttu-id="16f35-106">Beim Arbeiten mit Code First definieren Sie das Modell, indem Sie Ihre Domänen-CLR-Klassen definieren.</span><span class="sxs-lookup"><span data-stu-id="16f35-106">When working with Code First, you define your model by defining your domain CLR classes.</span></span> <span data-ttu-id="16f35-107">Standardmäßig verwendet Entity Framework die Code First Konventionen, um die Klassen dem Datenbankschema zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="16f35-107">By default, Entity Framework uses the Code First conventions to map your classes to the database schema.</span></span> <span data-ttu-id="16f35-108">Wenn Sie die Benennungs Konventionen für Code First verwenden, können Sie sich in den meisten Fällen auf Code First verlassen, um Beziehungen zwischen den Tabellen basierend auf den Fremdschlüsseln und Navigations Eigenschaften einzurichten, die Sie für die Klassen definieren.</span><span class="sxs-lookup"><span data-stu-id="16f35-108">If you use the Code First naming conventions, in most cases you can rely on Code First to set up relationships between your tables based on the foreign keys and navigation properties that you define on the classes.</span></span> <span data-ttu-id="16f35-109">Wenn Sie den Konventionen beim Definieren der Klassen nicht folgen, oder wenn Sie die Funktionsweise der Konventionen ändern möchten, können Sie die-Klasse mit der flüssigen API oder Daten Anmerkungen so konfigurieren, dass Code First die Beziehungen zwischen den Tabellen zuordnen kann.</span><span class="sxs-lookup"><span data-stu-id="16f35-109">If you do not follow the conventions when defining your classes, or if you want to change the way the conventions work, you can use the fluent API or data annotations to configure your classes so Code First can map the relationships between your tables.</span></span>  

## <a name="introduction"></a><span data-ttu-id="16f35-110">Einführung</span><span class="sxs-lookup"><span data-stu-id="16f35-110">Introduction</span></span>  

<span data-ttu-id="16f35-111">Beim Konfigurieren einer Beziehung mit der flüssigen API beginnen Sie mit der entitytypeconfiguration-Instanz und verwenden dann die hasrequired-, hasoptional-oder hasMany-Methode, um den Beziehungstyp anzugeben, an dem diese Entität beteiligt ist.</span><span class="sxs-lookup"><span data-stu-id="16f35-111">When configuring a relationship with the fluent API, you start with the EntityTypeConfiguration instance and then use the HasRequired, HasOptional, or HasMany method to specify the type of relationship this entity participates in.</span></span> <span data-ttu-id="16f35-112">Die hasrequired-Methode und die hasoptional-Methode akzeptieren einen Lambda-Ausdruck, der eine Verweis Navigations Eigenschaft darstellt.</span><span class="sxs-lookup"><span data-stu-id="16f35-112">The HasRequired and HasOptional methods take a lambda expression that represents a reference navigation property.</span></span> <span data-ttu-id="16f35-113">Die hasMany-Methode nimmt einen Lambda-Ausdruck an, der eine Auflistungs Navigations Eigenschaft darstellt.</span><span class="sxs-lookup"><span data-stu-id="16f35-113">The HasMany method takes a lambda expression that represents a collection navigation property.</span></span> <span data-ttu-id="16f35-114">Anschließend können Sie mithilfe der Methoden withrequired, withoptional und withmany eine umgekehrte Navigations Eigenschaft konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="16f35-114">You can then configure an inverse navigation property by using the WithRequired, WithOptional, and WithMany methods.</span></span> <span data-ttu-id="16f35-115">Diese Methoden verfügen über über Ladungen, die keine Argumente annehmen und zum Angeben der Kardinalität mit unidirektionaler Navigation verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="16f35-115">These methods have overloads that do not take arguments and can be used to specify cardinality with unidirectional navigations.</span></span>  

<span data-ttu-id="16f35-116">Anschließend können Sie die Fremdschlüssel Eigenschaften mithilfe der hasforeign nkey-Methode konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="16f35-116">You can then configure foreign key properties by using the HasForeignKey method.</span></span> <span data-ttu-id="16f35-117">Diese Methode verwendet einen Lambda-Ausdruck, der die Eigenschaft darstellt, die als Fremdschlüssel verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="16f35-117">This method takes a lambda expression that represents the property to be used as the foreign key.</span></span>  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a><span data-ttu-id="16f35-118">Konfigurieren einer erforderlichen und optionalen Beziehung (One-to – Zero-or-one)</span><span class="sxs-lookup"><span data-stu-id="16f35-118">Configuring a Required-to-Optional Relationship (One-to–Zero-or-One)</span></span>  

<span data-ttu-id="16f35-119">Im folgenden Beispiel wird eine 1:0-oder 1-Beziehung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="16f35-119">The following example configures a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="16f35-120">Officezuweisung verfügt über die InstructorID-Eigenschaft, bei der es sich um einen Primärschlüssel und einen Fremdschlüssel handelt, da der Name der Eigenschaft nicht der Konvention folgt, die die Haskey-Methode verwendet, um den Primärschlüssel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="16f35-120">The OfficeAssignment has the InstructorID property that is a primary key and a foreign key, because the name of the property does not follow the convention the HasKey method is used to configure the primary key.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a><span data-ttu-id="16f35-121">Konfigurieren einer Beziehung, bei der beide Enden (eins zu eins) erforderlich sind</span><span class="sxs-lookup"><span data-stu-id="16f35-121">Configuring a Relationship Where Both Ends Are Required (One-to-One)</span></span>  

<span data-ttu-id="16f35-122">In den meisten Fällen können Entity Framework ableiten, welcher Typ der abhängige Typ ist und welcher der Prinzipal in einer Beziehung ist.</span><span class="sxs-lookup"><span data-stu-id="16f35-122">In most cases Entity Framework can infer which type is the dependent and which is the principal in a relationship.</span></span> <span data-ttu-id="16f35-123">Wenn jedoch beide Enden der Beziehung erforderlich sind oder beide Seiten optional sind Entity Framework den abhängigen und den Prinzipal nicht identifizieren können.</span><span class="sxs-lookup"><span data-stu-id="16f35-123">However, when both ends of the relationship are required or both sides are optional Entity Framework cannot identify the dependent and principal.</span></span> <span data-ttu-id="16f35-124">Wenn beide Enden der Beziehung erforderlich sind, verwenden Sie withrequirements dprincipal oder withrequirements ddependent nach der hasrequired-Methode.</span><span class="sxs-lookup"><span data-stu-id="16f35-124">When both ends of the relationship are required, use WithRequiredPrincipal or WithRequiredDependent after the HasRequired method.</span></span> <span data-ttu-id="16f35-125">Wenn beide Enden der Beziehung optional sind, verwenden Sie withoptionalprincipal oder withoptionaldependent nach der hasoptionalen-Methode.</span><span class="sxs-lookup"><span data-stu-id="16f35-125">When both ends of the relationship are optional, use WithOptionalPrincipal or WithOptionalDependent after the HasOptional method.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a><span data-ttu-id="16f35-126">Konfigurieren einer m:n-Beziehung</span><span class="sxs-lookup"><span data-stu-id="16f35-126">Configuring a Many-to-Many Relationship</span></span>  

<span data-ttu-id="16f35-127">Der folgende Code konfiguriert eine m:n-Beziehung zwischen den Kurs-und Dozenten Typen.</span><span class="sxs-lookup"><span data-stu-id="16f35-127">The following code configures a many-to-many relationship between the Course and Instructor types.</span></span> <span data-ttu-id="16f35-128">Im folgenden Beispiel werden die standardmäßigen Code First Konventionen verwendet, um eine jointabelle zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="16f35-128">In the following example, the default Code First conventions are used to create a join table.</span></span> <span data-ttu-id="16f35-129">Daher wird die Tabelle CourseInstructor mit Course_CourseID-und Instructor_InstructorID-Spalten erstellt.</span><span class="sxs-lookup"><span data-stu-id="16f35-129">As a result the CourseInstructor table is created with Course_CourseID and Instructor_InstructorID columns.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

<span data-ttu-id="16f35-130">Wenn Sie den Namen der jointabelle und die Namen der Spalten in der Tabelle angeben möchten, müssen Sie mit der Map-Methode eine zusätzliche Konfiguration durchführen.</span><span class="sxs-lookup"><span data-stu-id="16f35-130">If you want to specify the join table name and the names of the columns in the table you need to do additional configuration by using the Map method.</span></span> <span data-ttu-id="16f35-131">Der folgende Code generiert die CourseInstructor-Tabelle mit den Spalten CourseID und InstructorID.</span><span class="sxs-lookup"><span data-stu-id="16f35-131">The following code generates the CourseInstructor table with CourseID and InstructorID columns.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
    .Map(m =>
    {
        m.ToTable("CourseInstructor");
        m.MapLeftKey("CourseID");
        m.MapRightKey("InstructorID");
    });
```  

## <a name="configuring-a-relationship-with-one-navigation-property"></a><span data-ttu-id="16f35-132">Konfigurieren einer Beziehung mit einer Navigations Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="16f35-132">Configuring a Relationship with One Navigation Property</span></span>  

<span data-ttu-id="16f35-133">Eine eindirektionale (auch als unidirektional bezeichnete Beziehung bezeichnet) ist, wenn eine Navigations Eigenschaft nur für eine der Enden der Beziehung definiert wird, nicht für beides.</span><span class="sxs-lookup"><span data-stu-id="16f35-133">A one-directional (also called unidirectional) relationship is when a navigation property is defined on only one of the relationship ends and not on both.</span></span> <span data-ttu-id="16f35-134">Gemäß der Konvention interpretiert Code First immer eine unidirektionale Beziehung als 1: n-Beziehung.</span><span class="sxs-lookup"><span data-stu-id="16f35-134">By convention, Code First always interprets a unidirectional relationship as one-to-many.</span></span> <span data-ttu-id="16f35-135">Wenn Sie z. b. eine eins-zu-eins-Beziehung zwischen "Instructor" und "officezuweisung" wünschen, bei der nur eine Navigations Eigenschaft für den Typ "Instructor" vorhanden ist, müssen Sie die fließende API verwenden, um diese Beziehung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="16f35-135">For example, if you want a one-to-one relationship between Instructor and OfficeAssignment, where you have a navigation property on only the Instructor type, you need to use the fluent API to configure this relationship.</span></span>  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a><span data-ttu-id="16f35-136">Aktivieren von Cascade Delete</span><span class="sxs-lookup"><span data-stu-id="16f35-136">Enabling Cascade Delete</span></span>  

<span data-ttu-id="16f35-137">Mithilfe der Methode "willcascadeondelete" können Sie die Lösch Weitergabe für eine Beziehung konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="16f35-137">You can configure cascade delete on a relationship by using the WillCascadeOnDelete method.</span></span> <span data-ttu-id="16f35-138">Wenn ein Fremdschlüssel für die abhängige Entität keine NULL-Werte zulässt, legt Code First den Löschvorgang für die Beziehung fest.</span><span class="sxs-lookup"><span data-stu-id="16f35-138">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="16f35-139">Wenn ein Fremdschlüssel für die abhängige Entität NULL-Werte zulässt, legt Code First für die Beziehung keine Lösch Weitergabe fest, und wenn der Prinzipal gelöscht wird, wird der Fremdschlüssel auf NULL festgelegt.</span><span class="sxs-lookup"><span data-stu-id="16f35-139">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span>  

<span data-ttu-id="16f35-140">Sie können diese kaskadierenden Lösch Konventionen mithilfe von entfernen:</span><span class="sxs-lookup"><span data-stu-id="16f35-140">You can remove these cascade delete conventions by using:</span></span>  

<span data-ttu-id="16f35-141">Modelbuilder. Conventions. Remove \<OneToManyCascadeDeleteConvention\> ()</span><span class="sxs-lookup"><span data-stu-id="16f35-141">modelBuilder.Conventions.Remove\<OneToManyCascadeDeleteConvention\>()</span></span>  
<span data-ttu-id="16f35-142">Modelbuilder. Conventions. Remove \<ManyToManyCascadeDeleteConvention\> ()</span><span class="sxs-lookup"><span data-stu-id="16f35-142">modelBuilder.Conventions.Remove\<ManyToManyCascadeDeleteConvention\>()</span></span>  

<span data-ttu-id="16f35-143">Der folgende Code konfiguriert die Beziehung als erforderlich und deaktiviert die Lösch Weitergabe.</span><span class="sxs-lookup"><span data-stu-id="16f35-143">The following code configures the relationship to be required and then disables cascade delete.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a><span data-ttu-id="16f35-144">Konfigurieren eines zusammengesetzten fremd Schlüssels</span><span class="sxs-lookup"><span data-stu-id="16f35-144">Configuring a Composite Foreign Key</span></span>  

<span data-ttu-id="16f35-145">Wenn der Primärschlüssel des Abteilungs Typs aus "DepartmentID" und "Name Properties" besteht, würden Sie den Primärschlüssel für die Abteilung und den Fremdschlüssel für die Kurs Typen wie folgt konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="16f35-145">If the primary key on the Department type consisted of DepartmentID and Name properties, you would configure the primary key for the Department and the foreign key on the Course types as follows:</span></span>  

``` csharp
// Composite primary key
modelBuilder.Entity<Department>()
.HasKey(d => new { d.DepartmentID, d.Name });

// Composite foreign key
modelBuilder.Entity<Course>()  
    .HasRequired(c => c.Department)  
    .WithMany(d => d.Courses)
    .HasForeignKey(d => new { d.DepartmentID, d.DepartmentName });
```  

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="16f35-146">Umbenennen eines fremd Schlüssels, der nicht im Modell definiert ist</span><span class="sxs-lookup"><span data-stu-id="16f35-146">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="16f35-147">Wenn Sie keinen Fremdschlüssel für den CLR-Typ definieren, aber angeben möchten, welcher Name in der Datenbank enthalten sein soll, gehen Sie wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="16f35-147">If you choose not to define a foreign key on the CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a><span data-ttu-id="16f35-148">Konfigurieren eines Fremdschlüssel namens, der nicht der Code First Konvention folgt</span><span class="sxs-lookup"><span data-stu-id="16f35-148">Configuring a Foreign Key Name That Does Not Follow the Code First Convention</span></span>  

<span data-ttu-id="16f35-149">Wenn die Foreign Key-Eigenschaft in der Course-Klasse anstelle von DepartmentID "somedepartmentid" genannt wurde, müssen Sie Folgendes durchführen, um anzugeben, dass "somementid" der Fremdschlüssel sein soll:</span><span class="sxs-lookup"><span data-stu-id="16f35-149">If the foreign key property on the Course class was called SomeDepartmentID instead of DepartmentID, you would need to do the following to specify that you want SomeDepartmentID to be the foreign key:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a><span data-ttu-id="16f35-150">In Beispielen verwendetes Modell</span><span class="sxs-lookup"><span data-stu-id="16f35-150">Model Used in Samples</span></span>  

<span data-ttu-id="16f35-151">Das folgende Code First Modell wird für die Beispiele auf dieser Seite verwendet.</span><span class="sxs-lookup"><span data-stu-id="16f35-151">The following Code First model is used for the samples on this page.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.ModelConfiguration.Conventions;
// add a reference to System.ComponentModel.DataAnnotations DLL
using System.ComponentModel.DataAnnotations;
using System.Collections.Generic;
using System;

public class SchoolEntities : DbContext
{
    public DbSet<Course> Courses { get; set; }
    public DbSet<Department> Departments { get; set; }
    public DbSet<Instructor> Instructors { get; set; }
    public DbSet<OfficeAssignment> OfficeAssignments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        // Configure Code First to ignore PluralizingTableName convention
        // If you keep this convention then the generated tables will have pluralized names.
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}

public class Department
{
    public Department()
    {
        this.Courses = new HashSet<Course>();
    }
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }
    public decimal Budget { get; set; }
    public System.DateTime StartDate { get; set; }
    public int? Administrator { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; private set; }
}

public class Course
{
    public Course()
    {
        this.Instructors = new HashSet<Instructor>();
    }
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
    public virtual ICollection<Instructor> Instructors { get; private set; }
}

public partial class OnlineCourse : Course
{
    public string URL { get; set; }
}

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

public class Instructor
{
    public Instructor()
    {
        this.Courses = new List<Course>();
    }

    // Primary key
    public int InstructorID { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
    public System.DateTime HireDate { get; set; }

    // Navigation properties
    public virtual ICollection<Course> Courses { get; private set; }
}

public class OfficeAssignment
{
    // Specifying InstructorID as a primary
    [Key()]
    public Int32 InstructorID { get; set; }

    public string Location { get; set; }

    // When Entity Framework sees Timestamp attribute
    // it configures ConcurrencyCheck and DatabaseGeneratedPattern=Computed.
    [Timestamp]
    public Byte[] Timestamp { get; set; }

    // Navigation property
    public virtual Instructor Instructor { get; set; }
}
```  
