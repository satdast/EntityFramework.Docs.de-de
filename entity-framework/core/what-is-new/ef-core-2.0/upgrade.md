---
title: 'EF Core: Upgrade für frühere Versionen auf EF Core 2'
description: In diesem Artikel finden Sie Anweisungen und Hinweise zum Durchführen eines Upgrades auf Entity Framework Core 2.0.
author: ajcvickers
ms.date: 08/13/2017
uid: core/what-is-new/ef-core-2.0/upgrade
ms.openlocfilehash: c7c736629209da99f191ceb0d4000d19f40414b9
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063438"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a>Durchführen eines Upgrades für Anwendungen mit früheren Versionen auf EF Core 2.0

Die vorhandenen APIs und Verhaltensweisen wurden mit EF Core 2.0 deutlich verfeinert. Für einige dieser Verbesserungen können Änderungen an vorhandenem Anwendungscode erforderlich sein. Dies sollte für einen Großteil von Anwendungen nur geringe Auswirkungen haben. In den meisten Fällen sollten lediglich eine Neukompilierung und minimale Änderungen zum Ersetzen veralteter APIs erforderlich sein.

Für das Update einer vorhandenen Anwendung auf EF Core 2.0 kann Folgendes erforderlich sein:

1. Durchführen eines Upgrade für die .NET-Zielimplementierung der Anwendung auf eine Version, die .NET Standard 2.0 unterstützt (Weitere Informationen finden Sie unter [Unterstützte .NET-Implementierungen](xref:core/platforms/index).)

2. Identifizieren eines Anbieters für die Zieldatenbank, die mit EF Core 2.0 kompatibel ist (Weitere Informationen finden Sie unten unter [EF Core 2.0 erfordert einen 2.0-Datenbankanbieter](#ef-core-20-requires-a-20-database-provider).)

3. Durchführen von Upgrades auf Version 2.0 für alle EF Core-Pakete (Runtime und Tools) (Ausführliche Informationen finden Sie unter [Installieren von EF Core](xref:core/get-started/install/index).)

4. Vornehmen jeglicher notwendigen Codeänderungen, um die im restlichen Dokument beschriebenen Breaking Changes auszugleichen

## <a name="aspnet-core-now-includes-ef-core"></a>ASP.NET Core umfasst nun EF Core

Anwendungen, deren Zielversionen für ASP.NET Core 2.0 festgelegt sind, können neben Datenbankanbietern von Drittanbietern EF Core 2.0 ohne zusätzliche Abhängigkeiten nutzen. Allerdings müssen für Anwendungen für vorherige Versionen von ASP.NET Core Upgrades auf ASP.NET Core 2.0 durchgeführt werden, damit EF Core 2.0 verwendet werden kann. Ausführliche Informationen zum Durchführen von Upgrades für ASP.NET Core-Anwendungen auf Version 2.0 finden Sie in der [ASP.NET Core-Dokumentation zum Thema](/aspnet/core/migration/1x-to-2x/).

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a>Neue Methode zum Abrufen der Anwendungsdienste in ASP.NET Core

Das empfohlene Muster für ASP.NET Core-Webanwendungen wurde für Version 2.0 so aktualisiert, dass die Entwurfszeitlogik von EF Core 1.x nicht mehr funktionierte. Zuvor versuchte EF Core zur Entwurfszeit, `Startup.ConfigureServices` direkt aufzurufen, um auf den Dienstanbieter der Anwendung zuzugreifen. In ASP.NET Core 2.0 wird die Konfiguration außerhalb der `Startup`-Klasse initialisiert. Anwendungen mit EF Core greifen in der Regel über die Konfiguration auf ihre Verbindungszeichenfolge zu, sodass `Startup` allein nicht mehr ausreicht. Wenn Sie ein Upgrade für eine ASP.NET Core 1.x-Anwendung durchführen, wird möglicherweise der folgende Fehler ausgelöst, wenn Sie EF Core-Tools verwenden.

> No parameterless constructor was found on „ApplicationContext'“. (Es wurde kein parameterloser Konstruktor in „ApplicationContext“ gefunden). Fügen Sie entweder einen parameterlosen Konstruktor zu „ApplicationContext“ hinzu, oder fügen Sie eine Implementierung von „IDesignTimeDbContextFactory&lt;ApplicationContext&gt;“ in derselben Assembly wie „ApplicationContext“ hinzu.

Zur Standardvorlage von ASP.NET Core 2.0 wurde ein neues Entwurfszeithook hinzugefügt. Die statische `Program.BuildWebHost`-Methode ermöglicht EF Core zur Entwurfszeit den Zugriff auf den Dienstanbieter der Anwendung. Wenn Sie ein Upgrade für eine ASP.NET Core 1.x-Anwendung durchführen, müssen Sie die `Program`-Klasse aktualisieren, damit sie der folgenden ähnelt.

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;

namespace AspNetCoreDotNetCore2._0App
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
}
```

Die Einführung dieses Musters beim Durchführen von Updates auf Version 2.0 für Anwendung wird dringend empfohlen und ist erforderlich, damit Produktfeatures wie Entity Framework Core-Migrationen funktionieren. Die gängige Alternative besteht darin, [*IDesignTimeDbContextFactory\<TContext>* zu implementieren](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory).

## <a name="idbcontextfactory-renamed"></a>Umbenennung von IDbContextFactory

In der Vergangenheit wurde die `IDbContextFactory<TContext>`-Schnittstelle zur Verfügung gestellt, um diverse Anwendungsmuster zu unterstützen und Benutzern mehr Kontrolle über die Verwendung von `DbContext` zur Entwurfszeit zu bieten. Zur Entwurfszeit ermitteln die EF Core-Tools Implementierungen dieser Schnittstelle in Ihrem Projekt und nutzen diese zum Erstellen von `DbContext`-Objekten.

Diese Schnittstelle hatte einen sehr allgemeinen Namen, der einige Benutzer insofern irregeführt hat, dass sie versucht haben, sie für andere Szenarios zum Erstellen von `DbContext` wiederzuverwenden. Diese Benutzer waren dann überrascht, wenn die EF-Tools daraufhin versuchten, ihre Implementierung zur Entwurfszeit zu verwenden, was zu Fehlern bei Befehlen wie `Update-Database` oder `dotnet ef database update` führte.

Die Schnittstelle wurde in `IDesignTimeDbContextFactory<TContext>` umbenannt, um die starke Entwurfszeitsemantik dieser Schnittstelle zu verdeutlichen.

Für das Release der Version 2.0 ist die Schnittstelle `IDbContextFactory<TContext>` weiterhin vorhanden, jedoch wurde sie als veraltet gekennzeichnet.

## <a name="dbcontextfactoryoptions-removed"></a>Entfernung von DbContextFactoryOptions

Aufgrund der oben beschriebenen ASP.NET Core 2.0-Änderungen wurde festgestellt, dass `DbContextFactoryOptions` nicht mehr für die neue `IDesignTimeDbContextFactory<TContext>`-Schnittstelle benötigt wurde. Im Folgenden finden Sie Alternativen, die Sie stattdessen verwenden sollten.

| DbContextFactoryOptions | Alternative                                                  |
|:------------------------|:-------------------------------------------------------------|
| ApplicationBasePath     | AppContext.BaseDirectory                                     |
| ContentRootPath         | Directory.GetCurrentDirectory()                              |
| EnvironmentName         | Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") |

## <a name="design-time-working-directory-changed"></a>Änderung am Entwurfszeit-Arbeitsverzeichnis

Die ASP.NET Core 2.0-Änderungen erforderten außerdem, dass das von `dotnet ef` verwendete Arbeitsverzeichnis dem von Visual Studio verwendeten Arbeitsverzeichnis entspricht, wenn Ihre Anwendung ausgeführt wird. Eine beobachtbare Nebenwirkung hiervon besteht darin, dass SQLite-Dateinamen sich nun nach dem Projektverzeichnis richten, anstatt wie zuvor nach dem Ausgabeverzeichnis.

## <a name="ef-core-20-requires-a-20-database-provider"></a>EF Core 2.0 erfordert einen 2.0-Datenbankanbieter

Für EF Core 2.0 wurden viele Vereinfachungen und Verbesserungen an der Funktionsweise von Datenbankanbietern vorgenommen. Das bedeutet, dass 1.0.x- und 1.1.x-Anbieter nicht mit EF Core 2.0 verwendet werden können.

Die SQL Server- und SQLite-Anbieter werden vom EF-Team bereitgestellt, und die entsprechenden 2.0-Versionen werden im Rahmen des Release von Version 2.0 zur Verfügung gestellt. Die Open-Source-Drittanbieter für [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact), [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL) und [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) werden für Version 2.0 aktualisiert. Für Informationen zu allen anderen Anbietern wenden Sie sich an den Writer des Anbieters.

## <a name="logging-and-diagnostics-events-have-changed"></a>Änderungen an der Protokollierung und an Diagnoseereignissen

Hinweis: Diese Änderungen sollten sich auf den meisten Anwendungscode nicht auswirken.

Die Ereignis-IDs für an eine [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger)-Schnittstelle gesendete Nachrichten wurden in 2.0 geändert. Die Ereignis-IDs sind nun für alle EF Core-Codes eindeutig. Diese Nachrichten entsprechen nun auch dem Standardmuster für die strukturierte Protokollierung, das z.B. von MVC eingesetzt wird.

Die Protokollierungskategorien haben sich ebenfalls geändert. Nun gibt es eine bekannte Gruppe von Kategorien, auf die über [DbLoggerCategory](https://github.com/aspnet/EntityFrameworkCore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs) zugegriffen werden kann.

[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)-Ereignisse verwenden nun dieselben Ereignis-ID-Namen wie die entsprechenden `ILogger`-Nachrichten. Die Ereignisnutzdaten sind nominale Typen, die von [EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata) abgeleitet wurden.

Ereignis-IDs, Nutzdatentypen und Kategorien werden in den Klassen [CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid) und [RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid) dokumentiert.

Außerdem wurden IDs von „Microsoft.EntityFrameworkCore.Infrastructure“ in den neuen Namespace „Microsoft.EntityFrameworkCore.Diagnostics“ verschoben.

## <a name="ef-core-relational-metadata-api-changes"></a>Änderungen an der EF Core-API für relationale Metadaten

EF Core 2.0 erstellt nun eine andere [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel)-Klasse für die verschiedenen verwendeten Anbieter. Dies geschieht in der Regel auf eine für die Anwendung transparente Weise. Dies hat eine Vereinfachung der untergeordneten Metadaten-APIs ermöglicht, sodass Zugriffe auf _gemeinsame relationale Metadatenkonzepte_ immer durch Aufrufen von `.Relational` statt `.SqlServer`, `.Sqlite` usw. erfolgen. Beispielsweise sollte der folgende 1.1.x-Code:

```csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

nun wie folgt geschrieben werden:

```csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

Anstatt Methoden wie `ForSqlServerToTable` zu verwenden, stehen Ihnen nun Erweiterungsmethoden zum Schreiben von Bedingungscode auf Grundlage des aktuell verwendeten Anbieters zur Verfügung. Beispiel:

```csharp
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

Beachten Sie, dass diese Änderung nur für APIs und Metadaten gilt, die für _alle_ relationalen Anbieter definiert sind. Die API und Metadaten bleiben unverändert, wenn sie für einen spezifischen einzelnen Anbieter vorgesehen sind. Beispielsweise sind gruppierte Indizes für SQL Server vorgesehen, daher müssen `ForSqlServerIsClustered` und `.SqlServer().IsClustered()` weiterhin verwendet werden.

## <a name="dont-take-control-of-the-ef-service-provider"></a>Kontrolle des EF-Dienstanbieters nicht übernehmen

EF Core nutzt einen internen Container für die Abhängigkeitsinjektion (`IServiceProvider`) für die interne Implementierung. Anwendungen sollten EF Core erlauben, diesen Anbieter zu erstellen und zu verwalten. Dies gilt in besonderen Fällen nicht. Ziehen Sie unbedingt in Erwägung, jegliche Aufrufe von `UseInternalServiceProvider` zu entfernen. Wenn eine Anwendung `UseInternalServiceProvider` nicht aufrufen muss, ziehen Sie es in Betracht, [ein Issue einzureichen](https://github.com/dotnet/efcore/Issues), damit andere Lösungsmöglichkeiten für Ihr Szenario untersucht werden können.

Aufrufe von `AddEntityFramework`, `AddEntityFrameworkSqlServer` und so weiter sind nicht für den Anwendungscode erforderlich, es sei denn, `UseInternalServiceProvider` wird ebenfalls aufgerufen. Entfernen Sie jegliche vorhandenen Aufrufe von `AddEntityFramework` oder `AddEntityFrameworkSqlServer`. `AddDbContext` sollte weiterhin wie zuvor verwendet werden.

## <a name="in-memory-databases-must-be-named"></a>In-Memory-Datenbanken müssen Namen erhalten

Die globale unbenannte In-Memory-Datenbank wurde entfernt, stattdessen müssen nun alle In-Memory-Datenbanken benannt werden. Beispiel:

```csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

Hiermit wird eine Datenbank mit dem Namen „MyDatabase“ erstellt bzw. verwendet. Wenn `UseInMemoryDatabase` noch mal mit demselben Namen aufgerufen wird, wird dieselbe In-Memory-Datenbank verwendet, wodurch diese gemeinsam von mehreren Kontextinstanzen verwendet werden kann.

## <a name="read-only-api-changes"></a>Änderungen an schreibgeschützten APIs

`IsReadOnlyBeforeSave`, `IsReadOnlyAfterSave` und `IsStoreGeneratedAlways` sind veraltet und wurden durch [BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior) und [AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior) ersetzt. Diese Verhaltensweisen gelten für alle Eigenschaften (nicht nur für vom Speicher generierte Eigenschaften) und bestimmen, wie der Wert der Eigenschaft verwendet werden sollte, wenn in eine Datenbankzeile eingefügt (`BeforeSaveBehavior`) oder eine vorhandene Datenbank geändert wird (`AfterSaveBehavior`).

Mit [ValueGenerated.OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) gekennzeichnete Eigenschaften (z. B. für berechnete Spalten) ignorieren standardmäßig jegliche Werte, die für die Eigenschaft festgelegt sind. Das bedeutet, dass ein vom Speicher generierter Wert immer abgerufen wird, unabhängig davon, ob ein Wert der überwachten Entität festgelegt oder geändert wurde. Dies können Sie ändern, indem Sie andere `Before\AfterSaveBehavior`-Eigenschaften angeben.

## <a name="new-clientsetnull-delete-behavior"></a>Neues Löschverhalten von ClientSetNull

In vorherigen Releases verfügte [DeleteBehavior.Restrict](/dotnet/api/microsoft.entityframeworkcore.deletebehavior) über ein Verhalten für vom Kontext überwachte Entitäten, das eher mit `SetNull`-Semantiken übereinstimmte. Mit EF Core 2.0 wurde ein neues `ClientSetNull`-Verhalten als Standardeinstellung für optionale Beziehungen eingeführt. Dies Verhalten umfasst `SetNull`-Semantiken für überwachte Entitäten und `Restrict`-Verhalten für Datenbanken, die mit EF Core erstellt wurden. Erfahrungsgemäß handelt es sich dabei um die am häufigsten erwarteten bzw. nützlichsten Verhalten für überwachte Entitäten und die Datenbank. `DeleteBehavior.Restrict` wird nun für überwachte Entitäten berücksichtigt, wenn es für optionale Beziehungen festgelegt wird.

## <a name="provider-design-time-packages-removed"></a>Entwurfszeitpakete für Anbieter wurden entfernt

Das Paket `Microsoft.EntityFrameworkCore.Relational.Design` wurde entfernt. Die Inhalte wurden in `Microsoft.EntityFrameworkCore.Relational` und `Microsoft.EntityFrameworkCore.Design` konsolidiert.

Dies wird in die Entwurfszeitpakete für Anbieter weitergeleitet. Diese Pakete (`Microsoft.EntityFrameworkCore.Sqlite.Design`, `Microsoft.EntityFrameworkCore.SqlServer.Design` usw.) wurden entfernt, und ihre Inhalte wurden in die Hauptpakete für Anbieter konsolidiert.

Zum Aktivieren von `Scaffold-DbContext` oder `dotnet ef dbcontext scaffold` in EF Core 2.0 müssen Sie lediglich auf ein einzelnes Anbieterpaket verweisen:

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
