---
title: Microsoft SQL Server Datenbankanbieter-Wert Generierung-EF Core
description: Für den SQL Server Entity Framework Core Datenbankanbieter spezifische Wert Generierungs Muster
author: roji
ms.date: 1/10/2020
uid: core/providers/sql-server/value-generation
ms.openlocfilehash: 90608f254a3d20e3c36586ae8325e0a982a7fa27
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987137"
---
# <a name="sql-server-value-generation"></a><span data-ttu-id="d645e-103">SQL Server Wert Generierung</span><span class="sxs-lookup"><span data-stu-id="d645e-103">SQL Server Value Generation</span></span>

<span data-ttu-id="d645e-104">Diese Seite enthält Details zur Konfiguration der Wert Generierung und Muster, die für den SQL Server-Anbieter spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="d645e-104">This page details value generation configuration  and patterns that are specific to the SQL Server provider.</span></span> <span data-ttu-id="d645e-105">Es wird empfohlen, zuerst [die Seite Allgemein der Wert Generierung](xref:core/modeling/generated-properties)zu lesen.</span><span class="sxs-lookup"><span data-stu-id="d645e-105">It's recommended to first read [the general page on value generation](xref:core/modeling/generated-properties).</span></span>

## <a name="identity-columns"></a><span data-ttu-id="d645e-106">IDENTITY-Spalten</span><span class="sxs-lookup"><span data-stu-id="d645e-106">IDENTITY columns</span></span>

<span data-ttu-id="d645e-107">Gemäß der Konvention werden numerische Spalten, die so konfiguriert sind, dass ihre Werte bei Add generiert werden, als [SQL Server Identitäts Spalten](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property)eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="d645e-107">By convention, numeric columns that are configured to have their values generated on add are set up as [SQL Server IDENTITY columns](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property).</span></span>

### <a name="seed-and-increment"></a><span data-ttu-id="d645e-108">Seed und Inkrement</span><span class="sxs-lookup"><span data-stu-id="d645e-108">Seed and increment</span></span>

<span data-ttu-id="d645e-109">Standardmäßig beginnen Identitäts Spalten bei 1 (dem Startwert) und werden jedes Mal um 1 erhöht, wenn eine Zeile hinzugefügt wird (das Inkrement).</span><span class="sxs-lookup"><span data-stu-id="d645e-109">By default, IDENTITY columns start off at 1 (the seed), and increment by 1 each time a row is added (the increment).</span></span> <span data-ttu-id="d645e-110">Sie können einen anderen Ausgangs Ausgangs-und Inkrement wie folgt konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="d645e-110">You can configure a different seed and increment as follows:</span></span>

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/IdentityOptionsContext.cs?name=IdentityOptions&highlight=5)]

> [!NOTE]
> <span data-ttu-id="d645e-111">Die Möglichkeit zum Konfigurieren des Identitäts Ausgangs und-Inkrements wurde in EF Core 3,0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="d645e-111">The ability to configure IDENTITY seed and increment was introduced in EF Core 3.0.</span></span>

### <a name="inserting-explicit-values-into-identity-columns"></a><span data-ttu-id="d645e-112">Einfügen von expliziten Werten in Identitäts Spalten</span><span class="sxs-lookup"><span data-stu-id="d645e-112">Inserting explicit values into IDENTITY columns</span></span>

<span data-ttu-id="d645e-113">Standardmäßig lässt SQL Server das Einfügen expliziter Werte in Identitäts Spalten nicht zu.</span><span class="sxs-lookup"><span data-stu-id="d645e-113">By default, SQL Server doesn't allow inserting explicit values into IDENTITY columns.</span></span> <span data-ttu-id="d645e-114">Zu diesem Zweck müssen Sie vor dem Aufrufen von manuell aktivieren `IDENTITY_INSERT` `SaveChanges()` , wie im folgenden beschrieben:</span><span class="sxs-lookup"><span data-stu-id="d645e-114">To do so, you must manually enable `IDENTITY_INSERT` before calling `SaveChanges()`, as follows:</span></span>

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/ExplicitIdentityValues.cs?name=ExplicitIdentityValues)]

> [!NOTE]
> <span data-ttu-id="d645e-115">Die Automatisierung dieses Vorgangs im SQL Server-Anbieter ist eine [Featureanforderung](https://github.com/aspnet/EntityFramework/issues/703), die sich im Backlog befindet.</span><span class="sxs-lookup"><span data-stu-id="d645e-115">We have a [feature request](https://github.com/aspnet/EntityFramework/issues/703) on our backlog to do this automatically within the SQL Server provider.</span></span>

## <a name="sequences"></a><span data-ttu-id="d645e-116">Sequenzen</span><span class="sxs-lookup"><span data-stu-id="d645e-116">Sequences</span></span>

<span data-ttu-id="d645e-117">Als Alternative zu Identitäts Spalten können Standardsequenzen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d645e-117">As an alternative to IDENTITY columns, you can use standard sequences.</span></span> <span data-ttu-id="d645e-118">Dies kann in verschiedenen Szenarien nützlich sein: Beispielsweise kann es sein, dass mehrere Spalten ihre Standardwerte aus einer einzelnen Sequenz zeichnen.</span><span class="sxs-lookup"><span data-stu-id="d645e-118">This can be useful in various scenarios; for example, you may want to have multiple columns drawing their default values from a single sequence.</span></span>

<span data-ttu-id="d645e-119">SQL Server ermöglicht das Erstellen von Sequenzen und deren Verwendung, wie [auf der Seite Allgemein in Sequenzen](xref:core/modeling/sequences)beschrieben.</span><span class="sxs-lookup"><span data-stu-id="d645e-119">SQL Server allows you to create sequences and use them as detailed in [the general page on sequences](xref:core/modeling/sequences).</span></span> <span data-ttu-id="d645e-120">Sie müssen ihre Eigenschaften so konfigurieren, dass Sie Sequenzen über verwenden `HasDefaultValueSql()` .</span><span class="sxs-lookup"><span data-stu-id="d645e-120">It's up to you to configure your properties to use sequences via `HasDefaultValueSql()`.</span></span>
