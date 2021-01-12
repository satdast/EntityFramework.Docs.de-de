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
# <a name="value-comparers"></a>Wert Vergleiche

> [!NOTE]
> Dieses Feature wurde in EF Core 3.0 eingeführt.

> [!TIP]
> Den Code in diesem Dokument finden Sie auf GitHub als Ausführ [bares Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).

## <a name="background"></a>Hintergrund

Die Änderungs Nachverfolgung bedeutet, dass EF Core automatisch bestimmt, welche Änderungen von der Anwendung auf einer geladenen Entitäts Instanz durchgeführt wurden, damit diese Änderungen wieder in der Datenbank gespeichert werden können, wenn <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aufgerufen wird. EF Core wird dies normalerweise durch Erstellen einer *Momentaufnahme* der Instanz, wenn Sie aus der Datenbank geladen wird, und *vergleichen* dieser Momentaufnahme mit der-Instanz, die an die Anwendung übergeben wird.

EF Core verfügt über integrierte Logik zum snapshoten und Vergleichen der meisten Standardtypen, die in Datenbanken verwendet werden, sodass sich Benutzer in der Regel nicht um dieses Thema kümmern müssen. Wenn eine Eigenschaft jedoch über einen [Wert Konverter](xref:core/modeling/value-conversions)zugeordnet wird, muss EF Core einen Vergleich mit beliebigen Benutzer Typen durchführen, die möglicherweise komplex sind. Standardmäßig verwendet EF Core den von Typen definierten Standard Gleichheits Vergleich (z. b. die- `Equals` Methode). für snapshotts werden Werttypen kopiert, um die Momentaufnahme zu erstellen, während für Verweis Typen kein Kopieren stattfindet und dieselbe Instanz als Momentaufnahme verwendet wird.

In Fällen, in denen das integrierte Vergleichs Verhalten nicht geeignet ist, können Benutzer einen *Wert Vergleich* bereitstellen, der Logik für snapshotevorgänge, vergleichen und Berechnen eines Hashcodes enthält. Im folgenden Beispiel wird die Wert Konvertierung für die-Eigenschaft so festgelegt, dass Sie `List<int>` in eine JSON-Zeichenfolge in der-Datenbank konvertiert wird. Außerdem wird ein entsprechender Wert Vergleich definiert:

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty)]

Weitere Informationen finden Sie weiter unten unter [änderbare Klassen](#mutable-classes) .

Beachten Sie, dass Wert Vergleiche auch verwendet werden, um zu bestimmen, ob beim Auflösen von Beziehungen zwei Schlüsselwerte identisch sind. Dies wird im folgenden erläutert.

## <a name="shallow-vs-deep-comparison"></a>Flache und tiefer gehende Vergleiche

Für kleine, unveränderliche Werttypen, wie z. b. `int` , EF Core die Standardlogik der Standardlogik funktioniert: der Wert wird unverändert bei snapshotet kopiert und mit dem integrierten Gleichheits Vergleich des Typs verglichen. Wenn Sie einen eigenen Wert Vergleich implementieren, ist es wichtig zu berücksichtigen, ob eine Tiefe oder flache Vergleichs Logik (und snapshote) geeignet ist.

Beachten Sie ggf. Byte Arrays, die beliebig groß sein können. Diese können verglichen werden:

* Als Verweis, sodass ein Unterschied nur erkannt wird, wenn ein neues Bytearray verwendet wird.
* Durch einen tiefgreifenden Vergleich, sodass die Mutation der Bytes im Array erkannt wird.

Standardmäßig verwendet EF Core den ersten dieser Ansätze für nicht-Schlüssel Byte Arrays. Das heißt, nur Verweise werden verglichen, und eine Änderung wird nur erkannt, wenn ein vorhandenes Bytearray durch einen neuen ersetzt wird. Dies ist eine pragmatische Entscheidung, bei der das Kopieren ganzer Arrays und das Vergleichen mit Byte bei der Ausführung vermieden wird <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> , und das gängige Szenario, bei dem ein Bild durch ein anderes ersetzt wird, in einer leistungsfähigen Weise behandelt wird.

Auf der anderen Seite funktioniert die Verweis Gleichheit nicht, wenn Byte Arrays zur Darstellung von Binär Schlüsseln verwendet werden, da es sehr unwahrscheinlich ist, dass eine FK-Eigenschaft auf _dieselbe Instanz_ wie eine PK-Eigenschaft festgelegt ist, mit der Sie verglichen werden muss. Daher verwendet EF Core Tiefe Vergleiche für Byte Arrays, die als Schlüssel fungieren. Es ist unwahrscheinlich, dass eine große Leistung erzielt wird, da binäre Schlüssel in der Regel kurz sind.

Beachten Sie, dass die gewählte Vergleichs-und snapshoflogik einander entsprechen muss: bei Deep Comparison muss Deep Snapshot ordnungsgemäß funktionieren.

## <a name="simple-immutable-classes"></a>Einfache unveränderliche Klassen

Stellen Sie sich eine Eigenschaft vor, die einen Wert Konverter verwendet, um eine einfache, unveränderliche Klasse zuzuordnen.

[!code-csharp[SimpleImmutableClass](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=SimpleImmutableClass)]

[!code-csharp[ConfigureImmutableClassProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableClassProperty.cs?name=ConfigureImmutableClassProperty)]

Eigenschaften dieses Typs benötigen keine besonderen Vergleiche oder Momentaufnahmen aus folgenden Gründen:

* Gleichheit wird überschrieben, sodass verschiedene Instanzen ordnungsgemäß verglichen werden.
* Der Typ ist unveränderlich, sodass es nicht möglich ist, einen Momentaufnahme Wert zu mutieren.

In diesem Fall ist das Standardverhalten von EF Core in Ordnung.

## <a name="simple-immutable-structs"></a>Einfache unveränderliche Strukturen

Die Zuordnung für einfache Strukturen ist auch einfach und erfordert keine speziellen Vergleiche oder snapshote.

[!code-csharp[SimpleImmutableStruct](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=SimpleImmutableStruct)]

[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/MappingImmutableStructProperty.cs?name=ConfigureImmutableStructProperty)]

EF Core verfügt über integrierte Unterstützung für das Erstellen von kompilierten, Mitgliedschafts bezogenen Vergleichen von Struktur Eigenschaften.
Dies bedeutet, dass Strukturen nicht für EF Core überschrieben werden müssen, aber Sie können dies auch aus [anderen Gründen](/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type)auswählen.
Außerdem ist das besondere snapshotieren nicht erforderlich, da Strukturen unveränderlich sind und immer auf die gleiche Weise kopiert werden.
(Dies gilt auch für änderbare Strukturen, jedoch [sollten änderbare Strukturen im allgemeinen vermieden werden](/dotnet/csharp/write-safe-efficient-code).)

## <a name="mutable-classes"></a>Änderbare Klassen

Es wird empfohlen, nach Möglichkeit unveränderliche Typen (Klassen oder Strukturen) mit Wert Konvertern zu verwenden.
Dies ist in der Regel effizienter und verfügt über eine sauberere Semantik als die Verwendung eines änderbaren Typs.

Allerdings ist es üblich, die Eigenschaften von Typen zu verwenden, die von der Anwendung nicht geändert werden können.
Beispiel: Zuordnung einer Eigenschaft, die eine Liste mit Zahlen enthält:

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ListProperty)]

Die <xref:System.Collections.Generic.List%601>-Klasse:

* Hat Verweis Gleichheit. zwei Listen, die dieselben Werte enthalten, werden als verschieden behandelt.
* Ist änderbar; Werte in der Liste können hinzugefügt und entfernt werden.

Eine typische Wert Konvertierung für eine Listen Eigenschaft kann die Liste in und aus JSON konvertieren:

### <a name="ef-core-50"></a>[EF Core 5.0](#tab/ef5)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListProperty.cs?name=ConfigureListProperty&highlight=7-10)]

### <a name="older-versions"></a>[Ältere Versionen](#tab/older-versions)

[!code-csharp[ListProperty](../../../samples/core/Modeling/ValueConversions/MappingListPropertyOld.cs?name=ConfigureListProperty&highlight=8-11,17)]

***

Der <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> Konstruktor akzeptiert drei Ausdrücke:

* Ein Ausdruck zum Überprüfen der Gleichheit.
* Ein Ausdruck zum Erzeugen eines Hashcodes.
* Ein Ausdruck zum Erstellen einer Momentaufnahme eines Werts.

In diesem Fall wird der Vergleich durchgeführt, indem überprüft wird, ob die Zahlen Sequenzen identisch sind.

Ebenso wird der Hashcode aus derselben Reihenfolge erstellt.
(Beachten Sie, dass dies ein Hashcode für änderbare Werte ist und daher [Probleme verursachen](https://ericlippert.com/2011/02/28/guidelines-and-rules-for-gethashcode/)kann.
Sie sollten stattdessen unveränderlich sein, wenn dies möglich ist.)

Die Momentaufnahme wird erstellt, indem Sie die Liste mit Klonen `ToList` .
Dies ist auch dann nur erforderlich, wenn die Listen mutiert werden.
Sie sollten stattdessen unveränderlich sein, wenn dies möglich ist.

> [!NOTE]
> Wert Konverter und Comparer werden mithilfe von Ausdrücken anstelle einfacher Delegaten erstellt.
> Dies liegt daran, dass EF Core diese Ausdrücke in eine wesentlich komplexere Ausdrucks Baumstruktur einfügt, die dann in einen Entitäts-Shaper-Delegaten kompiliert wird.
> Konzeptionell ähnelt dies dem Compiler-inlining.
> Eine einfache Konvertierung kann z. b. nur eine kompilierte Umwandlung sein, anstelle eines Aufrufes einer anderen Methode, um die Konvertierung durchzuführen.

## <a name="key-comparers"></a>Schlüssel Vergleiche

Der Hintergrund Abschnitt erläutert, warum Schlüssel Vergleiche möglicherweise eine besondere Semantik erfordern.
Stellen Sie sicher, dass Sie einen Vergleich erstellen, der für Schlüssel geeignet ist, wenn Sie ihn auf eine primär-, Prinzipal-oder Fremdschlüssel Eigenschaft festlegen.

Verwenden Sie <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A> in den seltenen Fällen, in denen unterschiedliche Semantik für die gleiche Eigenschaft erforderlich ist.

> [!NOTE]
> <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetStructuralValueComparer%2A> ist in EF Core 5,0 veraltet. Verwenden Sie stattdessen <xref:Microsoft.EntityFrameworkCore.MutablePropertyExtensions.SetKeyValueComparer%2A>.

## <a name="overriding-the-default-comparer"></a>Überschreiben des Standardcomparers

Manchmal ist der von EF Core verwendete Standard Vergleich möglicherweise nicht geeignet.
Beispielsweise wird die Mutation von Byte Arrays nicht standardmäßig in EF Core erkannt.
Dies kann überschrieben werden, indem für die-Eigenschaft ein anderer Vergleich festgelegt wird:

[!code-csharp[OverrideComparer](../../../samples/core/Modeling/ValueConversions/OverridingByteArrayComparisons.cs?name=OverrideComparer)]

EF Core vergleicht nun Byte Sequenzen und erkennt daher Bytearray-Mutationen.
