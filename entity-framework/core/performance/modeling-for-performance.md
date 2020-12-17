---
title: Modellierung für Leistungs EF Core
description: Effizientes modellieren bei Verwendung Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/performance/modeling-for-performance
ms.openlocfilehash: fc16ec67c3865aa7b7a95519463ca7493a2709b0
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657729"
---
# <a name="modeling-for-performance"></a>Modellieren der Leistung

In vielen Fällen kann die Art und Weise, wie Sie modellieren, eine tiefgreifende Auswirkung auf die Leistung der Anwendung haben. ein ordnungsgemäß normalisiertes und "korrektes" Modell ist in der Regel ein guter Ausgangspunkt, aber in realen Anwendungen können einige pragmatische Kompromisse eine gute Leistung erzielen. Da es ziemlich schwierig ist, das Modell zu ändern, sobald eine Anwendung in der Produktion ausgeführt wird, sollten Sie die Leistung beim Erstellen des anfänglichen Modells berücksichtigen.

## <a name="denormalization-and-caching"></a>Denormalisierung und Caching

*Denormalization* ist die Vorgehensweise, dem Schema redundante Daten hinzuzufügen, in der Regel, um Joins bei Abfragen auszuschließen. Beispielsweise müssen Sie für ein Modell mit Blogs und Beiträgen, in denen jeder Beitrag eine Bewertung hat, häufig die durchschnittliche Bewertung des Blogs anzeigen. Die einfache Vorgehensweise würde die Beiträge nach Ihrem Blog gruppieren und den Durchschnitt als Teil der Abfrage berechnen. dafür ist jedoch ein kostspieliger Join zwischen den beiden Tabellen erforderlich. Die Denormalisierung würde den berechneten Durchschnitt aller Beiträge zu einer neuen Spalte im Blog hinzufügen, sodass Sie sofort zugänglich ist, ohne zu beitreten oder zu berechnen.

Die *oben genannten können als Form der zwischen* Speicherung angezeigt werden: aggregierte Informationen aus den Beiträgen werden in Ihrem Blog zwischengespeichert. ebenso wie bei jedem Caching besteht das Problem darin, dass der zwischengespeicherte Wert mit den Daten, die zwischengespeichert werden, auf dem neuesten Stand gehalten wird. In vielen Fällen ist es in Ordnung, dass die zwischengespeicherten Daten für ein wenig verzögert werden. im obigen Beispiel ist es in der Regel sinnvoll, dass die durchschnittliche Bewertung des Blogs an einem bestimmten Punkt nicht vollständig auf dem neuesten Stand ist. Wenn dies der Fall ist, können Sie die Anwendung jetzt alle und dann neu berechnen. Andernfalls muss ein aufwendiges System eingerichtet werden, damit die zwischengespeicherten Werte auf dem neuesten Stand gehalten werden.

Im folgenden werden einige Verfahren für Denormalisierung und Caching in EF Core erläutert und auf die relevanten Abschnitte in der-Dokumentation verwiesen.

### <a name="stored-computed-columns"></a>Gespeicherte berechnete Spalten

Wenn die zwischengespeicherten Daten ein Produkt anderer Spalten in der gleichen Tabelle sind, kann eine [gespeicherte berechnete Spalte](xref:core/modeling/generated-properties#computed-columns) eine perfekte Lösung sein. Beispielsweise kann eine `Customer` `FirstName` -Spalte und eine- `LastName` Spalte enthalten, aber wir müssen möglicherweise nach dem *vollständigen Namen* des Kunden suchen. Eine gespeicherte berechnete Spalte wird automatisch von der Datenbank verwaltet. diese wird immer neu berechnet, wenn die Zeile geändert wird, und Sie können sogar einen Index darüber definieren, um Abfragen zu beschleunigen.

### <a name="update-cache-columns-when-inputs-change"></a>Aktualisieren von Cache Spalten bei Änderung von Eingaben

Wenn die zwischengespeicherte Spalte auf Eingaben von außerhalb der Tabellenzeile verweisen muss, können keine berechneten Spalten verwendet werden. Es ist jedoch immer noch möglich, die Spalte neu zu berechnen, wenn die Eingabe geändert wird. Beispielsweise können Sie die Bewertung des durchschnittlichen Blogs jedes Mal neu berechnen, wenn ein Beitrag geändert, hinzugefügt oder entfernt wird. Achten Sie darauf, die genauen Bedingungen zu identifizieren, wenn eine Neuberechnung erforderlich ist. andernfalls wird der zwischengespeicherte Wert nicht mehr synchronisiert.

Eine Möglichkeit besteht darin, das Update selbst über die reguläre EF Core-API auszuführen. `SaveChanges`[Ereignisse](xref:core/logging-events-diagnostics/events) oder [Interceptors](xref:core/logging-events-diagnostics/interceptors#savechanges-interception) können verwendet werden, um automatisch zu überprüfen, ob Beiträge aktualisiert werden, und um die Neuberechnung auf diese Weise auszuführen. Beachten Sie, dass dies in der Regel zusätzliche Datenbankroundtrips umfasst, da zusätzliche Befehle gesendet werden müssen.

Bei größeren Leistungs sensitiven Anwendungen können Daten Bank Trigger definiert werden, um die Neuberechnung in der Datenbank automatisch auszuführen. Dadurch werden zusätzliche Datenbankroundtrips eingespart, die automatisch innerhalb der gleichen Transaktion wie das Haupt Update ausgeführt werden, und es kann einfacher eingerichtet werden. EF bietet keine bestimmte API zum Erstellen oder Verwalten von Triggern, aber es ist ganz schön, [eine leere Migration zu erstellen und die Triggerdefinition über RAW SQL hinzuzufügen](xref:core/managing-schemas/migrations/managing#arbitrary-changes-via-raw-sql).

### <a name="materialized-views"></a>Materialisierte Sichten

Materialisierte Sichten ähneln regulären Sichten, mit dem Unterschied, dass Ihre Daten auf dem Datenträger ("MATERIALIZED") gespeichert werden und nicht jedes Mal berechnet werden, wenn die Sicht abgefragt wird. Dieses Tool ist nützlich, wenn Sie einer vorhandenen Datenbank nicht einfach eine einzelne Cache Spalte hinzufügen möchten, sondern den gesamten Resultset der Ergebnisse komplexer und kostspieliger Abfragen so speichern möchten, als ob es sich um eine reguläre Tabelle handelt. Diese Ergebnisse können dann sehr kostengünstig abgefragt werden, ohne dass Berechnungen oder Joins ausgeführt werden. Im Gegensatz zu berechneten Spalten werden materialisierte Sichten nicht automatisch aktualisiert, wenn sich die zugrunde liegenden Tabellen ändern. Sie müssen manuell aktualisiert werden. Wenn die zwischengespeicherten Daten verzögert werden können, kann das Aktualisieren der Ansicht über einen Timer erfolgen. eine andere Möglichkeit besteht darin, Daten Bank Trigger so einzurichten, dass eine materialisierte Sicht nach bestimmten Daten Bank Ereignissen überprüft wird.

EF stellt derzeit keine bestimmte API zum Erstellen oder Verwalten von Sichten bereit, materialisiert oder anderweitig; Es ist jedoch ganz schön, [eine leere Migration zu erstellen und die Sicht Definition über RAW SQL hinzuzufügen](xref:core/managing-schemas/migrations/managing#arbitrary-changes-via-raw-sql).

## <a name="inheritance-mapping"></a>Vererbungs Zuordnung

Es wird empfohlen, [die dedizierte Seite zur Vererbung](xref:core/modeling/inheritance) zu lesen, bevor Sie mit diesem Abschnitt fortfahren.

EF Core unterstützt derzeit zwei Verfahren zum Mapping eines Vererbungs Modells zu einer relationalen Datenbank:

* **Tabelle pro Hierarchie** (TPH), in der eine gesamte .net-Hierarchie von Klassen einer einzelnen Datenbanktabelle zugeordnet wird
* **Tabelle pro Typ** (TPT), in der jeder Typ in der .net-Hierarchie einer anderen Tabelle in der Datenbank zugeordnet wird.

Die Auswahl der Methode für die Vererbung von Vererbung kann eine beträchtliche Auswirkung auf die Anwendungsleistung haben. es wird empfohlen, vor dem Commit auf eine Auswahl sorgfältig zu messen.

Manchmal wählen Sie TPT aus, da es sich anscheinend um die "saubere" Methode handelt. eine separate Tabelle für jeden .NET-Typ bewirkt, dass das Datenbankschema der .net-Typhierarchie ähnelt. Da TPH die gesamte Hierarchie in einer einzelnen Tabelle darstellen muss, verfügen die Zeilen darüber hinaus über *alle* Spalten, unabhängig vom Typ, der tatsächlich in der Zeile gespeichert ist, und nicht verknüpfte Spalten sind immer leer und werden nicht verwendet. Abgesehen davon, dass es sich um ein unreines Zuordnungs Verfahren handelt, sind viele davon überzeugt, dass diese leeren Spalten beträchtlichen Speicherplatz in der Datenbank beanspruchen und die Leistung beeinträchtigen können.

Die Messung zeigt jedoch, dass TPT in den meisten Fällen die unterste Karten Methode aus Leistungs Sicht verwendet. Wenn alle Daten in TPH aus einer einzelnen Tabelle stammen, müssen TPT-Abfragen mehrere Tabellen miteinander verknüpfen, und Joins sind eine der primären Quellen für Leistungsprobleme in relationalen Datenbanken. Datenbanken neigen in der Regel auch zu leeren Spalten, und Features wie [SQL Server sparsespalten](/sql/relational-databases/tables/use-sparse-columns) können diesen Aufwand noch weiter reduzieren.

Ein konkretes Beispiel finden Sie unter [diesem Benchmarkwert](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Benchmarks/Inheritance.cs) , mit dem ein einfaches Modell mit einer Hierarchie von 7 Typen eingerichtet wird. 5000 Zeilen werden für jeden Typ mit der Grundlage von 35000 Zeilen in einem Seeding angezeigt, und der Vergleichstest lädt einfach alle Zeilen aus der Datenbank:

| Methode |     Mittelwert |   Fehler |  StdDev |     Gen 0 |     Gen 1 |     Gen 2 | Zugeordnet |
|------- |---------:|--------:|--------:|----------:|----------:|----------:|----------:|
|    TPH | 132,3 ms | 2,29 ms | 2,03 MS | 8000,0000 | 3000,0000 | 1250,0000 |  44,49 MB |
|    TPT | 201,3 ms | 3,32 MS | 3,10 ms | 9000,0000 | 4000,0000 |         - |  61,84 MB |

Wie Sie sehen können, ist TPH deutlich effizienter als TPT für dieses Szenario. Beachten Sie, dass die tatsächlichen Ergebnisse immer von der bestimmten ausgeführten Abfrage und der Anzahl der Tabellen in der Hierarchie abhängen, sodass andere Abfragen möglicherweise eine andere Leistungslücke darstellen. Es wird empfohlen, diesen Vergleichs Code als Vorlage zum Testen anderer Abfragen zu verwenden.
