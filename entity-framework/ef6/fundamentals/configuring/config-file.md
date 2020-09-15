---
title: Konfigurationsdatei Einstellungen-EF6
description: Konfigurationsdatei Einstellungen in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/config-file
ms.openlocfilehash: b407ae7d64df18f6a8d80aa13703a8c243c6f3a2
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070717"
---
# <a name="configuration-file-settings"></a><span data-ttu-id="09a32-103">Konfigurationsdatei Einstellungen</span><span class="sxs-lookup"><span data-stu-id="09a32-103">Configuration File Settings</span></span>
<span data-ttu-id="09a32-104">Entity Framework ermöglicht, dass eine Reihe von Einstellungen in der Konfigurationsdatei angegeben werden können.</span><span class="sxs-lookup"><span data-stu-id="09a32-104">Entity Framework allows a number of settings to be specified from the configuration file.</span></span> <span data-ttu-id="09a32-105">Im allgemeinen folgt EF einem Prinzip der Konvention für die Konfiguration: alle in diesem Beitrag behandelten Einstellungen haben ein Standardverhalten. Sie müssen sich lediglich um das Ändern der Einstellung kümmern, wenn die Standardeinstellung Ihre Anforderungen nicht mehr erfüllt.</span><span class="sxs-lookup"><span data-stu-id="09a32-105">In general EF follows a ‘convention over configuration’ principle: all the settings discussed in this post have a default behavior, you only need to worry about changing the setting when the default no longer satisfies your requirements.</span></span>  

## <a name="a-code-based-alternative"></a><span data-ttu-id="09a32-106">Eine Code basierte Alternative</span><span class="sxs-lookup"><span data-stu-id="09a32-106">A Code-Based Alternative</span></span>  

<span data-ttu-id="09a32-107">Alle diese Einstellungen können auch mithilfe von Code angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="09a32-107">All of these settings can also be applied using code.</span></span> <span data-ttu-id="09a32-108">Ab EF6 haben wir eine [Code basierte Konfiguration](xref:ef6/fundamentals/configuring/code-based)eingeführt, die eine zentrale Methode zum Anwenden der Konfiguration aus dem Code bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="09a32-108">Starting in EF6 we introduced [code-based configuration](xref:ef6/fundamentals/configuring/code-based), which provides a central way of applying configuration from code.</span></span> <span data-ttu-id="09a32-109">Vor EF6 kann die Konfiguration weiterhin aus dem Code übernommen werden, Sie müssen jedoch verschiedene APIs verwenden, um verschiedene Bereiche zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="09a32-109">Prior to EF6, configuration can still be applied from code but you need to use various APIs to configure different areas.</span></span> <span data-ttu-id="09a32-110">Mithilfe der Option Konfigurationsdatei können diese Einstellungen während der Bereitstellung problemlos geändert werden, ohne den Code zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="09a32-110">The configuration file option allows these settings to be easily changed during deployment without updating your code.</span></span>

## <a name="the-entity-framework-configuration-section"></a><span data-ttu-id="09a32-111">Der Abschnitt zur Entity Framework Konfiguration</span><span class="sxs-lookup"><span data-stu-id="09a32-111">The Entity Framework Configuration Section</span></span>  

<span data-ttu-id="09a32-112">Ab EF 4.1 können Sie den datenbankinitialisierer für einen Kontext mithilfe des Abschnitts **appSettings** der Konfigurationsdatei festlegen.</span><span class="sxs-lookup"><span data-stu-id="09a32-112">Starting with EF4.1 you could set the database initializer for a context using the **appSettings** section of the configuration file.</span></span> <span data-ttu-id="09a32-113">In EF 4,3 haben wir den benutzerdefinierten Abschnitt " **EntityFramework** " eingeführt, mit dem die neuen Einstellungen behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="09a32-113">In EF 4.3 we introduced the custom **entityFramework** section to handle the new settings.</span></span> <span data-ttu-id="09a32-114">Entity Framework erkennt weiterhin datenbankinitialisierer, die im alten Format festgelegt sind. es wird jedoch empfohlen, nach Möglichkeit in das neue Format zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="09a32-114">Entity Framework will still recognize database initializers set using the old format, but we recommend moving to the new format where possible.</span></span>

<span data-ttu-id="09a32-115">Wenn Sie das nuget-Paket "EntityFramework" installiert haben, wurde der Abschnitt " **EntityFramework** " automatisch zur Konfigurationsdatei Ihres Projekts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="09a32-115">The **entityFramework** section was automatically added to the configuration file of your project when you installed the EntityFramework NuGet package.</span></span>  

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections>
    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
    <section name="entityFramework"
       type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=4.3.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
  </configSections>
</configuration>
```  

## <a name="connection-strings"></a><span data-ttu-id="09a32-116">Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="09a32-116">Connection Strings</span></span>  

<span data-ttu-id="09a32-117">Auf dieser Seite finden Sie weitere Details dazu, wie Entity Framework die zu [verwendende](xref:ef6/fundamentals/configuring/connection-strings) Datenbank bestimmt, einschließlich der Verbindungs Zeichenfolgen in der Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="09a32-117">[This page](xref:ef6/fundamentals/configuring/connection-strings) provides more details on how Entity Framework determines the database to be used, including connection strings in the configuration file.</span></span>  

<span data-ttu-id="09a32-118">Verbindungs Zeichenfolgen werden im standardmäßigen **connectionStrings** -Element angezeigt und benötigen nicht den Abschnitt " **EntityFramework** ".</span><span class="sxs-lookup"><span data-stu-id="09a32-118">Connection strings go in the standard **connectionStrings** element and do not require the **entityFramework** section.</span></span>  

<span data-ttu-id="09a32-119">Code First basierten Modellen verwenden normale ADO.NET-Verbindungs Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="09a32-119">Code First based models use normal ADO.NET connection strings.</span></span> <span data-ttu-id="09a32-120">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="09a32-120">For example:</span></span>  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

<span data-ttu-id="09a32-121">EF-Designer-basierte Modelle verwenden spezielle EF-Verbindungs Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="09a32-121">EF Designer based models use special EF connection strings.</span></span> <span data-ttu-id="09a32-122">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="09a32-122">For example:</span></span>  

``` xml  
<connectionStrings>
  <add name="BlogContext"  
    connectionString=
      "metadata=
        res://*/BloggingModel.csdl|
        res://*/BloggingModel.ssdl|
        res://*/BloggingModel.msl;
      provider=System.Data.SqlClient;
      provider connection string=
        &quot;data source=(localdb)\mssqllocaldb;
        initial catalog=Blogging;
        integrated security=True;
        multipleactiveresultsets=True;&quot;"
     providerName="System.Data.EntityClient" />
</connectionStrings>
```

## <a name="code-based-configuration-type-ef6-onwards"></a><span data-ttu-id="09a32-123">Code basierter Konfigurationstyp (EF6 oder höher)</span><span class="sxs-lookup"><span data-stu-id="09a32-123">Code-Based Configuration Type (EF6 Onwards)</span></span>  

<span data-ttu-id="09a32-124">Ab EF6 können Sie die dbconfiguration für EF angeben, die für die [Code basierte Konfiguration](xref:ef6/fundamentals/configuring/code-based) in der Anwendung verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="09a32-124">Starting with EF6, you can specify the DbConfiguration for EF to use for [code-based configuration](xref:ef6/fundamentals/configuring/code-based) in your application.</span></span> <span data-ttu-id="09a32-125">In den meisten Fällen müssen Sie diese Einstellung nicht angeben, da EF ihre dbconfiguration automatisch erkennt.</span><span class="sxs-lookup"><span data-stu-id="09a32-125">In most cases you don't need to specify this setting as EF will automatically discover your DbConfiguration.</span></span> <span data-ttu-id="09a32-126">Ausführliche Informationen dazu, wann Sie dbconfiguration in der Konfigurationsdatei angeben müssen, finden Sie im Abschnitt **Verschieben von dbconfiguration** der [Code basierten Konfiguration](xref:ef6/fundamentals/configuring/code-based).</span><span class="sxs-lookup"><span data-stu-id="09a32-126">For details of when you may need to specify DbConfiguration in your config file see the **Moving DbConfiguration** section of [Code-Based Configuration](xref:ef6/fundamentals/configuring/code-based).</span></span>  

<span data-ttu-id="09a32-127">Um einen dbconfiguration-Typ festzulegen, geben Sie den qualifizierten Assemblynamen im **codeconfigurationtype** -Element an.</span><span class="sxs-lookup"><span data-stu-id="09a32-127">To set a DbConfiguration type, you specify the assembly qualified type name in the **codeConfigurationType** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="09a32-128">Ein qualifizierter Assemblyname ist der qualifizierte Namespace Name, gefolgt von einem Komma, und dann die Assembly, in der sich der Typ befindet.</span><span class="sxs-lookup"><span data-stu-id="09a32-128">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="09a32-129">Optional können Sie auch die Assemblyversion, die Kultur und das öffentliche Schlüssel Token angeben.</span><span class="sxs-lookup"><span data-stu-id="09a32-129">You can optionally also specify the assembly version, culture and public key token.</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a><span data-ttu-id="09a32-130">EF-Datenbankanbieter (EF6 oder höher)</span><span class="sxs-lookup"><span data-stu-id="09a32-130">EF Database Providers (EF6 Onwards)</span></span>  

<span data-ttu-id="09a32-131">Vor EF6 mussten Entity Framework spezifische Teile eines Datenbankanbieters als Teil des Core ADO.NET-Anbieters eingeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="09a32-131">Prior to EF6, Entity Framework-specific parts of a database provider had to be included as part of the core ADO.NET provider.</span></span> <span data-ttu-id="09a32-132">Ab EF6 werden die EF-spezifischen Teile nun separat verwaltet und registriert.</span><span class="sxs-lookup"><span data-stu-id="09a32-132">Starting with EF6, the EF specific parts are now managed and registered separately.</span></span>  

<span data-ttu-id="09a32-133">Normalerweise müssen Sie Anbieter nicht selbst registrieren.</span><span class="sxs-lookup"><span data-stu-id="09a32-133">Normally you won't need to register providers yourself.</span></span> <span data-ttu-id="09a32-134">Dies wird in der Regel vom Anbieter durchgeführt, wenn Sie ihn installieren.</span><span class="sxs-lookup"><span data-stu-id="09a32-134">This will typically be done by the provider when you install it.</span></span>  

<span data-ttu-id="09a32-135">Anbieter werden durch Einschließen eines **Anbieter** -Elements unter dem untergeordneten Abschnitt " **Providers** " im Abschnitt " **EntityFramework** " registriert.</span><span class="sxs-lookup"><span data-stu-id="09a32-135">Providers are registered by including a **provider** element under the **providers** child section of the **entityFramework** section.</span></span> <span data-ttu-id="09a32-136">Es gibt zwei erforderliche Attribute für einen Anbieter Eintrag:</span><span class="sxs-lookup"><span data-stu-id="09a32-136">There are two required attributes for a provider entry:</span></span>  

- <span data-ttu-id="09a32-137">**InvariantName** identifiziert den Kern ADO.NET-Anbieter, den dieser EF-Anbieter als Ziel hat.</span><span class="sxs-lookup"><span data-stu-id="09a32-137">**invariantName** identifies the core ADO.NET provider that this EF provider targets</span></span>  
- <span data-ttu-id="09a32-138">**Type** ist der durch die Assembly qualifizierte Typname der EF-Anbieter Implementierung.</span><span class="sxs-lookup"><span data-stu-id="09a32-138">**type** is the assembly qualified type name of the EF provider implementation</span></span>  

> [!NOTE]
> <span data-ttu-id="09a32-139">Ein qualifizierter Assemblyname ist der qualifizierte Namespace Name, gefolgt von einem Komma, und dann die Assembly, in der sich der Typ befindet.</span><span class="sxs-lookup"><span data-stu-id="09a32-139">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="09a32-140">Optional können Sie auch die Assemblyversion, die Kultur und das öffentliche Schlüssel Token angeben.</span><span class="sxs-lookup"><span data-stu-id="09a32-140">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="09a32-141">Ein Beispiel hierfür ist der Eintrag, der erstellt wird, um beim Installieren von Entity Framework den Standard SQL Server-Anbieter zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="09a32-141">As an example here is the entry created to register the default SQL Server provider when you install Entity Framework.</span></span>  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a><span data-ttu-id="09a32-142">Interceptors (ab EF 6.1)</span><span class="sxs-lookup"><span data-stu-id="09a32-142">Interceptors (EF6.1 Onwards)</span></span>  

<span data-ttu-id="09a32-143">Ab EF 6.1 können Sie Interceptors in der Konfigurationsdatei registrieren.</span><span class="sxs-lookup"><span data-stu-id="09a32-143">Starting with EF6.1 you can register interceptors in the configuration file.</span></span> <span data-ttu-id="09a32-144">Interceptors ermöglichen es Ihnen, zusätzliche Logik auszuführen, wenn EF bestimmte Vorgänge ausführt, z. b. das Ausführen von Datenbankabfragen, das Öffnen von Verbindungen usw.</span><span class="sxs-lookup"><span data-stu-id="09a32-144">Interceptors allow you to run additional logic when EF performs certain operations, such as executing database queries, opening connections, etc.</span></span>  

<span data-ttu-id="09a32-145">Interceptors werden durch Einschließen eines **Interceptor** Elements unter dem untergeordneten Abschnitt **Interceptors** des Abschnitts **EntityFramework** registriert.</span><span class="sxs-lookup"><span data-stu-id="09a32-145">Interceptors are registered by including an **interceptor** element under the **interceptors** child section of the **entityFramework** section.</span></span> <span data-ttu-id="09a32-146">Mit der folgenden Konfiguration wird z. b. der integrierte **databaselogger** -Interceptor registriert, der alle Daten Bank Vorgänge in der Konsole protokolliert.</span><span class="sxs-lookup"><span data-stu-id="09a32-146">For example, the following configuration registers the built-in **DatabaseLogger** interceptor that will log all database operations to the Console.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a><span data-ttu-id="09a32-147">Protokollieren von Daten Bank Vorgängen in einer Datei (ab EF 6.1)</span><span class="sxs-lookup"><span data-stu-id="09a32-147">Logging Database Operations to a File (EF6.1 Onwards)</span></span>  

<span data-ttu-id="09a32-148">Das Registrieren von Interceptors über die Konfigurationsdatei ist besonders nützlich, wenn Sie eine Protokollierung zu einer vorhandenen Anwendung hinzufügen möchten, um ein Problem zu beheben.</span><span class="sxs-lookup"><span data-stu-id="09a32-148">Registering interceptors via the config file is especially useful when you want to add logging to an existing application to help debug an issue.</span></span> <span data-ttu-id="09a32-149">**Databaselogger** unterstützt die Protokollierung in einer Datei, indem der Dateiname als Konstruktorparameter angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="09a32-149">**DatabaseLogger** supports logging to a file by supplying the file name as a constructor parameter.</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="09a32-150">Standardmäßig bewirkt dies, dass die Protokolldatei bei jedem Start der APP mit einer neuen Datei überschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="09a32-150">By default this will cause the log file to be overwritten with a new file each time the app starts.</span></span> <span data-ttu-id="09a32-151">Wenn Sie stattdessen an die Protokolldatei anfügen möchten, wenn Sie bereits vorhanden ist, verwenden Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="09a32-151">To instead append to the log file if it already exists use something like:</span></span>  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
      <parameter value="true" type="System.Boolean"/>
    </parameters>
  </interceptor>
</interceptors>
```  

<span data-ttu-id="09a32-152">Weitere Informationen zu **databaselogger** und zum Registrieren von Interceptors finden Sie im Blogbeitrag [EF 6,1: Aktivieren der Protokollierung ohne](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/)erneutes Kompilieren.</span><span class="sxs-lookup"><span data-stu-id="09a32-152">For additional information on **DatabaseLogger** and registering interceptors, see the blog post [EF 6.1: Turning on logging without recompiling](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/).</span></span>  

## <a name="code-first-default-connection-factory"></a><span data-ttu-id="09a32-153">Standardverbindungsfactory Code First</span><span class="sxs-lookup"><span data-stu-id="09a32-153">Code First Default Connection Factory</span></span>  

<span data-ttu-id="09a32-154">Im Konfigurations Abschnitt können Sie eine standardverbindungsfactory angeben, die Code First verwenden soll, um eine Datenbank zu suchen, die für einen Kontext verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="09a32-154">The configuration section allows you to specify a default connection factory that Code First should use to locate a database to use for a context.</span></span> <span data-ttu-id="09a32-155">Die standardverbindungsfactory wird nur verwendet, wenn der Konfigurationsdatei für einen Kontext keine Verbindungs Zeichenfolge hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="09a32-155">The default connection factory is only used when no connection string has been added to the configuration file for a context.</span></span>  

<span data-ttu-id="09a32-156">Wenn Sie das EF-nuget-Paket installiert haben, wurde eine standardverbindungsfactory registriert, die auf SQL Express oder localdb zeigt, je nachdem, welche installiert wurde.</span><span class="sxs-lookup"><span data-stu-id="09a32-156">When you installed the EF NuGet package a default connection factory was registered that points to either SQL Express or LocalDB, depending on which one you have installed.</span></span>  

<span data-ttu-id="09a32-157">Um eine Verbindungsfactory festzulegen, geben Sie den qualifizierten Assemblynamen im **defaultconnectionfactory** -Element an.</span><span class="sxs-lookup"><span data-stu-id="09a32-157">To set a connection factory, you specify the assembly qualified type name in the **defaultConnectionFactory** element.</span></span>  

> [!NOTE]
> <span data-ttu-id="09a32-158">Ein qualifizierter Assemblyname ist der qualifizierte Namespace Name, gefolgt von einem Komma, und dann die Assembly, in der sich der Typ befindet.</span><span class="sxs-lookup"><span data-stu-id="09a32-158">An assembly qualified name is the namespace qualified name, followed by a comma, then the assembly that the type resides in.</span></span> <span data-ttu-id="09a32-159">Optional können Sie auch die Assemblyversion, die Kultur und das öffentliche Schlüssel Token angeben.</span><span class="sxs-lookup"><span data-stu-id="09a32-159">You can optionally also specify the assembly version, culture and public key token.</span></span>  

<span data-ttu-id="09a32-160">Hier ist ein Beispiel für das Festlegen einer eigenen standardverbindungsfactory:</span><span class="sxs-lookup"><span data-stu-id="09a32-160">Here is an example of setting your own default connection factory:</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

<span data-ttu-id="09a32-161">Im obigen Beispiel ist es erforderlich, dass die benutzerdefinierte Factory einen Parameter losen Konstruktor hat.</span><span class="sxs-lookup"><span data-stu-id="09a32-161">The above example requires the custom factory to have a parameterless constructor.</span></span> <span data-ttu-id="09a32-162">Bei Bedarf können Sie mit dem **Parameters** -Element Konstruktorparameter angeben.</span><span class="sxs-lookup"><span data-stu-id="09a32-162">If needed, you can specify constructor parameters using the **parameters** element.</span></span>  

<span data-ttu-id="09a32-163">Beispielsweise müssen Sie für das sqlceconnectionfactory-Element, das in Entity Framework enthalten ist, den invarianten Namen eines Anbieters für den Konstruktor angeben.</span><span class="sxs-lookup"><span data-stu-id="09a32-163">For example, the SqlCeConnectionFactory, that is included in Entity Framework, requires you to supply a provider invariant name to the constructor.</span></span> <span data-ttu-id="09a32-164">Der invariante Name des Anbieters identifiziert die Version von SQL Compact, die Sie verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="09a32-164">The provider invariant name identifies the version of SQL Compact you want to use.</span></span> <span data-ttu-id="09a32-165">Die folgende Konfiguration bewirkt, dass Kontexte standardmäßig die SQL Compact-Version 4,0 verwenden.</span><span class="sxs-lookup"><span data-stu-id="09a32-165">The following configuration will cause contexts to use SQL Compact version 4.0 by default.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="09a32-166">Wenn Sie keine standardverbindungsfactory festlegen, verwendet Code First die sqlconnectionfactory, die auf verweist `.\SQLEXPRESS` .</span><span class="sxs-lookup"><span data-stu-id="09a32-166">If you don’t set a default connection factory, Code First uses the SqlConnectionFactory, pointing to `.\SQLEXPRESS`.</span></span> <span data-ttu-id="09a32-167">Sqlconnectionfactory verfügt auch über einen Konstruktor, mit dem Sie Teile der Verbindungs Zeichenfolge überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="09a32-167">SqlConnectionFactory also has a constructor that allows you to override parts of the connection string.</span></span> <span data-ttu-id="09a32-168">Wenn Sie eine andere SQL Server-Instanz als verwenden möchten `.\SQLEXPRESS` , können Sie diesen Konstruktor verwenden, um den Server festzulegen.</span><span class="sxs-lookup"><span data-stu-id="09a32-168">If you want to use a SQL Server instance other than `.\SQLEXPRESS` you can use this constructor to set the server.</span></span>  

<span data-ttu-id="09a32-169">Die folgende Konfiguration bewirkt, dass Code First **mydatabaseserver** für Kontexte verwendet, für die keine explizite Verbindungs Zeichenfolge festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="09a32-169">The following configuration will cause Code First to use **MyDatabaseServer** for contexts that don’t have an explicit connection string set.</span></span>  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

<span data-ttu-id="09a32-170">Standardmäßig wird davon ausgegangen, dass Konstruktorargumente den Typ "String" haben.</span><span class="sxs-lookup"><span data-stu-id="09a32-170">By default, it’s assumed that constructor arguments are of type string.</span></span> <span data-ttu-id="09a32-171">Sie können das Type-Attribut verwenden, um dies zu ändern.</span><span class="sxs-lookup"><span data-stu-id="09a32-171">You can use the type attribute to change this.</span></span>  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a><span data-ttu-id="09a32-172">Datenbankinitialisierer</span><span class="sxs-lookup"><span data-stu-id="09a32-172">Database Initializers</span></span>  

<span data-ttu-id="09a32-173">Datenbankinitialisierer werden pro Kontext konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="09a32-173">Database initializers are configured on a per-context basis.</span></span> <span data-ttu-id="09a32-174">Sie können mithilfe des **context** -Elements in der Konfigurationsdatei festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="09a32-174">They can be set in the configuration file using the **context** element.</span></span> <span data-ttu-id="09a32-175">Dieses Element verwendet den qualifizierten Assemblynamen, um den Kontext zu identifizieren, der konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="09a32-175">This element uses the assembly qualified name to identify the context being configured.</span></span>  

<span data-ttu-id="09a32-176">Standardmäßig sind Code First Kontexte so konfiguriert, dass Sie den Initialisierer "Initialisierer" von "kreatedatabaseifnotexists" verwenden</span><span class="sxs-lookup"><span data-stu-id="09a32-176">By default, Code First contexts are configured to use the CreateDatabaseIfNotExists initializer.</span></span> <span data-ttu-id="09a32-177">Es gibt ein **disabledatabaseinitialization** -Attribut für das **Kontext** Element, das zum Deaktivieren der Daten Bank Initialisierung verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="09a32-177">There is a **disableDatabaseInitialization** attribute on the **context** element that can be used to disable database initialization.</span></span>  

<span data-ttu-id="09a32-178">Die folgende Konfiguration deaktiviert beispielsweise die Daten Bank Initialisierung für den in MyAssembly.dll definierten Blog Kontext Kontext.</span><span class="sxs-lookup"><span data-stu-id="09a32-178">For example, the following configuration disables database initialization for the Blogging.BlogContext context defined in MyAssembly.dll.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

<span data-ttu-id="09a32-179">Sie können das **databaseinitializer** -Element verwenden, um einen benutzerdefinierten Initialisierer festzulegen.</span><span class="sxs-lookup"><span data-stu-id="09a32-179">You can use the **databaseInitializer** element to set a custom initializer.</span></span>  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

<span data-ttu-id="09a32-180">Konstruktorparameter verwenden dieselbe Syntax wie standardverbindungsfactorys.</span><span class="sxs-lookup"><span data-stu-id="09a32-180">Constructor parameters use the same syntax as default connection factories.</span></span>  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly">
      <parameters>
        <parameter value="MyConstructorParameter" />
      </parameters>
    </databaseInitializer>
  </context>
</contexts>
```  

<span data-ttu-id="09a32-181">Sie können einen der generischen datenbankinitialisierer konfigurieren, die in Entity Framework enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="09a32-181">You can configure one of the generic database initializers that are included in Entity Framework.</span></span> <span data-ttu-id="09a32-182">Das **Type** -Attribut verwendet das .NET Framework-Format für generische Typen.</span><span class="sxs-lookup"><span data-stu-id="09a32-182">The **type** attribute uses the .NET Framework format for generic types.</span></span>  

<span data-ttu-id="09a32-183">Wenn Sie z. b. Code First-Migrationen verwenden, können Sie die Datenbank so konfigurieren, dass Sie automatisch mit dem `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` Initialisierer migriert wird.</span><span class="sxs-lookup"><span data-stu-id="09a32-183">For example, if you are using Code First Migrations, you can configure the database to be migrated automatically using the `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` initializer.</span></span>  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
