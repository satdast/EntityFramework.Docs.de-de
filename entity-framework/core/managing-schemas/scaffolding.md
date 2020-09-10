---
title: Reverse Engineering-EF Core
description: Rückgängigmachen eines Modells aus einer vorhandenen Datenbank mithilfe Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: e88be834aa1f2f3be41657f07bc7ace3e34c58aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619216"
---
# <a name="reverse-engineering"></a> Reverse Engineering

Reverse Engineering ist der Prozess der Gerüstbau von Entitäts Typen Klassen und einer dbcontext-Klasse auf Grundlage eines Datenbankschemas. Dies kann mit dem Befehl der `Scaffold-DbContext` EF Core Package Manager Console-Konsole (PMC) oder dem Befehl der `dotnet ef dbcontext scaffold` .net-Befehlszeilenschnittstelle (CLI) ausgeführt werden.

## <a name="installing"></a>Installation

Bevor Reverse Engineering, müssen Sie entweder die [PMC-Tools](xref:core/miscellaneous/cli/powershell) (nur Visual Studio) oder die CLI- [Tools](xref:core/miscellaneous/cli/dotnet)installieren. Weitere Informationen finden Sie unter Links.

Außerdem müssen Sie einen geeigneten [Datenbankanbieter](xref:core/providers/index) für das Datenbankschema installieren, das Sie rückgängig machen möchten.

## <a name="connection-string"></a>Verbindungszeichenfolge

Das erste Argument für den Befehl ist eine Verbindungs Zeichenfolge für die Datenbank. Diese Verbindungs Zeichenfolge wird von den-Tools zum Lesen des Datenbankschemas verwendet.

Wie Sie die Verbindungs Zeichenfolge zitieren und mit Escapezeichen versehen, hängt davon ab, welche Shell Sie zum Ausführen des Befehls verwenden. Einzelheiten finden Sie in der Dokumentation Ihrer Shell. Beispielsweise erfordert PowerShell, dass Sie das `$` Zeichen mit Escapezeichen versehen `\` .

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

***

### <a name="configuration-and-user-secrets"></a>Konfiguration und Benutzer Geheimnisse

Wenn Sie ein ASP.net Core-Projekt haben, können Sie die-Syntax verwenden, `Name=<connection-string>` um die Verbindungs Zeichenfolge aus der Konfiguration zu lesen.

Dies funktioniert gut mit dem [Geheimnis-Manager-Tool](/aspnet/core/security/app-secrets#secret-manager) , um das Daten Bank Kennwort von ihrer CodeBase getrennt zu halten.

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=ConnectionStrings.Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a>Anbietername

Das zweite Argument ist der Anbieter Name. Der Anbieter Name ist in der Regel mit dem nuget-Paketnamen des Anbieters identisch.

## <a name="specifying-tables"></a>Angeben von Tabellen

Alle Tabellen im Datenbankschema werden standardmäßig in Entitäts Typen entwickelt. Sie können einschränken, welche Tabellen in umgekehrter Reihenfolge entwickelt werden, indem Sie Schemas und Tabellen angeben.

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

Die- `--schema` Option kann verwendet werden, um jede Tabelle innerhalb eines Schemas einzubeziehen, während `--table` zum Einschließen bestimmter Tabellen verwendet werden kann.

Wenn Sie mehrere Tabellen einschließen möchten, geben Sie die Option mehrmals an:

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

Die- `-Schemas` Option kann verwendet werden, um jede Tabelle innerhalb eines Schemas einzubeziehen, während `-Tables` zum Einschließen bestimmter Tabellen verwendet werden kann.

Verwenden Sie ein Array, um mehrere Tabellen einzubeziehen:

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

***

## <a name="preserving-names"></a>Beibehalten von Namen

Tabellen-und Spaltennamen werden korrigiert, um die .net-Benennungs Konventionen für Typen und Eigenschaften standardmäßig besser abzugleichen. Wenn Sie den `-UseDatabaseNames` Switch in der PMC oder die `--use-database-names` Option in der .net Core-CLI angeben, wird dieses Verhalten deaktiviert, sodass die ursprünglichen Datenbanknamen so weit wie möglich beibehalten werden. Ungültige .net-Bezeichner sind immer noch korrigiert, und synthetisierte Namen wie Navigations Eigenschaften entsprechen weiterhin den .net-Benennungs Konventionen.

## <a name="fluent-api-or-data-annotations"></a>Fließende API oder Daten Anmerkungen

Entitäts Typen werden standardmäßig mit der flüssigen API konfiguriert. Geben `-DataAnnotations` Sie (PMC) oder `--data-annotations` (.net Core-CLI) an, um Daten Anmerkungen zu verwenden, sofern möglich.

Beispielsweise wird das Gerüst mithilfe der fließenden API erstellt:

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

Beim Verwenden von Daten Anmerkungen wird dieses Gerüst erstellt:

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a>Dbcontext-Name

Der Name der dbcontext-Klasse mit dem Gerüst ist der Name der Datenbank, der standardmäßig mit dem *Kontext* versehen wird. Um einen anderen anzugeben, verwenden Sie `-Context` in der PMC und `--context` in der .net Core-CLI.

## <a name="directories-and-namespaces"></a>Verzeichnisse und Namespaces

Die Entitäts Klassen und eine dbcontext-Klasse werden im Stammverzeichnis des Projekts erstellt und verwenden den Standard Namespace des Projekts.

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

Sie können das Verzeichnis angeben, in dem Klassen mithilfe eines Gerüsts `--output-dir` erstellt werden, und `--context-dir` können verwendet werden, um die dbcontext-Klasse in ein separates Verzeichnis der Entitätstyp Klassen zu setzen:

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

Standardmäßig ist der Namespace der Stamm Namespace und die Namen aller Unterverzeichnisse im Stammverzeichnis des Projekts. Allerdings können Sie ab efcore 5,0 den Namespace für alle Ausgabe Klassen überschreiben, indem Sie verwenden `--namespace` . Sie können auch den Namespace nur für die dbcontext-Klasse überschreiben, indem Sie Folgendes verwenden `--context-namespace` :

```dotnetcli
dotnet ef dbcontext scaffold ... --namespace Your.Namespace --context-namespace Your.DbContext.Namespace
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

Sie können das Verzeichnis angeben, in dem Klassen mithilfe eines Gerüsts `-OutputDir` erstellt werden, und `-ContextDir` können verwendet werden, um die dbcontext-Klasse in ein separates Verzeichnis der Entitätstyp Klassen zu setzen:

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

Standardmäßig ist der Namespace der Stamm Namespace und die Namen aller Unterverzeichnisse im Stammverzeichnis des Projekts. Allerdings können Sie ab efcore 5,0 den Namespace für alle Ausgabe Klassen überschreiben, indem Sie verwenden `-Namespace` . Sie können auch den Namespace nur für die dbcontext-Klasse überschreiben, indem Sie verwenden `-ContextNamespace` .

``` powershell
Scaffold-DbContext ... -Namespace Your.Namespace -ContextNamespace Your.DbContext.Namespace
```

***

## <a name="how-it-works"></a>Funktionsweise

Reverse Engineering beginnt mit dem Lesen des Datenbankschemas. Er liest Informationen zu Tabellen, Spalten, Einschränkungen und Indizes.

Anschließend werden die Schema Informationen verwendet, um ein EF Core Modell zu erstellen. Tabellen werden verwendet, um Entitäts Typen zu erstellen. Spalten werden verwendet, um Eigenschaften zu erstellen; und Fremdschlüssel werden zum Erstellen von Beziehungen verwendet.

Schließlich wird das Modell verwendet, um Code zu generieren. Die entsprechenden Entitätstyp Klassen, die fließende API und Daten Anmerkungen werden erstellt, um das gleiche Modell aus Ihrer APP neu zu erstellen.

## <a name="limitations"></a>Einschränkungen

* Nicht alles über ein Modell kann mithilfe eines Datenbankschemas dargestellt werden. Beispielsweise sind Informationen zu [**Vererbungs Hierarchien**](xref:core/modeling/inheritance), [**eigenen Typen**](xref:core/modeling/owned-entities)und [**Tabellen Aufteilung**](xref:core/modeling/table-splitting) im Datenbankschema nicht vorhanden. Aus diesem Grund werden diese Konstrukte nie in umgekehrter Reihenfolge entwickelt.
* Außerdem werden **einige Spaltentypen** möglicherweise vom EF Core Anbieter nicht unterstützt. Diese Spalten werden nicht in das Modell eingeschlossen.
* Sie können Parallelitäts [**Token**](xref:core/modeling/concurrency)in einem EF Core Modell definieren, um zu verhindern, dass zwei Benutzer gleichzeitig dieselbe Entität aktualisieren. Einige Datenbanken verfügen über einen besonderen Typ, der diese Art von Spalte darstellt (z. b. rowversion in SQL Server). in diesem Fall können wir diese Informationen umkehren. andere Parallelitäts Token werden jedoch nicht in umgekehrter Reihenfolge entwickelt.
* [Der Referenztyp c# 8, der NULL-Werte](/dotnet/csharp/tutorials/nullable-reference-types) zulässt, wird derzeit in Reverse Engineering nicht unterstützt: EF Core generiert immer c#-Code, der annimmt, dass das Feature deaktiviert ist. Beispielsweise werden Textspalten, die NULL-Werte zulassen, als Eigenschaft mit dem Typ `string` erstellt, nicht `string?` mit der fließenden API oder den Daten Anmerkungen, mit denen konfiguriert wird, ob eine Eigenschaft erforderlich ist. Sie können den Gerüst Code bearbeiten und durch c#-Anmerkungen zur NULL-Zulässigkeit ersetzen. Die Gerüstbau Unterstützung für Verweis Typen, die NULL-Werte zulassen, wird von Issue [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520)

## <a name="customizing-the-model"></a>Anpassen des Modells

Der von EF Core generierte Code ist Ihr Code. Sie können es jederzeit ändern. Sie wird nur dann erneut generiert, wenn Sie das gleiche Modell erneut umkehren. Der Gerüst Code stellt *ein* Modell dar, das für den Zugriff auf die Datenbank verwendet werden kann. es ist jedoch sicherlich nicht das *einzige* Modell, das verwendet werden kann.

Passen Sie die Entitätstyp Klassen und die dbcontext-Klasse an Ihre Anforderungen an. Sie können z. b. Typen und Eigenschaften umbenennen, Vererbungs Hierarchien einführen oder eine Tabelle in mehrere Entitäten aufteilen. Sie können auch nicht eindeutige Indizes, nicht verwendete Sequenzen und Navigations Eigenschaften, optionale skalare Eigenschaften und Einschränkungs Namen aus dem Modell entfernen.

Sie können auch zusätzliche Konstruktoren, Methoden, Eigenschaften usw. hinzufügen. Verwenden einer anderen partiellen Klasse in einer separaten Datei. Diese Vorgehensweise funktioniert auch, wenn Sie das Modell erneut umkehren möchten.

## <a name="updating-the-model"></a>Aktualisieren des Modells

Nachdem Sie Änderungen an der Datenbank vorgenommen haben, müssen Sie möglicherweise das EF Core Modell aktualisieren, um diese Änderungen widerzuspiegeln. Wenn die Daten Bank Änderungen einfach sind, ist es möglicherweise am einfachsten, die Änderungen am EF Core Modell manuell vorzunehmen. Beispielsweise sind das Umbenennen einer Tabelle oder Spalte, das Entfernen einer Spalte oder das Aktualisieren eines Spalten Typs triviale Änderungen, die im Code vorgenommen werden müssen.

Bedeutendere Änderungen sind jedoch nicht so einfach wie die manuelle Ausführung. Ein allgemeiner Workflow besteht darin, das Modell mithilfe von `-Force` (PMC) oder (CLI) erneut aus der Datenbank `--force` zu entwickeln, um das vorhandene Modell mit einem aktualisierten zu überschreiben.

Ein weiteres häufig angefordertes Feature ist die Möglichkeit, das Modell aus der Datenbank zu aktualisieren und gleichzeitig Anpassungen wie Umbenennungen, Typhierarchien usw. beizubehalten. Verwenden Sie das Problem [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) , um den Fortschritt dieses Features zu verfolgen.

> [!WARNING]
> Wenn Sie das Modell erneut aus der Datenbank zurück entwickeln, gehen alle Änderungen, die Sie an den Dateien vorgenommen haben, verloren.
