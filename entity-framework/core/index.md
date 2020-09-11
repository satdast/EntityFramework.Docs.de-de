---
title: Überblick über Entity Framework Core – EF Core
description: Allgemeine Einführungsübersicht für Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: 4816f2d57590ddd2ad4807daa2cc1c5000cd5713
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619481"
---
# <a name="entity-framework-core"></a><span data-ttu-id="74ca5-103">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="74ca5-103">Entity Framework Core</span></span>

<span data-ttu-id="74ca5-104">Entity Framework Core (EF Core) ist eine einfache, erweiterbare und plattformübergreifende [Open Source](https://github.com/aspnet/EntityFrameworkCore)-Version der beliebten Entity Framework-Datenzugriffstechnologie.</span><span class="sxs-lookup"><span data-stu-id="74ca5-104">Entity Framework (EF) Core is a lightweight, extensible, [open source](https://github.com/aspnet/EntityFrameworkCore) and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="74ca5-105">EF Core kann als objektrelationaler Mapper (O/RM) eingesetzt werden und bietet .NET-Entwicklern so die Möglichkeit, unter Verwendung von .NET-Objekten mit einer Datenbank zu arbeiten. Auf diese Weise entfällt ein Großteil des Datenzugriffscodes, der üblicherweise geschrieben werden muss.</span><span class="sxs-lookup"><span data-stu-id="74ca5-105">EF Core can serve as an object-relational mapper (O/RM), enabling .NET developers to work with a database using .NET objects, and eliminating the need for most of the data-access code they usually need to write.</span></span>

<span data-ttu-id="74ca5-106">Einzelheiten zu den von EF Core unterstützten Datenbank-Engines finden Sie unter [Datenbankanbieter](xref:core/providers/index).</span><span class="sxs-lookup"><span data-stu-id="74ca5-106">EF Core supports many database engines, see [Database Providers](xref:core/providers/index) for details.</span></span>

## <a name="the-model"></a><span data-ttu-id="74ca5-107">Das Modell</span><span class="sxs-lookup"><span data-stu-id="74ca5-107">The Model</span></span>

<span data-ttu-id="74ca5-108">Bei EF Core erfolgt der Datenzugriff über ein Modell.</span><span class="sxs-lookup"><span data-stu-id="74ca5-108">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="74ca5-109">Ein Modell setzt sich aus Entitätsklassen und einem Kontextobjekt zusammen, das eine Sitzung mit der Datenbank darstellt und Ihnen das Abfragen und Speichern von Daten ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="74ca5-109">A model is made up of entity classes and a context object that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="74ca5-110">Weitere Informationen finden Sie unter [Erstellen eines Modells](xref:core/modeling/index).</span><span class="sxs-lookup"><span data-stu-id="74ca5-110">See [Creating a Model](xref:core/modeling/index) to learn more.</span></span>

<span data-ttu-id="74ca5-111">Sie können ein Modell aus einer vorhandenen Datenbank generieren, ein Modell manuell entsprechend Ihrer Datenbank codieren oder mithilfe von [EF-Migrationen](xref:core/managing-schemas/migrations/index) eine Datenbank anhand Ihres Modells erstellen und sie im Laufe der Zeit gemäß Ihres Modells weiterentwickeln.</span><span class="sxs-lookup"><span data-stu-id="74ca5-111">You can generate a model from an existing database, hand code a model to match your database, or use [EF Migrations](xref:core/managing-schemas/migrations/index) to create a database from your model, and then evolve it as your model changes over time.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a><span data-ttu-id="74ca5-112">Abfragen</span><span class="sxs-lookup"><span data-stu-id="74ca5-112">Querying</span></span>

<span data-ttu-id="74ca5-113">Instanzen Ihrer Entitätsklassen werden mit Language Integrated Query (LINQ) von der Datenbank abgerufen.</span><span class="sxs-lookup"><span data-stu-id="74ca5-113">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="74ca5-114">Weitere Informationen finden Sie unter [Abfragen von Daten](xref:core/querying/index).</span><span class="sxs-lookup"><span data-stu-id="74ca5-114">See [Querying Data](xref:core/querying/index) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a><span data-ttu-id="74ca5-115">Speichern von Daten</span><span class="sxs-lookup"><span data-stu-id="74ca5-115">Saving Data</span></span>

<span data-ttu-id="74ca5-116">Daten werden in der Datenbank mithilfe von Instanzen Ihrer Entitätsklassen erstellt, gelöscht und geändert.</span><span class="sxs-lookup"><span data-stu-id="74ca5-116">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="74ca5-117">Weitere Informationen finden Sie unter [Speichern von Daten](xref:core/saving/index).</span><span class="sxs-lookup"><span data-stu-id="74ca5-117">See [Saving Data](xref:core/saving/index) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="next-steps"></a><span data-ttu-id="74ca5-118">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="74ca5-118">Next steps</span></span>

<span data-ttu-id="74ca5-119">Einführungstutorials finden Sie unter [Erste Schritte mit Entity Framework Core](xref:core/get-started/index).</span><span class="sxs-lookup"><span data-stu-id="74ca5-119">For introductory tutorials, see [Getting Started with Entity Framework Core](xref:core/get-started/index).</span></span>
