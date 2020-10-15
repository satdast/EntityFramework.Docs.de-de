---
title: Anwenden von Migrationen-EF Core
description: Strategien zum Anwenden von Schema Migrationen auf Produktions-und Entwicklungs Datenbanken mithilfe von Entity Framework Core
author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: 68d482a34e5f5c7acf968acdfd8825e1d21ecb13
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062346"
---
# <a name="applying-migrations"></a>Anwenden von Migrationen

Nachdem Ihre Migrationen hinzugefügt wurden, müssen Sie bereitgestellt und auf Ihre Datenbanken angewendet werden. Hierfür gibt es verschiedene Strategien, die für Produktionsumgebungen besser geeignet sind, und andere für den Entwicklungs Lebenszyklus.

> [!NOTE]
> Ungeachtet der Bereitstellungs Strategie sollten Sie immer die generierten Migrationen untersuchen und testen, bevor Sie Sie auf eine Produktionsdatenbank anwenden. Eine Migration kann eine Spalte löschen, wenn die Absicht darin besteht, Sie umzubenennen, oder aus verschiedenen Gründen fehlschlagen, wenn Sie auf eine Datenbank angewendet wird.

## <a name="sql-scripts"></a>SQL-Skripts

Die empfohlene Vorgehensweise zum Bereitstellen von Migrationen in eine Produktionsdatenbank ist das Erstellen von SQL-Skripts. Diese Strategie bietet folgende Vorteile:

* SQL-Skripts können auf Genauigkeit überprüft werden. Dies ist wichtig, da das Anwenden von Schema Änderungen auf Produktionsdatenbanken einen potenziell gefährlichen Vorgang verursachen kann, der zu Datenverlusten führen kann.
* In einigen Fällen können die Skripts an die spezifischen Anforderungen einer Produktionsdatenbank angepasst werden.
* SQL-Skripts können in Verbindung mit einer Bereitstellungs Technologie verwendet werden und können sogar als Teil Ihres CI-Prozesses generiert werden.
* SQL-Skripts können einem Datenbankadministrator bereitgestellt werden und können separat verwaltet und archiviert werden.

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a>Grundlegende Verwendung

Im folgenden wird ein SQL-Skript aus einer leeren Datenbank mit der neuesten Migration generiert:

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a>Mit „from“ („to“ wird impliziert)

Im folgenden wird ein SQL-Skript aus der angegebenen Migration zur neuesten Migration generiert.

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a>Mit „from“ und „to“

Im folgenden wird ein SQL-Skript aus der angegebenen `from` Migration zur angegebenen `to` Migration generiert.

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

Sie können ein `from` verwenden, das aktueller ist als `to`, um ein Rollbackskript zu generieren.

> [!WARNING]
> Bitte achten Sie auf mögliche Datenverlustszenarios.

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

#### <a name="basic-usage"></a>Grundlegende Verwendung

Im folgenden wird ein SQL-Skript aus einer leeren Datenbank mit der neuesten Migration generiert:

```powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a>Mit „from“ („to“ wird impliziert)

Im folgenden wird ein SQL-Skript aus der angegebenen Migration zur neuesten Migration generiert.

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a>Mit „from“ und „to“

Im folgenden wird ein SQL-Skript aus der angegebenen `from` Migration zur angegebenen `to` Migration generiert.

```powershell
Script-Migration AddNewTables AddAuditTable
```
Sie können ein `from` verwenden, das aktueller ist als `to`, um ein Rollbackskript zu generieren. *Bitte achten Sie auf mögliche Datenverlustszenarios.*

***

Die Skript Generierung akzeptiert die folgenden zwei Argumente in, um anzugeben, welcher Bereich von Migrationen generiert werden soll:

* Die **from**-Migration sollte die letzte Migration sein, die vor der Skriptausführung für die Datenbank durchgeführt wurde. Wenn keine Migrationen durchgeführt wurden, geben Sie `0` an (dies ist die Standardeinstellung).
* Die **to**-Migration ist die letzte Migration, die nach der Skriptausführung für die Datenbank durchgeführt wurde. Dies ist standardmäßig die letzte Migration in Ihrem Projekt.

## <a name="idempotent-sql-scripts"></a>Idempotente SQL-Skripts

Die oben generierten SQL-Skripts können nur angewendet werden, um das Schema von einer Migration zu einer anderen zu ändern. Es liegt in ihrer Verantwortung, das Skript ordnungsgemäß und nur auf die Datenbank im richtigen Migrations Zustand anzuwenden. EF Core unterstützt auch das erzeugen **idempotenter** Skripts, die intern überprüfen, welche Migrationen bereits angewendet wurden (über die Migrations Verlaufs Tabelle) und nur fehlende anwenden. Dies ist hilfreich, wenn Sie nicht genau wissen, wie die letzte Migration auf die Datenbank angewendet wurde, oder wenn Sie eine Bereitstellung in mehreren Datenbanken durchgeführt haben, bei denen es sich jeweils um eine andere Migration handelt.

Im folgenden werden idempotente Migrationen generiert:

#### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Script-Migration -Idempotent
```

***

## <a name="command-line-tools"></a>Befehlszeilentools

Die EF-Befehlszeilen Tools können zum Anwenden von Migrationen auf eine Datenbank verwendet werden. Während der Produktivität bei der lokalen Entwicklung und beim Testen von Migrationen ist dieser Ansatz nicht ideal für die Verwaltung von Produktionsdatenbanken:

* Die SQL-Befehle werden direkt durch das Tool angewendet, ohne dem Entwickler die Möglichkeit zu geben, Sie zu überprüfen oder zu ändern. Dies kann in einer Produktionsumgebung gefährlich sein.
* Das .NET SDK und das EF-Tool müssen auf Produktionsservern installiert sein.

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/dotnet-core-cli)

Im folgenden wird die-Datenbank auf die neueste Migration aktualisiert:

```dotnetcli
dotnet ef database update
```

Im folgenden wird die-Datenbank auf eine bestimmte Migration aktualisiert:

```dotnetcli
dotnet ef database update AddNewTables
```

Beachten Sie, dass dies auch für einen Rollback zu einer früheren Migration verwendet werden kann.

> [!WARNING]
> Bitte achten Sie auf mögliche Datenverlustszenarios.

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

Im folgenden wird die-Datenbank auf die neueste Migration aktualisiert:

```powershell
Update-Database
```

Im folgenden wird die-Datenbank auf eine bestimmte Migration aktualisiert:

```powershell
Update-Database AddNewTables
```

Beachten Sie, dass dies auch für einen Rollback zu einer früheren Migration verwendet werden kann.

> [!WARNING]
> Bitte achten Sie auf mögliche Datenverlustszenarios.

***

Weitere Informationen zum Anwenden von Migrationen über die Befehlszeilen Tools finden Sie in der [EF Core Tools-Referenz](xref:core/miscellaneous/cli/index).

## <a name="apply-migrations-at-runtime"></a>Anwenden von Migrationen zur Laufzeit

Es ist möglich, dass die Anwendung selbst Migrationen Programm gesteuert anwendet, in der Regel während des Starts. Während der Produktivität bei der lokalen Entwicklung und beim Testen von Migrationen ist dieser Ansatz für die Verwaltung von Produktionsdatenbanken aus den folgenden Gründen ungeeignet:

* Wenn mehrere Instanzen der Anwendung ausgeführt werden, können beide Anwendungen versuchen, die Migration gleichzeitig anzuwenden und einen Fehler zu verursachen (oder eine Beschädigung der Daten zu verursachen).
* Wenn eine Anwendung auf die Datenbank zugreift, während Sie von einer anderen Anwendung migriert wird, kann dies zu schwerwiegenden Problemen führen.
* Die Anwendung muss über erhöhten Zugriff verfügen, um das Datenbankschema zu ändern. Im Allgemeinen empfiehlt es sich, die Daten Bank Berechtigungen der Anwendung in der Produktion einzuschränken.
* Es ist wichtig, dass ein Rollback für eine angewendete Migration im Falle eines Problems ausgeführt werden kann. Die anderen Strategien stellen dies problemlos und standardmäßig bereit.
* Die SQL-Befehle werden direkt durch das Programm angewendet, ohne dem Entwickler die Möglichkeit zu geben, Sie zu überprüfen oder zu ändern. Dies kann in einer Produktionsumgebung gefährlich sein.

Zum programmgesteuerten Anwenden von Migrationen muss aufgerufen werden `context.Database.Migrate()` . Eine typische ASP.NET-Anwendung kann z. b. folgende Aktionen ausführen:

```csharp
public static void Main(string[] args)
{
    var host = CreateHostBuilder(args).Build();

    using (var scope = host.Services.CreateScope())
    {
        var db = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
        db.Database.Migrate();
    }

    host.Run();
}
```

Beachten Sie, dass `Migrate()` auf dem `IMigrator` Dienst aufbaut, der für erweiterte Szenarien verwendet werden kann. Verwenden Sie `myDbContext.GetInfrastructure().GetService<IMigrator>()`, um darauf zugreifen.

> [!WARNING]
>
> * Beachten Sie sorgfältig, bevor Sie diesen Ansatz in der Produktion verwenden. Es wurde gezeigt, dass die Einfachheit dieser Bereitstellungs Strategie durch die von ihr erstellten Probleme überwiegen. Sie sollten stattdessen SQL-Skripts aus Migrationen erstellen.
> * Rufen Sie `EnsureCreated()` nicht vor `Migrate()` auf. `EnsureCreated()` umgeht Migrationen zum Erstellen von Schemas, was dazu führt, dass `Migrate()` fehlerhaft ist.
