---
title: 'Grundlegender Speichervorgang: EF Core'
description: Grundlegende Informationen zum Hinzufügen, Aktualisieren und Entfernen von Daten mit Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/basic
ms.openlocfilehash: c6288fc8ca79b78b6c377f25e3874211b38059b2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063633"
---
# <a name="basic-save"></a>Grundlegender Speichervorgang

Erfahren Sie, wie Sie Daten mithilfe Ihrer Kontext- und Entitätsklassen hinzufügen, ändern und entfernen können.

> [!TIP]  
> Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Basics/) finden Sie auf GitHub.

## <a name="adding-data"></a>Hinzufügen von Daten

Verwenden Sie die Methode *DbSet.Add*, um Ihren Entitätsklassen neue Instanzen hinzuzufügen. Die Daten werden in die Datenbank eingefügt, wenn Sie *SaveChanges* aufrufen.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Add)]

> [!TIP]  
> Die Methoden Add, Attach und Update wirken sich auf den vollständigen Graph an Entitäten aus, die an sie übergeben werden, wie im Abschnitt [Relevante Daten](xref:core/saving/related-data) beschrieben. Die Eigenschaft EntityEntry.State kann alternativ zum Festlegen des Zustands einer einzelnen Entität verwendet werden. Beispiel: `context.Entry(blog).State = EntityState.Modified`.

## <a name="updating-data"></a>Aktualisieren von Daten

EF erkennt automatisch Änderungen an einer vorhandenen Entität, die nach dem Kontext nachverfolgt wird. Dazu gehören Entitäten, die Sie aus der Datenbank laden oder abfragen, und Entitäten, die vorher der Datenbank hinzugefügt und darin gespeichert wurden.

Ändern Sie einfach die Werte der Eigenschaften, und rufen Sie dann *SaveChanges* auf.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a>Löschen von Daten

Verwenden Sie die Methode *DbSet.Remove*, um Instanzen Ihrer Entitätsklassen zu löschen.

Wenn die Entität bereits in der Datenbank vorhanden ist, wird sie während des Aufrufs von *SaveChanges* gelöscht. Wenn die Entität noch nicht in der Datenbank gespeichert wurde (d.h. sie wird als „hinzugefügt“ nachverfolgt), wird sie aus dem Kontext entfernt und nicht mehr eingefügt, wenn *SaveChanges* aufgerufen wird.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a>Mehrere Vorgänge in einem Aufruf von SaveChanges

Sie können Hinzufügungs-, Update- und Entfernungsvorgänge in einem einzelnen Aufruf von *SaveChanges* kombinieren.

> [!NOTE]  
> Für die meisten Datenbankanbieter ist *SaveChanges* transaktional. Das bedeutet, dass alle Vorgänge entweder erfolgreich oder gar nicht abgeschlossen werden und niemals nur teilweise angewendet werden.

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#MultipleOperations)]
