---
title: Fließende Eigenschaften und Typen für die API-Konfiguration und-Zuordnung EF6
description: Fließende Eigenschaften und Typen der API-Konfiguration und-Zuordnung in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/types-and-properties
ms.openlocfilehash: ddd56ff280b72adbfd2247a4f1a85e37bf2f4879
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072589"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a>Fließende Eigenschaften und Typen für die API-Konfiguration und-Zuordnung
Beim Arbeiten mit Entity Framework Code First das Standardverhalten das Zuordnen Ihrer poco-Klassen zu Tabellen mithilfe eines Satzes von Konventionen, die in EF gebacken werden. Manchmal ist es jedoch nicht möglich, diese Konventionen einzuhalten, und die Entitäten müssen einem anderen als dem von den Konventionen vorgegeben werden.  

Es gibt zwei Hauptmethoden zum Konfigurieren von EF, um etwas anderes als Konventionen zu verwenden, nämlich [Anmerkungen](xref:ef6/modeling/code-first/data-annotations) oder eine EFS-fließende API. Die Anmerkungen decken nur eine Teilmenge der Funktionen der fließenden API ab, daher gibt es zuordnungsszenarien, die mithilfe von Anmerkungen nicht erreicht werden können. In diesem Artikel wird veranschaulicht, wie Sie die fließende API zum Konfigurieren von Eigenschaften verwenden.  

Der Zugriff auf die Code First-fließend-API wird am häufigsten durch Überschreiben der [onmodelcreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) -Methode für den abgeleiteten [dbcontext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)erreicht. Die folgenden Beispiele veranschaulichen, wie verschiedene Aufgaben mit der fließend-API ausgeführt werden können und wie Sie den Code kopieren und an Ihr Modell anpassen können. Wenn Sie sehen möchten, wie das Modell, mit dem Sie arbeiten können, unverändert verwendet werden kann, wird es am Ende dieses Artikels bereitgestellt.  

## <a name="model-wide-settings"></a>Modell weite Einstellungen  

### <a name="default-schema-ef6-onwards"></a>Standard Schema (EF6 oder höher)  

Beginnend mit EF6 können Sie mit der hasdefaultschema-Methode von dbmodelbuilder das Datenbankschema angeben, das für alle Tabellen, gespeicherten Prozeduren usw. verwendet werden soll. Diese Standardeinstellung wird für alle Objekte, für die Sie explizit ein anderes Schema konfigurieren, überschrieben.  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a>Benutzerdefinierte Konventionen (EF6 oder höher)  

Beginnend mit EF6 können Sie eigene Konventionen erstellen, um die in Code First enthaltenen Konventionen zu ergänzen. Weitere Informationen finden Sie unter [benutzerdefinierte Code First Konventionen](xref:ef6/modeling/code-first/conventions/custom).  

## <a name="property-mapping"></a>Eigenschaftszuordnung  

Die [Property](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) -Methode wird verwendet, um Attribute für jede Eigenschaft zu konfigurieren, die zu einer Entität oder einem komplexen Typ gehört. Die-Eigenschaften Methode wird verwendet, um ein Konfigurationsobjekt für eine bestimmte Eigenschaft abzurufen. Die Optionen für das Konfigurationsobjekt sind spezifisch für den konfigurierten Typ. Isunicode ist beispielsweise nur für Zeichen folgen Eigenschaften verfügbar.  

### <a name="configuring-a-primary-key"></a>Konfigurieren eines Primärschlüssels  

Die Entity Framework Konvention für Primärschlüssel lautet wie folgt:  

1. Ihre Klasse definiert eine Eigenschaft mit dem Namen "ID" oder "ID".  
2. oder einen Klassennamen, gefolgt von "ID" oder "ID".  

Um explizit eine Eigenschaft als Primärschlüssel festzulegen, können Sie die Haskey-Methode verwenden. Im folgenden Beispiel wird die Haskey-Methode verwendet, um den "InstructorID"-Primärschlüssel für den officezuweisungstyp zu konfigurieren.  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a>Konfigurieren eines zusammengesetzten Primärschlüssels  

Im folgenden Beispiel werden die Eigenschaften "DepartmentID" und "Name" als zusammengesetzter Primärschlüssel des Abteilungs Typs konfiguriert.  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a>Ausschalten der Identität für numerische Primärschlüssel  

Im folgenden Beispiel wird die DepartmentID-Eigenschaft auf System. ComponentModel. DataAnnotations. databasegeneratedoption. None festgelegt, um anzugeben, dass der Wert nicht von der Datenbank generiert wird.  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a>Angeben der maximalen Länge für eine Eigenschaft  

Im folgenden Beispiel sollte die Name-Eigenschaft nicht länger als 50 Zeichen sein. Wenn Sie den Wert länger als 50 Zeichen machen, wird eine [dbentityvalidationexception](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) -Ausnahme ausgelöst. Wenn Code First eine Datenbank aus diesem Modell erstellt, wird auch die maximale Länge der Spalte Name auf 50 Zeichen festgelegt.  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a>Konfigurieren der Eigenschaft als erforderlich  

Im folgenden Beispiel ist die Name-Eigenschaft erforderlich. Wenn Sie den Namen nicht angeben, wird eine dbentityvalidationexception-Ausnahme ausgelöst. Wenn Code First eine Datenbank aus diesem Modell erstellt, ist die Spalte, die zum Speichern dieser Eigenschaft verwendet wird, in der Regel keine NULL-Werte zulässig.  

> [!NOTE]
> In einigen Fällen ist es möglicherweise nicht möglich, dass die Spalte in der Datenbank keine NULL-Werte zulässt, auch wenn die-Eigenschaft erforderlich ist. Wenn z. b. eine TPH-Vererbungs Strategie verwendet wird, werden Daten für mehrere Typen in einer einzelnen Tabelle gespeichert. Wenn ein abgeleiteter Typ eine erforderliche Eigenschaft enthält, kann die Spalte nicht auf NULL festleg Bare Werte festgelegt werden, da nicht alle Typen in der Hierarchie über diese Eigenschaft verfügen.  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a>Konfigurieren eines Indexes für eine oder mehrere Eigenschaften  

> [!NOTE]
> **Nur ab EF 6.1** : das Index Attribut wurde in Entity Framework 6,1 eingeführt. Wenn Sie eine frühere Version verwenden, gelten die Informationen in diesem Abschnitt nicht.  

Das Erstellen von Indizes wird von der fließenden API nicht nativ unterstützt, aber Sie können die Unterstützung von **Indexattribute** über die fließende API nutzen. Index Attribute werden verarbeitet, indem eine Modell Anmerkung in das Modell eingeschlossen wird, die dann später in der Pipeline in einen Index in der Datenbank umgewandelt wird. Sie können dieselben Anmerkungen mithilfe der fließend-API manuell hinzufügen.  

Die einfachste Möglichkeit hierfür ist das Erstellen einer Instanz von **Indexattribute** , die alle Einstellungen für den neuen Index enthält. Anschließend können Sie eine Instanz der **Indexnotation** erstellen, bei der es sich um einen EF-spezifischen Typ handelt, der die **Indexattribute** -Einstellungen in eine Modell Anmerkung konvertiert, die im EF-Modell gespeichert werden kann. Diese können dann an die **hascolumnannotation** -Methode für die fließende API weitergegeben werden, wobei der namens **Index** für die Anmerkung angegeben wird.  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

Eine umfassende Liste der in " **Indexattribute**" verfügbaren Einstellungen finden Sie im Abschnitt " *Index* " der [Code First Daten Anmerkungen](xref:ef6/modeling/code-first/data-annotations). Dies umfasst das Anpassen des Index namens, das Erstellen eindeutiger Indizes und das Erstellen von mehrspaltigen Indizes.  

Sie können mehrere Index Anmerkungen für eine einzelne Eigenschaft angeben, indem Sie ein Array von " **Indexattribute** " an den Konstruktor von " **indexannotation**" übergeben.  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation(
        "Index",  
        new IndexAnnotation(new[]
            {
                new IndexAttribute("Index1"),
                new IndexAttribute("Index2") { IsUnique = true }
            })));
```  

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a>Angeben, dass eine CLR-Eigenschaft einer Spalte in der Datenbank nicht zugeordnet werden soll  

Im folgenden Beispiel wird gezeigt, wie angegeben wird, dass eine Eigenschaft eines CLR-Typs keiner Spalte in der Datenbank zugeordnet ist.  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a>Mapping einer CLR-Eigenschaft zu einer bestimmten Spalte in der Datenbank  

Im folgenden Beispiel wird die Name CLR-Eigenschaft der DepartmentName-Daten Bank Spalte zugeordnet.  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>Umbenennen eines fremd Schlüssels, der nicht im Modell definiert ist  

Wenn Sie keinen Fremdschlüssel für einen CLR-Typ definieren, aber angeben möchten, welcher Name in der Datenbank enthalten sein soll, gehen Sie wie folgt vor:  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a>Konfigurieren, ob eine Zeichen folgen Eigenschaft Unicode-Inhalt unterstützt  

Standardmäßig sind Zeichen folgen Unicode (nvarchar in SQL Server). Sie können die isunicode-Methode verwenden, um anzugeben, dass eine Zeichenfolge vom Typ varchar sein soll.  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a>Konfigurieren des Datentyps einer Daten Bank Spalte  

Die [hascolumntype](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) -Methode ermöglicht die Zuordnung zu verschiedenen Darstellungen desselben Basistyp. Wenn Sie diese Methode verwenden, können Sie die Daten nicht zur Laufzeit konvertieren. Beachten Sie, dass isunicode die bevorzugte Methode zum Festlegen von Spalten auf varchar ist, da dies Daten Bank agnostisch ist.  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a>Konfigurieren von Eigenschaften für einen komplexen Typ  

Es gibt zwei Möglichkeiten, skalare Eigenschaften für einen komplexen Typ zu konfigurieren.  

Sie können die-Eigenschaft für complextypeconfiguration aufzurufen.  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

Sie können auch die Punkt Notation verwenden, um auf eine Eigenschaft eines komplexen Typs zuzugreifen.  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a>Konfigurieren einer Eigenschaft, die als Token für die vollständige Parallelität verwendet werden soll  

Um anzugeben, dass eine Eigenschaft in einer Entität ein Parallelitäts Token darstellt, können Sie entweder das Attribut "-Attribut" oder die iskonaccesscytoken-Methode verwenden.  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

Sie können auch die IsRowVersion-Methode verwenden, um die Eigenschaft als Zeilen Version in der Datenbank zu konfigurieren. Wenn die Eigenschaft auf eine Zeilen Version festgelegt wird, wird Sie automatisch als Token für die vollständige Parallelität konfiguriert.  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a>Typzuordnung  

### <a name="specifying-that-a-class-is-a-complex-type"></a>Angeben, dass eine Klasse ein komplexer Typ ist  

Gemäß der Konvention wird ein Typ, der keinen Primärschlüssel enthält, als komplexer Typ behandelt. Es gibt einige Szenarien, in denen Code First einen komplexen Typ nicht erkennt (wenn Sie z. b. über eine Eigenschaft mit dem Namen "ID" verfügen, aber nicht bedeuten, dass es sich um einen Primärschlüssel handelt). In solchen Fällen verwenden Sie die fließende API, um explizit anzugeben, dass ein Typ ein komplexer Typ ist.  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a>Angeben, dass kein CLR-Entitätstyp einer Tabelle in der Datenbank zugeordnet werden soll  

Im folgenden Beispiel wird gezeigt, wie ein CLR-Typ von der Zuordnung zu einer Tabelle in der-Datenbank ausgeschlossen wird.  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a>Zuordnung eines Entitäts Typs zu einer bestimmten Tabelle in der Datenbank  

Alle Eigenschaften der Abteilung werden Spalten in einer Tabelle mit dem Namen T_ Abteilung zugeordnet.  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

Sie können auch den Schema Namen wie folgt angeben:  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a>Zuordnung der "Tabelle pro Hierarchie"-Vererbung (TPH)  

Im TPH-Zuordnungsszenario werden alle Typen in einer Vererbungs Hierarchie einer einzelnen Tabelle zugeordnet. Eine diskriminatorspalte wird verwendet, um den Typ der einzelnen Zeilen zu identifizieren. Wenn Sie Ihr Modell mit Code First erstellen, ist TPH die Standardstrategie für die Typen, die an der Vererbungs Hierarchie teilnehmen. Standardmäßig wird die diskriminatorspalte der Tabelle mit dem Namen "Diskriminator" hinzugefügt, und der CLR-Typname jedes Typs in der Hierarchie wird für die diskriminatorwerte verwendet. Sie können das Standardverhalten mithilfe der flüssigen API ändern.  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a>Zuordnung der "Tabelle pro Typ"-Vererbung (TPT)  

Im TPT-Zuordnungsszenario werden alle Typen einzelnen Tabellen zugeordnet. Eigenschaften, die nur zu einem Basistyp oder einem abgeleiteten Typ gehören, werden in einer Tabelle gespeichert, die diesem Typ zugeordnet ist. Tabellen, die abgeleiteten Typen zugeordnet sind, speichern auch einen Fremdschlüssel, der die abgeleitete Tabelle mit der Basistabelle verbindet.  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a>Zuordnung der TPC-Vererbung (Tabelle pro konkrete Klasse)  

Im TPC-Zuordnungsszenario werden alle nicht abstrakten Typen in der Hierarchie einzelnen Tabellen zugeordnet. Die Tabellen, die den abgeleiteten Klassen zugeordnet sind, haben keine Beziehung zu der Tabelle, die der Basisklasse in der Datenbank zugeordnet ist. Alle Eigenschaften einer Klasse, einschließlich der geerbten Eigenschaften, werden den Spalten der entsprechenden Tabelle zugeordnet.  

Ruft die mapinheritedproperties-Methode auf, um jeden abgeleiteten Typ zu konfigurieren. Mapinheritedproperties ordnet alle Eigenschaften, die von der Basisklasse geerbt wurden, neuen Spalten in der Tabelle für die abgeleitete Klasse zu.  

> [!NOTE]
> Beachten Sie Folgendes: da die an der TPC-Vererbungs Hierarchie beteiligten Tabellen keinen Primärschlüssel gemeinsam verwenden, gibt es beim Einfügen in Tabellen, die Unterklassen zugeordnet sind, doppelte Entitäts Schlüssel, wenn von der Datenbank generierte Werte mit dem gleichen identitätsseed vorhanden sind. Um dieses Problem zu beheben, können Sie entweder für jede Tabelle einen anderen ursprünglichen Startwert angeben oder die Identität der Primärschlüssel Eigenschaft ausschalten. Identity ist der Standardwert für ganzzahlige Schlüsseleigenschaften beim Arbeiten mit Code First.  

``` csharp
modelBuilder.Entity<Course>()
    .Property(c => c.CourseID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);

modelBuilder.Entity<OnsiteCourse>().Map(m =>
{
    m.MapInheritedProperties();
    m.ToTable("OnsiteCourse");
});

modelBuilder.Entity<OnlineCourse>().Map(m =>
{
    m.MapInheritedProperties();
    m.ToTable("OnlineCourse");
});
```  

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a>Zuordnung von Eigenschaften eines Entitäts Typs zu mehreren Tabellen in der Datenbank (Entitäts Aufteilung)  

Durch die Entitäts Aufteilung können die Eigenschaften eines Entitäts Typs auf mehrere Tabellen verteilt werden. Im folgenden Beispiel ist die Abteilungs Entität in zwei Tabellen aufgeteilt: Department und departmentdetails. Bei der Entitäts Aufteilung werden mehrere Aufrufe der Map-Methode verwendet, um einer bestimmten Tabelle eine Teilmenge von Eigenschaften zuzuordnen.  

``` csharp
modelBuilder.Entity<Department>()
    .Map(m =>
    {
        m.Properties(t => new { t.DepartmentID, t.Name });
        m.ToTable("Department");
    })
    .Map(m =>
    {
        m.Properties(t => new { t.DepartmentID, t.Administrator, t.StartDate, t.Budget });
        m.ToTable("DepartmentDetails");
    });
```  

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a>Zuordnung mehrerer Entitäts Typen zu einer Tabelle in der Datenbank (Tabellen Aufteilung)  

Im folgenden Beispiel werden zwei Entitäts Typen, die einen Primärschlüssel gemeinsam verwenden, einer Tabelle zugeordnet.  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a>Zuordnung eines Entitäts Typs zu gespeicherten Prozeduren zum Einfügen/Aktualisieren/Löschen (EF6 oder höher)  

Beginnend mit EF6 können Sie eine Entität zuordnen, um gespeicherte Prozeduren für Insert Update und DELETE zu verwenden. Weitere Informationen finden Sie unter [Code First gespeicherten Prozeduren INSERT/UPDATE/DELETE](xref:ef6/modeling/code-first/fluent/cud-stored-procedures).  

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
