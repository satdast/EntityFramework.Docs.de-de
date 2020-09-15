---
title: 'Entity Framework-Anbieter: EF6'
description: Entity Framework-Anbieter in Entity Framework 6
author: divega
ms.date: 06/27/2018
uid: ef6/fundamentals/providers/index
ms.openlocfilehash: bb573cb0666556de8fb37802f465af97fc40673a
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073148"
---
# <a name="entity-framework-6-providers"></a>Anbieter von Entity Framework 6
> [!NOTE]
> **Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.

Das Entity Framework wird nun unter einer Open Source-Lizenz entwickelt, und EF6 und höher werden nicht als Teil von .NET Framework zur Verfügung gestellt. Dies bringt viele Vorteile mit sich, erfordert jedoch auch, dass EF-Anbieter für die EF6-Assemblys neu erstellt werden. Das heißt, dass EF-Anbieter für EF5 und darunter nicht mit EF6 funktionieren, es sei denn, sie wurden neu erstellt.

## <a name="which-providers-are-available-for-ef6"></a>Welche Anbieter sind für EF6 verfügbar?

Anbieter, von denen wir wissen, dass Sie für EF6 neu erstellt wurden:

*   **Microsoft SQL Server-Anbieter**
    *   Erstellt aus der [Open Sources-Codebasis des Entity Framework](https://github.com/aspnet/EntityFramework6)
    *   Geliefert als Teil des [EntityFramework NuGet-Pakets](https://nuget.org/packages/EntityFramework)
*   **Microsoft SQL Server Compact Edition-Anbieter**
    *   Erstellt aus der [Open Sources-Codebasis des Entity Framework](https://github.com/aspnet/EntityFramework6)
    *   Im Lieferumfang des [EntityFramework.SqlServerCompact NuGet-Pakets](https://nuget.org/packages/EntityFramework.SqlServerCompact)
*   [**Devart dotConnect-Datenanbieter**](https://www.devart.com/dotconnect/)
    *   Es gibt Anbieter von Drittanbietern von [Devart](https://www.devart.com/) für eine Vielzahl von Datenbanken, einschließlich Oracle, MySQL, PostgreSQL, SQLite, Salesforce, DB2 und SQL Server.
*   [**CData Software-Anbieter**](https://www.cdata.com/ado/)
    *   Es gibt Anbieter von Drittanbietern von [CData Software](https://www.cdata.com/ado/) für eine Vielzahl von Datenspeichern, einschließlich Salesforce, Azure Table Storage, MySql und viele mehr.
*   **Firebird-Anbieter**
    *   Verfügbar als [NuGet-Paket](https://www.nuget.org/packages/EntityFramework.Firebird/)
*   **Visual FoxPro-Anbieter**
    *   Verfügbar als [NuGet-Paket](https://www.nuget.org/packages/VFPEntityFrameworkProvider2/)
*   **MySQL**
    *   [MySQL-Connector/.NET für Entity Framework](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework60.html)
*   **PostgreSQL**
    *   Npgsql ist als [NuGet-Paket](https://www.nuget.org/packages/EntityFramework6.Npgsql/) verfügbar.
*   **Oracle**
    *   ODP.NET ist als [NuGet-Paket](https://www.nuget.org/packages/Oracle.ManagedDataAccess.EntityFramework/) verfügbar.

Beachten Sie, dass eine Aufnahme in die Liste keine Auskunft über die Ebene der Funktion oder Unterstützung für einen angegebenen Anbieter gibt, sondern nur darüber, dass ein Build für EF6 nun verfügbar ist.

## <a name="registering-ef-providers"></a>Registrieren von EF-Anbietern

Ab Entity Framework 6 können EF-Anbieter über die codebasierte Konfiguration oder die Konfigurationsdatei der Anwendung registriert werden.

### <a name="config-file-registration"></a>Registrierung über die Konfigurationsdatei

Die Registrierung des EF-Anbieters in „app.config“ oder „web.config“ besitzt folgendes Format:


``` xml
    <entityFramework>
       <providers>
         <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
       </providers>
    </entityFramework>
```

Beachten Sie, dass es oft vorkommt, dass wenn der EF-Anbieter von NuGet installiert wird, das NuGet-Paket diese Registrierung automatisch der Konfigurationsdatei hinzufügt. Wenn Sie das NuGet-Paket in einem Projekt installieren, das nicht das Startprojekt Ihrer App ist, müssen Sie die Registrierung in die Konfigurationsdatei für Ihr Startprojekt kopieren.

Der invariante Name, „invariantName“, ist in dieser Registrierung derselbe invariante Name, der für die Identifizierung eines ADO.NET-Anbieters verwendet wird. Dieser kann als „invariant“-Attribut in einer DbProviderFactories-Registrierung und als „providerName“-Attribut in einer Verbindungszeichenfolgenregistrierung gefunden werden. Der invariante Name, der verwendet werden soll, sollte auch in der Dokumentation für den Anbieter enthalten sein. Beispiele für invariante Namen sind u.a. „System.Data.SqlClient“ für SQL Server und „System.Data.SqlServerCe.4.0“ für SQL Server Compact.

Der Typ („type“) in dieser Registrierung ist der Name mit Assemblyqualifikation des Anbietertyps, der von „System.Data.Entity.Core.Common.DbProviderServices“ abgeleitet wird. Die Zeichenfolge, die z.B. für SQL Compact verwendet werden soll, ist „System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact“. Der Typ, der hier verwendet werden soll, sollte in der Dokumentation für den Anbieter enthalten sein.

### <a name="code-based-registration"></a>Codebasierte Registrierung

Ab Entity Framework 6 kann die anwendungsweite Konfiguration für EF im Code angegeben werden. Ausführliche Informationen dazu finden Sie unter _[Entity Framework – Code-Based Configuration (Codebasierte Konfiguration von Entity Framework)](https://msdn.microsoft.com/data/jj680699)_. Normalerweise wird ein EF-Anbieter, der die codebasierter Konfiguration verwendet, durch Erstellung einer neuen Klasse registriert, die von System.Data.Entity.DbConfiguration abstammt und in der gleichen Assembly platziert wird, wie Ihre DbContext-Klasse. Ihre DbConfiguration-Klasse sollte dann den Anbieter in ihrem Konstruktor registrieren. Die DbConfiguration-Klasse sieht wie folgt aus, um beispielsweise den SQL Compact-Anbieter zu registrieren:

``` csharp
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetProviderServices(
                SqlCeProviderServices.ProviderInvariantName,
                SqlCeProviderServices.Instance);
        }
    }
```

In diesem Code stellt „SqlCeProviderServices.ProviderInvariantName“ ein praktisches Feature für die Zeichenfolge des invarianten Namens des SQL Server Compact-Anbieters („System.Data.SqlServerCe.4.0“) dar, und SqlCeProviderServices.Instance gibt die Singletoninstanz des SQL Compact-EF-Anbieters zurück.

## <a name="what-if-the-provider-i-need-isnt-available"></a>Was passiert, wenn der Anbieter, den ich benötige, nicht verfügbar ist?

Wenn der Anbieter für vorherige Version von EF verfügbar ist, wenden Sie sich an den Besitzer des Anbieters, und bitten Sie diesen, eine EF6-Version zu erstellen. Sie sollten einen Verweis zur [Dokumentation für das EF6-Anbietermodell](xref:ef6/fundamentals/providers/provider-model) hinzufügen.

## <a name="can-i-write-a-provider-myself"></a>Kann ich selbst einen Anbieter schreiben?

Es ist bestimmt möglich, selbst einen EF-Anbieter zu erstellen, obwohl dies nicht als triviales Unterfangen angesehen werden sollte. Der Link oben zum EF6-Anbietermodell bietet einen guten Einstiegspunkt. Es kann auch nützlich sein, den Code für den SQL Server- und SQL CE-Anbieter, der in der [Open Source-Codebasis von EF](https://github.com/aspnet/EntityFramework6) enthalten ist, als Startpunkt oder zur Referenz zu verwenden.

Beachten Sie, dass der EF-Anbieter nicht so eng mit dem zugrunde liegenden ADO.NET-Anbieter verbunden ist, wenn Sie mit EF6 beginnen. Dadurch wird es einfacher, einen EF-Anbieter zu schreiben, da ADO.NET-Klassen nicht geschrieben oder umschlossen werden müssen.
