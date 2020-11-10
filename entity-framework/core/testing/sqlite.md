---
title: Testen mit SQLite-EF Core
description: Verwenden von SQLite zum Testen einer Entity Framework Core Anwendung
author: ajcvickers
ms.date: 04/24/2020
uid: core/testing/sqlite
ms.openlocfilehash: ebfcd36bf236cb83cab8683a8c31d4752d437998
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431270"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a><span data-ttu-id="97a57-103">Verwenden von SQLite zum Testen einer EF Core Anwendung</span><span class="sxs-lookup"><span data-stu-id="97a57-103">Using SQLite to test an EF Core application</span></span>

> [!WARNING]
> <span data-ttu-id="97a57-104">Die Verwendung von SQLite kann eine effektive Möglichkeit zum Testen einer EF Core Anwendung sein.</span><span class="sxs-lookup"><span data-stu-id="97a57-104">Using SQLite can be an effective way to test an EF Core application.</span></span>
> <span data-ttu-id="97a57-105">Es können jedoch Probleme auftreten, wenn SQLite anders als andere Datenbanksysteme verhält.</span><span class="sxs-lookup"><span data-stu-id="97a57-105">However, problems can arise where SQLite behaves differently from other database systems.</span></span>
> <span data-ttu-id="97a57-106">Eine Erläuterung der Probleme und Kompromisse finden Sie unter [Testen von Code, der EF Core verwendet](xref:core/testing/index) .</span><span class="sxs-lookup"><span data-stu-id="97a57-106">See [Testing code that uses EF Core](xref:core/testing/index) for a discussion of the issues and trade-offs.</span></span>  

<span data-ttu-id="97a57-107">In diesem Dokument werden die im Beispiel eingeführten Konzepte verwendet, die veranschaulichen, [wie Sie Anwendungen testen, die EF Core verwenden](xref:core/testing/testing-sample).</span><span class="sxs-lookup"><span data-stu-id="97a57-107">This document builds uses on the concepts introduced in [Sample showing how to test applications that use EF Core](xref:core/testing/testing-sample).</span></span>
<span data-ttu-id="97a57-108">Die hier gezeigten Codebeispiele stammen aus diesem Beispiel.</span><span class="sxs-lookup"><span data-stu-id="97a57-108">The code examples shown here come from this sample.</span></span>

## <a name="using-sqlite-in-memory-databases"></a><span data-ttu-id="97a57-109">Verwenden von sqlite-in-Memory-Datenbanken</span><span class="sxs-lookup"><span data-stu-id="97a57-109">Using SQLite in-memory databases</span></span>

<span data-ttu-id="97a57-110">Normalerweise erstellt SQLite Datenbanken als einfache Dateien und greift mit Ihrer Anwendung Prozess bedingt auf die Datei zu.</span><span class="sxs-lookup"><span data-stu-id="97a57-110">Normally, SQLite creates databases as simple files and accesses the file in-process with your application.</span></span>
<span data-ttu-id="97a57-111">Dies ist sehr schnell, insbesondere bei Verwendung einer schnellen [SSD](https://en.wikipedia.org/wiki/Solid-state_drive).</span><span class="sxs-lookup"><span data-stu-id="97a57-111">This is very fast, especially when using a fast [SSD](https://en.wikipedia.org/wiki/Solid-state_drive).</span></span>

<span data-ttu-id="97a57-112">SQLite kann auch Datenbanken verwenden, die ausschließlich im Arbeitsspeicher erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="97a57-112">SQLite can also use databases created purely in-memory.</span></span>
<span data-ttu-id="97a57-113">Dies ist mit EF Core leicht zu verwenden, solange Sie die in-Memory Database Lebensdauer verstehen:</span><span class="sxs-lookup"><span data-stu-id="97a57-113">This is easy to use with EF Core as long as you understand the in-memory database lifetime:</span></span>

* <span data-ttu-id="97a57-114">Die Datenbank wird erstellt, wenn die Verbindung mit ihr geöffnet wird.</span><span class="sxs-lookup"><span data-stu-id="97a57-114">The database is created when the connection to it is opened</span></span>
* <span data-ttu-id="97a57-115">Die Datenbank wird gelöscht, wenn die Verbindung mit der Datenbank geschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="97a57-115">The database is deleted when the connection to it is closed</span></span>

<span data-ttu-id="97a57-116">EF Core wird eine bereits geöffnete Verbindung verwendet, wenn eine vorhanden ist, und versucht nie, Sie zu schließen.</span><span class="sxs-lookup"><span data-stu-id="97a57-116">EF Core will use an already open connection when given one, and will never attempt to close it.</span></span>
<span data-ttu-id="97a57-117">Der Schlüssel für die Verwendung von EF Core mit einer in-Memory SQLite-Datenbank besteht also darin, die Verbindung zu öffnen, bevor Sie Sie an EF übergibt.</span><span class="sxs-lookup"><span data-stu-id="97a57-117">So the key to using EF Core with an in-memory SQLite database is to open the connection before passing it to EF.</span></span>  

<span data-ttu-id="97a57-118">Das [Beispiel](xref:core/testing/testing-sample) erreicht dies mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="97a57-118">The [sample](xref:core/testing/testing-sample) achieves this with the following code:</span></span>

[!code-csharp[SqliteInMemory](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

<span data-ttu-id="97a57-119">Beachten Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="97a57-119">Notice:</span></span>

* <span data-ttu-id="97a57-120">Die `CreateInMemoryDatabase` -Methode erstellt eine sqlite-in-Memory Database und öffnet die Verbindung mit der-Methode.</span><span class="sxs-lookup"><span data-stu-id="97a57-120">The `CreateInMemoryDatabase` method creates a SQLite in-memory database and opens the connection to it.</span></span>
* <span data-ttu-id="97a57-121">Der erstellte `DbConnection` wird aus der extrahiert `ContextOptions` und gespeichert.</span><span class="sxs-lookup"><span data-stu-id="97a57-121">The created `DbConnection` is extracted from the `ContextOptions` and saved.</span></span>
* <span data-ttu-id="97a57-122">Die Verbindung wird verworfen, wenn der Test verworfen wird, damit Ressourcen nicht verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="97a57-122">The connection is disposed when the test is disposed so that resources are not leaked.</span></span>

> [!NOTE]
> <span data-ttu-id="97a57-123">[Problem #16103](https://github.com/dotnet/efcore/issues/16103) ist das Nachverfolgen von Möglichkeiten, diese Verbindungs Verwaltung zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="97a57-123">[Issue #16103](https://github.com/dotnet/efcore/issues/16103) is tracking ways to make this connection management easier.</span></span>
