---
title: ''
description: ''
author: ''
ms.date: ''
uid: ''
no-loc:
- Item
- Tag
- Items
- Tags
ms.openlocfilehash: ae073fc0b3a99fb9de07a3e0a42c638fe0838a5a
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672815"
---
# <a name="ef-core-testing-sample"></a><span data-ttu-id="a0588-101">EF Core Testbeispiel</span><span class="sxs-lookup"><span data-stu-id="a0588-101">EF Core testing sample</span></span>

> [!TIP]
> <span data-ttu-id="a0588-102">Den Code in diesem Dokument finden Sie auf GitHub als Ausführ [bares Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span><span class="sxs-lookup"><span data-stu-id="a0588-102">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span></span>
> <span data-ttu-id="a0588-103">Beachten Sie, dass einige dieser Tests **erwartungsgemäß fehlschlagen**.</span><span class="sxs-lookup"><span data-stu-id="a0588-103">Note that some of these tests **are expected to fail**.</span></span> <span data-ttu-id="a0588-104">Die Gründe hierfür werden im folgenden erläutert.</span><span class="sxs-lookup"><span data-stu-id="a0588-104">The reasons for this are explained below.</span></span> 

<span data-ttu-id="a0588-105">Dieses Dokument führt Sie durch ein Beispiel für das Testen von Code, der EF Core verwendet.</span><span class="sxs-lookup"><span data-stu-id="a0588-105">This doc walks through a sample for testing code that uses EF Core.</span></span>

## <a name="the-application"></a><span data-ttu-id="a0588-106">Anwendung</span><span class="sxs-lookup"><span data-stu-id="a0588-106">The application</span></span>

<span data-ttu-id="a0588-107">Das [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) enthält zwei Projekte:</span><span class="sxs-lookup"><span data-stu-id="a0588-107">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contains two projects:</span></span>
- <span data-ttu-id="a0588-108">Itemswebapi: eine sehr einfache [Web-API, die durch ASP.net Core](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio) mit einem einzelnen Controller unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="a0588-108">ItemsWebApi: A very simple [Web API backed by ASP.NET Core](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio) with a single controller</span></span>
- <span data-ttu-id="a0588-109">Tests: ein [xUnit](https://xunit.net/) -Testprojekt zum Testen des Controllers</span><span class="sxs-lookup"><span data-stu-id="a0588-109">Tests: An [XUnit](https://xunit.net/) test project to test the controller</span></span>

### <a name="the-model-and-business-rules"></a><span data-ttu-id="a0588-110">Die Modell-und Geschäftsregeln</span><span class="sxs-lookup"><span data-stu-id="a0588-110">The model and business rules</span></span>

<span data-ttu-id="a0588-111">Das Modell, das diese API unterstützt, verfügt über zwei Entitäts Typen: Items und Tags .</span><span class="sxs-lookup"><span data-stu-id="a0588-111">The model backing this API has two entity types: Items and Tags.</span></span>

* Items<span data-ttu-id="a0588-112">Beachten Sie die Groß-/Kleinschreibung und eine Auflistung von Tags .</span><span class="sxs-lookup"><span data-stu-id="a0588-112"> have a case-sensitive name and a collection of Tags.</span></span>
* <span data-ttu-id="a0588-113">Jede Tag verfügt über eine Bezeichnung und eine Anzahl, die angibt, wie oft Sie auf das angewendet wurde Item .</span><span class="sxs-lookup"><span data-stu-id="a0588-113">Each Tag has a label and a count representing the number of times it has been applied to the Item.</span></span>
* <span data-ttu-id="a0588-114">Jeder Item darf nur einen Tag mit einer bestimmten Bezeichnung aufweisen.</span><span class="sxs-lookup"><span data-stu-id="a0588-114">Each Item should only have one Tag with a given label.</span></span>
  * <span data-ttu-id="a0588-115">Wenn ein Element mehrmals mit derselben Bezeichnung gekennzeichnet ist, wird die Anzahl der vorhandenen Tags mit dieser Bezeichnung inkrementiert, anstatt ein neues Tag zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a0588-115">If an item is tagged with the same label more than once, then the count on the existing tag with that label is incremented instead of a new tag being created.</span></span> 
* <span data-ttu-id="a0588-116">Beim Löschen eines Item sollten alle zugeordneten gelöscht werden Tags .</span><span class="sxs-lookup"><span data-stu-id="a0588-116">Deleting an Item should delete all associated Tags.</span></span>

#### <a name="the-item-entity-type"></a><span data-ttu-id="a0588-117">Der Item Entitätstyp</span><span class="sxs-lookup"><span data-stu-id="a0588-117">The Item entity type</span></span>

<span data-ttu-id="a0588-118">Der `Item` Entitätstyp:</span><span class="sxs-lookup"><span data-stu-id="a0588-118">The `Item` entity type:</span></span>

[!code-csharp[ItemEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

<span data-ttu-id="a0588-119">Und die Konfiguration in `DbContext.OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="a0588-119">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

<span data-ttu-id="a0588-120">Beachten Sie, dass der Entitätstyp die Art und Weise einschränkt, wie er das Domänen Modell und die Geschäftsregeln widerspiegeln kann.</span><span class="sxs-lookup"><span data-stu-id="a0588-120">Notice that entity type constrains the way it can be used to reflect the domain model and business rules.</span></span> <span data-ttu-id="a0588-121">Dies gilt insbesondere für:</span><span class="sxs-lookup"><span data-stu-id="a0588-121">In particular:</span></span>
- <span data-ttu-id="a0588-122">Der Primärschlüssel wird dem Feld direkt zugeordnet `_id` und nicht öffentlich verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="a0588-122">The primary key is mapped directly to the `_id` field and not exposed publicly</span></span>
  - <span data-ttu-id="a0588-123">EF erkennt und verwendet den privaten Konstruktor, der den Wert und den Namen des Primärschlüssels akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="a0588-123">EF detects and uses the private constructor accepting the primary key value and name.</span></span>
- <span data-ttu-id="a0588-124">Die `Name` -Eigenschaft ist schreibgeschützt und wird nur im-Konstruktor festgelegt.</span><span class="sxs-lookup"><span data-stu-id="a0588-124">The `Name` property is read-only and set only in the constructor.</span></span> 
- Tags<span data-ttu-id="a0588-125">werden als bereitgestellt `IReadOnlyList<Tag>` , um beliebige Änderungen zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="a0588-125"> are exposed as a `IReadOnlyList<Tag>` to prevent arbitrary modification.</span></span>
  - <span data-ttu-id="a0588-126">EF ordnet die- `Tags` Eigenschaft dem dahinter liegenden Feld zu, `_tags` indem die entsprechenden Namen abgeglichen werden.</span><span class="sxs-lookup"><span data-stu-id="a0588-126">EF associates the `Tags` property with the `_tags` backing field by matching their names.</span></span> 
  - <span data-ttu-id="a0588-127">Die `AddTag` -Methode nimmt eine tagbezeichnung an und implementiert die oben beschriebene Geschäftsregel.</span><span class="sxs-lookup"><span data-stu-id="a0588-127">The `AddTag` method takes a tag label and implements the business rule described above.</span></span>
    <span data-ttu-id="a0588-128">Das heißt, ein Tag wird nur für neue Bezeichnungen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="a0588-128">That is, a tag is only added for new labels.</span></span>
    <span data-ttu-id="a0588-129">Andernfalls wird die Anzahl für eine vorhandene Bezeichnung inkrementiert.</span><span class="sxs-lookup"><span data-stu-id="a0588-129">Otherwise the count on an existing label is incremented.</span></span>
- <span data-ttu-id="a0588-130">Die `Tags` Navigations Eigenschaft ist für eine n:1-Beziehung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a0588-130">The `Tags` navigation property is configured for a many-to-one relationship</span></span>
  - <span data-ttu-id="a0588-131">Es ist nicht erforderlich, dass eine Navigations Eigenschaft von Tag auf ist Item , sodass Sie nicht eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="a0588-131">There is no need for a navigation property from Tag to Item, so it is not included.</span></span>
  - <span data-ttu-id="a0588-132">Außerdem Tag definiert keine Fremdschlüssel Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="a0588-132">Also, Tag does not define a foreign key property.</span></span>
    <span data-ttu-id="a0588-133">Stattdessen erstellt und verwaltet EF eine Eigenschaft im Schatten Zustand.</span><span class="sxs-lookup"><span data-stu-id="a0588-133">Instead, EF will create and manage a property in shadow-state.</span></span>

#### <a name="the-tag-entity-type"></a><span data-ttu-id="a0588-134">Der Tag Entitätstyp</span><span class="sxs-lookup"><span data-stu-id="a0588-134">The Tag entity type</span></span>

<span data-ttu-id="a0588-135">Der `Tag` Entitätstyp:</span><span class="sxs-lookup"><span data-stu-id="a0588-135">The `Tag` entity type:</span></span>

[!code-csharp[TagEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

<span data-ttu-id="a0588-136">Und die Konfiguration in `DbContext.OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="a0588-136">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

<span data-ttu-id="a0588-137">Ebenso wie Item wird Tag der Primärschlüssel ausgeblendet, und die Eigenschaft wird als schreibgeschützt definiert `Label` .</span><span class="sxs-lookup"><span data-stu-id="a0588-137">Similarly to Item, Tag hides its primary key and makes the `Label` property read-only.</span></span>

### <a name="the-itemscontroller"></a><span data-ttu-id="a0588-138">Der itemscontroller</span><span class="sxs-lookup"><span data-stu-id="a0588-138">The ItemsController</span></span>

<span data-ttu-id="a0588-139">Der Web-API-Controller ist ziemlich einfach.</span><span class="sxs-lookup"><span data-stu-id="a0588-139">The Web API controller is pretty basic.</span></span>
<span data-ttu-id="a0588-140">Er ruft eine `DbContext` aus dem Container für die Abhängigkeitsinjektion durch die Konstruktorinjektion ab:</span><span class="sxs-lookup"><span data-stu-id="a0588-140">It gets a `DbContext` from the dependency injection container through constructor injection:</span></span>

[!code-csharp[Constructor](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

<span data-ttu-id="a0588-141">Sie verfügt über Methoden, um alle Items oder eine Item mit einem bestimmten Namen zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="a0588-141">It has methods to get all Items or an Item with a given name:</span></span>

[!code-csharp[Get](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

<span data-ttu-id="a0588-142">Es verfügt über eine Methode zum Hinzufügen eines neuen Item :</span><span class="sxs-lookup"><span data-stu-id="a0588-142">It has a method to add a new Item:</span></span>

[!code-csharp[PostItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

<span data-ttu-id="a0588-143">Eine Methode zum Markieren eines Item mit einer Bezeichnung:</span><span class="sxs-lookup"><span data-stu-id="a0588-143">A method to tag an Item with a label:</span></span>

[!code-csharp[PostTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

<span data-ttu-id="a0588-144">Und eine Methode zum Löschen eines Item und aller zugeordneten Tags :</span><span class="sxs-lookup"><span data-stu-id="a0588-144">And a method to delete an Item and all associated Tags:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

<span data-ttu-id="a0588-145">Die meisten Validierungs-und Fehlerbehandlung wurde entfernt, um die Übersichtlichkeit zu verringern.</span><span class="sxs-lookup"><span data-stu-id="a0588-145">Most validation and error handling have been removed to reduce clutter.</span></span>

## <a name="the-tests"></a><span data-ttu-id="a0588-146">Die Tests</span><span class="sxs-lookup"><span data-stu-id="a0588-146">The Tests</span></span>

<span data-ttu-id="a0588-147">Die Tests sind so organisiert, dass Sie mit mehreren Datenbankanbieter Konfigurationen ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="a0588-147">The tests are organized to run with multiple database provider configurations:</span></span>
* <span data-ttu-id="a0588-148">Der SQL Server-Anbieter, der der von der Anwendung verwendete Anbieter ist.</span><span class="sxs-lookup"><span data-stu-id="a0588-148">The SQL Server provider, which is the provider used by the application</span></span>
* <span data-ttu-id="a0588-149">Der SQLite-Anbieter</span><span class="sxs-lookup"><span data-stu-id="a0588-149">The SQLite provider</span></span>
* <span data-ttu-id="a0588-150">Der SQLite-Anbieter mit in-Memory-SQLite-Datenbanken</span><span class="sxs-lookup"><span data-stu-id="a0588-150">The SQLite provider using in-memory SQLite databases</span></span>
* <span data-ttu-id="a0588-151">Der EF in-Memory Database-Anbieter</span><span class="sxs-lookup"><span data-stu-id="a0588-151">The EF in-memory database provider</span></span>

<span data-ttu-id="a0588-152">Dies wird erreicht, indem alle Tests in einer Basisklasse vorgenommen werden und dann von diesem geerbt wird, um Sie mit jedem Anbieter zu testen.</span><span class="sxs-lookup"><span data-stu-id="a0588-152">This is achieved by putting all the tests in a base class, then inheriting from this to test with each provider.</span></span>

> [!TIP]
> <span data-ttu-id="a0588-153">Sie müssen die SQL Server Verbindungs Zeichenfolge ändern, wenn Sie nicht localdb verwenden.</span><span class="sxs-lookup"><span data-stu-id="a0588-153">You will need to change the SQL Server connection string if you're not using LocalDB.</span></span>

> [!TIP]
> <span data-ttu-id="a0588-154">Unter [Testen mit SQLite](xref:core/miscellaneous/testing/sqlite) finden Sie Anleitungen zur Verwendung von SQLite für in-Memory-Tests.</span><span class="sxs-lookup"><span data-stu-id="a0588-154">See [Testing with SQLite](xref:core/miscellaneous/testing/sqlite) for guidance on using SQLite for in-memory testing.</span></span> 

<span data-ttu-id="a0588-155">Es wird erwartet, dass die folgenden beiden Tests fehlschlagen:</span><span class="sxs-lookup"><span data-stu-id="a0588-155">The following two tests are expected to fail:</span></span>
* <span data-ttu-id="a0588-156">`Can_remove_item_and_all_associated_tags`bei Ausführung mit dem EF in-Memory Database-Anbieter</span><span class="sxs-lookup"><span data-stu-id="a0588-156">`Can_remove_item_and_all_associated_tags` when running with the EF in-memory database provider</span></span>
* <span data-ttu-id="a0588-157">`Can_add_item_differing_only_by_case`bei Ausführung mit dem SQL Server-Anbieter</span><span class="sxs-lookup"><span data-stu-id="a0588-157">`Can_add_item_differing_only_by_case` when running with the SQL Server provider</span></span>

<span data-ttu-id="a0588-158">Dies wird im folgenden ausführlicher beschrieben.</span><span class="sxs-lookup"><span data-stu-id="a0588-158">This is covered in more detail below.</span></span>

### <a name="setting-up-and-seeding-the-database"></a><span data-ttu-id="a0588-159">Einrichten und Seeding der Datenbank</span><span class="sxs-lookup"><span data-stu-id="a0588-159">Setting up and seeding the database</span></span>

<span data-ttu-id="a0588-160">XUnit erstellt, wie bei den meisten Test-Frameworks, eine neue Test Klasseninstanz für jeden Testlauf.</span><span class="sxs-lookup"><span data-stu-id="a0588-160">XUnit, like most testing frameworks, will create a new test class instance for each test run.</span></span>
<span data-ttu-id="a0588-161">Außerdem führt xUnit keine Tests in einer bestimmten Testklasse parallel aus.</span><span class="sxs-lookup"><span data-stu-id="a0588-161">Also, XUnit will not run tests within a given test class in parallel.</span></span> <span data-ttu-id="a0588-162">Dies bedeutet, dass wir die Datenbank im testkonstruktor einrichten und konfigurieren können. Sie werden für jeden Test in einem bekannten Zustand angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a0588-162">This means that we can setup and configure the database in the test constructor and it will be in a well-known state for each test.</span></span>

> [!TIP]
> <span data-ttu-id="a0588-163">In diesem Beispiel wird die Datenbank für jeden Test neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="a0588-163">This sample recreates the database for each test.</span></span>
> <span data-ttu-id="a0588-164">Dies funktioniert gut für SQLite-und EF-in-Memory Database Tests, kann jedoch mit anderen Datenbanksystemen, einschließlich SQL Server, einen erheblichen Aufwand verursachen.</span><span class="sxs-lookup"><span data-stu-id="a0588-164">This works well for SQLite and EF in-memory database testing but can involve significant overhead with other database systems, including SQL Server.</span></span>
> <span data-ttu-id="a0588-165">Die Vorgehensweisen zur Reduzierung dieses Aufwands werden bei der [gemeinsamen Nutzung von Datenbanken](xref:core/miscellaneous/testing/sharing-databases)behandelt.</span><span class="sxs-lookup"><span data-stu-id="a0588-165">Approaches for reducing this overhead are covered in [Sharing databases across tests](xref:core/miscellaneous/testing/sharing-databases).</span></span>

<span data-ttu-id="a0588-166">Beim Ausführen der einzelnen Tests:</span><span class="sxs-lookup"><span data-stu-id="a0588-166">When each test is run:</span></span>
* <span data-ttu-id="a0588-167">Dbcontextoptions wird für den verwendeten Anbieter konfiguriert und an den Basisklassenkonstruktor übergeben.</span><span class="sxs-lookup"><span data-stu-id="a0588-167">DbContextOptions are configured for the provider in use and passed to the base class constructor</span></span>
  * <span data-ttu-id="a0588-168">Diese Optionen werden in einer Eigenschaft gespeichert und in den Tests zum Erstellen von dbcontext-Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="a0588-168">These options are stored in a property and used throughout the tests for creating DbContext instances</span></span>
* <span data-ttu-id="a0588-169">Eine Seed-Methode wird aufgerufen, um die Datenbank zu erstellen und zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a0588-169">A Seed method is called to create and seed the database</span></span>
  * <span data-ttu-id="a0588-170">Mit der Seed-Methode wird sichergestellt, dass die Datenbank bereinigt wird, indem Sie gelöscht und anschließend neu erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="a0588-170">The Seed method ensures the database is clean by deleting it and then re-creating it</span></span>
  * <span data-ttu-id="a0588-171">Einige bekannte Test Entitäten werden erstellt und in der Datenbank gespeichert.</span><span class="sxs-lookup"><span data-stu-id="a0588-171">Some well-known test entities are created and saved to the database</span></span>

[!code-csharp[Seeding](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

<span data-ttu-id="a0588-172">Jede konkrete Testklasse erbt dann von diesem.</span><span class="sxs-lookup"><span data-stu-id="a0588-172">Each concrete test class then inherits from this.</span></span>
<span data-ttu-id="a0588-173">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a0588-173">For example:</span></span>

[!code-csharp[SqliteItemsControllerTest](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a><span data-ttu-id="a0588-174">Test Struktur</span><span class="sxs-lookup"><span data-stu-id="a0588-174">Test structure</span></span>

<span data-ttu-id="a0588-175">Obwohl die Anwendung eine Abhängigkeitsinjektion verwendet, ist dies bei den Tests nicht der Fall.</span><span class="sxs-lookup"><span data-stu-id="a0588-175">Even though the application uses dependency injection, the tests do not.</span></span>
<span data-ttu-id="a0588-176">Hier wäre es in Ordnung, die Abhängigkeitsinjektion zu verwenden, aber der zusätzliche Code, der benötigt wird, hat nur wenig Wert.</span><span class="sxs-lookup"><span data-stu-id="a0588-176">It would be fine to use dependency injection here, but the additional code it requires has little value.</span></span>
<span data-ttu-id="a0588-177">Stattdessen wird ein dbcontext mithilfe von erstellt `new` und dann direkt als Abhängigkeit an den Controller weitergegeben.</span><span class="sxs-lookup"><span data-stu-id="a0588-177">Instead, a DbContext is created using `new` and then directly passed as the dependency to the controller.</span></span>

<span data-ttu-id="a0588-178">Jeder Test führt dann die zu testende Methode auf dem Controller aus und bestätigt, dass die Ergebnisse erwartungsgemäß sind.</span><span class="sxs-lookup"><span data-stu-id="a0588-178">Each test then executes the method under test on the controller and asserts the results are as expected.</span></span>
<span data-ttu-id="a0588-179">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a0588-179">For example:</span></span>

[!code-csharp[CanGetItems](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

<span data-ttu-id="a0588-180">Beachten Sie, dass unterschiedliche dbcontext-Instanzen verwendet werden, um die Datenbank zu starten und die Tests auszuführen.</span><span class="sxs-lookup"><span data-stu-id="a0588-180">Notice that different DbContext instances are used to seed the database and run the tests.</span></span> <span data-ttu-id="a0588-181">Dadurch wird sichergestellt, dass der Test keine Entitäten verwendet, die vom Kontext nachverfolgt werden, wenn das Seeding durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a0588-181">This ensures that the test is not using (or tripping over) entities tracked by the context when seeding.</span></span>
<span data-ttu-id="a0588-182">Außerdem ist es besser, was in Web-Apps und-Diensten passiert.</span><span class="sxs-lookup"><span data-stu-id="a0588-182">It also better matches what happens in web apps and services.</span></span>

<span data-ttu-id="a0588-183">Durch Tests, die die Datenbank mutieren, wird aus ähnlichen Gründen eine zweite dbcontext-Instanz im Test erstellt.</span><span class="sxs-lookup"><span data-stu-id="a0588-183">Tests that mutate the database create a second DbContext instance in the test for similar reasons.</span></span>
<span data-ttu-id="a0588-184">Das heißt, Sie erstellen einen neuen, sauberen Kontext und lesen ihn aus der Datenbank, um sicherzustellen, dass die Änderungen in der Datenbank gespeichert wurden.</span><span class="sxs-lookup"><span data-stu-id="a0588-184">That is, creating a new, clean, context and then reading into it from the database to ensure that the changes were saved to the database.</span></span> <span data-ttu-id="a0588-185">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a0588-185">For example:</span></span>

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

<span data-ttu-id="a0588-186">Die Geschäftslogik zum Hinzufügen von Tags wird durch zwei etwas mehr beteiligte Tests abgedeckt.</span><span class="sxs-lookup"><span data-stu-id="a0588-186">Two slightly more involved tests cover the business logic around adding tags.</span></span>

[!code-csharp[CanAddTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a><span data-ttu-id="a0588-187">Probleme bei der Verwendung verschiedener Datenbankanbieter</span><span class="sxs-lookup"><span data-stu-id="a0588-187">Issues using different database providers</span></span>

<span data-ttu-id="a0588-188">Das Testen mit einem anderen Datenbanksystem, das in der Produktionsanwendung verwendet wird, kann zu Problemen führen.</span><span class="sxs-lookup"><span data-stu-id="a0588-188">Testing with a different database system than is used in the production application can lead to problems.</span></span>
<span data-ttu-id="a0588-189">Diese werden auf der konzeptionellen Ebene im [Testcode behandelt, der EF Core verwendet](xref:core/miscellaneous/testing/index).</span><span class="sxs-lookup"><span data-stu-id="a0588-189">These are covered at the conceptual level in [Testing code that uses EF Core](xref:core/miscellaneous/testing/index).</span></span>  
<span data-ttu-id="a0588-190">In den folgenden Abschnitten werden zwei Beispiele für solche Probleme behandelt, die von den Tests in diesem Beispiel demonstriert werden.</span><span class="sxs-lookup"><span data-stu-id="a0588-190">The sections below cover two examples of such issues demonstrated by the tests in this sample.</span></span>

### <a name="test-passes-when-the-application-is-broken"></a><span data-ttu-id="a0588-191">Test läuft, wenn die Anwendung beschädigt ist</span><span class="sxs-lookup"><span data-stu-id="a0588-191">Test passes when the application is broken</span></span>

<span data-ttu-id="a0588-192">Eine der Anforderungen für unsere Anwendung besteht darin, dass "die Items groß-und Kleinschreibung beachtet und eine Auflistung von" beachtet werden muss Tags .</span><span class="sxs-lookup"><span data-stu-id="a0588-192">One of the requirements for our application is that "Items have a case-sensitive name and a collection of Tags."</span></span>
<span data-ttu-id="a0588-193">Dies ist ziemlich einfach zu testen:</span><span class="sxs-lookup"><span data-stu-id="a0588-193">This is pretty simple to test:</span></span>

[!code-csharp[CanAddItemCaseInsensitive](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

<span data-ttu-id="a0588-194">Das Ausführen dieses Tests für den EF-in-Memory Database gibt an, dass alles in Ordnung ist.</span><span class="sxs-lookup"><span data-stu-id="a0588-194">Running this test against the EF in-memory database indicates that everything is fine.</span></span>
<span data-ttu-id="a0588-195">Bei der Verwendung von SQLite ist alles weiterhin einwandfrei.</span><span class="sxs-lookup"><span data-stu-id="a0588-195">Everything still looks fine when using SQLite.</span></span>
<span data-ttu-id="a0588-196">Der Test schlägt jedoch fehl, wenn er auf SQL Server ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a0588-196">But the test fails when run against SQL Server!</span></span>

```
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

<span data-ttu-id="a0588-197">Dies liegt daran, dass der EF-in-Memory Database und die SQLite-Datenbank standardmäßig die Groß-/Kleinschreibung beachten.</span><span class="sxs-lookup"><span data-stu-id="a0588-197">This is because both the EF in-memory database and the SQLite database are case-sensitive by default.</span></span>
<span data-ttu-id="a0588-198">SQL Server hingegen wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="a0588-198">SQL Server, on the other hand, is case-insensitive!</span></span> 

<span data-ttu-id="a0588-199">EF Core ändert diese Verhaltensweisen nicht, da das Erzwingen einer Änderung bei der Unterscheidung nach Groß-/Kleinschreibung eine erhebliche Auswirkung auf die Leistung haben kann.</span><span class="sxs-lookup"><span data-stu-id="a0588-199">EF Core, by design, does not change these behaviors because forcing a change in case-sensitivity can have a big performance impact.</span></span>

<span data-ttu-id="a0588-200">Sobald wir wissen, dass es sich um ein Problem handelt, können wir die Anwendung beheben und Tests kompensieren.</span><span class="sxs-lookup"><span data-stu-id="a0588-200">Once we know this is a problem we can fix the application and compensate in tests.</span></span>
<span data-ttu-id="a0588-201">Der Punkt hier ist jedoch, dass dieser Fehler übersehen werden kann, wenn nur der EF-in-Memory Database oder SQLite-Anbieter getestet wird.</span><span class="sxs-lookup"><span data-stu-id="a0588-201">However, the point here is that this bug could be missed if only testing with the EF in-memory database or SQLite providers.</span></span>

### <a name="test-fails-when-the-application-is-correct"></a><span data-ttu-id="a0588-202">Test schlägt fehl, wenn die Anwendung korrekt ist</span><span class="sxs-lookup"><span data-stu-id="a0588-202">Test fails when the application is correct</span></span> 

<span data-ttu-id="a0588-203">Eine weitere Anforderung für die Anwendung besteht darin, dass das Löschen eines Item alle zugeordneten löschen soll Tags .</span><span class="sxs-lookup"><span data-stu-id="a0588-203">Another of the requirements for our application is that "deleting an Item should delete all associated Tags."</span></span>
<span data-ttu-id="a0588-204">Auch hier leicht zu testen:</span><span class="sxs-lookup"><span data-stu-id="a0588-204">Again, easy to test:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

<span data-ttu-id="a0588-205">Dieser Test wird SQL Server und SQLite bestanden, schlägt jedoch mit dem EF-in-Memory Database fehl!</span><span class="sxs-lookup"><span data-stu-id="a0588-205">This test passes on SQL Server and SQLite, but fails with the EF in-memory database!</span></span>

```
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

<span data-ttu-id="a0588-206">In diesem Fall funktioniert die Anwendung ordnungsgemäß, da SQL Server [kaskadierte](xref:core/saving/cascade-delete)Löschvorgänge unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a0588-206">In this case, the application is working correctly because SQL Server supports [cascade deletes](xref:core/saving/cascade-delete).</span></span> <span data-ttu-id="a0588-207">SQLite unterstützt auch das Löschen von Lösch Vorgängen, wie bei den meisten relationalen Datenbanken, sodass das Testen auf SQLite funktioniert.</span><span class="sxs-lookup"><span data-stu-id="a0588-207">SQLite also supports cascade deletes, as do most relational databases, so testing this on SQLite works.</span></span>
<span data-ttu-id="a0588-208">Auf der anderen Seite unterstützt das EF-in-Memory Database [keine kaskadierenden](https://github.com/dotnet/efcore/issues/3924)Löschvorgänge.</span><span class="sxs-lookup"><span data-stu-id="a0588-208">On the other hand, the EF in-memory database [does not support cascade deletes](https://github.com/dotnet/efcore/issues/3924).</span></span>
<span data-ttu-id="a0588-209">Dies bedeutet, dass dieser Teil der Anwendung nicht mit dem EF in-Memory Database-Anbieter getestet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a0588-209">This means that this part of the application cannot be tested with the EF in-memory database provider.</span></span>
