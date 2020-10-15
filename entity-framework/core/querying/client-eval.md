---
title: Clientauswertung im Vergleich zur Serverauswertung – EF Core
description: Auswertung von Abfragen mit Entity Framework Core auf dem Client und auf dem Server
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/client-eval
ms.openlocfilehash: f2e80541439de8cc824c182e52400f730dd2af48
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062710"
---
# <a name="client-vs-server-evaluation"></a>Clientauswertung im Vergleich zur Serverauswertung

Im Allgemeinen versucht Entity Framework Core, eine Abfrage auf dem Server möglichst weitgehend auszuwerten. EF Core konvertiert Teile der Abfrage in Parameter, die auf dem Client ausgewertet werden können. Der Rest der Abfrage wird (mitsamt den generierten Parametern) an die Datenbankanbieter übermittelt, um die entsprechende auszuwertende Datenbankabfrage auf dem Server zu ermitteln. EF Core unterstützt die teilweise Clientauswertung der Projektion auf oberster Ebene (den letzten Aufruf von `Select()`). Wenn die Projektion auf oberster Ebene in der Abfrage nicht auf den Server übersetzt werden kann, ruft EF Core alle erforderlichen Daten vom Server an und wertet die übrigen Teile der Abfrage auf dem Client aus. Wenn EF Core einen Ausdruck ermittelt, der sich außerhalb der Projektion auf oberster Ebene befindet und nicht auf den Server übersetzt werden kann, wird eine Laufzeitausnahme ausgelöst. Informationen dazu, wie EF Core ermittelt, was nicht für den Server übersetzt werden kann, finden Sie unter [Funktionsweise von Abfragen](xref:core/querying/how-query-works).

> [!NOTE]
> Vor Version 3.0 hat Entity Framework Core die Clientauswertung überall in der Abfrage unterstützt. Weitere Informationen finden Sie im [Abschnitt zu vorherigen Versionen](#previous-versions).

> [!TIP]
> Das in diesem Artikel verwendete [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/ClientEvaluation) finden Sie auf GitHub.

## <a name="client-evaluation-in-the-top-level-projection"></a>Clientauswertung in der Projektion auf oberster Ebene

Im folgenden Beispiel wird eine Hilfsmethode verwendet, um URLs für Blogs zu standardisieren, die von einer SQL Server-Datenbank zurückgegeben werden. Da der SQL Server-Anbieter keinen Einblick darin hat, wie diese Methode implementiert wird, kann sie nicht in SQL übersetzt werden. Alle anderen Aspekte der Abfrage werden in der Datenbank ausgewertet. Die Rückgabe der `URL` über diese Methode erfolgt jedoch über den Client.

[!code-csharp[Main](../../../samples/core/Querying/ClientEvaluation/Program.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/ClientEvaluation/Program.cs#ClientMethod)]

## <a name="unsupported-client-evaluation"></a>Nicht unterstützte Clientauswertung

Obwohl die Clientauswertung nützlich ist, kann sie manchmal zu Leistungseinbußen führen. Betrachten Sie die folgende Abfrage, in der die Hilfsmethode nun in einem Where-Filter verwendet wird. Da der Filter nicht in der Datenbank angewendet werden kann, müssen alle Daten in den Arbeitsspeicher gepullt werden, um den Filter auf den Client anzuwenden. Basierend auf dem Filter und der Menge der Daten auf dem Server, könnte die Clientauswertung zu Leistungseinbußen führen. Daher blockiert Entity Framework Core diese Clientauswertung und löst eine Laufzeitausnahme aus.

[!code-csharp[Main](../../../samples/core/Querying/ClientEvaluation/Program.cs#ClientWhere)]

## <a name="explicit-client-evaluation"></a>Explizite Clientauswertung

In bestimmten Fällen müssen Sie die Clientauswertung explizit erzwingen, zum Beispiel in den folgenden:

- Wenn die Menge der Daten so klein ist, dass die Auswertung auf dem Client zu keinen großen Leistungseinbußen führt.
- Wenn der verwendete LINQ-Operator über keine serverseitige Übersetzung verfügt.

In solchen Fällen können Sie die Clientauswertung explizit aktivieren, indem Sie Methoden wie `AsEnumerable` oder `ToList` (`AsAsyncEnumerable` oder `ToListAsync` bei asynchronen Abfragen) aufrufen. Wenn Sie `AsEnumerable` verwenden, würden Sie die Ergebnisse streamen. Wenn Sie jedoch `ToList` verwenden, würde eine Pufferung verursacht werden, indem eine Liste erstellt wird, was ebenfalls zusätzlichen Arbeitsspeicher erfordert. Wenn Sie jedoch mehrere Aufzählungen durchführen, ist das Speichern der Ergebnisse in einer Liste hilfreicher, da die Datenbank nur einmal abgefragt wird. Abhängig von der jeweiligen Nutzung sollten Sie auswerten, welche Methode sich am besten eignet.

[!code-csharp[Main](../../../samples/core/Querying/ClientEvaluation/Program.cs#ExplicitClientEvaluation)]

## <a name="potential-memory-leak-in-client-evaluation"></a>Potenzieller Arbeitsspeicherverlust bei der Clientauswertung

Da die Übersetzung und Kompilierung von Abfragen leistungsintensiv ist, wird der kompilierte Abfrageplan von EF Core zwischengespeichert. Der zwischengespeicherte Delegat kann Clientcode während der Clientauswertung der Projektion auf oberster Ebene verwenden. EF Core generiert Parameter für die vom Client ausgewerteten Teile der Struktur und verwendet den Abfrageplan erneut, indem die Parameterwerte ersetzt werden. Gewisse Konstanten in der Ausdrucksbaumstruktur können jedoch nicht in Parameter konvertiert werden. Wenn der zwischengespeicherte Delegat solche Konstanten enthält, kann keine Garbage Collection für diese Objekte durchgeführt werden, da immer noch auf sie verwiesen wird. Wenn ein solches Objekt eine DbContext-Instanz oder andere Dienste enthält, würde die Arbeitsspeichernutzung der App mit der Zeit wachsen. In der Regel ist dieses Verhalten ein Anzeichen für Arbeitsspeicherverlust. EF Core löst eine Ausnahme aus, wenn Konstanten eines Typs ermittelt werden, die nicht mithilfe des aktuellen Datenbankanbieters zugeordnet werden können. Häufige Gründe und Lösungen lauten wie folgt:

- **Verwendung einer Instanzmethode:** Wenn Instanzmethoden in einer Clientprojektion verwendet werden, enthält die Ausdrucksbaumstruktur eine Konstante der Instanz. Wenn Ihre Methode keine Daten aus der Instanz verwendet, sollten Sie in Betracht ziehen, die Methode als statisch festzulegen. Wenn Sie Instanzdaten in den Methodentext einfügen müssen, übergeben Sie die spezifischen Daten als Argument an die Methode.
- **Übergeben konstanter Argumente an die Methode:** Dieser Fall tritt im Allgemeinen auf, wenn `this` in einem Argument für die Clientmethode verwendet wird. Erwägen Sie, das Argument in mehrere skalare Argumente aufzuteilen, die vom Datenbankanbieter zugeordnet werden können.
- **Andere Konstanten:** Wenn eine Konstante in einem anderen Fall ermittelt wird, können Sie bestimmen, ob diese Konstante für die Verarbeitung erforderlich ist. Wenn die Konstante erforderlich ist oder Sie keine Lösung aus den obigen Fällen verwenden können, erstellen Sie eine lokale Variable, um den Wert zu speichern und die lokale Variable in der Abfrage zu verwenden. EF Core konvertiert die lokale Variable in einen Parameter.

## <a name="previous-versions"></a>Frühere Versionen

Der folgende Abschnitt gilt für Versionen von EF Core vor Version 3.0.

Ältere EF Core-Versionen haben die Clientauswertung in beliebigen Teilen der Abfrage unterstützt, nicht nur in der Projektion auf oberster Ebene. Daher haben Abfragen wie die im Abschnitt [Nicht unterstützte Clientauswertung](#unsupported-client-evaluation) ordnungsgemäß funktioniert. Da dieses Verhalten zu unbemerkten Leistungsproblemen führte, hat EF Core eine Warnung für die Clientauswertung protokolliert. Weitere Informationen zum Anzeigen von Protokollierungsausgaben finden Sie unter [Protokollierung](xref:core/miscellaneous/logging).

Optional konnten Sie das Standardverhalten mit EF Core ändern, um bei der Clientauswertung entweder eine Ausnahme auszulösen oder keine Aktion durchzuführen (außer bei der Projektion). Das Verhalten zum Auslösen einer Ausnahme ähnelt dem Verhalten von Version 3.0. Zum Ändern des Verhaltens müssen Sie Warnungen konfigurieren, während Sie die Optionen für Ihren Kontext einrichten. Dies erfolgt in der Regel in `DbContext.OnConfiguring` oder in `Startup.cs`, wenn Sie ASP.NET Core verwenden.

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
