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
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="2bf71-102">Testbarkeit und Entity Framework 4.0</span><span class="sxs-lookup"><span data-stu-id="2bf71-102">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="2bf71-103">Scott Allen</span><span class="sxs-lookup"><span data-stu-id="2bf71-103">Scott Allen</span></span>

<span data-ttu-id="2bf71-104">Veröffentlicht: Mai 2010</span><span class="sxs-lookup"><span data-stu-id="2bf71-104">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="2bf71-105">Einführung</span><span class="sxs-lookup"><span data-stu-id="2bf71-105">Introduction</span></span>

<span data-ttu-id="2bf71-106">In diesem Whitepaper wird beschrieben und veranschaulicht, wie sie testbaren Code mit den ADO.NET Entity Framework 4.0 und Visual Studio 2010 schreiben.</span><span class="sxs-lookup"><span data-stu-id="2bf71-106">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="2bf71-107">In diesem Dokument wird nicht versucht, sich auf eine bestimmte Testmethodik zu konzentrieren, z. B. TDD (Test-Driven Design) oder behavior-driven design (BDD).</span><span class="sxs-lookup"><span data-stu-id="2bf71-107">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="2bf71-108">Stattdessen konzentriert sich dieses Dokument auf das Schreiben von Code, der die ADO.NET Entity Framework verwendet, aber weiterhin einfach zu isolieren und automatisiert zu testen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-108">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="2bf71-109">Wir untersuchen gängige Entwurfsmuster, die das Testen in Datenzugriffsszenarien erleichtern, und erfahren, wie diese Muster bei der Verwendung des Frameworks angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-109">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="2bf71-110">Wir werden auch bestimmte Funktionen des Frameworks betrachten, um zu sehen, wie diese Features in testbarem Code funktionieren können.</span><span class="sxs-lookup"><span data-stu-id="2bf71-110">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="2bf71-111">Was ist testbarer Code?</span><span class="sxs-lookup"><span data-stu-id="2bf71-111">What Is Testable Code?</span></span>

<span data-ttu-id="2bf71-112">Die Möglichkeit, eine Software mithilfe automatisierter Komponententests zu überprüfen, bietet viele wünschenswerte Vorteile.</span><span class="sxs-lookup"><span data-stu-id="2bf71-112">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="2bf71-113">Jeder weiß, dass gute Tests die Anzahl der Softwarefehler in einer Anwendung reduzieren und die Qualität der Anwendung erhöhen werden - aber die Anbelegung von Komponententests geht weit über das bloße Auffinden von Fehlern hinaus.</span><span class="sxs-lookup"><span data-stu-id="2bf71-113">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="2bf71-114">Eine gute Komponententestsammlung ermöglicht es einem Entwicklungsteam, Zeit zu sparen und die Kontrolle über die von ihnen erstellte Software zu behalten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-114">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="2bf71-115">Ein Team kann Änderungen am vorhandenen Code vornehmen, Software umgestalten, umgestalten und umstrukturieren, um neuen Anforderungen gerecht zu werden, und neue Komponenten zu einer Anwendung hinzufügen, während sie weiß, dass die Testsammlung das Verhalten der Anwendung überprüfen kann.</span><span class="sxs-lookup"><span data-stu-id="2bf71-115">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="2bf71-116">Komponententests sind Teil eines schnellen Feedback-Zyklus, um Änderungen zu erleichtern und die Wartbarkeit von Software bei zunehmender Komplexität zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-116">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="2bf71-117">Komponententests haben jedoch ihren Preis.</span><span class="sxs-lookup"><span data-stu-id="2bf71-117">Unit testing comes with a price, however.</span></span> <span data-ttu-id="2bf71-118">Ein Team muss die Zeit investieren, um Komponententests zu erstellen und zu warten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-118">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="2bf71-119">Der Aufwand, der zum Erstellen dieser Tests erforderlich ist, hängt direkt mit der **Testbarkeit** der zugrunde liegenden Software zusammen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-119">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="2bf71-120">Wie einfach ist die Software zu testen?</span><span class="sxs-lookup"><span data-stu-id="2bf71-120">How easy is the software to test?</span></span> <span data-ttu-id="2bf71-121">Ein Team, das Software mit Blick auf die Testbarkeit entwickelt, erstellt schnellere effektive Tests als das Team, das mit nicht testbarer Software arbeitet.</span><span class="sxs-lookup"><span data-stu-id="2bf71-121">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="2bf71-122">Microsoft hat das ADO.NET Entity Framework 4.0 (EF4) unter Berücksichtigung der Testbarkeit entwickelt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-122">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="2bf71-123">Dies bedeutet nicht, dass Entwickler Komponententests für Frameworkcode selbst schreiben.</span><span class="sxs-lookup"><span data-stu-id="2bf71-123">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="2bf71-124">Stattdessen erleichtern die Testbarkeitsziele für EF4 das Erstellen von testbarem Code, der auf dem Framework aufbaut.</span><span class="sxs-lookup"><span data-stu-id="2bf71-124">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="2bf71-125">Bevor wir uns bestimmte Beispiele ansehen, lohnt es sich, die Qualitäten des testbaren Codes zu verstehen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-125">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="2bf71-126">Die Qualitäten des testbaren Codes</span><span class="sxs-lookup"><span data-stu-id="2bf71-126">The Qualities of Testable Code</span></span>

<span data-ttu-id="2bf71-127">Code, der leicht zu testen ist, weist immer mindestens zwei Merkmale auf.</span><span class="sxs-lookup"><span data-stu-id="2bf71-127">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="2bf71-128">Erstens ist testbarer Code leicht zu **beobachten.**</span><span class="sxs-lookup"><span data-stu-id="2bf71-128">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="2bf71-129">Angesichts einiger Eingaben sollte es einfach sein, die Ausgabe des Codes zu beobachten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-129">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="2bf71-130">Beispielsweise ist das Testen der folgenden Methode einfach, da die Methode direkt das Ergebnis einer Berechnung zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-130">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="2bf71-131">Das Testen einer Methode ist schwierig, wenn die Methode den berechneten Wert in einen Netzwerksocket, eine Datenbanktabelle oder eine Datei wie den folgenden Code schreibt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-131">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="2bf71-132">Der Test muss zusätzliche Arbeit ausführen, um den Wert abzurufen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-132">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="2bf71-133">Zweitens ist testbarer Code einfach zu **isolieren.**</span><span class="sxs-lookup"><span data-stu-id="2bf71-133">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="2bf71-134">Verwenden wir den folgenden Pseudocode als schlechtes Beispiel für testbaren Code.</span><span class="sxs-lookup"><span data-stu-id="2bf71-134">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

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

<span data-ttu-id="2bf71-135">Die Methode ist leicht zu beobachten – wir können eine Versicherungspolice abschließen und überprüfen, ob der Rückgabewert mit dem erwarteten Ergebnis übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-135">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="2bf71-136">Um die Methode zu testen, müssen wir jedoch eine Datenbank mit dem richtigen Schema installiert haben, und den SMTP-Server konfigurieren, falls die Methode versucht, eine E-Mail zu senden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-136">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="2bf71-137">Der Komponententest möchte nur die Berechnungslogik innerhalb der Methode überprüfen, aber der Test schlägt möglicherweise fehl, weil der E-Mail-Server offline ist oder weil der Datenbankserver verschoben wurde.</span><span class="sxs-lookup"><span data-stu-id="2bf71-137">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="2bf71-138">Beide Fehler haben nichts mit dem Verhalten zu tun, das der Test überprüfen möchte.</span><span class="sxs-lookup"><span data-stu-id="2bf71-138">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="2bf71-139">Das Verhalten ist schwer zu isolieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-139">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="2bf71-140">Softwareentwickler, die testbaren Code schreiben möchten, streben häufig danach, eine Trennung von Bedenken in dem von ihnen geschriebenen Code beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-140">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="2bf71-141">Die obige Methode sollte sich auf die Geschäftsberechnungen konzentrieren und die Datenbank- und E-Mail-Implementierungsdetails an andere Komponenten delegieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-141">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="2bf71-142">Robert C. Martin nennt dies das Prinzip der einheitlichen Verantwortung.</span><span class="sxs-lookup"><span data-stu-id="2bf71-142">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="2bf71-143">Ein Objekt sollte eine einzelne, enge Verantwortung einkapseln, z. B. den Wert einer Richtlinie berechnen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-143">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="2bf71-144">Für alle anderen Datenbank- und Benachrichtigungsarbeiten sollte ein anderes Objekt verantwortlich sein.</span><span class="sxs-lookup"><span data-stu-id="2bf71-144">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="2bf71-145">Auf diese Weise geschriebener Code ist einfacher zu isolieren, da er sich auf eine einzelne Aufgabe konzentriert.</span><span class="sxs-lookup"><span data-stu-id="2bf71-145">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="2bf71-146">In .NET haben wir die Abstraktionen, die wir brauchen, um dem Prinzip der einheitlichen Verantwortung zu folgen und Isolation zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-146">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="2bf71-147">Wir können Schnittstellendefinitionen verwenden und den Code zwingen, die Schnittstellenabstraktion anstelle eines konkreten Typs zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-147">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="2bf71-148">Später in diesem Artikel werden wir sehen, wie eine Methode wie das schlechte Beispiel oben dargestellt mit Schnittstellen arbeiten kann, die *aussehen,* als würden sie mit der Datenbank sprechen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-148">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="2bf71-149">Zur Testzeit können wir jedoch eine Dummy-Implementierung ersetzen, die nicht mit der Datenbank spricht, sondern Daten im Speicher enthält.</span><span class="sxs-lookup"><span data-stu-id="2bf71-149">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="2bf71-150">Diese Dummy-Implementierung isoliert den Code von nicht verwandten Problemen im Datenzugriffscode oder in der Datenbankkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="2bf71-150">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="2bf71-151">Die Isolierung hat zusätzliche Vorteile.</span><span class="sxs-lookup"><span data-stu-id="2bf71-151">There are additional benefits to isolation.</span></span> <span data-ttu-id="2bf71-152">Die Ausführung der Geschäftsberechnung in der letzten Methode sollte nur wenige Millisekunden in Anspruch nehmen, aber der Test selbst kann mehrere Sekunden lang ausgeführt werden, wenn der Code im Netzwerk gehüpft ist und mit verschiedenen Servern spricht.</span><span class="sxs-lookup"><span data-stu-id="2bf71-152">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="2bf71-153">Komponententests sollten schnell durchgeführt werden, um kleine Änderungen zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="2bf71-153">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="2bf71-154">Komponententests sollten ebenfalls wiederholbar sein und nicht fehlschlagen, da eine Komponente, die nicht mit dem Test zusammenhängt, ein Problem hat.</span><span class="sxs-lookup"><span data-stu-id="2bf71-154">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="2bf71-155">Das Schreiben von Code, der leicht zu beobachten und zu isolieren ist, bedeutet, dass Entwickler weniger Zeit beim Schreiben von Tests für den Code haben, weniger Zeit damit verbringen, auf die Ausführung von Tests zu warten, und, was noch wichtiger ist, weniger Zeit damit verbringen, Fehler aufzuspüren, die nicht vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="2bf71-155">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="2bf71-156">Hoffentlich können Sie die Vorteile des Testens zu schätzen wissen und die Qualitäten verstehen, die testbarer Code aufweist.</span><span class="sxs-lookup"><span data-stu-id="2bf71-156">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="2bf71-157">Wir sind dabei, wie Code geschrieben wird, der mit EF4 funktioniert, um Daten in einer Datenbank zu speichern, während sie beobachtbar und leicht zu isolieren sind.</span><span class="sxs-lookup"><span data-stu-id="2bf71-157">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="2bf71-158">Entwurfsmuster für Diepersistenz</span><span class="sxs-lookup"><span data-stu-id="2bf71-158">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="2bf71-159">Beide schlechten Beispiele, die zuvor vorgestellt wurden, hatten zu viele Verantwortlichkeiten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-159">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="2bf71-160">Das erste schlechte Beispiel musste eine Berechnung durchführen *und* in eine Datei schreiben.</span><span class="sxs-lookup"><span data-stu-id="2bf71-160">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="2bf71-161">Das zweite schlechte Beispiel musste Daten aus einer Datenbank lesen *und* eine Geschäftsberechnung durchführen *und* E-Mails senden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-161">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="2bf71-162">Durch das Entwerfen kleinerer Methoden, die Anliegen trennen und Die Verantwortung an andere Komponenten delegieren, machen Sie große Fortschritte beim Schreiben von testbarem Code.</span><span class="sxs-lookup"><span data-stu-id="2bf71-162">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="2bf71-163">Das Ziel besteht darin, Funktionalität zu erstellen, indem Aktionen aus kleinen und fokussierten Abstraktionen erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-163">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="2bf71-164">Wenn es um Datenpersistenz geht, sind die kleinen und fokussierten Abstraktionen, nach denen wir suchen, so häufig, dass sie als Designmuster dokumentiert wurden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-164">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="2bf71-165">Martin Fowlers Buch Patterns of Enterprise Application Architecture war das erste Werk, das diese Muster im Druck beschreibt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-165">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="2bf71-166">In den folgenden Abschnitten finden Sie eine kurze Beschreibung dieser Muster, bevor wir zeigen, wie diese ADO.NET Entity Framework diese Muster implementiert und mit ihnen arbeitet.</span><span class="sxs-lookup"><span data-stu-id="2bf71-166">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="2bf71-167">Repositorymuster</span><span class="sxs-lookup"><span data-stu-id="2bf71-167">The Repository Pattern</span></span>

<span data-ttu-id="2bf71-168">Fowler sagt, dass ein Repository "zwischen den Domänen- und Datenzuordnungsebenen über eine sammlungsähnliche Schnittstelle für den Zugriff auf Domänenobjekte vermittelt".</span><span class="sxs-lookup"><span data-stu-id="2bf71-168">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="2bf71-169">Das Ziel des Repository-Musters ist es, Code von den Minutien des Datenzugriffs zu isolieren, und wie wir zuvor gesehen haben, ist die Isolierung ein erforderliches Merkmal für die Testbarkeit.</span><span class="sxs-lookup"><span data-stu-id="2bf71-169">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="2bf71-170">Der Schlüssel zur Isolierung ist, wie das Repository Objekte mithilfe einer sammlungsähnlichen Schnittstelle verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="2bf71-170">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="2bf71-171">Die Logik, die Sie schreiben, um das Repository zu verwenden, hat keine Ahnung, wie das Repository die von Ihnen anfordernden Objekte materialisieren wird.</span><span class="sxs-lookup"><span data-stu-id="2bf71-171">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="2bf71-172">Das Repository kann mit einer Datenbank sprechen, oder es gibt einfach Objekte aus einer In-Memory-Auflistung zurück.</span><span class="sxs-lookup"><span data-stu-id="2bf71-172">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="2bf71-173">Ihr Code muss nur wissen, dass das Repository die Sammlung zu verwalten scheint, und Sie können Objekte aus der Auflistung abrufen, hinzufügen und löschen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-173">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="2bf71-174">In vorhandenen .NET-Anwendungen erbt ein konkretes Repository häufig von einer generischen Schnittstelle wie der folgenden:</span><span class="sxs-lookup"><span data-stu-id="2bf71-174">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="2bf71-175">Wir werden einige Änderungen an der Schnittstellendefinition vornehmen, wenn wir eine Implementierung für EF4 bereitstellen, aber das Grundlegende Konzept bleibt das gleiche.</span><span class="sxs-lookup"><span data-stu-id="2bf71-175">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="2bf71-176">Code kann ein konkretes Repository verwenden, das diese Schnittstelle implementiert, um eine Entität anhand ihres Primärschlüsselwerts abzurufen, eine Auflistung von Entitäten basierend auf der Auswertung eines Prädikats abzurufen oder einfach alle verfügbaren Entitäten abzurufen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-176">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="2bf71-177">Der Code kann auch Entitäten über die Repository-Schnittstelle hinzufügen und entfernen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-177">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="2bf71-178">Bei einem IRepository von Employee-Objekten kann Code die folgenden Vorgänge ausführen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-178">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="2bf71-179">Da der Code eine Schnittstelle (IRepository of Employee) verwendet, können wir dem Code verschiedene Implementierungen der Schnittstelle bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-179">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="2bf71-180">Eine Implementierung kann eine Implementierung sein, die von EF4 unterstützt wird, und das Beibehalten von Objekten in einer Microsoft SQL Server-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="2bf71-180">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="2bf71-181">Eine andere Implementierung (die wir während des Tests verwenden) kann durch eine Speicherliste von Employee-Objekten unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-181">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="2bf71-182">Die Schnittstelle wird dazu beitragen, die Isolierung im Code zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-182">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="2bf71-183">Beachten Sie,&lt;&gt; dass die IRepository T-Schnittstelle keinen Save-Vorgang verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="2bf71-183">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="2bf71-184">Wie aktualisieren wir vorhandene Objekte?</span><span class="sxs-lookup"><span data-stu-id="2bf71-184">How do we update existing objects?</span></span> <span data-ttu-id="2bf71-185">Möglicherweise stoßen Sie auf IRepository-Definitionen, die den Save-Vorgang enthalten, und Implementierungen dieser Repositorys müssen sofort ein Objekt in der Datenbank beibehalten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-185">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="2bf71-186">In vielen Anwendungen möchten wir Objekte jedoch nicht einzeln beibehalten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-186">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="2bf71-187">Stattdessen möchten wir Objekte zum Leben erwecken, vielleicht aus verschiedenen Repositorys, diese Objekte als Teil einer Geschäftsaktivität ändern und dann alle Objekte als Teil eines einzelnen atomaren Vorgangs beibehalten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-187">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="2bf71-188">Glücklicherweise gibt es ein Muster, um diese Art von Verhalten zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-188">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="2bf71-189">Die Arbeitseinheit</span><span class="sxs-lookup"><span data-stu-id="2bf71-189">The Unit of Work Pattern</span></span>

<span data-ttu-id="2bf71-190">Fowler sagt, dass eine Arbeitseinheit "eine Liste der Objekte führen wird, die von einer Geschäftstransaktion betroffen sind, und das Ausschreiben von Änderungen und die Lösung von Parallelitätsproblemen koordiniert".</span><span class="sxs-lookup"><span data-stu-id="2bf71-190">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="2bf71-191">Es liegt in der Verantwortung der Arbeitseinheit, Änderungen an den Objekten, die wir aus einem Repository zum Leben erwecken, nachzuverfolgen und alle Änderungen beizubehalten, die wir an den Objekten vorgenommen haben, wenn wir der Arbeitseinheit sagen, dass sie die Änderungen übernehmen soll.</span><span class="sxs-lookup"><span data-stu-id="2bf71-191">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="2bf71-192">Es liegt auch in der Verantwortung der Arbeitseinheit, die neuen Objekte, die wir allen Repositorys hinzugefügt haben, in eine Datenbank einzufügen und die Objekte in eine Datenbank einzufügen und auch das Löschen von Mange zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-192">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="2bf71-193">Wenn Sie jemals mit ADO.NET DataSets gearbeitet haben, sind Sie bereits mit dem Arbeitseinheitsmuster vertraut.</span><span class="sxs-lookup"><span data-stu-id="2bf71-193">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="2bf71-194">ADO.NET DataSets hatten die Möglichkeit, unsere Aktualisierungen, Löschungen und das Einfügen von DataRow-Objekten nachzuverfolgen und (mit Hilfe eines TableAdapters) alle unsere Änderungen mit einer Datenbank abzugleichen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-194">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="2bf71-195">DataSet-Objekte modellieren jedoch eine getrennte Teilmenge der zugrunde liegenden Datenbank.</span><span class="sxs-lookup"><span data-stu-id="2bf71-195">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="2bf71-196">Das Arbeitseinheitsmuster weist das gleiche Verhalten auf, arbeitet jedoch mit Geschäftsobjekten und Domänenobjekten, die vom Datenzugriffscode isoliert sind und die Datenbank nicht kennen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-196">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="2bf71-197">Eine Abstraktion zum Modellieren der Arbeitseinheit im .NET-Code könnte wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="2bf71-197">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="2bf71-198">Durch das Auflegen von Repository-Referenzen aus der Arbeitseinheit können wir sicherstellen, dass eine einzelne Arbeitseinheit samt Arbeitsobjekt die Möglichkeit hat, alle Entitäten zu verfolgen, die während einer Geschäftstransaktion materialisiert wurden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-198">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="2bf71-199">Die Implementierung der Commit-Methode für eine echte Arbeitseinheit ist der Ort, an dem die ganze Magie geschieht, um Änderungen im Arbeitsspeicher mit der Datenbank in Einklang zu bringen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-199">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="2bf71-200">Bei einem IUnitOfWork-Verweis kann Code Änderungen an Geschäftsobjekten vornehmen, die aus einem oder mehreren Repositorys abgerufen wurden, und alle Änderungen mithilfe des atomaren Commit-Vorgangs speichern.</span><span class="sxs-lookup"><span data-stu-id="2bf71-200">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="2bf71-201">Das Lazy Load Pattern</span><span class="sxs-lookup"><span data-stu-id="2bf71-201">The Lazy Load Pattern</span></span>

<span data-ttu-id="2bf71-202">Fowler verwendet den Namen lazy load, um "ein Objekt zu beschreiben, das nicht alle Benötigten enthält, aber weiß, wie man es bekommt".</span><span class="sxs-lookup"><span data-stu-id="2bf71-202">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="2bf71-203">Transparentes verzögertes Laden ist ein wichtiges Feature beim Schreiben von testbarem Geschäftscode und beim Arbeiten mit einer relationalen Datenbank.</span><span class="sxs-lookup"><span data-stu-id="2bf71-203">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="2bf71-204">Betrachten Sie als Beispiel den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="2bf71-204">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="2bf71-205">Wie wird die TimeCards-Auflistung aufgefüllt?</span><span class="sxs-lookup"><span data-stu-id="2bf71-205">How is the TimeCards collection populated?</span></span> <span data-ttu-id="2bf71-206">Es gibt zwei mögliche Antworten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-206">There are two possible answers.</span></span> <span data-ttu-id="2bf71-207">Eine Antwort ist, dass das Mitarbeiter-Repository, wenn es aufgefordert wird, einen Mitarbeiter abzurufen, eine Abfrage ausgibt, um sowohl den Mitarbeiter zusammen mit den zugehörigen Zeitkarteninformationen des Mitarbeiters abzurufen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-207">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="2bf71-208">In relationalen Datenbanken erfordert dies in der Regel eine Abfrage mit einer JOIN-Klausel und kann dazu führen, dass mehr Informationen abgerufen werden, als eine Anwendung benötigt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-208">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="2bf71-209">Was passiert, wenn die Anwendung niemals die TimeCards-Eigenschaft berühren muss?</span><span class="sxs-lookup"><span data-stu-id="2bf71-209">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="2bf71-210">Eine zweite Antwort ist, die TimeCards-Eigenschaft "on demand" zu laden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-210">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="2bf71-211">Dieses verzögerte Laden ist implizit und für die Geschäftslogik transparent, da der Code keine speziellen APIs aufruft, um Zeitkarteninformationen abzurufen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-211">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="2bf71-212">Der Code setzt voraus, dass die Zeitkarteninformationen bei Bedarf vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="2bf71-212">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="2bf71-213">Es gibt einige Magie mit faulen Laden beteiligt, die in der Regel Laufzeit Abfangen von Methodenaufrufe beinhaltet.</span><span class="sxs-lookup"><span data-stu-id="2bf71-213">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="2bf71-214">Der Abfangcode ist für das Gespräch mit der Datenbank und das Abrufen von Zeitkarteninformationen verantwortlich, während die Geschäftslogik frei bleibt, um Geschäftslogik zu sein.</span><span class="sxs-lookup"><span data-stu-id="2bf71-214">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="2bf71-215">Diese verzögerte Lastmagie ermöglicht es dem Geschäftscode, sich von Datenabrufvorgängen zu isolieren und führt zu mehr testbarem Code.</span><span class="sxs-lookup"><span data-stu-id="2bf71-215">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="2bf71-216">Der Nachteil einer verzögerten Last besteht darin, dass der Code eine zusätzliche Abfrage ausführt, wenn eine *Anwendung* die Zeitkarteninformationen benötigt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-216">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="2bf71-217">Dies ist für viele Anwendungen kein Problem, aber für leistungskritische Anwendungen oder Anwendungen, die eine Reihe von Mitarbeiterobjekten durchlaufen und eine Abfrage ausführen, um Zeitkarten während jeder Iteration der Schleife abzurufen (ein Problem, das häufig als N+1-Abfrageproblem bezeichnet wird), ist das verzögerte Laden ein Ziehen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-217">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="2bf71-218">In diesen Szenarien möchte eine Anwendung möglicherweise zeitkarteninformationen so effizient wie möglich laden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-218">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="2bf71-219">Glücklicherweise werden wir sehen, wie EF4 sowohl implizite verzögerte Lasten als auch effiziente eifrige Lasten unterstützt, während wir in den nächsten Abschnitt gehen und diese Muster implementieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-219">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="2bf71-220">Implementieren von Mustern mit dem Entity Framework</span><span class="sxs-lookup"><span data-stu-id="2bf71-220">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="2bf71-221">Die gute Nachricht ist, dass alle Entwurfsmuster, die wir im letzten Abschnitt beschrieben haben, einfach mit EF4 zu implementieren sind.</span><span class="sxs-lookup"><span data-stu-id="2bf71-221">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="2bf71-222">Um zu demonstrieren, verwenden wir eine einfache ASP.NET MVC-Anwendung, um Mitarbeiter und die zugehörigen Zeitkarteninformationen zu bearbeiten und anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-222">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="2bf71-223">Beginnen wir mit den folgenden "einfachen alten CLR-Objekten" (POCOs).</span><span class="sxs-lookup"><span data-stu-id="2bf71-223">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

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

<span data-ttu-id="2bf71-224">Diese Klassendefinitionen werden sich leicht ändern, wenn wir verschiedene Ansätze und Features von EF4 untersuchen, aber die Absicht ist, diese Klassen so persistenzunvierungsunwissend (PI) wie möglich zu halten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-224">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="2bf71-225">Ein PI-Objekt weiß *nicht, wie*, oder *sogar, wenn*, der Zustand, den es hält, in einer Datenbank lebt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-225">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="2bf71-226">PI- und POCOs gehen Hand in Hand mit testbarer Software.</span><span class="sxs-lookup"><span data-stu-id="2bf71-226">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="2bf71-227">Objekte, die einen POCO-Ansatz verwenden, sind weniger eingeschränkt, flexibler und einfacher zu testen, da sie ohne vorhandene Datenbank ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="2bf71-227">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="2bf71-228">Mit den POCOs können wir ein Entity Data Model (EDM) in Visual Studio erstellen (siehe Abbildung 1).</span><span class="sxs-lookup"><span data-stu-id="2bf71-228">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="2bf71-229">Wir werden das EDM nicht verwenden, um Code für unsere Entitäten zu generieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-229">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="2bf71-230">Stattdessen wollen wir die Entitäten, die wir liebevoll von Hand herstellen, nutzen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-230">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="2bf71-231">Wir verwenden das EDM nur zum Generieren unseres Datenbankschemas und bereitstellen die Metadaten, die EF4 zum Zuordnen von Objekten in die Datenbank benötigt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-231">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![ef test_01](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="2bf71-233">**Abbildung 1**</span><span class="sxs-lookup"><span data-stu-id="2bf71-233">**Figure 1**</span></span>

<span data-ttu-id="2bf71-234">Hinweis: Wenn Sie zuerst das EDM-Modell entwickeln möchten, ist es möglich, sauberen POCO-Code aus dem EDM zu generieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-234">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="2bf71-235">Sie können dies mit einer Visual Studio 2010-Erweiterung tun, die vom Data Programmability-Team bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="2bf71-235">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="2bf71-236">Um die Erweiterung herunterzuladen, starten Sie den Erweiterungs-Manager aus dem Menü Extras in Visual Studio und durchsuchen Sie die Online-Galerie mit Vorlagen nach "POCO" (siehe Abbildung 2).</span><span class="sxs-lookup"><span data-stu-id="2bf71-236">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="2bf71-237">Für EF stehen mehrere POCO-Vorlagen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="2bf71-237">There are several POCO templates available for EF.</span></span> <span data-ttu-id="2bf71-238">Weitere Informationen zur Verwendung der Vorlage finden Sie unter ["Exemplarische Vorgehensweise: POCO-Vorlage für das Entity Framework](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)".</span><span class="sxs-lookup"><span data-stu-id="2bf71-238">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)”.</span></span>

![ef test_02](~/ef6/media/eftest-02.png)

<span data-ttu-id="2bf71-240">**Abbildung 2**</span><span class="sxs-lookup"><span data-stu-id="2bf71-240">**Figure 2**</span></span>

<span data-ttu-id="2bf71-241">Von diesem POCO-Startpunkt aus werden wir zwei verschiedene Ansätze für testbaren Code untersuchen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-241">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="2bf71-242">Der erste Ansatz, den ich den EF-Ansatz nenne, weil er Abstraktionen aus der Entity Framework-API nutzt, um Arbeitseinheiten und Repositorys zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-242">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="2bf71-243">Im zweiten Ansatz erstellen wir unsere eigenen benutzerdefinierten Repository-Abstraktionen und sehen dann die Vor- und Nachteile jedes Ansatzes.</span><span class="sxs-lookup"><span data-stu-id="2bf71-243">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="2bf71-244">Wir beginnen mit der Erforschung des EF-Ansatzes.</span><span class="sxs-lookup"><span data-stu-id="2bf71-244">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="2bf71-245">Eine EF-zentrierte Implementierung</span><span class="sxs-lookup"><span data-stu-id="2bf71-245">An EF Centric Implementation</span></span>

<span data-ttu-id="2bf71-246">Betrachten Sie die folgende Controlleraktion aus einem ASP.NET MVC-Projekt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-246">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="2bf71-247">Die Aktion ruft ein Employee-Objekt ab und gibt ein Ergebnis zurück, um eine detaillierte Ansicht des Mitarbeiters anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-247">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="2bf71-248">Ist der Code testbar?</span><span class="sxs-lookup"><span data-stu-id="2bf71-248">Is the code testable?</span></span> <span data-ttu-id="2bf71-249">Es gibt mindestens zwei Tests, die wir benötigen, um das Verhalten der Aktion zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-249">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="2bf71-250">Zuerst möchten wir überprüfen, ob die Aktion die richtige Ansicht zurückgibt – ein einfacher Test.</span><span class="sxs-lookup"><span data-stu-id="2bf71-250">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="2bf71-251">Wir möchten auch einen Test schreiben, um zu überprüfen, ob die Aktion den richtigen Mitarbeiter abruft, und wir möchten dies tun, ohne Code zum Abfragen der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-251">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="2bf71-252">Denken Sie daran, dass wir den zu testenden Code isolieren möchten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-252">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="2bf71-253">Durch die Isolierung wird sichergestellt, dass der Test nicht aufgrund eines Fehlers im Datenzugriffscode oder in der Datenbankkonfiguration fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-253">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="2bf71-254">Wenn der Test fehlschlägt, werden wir wissen, dass wir einen Fehler in der Controllerlogik und nicht in einer niedrigeren Systemkomponente haben.</span><span class="sxs-lookup"><span data-stu-id="2bf71-254">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="2bf71-255">Um Isolation zu erreichen, benötigen wir einige Abstraktionen wie die Schnittstellen, die wir zuvor für Repositories und Arbeitseinheiten vorgestellt haben.</span><span class="sxs-lookup"><span data-stu-id="2bf71-255">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="2bf71-256">Denken Sie daran, dass das Repository-Muster so konzipiert ist, dass es zwischen Domänenobjekten und dem Datenzuordnungs-Layer vermittelt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-256">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="2bf71-257">In diesem Szenario *ist* EF4 der Datenzuordnungs-Layer und stellt bereits&lt;eine&gt; Repository-ähnliche Abstraktion mit dem Namen IObjectSet T (aus dem Namespace System.Data.Objects) bereit.</span><span class="sxs-lookup"><span data-stu-id="2bf71-257">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="2bf71-258">Die Schnittstellendefinition sieht wie folgt aus.</span><span class="sxs-lookup"><span data-stu-id="2bf71-258">The interface definition looks like the following.</span></span>

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

<span data-ttu-id="2bf71-259">IObjectSet&lt;&gt; T erfüllt die Anforderungen für ein Repository, da es einer&lt;Auflistung&gt;von Objekten ähnelt (über IEnumerable T ) und Methoden zum Hinzufügen und Entfernen von Objekten aus der simulierten Auflistung bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-259">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="2bf71-260">Die Methoden Anfügen und Trennen stellen zusätzliche Funktionen der EF4-API bereit.</span><span class="sxs-lookup"><span data-stu-id="2bf71-260">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="2bf71-261">Um IObjectSet&lt;&gt; T als Schnittstelle für Repositories zu verwenden, benötigen wir eine Einheit der Arbeitsabstraktion, um Repositories miteinander zu binden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-261">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="2bf71-262">Eine konkrete Implementierung dieser Schnittstelle wird mit SQL Server gesprochen und ist einfach mit der ObjectContext-Klasse aus EF4 zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-262">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="2bf71-263">Die ObjectContext-Klasse ist die eigentliche Arbeitseinheit in der EF4-API.</span><span class="sxs-lookup"><span data-stu-id="2bf71-263">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

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

<span data-ttu-id="2bf71-264">Das Leben eines&lt;&gt; IObjectSets T ist so einfach wie das Aufrufen der CreateObjectSet-Methode des ObjectContext-Objekts.</span><span class="sxs-lookup"><span data-stu-id="2bf71-264">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="2bf71-265">Hinter den Kulissen verwendet das Framework die Metadaten, die wir&lt;im&gt;EDM bereitgestellt haben, um ein konkretes ObjectSet T zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-265">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="2bf71-266">Wir bleiben bei der Rückgabe&lt;der&gt; IObjectSet T-Schnittstelle, da sie dazu beiträgt, die Testbarkeit im Clientcode zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-266">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="2bf71-267">Diese konkrete Implementierung ist in der Produktion nützlich, aber wir müssen uns darauf konzentrieren, wie wir unsere IUnitOfWork-Abstraktion verwenden, um das Testen zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="2bf71-267">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="2bf71-268">Die Test-Doppel</span><span class="sxs-lookup"><span data-stu-id="2bf71-268">The Test Doubles</span></span>

<span data-ttu-id="2bf71-269">Um die Controller-Aktion zu isolieren, müssen wir zwischen der tatsächlichen Arbeitseinheit (unterstützt durch einen ObjectContext) und einer doppelten oder "gefälschten" Arbeitseinheit (Durchführung von In-Memory-Vorgängen) wechseln.</span><span class="sxs-lookup"><span data-stu-id="2bf71-269">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="2bf71-270">Der übliche Ansatz für diese Art des Schaltens besteht darin, den MVC-Controller nicht eine Arbeitseinheit instanziieren zu lassen, sondern die Arbeitseinheit als Konstruktorparameter an den Controller weiterzugeben.</span><span class="sxs-lookup"><span data-stu-id="2bf71-270">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="2bf71-271">Der obige Code ist ein Beispiel für Abhängigkeitsinjektion.</span><span class="sxs-lookup"><span data-stu-id="2bf71-271">The above code is an example of dependency injection.</span></span> <span data-ttu-id="2bf71-272">Wir erlauben dem Controller nicht, seine Abhängigkeit (die Arbeitseinheit) zu erstellen, sondern die Abhängigkeit in den Controller einzuschleusen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-272">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="2bf71-273">In einem MVC-Projekt ist es üblich, eine benutzerdefinierte Controller-Factory in Kombination mit einem Inversion of Control (IoC)-Container zu verwenden, um die Abhängigkeitsinjektion zu automatisieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-273">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="2bf71-274">Diese Themen gehen über den Rahmen dieses Artikels hinaus, sie können jedoch den Verweisen am Ende dieses Artikels folgen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-274">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="2bf71-275">Eine gefälschte Arbeitseinheit, die wir zum Testen verwenden können, könnte wie folgt aussehen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-275">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

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

<span data-ttu-id="2bf71-276">Beachten Sie, dass die gefälschte Arbeitseinheit eine Commited-Eigenschaft verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="2bf71-276">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="2bf71-277">Manchmal ist es nützlich, Einer gefälschten Klasse Features hinzuzufügen, die das Testen erleichtern.</span><span class="sxs-lookup"><span data-stu-id="2bf71-277">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="2bf71-278">In diesem Fall ist es leicht zu beobachten, ob Code eine Arbeitseinheit durch Überprüfen der Commited-Eigenschaft festüberträgt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-278">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="2bf71-279">Wir benötigen auch ein gefälschtes&lt;&gt; IObjectSet T, um Mitarbeiter- und TimeCard-Objekte im Speicher zu halten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-279">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="2bf71-280">Wir können eine einzelne Implementierung mit Generika bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-280">We can provide a single implementation using generics.</span></span>

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

<span data-ttu-id="2bf71-281">Dieser Test delegiert den größten Teil&lt;&gt; seiner Arbeit an ein zugrunde liegendes HashSet T-Objekt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-281">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="2bf71-282">Beachten Sie,&lt;dass&gt; IObjectSet T eine generische Einschränkung erfordert, die T als Klasse&lt;(einen Verweistyp) erzwingt, und zwingt uns außerdem, IQueryable T&gt;zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-282">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="2bf71-283">Es ist einfach, eine In-Memory-Sammlung als&lt;&gt; IQueryable T mit dem Standard-LINQ-Operator AsQueryable erscheinen zu lassen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-283">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="2bf71-284">Die Tests</span><span class="sxs-lookup"><span data-stu-id="2bf71-284">The Tests</span></span>

<span data-ttu-id="2bf71-285">Herkömmliche Komponententests verwenden eine einzelne Testklasse, um alle Tests für alle Aktionen in einem einzigen MVC-Controller abzuhalten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-285">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="2bf71-286">Wir können diese Tests oder jede Art von Komponententest mit den in Memory-Fakes schreiben, die wir erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="2bf71-286">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="2bf71-287">Für diesen Artikel vermeiden wir jedoch den monolithischen Testklassenansatz und gruppieren unsere Tests stattdessen, um sich auf eine bestimmte Funktionalität zu konzentrieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-287">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span><span data-ttu-id="2bf71-288">"Neuer Mitarbeiter erstellen" kann z. B. die Funktionalität sein, die wir testen möchten, daher verwenden wir eine einzelne Testklasse, um die Einzelcontrolleraktion zu überprüfen, die für die Erstellung eines neuen Mitarbeiters verantwortlich ist.</span><span class="sxs-lookup"><span data-stu-id="2bf71-288">  For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="2bf71-289">Es gibt einen allgemeinen Setup-Code, den wir für all diese feinkörnigen Testklassen benötigen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-289">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="2bf71-290">Zum Beispiel müssen wir immer unsere In-Memory-Repositorys und gefälschte Arbeitseinheit erstellen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-290">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="2bf71-291">Wir brauchen auch eine Instanz des Mitarbeiter-Controllers mit der gefälschten Arbeitseinheit injiziert.</span><span class="sxs-lookup"><span data-stu-id="2bf71-291">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="2bf71-292">Wir teilen diesen allgemeinen Setupcode über Testklassen hinweg mithilfe einer Basisklasse.</span><span class="sxs-lookup"><span data-stu-id="2bf71-292">We’ll share this common setup code across test classes by using a base class.</span></span>

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

<span data-ttu-id="2bf71-293">Die "Objektmutter", die wir in der Basisklasse verwenden, ist ein gängiges Muster zum Erstellen von Testdaten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-293">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="2bf71-294">Eine Objektmutter enthält Factorymethoden zum Instanziieren von Testentitäten für die Verwendung über mehrere Prüfgeräte hinweg.</span><span class="sxs-lookup"><span data-stu-id="2bf71-294">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

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

<span data-ttu-id="2bf71-295">Wir können die EmployeeControllerTestBase als Basisklasse für eine Reihe von Prüfvorrichtungen verwenden (siehe Abbildung 3).</span><span class="sxs-lookup"><span data-stu-id="2bf71-295">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="2bf71-296">Jede Prüfvorrichtung testet eine bestimmte Controlleraktion.</span><span class="sxs-lookup"><span data-stu-id="2bf71-296">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="2bf71-297">Ein Testfixture konzentriert sich beispielsweise auf das Testen der Aktion Erstellen, die während einer HTTP GET-Anforderung verwendet wird (um die Ansicht zum Erstellen eines Mitarbeiters anzuzeigen), und eine andere Fixture konzentriert sich auf die Aktion Erstellen, die in einer HTTP-POST-Anforderung verwendet wird (um Informationen zu verwenden, die vom Benutzer zum Erstellen eines Mitarbeiters übermittelt wurden).</span><span class="sxs-lookup"><span data-stu-id="2bf71-297">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="2bf71-298">Jede abgeleitete Klasse ist nur für die Einrichtung verantwortlich, die in ihrem spezifischen Kontext erforderlich ist, und stellt die Assertionen bereit, die erforderlich sind, um die Ergebnisse für ihren spezifischen Testkontext zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-298">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![ef test_03](~/ef6/media/eftest-03.png)

<span data-ttu-id="2bf71-300">**Abbildung 3**</span><span class="sxs-lookup"><span data-stu-id="2bf71-300">**Figure 3**</span></span>

<span data-ttu-id="2bf71-301">Die hier vorgestellte Namenskonvention und der Teststil sind für testbaren Code nicht erforderlich – es ist nur ein Ansatz.</span><span class="sxs-lookup"><span data-stu-id="2bf71-301">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="2bf71-302">Abbildung 4 zeigt die Tests, die im Jet Brains Resharper Testrunner-Plugin für Visual Studio 2010 ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-302">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![ef test_04](~/ef6/media/eftest-04.png)

<span data-ttu-id="2bf71-304">**Abbildung 4**</span><span class="sxs-lookup"><span data-stu-id="2bf71-304">**Figure 4**</span></span>

<span data-ttu-id="2bf71-305">Mit einer Basisklasse zum Behandeln des freigegebenen Setupcodes sind die Komponententests für jede Controlleraktion klein und einfach zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="2bf71-305">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="2bf71-306">Die Tests werden schnell ausgeführt (da wir In-Memory-Vorgänge ausführen) und sollten nicht aufgrund nicht verwandter Infrastruktur- oder Umgebungsprobleme fehlschlagen (weil wir die zu testende Einheit isoliert haben).</span><span class="sxs-lookup"><span data-stu-id="2bf71-306">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

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

<span data-ttu-id="2bf71-307">In diesen Tests übernimmt die Basisklasse den größten Teil der Einrichtungsarbeit.</span><span class="sxs-lookup"><span data-stu-id="2bf71-307">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="2bf71-308">Denken Sie daran, dass der Basisklassenkonstruktor das In-Memory-Repository, eine gefälschte Arbeitseinheit und eine Instanz der EmployeeController-Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-308">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="2bf71-309">Die Testklasse leitet sich von dieser Basisklasse ab und konzentriert sich auf die Besonderheiten des Testens der Create-Methode.</span><span class="sxs-lookup"><span data-stu-id="2bf71-309">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="2bf71-310">In diesem Fall laufen die Einzelheiten auf die Schritte "Anordnen, Handeln und Behaupten" hinaus, die Sie in jedem Komponententestverfahren sehen:</span><span class="sxs-lookup"><span data-stu-id="2bf71-310">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="2bf71-311">Erstellen Sie ein neuesEmployee-Objekt, um eingehende Daten zu simulieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-311">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="2bf71-312">Rufen Sie die Aktion Erstellen des EmployeeControllers auf, und übergeben Sie den neuen Mitarbeiter.</span><span class="sxs-lookup"><span data-stu-id="2bf71-312">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="2bf71-313">Überprüfen Sie, ob die Aktion Erstellen die erwarteten Ergebnisse liefert (der Mitarbeiter wird im Repository angezeigt).</span><span class="sxs-lookup"><span data-stu-id="2bf71-313">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="2bf71-314">Was wir erstellt haben, ermöglicht es uns, jede der EmployeeController-Aktionen zu testen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-314">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="2bf71-315">Wenn wir beispielsweise Tests für die Indexaktion des Employee-Controllers schreiben, können wir von der Testbasisklasse erben, um die gleiche Basiseinrichtung für unsere Tests einzurichten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-315">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="2bf71-316">Auch hier erstellt die Basisklasse das In-Memory-Repository, die gefälschte Arbeitseinheit und eine Instanz des EmployeeControllers.</span><span class="sxs-lookup"><span data-stu-id="2bf71-316">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="2bf71-317">Die Tests für die Indexaktion müssen sich nur auf das Aufrufen der Indexaktion und das Testen der Qualitäten des Modells konzentrieren, das die Aktion zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-317">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

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

<span data-ttu-id="2bf71-318">Die Tests, die wir mit In-Memory-Fakes erstellen, sind darauf ausgerichtet, den *Zustand* der Software zu testen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-318">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="2bf71-319">Wenn wir beispielsweise die Aktion Erstellen testen, möchten wir den Status des Repositorys überprüfen, nachdem die Erstellungsaktion ausgeführt wurde – hält das Repository den neuen Mitarbeiter?</span><span class="sxs-lookup"><span data-stu-id="2bf71-319">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="2bf71-320">Später werden wir uns mit interaktionsbasierten Tests befassen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-320">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="2bf71-321">Interaktionsbasierte Tests fragen, ob der zu testende Code die richtigen Methoden für unsere Objekte aufgerufen und die richtigen Parameter übergeben hat.</span><span class="sxs-lookup"><span data-stu-id="2bf71-321">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="2bf71-322">Vorerst bewegen wir auf der Abdeckung ein weiteres Designmuster – die faule Last.</span><span class="sxs-lookup"><span data-stu-id="2bf71-322">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="2bf71-323">Eager Loading und Lazy Loading</span><span class="sxs-lookup"><span data-stu-id="2bf71-323">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="2bf71-324">Irgendwann in der ASP.NET MVC-Webanwendung möchten wir möglicherweise die Informationen eines Mitarbeiters anzeigen und die zugehörigen Zeitkarten des Mitarbeiters einschließen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-324">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="2bf71-325">Beispielsweise können wir eine Zeitkartenzusammenfassungsanzeige haben, die den Namen des Mitarbeiters und die Gesamtzahl der Zeitkarten im System anzeigt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-325">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="2bf71-326">Es gibt mehrere Ansätze, die wir ergreifen können, um diese Funktion zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-326">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="2bf71-327">Projection</span><span class="sxs-lookup"><span data-stu-id="2bf71-327">Projection</span></span>

<span data-ttu-id="2bf71-328">Ein einfacher Ansatz zum Erstellen der Zusammenfassung besteht darin, ein Modell zu erstellen, das den Informationen gewidmet ist, die in der Ansicht angezeigt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-328">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="2bf71-329">In diesem Szenario könnte das Modell wie folgt aussehen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-329">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="2bf71-330">Beachten Sie, dass das EmployeeSummaryViewModel keine Entität ist, d. h. es ist nicht etwas, das wir in der Datenbank beibehalten möchten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-330">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="2bf71-331">Wir verwenden diese Klasse nur, um Daten stark typisiert in die Ansicht zu mischen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-331">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="2bf71-332">Das Ansichtsmodell ist wie ein Datenübertragungsobjekt (DTO), da es kein Verhalten (keine Methoden) enthält – nur Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="2bf71-332">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="2bf71-333">Die Eigenschaften enthalten die Daten, die wir verschieben müssen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-333">The properties will hold the data we need to move.</span></span> <span data-ttu-id="2bf71-334">Es ist einfach, dieses Ansichtsmodell mit dem Standardprojektionsoperator von LINQ – dem Select-Operator – zu instanziieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-334">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

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

<span data-ttu-id="2bf71-335">Es gibt zwei bemerkenswerte Funktionen zum obigen Code.</span><span class="sxs-lookup"><span data-stu-id="2bf71-335">There are two notable features to the above code.</span></span> <span data-ttu-id="2bf71-336">Erstens – der Code ist einfach zu testen, da er immer noch leicht zu beobachten und zu isolieren ist.</span><span class="sxs-lookup"><span data-stu-id="2bf71-336">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="2bf71-337">Der Select-Operator arbeitet genauso gut gegen unsere In-Memory-Fakes wie gegen die reale Arbeitseinheit.</span><span class="sxs-lookup"><span data-stu-id="2bf71-337">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

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

<span data-ttu-id="2bf71-338">Die zweite bemerkenswerte Funktion ist, wie der Code EF4 ermöglicht, eine einzelne, effiziente Abfrage zu generieren, um Mitarbeiter- und Zeitkarteninformationen zusammenzustellen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-338">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="2bf71-339">Wir haben Mitarbeiterinformationen und Zeitkarteninformationen in dasselbe Objekt geladen, ohne spezielle APIs zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-339">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="2bf71-340">Der Code hat lediglich die Informationen ausgedrückt, die er benötigt, indem Standard-LINQ-Operatoren verwendet werden, die mit In-Memory-Datenquellen sowie Remote-Datenquellen arbeiten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-340">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="2bf71-341">EF4 konnte die von der LINQ-Abfrage und\# dem C-Compiler generierten Ausdrucksstrukturen in eine einzige und effiziente T-SQL-Abfrage übersetzen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-341">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

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

<span data-ttu-id="2bf71-342">Es gibt andere Zeiten, in denen wir nicht mit einem Ansichtsmodell oder DTO-Objekt arbeiten möchten, sondern mit realen Entitäten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-342">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="2bf71-343">Wenn wir wissen, dass wir einen Mitarbeiter *und* die Zeitkarten des Mitarbeiters benötigen, können wir die zugehörigen Daten unauffällig und effizient laden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-343">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="2bf71-344">Explizites Eager-Laden</span><span class="sxs-lookup"><span data-stu-id="2bf71-344">Explicit Eager Loading</span></span>

<span data-ttu-id="2bf71-345">Wenn wir verwandte Entitätsinformationen eifrig laden möchten, benötigen wir einen Mechanismus für die Geschäftslogik (oder in diesem Szenario die Steuerungsaktionslogik), um seinen Wunsch in das Repository auszudrücken.</span><span class="sxs-lookup"><span data-stu-id="2bf71-345">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="2bf71-346">Die EF4&lt;ObjectQuery T-Klasse&gt; definiert eine Include-Methode, um die zugehörigen Objekte anzugeben, die während einer Abfrage abgerufen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-346">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="2bf71-347">Denken Sie daran, dass DER EF4&lt;ObjectContext Entitäten über die konkrete ObjectSet T-Klasse&gt; verfügbar macht, die von ObjectQuery&lt;T&gt;erbt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-347">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span><span data-ttu-id="2bf71-348">Wenn wir ObjectSet&lt;&gt; T-Referenzen in unserer Controlleraktion verwenden, könnten wir den folgenden Code schreiben, um eine eifrige Last von Zeitkarteninformationen für jeden Mitarbeiter anzugeben.</span><span class="sxs-lookup"><span data-stu-id="2bf71-348">  If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="2bf71-349">Da wir jedoch versuchen, unseren Code testbar zu halten, setzen wir ObjectSet&lt;T&gt; nicht von außerhalb der tatsächlichen Einheit der Arbeitsklasse aus.</span><span class="sxs-lookup"><span data-stu-id="2bf71-349">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="2bf71-350">Stattdessen verlassen wir uns auf&lt;&gt; die IObjectSet T-Schnittstelle,&lt;die&gt; einfacher zu fälschen ist, aber IObjectSet T definiert keine Include-Methode.</span><span class="sxs-lookup"><span data-stu-id="2bf71-350">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="2bf71-351">Das Schöne an LINQ ist, dass wir unseren eigenen Include-Operator erstellen können.</span><span class="sxs-lookup"><span data-stu-id="2bf71-351">The beauty of LINQ is that we can create our own Include operator.</span></span>

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

<span data-ttu-id="2bf71-352">Beachten Sie, dass dieser Include-Operator als&lt;&gt; Erweiterungsmethode für&lt;&gt;IQueryable T anstelle von IObjectSet T definiert ist.</span><span class="sxs-lookup"><span data-stu-id="2bf71-352">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="2bf71-353">Dies gibt uns die Möglichkeit, die Methode mit einem breiteren&gt;Spektrum möglicher&lt;&gt;Typen zu&lt;&gt;verwenden, einschließlich&lt;&gt;IQueryable&lt;T , IObjectSet T , ObjectQuery T und ObjectSet T .</span><span class="sxs-lookup"><span data-stu-id="2bf71-353">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="2bf71-354">Für den Fall, dass die zugrunde liegende&lt;&gt;Sequenz keine echte EF4 ObjectQuery T ist, wird kein Schaden angerichtet, und der Include-Operator ist ein No-Op.</span><span class="sxs-lookup"><span data-stu-id="2bf71-354">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="2bf71-355">Wenn es sich bei&lt;der&gt; zugrunde liegenden *Sequenz* um eine ObjectQuery T handelt (oder von ObjectQuery&lt;T&gt;abgeleitet wurde), wird EF4 unsere Anforderung für zusätzliche Daten sehen und die richtige SQL-Abfrage formulieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-355">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="2bf71-356">Mit diesem neuen Betreiber können wir explizit eine eifrige Ladung von Zeitkarteninformationen aus dem Repository anfordern.</span><span class="sxs-lookup"><span data-stu-id="2bf71-356">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="2bf71-357">Bei der Ausführung für einen echten ObjectContext erzeugt der Code die folgende einzelne Abfrage.</span><span class="sxs-lookup"><span data-stu-id="2bf71-357">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="2bf71-358">Die Abfrage sammelt in einer Fahrt genügend Informationen aus der Datenbank, um die Mitarbeiterobjekte zu materialisieren und ihre TimeCards-Eigenschaft vollständig aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-358">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

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

<span data-ttu-id="2bf71-359">Die gute Nachricht ist, dass der Code innerhalb der Aktionsmethode vollständig testbar bleibt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-359">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="2bf71-360">Wir müssen keine zusätzlichen Funktionen für unsere Fälschungen bereitstellen, um den Include-Operator zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-360">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="2bf71-361">Die schlechte Nachricht ist, dass wir den Include-Operator innerhalb des Codes verwenden mussten, den wir persistenz unwissend halten wollten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-361">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="2bf71-362">Dies ist ein Paradebeispiel für die Art von Kompromissen, die Sie beim Erstellen von testbarem Code auswerten müssen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-362">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="2bf71-363">Es gibt Zeiten, in denen Sie Persistenzbedenken außerhalb der Repository-Abstraktion auslaufen lassen müssen, um die Leistungsziele zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-363">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="2bf71-364">Die Alternative zum eifrigen Laden ist das faule Laden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-364">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="2bf71-365">Lazy Loading bedeutet, dass wir unseren Geschäftscode *nicht* benötigen, um die Anforderung für zugehörige Daten explizit anzukündigen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-365">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="2bf71-366">Stattdessen verwenden wir unsere Entitäten in der Anwendung und wenn zusätzliche Daten benötigt werden, lädt Entity Framework die Daten bei Bedarf.</span><span class="sxs-lookup"><span data-stu-id="2bf71-366">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="2bf71-367">Lazy Loading</span><span class="sxs-lookup"><span data-stu-id="2bf71-367">Lazy Loading</span></span>

<span data-ttu-id="2bf71-368">Es ist leicht, sich ein Szenario vorzustellen, in dem wir nicht wissen, welche Daten eine Geschäftslogik benötigt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-368">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="2bf71-369">Wir wissen vielleicht, dass die Logik ein Mitarbeiterobjekt benötigt, aber wir können in verschiedene Ausführungspfade verzweigen, wo einige dieser Pfade Zeitkarteninformationen vom Mitarbeiter erfordern, und einige nicht.</span><span class="sxs-lookup"><span data-stu-id="2bf71-369">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="2bf71-370">Szenarien wie diese sind perfekt für implizites lazy loading, weil Daten auf magischeWeise auf einer bedarfsgemäßen Basis angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-370">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="2bf71-371">Das verzögerte Laden, auch als verzögertes Laden bezeichnet, stellt einige Anforderungen an unsere Entitätsobjekte.</span><span class="sxs-lookup"><span data-stu-id="2bf71-371">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="2bf71-372">POCOs mit wahrer Persistenz-Ignoranz würden keine Anforderungen der Persistenzschicht erfüllen, aber wahre Persistenz-Ignoranz ist praktisch unmöglich zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-372">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span><span data-ttu-id="2bf71-373">Stattdessen messen wir Persistenz-Ignoranz in relativen Graden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-373">  Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="2bf71-374">Es wäre bedauerlich, wenn wir von einer Persistenz-orientierten Basisklasse erben oder eine spezialisierte Sammlung verwenden müssten, um ein verzögertes Laden in POCOs zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-374">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="2bf71-375">Glücklicherweise hat EF4 eine weniger aufdringliche Lösung.</span><span class="sxs-lookup"><span data-stu-id="2bf71-375">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="2bf71-376">Praktisch nicht nachweisbar</span><span class="sxs-lookup"><span data-stu-id="2bf71-376">Virtually Undetectable</span></span>

<span data-ttu-id="2bf71-377">Bei Verwendung von POCO-Objekten kann EF4 Laufzeitproxys für Entitäten dynamisch generieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-377">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="2bf71-378">Diese Proxys umschließen die materialisierten POCOs unsichtbar und bieten zusätzliche Dienste, indem sie jeden Sachabnehmer- und -satzvorgang abfangen, um zusätzliche Arbeiten auszuführen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-378">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="2bf71-379">Ein solcher Service ist die faule Ladefunktion, die wir suchen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-379">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="2bf71-380">Ein weiterer Dienst ist ein effizienter Änderungsverfolgungsmechanismus, der aufzeichnen kann, wenn die Anwendung die Eigenschaftswerte einer Entität ändert.</span><span class="sxs-lookup"><span data-stu-id="2bf71-380">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="2bf71-381">Die Liste der Änderungen wird vom ObjectContext während der SaveChanges-Methode verwendet, um alle geänderten Entitäten mithilfe von UPDATE-Befehlen beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-381">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="2bf71-382">Damit diese Proxys funktionieren, benötigen sie jedoch eine Möglichkeit, sich an Eigenschaften zu binden, die Vorgänge für eine Entität abrufen und festlegen, und die Proxys erreichen dieses Ziel, indem sie virtuelle Member überschreiben.</span><span class="sxs-lookup"><span data-stu-id="2bf71-382">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="2bf71-383">Wenn wir also implizites verzögertes Laden und effizientes Änderungstracking haben wollen, müssen wir zu unseren POCO-Klassendefinitionen zurückkehren und Eigenschaften als virtuell markieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-383">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="2bf71-384">Wir können immer noch sagen, dass die Mitarbeiter-Entität meist Ausdauer-Ignoranz ist.</span><span class="sxs-lookup"><span data-stu-id="2bf71-384">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="2bf71-385">Die einzige Anforderung ist die Verwendung virtueller Member, und dies hat keinen Einfluss auf die Testbarkeit des Codes.</span><span class="sxs-lookup"><span data-stu-id="2bf71-385">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="2bf71-386">Wir müssen keine spezielle Basisklasse ableiten oder sogar eine spezielle Kollektion verwenden, die dem verzögerten Laden gewidmet ist.</span><span class="sxs-lookup"><span data-stu-id="2bf71-386">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="2bf71-387">Wie der Code zeigt, ist&lt;&gt; jede Klasse, die ICollection T implementiert, verfügbar, um verwandte Entitäten zu halten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-387">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="2bf71-388">Es gibt auch eine kleine Änderung, die wir innerhalb unserer Arbeitseinheit vornehmen müssen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-388">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="2bf71-389">Lazy Loading ist standardmäßig *deaktiviert,* wenn Sie direkt mit einem ObjectContext-Objekt arbeiten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-389">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="2bf71-390">Es gibt eine Eigenschaft, die wir für die ContextOptions-Eigenschaft festlegen können, um das verzögerte Laden zu ermöglichen, und wir können diese Eigenschaft innerhalb unserer tatsächlichen Arbeitseinheit festlegen, wenn wir das verzögerte Laden überall aktivieren möchten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-390">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

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

<span data-ttu-id="2bf71-391">Wenn implizites verzögertes Laden aktiviert ist, kann Anwendungscode einen Mitarbeiter und die zugehörigen Zeitkarten des Mitarbeiters verwenden, während er sich der Arbeit, die EF zum Laden der zusätzlichen Daten benötigt, glücklicherweise nicht bewusst ist.</span><span class="sxs-lookup"><span data-stu-id="2bf71-391">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="2bf71-392">Lazy Loading erleichtert das Schreiben des Anwendungscodes, und mit der Proxymagie bleibt der Code vollständig testbar.</span><span class="sxs-lookup"><span data-stu-id="2bf71-392">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="2bf71-393">In-Memory-Fakes der Arbeitseinheit können einfach gefälschte Entitäten mit zugehörigen Daten vorladen, wenn sie während eines Tests benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-393">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="2bf71-394">An diesem Punkt wenden wir unsere Aufmerksamkeit von der&lt;Erstellung&gt; von Repositorys mit IObjectSet T und betrachten Abstraktionen, um alle Zeichen des Persistenzframeworks auszublenden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-394">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="2bf71-395">Benutzerdefinierte Repositorys</span><span class="sxs-lookup"><span data-stu-id="2bf71-395">Custom Repositories</span></span>

<span data-ttu-id="2bf71-396">Als wir zum ersten Mal die Arbeitseinheit Design-Muster in diesem Artikel vorgestellt haben, haben wir einige Beispielcode für, wie die Arbeitseinheit aussehen könnte.</span><span class="sxs-lookup"><span data-stu-id="2bf71-396">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="2bf71-397">Lassen Sie uns diese ursprüngliche Idee mithilfe des Zeitkartenszenarios für Mitarbeiter und Mitarbeiter, mit dem wir gearbeitet haben, erneut präsentieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-397">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="2bf71-398">Der Hauptunterschied zwischen dieser Arbeitseinheit und der Arbeitseinheit, die wir im letzten Abschnitt erstellt haben, besteht darin, dass diese&lt;&gt;Arbeitseinheit keine Abstraktionen aus dem EF4-Framework verwendet (es gibt kein IObjectSet T ).</span><span class="sxs-lookup"><span data-stu-id="2bf71-398">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="2bf71-399">IObjectSet&lt;&gt; T funktioniert gut als Repository-Schnittstelle, aber die API, die es verfügbar macht, entspricht möglicherweise nicht perfekt den Anforderungen unserer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="2bf71-399">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="2bf71-400">In diesem kommenden Ansatz werden wir Repositories&lt;&gt; mit einer benutzerdefinierten IRepository T-Abstraktion darstellen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-400">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="2bf71-401">Viele Entwickler, die testgesteuerten Entwurf, verhaltensgesteuertes Design und domänengesteuertes&lt;&gt; Methodendesign befolgen, bevorzugen den IRepository T-Ansatz aus mehreren Gründen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-401">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="2bf71-402">Erstens stellt die&lt;&gt; IRepository T-Schnittstelle eine "Anti-Korruptions"-Schicht dar.</span><span class="sxs-lookup"><span data-stu-id="2bf71-402">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="2bf71-403">Wie Eric Evans in seinem Domain Driven Design-Buch beschrieben, hält eine Anti-Korruptions-Schicht Ihren Domänencode von Infrastruktur-APIs fern, wie eine Persistenz-API.</span><span class="sxs-lookup"><span data-stu-id="2bf71-403">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="2bf71-404">Zweitens können Entwickler Methoden in das Repository erstellen, die die genauen Anforderungen einer Anwendung erfüllen (wie beim Schreiben von Tests entdeckt).</span><span class="sxs-lookup"><span data-stu-id="2bf71-404">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="2bf71-405">Beispielsweise müssen wir häufig eine einzelne Entität mithilfe eines ID-Werts suchen, damit wir der Repository-Schnittstelle eine FindById-Methode hinzufügen können.</span><span class="sxs-lookup"><span data-stu-id="2bf71-405">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span><span data-ttu-id="2bf71-406">Unsere&lt;IRepository&gt; T-Definition sieht wie folgt aus.</span><span class="sxs-lookup"><span data-stu-id="2bf71-406">  Our IRepository&lt;T&gt; definition will look like the following.</span></span>

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

<span data-ttu-id="2bf71-407">Beachten Sie, dass wir die Verwendung&lt;&gt; einer IQueryable T-Schnittstelle zum Verfügbarmachen von Entitätsauflistungen zurücksenden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-407">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="2bf71-408">IQueryable&lt;&gt; T ermöglicht es LINQ-Ausdrucksstrukturen, in den EF4-Anbieter zu fließen und dem Anbieter eine ganzheitliche Ansicht der Abfrage zu geben.</span><span class="sxs-lookup"><span data-stu-id="2bf71-408">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="2bf71-409">Eine zweite Option wäre die Rückgabe&lt;von&gt;IEnumerable T , was bedeutet, dass der EF4-LINQ-Anbieter nur die ausdrücke sieht, die im Repository erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-409">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="2bf71-410">Jede Gruppierung, Reihenfolge und Projektion außerhalb des Repositorys wird nicht in den SQL-Befehl integriert, der an die Datenbank gesendet wird, was die Leistung beeinträchtigen kann.</span><span class="sxs-lookup"><span data-stu-id="2bf71-410">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="2bf71-411">Auf der anderen Seite wird ein Repository,&lt;&gt; das nur IEnumerable T-Ergebnisse zurückgibt, Sie nie mit einem neuen SQL-Befehl überraschen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-411">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="2bf71-412">Beide Ansätze werden funktionieren, und beide Ansätze bleiben testbar.</span><span class="sxs-lookup"><span data-stu-id="2bf71-412">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="2bf71-413">Es ist einfach, eine einzelne Implementierung&lt;der&gt; IRepository T-Schnittstelle mithilfe von Generika und der EF4 ObjectContext-API bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-413">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

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

<span data-ttu-id="2bf71-414">Der&lt;IRepository&gt; T-Ansatz gibt uns zusätzliche Kontrolle über unsere Abfragen, da ein Client eine Methode aufrufen muss, um zu einer Entität zu gelangen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-414">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="2bf71-415">Innerhalb der Methode könnten wir zusätzliche Prüfungen und LINQ-Operatoren bereitstellen, um Anwendungseinschränkungen zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-415">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="2bf71-416">Beachten Sie, dass die Schnittstelle zwei Einschränkungen für den generischen Typparameter enthält.</span><span class="sxs-lookup"><span data-stu-id="2bf71-416">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="2bf71-417">Die erste Einschränkung ist die Von ObjectSet&lt;T&gt;erforderliche Klasse cons taint, und die zweite Einschränkung zwingt unsere Entitäten, IEntity zu implementieren – eine Abstraktion, die für die Anwendung erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="2bf71-417">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="2bf71-418">Die IEntity-Schnittstelle erzwingt Entitäten, über eine lesbare Id-Eigenschaft zu verfügen, und wir können diese Eigenschaft dann in der FindById-Methode verwenden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-418">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="2bf71-419">IEntity wird mit dem folgenden Code definiert.</span><span class="sxs-lookup"><span data-stu-id="2bf71-419">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="2bf71-420">IEntity könnte als eine kleine Verletzung der Persistenz-Ignoranz betrachtet werden, da unsere Entitäten diese Schnittstelle implementieren müssen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-420">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="2bf71-421">Denken Sie daran, Persistenz-Ignoranz ist über Kompromisse, und für viele die FindById-Funktionalität wird die Einschränkung durch die Schnittstelle auferlegt überwiegen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-421">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="2bf71-422">Die Schnittstelle hat keine Auswirkungen auf die Testbarkeit.</span><span class="sxs-lookup"><span data-stu-id="2bf71-422">The interface has no impact on testability.</span></span>

<span data-ttu-id="2bf71-423">Für das Sofortige eines&lt;Live-IRepository T&gt; ist ein EF4 ObjectContext erforderlich, daher sollte eine konkrete Arbeitseinheitsimplementierung die Instanziierung verwalten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-423">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

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

### <a name="using-the-custom-repository"></a><span data-ttu-id="2bf71-424">Verwenden des benutzerdefinierten Repositorys</span><span class="sxs-lookup"><span data-stu-id="2bf71-424">Using the Custom Repository</span></span>

<span data-ttu-id="2bf71-425">Die Verwendung unseres benutzerdefinierten Repositorys unterscheidet sich nicht&lt;&gt;wesentlich von der Verwendung des Repositorys basierend auf IObjectSet T .</span><span class="sxs-lookup"><span data-stu-id="2bf71-425">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="2bf71-426">Anstatt LINQ-Operatoren direkt auf eine Eigenschaft anzuwenden, müssen wir zunächst eine der Methoden&lt;&gt; des Repositorys aufrufen, um einen IQueryable T-Verweis zu abrufen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-426">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="2bf71-427">Beachten Sie, dass der benutzerdefinierte Include-Operator, den wir zuvor implementiert haben, ohne Änderungen funktioniert.</span><span class="sxs-lookup"><span data-stu-id="2bf71-427">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="2bf71-428">Die FindById-Methode des Repositorys entfernt duplizierte Logik aus Aktionen, die versuchen, eine einzelne Entität abzurufen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-428">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="2bf71-429">Es gibt keinen signifikanten Unterschied in der Testbarkeit der beiden Ansätze, die wir untersucht haben.</span><span class="sxs-lookup"><span data-stu-id="2bf71-429">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="2bf71-430">Wir könnten gefälschte Implementierungen&lt;von&gt; IRepository T bereitstellen,&lt;&gt; indem wir Betonklassen erstellen, die von HashSet Employee unterstützt werden - genau wie im letzten Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-430">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="2bf71-431">Einige Entwickler bevorzugen es jedoch, Mock-Objekte und Mock-Objekt-Frameworks zu verwenden, anstatt Fälschungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-431">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="2bf71-432">Wir werden uns mit Mocks befassen, um unsere Implementierung zu testen und die Unterschiede zwischen Mocks und Fakes im nächsten Abschnitt zu diskutieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-432">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="2bf71-433">Testen mit Mocks</span><span class="sxs-lookup"><span data-stu-id="2bf71-433">Testing with Mocks</span></span>

<span data-ttu-id="2bf71-434">Es gibt verschiedene Ansätze, um das aufzubauen, was Martin Fowler ein "Testdouble" nennt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-434">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="2bf71-435">Ein Test-Double (wie ein Film-Stunt-Double) ist ein Objekt, das Sie erstellen, um während der Tests für reale Produktionsobjekte "zu stehen".</span><span class="sxs-lookup"><span data-stu-id="2bf71-435">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="2bf71-436">Die von uns erstellten In-Memory-Repositorys sind Testdoubles für die Repositorys, die mit SQL Server sprechen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-436">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="2bf71-437">Wir haben gesehen, wie diese Test-Doubles während der Komponententests verwendet werden, um Code zu isolieren und Tests schnell laufen zu lassen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-437">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="2bf71-438">Die Test-Doubles, die wir erstellt haben, verfügen über echte, funktionierende Implementierungen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-438">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="2bf71-439">Hinter den Kulissen speichert jeder eine konkrete Sammlung von Objekten, und sie fügen Objekte aus dieser Auflistung hinzu und entfernen sie aus dieser Auflistung, während wir das Repository während eines Tests bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-439">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="2bf71-440">Einige Entwickler bauen ihre Test-Doubles gerne auf diese Weise – mit echtem Code und funktionierenden Implementierungen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-440">Some developers like to build their test doubles this way – with real code and working implementations.</span></span><span data-ttu-id="2bf71-441">Diese Test-Doppel sind, was wir *Fakes*nennen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-441">  These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="2bf71-442">Sie haben funktionierende Implementierungen, aber sie sind nicht real genug für den Produktionseinsatz.</span><span class="sxs-lookup"><span data-stu-id="2bf71-442">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="2bf71-443">Das gefälschte Repository schreibt nicht in die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="2bf71-443">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="2bf71-444">Der gefälschte SMTP-Server sendet keine E-Mail-Nachricht über das Netzwerk.</span><span class="sxs-lookup"><span data-stu-id="2bf71-444">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="2bf71-445">Mocks versus Fakes</span><span class="sxs-lookup"><span data-stu-id="2bf71-445">Mocks versus Fakes</span></span>

<span data-ttu-id="2bf71-446">Es gibt eine andere Art von Test-Double, das als *Mock*bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="2bf71-446">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="2bf71-447">Während Fakes funktionierende Implementierungen haben, kommen Mocks ohne Implementierung.</span><span class="sxs-lookup"><span data-stu-id="2bf71-447">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="2bf71-448">Mit Hilfe eines Mock-Objekt-Frameworks konstruieren wir diese Mock-Objekte zur Laufzeit und verwenden sie als Testdoppel.</span><span class="sxs-lookup"><span data-stu-id="2bf71-448">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="2bf71-449">In diesem Abschnitt verwenden wir das Open-Source-Mocking-Framework Moq.</span><span class="sxs-lookup"><span data-stu-id="2bf71-449">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="2bf71-450">Hier ist ein einfaches Beispiel für die Verwendung von Moq zum dynamischen Erstellen eines Testdoubles für ein Mitarbeiter-Repository.</span><span class="sxs-lookup"><span data-stu-id="2bf71-450">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="2bf71-451">Wir bitten Moq um&lt;&gt; eine IRepository-Mitarbeiterimplementierung, die dynamisch erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="2bf71-451">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="2bf71-452">Wir können auf das Objekt&lt;&gt; zugreifen, das IRepository Employee&lt;&gt; implementiert, indem wir auf die Object-Eigenschaft des Mock T-Objekts zugreifen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-452">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="2bf71-453">Es ist dieses innere Objekt, das wir an unsere Controller übergeben können, und sie werden nicht wissen, ob dies ein Test-Double oder das eigentliche Repository ist.</span><span class="sxs-lookup"><span data-stu-id="2bf71-453">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="2bf71-454">Wir können Methoden für das Objekt aufrufen, genau wie wir Methoden für ein Objekt mit einer realen Implementierung aufrufen würden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-454">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="2bf71-455">Sie müssen sich fragen, was das Mock-Repository tun wird, wenn wir die Add-Methode aufrufen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-455">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="2bf71-456">Da sich hinter dem Mock-Objekt keine Implementierung befindet, unternimmt Add nichts.</span><span class="sxs-lookup"><span data-stu-id="2bf71-456">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="2bf71-457">Es gibt keine konkrete Sammlung hinter den Kulissen, wie wir es mit den Fälschungen hatten, die wir geschrieben haben, also wird der Mitarbeiter verworfen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-457">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="2bf71-458">Was ist mit dem Rückgabewert von FindById?</span><span class="sxs-lookup"><span data-stu-id="2bf71-458">What about the return value of FindById?</span></span> <span data-ttu-id="2bf71-459">In diesem Fall tut das Mock-Objekt das einzige, was es tun kann, d. h. einen Standardwert zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="2bf71-459">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="2bf71-460">Da wir einen Verweistyp (einen Mitarbeiter) zurückgeben, ist der Rückgabewert ein NULL-Wert.</span><span class="sxs-lookup"><span data-stu-id="2bf71-460">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="2bf71-461">Mocks mag wertlos klingen; es gibt jedoch zwei weitere Features von Mocks, über die wir nicht gesprochen haben.</span><span class="sxs-lookup"><span data-stu-id="2bf71-461">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="2bf71-462">Zunächst zeichnet das Moq-Framework alle Aufrufe auf dem Mock-Objekt auf.</span><span class="sxs-lookup"><span data-stu-id="2bf71-462">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="2bf71-463">Später im Code können wir Moq fragen, ob jemand die Add-Methode aufgerufen hat oder ob jemand die FindById-Methode aufgerufen hat.</span><span class="sxs-lookup"><span data-stu-id="2bf71-463">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="2bf71-464">Wir werden später sehen, wie wir diese "Black Box"-Aufnahmefunktion in Tests verwenden können.</span><span class="sxs-lookup"><span data-stu-id="2bf71-464">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="2bf71-465">Die zweite großartige Funktion ist, wie wir Moq verwenden können, um ein Mock-Objekt mit Erwartungen zu *programmieren.*</span><span class="sxs-lookup"><span data-stu-id="2bf71-465">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="2bf71-466">Eine Erwartung sagt dem Mock-Objekt, wie auf eine bestimmte Interaktion zu reagieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-466">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="2bf71-467">Beispielsweise können wir eine Erwartung in unseren Mock programmieren und ihr sagen, dass ein Mitarbeiterobjekt zurückgegeben werden soll, wenn jemand FindById aufruft.</span><span class="sxs-lookup"><span data-stu-id="2bf71-467">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="2bf71-468">Das Moq-Framework verwendet eine Setup-API und Lambda-Ausdrücke, um diese Erwartungen zu programmieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-468">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

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

<span data-ttu-id="2bf71-469">In diesem Beispiel bitten wir Moq, dynamisch ein Repository zu erstellen, und dann programmieren wir das Repository mit einer Erwartung.</span><span class="sxs-lookup"><span data-stu-id="2bf71-469">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="2bf71-470">Die Erwartung weist das Mock-Objekt an, ein neues Mitarbeiterobjekt mit dem ID-Wert 5 zurückzugeben, wenn jemand die FindById-Methode aufruft, die den Wert 5 übergibt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-470">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="2bf71-471">Dieser Test besteht, und wir mussten keine vollständige Implementierung&lt;für&gt;gefälschte IRepository T erstellen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-471">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="2bf71-472">Lassen Sie uns die Tests, die wir zuvor geschrieben haben, noch einmal überprüfen und sie überarbeiten, um Mocks anstelle von Fälschungen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-472">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="2bf71-473">Wie zuvor verwenden wir eine Basisklasse, um die gemeinsamen Komponenten der Infrastruktur einzurichten, die wir für alle Tests des Controllers benötigen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-473">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

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

<span data-ttu-id="2bf71-474">Der Setup-Code bleibt größtenteils derselbe.</span><span class="sxs-lookup"><span data-stu-id="2bf71-474">The setup code remains mostly the same.</span></span> <span data-ttu-id="2bf71-475">Anstatt Fälschungen zu verwenden, verwenden wir Moq, um Mock-Objekte zu konstruieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-475">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="2bf71-476">Die Basisklasse ordnet an, dass die Mock-Arbeitseinheit ein Mock-Repository zurückgibt, wenn Code die Employees-Eigenschaft aufruft.</span><span class="sxs-lookup"><span data-stu-id="2bf71-476">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="2bf71-477">Der Rest des Mock-Setups findet innerhalb der Testvorrichtungen statt, die jedem spezifischen Szenario gewidmet sind.</span><span class="sxs-lookup"><span data-stu-id="2bf71-477">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="2bf71-478">Beispielsweise wird das Mock-Repository vom Testpunkt für die Index-Aktion so eingerichtet, dass eine Liste der Mitarbeiter zurückgegeben wird, wenn die Aktion die FindAll-Methode des Mock-Repositorys aufruft.</span><span class="sxs-lookup"><span data-stu-id="2bf71-478">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

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

<span data-ttu-id="2bf71-479">Abgesehen von den Erwartungen sehen unsere Tests ähnlich aus wie die Tests, die wir vorher hatten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-479">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="2bf71-480">Mit der Aufnahmefähigkeit eines Mock-Frameworks können wir uns jedoch dem Testen aus einem anderen Blickwinkel nähern.</span><span class="sxs-lookup"><span data-stu-id="2bf71-480">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="2bf71-481">Wir werden uns diese neue Perspektive im nächsten Abschnitt ansehen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-481">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="2bf71-482">Status-gegen-Interaktionstests</span><span class="sxs-lookup"><span data-stu-id="2bf71-482">State versus Interaction Testing</span></span>

<span data-ttu-id="2bf71-483">Es gibt verschiedene Techniken, die Sie verwenden können, um Software mit Mock-Objekten zu testen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-483">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="2bf71-484">Ein Ansatz besteht darin, zustandsbasierte Tests zu verwenden, was wir bisher in diesem Papier getan haben.</span><span class="sxs-lookup"><span data-stu-id="2bf71-484">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="2bf71-485">Staatliche Tests machen Behauptungen über den Zustand der Software.</span><span class="sxs-lookup"><span data-stu-id="2bf71-485">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="2bf71-486">Im letzten Test haben wir eine Aktionsmethode auf dem Controller aufgerufen und eine Behauptung über das Modell gemacht, das er erstellen soll.</span><span class="sxs-lookup"><span data-stu-id="2bf71-486">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="2bf71-487">Hier sind einige weitere Beispiele für den Teststatus:</span><span class="sxs-lookup"><span data-stu-id="2bf71-487">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="2bf71-488">Überprüfen Sie, ob das Repository das neue Mitarbeiterobjekt enthält, nachdem Create ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="2bf71-488">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="2bf71-489">Überprüfen Sie, ob das Modell eine Liste aller Mitarbeiter enthält, nachdem Index ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="2bf71-489">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="2bf71-490">Stellen Sie sicher, dass das Repository nach der Ausführung von Delete keinen bestimmten Mitarbeiter enthält.</span><span class="sxs-lookup"><span data-stu-id="2bf71-490">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="2bf71-491">Ein weiterer Ansatz, den Sie bei Mock-Objekten sehen, ist die Überprüfung *von Interaktionen*.</span><span class="sxs-lookup"><span data-stu-id="2bf71-491">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="2bf71-492">Während zustandsbasierte Tests Behauptungen über den Zustand von Objekten machen, macht interaktionsbasiertetests Behauptungen darüber, wie Objekte interagieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-492">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="2bf71-493">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2bf71-493">For example:</span></span>

-   <span data-ttu-id="2bf71-494">Überprüfen Sie, ob der Controller die Add-Methode des Repositorys aufruft, wenn Create ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2bf71-494">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="2bf71-495">Überprüfen Sie, ob der Controller die FindAll-Methode des Repositorys aufruft, wenn Index ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2bf71-495">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="2bf71-496">Überprüfen Sie, ob der Controller die Commit-Methode der Arbeitseinheit aufruft, um Änderungen zu speichern, wenn Bearbeiten ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2bf71-496">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="2bf71-497">Interaktionstests erfordern oft weniger Testdaten, da wir nicht in Sammlungen einstechen und die Anzahl überprüfen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-497">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="2bf71-498">Wenn wir beispielsweise wissen, dass die Aktion Details die FindById-Methode eines Repositorys mit dem richtigen Wert aufruft, verhält sich die Aktion wahrscheinlich korrekt.</span><span class="sxs-lookup"><span data-stu-id="2bf71-498">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="2bf71-499">Wir können dieses Verhalten überprüfen, ohne Testdaten für die Rückgabe von FindById einzurichten.</span><span class="sxs-lookup"><span data-stu-id="2bf71-499">We can verify this behavior without setting up any test data to return from FindById.</span></span>

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

<span data-ttu-id="2bf71-500">Das einzige Setup, das in der obigen Prüfvorrichtung erforderlich ist, ist das Setup, das von der Basisklasse bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="2bf71-500">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="2bf71-501">Wenn wir die Controller-Aktion aufrufen, zeichnet Moq die Interaktionen mit dem Mock-Repository auf.</span><span class="sxs-lookup"><span data-stu-id="2bf71-501">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="2bf71-502">Mithilfe der Verify-API von Moq können wir Moq fragen, ob der Controller FindById mit dem richtigen ID-Wert aufgerufen hat.</span><span class="sxs-lookup"><span data-stu-id="2bf71-502">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="2bf71-503">Wenn der Controller die Methode nicht aufgerufen oder die Methode mit einem unerwarteten Parameterwert aufgerufen hat, löst die Verify-Methode eine Ausnahme aus, und der Test schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="2bf71-503">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="2bf71-504">Hier ist ein weiteres Beispiel zum Überprüfen, ob die Aktion Erstellen Commit für die aktuelle Arbeitseinheit aufruft.</span><span class="sxs-lookup"><span data-stu-id="2bf71-504">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="2bf71-505">Eine Gefahr bei Interaktionstests ist die Tendenz, Wechselwirkungen zu überspezifizieren.</span><span class="sxs-lookup"><span data-stu-id="2bf71-505">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="2bf71-506">Die Fähigkeit des Mockobjekts, jede Interaktion mit dem Mockobjekt aufzuzeichnen und zu überprüfen, bedeutet nicht, dass der Test versuchen sollte, jede Interaktion zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-506">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="2bf71-507">Einige Interaktionen sind Implementierungsdetails, und Sie sollten nur die Interaktionen überprüfen, die *erforderlich* sind, um den aktuellen Test zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-507">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="2bf71-508">Die Wahl zwischen Mocks oder Fakes hängt weitgehend von dem System ab, das Sie testen, und Von Ihren persönlichen (oder Team-)Präferenzen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-508">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="2bf71-509">Mock-Objekte können die Menge an Code, die Sie zum Implementieren von Testdoubles benötigen, drastisch reduzieren, aber nicht jeder ist mit komfortablen Programmiererwartungen und der Überprüfung von Interaktionen zufrieden.</span><span class="sxs-lookup"><span data-stu-id="2bf71-509">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="2bf71-510">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="2bf71-510">Conclusions</span></span>

<span data-ttu-id="2bf71-511">In diesem Artikel haben wir mehrere Ansätze zum Erstellen von testbarem Code bei der Verwendung des ADO.NET Entity Framework für die Datenpersistenz demonstriert.</span><span class="sxs-lookup"><span data-stu-id="2bf71-511">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="2bf71-512">Wir können integrierte Abstraktionen wie&lt;IObjectSet&gt;T nutzen oder unsere&lt;&gt;eigenen Abstraktionen wie IRepository T erstellen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-512">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span><span data-ttu-id="2bf71-513">In beiden Fällen ermöglicht die POCO-Unterstützung im ADO.NET Entity Framework 4.0 den Verbrauchern dieser Abstraktionen, beharrlich ignorant und hochgradig testbar zu bleiben.</span><span class="sxs-lookup"><span data-stu-id="2bf71-513">  In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="2bf71-514">Zusätzliche EF4-Funktionen wie das implizite lazy loading ermöglichen es Geschäfts- und Anwendungsdienstcode, ohne sich um die Details eines relationalen Datenspeichers kümmern zu müssen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-514">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="2bf71-515">Schließlich sind die Abstraktionen, die wir erstellen, einfach zu verspotten oder zu fälschen innerhalb von Komponententests, und wir können diese Test-Doubles verwenden, um schnell laufende, stark isolierte und zuverlässige Tests zu erreichen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-515">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="2bf71-516">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2bf71-516">Additional Resources</span></span>

-   <span data-ttu-id="2bf71-517">Robert C. Martin, " [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)"</span><span class="sxs-lookup"><span data-stu-id="2bf71-517">Robert C. Martin, “ [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="2bf71-518">Martin Fowler, [Katalog der Muster](https://www.martinfowler.com/eaaCatalog/index.html) aus *Mustern der Unternehmensanwendungsarchitektur*</span><span class="sxs-lookup"><span data-stu-id="2bf71-518">Martin Fowler, [Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="2bf71-519">Griffin Caprio, " [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)"</span><span class="sxs-lookup"><span data-stu-id="2bf71-519">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="2bf71-520">Data Programmability Blog, " [Walkthrough: Test Driven Development with the Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)".</span><span class="sxs-lookup"><span data-stu-id="2bf71-520">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)”.</span></span>
-   <span data-ttu-id="2bf71-521">Data Programmability Blog, " [Using Repository and Unit of Work patterns with Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"</span><span class="sxs-lookup"><span data-stu-id="2bf71-521">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)”</span></span>
-   <span data-ttu-id="2bf71-522">Aaron Jensen, " [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"</span><span class="sxs-lookup"><span data-stu-id="2bf71-522">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="2bf71-523">Eric Lee, [BDD mit MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)</span><span class="sxs-lookup"><span data-stu-id="2bf71-523">Eric Lee, “ [BDD with MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)”</span></span>
-   <span data-ttu-id="2bf71-524">Eric Evans, " [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"</span><span class="sxs-lookup"><span data-stu-id="2bf71-524">Eric Evans, “ [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="2bf71-525">Martin Fowler, " [Mocks Aren't Stubs](https://martinfowler.com/articles/mocksArentStubs.html)"</span><span class="sxs-lookup"><span data-stu-id="2bf71-525">Martin Fowler, “ [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="2bf71-526">Martin Fowler, Test [Double](https://martinfowler.com/bliki/TestDouble.html)</span><span class="sxs-lookup"><span data-stu-id="2bf71-526">Martin Fowler, “ [Test Double](https://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   [<span data-ttu-id="2bf71-527">Moq</span><span class="sxs-lookup"><span data-stu-id="2bf71-527">Moq</span></span>](https://code.google.com/p/moq/)

### <a name="biography"></a><span data-ttu-id="2bf71-528">Biographie</span><span class="sxs-lookup"><span data-stu-id="2bf71-528">Biography</span></span>

<span data-ttu-id="2bf71-529">Scott Allen ist Mitglied des technischen Personals bei Pluralsight und Gründer von OdeToCode.com.</span><span class="sxs-lookup"><span data-stu-id="2bf71-529">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="2bf71-530">In 15 Jahren kommerzieller Softwareentwicklung hat Scott an Lösungen für alles gearbeitet, von 8-Bit-Embedded-Geräten bis hin zu hochskalierbaren ASP.NET Webanwendungen.</span><span class="sxs-lookup"><span data-stu-id="2bf71-530">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="2bf71-531">Sie erreichen Scott über seinen Blog bei OdeToCode oder auf Twitter unter [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span><span class="sxs-lookup"><span data-stu-id="2bf71-531">You can reach Scott on his blog at OdeToCode, or on Twitter at [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span></span>
