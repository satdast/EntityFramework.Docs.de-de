---
title: Aktualisieren von EF Core 1,0 rc1 auf RC2-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: 5500addc98a0c0eb314c4d9f9401fa301ce5c6f6
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370525"
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a>Upgrade von EF Core 1,0 rc1 auf 1,0 rc2

Dieser Artikel enthält Anleitungen zum Verschieben einer Anwendung, die mit den RC1-Paketen in RC2 erstellt wurde.

## <a name="package-names-and-versions"></a>Paketnamen und-Versionen

Zwischen RC1 und RC2 wurde von "Entity Framework 7" in "Entity Framework Core" geändert. Weitere Informationen zu den Gründen für die Änderung [dieses Beitrags von Scott Hanselman](https://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx)finden Sie hier. Aufgrund dieser Änderung haben sich die Paketnamen von `EntityFramework.*` in `Microsoft.EntityFrameworkCore.*` und unsere Versionen von `7.0.0-rc1-final` in geändert `1.0.0-rc2-final` (oder `1.0.0-preview1-final` für-Tools).

**Sie müssen die RC1-Pakete vollständig entfernen und dann RC2 installieren.** Hier ist die Zuordnung für einige gängige Pakete.

| RC1-Paket                                               | RC2-Entsprechung                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| EntityFramework. MicrosoftSQLServer 7.0.0-rc1-Final | Microsoft. entityframeworkcore. SqlServer 1.0.0-rc2-Final      |
| EntityFramework. sqlite 7.0.0-rc1-Final | Microsoft. entityframeworkcore. sqlite 1.0.0-rc2-Final      |
| EntityFramework7. npgsql 3.1.0-rc1-3     | Npgsql. entityframeworkcore. postgres<to be advised>      |
| EntityFramework. SqlServerCompact35 7.0.0-rc1-Final | Entityframeworkcore. SqlServerCompact35 1.0.0-rc2-Final      |
| EntityFramework. SqlServerCompact40 7.0.0-rc1-Final | Entityframeworkcore. SqlServerCompact40 1.0.0-rc2-Final      |
| EntityFramework. inMemory 7.0.0-rc1-Final | Microsoft. entityframeworkcore. inMemory 1.0.0-rc2-Final      |
| EntityFramework. ibmdataserver 7.0.0-beta1     | Noch nicht für RC2 verfügbar                                            |
| EntityFramework. Commands 7.0.0-rc1-Final | Microsoft. entityframeworkcore. Tools 1.0.0-Preview1-Final |
| EntityFramework. MicrosoftSQLServer. Design 7.0.0-rc1-Final | Microsoft. entityframeworkcore. SqlServer. Design 1.0.0-rc2-Final      |

## <a name="namespaces"></a>Namespaces

Zusammen mit den Paketnamen haben sich Namespaces von `Microsoft.Data.Entity.*` in geändert `Microsoft.EntityFrameworkCore.*` . Sie können diese Änderung mit einem Suchen/Ersetzen von `using Microsoft.Data.Entity` mit verarbeiten `using Microsoft.EntityFrameworkCore` .

## <a name="table-naming-convention-changes"></a>Änderungen der Tabellen Benennungs Konvention

Eine bedeutende funktionale Änderung in RC2 bestand darin, den Namen der `DbSet<TEntity>` Eigenschaft für eine bestimmte Entität als den Tabellennamen zu verwenden, dem Sie zugeordnet ist, anstatt nur den Klassennamen zu verwenden. Weitere Informationen zu dieser Änderung finden Sie im [zugehörigen Ankündigungs Problem](https://github.com/aspnet/Announcements/issues/167).

Für vorhandene RC1-Anwendungen wird empfohlen, den folgenden Code am Anfang der Methode hinzuzufügen, `OnModelCreating` um die RC1-Benennungs Strategie beizubehalten:

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

Wenn Sie die neue Benennungs Strategie übernehmen möchten, empfehlen wir, die restlichen Upgradeschritte erfolgreich abzuschließen und dann den Code zu entfernen und eine Migration zum Anwenden der Tabellennamen zu erstellen.

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a>Adddbcontext/Startup.cs-Änderungen (nur ASP.net Core-Projekte)

In RC1 mussten Sie dem Anwendungs Dienstanbieter Entity Framework Dienste hinzufügen `Startup.ConfigureServices(...)` :

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

In RC2 können Sie die Aufrufe von `AddEntityFramework()` , `AddSqlServer()` usw. entfernen:

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

Außerdem müssen Sie Ihrem abgeleiteten Kontext einen Konstruktor hinzufügen, der Kontextoptionen annimmt und Sie an den Basiskonstruktor übergibt. Dies ist erforderlich, da wir einige der beängstigenden Magie entfernt haben, die Sie hinter den Kulissen schnuppern:

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a>Übergeben von IServiceProvider

Wenn Sie über RC1-Code verfügen, `IServiceProvider` der ein an den Kontext übergibt, wird dieser nun in verschoben `DbContextOptions` , anstatt einen separaten Konstruktorparameter zu sein. Verwenden `DbContextOptionsBuilder.UseInternalServiceProvider(...)` Sie, um den Dienstanbieter festzulegen.

### <a name="testing"></a>Testen

Das häufigste Szenario hierfür war, den Umfang einer inMemory-Datenbank beim Testen zu steuern. Ein Beispiel für die Verwendung von rc2 finden Sie im Artikel aktualisierte [Tests](testing/index.md) .

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a>Auflösen interner Dienste vom Anwendungs Dienstanbieter (nur ASP.net Core Projekte)

Wenn Sie über eine ASP.net Core Anwendung verfügen und EF möchten, dass interne Dienste vom Anwendungs Dienstanbieter aufgelöst werden, gibt es eine Überladung von, mit der `AddDbContext` Sie Folgendes konfigurieren können:

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider));
```

> [!WARNING]  
> Es wird empfohlen, dass EF intern seine eigenen Dienste verwaltet, es sei denn, Sie haben einen Grund, die internen EF-Dienste in Ihrem Anwendungs Dienstanbieter zu kombinieren. Der Hauptgrund hierfür ist, dass Sie den Anwendungs Dienstanbieter verwenden, um Dienste zu ersetzen, die von EF intern verwendet werden.

## <a name="dnx-commands--net-core-cli-aspnet-core-projects-only"></a>DNX-Befehle => .net Core-CLI (nur ASP.net Core Projekte)

Wenn Sie zuvor die `dnx ef` Befehle für ASP.net 5-Projekte verwendet haben, wurden diese nun in `dotnet ef` Befehle verschoben. Dieselbe Befehlssyntax gilt weiterhin. Sie können `dotnet ef --help` für Syntax Informationen verwenden.

Die Art und Weise, in der Befehle registriert werden, wurde in RC2 geändert, weil DNX durch den .net Core-CLI ersetzt wurde. Befehle sind nun in einem `tools` Abschnitt in registriert `project.json` :

``` json
"tools": {
  "Microsoft.EntityFrameworkCore.Tools": {
    "version": "1.0.0-preview1-final",
    "imports": [
      "portable-net45+win8+dnxcore50",
      "portable-net45+win8"
    ]
  }
}
```

> [!TIP]  
> Wenn Sie Visual Studio verwenden, können Sie jetzt die Paket-Manager-Konsole verwenden, um EF-Befehle für ASP.net Core Projekte auszuführen (Dies wurde in RC1 nicht unterstützt). `tools`Um dies zu tun, müssen Sie die Befehle im Abschnitt von immer noch registrieren `project.json` .

## <a name="package-manager-commands-require-powershell-5"></a>Paket-Manager-Befehle erfordern PowerShell 5

Wenn Sie die Entity Framework-Befehle in der Paket-Manager-Konsole in Visual Studio verwenden, müssen Sie sicherstellen, dass PowerShell 5 installiert ist. Dies ist eine temporäre Anforderung, die in der nächsten Version entfernt wird (Weitere Informationen finden Sie unter [Problem #5327](https://github.com/aspnet/EntityFramework/issues/5327) ).

## <a name="using-imports-in-projectjson"></a>Verwenden von "Imports" in project.js

Einige der Abhängigkeiten von EF Core unterstützen .NET Standard noch nicht. EF Core in .NET Standard und in .net Core-Projekten erfordert möglicherweise das Hinzufügen von "Imports" zum project.jsvon als vorübergehende Problem Umgehung.

Beim Hinzufügen von EF wird in der nuget-Wiederherstellung die folgende Fehlermeldung angezeigt:

``` Console
Package Ix-Async 1.2.5 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Ix-Async 1.2.5 supports:
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8 (.NETPortable,Version=v0.0,Profile=Profile78)
Package Remotion.Linq 2.0.2 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Remotion.Linq 2.0.2 supports:
  - net35 (.NETFramework,Version=v3.5)
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
```

Die Problem Umgehung besteht darin, das Portable Profil "Portable-net451 + win8" manuell zu importieren. Dies erzwingt, dass nuget diese Binärdateien, die diesem entsprechen, als kompatibles Framework mit .NET Standard behandelt, auch wenn dies nicht der Fall ist. Obwohl "Portable-net451 + win8" 100 nicht mit .NET Standard kompatibel ist, ist es für den Übergang von PCL zu .NET Standard kompatibel. Importe können entfernt werden, wenn die Abhängigkeiten von EF schließlich auf .NET Standard aktualisiert werden.

Mehrere Frameworks können "Imports" in der Array Syntax hinzugefügt werden. Andere Importe sind möglicherweise erforderlich, wenn Sie Ihrem Projekt weitere Bibliotheken hinzufügen.

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

Siehe [Problem #5176](https://github.com/aspnet/EntityFramework/issues/5176).
