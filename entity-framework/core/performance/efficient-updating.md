---
title: Effizientes aktualisieren-EF Core
description: Leistungs Leit Faden zur effizienten Aktualisierung mithilfe Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/performance/efficient-updating
ms.openlocfilehash: 92766d2339fb04ed5ebc3123429171cc9be424b1
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657730"
---
# <a name="efficient-updating"></a><span data-ttu-id="bdfdc-103">Effizientes aktualisieren</span><span class="sxs-lookup"><span data-stu-id="bdfdc-103">Efficient Updating</span></span>

## <a name="batching"></a><span data-ttu-id="bdfdc-104">Batchverarbeitung</span><span class="sxs-lookup"><span data-stu-id="bdfdc-104">Batching</span></span>

<span data-ttu-id="bdfdc-105">EF Core hilft, Roundtrips zu minimieren, indem alle Updates automatisch in einem einzigen Roundtrip zusammengefasst werden.</span><span class="sxs-lookup"><span data-stu-id="bdfdc-105">EF Core helps minimize roundtrips by automatically batching together all updates in a single roundtrip.</span></span> <span data-ttu-id="bdfdc-106">Beachten Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="bdfdc-106">Consider the following:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#SaveChangesBatching)]

<span data-ttu-id="bdfdc-107">Im obigen Beispiel wird ein Blog aus der Datenbank geladen, dessen Name geändert und zwei neue Blogs hinzugefügt. um dies anzuwenden, werden zwei SQL INSERT-Anweisungen und eine Update-Anweisung an die Datenbank gesendet.</span><span class="sxs-lookup"><span data-stu-id="bdfdc-107">The above loads a blog from the database, changes its name, and then adds two new blogs; to apply this, two SQL INSERT statements and one UPDATE statement are sent to the database.</span></span> <span data-ttu-id="bdfdc-108">Anstatt sie einzeln zu senden, da blobinstanzen hinzugefügt werden, verfolgt EF Core diese Änderungen intern und führt Sie in einem einzelnen Roundtrip aus, wenn <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="bdfdc-108">Rather than sending them one by one, as Blog instances are added, EF Core tracks these changes internally, and executes them in a single roundtrip when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="bdfdc-109">Die Anzahl der Anweisungen, die EF in einem einzelnen Roundtrip verarbeitet, hängt vom verwendeten Datenbankanbieter ab.</span><span class="sxs-lookup"><span data-stu-id="bdfdc-109">The number of statements that EF batches in a single roundtrip depends on the database provider being used.</span></span> <span data-ttu-id="bdfdc-110">Beispielsweise hat die Leistungsanalyse gezeigt, dass die Batch Verarbeitung für SQL Server im Allgemeinen weniger effizient ist, wenn weniger als vier Anweisungen beteiligt sind.</span><span class="sxs-lookup"><span data-stu-id="bdfdc-110">For example, performance analysis has shown batching to be generally less efficient for SQL Server when less than 4 statements are involved.</span></span> <span data-ttu-id="bdfdc-111">Ebenso sind die Vorteile der Batch Verarbeitung nach ungefähr 40 Anweisungen für SQL Server, sodass EF Core standardmäßig nur bis zu 42 Anweisungen in einem einzelnen Batch ausführt und zusätzliche Anweisungen in separaten Roundtrips ausführt.</span><span class="sxs-lookup"><span data-stu-id="bdfdc-111">Similarly, the benefits of batching degrade after around 40 statements for SQL Server, so EF Core will by default only execute up to 42 statements in a single batch, and execute additional statements in separate roundtrips.</span></span>

<span data-ttu-id="bdfdc-112">Benutzer können diese Schwellenwerte auch optimieren, um eine potenziell höhere Leistung zu erzielen, aber Vergleichstests sorgfältig durchzuführen, bevor Sie Sie ändern:</span><span class="sxs-lookup"><span data-stu-id="bdfdc-112">Users can also tweak these thresholds to achieve potentially higher performance - but benchmark carefully before modifying these:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/BatchTweakingContext.cs#BatchTweaking)]

## <a name="bulk-updates"></a><span data-ttu-id="bdfdc-113">Massen Aktualisierungen</span><span class="sxs-lookup"><span data-stu-id="bdfdc-113">Bulk updates</span></span>

<span data-ttu-id="bdfdc-114">Angenommen, Sie möchten allen ihren Mitarbeitern eine Erhöhung zukommen lassen.</span><span class="sxs-lookup"><span data-stu-id="bdfdc-114">Let's assume you want to give all your employees a raise.</span></span> <span data-ttu-id="bdfdc-115">Eine typische Implementierung hierfür in EF Core würde wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="bdfdc-115">A typical implementation for this in EF Core would look like the following:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithoutBulk)]

<span data-ttu-id="bdfdc-116">Obwohl es sich um einen vollständig gültigen Code handelt, analysieren wir die Leistung aus Leistungsgründen:</span><span class="sxs-lookup"><span data-stu-id="bdfdc-116">While this is perfectly valid code, let's analyze what it does from a performance perspective:</span></span>

* <span data-ttu-id="bdfdc-117">Ein datenbankroundtrip wird ausgeführt, um alle relevanten Mitarbeiter zu laden. Beachten Sie, dass dadurch alle Zeilendaten aller Mitarbeiter an den Client weitergeführt werden, auch wenn nur das Gehalt benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="bdfdc-117">A database roundtrip is performed, to load all the relevant employees; note that this brings all the Employees' row data to the client, even if only the salary will be needed.</span></span>
* <span data-ttu-id="bdfdc-118">Die Änderungs Nachverfolgung von EF Core erstellt Momentaufnahmen, wenn die Entitäten geladen werden, und vergleicht dann diese Momentaufnahmen mit den Instanzen, um herauszufinden, welche Eigenschaften geändert wurden.</span><span class="sxs-lookup"><span data-stu-id="bdfdc-118">EF Core's change tracking creates snapshots when loading the entities, and then compares those snapshots to the instances to find out which properties changed.</span></span>
* <span data-ttu-id="bdfdc-119">Ein zweiter datenbankroundtrip wird ausgeführt, um alle Änderungen zu speichern.</span><span class="sxs-lookup"><span data-stu-id="bdfdc-119">A second database roundtrip is performed to save all the changes.</span></span> <span data-ttu-id="bdfdc-120">Obwohl alle Änderungen aufgrund der Batch Verarbeitung in einem einzelnen Roundtrip vorgenommen werden, sendet EF Core weiterhin eine Update-Anweisung pro Mitarbeiter, die von der Datenbank ausgeführt werden muss.</span><span class="sxs-lookup"><span data-stu-id="bdfdc-120">While all changes are done in a single roundtrip thanks to batching, EF Core still sends an UPDATE statement per employee, which must be executed by the database.</span></span>

<span data-ttu-id="bdfdc-121">Relationale Datenbanken unterstützen auch *Massen Aktualisierungen*, sodass die oben genannten mit der folgenden einzelnen SQL-Anweisung umgeschrieben werden könnten:</span><span class="sxs-lookup"><span data-stu-id="bdfdc-121">Relational databases also support *bulk updates*, so the above could be rewritten as the following single SQL statement:</span></span>

```sql
UPDATE [Employees] SET [Salary] = [Salary] + 1000;
```

<span data-ttu-id="bdfdc-122">Dadurch wird der gesamte Vorgang in einem einzigen Roundtrip durchführt, ohne dass Daten in die Datenbank geladen oder gesendet werden, und ohne dass die Änderungs nach Verfolgungs Mechanismen von EF genutzt werden, was einen zusätzlichen mehr Aufwand erfordert.</span><span class="sxs-lookup"><span data-stu-id="bdfdc-122">This performs the entire operation in a single roundtrip, without loading or sending any actual data to the database, and without making use of EF's change tracking machinery, which imposes an additional overhead.</span></span>

<span data-ttu-id="bdfdc-123">Leider bietet EF derzeit keine APIs für die Durchführung von Massen Aktualisierungen.</span><span class="sxs-lookup"><span data-stu-id="bdfdc-123">Unfortunately, EF doesn't currently provide APIs for performing bulk updates.</span></span> <span data-ttu-id="bdfdc-124">Bis Sie diese eingeführt haben, können Sie den Vorgang, bei dem die Leistung sensibel ist, mithilfe von RAW SQL ausführen:</span><span class="sxs-lookup"><span data-stu-id="bdfdc-124">Until these are introduced, you can use raw SQL to perform the operation where performance is sensitive:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithBulk)]
