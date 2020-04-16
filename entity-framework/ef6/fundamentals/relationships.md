---
title: Beziehungen, Navigationseigenschaften und Fremdschlüssel - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: 76169f25d01bed81ad0748c9e85a67bd112a6e65
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434325"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a>Beziehungen, Navigationseigenschaften und Fremdschlüssel

Dieser Artikel gibt einen Überblick darüber, wie Entity Framework Beziehungen zwischen Entitäten verwaltet. Es gibt auch einige Anleitungen, wie Beziehungen zugeordnet und manipuliert werden können.

## <a name="relationships-in-ef"></a>Beziehungen in EF

In relationalen Datenbanken werden Beziehungen (auch Als Zuordnungen bezeichnet) zwischen Tabellen durch Fremdschlüssel definiert. Ein Fremdschlüssel (FS) ist eine Spalte oder eine Kombination von Spalten, mit deren Hilfe ein Link zwischen den Daten in zwei Tabellen eingerichtet und erzwungen wird. Es gibt in der Regel drei Arten von Beziehungen: Eins-zu-eins, Eins-zu-viele und viele.-zu-viele. In einer 1:n-Beziehung wird der Fremdschlüssel in der Tabelle definiert, der das viele Ende der Beziehung darstellt. Die n:n-Beziehung umfasst das Definieren einer dritten Tabelle (eine so genannte Verknüpfungs- oder Join-Tabelle), deren Primärschlüssel aus den Fremdschlüsseln aus beiden verknüpften Tabellen besteht. In einer 1:1-Beziehung fungiert der Primärschlüssel zusätzlich als Fremdschlüssel, und es gibt keine separate Fremdschlüsselspalte für eine der beiden Tabellen.

Die folgende Abbildung zeigt zwei Tabellen, die an einer 1:n-Beziehung teilnehmen. Die **Tabelle "Kurs"** ist die abhängige Tabelle, da sie die **Spalte DepartmentID** enthält, die sie mit der Tabelle **"Abteilung"** verknüpft.

![Abteilungs- und Kurstabellen](~/ef6/media/database2.png)

In Entity Framework kann eine Entität über eine Zuordnung oder Beziehung mit anderen Entitäten verknüpft werden. Jede Beziehung enthält zwei Enden, die den Entitätstyp und die Multiplizität des Typs (eins, null oder eins oder viele) für die beiden Entitäten in dieser Beziehung beschreiben. Die Beziehung wird möglicherweise von einer referenziellen Einschränkung geregelt, die beschreibt, welches Ende der Beziehung die Prinzipalrolle und welches die abhängige Rolle ist.

Navigationseigenschaften bieten eine Möglichkeit, eine Zuordnung zwischen zwei Entitätstypen zu navigieren. Jedes Objekt kann für jede Beziehung, an der es beteiligt ist, über eine Navigationseigenschaft verfügen. Navigationseigenschaften ermöglichen es Ihnen, Beziehungen in beide Richtungen zu navigieren und zu verwalten, indem Sie entweder ein Referenzobjekt (wenn die Multiplizität entweder eins oder null oder eins ist) oder eine Auflistung (wenn die Multiplizität viele ist) zurückgeben. Sie können auch eine einseitige Navigation wählen, in diesem Fall definieren Sie die Navigationseigenschaft nur für einen der Typen, die an der Beziehung beteiligt sind, und nicht für beide.

Es wird empfohlen, Eigenschaften in das Modell einzuschließen, die Fremdschlüsseln in der Datenbank zugeordnet werden. Wenn Fremdschlüsseleigenschaften einbezogen wurden, können Sie durch das Ändern des Fremdschlüsselwerts für ein abhängiges Objekt eine Beziehung erstellen oder ändern. Diese Art von Zuordnung wird Fremdschlüsselzuordnung genannt. Die Verwendung von Fremdschlüsseln ist bei der Arbeit mit getrennten Entitäten noch wichtiger. Beachten Sie, dass bei der Arbeit mit 1-zu-1 oder 1-zu-0.. 1 Beziehungen gibt es keine separate Fremdschlüsselspalte, die Primärschlüsseleigenschaft fungiert als Fremdschlüssel und ist immer im Modell enthalten.

Wenn Fremdschlüsselspalten nicht im Modell enthalten sind, werden die Zuordnungsinformationen als unabhängiges Objekt verwaltet. Beziehungen werden über Objektverweise anstelle von Fremdschlüsseleigenschaften nachverfolgt. Diese Art von Zuordnung wird als *unabhängige Zuordnung*bezeichnet. Die häufigste Möglichkeit zum Ändern einer *unabhängigen Zuordnung* besteht darin, die Navigationseigenschaften zu ändern, die für jede Entität generiert werden, die an der Zuordnung teilnimmt.

Sie können wahlweise einen oder beide Zuordnungstypen im Modell verwenden. Wenn Sie jedoch über eine reine N:n-Beziehung verfügen, die durch eine Join-Tabelle verbunden ist, die nur Fremdschlüssel enthält, verwendet der EF eine unabhängige Zuordnung, um eine solche n:n-Beziehung zu verwalten.   

Die folgende Abbildung zeigt ein konzeptionelles Modell, das mit dem Entity Framework Designer erstellt wurde. Das Modell enthält zwei Entitäten, die an einer 1:n-Beziehung beteiligt sind. Beide Entitäten verfügen über Navigationseigenschaften. **Kurs** ist die abhängige Entität und hat die **DepartmentID** Fremdschlüsseleigenschaft definiert.

![Abteilungs- und Kurstabellen mit Navigationseigenschaften](~/ef6/media/relationshipefdesigner.png)

Der folgende Codeausschnitt zeigt dasselbe Modell, das mit Code First erstellt wurde.

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

## <a name="configuring-or-mapping-relationships"></a>Konfigurieren oder Zuordnen von Beziehungen

Der Rest dieser Seite behandelt den Zugriff auf und die Bearbeitung von Daten mithilfe von Beziehungen. Informationen zum Einrichten von Beziehungen in Ihrem Modell finden Sie auf den folgenden Seiten.

-   Informationen zum Konfigurieren von Beziehungen in Code First finden Sie unter [Datenanmerkungen](~/ef6/modeling/code-first/data-annotations.md) und [Fluent API – Beziehungen](~/ef6/modeling/code-first/fluent/relationships.md).
-   Informationen zum Konfigurieren von Beziehungen mit dem Entity Framework Designer finden Sie unter [Beziehungen mit dem EF-Designer](~/ef6/modeling/designer/relationships.md).

## <a name="creating-and-modifying-relationships"></a>Erstellen und Ändern von Beziehungen

In einer *Fremdschlüsselzuordnung*ändert sich beim Ändern der Beziehung `EntityState.Unchanged` der Status `EntityState.Modified`eines abhängigen Objekts mit einem Zustand in . In einer unabhängigen Beziehung wird durch Ändern der Beziehung der Status des abhängigen Objekts nicht aktualisiert.

Die folgenden Beispiele zeigen, wie Sie die Fremdschlüsseleigenschaften und Navigationseigenschaften verwenden, um die zugehörigen Objekte zuzuordnen. Bei Fremdschlüsselzuordnungen können Sie entweder eine Methode zum Ändern, Erstellen oder Ändern von Beziehungen verwenden. Bei unabhängigen Zuordnungen können Sie die Fremdschlüsseleigenschaft nicht verwenden.

- Durch Zuweisen eines neuen Werts zu einer Fremdschlüsseleigenschaft, wie im folgenden Beispiel.  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- Der folgende Code entfernt eine Beziehung, indem der Fremdschlüssel auf **null**gesetzt wird. Beachten Sie, dass die Fremdschlüsseleigenschaft nullierbar sein muss.  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > Wenn sich der Verweis im hinzugefügten Zustand befindet (in diesem Beispiel das Kursobjekt), wird die Referenznavigationseigenschaft erst dann mit den Schlüsselwerten eines neuen Objekts synchronisiert, wenn SaveChanges aufgerufen wird. Es findet keine Synchronisierung statt, da der Objektkontext erst dann permanente Schlüssel für hinzugefügte Objekte enthält, wenn diese gespeichert werden. Wenn neue Objekte vollständig synchronisiert werden müssen, sobald Sie die Beziehung festlegen, verwenden Sie eine der folgenden Methoden.*

- Durch das Zuweisen einer Navigationseigenschaft zu einem neuen Objekt. Der folgende Code erstellt eine Beziehung `department`zwischen einem Kurs und einer . Wenn die Objekte dem Kontext `course` zugeordnet sind, `department.Courses` wird die auch der Auflistung `course` hinzugefügt, und die entsprechende Fremdschlüsseleigenschaft für das Objekt wird auf den Schlüsseleigenschaftswert der Abteilung festgelegt.  
  ``` csharp
  course.Department = department;
  ```

- Um die Beziehung zu löschen, `null`legen Sie die Navigationseigenschaft auf . Wenn Sie mit Entity Framework arbeiten, das auf .NET 4.0 basiert, muss das zugehörige Ende geladen werden, bevor Sie es auf null setzen. Beispiel:   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  Beginnend mit Entity Framework 5.0, das auf .NET 4.5 basiert, können Sie die Beziehung auf null festlegen, ohne das zugehörige Ende zu laden. Sie können den aktuellen Wert auch mit der folgenden Methode auf NULL festlegen.   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- Durch das Löschen eines Objekts aus einer Entitätsauflistung oder das Hinzufügen eines Objekts zu einer Entitätsauflistung. Sie können der `Course` `department.Courses` Auflistung z. B. ein Objekt vom Typ hinzufügen. Dieser Vorgang erstellt eine **course** Beziehung zwischen `department`einem bestimmten Kurs und einem bestimmten . Wenn die Objekte dem Kontext zugeordnet sind, werden der Abteilungsverweis und die `department`Fremdschlüsseleigenschaft im **Kursobjekt** auf das entsprechende festgelegt.  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- Mithilfe der `ChangeRelationshipState` Methode wird der Status der angegebenen Beziehung zwischen zwei Entitätsobjekten geändert. Diese Methode wird am häufigsten bei der Arbeit mit N-Tier-Anwendungen und einer *unabhängigen Zuordnung* verwendet (sie kann nicht mit einer Fremdschlüsselzuordnung verwendet werden). Um diese Methode verwenden zu können, müssen Sie auch auf `ObjectContext`, wie im folgenden Beispiel gezeigt, herunterdrop.  
Im folgenden Beispiel besteht eine n:n-Beziehung zwischen Kursleitern und Kursen. Wenn `ChangeRelationshipState` Sie die `EntityState.Added` Methode aufrufen `SchoolContext` und den Parameter übergeben, wird die seither darüber informiert, dass eine Beziehung zwischen den beiden Objekten hinzugefügt wurde:
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  Beachten Sie, dass Sie beim Aktualisieren (nicht nur Hinzufügen) einer Beziehung die alte Beziehung löschen müssen, nachdem Sie die neue Beziehung hinzugefügt haben:

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>Synchronisieren der Änderungen zwischen fremdschlüsseln und Navigationseigenschaften

Wenn Sie die Beziehung der dem Kontext zugeordneten Objekte mithilfe einer der oben beschriebenen Methoden ändern, muss Entity Framework Fremdschlüssel, Verweise und Auflistungen synchron halten. Entity Framework verwaltet diese Synchronisierung (auch als Beziehungsfixierung bezeichnet) für die POCO-Entitäten mit Proxys automatisch. Weitere Informationen finden Sie unter [Arbeiten mit Proxys](~/ef6/fundamentals/proxies.md).

Wenn Sie POCO-Entitäten ohne Proxys verwenden, müssen Sie sicherstellen, dass die **DetectChanges-Methode** aufgerufen wird, um die zugehörigen Objekte im Kontext zu synchronisieren. Beachten Sie, dass die folgenden APIs automatisch einen **DetectChanges-Aufruf** auslösen.

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
-   Ausführen einer LINQ-Abfrage für eine`DbSet`

## <a name="loading-related-objects"></a>Laden verwandter Objekte

In Entity Framework verwenden Sie häufig Navigationseigenschaften, um Entitäten zu laden, die mit der zurückgegebenen Entität durch die definierte Zuordnung verknüpft sind. Weitere Informationen finden Sie unter [Laden verwandter Objekte](~/ef6/querying/related-data.md).

> [!NOTE]
> Wenn Sie in einer Fremdschlüsselzuordnung ein verknüpftes Ende eines abhängigen Objekts laden, wird das verknüpfte Objekt auf der Grundlage des Fremdschlüsselwerts des abhängigen Elements geladen, der sich gerade im Arbeitsspeicher befindet:

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course2 = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

In einer unabhängigen Zuordnung wird das verknüpfte Ende eines abhängigen Objekts auf der Grundlage des Fremdschlüsselwerts abgefragt, der sich gerade in der Datenbank befindet. Wenn die Beziehung jedoch geändert wurde und die Referenzeigenschaft für das abhängige Objekt auf ein anderes Prinzipalobjekt verweist, das im Objektkontext geladen wird, versucht Entity Framework, eine Beziehung zu erstellen, wie sie auf dem Client definiert ist.

## <a name="managing-concurrency"></a>Verwalten von Nebenläufigkeit

In Fremdschlüssel- und unabhängigen Zuordnungen basieren Parallelitätsprüfungen auf den Entitätsschlüsseln und anderen Entitätseigenschaften, die im Modell definiert sind. Wenn Sie den EF-Designer zum `ConcurrencyMode` Erstellen eines Modells verwenden, legen Sie das Attribut auf **"Fix"** fest, um anzugeben, dass die Eigenschaft auf Parallelität überprüft werden soll. Wenn Sie Code First zum Definieren `ConcurrencyCheck` eines Modells verwenden, verwenden Sie die Anmerkung für Eigenschaften, die auf Parallelität überprüft werden sollen. Wenn Sie mit Code First `TimeStamp` arbeiten, können Sie auch die Anmerkung verwenden, um anzugeben, dass die Eigenschaft auf Parallelität überprüft werden soll. Sie können nur eine Timestamp-Eigenschaft in einer bestimmten Klasse haben. Code First ordnet diese Eigenschaft einem nicht NULL-fähigen Feld in der Datenbank zu.

Es wird empfohlen, die Fremdschlüsselzuordnung immer zu verwenden, wenn Sie mit Entitäten arbeiten, die an der Parallelitätsprüfung und -abwicklung beteiligt sind.

Weitere Informationen finden Sie [unter Behandeln von Parallelitätskonflikten](~/ef6/saving/concurrency.md).

## <a name="working-with-overlapping-keys"></a>Arbeiten mit überlappenden Schlüsseln

Überlappende Schlüssel sind zusammengesetzte Schlüssel, wobei einige Eigenschaften eines Schlüssels auch Teil eines anderen Schlüssels der Entität sind. Unabhängige Zuordnungen können keine überlappenden Schlüssel enthalten. Um eine Fremdschlüsselzuordnung zu ändern, die überlappende Schlüssel enthält, empfiehlt es sich, die Fremdschlüsselwerte zu ändern, statt die Objektverweise zu verwenden.
