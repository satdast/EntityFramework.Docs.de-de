---
title: 'Asynchroner Speichervorgang: EF Core'
author: rowanmiller
ms.date: 01/24/2017
ms.assetid: b64a606e-ecd9-4807-829a-b6ec05ade33f
uid: core/saving/async
ms.openlocfilehash: aa1fbfdc44895eace04354063435f98370aed2c4
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526913"
---
# <a name="asynchronous-saving"></a>Asynchroner Speichervorgang

Mit dem asynchronen Speichervorgang wird vermieden, dass ein Thread blockiert wird, während die Änderungen in die Datenbank geschrieben werden. Dies hilft dabei, das Abstürzen der Benutzeroberfläche einer Thick-Client-Anwendung zu vermeiden. Asynchrone Vorgänge können außerdem den Durchsatz in Webanwendungen erhöhen, in denen der Thread für andere Anforderungen freigegeben werden kann, während der Datenbankvorgang abgeschlossen wird. Weitere Informationen finden Sie unter [Asynchrone Programmierung in C#](/dotnet/csharp/async).

> [!WARNING]  
> EF Core unterstützt nicht die Ausführung mehrerer paralleler Vorgänge, die auf derselben Kontextinstanz ausgeführt werden. Sie sollten immer auf den Abschluss eines Vorgangs warten, bevor Sie den nächsten starten. In der Regel erfolgt dies für alle asynchronen Vorgänge durch das Schlüsselwort `await`.

Entity Framework Core stellt `DbContext.SaveChangesAsync()` als asynchrone Alternative zu `DbContext.SaveChanges()` bereit.

[!code-csharp[Main](../../../samples/core/Saving/Async/Sample.cs#Sample)]
