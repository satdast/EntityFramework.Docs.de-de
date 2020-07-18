---
title: SQLite-Datenbankanbieter-Einschränkungen-EF Core
author: bricelam
ms.date: 07/16/2020
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 393f5e80ce2e11dcb11c2048e06effa27e48dc13
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451228"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>SQLite EF Core-Datenbank-Anbieter-Einschränkungen

Der SQLite-Anbieter hat eine Reihe von Migrations Einschränkungen. Die meisten dieser Einschränkungen sind das Ergebnis von Einschränkungen in der zugrunde liegenden SQLite-Datenbank-Engine und nicht spezifisch für EF.

## <a name="modeling-limitations"></a>Modellierungseinschränkungen

Die gemeinsame relationale Bibliothek (die von Entity Framework relationalen Datenbankanbietern gemeinsam verwendet wird) definiert APIs zum Modellieren von Konzepten, die für die meisten relationalen Daten Bank Engines Einige dieser Konzepte werden vom SQLite-Anbieter nicht unterstützt.

* Schemas
* Sequenzen

## <a name="query-limitations"></a>Abfrage Einschränkungen

SQLite unterstützt die folgenden Datentypen nicht nativ. EF Core können Werte dieser Typen lesen und schreiben, und Abfragen auf Gleichheit ( `where e.Property == value` ) werden ebenfalls unterstützt. Für andere Vorgänge, wie z. b. Vergleich und Reihenfolge, ist jedoch eine Auswertung auf dem Client erforderlich.

* DateTimeOffset
* Decimal
* TimeSpan
* UInt64

Anstelle von `DateTimeOffset` wird empfohlen, DateTime-Werte zu verwenden. Wenn Sie mehrere Zeitzonen verarbeiten, empfiehlt es sich, die Werte vor dem Speichern in die UTC zu umrechnen und dann wieder in die entsprechende Zeitzone umzuwandeln.

Der- `Decimal` Typ bietet eine hohe Genauigkeit. Wenn Sie diese Genauigkeits Stufe nicht benötigen, empfiehlt sich stattdessen die Verwendung von Double. Sie können einen [Wert Konverter](../../modeling/value-conversions.md) verwenden, um in ihren Klassen weiterhin Decimal zu verwenden.

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a>Migrations Einschränkungen

Die SQLite-Datenbank-Engine unterstützt keine Reihe von Schema Vorgängen, die von den meisten anderen relationalen Datenbanken unterstützt werden. Wenn Sie versuchen, einen der nicht unterstützten Vorgänge auf eine SQLite-Datenbank anzuwenden, `NotSupportedException` wird eine ausgelöst.

Es wird versucht, eine Neuerstellung auszuführen, um bestimmte Vorgänge auszuführen. Neubuilds sind nur für Daten Bank Artefakte möglich, die Teil Ihres EF Core Modells sind. Wenn ein Daten Bank Element nicht Teil des Modells ist (z. b., wenn es in einer Migration manuell erstellt wurde), `NotSupportedException` wird immer noch eine ausgelöst.

| Vorgang            | Unterstützt?  | Erfordert Version |
|:---------------------|:------------|:-----------------|
| Addcheckeinschränkung   | ✔ (neu erstellen) | 5.0              |
| AddColumn            | ✔           | 1.0              |
| Addfremdnkey        | ✔ (neu erstellen) | 5.0              |
| Addprimarykey        | ✔ (neu erstellen) | 5.0              |
| AddUniqueConstraint  | ✔ (neu erstellen) | 5.0              |
| AlterColumn          | ✔ (neu erstellen) | 5.0              |
| CreateIndex          | ✔           | 1.0              |
| CreateTable          | ✔           | 1.0              |
| Dropcheck-Einschränkung  | ✔ (neu erstellen) | 5.0              |
| DropColumn           | ✔ (neu erstellen) | 5.0              |
| Dropfremdnkey       | ✔ (neu erstellen) | 5.0              |
| DropIndex            | ✔           | 1.0              |
| Dropprimarykey       | ✔ (neu erstellen) | 5.0              |
| DropTable            | ✔           | 1.0              |
| Dropuniqueconstraint | ✔ (neu erstellen) | 5.0              |
| Renamecolumn         | ✔           | 2.2.2            |
| Renameingedex          | ✔ (neu erstellen) | 2.1              |
| RenameTable          | ✔           | 1.0              |
| Ensureschema         | ✔ (No-OP)   | 2.0              |
| DropSchema           | ✔ (No-OP)   | 2.0              |
| Einfügen               | ✔           | 2.0              |
| Aktualisieren               | ✔           | 2.0              |
| Löschen               | ✔           | 2.0              |

## <a name="migrations-limitations-workaround"></a>Problem Umgehung der Migrations Einschränkungen

Sie können einige dieser Einschränkungen umgehen, indem Sie Code in ihren Migrationen manuell schreiben, um eine Neuerstellung durchzuführen. Tabellen neubuilds umfassen das Erstellen einer neuen Tabelle, das Kopieren von Daten in die neue Tabelle, das Löschen der alten Tabelle und das Umbenennen der neuen Tabelle. Sie müssen die-Methode verwenden `Sql(string)` , um einige dieser Schritte auszuführen.

Weitere Informationen finden Sie unter [vornehmen anderer Arten von Tabellen Schema Änderungen](https://sqlite.org/lang_altertable.html#otheralter) in der SQLite-Dokumentation.
