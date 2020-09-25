---
title: Explizites Laden zugehöriger Daten – EF Core
description: Explizites Laden zugehöriger Daten mit Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/explicit
ms.openlocfilehash: 4cfc11237b498f5357476ee4ad96fdc279cd3fee
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078869"
---
# <a name="explicit-loading-of-related-data"></a>Explizites Laden zugehöriger Daten

## <a name="explicit-loading"></a>Explizites Laden

Sie können eine Navigationseigenschaft über die API `DbContext.Entry(...)` explizit laden.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

Sie können eine Navigationseigenschaft auch explizit laden, indem Sie eine separate Abfrage ausführen, die die zugehörigen Entitäten zurückgibt. Wenn die Änderungsnachverfolgung aktiviert ist, legt EF Core, wenn eine Abfrage eine Entität materialisiert, die Navigationseigenschaften der neu geladenen Entität automatisch so fest, dass diese auf bereits geladene Entitäten verweisen, und die Navigationseigenschaften der bereits geladenen Entitäten so, dass diese auf die neu geladene Entität verweisen.

## <a name="querying-related-entities"></a>Abfragen zugehöriger Entitäten

Sie können auch eine LINQ-Abfrage abrufen, welche die Inhalte einer Navigationseigenschaft darstellt.

Damit können Sie zusätzliche Operatoren auf die Abfrage anwenden. Beispielsweise können Sie einen Aggregatoperator auf die verknüpften Entitäten anwenden, ohne diese in den Arbeitsspeicher laden zu müssen.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

Darüber hinaus können Sie filtern, welche zugehörigen Entitäten in den Speicher geladen werden.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]
