---
title: Datenseeding-EF Core
description: Verwenden von datenseeding zum Auffüllen einer Datenbank mit einem anfänglichen Satz von Daten mithilfe von Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/02/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/data-seeding
ms.openlocfilehash: cdf189a4d2ec00f2bb094045541a98d1a66ffefc
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619380"
---
# <a name="data-seeding"></a>Datenseeding

Datenseeding ist der Vorgang, bei dem eine Datenbank mit einem anfänglichen Satz von Daten gefüllt wird.

Es gibt mehrere Möglichkeiten, wie dies in EF Core erreicht werden kann:

* Modellseed-Daten
* Anpassung der manuellen Migration
* Benutzerdefinierte Initialisierungs Logik

## <a name="model-seed-data"></a>Modellseed-Daten

> [!NOTE]
> Dieses Feature ist neu in EF Core 2.1.

Anders als in EF6 kann das Seeding von Daten in EF Core einem Entitätstyp als Teil der Modell Konfiguration zugeordnet werden. Anschließend können EF Core [Migrationen](xref:core/managing-schemas/migrations/index) automatisch berechnen, welche INSERT-, Update-oder DELETE-Vorgänge angewendet werden müssen, wenn die Datenbank auf eine neue Version des Modells aktualisiert wird.

> [!NOTE]
> Bei Migrationen werden Modelländerungen nur berücksichtigt, wenn bestimmt wird, welcher Vorgang ausgeführt werden soll, um die Ausgangsdaten in den gewünschten Zustand zu bringen. Daher können alle Änderungen an den Daten, die außerhalb der Migrationen durchgeführt werden, verloren gehen oder einen Fehler verursachen.

So werden z. b. Seed-Daten für einen `Blog` in konfiguriert `OnModelCreating` :

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

Um Entitäten hinzuzufügen, die eine Beziehung aufweisen, müssen die Fremdschlüssel Werte angegeben werden:

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

Wenn der Entitätstyp über Eigenschaften im Schatten Zustand verfügt, kann eine anonyme Klasse verwendet werden, um die Werte bereitzustellen:

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

Eigene Entitäts Typen können auf ähnliche Weise als Seeding betrachtet werden:

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

Weitere Informationen finden Sie im [vollständigen Beispiel Projekt](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) .

Nachdem die Daten dem Modell hinzugefügt [wurden, sollten](xref:core/managing-schemas/migrations/index) die Änderungen verwendet werden, um die Änderungen zu übernehmen.

> [!TIP]
> Wenn Sie Migrationen als Teil einer automatisierten Bereitstellung anwenden müssen, können Sie [ein SQL-Skript erstellen](xref:core/managing-schemas/migrations/index#generate-sql-scripts) , das vor der Ausführung in der Vorschau angezeigt werden kann.

Alternativ können Sie verwenden, `context.Database.EnsureCreated()` um eine neue Datenbank zu erstellen, die die Ausgangsdaten enthält, z. b. für eine Testdatenbank, oder wenn Sie den in-Memory-Anbieter oder eine nicht-beziehungsdatenbank verwenden. Beachten Sie Folgendes: Wenn die Datenbank bereits vorhanden ist, `EnsureCreated()` aktualisiert weder das Schema noch die Seed-Daten in der Datenbank. Bei relationalen Datenbanken sollten Sie nicht anrufen `EnsureCreated()` , wenn Sie beabsichtigen, Migrationen zu verwenden.

### <a name="limitations-of-model-seed-data"></a>Einschränkungen von modellseed-Daten

Diese Art von Ausgangsdaten wird durch Migrationen verwaltet, und das Skript zum Aktualisieren der Daten, die bereits in der Datenbank vorhanden sind, muss generiert werden, ohne dass eine Verbindung mit der Datenbank hergestellt werden muss. Dies setzt einige Einschränkungen mit sich:

* Der Primärschlüssel Wert muss angegeben werden, auch wenn er normalerweise von der Datenbank generiert wird. Es wird verwendet, um Datenänderungen zwischen Migrationen zu erkennen.
* Zuvor Seeding Daten werden entfernt, wenn der Primärschlüssel in irgendeiner Weise geändert wird.

Daher ist diese Funktion besonders nützlich für statische Daten, die sich außerhalb der Migrationen nicht ändern sollten, und ist nicht von anderen in der Datenbank abhängig, z. b. Postleitzahlen.

Wenn in Ihrem Szenario eine der folgenden Szenarien enthalten ist, wird empfohlen, die im letzten Abschnitt beschriebene benutzerdefinierte Initialisierungs Logik zu verwenden:

* Temporäre Daten für Tests
* Daten, die vom Daten Bank Status abhängen
* Daten, für die Schlüsselwerte benötigt werden, die von der Datenbank generiert werden müssen, einschließlich der Entitäten, die Alternative Schlüssel als Identität verwenden
* Daten, die eine benutzerdefinierte Transformation erfordern (die nicht durch [Wert Konvertierungen](xref:core/modeling/value-conversions)behandelt wird), z. b. ein Kennwort-Hashwert
* Daten, für die Aufrufe externer API erforderlich sind, z. b. ASP.net Core Identitäts Rollen und Benutzer Erstellung

## <a name="manual-migration-customization"></a>Anpassung der manuellen Migration

Beim Hinzufügen einer Migration werden die Änderungen an den mit angegebenen Daten `HasData` in Aufrufe von `InsertData()` , und transformiert `UpdateData()` `DeleteData()` . Eine Möglichkeit, einige der Einschränkungen von `HasData` zu umgehen, besteht darin, diese Aufrufe oder [benutzerdefinierten Vorgänge](xref:core/managing-schemas/migrations/operations) stattdessen manuell zur Migration hinzuzufügen.

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a>Benutzerdefinierte Initialisierungs Logik

Eine einfache und leistungsfähige Methode zum Durchführen von datenseeding ist die Verwendung von, [`DbContext.SaveChanges()`](xref:core/saving/index) bevor die Haupt Anwendungslogik mit der Ausführung beginnt.

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> Der Seeding Code sollte nicht Teil der normalen App-Ausführung sein, da dies zu Parallelitäts Problemen führen kann, wenn mehrere Instanzen ausgeführt werden und die APP ebenfalls über die Berechtigung zum Ändern des Datenbankschemas verfügen würde.

Abhängig von den Einschränkungen der Bereitstellung kann der Initialisierungs Code auf unterschiedliche Weise ausgeführt werden:

* Lokales Ausführen der Initialisierungs-App
* Stellen Sie die Initialisierungs-App mit der Haupt-App bereit, indem Sie die Initialisierungs Routine aufrufen und die Initialisierungs-App deaktivieren oder entfernen.

Dies kann normalerweise mithilfe von [Veröffentlichungs Profilen](/aspnet/core/host-and-deploy/visual-studio-publish-profiles)automatisiert werden.
