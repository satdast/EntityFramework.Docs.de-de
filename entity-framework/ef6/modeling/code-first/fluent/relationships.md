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
# <a name="fluent-api---relationships"></a>Fließende API-Beziehungen
> [!NOTE]
> Diese Seite enthält Informationen zum Einrichten von Beziehungen in Ihrem Code First-Modell mithilfe der fließend-API. Allgemeine Informationen zu Beziehungen in EF und zum Zugreifen auf und Bearbeiten von Daten mithilfe von Beziehungen finden Sie unter [Beziehungen & Navigations Eigenschaften](xref:ef6/fundamentals/relationships).  

Beim Arbeiten mit Code First definieren Sie das Modell, indem Sie Ihre Domänen-CLR-Klassen definieren. Standardmäßig verwendet Entity Framework die Code First Konventionen, um die Klassen dem Datenbankschema zuzuordnen. Wenn Sie die Benennungs Konventionen für Code First verwenden, können Sie sich in den meisten Fällen auf Code First verlassen, um Beziehungen zwischen den Tabellen basierend auf den Fremdschlüsseln und Navigations Eigenschaften einzurichten, die Sie für die Klassen definieren. Wenn Sie den Konventionen beim Definieren der Klassen nicht folgen, oder wenn Sie die Funktionsweise der Konventionen ändern möchten, können Sie die-Klasse mit der flüssigen API oder Daten Anmerkungen so konfigurieren, dass Code First die Beziehungen zwischen den Tabellen zuordnen kann.  

## <a name="introduction"></a>Einführung  

Beim Konfigurieren einer Beziehung mit der flüssigen API beginnen Sie mit der entitytypeconfiguration-Instanz und verwenden dann die hasrequired-, hasoptional-oder hasMany-Methode, um den Beziehungstyp anzugeben, an dem diese Entität beteiligt ist. Die hasrequired-Methode und die hasoptional-Methode akzeptieren einen Lambda-Ausdruck, der eine Verweis Navigations Eigenschaft darstellt. Die hasMany-Methode nimmt einen Lambda-Ausdruck an, der eine Auflistungs Navigations Eigenschaft darstellt. Anschließend können Sie mithilfe der Methoden withrequired, withoptional und withmany eine umgekehrte Navigations Eigenschaft konfigurieren. Diese Methoden verfügen über über Ladungen, die keine Argumente annehmen und zum Angeben der Kardinalität mit unidirektionaler Navigation verwendet werden können.  

Anschließend können Sie die Fremdschlüssel Eigenschaften mithilfe der hasforeign nkey-Methode konfigurieren. Diese Methode verwendet einen Lambda-Ausdruck, der die Eigenschaft darstellt, die als Fremdschlüssel verwendet werden soll.  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a>Konfigurieren einer erforderlichen und optionalen Beziehung (One-to – Zero-or-one)  

Im folgenden Beispiel wird eine 1:0-oder 1-Beziehung konfiguriert. Officezuweisung verfügt über die InstructorID-Eigenschaft, bei der es sich um einen Primärschlüssel und einen Fremdschlüssel handelt, da der Name der Eigenschaft nicht der Konvention folgt, die die Haskey-Methode verwendet, um den Primärschlüssel zu konfigurieren.  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a>Konfigurieren einer Beziehung, bei der beide Enden (eins zu eins) erforderlich sind  

In den meisten Fällen können Entity Framework ableiten, welcher Typ der abhängige Typ ist und welcher der Prinzipal in einer Beziehung ist. Wenn jedoch beide Enden der Beziehung erforderlich sind oder beide Seiten optional sind Entity Framework den abhängigen und den Prinzipal nicht identifizieren können. Wenn beide Enden der Beziehung erforderlich sind, verwenden Sie withrequirements dprincipal oder withrequirements ddependent nach der hasrequired-Methode. Wenn beide Enden der Beziehung optional sind, verwenden Sie withoptionalprincipal oder withoptionaldependent nach der hasoptionalen-Methode.  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a>Konfigurieren einer m:n-Beziehung  

Der folgende Code konfiguriert eine m:n-Beziehung zwischen den Kurs-und Dozenten Typen. Im folgenden Beispiel werden die standardmäßigen Code First Konventionen verwendet, um eine jointabelle zu erstellen. Daher wird die Tabelle CourseInstructor mit Course_CourseID-und Instructor_InstructorID-Spalten erstellt.  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

Wenn Sie den Namen der jointabelle und die Namen der Spalten in der Tabelle angeben möchten, müssen Sie mit der Map-Methode eine zusätzliche Konfiguration durchführen. Der folgende Code generiert die CourseInstructor-Tabelle mit den Spalten CourseID und InstructorID.  

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

## <a name="configuring-a-relationship-with-one-navigation-property"></a>Konfigurieren einer Beziehung mit einer Navigations Eigenschaft  

Eine eindirektionale (auch als unidirektional bezeichnete Beziehung bezeichnet) ist, wenn eine Navigations Eigenschaft nur für eine der Enden der Beziehung definiert wird, nicht für beides. Gemäß der Konvention interpretiert Code First immer eine unidirektionale Beziehung als 1: n-Beziehung. Wenn Sie z. b. eine eins-zu-eins-Beziehung zwischen "Instructor" und "officezuweisung" wünschen, bei der nur eine Navigations Eigenschaft für den Typ "Instructor" vorhanden ist, müssen Sie die fließende API verwenden, um diese Beziehung zu konfigurieren.  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a>Aktivieren von Cascade Delete  

Mithilfe der Methode "willcascadeondelete" können Sie die Lösch Weitergabe für eine Beziehung konfigurieren. Wenn ein Fremdschlüssel für die abhängige Entität keine NULL-Werte zulässt, legt Code First den Löschvorgang für die Beziehung fest. Wenn ein Fremdschlüssel für die abhängige Entität NULL-Werte zulässt, legt Code First für die Beziehung keine Lösch Weitergabe fest, und wenn der Prinzipal gelöscht wird, wird der Fremdschlüssel auf NULL festgelegt.  

Sie können diese kaskadierenden Lösch Konventionen mithilfe von entfernen:  

Modelbuilder. Conventions. Remove \<OneToManyCascadeDeleteConvention\> ()  
Modelbuilder. Conventions. Remove \<ManyToManyCascadeDeleteConvention\> ()  

Der folgende Code konfiguriert die Beziehung als erforderlich und deaktiviert die Lösch Weitergabe.  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a>Konfigurieren eines zusammengesetzten fremd Schlüssels  

Wenn der Primärschlüssel des Abteilungs Typs aus "DepartmentID" und "Name Properties" besteht, würden Sie den Primärschlüssel für die Abteilung und den Fremdschlüssel für die Kurs Typen wie folgt konfigurieren:  

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

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>Umbenennen eines fremd Schlüssels, der nicht im Modell definiert ist  

Wenn Sie keinen Fremdschlüssel für den CLR-Typ definieren, aber angeben möchten, welcher Name in der Datenbank enthalten sein soll, gehen Sie wie folgt vor:  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a>Konfigurieren eines Fremdschlüssel namens, der nicht der Code First Konvention folgt  

Wenn die Foreign Key-Eigenschaft in der Course-Klasse anstelle von DepartmentID "somedepartmentid" genannt wurde, müssen Sie Folgendes durchführen, um anzugeben, dass "somementid" der Fremdschlüssel sein soll:  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a>In Beispielen verwendetes Modell  

Das folgende Code First Modell wird für die Beispiele auf dieser Seite verwendet.  

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
