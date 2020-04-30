---
title: Reverse Engineering-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 41204de8cd8c4f292afa16b209eb5302eaf74905
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538440"
---
# <a name="reverse-engineering"></a><span data-ttu-id="acd55-102"> Reverse Engineering</span><span class="sxs-lookup"><span data-stu-id="acd55-102">Reverse Engineering</span></span>

<span data-ttu-id="acd55-103">Reverse Engineering ist der Prozess der Gerüstbau von Entitäts Typen Klassen und einer dbcontext-Klasse auf Grundlage eines Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="acd55-103">Reverse engineering is the process of scaffolding entity type classes and a DbContext class based on a database schema.</span></span> <span data-ttu-id="acd55-104">Dies kann mit dem `Scaffold-DbContext` Befehl der EF Core Package Manager Console-Konsole (PMC) oder dem `dotnet ef dbcontext scaffold` Befehl der .net-Befehlszeilenschnittstelle (CLI) ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="acd55-104">It can be performed using the `Scaffold-DbContext` command of the EF Core Package Manager Console (PMC) tools or the `dotnet ef dbcontext scaffold` command of the .NET Command-line Interface (CLI) tools.</span></span>

## <a name="installing"></a><span data-ttu-id="acd55-105">Installation</span><span class="sxs-lookup"><span data-stu-id="acd55-105">Installing</span></span>

<span data-ttu-id="acd55-106">Bevor Reverse Engineering, müssen Sie entweder die [PMC-Tools](xref:core/miscellaneous/cli/powershell) (nur Visual Studio) oder die CLI- [Tools](xref:core/miscellaneous/cli/dotnet)installieren.</span><span class="sxs-lookup"><span data-stu-id="acd55-106">Before reverse engineering, you'll need to install either the [PMC tools](xref:core/miscellaneous/cli/powershell) (Visual Studio only) or the [CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span> <span data-ttu-id="acd55-107">Weitere Informationen finden Sie unter Links.</span><span class="sxs-lookup"><span data-stu-id="acd55-107">See links for details.</span></span>

<span data-ttu-id="acd55-108">Außerdem müssen Sie einen geeigneten [Datenbankanbieter](xref:core/providers/index) für das Datenbankschema installieren, das Sie rückgängig machen möchten.</span><span class="sxs-lookup"><span data-stu-id="acd55-108">You'll also need to install an appropriate [database provider](xref:core/providers/index) for the database schema you want to reverse engineer.</span></span>

## <a name="connection-string"></a><span data-ttu-id="acd55-109">Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="acd55-109">Connection string</span></span>

<span data-ttu-id="acd55-110">Das erste Argument für den Befehl ist eine Verbindungs Zeichenfolge für die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="acd55-110">The first argument to the command is a connection string to the database.</span></span> <span data-ttu-id="acd55-111">Diese Verbindungs Zeichenfolge wird von den-Tools zum Lesen des Datenbankschemas verwendet.</span><span class="sxs-lookup"><span data-stu-id="acd55-111">The tools will use this connection string to read the database schema.</span></span>

<span data-ttu-id="acd55-112">Wie Sie die Verbindungs Zeichenfolge zitieren und mit Escapezeichen versehen, hängt davon ab, welche Shell Sie zum Ausführen des Befehls verwenden.</span><span class="sxs-lookup"><span data-stu-id="acd55-112">How you quote and escape the connection string depends on which shell you are using to execute the command.</span></span> <span data-ttu-id="acd55-113">Einzelheiten finden Sie in der Dokumentation Ihrer Shell.</span><span class="sxs-lookup"><span data-stu-id="acd55-113">Refer to your shell's documentation for specifics.</span></span> <span data-ttu-id="acd55-114">Beispielsweise erfordert PowerShell, dass Sie das `$` Zeichen mit Escapezeichen `\`versehen.</span><span class="sxs-lookup"><span data-stu-id="acd55-114">For example, PowerShell requires you to escape the `$` character, but not `\`.</span></span>

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="configuration-and-user-secrets"></a><span data-ttu-id="acd55-115">Konfiguration und Benutzer Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="acd55-115">Configuration and User Secrets</span></span>

<span data-ttu-id="acd55-116">Wenn Sie ein ASP.net Core-Projekt haben, können Sie die `Name=<connection-string>` -Syntax verwenden, um die Verbindungs Zeichenfolge aus der Konfiguration zu lesen.</span><span class="sxs-lookup"><span data-stu-id="acd55-116">If you have an ASP.NET Core project, you can use the `Name=<connection-string>` syntax to read the connection string from configuration.</span></span>

<span data-ttu-id="acd55-117">Dies funktioniert gut mit dem [Geheimnis-Manager-Tool](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) , um das Daten Bank Kennwort von ihrer CodeBase getrennt zu halten.</span><span class="sxs-lookup"><span data-stu-id="acd55-117">This works well with the [Secret Manager tool](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) to keep your database password separate from your codebase.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a><span data-ttu-id="acd55-118">Anbietername</span><span class="sxs-lookup"><span data-stu-id="acd55-118">Provider name</span></span>

<span data-ttu-id="acd55-119">Das zweite Argument ist der Anbieter Name.</span><span class="sxs-lookup"><span data-stu-id="acd55-119">The second argument is the provider name.</span></span> <span data-ttu-id="acd55-120">Der Anbieter Name ist in der Regel mit dem nuget-Paketnamen des Anbieters identisch.</span><span class="sxs-lookup"><span data-stu-id="acd55-120">The provider name is typically the same as the provider's NuGet package name.</span></span>

## <a name="specifying-tables"></a><span data-ttu-id="acd55-121">Angeben von Tabellen</span><span class="sxs-lookup"><span data-stu-id="acd55-121">Specifying tables</span></span>

<span data-ttu-id="acd55-122">Alle Tabellen im Datenbankschema werden standardmäßig in Entitäts Typen entwickelt.</span><span class="sxs-lookup"><span data-stu-id="acd55-122">All tables in the database schema are reverse engineered into entity types by default.</span></span> <span data-ttu-id="acd55-123">Sie können einschränken, welche Tabellen in umgekehrter Reihenfolge entwickelt werden, indem Sie Schemas und Tabellen angeben.</span><span class="sxs-lookup"><span data-stu-id="acd55-123">You can limit which tables are reverse engineered by specifying schemas and tables.</span></span>

<span data-ttu-id="acd55-124">Der `-Schemas` -Parameter in der PMC `--schema` und die-Option in der CLI können verwendet werden, um jede Tabelle innerhalb eines Schemas einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="acd55-124">The `-Schemas` parameter in PMC and the `--schema` option in the CLI can be used to include every table within a schema.</span></span>

<span data-ttu-id="acd55-125">`-Tables`(PMC) und `--table` (CLI) können verwendet werden, um bestimmte Tabellen einzuschließen.</span><span class="sxs-lookup"><span data-stu-id="acd55-125">`-Tables` (PMC) and `--table` (CLI) can be used to include specific tables.</span></span>

<span data-ttu-id="acd55-126">Wenn Sie mehrere Tabellen in die PMC einschließen möchten, verwenden Sie ein Array.</span><span class="sxs-lookup"><span data-stu-id="acd55-126">To include multiple tables in PMC, use an array.</span></span>

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

<span data-ttu-id="acd55-127">Wenn Sie mehrere Tabellen in der CLI einschließen möchten, müssen Sie die Option mehrmals angeben.</span><span class="sxs-lookup"><span data-stu-id="acd55-127">To include multiple tables in the CLI, specify the option multiple times.</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

## <a name="preserving-names"></a><span data-ttu-id="acd55-128">Beibehalten von Namen</span><span class="sxs-lookup"><span data-stu-id="acd55-128">Preserving names</span></span>

<span data-ttu-id="acd55-129">Tabellen-und Spaltennamen werden korrigiert, um die .net-Benennungs Konventionen für Typen und Eigenschaften standardmäßig besser abzugleichen.</span><span class="sxs-lookup"><span data-stu-id="acd55-129">Table and column names are fixed up to better match the .NET naming conventions for types and properties by default.</span></span> <span data-ttu-id="acd55-130">Wenn Sie `-UseDatabaseNames` den Switch in der PMC `--use-database-names` oder die Option in der CLI angeben, wird dieses Verhalten deaktiviert, sodass die ursprünglichen Datenbanknamen so weit wie möglich beibehalten werden.</span><span class="sxs-lookup"><span data-stu-id="acd55-130">Specifying the `-UseDatabaseNames` switch in PMC or the `--use-database-names` option in the CLI will disable this behavior preserving the original database names as much as possible.</span></span> <span data-ttu-id="acd55-131">Ungültige .net-Bezeichner sind immer noch korrigiert, und synthetisierte Namen wie Navigations Eigenschaften entsprechen weiterhin den .net-Benennungs Konventionen.</span><span class="sxs-lookup"><span data-stu-id="acd55-131">Invalid .NET identifiers will still be fixed and synthesized names like navigation properties will still conform to .NET naming conventions.</span></span>

## <a name="fluent-api-or-data-annotations"></a><span data-ttu-id="acd55-132">Fließende API oder Daten Anmerkungen</span><span class="sxs-lookup"><span data-stu-id="acd55-132">Fluent API or Data Annotations</span></span>

<span data-ttu-id="acd55-133">Entitäts Typen werden standardmäßig mit der flüssigen API konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="acd55-133">Entity types are configured using the Fluent API by default.</span></span> <span data-ttu-id="acd55-134">Geben `-DataAnnotations` Sie (PMC) `--data-annotations` oder (CLI) an, um Daten Anmerkungen zu verwenden, sofern möglich.</span><span class="sxs-lookup"><span data-stu-id="acd55-134">Specify `-DataAnnotations` (PMC) or `--data-annotations` (CLI) to instead use data annotations when possible.</span></span>

<span data-ttu-id="acd55-135">Beispielsweise wird das Gerüst mithilfe der fließenden API erstellt:</span><span class="sxs-lookup"><span data-stu-id="acd55-135">For example, using the Fluent API will scaffold this:</span></span>

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

<span data-ttu-id="acd55-136">Beim Verwenden von Daten Anmerkungen wird dieses Gerüst erstellt:</span><span class="sxs-lookup"><span data-stu-id="acd55-136">While using Data Annotations will scaffold this:</span></span>

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a><span data-ttu-id="acd55-137">Dbcontext-Name</span><span class="sxs-lookup"><span data-stu-id="acd55-137">DbContext name</span></span>

<span data-ttu-id="acd55-138">Der Name der dbcontext-Klasse mit dem Gerüst ist der Name der Datenbank, der standardmäßig mit dem *Kontext* versehen wird.</span><span class="sxs-lookup"><span data-stu-id="acd55-138">The scaffolded DbContext class name will be the name of the database suffixed with *Context* by default.</span></span> <span data-ttu-id="acd55-139">Verwenden `-Context` Sie in der PMC und `--context` in der CLI, um eine andere anzugeben.</span><span class="sxs-lookup"><span data-stu-id="acd55-139">To specify a different one, use `-Context` in PMC and `--context` in the CLI.</span></span>

## <a name="directories-and-namespaces"></a><span data-ttu-id="acd55-140">Verzeichnisse und Namespaces</span><span class="sxs-lookup"><span data-stu-id="acd55-140">Directories and namespaces</span></span>

<span data-ttu-id="acd55-141">Die Entitäts Klassen und eine dbcontext-Klasse werden im Stammverzeichnis des Projekts erstellt und verwenden den Standard Namespace des Projekts.</span><span class="sxs-lookup"><span data-stu-id="acd55-141">The entity classes and a DbContext class are scaffolded into the project's root directory and use the project's default namespace.</span></span> <span data-ttu-id="acd55-142">Sie können das Verzeichnis angeben, in dem Klassen mit einem Gerüst `-OutputDir` mithilfe von (PMC `--output-dir` ) oder (CLI) erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="acd55-142">You can specify the directory where classes are scaffolded using `-OutputDir` (PMC) or `--output-dir` (CLI).</span></span>

<span data-ttu-id="acd55-143">Sie können auch ( `-ContextDir` PMC) und ( `--context-dir` CLI) verwenden, um die dbcontext-Klasse in ein separates Verzeichnis der Entitätstyp Klassen zu setzen.</span><span class="sxs-lookup"><span data-stu-id="acd55-143">You can also use `-ContextDir` (PMC) and `--context-dir` (CLI) to scaffold the DbContext class into a separate directory from the entity type classes.</span></span>

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

 <span data-ttu-id="acd55-144">Standardmäßig ist der Namespace der Stamm Namespace und die Namen aller Unterverzeichnisse im Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="acd55-144">By default, the namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span> <span data-ttu-id="acd55-145">Allerdings können Sie den Namespace für alle Ausgabe Klassen mithilfe `-Namespace` von (PMC) oder `--namespace` (CLI) überschreiben.</span><span class="sxs-lookup"><span data-stu-id="acd55-145">However you can override the namespace for all output classes by using `-Namespace` (PMC) or `--namespace` (CLI).</span></span> <span data-ttu-id="acd55-146">Sie können auch den Namespace nur für die dbcontext-Klasse mithilfe `-ContextNamespace` von (PMC) `--context-namespace` oder (CLI) überschreiben.</span><span class="sxs-lookup"><span data-stu-id="acd55-146">You can also override the namespace for just the DbContext class using `-ContextNamespace` (PMC) or `--context-namespace` (CLI).</span></span>

``` powershell
Scaffold-DbContext ... -Namespace Your.Namespace -ContextNamespace Your.DbContext.Namespace
```

```dotnetcli
dotnet ef dbcontext scaffold ... --namespace Your.Namespace --context-namespace Your.DbContext.Namespace
```

## <a name="how-it-works"></a><span data-ttu-id="acd55-147">Funktionsweise</span><span class="sxs-lookup"><span data-stu-id="acd55-147">How it works</span></span>

<span data-ttu-id="acd55-148">Reverse Engineering beginnt mit dem Lesen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="acd55-148">Reverse engineering starts by reading the database schema.</span></span> <span data-ttu-id="acd55-149">Er liest Informationen zu Tabellen, Spalten, Einschränkungen und Indizes.</span><span class="sxs-lookup"><span data-stu-id="acd55-149">It reads information about tables, columns, constraints, and indexes.</span></span>

<span data-ttu-id="acd55-150">Anschließend werden die Schema Informationen verwendet, um ein EF Core Modell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="acd55-150">Next, it uses the schema information to create an EF Core model.</span></span> <span data-ttu-id="acd55-151">Tabellen werden verwendet, um Entitäts Typen zu erstellen. Spalten werden verwendet, um Eigenschaften zu erstellen; und Fremdschlüssel werden zum Erstellen von Beziehungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="acd55-151">Tables are used to create entity types; columns are used to create properties; and foreign keys are used to create relationships.</span></span>

<span data-ttu-id="acd55-152">Schließlich wird das Modell verwendet, um Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="acd55-152">Finally, the model is used to generate code.</span></span> <span data-ttu-id="acd55-153">Die entsprechenden Entitätstyp Klassen, die fließende API und Daten Anmerkungen werden erstellt, um das gleiche Modell aus Ihrer APP neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="acd55-153">The corresponding entity type classes, Fluent API, and data annotations are scaffolded in order to re-create the same model from your app.</span></span>

## <a name="limitations"></a><span data-ttu-id="acd55-154">Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="acd55-154">Limitations</span></span>

* <span data-ttu-id="acd55-155">Nicht alles über ein Modell kann mithilfe eines Datenbankschemas dargestellt werden.</span><span class="sxs-lookup"><span data-stu-id="acd55-155">Not everything about a model can be represented using a database schema.</span></span> <span data-ttu-id="acd55-156">Beispielsweise sind Informationen zu [**Vererbungs Hierarchien**](../modeling/inheritance.md), [**eigenen Typen**](../modeling/owned-entities.md)und [**Tabellen Aufteilung**](../modeling/table-splitting.md) im Datenbankschema nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="acd55-156">For example, information about [**inheritance hierarchies**](../modeling/inheritance.md), [**owned types**](../modeling/owned-entities.md), and [**table splitting**](../modeling/table-splitting.md) are not present in the database schema.</span></span> <span data-ttu-id="acd55-157">Aus diesem Grund werden diese Konstrukte nie in umgekehrter Reihenfolge entwickelt.</span><span class="sxs-lookup"><span data-stu-id="acd55-157">Because of this, these constructs will never be reverse engineered.</span></span>
* <span data-ttu-id="acd55-158">Außerdem werden **einige Spaltentypen** möglicherweise vom EF Core Anbieter nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="acd55-158">In addition, **some column types** may not be supported by the EF Core provider.</span></span> <span data-ttu-id="acd55-159">Diese Spalten werden nicht in das Modell eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="acd55-159">These columns won't be included in the model.</span></span>
* <span data-ttu-id="acd55-160">Sie können Parallelitäts [**Token**](../modeling/concurrency.md)in einem EF Core Modell definieren, um zu verhindern, dass zwei Benutzer gleichzeitig dieselbe Entität aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="acd55-160">You can define [**concurrency tokens**](../modeling/concurrency.md), in an EF Core model to prevent two users from updating the same entity at the same time.</span></span> <span data-ttu-id="acd55-161">Einige Datenbanken verfügen über einen besonderen Typ, der diese Art von Spalte darstellt (z. b. rowversion in SQL Server). in diesem Fall können wir diese Informationen umkehren. andere Parallelitäts Token werden jedoch nicht in umgekehrter Reihenfolge entwickelt.</span><span class="sxs-lookup"><span data-stu-id="acd55-161">Some databases have a special type to represent this type of column (for example, rowversion in SQL Server) in which case we can reverse engineer this information; however, other concurrency tokens will not be reverse engineered.</span></span>
* <span data-ttu-id="acd55-162">[Der Referenztyp c# 8, der NULL-Werte](/dotnet/csharp/tutorials/nullable-reference-types) zulässt, wird derzeit in Reverse Engineering nicht unterstützt: EF Core generiert immer c#-Code, der annimmt, dass das Feature deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="acd55-162">[The C# 8 nullable reference type feature](/dotnet/csharp/tutorials/nullable-reference-types) is currently unsupported in reverse engineering: EF Core always generates C# code that assumes the feature is disabled.</span></span> <span data-ttu-id="acd55-163">Beispielsweise werden Textspalten, die NULL-Werte zulassen, als Eigenschaft mit dem Typ `string` erstellt, `string?`nicht mit der fließenden API oder den Daten Anmerkungen, mit denen konfiguriert wird, ob eine Eigenschaft erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="acd55-163">For example, nullable text columns will be scaffolded as a property with type `string` , not `string?`, with either the Fluent API or Data Annotations used to configure whether a property is required or not.</span></span> <span data-ttu-id="acd55-164">Sie können den Gerüst Code bearbeiten und durch c#-Anmerkungen zur NULL-Zulässigkeit ersetzen.</span><span class="sxs-lookup"><span data-stu-id="acd55-164">You can edit the scaffolded code and replace these with C# nullability annotations.</span></span> <span data-ttu-id="acd55-165">Die Gerüstbau Unterstützung für Verweis Typen, die NULL-Werte zulassen, wird von Issue [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520)</span><span class="sxs-lookup"><span data-stu-id="acd55-165">Scaffolding support for nullable reference types is tracked by issue [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).</span></span>

## <a name="customizing-the-model"></a><span data-ttu-id="acd55-166">Anpassen des Modells</span><span class="sxs-lookup"><span data-stu-id="acd55-166">Customizing the model</span></span>

<span data-ttu-id="acd55-167">Der von EF Core generierte Code ist Ihr Code.</span><span class="sxs-lookup"><span data-stu-id="acd55-167">The code generated by EF Core is your code.</span></span> <span data-ttu-id="acd55-168">Sie können es jederzeit ändern.</span><span class="sxs-lookup"><span data-stu-id="acd55-168">Feel free to change it.</span></span> <span data-ttu-id="acd55-169">Sie wird nur dann erneut generiert, wenn Sie das gleiche Modell erneut umkehren.</span><span class="sxs-lookup"><span data-stu-id="acd55-169">It will only be regenerated if you reverse engineer the same model again.</span></span> <span data-ttu-id="acd55-170">Der Gerüst Code stellt *ein* Modell dar, das für den Zugriff auf die Datenbank verwendet werden kann. es ist jedoch sicherlich nicht das *einzige* Modell, das verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="acd55-170">The scaffolded code represents *one* model that can be used to access the database, but it's certainly not the *only* model that can be used.</span></span>

<span data-ttu-id="acd55-171">Passen Sie die Entitätstyp Klassen und die dbcontext-Klasse an Ihre Anforderungen an.</span><span class="sxs-lookup"><span data-stu-id="acd55-171">Customize the entity type classes and DbContext class to fit your needs.</span></span> <span data-ttu-id="acd55-172">Sie können z. b. Typen und Eigenschaften umbenennen, Vererbungs Hierarchien einführen oder eine Tabelle in mehrere Entitäten aufteilen.</span><span class="sxs-lookup"><span data-stu-id="acd55-172">For example, you may choose to rename types and properties, introduce inheritance hierarchies, or split a table into to multiple entities.</span></span> <span data-ttu-id="acd55-173">Sie können auch nicht eindeutige Indizes, nicht verwendete Sequenzen und Navigations Eigenschaften, optionale skalare Eigenschaften und Einschränkungs Namen aus dem Modell entfernen.</span><span class="sxs-lookup"><span data-stu-id="acd55-173">You can also remove non-unique indexes, unused sequences and navigation properties, optional scalar properties, and constraint names from the model.</span></span>

<span data-ttu-id="acd55-174">Sie können auch zusätzliche Konstruktoren, Methoden, Eigenschaften usw. hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="acd55-174">You can also add additional constructors, methods, properties, etc.</span></span> <span data-ttu-id="acd55-175">Verwenden einer anderen partiellen Klasse in einer separaten Datei.</span><span class="sxs-lookup"><span data-stu-id="acd55-175">using another partial class in a separate file.</span></span> <span data-ttu-id="acd55-176">Diese Vorgehensweise funktioniert auch, wenn Sie das Modell erneut umkehren möchten.</span><span class="sxs-lookup"><span data-stu-id="acd55-176">This approach works even when you intend to reverse engineer the model again.</span></span>

## <a name="updating-the-model"></a><span data-ttu-id="acd55-177">Aktualisieren des Modells</span><span class="sxs-lookup"><span data-stu-id="acd55-177">Updating the model</span></span>

<span data-ttu-id="acd55-178">Nachdem Sie Änderungen an der Datenbank vorgenommen haben, müssen Sie möglicherweise das EF Core Modell aktualisieren, um diese Änderungen widerzuspiegeln.</span><span class="sxs-lookup"><span data-stu-id="acd55-178">After making changes to the database, you may need to update your EF Core model to reflect those changes.</span></span> <span data-ttu-id="acd55-179">Wenn die Daten Bank Änderungen einfach sind, ist es möglicherweise am einfachsten, die Änderungen am EF Core Modell manuell vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="acd55-179">If the database changes are simple, it may be easiest just to manually make the changes to your EF Core model.</span></span> <span data-ttu-id="acd55-180">Beispielsweise sind das Umbenennen einer Tabelle oder Spalte, das Entfernen einer Spalte oder das Aktualisieren eines Spalten Typs triviale Änderungen, die im Code vorgenommen werden müssen.</span><span class="sxs-lookup"><span data-stu-id="acd55-180">For example, renaming a table or column, removing a column, or updating a column's type are trivial changes to make in code.</span></span>

<span data-ttu-id="acd55-181">Bedeutendere Änderungen sind jedoch nicht so einfach wie die manuelle Ausführung.</span><span class="sxs-lookup"><span data-stu-id="acd55-181">More significant changes, however, are not as easy make manually.</span></span> <span data-ttu-id="acd55-182">Ein allgemeiner Workflow besteht darin, das Modell mithilfe `-Force` von (PMC) oder `--force` (CLI) erneut aus der Datenbank zu entwickeln, um das vorhandene Modell mit einem aktualisierten zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="acd55-182">One common workflow is to reverse engineer the model from the database again using `-Force` (PMC) or `--force` (CLI) to overwrite the existing model with an updated one.</span></span>

<span data-ttu-id="acd55-183">Ein weiteres häufig angefordertes Feature ist die Möglichkeit, das Modell aus der Datenbank zu aktualisieren und gleichzeitig Anpassungen wie Umbenennungen, Typhierarchien usw. beizubehalten. Verwenden Sie das Problem [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) , um den Fortschritt dieses Features zu verfolgen.</span><span class="sxs-lookup"><span data-stu-id="acd55-183">Another commonly requested feature is the ability to update the model from the database while preserving customization like renames, type hierarchies, etc. Use issue [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) to track the progress of this feature.</span></span>

> [!WARNING]
> <span data-ttu-id="acd55-184">Wenn Sie das Modell erneut aus der Datenbank zurück entwickeln, gehen alle Änderungen, die Sie an den Dateien vorgenommen haben, verloren.</span><span class="sxs-lookup"><span data-stu-id="acd55-184">If you reverse engineer the model from the database again, any changes you've made to the files will be lost.</span></span>
