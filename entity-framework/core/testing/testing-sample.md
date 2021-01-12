---
title: EF Core Testbeispiel-EF Core
description: Beispiel für das Testen von Anwendungen, die verwenden Entity Framework Core
author: ajcvickers
ms.date: 04/22/2020
uid: core/testing/testing-sample
no-loc:
- Item
- Tag
- Items
- Tags
- items
- tags
ms.openlocfilehash: 7af516421a6ec3040b636507a6cd7976cabce3a9
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128796"
---
# <a name="ef-core-testing-sample"></a><span data-ttu-id="ffa87-103">EF Core Testbeispiel</span><span class="sxs-lookup"><span data-stu-id="ffa87-103">EF Core testing sample</span></span>

> [!TIP]
> <span data-ttu-id="ffa87-104">Den Code in diesem Dokument finden Sie auf GitHub als Ausführ [bares Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span><span class="sxs-lookup"><span data-stu-id="ffa87-104">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span></span>
> <span data-ttu-id="ffa87-105">Beachten Sie, dass einige dieser Tests **erwartungsgemäß fehlschlagen**.</span><span class="sxs-lookup"><span data-stu-id="ffa87-105">Note that some of these tests **are expected to fail**.</span></span> <span data-ttu-id="ffa87-106">Die Gründe hierfür werden im folgenden erläutert.</span><span class="sxs-lookup"><span data-stu-id="ffa87-106">The reasons for this are explained below.</span></span>

<span data-ttu-id="ffa87-107">Dieses Dokument führt Sie durch ein Beispiel für das Testen von Code, der EF Core verwendet.</span><span class="sxs-lookup"><span data-stu-id="ffa87-107">This doc walks through a sample for testing code that uses EF Core.</span></span>

## <a name="the-application"></a><span data-ttu-id="ffa87-108">Anwendung</span><span class="sxs-lookup"><span data-stu-id="ffa87-108">The application</span></span>

<span data-ttu-id="ffa87-109">Das [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) enthält zwei Projekte:</span><span class="sxs-lookup"><span data-stu-id="ffa87-109">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contains two projects:</span></span>

- <span data-ttu-id="ffa87-110">ItemsWebAPI: eine sehr einfache [Web-API, die durch ASP.net Core](/aspnet/core/tutorials/first-web-api) mit einem einzelnen Controller unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="ffa87-110">ItemsWebApi: A very simple [Web API backed by ASP.NET Core](/aspnet/core/tutorials/first-web-api) with a single controller</span></span>
- <span data-ttu-id="ffa87-111">Tests: ein [xUnit](https://xunit.net/) -Testprojekt zum Testen des Controllers</span><span class="sxs-lookup"><span data-stu-id="ffa87-111">Tests: An [XUnit](https://xunit.net/) test project to test the controller</span></span>

### <a name="the-model-and-business-rules"></a><span data-ttu-id="ffa87-112">Die Modell-und Geschäftsregeln</span><span class="sxs-lookup"><span data-stu-id="ffa87-112">The model and business rules</span></span>

<span data-ttu-id="ffa87-113">Das Modell, das diese API unterstützt, verfügt über zwei Entitäts Typen: Items und Tags .</span><span class="sxs-lookup"><span data-stu-id="ffa87-113">The model backing this API has two entity types: Items and Tags.</span></span>

- <span data-ttu-id="ffa87-114">Items Beachten Sie die Groß-/Kleinschreibung und eine Auflistung von Tags .</span><span class="sxs-lookup"><span data-stu-id="ffa87-114">Items have a case-sensitive name and a collection of Tags.</span></span>
- <span data-ttu-id="ffa87-115">Jede Tag verfügt über eine Bezeichnung und eine Anzahl, die angibt, wie oft Sie auf das angewendet wurde Item .</span><span class="sxs-lookup"><span data-stu-id="ffa87-115">Each Tag has a label and a count representing the number of times it has been applied to the Item.</span></span>
- <span data-ttu-id="ffa87-116">Jeder Item darf nur einen Tag mit einer bestimmten Bezeichnung aufweisen.</span><span class="sxs-lookup"><span data-stu-id="ffa87-116">Each Item should only have one Tag with a given label.</span></span>
  - <span data-ttu-id="ffa87-117">Wenn ein Element mehrmals mit derselben Bezeichnung gekennzeichnet ist, wird die Anzahl der vorhandenen Tags mit dieser Bezeichnung inkrementiert, anstatt ein neues Tag zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ffa87-117">If an item is tagged with the same label more than once, then the count on the existing tag with that label is incremented instead of a new tag being created.</span></span>
- <span data-ttu-id="ffa87-118">Beim Löschen eines Item sollten alle zugeordneten gelöscht werden Tags .</span><span class="sxs-lookup"><span data-stu-id="ffa87-118">Deleting an Item should delete all associated Tags.</span></span>

#### <a name="the-no-locitem-entity-type"></a><span data-ttu-id="ffa87-119">Der Item Entitätstyp</span><span class="sxs-lookup"><span data-stu-id="ffa87-119">The Item entity type</span></span>

<span data-ttu-id="ffa87-120">Der `Item` Entitätstyp:</span><span class="sxs-lookup"><span data-stu-id="ffa87-120">The `Item` entity type:</span></span>

[!code-csharp[ItemEntityType](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

<span data-ttu-id="ffa87-121">Und die Konfiguration in `DbContext.OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="ffa87-121">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

<span data-ttu-id="ffa87-122">Beachten Sie, dass der Entitätstyp die Art und Weise einschränkt, wie er das Domänen Modell und die Geschäftsregeln widerspiegeln kann.</span><span class="sxs-lookup"><span data-stu-id="ffa87-122">Notice that entity type constrains the way it can be used to reflect the domain model and business rules.</span></span> <span data-ttu-id="ffa87-123">Dies gilt insbesondere für:</span><span class="sxs-lookup"><span data-stu-id="ffa87-123">In particular:</span></span>

- <span data-ttu-id="ffa87-124">Der Primärschlüssel wird dem Feld direkt zugeordnet `_id` und nicht öffentlich verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="ffa87-124">The primary key is mapped directly to the `_id` field and not exposed publicly</span></span>
  - <span data-ttu-id="ffa87-125">EF erkennt und verwendet den privaten Konstruktor, der den Wert und den Namen des Primärschlüssels akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="ffa87-125">EF detects and uses the private constructor accepting the primary key value and name.</span></span>
- <span data-ttu-id="ffa87-126">Die `Name` -Eigenschaft ist schreibgeschützt und wird nur im-Konstruktor festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ffa87-126">The `Name` property is read-only and set only in the constructor.</span></span>
- <span data-ttu-id="ffa87-127">Tags werden als bereitgestellt `IReadOnlyList<Tag>` , um beliebige Änderungen zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="ffa87-127">Tags are exposed as a `IReadOnlyList<Tag>` to prevent arbitrary modification.</span></span>
  - <span data-ttu-id="ffa87-128">EF ordnet die- `Tags` Eigenschaft dem dahinter liegenden Feld zu, `_tags` indem die entsprechenden Namen abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="ffa87-128">EF associates the `Tags` property with the `_tags` backing field by matching their names.</span></span>
  - <span data-ttu-id="ffa87-129">Die `AddTag` -Methode nimmt eine tagbezeichnung an und implementiert die oben beschriebene Geschäftsregel.</span><span class="sxs-lookup"><span data-stu-id="ffa87-129">The `AddTag` method takes a tag label and implements the business rule described above.</span></span>
    <span data-ttu-id="ffa87-130">Das heißt, ein Tag wird nur für neue Bezeichnungen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ffa87-130">That is, a tag is only added for new labels.</span></span>
    <span data-ttu-id="ffa87-131">Andernfalls wird die Anzahl für eine vorhandene Bezeichnung inkrementiert.</span><span class="sxs-lookup"><span data-stu-id="ffa87-131">Otherwise the count on an existing label is incremented.</span></span>
- <span data-ttu-id="ffa87-132">Die `Tags` Navigations Eigenschaft ist für eine n:1-Beziehung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="ffa87-132">The `Tags` navigation property is configured for a many-to-one relationship</span></span>
  - <span data-ttu-id="ffa87-133">Es ist nicht erforderlich, dass eine Navigations Eigenschaft von Tag auf ist Item , sodass Sie nicht eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="ffa87-133">There is no need for a navigation property from Tag to Item, so it is not included.</span></span>
  - <span data-ttu-id="ffa87-134">Außerdem Tag definiert keine Fremdschlüssel Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ffa87-134">Also, Tag does not define a foreign key property.</span></span>
    <span data-ttu-id="ffa87-135">Stattdessen erstellt und verwaltet EF eine Eigenschaft im Schatten Zustand.</span><span class="sxs-lookup"><span data-stu-id="ffa87-135">Instead, EF will create and manage a property in shadow-state.</span></span>

#### <a name="the-no-loctag-entity-type"></a><span data-ttu-id="ffa87-136">Der Tag Entitätstyp</span><span class="sxs-lookup"><span data-stu-id="ffa87-136">The Tag entity type</span></span>

<span data-ttu-id="ffa87-137">Der `Tag` Entitätstyp:</span><span class="sxs-lookup"><span data-stu-id="ffa87-137">The `Tag` entity type:</span></span>

[!code-csharp[TagEntityType](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

<span data-ttu-id="ffa87-138">Und die Konfiguration in `DbContext.OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="ffa87-138">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureTag](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

<span data-ttu-id="ffa87-139">Ebenso wie Item wird Tag der Primärschlüssel ausgeblendet, und die Eigenschaft wird als schreibgeschützt definiert `Label` .</span><span class="sxs-lookup"><span data-stu-id="ffa87-139">Similarly to Item, Tag hides its primary key and makes the `Label` property read-only.</span></span>

### <a name="the-no-locitemscontroller"></a><span data-ttu-id="ffa87-140">Der Items Controller</span><span class="sxs-lookup"><span data-stu-id="ffa87-140">The ItemsController</span></span>

<span data-ttu-id="ffa87-141">Der Web-API-Controller ist ziemlich einfach.</span><span class="sxs-lookup"><span data-stu-id="ffa87-141">The Web API controller is pretty basic.</span></span>
<span data-ttu-id="ffa87-142">Er ruft eine `DbContext` aus dem Container für die Abhängigkeitsinjektion durch die Konstruktorinjektion ab:</span><span class="sxs-lookup"><span data-stu-id="ffa87-142">It gets a `DbContext` from the dependency injection container through constructor injection:</span></span>

[!code-csharp[Constructor](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

<span data-ttu-id="ffa87-143">Sie verfügt über Methoden, um alle Items oder eine Item mit einem bestimmten Namen zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="ffa87-143">It has methods to get all Items or an Item with a given name:</span></span>

[!code-csharp[Get](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

<span data-ttu-id="ffa87-144">Es verfügt über eine Methode zum Hinzufügen eines neuen Item :</span><span class="sxs-lookup"><span data-stu-id="ffa87-144">It has a method to add a new Item:</span></span>

[!code-csharp[PostItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

<span data-ttu-id="ffa87-145">Eine Methode zum Markieren eines Item mit einer Bezeichnung:</span><span class="sxs-lookup"><span data-stu-id="ffa87-145">A method to tag an Item with a label:</span></span>

[!code-csharp[PostTag](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

<span data-ttu-id="ffa87-146">Und eine Methode zum Löschen eines Item und aller zugeordneten Tags :</span><span class="sxs-lookup"><span data-stu-id="ffa87-146">And a method to delete an Item and all associated Tags:</span></span>

[!code-csharp[DeleteItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

<span data-ttu-id="ffa87-147">Die meisten Validierungs-und Fehlerbehandlung wurde entfernt, um die Übersichtlichkeit zu verringern.</span><span class="sxs-lookup"><span data-stu-id="ffa87-147">Most validation and error handling have been removed to reduce clutter.</span></span>

## <a name="the-tests"></a><span data-ttu-id="ffa87-148">Die Tests</span><span class="sxs-lookup"><span data-stu-id="ffa87-148">The Tests</span></span>

<span data-ttu-id="ffa87-149">Die Tests sind so organisiert, dass Sie mit mehreren Datenbankanbieter Konfigurationen ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="ffa87-149">The tests are organized to run with multiple database provider configurations:</span></span>

- <span data-ttu-id="ffa87-150">Der SQL Server-Anbieter, der der von der Anwendung verwendete Anbieter ist.</span><span class="sxs-lookup"><span data-stu-id="ffa87-150">The SQL Server provider, which is the provider used by the application</span></span>
- <span data-ttu-id="ffa87-151">Der SQLite-Anbieter</span><span class="sxs-lookup"><span data-stu-id="ffa87-151">The SQLite provider</span></span>
- <span data-ttu-id="ffa87-152">Der SQLite-Anbieter mit in-Memory-SQLite-Datenbanken</span><span class="sxs-lookup"><span data-stu-id="ffa87-152">The SQLite provider using in-memory SQLite databases</span></span>
- <span data-ttu-id="ffa87-153">Der EF in-Memory Database-Anbieter</span><span class="sxs-lookup"><span data-stu-id="ffa87-153">The EF in-memory database provider</span></span>

<span data-ttu-id="ffa87-154">Dies wird erreicht, indem alle Tests in einer Basisklasse vorgenommen werden und dann von diesem geerbt wird, um Sie mit jedem Anbieter zu testen.</span><span class="sxs-lookup"><span data-stu-id="ffa87-154">This is achieved by putting all the tests in a base class, then inheriting from this to test with each provider.</span></span>

> [!TIP]
> <span data-ttu-id="ffa87-155">Sie müssen die SQL Server Verbindungs Zeichenfolge ändern, wenn Sie nicht localdb verwenden.</span><span class="sxs-lookup"><span data-stu-id="ffa87-155">You will need to change the SQL Server connection string if you're not using LocalDB.</span></span>
> <span data-ttu-id="ffa87-156">Unter [Testen mit SQLite](xref:core/testing/sqlite) finden Sie Anleitungen zur Verwendung von SQLite für in-Memory-Tests.</span><span class="sxs-lookup"><span data-stu-id="ffa87-156">See [Testing with SQLite](xref:core/testing/sqlite) for guidance on using SQLite for in-memory testing.</span></span>

<span data-ttu-id="ffa87-157">Es wird erwartet, dass die folgenden beiden Tests fehlschlagen:</span><span class="sxs-lookup"><span data-stu-id="ffa87-157">The following two tests are expected to fail:</span></span>

- <span data-ttu-id="ffa87-158">`Can_remove_item_and_all_associated_tags` bei Ausführung mit dem EF in-Memory Database-Anbieter</span><span class="sxs-lookup"><span data-stu-id="ffa87-158">`Can_remove_item_and_all_associated_tags` when running with the EF in-memory database provider</span></span>
- <span data-ttu-id="ffa87-159">`Can_add_item_differing_only_by_case` bei Ausführung mit dem SQL Server-Anbieter</span><span class="sxs-lookup"><span data-stu-id="ffa87-159">`Can_add_item_differing_only_by_case` when running with the SQL Server provider</span></span>

<span data-ttu-id="ffa87-160">Dies wird im folgenden ausführlicher beschrieben.</span><span class="sxs-lookup"><span data-stu-id="ffa87-160">This is covered in more detail below.</span></span>

### <a name="setting-up-and-seeding-the-database"></a><span data-ttu-id="ffa87-161">Einrichten und Seeding der Datenbank</span><span class="sxs-lookup"><span data-stu-id="ffa87-161">Setting up and seeding the database</span></span>

<span data-ttu-id="ffa87-162">XUnit erstellt, wie bei den meisten Test-Frameworks, eine neue Test Klasseninstanz für jeden Testlauf.</span><span class="sxs-lookup"><span data-stu-id="ffa87-162">XUnit, like most testing frameworks, will create a new test class instance for each test run.</span></span>
<span data-ttu-id="ffa87-163">Außerdem führt xUnit keine Tests in einer bestimmten Testklasse parallel aus.</span><span class="sxs-lookup"><span data-stu-id="ffa87-163">Also, XUnit will not run tests within a given test class in parallel.</span></span>
<span data-ttu-id="ffa87-164">Dies bedeutet, dass wir die Datenbank im testkonstruktor einrichten und konfigurieren können. Sie werden für jeden Test in einem bekannten Zustand angezeigt.</span><span class="sxs-lookup"><span data-stu-id="ffa87-164">This means that we can set up and configure the database in the test constructor and it will be in a well-known state for each test.</span></span>

> [!TIP]
> <span data-ttu-id="ffa87-165">In diesem Beispiel wird die Datenbank für jeden Test neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="ffa87-165">This sample recreates the database for each test.</span></span>
> <span data-ttu-id="ffa87-166">Dies funktioniert gut für SQLite-und EF-in-Memory Database Tests, kann jedoch mit anderen Datenbanksystemen, einschließlich SQL Server, einen erheblichen Aufwand verursachen.</span><span class="sxs-lookup"><span data-stu-id="ffa87-166">This works well for SQLite and EF in-memory database testing but can involve significant overhead with other database systems, including SQL Server.</span></span>
> <span data-ttu-id="ffa87-167">Die Vorgehensweisen zur Reduzierung dieses Aufwands werden bei der [gemeinsamen Nutzung von Datenbanken](xref:core/testing/sharing-databases)behandelt.</span><span class="sxs-lookup"><span data-stu-id="ffa87-167">Approaches for reducing this overhead are covered in [Sharing databases across tests](xref:core/testing/sharing-databases).</span></span>

<span data-ttu-id="ffa87-168">Beim Ausführen der einzelnen Tests:</span><span class="sxs-lookup"><span data-stu-id="ffa87-168">When each test is run:</span></span>

- <span data-ttu-id="ffa87-169">Dbcontextoptions wird für den verwendeten Anbieter konfiguriert und an den Basisklassenkonstruktor übergeben.</span><span class="sxs-lookup"><span data-stu-id="ffa87-169">DbContextOptions are configured for the provider in use and passed to the base class constructor</span></span>
  - <span data-ttu-id="ffa87-170">Diese Optionen werden in einer Eigenschaft gespeichert und in den Tests zum Erstellen von dbcontext-Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="ffa87-170">These options are stored in a property and used throughout the tests for creating DbContext instances</span></span>
- <span data-ttu-id="ffa87-171">Eine Seed-Methode wird aufgerufen, um die Datenbank zu erstellen und zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ffa87-171">A Seed method is called to create and seed the database</span></span>
  - <span data-ttu-id="ffa87-172">Mit der Seed-Methode wird sichergestellt, dass die Datenbank bereinigt wird, indem Sie gelöscht und anschließend neu erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="ffa87-172">The Seed method ensures the database is clean by deleting it and then re-creating it</span></span>
  - <span data-ttu-id="ffa87-173">Einige bekannte Test Entitäten werden erstellt und in der Datenbank gespeichert.</span><span class="sxs-lookup"><span data-stu-id="ffa87-173">Some well-known test entities are created and saved to the database</span></span>

[!code-csharp[Seeding](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

<span data-ttu-id="ffa87-174">Jede konkrete Testklasse erbt dann von diesem.</span><span class="sxs-lookup"><span data-stu-id="ffa87-174">Each concrete test class then inherits from this.</span></span>
<span data-ttu-id="ffa87-175">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ffa87-175">For example:</span></span>

[!code-csharp[SqliteItemsControllerTest](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a><span data-ttu-id="ffa87-176">Test Struktur</span><span class="sxs-lookup"><span data-stu-id="ffa87-176">Test structure</span></span>

<span data-ttu-id="ffa87-177">Obwohl die Anwendung eine Abhängigkeitsinjektion verwendet, ist dies bei den Tests nicht der Fall.</span><span class="sxs-lookup"><span data-stu-id="ffa87-177">Even though the application uses dependency injection, the tests do not.</span></span>
<span data-ttu-id="ffa87-178">Hier wäre es in Ordnung, die Abhängigkeitsinjektion zu verwenden, aber der zusätzliche Code, der benötigt wird, hat nur wenig Wert.</span><span class="sxs-lookup"><span data-stu-id="ffa87-178">It would be fine to use dependency injection here, but the additional code it requires has little value.</span></span>
<span data-ttu-id="ffa87-179">Stattdessen wird ein dbcontext mithilfe von erstellt `new` und dann direkt als Abhängigkeit an den Controller weitergegeben.</span><span class="sxs-lookup"><span data-stu-id="ffa87-179">Instead, a DbContext is created using `new` and then directly passed as the dependency to the controller.</span></span>

<span data-ttu-id="ffa87-180">Jeder Test führt dann die zu testende Methode auf dem Controller aus und bestätigt, dass die Ergebnisse erwartungsgemäß sind.</span><span class="sxs-lookup"><span data-stu-id="ffa87-180">Each test then executes the method under test on the controller and asserts the results are as expected.</span></span>
<span data-ttu-id="ffa87-181">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ffa87-181">For example:</span></span>

[!code-csharp[CanGetItems](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

<span data-ttu-id="ffa87-182">Beachten Sie, dass unterschiedliche dbcontext-Instanzen verwendet werden, um die Datenbank zu starten und die Tests auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ffa87-182">Notice that different DbContext instances are used to seed the database and run the tests.</span></span>
<span data-ttu-id="ffa87-183">Dadurch wird sichergestellt, dass der Test keine Entitäten verwendet, die vom Kontext nachverfolgt werden, wenn das Seeding durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ffa87-183">This ensures that the test is not using (or tripping over) entities tracked by the context when seeding.</span></span>
<span data-ttu-id="ffa87-184">Außerdem ist es besser, was in Web-Apps und-Diensten passiert.</span><span class="sxs-lookup"><span data-stu-id="ffa87-184">It also better matches what happens in web apps and services.</span></span>

<span data-ttu-id="ffa87-185">Durch Tests, die die Datenbank mutieren, wird aus ähnlichen Gründen eine zweite dbcontext-Instanz im Test erstellt.</span><span class="sxs-lookup"><span data-stu-id="ffa87-185">Tests that mutate the database create a second DbContext instance in the test for similar reasons.</span></span>
<span data-ttu-id="ffa87-186">Das heißt, Sie erstellen einen neuen, sauberen Kontext und lesen ihn aus der Datenbank, um sicherzustellen, dass die Änderungen in der Datenbank gespeichert wurden.</span><span class="sxs-lookup"><span data-stu-id="ffa87-186">That is, creating a new, clean, context and then reading into it from the database to ensure that the changes were saved to the database.</span></span>
<span data-ttu-id="ffa87-187">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ffa87-187">For example:</span></span>

[!code-csharp[CanAddItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

<span data-ttu-id="ffa87-188">Zwei etwas mehr beteiligte Tests decken die Geschäftslogik um das Hinzufügen von ab tags .</span><span class="sxs-lookup"><span data-stu-id="ffa87-188">Two slightly more involved tests cover the business logic around adding tags.</span></span>

[!code-csharp[CanAddTag](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a><span data-ttu-id="ffa87-189">Probleme bei der Verwendung verschiedener Datenbankanbieter</span><span class="sxs-lookup"><span data-stu-id="ffa87-189">Issues using different database providers</span></span>

<span data-ttu-id="ffa87-190">Das Testen mit einem anderen Datenbanksystem, das in der Produktionsanwendung verwendet wird, kann zu Problemen führen.</span><span class="sxs-lookup"><span data-stu-id="ffa87-190">Testing with a different database system than is used in the production application can lead to problems.</span></span>
<span data-ttu-id="ffa87-191">Diese werden auf der konzeptionellen Ebene im [Testcode behandelt, der EF Core verwendet](xref:core/testing/index).</span><span class="sxs-lookup"><span data-stu-id="ffa87-191">These are covered at the conceptual level in [Testing code that uses EF Core](xref:core/testing/index).</span></span>
<span data-ttu-id="ffa87-192">In den folgenden Abschnitten werden zwei Beispiele für solche Probleme behandelt, die von den Tests in diesem Beispiel demonstriert werden.</span><span class="sxs-lookup"><span data-stu-id="ffa87-192">The sections below cover two examples of such issues demonstrated by the tests in this sample.</span></span>

### <a name="test-passes-when-the-application-is-broken"></a><span data-ttu-id="ffa87-193">Test läuft, wenn die Anwendung beschädigt ist</span><span class="sxs-lookup"><span data-stu-id="ffa87-193">Test passes when the application is broken</span></span>

<span data-ttu-id="ffa87-194">Eine der Anforderungen für unsere Anwendung besteht darin, dass "die Items groß-und Kleinschreibung beachtet und eine Auflistung von" beachtet werden muss Tags .</span><span class="sxs-lookup"><span data-stu-id="ffa87-194">One of the requirements for our application is that "Items have a case-sensitive name and a collection of Tags."</span></span>
<span data-ttu-id="ffa87-195">Dies ist ziemlich einfach zu testen:</span><span class="sxs-lookup"><span data-stu-id="ffa87-195">This is pretty simple to test:</span></span>

[!code-csharp[CanAddItemCaseInsensitive](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

<span data-ttu-id="ffa87-196">Das Ausführen dieses Tests für den EF-in-Memory Database gibt an, dass alles in Ordnung ist.</span><span class="sxs-lookup"><span data-stu-id="ffa87-196">Running this test against the EF in-memory database indicates that everything is fine.</span></span>
<span data-ttu-id="ffa87-197">Bei der Verwendung von SQLite ist alles weiterhin einwandfrei.</span><span class="sxs-lookup"><span data-stu-id="ffa87-197">Everything still looks fine when using SQLite.</span></span>
<span data-ttu-id="ffa87-198">Der Test schlägt jedoch fehl, wenn er auf SQL Server ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ffa87-198">But the test fails when run against SQL Server!</span></span>

```output
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

<span data-ttu-id="ffa87-199">Dies liegt daran, dass der EF-in-Memory Database und die SQLite-Datenbank standardmäßig die Groß-/Kleinschreibung beachten.</span><span class="sxs-lookup"><span data-stu-id="ffa87-199">This is because both the EF in-memory database and the SQLite database are case-sensitive by default.</span></span>
<span data-ttu-id="ffa87-200">SQL Server hingegen wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="ffa87-200">SQL Server, on the other hand, is case-insensitive!</span></span>

<span data-ttu-id="ffa87-201">EF Core ändert diese Verhaltensweisen nicht, da das Erzwingen einer Änderung bei der Unterscheidung nach Groß-/Kleinschreibung eine erhebliche Auswirkung auf die Leistung haben kann.</span><span class="sxs-lookup"><span data-stu-id="ffa87-201">EF Core, by design, does not change these behaviors because forcing a change in case-sensitivity can have a big performance impact.</span></span>

<span data-ttu-id="ffa87-202">Sobald wir wissen, dass es sich um ein Problem handelt, können wir die Anwendung beheben und Tests kompensieren.</span><span class="sxs-lookup"><span data-stu-id="ffa87-202">Once we know this is a problem we can fix the application and compensate in tests.</span></span>
<span data-ttu-id="ffa87-203">Der Punkt hier ist jedoch, dass dieser Fehler übersehen werden kann, wenn nur der EF-in-Memory Database oder SQLite-Anbieter getestet wird.</span><span class="sxs-lookup"><span data-stu-id="ffa87-203">However, the point here is that this bug could be missed if only testing with the EF in-memory database or SQLite providers.</span></span>

### <a name="test-fails-when-the-application-is-correct"></a><span data-ttu-id="ffa87-204">Test schlägt fehl, wenn die Anwendung korrekt ist</span><span class="sxs-lookup"><span data-stu-id="ffa87-204">Test fails when the application is correct</span></span>

<span data-ttu-id="ffa87-205">Eine weitere Anforderung für die Anwendung besteht darin, dass das Löschen eines Item alle zugeordneten löschen soll Tags .</span><span class="sxs-lookup"><span data-stu-id="ffa87-205">Another of the requirements for our application is that "deleting an Item should delete all associated Tags."</span></span>
<span data-ttu-id="ffa87-206">Auch hier leicht zu testen:</span><span class="sxs-lookup"><span data-stu-id="ffa87-206">Again, easy to test:</span></span>

[!code-csharp[DeleteItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

<span data-ttu-id="ffa87-207">Dieser Test wird SQL Server und SQLite bestanden, schlägt jedoch mit dem EF-in-Memory Database fehl!</span><span class="sxs-lookup"><span data-stu-id="ffa87-207">This test passes on SQL Server and SQLite, but fails with the EF in-memory database!</span></span>

```output
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

<span data-ttu-id="ffa87-208">In diesem Fall funktioniert die Anwendung ordnungsgemäß, da SQL Server [kaskadierte](xref:core/saving/cascade-delete)Löschvorgänge unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ffa87-208">In this case, the application is working correctly because SQL Server supports [cascade deletes](xref:core/saving/cascade-delete).</span></span>
<span data-ttu-id="ffa87-209">SQLite unterstützt auch das Löschen von Lösch Vorgängen, wie bei den meisten relationalen Datenbanken, sodass das Testen auf SQLite funktioniert.</span><span class="sxs-lookup"><span data-stu-id="ffa87-209">SQLite also supports cascade deletes, as do most relational databases, so testing this on SQLite works.</span></span>
<span data-ttu-id="ffa87-210">Auf der anderen Seite unterstützt das EF-in-Memory Database [keine kaskadierenden](https://github.com/dotnet/efcore/issues/3924)Löschvorgänge.</span><span class="sxs-lookup"><span data-stu-id="ffa87-210">On the other hand, the EF in-memory database [does not support cascade deletes](https://github.com/dotnet/efcore/issues/3924).</span></span>
<span data-ttu-id="ffa87-211">Dies bedeutet, dass dieser Teil der Anwendung nicht mit dem EF in-Memory Database-Anbieter getestet werden kann.</span><span class="sxs-lookup"><span data-stu-id="ffa87-211">This means that this part of the application cannot be tested with the EF in-memory database provider.</span></span>
