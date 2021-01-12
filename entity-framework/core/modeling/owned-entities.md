---
title: Eigene Entitäts Typen-EF Core
description: Konfigurieren eigener Entitäts Typen oder Aggregate bei Verwendung von Entity Framework Core
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/owned-entities
ms.openlocfilehash: 0cd6bfd25d4462509a3e6c112b892d652d29e45e
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128627"
---
# <a name="owned-entity-types"></a>Nicht eigenständige Entitätstypen

EF Core ermöglicht es Ihnen, Entitäts Typen zu modellieren, die nur in den Navigations Eigenschaften anderer Entitäts Typen angezeigt werden können. Diese werden als _eigene Entitäts Typen_ bezeichnet. Die Entität, die einen eigenen Entitätstyp enthält, ist Ihr _Besitzer_.

Besitzende Entitäten sind im wesentlichen Teil des Besitzers und können nicht ohne Sie vorhanden sein, Sie sind konzeptionell ähnlich wie [Aggregate](https://martinfowler.com/bliki/DDD_Aggregate.html). Dies bedeutet, dass die eigene Entität definitionsgemäß auf der abhängigen Seite der Beziehung mit dem Besitzer ist.

## <a name="explicit-configuration"></a>Explizite Konfiguration

Eigene Entitäts Typen werden nie von EF Core im Modell nach Konvention eingeschlossen. Sie können die- `OwnsOne` Methode in verwenden `OnModelCreating` oder den Typ mit kommentieren `OwnedAttribute` , um den Typ als eigenen Typ zu konfigurieren.

In diesem Beispiel `StreetAddress` ist ein Typ ohne Identity-Eigenschaft. Dieser Typ wird als Eigenschaft des Typs „Order“ verwendet, um die Lieferadresse für eine bestimmte Bestellung anzugeben.

Wir können das verwenden `OwnedAttribute` , um es als eigene Entität zu behandeln, wenn von einem anderen Entitätstyp darauf verwiesen wird:

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

Es ist auch möglich, mit der- `OwnsOne` Methode in `OnModelCreating` anzugeben, dass es sich bei der `ShippingAddress` Eigenschaft um eine Entität des `Order` Entitäts Typs handelt, und um ggf. weitere Facetten zu konfigurieren.

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

Wenn die `ShippingAddress` Eigenschaft im-Typ privat ist `Order` , können Sie die Zeichen folgen Version der- `OwnsOne` Methode verwenden:

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

Das obige Modell ist dem folgenden Datenbankschema zugeordnet:

![Sceenshot des Datenbankmodells für eine Entität, die einen eigenen Verweis enthält](_static/owned-entities-ownsone.png)

Weitere Informationen finden Sie im [vollständigen Beispiel Projekt](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) .

> [!TIP]
> Der zugehörige Entitätstyp kann als erforderlich markiert werden. Weitere Informationen finden [Sie unter erforderliche 1-zu-eins-abhängige Abhängigkeiten](xref:core/modeling/relationships#one-to-one) .

## <a name="implicit-keys"></a>Implizite Schlüssel

`OwnsOne`Besitzer Typen, die mit einer Verweis Navigation konfiguriert oder ermittelt wurden, haben immer eine eins-zu-eins-Beziehung mit dem Besitzer und benötigen daher keine eigenen Schlüsselwerte, da die Fremdschlüssel Werte eindeutig sind. Im vorherigen Beispiel muss der- `StreetAddress` Typ keine Schlüsseleigenschaft definieren.

Um zu verstehen, wie EF Core diese Objekte verfolgt, ist es hilfreich zu wissen, dass ein Primärschlüssel als [Schatten Eigenschaft](xref:core/modeling/shadow-properties) für den eigenen Typ erstellt wird. Der Wert des Schlüssels einer Instanz des eigenen Typs ist mit dem Wert des Schlüssels der Besitzer Instanz identisch.

## <a name="collections-of-owned-types"></a>Auflistungen von eigenen Typen

Verwenden Sie zum Konfigurieren einer Sammlung von eigenen Typen `OwnsMany` in `OnModelCreating` .

Für eigene Typen ist ein Primärschlüssel erforderlich. Wenn für den .NET-Typ keine guten Kandidaten Eigenschaften vorhanden sind, können EF Core versuchen, eine zu erstellen. Wenn jedoch eigene Typen durch eine Auflistung definiert werden, reicht es nicht aus, eine Schatten Eigenschaft zu erstellen, die sowohl als Fremdschlüssel für den Besitzer als auch als Primärschlüssel der eigenen Instanz fungiert. Dies geschieht wie folgt `OwnsOne` : Es können mehrere eigene Typinstanzen für jeden Besitzer vorhanden sein, und daher reicht der Schlüssel des Besitzers nicht aus, um eine eindeutige Identität für jede eigene Instanz bereitzustellen.

Die beiden einfachsten Lösungen hierfür sind:

- Definieren eines Ersatz Primärschlüssels für eine neue Eigenschaft, unabhängig vom Fremdschlüssel, der auf den Besitzer zeigt. Die enthaltenen Werte müssen für alle Besitzer eindeutig sein (z. b. wenn das übergeordnete Element über ein untergeordnetes Element {1} verfügt, das übergeordnete Element kein unter {1} geordnetes Element {2} haben kann {1} ), sodass der Wert keine inhärente Bedeutung hat Da der Fremdschlüssel nicht Teil des Primärschlüssels ist, können seine Werte geändert werden, sodass Sie ein untergeordnetes Element von einem übergeordneten Element zu einem anderen verschieben können. Dies ist jedoch in der Regel gegen Aggregat Semantik.
- Verwenden des fremd Schlüssels und einer zusätzlichen Eigenschaft als zusammengesetzten Schlüssel. Der zusätzliche Eigenschafts Wert muss nun nur für ein bestimmtes übergeordnetes Element eindeutig sein (wenn das übergeordnete Element über ein untergeordnetes Element verfügt, kann das übergeordnete Element {1} {1,1} {2} nach wie vor über {2,1} Durch den Fremdschlüssel Teil des Primärschlüssels wird die Beziehung zwischen dem Besitzer und der Entität, die im Besitz des Primärschlüssels ist, unveränderlich und die Aggregat Semantik ist besser. Dies geschieht EF Core standardmäßig.

In diesem Beispiel verwenden wir die- `Distributor` Klasse.

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

Standardmäßig ist der Primärschlüssel, der für den eigenen Typ verwendet wird, auf den über die Navigations Eigenschaft verwiesen wird, `ShippingCenters` `("DistributorId", "Id")` wobei `"DistributorId"` der FK und ein eindeutiger `"Id"` `int` Wert ist.

Zum Konfigurieren eines anderen Primärschlüssel Aufrufes `HasKey` .

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

Das obige Modell ist dem folgenden Datenbankschema zugeordnet:

![Sceenshot des Datenbankmodells für eine Entität, die eine eigene Sammlung enthält](_static/owned-entities-ownsmany.png)

## <a name="mapping-owned-types-with-table-splitting"></a>Zuordnung eigener Typen mit Tabellen Aufteilung

Bei der Verwendung von relationalen Datenbanken werden Verweis eigene Typen standardmäßig derselben Tabelle wie der Besitzer zugeordnet. Dies erfordert das Aufteilen der Tabelle in zwei: einige Spalten werden zum Speichern der Daten des Besitzers verwendet, und einige Spalten werden zum Speichern von Daten der Entität verwendet. Dies ist ein gängiges Feature, das als [Tabellen Aufteilung](xref:core/modeling/table-splitting)bezeichnet wird.

Standardmäßig benennen EF Core die Daten Bank Spalten für die Eigenschaften des eigenen Entitäts Typs nach dem Muster _Navigation_OwnedEntityProperty_. Daher `StreetAddress` werden die Eigenschaften in der Tabelle "Orders" mit den Namen "ShippingAddress_Street" und "ShippingAddress_City" angezeigt.

Sie können die- `HasColumnName` Methode verwenden, um diese Spalten umzubenennen.

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

> [!NOTE]
> Die meisten der normalen Entitätstyp-Konfigurations Methoden wie [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) können auf die gleiche Weise aufgerufen werden.

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a>Gemeinsame Nutzung desselben .net-Typs für mehrere eigene Typen

Ein eigener Entitätstyp kann vom gleichen .NET-Typ wie ein anderer eigener Entitätstyp sein. Daher reicht der .NET-Typ möglicherweise nicht aus, um einen eigenen Typ zu identifizieren.

In diesen Fällen wird die-Eigenschaft, die vom Besitzer auf die eigene Entität zeigt, zur _definierenden Navigation_ des eigenen Entitäts Typs. Aus Sicht der EF Core ist die definierende Navigation Teil der Typidentität neben dem .NET-Typ.

In der folgenden Klasse `ShippingAddress` und `BillingAddress` sind z. b. beide identisch mit dem .NET-Typ `StreetAddress` .

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

Um zu verstehen, wie EF Core überwachte Instanzen dieser Objekte unterscheiden, kann es hilfreich sein zu wissen, dass die definierende Navigation neben dem Wert des Schlüssels des Besitzers und dem .NET-Typ des eigenen Typs zu einem Teil des Schlüssels der Instanz geworden ist.

## <a name="nested-owned-types"></a>Im Besitz befindliche Typen

In diesem Beispiel `OrderDetails` gehören `BillingAddress` und `ShippingAddress` , bei denen es sich um beide `StreetAddress` Typen handelt. Dann besitzt der `DetailedOrder`-Typ `OrderDetails`.

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

Jede Navigation zu einem eigenen Typ definiert einen separaten Entitätstyp mit vollständig unabhängiger Konfiguration.

Zusätzlich zu den Typen, die im Besitz von Typen sind, kann ein eigener Typ auf eine reguläre Entität verweisen, die entweder der Besitzer oder eine andere Entität sein kann, solange sich die zugehörige Entität auf der abhängigen Seite befindet. Diese Funktion legt eigene Entitäts Typen außer komplexen Typen in EF6 fest.

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

## <a name="configuring-owned-types"></a>Konfigurieren von eigenen Typen

Es ist möglich, die- `OwnsOne` Methode in einem fließend aufzurufenden aufzurufen, um dieses Modell zu konfigurieren:

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

Beachten Sie den-Befehl `WithOwner` , der zum Definieren der Navigations Eigenschaft verwendet wird, die auf den Besitzer zeigt. Zum Definieren einer Navigation zum Owner-Entitätstyp, der nicht Teil der Besitz Beziehung ist, `WithOwner()` sollte ohne Argumente aufgerufen werden.

Sie können dieses Ergebnis auch mithilfe `OwnedAttribute` von sowohl in als auch in erreichen `OrderDetails` `StreetAddress` .

Beachten Sie außerdem den-Befehl `Navigation` . In efcore 5,0 können Navigations Eigenschaften zu eigenen Typen [für nicht-eigene Navigations Eigenschaften](xref:core/modeling/relationships#configuring-navigation-properties)weiter konfiguriert werden.

Das obige Modell ist dem folgenden Datenbankschema zugeordnet:

![Screenshot des Datenbankmodells für Entitäten, die geclusterte Verweise enthalten](_static/owned-entities-nested.png)

## <a name="storing-owned-types-in-separate-tables"></a>Speichern von eigenen Typen in separaten Tabellen

Im Gegensatz zu komplexen EF6-Typen können eigene Typen in einer separaten Tabelle vom Besitzer gespeichert werden. Um die Konvention zu überschreiben, die einen eigenen Typ derselben Tabelle wie der Besitzer zuordnet, können Sie einfach aufzurufen `ToTable` und einen anderen Tabellennamen angeben. Im folgenden Beispiel werden `OrderDetails` und die beiden Adressen einer separaten Tabelle von zugeordnet `DetailedOrder` :

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

Es ist auch möglich `TableAttribute` , das zu verwenden, aber beachten Sie, dass dies fehlschlägt, wenn mehrere Navigationen zum eigenen Typ vorhanden sind, da in diesem Fall mehrere Entitäts Typen derselben Tabelle zugeordnet werden.

## <a name="querying-owned-types"></a>Abfragen von eigenen Typen

Beim Abfragen des Besitzers werden standardmäßig eigene Typen eingeschlossen. Es ist nicht erforderlich, die- `Include` Methode zu verwenden, auch wenn die eigenen Typen in einer separaten Tabelle gespeichert werden. Basierend auf dem zuvor beschriebenen Modell wird die folgende Abfrage `Order` `OrderDetails` und die beiden im Besitz der- `StreetAddresses` Datenbank angezeigt:

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a>Einschränkungen

Einige dieser Einschränkungen sind grundlegend für die Funktionsweise von Entitäts Typen im Besitz, einige andere sind jedoch Einschränkungen, die in zukünftigen Versionen möglicherweise entfernt werden könnten:

### <a name="by-design-restrictions"></a>Entwurfs Einschränkungen

- Sie können keinen `DbSet<T>` für einen eigenen Typ erstellen.
- Es ist nicht möglich, `Entity<T>()` mit einem eigenen Typ für aufzurufen `ModelBuilder` .
- Instanzen von eigenen Entitäts Typen können nicht von mehreren Besitzern gemeinsam genutzt werden (Dies ist ein bekanntes Szenario für Wert Objekte, das nicht mit Entitäts Typen im Besitz von Entitäten implementiert werden kann).

### <a name="current-shortcomings"></a>Aktuelle Mängel

- Besitzende Entitäts Typen dürfen keine Vererbungs Hierarchien aufweisen

### <a name="shortcomings-in-previous-versions"></a>Mängel in früheren Versionen

- In EF Core 2. x-Referenz Navigation zu besitzenden Entitäts Typen dürfen nicht NULL sein, es sei denn, Sie werden explizit einer separaten Tabelle vom Besitzer zugeordnet.
- In EF Core 3. x werden die Spalten für eigene Entitäts Typen, die derselben Tabelle wie der Besitzer zugeordnet sind, immer als "Nullable" markiert.
