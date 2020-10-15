---
title: Erste Schritte mit Entity Framework 6 – EF6
description: Erste Schritte mit Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/get-started
ms.openlocfilehash: b518b8ec70045066ffce6ac2a32136df97e14e99
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062788"
---
# <a name="get-started-with-entity-framework-6"></a><span data-ttu-id="38046-103">Erste Schritte mit Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="38046-103">Get started with Entity Framework 6</span></span>

<span data-ttu-id="38046-104">Dieser Leitfaden enthält Links zu ausgewählten Dokumentationsartikeln, exemplarischen Vorgehensweisen und Videos, die Ihnen beim schnellen Einstieg helfen können.</span><span class="sxs-lookup"><span data-stu-id="38046-104">This guide contains a collection of links to selected documentation articles, walkthroughs and videos that can help you get started quickly.</span></span>

## <a name="fundamentals"></a><span data-ttu-id="38046-105">Grundlagen</span><span class="sxs-lookup"><span data-stu-id="38046-105">Fundamentals</span></span>

* [<span data-ttu-id="38046-106">Abrufen von Entity Framework</span><span class="sxs-lookup"><span data-stu-id="38046-106">Get Entity Framework</span></span>](xref:ef6/fundamentals/install)

  <span data-ttu-id="38046-107">Hier erfahren Sie, wie Sie Entity Framework zu Ihren Anwendungen hinzufügen können. Wenn Sie den EF-Designer verwenden möchten, dann installieren Sie ihn in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="38046-107">Here you will learn how to add Entity Framework to your applications and, if you want to use the EF Designer, make sure you get it installed in Visual Studio.</span></span>

* [<span data-ttu-id="38046-108">Erstellen eines Modells: Code First, der EF-Designer und die EF-Workflows</span><span class="sxs-lookup"><span data-stu-id="38046-108">Creating a Model: Code First, the EF Designer, and the EF Workflows</span></span>](xref:ef6/modeling/index)

  <span data-ttu-id="38046-109">Möchten Sie Ihr EF-Modell lieber durch Schreiben von Code oder Zeichnen von Feldern und Linien entwerfen?</span><span class="sxs-lookup"><span data-stu-id="38046-109">Do you prefer to specify your EF model writing code or drawing boxes and lines?</span></span>
<span data-ttu-id="38046-110">Werden Sie EF verwenden, um Ihre Objekte einer vorhandenen Datenbank zuzuweisen oder soll EF eine Datenbank erstellen, die perfekt auf Ihre Objekte abgestimmt ist?</span><span class="sxs-lookup"><span data-stu-id="38046-110">Are you going to use EF to map your objects to an existing database or would you like EF to create a database tailored for your objects?</span></span>
<span data-ttu-id="38046-111">Hier erfahren Sie mehr zu den zwei Ansätzen zur Verwendung von EF6: EF-Designer und Code First.</span><span class="sxs-lookup"><span data-stu-id="38046-111">Here you learn about two different approaches to use EF6: EF Designer and Code First.</span></span>
<span data-ttu-id="38046-112">Verfolgen Sie die Diskussion, und sehen Sie sich das Video zu den Unterschieden an.</span><span class="sxs-lookup"><span data-stu-id="38046-112">Make sure you follow the discussion and watch the video about the difference.</span></span>

* [<span data-ttu-id="38046-113">Arbeiten mit DbContext</span><span class="sxs-lookup"><span data-stu-id="38046-113">Working with DbContext</span></span>](xref:ef6/fundamentals/working-with-dbcontext)

  <span data-ttu-id="38046-114">DBContext ist der erste und auch wichtigste EF-Typ, dessen Verwendung Sie kennen müssen.</span><span class="sxs-lookup"><span data-stu-id="38046-114">DbContext is the first and most important EF type that you need to learn how to use.</span></span> <span data-ttu-id="38046-115">Er dient als Launchpad für Datenbankabfragen und verfolgt Änderungen, die Sie an Objekten vornehmen, damit sie in die Datenbank zurückgespeichert werden können.</span><span class="sxs-lookup"><span data-stu-id="38046-115">It serves as the launchpad for database queries and keeps track of changes you make to objects so that they can be persisted back to the database.</span></span>

* [<span data-ttu-id="38046-116">Frage stellen</span><span class="sxs-lookup"><span data-stu-id="38046-116">Ask a Question</span></span>](xref:ef6/resources/get-help)

  <span data-ttu-id="38046-117">Finden Sie heraus, wie Sie Hilfe von Experten erhalten und mit Ihren eigenen Antworten zur Community beitragen können.</span><span class="sxs-lookup"><span data-stu-id="38046-117">Find out how to get help from the experts and contribute your own answers to the community.</span></span>

* [<span data-ttu-id="38046-118">Mitwirken</span><span class="sxs-lookup"><span data-stu-id="38046-118">Contribute</span></span>](https://github.com/aspnet/EntityFramework6/)

  <span data-ttu-id="38046-119">Entity Framework 6 verwendet ein offenes Entwicklungsmodell.</span><span class="sxs-lookup"><span data-stu-id="38046-119">Entity Framework 6 uses an open development model.</span></span> <span data-ttu-id="38046-120">Erfahren Sie im GitHub-Repository, wie Sie EF noch besser machen können.</span><span class="sxs-lookup"><span data-stu-id="38046-120">Find out how you can help make EF even better by visiting our GitHub repository.</span></span>

## <a name="code-first-resources"></a><span data-ttu-id="38046-121">Code First-Ressourcen</span><span class="sxs-lookup"><span data-stu-id="38046-121">Code First resources</span></span>

  - [<span data-ttu-id="38046-122">Code First mit einer vorhandenen Datenbank</span><span class="sxs-lookup"><span data-stu-id="38046-122">Code First to an Existing Database Workflow</span></span>](xref:ef6/modeling/code-first/workflows/existing-database)
  - [<span data-ttu-id="38046-123">Code First für eine neue Datenbank</span><span class="sxs-lookup"><span data-stu-id="38046-123">Code First to a New Database Workflow</span></span>](xref:ef6/modeling/code-first/workflows/new-database)
  - [<span data-ttu-id="38046-124">Unterstützung von Enumerationen – Code First</span><span class="sxs-lookup"><span data-stu-id="38046-124">Mapping Enums Using Code First</span></span>](xref:ef6/modeling/code-first/data-types/enums)
  - [<span data-ttu-id="38046-125">Mapping Spatial Types Using Code First (Zuordnen von räumlichen Typen mit Code First)</span><span class="sxs-lookup"><span data-stu-id="38046-125">Mapping Spatial Types Using Code First</span></span>](xref:ef6/modeling/code-first/data-types/spatial)
  - [<span data-ttu-id="38046-126">Benutzerdefinierte Code First-Konventionen</span><span class="sxs-lookup"><span data-stu-id="38046-126">Writing Custom Code First Conventions</span></span>](xref:ef6/modeling/code-first/conventions/custom)
  - [<span data-ttu-id="38046-127">Using Code First Fluent Configuration with Visual Basic (Verwenden der Fluent-Konfiguration von Code First für Visual Basic)</span><span class="sxs-lookup"><span data-stu-id="38046-127">Using Code First Fluent Configuration with Visual Basic</span></span>](xref:ef6/modeling/code-first/fluent/vb)
  - [<span data-ttu-id="38046-128">Code First-Migrationen</span><span class="sxs-lookup"><span data-stu-id="38046-128">Code First Migrations</span></span>](xref:ef6/modeling/code-first/migrations/index)
  - [<span data-ttu-id="38046-129">Code First Migrations in Team Environments (Code First-Migrationen in Teamumgebungen)</span><span class="sxs-lookup"><span data-stu-id="38046-129">Code First Migrations in Team Environments</span></span>](xref:ef6/modeling/code-first/migrations/teams)
  - <span data-ttu-id="38046-130">[Automatische Code First-Migrationen](xref:ef6/modeling/code-first/migrations/automatic) (wird nicht länger empfohlen)</span><span class="sxs-lookup"><span data-stu-id="38046-130">[Automatic Code First Migrations](xref:ef6/modeling/code-first/migrations/automatic) (This is no longer recommended)</span></span>

## <a name="ef-designer-resources"></a><span data-ttu-id="38046-131">EF Designer-Ressourcen</span><span class="sxs-lookup"><span data-stu-id="38046-131">EF Designer resources</span></span>
  - [<span data-ttu-id="38046-132">Database First-Workflow</span><span class="sxs-lookup"><span data-stu-id="38046-132">Database First Workflow</span></span>](xref:ef6/modeling/designer/workflows/database-first)
  - [<span data-ttu-id="38046-133">Model First-Workflow</span><span class="sxs-lookup"><span data-stu-id="38046-133">Model First Workflow</span></span>](xref:ef6/modeling/designer/workflows/model-first)
  - [<span data-ttu-id="38046-134">Mapping Enums (Zuordnen von Enumerationen)</span><span class="sxs-lookup"><span data-stu-id="38046-134">Mapping Enums</span></span>](xref:ef6/modeling/designer/data-types/enums)
  - [<span data-ttu-id="38046-135">Mapping Spatial Types (Zuordnen von räumlichen Typen)</span><span class="sxs-lookup"><span data-stu-id="38046-135">Mapping Spatial Types</span></span>](xref:ef6/modeling/designer/data-types/spatial)
  - [<span data-ttu-id="38046-136">Table-Per Hierarchy Inheritance Mapping (Vererbungszuordnung einer Tabelle pro Hierarchie)</span><span class="sxs-lookup"><span data-stu-id="38046-136">Table-Per Hierarchy Inheritance Mapping</span></span>](xref:ef6/modeling/designer/inheritance/tph)
  - [<span data-ttu-id="38046-137">Table-Per Type Inheritance Mapping („Tabelle pro Typ“-Vererbungszuordnung)</span><span class="sxs-lookup"><span data-stu-id="38046-137">Table-Per Type Inheritance Mapping</span></span>](xref:ef6/modeling/designer/inheritance/tpt)
  - [<span data-ttu-id="38046-138">Stored Procedure Mapping for Updates (Zuordnung einer gespeicherten Prozedur für Updates)</span><span class="sxs-lookup"><span data-stu-id="38046-138">Stored Procedure Mapping for Updates</span></span>](xref:ef6/modeling/designer/stored-procedures/cud)
  - [<span data-ttu-id="38046-139">Stored Procedure Mapping for Query (Zuordnung einer gespeicherten Prozedur für die Abfrage)</span><span class="sxs-lookup"><span data-stu-id="38046-139">Stored Procedure Mapping for Query</span></span>](xref:ef6/modeling/designer/stored-procedures/query)
  - [<span data-ttu-id="38046-140">Entitätsaufteilung</span><span class="sxs-lookup"><span data-stu-id="38046-140">Entity Splitting</span></span>](xref:ef6/modeling/designer/entity-splitting)
  - [<span data-ttu-id="38046-141">Tabellenaufteilung</span><span class="sxs-lookup"><span data-stu-id="38046-141">Table Splitting</span></span>](xref:ef6/modeling/designer/table-splitting)
  - <span data-ttu-id="38046-142">[Definieren der Abfrage](xref:ef6/modeling/designer/advanced/defining-query) (erweitert)</span><span class="sxs-lookup"><span data-stu-id="38046-142">[Defining Query](xref:ef6/modeling/designer/advanced/defining-query) (Advanced)</span></span>
  - <span data-ttu-id="38046-143">[Tabellenwertfunktionen](xref:ef6/modeling/designer/advanced/tvfs) (erweitert)</span><span class="sxs-lookup"><span data-stu-id="38046-143">[Table-Valued Functions](xref:ef6/modeling/designer/advanced/tvfs) (Advanced)</span></span>

## <a name="other-resources"></a><span data-ttu-id="38046-144">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="38046-144">Other resources</span></span>
  - [<span data-ttu-id="38046-145">Asynchrone Abfragen und Speichern</span><span class="sxs-lookup"><span data-stu-id="38046-145">Async Query and Save</span></span>](xref:ef6/fundamentals/async)
  - [<span data-ttu-id="38046-146">Datenbindung mit Windows Forms</span><span class="sxs-lookup"><span data-stu-id="38046-146">Databinding with WinForms</span></span>](xref:ef6/fundamentals/databinding/winforms)
  - [<span data-ttu-id="38046-147">Datenbindung mit WPF</span><span class="sxs-lookup"><span data-stu-id="38046-147">Databinding with WPF</span></span>](xref:ef6/fundamentals/databinding/wpf)
  - <span data-ttu-id="38046-148">[Disconnected scenarios with Self-Tracking Entities (Szenarios mit getrennten Verbindungen mit Entitäten mit Selbstnachverfolgung)](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (wird nicht länger empfohlen)</span><span class="sxs-lookup"><span data-stu-id="38046-148">[Disconnected scenarios with Self-Tracking Entities](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (This is no longer recommended)</span></span>
