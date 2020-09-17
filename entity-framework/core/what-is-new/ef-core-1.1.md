---
title: Neuigkeiten in EF Core 1.1 – EF Core
description: Änderungen und Verbesserungen an Entity Framework Core 1.1
author: divega
ms.date: 10/27/2016
uid: core/what-is-new/ef-core-1.1
ms.openlocfilehash: 5f81e8b25feba5cdf5ae5e84b1d3362912ab5b26
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072407"
---
# <a name="new-features-in-ef-core-11"></a><span data-ttu-id="2a789-103">Neue Features in EF Core 1.1</span><span class="sxs-lookup"><span data-stu-id="2a789-103">New features in EF Core 1.1</span></span>

## <a name="modeling"></a><span data-ttu-id="2a789-104">Modellierung</span><span class="sxs-lookup"><span data-stu-id="2a789-104">Modeling</span></span>

### <a name="field-mapping"></a><span data-ttu-id="2a789-105">Feldzuordnung</span><span class="sxs-lookup"><span data-stu-id="2a789-105">Field mapping</span></span>

<span data-ttu-id="2a789-106">Ermöglicht Ihnen das Konfigurieren eines Unterstützungsfelds für eine Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="2a789-106">Allows you to configure a backing field for a property.</span></span> <span data-ttu-id="2a789-107">Dies kann für schreibgeschützte Eigenschaften oder Daten nützlich sein, die anstelle einer Eigenschaft Get/Set-Methoden verwenden.</span><span class="sxs-lookup"><span data-stu-id="2a789-107">This can be useful for read-only properties, or data that has Get/Set methods rather than a property.</span></span>

### <a name="mapping-to-memory-optimized-tables-in-sql-server"></a><span data-ttu-id="2a789-108">Zuordnung zu speicheroptimierten Tabellen in SQL Server</span><span class="sxs-lookup"><span data-stu-id="2a789-108">Mapping to Memory-Optimized Tables in SQL Server</span></span>

<span data-ttu-id="2a789-109">Sie können angeben, dass es sich bei der einer Entität zugeordneten Tabelle um eine speicheroptimierte Tabelle handelt.</span><span class="sxs-lookup"><span data-stu-id="2a789-109">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="2a789-110">Beim Einsatz von EF Core zum Erstellen und Verwalten einer auf Ihrem Modell basierenden Datenbank (entweder mit Migrationen oder `Database.EnsureCreated()`) wird eine speicheroptimierte Tabelle für diese Entitäten erstellt.</span><span class="sxs-lookup"><span data-stu-id="2a789-110">When using EF Core to create and maintain a database based on your model (either with migrations or `Database.EnsureCreated()`), a memory-optimized table will be created for these entities.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="2a789-111">Änderungsnachverfolgung</span><span class="sxs-lookup"><span data-stu-id="2a789-111">Change tracking</span></span>

### <a name="additional-change-tracking-apis-from-ef6"></a><span data-ttu-id="2a789-112">Zusätzliche APIs für die Änderungsnachverfolgung aus EF6</span><span class="sxs-lookup"><span data-stu-id="2a789-112">Additional change tracking APIs from EF6</span></span>

<span data-ttu-id="2a789-113">Hierzu gehören beispielsweise `Reload`, `GetModifiedProperties`, `GetDatabaseValues` usw.</span><span class="sxs-lookup"><span data-stu-id="2a789-113">Such as `Reload`, `GetModifiedProperties`, `GetDatabaseValues` etc.</span></span>

## <a name="query"></a><span data-ttu-id="2a789-114">Abfrage</span><span class="sxs-lookup"><span data-stu-id="2a789-114">Query</span></span>

### <a name="explicit-loading"></a><span data-ttu-id="2a789-115">Explizites Laden</span><span class="sxs-lookup"><span data-stu-id="2a789-115">Explicit Loading</span></span>

<span data-ttu-id="2a789-116">Ermöglicht es Ihnen, das Auffüllen einer Navigationseigenschaft oder einer Entität auszulösen, die zuvor aus der Datenbank geladen wurde.</span><span class="sxs-lookup"><span data-stu-id="2a789-116">Allows you to trigger population of a navigation property on an entity that was previously loaded from the database.</span></span>

### <a name="dbsetfind"></a><span data-ttu-id="2a789-117">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="2a789-117">DbSet.Find</span></span>

<span data-ttu-id="2a789-118">Bietet eine einfache Möglichkeit zum Abrufen einer Entität basierend auf dem Wert ihres Primärschlüssels.</span><span class="sxs-lookup"><span data-stu-id="2a789-118">Provides an easy way to fetch an entity based on its primary key value.</span></span>

## <a name="other"></a><span data-ttu-id="2a789-119">Andere</span><span class="sxs-lookup"><span data-stu-id="2a789-119">Other</span></span>

### <a name="connection-resiliency"></a><span data-ttu-id="2a789-120">Verbindungsstabilität</span><span class="sxs-lookup"><span data-stu-id="2a789-120">Connection resiliency</span></span>

<span data-ttu-id="2a789-121">Für nicht erfolgreich ausgeführte Datenbankbefehle werden automatisch Neuversuche ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2a789-121">Automatically retries failed database commands.</span></span> <span data-ttu-id="2a789-122">Dies ist insbesondere bei der Verbindungsherstellung mit SQL Azure nützlich, weil hier häufiger vorübergehende Fehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="2a789-122">This is especially useful when connection to SQL Azure, where transient failures are common.</span></span>

### <a name="simplified-service-replacement"></a><span data-ttu-id="2a789-123">Vereinfachte Dienstersetzung</span><span class="sxs-lookup"><span data-stu-id="2a789-123">Simplified service replacement</span></span>

<span data-ttu-id="2a789-124">Es ist jetzt einfacher, von EF verwendete interne Dienste zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="2a789-124">Makes it easier to replace internal services that EF uses.</span></span>
