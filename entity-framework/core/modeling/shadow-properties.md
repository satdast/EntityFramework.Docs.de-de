---
title: Schatten Eigenschaften-EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: b24ff85d8f5910a5625910e7225a94112d769824
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238280"
---
# <a name="shadow-properties"></a>Schatteneigenschaften

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

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

Auf Schatten Eigenschaften kann in LINQ-Abfragen über die `EF.Property` statische Methode verwiesen werden:

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

Auf Schatten Eigenschaften kann nicht nach einer Abfrage ohne Nachverfolgung zugegriffen werden, da die zurückgegebenen Entitäten nicht von der Änderungs Nachverfolgung nachverfolgt werden.
