---
title: Anwenden von Migrationen-EF Core
description: Strategien zum Anwenden von Schema Migrationen auf Produktions-und Entwicklungs Datenbanken mithilfe von Entity Framework Core
author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: 7ff84636fb0999941b832c6a2d65d77b0ad368c5
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429849"
---
# <a name="applying-migrations"></a><span data-ttu-id="0a005-103">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="0a005-103">Applying Migrations</span></span>

<span data-ttu-id="0a005-104">Nachdem Ihre Migrationen hinzugefügt wurden, müssen Sie bereitgestellt und auf Ihre Datenbanken angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="0a005-104">Once your migrations have been added, they need to be deployed and applied to your databases.</span></span> <span data-ttu-id="0a005-105">Hierfür gibt es verschiedene Strategien, die für Produktionsumgebungen besser geeignet sind, und andere für den Entwicklungs Lebenszyklus.</span><span class="sxs-lookup"><span data-stu-id="0a005-105">There are various strategies for doing this, with some being more appropriate for production environments, and others for the development lifecycle.</span></span>

> [!NOTE]
> <span data-ttu-id="0a005-106">Ungeachtet der Bereitstellungs Strategie sollten Sie immer die generierten Migrationen untersuchen und testen, bevor Sie Sie auf eine Produktionsdatenbank anwenden.</span><span class="sxs-lookup"><span data-stu-id="0a005-106">Whatever your deployment strategy, always inspect the generated migrations and test them before applying to a production database.</span></span> <span data-ttu-id="0a005-107">Eine Migration kann eine Spalte löschen, wenn die Absicht darin besteht, Sie umzubenennen, oder aus verschiedenen Gründen fehlschlagen, wenn Sie auf eine Datenbank angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="0a005-107">A migration may drop a column when the intent was to rename it, or may fail for various reasons when applied to a database.</span></span>

## <a name="sql-scripts"></a><span data-ttu-id="0a005-108">SQL-Skripts</span><span class="sxs-lookup"><span data-stu-id="0a005-108">SQL scripts</span></span>

<span data-ttu-id="0a005-109">Die empfohlene Vorgehensweise zum Bereitstellen von Migrationen in eine Produktionsdatenbank ist das Erstellen von SQL-Skripts.</span><span class="sxs-lookup"><span data-stu-id="0a005-109">The recommended way to deploy migrations to a production database is by generating SQL scripts.</span></span> <span data-ttu-id="0a005-110">Diese Strategie bietet folgende Vorteile:</span><span class="sxs-lookup"><span data-stu-id="0a005-110">The advantages of this strategy include the following:</span></span>

* <span data-ttu-id="0a005-111">SQL-Skripts können auf Genauigkeit überprüft werden. Dies ist wichtig, da das Anwenden von Schema Änderungen auf Produktionsdatenbanken einen potenziell gefährlichen Vorgang verursachen kann, der zu Datenverlusten führen kann.</span><span class="sxs-lookup"><span data-stu-id="0a005-111">SQL scripts can be reviewed for accuracy; this is important since applying schema changes to production databases is a potentially dangerous operation that could involve data loss.</span></span>
* <span data-ttu-id="0a005-112">In einigen Fällen können die Skripts an die spezifischen Anforderungen einer Produktionsdatenbank angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="0a005-112">In some cases, the scripts can be tuned to fit the specific needs of a production database.</span></span>
* <span data-ttu-id="0a005-113">SQL-Skripts können in Verbindung mit einer Bereitstellungs Technologie verwendet werden und können sogar als Teil Ihres CI-Prozesses generiert werden.</span><span class="sxs-lookup"><span data-stu-id="0a005-113">SQL scripts can be used in conjunction with a deployment technology, and can even be generated as part of your CI process.</span></span>
* <span data-ttu-id="0a005-114">SQL-Skripts können einem Datenbankadministrator bereitgestellt werden und können separat verwaltet und archiviert werden.</span><span class="sxs-lookup"><span data-stu-id="0a005-114">SQL scripts can be provided to a DBA, and can be managed and archived separately.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0a005-115">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="0a005-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a><span data-ttu-id="0a005-116">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="0a005-116">Basic Usage</span></span>

<span data-ttu-id="0a005-117">Im folgenden wird ein SQL-Skript aus einer leeren Datenbank mit der neuesten Migration generiert:</span><span class="sxs-lookup"><span data-stu-id="0a005-117">The following generates a SQL script from a blank database to the latest migration:</span></span>

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="0a005-118">Mit „from“ („to“ wird impliziert)</span><span class="sxs-lookup"><span data-stu-id="0a005-118">With From (to implied)</span></span>

<span data-ttu-id="0a005-119">Im folgenden wird ein SQL-Skript aus der angegebenen Migration zur neuesten Migration generiert.</span><span class="sxs-lookup"><span data-stu-id="0a005-119">The following generates a SQL script from the given migration to the latest migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="0a005-120">Mit „from“ und „to“</span><span class="sxs-lookup"><span data-stu-id="0a005-120">With From and To</span></span>

<span data-ttu-id="0a005-121">Im folgenden wird ein SQL-Skript aus der angegebenen `from` Migration zur angegebenen `to` Migration generiert.</span><span class="sxs-lookup"><span data-stu-id="0a005-121">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

<span data-ttu-id="0a005-122">Sie können ein `from` verwenden, das aktueller ist als `to`, um ein Rollbackskript zu generieren.</span><span class="sxs-lookup"><span data-stu-id="0a005-122">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span>

> [!WARNING]
> <span data-ttu-id="0a005-123">Bitte achten Sie auf mögliche Datenverlustszenarios.</span><span class="sxs-lookup"><span data-stu-id="0a005-123">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="0a005-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a005-124">Visual Studio</span></span>](#tab/vs)

#### <a name="basic-usage"></a><span data-ttu-id="0a005-125">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="0a005-125">Basic Usage</span></span>

<span data-ttu-id="0a005-126">Im folgenden wird ein SQL-Skript aus einer leeren Datenbank mit der neuesten Migration generiert:</span><span class="sxs-lookup"><span data-stu-id="0a005-126">The following generates a SQL script from a blank database to the latest migration:</span></span>

```powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="0a005-127">Mit „from“ („to“ wird impliziert)</span><span class="sxs-lookup"><span data-stu-id="0a005-127">With From (to implied)</span></span>

<span data-ttu-id="0a005-128">Im folgenden wird ein SQL-Skript aus der angegebenen Migration zur neuesten Migration generiert.</span><span class="sxs-lookup"><span data-stu-id="0a005-128">The following generates a SQL script from the given migration to the latest migration.</span></span>

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="0a005-129">Mit „from“ und „to“</span><span class="sxs-lookup"><span data-stu-id="0a005-129">With From and To</span></span>

<span data-ttu-id="0a005-130">Im folgenden wird ein SQL-Skript aus der angegebenen `from` Migration zur angegebenen `to` Migration generiert.</span><span class="sxs-lookup"><span data-stu-id="0a005-130">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```powershell
Script-Migration AddNewTables AddAuditTable
```

<span data-ttu-id="0a005-131">Sie können ein `from` verwenden, das aktueller ist als `to`, um ein Rollbackskript zu generieren.</span><span class="sxs-lookup"><span data-stu-id="0a005-131">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="0a005-132">*Bitte achten Sie auf mögliche Datenverlustszenarios.*</span><span class="sxs-lookup"><span data-stu-id="0a005-132">*Please take note of potential data loss scenarios.*</span></span>

***

<span data-ttu-id="0a005-133">Die Skript Generierung akzeptiert die folgenden zwei Argumente in, um anzugeben, welcher Bereich von Migrationen generiert werden soll:</span><span class="sxs-lookup"><span data-stu-id="0a005-133">Script generation accepts the following two arguments in indicate which range of migrations should be generated:</span></span>

* <span data-ttu-id="0a005-134">Die **from** -Migration sollte die letzte Migration sein, die vor der Skriptausführung für die Datenbank durchgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="0a005-134">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="0a005-135">Wenn keine Migrationen durchgeführt wurden, geben Sie `0` an (dies ist die Standardeinstellung).</span><span class="sxs-lookup"><span data-stu-id="0a005-135">If no migrations have been applied, specify `0` (this is the default).</span></span>
* <span data-ttu-id="0a005-136">Die **to** -Migration ist die letzte Migration, die nach der Skriptausführung für die Datenbank durchgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="0a005-136">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="0a005-137">Dies ist standardmäßig die letzte Migration in Ihrem Projekt.</span><span class="sxs-lookup"><span data-stu-id="0a005-137">This defaults to the last migration in your project.</span></span>

## <a name="idempotent-sql-scripts"></a><span data-ttu-id="0a005-138">Idempotente SQL-Skripts</span><span class="sxs-lookup"><span data-stu-id="0a005-138">Idempotent SQL scripts</span></span>

<span data-ttu-id="0a005-139">Die oben generierten SQL-Skripts können nur angewendet werden, um das Schema von einer Migration zu einer anderen zu ändern. Es liegt in ihrer Verantwortung, das Skript ordnungsgemäß und nur auf die Datenbank im richtigen Migrations Zustand anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="0a005-139">The SQL scripts generated above can only be applied to change your schema from one migration to another; it is your responsibility to apply the script appropriately, and only to database in the correct migration state.</span></span> <span data-ttu-id="0a005-140">EF Core unterstützt auch das erzeugen **idempotenter** Skripts, die intern überprüfen, welche Migrationen bereits angewendet wurden (über die Migrations Verlaufs Tabelle) und nur fehlende anwenden.</span><span class="sxs-lookup"><span data-stu-id="0a005-140">EF Core also supports generating **idempotent** scripts, which internally check which migrations have already been applied (via the migrations history table), and only apply missing ones.</span></span> <span data-ttu-id="0a005-141">Dies ist hilfreich, wenn Sie nicht genau wissen, wie die letzte Migration auf die Datenbank angewendet wurde, oder wenn Sie eine Bereitstellung in mehreren Datenbanken durchgeführt haben, bei denen es sich jeweils um eine andere Migration handelt.</span><span class="sxs-lookup"><span data-stu-id="0a005-141">This is useful if you don't exactly know what the last migration applied to the database was, or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<span data-ttu-id="0a005-142">Im folgenden werden idempotente Migrationen generiert:</span><span class="sxs-lookup"><span data-stu-id="0a005-142">The following generates idempotent migrations:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0a005-143">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="0a005-143">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

### <a name="visual-studio"></a>[<span data-ttu-id="0a005-144">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a005-144">Visual Studio</span></span>](#tab/vs)

```powershell
Script-Migration -Idempotent
```

<span data-ttu-id="0a005-145">\*\*_</span><span class="sxs-lookup"><span data-stu-id="0a005-145">\*\*_</span></span>

## <a name="command-line-tools"></a><span data-ttu-id="0a005-146">Befehlszeilentools</span><span class="sxs-lookup"><span data-stu-id="0a005-146">Command-line tools</span></span>

<span data-ttu-id="0a005-147">Die EF-Befehlszeilen Tools können zum Anwenden von Migrationen auf eine Datenbank verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0a005-147">The EF command-line tools can be used to apply migrations to a database.</span></span> <span data-ttu-id="0a005-148">Während der Produktivität bei der lokalen Entwicklung und beim Testen von Migrationen ist dieser Ansatz nicht ideal für die Verwaltung von Produktionsdatenbanken:</span><span class="sxs-lookup"><span data-stu-id="0a005-148">While productive for local development and testing of migrations, this approach isn't ideal for managing production databases:</span></span>

<span data-ttu-id="0a005-149">_ Die SQL-Befehle werden direkt durch das Tool angewendet, ohne dem Entwickler die Möglichkeit zu geben, Sie zu überprüfen oder zu ändern.</span><span class="sxs-lookup"><span data-stu-id="0a005-149">_ The SQL commands are applied directly by the tool, without giving the developer a chance to inspect or modify them.</span></span> <span data-ttu-id="0a005-150">Dies kann in einer Produktionsumgebung gefährlich sein.</span><span class="sxs-lookup"><span data-stu-id="0a005-150">This can be dangerous in a production environment.</span></span>
* <span data-ttu-id="0a005-151">Das .NET SDK und das EF-Tool müssen auf Produktionsservern installiert sein.</span><span class="sxs-lookup"><span data-stu-id="0a005-151">The .NET SDK and the EF tool must be installed on production servers.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0a005-152">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="0a005-152">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="0a005-153">Im folgenden wird die-Datenbank auf die neueste Migration aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="0a005-153">The following updates your database to the latest migration:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="0a005-154">Im folgenden wird die-Datenbank auf eine bestimmte Migration aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="0a005-154">The following updates your database to a given migration:</span></span>

```dotnetcli
dotnet ef database update AddNewTables
```

<span data-ttu-id="0a005-155">Beachten Sie, dass dies auch für einen Rollback zu einer früheren Migration verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="0a005-155">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="0a005-156">Bitte achten Sie auf mögliche Datenverlustszenarios.</span><span class="sxs-lookup"><span data-stu-id="0a005-156">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="0a005-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a005-157">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="0a005-158">Im folgenden wird die-Datenbank auf die neueste Migration aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="0a005-158">The following updates your database to the latest migration:</span></span>

```powershell
Update-Database
```

<span data-ttu-id="0a005-159">Im folgenden wird die-Datenbank auf eine bestimmte Migration aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="0a005-159">The following updates your database to a given migration:</span></span>

```powershell
Update-Database AddNewTables
```

<span data-ttu-id="0a005-160">Beachten Sie, dass dies auch für einen Rollback zu einer früheren Migration verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="0a005-160">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="0a005-161">Bitte achten Sie auf mögliche Datenverlustszenarios.</span><span class="sxs-lookup"><span data-stu-id="0a005-161">Please take note of potential data loss scenarios.</span></span>

***

<span data-ttu-id="0a005-162">Weitere Informationen zum Anwenden von Migrationen über die Befehlszeilen Tools finden Sie in der [EF Core Tools-Referenz](xref:core/cli/index).</span><span class="sxs-lookup"><span data-stu-id="0a005-162">For more information on applying migrations via the command-line tools, see the [EF Core tools reference](xref:core/cli/index).</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="0a005-163">Anwenden von Migrationen zur Laufzeit</span><span class="sxs-lookup"><span data-stu-id="0a005-163">Apply migrations at runtime</span></span>

<span data-ttu-id="0a005-164">Es ist möglich, dass die Anwendung selbst Migrationen Programm gesteuert anwendet, in der Regel während des Starts.</span><span class="sxs-lookup"><span data-stu-id="0a005-164">It's possible for the application itself to apply migrations programmatically, typically during startup.</span></span> <span data-ttu-id="0a005-165">Während der Produktivität bei der lokalen Entwicklung und beim Testen von Migrationen ist dieser Ansatz für die Verwaltung von Produktionsdatenbanken aus den folgenden Gründen ungeeignet:</span><span class="sxs-lookup"><span data-stu-id="0a005-165">While productive for local development and testing of migrations, this approach is inappropriate for managing production databases, for the following reasons:</span></span>

* <span data-ttu-id="0a005-166">Wenn mehrere Instanzen der Anwendung ausgeführt werden, können beide Anwendungen versuchen, die Migration gleichzeitig anzuwenden und einen Fehler zu verursachen (oder eine Beschädigung der Daten zu verursachen).</span><span class="sxs-lookup"><span data-stu-id="0a005-166">If multiple instances of your application are running, both applications could attempt to apply the migration concurrently and fail (or worse, cause data corruption).</span></span>
* <span data-ttu-id="0a005-167">Wenn eine Anwendung auf die Datenbank zugreift, während Sie von einer anderen Anwendung migriert wird, kann dies zu schwerwiegenden Problemen führen.</span><span class="sxs-lookup"><span data-stu-id="0a005-167">Similarly, if an application is accessing the database while another application migrates it, this can cause severe issues.</span></span>
* <span data-ttu-id="0a005-168">Die Anwendung muss über erhöhten Zugriff verfügen, um das Datenbankschema zu ändern.</span><span class="sxs-lookup"><span data-stu-id="0a005-168">The application must have elevated access to modify the database schema.</span></span> <span data-ttu-id="0a005-169">Im Allgemeinen empfiehlt es sich, die Daten Bank Berechtigungen der Anwendung in der Produktion einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="0a005-169">It's generally good practice to limit the application's database permissions in production.</span></span>
* <span data-ttu-id="0a005-170">Es ist wichtig, dass ein Rollback für eine angewendete Migration im Falle eines Problems ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="0a005-170">It's important to be able to roll back an applied migration in case of an issue.</span></span> <span data-ttu-id="0a005-171">Die anderen Strategien stellen dies problemlos und standardmäßig bereit.</span><span class="sxs-lookup"><span data-stu-id="0a005-171">The other strategies provide this easily and out of the box.</span></span>
* <span data-ttu-id="0a005-172">Die SQL-Befehle werden direkt durch das Programm angewendet, ohne dem Entwickler die Möglichkeit zu geben, Sie zu überprüfen oder zu ändern.</span><span class="sxs-lookup"><span data-stu-id="0a005-172">The SQL commands are applied directly by the program, without giving the developer a chance to inspect or modify them.</span></span> <span data-ttu-id="0a005-173">Dies kann in einer Produktionsumgebung gefährlich sein.</span><span class="sxs-lookup"><span data-stu-id="0a005-173">This can be dangerous in a production environment.</span></span>

<span data-ttu-id="0a005-174">Zum programmgesteuerten Anwenden von Migrationen muss aufgerufen werden `context.Database.Migrate()` .</span><span class="sxs-lookup"><span data-stu-id="0a005-174">To apply migrations programmatically, call `context.Database.Migrate()`.</span></span> <span data-ttu-id="0a005-175">Eine typische ASP.NET-Anwendung kann z. b. folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="0a005-175">For example, a typical ASP.NET application can do the following:</span></span>

```csharp
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

<span data-ttu-id="0a005-176">Beachten Sie, dass `Migrate()` auf dem `IMigrator` Dienst aufbaut, der für erweiterte Szenarien verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="0a005-176">Note that `Migrate()` builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="0a005-177">Verwenden Sie `myDbContext.GetInfrastructure().GetService<IMigrator>()`, um darauf zugreifen.</span><span class="sxs-lookup"><span data-stu-id="0a005-177">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

> [!WARNING]
>
> * <span data-ttu-id="0a005-178">Beachten Sie sorgfältig, bevor Sie diesen Ansatz in der Produktion verwenden.</span><span class="sxs-lookup"><span data-stu-id="0a005-178">Carefully consider before using this approach in production.</span></span> <span data-ttu-id="0a005-179">Es wurde gezeigt, dass die Einfachheit dieser Bereitstellungs Strategie durch die von ihr erstellten Probleme überwiegen.</span><span class="sxs-lookup"><span data-stu-id="0a005-179">Experience has shown that the simplicity of this deployment strategy is outweighed by the issues it creates.</span></span> <span data-ttu-id="0a005-180">Sie sollten stattdessen SQL-Skripts aus Migrationen erstellen.</span><span class="sxs-lookup"><span data-stu-id="0a005-180">Consider generating SQL scripts from migrations instead.</span></span>
> * <span data-ttu-id="0a005-181">Rufen Sie `EnsureCreated()` nicht vor `Migrate()` auf.</span><span class="sxs-lookup"><span data-stu-id="0a005-181">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="0a005-182">`EnsureCreated()` umgeht Migrationen zum Erstellen von Schemas, was dazu führt, dass `Migrate()` fehlerhaft ist.</span><span class="sxs-lookup"><span data-stu-id="0a005-182">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>
