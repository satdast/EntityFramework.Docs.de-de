---
title: Überlegungen zur Leistung für EF4, EF5 und EF6-EF6
description: Überlegungen zur Leistung für Entity Framework 4, 5 und 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/performance/perf-whitepaper
ms.openlocfilehash: ae9374401b66f0493f7318ffcbfd9c4d6a24ada5
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063425"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a>Überlegungen zur Leistung von EF 4, 5 und 6
Von David Obando, Eric erttinger und anderen

Veröffentlicht: April 2012

Letzte Aktualisierung: Mai 2014

------------------------------------------------------------------------

## <a name="1-introduction"></a>1. Einführung

Object-Relational Mapping-Frameworks sind eine bequeme Möglichkeit, eine Abstraktion für den Datenzugriff in einer objektorientierten Anwendung bereitzustellen. Für .NET-Anwendungen ist der empfohlene O/RM von Microsoft Entity Framework. Mit jeder Abstraktion kann die Leistung jedoch zu einem Problem werden.

Dieses Whitepaper wurde geschrieben, um die Leistungs Überlegungen beim Entwickeln von Anwendungen mit Entity Framework zu veranschaulichen, um Entwicklern eine Vorstellung von den Entity Framework internen Algorithmen zu vermitteln, die die Leistung beeinträchtigen können, und um Tipps zur Untersuchung und zur Verbesserung der Leistung in Ihren Anwendungen bereitzustellen, die Entity Framework verwenden. Es gibt eine Reihe von guten Themen zur Leistung, die bereits im Web verfügbar sind, und wir haben auch versucht, nach Möglichkeit auf diese Ressourcen zu verweisen.

Die Leistung ist ein kniffliges Thema. Dieses Whitepaper ist als Ressource gedacht, mit der Sie Leistungs relevante Entscheidungen für Ihre Anwendungen treffen können, die Entity Framework verwenden. Wir haben einige Testmetriken eingefügt, um die Leistung zu veranschaulichen, aber diese Metriken sind nicht als absolute Indikatoren für die Leistung gedacht, die Sie in Ihrer Anwendung sehen werden.

Aus praktischen Gründen wird in diesem Dokument davon ausgegangen, dass Entity Framework 4 unter .NET 4,0 und Entity Framework 5 und 6 unter .NET 4,5 ausgeführt wird. Viele der Leistungsverbesserungen für Entity Framework 5 befinden sich in den Kernkomponenten, die mit .NET 4,5 ausgeliefert werden.

Entity Framework 6 ist ein Out-of-Band-Release und ist nicht von den Entity Framework Komponenten abhängig, die mit .net ausgeliefert werden. Entity Framework 6 funktioniert sowohl mit .NET 4,0 als auch mit .NET 4,5 und bietet einen großen Leistungsvorteil für diejenigen, die kein Upgrade von .NET 4,0 durchgeführt haben, aber die neuesten Entity Framework Bits in Ihrer Anwendung benötigen. Wenn in diesem Dokument Entity Framework 6 erwähnt wird, verweist es auf die neueste Version, die zum Zeitpunkt der Erstellung dieses Artikels verfügbar ist: Version 6.1.0.

## <a name="2-cold-vs-warm-query-execution"></a>2. kalte und warm-Abfrage Ausführung

Das erste Mal, wenn eine Abfrage für ein bestimmtes Modell durchgeführt wird, führt die Entity Framework viel Arbeit im Hintergrund aus, um das Modell zu laden und zu überprüfen. Diese erste Abfrage wird häufig als "kalte" Abfrage bezeichnet.Weitere Abfragen für ein bereits geladenes Modell werden als "warm"-Abfragen bezeichnet und sind viel schneller.

Betrachten wir einen Überblick über die Zeit, die beim Ausführen einer Abfrage mit Entity Framework aufgewendet wird, und sehen uns an, wo sich die Dinge in Entity Framework 6 verbessern.

**Erste Abfrage Ausführung – kalte Abfrage**

| Schreiben von Code Benutzern                                                                                     | Aktion                    | Auswirkung der EF4 Leistung                                                                                                                                                                                                                                                                                                                                                                                                        | Auswirkung der EF5 Leistung                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Auswirkung der EF6 Leistung                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Kontext Erstellung          | Medium                                                                                                                                                                                                                                                                                                                                                                                                                        | Medium                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Erstellung von Abfrage Ausdrücken | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                           | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | LINQ-Abfrage Ausführung      | -Das Laden von Metadaten: hoch, aber zwischengespeichert <br/> -Generierung anzeigen: potenziell sehr hoch, aber zwischengespeichert <br/> -Parameter Auswertung: Mittel <br/> -Abfrage Übersetzung: Mittel <br/> -Materializer-Generierung: Mittel, aber zwischengespeichert <br/> -Datenbankabfrage Ausführung: potenziell hoch <br/> + Verbindung. Öffnen <br/> + Command.Executereader <br/> + DataReader. Read <br/> Objektmaterialisierung: Mittel <br/> -Identitätssuche: Mittel | -Das Laden von Metadaten: hoch, aber zwischengespeichert <br/> -Generierung anzeigen: potenziell sehr hoch, aber zwischengespeichert <br/> -Parameter Auswertung: niedrig <br/> -Abfrage Übersetzung: Mittel, aber zwischengespeichert <br/> -Materializer-Generierung: Mittel, aber zwischengespeichert <br/> -Datenbankabfrage Ausführung: potenziell hoch (bessere Abfragen in einigen Situationen) <br/> + Verbindung. Öffnen <br/> + Command.Executereader <br/> + DataReader. Read <br/> Objektmaterialisierung: Mittel <br/> -Identitätssuche: Mittel | -Das Laden von Metadaten: hoch, aber zwischengespeichert <br/> -Sicht Generierung: Mittel, aber zwischengespeichert <br/> -Parameter Auswertung: niedrig <br/> -Abfrage Übersetzung: Mittel, aber zwischengespeichert <br/> -Materializer-Generierung: Mittel, aber zwischengespeichert <br/> -Datenbankabfrage Ausführung: potenziell hoch (bessere Abfragen in einigen Situationen) <br/> + Verbindung. Öffnen <br/> + Command.Executereader <br/> + DataReader. Read <br/> Objektmaterialisierung: Mittel (schneller als EF5) <br/> -Identitätssuche: Mittel |
| `}`                                                                                                  | Verbindung. Schließen          | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                           | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


**Zweite Abfrage Ausführung – warm Abfrage**

| Schreiben von Code Benutzern                                                                                     | Aktion                    | Auswirkung der EF4 Leistung                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Auswirkung der EF5 Leistung                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Auswirkung der EF6 Leistung                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Kontext Erstellung          | Medium                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Medium                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Erstellung von Abfrage Ausdrücken | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | LINQ-Abfrage Ausführung      | -Das Suchen nach dem ~~metadatenlade Vorgang~~ : ~~hoch, aber zwischengespeichert~~ <br/> -Ansicht ~~Generierungs~~ Suche: ~~potenziell sehr hoch, aber zwischengespeichert~~ <br/> -Parameter Auswertung: Mittel <br/> -Abfrage ~~Übersetzungs~~ Suche: Mittel <br/> -Materializer ~~Generierungs~~ Suche: ~~Mittel, aber niedrig zwischengespeichert~~ <br/> -Datenbankabfrage Ausführung: potenziell hoch <br/> + Verbindung. Öffnen <br/> + Command.Executereader <br/> + DataReader. Read <br/> Objektmaterialisierung: Mittel <br/> -Identitätssuche: Mittel | -Das Suchen nach dem ~~metadatenlade Vorgang~~ : ~~hoch, aber zwischengespeichert~~ <br/> -Ansicht ~~Generierungs~~ Suche: ~~potenziell sehr hoch, aber zwischengespeichert~~ <br/> -Parameter Auswertung: niedrig <br/> -Abfrage ~~Übersetzungs~~ Suche: ~~Mittel, aber niedrig zwischengespeichert~~ <br/> -Materializer ~~Generierungs~~ Suche: ~~Mittel, aber niedrig zwischengespeichert~~ <br/> -Datenbankabfrage Ausführung: potenziell hoch (bessere Abfragen in einigen Situationen) <br/> + Verbindung. Öffnen <br/> + Command.Executereader <br/> + DataReader. Read <br/> Objektmaterialisierung: Mittel <br/> -Identitätssuche: Mittel | -Das Suchen nach dem ~~metadatenlade Vorgang~~ : ~~hoch, aber zwischengespeichert~~ <br/> -Ansicht ~~Generierungs~~ Suche: ~~Mittel, aber niedrig zwischengespeichert~~ <br/> -Parameter Auswertung: niedrig <br/> -Abfrage ~~Übersetzungs~~ Suche: ~~Mittel, aber niedrig zwischengespeichert~~ <br/> -Materializer ~~Generierungs~~ Suche: ~~Mittel, aber niedrig zwischengespeichert~~ <br/> -Datenbankabfrage Ausführung: potenziell hoch (bessere Abfragen in einigen Situationen) <br/> + Verbindung. Öffnen <br/> + Command.Executereader <br/> + DataReader. Read <br/> Objektmaterialisierung: Mittel (schneller als EF5) <br/> -Identitätssuche: Mittel |
| `}`                                                                                                  | Verbindung. Schließen          | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


Es gibt mehrere Möglichkeiten, die Leistungskosten sowohl für kalte als auch für warme Abfragen zu reduzieren. im folgenden Abschnitt sehen wir uns diese Informationen an. Insbesondere wird die Reduzierung der Kosten für das Laden von Modellen in kalten Abfragen mithilfe von vorab generierten Sichten untersucht, die bei der Generierung von Leistungsproblemen helfen sollen. Bei warmen Abfragen werden das Zwischenspeichern von Abfrage Plänen, keine nach Verfolgungs Abfragen und andere Optionen für die Abfrage Ausführung behandelt.

### <a name="21-what-is-view-generation"></a>2,1 Was ist die Ansichts Generierung?

Um zu verstehen, welche Sicht Generierung ist, müssen wir zuerst verstehen, was "Mapping views" ist. Zuordnungs Sichten sind ausführbare Darstellungen der in der Zuordnung für jede Entitätenmenge und Zuordnung angegebenen Transformationen. Intern haben diese Mapping-Sichten die Form von cqts (kanonische Abfrage Strukturen). Es gibt zwei Typen von Karten Sichten:

-   Abfrage Sichten: Diese stellen die Transformation dar, die erforderlich ist, um vom Datenbankschema zum konzeptionellen Modell zu wechseln.
-   Aktualisieren von Sichten: Diese stellen die Transformation dar, die erforderlich ist, um vom konzeptionellen Modell zum Datenbankschema zu wechseln.

Beachten Sie, dass das konzeptionelle Modell auf unterschiedliche Weise vom Datenbankschema abweichen kann. Beispielsweise kann eine einzelne Tabelle zum Speichern der Daten für zwei verschiedene Entitäts Typen verwendet werden. Vererbung und nicht triviale Zuordnungen spielen bei der Komplexität der Zuordnungs Sichten eine Rolle.

Das Berechnen dieser Sichten auf der Grundlage der Spezifikation der Zuordnung ist das, was wir als Ansichts Generierung bezeichnen. Die Ansichts Generierung kann entweder dynamisch erfolgen, wenn ein Modell geladen wird, oder zur Buildzeit mithilfe von "vorgenerierten Sichten"; Letztere werden in Form von Entity SQL-Anweisungen in eine C- \# oder VB-Datei serialisiert.

Wenn Sichten generiert werden, werden Sie ebenfalls überprüft. Aus Sicht der Leistung ist der Großteil der Kosten der Ansichts Generierung tatsächlich die Validierung der Sichten, die sicherstellt, dass die Verbindungen zwischen den Entitäten sinnvoll sind und über die richtige Kardinalität für alle unterstützten Vorgänge verfügen.

Wenn eine Abfrage über eine Entitätenmenge ausgeführt wird, wird die Abfrage mit der entsprechenden Abfrage Ansicht kombiniert, und das Ergebnis dieser Komposition wird durch den Plan Compiler ausgeführt, um die Darstellung der Abfrage zu erstellen, die der Sicherungs Speicher verstehen kann. Bei SQL Server ist das Endergebnis dieser Kompilierung eine T-SQL-SELECT-Anweisung. Wenn ein Update für eine Entitätenmenge zum ersten Mal ausgeführt wird, wird die Update Ansicht durch einen ähnlichen Prozess ausgeführt, um Sie in DML-Anweisungen für die Zieldatenbank umzuwandeln.

### <a name="22-factors-that-affect-view-generation-performance"></a>2,2 Faktoren, die die Leistung der Ansichts Generierung beeinflussen

Die Leistung des Ansichts Generierungs Schritts hängt nicht nur von der Größe des Modells, sondern auch davon ab, wie das Modell miteinander verbunden ist. Wenn zwei Entitäten über eine Vererbungs Kette oder eine Zuordnung verbunden sind, werden Sie als verbunden bezeichnet. Wenn zwei Tabellen über einen Fremdschlüssel verbunden sind, sind Sie ebenfalls verbunden. Wenn die Anzahl der verbundenen Entitäten und Tabellen in den Schemas zunimmt, steigt die Kosten der Ansichts Generierung.

Der Algorithmus, der zum Generieren und Validieren von Sichten verwendet wird, ist im schlimmsten Fall exponentiell, obwohl wir einige Optimierungen verwenden, um dies zu verbessern. Die wichtigsten Faktoren, die sich negativ auf die Leistung auswirken, sind folgende:

-   Modell Größe, die auf die Anzahl der Entitäten und die Menge der Zuordnungen zwischen diesen Entitäten verweist.
-   Modell Komplexität, insbesondere Vererbung für eine große Anzahl von Typen.
-   Verwenden unabhängiger Zuordnungen anstelle von Fremdschlüssel Zuordnungen.

Für kleine, einfache Modelle können die Kosten klein genug sein, um sich nicht mit vorgenerierten Sichten zu beschäftigen. Wenn die Modell Größe und-Komplexität zunehmen, stehen mehrere Optionen zur Verfügung, um die Kosten für die Anzeige Generierung und-Überprüfung zu verringern.

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a>2,3 Verwenden von vordefinierten Sichten zum Verringern der Ladezeit des Modells

Ausführliche Informationen zur Verwendung von vordefinierten Sichten auf Entity Framework 6 finden Sie unter [vorgenerierte Mapping-Sichten](xref:ef6/fundamentals/performance/pre-generated-views) .

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a>2.3.1 vorgenerierte Ansichten mit der Entity Framework Power Tools Community Edition

Mit der [Entity Framework 6 Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) können Sie Ansichten von edmx-und Code First Modellen generieren, indem Sie mit der rechten Maustaste auf die Modellklassen Datei klicken und das Menü Entity Framework verwenden, um "Sichten generieren" auszuwählen. Die Entity Framework Power Tools Community Edition funktioniert nur in Zusammenhang mit dbcontext-abgeleiteten Kontexten.

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a>2.3.2 verwenden vorgenerierter Sichten mit einem von EdmGen erstellten Modell

EdmGen ist ein Hilfsprogramm, das mit .net ausgeliefert wird und mit Entity Framework 4 und 5, aber nicht mit Entity Framework 6 funktioniert. Mit EdmGen können Sie eine Modelldatei, die Objektebene und die Ansichten von der Befehlszeile aus generieren. Bei einer der Ausgaben handelt es sich um eine Ansichts Datei in Ihrer bevorzugten Sprache, VB oder C \# . Dies ist eine Codedatei, die Entity SQL Ausschnitte für jede Entitätenmenge enthält. Um vorab generierte Sichten zu aktivieren, schließen Sie einfach die Datei in Ihr Projekt ein.

Wenn Sie Änderungen an den Schema Dateien für das Modell manuell vornehmen, müssen Sie die Ansichts Datei erneut generieren. Hierfür können Sie EdmGen mit dem Flag **/Mode: viewgene Ration** ausführen.

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a>2.3.3 verwenden vorgenerierter Sichten mit einer EDMX-Datei

Sie können EdmGen auch zum Generieren von Sichten für eine EDMX-Datei verwenden. im zuvor referenzierten MSDN-Thema wird beschrieben, wie Sie hierfür ein Präbuildereignis hinzufügen können. Dies ist jedoch kompliziert, und es gibt einige Fälle, in denen es nicht möglich ist. Im Allgemeinen ist es einfacher, eine T4-Vorlage zu verwenden, um die Sichten zu generieren, wenn das Modell in einer EDMX-Datei ist.

Der ADO.NET-Teamblog enthält einen Beitrag, der beschreibt, wie eine T4-Vorlage für die Ansichts Generierung () verwendet wird \<https://docs.microsoft.com/archive/blogs/adonet/how-to-use-a-t4-template-for-view-generation> . Dieser Beitrag enthält eine Vorlage, die heruntergeladen und dem Projekt hinzugefügt werden kann. Die Vorlage wurde für die erste Version von Entity Framework geschrieben, sodass Sie mit den neuesten Versionen von Entity Framework nicht sicher funktionieren. Sie können jedoch einen aktuellere Satz von Ansichts Generierungs Vorlagen für Entity Framework 4 und 5 aus der Visual Studio Gallery herunterladen:

-   VB.net: \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d>
-   C \# : \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d>

Wenn Sie Entity Framework 6 verwenden, können Sie die T4-Vorlagen der Sicht Generierung aus der Visual Studio Gallery unter erhalten \<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f> .

### <a name="24-reducing-the-cost-of-view-generation"></a>2,4 verringern der Kosten für die Ansichts Generierung

Durch die Verwendung von vorgenerierten Sichten werden die Kosten der Ansichts Generierung von der Modell Lade Zeit (Laufzeit) zur Entwurfszeit verschoben. Obwohl dies die Startleistung zur Laufzeit verbessert, treten die Probleme bei der Ansichts Generierung während der Entwicklung immer noch auf. Es gibt mehrere zusätzliche Tricks, die dazu beitragen können, die Kosten der Ansichts Generierung zu reduzieren, sowohl zur Kompilierzeit als auch zur Laufzeit.

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a>2.4.1 mit Fremdschlüssel Zuordnungen zum Reduzieren der Ansichts Generierungs Kosten

Wir haben eine Reihe von Fällen gesehen, in denen das Wechseln der Zuordnungen im Modell von unabhängigen Zuordnungen zu Fremdschlüssel Zuordnungen die für die Sicht Generierung aufgewendeten Zeit erheblich verbessert hat.

Um diese Verbesserung zu veranschaulichen, haben wir mithilfe von EdmGen zwei Versionen des Navision-Modells generiert. *Hinweis: eine Beschreibung des Navision-Modells finden Sie in Anhang C.* Das Navision-Modell ist für diese Übung interessant, weil es eine große Menge an Entitäten und Beziehungen zwischen Ihnen gibt.

Eine Version dieses sehr großen Modells wurde mit Fremdschlüssel Zuordnungen generiert, und die andere Version wurde mit unabhängigen Zuordnungen generiert. Dann haben wir festgenommen, wie lange es gedauert hat, die Ansichten für jedes Modell zu generieren. Entity Framework 5-Test verwendete die GenerateViews ()-Methode aus der EntityViewGenerator-Klasse, um die Sichten zu generieren, während der Entity Framework 6-Test die GenerateViews ()-Methode aus der StorageMappingItemCollection-Klasse verwendete. Dies ist auf die Code Umstrukturierung zurückzuführen, die in der-CodeBase Entity Framework 6 aufgetreten ist.

Bei Verwendung von Entity Framework 5 benötigte die Anzeige Generierung für das Modell mit Fremdschlüsseln 65 Minuten in einem Lab-Computer. Es ist unbekannt, wie lange es gedauert hätte, die Ansichten für das Modell zu generieren, die unabhängige Zuordnungen verwendet haben. Wir haben den Test über einen Monat lang ausgeführt, bevor der Computer in unserem Lab neu gestartet wurde, um monatliche Updates zu installieren.

Bei Verwendung von Entity Framework 6 benötigte die Anzeige Generierung für das Modell mit Fremdschlüsseln 28 Sekunden im gleichen Lab-Computer. Die Ansichts Generierung für das Modell, das unabhängige Zuordnungen verwendet, hat 58 Sekunden gedauert. Durch die Verbesserungen an Entity Framework 6 auf dem Code der Ansichts Generierung bedeutet dies, dass viele Projekte keine vorab generierten Sichten benötigen, um schnellere Startzeiten zu erzielen.

Es ist wichtig zu beachten, dass die vorab Generierung von Sichten in Entity Framework 4 und 5 mit EdmGen oder den Entity Framework Power Tools erfolgen kann. Entity Framework 6-Sicht Generierung kann über die Entity Framework Power Tools oder Programm gesteuert durchgeführt werden, wie in [vorab generierten Zustellungs Sichten](xref:ef6/fundamentals/performance/pre-generated-views)beschrieben.

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a>2.4.1.1 Verwenden von Fremdschlüsseln anstelle unabhängiger Zuordnungen

Wenn Sie EdmGen oder das Entity Designer in Visual Studio verwenden, erhalten Sie standardmäßig Fert, und es wird nur ein Kontrollkästchen oder ein befehlszeilenflag zum Wechseln zwischen den fschen und IAS benötigt.

Wenn Sie über ein großes Code First Modell verfügen, hat die Verwendung unabhängiger Zuordnungen dieselbe Auswirkung auf die Ansichts Generierung. Sie können diese Auswirkung vermeiden, indem Sie Fremdschlüssel Eigenschaften in die Klassen für die abhängigen Objekte einschließen. einige Entwickler werden dies jedoch in Erwägung gezogen, um Ihr Objektmodell zu beschädigen. Weitere Informationen zu diesem Betreff finden Sie unter \<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/> .

| Bei Verwendung von      | Aktion                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Entity Designer | Stellen Sie nach dem Hinzufügen einer Zuordnung zwischen zwei Entitäten sicher, dass Sie über eine referenzielle Einschränkung verfügen. Referenzielle Einschränkungen Teilen Entity Framework die Verwendung von Fremdschlüsseln anstelle unabhängiger Zuordnungen. Weitere Informationen finden Sie unter \<https://docs.microsoft.com/archive/blogs/efdesign/foreign-keys-in-the-entity-framework> . |
| EDMGen          | Wenn Sie die Dateien mithilfe von EdmGen aus der Datenbank generieren, werden die Fremdschlüssel berücksichtigt und dem Modell als solche hinzugefügt. Weitere Informationen zu den verschiedenen Optionen, die von EdmGen angeboten werden, finden Sie unter [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx) .                           |
| Code First      | Weitere Informationen zum Einschließen von Fremdschlüssel Eigenschaften für abhängige Objekte bei Verwendung von Code First finden Sie im Abschnitt "Beziehungs Konvention" im Thema [Code First Konventionen](xref:ef6/modeling/code-first/conventions/built-in) .                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a>2.4.2 Verschieben des Modells in eine separate Assembly

Wenn Ihr Modell direkt in das Projekt Ihrer Anwendung eingefügt wird und Sie Sichten über ein Präbuildereignis oder eine T4-Vorlage generieren, werden die Ansichts Generierung und-Validierung immer dann durchgeführt, wenn das Projekt neu erstellt wird, auch wenn das Modell nicht geändert wurde. Wenn Sie das Modell in eine separate Assembly verschieben und aus dem Projekt Ihrer Anwendung darauf verweisen, können Sie andere Änderungen an Ihrer Anwendung vornehmen, ohne das Projekt neu erstellen zu müssen, das das Modell enthält.

*Hinweis:*   Wenn Sie das Modell in separate Assemblys verschieben, denken Sie daran, die Verbindungs Zeichenfolgen für das Modell in die Anwendungs Konfigurationsdatei des Client Projekts zu kopieren.

#### <a name="243-disable-validation-of-an-edmx-based-model"></a>2.4.3 Deaktivieren der Überprüfung eines edmx-basierten Modells

Edmx-Modelle werden zur Kompilierzeit überprüft, auch wenn das Modell unverändert ist. Wenn das Modell bereits überprüft wurde, können Sie die Überprüfung zur Kompilierzeit unterdrücken, indem Sie im Eigenschaften Fenster die Eigenschaft "auf Build überprüfen" auf "false" festlegen. Wenn Sie die Zuordnung oder das Modell ändern, können Sie die Überprüfung vorübergehend erneut aktivieren, um die Änderungen zu überprüfen.

Beachten Sie, dass die Entity Framework Designer für Entity Framework 6 Leistungsverbesserungen vorgenommen wurden und die Kosten für "überprüfen bei Build" wesentlich niedriger sind als in früheren Versionen des Designers.

## <a name="3-caching-in-the-entity-framework"></a>3 Caching in der Entity Framework

In Entity Framework sind die folgenden Formen der Zwischenspeicherung integriert:

1.  Objekt Zwischenspeicherung – der in eine ObjectContext-Instanz integrierte objectstatus Manager behält den Arbeitsspeicher der Objekte bei, die mit dieser Instanz abgerufen wurden. Dies wird auch als Cache der ersten Ebene bezeichnet.
2.  Zwischenspeichern von Abfrage Plänen: wieder verwenden des generierten Speicher Befehls, wenn eine Abfrage mehrmals ausgeführt wird.
3.  Metadatencaching: Freigeben der Metadaten für ein Modell über verschiedene Verbindungen zum gleichen Modell.

Neben den von EF standardmäßig bereitgestellten Caches kann eine besondere Art von ADO.NET-Datenanbieter, der als Wrapping Anbieter bezeichnet wird, auch verwendet werden, um Entity Framework mit einem Cache für die Ergebnisse zu erweitern, die aus der Datenbank abgerufen werden, auch als Caching der zweiten Ebene bezeichnet.

### <a name="31-object-caching"></a>3,1 Zwischenspeichern von Objekten

Wenn eine Entität in den Ergebnissen einer Abfrage zurückgegeben wird, kurz bevor EF Sie materialisiert, prüft der ObjectContext standardmäßig, ob eine Entität mit demselben Schlüssel bereits in den objectstatus Ager geladen wurde. Wenn bereits eine Entität mit denselben Schlüsseln vorhanden ist, wird Sie von EF in die Ergebnisse der Abfrage eingeschlossen. Obwohl EF immer noch die Abfrage für die Datenbank ausgibt, kann dieses Verhalten einen Großteil der Kosten für eine mehrfache Materialisierung der Entität umgehen.

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a>3.1.1 Abrufen von Entitäten aus dem Objekt Cache mithilfe von dbcontext Find

Anders als bei einer regulären Abfrage führt die Find-Methode in dbset (APIs, die zum ersten Mal in EF 4,1 enthalten sind) eine Suche im Speicher aus, bevor Sie die Abfrage für die Datenbank ausgibt. Es ist wichtig zu beachten, dass zwei unterschiedliche ObjectContext-Instanzen zwei unterschiedliche objectstatus Manager-Instanzen aufweisen, was bedeutet, dass Sie über separate Objekt Caches verfügen.

Find verwendet den Primärschlüssel Wert, um eine Entität zu suchen, die vom Kontext nachverfolgt wird. Wenn sich die Entität nicht im Kontext befindet, wird eine Abfrage ausgeführt und mit der Datenbank ausgewertet, und NULL wird zurückgegeben, wenn die Entität nicht im Kontext oder in der Datenbank gefunden wurde. Beachten Sie, dass Find auch Entitäten zurückgibt, die dem Kontext hinzugefügt wurden, aber noch nicht in der Datenbank gespeichert wurden.

Bei der Verwendung von Find wird eine Leistungs Überlegung berücksichtigt. Bei Aufrufen dieser Methode wird standardmäßig eine Überprüfung des Objekt Caches durchführt, um Änderungen zu erkennen, für die noch ein Commit in der Datenbank aussteht. Dieser Prozess kann sehr kostspielig sein, wenn eine große Anzahl von Objekten im Objekt Cache oder in einem großen Objekt Diagramm dem Objekt Cache hinzugefügt wird. es kann jedoch auch deaktiviert werden. In bestimmten Fällen können Sie sich über eine Größenordnung von Unterschieden beim Aufrufen der Find-Methode mit dem Deaktivieren von Änderungen bei der automatischen Erkennung informieren. Eine zweite Größenordnung wird jedoch wahrgenommen, wenn sich das Objekt tatsächlich im Cache befindet, und wenn das Objekt aus der Datenbank abgerufen werden muss. Hier sehen Sie ein Beispiel Diagramm mit Messungen, die mithilfe einiger unserer Mikro Benchmarks (in Millisekunden ausgedrückt) mit einer Auslastung von 5000 Entitäten durchgeführt wurden:

![Logarithmische Skalierung in .NET 4,5](~/ef6/media/net45logscale.png ".NET 4,5-logarithmische Skalierung")

Beispiel für Suche mit deaktiviertem automatischen Erkennen von Änderungen:

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

Bei der Verwendung der Find-Methode müssen Sie Folgendes beachten:

1.  Wenn sich das Objekt nicht im Cache befindet, werden die Vorteile von Find negiert, aber die Syntax ist noch einfacher als eine Abfrage nach Schlüssel.
2.  Wenn die Option zum automatischen Erkennen von Änderungen aktiviert ist, können die Kosten der Find-Methode je nach Komplexität des Modells und der Menge der Entitäten im Objekt Cache in einer Größenordnung zunehmen.

Beachten Sie außerdem, dass Find nur die Entität zurückgibt, nach der Sie suchen, und die zugehörigen Entitäten nicht automatisch lädt, wenn Sie sich nicht bereits im Objekt Cache befinden. Wenn Sie zugehörige Entitäten abrufen müssen, können Sie eine Abfrage nach Schlüssel mit Eager Loading verwenden. Weitere Informationen finden Sie unter **8,1 Lazy Load im Vergleich zum unverwollten laden**.

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a>3.1.2 Leistungsprobleme, wenn der Objekt Cache über viele Entitäten verfügt

Der Objekt Cache trägt dazu bei, die allgemeine Reaktionsfähigkeit von Entity Framework zu erhöhen. Wenn jedoch eine große Menge an Entitäten in den Objekt Cache geladen wird, kann sich dies auf bestimmte Vorgänge wie hinzufügen, entfernen, suchen, Eintrags, SaveChanges usw. auswirken. Insbesondere Vorgänge, die einen aufrufungserkennungs-aufruder DetectChanges auslöst, werden von sehr großen Objekt Caches negativ beeinflusst. DetectChanges synchronisiert das Objekt Diagramm mit dem Objekt Zustands-Manager, und die Leistung wird direkt durch die Größe des Objekt Diagramms bestimmt. Weitere Informationen zu DetectChanges finden Sie unter nach [Verfolgen von Änderungen in poco-Entitäten](https://msdn.microsoft.com/library/dd456848.aspx).

Wenn Sie Entity Framework 6 verwenden, können Entwickler AddRange und RemoveRange direkt in einem dbset aufrufen, anstatt eine Auflistung zu durchlaufen und "Add Once" pro Instanz aufrufen zu müssen. Der Vorteil der Verwendung der Bereichs Methoden besteht darin, dass die Kosten von "DetectChanges" nur einmal für den gesamten entitätensatz gezahlt werden, anstatt einmal pro hinzugefügter Entität.

### <a name="32-query-plan-caching"></a>3,2 Zwischenspeichern von Abfrage Plänen

Wenn eine Abfrage zum ersten Mal ausgeführt wird, durchläuft Sie den internen Plan Compiler, um die konzeptionelle Abfrage in den Store-Befehl zu übersetzen (z. b. die T-SQL-Anweisung, die ausgeführt wird, wenn Sie für SQL Server ausgeführt wird).Wenn das Zwischenspeichern von Abfrage Plänen aktiviert ist, wird der Speicher Befehl beim nächsten Ausführen der Abfrage direkt aus dem Abfrageplan Cache für die Ausführung abgerufen, wobei der Plan Compiler umgangen wird.

Der Abfrageplan Cache wird über ObjectContext-Instanzen innerhalb derselben AppDomain freigegeben. Sie müssen sich nicht auf einer ObjectContext-Instanz befinden, um von der Zwischenspeicherung von Abfrage Plänen zu profitieren.

#### <a name="321-some-notes-about-query-plan-caching"></a>3.2.1 einige Hinweise zum Zwischenspeichern von Abfrage Plänen

-   Der Abfrageplan Cache wird für alle Abfrage Typen freigegeben: Entity SQL, LINQ to Entities und CompiledQuery-Objekten.
-   Standardmäßig ist das Zwischenspeichern von Abfrage Plänen für Entity SQL Abfragen aktiviert, unabhängig davon, ob diese über einen EntityCommand oder durch eine ObjectQuery ausgeführt werden. Sie ist auch standardmäßig für LINQ to Entities Abfragen in Entity Framework unter .NET 4,5 und Entity Framework 6 aktiviert.
    -   Das Zwischenspeichern von Abfrage Plänen kann deaktiviert werden, indem die EnablePlanCaching-Eigenschaft (für EntityCommand oder ObjectQuery) auf false festgelegt wird. Beispiel:
``` csharp
                    var query = from customer in context.Customer
                                where customer.CustomerId == id
                                select new
                                {
                                    customer.CustomerId,
                                    customer.Name
                                };
                    ObjectQuery oQuery = query as ObjectQuery;
                    oQuery.EnablePlanCaching = false;
```
-   Bei parametrisierten Abfragen trifft das Ändern des Parameter Werts weiterhin auf die zwischengespeicherte Abfrage. Beim Ändern der Facetten eines Parameters (z. b. Größe, Genauigkeit oder Skala) wird jedoch ein anderer Eintrag im Cache angezeigt.
-   Wenn Sie Entity SQL verwenden, ist die Abfrage Zeichenfolge Teil des Schlüssels. Wenn Sie die Abfrage überhaupt ändern, führt dies zu unterschiedlichen Cache Einträgen, auch wenn die Abfragen funktionell gleichwertig sind. Dies schließt Änderungen an der Groß-/Kleinschreibung oder Leerräume ein.
-   Wenn Sie LINQ verwenden, wird die Abfrage verarbeitet, um einen Teil des Schlüssels zu generieren. Wenn Sie den LINQ-Ausdruck ändern, wird daher ein anderer Schlüssel generiert.
-   Es können weitere technische Einschränkungen gelten. Weitere Informationen finden Sie unter automatisch kompilierte Abfragen.

#### <a name="322-cache-eviction-algorithm"></a>3.2.2 Cache Entfernungs Algorithmus

Wenn Sie verstehen, wie der interne Algorithmus funktioniert, können Sie herausfinden, wann Sie das Zwischenspeichern von Abfrage Plänen aktivieren oder deaktivieren. Der Bereinigungs Algorithmus lautet wie folgt:

1.  Wenn der Cache eine festgelegte Anzahl von Einträgen (800) enthält, wird ein Timer gestartet, der in regelmäßigen Abständen (einmal pro Minute) den Cache durch geht.
2.  Während des Cache Vorgangs werden Einträge aus dem Cache auf einem lfru-Basis (am wenigsten häufig – zuletzt verwendet) entfernt. Bei diesem Algorithmus wird sowohl die Treffer Anzahl als auch das Alter berücksichtigt, wenn Sie entscheiden, welche Einträge ausworfen werden.
3.  Am Ende jedes Cache-Sweep enthält der Cache wieder 800 Einträge.

Alle Cache Einträge werden gleichermaßen behandelt, wenn bestimmt wird, welche Einträge entfernt werden sollen. Dies bedeutet, dass der Speicher Befehl für eine CompiledQuery die gleiche Möglichkeit hat, wie der Speicher Befehl für eine Entity SQL Abfrage zu entfernen.

Beachten Sie, dass der Cache Entfernungs Zeit Geber in gestartet wird, wenn 800 Entitäten im Cache vorhanden sind. der Cache wird jedoch nur 60 Sekunden nach dem Start dieses Timers überschwemmt. Dies bedeutet, dass es bis zu 60 Sekunden dauern kann, bis der Cache sehr groß ist.

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a>3.2.3 Testmetriken veranschaulichen der Zwischenspeicherung von Abfrage Plänen

Um die Auswirkung der Zwischenspeicherung von Abfrage Plänen auf die Leistung Ihrer Anwendung zu veranschaulichen, haben wir einen Test durchgeführt, bei dem wir eine Reihe von Entity SQL Abfragen für das Navision-Modell ausgeführt haben. Eine Beschreibung des Navision-Modells und der ausgeführten Abfrage Typen finden Sie im Anhang. In diesem Test durchlaufen wir zuerst die Liste der Abfragen und führen diese einmal aus, um Sie dem Cache hinzuzufügen (wenn das Caching aktiviert ist). Dieser Schritt ist nicht geplant. Als nächstes wird der Haupt Thread für mehr als 60 Sekunden in den Standbymodus versetzt, um das Zwischenspeichern von Caches zuzulassen. zum Schluss durchlaufen wir die Liste ein zweites Mal, um die zwischengespeicherten Abfragen auszuführen. Außerdem wird der SQL Server Plancache geleert, bevor jeder Satz von Abfragen ausgeführt wird, sodass die Zeiten, die der Abfrageplan Cache liefert, genau wiedergegeben werden.

##### <a name="3231-test-results"></a>3.2.3.1 Testergebnisse

| Test                                                                   | EF5 kein Cache | EF5 zwischengespeichert | EF6 kein Cache | EF6 zwischengespeichert |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| Auflisten aller 18723-Abfragen                                          | 124          | 125,4      | 124,3        | 125,3      |
| Vermeiden von Sweep (nur die ersten 800-Abfragen, unabhängig von der Komplexität)  | 41,7         | 5.5        | 40,5         | 5.4        |
| Nur die aggregatingsubsummen-Abfragen (178 Total, wodurch das Sweep vermieden wird) | 39,5         | 4,5        | 38,1         | 4.6        |

*Alle Uhrzeiten in Sekunden.*

Moralisch: beim Ausführen von vielen unterschiedlichen Abfragen (z. b. dynamisch erstellte Abfragen) ist das Caching nicht hilfreich, und die daraus resultierende Speicherung des Caches kann die Abfragen, die am meisten von der Zwischenspeicherung des Plans profitieren, von der eigentlichen Verwendung der Abfrage behalten.

Die aggregatingsubsummen-Abfragen sind die komplexesten der Abfragen, die wir mit getestet haben. Je komplexer die Abfrage ist, desto komplexer wird der Zwischenspeicher des Abfrage Plans.

Da es sich bei einer CompiledQuery tatsächlich um eine LINQ-Abfrage handelt, bei der der Plan zwischengespeichert ist, sollte der Vergleich einer CompiledQuery und der entsprechenden Entity SQL Abfrage ähnliche Ergebnisse aufweisen. Wenn eine APP viele dynamische Entity SQL Abfragen enthält, führt das Auffüllen des Caches mit Abfragen auch dazu, dass compiledqueries beim leeren aus dem Cache "deaktiviert" wird. In diesem Szenario kann die Leistung verbessert werden, indem das Zwischenspeichern für die dynamischen Abfragen deaktiviert wird, um die compiledqueries zu priorisieren. Noch besser wäre es, die APP neu zu schreiben, um parametrisierte Abfragen anstelle dynamischer Abfragen zu verwenden.

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a>3,3 Verwenden von CompiledQuery zum Verbessern der Leistung mit LINQ-Abfragen

Unsere Tests deuten darauf hin, dass die Verwendung von CompiledQuery bei automatisch kompilierten LINQ-Abfragen einen Vorteil von 7% haben kann. Dies bedeutet, dass Sie 7% weniger Zeit für das Ausführen von Code aus dem Entity Framework Stapel aufwenden müssen. Dies bedeutet nicht, dass Ihre Anwendung 7% schneller ist. Im Allgemeinen sind die Kosten für das Schreiben und warten von CompiledQuery-Objekten in EF 5,0 im Vergleich zu den Vorteilen möglicherweise nicht so schwierig. Der Meilenstein kann variieren. führen Sie daher diese Option aus, wenn das Projekt den zusätzlichen Push erfordert. Beachten Sie, dass compiledqueries nur mit von ObjectContext abgeleiteten Modellen kompatibel sind und nicht mit von dbcontext abgeleiteten Modellen kompatibel ist.

Weitere Informationen zum Erstellen und Aufrufen einer CompiledQuery finden Sie unter [kompilierte Abfragen (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx).

Bei der Verwendung einer CompiledQuery müssen Sie zwei Aspekte beachten, nämlich die Anforderung, statische Instanzen zu verwenden, und die Probleme, die Sie mit der Zusammensetz barkeit haben. Im folgenden finden Sie eine ausführliche Erläuterung dieser beiden Überlegungen.

#### <a name="331-use-static-compiledquery-instances"></a>3.3.1 Verwenden statischer CompiledQuery-Instanzen

Da das Kompilieren einer LINQ-Abfrage ein zeitaufwändiger Prozess ist, möchten wir Sie nicht jedes Mal durchführen, wenn Daten aus der Datenbank abgerufen werden müssen. Mit CompiledQuery-Instanzen können Sie eine einmalige Kompilierung ausführen und mehrmals ausführen, aber Sie müssen darauf achten, dass Sie jedes Mal dieselbe CompiledQuery-Instanz wieder verwenden, anstatt Sie immer wieder zu kompilieren. Die Verwendung statischer Member zum Speichern der CompiledQuery-Instanzen wird benötigt. Andernfalls wird kein Vorteil angezeigt.

Nehmen Sie beispielsweise an, dass Ihre Seite den folgenden Methoden Text enthält, mit dem die Produkte für die ausgewählte Kategorie angezeigt werden:

``` csharp
    // Warning: this is the wrong way of using CompiledQuery
    using (NorthwindEntities context = new NorthwindEntities())
    {
        string selectedCategory = this.categoriesList.SelectedValue;

        var productsForCategory = CompiledQuery.Compile<NorthwindEntities, string, IQueryable<Product>>(
            (NorthwindEntities nwnd, string category) =>
                nwnd.Products.Where(p => p.Category.CategoryName == category)
        );

        this.productsGrid.DataSource = productsForCategory.Invoke(context, selectedCategory).ToList();
        this.productsGrid.DataBind();
    }

    this.productsGrid.Visible = true;
```

In diesem Fall erstellen Sie jedes Mal, wenn die-Methode aufgerufen wird, eine neue CompiledQuery-Instanz. Anstatt die Leistungsvorteile durch Abrufen des Speicher Befehls aus dem Abfrageplan Cache zu erkennen, durchläuft CompiledQuery den Plan Compiler jedes Mal, wenn eine neue Instanz erstellt wird. Tatsächlich wird der Abfrageplan Cache durch einen neuen CompiledQuery-Eintrag bei jedem Aufruf der-Methode verschmutzt.

Stattdessen möchten Sie eine statische Instanz der kompilierten Abfrage erstellen, sodass Sie bei jedem Aufruf der Methode dieselbe kompilierte Abfrage aufrufen. Eine Möglichkeit hierfür besteht darin, die CompiledQuery-Instanz als Member des Objekt Kontexts hinzuzufügen.Sie können dann etwas sauberer machen, indem Sie auf die CompiledQuery über eine Hilfsmethode zugreifen:

``` csharp
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IEnumerable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
            );

        public IEnumerable<Product> GetProductsForCategory(string categoryName)
        {
            return productsForCategoryCQ.Invoke(this, categoryName).ToList();
        }
```

Diese Hilfsmethode wird wie folgt aufgerufen:

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a>3.3.2 Komposition über eine CompiledQuery

Die Möglichkeit zum Verfassen von LINQ-Abfragen ist äußerst nützlich. Dazu rufen Sie einfach eine Methode nach dem iquerable-Element auf, z. b. *Skip ()* oder *count ()*. Dadurch wird jedoch im Grunde ein neues iquerable-Objekt zurückgegeben. Es gibt zwar nichts, was technisch nicht durch das Verfassen einer CompiledQuery zu tun hat, dies führt jedoch dazu, dass ein neues iquerable-Objekt generiert wird, das den Plan Compiler erneut durchlaufen muss.

Einige Komponenten verwenden zusammengesetzte iquerable-Objekte, um erweiterte Funktionen zu ermöglichen. Beispielsweise ASP. GridView von NET kann an ein iquerable-Objekt über die SelectMethod-Eigenschaft gebunden werden. Die GridView wird dann über dieses iquerable-Objekt erstellt, um das Sortieren und Paging über das Datenmodell zuzulassen. Wie Sie sehen, würde die Verwendung einer CompiledQuery für die GridView nicht die kompilierte Abfrage erreichen, sondern eine neue automatisch kompilierte Abfrage generieren.

Ein Ort, an dem Sie möglicherweise darauf stoßen, ist das Hinzufügen progressiver Filter zu einer Abfrage. Angenommen, Sie haben eine Kundenseite mit mehreren Dropdown Listen für optionale Filter (z. b. Country und OrdersCount). Sie können diese Filter über die iquervable-Ergebnisse einer CompiledQuery-Abfrage verfassen. Dies führt jedoch dazu, dass die neue Abfrage bei jeder Ausführung den Plan Compiler durchläuft.

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployee();

        if (this.orderCountFilterList.SelectedItem.Value != defaultFilterText)
        {
            int orderCount = int.Parse(orderCountFilterList.SelectedValue);
            myCustomers = myCustomers.Where(c => c.Orders.Count > orderCount);
        }

        if (this.countryFilterList.SelectedItem.Value != defaultFilterText)
        {
            myCustomers = myCustomers.Where(c => c.Address.Country == countryFilterList.SelectedValue);
        }

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 Um diese Neukompilierung zu vermeiden, können Sie CompiledQuery umschreiben, um die möglichen Filter zu berücksichtigen:

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

Dies wird in der Benutzeroberfläche wie folgt aufgerufen:

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        int? countFilter = (this.orderCountFilterList.SelectedIndex == 0) ?
            (int?)null :
            int.Parse(this.orderCountFilterList.SelectedValue);

        string countryFilter = (this.countryFilterList.SelectedIndex == 0) ?
            null :
            this.countryFilterList.SelectedValue;

        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployeeWithFilters(
                countFilter, countryFilter);

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 Ein Kompromiss besteht darin, dass der generierte Speicher Befehl immer die Filter mit den Null-Überprüfungen enthält. diese müssen jedoch für die Optimierung des Datenbankservers recht einfach sein:

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a>3,4 Zwischenspeichern von Metadaten

Der Entity Framework unterstützt auch das Zwischenspeichern von Metadaten. Dabei handelt es sich im Wesentlichen um das Zwischenspeichern von Typinformationen und Typdaten für die Daten Bank Zuordnung über verschiedene Verbindungen zum gleichen Modell. Der Metadatencache ist pro AppDomain eindeutig.

#### <a name="341-metadata-caching-algorithm"></a>3.4.1 Metadata Caching-Algorithmus

1.  Metadateninformationen für ein Modell werden in einer ItemCollection für jede EntityConnection gespeichert.
    -   Als neben Hinweis gibt es verschiedene ItemCollection-Objekte für verschiedene Teile des Modells. Storeitemcollections enthält z. b. die Informationen zum Datenbankmodell. ObjectItemCollection enthält Informationen zum Datenmodell. EdmItemCollection enthält Informationen über das konzeptionelle Modell.

2.  Wenn zwei Verbindungen dieselbe Verbindungs Zeichenfolge verwenden, verwenden Sie dieselbe ItemCollection-Instanz.
3.  Funktionale Äquivalente, aber texell unterschiedliche Verbindungs Zeichenfolgen können zu unterschiedlichen Metadatencaches führen. Verbindungs Zeichenfolgen werden mit Token versehen, sodass das Ändern der Reihenfolge der Token zu freigegebenen Metadaten führen sollte. Zwei Verbindungs Zeichenfolgen, die funktionstüchtig erscheinen, werden jedoch möglicherweise nach der Tokenisierung nicht als identisch ausgewertet.
4.  Die ItemCollection wird regelmäßig zur Verwendung überprüft. Wenn festgestellt wird, dass in jüngster Zeit kein Zugriff auf einen Arbeitsbereich erfolgt ist, wird er beim nächsten Cache Sweep für die Bereinigung gekennzeichnet.
5.  Die bloße Erstellung einer EntityConnection bewirkt, dass ein Metadatencache erstellt wird (obwohl die Element Auflistungen darin erst nach dem Öffnen der Verbindung initialisiert werden). Dieser Arbeitsbereich verbleibt im Arbeitsspeicher, bis der Cache Algorithmus festlegt, dass er nicht verwendet wird.

Das Customer Advisory Team hat einen Blogbeitrag geschrieben, in dem beschrieben wird, wie Sie einen Verweis auf eine ItemCollection enthalten, um die "Veraltung" bei der Verwendung von großen Modellen zu vermeiden: \<https://docs.microsoft.com/archive/blogs/appfabriccat/holding-a-reference-to-the-ef-metadataworkspace-for-wcf-services> .

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a>3.4.2 Beziehung zwischen dem Zwischenspeichern von Metadaten und dem Zwischenspeichern von Abfrage Plänen

Die Abfrageplan Cache-Instanz befindet sich in der ItemCollection des MetadataWorkspace der Speichertypen. Dies bedeutet, dass zwischengespeicherte Speicher Befehle für Abfragen für jeden Kontext verwendet werden, der mit einem bestimmten MetadataWorkspace instanziiert wird. Dies bedeutet auch, dass bei zwei Verbindungs Zeichenfolgen, die sich geringfügig unterscheiden und nach dem tokenisierungsplan nicht abgeglichen werden, verschiedene Abfrageplan Cache Instanzen vorhanden sind.

### <a name="35-results-caching"></a>3,5 Ergebnisse Caching

Mit dem Zwischenspeichern von Ergebnissen (auch als "Caching auf zweiter Ebene" bezeichnet) behalten Sie die Ergebnisse der Abfragen in einem lokalen Cache bei. Wenn Sie eine Abfrage ausgeben, sehen Sie zunächst, ob die Ergebnisse lokal verfügbar sind, bevor Sie den Speicher Abfragen. Während das Zwischenspeichern von Ergebnissen nicht direkt von Entity Framework unterstützt wird, ist es möglich, einen Cache der zweiten Ebene mithilfe eines Wrapping Anbieters hinzuzufügen. Ein Beispiel für einen Wrapping Anbieter mit einem Cache der zweiten Ebene ist der [auf NCache basierende Cache für Entity Framework Sekunde auf zweiter Ebene auf der zweiten](https://www.alachisoft.com/ncache/entity-framework.html)Ebene.

Diese Implementierung des zwischen Speicherns auf zweiter Ebene ist eine eingefügte Funktionalität, die ausgeführt wird, nachdem der LINQ-Ausdruck ausgewertet (und funcletisiert) wurde und der Abfrage Ausführungsplan berechnet oder aus dem Cache der ersten Ebene abgerufen wurde. Der Cache der zweiten Ebene speichert dann nur die unformatierten Daten Bank Ergebnisse, sodass die Materialisierungs Pipeline nach wie vor ausgeführt wird.

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a>3.5.1 zusätzliche Verweise für das Zwischenspeichern von Ergebnissen mit dem Wrapping Anbieter

-   Julie Lerman hat im MSDN-Artikel "Caching der zweiten Ebene in Entity Framework und Windows Azure" das Aktualisieren des Beispiel-Wrapping Anbieters für die Verwendung von Windows Server AppFabric Caching erläutert: [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)
-   Wenn Sie mit Entity Framework 5 arbeiten, enthält der Teamblog einen Beitrag, in dem beschrieben wird, wie Sie mit dem Caching-Anbieter für Entity Framework 5: Ausführen \<https://docs.microsoft.com/archive/blogs/adonet/ef-caching-with-jarek-kowalskis-provider> . Außerdem enthält Sie eine T4-Vorlage, mit der Sie das Zwischenspeichern der zweiten Ebene zu Ihrem Projekt automatisieren können.

## <a name="4-autocompiled-queries"></a>4 automatisch kompilierte Abfragen

Wenn eine Abfrage für eine Datenbank mithilfe von Entity Framework ausgegeben wird, muss Sie eine Reihe von Schritten durchlaufen, bevor die Ergebnisse tatsächlich materialisiert werden. ein solcher Schritt ist die Abfrage Kompilierung. Entity SQL-Abfragen haben bekanntermaßen eine gute Leistung, da Sie automatisch zwischengespeichert werden. Wenn Sie also die gleiche Abfrage ausführen, können Sie den Plan Compiler überspringen und stattdessen den zwischengespeicherten Plan verwenden.

In Entity Framework 5 wurde auch das automatische Zwischenspeichern für LINQ to Entities Abfragen eingeführt. In früheren Editionen von Entity Framework das Erstellen einer CompiledQuery zum beschleunigen ihrer Leistung eine gängige Vorgehensweise, da dadurch die LINQ to Entities Abfrage zwischengespeichert werden konnte. Da das Caching jetzt ohne Verwendung von CompiledQuery automatisch erfolgt, wird dieses Feature als "automatisch kompilierte Abfragen" bezeichnet. Weitere Informationen über den Abfrageplan Cache und seine Mechanismen finden Sie unter Zwischenspeichern von Abfrage Plänen.

Entity Framework erkennt, wenn eine Abfrage erneut kompiliert werden muss. Dies geschieht auch, wenn die Abfrage aufgerufen wird, auch wenn Sie zuvor kompiliert wurde. Folgende häufige Bedingungen bewirken, dass die Abfrage neu kompiliert wird:

-   Ändern der MergeOption, die der Abfrage zugeordnet ist. Die zwischengespeicherte Abfrage wird nicht verwendet, sondern der Plan Compiler wird erneut ausgeführt, und der neu erstellte Plan wird zwischengespeichert.
-   Ändern des Werts von ContextOptions. usecsharpnullcomparisonbehavior. Sie erhalten die gleichen Auswirkungen wie das Ändern der MergeOption.

Andere Bedingungen können verhindern, dass Ihre Abfrage den Cache verwendet. Typische Beispiele:

-   Verwenden von IEnumerable &lt; T &gt; . Enthält &lt; &gt; (T-Wert).
-   Verwenden von Funktionen, mit denen Abfragen mit Konstanten erzeugt werden.
-   Verwenden der Eigenschaften eines nicht zugeordneten Objekts.
-   Verknüpfen der Abfrage mit einer anderen Abfrage, für die eine erneute Kompilierung erforderlich ist.

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a>4,1 Verwenden von IEnumerable &lt; T &gt; . Enthält &lt; t &gt; (t-Wert)

In Entity Framework werden keine Abfragen zwischengespeichert, die IEnumerable &lt; T aufrufen &gt; . Enthält &lt; t &gt; (t-Wert) für eine Auflistung im Arbeitsspeicher, da die Werte der Auflistung als flüchtig angesehen werden. Die folgende Beispiel Abfrage wird nicht zwischengespeichert, sodass Sie immer vom Plan Compiler verarbeitet wird:

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var query = context.MyEntities
                    .Where(entity => ids.Contains(entity.Id));

    var results = query.ToList();
    ...
}
```

Beachten Sie, dass die Größe des IEnumerable-Element, in dem enthalten ist, bestimmt, wie schnell oder wie langsam die Abfrage kompiliert wird. Die Leistung kann erheblich beeinträchtigt werden, wenn große Auflistungen verwendet werden, wie im obigen Beispiel gezeigt.

Entity Framework 6 enthält Optimierungen, wie IEnumerable &lt; T &gt; . Enthält &lt; t &gt; (t-Wert) funktioniert, wenn Abfragen ausgeführt werden. Der generierte SQL-Code ist viel schneller zu erzeugen und lesbarer zu machen, und in den meisten Fällen wird er auch schneller auf dem Server ausgeführt.

### <a name="42-using-functions-that-produce-queries-with-constants"></a>4,2 Verwenden von Funktionen, die Abfragen mit Konstanten entwickeln

Die LINQ-Operatoren Skip (), Take (), enthält () und defautifempty () erstellen keine SQL-Abfragen mit Parametern, sondern legen die an Sie übergebenen Werte als Konstanten fest. Aus diesem Grund werden Abfragen, die andernfalls identisch sein könnten, den Abfrageplan Cache sowohl auf dem EF-Stapel als auch auf dem Datenbankserver verschmutzen und werden nicht wieder hergestellt, es sei denn, in einer nachfolgenden Abfrage Ausführung werden dieselben Konstanten verwendet. Beispiel:

``` csharp
var id = 10;
...
using (var context = new MyContext())
{
    var query = context.MyEntities.Select(entity => entity.Id).Contains(id);

    var results = query.ToList();
    ...
}
```

In diesem Beispiel wird jedes Mal, wenn diese Abfrage mit einem anderen Wert für ID ausgeführt wird, die Abfrage in einen neuen Plan kompiliert.

Achten Sie insbesondere auf die Verwendung von Skip und Take beim Paging. In EF6 verfügen diese Methoden über eine Lambda Überladung, die den zwischengespeicherten Abfrageplan effektiv wiederverwendbar macht, da EF an diese Methoden übergebenen Variablen erfassen und in SQLPARAMETERS übersetzen kann. Dies trägt auch dazu bei, den Cache zu bereinigen, da andernfalls jede Abfrage mit einer anderen Konstante für Skip und Take einen eigenen Cache Eintrag für den Abfrageplan erhalten würde.

Sehen Sie sich den folgenden Code an, der nicht optimal ist, aber nur für die Veranschaulichung dieser Abfrage Klasse vorgesehen ist:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

Eine schnellere Version desselben Codes umfasst das Aufrufen von Skip mit einem Lambda-Ausdruck:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

Der zweite Ausschnitt kann bis zu 11% schneller ausgeführt werden, da derselbe Abfrageplan bei jedem Ausführen der Abfrage verwendet wird. Dadurch wird die CPU-Zeit gespart, und der Abfragecache wird vermieden. Da der zu über springende Parameter in einem Abschluss liegt, könnte der Code nun wie folgt aussehen:

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a>4,3 Verwenden der Eigenschaften eines nicht zugeordneten Objekts

Wenn eine Abfrage die Eigenschaften eines nicht zugeordneten Objekt Typs als Parameter verwendet, wird die Abfrage nicht zwischengespeichert. Beispiel:

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();

    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myObject.MyProperty)
                select entity;

   var results = query.ToList();
    ...
}
```

Nehmen Sie in diesem Beispiel an, dass die Klasse nonmappedtype nicht Teil des Entitäts Modells ist. Diese Abfrage kann problemlos so geändert werden, dass kein nicht zugeordneter Typ verwendet wird, sondern stattdessen eine lokale Variable als Parameter für die Abfrage verwendet wird:

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();
    var myValue = myObject.MyProperty;
    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myValue)
                select entity;

    var results = query.ToList();
    ...
}
```

In diesem Fall kann die Abfrage zwischengespeichert werden und profitiert vom Abfrageplan Cache.

### <a name="44-linking-to-queries-that-require-recompiling"></a>4,4 verknüpfen mit Abfragen, die eine erneute Kompilierung erfordern

Wenn Sie über eine zweite Abfrage verfügen, die auf einer Abfrage basiert, die erneut kompiliert werden muss, wird das gleiche Beispiel wie oben beschrieben. Es folgt ein Beispiel zur Veranschaulichung dieses Szenarios:

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var firstQuery = from entity in context.MyEntities
                        where ids.Contains(entity.Id)
                        select entity;

    var secondQuery = from entity in context.MyEntities
                        where firstQuery.Any(otherEntity => otherEntity.Id == entity.Id)
                        select entity;

    var results = secondQuery.ToList();
    ...
}
```

Das Beispiel ist generisch, aber es veranschaulicht, wie die Verknüpfung mit firstquery bewirkt, dass secondquery nicht zwischengespeichert wird. Wenn firstquery keine Abfrage war, die eine erneute Kompilierung erfordert, wurde secondquery zwischengespeichert.

## <a name="5-notracking-queries"></a>5 NoTracking-Abfragen

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a>5,1 Deaktivieren der Änderungs Nachverfolgung, um den Status Verwaltungsaufwand zu reduzieren

Wenn Sie in einem schreibgeschützten Szenario arbeiten und den mehr Aufwand beim Laden der Objekte in den objectstatus Manager vermeiden möchten, können Sie keine nach Verfolgungs Abfragen ausgeben.Die Änderungs Nachverfolgung kann auf Abfrage Ebene deaktiviert werden.

Beachten Sie jedoch, dass Sie durch das Deaktivieren der Änderungs Nachverfolgung den Objekt Cache tatsächlich ausschalten. Wenn Sie eine Abfrage für eine Entität durchführen, können Sie die Materialisierung nicht überspringen, indem Sie die zuvor materialisierten Abfrageergebnisse von objectstatus Manager abrufen. Wenn Sie wiederholt die gleichen Entitäten im gleichen Kontext Abfragen, sehen Sie möglicherweise einen Leistungsvorteil von der Aktivierung der Änderungs Nachverfolgung.

Bei der Abfrage mithilfe von ObjectContext merken sich die Instanzen von ObjectQuery und ObjectSet eine MergeOption, nachdem Sie festgelegt wurde, und Abfragen, die für Sie erstellt werden, erben die effektive MergeOption der übergeordneten Abfrage. Wenn Sie dbcontext verwenden, kann die Überwachung durch Aufrufen des asnotracking ()-Modifizierers für das dbset deaktiviert werden.

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a>5.1.1 Deaktivierung der Änderungs Nachverfolgung für eine Abfrage bei Verwendung von dbcontext

Sie können den Modus einer Abfrage auf NoTracking umstellen, indem Sie in der Abfrage einen aufzurufenden Rückruf der asnotracking ()-Methode verketten. Anders als bei ObjectQuery haben die dbset-und dbquery-Klassen in der dbcontext-API keine änderbare Eigenschaft für die MergeOption.

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a>5.1.2 Deaktivieren der Änderungs Nachverfolgung auf Abfrage Ebene mithilfe von ObjectContext

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a>5.1.3 Deaktivieren der Änderungs Nachverfolgung für eine gesamte Entitätenmenge mithilfe von ObjectContext

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a>5,2 Testmetriken veranschaulichen den Leistungsvorteil von NoTracking-Abfragen

In diesem Test betrachten wir die Kosten für das Ausfüllen von objectstatus Manager, indem wir die Nachverfolgung mit NoTracking-Abfragen für das Navision-Modell vergleichen. Eine Beschreibung des Navision-Modells und der ausgeführten Abfrage Typen finden Sie im Anhang. In diesem Test durchlaufen wir die Abfrage Liste und führen jede einzelne aus. Wir haben zwei Variationen des Tests ausgeführt, einmal mit NoTracking-Abfragen und einmal mit der Standard Zusammenfassungs Option "AppendOnly". Wir haben jede Variation dreimal ausgeführt und den Mittelwert der Ausführungen übernommen. Zwischen den Tests löschen wir den Abfragecache auf dem SQL Server und verkleinern tempdb, indem wir die folgenden Befehle ausführen:

1.  DBCC DROPCLEANBUFFERS
2.  DBCC FREEPROCCACHE
3.  DBCC SHRINKDATABASE (tempdb, 0)

Testergebnisse, Median über 3 Ausführungen:

|                        | keine Nachverfolgung – Workingset | keine Nachverfolgung – Zeit | Nur anfügen – Workingset | Nur anfügen – Zeit |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| **Entity Framework 5** | 460361728                 | 1163536 MS         | 596545536                 | 1273042 MS         |
| **Entity Framework 6** | 647127040                 | 190228 MS          | 832798720                 | 195521 MS          |

Entity Framework 5 hat am Ende des Testlaufs einen geringeren Speicherbedarf als Entity Framework 6. Der zusätzliche von Entity Framework 6 belegte Arbeitsspeicher ist das Ergebnis zusätzlicher Speicherstrukturen und Code, die neue Features und eine bessere Leistung ermöglichen.

Bei der Verwendung von objectstatus Manager gibt es auch einen deutlichen Unterschied bei der Speicher Beanspruchung. Entity Framework 5 hat den Speicherbedarf um 30% gesteigert, wenn alle Entitäten nachverfolgt werden, die aus der Datenbank materialisiert wurden. In Entity Framework 6 wurde der Speicherbedarf um 28% gesteigert.

Im Hinblick auf die Zeit führt Entity Framework 6 Entity Framework 5 in diesem Test um einen großen Rand aus. Entity Framework 6 hat den Test in ungefähr 16% der von Entity Framework 5 verbrauchten Zeit abgeschlossen. Außerdem nimmt Entity Framework 5 9% mehr Zeit in Anspruch, wenn "objectstatus Manager" verwendet wird. Im Vergleich dazu verwendet Entity Framework 6 3% mehr Zeit, wenn objectstatus Manager verwendet wird.

## <a name="6-query-execution-options"></a>6 Abfrage Ausführungs Optionen

Entity Framework bietet verschiedene Möglichkeiten für die Abfrage. Wir sehen uns die folgenden Optionen an, vergleichen die vor-und Nachteile der einzelnen und überprüfen ihre Leistungsmerkmale:

-   LINQ to Entities.
-   Keine nach Verfolgungs LINQ to Entities.
-   Entity SQL über eine ObjectQuery.
-   Entity SQL über einem EntityCommand.
-   ExecuteStoreQuery.
-   SqlQuery.
-   CompiledQuery.

### <a name="61-linq-to-entities-queries"></a>6,1 LINQ to Entities Abfragen

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

**Vorteile**

-   Geeignet für CUD-Vorgänge.
-   Vollständig materialisierte Objekte.
-   Das einfachste schreiben mit Syntax, die in die Programmiersprache integriert ist.
-   Gute Leistung.

**Nachteile**

-   Bestimmte technische Einschränkungen, z. b.:
    -   Muster, die DefaultIfEmpty für äußere joinabfragen verwenden, führen zu komplexeren Abfragen als einfache äußere JOIN-Anweisungen in Entity SQL.
    -   Sie können "like" weiterhin nicht mit dem allgemeinen Musterabgleich verwenden.

### <a name="62-no-tracking-linq-to-entities-queries"></a>6,2 keine Nachverfolgung LINQ to Entities Abfragen

Wenn der Kontext ObjectContext ableitet:

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

Wenn der Kontext dbcontext ableitet:

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

**Vorteile**

-   Verbesserte Leistung gegenüber regulären LINQ-Abfragen.
-   Vollständig materialisierte Objekte.
-   Das einfachste schreiben mit Syntax, die in die Programmiersprache integriert ist.

**Nachteile**

-   Nicht geeignet für CUD-Vorgänge.
-   Bestimmte technische Einschränkungen, z. b.:
    -   Muster, die DefaultIfEmpty für äußere joinabfragen verwenden, führen zu komplexeren Abfragen als einfache äußere JOIN-Anweisungen in Entity SQL.
    -   Sie können "like" weiterhin nicht mit dem allgemeinen Musterabgleich verwenden.

Beachten Sie, dass Abfragen, die skalare Eigenschaften projizieren, auch dann nicht nachverfolgt werden, wenn NoTracking nicht angegeben ist. Beispiel:

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

Diese spezielle Abfrage gibt nicht explizit die NoTracking-Angabe an, aber da Sie keinen Typ materialisiert, der dem Objekt Zustands-Manager bekannt ist, wird das materialisierte Ergebnis nicht nachverfolgt.

### <a name="63-entity-sql-over-an-objectquery"></a>6,3 Entity SQL über ObjectQuery

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

**Vorteile**

-   Geeignet für CUD-Vorgänge.
-   Vollständig materialisierte Objekte.
-   Unterstützt Zwischenspeichern von Abfrage Plänen.

**Nachteile**

-   Umfasst Text Abfrage Zeichenfolgen, die anfälliger für Benutzerfehler sind als in die Sprache integrierte Abfragekonstrukte.

### <a name="64-entity-sql-over-an-entity-command"></a>6,4 Entity SQL über einen Entitäts Befehl

``` csharp
EntityCommand cmd = eConn.CreateCommand();
cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
{
    while (reader.Read())
    {
        // manually 'materialize' the product
    }
}
```

**Vorteile**

-   Unterstützt das Zwischenspeichern von Abfrage Plänen in .NET 4,0 (die Plan Zwischenspeicherung wird von allen anderen Abfrage Typen in .NET 4,5 unterstützt).

**Nachteile**

-   Umfasst Text Abfrage Zeichenfolgen, die anfälliger für Benutzerfehler sind als in die Sprache integrierte Abfragekonstrukte.
-   Nicht geeignet für CUD-Vorgänge.
-   Die Ergebnisse werden nicht automatisch materialisiert und müssen vom Daten Reader gelesen werden.

### <a name="65-sqlquery-and-executestorequery"></a>6,5 sqlQuery und ExecuteStoreQuery

SqlQuery für Datenbank:

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

SqlQuery für dbset:

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

Execytestorequery:

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

**Vorteile**

-   Allgemein schnellste Leistung, da der Plan Compiler umgangen wird.
-   Vollständig materialisierte Objekte.
-   Eignet sich für CUD-Vorgänge, wenn Sie aus dem dbset verwendet werden.

**Nachteile**

-   Die Abfrage ist Text und fehleranfällig.
-   Die Abfrage ist an ein bestimmtes Back-End gebunden, indem die Speicher Semantik anstelle der konzeptionellen Semantik verwendet wird.
-   Wenn eine Vererbung vorhanden ist, muss die abgeforderte Abfrage die Zuordnung von Bedingungen für den angeforderten Typ berücksichtigen.

### <a name="66-compiledquery"></a>6,6 CompiledQuery

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

**Vorteile**

-   Bietet eine Leistungsverbesserung von bis zu 7% gegenüber regulären LINQ-Abfragen.
-   Vollständig materialisierte Objekte.
-   Geeignet für CUD-Vorgänge.

**Nachteile**

-   Größere Komplexität und Programmieraufwand.
-   Die Leistungsverbesserung geht verloren, wenn eine kompilierte Abfrage erstellt wird.
-   Einige LINQ-Abfragen können nicht als CompiledQuery geschrieben werden, z. b. Projektionen anonymer Typen.

### <a name="67-performance-comparison-of-different-query-options"></a>6,7 Leistungsvergleich verschiedener Abfrage Optionen

Einfache Mikrobenchmarks, bei denen die Kontext Erstellung nicht durchgesetzt wurde, wurden in den Test eingefügt. Wir haben das Abfragen von 5000-mal für eine Reihe von nicht zwischengespeicherten Entitäten in einer kontrollierten Umgebung gemessen. Diese Zahlen müssen mit einer Warnung erstellt werden: Sie reflektieren nicht die tatsächlichen Zahlen, die von einer Anwendung erzeugt werden, sondern Sie sind ein sehr genaues Maß für den Leistungsunterschied, wenn verschiedene Abfrage Optionen als Äpfel-zu-Äpfel verglichen werden, ausgenommen der Kosten für die Erstellung eines neuen Kontexts.

| EF  | Test                                 | Zeit (MS) | Arbeitsspeicher   |
|:----|:-------------------------------------|:----------|:---------|
| EF5 | ObjectContext ESQL                   | 2414      | 38801408 |
| EF5 | ObjectContext (LINQ-Abfrage)             | 2692      | 38277120 |
| EF5 | Dbcontext LINQ-Abfrage keine Nachverfolgung     | 2818      | 41840640 |
| EF5 | Dbcontext-LINQ-Abfrage                 | 2930      | 41771008 |
| EF5 | ObjectContext LINQ-Abfrage keine Nachverfolgung | 3013      | 38412288 |
|     |                                      |           |          |
| EF6 | ObjectContext ESQL                   | 2059      | 46039040 |
| EF6 | ObjectContext (LINQ-Abfrage)             | 3074      | 45248512 |
| EF6 | Dbcontext LINQ-Abfrage keine Nachverfolgung     | 3125      | 47575040 |
| EF6 | Dbcontext-LINQ-Abfrage                 | 3420      | 47652864 |
| EF6 | ObjectContext LINQ-Abfrage keine Nachverfolgung | 3593      | 45260800 |

![EF5 Micro-Benchmarks, 5000-warme Iterationen](~/ef6/media/ef5micro5000warm.png)

![EF6 Micro-Benchmarks, 5000-warme Iterationen](~/ef6/media/ef6micro5000warm.png)

Mikrobenchmarks sind sehr empfindlich gegenüber kleinen Änderungen im Code. In diesem Fall ist der Unterschied zwischen den Kosten von Entity Framework 5 und Entity Framework 6 auf das Hinzufügen von [Abfang](xref:ef6/fundamentals/logging-and-interception) -und [Transaktions Verbesserungen](xref:ef6/saving/transactions)zurückzuführen. Diese Mikrobenchmarks-Zahlen sind jedoch eine verstärkte Vision in einem sehr kleinen Fragment, was Entity Framework tut. In realen Szenarien mit warmen Abfragen sollte bei der Aktualisierung von Entity Framework 5 auf Entity Framework 6 keine Leistungs Regression auftreten.

Um die tatsächliche Leistung der verschiedenen Abfrage Optionen zu vergleichen, haben wir fünf separate Test Variationen erstellt, bei denen wir eine andere Abfrage Option verwenden, um alle Produkte auszuwählen, deren Kategoriename "Beverage" lautet. Jede Iterationen umfasst die Kosten für die Erstellung des Kontexts und die Kosten für das Materialisieren aller zurückgegebenen Entitäten. 10 Iterationen werden ohne Zeitüberschreitung ausgeführt, bevor die Summe von 1000 zeitgesteuerten Iterationen übernehmen wird. Die angezeigten Ergebnisse sind die durchschnittliche Ausführung von 5 Ausführungen der einzelnen Tests. Weitere Informationen finden Sie in Anhang B, in dem der Code für den Test enthalten ist.

| EF  | Test                                        | Zeit (MS) | Arbeitsspeicher   |
|:----|:--------------------------------------------|:----------|:---------|
| EF5 | ObjectContext-Entitäts Befehl                | 621       | 39350272 |
| EF5 | Dbcontext-SQL-Abfrage für Datenbank             | 825       | 37519360 |
| EF5 | ObjectContext-Speicher Abfrage                   | 878       | 39460864 |
| EF5 | ObjectContext LINQ-Abfrage keine Nachverfolgung        | 969       | 38293504 |
| EF5 | ObjectContext Entity SQL mit Objekt Abfrage | 1089      | 38981632 |
| EF5 | Kompilierte ObjectContext-Abfrage                | 1099      | 38682624 |
| EF5 | ObjectContext (LINQ-Abfrage)                    | 1152      | 38178816 |
| EF5 | Dbcontext LINQ-Abfrage keine Nachverfolgung            | 1208      | 41803776 |
| EF5 | Dbcontext-SQL-Abfrage für dbset                | 1414      | 37982208 |
| EF5 | Dbcontext-LINQ-Abfrage                        | 1574      | 41738240 |
|     |                                             |           |          |
| EF6 | ObjectContext-Entitäts Befehl                | 480       | 47247360 |
| EF6 | ObjectContext-Speicher Abfrage                   | 493       | 46739456 |
| EF6 | Dbcontext-SQL-Abfrage für Datenbank             | 614       | 41607168 |
| EF6 | ObjectContext LINQ-Abfrage keine Nachverfolgung        | 684       | 46333952 |
| EF6 | ObjectContext Entity SQL mit Objekt Abfrage | 767       | 48865280 |
| EF6 | Kompilierte ObjectContext-Abfrage                | 788       | 48467968 |
| EF6 | Dbcontext LINQ-Abfrage keine Nachverfolgung            | 878       | 47554560 |
| EF6 | ObjectContext (LINQ-Abfrage)                    | 953       | 47632384 |
| EF6 | Dbcontext-SQL-Abfrage für dbset                | 1023      | 41992192 |
| EF6 | Dbcontext-LINQ-Abfrage                        | 1290      | 47529984 |


![EF5 warm Query 1000 Iterationen](~/ef6/media/ef5warmquery1000.png)

![EF6 warm Query 1000 Iterationen](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> Aus Gründen der Vollständigkeit enthalten wir eine Variation, bei der wir eine Entity SQL Abfrage für einen EntityCommand ausführen. Da die Ergebnisse für solche Abfragen jedoch nicht materialisiert werden, ist der Vergleich nicht notwendigerweise "Äpfel-zu-Äpfel". Der Test beinhaltet eine genaue Näherung, um den Vergleich zu gestalten.

In diesem End-to-End-Fall Entity Framework 6 Entity Framework 5 aufgrund von Leistungsverbesserungen, die an mehreren Teilen des Stapels vorgenommen werden, einschließlich einer wesentlich leichteren dbcontext-Initialisierung und schnelleren MetadataCollection T-suchen &lt; &gt; .

## <a name="7-design-time-performance-considerations"></a>7 Überlegungen zur Entwurfszeit Leistung

### <a name="71-inheritance-strategies"></a>7,1 Vererbungs Strategien

Ein weiterer Leistungs Aspekt bei der Verwendung von Entity Framework ist die von Ihnen verwendete Vererbungs Strategie. Entity Framework unterstützt 3 grundlegende Arten von Vererbung und deren Kombinationen:

-   Tabelle pro Hierarchie (TPH) – wobei jeder Vererbungs Satz einer Tabelle mit einer diskriminatorspalte zugeordnet wird, um anzugeben, welcher bestimmte Typ in der Hierarchie in der Zeile dargestellt wird.
-   Tabelle pro Typ (TPT) – wobei jeder Typ über eine eigene Tabelle in der Datenbank verfügt. in den untergeordneten Tabellen sind nur die Spalten definiert, die in der übergeordneten Tabelle enthalten sind.
-   Tabelle pro Klasse (TPC) – wobei jeder Typ über eine eigene vollständige Tabelle in der Datenbank verfügt. die untergeordneten Tabellen definieren alle Ihre Felder, einschließlich derjenigen, die in übergeordneten Typen definiert sind.

Wenn in Ihrem Modell die TPT-Vererbung verwendet wird, sind die generierten Abfragen komplexer als diejenigen, die mit den anderen Vererbungs Strategien generiert werden, was zu längeren Ausführungszeiten im Speicher führen kann.Es dauert in der Regel länger, bis Abfragen über ein TPT-Modell generiert werden und die resultierenden Objekte materialisiert werden.

Weitere Informationen finden Sie Entity Framework im MSDN-Blogbeitrag unter "Überlegungen zur Leistung bei der Verwendung der TPT-Vererbung (Tabelle pro Typ)": \<https://docs.microsoft.com/archive/blogs/adonet/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework> .

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a>7.1.1 vermeiden von TPT in Model First oder Code First Anwendungen

Wenn Sie ein Modell über eine vorhandene Datenbank mit einem TPT-Schema erstellen, haben Sie nicht viele Optionen. Wenn Sie jedoch eine Anwendung mit Model First oder Code First erstellen, sollten Sie die TPT-Vererbung für Leistungsprobleme vermeiden.

Wenn Sie Model First im Entity Designer-Assistenten verwenden, erhalten Sie die TPT für jede Vererbung in Ihrem Modell. Wenn Sie mit Model First zu einer TPH-Vererbungs Strategie wechseln möchten, können Sie das "Entity Designer Database Generation Power Pack" verwenden, das in der Visual Studio Gallery () verfügbar ist \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/> .

Wenn Sie Code First verwenden, um die Zuordnung eines Modells mit Vererbung zu konfigurieren, verwendet EF standardmäßig TPH. Daher werden alle Entitäten in der Vererbungs Hierarchie derselben Tabelle zugeordnet. Weitere Informationen finden Sie im MSDN Magazine () im Abschnitt "Mapping with the fließend API" des Themas "Code First in Entity Framework 4.1" [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx) .

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a>7,2 Upgrade von EF4 zur Verbesserung der Modell Generierungs Zeit

Eine SQL Server spezifische Verbesserung des Algorithmus, der die Speicherschicht (SSDL) des Modells generiert, ist in Entity Framework 5 und 6 verfügbar und als Update für Entity Framework 4, wenn Visual Studio 2010 SP1 installiert ist. Die folgenden Testergebnisse veranschaulichen die Verbesserung beim Erstellen eines sehr großen Modells, in diesem Fall das Navision-Modell. Weitere Informationen hierzu finden Sie in Anhang C.

Das Modell enthält 1005 Entitätenmengen und 4227 Zuordnungs Sätze.

| Konfiguration                              | Aufschlüsselung der verbrauchten Zeit                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Visual Studio 2010, Entity Framework 4     | SSDL-Generierung: 2 Std. 27 min. <br/> Mapping-Generierung: 1 Sekunde <br/> CSDL-Generierung: 1 Sekunde <br/> Objectlayer-Generierung: 1 Sekunde <br/> Ansichts Generierung: 2 Std. 14 min. |
| Visual Studio 2010 SP1, Entity Framework 4 | SSDL-Generierung: 1 Sekunde <br/> Mapping-Generierung: 1 Sekunde <br/> CSDL-Generierung: 1 Sekunde <br/> Objectlayer-Generierung: 1 Sekunde <br/> Sicht Generierung: 1 Stunde 53 min.   |
| Visual Studio 2013, Entity Framework 5     | SSDL-Generierung: 1 Sekunde <br/> Mapping-Generierung: 1 Sekunde <br/> CSDL-Generierung: 1 Sekunde <br/> Objectlayer-Generierung: 1 Sekunde <br/> Ansichts Generierung: 65 Minuten    |
| Visual Studio 2013, Entity Framework 6     | SSDL-Generierung: 1 Sekunde <br/> Mapping-Generierung: 1 Sekunde <br/> CSDL-Generierung: 1 Sekunde <br/> Objectlayer-Generierung: 1 Sekunde <br/> Sicht Generierung: 28 Sekunden.   |


Beachten Sie, dass die Auslastung beim Erzeugen der SSDL fast vollständig für den SQL Server aufgewendet wird, während der Client Entwicklungs Computer im Leerlauf darauf wartet, dass Ergebnisse vom Server zurückgegeben werden. DBAs sollten diese Verbesserung besonders schätzen. Es ist auch erwähnenswert, dass im Wesentlichen die Gesamtkosten für die Modell Generierung in der Ansichts Generierung erfolgt sind.

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a>7,3 Aufteilen von großen Modellen mit Database First und Model First

Wenn die Modell Größe zunimmt, wird die Designer Oberfläche überladen und ist schwierig zu verwenden. In der Regel wird ein Modell mit mehr als 300 Entitäten als zu groß betrachtet, um den Designer effektiv zu verwenden. Im folgenden Blogbeitrag werden verschiedene Optionen zum Aufteilen großer Modelle beschrieben: \<https://docs.microsoft.com/archive/blogs/adonet/working-with-large-models-in-entity-framework-part-2> .

Der Beitrag wurde für die erste Version von Entity Framework geschrieben, die Schritte sind jedoch weiterhin anwendbar.

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a>7,4 Überlegungen zur Leistung mit dem Entitäts Datenquellen-Steuerelement

Es wurden Fälle bei Multithread-Leistungs-und Belastungstests erkannt, bei denen sich die Leistung einer Webanwendung mit dem EntityDataSource-Steuerelement erheblich verschlechtert. Der Grund dafür ist, dass EntityDataSource wiederholt MetadataWorkspace. LoadFromAssembly für die Assemblys aufruft, auf die die Webanwendung verweist, um die Typen zu ermitteln, die als Entitäten verwendet werden sollen.

Die Lösung besteht darin, den contexttyid-Wert der EntityDataSource auf den Typnamen der abgeleiteten ObjectContext-Klasse festzulegen. Dadurch wird der Mechanismus deaktiviert, der alle referenzierten Assemblys für Entitäts Typen scannt.

Wenn Sie das Feld "contexttytzame" festlegen, wird auch ein funktionales Problem vermieden, bei dem die EntityDataSource in .NET 4,0 eine ReflectionTypeLoadException auslöst, wenn ein Typ nicht über Reflektion aus einer Assembly geladen werden kann. Dieses Problem wurde in .NET 4,5 behoben.

### <a name="75-poco-entities-and-change-tracking-proxies"></a>7,5 poco-Entitäten und Änderungs nach Verfolgungs Proxys

Entity Framework ermöglicht es Ihnen, benutzerdefinierte Daten Klassen in Verbindung mit dem Datenmodell zu verwenden, ohne Änderungen an den Daten Klassen vorzunehmen. Dies bedeutet, dass Sie POCO-Objekte (Plain-old CLR objects), z. B. vorhandene Domänenobjekte, mit dem Datenmodell verwenden können. Diese poco-Daten Klassen (auch als Persistenz ignorierende Objekte bezeichnet), die Entitäten zugeordnet werden, die in einem Datenmodell definiert sind, unterstützen die meisten der gleichen Abfrage-, Einfüge-, Aktualisierungs-und Lösch Verhalten wie Entitäts Typen, die von den Entity Data Model Tools generiert werden.

Entity Framework können auch Proxy Klassen erstellen, die von ihren poco-Typen abgeleitet werden, die verwendet werden, um Funktionen wie Lazy Loading und die automatische Änderungs Nachverfolgung für poco-Entitäten zu aktivieren. Die poco-Klassen müssen bestimmte Anforderungen erfüllen, damit Entity Framework Proxys verwenden können, wie hier beschrieben: [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx) .

Proxynachverfolgungsproxys benachrichtigen den Objekt Zustands-Manager immer dann, wenn für eine der Eigenschaften der Entitäten der Wert geändert wird, sodass Entity Framework den tatsächlichen Zustand ihrer Entitäten ständig kennt. Dies erfolgt durch Hinzufügen von Benachrichtigungs Ereignissen zum Text der Setter-Methoden der Eigenschaften, und der Objekt Zustands-Manager verarbeitet solche Ereignisse. Beachten Sie, dass das Erstellen einer Proxy Entität in der Regel teurer ist als das Erstellen einer nicht-Proxy-poco-Entität aufgrund des zusätzlichen Satzes von Ereignissen, die von Entity Framework erstellt werden.

Wenn eine poco-Entität keinen Änderungs nach Verfolgungs Proxy aufweist, werden Änderungen durch Vergleichen des Inhalts ihrer Entitäten mit einer Kopie eines vorherigen gespeicherten Zustands gefunden. Dieser umfassende Vergleich wird ein langwieriger Prozess, wenn Sie viele Entitäten in ihrem Kontext haben oder wenn Ihre Entitäten über eine sehr große Anzahl von Eigenschaften verfügen, auch wenn Sie sich seit dem letzten Vergleich nicht geändert haben.

Zusammenfassung: beim Erstellen des Änderungs nach Verfolgungs Proxys wird eine Leistungs Beeinträchtigung erzielt, aber die Änderungs Nachverfolgung hilft Ihnen, den Änderungs Erkennungsprozess zu beschleunigen, wenn die Entitäten viele Eigenschaften haben oder wenn Sie über viele Entitäten in Ihrem Modell verfügen. Für Entitäten mit einer kleinen Anzahl von Eigenschaften, bei denen die Menge an Entitäten nicht zu stark zunimmt, sind Änderungs nach Verfolgungs Proxys möglicherweise nicht von großem Nutzen.

## <a name="8-loading-related-entities"></a>8 Laden verwandter Entitäten

### <a name="81-lazy-loading-vs-eager-loading"></a>8,1 Lazy Load im Vergleich zum unverwollten laden

Entity Framework bietet verschiedene Möglichkeiten, um die Entitäten zu laden, die mit der Ziel Entität verknüpft sind. Wenn Sie z. b. Produkte Abfragen, gibt es verschiedene Möglichkeiten, wie die zugehörigen Bestellungen in den Objekt Zustands-Manager geladen werden. Aus Sicht der Leistung ist es am wichtigsten, beim Laden von verknüpften Entitäten zu beachten, ob Lazy Load oder das unverzügliches Laden verwendet werden soll.

Wenn Sie das unverzügliches Laden verwenden, werden die zugehörigen Entitäten zusammen mit der Ziel Entitätenmenge geladen. Sie verwenden eine include-Anweisung in der Abfrage, um anzugeben, welche verknüpften Entitäten Sie einfügen möchten.

Wenn Lazy Load verwendet wird, führt die anfängliche Abfrage nur die Ziel Entitätenmenge ein. Wenn Sie jedoch auf eine Navigations Eigenschaft zugreifen, wird eine andere Abfrage für den Speicher ausgegeben, um die zugehörige Entität zu laden.

Nachdem eine Entität geladen wurde, laden alle weiteren Abfragen für die Entität Sie direkt aus dem Objektstatus-Manager, unabhängig davon, ob Sie Lazy Loading oder Eager Loading verwenden.

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a>8,2 auswählen zwischen Lazy Load und unverzüglichem laden

Wichtig ist, dass Sie den Unterschied zwischen Lazy Load und Lazy Load verstehen, damit Sie die richtige Wahl für Ihre Anwendung treffen können. Dies hilft Ihnen, den Kompromiss zwischen mehreren Anforderungen an die Datenbank und einer einzelnen Anforderung, die möglicherweise eine große Nutzlast enthält, auszuwerten. Es kann sinnvoll sein, Eager Loading in einigen Teilen Ihrer Anwendung zu verwenden und in anderen Teilen Lazy Loading.

Angenommen, Sie möchten die Kunden, die im Vereinigten Königreich leben, und deren Bestell Anzahl Abfragen.

**Verwenden des unverwollten**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

**Verwenden von Lazy Load**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    context.ContextOptions.LazyLoadingEnabled = true;

    //Notice that the Include method call is missing in the query
    var ukCustomers = context.Customers.Where(c => c.Address.Country == "UK");

    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

Wenn Sie Eager Loading verwenden, geben Sie eine einzelne Abfrage aus, mit der alle Kunden und alle Bestellungen zurückgegeben werden. Der Store-Befehl sieht wie folgt aus:

``` SQL
SELECT
[Project1].[C1] AS [C1],
[Project1].[CustomerID] AS [CustomerID],
[Project1].[CompanyName] AS [CompanyName],
[Project1].[ContactName] AS [ContactName],
[Project1].[ContactTitle] AS [ContactTitle],
[Project1].[Address] AS [Address],
[Project1].[City] AS [City],
[Project1].[Region] AS [Region],
[Project1].[PostalCode] AS [PostalCode],
[Project1].[Country] AS [Country],
[Project1].[Phone] AS [Phone],
[Project1].[Fax] AS [Fax],
[Project1].[C2] AS [C2],
[Project1].[OrderID] AS [OrderID],
[Project1].[CustomerID1] AS [CustomerID1],
[Project1].[EmployeeID] AS [EmployeeID],
[Project1].[OrderDate] AS [OrderDate],
[Project1].[RequiredDate] AS [RequiredDate],
[Project1].[ShippedDate] AS [ShippedDate],
[Project1].[ShipVia] AS [ShipVia],
[Project1].[Freight] AS [Freight],
[Project1].[ShipName] AS [ShipName],
[Project1].[ShipAddress] AS [ShipAddress],
[Project1].[ShipCity] AS [ShipCity],
[Project1].[ShipRegion] AS [ShipRegion],
[Project1].[ShipPostalCode] AS [ShipPostalCode],
[Project1].[ShipCountry] AS [ShipCountry]
FROM ( SELECT
      [Extent1].[CustomerID] AS [CustomerID],
       [Extent1].[CompanyName] AS [CompanyName],
       [Extent1].[ContactName] AS [ContactName],
       [Extent1].[ContactTitle] AS [ContactTitle],
       [Extent1].[Address] AS [Address],
       [Extent1].[City] AS [City],
       [Extent1].[Region] AS [Region],
       [Extent1].[PostalCode] AS [PostalCode],
       [Extent1].[Country] AS [Country],
       [Extent1].[Phone] AS [Phone],
       [Extent1].[Fax] AS [Fax],
      1 AS [C1],
       [Extent2].[OrderID] AS [OrderID],
       [Extent2].[CustomerID] AS [CustomerID1],
       [Extent2].[EmployeeID] AS [EmployeeID],
       [Extent2].[OrderDate] AS [OrderDate],
       [Extent2].[RequiredDate] AS [RequiredDate],
       [Extent2].[ShippedDate] AS [ShippedDate],
       [Extent2].[ShipVia] AS [ShipVia],
       [Extent2].[Freight] AS [Freight],
       [Extent2].[ShipName] AS [ShipName],
       [Extent2].[ShipAddress] AS [ShipAddress],
       [Extent2].[ShipCity] AS [ShipCity],
       [Extent2].[ShipRegion] AS [ShipRegion],
       [Extent2].[ShipPostalCode] AS [ShipPostalCode],
       [Extent2].[ShipCountry] AS [ShipCountry],
      CASE WHEN ([Extent2].[OrderID] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C2]
      FROM  [dbo].[Customers] AS [Extent1]
      LEFT OUTER JOIN [dbo].[Orders] AS [Extent2] ON [Extent1].[CustomerID] = [Extent2].[CustomerID]
      WHERE N'UK' = [Extent1].[Country]
)  AS [Project1]
ORDER BY [Project1].[CustomerID] ASC, [Project1].[C2] ASC
```

Wenn Sie Lazy Loading verwenden, geben Sie zunächst die folgende Abfrage aus:

``` SQL
SELECT
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[CompanyName] AS [CompanyName],
[Extent1].[ContactName] AS [ContactName],
[Extent1].[ContactTitle] AS [ContactTitle],
[Extent1].[Address] AS [Address],
[Extent1].[City] AS [City],
[Extent1].[Region] AS [Region],
[Extent1].[PostalCode] AS [PostalCode],
[Extent1].[Country] AS [Country],
[Extent1].[Phone] AS [Phone],
[Extent1].[Fax] AS [Fax]
FROM [dbo].[Customers] AS [Extent1]
WHERE N'UK' = [Extent1].[Country]
```

Und jedes Mal, wenn Sie auf die Orders-Navigations Eigenschaft eines Kunden zugreifen, wird eine andere Abfrage wie die folgende für den Speicher ausgegeben:

``` SQL
exec sp_executesql N'SELECT
[Extent1].[OrderID] AS [OrderID],
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[EmployeeID] AS [EmployeeID],
[Extent1].[OrderDate] AS [OrderDate],
[Extent1].[RequiredDate] AS [RequiredDate],
[Extent1].[ShippedDate] AS [ShippedDate],
[Extent1].[ShipVia] AS [ShipVia],
[Extent1].[Freight] AS [Freight],
[Extent1].[ShipName] AS [ShipName],
[Extent1].[ShipAddress] AS [ShipAddress],
[Extent1].[ShipCity] AS [ShipCity],
[Extent1].[ShipRegion] AS [ShipRegion],
[Extent1].[ShipPostalCode] AS [ShipPostalCode],
[Extent1].[ShipCountry] AS [ShipCountry]
FROM [dbo].[Orders] AS [Extent1]
WHERE [Extent1].[CustomerID] = @EntityKeyValue1',N'@EntityKeyValue1 nchar(5)',@EntityKeyValue1=N'AROUT'
```

Weitere Informationen finden Sie unter [Laden verwandter Objekte](https://msdn.microsoft.com/library/bb896272.aspx).

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a>8.2.1 Lazy Load im Vergleich zu unverzüglichem laden Cheat Sheet

Es gibt keine Möglichkeit, Eager Loading im Vergleich zu Lazy Loading zu wählen. Versuchen Sie zunächst, die Unterschiede zwischen beiden Strategien zu verstehen, damit Sie eine fundierte Entscheidung treffen können. Beachten Sie auch, wenn Ihr Code in eines der folgenden Szenarien passt:

| Szenario                                                                    | Unsere Empfehlung                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Benötigen Sie Zugriff auf viele Navigations Eigenschaften aus den abgerufenen Entitäten? | **Nein** , beide Optionen werden wahrscheinlich funktionieren. Wenn die von der Abfrage abzurufende Nutzlast jedoch nicht zu groß ist, können Sie Leistungsvorteile erzielen, indem Sie das unverzügliches Laden verwenden, da dafür weniger Netzwerkroundtrips erforderlich sind, um Ihre Objekte zu materialisieren. <br/> <br/> **Ja** : Wenn Sie von den Entitäten aus auf viele Navigations Eigenschaften zugreifen müssen, verwenden Sie mehrere include-Anweisungen in der Abfrage mit unverzüglichem laden. Je mehr Entitäten Sie einbeziehen, desto größer ist die Nutzlast, die von der Abfrage zurückgegeben wird. Wenn Sie drei oder mehr Entitäten in die Abfrage einschließen, sollten Sie in Erwägung gezogen, zu Lazy Load zu wechseln. |
| Wissen Sie genau, welche Daten zur Laufzeit benötigt werden?                   | **Kein** Lazy Load ist für Sie besser geeignet. Andernfalls können Sie Daten Abfragen, die Sie nicht benötigen. <br/> <br/> " **Ja** " ist wahrscheinlich die beste Lösung. Dadurch wird das Laden ganzer Mengen beschleunigt. Wenn die Abfrage das Abrufen einer sehr großen Datenmenge erfordert und diese zu langsam wird, versuchen Sie stattdessen Lazy Load.                                                                                                                                                                                                                                                       |
| Wird Ihr Code weit von der Datenbank ausgeführt? (größere Netzwerk Latenz)  | **Nein** : Wenn die Netzwerk Latenz kein Problem ist, kann der Code durch die Verwendung von Lazy Load vereinfacht werden. Denken Sie daran, dass sich die Topologie Ihrer Anwendung ändern kann, und nehmen Sie daher keine Daten Bank Nähe. <br/> <br/> **Ja** : Wenn es sich bei dem Netzwerk um ein Problem handelt, können Sie nur entscheiden, was für Ihr Szenario besser geeignet ist. Üblicherweise ist das vorzeitige Laden besser, da es weniger Roundtrips erfordert.                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a>8.2.2 Leistungsprobleme mit mehreren includes

Wenn wir Leistungs Fragen hören, die Probleme mit der Serverantwort Zeit betreffen, ist die Ursache des Problems häufig Abfragen mit mehreren include-Anweisungen. Das Einschließen von verknüpften Entitäten in eine Abfrage ist zwar leistungsstark, aber es ist wichtig zu verstehen, was im Zusammenhang geschieht.

Es dauert relativ lange, bis eine Abfrage mit mehreren include-Anweisungen den internen Plan Compiler durchläuft, um den Store-Befehl zu erhalten. Der Großteil dieser Zeit wird beim Versuch aufgewendet, die resultierende Abfrage zu optimieren. Der generierte Speicher Befehl enthält abhängig von ihrer Zuordnung einen äußeren Join oder eine Union für jede include-Anweisung. Abfragen wie diese führen zu großen verbundenen Diagrammen aus Ihrer Datenbank in einer einzelnen Nutzlast, wodurch alle Bandbreitenprobleme, insbesondere dann, wenn die Nutzlast viele Redundanz aufweist (z. b. Wenn mehrere Ebenen von include verwendet werden, zum Durchlaufen von Zuordnungen in der 1: n-Richtung verwendet werden).

Sie können überprüfen, ob Ihre Abfragen übermäßig große Nutzlasten zurückgeben, indem Sie auf den zugrunde liegenden TQL für die Abfrage zugreifen, indem Sie "$ tracestring" verwenden und den Speicher Befehl in SQL Server Management Studio ausführen, um die Nutzlastgröße anzuzeigen. In solchen Fällen können Sie versuchen, die Anzahl der include-Anweisungen in der Abfrage zu reduzieren, um nur die benötigten Daten zu erhalten. Oder Sie können die Abfrage in eine kleinere Sequenz von Unterabfragen zerlegen, z. b.:

**Vor dem Abbrechen der Abfrage:**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var customers = from c in context.Customers.Include(c => c.Orders)
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

**Nach dem Abbrechen der Abfrage:**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var orders = from o in context.Orders
                 where o.Customer.LastName.StartsWith(lastNameParameter)
                 select o;

    orders.Load();

    var customers = from c in context.Customers
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

Dies funktioniert nur für nach verfolgte Abfragen, da wir die Möglichkeit nutzen, den Kontext für die automatische Durchführung der Identitäts Auflösung und Zuordnungs Korrektur zu verwenden.

Wie bei Lazy Loading sind die Nachteile mehr Abfragen für kleinere Nutzlasten. Sie können auch Projektionen von einzelnen Eigenschaften verwenden, um explizit nur die benötigten Daten aus den einzelnen Entitäten auszuwählen. in diesem Fall werden jedoch keine Entitäten geladen, und Updates werden nicht unterstützt.

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a>8.2.3 Problem Umgehung, um Lazy Loading von Eigenschaften zu erhalten

Entity Framework Lazy Loading von skalaren oder komplexen Eigenschaften derzeit nicht unterstützt. In Fällen, in denen Sie über eine Tabelle verfügen, die ein großes Objekt wie z. b. ein BLOB enthält, können Sie die Tabellen Aufteilung verwenden, um die großen Eigenschaften in einer separaten Entität voneinander zu trennen. Angenommen, Sie verfügen über eine Tabelle "Product", die eine varbinary-Foto Spalte enthält. Wenn Sie in Ihren Abfragen nicht häufig auf diese Eigenschaft zugreifen müssen, können Sie die Tabellen Aufteilung verwenden, um nur die Teile der Entität zu verwenden, die Sie normalerweise benötigen. Die Entität, die das Produktfoto darstellt, wird nur geladen, wenn Sie Sie explizit benötigen.

Eine gute Ressource, die zeigt, wie Sie die Tabellen Aufteilung aktivieren, ist der Blogbeitrag "Tabellen Aufteilung in Entity Framework" von Gil Fink: \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx> .

## <a name="9-other-considerations"></a>9 weitere Überlegungen

### <a name="91-server-garbage-collection"></a>9,1 Garbage Collection für Server

Einige Benutzer können Ressourcenkonflikte feststellen, die die Parallelität einschränken, die Sie erwarten, wenn der Garbage Collector nicht ordnungsgemäß konfiguriert ist. Wenn EF in einem Multithread-Szenario oder in einer Anwendung verwendet wird, die einem serverseitigen System ähnelt, stellen Sie sicher, dass die Garbage Collection auf dem Server aktiviert ist. Dies erfolgt über eine einfache Einstellung in ihrer Anwendungs Konfigurationsdatei:

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

Dies sollte die Thread Konflikte verringern und den Durchsatz um bis zu 30% erhöhen. In der Regel sollten Sie immer testen, wie sich Ihre Anwendung verhält, indem Sie die klassische Garbage Collection (die für Benutzeroberflächen-und Client seitige Szenarien besser optimiert ist) sowie die Garbage Collection auf dem Server verwenden.

### <a name="92-autodetectchanges"></a>9,2 autodetectchanges

Wie bereits erwähnt, können Entity Framework Leistungsprobleme anzeigen, wenn der Objekt Cache über viele Entitäten verfügt. Bestimmte Vorgänge, wie z. b. "Add", "Remove", "Find", "Entry" und "SaveChanges", fordern Aufrufe von "DetectChanges" an, die abhängig davon, wie groß der Objekt Cache geworden ist, viel CPU Der Grund hierfür ist, dass der Objekt Cache und der Objekt Zustands-Manager versuchen, bei jedem Vorgang, der in einem Kontext ausgeführt wird, so synchronisiert zu bleiben, dass die erzeugten Daten in einer Vielzahl von Szenarien korrekt sind.

Im Allgemeinen empfiehlt es sich, die automatische Änderungs Erkennung Entity Framework für die gesamte Lebensdauer Ihrer Anwendung aktiviert zu lassen. Wenn Ihr Szenario durch hohe CPU-Auslastung negativ beeinflusst wird und ihre Profile darauf hindeuten, dass es sich bei dem betäter um den Erkennungs Nachweis handelt, empfiehlt es sich, autodetectchanges im sensiblen Teil Ihres Codes vorübergehend zu deaktivieren:

``` csharp
try
{
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    ...
}
finally
{
    context.Configuration.AutoDetectChangesEnabled = true;
}
```

Vor dem Ausschalten von autodetectchanges sollten Sie sich bewusst sein, dass dies dazu führen kann, dass Entity Framework die Fähigkeit verliert, bestimmte Informationen zu den Änderungen zu verfolgen, die für die Entitäten ausgeführt werden. Wenn Sie falsch behandelt werden, kann dies zu einer Daten Inkonsistenz Ihrer Anwendung führen. Weitere Informationen zum Ausschalten von autodetectchanges finden Sie unter \<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/> .

### <a name="93-context-per-request"></a>9,3-Kontext pro Anforderung

Die Kontexte von Entity Framework sollen als kurzlebige Instanzen verwendet werden, um die optimale Leistung zu gewährleisten. Es wird davon ausgegangen, dass Kontexte kurzlebig sind und verworfen werden. Daher wurden Sie so implementiert, dass Sie möglichst sehr einfach sind, und die Metadaten immer wieder überprüfen. In Webszenarien ist es wichtig, dies zu berücksichtigen und keinen Kontext für mehr als die Dauer einer einzelnen Anforderung zu erhalten. Analog dazu sollte der Kontext in nicht-Webanwendungen verworfen werden, basierend auf dem Verständnis der verschiedenen Ebenen der Zwischenspeicherung in der Entity Framework. Im Allgemeinen sollten Sie eine Kontext Instanz im gesamten Lebenszyklus der Anwendung sowie Kontexte pro Thread und statischen Kontexten vermeiden.

### <a name="94-database-null-semantics"></a>9,4-Datenbank-NULL-Semantik

Standardmäßig wird von Entity Framework SQL-Code generiert, der über eine C- \# null-Vergleichs Semantik verfügt. Betrachten Sie die folgende Beispielabfrage:

``` csharp
            int? categoryId = 7;
            int? supplierId = 8;
            decimal? unitPrice = 0;
            short? unitsInStock = 100;
            short? unitsOnOrder = 20;
            short? reorderLevel = null;

            var q = from p incontext.Products
                    wherep.Category.CategoryName == "Beverages"
                          || (p.CategoryID == categoryId
                                || p.SupplierID == supplierId
                                || p.UnitPrice == unitPrice
                                || p.UnitsInStock == unitsInStock
                                || p.UnitsOnOrder == unitsOnOrder
                                || p.ReorderLevel == reorderLevel)
                    select p;

            var r = q.ToList();
```

In diesem Beispiel vergleichen wir eine Reihe von Variablen, die NULL-Werte zulassen, mit auf NULL festleg baren Eigenschaften in der Entität, z. b. SupplierID und UnitPrice. Der generierte SQL-Wert für diese Abfrage fragt, ob der Parameterwert mit dem Spaltenwert übereinstimmt oder ob der Parameter und die Spaltenwerte NULL sind. Dadurch wird die Art und Weise ausgeblendet, wie der Datenbankserver NULL-Werte verarbeitet und eine konsistente C-NULL-Funktion für \# verschiedene Datenbankanbieter bereitstellt. Auf der anderen Seite ist der generierte Code etwas kompliziert und funktioniert möglicherweise nicht gut, wenn die Anzahl der Vergleiche in der WHERE-Anweisung der Abfrage zu einer hohen Zahl wächst.

Eine Möglichkeit, diese Situation zu behandeln, besteht in der Verwendung der NULL-Semantik für die Datenbank. Beachten Sie, dass dies möglicherweise anders als die Semantik des C-NULL-Werts verhält, \# da Entity Framework nun einfacheres SQL generieren, das die Verarbeitung von NULL-Werten durch die Datenbank-Engine ermöglicht Die NULL-Semantik der Datenbank kann pro Kontext mit einer einzelnen Konfigurationszeile für die Kontext Konfiguration aktiviert werden:

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

Bei kleinen bis mittelgroßen Abfragen wird bei Verwendung der NULL-Semantik der Datenbank keine spürbare Leistungsverbesserung angezeigt, aber der Unterschied wird bei Abfragen mit einer großen Anzahl möglicher NULL-Vergleiche deutlicher.

In der obigen Beispiel Abfrage war der Leistungsunterschied in einem in einer kontrollierten Umgebung laufenden Mikrobenchmark kleiner als 2%.

### <a name="95-async"></a>9,5 Async

In Entity Framework 6 wurde die Unterstützung von asynchronen Vorgängen bei Ausführung unter .NET 4,5 oder höher eingeführt. In den meisten Fällen profitieren Anwendungen mit e/a-bezogenen Konflikten am meisten von der Verwendung von asynchronen Abfrage-und Speicher Vorgängen. Wenn Ihre Anwendung von e/a-Konflikten nicht beeinträchtigt wird, wird die Verwendung von Async in den meisten Fällen synchron ausgeführt, und das Ergebnis wird in der gleichen Zeit wie ein synchroner Aufruf zurückgegeben, oder im schlimmsten Fall wird die Ausführung auf eine asynchrone Aufgabe verzögert, und der Abschluss des Szenarios wird zusätzlicher Zeitaufwand hinzugefügt.

Informationen dazu, wie die asynchrone Programmierung funktioniert, um zu entscheiden, ob die Leistung Ihrer Anwendung durch Async verbessert wird, finden Sie unter [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx) . Weitere Informationen zur Verwendung von asynchronen Vorgängen auf Entity Framework finden Sie unter asynchrone [Abfrage und Speicherung](xref:ef6/fundamentals/async
).

### <a name="96-ngen"></a>9,6 ngen

Entity Framework 6 ist nicht in der Standardinstallation von .NET Framework enthalten. Daher sind die Entity Framework-Assemblys standardmäßig nicht ngen, was bedeutet, dass der gesamte Entity Framework Code denselben jitten Kosten wie jede andere MSIL-Assembly unterliegt. Dies kann die F5-Umgebung bei der Entwicklung und dem Kaltstart der Anwendung in der Produktionsumgebung beeinträchtigen. Um die CPU-und Arbeitsspeicher Kosten zu verringern, empfiehlt es sich, die Entity Framework Abbilder nach Bedarf zu ngen. Weitere Informationen zum Verbessern der Startleistung von Entity Framework 6 mit Ngen finden Sie unter Verbessern der [Startleistung mit Ngen](xref:ef6/fundamentals/performance/ngen).

### <a name="97-code-first-versus-edmx"></a>9,7 Code First im Vergleich zu EDMX

Entity Framework Gründe für das Problem mit dem Impedance-Konflikt zwischen der objektorientierten Programmierung und relationalen Datenbanken, indem eine Darstellung des konzeptionellen Modells (Objekte) im Speicher, das Speicher Schema (die Datenbank) und eine Zuordnung zwischen den beiden Datenbanken vorhanden sind. Diese Metadaten werden als "Entity Data Model" oder "EDM" bezeichnet. Von diesem EDM leiten Entity Framework die Sichten zum Roundtrip von Daten aus den Objekten im Arbeitsspeicher in die Datenbank und zurück.

Wenn Entity Framework mit einer EDMX-Datei verwendet wird, die das konzeptionelle Modell, das Speicher Schema und die Zuordnung formal angibt, muss in der Modell Lade Phase nur überprüft werden, ob das EDM korrekt ist (z. b. Stellen Sie sicher, dass keine Zuordnungen fehlen). generieren Sie dann die Sichten, und überprüfen Sie dann die Sichten, und lassen Sie diese Metadaten dann verwenden. Nur dann kann eine Abfrage ausgeführt oder neue Daten im Datenspeicher gespeichert werden.

Der Code First Ansatz ist im Wesentlichen ein anspruchsvoller Entity Data Model Generator. Der Entity Framework muss ein EDM aus dem bereitgestellten Code ergeben. Dies geschieht durch Analysieren der im Modell beteiligten Klassen, Anwenden von Konventionen und Konfigurieren des Modells über die fließende API. Nachdem das EDM erstellt wurde, verhält sich der Entity Framework im Wesentlichen wie eine EDMX-Datei im Projekt. Daher wird durch die Erstellung des Modells aus Code First eine zusätzliche Komplexität hinzugefügt, die im Vergleich zu edmx zu einer langsameren Startzeit für die Entity Framework führt. Die Kosten sind vollständig von der Größe und Komplexität des Modells abhängig, das erstellt wird.

Wenn Sie sich für die Verwendung von edmx im Vergleich zu Code First entscheiden, ist es wichtig zu wissen, dass die durch Code First eingeführte Flexibilität die Kosten für die erstmalige Erstellung des Modells erhöht. Wenn Ihre Anwendung die Kosten für diese erstmalige Auslastung widerrufen kann, ist Code First in der Regel die bevorzugte Methode.

## <a name="10-investigating-performance"></a>10 Untersuchung der Leistung

### <a name="101-using-the-visual-studio-profiler"></a>10,1 Verwenden von Visual Studio Profiler

Wenn Sie Leistungsprobleme mit dem Entity Framework haben, können Sie einen Profiler wie den in Visual Studio integrierten Profiler verwenden, um zu sehen, wo Ihre Anwendung Ihre Zeit verbringt. Dies ist das Tool, das wir zum Generieren der Kreis Diagramme im Blogbeitrag "untersuchen der Leistung des ADO.NET Entity Framework-Part 1"-Blogbeitrags () verwendet haben, in dem \<https://docs.microsoft.com/archive/blogs/adonet/exploring-the-performance-of-the-ado-net-entity-framework-part-1> gezeigt wird, wo Entity Framework seine Zeit während kalter und warmer Abfragen verbringt.

Der Blogbeitrag "Profilerstellung Entity Framework mit dem Visual Studio 2010 Profiler", der vom Kunden Beratungs Team für Daten und Modellierung verfasst wurde, zeigt ein reales Beispiel dafür, wie der Profiler zum Untersuchen eines Leistungs Problems verwendet   \<https://docs.microsoft.com/archive/blogs/dmcat/profiling-entity-framework-using-the-visual-studio-2010-profiler> wurde. Dieser Beitrag wurde für eine Windows-Anwendung geschrieben. Wenn Sie ein Profil für eine Webanwendung erstellen müssen, funktionieren die Tools für Windows Performance Recorder (WPR) und Windows Performance Analyzer (WPA) möglicherweise besser als die Arbeit in Visual Studio. WPR und WPA sind Teil des Windows Performance Toolkit, das im Windows Assessment and Deployment Kit () enthalten ist [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982) .

### <a name="102-applicationdatabase-profiling"></a>10,2 Anwendung/Datenbankprofil Erstellung

Tools wie der in Visual Studio integrierte Profiler zeigen Ihnen, wo Ihre Anwendung Zeit verbringt.Ein weiterer Profiler ist verfügbar, der eine dynamische Analyse ihrer ausgeführten Anwendung ausführt, je nach Anforderungen in der Produktions-oder der Präproduktionsumgebung, und nach häufigen Fehlerquellen und Antimustern des Datenbankzugriffs sucht.

Zwei kommerziell verfügbare Profiler sind der Entity Framework Profiler ( \<http://efprof.com> ) und ormprofiler ( \<http://ormprofiler.com> ).

Wenn es sich bei Ihrer Anwendung um eine MVC-Anwendung handelt, die Code First verwendet, können Sie den stackexchange-miniprofiler verwenden. Scott Hanselman beschreibt dieses Tool in seinem Blog unter: \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx> .

Weitere Informationen zur Profilerstellung für die Datenbankaktivität Ihrer Anwendung finden Sie im MSDN Magazine-Artikel der Julie Lerman [im Entity Framework](https://msdn.microsoft.com/magazine/gg490349.aspx).

### <a name="103-database-logger"></a>10,3 Daten Bank Protokollierung

Wenn Sie Entity Framework 6 verwenden, sollten Sie auch die Verwendung der integrierten Protokollierungsfunktionen in Erwägung gezogen. Die Daten Bank Eigenschaft des Kontexts kann angewiesen werden, die Aktivität über eine einfache einzeilige Konfiguration zu protokollieren:

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

In diesem Beispiel wird die Datenbankaktivität in der Konsole protokolliert, die Log-Eigenschaft kann jedoch so konfiguriert werden, dass jeder beliebige Aktions Zeichen folgen Delegat aufgerufen wird &lt; &gt; .

Wenn Sie die Daten Bank Protokollierung ohne Neukompilierung aktivieren möchten, und Sie Entity Framework 6,1 oder höher verwenden, fügen Sie einen Interceptor in der web.config-oder app.config-Datei Ihrer Anwendung hinzu.

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

Weitere Informationen zum Hinzufügen der Protokollierung ohne Neukompilieren finden Sie unter \<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/> .

## <a name="11-appendix"></a>11 Anhang

### <a name="111-a-test-environment"></a>11,1 A. Test Umgebung

In dieser Umgebung wird eine 2-Computer-Einrichtung mit der Datenbank auf einem anderen Computer als die Client Anwendung verwendet. Computer befinden sich im gleichen Rack, sodass die Netzwerk Latenz relativ gering, aber realistischer ist als eine Umgebung mit einem einzigen Computer.

#### <a name="1111-app-server"></a>11.1.1-App-Server

##### <a name="11111-software-environment"></a>11.1.1.1-Software Umgebung

-   Entity Framework 4-Software Umgebung
    -   Betriebssystem Name: Windows Server 2008 R2 Enterprise SP1.
    -   Visual Studio 2010 – Ultimate.
    -   Visual Studio 2010 SP1 (nur für einige Vergleiche).
-   Entity Framework 5-und 6-Software Umgebung
    -   Betriebssystem Name: Windows 8.1 Enterprise
    -   Visual Studio 2013 – Ultimate.

##### <a name="11112-hardware-environment"></a>11.1.1.2-Hardware Umgebung

-   Dual Prozessor: Intel (r) Xeon (r) CPU L5520 W3530 @ 2,27 GHz, 2261 Mhz8 GHz, 4 Kerne (e), 84 logische Prozessoren.
-   2412 GB ramram.
-   136 GB SCSI250GB SATA 7200 rpm, 3 GB/s, in 4 Partitionen aufgeteilt.

#### <a name="1112-db-server"></a>11.1.2 DB-Server

##### <a name="11121-software-environment"></a>11.1.2.1-Software Umgebung

-   Betriebssystem Name: Windows Server 2008 r 28,1 Enterprise SP1.
-   SQL Server 2008 R22012.

##### <a name="11122-hardware-environment"></a>11.1.2.2-Hardware Umgebung

-   Einzelner Prozessor: Intel (r) Xeon (r) CPU L5520 @ 2,27 GHz, 2261 mhzes-1620 0 @ 3,60 GHz, 4 Kerne (e), 8 logische Prozessoren.
-   824 GB ramram.
-   465 GB ATA500GB SATA 7200 rpm, 6-GB/s, in 4 Partitionen aufgeteilt.

### <a name="112-b-query-performance-comparison-tests"></a>11,2 B. Abfragen von Leistungs Vergleichstests

Das Northwind-Modell wurde verwendet, um diese Tests auszuführen. Es wurde mit dem Entity Framework-Designer aus der Datenbank generiert. Anschließend wurde der folgende Code verwendet, um die Leistung der Abfrage Ausführungs Optionen zu vergleichen:

``` csharp
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.Common;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Data.Objects;
using System.Linq;

namespace QueryComparison
{
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
                );

        public IQueryable<Product> InvokeProductsForCategoryCQ(string categoryName)
        {
            return productsForCategoryCQ(this, categoryName);
        }
    }

    public class QueryTypePerfComparison
    {
        private static string entityConnectionStr = @"metadata=res://*/Northwind.csdl|res://*/Northwind.ssdl|res://*/Northwind.msl;provider=System.Data.SqlClient;provider connection string='data source=.;initial catalog=Northwind;integrated security=True;multipleactiveresultsets=True;App=EntityFramework'";

        public void LINQIncludingContextCreation()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTracking()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                context.Products.MergeOption = MergeOption.NoTracking;

                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void CompiledQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                var q = context.InvokeProductsForCategoryCQ("Beverages");
                q.ToList();
            }
        }

        public void ObjectQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
                products.ToList();
            }
        }

        public void EntityCommand()
        {
            using (EntityConnection eConn = new EntityConnection(entityConnectionStr))
            {
                eConn.Open();
                EntityCommand cmd = eConn.CreateCommand();
                cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

                using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
                {
                    List<Product> productsList = new List<Product>();
                    while (reader.Read())
                    {
                        DbDataRecord record = (DbDataRecord)reader.GetValue(0);

                        // 'materialize' the product by accessing each field and value. Because we are materializing products, we won't have any nested data readers or records.
                        int fieldCount = record.FieldCount;

                        // Treat all products as Product, even if they are the subtype DiscontinuedProduct.
                        Product product = new Product();  

                        product.ProductID = record.GetInt32(0);
                        product.ProductName = record.GetString(1);
                        product.SupplierID = record.GetInt32(2);
                        product.CategoryID = record.GetInt32(3);
                        product.QuantityPerUnit = record.GetString(4);
                        product.UnitPrice = record.GetDecimal(5);
                        product.UnitsInStock = record.GetInt16(6);
                        product.UnitsOnOrder = record.GetInt16(7);
                        product.ReorderLevel = record.GetInt16(8);
                        product.Discontinued = record.GetBoolean(9);

                        productsList.Add(product);
                    }
                }
            }
        }

        public void ExecuteStoreQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectResult<Product> beverages = context.ExecuteStoreQuery<Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Database.SqlQuery\<QueryComparison.DbC.Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbSet()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Products.SqlQuery(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void LINQIncludingContextCreationDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTrackingDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var q = context.Products.AsNoTracking().Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }
    }
}
```

### <a name="113-c-navision-model"></a>11,3 C. Navision-Modell

Die Navision-Datenbank ist eine große Datenbank, die für die Demo von Microsoft Dynamics – NAV verwendet wird. Das generierte konzeptionelle Modell enthält 1005 Entitätenmengen und 4227 Zuordnungs Sätze. Das im Test verwendete Modell ist "Flat" – Es wurde keine Vererbung hinzugefügt.

#### <a name="1131-queries-used-for-navision-tests"></a>11.3.1-Abfragen, die für Navision-Tests verwendet werden

Die mit dem Navision-Modell verwendete Abfrage Liste enthält drei Kategorien Entity SQL Abfragen:

##### <a name="11311-lookup"></a>11.3.1.1-Suche

Eine einfache Suchabfrage ohne Aggregationen

-   Anzahl: 16232
-   Beispiel:

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a>11.3.1.2 singleaggregating

Eine normale BI-Abfrage mit mehreren Aggregationen, aber keine Teilergebnisse (einzelne Abfrage)

-   Anzahl: 2313
-   Beispiel:

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

Wenn MDF \_ sessionlogin \_ time \_ Max () im Modell wie folgt definiert ist:

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a>11.3.1.3 aggregatingsubsummen

Eine BI-Abfrage mit Aggregationen und Teilsummen (über Union all)

-   Anzahl: 178
-   Beispiel:

``` xml
  <Query complexity="AggregatingSubtotals">
    <CommandText>
using NavisionFK;
function AmountConsumed(entities Collection([CRONUS_International_Ltd__Zone])) as
(
    Edm.Sum(select value N.Block_Movement FROM entities as E, E.CRONUS_International_Ltd__Bin as N)
)
function AmountConsumed(P1 Edm.Int32) as
(
    AmountConsumed(select value e from NavisionFKContext.CRONUS_International_Ltd__Zone as e where e.Zone_Ranking = P1)
)
----------------------------------------------------------------------------------------------------------------------
(
    select top(10) Zone_Ranking, Cross_Dock_Bin_Zone, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking, E.Cross_Dock_Bin_Zone
)
union all
(
    select top(10) Zone_Ranking, Cast(null as Edm.Byte) as P2, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking
)
union all
{
    Row(Cast(null as Edm.Int32) as P1, Cast(null as Edm.Byte) as P2, AmountConsumed(select value E
                                                                         from NavisionFKContext.CRONUS_International_Ltd__Zone as E
                                                                         where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed))
}</CommandText>
    <Parameters>
      <Parameter Name="MinAmountConsumed" DbType="Int32" Value="10000" />
    </Parameters>
  </Query>
```
