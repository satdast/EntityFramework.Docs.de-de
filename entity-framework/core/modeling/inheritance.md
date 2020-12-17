---
title: Vererbung-EF Core
description: Konfigurieren der Vererbung von Entitäts Typen mit Entity Framework Core
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/inheritance
ms.openlocfilehash: 11bd653a53767aa732790b1222da1beff8ad26a9
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635756"
---
# <a name="inheritance"></a>Vererbung

EF kann eine .net-Typhierarchie einer Datenbank zuordnen. Dies ermöglicht es Ihnen, Ihre .NET-Entitäten wie gewohnt mit Basis-und abgeleiteten Typen in Code zu schreiben und EF nahtlos das geeignete Datenbankschema zu erstellen, Abfragen auszugeben usw. Die tatsächlichen Details, wie eine Typhierarchie zugeordnet wird, sind Anbieter abhängig. Diese Seite beschreibt die Vererbungs Unterstützung im Kontext einer relationalen Datenbank.

## <a name="entity-type-hierarchy-mapping"></a>Entitätstyp-Hierarchie Zuordnung

Gemäß der Konvention scannt EF nicht automatisch nach Basis-oder abgeleiteten Typen. Dies bedeutet, dass Sie den Typ für das Modell explizit angeben müssen, wenn Sie möchten, dass ein CLR-Typ in der Hierarchie zugeordnet wird. Wenn Sie z. b. nur den Basistyp einer Hierarchie angeben, bewirkt dies nicht, dass EF Core implizit alle seine Untertypen einschließt.

Im folgenden Beispiel wird ein dbset für `Blog` und seine Unterklasse verfügbar gemacht `RssBlog` . Wenn `Blog` eine andere Unterklasse aufweist, wird Sie nicht in das Modell eingeschlossen.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

> [!NOTE]
> Bei Verwendung der TPH-Zuordnung werden bei Bedarf automatisch NULL-Werte für Daten Bank Spalten festgelegt. Beispielsweise kann die-Spalte auf NULL festgelegt werden, `RssUrl` da reguläre `Blog` Instanzen nicht über diese Eigenschaft verfügen.

Wenn Sie `DbSet` für eine oder mehrere Entitäten in der Hierarchie keinen verfügbar machen möchten, können Sie auch die fließende API verwenden, um sicherzustellen, dass Sie im Modell enthalten sind.

> [!TIP]
> Wenn Sie sich nicht auf Konventionen verlassen, können Sie den Basistyp explizit mithilfe von angeben `HasBaseType` . Sie können auch verwenden `.HasBaseType((Type)null)` , um einen Entitätstyp aus der Hierarchie zu entfernen.

## <a name="table-per-hierarchy-and-discriminator-configuration"></a>Tabelle pro Hierarchie und diskriminatorkonfiguration

Standardmäßig ordnet EF die Vererbung mithilfe des TPH-Musters ( *Table-per Hierarchy* ) zu. TPH verwendet eine einzelne Tabelle zum Speichern der Daten für alle Typen in der Hierarchie, und eine diskriminatorspalte wird verwendet, um den Typ zu identifizieren, den jede Zeile darstellt.

Das obige Modell ist dem folgenden Datenbankschema zugeordnet (Beachten Sie die implizit erstellte `Discriminator` Spalte, die angibt, welcher Typ von `Blog` in den einzelnen Zeilen gespeichert ist).

![Screenshot der Ergebnisse der Abfrage der Blog-Entitäts Hierarchie mithilfe eines "Tabelle pro Hierarchie"-Musters](_static/inheritance-tph-data.png)

Sie können den Namen und den Typ der diskriminatorspalte und die Werte, die zum Identifizieren der einzelnen Typen in der Hierarchie verwendet werden, konfigurieren:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

In den obigen Beispielen wurde der Diskriminator von EF implizit als [Schatten Eigenschaft](xref:core/modeling/shadow-properties) für die Basis Entität der Hierarchie hinzugefügt. Diese Eigenschaft kann wie jede andere konfiguriert werden:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

Schließlich kann der Diskriminator auch einer regulären .net-Eigenschaft in der Entität zugeordnet werden:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

Beim Abfragen abgeleiteter Entitäten, die das TPH-Muster verwenden, fügt EF Core in der Abfrage ein Prädikat für die diskriminatorspalte hinzu. Dieser Filter stellt sicher, dass keine zusätzlichen Zeilen für Basis Typen oder gleich geordnete Typen, die nicht im Ergebnis sind, angezeigt werden. Dieses Filter Prädikat wird für den Basis Entitätstyp übersprungen, da Abfragen für die Basis Entität Ergebnisse für alle Entitäten in der Hierarchie erhalten. Wenn beim Materialisieren der Ergebnisse aus einer Abfrage ein Diskriminatorwert vorhanden ist, der keinem Entitätstyp im Modell zugeordnet ist, wird eine Ausnahme ausgelöst, da wir nicht wissen, wie die Ergebnisse materialisiert werden. Dieser Fehler tritt nur auf, wenn die Datenbank Zeilen mit diskriminatorwerten enthält, die im EF-Modell nicht zugeordnet sind. Wenn Sie solche Daten haben, können Sie die diskriminatorzuordnung in EF Core Modell als unvollständig kennzeichnen, um anzugeben, dass wir immer ein Filter Prädikat zum Abfragen beliebiger Typen in der Hierarchie hinzufügen sollten. `IsComplete(false)` der-Befehl für die diskriminatorkonfiguration markiert die Zuordnung als unvollständig.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorMappingIncomplete.cs?name=DiscriminatorMappingIncomplete&highlight=5)]

### <a name="shared-columns"></a>Freigegebene Spalten

Wenn zwei gleich geordnete Entitäts Typen in der Hierarchie über eine Eigenschaft mit demselben Namen verfügen, werden Sie standardmäßig zwei separaten Spalten zugeordnet. Wenn Ihr Typ jedoch identisch ist, können Sie derselben Daten Bank Spalte zugeordnet werden:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]

## <a name="table-per-type-configuration"></a>"Tabelle pro Typ"-Konfiguration

> [!NOTE]
> Die Funktion "Tabelle pro Typ" (TPT) wurde in EF Core 5,0 eingeführt. "Table-per-Concrete-Type" (TPC) wird von EF6 unterstützt, aber noch nicht von EF Core unterstützt.

Im TPT-Zuordnungsmuster werden alle Typen einzelnen Tabellen zugeordnet. Eigenschaften, die nur zu einem Basistyp oder einem abgeleiteten Typ gehören, werden in einer Tabelle gespeichert, die diesem Typ zugeordnet ist. Tabellen, die abgeleiteten Typen zugeordnet sind, speichern auch einen Fremdschlüssel, der die abgeleitete Tabelle mit der Basistabelle verbindet.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=TPTConfiguration)]

EF erstellt das folgende Datenbankschema für das obige Modell.

```sql
CREATE TABLE [Blogs] (
    [BlogId] int NOT NULL IDENTITY,
    [Url] nvarchar(max) NULL,
    CONSTRAINT [PK_Blogs] PRIMARY KEY ([BlogId])
);

CREATE TABLE [RssBlogs] (
    [BlogId] int NOT NULL,
    [RssUrl] nvarchar(max) NULL,
    CONSTRAINT [PK_RssBlogs] PRIMARY KEY ([BlogId]),
    CONSTRAINT [FK_RssBlogs_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([BlogId]) ON DELETE NO ACTION
);
```

> [!NOTE]
> Wenn die PRIMARY KEY-Einschränkung umbenannt wird, wird der neue Name auf alle Tabellen angewendet, die der Hierarchie zugeordnet sind. in zukünftigen EF-Versionen wird das Umbenennen der Einschränkung nur für eine bestimmte Tabelle ermöglicht, wenn das [Problem 19970](https://github.com/dotnet/efcore/issues/19970) behoben ist.

Wenn Sie die Massen Konfiguration verwenden, können Sie den Spaltennamen für eine bestimmte Tabelle abrufen, indem Sie aufrufen <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName(Microsoft.EntityFrameworkCore.Metadata.IProperty,Microsoft.EntityFrameworkCore.Metadata.StoreObjectIdentifier@)> .

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=Metadata&highlight=10)]

> [!WARNING]
> In vielen Fällen zeigt TPT im Vergleich zu TPH eine geringere Leistung. [Weitere Informationen finden](xref:core/performance/modeling-for-performance#inheritance-mapping)Sie in der Dokumentation zur Leistung.
