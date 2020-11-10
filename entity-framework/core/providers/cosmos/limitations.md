---
title: Azure Cosmos DB Anbieter-Einschränkungen-EF Core
description: Einschränkungen der Entity Framework Core Azure Cosmos DB Anbieter im Vergleich zu anderen Anbietern
author: AndriySvyryd
ms.date: 11/05/2020
uid: core/providers/cosmos/limitations
ms.openlocfilehash: f7517954b854c8ea1020c039e490b4fb0dea7181
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430195"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a>Einschränkungen für EF Core Azure Cosmos DB-Anbieter

Der Cosmos-Anbieter hat eine Reihe von Einschränkungen. Viele dieser Einschränkungen sind das Ergebnis von Einschränkungen in der zugrunde liegenden Cosmos-Datenbank-Engine und nicht spezifisch für EF. Die meisten [sind jedoch noch nicht implementiert](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).

## <a name="temporary-limitations"></a>Temporäre Einschränkungen

- `Include` Aufrufe werden nicht unterstützt.
- `Join` Aufrufe werden nicht unterstützt.

## <a name="azure-cosmos-db-sdk-limitations"></a>Azure Cosmos DB SDK-Einschränkungen

- Es werden nur Async-Methoden bereitgestellt.

> [!WARNING]
> Da es keine Synchronisierungs Versionen der Low-Level-Methoden gibt EF Core die von abhängig sind, wird die entsprechende Funktionalität derzeit durch Aufrufen von `.Wait()` für das zurückgegebene implementiert `Task` . Dies bedeutet, dass die Verwendung `SaveChanges` von Methoden wie oder anstelle der asynchronen `ToList` Gegenstücke zu einem Deadlock in der Anwendung führen kann.

## <a name="azure-cosmos-db-limitations"></a>Einschränkungen für Azure Cosmos DB

Im Vergleich zu einer relationalen Datenbank finden Sie die vollständige Übersicht über [Azure Cosmos DB unterstützten Features](/azure/cosmos-db/modeling-data).

- Vom Client initiierte Transaktionen werden nicht unterstützt.
- Einige Partitions übergreifende Abfragen sind abhängig von den beteiligten Operatoren langsamer (z. b. `Skip/Take` oder `OFFSET LIMIT` ).
