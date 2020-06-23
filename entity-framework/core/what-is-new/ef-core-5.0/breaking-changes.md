---
title: 'EF Core: Breaking Changes in EF Core 5.0'
author: bricelam
ms.date: 06/05/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 0ffe62d9b21dd901c2cdaf0ea5d416d14938497f
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84666168"
---
# <a name="breaking-changes-in-ef-core-50"></a>Breaking Changes in EF Core 5.0

Die folgenden API-Änderungen und Behavior Changes können dazu führen, dass vorhandene Anwendungen nach einem Upgrade auf EF Core 5.0.0 nicht mehr funktionieren.

## <a name="summary"></a>Zusammenfassung

| **Wichtige Änderung**                                                                                                               | **Auswirkungen** |
|:----------------------------------------------------------------------------------------------------------------------------------|------------|
| [Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt](#removed-hasgeometricdimension-method-from-sqlite-nts-extension) | Niedrig        |

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a>Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt.

[Issue 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

**Altes Verhalten**

Die HasGeometricDimension-Methode wurde dazu verwendet, zusätzliche Maße (Z und M) für Geometriespalten zu ermöglichen. Dies hat sich jedoch immer nur auf die Datenbankerstellung ausgewirkt. Das Angeben dieser Methode zum Abfragen von Werten mit zusätzlichen Maßen war nicht notwendig. Außerdem hat die Methode nicht ordnungsgemäß funktioniert, wenn Werte mit zusätzlichen Maßen eingefügt oder geändert wurden (siehe [Issue 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).

**Neues Verhalten**

Zum Ermöglichen des Einfügens und Änderns von Geometriewerten mit zusätzlichen Maßen (Z und M) muss das jeweilige Maß als Teil des Spaltentypnamens angegeben werden. Dies entspricht eher dem zugrunde liegenden Verhalten der AddGeometryColumn-Funktion von SpatiaLite.

**Hintergründe**

Die Verwendung der HasGeometricDimension-Methode, nachdem das Maß im Spaltentyp festgelegt wurde, ist redundant und nicht notwendig. Daher wurde die gesamte HasGeometricDimension-Methode entfernt.

**Vorbeugende Maßnahmen**

Verwenden Sie `HasColumnType` zum Festlegen der Maße:

```cs
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```
