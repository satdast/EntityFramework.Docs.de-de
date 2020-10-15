---
title: Typen von Keyless-Entitäten-EF Core
description: Konfigurieren von schlüssellosen Entitäts Typen mithilfe von Entity Framework Core
author: AndriySvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: cb4ce44526ada77e37eb4dceb9986a670ea3656b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063802"
---
# <a name="keyless-entity-types"></a>Schlüssellose Entitätstypen

> [!NOTE]
> Diese Funktion wurde unter dem Namen der Abfrage Typen hinzugefügt. In EF Core 3,0 wurde das Konzept in schlüssellose Entitäts Typen umbenannt. Die `[Keyless]` Daten Anmerkung wurde in efcore 5,0 verfügbar.

Zusätzlich zu regulären Entitäts Typen kann ein EF Core Modell _Schlüssel-Entitäts Typen_enthalten, die verwendet werden können, um Datenbankabfragen für Daten auszuführen, die keine Schlüsselwerte enthalten.

## <a name="defining-keyless-entity-types"></a>Definieren von schlüssellosen Entitäts Typen

Schlüssellose Entitäts Typen können entweder mithilfe der Daten Anmerkung oder der fließenden API definiert werden:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a>Merkmale von Keyless-Entitäts Typen

Schlüssellose Entitäts Typen unterstützen viele der gleichen Zuordnungs Funktionen wie reguläre Entitäts Typen wie Vererbungs Zuordnung und Navigations Eigenschaften. In relationalen speichern können Sie die Ziel Datenbankobjekte und-Spalten überfließende API-Methoden oder Daten Anmerkungen konfigurieren.

Sie unterscheiden sich jedoch von regulären Entitäts Typen darin, dass Sie:

- Ein Schlüssel kann nicht definiert werden.
- Werden nie nach Änderungen im _dbcontext_ nachverfolgt und daher nie in der Datenbank eingefügt, aktualisiert oder gelöscht.
- Werden nie nach Konvention ermittelt.
- Unterstützt nur eine Teilmenge der Navigations Zuordnungsfunktionen, insbesondere:
  - Sie werden möglicherweise nie als Prinzipal Ende einer Beziehung fungieren.
  - Sie haben möglicherweise keine Navigation zu besitzende Entitäten.
  - Sie können nur Verweis Navigations Eigenschaften enthalten, die auf reguläre Entitäten verweisen.
  - Entitäten können keine Navigations Eigenschaften für Entitäts Typen ohne Schlüssel enthalten.
- Muss mit einer `[Keyless]` Daten Anmerkung oder einem Methoden Befehl konfiguriert werden `.HasNoKey()` .
- Kann einer _definierenden Abfrage_zugeordnet werden. Eine definierende Abfrage ist eine im Modell deklarierte Abfrage, die als Datenquelle für einen schlüssellosen Entitätstyp fungiert.

## <a name="usage-scenarios"></a>Verwendungsszenarios

Einige der wichtigsten Verwendungs Szenarien für Typen von schlüssellosen Entitäten sind:

- Fungieren als Rückgabetyp für unformatierte [SQL-Abfragen](xref:core/querying/raw-sql).
- Zuordnung zu Daten Bank Sichten, die keinen Primärschlüssel enthalten.
- Zuordnung zu Tabellen, für die kein Primärschlüssel definiert ist.
- Zuordnung zu Abfragen, die im Modell definiert sind.

## <a name="mapping-to-database-objects"></a>Zuordnung zu Datenbankobjekten

Die Zuordnung eines Entitäts Typs mit einer anderen Tastatur zu einem Datenbankobjekt erfolgt mithilfe der-oder-über `ToTable` `ToView` flüssigen API. Aus Sicht der EF Core ist das in dieser Methode angegebene Datenbankobjekt eine _Sicht_. Dies bedeutet, dass es als schreibgeschützte Abfrage Quelle behandelt wird und nicht das Ziel von Aktualisierungs-, Einfüge-oder Lösch Vorgängen sein kann. Dies bedeutet jedoch nicht, dass das Datenbankobjekt tatsächlich eine Daten Bank Sicht sein muss. Alternativ kann es sich um eine Datenbanktabelle handeln, die als schreibgeschützt behandelt wird. Im Gegensatz dazu geht EF Core bei regulären Entitäts Typen davon aus, dass ein in der-Methode angegebenes Datenbankobjekt `ToTable` als _Tabelle_behandelt werden kann. Dies bedeutet, dass es als Abfrage Quelle, aber auch als Ziel für Update-, DELETE-und INSERT-Vorgänge verwendet werden kann. In der Tat können Sie den Namen einer Daten Bank Sicht in angeben, `ToTable` und alles sollte einwandfrei funktionieren, solange die Sicht so konfiguriert ist, dass Sie für die Datenbank aktualisierbar ist.

> [!NOTE]
> `ToView` geht davon aus, dass das Objekt bereits in der Datenbank vorhanden ist, und wird nicht durch Migrationen erstellt.

## <a name="example"></a>Beispiel

Das folgende Beispiel zeigt, wie Sie schlüssellose Entitäts Typen verwenden, um eine Daten Bank Sicht abzufragen.

> [!TIP]
> Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) finden Sie auf GitHub.

Zunächst definieren wir einen einfachen Blog und ein Post-Modell:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

Als nächstes definieren wir eine einfache Daten Bank Ansicht, die es uns ermöglicht, die Anzahl von Beiträgen abzufragen, die mit den einzelnen Blogs verknüpft sind:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

Als nächstes definieren wir eine Klasse, die das Ergebnis aus der Daten Bank Sicht enthält:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

Als Nächstes konfigurieren wir den Entitätstyp "Schlüssel" in " _onmodelcreating_ " mithilfe der `HasNoKey` API.
Wir verwenden die fließende Konfigurations-API, um die Zuordnung für den Entitätstyp "Schlüssel" zu konfigurieren:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

Als Nächstes konfigurieren wir den so, dass er `DbContext` Folgendes einschließt `DbSet<T>` :

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

Schließlich können wir die Daten Bank Sicht standardmäßig Abfragen:

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> Beachten Sie, dass wir auch eine Kontext Stufen-Abfrage Eigenschaft (dbset) definiert haben, die als Stamm für Abfragen dieses Typs fungiert.
