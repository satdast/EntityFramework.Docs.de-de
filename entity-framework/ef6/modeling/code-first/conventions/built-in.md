---
title: Code First Konventionen-EF6
description: Code First Konventionen in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/built-in
ms.openlocfilehash: d5b7419258a9e45af5437fae5084a78ebc05f115
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066446"
---
# <a name="code-first-conventions"></a>Code First Konventionen
Code First ermöglicht es Ihnen, ein Modell mithilfe von c#-oder Visual Basic .NET-Klassen zu beschreiben. Die grundlegende Form des Modells wird mithilfe von Konventionen erkannt. Konventionen sind Sätze von Regeln, die verwendet werden, um ein konzeptionelles Modell, das auf Klassendefinitionen basiert, bei der Arbeit mit Code First automatisch zu konfigurieren. Die Konventionen werden im Namespace System. Data. Entity. modelconfiguration. Conventions definiert.  

Sie können Ihr Modell mithilfe von Daten Anmerkungen oder der fließenden API weiter konfigurieren. Die-Konfiguration wird durch die fließende API, gefolgt von Daten Anmerkungen und then-Konventionen, Vorrang eingeräumt. Weitere Informationen finden Sie unter [Daten Anmerkungen](xref:ef6/modeling/code-first/data-annotations), [fließende API-Beziehungen](xref:ef6/modeling/code-first/fluent/relationships), [fließende API-Typen & Eigenschaften](xref:ef6/modeling/code-first/fluent/types-and-properties) und eine [fließende API mit VB.net](xref:ef6/modeling/code-first/fluent/vb).  

Eine ausführliche Liste der Code First Konventionen finden Sie in der [API-Dokumentation](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx). Dieses Thema enthält eine Übersicht über die Konventionen, die von Code First verwendet werden.  

## <a name="type-discovery"></a>Typanmittlung  

Wenn Sie Code First Entwicklung verwenden, schreiben Sie in der Regel .NET Framework Klassen, die ihr konzeptionelles (Domänen-) Modell definieren. Zusätzlich zum Definieren der Klassen müssen Sie **dbcontext** auch darüber informieren, welche Typen Sie in das Modell einschließen möchten. Zu diesem Zweck definieren Sie eine Kontext Klasse, die von **dbcontext** abgeleitet ist und für die Typen, die Sie als Teil des Modells verwenden möchten, **dbset** -Eigenschaften verfügbar macht. Code First diese Typen einschließen und auch alle referenzierten Typen abrufen, auch wenn die Typen, auf die verwiesen wird, in einer anderen Assembly definiert sind.  

Wenn Ihre Typen an einer Vererbungs Hierarchie teilnehmen, genügt es, eine **dbset** -Eigenschaft für die Basisklasse zu definieren, und die abgeleiteten Typen werden automatisch eingeschlossen, wenn Sie sich in derselben Assembly wie die Basisklasse befinden.  

Im folgenden Beispiel wird nur eine **dbset** -Eigenschaft für die **SchoolEntities** -Klasse (**Abteilungen**) definiert. Code First verwendet diese Eigenschaft zum Ermitteln und Abrufen aller referenzierten Typen.  

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

Wenn Sie einen Typ aus dem Modell ausschließen möchten, verwenden Sie das Attribut " **notmapping** " oder die " **dbmodelbuilder. Ignore** "-API.  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a>Primärschlüssel Konvention  

Code First ab, dass eine Eigenschaft ein Primärschlüssel ist, wenn eine Eigenschaft in einer Klasse mit dem Namen "ID" (ohne Beachtung der Groß-/Kleinschreibung) oder dem Klassennamen gefolgt von "ID" benannt wird. Wenn der Typ der Primärschlüssel Eigenschaft numerisch oder GUID ist, wird er als Identitäts Spalte konfiguriert.  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a>Beziehungs Konvention  

Im Entity Framework bieten Navigationseigenschaften eine Möglichkeit, in einer Beziehung zwischen zwei Entitätstypen zu navigieren. Jedes Objekt kann für jede Beziehung, an der es beteiligt ist, über eine Navigationseigenschaft verfügen. Mithilfe von Navigations Eigenschaften können Sie Beziehungen in beiden Richtungen navigieren und verwalten, wobei entweder ein Verweis Objekt (wenn die Multiplizität entweder 1 oder 0 ist) oder eine Auflistung (wenn die Multiplizität viele ist) zurückgegeben werden. Code First leitet Beziehungen auf der Grundlage der Navigations Eigenschaften ab, die für Ihre Typen definiert sind.  

Zusätzlich zu den Navigations Eigenschaften empfiehlt es sich, Fremdschlüssel Eigenschaften für die Typen einzuschließen, die abhängige Objekte darstellen. Jede Eigenschaft mit demselben Datentyp wie die Prinzipal-Primärschlüssel Eigenschaft und mit einem Namen, der einem der folgenden Formate folgt, stellt einen Fremdschlüssel für die Beziehung dar: " \<navigation property name\> \<principal primary key property name\> ", " \<principal class name\> \<primary key property name\> " oder " \<principal primary key property name\> ". Wenn mehrere Übereinstimmungen gefunden werden, wird in der oben aufgeführten Reihenfolge Vorrang eingeräumt. Bei der Fremdschlüssel Erkennung wird die Groß-/Kleinschreibung Wenn eine Fremdschlüssel Eigenschaft erkannt wird, leitet Code First die Multiplizität der Beziehung basierend auf der NULL-Zulässigkeit des fremd Schlüssels ab. Wenn die Eigenschaft NULL-Werte zulässt, wird die Beziehung als optional registriert. Andernfalls wird die Beziehung nach Bedarf registriert.  

Wenn ein Fremdschlüssel für die abhängige Entität keine NULL-Werte zulässt, legt Code First den Löschvorgang für die Beziehung fest. Wenn ein Fremdschlüssel für die abhängige Entität NULL-Werte zulässt, legt Code First für die Beziehung keine Lösch Weitergabe fest, und wenn der Prinzipal gelöscht wird, wird der Fremdschlüssel auf NULL festgelegt. Das von der Konvention erkannte Multiplizität und Lösch Verhalten können überschrieben werden, indem die fließende API verwendet wird.  

Im folgenden Beispiel werden die Navigations Eigenschaften und ein Fremdschlüssel verwendet, um die Beziehung zwischen den Klassen "Department" und "Course" zu definieren.  

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
> Wenn Sie über mehrere Beziehungen zwischen denselben Typen verfügen (z. b. angenommen, Sie definieren die **Personen** -und **Buch** Klassen, in denen die **Person** -Klasse die Navigations Eigenschaften **reviewedbooks** und **authoredbooks** enthält und die **Book** -Klasse die Navigations Eigenschaften **Author** und **Reviewer** enthält), müssen Sie die Beziehungen mithilfe von Daten Anmerkungen oder der fließenden API manuell konfigurieren. Weitere Informationen finden Sie unter [Daten Anmerkungen-Beziehungen](xref:ef6/modeling/code-first/data-annotations) und [fließende API-Beziehungen](xref:ef6/modeling/code-first/fluent/relationships).  

## <a name="complex-types-convention"></a>Komplexe Typen Konvention  

Wenn Code First eine Klassendefinition ermittelt, in der ein Primärschlüssel nicht abgeleitet werden kann, und kein Primärschlüssel durch Daten Anmerkungen oder die fließende API registriert ist, wird der Typ automatisch als komplexer Typ registriert. Die Erkennung komplexer Typen erfordert auch, dass der Typ keine Eigenschaften hat, die auf Entitäts Typen verweisen, und auf die von einer Auflistungs Eigenschaft eines anderen Typs nicht verwiesen wird. Wenn die folgenden Klassendefinitionen angegeben werden, wird Code First abgeleitet, dass die **Details** ein komplexer Typ ist, da er keinen Primärschlüssel aufweist.  

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

## <a name="connection-string-convention"></a>Verbindungs Zeichen folgen Konvention  

Informationen zu den Konventionen, die dbcontext zum Ermitteln der zu verwendenden Verbindung verwendet, finden Sie unter [Verbindungen und Modelle](xref:ef6/fundamentals/configuring/connection-strings).  

## <a name="removing-conventions"></a>Entfernen von Konventionen  

Sie können alle Konventionen entfernen, die im System. Data. Entity. modelconfiguration. Conventions-Namespace definiert sind. Im folgenden Beispiel wird **pluralizingtablenameconvention**entfernt.  

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

## <a name="custom-conventions"></a>Benutzerdefinierte Konventionen  

Benutzerdefinierte Konventionen werden ab EF6 unterstützt. Weitere Informationen finden Sie unter [benutzerdefinierte Code First Konventionen](xref:ef6/modeling/code-first/conventions/custom).
