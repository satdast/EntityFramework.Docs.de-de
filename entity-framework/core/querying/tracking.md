---
title: 'Abfragen mit Nachverfolgung im Vergleich zu Abfragen ohne Nachverfolgung: EF Core'
description: Informationen zu Abfragen mit und ohne Nachverfolgung in Entity Framework Core
author: smitpatel
ms.date: 11/09/2020
uid: core/querying/tracking
ms.openlocfilehash: cb18125fb3453bb533981afb36480b12727cd6f2
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983416"
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

Wenn die Ergebnisse in einer Abfrage mit Nachverfolgung zurückgegeben werden, prüft EF Core, ob die Entität bereits im Kontext ist. Wenn EF Core eine vorhandene Entität findet, wird dieselbe Instanz zurückgegeben. EF Core überschreibt im Eintrag mit den Datenbankwerten nicht die aktuellen und ursprünglichen Werte der Eigenschaften der Entität. Wenn die Entität nicht im Kontext gefunden wird, erstellt EF Core eine neue Entitätsinstanz und fügt sie dem Kontext an. Abfrageergebnisse enthalten keine Entität, die dem Kontext hinzugefügt, aber noch nicht in der Datenbank gespeichert wurde.

## <a name="no-tracking-queries"></a>Abfragen ohne Nachverfolgung

Abfragen ohne Nachverfolgung sind nützlich, wenn die Ergebnisse in einem schreibgeschützten Szenario verwendet werden. Sie werden schneller ausgeführt, da keine Informationen für die Änderungsnachverfolgung eingerichtet werden müssen. Wenn Sie die aus der Datenbank abgerufenen Entitäten nicht aktualisieren müssen, sollte eine Abfrage ohne Nachverfolgung verwendet werden. Sie können eine einzelne Abfrage ändern, sodass sie keine Nachverfolgung ausführt. Keine Abfrage mit Nachverfolgung liefert Ihnen zudem Ergebnisse, die auf dem Inhalt der Datenbank basieren, wobei lokale Änderungen oder hinzugefügte Entitäten unberücksichtigt bleiben.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#NoTracking)]

Sie können das Standardnachverfolgungsverhalten auch auf der Ebene der Kontextinstanz ändern:

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ContextDefaultTrackingBehavior)]

## <a name="identity-resolution"></a>Identitätsauflösung

Da eine Nachverfolgungsabfrage die Änderungsprotokollierung verwendet, führt EF Core in einer Nachverfolgungsabfrage eine Identitätsauflösung durch. Beim Materialisieren einer Entität gibt EF Core dieselbe Entitätsinstanz aus der Änderungsprotokollierung zurück, wenn sie bereits nachverfolgt wird. Wenn das Ergebnis mehrmals dieselbe Entität enthält, erhalten Sie für jedes Vorkommen dieselbe Instanz. Abfragen ohne Nachverfolgung verwenden weder die Änderungsprotokollierung, noch führen sie eine Identitätsauflösung durch. Sie erhalten also auch dann eine neue Instanz der Entität, wenn dieselbe Entität mehrmals im Ergebnis enthalten ist. Dieses Verhalten war in Versionen vor EF Core 3.0 anders, siehe [frühere Versionen](#previous-versions).

Ab EF Core 5.0 können Sie beide oben genannten Verhalten in derselben Abfrage kombinieren. Das heißt, Sie können eine Abfrage ohne Nachverfolgung haben, die eine Identitätsauflösung in den Ergebnissen vornimmt. Genau wie den abfragbaren Operator `AsNoTracking()` haben wir mit `AsNoTrackingWithIdentityResolution()` einen weiteren Operator hinzugefügt. Der Enumeration <xref:Microsoft.EntityFrameworkCore.QueryTrackingBehavior> wurde außerdem ein zugeordneter Eintrag hinzugefügt. Wenn Sie die Abfrage so konfigurieren, dass die Identitätsauflösung ohne Nachverfolgung verwendet wird, verwenden wir bei der Generierung der Abfrageergebnisse eine eigenständige Änderungsnachverfolgung im Hintergrund, sodass jede Instanz sich nur einmal materialisiert. Da sich diese Änderungsnachverfolgung von der im Kontext unterscheidet, werden die Ergebnisse nicht vom Kontext nachverfolgt. Nachdem die Abfrage vollständig aufgezählt ist, verlässt die Änderungsnachverfolgung den Gültigkeitsbereich, und die Garbage Collection erfolgt den Anforderungen entsprechend.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#NoTrackingWithIdentityResolution)]

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

- Wenn Abfrageergebnisse schlüssellose Entitätstypen enthielten, wurde die gesamte Abfrage nicht nachverfolgt. Dies bedeutet, dass Entitätstypen mit Schlüsseln, die sich im Ergebnis befanden, auch nicht nachverfolgt wurden.
- EF Core führte in Abfragen ohne Nachverfolgung eine Identitätsauflösung durch. Schwache Verweise wurden verwendet, um bereits zurückgegebene Entitäten nachzuverfolgen. Wenn also ein Resultset dieselbe Entität mehrfach enthielt, erhielten Sie für jedes Vorkommen dieselbe Instanz. Auch wenn ein vorheriges Ergebnis mit derselben Identität den Gültigkeitsbereich verließ und eine Garbage Collection durchgeführt wurde, gab EF Core eine neue Instanz zurück.
