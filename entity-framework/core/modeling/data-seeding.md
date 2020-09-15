---
title: Datenseeding-EF Core
description: Verwenden von datenseeding zum Auffüllen einer Datenbank mit einem anfänglichen Satz von Daten mithilfe von Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/02/2018
uid: core/modeling/data-seeding
ms.openlocfilehash: 1d7adbe45c4cbc64a39485c76d8f516e32ffeba5
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071601"
---
# <a name="data-seeding"></a><span data-ttu-id="c6aa0-103">Datenseeding</span><span class="sxs-lookup"><span data-stu-id="c6aa0-103">Data Seeding</span></span>

<span data-ttu-id="c6aa0-104">Datenseeding ist der Vorgang, bei dem eine Datenbank mit einem anfänglichen Satz von Daten gefüllt wird.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-104">Data seeding is the process of populating a database with an initial set of data.</span></span>

<span data-ttu-id="c6aa0-105">Es gibt mehrere Möglichkeiten, wie dies in EF Core erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="c6aa0-105">There are several ways this can be accomplished in EF Core:</span></span>

* <span data-ttu-id="c6aa0-106">Modellseed-Daten</span><span class="sxs-lookup"><span data-stu-id="c6aa0-106">Model seed data</span></span>
* <span data-ttu-id="c6aa0-107">Anpassung der manuellen Migration</span><span class="sxs-lookup"><span data-stu-id="c6aa0-107">Manual migration customization</span></span>
* <span data-ttu-id="c6aa0-108">Benutzerdefinierte Initialisierungs Logik</span><span class="sxs-lookup"><span data-stu-id="c6aa0-108">Custom initialization logic</span></span>

## <a name="model-seed-data"></a><span data-ttu-id="c6aa0-109">Modellseed-Daten</span><span class="sxs-lookup"><span data-stu-id="c6aa0-109">Model seed data</span></span>

> [!NOTE]
> <span data-ttu-id="c6aa0-110">Dieses Feature ist neu in EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-110">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="c6aa0-111">Anders als in EF6 kann das Seeding von Daten in EF Core einem Entitätstyp als Teil der Modell Konfiguration zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-111">Unlike in EF6, in EF Core, seeding data can be associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="c6aa0-112">Anschließend können EF Core [Migrationen](xref:core/managing-schemas/migrations/index) automatisch berechnen, welche INSERT-, Update-oder DELETE-Vorgänge angewendet werden müssen, wenn die Datenbank auf eine neue Version des Modells aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-112">Then EF Core [migrations](xref:core/managing-schemas/migrations/index) can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

> [!NOTE]
> <span data-ttu-id="c6aa0-113">Bei Migrationen werden Modelländerungen nur berücksichtigt, wenn bestimmt wird, welcher Vorgang ausgeführt werden soll, um die Ausgangsdaten in den gewünschten Zustand zu bringen.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-113">Migrations only considers model changes when determining what operation should be performed to get the seed data into the desired state.</span></span> <span data-ttu-id="c6aa0-114">Daher können alle Änderungen an den Daten, die außerhalb der Migrationen durchgeführt werden, verloren gehen oder einen Fehler verursachen.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-114">Thus any changes to the data performed outside of migrations might be lost or cause an error.</span></span>

<span data-ttu-id="c6aa0-115">So werden z. b. Seed-Daten für einen `Blog` in konfiguriert `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="c6aa0-115">As an example, this will configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="c6aa0-116">Um Entitäten hinzuzufügen, die eine Beziehung aufweisen, müssen die Fremdschlüssel Werte angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="c6aa0-116">To add entities that have a relationship the foreign key values need to be specified:</span></span>

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

<span data-ttu-id="c6aa0-117">Wenn der Entitätstyp über Eigenschaften im Schatten Zustand verfügt, kann eine anonyme Klasse verwendet werden, um die Werte bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="c6aa0-117">If the entity type has any properties in shadow state an anonymous class can be used to provide the values:</span></span>

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

<span data-ttu-id="c6aa0-118">Eigene Entitäts Typen können auf ähnliche Weise als Seeding betrachtet werden:</span><span class="sxs-lookup"><span data-stu-id="c6aa0-118">Owned entity types can be seeded in a similar fashion:</span></span>

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

<span data-ttu-id="c6aa0-119">Weitere Informationen finden Sie im [vollständigen Beispiel Projekt](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) .</span><span class="sxs-lookup"><span data-stu-id="c6aa0-119">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) for more context.</span></span>

<span data-ttu-id="c6aa0-120">Nachdem die Daten dem Modell hinzugefügt [wurden, sollten](xref:core/managing-schemas/migrations/index) die Änderungen verwendet werden, um die Änderungen zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-120">Once the data has been added to the model, [migrations](xref:core/managing-schemas/migrations/index) should be used to apply the changes.</span></span>

> [!TIP]
> <span data-ttu-id="c6aa0-121">Wenn Sie Migrationen als Teil einer automatisierten Bereitstellung anwenden müssen, können Sie [ein SQL-Skript erstellen](xref:core/managing-schemas/migrations/index#generate-sql-scripts) , das vor der Ausführung in der Vorschau angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-121">If you need to apply migrations as part of an automated deployment you can [create a SQL script](xref:core/managing-schemas/migrations/index#generate-sql-scripts) that can be previewed before execution.</span></span>

<span data-ttu-id="c6aa0-122">Alternativ können Sie verwenden, `context.Database.EnsureCreated()` um eine neue Datenbank zu erstellen, die die Ausgangsdaten enthält, z. b. für eine Testdatenbank, oder wenn Sie den in-Memory-Anbieter oder eine nicht-beziehungsdatenbank verwenden.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-122">Alternatively, you can use `context.Database.EnsureCreated()` to create a new database containing the seed data, for example for a test database or when using the in-memory provider or any non-relation database.</span></span> <span data-ttu-id="c6aa0-123">Beachten Sie Folgendes: Wenn die Datenbank bereits vorhanden ist, `EnsureCreated()` aktualisiert weder das Schema noch die Seed-Daten in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-123">Note that if the database already exists, `EnsureCreated()` will neither update the schema nor seed data in the database.</span></span> <span data-ttu-id="c6aa0-124">Bei relationalen Datenbanken sollten Sie nicht anrufen `EnsureCreated()` , wenn Sie beabsichtigen, Migrationen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-124">For relational databases you shouldn't call `EnsureCreated()` if you plan to use Migrations.</span></span>

### <a name="limitations-of-model-seed-data"></a><span data-ttu-id="c6aa0-125">Einschränkungen von modellseed-Daten</span><span class="sxs-lookup"><span data-stu-id="c6aa0-125">Limitations of model seed data</span></span>

<span data-ttu-id="c6aa0-126">Diese Art von Ausgangsdaten wird durch Migrationen verwaltet, und das Skript zum Aktualisieren der Daten, die bereits in der Datenbank vorhanden sind, muss generiert werden, ohne dass eine Verbindung mit der Datenbank hergestellt werden muss.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-126">This type of seed data is managed by migrations and the script to update the data that's already in the database needs to be generated without connecting to the database.</span></span> <span data-ttu-id="c6aa0-127">Dies setzt einige Einschränkungen mit sich:</span><span class="sxs-lookup"><span data-stu-id="c6aa0-127">This imposes some restrictions:</span></span>

* <span data-ttu-id="c6aa0-128">Der Primärschlüssel Wert muss angegeben werden, auch wenn er normalerweise von der Datenbank generiert wird.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-128">The primary key value needs to be specified even if it's usually generated by the database.</span></span> <span data-ttu-id="c6aa0-129">Es wird verwendet, um Datenänderungen zwischen Migrationen zu erkennen.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-129">It will be used to detect data changes between migrations.</span></span>
* <span data-ttu-id="c6aa0-130">Zuvor Seeding Daten werden entfernt, wenn der Primärschlüssel in irgendeiner Weise geändert wird.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-130">Previously seeded data will be removed if the primary key is changed in any way.</span></span>

<span data-ttu-id="c6aa0-131">Daher ist diese Funktion besonders nützlich für statische Daten, die sich außerhalb der Migrationen nicht ändern sollten, und ist nicht von anderen in der Datenbank abhängig, z. b. Postleitzahlen.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-131">Therefore this feature is most useful for static data that's not expected to change outside of migrations and does not depend on anything else in the database, for example ZIP codes.</span></span>

<span data-ttu-id="c6aa0-132">Wenn in Ihrem Szenario eine der folgenden Szenarien enthalten ist, wird empfohlen, die im letzten Abschnitt beschriebene benutzerdefinierte Initialisierungs Logik zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="c6aa0-132">If your scenario includes any of the following it is recommended to use custom initialization logic described in the last section:</span></span>

* <span data-ttu-id="c6aa0-133">Temporäre Daten für Tests</span><span class="sxs-lookup"><span data-stu-id="c6aa0-133">Temporary data for testing</span></span>
* <span data-ttu-id="c6aa0-134">Daten, die vom Daten Bank Status abhängen</span><span class="sxs-lookup"><span data-stu-id="c6aa0-134">Data that depends on database state</span></span>
* <span data-ttu-id="c6aa0-135">Daten, für die Schlüsselwerte benötigt werden, die von der Datenbank generiert werden müssen, einschließlich der Entitäten, die Alternative Schlüssel als Identität verwenden</span><span class="sxs-lookup"><span data-stu-id="c6aa0-135">Data that needs key values to be generated by the database, including entities that use alternate keys as the identity</span></span>
* <span data-ttu-id="c6aa0-136">Daten, die eine benutzerdefinierte Transformation erfordern (die nicht durch [Wert Konvertierungen](xref:core/modeling/value-conversions)behandelt wird), z. b. ein Kennwort-Hashwert</span><span class="sxs-lookup"><span data-stu-id="c6aa0-136">Data that requires custom transformation (that is not handled by [value conversions](xref:core/modeling/value-conversions)), such as some password hashing</span></span>
* <span data-ttu-id="c6aa0-137">Daten, für die Aufrufe externer API erforderlich sind, z. b. ASP.net Core Identitäts Rollen und Benutzer Erstellung</span><span class="sxs-lookup"><span data-stu-id="c6aa0-137">Data that requires calls to external API, such as ASP.NET Core Identity roles and users creation</span></span>

## <a name="manual-migration-customization"></a><span data-ttu-id="c6aa0-138">Anpassung der manuellen Migration</span><span class="sxs-lookup"><span data-stu-id="c6aa0-138">Manual migration customization</span></span>

<span data-ttu-id="c6aa0-139">Beim Hinzufügen einer Migration werden die Änderungen an den mit angegebenen Daten `HasData` in Aufrufe von `InsertData()` , und transformiert `UpdateData()` `DeleteData()` .</span><span class="sxs-lookup"><span data-stu-id="c6aa0-139">When a migration is added the changes to the data specified with `HasData` are transformed to calls to `InsertData()`, `UpdateData()`, and `DeleteData()`.</span></span> <span data-ttu-id="c6aa0-140">Eine Möglichkeit, einige der Einschränkungen von `HasData` zu umgehen, besteht darin, diese Aufrufe oder [benutzerdefinierten Vorgänge](xref:core/managing-schemas/migrations/operations) stattdessen manuell zur Migration hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-140">One way of working around some of the limitations of `HasData` is to manually add these calls or [custom operations](xref:core/managing-schemas/migrations/operations) to the migration instead.</span></span>

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a><span data-ttu-id="c6aa0-141">Benutzerdefinierte Initialisierungs Logik</span><span class="sxs-lookup"><span data-stu-id="c6aa0-141">Custom initialization logic</span></span>

<span data-ttu-id="c6aa0-142">Eine einfache und leistungsfähige Methode zum Durchführen von datenseeding ist die Verwendung von, [`DbContext.SaveChanges()`](xref:core/saving/index) bevor die Haupt Anwendungslogik mit der Ausführung beginnt.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-142">A straightforward and powerful way to perform data seeding is to use [`DbContext.SaveChanges()`](xref:core/saving/index) before the main application logic begins execution.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> <span data-ttu-id="c6aa0-143">Der Seeding Code sollte nicht Teil der normalen App-Ausführung sein, da dies zu Parallelitäts Problemen führen kann, wenn mehrere Instanzen ausgeführt werden und die APP ebenfalls über die Berechtigung zum Ändern des Datenbankschemas verfügen würde.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-143">The seeding code should not be part of the normal app execution as this can cause concurrency issues when multiple instances are running and would also require the app having permission to modify the database schema.</span></span>

<span data-ttu-id="c6aa0-144">Abhängig von den Einschränkungen der Bereitstellung kann der Initialisierungs Code auf unterschiedliche Weise ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="c6aa0-144">Depending on the constraints of your deployment the initialization code can be executed in different ways:</span></span>

* <span data-ttu-id="c6aa0-145">Lokales Ausführen der Initialisierungs-App</span><span class="sxs-lookup"><span data-stu-id="c6aa0-145">Running the initialization app locally</span></span>
* <span data-ttu-id="c6aa0-146">Stellen Sie die Initialisierungs-App mit der Haupt-App bereit, indem Sie die Initialisierungs Routine aufrufen und die Initialisierungs-App deaktivieren oder entfernen.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-146">Deploying the initialization app with the main app, invoking the initialization routine and disabling or removing the initialization app.</span></span>

<span data-ttu-id="c6aa0-147">Dies kann normalerweise mithilfe von [Veröffentlichungs Profilen](/aspnet/core/host-and-deploy/visual-studio-publish-profiles)automatisiert werden.</span><span class="sxs-lookup"><span data-stu-id="c6aa0-147">This can usually be automated by using [publish profiles](/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span></span>
