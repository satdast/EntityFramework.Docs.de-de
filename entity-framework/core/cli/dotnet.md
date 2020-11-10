---
title: Referenz zu EF Core Tools (.net CLI)-EF Core
description: Referenzhandbuch für die Entity Framework Core .net Core-CLI Tools
author: bricelam
ms.date: 10/27/2020
uid: core/cli/dotnet
ms.openlocfilehash: 83989b8690236dbec3466cda78c204ab67fd10c4
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431262"
---
# <a name="entity-framework-core-tools-reference---net-core-cli"></a>Referenz zu Entity Framework Core Tools .net Core-CLI

Die CLI-Tools (Command-Line Interface, Befehlszeilenschnittstelle) für Entity Framework Core die Entwicklungsaufgaben zur Entwurfszeit ausführen. Beispielsweise erstellen Sie [Migrationen](/aspnet/core/data/ef-mvc/migrations), wenden Migrationen an und generieren Code für ein Modell, das auf einer vorhandenen Datenbank basiert. Die Befehle sind eine Erweiterung des plattformübergreifenden [dotnet](/dotnet/core/tools) -Befehls, der Teil der [.net Core SDK](https://www.microsoft.com/net/core)ist. Diese Tools funktionieren mit .net Core-Projekten.

Wenn Sie Visual Studio verwenden, sollten Sie die [Tools der Paket-Manager-Konsole](xref:core/cli/powershell) anstelle der CLI-Tools verwenden. Paket-Manager-Konsolen Tools automatisch:

* Funktioniert mit dem aktuellen Projekt, das in der **Paket-Manager-Konsole** ausgewählt ist, ohne dass Sie manuell Verzeichnisse wechseln müssten.
* Öffnet Dateien, die von einem Befehl generiert wurden, nachdem der Befehl abgeschlossen wurde.
* Stellt die Befehlszeilen Vervollständigung von Befehlen, Parametern, Projektnamen, Kontext Typen und Migrations Namen bereit.

## <a name="installing-the-tools"></a>Installieren der Tools

`dotnet ef` kann als globales oder lokales Tool installiert werden. Die meisten Entwickler bevorzugen das Installieren von `dotnet ef` als globales Tool mithilfe des folgenden Befehls:

```dotnetcli
dotnet tool install --global dotnet-ef
```

Für die Verwendung als lokales Tool stellen Sie die Abhängigkeiten eines Projekts wieder her, das das Tool mithilfe einer [Toolmanifestdatei](/dotnet/core/tools/global-tools#install-a-local-tool) als Toolabhängigkeit deklariert.

Aktualisieren Sie das Tool Tool mit dem folgenden Befehl:

```dotnetcli
dotnet tool update --global dotnet-ef
```

Bevor Sie die Tools für ein bestimmtes Projekt verwenden können, müssen Sie das Paket hinzufügen `Microsoft.EntityFrameworkCore.Design` .

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Design
```

### <a name="verify-installation"></a>Überprüfen der Installation

Führen Sie die folgenden Befehle aus, um sicherzustellen, dass EF Core CLI-Tools ordnungsgemäß installiert sind

  ```dotnetcli
  dotnet ef
  ```

Die Ausgabe des Befehls identifiziert die Version der verwendeten Tools:

```output

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

Entity Framework Core .NET Command-line Tools 2.1.3-rtm-32065

<Usage documentation follows, not shown.>
```

## <a name="update-the-tools"></a>Aktualisieren der Tools

Verwenden `dotnet tool update --global dotnet-ef` Sie, um die globalen Tools auf die neueste verfügbare Version zu aktualisieren. Wenn Sie die Tools lokal in Ihrem Projekt installiert haben, verwenden Sie `dotnet tool update dotnet-ef` . Installieren Sie eine bestimmte Version durch Anhängen `--version <VERSION>` an den Befehl. Weitere Informationen finden Sie im Abschnitt [Update](/dotnet/core/tools/dotnet-tool-update) der Dokumentation zum dotnet-Tool.

## <a name="using-the-tools"></a>Verwenden der Tools

Vor der Verwendung der Tools müssen Sie möglicherweise ein Startprojekt erstellen oder die Umgebung festlegen.

### <a name="target-project-and-startup-project"></a>Ziel Projekt und Startprojekt

Die Befehle verweisen auf ein *Projekt* und ein *Startprojekt*.

* Das *Projekt* wird auch als *Ziel Projekt* bezeichnet, weil die Befehle Dateien hinzufügen oder entfernen. Standardmäßig ist das Projekt im aktuellen Verzeichnis das Ziel Projekt. Sie können ein anderes Projekt als Ziel Projekt angeben, indem Sie die <nobr>`--project`</nobr> Option verwenden.

* Das *Startprojekt ist das Startprojekt* , das von den Tools erstellt und ausgeführt wird. Die Tools müssen Anwendungscode zur Entwurfszeit ausführen, um Informationen zum Projekt zu erhalten, z. b. die Daten bankverbindungs Zeichenfolge und die Konfiguration des Modells. Standardmäßig ist das Projekt im aktuellen Verzeichnis das Startprojekt. Sie können ein anderes Projekt als Startprojekt angeben, indem Sie die <nobr>`--startup-project`</nobr> Option verwenden.

Das Startprojekt und das Ziel Projekt sind häufig das gleiche Projekt. Ein typisches Szenario, bei dem es sich um separate Projekte handelt, sind folgende:

* Die EF Core Kontext-und Entitäts Klassen befinden sich in einer .net Core-Klassenbibliothek.
* Eine .net Core-Konsolen-APP oder-Web-App verweist auf die Klassenbibliothek.

Es ist auch möglich, [Migrations Code in einer Klassenbibliothek zu platzieren, getrennt vom EF Core Kontext](xref:core/managing-schemas/migrations/projects).

### <a name="other-target-frameworks"></a>Andere Ziel-Frameworks

Die CLI-Tools funktionieren mit .net Core-Projekten und .NET Framework Projekten. Apps, die über das EF Core Modell in einer .NET Standard-Klassenbibliothek verfügen, verfügen möglicherweise nicht über ein .net Core-oder .NET Framework-Projekt. Dies gilt z. b. für xamarin-und universelle Windows-Plattform-apps. In solchen Fällen können Sie ein .net Core-Konsolen-App-Projekt erstellen, dessen einziger Zweck darin besteht, als Startprojekt für die Tools zu fungieren. Das Projekt kann ein Dummyprojekt ohne echten Code sein &mdash; . es ist nur erforderlich, um ein Ziel für die Tools bereitzustellen.

Warum ist ein Dummyprojekt erforderlich? Wie bereits erwähnt, müssen die Tools Anwendungscode zur Entwurfszeit ausführen. Hierzu müssen Sie die .net Core-Laufzeit verwenden. Wenn sich das EF Core Modell in einem Projekt befindet, das .net Core oder .NET Framework als Ziel hat, wird die Laufzeit von den EF Core Tools aus dem Projekt ausgeliehen. Dies ist nicht möglich, wenn sich das EF Core Modell in einer .NET Standard Klassenbibliothek befindet. Der .NET Standard ist keine tatsächliche .NET-Implementierung. Es handelt sich um eine Spezifikation für eine Reihe von APIs, die von .net-Implementierungen unterstützt werden müssen. Daher ist .NET Standard für die EF Core Tools nicht ausreichend, um Anwendungscode auszuführen. Das Dummyprojekt, das Sie als Startprojekt verwenden, stellt eine konkrete Zielplattform bereit, in die die Tools die .NET Standard Klassenbibliothek laden können.

### <a name="aspnet-core-environment"></a>ASP.net Core Umgebung

Legen Sie die Umgebungsvariable **ASPNETCORE_ENVIRONMENT** vor dem Ausführen von Befehlen fest, um [die Umgebung](/aspnet/core/fundamentals/environments) für ASP.net Core Projekte anzugeben.

Ab EF Core 5,0 können zusätzliche Argumente auch an "Program. kreatehostbuilder" übergeben werden, sodass Sie die Umgebung in der Befehlszeile angeben können:

```dotnetcli
dotnet ef database update -- --environment Production
```

> [!TIP]
> Das `--` Token leitet `dotnet ef` , um alle nachfolgenden Elemente als Argument zu behandeln, und versucht nicht, Sie als Optionen zu analysieren. Alle zusätzlichen Argumente, die nicht von verwendet `dotnet ef` werden, werden an die APP weitergeleitet.

## <a name="common-options"></a>Allgemeine Optionen

| Option                                         | Short             | BESCHREIBUNG                                                                                                                                                                                                                                                   |
|:-----------------------------------------------|:------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--json`                                       |                   | JSON-Ausgabe anzeigen.                                                                                                                                                                                                                                             |
| `--context <DBCONTEXT>`                        | <nobr>`-c`</nobr> | Die `DbContext`-Klasse, die verwendet werden soll. Der Klassenname oder voll qualifiziert mit Namespaces.  Wenn diese Option weggelassen wird, wird EF Core die Kontext Klasse finden. Wenn mehrere Kontext Klassen vorhanden sind, ist diese Option erforderlich.                                            |
| `--project <PROJECT>`                          | `-p`              | Relativer Pfad zum Projektordner des Ziel Projekts.  Der Standardwert ist der aktuelle Ordner.                                                                                                                                                              |
| `--startup-project <PROJECT>`                  | `-s`              | Relativer Pfad zum Projektordner des Start Projekts. Der Standardwert ist der aktuelle Ordner.                                                                                                                                                              |
| `--framework <FRAMEWORK>`                      |                   | Der [zielframeworkmoniker](/dotnet/standard/frameworks#supported-target-framework-versions) für das [Ziel Framework](/dotnet/standard/frameworks).  Verwenden Sie, wenn die Projektdatei mehrere Ziel-Frameworks angibt, und wählen Sie eine davon aus. |
| <nobr>`--configuration <CONFIGURATION>`</nobr> |                   | Die Buildkonfiguration, z `Debug` . b `Release` . oder.                                                                                                                                                                                                   |
| `--runtime <IDENTIFIER>`                       |                   | Der Bezeichner der Ziel Laufzeit, für die Pakete wieder hergestellt werden sollen. Eine Liste der Runtime-IDs (RIDs) finden Sie unter [RID-Katalog](/dotnet/core/rid-catalog).                                                                                                      |
| `--no-build`                                   |                   | Erstellen Sie das Projekt nicht. Soll verwendet werden, wenn der Build auf dem neuesten Stand ist.                                                                                                                                                                                    |
| `--help`                                       | `-h`              | Zeigt Hilfeinformationen an                                                                                                                                                                                                                                        |
| `--verbose`                                    | `-v`              | Zeigt eine ausführliche Ausgabe an.                                                                                                                                                                                                                                          |
| `--no-color`                                   |                   | Ausgabe nicht einfärben.                                                                                                                                                                                                                                        |
| `--prefix-output`                              |                   | Präfix Ausgabe mit Ebene.                                                                                                                                                                                                                                     |

Ab EF Core 5,0 werden alle zusätzlichen Argumente an die Anwendung weitergegeben.

## <a name="dotnet-ef-database-drop"></a>DotNet EF-Datenbank löschen

Löscht die Datenbank.

Optionen:

| Option                   | Short             | BESCHREIBUNG                                              |
|:-------------------------|:------------------|:---------------------------------------------------------|
| `--force`                | <nobr>`-f`</nobr> | Nicht bestätigen.                                           |
| <nobr>`--dry-run`</nobr> |                   | Zeigen Sie an, welche Datenbank gelöscht werden soll, aber löschen Sie Sie nicht. |

Die [allgemeinen Optionen](#common-options) sind oben aufgeführt.

## <a name="dotnet-ef-database-update"></a>DotNet EF-Datenbankupdate

Aktualisiert die Datenbank auf die letzte Migration oder eine angegebene Migration.

Argumente:

| Argument                   | BESCHREIBUNG                                                                                                                                                                                                                                                     |
|:---------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<MIGRATION>`</nobr> | Die Ziel Migration. Migrationen können anhand des Namens oder der ID identifiziert werden. Die Zahl 0 (null) ist ein Sonderfall, der *vor der ersten Migration* steht und bewirkt, dass alle Migrationen rückgängig gemacht werden. Wenn keine Migration angegeben ist, wird für den Befehl standardmäßig die letzte Migration verwendet. |

Optionen:

| Option                                    | BESCHREIBUNG                                                                                                                      |
|:------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------|
|  <nobr>`--connection <CONNECTION>`</nobr> | Die Verbindungszeichenfolge für die Datenbank. Der Standardwert ist der in `AddDbContext` oder angegebene `OnConfiguring` . In EF Core 5,0 hinzugefügt. |

Die [allgemeinen Optionen](#common-options) sind oben aufgeführt.

In den folgenden Beispielen wird die-Datenbank auf eine angegebene Migration aktualisiert. Der erste verwendet den Migrations Namen, der zweite verwendet die Migrations-ID und eine angegebene Verbindung:

```dotnetcli
dotnet ef database update InitialCreate
dotnet ef database update 20180904195021_InitialCreate --connection your_connection_string
```

## <a name="dotnet-ef-dbcontext-info"></a>DotNet EF dbcontext-Informationen

Ruft Informationen zu einem `DbContext` Typ ab.

Die [allgemeinen Optionen](#common-options) sind oben aufgeführt.

## <a name="dotnet-ef-dbcontext-list"></a>DotNet EF-dbcontext-Liste

Listet die verfügbaren `DbContext` Typen auf.

Die [allgemeinen Optionen](#common-options) sind oben aufgeführt.

## <a name="dotnet-ef-dbcontext-scaffold"></a>DotNet EF-dbcontext-Gerüst

Generiert Code für einen `DbContext` und Entitäts Typen für eine Datenbank. Damit dieser Befehl einen Entitätstyp generieren kann, muss die Datenbanktabelle über einen Primärschlüssel verfügen.

Argumente:

| Argument                    | BESCHREIBUNG                                                                                                                                                                                                             |
|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<CONNECTION>`</nobr> | Die Verbindungszeichenfolge für die Datenbank. Bei ASP.net Core 2. x-Projekten kann der Wert " *Name = \<name of connection string>* " lauten. In diesem Fall stammt der Name aus den Konfigurations Quellen, die für das Projekt eingerichtet sind. |
| `<PROVIDER>`                | Der zu verwendende Anbieter. In der Regel ist dies der Name des nuget-Pakets, z `Microsoft.EntityFrameworkCore.SqlServer` . b.:.                                                                                           |

Optionen:

| Option                                   | Short             | BESCHREIBUNG                                                                                                                                                                    |
|:-----------------------------------------|:------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--data-annotations`                     | <nobr>`-d`</nobr> | Verwenden Sie Attribute, um das Modell zu konfigurieren (sofern möglich). Wenn diese Option weggelassen wird, wird nur die fließende API verwendet.                                                                |
| `--context <NAME>`                       | `-c`              | Der Name der `DbContext` zu generierenden Klasse.                                                                                                                                 |
| `--context-dir <PATH>`                   |                   | Das Verzeichnis, in das die Klassendatei eingefügt werden soll `DbContext` . Pfade sind relativ zum Projektverzeichnis. Namespaces werden aus den Ordnernamen abgeleitet.                                 |
| `--context-namespace <NAMESPACE>`        |                   | Der Namespace, der für die generierte Klasse verwendet werden soll `DbContext` . Hinweis: überschreibt `--namespace` . In EF Core 5,0 hinzugefügt.                                                                 |
| `--force`                                | `-f`              | Überschreibt vorhandene Dateien.                                                                                                                                                      |
| `--output-dir <PATH>`                    | `-o`              | Das Verzeichnis, in dem Entitäts Klassendateien abgelegt werden sollen. Pfade sind relativ zum Projektverzeichnis.                                                                                       |
| `--namespace <NAMESPACE>`                | `-n`              | Der Namespace, der für alle generierten Klassen verwendet werden soll. Wird standardmäßig aus dem Stamm Namespace und dem Ausgabeverzeichnis generiert. In EF Core 5,0 hinzugefügt.                                  |
| <nobr>`--schema <SCHEMA_NAME>...`</nobr> |                   | Die Schemas von Tabellen, für die Entitäts Typen generiert werden sollen. Wenn Sie mehrere Schemas angeben möchten, wiederholen Sie diese `--schema` für jeden Vorgang. Wenn diese Option weggelassen wird, werden alle Schemas eingeschlossen.          |
| `--table <TABLE_NAME>`...                | `-t`              | Die Tabellen, für die Entitäts Typen generiert werden sollen. Wenn Sie mehrere Tabellen angeben möchten, wiederholen Sie `-t` oder `--table` für jede einzelne Tabelle. Wenn diese Option weggelassen wird, werden alle Tabellen eingeschlossen.                |
| `--use-database-names`                   |                   | Verwenden Sie Tabellen-und Spaltennamen genau so, wie Sie in der Datenbank angezeigt werden. Wenn diese Option weggelassen wird, werden Datenbanknamen geändert, um den c#-namens Stil Konventionen genauer zu entsprechen. |
| `--no-onconfiguring`                     |                   | Unterdrückt die Generierung der `OnConfiguring` Methode in der generierten `DbContext` Klasse. In EF Core 5,0 hinzugefügt.                                                                  |
| `--no-pluralize`                         |                   | Verwenden Sie nicht das pluralizer-Element. In EF Core 5,0 hinzugefügt                                                                                                                                 |

Die [allgemeinen Optionen](#common-options) sind oben aufgeführt.

Im folgenden Beispiel wird ein Gerüst für alle Schemas und Tabellen und die neuen Dateien im Ordner " *Models* " eingefügt.

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models
```

Im folgenden Beispiel wird nur ein Gerüst für ausgewählte Tabellen erstellt und der Kontext in einem separaten Ordner mit einem angegebenen Namen und Namespace erstellt:

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -o Models -t Blog -t Post --context-dir Context -c BlogContext --context-namespace New.Namespace
```

Im folgenden Beispiel wird die Verbindungs Zeichenfolge mit dem Geheimnis-Manager- [Tool](/aspnet/core/security/app-secrets#secret-manager)aus dem Konfigurationssatz des Projekts gelesen.

```dotnetcli
dotnet user-secrets set ConnectionStrings.Blogging "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Blogging"
dotnet ef dbcontext scaffold Name=ConnectionStrings.Blogging Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="dotnet-ef-dbcontext-script"></a>DotNet EF-dbcontext-Skript

Generiert ein SQL-Skript aus dem dbcontext. Umgeht alle Migrationen. In EF Core 3,0 hinzugefügt.

Optionen:

| Option                         | Short             | BESCHREIBUNG                      |
| ------------------------------ | ----------------- | -------------------------------- |
| <nobr>`--output <FILE>`</nobr> | <nobr>`-o`</nobr> | Die Datei, in die das Ergebnis geschrieben werden soll. |

Die [allgemeinen Optionen](#common-options) sind oben aufgeführt.

## <a name="dotnet-ef-migrations-add"></a>DotNet EF-Migrationen hinzufügen

Fügt eine neue Migration hinzu.

Argumente:

| Argument              | BESCHREIBUNG                |
|:----------------------|:---------------------------|
| <nobr>`<NAME>`</nobr> | Der Name der Migration. |

Optionen:

| Option                                 | Short             | BESCHREIBUNG                                                                                                            |
|:---------------------------------------|:------------------|:-----------------------------------------------------------------------------------------------------------------------|
| `--output-dir <PATH>`                  | <nobr>`-o`</nobr> | Das Verzeichnis, mit dem die Dateien ausgegeben werden. Pfade sind relativ zum Ziel Projektverzeichnis. Der Standardwert ist "Migrationen".   |
| <nobr>`--namespace <NAMESPACE>`</nobr> | `-n`              | Der Namespace, der für die generierten Klassen verwendet werden soll. Wird standardmäßig aus dem Ausgabeverzeichnis generiert. In EF Core 5,0 hinzugefügt. |

Die [allgemeinen Optionen](#common-options) sind oben aufgeführt.

## <a name="dotnet-ef-migrations-list"></a>DotNet EF-Migrations Liste

Listet verfügbare Migrationen auf.

Optionen:

| Option                                   | BESCHREIBUNG                                                                                                                  |
| ---------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| <nobr>`--connection <CONNECTION>`</nobr> | Die Verbindungszeichenfolge für die Datenbank. Der Standardwert ist der in "adddbcontext" oder "onkonfiguration" angegebene. In EF Core 5,0 hinzugefügt. |
| `--no-connect`                           | Stellen Sie keine Verbindung mit der Datenbank her. In EF Core 5,0 hinzugefügt.                                                                         |

Die [allgemeinen Optionen](#common-options) sind oben aufgeführt.

## <a name="dotnet-ef-migrations-remove"></a>DotNet EF-Migrationen entfernen

Entfernt die letzte Migration (führt einen Rollback für die Codeänderungen aus, die für die Migration durchgeführt wurden).

Optionen:

| Option                 | Short             | BESCHREIBUNG                                                                     |
|:-----------------------|:------------------|:--------------------------------------------------------------------------------|
| <nobr>`--force`</nobr> | <nobr>`-f`</nobr> | Setzen Sie die Migration zurück (führen Sie ein Rollback der Änderungen aus, die auf die Datenbank angewendet wurden). |

Die [allgemeinen Optionen](#common-options) sind oben aufgeführt.

## <a name="dotnet-ef-migrations-script"></a>DotNet EF-Migrations Skript

Generiert ein SQL-Skript aus Migrationen.

Argumente:

| Argument              | BESCHREIBUNG                                                                                                                                                   |
|:----------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>`<FROM>`</nobr> | Die Migration wird gestartet. Migrationen können anhand des Namens oder der ID identifiziert werden. Die Zahl 0 (null) ist ein Sonderfall, der *vor der ersten Migration* liegt. Der Standardwert ist 0. |
| `<TO>`                | Die Beendigung der Migration. Standardmäßig wird die letzte Migration verwendet.                                                                                                         |

Optionen:

| Option                           | Short             | BESCHREIBUNG                                                        |
|:---------------------------------|:------------------|:-------------------------------------------------------------------|
| `--output <FILE>`                | <nobr>`-o`</nobr> | Die Datei, in die das Skript geschrieben werden soll.                                   |
| `--idempotent`                   | `-i`              | Generieren Sie ein Skript, das bei jeder Migration in einer Datenbank verwendet werden kann. |
| <nobr>`--no-transactions`</nobr> |                   | Generieren Sie keine SQL-Transaktions Anweisungen. In EF Core 5,0 hinzugefügt.   |

Die [allgemeinen Optionen](#common-options) sind oben aufgeführt.

Im folgenden Beispiel wird ein Skript für die InitialCreate-Migration erstellt:

```dotnetcli
dotnet ef migrations script 0 InitialCreate
```

Im folgenden Beispiel wird nach der InitialCreate-Migration ein Skript für alle Migrationen erstellt.

```dotnetcli
dotnet ef migrations script 20180904195021_InitialCreate
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Migrationen](xref:core/managing-schemas/migrations/index)
* [Reverse Engineering](xref:core/managing-schemas/scaffolding)
