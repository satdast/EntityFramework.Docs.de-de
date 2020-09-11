---
title: 'Portieren von EF6 auf EF Core: Portieren eines codebasierten Modells'
description: Spezifische Informationen zum Portieren einer codebasierten Entity Framework 6-Modellanwendung zu Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: d3920e6132576e3a93dd0ffb9dac1412b6e511aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619622"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a><span data-ttu-id="87ec0-103">Portieren eines codebasierten EF6-Modells auf EF Core</span><span class="sxs-lookup"><span data-stu-id="87ec0-103">Porting an EF6 Code-Based Model to EF Core</span></span>

<span data-ttu-id="87ec0-104">Wenn Sie alle wichtigen Hinweise gelesen haben und bereit für die Portierung sind, finden Sie hier einige Richtlinien für den Anfang.</span><span class="sxs-lookup"><span data-stu-id="87ec0-104">If you've read all the caveats and you are ready to port, then here are some guidelines to help you get started.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="87ec0-105">Installieren von EF Core-NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="87ec0-105">Install EF Core NuGet packages</span></span>

<span data-ttu-id="87ec0-106">Installieren Sie das NuGet-Paket für den gewünschten Datenbankanbieter, um EF Core zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="87ec0-106">To use EF Core, you install the NuGet package for the database provider you want to use.</span></span> <span data-ttu-id="87ec0-107">Wenn Sie SQL Server nutzen möchten, würden Sie beispielsweise `Microsoft.EntityFrameworkCore.SqlServer` installieren.</span><span class="sxs-lookup"><span data-stu-id="87ec0-107">For example, when targeting SQL Server, you would install `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="87ec0-108">Weitere Informationen finden Sie unter [Datenbankanbieter](xref:core/providers/index).</span><span class="sxs-lookup"><span data-stu-id="87ec0-108">See [Database Providers](xref:core/providers/index) for details.</span></span>

<span data-ttu-id="87ec0-109">Wenn Sie beabsichtigen, Migrationen zu verwenden, sollten Sie auch das `Microsoft.EntityFrameworkCore.Tools`-Paket installieren.</span><span class="sxs-lookup"><span data-stu-id="87ec0-109">If you are planning to use migrations, then you should also install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span>

<span data-ttu-id="87ec0-110">Sie müssen das EF6-NuGet-Paket (EntityFramework) nicht deinstallieren, da EF Core und EF6 parallel in derselben Anwendung verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="87ec0-110">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="87ec0-111">Wenn Sie jedoch nicht beabsichtigen, EF6 in Ihrer Anwendung zu verwenden, kann das Deinstallieren des Pakets dazu beitragen, dass nur zu den Codebestandteilen Kompilierfehler ausgegeben werden, die wirklich Ihre Aufmerksamkeit erfordern.</span><span class="sxs-lookup"><span data-stu-id="87ec0-111">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="swap-namespaces"></a><span data-ttu-id="87ec0-112">Austauschen von Namespaces</span><span class="sxs-lookup"><span data-stu-id="87ec0-112">Swap namespaces</span></span>

<span data-ttu-id="87ec0-113">Die meisten APIs, die Sie in EF6 verwenden, befinden sich im `System.Data.Entity`-Namespace (und in den zugehörigen Subnamespaces).</span><span class="sxs-lookup"><span data-stu-id="87ec0-113">Most APIs that you use in EF6 are in the `System.Data.Entity` namespace (and related sub-namespaces).</span></span> <span data-ttu-id="87ec0-114">Die erste Codeänderung besteht darin, den Namespace durch `Microsoft.EntityFrameworkCore` auszutauschen.</span><span class="sxs-lookup"><span data-stu-id="87ec0-114">The first code change is to swap to the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="87ec0-115">In der Regel beginnen Sie mit der Codedatei, die den abgeleiteten Kontext enthält, und arbeiten von dort aus weiter, um Kompilierungsfehler zu beheben, sobald diese auftreten.</span><span class="sxs-lookup"><span data-stu-id="87ec0-115">You would typically start with your derived context code file and then work out from there, addressing compilation errors as they occur.</span></span>

## <a name="context-configuration-connection-etc"></a><span data-ttu-id="87ec0-116">Kontextkonfiguration (z. B. Verbindungen)</span><span class="sxs-lookup"><span data-stu-id="87ec0-116">Context configuration (connection etc.)</span></span>

<span data-ttu-id="87ec0-117">Wie unter [Portieren von EF 6 nach EF Core](xref:efcore-and-ef6/porting/index) beschrieben wird, ist EF Core beim Erkennen der Datenbank, mit der eine Verbindung hergestellt werden soll, weniger eigenständig.</span><span class="sxs-lookup"><span data-stu-id="87ec0-117">As described in [Ensure EF Core Will Work for Your Application](xref:efcore-and-ef6/porting/index), EF Core has less magic around detecting the database to connect to.</span></span> <span data-ttu-id="87ec0-118">Sie müssen die `OnConfiguring`-Methode in Ihrem abgeleiteten Kontext überschreiben und die datenbankanbieterspezifische API verwenden, um die Verbindung mit der Datenbank einzurichten.</span><span class="sxs-lookup"><span data-stu-id="87ec0-118">You will need to override the `OnConfiguring` method on your derived context, and use the database provider specific API to setup the connection to the database.</span></span>

<span data-ttu-id="87ec0-119">In den meisten EF6-Anwendungen wird die Verbindungszeichenfolge in der Anwendungsdatei `App/Web.config` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="87ec0-119">Most EF6 applications store the connection string in the applications `App/Web.config` file.</span></span> <span data-ttu-id="87ec0-120">In EF Core wird diese Verbindungszeichenfolge mit der `ConfigurationManager`-API gelesen.</span><span class="sxs-lookup"><span data-stu-id="87ec0-120">In EF Core, you read this connection string using the `ConfigurationManager` API.</span></span> <span data-ttu-id="87ec0-121">Möglicherweise müssen Sie einen Verweis auf die Frameworkassembly `System.Configuration` hinzufügen, damit diese API verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="87ec0-121">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

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

## <a name="update-your-code"></a><span data-ttu-id="87ec0-122">Aktualisieren Ihres Codes</span><span class="sxs-lookup"><span data-stu-id="87ec0-122">Update your code</span></span>

<span data-ttu-id="87ec0-123">An dieser Stelle ist es wichtig, Kompilierfehler zu beheben und ein Code Review durchzuführen, um festzustellen, ob die Verhaltensänderungen eine Auswirkung zeigen.</span><span class="sxs-lookup"><span data-stu-id="87ec0-123">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes will impact you.</span></span>

## <a name="existing-migrations"></a><span data-ttu-id="87ec0-124">Vorhandene Migrationen</span><span class="sxs-lookup"><span data-stu-id="87ec0-124">Existing migrations</span></span>

<span data-ttu-id="87ec0-125">Es gibt derzeit keine umsetzbare Möglichkeit, vorhandene EF6-Migrationen auf EF Core zu portieren.</span><span class="sxs-lookup"><span data-stu-id="87ec0-125">There isn't really a feasible way to port existing EF6 migrations to EF Core.</span></span>

<span data-ttu-id="87ec0-126">Sie sollten nach Möglichkeit davon ausgehen, dass alle vorherigen Migrationen von EF6 auf die Datenbank angewendet wurden und dann mit der Migration des Schemas von diesem Punkt aus unter Einsatz von EF Core beginnen.</span><span class="sxs-lookup"><span data-stu-id="87ec0-126">If possible, it is best to assume that all previous migrations from EF6 have been applied to the database and then start migrating the schema from that point using EF Core.</span></span> <span data-ttu-id="87ec0-127">Hierfür können Sie den Befehl `Add-Migration` verwenden, um eine Migration hinzuzufügen, sobald das Modell auf EF Core portiert wurde.</span><span class="sxs-lookup"><span data-stu-id="87ec0-127">To do this, you would use the `Add-Migration` command to add a migration once the model is ported to EF Core.</span></span> <span data-ttu-id="87ec0-128">Anschließend entfernen Sie den gesamten Code aus den Methoden `Up` und `Down` der Gerüstmigration.</span><span class="sxs-lookup"><span data-stu-id="87ec0-128">You would then remove all code from the `Up` and `Down` methods of the scaffolded migration.</span></span> <span data-ttu-id="87ec0-129">Nachfolgende Migrationen werden mit dem Modell verglichen, wenn für die anfängliche Migration ein Gerüst erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="87ec0-129">Subsequent migrations will compare to the model when that initial migration was scaffolded.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="87ec0-130">Testen des Ports</span><span class="sxs-lookup"><span data-stu-id="87ec0-130">Test the port</span></span>

<span data-ttu-id="87ec0-131">Nur weil Ihre Anwendung erfolgreich kompiliert wird, bedeutet das nicht, dass sie auch erfolgreich auf EF Core portiert wurde.</span><span class="sxs-lookup"><span data-stu-id="87ec0-131">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="87ec0-132">Sie müssen alle Bereiche Ihrer Anwendung testen, um sicherzustellen, dass keine der Verhaltensänderungen Ihre Anwendung beeinträchtigt hat.</span><span class="sxs-lookup"><span data-stu-id="87ec0-132">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
