---
title: Gemeinsame Nutzung von Datenbanken zwischen Tests-EF Core
description: Beispiel, das zeigt, wie eine Datenbank zwischen mehreren Tests gemeinsam genutzt wird
author: ajcvickers
ms.date: 04/25/2020
uid: core/testing/sharing-databases
ms.openlocfilehash: 95b756c80b983356a07fd836aa1b02f2835e6629
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431224"
---
# <a name="sharing-databases-between-tests"></a>Freigeben von Datenbanken zwischen Tests

Im [Beispiel für die EF Core Tests](xref:core/testing/testing-sample) wurde gezeigt, wie Sie Anwendungen mit verschiedenen Datenbanksystemen testen.
In diesem Beispiel hat jeder Test eine neue Datenbank erstellt.
Dies ist ein gutes Muster bei der Verwendung von SQLite oder der EF-in-Memory Database, aber es kann einen erheblichen mehr Aufwand bei der Verwendung anderer Datenbanksysteme bedeuten.

Dieses Beispiel baut auf dem vorherigen Beispiel auf, indem die Daten Bank Erstellung in eine Test Fixierung verschoben wird.
Dadurch kann eine einzelne SQL Server Datenbank erstellt und nur einmal für alle Tests durchsucht werden.

> [!TIP]
> Stellen Sie sicher, dass Sie das [Beispiel für EF Core Tests](xref:core/testing/testing-sample) durcharbeiten, bevor Sie fortfahren.

Es ist nicht schwierig, mehrere Tests für dieselbe Datenbank zu schreiben.
Der Trick besteht darin, dass die Tests nicht aufeinander verlaufen, während Sie ausgeführt werden.
Dies erfordert Folgendes:

* Sicheres Freigeben von Objekten zwischen Tests
* Wenn das Test Framework Tests parallel ausführt
* So halten Sie die Datenbank für jeden Test in einem sauberen Zustand  

## <a name="the-fixture"></a>Die Fixierung

Wir verwenden eine Test Fixierung für die Freigabe von Objekten zwischen Tests.
In der [xUnit-Dokumentation](https://xunit.net/docs/shared-context.html) wird angegeben, dass eine Fixierung verwendet werden soll: "Wenn Sie einen einzelnen Test Kontext erstellen und für alle Tests in der Klasse freigeben möchten, und nachdem alle Tests in der Klasse abgeschlossen wurden."

> [!TIP]
> In diesem Beispiel wird [xUnit](https://xunit.net/)verwendet, aber ähnliche Konzepte sind in anderen Test-Frameworks, einschließlich [nunit](https://nunit.org/), vorhanden.

Dies bedeutet, dass wir die Daten Bank Erstellung und das Seeding in eine Fixierungs Klasse verschieben müssen.
Das Fenster sieht so aus:

[!code-csharp[SharedDatabaseFixture](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseFixture.cs?name=SharedDatabaseFixture)]

Beachten Sie vorerst, wie der Konstruktor lautet:

* Erstellt eine einzelne Datenbankverbindung für die Lebensdauer der Fixierung.
* Erstellt und erstellt die Datenbank durch Aufrufen der- `Seed` Methode.

Sperre vorerst ignorieren; Wir werden später darauf zurückkommen.

> [!TIP]
> Der Erstellungs-und Seeding Code muss nicht Async sein.
> Die asynchrone Erstellung erschwert den Code und führt nicht zu einer Verbesserung der Leistung oder des Durchsatzes von Tests.

Die Datenbank wird erstellt, indem zuerst eine vorhandene Datenbank gelöscht und dann eine neue Datenbank erstellt wird.
Dadurch wird sichergestellt, dass die Datenbank mit dem aktuellen EF-Modell übereinstimmt, auch wenn Sie seit dem letzten Testlauf geändert wurde.

> [!TIP]
> Es kann schneller sein, die vorhandene Datenbank zu "bereinigen", indem Sie etwas ähnliches wie das Erstellen von [Vorgängen](https://jimmybogard.com/tag/respawn/) verwendet, anstatt Sie jedes Mal neu zu erstellen.
> Es muss jedoch darauf geachtet werden, dass das Datenbankschema mit dem EF-Modell auf dem neuesten Stand ist.

Die Datenbankverbindung wird verworfen, wenn die Fixierung verworfen wird.
Sie können auch das Löschen der Testdatenbank an dieser Stelle in Erwägung gezogen.
Dies erfordert jedoch zusätzliche Sperren und eine Verweis Zählung, wenn die Fixierung von mehreren Test Klassen gemeinsam genutzt wird.
Außerdem ist es häufig hilfreich, dass die Testdatenbank weiterhin zum Debuggen von fehlgeschlagenen Tests verfügbar ist.  

## <a name="using-the-fixture"></a>Verwenden der Fixierung

XUnit hat ein gängiges Muster zum Zuordnen einer Test Fixierung zu einer Klasse von Tests:

[!code-csharp[UsingTheFixture](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=UsingTheFixture)]

XUnit erstellt jetzt eine einzelne Fixierungs Instanz und übergibt sie an jede Instanz der Testklasse.
(Beachten Sie das erste [Testbeispiel](xref:core/testing/testing-sample) , dass xUnit bei jeder Ausführung eines Tests eine neue Test Klasseninstanz erstellt.) Dies bedeutet, dass die Datenbank einmal erstellt und ein Seeding erstellt wird, und dann wird jeder Test diese Datenbank verwenden.

Beachten Sie, dass Tests in einer einzelnen Klasse nicht parallel ausgeführt werden.
Dies bedeutet, dass es für jeden Test sicher ist, dieselbe Datenbankverbindung zu verwenden, auch wenn das `DbConnection` Objekt nicht Thread sicher ist.

## <a name="maintaining-database-state"></a>Verwalten des Daten Bank Status

Tests müssen häufig die Testdaten durch Einfügungen, Updates und Löschungen mutieren.
Diese Änderungen wirken sich jedoch auf andere Tests aus, bei denen eine saubere, Seeding Datenbank erwartet wird.

Dies kann durch Ausführen von veränderenden Tests innerhalb einer Transaktion behoben werden.
Beispiel:

[!code-csharp[CanAddItem](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=CanAddItem)]

Beachten Sie, dass die Transaktion beim Start des Tests erstellt und verworfen wird.
Das Verwerfen der Transaktion bewirkt, dass ein Rollback ausgeführt wird, sodass keine der Änderungen von anderen Tests erkannt wird.

Die Hilfsmethode zum Erstellen eines Kontexts (siehe den obigen Fixierungs Code) akzeptiert diese Transaktion und optet den dbcontext in der Verwendung.

## <a name="sharing-the-fixture"></a>Freigeben der Fixierung

Sie haben vielleicht bemerkt, dass Sie den Code für die Erstellung und das Seeding der Datenbank
Dies ist für dieses Beispiel nicht erforderlich, da nur eine Klasse von Tests die Fixierung verwendet, sodass nur eine einzige Fixierungs Instanz erstellt wird.

Möglicherweise möchten Sie jedoch dieselbe Fixierung mit mehreren Test Klassen verwenden.
XUnit erstellt eine Fixierungs Instanz für jede dieser Klassen.
Diese können von verschiedenen Threads verwendet werden, die Tests parallel ausführen.
Daher ist es wichtig, dass Sie über eine geeignete Sperre verfügen, um sicherzustellen, dass nur ein Thread die Erstellung und das Seeding der Datenbank übernimmt

> [!TIP]
> Eine einfache `lock` ist in Ordnung.
> Es ist nicht erforderlich, etwas komplexeres zu versuchen, wie z. b. sperrfreie Muster.
