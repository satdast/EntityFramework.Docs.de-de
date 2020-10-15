---
title: Überblick über Entity Framework 6 (EF6)
description: Überblick über Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/index
ms.openlocfilehash: 09b7cb9e0141e9739b15d6c9789286c2e47018cc
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065393"
---
# <a name="entity-framework-6"></a><span data-ttu-id="39730-103">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="39730-103">Entity Framework 6</span></span>
<span data-ttu-id="39730-104">Entity Framework 6 (EF6) ist ein bewährtes Framework für objektrelationale Abbildung (Object-Relational Mapping, ORM) für .NET, dessen Features im Laufe der Zeit immer weiter optimiert und stabilisiert wurden.</span><span class="sxs-lookup"><span data-stu-id="39730-104">Entity Framework 6 (EF6) is a tried and tested object-relational mapper (O/RM) for .NET with many years of feature development and stabilization.</span></span>

<span data-ttu-id="39730-105">Als ORM-Framework senkt EF6 die Inkongruenz zwischen dem relationalen und dem objektorientierten Bereich. Dadurch können Entwickler Anwendungen schreiben, die mit Daten interagieren, die in relationalen Datenbanken mit stark typisierten .NET-Objekten gespeichert wurden, die die Anwendungsdomäne darstellen. Außerdem ist es durch EF6 nicht mehr erforderlich, dass ein großer Teil der Daten auf Grundlagencode zugreifen muss, den sie normalerweise schreiben müssen.</span><span class="sxs-lookup"><span data-stu-id="39730-105">As an O/RM, EF6 reduces the impedance mismatch between the relational and object-oriented worlds, enabling developers to write applications that interact with data stored in relational databases using strongly-typed .NET objects that represent the application's domain, and eliminating the need for a large portion of the data access "plumbing" code that they usually need to write.</span></span>

<span data-ttu-id="39730-106">EF6 implementiert viele beliebte ORM-Features:</span><span class="sxs-lookup"><span data-stu-id="39730-106">EF6 implements many popular O/RM features:</span></span>
- <span data-ttu-id="39730-107">Zuordnung von [POCO](xref:ef6/resources/glossary#poco)-Entitätsklassen, die nicht von EF-Typen abhängen</span><span class="sxs-lookup"><span data-stu-id="39730-107">Mapping of [POCO](xref:ef6/resources/glossary#poco) entity classes which do not depend on any EF types</span></span>
- <span data-ttu-id="39730-108">Automatische Änderungsnachverfolgung</span><span class="sxs-lookup"><span data-stu-id="39730-108">Automatic change tracking</span></span>
- <span data-ttu-id="39730-109">Identitätsauflösung und Arbeitseinheit</span><span class="sxs-lookup"><span data-stu-id="39730-109">Identity resolution and Unit of Work</span></span>
- <span data-ttu-id="39730-110">Eager und Lazy Loading sowie explizites Laden</span><span class="sxs-lookup"><span data-stu-id="39730-110">Eager, lazy and explicit loading</span></span>
- <span data-ttu-id="39730-111">Übersetzung stark typisierter Abfragen mit [LINQ (Language Integrated Query)](https://aka.ms/AA6hsvu)</span><span class="sxs-lookup"><span data-stu-id="39730-111">Translation of strongly-typed queries using [LINQ (Language INtegrated Query)](https://aka.ms/AA6hsvu)</span></span>
- <span data-ttu-id="39730-112">Umfangreiche Mappingfunktionen, u.a. Unterstützung für:</span><span class="sxs-lookup"><span data-stu-id="39730-112">Rich mapping capabilities, including support for:</span></span>
  - <span data-ttu-id="39730-113">1:1-, 1:n- und m:n-Beziehungen</span><span class="sxs-lookup"><span data-stu-id="39730-113">One-to-one, one-to-many and many-to-many relationships</span></span>
  - <span data-ttu-id="39730-114">Vererbung (Tabelle pro Hierarchie, Tabelle pro Typ und Tabelle pro konkreter Klasse)</span><span class="sxs-lookup"><span data-stu-id="39730-114">Inheritance (table per hierarchy, table per type and table per concrete class)</span></span>
  - <span data-ttu-id="39730-115">Komplexe Typen</span><span class="sxs-lookup"><span data-stu-id="39730-115">Complex types</span></span>
  - <span data-ttu-id="39730-116">Gespeicherte Prozeduren</span><span class="sxs-lookup"><span data-stu-id="39730-116">Stored procedures</span></span>
- <span data-ttu-id="39730-117">Ein visueller Designer zum Erstellen von Entitätsmodellen.</span><span class="sxs-lookup"><span data-stu-id="39730-117">A visual designer to create entity models.</span></span>
- <span data-ttu-id="39730-118">Ein Code First-Prozess für das Erstellen von Entitätsmodellen durch das Schreiben von Code.</span><span class="sxs-lookup"><span data-stu-id="39730-118">A "Code First" experience to create entity models by writing code.</span></span>
- <span data-ttu-id="39730-119">Modelle können entweder anhand vorhandener Datenbanken erstellt und dann manuell bearbeitet werden, oder Sie können sie komplett neu erstellen und dann zum Erstellen neuer Datenbanken verwenden.</span><span class="sxs-lookup"><span data-stu-id="39730-119">Models can either be generated from existing databases and then hand-edited, or they can be created from scratch and then used to generate new databases.</span></span>
- <span data-ttu-id="39730-120">Integration in .NET Framework-Anwendungsmodelle, ASP.NET eingeschlossen, und anhand von Datenbindung mit WPF und WinForms.</span><span class="sxs-lookup"><span data-stu-id="39730-120">Integration with .NET Framework application models, including ASP.NET, and through databinding, with WPF and WinForms.</span></span>
- <span data-ttu-id="39730-121">Datenbankkonnektivität basierend auf ADO.NET und zahlreichen anderen [Anbietern](xref:ef6/fundamentals/providers/index), die eine Verbindung mit SQL Server, Oracle, MySQL, SQLite, PostgreSQL, DB2 usw. herstellen.</span><span class="sxs-lookup"><span data-stu-id="39730-121">Database connectivity based on ADO.NET and numerous [providers](xref:ef6/fundamentals/providers/index) available to connect to SQL Server, Oracle, MySQL, SQLite, PostgreSQL, DB2, etc.</span></span>

## <a name="should-i-use-ef6-or-ef-core"></a><span data-ttu-id="39730-122">Sollte ich EF6 oder EF Core verwenden?</span><span class="sxs-lookup"><span data-stu-id="39730-122">Should I use EF6 or EF Core?</span></span>

<span data-ttu-id="39730-123">EF Core ist eine modernere, einfache und erweiterbare Version von Entity Framework mit ähnlichen Funktionen wie EF6 und einigen Vorteilen.</span><span class="sxs-lookup"><span data-stu-id="39730-123">EF Core is a more modern, lightweight and extensible version of Entity Framework that has very similar capabilities and benefits to EF6.</span></span>
<span data-ttu-id="39730-124">EF Core wurde vollständig neu geschrieben und enthält viele neue Features, die es in EF6 nicht gibt. Andererseits fehlen die komplexesten Mappingfeatures, die in EF6 vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="39730-124">EF Core is a complete rewrite and contains many new features not available in EF6, although it also still lacks some of the most advanced mapping capabilities of EF6.</span></span>
<span data-ttu-id="39730-125">Erwägen Sie die Verwendung von EF Core in neuen Anwendungen, wenn die enthaltenen Features Ihren Anforderungen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="39730-125">Consider using EF Core in new applications if the feature set matches your requirements.</span></span>
<span data-ttu-id="39730-126">Unter [Vergleichen von EF Core und EF 6](xref:efcore-and-ef6/index) wird darauf ausführlicher eingegangen.</span><span class="sxs-lookup"><span data-stu-id="39730-126">[Compare EF Core & EF6](xref:efcore-and-ef6/index) examines this choice in greater detail.</span></span>

## <a name="get-started"></a>[<span data-ttu-id="39730-127">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="39730-127">Get Started</span></span>](xref:ef6/get-started)

<span data-ttu-id="39730-128">Fügen Sie Ihrem Projekt das EntityFramework-NuGet-Paket hinzu, oder installieren Sie [Entity Framework Tools für Visual Studio](https://aka.ms/AA6i8c5).</span><span class="sxs-lookup"><span data-stu-id="39730-128">Add the EntityFramework NuGet package to your project or install the [Entity Framework Tools for Visual Studio](https://aka.ms/AA6i8c5).</span></span> <span data-ttu-id="39730-129">Sehen Sie sich anschließend Videos an, lesen Sie Tutorials und weiterführende Dokumentationsartikel, sodass Sie die Funktionen von Entity Framework 6 optimal nutzen können.</span><span class="sxs-lookup"><span data-stu-id="39730-129">Then watch videos, read tutorials, and advanced documentation to help you make the most of EF6.</span></span>

## <a name="past-entity-framework-versions"></a><span data-ttu-id="39730-130">Frühere Versionen von Entity Framework</span><span class="sxs-lookup"><span data-stu-id="39730-130">Past Entity Framework Versions</span></span>

<span data-ttu-id="39730-131">Dies ist die Dokumentation für die aktuelle Version von Entity Framework 6. Viele der Punkte gelten jedoch auch für ältere Releases.</span><span class="sxs-lookup"><span data-stu-id="39730-131">This is the documentation for the latest version of Entity Framework 6, although much of it also applies to past releases.</span></span>
<span data-ttu-id="39730-132">Unter [Neues](xref:ef6/what-is-new/index) und [Frühere Releases](xref:ef6/what-is-new/past-releases) finden Sie eine vollständige Liste der EF-Releases mit dazugehörigen neuen Features.</span><span class="sxs-lookup"><span data-stu-id="39730-132">Check out [What's New](xref:ef6/what-is-new/index) and [Past Releases](xref:ef6/what-is-new/past-releases) for a complete list of EF releases and the features they introduced.</span></span>
