---
title: Verwenden von migrate.exe-EF6
description: Verwenden von migrate.exe in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/migrate-exe
ms.openlocfilehash: f57f56f7e3fe876c7265526bf6541e3c9e91ca7d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066368"
---
# <a name="using-migrateexe"></a>Verwenden von migrate.exe
Code First-Migrationen kann zum Aktualisieren einer Datenbank in Visual Studio verwendet werden, kann aber auch über das Befehlszeilen Tool migrate.exe ausgeführt werden. Auf dieser Seite erhalten Sie einen kurzen Überblick über die Verwendung von migrate.exe zum Ausführen von Migrationen für eine-Datenbank.

> [!NOTE]
> In diesem Artikel wird davon ausgegangen, dass Sie wissen, wie Code First-Migrationen in einfachen Szenarien verwendet wird. Andernfalls müssen Sie [Code First-Migrationen](xref:ef6/modeling/code-first/migrations/index) lesen, bevor Sie fortfahren.

## <a name="copy-migrateexe"></a>Kopieren migrate.exe

Wenn Sie Entity Framework mithilfe von nuget installieren, befindet sich migrate.exe im Ordner Tools des heruntergeladenen Pakets. In &lt; Projektordner &gt; \\ Paketen \\ EntityFramework. &lt; Versions &gt; \\ Tools

Wenn Sie migrate.exe haben, müssen Sie Sie an den Speicherort der Assembly kopieren, die ihre Migrationen enthält.

Wenn Ihre Anwendung auf .NET 4 und nicht auf 4,5 abzielt, müssen Sie die **Redirect.config** in den Speicherort kopieren und **migrate.exe.config**umbenennen. Dies bewirkt, dass migrate.exe die richtigen Bindungs Umleitungen abruft, um die Entity Framework Assembly suchen zu können.

| .NET 4.5                                      | .NET 4,0                                      |
|:----------------------------------------------|:----------------------------------------------|
| ![.NET 4,5-Dateien](~/ef6/media/net45files.png) | ![.NET 4,0-Dateien](~/ef6/media/net40files.png) |

> [!NOTE]
> migrate.exe unterstützt keine x64-Assemblys.

Nachdem Sie die migrate.exe in den richtigen Ordner verschoben haben, sollten Sie Sie verwenden können, um Migrationen für die Datenbank auszuführen. Das gesamte Hilfsprogramm ist für das Ausführen von Migrationen konzipiert. Es können keine Migrationen generiert oder ein SQL-Skript erstellt werden.

## <a name="see-options"></a>Siehe Optionen

``` console
Migrate.exe /?
```

Im obigen Beispiel wird die Hilfeseite angezeigt, die mit diesem Hilfsprogramm verknüpft ist. Beachten Sie, dass sich die EntityFramework.dll an demselben Speicherort befinden muss, den Sie migrate.exe ausführen, damit dies funktioniert.

## <a name="migrate-to-the-latest-migration"></a>Migrieren zur neuesten Migration

``` console
Migrate.exe MyMvcApplication.dll /startupConfigurationFile="..\\web.config"
```

Wenn Sie migrate.exe ausführen, ist die Assembly der einzige erforderliche Parameter, bei dem es sich um die Assembly handelt, die die Migrationen enthält, die Sie ausführen möchten. allerdings werden alle auf der Konvention basierenden Einstellungen verwendet, wenn Sie die Konfigurationsdatei nicht angeben.

## <a name="migrate-to-a-specific-migration"></a>Migrieren zu einer bestimmten Migration

``` console
Migrate.exe MyApp.exe /startupConfigurationFile="MyApp.exe.config" /targetMigration="AddTitle"
```

Wenn Sie Migrationen bis zu einer bestimmten Migration ausführen möchten, können Sie den Namen der Migration angeben. Dadurch werden alle vorherigen Migrationen nach Bedarf ausgeführt, bis die Migration durchgeführt wird.

## <a name="specify-working-directory"></a>Arbeitsverzeichnis angeben

``` console
Migrate.exe MyApp.exe /startupConfigurationFile="MyApp.exe.config" /startupDirectory="c:\\MyApp"
```

Wenn die Assembly über Abhängigkeiten verfügt oder Dateien relativ zum Arbeitsverzeichnis liest, müssen Sie startupdirectory festlegen.

## <a name="specify-migration-configuration-to-use"></a>Zu verwendende Migrations Konfiguration angeben

``` console
Migrate.exe MyAssembly CustomConfig /startupConfigurationFile="..\\web.config"
```

Wenn Sie über mehrere Migrations Konfigurations Klassen verfügen, die von dbmigrationconfiguration erben, müssen Sie angeben, welche für diese Ausführung verwendet werden soll. Diese Angabe wird angegeben, indem der optionale zweite Parameter ohne einen Switch wie oben angegeben wird.

## <a name="provide-connection-string"></a>Verbindungs Zeichenfolge angeben

``` console
Migrate.exe BlogDemo.dll /connectionString="Data Source=localhost;Initial Catalog=BlogDemo;Integrated Security=SSPI" /connectionProviderName="System.Data.SqlClient"
```

Wenn Sie in der Befehlszeile eine Verbindungs Zeichenfolge angeben möchten, müssen Sie auch den Anbieter Namen angeben. Wenn Sie den Anbieter Namen nicht angeben, wird eine Ausnahme ausgelöst.

## <a name="common-problems"></a>Häufige Probleme

| Fehlermeldung                                                                                                                                                                                                                                                                                                                      | Lösung                                                                                                                                                                                                                                                                                             |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Unbehandelte Ausnahme: System. IO. FileLoadException: die Datei oder Assembly "EntityFramework, Version = 5.0.0.0, Culture = neutral, PublicKeyToken = b77a5c561934e089" oder eine ihrer Abhängigkeiten konnte nicht geladen werden. Die Manifest-Definition der lokalisierte Assembly stimmt nicht mit dem Assemblyverweis. (Ausnahme von HRESULT: 0x80131040)         | Dies bedeutet in der Regel, dass Sie eine .NET 4-Anwendung ohne die Redirect.config-Datei ausführen. Sie müssen die Redirect.config an denselben Speicherort wie migrate.exe kopieren und in migrate.exe.config umbenennen.                                                                                       |
| Unbehandelte Ausnahme: System. IO. FileLoadException: die Datei oder Assembly "EntityFramework, Version = 4.4.0.0, Culture = neutral, PublicKeyToken = b77a5c561934e089" oder eine ihrer Abhängigkeiten konnte nicht geladen werden. Die Manifest-Definition der lokalisierte Assembly stimmt nicht mit dem Assemblyverweis. (Ausnahme von HRESULT: 0x80131040)          | Diese Ausnahme bedeutet, dass Sie eine .NET 4,5-Anwendung ausführen, bei der der Redirect.config in den migrate.exe Speicherort kopiert wurde. Wenn Ihre APP .NET 4,5 ist, muss die Konfigurationsdatei nicht mit den Umleitungen innerhalb von vorhanden sein. Löschen Sie die migrate.exe.config Datei.                                    |
| Fehler: die Datenbank kann nicht so aktualisiert werden, dass Sie dem aktuellen Modell entspricht, weil ausstehende Änderungen vorhanden sind und die automatische Migration deaktiviert ist. Schreiben Sie entweder die ausstehenden Modelländerungen in eine Code basierte Migration, oder aktivieren Sie die automatische Migration. Legen Sie dbmigrationsconfiguration. automaticmigrationsenabled auf "true" fest, um die automatische Migration zu aktivieren. | Dieser Fehler tritt auf, wenn eine Migration ausgeführt wird, wenn Sie keine Migration erstellt haben, um Änderungen am Modell zu bewältigen, und die Datenbank nicht mit dem Modell identisch ist. Wenn Sie einer Modell Klasse eine Eigenschaft hinzufügen und dann migrate.exe ausführen, ohne eine Migration zum Aktualisieren der Datenbank zu erstellen, ist ein Beispiel dafür. |
| Fehler: der Typ ist für den Member "System. Data. Entity. Migrationen. Design. toolingfacade + updaterunner, EntityFramework, Version = 5.0.0.0, Culture = neutral, PublicKeyToken = b77a5c561934e089" nicht aufgelöst.                                                                                                                                       | Dieser Fehler kann durch die Angabe eines falschen Start Verzeichnisses verursacht werden. Dabei muss es sich um den Speicherort der migrate.exe                                                                                                                                                                                      |
| Unbehandelte Ausnahme: System. NullReferenceException: der Objekt Verweis ist nicht auf eine Instanz eines Objekts festgelegt. <br/>   bei System. Data. Entity. Migrationen. Console. Program. Main (String [] args)                                                                                                                                             | Dies kann dadurch verursacht werden, dass kein erforderlicher Parameter für ein von Ihnen verwentigtes Szenario angegeben wird. Beispielsweise wird eine Verbindungs Zeichenfolge ohne Angabe des Anbieter namens angegeben.                                                                                                                        |
| Fehler: in der Assembly "ClassLibrary1" wurde mehr als ein Migrations Konfigurationstyp gefunden. Geben Sie den Namen des zu verwendenden an.                                                                                                                                                                                                  | Da der Fehlerstatus ist, gibt es in der angegebenen Assembly mehr als eine Konfigurations Klasse. Sie müssen den/configurationType-Schalter verwenden, um anzugeben, welche verwendet werden soll.                                                                                                                                           |
| Fehler: die Datei oder Assembly " &lt; AssemblyName &gt; " oder eine ihrer Abhängigkeiten konnte nicht geladen werden. Der angegebene Assemblyname oder die angegebene Codebasis war ungültig. (Ausnahme von HRESULT: 0x80131047)                                                                                                                                                    | Dies kann dadurch verursacht werden, dass ein AssemblyName falsch angegeben oder nicht vorhanden ist.                                                                                                                                                                                                                          |
| Fehler: die Datei oder Assembly " &lt; AssemblyName &gt; " oder eine ihrer Abhängigkeiten konnte nicht geladen werden. Es wurde versucht, ein Programm mit einem falschen Format zu laden.                                                                                                                                                                          | Dies geschieht, wenn Sie versuchen, migrate.exe für eine x64-Anwendung auszuführen. EF 5,0 und niedriger ist nur auf x86 funktionsfähig.                                                                                                                                                                                |
