---
title: Verbindungs Zeichenfolgen-EF Core
description: Verwalten von Verbindungs Zeichenfolgen in unterschiedlichen Umgebungen mit Entity Framework Core
author: bricelam
ms.date: 10/27/2016
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: 75e364eddd02087cffdffd1c152d1e988a99817b
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983559"
---
# <a name="connection-strings"></a>Verbindungszeichenfolgen

Die meisten Datenbankanbieter benötigen eine bestimmte Verbindungs Zeichenfolge, um eine Verbindung mit der Datenbank herzustellen. Manchmal enthält diese Verbindungs Zeichenfolge vertrauliche Informationen, die geschützt werden müssen. Möglicherweise müssen Sie auch die Verbindungs Zeichenfolge ändern, wenn Sie Ihre Anwendung zwischen Umgebungen, wie z. b. Entwicklung, Tests und Produktion, verschieben.

## <a name="aspnet-core"></a>ASP.NET Core

In ASP.net Core das Konfigurationssystem sehr flexibel, und die Verbindungs Zeichenfolge kann in `appsettings.json` , einer Umgebungsvariablen, dem geheimen Benutzerspeicher oder einer anderen Konfigurations Quelle gespeichert werden. Weitere Informationen finden Sie im [Abschnitt zur Konfiguration der ASP.net Core-Dokumentation](/aspnet/core/fundamentals/configuration) .

Beispielsweise können Sie das Geheimnis- [Manager-Tool](/aspnet/core/security/app-secrets#secret-manager) verwenden, um Ihr Daten Bank Kennwort zu speichern, und dann in Gerüstbau eine Verbindungs Zeichenfolge verwenden, die einfach aus besteht `Name=<database-alias>` .

```dotnetcli
dotnet user-secrets set ConnectionStrings:YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=ConnectionStrings:YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

Das folgende Beispiel zeigt die Verbindungs Zeichenfolge, die in gespeichert ist `appsettings.json` .

```json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

Anschließend wird der Kontext in der Regel in konfiguriert `Startup.cs` , wobei die Verbindungs Zeichenfolge aus der Konfiguration gelesen wird. Beachten Sie, dass die- `GetConnectionString()` Methode nach einem Konfigurations Wert sucht, dessen Schlüssel ist `ConnectionStrings:<connection string name>` . Sie müssen den [Microsoft.Extensions.Configurations](/dotnet/api/microsoft.extensions.configuration) -Namespace importieren, um diese Erweiterungsmethode zu verwenden.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```

## <a name="winforms--wpf-applications"></a>WinForms-& WPF-Anwendungen

WinForms-, WPF-und ASP.NET 4-Anwendungen haben ein bewährtes und getestetes Verbindungs Zeichen folgen Muster. Die Verbindungs Zeichenfolge sollte der App.config Datei Ihrer Anwendung hinzugefügt werden (Web.config, wenn Sie ASP.NET verwenden). Wenn Ihre Verbindungs Zeichenfolge vertrauliche Informationen (z. b. Benutzername und Kennwort) enthält, können Sie den Inhalt der Konfigurationsdatei mithilfe der [geschützten Konfiguration](/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration)schützen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>

  <connectionStrings>
    <add name="BloggingDatabase"
         connectionString="Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" />
  </connectionStrings>
</configuration>
```

> [!TIP]
> Die `providerName` Einstellung ist für EF Core Verbindungs Zeichenfolgen, die in App.config gespeichert sind, nicht erforderlich, da der Datenbankanbieter über Code konfiguriert ist.

Anschließend können Sie die Verbindungs `ConfigurationManager` Zeichenfolge mithilfe der-API in der-Methode Ihres Kontexts lesen `OnConfiguring` . Möglicherweise müssen Sie einen Verweis auf die Frameworkassembly `System.Configuration` hinzufügen, damit diese API verwendet werden kann.

```csharp
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

```csharp
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
