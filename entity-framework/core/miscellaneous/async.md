---
title: Asynchrone Programmierung-EF Core
description: Asynchrones Abfragen und Speichern von Daten mit Entity Framework Core
author: roji
ms.date: 9/2/2020
uid: core/miscellaneous/async
ms.openlocfilehash: 52b3ac20a50babbed6937ebe3365ac1947dcaef1
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128575"
---
# <a name="asynchronous-programming"></a>Asynchrone Programmierung

Asynchrone Vorgänge verhindern, dass ein Thread blockiert wird, während die Abfrage in der Datenbank ausgeführt wird. Asynchrone Vorgänge sind wichtig, um eine reaktionsfähige Benutzeroberfläche in Rich Client-Anwendungen zu erhalten. Außerdem kann der Durchsatz in Webanwendungen erhöht werden, wo Sie den Thread für andere Anforderungen in Webanwendungen freigeben.

Nach dem .NET-Standard stellt EF Core asynchrone Entsprechungen für alle synchronen Methoden bereit, die e/a-Vorgänge ausführen. Diese haben die gleichen Auswirkungen wie die Synchronisierungs Methoden und können mit den c# `async` -und- `await` Schlüsselwörtern verwendet werden. Anstatt z. b. dbcontext. SaveChanges zu verwenden, wodurch ein Thread blockiert wird, während die Datenbank-e/a-Vorgänge ausgeführt werden, kann dbcontext. savechangesasync verwendet werden:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#SaveChangesAsync)]

Weitere Informationen finden Sie in [der allgemeinen Dokumentation zur asynchronen Programmierung in c#](/dotnet/csharp/async).

> [!WARNING]
> EF Core unterstützt nicht die Ausführung mehrerer paralleler Vorgänge, die auf derselben Kontextinstanz ausgeführt werden. Sie sollten immer auf den Abschluss eines Vorgangs warten, bevor Sie den nächsten starten. In der Regel erfolgt dies für alle asynchronen Vorgänge durch das Schlüsselwort `await`.

> [!WARNING]
> Bei der asynchronen Implementierung von [Microsoft. Data. SqlClient](https://github.com/dotnet/SqlClient) sind leider einige bekannte Probleme aufgetreten (z. b. [#593](https://github.com/dotnet/SqlClient/issues/593), [#601](https://github.com/dotnet/SqlClient/issues/601)und andere).

> [!NOTE]
> EF Core übergibt Abbruch Token an den verwendeten zugrunde liegenden Datenbankanbieter (z. b. Microsoft. Data. SqlClient). Diese Token werden möglicherweise nicht berücksichtigt. Informationen hierzu finden Sie in der Dokumentation des Datenbankanbieters.

## <a name="async-linq-operators"></a>Asynchrone LINQ-Operatoren

Um die asynchrone Ausführung von LINQ-Abfragen zu unterstützen, stellt EF Core einen Satz asynchroner Erweiterungs Methoden bereit, die die Abfrage ausführen und Ergebnisse zurückgeben. Diese Entsprechungen zu den standardmäßigen synchronen LINQ-Operatoren umfassen "delistasync", "singleasync", "asasyncenenerable" usw.:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#ToListAsync)]

Beachten Sie, dass es keine asynchronen Versionen einiger LINQ-Operatoren gibt, wie z. b. WHERE oder OrderBy, da diese nur die LINQ-Ausdrucks Baumstruktur erstellen und nicht bewirken, dass die Abfrage in der Datenbank ausgeführt wird. Nur Operatoren, die die Abfrage Ausführung bewirken, haben asynchrone Entsprechungen

> [!IMPORTANT]
> Die asynchronen Erweiterungsmethoden von EF Core werden im Namespace `Microsoft.EntityFrameworkCore` definiert. Dieser Namespace muss importiert werden, damit die Methoden verfügbar sind.

## <a name="client-side-async-linq-operators"></a>Client seitige asynchrone LINQ-Operatoren

Die oben beschriebenen asynchronen LINQ-Operatoren können nur für EF-Abfragen verwendet werden. Sie können nicht mit der Client seitigen LINQ to Objects Abfrage verwendet werden. Verwenden Sie das [System. Interactive. Async-Paket](https://www.nuget.org/packages/System.Interactive.Async), um Client seitige asynchrone LINQ-Vorgänge außerhalb von EF auszuführen. Dieses Paket ist besonders nützlich für die Ausführung von Vorgängen auf dem Client, die nicht zur Auswertung auf dem Server übersetzt werden können.

Leider verursacht der Verweis auf System. Interactive. Async mehrdeutige Aufruf Kompilierungsfehler bei LINQ-Operatoren, die auf die dbsets von EF angewendet werden. Dadurch ist es schwierig, EF und System. Interactive. Async im gleichen Projekt zu verwenden. Um dieses Problem zu umgehen, fügen Sie Ihrem dbset asquerable hinzu:

[!code-csharp[Main](../../../samples/core/Miscellaneous/AsyncWithSystemInteractive/Program.cs#SystemInteractiveAsync)]
