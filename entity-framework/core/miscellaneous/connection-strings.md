---
title: Verbindungs Zeichenfolgen-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: e955e93723fc371170641b0b3209cca014ef1c26
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238150"
---
# <a name="connection-strings"></a><span data-ttu-id="f0033-102">Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="f0033-102">Connection Strings</span></span>

<span data-ttu-id="f0033-103">Die meisten Datenbankanbieter benötigen eine bestimmte Verbindungs Zeichenfolge, um eine Verbindung mit der Datenbank herzustellen.</span><span class="sxs-lookup"><span data-stu-id="f0033-103">Most database providers require some form of connection string to connect to the database.</span></span> <span data-ttu-id="f0033-104">Manchmal enthält diese Verbindungs Zeichenfolge vertrauliche Informationen, die geschützt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="f0033-104">Sometimes this connection string contains sensitive information that needs to be protected.</span></span> <span data-ttu-id="f0033-105">Möglicherweise müssen Sie auch die Verbindungs Zeichenfolge ändern, wenn Sie Ihre Anwendung zwischen Umgebungen, wie z. b. Entwicklung, Tests und Produktion, verschieben.</span><span class="sxs-lookup"><span data-stu-id="f0033-105">You may also need to change the connection string as you move your application between environments, such as development, testing, and production.</span></span>

## <a name="winforms--wpf-applications"></a><span data-ttu-id="f0033-106">WinForms-& WPF-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="f0033-106">WinForms & WPF Applications</span></span>

<span data-ttu-id="f0033-107">WinForms-, WPF-und ASP.NET 4-Anwendungen haben ein bewährtes und getestetes Verbindungs Zeichen folgen Muster.</span><span class="sxs-lookup"><span data-stu-id="f0033-107">WinForms, WPF, and ASP.NET 4 applications have a tried and tested connection string pattern.</span></span> <span data-ttu-id="f0033-108">Die Verbindungs Zeichenfolge sollte der App.config Datei Ihrer Anwendung hinzugefügt werden (Web.config, wenn Sie ASP.NET verwenden).</span><span class="sxs-lookup"><span data-stu-id="f0033-108">The connection string should be added to your application's App.config file (Web.config if you are using ASP.NET).</span></span> <span data-ttu-id="f0033-109">Wenn Ihre Verbindungs Zeichenfolge vertrauliche Informationen (z. b. Benutzername und Kennwort) enthält, können Sie den Inhalt der Konfigurationsdatei mit dem [Geheimnis-Manager-Tool](/aspnet/core/security/app-secrets#secret-manager)schützen.</span><span class="sxs-lookup"><span data-stu-id="f0033-109">If your connection string contains sensitive information, such as username and password, you can protect the contents of the configuration file using the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager).</span></span>

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
> <span data-ttu-id="f0033-110">Die `providerName` Einstellung ist für EF Core Verbindungs Zeichenfolgen, die in App.config gespeichert sind, nicht erforderlich, da der Datenbankanbieter über Code konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="f0033-110">The `providerName` setting is not required on EF Core connection strings stored in App.config because the database provider is configured via code.</span></span>

<span data-ttu-id="f0033-111">Anschließend können Sie die Verbindungs `ConfigurationManager` Zeichenfolge mithilfe der-API in der-Methode Ihres Kontexts lesen `OnConfiguring` .</span><span class="sxs-lookup"><span data-stu-id="f0033-111">You can then read the connection string using the `ConfigurationManager` API in your context's `OnConfiguring` method.</span></span> <span data-ttu-id="f0033-112">Möglicherweise müssen Sie einen Verweis auf die FrameworkAssembly hinzufügen `System.Configuration` , damit diese API verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f0033-112">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

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

## <a name="universal-windows-platform-uwp"></a><span data-ttu-id="f0033-113">Universelle Windows-Plattform (UWP)</span><span class="sxs-lookup"><span data-stu-id="f0033-113">Universal Windows Platform (UWP)</span></span>

<span data-ttu-id="f0033-114">Verbindungs Zeichenfolgen in einer UWP-Anwendung sind in der Regel eine SQLite-Verbindung, die nur einen lokalen Dateinamen angibt.</span><span class="sxs-lookup"><span data-stu-id="f0033-114">Connection strings in a UWP application are typically a SQLite connection that just specifies a local filename.</span></span> <span data-ttu-id="f0033-115">Sie enthalten in der Regel keine vertraulichen Informationen und müssen nicht geändert werden, wenn eine Anwendung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="f0033-115">They typically do not contain sensitive information, and do not need to be changed as an application is deployed.</span></span> <span data-ttu-id="f0033-116">Daher können diese Verbindungs Zeichenfolgen in der Regel im Code verbleiben, wie unten gezeigt.</span><span class="sxs-lookup"><span data-stu-id="f0033-116">As such, these connection strings are usually fine to be left in code, as shown below.</span></span> <span data-ttu-id="f0033-117">Wenn Sie Sie aus dem Code heraus verschieben möchten, unterstützt UWP das Konzept der Einstellungen. Weitere Informationen finden Sie im [Abschnitt App-Einstellungen der UWP-Dokumentation](/windows/uwp/app-settings/store-and-retrieve-app-data) .</span><span class="sxs-lookup"><span data-stu-id="f0033-117">If you wish to move them out of code then UWP supports the concept of settings, see the [App Settings section of the UWP documentation](/windows/uwp/app-settings/store-and-retrieve-app-data) for details.</span></span>

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

## <a name="aspnet-core"></a><span data-ttu-id="f0033-118">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f0033-118">ASP.NET Core</span></span>

<span data-ttu-id="f0033-119">In ASP.net Core das Konfigurationssystem sehr flexibel, und die Verbindungs Zeichenfolge kann in `appsettings.json` , einer Umgebungsvariablen, dem geheimen Benutzerspeicher oder einer anderen Konfigurations Quelle gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="f0033-119">In ASP.NET Core the configuration system is very flexible, and the connection string could be stored in `appsettings.json`, an environment variable, the user secret store, or another configuration source.</span></span> <span data-ttu-id="f0033-120">Weitere Informationen finden Sie im [Abschnitt zur Konfiguration der ASP.net Core-Dokumentation](/aspnet/core/fundamentals/configuration) .</span><span class="sxs-lookup"><span data-stu-id="f0033-120">See the [Configuration section of the ASP.NET Core documentation](/aspnet/core/fundamentals/configuration) for more details.</span></span>

<span data-ttu-id="f0033-121">Beispielsweise können Sie das Geheimnis- [Manager-Tool](/aspnet/core/security/app-secrets#secret-manager) verwenden, um Ihr Daten Bank Kennwort zu speichern, und dann in Gerüstbau eine Verbindungs Zeichenfolge verwenden, die einfach aus besteht `Name=<database-alias>` .</span><span class="sxs-lookup"><span data-stu-id="f0033-121">For instance, you can use the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager) to store your database password and then, in scaffolding, use a connection string that simply consists of `Name=<database-alias>`.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=ConnectionStrings.YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="f0033-122">Das folgende Beispiel zeigt die Verbindungs Zeichenfolge, die in gespeichert ist `appsettings.json` .</span><span class="sxs-lookup"><span data-stu-id="f0033-122">Or the following example shows the connection string stored in `appsettings.json`.</span></span>

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

<span data-ttu-id="f0033-123">Anschließend wird der Kontext in der Regel in konfiguriert `Startup.cs` , wobei die Verbindungs Zeichenfolge aus der Konfiguration gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="f0033-123">Then the context is typically configured in `Startup.cs` with the connection string being read from configuration.</span></span> <span data-ttu-id="f0033-124">Beachten Sie, dass die- `GetConnectionString()` Methode nach einem Konfigurations Wert sucht, dessen Schlüssel ist `ConnectionStrings:<connection string name>` .</span><span class="sxs-lookup"><span data-stu-id="f0033-124">Note the `GetConnectionString()` method looks for a configuration value whose key is `ConnectionStrings:<connection string name>`.</span></span> <span data-ttu-id="f0033-125">Sie müssen den [Microsoft.Extensions.Configurations](/dotnet/api/microsoft.extensions.configuration) -Namespace importieren, um diese Erweiterungsmethode zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="f0033-125">You need to import the [Microsoft.Extensions.Configuration](/dotnet/api/microsoft.extensions.configuration) namespace to use this extension method.</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
