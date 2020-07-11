---
title: Anwenden von Migrationen-EF Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: f83a014651fdf2262a603fb91da7984672690197
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238723"
---
# <a name="applying-migrations"></a><span data-ttu-id="7dd88-102">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="7dd88-102">Applying Migrations</span></span>

<span data-ttu-id="7dd88-103">Nachdem Ihre Migrationen hinzugefügt wurden, müssen Sie bereitgestellt und auf Ihre Datenbanken angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="7dd88-103">Once your migrations have been added, they need to be deployed and applied to your databases.</span></span> <span data-ttu-id="7dd88-104">Hierfür gibt es verschiedene Strategien, die für Produktionsumgebungen besser geeignet sind, und andere für den Entwicklungs Lebenszyklus.</span><span class="sxs-lookup"><span data-stu-id="7dd88-104">There are various strategies for doing this, with some being more appropriate for production environments, and others for the development lifecycle.</span></span>

> [!NOTE]
> <span data-ttu-id="7dd88-105">Ungeachtet der Bereitstellungs Strategie sollten Sie immer die generierten Migrationen untersuchen und testen, bevor Sie Sie auf eine Produktionsdatenbank anwenden.</span><span class="sxs-lookup"><span data-stu-id="7dd88-105">Whatever your deployment strategy, always inspect the generated migrations and test them before applying to a production database.</span></span> <span data-ttu-id="7dd88-106">Eine Migration kann eine Spalte löschen, wenn die Absicht darin besteht, Sie umzubenennen, oder aus verschiedenen Gründen fehlschlagen, wenn Sie auf eine Datenbank angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="7dd88-106">A migration may drop a column when the intent was to rename it, or may fail for various reasons when applied to a database.</span></span>

## <a name="sql-scripts"></a><span data-ttu-id="7dd88-107">SQL-Skripts</span><span class="sxs-lookup"><span data-stu-id="7dd88-107">SQL scripts</span></span>

<span data-ttu-id="7dd88-108">Die empfohlene Vorgehensweise zum Bereitstellen von Migrationen in eine Produktionsdatenbank ist das Erstellen von SQL-Skripts.</span><span class="sxs-lookup"><span data-stu-id="7dd88-108">The recommended way to deploy migrations to a production database is by generating SQL scripts.</span></span> <span data-ttu-id="7dd88-109">Diese Strategie bietet folgende Vorteile:</span><span class="sxs-lookup"><span data-stu-id="7dd88-109">The advantages of this strategy include the following:</span></span>

* <span data-ttu-id="7dd88-110">SQL-Skripts können auf Genauigkeit überprüft werden. Dies ist wichtig, da das Anwenden von Schema Änderungen auf Produktionsdatenbanken einen potenziell gefährlichen Vorgang verursachen kann, der zu Datenverlusten führen kann.</span><span class="sxs-lookup"><span data-stu-id="7dd88-110">SQL scripts can be reviewed for accuracy; this is important since applying schema changes to production databases is a potentially dangerous operation that could involve data loss.</span></span>
* <span data-ttu-id="7dd88-111">In einigen Fällen können die Skripts an die spezifischen Anforderungen einer Produktionsdatenbank angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="7dd88-111">In some cases, the scripts can be tuned to fit the specific needs of a production database.</span></span>
* <span data-ttu-id="7dd88-112">SQL-Skripts können in Verbindung mit einer Bereitstellungs Technologie verwendet werden und können sogar als Teil Ihres CI-Prozesses generiert werden.</span><span class="sxs-lookup"><span data-stu-id="7dd88-112">SQL scripts can be used in conjunction with a deployment technology, and can even be generated as part of your CI process.</span></span>
* <span data-ttu-id="7dd88-113">SQL-Skripts können einem Datenbankadministrator bereitgestellt werden und können separat verwaltet und archiviert werden.</span><span class="sxs-lookup"><span data-stu-id="7dd88-113">SQL scripts can be provided to a DBA, and can be managed and archived separately.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="7dd88-114">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="7dd88-114">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a><span data-ttu-id="7dd88-115">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="7dd88-115">Basic Usage</span></span>

<span data-ttu-id="7dd88-116">Im folgenden wird ein SQL-Skript aus einer leeren Datenbank mit der neuesten Migration generiert:</span><span class="sxs-lookup"><span data-stu-id="7dd88-116">The following generates a SQL script from a blank database to the latest migration:</span></span>

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="7dd88-117">Mit „from“ („to“ wird impliziert)</span><span class="sxs-lookup"><span data-stu-id="7dd88-117">With From (to implied)</span></span>

<span data-ttu-id="7dd88-118">Im folgenden wird ein SQL-Skript aus der angegebenen Migration zur neuesten Migration generiert.</span><span class="sxs-lookup"><span data-stu-id="7dd88-118">The following generates a SQL script from the given migration to the latest migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="7dd88-119">Mit „from“ und „to“</span><span class="sxs-lookup"><span data-stu-id="7dd88-119">With From and To</span></span>

<span data-ttu-id="7dd88-120">Im folgenden wird ein SQL-Skript aus der angegebenen `from` Migration zur angegebenen `to` Migration generiert.</span><span class="sxs-lookup"><span data-stu-id="7dd88-120">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

<span data-ttu-id="7dd88-121">Sie können ein `from` verwenden, das aktueller ist als `to`, um ein Rollbackskript zu generieren.</span><span class="sxs-lookup"><span data-stu-id="7dd88-121">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span>

> [!WARNING]
> <span data-ttu-id="7dd88-122">Bitte achten Sie auf mögliche Datenverlustszenarios.</span><span class="sxs-lookup"><span data-stu-id="7dd88-122">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="7dd88-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7dd88-123">Visual Studio</span></span>](#tab/vs)

#### <a name="basic-usage"></a><span data-ttu-id="7dd88-124">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="7dd88-124">Basic Usage</span></span>

<span data-ttu-id="7dd88-125">Im folgenden wird ein SQL-Skript aus einer leeren Datenbank mit der neuesten Migration generiert:</span><span class="sxs-lookup"><span data-stu-id="7dd88-125">The following generates a SQL script from a blank database to the latest migration:</span></span>

``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="7dd88-126">Mit „from“ („to“ wird impliziert)</span><span class="sxs-lookup"><span data-stu-id="7dd88-126">With From (to implied)</span></span>

<span data-ttu-id="7dd88-127">Im folgenden wird ein SQL-Skript aus der angegebenen Migration zur neuesten Migration generiert.</span><span class="sxs-lookup"><span data-stu-id="7dd88-127">The following generates a SQL script from the given migration to the latest migration.</span></span>

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="7dd88-128">Mit „from“ und „to“</span><span class="sxs-lookup"><span data-stu-id="7dd88-128">With From and To</span></span>

<span data-ttu-id="7dd88-129">Im folgenden wird ein SQL-Skript aus der angegebenen `from` Migration zur angegebenen `to` Migration generiert.</span><span class="sxs-lookup"><span data-stu-id="7dd88-129">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```powershell
Script-Migration AddNewTables AddAuditTable
```
<span data-ttu-id="7dd88-130">Sie können ein `from` verwenden, das aktueller ist als `to`, um ein Rollbackskript zu generieren.</span><span class="sxs-lookup"><span data-stu-id="7dd88-130">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="7dd88-131">*Bitte achten Sie auf mögliche Datenverlustszenarios.*</span><span class="sxs-lookup"><span data-stu-id="7dd88-131">*Please take note of potential data loss scenarios.*</span></span>

***

<span data-ttu-id="7dd88-132">Die Skript Generierung akzeptiert die folgenden zwei Argumente in, um anzugeben, welcher Bereich von Migrationen generiert werden soll:</span><span class="sxs-lookup"><span data-stu-id="7dd88-132">Script generation accepts the following two arguments in indicate which range of migrations should be generated:</span></span>

* <span data-ttu-id="7dd88-133">Die **from**-Migration sollte die letzte Migration sein, die vor der Skriptausführung für die Datenbank durchgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="7dd88-133">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="7dd88-134">Wenn keine Migrationen durchgeführt wurden, geben Sie `0` an (dies ist die Standardeinstellung).</span><span class="sxs-lookup"><span data-stu-id="7dd88-134">If no migrations have been applied, specify `0` (this is the default).</span></span>
* <span data-ttu-id="7dd88-135">Die **to**-Migration ist die letzte Migration, die nach der Skriptausführung für die Datenbank durchgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="7dd88-135">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="7dd88-136">Dies ist standardmäßig die letzte Migration in Ihrem Projekt.</span><span class="sxs-lookup"><span data-stu-id="7dd88-136">This defaults to the last migration in your project.</span></span>

## <a name="idempotent-sql-scripts"></a><span data-ttu-id="7dd88-137">Idempotente SQL-Skripts</span><span class="sxs-lookup"><span data-stu-id="7dd88-137">Idempotent SQL scripts</span></span>

<span data-ttu-id="7dd88-138">Die oben generierten SQL-Skripts können nur angewendet werden, um das Schema von einer Migration zu einer anderen zu ändern. Es liegt in ihrer Verantwortung, das Skript ordnungsgemäß und nur auf die Datenbank im richtigen Migrations Zustand anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="7dd88-138">The SQL scripts generated above can only be applied to change your schema from one migration to another; it is your responsibility to apply the script appropriately, and only to database in the correct migration state.</span></span> <span data-ttu-id="7dd88-139">EF Core unterstützt auch das erzeugen **idempotenter** Skripts, die intern überprüfen, welche Migrationen bereits angewendet wurden (über die Migrations Verlaufs Tabelle) und nur fehlende anwenden.</span><span class="sxs-lookup"><span data-stu-id="7dd88-139">EF Core also supports generating **idempotent** scripts, which internally check which migrations have already been applied (via the migrations history table), and only apply missing ones.</span></span> <span data-ttu-id="7dd88-140">Dies ist hilfreich, wenn Sie nicht genau wissen, wie die letzte Migration auf die Datenbank angewendet wurde, oder wenn Sie eine Bereitstellung in mehreren Datenbanken durchgeführt haben, bei denen es sich jeweils um eine andere Migration handelt.</span><span class="sxs-lookup"><span data-stu-id="7dd88-140">This is useful if you don't exactly know what the last migration applied to the database was, or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<span data-ttu-id="7dd88-141">Im folgenden werden idempotente Migrationen generiert:</span><span class="sxs-lookup"><span data-stu-id="7dd88-141">The following generates idempotent migrations:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="7dd88-142">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="7dd88-142">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

#### <a name="visual-studio"></a>[<span data-ttu-id="7dd88-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7dd88-143">Visual Studio</span></span>](#tab/vs)

``` powershell
Script-Migration -Idempotent
```

***

## <a name="command-line-tools"></a><span data-ttu-id="7dd88-144">Befehlszeilentools</span><span class="sxs-lookup"><span data-stu-id="7dd88-144">Command-line tools</span></span>

<span data-ttu-id="7dd88-145">Die EF-Befehlszeilen Tools können zum Anwenden von Migrationen auf eine Datenbank verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7dd88-145">The EF command-line tools can be used to apply migrations to a database.</span></span> <span data-ttu-id="7dd88-146">Während der Produktivität bei der lokalen Entwicklung und beim Testen von Migrationen ist dieser Ansatz nicht ideal für die Verwaltung von Produktionsdatenbanken:</span><span class="sxs-lookup"><span data-stu-id="7dd88-146">While productive for local development and testing of migrations, this approach isn't ideal for managing production databases:</span></span>

* <span data-ttu-id="7dd88-147">Die SQL-Befehle werden direkt durch das Tool angewendet, ohne dem Entwickler eine Änderung an der Überprüfung oder Änderung zu erteilen.</span><span class="sxs-lookup"><span data-stu-id="7dd88-147">The SQL commands are applied directly by the tool, without giving the developer a change to inspect or modify them.</span></span> <span data-ttu-id="7dd88-148">Dies kann in einer Produktionsumgebung gefährlich sein.</span><span class="sxs-lookup"><span data-stu-id="7dd88-148">This can be dangerous in a production environment.</span></span>
* <span data-ttu-id="7dd88-149">Das .NET SDK und das EF-Tool müssen auf Produktionsservern installiert sein.</span><span class="sxs-lookup"><span data-stu-id="7dd88-149">The .NET SDK and the EF tool must be installed on production servers.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="7dd88-150">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="7dd88-150">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="7dd88-151">Im folgenden wird die-Datenbank auf die neueste Migration aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="7dd88-151">The following updates your database to the latest migration:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="7dd88-152">Im folgenden wird die-Datenbank auf eine bestimmte Migration aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="7dd88-152">The following updates your database to a given migration:</span></span>

```dotnetcli
dotnet ef database update AddNewTables
```

<span data-ttu-id="7dd88-153">Beachten Sie, dass dies auch für einen Rollback zu einer früheren Migration verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7dd88-153">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="7dd88-154">Bitte achten Sie auf mögliche Datenverlustszenarios.</span><span class="sxs-lookup"><span data-stu-id="7dd88-154">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="7dd88-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7dd88-155">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="7dd88-156">Im folgenden wird die-Datenbank auf die neueste Migration aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="7dd88-156">The following updates your database to the latest migration:</span></span>

``` powershell
Update-Database
```

<span data-ttu-id="7dd88-157">Im folgenden wird die-Datenbank auf eine bestimmte Migration aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="7dd88-157">The following updates your database to a given migration:</span></span>

``` powershell
Update-Database AddNewTables
```

<span data-ttu-id="7dd88-158">Beachten Sie, dass dies auch für einen Rollback zu einer früheren Migration verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7dd88-158">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="7dd88-159">Bitte achten Sie auf mögliche Datenverlustszenarios.</span><span class="sxs-lookup"><span data-stu-id="7dd88-159">Please take note of potential data loss scenarios.</span></span>

***

<span data-ttu-id="7dd88-160">Weitere Informationen zum Anwenden von Migrationen über die Befehlszeilen Tools finden Sie in der [EF Core Tools-Referenz](xref:core/miscellaneous/cli/index).</span><span class="sxs-lookup"><span data-stu-id="7dd88-160">For more information on applying migrations via the command-line tools, see the [EF Core tools reference](xref:core/miscellaneous/cli/index).</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="7dd88-161">Anwenden von Migrationen zur Laufzeit</span><span class="sxs-lookup"><span data-stu-id="7dd88-161">Apply migrations at runtime</span></span>

<span data-ttu-id="7dd88-162">Es ist möglich, dass die Anwendung selbst Migrationen Programm gesteuert anwendet, in der Regel während des Starts.</span><span class="sxs-lookup"><span data-stu-id="7dd88-162">It's possible for the application itself to apply migrations programmatically, typically during startup.</span></span> <span data-ttu-id="7dd88-163">Während der Produktivität bei der lokalen Entwicklung und beim Testen von Migrationen ist dieser Ansatz für die Verwaltung von Produktionsdatenbanken aus den folgenden Gründen ungeeignet:</span><span class="sxs-lookup"><span data-stu-id="7dd88-163">While productive for local development and testing of migrations, this approach is inappropriate for managing production databases, for the following reasons:</span></span>

* <span data-ttu-id="7dd88-164">Wenn mehrere Instanzen der Anwendung ausgeführt werden, können beide Anwendungen versuchen, die Migration gleichzeitig anzuwenden und einen Fehler zu verursachen (oder eine Beschädigung der Daten zu verursachen).</span><span class="sxs-lookup"><span data-stu-id="7dd88-164">If multiple instances of your application are running, both applications could attempt to apply the migration concurrently and fail (or worse, cause data corruption).</span></span>
* <span data-ttu-id="7dd88-165">Wenn eine Anwendung auf die Datenbank zugreift, während Sie von einer anderen Anwendung migriert wird, kann dies zu schwerwiegenden Problemen führen.</span><span class="sxs-lookup"><span data-stu-id="7dd88-165">Similarly, if an application is accessing the database while another application migrates it, this can cause severe issues.</span></span>
* <span data-ttu-id="7dd88-166">Die Anwendung muss über erhöhten Zugriff verfügen, um das Datenbankschema zu ändern.</span><span class="sxs-lookup"><span data-stu-id="7dd88-166">The application must have elevated access to modify the database schema.</span></span> <span data-ttu-id="7dd88-167">Im Allgemeinen empfiehlt es sich, die Daten Bank Berechtigungen der Anwendung in der Produktion einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="7dd88-167">It's generally good practice to limit the application's database permissions in production.</span></span>
* <span data-ttu-id="7dd88-168">Es ist wichtig, dass ein Rollback für eine angewendete Migration im Falle eines Problems ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="7dd88-168">It's important to be able to roll back an applied migration in case of an issue.</span></span> <span data-ttu-id="7dd88-169">Die anderen Strategien stellen dies problemlos und standardmäßig bereit.</span><span class="sxs-lookup"><span data-stu-id="7dd88-169">The other strategies provide this easily and out of the box.</span></span>
* <span data-ttu-id="7dd88-170">Die SQL-Befehle werden direkt durch das Programm angewendet, ohne dass der Entwickler eine Änderung vornehmen muss, um Sie zu überprüfen oder zu ändern.</span><span class="sxs-lookup"><span data-stu-id="7dd88-170">The SQL commands are applied directly by the program, without giving the developer a change to inspect or modify them.</span></span> <span data-ttu-id="7dd88-171">Dies kann in einer Produktionsumgebung gefährlich sein.</span><span class="sxs-lookup"><span data-stu-id="7dd88-171">This can be dangerous in a production environment.</span></span>

<span data-ttu-id="7dd88-172">Zum programmgesteuerten Anwenden von Migrationen muss aufgerufen werden `context.Database.Migrate()` .</span><span class="sxs-lookup"><span data-stu-id="7dd88-172">To apply migrations programmatically, call `context.Database.Migrate()`.</span></span> <span data-ttu-id="7dd88-173">Eine typische ASP.NET-Anwendung kann z. b. folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="7dd88-173">For example, a typical ASP.NET application can do the following:</span></span>

```c#
public static void Main(string[] args)
{
    var host = CreateHostBuilder(args).Build();

    using (var scope = host.Services.CreateScope())
    {
        var db = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
        db.Database.Migrate();
    }

    host.Run();
}
```

<span data-ttu-id="7dd88-174">Beachten Sie, dass `Migrate()` auf dem `IMigrator` Dienst aufbaut, der für erweiterte Szenarien verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="7dd88-174">Note that `Migrate()` builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="7dd88-175">Verwenden Sie `myDbContext.GetInfrastructure().GetService<IMigrator>()`, um darauf zugreifen.</span><span class="sxs-lookup"><span data-stu-id="7dd88-175">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

> [!WARNING]
>
> * <span data-ttu-id="7dd88-176">Beachten Sie sorgfältig, bevor Sie diesen Ansatz in der Produktion verwenden.</span><span class="sxs-lookup"><span data-stu-id="7dd88-176">Carefully consider before using this approach in production.</span></span> <span data-ttu-id="7dd88-177">Es wurde gezeigt, dass die Einfachheit dieser Bereitstellungs Strategie durch die von ihr erstellten Probleme überwiegen.</span><span class="sxs-lookup"><span data-stu-id="7dd88-177">Experience has shown that the simplicity of this deployment strategy is outweighed by the issues it creates.</span></span> <span data-ttu-id="7dd88-178">Verwenden Sie stattdessen SQL-Skripts.</span><span class="sxs-lookup"><span data-stu-id="7dd88-178">Consider using SQL scripts instead.</span></span>
> * <span data-ttu-id="7dd88-179">Rufen Sie `EnsureCreated()` nicht vor `Migrate()` auf.</span><span class="sxs-lookup"><span data-stu-id="7dd88-179">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="7dd88-180">`EnsureCreated()` umgeht Migrationen zum Erstellen von Schemas, was dazu führt, dass `Migrate()` fehlerhaft ist.</span><span class="sxs-lookup"><span data-stu-id="7dd88-180">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>
