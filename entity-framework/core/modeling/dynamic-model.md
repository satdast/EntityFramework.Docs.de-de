---
title: Wechsel zwischen mehreren Modellen mit demselben dbcontext-Typ-EF Core
description: Wechsel zwischen mehreren Modellen mit demselben dbcontext-Typ mithilfe von Entity Framework Core
author: AndriySvyryd
ms.date: 01/03/2020
uid: core/modeling/dynamic-model
ms.openlocfilehash: 0e0af67eab5262ab2b26edadea470c753b6349a0
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071523"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>Wechsel zwischen mehreren Modellen mit demselben dbcontext-Typ

Das in integrierte Modell `OnModelCreating` kann eine Eigenschaft im Kontext verwenden, um zu ändern, wie das Modell erstellt wird. Angenommen, Sie möchten eine Entität auf der Grundlage einer Eigenschaft auf unterschiedliche Weise konfigurieren:

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

Leider funktioniert dieser Code nicht unverändert, da EF das Modell erstellt und `OnModelCreating` nur einmal ausgeführt wird, um das Ergebnis aus Leistungsgründen zwischenzuspeichern. Sie können jedoch einen Hook in den Mechanismus zum Zwischenspeichern von Modellen durchführen, um EF die Eigenschaft zu berücksichtigen, die verschiedene Modelle erzeugt.

## <a name="imodelcachekeyfactory"></a>Imodelcachekeyfactory

EF verwendet zum `IModelCacheKeyFactory` Generieren von Cache Schlüsseln für Modelle. Standardmäßig geht EF davon aus, dass das Modell für jeden beliebigen Kontexttyp identisch ist, sodass die Standard Implementierung dieses dienstantors einen Schlüssel zurückgibt, der nur den Kontexttyp enthält. Um verschiedene Modelle desselben Kontext Typs zu erzeugen, müssen Sie den `IModelCacheKeyFactory` Dienst durch die richtige Implementierung ersetzen. der generierte Schlüssel wird mit anderen Modell Schlüsseln verglichen, wobei die-Methode verwendet wird `Equals` , wobei alle Variablen berücksichtigt werden, die sich auf das Modell auswirken.

Beim `UseIntProperty` Erstellen eines Modell Cache Schlüssels wird die folgende Implementierung berücksichtigt:

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

Registrieren Sie schließlich ihre neue `IModelCacheKeyFactory` in ihrem Kontext `OnConfiguring` :

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

Weitere Informationen finden Sie im [vollständigen Beispiel Projekt](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) .
