---
title: Schlüssellose Entitätstypen - EF Core
description: Konfigurieren schlüsselloser Entitätstypen mithilfe von Entity Framework Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 496e1e8983ba2d5e15dbee02607ea3f2c861503e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434213"
---
# <a name="keyless-entity-types"></a>Schlüssellose Entitätstypen

> [!NOTE]
> Diese Funktion wurde in EF Core 2.1 unter dem Namen der Abfragetypen hinzugefügt. In EF Core 3.0 wurde das Konzept in schlüssellose Entitätstypen umbenannt.

Zusätzlich zu den regulären Entitätstypen kann ein EF Core-Modell _schlüssellose Entitätstypen_enthalten, die zum Ausführen von Datenbankabfragen für Daten verwendet werden können, die keine Schlüsselwerte enthalten.

## <a name="defining-keyless-entity-types"></a>Definieren von Schlüssellosen Entitätstypen

Schlüssellose Entitätstypen können entweder mit der Datenanmerkung oder der Fluent-API definiert werden:

### <a name="data-annotations"></a>[Datenanmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a>Merkmale ohne Schlüsselentitätstypen

Schlüssellose Entitätstypen unterstützen viele der gleichen Zuordnungsfunktionen wie normale Entitätstypen, z. B. Vererbungszuordnung und Navigationseigenschaften. In relationalen Speichern können sie die Zieldatenbankobjekte und -spalten über fließende API-Methoden oder Datenanmerkungen konfigurieren.

Sie unterscheiden sich jedoch von regulären Entitätstypen dadurch, dass sie:

- Es kann kein Schlüssel definiert werden.
- Werden nie nach Änderungen im _DbContext_ nachverfolgt und daher nie in die Datenbank eingefügt, aktualisiert oder gelöscht.
- Werden nie durch Konvention entdeckt.
- Unterstützen Sie nur eine Teilmenge der Navigationszuordnungsfunktionen, insbesondere:
  - Sie dürfen niemals als wichtigstes Ende einer Beziehung fungieren.
  - Sie verfügen möglicherweise nicht über Navigationsgeräte an eigene Unternehmen.
  - Sie können nur Referenznavigationseigenschaften enthalten, die auf reguläre Entitäten verweisen.
  - Entitäten können keine Navigationseigenschaften für schlüssellose Entitätstypen enthalten.
- Sie müssen mit einer `[Keyless]` Datenanmerkung oder `.HasNoKey()` einem Methodenaufruf konfiguriert werden.
- Kann einer _definierenden Abfrage_zugeordnet werden. Eine definierende Abfrage ist eine Abfrage, die im Modell deklariert wird und als Datenquelle für einen schlüssellosen Entitätstyp fungiert.

## <a name="usage-scenarios"></a>Verwendungsszenarios

Einige der wichtigsten Verwendungsszenarien für schlüssellose Entitätstypen sind:

- Dient als Rückgabetyp für [unformatierte SQL-Abfragen](xref:core/querying/raw-sql).
- Zuordnen zu Datenbankansichten, die keinen Primärschlüssel enthalten.
- Zuordnung zu Tabellen, für die kein Primärschlüssel definiert ist.
- Zuordnen zu Abfragen, die im Modell definiert sind.

## <a name="mapping-to-database-objects"></a>Zuordnen zu Datenbankobjekten

Das Zuordnen eines schlüssellosen Entitätstyps `ToTable` zu `ToView` einem Datenbankobjekt wird mithilfe der oder fließenden API erreicht. Aus der Sicht von EF Core ist das in dieser Methode angegebene Datenbankobjekt eine _Ansicht_, was bedeutet, dass es als schreibgeschützte Abfragequelle behandelt wird und nicht das Ziel von Aktualisierungs-, Einfüge- oder Löschvorgängen sein kann. Dies bedeutet jedoch nicht, dass das Datenbankobjekt tatsächlich als Datenbankansicht erforderlich ist. Alternativ kann es sich um eine Datenbanktabelle handelt, die als schreibgeschützt behandelt wird. Umgekehrt geht EF Core bei regulären Entitätstypen davon `ToTable` aus, dass ein in der Methode angegebenes Datenbankobjekt als _Tabelle_behandelt werden kann, was bedeutet, dass es als Abfragequelle verwendet werden kann, aber auch durch Aktualisierungs-, Lösch- und Einfügevorgänge ausgerichtet werden kann. In der Tat können Sie den Namen `ToTable` einer Datenbankansicht in angeben, und alles sollte einwandfrei funktionieren, solange die Ansicht so konfiguriert ist, dass sie in der Datenbank aufrüstbar ist.

> [!NOTE]
> `ToView`geht davon aus, dass das Objekt bereits in der Datenbank vorhanden ist und nicht durch Migrationen erstellt wird.

## <a name="example"></a>Beispiel

Das folgende Beispiel zeigt, wie schlüssellose Entitätstypen zum Abfragen einer Datenbankansicht verwendet werden.

> [!TIP]
> Sie können das [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) dieses Artikels auf GitHub anzeigen.

Zuerst definieren wir ein einfaches Blog- und Post-Modell:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

Als Nächstes definieren wir eine einfache Datenbankansicht, die es uns ermöglicht, die Anzahl der Beiträge abzufragen, die jedem Blog zugeordnet sind:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

Als Nächstes definieren wir eine Klasse, die das Ergebnis aus der Datenbankansicht enthält:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

Als Nächstes konfigurieren wir den schlüssellosen Entitätstyp in _OnModelCreating_ mithilfe der `HasNoKey` API.
Wir verwenden eine fließende Konfigurations-API, um die Zuordnung für den schlüssellosen Entitätstyp zu konfigurieren:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

Als Nächstes konfigurieren `DbContext` wir `DbSet<T>`die, um Folgendes einzuschließen:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

Schließlich können wir die Datenbankansicht standardmäßig abfragen:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> Beachten Sie, dass wir auch eine Abfrageeigenschaft auf Kontextebene (DbSet) definiert haben, die als Stamm für Abfragen für diesen Typ fungiert.
