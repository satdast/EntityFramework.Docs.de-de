---
title: Erweiterte Leistungs Themen
description: Erweiterte Leistungs Themen für Entity Framework Core
author: rick-anderson
ms.author: riande
ms.date: 12/9/2020
uid: core/performance/advanced-performance-topics
ms.openlocfilehash: 3c0340e1b36cbbb96d23db0633cb2eebc04dd970
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657735"
---
# <a name="advanced-performance-topics"></a>Erweiterte Leistungs Themen

## <a name="dbcontext-pooling"></a>DbContext-Pooling

`AddDbContextPool` ermöglicht das Pooling von `DbContext` Instanzen. Durch das Kontext Pooling kann der Durchsatz in Szenarios mit hoher Skalierung (z. b. Webserver durch Wiederverwendung von Kontext Instanzen) gesteigert werden, anstatt für jede Anforderung neue Instanzen zu erstellen.

Das typische Muster in einer ASP.net Core-APP, die EF Core verwendet, umfasst das Registrieren eines benutzerdefinierten <xref:Microsoft.EntityFrameworkCore.DbContext> Typs im Container für die [Abhängigkeitsinjektion](/aspnet/core/fundamentals/dependency-injection) und das Abrufen von Instanzen dieses Typs über Konstruktorparameter in Controllern oder Razor pages. Mithilfe der Konstruktorinjektion wird für jede Anforderung eine neue Kontext Instanz erstellt.

<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> aktiviert einen Pool von wiederverwendbaren Kontext Instanzen. Um Kontext Pooling zu verwenden, verwenden Sie die- `AddDbContextPool` Methode anstelle von `AddDbContext` bei der Dienst Registrierung:

```csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

Wenn zum `AddDbContextPool` Zeitpunkt der Anforderung einer Kontext Instanz verwendet wird, prüft EF zunächst, ob eine Instanz im Pool verfügbar ist. Sobald die Anforderungsverarbeitung abgeschlossen ist, werden alle Zustände der Instanz zurückgesetzt, und die Instanz selbst wird an den Pool zurückgegeben.

Dies ist konzeptionell vergleichbar mit der Funktionsweise von Verbindungspooling in ADO.NET-Anbietern und hat den Vorteil, dass einige der Kosten für die Initialisierung der Kontext Instanz eingespart werden.

Der- `poolSize` Parameter von <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> legt die maximale Anzahl von-Instanzen fest, die vom Pool beibehalten werden. Sobald `poolSize` überschritten wird, werden neue Kontext Instanzen nicht zwischengespeichert, und EF greift auf das nicht-Pooling-Verhalten zurück, wenn Bedarfs gesteuert Instanzen erstellt werden.

### <a name="limitations"></a>Einschränkungen

Für apps sollte ein Profil erstellt und getestet werden, um anzuzeigen, dass die kontextinitialisierung einen erheblichen Aufwand verursacht.

`AddDbContextPool` in gibt es einige Einschränkungen, die in der- `OnConfiguring` Methode des-Kontexts ausgeführt werden können.

> [!WARNING]
> Vermeiden Sie die Verwendung von Kontext Pooling in apps, die den Zustand beibehalten. Beispielsweise private Felder im Kontext, die nicht über Anforderungen hinweg freigegeben werden sollten. EF Core setzt den Zustand, den es kennt, nur dann zurück, wenn dem Pool eine Kontext Instanz hinzugefügt wurde.

Das Kontext Pooling verwendet die gleiche Kontext Instanz in allen Anforderungen. Dies bedeutet, dass Sie als [Singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) in Bezug auf die Instanz selbst registriert ist, damit Sie persistent gespeichert werden kann.

Das Kontext Pooling ist für Szenarien vorgesehen, in denen die Kontext Konfiguration, die die aufgelösten Dienste umfasst, zwischen Anforderungen korrigiert wird. Für Fälle, [in denen](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) Bereichs bezogene Dienste erforderlich sind oder die Konfiguration geändert werden muss, verwenden Sie Pooling nicht. Der Leistungsgewinn von Pooling ist in der Regel mit Ausnahme von hochgradig optimierten Szenarien unerheblich.

## <a name="query-caching-and-parameterization"></a>Zwischenspeichern von Abfragen und Parametrisierung

Wenn EF eine LINQ-Abfrage Struktur zur Ausführung empfängt, muss die Struktur zuerst in eine SQL-Abfrage kompiliert werden. Da es sich hierbei um einen hohen Prozess handelt, speichert EF Abfragen nach der *Form* des Abfrage Baums zwischen: Abfragen mit derselben Struktur verwenden intern zwischengespeicherte Kompilierungs Ausgaben und können die wiederholte Kompilierung überspringen. Die unterschiedlichen Abfragen verweisen möglicherweise weiterhin auf andere *Werte*, aber solange diese Werte ordnungsgemäß parametrisiert sind, ist die Struktur identisch, und die Zwischenspeicherung funktioniert ordnungsgemäß.

Beachten Sie die folgenden beiden Abfragen:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#QueriesWithConstants)]

Da die Ausdrucks Baumstrukturen unterschiedliche Konstanten enthalten, unterscheidet sich die Ausdrucks Baumstruktur, und jede dieser Abfragen wird separat durch EF Core kompiliert. Außerdem erzeugt jede Abfrage einen etwas anderen SQL-Befehl:

```sql
SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = N'blog1'

SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = N'blog2'
```

Da SQL sich unterscheidet, muss der Datenbankserver wahrscheinlich auch einen Abfrageplan für beide Abfragen und nicht denselben Plan wieder verwenden.

Eine kleine Änderung an Ihren Abfragen kann die Dinge erheblich ändern:

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#QueriesWithParameterization)]

Da der Blog Name nun *parametrisiert* ist, haben beide Abfragen dieselbe Struktur Form, und EF muss nur einmal kompiliert werden. Der erstellte SQL-Code wird ebenfalls parametrisiert, sodass die Datenbank denselben Abfrageplan wieder verwenden kann:

```sql
SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = @__blogName_0
```

Beachten Sie, dass es nicht erforderlich ist, jede einzelne Abfrage zu parametrisieren: Es ist in Ordnung, einige Abfragen mit Konstanten zu haben, und Datenbanken (und EF) können manchmal bestimmte Optimierungen für Konstanten ausführen, die bei parametrisierten Abfragen nicht möglich sind. Ein Beispiel für eine wichtige Parametrisierung finden Sie im Abschnitt über [dynamisch erstellte Abfragen](#dynamically-constructed-queries) .

> [!NOTE]
> Die [Ereignis](xref:core/logging-events-diagnostics/event-counters) Indikatoren von EF Core melden die Trefferquote für den Abfrage Cache. In einer normalen Anwendung erreicht dieser Wert nach dem Programmstart 100%, sobald die meisten Abfragen mindestens einmal ausgeführt wurden. Wenn dieser Indikator unter 100% stabil bleibt, weist dies darauf hin, dass Ihre Anwendung möglicherweise etwas bewirkt, das den Abfragecache unterscheidet. es ist ratsam, dies zu untersuchen.

> [!NOTE]
> Die Art und Weise, wie die Datenbank Cache-Abfrage Pläne verwaltet, ist Daten Bank abhängig Beispielsweise wird von SQL Server implizit ein LRU-Abfrageplan Cache verwaltet, während dies bei PostgreSQL nicht der gibt (aber vorbereitete-Anweisungen können zu einem sehr ähnlichen Endeffekt führen). Weitere Informationen finden Sie in der Datenbankdokumentation.

## <a name="dynamically-constructed-queries"></a>Dynamisch erstellte Abfragen

In einigen Situationen ist es notwendig, LINQ-Abfragen dynamisch zu erstellen, anstatt Sie im Quellcode direkt anzugeben. Dies kann z. b. auf einer Website vorkommen, die beliebige Abfrage Details von einem Client empfängt, mit offenen Abfrage Operatoren (Sortieren, Filtern, Paging...). Im Prinzip können dynamisch erstellte Abfragen bei ordnungsgemäßer Ausführung genauso effizient sein wie reguläre (obwohl es nicht möglich ist, die kompilierte Abfrageoptimierung mit dynamischen Abfragen zu verwenden). In der Praxis handelt es sich jedoch häufig um Leistungsprobleme, da es einfach ist, Ausdrucks Baumstrukturen mit Formen zu schaffen, die sich jedes Mal voneinander unterscheiden.

Im folgenden Beispiel werden zwei Techniken verwendet, um eine Abfrage dynamisch zu erstellen: `Where` der Abfrage wird nur dann ein Operator hinzugefügt, wenn der angegebene Parameter nicht NULL ist. Beachten Sie, dass dies kein guter Anwendungsfall für das dynamische Erstellen einer Abfrage ist, aber wir verwenden Sie zur Vereinfachung:

### <a name="with-constant"></a>[Mit Konstante](#tab/with-constant)

[!code-csharp[Main](../../../samples/core/Benchmarks/DynamicallyConstructedQueries.cs?name=WithConstant&highlight=14-24)]

### <a name="with-parameter"></a>[With-Parameter](#tab/with-parameter)

[!code-csharp[Main](../../../samples/core/Benchmarks/DynamicallyConstructedQueries.cs?name=WithParameter&highlight=14)]

***

Das Benchmarkverfahren führt zu den folgenden Ergebnissen:

|        Methode |       Mittelwert |    Fehler |    StdDev |   Gen 0 |  Gen 1 | Gen 2 | Zugeordnet |
|-------------- |-----------:|---------:|----------:|--------:|-------:|------:|----------:|
|  Withconstant | 1.096,7 US- | 12,54 US- |  11,12 US- | 13,6719 | 1,9531 |     - |  83,91 KB |
| Withparameter |   570,8 US- | 42,43 US- | 124,43 US- |  5,8594 |      - |     - |  37,16 KB |

Auch wenn der Unterschied zwischen unter Millisekunden gering erscheint, sollten Sie Bedenken, dass die Konstante Version den Cache kontinuierlich verschmutzt und die erneute Kompilierung anderer Abfragen bewirkt.

> [!NOTE]
> Vermeiden Sie das Erstellen von Abfragen mit der Ausdrucks Baumstruktur-API, es sei denn, Sie müssen Abgesehen von der Komplexität der API ist es sehr einfach, bei der Verwendung versehentlich erhebliche Leistungsprobleme zu verursachen.
