---
title: Testen mit der EF In-Memory-Datenbank EF Core
description: Verwenden der EF-in-Memory Database zum Testen einer Entity Framework Core-Anwendung
author: ajcvickers
ms.date: 10/27/2016
uid: core/testing/in-memory
ms.openlocfilehash: 78dcac3d0fd69110986c99a097a864104caa1951
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128809"
---
# <a name="testing-with-the-ef-in-memory-database"></a>Testen mit der EF In-Memory-Datenbank

> [!WARNING]
> Das EF-in-Memory Database verhält sich häufig anders als relationale Datenbanken.
> Verwenden Sie nur die EF-in-Memory Database, nachdem Sie sich mit den Problemen und vor-und Nachteile vertraut machen, wie unter [Testen von Code mit EF Core](xref:core/testing/index)erläutert.

> [!TIP]
> SQLite ist ein relationaler Anbieter und kann auch in-Memory-Datenbanken verwenden.
> Verwenden Sie diese für Tests, um das allgemeine Verhalten von relationalen Datenbanken genauer zu vergleichen.
> Dies wird in [Verwenden von SQLite zum Testen einer EF Core Anwendung](xref:core/testing/sqlite)beschrieben.

Die Informationen auf dieser Seite befinden sich jetzt an anderen Standorten:

* Allgemeine Informationen zum Testen mit dem EF-in-Memory Database finden Sie unter [Testen von Code, der EF Core verwendet](xref:core/testing/index) .
* Im [Beispiel wird gezeigt, wie Sie Anwendungen testen, die EF Core](xref:core/testing/testing-sample) für ein Beispiel mithilfe der EF-in-Memory Database verwenden.
* Allgemeine Informationen über die EF-in-Memory Database finden Sie [im EF in-Memory Database-Anbieter](xref:core/providers/in-memory/index) .
