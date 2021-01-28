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
# <a name="connection-strings"></a><span data-ttu-id="283dd-103">Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="283dd-103">Connection Strings</span></span>

<span data-ttu-id="283dd-104">Die meisten Datenbankanbieter benötigen eine bestimmte Verbindungs Zeichenfolge, um eine Verbindung mit der Datenbank herzustellen.</span><span class="sxs-lookup"><span data-stu-id="283dd-104">Most database providers require some form of connection string to connect to the database.</span></span> <span data-ttu-id="283dd-105">Manchmal enthält diese Verbindungs Zeichenfolge vertrauliche Informationen, die geschützt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="283dd-105">Sometimes this connection string contains sensitive information that needs to be protected.</span></span> <span data-ttu-id="283dd-106">Möglicherweise müssen Sie auch die Verbindungs Zeichenfolge ändern, wenn Sie Ihre Anwendung zwischen Umgebungen, wie z. b. Entwicklung, Tests und Produktion, verschieben.</span><span class="sxs-lookup"><span data-stu-id="283dd-106">You may also need to change the connection string as you move your application between environments, such as development, testing, and production.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="283dd-107">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="283dd-107">ASP.NET Core</span></span>

<span data-ttu-id="283dd-108">In ASP.net Core das Konfigurationssystem sehr flexibel, und die Verbindungs Zeichenfolge kann in `appsettings.json` , einer Umgebungsvariablen, dem geheimen Benutzerspeicher oder einer anderen Konfigurations Quelle gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="283dd-108">In ASP.NET Core the configuration system is very flexible, and the connection string could be stored in `appsettings.json`, an environment variable, the user secret store, or another configuration source.</span></span> <span data-ttu-id="283dd-109">Weitere Informationen finden Sie im [Abschnitt zur Konfiguration der ASP.net Core-Dokumentation](/aspnet/core/fundamentals/configuration) .</span><span class="sxs-lookup"><span data-stu-id="283dd-109">See the [Configuration section of the ASP.NET Core documentation](/aspnet/core/fundamentals/configuration) for more details.</span></span>

<span data-ttu-id="283dd-110">Beispielsweise können Sie das Geheimnis- [Manager-Tool](/aspnet/core/security/app-secrets#secret-manager) verwenden, um Ihr Daten Bank Kennwort zu speichern, und dann in Gerüstbau eine Verbindungs Zeichenfolge verwenden, die einfach aus besteht `Name=<database-alias>` .</span><span class="sxs-lookup"><span data-stu-id="283dd-110">For instance, you can use the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager) to store your database password and then, in scaffolding, use a connection string that simply consists of `Name=<database-alias>`.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings:YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=ConnectionStrings:YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="283dd-111">Das folgende Beispiel zeigt die Verbindungs Zeichenfolge, die in gespeichert ist `appsettings.json` .</span><span class="sxs-lookup"><span data-stu-id="283dd-111">Or the following example shows the connection string stored in `appsettings.json`.</span></span>

```json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

<span data-ttu-id="283dd-112">Anschließend wird der Kontext in der Regel in konfiguriert `Startup.cs` , wobei die Verbindungs Zeichenfolge aus der Konfiguration gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="283dd-112">Then the context is typically configured in `Startup.cs` with the connection string being read from configuration.</span></span> <span data-ttu-id="283dd-113">Beachten Sie, dass die- `GetConnectionString()` Methode nach einem Konfigurations Wert sucht, dessen Schlüssel ist `ConnectionStrings:<connection string name>` .</span><span class="sxs-lookup"><span data-stu-id="283dd-113">Note the `GetConnectionString()` method looks for a configuration value whose key is `ConnectionStrings:<connection string name>`.</span></span> <span data-ttu-id="283dd-114">Sie müssen den [Microsoft.Extensions.Configurations](/dotnet/api/microsoft.extensions.configuration) -Namespace importieren, um diese Erweiterungsmethode zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="283dd-114">You need to import the [Microsoft.Extensions.Configuration](/dotnet/api/microsoft.extensions.configuration) namespace to use this extension method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```

## <a name="winforms--wpf-applications"></a><span data-ttu-id="283dd-115">WinForms-& WPF-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="283dd-115">WinForms & WPF Applications</span></span>

<span data-ttu-id="283dd-116">WinForms-, WPF-und ASP.NET 4-Anwendungen haben ein bewährtes und getestetes Verbindungs Zeichen folgen Muster.</span><span class="sxs-lookup"><span data-stu-id="283dd-116">WinForms, WPF, and ASP.NET 4 applications have a tried and tested connection string pattern.</span></span> <span data-ttu-id="283dd-117">Die Verbindungs Zeichenfolge sollte der App.config Datei Ihrer Anwendung hinzugefügt werden (Web.config, wenn Sie ASP.NET verwenden).</span><span class="sxs-lookup"><span data-stu-id="283dd-117">The connection string should be added to your application's App.config file (Web.config if you are using ASP.NET).</span></span> <span data-ttu-id="283dd-118">Wenn Ihre Verbindungs Zeichenfolge vertrauliche Informationen (z. b. Benutzername und Kennwort) enthält, können Sie den Inhalt der Konfigurationsdatei mithilfe der [geschützten Konfiguration](/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration)schützen.</span><span class="sxs-lookup"><span data-stu-id="283dd-118">If your connection string contains sensitive information, such as username and password, you can protect the contents of the configuration file using [Protected Configuration](/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration).</span></span>

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
> <span data-ttu-id="283dd-119">Die `providerName` Einstellung ist für EF Core Verbindungs Zeichenfolgen, die in App.config gespeichert sind, nicht erforderlich, da der Datenbankanbieter über Code konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="283dd-119">The `providerName` setting is not required on EF Core connection strings stored in App.config because the database provider is configured via code.</span></span>

<span data-ttu-id="283dd-120">Anschließend können Sie die Verbindungs `ConfigurationManager` Zeichenfolge mithilfe der-API in der-Methode Ihres Kontexts lesen `OnConfiguring` .</span><span class="sxs-lookup"><span data-stu-id="283dd-120">You can then read the connection string using the `ConfigurationManager` API in your context's `OnConfiguring` method.</span></span> <span data-ttu-id="283dd-121">Möglicherweise müssen Sie einen Verweis auf die Frameworkassembly `System.Configuration` hinzufügen, damit diese API verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="283dd-121">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

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

## <a name="universal-windows-platform-uwp"></a><span data-ttu-id="283dd-122">Universelle Windows-Plattform (UWP)</span><span class="sxs-lookup"><span data-stu-id="283dd-122">Universal Windows Platform (UWP)</span></span>

<span data-ttu-id="283dd-123">Verbindungs Zeichenfolgen in einer UWP-Anwendung sind in der Regel eine SQLite-Verbindung, die nur einen lokalen Dateinamen angibt.</span><span class="sxs-lookup"><span data-stu-id="283dd-123">Connection strings in a UWP application are typically a SQLite connection that just specifies a local filename.</span></span> <span data-ttu-id="283dd-124">Sie enthalten in der Regel keine vertraulichen Informationen und müssen nicht geändert werden, wenn eine Anwendung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="283dd-124">They typically do not contain sensitive information, and do not need to be changed as an application is deployed.</span></span> <span data-ttu-id="283dd-125">Daher können diese Verbindungs Zeichenfolgen in der Regel im Code verbleiben, wie unten gezeigt.</span><span class="sxs-lookup"><span data-stu-id="283dd-125">As such, these connection strings are usually fine to be left in code, as shown below.</span></span> <span data-ttu-id="283dd-126">Wenn Sie Sie aus dem Code heraus verschieben möchten, unterstützt UWP das Konzept der Einstellungen. Weitere Informationen finden Sie im [Abschnitt App-Einstellungen der UWP-Dokumentation](/windows/uwp/app-settings/store-and-retrieve-app-data) .</span><span class="sxs-lookup"><span data-stu-id="283dd-126">If you wish to move them out of code then UWP supports the concept of settings, see the [App Settings section of the UWP documentation](/windows/uwp/app-settings/store-and-retrieve-app-data) for details.</span></span>

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
