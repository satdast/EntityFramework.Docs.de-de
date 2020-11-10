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
# <a name="using-sqlite-to-test-an-ef-core-application"></a>Verwenden von SQLite zum Testen einer EF Core Anwendung

> [!WARNING]
> Die Verwendung von SQLite kann eine effektive Möglichkeit zum Testen einer EF Core Anwendung sein.
> Es können jedoch Probleme auftreten, wenn SQLite anders als andere Datenbanksysteme verhält.
> Eine Erläuterung der Probleme und Kompromisse finden Sie unter [Testen von Code, der EF Core verwendet](xref:core/testing/index) .  

In diesem Dokument werden die im Beispiel eingeführten Konzepte verwendet, die veranschaulichen, [wie Sie Anwendungen testen, die EF Core verwenden](xref:core/testing/testing-sample).
Die hier gezeigten Codebeispiele stammen aus diesem Beispiel.

## <a name="using-sqlite-in-memory-databases"></a>Verwenden von sqlite-in-Memory-Datenbanken

Normalerweise erstellt SQLite Datenbanken als einfache Dateien und greift mit Ihrer Anwendung Prozess bedingt auf die Datei zu.
Dies ist sehr schnell, insbesondere bei Verwendung einer schnellen [SSD](https://en.wikipedia.org/wiki/Solid-state_drive).

SQLite kann auch Datenbanken verwenden, die ausschließlich im Arbeitsspeicher erstellt wurden.
Dies ist mit EF Core leicht zu verwenden, solange Sie die in-Memory Database Lebensdauer verstehen:

* Die Datenbank wird erstellt, wenn die Verbindung mit ihr geöffnet wird.
* Die Datenbank wird gelöscht, wenn die Verbindung mit der Datenbank geschlossen wird.

EF Core wird eine bereits geöffnete Verbindung verwendet, wenn eine vorhanden ist, und versucht nie, Sie zu schließen.
Der Schlüssel für die Verwendung von EF Core mit einer in-Memory SQLite-Datenbank besteht also darin, die Verbindung zu öffnen, bevor Sie Sie an EF übergibt.  

Das [Beispiel](xref:core/testing/testing-sample) erreicht dies mit folgendem Code:

[!code-csharp[SqliteInMemory](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

Beachten Sie Folgendes:

* Die `CreateInMemoryDatabase` -Methode erstellt eine sqlite-in-Memory Database und öffnet die Verbindung mit der-Methode.
* Der erstellte `DbConnection` wird aus der extrahiert `ContextOptions` und gespeichert.
* Die Verbindung wird verworfen, wenn der Test verworfen wird, damit Ressourcen nicht verloren gehen.

> [!NOTE]
> [Problem #16103](https://github.com/dotnet/efcore/issues/16103) ist das Nachverfolgen von Möglichkeiten, diese Verbindungs Verwaltung zu vereinfachen.
