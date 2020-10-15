---
title: SQLite-Datenbankanbieter-Einschränkungen-EF Core
description: Einschränkungen für den Entity Framework Core SQLite-Datenbankanbieter im Vergleich zu anderen Anbietern
author: bricelam
ms.date: 09/24/2020
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 3d696474d401e8fd6c26a78067d292f0bb97a457
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062736"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>SQLite EF Core-Datenbank-Anbieter-Einschränkungen

Der SQLite-Anbieter hat eine Reihe von Migrations Einschränkungen. Die meisten dieser Einschränkungen sind das Ergebnis von Einschränkungen in der zugrunde liegenden SQLite-Datenbank-Engine und nicht spezifisch für EF.

## <a name="modeling-limitations"></a>Modellierungseinschränkungen

Die gemeinsame relationale Bibliothek (die von Entity Framework relationalen Datenbankanbietern gemeinsam verwendet wird) definiert APIs zum Modellieren von Konzepten, die für die meisten relationalen Daten Bank Engines Einige dieser Konzepte werden vom SQLite-Anbieter nicht unterstützt.

* Schemas
* Sequenzen

## <a name="query-limitations"></a>Abfrageeinschränkungen

SQLite unterstützt die folgenden Datentypen nicht nativ. EF Core können Werte dieser Typen lesen und schreiben, und Abfragen auf Gleichheit ( `where e.Property == value` ) werden ebenfalls unterstützt. Für andere Vorgänge, wie z. b. Vergleich und Reihenfolge, ist jedoch eine Auswertung auf dem Client erforderlich.

* DateTimeOffset
* Decimal
* TimeSpan
* UInt64

Anstelle von `DateTimeOffset` wird empfohlen, DateTime-Werte zu verwenden. Wenn Sie mehrere Zeitzonen verarbeiten, empfiehlt es sich, die Werte vor dem Speichern in die UTC zu umrechnen und dann wieder in die entsprechende Zeitzone umzuwandeln.

Der- `Decimal` Typ bietet eine hohe Genauigkeit. Wenn Sie diese Genauigkeits Stufe nicht benötigen, empfiehlt sich stattdessen die Verwendung von Double. Sie können einen [Wert Konverter](xref:core/modeling/value-conversions) verwenden, um in ihren Klassen weiterhin Decimal zu verwenden.

```csharp
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
| AddColumn            | ✔           |                  |
| Addfremdnkey        | ✔ (neu erstellen) | 5.0              |
| Addprimarykey        | ✔ (neu erstellen) | 5.0              |
| AddUniqueConstraint  | ✔ (neu erstellen) | 5.0              |
| AlterColumn          | ✔ (neu erstellen) | 5.0              |
| CreateIndex          | ✔           |                  |
| CreateTable          | ✔           |                  |
| Dropcheck-Einschränkung  | ✔ (neu erstellen) | 5.0              |
| DropColumn           | ✔ (neu erstellen) | 5.0              |
| Dropfremdnkey       | ✔ (neu erstellen) | 5.0              |
| DropIndex            | ✔           |                  |
| Dropprimarykey       | ✔ (neu erstellen) | 5.0              |
| DropTable            | ✔           |                  |
| Dropuniqueconstraint | ✔ (neu erstellen) | 5.0              |
| Renamecolumn         | ✔           | 2.2              |
| Renameingedex          | ✔ (neu erstellen) |                  |
| RenameTable          | ✔           |                  |
| Ensureschema         | ✔ (No-OP)   |                  |
| DropSchema           | ✔ (No-OP)   |                  |
| Einfügen               | ✔           |                  |
| Aktualisieren               | ✔           |                  |
| Löschen               | ✔           |                  |

### <a name="migrations-limitations-workaround"></a>Problem Umgehung der Migrations Einschränkungen

Sie können einige dieser Einschränkungen umgehen, indem Sie Code in ihren Migrationen manuell schreiben, um eine Neuerstellung durchzuführen. Tabellen neubuilds umfassen das Erstellen einer neuen Tabelle, das Kopieren von Daten in die neue Tabelle, das Löschen der alten Tabelle und das Umbenennen der neuen Tabelle. Sie müssen die-Methode verwenden `Sql(string)` , um einige dieser Schritte auszuführen.

Weitere Informationen finden Sie unter [vornehmen anderer Arten von Tabellen Schema Änderungen](https://sqlite.org/lang_altertable.html#otheralter) in der SQLite-Dokumentation.

### <a name="idempotent-script-limitations"></a>Einschränkungen für idempotente Skripts

Anders als andere Datenbanken enthält SQLite keine prozedurale Sprache. Aus diesem Grund gibt es keine Möglichkeit, die von den idempotenten Migrations Skripts benötigte if-then-Logik zu generieren.

Wenn Sie wissen, dass die letzte Migration auf eine Datenbank angewendet wurde, können Sie ein Skript aus dieser Migration zur neuesten Migration generieren.

```dotnetcli
dotnet ef migrations script CurrentMigration
```

Andernfalls wird empfohlen, `dotnet ef database update` zum Anwenden von Migrationen zu verwenden. Ab EF Core 5,0 können Sie die Datenbankdatei angeben, wenn Sie den Befehl ausführen.

```dotnetcli
dotnet ef database update --connection "Data Source=My.db"
```

## <a name="see-also"></a>Weitere Informationen

* [Microsoft. Data. sqlite Async-Einschränkungen](/dotnet/standard/data/sqlite/async)
* [Microsoft. Data. sqlite ADO.net Einschränkungen](/dotnet/standard/data/sqlite/adonet-limitations)
