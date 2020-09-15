---
title: Handhaben von Nebenläufigkeitskonflikten
description: Umgang mit Konflikten bei gleichzeitiger Aktualisierung derselben Daten in Entity Framework Core
author: rowanmiller
ms.date: 03/03/2018
uid: core/saving/concurrency
ms.openlocfilehash: 7e3781879b39e6c30a0c981b5e0b74baf2b2863b
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617308"
---
# <a name="handling-concurrency-conflicts"></a>Behandlung von Parallelitätskonflikten

> [!NOTE]
> Auf dieser Seite wird erläutert, wie Parallelität in EF Core funktioniert und wie sich Nebenläufigkeitskonflikte in Ihrer Anwendung handhaben lassen. Weitere Informationen zum Konfigurieren von Parallelitätstoken in Ihrem Modell finden Sie unter [Parallelitätstoken](xref:core/modeling/concurrency).

> [!TIP]
> Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Concurrency/) finden Sie auf GitHub.

_Datenbankparallelität_ ist gegeben, wenn mehrere Prozesse oder Benutzer gleichzeitig auf dieselben Daten in einer Datenbank zugreifen oder diese ändern. Mit _Parallelitätssteuerung_ sind bestimmte Mechanismen gemeint, mit denen die Datenkonsistenz bei gleichzeitigen Änderungen sichergestellt wird.

EF Core implementiert eine _optimistische Parallelitätssteuerung_: Mehrere Prozesse oder Benutzer können also unabhängig voneinander Änderungen vornehmen, ohne dass der Mehraufwand einer Synchronisierung oder Sperrung anfällt. Im Idealfall wirken sich diese Änderungen nicht aufeinander aus und werden daher übernommen. Im schlechtesten Fall versuchen zwei oder mehr Prozesse, in Konflikt stehende Änderungen vorzunehmen, und nur einer davon ist erfolgreich.

## <a name="how-concurrency-control-works-in-ef-core"></a>Funktionsweise der Parallelitätssteuerung in EF Core

Mithilfe von Eigenschaften, die als Parallelitätstoken konfiguriert werden, wird die optimistische Parallelitätssteuerung implementiert: Immer wenn während `SaveChanges` ein Update- oder Löschvorgang ausgeführt wird, wird der Wert des Parallelitätstokens der Datenbank mit dem ursprünglichen Wert verglichen, der von EF Core gelesen wird.

- Falls die Werte übereinstimmen, kann der Vorgang abgeschlossen werden.
- Ist dies jedoch nicht der Fall, geht EF Core davon aus, dass ein anderer Benutzer einen in Konflikt stehenden Vorgang ausgeführt hat, und bricht die aktuelle Transaktion ab.

Wenn ein anderer Benutzer einen in Konflikt stehenden Vorgang ausgeführt hat, spricht man von einem _Nebenläufigkeitskonflikt_.

Die Implementierung des Vergleichs der Parallelitätstokenwerte ist Aufgabe der Datenbankanbieter.

Für relationale Datenbanken lässt sich in EF Core eine Überprüfung des Parallelitätstokenwerts in der `WHERE`-Klausel einer jeden `UPDATE`- oder `DELETE`-Anweisung ausführen. Nach dem Ausführen der Anweisungen liest EF Core die Anzahl der Zeilen, die betroffen sind.

Wenn keine Zeilen betroffen sind, wird ein Nebenläufigkeitskonflikt erkannt, und EF Core löst `DbUpdateConcurrencyException` aus.

Angenommen, wir möchten `LastName` als Parallelitätstoken von `Person` konfigurieren. Alle Updatevorgänge von „Person“ enthalten dann eine Parallelitätsüberprüfung in der `WHERE`-Klausel:

``` sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="resolving-concurrency-conflicts"></a>Beheben von Nebenläufigkeitskonflikten

Setzen wir das vorherige Beispiel fort: Wenn ein Benutzer versucht, Änderungen an `Person` zu speichern, ein anderer Benutzer `LastName` jedoch bereits geändert hat, wird eine Ausnahme ausgelöst.

Die Anwendung sollte den Benutzer einfach darüber informieren, dass das Update aufgrund von in Konflikt stehenden Änderungen nicht durchgeführt werden konnte war, und fortfahren. Es kann sich jedoch auch empfehlen, den Benutzer aufzufordern, sicherzustellen, dass dieser Datensatz weiterhin dieselbe Person darstellt, und den Vorgang zu wiederholen.

So lässt sich z.B. ein _Nebenläufigkeitskonflikt beheben_.

Dazu müssen Sie die ausstehenden Änderungen aus dem aktuellen `DbContext` mit den Werten in der Datenbank zusammenführen. Welche Werte zusammengeführt werden, hängt von der jeweiligen Anwendung ab und kann durch die Benutzereingabe gesteuert sein.

**Nebenläufigkeitskonflikte können mit drei verschiedenen Wertetypen gelöst werden:**

- **Aktuelle Werte** sind die Werte, die die Anwendung in die Datenbank schreiben wollte.
- **Ursprüngliche Werte** sind die Werte, die vor den Änderungen aus der Datenbank abgerufen wurden.
- **Datenbankwerte** sind die Werte, die derzeit in der Datenbank gespeichert sind.

Nebenläufigkeitskonflikte werden im Allgemeinen folgendermaßen behoben:

1. Fangen Sie `DbUpdateConcurrencyException` während `SaveChanges` ab.
2. Bereiten Sie mit `DbUpdateConcurrencyException.Entries` neue Änderungen für die betroffenen Entitäten vor.
3. Aktualisieren Sie die ursprünglichen Werte des Parallelitätstokens, sodass sie mit den aktuellen Werten in der Datenbank übereinstimmen.
4. Wiederholen Sie den Prozess, bis keine Konflikte mehr auftreten.

Im folgenden Beispiel werden `Person.FirstName` und `Person.LastName` als Parallelitätstoken eingerichtet. Dort, wo Sie die anwendungsspezifische Logik platzieren, nach der der zu speichernde Wert ausgewählt wird, befindet sich ein `// TODO:`-Kommentar.

[!code-csharp[Main](../../../samples/core/Saving/Concurrency/Sample.cs?name=ConcurrencyHandlingCode&highlight=34-35)]
