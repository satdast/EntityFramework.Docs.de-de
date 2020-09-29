---
title: 'Azure Cosmos DB-Anbieter: EF Core'
description: Dokumentation für den Datenbankanbieter, mit der Entity Framework Core mit der SQL-API von Azure Cosmos DB verwendet werden kann
author: AndriySvyryd
ms.date: 09/14/2020
uid: core/providers/cosmos/index
ms.openlocfilehash: 94ba29f3f2643e8f563a460e17dce9d15cb7c2df
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210340"
---
# <a name="ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="4a14c-103">EF Core Azure Cosmos DB-Anbieter</span><span class="sxs-lookup"><span data-stu-id="4a14c-103">EF Core Azure Cosmos DB Provider</span></span>

> [!NOTE]
> <span data-ttu-id="4a14c-104">Dieser Anbieter ist neu in EF Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="4a14c-104">This provider is new in EF Core 3.0.</span></span>

<span data-ttu-id="4a14c-105">Dieser Datenbankanbieter ermöglicht die Verwendung von Entity Framework Core mit Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="4a14c-105">This database provider allows Entity Framework Core to be used with Azure Cosmos DB.</span></span> <span data-ttu-id="4a14c-106">Dieser Anbieter wird nicht im Rahmen des [Entity Framework Core-Projekts](https://github.com/aspnet/EntityFrameworkCore) verwaltet.</span><span class="sxs-lookup"><span data-stu-id="4a14c-106">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

<span data-ttu-id="4a14c-107">Es wird dringend empfohlen, sich vor dem Lesen dieses Abschnitts mit der [Azure Cosmos DB-Dokumentation](/azure/cosmos-db/introduction) vertraut zu machen.</span><span class="sxs-lookup"><span data-stu-id="4a14c-107">It is strongly recommended to familiarize yourself with the [Azure Cosmos DB documentation](/azure/cosmos-db/introduction) before reading this section.</span></span>

> [!NOTE]
> <span data-ttu-id="4a14c-108">Dieser Anbieter kann nur mit der SQL-API von Azure Cosmos DB verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4a14c-108">This provider only works with the SQL API of Azure Cosmos DB.</span></span>

## <a name="install"></a><span data-ttu-id="4a14c-109">Installieren</span><span class="sxs-lookup"><span data-stu-id="4a14c-109">Install</span></span>

<span data-ttu-id="4a14c-110">Installieren Sie das [NuGet-Paket „Microsoft.EntityFrameworkCore.Cosmos“](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).</span><span class="sxs-lookup"><span data-stu-id="4a14c-110">Install the [Microsoft.EntityFrameworkCore.Cosmos NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="4a14c-111">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="4a14c-111">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Cosmos
```

### <a name="visual-studio"></a>[<span data-ttu-id="4a14c-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4a14c-112">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Cosmos
```

***

## <a name="get-started"></a><span data-ttu-id="4a14c-113">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="4a14c-113">Get started</span></span>

> [!TIP]  
> <span data-ttu-id="4a14c-114">Das in diesem Artikel verwendete [Beispiel finden Sie auf GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Cosmos).</span><span class="sxs-lookup"><span data-stu-id="4a14c-114">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Cosmos).</span></span>

<span data-ttu-id="4a14c-115">Wie bei anderen Anbietern besteht der erste Schritt darin, [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos) aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="4a14c-115">As for other providers the first step is to call [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos):</span></span>

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Configuration)]

> [!WARNING]
> <span data-ttu-id="4a14c-116">Der Endpunkt und der Schlüssel sind aus Gründen der Einfachheit hier hartcodiert, aber in einer Produktions-App sollten sie [sicher gespeichert werden](/aspnet/core/security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="4a14c-116">The endpoint and key are hardcoded here for simplicity, but in a production app these should be [stored securely](/aspnet/core/security/app-secrets#secret-manager).</span></span>

<span data-ttu-id="4a14c-117">In diesem Beispiel ist `Order` eine einfache Entität mit einem Verweis auf den [nicht eigenständigen Typ ](xref:core/modeling/owned-entities) `StreetAddress`.</span><span class="sxs-lookup"><span data-stu-id="4a14c-117">In this example `Order` is a simple entity with a reference to the [owned type](xref:core/modeling/owned-entities) `StreetAddress`.</span></span>

[!code-csharp[Order](../../../../samples/core/Cosmos/ModelBuilding/Order.cs?name=Order)]

[!code-csharp[StreetAddress](../../../../samples/core/Cosmos/ModelBuilding/StreetAddress.cs?name=StreetAddress)]

<span data-ttu-id="4a14c-118">Das Speichern und Abfragen von Daten folgt dem normalen EF-Muster:</span><span class="sxs-lookup"><span data-stu-id="4a14c-118">Saving and querying data follows the normal EF pattern:</span></span>

[!code-csharp[HelloCosmos](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=HelloCosmos)]

> [!IMPORTANT]
> <span data-ttu-id="4a14c-119">Der Aufruf von [EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync) ist erforderlich, um die erforderlichen Container zu erstellen und die [Seeddaten](xref:core/modeling/data-seeding) einzufügen, wenn Sie im Modell vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="4a14c-119">Calling [EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync) is necessary to create the required containers and insert the [seed data](xref:core/modeling/data-seeding) if present in the model.</span></span> <span data-ttu-id="4a14c-120">`EnsureCreatedAsync` sollte jedoch nur während der Bereitstellung und nicht im normalen Betrieb aufgerufen werden, da dies zu Leistungsproblemen führen kann.</span><span class="sxs-lookup"><span data-stu-id="4a14c-120">However `EnsureCreatedAsync` should only be called during deployment, not normal operation, as it may cause performance issues.</span></span>

## <a name="cosmos-options"></a><span data-ttu-id="4a14c-121">Cosmos-Optionen</span><span class="sxs-lookup"><span data-stu-id="4a14c-121">Cosmos options</span></span>

<span data-ttu-id="4a14c-122">Es ist auch möglich, den Cosmos DB-Anbieter mit einer einzelnen Verbindungszeichenfolge zu konfigurieren und andere Optionen zum Anpassen der Verbindung anzugeben:</span><span class="sxs-lookup"><span data-stu-id="4a14c-122">It is also possible to configure the Cosmos DB provider with a single connection string and to specify other options to customize the connection:</span></span>

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OptionsContext.cs?name=Configuration)]

> [!NOTE]
> <span data-ttu-id="4a14c-123">Die meisten dieser Optionen sind in EF Core Cosmos 5.0 neu.</span><span class="sxs-lookup"><span data-stu-id="4a14c-123">Most of these options are new in EF Core Cosmos 5.0.</span></span>

> [!TIP]
> <span data-ttu-id="4a14c-124">In der Dokumentation zu [Azure Cosmos DB-Optionen](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions) finden Sie eine ausführliche Beschreibung der Auswirkungen der einzelnen oben genannten Optionen.</span><span class="sxs-lookup"><span data-stu-id="4a14c-124">See the [Azure Cosmos DB Options documentation](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions) for a detailed description of the effect of each option mentioned above.</span></span>

## <a name="cosmos-specific-model-customization"></a><span data-ttu-id="4a14c-125">Cosmos-spezifische Modellanpassung</span><span class="sxs-lookup"><span data-stu-id="4a14c-125">Cosmos-specific model customization</span></span>

<span data-ttu-id="4a14c-126">Standardmäßig werden alle Entitätstypen demselben Container zugeordnet, der nach dem abgeleiteten Kontext benannt wird (in diesem Fall `"OrderContext"`).</span><span class="sxs-lookup"><span data-stu-id="4a14c-126">By default all entity types are mapped to the same container, named after the derived context (`"OrderContext"` in this case).</span></span> <span data-ttu-id="4a14c-127">Verwenden Sie zum Ändern des Standardcontainernamens [HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer):</span><span class="sxs-lookup"><span data-stu-id="4a14c-127">To change the default container name use [HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer):</span></span>

[!code-csharp[DefaultContainer](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=DefaultContainer)]

<span data-ttu-id="4a14c-128">Um einen Entitätstyp einem anderen Container zuzuordnen, verwenden Sie [ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer):</span><span class="sxs-lookup"><span data-stu-id="4a14c-128">To map an entity type to a different container use [ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer):</span></span>

[!code-csharp[Container](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Container)]

<span data-ttu-id="4a14c-129">Um den Entitätstyp zu identifizieren, den ein bestimmtes Element darstellt, fügt EF Core einen Diskriminatorwert auch dann hinzu, wenn keine abgeleiteten Entitätstypen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="4a14c-129">To identify the entity type that a given item represent EF Core adds a discriminator value even if there are no derived entity types.</span></span> <span data-ttu-id="4a14c-130">Der Name und der Wert des Diskriminators [können geändert werden](xref:core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="4a14c-130">The name and value of the discriminator [can be changed](xref:core/modeling/inheritance).</span></span>

<span data-ttu-id="4a14c-131">Wenn kein anderer Entitätstyp jemals im gleichen Container gespeichert wird, kann der Diskriminator durch Aufruf von [HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator) entfernt werden:</span><span class="sxs-lookup"><span data-stu-id="4a14c-131">If no other entity type will ever be stored in the same container the discriminator can be removed by calling [HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator):</span></span>

[!code-csharp[NoDiscriminator](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=NoDiscriminator)]

### <a name="partition-keys"></a><span data-ttu-id="4a14c-132">Partitionsschlüssel</span><span class="sxs-lookup"><span data-stu-id="4a14c-132">Partition keys</span></span>

<span data-ttu-id="4a14c-133">Standardmäßig erstellt EF Core Container, bei denen der Partitionsschlüssel auf `"__partitionKey"` festgelegt ist, ohne beim Einfügen von Elementen einen Wert dafür anzugeben.</span><span class="sxs-lookup"><span data-stu-id="4a14c-133">By default EF Core will create containers with the partition key set to `"__partitionKey"` without supplying any value for it when inserting items.</span></span> <span data-ttu-id="4a14c-134">Um die Leistungsfunktionen von Azure Cosmos vollständig nutzen zu können, sollten Sie jedoch einen [sorgfältig ausgewählten Partitionsschlüssel](/azure/cosmos-db/partition-data) verwenden.</span><span class="sxs-lookup"><span data-stu-id="4a14c-134">But to fully leverage the performance capabilities of Azure Cosmos a [carefully selected partition key](/azure/cosmos-db/partition-data) should be used.</span></span> <span data-ttu-id="4a14c-135">Dieser kann durch Aufrufen von [HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey) konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="4a14c-135">It can be configured by calling [HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey):</span></span>

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PartitionKey)]

> [!NOTE]
><span data-ttu-id="4a14c-136">Als Eigenschaft des Partitionsschlüssels kann ein beliebiger Typ verwendet werden, sofern dieser in [eine Zeichenfolge konvertiert wird](xref:core/modeling/value-conversions).</span><span class="sxs-lookup"><span data-stu-id="4a14c-136">The partition key property can be of any type as long as it is [converted to string](xref:core/modeling/value-conversions).</span></span>

<span data-ttu-id="4a14c-137">Nach der Konfiguration muss die Partitionsschlüsseleigenschaft immer einen Wert aufweisen, der nicht NULL ist.</span><span class="sxs-lookup"><span data-stu-id="4a14c-137">Once configured the partition key property should always have a non-null value.</span></span> <span data-ttu-id="4a14c-138">Beim Ausgeben einer Abfrage kann eine Bedingung hinzugefügt werden, um eine einzelne Partition zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4a14c-138">When issuing a query a condition can be added to make it single-partition.</span></span>

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=PartitionKey)]

## <a name="embedded-entities"></a><span data-ttu-id="4a14c-139">Eingebettete Entitäten</span><span class="sxs-lookup"><span data-stu-id="4a14c-139">Embedded entities</span></span>

<span data-ttu-id="4a14c-140">Nicht eigenständige Cosmos-Entitäten sind in das gleiche Element wie der Besitzer eingebettet.</span><span class="sxs-lookup"><span data-stu-id="4a14c-140">For Cosmos, owned entities are embedded in the same item as the owner.</span></span> <span data-ttu-id="4a14c-141">Verwenden Sie zum Ändern eines Eigenschaftennamens [ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty):</span><span class="sxs-lookup"><span data-stu-id="4a14c-141">To change a property name use [ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty):</span></span>

[!code-csharp[PropertyNames](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PropertyNames)]

<span data-ttu-id="4a14c-142">Mit dieser Konfiguration wird die Reihenfolge des obigen Beispiels wie folgt gespeichert:</span><span class="sxs-lookup"><span data-stu-id="4a14c-142">With this configuration the order from the example above is stored like this:</span></span>

``` json
{
    "Id": 1,
    "PartitionKey": "1",
    "TrackingNumber": null,
    "id": "1",
    "Address": {
        "ShipsToCity": "London",
        "ShipsToStreet": "221 B Baker St"
    },
    "_rid": "6QEKAM+BOOABAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKAM+BOOA=/docs/6QEKAM+BOOABAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-692e763901d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163674
}
```

<span data-ttu-id="4a14c-143">Sammlungen von nicht eigenständigen Entitäten werden ebenfalls eingebettet.</span><span class="sxs-lookup"><span data-stu-id="4a14c-143">Collections of owned entities are embedded as well.</span></span> <span data-ttu-id="4a14c-144">Im nächsten Beispiel verwenden wir die `Distributor`-Klasse mit einer Sammlung von `StreetAddress`:</span><span class="sxs-lookup"><span data-stu-id="4a14c-144">For the next example we'll use the `Distributor` class with a collection of `StreetAddress`:</span></span>

[!code-csharp[Distributor](../../../../samples/core/Cosmos/ModelBuilding/Distributor.cs?name=Distributor)]

<span data-ttu-id="4a14c-145">Die nicht eigenständigen Entitäten müssen keine expliziten Schlüsselwerte angeben, die gespeichert werden sollen:</span><span class="sxs-lookup"><span data-stu-id="4a14c-145">The owned entities don't need to provide explicit key values to be stored:</span></span>

[!code-csharp[OwnedCollection](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=OwnedCollection)]

<span data-ttu-id="4a14c-146">Sie werden auf diese Weise persistent gespeichert:</span><span class="sxs-lookup"><span data-stu-id="4a14c-146">They will be persisted in this way:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Distributor",
    "id": "Distributor|1",
    "ShippingCenters": [
        {
            "City": "Phoenix",
            "Street": "500 S 48th Street"
        },
        {
            "City": "Anaheim",
            "Street": "5650 Dolly Ave"
        }
    ],
    "_rid": "6QEKANzISj0BAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKANzISj0=/docs/6QEKANzISj0BAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-7b2b439701d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163705
}
```

<span data-ttu-id="4a14c-147">Intern muss EF Core für alle nachverfolgten Entitäten immer eindeutige Schlüsselwerte aufweisen.</span><span class="sxs-lookup"><span data-stu-id="4a14c-147">Internally EF Core always needs to have unique key values for all tracked entities.</span></span> <span data-ttu-id="4a14c-148">Der Primärschlüssel, der standardmäßig für Sammlungen nicht eigenständiger Typen erstellt wird, besteht aus den Fremdschlüsseleigenschaften, die auf den Besitzer verweisen, und einer `int`-Eigenschaft, die dem Index im JSON-Array entspricht.</span><span class="sxs-lookup"><span data-stu-id="4a14c-148">The primary key created by default for collections of owned types consists of the foreign key properties pointing to the owner and an `int` property corresponding to the index in the JSON array.</span></span> <span data-ttu-id="4a14c-149">Zum Abrufen dieser Werte kann die Eingabe-API verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="4a14c-149">To retrieve these values entry API could be used:</span></span>

[!code-csharp[ImpliedProperties](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=ImpliedProperties)]

> [!TIP]
> <span data-ttu-id="4a14c-150">Bei Bedarf kann der standardmäßige Primärschlüssel für die nicht eigenständigen Entitätstypen geändert werden. Dann sollten jedoch Schlüsselwerte explizit angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="4a14c-150">When necessary the default primary key for the owned entity types can be changed, but then key values should be provided explicitly.</span></span>

## <a name="working-with-disconnected-entities"></a><span data-ttu-id="4a14c-151">Arbeiten mit getrennten Entitäten</span><span class="sxs-lookup"><span data-stu-id="4a14c-151">Working with disconnected entities</span></span>

<span data-ttu-id="4a14c-152">Jedes Element muss über einen `id`-Wert verfügen, der für den angegebenen Partitionsschlüssel eindeutig ist.</span><span class="sxs-lookup"><span data-stu-id="4a14c-152">Every item needs to have an `id` value that is unique for the given partition key.</span></span> <span data-ttu-id="4a14c-153">Standardmäßig generiert EF Core den Wert durch Verkettung der Diskriminatorwerte und der Primärschlüsselwerte, wobei „|“ als Trennzeichen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="4a14c-153">By default EF Core generates the value by concatenating the discriminator and the primary key values, using '|' as a delimiter.</span></span> <span data-ttu-id="4a14c-154">Die Schlüsselwerte werden nur generiert, wenn eine Entität in den Zustand `Added` wechselt.</span><span class="sxs-lookup"><span data-stu-id="4a14c-154">The key values are only generated when an entity enters the `Added` state.</span></span> <span data-ttu-id="4a14c-155">Dies kann ein Problem darstellen, wenn Sie [Entitäten anfügen](xref:core/saving/disconnected-entities), wenn diese nicht über eine `id`-Eigenschaft für den .NET-Typ verfügen, um den Wert zu speichern.</span><span class="sxs-lookup"><span data-stu-id="4a14c-155">This might pose a problem when [attaching entities](xref:core/saving/disconnected-entities) if they don't have an `id` property on the .NET type to store the value.</span></span>

<span data-ttu-id="4a14c-156">Um diese Einschränkung zu umgehen, können Sie den `id`-Wert manuell erstellen und festlegen oder die Entität zuerst als hinzugefügt markieren und dann in den gewünschten Zustand ändern:</span><span class="sxs-lookup"><span data-stu-id="4a14c-156">To work around this limitation one could create and set the `id` value manually or mark the entity as added first, then changing it to the desired state:</span></span>

[!code-csharp[Attach](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?highlight=4&name=Attach)]

<span data-ttu-id="4a14c-157">Dieser JSON-Code ergibt sich:</span><span class="sxs-lookup"><span data-stu-id="4a14c-157">This is the resulting JSON:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Distributor",
    "id": "Distributor|1",
    "ShippingCenters": [
        {
            "City": "Phoenix",
            "Street": "500 S 48th Street"
        }
    ],
    "_rid": "JBwtAN8oNYEBAAAAAAAAAA==",
    "_self": "dbs/JBwtAA==/colls/JBwtAN8oNYE=/docs/JBwtAN8oNYEBAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-9377-d7a1ae7c01d5\"",
    "_attachments": "attachments/",
    "_ts": 1572917100
}
```
