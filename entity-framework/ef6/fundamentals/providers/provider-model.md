---
title: Das Entity Framework 6-Anbieter Modell EF6
description: Das Entity Framework 6-Anbieter Modell in Entity Framework 6
author: divega
ms.date: 06/27/2018
ms.assetid: 066832F0-D51B-4655-8BE7-C983C557E0E4
uid: ef6/fundamentals/providers/provider-model
ms.openlocfilehash: af5522a5f8cf03fbf2725f7cf85673c3aa43aa27
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618386"
---
# <a name="the-entity-framework-6-provider-model"></a>Das Entity Framework 6-Anbieter Modell

Das Entity Framework-Anbieter Modell ermöglicht die Verwendung Entity Framework mit verschiedenen Typen von Datenbankservern. Ein Anbieter kann z. b. angeschlossen werden, damit EF für Microsoft SQL Server verwendet werden kann, während ein anderer Anbieter angeschlossen werden kann, damit EF für Microsoft SQL Server Compact Edition verwendet werden kann. Die Anbieter für EF6, die wir kennen, finden Sie auf der Seite [Entity Framework Anbieter](xref:ef6/fundamentals/providers/index) .

Es waren bestimmte Änderungen an der Art und Weise erforderlich, in der EF mit Anbietern interagiert, damit EF unter einer Open-Source-Lizenzfrei gegeben werden kann. Diese Änderungen erfordern die Neuerstellung von EF-Anbietern für die EF6-Assemblys und neue Mechanismen für die Registrierung des Anbieters.

## <a name="rebuilding"></a>Neuerstellung

Mit EF6 wird der Kerncode, der zuvor Teil des .NET Framework war, jetzt als out-of-Band-Assemblys (OOB) ausgeliefert. Ausführliche Informationen zum Erstellen von Anwendungen für EF6 finden Sie auf der Seite [Aktualisieren von Anwendungen für EF6](xref:ef6/what-is-new/upgrading-to-ef6) . Anbieter müssen auch mit diesen Anweisungen neu erstellt werden.

## <a name="provider-types-overview"></a>Übersicht über Anbieter Typen

Ein EF-Anbieter ist wirklich eine Auflistung von anbieterspezifischen Diensten, die von CLR-Typen definiert werden, von denen diese Dienste (für eine Basisklasse) oder implementieren (für eine Schnittstelle). Zwei dieser Dienste sind grundlegend und notwendig, damit EF überhaupt funktionsfähig ist. Andere sind optional und müssen nur implementiert werden, wenn bestimmte Funktionen erforderlich sind und/oder die Standard Implementierungen dieser Dienste für den spezifischen Datenbankserver nicht funktionieren.

## <a name="fundamental-provider-types"></a>Grundlegende Anbieter Typen

### <a name="dbproviderfactory"></a>DbProviderFactory

EF ist davon abhängig, dass ein von [System. Data. Common. DbProviderFactory](https://msdn.microsoft.com/library/system.data.common.dbproviderfactory.aspx) abgeleiteter Typ für den gesamten Datenbankzugriff auf niedriger Ebene vorhanden ist. DbProviderFactory ist nicht Teil von EF, sondern ist stattdessen eine Klasse in der .NET Framework, die einen Einstiegspunkt für ADO.NET-Anbieter verwendet, der von EF, anderen O/RMS oder direkt von einer Anwendung verwendet werden kann, um Instanzen von Verbindungen, Befehlen, Parametern und anderen ADO.net-Abstraktionen in einer Anbieter agnostischen Weise abzurufen. Weitere Informationen zu DbProviderFactory finden Sie in der [MSDN-Dokumentation zu ADO.net](https://msdn.microsoft.com/library/a6cd7c08.aspx).

### <a name="dbproviderservices"></a>DbProviderServices

EF hängt von der Verwendung eines Typs ab, der von DbProviderServices abgeleitet ist, um zusätzliche Funktionalität bereitzustellen, die von EF zusätzlich zu den bereits vom ADO.NET-Anbieter bereitgestellten Funktionen benötigt wird. In älteren Versionen von EF war die DbProviderServices-Klasse Teil des .NET Framework und wurde im System. Data. Common-Namespace gefunden. Beginnend mit EF6 diese Klasse ist nun Teil EntityFramework.dll und befindet sich im System. Data. Entity. Core. Common-Namespace.

Weitere Informationen zu den grundlegenden Funktionen einer DbProviderServices-Implementierung finden Sie auf der [MSDN](https://msdn.microsoft.com/library/ee789835.aspx)-Website. Beachten Sie jedoch, dass zum Zeitpunkt der Erstellung dieser Informationen nicht für EF6 aktualisiert wird, obwohl die meisten Konzepte weiterhin gültig sind. Die SQL Server-und SQL Server Compact-Implementierungen von DbProviderServices werden ebenfalls in die [Open Source-Codebasis](https://github.com/aspnet/EntityFramework6/) eingecheckt und können als nützliche Verweise für andere Implementierungen dienen.

In älteren Versionen von EF wurde die zu verwendende DbProviderServices-Implementierung direkt von einem ADO.NET-Anbieter abgerufen. Dies erfolgt durch Umwandeln von DbProviderFactory in IServiceProvider und Aufrufen der GetService-Methode. Dadurch wurde der EF-Anbieter eng an die DbProviderFactory gekoppelt. Diese Kopplung blockierte EF daran, nicht aus der .NET Framework verschoben zu werden. Daher wurde diese enge Kopplung entfernt, und eine Implementierung von DbProviderServices ist jetzt direkt in der Konfigurationsdatei der Anwendung oder in der Code basierten Konfiguration registriert, wie im Abschnitt Registrieren von _DbProviderServices_ weiter unten beschrieben.

## <a name="additional-services"></a>Zusätzliche Dienste

Zusätzlich zu den grundlegenden Diensten, die oben beschrieben werden, gibt es auch viele andere Dienste, die von EF verwendet werden, die entweder immer oder manchmal Anbieter spezifisch sind. Standardmäßige anbieterspezifische Implementierungen dieser Dienste können durch eine DbProviderServices-Implementierung bereitgestellt werden. Anwendungen können auch die Implementierungen dieser Dienste überschreiben oder Implementierungen bereitstellen, wenn ein DbProviderServices-Typ keinen Standardwert bereitstellt. Dies wird im Abschnitt " _Auflösen zusätzlicher Dienste_ " ausführlicher beschrieben.

Die zusätzlichen Dienst Typen, die ein Anbieter für einen Anbieter interessieren kann, sind unten aufgeführt. Weitere Informationen zu den einzelnen Dienst Typen finden Sie in der API-Dokumentation.

### <a name="idbexecutionstrategy"></a>IDbExecutionStrategy

Dies ist ein optionaler Dienst, der es einem Anbieter ermöglicht, Wiederholungs Versuche oder andere Verhalten zu implementieren, wenn Abfragen und Befehle für die Datenbank ausgeführt werden. Wenn keine Implementierung bereitgestellt wird, führt EF die Befehle einfach aus und verteilt alle ausgelösten Ausnahmen. Bei SQL Server dieser Dienst zum Bereitstellen einer Wiederholungs Richtlinie verwendet, die besonders nützlich ist, wenn Sie auf cloudbasierten Datenbankservern wie SQL Azure ausgeführt werden.

### <a name="idbconnectionfactory"></a>Idbconnectionfactory

Dies ist ein optionaler Dienst, mit dem ein Anbieter DbConnection-Objekte gemäß der Konvention erstellen kann, wenn nur ein Datenbankname angegeben wird. Beachten Sie, dass dieser Dienst zwar durch eine DbProviderServices-Implementierung aufgelöst werden kann, aber er ist seit EF 4,1 vorhanden und kann auch explizit in der Konfigurationsdatei oder im Code festgelegt werden. Der Anbieter erhält nur die Möglichkeit, diesen Dienst aufzulösen, wenn er als Standardanbieter registriert ist (siehe _Standardanbieter_ unten) und eine standardverbindungsfactory nicht an anderer Stelle festgelegt wurde.

### <a name="dbspatialservices"></a>Dbspatialservices

Dies ist ein optionaler Dienst, mit dem ein Anbieter Unterstützung für räumliche Geography-und geometry-Typen hinzufügen kann. Eine Implementierung dieses Dienstanbieter muss bereitgestellt werden, damit eine Anwendung EF mit räumlichen Typen verwenden können. Dbsptialservices wird auf zwei Arten angefordert. Zuerst werden anbieterspezifische räumliche Dienste mithilfe eines dbproviderinfo-Objekts (das den invarianten Namen und das Manifest-Token enthält) als Schlüssel angefordert. Zweitens können dbspatialservices ohne Schlüssel angefordert werden. Dies wird zum Auflösen des "globalen räumlichen Anbieters" verwendet, der beim Erstellen eigenständiger dbgeography-oder dbgeometry-Typen verwendet wird.

### <a name="migrationsqlgenerator"></a>Migrationsqlgenerator

Dies ist ein optionaler Dienst, mit dem EF-Migrationen für die Generierung von SQL verwendet werden können, die beim Erstellen und Ändern von Datenbankschemas durch Code First verwendet wird. Eine-Implementierung ist erforderlich, um Migrationen zu unterstützen. Wenn eine Implementierung bereitgestellt wird, wird Sie auch verwendet, wenn Datenbanken mithilfe von datenbankinitialisierern oder der Database. Create-Methode erstellt werden.

### <a name="funcdbconnection-string-historycontextfactory"></a>Func<DbConnection, String, historycontextfactory>

Dies ist ein optionaler Dienst, mit dem ein Anbieter die Zuordnung von historycontext zu der `__MigrationHistory` von EF-Migrationen verwendeten Tabelle konfigurieren kann. Der historycontext ist eine Code First dbcontext und kann mit der normalen flüssigen API konfiguriert werden, um Dinge wie den Namen der Tabelle und die Spalten Mappingspezifikationen zu ändern. Die Standard Implementierung dieses Dienstanbieters, der von EF für alle Anbieter zurückgegeben wird, kann für einen bestimmten Datenbankserver funktionieren, wenn alle standardmäßigen Tabellen-und Spalten Zuordnungen von diesem Anbieter unterstützt werden. In einem solchen Fall muss der Anbieter keine Implementierung dieses Dienstanbieters bereitstellen.

### <a name="idbproviderfactoryresolver"></a>Idbproviderfactor yresolver

Dies ist ein optionaler Dienst zum Abrufen der richtigen DbProviderFactory aus einem bestimmten DbConnection-Objekt. Die Standard Implementierung dieses diensdienstanbietern, der von EF für alle Anbieter zurückgegeben wird, ist für alle Anbieter vorgesehen. Bei der Ausführung unter .NET 4 ist die DbProviderFactory jedoch nicht öffentlich zugänglich, wenn die dbconnections-Verbindungen hergestellt werden. Daher verwendet EF einige Heuristik, um die registrierten Anbieter zu suchen, um eine Entsprechung zu finden. Es ist möglich, dass bei manchen Anbietern diese Heuristik fehlschlägt. in solchen Fällen sollte der Anbieter eine neue Implementierung bereitstellen.

## <a name="registering-dbproviderservices"></a>Registrieren von DbProviderServices

Die zu verwendende DbProviderServices-Implementierung kann entweder in der Konfigurationsdatei der Anwendung (app.config oder web.config) oder mithilfe der Code basierten Konfiguration registriert werden. In beiden Fällen wird bei der Registrierung der invariante Name des Anbieters als Schlüssel verwendet. Dadurch können mehrere Anbieter registriert und in einer einzigen Anwendung verwendet werden. Der für EF-Registrierungen verwendete invarianter Name ist identisch mit dem invarianten Namen, der für die ADO.NET-Anbieter Registrierung und Verbindungs Zeichenfolgen verwendet wird. Beispielsweise wird für SQL Server der invariante Name "System. Data. SqlClient" verwendet.

### <a name="config-file-registration"></a>Registrierung über die Konfigurationsdatei

Der zu verwendende DbProviderServices-Typ wird als Anbieter Element in der Anbieterliste des Abschnitts EntityFramework der Konfigurationsdatei der Anwendung registriert. Beispiel:

``` xml
<entityFramework>
  <providers>
    <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
  </providers>
</entityFramework>
```

Die _Typzeichenfolge_ muss der durch die Assembly qualifizierte Typname der zu verwendenden DbProviderServices-Implementierung sein.

### <a name="code-based-registration"></a>Codebasierte Registrierung

Beginnend mit EF6-Anbietern können auch mithilfe von Code registriert werden. Dadurch kann ein EF-Anbieter ohne Änderung an der Konfigurationsdatei der Anwendung verwendet werden. Um die Code basierte Konfiguration zu verwenden, sollte eine Anwendung eine dbconfiguration-Klasse erstellen, wie in der [Dokumentation zur Code basierten Konfiguration](https://msdn.com/data/jj680699)beschrieben. Der Konstruktor der dbconfiguration-Klasse sollte dann setproviderservices aufrufen, um den EF-Anbieter zu registrieren. Beispiel:

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetProviderServices("My.New.Provider", new MyProviderServices());
    }
}
```

## <a name="resolving-additional-services"></a>Auflösen zusätzlicher Dienste

Wie oben im Abschnitt _Übersicht über Anbieter Typen_ erwähnt, kann eine DbProviderServices-Klasse auch zum Auflösen zusätzlicher Dienste verwendet werden. Dies ist möglich, weil DbProviderServices idbdependencyresolver implementiert und jeder registrierte DbProviderServices-Typ als "Standard Konflikt Löser" hinzugefügt wird. Der idbdpdencyresolver-Mechanismus wird in der [Abhängigkeitsauflösung](xref:ef6/fundamentals/configuring/dependency-resolution)ausführlicher beschrieben. Es ist jedoch nicht erforderlich, alle Konzepte in dieser Spezifikation zu verstehen, um zusätzliche Dienste in einem Anbieter aufzulösen.

Die gängigste Methode für einen Anbieter, zusätzliche Dienste aufzulösen, besteht darin, DbProviderServices. adddependencyresolver für jeden Dienst im Konstruktor der DbProviderServices-Klasse aufzurufen. Sqlproviderservices (der EF-Anbieter für SQL Server) verfügt z. b. über Code, der diesem für die Initialisierung ähnelt:

``` csharp
private SqlProviderServices()
{
    AddDependencyResolver(new SingletonDependencyResolver<IDbConnectionFactory>(
        new SqlConnectionFactory()));

    AddDependencyResolver(new ExecutionStrategyResolver<DefaultSqlExecutionStrategy>(
        "System.data.SqlClient", null, () => new DefaultSqlExecutionStrategy()));

    AddDependencyResolver(new SingletonDependencyResolver<Func<MigrationSqlGenerator>>(
        () => new SqlServerMigrationSqlGenerator(), "System.data.SqlClient"));

    AddDependencyResolver(new SingletonDependencyResolver<DbSpatialServices>(
        SqlSpatialServices.Instance,
        k =>
        {
            var asSpatialKey = k as DbProviderInfo;
            return asSpatialKey == null
                || asSpatialKey.ProviderInvariantName == ProviderInvariantName;
        }));
}
```

Dieser Konstruktor verwendet die folgenden Hilfsklassen:

*   Singletondependencyresolver: bietet eine einfache Möglichkeit zum Auflösen von Singleton-Diensten – d. h. Dienste, für die bei jedem Aufruf von GetService dieselbe Instanz zurückgegeben wird. Vorübergehende Dienste werden häufig als Singleton-Factory registriert, die zum bedarfsgesteuerten Erstellen vorübergehender Instanzen verwendet werden.
*   Executionstrategyresolver: ein Konflikt Löser, der spezifisch für die Rückgabe von iexecutionstrategy-Implementierungen ist.

Anstatt DbProviderServices. adddependencyresolver zu verwenden, ist es auch möglich, DbProviderServices. GetService zu überschreiben und zusätzliche Dienste direkt aufzulösen. Diese Methode wird aufgerufen, wenn EF einen Dienst benötigt, der durch einen bestimmten Typ definiert ist, und in manchen Fällen für einen bestimmten Schlüssel. Die-Methode sollte den Dienst zurückgeben, wenn dies möglich ist, oder NULL zurückgeben, um die Rückgabe des Dienstanbieter zu beenden und stattdessen einer anderen Klasse zu gestatten, diese zu beheben. Um beispielsweise die standardverbindungsfactory aufzulösen, könnte der Code in GetService etwa wie folgt aussehen:

``` csharp
public override object GetService(Type type, object key)
{
    if (type == typeof(IDbConnectionFactory))
    {
        return new SqlConnectionFactory();
    }
    return null;
}
```

### <a name="registration-order"></a>Registrierungs Reihenfolge

Wenn mehrere DbProviderServices-Implementierungen in der Konfigurationsdatei einer Anwendung registriert werden, werden Sie als sekundäre Resolver in der Reihenfolge hinzugefügt, in der Sie aufgelistet sind. Da Konflikt Löser immer am Anfang der sekundären resolverkette hinzugefügt werden, bedeutet dies, dass der Anbieter am Ende der Liste die Möglichkeit erhält, Abhängigkeiten vor den anderen aufzulösen. (Dies mag zunächst ein wenig intuitiv sein, aber es ist sinnvoll, wenn Sie sich vorstellen, dass die einzelnen Anbieter aus der Liste herausgenommen werden und Sie auf den vorhandenen Anbietern stapeln.)

Diese Reihenfolge spielt in der Regel keine Rolle, da die meisten Anbieter Dienste Anbieter spezifisch sind und nach Anbieter invarianter Namen verschlüsselt sind. Für Dienste, die nicht durch einen invarianten Anbieter Namen oder einen anderen anbieterspezifischen Schlüssel verschlüsselt sind, wird der Dienst jedoch basierend auf dieser Reihenfolge aufgelöst. Wenn Sie z. b. nicht explizit anders festgelegt wird, wird die standardverbindungsfactory von dem obersten Anbieter in der Kette abgeleitet.

## <a name="additional-config-file-registrations"></a>Weitere Registrierungen von Konfigurationsdateien

Es ist möglich, einige der oben beschriebenen zusätzlichen Anbieter Dienste direkt in der Konfigurationsdatei einer Anwendung zu registrieren. Wenn dies erfolgt ist, wird die Registrierung in der Konfigurationsdatei anstelle der von der GetService-Methode der DbProviderServices-Implementierung zurückgegebenen Elemente verwendet.

### <a name="registering-the-default-connection-factory"></a>Registrieren der standardverbindungsfactory

Ab EF5 registriert das nuget-Paket "EntityFramework" automatisch entweder die SQL Express-Verbindungsfactory oder die localdb-Verbindungsfactory in der Konfigurationsdatei.

Beispiel:

``` xml
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework" >
</entityFramework>
```

Der _Typ_ ist der durch die Assembly qualifizierte Typname für die standardverbindungsfactory, die idbconnectionfactory implementieren muss.

Es wird empfohlen, dass das nuget-Paket eines Anbieters die standardverbindungsfactory bei der Installation auf diese Weise festgelegt. Siehe _nuget-Pakete für Anbieter_ weiter unten.

## <a name="additional-ef6-provider-changes"></a>Zusätzliche EF6-Anbieter Änderungen

### <a name="spatial-provider-changes"></a>Änderungen an räumlichen Anbietern

Anbieter, die räumliche Typen unterstützen, müssen jetzt einige zusätzliche Methoden für Klassen implementieren, die von dbspatialdatareader abgeleitet werden:

*   `public abstract bool IsGeographyColumn(int ordinal)`
*   `public abstract bool IsGeometryColumn(int ordinal)`

Es gibt auch neue asynchrone Versionen vorhandener Methoden, die überschrieben werden sollen, wenn der standardimplementierungs Delegat an die synchronen Methoden delegiert und daher nicht asynchron ausgeführt wird:

*   `public virtual Task<DbGeography> GetGeographyAsync(int ordinal, CancellationToken cancellationToken)`
*   `public virtual Task<DbGeometry> GetGeometryAsync(int ordinal, CancellationToken cancellationToken)`

### <a name="native-support-for-enumerablecontains"></a>Native Unterstützung für Enumerable. enthält

EF6 führt den neuen Ausdruckstyp dbinexpression ein, der hinzugefügt wurde, um Leistungsprobleme im Zusammenhang mit der Verwendung von Enumerable zu beheben. enthält in LINQ-Abfragen. Die DbProviderManifest-Klasse verfügt über die neue virtuelle Methode supportsinexpression, die von EF aufgerufen wird, um zu bestimmen, ob ein Anbieter den neuen Ausdruckstyp behandelt. Aus Kompatibilitätsgründen mit vorhandenen Anbieter Implementierungen gibt die Methode false zurück. Um von dieser Verbesserung profitieren zu können, kann ein EF6-Anbieter Code hinzufügen, der dbinexpression verarbeitet und supportsinexpression überschreibt, um true zurückzugeben. Eine Instanz von dbinexpression kann erstellt werden, indem die DbExpressionBuilder.in-Methode aufgerufen wird. Eine dbinexpression-Instanz besteht aus einem DbExpression, der in der Regel eine Tabellenspalte darstellt, und einer Liste von DbConstantExpression, die auf eine Entsprechung überprüft werden soll.

## <a name="nuget-packages-for-providers"></a>Nuget-Pakete für Anbieter

Eine Möglichkeit, einen EF6-Anbieter verfügbar zu machen, besteht darin, ihn als nuget-Paket freizugeben. Die Verwendung eines nuget-Pakets bietet folgende Vorteile:

*   Es ist einfach, nuget zu verwenden, um die Anbieter Registrierung zur Konfigurationsdatei der Anwendung hinzuzufügen.
*   Es können zusätzliche Änderungen an der Konfigurationsdatei vorgenommen werden, um die standardverbindungsfactory festzulegen, damit von der Konvention hergestellte Verbindungen der registrierte Anbieter verwendet werden.
*   Nuget übernimmt das Hinzufügen von Bindungs Umleitungen, sodass der EF6-Anbieter auch nach der Veröffentlichung eines neuen EF-Pakets weiterhin funktionieren sollte.

Ein Beispiel hierfür ist das Paket "EntityFramework. sqlservercompact", das in der [Open Source-Codebasis](https://github.com/aspnet/entityframework6)enthalten ist. Dieses Paket bietet eine gute Vorlage zum Erstellen von nuget-Paketen für EF-Anbieter.

### <a name="powershell-commands"></a>PowerShell-Befehle

Wenn das nuget-Paket "EntityFramework" installiert ist, wird ein PowerShell-Modul registriert, das zwei Befehle enthält, die für Anbieter Pakete sehr nützlich sind:

*   Add-efprovider fügt eine neue Entität für den Anbieter in der Konfigurationsdatei des Ziel Projekts hinzu und stellt sicher, dass Sie sich am Ende der Liste registrierter Anbieter befindet.
*   Add-efdefaultconnectionfactory fügt entweder die defaultconnectionfactory-Registrierung in der Konfigurationsdatei des Ziel Projekts hinzu oder aktualisiert sie.

Beide Befehle kümmern sich um die Hinzufügung eines EntityFramework-Abschnitts zur Konfigurationsdatei und das Hinzufügen einer Anbieter Auflistung, falls erforderlich.

Es ist vorgesehen, dass diese Befehle vom install.ps1 nuget-Skript aufgerufen werden. install.ps1 für den SQL Compact-Anbieter sieht z. b. wie folgt aus:

``` powershell
param($installPath, $toolsPath, $package, $project)
Add-EFDefaultConnectionFactory $project 'System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework' -ConstructorArguments 'System.Data.SqlServerCe.4.0'
Add-EFProvider $project 'System.Data.SqlServerCe.4.0' 'System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact'</pre>
```

Weitere Informationen zu diesen Befehlen finden Sie unter Verwenden von "Get-Help" im Fenster "Paket-Manager-Konsole".

## <a name="wrapping-providers"></a>Umwickeln von Anbietern

Ein Wrapping Anbieter ist ein EF-und/oder ADO.NET-Anbieter, der einen vorhandenen Anbieter umschließt, um ihn mit anderen Funktionen wie Profilerstellung oder Ablauf Verfolgungs Funktionen zu erweitern. Das Umbrechen von Anbietern kann auf normale Weise registriert werden. es ist jedoch oft bequemer, den Wrapping Anbieter zur Laufzeit zu erstellen, indem die Auflösung von Anbieter bezogenen Diensten abgefangen wird. Dazu kann das statische Ereignis onlockingconfiguration in der dbconfiguration-Klasse verwendet werden.

"Onlockingconfiguration" wird aufgerufen, nachdem EF festgelegt hat, wo alle EF-Konfigurationen für die APP-Domäne abgerufen werden, aber bevor Sie für die Verwendung gesperrt sind. Beim Starten der APP (vor der Verwendung von EF) sollte die APP einen Ereignishandler für dieses Ereignis registrieren. (Unterstützung für das Registrieren dieses Handlers in der Konfigurationsdatei wird in Erwägung gezogen, aber dies wird noch nicht unterstützt.) Der Ereignishandler sollte dann den replaceservice für jeden Dienst aufrufen, der umschließen werden muss.  

Um beispielsweise idbconnectionfactory und dbproviderservice zu wrappen, sollte ein Handler ähnlich dem folgenden registriert werden:

``` csharp
DbConfiguration.OnLockingConfiguration +=
    (_, a) =>
    {
        a.ReplaceService<DbProviderServices>(
            (s, k) => new MyWrappedProviderServices(s));

        a.ReplaceService<IDbConnectionFactory>(
            (s, k) => new MyWrappedConnectionFactory(s));
    };
```

Der Dienst, der aufgelöst wurde und nun mit dem Schlüssel umschließt werden soll, mit dem der Dienst aufgelöst wurde, wird an den-Handler übermittelt. Der Handler kann dann diesen Dienst umschließen und den zurückgegebenen Dienst durch die umschließende Version ersetzen.

## <a name="resolving-a-dbproviderfactory-with-ef"></a>Auflösen einer DbProviderFactory mit EF

DbProviderFactory ist einer der grundlegenden Anbieter Typen, der von EF benötigt wird, wie im Abschnitt Übersicht über die _Anbieter Typen_ beschrieben. Wie bereits erwähnt, handelt es sich nicht um einen EF-Typ, und die Registrierung ist normalerweise nicht Teil der EF-Konfiguration, sondern ist stattdessen die normale ADO.NET-Anbieter Registrierung in der machine.config Datei und/oder der Anwendungs Konfigurationsdatei.

Trotz dieses EF verwendet EF weiterhin seinen normalen Mechanismus zur Abhängigkeitsauflösung, wenn eine DbProviderFactory verwendet werden soll. Der Standard Konflikt Löser verwendet die normale ADO.net-Registrierung in den Konfigurationsdateien und ist daher normalerweise transparent. Aufgrund des normalen Mechanismus zur Abhängigkeitsauflösung bedeutet dies jedoch, dass ein idbdependencyresolver verwendet werden kann, um eine DbProviderFactory aufzulösen, auch wenn die normale ADO.net-Registrierung nicht durchgeführt wurde.

Das Auflösen von DbProviderFactory auf diese Weise hat mehrere Auswirkungen:

*   Eine Anwendung, die die Code basierte Konfiguration verwendet, kann Aufrufe in ihrer dbconfiguration-Klasse hinzufügen, um die entsprechende DbProviderFactory zu registrieren. Dies ist besonders nützlich für Anwendungen, die keine dateibasierte Konfiguration verwenden möchten (oder nicht).
*   Der Dienst kann mithilfe von replaceservice umschließt oder ersetzt werden, wie im Abschnitt _Wrapping Anbieter_ beschrieben.
*   Theoretisch könnte eine DbProviderServices-Implementierung eine DbProviderFactory auflösen.

Beachten Sie, dass sich diese Dinge unbedingt auf die Suche von DbProviderFactory durch EF auswirken. Anderer nicht-EF-Code erwartet möglicherweise weiterhin, dass der ADO.NET-Anbieter auf normale Weise registriert wird, und schlägt möglicherweise fehl, wenn die Registrierung nicht gefunden wurde. Aus diesem Grund ist es normalerweise besser, eine DbProviderFactory auf normale Weise zu registrieren ADO.net.

### <a name="related-services"></a>Verknüpfte Dienste

Wenn EF verwendet wird, um eine DbProviderFactory aufzulösen, sollte Sie auch die iproviderinvariantname-und idbproviderfactor yresolver-Dienste auflösen.

Iproviderinvariantname ist ein Dienst, der verwendet wird, um einen invarianten Anbieter Namen für einen bestimmten Typ von DbProviderFactory zu ermitteln. Die Standard Implementierung dieses Dienstanbieters verwendet die ADO.NET-Anbieter Registrierung. Dies bedeutet Folgendes: Wenn der ADO.NET-Anbieter nicht auf die normale Weise registriert ist, weil DbProviderFactory von EF aufgelöst wird, muss dieser Dienst ebenfalls aufgelöst werden. Beachten Sie, dass ein Konflikt Löser für diesen Dienst automatisch hinzugefügt wird, wenn die dbconfiguration. setproviderfactory-Methode verwendet wird.

Wie im Abschnitt Übersicht über die _Anbieter Typen_ beschrieben, wird der idbproviderfactoryresolver verwendet, um die korrekte DbProviderFactory aus einem bestimmten DbConnection-Objekt abzurufen. Die Standard Implementierung dieses Dienstanbieters bei Ausführung auf .NET 4 verwendet die ADO.NET-Anbieter Registrierung. Dies bedeutet Folgendes: Wenn der ADO.NET-Anbieter nicht auf die normale Weise registriert ist, weil DbProviderFactory von EF aufgelöst wird, muss dieser Dienst ebenfalls aufgelöst werden.
