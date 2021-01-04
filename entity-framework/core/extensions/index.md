---
title: Tools und Erweiterungen – EF Core
description: Externe Tools und Erweiterungen für Entity Framework Core
author: ErikEJ
ms.date: 11/22/2020
uid: core/extensions/index
ms.openlocfilehash: 4136710567d0f6b088a0dd5ad9895d8b93313e5e
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635405"
---
# <a name="ef-core-tools--extensions"></a>EF Core Tools und -Erweiterungen

Diese Tools und Erweiterungen bieten zusätzliche Funktionen für Entity Framework Core 2.1 und höher.

> [!IMPORTANT]  
> Erweiterungen werden durch eine Vielzahl von Quellen erstellt und nicht als Teil des Entity Framework Core-Projekts verwaltet. Wenn Sie die Erweiterung eines Drittanbieters in Betracht ziehen, sollten Sie Qualität, Lizenzierung, Kompatibilität, Support usw. auswerten, um sicherzustellen, dass diese Ihren Anforderungen entspricht. Bei Erweiterungen, die für eine ältere EF Core-Version erstellt wurden, ist insbesondere darauf zu achten, dass sie möglicherweise aktualisiert werden müssen, bevor sie mit den neuesten Versionen kompatibel sind.

## <a name="tools"></a>Tools

### <a name="llblgen-pro"></a>LLBLGen Pro

LLBLGen Pro ist eine Entitätsmodelllösung, die Entity Framework und Entity Framework Core unterstützt. Sie können problemlos ihr Entitätsmodell definieren und es Ihrer Datenbank zuordnen, indem Sie Database First oder Model First verwenden, sodass Sie sofort mit dem Schreiben von Abfragen beginnen können. Für EF Core: 2, 3.

[Website](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a>Devart Entity Developer

Entity Developer ist ein leistungsstarker O/RM-Designer für ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access und LINQ to SQL. Sie können damit EF Core-Modelle mithilfe von Model First oder Database First visuell entwerfen sowie C#- oder Visual Basic-Code generieren. Für EF Core: 2, 3, 5.

[Website](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a>nHydrate ORM für Entity Framework

Ein O/RM, der stark typisierte, erweiterbare Klassen für Entity Framework erstellt. Der generierte Code ist Entity Framework Core. Es besteht kein Unterschied. Dies ist kein Ersatz für EF oder einen benutzerdefinierten O/RM. Dabei handelt es sich um eine visuelle, Modellierungsebene, die einem Team ermöglicht, komplexe Datenbankschemas zu verwalten. Dies funktioniert gut mit SCM-Software wie Git, sodass mehrere Benutzer mit minimalen Konflikten auf Ihr Modell zugreifen können. Das Installationsprogramm verfolgt Modelländerungen und erstellt Upgradeskripts. Für EF Core: 3.

[GitHub-Website](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a>EF Core Power Tools

EF Core Power Tools ist eine Erweiterung von Visual Studio, die verschiedene Aufgaben von EF Core zur Entwurfszeit in einer einfachen Benutzeroberfläche verfügbar macht. Dadurch wird Reverse Engineering (Zurückentwicklung) von DbContext- und Entitätsklassen aus vorhandenen Datenbanken und [SQL Server-DACPACs](/sql/relational-databases/data-tier-applications/data-tier-applications), die Verwaltung von Datenbankmigrationen und das Vornehmen von Modellvisualisierungen ermöglicht. Für EF Core: 3, 5.

[GitHub-Wiki](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a>Visueller Editor für Entity Framework

Entity Framework Visual Editor ist eine Erweiterung von Visual Studio, mit der ein O/RM-Designer für visuelle Entwürfe von EF 6- und EF Core-Klassen hinzugefügt werden kann. Code wird mithilfe von T4-Vorlagen generiert und kann somit vollständig an die Anforderungen des Benutzers angepasst werden. Vererbung, uni- und bidirektionale Zuordnungen, Enumerationen, Farbcode für Klassen und das Hinzufügen von Textblöcken für Erklärungen zu möglicherweise schwer durchschaubaren Bereichen Ihres Designs werden unterstützt. Für EF Core: 2, 3, 5.

[Marketplace](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a>CatFactory

CatFactory ist eine Gerüstbau-Engine für .NET Core, mit der die Generierung von DbContext-Klassen und -Entitäten, Zuordnungskonfigurationen und Repositoryklassen aus einer SQL Server-Datenbank automatisiert werden kann. Für EF Core: 2.

[GitHub-Repository](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a>Entity Framework Core-Generator von LoreSoft

Entity Framework Core-Generator (efg) ist ein .NET Core-CLI-Tool, mit dem EF Core-Modelle aus einer vorhandenen Datenbank generiert werden können, wie beispielsweise `dotnet ef dbcontext scaffold`. Es unterstützt aber auch die [Neugenerierung](https://efg.loresoft.com/en/latest/regeneration/) sicheren Codes durch das Ersetzen eines Bereichs oder durch das Analysieren von Zuordnungsdateien. Dieses Tool unterstützt das Generieren von Ansichtsmodellen, die Validierung und Objektzuordnungscode. Für EF Core: 2.

[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Dokumentation](https://efg.loresoft.com/en/latest/)

## <a name="extensions"></a>Erweiterungen

### <a name="microsoftentityframeworkcoreautohistory"></a>Microsoft.EntityFrameworkCore.AutoHistory

Eine Plug-in-Bibliothek, mit deren Hilfe automatisch von EF Core durchgeführte Änderungen an den Daten in einer Verlaufstabelle aufgezeichnet werden. Für EF Core: 2, 3.

[GitHub-Repository](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a>EFCoreSecondLevelCacheInterceptor

Das Zwischenspeichern zweiter Ebene ist ein Abfragecache. Die Ergebnisse von EF-Befehlen werden im Cache gespeichert, sodass die gleichen EF-Befehle ihre Daten eher aus dem Cache abrufen, als die Datenbank erneut zu durchsuchen. Für EF Core: 3, 5.

[GitHub-Repository](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a>Geco

Geco (Generator-Konsole) ist ein einfacher Code-Generator, der auf einem Konsolenprojekt basiert, das auf .NET Core ausgeführt wird und interpolierte Zeichenfolgen in C# für die Codegenerierung verwendet. Geco enthält einen Reversemodell-Generator für EF Core, der die Pluralisierung, Singularisierung und bearbeitbare Vorlagen unterstützt. Er enthält auch einen Startdatenskript-Generator, einen Skript-Runner sowie eine Datenbankbereinigung. Für EF Core: 2.

[GitHub-Repository](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a>EntityFrameworkCore.Scaffolding.Handlebars

Mit dieser Erweiterung können Klassen angepasst werden, die per Reverse Engineering mithilfe der Entity Framework Core-Toolkette mit Handlebars-Vorlagen aus einer vorhandenen Datenbank erstellt wurden. Für EF Core: 2, 3, 5.

[GitHub-Repository](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a>NeinLinq.EntityFrameworkCore

NeinLinq ist eine Erweiterung für LINQ-Anbieter wie Entity Framework, um Funktionen wiederverwenden zu können, Abfragen erneut schreiben zu können und dynamische Abfragen mithilfe von übersetzbaren Prädikaten und Selektoren erstellen zu können. Für EF Core: 2, 3, 5.

[GitHub-Repository](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a>Microsoft.EntityFrameworkCore.UnitOfWork

Ein Plug-In für Microsoft.EntityFrameworkCore zur Unterstützung von Repositorys, Arbeitseinheitsmustern und mehreren Datenbanken, die verteilte Transaktionen unterstützen. Für EF Core: 2, 3.

[GitHub-Repository](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a>EFCore.BulkExtensions

EF Core-Erweiterungen für Massenvorgänge (Einfügen, Aktualisieren, Löschen) Für EF Core: 2, 3.

[GitHub-Repository](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a>Bricelam.EntityFrameworkCore.Pluralizer

Diese Erweiterung fügt die Pluralisierung zur Entwurfszeit hinzu. Für EF Core: 2, 3.

[GitHub-Repository](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a>Toolbelt.EntityFrameworkCore.IndexAttribute

Über diese Erweiterung ist das [Index]-Attribut mit der Erweiterung für die Modellerstellung wieder verfügbar. Für EF Core: 2, 3, 5.

[GitHub-Repository](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a>EfCore.InMemoryHelpers

Bietet einen Wrapper, der den In-Memory-Datenbankanbieter für EF Core umschließt. Fungiert dadurch mehr als relationaler Anbieter. Für EF Core: 2.

[GitHub-Repository](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a>EFCore.TemporalSupport

Mit dieser Erweiterung wird die Unterstützung temporaler Daten implementiert. Für EF Core: 2.

[GitHub-Repository](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a>EfCoreTemporalTable

Mit dieser Erweiterung können Sie mithilfe bereits eingeführter Erweiterungsmethoden temporale Abfragen für eine Datenbank Ihrer Wahl ausführen: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`. Für EF Core: 3.

[GitHub-Repository](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="entityframeworkcoretemporaltables"></a>EntityFrameworkCore.TemporalTables

Eine Erweiterungsbibliothek für Entity Framework Core, mit der Entwickler, die SQL Server nutzen, temporale Tabellen einfach verwenden können. Für EF Core: 2, 3.

[GitHub-Repository](https://github.com/findulov/EntityFrameworkCore.TemporalTables)

### <a name="entityframeworkcorecacheable"></a>EntityFrameworkCore.Cacheable

Ein Abfragecache auf zweiter Ebene mit hoher Leistung. Für EF Core: 2.

[GitHub-Repository](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a>EntityFrameworkCore.NCache

Der NCache-Anbieter von Entity Framework Core ist ein verteilter Cacheanbieter der zweiten Ebene zum Zwischenspeichern von Abfrageergebnissen. Die verteilte Architektur von NCache macht den Anbieter skalierbarer und hochverfügbar. Für EF Core 2, 3.

[Website](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entityframeworkcoretriggered"></a>EntityFrameworkCore.Triggered

Trigger für EF Core. Hiermit können Sie auf Änderungen an DbContext reagieren, bevor und nachdem sie in der Datenbank committet wurden. Trigger sind vollständig asynchron und unterstützen Dependency Injection, Vererbung, Kaskadierung und vieles mehr. Für EF Core: 3, 5.

[GitHub-Repository](https://github.com/koenbeuk/EntityFrameworkCore.Triggered)

### <a name="entity-framework-plus"></a>Entity Framework Plus

Erweitert Ihren DbContext mit Features wie den folgenden: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update und vielen weiteren. Für EF Core: 2, 3, 5.

[Website](https://entityframework-plus.net/)
[GitHub-Repository](https://github.com/zzzprojects/EntityFramework-Plus)

### <a name="entity-framework-extensions"></a>Entity Framework-Erweiterungen

Erweitert Ihren DbContext mit hochleistungsfähigen Massenvorgängen: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge und vielen weiteren. Für EF Core: 2, 3.

[Website](https://entityframework-extensions.net/)

### <a name="expressionify"></a>Expressionify

Fügt Unterstützung für das Aufrufen von Erweiterungsmethoden in LINQ-Lambdas hinzu. Für EF Core: 3.

[GitHub-Repository](https://github.com/ClaveConsulting/Expressionify)

### <a name="elinq"></a>ELinq

LINQ-Technologie (Language Integrated Query) für relationale Datenbanken. Sie ermöglicht Ihnen, mit C# stark typisierte Abfragen zu schreiben. Für EF Core: 3.

- Volle Unterstützung von C# für das Erstellen von Abfragen: mehrere Anweisungen innerhalb von Lambdaausdrücken, Variablen, Funktionen usw.
- Keine semantische Lücke zu SQL. ELinq deklariert SQL-Anweisungen (wie `SELECT`, `FROM`, `WHERE`) als erstklassige C#-Methoden, wobei die vertraute Syntax mit IntelliSense, Typsicherheit und Refactoring kombiniert wird.

Infolgedessen wird SQL einfach zu einer „weiteren“ Klassenbibliothek, die ihre API lokal verfügbar macht, wörtlich *Sprachintegrierte SQL*.

[Website](https://entitylinq.com/)

### <a name="ramses"></a>Ramses

Lebenszyklushooks (für SaveChanges). Für EF Core: 2, 3.

[GitHub-Repository](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a>EFCore.NamingConventions

Hiermit wird automatisch snake_case-Schreibung, reine Großschreibung oder reine Kleinschreibung auf alle Tabellen- und Spaltennamen angewendet. Für EF Core: 3.

[GitHub-Repository](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a>SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime

Fügt EntityFrameworkCore für SQL Server native Unterstützung für die NodaTime-Typen hinzu. Für EF Core: 3, 5.

[GitHub-Repository](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a>Dabble.EntityFrameworkCore.Temporal.Query

LINQ-Erweiterungen für Entity Framework Core 3.1 zur Unterstützung von Abfragen temporaler Tabellen in Microsoft SQL Server. Für EF Core: 3.

[GitHub-Repository](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a>EntityFrameworkCore.SqlServer.HierarchyId

Fügt dem SQL Server EF Core-Anbieter hierarchyid-Unterstützung hinzu. Für EF Core: 3.

[GitHub-Repository](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a>linq2db.EntityFrameworkCore

Alternativer Übersetzer von LINQ-Abfragen an SQL-Ausdrücke Für EF Core: 3, 5.

Bietet Unterstützung für erweiterte SQL-Features wie CTEs, Massenkopiervorgänge, Tabellenhinweise, Fensterfunktionen, temporäre Tabellen und Erstell-, Update, und Löschvorgänge auf Datenbankseite

[GitHub-Repository](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a>EFCore.SoftDelete

Eine-Implementierung für das vorläufige Löschen von Entitäten. Für EF Core: 3.

[NuGet](https://www.nuget.org/packages/EFCore.SoftDelete)

### <a name="entityframeworkcoreconfigurationmanager"></a>EntityFrameworkCore.ConfigurationManager

Erweitert EF Core zum Auflösen von Verbindungszeichenfolgen aus „App.config“. Für EF Core: 3.

[GitHub-Repository](https://github.com/efcore/EFCore.ConfigurationManager)

### <a name="detached-mapper"></a>Getrennter Mapper

Ein DTO-Entity-Mapper mit Kompositions-/Aggregationsverarbeitung (ähnlich wie GraphDiff). Für EF Core: 3, 5.

[NuGet](https://www.nuget.org/packages/Detached.Mappers.EntityFramework)
