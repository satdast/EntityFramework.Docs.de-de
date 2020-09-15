---
title: Abhängigkeitsauflösung-EF6
description: Abhängigkeitsauflösung in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/dependency-resolution
ms.openlocfilehash: 24e932646f0c44f642bff3f6db321590abae1c8d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070548"
---
# <a name="dependency-resolution"></a>Abhängigkeitsauflösung
> [!NOTE]
> **Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.  

Ab EF6 enthält Entity Framework einen allgemeinen Mechanismus zum Abrufen von erforderlichen Implementierungen von Diensten. Das heißt, wenn EF eine Instanz einiger Schnittstellen oder Basisklassen verwendet, wird eine konkrete Implementierung der-Schnittstelle oder der Basisklasse angefordert, die verwendet werden soll. Dies wird durch die Verwendung der idbdependencyresolver-Schnittstelle erreicht:  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

Die GetService-Methode wird in der Regel von EF aufgerufen und wird von einer Implementierung von idbdependencyresolver behandelt, die entweder von EF oder der Anwendung bereitgestellt wird. Beim Aufruf ist das Typargument die Schnittstelle oder der Basis Klassentyp des angeforderten Dienstanbieter, das Schlüsselobjekt ist entweder NULL oder ein Objekt, das Kontextinformationen zum angeforderten Dienst bereitstellt.  

Sofern nicht anders angegeben, muss jedes zurückgegebene Objekt Thread sicher sein, da es als Singleton verwendet werden kann. In vielen Fällen ist das zurückgegebene Objekt eine Factory. in diesem Fall muss die Factory selbst Thread sicher sein, aber das von der Factory zurückgegebene Objekt muss nicht Thread sicher sein, da eine neue Instanz für jede Verwendung von der Factory angefordert wird.

Dieser Artikel enthält keine vollständigen Details zur Implementierung von idbdependencyresolver, sondern fungiert als Referenz für die Dienst Typen (d. h. die Schnittstelle und Basisklassen Typen), für die EF GetService aufruft, und die Semantik des Schlüssel Objekts für jeden dieser Aufrufe.

## <a name="systemdataentityidatabaseinitializertcontext"></a>System. Data. Entity. idatabaseinitializer<tcontext\>  

**Eingeführte Version**: EF 6.0.0  

**Zurück**gegebenes Objekt: ein datenbankinitialisierer für den angegebenen Kontexttyp.  

**Schlüssel**: nicht verwendet; wird NULL sein.  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a>Func<System. Data. Entity. Migration. SQL. migrationsqlgenerator\>  

**Eingeführte Version**: EF 6.0.0

**Zurück**gegebenes Objekt: eine Factory zum Erstellen eines SQL-Generators, der für Migrationen und andere Aktionen verwendet werden kann, die eine Datenbank erstellen, z. b. die Erstellung von Datenbanken mit datenbankinitialisierern.  

**Key**: eine Zeichenfolge, die den invarianten Namen des ADO.NET-Anbieters enthält, der den Typ der Datenbank angibt, für die SQL generiert wird. Beispielsweise wird der SQL Server SQL-Generator für den Schlüssel "System. Data. SqlClient" zurückgegeben.  

>[!NOTE]
> Weitere Informationen zu Anbieter bezogenen Diensten in EF6 finden Sie im Abschnitt [EF6 Provider Model](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentitycorecommondbproviderservices"></a>System. Data. Entity. Core. Common. DbProviderServices  

**Eingeführte Version**: EF 6.0.0  

**Zurück**gegebenes Objekt: der EF-Anbieter, der für den invarianten Namen eines angegebenen Anbieters verwendet wird.  

**Key**: eine Zeichenfolge, die den invarianten Namen des ADO.NET-Anbieters enthält, der den Typ der Datenbank angibt, für die ein Anbieter benötigt wird. Beispielsweise wird der SQL Server Anbieter für den Schlüssel "System. Data. SqlClient" zurückgegeben.  

>[!NOTE]
> Weitere Informationen zu Anbieter bezogenen Diensten in EF6 finden Sie im Abschnitt [EF6 Provider Model](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a>System. Data. Entity. Infrastructure. idbconnectionfactory  

**Eingeführte Version**: EF 6.0.0  

**Zurück**gegebenes Objekt: die Verbindungsfactory, die verwendet wird, wenn EF eine Datenbankverbindung gemäß der Konvention erstellt. Das heißt, wenn EF keine Verbindung oder Verbindungs Zeichenfolge erhält und keine Verbindungs Zeichenfolge in der app.config oder web.config gefunden werden kann, wird dieser Dienst zum Erstellen einer Verbindung gemäß der Konvention verwendet. Wenn Sie die Verbindungsfactory ändern, kann EF standardmäßig einen anderen Typ von Datenbank (z. b. SQL Server Compact Edition) verwenden.  

**Schlüssel**: nicht verwendet; wird NULL sein.  

>[!NOTE]
> Weitere Informationen zu Anbieter bezogenen Diensten in EF6 finden Sie im Abschnitt [EF6 Provider Model](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a>System. Data. Entity. Infrastructure. IManifestTokenService  

**Eingeführte Version**: EF 6.0.0  

**Zurück**gegebenes Objekt: ein Dienst, der ein Anbieter Manifest-Token aus einer Verbindung generieren kann. Dieser Dienst wird in der Regel auf zweierlei Weise verwendet. Zuerst kann Sie verwendet werden, um zu vermeiden, dass beim Entwickeln eines Modells Code First eine Verbindung mit der Datenbank hergestellt wird. Zweitens kann Sie verwendet werden, um Code First zu erzwingen, ein Modell für eine bestimmte Datenbankversion zu erstellen, z. b. um ein Modell für SQL Server 2005 zu erzwingen, auch wenn manchmal SQL Server 2008 verwendet wird.  

**Objekt Lebensdauer**: Singleton--das gleiche Objekt kann mehrmals und gleichzeitig von verschiedenen Threads verwendet werden.  

**Schlüssel**: nicht verwendet; wird NULL sein.  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a>System. Data. Entity. Infrastructure. idbproviderfactoryservice  

**Eingeführte Version**: EF 6.0.0  

**Zurück**gegebenes Objekt: ein Dienst, der eine Anbieterfactory von einer bestimmten Verbindung abrufen kann. Unter .NET 4,5 ist der Anbieter öffentlich über die Verbindung zugänglich. In .NET 4 verwendet die Standard Implementierung dieses Dienstanbieters einige Heuristiken, um den passenden Anbieter zu suchen. Wenn diese Fehler auftreten, kann eine neue Implementierung dieses Dienstanbieter registriert werden, um eine geeignete Lösung bereitzustellen.  

**Schlüssel**: nicht verwendet; wird NULL sein.  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a>Func<dbcontext, System. Data. Entity. Infrastructure. idbmodelcachekey\>  

**Eingeführte Version**: EF 6.0.0  

**Zurück**gegebenes Objekt: eine Factory, die einen Modell Cache Schlüssel für einen bestimmten Kontext generiert. Standardmäßig speichert EF ein Modell pro dbcontext-Typ pro Anbieter zwischen. Eine andere Implementierung dieses Dienstanbieter kann verwendet werden, um weitere Informationen, wie z. b. den Schema Namen, zum Cache Schlüssel hinzuzufügen.  

**Schlüssel**: nicht verwendet; wird NULL sein.  

## <a name="systemdataentityspatialdbspatialservices"></a>System. Data. Entity. Spatial. dbspatialservices  

**Eingeführte Version**: EF 6.0.0  

**Zurück**gegebenes Objekt: ein EF-Anbieter für räumliche Daten, der dem einfachen EF-Anbieter Unterstützung für räumliche Geography-und geometry-Typen hinzufügt  

**Schlüssel**: dbsptialservices wird auf zwei Arten angefordert. Zuerst werden anbieterspezifische räumliche Dienste mithilfe eines dbproviderinfo-Objekts (das den invarianten Namen und das Manifest-Token enthält) als Schlüssel angefordert. Zweitens können dbspatialservices ohne Schlüssel angefordert werden. Dies wird zum Auflösen des "globalen räumlichen Anbieters" verwendet, der beim Erstellen eigenständiger dbgeography-oder dbgeometry-Typen verwendet wird.  

>[!NOTE]
> Weitere Informationen zu Anbieter bezogenen Diensten in EF6 finden Sie im Abschnitt [EF6 Provider Model](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a>Func<System. Data. Entity. Infrastructure. idbexecutionstrategy\>  

**Eingeführte Version**: EF 6.0.0  

**Zurück**gegebenes Objekt: eine Factory zum Erstellen eines Dienstanbieters, der es einem Anbieter ermöglicht, Wiederholungs Versuche oder andere Verhalten zu implementieren, wenn Abfragen und Befehle für die Datenbank ausgeführt werden. Wenn keine Implementierung bereitgestellt wird, führt EF die Befehle einfach aus und verteilt alle ausgelösten Ausnahmen. Bei SQL Server dieser Dienst zum Bereitstellen einer Wiederholungs Richtlinie verwendet, die besonders nützlich ist, wenn Sie auf cloudbasierten Datenbankservern wie SQL Azure ausgeführt werden.  

**Key**: ein executionstrategykey-Objekt, das den invarianten Namen des Anbieters und optional einen Servernamen enthält, für den die Ausführungs Strategie verwendet werden soll.  

>[!NOTE]
> Weitere Informationen zu Anbieter bezogenen Diensten in EF6 finden Sie im Abschnitt [EF6 Provider Model](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a>Func<DbConnection, String, System. Data. Entity. Migrationen. History. historycontext\>  

**Eingeführte Version**: EF 6.0.0  

**Zurück**gegebenes Objekt: eine Factory, mit der ein Anbieter die Zuordnung von historycontext zu der `__MigrationHistory` von EF-Migrationen verwendeten Tabelle konfigurieren kann. Der historycontext ist eine Code First dbcontext und kann mit der normalen flüssigen API konfiguriert werden, um Dinge wie den Namen der Tabelle und die Spalten Mappingspezifikationen zu ändern.  

**Schlüssel**: nicht verwendet; wird NULL sein.  

>[!NOTE]
> Weitere Informationen zu Anbieter bezogenen Diensten in EF6 finden Sie im Abschnitt [EF6 Provider Model](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdatacommondbproviderfactory"></a>System. Data. Common. DbProviderFactory  

**Eingeführte Version**: EF 6.0.0  

**Zurück**gegebenes Objekt: der ADO.NET-Anbieter, der für den invarianten Namen eines angegebenen Anbieters verwendet werden soll.  

**Key**: eine Zeichenfolge, die den invarianten Namen des ADO.NET-Anbieters enthält  

>[!NOTE]
> Dieser Dienst wird in der Regel nicht direkt geändert, da die Standard Implementierung die normale ADO.NET-Anbieter Registrierung verwendet. Weitere Informationen zu Anbieter bezogenen Diensten in EF6 finden Sie im Abschnitt [EF6 Provider Model](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a>System. Data. Entity. Infrastructure. iproviderinvariantname  

**Eingeführte Version**: EF 6.0.0  

**Zurück**gegebenes Objekt: ein Dienst, der verwendet wird, um einen invarianten Anbieter Namen für einen bestimmten Typ von "DbProviderFactory" zu ermitteln. Die Standard Implementierung dieses Dienstanbieters verwendet die ADO.NET-Anbieter Registrierung. Dies bedeutet Folgendes: Wenn der ADO.NET-Anbieter nicht auf die normale Weise registriert ist, weil DbProviderFactory von EF aufgelöst wird, muss dieser Dienst ebenfalls aufgelöst werden.  

**Key**: die DbProviderFactory-Instanz, für die ein invarianter Name erforderlich ist.  

>[!NOTE]
> Weitere Informationen zu Anbieter bezogenen Diensten in EF6 finden Sie im Abschnitt [EF6 Provider Model](xref:ef6/fundamentals/providers/provider-model) .  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a>System. Data. Entity. Core. Mapping. viewgene. iviewassemblycache  

**Eingeführte Version**: EF 6.0.0  

**Zurück**gegebenes Objekt: ein Cache der Assemblys, die vorgenerierte Sichten enthalten. Ein Ersatz wird normalerweise verwendet, um EF darüber zu informieren, welche Assemblys vorab generierte Sichten enthalten, ohne dass eine Ermittlung durchgeführt wird  

**Schlüssel**: nicht verwendet; wird NULL sein.  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a>System. Data. Entity. Infrastructure. Pluralization. ipluralizationservice

**Eingeführte Version**: EF 6.0.0  

**Zurück**gegebenes Objekt: ein Dienst, der von EF zum pluralisieren und singularisieren von Namen verwendet wird. Standardmäßig wird ein englischer pluralisierungs Dienst verwendet.  

**Schlüssel**: nicht verwendet; wird NULL sein.  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a>System. Data. Entity. Infrastructure. intercep. idbinterceptor  

**Eingeführte Version**: EF 6.0.0

**Zurückgegebene Objekte**: alle Interceptors, die beim Starten der Anwendung registriert werden sollen. Beachten Sie, dass diese Objekte mithilfe des GetServices-Aufrufes angefordert werden und alle von einem Abhängigkeits Konflikt Löser zurückgegebenen Interceptors registriert werden.

**Schlüssel**: nicht verwendet; wird NULL sein.  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a>Func<System. Data. Entity. dbcontext, Action<String \> , System. Data. Entity. Infrastructure. intercep. databaselogformatter\>  

**Eingeführte Version**: EF 6.0.0  

**Zurück**gegebenes Objekt: eine Factory, die verwendet wird, um den Daten Bank Protokoll-Formatierer zu erstellen, der verwendet wird, wenn der Kontext verwendet wird. Die Database. Log-Eigenschaft wird für den angegebenen Kontext festgelegt.  

**Schlüssel**: nicht verwendet; wird NULL sein.  

## <a name="funcsystemdataentitydbcontext"></a>Func-<System. Data. Entity. dbcontext\>  

**Eingeführte Version**: EF 6.1.0  

**Zurück**gegebenes Objekt: eine Factory, die verwendet wird, um Kontext Instanzen für Migrationen zu erstellen, wenn der Kontext keinen zugänglichen Parameter losen Konstruktor hat.  

**Key**: das Type-Objekt für den Typ des abgeleiteten dbcontext, für den eine Factory benötigt wird.  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a>Func<System. Data. Entity. Core. Metadata. Edm. IMetadataAnnotationSerializer\>  

**Eingeführte Version**: EF 6.1.0  

**Zurück**gegebenes Objekt: eine Factory, die verwendet wird, um Serialisierungsprogrammen für die Serialisierung von stark typisierten benutzerdefinierten Anmerkungen zu erstellen, sodass Sie serialisiert und in XML umgewandelt werden können, um Sie in Code First-Migrationen zu verwenden.  

**Key**: der Name der Anmerkung, die serialisiert oder deserialisiert wird.  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a>Func-<System. Data. Entity. Infrastructure. transaktionhandler\>  

**Eingeführte Version**: EF 6.1.0  

**Zurück**gegebenes Objekt: eine Factory, die zum Erstellen von Handlern für Transaktionen verwendet wird, sodass eine spezielle Behandlung für Situationen wie das Behandeln von commitfehlern angewendet werden kann.  

**Key**: ein executionstrategykey-Objekt, das den invarianten Namen des Anbieters und optional einen Servernamen enthält, für den der Transaktions Handler verwendet wird.  
