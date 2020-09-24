---
title: Dbcontext-Pooling
description: Dbcontext-Pooling in Entity Framework Core
author: rick-anderson
ms.author: riande
ms.date: 9/19/2020
uid: core/miscellaneous/context-pooling
ms.openlocfilehash: fd5f53ff97a73895f0c4239439730dd8cb3ecc29
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2020
ms.locfileid: "91215583"
---
# <a name="dbcontext-pooling"></a>DbContext-Pooling

`AddDbContextPool` ermöglicht das Pooling von `DbContext` Instanzen. Durch das Kontext Pooling kann der Durchsatz in Szenarios mit hoher Skalierung (z. b. Webserver durch Wiederverwendung von Kontext Instanzen) gesteigert werden, anstatt für jede Anforderung neue Instanzen zu erstellen.

Das typische Muster in einer ASP.net Core-APP, die EF Core verwendet, umfasst das Registrieren eines benutzerdefinierten <xref:Microsoft.EntityFrameworkCore.DbContext> Typs im Container für die [Abhängigkeitsinjektion](/aspnet/core/fundamentals/dependency-injection) und das Abrufen von Instanzen dieses Typs über Konstruktorparameter in Controllern oder Razor pages. Mithilfe der Konstruktorinjektion wird für jede Anforderung eine neue Kontext Instanz erstellt.

<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> aktiviert einen Pool von wiederverwendbaren Kontext Instanzen. Um Kontext Pooling zu verwenden, verwenden Sie die- `AddDbContextPool` Methode anstelle von `AddDbContext` bei der Dienst Registrierung:

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

Wenn zum `AddDbContextPool` Zeitpunkt der Anforderung einer Kontext Instanz verwendet wird, prüft EF zunächst, ob eine Instanz im Pool verfügbar ist. Sobald die Anforderungsverarbeitung abgeschlossen ist, werden alle Zustände der Instanz zurückgesetzt, und die Instanz selbst wird an den Pool zurückgegeben.

Dies ist konzeptionell vergleichbar mit der Funktionsweise von Verbindungspooling in ADO.NET-Anbietern und hat den Vorteil, dass einige der Kosten für die Initialisierung der Kontext Instanz eingespart werden.

Der- `poolSize` Parameter von <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> legt die maximale Anzahl von-Instanzen fest, die vom Pool beibehalten werden. Sobald `poolSize` überschritten wird, werden neue Kontext Instanzen nicht zwischengespeichert, und EF greift auf das nicht-Pooling-Verhalten zurück, wenn Bedarfs gesteuert Instanzen erstellt werden.

## <a name="limitations"></a>Einschränkungen

Für apps sollte ein Profil erstellt und getestet werden, um anzuzeigen, dass die kontextinitialisierung einen erheblichen Aufwand verursacht.

`AddDbContextPool` in gibt es einige Einschränkungen, die in der- `OnConfiguring` Methode des-Kontexts ausgeführt werden können.

> [!WARNING]  
> Vermeiden Sie die Verwendung von Kontext Pooling in apps, die den Zustand beibehalten. Beispielsweise private Felder im Kontext, die nicht über Anforderungen hinweg freigegeben werden sollten. EF Core setzt den Zustand, den es kennt, nur dann zurück, wenn dem Pool eine Kontext Instanz hinzugefügt wurde.

Das Kontext Pooling verwendet die gleiche Kontext Instanz in allen Anforderungen. Dies bedeutet, dass Sie als [Singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) in Bezug auf die Instanz selbst registriert ist, damit Sie persistent gespeichert werden kann.

Das Kontext Pooling ist für Szenarien vorgesehen, in denen die Kontext Konfiguration, die die aufgelösten Dienste umfasst, zwischen Anforderungen korrigiert wird. Für Fälle, [in denen](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) Bereichs bezogene Dienste erforderlich sind oder die Konfiguration geändert werden muss, verwenden Sie Pooling nicht. Der Leistungsgewinn von Pooling ist in der Regel mit Ausnahme von hochgradig optimierten Szenarien unerheblich.
