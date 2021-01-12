---
title: Wert Vergleiche-EF Core
description: Verwenden von Wert vergleichen zum Steuern der Art EF Core Vergleichen von Eigenschafts Werten
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: 618341315de05f6efae8f43384809ed72226e18b
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128510"
---
# <a name="value-comparers"></a><span data-ttu-id="c1125-103">Wert Vergleiche</span><span class="sxs-lookup"><span data-stu-id="c1125-103">Value Comparers</span></span>

> [!NOTE]
> <span data-ttu-id="c1125-104">Dieses Feature wurde in EF Core 3.0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="c1125-104">This feature was introduced in EF Core 3.0.</span></span>

> [!TIP]
> <span data-ttu-id="c1125-105">Den Code in diesem Dokument finden Sie auf GitHub als Ausführ [bares Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span><span class="sxs-lookup"><span data-stu-id="c1125-105">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="background"></a><span data-ttu-id="c1125-106">Hintergrund</span><span class="sxs-lookup"><span data-stu-id="c1125-106">Background</span></span>

<span data-ttu-id="c1125-107">Die Änderungs Nachverfolgung bedeutet, dass EF Core automatisch bestimmt, welche Änderungen von der Anwendung auf einer geladenen Entitäts Instanz durchgeführt wurden, damit diese Änderungen wieder in der Datenbank gespeichert werden können, wenn <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="c1125-107">Change tracking means that EF Core automatically determines what changes were performed by the application on a loaded entity instance, so that those changes can be saved back to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="c1125-108">EF Core wird dies normalerweise durch Erstellen einer *Momentaufnahme* der Instanz, wenn Sie aus der Datenbank geladen wird, und *vergleichen* dieser Momentaufnahme mit der-Instanz, die an die Anwendung übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="c1125-108">EF Core usually performs this by taking a *snapshot* of the instance when it's loaded from the database, and *comparing* that snapshot to the instance handed out to the application.</span></span>

<span data-ttu-id="c1125-109">EF Core verfügt über integrierte Logik zum snapshoten und Vergleichen der meisten Standardtypen, die in Datenbanken verwendet werden, sodass sich Benutzer in der Regel nicht um dieses Thema kümmern müssen.</span><span class="sxs-lookup"><span data-stu-id="c1125-109">EF Core comes with built-in logic for snapshotting and comparing most standard types used in databases, so users don't usually need to worry about this topic.</span></span> <span data-ttu-id="c1125-110">Wenn eine Eigenschaft jedoch über einen [Wert Konverter](xref:core/modeling/value-conversions)zugeordnet wird, muss EF Core einen Vergleich mit beliebigen Benutzer Typen durchführen, die möglicherweise komplex sind.</span><span class="sxs-lookup"><span data-stu-id="c1125-110">However, when a property is mapped through a [value converter](xref:core/modeling/value-conversions), EF Core needs to perform comparison on arbitrary user types, which may be complex.</span></span> <span data-ttu-id="c1125-111">Standardmäßig verwendet EF Core den von Typen definierten Standard Gleichheits Vergleich (z. b. die- `Equals` Methode). für snapshotts werden Werttypen kopiert, um die Momentaufnahme zu erstellen, während für Verweis Typen kein Kopieren stattfindet und dieselbe Instanz als Momentaufnahme verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c1125-111">By default, EF Core uses the default equality comparison defined by types (e.g. the `Equals` method); for snapshotting, value types are copied to produce the snapshot, while for reference types no copying occurs, and the same instance is used as the snapshot.</span></span>

<span data-ttu-id="c1125-112">In Fällen, in denen das integrierte Vergleichs Verhalten nicht geeignet ist, können Benutzer einen *Wert Vergleich* bereitstellen, der Logik für snapshotevorgänge, vergleichen und Berechnen eines Hashcodes enthält.</span><span class="sxs-lookup"><span data-stu-id="c1125-112">In cases where the built-in comparison behavior isn't appropriate, users may provide a *value comparer*, which contains logic for snapshotting, comparing and calculating a hash code.</span></span> <span data-ttu-id="c1125-113">Im folgenden Beispiel wird die Wert Konvertierung für die-Eigenschaft so festgelegt, dass Sie `List<int>` in eine JSON-Zeichenfolge in der-Datenbank konvertiert wird. Außerdem wird ein entsprechender Wert Vergleich definiert:</span><span class="sxs-lookup"><span data-stu-id="c1125-113">For example, the following sets up value conversion for `List<int>` property to be value converted to a JSON string in the database, and defines an appropriate value comparer as well:</span></span>

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

<span data-ttu-id="c1125-114">Weitere Informationen finden Sie weiter unten unter [änderbare Klassen](#mutable-classes) .</span><span class="sxs-lookup"><span data-stu-id="c1125-114">See [mutable classes](#mutable-classes) below for further details.</span></span>

<span data-ttu-id="c1125-115">Beachten Sie, dass Wert Vergleiche auch verwendet werden, um zu bestimmen, ob beim Auflösen von Beziehungen zwei Schlüsselwerte identisch sind. Dies wird im folgenden erläutert.</span><span class="sxs-lookup"><span data-stu-id="c1125-115">Note that value comparers are also used when determining whether two key values are the same when resolving relationships; this is explained below.</span></span>

## <a name="shallow-vs-deep-comparison"></a><span data-ttu-id="c1125-116">Flache und tiefer gehende Vergleiche</span><span class="sxs-lookup"><span data-stu-id="c1125-116">Shallow vs. deep comparison</span></span>

<span data-ttu-id="c1125-117">Für kleine, unveränderliche Werttypen, wie z. b. `int` , EF Core die Standardlogik der Standardlogik funktioniert: der Wert wird unverändert bei snapshotet kopiert und mit dem integrierten Gleichheits Vergleich des Typs verglichen.</span><span class="sxs-lookup"><span data-stu-id="c1125-117">For small, immutable value types such as `int`, EF Core's default logic works well: the value is copied as-is when snapshotted, and compared with the type's built-in equality comparison.</span></span> <span data-ttu-id="c1125-118">Wenn Sie einen eigenen Wert Vergleich implementieren, ist es wichtig zu berücksichtigen, ob eine Tiefe oder flache Vergleichs Logik (und snapshote) geeignet ist.</span><span class="sxs-lookup"><span data-stu-id="c1125-118">When implementing your own value comparer, it's important to consider whether deep or shallow comparison (and snapshotting) logic is appropriate.</span></span>

<span data-ttu-id="c1125-119">Beachten Sie ggf. Byte Arrays, die beliebig groß sein können.</span><span class="sxs-lookup"><span data-stu-id="c1125-119">Consider byte arrays, which can be arbitrarily large.</span></span> <span data-ttu-id="c1125-120">Diese können verglichen werden:</span><span class="sxs-lookup"><span data-stu-id="c1125-120">These could be compared:</span></span>

* <span data-ttu-id="c1125-121">Als Verweis, sodass ein Unterschied nur erkannt wird, wenn ein neues Bytearray verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c1125-121">By reference, such that a difference is only detected if a new byte array is used</span></span>
* <span data-ttu-id="c1125-122">Durch einen tiefgreifenden Vergleich, sodass die Mutation der Bytes im Array erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="c1125-122">By deep comparison, such that mutation of the bytes in the array is detected</span></span>

<span data-ttu-id="c1125-123">Standardmäßig verwendet EF Core den ersten dieser Ansätze für nicht-Schlüssel Byte Arrays.</span><span class="sxs-lookup"><span data-stu-id="c1125-123">By default, EF Core uses the first of these approaches for non-key byte arrays.</span></span> <span data-ttu-id="c1125-124">Das heißt, nur Verweise werden verglichen, und eine Änderung wird nur erkannt, wenn ein vorhandenes Bytearray durch einen neuen ersetzt wird.</span><span class="sxs-lookup"><span data-stu-id="c1125-124">That is, only references are compared and a change is detected only when an existing byte array is replaced with a new one.</span></span> <span data-ttu-id="c1125-125">Dies ist eine pragmatische Entscheidung, bei der das Kopieren ganzer Arrays und das Vergleichen mit Byte bei der Ausführung vermieden wird <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> , und das gängige Szenario, bei dem ein Bild durch ein anderes ersetzt wird, in einer leistungsfähigen Weise behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="c1125-125">This is a pragmatic decision that avoids copying entire arrays and comparing them byte-to-byte when executing <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>, and the common scenario of replacing, say, one image with another is handled in a performant way.</span></span>

<span data-ttu-id="c1125-126">Auf der anderen Seite funktioniert die Verweis Gleichheit nicht, wenn Byte Arrays zur Darstellung von Binär Schlüsseln verwendet werden, da es sehr unwahrscheinlich ist, dass eine FK-Eigenschaft auf _dieselbe Instanz_ wie eine PK-Eigenschaft festgelegt ist, mit der Sie verglichen werden muss.</span><span class="sxs-lookup"><span data-stu-id="c1125-126">On the other hand, reference equality would not work when byte arrays are used to represent binary keys, since it's very unlikely that an FK property is set to the _same instance_ as a PK property to which it needs to be compared.</span></span> <span data-ttu-id="c1125-127">Daher verwendet EF Core Tiefe Vergleiche für Byte Arrays, die als Schlüssel fungieren. Es ist unwahrscheinlich, dass eine große Leistung erzielt wird, da binäre Schlüssel in der Regel kurz sind.</span><span class="sxs-lookup"><span data-stu-id="c1125-127">Therefore, EF Core uses deep comparisons for byte arrays acting as keys; this is unlikely to have a big performance hit since binary keys are usually short.</span></span>

<span data-ttu-id="c1125-128">Beachten Sie, dass die gewählte Vergleichs-und snapshoflogik einander entsprechen muss: bei Deep Comparison muss Deep Snapshot ordnungsgemäß funktionieren.</span><span class="sxs-lookup"><span data-stu-id="c1125-128">Note that the chosen comparison and snapshotting logic must correspond to each other: deep comparison requires deep snapshotting to function correctly.</span></span>

## <a name="simple-immutable-classes"></a><span data-ttu-id="c1125-129">Einfache unveränderliche Klassen</span><span class="sxs-lookup"><span data-stu-id="c1125-129">Simple immutable classes</span></span>

<span data-ttu-id="c1125-130">Stellen Sie sich eine Eigenschaft vor, die einen Wert Konverter verwendet, um eine einfache, unveränderliche Klasse zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="c1125-130">Consider a property that uses a value converter to map a simple, immutable class.</span></span>

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

<span data-ttu-id="c1125-131">Eigenschaften dieses Typs benötigen keine besonderen Vergleiche oder Momentaufnahmen aus folgenden Gründen:</span><span class="sxs-lookup"><span data-stu-id="c1125-131">Properties of this type do not need special comparisons or snapshots because:</span></span>

* <span data-ttu-id="c1125-132">Gleichheit wird überschrieben, sodass verschiedene Instanzen ordnungsgemäß verglichen werden.</span><span class="sxs-lookup"><span data-stu-id="c1125-132">Equality is overridden so that different instances will compare correctly</span></span>
* <span data-ttu-id="c1125-133">Der Typ ist unveränderlich, sodass es nicht möglich ist, einen Momentaufnahme Wert zu mutieren.</span><span class="sxs-lookup"><span data-stu-id="c1125-133">The type is immutable, so there is no chance of mutating a snapshot value</span></span>

<span data-ttu-id="c1125-134">In diesem Fall ist das Standardverhalten von EF Core in Ordnung.</span><span class="sxs-lookup"><span data-stu-id="c1125-134">So in this case the default behavior of EF Core is fine as it is.</span></span>

## <a name="simple-immutable-structs"></a><span data-ttu-id="c1125-135">Einfache unveränderliche Strukturen</span><span class="sxs-lookup"><span data-stu-id="c1125-135">Simple immutable structs</span></span>

<span data-ttu-id="c1125-136">Die Zuordnung für einfache Strukturen ist auch einfach und erfordert keine speziellen Vergleiche oder snapshote.</span><span class="sxs-lookup"><span data-stu-id="c1125-136">The mapping for simple structs is also simple and requires no special comparers or snapshotting.</span></span>

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

<span data-ttu-id="c1125-137">EF Core verfügt über integrierte Unterstützung für das Erstellen von kompilierten, Mitgliedschafts bezogenen Vergleichen von Struktur Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="c1125-137">EF Core has built-in support for generating compiled, memberwise comparisons of struct properties.</span></span>
<span data-ttu-id="c1125-138">Dies bedeutet, dass Strukturen nicht für EF Core überschrieben werden müssen, aber Sie können dies auch aus [anderen Gründen](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type)auswählen.</span><span class="sxs-lookup"><span data-stu-id="c1125-138">This means structs don't need to have equality overridden for EF Core, but you may still choose to do this for [other reasons](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span></span>
<span data-ttu-id="c1125-139">Außerdem ist das besondere snapshotieren nicht erforderlich, da Strukturen unveränderlich sind und immer auf die gleiche Weise kopiert werden.</span><span class="sxs-lookup"><span data-stu-id="c1125-139">Also, special snapshotting is not needed since structs are immutable and are always copied memberwise anyway.</span></span>
<span data-ttu-id="c1125-140">(Dies gilt auch für änderbare Strukturen, jedoch [sollten änderbare Strukturen im allgemeinen vermieden werden](/dotnet/csharp/write-safe-efficient-code).)</span><span class="sxs-lookup"><span data-stu-id="c1125-140">(This is also true for mutable structs, but [mutable structs should in general be avoided](/dotnet/csharp/write-safe-efficient-code).)</span></span>

## <a name="mutable-classes"></a><span data-ttu-id="c1125-141">Änderbare Klassen</span><span class="sxs-lookup"><span data-stu-id="c1125-141">Mutable classes</span></span>

<span data-ttu-id="c1125-142">Es wird empfohlen, nach Möglichkeit unveränderliche Typen (Klassen oder Strukturen) mit Wert Konvertern zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c1125-142">It is recommended that you use immutable types (classes or structs) with value converters when possible.</span></span>
<span data-ttu-id="c1125-143">Dies ist in der Regel effizienter und verfügt über eine sauberere Semantik als die Verwendung eines änderbaren Typs.</span><span class="sxs-lookup"><span data-stu-id="c1125-143">This is usually more efficient and has cleaner semantics than using a mutable type.</span></span>

<span data-ttu-id="c1125-144">Allerdings ist es üblich, die Eigenschaften von Typen zu verwenden, die von der Anwendung nicht geändert werden können.</span><span class="sxs-lookup"><span data-stu-id="c1125-144">However, that being said, it is common to use properties of types that the application cannot change.</span></span>
<span data-ttu-id="c1125-145">Beispiel: Zuordnung einer Eigenschaft, die eine Liste mit Zahlen enthält:</span><span class="sxs-lookup"><span data-stu-id="c1125-145">For example, mapping a property containing a list of numbers:</span></span>

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

<span data-ttu-id="c1125-146">Die <xref:System.Collections.Generic.List%601>-Klasse:</span><span class="sxs-lookup"><span data-stu-id="c1125-146">The <xref:System.Collections.Generic.List%601> class:</span></span>

* <span data-ttu-id="c1125-147">Hat Verweis Gleichheit. zwei Listen, die dieselben Werte enthalten, werden als verschieden behandelt.</span><span class="sxs-lookup"><span data-stu-id="c1125-147">Has reference equality; two lists containing the same values are treated as different.</span></span>
* <span data-ttu-id="c1125-148">Ist änderbar; Werte in der Liste können hinzugefügt und entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="c1125-148">Is mutable; values in the list can be added and removed.</span></span>

<span data-ttu-id="c1125-149">Eine typische Wert Konvertierung für eine Listen Eigenschaft kann die Liste in und aus JSON konvertieren:</span><span class="sxs-lookup"><span data-stu-id="c1125-149">A typical value conversion on a list property might convert the list to and from JSON:</span></span>

### <a name="ef-core-50"></a>[<span data-ttu-id="c1125-150">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="c1125-150">EF Core 5.0</span></span>](#tab/ef5)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty&highlight=7-10)]

### <a name="older-versions"></a>[<span data-ttu-id="c1125-151">Ältere Versionen</span><span class="sxs-lookup"><span data-stu-id="c1125-151">Older versions</span></span>](#tab/older-versions)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListPropertyOld.cs?name=ConfigureListProperty&highlight=8-11,17)]

***

<span data-ttu-id="c1125-152">Der <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> Konstruktor akzeptiert drei Ausdrücke:</span><span class="sxs-lookup"><span data-stu-id="c1125-152">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> constructor accepts three expressions:</span></span>

* <span data-ttu-id="c1125-153">Ein Ausdruck zum Überprüfen der Gleichheit.</span><span class="sxs-lookup"><span data-stu-id="c1125-153">An expression for checking equality</span></span>
* <span data-ttu-id="c1125-154">Ein Ausdruck zum Erzeugen eines Hashcodes.</span><span class="sxs-lookup"><span data-stu-id="c1125-154">An expression for generating a hash code</span></span>
* <span data-ttu-id="c1125-155">Ein Ausdruck zum Erstellen einer Momentaufnahme eines Werts.</span><span class="sxs-lookup"><span data-stu-id="c1125-155">An expression to snapshot a value</span></span>

<span data-ttu-id="c1125-156">In diesem Fall wird der Vergleich durchgeführt, indem überprüft wird, ob die Zahlen Sequenzen identisch sind.</span><span class="sxs-lookup"><span data-stu-id="c1125-156">In this case the comparison is done by checking if the sequences of numbers are the same.</span></span>

<span data-ttu-id="c1125-157">Ebenso wird der Hashcode aus derselben Reihenfolge erstellt.</span><span class="sxs-lookup"><span data-stu-id="c1125-157">Likewise, the hash code is built from this same sequence.</span></span>
<span data-ttu-id="c1125-158">(Beachten Sie, dass dies ein Hashcode für änderbare Werte ist und daher [Probleme verursachen](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/)kann.</span><span class="sxs-lookup"><span data-stu-id="c1125-158">(Note that this is a hash code over mutable values and hence can [cause problems](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span></span>
<span data-ttu-id="c1125-159">Sie sollten stattdessen unveränderlich sein, wenn dies möglich ist.)</span><span class="sxs-lookup"><span data-stu-id="c1125-159">Be immutable instead if you can.)</span></span>

<span data-ttu-id="c1125-160">Die Momentaufnahme wird erstellt, indem Sie die Liste mit Klonen `ToList` .</span><span class="sxs-lookup"><span data-stu-id="c1125-160">The snapshot is created by cloning the list with `ToList`.</span></span>
<span data-ttu-id="c1125-161">Dies ist auch dann nur erforderlich, wenn die Listen mutiert werden.</span><span class="sxs-lookup"><span data-stu-id="c1125-161">Again, this is only needed if the lists are going to be mutated.</span></span>
<span data-ttu-id="c1125-162">Sie sollten stattdessen unveränderlich sein, wenn dies möglich ist.</span><span class="sxs-lookup"><span data-stu-id="c1125-162">Be immutable instead if you can.</span></span>

> [!NOTE]
> <span data-ttu-id="c1125-163">Wert Konverter und Comparer werden mithilfe von Ausdrücken anstelle einfacher Delegaten erstellt.</span><span class="sxs-lookup"><span data-stu-id="c1125-163">Value converters and comparers are constructed using expressions rather than simple delegates.</span></span>
> <span data-ttu-id="c1125-164">Dies liegt daran, dass EF Core diese Ausdrücke in eine wesentlich komplexere Ausdrucks Baumstruktur einfügt, die dann in einen Entitäts-Shaper-Delegaten kompiliert wird.</span><span class="sxs-lookup"><span data-stu-id="c1125-164">This is because EF Core inserts these expressions into a much more complex expression tree that is then compiled into an entity shaper delegate.</span></span>
> <span data-ttu-id="c1125-165">Konzeptionell ähnelt dies dem Compiler-inlining.</span><span class="sxs-lookup"><span data-stu-id="c1125-165">Conceptually, this is similar to compiler inlining.</span></span>
> <span data-ttu-id="c1125-166">Eine einfache Konvertierung kann z. b. nur eine kompilierte Umwandlung sein, anstelle eines Aufrufes einer anderen Methode, um die Konvertierung durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="c1125-166">For example, a simple conversion may just be a compiled in cast, rather than a call to another method to do the conversion.</span></span>

## <a name="key-comparers"></a><span data-ttu-id="c1125-167">Schlüssel Vergleiche</span><span class="sxs-lookup"><span data-stu-id="c1125-167">Key comparers</span></span>

<span data-ttu-id="c1125-168">Der Hintergrund Abschnitt erläutert, warum Schlüssel Vergleiche möglicherweise eine besondere Semantik erfordern.</span><span class="sxs-lookup"><span data-stu-id="c1125-168">The background section covers why key comparisons may require special semantics.</span></span>
<span data-ttu-id="c1125-169">Stellen Sie sicher, dass Sie einen Vergleich erstellen, der für Schlüssel geeignet ist, wenn Sie ihn auf eine primär-, Prinzipal-oder Fremdschlüssel Eigenschaft festlegen.</span><span class="sxs-lookup"><span data-stu-id="c1125-169">Make sure to create a comparer that is appropriate for keys when setting it on a primary, principal, or foreign key property.</span></span>

<span data-ttu-id="c1125-170">Verwenden Sie <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> in den seltenen Fällen, in denen unterschiedliche Semantik für die gleiche Eigenschaft erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="c1125-170">Use <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> in the rare cases where different semantics is required on the same property.</span></span>

> [!NOTE]
> <span data-ttu-id="c1125-171"><xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetStructuralValueComparer%2A> ist in EF Core 5,0 veraltet.</span><span class="sxs-lookup"><span data-stu-id="c1125-171"><xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetStructuralValueComparer%2A> has been obsoleted in EF Core 5.0.</span></span> <span data-ttu-id="c1125-172">Verwenden Sie stattdessen <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A>.</span><span class="sxs-lookup"><span data-stu-id="c1125-172">Use <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> instead.</span></span>

## <a name="overriding-the-default-comparer"></a><span data-ttu-id="c1125-173">Überschreiben des Standardcomparers</span><span class="sxs-lookup"><span data-stu-id="c1125-173">Overriding the default comparer</span></span>

<span data-ttu-id="c1125-174">Manchmal ist der von EF Core verwendete Standard Vergleich möglicherweise nicht geeignet.</span><span class="sxs-lookup"><span data-stu-id="c1125-174">Sometimes the default comparison used by EF Core may not be appropriate.</span></span>
<span data-ttu-id="c1125-175">Beispielsweise wird die Mutation von Byte Arrays nicht standardmäßig in EF Core erkannt.</span><span class="sxs-lookup"><span data-stu-id="c1125-175">For example, mutation of byte arrays is not, by default, detected in EF Core.</span></span>
<span data-ttu-id="c1125-176">Dies kann überschrieben werden, indem für die-Eigenschaft ein anderer Vergleich festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="c1125-176">This can be overridden by setting a different comparer on the property:</span></span>

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

<span data-ttu-id="c1125-177">EF Core vergleicht nun Byte Sequenzen und erkennt daher Bytearray-Mutationen.</span><span class="sxs-lookup"><span data-stu-id="c1125-177">EF Core will now compare byte sequences and will therefore detect byte array mutations.</span></span>
