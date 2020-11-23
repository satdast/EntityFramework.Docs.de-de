---
title: 'Azure Cosmos DB-Anbieter: EF Core'
description: Dokumentation für den Datenbankanbieter, mit der Entity Framework Core mit der SQL-API von Azure Cosmos DB verwendet werden kann
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/providers/cosmos/index
ms.openlocfilehash: b167f53515799efdaead232f44ad5eab37fb0b14
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003600"
---
# <a name="ef-core-azure-cosmos-db-provider"></a>EF Core Azure Cosmos DB-Anbieter

> [!NOTE]
> Dieser Anbieter wurde in EF Core 3.0 eingeführt.

Dieser Datenbankanbieter ermöglicht die Verwendung von Entity Framework Core mit Azure Cosmos DB. Dieser Anbieter wird nicht im Rahmen des [Entity Framework Core-Projekts](https://github.com/dotnet/efcore) verwaltet.

Es wird dringend empfohlen, sich vor dem Lesen dieses Abschnitts mit der [Azure Cosmos DB-Dokumentation](/azure/cosmos-db/introduction) vertraut zu machen.

> [!NOTE]
> Dieser Anbieter kann nur mit der SQL-API von Azure Cosmos DB verwendet werden.

## <a name="install"></a>Installieren

Installieren Sie das [NuGet-Paket „Microsoft.EntityFrameworkCore.Cosmos“](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Cosmos
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.Cosmos
```

***

## <a name="get-started"></a>Erste Schritte

> [!TIP]
> Das in diesem Artikel verwendete [Beispiel finden Sie auf GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Cosmos).

Wie bei anderen Anbietern besteht der erste Schritt darin, [UseCosmos](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosDbContextOptionsExtensions.UseCosmos) aufzurufen:

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Configuration)]

> [!WARNING]
> Der Endpunkt und der Schlüssel sind aus Gründen der Einfachheit hier hartcodiert, aber in einer Produktions-App sollten sie [sicher gespeichert werden](/aspnet/core/security/app-secrets#secret-manager).

In diesem Beispiel ist `Order` eine einfache Entität mit einem Verweis auf den [nicht eigenständigen Typ ](xref:core/modeling/owned-entities) `StreetAddress`.

[!code-csharp[Order](../../../../samples/core/Cosmos/ModelBuilding/Order.cs?name=Order)]

[!code-csharp[StreetAddress](../../../../samples/core/Cosmos/ModelBuilding/StreetAddress.cs?name=StreetAddress)]

Das Speichern und Abfragen von Daten folgt dem normalen EF-Muster:

[!code-csharp[HelloCosmos](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=HelloCosmos)]

> [!IMPORTANT]
> Der Aufruf von [EnsureCreatedAsync](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreatedAsync) ist erforderlich, um die erforderlichen Container zu erstellen und die [Seeddaten](xref:core/modeling/data-seeding) einzufügen, wenn Sie im Modell vorhanden sind. `EnsureCreatedAsync` sollte jedoch nur während der Bereitstellung und nicht im normalen Betrieb aufgerufen werden, da dies zu Leistungsproblemen führen kann.

## <a name="cosmos-options"></a>Cosmos-Optionen

Es ist auch möglich, den Cosmos DB-Anbieter mit einer einzelnen Verbindungszeichenfolge zu konfigurieren und andere Optionen zum Anpassen der Verbindung anzugeben:

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OptionsContext.cs?name=Configuration)]

> [!NOTE]
> Die meisten dieser Optionen wurden in EF Core 5.0 eingeführt.

> [!TIP]
> In der Dokumentation zu [Azure Cosmos DB-Optionen](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions) finden Sie eine ausführliche Beschreibung der Auswirkungen der einzelnen oben genannten Optionen.

## <a name="cosmos-specific-model-customization"></a>Cosmos-spezifische Modellanpassung

Standardmäßig werden alle Entitätstypen demselben Container zugeordnet, der nach dem abgeleiteten Kontext benannt wird (in diesem Fall `"OrderContext"`). Verwenden Sie zum Ändern des Standardcontainernamens [HasDefaultContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosModelBuilderExtensions.HasDefaultContainer):

[!code-csharp[DefaultContainer](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=DefaultContainer)]

Um einen Entitätstyp einem anderen Container zuzuordnen, verwenden Sie [ToContainer](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToContainer):

[!code-csharp[Container](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Container)]

Um den Entitätstyp zu identifizieren, den ein bestimmtes Element darstellt, fügt EF Core einen Diskriminatorwert auch dann hinzu, wenn keine abgeleiteten Entitätstypen vorhanden sind. Der Name und der Wert des Diskriminators [können geändert werden](xref:core/modeling/inheritance).

Wenn kein anderer Entitätstyp jemals im gleichen Container gespeichert wird, kann der Diskriminator durch Aufruf von [HasNoDiscriminator](/dotnet/api/Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasNoDiscriminator) entfernt werden:

[!code-csharp[NoDiscriminator](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=NoDiscriminator)]

### <a name="partition-keys"></a>Partitionsschlüssel

Standardmäßig erstellt EF Core Container, bei denen der Partitionsschlüssel auf `"__partitionKey"` festgelegt ist, ohne beim Einfügen von Elementen einen Wert dafür anzugeben. Um die Leistungsfunktionen von Azure Cosmos vollständig nutzen zu können, sollten Sie jedoch einen [sorgfältig ausgewählten Partitionsschlüssel](/azure/cosmos-db/partition-data) verwenden. Dieser kann durch Aufrufen von [HasPartitionKey](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.HasPartitionKey) konfiguriert werden:

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PartitionKey)]

> [!NOTE]
>Als Eigenschaft des Partitionsschlüssels kann ein beliebiger Typ verwendet werden, sofern dieser in [eine Zeichenfolge konvertiert wird](xref:core/modeling/value-conversions).

Nach der Konfiguration muss die Partitionsschlüsseleigenschaft immer einen Wert aufweisen, der nicht NULL ist. Eine Abfrage kann durch Hinzufügen eines `WithPartitionKey`-Aufrufs einer einzelnen Partition zugewiesen werden.

[!code-csharp[PartitionKey](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=PartitionKey&highlight=15)]

> [!NOTE]
> `WithPartitionKey` wurde in EF Core 5.0 eingeführt.

Im Allgemeinen wird empfohlen, den Partitionsschlüssel dem Primärschlüssel hinzuzufügen, da dies die Serversemantik am besten widerspiegelt und einige Optimierungen ermöglicht, z. B. in `FindAsync`.

## <a name="embedded-entities"></a>Eingebettete Entitäten

Nicht eigenständige Cosmos-Entitäten sind in das gleiche Element wie der Besitzer eingebettet. Verwenden Sie zum Ändern eines Eigenschaftennamens [ToJsonProperty](/dotnet/api/Microsoft.EntityFrameworkCore.CosmosEntityTypeBuilderExtensions.ToJsonProperty):

[!code-csharp[PropertyNames](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PropertyNames)]

Mit dieser Konfiguration wird die Reihenfolge des obigen Beispiels wie folgt gespeichert:

```json
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

Sammlungen von nicht eigenständigen Entitäten werden ebenfalls eingebettet. Im nächsten Beispiel verwenden wir die `Distributor`-Klasse mit einer Sammlung von `StreetAddress`:

[!code-csharp[Distributor](../../../../samples/core/Cosmos/ModelBuilding/Distributor.cs?name=Distributor)]

Die nicht eigenständigen Entitäten müssen keine expliziten Schlüsselwerte angeben, die gespeichert werden sollen:

[!code-csharp[OwnedCollection](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=OwnedCollection)]

Sie werden auf diese Weise persistent gespeichert:

```json
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

Intern muss EF Core für alle nachverfolgten Entitäten immer eindeutige Schlüsselwerte aufweisen. Der Primärschlüssel, der standardmäßig für Sammlungen nicht eigenständiger Typen erstellt wird, besteht aus den Fremdschlüsseleigenschaften, die auf den Besitzer verweisen, und einer `int`-Eigenschaft, die dem Index im JSON-Array entspricht. Zum Abrufen dieser Werte kann die Eingabe-API verwendet werden:

[!code-csharp[ImpliedProperties](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=ImpliedProperties)]

> [!TIP]
> Bei Bedarf kann der standardmäßige Primärschlüssel für die nicht eigenständigen Entitätstypen geändert werden. Dann sollten jedoch Schlüsselwerte explizit angegeben werden.

## <a name="working-with-disconnected-entities"></a>Arbeiten mit getrennten Entitäten

Jedes Element muss über einen `id`-Wert verfügen, der für den angegebenen Partitionsschlüssel eindeutig ist. Standardmäßig generiert EF Core den Wert durch Verkettung der Diskriminatorwerte und der Primärschlüsselwerte, wobei „|“ als Trennzeichen verwendet wird. Die Schlüsselwerte werden nur generiert, wenn eine Entität in den Zustand `Added` wechselt. Dies kann ein Problem darstellen, wenn Sie [Entitäten anfügen](xref:core/saving/disconnected-entities), wenn diese nicht über eine `id`-Eigenschaft für den .NET-Typ verfügen, um den Wert zu speichern.

Um diese Einschränkung zu umgehen, können Sie den `id`-Wert manuell erstellen und festlegen oder die Entität zuerst als hinzugefügt markieren und dann in den gewünschten Zustand ändern:

[!code-csharp[Attach](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?highlight=4&name=Attach)]

Dieser JSON-Code ergibt sich:

```json
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

## <a name="optimistic-concurrency-with-etags"></a>Optimistische Nebenläufigkeit mit ETags

> [!NOTE]
> Nebenläufigkeit für ETags wurde in EF Core 5.0 eingeführt.

Rufen Sie `UseETagConcurrency` auf, um einen Entitätstyp so zu konfigurieren, dass die [optimistische Nebenläufigkeit](xref:core/modeling/concurrency) verwendet wird. Dieser Aufruf erstellt eine `_etag`-Eigenschaft als [Schatteneigenschaft](xref:core/modeling/shadow-properties), und diese Eigenschaft wird als Nebenläufigkeitstoken festgelegt.

[!code-csharp[Main](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=ETag)]

Wenn Sie das Auflösen von Nebenläufigkeitsfehlern vereinfachen möchten, können Sie das ETag mithilfe von `IsETagConcurrency` einer CLR-Eigenschaft zuordnen.

[!code-csharp[Main](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=ETagProperty)]
