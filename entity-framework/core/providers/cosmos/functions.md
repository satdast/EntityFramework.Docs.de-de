---
title: Funktions Zuordnungen-Azure Cosmos DB Anbieter-EF Core
description: Funktions Zuordnungen des Azure Cosmos DB EF Core Anbieters
author: bricelam
ms.date: 10/08/2020
uid: core/providers/cosmos/functions
ms.openlocfilehash: ffcf82fbe57ea16e6fc62f3c9c80c572b3e1ed91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066511"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a>Funktions Zuordnungen des Azure Cosmos DB EF Core Anbieters

Diese Seite zeigt, welche .net-Member bei Verwendung des Azure Cosmos DB Anbieters in welche SQL-Funktionen übersetzt werden.

.NET                          | SQL                              | Hinzugefügt in
----------------------------- | -------------------------------- | --------
Ausstellung. Enthält (Element)     | @item IN @collection
StringValue. enthält (Wert)   | Enthält ( @stringValue , @value )   | EF Core 5.0
StringValue. EndsWith (Wert)   | EndsWith ( @stringValue , @value )   | EF Core 5.0
StringValue. FirstOrDefault ()  | Links ( @stringValue , 1)            | EF Core 5.0
StringValue. LastOrDefault ()   | Right ( @stringValue , 1)           | EF Core 5.0
StringValue. starttwith (Wert) | Start mit ( @stringValue , @value ) | EF Core 5.0
