---
title: Reverse Engineering-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 19945ef2eb99ac423cc50510edc85439964024b8
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238189"
---
# <a name="reverse-engineering"></a><span data-ttu-id="1bb9b-102"> Reverse Engineering</span><span class="sxs-lookup"><span data-stu-id="1bb9b-102">Reverse Engineering</span></span>

<span data-ttu-id="1bb9b-103">Reverse Engineering ist der Prozess der Gerüstbau von Entitäts Typen Klassen und einer dbcontext-Klasse auf Grundlage eines Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-103">Reverse engineering is the process of scaffolding entity type classes and a DbContext class based on a database schema.</span></span> <span data-ttu-id="1bb9b-104">Dies kann mit dem Befehl der `Scaffold-DbContext` EF Core Package Manager Console-Konsole (PMC) oder dem Befehl der `dotnet ef dbcontext scaffold` .net-Befehlszeilenschnittstelle (CLI) ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-104">It can be performed using the `Scaffold-DbContext` command of the EF Core Package Manager Console (PMC) tools or the `dotnet ef dbcontext scaffold` command of the .NET Command-line Interface (CLI) tools.</span></span>

## <a name="installing"></a><span data-ttu-id="1bb9b-105">Installation</span><span class="sxs-lookup"><span data-stu-id="1bb9b-105">Installing</span></span>

<span data-ttu-id="1bb9b-106">Bevor Reverse Engineering, müssen Sie entweder die [PMC-Tools](xref:core/miscellaneous/cli/powershell) (nur Visual Studio) oder die CLI- [Tools](xref:core/miscellaneous/cli/dotnet)installieren.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-106">Before reverse engineering, you'll need to install either the [PMC tools](xref:core/miscellaneous/cli/powershell) (Visual Studio only) or the [CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span> <span data-ttu-id="1bb9b-107">Weitere Informationen finden Sie unter Links.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-107">See links for details.</span></span>

<span data-ttu-id="1bb9b-108">Außerdem müssen Sie einen geeigneten [Datenbankanbieter](xref:core/providers/index) für das Datenbankschema installieren, das Sie rückgängig machen möchten.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-108">You'll also need to install an appropriate [database provider](xref:core/providers/index) for the database schema you want to reverse engineer.</span></span>

## <a name="connection-string"></a><span data-ttu-id="1bb9b-109">Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="1bb9b-109">Connection string</span></span>

<span data-ttu-id="1bb9b-110">Das erste Argument für den Befehl ist eine Verbindungs Zeichenfolge für die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-110">The first argument to the command is a connection string to the database.</span></span> <span data-ttu-id="1bb9b-111">Diese Verbindungs Zeichenfolge wird von den-Tools zum Lesen des Datenbankschemas verwendet.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-111">The tools will use this connection string to read the database schema.</span></span>

<span data-ttu-id="1bb9b-112">Wie Sie die Verbindungs Zeichenfolge zitieren und mit Escapezeichen versehen, hängt davon ab, welche Shell Sie zum Ausführen des Befehls verwenden.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-112">How you quote and escape the connection string depends on which shell you are using to execute the command.</span></span> <span data-ttu-id="1bb9b-113">Einzelheiten finden Sie in der Dokumentation Ihrer Shell.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-113">Refer to your shell's documentation for specifics.</span></span> <span data-ttu-id="1bb9b-114">Beispielsweise erfordert PowerShell, dass Sie das `$` Zeichen mit Escapezeichen versehen `\` .</span><span class="sxs-lookup"><span data-stu-id="1bb9b-114">For example, PowerShell requires you to escape the `$` character, but not `\`.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="1bb9b-115">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="1bb9b-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[<span data-ttu-id="1bb9b-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bb9b-116">Visual Studio</span></span>](#tab/vs)

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

***

### <a name="configuration-and-user-secrets"></a><span data-ttu-id="1bb9b-117">Konfiguration und Benutzer Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="1bb9b-117">Configuration and User Secrets</span></span>

<span data-ttu-id="1bb9b-118">Wenn Sie ein ASP.net Core-Projekt haben, können Sie die-Syntax verwenden, `Name=<connection-string>` um die Verbindungs Zeichenfolge aus der Konfiguration zu lesen.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-118">If you have an ASP.NET Core project, you can use the `Name=<connection-string>` syntax to read the connection string from configuration.</span></span>

<span data-ttu-id="1bb9b-119">Dies funktioniert gut mit dem [Geheimnis-Manager-Tool](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) , um das Daten Bank Kennwort von ihrer CodeBase getrennt zu halten.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-119">This works well with the [Secret Manager tool](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) to keep your database password separate from your codebase.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=ConnectionStrings.Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a><span data-ttu-id="1bb9b-120">Anbietername</span><span class="sxs-lookup"><span data-stu-id="1bb9b-120">Provider name</span></span>

<span data-ttu-id="1bb9b-121">Das zweite Argument ist der Anbieter Name.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-121">The second argument is the provider name.</span></span> <span data-ttu-id="1bb9b-122">Der Anbieter Name ist in der Regel mit dem nuget-Paketnamen des Anbieters identisch.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-122">The provider name is typically the same as the provider's NuGet package name.</span></span>

## <a name="specifying-tables"></a><span data-ttu-id="1bb9b-123">Angeben von Tabellen</span><span class="sxs-lookup"><span data-stu-id="1bb9b-123">Specifying tables</span></span>

<span data-ttu-id="1bb9b-124">Alle Tabellen im Datenbankschema werden standardmäßig in Entitäts Typen entwickelt.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-124">All tables in the database schema are reverse engineered into entity types by default.</span></span> <span data-ttu-id="1bb9b-125">Sie können einschränken, welche Tabellen in umgekehrter Reihenfolge entwickelt werden, indem Sie Schemas und Tabellen angeben.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-125">You can limit which tables are reverse engineered by specifying schemas and tables.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="1bb9b-126">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="1bb9b-126">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="1bb9b-127">Die- `--schema` Option kann verwendet werden, um jede Tabelle innerhalb eines Schemas einzubeziehen, während `--table` zum Einschließen bestimmter Tabellen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-127">The `--schema` option can be used to include every table within a schema, while `--table` can be used to include specific tables.</span></span>

<span data-ttu-id="1bb9b-128">Wenn Sie mehrere Tabellen einschließen möchten, geben Sie die Option mehrmals an:</span><span class="sxs-lookup"><span data-stu-id="1bb9b-128">To include multiple tables, specify the option multiple times:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

### <a name="visual-studio"></a>[<span data-ttu-id="1bb9b-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bb9b-129">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="1bb9b-130">Die- `-Schemas` Option kann verwendet werden, um jede Tabelle innerhalb eines Schemas einzubeziehen, während `-Tables` zum Einschließen bestimmter Tabellen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-130">The `-Schemas` option can be used to include every table within a schema, while `-Tables` can be used to include specific tables.</span></span>

<span data-ttu-id="1bb9b-131">Verwenden Sie ein Array, um mehrere Tabellen einzubeziehen:</span><span class="sxs-lookup"><span data-stu-id="1bb9b-131">To include multiple tables, use an array:</span></span>

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

***

## <a name="preserving-names"></a><span data-ttu-id="1bb9b-132">Beibehalten von Namen</span><span class="sxs-lookup"><span data-stu-id="1bb9b-132">Preserving names</span></span>

<span data-ttu-id="1bb9b-133">Tabellen-und Spaltennamen werden korrigiert, um die .net-Benennungs Konventionen für Typen und Eigenschaften standardmäßig besser abzugleichen.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-133">Table and column names are fixed up to better match the .NET naming conventions for types and properties by default.</span></span> <span data-ttu-id="1bb9b-134">Wenn Sie den `-UseDatabaseNames` Switch in der PMC oder die `--use-database-names` Option in der .net Core-CLI angeben, wird dieses Verhalten deaktiviert, sodass die ursprünglichen Datenbanknamen so weit wie möglich beibehalten werden.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-134">Specifying the `-UseDatabaseNames` switch in PMC or the `--use-database-names` option in the .NET Core CLI will disable this behavior preserving the original database names as much as possible.</span></span> <span data-ttu-id="1bb9b-135">Ungültige .net-Bezeichner sind immer noch korrigiert, und synthetisierte Namen wie Navigations Eigenschaften entsprechen weiterhin den .net-Benennungs Konventionen.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-135">Invalid .NET identifiers will still be fixed and synthesized names like navigation properties will still conform to .NET naming conventions.</span></span>

## <a name="fluent-api-or-data-annotations"></a><span data-ttu-id="1bb9b-136">Fließende API oder Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="1bb9b-136">Fluent API or Data Annotations</span></span>

<span data-ttu-id="1bb9b-137">Entitäts Typen werden standardmäßig mit der flüssigen API konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-137">Entity types are configured using the Fluent API by default.</span></span> <span data-ttu-id="1bb9b-138">Geben `-DataAnnotations` Sie (PMC) oder `--data-annotations` (.net Core-CLI) an, um Daten Anmerkungen zu verwenden, sofern möglich.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-138">Specify `-DataAnnotations` (PMC) or `--data-annotations` (.NET Core CLI) to instead use data annotations when possible.</span></span>

<span data-ttu-id="1bb9b-139">Beispielsweise wird das Gerüst mithilfe der fließenden API erstellt:</span><span class="sxs-lookup"><span data-stu-id="1bb9b-139">For example, using the Fluent API will scaffold this:</span></span>

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

<span data-ttu-id="1bb9b-140">Beim Verwenden von Daten Anmerkungen wird dieses Gerüst erstellt:</span><span class="sxs-lookup"><span data-stu-id="1bb9b-140">While using Data Annotations will scaffold this:</span></span>

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a><span data-ttu-id="1bb9b-141">Dbcontext-Name</span><span class="sxs-lookup"><span data-stu-id="1bb9b-141">DbContext name</span></span>

<span data-ttu-id="1bb9b-142">Der Name der dbcontext-Klasse mit dem Gerüst ist der Name der Datenbank, der standardmäßig mit dem *Kontext* versehen wird.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-142">The scaffolded DbContext class name will be the name of the database suffixed with *Context* by default.</span></span> <span data-ttu-id="1bb9b-143">Um einen anderen anzugeben, verwenden Sie `-Context` in der PMC und `--context` in der .net Core-CLI.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-143">To specify a different one, use `-Context` in PMC and `--context` in the .NET Core CLI.</span></span>

## <a name="directories-and-namespaces"></a><span data-ttu-id="1bb9b-144">Verzeichnisse und Namespaces</span><span class="sxs-lookup"><span data-stu-id="1bb9b-144">Directories and namespaces</span></span>

<span data-ttu-id="1bb9b-145">Die Entitäts Klassen und eine dbcontext-Klasse werden im Stammverzeichnis des Projekts erstellt und verwenden den Standard Namespace des Projekts.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-145">The entity classes and a DbContext class are scaffolded into the project's root directory and use the project's default namespace.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="1bb9b-146">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="1bb9b-146">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="1bb9b-147">Sie können das Verzeichnis angeben, in dem Klassen mithilfe eines Gerüsts `--output-dir` erstellt werden, und `--context-dir` können verwendet werden, um die dbcontext-Klasse in ein separates Verzeichnis der Entitätstyp Klassen zu setzen:</span><span class="sxs-lookup"><span data-stu-id="1bb9b-147">You can specify the directory where classes are scaffolded using `--output-dir`, and `--context-dir` can be used to scaffold the DbContext class into a separate directory from the entity type classes:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

<span data-ttu-id="1bb9b-148">Standardmäßig ist der Namespace der Stamm Namespace und die Namen aller Unterverzeichnisse im Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-148">By default, the namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span> <span data-ttu-id="1bb9b-149">Allerdings können Sie ab efcore 5,0 den Namespace für alle Ausgabe Klassen überschreiben, indem Sie verwenden `--namespace` .</span><span class="sxs-lookup"><span data-stu-id="1bb9b-149">However, from EFCore 5.0 onwards, you can override the namespace for all output classes by using `--namespace`.</span></span> <span data-ttu-id="1bb9b-150">Sie können auch den Namespace nur für die dbcontext-Klasse überschreiben, indem Sie Folgendes verwenden `--context-namespace` :</span><span class="sxs-lookup"><span data-stu-id="1bb9b-150">You can also override the namespace for just the DbContext class using `--context-namespace`:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --namespace Your.Namespace --context-namespace Your.DbContext.Namespace
```

### <a name="visual-studio"></a>[<span data-ttu-id="1bb9b-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bb9b-151">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="1bb9b-152">Sie können das Verzeichnis angeben, in dem Klassen mithilfe eines Gerüsts `-OutputDir` erstellt werden, und `-ContextDir` können verwendet werden, um die dbcontext-Klasse in ein separates Verzeichnis der Entitätstyp Klassen zu setzen:</span><span class="sxs-lookup"><span data-stu-id="1bb9b-152">You can specify the directory where classes are scaffolded using `-OutputDir`, and `-ContextDir` can be used to scaffold the DbContext class into a separate directory from the entity type classes:</span></span>

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

<span data-ttu-id="1bb9b-153">Standardmäßig ist der Namespace der Stamm Namespace und die Namen aller Unterverzeichnisse im Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-153">By default, the namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span> <span data-ttu-id="1bb9b-154">Allerdings können Sie ab efcore 5,0 den Namespace für alle Ausgabe Klassen überschreiben, indem Sie verwenden `-Namespace` .</span><span class="sxs-lookup"><span data-stu-id="1bb9b-154">However, from EFCore 5.0 onwards, you can override the namespace for all output classes by using `-Namespace`.</span></span> <span data-ttu-id="1bb9b-155">Sie können auch den Namespace nur für die dbcontext-Klasse überschreiben, indem Sie verwenden `-ContextNamespace` .</span><span class="sxs-lookup"><span data-stu-id="1bb9b-155">You can also override the namespace for just the DbContext class using `-ContextNamespace`.</span></span>

``` powershell
Scaffold-DbContext ... -Namespace Your.Namespace -ContextNamespace Your.DbContext.Namespace
```

***

## <a name="how-it-works"></a><span data-ttu-id="1bb9b-156">Funktionsweise</span><span class="sxs-lookup"><span data-stu-id="1bb9b-156">How it works</span></span>

<span data-ttu-id="1bb9b-157">Reverse Engineering beginnt mit dem Lesen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-157">Reverse engineering starts by reading the database schema.</span></span> <span data-ttu-id="1bb9b-158">Er liest Informationen zu Tabellen, Spalten, Einschränkungen und Indizes.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-158">It reads information about tables, columns, constraints, and indexes.</span></span>

<span data-ttu-id="1bb9b-159">Anschließend werden die Schema Informationen verwendet, um ein EF Core Modell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-159">Next, it uses the schema information to create an EF Core model.</span></span> <span data-ttu-id="1bb9b-160">Tabellen werden verwendet, um Entitäts Typen zu erstellen. Spalten werden verwendet, um Eigenschaften zu erstellen; und Fremdschlüssel werden zum Erstellen von Beziehungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-160">Tables are used to create entity types; columns are used to create properties; and foreign keys are used to create relationships.</span></span>

<span data-ttu-id="1bb9b-161">Schließlich wird das Modell verwendet, um Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-161">Finally, the model is used to generate code.</span></span> <span data-ttu-id="1bb9b-162">Die entsprechenden Entitätstyp Klassen, die fließende API und Daten Anmerkungen werden erstellt, um das gleiche Modell aus Ihrer APP neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-162">The corresponding entity type classes, Fluent API, and data annotations are scaffolded in order to re-create the same model from your app.</span></span>

## <a name="limitations"></a><span data-ttu-id="1bb9b-163">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="1bb9b-163">Limitations</span></span>

* <span data-ttu-id="1bb9b-164">Nicht alles über ein Modell kann mithilfe eines Datenbankschemas dargestellt werden.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-164">Not everything about a model can be represented using a database schema.</span></span> <span data-ttu-id="1bb9b-165">Beispielsweise sind Informationen zu [**Vererbungs Hierarchien**](../modeling/inheritance.md), [**eigenen Typen**](../modeling/owned-entities.md)und [**Tabellen Aufteilung**](../modeling/table-splitting.md) im Datenbankschema nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-165">For example, information about [**inheritance hierarchies**](../modeling/inheritance.md), [**owned types**](../modeling/owned-entities.md), and [**table splitting**](../modeling/table-splitting.md) are not present in the database schema.</span></span> <span data-ttu-id="1bb9b-166">Aus diesem Grund werden diese Konstrukte nie in umgekehrter Reihenfolge entwickelt.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-166">Because of this, these constructs will never be reverse engineered.</span></span>
* <span data-ttu-id="1bb9b-167">Außerdem werden **einige Spaltentypen** möglicherweise vom EF Core Anbieter nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-167">In addition, **some column types** may not be supported by the EF Core provider.</span></span> <span data-ttu-id="1bb9b-168">Diese Spalten werden nicht in das Modell eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-168">These columns won't be included in the model.</span></span>
* <span data-ttu-id="1bb9b-169">Sie können Parallelitäts [**Token**](../modeling/concurrency.md)in einem EF Core Modell definieren, um zu verhindern, dass zwei Benutzer gleichzeitig dieselbe Entität aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-169">You can define [**concurrency tokens**](../modeling/concurrency.md), in an EF Core model to prevent two users from updating the same entity at the same time.</span></span> <span data-ttu-id="1bb9b-170">Einige Datenbanken verfügen über einen besonderen Typ, der diese Art von Spalte darstellt (z. b. rowversion in SQL Server). in diesem Fall können wir diese Informationen umkehren. andere Parallelitäts Token werden jedoch nicht in umgekehrter Reihenfolge entwickelt.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-170">Some databases have a special type to represent this type of column (for example, rowversion in SQL Server) in which case we can reverse engineer this information; however, other concurrency tokens will not be reverse engineered.</span></span>
* <span data-ttu-id="1bb9b-171">[Der Referenztyp c# 8, der NULL-Werte](/dotnet/csharp/tutorials/nullable-reference-types) zulässt, wird derzeit in Reverse Engineering nicht unterstützt: EF Core generiert immer c#-Code, der annimmt, dass das Feature deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-171">[The C# 8 nullable reference type feature](/dotnet/csharp/tutorials/nullable-reference-types) is currently unsupported in reverse engineering: EF Core always generates C# code that assumes the feature is disabled.</span></span> <span data-ttu-id="1bb9b-172">Beispielsweise werden Textspalten, die NULL-Werte zulassen, als Eigenschaft mit dem Typ `string` erstellt, nicht `string?` mit der fließenden API oder den Daten Anmerkungen, mit denen konfiguriert wird, ob eine Eigenschaft erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-172">For example, nullable text columns will be scaffolded as a property with type `string` , not `string?`, with either the Fluent API or Data Annotations used to configure whether a property is required or not.</span></span> <span data-ttu-id="1bb9b-173">Sie können den Gerüst Code bearbeiten und durch c#-Anmerkungen zur NULL-Zulässigkeit ersetzen.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-173">You can edit the scaffolded code and replace these with C# nullability annotations.</span></span> <span data-ttu-id="1bb9b-174">Die Gerüstbau Unterstützung für Verweis Typen, die NULL-Werte zulassen, wird von Issue [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520)</span><span class="sxs-lookup"><span data-stu-id="1bb9b-174">Scaffolding support for nullable reference types is tracked by issue [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).</span></span>

## <a name="customizing-the-model"></a><span data-ttu-id="1bb9b-175">Anpassen des Modells</span><span class="sxs-lookup"><span data-stu-id="1bb9b-175">Customizing the model</span></span>

<span data-ttu-id="1bb9b-176">Der von EF Core generierte Code ist Ihr Code.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-176">The code generated by EF Core is your code.</span></span> <span data-ttu-id="1bb9b-177">Sie können es jederzeit ändern.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-177">Feel free to change it.</span></span> <span data-ttu-id="1bb9b-178">Sie wird nur dann erneut generiert, wenn Sie das gleiche Modell erneut umkehren.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-178">It will only be regenerated if you reverse engineer the same model again.</span></span> <span data-ttu-id="1bb9b-179">Der Gerüst Code stellt *ein* Modell dar, das für den Zugriff auf die Datenbank verwendet werden kann. es ist jedoch sicherlich nicht das *einzige* Modell, das verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-179">The scaffolded code represents *one* model that can be used to access the database, but it's certainly not the *only* model that can be used.</span></span>

<span data-ttu-id="1bb9b-180">Passen Sie die Entitätstyp Klassen und die dbcontext-Klasse an Ihre Anforderungen an.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-180">Customize the entity type classes and DbContext class to fit your needs.</span></span> <span data-ttu-id="1bb9b-181">Sie können z. b. Typen und Eigenschaften umbenennen, Vererbungs Hierarchien einführen oder eine Tabelle in mehrere Entitäten aufteilen.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-181">For example, you may choose to rename types and properties, introduce inheritance hierarchies, or split a table into to multiple entities.</span></span> <span data-ttu-id="1bb9b-182">Sie können auch nicht eindeutige Indizes, nicht verwendete Sequenzen und Navigations Eigenschaften, optionale skalare Eigenschaften und Einschränkungs Namen aus dem Modell entfernen.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-182">You can also remove non-unique indexes, unused sequences and navigation properties, optional scalar properties, and constraint names from the model.</span></span>

<span data-ttu-id="1bb9b-183">Sie können auch zusätzliche Konstruktoren, Methoden, Eigenschaften usw. hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-183">You can also add additional constructors, methods, properties, etc.</span></span> <span data-ttu-id="1bb9b-184">Verwenden einer anderen partiellen Klasse in einer separaten Datei.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-184">using another partial class in a separate file.</span></span> <span data-ttu-id="1bb9b-185">Diese Vorgehensweise funktioniert auch, wenn Sie das Modell erneut umkehren möchten.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-185">This approach works even when you intend to reverse engineer the model again.</span></span>

## <a name="updating-the-model"></a><span data-ttu-id="1bb9b-186">Aktualisieren des Modells</span><span class="sxs-lookup"><span data-stu-id="1bb9b-186">Updating the model</span></span>

<span data-ttu-id="1bb9b-187">Nachdem Sie Änderungen an der Datenbank vorgenommen haben, müssen Sie möglicherweise das EF Core Modell aktualisieren, um diese Änderungen widerzuspiegeln.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-187">After making changes to the database, you may need to update your EF Core model to reflect those changes.</span></span> <span data-ttu-id="1bb9b-188">Wenn die Daten Bank Änderungen einfach sind, ist es möglicherweise am einfachsten, die Änderungen am EF Core Modell manuell vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-188">If the database changes are simple, it may be easiest just to manually make the changes to your EF Core model.</span></span> <span data-ttu-id="1bb9b-189">Beispielsweise sind das Umbenennen einer Tabelle oder Spalte, das Entfernen einer Spalte oder das Aktualisieren eines Spalten Typs triviale Änderungen, die im Code vorgenommen werden müssen.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-189">For example, renaming a table or column, removing a column, or updating a column's type are trivial changes to make in code.</span></span>

<span data-ttu-id="1bb9b-190">Bedeutendere Änderungen sind jedoch nicht so einfach wie die manuelle Ausführung.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-190">More significant changes, however, are not as easy make manually.</span></span> <span data-ttu-id="1bb9b-191">Ein allgemeiner Workflow besteht darin, das Modell mithilfe von `-Force` (PMC) oder (CLI) erneut aus der Datenbank `--force` zu entwickeln, um das vorhandene Modell mit einem aktualisierten zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-191">One common workflow is to reverse engineer the model from the database again using `-Force` (PMC) or `--force` (CLI) to overwrite the existing model with an updated one.</span></span>

<span data-ttu-id="1bb9b-192">Ein weiteres häufig angefordertes Feature ist die Möglichkeit, das Modell aus der Datenbank zu aktualisieren und gleichzeitig Anpassungen wie Umbenennungen, Typhierarchien usw. beizubehalten. Verwenden Sie das Problem [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) , um den Fortschritt dieses Features zu verfolgen.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-192">Another commonly requested feature is the ability to update the model from the database while preserving customization like renames, type hierarchies, etc. Use issue [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) to track the progress of this feature.</span></span>

> [!WARNING]
> <span data-ttu-id="1bb9b-193">Wenn Sie das Modell erneut aus der Datenbank zurück entwickeln, gehen alle Änderungen, die Sie an den Dateien vorgenommen haben, verloren.</span><span class="sxs-lookup"><span data-stu-id="1bb9b-193">If you reverse engineer the model from the database again, any changes you've made to the files will be lost.</span></span>
