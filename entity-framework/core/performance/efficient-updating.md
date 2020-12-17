---
title: Effizientes aktualisieren-EF Core
description: Leistungs Leit Faden zur effizienten Aktualisierung mithilfe Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/performance/efficient-updating
ms.openlocfilehash: 92766d2339fb04ed5ebc3123429171cc9be424b1
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657730"
---
# <a name="efficient-updating"></a>Effizientes aktualisieren

## <a name="batching"></a>Batchverarbeitung

EF Core hilft, Roundtrips zu minimieren, indem alle Updates automatisch in einem einzigen Roundtrip zusammengefasst werden. Beachten Sie Folgendes:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#SaveChangesBatching)]

Im obigen Beispiel wird ein Blog aus der Datenbank geladen, dessen Name geändert und zwei neue Blogs hinzugefügt. um dies anzuwenden, werden zwei SQL INSERT-Anweisungen und eine Update-Anweisung an die Datenbank gesendet. Anstatt sie einzeln zu senden, da blobinstanzen hinzugefügt werden, verfolgt EF Core diese Änderungen intern und führt Sie in einem einzelnen Roundtrip aus, wenn <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aufgerufen wird.

Die Anzahl der Anweisungen, die EF in einem einzelnen Roundtrip verarbeitet, hängt vom verwendeten Datenbankanbieter ab. Beispielsweise hat die Leistungsanalyse gezeigt, dass die Batch Verarbeitung für SQL Server im Allgemeinen weniger effizient ist, wenn weniger als vier Anweisungen beteiligt sind. Ebenso sind die Vorteile der Batch Verarbeitung nach ungefähr 40 Anweisungen für SQL Server, sodass EF Core standardmäßig nur bis zu 42 Anweisungen in einem einzelnen Batch ausführt und zusätzliche Anweisungen in separaten Roundtrips ausführt.

Benutzer können diese Schwellenwerte auch optimieren, um eine potenziell höhere Leistung zu erzielen, aber Vergleichstests sorgfältig durchzuführen, bevor Sie Sie ändern:

[!code-csharp[Main](../../../samples/core/Performance/BatchTweakingContext.cs#BatchTweaking)]

## <a name="bulk-updates"></a>Massen Aktualisierungen

Angenommen, Sie möchten allen ihren Mitarbeitern eine Erhöhung zukommen lassen. Eine typische Implementierung hierfür in EF Core würde wie folgt aussehen:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithoutBulk)]

Obwohl es sich um einen vollständig gültigen Code handelt, analysieren wir die Leistung aus Leistungsgründen:

* Ein datenbankroundtrip wird ausgeführt, um alle relevanten Mitarbeiter zu laden. Beachten Sie, dass dadurch alle Zeilendaten aller Mitarbeiter an den Client weitergeführt werden, auch wenn nur das Gehalt benötigt wird.
* Die Änderungs Nachverfolgung von EF Core erstellt Momentaufnahmen, wenn die Entitäten geladen werden, und vergleicht dann diese Momentaufnahmen mit den Instanzen, um herauszufinden, welche Eigenschaften geändert wurden.
* Ein zweiter datenbankroundtrip wird ausgeführt, um alle Änderungen zu speichern. Obwohl alle Änderungen aufgrund der Batch Verarbeitung in einem einzelnen Roundtrip vorgenommen werden, sendet EF Core weiterhin eine Update-Anweisung pro Mitarbeiter, die von der Datenbank ausgeführt werden muss.

Relationale Datenbanken unterstützen auch *Massen Aktualisierungen*, sodass die oben genannten mit der folgenden einzelnen SQL-Anweisung umgeschrieben werden könnten:

```sql
UPDATE [Employees] SET [Salary] = [Salary] + 1000;
```

Dadurch wird der gesamte Vorgang in einem einzigen Roundtrip durchführt, ohne dass Daten in die Datenbank geladen oder gesendet werden, und ohne dass die Änderungs nach Verfolgungs Mechanismen von EF genutzt werden, was einen zusätzlichen mehr Aufwand erfordert.

Leider bietet EF derzeit keine APIs für die Durchführung von Massen Aktualisierungen. Bis Sie diese eingeführt haben, können Sie den Vorgang, bei dem die Leistung sensibel ist, mithilfe von RAW SQL ausführen:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithBulk)]
