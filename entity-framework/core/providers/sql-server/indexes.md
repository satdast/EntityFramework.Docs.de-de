---
title: Microsoft SQL Server Datenbankanbieter-Indizes-EF Core
description: Spezifische Index Features für den Entity Framework Core SQL Server-Anbieter
author: roji
ms.date: 9/1/2020
uid: core/providers/sql-server/indexes
ms.openlocfilehash: 42411a562b4741ba39b4eb855bb84c66e100456b
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129160"
---
# <a name="index-features-specific-to-the-entity-framework-core-sql-server-provider"></a><span data-ttu-id="1bf94-103">Spezifische Index Features für den Entity Framework Core SQL Server-Anbieter</span><span class="sxs-lookup"><span data-stu-id="1bf94-103">Index features specific to the Entity Framework Core SQL Server provider</span></span>

<span data-ttu-id="1bf94-104">Auf dieser Seite werden die Index Konfigurationsoptionen beschrieben, die für den SQL Server-Anbieter spezifisch sind.</span><span class="sxs-lookup"><span data-stu-id="1bf94-104">This page details index configuration options that are specific to the SQL Server provider.</span></span>

## <a name="clustering"></a><span data-ttu-id="1bf94-105">Clustering</span><span class="sxs-lookup"><span data-stu-id="1bf94-105">Clustering</span></span>

<span data-ttu-id="1bf94-106">Gruppierte Indizes sortieren und speichern die Datenzeilen in der Tabelle oder Sicht basierend auf ihren Schlüsselwerten.</span><span class="sxs-lookup"><span data-stu-id="1bf94-106">Clustered indexes sort and store the data rows in the table or view based on their key values.</span></span> <span data-ttu-id="1bf94-107">Wenn Sie den richtigen gruppierten Index für die Tabelle erstellen, kann die Geschwindigkeit Ihrer Abfragen erheblich verbessert werden, da die Daten bereits in der optimalen Reihenfolge angeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="1bf94-107">Creating the right clustered index for your table can significantly improve the speed of your queries, as data is already laid out in the optimal order.</span></span> <span data-ttu-id="1bf94-108">Pro Tabelle kann nur ein gruppierter Index vorhanden sein, da die Datenzeilen nur in einer Reihenfolge gespeichert werden können.</span><span class="sxs-lookup"><span data-stu-id="1bf94-108">There can be only one clustered index per table, because the data rows themselves can be stored in only one order.</span></span> <span data-ttu-id="1bf94-109">Weitere Informationen finden Sie [in der SQL Server-Dokumentation zu gruppierten und nicht gruppierten Indizes](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).</span><span class="sxs-lookup"><span data-stu-id="1bf94-109">For more information, see [the SQL Server documentation on clustered and non-clustered indexes](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described).</span></span>

<span data-ttu-id="1bf94-110">Standardmäßig wird die Primärschlüssel Spalte einer Tabelle implizit durch einen gruppierten Index gestützt, und alle anderen Indizes sind nicht gruppiert.</span><span class="sxs-lookup"><span data-stu-id="1bf94-110">By default, the primary key column of a table is implicitly backed by a clustered index, and all other indexes are non-clustered.</span></span>

<span data-ttu-id="1bf94-111">Sie können einen Index oder Schlüssel so konfigurieren, dass er wie folgt gruppiert wird:</span><span class="sxs-lookup"><span data-stu-id="1bf94-111">You can configure an index or key to be clustered as follows:</span></span>

[!code-csharp[ClusteredIndex](../../../../samples/core/SqlServer/Indexes/ClusteredIndexContext.cs?name=ClusteredIndex)]

> [!NOTE]
> <span data-ttu-id="1bf94-112">SQL Server unterstützt nur einen gruppierten Index pro Tabelle, und der Primärschlüssel ist standardmäßig gruppiert.</span><span class="sxs-lookup"><span data-stu-id="1bf94-112">SQL Server only supports one clustered index per table, and the primary key is by default clustered.</span></span> <span data-ttu-id="1bf94-113">Wenn Sie einen gruppierten Index für eine nicht Schlüssel Spalte erstellen möchten, müssen Sie den Schlüssel explizit als nicht gruppierten Schlüssel festlegen.</span><span class="sxs-lookup"><span data-stu-id="1bf94-113">If you'd like to have a clustered index on a non-key column, you must explicitly make your key non-clustered.</span></span>

## <a name="fill-factor"></a><span data-ttu-id="1bf94-114">Füllfaktor</span><span class="sxs-lookup"><span data-stu-id="1bf94-114">Fill factor</span></span>

> [!NOTE]
> <span data-ttu-id="1bf94-115">Dieses Feature wurde in EF Core 5.0 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="1bf94-115">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="1bf94-116">Die Füllfaktor Option Index wird für die Feinabstimmung der Speicherung und Leistung von Indexdaten bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1bf94-116">The index fill-factor option is provided for fine-tuning index data storage and performance.</span></span> <span data-ttu-id="1bf94-117">Weitere Informationen finden [Sie in der SQL Server-Dokumentation zum Füllfaktor](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).</span><span class="sxs-lookup"><span data-stu-id="1bf94-117">For more information, see [the SQL Server documentation on fill factor](/sql/relational-databases/indexes/specify-fill-factor-for-an-index).</span></span>

<span data-ttu-id="1bf94-118">Sie können den Füllfaktor eines Indexes wie folgt konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="1bf94-118">You can configure an index's fill factor as follows:</span></span>

[!code-csharp[IndexFillFactor](../../../../samples/core/SqlServer/Indexes/IndexFillFactorContext.cs?name=IndexFillFactor)]

## <a name="online-creation"></a><span data-ttu-id="1bf94-119">Online Erstellung</span><span class="sxs-lookup"><span data-stu-id="1bf94-119">Online creation</span></span>

<span data-ttu-id="1bf94-120">Die Online-Option ermöglicht gleichzeitigen Benutzern den Zugriff auf die zugrunde liegenden Tabellen-oder gruppierten Indexdaten und alle zugehörigen nicht gruppierten Indizes während der Indexerstellung, sodass Benutzer die zugrunde liegenden Daten weiterhin aktualisieren und Abfragen können.</span><span class="sxs-lookup"><span data-stu-id="1bf94-120">The ONLINE option allows concurrent user access to the underlying table or clustered index data and any associated nonclustered indexes during index creation, so that users can continue to update and query the underlying data.</span></span> <span data-ttu-id="1bf94-121">Wenn Sie DDL-Vorgänge (Datendefinitionssprache) wie das Erstellen oder Neuerstellen eines gruppierten Indexes offline ausführen, richten diese Vorgänge exklusive Sperren für die dem Index zugrunde liegenden Daten und damit verbundene Indizes ein.</span><span class="sxs-lookup"><span data-stu-id="1bf94-121">When you perform data definition language (DDL) operations offline, such as building or rebuilding a clustered index; these operations hold exclusive locks on the underlying data and associated indexes.</span></span> <span data-ttu-id="1bf94-122">Weitere Informationen finden Sie [in der SQL Server-Dokumentation der Online Index Option](/sql/relational-databases/indexes/perform-index-operations-online).</span><span class="sxs-lookup"><span data-stu-id="1bf94-122">For more information, see [the SQL Server documentation on the ONLINE index option](/sql/relational-databases/indexes/perform-index-operations-online).</span></span>

<span data-ttu-id="1bf94-123">Sie können einen Index mit der Online-Option wie folgt konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="1bf94-123">You can configure an index with the ONLINE option as follows:</span></span>

[!code-csharp[IndexOnline](../../../../samples/core/SqlServer/Indexes/IndexOnlineContext.cs?name=IndexOnline)]
