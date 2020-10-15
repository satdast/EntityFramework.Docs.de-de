---
title: Getrennte Entitäten – EF Core
description: Kontextinstanzenübergreifendes Arbeiten mit getrennten, nicht nachverfolgten Entitäten in Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/disconnected-entities
ms.openlocfilehash: f21cc71ef11ee4ef37618a68d5a5219e8174bf8b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063568"
---
# <a name="disconnected-entities"></a>Getrennte Entitäten

Eine DbContext-Instanz verfolgt automatisch Entitäten nach, die von der Datenbank zurückgegeben wurden. An diesen Entitäten vorgenommene Änderungen werden nach dem Aufrufen von SaveChanges erkannt, und die Datenbank wird ggf. aktualisiert. Weitere Einzelheiten finden Sie unter [Grundlegendes zum Speichern](xref:core/saving/basic) und [Zugehörige Daten](xref:core/saving/related-data).

Entitäten werden jedoch manchmal mit einer Kontextinstanz abgefragt und anschließend mit einer anderen Instanz gespeichert. Dies geschieht häufig in „getrennten“ Szenarios, wie z.B. einer Webanwendung, in welcher die Entitäten abgefragt werden, an den Client gesendet werden, geändert werden, in einer Anforderung zurück an den Server gesendet werden und anschließend gespeichert werden. In diesem Fall muss der zweiten Kontextinstanz bekannt sein, ob die Entitäten neu (Einfügung erforderlich) oder bereits vorhanden (Aktualisierung erforderlich) sind.

<!-- markdownlint-disable MD028 -->
> [!TIP]
> Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/) finden Sie auf GitHub.

> [!TIP]
> EF Core kann nur eine Instanz einer Entität mit einem bestimmten primären Schlüsselwert nachverfolgen. Dass dies ein Problem darstellt, kann verhindert werden, indem für die einzelnen Arbeitseinheiten kurzlebiger Kontext verwendet wird, wie z.B. dass der Kontext leer beginnt, über angefügte Entitäten verfügt, diese Entitäten speichert und der Kontext anschließend verworfen wird.
<!-- markdownlint-enable MD028 -->

## <a name="identifying-new-entities"></a>Identifizieren neuer Entitäten

### <a name="client-identifies-new-entities"></a>Client identifiziert neue Entitäten

Am einfachsten ist, wenn der Client den Server darüber informiert, ob die Entität neu oder vorhanden ist. Die Anforderung zum Einfügen einer neuen Entität unterscheidet sich beispielsweise häufig von der Anforderung zum Aktualisieren einer vorhandenen Entität.

Im restlichen Teil dieses Abschnitts werden die Fälle behandelt, bei denen auf andere Weise bestimmt werden muss, ob eine Einfügung oder ein Update erforderlich ist.

### <a name="with-auto-generated-keys"></a>Mit automatisch generierten Schlüsseln

Mit dem Wert eines automatisch generierten Schlüssels kann häufig bestimmt werden, ob eine Entität eingefügt oder aktualisiert werden muss. Wenn der Schlüssel nicht festgelegt wurde (d.h. der CLR-Standardwert ist noch NULL, 0 (Null) etc.), ist davon auszugehen, dass die Entität neu ist und eingefügt werden muss. Wenn der Schlüsselwert bereits festgelegt worden ist, muss er andererseits zuvor gespeichert worden sein und nun aktualisiert werden. Das heißt, wenn der Schlüssel einen Wert aufweist, wurde die Entität abgefragt, an den Client gesendet und nun für ein Update zurückgesendet.

Wenn der Entitätstyp bekannt ist, kann ohne großen Aufwand überprüft werden, ob ein nicht festgelegter Schlüssel vorhanden ist:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewSimple)]

EF verfügt jedoch auch über eine integrierte Möglichkeit, diesen Vorgang für einen beliebigen Entitäts- und Schlüsseltyp durchzuführen:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]  
> Schlüssel werden festgelegt, sobald Entitäten vom Kontext nachverfolgt werden, selbst dann, wenn die Entität den Status „Hinzugefügt“ aufweist. Dies ist hilfreich, wenn ein Graph mit Entitäten durchlaufen wird und entschieden wird, wie mit den einzelnen Entitäten umgegangen werden soll, z.B. bei der Verwendung der TrackGraph-API. Der Schlüsselwert sollte nur auf die hier dargestellte Weise verwendet werden, _bevor_ ein Aufruf zum Nachverfolgen der Entität durchgeführt wird.

### <a name="with-other-keys"></a>Mit anderen Schlüsseln

Zum Identifizieren neuer Entitäten sind einige andere Mechanismen erforderlich, wenn Schlüsselwerte nicht automatisch generiert werden. Hierfür gibt es zwei allgemeine Ansätze:

* Abfrage für die Entität
* Übergeben eines Flags vom Client

Verwenden Sie für eine Abfrage für die Entität einfach die Find-Methode:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewQuery)]

Die Anzeige des vollständigen Codes für die Übergabe eines Flags von einem Client ist nicht Gegenstand dieses Dokuments. In einer Web-App bedeutet dies in der Regel, dass für verschiedene Aktionen unterschiedliche Anforderungen durchgeführt werden, oder dass einige Status in der Anforderung übergeben und anschließend im Controller extrahiert werden.

## <a name="saving-single-entities"></a>Speichern einzelner Entitäten

Wenn bekannt ist, ob eine Einfügung oder ein Update erforderlich ist, kann entsprechend die Add- oder die Update-Methode verwendet werden:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

Wenn die Entität automatisch generierte Schlüsselwerte verwendet, kann die Update-Methode in beiden Fällen verwendet werden:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

Die Update-Methode markiert die Entität normalerweise für das Update, nicht für die Einfügung. Wenn die Entität über einen automatisch generierten Schlüssel verfügt und kein Schlüsselwert festgelegt wurde, wird die Entität jedoch stattdessen für eine Einfügung markiert.

Wenn die Entität keine automatisch generierten Schlüssel verwendet, muss die Anwendung entscheiden, ob die Entität eingefügt oder aktualisiert werden sollte. Beispiel:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

Folgende Schritte müssen ausgeführt werden:

* Wenn die Find-Methode NULL zurückgibt, enthält die Datenbank den Blog mit dieser ID noch nicht. Daher wird die Add-Methode aufgerufen, um die Datenbank für eine Einfügung zu markieren.
* Wenn die Find-Methode eine Entität zurückgibt, ist diese in der Datenbank vorhanden, und der Kontext verfolgt nun die vorhandene Entität nach
  * Anschließend werden die Werte für sämtliche Eigenschaften dieser Entität mit der SetValues-Methode auf die vom Client stammenden Werte festgelegt.
  * Beim SetValues-Aufruf wird die Entität entsprechend ihrer Markierung nach Bedarf aktualisiert.

> [!TIP]  
> SetValues markiert nur die Eigenschaften als geändert, die andere Werte aufweisen als die Eigenschaften in der verfolgten Entität. Das heißt, wenn das Update gesendet wird, werden nur die Spalten aktualisiert, die tatsächlich geändert wurden. (Und wenn keine Änderungen vorgenommen wurden, wird gar kein Update gesendet.)

## <a name="working-with-graphs"></a>Arbeiten mit Graphen

### <a name="identity-resolution"></a>Identitätsauflösung

Wie oben bereits erwähnt, kann EF Core nur eine Instanz einer Entität mit einem bestimmten primären Schlüsselwert nachverfolgen. Bei der Arbeit mit Graphen sollte der Graph idealerweise so erstellt werden, dass diese Invariante beibehalten wird. Zudem sollte der Kontext nur für eine Arbeitseinheit verwendet werden. Wenn der Graph Duplikate enthält, muss dieser verarbeitet werden, bevor er an EF gesendet wird, um mehrere Instanzen in eine zu konsolidieren. Dies ist möglicherweise nicht einfach, wenn Instanzen in Konflikt stehende Werte und Beziehungen aufweisen. Zur Vermeidung einer Konfliktauflösung sollte die Konsolidierung von Duplikaten folglich so schnell wie möglich in Ihrer Anwendungspipeline erfolgen.

### <a name="all-newall-existing-entities"></a>Alle neuen/alle vorhandenen Entitäten

Ein Beispiel für die Arbeit mit Graphen besteht in einer Einfügung oder Aktualisierung eines Blogs zusammen mit der zugehörigen Sammlung zugehöriger Beiträge. Wenn alle Entitäten im Graph eingefügt werden sollen, oder wenn alle Entitäten aktualisiert werden sollen, entspricht der Prozess dem oben beschriebenen Prozess für einzelne Entitäten. Beispiel: Ein wie folgt erstellter Graph mit Blogs und Beiträgen:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

kann wie folgt eingefügt werden:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertGraph)]

Beim Aufruf zum Hinzufügen werden der Blog und sämtliche Beiträge für eine Einfügung markiert.

Gleichermaßen kann die Update-Methode verwendet werden, wenn sämtliche Entitäten in einem Graph aktualisiert werden müssen:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#UpdateGraph)]

Der Blog und alle zugehörigen Beiträge werden für ein Update markiert.

### <a name="mix-of-new-and-existing-entities"></a>Mischung aus neuen und vorhandenen Entitäten

Bei automatisch generierten Schlüsseln kann erneut für Einfügungen und Updates die Update-Methode verwendet werden. Dies gilt auch dann, wenn der Graph eine Mischung aus einzufügenden und zu aktualisierenden Entitäten enthält:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

Die Update-Methode markiert eine beliebige Entität im Graph, Blog oder Beitrag für eine Einfügung, wenn diese keinen festgelegten Schlüsselwert aufweist, während alle anderen Entitäten für eine Aktualisierung markiert werden.

Wie bisher können eine Abfrage und eine Verarbeitungsschritte verwendet werden, wenn keine automatisch generierten Schlüssel verwendet werden:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a>Behandlung von Löschvorgängen

Die Behandlung von Löschvorgängen kann kompliziert sein, da die Abwesenheit einer Entität häufig bedeutet, dass diese gelöscht werden sollte. Eine Möglichkeit für den Umgang hiermit besteht in der Verwendung von „vorläufigen Löschvorgängen“. Dabei wird die Entität als gelöscht markiert, statt tatsächlich gelöscht zu werden. Löschvorgänge entsprechen anschließend Updates. Vorläufige Löschvorgänge können mit [Abfragefiltern](xref:core/querying/filters) implementiert werden.

Bei Löschvorgängen mit dem Wert „TRUE“ wird häufig eine Erweiterung des auszuführenden Abfragemusters verwendet. Dies ist im Grunde genommen eine GraphDiff-Methode. Beispiel:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a>TrackGraph

Die Add-, Attach- und Update-Methoden verwenden intern einen Diagrammdurchlauf, in dem bestimmt wird, ob die einzelnen Entitäten als „Hinzugefügt“ (für Einfügung), „Geändert“ (für Update), „Unverändert“ (nichts unternehmen) oder als „Gelöscht“ (für Löschung) markiert werden sollen. Dieser Mechanismus wird über die TrackGraph-API verfügbar gemacht. Angenommen beispielsweise, der Client sendet einen Graph mit Entitäten zurück und legt für jede Entität ein Flag fest, das angibt, wie die Entität behandelt werden soll. Anschließend kann dieses Flag mit TrackGraph verarbeitet werden:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#TrackGraph)]

Zur Vereinfachung des Beispiels werden die Flags nur als Teil der Entität angezeigt. Normalerweise wären die Flags Teil eines DTO oder eines anderen in der Anforderung enthaltenen Status.
