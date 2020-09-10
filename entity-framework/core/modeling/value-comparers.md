---
title: Wert Vergleiche-EF Core
description: Verwenden von Wert vergleichen zum Steuern der Art EF Core Vergleichen von Eigenschafts Werten
author: ajcvickers
ms.date: 03/20/2020
uid: core/modeling/value-comparers
ms.openlocfilehash: d07aee866a542f55c4e1074c5782e67cb4035a89
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616680"
---
# <a name="value-comparers"></a>Wert Vergleiche

> [!NOTE]  
> Diese Funktion ist neu in EF Core 3,0.

> [!TIP]  
> Den Code in diesem Dokument finden Sie auf GitHub als Ausführ [bares Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).

## <a name="background"></a>Hintergrund

EF Core müssen die Eigenschaftswerte in folgenden vergleichen:

* Ermitteln, ob eine Eigenschaft im Rahmen der Erkennung von [Änderungen für Updates](xref:core/saving/basic) geändert wurde
* Bestimmen, ob zwei Schlüsselwerte beim Auflösen von Beziehungen identisch sind

Dies wird automatisch für allgemeine primitive Typen wie int, bool, DateTime usw. behandelt.

Für komplexere Typen müssen Entscheidungen getroffen werden, um den Vergleich durchzuführen.
Ein Bytearray könnte beispielsweise verglichen werden:

* Als Verweis, sodass ein Unterschied nur erkannt wird, wenn ein neues Bytearray verwendet wird.
* Durch einen tiefgreifenden Vergleich, sodass die Mutation der Bytes im Array erkannt wird.

Standardmäßig verwendet EF Core den ersten dieser Ansätze für nicht-Schlüssel Byte Arrays.
Das heißt, nur Verweise werden verglichen, und eine Änderung wird nur erkannt, wenn ein vorhandenes Bytearray durch einen neuen ersetzt wird.
Dies ist eine pragmatische Entscheidung, die beim Ausführen von SaveChanges einen tiefgreifenden Vergleich vieler großer Byte Arrays vermeidet.
Allerdings wird das gängige Szenario zum Austauschen eines Bilds mit einem anderen Bild auf leistungsstarke Weise behandelt.

Andererseits funktioniert die Verweis Gleichheit nicht, wenn Byte Arrays zur Darstellung von Binär Schlüsseln verwendet werden.
Es ist sehr unwahrscheinlich, dass eine FK-Eigenschaft auf die _gleiche Instanz_ wie eine PK-Eigenschaft festgelegt ist, zu der Sie verglichen werden muss.
Daher verwendet EF Core Tiefe Vergleiche für Byte Arrays, die als Schlüssel fungieren.
Es ist unwahrscheinlich, dass eine große Leistung erzielt wird, da binäre Schlüssel in der Regel kurz sind.

### <a name="snapshots"></a>Momentaufnahmen

Tiefe Vergleiche bei änderbaren Typen bedeutet, dass EF Core die Möglichkeit benötigt, eine Tiefe "Momentaufnahme" des Eigenschafts Werts zu erstellen.
Wenn Sie stattdessen nur den Verweis kopieren, würde dies dazu führen, dass der aktuelle Wert und die Momentaufnahme geändert werden, da es sich um _dasselbe Objekt_handelt.
Wenn Deep-Vergleiche für änderbare Typen verwendet werden, ist daher auch Deep snapshotts erforderlich.

## <a name="properties-with-value-converters"></a>Eigenschaften mit Wert Konvertern

Im obigen Fall verfügt EF Core über Native zuordnungsunterstützung für Byte Arrays und kann daher automatisch geeignete Standardwerte auswählen.
Wenn die Eigenschaft jedoch über einen [Wert Konverter](xref:core/modeling/value-conversions)zugeordnet wird, kann EF Core nicht immer den passenden Vergleich ermitteln, der verwendet werden soll.
Stattdessen verwendet EF Core immer den Standard Gleichheits Vergleich, der durch den Typ der Eigenschaft definiert wird.
Dies ist oft richtig, kann jedoch beim Mapping komplexer Typen überschrieben werden.

### <a name="simple-immutable-classes"></a>Einfache unveränderliche Klassen

Stellen Sie sich eine Eigenschaft vor, die einen Wert Konverter verwendet, um eine einfache, unveränderliche Klasse zuzuordnen.

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

Eigenschaften dieses Typs benötigen keine besonderen Vergleiche oder Momentaufnahmen aus folgenden Gründen:

* Gleichheit wird überschrieben, sodass verschiedene Instanzen ordnungsgemäß verglichen werden.
* Der Typ ist unveränderlich, sodass es nicht möglich ist, einen Momentaufnahme Wert zu mutieren.

In diesem Fall ist das Standardverhalten von EF Core in Ordnung.

### <a name="simple-immutable-structs"></a>Einfache unveränderliche Strukturen

Die Zuordnung für einfache Strukturen ist auch einfach und erfordert keine speziellen Vergleiche oder snapshote.

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

EF Core verfügt über integrierte Unterstützung für das Erstellen von kompilierten, Mitgliedschafts bezogenen Vergleichen von Struktur Eigenschaften.
Dies bedeutet, dass Strukturen nicht für EF überschrieben werden müssen, aber Sie können dies auch aus [anderen Gründen](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type)auswählen.
Außerdem ist eine besondere snapshote nicht erforderlich, da Strukturen unveränderlich sind und immer auf die gleiche Weise kopiert werden.
(Dies gilt auch für änderbare Strukturen, jedoch [sollten änderbare Strukturen im allgemeinen vermieden werden](/dotnet/csharp/write-safe-efficient-code).)

### <a name="mutable-classes"></a>Änderbare Klassen

Es wird empfohlen, nach Möglichkeit unveränderliche Typen (Klassen oder Strukturen) mit Wert Konvertern zu verwenden.
Dies ist in der Regel effizienter und verfügt über eine sauberere Semantik als die Verwendung eines änderbaren Typs.

Allerdings ist es üblich, die Eigenschaften von Typen zu verwenden, die von der Anwendung nicht geändert werden können.
Beispiel: Zuordnung einer Eigenschaft, die eine Liste mit Zahlen enthält:

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

Die- [ `List<T>` Klasse](/dotnet/api/system.collections.generic.list-1):

* Hat Verweis Gleichheit. zwei Listen, die dieselben Werte enthalten, werden als verschieden behandelt.
* Ist änderbar; Werte in der Liste können hinzugefügt und entfernt werden.

Eine typische Wert Konvertierung für eine Listen Eigenschaft kann die Liste in und aus JSON konvertieren:

[!code-csharp[ConfigureListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

Dies erfordert dann das Festlegen einer `ValueComparer<T>` für die-Eigenschaft, um das Erzwingen EF Core verwenden korrekter Vergleiche mit dieser Konvertierung zu erzwingen:

[!code-csharp[ConfigureListPropertyComparer](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListPropertyComparer)]

> [!NOTE]  
> Die Modell-Generator-API ("fließend"), mit der ein Wert Vergleich festgelegt wurde, wurde noch nicht implementiert.
> Stattdessen ruft der obige Code setvaluecomparer auf der untergeordneten imutableproperty auf, die vom Generator als "Metadata" verfügbar gemacht wird.

Der `ValueComparer<T>` Konstruktor akzeptiert drei Ausdrücke:

* Ein Ausdruck zum Überprüfen der Gleichheit.
* Ein Ausdruck zum Erzeugen eines Hashcodes.
* Ein Ausdruck zum Erstellen einer Momentaufnahme eines Werts.  

In diesem Fall wird der Vergleich durchgeführt, indem überprüft wird, ob die Zahlen Sequenzen identisch sind.

Ebenso wird der Hashcode aus derselben Reihenfolge erstellt.
(Beachten Sie, dass dies ein Hashcode für änderbare Werte ist und daher [Probleme verursachen](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/)kann.
Sie sollten stattdessen unveränderlich sein, wenn dies möglich ist.)

Die Momentaufnahme wird erstellt, indem Sie die Liste mit dem Listen paar Klonen.
Dies ist auch dann nur erforderlich, wenn die Listen mutiert werden.
Sie sollten stattdessen unveränderlich sein, wenn dies möglich ist.

> [!NOTE]  
> Wert Konverter und Comparer werden mithilfe von Ausdrücken anstelle einfacher Delegaten erstellt.
> Dies liegt daran, dass EF diese Ausdrücke in eine wesentlich komplexere Ausdrucks Baumstruktur einfügt, die dann in einen Entitäts-Shaper-Delegaten kompiliert wird.
> Konzeptionell ähnelt dies dem Compiler-inlining.
> Eine einfache Konvertierung kann z. b. nur eine kompilierte Umwandlung sein, anstelle eines Aufrufes einer anderen Methode, um die Konvertierung durchzuführen.

### <a name="key-comparers"></a>Schlüssel Vergleiche

Der Hintergrund Abschnitt erläutert, warum Schlüssel Vergleiche möglicherweise eine besondere Semantik erfordern.
Stellen Sie sicher, dass Sie einen Vergleich erstellen, der für Schlüssel geeignet ist, wenn Sie ihn auf eine primär-, Prinzipal-oder Fremdschlüssel Eigenschaft festlegen.

Verwenden Sie [setkeyvaluecomparer](/dotnet/api/microsoft.entityframeworkcore.mutablepropertyextensions.setkeyvaluecomparer) in seltenen Fällen, in denen unterschiedliche Semantik für die gleiche Eigenschaft erforderlich ist.

> [!NOTE]  
> Setstructuralcomparer ist in EF Core 5,0 veraltet.
> Verwenden Sie stattdessen setkeyvaluecomparer.

### <a name="overriding-defaults"></a>Überschreiben von Standardeinstellungen

Manchmal ist der von EF Core verwendete Standard Vergleich möglicherweise nicht geeignet.
Beispielsweise wird die Mutation von Byte Arrays nicht standardmäßig in EF Core erkannt.
Dies kann überschrieben werden, indem für die-Eigenschaft ein anderer Vergleich festgelegt wird:

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

EF Core vergleicht nun Byte Sequenzen und erkennt daher Bytearray-Mutationen.
