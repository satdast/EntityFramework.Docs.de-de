---
title: Microsoft SQL Server Datenbankanbieter-Indizes-EF Core
description: Spezifische Index Features für den Entity Framework Core SQL Server-Anbieter
author: roji
ms.author: shrojans
ms.date: 9/1/2020
uid: core/providers/sql-server/indexes
ms.openlocfilehash: 3830377562fcc6a59239cd2c09d1419bbd00922e
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620703"
---
# <a name="index-features-specific-to-the-entity-framework-core-sql-server-provider"></a><span data-ttu-id="066ab-103">Spezifische Index Features für den Entity Framework Core SQL Server-Anbieter</span><span class="sxs-lookup"><span data-stu-id="066ab-103">Index features specific to the Entity Framework Core SQL Server provider</span></span>

<span data-ttu-id="066ab-104">Auf dieser Seite werden die Index Konfigurationsoptionen beschrieben, die für den SQL Server-Anbieter spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="066ab-104">This page details index configuration options that are specific to the SQL Server provider.</span></span>

## <a name="clustering"></a><span data-ttu-id="066ab-105">Clustering</span><span class="sxs-lookup"><span data-stu-id="066ab-105">Clustering</span></span>

<span data-ttu-id="066ab-106">Gruppierte Indizes sortieren und speichern die Datenzeilen in der Tabelle oder Sicht basierend auf ihren Schlüsselwerten.</span><span class="sxs-lookup"><span data-stu-id="066ab-106">Clustered indexes sort and store the data rows in the table or view based on their key values.</span></span> <span data-ttu-id="066ab-107">Wenn Sie den richtigen gruppierten Index für die Tabelle erstellen, kann die Geschwindigkeit Ihrer Abfragen erheblich verbessert werden, da die Daten bereits in der optimalen Reihenfolge angeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="066ab-107">Creating the right clustered index for your table can significantly improve the speed of your queries, as data is already laid out in the optimal order.</span></span> <span data-ttu-id="066ab-108">Pro Tabelle kann nur ein gruppierter Index vorhanden sein, da die Datenzeilen nur in einer Reihenfolge gespeichert werden können.</span><span class="sxs-lookup"><span data-stu-id="066ab-108">There can be only one clustered index per table, because the data rows themselves can be stored in only one order.</span></span> <span data-ttu-id="066ab-109">Weitere Informationen finden Sie [in der SQL Server-Dokumentation zu gruppierten und nicht gruppierten Indizes](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).</span><span class="sxs-lookup"><span data-stu-id="066ab-109">For more information, see [the SQL Server documentation on clustered and non-clustered indexes](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).</span></span>

<span data-ttu-id="066ab-110">Standardmäßig wird die Primärschlüssel Spalte einer Tabelle implizit durch einen gruppierten Index gestützt, und alle anderen Indizes sind nicht gruppiert.</span><span class="sxs-lookup"><span data-stu-id="066ab-110">By default, the primary key column of a table is implicitly backed by a clustered index, and all other indexes are non-clustered.</span></span>

<span data-ttu-id="066ab-111">Sie können einen Index oder Schlüssel so konfigurieren, dass er wie folgt gruppiert wird:</span><span class="sxs-lookup"><span data-stu-id="066ab-111">You can configure an index or key to be clustered as follows:</span></span>

[!code-csharp[ClusteredIndex](../../../../samples/core/SqlServer/Indexes/ClusteredIndexContext.cs?name=ClusteredIndex)]

## <a name="fill-factor"></a><span data-ttu-id="066ab-112">Füllfaktor</span><span class="sxs-lookup"><span data-stu-id="066ab-112">Fill factor</span></span>

> [!NOTE]
> <span data-ttu-id="066ab-113">Diese Funktion wird in EF Core 5.0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="066ab-113">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="066ab-114">Die Füllfaktor Option Index wird für die Feinabstimmung der Speicherung und Leistung von Indexdaten bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="066ab-114">The index fill-factor option is provided for fine-tuning index data storage and performance.</span></span> <span data-ttu-id="066ab-115">Weitere Informationen finden [Sie in der SQL Server-Dokumentation zum Füllfaktor](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).</span><span class="sxs-lookup"><span data-stu-id="066ab-115">For more information, see [the SQL Server documentation on fill factor](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).</span></span>

<span data-ttu-id="066ab-116">Sie können den Füllfaktor eines Indexes wie folgt konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="066ab-116">You can configure an index's fill factor as follows:</span></span>

[!code-csharp[IndexFillFactor](../../../../samples/core/SqlServer/Indexes/IndexFillFactorContext.cs?name=IndexFillFactor)]

## <a name="online-creation"></a><span data-ttu-id="066ab-117">Online Erstellung</span><span class="sxs-lookup"><span data-stu-id="066ab-117">Online creation</span></span>

<span data-ttu-id="066ab-118">Die Online-Option ermöglicht gleichzeitigen Benutzern den Zugriff auf die zugrunde liegenden Tabellen-oder gruppierten Indexdaten und alle zugehörigen nicht gruppierten Indizes während der Indexerstellung, sodass Benutzer die zugrunde liegenden Daten weiterhin aktualisieren und Abfragen können.</span><span class="sxs-lookup"><span data-stu-id="066ab-118">The ONLINE option allows concurrent user access to the underlying table or clustered index data and any associated nonclustered indexes during index creation, so that users can continue to update and query the underlying data.</span></span> <span data-ttu-id="066ab-119">Wenn Sie DDL-Vorgänge (Datendefinitionssprache) wie das Erstellen oder Neuerstellen eines gruppierten Indexes offline ausführen, richten diese Vorgänge exklusive Sperren für die dem Index zugrunde liegenden Daten und damit verbundene Indizes ein.</span><span class="sxs-lookup"><span data-stu-id="066ab-119">When you perform data definition language (DDL) operations offline, such as building or rebuilding a clustered index; these operations hold exclusive locks on the underlying data and associated indexes.</span></span> <span data-ttu-id="066ab-120">Weitere Informationen finden Sie [in der SQL Server-Dokumentation der Online Index Option](/sql/relational-databases/indexes/perform-index-operations-online).</span><span class="sxs-lookup"><span data-stu-id="066ab-120">For more information, see [the SQL Server documentation on the ONLINE index option](/sql/relational-databases/indexes/perform-index-operations-online).</span></span>

<span data-ttu-id="066ab-121">Sie können einen Index mit der Online-Option wie folgt konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="066ab-121">You can configure an index with the ONLINE option as follows:</span></span>

[!code-csharp[IndexOnline](../../../../samples/core/SqlServer/Indexes/IndexOnlineContext.cs?name=IndexOnline)]
