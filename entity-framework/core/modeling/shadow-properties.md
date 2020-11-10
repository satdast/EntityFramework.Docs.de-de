---
title: Eigenschaften von Schatten und Indexer-EF Core
description: Konfigurieren von Schatten-und Indexereigenschaften in einem Entity Framework Core Modell
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: f03dc000bb111253ae74c05a668703f2e6237a57
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430416"
---
# <a name="shadow-and-indexer-properties"></a>Eigenschaften von Schatten und Indexer

Schatten Eigenschaften sind Eigenschaften, die nicht in der .net-Entitäts Klasse definiert sind, aber für diesen Entitätstyp im EF Core Modell definiert sind. Der Wert und der Status dieser Eigenschaften werden ausschließlich in der Änderungs Nachverfolgung beibehalten. Schatten Eigenschaften sind nützlich, wenn Daten in der Datenbank vorhanden sind, die für die zugeordneten Entitäts Typen nicht verfügbar gemacht werden sollen.

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

## <a name="property-bag-entity-types"></a>Eigenschaften Behälter-Entitäts Typen

> [!NOTE]
> Unterstützung für Eigenschaften Behälter-Entitäts Typen wurde in EF Core 5,0 hinzugefügt.

Entitäts Typen, die nur Indexer-Eigenschaften enthalten, werden als Entitäts Typen für Eigenschaften Behälter bezeichnet. Diese Entitäts Typen haben keine Schatten Eigenschaften. Derzeit `Dictionary<string, object>` wird nur als Entitätstyp für Eigenschaften Behälter unterstützt. Dies bedeutet, dass Sie als frei gegebener Entitätstyp mit einem eindeutigen Namen konfiguriert werden muss, und die entsprechende `DbSet` Eigenschaft muss mithilfe eines- `Set` Aufrufes implementiert werden.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]
