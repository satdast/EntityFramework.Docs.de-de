---
title: 'Migrationsübersicht: EF Core'
description: Übersicht über die Verwendung von Migrationen zum Verwalten von Datenbankschemas mit Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: f1197fb869c33c34672d20e9b727cd187c9c5601
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619476"
---
# <a name="migrations-overview"></a>Migrationsübersicht

In realen Projekten ändern sich die Datenmodelle mit der Implementierung von Funktionen: Neue Entitäten oder Eigenschaften werden hinzugefügt oder entfernt, und Datenbankschemas müssen entsprechend geändert werden, um mit der Anwendung synchron zu bleiben. Das Migrations-Feature in EF Core bietet einen Weg, das Datenbankschema inkrementell zu aktualisieren, um es mit dem Datenmodell der Anwendung synchron zu halten und zugleich die vorhandenen Daten in der Datenbank beizubehalten.

Auf hoher Ebene funktionieren Migrationen wie folgt:

* Wenn eine Datenmodelländerung eingeführt wird, verwendet der Entwickler EF Core-Tools, um eine entsprechende Migration hinzuzufügen, die die Aktualisierungen beschreibt, die erforderlich sind, um das Datenbankschema synchron zu halten. EF Core vergleicht das aktuelle Modell mit einer Momentaufnahme des alten Modells, um die Unterschiede zu ermitteln, und generiert Migrationsquelldateien. Die Dateien können in der Quellcodeverwaltung Ihres Projekts wie jede andere Quelldatei nachverfolgt werden.
* Nachdem eine neue Migration generiert wurde, kann Sie auf verschiedene Weise auf eine Datenbank angewendet werden. EF Core zeichnet alle angewendeten Migrationen in einer speziellen Verlaufstabelle auf und ermöglicht es Ihnen zu erkennen, welche Migrationen angewendet wurden und welche nicht.

Der Rest dieser Seite ist eine schrittweise Anleitung für die Verwendung von Migrationen für Einsteiger. Weitere ausführliche Informationen finden Sie auf den anderen Seiten in diesem Abschnitt.

## <a name="getting-started"></a>Erste Schritte

Angenommen, Sie haben ihre erste EF Core Anwendung abgeschlossen, die das folgende einfache Modell enthält:

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

Während der Entwicklung haben Sie vielleicht [Erstellen und Verwerfen von APIs](xref:core/managing-schemas/ensure-created) verwendet, um schnell zu iterieren und Ihr Modell nach Bedarf zu ändern. Jetzt aber, wo Ihre Anwendung in Produktion geht, benötigen Sie eine Möglichkeit, das Schema sicher zu entwickeln, ohne die gesamte Datenbank zu verwerfen.

### <a name="install-the-tools"></a>Installieren der Tools

Zunächst müssen Sie die [EF Core-Befehlszeilentools](xref:core/miscellaneous/cli/index) installieren:

* Im Allgemeinen wird empfohlen, die [.NET Core CLI-Tools](xref:core/miscellaneous/cli/dotnet) zu verwenden, die auf allen Plattformen funktionieren.
* Wenn Ihnen die Arbeit in Visual Studio angenehmer ist oder Sie Erfahrung mit EF6-Migrationen haben, können Sie auch die [Tools der Paket-Manager-Konsole](xref:core/miscellaneous/cli/powershell) verwenden.

### <a name="create-your-first-migration"></a>Erstellen Ihrer ersten Migration

Sie sind jetzt bereit, Ihre erste Migration hinzuzufügen! Weisen Sie EF Core an, eine Migration mit dem Namen **InitialCreate** zu erstellen:

#### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration InitialCreate
```

***

EF Core erstellt ein Verzeichnis namens **Migrations** in Ihrem Projekt und generiert einige Dateien. Es ist eine gute Idee, zu überprüfen, was genau EF Core generiert hat (und es möglicherweise zu ändern), aber wir überspringen dies vorerst.

### <a name="create-your-database-and-schema"></a>Erstellen der Datenbank und des Schemas

An diesem Punkt können Sie EF Ihre Datenbank und Ihr Schema aus der Migration erstellen lassen. Hierzu können Sie Folgendes verwenden:

#### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

Das ist alles: Ihre Anwendung ist bereit, mit Ihrer neuen Datenbank ausgeführt zu werden, und Sie mussten nicht eine einzige Zeile SQL-Code schreiben. Beachten Sie, dass diese Art der Anwendung von Migrationen ideal für die lokale Entwicklung ist, aber für Produktionsumgebungen weniger geeignet ist. Weitere Informationen finden Sie auf der Seite [Anwenden von Migrationen](xref:core/managing-schemas/migrations/applying).

### <a name="evolving-your-model"></a>Entwickeln Ihres Modells

Es sind einige Tage vergangen, und Sie werden gebeten, Ihren Blogs einen Zeitstempel für die Erstellung hinzuzufügen. Sie haben die erforderlichen Änderungen an Ihrer Anwendung vorgenommen, und Ihr Modell sieht jetzt so aus:

```c#
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

Ihr Modell und ihre Produktionsdatenbank sind jetzt nicht mehr synchron. Sie müssen dem Datenbankschema eine neue Spalte hinzufügen. Erstellen Sie dazu eine neue Migration:

#### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

Beachten Sie, dass wir Migrationen einen beschreibenden Namen geben, um den Projektverlauf später leichter verständlich zu machen.

Da es sich nicht um die erste Migration des Projekts handelt, vergleicht EF Core jetzt Ihr aktualisiertes Modell mit einer Momentaufnahme des alten Modells, bevor die Spalte hinzugefügt wurde. Die Modellmomentaufnahme ist eine der Dateien, die von EF Core generiert wird, wenn Sie eine Migration hinzufügen, und sie wird in die Quellcodeverwaltung eingecheckt. Basierend auf diesem Vergleich erkennt EF Core, dass eine Spalte hinzugefügt wurde, und fügt die entsprechende Migration hinzu.

Sie können Ihre Migration jetzt wie zuvor anwenden:

#### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```
#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Update-Database
```

***

Beachten Sie, dass EF dieses Mal erkennt, dass die Datenbank bereits vorhanden ist. Darüber hinaus wurde diese Tatsache bei der Anwendung unserer ersten Migration oben in einer speziellen Migrationsverlaufstabelle in Ihrer Datenbank aufgezeichnet. Dies ermöglicht es EF, automatisch nur die neue Migration anzuwenden.

### <a name="next-steps"></a>Nächste Schritte

Die Ausführungen oben waren nur eine kurze Einführung in Migrationen. Weitere Informationen zum [Verwalten von Migrationen](xref:core/managing-schemas/migrations/managing), [Anwenden von Migrationen](xref:core/managing-schemas/migrations/applying) und zu weiteren Aspekte finden Sie auf den anderen Dokumentationsseiten. Die [.NET Core CLI-Toolreferenz](xref:core/miscellaneous/cli/index) enthält ebenfalls nützliche Informationen zu den verschiedenen Befehlen.
