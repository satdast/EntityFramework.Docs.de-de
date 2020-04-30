---
title: Testen mit der EF-in-Memory-Datenbank-EF Core
author: ajcvickers
description: Verwenden der EF-in-Memory Database zum Testen einer EF Core-Anwendung
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: f31b3bdedb8c339dbb6baa9d7f2031d023d5757f
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538351"
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
