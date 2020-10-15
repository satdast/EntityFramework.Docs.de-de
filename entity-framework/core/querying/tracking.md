---
title: 'Abfragen mit Nachverfolgung im Vergleich zu Abfragen ohne Nachverfolgung: EF Core'
description: Informationen zu Abfragen mit und ohne Nachverfolgung in Entity Framework Core
author: smitpatel
ms.date: 10/10/2019
uid: core/querying/tracking
ms.openlocfilehash: dff6c14edcd69e7d16be8bab5fa3088c2c1288e1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063659"
---
# <a name="tracking-vs-no-tracking-queries"></a>Abfragen mit Nachverfolgung im Vergleich zu Abfragen ohne Nachverfolgung

Das Nachverfolgungsverhalten steuert, ob Entity Framework Core Informationen über eine Entitätsinstanz in der Änderungsprotokollierung speichert. Wenn eine Entität nachverfolgt wird, werden alle Änderungen, die in der Entität erkannt werden, während `SaveChanges()` in der Datenbank beibehalten. EF Core korrigiert auch Unstimmigkeiten zwischen den Navigationseigenschaften von Entitäten in einem Nachverfolgungs-Abfrageergebnis und Entitäten, die sich in der Änderungsprotokollierung befinden.

> [!NOTE]
> [Schlüssellose Entitätstypen](xref:core/modeling/keyless-entity-types) werden nie nachverfolgt. Wenn in diesem Artikel Entitätstypen erwähnt werden, sind Entitätstypen gemeint, für die ein Schlüssel definiert ist.

> [!TIP]  
> Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tracking) finden Sie auf GitHub.

## <a name="tracking-queries"></a>Abfragen mit Nachverfolgung

Abfragen, die Entitätstypen zurückgeben, verfügen standardmäßig über Nachverfolgung. Das bedeutet, Sie können Änderungen an diesen Entitätsinstanzen vornehmen, und diese Änderungen werden von `SaveChanges()` beibehalten. Im folgenden Beispiel wird die Änderung an der Bewertung des Blogs erkannt und während `SaveChanges()` in der Datenbank beibehalten.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#Tracking)]

## <a name="no-tracking-queries"></a>Abfragen ohne Nachverfolgung

Abfragen ohne Nachverfolgung sind nützlich, wenn die Ergebnisse in einem schreibgeschützten Szenario verwendet werden. Sie werden schneller ausgeführt, da keine Informationen für die Änderungsnachverfolgung eingerichtet werden müssen. Wenn Sie die aus der Datenbank abgerufenen Entitäten nicht aktualisieren müssen, sollte eine Abfrage ohne Nachverfolgung verwendet werden. Sie können eine einzelne Abfrage ändern, sodass sie keine Nachverfolgung ausführt.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#NoTracking)]

Sie können das Standardnachverfolgungsverhalten auch auf der Ebene der Kontextinstanz ändern:

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ContextDefaultTrackingBehavior)]

## <a name="identity-resolution"></a>Identitätsauflösung

Da eine Nachverfolgungsabfrage die Änderungsprotokollierung verwendet, führt EF Core in einer Nachverfolgungsabfrage eine Identitätsauflösung durch. Beim Materialisieren einer Entität gibt EF Core dieselbe Entitätsinstanz aus der Änderungsprotokollierung zurück, wenn sie bereits nachverfolgt wird. Wenn das Ergebnis mehrmals dieselbe Entität enthält, erhalten Sie für jedes Vorkommen dieselbe Instanz. Abfragen ohne Nachverfolgung verwenden weder die Änderungsprotokollierung, noch führen sie eine Identitätsauflösung durch. Sie erhalten also auch dann eine neue Instanz der Entität, wenn dieselbe Entität mehrmals im Ergebnis enthalten ist. Dieses Verhalten war in Versionen vor EF Core 3.0 anders, siehe [frühere Versionen](#previous-versions).

## <a name="tracking-and-custom-projections"></a>Nachverfolgung und benutzerdefinierte Projektionen

Selbst wenn der Ergebnistyp der Abfrage kein Entitätstyp ist, verfolgt EF Core im Ergebnis enthaltene Entitätstypen standardmäßig nach. In der folgenden Abfrage, die einen anonymen Typ zurückgibt, werden die Instanzen von `Blog` im Ergebnis nachverfolgt.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection1)]

Wenn das Resultset Entitätstypen enthält, die aus der LINQ-Komposition stammen, werden sie von EF Core nachverfolgt.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection2)]

Wenn das Resultset keine Entitätstypen enthält, wird keine Nachverfolgung ausgeführt. In der folgenden Abfrage geben wir einen anonymen Typ mit einigen Werten der Entität zurück (aber keine Instanzen des aktuellen Entitätstyps). Es sind keine aus der Abfrage stammenden nachverfolgten Entitäten vorhanden.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection3)]

 EF Core unterstützt die Clientauswertung in der Projektion auf oberster Ebene. Wenn EF Core eine Entitätsinstanz für die Clientauswertung materialisiert, wird sie nachverfolgt. Da wir hier `blog`-Entitäten an die Clientmethode `StandardizeURL` übergeben, verfolgt EF Core auch die Bloginstanzen nach.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientMethod)]

EF Core verfolgt nicht die schlüssellosen Entitätsinstanzen nach, die im Ergebnis enthalten sind. EF Core verfolgt jedoch alle anderen Instanzen von Entitätstypen mit Schlüssel gemäß den oben aufgeführten Regeln nach.

Einige der oben genannten Regeln funktionierten vor EF Core 3.0 anders. Weitere Informationen siehe [frühere Versionen](#previous-versions).

## <a name="previous-versions"></a>Vorherige Versionen

Vor Version 3.0 wies EF Core bei der Nachverfolgung einige Unterschiede auf. Wesentliche Unterschiede sind:

- Wie auf der Seite [Clientauswertung im Vergleich zur Serverauswertung](xref:core/querying/client-eval) erläutert, unterstützte EF Core vor Version 3.0 die Clientauswertung in einem beliebigen Teil der Abfrage. Die Clientauswertung verursachte Materialisierungen von Entitäten, die nicht Teil des Ergebnisses waren. Daher analysierte EF Core das Ergebnis, um zu ermitteln, was nachverfolgt werden soll. Dieser Entwurf wies wie folgt bestimmte Unterschiede auf:
  - Clientauswertung in der Projektion, die die Materialisierung verursachte, aber die materialisierte Entitätsinstanz nicht zurückgab, wurde nicht nachverfolgt. Im folgenden Beispiel wurden `blog`-Entitäten nicht nachverfolgt.
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientProjection)]

  - In bestimmten Fällen wurden aus der LINQ-Komposition stammende Objekte von EF Core nicht nachverfolgt. Im folgenden Beispiel wurde `Post` nicht nachverfolgt.
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection2)]

- Wenn Abfrageergebnisse schlüssellose Entitätstypen enthielten, wurde die gesamte Abfrage nicht nachverfolgt. Dies bedeutet, dass Entitätstypen mit Schlüsseln, die sich in einem Ergebnis befanden, auch nicht nachverfolgt wurden.
- EF Core führte in Abfragen ohne Nachverfolgung keine Identitätsauflösung durch. Schwache Verweise wurden verwendet, um bereits zurückgegebene Entitäten nachzuverfolgen. Wenn also ein Resultset dieselbe Entität mehrfach enthielt, erhielten Sie für jedes Vorkommen dieselbe Instanz. Auch wenn ein vorheriges Ergebnis mit derselben Identität den Gültigkeitsbereich verließ und eine Garbage Collection durchgeführt wurde, gab EF Core eine neue Instanz zurück.
