---
title: Testfähigkeit und Entity Framework 4.0 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 96b6b2791f12b7d60a233f7e6dc77e5a8579fb66
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434312"
---
# <a name="testability-and-entity-framework-40"></a>Testbarkeit und Entity Framework 4.0
Scott Allen

Veröffentlicht: Mai 2010

## <a name="introduction"></a>Einführung

In diesem Whitepaper wird beschrieben und veranschaulicht, wie sie testbaren Code mit den ADO.NET Entity Framework 4.0 und Visual Studio 2010 schreiben. In diesem Dokument wird nicht versucht, sich auf eine bestimmte Testmethodik zu konzentrieren, z. B. TDD (Test-Driven Design) oder behavior-driven design (BDD). Stattdessen konzentriert sich dieses Dokument auf das Schreiben von Code, der die ADO.NET Entity Framework verwendet, aber weiterhin einfach zu isolieren und automatisiert zu testen. Wir untersuchen gängige Entwurfsmuster, die das Testen in Datenzugriffsszenarien erleichtern, und erfahren, wie diese Muster bei der Verwendung des Frameworks angewendet werden. Wir werden auch bestimmte Funktionen des Frameworks betrachten, um zu sehen, wie diese Features in testbarem Code funktionieren können.

## <a name="what-is-testable-code"></a>Was ist testbarer Code?

Die Möglichkeit, eine Software mithilfe automatisierter Komponententests zu überprüfen, bietet viele wünschenswerte Vorteile. Jeder weiß, dass gute Tests die Anzahl der Softwarefehler in einer Anwendung reduzieren und die Qualität der Anwendung erhöhen werden - aber die Anbelegung von Komponententests geht weit über das bloße Auffinden von Fehlern hinaus.

Eine gute Komponententestsammlung ermöglicht es einem Entwicklungsteam, Zeit zu sparen und die Kontrolle über die von ihnen erstellte Software zu behalten. Ein Team kann Änderungen am vorhandenen Code vornehmen, Software umgestalten, umgestalten und umstrukturieren, um neuen Anforderungen gerecht zu werden, und neue Komponenten zu einer Anwendung hinzufügen, während sie weiß, dass die Testsammlung das Verhalten der Anwendung überprüfen kann. Komponententests sind Teil eines schnellen Feedback-Zyklus, um Änderungen zu erleichtern und die Wartbarkeit von Software bei zunehmender Komplexität zu erhalten.

Komponententests haben jedoch ihren Preis. Ein Team muss die Zeit investieren, um Komponententests zu erstellen und zu warten. Der Aufwand, der zum Erstellen dieser Tests erforderlich ist, hängt direkt mit der **Testbarkeit** der zugrunde liegenden Software zusammen. Wie einfach ist die Software zu testen? Ein Team, das Software mit Blick auf die Testbarkeit entwickelt, erstellt schnellere effektive Tests als das Team, das mit nicht testbarer Software arbeitet.

Microsoft hat das ADO.NET Entity Framework 4.0 (EF4) unter Berücksichtigung der Testbarkeit entwickelt. Dies bedeutet nicht, dass Entwickler Komponententests für Frameworkcode selbst schreiben. Stattdessen erleichtern die Testbarkeitsziele für EF4 das Erstellen von testbarem Code, der auf dem Framework aufbaut. Bevor wir uns bestimmte Beispiele ansehen, lohnt es sich, die Qualitäten des testbaren Codes zu verstehen.

### <a name="the-qualities-of-testable-code"></a>Die Qualitäten des testbaren Codes

Code, der leicht zu testen ist, weist immer mindestens zwei Merkmale auf. Erstens ist testbarer Code leicht zu **beobachten.** Angesichts einiger Eingaben sollte es einfach sein, die Ausgabe des Codes zu beobachten. Beispielsweise ist das Testen der folgenden Methode einfach, da die Methode direkt das Ergebnis einer Berechnung zurückgibt.

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

Das Testen einer Methode ist schwierig, wenn die Methode den berechneten Wert in einen Netzwerksocket, eine Datenbanktabelle oder eine Datei wie den folgenden Code schreibt. Der Test muss zusätzliche Arbeit ausführen, um den Wert abzurufen.

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

Zweitens ist testbarer Code einfach zu **isolieren.** Verwenden wir den folgenden Pseudocode als schlechtes Beispiel für testbaren Code.

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

Die Methode ist leicht zu beobachten – wir können eine Versicherungspolice abschließen und überprüfen, ob der Rückgabewert mit dem erwarteten Ergebnis übereinstimmt. Um die Methode zu testen, müssen wir jedoch eine Datenbank mit dem richtigen Schema installiert haben, und den SMTP-Server konfigurieren, falls die Methode versucht, eine E-Mail zu senden.

Der Komponententest möchte nur die Berechnungslogik innerhalb der Methode überprüfen, aber der Test schlägt möglicherweise fehl, weil der E-Mail-Server offline ist oder weil der Datenbankserver verschoben wurde. Beide Fehler haben nichts mit dem Verhalten zu tun, das der Test überprüfen möchte. Das Verhalten ist schwer zu isolieren.

Softwareentwickler, die testbaren Code schreiben möchten, streben häufig danach, eine Trennung von Bedenken in dem von ihnen geschriebenen Code beizubehalten. Die obige Methode sollte sich auf die Geschäftsberechnungen konzentrieren und die Datenbank- und E-Mail-Implementierungsdetails an andere Komponenten delegieren. Robert C. Martin nennt dies das Prinzip der einheitlichen Verantwortung. Ein Objekt sollte eine einzelne, enge Verantwortung einkapseln, z. B. den Wert einer Richtlinie berechnen. Für alle anderen Datenbank- und Benachrichtigungsarbeiten sollte ein anderes Objekt verantwortlich sein. Auf diese Weise geschriebener Code ist einfacher zu isolieren, da er sich auf eine einzelne Aufgabe konzentriert.

In .NET haben wir die Abstraktionen, die wir brauchen, um dem Prinzip der einheitlichen Verantwortung zu folgen und Isolation zu erreichen. Wir können Schnittstellendefinitionen verwenden und den Code zwingen, die Schnittstellenabstraktion anstelle eines konkreten Typs zu verwenden. Später in diesem Artikel werden wir sehen, wie eine Methode wie das schlechte Beispiel oben dargestellt mit Schnittstellen arbeiten kann, die *aussehen,* als würden sie mit der Datenbank sprechen. Zur Testzeit können wir jedoch eine Dummy-Implementierung ersetzen, die nicht mit der Datenbank spricht, sondern Daten im Speicher enthält. Diese Dummy-Implementierung isoliert den Code von nicht verwandten Problemen im Datenzugriffscode oder in der Datenbankkonfiguration.

Die Isolierung hat zusätzliche Vorteile. Die Ausführung der Geschäftsberechnung in der letzten Methode sollte nur wenige Millisekunden in Anspruch nehmen, aber der Test selbst kann mehrere Sekunden lang ausgeführt werden, wenn der Code im Netzwerk gehüpft ist und mit verschiedenen Servern spricht. Komponententests sollten schnell durchgeführt werden, um kleine Änderungen zu erleichtern. Komponententests sollten ebenfalls wiederholbar sein und nicht fehlschlagen, da eine Komponente, die nicht mit dem Test zusammenhängt, ein Problem hat. Das Schreiben von Code, der leicht zu beobachten und zu isolieren ist, bedeutet, dass Entwickler weniger Zeit beim Schreiben von Tests für den Code haben, weniger Zeit damit verbringen, auf die Ausführung von Tests zu warten, und, was noch wichtiger ist, weniger Zeit damit verbringen, Fehler aufzuspüren, die nicht vorhanden sind.

Hoffentlich können Sie die Vorteile des Testens zu schätzen wissen und die Qualitäten verstehen, die testbarer Code aufweist. Wir sind dabei, wie Code geschrieben wird, der mit EF4 funktioniert, um Daten in einer Datenbank zu speichern, während sie beobachtbar und leicht zu isolieren sind.

## <a name="design-patterns-for-data-persistence"></a>Entwurfsmuster für Diepersistenz

Beide schlechten Beispiele, die zuvor vorgestellt wurden, hatten zu viele Verantwortlichkeiten. Das erste schlechte Beispiel musste eine Berechnung durchführen *und* in eine Datei schreiben. Das zweite schlechte Beispiel musste Daten aus einer Datenbank lesen *und* eine Geschäftsberechnung durchführen *und* E-Mails senden. Durch das Entwerfen kleinerer Methoden, die Anliegen trennen und Die Verantwortung an andere Komponenten delegieren, machen Sie große Fortschritte beim Schreiben von testbarem Code. Das Ziel besteht darin, Funktionalität zu erstellen, indem Aktionen aus kleinen und fokussierten Abstraktionen erstellt werden.

Wenn es um Datenpersistenz geht, sind die kleinen und fokussierten Abstraktionen, nach denen wir suchen, so häufig, dass sie als Designmuster dokumentiert wurden. Martin Fowlers Buch Patterns of Enterprise Application Architecture war das erste Werk, das diese Muster im Druck beschreibt. In den folgenden Abschnitten finden Sie eine kurze Beschreibung dieser Muster, bevor wir zeigen, wie diese ADO.NET Entity Framework diese Muster implementiert und mit ihnen arbeitet.

### <a name="the-repository-pattern"></a>Repositorymuster

Fowler sagt, dass ein Repository "zwischen den Domänen- und Datenzuordnungsebenen über eine sammlungsähnliche Schnittstelle für den Zugriff auf Domänenobjekte vermittelt". Das Ziel des Repository-Musters ist es, Code von den Minutien des Datenzugriffs zu isolieren, und wie wir zuvor gesehen haben, ist die Isolierung ein erforderliches Merkmal für die Testbarkeit.

Der Schlüssel zur Isolierung ist, wie das Repository Objekte mithilfe einer sammlungsähnlichen Schnittstelle verfügbar macht. Die Logik, die Sie schreiben, um das Repository zu verwenden, hat keine Ahnung, wie das Repository die von Ihnen anfordernden Objekte materialisieren wird. Das Repository kann mit einer Datenbank sprechen, oder es gibt einfach Objekte aus einer In-Memory-Auflistung zurück. Ihr Code muss nur wissen, dass das Repository die Sammlung zu verwalten scheint, und Sie können Objekte aus der Auflistung abrufen, hinzufügen und löschen.

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

Wir werden einige Änderungen an der Schnittstellendefinition vornehmen, wenn wir eine Implementierung für EF4 bereitstellen, aber das Grundlegende Konzept bleibt das gleiche. Code kann ein konkretes Repository verwenden, das diese Schnittstelle implementiert, um eine Entität anhand ihres Primärschlüsselwerts abzurufen, eine Auflistung von Entitäten basierend auf der Auswertung eines Prädikats abzurufen oder einfach alle verfügbaren Entitäten abzurufen. Der Code kann auch Entitäten über die Repository-Schnittstelle hinzufügen und entfernen.

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

Da der Code eine Schnittstelle (IRepository of Employee) verwendet, können wir dem Code verschiedene Implementierungen der Schnittstelle bereitstellen. Eine Implementierung kann eine Implementierung sein, die von EF4 unterstützt wird, und das Beibehalten von Objekten in einer Microsoft SQL Server-Datenbank. Eine andere Implementierung (die wir während des Tests verwenden) kann durch eine Speicherliste von Employee-Objekten unterstützt werden. Die Schnittstelle wird dazu beitragen, die Isolierung im Code zu erreichen.

Beachten Sie,&lt;&gt; dass die IRepository T-Schnittstelle keinen Save-Vorgang verfügbar macht. Wie aktualisieren wir vorhandene Objekte? Möglicherweise stoßen Sie auf IRepository-Definitionen, die den Save-Vorgang enthalten, und Implementierungen dieser Repositorys müssen sofort ein Objekt in der Datenbank beibehalten. In vielen Anwendungen möchten wir Objekte jedoch nicht einzeln beibehalten. Stattdessen möchten wir Objekte zum Leben erwecken, vielleicht aus verschiedenen Repositorys, diese Objekte als Teil einer Geschäftsaktivität ändern und dann alle Objekte als Teil eines einzelnen atomaren Vorgangs beibehalten. Glücklicherweise gibt es ein Muster, um diese Art von Verhalten zu ermöglichen.

### <a name="the-unit-of-work-pattern"></a>Die Arbeitseinheit

Fowler sagt, dass eine Arbeitseinheit "eine Liste der Objekte führen wird, die von einer Geschäftstransaktion betroffen sind, und das Ausschreiben von Änderungen und die Lösung von Parallelitätsproblemen koordiniert". Es liegt in der Verantwortung der Arbeitseinheit, Änderungen an den Objekten, die wir aus einem Repository zum Leben erwecken, nachzuverfolgen und alle Änderungen beizubehalten, die wir an den Objekten vorgenommen haben, wenn wir der Arbeitseinheit sagen, dass sie die Änderungen übernehmen soll. Es liegt auch in der Verantwortung der Arbeitseinheit, die neuen Objekte, die wir allen Repositorys hinzugefügt haben, in eine Datenbank einzufügen und die Objekte in eine Datenbank einzufügen und auch das Löschen von Mange zu verwalten.

Wenn Sie jemals mit ADO.NET DataSets gearbeitet haben, sind Sie bereits mit dem Arbeitseinheitsmuster vertraut. ADO.NET DataSets hatten die Möglichkeit, unsere Aktualisierungen, Löschungen und das Einfügen von DataRow-Objekten nachzuverfolgen und (mit Hilfe eines TableAdapters) alle unsere Änderungen mit einer Datenbank abzugleichen. DataSet-Objekte modellieren jedoch eine getrennte Teilmenge der zugrunde liegenden Datenbank. Das Arbeitseinheitsmuster weist das gleiche Verhalten auf, arbeitet jedoch mit Geschäftsobjekten und Domänenobjekten, die vom Datenzugriffscode isoliert sind und die Datenbank nicht kennen.

Eine Abstraktion zum Modellieren der Arbeitseinheit im .NET-Code könnte wie folgt aussehen:

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

Durch das Auflegen von Repository-Referenzen aus der Arbeitseinheit können wir sicherstellen, dass eine einzelne Arbeitseinheit samt Arbeitsobjekt die Möglichkeit hat, alle Entitäten zu verfolgen, die während einer Geschäftstransaktion materialisiert wurden. Die Implementierung der Commit-Methode für eine echte Arbeitseinheit ist der Ort, an dem die ganze Magie geschieht, um Änderungen im Arbeitsspeicher mit der Datenbank in Einklang zu bringen. 

Bei einem IUnitOfWork-Verweis kann Code Änderungen an Geschäftsobjekten vornehmen, die aus einem oder mehreren Repositorys abgerufen wurden, und alle Änderungen mithilfe des atomaren Commit-Vorgangs speichern.

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>Das Lazy Load Pattern

Fowler verwendet den Namen lazy load, um "ein Objekt zu beschreiben, das nicht alle Benötigten enthält, aber weiß, wie man es bekommt". Transparentes verzögertes Laden ist ein wichtiges Feature beim Schreiben von testbarem Geschäftscode und beim Arbeiten mit einer relationalen Datenbank. Betrachten Sie als Beispiel den folgenden Code.

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

Wie wird die TimeCards-Auflistung aufgefüllt? Es gibt zwei mögliche Antworten. Eine Antwort ist, dass das Mitarbeiter-Repository, wenn es aufgefordert wird, einen Mitarbeiter abzurufen, eine Abfrage ausgibt, um sowohl den Mitarbeiter zusammen mit den zugehörigen Zeitkarteninformationen des Mitarbeiters abzurufen. In relationalen Datenbanken erfordert dies in der Regel eine Abfrage mit einer JOIN-Klausel und kann dazu führen, dass mehr Informationen abgerufen werden, als eine Anwendung benötigt. Was passiert, wenn die Anwendung niemals die TimeCards-Eigenschaft berühren muss?

Eine zweite Antwort ist, die TimeCards-Eigenschaft "on demand" zu laden. Dieses verzögerte Laden ist implizit und für die Geschäftslogik transparent, da der Code keine speziellen APIs aufruft, um Zeitkarteninformationen abzurufen. Der Code setzt voraus, dass die Zeitkarteninformationen bei Bedarf vorhanden sind. Es gibt einige Magie mit faulen Laden beteiligt, die in der Regel Laufzeit Abfangen von Methodenaufrufe beinhaltet. Der Abfangcode ist für das Gespräch mit der Datenbank und das Abrufen von Zeitkarteninformationen verantwortlich, während die Geschäftslogik frei bleibt, um Geschäftslogik zu sein. Diese verzögerte Lastmagie ermöglicht es dem Geschäftscode, sich von Datenabrufvorgängen zu isolieren und führt zu mehr testbarem Code.

Der Nachteil einer verzögerten Last besteht darin, dass der Code eine zusätzliche Abfrage ausführt, wenn eine *Anwendung* die Zeitkarteninformationen benötigt. Dies ist für viele Anwendungen kein Problem, aber für leistungskritische Anwendungen oder Anwendungen, die eine Reihe von Mitarbeiterobjekten durchlaufen und eine Abfrage ausführen, um Zeitkarten während jeder Iteration der Schleife abzurufen (ein Problem, das häufig als N+1-Abfrageproblem bezeichnet wird), ist das verzögerte Laden ein Ziehen. In diesen Szenarien möchte eine Anwendung möglicherweise zeitkarteninformationen so effizient wie möglich laden.

Glücklicherweise werden wir sehen, wie EF4 sowohl implizite verzögerte Lasten als auch effiziente eifrige Lasten unterstützt, während wir in den nächsten Abschnitt gehen und diese Muster implementieren.

## <a name="implementing-patterns-with-the-entity-framework"></a>Implementieren von Mustern mit dem Entity Framework

Die gute Nachricht ist, dass alle Entwurfsmuster, die wir im letzten Abschnitt beschrieben haben, einfach mit EF4 zu implementieren sind. Um zu demonstrieren, verwenden wir eine einfache ASP.NET MVC-Anwendung, um Mitarbeiter und die zugehörigen Zeitkarteninformationen zu bearbeiten und anzuzeigen. Beginnen wir mit den folgenden "einfachen alten CLR-Objekten" (POCOs). 

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

Diese Klassendefinitionen werden sich leicht ändern, wenn wir verschiedene Ansätze und Features von EF4 untersuchen, aber die Absicht ist, diese Klassen so persistenzunvierungsunwissend (PI) wie möglich zu halten. Ein PI-Objekt weiß *nicht, wie*, oder *sogar, wenn*, der Zustand, den es hält, in einer Datenbank lebt. PI- und POCOs gehen Hand in Hand mit testbarer Software. Objekte, die einen POCO-Ansatz verwenden, sind weniger eingeschränkt, flexibler und einfacher zu testen, da sie ohne vorhandene Datenbank ausgeführt werden können.

Mit den POCOs können wir ein Entity Data Model (EDM) in Visual Studio erstellen (siehe Abbildung 1). Wir werden das EDM nicht verwenden, um Code für unsere Entitäten zu generieren. Stattdessen wollen wir die Entitäten, die wir liebevoll von Hand herstellen, nutzen. Wir verwenden das EDM nur zum Generieren unseres Datenbankschemas und bereitstellen die Metadaten, die EF4 zum Zuordnen von Objekten in die Datenbank benötigt.

![ef test_01](~/ef6/media/eftest-01.jpg)

**Abbildung 1**

Hinweis: Wenn Sie zuerst das EDM-Modell entwickeln möchten, ist es möglich, sauberen POCO-Code aus dem EDM zu generieren. Sie können dies mit einer Visual Studio 2010-Erweiterung tun, die vom Data Programmability-Team bereitgestellt wird. Um die Erweiterung herunterzuladen, starten Sie den Erweiterungs-Manager aus dem Menü Extras in Visual Studio und durchsuchen Sie die Online-Galerie mit Vorlagen nach "POCO" (siehe Abbildung 2). Für EF stehen mehrere POCO-Vorlagen zur Verfügung. Weitere Informationen zur Verwendung der Vorlage finden Sie unter ["Exemplarische Vorgehensweise: POCO-Vorlage für das Entity Framework](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)".

![ef test_02](~/ef6/media/eftest-02.png)

**Abbildung 2**

Von diesem POCO-Startpunkt aus werden wir zwei verschiedene Ansätze für testbaren Code untersuchen. Der erste Ansatz, den ich den EF-Ansatz nenne, weil er Abstraktionen aus der Entity Framework-API nutzt, um Arbeitseinheiten und Repositorys zu implementieren. Im zweiten Ansatz erstellen wir unsere eigenen benutzerdefinierten Repository-Abstraktionen und sehen dann die Vor- und Nachteile jedes Ansatzes. Wir beginnen mit der Erforschung des EF-Ansatzes.  

### <a name="an-ef-centric-implementation"></a>Eine EF-zentrierte Implementierung

Betrachten Sie die folgende Controlleraktion aus einem ASP.NET MVC-Projekt. Die Aktion ruft ein Employee-Objekt ab und gibt ein Ergebnis zurück, um eine detaillierte Ansicht des Mitarbeiters anzuzeigen.

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

Ist der Code testbar? Es gibt mindestens zwei Tests, die wir benötigen, um das Verhalten der Aktion zu überprüfen. Zuerst möchten wir überprüfen, ob die Aktion die richtige Ansicht zurückgibt – ein einfacher Test. Wir möchten auch einen Test schreiben, um zu überprüfen, ob die Aktion den richtigen Mitarbeiter abruft, und wir möchten dies tun, ohne Code zum Abfragen der Datenbank auszuführen. Denken Sie daran, dass wir den zu testenden Code isolieren möchten. Durch die Isolierung wird sichergestellt, dass der Test nicht aufgrund eines Fehlers im Datenzugriffscode oder in der Datenbankkonfiguration fehlschlägt. Wenn der Test fehlschlägt, werden wir wissen, dass wir einen Fehler in der Controllerlogik und nicht in einer niedrigeren Systemkomponente haben.

Um Isolation zu erreichen, benötigen wir einige Abstraktionen wie die Schnittstellen, die wir zuvor für Repositories und Arbeitseinheiten vorgestellt haben. Denken Sie daran, dass das Repository-Muster so konzipiert ist, dass es zwischen Domänenobjekten und dem Datenzuordnungs-Layer vermittelt. In diesem Szenario *ist* EF4 der Datenzuordnungs-Layer und stellt bereits&lt;eine&gt; Repository-ähnliche Abstraktion mit dem Namen IObjectSet T (aus dem Namespace System.Data.Objects) bereit. Die Schnittstellendefinition sieht wie folgt aus.

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

IObjectSet&lt;&gt; T erfüllt die Anforderungen für ein Repository, da es einer&lt;Auflistung&gt;von Objekten ähnelt (über IEnumerable T ) und Methoden zum Hinzufügen und Entfernen von Objekten aus der simulierten Auflistung bereitstellt. Die Methoden Anfügen und Trennen stellen zusätzliche Funktionen der EF4-API bereit. Um IObjectSet&lt;&gt; T als Schnittstelle für Repositories zu verwenden, benötigen wir eine Einheit der Arbeitsabstraktion, um Repositories miteinander zu binden.

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

Eine konkrete Implementierung dieser Schnittstelle wird mit SQL Server gesprochen und ist einfach mit der ObjectContext-Klasse aus EF4 zu erstellen. Die ObjectContext-Klasse ist die eigentliche Arbeitseinheit in der EF4-API.

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

Das Leben eines&lt;&gt; IObjectSets T ist so einfach wie das Aufrufen der CreateObjectSet-Methode des ObjectContext-Objekts. Hinter den Kulissen verwendet das Framework die Metadaten, die wir&lt;im&gt;EDM bereitgestellt haben, um ein konkretes ObjectSet T zu erstellen. Wir bleiben bei der Rückgabe&lt;der&gt; IObjectSet T-Schnittstelle, da sie dazu beiträgt, die Testbarkeit im Clientcode zu erhalten.

Diese konkrete Implementierung ist in der Produktion nützlich, aber wir müssen uns darauf konzentrieren, wie wir unsere IUnitOfWork-Abstraktion verwenden, um das Testen zu erleichtern.

### <a name="the-test-doubles"></a>Die Test-Doppel

Um die Controller-Aktion zu isolieren, müssen wir zwischen der tatsächlichen Arbeitseinheit (unterstützt durch einen ObjectContext) und einer doppelten oder "gefälschten" Arbeitseinheit (Durchführung von In-Memory-Vorgängen) wechseln. Der übliche Ansatz für diese Art des Schaltens besteht darin, den MVC-Controller nicht eine Arbeitseinheit instanziieren zu lassen, sondern die Arbeitseinheit als Konstruktorparameter an den Controller weiterzugeben.

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

Der obige Code ist ein Beispiel für Abhängigkeitsinjektion. Wir erlauben dem Controller nicht, seine Abhängigkeit (die Arbeitseinheit) zu erstellen, sondern die Abhängigkeit in den Controller einzuschleusen. In einem MVC-Projekt ist es üblich, eine benutzerdefinierte Controller-Factory in Kombination mit einem Inversion of Control (IoC)-Container zu verwenden, um die Abhängigkeitsinjektion zu automatisieren. Diese Themen gehen über den Rahmen dieses Artikels hinaus, sie können jedoch den Verweisen am Ende dieses Artikels folgen.

Eine gefälschte Arbeitseinheit, die wir zum Testen verwenden können, könnte wie folgt aussehen.

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

Beachten Sie, dass die gefälschte Arbeitseinheit eine Commited-Eigenschaft verfügbar macht. Manchmal ist es nützlich, Einer gefälschten Klasse Features hinzuzufügen, die das Testen erleichtern. In diesem Fall ist es leicht zu beobachten, ob Code eine Arbeitseinheit durch Überprüfen der Commited-Eigenschaft festüberträgt.

Wir benötigen auch ein gefälschtes&lt;&gt; IObjectSet T, um Mitarbeiter- und TimeCard-Objekte im Speicher zu halten. Wir können eine einzelne Implementierung mit Generika bereitstellen.

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

Dieser Test delegiert den größten Teil&lt;&gt; seiner Arbeit an ein zugrunde liegendes HashSet T-Objekt. Beachten Sie,&lt;dass&gt; IObjectSet T eine generische Einschränkung erfordert, die T als Klasse&lt;(einen Verweistyp) erzwingt, und zwingt uns außerdem, IQueryable T&gt;zu implementieren. Es ist einfach, eine In-Memory-Sammlung als&lt;&gt; IQueryable T mit dem Standard-LINQ-Operator AsQueryable erscheinen zu lassen.

### <a name="the-tests"></a>Die Tests

Herkömmliche Komponententests verwenden eine einzelne Testklasse, um alle Tests für alle Aktionen in einem einzigen MVC-Controller abzuhalten. Wir können diese Tests oder jede Art von Komponententest mit den in Memory-Fakes schreiben, die wir erstellt haben. Für diesen Artikel vermeiden wir jedoch den monolithischen Testklassenansatz und gruppieren unsere Tests stattdessen, um sich auf eine bestimmte Funktionalität zu konzentrieren."Neuer Mitarbeiter erstellen" kann z. B. die Funktionalität sein, die wir testen möchten, daher verwenden wir eine einzelne Testklasse, um die Einzelcontrolleraktion zu überprüfen, die für die Erstellung eines neuen Mitarbeiters verantwortlich ist.

Es gibt einen allgemeinen Setup-Code, den wir für all diese feinkörnigen Testklassen benötigen. Zum Beispiel müssen wir immer unsere In-Memory-Repositorys und gefälschte Arbeitseinheit erstellen. Wir brauchen auch eine Instanz des Mitarbeiter-Controllers mit der gefälschten Arbeitseinheit injiziert. Wir teilen diesen allgemeinen Setupcode über Testklassen hinweg mithilfe einer Basisklasse.

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

Die "Objektmutter", die wir in der Basisklasse verwenden, ist ein gängiges Muster zum Erstellen von Testdaten. Eine Objektmutter enthält Factorymethoden zum Instanziieren von Testentitäten für die Verwendung über mehrere Prüfgeräte hinweg.

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

Wir können die EmployeeControllerTestBase als Basisklasse für eine Reihe von Prüfvorrichtungen verwenden (siehe Abbildung 3). Jede Prüfvorrichtung testet eine bestimmte Controlleraktion. Ein Testfixture konzentriert sich beispielsweise auf das Testen der Aktion Erstellen, die während einer HTTP GET-Anforderung verwendet wird (um die Ansicht zum Erstellen eines Mitarbeiters anzuzeigen), und eine andere Fixture konzentriert sich auf die Aktion Erstellen, die in einer HTTP-POST-Anforderung verwendet wird (um Informationen zu verwenden, die vom Benutzer zum Erstellen eines Mitarbeiters übermittelt wurden). Jede abgeleitete Klasse ist nur für die Einrichtung verantwortlich, die in ihrem spezifischen Kontext erforderlich ist, und stellt die Assertionen bereit, die erforderlich sind, um die Ergebnisse für ihren spezifischen Testkontext zu überprüfen.

![ef test_03](~/ef6/media/eftest-03.png)

**Abbildung 3**

Die hier vorgestellte Namenskonvention und der Teststil sind für testbaren Code nicht erforderlich – es ist nur ein Ansatz. Abbildung 4 zeigt die Tests, die im Jet Brains Resharper Testrunner-Plugin für Visual Studio 2010 ausgeführt werden.

![ef test_04](~/ef6/media/eftest-04.png)

**Abbildung 4**

Mit einer Basisklasse zum Behandeln des freigegebenen Setupcodes sind die Komponententests für jede Controlleraktion klein und einfach zu schreiben. Die Tests werden schnell ausgeführt (da wir In-Memory-Vorgänge ausführen) und sollten nicht aufgrund nicht verwandter Infrastruktur- oder Umgebungsprobleme fehlschlagen (weil wir die zu testende Einheit isoliert haben).

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

In diesen Tests übernimmt die Basisklasse den größten Teil der Einrichtungsarbeit. Denken Sie daran, dass der Basisklassenkonstruktor das In-Memory-Repository, eine gefälschte Arbeitseinheit und eine Instanz der EmployeeController-Klasse erstellt. Die Testklasse leitet sich von dieser Basisklasse ab und konzentriert sich auf die Besonderheiten des Testens der Create-Methode. In diesem Fall laufen die Einzelheiten auf die Schritte "Anordnen, Handeln und Behaupten" hinaus, die Sie in jedem Komponententestverfahren sehen:

-   Erstellen Sie ein neuesEmployee-Objekt, um eingehende Daten zu simulieren.
-   Rufen Sie die Aktion Erstellen des EmployeeControllers auf, und übergeben Sie den neuen Mitarbeiter.
-   Überprüfen Sie, ob die Aktion Erstellen die erwarteten Ergebnisse liefert (der Mitarbeiter wird im Repository angezeigt).

Was wir erstellt haben, ermöglicht es uns, jede der EmployeeController-Aktionen zu testen. Wenn wir beispielsweise Tests für die Indexaktion des Employee-Controllers schreiben, können wir von der Testbasisklasse erben, um die gleiche Basiseinrichtung für unsere Tests einzurichten. Auch hier erstellt die Basisklasse das In-Memory-Repository, die gefälschte Arbeitseinheit und eine Instanz des EmployeeControllers. Die Tests für die Indexaktion müssen sich nur auf das Aufrufen der Indexaktion und das Testen der Qualitäten des Modells konzentrieren, das die Aktion zurückgibt.

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

Die Tests, die wir mit In-Memory-Fakes erstellen, sind darauf ausgerichtet, den *Zustand* der Software zu testen. Wenn wir beispielsweise die Aktion Erstellen testen, möchten wir den Status des Repositorys überprüfen, nachdem die Erstellungsaktion ausgeführt wurde – hält das Repository den neuen Mitarbeiter?

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

Später werden wir uns mit interaktionsbasierten Tests befassen. Interaktionsbasierte Tests fragen, ob der zu testende Code die richtigen Methoden für unsere Objekte aufgerufen und die richtigen Parameter übergeben hat. Vorerst bewegen wir auf der Abdeckung ein weiteres Designmuster – die faule Last.

## <a name="eager-loading-and-lazy-loading"></a>Eager Loading und Lazy Loading

Irgendwann in der ASP.NET MVC-Webanwendung möchten wir möglicherweise die Informationen eines Mitarbeiters anzeigen und die zugehörigen Zeitkarten des Mitarbeiters einschließen. Beispielsweise können wir eine Zeitkartenzusammenfassungsanzeige haben, die den Namen des Mitarbeiters und die Gesamtzahl der Zeitkarten im System anzeigt. Es gibt mehrere Ansätze, die wir ergreifen können, um diese Funktion zu implementieren.

### <a name="projection"></a>Projection

Ein einfacher Ansatz zum Erstellen der Zusammenfassung besteht darin, ein Modell zu erstellen, das den Informationen gewidmet ist, die in der Ansicht angezeigt werden sollen. In diesem Szenario könnte das Modell wie folgt aussehen.

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

Beachten Sie, dass das EmployeeSummaryViewModel keine Entität ist, d. h. es ist nicht etwas, das wir in der Datenbank beibehalten möchten. Wir verwenden diese Klasse nur, um Daten stark typisiert in die Ansicht zu mischen. Das Ansichtsmodell ist wie ein Datenübertragungsobjekt (DTO), da es kein Verhalten (keine Methoden) enthält – nur Eigenschaften. Die Eigenschaften enthalten die Daten, die wir verschieben müssen. Es ist einfach, dieses Ansichtsmodell mit dem Standardprojektionsoperator von LINQ – dem Select-Operator – zu instanziieren.

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

Es gibt zwei bemerkenswerte Funktionen zum obigen Code. Erstens – der Code ist einfach zu testen, da er immer noch leicht zu beobachten und zu isolieren ist. Der Select-Operator arbeitet genauso gut gegen unsere In-Memory-Fakes wie gegen die reale Arbeitseinheit.

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

Die zweite bemerkenswerte Funktion ist, wie der Code EF4 ermöglicht, eine einzelne, effiziente Abfrage zu generieren, um Mitarbeiter- und Zeitkarteninformationen zusammenzustellen. Wir haben Mitarbeiterinformationen und Zeitkarteninformationen in dasselbe Objekt geladen, ohne spezielle APIs zu verwenden. Der Code hat lediglich die Informationen ausgedrückt, die er benötigt, indem Standard-LINQ-Operatoren verwendet werden, die mit In-Memory-Datenquellen sowie Remote-Datenquellen arbeiten. EF4 konnte die von der LINQ-Abfrage und\# dem C-Compiler generierten Ausdrucksstrukturen in eine einzige und effiziente T-SQL-Abfrage übersetzen.

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

Es gibt andere Zeiten, in denen wir nicht mit einem Ansichtsmodell oder DTO-Objekt arbeiten möchten, sondern mit realen Entitäten. Wenn wir wissen, dass wir einen Mitarbeiter *und* die Zeitkarten des Mitarbeiters benötigen, können wir die zugehörigen Daten unauffällig und effizient laden.

### <a name="explicit-eager-loading"></a>Explizites Eager-Laden

Wenn wir verwandte Entitätsinformationen eifrig laden möchten, benötigen wir einen Mechanismus für die Geschäftslogik (oder in diesem Szenario die Steuerungsaktionslogik), um seinen Wunsch in das Repository auszudrücken. Die EF4&lt;ObjectQuery T-Klasse&gt; definiert eine Include-Methode, um die zugehörigen Objekte anzugeben, die während einer Abfrage abgerufen werden sollen. Denken Sie daran, dass DER EF4&lt;ObjectContext Entitäten über die konkrete ObjectSet T-Klasse&gt; verfügbar macht, die von ObjectQuery&lt;T&gt;erbt.Wenn wir ObjectSet&lt;&gt; T-Referenzen in unserer Controlleraktion verwenden, könnten wir den folgenden Code schreiben, um eine eifrige Last von Zeitkarteninformationen für jeden Mitarbeiter anzugeben.

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

Da wir jedoch versuchen, unseren Code testbar zu halten, setzen wir ObjectSet&lt;T&gt; nicht von außerhalb der tatsächlichen Einheit der Arbeitsklasse aus. Stattdessen verlassen wir uns auf&lt;&gt; die IObjectSet T-Schnittstelle,&lt;die&gt; einfacher zu fälschen ist, aber IObjectSet T definiert keine Include-Methode. Das Schöne an LINQ ist, dass wir unseren eigenen Include-Operator erstellen können.

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

Beachten Sie, dass dieser Include-Operator als&lt;&gt; Erweiterungsmethode für&lt;&gt;IQueryable T anstelle von IObjectSet T definiert ist. Dies gibt uns die Möglichkeit, die Methode mit einem breiteren&gt;Spektrum möglicher&lt;&gt;Typen zu&lt;&gt;verwenden, einschließlich&lt;&gt;IQueryable&lt;T , IObjectSet T , ObjectQuery T und ObjectSet T . Für den Fall, dass die zugrunde liegende&lt;&gt;Sequenz keine echte EF4 ObjectQuery T ist, wird kein Schaden angerichtet, und der Include-Operator ist ein No-Op. Wenn es sich bei&lt;der&gt; zugrunde liegenden *Sequenz* um eine ObjectQuery T handelt (oder von ObjectQuery&lt;T&gt;abgeleitet wurde), wird EF4 unsere Anforderung für zusätzliche Daten sehen und die richtige SQL-Abfrage formulieren.

Mit diesem neuen Betreiber können wir explizit eine eifrige Ladung von Zeitkarteninformationen aus dem Repository anfordern.

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Bei der Ausführung für einen echten ObjectContext erzeugt der Code die folgende einzelne Abfrage. Die Abfrage sammelt in einer Fahrt genügend Informationen aus der Datenbank, um die Mitarbeiterobjekte zu materialisieren und ihre TimeCards-Eigenschaft vollständig aufzufüllen.

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

Die gute Nachricht ist, dass der Code innerhalb der Aktionsmethode vollständig testbar bleibt. Wir müssen keine zusätzlichen Funktionen für unsere Fälschungen bereitstellen, um den Include-Operator zu unterstützen. Die schlechte Nachricht ist, dass wir den Include-Operator innerhalb des Codes verwenden mussten, den wir persistenz unwissend halten wollten. Dies ist ein Paradebeispiel für die Art von Kompromissen, die Sie beim Erstellen von testbarem Code auswerten müssen. Es gibt Zeiten, in denen Sie Persistenzbedenken außerhalb der Repository-Abstraktion auslaufen lassen müssen, um die Leistungsziele zu erreichen.

Die Alternative zum eifrigen Laden ist das faule Laden. Lazy Loading bedeutet, dass wir unseren Geschäftscode *nicht* benötigen, um die Anforderung für zugehörige Daten explizit anzukündigen. Stattdessen verwenden wir unsere Entitäten in der Anwendung und wenn zusätzliche Daten benötigt werden, lädt Entity Framework die Daten bei Bedarf.

### <a name="lazy-loading"></a>Lazy Loading

Es ist leicht, sich ein Szenario vorzustellen, in dem wir nicht wissen, welche Daten eine Geschäftslogik benötigt. Wir wissen vielleicht, dass die Logik ein Mitarbeiterobjekt benötigt, aber wir können in verschiedene Ausführungspfade verzweigen, wo einige dieser Pfade Zeitkarteninformationen vom Mitarbeiter erfordern, und einige nicht. Szenarien wie diese sind perfekt für implizites lazy loading, weil Daten auf magischeWeise auf einer bedarfsgemäßen Basis angezeigt werden.

Das verzögerte Laden, auch als verzögertes Laden bezeichnet, stellt einige Anforderungen an unsere Entitätsobjekte. POCOs mit wahrer Persistenz-Ignoranz würden keine Anforderungen der Persistenzschicht erfüllen, aber wahre Persistenz-Ignoranz ist praktisch unmöglich zu erreichen.Stattdessen messen wir Persistenz-Ignoranz in relativen Graden. Es wäre bedauerlich, wenn wir von einer Persistenz-orientierten Basisklasse erben oder eine spezialisierte Sammlung verwenden müssten, um ein verzögertes Laden in POCOs zu erreichen. Glücklicherweise hat EF4 eine weniger aufdringliche Lösung.

### <a name="virtually-undetectable"></a>Praktisch nicht nachweisbar

Bei Verwendung von POCO-Objekten kann EF4 Laufzeitproxys für Entitäten dynamisch generieren. Diese Proxys umschließen die materialisierten POCOs unsichtbar und bieten zusätzliche Dienste, indem sie jeden Sachabnehmer- und -satzvorgang abfangen, um zusätzliche Arbeiten auszuführen. Ein solcher Service ist die faule Ladefunktion, die wir suchen. Ein weiterer Dienst ist ein effizienter Änderungsverfolgungsmechanismus, der aufzeichnen kann, wenn die Anwendung die Eigenschaftswerte einer Entität ändert. Die Liste der Änderungen wird vom ObjectContext während der SaveChanges-Methode verwendet, um alle geänderten Entitäten mithilfe von UPDATE-Befehlen beizubehalten.

Damit diese Proxys funktionieren, benötigen sie jedoch eine Möglichkeit, sich an Eigenschaften zu binden, die Vorgänge für eine Entität abrufen und festlegen, und die Proxys erreichen dieses Ziel, indem sie virtuelle Member überschreiben. Wenn wir also implizites verzögertes Laden und effizientes Änderungstracking haben wollen, müssen wir zu unseren POCO-Klassendefinitionen zurückkehren und Eigenschaften als virtuell markieren.

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

Wir können immer noch sagen, dass die Mitarbeiter-Entität meist Ausdauer-Ignoranz ist. Die einzige Anforderung ist die Verwendung virtueller Member, und dies hat keinen Einfluss auf die Testbarkeit des Codes. Wir müssen keine spezielle Basisklasse ableiten oder sogar eine spezielle Kollektion verwenden, die dem verzögerten Laden gewidmet ist. Wie der Code zeigt, ist&lt;&gt; jede Klasse, die ICollection T implementiert, verfügbar, um verwandte Entitäten zu halten.

Es gibt auch eine kleine Änderung, die wir innerhalb unserer Arbeitseinheit vornehmen müssen. Lazy Loading ist standardmäßig *deaktiviert,* wenn Sie direkt mit einem ObjectContext-Objekt arbeiten. Es gibt eine Eigenschaft, die wir für die ContextOptions-Eigenschaft festlegen können, um das verzögerte Laden zu ermöglichen, und wir können diese Eigenschaft innerhalb unserer tatsächlichen Arbeitseinheit festlegen, wenn wir das verzögerte Laden überall aktivieren möchten.

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

Wenn implizites verzögertes Laden aktiviert ist, kann Anwendungscode einen Mitarbeiter und die zugehörigen Zeitkarten des Mitarbeiters verwenden, während er sich der Arbeit, die EF zum Laden der zusätzlichen Daten benötigt, glücklicherweise nicht bewusst ist.

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

Lazy Loading erleichtert das Schreiben des Anwendungscodes, und mit der Proxymagie bleibt der Code vollständig testbar. In-Memory-Fakes der Arbeitseinheit können einfach gefälschte Entitäten mit zugehörigen Daten vorladen, wenn sie während eines Tests benötigt werden.

An diesem Punkt wenden wir unsere Aufmerksamkeit von der&lt;Erstellung&gt; von Repositorys mit IObjectSet T und betrachten Abstraktionen, um alle Zeichen des Persistenzframeworks auszublenden.

## <a name="custom-repositories"></a>Benutzerdefinierte Repositorys

Als wir zum ersten Mal die Arbeitseinheit Design-Muster in diesem Artikel vorgestellt haben, haben wir einige Beispielcode für, wie die Arbeitseinheit aussehen könnte. Lassen Sie uns diese ursprüngliche Idee mithilfe des Zeitkartenszenarios für Mitarbeiter und Mitarbeiter, mit dem wir gearbeitet haben, erneut präsentieren.

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

Der Hauptunterschied zwischen dieser Arbeitseinheit und der Arbeitseinheit, die wir im letzten Abschnitt erstellt haben, besteht darin, dass diese&lt;&gt;Arbeitseinheit keine Abstraktionen aus dem EF4-Framework verwendet (es gibt kein IObjectSet T ). IObjectSet&lt;&gt; T funktioniert gut als Repository-Schnittstelle, aber die API, die es verfügbar macht, entspricht möglicherweise nicht perfekt den Anforderungen unserer Anwendung. In diesem kommenden Ansatz werden wir Repositories&lt;&gt; mit einer benutzerdefinierten IRepository T-Abstraktion darstellen.

Viele Entwickler, die testgesteuerten Entwurf, verhaltensgesteuertes Design und domänengesteuertes&lt;&gt; Methodendesign befolgen, bevorzugen den IRepository T-Ansatz aus mehreren Gründen. Erstens stellt die&lt;&gt; IRepository T-Schnittstelle eine "Anti-Korruptions"-Schicht dar. Wie Eric Evans in seinem Domain Driven Design-Buch beschrieben, hält eine Anti-Korruptions-Schicht Ihren Domänencode von Infrastruktur-APIs fern, wie eine Persistenz-API. Zweitens können Entwickler Methoden in das Repository erstellen, die die genauen Anforderungen einer Anwendung erfüllen (wie beim Schreiben von Tests entdeckt). Beispielsweise müssen wir häufig eine einzelne Entität mithilfe eines ID-Werts suchen, damit wir der Repository-Schnittstelle eine FindById-Methode hinzufügen können.Unsere&lt;IRepository&gt; T-Definition sieht wie folgt aus.

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

Beachten Sie, dass wir die Verwendung&lt;&gt; einer IQueryable T-Schnittstelle zum Verfügbarmachen von Entitätsauflistungen zurücksenden. IQueryable&lt;&gt; T ermöglicht es LINQ-Ausdrucksstrukturen, in den EF4-Anbieter zu fließen und dem Anbieter eine ganzheitliche Ansicht der Abfrage zu geben. Eine zweite Option wäre die Rückgabe&lt;von&gt;IEnumerable T , was bedeutet, dass der EF4-LINQ-Anbieter nur die ausdrücke sieht, die im Repository erstellt wurden. Jede Gruppierung, Reihenfolge und Projektion außerhalb des Repositorys wird nicht in den SQL-Befehl integriert, der an die Datenbank gesendet wird, was die Leistung beeinträchtigen kann. Auf der anderen Seite wird ein Repository,&lt;&gt; das nur IEnumerable T-Ergebnisse zurückgibt, Sie nie mit einem neuen SQL-Befehl überraschen. Beide Ansätze werden funktionieren, und beide Ansätze bleiben testbar.

Es ist einfach, eine einzelne Implementierung&lt;der&gt; IRepository T-Schnittstelle mithilfe von Generika und der EF4 ObjectContext-API bereitzustellen.

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

Der&lt;IRepository&gt; T-Ansatz gibt uns zusätzliche Kontrolle über unsere Abfragen, da ein Client eine Methode aufrufen muss, um zu einer Entität zu gelangen. Innerhalb der Methode könnten wir zusätzliche Prüfungen und LINQ-Operatoren bereitstellen, um Anwendungseinschränkungen zu erzwingen. Beachten Sie, dass die Schnittstelle zwei Einschränkungen für den generischen Typparameter enthält. Die erste Einschränkung ist die Von ObjectSet&lt;T&gt;erforderliche Klasse cons taint, und die zweite Einschränkung zwingt unsere Entitäten, IEntity zu implementieren – eine Abstraktion, die für die Anwendung erstellt wurde. Die IEntity-Schnittstelle erzwingt Entitäten, über eine lesbare Id-Eigenschaft zu verfügen, und wir können diese Eigenschaft dann in der FindById-Methode verwenden. IEntity wird mit dem folgenden Code definiert.

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

IEntity könnte als eine kleine Verletzung der Persistenz-Ignoranz betrachtet werden, da unsere Entitäten diese Schnittstelle implementieren müssen. Denken Sie daran, Persistenz-Ignoranz ist über Kompromisse, und für viele die FindById-Funktionalität wird die Einschränkung durch die Schnittstelle auferlegt überwiegen. Die Schnittstelle hat keine Auswirkungen auf die Testbarkeit.

Für das Sofortige eines&lt;Live-IRepository T&gt; ist ein EF4 ObjectContext erforderlich, daher sollte eine konkrete Arbeitseinheitsimplementierung die Instanziierung verwalten.

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

### <a name="using-the-custom-repository"></a>Verwenden des benutzerdefinierten Repositorys

Die Verwendung unseres benutzerdefinierten Repositorys unterscheidet sich nicht&lt;&gt;wesentlich von der Verwendung des Repositorys basierend auf IObjectSet T . Anstatt LINQ-Operatoren direkt auf eine Eigenschaft anzuwenden, müssen wir zunächst eine der Methoden&lt;&gt; des Repositorys aufrufen, um einen IQueryable T-Verweis zu abrufen.

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

Beachten Sie, dass der benutzerdefinierte Include-Operator, den wir zuvor implementiert haben, ohne Änderungen funktioniert. Die FindById-Methode des Repositorys entfernt duplizierte Logik aus Aktionen, die versuchen, eine einzelne Entität abzurufen.

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

Es gibt keinen signifikanten Unterschied in der Testbarkeit der beiden Ansätze, die wir untersucht haben. Wir könnten gefälschte Implementierungen&lt;von&gt; IRepository T bereitstellen,&lt;&gt; indem wir Betonklassen erstellen, die von HashSet Employee unterstützt werden - genau wie im letzten Abschnitt. Einige Entwickler bevorzugen es jedoch, Mock-Objekte und Mock-Objekt-Frameworks zu verwenden, anstatt Fälschungen zu erstellen. Wir werden uns mit Mocks befassen, um unsere Implementierung zu testen und die Unterschiede zwischen Mocks und Fakes im nächsten Abschnitt zu diskutieren.

### <a name="testing-with-mocks"></a>Testen mit Mocks

Es gibt verschiedene Ansätze, um das aufzubauen, was Martin Fowler ein "Testdouble" nennt. Ein Test-Double (wie ein Film-Stunt-Double) ist ein Objekt, das Sie erstellen, um während der Tests für reale Produktionsobjekte "zu stehen". Die von uns erstellten In-Memory-Repositorys sind Testdoubles für die Repositorys, die mit SQL Server sprechen. Wir haben gesehen, wie diese Test-Doubles während der Komponententests verwendet werden, um Code zu isolieren und Tests schnell laufen zu lassen.

Die Test-Doubles, die wir erstellt haben, verfügen über echte, funktionierende Implementierungen. Hinter den Kulissen speichert jeder eine konkrete Sammlung von Objekten, und sie fügen Objekte aus dieser Auflistung hinzu und entfernen sie aus dieser Auflistung, während wir das Repository während eines Tests bearbeiten. Einige Entwickler bauen ihre Test-Doubles gerne auf diese Weise – mit echtem Code und funktionierenden Implementierungen.Diese Test-Doppel sind, was wir *Fakes*nennen. Sie haben funktionierende Implementierungen, aber sie sind nicht real genug für den Produktionseinsatz. Das gefälschte Repository schreibt nicht in die Datenbank. Der gefälschte SMTP-Server sendet keine E-Mail-Nachricht über das Netzwerk.

### <a name="mocks-versus-fakes"></a>Mocks versus Fakes

Es gibt eine andere Art von Test-Double, das als *Mock*bezeichnet wird. Während Fakes funktionierende Implementierungen haben, kommen Mocks ohne Implementierung. Mit Hilfe eines Mock-Objekt-Frameworks konstruieren wir diese Mock-Objekte zur Laufzeit und verwenden sie als Testdoppel. In diesem Abschnitt verwenden wir das Open-Source-Mocking-Framework Moq. Hier ist ein einfaches Beispiel für die Verwendung von Moq zum dynamischen Erstellen eines Testdoubles für ein Mitarbeiter-Repository.

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

Wir bitten Moq um&lt;&gt; eine IRepository-Mitarbeiterimplementierung, die dynamisch erstellt wird. Wir können auf das Objekt&lt;&gt; zugreifen, das IRepository Employee&lt;&gt; implementiert, indem wir auf die Object-Eigenschaft des Mock T-Objekts zugreifen. Es ist dieses innere Objekt, das wir an unsere Controller übergeben können, und sie werden nicht wissen, ob dies ein Test-Double oder das eigentliche Repository ist. Wir können Methoden für das Objekt aufrufen, genau wie wir Methoden für ein Objekt mit einer realen Implementierung aufrufen würden.

Sie müssen sich fragen, was das Mock-Repository tun wird, wenn wir die Add-Methode aufrufen. Da sich hinter dem Mock-Objekt keine Implementierung befindet, unternimmt Add nichts. Es gibt keine konkrete Sammlung hinter den Kulissen, wie wir es mit den Fälschungen hatten, die wir geschrieben haben, also wird der Mitarbeiter verworfen. Was ist mit dem Rückgabewert von FindById? In diesem Fall tut das Mock-Objekt das einzige, was es tun kann, d. h. einen Standardwert zurückzugeben. Da wir einen Verweistyp (einen Mitarbeiter) zurückgeben, ist der Rückgabewert ein NULL-Wert.

Mocks mag wertlos klingen; es gibt jedoch zwei weitere Features von Mocks, über die wir nicht gesprochen haben. Zunächst zeichnet das Moq-Framework alle Aufrufe auf dem Mock-Objekt auf. Später im Code können wir Moq fragen, ob jemand die Add-Methode aufgerufen hat oder ob jemand die FindById-Methode aufgerufen hat. Wir werden später sehen, wie wir diese "Black Box"-Aufnahmefunktion in Tests verwenden können.

Die zweite großartige Funktion ist, wie wir Moq verwenden können, um ein Mock-Objekt mit Erwartungen zu *programmieren.* Eine Erwartung sagt dem Mock-Objekt, wie auf eine bestimmte Interaktion zu reagieren. Beispielsweise können wir eine Erwartung in unseren Mock programmieren und ihr sagen, dass ein Mitarbeiterobjekt zurückgegeben werden soll, wenn jemand FindById aufruft. Das Moq-Framework verwendet eine Setup-API und Lambda-Ausdrücke, um diese Erwartungen zu programmieren.

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

In diesem Beispiel bitten wir Moq, dynamisch ein Repository zu erstellen, und dann programmieren wir das Repository mit einer Erwartung. Die Erwartung weist das Mock-Objekt an, ein neues Mitarbeiterobjekt mit dem ID-Wert 5 zurückzugeben, wenn jemand die FindById-Methode aufruft, die den Wert 5 übergibt. Dieser Test besteht, und wir mussten keine vollständige Implementierung&lt;für&gt;gefälschte IRepository T erstellen.

Lassen Sie uns die Tests, die wir zuvor geschrieben haben, noch einmal überprüfen und sie überarbeiten, um Mocks anstelle von Fälschungen zu verwenden. Wie zuvor verwenden wir eine Basisklasse, um die gemeinsamen Komponenten der Infrastruktur einzurichten, die wir für alle Tests des Controllers benötigen.

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

Der Setup-Code bleibt größtenteils derselbe. Anstatt Fälschungen zu verwenden, verwenden wir Moq, um Mock-Objekte zu konstruieren. Die Basisklasse ordnet an, dass die Mock-Arbeitseinheit ein Mock-Repository zurückgibt, wenn Code die Employees-Eigenschaft aufruft. Der Rest des Mock-Setups findet innerhalb der Testvorrichtungen statt, die jedem spezifischen Szenario gewidmet sind. Beispielsweise wird das Mock-Repository vom Testpunkt für die Index-Aktion so eingerichtet, dass eine Liste der Mitarbeiter zurückgegeben wird, wenn die Aktion die FindAll-Methode des Mock-Repositorys aufruft.

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

Abgesehen von den Erwartungen sehen unsere Tests ähnlich aus wie die Tests, die wir vorher hatten. Mit der Aufnahmefähigkeit eines Mock-Frameworks können wir uns jedoch dem Testen aus einem anderen Blickwinkel nähern. Wir werden uns diese neue Perspektive im nächsten Abschnitt ansehen.

### <a name="state-versus-interaction-testing"></a>Status-gegen-Interaktionstests

Es gibt verschiedene Techniken, die Sie verwenden können, um Software mit Mock-Objekten zu testen. Ein Ansatz besteht darin, zustandsbasierte Tests zu verwenden, was wir bisher in diesem Papier getan haben. Staatliche Tests machen Behauptungen über den Zustand der Software. Im letzten Test haben wir eine Aktionsmethode auf dem Controller aufgerufen und eine Behauptung über das Modell gemacht, das er erstellen soll. Hier sind einige weitere Beispiele für den Teststatus:

-   Überprüfen Sie, ob das Repository das neue Mitarbeiterobjekt enthält, nachdem Create ausgeführt wurde.
-   Überprüfen Sie, ob das Modell eine Liste aller Mitarbeiter enthält, nachdem Index ausgeführt wurde.
-   Stellen Sie sicher, dass das Repository nach der Ausführung von Delete keinen bestimmten Mitarbeiter enthält.

Ein weiterer Ansatz, den Sie bei Mock-Objekten sehen, ist die Überprüfung *von Interaktionen*. Während zustandsbasierte Tests Behauptungen über den Zustand von Objekten machen, macht interaktionsbasiertetests Behauptungen darüber, wie Objekte interagieren. Beispiel:

-   Überprüfen Sie, ob der Controller die Add-Methode des Repositorys aufruft, wenn Create ausgeführt wird.
-   Überprüfen Sie, ob der Controller die FindAll-Methode des Repositorys aufruft, wenn Index ausgeführt wird.
-   Überprüfen Sie, ob der Controller die Commit-Methode der Arbeitseinheit aufruft, um Änderungen zu speichern, wenn Bearbeiten ausgeführt wird.

Interaktionstests erfordern oft weniger Testdaten, da wir nicht in Sammlungen einstechen und die Anzahl überprüfen. Wenn wir beispielsweise wissen, dass die Aktion Details die FindById-Methode eines Repositorys mit dem richtigen Wert aufruft, verhält sich die Aktion wahrscheinlich korrekt. Wir können dieses Verhalten überprüfen, ohne Testdaten für die Rückgabe von FindById einzurichten.

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

Das einzige Setup, das in der obigen Prüfvorrichtung erforderlich ist, ist das Setup, das von der Basisklasse bereitgestellt wird. Wenn wir die Controller-Aktion aufrufen, zeichnet Moq die Interaktionen mit dem Mock-Repository auf. Mithilfe der Verify-API von Moq können wir Moq fragen, ob der Controller FindById mit dem richtigen ID-Wert aufgerufen hat. Wenn der Controller die Methode nicht aufgerufen oder die Methode mit einem unerwarteten Parameterwert aufgerufen hat, löst die Verify-Methode eine Ausnahme aus, und der Test schlägt fehl.

Hier ist ein weiteres Beispiel zum Überprüfen, ob die Aktion Erstellen Commit für die aktuelle Arbeitseinheit aufruft.

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

Eine Gefahr bei Interaktionstests ist die Tendenz, Wechselwirkungen zu überspezifizieren. Die Fähigkeit des Mockobjekts, jede Interaktion mit dem Mockobjekt aufzuzeichnen und zu überprüfen, bedeutet nicht, dass der Test versuchen sollte, jede Interaktion zu überprüfen. Einige Interaktionen sind Implementierungsdetails, und Sie sollten nur die Interaktionen überprüfen, die *erforderlich* sind, um den aktuellen Test zu erfüllen.

Die Wahl zwischen Mocks oder Fakes hängt weitgehend von dem System ab, das Sie testen, und Von Ihren persönlichen (oder Team-)Präferenzen. Mock-Objekte können die Menge an Code, die Sie zum Implementieren von Testdoubles benötigen, drastisch reduzieren, aber nicht jeder ist mit komfortablen Programmiererwartungen und der Überprüfung von Interaktionen zufrieden.

## <a name="conclusions"></a>Zusammenfassung

In diesem Artikel haben wir mehrere Ansätze zum Erstellen von testbarem Code bei der Verwendung des ADO.NET Entity Framework für die Datenpersistenz demonstriert. Wir können integrierte Abstraktionen wie&lt;IObjectSet&gt;T nutzen oder unsere&lt;&gt;eigenen Abstraktionen wie IRepository T erstellen.In beiden Fällen ermöglicht die POCO-Unterstützung im ADO.NET Entity Framework 4.0 den Verbrauchern dieser Abstraktionen, beharrlich ignorant und hochgradig testbar zu bleiben. Zusätzliche EF4-Funktionen wie das implizite lazy loading ermöglichen es Geschäfts- und Anwendungsdienstcode, ohne sich um die Details eines relationalen Datenspeichers kümmern zu müssen. Schließlich sind die Abstraktionen, die wir erstellen, einfach zu verspotten oder zu fälschen innerhalb von Komponententests, und wir können diese Test-Doubles verwenden, um schnell laufende, stark isolierte und zuverlässige Tests zu erreichen.

### <a name="additional-resources"></a>Weitere Ressourcen

-   Robert C. Martin, " [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)"
-   Martin Fowler, [Katalog der Muster](https://www.martinfowler.com/eaaCatalog/index.html) aus *Mustern der Unternehmensanwendungsarchitektur*
-   Griffin Caprio, " [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)"
-   Data Programmability Blog, " [Walkthrough: Test Driven Development with the Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)".
-   Data Programmability Blog, " [Using Repository and Unit of Work patterns with Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"
-   Aaron Jensen, " [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"
-   Eric Lee, [BDD mit MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)
-   Eric Evans, " [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"
-   Martin Fowler, " [Mocks Aren't Stubs](https://martinfowler.com/articles/mocksArentStubs.html)"
-   Martin Fowler, Test [Double](https://martinfowler.com/bliki/TestDouble.html)
-   [Moq](https://code.google.com/p/moq/)

### <a name="biography"></a>Biographie

Scott Allen ist Mitglied des technischen Personals bei Pluralsight und Gründer von OdeToCode.com. In 15 Jahren kommerzieller Softwareentwicklung hat Scott an Lösungen für alles gearbeitet, von 8-Bit-Embedded-Geräten bis hin zu hochskalierbaren ASP.NET Webanwendungen. Sie erreichen Scott über seinen Blog bei OdeToCode oder auf Twitter unter [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).
