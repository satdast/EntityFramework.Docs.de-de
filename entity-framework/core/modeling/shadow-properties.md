---
title: Eigenschaften von Schatten und Indexer-EF Core
description: Konfigurieren von Schatten-und Indexereigenschaften in einem Entity Framework Core Modell
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: 180478212b683a271d2519cc1a4c79be5d3f11b9
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503188"
---
# <a name="shadow-and-indexer-properties"></a>Eigenschaften von Schatten und Indexer

Schatten Eigenschaften sind Eigenschaften, die nicht in der .net-Entitäts Klasse definiert sind, aber für diesen Entitätstyp im EF Core Modell definiert sind. Der Wert und der Status dieser Eigenschaften werden ausschließlich in der Änderungs Nachverfolgung beibehalten. Schatten Eigenschaften sind nützlich, wenn Daten in der Datenbank vorhanden sind, die für die zugeordneten Entitäts Typen nicht verfügbar gemacht werden sollen.

Indexer-Eigenschaften sind Entitätstyp Eigenschaften, die von einem [Indexer](/dotnet/csharp/programming-guide/indexers/) in der .net-Entitäts Klasse unterstützt werden. Auf Sie kann mithilfe des Indexers auf den .NET-Klassen Instanzen zugegriffen werden. Außerdem können Sie zusätzliche Eigenschaften zum Entitätstyp hinzufügen, ohne die CLR-Klasse zu ändern.

## <a name="foreign-key-shadow-properties"></a>Fremdschlüssel Schatten-Eigenschaften

Schatten Eigenschaften werden am häufigsten für Fremdschlüssel Eigenschaften verwendet, bei denen die Beziehung zwischen zwei Entitäten durch einen Fremdschlüssel Wert in der Datenbank dargestellt wird, die Beziehung jedoch für die Entitäts Typen mithilfe von Navigations Eigenschaften zwischen den Entitäts Typen verwaltet wird. Gemäß der Konvention führt EF eine Schatten Eigenschaft ein, wenn eine Beziehung entdeckt wird, aber in der abhängigen Entitäts Klasse keine Fremdschlüssel Eigenschaft gefunden wird.

Die-Eigenschaft wird benannt `<navigation property name><principal key property name>` (die Navigation auf der abhängigen Entität, die auf die Prinzipal Entität verweist, wird für die Benennung verwendet). Wenn der Name der Prinzipal Schlüsseleigenschaft den Namen der Navigations Eigenschaft enthält, ist der Name einfach `<principal key property name>` . Wenn für die abhängige Entität keine Navigations Eigenschaft vorhanden ist, wird der Prinzipal Typname an seiner Stelle verwendet.

Beispielsweise führt das folgende Codebeispiel dazu, dass eine `BlogId` Schatten Eigenschaft in die- `Post` Entität eingefügt wird:

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a>Konfigurieren von Schatten Eigenschaften

Sie können die fließende API verwenden, um Schatten Eigenschaften zu konfigurieren. Nachdem Sie die Zeichen folgen Überladung von aufgerufen haben `Property` , können Sie alle Konfigurations Aufrufe verketten, die für andere Eigenschaften gelten. Im folgenden Beispiel `Blog` `LastUpdated` wird eine Schatten Eigenschaft erstellt, da keine CLR-Eigenschaft mit dem Namen hat:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

Wenn der für die Methode angegebene Name mit `Property` dem Namen einer vorhandenen Eigenschaft übereinstimmt (eine Schatten Eigenschaft oder eine, die für die Entitäts Klasse definiert ist), konfiguriert der Code diese vorhandene Eigenschaft, anstatt eine neue Schatten Eigenschaft einzuführen.

## <a name="accessing-shadow-properties"></a>Zugreifen auf Schatten Eigenschaften

Schatten Eigenschaftswerte können über die API abgerufen und geändert werden `ChangeTracker` :

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

Auf Schatten Eigenschaften kann in LINQ-Abfragen über die `EF.Property` statische Methode verwiesen werden:

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

Auf Schatten Eigenschaften kann nicht nach einer Abfrage ohne Nachverfolgung zugegriffen werden, da die zurückgegebenen Entitäten nicht von der Änderungs Nachverfolgung nachverfolgt werden.

## <a name="configuring-indexer-properties"></a>Konfigurieren von Indexer-Eigenschaften

Sie können die fließende API verwenden, um Indexer-Eigenschaften zu konfigurieren. Nachdem Sie die-Methode aufgerufen haben `IndexerProperty` , können Sie alle Konfigurations Aufrufe verketten, die Sie für andere Eigenschaften durchsuchen. Im folgenden Beispiel `Blog` ist ein Indexer definiert, der zum Erstellen einer Indexer-Eigenschaft verwendet wird.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexerProperty.cs?name=ShadowProperty&highlight=3)]

Wenn der für die Methode angegebene Name mit `IndexerProperty` dem Namen einer vorhandenen Indexer-Eigenschaft übereinstimmt, wird diese vorhandene Eigenschaft vom Code konfiguriert. Wenn der Entitätstyp über eine Eigenschaft verfügt, die von einer Eigenschaft in der Entitäts Klasse unterstützt wird, wird eine Ausnahme ausgelöst, da auf Indexer-Eigenschaften nur über den Indexer zugegriffen werden muss.

## <a name="property-bag-entity-types"></a>Eigenschaften Behälter-Entitäts Typen

> [!NOTE]
> Unterstützung für Eigenschaften Behälter-Entitäts Typen wurde in EF Core 5,0 hinzugefügt.

Entitäts Typen, die nur Indexer-Eigenschaften enthalten, werden als Entitäts Typen für Eigenschaften Behälter bezeichnet. Diese Entitäts Typen haben keine Schatten Eigenschaften, stattdessen erstellt EF Indexer-Eigenschaften. Derzeit `Dictionary<string, object>` wird nur als Entitätstyp für Eigenschaften Behälter unterstützt. Er muss als frei gegebener Entitätstyp mit einem eindeutigen Namen konfiguriert werden, und die entsprechende `DbSet` Eigenschaft muss mithilfe eines- `Set` Aufrufes implementiert werden.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]
