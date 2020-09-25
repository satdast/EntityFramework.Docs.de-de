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
# <a name="related-data-and-serialization"></a>Zugehörige Daten und Serialisierung

Da EF Core Navigationseigenschaften automatisch korrigiert, können sich Zyklen in Ihrem Objektgraph ergeben. Das Laden eines Blogs und der zugehörigen Beiträge führt beispielsweise zu einem Blogobjekt, das auf eine Sammlung von Beiträgen verweist. Die einzelnen Beiträge verweisen wieder auf den Blog.

Einige Serialisierungsframeworks lassen solche Zyklen nicht zu. In Json.NET wird beispielsweise die folgende Ausnahme ausgelöst, wenn ein Zyklus erkannt wird.

> Newtonsoft.Json.JsonSerializationException: Für die Eigenschaft „Blog“ mit dem Typ „MyApplication.Models.Blog“ wurde eine auf sich selbst verweisende Schleife erkannt.

Wenn Sie ASP.NET Core verwenden, können Sie Json.NET so konfigurieren, dass im Objektgraph gefundene Zyklen ignoriert werden. Diese Konfiguration wird in der Methode `ConfigureServices(...)` in `Startup.cs` vorgenommen.

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

Eine weitere Alternative ist die Ergänzung einer der Navigationseigenschaften mit dem `[JsonIgnore]`-Attribut, das Json.NET anweist, diese Navigationseigenschaft während der Serialisierung nicht zu durchlaufen.
