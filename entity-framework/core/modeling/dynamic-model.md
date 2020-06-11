---
title: Wechsel zwischen mehreren Modellen mit demselben dbcontext-Typ-EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: 35743b5ba87bb4239d7f758320f9facccc74330f
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664220"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="2847e-102">Wechsel zwischen mehreren Modellen mit demselben dbcontext-Typ</span><span class="sxs-lookup"><span data-stu-id="2847e-102">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="2847e-103">Das in integrierte Modell `OnModelCreating` kann eine Eigenschaft im Kontext verwenden, um zu ändern, wie das Modell erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="2847e-103">The model built in `OnModelCreating` can use a property on the context to change how the model is built.</span></span> <span data-ttu-id="2847e-104">Angenommen, Sie möchten eine Entität auf der Grundlage einer Eigenschaft auf unterschiedliche Weise konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="2847e-104">For example, suppose you wanted to configure an entity differently based on some property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

<span data-ttu-id="2847e-105">Leider funktioniert dieser Code nicht unverändert, da EF das Modell erstellt und `OnModelCreating` nur einmal ausgeführt wird, um das Ergebnis aus Leistungsgründen zwischenzuspeichern.</span><span class="sxs-lookup"><span data-stu-id="2847e-105">Unfortunately, this code wouldn't work as-is, since EF builds the model and runs `OnModelCreating` only once, caching the result for performance reasons.</span></span> <span data-ttu-id="2847e-106">Sie können jedoch einen Hook in den Mechanismus zum Zwischenspeichern von Modellen durchführen, um EF die Eigenschaft zu berücksichtigen, die verschiedene Modelle erzeugt.</span><span class="sxs-lookup"><span data-stu-id="2847e-106">However, you can hook into the model caching mechanism to make EF aware of the property producing different models.</span></span>

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="2847e-107">Imodelcachekeyfactory</span><span class="sxs-lookup"><span data-stu-id="2847e-107">IModelCacheKeyFactory</span></span>

<span data-ttu-id="2847e-108">EF verwendet zum `IModelCacheKeyFactory` Generieren von Cache Schlüsseln für Modelle. Standardmäßig geht EF davon aus, dass das Modell für jeden beliebigen Kontexttyp identisch ist, sodass die Standard Implementierung dieses dienstantors einen Schlüssel zurückgibt, der nur den Kontexttyp enthält.</span><span class="sxs-lookup"><span data-stu-id="2847e-108">EF uses the `IModelCacheKeyFactory` to generate cache keys for models; by default, EF assumes that for any given context type the model will be the same, so the default implementation of this service returns a key that just contains the context type.</span></span> <span data-ttu-id="2847e-109">Um verschiedene Modelle desselben Kontext Typs zu erzeugen, müssen Sie den `IModelCacheKeyFactory` Dienst durch die richtige Implementierung ersetzen. der generierte Schlüssel wird mit anderen Modell Schlüsseln verglichen, wobei die-Methode verwendet wird `Equals` , wobei alle Variablen berücksichtigt werden, die sich auf das Modell auswirken.</span><span class="sxs-lookup"><span data-stu-id="2847e-109">To produce different models from the same context type, you need to replace the `IModelCacheKeyFactory` service with the correct implementation; the generated key will be compared to other model keys using the `Equals` method, taking into account all the variables that affect the model.</span></span>

<span data-ttu-id="2847e-110">Beim `UseIntProperty` Erstellen eines Modell Cache Schlüssels wird die folgende Implementierung berücksichtigt:</span><span class="sxs-lookup"><span data-stu-id="2847e-110">The following implementation takes the `UseIntProperty` into account when producing a model cache key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

<span data-ttu-id="2847e-111">Registrieren Sie schließlich ihre neue `IModelCacheKeyFactory` in ihrem Kontext `OnConfiguring` :</span><span class="sxs-lookup"><span data-stu-id="2847e-111">Finally, register your new `IModelCacheKeyFactory` in your context's `OnConfiguring`:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

<span data-ttu-id="2847e-112">Weitere Informationen finden Sie im [vollständigen Beispiel Projekt](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) .</span><span class="sxs-lookup"><span data-stu-id="2847e-112">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) for more context.</span></span>
