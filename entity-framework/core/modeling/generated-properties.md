---
title: Generierte Werte-EF Core
description: Konfigurieren der Wert Generierung für Eigenschaften bei Verwendung von Entity Framework Core
author: AndriySvyryd
ms.date: 1/10/2021
uid: core/modeling/generated-properties
ms.openlocfilehash: 76fa4454c88a5ef7afb9864c2a4b1063ac75e37e
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983546"
---
# <a name="generated-values"></a><span data-ttu-id="a5183-103">Generierte Werte</span><span class="sxs-lookup"><span data-stu-id="a5183-103">Generated Values</span></span>

<span data-ttu-id="a5183-104">Die Werte von Daten Bank Spalten können auf verschiedene Arten generiert werden: Primärschlüssel Spalten sind häufig automatisch inkrementierende Ganzzahlen, andere Spalten haben Standardwerte oder berechnete Werte usw. Auf dieser Seite werden verschiedene Muster für die Generierung von Konfigurations Werten mit EF Core erläutert.</span><span class="sxs-lookup"><span data-stu-id="a5183-104">Database columns can have their values generated in various ways: primary key columns are frequently auto-incrementing integers, other columns have default or computed values, etc. This page details various patterns for configuration value generation with EF Core.</span></span>

## <a name="default-values"></a><span data-ttu-id="a5183-105">Standardwerte</span><span class="sxs-lookup"><span data-stu-id="a5183-105">Default values</span></span>

<span data-ttu-id="a5183-106">Bei relationalen Datenbanken kann eine Spalte mit einem Standardwert konfiguriert werden. Wenn eine Zeile ohne einen Wert für diese Spalte eingefügt wird, wird der Standardwert verwendet.</span><span class="sxs-lookup"><span data-stu-id="a5183-106">On relational databases, a column can be configured with a default value; if a row is inserted without a value for that column, the default value will be used.</span></span>

<span data-ttu-id="a5183-107">Sie können einen Standardwert für eine Eigenschaft konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="a5183-107">You can configure a default value on a property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValue.cs?name=DefaultValue&highlight=5)]

<span data-ttu-id="a5183-108">Sie können auch ein SQL-Fragment angeben, das zum Berechnen des Standardwerts verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="a5183-108">You can also specify a SQL fragment that is used to calculate the default value:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

## <a name="computed-columns"></a><span data-ttu-id="a5183-109">Berechnete Spalten</span><span class="sxs-lookup"><span data-stu-id="a5183-109">Computed columns</span></span>

<span data-ttu-id="a5183-110">Bei den meisten relationalen Datenbanken kann eine Spalte so konfiguriert werden, dass ihr Wert in der Datenbank berechnet wird, in der Regel mit einem Ausdruck, der auf andere Spalten verweist:</span><span class="sxs-lookup"><span data-stu-id="a5183-110">On most relational databases, a column can be configured to have its value computed in the database, typically with an expression referring to other columns:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=DefaultComputedColumn&highlight=3)]

<span data-ttu-id="a5183-111">Im obigen Beispiel wird eine *virtuelle* berechnete Spalte erstellt, deren Wert jedes Mal berechnet wird, wenn Sie aus der Datenbank abgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="a5183-111">The above creates a *virtual* computed column, whose value is computed every time it is fetched from the database.</span></span> <span data-ttu-id="a5183-112">Sie können auch angeben, dass eine berechnete Spalte *gespeichert* werden soll (manchmal als *persistent bezeichnet)*, was bedeutet, dass Sie bei jedem Update der Zeile berechnet wird und auf dem Datenträger neben regulären Spalten gespeichert wird:</span><span class="sxs-lookup"><span data-stu-id="a5183-112">You may also specify that a computed column be *stored* (sometimes called *persisted*), meaning that it is computed on every update of the row, and is stored on disk alongside regular columns:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ComputedColumn.cs?name=StoredComputedColumn&highlight=3)]

> [!NOTE]
> <span data-ttu-id="a5183-113">Unterstützung für das Erstellen gespeicherter berechneter Spalten wurde in EF Core 5,0 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="a5183-113">Support for creating stored computed columns was added in EF Core 5.0.</span></span>

## <a name="primary-keys"></a><span data-ttu-id="a5183-114">Primärschlüssel</span><span class="sxs-lookup"><span data-stu-id="a5183-114">Primary keys</span></span>

<span data-ttu-id="a5183-115">Gemäß der Konvention sind nicht zusammengesetzte Primärschlüssel vom Typ Short, int, Long oder GUID so eingerichtet, dass Werte für eingefügte Entitäten generiert werden, wenn ein Wert nicht von der Anwendung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a5183-115">By convention, non-composite primary keys of type short, int, long, or Guid are set up to have values generated for inserted entities if a value isn't provided by the application.</span></span> <span data-ttu-id="a5183-116">Ihr Datenbankanbieter übernimmt in der Regel die erforderliche Konfiguration. Beispielsweise wird ein numerischer Primärschlüssel in SQL Server automatisch als Identitäts Spalte festgelegt.</span><span class="sxs-lookup"><span data-stu-id="a5183-116">Your database provider typically takes care of the necessary configuration; for example, a numeric primary key in SQL Server is automatically set up to be an IDENTITY column.</span></span>

<span data-ttu-id="a5183-117">Weitere Informationen finden Sie in [der Dokumentation zu Schlüsseln](xref:core/modeling/keys).</span><span class="sxs-lookup"><span data-stu-id="a5183-117">For more information, [see the documentation about keys](xref:core/modeling/keys).</span></span>

## <a name="explicitly-configuring-value-generation"></a><span data-ttu-id="a5183-118">Explizites Konfigurieren der Wert Generierung</span><span class="sxs-lookup"><span data-stu-id="a5183-118">Explicitly configuring value generation</span></span>

<span data-ttu-id="a5183-119">Wir haben oben gesehen, dass EF Core automatisch die Wert Generierung für Primärschlüssel festlegt, aber wir möchten dies möglicherweise für nicht Schlüsseleigenschaften verwenden.</span><span class="sxs-lookup"><span data-stu-id="a5183-119">We saw above that EF Core automatically sets up value generation for primary keys - but we may want to do the same for non-key properties.</span></span> <span data-ttu-id="a5183-120">Sie können jede Eigenschaft so konfigurieren, dass ihr Wert für eingefügte Entitäten wie folgt generiert wird:</span><span class="sxs-lookup"><span data-stu-id="a5183-120">You can configure any property to have its value generated for inserted entities as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="a5183-121">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="a5183-121">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

### <a name="fluent-api"></a>[<span data-ttu-id="a5183-122">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="a5183-122">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAdd.cs?name=ValueGeneratedOnAdd&highlight=5)]

***

<span data-ttu-id="a5183-123">Ebenso kann eine Eigenschaft so konfiguriert werden, dass ihr Wert beim Hinzufügen oder aktualisieren generiert wird:</span><span class="sxs-lookup"><span data-stu-id="a5183-123">Similarly, a property can be configured to have its value generated on add or update:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="a5183-124">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="a5183-124">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

### <a name="fluent-api"></a>[<span data-ttu-id="a5183-125">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="a5183-125">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdate.cs?name=ValueGeneratedOnAddOrUpdate&highlight=5)]

<span data-ttu-id="a5183-126">\*\*_</span><span class="sxs-lookup"><span data-stu-id="a5183-126">\*\*_</span></span>

> [!WARNING]
> <span data-ttu-id="a5183-127">Anders als bei Standardwerten oder berechneten Spalten geben wir nicht an, _how \* die Werte generiert werden sollen. Das hängt vom verwendeten Datenbankanbieter ab.</span><span class="sxs-lookup"><span data-stu-id="a5183-127">Unlike with default values or computed columns, we are not specifying _how\* the values are to be generated; that depends on the database provider being used.</span></span> <span data-ttu-id="a5183-128">Datenbankanbieter richten möglicherweise automatisch die Wert Generierung für einige Eigenschafts Typen ein, bei anderen müssen Sie jedoch möglicherweise manuell festlegen, wie der Wert generiert wird.</span><span class="sxs-lookup"><span data-stu-id="a5183-128">Database providers may automatically set up value generation for some property types, but others may require you to manually set up how the value is generated.</span></span>
>
> <span data-ttu-id="a5183-129">Wenn beispielsweise auf SQL Server eine GUID-Eigenschaft als Wert, der beim Hinzufügen generiert wird, konfiguriert ist, führt der Anbieter automatisch die Client seitige Wert Generierung aus, wobei ein Algorithmus verwendet wird, um optimale sequenzielle GUID-Werte zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a5183-129">For example, on SQL Server, when a GUID property is configured as value generated on add, the provider automatically performs value generation client-side, using an algorithm to generate optimal sequential GUID values.</span></span> <span data-ttu-id="a5183-130">`ValueGeneratedOnAdd()`Die Angabe von für eine DateTime-Eigenschaft hat jedoch keine Auswirkung (Weitere Informationen[finden Sie im nachfolgenden Abschnitt für die DateTime-Wert Generierung](#datetime-value-generation)).</span><span class="sxs-lookup"><span data-stu-id="a5183-130">However, specifying `ValueGeneratedOnAdd()` on a DateTime property will have no effect ([see the section below for DateTime value generation](#datetime-value-generation)).</span></span>
>
> <span data-ttu-id="a5183-131">Ebenso werden Byte []-Eigenschaften, die beim Hinzufügen oder aktualisieren als generiert und als Parallelitäts Token gekennzeichnet sind, mit dem rowversion-Datentyp eingerichtet, sodass Werte automatisch in der Datenbank generiert werden.</span><span class="sxs-lookup"><span data-stu-id="a5183-131">Similarly, byte[] properties that are configured as generated on add or update and marked as concurrency tokens are set up with the rowversion data type, so that values are automatically generated in the database.</span></span> <span data-ttu-id="a5183-132">Das Angeben von `ValueGeneratedOnAddOrUpdate()` hat jedoch keine Auswirkungen.</span><span class="sxs-lookup"><span data-stu-id="a5183-132">However, specifying `ValueGeneratedOnAddOrUpdate()` will again have no effect.</span></span>
>
> [!NOTE]
> <span data-ttu-id="a5183-133">Abhängig vom verwendeten Datenbankanbieter können die Werte Client seitig von EF oder in der Datenbank generiert werden.</span><span class="sxs-lookup"><span data-stu-id="a5183-133">Depending on the database provider being used, values may be generated client side by EF or in the database.</span></span> <span data-ttu-id="a5183-134">Wenn der Wert von der Datenbank generiert wird, kann EF einen temporären Wert zuweisen, wenn Sie die Entität zum Kontext hinzufügen. Dieser temporäre Wert wird dann durch den von der Datenbank generierten Wert ersetzt `SaveChanges()` .</span><span class="sxs-lookup"><span data-stu-id="a5183-134">If the value is generated by the database, then EF may assign a temporary value when you add the entity to the context; this temporary value will then be replaced by the database generated value during `SaveChanges()`.</span></span> <span data-ttu-id="a5183-135">Weitere Informationen finden Sie [in der Dokumentation zu temporären Werten](xref:core/change-tracking/explicit-tracking#temporary-values).</span><span class="sxs-lookup"><span data-stu-id="a5183-135">For more information, [see the docs on temporary values](xref:core/change-tracking/explicit-tracking#temporary-values).</span></span>

## <a name="datetime-value-generation"></a><span data-ttu-id="a5183-136">Datums-/uhrzeitwertgenerierung</span><span class="sxs-lookup"><span data-stu-id="a5183-136">Date/time value generation</span></span>

<span data-ttu-id="a5183-137">Eine häufige Anforderung besteht darin, eine Daten Bank Spalte zu haben, die das Datum und die Uhrzeit für den ersten Einfügevorgang der Spalte (beim hinzufügen generierten Wert) oder für den Zeitpunkt der letzten Aktualisierung (beim Hinzufügen oder aktualisieren generierten Wert) enthält.</span><span class="sxs-lookup"><span data-stu-id="a5183-137">A common request is to have a database column which contains the date/time for when the column was first inserted (value generated on add), or for when it was last updated (value generated on add or update).</span></span> <span data-ttu-id="a5183-138">Wie es verschiedene Strategien gibt, legen EF Core Anbieter in der Regel keine Wert Generierung für Datums-/Uhrzeitspalten fest. Dies müssen Sie selbst konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a5183-138">As there are various strategies to do this, EF Core providers usually don't set up value generation automatically for date/time columns - you have to configure this yourself.</span></span>

### <a name="creation-timestamp"></a><span data-ttu-id="a5183-139">Erstellungszeit Stempel</span><span class="sxs-lookup"><span data-stu-id="a5183-139">Creation timestamp</span></span>

<span data-ttu-id="a5183-140">Das Konfigurieren einer Datums-/uhrzeitspalte mit dem Erstellungszeit Stempel der Zeile ist in der Regel eine Frage, wie ein Standardwert mit der entsprechenden SQL-Funktion konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="a5183-140">Configuring a date/time column to have the creation timestamp of the row is usually a matter of configuring a default value with the appropriate SQL function.</span></span> <span data-ttu-id="a5183-141">Beispielsweise können Sie auf SQL Server Folgendes verwenden:</span><span class="sxs-lookup"><span data-stu-id="a5183-141">For example, on SQL Server you may use the following:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultValueSql.cs?name=DefaultValueSql&highlight=5)]

<span data-ttu-id="a5183-142">Stellen Sie sicher, dass Sie die entsprechende Funktion auswählen, da mehrere vorhanden sein können (z. b. `GETDATE()` vs `GETUTCDATE()` ).</span><span class="sxs-lookup"><span data-stu-id="a5183-142">Be sure to select the appropriate function, as several may exist (e.g. `GETDATE()` vs. `GETUTCDATE()`).</span></span>

### <a name="update-timestamp"></a><span data-ttu-id="a5183-143">Zeitstempel aktualisieren</span><span class="sxs-lookup"><span data-stu-id="a5183-143">Update timestamp</span></span>

<span data-ttu-id="a5183-144">Obwohl gespeicherte berechnete Spalten eine gute Lösung für die Verwaltung der zuletzt aktualisierten Zeitstempel sind, können Datenbanken in der Regel keine Funktionen wie `GETDATE()` in einer berechneten Spalte angeben.</span><span class="sxs-lookup"><span data-stu-id="a5183-144">Although stored computed columns seem like a good solution for managing last-updated timestamps, databases usually don't allow specifying functions such as `GETDATE()` in a computed column.</span></span> <span data-ttu-id="a5183-145">Als Alternative können Sie einen Daten Bank Auslösers einrichten, um denselben Effekt zu erzielen:</span><span class="sxs-lookup"><span data-stu-id="a5183-145">As an alternative, you can set up a database trigger to achieve the same effect:</span></span>

```sql
CREATE TRIGGER [dbo].[Blogs_UPDATE] ON [dbo].[Blogs]
    AFTER UPDATE
AS
BEGIN
    SET NOCOUNT ON;

    IF ((SELECT TRIGGER_NESTLEVEL()) > 1) RETURN;

    DECLARE @Id INT

    SELECT @Id = INSERTED.BlogId
    FROM INSERTED

    UPDATE dbo.Blogs
    SET LastUpdated = GETDATE()
    WHERE BlogId = @Id
END
```

<span data-ttu-id="a5183-146">Weitere Informationen zum Erstellen von Triggern [finden Sie in der Dokumentation zur Verwendung von Rohdaten in SQL bei Migrationen](xref:core/managing-schemas/migrations/managing#adding-raw-sql).</span><span class="sxs-lookup"><span data-stu-id="a5183-146">For information on creating triggers, [see the documentation on using raw SQL in migrations](xref:core/managing-schemas/migrations/managing#adding-raw-sql).</span></span>

## <a name="overriding-value-generation"></a><span data-ttu-id="a5183-147">Überschreiben der Wert Generierung</span><span class="sxs-lookup"><span data-stu-id="a5183-147">Overriding value generation</span></span>

<span data-ttu-id="a5183-148">Obwohl eine Eigenschaft für die Generierung von Werten konfiguriert ist, können Sie in vielen Fällen trotzdem explizit einen Wert angeben.</span><span class="sxs-lookup"><span data-stu-id="a5183-148">Although a property is configured for value generation, in many cases you may still explicitly specify a value for it.</span></span> <span data-ttu-id="a5183-149">Ob dies tatsächlich funktioniert, hängt vom spezifischen Wert Generierungs Mechanismus ab, der konfiguriert wurde. Obwohl Sie einen expliziten Wert angeben dürfen, anstatt den Standardwert einer Spalte zu verwenden, kann dies nicht mit berechneten Spalten erfolgen.</span><span class="sxs-lookup"><span data-stu-id="a5183-149">Whether this will actually work depends on the specific value generation mechanism that has been configured; while you may specify an explicit value instead of using a column's default value, the same cannot be done with computed columns.</span></span>

<span data-ttu-id="a5183-150">Um die Wert Generierung mit einem expliziten Wert zu überschreiben, legen Sie einfach die-Eigenschaft auf einen beliebigen Wert fest, der nicht der CLR-Standardwert für den Eigenschaftentyp ist ( `null` für, `string` für, `0` `int` `Guid.Empty` `Guid` usw.).</span><span class="sxs-lookup"><span data-stu-id="a5183-150">To override value generation with an explicit value, simply set the property to any value that is not the CLR default value for that property's type (`null` for `string`, `0` for `int`, `Guid.Empty` for `Guid`, etc.).</span></span>

> [!NOTE]
> <span data-ttu-id="a5183-151">Der Versuch, explizite Werte in SQL Server Identität einzufügen, schlägt standardmäßig fehl. [eine Problem Umgehung finden](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns)Sie in diesen Dokumentationen.</span><span class="sxs-lookup"><span data-stu-id="a5183-151">Trying to insert explicit values into SQL Server IDENTITY fails by default; [see these docs for a workaround](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns).</span></span>

<span data-ttu-id="a5183-152">Um einen expliziten Wert für Eigenschaften bereitzustellen, die als Wert konfiguriert wurden, der beim Hinzufügen oder aktualisieren generiert wurde, müssen Sie auch die-Eigenschaft wie folgt konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="a5183-152">To provide an explicit value for properties that have been configured as value generated on add or update, you must also configure the property as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior.cs?name=ValueGeneratedOnAddOrUpdateWithPropertySaveBehavior&highlight=5)]

## <a name="no-value-generation"></a><span data-ttu-id="a5183-153">Keine Wert Generierung</span><span class="sxs-lookup"><span data-stu-id="a5183-153">No value generation</span></span>

<span data-ttu-id="a5183-154">Abgesehen von bestimmten Szenarien, wie z. b. den oben beschriebenen, haben Eigenschaften in der Regel keine konfigurierte Wert Generierung. Dies bedeutet, dass die Anwendung immer einen Wert bereitstellen muss, der in der Datenbank gespeichert werden soll.</span><span class="sxs-lookup"><span data-stu-id="a5183-154">Apart from specific scenarios such as those described above, properties typically have no value generation configured; this means that it's up to the application to always supply a value to be saved to the database.</span></span> <span data-ttu-id="a5183-155">Dieser Wert muss neuen Entitäten zugewiesen werden, bevor Sie dem Kontext hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="a5183-155">This value must be assigned to new entities before they are added to the context.</span></span>

<span data-ttu-id="a5183-156">In einigen Fällen möchten Sie jedoch möglicherweise die durch Konvention festgelegte Wert Generierung deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="a5183-156">However, in some cases you may want to disable value generation that has been set up by convention.</span></span> <span data-ttu-id="a5183-157">Beispielsweise wird ein Primärschlüssel vom Typ int in der Regel implizit als Value-generated-on-Add (z. b. Identitäts Spalte auf SQL Server) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a5183-157">For example, a primary key of type int is usually implicitly configured as value-generated-on-add (e.g. identity column on SQL Server).</span></span> <span data-ttu-id="a5183-158">Dies kann über Folgendes deaktiviert werden:</span><span class="sxs-lookup"><span data-stu-id="a5183-158">You can disable this via the following:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="a5183-159">Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="a5183-159">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="a5183-160">Fluent-API</span><span class="sxs-lookup"><span data-stu-id="a5183-160">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ValueGeneratedNever.cs?name=ValueGeneratedNever&highlight=5)]

***
