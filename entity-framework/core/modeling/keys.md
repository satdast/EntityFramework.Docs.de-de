---
title: Schlüssel-EF Core
description: Konfigurieren von Schlüsseln für Entitäts Typen bei Verwendung von Entity Framework Core
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: 805396a13227aa62ed86ac17c742d055d7a22bbf
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129186"
---
# <a name="keys"></a><span data-ttu-id="c5768-103">Schlüssel</span><span class="sxs-lookup"><span data-stu-id="c5768-103">Keys</span></span>

<span data-ttu-id="c5768-104">Ein Schlüssel dient als eindeutiger Bezeichner für jede Entitäts Instanz.</span><span class="sxs-lookup"><span data-stu-id="c5768-104">A key serves as a unique identifier for each entity instance.</span></span> <span data-ttu-id="c5768-105">Die meisten Entitäten in EF verfügen über einen einzelnen Schlüssel, der dem Konzept eines *Primärschlüssels* in relationalen Datenbanken zugeordnet ist (für Entitäten ohne Schlüssel siehe [Keyless Entities](xref:core/modeling/keyless-entity-types)).</span><span class="sxs-lookup"><span data-stu-id="c5768-105">Most entities in EF have a single key, which maps to the concept of a *primary key* in relational databases (for entities without keys, see [Keyless entities](xref:core/modeling/keyless-entity-types)).</span></span> <span data-ttu-id="c5768-106">Entitäten können über den Primärschlüssel hinaus zusätzliche Schlüssel aufweisen (Weitere Informationen finden Sie in den [Alternativen Schlüsseln](#alternate-keys) ).</span><span class="sxs-lookup"><span data-stu-id="c5768-106">Entities can have additional keys beyond the primary key (see [Alternate Keys](#alternate-keys) for more information).</span></span>

<span data-ttu-id="c5768-107">Gemäß der Konvention wird eine Eigenschaft mit `Id` dem Namen oder `<type name>Id` als Primärschlüssel einer Entität konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c5768-107">By convention, a property named `Id` or `<type name>Id` will be configured as the primary key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3,11)]

> [!NOTE]
> <span data-ttu-id="c5768-108">[Besitzende Entitäts Typen](xref:core/modeling/owned-entities) verwenden verschiedene Regeln, um Schlüssel zu definieren.</span><span class="sxs-lookup"><span data-stu-id="c5768-108">[Owned entity types](xref:core/modeling/owned-entities) use different rules to define keys.</span></span>

<span data-ttu-id="c5768-109">Sie können eine einzelne Eigenschaft wie folgt als Primärschlüssel einer Entität konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="c5768-109">You can configure a single property to be the primary key of an entity as follows:</span></span>

## <a name="data-annotations"></a>[<span data-ttu-id="c5768-110">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="c5768-110">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?name=KeySingle&highlight=3)]

## <a name="fluent-api"></a>[<span data-ttu-id="c5768-111">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="c5768-111">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?name=KeySingle&highlight=4)]

***

<span data-ttu-id="c5768-112">Sie können auch mehrere Eigenschaften als Schlüssel für eine Entität konfigurieren. Dies wird als zusammengesetzter Schlüssel bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="c5768-112">You can also configure multiple properties to be the key of an entity - this is known as a composite key.</span></span> <span data-ttu-id="c5768-113">Zusammengesetzte Schlüssel können nur mithilfe der fließenden API konfiguriert werden. Konventionen richten nie einen zusammengesetzten Schlüssel ein, und Sie können keine Daten Anmerkungen verwenden, um einen zusammengesetzten Schlüssel zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c5768-113">Composite keys can only be configured using the Fluent API; conventions will never set up a composite key, and you can not use Data Annotations to configure one.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?name=KeyComposite&highlight=4)]

## <a name="primary-key-name"></a><span data-ttu-id="c5768-114">Primärschlüssel Name</span><span class="sxs-lookup"><span data-stu-id="c5768-114">Primary key name</span></span>

<span data-ttu-id="c5768-115">Gemäß der Konvention werden Primärschlüssel für relationale Datenbanken mit dem Namen erstellt `PK_<type name>` .</span><span class="sxs-lookup"><span data-stu-id="c5768-115">By convention, on relational databases primary keys are created with the name `PK_<type name>`.</span></span> <span data-ttu-id="c5768-116">Sie können den Namen der PRIMARY KEY-Einschränkung wie folgt konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="c5768-116">You can configure the name of the primary key constraint as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyName.cs?name=KeyName&highlight=5)]

## <a name="key-types-and-values"></a><span data-ttu-id="c5768-117">Schlüsseltypen und-Werte</span><span class="sxs-lookup"><span data-stu-id="c5768-117">Key types and values</span></span>

<span data-ttu-id="c5768-118">Obwohl EF Core die Verwendung von Eigenschaften eines beliebigen primitiven Typs als Primärschlüssel unterstützt, einschließlich `string` , `Guid` `byte[]` und anderen, werden nicht alle Typen von allen Datenbanken als Schlüssel unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c5768-118">While EF Core supports using properties of any primitive type as the primary key, including `string`, `Guid`, `byte[]` and others, not all databases support all types as keys.</span></span> <span data-ttu-id="c5768-119">In einigen Fällen können die Schlüsselwerte automatisch in einen unterstützten Typ konvertiert werden. andernfalls sollte die Konvertierung [manuell angegeben](xref:core/modeling/value-conversions)werden.</span><span class="sxs-lookup"><span data-stu-id="c5768-119">In some cases the key values can be converted to a supported type automatically, otherwise the conversion should be [specified manually](xref:core/modeling/value-conversions).</span></span>

<span data-ttu-id="c5768-120">Schlüsseleigenschaften müssen immer einen nicht standardmäßigen Wert aufweisen, wenn eine neue Entität zum Kontext hinzugefügt wird, aber einige Typen werden [von der Datenbank generiert](xref:core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="c5768-120">Key properties must always have a non-default value when adding a new entity to the context, but some types will be [generated by the database](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="c5768-121">In diesem Fall versucht EF, einen temporären Wert zu generieren, wenn die Entität zu nach Verfolgungs Zwecken hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="c5768-121">In that case EF will try to generate a temporary value when the entity is added for tracking purposes.</span></span> <span data-ttu-id="c5768-122">Nachdem [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges) aufgerufen wurde, wird der temporäre Wert durch den von der Datenbank generierten Wert ersetzt.</span><span class="sxs-lookup"><span data-stu-id="c5768-122">After [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges) is called the temporary value will be replaced by the value generated by the database.</span></span>

> [!Important]
> <span data-ttu-id="c5768-123">Wenn eine Schlüsseleigenschaft den von der Datenbank generierten Wert aufweist und beim Hinzufügen einer Entität ein nicht standardmäßiger Wert angegeben wird, geht EF davon aus, dass die Entität bereits in der Datenbank vorhanden ist, und versucht dann, Sie zu aktualisieren, anstatt eine neue einzufügen.</span><span class="sxs-lookup"><span data-stu-id="c5768-123">If a key property has its value generated by the database and a non-default value is specified when an entity is added, then EF will assume that the entity already exists in the database and will try to update it instead of inserting a new one.</span></span> <span data-ttu-id="c5768-124">Um dies zu vermeiden, deaktivieren Sie die Wert Generierung, oder erfahren Sie, [wie Sie explizite Werte für generierte Eigenschaften angeben](xref:core/saving/explicit-values-generated-properties).</span><span class="sxs-lookup"><span data-stu-id="c5768-124">To avoid this turn off value generation or see [how to specify explicit values for generated properties](xref:core/saving/explicit-values-generated-properties).</span></span>

## <a name="alternate-keys"></a><span data-ttu-id="c5768-125">Alternativschlüssel</span><span class="sxs-lookup"><span data-stu-id="c5768-125">Alternate Keys</span></span>

<span data-ttu-id="c5768-126">Ein alternativer Schlüssel dient als alternativer eindeutiger Bezeichner für jede Entitäts Instanz zusätzlich zum Primärschlüssel. Sie kann als Ziel einer Beziehung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c5768-126">An alternate key serves as an alternate unique identifier for each entity instance in addition to the primary key; it can be used as the target of a relationship.</span></span> <span data-ttu-id="c5768-127">Bei Verwendung einer relationalen Datenbank wird diese dem Konzept eines eindeutigen Indexes/einer eindeutigen Einschränkung in den alternativen Schlüssel Spalten und einer oder mehrerer Foreign Key-Einschränkungen zugeordnet, die auf die Spalte (n) verweisen.</span><span class="sxs-lookup"><span data-stu-id="c5768-127">When using a relational database this maps to the concept of a unique index/constraint on the alternate key column(s) and one or more foreign key constraints that reference the column(s).</span></span>

> [!TIP]
> <span data-ttu-id="c5768-128">Wenn Sie nur die Eindeutigkeit für eine Spalte erzwingen möchten, definieren Sie einen eindeutigen Index anstelle eines alternativen Schlüssels (Weitere Informationen finden Sie unter [Indizes](xref:core/modeling/indexes)).</span><span class="sxs-lookup"><span data-stu-id="c5768-128">If you just want to enforce uniqueness on a column, define a unique index rather than an alternate key (see [Indexes](xref:core/modeling/indexes)).</span></span> <span data-ttu-id="c5768-129">In EF sind alternative Schlüssel schreibgeschützt und bieten zusätzliche Semantik über eindeutige Indizes, da Sie als Ziel eines fremd Schlüssels verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="c5768-129">In EF, alternate keys are read-only and provide additional semantics over unique indexes because they can be used as the target of a foreign key.</span></span>

<span data-ttu-id="c5768-130">Alternative Schlüssel werden in der Regel bei Bedarf für Sie eingeführt, und Sie müssen Sie nicht manuell konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="c5768-130">Alternate keys are typically introduced for you when needed and you do not need to manually configure them.</span></span> <span data-ttu-id="c5768-131">Gemäß der Konvention wird ein alternativer Schlüssel für Sie eingeführt, wenn Sie eine Eigenschaft identifizieren, die nicht der Primärschlüssel als Ziel einer Beziehung ist.</span><span class="sxs-lookup"><span data-stu-id="c5768-131">By convention, an alternate key is introduced for you when you identify a property which isn't the primary key as the target of a relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

<span data-ttu-id="c5768-132">Sie können auch eine einzelne Eigenschaft als alternativen Schlüssel konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="c5768-132">You can also configure a single property to be an alternate key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=4)]

<span data-ttu-id="c5768-133">Sie können auch mehrere Eigenschaften als alternativen Schlüssel (als zusammengesetzten alternativen Schlüssel bezeichnet) konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="c5768-133">You can also configure multiple properties to be an alternate key (known as a composite alternate key):</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=4)]

<span data-ttu-id="c5768-134">Schließlich werden der Index und die Einschränkung, die für einen alternativen Schlüssel eingeführt werden, gemäß der Konvention benannt `AK_<type name>_<property name>` (bei zusammengesetzten Alternativen Schlüsseln zu einer durch Trennzeichen `<property name>` getrennten Liste mit Eigenschaftsnamen).</span><span class="sxs-lookup"><span data-stu-id="c5768-134">Finally, by convention, the index and constraint that are introduced for an alternate key will be named `AK_<type name>_<property name>` (for composite alternate keys `<property name>` becomes an underscore separated list of property names).</span></span> <span data-ttu-id="c5768-135">Sie können den Namen des Indexes und der Unique-Einschränkung für den alternativen Schlüssel konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="c5768-135">You can configure the name of the alternate key's index and unique constraint:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyName.cs?name=AlternateKeyName&highlight=5)]
