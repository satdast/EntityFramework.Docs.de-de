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
# <a name="code-based-configuration"></a>Code basierte Konfiguration
> [!NOTE]
> **Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.  

Die Konfiguration für eine Entity Framework Anwendung kann in einer Konfigurationsdatei (app.config/web.config) oder über Code angegeben werden. Letzteres wird als Code basierte Konfiguration bezeichnet.  

Die Konfiguration in einer Konfigurationsdatei wird in einem [separaten Artikel](xref:ef6/fundamentals/configuring/config-file)beschrieben. Die Konfigurationsdatei hat Vorrang vor der Code basierten Konfiguration. Anders ausgedrückt: Wenn eine Konfigurationsoption sowohl im Code als auch in der Konfigurationsdatei festgelegt wird, wird die Einstellung in der Konfigurationsdatei verwendet.  

## <a name="using-dbconfiguration"></a>Verwenden von dbconfiguration  

Die Code basierte Konfiguration in EF6 und höher wird durch Erstellen einer Unterklasse von System.Data.Entity.Config erreicht. DbConfiguration. Beachten Sie die folgenden Richtlinien, wenn Sie dbconfiguration Unterklassen:  

- Erstellen Sie nur eine dbconfiguration-Klasse für Ihre Anwendung. Diese Klasse gibt App-Domänen weite Einstellungen an.  
- Platzieren Sie die dbconfiguration-Klasse in der gleichen Assembly wie die dbcontext-Klasse. (Weitere Informationen finden Sie im Abschnitt *Verschieben von dbconfiguration* .)  
- Übergeben Sie der dbconfiguration-Klasse einen öffentlichen Parameter losen Konstruktor.  
- Legen Sie Konfigurationsoptionen fest, indem Sie in diesem Konstruktor geschützte dbconfiguration-Methoden aufrufen.  

Wenn Sie diese Richtlinien befolgen, können Sie Ihre Konfiguration automatisch von den Tools ermitteln und verwenden, die auf Ihr Modell zugreifen müssen, und wenn die Anwendung ausgeführt wird.  

## <a name="example"></a>Beispiel  

Eine von dbconfiguration abgeleitete Klasse könnte wie folgt aussehen:  

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

Diese Klasse richtet EF so ein, dass die SQL Azure Ausführungs Strategie verwendet wird, um fehlerhafte Daten Bank Vorgänge automatisch zu wiederholen, und um lokale DB für Datenbanken zu verwenden, die gemäß der Konvention aus Code First erstellt werden.  

## <a name="moving-dbconfiguration"></a>Verschieben von dbconfiguration  

Es gibt Fälle, in denen es nicht möglich ist, die dbconfiguration-Klasse in derselben Assembly wie die dbcontext-Klasse zu platzieren. Beispielsweise können Sie über zwei dbcontext-Klassen verfügen, die jeweils in verschiedenen Assemblys enthalten sind. Hierfür gibt es zwei Möglichkeiten.  

Die erste Option besteht darin, die Konfigurationsdatei zu verwenden, um die zu verwendende dbconfiguration-Instanz anzugeben. Legen Sie zu diesem Zweck das codeconfigurationtype-Attribut des Abschnitts "EntityFramework" fest. Beispiel:  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

Der Wert von codeconfigurationtype muss die Assembly und der Namespace qualifizierte Name der dbconfiguration-Klasse sein.  

Die zweite Option besteht darin, dbconfigurationtypeattribute für Ihre Kontext Klasse zu platzieren. Beispiel:  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

Der an das Attribut weiter gegebene Wert kann entweder Ihr dbconfiguration-Typ sein, wie oben gezeigt, oder die Zeichenfolge der qualifizierten Assembly-und Namespace-Typnamen. Beispiel:  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a>Explizites Festlegen von dbconfiguration  

Es gibt einige Situationen, in denen die Konfiguration möglicherweise erforderlich ist, bevor ein dbcontext-Typ verwendet wird. Beispiele hierfür sind:  

- Verwenden von dbmodelbuilder zum Erstellen eines Modells ohne Kontext  
- Verwenden eines anderen Frameworks-/Utility-Codes, der einen dbcontext verwendet, in dem dieser Kontext verwendet wird, bevor der Anwendungskontext verwendet wird  

In solchen Situationen kann EF die Konfiguration nicht automatisch ermitteln, und Sie müssen stattdessen eine der folgenden Aktionen ausführen:  

- Legen Sie den dbconfiguration-Typ in der Konfigurationsdatei fest, wie im Abschnitt *Verschieben von dbconfiguration* weiter oben beschrieben.
- Aufrufen der statischen dbconfiguration. SetConfiguration-Methode beim Starten der Anwendung  

## <a name="overriding-dbconfiguration"></a>Überschreiben von dbconfiguration  

Es gibt einige Situationen, in denen Sie den Konfigurationssatz in dbconfiguration überschreiben müssen. Dies erfolgt in der Regel nicht von Anwendungsentwicklern, sondern von Drittanbietern und Plug-ins, die keine abgeleitete dbconfiguration-Klasse verwenden können.  

Hierfür ermöglicht EntityFramework das Registrieren eines Ereignis Handlers, der die vorhandene Konfiguration ändern kann, kurz bevor Sie gesperrt wird.  Außerdem bietet es eine Sugar-Methode, mit der alle vom EF-Dienstlocator zurückgegebenen Dienste ersetzt werden. So ist die Verwendung vorgesehen:  

- Beim Starten der APP (vor der Verwendung von EF) sollte das Plug-in oder der Anbieter die Ereignishandlermethode für dieses Ereignis registrieren. (Beachten Sie, dass dies geschehen muss, bevor die Anwendung EF verwendet.)  
- Der Ereignishandler ruft replaceservice für jeden Dienst auf, der ersetzt werden muss.  

Wenn Sie z. b. idbconnectionfactory und dbproviderservice ersetzen möchten, registrieren Sie einen Handler in etwa wie folgt:  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

Im obigen Code stellen myproviderservices und myconnectionfactory Ihre Implementierungen des Diensts dar.  

Sie können auch zusätzliche Abhängigkeits Handler hinzufügen, um denselben Effekt zu erzielen.  

Beachten Sie, dass Sie DbProviderFactory auch auf diese Weise einbinden können. Dies wirkt sich jedoch nur auf EF und nicht auf die Verwendung von DbProviderFactory außerhalb von EF aus. Aus diesem Grund ist es wahrscheinlich, dass Sie DbProviderFactory weiterhin wie zuvor verpacken.  

Berücksichtigen Sie auch die Dienste, die Sie extern für Ihre Anwendung ausführen, z. b. bei der Ausführung von Migrationen über die Paket-Manager-Konsole. Wenn Sie Migrieren von der-Konsole ausführen, wird versucht, die dbconfiguration zu finden. Ob der umschließende Dienst erhalten bleibt, hängt jedoch davon ab, wo der Ereignishandler registriert ist. Wenn Sie im Rahmen der Erstellung von dbconfiguration registriert ist, sollte der Code ausgeführt werden, und der Dienst sollte umschließt werden. Normalerweise ist dies nicht der Fall, und das bedeutet, dass Tools den umschließenden Dienst nicht erhalten.  
