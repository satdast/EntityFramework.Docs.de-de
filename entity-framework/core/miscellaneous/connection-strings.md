---
title: Verbindungs Zeichenfolgen-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: 062a7f292d16deb3840fd116f270edb11c6e0687
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672923"
---
# <a name="connection-strings"></a>Verbindungszeichenfolgen

Die meisten Datenbankanbieter benötigen eine bestimmte Verbindungs Zeichenfolge, um eine Verbindung mit der Datenbank herzustellen. Manchmal enthält diese Verbindungs Zeichenfolge vertrauliche Informationen, die geschützt werden müssen. Möglicherweise müssen Sie auch die Verbindungs Zeichenfolge ändern, wenn Sie Ihre Anwendung zwischen Umgebungen, wie z. b. Entwicklung, Tests und Produktion, verschieben.

## <a name="winforms--wpf-applications"></a>WinForms-& WPF-Anwendungen

WinForms-, WPF-und ASP.NET 4-Anwendungen haben ein bewährtes und getestetes Verbindungs Zeichen folgen Muster. Die Verbindungs Zeichenfolge sollte der app. config-Datei Ihrer Anwendung hinzugefügt werden (Web. config, wenn Sie ASP.NET verwenden). Wenn Ihre Verbindungs Zeichenfolge vertrauliche Informationen (z. b. Benutzername und Kennwort) enthält, können Sie den Inhalt der Konfigurationsdatei mit dem [Geheimnis-Manager-Tool](/aspnet/core/security/app-secrets#secret-manager)schützen.

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>

  <connectionStrings>
    <add name="BloggingDatabase"
         connectionString="Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" />
  </connectionStrings>
</configuration>
```

> [!TIP]  
> Die `providerName` Einstellung ist für EF Core Verbindungs Zeichenfolgen, die in app. config gespeichert sind, nicht erforderlich, da der Datenbankanbieter über Code konfiguriert ist.

Anschließend können Sie die Verbindungs `ConfigurationManager` Zeichenfolge mithilfe der-API in der-Methode Ihres Kontexts lesen `OnConfiguring` . Möglicherweise müssen Sie einen Verweis auf die FrameworkAssembly hinzufügen `System.Configuration` , damit diese API verwendet werden kann.

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="universal-windows-platform-uwp"></a>Universelle Windows-Plattform (UWP)

Verbindungs Zeichenfolgen in einer UWP-Anwendung sind in der Regel eine SQLite-Verbindung, die nur einen lokalen Dateinamen angibt. Sie enthalten in der Regel keine vertraulichen Informationen und müssen nicht geändert werden, wenn eine Anwendung bereitgestellt wird. Daher können diese Verbindungs Zeichenfolgen in der Regel im Code verbleiben, wie unten gezeigt. Wenn Sie Sie aus dem Code heraus verschieben möchten, unterstützt UWP das Konzept der Einstellungen. Weitere Informationen finden Sie im [Abschnitt App-Einstellungen der UWP-Dokumentation](/windows/uwp/app-settings/store-and-retrieve-app-data) .

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
            optionsBuilder.UseSqlite("Data Source=blogging.db");
    }
}
```

## <a name="aspnet-core"></a>ASP.NET Core

In ASP.net Core das Konfigurationssystem sehr flexibel, und die Verbindungs Zeichenfolge kann in `appsettings.json` , einer Umgebungsvariablen, dem geheimen Benutzerspeicher oder einer anderen Konfigurations Quelle gespeichert werden. Weitere Informationen finden Sie im [Abschnitt zur Konfiguration der ASP.net Core-Dokumentation](/aspnet/core/fundamentals/configuration) .

Beispielsweise können Sie das Geheimnis- [Manager-Tool](/aspnet/core/security/app-secrets#secret-manager) verwenden, um Ihr Daten Bank Kennwort zu speichern, und dann in Gerüstbau eine Verbindungs Zeichenfolge verwenden, die einfach aus besteht `Name=<database-alias>` .

```dotnetcli
dotnet user-secrets set ConnectionStrings.YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

Das folgende Beispiel zeigt die Verbindungs Zeichenfolge, die in gespeichert ist `appsettings.json` .

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

Anschließend wird der Kontext in der Regel in konfiguriert `Startup.cs` , wobei die Verbindungs Zeichenfolge aus der Konfiguration gelesen wird. Beachten Sie, dass die- `GetConnectionString()` Methode nach einem Konfigurations Wert sucht, dessen Schlüssel ist `ConnectionStrings:<connection string name>` . Sie müssen den [Microsoft. Extensions. Configuration](/dotnet/api/microsoft.extensions.configuration) -Namespace importieren, um diese Erweiterungsmethode zu verwenden.

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
