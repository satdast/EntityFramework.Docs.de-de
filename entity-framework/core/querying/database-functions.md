---
title: 'Datenbankfunktionen: EF Core'
description: Informationen zu Datenbankfunktionen, die bei der Übersetzung von EF Core-Abfragen unterstützt werden
author: smitpatel
ms.date: 11/10/2020
uid: core/querying/database-functions
ms.openlocfilehash: 34ced2a602168f6ed84763c046ef581cb87026e8
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503781"
---
# <a name="database-functions"></a>Datenbankfunktionen

Datenbankfunktionen sind die Datenbankentsprechung von [C#-Methoden](/dotnet/csharp/programming-guide/classes-and-structs/methods). Eine Datenbankfunktion kann mit null oder mehr Parametern aufgerufen werden und berechnet das Ergebnis auf Grundlage der Parameterwerte. Die meisten Datenbanken, die SQL für Abfragen verwenden, unterstützen Datenbankfunktionen. Deshalb ermöglicht SQL, die bei der Übersetzung von EF Core-Abfragen generiert wird, auch das Aufrufen von Datenbankfunktionen. C#-Methoden müssen nicht zwangsläufig in Datenbankfunktionen in EF Core übersetzt werden.

- Eine C#-Methode hat möglicherweise keine entsprechende Datenbankfunktion.
  - Die <xref:System.String.IsNullOrEmpty%2A?displayProperty=nameWithType>-Methode wird in eine Prüfung auf NULL und einen Vergleich mit einer leeren Zeichenfolge in der Datenbank und nicht in eine Funktion übersetzt.
  - Die <xref:System.String.Equals(System.String,System.StringComparison)?displayProperty=nameWithType>-Methode hat keine Datenbankentsprechung, da Zeichenfolgenvergleiche in einer Datenbank nicht einfach dargestellt oder imitiert werden können.
- Eine Datenbankfunktion hat möglicherweise keine entsprechende C#-Methode. Der Operator `??` in C#, der über keine Methoden verfügt, wird in der Datenbank in die Funktion `COALESCE` übersetzt.

## <a name="types-of-database-functions"></a>Typen von Datenbankfunktionen

Die SQL-Generierung durch EF Core unterstützt eine Teilmenge von Funktionen, die in Datenbanken verwendet werden können. Diese Einschränkung ergibt sich aus der Möglichkeit, eine Abfrage in LINQ für die angegebene Datenbankfunktion darzustellen. Darüber hinaus unterstützt jede Datenbank unterschiedliche Datenbankfunktionen, weshalb EF Core eine gemeinsame Teilmenge bietet. Ein Datenbankanbieter kann die SQL-Generierung durch EF Core erweitern, um weitere Muster zu unterstützen. Es folgen die Arten von Datenbankfunktionen, die EF Core unterstützt und eindeutig identifiziert. Diese Begriffe tragen auch zum Verständnis bei, welche Übersetzungen in EF Core-Anbieter integriert sind.

### <a name="built-in-vs-user-defined-functions"></a>Vergleich von integrierten und benutzerdefinierten Funktionen

Integrierte Funktionen sind in der Datenbank vordefiniert, während benutzerdefinierte Funktionen explizit vom Benutzer in der Datenbank definiert werden. Wenn EF Core Abfragen zur Nutzung von Datenbankfunktionen übersetzt, werden integrierte Funktionen verwendet, um sicherzustellen, dass die Funktion stets in der Datenbank verfügbar ist. Die Trennung integrierter Funktionen ist in einigen Datenbanken notwendig, um SQL ordnungsgemäß zu generieren. Beispielsweise verlangt SqlServer, dass jede benutzerdefinierte Funktion mit einem durch das Schema qualifizierten Namen aufgerufen wird. Integrierte Funktionen in SqlServer haben jedoch kein Schema. PostgreSQL definiert integrierte Funktionen im Schema `public`, die jedoch mit vom Schema qualifizierten Namen aufgerufen werden können.

### <a name="aggregate-vs-scalar-vs-table-valued-functions"></a>Vergleich von Aggregat-, Skalar- und Tabellenwertfunktionen

- Skalare Funktionen verwenden skalare Werte wie z. B. ganze Zahlen oder Zeichenfolgen als Parameter und geben als Ergebnis einen skalaren Wert zurück. Skalare Funktionen können überall in SQL dort verwendet werden, wo ein skalarer Wert übergeben werden kann.
- Aggregatfunktionen verwenden einen Datenstrom aus skalaren Werten als Parameter und geben als Ergebnis einen skalaren Wert zurück. Aggregatfunktionen werden auf das gesamte Resultset der Abfrage oder auf eine Gruppe von Werten angewendet, die durch Anwenden des Operators `GROUP BY` generiert wurden.
- Tabellenwertfunktionen verwenden skalare Werte als Parameter und geben als Ergebnis einen Datenstrom mit Zeilen zurück. Tabellenwertfunktionen werden in der `FROM`-Klausel als Tabellenquelle verwendet.

### <a name="niladic-functions"></a>NILADIC-Funktionen

NILADIC-Funktionen sind spezielle Datenbankfunktionen, die keine Parameter aufweisen und ohne Klammer aufgerufen werden müssen. Sie sind vergleichbar mit dem Zugriff auf Eigenschaften/Felder in einer Instanz in C#. NILADIC-Funktionen unterscheiden sich von parameterlosen Funktionen, da letztere leere Klammern erfordern. Es gibt keinen speziellen Namen für Datenbankfunktionen, die stets Klammern erfordern. Eine weitere Teilmenge von Datenbankfunktionen, die auf der Parameteranzahl basiert, sind variadische Funktionen. Variadische Funktionen können beim Aufrufen unterschiedlich viele Parameter aufweisen.

## <a name="database-function-mappings-in-ef-core"></a>Zuordnungen von Datenbankfunktion in EF Core

EF Core unterstützt drei verschiedene Arten der Zuordnung zwischen C#- und Datenbankfunktionen.

### <a name="built-in-function-mapping"></a>Zuordnung integrierter Funktionen

Standardmäßig bieten EF Core-Anbieter Zuordnungen für verschiedene integrierte Funktionen über primitive Typen. <xref:System.String.ToLower?displayProperty=nameWithType> wird in SqlServer beispielsweise in `LOWER` übersetzt. Diese Funktionalität ermöglicht Benutzern das reibungslose Schreiben von Abfragen in LINQ. Wir liefern in der Regel eine Übersetzung in der Datenbank, die das gleiche Ergebnis wie die C#-Funktion auf Clientseite liefert. Um dies zu erreichen, kann die tatsächliche Übersetzung mitunter etwas komplizierter sein als eine Datenbankfunktion. In einigen Szenarien bieten wir auch die am besten geeignete Übersetzung anstelle einer passenden C#-Semantik. Dasselbe Feature wird auch verwendet, um einheitliche Übersetzungen für einige der C#-Memberzugriffe bereitzustellen. <xref:System.String.Length?displayProperty=nameWithType> wird in SqlServer beispielsweise in `LEN` übersetzt. Neben Anbietern können auch Plug-In-Autoren zusätzliche Übersetzungen hinzufügen. Diese Erweiterbarkeit ist nützlich, wenn Plug-Ins Unterstützung für weitere Typen als primitive Typen hinzufügen und Methoden mit deren Hilfe übersetzen möchten.

### <a name="effunctions-mapping"></a>Zuordnung von EF.Functions

Da nicht alle Datenbankfunktionen entsprechende C#-Funktionen haben, verfügen EF Core-Anbieter über spezielle C#-Methoden, um bestimmte Datenbankfunktionen aufzurufen. Diese Methoden werden als Erweiterungsmethoden über `EF.Functions` definiert, die in LINQ-Abfragen verwendet werden sollen. Diese Methoden sind anbieterspezifisch, da sie eng mit bestimmten Datenbankfunktionen verknüpft sind. Daher funktioniert eine Methode, die bei einem Anbieter erfolgreich ist, wahrscheinlich bei keinem anderen Anbieter. Da die Absicht dieser Methoden darin besteht, in der übersetzten Abfrage eine Datenbankfunktion aufzurufen, führt der Versuch, sie auf dem Client auszuwerten, zu einer Ausnahme.

### <a name="user-defined-function-mapping"></a>Zuordnung benutzerdefinierter Funktionen

Abgesehen von Zuordnungen, die von EF Core-Anbietern bereitgestellt werden, können Benutzer auch benutzerdefinierte Zuordnungen definieren. Eine benutzerdefinierte Zuordnung erweitert die Übersetzung von Abfragen entsprechend den Anforderungen des Benutzers. Diese Funktionalität ist nützlich, wenn es in der Datenbank benutzerdefinierte Funktionen gibt, die der Benutzer über seine LINQ-Abfrage aufrufen möchte.

## <a name="see-also"></a>Siehe auch

- [Zuordnung von in SqlServer integrierten Funktionen](xref:core/providers/sql-server/functions)
- [Zuordnung von in Sqlite integrierten Funktionen](xref:core/providers/sqlite/functions)
- [Zuordnung von in Cosmos integrierten Funktionen](xref:core/providers/cosmos/functions)
