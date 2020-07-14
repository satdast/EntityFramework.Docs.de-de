---
title: Datenbankanbieter – EF Core
author: ajcvickers
ms.date: 12/17/2019
uid: core/providers/index
ms.openlocfilehash: 7970636ab68b04d4d8583a4c126864ecc27ce6b2
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238163"
---
# <a name="database-providers"></a>Datenbankanbieter

Entity Framework Core kann viele verschiedene Datenbanken über Plug-in-Bibliotheken erreichen, die als „Datenbankanbieter“ bezeichnet werden.

## <a name="current-providers"></a>Aktuelle Anbieter

> [!IMPORTANT]  
> EF Core-Anbieter werden durch eine Vielzahl von Quellen erstellt. Nicht alle Anbieter werden im Rahmen des Entity [Entity Framework Core-Projekts](https://github.com/aspnet/EntityFrameworkCore) verwaltet. Wenn Sie einen Anbieter in Betracht ziehen, sollten Sie Aspekte wie Qualität, Lizenzierung und Support auswerten, um sicherzustellen, dass dieser Ihren Anforderungen entspricht. Lesen Sie außerdem in jedem Fall die ausführlichen Informationen zur Versionskompatibilität in der Dokumentation der einzelnen Anbieter.

> [!IMPORTANT]  
> EF Core-Anbieter können in der Regel für alle Nebenversionen verwendet werden, nicht jedoch für Hauptversionen. Ein für EF Core 2.1 veröffentlichter Anbieter sollte beispielsweise mit EF Core 2.2 funktionieren, kann jedoch nicht mit EF Core 3.0 verwendet werden. 

| NuGet-Paket                                                                                                                                                                         | Unterstützte Datenbank-Engines | Maintainer/Anbieter                                                           | Hinweise/Anforderungen     | Zielversion | Nützliche Links                                                                                                                                                                                       |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------|:------------------------------------------------------------------------------|:-------------------------|:------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer)                                                                     | SQL Server 2012 oder höher    | [EF Core-Projekt](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                          | 3.1               | [docs](xref:core/providers/sql-server/index)                                                                                                                                                       |
| [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite)                                                                           | SQLite 3.7 oder höher         | [EF Core-Projekt](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                          | 3.1               | [docs](xref:core/providers/sqlite/index)                                                                                                                                                           |
| [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)                                                                       | EF Core-In-Memory-Datenbank | [EF Core-Projekt](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) | [Einschränkungen](xref:core/miscellaneous/testing/in-memory)                 | 3.1               | [docs](xref:core/providers/in-memory/index)                                                                                                                                                        |
| [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos)                                                                           | Azure Cosmos DB SQL-API    | [EF Core-Projekt](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                          | 3.1               | [docs](xref:core/providers/cosmos/index)                                                                                                                                                           |
| [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL)                                                                         | PostgreSQL                 | [Npgsql-Entwicklungsteam](https://github.com/npgsql)                          |                          | 3.1               | [docs](https://www.npgsql.org/efcore/index.html)                                                                                                                                                   |
| [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql)                                                                                   | MySQL, MariaDB             | [Pomelo Foundation-Projekt](https://github.com/PomeloFoundation)              |                          | 3.1               | [readme](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql/blob/master/README.md)                                                                                               |
| [MySql.Data.EntityFrameworkCore](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore)                                                                                       | MySQL                      | [MySQL-Projekt](https://dev.mysql.com) (Oracle)                               |                          | 3.1               | [docs](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework-core.html)                                                                                                         |
| [Oracle.EntityFrameworkCore](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)                                                                                              | Oracle DB 11.2 und höher     | [Oracle](https://www.oracle.com/technetwork/topics/dotnet/)                   | Beta                     | 3.1               | [Website](https://www.oracle.com/technetwork/topics/dotnet/)                                                                                                                                       |
| [Devart.Data.MySql.EFCore](https://www.nuget.org/packages/Devart.Data.MySql.EFCore/)                                                                                                  | MySQL 5 oder höher            | [DevArt](https://www.devart.com/)                                             | Bezahlt                     | 3.1               | [docs](https://www.devart.com/dotconnect/mysql/docs/)                                                                                                                                              |
| [Devart.Data.Oracle.EFCore](https://www.nuget.org/packages/Devart.Data.Oracle.EFCore/)                                                                                                | Oracle DB 9.2.0.4 und höher  | [DevArt](https://www.devart.com/)                                             | Bezahlt                     | 3.1               | [docs](https://www.devart.com/dotconnect/oracle/docs/)                                                                                                                                             |
| [Devart.Data.PostgreSql.EFCore](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore/)                                                                                        | PostgreSQL 8.0 oder höher     | [DevArt](https://www.devart.com/)                                             | Bezahlt                     | 3.1               | [docs](https://www.devart.com/dotconnect/postgresql/docs/)                                                                                                                                         |
| [Devart.Data.SQLite.EFCore](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore/)                                                                                                | SQLite 3 oder höher           | [DevArt](https://www.devart.com/)                                             | Bezahlt                     | 3.1               | [docs](https://www.devart.com/dotconnect/sqlite/docs/)                                                                                                                                             |
| [FirebirdSql.EntityFrameworkCore.Firebird](https://www.nuget.org/packages/FirebirdSql.EntityFrameworkCore.Firebird/)                                                                  | Firebird 2.5 und 3.x       | [Jiří Činčura](https://github.com/cincuranet)                                 |                          | 3.1               | [docs](https://github.com/cincuranet/FirebirdSql.Data.FirebirdClient/blob/master/Provider/docs/entity-framework-core.md)                                                                           |
| [IBM.EntityFrameworkCore](https://www-112.ibm.com/software/howtobuy/passportadvantage/paocustomer/sdma/SDMA?P0=DOWNLOAD_SEARCH_BY_PART_NO&FIELD_SEARCH_TYPE=3&searchVal=CC6XFML)      | DB2, Informix              | [IBM](https://ibm.com)                                                        | Kostenpflichtig, Windows            | 3.1               | [Kundenwebsite](https://www.ibm.com/software/passportadvantage/pao_customer.html) |
| [IBM.EntityFrameworkCore-lnx](https://www-112.ibm.com/software/howtobuy/passportadvantage/paocustomer/sdma/SDMA?P0=DOWNLOAD_SEARCH_BY_PART_NO&FIELD_SEARCH_TYPE=3&searchVal=CC6XGML)  | DB2, Informix              | [IBM](https://ibm.com)                                                        | Kostenpflichtig, Linux              | 3.1               | [Kundenwebsite](https://www.ibm.com/software/passportadvantage/pao_customer.html) |
| [IBM.EntityFrameworkCore-osx](https://www-112.ibm.com/software/howtobuy/passportadvantage/paocustomer/sdma/SDMA?P0=DOWNLOAD_SEARCH_BY_PART_NO&FIELD_SEARCH_TYPE=3&searchVal=CC6XHML)  | DB2, Informix              | [IBM](https://ibm.com)                                                        | Kostenpflichtig, macOS              | 3.1               | [Kundenwebsite](https://www.ibm.com/software/passportadvantage/pao_customer.html) |
| [FileContextCore](https://www.nuget.org/packages/FileContextCore/)                                                                                                                    | Speichert Daten in Dateien       | [Morris Janatzek](https://github.com/morrisjdev)                              | Dient Entwicklungszwecken | 3.0               | [readme](https://github.com/morrisjdev/FileContextCore/blob/master/README.md)                                                                                                                                              |
| [EntityFrameworkCore.Jet](https://www.nuget.org/packages/EntityFrameworkCore.Jet/)                                                                                                    | Microsoft Access-Dateien     | [Bubi](https://github.com/bubibubi)                                           | .NET Framework           | 2.2               | [readme](https://github.com/bubibubi/EntityFrameworkCore.Jet/blob/master/docs/README.md)                                                                                                           |
| [EntityFrameworkCore.SqlServerCompact35](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact35)                                                                       | SQL Server Compact 3,5     | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                             | .NET Framework           | 2.2               | [wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)                                                     |
| [EntityFrameworkCore.SqlServerCompact40](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact40)                                                                       | SQL Server Compact 4.0     | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                             | .NET Framework           | 2.2               | [wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)                                                     |
| [Teradata.EntityFrameworkCore](https://www.nuget.org/packages/Teradata.EntityFrameworkCore/)                                                                                          | Teradata-Datenbank ab 16.10 | [Teradata](https://downloads.teradata.com/download/connectivity/net-data-provider-for-teradata) |   | 2.2               |[Website](https://www.nuget.org/packages/Teradata.EntityFrameworkCore/)                                                                                                                            |
| [EntityFrameworkCore.FirebirdSql](https://www.nuget.org/packages/EntityFrameworkCore.FirebirdSql/)                                                                                    | Firebird 2.5 und 3.x       | [Rafael Almeida](https://github.com/ralmsdeveloper)                           |                          | 2.1               | [wiki](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL/wiki)                                                                                                                     |
| [EntityFrameworkCore.OpenEdge](https://www.nuget.org/packages/EntityFrameworkCore.OpenEdge/)                                                                                          | Progress OpenEdge          | [Alex Wiese](https://github.com/alexwiese)                                    |                          | 2.1               | [readme](https://github.com/alexwiese/EntityFrameworkCore.OpenEdge/blob/master/README.md)                                                                                                          |
| [Pomelo.EntityFrameworkCore.MyCat](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MyCat)                                                                                   | MyCAT-Server               | [Pomelo Foundation-Projekt](https://github.com/PomeloFoundation)              | Nur Vorabversion          | 1.1               | [readme](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MyCat/blob/master/README.md)                                                                                               |

## <a name="adding-a-database-provider-to-your-application"></a>Hinzufügen eines Datenbankanbieters zu Ihrer Anwendung

Die meisten Datenbankanbieter für EF Core werden als NuGet-Pakete verteilt und können wie folgt installiert werden:

## <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package provider_package_name
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
install-package provider_package_name
```

***

Nach Abschluss der Installation konfigurieren Sie den Anbieter in `DbContext` entweder in der `OnConfiguring`-Methode oder in der `AddDbContext`-Methode, wenn Sie einen Abhängigkeitsinjektionscontainer verwenden.
Die folgende Zeile konfiguriert z.B. den SQL Server-Anbieter mit der übergebenen Verbindungszeichenfolge:

``` csharp
optionsBuilder.UseSqlServer(
    "Server=(localdb)\mssqllocaldb;Database=MyDatabase;Trusted_Connection=True;");
```  

Datenbankanbieter können EF Core erweitern, um datenbankspezifische Funktionen zu aktivieren.
Einige Konzepte gelten für die meisten Datenbanken und sind im Leistungsumfang der primären EF Core-Komponenten inbegriffen.
Zu diesen Konzepten gehören das Ausdrücken von Abfragen in LINQ, Transaktionen und das Nachverfolgen von Änderungen an Objekten, nachdem sie aus der Datenbank geladen wurden.
Einige Konzepte gelten speziell für einen bestimmten Anbieter.
Der SQL Server-Anbieter ermöglicht es Ihnen beispielsweise, [speicheroptimierte Tabellen (ein für SQL Server spezifisches Feature) zu konfigurieren](xref:core/providers/sql-server/memory-optimized-tables).
Andere Konzepte gelten speziell für eine Klasse von Anbietern.
Beispielsweise bauen EF Core-Anbieter für relationale Datenbanken auf der gemeinsamen `Microsoft.EntityFrameworkCore.Relational`-Bibliothek auf, die u.a. APIs für die Konfiguration von Tabellen- und Spaltenzuordnungen und Fremdschlüsseleinschränkungen bereitstellt. Anbieter werden in der Regel als NuGet-Pakete verteilt.

> [!IMPORTANT]  
> Eine neue veröffentlichte Patchversion von EF Core enthält häufig Updates für das `Microsoft.EntityFrameworkCore.Relational`-Paket.
> Wenn Sie einen relationale Datenbankanbieter hinzufügen, wird dieses Paket zu einer transitiven Abhängigkeit Ihrer Anwendung.
> Viele Anbieter werden jedoch unabhängig von EF Core veröffentlicht und können nicht dahingehend aktualisiert werden, dass sie von der neueren Patchversion dieses Pakets abhängen.
> Um sicherzustellen, dass Sie von allen Fehlerbehebungen profitieren, wird empfohlen, die Patchversion von `Microsoft.EntityFrameworkCore.Relational` als direkte Abhängigkeit von Ihrer Anwendung hinzuzufügen.
