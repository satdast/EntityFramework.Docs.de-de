---
title: Eigene Entitäts Typen-EF Core
description: Konfigurieren eigener Entitäts Typen oder Aggregate bei Verwendung von Entity Framework Core
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/owned-entities
ms.openlocfilehash: 36f756b70c9ad1727c48b5c789fd324c9dc6cd29
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429436"
---
# <a name="owned-entity-types"></a><span data-ttu-id="15967-103">Nicht eigenständige Entitätstypen</span><span class="sxs-lookup"><span data-stu-id="15967-103">Owned Entity Types</span></span>

<span data-ttu-id="15967-104">EF Core ermöglicht es Ihnen, Entitäts Typen zu modellieren, die nur in den Navigations Eigenschaften anderer Entitäts Typen angezeigt werden können.</span><span class="sxs-lookup"><span data-stu-id="15967-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="15967-105">Diese werden als _eigene Entitäts Typen_ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="15967-105">These are called _owned entity types_.</span></span> <span data-ttu-id="15967-106">Die Entität, die einen eigenen Entitätstyp enthält, ist Ihr _Besitzer_.</span><span class="sxs-lookup"><span data-stu-id="15967-106">The entity containing an owned entity type is its _owner_.</span></span>

<span data-ttu-id="15967-107">Besitzende Entitäten sind im wesentlichen Teil des Besitzers und können nicht ohne Sie vorhanden sein, Sie sind konzeptionell ähnlich wie [Aggregate](https://martinfowler.com/bliki/DDD_Aggregate.html).</span><span class="sxs-lookup"><span data-stu-id="15967-107">Owned entities are essentially a part of the owner and cannot exist without it, they are conceptually similar to [aggregates](https://martinfowler.com/bliki/DDD_Aggregate.html).</span></span> <span data-ttu-id="15967-108">Dies bedeutet, dass die eigene Entität definitionsgemäß auf der abhängigen Seite der Beziehung mit dem Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="15967-108">This means that the owned entity is by definition on the dependent side of the relationship with the owner.</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="15967-109">Explizite Konfiguration</span><span class="sxs-lookup"><span data-stu-id="15967-109">Explicit configuration</span></span>

<span data-ttu-id="15967-110">Eigene Entitäts Typen werden nie von EF Core im Modell nach Konvention eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="15967-110">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="15967-111">Sie können die- `OwnsOne` Methode in verwenden `OnModelCreating` oder den Typ mit kommentieren `OwnedAttribute` , um den Typ als eigenen Typ zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="15967-111">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` to configure the type as an owned type.</span></span>

<span data-ttu-id="15967-112">In diesem Beispiel `StreetAddress` ist ein Typ ohne Identity-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="15967-112">In this example, `StreetAddress` is a type with no identity property.</span></span> <span data-ttu-id="15967-113">Dieser Typ wird als Eigenschaft des Typs „Order“ verwendet, um die Lieferadresse für eine bestimmte Bestellung anzugeben.</span><span class="sxs-lookup"><span data-stu-id="15967-113">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span>

<span data-ttu-id="15967-114">Wir können das verwenden `OwnedAttribute` , um es als eigene Entität zu behandeln, wenn von einem anderen Entitätstyp darauf verwiesen wird:</span><span class="sxs-lookup"><span data-stu-id="15967-114">We can use the `OwnedAttribute` to treat it as an owned entity when referenced from another entity type:</span></span>

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

<span data-ttu-id="15967-115">Es ist auch möglich, mit der- `OwnsOne` Methode in `OnModelCreating` anzugeben, dass es sich bei der `ShippingAddress` Eigenschaft um eine Entität des `Order` Entitäts Typs handelt, und um ggf. weitere Facetten zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="15967-115">It is also possible to use the `OwnsOne` method in `OnModelCreating` to specify that the `ShippingAddress` property is an Owned Entity of the `Order` entity type and to configure additional facets if needed.</span></span>

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

<span data-ttu-id="15967-116">Wenn die `ShippingAddress` Eigenschaft im-Typ privat ist `Order` , können Sie die Zeichen folgen Version der- `OwnsOne` Methode verwenden:</span><span class="sxs-lookup"><span data-stu-id="15967-116">If the `ShippingAddress` property is private in the `Order` type, you can use the string version of the `OwnsOne` method:</span></span>

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

<span data-ttu-id="15967-117">Das obige Modell ist dem folgenden Datenbankschema zugeordnet:</span><span class="sxs-lookup"><span data-stu-id="15967-117">The model above is mapped to the following database schema:</span></span>

![Sceenshot des Datenbankmodells für eine Entität, die einen eigenen Verweis enthält](_static/owned-entities-ownsone.png)

<span data-ttu-id="15967-119">Weitere Informationen finden Sie im [vollständigen Beispiel Projekt](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) .</span><span class="sxs-lookup"><span data-stu-id="15967-119">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) for more context.</span></span>

> [!TIP]
> <span data-ttu-id="15967-120">Der zugehörige Entitätstyp kann als erforderlich markiert werden. Weitere Informationen finden [Sie unter erforderliche 1-zu-eins-abhängige Abhängigkeiten](xref:core/modeling/relationships#one-to-one) .</span><span class="sxs-lookup"><span data-stu-id="15967-120">The owned entity type can be marked as required, see [Required one-to-one dependents](xref:core/modeling/relationships#one-to-one) for more information.</span></span>

## <a name="implicit-keys"></a><span data-ttu-id="15967-121">Implizite Schlüssel</span><span class="sxs-lookup"><span data-stu-id="15967-121">Implicit keys</span></span>

<span data-ttu-id="15967-122">`OwnsOne`Besitzer Typen, die mit einer Verweis Navigation konfiguriert oder ermittelt wurden, haben immer eine eins-zu-eins-Beziehung mit dem Besitzer und benötigen daher keine eigenen Schlüsselwerte, da die Fremdschlüssel Werte eindeutig sind.</span><span class="sxs-lookup"><span data-stu-id="15967-122">Owned types configured with `OwnsOne` or discovered through a reference navigation always have a one-to-one relationship with the owner, therefore they don't need their own key values as the foreign key values are unique.</span></span> <span data-ttu-id="15967-123">Im vorherigen Beispiel muss der- `StreetAddress` Typ keine Schlüsseleigenschaft definieren.</span><span class="sxs-lookup"><span data-stu-id="15967-123">In the previous example, the `StreetAddress` type does not need to define a key property.</span></span>  

<span data-ttu-id="15967-124">Um zu verstehen, wie EF Core diese Objekte verfolgt, ist es hilfreich zu wissen, dass ein Primärschlüssel als [Schatten Eigenschaft](xref:core/modeling/shadow-properties) für den eigenen Typ erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="15967-124">In order to understand how EF Core tracks these objects, it is useful to know that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="15967-125">Der Wert des Schlüssels einer Instanz des eigenen Typs ist mit dem Wert des Schlüssels der Besitzer Instanz identisch.</span><span class="sxs-lookup"><span data-stu-id="15967-125">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>

## <a name="collections-of-owned-types"></a><span data-ttu-id="15967-126">Auflistungen von eigenen Typen</span><span class="sxs-lookup"><span data-stu-id="15967-126">Collections of owned types</span></span>

<span data-ttu-id="15967-127">Verwenden Sie zum Konfigurieren einer Sammlung von eigenen Typen `OwnsMany` in `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="15967-127">To configure a collection of owned types use `OwnsMany` in `OnModelCreating`.</span></span>

<span data-ttu-id="15967-128">Für eigene Typen ist ein Primärschlüssel erforderlich.</span><span class="sxs-lookup"><span data-stu-id="15967-128">Owned types need a primary key.</span></span> <span data-ttu-id="15967-129">Wenn für den .NET-Typ keine guten Kandidaten Eigenschaften vorhanden sind, können EF Core versuchen, eine zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="15967-129">If there are no good candidates properties on the .NET type, EF Core can try to create one.</span></span> <span data-ttu-id="15967-130">Wenn jedoch eigene Typen durch eine Auflistung definiert werden, reicht es nicht aus, eine Schatten Eigenschaft zu erstellen, die sowohl als Fremdschlüssel für den Besitzer als auch als Primärschlüssel der eigenen Instanz fungiert. Dies geschieht wie folgt `OwnsOne` : Es können mehrere eigene Typinstanzen für jeden Besitzer vorhanden sein, und daher reicht der Schlüssel des Besitzers nicht aus, um eine eindeutige Identität für jede eigene Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="15967-130">However, when owned types are defined through a collection, it isn't enough to just create a shadow property to act as both the foreign key into the owner and the primary key of the owned instance, as we do for `OwnsOne`: there can be multiple owned type instances for each owner, and hence the key of the owner isn't enough to provide a unique identity for each owned instance.</span></span>

<span data-ttu-id="15967-131">Die beiden einfachsten Lösungen hierfür sind:</span><span class="sxs-lookup"><span data-stu-id="15967-131">The two most straightforward solutions to this are:</span></span>

- <span data-ttu-id="15967-132">Definieren eines Ersatz Primärschlüssels für eine neue Eigenschaft, unabhängig vom Fremdschlüssel, der auf den Besitzer zeigt.</span><span class="sxs-lookup"><span data-stu-id="15967-132">Defining a surrogate primary key on a new property independent of the foreign key that points to the owner.</span></span> <span data-ttu-id="15967-133">Die enthaltenen Werte müssen für alle Besitzer eindeutig sein (z. b. wenn das übergeordnete Element über ein untergeordnetes Element {1} verfügt, das übergeordnete Element kein unter {1} geordnetes Element {2} haben kann {1} ), sodass der Wert keine inhärente Bedeutung hat</span><span class="sxs-lookup"><span data-stu-id="15967-133">The contained values would need to be unique across all owners (e.g. if Parent {1} has Child {1}, then Parent {2} cannot have Child {1}), so the value doesn't have any inherent meaning.</span></span> <span data-ttu-id="15967-134">Da der Fremdschlüssel nicht Teil des Primärschlüssels ist, können seine Werte geändert werden, sodass Sie ein untergeordnetes Element von einem übergeordneten Element zu einem anderen verschieben können. Dies ist jedoch in der Regel gegen Aggregat Semantik.</span><span class="sxs-lookup"><span data-stu-id="15967-134">Since the foreign key is not part of the primary key its values can be changed, so you could move a child from one parent to another one, however this usually goes against aggregate semantics.</span></span>
- <span data-ttu-id="15967-135">Verwenden des fremd Schlüssels und einer zusätzlichen Eigenschaft als zusammengesetzten Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="15967-135">Using the foreign key and an additional property as a composite key.</span></span> <span data-ttu-id="15967-136">Der zusätzliche Eigenschafts Wert muss nun nur für ein bestimmtes übergeordnetes Element eindeutig sein (wenn das übergeordnete Element über ein untergeordnetes Element verfügt, kann das übergeordnete Element {1} {1,1} {2} nach wie vor über {2,1}</span><span class="sxs-lookup"><span data-stu-id="15967-136">The additional property value now only needs to be unique for a given parent (so if Parent {1} has Child {1,1} then Parent {2} can still have Child {2,1}).</span></span> <span data-ttu-id="15967-137">Durch den Fremdschlüssel Teil des Primärschlüssels wird die Beziehung zwischen dem Besitzer und der Entität, die im Besitz des Primärschlüssels ist, unveränderlich und die Aggregat Semantik ist besser.</span><span class="sxs-lookup"><span data-stu-id="15967-137">By making the foreign key part of the primary key the relationship between the owner and the owned entity becomes immutable and reflects aggregate semantics better.</span></span> <span data-ttu-id="15967-138">Dies geschieht EF Core standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="15967-138">This is what EF Core does by default.</span></span>

<span data-ttu-id="15967-139">In diesem Beispiel verwenden wir die- `Distributor` Klasse.</span><span class="sxs-lookup"><span data-stu-id="15967-139">In this example we'll use the `Distributor` class.</span></span>

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

<span data-ttu-id="15967-140">Standardmäßig ist der Primärschlüssel, der für den eigenen Typ verwendet wird, auf den über die Navigations Eigenschaft verwiesen wird, `ShippingCenters` `("DistributorId", "Id")` wobei `"DistributorId"` der FK und ein eindeutiger `"Id"` `int` Wert ist.</span><span class="sxs-lookup"><span data-stu-id="15967-140">By default the primary key used for the owned type referenced through the `ShippingCenters` navigation property will be `("DistributorId", "Id")` where `"DistributorId"` is the FK and `"Id"` is a unique `int` value.</span></span>

<span data-ttu-id="15967-141">Zum Konfigurieren eines anderen Primärschlüssel Aufrufes `HasKey` .</span><span class="sxs-lookup"><span data-stu-id="15967-141">To configure a different primary key call `HasKey`.</span></span>

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

<span data-ttu-id="15967-142">Das obige Modell ist dem folgenden Datenbankschema zugeordnet:</span><span class="sxs-lookup"><span data-stu-id="15967-142">The model above is mapped to the following database schema:</span></span>

![Sceenshot des Datenbankmodells für eine Entität, die eine eigene Sammlung enthält](_static/owned-entities-ownsmany.png)

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="15967-144">Zuordnung eigener Typen mit Tabellen Aufteilung</span><span class="sxs-lookup"><span data-stu-id="15967-144">Mapping owned types with table splitting</span></span>

<span data-ttu-id="15967-145">Bei der Verwendung von relationalen Datenbanken werden Verweis eigene Typen standardmäßig derselben Tabelle wie der Besitzer zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="15967-145">When using relational databases, by default reference owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="15967-146">Dies erfordert das Aufteilen der Tabelle in zwei: einige Spalten werden zum Speichern der Daten des Besitzers verwendet, und einige Spalten werden zum Speichern von Daten der Entität verwendet.</span><span class="sxs-lookup"><span data-stu-id="15967-146">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="15967-147">Dies ist ein gängiges Feature, das als [Tabellen Aufteilung](xref:core/modeling/table-splitting)bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="15967-147">This is a common feature known as [table splitting](xref:core/modeling/table-splitting).</span></span>

<span data-ttu-id="15967-148">Standardmäßig benennen EF Core die Daten Bank Spalten für die Eigenschaften des eigenen Entitäts Typs nach dem Muster _Navigation_OwnedEntityProperty_.</span><span class="sxs-lookup"><span data-stu-id="15967-148">By default, EF Core will name the database columns for the properties of the owned entity type following the pattern _Navigation_OwnedEntityProperty_.</span></span> <span data-ttu-id="15967-149">Daher `StreetAddress` werden die Eigenschaften in der Tabelle "Orders" mit den Namen "ShippingAddress_Street" und "ShippingAddress_City" angezeigt.</span><span class="sxs-lookup"><span data-stu-id="15967-149">Therefore the `StreetAddress` properties will appear in the 'Orders' table with the names 'ShippingAddress_Street' and 'ShippingAddress_City'.</span></span>

<span data-ttu-id="15967-150">Sie können die- `HasColumnName` Methode verwenden, um diese Spalten umzubenennen.</span><span class="sxs-lookup"><span data-stu-id="15967-150">You can use the `HasColumnName` method to rename those columns.</span></span>

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

> [!NOTE]
> <span data-ttu-id="15967-151">Die meisten der normalen Entitätstyp-Konfigurations Methoden wie [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) können auf die gleiche Weise aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="15967-151">Most of the normal entity type configuration methods like [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) can be called in the same way.</span></span>

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="15967-152">Gemeinsame Nutzung desselben .net-Typs für mehrere eigene Typen</span><span class="sxs-lookup"><span data-stu-id="15967-152">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="15967-153">Ein eigener Entitätstyp kann vom gleichen .NET-Typ wie ein anderer eigener Entitätstyp sein. Daher reicht der .NET-Typ möglicherweise nicht aus, um einen eigenen Typ zu identifizieren.</span><span class="sxs-lookup"><span data-stu-id="15967-153">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="15967-154">In diesen Fällen wird die-Eigenschaft, die vom Besitzer auf die eigene Entität zeigt, zur _definierenden Navigation_ des eigenen Entitäts Typs.</span><span class="sxs-lookup"><span data-stu-id="15967-154">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="15967-155">Aus Sicht der EF Core ist die definierende Navigation Teil der Typidentität neben dem .NET-Typ.</span><span class="sxs-lookup"><span data-stu-id="15967-155">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>

<span data-ttu-id="15967-156">In der folgenden Klasse `ShippingAddress` und `BillingAddress` sind z. b. beide identisch mit dem .NET-Typ `StreetAddress` .</span><span class="sxs-lookup"><span data-stu-id="15967-156">For example, in the following class `ShippingAddress` and `BillingAddress` are both of the same .NET type, `StreetAddress`.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="15967-157">Um zu verstehen, wie EF Core überwachte Instanzen dieser Objekte unterscheiden, kann es hilfreich sein zu wissen, dass die definierende Navigation neben dem Wert des Schlüssels des Besitzers und dem .NET-Typ des eigenen Typs zu einem Teil des Schlüssels der Instanz geworden ist.</span><span class="sxs-lookup"><span data-stu-id="15967-157">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="15967-158">Im Besitz befindliche Typen</span><span class="sxs-lookup"><span data-stu-id="15967-158">Nested owned types</span></span>

<span data-ttu-id="15967-159">In diesem Beispiel `OrderDetails` gehören `BillingAddress` und `ShippingAddress` , bei denen es sich um beide `StreetAddress` Typen handelt.</span><span class="sxs-lookup"><span data-stu-id="15967-159">In this example `OrderDetails` owns `BillingAddress` and `ShippingAddress`, which are both `StreetAddress` types.</span></span> <span data-ttu-id="15967-160">Dann besitzt der `DetailedOrder`-Typ `OrderDetails`.</span><span class="sxs-lookup"><span data-stu-id="15967-160">Then `OrderDetails` is owned by the `DetailedOrder` type.</span></span>

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

<span data-ttu-id="15967-161">Jede Navigation zu einem eigenen Typ definiert einen separaten Entitätstyp mit vollständig unabhängiger Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="15967-161">Each navigation to an owned type defines a separate entity type with completely independent configuration.</span></span>

<span data-ttu-id="15967-162">Zusätzlich zu den Typen, die im Besitz von Typen sind, kann ein eigener Typ auf eine reguläre Entität verweisen, die entweder der Besitzer oder eine andere Entität sein kann, solange sich die zugehörige Entität auf der abhängigen Seite befindet.</span><span class="sxs-lookup"><span data-stu-id="15967-162">In addition to nested owned types, an owned type can reference a regular entity which can be either the owner or a different entity as long as the owned entity is on the dependent side.</span></span> <span data-ttu-id="15967-163">Diese Funktion legt eigene Entitäts Typen außer komplexen Typen in EF6 fest.</span><span class="sxs-lookup"><span data-stu-id="15967-163">This capability sets owned entity types apart from complex types in EF6.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

## <a name="configuring-owned-types"></a><span data-ttu-id="15967-164">Konfigurieren von eigenen Typen</span><span class="sxs-lookup"><span data-stu-id="15967-164">Configuring Owned Types</span></span>

<span data-ttu-id="15967-165">Es ist möglich, die- `OwnsOne` Methode in einem fließend aufzurufenden aufzurufen, um dieses Modell zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="15967-165">It is possible to chain the `OwnsOne` method in a fluent call to configure this model:</span></span>

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

<span data-ttu-id="15967-166">Beachten Sie den-Befehl `WithOwner` , der zum Definieren der Navigations Eigenschaft verwendet wird, die auf den Besitzer zeigt.</span><span class="sxs-lookup"><span data-stu-id="15967-166">Notice the `WithOwner` call used to define the navigation property pointing back at the owner.</span></span> <span data-ttu-id="15967-167">Zum Definieren einer Navigation zum Owner-Entitätstyp, der nicht Teil der Besitz Beziehung ist, `WithOwner()` sollte ohne Argumente aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="15967-167">To define a navigation to the owner entity type that's not part of the ownership relationship `WithOwner()` should be called without any arguments.</span></span>

<span data-ttu-id="15967-168">Sie können dieses Ergebnis auch mithilfe `OwnedAttribute` von sowohl in als auch in erreichen `OrderDetails` `StreetAddress` .</span><span class="sxs-lookup"><span data-stu-id="15967-168">It is also possible to achieve this result using `OwnedAttribute` on both `OrderDetails` and `StreetAddress`.</span></span>

<span data-ttu-id="15967-169">Beachten Sie außerdem den-Befehl `Navigation` .</span><span class="sxs-lookup"><span data-stu-id="15967-169">In addition, notice the `Navigation` call.</span></span> <span data-ttu-id="15967-170">In efcore 5,0 können Navigations Eigenschaften zu eigenen Typen [für nicht-eigene Navigations Eigenschaften](xref:core/modeling/relationships#configuring-navigation-properties)weiter konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="15967-170">In EFCore 5.0, navigation properties to owned types can be further configured [as for non-owned navigation properties](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

<span data-ttu-id="15967-171">Das obige Modell ist dem folgenden Datenbankschema zugeordnet:</span><span class="sxs-lookup"><span data-stu-id="15967-171">The model above is mapped to the following database schema:</span></span>

![Sceenshot des Datenbankmodells für eine Entität, die geschpostete Verweise enthält](_static/owned-entities-nested.png)

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="15967-173">Speichern von eigenen Typen in separaten Tabellen</span><span class="sxs-lookup"><span data-stu-id="15967-173">Storing owned types in separate tables</span></span>

<span data-ttu-id="15967-174">Im Gegensatz zu komplexen EF6-Typen können eigene Typen in einer separaten Tabelle vom Besitzer gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="15967-174">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="15967-175">Um die Konvention zu überschreiben, die einen eigenen Typ derselben Tabelle wie der Besitzer zuordnet, können Sie einfach aufzurufen `ToTable` und einen anderen Tabellennamen angeben.</span><span class="sxs-lookup"><span data-stu-id="15967-175">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="15967-176">Im folgenden Beispiel werden `OrderDetails` und die beiden Adressen einer separaten Tabelle von zugeordnet `DetailedOrder` :</span><span class="sxs-lookup"><span data-stu-id="15967-176">The following example will map `OrderDetails` and its two addresses to a separate table from `DetailedOrder`:</span></span>

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

<span data-ttu-id="15967-177">Es ist auch möglich `TableAttribute` , das zu verwenden, aber beachten Sie, dass dies fehlschlägt, wenn mehrere Navigationen zum eigenen Typ vorhanden sind, da in diesem Fall mehrere Entitäts Typen derselben Tabelle zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="15967-177">It is also possible to use the `TableAttribute` to accomplish this, but note that this would fail if there are multiple navigations to the owned type since in that case multiple entity types would be mapped to the same table.</span></span>

## <a name="querying-owned-types"></a><span data-ttu-id="15967-178">Abfragen von eigenen Typen</span><span class="sxs-lookup"><span data-stu-id="15967-178">Querying owned types</span></span>

<span data-ttu-id="15967-179">Beim Abfragen des Besitzers werden standardmäßig eigene Typen eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="15967-179">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="15967-180">Es ist nicht erforderlich, die- `Include` Methode zu verwenden, auch wenn die eigenen Typen in einer separaten Tabelle gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="15967-180">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="15967-181">Basierend auf dem zuvor beschriebenen Modell wird die folgende Abfrage `Order` `OrderDetails` und die beiden im Besitz der- `StreetAddresses` Datenbank angezeigt:</span><span class="sxs-lookup"><span data-stu-id="15967-181">Based on the model described before, the following query will get `Order`, `OrderDetails` and the two owned `StreetAddresses` from the database:</span></span>

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a><span data-ttu-id="15967-182">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="15967-182">Limitations</span></span>

<span data-ttu-id="15967-183">Einige dieser Einschränkungen sind grundlegend für die Funktionsweise von Entitäts Typen im Besitz, einige andere sind jedoch Einschränkungen, die in zukünftigen Versionen möglicherweise entfernt werden könnten:</span><span class="sxs-lookup"><span data-stu-id="15967-183">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="15967-184">Entwurfs Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="15967-184">By-design restrictions</span></span>

- <span data-ttu-id="15967-185">Sie können keinen `DbSet<T>` für einen eigenen Typ erstellen.</span><span class="sxs-lookup"><span data-stu-id="15967-185">You cannot create a `DbSet<T>` for an owned type.</span></span>
- <span data-ttu-id="15967-186">Es ist nicht möglich, `Entity<T>()` mit einem eigenen Typ für aufzurufen `ModelBuilder` .</span><span class="sxs-lookup"><span data-stu-id="15967-186">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`.</span></span>
- <span data-ttu-id="15967-187">Instanzen von eigenen Entitäts Typen können nicht von mehreren Besitzern gemeinsam genutzt werden (Dies ist ein bekanntes Szenario für Wert Objekte, das nicht mit Entitäts Typen im Besitz von Entitäten implementiert werden kann).</span><span class="sxs-lookup"><span data-stu-id="15967-187">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types).</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="15967-188">Aktuelle Mängel</span><span class="sxs-lookup"><span data-stu-id="15967-188">Current shortcomings</span></span>

- <span data-ttu-id="15967-189">Besitzende Entitäts Typen dürfen keine Vererbungs Hierarchien aufweisen</span><span class="sxs-lookup"><span data-stu-id="15967-189">Owned entity types cannot have inheritance hierarchies</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="15967-190">Mängel in früheren Versionen</span><span class="sxs-lookup"><span data-stu-id="15967-190">Shortcomings in previous versions</span></span>

- <span data-ttu-id="15967-191">In EF Core 2. x-Referenz Navigation zu besitzenden Entitäts Typen dürfen nicht NULL sein, es sei denn, Sie werden explizit einer separaten Tabelle vom Besitzer zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="15967-191">In EF Core 2.x reference navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner.</span></span>
- <span data-ttu-id="15967-192">In EF Core 3. x werden die Spalten für eigene Entitäts Typen, die derselben Tabelle wie der Besitzer zugeordnet sind, immer als "Nullable" markiert.</span><span class="sxs-lookup"><span data-stu-id="15967-192">In EF Core 3.x the columns for owned entity types mapped to the same table as the owner are always marked as nullable.</span></span>
