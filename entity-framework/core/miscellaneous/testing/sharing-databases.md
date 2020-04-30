---
title: Gemeinsame Nutzung von Datenbanken zwischen Tests-EF Core
description: Beispiel, das zeigt, wie eine Datenbank zwischen mehreren Tests gemeinsam genutzt wird
author: ajcvickers
ms.date: 04/25/2020
uid: core/miscellaneous/testing/sharing-databases
ms.openlocfilehash: 96216fb8afd6916402637de3ffab04b79d37e11c
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564253"
---
# <a name="sharing-databases-between-tests"></a><span data-ttu-id="40ac8-103">Freigeben von Datenbanken zwischen Tests</span><span class="sxs-lookup"><span data-stu-id="40ac8-103">Sharing databases between tests</span></span>

<span data-ttu-id="40ac8-104">Im [Beispiel für die EF Core Tests](xref:core/miscellaneous/testing/testing-sample) wurde gezeigt, wie Sie Anwendungen mit verschiedenen Datenbanksystemen testen.</span><span class="sxs-lookup"><span data-stu-id="40ac8-104">The [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) showed how to test applications against different database systems.</span></span>
<span data-ttu-id="40ac8-105">In diesem Beispiel hat jeder Test eine neue Datenbank erstellt.</span><span class="sxs-lookup"><span data-stu-id="40ac8-105">For that sample, each test created a new database.</span></span>
<span data-ttu-id="40ac8-106">Dies ist ein gutes Muster bei der Verwendung von SQLite oder der EF-in-Memory Database, aber es kann einen erheblichen mehr Aufwand bei der Verwendung anderer Datenbanksysteme bedeuten.</span><span class="sxs-lookup"><span data-stu-id="40ac8-106">This is a good pattern when using SQLite or the EF in-memory database, but it can involve significant overhead when using other database systems.</span></span>

<span data-ttu-id="40ac8-107">Dieses Beispiel baut auf dem vorherigen Beispiel auf, indem die Daten Bank Erstellung in eine Test Fixierung verschoben wird.</span><span class="sxs-lookup"><span data-stu-id="40ac8-107">This sample builds on the previous sample by moving database creation into a test fixture.</span></span>
<span data-ttu-id="40ac8-108">Dadurch kann eine einzelne SQL Server Datenbank erstellt und nur einmal für alle Tests durchsucht werden.</span><span class="sxs-lookup"><span data-stu-id="40ac8-108">This allows a single SQL Server database to be created and seeded only once for all tests.</span></span>

> [!TIP]
> <span data-ttu-id="40ac8-109">Stellen Sie sicher, dass Sie das [Beispiel für EF Core Tests](xref:core/miscellaneous/testing/testing-sample) durcharbeiten, bevor Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="40ac8-109">Make sure to work through the [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) before continuing here.</span></span>

<span data-ttu-id="40ac8-110">Es ist nicht schwierig, mehrere Tests für dieselbe Datenbank zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="40ac8-110">It's not difficult to write multiple tests against the same database.</span></span>
<span data-ttu-id="40ac8-111">Der Trick besteht darin, dass die Tests nicht aufeinander verlaufen, während Sie ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="40ac8-111">The trick is doing it in a way that the tests don't trip over each other as they run.</span></span>
<span data-ttu-id="40ac8-112">Dies erfordert Folgendes:</span><span class="sxs-lookup"><span data-stu-id="40ac8-112">This requires understanding:</span></span>
* <span data-ttu-id="40ac8-113">Sicheres Freigeben von Objekten zwischen Tests</span><span class="sxs-lookup"><span data-stu-id="40ac8-113">How to safely share objects between tests</span></span>
* <span data-ttu-id="40ac8-114">Wenn das Test Framework Tests parallel ausführt</span><span class="sxs-lookup"><span data-stu-id="40ac8-114">When the test framework runs tests in parallel</span></span>
* <span data-ttu-id="40ac8-115">So halten Sie die Datenbank für jeden Test in einem sauberen Zustand</span><span class="sxs-lookup"><span data-stu-id="40ac8-115">How to keep the database in a clean state for every test</span></span>  

## <a name="the-fixture"></a><span data-ttu-id="40ac8-116">Die Fixierung</span><span class="sxs-lookup"><span data-stu-id="40ac8-116">The fixture</span></span>

<span data-ttu-id="40ac8-117">Wir verwenden eine Test Fixierung für die Freigabe von Objekten zwischen Tests.</span><span class="sxs-lookup"><span data-stu-id="40ac8-117">We will use a test fixture for sharing objects between tests.</span></span>
<span data-ttu-id="40ac8-118">In der [xUnit-Dokumentation](https://xunit.net/docs/shared-context.html) wird angegeben, dass eine Fixierung verwendet werden soll: "Wenn Sie einen einzelnen Test Kontext erstellen und für alle Tests in der Klasse freigeben möchten, und nachdem alle Tests in der Klasse abgeschlossen wurden."</span><span class="sxs-lookup"><span data-stu-id="40ac8-118">The [XUnit documentation](https://xunit.net/docs/shared-context.html) states that a fixture should be used "when you want to create a single test context and share it among all the tests in the class, and have it cleaned up after all the tests in the class have finished."</span></span>

> [!TIP]
> <span data-ttu-id="40ac8-119">In diesem Beispiel wird [xUnit](https://xunit.net/)verwendet, aber ähnliche Konzepte sind in anderen Test-Frameworks, einschließlich [nunit](https://nunit.org/), vorhanden.</span><span class="sxs-lookup"><span data-stu-id="40ac8-119">This sample uses [XUnit](https://xunit.net/), but similar concepts exist in other testing frameworks, including [NUnit](https://nunit.org/).</span></span>

<span data-ttu-id="40ac8-120">Dies bedeutet, dass wir die Daten Bank Erstellung und das Seeding in eine Fixierungs Klasse verschieben müssen.</span><span class="sxs-lookup"><span data-stu-id="40ac8-120">This means that we need to move database creation and seeding to a fixture class.</span></span>
<span data-ttu-id="40ac8-121">Hier sehen Sie, wie es aussieht:</span><span class="sxs-lookup"><span data-stu-id="40ac8-121">Here's what it looks like:</span></span>

[!code-csharp[SharedDatabaseFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseFixture.cs?name=SharedDatabaseFixture)]

<span data-ttu-id="40ac8-122">Beachten Sie vorerst, wie der Konstruktor lautet:</span><span class="sxs-lookup"><span data-stu-id="40ac8-122">For now, notice how the constructor:</span></span>
* <span data-ttu-id="40ac8-123">Erstellt eine einzelne Datenbankverbindung für die Lebensdauer der Fixierung.</span><span class="sxs-lookup"><span data-stu-id="40ac8-123">Creates a single database connection for the lifetime of the fixture</span></span>
* <span data-ttu-id="40ac8-124">Erstellt und erstellt die Datenbank durch Aufrufen der `Seed` -Methode.</span><span class="sxs-lookup"><span data-stu-id="40ac8-124">Creates and seeds that database by calling the `Seed` method</span></span> 

<span data-ttu-id="40ac8-125">Sperre vorerst ignorieren; Wir werden später darauf zurückkommen.</span><span class="sxs-lookup"><span data-stu-id="40ac8-125">Ignore the locking for now; we will come back to it later.</span></span>

> [!TIP]
> <span data-ttu-id="40ac8-126">Der Erstellungs-und Seeding Code muss nicht Async sein.</span><span class="sxs-lookup"><span data-stu-id="40ac8-126">The creation and seeding code does not need to be async.</span></span>
> <span data-ttu-id="40ac8-127">Die asynchrone Erstellung erschwert den Code und führt nicht zu einer Verbesserung der Leistung oder des Durchsatzes von Tests.</span><span class="sxs-lookup"><span data-stu-id="40ac8-127">Making it async will complicate the code and will not improve performance or throughput of tests.</span></span>

<span data-ttu-id="40ac8-128">Die Datenbank wird erstellt, indem zuerst eine vorhandene Datenbank gelöscht und dann eine neue Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="40ac8-128">The database is created by first deleting any existing database and then creating a new database.</span></span>
<span data-ttu-id="40ac8-129">Dadurch wird sichergestellt, dass die Datenbank mit dem aktuellen EF-Modell übereinstimmt, auch wenn Sie seit dem letzten Testlauf geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="40ac8-129">This ensures that the database matches the current EF model even if it has been changed since the last test run.</span></span>

> [!TIP]
> <span data-ttu-id="40ac8-130">Es kann schneller sein, die vorhandene Datenbank zu "bereinigen", indem Sie etwas ähnliches wie das Erstellen von [Vorgängen](https://jimmybogard.com/tag/respawn/) verwendet, anstatt Sie jedes Mal neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="40ac8-130">It can be faster to "clean" the existing database using something like [respawn](https://jimmybogard.com/tag/respawn/) rather than re-create it each time.</span></span>
> <span data-ttu-id="40ac8-131">Es muss jedoch darauf geachtet werden, dass das Datenbankschema mit dem EF-Modell auf dem neuesten Stand ist.</span><span class="sxs-lookup"><span data-stu-id="40ac8-131">However, care must be taken to ensure that the database schema is up-to-date with the EF model when doing this.</span></span>

<span data-ttu-id="40ac8-132">Die Datenbankverbindung wird verworfen, wenn die Fixierung verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="40ac8-132">The database connection is disposed when the fixture is disposed.</span></span>
<span data-ttu-id="40ac8-133">Sie können auch das Löschen der Testdatenbank an dieser Stelle in Erwägung gezogen.</span><span class="sxs-lookup"><span data-stu-id="40ac8-133">You may also consider deleting the test database at this point.</span></span>
<span data-ttu-id="40ac8-134">Dies erfordert jedoch zusätzliche Sperren und eine Verweis Zählung, wenn die Fixierung von mehreren Test Klassen gemeinsam genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="40ac8-134">However, this will require additional locking and reference counting if the fixture is being shared by multiple test classes.</span></span>
<span data-ttu-id="40ac8-135">Außerdem ist es häufig hilfreich, dass die Testdatenbank weiterhin zum Debuggen von fehlgeschlagenen Tests verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="40ac8-135">Also, it is often useful to have the test database still available for debugging failed tests.</span></span>  

## <a name="using-the-fixture"></a><span data-ttu-id="40ac8-136">Verwenden der Fixierung</span><span class="sxs-lookup"><span data-stu-id="40ac8-136">Using the fixture</span></span>

<span data-ttu-id="40ac8-137">XUnit hat ein gängiges Muster zum Zuordnen einer Test Fixierung zu einer Klasse von Tests:</span><span class="sxs-lookup"><span data-stu-id="40ac8-137">XUnit has a common pattern for associating a test fixture with a class of tests:</span></span>

[!code-csharp[UsingTheFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=UsingTheFixture)]

<span data-ttu-id="40ac8-138">XUnit erstellt jetzt eine einzelne Fixierungs Instanz und übergibt sie an jede Instanz der Testklasse.</span><span class="sxs-lookup"><span data-stu-id="40ac8-138">XUnit will now create a single fixture instance and pass it to each instance of the test class.</span></span>
<span data-ttu-id="40ac8-139">(Beachten Sie das erste [Testbeispiel](xref:core/miscellaneous/testing/testing-sample) , dass xUnit bei jeder Ausführung eines Tests eine neue Test Klasseninstanz erstellt.) Dies bedeutet, dass die Datenbank einmal erstellt und ein Seeding erstellt wird, und dann wird jeder Test diese Datenbank verwenden.</span><span class="sxs-lookup"><span data-stu-id="40ac8-139">(Remember from the first [testing sample](xref:core/miscellaneous/testing/testing-sample) that XUnit creates a new test class instance every time it runs a test.) This means that the database will be created and seeded once and then each test will use this database.</span></span>

<span data-ttu-id="40ac8-140">Beachten Sie, dass Tests in einer einzelnen Klasse nicht parallel ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="40ac8-140">Note that tests within a single class will not be run in parallel.</span></span>
<span data-ttu-id="40ac8-141">Dies bedeutet, dass es für jeden Test sicher ist, dieselbe Datenbankverbindung zu verwenden, `DbConnection` auch wenn das Objekt nicht Thread sicher ist.</span><span class="sxs-lookup"><span data-stu-id="40ac8-141">This means it is safe for each test to use the same database connection, even though the `DbConnection` object is not thread-safe.</span></span>

## <a name="maintaining-database-state"></a><span data-ttu-id="40ac8-142">Verwalten des Daten Bank Status</span><span class="sxs-lookup"><span data-stu-id="40ac8-142">Maintaining database state</span></span>

<span data-ttu-id="40ac8-143">Tests müssen häufig die Testdaten durch Einfügungen, Updates und Löschungen mutieren.</span><span class="sxs-lookup"><span data-stu-id="40ac8-143">Tests often need to mutate the test data with inserts, updates, and deletes.</span></span>
<span data-ttu-id="40ac8-144">Diese Änderungen wirken sich jedoch auf andere Tests aus, bei denen eine saubere, Seeding Datenbank erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="40ac8-144">But these changes will then impact other tests which are expecting a clean, seeded database.</span></span>

<span data-ttu-id="40ac8-145">Dies kann durch Ausführen von veränderenden Tests innerhalb einer Transaktion behoben werden.</span><span class="sxs-lookup"><span data-stu-id="40ac8-145">This can be dealt with by running mutating tests inside a transaction.</span></span>
<span data-ttu-id="40ac8-146">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="40ac8-146">For example:</span></span>

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=CanAddItem)]

<span data-ttu-id="40ac8-147">Beachten Sie, dass die Transaktion beim Start des Tests erstellt und verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="40ac8-147">Notice that the transaction is created as the test starts and disposed when it is finished.</span></span>
<span data-ttu-id="40ac8-148">Das Verwerfen der Transaktion bewirkt, dass ein Rollback ausgeführt wird, sodass keine der Änderungen von anderen Tests erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="40ac8-148">Disposing the transaction causes it to be rolled back, so none of the changes will be seen by other tests.</span></span>

<span data-ttu-id="40ac8-149">Die Hilfsmethode zum Erstellen eines Kontexts (siehe den obigen Fixierungs Code) akzeptiert diese Transaktion und optet den dbcontext in der Verwendung.</span><span class="sxs-lookup"><span data-stu-id="40ac8-149">The helper method for creating a context (see the fixture code above) accepts this transaction and opts the DbContext into using it.</span></span> 

## <a name="sharing-the-fixture"></a><span data-ttu-id="40ac8-150">Freigeben der Fixierung</span><span class="sxs-lookup"><span data-stu-id="40ac8-150">Sharing the fixture</span></span>

<span data-ttu-id="40ac8-151">Sie haben vielleicht bemerkt, dass Sie den Code für die Erstellung und das Seeding der Datenbank</span><span class="sxs-lookup"><span data-stu-id="40ac8-151">You may have noticed locking code around database creation and seeding.</span></span>
<span data-ttu-id="40ac8-152">Dies ist für dieses Beispiel nicht erforderlich, da nur eine Klasse von Tests die Fixierung verwendet, sodass nur eine einzige Fixierungs Instanz erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="40ac8-152">This is not needed for this sample since only one class of tests use the fixture, so only a single fixture instance is created.</span></span>

<span data-ttu-id="40ac8-153">Möglicherweise möchten Sie jedoch dieselbe Fixierung mit mehreren Test Klassen verwenden.</span><span class="sxs-lookup"><span data-stu-id="40ac8-153">However, you may want to use the same fixture with multiple classes of tests.</span></span>
<span data-ttu-id="40ac8-154">XUnit erstellt eine Fixierungs Instanz für jede dieser Klassen.</span><span class="sxs-lookup"><span data-stu-id="40ac8-154">XUnit will create one fixture instance for each of these classes.</span></span>
<span data-ttu-id="40ac8-155">Diese können von verschiedenen Threads verwendet werden, die Tests parallel ausführen.</span><span class="sxs-lookup"><span data-stu-id="40ac8-155">These may be used by different threads running tests in parallel.</span></span>
<span data-ttu-id="40ac8-156">Daher ist es wichtig, dass Sie über eine geeignete Sperre verfügen, um sicherzustellen, dass nur ein Thread die Erstellung und das Seeding der Datenbank übernimmt</span><span class="sxs-lookup"><span data-stu-id="40ac8-156">Therefore, it is important to have appropriate locking to ensure only one thread does the database creation and seeding.</span></span>

> [!TIP]
> <span data-ttu-id="40ac8-157">Eine einfache `lock` ist in Ordnung.</span><span class="sxs-lookup"><span data-stu-id="40ac8-157">A simple `lock` is fine here.</span></span>
> <span data-ttu-id="40ac8-158">Es ist nicht erforderlich, etwas komplexeres zu versuchen, wie z. b. sperrfreie Muster.</span><span class="sxs-lookup"><span data-stu-id="40ac8-158">There is no need to attempt anything more complex, such as any lock-free patterns.</span></span>
