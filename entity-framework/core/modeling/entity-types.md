---
title: Entitäts Typen-EF Core
description: Konfigurieren und Zuordnen von Entitäts Typen mithilfe von Entity Framework Core
author: roji
ms.date: 10/06/2020
uid: core/modeling/entity-types
ms.openlocfilehash: 9094193640e7cab6db3fed7ae0ab818a455156ca
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429584"
---
# <a name="entity-types"></a>Entitätstypen

Das Einschließen eines dbsets eines Typs in den Kontext bedeutet, dass es in EF Core Modell enthalten ist. Normalerweise wird ein solcher Typ als *Entität* bezeichnet. EF Core können Entitäts Instanzen aus der Datenbank lesen und in diese schreiben. Wenn Sie eine relationale Datenbank verwenden, können EF Core Tabellen für Ihre Entitäten über Migrationen erstellen.

## <a name="including-types-in-the-model"></a>Einschließen von Typen im Modell

Gemäß der Konvention sind Typen, die in dbset-Eigenschaften für den Kontext verfügbar gemacht werden, im Modell als Entitäten enthalten. In der-Methode angegebene Entitäts Typen `OnModelCreating` werden ebenfalls eingeschlossen, ebenso wie alle Typen, die gefunden werden, indem die Navigations Eigenschaften anderer ermittelter Entitäts Typen rekursiv untersucht werden.

Im folgenden Codebeispiel sind alle Typen enthalten:

* `Blog` ist enthalten, da es in einer dbset-Eigenschaft für den Kontext verfügbar gemacht wird.
* `Post` ist enthalten, da Sie über die- `Blog.Posts` Navigations Eigenschaft erkannt wird.
* `AuditEntry` Da es in angegeben wird `OnModelCreating` .

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/EntityTypes.cs?name=EntityTypes&highlight=3,7,16)]

## <a name="excluding-types-from-the-model"></a>Ausschließen von Typen aus dem Modell

Wenn Sie nicht möchten, dass ein Typ im Modell enthalten ist, können Sie ihn ausschließen:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreType.cs?name=IgnoreType&highlight=1)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreType.cs?name=IgnoreType&highlight=3)]

***

### <a name="excluding-from-migrations"></a>Ausschließen von Migrationen

> [!NOTE]
> Die Möglichkeit, Tabellen aus Migrationen auszuschließen, wurde in EF Core 5,0 hinzugefügt.

Es ist manchmal hilfreich, den gleichen Entitätstyp in mehreren Typen zugeordnet zu haben `DbContext` . Dies trifft vor allem dann zu, wenn [begrenzte Kontexte](https://www.martinfowler.com/bliki/BoundedContext.html)verwendet werden, für die es üblich ist, `DbContext` für jeden Kontext Kontext einen anderen Typ zu verwenden.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs?name=TableExcludeFromMigrations&highlight=4)]

Bei dieser Konfigurations Migration wird die Tabelle nicht erstellt `AspNetUsers` , Sie `IdentityUser` ist jedoch noch im Modell enthalten und kann normal verwendet werden.

Wenn Sie erneut mit der Verwaltung der Tabelle beginnen müssen, sollte eine neue Migration erstellt werden, bei der `AspNetUsers` nicht ausgeschlossen ist. Die nächste Migration enthält jetzt alle Änderungen, die an der Tabelle vorgenommen werden.

## <a name="table-name"></a>Tabellenname

Gemäß der Konvention wird jeder Entitätstyp so eingerichtet, dass er einer Datenbanktabelle mit dem gleichen Namen wie die dbset-Eigenschaft zugeordnet wird, die die Entität verfügbar macht. Wenn kein dbset für die angegebene Entität vorhanden ist, wird der Klassenname verwendet.

Sie können den Tabellennamen manuell konfigurieren:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableName.cs?Name=TableName&highlight=1)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableName.cs?Name=TableName&highlight=3-4)]

**_

## <a name="table-schema"></a>Tabellenschema

Wenn Sie eine relationale Datenbank verwenden, werden Tabellen gemäß der Konvention im Standardschema Ihrer Datenbank erstellt. Beispielsweise wird Microsoft SQL Server das Schema verwenden `dbo` (SQLite unterstützt keine Schemas).

Tabellen, die in einem bestimmten Schema erstellt werden sollen, können wie folgt konfiguriert werden:

### <a name="data-annotations"></a>[Daten Anmerkungen](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=1)]

### <a name="fluent-api"></a>[Fluent-API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TableNameAndSchema.cs?name=TableNameAndSchema&highlight=3-4)]

_**

Anstatt das Schema für jede Tabelle anzugeben, können Sie auch das Standardschema auf Modell Ebene mit der fließenden API definieren:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DefaultSchema.cs?name=DefaultSchema&highlight=3)]

Beachten Sie, dass das Festlegen des Standard Schemas auch andere Datenbankobjekte, z. b. Sequenzen, beeinträchtigt.

## <a name="view-mapping"></a>Zuordnung anzeigen

Entitäts Typen können mithilfe der flüssigen API Daten Bank Sichten zugeordnet werden.

> [!Note]
> EF nimmt an, dass die Sicht, auf die verwiesen wird, bereits in der Datenbank vorhanden ist, wird Sie in einer Migration nicht automatisch erstellt.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ViewNameAndSchema.cs?name=ViewNameAndSchema&highlight=1)]

 Durch die Zuordnung zu einer Sicht wird die Standard Tabellen Zuordnung entfernt. ab EF 5,0 kann der Entitätstyp jedoch auch explizit einer Tabelle zugeordnet werden. In diesem Fall wird die Abfrage Zuordnung für Abfragen verwendet, und die Tabellen Zuordnung wird für Updates verwendet.

> [!TIP]
> Zum Testen von Entitäts Typen, die Ansichten zugeordnet sind, mithilfe des Speicher internen Anbieters, ordnen Sie Sie einer Abfrage über zu `ToInMemoryQuery` . Weitere Informationen finden Sie unter [ausführbares Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) mit dieser Technik.
