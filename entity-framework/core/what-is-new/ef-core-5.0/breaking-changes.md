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
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="18ccc-102">Breaking Changes in EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="18ccc-102">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="18ccc-103">Die folgenden API-Änderungen und Behavior Changes können dazu führen, dass vorhandene Anwendungen nach einem Upgrade auf EF Core 5.0.0 nicht mehr funktionieren.</span><span class="sxs-lookup"><span data-stu-id="18ccc-103">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="18ccc-104">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="18ccc-104">Summary</span></span>

| <span data-ttu-id="18ccc-105">**Wichtige Änderung**</span><span class="sxs-lookup"><span data-stu-id="18ccc-105">**Breaking change**</span></span>                                                                                                               | <span data-ttu-id="18ccc-106">**Auswirkungen**</span><span class="sxs-lookup"><span data-stu-id="18ccc-106">**Impact**</span></span> |
|:----------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="18ccc-107">Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt</span><span class="sxs-lookup"><span data-stu-id="18ccc-107">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#removed-hasgeometricdimension-method-from-sqlite-nts-extension) | <span data-ttu-id="18ccc-108">Niedrig</span><span class="sxs-lookup"><span data-stu-id="18ccc-108">Low</span></span>        |

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="18ccc-109">Die HasGeometricDimension-Methode wurde aus der SQLite NTS-Erweiterung entfernt.</span><span class="sxs-lookup"><span data-stu-id="18ccc-109">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="18ccc-110">Issue 14257</span><span class="sxs-lookup"><span data-stu-id="18ccc-110">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="18ccc-111">**Altes Verhalten**</span><span class="sxs-lookup"><span data-stu-id="18ccc-111">**Old behavior**</span></span>

<span data-ttu-id="18ccc-112">Die HasGeometricDimension-Methode wurde dazu verwendet, zusätzliche Maße (Z und M) für Geometriespalten zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="18ccc-112">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="18ccc-113">Dies hat sich jedoch immer nur auf die Datenbankerstellung ausgewirkt.</span><span class="sxs-lookup"><span data-stu-id="18ccc-113">However, it only ever affected database creation.</span></span> <span data-ttu-id="18ccc-114">Das Angeben dieser Methode zum Abfragen von Werten mit zusätzlichen Maßen war nicht notwendig.</span><span class="sxs-lookup"><span data-stu-id="18ccc-114">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="18ccc-115">Außerdem hat die Methode nicht ordnungsgemäß funktioniert, wenn Werte mit zusätzlichen Maßen eingefügt oder geändert wurden (siehe [Issue 14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span><span class="sxs-lookup"><span data-stu-id="18ccc-115">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="18ccc-116">**Neues Verhalten**</span><span class="sxs-lookup"><span data-stu-id="18ccc-116">**New behavior**</span></span>

<span data-ttu-id="18ccc-117">Zum Ermöglichen des Einfügens und Änderns von Geometriewerten mit zusätzlichen Maßen (Z und M) muss das jeweilige Maß als Teil des Spaltentypnamens angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="18ccc-117">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="18ccc-118">Dies entspricht eher dem zugrunde liegenden Verhalten der AddGeometryColumn-Funktion von SpatiaLite.</span><span class="sxs-lookup"><span data-stu-id="18ccc-118">This more closely matches the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="18ccc-119">**Hintergründe**</span><span class="sxs-lookup"><span data-stu-id="18ccc-119">**Why**</span></span>

<span data-ttu-id="18ccc-120">Die Verwendung der HasGeometricDimension-Methode, nachdem das Maß im Spaltentyp festgelegt wurde, ist redundant und nicht notwendig. Daher wurde die gesamte HasGeometricDimension-Methode entfernt.</span><span class="sxs-lookup"><span data-stu-id="18ccc-120">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="18ccc-121">**Vorbeugende Maßnahmen**</span><span class="sxs-lookup"><span data-stu-id="18ccc-121">**Mitigations**</span></span>

<span data-ttu-id="18ccc-122">Verwenden Sie `HasColumnType` zum Festlegen der Maße:</span><span class="sxs-lookup"><span data-stu-id="18ccc-122">Use `HasColumnType` to specify the dimension:</span></span>

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
