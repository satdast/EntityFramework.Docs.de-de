---
title: Code basierte Konfiguration-EF6
description: Code basierte Konfiguration in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/code-based
ms.openlocfilehash: 67bb7ebd620c90ebe80983cc5baa6cab032907f8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070730"
---
# <a name="code-based-configuration"></a><span data-ttu-id="34271-103">Code basierte Konfiguration</span><span class="sxs-lookup"><span data-stu-id="34271-103">Code-based configuration</span></span>
> [!NOTE]
> <span data-ttu-id="34271-104">**Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="34271-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="34271-105">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="34271-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="34271-106">Die Konfiguration für eine Entity Framework Anwendung kann in einer Konfigurationsdatei (app.config/web.config) oder über Code angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="34271-106">Configuration for an Entity Framework application can be specified in a config file (app.config/web.config) or through code.</span></span> <span data-ttu-id="34271-107">Letzteres wird als Code basierte Konfiguration bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="34271-107">The latter is known as code-based configuration.</span></span>  

<span data-ttu-id="34271-108">Die Konfiguration in einer Konfigurationsdatei wird in einem [separaten Artikel](xref:ef6/fundamentals/configuring/config-file)beschrieben.</span><span class="sxs-lookup"><span data-stu-id="34271-108">Configuration in a config file is described in a [separate article](xref:ef6/fundamentals/configuring/config-file).</span></span> <span data-ttu-id="34271-109">Die Konfigurationsdatei hat Vorrang vor der Code basierten Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="34271-109">The config file takes precedence over code-based configuration.</span></span> <span data-ttu-id="34271-110">Anders ausgedrückt: Wenn eine Konfigurationsoption sowohl im Code als auch in der Konfigurationsdatei festgelegt wird, wird die Einstellung in der Konfigurationsdatei verwendet.</span><span class="sxs-lookup"><span data-stu-id="34271-110">In other words, if a configuration option is set in both code and in the config file, then the setting in the config file is used.</span></span>  

## <a name="using-dbconfiguration"></a><span data-ttu-id="34271-111">Verwenden von dbconfiguration</span><span class="sxs-lookup"><span data-stu-id="34271-111">Using DbConfiguration</span></span>  

<span data-ttu-id="34271-112">Die Code basierte Konfiguration in EF6 und höher wird durch Erstellen einer Unterklasse von System.Data.Entity.Config erreicht. DbConfiguration.</span><span class="sxs-lookup"><span data-stu-id="34271-112">Code-based configuration in EF6 and above is achieved by creating a subclass of System.Data.Entity.Config.DbConfiguration.</span></span> <span data-ttu-id="34271-113">Beachten Sie die folgenden Richtlinien, wenn Sie dbconfiguration Unterklassen:</span><span class="sxs-lookup"><span data-stu-id="34271-113">The following guidelines should be followed when subclassing DbConfiguration:</span></span>  

- <span data-ttu-id="34271-114">Erstellen Sie nur eine dbconfiguration-Klasse für Ihre Anwendung.</span><span class="sxs-lookup"><span data-stu-id="34271-114">Create only one DbConfiguration class for your application.</span></span> <span data-ttu-id="34271-115">Diese Klasse gibt App-Domänen weite Einstellungen an.</span><span class="sxs-lookup"><span data-stu-id="34271-115">This class specifies app-domain wide settings.</span></span>  
- <span data-ttu-id="34271-116">Platzieren Sie die dbconfiguration-Klasse in der gleichen Assembly wie die dbcontext-Klasse.</span><span class="sxs-lookup"><span data-stu-id="34271-116">Place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="34271-117">(Weitere Informationen finden Sie im Abschnitt *Verschieben von dbconfiguration* .)</span><span class="sxs-lookup"><span data-stu-id="34271-117">(See the *Moving DbConfiguration* section if you want to change this.)</span></span>  
- <span data-ttu-id="34271-118">Übergeben Sie der dbconfiguration-Klasse einen öffentlichen Parameter losen Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="34271-118">Give your DbConfiguration class a public parameterless constructor.</span></span>  
- <span data-ttu-id="34271-119">Legen Sie Konfigurationsoptionen fest, indem Sie in diesem Konstruktor geschützte dbconfiguration-Methoden aufrufen.</span><span class="sxs-lookup"><span data-stu-id="34271-119">Set configuration options by calling protected DbConfiguration methods from within this constructor.</span></span>  

<span data-ttu-id="34271-120">Wenn Sie diese Richtlinien befolgen, können Sie Ihre Konfiguration automatisch von den Tools ermitteln und verwenden, die auf Ihr Modell zugreifen müssen, und wenn die Anwendung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="34271-120">Following these guidelines allows EF to discover and use your configuration automatically by both tooling that needs to access your model and when your application is run.</span></span>  

## <a name="example"></a><span data-ttu-id="34271-121">Beispiel</span><span class="sxs-lookup"><span data-stu-id="34271-121">Example</span></span>  

<span data-ttu-id="34271-122">Eine von dbconfiguration abgeleitete Klasse könnte wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="34271-122">A class derived from DbConfiguration might look like this:</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

namespace MyNamespace
{
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            SetDefaultConnectionFactory(new LocalDbConnectionFactory("mssqllocaldb"));
        }
    }
}
```  

<span data-ttu-id="34271-123">Diese Klasse richtet EF so ein, dass die SQL Azure Ausführungs Strategie verwendet wird, um fehlerhafte Daten Bank Vorgänge automatisch zu wiederholen, und um lokale DB für Datenbanken zu verwenden, die gemäß der Konvention aus Code First erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="34271-123">This class sets up EF to use the SQL Azure execution strategy - to automatically retry failed database operations - and to use Local DB for databases that are created by convention from Code First.</span></span>  

## <a name="moving-dbconfiguration"></a><span data-ttu-id="34271-124">Verschieben von dbconfiguration</span><span class="sxs-lookup"><span data-stu-id="34271-124">Moving DbConfiguration</span></span>  

<span data-ttu-id="34271-125">Es gibt Fälle, in denen es nicht möglich ist, die dbconfiguration-Klasse in derselben Assembly wie die dbcontext-Klasse zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="34271-125">There are cases where it is not possible to place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="34271-126">Beispielsweise können Sie über zwei dbcontext-Klassen verfügen, die jeweils in verschiedenen Assemblys enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="34271-126">For example, you may have two DbContext classes each in different assemblies.</span></span> <span data-ttu-id="34271-127">Hierfür gibt es zwei Möglichkeiten.</span><span class="sxs-lookup"><span data-stu-id="34271-127">There are two options for handling this.</span></span>  

<span data-ttu-id="34271-128">Die erste Option besteht darin, die Konfigurationsdatei zu verwenden, um die zu verwendende dbconfiguration-Instanz anzugeben.</span><span class="sxs-lookup"><span data-stu-id="34271-128">The first option is to use the config file to specify the DbConfiguration instance to use.</span></span> <span data-ttu-id="34271-129">Legen Sie zu diesem Zweck das codeconfigurationtype-Attribut des Abschnitts "EntityFramework" fest.</span><span class="sxs-lookup"><span data-stu-id="34271-129">To do this, set the codeConfigurationType attribute of the entityFramework section.</span></span> <span data-ttu-id="34271-130">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="34271-130">For example:</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

<span data-ttu-id="34271-131">Der Wert von codeconfigurationtype muss die Assembly und der Namespace qualifizierte Name der dbconfiguration-Klasse sein.</span><span class="sxs-lookup"><span data-stu-id="34271-131">The value of codeConfigurationType must be the assembly and namespace qualified name of your DbConfiguration class.</span></span>  

<span data-ttu-id="34271-132">Die zweite Option besteht darin, dbconfigurationtypeattribute für Ihre Kontext Klasse zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="34271-132">The second option is to place DbConfigurationTypeAttribute on your context class.</span></span> <span data-ttu-id="34271-133">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="34271-133">For example:</span></span>  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

<span data-ttu-id="34271-134">Der an das Attribut weiter gegebene Wert kann entweder Ihr dbconfiguration-Typ sein, wie oben gezeigt, oder die Zeichenfolge der qualifizierten Assembly-und Namespace-Typnamen.</span><span class="sxs-lookup"><span data-stu-id="34271-134">The value passed to the attribute can either be your DbConfiguration type - as shown above - or the assembly and namespace qualified type name string.</span></span> <span data-ttu-id="34271-135">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="34271-135">For example:</span></span>  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a><span data-ttu-id="34271-136">Explizites Festlegen von dbconfiguration</span><span class="sxs-lookup"><span data-stu-id="34271-136">Setting DbConfiguration explicitly</span></span>  

<span data-ttu-id="34271-137">Es gibt einige Situationen, in denen die Konfiguration möglicherweise erforderlich ist, bevor ein dbcontext-Typ verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="34271-137">There are some situations where configuration may be needed before any DbContext type has been used.</span></span> <span data-ttu-id="34271-138">Beispiele hierfür sind:</span><span class="sxs-lookup"><span data-stu-id="34271-138">Examples of this include:</span></span>  

- <span data-ttu-id="34271-139">Verwenden von dbmodelbuilder zum Erstellen eines Modells ohne Kontext</span><span class="sxs-lookup"><span data-stu-id="34271-139">Using DbModelBuilder to build a model without a context</span></span>  
- <span data-ttu-id="34271-140">Verwenden eines anderen Frameworks-/Utility-Codes, der einen dbcontext verwendet, in dem dieser Kontext verwendet wird, bevor der Anwendungskontext verwendet wird</span><span class="sxs-lookup"><span data-stu-id="34271-140">Using some other framework/utility code that utilizes a DbContext where that context is used before your application context is used</span></span>  

<span data-ttu-id="34271-141">In solchen Situationen kann EF die Konfiguration nicht automatisch ermitteln, und Sie müssen stattdessen eine der folgenden Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="34271-141">In such situations EF is unable to discover the configuration automatically and you must instead do one of the following:</span></span>  

- <span data-ttu-id="34271-142">Legen Sie den dbconfiguration-Typ in der Konfigurationsdatei fest, wie im Abschnitt *Verschieben von dbconfiguration* weiter oben beschrieben.</span><span class="sxs-lookup"><span data-stu-id="34271-142">Set the DbConfiguration type in the config file, as described in the *Moving DbConfiguration* section above</span></span>
- <span data-ttu-id="34271-143">Aufrufen der statischen dbconfiguration. SetConfiguration-Methode beim Starten der Anwendung</span><span class="sxs-lookup"><span data-stu-id="34271-143">Call the static DbConfiguration.SetConfiguration method during application startup</span></span>  

## <a name="overriding-dbconfiguration"></a><span data-ttu-id="34271-144">Überschreiben von dbconfiguration</span><span class="sxs-lookup"><span data-stu-id="34271-144">Overriding DbConfiguration</span></span>  

<span data-ttu-id="34271-145">Es gibt einige Situationen, in denen Sie den Konfigurationssatz in dbconfiguration überschreiben müssen.</span><span class="sxs-lookup"><span data-stu-id="34271-145">There are some situations where you need to override the configuration set in the DbConfiguration.</span></span> <span data-ttu-id="34271-146">Dies erfolgt in der Regel nicht von Anwendungsentwicklern, sondern von Drittanbietern und Plug-ins, die keine abgeleitete dbconfiguration-Klasse verwenden können.</span><span class="sxs-lookup"><span data-stu-id="34271-146">This is not typically done by application developers but rather by third party providers and plug-ins that cannot use a derived DbConfiguration class.</span></span>  

<span data-ttu-id="34271-147">Hierfür ermöglicht EntityFramework das Registrieren eines Ereignis Handlers, der die vorhandene Konfiguration ändern kann, kurz bevor Sie gesperrt wird.</span><span class="sxs-lookup"><span data-stu-id="34271-147">For this, EntityFramework allows an event handler to be registered that can modify existing configuration just before it is locked down.</span></span>  <span data-ttu-id="34271-148">Außerdem bietet es eine Sugar-Methode, mit der alle vom EF-Dienstlocator zurückgegebenen Dienste ersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="34271-148">It also provides a sugar method specifically for replacing any service returned by the EF service locator.</span></span> <span data-ttu-id="34271-149">So ist die Verwendung vorgesehen:</span><span class="sxs-lookup"><span data-stu-id="34271-149">This is how it is intended to be used:</span></span>  

- <span data-ttu-id="34271-150">Beim Starten der APP (vor der Verwendung von EF) sollte das Plug-in oder der Anbieter die Ereignishandlermethode für dieses Ereignis registrieren.</span><span class="sxs-lookup"><span data-stu-id="34271-150">At app startup (before EF is used) the plug-in or provider should register the event handler method for this event.</span></span> <span data-ttu-id="34271-151">(Beachten Sie, dass dies geschehen muss, bevor die Anwendung EF verwendet.)</span><span class="sxs-lookup"><span data-stu-id="34271-151">(Note that this must happen before the application uses EF.)</span></span>  
- <span data-ttu-id="34271-152">Der Ereignishandler ruft replaceservice für jeden Dienst auf, der ersetzt werden muss.</span><span class="sxs-lookup"><span data-stu-id="34271-152">The event handler makes a call to ReplaceService for every service that needs to be replaced.</span></span>  

<span data-ttu-id="34271-153">Wenn Sie z. b. idbconnectionfactory und dbproviderservice ersetzen möchten, registrieren Sie einen Handler in etwa wie folgt:</span><span class="sxs-lookup"><span data-stu-id="34271-153">For example, to replace IDbConnectionFactory and DbProviderService you would register a handler something like this:</span></span>  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

<span data-ttu-id="34271-154">Im obigen Code stellen myproviderservices und myconnectionfactory Ihre Implementierungen des Diensts dar.</span><span class="sxs-lookup"><span data-stu-id="34271-154">In the code above MyProviderServices and MyConnectionFactory represent your implementations of the service.</span></span>  

<span data-ttu-id="34271-155">Sie können auch zusätzliche Abhängigkeits Handler hinzufügen, um denselben Effekt zu erzielen.</span><span class="sxs-lookup"><span data-stu-id="34271-155">You can also add additional dependency handlers to get the same effect.</span></span>  

<span data-ttu-id="34271-156">Beachten Sie, dass Sie DbProviderFactory auch auf diese Weise einbinden können. Dies wirkt sich jedoch nur auf EF und nicht auf die Verwendung von DbProviderFactory außerhalb von EF aus.</span><span class="sxs-lookup"><span data-stu-id="34271-156">Note that you could also wrap DbProviderFactory in this way, but doing so will only affect EF and not uses of the DbProviderFactory outside of EF.</span></span> <span data-ttu-id="34271-157">Aus diesem Grund ist es wahrscheinlich, dass Sie DbProviderFactory weiterhin wie zuvor verpacken.</span><span class="sxs-lookup"><span data-stu-id="34271-157">For this reason you’ll probably want to continue to wrap DbProviderFactory as you have before.</span></span>  

<span data-ttu-id="34271-158">Berücksichtigen Sie auch die Dienste, die Sie extern für Ihre Anwendung ausführen, z. b. bei der Ausführung von Migrationen über die Paket-Manager-Konsole.</span><span class="sxs-lookup"><span data-stu-id="34271-158">You should also keep in mind the services that you run externally to your application - for example, when running migrations from the Package Manager Console.</span></span> <span data-ttu-id="34271-159">Wenn Sie Migrieren von der-Konsole ausführen, wird versucht, die dbconfiguration zu finden.</span><span class="sxs-lookup"><span data-stu-id="34271-159">When you run migrate from the console it will attempt to find your DbConfiguration.</span></span> <span data-ttu-id="34271-160">Ob der umschließende Dienst erhalten bleibt, hängt jedoch davon ab, wo der Ereignishandler registriert ist.</span><span class="sxs-lookup"><span data-stu-id="34271-160">However, whether or not it will get the wrapped service depends on where the event handler it registered.</span></span> <span data-ttu-id="34271-161">Wenn Sie im Rahmen der Erstellung von dbconfiguration registriert ist, sollte der Code ausgeführt werden, und der Dienst sollte umschließt werden.</span><span class="sxs-lookup"><span data-stu-id="34271-161">If it is registered as part of the construction of your DbConfiguration then the code should execute and the service should get wrapped.</span></span> <span data-ttu-id="34271-162">Normalerweise ist dies nicht der Fall, und das bedeutet, dass Tools den umschließenden Dienst nicht erhalten.</span><span class="sxs-lookup"><span data-stu-id="34271-162">Usually this won’t be the case and this means that tooling won’t get the wrapped service.</span></span>  
