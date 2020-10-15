---
title: Abhängigkeitsauflösung-EF6
description: Abhängigkeitsauflösung in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/dependency-resolution
ms.openlocfilehash: 84f2391cdad974bb14aecd7e8650d46e78e47b75
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063308"
---
# <a name="dependency-resolution"></a><span data-ttu-id="c8e83-103">Abhängigkeitsauflösung</span><span class="sxs-lookup"><span data-stu-id="c8e83-103">Dependency resolution</span></span>
> [!NOTE]
> <span data-ttu-id="c8e83-104">**Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="c8e83-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="c8e83-105">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="c8e83-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="c8e83-106">Ab EF6 enthält Entity Framework einen allgemeinen Mechanismus zum Abrufen von erforderlichen Implementierungen von Diensten.</span><span class="sxs-lookup"><span data-stu-id="c8e83-106">Starting with EF6, Entity Framework contains a general-purpose mechanism for obtaining implementations of services that it requires.</span></span> <span data-ttu-id="c8e83-107">Das heißt, wenn EF eine Instanz einiger Schnittstellen oder Basisklassen verwendet, wird eine konkrete Implementierung der-Schnittstelle oder der Basisklasse angefordert, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="c8e83-107">That is, when EF uses an instance of some interfaces or base classes it will ask for a concrete implementation of the interface or base class to use.</span></span> <span data-ttu-id="c8e83-108">Dies wird durch die Verwendung der idbdependencyresolver-Schnittstelle erreicht:</span><span class="sxs-lookup"><span data-stu-id="c8e83-108">This is achieved through use of the IDbDependencyResolver interface:</span></span>  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

<span data-ttu-id="c8e83-109">Die GetService-Methode wird in der Regel von EF aufgerufen und wird von einer Implementierung von idbdependencyresolver behandelt, die entweder von EF oder der Anwendung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c8e83-109">The GetService method is typically called by EF and is handled by an implementation of IDbDependencyResolver provided either by EF or by the application.</span></span> <span data-ttu-id="c8e83-110">Beim Aufruf ist das Typargument die Schnittstelle oder der Basis Klassentyp des angeforderten Dienstanbieter, das Schlüsselobjekt ist entweder NULL oder ein Objekt, das Kontextinformationen zum angeforderten Dienst bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="c8e83-110">When called, the type argument is the interface or base class type of the service being requested, and the key object is either null or an object providing contextual information about the requested service.</span></span>  

<span data-ttu-id="c8e83-111">Sofern nicht anders angegeben, muss jedes zurückgegebene Objekt Thread sicher sein, da es als Singleton verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c8e83-111">Unless otherwise stated any object returned must be thread-safe since it can be used as a singleton.</span></span> <span data-ttu-id="c8e83-112">In vielen Fällen ist das zurückgegebene Objekt eine Factory. in diesem Fall muss die Factory selbst Thread sicher sein, aber das von der Factory zurückgegebene Objekt muss nicht Thread sicher sein, da eine neue Instanz für jede Verwendung von der Factory angefordert wird.</span><span class="sxs-lookup"><span data-stu-id="c8e83-112">In many cases the object returned is a factory in which case the factory itself must be thread-safe but the object returned from the factory does not need to be thread-safe since a new instance is requested from the factory for each use.</span></span>

<span data-ttu-id="c8e83-113">Dieser Artikel enthält keine vollständigen Details zur Implementierung von idbdependencyresolver, sondern fungiert als Referenz für die Dienst Typen (d. h. die Schnittstelle und Basisklassen Typen), für die EF GetService aufruft, und die Semantik des Schlüssel Objekts für jeden dieser Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="c8e83-113">This article does not contain full details on how to implement IDbDependencyResolver, but instead acts as a reference for the service types (that is, the interface and base class types) for which EF calls GetService and the semantics of the key object for each of these calls.</span></span>

## <a name="systemdataentityidatabaseinitializertcontext"></a><span data-ttu-id="c8e83-114">System. Data. Entity. idatabaseinitializer<tcontext\></span><span class="sxs-lookup"><span data-stu-id="c8e83-114">System.Data.Entity.IDatabaseInitializer<TContext\></span></span>  

<span data-ttu-id="c8e83-115">**Eingeführte Version**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-115">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="c8e83-116">**Zurück**gegebenes Objekt: ein datenbankinitialisierer für den angegebenen Kontexttyp.</span><span class="sxs-lookup"><span data-stu-id="c8e83-116">**Object returned**: A database initializer for the given context type</span></span>  

<span data-ttu-id="c8e83-117">**Schlüssel**: nicht verwendet; wird NULL sein.</span><span class="sxs-lookup"><span data-stu-id="c8e83-117">**Key**: Not used; will be null</span></span>  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a><span data-ttu-id="c8e83-118">Func<System. Data. Entity. Migration. SQL. migrationsqlgenerator\></span><span class="sxs-lookup"><span data-stu-id="c8e83-118">Func<System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\></span></span>  

<span data-ttu-id="c8e83-119">**Eingeführte Version**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-119">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="c8e83-120">**Zurück**gegebenes Objekt: eine Factory zum Erstellen eines SQL-Generators, der für Migrationen und andere Aktionen verwendet werden kann, die eine Datenbank erstellen, z. b. die Erstellung von Datenbanken mit datenbankinitialisierern.</span><span class="sxs-lookup"><span data-stu-id="c8e83-120">**Object returned**: A factory to create a SQL generator that can be used for Migrations and other actions that cause a database to be created, such as database creation with database initializers.</span></span>  

<span data-ttu-id="c8e83-121">**Key**: eine Zeichenfolge, die den invarianten Namen des ADO.NET-Anbieters enthält, der den Typ der Datenbank angibt, für die SQL generiert wird.</span><span class="sxs-lookup"><span data-stu-id="c8e83-121">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which SQL will be generated.</span></span> <span data-ttu-id="c8e83-122">Beispielsweise wird der SQL Server SQL-Generator für den Schlüssel "System. Data. SqlClient" zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c8e83-122">For example, the SQL Server SQL generator is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="c8e83-123">Weitere Informationen zu Anbieter bezogenen Diensten in EF6 finden Sie im Abschnitt [EF6 Provider Model](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="c8e83-123">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentitycorecommondbproviderservices"></a><span data-ttu-id="c8e83-124">System. Data. Entity. Core. Common. DbProviderServices</span><span class="sxs-lookup"><span data-stu-id="c8e83-124">System.Data.Entity.Core.Common.DbProviderServices</span></span>  

<span data-ttu-id="c8e83-125">**Eingeführte Version**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-125">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="c8e83-126">**Zurück**gegebenes Objekt: der EF-Anbieter, der für den invarianten Namen eines angegebenen Anbieters verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c8e83-126">**Object returned**: The EF provider to use for a given provider invariant name</span></span>  

<span data-ttu-id="c8e83-127">**Key**: eine Zeichenfolge, die den invarianten Namen des ADO.NET-Anbieters enthält, der den Typ der Datenbank angibt, für die ein Anbieter benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="c8e83-127">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which a provider is needed.</span></span> <span data-ttu-id="c8e83-128">Beispielsweise wird der SQL Server Anbieter für den Schlüssel "System. Data. SqlClient" zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c8e83-128">For example, the SQL Server provider is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="c8e83-129">Weitere Informationen zu Anbieter bezogenen Diensten in EF6 finden Sie im Abschnitt [EF6 Provider Model](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="c8e83-129">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a><span data-ttu-id="c8e83-130">System. Data. Entity. Infrastructure. idbconnectionfactory</span><span class="sxs-lookup"><span data-stu-id="c8e83-130">System.Data.Entity.Infrastructure.IDbConnectionFactory</span></span>  

<span data-ttu-id="c8e83-131">**Eingeführte Version**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-131">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="c8e83-132">**Zurück**gegebenes Objekt: die Verbindungsfactory, die verwendet wird, wenn EF eine Datenbankverbindung gemäß der Konvention erstellt.</span><span class="sxs-lookup"><span data-stu-id="c8e83-132">**Object returned**: The connection factory that will be used when EF creates a database connection by convention.</span></span> <span data-ttu-id="c8e83-133">Das heißt, wenn EF keine Verbindung oder Verbindungs Zeichenfolge erhält und keine Verbindungs Zeichenfolge in der app.config oder web.config gefunden werden kann, wird dieser Dienst zum Erstellen einer Verbindung gemäß der Konvention verwendet.</span><span class="sxs-lookup"><span data-stu-id="c8e83-133">That is, when no connection or connection string is given to EF, and no connection string can be found in the app.config or web.config, then this service is used to create a connection by convention.</span></span> <span data-ttu-id="c8e83-134">Wenn Sie die Verbindungsfactory ändern, kann EF standardmäßig einen anderen Typ von Datenbank (z. b. SQL Server Compact Edition) verwenden.</span><span class="sxs-lookup"><span data-stu-id="c8e83-134">Changing the connection factory can allow EF to use a different type of database (for example, SQL Server Compact Edition) by default.</span></span>  

<span data-ttu-id="c8e83-135">**Schlüssel**: nicht verwendet; wird NULL sein.</span><span class="sxs-lookup"><span data-stu-id="c8e83-135">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="c8e83-136">Weitere Informationen zu Anbieter bezogenen Diensten in EF6 finden Sie im Abschnitt [EF6 Provider Model](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="c8e83-136">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a><span data-ttu-id="c8e83-137">System. Data. Entity. Infrastructure. IManifestTokenService</span><span class="sxs-lookup"><span data-stu-id="c8e83-137">System.Data.Entity.Infrastructure.IManifestTokenService</span></span>  

<span data-ttu-id="c8e83-138">**Eingeführte Version**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-138">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="c8e83-139">**Zurück**gegebenes Objekt: ein Dienst, der ein Anbieter Manifest-Token aus einer Verbindung generieren kann.</span><span class="sxs-lookup"><span data-stu-id="c8e83-139">**Object returned**: A service that can generate a provider manifest token from a connection.</span></span> <span data-ttu-id="c8e83-140">Dieser Dienst wird in der Regel auf zweierlei Weise verwendet.</span><span class="sxs-lookup"><span data-stu-id="c8e83-140">This service is typically used in two ways.</span></span> <span data-ttu-id="c8e83-141">Zuerst kann Sie verwendet werden, um zu vermeiden, dass beim Entwickeln eines Modells Code First eine Verbindung mit der Datenbank hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c8e83-141">First, it can be used to avoid Code First connecting to the database when building a model.</span></span> <span data-ttu-id="c8e83-142">Zweitens kann Sie verwendet werden, um Code First zu erzwingen, ein Modell für eine bestimmte Datenbankversion zu erstellen, z. b. um ein Modell für SQL Server 2005 zu erzwingen, auch wenn manchmal SQL Server 2008 verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c8e83-142">Second, it can be used to force Code First to build a model for a specific database version -- for example, to force a model for SQL Server 2005 even if sometimes SQL Server 2008 is used.</span></span>  

<span data-ttu-id="c8e83-143">**Objekt Lebensdauer**: Singleton--das gleiche Objekt kann mehrmals und gleichzeitig von verschiedenen Threads verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c8e83-143">**Object lifetime**: Singleton -- the same object may be used multiple times and concurrently by different threads</span></span>  

<span data-ttu-id="c8e83-144">**Schlüssel**: nicht verwendet; wird NULL sein.</span><span class="sxs-lookup"><span data-stu-id="c8e83-144">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a><span data-ttu-id="c8e83-145">System. Data. Entity. Infrastructure. idbproviderfactoryservice</span><span class="sxs-lookup"><span data-stu-id="c8e83-145">System.Data.Entity.Infrastructure.IDbProviderFactoryService</span></span>  

<span data-ttu-id="c8e83-146">**Eingeführte Version**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-146">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="c8e83-147">**Zurück**gegebenes Objekt: ein Dienst, der eine Anbieterfactory von einer bestimmten Verbindung abrufen kann.</span><span class="sxs-lookup"><span data-stu-id="c8e83-147">**Object returned**: A service that can obtain a provider factory from a given connection.</span></span> <span data-ttu-id="c8e83-148">Unter .NET 4,5 ist der Anbieter öffentlich über die Verbindung zugänglich.</span><span class="sxs-lookup"><span data-stu-id="c8e83-148">On .NET 4.5 the provider is publicly accessible from the connection.</span></span> <span data-ttu-id="c8e83-149">In .NET 4 verwendet die Standard Implementierung dieses Dienstanbieters einige Heuristiken, um den passenden Anbieter zu suchen.</span><span class="sxs-lookup"><span data-stu-id="c8e83-149">On .NET 4 the default implementation of this service uses some heuristics to find the matching provider.</span></span> <span data-ttu-id="c8e83-150">Wenn diese Fehler auftreten, kann eine neue Implementierung dieses Dienstanbieter registriert werden, um eine geeignete Lösung bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="c8e83-150">If these fail then a new implementation of this service can be registered to provide an appropriate resolution.</span></span>  

<span data-ttu-id="c8e83-151">**Schlüssel**: nicht verwendet; wird NULL sein.</span><span class="sxs-lookup"><span data-stu-id="c8e83-151">**Key**: Not used; will be null</span></span>  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a><span data-ttu-id="c8e83-152">Func<dbcontext, System. Data. Entity. Infrastructure. idbmodelcachekey\></span><span class="sxs-lookup"><span data-stu-id="c8e83-152">Func<DbContext, System.Data.Entity.Infrastructure.IDbModelCacheKey\></span></span>  

<span data-ttu-id="c8e83-153">**Eingeführte Version**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-153">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="c8e83-154">**Zurück**gegebenes Objekt: eine Factory, die einen Modell Cache Schlüssel für einen bestimmten Kontext generiert.</span><span class="sxs-lookup"><span data-stu-id="c8e83-154">**Object returned**: A factory that will generate a model cache key for a given context.</span></span> <span data-ttu-id="c8e83-155">Standardmäßig speichert EF ein Modell pro dbcontext-Typ pro Anbieter zwischen.</span><span class="sxs-lookup"><span data-stu-id="c8e83-155">By default, EF caches one model per DbContext type per provider.</span></span> <span data-ttu-id="c8e83-156">Eine andere Implementierung dieses Dienstanbieter kann verwendet werden, um weitere Informationen, wie z. b. den Schema Namen, zum Cache Schlüssel hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c8e83-156">A different implementation of this service can be used to add other information, such as schema name, to the cache key.</span></span>  

<span data-ttu-id="c8e83-157">**Schlüssel**: nicht verwendet; wird NULL sein.</span><span class="sxs-lookup"><span data-stu-id="c8e83-157">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityspatialdbspatialservices"></a><span data-ttu-id="c8e83-158">System. Data. Entity. Spatial. dbspatialservices</span><span class="sxs-lookup"><span data-stu-id="c8e83-158">System.Data.Entity.Spatial.DbSpatialServices</span></span>  

<span data-ttu-id="c8e83-159">**Eingeführte Version**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-159">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="c8e83-160">**Zurück**gegebenes Objekt: ein EF-Anbieter für räumliche Daten, der dem einfachen EF-Anbieter Unterstützung für räumliche Geography-und geometry-Typen hinzufügt</span><span class="sxs-lookup"><span data-stu-id="c8e83-160">**Object returned**: An EF spatial provider that adds support to the basic EF provider for geography and geometry spatial types.</span></span>  

<span data-ttu-id="c8e83-161">**Schlüssel**: dbsptialservices wird auf zwei Arten angefordert.</span><span class="sxs-lookup"><span data-stu-id="c8e83-161">**Key**: DbSptialServices is asked for in two ways.</span></span> <span data-ttu-id="c8e83-162">Zuerst werden anbieterspezifische räumliche Dienste mithilfe eines dbproviderinfo-Objekts (das den invarianten Namen und das Manifest-Token enthält) als Schlüssel angefordert.</span><span class="sxs-lookup"><span data-stu-id="c8e83-162">First, provider-specific spatial services are requested using a DbProviderInfo object (which contains invariant name and manifest token) as the key.</span></span> <span data-ttu-id="c8e83-163">Zweitens können dbspatialservices ohne Schlüssel angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="c8e83-163">Second, DbSpatialServices can be asked for with no key.</span></span> <span data-ttu-id="c8e83-164">Dies wird zum Auflösen des "globalen räumlichen Anbieters" verwendet, der beim Erstellen eigenständiger dbgeography-oder dbgeometry-Typen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c8e83-164">This is used to resolve the "global spatial provider" which is used when creating stand-alone DbGeography or DbGeometry types.</span></span>  

>[!NOTE]
> <span data-ttu-id="c8e83-165">Weitere Informationen zu Anbieter bezogenen Diensten in EF6 finden Sie im Abschnitt [EF6 Provider Model](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="c8e83-165">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a><span data-ttu-id="c8e83-166">Func<System. Data. Entity. Infrastructure. idbexecutionstrategy\></span><span class="sxs-lookup"><span data-stu-id="c8e83-166">Func<System.Data.Entity.Infrastructure.IDbExecutionStrategy\></span></span>  

<span data-ttu-id="c8e83-167">**Eingeführte Version**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-167">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="c8e83-168">**Zurück**gegebenes Objekt: eine Factory zum Erstellen eines Dienstanbieters, der es einem Anbieter ermöglicht, Wiederholungs Versuche oder andere Verhalten zu implementieren, wenn Abfragen und Befehle für die Datenbank ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="c8e83-168">**Object returned**: A factory to create a service that allows a provider to implement retries or other behavior when queries and commands are executed against the database.</span></span> <span data-ttu-id="c8e83-169">Wenn keine Implementierung bereitgestellt wird, führt EF die Befehle einfach aus und verteilt alle ausgelösten Ausnahmen.</span><span class="sxs-lookup"><span data-stu-id="c8e83-169">If no implementation is provided, then EF will simply execute the commands and propagate any exceptions thrown.</span></span> <span data-ttu-id="c8e83-170">Bei SQL Server dieser Dienst zum Bereitstellen einer Wiederholungs Richtlinie verwendet, die besonders nützlich ist, wenn Sie auf cloudbasierten Datenbankservern wie SQL Azure ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="c8e83-170">For SQL Server this service is used to provide a retry policy which is especially useful when running against cloud-based database servers such as SQL Azure.</span></span>  

<span data-ttu-id="c8e83-171">**Key**: ein executionstrategykey-Objekt, das den invarianten Namen des Anbieters und optional einen Servernamen enthält, für den die Ausführungs Strategie verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="c8e83-171">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the execution strategy will be used.</span></span>  

>[!NOTE]
> <span data-ttu-id="c8e83-172">Weitere Informationen zu Anbieter bezogenen Diensten in EF6 finden Sie im Abschnitt [EF6 Provider Model](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="c8e83-172">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a><span data-ttu-id="c8e83-173">Func<DbConnection, String, System. Data. Entity. Migrationen. History. historycontext\></span><span class="sxs-lookup"><span data-stu-id="c8e83-173">Func<DbConnection, string, System.Data.Entity.Migrations.History.HistoryContext\></span></span>  

<span data-ttu-id="c8e83-174">**Eingeführte Version**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-174">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="c8e83-175">**Zurück**gegebenes Objekt: eine Factory, mit der ein Anbieter die Zuordnung von historycontext zu der `__MigrationHistory` von EF-Migrationen verwendeten Tabelle konfigurieren kann.</span><span class="sxs-lookup"><span data-stu-id="c8e83-175">**Object returned**: A factory that allows a provider to configure the mapping of the HistoryContext to the `__MigrationHistory` table used by EF Migrations.</span></span> <span data-ttu-id="c8e83-176">Der historycontext ist eine Code First dbcontext und kann mit der normalen flüssigen API konfiguriert werden, um Dinge wie den Namen der Tabelle und die Spalten Mappingspezifikationen zu ändern.</span><span class="sxs-lookup"><span data-stu-id="c8e83-176">The HistoryContext is a Code First DbContext and can be configured using the normal fluent API to change things like the name of the table and the column mapping specifications.</span></span>  

<span data-ttu-id="c8e83-177">**Schlüssel**: nicht verwendet; wird NULL sein.</span><span class="sxs-lookup"><span data-stu-id="c8e83-177">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="c8e83-178">Weitere Informationen zu Anbieter bezogenen Diensten in EF6 finden Sie im Abschnitt [EF6 Provider Model](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="c8e83-178">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdatacommondbproviderfactory"></a><span data-ttu-id="c8e83-179">System. Data. Common. DbProviderFactory</span><span class="sxs-lookup"><span data-stu-id="c8e83-179">System.Data.Common.DbProviderFactory</span></span>  

<span data-ttu-id="c8e83-180">**Eingeführte Version**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-180">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="c8e83-181">**Zurück**gegebenes Objekt: der ADO.NET-Anbieter, der für den invarianten Namen eines angegebenen Anbieters verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="c8e83-181">**Object returned**: The ADO.NET provider to use for a given provider invariant name.</span></span>  

<span data-ttu-id="c8e83-182">**Key**: eine Zeichenfolge, die den invarianten Namen des ADO.NET-Anbieters enthält</span><span class="sxs-lookup"><span data-stu-id="c8e83-182">**Key**: A string containing the ADO.NET provider invariant name</span></span>  

>[!NOTE]
> <span data-ttu-id="c8e83-183">Dieser Dienst wird in der Regel nicht direkt geändert, da die Standard Implementierung die normale ADO.NET-Anbieter Registrierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="c8e83-183">This service is not usually changed directly since the default implementation uses the normal ADO.NET provider registration.</span></span> <span data-ttu-id="c8e83-184">Weitere Informationen zu Anbieter bezogenen Diensten in EF6 finden Sie im Abschnitt [EF6 Provider Model](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="c8e83-184">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a><span data-ttu-id="c8e83-185">System. Data. Entity. Infrastructure. iproviderinvariantname</span><span class="sxs-lookup"><span data-stu-id="c8e83-185">System.Data.Entity.Infrastructure.IProviderInvariantName</span></span>  

<span data-ttu-id="c8e83-186">**Eingeführte Version**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-186">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="c8e83-187">**Zurück**gegebenes Objekt: ein Dienst, der verwendet wird, um einen invarianten Anbieter Namen für einen bestimmten Typ von "DbProviderFactory" zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="c8e83-187">**Object returned**: a service that is used to determine a provider invariant name for a given type of DbProviderFactory.</span></span> <span data-ttu-id="c8e83-188">Die Standard Implementierung dieses Dienstanbieters verwendet die ADO.NET-Anbieter Registrierung.</span><span class="sxs-lookup"><span data-stu-id="c8e83-188">The default implementation of this service uses the ADO.NET provider registration.</span></span> <span data-ttu-id="c8e83-189">Dies bedeutet Folgendes: Wenn der ADO.NET-Anbieter nicht auf die normale Weise registriert ist, weil DbProviderFactory von EF aufgelöst wird, muss dieser Dienst ebenfalls aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="c8e83-189">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span>  

<span data-ttu-id="c8e83-190">**Key**: die DbProviderFactory-Instanz, für die ein invarianter Name erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="c8e83-190">**Key**: The DbProviderFactory instance for which an invariant name is required.</span></span>  

>[!NOTE]
> <span data-ttu-id="c8e83-191">Weitere Informationen zu Anbieter bezogenen Diensten in EF6 finden Sie im Abschnitt [EF6 Provider Model](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="c8e83-191">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a><span data-ttu-id="c8e83-192">System. Data. Entity. Core. Mapping. viewgene. iviewassemblycache</span><span class="sxs-lookup"><span data-stu-id="c8e83-192">System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache</span></span>  

<span data-ttu-id="c8e83-193">**Eingeführte Version**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-193">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="c8e83-194">**Zurück**gegebenes Objekt: ein Cache der Assemblys, die vorgenerierte Sichten enthalten.</span><span class="sxs-lookup"><span data-stu-id="c8e83-194">**Object returned**: a cache of the assemblies that contain pre-generated views.</span></span> <span data-ttu-id="c8e83-195">Ein Ersatz wird normalerweise verwendet, um EF darüber zu informieren, welche Assemblys vorab generierte Sichten enthalten, ohne dass eine Ermittlung durchgeführt wird</span><span class="sxs-lookup"><span data-stu-id="c8e83-195">A replacement is typically used to let EF know which assemblies contain pre-generated views without doing any discovery.</span></span>  

<span data-ttu-id="c8e83-196">**Schlüssel**: nicht verwendet; wird NULL sein.</span><span class="sxs-lookup"><span data-stu-id="c8e83-196">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a><span data-ttu-id="c8e83-197">System. Data. Entity. Infrastructure. Pluralization. ipluralizationservice</span><span class="sxs-lookup"><span data-stu-id="c8e83-197">System.Data.Entity.Infrastructure.Pluralization.IPluralizationService</span></span>

<span data-ttu-id="c8e83-198">**Eingeführte Version**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-198">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="c8e83-199">**Zurück**gegebenes Objekt: ein Dienst, der von EF zum pluralisieren und singularisieren von Namen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c8e83-199">**Object returned**: a service used by EF to pluralize and singularize names.</span></span> <span data-ttu-id="c8e83-200">Standardmäßig wird ein englischer pluralisierungs Dienst verwendet.</span><span class="sxs-lookup"><span data-stu-id="c8e83-200">By default an English pluralization service is used.</span></span>  

<span data-ttu-id="c8e83-201">**Schlüssel**: nicht verwendet; wird NULL sein.</span><span class="sxs-lookup"><span data-stu-id="c8e83-201">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a><span data-ttu-id="c8e83-202">System. Data. Entity. Infrastructure. intercep. idbinterceptor</span><span class="sxs-lookup"><span data-stu-id="c8e83-202">System.Data.Entity.Infrastructure.Interception.IDbInterceptor</span></span>  

<span data-ttu-id="c8e83-203">**Eingeführte Version**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-203">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="c8e83-204">**Zurückgegebene Objekte**: alle Interceptors, die beim Starten der Anwendung registriert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c8e83-204">**Objects returned**: Any interceptors that should be registered when the application starts.</span></span> <span data-ttu-id="c8e83-205">Beachten Sie, dass diese Objekte mithilfe des GetServices-Aufrufes angefordert werden und alle von einem Abhängigkeits Konflikt Löser zurückgegebenen Interceptors registriert werden.</span><span class="sxs-lookup"><span data-stu-id="c8e83-205">Note that these objects are requested using the GetServices call and all interceptors returned by any dependency resolver will registered.</span></span>

<span data-ttu-id="c8e83-206">**Schlüssel**: nicht verwendet; wird NULL sein.</span><span class="sxs-lookup"><span data-stu-id="c8e83-206">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a><span data-ttu-id="c8e83-207">Func<System. Data. Entity. dbcontext, Action<String \> , System. Data. Entity. Infrastructure. intercep. databaselogformatter\></span><span class="sxs-lookup"><span data-stu-id="c8e83-207">Func<System.Data.Entity.DbContext, Action<string\>, System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\></span></span>  

<span data-ttu-id="c8e83-208">**Eingeführte Version**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-208">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="c8e83-209">**Zurück**gegebenes Objekt: eine Factory, die verwendet wird, um den Daten Bank Protokoll-Formatierer zu erstellen, der verwendet wird, wenn der Kontext verwendet wird. Die Database. Log-Eigenschaft wird für den angegebenen Kontext festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c8e83-209">**Object returned**: A factory that will be used to create the database log formatter that will be used when the context.Database.Log property is set on the given context.</span></span>  

<span data-ttu-id="c8e83-210">**Schlüssel**: nicht verwendet; wird NULL sein.</span><span class="sxs-lookup"><span data-stu-id="c8e83-210">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext"></a><span data-ttu-id="c8e83-211">Func-<System. Data. Entity. dbcontext\></span><span class="sxs-lookup"><span data-stu-id="c8e83-211">Func<System.Data.Entity.DbContext\></span></span>  

<span data-ttu-id="c8e83-212">**Eingeführte Version**: EF 6.1.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-212">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="c8e83-213">**Zurück**gegebenes Objekt: eine Factory, die verwendet wird, um Kontext Instanzen für Migrationen zu erstellen, wenn der Kontext keinen zugänglichen Parameter losen Konstruktor hat.</span><span class="sxs-lookup"><span data-stu-id="c8e83-213">**Object returned**: A factory that will be used to create context instances for Migrations when the context does not have an accessible parameterless constructor.</span></span>  

<span data-ttu-id="c8e83-214">**Key**: das Type-Objekt für den Typ des abgeleiteten dbcontext, für den eine Factory benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="c8e83-214">**Key**: The Type object for the type of the derived DbContext for which a factory is needed.</span></span>  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a><span data-ttu-id="c8e83-215">Func<System. Data. Entity. Core. Metadata. Edm. IMetadataAnnotationSerializer\></span><span class="sxs-lookup"><span data-stu-id="c8e83-215">Func<System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\></span></span>  

<span data-ttu-id="c8e83-216">**Eingeführte Version**: EF 6.1.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-216">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="c8e83-217">**Zurück**gegebenes Objekt: eine Factory, die verwendet wird, um Serialisierungsprogrammen für die Serialisierung von stark typisierten benutzerdefinierten Anmerkungen zu erstellen, sodass Sie serialisiert und in XML umgewandelt werden können, um Sie in Code First-Migrationen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c8e83-217">**Object returned**: A factory that will be used to create serializers for serialization of strongly-typed custom annotations such that they can be serialized and desterilized into XML for use in Code First Migrations.</span></span>  

<span data-ttu-id="c8e83-218">**Key**: der Name der Anmerkung, die serialisiert oder deserialisiert wird.</span><span class="sxs-lookup"><span data-stu-id="c8e83-218">**Key**: The name of the annotation that is being serialized or deserialized.</span></span>  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a><span data-ttu-id="c8e83-219">Func-<System. Data. Entity. Infrastructure. transaktionhandler\></span><span class="sxs-lookup"><span data-stu-id="c8e83-219">Func<System.Data.Entity.Infrastructure.TransactionHandler\></span></span>  

<span data-ttu-id="c8e83-220">**Eingeführte Version**: EF 6.1.0</span><span class="sxs-lookup"><span data-stu-id="c8e83-220">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="c8e83-221">**Zurück**gegebenes Objekt: eine Factory, die zum Erstellen von Handlern für Transaktionen verwendet wird, sodass eine spezielle Behandlung für Situationen wie das Behandeln von commitfehlern angewendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c8e83-221">**Object returned**: A factory that will be used to create handlers for transactions so that special handling can be applied for situations such as handling commit failures.</span></span>  

<span data-ttu-id="c8e83-222">**Key**: ein executionstrategykey-Objekt, das den invarianten Namen des Anbieters und optional einen Servernamen enthält, für den der Transaktions Handler verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c8e83-222">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the transaction handler will be used.</span></span>  
