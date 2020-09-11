---
title: Verwalten von Datenbankschemas – EF Core
description: Übersicht über Verwaltungsstrategien für Datenbankschemas mit Entity Framework Core
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/index
ms.openlocfilehash: e4f8c82125534e9e7e0c2de552bce336a544a2aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619498"
---
# <a name="managing-database-schemas"></a><span data-ttu-id="2d495-103">Verwalten von Datenbankschemas</span><span class="sxs-lookup"><span data-stu-id="2d495-103">Managing Database Schemas</span></span>

<span data-ttu-id="2d495-104">EF Core bietet zwei wesentliche Möglichkeiten, wie Sie Ihr EF Core-Modell und Ihr Datenbankschema synchron halten können. Um zwischen den beiden Optionen zu wählen, legen Sie fest, ob Ihr EF Core-Modell oder das Datenbankschema die einzige zuverlässige Datenquelle darstellt.</span><span class="sxs-lookup"><span data-stu-id="2d495-104">EF Core provides two primary ways of keeping your EF Core model and database schema in sync. To choose between the two, decide whether your EF Core model or the database schema is the source of truth.</span></span>

<span data-ttu-id="2d495-105">Wenn das EF Core-Modell die einzige zuverlässige Datenquelle darstellen soll, verwenden Sie [Migrationen][1].</span><span class="sxs-lookup"><span data-stu-id="2d495-105">If you want your EF Core model to be the source of truth, use [Migrations][1].</span></span> <span data-ttu-id="2d495-106">Wenn Sie Änderungen an Ihrem EF Core-Modell vornehmen, werden bei dieser Vorgehensweise schrittweise die entsprechenden Schemaänderungen an Ihrer Datenbank vorgenommen, um die Kompatibilität mit Ihrem EF Core-Modell aufrechtzuerhalten.</span><span class="sxs-lookup"><span data-stu-id="2d495-106">As you make changes to your EF Core model, this approach incrementally applies the corresponding schema changes to your database so that it remains compatible with your EF Core model.</span></span>

<span data-ttu-id="2d495-107">Verwenden Sie [Reverse Engineering][2], wenn Ihr Datenbankschema die einzige zuverlässige Datenquelle sein soll.</span><span class="sxs-lookup"><span data-stu-id="2d495-107">Use [Reverse Engineering][2] if you want your database schema to be the source of truth.</span></span> <span data-ttu-id="2d495-108">Diese Vorgehensweise ermöglicht es Ihnen, ein Gerüst für DbContext und die Entitätstypklassen durch Reverse Engineering Ihres Datenbankschemas in ein EF Core-Modell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2d495-108">This approach allows you to scaffold a DbContext and the entity type classes by reverse engineering your database schema into an EF Core model.</span></span>

> [!NOTE]
> <span data-ttu-id="2d495-109">Die [APIs zum Erstellen und Löschen][3] können ebenfalls das Datenbankschema anhand Ihres EF Core-Modells erstellen.</span><span class="sxs-lookup"><span data-stu-id="2d495-109">The [create and drop APIs][3] can also create the database schema from your EF Core model.</span></span> <span data-ttu-id="2d495-110">Diese werden jedoch in erster Linie für Tests, die Prototyperstellung und andere Szenarien eingesetzt, in denen die Datenbank gelegentlich gelöscht werden soll.</span><span class="sxs-lookup"><span data-stu-id="2d495-110">However, they are primarily for testing, prototyping, and other scenarios where dropping the database is acceptable.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/managing-schemas/scaffolding
  [3]: xref:core/managing-schemas/ensure-created
