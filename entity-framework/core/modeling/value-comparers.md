---
title: Wert Vergleiche-EF Core
description: Verwenden von Wert vergleichen zum Steuern der Art EF Core Vergleichen von Eigenschafts Werten
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: fa5352129977d858d54d4aede746b320c91b0ad3
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526783"
---
# <a name="value-comparers"></a><span data-ttu-id="e5291-103">Wert Vergleiche</span><span class="sxs-lookup"><span data-stu-id="e5291-103">Value Comparers</span></span>

> [!NOTE]  
> <span data-ttu-id="e5291-104">Diese Funktion ist neu in EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="e5291-104">This feature is new in EF Core 3.0.</span></span>

> [!TIP]  
> <span data-ttu-id="e5291-105">Den Code in diesem Dokument finden Sie auf GitHub als Ausführ [bares Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span><span class="sxs-lookup"><span data-stu-id="e5291-105">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="background"></a><span data-ttu-id="e5291-106">Hintergrund</span><span class="sxs-lookup"><span data-stu-id="e5291-106">Background</span></span>

<span data-ttu-id="e5291-107">EF Core müssen die Eigenschaftswerte in folgenden vergleichen:</span><span class="sxs-lookup"><span data-stu-id="e5291-107">EF Core needs to compare property values when:</span></span>

* <span data-ttu-id="e5291-108">Ermitteln, ob eine Eigenschaft im Rahmen der Erkennung von [Änderungen für Updates](xref:core/saving/basic) geändert wurde</span><span class="sxs-lookup"><span data-stu-id="e5291-108">Determining whether a property has been changed as part of [detecting changes for updates](xref:core/saving/basic)</span></span>
* <span data-ttu-id="e5291-109">Bestimmen, ob zwei Schlüsselwerte beim Auflösen von Beziehungen identisch sind</span><span class="sxs-lookup"><span data-stu-id="e5291-109">Determining whether two key values are the same when resolving relationships</span></span> 

<span data-ttu-id="e5291-110">Dies wird automatisch für allgemeine primitive Typen wie int, bool, DateTime usw. behandelt.</span><span class="sxs-lookup"><span data-stu-id="e5291-110">This is handled automatically for common primitive types such as int, bool, DateTime, etc.</span></span>

<span data-ttu-id="e5291-111">Für komplexere Typen müssen Entscheidungen getroffen werden, um den Vergleich durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="e5291-111">For more complex types, choices need to be made as to how to do the comparison.</span></span>
<span data-ttu-id="e5291-112">Ein Bytearray könnte beispielsweise verglichen werden:</span><span class="sxs-lookup"><span data-stu-id="e5291-112">For example, a byte array could be compared:</span></span>

* <span data-ttu-id="e5291-113">Als Verweis, sodass ein Unterschied nur erkannt wird, wenn ein neues Bytearray verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e5291-113">By reference, such that a difference is only detected if a new byte array is used</span></span>
* <span data-ttu-id="e5291-114">Durch einen tiefgreifenden Vergleich, sodass die Mutation der Bytes im Array erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="e5291-114">By deep comparison, such that mutation of the bytes in the array is detected</span></span>

<span data-ttu-id="e5291-115">Standardmäßig verwendet EF Core den ersten dieser Ansätze für nicht-Schlüssel Byte Arrays.</span><span class="sxs-lookup"><span data-stu-id="e5291-115">By default, EF Core uses the first of these approaches for non-key byte arrays.</span></span>
<span data-ttu-id="e5291-116">Das heißt, nur Verweise werden verglichen, und eine Änderung wird nur erkannt, wenn ein vorhandenes Bytearray durch einen neuen ersetzt wird.</span><span class="sxs-lookup"><span data-stu-id="e5291-116">That is, only references are compared and a change is detected only when an existing byte array is replaced with a new one.</span></span>
<span data-ttu-id="e5291-117">Dies ist eine pragmatische Entscheidung, die beim Ausführen von SaveChanges einen tiefgreifenden Vergleich vieler großer Byte Arrays vermeidet.</span><span class="sxs-lookup"><span data-stu-id="e5291-117">This is a pragmatic decision that avoids deep comparison of many large byte arrays when executing SaveChanges.</span></span>
<span data-ttu-id="e5291-118">Allerdings wird das gängige Szenario zum Austauschen eines Bilds mit einem anderen Bild auf leistungsstarke Weise behandelt.</span><span class="sxs-lookup"><span data-stu-id="e5291-118">But the common scenario of replacing, say, an image with a different image is handled in a performant way.</span></span>

<span data-ttu-id="e5291-119">Andererseits funktioniert die Verweis Gleichheit nicht, wenn Byte Arrays zur Darstellung von Binär Schlüsseln verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e5291-119">On the other hand, reference equality would not work when byte arrays are used to represent binary keys.</span></span>
<span data-ttu-id="e5291-120">Es ist sehr unwahrscheinlich, dass eine FK-Eigenschaft auf die _gleiche Instanz_ wie eine PK-Eigenschaft festgelegt ist, zu der Sie verglichen werden muss.</span><span class="sxs-lookup"><span data-stu-id="e5291-120">It's very unlikely that an FK property is set to the _same instance_ as a PK property to which it needs to be compared.</span></span>
<span data-ttu-id="e5291-121">Daher verwendet EF Core Tiefe Vergleiche für Byte Arrays, die als Schlüssel fungieren.</span><span class="sxs-lookup"><span data-stu-id="e5291-121">Therefore, EF Core uses deep comparisons for byte arrays acting as keys.</span></span>
<span data-ttu-id="e5291-122">Es ist unwahrscheinlich, dass eine große Leistung erzielt wird, da binäre Schlüssel in der Regel kurz sind.</span><span class="sxs-lookup"><span data-stu-id="e5291-122">This is unlikely to have a big performance hit since binary keys are usually short.</span></span>

### <a name="snapshots"></a><span data-ttu-id="e5291-123">Momentaufnahmen</span><span class="sxs-lookup"><span data-stu-id="e5291-123">Snapshots</span></span>

<span data-ttu-id="e5291-124">Tiefe Vergleiche bei änderbaren Typen bedeutet, dass EF Core die Möglichkeit benötigt, eine Tiefe "Momentaufnahme" des Eigenschafts Werts zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="e5291-124">Deep comparisons on mutable types means that EF Core needs the ability to create a deep "snapshot" of the property value.</span></span>
<span data-ttu-id="e5291-125">Wenn Sie stattdessen nur den Verweis kopieren, würde dies dazu führen, dass der aktuelle Wert und die Momentaufnahme geändert werden, da es sich um _dasselbe Objekt_handelt.</span><span class="sxs-lookup"><span data-stu-id="e5291-125">Just copying the reference instead would result in mutating both the current value and the snapshot, since they are _the same object_.</span></span>
<span data-ttu-id="e5291-126">Wenn Deep-Vergleiche für änderbare Typen verwendet werden, ist daher auch Deep snapshotts erforderlich.</span><span class="sxs-lookup"><span data-stu-id="e5291-126">Therefore, when deep comparisons are used on mutable types, deep snapshotting is also required.</span></span>

## <a name="properties-with-value-converters"></a><span data-ttu-id="e5291-127">Eigenschaften mit Wert Konvertern</span><span class="sxs-lookup"><span data-stu-id="e5291-127">Properties with value converters</span></span>

<span data-ttu-id="e5291-128">Im obigen Fall verfügt EF Core über Native zuordnungsunterstützung für Byte Arrays und kann daher automatisch geeignete Standardwerte auswählen.</span><span class="sxs-lookup"><span data-stu-id="e5291-128">In the case above, EF Core has native mapping support for byte arrays and so can automatically choose appropriate defaults.</span></span>
<span data-ttu-id="e5291-129">Wenn die Eigenschaft jedoch über einen [Wert Konverter](xref:core/modeling/value-conversions)zugeordnet wird, kann EF Core nicht immer den passenden Vergleich ermitteln, der verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="e5291-129">However, if the property is mapped through a [value converter](xref:core/modeling/value-conversions), then EF Core can't always determine the appropriate comparison to use.</span></span>
<span data-ttu-id="e5291-130">Stattdessen verwendet EF Core immer den Standard Gleichheits Vergleich, der durch den Typ der Eigenschaft definiert wird.</span><span class="sxs-lookup"><span data-stu-id="e5291-130">Instead, EF Core always uses the default equality comparison defined by the type of the property.</span></span>
<span data-ttu-id="e5291-131">Dies ist oft richtig, kann jedoch beim Mapping komplexer Typen überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="e5291-131">This is often correct, but may need to be overridden when mapping more complex types.</span></span>

### <a name="simple-immutable-classes"></a><span data-ttu-id="e5291-132">Einfache unveränderliche Klassen</span><span class="sxs-lookup"><span data-stu-id="e5291-132">Simple immutable classes</span></span>

<span data-ttu-id="e5291-133">Stellen Sie sich eine Eigenschaft vor, die einen Wert Konverter verwendet, um eine einfache, unveränderliche Klasse zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="e5291-133">Consider a property that uses a value converter to map a simple, immutable class.</span></span>

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

<span data-ttu-id="e5291-134">Eigenschaften dieses Typs benötigen keine besonderen Vergleiche oder Momentaufnahmen aus folgenden Gründen:</span><span class="sxs-lookup"><span data-stu-id="e5291-134">Properties of this type do not need special comparisons or snapshots because:</span></span>
* <span data-ttu-id="e5291-135">Gleichheit wird überschrieben, sodass verschiedene Instanzen ordnungsgemäß verglichen werden.</span><span class="sxs-lookup"><span data-stu-id="e5291-135">Equality is overridden so that different instances will compare correctly</span></span>
* <span data-ttu-id="e5291-136">Der Typ ist unveränderlich, sodass es nicht möglich ist, einen Momentaufnahme Wert zu mutieren.</span><span class="sxs-lookup"><span data-stu-id="e5291-136">The type is immutable, so there is no chance of mutating a snapshot value</span></span>

<span data-ttu-id="e5291-137">In diesem Fall ist das Standardverhalten von EF Core in Ordnung.</span><span class="sxs-lookup"><span data-stu-id="e5291-137">So in this case the default behavior of EF Core is fine as it is.</span></span>

### <a name="simple-immutable-structs"></a><span data-ttu-id="e5291-138">Einfache unveränderliche Strukturen</span><span class="sxs-lookup"><span data-stu-id="e5291-138">Simple immutable Structs</span></span>

<span data-ttu-id="e5291-139">Die Zuordnung für einfache Strukturen ist auch einfach und erfordert keine speziellen Vergleiche oder snapshote.</span><span class="sxs-lookup"><span data-stu-id="e5291-139">The mapping for simple structs is also simple and requires no special comparers or snapshotting.</span></span>

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

<span data-ttu-id="e5291-140">EF Core verfügt über integrierte Unterstützung für das Erstellen von kompilierten, Mitgliedschafts bezogenen Vergleichen von Struktur Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="e5291-140">EF Core has built-in support for generating compiled, memberwise comparisons of struct properties.</span></span>
<span data-ttu-id="e5291-141">Dies bedeutet, dass Strukturen nicht für EF überschrieben werden müssen, aber Sie können dies auch aus [anderen Gründen](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type)auswählen.</span><span class="sxs-lookup"><span data-stu-id="e5291-141">This means structs don't need to have equality overridden for EF, but you may still choose to do this for [other reasons](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type).</span></span>
<span data-ttu-id="e5291-142">Außerdem ist eine besondere snapshote nicht erforderlich, da Strukturen unveränderlich sind und immer auf die gleiche Weise kopiert werden.</span><span class="sxs-lookup"><span data-stu-id="e5291-142">Also, special snapshotting is not needed since structs immutable and are always memberwise copied anyway.</span></span>
<span data-ttu-id="e5291-143">(Dies gilt auch für änderbare Strukturen, jedoch [sollten änderbare Strukturen im allgemeinen vermieden werden](/dotnet/csharp/write-safe-efficient-code).)</span><span class="sxs-lookup"><span data-stu-id="e5291-143">(This is also true for mutable structs, but [mutable structs should in general be avoided](/dotnet/csharp/write-safe-efficient-code).)</span></span>

### <a name="mutable-classes"></a><span data-ttu-id="e5291-144">Änderbare Klassen</span><span class="sxs-lookup"><span data-stu-id="e5291-144">Mutable classes</span></span>

<span data-ttu-id="e5291-145">Es wird empfohlen, nach Möglichkeit unveränderliche Typen (Klassen oder Strukturen) mit Wert Konvertern zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="e5291-145">It is recommended that you use immutable types (classes or structs) with value converters when possible.</span></span>
<span data-ttu-id="e5291-146">Dies ist in der Regel effizienter und verfügt über eine sauberere Semantik als die Verwendung eines änderbaren Typs.</span><span class="sxs-lookup"><span data-stu-id="e5291-146">This is usually more efficient and has cleaner semantics than using a mutable type.</span></span>

<span data-ttu-id="e5291-147">Allerdings ist es üblich, die Eigenschaften von Typen zu verwenden, die von der Anwendung nicht geändert werden können.</span><span class="sxs-lookup"><span data-stu-id="e5291-147">However, that being said, it is common to use properties of types that the application cannot change.</span></span>
<span data-ttu-id="e5291-148">Beispiel: Zuordnung einer Eigenschaft, die eine Liste mit Zahlen enthält:</span><span class="sxs-lookup"><span data-stu-id="e5291-148">For example, mapping a property containing a list of numbers:</span></span> 

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

<span data-ttu-id="e5291-149">Die- [ `List<T>` Klasse](/dotnet/api/system.collections.generic.list-1?view=netstandard-2.1):</span><span class="sxs-lookup"><span data-stu-id="e5291-149">The [`List<T>` class](/dotnet/api/system.collections.generic.list-1?view=netstandard-2.1):</span></span>
* <span data-ttu-id="e5291-150">Hat Verweis Gleichheit. zwei Listen, die dieselben Werte enthalten, werden als verschieden behandelt.</span><span class="sxs-lookup"><span data-stu-id="e5291-150">Has reference equality; two lists containing the same values are treated as different.</span></span>
* <span data-ttu-id="e5291-151">Ist änderbar; Werte in der Liste können hinzugefügt und entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="e5291-151">Is mutable; values in the list can be added and removed.</span></span>

<span data-ttu-id="e5291-152">Eine typische Wert Konvertierung für eine Listen Eigenschaft kann die Liste in und aus JSON konvertieren:</span><span class="sxs-lookup"><span data-stu-id="e5291-152">A typical value conversion on a list property might convert the list to and from JSON:</span></span>

[!code-csharp[ConfigureListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

<span data-ttu-id="e5291-153">Dies erfordert dann das Festlegen einer `ValueComparer<T>` für die-Eigenschaft, um das Erzwingen EF Core verwenden korrekter Vergleiche mit dieser Konvertierung zu erzwingen:</span><span class="sxs-lookup"><span data-stu-id="e5291-153">This then requires setting a `ValueComparer<T>` on the property to force EF Core use correct comparisons with this conversion:</span></span>

[!code-csharp[ConfigureListPropertyComparer](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListPropertyComparer)]

> [!NOTE]  
> <span data-ttu-id="e5291-154">Die Modell-Generator-API ("fließend"), mit der ein Wert Vergleich festgelegt wurde, wurde noch nicht implementiert.</span><span class="sxs-lookup"><span data-stu-id="e5291-154">The model builder ("fluent") API to set a value comparer has not yet been implemented.</span></span>
> <span data-ttu-id="e5291-155">Stattdessen ruft der obige Code setvaluecomparer auf der untergeordneten imutableproperty auf, die vom Generator als "Metadata" verfügbar gemacht wird.</span><span class="sxs-lookup"><span data-stu-id="e5291-155">Instead, the code above calls SetValueComparer on the lower-level IMutableProperty exposed by the builder as 'Metadata'.</span></span>

<span data-ttu-id="e5291-156">Der `ValueComparer<T>` Konstruktor akzeptiert drei Ausdrücke:</span><span class="sxs-lookup"><span data-stu-id="e5291-156">The `ValueComparer<T>` constructor accepts three expressions:</span></span>
* <span data-ttu-id="e5291-157">Ein Ausdruck zum Überprüfen der Gleichheit.</span><span class="sxs-lookup"><span data-stu-id="e5291-157">An expression for checking equality</span></span>
* <span data-ttu-id="e5291-158">Ein Ausdruck zum Erzeugen eines Hashcodes.</span><span class="sxs-lookup"><span data-stu-id="e5291-158">An expression for generating a hash code</span></span>
* <span data-ttu-id="e5291-159">Ein Ausdruck zum Erstellen einer Momentaufnahme eines Werts.</span><span class="sxs-lookup"><span data-stu-id="e5291-159">An expression to snapshot a value</span></span>  

<span data-ttu-id="e5291-160">In diesem Fall wird der Vergleich durchgeführt, indem überprüft wird, ob die Zahlen Sequenzen identisch sind.</span><span class="sxs-lookup"><span data-stu-id="e5291-160">In this case the comparison is done by checking if the sequences of numbers are the same.</span></span>

<span data-ttu-id="e5291-161">Ebenso wird der Hashcode aus derselben Reihenfolge erstellt.</span><span class="sxs-lookup"><span data-stu-id="e5291-161">Likewise, the hash code is built from this same sequence.</span></span>
<span data-ttu-id="e5291-162">(Beachten Sie, dass dies ein Hashcode für änderbare Werte ist und daher [Probleme verursachen](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/)kann.</span><span class="sxs-lookup"><span data-stu-id="e5291-162">(Note that this is a hash code over mutable values and hence can [cause problems](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/).</span></span>
<span data-ttu-id="e5291-163">Sie sollten stattdessen unveränderlich sein, wenn dies möglich ist.)</span><span class="sxs-lookup"><span data-stu-id="e5291-163">Be immutable instead if you can.)</span></span>

<span data-ttu-id="e5291-164">Die Momentaufnahme wird erstellt, indem Sie die Liste mit dem Listen paar Klonen.</span><span class="sxs-lookup"><span data-stu-id="e5291-164">The snapshot is created by cloning the list with ToList.</span></span>
<span data-ttu-id="e5291-165">Dies ist auch dann nur erforderlich, wenn die Listen mutiert werden.</span><span class="sxs-lookup"><span data-stu-id="e5291-165">Again, this is only needed if the lists are going to be mutated.</span></span>
<span data-ttu-id="e5291-166">Sie sollten stattdessen unveränderlich sein, wenn dies möglich ist.</span><span class="sxs-lookup"><span data-stu-id="e5291-166">Be immutable instead if you can.</span></span> 

> [!NOTE]  
> <span data-ttu-id="e5291-167">Wert Konverter und Comparer werden mithilfe von Ausdrücken anstelle einfacher Delegaten erstellt.</span><span class="sxs-lookup"><span data-stu-id="e5291-167">Value converters and comparers are constructed using expressions rather than simple delegates.</span></span>
> <span data-ttu-id="e5291-168">Dies liegt daran, dass EF diese Ausdrücke in eine wesentlich komplexere Ausdrucks Baumstruktur einfügt, die dann in einen Entitäts-Shaper-Delegaten kompiliert wird.</span><span class="sxs-lookup"><span data-stu-id="e5291-168">This is because EF inserts these expressions into a much more complex expression tree that is then compiled into an entity shaper delegate.</span></span>
> <span data-ttu-id="e5291-169">Konzeptionell ähnelt dies dem Compiler-inlining.</span><span class="sxs-lookup"><span data-stu-id="e5291-169">Conceptually, this is similar to compiler inlining.</span></span>
> <span data-ttu-id="e5291-170">Eine einfache Konvertierung kann z. b. nur eine kompilierte Umwandlung sein, anstelle eines Aufrufes einer anderen Methode, um die Konvertierung durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="e5291-170">For example, a simple conversion may just be a compiled in cast, rather than a call to another method to do the conversion.</span></span>    

### <a name="key-comparers"></a><span data-ttu-id="e5291-171">Schlüssel Vergleiche</span><span class="sxs-lookup"><span data-stu-id="e5291-171">Key comparers</span></span>

<span data-ttu-id="e5291-172">Der Hintergrund Abschnitt erläutert, warum Schlüssel Vergleiche möglicherweise eine besondere Semantik erfordern.</span><span class="sxs-lookup"><span data-stu-id="e5291-172">The background section covers why key comparisons may require special semantics.</span></span>
<span data-ttu-id="e5291-173">Stellen Sie sicher, dass Sie einen Vergleich erstellen, der für Schlüssel geeignet ist, wenn Sie ihn auf eine primär-, Prinzipal-oder Fremdschlüssel Eigenschaft festlegen.</span><span class="sxs-lookup"><span data-stu-id="e5291-173">Make sure to create a comparer that is appropriate for keys when setting it on a primary, principal, or foreign key property.</span></span>

<span data-ttu-id="e5291-174">Verwenden Sie [setkeyvaluecomparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer?view=efcore-3.1) in seltenen Fällen, in denen unterschiedliche Semantik für die gleiche Eigenschaft erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="e5291-174">Use [SetKeyValueComparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer?view=efcore-3.1) in the rare cases where different semantics is required on the same property.</span></span>

> [!NOTE]  
> <span data-ttu-id="e5291-175">Setstructuralcomparer ist in EF Core 5,0 veraltet.</span><span class="sxs-lookup"><span data-stu-id="e5291-175">SetStructuralComparer has been obsoleted in EF Core 5.0.</span></span>
> <span data-ttu-id="e5291-176">Verwenden Sie stattdessen setkeyvaluecomparer.</span><span class="sxs-lookup"><span data-stu-id="e5291-176">Use SetKeyValueComparer instead.</span></span>

### <a name="overriding-defaults"></a><span data-ttu-id="e5291-177">Überschreiben von Standardeinstellungen</span><span class="sxs-lookup"><span data-stu-id="e5291-177">Overriding defaults</span></span>

<span data-ttu-id="e5291-178">Manchmal ist der von EF Core verwendete Standard Vergleich möglicherweise nicht geeignet.</span><span class="sxs-lookup"><span data-stu-id="e5291-178">Sometimes the default comparison used by EF Core may not be appropriate.</span></span>
<span data-ttu-id="e5291-179">Beispielsweise wird die Mutation von Byte Arrays nicht standardmäßig in EF Core erkannt.</span><span class="sxs-lookup"><span data-stu-id="e5291-179">For example, mutation of byte arrays is not, by default, detected in EF Core.</span></span>
<span data-ttu-id="e5291-180">Dies kann überschrieben werden, indem für die-Eigenschaft ein anderer Vergleich festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="e5291-180">This can be overridden by setting a different comparer on the property:</span></span> 

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

<span data-ttu-id="e5291-181">EF Core vergleicht nun Byte Sequenzen und erkennt daher Bytearray-Mutationen.</span><span class="sxs-lookup"><span data-stu-id="e5291-181">EF Core will now compare byte sequences and will therefore detect byte array mutations.</span></span>
