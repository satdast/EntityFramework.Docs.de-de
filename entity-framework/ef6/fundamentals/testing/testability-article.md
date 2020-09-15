---
title: Testability und Entity Framework 4,0-EF6
description: Testability und Entity Framework 4,0
author: divega
ms.date: 10/23/2016
ms.openlocfilehash: 439e2af6b0c851a2ab70d6d9ed1004deb83b1a0e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070509"
---
# <a name="testability-and-entity-framework-40"></a>Testability und Entity Framework 4,0
Scott allen

Veröffentlicht: Mai 2010

## <a name="introduction"></a>Einführung

In diesem Whitepaper wird beschrieben und veranschaulicht, wie Sie testbaren Code mit den ADO.NET-Entity Framework 4,0 und Visual Studio 2010 schreiben können. In diesem Whitepaper wird nicht versucht, sich auf eine bestimmte Testmethode zu konzentrieren, wie z. b. Test gesteuerte Entwürfe (Test-gesteuerte Design) oder BDD. Stattdessen konzentriert sich dieses Whitepaper darauf, wie Sie Code schreiben können, der den ADO.NET-Entity Framework verwendet, um die Isolierung und den Test auf automatisierte Weise zu testen. Wir betrachten allgemeine Entwurfsmuster, die das Testen in Datenzugriffs Szenarien vereinfachen und sehen, wie diese Muster bei der Verwendung des Frameworks angewendet werden. Wir betrachten auch bestimmte Features des Frameworks, um zu sehen, wie diese Features in testbarem Code funktionieren können.

## <a name="what-is-testable-code"></a>Was ist prüfbarer Code?

Die Möglichkeit, eine Software Software mithilfe automatisierter Komponententests zu überprüfen, bietet zahlreiche Vorteile. Jeder weiß, dass gute Tests die Anzahl der Software Mängel in einer Anwendung reduzieren und die Qualität der Anwendung erhöhen, aber das vorhanden sein von Komponententests geht weit über das Auffinden von Fehlern hinaus.

Eine gute Komponenten Test Suite ermöglicht einem Entwicklungsteam, Zeit zu sparen, und die Kontrolle über die von Ihnen erstellte Software zu behalten. Ein Team kann Änderungen an vorhandenem Code vornehmen, die Software umgestalten, umgestalten und umstrukturieren, um neue Anforderungen zu erfüllen, und neue Komponenten zu einer Anwendung hinzufügen, während Sie wissen, dass die Test Sammlung das Verhalten der Anwendung überprüfen kann. Komponententests sind Teil eines kurzen Feedbacks, um Änderungen zu vereinfachen und die Verwaltbarkeit von Software zu gewährleisten, wenn sich die Komplexität erhöht.

Komponententests verfügen jedoch über einen Preis. Ein Team muss die Zeit investieren, um Komponententests zu erstellen und zu verwalten. Der Aufwand, der zum Erstellen dieser Tests erforderlich ist, hängt direkt mit der **Prüfbarkeit** der zugrunde liegenden Software zusammen. Wie einfach ist die Software zu testen? Ein Team, das Software mit Prüfbarkeit im Hinterkopf entwirft, erstellt effektive Tests schneller als das Team, das mit der nicht Test fähigen Software arbeitet.

Microsoft hat den ADO.NET Entity Framework 4,0 (EF4) mit Prüfbarkeit entworfen. Dies bedeutet nicht, dass Entwickler Komponententests für den Frameworkcode selbst schreiben werden. Stattdessen erleichtern die Testability-Ziele für EF4 das Erstellen von prüfbarem Code, der auf dem Framework aufbaut. Bevor wir uns mit bestimmten Beispielen beschäftigen, ist es sinnvoll, die Qualität des testbaren Codes zu verstehen.

### <a name="the-qualities-of-testable-code"></a>Die Qualitäten von testablem Code

Code, der leicht zu testen ist, enthält immer mindestens zwei Merkmale. Zuerst ist testbarer Code leicht zu **beobachten**. Bei einem bestimmten Satz von Eingaben sollte die Ausgabe des Codes leicht zu beobachten sein. Beispielsweise ist das Testen der folgenden Methode einfach, da die-Methode das Ergebnis einer Berechnung direkt zurückgibt.

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

Das Testen einer Methode ist schwierig, wenn die-Methode den berechneten Wert in einen Netzwerk Socket, eine Datenbanktabelle oder eine Datei wie den folgenden Code schreibt. Der Test muss zusätzliche Arbeitsschritte ausführen, um den Wert abzurufen.

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

Zweitens lässt sich der prüfbare Code leicht **isolieren**. Wir verwenden den folgenden Pseudo Code als ungültiges Beispiel für testbaren Code.

``` csharp
    public int ComputePolicyValue(InsurancePolicy policy) {
        using (var connection = new SqlConnection("dbConnection"))
        using (var command = new SqlCommand(query, connection)) {

            // business calculations omitted ...               

            if (totalValue > notificationThreshold) {
                var message = new MailMessage();
                message.Subject = "Warning!";
                var client = new SmtpClient();
                client.Send(message);
            }
        }
        return totalValue;
    }
```

Die Methode ist leicht zu beobachten – wir können eine Versicherungsrichtlinie übergeben und überprüfen, ob der Rückgabewert mit einem erwarteten Ergebnis übereinstimmt. Zum Testen der Methode muss jedoch eine Datenbank mit dem richtigen Schema installiert werden, und der SMTP-Server muss konfiguriert werden, falls die Methode versucht, eine e-Mail zu senden.

Der Komponenten Test möchte nur die Berechnungs Logik innerhalb der Methode überprüfen, aber der Test schlägt möglicherweise fehl, weil der e-Mail-Server offline ist oder der Datenbankserver verschoben wurde. Beide Fehler stehen nicht im Zusammenhang mit dem Verhalten, das der Test überprüfen soll. Das Verhalten ist schwierig zu isolieren.

Software Entwickler, die sich bemühen, testbaren Code zu schreiben, werden häufig bestrebt sein, Probleme im Code, den Sie schreiben, zu trennen. Die obige Methode sollte sich auf die Geschäfts Berechnungen konzentrieren und die Datenbank-und e-Mail-Implementierungsdetails an andere Komponenten delegieren. Robert C. Martin nennt dies das Prinzip der einzigen Verantwortung. Ein Objekt muss eine einzelne, schmale Verantwortung Kapseln, wie z. b. die Berechnung des Werts einer Richtlinie. Alle anderen Datenbank-und Benachrichtigungs arbeiten sollten die Verantwortung für ein anderes Objekt haben. Auf diese Weise geschriebener Code ist leichter zu isolieren, da er sich auf eine einzelne Aufgabe konzentriert.

In .net gibt es die Abstraktionen, die wir benötigen, um das Prinzip der einzelnen Verantwortung einzuhalten und Isolation zu erzielen. Wir können Schnittstellendefinitionen verwenden und erzwingen, dass der Code die Schnittstellen Abstraktion anstelle eines konkreten Typs verwendet. Später in diesem Whitepaper wird erläutert, wie eine Methode wie das ungültige Beispiel mit Schnittstellen funktionieren kann, die so *aussehen* , als Sie mit der Datenbank kommunizieren. Zum Testzeitpunkt können wir jedoch eine dummyimplementierung ersetzen, die nicht mit der Datenbank kommuniziert, sondern stattdessen Daten im Arbeitsspeicher speichert. Diese Dummy-Implementierung isoliert den Code von nicht verknüpften Problemen im Datenzugriffs Code oder in der Daten Bank Konfiguration.

Es gibt weitere Vorteile bei der Isolierung. Die Ausführung der Geschäfts Berechnung in der letzten Methode sollte nur wenige Millisekunden dauern, aber der Test selbst kann mehrere Sekunden dauern, da der Code das Netzwerk überspringt und mit verschiedenen Servern kommuniziert. Komponententests sollten schnell ausgeführt werden, um kleine Änderungen zu vereinfachen. Komponententests sollten ebenfalls wiederholbar sein und nicht fehlschlagen, da eine Komponente, die nicht mit dem Test verknüpft ist, ein Problem aufweist. Das Schreiben von Code, der leicht zu beobachten und zu isolieren ist, bedeutet, dass Entwickler einen einfacheren Zeitaufwand für das Schreiben von Tests für den Code haben, weniger Zeit mit dem warten auf die Ausführung von Tests verbringen und noch wichtiger ist, dass weniger Zeit für das Nachverfolgen von Fehlern aufgewendet wird

Wir hoffen, dass Sie die Vorteile der Tests und der Qualität der von Test fähige Code ausgestellten Qualitäten schätzen können. Wir sind im Begriff, zu erfahren, wie Sie Code schreiben können, der mit EF4 verwendet wird, um Daten in einer Datenbank zu speichern, während Sie sichtbar und leicht zu isolieren ist, aber zuerst wird der Fokus auf die Erörterung testbarer Entwürfe für den Datenzugriff eingrenzen.

## <a name="design-patterns-for-data-persistence"></a>Entwurfsmuster für die Daten Persistenz

Beide ungültigen Beispiele waren zu viele Zuständigkeiten. Das erste ungültige Beispiel war das Ausführen einer Berechnung *und* das Schreiben in eine Datei. Das zweite ungültige Beispiel war das Lesen von Daten aus einer Datenbank *und* das Ausführen einer Geschäfts Berechnung *und* das Senden von e-Mails. Wenn Sie kleinere Methoden entwerfen, die die Belange voneinander trennen und die Verantwortung an andere Komponenten delegieren, machen Sie große Fortschritte beim Schreiben von testbarem Code. Das Ziel besteht darin, die Funktionalität zu erstellen, indem Aktionen aus kleinen und fokussierten Abstraktionen verfasst werden.

Wenn es um die Daten Persistenz geht, sind die kleinen und ausgerichteten Abstraktionen, nach denen wir suchen, so häufig, dass Sie als Entwurfsmuster dokumentiert wurden. Martin Fowler Book Patterns of Enterprise Application Architecture war die erste Aufgabe, diese Muster im Druck zu beschreiben. In den folgenden Abschnitten wird eine kurze Beschreibung dieser Muster bereitgestellt, bevor gezeigt wird, wie diese ADO.NET Entity Framework implementiert und mit diesen Mustern arbeitet.

### <a name="the-repository-pattern"></a>Repositorymuster

Fowler sagt, dass ein Repository zwischen den Domänen-und Daten Zustellungs Ebenen mithilfe einer Sammlungs ähnlichen Schnittstelle für den Zugriff auf Domänen Objekte mediiert. Das Ziel des Repository-Musters besteht darin, Code aus den Minutiae des Datenzugriffs zu isolieren. wie bereits erwähnt wurde, ist die Isolation ein erforderliches Merkmal für die Prüfbarkeit.

Der Schlüssel zur Isolation ist, wie das Repository Objekte mithilfe einer Auflistungs ähnlichen Schnittstelle verfügbar macht. Die Logik, die Sie schreiben, um das Repository zu verwenden, hat keine Idee, wie das Repository die von Ihnen angeforderten Objekte materialisieren soll. Das Repository kann mit einer Datenbank kommunizieren, oder es können nur Objekte aus einer Auflistung im Speicher zurückgegeben werden. Der gesamte Code muss wissen, dass das Repository die Auflistung beibehält, und Sie können Objekte aus der Auflistung abrufen, hinzufügen und löschen.

In vorhandenen .NET-Anwendungen erbt ein konkretes Repository häufig von einer generischen Schnittstelle wie der folgenden:

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

Wir nehmen einige Änderungen an der Schnittstellen Definition vor, wenn wir eine Implementierung für EF4 bereitstellen, das grundlegende Konzept bleibt jedoch unverändert. Code kann ein konkretes Repository verwenden, das diese Schnittstelle implementiert, um eine Entität nach ihrem Primärschlüssel Wert abzurufen, eine Auflistung von Entitäten basierend auf der Auswertung eines Prädikats abzurufen oder einfach alle verfügbaren Entitäten abzurufen. Der Code kann auch Entitäten über die Repository-Schnittstelle hinzufügen und entfernen.

Bei einem IRepository von Employee-Objekten kann Code die folgenden Vorgänge ausführen.

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

Da der Code eine Schnittstelle (IRepository of Employee) verwendet, können wir den Code mit verschiedenen Implementierungen der Schnittstelle bereitstellen. Eine Implementierung kann eine Implementierung sein, die von EF4 und das Beibehalten von Objekten in einer Microsoft SQL Server Datenbank unterstützt wird. Eine andere-Implementierung (die während des Tests verwendet wird) kann durch eine in-Memory-Liste von Employee-Objekten unterstützt werden. Die-Schnittstelle unterstützt Sie dabei, die Isolation im Code zu erreichen.

Beachten Sie, dass die IRepository &lt; T- &gt; Schnittstelle keinen Speichervorgang verfügbar macht. Wie werden vorhandene Objekte aktualisiert? Sie können über IRepository-Definitionen verfügen, die den Speichervorgang einschließen, und Implementierungen dieser Depots müssen ein Objekt direkt in der Datenbank speichern. In vielen Anwendungen möchten wir Objekte jedoch nicht einzeln beibehalten. Stattdessen möchten wir Objekte zum Leben bringen, vielleicht aus verschiedenen Depots, die Objekte im Rahmen einer Geschäftsaktivität ändern und dann alle Objekte im Rahmen eines einzelnen atomarischen Vorgangs beibehalten. Glücklicherweise gibt es ein Muster, das diese Art von Verhalten zulässt.

### <a name="the-unit-of-work-pattern"></a>Das Arbeits Einheits Muster

Fowler sagt, dass eine Arbeitseinheit eine Liste von Objekten verwaltet, die von einer Geschäftstransaktion betroffen sind, und koordiniert das Schreiben von Änderungen und die Auflösung von Parallelitäts Problemen. Es liegt in der Verantwortung der Arbeitseinheit, Änderungen an den Objekten zu verfolgen, die wir aus einem Repository in den Lebenszyklus bringen, und die Änderungen beizubehalten, die wir an den Objekten vorgenommen haben, wenn wir die Arbeitseinheit anweisen, die Änderungen zu übernehmen. Außerdem liegt es in der Verantwortung der Arbeitseinheit, die neuen Objekte zu übernehmen, die wir allen Depots hinzugefügt haben, und die Objekte in eine Datenbank einzufügen.

Wenn Sie schon einmal mit ADO.NET DataSets gearbeitet haben, sind Sie bereits mit dem Unit of Work-Muster vertraut. ADO.NET Datasets konnten unsere Updates, Löschungen und Einfügungen von DataRow-Objekten nachverfolgen und konnten (mit einem TableAdapter) alle Änderungen an einer Datenbank abgleichen. DataSet-Objekte modellieren jedoch eine getrennte Teilmenge der zugrunde liegenden Datenbank. Das Arbeitseinheits Muster weist das gleiche Verhalten auf, funktioniert jedoch mit Geschäftsobjekten und Domänen Objekten, die vom Datenzugriffs Code isoliert sind und nicht über die Datenbank verfügen.

Eine Abstraktion, um die Arbeitseinheit in .NET-Code zu modellieren, könnte wie folgt aussehen:

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

Durch das verfügbar machen von Repository-verweisen aus der Arbeitseinheit kann sichergestellt werden, dass ein einzelnes Arbeitseinheiten Objekt über die Möglichkeit verfügt, alle Entitäten zu verfolgen, die während einer Geschäftstransaktion materialisiert werden. Die Implementierung der Commit-Methode für eine echte Arbeitseinheit besteht darin, dass der gesamte Magic-Vorgang im Arbeitsspeicher mit der Datenbank abgeglichen wird. 

Bei einem iunitofwork-Verweis kann Codeänderungen an Geschäftsobjekten vornehmen, die aus einem oder mehreren Depots abgerufen wurden, und alle Änderungen mithilfe des atomarischen Commitvorgangs speichern.

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>Das Lazy Load-Muster

Fowler verwendet den Namen Lazy Load, um "ein Objekt zu beschreiben, das nicht alle benötigten Daten enthält, aber weiß, wie es zu erhalten ist". Transparente Lazy Loading ist ein wichtiges Feature, das beim Schreiben von testbarem Geschäfts Code und beim Arbeiten mit einer relationalen Datenbank erforderlich ist. Sehen Sie sich als Beispiel den folgenden Code an:

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

Wie wird die Sammlung "TIMECARDS" aufgefüllt? Es gibt zwei mögliche Antworten. Eine Antwort ist, dass das Employee-Repository bei der Aufforderung zum Abrufen eines Mitarbeiters eine Abfrage ausgibt, um den Mitarbeiter zusammen mit den zugehörigen Zeitkarten Informationen des Mitarbeiters abzurufen. In relationalen Datenbanken erfordert dies in der Regel eine Abfrage mit einer Join-Klausel und kann dazu führen, dass mehr Informationen als die Anwendungsanforderungen abgerufen werden. Was geschieht, wenn die Anwendung die TIMECARDS-Eigenschaft nie berühren muss?

Eine zweite Antwort besteht darin, die TIMECARDS-Eigenschaft "Bedarfs gesteuert" zu laden. Diese Lazy Loading ist implizit und transparent für die Geschäftslogik, da der Code keine speziellen APIs aufruft, um Zeitkarten Informationen abzurufen. Der Code setzt voraus, dass die Zeitkarten Informationen bei Bedarf vorhanden sind. Es gibt eine Reihe von Magic-Lazy Loading, die im Allgemeinen das Lauf Zeit Abfangen von Methoden aufrufen umfasst. Der abzurufende Code ist für die Kommunikation mit der Datenbank und das Abrufen von Zeitkarten Informationen verantwortlich, während die Geschäftslogik kostenlos als Geschäftslogik belassen wird. Diese Lazy Load-Magic ermöglicht es dem Geschäfts Code, sich selbst von Datenabruf Vorgängen zu isolieren, und führt zu einem besser prüfbaren Code.

Der Nachteil eines verzögerten Ladens besteht darin, dass der Code eine zusätzliche Abfrage ausführt, wenn eine Anwendung die Zeitkarten *Informationen benötigt.* Dies ist für viele Anwendungen nicht von Bedeutung, aber für Leistungs relevante Anwendungen oder Anwendungen, die eine Reihe von Mitarbeiter Objekten durchlaufen und eine Abfrage ausführen, um Zeitkarten während jeder Iterationen der Schleife abzurufen (ein Problem, das häufig als "N + 1 Query Problem" bezeichnet wird), Lazy Loading ein Drag-Vorgang ist. In diesen Szenarien kann es vorkommen, dass eine Anwendung Zeitkarten Informationen auf möglichst effiziente Weise lädt.

Glücklicherweise werden wir sehen, wie EF4 sowohl implizite Lazy Loads als auch effiziente, sorgfältige Auslastungen unterstützt, wenn wir mit dem nächsten Abschnitt fortfahren und diese Muster implementieren.

## <a name="implementing-patterns-with-the-entity-framework"></a>Implementieren von Mustern mit dem Entity Framework

Die gute Nachricht ist, dass alle Entwurfsmuster, die wir im letzten Abschnitt beschrieben haben, einfach mit EF4 implementiert werden können. Um zu veranschaulichen, dass wir eine einfache ASP.NET MVC-Anwendung verwenden, um Mitarbeiter und die dazugehörigen Zeitkarten Informationen zu bearbeiten und anzuzeigen. Wir beginnen mit der Verwendung der folgenden "Plain Old CLR Objects" (POCOS). 

``` csharp
    public class Employee {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime HireDate { get; set; }
        public ICollection<TimeCard> TimeCards { get; set; }
    }

    public class TimeCard {
        public int Id { get; set; }
        public int Hours { get; set; }
        public DateTime EffectiveDate { get; set; }
    }
```

Diese Klassendefinitionen ändern sich geringfügig, da wir unterschiedliche Ansätze und Features von EF4 untersuchen, aber die Absicht besteht darin, diese Klassen möglichst persistent zu halten. Ein PI-Objekt weiß nicht, *wie*oder auch *Wenn*der Zustand, den er enthält, innerhalb einer Datenbank ist. Pi und POCOS sind mit Test fähiger Software Hand. Objekte, die einen poco-Ansatz verwenden, sind weniger eingeschränkt, flexibler und leichter zu testen, da Sie ohne eine vorhandene Datenbank betrieben werden können.

Mit den POCOS können wir eine Entity Data Model (EDM) in Visual Studio erstellen (siehe Abbildung 1). Der EDM wird nicht zum Generieren von Code für unsere Entitäten verwendet. Stattdessen möchten wir die Entitäten verwenden, die wir in Hand haben. Wir verwenden das EDM nur zum Generieren des Datenbankschemas und zur Bereitstellung der Metadaten EF4 muss Objekte der Datenbank zuordnen.

![EF-test_01](~/ef6/media/eftest-01.jpg)

**Abbildung 1**

Hinweis: Wenn Sie das EDM-Modell zuerst entwickeln möchten, ist es möglich, sauberen, Poco-Code aus dem EDM zu generieren. Dies können Sie mit einer Erweiterung von Visual Studio 2010 durchführen, die vom datenprogrammierbarkeits-Team bereitgestellt wird. Um die Erweiterung herunterzuladen, starten Sie den Erweiterungs-Manager über das Menü Extras in Visual Studio, und Durchsuchen Sie den Onlinekatalog der Vorlagen nach "poco" (siehe Abbildung 2). Für EF sind mehrere poco-Vorlagen verfügbar. Weitere Informationen zur Verwendung der Vorlage finden Sie unter "Exemplarische Vorgehensweise [: POCO-Vorlage für die Entity Framework](/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)".

![EF-test_02](~/ef6/media/eftest-02.png)

**Abbildung 2**

Von diesem poco-Ausgangspunkt aus untersuchen wir zwei unterschiedliche Ansätze für Test fähigen Code. Der erste Ansatz ist der EF-Ansatz, da er Abstraktionen von der Entity Framework-API nutzt, um Arbeitseinheiten und Depots zu implementieren. In der zweiten Vorgehensweise werden wir unsere eigenen benutzerdefinierten Repository-Abstraktionen erstellen und dann die vor-und Nachteile der einzelnen Ansätze sehen. Wir beginnen mit dem EF-Ansatz.  

### <a name="an-ef-centric-implementation"></a>Eine EF-zentrierte Implementierung

Sehen Sie sich die folgende Controller Aktion aus einem ASP.NET-MVC-Projekt an. Mit der Aktion wird ein Employee-Objekt abgerufen, und es wird ein Ergebnis zurückgegeben, um eine detaillierte Ansicht des Mitarbeiters anzuzeigen.

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

Ist der Code testable? Es gibt mindestens zwei Tests, die wir benötigen, um das Verhalten der Aktion zu überprüfen. Zuerst möchten wir überprüfen, ob die Aktion die korrekte Ansicht zurückgibt – einen einfachen Test. Wir möchten auch einen Test schreiben, um zu überprüfen, ob die Aktion den richtigen Mitarbeiter abruft, und wir möchten dies tun, ohne Code zum Abfragen der Datenbank auszuführen. Denken Sie daran, dass der zu testende Code isoliert werden soll. Durch die Isolation wird sichergestellt, dass der Test aufgrund eines Fehlers im Datenzugriffs Code oder in der Daten Bank Konfiguration nicht fehlschlägt. Wenn der Test fehlschlägt, wissen wir, dass ein Fehler in der Controller Logik und nicht in einer anderen Systemkomponente vorhanden ist.

Um die Isolation zu erreichen, benötigen wir einige Abstraktionen, wie z. b. die Schnittstellen, die wir zuvor für die Depots und Arbeitseinheiten Denken Sie daran, dass das Repository-Muster für die Vermittlung zwischen Domänen Objekten und der Datenzuordnung konzipiert ist. In diesem Szenario *ist* EF4 die Daten Zuordnungsschicht und stellt bereits eine Repository-ähnliche Abstraktion namens IObjectSet &lt; T &gt; (aus dem Namespace System. Data. Objects) bereit. Die Schnittstellen Definition sieht wie folgt aus.

``` csharp
    public interface IObjectSet<TEntity> :
                     IQueryable<TEntity>,
                     IEnumerable<TEntity>,
                     IQueryable,
                     IEnumerable
                     where TEntity : class
    {
        void AddObject(TEntity entity);
        void Attach(TEntity entity);
        void DeleteObject(TEntity entity);
        void Detach(TEntity entity);
    }
```

IObjectSet &lt; T &gt; erfüllt die Anforderungen für ein Repository, da es einer Auflistung von Objekten (über IEnumerable &lt; T &gt; ) ähnelt und Methoden zum Hinzufügen und Entfernen von Objekten aus der simulierten Auflistung bereitstellt. Die Anfüge-und Trennmethoden machen zusätzliche Funktionen der EF4-API verfügbar. Um IObjectSet &lt; T &gt; als Schnittstelle für Repository zu verwenden, benötigen wir eine Arbeitseinheits-Abstraktion, um die Objekte gemeinsam zu binden.

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

Eine konkrete Implementierung dieser Schnittstelle wird mit SQL Server kommunizieren und ist einfach zu erstellen, indem die ObjectContext-Klasse aus EF4 verwendet wird. Die ObjectContext-Klasse ist die tatsächliche Arbeitseinheit in der EF4-API.

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;
            _context = new ObjectContext(connectionString);
        }

        public IObjectSet<Employee> Employees {
            get { return _context.CreateObjectSet<Employee>(); }
        }

        public IObjectSet<TimeCard> TimeCards {
            get { return _context.CreateObjectSet<TimeCard>(); }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

Ein IObjectSet &lt; T &gt; in den Lebenszyklus zu bringen, ist so einfach wie das Aufrufen der Methode "| ateobjectset" des Objekts "ObjectContext". Im Hintergrund verwendet das Framework die Metadaten, die wir im EDM bereitgestellt haben, um ein konkretes ObjectSet T zu liefern &lt; &gt; . Wir behalten uns die Rückgabe der IObjectSet &lt; T- &gt; Schnittstelle vor, da Sie dabei helfen soll, die Prüfbarkeit im Client Code beizubehalten.

Diese konkrete Implementierung ist in der Produktion nützlich, aber wir müssen uns darauf konzentrieren, wie wir unsere iuniyfwork-Abstraktion verwenden, um Tests zu vereinfachen.

### <a name="the-test-doubles"></a>Die Test Doubles

Um die Controller Aktion zu isolieren, benötigen wir die Möglichkeit, zwischen der realen Arbeitseinheit (gestützt durch einen ObjectContext) und einer Test Double-oder "Fake"-Arbeitseinheit zu wechseln (Durchführung von in-Memory-Vorgängen). Der gängige Ansatz zum Durchführen dieser Art von Umschaltung besteht darin, dass der MVC-Controller nicht eine Arbeitseinheit instanziiert, sondern stattdessen die Arbeitseinheit als Konstruktorparameter an den Controller übergibt.

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

Der obige Code ist ein Beispiel für eine Abhängigkeitsinjektion. Wir gestatten dem Controller nicht, seine Abhängigkeit (die Arbeitseinheit) zu erstellen, sondern die Abhängigkeit in den Controller einzufügen. In einem MVC-Projekt ist es üblich, eine benutzerdefinierte Controller-Factory in Kombination mit einem IOC-Container (Inversion of Control) zu verwenden, um die Abhängigkeitsinjektion zu automatisieren. Diese Themen gehen über den Rahmen dieses Artikels hinaus, aber Sie können weitere Informationen lesen, indem Sie die Verweise am Ende dieses Artikels befolgen.

Eine gefälschte Arbeitseinheit, die wir für Tests verwenden können, könnte wie folgt aussehen.

``` csharp
    public class InMemoryUnitOfWork : IUnitOfWork {
        public InMemoryUnitOfWork() {
            Committed = false;
        }
        public IObjectSet<Employee> Employees {
            get;
            set;
        }

        public IObjectSet<TimeCard> TimeCards {
            get;
            set;
        }

        public bool Committed { get; set; }
        public void Commit() {
            Committed = true;
        }
    }
```

Beachten Sie, dass die gefälschte Arbeitseinheit eine durch ein Commit getrennte Eigenschaft verfügbar macht. Es ist manchmal hilfreich, einer gefälschten Klasse Features hinzuzufügen, die das Testen vereinfachen. In diesem Fall ist es leicht zu beobachten, ob der Code einen Commit für eine Arbeitseinheit durchführt, indem er die Eigenschaft "komprimiert" überprüft.

Wir benötigen auch ein gefälschtes IObjectSet &lt; T &gt; , das Mitarbeiter-und Timecard-Objekte im Arbeitsspeicher hält. Wir können eine einzelne Implementierung mit Generika bereitstellen.

``` csharp
    public class InMemoryObjectSet<T> : IObjectSet<T> where T : class
        public InMemoryObjectSet()
            : this(Enumerable.Empty<T>()) {
        }
        public InMemoryObjectSet(IEnumerable<T> entities) {
            _set = new HashSet<T>();
            foreach (var entity in entities) {
                _set.Add(entity);
            }
            _queryableSet = _set.AsQueryable();
        }
        public void AddObject(T entity) {
            _set.Add(entity);
        }
        public void Attach(T entity) {
            _set.Add(entity);
        }
        public void DeleteObject(T entity) {
            _set.Remove(entity);
        }
        public void Detach(T entity) {
            _set.Remove(entity);
        }
        public Type ElementType {
            get { return _queryableSet.ElementType; }
        }
        public Expression Expression {
            get { return _queryableSet.Expression; }
        }
        public IQueryProvider Provider {
            get { return _queryableSet.Provider; }
        }
        public IEnumerator<T> GetEnumerator() {
            return _set.GetEnumerator();
        }
        IEnumerator IEnumerable.GetEnumerator() {
            return GetEnumerator();
        }

        readonly HashSet<T> _set;
        readonly IQueryable<T> _queryableSet;
    }
```

Bei diesem Test wird der größte Teil seiner Arbeit an ein zugrunde liegendes HashSet &lt; T- &gt; Objekt delegiert. Beachten Sie, dass IObjectSet &lt; t &gt; eine generische Einschränkung erfordert, die T als Klasse (einen Verweistyp) erzwingt, und erzwingt, dass "iquerable t" implementiert wird &lt; &gt; . Es ist einfach, eine Auflistung im Arbeitsspeicher als "iquerable T" &lt; zu &gt; verwenden, indem der standardmäßige LINQ-Operator "asquerable" verwendet wird.

### <a name="the-tests"></a>Die Tests

Bei herkömmlichen Unittests wird eine einzelne Testklasse verwendet, um alle Tests für alle Aktionen in einem einzelnen MVC-Controller aufzunehmen. Wir können diese Tests oder eine beliebige Art von Komponenten Test mithilfe der in Arbeitsspeicher-Fakes, die wir erstellt haben, schreiben. In diesem Artikel vermeiden wir jedoch die monolithische Test Klassen Herangehensweise und Gruppieren unsere Tests, um sich auf eine bestimmte Funktionalität zu konzentrieren.Beispielsweise könnte "Create New Employee" die Funktionalität sein, die wir testen möchten. Daher verwenden wir eine einzelne Testklasse, um die einzige Controller Aktion zu überprüfen, die für die Erstellung eines neuen Mitarbeiters zuständig ist.

Es gibt einige gängige Setup Codes, die wir für alle diese differenzierten Test Klassen benötigen. Beispielsweise müssen wir immer unsere in-Memory-Repository und gefälschte Arbeitseinheit erstellen. Außerdem benötigen wir eine Instanz des Employee-Controllers, bei der die gefälschte Arbeitseinheit eingefügt wurde. Wir geben diesen allgemeinen Setup Code über Test Klassen hinweg mithilfe einer Basisklasse frei.

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .ToList();
            _repository = new InMemoryObjectSet<Employee>(_employeeData);
            _unitOfWork = new InMemoryUnitOfWork();
            _unitOfWork.Employees = _repository;
            _controller = new EmployeeController(_unitOfWork);
        }

        protected IList<Employee> _employeeData;
        protected EmployeeController _controller;
        protected InMemoryObjectSet<Employee> _repository;
        protected InMemoryUnitOfWork _unitOfWork;
    }
```

Die "Objekt-Mutter", die in der Basisklasse verwendet wird, ist ein gängiges Muster zum Erstellen von Testdaten. Eine Objekt-Mutter enthält Factorymethoden zum Instanziieren von Test Entitäten für die Verwendung in mehreren Test Vorrichtungen.

``` csharp
    public static class EmployeeObjectMother {
        public static IEnumerable<Employee> CreateEmployees() {
            yield return new Employee() {
                Id = 1, Name = "Scott", HireDate=new DateTime(2002, 1, 1)
            };
            yield return new Employee() {
                Id = 2, Name = "Poonam", HireDate=new DateTime(2001, 1, 1)
            };
            yield return new Employee() {
                Id = 3, Name = "Simon", HireDate=new DateTime(2008, 1, 1)
            };
        }
        // ... more fake data for different scenarios
    }
```

Wir können "Mitarbeiter Controller TestBase" als Basisklasse für eine Reihe von Test Vorrichtungen verwenden (siehe Abbildung 3). Jede Test Fixierung testet eine bestimmte Controller Aktion. Beispielsweise konzentriert sich eine Test Fixierung auf das Testen der Erstellungs Aktion, die während einer HTTP GET-Anforderung verwendet wird (um die Ansicht zum Erstellen eines Mitarbeiters anzuzeigen), und eine andere Fixierung konzentriert sich auf die CREATE-Aktion, die in einer HTTP POST-Anforderung verwendet wird (um die vom Benutzer gesendeten Informationen zu erstellen, um einen Mitarbeiter zu erstellen). Jede abgeleitete Klasse ist nur für das Setup verantwortlich, das in Ihrem speziellen Kontext benötigt wird, und um die Assertionen bereitzustellen, die zum Überprüfen der Ergebnisse für den jeweiligen Test Kontext erforderlich sind.

![EF-test_03](~/ef6/media/eftest-03.png)

**Abbildung 3**

Die hier dargestellten Benennungs Konventionen und teststile sind für testbaren Code nicht erforderlich – es handelt sich nur um einen Ansatz. Abbildung 4 zeigt die Tests, die im Jet Brains reschärfere Test Runner-Plug-in für Visual Studio 2010 ausgeführt werden.

![EF-test_04](~/ef6/media/eftest-04.png)

**Abbildung 4**

Mit einer Basisklasse zur Handhabung des freigegebenen Setup Codes sind die Komponententests für die einzelnen Controller Aktionen klein und leicht zu schreiben. Die Tests werden schnell ausgeführt (da wir in-Memory-Vorgänge durchführen) und sollten aufgrund von nicht verknüpften Infrastrukturen oder umweltbezogenen Belangen fehlschlagen (da wir die zu testende Einheit isoliert haben).

``` csharp
    [TestClass]
    public class EmployeeControllerCreateActionPostTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldAddNewEmployeeToRepository() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_repository.Contains(_newEmployee));
        }
        [TestMethod]
        public void ShouldCommitUnitOfWork() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_unitOfWork.Committed);
        }
        // ... more tests

        Employee _newEmployee = new Employee() {
            Name = "NEW EMPLOYEE",
            HireDate = new System.DateTime(2010, 1, 1)
        };
    }
```

In diesen Tests führt die Basisklasse den größten Teil der Einrichtung aus. Beachten Sie, dass der Basisklassenkonstruktor das in-Memory-Repository, eine gefälschte Arbeitseinheit und eine Instanz der Klasse Mitarbeiter Controller erstellt. Die Testklasse wird von dieser Basisklasse abgeleitet und konzentriert sich auf die Besonderheiten beim Testen der Create-Methode. In diesem Fall werden die Besonderheiten in den Schritten "anordnen, Act und Assert" angezeigt, die Sie bei jedem Komponenten Testverfahren sehen:

-   Erstellen Sie ein netwemployee-Objekt, um eingehende Daten zu simulieren.
-   Rufen Sie die CREATE-Aktion von Mitarbeiter Controller auf, und übergeben Sie die Datei "netwemployee".
-   Überprüfen Sie, ob die CREATE-Aktion die erwarteten Ergebnisse erzeugt (der Mitarbeiter wird im Repository angezeigt).

Wir haben die Möglichkeit, alle Mitarbeiter zu testen, die wir erstellt haben. Wenn wir z. b. Tests für die Index Aktion des Employee-Controllers schreiben, können wir von der Test Basisklasse erben, um das gleiche Basis Setup für unsere Tests einzurichten. Erneut erstellt die Basisklasse das in-Memory-Repository, die gefälschte Arbeitseinheit und eine Instanz von Mitarbeiter Controller. Die Tests für die Index Aktion müssen sich nur auf das Aufrufen der Index Aktion und das Testen der Qualitäten des Modells konzentrieren, das von der Aktion zurückgegeben wird.

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count);
        }
        [TestMethod]
        public void ShouldOrderModelByHiredateAscending() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                         as IEnumerable<Employee>;
            Assert.IsTrue(model.SequenceEqual(
                           _employeeData.OrderBy(e => e.HireDate)));
        }
        // ...
    }
```

Die Tests, die wir mit in-Memory-Fakes erstellen, sind darauf ausgerichtet, den *Zustand* der Software zu testen. Wenn Sie z. b. die Erstellungs Aktion testen möchten, soll der Status des Repository nach der Ausführung der CREATE-Aktion überprüft werden – enthält das Repository den neuen Mitarbeiter?

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

Später befassen wir uns mit Interaktions basierten Tests. Durch Interaktions basierte Tests wird gefragt, ob der zu testende Code die richtigen Methoden für unsere Objekte aufgerufen hat und die richtigen Parameter übergebenen. Im folgenden wird ein weiteres Entwurfsmuster behandelt – das verzögerte Laden.

## <a name="eager-loading-and-lazy-loading"></a>Unverzügliches Laden und Lazy Load

An einem bestimmten Punkt in der ASP.NET MVC-Webanwendung möchten wir möglicherweise die Informationen eines Mitarbeiters anzeigen und die zugeordneten Zeitkarten des Mitarbeiters einschließen. Beispielsweise kann eine Zeitkarten-Übersichts Anzeige angezeigt werden, in der der Name des Mitarbeiters und die Gesamtzahl der Zeitkarten im System angezeigt werden. Es gibt mehrere Ansätze, die wir zur Implementierung dieser Funktion verwenden können.

### <a name="projection"></a>Projektion

Ein einfacher Ansatz zum Erstellen der Zusammenfassung besteht darin, ein Modell zu erstellen, das für die Informationen vorgesehen ist, die in der Ansicht angezeigt werden sollen. In diesem Szenario könnte das Modell wie folgt aussehen.

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

Beachten Sie, dass es sich bei "Mitarbeiter Name" nicht um eine Entität handelt – das heißt, dass es sich nicht um etwas handelt, das in der Datenbank persistent gespeichert werden soll. Diese Klasse wird nur verwendet, um Daten in einer stark typisierten Weise in die Ansicht zu mischen. Das Ansichts Modell ist wie ein Datenübertragungs Objekt (Data Transfer Object, dto), da es kein Verhalten (keine Methoden) – nur Eigenschaften enthält. Die Eigenschaften enthalten die Daten, die wir verschieben müssen. Es ist einfach, dieses Ansichts Modell mit dem Standard Projektions Operator von LINQ – dem Select-Operator zu instanziieren.

``` csharp
    public ViewResult Summary(int id) {
        var model = _unitOfWork.Employees
                               .Where(e => e.Id == id)
                               .Select(e => new EmployeeSummaryViewModel
                                  {
                                    Name = e.Name,
                                    TotalTimeCards = e.TimeCards.Count()
                                  })
                               .Single();
        return View(model);
    }
```

Der obige Code enthält zwei wichtige Features. Zuerst – der Code ist einfach zu testen, da er immer noch leicht zu beobachten und zu isolieren ist. Der Select-Operator funktioniert genauso gut wie bei den in-Memory-Fakes, wie es bei der realen Arbeitseinheit der Fall ist.

``` csharp
    [TestClass]
    public class EmployeeControllerSummaryActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithCorrectEmployeeSummary() {
            var id = 1;
            var result = _controller.Summary(id);
            var model = result.ViewData.Model as EmployeeSummaryViewModel;
            Assert.IsTrue(model.TotalTimeCards == 3);
        }
        // ...
    }
```

Das zweite wichtige Feature ist, wie der Code es EF4 ermöglicht, eine einzelne, effiziente Abfrage zu generieren, um Mitarbeiter-und Zeitkarten Informationen zusammenzustellen. Wir haben Mitarbeiter Informationen und Zeitkarten Informationen in dasselbe Objekt geladen, ohne dass spezielle APIs verwendet werden. Der Code hat lediglich die erforderlichen Informationen zum Verwenden von standardmäßigen LINQ-Operatoren ausgedrückt, die für in-Memory-Datenquellen und Remote Datenquellen geeignet sind. EF4 konnte die von der LINQ-Abfrage und dem C-Compiler generierten Ausdrucks Baumstrukturen \# in eine einzelne und effiziente T-SQL-Abfrage übersetzen.

``` SQL
    SELECT
    [Limit1].[Id] AS [Id],
    [Limit1].[Name] AS [Name],
    [Limit1].[C1] AS [C1]
    FROM (SELECT TOP (2)
      [Project1].[Id] AS [Id],
      [Project1].[Name] AS [Name],
      [Project1].[C1] AS [C1]
      FROM (SELECT
        [Extent1].[Id] AS [Id],
        [Extent1].[Name] AS [Name],
        (SELECT COUNT(1) AS [A1]
         FROM [dbo].[TimeCards] AS [Extent2]
         WHERE [Extent1].[Id] =
               [Extent2].[EmployeeTimeCard_TimeCard_Id]) AS [C1]
              FROM [dbo].[Employees] AS [Extent1]
               WHERE [Extent1].[Id] = @p__linq__0
         )  AS [Project1]
    )  AS [Limit1]
```

In anderen Zeiten möchten wir nicht mit einem Ansichts Modell oder DTO-Objekt arbeiten, sondern mit echten Entitäten. Wenn wir wissen, dass wir einen Mitarbeiter *und* die Zeitkarten des Mitarbeiters benötigen, können wir die verknüpften Daten auf unaufdringliche und effiziente Weise laden.

### <a name="explicit-eager-loading"></a>Explizites Laden

Wenn Sie zusammen gehörige Entitäts Informationen laden möchten, benötigen wir einige Mechanismen für die Geschäftslogik (oder in diesem Szenario, Controller Aktions Logik), um den Wunsch zum Repository auszudrücken. Die Klasse EF4 ObjectQuery &lt; T &gt; definiert eine include-Methode, um die verknüpften Objekte anzugeben, die während einer Abfrage abgerufen werden sollen. Beachten Sie, dass EF4 ObjectContext Entitäten über die konkrete ObjectSet t-Klasse verfügbar macht &lt; &gt; , die von ObjectQuery t erbt &lt; &gt; .Wenn wir ObjectSet T- &lt; &gt; Verweise in unserer Controller Aktion verwenden, könnten wir den folgenden Code schreiben, um die Zeitkarten Informationen für die einzelnen Mitarbeiter zu verwenden.

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

Da wir jedoch versuchen, den Code testfähig zu halten, machen wir ObjectSet T nicht &lt; &gt; von außerhalb der realen Arbeitseinheits Klasse verfügbar. Stattdessen wird die IObjectSet &lt; t- &gt; Schnittstelle verwendet, die einfacher zu fälschen ist, aber IObjectSet &lt; t &gt; definiert keine Include-Methode. Die Schönheit von LINQ besteht darin, dass wir unseren eigenen Include-Operator erstellen können.

``` csharp
    public static class QueryableExtensions {
        public static IQueryable<T> Include<T>
                (this IQueryable<T> sequence, string path) {
            var objectQuery = sequence as ObjectQuery<T>;
            if(objectQuery != null)
            {
                return objectQuery.Include(path);
            }
            return sequence;
        }
    }
```

Beachten Sie, dass dieser Include-Operator als Erweiterungsmethode für iquerable &lt; t &gt; anstelle von IObjectSet t definiert ist &lt; &gt; . Dies ermöglicht uns die Verwendung der-Methode mit einer breiteren Palette möglicher Typen, einschließlich iquervable &lt; t &gt; , IObjectSet &lt; t &gt; , ObjectQuery &lt; t &gt; und ObjectSet &lt; t &gt; . Wenn die zugrunde liegende Sequenz keine echte EF4 ObjectQuery &lt; T ist &gt; , wird keine Beschädigung durchgeführt, und der Include-Operator ist ein No-op. Wenn die zugrunde liegende *is* Sequenz ein ObjectQuery &lt; t &gt; (oder von ObjectQuery &lt; t abgeleitet &gt; ) ist, werden die Anforderungen für zusätzliche Daten von EF4 angezeigt, und die richtige SQL-Abfrage wird formuliert.

Mit diesem neuen Operator können wir explizit eine Zeitkarten Informationen aus dem Repository anfordern.

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Beim Ausführen für einen echten ObjectContext erzeugt der Code die folgende einzelne Abfrage. Die Abfrage sammelt in einer einzigen Fahrt genügend Informationen aus der Datenbank, um die Mitarbeiter Objekte zu materialisieren und ihre TIMECARDS-Eigenschaft vollständig aufzufüllen.

``` SQL
    SELECT
    [Project1].[Id] AS [Id],
    [Project1].[Name] AS [Name],
    [Project1].[HireDate] AS [HireDate],
    [Project1].[C1] AS [C1],
    [Project1].[Id1] AS [Id1],
    [Project1].[Hours] AS [Hours],
    [Project1].[EffectiveDate] AS [EffectiveDate],
    [Project1].[EmployeeTimeCard_TimeCard_Id] AS [EmployeeTimeCard_TimeCard_Id]
    FROM ( SELECT
         [Extent1].[Id] AS [Id],
         [Extent1].[Name] AS [Name],
         [Extent1].[HireDate] AS [HireDate],
         [Extent2].[Id] AS [Id1],
         [Extent2].[Hours] AS [Hours],
         [Extent2].[EffectiveDate] AS [EffectiveDate],
         [Extent2].[EmployeeTimeCard_TimeCard_Id] AS
                    [EmployeeTimeCard_TimeCard_Id],
         CASE WHEN ([Extent2].[Id] IS NULL) THEN CAST(NULL AS int)
         ELSE 1 END AS [C1]
         FROM  [dbo].[Employees] AS [Extent1]
         LEFT OUTER JOIN [dbo].[TimeCards] AS [Extent2] ON [Extent1].[Id] = [Extent2].[EmployeeTimeCard_TimeCard_Id]
    )  AS [Project1]
    ORDER BY [Project1].[HireDate] ASC,
             [Project1].[Id] ASC, [Project1].[C1] ASC
```

Die großartige Nachricht ist, dass der Code innerhalb der Aktionsmethode vollständig getestet werden kann. Wir müssen keine zusätzlichen Features für unsere Fakes bereitstellen, um den Include-Operator zu unterstützen. Die schlechte Nachricht ist, dass wir den Include-Operator in dem Code verwenden mussten, der persistent bleiben sollte. Dies ist ein gutes Beispiel für die Art von vor-und Nachteile, die Sie bei der Erstellung von Test barem Code auswerten müssen. In manchen Zeiten ist es erforderlich, dass Persistenzprobleme außerhalb der Repository-Abstraktion verbleibt, um Leistungsziele zu erreichen.

Die Alternative zum Eager Loading ist Lazy Loading. Lazy Load bedeutet, dass der Geschäfts Code *nicht* erforderlich ist, um die Anforderung für die zugehörigen Daten explizit anzukündigen. Stattdessen werden unsere Entitäten in der Anwendung verwendet, und wenn zusätzliche Daten benötigt werden Entity Framework werden die Daten bei Bedarf geladen.

### <a name="lazy-loading"></a>Lazy Loading

Es ist leicht vorstellbar, ein Szenario zu finden, in dem wir nicht wissen, welche Daten eine Geschäftslogik benötigt. Möglicherweise wissen Sie, dass die Logik ein Employee-Objekt benötigt, aber wir können in verschiedene Ausführungs Pfade verzweigen, in denen einige dieser Pfade Zeitkarten Informationen vom Mitarbeiter erfordern, und andere nicht. Szenarios wie diese eignen sich hervorragend für implizite Lazy Loading, da Daten nach Bedarf nach Bedarf angezeigt werden.

Lazy Load, auch als verzögertes Laden bezeichnet, erfüllt einige Anforderungen an unsere Entitäts Objekte. POCOS mit der wahren Persistenz von Persistenz würde keinerlei Anforderungen von der Persistenzebene genügen, aber es ist praktisch unmöglich, eine echte Persistenz zu gewährleisten.Stattdessen messen wir die Persistenz der Persistenz in relativen Grad. Es wäre bedauerlich, wenn wir von einer persistenzorientierten Basisklasse erben oder eine spezialisierte Sammlung verwenden müssen, um Lazy Loading in POCOS zu erzielen. Glücklicherweise hat EF4 eine weniger eindringliche Lösung.

### <a name="virtually-undetectable"></a>Nahezu nicht erkennbar

Wenn poco-Objekte verwendet werden, kann EF4 dynamisch Lauf Zeit Proxys für Entitäten generieren. Diese Proxys wrappen die materialisierten POCOS unsichtbar und bieten zusätzliche Dienste, indem jeder Get-und Set-Vorgang der einzelnen Eigenschaften abgefangen wird, um zusätzliche Aufgaben auszuführen. Ein solcher Dienst ist das Lazy Loading Feature, nach dem wir suchen. Ein anderer Dienst ist ein effizienter Mechanismus zur Änderungs Nachverfolgung, der aufzeichnen kann, wann das Programm die Eigenschaftswerte einer Entität ändert. Die Liste der Änderungen wird von ObjectContext während der SaveChanges-Methode verwendet, um alle geänderten Entitäten mithilfe von Update-Befehlen beizubehalten.

Damit diese Proxys funktionieren, benötigen Sie jedoch eine Möglichkeit, mit den Get-und Set-Vorgängen für eine Entität zu verbinden, und die Proxys erreichen dieses Ziel durch Überschreiben von virtuellen Membern. Wenn wir also implizites Lazy Loading und eine effiziente Änderungs Nachverfolgung wünschen, müssen wir zu den poco-Klassendefinitionen zurückkehren und Eigenschaften als virtuell markieren.

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

Wir können weiterhin sagen, dass die Mitarbeiter Entität größtenteils persistent ist. Die einzige Anforderung besteht darin, virtuelle Member zu verwenden, und dies hat keine Auswirkung auf die Testability des Codes. Wir müssen nicht von einer speziellen Basisklasse ableiten oder sogar eine spezielle Sammlung verwenden, die für die Lazy Loading reserviert ist. Wie der Code zeigt, steht jede Klasse, die ICollection T implementiert, &lt; &gt; zum Speichern verwandter Entitäten zur Verfügung.

Es gibt auch eine geringfügige Änderung, die wir in unserer Arbeitseinheit vornehmen müssen. Lazy *Load ist Standard* mäßig deaktiviert, wenn Sie direkt mit einem ObjectContext-Objekt arbeiten. Es gibt eine Eigenschaft, die für die ContextOptions-Eigenschaft festgelegt werden kann, um verzögertes Laden zu ermöglichen, und wir können diese Eigenschaft in unserer echten Arbeitseinheit festlegen, wenn wir Lazy Loading überall aktivieren möchten.

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
         public SqlUnitOfWork() {
             // ...
             _context = new ObjectContext(connectionString);
             _context.ContextOptions.LazyLoadingEnabled = true;
         }    
         // ...
     }
```

Wenn implizites Lazy Loading aktiviert ist, kann der Anwendungscode einen Mitarbeiter und die zugeordneten Zeitkarten des Mitarbeiters verwenden, während der verbleibende Arbeitsaufwand, den EF zum Laden zusätzlicher Daten benötigt, nicht vollständig unbekannt ist.

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

Lazy Load vereinfacht das Schreiben des Anwendungs Codes, und mit dem Proxy Magic bleibt der Code vollständig prüfbar. In-Memory-Fakes der Arbeitseinheit können bei Bedarf einfach gefälschte Entitäten mit zugeordneten Daten vorab geladen werden.

An dieser Stelle wird das Entwickeln von Depots mithilfe von IObjectSet &lt; T und das unter &gt; Suchen von Abstraktionen zum Ausblenden aller Vorzeichen des persistenzframe Works berücksichtigt.

## <a name="custom-repositories"></a>Benutzerdefinierte Depots

Als wir zuerst das Entwurfsmuster für Arbeitseinheiten in diesem Artikel vorgestellt haben, haben wir einen Beispielcode dafür bereitgestellt, wie die Arbeitseinheit aussehen könnte. Wir stellen diese ursprüngliche Idee mit dem Zeitkarten Szenario für Mitarbeiter und Mitarbeiter wieder her, mit dem wir gearbeitet haben.

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

Der Hauptunterschied zwischen dieser Arbeitseinheit und der Arbeitseinheit, die wir im letzten Abschnitt erstellt haben, besteht darin, wie diese Arbeitseinheit keine Abstraktionen aus EF4 Framework verwendet (es gibt kein IObjectSet &lt; T &gt; ). IObjectSet &lt; T &gt; funktioniert gut als Repository-Schnittstelle, aber die API, die Sie verfügbar macht, ist möglicherweise nicht perfekt an die Anforderungen unserer Anwendung angepasst. In diesem bevorstehenden Ansatz stellen wir die Repository mithilfe einer benutzerdefinierten IRepository &lt; T- &gt; Abstraktion dar.

Viele Entwickler, die sich auf Test gesteuerte Entwurfs-, Verhaltens gesteuerte Entwurfs-und Domänen gesteuerte Methoden entwickeln, bevorzugen den IRepository &lt; T- &gt; Ansatz aus verschiedenen Gründen. Zuerst stellt die IRepository &lt; T- &gt; Schnittstelle eine "antibeschädigungsebene" dar. Wie von Eric Evans in seinem domänengesteuerten Entwurfs Buch beschrieben, behält eine antibeschädigungs Schicht Ihren Domänen Code von Infrastruktur-APIs, wie z. b. eine persistenzapi, fern Zweitens können Entwickler Methoden im Repository erstellen, die genau den Anforderungen einer Anwendung entsprechen (wie beim Schreiben von Tests erkannt). Beispielsweise kann es vorkommen, dass wir häufig eine einzelne Entität mit einem ID-Wert suchen müssen, damit wir der Repository-Schnittstelle eine findByID-Methode hinzufügen können.Die Definition von IRepository &lt; T &gt; sieht wie folgt aus:

``` csharp
    public interface IRepository<T>
                    where T : class, IEntity {
        IQueryable<T> FindAll();
        IQueryable<T> FindWhere(Expression<Func\<T, bool>> predicate);
        T FindById(int id);       
        void Add(T newEntity);
        void Remove(T entity);
    }
```

Beachten Sie, dass Sie eine iquerable T- &lt; &gt; Schnittstelle verwenden, um Entitäts Sammlungen verfügbar zu machen. Iquervable &lt; T &gt; ermöglicht, dass LINQ-Ausdrucks Baumstrukturen in den EF4-Anbieter fließen und dem Anbieter eine ganzheitliche Ansicht der Abfrage zur Verfügung steht. Eine zweite Option wäre die Rückgabe von IEnumerable &lt; T &gt; . Dies bedeutet, dass der EF4 LINQ-Anbieter nur die im Repository erstellten Ausdrücke sehen kann. Alle Gruppierungen, Reihenfolge und Projektionen, die außerhalb des Repository durchgeführt werden, werden nicht in den SQL-Befehl zusammengefasst, der an die Datenbank gesendet wird. Dies kann die Leistung beeinträchtigen. Andererseits überrascht ein Repository, das nur IEnumerable T- &lt; &gt; Ergebnisse zurückgibt, nie einen neuen SQL-Befehl. Beide Ansätze funktionieren, und beide Ansätze bleiben prüfbar.

Es ist einfach, eine einzige Implementierung der IRepository T- &lt; &gt; Schnittstelle mithilfe von Generika und der EF4 ObjectContext-API bereitzustellen.

``` csharp
    public class SqlRepository<T> : IRepository<T>
                                    where T : class, IEntity {
        public SqlRepository(ObjectContext context) {
            _objectSet = context.CreateObjectSet<T>();
        }
        public IQueryable<T> FindAll() {
            return _objectSet;
        }
        public IQueryable<T> FindWhere(
                               Expression<Func\<T, bool>> predicate) {
            return _objectSet.Where(predicate);
        }
        public T FindById(int id) {
            return _objectSet.Single(o => o.Id == id);
        }
        public void Add(T newEntity) {
            _objectSet.AddObject(newEntity);
        }
        public void Remove(T entity) {
            _objectSet.DeleteObject(entity);
        }
        protected ObjectSet<T> _objectSet;
    }
```

Der IRepository &lt; T- &gt; Ansatz bietet uns eine zusätzliche Kontrolle über unsere Abfragen, da ein Client eine Methode aufrufen muss, um eine Entität zu erhalten. In der-Methode könnten wir zusätzliche Überprüfungen und LINQ-Operatoren zur Durchsetzung von Anwendungs Einschränkungen bereitstellen. Beachten Sie, dass die Schnittstelle über zwei Einschränkungen für den generischen Typparameter verfügt. Die erste Einschränkung ist die von ObjectSet T benötigte Klasse Cons &lt; &gt; , und die zweite Einschränkung zwingt unsere Entitäten, IEntity – eine Abstraktion zu implementieren, die für die Anwendung erstellt wurde. Die IEntity-Schnittstelle erzwingt Entitäten, dass eine lesbare ID-Eigenschaft vorhanden ist, und wir können diese Eigenschaft dann in der findByID-Methode verwenden. IEntity ist mit folgendem Code definiert.

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

IEntity könnte als geringfügige Verletzung der Persistenz bei der Persistenz angesehen werden, da unsere Entitäten zum Implementieren dieser Schnittstelle erforderlich sind. Denken Sie daran, dass Persistenz bei der Persistenz von Kompromisse liegt, und für viele der findByID-Funktionen wird die von der-Schnittstelle festgelegten Einschränkung Die-Schnittstelle hat keine Auswirkung auf die Testability.

Zum Instanziieren eines Live IRepository &lt; T &gt; ist ein EF4 ObjectContext erforderlich, sodass die Instanziierung durch eine konkrete Arbeitseinheits Implementierung verwaltet werden muss.

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;

            _context = new ObjectContext(connectionString);
            _context.ContextOptions.LazyLoadingEnabled = true;
        }

        public IRepository<Employee> Employees {
            get {
                if (_employees == null) {
                    _employees = new SqlRepository<Employee>(_context);
                }
                return _employees;
            }
        }

        public IRepository<TimeCard> TimeCards {
            get {
                if (_timeCards == null) {
                    _timeCards = new SqlRepository<TimeCard>(_context);
                }
                return _timeCards;
            }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        SqlRepository<Employee> _employees = null;
        SqlRepository<TimeCard> _timeCards = null;
        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

### <a name="using-the-custom-repository"></a>Verwenden des benutzerdefinierten Repository

Die Verwendung des benutzerdefinierten Repository unterscheidet sich nicht wesentlich von der Verwendung des Repository auf der Grundlage von IObjectSet &lt; T &gt; . Anstatt LINQ-Operatoren direkt auf eine Eigenschaft anzuwenden, müssen wir zuerst eine der Repository-Methoden aufrufen, um einen iquerable T-Verweis zu erfassen &lt; &gt; .

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Beachten Sie, dass der zuvor implementierte benutzerdefinierte Include-Operator unverändert funktioniert. Die findByID-Methode des Repository entfernt duplizierte Logik von Aktionen, die versuchen, eine einzelne Entität abzurufen.

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

Es gibt keinen signifikanten Unterschied bei der Testability der beiden Ansätze, die wir untersucht haben. Wir könnten gefälschte Implementierungen von IRepository T bereitstellen, indem wir konkrete Klassen erstellen, die &lt; &gt; von HashSet Employee unterstützt werden, &lt; &gt; genau wie im letzten Abschnitt. Allerdings bevorzugen einige Entwickler die Verwendung von Mockobjekten und Pseudo Objekt-Frameworks, anstatt Fakes zu entwickeln. Wir werden uns mit der Verwendung von Mock beschäftigen, um unsere Implementierung zu testen und die Unterschiede zwischen den Pseudo-und Fakes im nächsten Abschnitt zu erörtern.

### <a name="testing-with-mocks"></a>Testen mit Mock

Es gibt verschiedene Ansätze zum Entwickeln von Martin Fowler, der einen "Test Double" aufruft. Ein Test Double (z. b. ein Movie Stuntdouble) ist ein Objekt, das Sie für die "Position" bei echten Produktions Objekten während der Tests erstellen. Bei den in-Memory-Depots, die wir erstellt haben, handelt es sich um Test Doubles für die Depots, die SQL Server Wir haben gesehen, wie diese Test-Doubles während der Komponententests verwendet werden, um den Code zu isolieren und die Tests schnell ausführen zu lassen.

Die Test Doubles, die wir erstellt haben, verfügen über echte, funktionierende Implementierungen. Im Hintergrund speichert jedes eine konkrete Auflistung von Objekten, und Sie fügen Objekte aus dieser Auflistung hinzu und entfernen Sie, während das Repository während eines Tests bearbeitet wird. Einige Entwickler, die Ihre Tests erstellen möchten, verdoppeln diese Art – mit echtem Code und funktionierenden Implementierungen.Diese Test Doubles werden als *Fakes*bezeichnet. Sie verfügen über funktionierende Implementierungen, sind aber nicht für die Verwendung in der Produktion ausreichend. Das Fake-Repository schreibt tatsächlich nicht in die Datenbank. Der gefälschte SMTP-Server sendet tatsächlich keine e-Mail-Nachricht über das Netzwerk.

### <a name="mocks-versus-fakes"></a>Pseudo-und Fakes

Es gibt einen anderen Typ von Test Double, der als *Mock*bezeichnet wird. Obwohl Fakes über funktionierende Implementierungen verfügen, gibt es keine Implementierung. Mithilfe eines Pseudo Objekt Frameworks erstellen wir diese Mockobjekte zur Laufzeit und verwenden Sie als Test Doubles. In diesem Abschnitt verwenden wir das Open Source-Framework für das Microsoft-Framework. Im folgenden finden Sie ein einfaches Beispiel für die dynamische Erstellung eines Test Double für ein Employee-Repository mithilfe von "muq".

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

Wir stellen für eine IRepository-Mitarbeiter Implementierung eine Microsoft-Implementierung von muq vor &lt; &gt; und erstellen dynamisch eine. Wir können zu dem Objekt gelangen, das IRepository Employee implementiert, indem wir auf &lt; &gt; die Object-Eigenschaft des Mock &lt; T-Objekts zugreifen &gt; . Es ist das innere Objekt, das wir an unsere Controller übergeben können, und Sie wissen nicht, ob es sich um ein Test Double oder das echte Repository handelt. Wir können Methoden für das Objekt so aufrufen, wie Methoden für ein Objekt mit einer echten Implementierung aufgerufen werden.

Beim Aufrufen der Add-Methode müssen Sie sich Fragen, was das mockrepository tun wird. Da keine Implementierung hinter dem Mock-Objekt vorhanden ist, wird von Add nichts unterstützt. Es gibt keine konkrete Sammlung im Hintergrund, wie wir es mit den von uns geschriebenen Fakes getan haben, sodass der Mitarbeiter verworfen wird. Wie sieht es mit dem Rückgabewert von "findByID" aus? In diesem Fall übernimmt das Mock-Objekt das einzige, was es tun kann. es wird ein Standardwert zurückgegeben. Da wir einen Verweistyp (einen Mitarbeiter) zurückgeben, ist der Rückgabewert ein NULL-Wert.

Mock klingen möglicherweise wertlos. Allerdings gibt es noch zwei weitere Features, über die wir nicht gesprochen haben. Zuerst zeichnet das MOQ-Framework alle Aufrufe auf dem Mock-Objekt auf. Zu einem späteren Zeitpunkt im Code können Sie die Frage stellen, ob jemand die Add-Methode aufgerufen hat, oder ob jemand die findByID-Methode aufgerufen hat. Wir werden später sehen, wie diese "Black Box"-Aufzeichnungsfunktion in Tests verwendet werden kann.

Das zweite hervorragend ist, wie wir MOQ verwenden können, um ein Mock-Objekt mit *Erwartungen*zu programmieren. Eine Erwartungs Angabe weist das Mock-Objekt an, wie auf eine bestimmte Interaktion reagiert werden soll. Beispielsweise können wir eine Erwartungs Annahme in unser Mock programmieren und Sie darüber informieren, dass ein Employee-Objekt zurückgegeben wird, wenn ein Benutzer "findByID" aufruft Das-Framework verwendet eine Setup-API und Lambda-Ausdrücke, um diese Erwartungen zu programmieren.

``` csharp
    [TestMethod]
    public void MockSample() {
        Mock<IRepository<Employee>> mock =
            new Mock<IRepository<Employee>>();
        mock.Setup(m => m.FindById(5))
            .Returns(new Employee {Id = 5});
        IRepository<Employee> repository = mock.Object;
        var employee = repository.FindById(5);
        Assert.IsTrue(employee.Id == 5);
    }
```

In diesem Beispiel stellen wir fest, dass Sie ein Repository dynamisch erstellen und dann das Repository mit einer Erwartung programmieren. Die Erwartungs Angabe weist das Mock-Objekt an, ein neues Employee-Objekt mit dem ID-Wert 5 zurückzugeben, wenn jemand die findByID-Methode aufruft und den Wert 5 übergibt. Dieser Test wird erfolgreich durchlaufen, und es ist nicht erforderlich, eine vollständige Implementierung für das gefälschte IRepository t zu erstellen &lt; &gt; .

Schauen wir uns noch einmal die Tests an, die wir zuvor geschrieben haben, und arbeiten Sie zur Verwendung von Pseudo-anstelle von Fakes. Wie zuvor verwenden wir eine Basisklasse, um die gemeinsamen Teile der Infrastruktur einzurichten, die wir für alle Tests des Controllers benötigen.

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .AsQueryable();
            _repository = new Mock<IRepository<Employee>>();
            _unitOfWork = new Mock<IUnitOfWork>();
            _unitOfWork.Setup(u => u.Employees)
                       .Returns(_repository.Object);
            _controller = new EmployeeController(_unitOfWork.Object);
        }

        protected IQueryable<Employee> _employeeData;
        protected Mock<IUnitOfWork> _unitOfWork;
        protected EmployeeController _controller;
        protected Mock<IRepository<Employee>> _repository;
    }
```

Der Setup Code bleibt größtenteils unverändert. Anstatt Fakes zu verwenden, verwenden wir MOQ, um Mock-Objekte zu erstellen. Die-Basisklasse ordnet an, dass die Mock-Arbeitseinheit ein Mock-Repository zurückgibt, wenn der Code die Employees-Eigenschaft aufruft. Der Rest des Mock-Setups erfolgt in den Test Vorrichtungen, die für jedes bestimmte Szenario vorgesehen sind. Beispielsweise wird durch die Test Fixierung für die Index Aktion das mockrepository so eingerichtet, dass eine Liste von Mitarbeitern zurückgegeben wird, wenn die Aktion die FindAll-Methode des mockrepository aufruft.

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        public EmployeeControllerIndexActionTests() {
            _repository.Setup(r => r.FindAll())
                        .Returns(_employeeData);
        }
        // .. tests
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count());
        }
        // .. and more tests
    }
```

Mit Ausnahme der Erwartungen sehen unsere Tests den Tests ähnlich wie zuvor. Mit der Aufzeichnungs Fähigkeit eines Mock-Frameworks können wir die Tests jedoch in einem anderen Winkel durchgehen. Wir betrachten diese neue Perspektive im nächsten Abschnitt.

### <a name="state-versus-interaction-testing"></a>Testzustand im Vergleich zu Interaktion

Es gibt verschiedene Techniken, die Sie zum Testen von Software mit Mock-Objekten verwenden können. Ein Ansatz besteht in der Verwendung von Zustands basierten Tests, was in diesem Artikel bisher geschehen ist. Zustands basierte Tests machen Assertionen zum Zustand der Software. Im letzten Test haben wir eine Aktionsmethode auf dem Controller aufgerufen und eine Aussage über das Modell vorgenommen, das erstellt werden soll. Hier sind einige weitere Beispiele für den Test Status:

-   Vergewissern Sie sich, dass das Repository das neue Mitarbeiter Objekt enthält, nachdem Create ausgeführt wurde.
-   Vergewissern Sie sich, dass das Modell nach der Ausführung des Indexes eine Liste aller Mitarbeiter enthält.
-   Vergewissern Sie sich, dass das Repository nach der Ausführung von DELETE keinen bestimmten Mitarbeiter enthält.

Ein weiterer Ansatz, den Sie mit Mock-Objekten erkennen, ist das Überprüfen von *Interaktionen*. Während Zustands basierte Tests Assertionen zum Status von Objekten machen, werden durch Interaktions basierte Tests Assertionen für die Interaktion von Objekten durchführt. Beispiel:

-   Vergewissern Sie sich, dass der Controller beim Ausführen von CREATE die Add-Methode des Repository aufruft.
-   Vergewissern Sie sich, dass der Controller die FindAll-Methode des Repository aufruft, wenn Index ausgeführt wird
-   Vergewissern Sie sich, dass der Controller die Commit-Methode der Arbeitseinheit aufruft, um beim Ausführen der Bearbeitung Änderungen zu speichern.

Interaktions Tests erfordern häufig weniger Testdaten, da wir nicht innerhalb von Auflistungen stehen und die Anzahl überprüfen. Wenn wir z. b. wissen, dass die Aktion "Details" die findByID-Methode eines Repository mit dem korrekten Wert aufruft, verhält sich die Aktion wahrscheinlich ordnungsgemäß. Wir können dieses Verhalten überprüfen, ohne Testdaten einzurichten, die von findByID zurückgegeben werden sollen.

``` csharp
    [TestClass]
    public class EmployeeControllerDetailsActionTests
               : EmployeeControllerTestBase {
         // ...
        [TestMethod]
        public void ShouldInvokeRepositoryToFindEmployee() {
            var result = _controller.Details(_detailsId);
            _repository.Verify(r => r.FindById(_detailsId));
        }
        int _detailsId = 1;
    }
```

Das einzige Setup, das in der obigen Test Fixierung erforderlich ist, ist das von der Basisklasse bereitgestellte Setup. Wenn wir die Controller Aktion aufrufen, zeichnet MOQ die Interaktionen mit dem Mock-Repository auf. Mithilfe der Verify-API von WQ können wir die Frage stellen, ob der Controller "findByID" mit dem richtigen ID-Wert aufgerufen hat. Wenn der Controller die Methode nicht aufgerufen hat oder die Methode mit einem unerwarteten Parameterwert aufgerufen hat, löst die Verify-Methode eine Ausnahme aus, und der Test schlägt fehl.

Es folgt ein weiteres Beispiel, um zu überprüfen, ob die CREATE-Aktion Commit für die aktuelle Arbeitseinheit aufruft.

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

Eine Gefahr beim Testen der Interaktion ist die Tendenz, Interaktionen zu überschreiten. Die Fähigkeit des Mockobjekts, jede Interaktion mit dem Mock-Objekt aufzuzeichnen und zu überprüfen, bedeutet nicht, dass der Test versuchen soll, jede Interaktion zu überprüfen. Einige Interaktionen sind Implementierungsdetails, und Sie sollten nur die Interaktionen überprüfen, die zum erfüllen des aktuellen Tests *erforderlich* sind.

Die Wahl zwischen den Mocken oder Fakes hängt größtenteils von dem System ab, das Sie testen, und Ihren persönlichen (oder Team-) Voreinstellungen. Mock-Objekte können die Menge an Code, den Sie zum Implementieren von Test Doubles benötigen, drastisch reduzieren, aber nicht jeder ist für die Programmierung von Erwartungen und das Überprüfen von Interaktionen

## <a name="conclusions"></a>Zusammenfassung

In diesem Whitepaper haben wir verschiedene Ansätze zum Erstellen von testbarem Code gezeigt, während wir die ADO.NET-Entity Framework für die Daten Persistenz verwenden. Wir können integrierte Abstraktionen wie IObjectSet t nutzen &lt; &gt; oder eigene Abstraktionen wie IRepository &lt; t erstellen &gt; .In beiden Fällen ermöglicht die poco-Unterstützung im ADO.NET-Entity Framework 4,0, dass die Consumer dieser Abstraktionen permanent ignoriert werden und hochgradig testfähig bleiben. Zusätzliche EF4-Features wie implizites Lazy Loading ermöglichen, dass Geschäfts-und Anwendungs Dienst Code funktionieren, ohne sich Gedanken über die Details eines relationalen Datenspeicher zu machen. Schließlich können die von uns erstellten Abstraktionen leicht in Komponententests hinein oder gefälscht werden, und wir können diese Test Doubles verwenden, um schnelle, hochgradig isolierte und zuverlässige Tests zu erzielen.

### <a name="additional-resources"></a>Zusätzliche Ressourcen

-   Robert C. Martin " [das Prinzip der einzelnen Verantwortung](https://www.objectmentor.com/resources/articles/srp.pdf)"
-   Martin Fowler, [Katalog mit Mustern](https://www.martinfowler.com/eaaCatalog/index.html) aus *Mustern der Unternehmens Anwendungsarchitektur*
-   Griffin Caprio, " [Abhängigkeitsinjektion](https://msdn.microsoft.com/magazine/cc163739.aspx)"
-   Blog zur Daten Programmierbarkeit, "Exemplarische Vorgehensweise [: Test gesteuerte Entwicklung mit dem Entity Framework 4,0](/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)".
-   Daten Programmierbarkeits Blog " [using Repository and Unit of Work Patterns with Entity Framework 4,0](/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"
-   Aaron Jensen, " [Einführung in Computerspezifikationen](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"
-   Eric Lee, " [BDD with MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)"
-   Eric Evans, " [Domain-gesteuerte Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"
-   Martin Fowler, "Mock [sind keine Stuf"](https://martinfowler.com/articles/mocksArentStubs.html)
-   Martin Fowler, " [Test Double](https://martinfowler.com/bliki/TestDouble.html)"
-   [Moq](https://code.google.com/p/moq/)

### <a name="biography"></a>Biografie

Scott allen ist Mitglied des technischen Personals bei Pluralsight und der Gründer von OdeToCode.com. In 15 Jahren kommerzieller Softwareentwicklung hat Scott an Lösungen für alles von 8-Bit Embedded-Geräten bis hin zu hochgradig skalierbaren ASP.NET-Webanwendungen gearbeitet. Scott finden Sie in seinem Blog unter "odeycode" oder auf Twitter unter [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode) .
