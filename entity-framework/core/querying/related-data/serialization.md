---
title: Zugehörige Daten und Serialisierung – EF Core
description: Informationen dazu, wie sich Zyklen in zugehörigen Daten bei Entity Framework Core auf Serialisierungsframeworks auswirken können
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/serialization
ms.openlocfilehash: 6b28a1fc79a8c23e5249d36386c8ed06805dbe30
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078866"
---
# <a name="related-data-and-serialization"></a><span data-ttu-id="d413c-103">Zugehörige Daten und Serialisierung</span><span class="sxs-lookup"><span data-stu-id="d413c-103">Related data and serialization</span></span>

<span data-ttu-id="d413c-104">Da EF Core Navigationseigenschaften automatisch korrigiert, können sich Zyklen in Ihrem Objektgraph ergeben.</span><span class="sxs-lookup"><span data-stu-id="d413c-104">Because EF Core automatically does fix-up of navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="d413c-105">Das Laden eines Blogs und der zugehörigen Beiträge führt beispielsweise zu einem Blogobjekt, das auf eine Sammlung von Beiträgen verweist.</span><span class="sxs-lookup"><span data-stu-id="d413c-105">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="d413c-106">Die einzelnen Beiträge verweisen wieder auf den Blog.</span><span class="sxs-lookup"><span data-stu-id="d413c-106">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="d413c-107">Einige Serialisierungsframeworks lassen solche Zyklen nicht zu.</span><span class="sxs-lookup"><span data-stu-id="d413c-107">Some serialization frameworks don't allow such cycles.</span></span> <span data-ttu-id="d413c-108">In Json.NET wird beispielsweise die folgende Ausnahme ausgelöst, wenn ein Zyklus erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="d413c-108">For example, Json.NET will throw the following exception if a cycle is found.</span></span>

> <span data-ttu-id="d413c-109">Newtonsoft.Json.JsonSerializationException: Für die Eigenschaft „Blog“ mit dem Typ „MyApplication.Models.Blog“ wurde eine auf sich selbst verweisende Schleife erkannt.</span><span class="sxs-lookup"><span data-stu-id="d413c-109">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="d413c-110">Wenn Sie ASP.NET Core verwenden, können Sie Json.NET so konfigurieren, dass im Objektgraph gefundene Zyklen ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="d413c-110">If you're using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="d413c-111">Diese Konfiguration wird in der Methode `ConfigureServices(...)` in `Startup.cs` vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="d413c-111">This configuration is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

<span data-ttu-id="d413c-112">Eine weitere Alternative ist die Ergänzung einer der Navigationseigenschaften mit dem `[JsonIgnore]`-Attribut, das Json.NET anweist, diese Navigationseigenschaft während der Serialisierung nicht zu durchlaufen.</span><span class="sxs-lookup"><span data-stu-id="d413c-112">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
