---
title: Beziehungen, Navigations Eigenschaften und Fremdschlüssel-EF6
description: Beziehungen, Navigations Eigenschaften und Fremdschlüssel in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/relationships
ms.openlocfilehash: 65caacfbd860b5b896695c208b55b9c66d11255d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072251"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a>Beziehungen, Navigations Eigenschaften und Fremdschlüssel

Dieser Artikel bietet einen Überblick darüber, wie Entity Framework Beziehungen zwischen Entitäten verwaltet. Außerdem erhalten Sie eine Anleitung zum Zuordnen und Bearbeiten von Beziehungen.

## <a name="relationships-in-ef"></a>Beziehungen in EF

In relationalen Datenbanken werden Beziehungen (auch als Zuordnungen bezeichnet) zwischen Tabellen durch Fremdschlüssel definiert. Ein Fremdschlüssel (FS) ist eine Spalte oder eine Kombination von Spalten, mit deren Hilfe ein Link zwischen den Daten in zwei Tabellen eingerichtet und erzwungen wird. Im Allgemeinen gibt es drei Typen von Beziehungen: eins-zu-eins, 1: n und m:n-. In einer 1: n-Beziehung wird der Fremdschlüssel für die Tabelle definiert, die das viele Ende der Beziehung darstellt. Die m:n-Beziehung umfasst das Definieren einer dritten Tabelle (als Verknüpfung oder jointabelle bezeichnet), deren Primärschlüssel aus den Fremdschlüsseln aus beiden verknüpften Tabellen besteht. In einer eins-zu-eins-Beziehung agiert der Primärschlüssel zusätzlich als Fremdschlüssel, und für beide Tabellen gibt es keine separate Fremdschlüssel Spalte.

Die folgende Abbildung zeigt zwei Tabellen, die an einer 1: n-Beziehung beteiligt sind. Die **Kurs** Tabelle ist die abhängige Tabelle, da Sie die **DepartmentID** -Spalte enthält, die Sie mit der **Abteilungs** Tabelle verknüpft.

![Abteilungs-und Kurs Tabellen](~/ef6/media/database2.png)

In Entity Framework kann eine Entität über eine Zuordnung oder Beziehung mit anderen Entitäten verknüpft werden. Jede Beziehung enthält zwei Enden, die den Entitätstyp und die Multiplizität des Typs (1, 0 oder 1 oder viele) für die beiden Entitäten in dieser Beziehung beschreiben. Die Beziehung wird möglicherweise von einer referenziellen Einschränkung geregelt, die beschreibt, welches Ende der Beziehung die Prinzipalrolle und welches die abhängige Rolle ist.

Navigations Eigenschaften bieten eine Möglichkeit, eine Zuordnung zwischen zwei Entitäts Typen zu navigieren. Jedes Objekt kann für jede Beziehung, an der es beteiligt ist, über eine Navigationseigenschaft verfügen. Mithilfe von Navigations Eigenschaften können Sie Beziehungen in beiden Richtungen navigieren und verwalten, wobei entweder ein Verweis Objekt (wenn die Multiplizität entweder 1 oder 0 ist) oder eine Auflistung (wenn die Multiplizität viele ist) zurückgegeben werden. Sie haben auch die Möglichkeit, eine unidirektionale Navigation zu wählen. in diesem Fall definieren Sie die Navigations Eigenschaft nur für einen Typ, der an der Beziehung teilnimmt, nicht für beides.

Es wird empfohlen, Eigenschaften in das Modell einzuschließen, die Fremdschlüsseln in der Datenbank zugeordnet werden. Wenn Fremdschlüsseleigenschaften einbezogen wurden, können Sie durch das Ändern des Fremdschlüsselwerts für ein abhängiges Objekt eine Beziehung erstellen oder ändern. Diese Art von Zuordnung wird Fremdschlüsselzuordnung genannt. Die Verwendung von Fremdschlüsseln ist noch wichtiger, wenn Sie mit getrennten Entitäten arbeiten. Beachten Sie, dass bei der Arbeit mit 1-zu-1 oder 1-zu-0. 1 Beziehungen, es gibt keine separate Fremdschlüssel Spalte, die Primärschlüssel Eigenschaft fungiert als Fremdschlüssel und ist immer im Modell enthalten.

Wenn Fremdschlüssel Spalten nicht im Modell enthalten sind, werden die Zuordnungs Informationen als unabhängiges Objekt verwaltet. Beziehungen werden über Objekt Verweise anstelle von Fremdschlüssel Eigenschaften nachverfolgt. Diese Art von Zuordnung wird als *unabhängige*Zuordnung bezeichnet. Die gängigste Methode zum Ändern einer *unabhängigen* Zuordnung besteht darin, die Navigations Eigenschaften zu ändern, die für jede Entität generiert werden, die an der Zuordnung teilnimmt.

Sie können wahlweise einen oder beide Zuordnungstypen im Modell verwenden. Wenn Sie jedoch über eine reine m:n-Beziehung verfügen, die durch eine Verknüpfungs Tabelle verbunden ist, die nur Fremdschlüssel enthält, verwendet das EF eine unabhängige Zuordnung, um eine m:n-Beziehung zu verwalten.   

Die folgende Abbildung zeigt ein konzeptionelles Modell, das mit dem Entity Framework Designer erstellt wurde. Das Modell enthält zwei Entitäten, die an einer 1: n-Beziehung beteiligt sind. Beide Entitäten verfügen über Navigations Eigenschaften. **Course** ist die abhängige Entität, und die Eigenschaft " **DepartmentID** Foreign Key" ist definiert.

![Abteilungs-und Kurs Tabellen mit Navigations Eigenschaften](~/ef6/media/relationshipefdesigner.png)

Der folgende Code Ausschnitt zeigt das gleiche Modell, das mit Code First erstellt wurde.

``` csharp
public class Course
{
  public int CourseID { get; set; }
  public string Title { get; set; }
  public int Credits { get; set; }
  public int DepartmentID { get; set; }
  public virtual Department Department { get; set; }
}

public class Department
{
   public Department()
   {
     this.Courses = new HashSet<Course>();
   }  
   public int DepartmentID { get; set; }
   public string Name { get; set; }
   public decimal Budget { get; set; }
   public DateTime StartDate { get; set; }
   public int? Administrator {get ; set; }
   public virtual ICollection<Course> Courses { get; set; }
}
```

## <a name="configuring-or-mapping-relationships"></a>Konfigurieren oder Mapping von Beziehungen

Im restlichen Abschnitt wird erläutert, wie Sie mithilfe von Beziehungen auf Daten zugreifen und diese bearbeiten. Informationen zum Einrichten von Beziehungen in Ihrem Modell finden Sie auf den folgenden Seiten.

-   Informationen zum Konfigurieren von Beziehungen in Code First finden Sie unter [Daten Anmerkungen](xref:ef6/modeling/code-first/data-annotations) und [fließende API-–-Beziehungen](xref:ef6/modeling/code-first/fluent/relationships).
-   Informationen zum Konfigurieren von Beziehungen mit dem Entity Framework Designer finden Sie unter [Beziehungen mit dem EF-Designer](xref:ef6/modeling/designer/relationships).

## <a name="creating-and-modifying-relationships"></a>Erstellen und Ändern von Beziehungen

Wenn Sie in einer *Fremdschlüssel*Zuordnung die Beziehung ändern, wird der Status eines abhängigen Objekts mit einem- `EntityState.Unchanged` Zustand in geändert `EntityState.Modified` . In einer unabhängigen Beziehung wird durch das Ändern der Beziehung der Zustand des abhängigen Objekts nicht aktualisiert.

In den folgenden Beispielen wird gezeigt, wie die Fremdschlüssel Eigenschaften und Navigations Eigenschaften verwendet werden, um die zugehörigen Objekte zuzuordnen. Mit Fremdschlüssel Zuordnungen können Sie eine der beiden Methoden verwenden, um Beziehungen zu ändern, zu erstellen oder zu ändern. Bei unabhängigen Zuordnungen können Sie die Fremdschlüsseleigenschaft nicht verwenden.

- Durch Zuweisen eines neuen Werts zu einer Fremdschlüssel Eigenschaft, wie im folgenden Beispiel gezeigt.  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- Mit dem folgenden Code wird eine Beziehung entfernt, indem der Fremdschlüssel auf **null**festgelegt wird. Beachten Sie, dass die Fremdschlüssel Eigenschaft NULL-Werte zulassen muss.  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > Wenn sich der Verweis im hinzugefügten Zustand befindet (in diesem Beispiel das Kurs Objekt), wird die Verweis Navigations Eigenschaft nicht mit den Schlüsselwerten eines neuen Objekts synchronisiert, bis SaveChanges aufgerufen wird. Es findet keine Synchronisierung statt, da der Objektkontext erst dann permanente Schlüssel für hinzugefügte Objekte enthält, wenn diese gespeichert werden. Wenn neue Objekte vollständig synchronisiert werden müssen, sobald Sie die Beziehung festgelegt haben, verwenden Sie eine der folgenden Methoden. *

- Durch das Zuweisen einer Navigationseigenschaft zu einem neuen Objekt. Mit dem folgenden Code wird eine Beziehung zwischen einem Kurs und einem erstellt `department` . Wenn die Objekte an den Kontext angefügt werden, `course` wird auch der-Auflistung hinzugefügt `department.Courses` , und die entsprechende Fremdschlüssel Eigenschaft für das- `course` Objekt wird auf den Schlüssel Eigenschafts Wert der Abteilung festgelegt.  
  ``` csharp
  course.Department = department;
  ```

- Legen Sie die-Navigations Eigenschaft auf fest, um die Beziehung zu löschen `null` . Wenn Sie mit Entity Framework arbeiten, die auf .NET 4,0 basiert, muss das verknüpfte Ende geladen werden, bevor Sie es auf NULL festlegen. Beispiel:   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  Beginnend mit Entity Framework 5,0, das auf .NET 4,5 basiert, können Sie die Beziehung auf NULL festlegen, ohne das verknüpfte Ende zu laden. Sie können den aktuellen Wert auch mit der folgenden Methode auf NULL festlegen.   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- Durch das Löschen eines Objekts aus einer Entitätsauflistung oder das Hinzufügen eines Objekts zu einer Entitätsauflistung. Beispielsweise können Sie der Auflistung ein Objekt vom Typ hinzufügen `Course` `department.Courses` . Mit diesem Vorgang wird eine Beziehung zwischen einem bestimmten **Kurs** und einem bestimmten erstellt `department` . Wenn die Objekte an den Kontext angefügt werden, werden der Abteilungs Verweis und die Fremdschlüssel Eigenschaft des **Kurs** Objekts auf den entsprechenden festgelegt `department` .  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- Mithilfe der- `ChangeRelationshipState` Methode können Sie den Zustand der angegebenen Beziehung zwischen zwei Entitäts Objekten ändern. Diese Methode wird am häufigsten bei der Arbeit mit N-Tier-Anwendungen und einer *unabhängigen* Zuordnung verwendet (Sie kann nicht mit einer Fremdschlüssel Zuordnung verwendet werden). Um diese Methode verwenden zu können, müssen Sie auch auf festlegen `ObjectContext` , wie im folgenden Beispiel gezeigt.  
Im folgenden Beispiel gibt es eine m:n-Beziehung zwischen Dozenten und Kursen. Durch Aufrufen der `ChangeRelationshipState` -Methode und übergeben des- `EntityState.Added` Parameters weiß das, `SchoolContext` dass eine Beziehung zwischen den beiden-Objekten hinzugefügt wurde:
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  Beachten Sie Folgendes: Wenn Sie eine Beziehung aktualisieren (nicht nur hinzufügen), müssen Sie die alte Beziehung löschen, nachdem Sie die neue Beziehung hinzugefügt haben:

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>Synchronisieren der Änderungen zwischen den Fremdschlüsseln und den Navigations Eigenschaften

Wenn Sie die Beziehung der Objekte ändern, die mit einer der oben beschriebenen Methoden an den Kontext angefügt sind, müssen Entity Framework Fremdschlüssel, Verweise und Auflistungen synchron halten. Entity Framework diese Synchronisierung (auch als Beziehungs Korrektur bezeichnet) für die poco-Entitäten mit Proxys automatisch verwaltet. Weitere Informationen finden Sie unter [Arbeiten mit](xref:ef6/fundamentals/proxies)Proxys.

Wenn Sie poco-Entitäten ohne Proxys verwenden, müssen Sie sicherstellen, dass die **DetectChanges** -Methode aufgerufen wird, um die zugehörigen Objekte im Kontext zu synchronisieren. Beachten Sie, dass die folgenden APIs automatisch einen **DetectChanges** -Befehl auslöst.

-   `DbSet.Add`
-   `DbSet.AddRange`
-   `DbSet.Remove`
-   `DbSet.RemoveRange`
-   `DbSet.Find`
-   `DbSet.Local`
-   `DbContext.SaveChanges`
-   `DbSet.Attach`
-   `DbContext.GetValidationErrors`
-   `DbContext.Entry`
-   `DbChangeTracker.Entries`
-   Ausführen einer LINQ-Abfrage für eine `DbSet`

## <a name="loading-related-objects"></a>Zugehörige Objekte werden geladen

In Entity Framework Sie häufig Navigations Eigenschaften verwenden, um Entitäten zu laden, die mit der zurückgegebenen Entität von der definierten Zuordnung verknüpft sind. Weitere Informationen finden Sie unter [Laden verwandter Objekte](xref:ef6/querying/related-data).

> [!NOTE]
> Wenn Sie in einer Fremdschlüsselzuordnung ein verknüpftes Ende eines abhängigen Objekts laden, wird das verknüpfte Objekt auf der Grundlage des Fremdschlüsselwerts des abhängigen Elements geladen, der sich gerade im Arbeitsspeicher befindet:

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

In einer unabhängigen Zuordnung wird das verknüpfte Ende eines abhängigen Objekts auf der Grundlage des Fremdschlüsselwerts abgefragt, der sich gerade in der Datenbank befindet. Wenn jedoch die Beziehung geändert wurde und die Verweis Eigenschaft des abhängigen Objekts auf ein anderes Prinzipal Objekt verweist, das in den Objekt Kontext geladen wird, wird Entity Framework versucht, eine Beziehung zu erstellen, die auf dem Client definiert ist.

## <a name="managing-concurrency"></a>Verwalten von Nebenläufigkeit

Sowohl in Fremdschlüssel-als auch in unabhängigen Zuordnungen basieren Parallelitäts Überprüfungen auf den Entitäts Schlüsseln und anderen Entitäts Eigenschaften, die im Modell definiert sind. Wenn Sie den EF-Designer verwenden, um ein Modell zu erstellen, legen Sie das- `ConcurrencyMode` Attribut auf **fest** , um anzugeben, dass die-Eigenschaft auf Parallelität geprüft werden soll. Wenn Sie Code First verwenden, um ein Modell zu definieren, verwenden Sie die-Anmerkung für Eigenschaften, die auf Parallelität `ConcurrencyCheck` geprüft werden sollen. Wenn Sie mit Code First arbeiten, können Sie auch die-Anmerkung verwenden `TimeStamp` , um anzugeben, dass die-Eigenschaft auf Parallelität geprüft werden soll. In einer bestimmten Klasse kann nur eine Zeitstempel-Eigenschaft vorhanden sein. Code First ordnet diese Eigenschaft einem Feld, das keine NULL-Werte zulässt, in der Datenbank zu.

Es wird empfohlen, bei der Arbeit mit Entitäten, die an der Parallelitäts Überprüfung und-Auflösung beteiligt sind, stets die Fremdschlüssel Zuordnung zu verwenden.

Weitere Informationen finden Sie unter [behandeln](xref:ef6/saving/concurrency)von Parallelitäts Konflikten.

## <a name="working-with-overlapping-keys"></a>Arbeiten mit überlappenden Schlüsseln

Überlappende Schlüssel sind zusammengesetzte Schlüssel, wobei einige Eigenschaften eines Schlüssels auch Teil eines anderen Schlüssels der Entität sind. Unabhängige Zuordnungen können keine überlappenden Schlüssel enthalten. Um eine Fremdschlüsselzuordnung zu ändern, die überlappende Schlüssel enthält, empfiehlt es sich, die Fremdschlüsselwerte zu ändern, statt die Objektverweise zu verwenden.
