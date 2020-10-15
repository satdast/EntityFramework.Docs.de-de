---
title: Verbindungs Zeichenfolgen und Modelle EF6
description: Verbindungs Zeichenfolgen und Modelle in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/connection-strings
ms.openlocfilehash: c60acb965b7062f3cd35dab94ee8dfe48394969a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063386"
---
# <a name="connection-strings-and-models"></a>Verbindungs Zeichenfolgen und Modelle
In diesem Thema wird erläutert, wie Entity Framework ermittelt, welche Datenbankverbindung verwendet werden soll, und wie Sie Sie ändern können. Die Modelle, die mit Code First und dem EF-Designer erstellt wurden, werden in diesem Thema behandelt.  

In der Regel verwendet eine Entity Framework Anwendung eine von dbcontext abgeleitete Klasse. Diese abgeleitete Klasse ruft einen der Konstruktoren für die Basisklasse "dbcontext" auf, um Folgendes zu steuern:  

- Wie der Kontext eine Verbindung mit einer Datenbank herstellt – d. h., wie eine Verbindungs Zeichenfolge gefunden bzw. verwendet wird  
- Ob der Kontext das Berechnen eines Modells mithilfe von Code First oder das Laden eines Modells verwendet, das mit dem EF-Designer erstellt wurde  
- Weitere Erweiterte Optionen  

Die folgenden Fragmente zeigen einige Möglichkeiten, wie die dbcontext-Konstruktoren verwendet werden können.  

## <a name="use-code-first-with-connection-by-convention"></a>Code First mit Verbindung nach Konvention verwenden  

Wenn Sie in der Anwendung keine andere Konfiguration ausgeführt haben, führt das Aufrufen des Parameter losen Konstruktors in dbcontext dazu, dass dbcontext im Code First Modus mit einer Datenbankverbindung ausgeführt wird, die von der Konvention erstellt wurde. Beispiel:  

``` csharp  
namespace Demo.EF
{
    public class BloggingContext : DbContext
    {
        public BloggingContext()
        // C# will call base class parameterless constructor by default
        {
        }
    }
}
```  

In diesem Beispiel verwendet dbcontext den qualifizierten Namespace Namen ihrer abgeleiteten Kontext Klasse – Demo. EF. bloggingcontext – als Datenbanknamen und erstellt eine Verbindungs Zeichenfolge für diese Datenbank mithilfe von SQL Express oder localdb. Wenn beides installiert ist, wird SQL Express verwendet.  

Visual Studio 2010 enthält standardmäßig SQL Express, und Visual Studio 2012 und höher enthalten localdb. Während der Installation überprüft das nuget-Paket "EntityFramework", welcher Datenbankserver verfügbar ist. Das nuget-Paket aktualisiert dann die Konfigurationsdatei, indem der von Code First verwendete Standarddaten Bankserver beim Erstellen einer Verbindung per Konvention festgelegt wird. Wenn SQL Express ausgeführt wird, wird es verwendet. Wenn SQL Express nicht verfügbar ist, wird localdb stattdessen als Standard registriert. An der Konfigurationsdatei werden keine Änderungen vorgenommen, wenn Sie bereits eine Einstellung für die standardverbindungsfactory enthält.  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a>Code First mit Verbindung gemäß Konvention und angegebenem Datenbanknamen verwenden  

Wenn Sie in der Anwendung keine andere Konfiguration ausgeführt haben, führt der Aufruf des zeichenfolgenkonstruktors in dbcontext mit dem Datenbanknamen, den Sie verwenden möchten, dazu, dass dbcontext in Code First Modus mit einer Datenbankverbindung ausgeführt wird, die in der Datenbank mit diesem Namen erstellt wurde. Beispiel:  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

In diesem Beispiel verwendet dbcontext "bloggingdatabase" als Datenbanknamen und erstellt eine Verbindungs Zeichenfolge für diese Datenbank entweder mithilfe von SQL Express (installiert mit Visual Studio 2010) oder localdb (installiert mit Visual Studio 2012). Wenn beides installiert ist, wird SQL Express verwendet.  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a>Verwenden von Code First mit Verbindungs Zeichenfolge in app.config/web.config-Datei  

Sie können eine Verbindungs Zeichenfolge in ihrer app.config oder web.config Datei platzieren. Beispiel:  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

Dies ist eine einfache Möglichkeit, dbcontext anzuweisen, einen anderen Datenbankserver als SQL Express oder localdb zu verwenden – das obige Beispiel gibt eine SQL Server Compact Edition-Datenbank an.  

Wenn der Name der Verbindungs Zeichenfolge mit dem Namen Ihres Kontexts übereinstimmt (entweder mit oder ohne Namespace Qualifizierung), wird er von dbcontext gefunden, wenn der Parameter lose Konstruktor verwendet wird. Wenn sich der Name der Verbindungs Zeichenfolge vom Namen Ihres Kontexts unterscheidet, können Sie dbcontext anweisen, diese Verbindung im Code First Modus zu verwenden, indem Sie den Namen der Verbindungs Zeichenfolge an den dbcontext-Konstruktor übergeben. Beispiel:  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

Alternativ können Sie das Format "Name = \<connection string name\> " für die Zeichenfolge verwenden, die an den dbcontext-Konstruktor übergeben wird. Beispiel:  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

Dieses Formular macht es explizit, dass die Verbindungs Zeichenfolge in der Konfigurationsdatei zu finden ist. Eine Ausnahme wird ausgelöst, wenn keine Verbindungs Zeichenfolge mit dem angegebenen Namen gefunden wird.  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a>Datenbank/Model First mit Verbindungs Zeichenfolge in app.config/web.config-Datei  

Modelle, die mit dem EF-Designer erstellt wurden, unterscheiden sich von Code First darin, dass das Modell bereits vorhanden ist und nicht aus Code generiert wird, wenn die Anwendung ausgeführt wird Das Modell ist in der Regel als EDMX-Datei in Ihrem Projekt vorhanden.  

Der Designer fügt eine EF-Verbindungs Zeichenfolge zu ihrer app.config oder web.config Datei hinzu. Diese Verbindungs Zeichenfolge ist insofern speziell, als Sie Informationen darüber enthält, wie Sie die Informationen in der EDMX-Datei finden. Beispiel:  

``` xml  
<configuration>  
  <connectionStrings>  
    <add name="Northwind_Entities"  
         connectionString="metadata=res://*/Northwind.csdl|  
                                    res://*/Northwind.ssdl|  
                                    res://*/Northwind.msl;  
                           provider=System.Data.SqlClient;  
                           provider connection string=  
                               &quot;Data Source=.\sqlexpress;  
                                     Initial Catalog=Northwind;  
                                     Integrated Security=True;  
                                     MultipleActiveResultSets=True&quot;"  
         providerName="System.Data.EntityClient"/>  
  </connectionStrings>  
</configuration>
```  

Der EF-Designer generiert außerdem Code, der dbcontext mitteilt, dass diese Verbindung verwendet werden soll, indem der Name der Verbindungs Zeichenfolge an den dbcontext-Konstruktor übergeben wird. Beispiel:  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

Dbcontext weiß, dass das vorhandene Modell geladen werden soll (anstatt Code First zu verwenden, um es aus Code zu berechnen), da die Verbindungs Zeichenfolge eine EF-Verbindungs Zeichenfolge mit Details des zu verwendenden Modells ist.  

## <a name="other-dbcontext-constructor-options"></a>Weitere dbcontext-konstruktoroptionen  

Die dbcontext-Klasse enthält andere Konstruktoren und Verwendungs Muster, die einige erweiterte Szenarien ermöglichen. Einige davon sind:  

- Sie können die dbmodelbuilder-Klasse verwenden, um ein Code First Modell zu erstellen, ohne eine dbcontext-Instanz zu instanziieren. Das Ergebnis dieses Objekts ist ein dbmodel-Objekt. Sie können dieses dbmodel-Objekt dann an einen der dbcontext-Konstruktoren übergeben, wenn Sie bereit sind, die dbcontext-Instanz zu erstellen.  
- Sie können eine vollständige Verbindungs Zeichenfolge an dbcontext übergeben, anstatt nur den Namen der Datenbank oder der Verbindungs Zeichenfolge. Diese Verbindungs Zeichenfolge wird standardmäßig mit dem System. Data. SqlClient-Anbieter verwendet. Dies kann geändert werden, indem eine andere iconnectionfactory-Implementierung auf den Kontext festgelegt wird. Database. defaultconnectionfactory.  
- Sie können ein vorhandenes DbConnection-Objekt verwenden, indem Sie es an einen dbcontext-Konstruktor übergeben. Wenn das Verbindungs Objekt eine Instanz von EntityConnection ist, wird das in der Verbindung angegebene Modell verwendet, anstatt ein Modell mithilfe von Code First zu berechnen. Wenn das Objekt eine Instanz eines anderen Typs ist – z. b. SqlConnection –, wird der Kontext ihn für Code First Modus verwenden.  
- Sie können einen vorhandenen ObjectContext an einen dbcontext-Konstruktor übergeben, um einen dbcontext zu erstellen, der den vorhandenen Kontext umwickelt. Dies kann für vorhandene Anwendungen verwendet werden, die ObjectContext verwenden, aber in einigen Teilen der Anwendung dbcontext nutzen möchten.  
