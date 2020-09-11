---
title: 'Portieren von EF6 auf EF Core: Portieren eines EDMX-basierten Modells'
description: Spezifische Informationen zum Portieren einer EDMX-basierten Entity Framework 6-Modellanwendung zu Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 63003709-f1ec-4bdc-8083-65a60c4826d2
uid: efcore-and-ef6/porting/port-edmx
ms.openlocfilehash: 3767b1dc083ec886115cea9b0750101fb49ad84c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619604"
---
# <a name="porting-an-ef6-edmx-based-model-to-ef-core"></a><span data-ttu-id="c21ef-103">Portieren eines EDMX-basierten EF6-Modells auf EF Core</span><span class="sxs-lookup"><span data-stu-id="c21ef-103">Porting an EF6 EDMX-Based Model to EF Core</span></span>

<span data-ttu-id="c21ef-104">EF Core unterstützt das EDMX-Dateiformat nicht für Modelle.</span><span class="sxs-lookup"><span data-stu-id="c21ef-104">EF Core does not support the EDMX file format for models.</span></span> <span data-ttu-id="c21ef-105">Der beste Ansatz zum Portieren dieser Modelle besteht darin, ein neues codebasiertes Modell aus der Datenbank für Ihre Anwendung zu generieren.</span><span class="sxs-lookup"><span data-stu-id="c21ef-105">The best option to port these models, is to generate a new code-based model from the database for your application.</span></span>

## <a name="install-ef-core-nuget-packages"></a><span data-ttu-id="c21ef-106">Installieren von EF Core-NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="c21ef-106">Install EF Core NuGet packages</span></span>

<span data-ttu-id="c21ef-107">Installieren Sie das NuGet-Paket `Microsoft.EntityFrameworkCore.Tools`.</span><span class="sxs-lookup"><span data-stu-id="c21ef-107">Install the `Microsoft.EntityFrameworkCore.Tools` NuGet package.</span></span>

## <a name="regenerate-the-model"></a><span data-ttu-id="c21ef-108">Erneutes Generieren des Modells</span><span class="sxs-lookup"><span data-stu-id="c21ef-108">Regenerate the model</span></span>

<span data-ttu-id="c21ef-109">Sie können jetzt die Reverse-Engineering-Funktion verwenden, um ein Modell auf Grundlage der vorhandenen Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c21ef-109">You can now use the reverse engineer functionality to create a model based on your existing database.</span></span>

<span data-ttu-id="c21ef-110">Führen Sie in der Paket-Manager-Konsole (Extras > NuGet-Paket-Manager > Paket-Manager-Konsole) den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="c21ef-110">Run the following command in Package Manager Console (Tools –> NuGet Package Manager –> Package Manager Console).</span></span> <span data-ttu-id="c21ef-111">Im Artikel zur [Paket-Manager-Konsole in Visual Studio](xref:core/miscellaneous/cli/powershell) finden Sie Befehlsoptionen für das Erstellen eines Gerüsts für eine Teilmenge von Tabellen oder anderen Komponenten.</span><span class="sxs-lookup"><span data-stu-id="c21ef-111">See [Package Manager Console (Visual Studio)](xref:core/miscellaneous/cli/powershell) for command options to scaffold a subset of tables etc.</span></span>

``` powershell
Scaffold-DbContext "<connection string>" <database provider name>
```

<span data-ttu-id="c21ef-112">Hier sehen Sie z. B. den Befehl, um das Gerüst eines Modell aus der Blogging-Datenbank auf Ihrer SQL Server-LocalDB-Instanz zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c21ef-112">For example, here is the command to scaffold a model from the Blogging database on your SQL Server LocalDB instance.</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="remove-ef6-model"></a><span data-ttu-id="c21ef-113">Entfernen des EF6-Modells</span><span class="sxs-lookup"><span data-stu-id="c21ef-113">Remove EF6 model</span></span>

<span data-ttu-id="c21ef-114">An dieser Stelle entfernen Sie das EF6-Modell aus der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="c21ef-114">You would now remove the EF6 model from your application.</span></span>

<span data-ttu-id="c21ef-115">Sie müssen das EF6-NuGet-Paket (EntityFramework) nicht deinstallieren, da EF Core und EF6 parallel in derselben Anwendung verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="c21ef-115">It is fine to leave the EF6 NuGet package (EntityFramework) installed, as EF Core and EF6 can be used side-by-side in the same application.</span></span> <span data-ttu-id="c21ef-116">Wenn Sie jedoch nicht beabsichtigen, EF6 in Ihrer Anwendung zu verwenden, kann das Deinstallieren des Pakets dazu beitragen, dass nur zu den Codebestandteilen Kompilierfehler ausgegeben werden, die wirklich Ihre Aufmerksamkeit erfordern.</span><span class="sxs-lookup"><span data-stu-id="c21ef-116">However, if you aren't intending to use EF6 in any areas of your application, then uninstalling the package will help give compile errors on pieces of code that need attention.</span></span>

## <a name="update-your-code"></a><span data-ttu-id="c21ef-117">Aktualisieren Ihres Codes</span><span class="sxs-lookup"><span data-stu-id="c21ef-117">Update your code</span></span>

<span data-ttu-id="c21ef-118">An dieser Stelle ist es wichtig, Kompilierfehler zu beheben und ein Code Review durchzuführen, um festzustellen, ob die Verhaltensänderungen zwischen EF6 und EF Core eine Auswirkung zeigen.</span><span class="sxs-lookup"><span data-stu-id="c21ef-118">At this point, it's a matter of addressing compilation errors and reviewing code to see if the behavior changes between EF6 and EF Core will impact you.</span></span>

## <a name="test-the-port"></a><span data-ttu-id="c21ef-119">Testen des Ports</span><span class="sxs-lookup"><span data-stu-id="c21ef-119">Test the port</span></span>

<span data-ttu-id="c21ef-120">Nur weil Ihre Anwendung erfolgreich kompiliert wird, bedeutet das nicht, dass sie auch erfolgreich auf EF Core portiert wurde.</span><span class="sxs-lookup"><span data-stu-id="c21ef-120">Just because your application compiles, does not mean it is successfully ported to EF Core.</span></span> <span data-ttu-id="c21ef-121">Sie müssen alle Bereiche Ihrer Anwendung testen, um sicherzustellen, dass keine der Verhaltensänderungen Ihre Anwendung beeinträchtigt hat.</span><span class="sxs-lookup"><span data-stu-id="c21ef-121">You will need to test all areas of your application to ensure that none of the behavior changes have adversely impacted your application.</span></span>
