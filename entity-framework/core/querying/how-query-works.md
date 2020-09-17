---
title: Funktionsweise von Abfragen – EF Core
description: Allgemeine Informationen darüber, wie Abfragen von Entity Framework Core intern kompiliert und ausgeführt werden
author: ajcvickers
ms.date: 03/17/2020
uid: core/querying/how-query-works
ms.openlocfilehash: f3a6794b9bfdf70ae40bc8e97ee41861931b9b46
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071185"
---
# <a name="how-queries-work"></a><span data-ttu-id="94079-103">Funktionsweise von Abfragen</span><span class="sxs-lookup"><span data-stu-id="94079-103">How Queries Work</span></span>

<span data-ttu-id="94079-104">Entity Framework Core verwendet Language Integrated Query (LINQ), um Daten von der Datenbank abzufragen.</span><span class="sxs-lookup"><span data-stu-id="94079-104">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="94079-105">LINQ ermöglicht Ihnen, mit C# (oder Ihrer bevorzugten .NET-Sprache) stark typisierte Abfragen basierend auf Ihrem abgeleiteten Kontext und Entitätsklassen zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="94079-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span>

## <a name="the-life-of-a-query"></a><span data-ttu-id="94079-106">Der Abfragezyklus</span><span class="sxs-lookup"><span data-stu-id="94079-106">The life of a query</span></span>

<span data-ttu-id="94079-107">Nachstehend finden Sie eine grobe Übersicht über die verschiedenen Phasen des Abfragevorgangs.</span><span class="sxs-lookup"><span data-stu-id="94079-107">The following description is a high-level overview of the process each query goes through.</span></span>

1. <span data-ttu-id="94079-108">Die LINQ-Abfrage wird von Entity Framework Core verarbeitet. Dabei wird eine Darstellung erstellt, die wiederum vom Datenbankanbieter verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="94079-108">The LINQ query is processed by Entity Framework Core to build a representation that is ready to be processed by the database provider</span></span>
   1. <span data-ttu-id="94079-109">Das Ergebnis wird zwischengespeichert, damit dieser Vorgang nicht jedes Mal ausgeführt werden muss, wenn die Abfrage ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="94079-109">The result is cached so that this processing does not need to be done every time the query is executed</span></span>
2. <span data-ttu-id="94079-110">Das Ergebnis wird an den Datenbankanbieter übergeben.</span><span class="sxs-lookup"><span data-stu-id="94079-110">The result is passed to the database provider</span></span>
   1. <span data-ttu-id="94079-111">Der Datenbankanbieter ermittelt, welche Teile der Abfrage in der Datenbank ausgewertet werden können.</span><span class="sxs-lookup"><span data-stu-id="94079-111">The database provider identifies which parts of the query can be evaluated in the database</span></span>
   2. <span data-ttu-id="94079-112">Diese Teile der Abfrage werden in die datenbankspezifische Abfragesprache übersetzt, z. B. SQL für relationale Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="94079-112">These parts of the query are translated to database-specific query language (for example, SQL for a relational database)</span></span>
   3. <span data-ttu-id="94079-113">Eine Abfrage wird an die Datenbank gesendet, und das Resultset wird zurückgegeben (Ergebnisse sind Datenbankwerte, keine Entitätsinstanzen).</span><span class="sxs-lookup"><span data-stu-id="94079-113">A query is sent to the database and the result set returned (results are values from the database, not entity instances)</span></span>
3. <span data-ttu-id="94079-114">Für jedes Element im Resultset wird Folgendes ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="94079-114">For each item in the result set</span></span>
   1. <span data-ttu-id="94079-115">Wenn es sich bei der Abfrage um eine Überwachungsabfrage handelt, überprüft EF, ob die Daten eine Entität darstellen, die bereits in der Änderungsnachverfolgung für die Kontextinstanz vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="94079-115">If the query is a tracking query, EF checks if the data represents an entity already in the change tracker for the context instance</span></span>
      * <span data-ttu-id="94079-116">Wenn dies der Fall ist, wird die vorhandene Entität zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="94079-116">If so, the existing entity is returned</span></span>
      * <span data-ttu-id="94079-117">Wenn dies nicht der Fall ist, wird eine neue Entität erstellt, die Änderungsnachverfolgung wird eingerichtet, und die neue Entität wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="94079-117">If not, a new entity is created, change tracking is set up, and the new entity is returned</span></span>
   2. <span data-ttu-id="94079-118">Wenn es sich bei der Abfrage um eine nicht nachverfolgungsbezogene Abfrage handelt, wird immer eine neue Entität erstellt und zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="94079-118">If the query is a no-tracking query, then a new entity is always created and returned</span></span>

## <a name="when-queries-are-executed"></a><span data-ttu-id="94079-119">Ausführen von Abfragen</span><span class="sxs-lookup"><span data-stu-id="94079-119">When queries are executed</span></span>

<span data-ttu-id="94079-120">Wenn Sie LINQ-Operatoren aufrufen, erstellen Sie einfach eine speicherinterne Darstellung der Abfrage.</span><span class="sxs-lookup"><span data-stu-id="94079-120">When you call LINQ operators, you're simply building up an in-memory representation of the query.</span></span> <span data-ttu-id="94079-121">Die Abfrage wird nur an die Datenbank gesendet, wenn die Ergebnisse verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="94079-121">The query is only sent to the database when the results are consumed.</span></span>

<span data-ttu-id="94079-122">Dies sind die üblichsten Vorgänge, aufgrund derer die Abfrage wird an die Datenbank gesendet wird:</span><span class="sxs-lookup"><span data-stu-id="94079-122">The most common operations that result in the query being sent to the database are:</span></span>

* <span data-ttu-id="94079-123">Durchlaufen die Ergebnisse in einer `for`-Schleife</span><span class="sxs-lookup"><span data-stu-id="94079-123">Iterating the results in a `for` loop</span></span>
* <span data-ttu-id="94079-124">Verwenden eines Operators wie `ToList`, `ToArray`, `Single`, `Count` oder die äquivalenten asynchronen Überladungen</span><span class="sxs-lookup"><span data-stu-id="94079-124">Using an operator such as `ToList`, `ToArray`, `Single`, `Count`, or the equivalent async overloads</span></span>

> [!WARNING]  
> <span data-ttu-id="94079-125">**Benutzereingaben immer überprüfen:** Zwar schützt EF Core vor SQL-Einschleusungsangriffen durch Verwendung von Parametern und das Escapen von Literalen in Abfragen, es überprüft jedoch keine Eingaben.</span><span class="sxs-lookup"><span data-stu-id="94079-125">**Always validate user input:** While EF Core protects against SQL injection attacks by using parameters and escaping literals in queries, it does not validate inputs.</span></span> <span data-ttu-id="94079-126">Geeignete Validierung gemäß den Anforderungen der Anwendung sollte erfolgen, bevor Werte aus nicht vertrauenswürdigen Quellen in LINQ-Abfragen verwendet, Entitätseigenschaften zugewiesen oder an andere EF Core-APIs übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="94079-126">Appropriate validation, per the application's requirements, should be performed before values from un-trusted sources are used in LINQ queries, assigned to entity properties, or passed to other EF Core APIs.</span></span> <span data-ttu-id="94079-127">Dies schließt alle Benutzereingaben ein, mit denen Abfragen dynamisch erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="94079-127">This includes any user input used to dynamically construct queries.</span></span> <span data-ttu-id="94079-128">Wenn Sie Benutzereingaben zum Erstellen von Ausdrücken zulassen, müssen Sie selbst bei LINQ sicherstellen, dass nur beabsichtigte Ausdrücke erstellt werden können.</span><span class="sxs-lookup"><span data-stu-id="94079-128">Even when using LINQ, if you are accepting user input to build expressions, you need to make sure that only intended expressions can be constructed.</span></span>
