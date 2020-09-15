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
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="9411e-103">Wechsel zwischen mehreren Modellen mit demselben dbcontext-Typ</span><span class="sxs-lookup"><span data-stu-id="9411e-103">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="9411e-104">Das in integrierte Modell `OnModelCreating` kann eine Eigenschaft im Kontext verwenden, um zu ändern, wie das Modell erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9411e-104">The model built in `OnModelCreating` can use a property on the context to change how the model is built.</span></span> <span data-ttu-id="9411e-105">Angenommen, Sie möchten eine Entität auf der Grundlage einer Eigenschaft auf unterschiedliche Weise konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="9411e-105">For example, suppose you wanted to configure an entity differently based on some property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

<span data-ttu-id="9411e-106">Leider funktioniert dieser Code nicht unverändert, da EF das Modell erstellt und `OnModelCreating` nur einmal ausgeführt wird, um das Ergebnis aus Leistungsgründen zwischenzuspeichern.</span><span class="sxs-lookup"><span data-stu-id="9411e-106">Unfortunately, this code wouldn't work as-is, since EF builds the model and runs `OnModelCreating` only once, caching the result for performance reasons.</span></span> <span data-ttu-id="9411e-107">Sie können jedoch einen Hook in den Mechanismus zum Zwischenspeichern von Modellen durchführen, um EF die Eigenschaft zu berücksichtigen, die verschiedene Modelle erzeugt.</span><span class="sxs-lookup"><span data-stu-id="9411e-107">However, you can hook into the model caching mechanism to make EF aware of the property producing different models.</span></span>

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="9411e-108">Imodelcachekeyfactory</span><span class="sxs-lookup"><span data-stu-id="9411e-108">IModelCacheKeyFactory</span></span>

<span data-ttu-id="9411e-109">EF verwendet zum `IModelCacheKeyFactory` Generieren von Cache Schlüsseln für Modelle. Standardmäßig geht EF davon aus, dass das Modell für jeden beliebigen Kontexttyp identisch ist, sodass die Standard Implementierung dieses dienstantors einen Schlüssel zurückgibt, der nur den Kontexttyp enthält.</span><span class="sxs-lookup"><span data-stu-id="9411e-109">EF uses the `IModelCacheKeyFactory` to generate cache keys for models; by default, EF assumes that for any given context type the model will be the same, so the default implementation of this service returns a key that just contains the context type.</span></span> <span data-ttu-id="9411e-110">Um verschiedene Modelle desselben Kontext Typs zu erzeugen, müssen Sie den `IModelCacheKeyFactory` Dienst durch die richtige Implementierung ersetzen. der generierte Schlüssel wird mit anderen Modell Schlüsseln verglichen, wobei die-Methode verwendet wird `Equals` , wobei alle Variablen berücksichtigt werden, die sich auf das Modell auswirken.</span><span class="sxs-lookup"><span data-stu-id="9411e-110">To produce different models from the same context type, you need to replace the `IModelCacheKeyFactory` service with the correct implementation; the generated key will be compared to other model keys using the `Equals` method, taking into account all the variables that affect the model.</span></span>

<span data-ttu-id="9411e-111">Beim `UseIntProperty` Erstellen eines Modell Cache Schlüssels wird die folgende Implementierung berücksichtigt:</span><span class="sxs-lookup"><span data-stu-id="9411e-111">The following implementation takes the `UseIntProperty` into account when producing a model cache key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

<span data-ttu-id="9411e-112">Registrieren Sie schließlich ihre neue `IModelCacheKeyFactory` in ihrem Kontext `OnConfiguring` :</span><span class="sxs-lookup"><span data-stu-id="9411e-112">Finally, register your new `IModelCacheKeyFactory` in your context's `OnConfiguring`:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

<span data-ttu-id="9411e-113">Weitere Informationen finden Sie im [vollständigen Beispiel Projekt](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) .</span><span class="sxs-lookup"><span data-stu-id="9411e-113">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) for more context.</span></span>
