---
title: 'Testen von Code, der EF Core verwendet: EF Core'
description: Unterschiedliche Ansätze zum Testen von Anwendungen, die Entity Framework Core verwenden
author: ajcvickers
ms.date: 04/22/2020
uid: core/testing/index
ms.openlocfilehash: d94c40bdb1082473ee88e7c3c1fbfecee90ba4ff
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129069"
---
# <a name="testing-code-that-uses-ef-core"></a><span data-ttu-id="25ba8-103">Testen von Code, der EF Core verwendet</span><span class="sxs-lookup"><span data-stu-id="25ba8-103">Testing code that uses EF Core</span></span>

<span data-ttu-id="25ba8-104">Das Testen von Code, der auf eine Datenbank zugreift, erfordert Folgendes:</span><span class="sxs-lookup"><span data-stu-id="25ba8-104">Testing code that accesses a database requires either:</span></span>

* <span data-ttu-id="25ba8-105">Das Ausführen von Abfragen und Aktualisierungen in dem Datenbanksystem, das auch in der Produktion verwendet wird, oder</span><span class="sxs-lookup"><span data-stu-id="25ba8-105">Running queries and updates against the same database system used in production.</span></span>
* <span data-ttu-id="25ba8-106">Das Ausführen von Abfragen und Aktualisierungen in einem andere einfacher zu verwaltenden Datenbanksystem oder</span><span class="sxs-lookup"><span data-stu-id="25ba8-106">Running queries and updates against some other easier to manage database system.</span></span>
* <span data-ttu-id="25ba8-107">Das Verwenden von Testdoubles oder eines anderen Mechanismus, um die Verwendung einer Datenbank grundsätzlich zu vermeiden</span><span class="sxs-lookup"><span data-stu-id="25ba8-107">Using test doubles or some other mechanism to avoid using a database at all.</span></span>

<span data-ttu-id="25ba8-108">In diesem Dokument werden die Abwägungen dargelegt, die mit jeder dieser Optionen verbunden sind, und es wird gezeigt, wie EF Core bei jedem Ansatz eingesetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="25ba8-108">This document outlines the trade-offs involved in each of these choices and shows how EF Core can be used with each approach.</span></span>

> [!TIP]
> <span data-ttu-id="25ba8-109">Informationen zu den hier vorgestellten Konzepten finden Sie unter [EF Core-Testbeispiel](xref:core/testing/testing-sample).</span><span class="sxs-lookup"><span data-stu-id="25ba8-109">See [EF Core testing sample](xref:core/testing/testing-sample) for code demonstrating the concepts introduced here.</span></span>

## <a name="all-database-providers-are-not-equal"></a><span data-ttu-id="25ba8-110">Alle Datenbankanbieter sind nicht gleich</span><span class="sxs-lookup"><span data-stu-id="25ba8-110">All database providers are not equal</span></span>

<span data-ttu-id="25ba8-111">Es ist sehr wichtig zu verstehen, dass EF Core nicht darauf ausgelegt ist, jeden Aspekt des zugrunde liegenden Datenbanksystems zu abstrahieren.</span><span class="sxs-lookup"><span data-stu-id="25ba8-111">It is very important to understand that EF Core is not designed to abstract every aspect of the underlying database system.</span></span>
<span data-ttu-id="25ba8-112">Stattdessen handelt es sich bei EF Core um einen einheitlichen Satz von Mustern und Konzepten, der bei jedem Datenbanksystem verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="25ba8-112">Instead, EF Core is a common set of patterns and concepts that can be used with any database system.</span></span>
<span data-ttu-id="25ba8-113">EF Core-Datenbankanbieter schichten dann datenbankspezifisches Verhalten und Funktionalität über diesem allgemeinen Framework.</span><span class="sxs-lookup"><span data-stu-id="25ba8-113">EF Core database providers then layer database-specific behavior and functionality over this common framework.</span></span>
<span data-ttu-id="25ba8-114">Auf diese Weise kann jedes Datenbanksystem das tun, was es am besten kann, während gleichzeitig die Gemeinsamkeiten mit anderen Datenbanksystemen, falls angebracht, gewahrt bleiben.</span><span class="sxs-lookup"><span data-stu-id="25ba8-114">This allows each database system to do what it does best while still maintaining commonality, where appropriate, with other database systems.</span></span>

<span data-ttu-id="25ba8-115">Grundsätzlich bedeutet dies, dass ein Austauschen des Datenbankanbieters das Verhalten von EF Core verändert, und es kann nicht erwartet werden, dass die Anwendung ordnungsgemäß funktioniert, wenn sie nicht explizit alle Unterschiede im Verhalten berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="25ba8-115">Fundamentally, this means that switching out the database provider will change EF Core behavior and the application can't be expected to function correctly unless it explicitly accounts for any differences in behavior.</span></span>
<span data-ttu-id="25ba8-116">Dennoch wird dies in vielen Fällen funktionieren, weil es ein hohes Maß an Gemeinsamkeiten unter relationalen Datenbanken gibt.</span><span class="sxs-lookup"><span data-stu-id="25ba8-116">That being said, in many cases doing this will work because there is a high degree of commonality amongst relational databases.</span></span>
<span data-ttu-id="25ba8-117">Das ist gut und schlecht zugleich.</span><span class="sxs-lookup"><span data-stu-id="25ba8-117">This is good and bad.</span></span>
<span data-ttu-id="25ba8-118">Gut, da das Wechseln zwischen Datenbanksystemen relativ einfach erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="25ba8-118">Good because moving between database systems can be relatively easy.</span></span>
<span data-ttu-id="25ba8-119">Schlecht, weil es ein falsches Gefühl der Sicherheit vermitteln kann, wenn die Anwendung nicht vollständig mit dem neuen Datenbanksystem getestet wird.</span><span class="sxs-lookup"><span data-stu-id="25ba8-119">Bad because it can give a false sense of security if the application is not fully tested against the new database system.</span></span>

## <a name="approach-1-production-database-system"></a><span data-ttu-id="25ba8-120">Ansatz 1: Produktions-Datenbanksystem</span><span class="sxs-lookup"><span data-stu-id="25ba8-120">Approach 1: Production database system</span></span>

<span data-ttu-id="25ba8-121">Wie im vorigen Abschnitt beschrieben, ist die einzige Möglichkeit, sicher zu sein, dass das getestet wird, was in der Produktion ausgeführt wird, die Verwendung desselben Datenbanksystems.</span><span class="sxs-lookup"><span data-stu-id="25ba8-121">As described in the previous section, the only way to be sure you are testing what runs in production is to use the same database system.</span></span>
<span data-ttu-id="25ba8-122">Wenn die bereitgestellte Anwendung beispielsweise SQL Azure verwendet, müssen die Tests auch mit SQL Azure erfolgen.</span><span class="sxs-lookup"><span data-stu-id="25ba8-122">For example, if the deployed application uses SQL Azure, then testing should also be done against SQL Azure.</span></span>

<span data-ttu-id="25ba8-123">Allerdings wäre es sowohl langsam als auch teuer, wenn jeder Entwickler Tests mit SQL Azure durchführen würde, während er aktiv am Code arbeitet.</span><span class="sxs-lookup"><span data-stu-id="25ba8-123">However, having every developer run tests against SQL Azure while actively working on the code would be both slow and expensive.</span></span>
<span data-ttu-id="25ba8-124">Dies veranschaulicht den wesentlichen Haken bei diesen Ansätzen: Wann ist es angebracht, vom Produktions-Datenbanksystem abzuweichen, um die Testeffizienz zu verbessern?</span><span class="sxs-lookup"><span data-stu-id="25ba8-124">This illustrates the main trade-off involved throughout these approaches: when is it appropriate to deviate from the production database system so as to improve test efficiency?</span></span>

<span data-ttu-id="25ba8-125">Glücklicherweise ist die Antwort in diesem Fall recht einfach, nämlich das Verwenden einer lokal SQL Server-Instanz für Entwicklertests.</span><span class="sxs-lookup"><span data-stu-id="25ba8-125">Luckily, in this case the answer is quite easy: use local or on-premises SQL Server for developer testing.</span></span>
<span data-ttu-id="25ba8-126">SQL Azure und SQL Server sind sehr ähnlich, sodass das Testen mit SQL Server in der Regel ein angemessener Kompromiss ist.</span><span class="sxs-lookup"><span data-stu-id="25ba8-126">SQL Azure and SQL Server are extremely similar, so testing against SQL Server is usually a reasonable trade-off.</span></span>
<span data-ttu-id="25ba8-127">Dennoch ist es ratsam, vor dem Aufnehmen der Produktion Tests mit SQL Azure selbst durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="25ba8-127">That being said, it is still wise to run tests against SQL Azure itself before going into production.</span></span>

### <a name="localdb"></a><span data-ttu-id="25ba8-128">LocalDB</span><span class="sxs-lookup"><span data-stu-id="25ba8-128">LocalDB</span></span>

<span data-ttu-id="25ba8-129">Alle wichtigen Datenbanksysteme verfügen über eine Form von „Developer Edition“ für lokales Testen.</span><span class="sxs-lookup"><span data-stu-id="25ba8-129">All the major database systems have some form of "Developer Edition" for local testing.</span></span>
<span data-ttu-id="25ba8-130">Bei SQL Server heißt dieses Feature [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="25ba8-130">SQL Server also has a feature called [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb).</span></span>
<span data-ttu-id="25ba8-131">Der Hauptvorteil von LocalDB besteht darin, dass die Datenbankinstanz bei Bedarf hochgefahren wird.</span><span class="sxs-lookup"><span data-stu-id="25ba8-131">The primary advantage of LocalDB is that it spins up the database instance on demand.</span></span>
<span data-ttu-id="25ba8-132">Dadurch wird vermieden, dass ein Datenbankdienst auf Ihrem Computer läuft, auch wenn gerade keine Tests ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="25ba8-132">This avoids having a database service running on your machine even when you're not running tests.</span></span>

<span data-ttu-id="25ba8-133">LocalDB ist nicht ohne Probleme:</span><span class="sxs-lookup"><span data-stu-id="25ba8-133">LocalDB is not without its issues:</span></span>

* <span data-ttu-id="25ba8-134">Sie unterstützt nicht alles, was von [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-version-15?view=sql-server-ver15&preserve-view=true) unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="25ba8-134">It doesn't support everything that [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-version-15?view=sql-server-ver15&preserve-view=true) does.</span></span>
* <span data-ttu-id="25ba8-135">Sie ist unter Linux nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="25ba8-135">It isn't available on Linux.</span></span>
* <span data-ttu-id="25ba8-136">Sie kann beim ersten Testlauf Verzögerungen verursachen, wenn der Dienst hochgefahren wird.</span><span class="sxs-lookup"><span data-stu-id="25ba8-136">It can cause lag on first test run as the service is spun up.</span></span>

<span data-ttu-id="25ba8-137">Ich persönlich habe es nie als Problem empfunden, dass ein Datenbankdienst auf meinem Entwicklungscomputer läuft, und würde generell empfehlen, stattdessen die Developer Edition zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="25ba8-137">Personally, I've never found it a problem having a database service running on my dev machine and I would generally recommend using Developer Edition instead.</span></span>
<span data-ttu-id="25ba8-138">Allerdings kann LocalDB für einige Entwickler geeignet sein, insbesondere auf weniger leistungsfähigen Entwicklungscomputern.</span><span class="sxs-lookup"><span data-stu-id="25ba8-138">However, LocalDB may be appropriate for some people, especially on less powerful dev machines.</span></span>

<span data-ttu-id="25ba8-139">Das [Ausführen von SQL Server](/sql/linux/quickstart-install-connect-docker) (oder einem anderen Datenbanksystem) in einem Docker-Container (oder ähnlichem) ist eine andere Möglichkeit, das Datenbanksystem nicht direkt auf dem Entwicklungscomputer ausführen zu müssen.</span><span class="sxs-lookup"><span data-stu-id="25ba8-139">[Running SQL Server](/sql/linux/quickstart-install-connect-docker) (or any other database system) in a Docker container (or similar) is another way to avoid running the database system directly on your development machine.</span></span>

## <a name="approach-2-sqlite"></a><span data-ttu-id="25ba8-140">Ansatz 2: SQLite</span><span class="sxs-lookup"><span data-stu-id="25ba8-140">Approach 2: SQLite</span></span>

<span data-ttu-id="25ba8-141">EF Core testet den SQL Server-Anbieter hauptsächlich durch Ausführen in einer lokalen SQL Server-Instanz.</span><span class="sxs-lookup"><span data-stu-id="25ba8-141">EF Core tests the SQL Server provider primarily by running it against a local SQL Server instance.</span></span>
<span data-ttu-id="25ba8-142">Bei diesen Tests werden in ein paar Minuten Zehntausende von Abfragen auf einem schnellen Computer ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="25ba8-142">These tests run tens of thousands of queries in a couple of minutes on a fast machine.</span></span>
<span data-ttu-id="25ba8-143">Dies veranschaulicht, dass die Verwendung des realen Datenbanksystems eine leistungsstarke Lösung sein kann.</span><span class="sxs-lookup"><span data-stu-id="25ba8-143">This illustrates that using the real database system can be a performant solution.</span></span>
<span data-ttu-id="25ba8-144">Es ist ein Mythos, dass die Verwendung einer schlankeren Datenbank die einzige Möglichkeit ist, Tests schnell durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="25ba8-144">It is a myth that using some lighter-weight database is the only way to run tests quickly.</span></span>

<span data-ttu-id="25ba8-145">Davon abgesehen, was ist, wenn Sie aus welchem Grund auch immer keine Tests mit etwas durchführen können, das Ihrem Produktions-Datenbanksystem nahe kommt?</span><span class="sxs-lookup"><span data-stu-id="25ba8-145">That being said, what if for whatever reason you can't run tests against something close to your production database system?</span></span>
<span data-ttu-id="25ba8-146">Die nächstbeste Wahl ist die Verwendung von etwas mit ähnlicher Funktionalität.</span><span class="sxs-lookup"><span data-stu-id="25ba8-146">The next best choice is to use something with similar functionality.</span></span>
<span data-ttu-id="25ba8-147">Dies bedeutet in der Regel eine andere relationale Datenbank, wofür [SQLite](https://sqlite.org/index.html) die offensichtliche Wahl ist.</span><span class="sxs-lookup"><span data-stu-id="25ba8-147">This usually means another relational database, for which [SQLite](https://sqlite.org/index.html) is the obvious choice.</span></span>

<span data-ttu-id="25ba8-148">SQLite ist aus folgenden Gründen eine gute Wahl:</span><span class="sxs-lookup"><span data-stu-id="25ba8-148">SQLite is a good choice because:</span></span>

* <span data-ttu-id="25ba8-149">Sie wird In-Process mit Ihrer Anwendung ausgeführt und hat daher einen geringen Verarbeitungsaufwand.</span><span class="sxs-lookup"><span data-stu-id="25ba8-149">It runs in-process with your application and so has low overhead.</span></span>
* <span data-ttu-id="25ba8-150">Sie verwendet einfache, automatisch erstellte Dateien für Datenbanken und erfordert daher keine Datenbankverwaltung.</span><span class="sxs-lookup"><span data-stu-id="25ba8-150">It uses simple, automatically created files for databases, and so doesn't require database management.</span></span>
* <span data-ttu-id="25ba8-151">Sie hat einen InMemory-Modus, der sogar die Dateierstellung vermeidet.</span><span class="sxs-lookup"><span data-stu-id="25ba8-151">It has an in-memory mode that avoids even the file creation.</span></span>

<span data-ttu-id="25ba8-152">Beachten Sie jedoch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="25ba8-152">However, remember that:</span></span>

* <span data-ttu-id="25ba8-153">SQLite unterstützt nicht zwangsläufig alles, was Ihr Produktions-Datenbanksystem leistet.</span><span class="sxs-lookup"><span data-stu-id="25ba8-153">SQLite inevitability doesn't support everything that your production database system does.</span></span>
* <span data-ttu-id="25ba8-154">SQLite verhält sich bei einigen Abfragen anders als Ihr Produktions-Datenbanksystem.</span><span class="sxs-lookup"><span data-stu-id="25ba8-154">SQLite will behave differently than your production database system for some queries.</span></span>

<span data-ttu-id="25ba8-155">Wenn Sie also SQLite für einige Tests verwenden, stellen Sie sicher, dass Sie die Test auch mit Ihrem tatsächlichen Datenbanksystem durchführen.</span><span class="sxs-lookup"><span data-stu-id="25ba8-155">So if you do use SQLite for some testing, make sure to also test against your real database system.</span></span>

<span data-ttu-id="25ba8-156">Unter [Testen mit SQLite](xref:core/testing/sqlite) finden Sie eine EF Core-spezifische Anleitung.</span><span class="sxs-lookup"><span data-stu-id="25ba8-156">See [Testing with SQLite](xref:core/testing/sqlite) for EF Core specific guidance.</span></span>

## <a name="approach-3-the-ef-core-in-memory-database"></a><span data-ttu-id="25ba8-157">Ansatz 3: Die InMemory-Datenbank von EF Core</span><span class="sxs-lookup"><span data-stu-id="25ba8-157">Approach 3: The EF Core in-memory database</span></span>

<span data-ttu-id="25ba8-158">EF Core bietet eine InMemory-Datenbank, die wir für interne Tests von EF Core selbst verwenden.</span><span class="sxs-lookup"><span data-stu-id="25ba8-158">EF Core comes with an in-memory database that we use for internal testing of EF Core itself.</span></span>
<span data-ttu-id="25ba8-159">Diese Datenbank ist im Allgemeinen **nicht für das Testen von Anwendungen geeignet, die EF Core verwenden**.</span><span class="sxs-lookup"><span data-stu-id="25ba8-159">This database is in general **not suitable for testing applications that use EF Core**.</span></span> <span data-ttu-id="25ba8-160">Dies gilt insbesondere in folgenden Fällen:</span><span class="sxs-lookup"><span data-stu-id="25ba8-160">Specifically:</span></span>

* <span data-ttu-id="25ba8-161">Es handelt sich nicht um eine relationale Datenbank.</span><span class="sxs-lookup"><span data-stu-id="25ba8-161">It is not a relational database.</span></span>
* <span data-ttu-id="25ba8-162">Transaktionen werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="25ba8-162">It doesn't support transactions.</span></span>
* <span data-ttu-id="25ba8-163">Unformatierte SQL-Abfragen können nicht ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="25ba8-163">It cannot run raw SQL queries.</span></span>
* <span data-ttu-id="25ba8-164">Sie ist nicht für Leistung optimiert.</span><span class="sxs-lookup"><span data-stu-id="25ba8-164">It is not optimized for performance.</span></span>

<span data-ttu-id="25ba8-165">Nichts davon ist beim Testen interner EF Core-Aspekte sehr wichtig, weil wir es speziell dort einsetzen, wo die Datenbank für den Test irrelevant ist.</span><span class="sxs-lookup"><span data-stu-id="25ba8-165">None of this is very important when testing EF Core internals because we use it specifically where the database is irrelevant to the test.</span></span>
<span data-ttu-id="25ba8-166">Auf der anderen Seite sind diese Dinge beim Testen einer Anwendung, die EF Core verwendet, in der Regel sehr wichtig.</span><span class="sxs-lookup"><span data-stu-id="25ba8-166">On the other hand, these things tend to be very important when testing an application that uses EF Core.</span></span>

## <a name="unit-testing"></a><span data-ttu-id="25ba8-167">Komponententest</span><span class="sxs-lookup"><span data-stu-id="25ba8-167">Unit testing</span></span>

<span data-ttu-id="25ba8-168">Erwägen Sie das Testen eines Teils der Geschäftslogik, der möglicherweise einige Daten aus einer Datenbank verwenden muss, aber nicht inhärent die Datenbankinteraktionen testet.</span><span class="sxs-lookup"><span data-stu-id="25ba8-168">Consider testing a piece of business logic that might need to use some data from a database, but is not inherently testing the database interactions.</span></span>
<span data-ttu-id="25ba8-169">Eine Möglichkeit ist die Verwendung eines [Testdoubles](https://en.wikipedia.org/wiki/Test_double), z. B. eines simulierten oder nachgemachten Elements.</span><span class="sxs-lookup"><span data-stu-id="25ba8-169">One option is to use a [test double](https://en.wikipedia.org/wiki/Test_double) such as a mock or fake.</span></span>

<span data-ttu-id="25ba8-170">Wir verwenden Testdoubles für interne Tests von EF Core.</span><span class="sxs-lookup"><span data-stu-id="25ba8-170">We use test doubles for internal testing of EF Core.</span></span>
<span data-ttu-id="25ba8-171">Wir versuchen jedoch nie, DbContext oder IQueryable zu simulieren.</span><span class="sxs-lookup"><span data-stu-id="25ba8-171">However, we never try to mock DbContext or IQueryable.</span></span>
<span data-ttu-id="25ba8-172">Dies ist schwierig, umständlich und instabil.</span><span class="sxs-lookup"><span data-stu-id="25ba8-172">Doing so is difficult, cumbersome, and fragile.</span></span>
<span data-ttu-id="25ba8-173">**Machen Sie es nicht.**</span><span class="sxs-lookup"><span data-stu-id="25ba8-173">**Don't do it.**</span></span>

<span data-ttu-id="25ba8-174">Stattdessen verwenden wir die In-Memory-Datenbank von EF, wenn wir Komponententests mit Logik durchführen, die DbContext verwendet.</span><span class="sxs-lookup"><span data-stu-id="25ba8-174">Instead we use the EF in-memory database when unit testing something that uses DbContext.</span></span>
<span data-ttu-id="25ba8-175">In diesem Fall ist die Verwendung der In-Memory-Datenbank von EF angemessen, da der Test nicht vom Datenbankverhalten abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="25ba8-175">In this case using the EF in-memory database is appropriate because the test is not dependent on database behavior.</span></span>
<span data-ttu-id="25ba8-176">Tun Sie dies bloß nicht, um tatsächliche Datenbankabfragen oder -aktualisierungen zu testen.</span><span class="sxs-lookup"><span data-stu-id="25ba8-176">Just don't do this to test actual database queries or updates.</span></span>

<span data-ttu-id="25ba8-177">Das [EF Core-Testbeispiel](xref:core/testing/testing-sample) veranschaulicht Tests mit der In-Memory-Datenbank von EF sowie SQL Server und SQLite.</span><span class="sxs-lookup"><span data-stu-id="25ba8-177">The [EF Core testing sample](xref:core/testing/testing-sample) demonstrates tests using the EF in-memory database, as well as SQL Server and SQLite.</span></span>
