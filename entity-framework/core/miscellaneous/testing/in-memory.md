---
title: Testen mit der EF-in-Memory-Datenbank-EF Core
description: Verwenden der EF-in-Memory Database zum Testen einer Entity Framework Core-Anwendung
author: ajcvickers
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 353f67fb0e78fefa74fc77d302e505bacb692ed4
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619392"
---
# <a name="testing-with-the-ef-in-memory-database"></a>Testen mit der EF-in-Memory-Datenbank

> [!WARNING]
> Das EF-in-Memory Database verhält sich häufig anders als relationale Datenbanken.
> Verwenden Sie nur die EF-in-Memory Database, nachdem Sie sich mit den Problemen und vor-und Nachteile vertraut machen, wie unter [Testen von Code mit EF Core](xref:core/miscellaneous/testing/index)erläutert.  

> [!TIP]
> SQLite ist ein relationaler Anbieter und kann auch in-Memory-Datenbanken verwenden.
> Verwenden Sie diese für Tests, um das allgemeine Verhalten von relationalen Datenbanken genauer zu vergleichen.
> Dies wird in [Verwenden von SQLite zum Testen einer EF Core Anwendung](xref:core/miscellaneous/testing/sqlite)beschrieben.   

Die Informationen auf dieser Seite befinden sich jetzt an anderen Standorten:
* Allgemeine Informationen zum Testen mit dem EF-in-Memory Database finden Sie unter [Testen von Code, der EF Core verwendet](xref:core/miscellaneous/testing/index) .
* Im [Beispiel wird gezeigt, wie Sie Anwendungen testen, die EF Core](xref:core/miscellaneous/testing/testing-sample) für ein Beispiel mithilfe der EF-in-Memory Database verwenden.
* Allgemeine Informationen über die EF-in-Memory Database finden Sie [im EF in-Memory Database-Anbieter](xref:core/providers/in-memory/index) .
