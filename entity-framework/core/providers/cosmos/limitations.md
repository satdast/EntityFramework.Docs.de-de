---
title: Azure Cosmos DB Anbieter-Einschränkungen-EF Core
description: Einschränkungen der Entity Framework Core Azure Cosmos DB Anbieter im Vergleich zu anderen Anbietern
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/limitations
ms.openlocfilehash: e4d1c38da14d1e722797f8543313a69c7fb088cc
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064036"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a>Einschränkungen für EF Core Azure Cosmos DB-Anbieter

Der Cosmos-Anbieter hat eine Reihe von Einschränkungen. Viele dieser Einschränkungen sind das Ergebnis von Einschränkungen in der zugrunde liegenden Cosmos-Datenbank-Engine und nicht spezifisch für EF. Die meisten [sind jedoch noch nicht implementiert](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).

## <a name="temporary-limitations"></a>Temporäre Einschränkungen

- Auch wenn nur ein Entitätstyp ohne Vererbung einem Container zugeordnet ist, verfügt er immer noch über eine diskriminatoreigenschaft.
- Entitäts Typen mit Partitions Schlüsseln funktionieren in einigen Szenarios nicht ordnungsgemäß.
- `Include` Aufrufe werden nicht unterstützt.
- `Join` Aufrufe werden nicht unterstützt.

## <a name="azure-cosmos-db-sdk-limitations"></a>Azure Cosmos DB SDK-Einschränkungen

- Es werden nur Async-Methoden bereitgestellt.

> [!WARNING]
> Da es keine Synchronisierungs Versionen der Low-Level-Methoden gibt EF Core die von abhängig sind, wird die entsprechende Funktionalität derzeit durch Aufrufen von `.Wait()` für das zurückgegebene implementiert `Task` . Dies bedeutet, dass die Verwendung `SaveChanges` von Methoden wie oder anstelle der asynchronen `ToList` Gegenstücke zu einem Deadlock in der Anwendung führen kann.

## <a name="azure-cosmos-db-limitations"></a>Einschränkungen für Azure Cosmos DB

Im Vergleich zu einer relationalen Datenbank finden Sie die vollständige Übersicht über [Azure Cosmos DB unterstützten Features](/azure/cosmos-db/modeling-data).

- Vom Client initiierte Transaktionen werden nicht unterstützt.
- Einige Partitions übergreifende Abfragen werden abhängig von den beteiligten Operatoren entweder nicht unterstützt oder sind viel langsamer.
