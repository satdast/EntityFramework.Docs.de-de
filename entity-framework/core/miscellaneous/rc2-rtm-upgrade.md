---
title: Aktualisieren von EF Core 1,0 rc2 auf RTM-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: f496806ea6330c60cf43068882b7de839e18e383
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526770"
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a><span data-ttu-id="09893-102">Aktualisieren von EF Core 1,0 rc2 auf RTM</span><span class="sxs-lookup"><span data-stu-id="09893-102">Upgrading from EF Core 1.0 RC2 to RTM</span></span>

<span data-ttu-id="09893-103">Dieser Artikel enthält Anleitungen zum Verschieben einer Anwendung, die mit den RC2-Paketen erstellt wurde, in 1.0.0 RTM.</span><span class="sxs-lookup"><span data-stu-id="09893-103">This article provides guidance for moving an application built with the RC2 packages to 1.0.0 RTM.</span></span>

## <a name="package-versions"></a><span data-ttu-id="09893-104">Paketversionen</span><span class="sxs-lookup"><span data-stu-id="09893-104">Package Versions</span></span>

<span data-ttu-id="09893-105">Die Namen der Pakete auf oberster Ebene, die normalerweise in einer Anwendung installiert werden, haben sich zwischen RC2 und RTM nicht geändert.</span><span class="sxs-lookup"><span data-stu-id="09893-105">The names of the top level packages that you would typically install into an application did not change between RC2 and RTM.</span></span>

<span data-ttu-id="09893-106">**Sie müssen die installierten Pakete auf die RTM-Versionen aktualisieren:**</span><span class="sxs-lookup"><span data-stu-id="09893-106">**You need to upgrade the installed packages to the RTM versions:**</span></span>

* <span data-ttu-id="09893-107">Lauf Zeit Pakete (z `Microsoft.EntityFrameworkCore.SqlServer` . b.) wurden von `1.0.0-rc2-final` in geändert `1.0.0` .</span><span class="sxs-lookup"><span data-stu-id="09893-107">Runtime packages (for example, `Microsoft.EntityFrameworkCore.SqlServer`) changed from `1.0.0-rc2-final` to `1.0.0`.</span></span>

* <span data-ttu-id="09893-108">Das `Microsoft.EntityFrameworkCore.Tools` Paket wurde von `1.0.0-preview1-final` in geändert `1.0.0-preview2-final` .</span><span class="sxs-lookup"><span data-stu-id="09893-108">The `Microsoft.EntityFrameworkCore.Tools` package changed from `1.0.0-preview1-final` to `1.0.0-preview2-final`.</span></span> <span data-ttu-id="09893-109">Beachten Sie, dass die Tools noch vorab veröffentlicht werden.</span><span class="sxs-lookup"><span data-stu-id="09893-109">Note that tooling is still pre-release.</span></span>

## <a name="existing-migrations-may-need-maxlength-added"></a><span data-ttu-id="09893-110">Vorhandene Migrationen müssen möglicherweise MaxLength hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="09893-110">Existing migrations may need maxLength added</span></span>

<span data-ttu-id="09893-111">In RC2 schaute die Spaltendefinition in einer Migration wie folgt aus, `table.Column<string>(nullable: true)` und die Länge der Spalte wurde in einigen Metadaten gesucht, die im Code hinter der Migration gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="09893-111">In RC2, the column definition in a migration looked like `table.Column<string>(nullable: true)` and the length of the column was looked up in some metadata we store in the code behind the migration.</span></span> <span data-ttu-id="09893-112">In RTM ist die Länge jetzt im Gerüsten Code enthalten `table.Column<string>(maxLength: 450, nullable: true)` .</span><span class="sxs-lookup"><span data-stu-id="09893-112">In RTM, the length is now included in the scaffolded code `table.Column<string>(maxLength: 450, nullable: true)`.</span></span>

<span data-ttu-id="09893-113">Für alle vorhandenen Migrationen, die vor der Verwendung von RTM erstellt wurden, wird das- `maxLength` Argument nicht angegeben.</span><span class="sxs-lookup"><span data-stu-id="09893-113">Any existing migrations that were scaffolded prior to using RTM will not have the `maxLength` argument specified.</span></span> <span data-ttu-id="09893-114">Dies bedeutet, dass die maximale Länge, die von der Datenbank unterstützt wird, ( `nvarchar(max)` auf SQL Server) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="09893-114">This means the maximum length supported by the database will be used (`nvarchar(max)` on SQL Server).</span></span> <span data-ttu-id="09893-115">Dies kann für einige Spalten in Ordnung sein, aber Spalten, die Teil eines Schlüssels, eines fremd Schlüssels oder eines Indexes sind, müssen aktualisiert werden, damit Sie eine maximale Länge enthalten.</span><span class="sxs-lookup"><span data-stu-id="09893-115">This may be fine for some columns, but columns that are part of a key, foreign key, or index need to be updated to include a maximum length.</span></span> <span data-ttu-id="09893-116">Gemäß der Konvention ist 450 die maximale Länge, die für Schlüssel, Fremdschlüssel und indizierte Spalten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="09893-116">By convention, 450 is the maximum length used for keys, foreign keys, and indexed columns.</span></span> <span data-ttu-id="09893-117">Wenn Sie im Modell explizit eine Länge konfiguriert haben, sollten Sie diese Länge stattdessen verwenden.</span><span class="sxs-lookup"><span data-stu-id="09893-117">If you have explicitly configured a length in the model, then you should use that length instead.</span></span>

### <a name="aspnet-identity"></a><span data-ttu-id="09893-118">ASP.NET Identity</span><span class="sxs-lookup"><span data-stu-id="09893-118">ASP.NET Identity</span></span>

<span data-ttu-id="09893-119">Diese Änderung wirkt sich auf Projekte aus, die ASP.net Identity verwenden und aus einer Pre-RTM-Projektvorlage erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="09893-119">This change impacts projects that use ASP.NET Identity and were created from a pre-RTM project template.</span></span> <span data-ttu-id="09893-120">Die Projektvorlage enthält eine Migration, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="09893-120">The project template includes a migration used to create the database.</span></span> <span data-ttu-id="09893-121">Diese Migration muss bearbeitet werden, um `256` für die folgenden Spalten eine maximale Länge von anzugeben.</span><span class="sxs-lookup"><span data-stu-id="09893-121">This migration must be edited to specify a maximum length of `256` for the following columns.</span></span>

* <span data-ttu-id="09893-122">**Aspnettroles**</span><span class="sxs-lookup"><span data-stu-id="09893-122">**AspNetRoles**</span></span>
  * <span data-ttu-id="09893-123">name</span><span class="sxs-lookup"><span data-stu-id="09893-123">Name</span></span>
  * <span data-ttu-id="09893-124">Normalizedname</span><span class="sxs-lookup"><span data-stu-id="09893-124">NormalizedName</span></span>
* <span data-ttu-id="09893-125">**AspNetUsers**</span><span class="sxs-lookup"><span data-stu-id="09893-125">**AspNetUsers**</span></span>
  * <span data-ttu-id="09893-126">E-Mail</span><span class="sxs-lookup"><span data-stu-id="09893-126">Email</span></span>
  * <span data-ttu-id="09893-127">Normalizedebug</span><span class="sxs-lookup"><span data-stu-id="09893-127">NormalizedEmail</span></span>
  * <span data-ttu-id="09893-128">Normalizedusername</span><span class="sxs-lookup"><span data-stu-id="09893-128">NormalizedUserName</span></span>
  * <span data-ttu-id="09893-129">UserName</span><span class="sxs-lookup"><span data-stu-id="09893-129">UserName</span></span>

<span data-ttu-id="09893-130">Wenn Sie diese Änderung nicht vornehmen, wird die folgende Ausnahme ausgelöst, wenn die anfängliche Migration auf eine Datenbank angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="09893-130">Failure to make this change will result in the following exception when the initial migration is applied to a database.</span></span>

``` Console
System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.
```

## <a name="net-core-remove-imports-in-projectjson"></a><span data-ttu-id="09893-131">.Net Core: Entfernen von "Imports" in project.js</span><span class="sxs-lookup"><span data-stu-id="09893-131">.NET Core: Remove "imports" in project.json</span></span>

<span data-ttu-id="09893-132">Wenn Sie .net Core mit RC2 als Zielplattform verwendet haben, mussten Sie `imports` project.jsals temporäre Problem Umgehung für einige EF Core Abhängigkeiten hinzufügen, die .NET Standard nicht unterstützen.</span><span class="sxs-lookup"><span data-stu-id="09893-132">If you were targeting .NET Core with RC2, you needed to add `imports` to project.json as a temporary workaround for some of EF Core's dependencies not supporting .NET Standard.</span></span> <span data-ttu-id="09893-133">Diese können nun entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="09893-133">These can now be removed.</span></span>

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

> [!NOTE]  
> <span data-ttu-id="09893-134">Ab Version 1,0 RTM unterstützt oder entwickelt der [.net Core SDK](https://www.microsoft.com/net/download/core) keine `project.json` .net Core-Anwendungen mehr unter Verwendung von Visual Studio 2015.</span><span class="sxs-lookup"><span data-stu-id="09893-134">As of version 1.0 RTM, the [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or developing .NET Core applications using Visual Studio 2015.</span></span> <span data-ttu-id="09893-135">Es wird empfohlen, [eine Migration von „project.json“ zu „csproj“ durchzuführen](/dotnet/articles/core/migration/).</span><span class="sxs-lookup"><span data-stu-id="09893-135">We recommend you [migrate from project.json to csproj](/dotnet/articles/core/migration/).</span></span> <span data-ttu-id="09893-136">Wenn Sie Visual Studio verwenden, wird empfohlen, ein Upgrade auf [Visual Studio 2017](https://www.visualstudio.com/downloads/)auszuführen.</span><span class="sxs-lookup"><span data-stu-id="09893-136">If you are using Visual Studio, we recommend you upgrade to [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

## <a name="uwp-add-binding-redirects"></a><span data-ttu-id="09893-137">UWP: Bindungs Umleitungen hinzufügen</span><span class="sxs-lookup"><span data-stu-id="09893-137">UWP: Add binding redirects</span></span>

<span data-ttu-id="09893-138">Der Versuch, EF-Befehle für universelle Windows-Plattform-Projekte (UWP) auszuführen, führt zu folgendem Fehler:</span><span class="sxs-lookup"><span data-stu-id="09893-138">Attempting to run EF commands on Universal Windows Platform (UWP) projects results in the following error:</span></span>

```output
System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.
```

<span data-ttu-id="09893-139">Sie müssen dem UWP-Projekt manuell Bindungs Umleitungen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="09893-139">You need to manually add binding redirects to the UWP project.</span></span> <span data-ttu-id="09893-140">Erstellen Sie eine Datei namens `App.config` im Stamm Ordner des Projekts, und fügen Sie den richtigen Assemblyversionen Umleitungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="09893-140">Create a file named `App.config` in the project root folder and add redirects to the correct assembly versions.</span></span>

```xml
<configuration>
 <runtime>
   <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
     <dependentAssembly>
       <assemblyIdentity name="System.IO.FileSystem.Primitives"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
     <dependentAssembly>
       <assemblyIdentity name="System.Threading.Overlapped"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
   </assemblyBinding>
 </runtime>
</configuration>
```
