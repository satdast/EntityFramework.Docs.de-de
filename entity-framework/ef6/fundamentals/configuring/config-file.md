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
# <a name="configuration-file-settings"></a>Konfigurationsdatei Einstellungen
Entity Framework ermöglicht, dass eine Reihe von Einstellungen in der Konfigurationsdatei angegeben werden können. Im allgemeinen folgt EF einem Prinzip der Konvention für die Konfiguration: alle in diesem Beitrag behandelten Einstellungen haben ein Standardverhalten. Sie müssen sich lediglich um das Ändern der Einstellung kümmern, wenn die Standardeinstellung Ihre Anforderungen nicht mehr erfüllt.  

## <a name="a-code-based-alternative"></a>Eine Code basierte Alternative  

Alle diese Einstellungen können auch mithilfe von Code angewendet werden. Ab EF6 haben wir eine [Code basierte Konfiguration](xref:ef6/fundamentals/configuring/code-based)eingeführt, die eine zentrale Methode zum Anwenden der Konfiguration aus dem Code bereitstellt. Vor EF6 kann die Konfiguration weiterhin aus dem Code übernommen werden, Sie müssen jedoch verschiedene APIs verwenden, um verschiedene Bereiche zu konfigurieren. Mithilfe der Option Konfigurationsdatei können diese Einstellungen während der Bereitstellung problemlos geändert werden, ohne den Code zu aktualisieren.

## <a name="the-entity-framework-configuration-section"></a>Der Abschnitt zur Entity Framework Konfiguration  

Ab EF 4.1 können Sie den datenbankinitialisierer für einen Kontext mithilfe des Abschnitts **appSettings** der Konfigurationsdatei festlegen. In EF 4,3 haben wir den benutzerdefinierten Abschnitt " **EntityFramework** " eingeführt, mit dem die neuen Einstellungen behandelt werden. Entity Framework erkennt weiterhin datenbankinitialisierer, die im alten Format festgelegt sind. es wird jedoch empfohlen, nach Möglichkeit in das neue Format zu wechseln.

Wenn Sie das nuget-Paket "EntityFramework" installiert haben, wurde der Abschnitt " **EntityFramework** " automatisch zur Konfigurationsdatei Ihres Projekts hinzugefügt.  

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

## <a name="connection-strings"></a>Verbindungszeichenfolgen  

Auf dieser Seite finden Sie weitere Details dazu, wie Entity Framework die zu [verwendende](xref:ef6/fundamentals/configuring/connection-strings) Datenbank bestimmt, einschließlich der Verbindungs Zeichenfolgen in der Konfigurationsdatei.  

Verbindungs Zeichenfolgen werden im standardmäßigen **connectionStrings** -Element angezeigt und benötigen nicht den Abschnitt " **EntityFramework** ".  

Code First basierten Modellen verwenden normale ADO.NET-Verbindungs Zeichenfolgen. Beispiel:  

``` xml
<connectionStrings>
  <add name="BlogContext"  
        providerName="System.Data.SqlClient"  
        connectionString="Server=.\SQLEXPRESS;Database=Blogging;Integrated Security=True;"/>
</connectionStrings>
```  

EF-Designer-basierte Modelle verwenden spezielle EF-Verbindungs Zeichenfolgen. Beispiel:  

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

## <a name="code-based-configuration-type-ef6-onwards"></a>Code basierter Konfigurationstyp (EF6 oder höher)  

Ab EF6 können Sie die dbconfiguration für EF angeben, die für die [Code basierte Konfiguration](xref:ef6/fundamentals/configuring/code-based) in der Anwendung verwendet werden soll. In den meisten Fällen müssen Sie diese Einstellung nicht angeben, da EF ihre dbconfiguration automatisch erkennt. Ausführliche Informationen dazu, wann Sie dbconfiguration in der Konfigurationsdatei angeben müssen, finden Sie im Abschnitt **Verschieben von dbconfiguration** der [Code basierten Konfiguration](xref:ef6/fundamentals/configuring/code-based).  

Um einen dbconfiguration-Typ festzulegen, geben Sie den qualifizierten Assemblynamen im **codeconfigurationtype** -Element an.  

> [!NOTE]
> Ein qualifizierter Assemblyname ist der qualifizierte Namespace Name, gefolgt von einem Komma, und dann die Assembly, in der sich der Typ befindet. Optional können Sie auch die Assemblyversion, die Kultur und das öffentliche Schlüssel Token angeben.  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyConfiguration, MyAssembly">
</entityFramework>
```  

## <a name="ef-database-providers-ef6-onwards"></a>EF-Datenbankanbieter (EF6 oder höher)  

Vor EF6 mussten Entity Framework spezifische Teile eines Datenbankanbieters als Teil des Core ADO.NET-Anbieters eingeschlossen werden. Ab EF6 werden die EF-spezifischen Teile nun separat verwaltet und registriert.  

Normalerweise müssen Sie Anbieter nicht selbst registrieren. Dies wird in der Regel vom Anbieter durchgeführt, wenn Sie ihn installieren.  

Anbieter werden durch Einschließen eines **Anbieter** -Elements unter dem untergeordneten Abschnitt " **Providers** " im Abschnitt " **EntityFramework** " registriert. Es gibt zwei erforderliche Attribute für einen Anbieter Eintrag:  

- **InvariantName** identifiziert den Kern ADO.NET-Anbieter, den dieser EF-Anbieter als Ziel hat.  
- **Type** ist der durch die Assembly qualifizierte Typname der EF-Anbieter Implementierung.  

> [!NOTE]
> Ein qualifizierter Assemblyname ist der qualifizierte Namespace Name, gefolgt von einem Komma, und dann die Assembly, in der sich der Typ befindet. Optional können Sie auch die Assemblyversion, die Kultur und das öffentliche Schlüssel Token angeben.  

Ein Beispiel hierfür ist der Eintrag, der erstellt wird, um beim Installieren von Entity Framework den Standard SQL Server-Anbieter zu registrieren.  

``` xml  
<providers>
  <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
</providers>
```  

## <a name="interceptors-ef61-onwards"></a>Interceptors (ab EF 6.1)  

Ab EF 6.1 können Sie Interceptors in der Konfigurationsdatei registrieren. Interceptors ermöglichen es Ihnen, zusätzliche Logik auszuführen, wenn EF bestimmte Vorgänge ausführt, z. b. das Ausführen von Datenbankabfragen, das Öffnen von Verbindungen usw.  

Interceptors werden durch Einschließen eines **Interceptor** Elements unter dem untergeordneten Abschnitt **Interceptors** des Abschnitts **EntityFramework** registriert. Mit der folgenden Konfiguration wird z. b. der integrierte **databaselogger** -Interceptor registriert, der alle Daten Bank Vorgänge in der Konsole protokolliert.  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework"/>
</interceptors>
```  

### <a name="logging-database-operations-to-a-file-ef61-onwards"></a>Protokollieren von Daten Bank Vorgängen in einer Datei (ab EF 6.1)  

Das Registrieren von Interceptors über die Konfigurationsdatei ist besonders nützlich, wenn Sie eine Protokollierung zu einer vorhandenen Anwendung hinzufügen möchten, um ein Problem zu beheben. **Databaselogger** unterstützt die Protokollierung in einer Datei, indem der Dateiname als Konstruktorparameter angegeben wird.  

``` xml  
<interceptors>
  <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
    <parameters>
      <parameter value="C:\Temp\LogOutput.txt"/>
    </parameters>
  </interceptor>
</interceptors>
```  

Standardmäßig bewirkt dies, dass die Protokolldatei bei jedem Start der APP mit einer neuen Datei überschrieben wird. Wenn Sie stattdessen an die Protokolldatei anfügen möchten, wenn Sie bereits vorhanden ist, verwenden Sie Folgendes:  

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

Weitere Informationen zu **databaselogger** und zum Registrieren von Interceptors finden Sie im Blogbeitrag [EF 6,1: Aktivieren der Protokollierung ohne](https://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/)erneutes Kompilieren.  

## <a name="code-first-default-connection-factory"></a>Standardverbindungsfactory Code First  

Im Konfigurations Abschnitt können Sie eine standardverbindungsfactory angeben, die Code First verwenden soll, um eine Datenbank zu suchen, die für einen Kontext verwendet werden soll. Die standardverbindungsfactory wird nur verwendet, wenn der Konfigurationsdatei für einen Kontext keine Verbindungs Zeichenfolge hinzugefügt wurde.  

Wenn Sie das EF-nuget-Paket installiert haben, wurde eine standardverbindungsfactory registriert, die auf SQL Express oder localdb zeigt, je nachdem, welche installiert wurde.  

Um eine Verbindungsfactory festzulegen, geben Sie den qualifizierten Assemblynamen im **defaultconnectionfactory** -Element an.  

> [!NOTE]
> Ein qualifizierter Assemblyname ist der qualifizierte Namespace Name, gefolgt von einem Komma, und dann die Assembly, in der sich der Typ befindet. Optional können Sie auch die Assemblyversion, die Kultur und das öffentliche Schlüssel Token angeben.  

Hier ist ein Beispiel für das Festlegen einer eigenen standardverbindungsfactory:  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="MyNamespace.MyCustomFactory, MyAssembly"/>
</entityFramework>
```  

Im obigen Beispiel ist es erforderlich, dass die benutzerdefinierte Factory einen Parameter losen Konstruktor hat. Bei Bedarf können Sie mit dem **Parameters** -Element Konstruktorparameter angeben.  

Beispielsweise müssen Sie für das sqlceconnectionfactory-Element, das in Entity Framework enthalten ist, den invarianten Namen eines Anbieters für den Konstruktor angeben. Der invariante Name des Anbieters identifiziert die Version von SQL Compact, die Sie verwenden möchten. Die folgende Konfiguration bewirkt, dass Kontexte standardmäßig die SQL Compact-Version 4,0 verwenden.  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlCeConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="System.Data.SqlServerCe.4.0" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

Wenn Sie keine standardverbindungsfactory festlegen, verwendet Code First die sqlconnectionfactory, die auf verweist `.\SQLEXPRESS` . Sqlconnectionfactory verfügt auch über einen Konstruktor, mit dem Sie Teile der Verbindungs Zeichenfolge überschreiben können. Wenn Sie eine andere SQL Server-Instanz als verwenden möchten `.\SQLEXPRESS` , können Sie diesen Konstruktor verwenden, um den Server festzulegen.  

Die folgende Konfiguration bewirkt, dass Code First **mydatabaseserver** für Kontexte verwendet, für die keine explizite Verbindungs Zeichenfolge festgelegt ist.  

``` xml  
<entityFramework>
  <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework">
    <parameters>
      <parameter value="Data Source=MyDatabaseServer; Integrated Security=True; MultipleActiveResultSets=True" />
    </parameters>
  </defaultConnectionFactory>
</entityFramework>
```  

Standardmäßig wird davon ausgegangen, dass Konstruktorargumente den Typ "String" haben. Sie können das Type-Attribut verwenden, um dies zu ändern.  

``` xml
<parameter value="2" type="System.Int32" />
```  

## <a name="database-initializers"></a>Datenbankinitialisierer  

Datenbankinitialisierer werden pro Kontext konfiguriert. Sie können mithilfe des **context** -Elements in der Konfigurationsdatei festgelegt werden. Dieses Element verwendet den qualifizierten Assemblynamen, um den Kontext zu identifizieren, der konfiguriert wird.  

Standardmäßig sind Code First Kontexte so konfiguriert, dass Sie den Initialisierer "Initialisierer" von "kreatedatabaseifnotexists" verwenden Es gibt ein **disabledatabaseinitialization** -Attribut für das **Kontext** Element, das zum Deaktivieren der Daten Bank Initialisierung verwendet werden kann.  

Die folgende Konfiguration deaktiviert beispielsweise die Daten Bank Initialisierung für den in MyAssembly.dll definierten Blog Kontext Kontext.  

``` xml  
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly" disableDatabaseInitialization="true" />
</contexts>
```  

Sie können das **databaseinitializer** -Element verwenden, um einen benutzerdefinierten Initialisierer festzulegen.  

``` xml
<contexts>
  <context type=" Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="Blogging.MyCustomBlogInitializer, MyAssembly" />
  </context>
</contexts>
```  

Konstruktorparameter verwenden dieselbe Syntax wie standardverbindungsfactorys.  

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

Sie können einen der generischen datenbankinitialisierer konfigurieren, die in Entity Framework enthalten sind. Das **Type** -Attribut verwendet das .NET Framework-Format für generische Typen.  

Wenn Sie z. b. Code First-Migrationen verwenden, können Sie die Datenbank so konfigurieren, dass Sie automatisch mit dem `MigrateDatabaseToLatestVersion<TContext, TMigrationsConfiguration>` Initialisierer migriert wird.  

``` xml
<contexts>
  <context type="Blogging.BlogContext, MyAssembly">
    <databaseInitializer type="System.Data.Entity.MigrateDatabaseToLatestVersion`2[[Blogging.BlogContext, MyAssembly], [Blogging.Migrations.Configuration, MyAssembly]], EntityFramework" />
  </context>
</contexts>
```
