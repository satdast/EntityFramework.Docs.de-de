---
title: Testen mit der EF In-Memory-Datenbank EF Core
description: Verwenden der EF-in-Memory Database zum Testen einer Entity Framework Core-Anwendung
author: ajcvickers
ms.date: 10/27/2016
uid: core/testing/in-memory
ms.openlocfilehash: db91570dc9d5a4b95d513df509867e9bca406356
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431225"
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
