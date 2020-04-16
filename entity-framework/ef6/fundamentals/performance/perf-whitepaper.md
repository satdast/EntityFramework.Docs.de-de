---
title: Leistungsüberlegungen für EF4, EF5 und EF6 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: 0ece383bb5083b41c7a2636c009473333af6d3e2
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434338"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a>Leistungsüberlegungen für EF 4, 5 und 6
Von David Obando, Eric Dettinger und anderen

Veröffentlicht: April 2012

Letzte Aktualisierung: Mai 2014

------------------------------------------------------------------------

## <a name="1-introduction"></a>1. Einführung

Objektrelationale Mapping-Frameworks sind eine bequeme Möglichkeit, eine Abstraktion für den Datenzugriff in einer objektorientierten Anwendung bereitzustellen. Für .NET-Anwendungen ist Microsofts empfohlenes O/RM Entity Framework. Mit jeder Abstraktion kann Leistung jedoch zum Problem werden.

Dieses Whitepaper wurde geschrieben, um die Leistungsüberlegungen bei der Entwicklung von Anwendungen mit Entity Framework aufzuzeigen, Entwicklern eine Vorstellung von den internen Entity Framework-Algorithmen zu geben, die sich auf die Leistung auswirken können, und um Tipps für die Untersuchung und Verbesserung der Leistung in ihren Anwendungen bereitzustellen, die Entity Framework verwenden. Es gibt eine Reihe von guten Themen zur Leistung, die bereits im Web verfügbar sind, und wir haben auch versucht, auf diese Ressourcen zu verweisen, wo immer möglich.

Leistung ist ein heikles Thema. Dieses Whitepaper dient als Ressource, mit der Sie leistungsbezogene Entscheidungen für Ihre Anwendungen treffen können, die Entity Framework verwenden. Wir haben einige Testmetriken zum Nachweis der Leistung aufgenommen, aber diese Metriken sind nicht als absolute Indikatoren für die Leistung gedacht, die Sie in Ihrer Anwendung sehen werden.

Aus praktischen Gründen geht dieses Dokument davon aus, dass Entity Framework 4 unter .NET 4.0 und Entity Framework 5 und 6 unter .NET 4.5 ausgeführt wird. Viele der für Entity Framework 5 vorgenommenen Leistungsverbesserungen befinden sich in den Kernkomponenten, die mit .NET 4.5 ausgeliefert werden.

Entity Framework 6 ist eine Out-of-Band-Version und hängt nicht von den Entity Framework-Komponenten ab, die mit .NET ausgeliefert werden. Entity Framework 6 funktioniert sowohl auf .NET 4.0 als auch auf .NET 4.5 und kann denjenigen, die kein Upgrade von .NET 4.0 durchgeführt haben, aber die neuesten Entity Framework-Bits in ihrer Anwendung wünschen, einen großen Leistungsvorteil bieten. Wenn in diesem Dokument Entity Framework 6 erwähnt wird, bezieht es sich auf die neueste Version, die zum Zeitpunkt dieses Schreibens verfügbar war: Version 6.1.0.

## <a name="2-cold-vs-warm-query-execution"></a>2. Cold vs. Warm Query Execution

Wenn zum ersten Mal abfragen für ein bestimmtes Modell gestellt wird, macht Entity Framework viel Arbeit hinter den Kulissen, um das Modell zu laden und zu validieren. Wir bezeichnen diese erste Abfrage häufig als "kalte" Abfrage.Weitere Abfragen für ein bereits geladenes Modell werden als "warme" Abfragen bezeichnet und sind viel schneller.

Lassen Sie uns eine aktuelle Ansicht darüber werfen, wo zeitfürstduzt wird, wenn eine Abfrage mit Entity Framework ausgeführt wird, und sehen Sie, wo sich die Dinge in Entity Framework 6 verbessern.

**Erste Abfrageausführung – kalte Abfrage**

| Codebenutzerschreibvorgänge                                                                                     | Aktion                    | EF4-Leistungsauswirkungen                                                                                                                                                                                                                                                                                                                                                                                                        | EF5-Leistungsauswirkungen                                                                                                                                                                                                                                                                                                                                                                                                                                                    | EF6-Auswirkungen auf die Leistung                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Kontexterstellung          | Medium                                                                                                                                                                                                                                                                                                                                                                                                                        | Medium                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Erstellung von Abfrageausdrucksausdrücken | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                           | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | LINQ-Abfrageausführung      | - Metadaten-Laden: Hoch, aber zwischengespeichert <br/> - Ansichtsgenerierung: Potenziell sehr hoch, aber zwischengespeichert <br/> - Parameterauswertung: Mittel <br/> - Abfrageübersetzung: Mittel <br/> - Materializer-Generierung: Mittel, aber zwischengespeichert <br/> - Datenbankabfrageausführung: Potenziell hoch <br/> + Connection.Open <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Objektmaterialisierung: Medium <br/> - Identitätssuche: Mittel | - Metadaten-Laden: Hoch, aber zwischengespeichert <br/> - Ansichtsgenerierung: Potenziell sehr hoch, aber zwischengespeichert <br/> - Parameterauswertung: Niedrig <br/> - Abfrageübersetzung: Mittel, aber zwischengespeichert <br/> - Materializer-Generierung: Mittel, aber zwischengespeichert <br/> - Datenbankabfrageausführung: Potenziell hoch (bessere Abfragen in einigen Situationen) <br/> + Connection.Open <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Objektmaterialisierung: Medium <br/> - Identitätssuche: Mittel | - Metadaten-Laden: Hoch, aber zwischengespeichert <br/> - Ansichtsgenerierung: Mittel, aber zwischengespeichert <br/> - Parameterauswertung: Niedrig <br/> - Abfrageübersetzung: Mittel, aber zwischengespeichert <br/> - Materializer-Generierung: Mittel, aber zwischengespeichert <br/> - Datenbankabfrageausführung: Potenziell hoch (bessere Abfragen in einigen Situationen) <br/> + Connection.Open <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Objektmaterialisierung: Mittel (schneller als EF5) <br/> - Identitätssuche: Mittel |
| `}`                                                                                                  | Connection.Close          | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                           | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


**Zweite Abfrageausführung – warme Abfrage**

| Codebenutzerschreibvorgänge                                                                                     | Aktion                    | EF4-Leistungsauswirkungen                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | EF5-Leistungsauswirkungen                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | EF6-Auswirkungen auf die Leistung                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Kontexterstellung          | Medium                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Medium                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Erstellung von Abfrageausdrucksausdrücken | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | LINQ-Abfrageausführung      | - Metadaten ~~laden~~ Suche: ~~Hoch, aber zwischengespeichert~~ Low <br/> - ~~Ansichtsgenerierungssuche:~~ ~~Potenziell sehr hoch, aber zwischengespeichert~~ Low <br/> - Parameterauswertung: Mittel <br/> - ~~Abfrage-Übersetzungssuche:~~ Mittel <br/> - ~~Materializer-Generierungssuche:~~ ~~Mittel, aber zwischengespeichert~~ Low <br/> - Datenbankabfrageausführung: Potenziell hoch <br/> + Connection.Open <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Objektmaterialisierung: Medium <br/> - Identitätssuche: Mittel | - Metadaten ~~laden~~ Suche: ~~Hoch, aber zwischengespeichert~~ Low <br/> - ~~Ansichtsgenerierungssuche:~~ ~~Potenziell sehr hoch, aber zwischengespeichert~~ Low <br/> - Parameterauswertung: Niedrig <br/> - ~~Abfrage-Übersetzungssuche:~~ ~~Mittel, aber zwischengespeichert~~ Low <br/> - ~~Materializer-Generierungssuche:~~ ~~Mittel, aber zwischengespeichert~~ Low <br/> - Datenbankabfrageausführung: Potenziell hoch (bessere Abfragen in einigen Situationen) <br/> + Connection.Open <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Objektmaterialisierung: Medium <br/> - Identitätssuche: Mittel | - Metadaten ~~laden~~ Suche: ~~Hoch, aber zwischengespeichert~~ Low <br/> - Ansicht ~~Segeneration~~ Lookup: ~~Mittel, aber zwischengespeichert~~ Low <br/> - Parameterauswertung: Niedrig <br/> - ~~Abfrage-Übersetzungssuche:~~ ~~Mittel, aber zwischengespeichert~~ Low <br/> - ~~Materializer-Generierungssuche:~~ ~~Mittel, aber zwischengespeichert~~ Low <br/> - Datenbankabfrageausführung: Potenziell hoch (bessere Abfragen in einigen Situationen) <br/> + Connection.Open <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Objektmaterialisierung: Mittel (schneller als EF5) <br/> - Identitätssuche: Mittel |
| `}`                                                                                                  | Connection.Close          | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Niedrig                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


Es gibt mehrere Möglichkeiten, die Leistungskosten von kalten und warmen Abfragen zu reduzieren, und wir werden uns diese im folgenden Abschnitt ansehen. Insbesondere werden wir die Senkung der Kosten für das Laden von Modell in Kaltabfragen mithilfe von vorgenerierten Ansichten untersuchen, die dazu beitragen sollen, Leistungsprobleme zu lindern, die bei der Ansichtsgenerierung auftreten. Für warme Abfragen behandeln wir die Zwischenspeicherung von Abfrageplanen, keine Nachverfolgungsabfragen und verschiedene Abfrageausführungsoptionen.

### <a name="21-what-is-view-generation"></a>2.1 Was ist View Generation?

Um zu verstehen, was Ansichtsgenerierung ist, müssen wir zuerst verstehen, was "Mapping Views" sind. Zuordnungsansichten sind ausführbare Darstellungen der Transformationen, die in der Zuordnung für jeden Entitätssatz und jede Zuordnung angegeben sind. Intern nehmen diese Zuordnungsansichten die Form von CQTs (kanonische Abfragestrukturen) an. Es gibt zwei Arten von Zuordnungsansichten:

-   Abfrageansichten: Diese stellen die Transformation dar, die erforderlich ist, um vom Datenbankschema zum konzeptionellen Modell zu wechseln.
-   Aktualisierungsansichten: Diese stellen die Transformation dar, die erforderlich ist, um vom konzeptionellen Modell zum Datenbankschema zu wechseln.

Beachten Sie, dass sich das konzeptionelle Modell auf verschiedene Weise vom Datenbankschema unterscheiden kann. Beispielsweise kann eine einzelne Tabelle verwendet werden, um die Daten für zwei verschiedene Entitätstypen zu speichern. Vererbungs- und nicht triviale Zuordnungen spielen eine Rolle bei der Komplexität der Zuordnungsansichten.

Der Prozess der Berechnung dieser Ansichten basierend auf der Spezifikation der Zuordnung ist das, was wir Ansichtsgenerierung nennen. Die Ansichtsgenerierung kann entweder dynamisch erfolgen, wenn ein Modell geladen wird, oder zur Buildzeit mithilfe von "vorgenerierten Ansichten". Letztere werden in Form von Entity SQL-Anweisungen\# in eine C- oder VB-Datei serialisiert.

Wenn Ansichten generiert werden, werden sie ebenfalls überprüft. Aus Performance-Sicht ist der überwiegende Teil der Kosten für die Ansichtsgenerierung tatsächlich die Validierung der Ansichten, die sicherstellt, dass die Verbindungen zwischen den Entitäten sinnvoll sind und die richtige Kardinalität für alle unterstützten Vorgänge aufweisen.

Wenn eine Abfrage über einen Entitätssatz ausgeführt wird, wird die Abfrage mit der entsprechenden Abfrageansicht kombiniert, und das Ergebnis dieser Komposition wird über den Plancompiler ausgeführt, um die Darstellung der Abfrage zu erstellen, die der Sicherungsspeicher verstehen kann. Für SQL Server ist das Endergebnis dieser Kompilierung eine T-SQL SELECT-Anweisung. Wenn eine Aktualisierung über einen Entitätssatz zum ersten Mal durchgeführt wird, wird die Aktualisierungsansicht durch einen ähnlichen Prozess ausgeführt, um sie in DML-Anweisungen für die Zieldatenbank umzuwandeln.

### <a name="22-factors-that-affect-view-generation-performance"></a>2.2 Faktoren, die die Leistung der Ansichtsgenerierung beeinflussen

Die Leistung des Ansichtsgenerierungsschritts hängt nicht nur von der Größe des Modells ab, sondern auch davon, wie eng das Modell verbunden ist. Wenn zwei Entitäten über eine Vererbungskette oder einen Verein verbunden sind, sollen sie verbunden sein. Wenn zwei Tabellen über einen Fremdschlüssel verbunden sind, sind sie ebenfalls verbunden. Wenn die Anzahl der verbundenen Entitäten und Tabellen in Ihren Schemas zunimmt, steigen die Kosten für die Ansichtsgenerierung.

Der Algorithmus, den wir zum Generieren und Validieren von Ansichten verwenden, ist im schlimmsten Fall exponentiell, obwohl wir einige Optimierungen verwenden, um dies zu verbessern. Die größten Faktoren, die sich negativ auf die Leistung zu auswirken scheinen, sind:

-   Modellgröße, bezogen auf die Anzahl der Entitäten und die Anzahl der Zuordnungen zwischen diesen Entitäten.
-   Modellkomplexität, insbesondere Vererbung mit einer großen Anzahl von Typen.
-   Verwenden unabhängiger Vereinigungen anstelle von Foreign Key-Verbänden.

Bei kleinen, einfachen Modellen können die Kosten klein genug sein, um sich nicht an die Verwendung von vorgenerierten Ansichten zu kümmern. Mit zunehmender Modellgröße und -komplexität stehen mehrere Optionen zur Verfügung, um die Kosten für die Ansichtsgenerierung und -validierung zu senken.

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a>2.3 Verwenden von vorgenerierten Ansichten zur Verringerung der Modelllastzeit

Ausführliche Informationen zur Verwendung vorgenerierter Ansichten zu Entity Framework 6 finden Sie [unter Vorgenerierte Zuordnungsansichten](~/ef6/fundamentals/performance/pre-generated-views.md)

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a>2.3.1 Vorgenerierte Ansichten mit der Entity Framework Power Tools Community Edition

Sie können die [Entity Framework 6 Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) verwenden, um Ansichten von EDMX- und Code First-Modellen zu generieren, indem Sie mit der rechten Maustaste auf die Modellklassendatei klicken und das Menü Entity Framework verwenden, um "Ansichten generieren" auszuwählen. Die Entity Framework Power Tools Community Edition funktioniert nur für DbContext-abgeleitete Kontexte.

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a>2.3.2 Verwenden von vorgenerierten Ansichten mit einem von EDMGen erstellten Modell

EDMGen ist ein Dienstprogramm, das mit .NET ausgeliefert wird und mit Entity Framework 4 und 5 arbeitet, jedoch nicht mit Entity Framework 6. Mit EDMGen können Sie eine Modelldatei, die Objektebene und die Ansichten aus der Befehlszeile generieren. Eine der Ausgaben ist eine Views-Datei in Ihrer Sprache\#Ihrer Wahl, VB oder C . Dies ist eine Codedatei, die Entity SQL-Ausschnitte für jeden Entitätssatz enthält. Um vorgenerierte Ansichten zu aktivieren, fügen Sie einfach die Datei in Ihr Projekt ein.

Wenn Sie die Schemadateien für das Modell manuell bearbeiten, müssen Sie die Ansichtsdatei erneut generieren. Sie können dies tun, indem Sie EDMGen mit dem Flag **/mode:ViewGeneration** ausführen.

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a>2.3.3 Verwendung von vorgenerierten Ansichten mit einer EDMX-Datei

Sie können EDMGen auch verwenden, um Ansichten für eine EDMX-Datei zu generieren - das zuvor referenzierte MSDN-Thema beschreibt, wie Sie dazu ein Pre-Build-Ereignis hinzufügen - aber dies ist kompliziert und es gibt einige Fälle, in denen dies nicht möglich ist. Im Allgemeinen ist es einfacher, eine T4-Vorlage zu verwenden, um die Ansichten zu generieren, wenn sich Ihr Modell in einer edmx-Datei befindet.

Der ADO.NET Teamblog hat einen Beitrag, der beschreibt, wie \< https://docs.microsoft.com/archive/blogs/adonet/how-to-use-a-t4-template-for-view-generation>)eine T4-Vorlage für die Ansichtsgenerierung verwendet wird ( . Dieser Beitrag enthält eine Vorlage, die heruntergeladen und Ihrem Projekt hinzugefügt werden kann. Die Vorlage wurde für die erste Version von Entity Framework geschrieben, daher ist es nicht garantiert, dass sie mit den neuesten Versionen von Entity Framework funktionieren. Sie können jedoch einen aktuelleren Satz von Ansichtsgenerierungsvorlagen für Entity Framework 4 und 5 aus der Visual Studio-Galerie herunterladen:

-   VB.NET:\<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d>
-   C\#:\<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d>

Wenn Sie Entity Framework 6 verwenden, können Sie die T4-Vorlagen \< http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>für die Ansichtsgenerierung aus der Visual Studio-Galerie unter abrufen.

### <a name="24-reducing-the-cost-of-view-generation"></a>2.4 Senkung der Kosten für die Ansichtsgenerierung

Durch die Verwendung vorgenerierter Ansichten werden die Kosten für die Ansichtsgenerierung vom Laden des Modells (Laufzeit) auf die Entwurfszeit verschoben. Während dies die Startleistung zur Laufzeit verbessert, werden Sie immer noch die Schmerzen der Ansichtsgenerierung erleben, während Sie sich entwickeln. Es gibt mehrere zusätzliche Tricks, die dazu beitragen können, die Kosten für die Ansichtsgenerierung zu reduzieren, sowohl zur Kompilierungszeit als auch zur Laufzeit.

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a>2.4.1 Verwendung von Fremdschlüsselzuordnungen zur Senkung der Kosten für die Ansichtsgenerierung

Wir haben eine Reihe von Fällen gesehen, in denen die Umstellung der Assoziationen im Modell von unabhängigen Vereinigungen auf Fremdschlüsselverbände die Zeit, die für die Schaffung von Aussichten aufgewendet wurde, drastisch verbessert hat.

Um diese Verbesserung zu demonstrieren, haben wir zwei Versionen des Navision-Modells mit EDMGen generiert. *Hinweis: Siehe Anhang C für eine Beschreibung des Navision-Modells.* Das Navision-Modell ist für diese Übung aufgrund seiner sehr großen Anzahl von Entitäten und Beziehungen zwischen ihnen interessant.

Eine Version dieses sehr großen Modells wurde mit Foreign Keys Associations und die andere mit Unabhängigen Verbänden generiert. Wir haben dann eine Zeitzeit für die Generierung der Ansichten für jedes Modell gedauert. Entity Framework 5 test verwendete die GenerateViews()-Methode der Klasse EntityViewGenerator, um die Ansichten zu generieren, während der Entity Framework 6-Test die GenerateViews()-Methode der Klasse StorageMappingItemCollection verwendete. Dies aufgrund der Codeumstrukturierung, die in der Entity Framework 6-Codebasis aufgetreten ist.

Mit Entity Framework 5 dauerte die Ansichtsgenerierung für das Modell mit Fremdschlüsseln 65 Minuten in einem Laborcomputer. Es ist nicht bekannt, wie lange es gedauert hätte, die Ansichten für das Modell zu generieren, das unabhängige Zuordnungen verwendet hat. Wir haben den Test über einen Monat lang ausgeführt, bevor der Computer in unserem Labor neu gestartet wurde, um monatliche Updates zu installieren.

Mit Entity Framework 6 dauerte die Ansichtsgenerierung für das Modell mit Fremdschlüsseln 28 Sekunden auf demselben Lab-Computer. Die Ansichtsgenerierung für das Modell, das unabhängige Zuordnungen verwendet, dauerte 58 Sekunden. Die Verbesserungen an Entity Framework 6 für den Ansichtsgenerierungscode bedeuten, dass viele Projekte keine vorgenerierten Ansichten benötigen, um schnellere Startzeiten zu erhalten.

Es ist wichtig zu beachten, dass vorgenerierte Ansichten in Entity Framework 4 und 5 mit EDMGen oder den Entity Framework Power Tools durchgeführt werden können. Für Entity Framework 6 kann die Ansichtsgenerierung über die Entity Framework Power Tools oder programmgesteuert erfolgen, wie unter [Vorgenerierte Zuordnungsansichten](~/ef6/fundamentals/performance/pre-generated-views.md)beschrieben.

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a>2.4.1.1 Verwendung von Fremdschlüsseln anstelle unabhängiger Vereinigungen

Wenn Sie EDMGen oder den Entity Designer in Visual Studio verwenden, erhalten Sie standardmäßig FKs, und es wird nur ein einzelnes Kontrollkästchen oder Befehlszeilenflag benötigt, um zwischen FKs und IAs zu wechseln.

Wenn Sie über ein großes Code First-Modell verfügen, hat die Verwendung unabhängiger Zuordnungen den gleichen Effekt auf die Ansichtsgenerierung. Sie können diese Auswirkungen vermeiden, indem Sie Fremdschlüsseleigenschaften für die Klassen für Ihre abhängigen Objekte einschließen, obwohl einige Entwickler dies als Umweltverschmutzung ihres Objektmodells betrachten. Weitere Informationen zu diesem Thema \< http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>finden Sie unter .

| Bei der Verwendung      | Aktion                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Entity Designer | Stellen Sie nach dem Hinzufügen einer Zuordnung zwischen zwei Entitäten sicher, dass Sie über eine referenzielle Einschränkung verfügen. Referenzielle Einschränkungen weisen Entity Framework an, Fremdschlüssel anstelle von unabhängigen Zuordnungen zu verwenden. Weitere Informationen \< https://docs.microsoft.com/archive/blogs/efdesign/foreign-keys-in-the-entity-framework>finden Sie unter . |
| Edmgen.exe          | Wenn Sie EDMGen zum Generieren Ihrer Dateien aus der Datenbank verwenden, werden Ihre Fremdschlüssel respektiert und dem Modell als solchem hinzugefügt. Weitere Informationen zu den verschiedenen Optionen, [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx)die EDMGen bietet, finden Sie unter .                           |
| Code First      | Informationen zum Einschließen von Fremdschlüsseleigenschaften zu abhängigen Objekten bei der Verwendung von Code First finden Sie im Abschnitt "Beziehungskonvention" im Thema [Code First Conventions.](~/ef6/modeling/code-first/conventions/built-in.md)                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a>2.4.2 Verschieben des Modells in eine separate Baugruppe

Wenn Ihr Modell direkt in das Projekt Ihrer Anwendung einbezogen wird und Sie Ansichten über ein Pre-Build-Ereignis oder eine T4-Vorlage generieren, erfolgt die Ansichtsgenerierung und -validierung, wenn das Projekt neu erstellt wird, auch wenn das Modell nicht geändert wurde. Wenn Sie das Modell in eine separate Baugruppe verschieben und aus dem Projekt Ihrer Anwendung darauf verweisen, können Sie weitere Änderungen an der Anwendung vornehmen, ohne das Projekt, das das Modell enthält, neu erstellen zu müssen.

*Hinweis:*  Wenn Sie das Modell in separate Assemblys verschieben, denken Sie daran, die Verbindungszeichenfolgen für das Modell in die Anwendungskonfigurationsdatei des Clientprojekts zu kopieren.

#### <a name="243-disable-validation-of-an-edmx-based-model"></a>2.4.3 Deaktivieren der Validierung eines edmx-basierten Modells

EDMX-Modelle werden zur Kompilierungszeit validiert, auch wenn das Modell unverändert bleibt. Wenn Ihr Modell bereits validiert wurde, können Sie die Validierung zur Kompilierungszeit unterdrücken, indem Sie die Eigenschaft "Validate on Build" im Eigenschaftenfenster auf false festlegen. Wenn Sie ihre Zuordnung oder Ihr Modell ändern, können Sie die Validierung vorübergehend erneut aktivieren, um ihre Änderungen zu überprüfen.

Beachten Sie, dass Leistungsverbesserungen am Entity Framework Designer für Entity Framework 6 vorgenommen wurden und die Kosten für "Validate on Build" viel niedriger sind als in früheren Versionen des Designers.

## <a name="3-caching-in-the-entity-framework"></a>3 Zwischenspeichern im Entity Framework

Entity Framework verfügt über die folgenden Integrierten-Caching-Formen:

1.  Objektzwischenspeicherung – Der in eine ObjectContext-Instanz integrierte ObjectStateManager verfolgt im Speicher die Objekte, die mit dieser Instanz abgerufen wurden. Dies wird auch als Cache der ersten Ebene bezeichnet.
2.  Abfrageplan-Caching – Wiederverwendung des generierten Speicherbefehls, wenn eine Abfrage mehr als einmal ausgeführt wird.
3.  Metadaten-Caching – Freigabe der Metadaten für ein Modell über verschiedene Verbindungen mit demselben Modell.

Neben den Caches, die EF sofort bereitstellt, kann eine spezielle Art von ADO.NET Datenanbieter, der als Wrapping-Anbieter bezeichnet wird, auch verwendet werden, um Entity Framework um einen Cache für die aus der Datenbank abgerufenen Ergebnisse zu erweitern, die auch als Zwischenspeicherung der zweiten Ebene bezeichnet werden.

### <a name="31-object-caching"></a>3.1 Objektzwischenspeicherung

Wenn eine Entität in den Ergebnissen einer Abfrage zurückgegeben wird, überprüft der ObjectContext standardmäßig, ob eine Entität mit demselben Schlüssel bereits in den ObjectStateManager geladen wurde. Wenn eine Entität mit denselben Schlüsseln bereits vorhanden ist, wird EF sie in die Ergebnisse der Abfrage einbeziehen. Obwohl EF die Abfrage weiterhin für die Datenbank ausgibt, kann dieses Verhalten einen Großteil der Kosten für die mehrfache Materialisierung der Entität umgehen.

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a>3.1.1 Abrufen von Entitäten aus dem Objektcache mithilfe von DbContext Find

Im Gegensatz zu einer regulären Abfrage führt die Find-Methode in DbSet (APIs, die zum ersten Mal in EF 4.1 enthalten sind) eine Suche im Arbeitsspeicher durch, bevor die Abfrage überhaupt für die Datenbank ausgegeben wird. Es ist wichtig zu beachten, dass zwei verschiedene ObjectContext-Instanzen über zwei verschiedene ObjectStateManager-Instanzen verfügen, was bedeutet, dass sie über separate Objektcaches verfügen.

Find verwendet den Primärschlüsselwert, um zu versuchen, eine Entität zu finden, die vom Kontext nachverfolgt wird. Wenn sich die Entität nicht im Kontext befindet, wird eine Abfrage ausgeführt und für die Datenbank ausgewertet, und null wird zurückgegeben, wenn die Entität nicht im Kontext oder in der Datenbank gefunden wird. Beachten Sie, dass Find auch Entitäten zurückgibt, die dem Kontext hinzugefügt, aber noch nicht in der Datenbank gespeichert wurden.

Bei der Verwendung von Find ist eine Leistungsüberlegung zu berücksichtigen. Aufrufe zu dieser Methode lösen standardmäßig eine Überprüfung des Objektcache aus, um Änderungen zu erkennen, die noch ausstehen, wenn der Commit für die Datenbank aussteht. Dieser Vorgang kann sehr teuer sein, wenn sich eine sehr große Anzahl von Objekten im Objektcache oder in einem großen Objektdiagramm befindet, das dem Objektcache hinzugefügt wird, aber es kann auch deaktiviert werden. In bestimmten Fällen können Sie beim Aufrufen der Find-Methode eine Größenordnung von Unterschieden erkennen, wenn Sie Änderungen deaktivieren, die automatisch erkannt werden. Eine zweite Größenordnung wird jedoch wahrgenommen, wenn sich das Objekt tatsächlich im Cache befindet und wenn das Objekt aus der Datenbank abgerufen werden muss. Hier ist ein Beispieldiagramm mit Messungen, die mit einigen unserer Mikrobenchmarks, ausgedrückt in Millisekunden, mit einer Last von 5000 Entitäten durchgeführt wurden:

![.NET 4.5 logarithmische Skala](~/ef6/media/net45logscale.png ".NET 4.5 - logarithmische Skala")

Beispiel suchen mit deaktivierten Änderungen zur automatischen Erkennung:

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

Was Sie bei der Verwendung der Find-Methode beachten müssen, ist:

1.  Wenn sich das Objekt nicht im Cache befindet, werden die Vorteile von Find negiert, aber die Syntax ist immer noch einfacher als eine Abfrage nach Schlüssel.
2.  Wenn Änderungen zur automatischen Erkennung aktiviert sind, können die Kosten der Find-Methode je nach Komplexität des Modells und der Anzahl der Entitäten im Objektcache um eine Größenordnung oder sogar noch mehr steigen.

Beachten Sie außerdem, dass Find nur die gesuchte Entität zurückgibt und die zugeordneten Entitäten nicht automatisch geladen werden, wenn sie sich nicht bereits im Objektcache befinden. Wenn Sie zugeordnete Entitäten abrufen müssen, können Sie eine Abfrage nach Schlüssel mit eifrigem Laden verwenden. Weitere Informationen finden Sie unter **8.1 Lazy Loading vs. Eager Loading**.

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a>3.1.2 Leistungsprobleme, wenn der Objektcache über viele Entitäten verfügt

Der Objektcache trägt dazu bei, die Gesamtreaktionsfähigkeit von Entity Framework zu erhöhen. Wenn jedoch im Objektcache eine sehr große Anzahl von Entitäten geladen ist, kann dies bestimmte Vorgänge wie Hinzufügen, Entfernen, Suchen, Eintrag, SaveChanges und mehr beeinflussen. Insbesondere Vorgänge, die einen Aufruf von DetectChanges auslösen, werden durch sehr große Objektcaches negativ beeinflusst. DetectChanges synchronisiert das Objektdiagramm mit dem Objektstatus-Manager, und seine Leistung wird direkt durch die Größe des Objektdiagramms bestimmt. Weitere Informationen zu DetectChanges finden Sie unter [Nachverfolgen von Änderungen in POCO-Entitäten](https://msdn.microsoft.com/library/dd456848.aspx).

Bei verwendung von Entity Framework 6 können Entwickler AddRange und RemoveRange direkt auf einem DbSet aufrufen, anstatt eine Auflistung zu iterieren und Add einmal pro Instanz aufzurufen. Der Vorteil der Verwendung der Bereichsmethoden besteht darin, dass die Kosten von DetectChanges nur einmal für den gesamten Satz von Entitäten bezahlt werden, anstatt einmal pro hinzugefügter Entität.

### <a name="32-query-plan-caching"></a>3.2 Abfrageplan-Caching

Wenn eine Abfrage zum ersten Mal ausgeführt wird, wird der interne Plancompiler durchlaufen, um die konzeptionelle Abfrage in den Store-Befehl zu übersetzen (z. B. den T-SQL, der ausgeführt wird, wenn er für SQL Server ausgeführt wird).Wenn die Abfrageplanzwischenspeicherung aktiviert ist, wird der Speicherbefehl beim nächsten Ausführen der Abfrage direkt aus dem Abfrageplancache für die Ausführung abgerufen, wodurch der Plancompiler umgangen wird.

Der Abfrageplancache wird für ObjectContext-Instanzen innerhalb derselben AppDomain freigegeben. Sie müssen nicht an einer ObjectContext-Instanz festhalten, um von der Zwischenspeicherung von Abfrageplanen zu profitieren.

#### <a name="321-some-notes-about-query-plan-caching"></a>3.2.1 Einige Hinweise zum Query Plan Caching

-   Der Abfrageplancache wird für alle Abfragetypen freigegeben: Entity SQL, LINQ to Entities und CompiledQuery-Objekte.
-   Standardmäßig ist die Abfrageplanzwischenspeicherung für Entity SQL-Abfragen aktiviert, unabhängig davon, ob sie über einen EntityCommand oder über eine ObjectQuery ausgeführt werden. Es ist auch standardmäßig für LINQ to Entities-Abfragen in Entity Framework auf .NET 4.5 und in Entity Framework 6 aktiviert.
    -   Die Zwischenspeicherung von Abfrageplanen kann deaktiviert werden, indem die EnablePlanCaching-Eigenschaft (auf EntityCommand oder ObjectQuery) auf false festgelegt wird. Beispiel:
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
-   Bei parametrisierten Abfragen wird das Ändern des Parameterswerts weiterhin auf die zwischengespeicherte Abfrage treffen. Das Ändern der Facetten eines Parameters (z. B. Größe, Genauigkeit oder Skalierung) trifft jedoch einen anderen Eintrag im Cache.
-   Bei Verwendung von Entity SQL ist die Abfragezeichenfolge Teil des Schlüssels. Wenn Sie die Abfrage überhaupt ändern, führt dies zu unterschiedlichen Cacheeinträgen, auch wenn die Abfragen funktional gleichwertig sind. Dazu gehören Änderungen an der Groß-/Kleinschreibung oder im Leerraum.
-   Bei Verwendung von LINQ wird die Abfrage verarbeitet, um einen Teil des Schlüssels zu generieren. Durch Ändern des LINQ-Ausdrucks wird daher ein anderer Schlüssel generiert.
-   Es können andere technische Einschränkungen gelten; Weitere Informationen finden Sie unter Automatisch kompilierte Abfragen.

#### <a name="322-cache-eviction-algorithm"></a>3.2.2 Cache-Räumungsalgorithmus

Wenn Sie verstehen, wie der interne Algorithmus funktioniert, können Sie herausfinden, wann die Zwischenspeicherung von Abfrageplandateien aktiviert oder deaktiviert werden soll. Der Bereinigungsalgorithmus ist wie folgt:

1.  Sobald der Cache eine festgelegte Anzahl von Einträgen (800) enthält, starten wir einen Timer, der den Cache periodisch (einmal pro Minute) durchzieht.
2.  Während Cache-Sweeps werden Einträge auf LFRU-Basis (Least frequently – zuletzt verwendet) aus dem Cache entfernt. Dieser Algorithmus berücksichtigt sowohl die Trefferanzahl als auch das Alter bei der Entscheidung, welche Einträge ausgeworfen werden.
3.  Am Ende jedes Cache-Sweeps enthält der Cache erneut 800 Einträge.

Alle Cacheeinträge werden bei der Bestimmung der zu entfernenden Einträge gleich behandelt. Dies bedeutet, dass der store-Befehl für eine CompiledQuery die gleiche Chance auf Räumung hat wie der Store-Befehl für eine Entity SQL-Abfrage.

Beachten Sie, dass der Cache-Enträumungszeitgeber ausgelöst wird, wenn sich 800 Entitäten im Cache befinden, der Cache jedoch erst 60 Sekunden nach dem Start dieses Zeitgebers gefegt wird. Das bedeutet, dass Ihr Cache für bis zu 60 Sekunden recht groß werden kann.

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a>3.2.3 Testmetriken, die die Abfrageplan-Caching-Leistung demonstrieren

Um die Auswirkungen der Abfrageplanzwischenspeicherung auf die Leistung Ihrer Anwendung zu veranschaulichen, haben wir einen Test durchgeführt, bei dem wir eine Reihe von Entity SQL-Abfragen für das Navision-Modell ausgeführt haben. Eine Beschreibung des Navision-Modells und der ausgeführten Abfragetypen finden Sie im Anhang. In diesem Test durchlaufen wir zunächst die Liste der Abfragen und führen jede einmal aus, um sie dem Cache hinzuzufügen (wenn die Zwischenspeicherung aktiviert ist). Dieser Schritt ist nicht zeitgleich. Als nächstes schlafen wir den Hauptthread für mehr als 60 Sekunden, damit das Cache-Sweepen stattfinden kann. Schließlich durchlaufen wir die Liste zum zweiten Mal, um die zwischengespeicherten Abfragen auszuführen. Darüber hinaus wird der SQL Server-Plancache geleert, bevor jeder Satz von Abfragen ausgeführt wird, sodass die Zeiten, die wir erhalten, genau den Nutzen widerspiegeln, der vom Abfrageplancache gegeben wird.

##### <a name="3231-test-results"></a>3.2.3.1 Testergebnisse

| Test                                                                   | EF5 kein Cache | EF5 zwischengespeichert | EF6 kein Cache | EF6 zwischengespeichert |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| Aufzählen aller 18723-Abfragen                                          | 124          | 125.4      | 124.3        | 125.3      |
| Vermeiden von Sweep (nur die ersten 800 Abfragen, unabhängig von der Komplexität)  | 41.7         | 5.5        | 40,5         | 5.4        |
| Nur die AggregationSubtotals-Abfragen (178 insgesamt - was Sweep vermeidet) | 39.5         | 4,5        | 38.1         | 4.6        |

*Alle Zeiten in Sekunden.*

Moral - Beim Ausführen vieler unterschiedlicher Abfragen (z. B. dynamisch erstellter Abfragen) hilft das Zwischenspeichern nicht weiter, und das resultierende Leeren des Caches kann die Abfragen, die am meisten von der Planzwischenspeicherung profitieren würden, davon abhalten, sie tatsächlich zu verwenden.

Die AggregationingSubtotals-Abfragen sind die komplexesten Abfragen, mit denen wir getestet haben. Je komplexer die Abfrage ist, desto mehr Nutzen sehen Sie erwartungsgemäß durch die Zwischenspeicherung von Abfrageplandateien.

Da es sich bei einer CompiledQuery wirklich um eine LINQ-Abfrage handelt, deren Plan zwischengespeichert ist, sollte der Vergleich einer CompiledQuery mit der entsprechenden Entity SQL-Abfrage ähnliche Ergebnisse haben. Wenn eine App über viele dynamische Entity SQL-Abfragen verfügt, führt das Füllen des Caches mit Abfragen auch effektiv dazu, dass CompiledQueries beim Leeren aus dem Cache "dekompiliert" wird. In diesem Szenario kann die Leistung verbessert werden, indem die Verkabelung für die dynamischen Abfragen deaktiviert wird, um die CompiledQueries zu priorisieren. Besser wäre es natürlich, die App neu zu schreiben, um parametrisierte Abfragen anstelle dynamischer Abfragen zu verwenden.

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a>3.3 Verwenden von CompiledQuery zur Verbesserung der Leistung mit LINQ-Abfragen

Unsere Tests zeigen, dass die Verwendung von CompiledQuery einen Vorteil von 7 % gegenüber automatisch kompilierten LINQ-Abfragen bringen kann; Dies bedeutet, dass Sie 7 % weniger Zeit mit der Ausführung von Code aus dem Entity Framework-Stack verbringen. Es bedeutet nicht, dass Ihre Anwendung 7% schneller sein wird. Im Allgemeinen sind die Kosten für das Schreiben und Verwalten von CompiledQuery-Objekten in EF 5.0 im Vergleich zu den Vorteilen möglicherweise nicht die Mühe wert. Ihre Kilometerleistung kann variieren, also üben Sie diese Option aus, wenn Ihr Projekt den zusätzlichen Push erfordert. Beachten Sie, dass CompiledQueries nur mit ObjectContext-abgeleiteten Modellen und nicht mit DbContext-abgeleiteten Modellen kompatibel sind.

Weitere Informationen zum Erstellen und Aufrufen einer CompiledQuery finden Sie unter [Kompilierte Abfragen (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx).

Es gibt zwei Überlegungen, die Sie bei der Verwendung einer CompiledQuery berücksichtigen müssen, nämlich die Anforderung, statische Instanzen zu verwenden, und die Probleme, die sie mit der Komposierbarkeit haben. Hier folgt eine ausführliche Erläuterung dieser beiden Überlegungen.

#### <a name="331-use-static-compiledquery-instances"></a>3.3.1 Statische CompiledQuery-Instanzen verwenden

Da das Kompilieren einer LINQ-Abfrage ein zeitaufwändiger Prozess ist, möchten wir dies nicht jedes Mal tun, wenn wir Daten aus der Datenbank abrufen müssen. CompiledQuery-Instanzen ermöglichen es Ihnen, mehrmals zu kompilieren und auszuführen, aber Sie müssen vorsichtig sein und beschaffen, dass Sie jedes Mal dieselbe CompiledQuery-Instanz wiederverwenden, anstatt sie immer wieder zu kompilieren. Die Verwendung statischer Member zum Speichern der CompiledQuery-Instanzen wird erforderlich. andernfalls sehen Sie keinen Nutzen.

Angenommen, Ihre Seite verfügt über den folgenden Methodentext, um die Anzeigen der Produkte für die ausgewählte Kategorie zu behandeln:

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

In diesem Fall erstellen Sie bei jedem Aufruf der Methode im Fly-Betrieb eine neue CompiledQuery-Instanz. Anstatt Leistungsvorteile durch Abrufen des Store-Befehls aus dem Abfrageplancache anzuzeigen, wird die CompiledQuery bei jeder Erstellung einer neuen Instanz durch den Plancompiler gehen. Tatsächlich werden Sie Ihren Abfrageplancache bei jedem Aufruf der Methode mit einem neuen CompiledQuery-Eintrag verschmutzen.

Stattdessen möchten Sie eine statische Instanz der kompilierten Abfrage erstellen, sodass Sie bei jedem Aufruf der Methode dieselbe kompilierte Abfrage aufrufen. Eine Möglichkeit besteht darin, die CompiledQuery-Instanz als Member Ihres Objektkontexts hinzuzufügen.Sie können die Dinge dann ein wenig sauberer machen, indem Sie über eine Hilfsmethode auf die CompiledQuery zugreifen:

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

#### <a name="332-composing-over-a-compiledquery"></a>3.3.2 Komponieren über eine KompilierteAbfrage

Die Möglichkeit, über jede LINQ-Abfrage zu komponieren, ist äußerst nützlich. Um dies zu tun, rufen Sie einfach eine Methode nach dem IQueryable wie *Skip()* oder *Count()* auf. Dadurch wird jedoch im Wesentlichen ein neues IQueryable-Objekt zurückgegeben. Obwohl Sie technisch nichts davon abhalten, über eine CompiledQuery zu komponieren, führt dies zur Generierung eines neuen IQueryable-Objekts, das die Erneute Durchquerung des Plancompilers erfordert.

Einige Komponenten verwenden zusammengesetzte IQueryable-Objekte, um erweiterte Funktionen zu aktivieren. Beispiel: ASP. Net GridView kann über die SelectMethod-Eigenschaft an ein IQueryable-Objekt gebunden werden. Die GridView wird dann über dieses IQueryable-Objekt komponieren, um das Sortieren und Paging über das Datenmodell zu ermöglichen. Wie Sie sehen können, würde die Verwendung einer CompiledQuery für die GridView die kompilierte Abfrage nicht treffen, sondern eine neue automatisch kompilierte Abfrage generieren.

Ein Ort, an dem Sie dies ausführen können, ist das Hinzufügen progressiver Filter zu einer Abfrage. Angenommen, Sie haben eine Kundenseite mit mehreren Dropdownlisten für optionale Filter (z. B. Land und OrdersCount). Sie können diese Filter über die IQueryable-Ergebnisse einer CompiledQuery erstellen, aber dies führt dazu, dass die neue Abfrage bei jeder Ausführung durch den Plancompiler geht.

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

 Um diese Neukompilierung zu vermeiden, können Sie compiledQuery neu schreiben, um die möglichen Filter zu berücksichtigen:

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

Die in der Benutzeroberfläche wie folgt aufgerufen werden würde:

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

 Ein Kompromiss hier ist der generierte Store-Befehl wird immer die Filter mit den NULL-Prüfungen haben, aber diese sollten ziemlich einfach für den Datenbankserver zu optimieren sein:

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a>3.4 Metadaten-Caching

Das Entity Framework unterstützt auch das Zwischenspeichern von Metadaten. Hierbei handelt es sich im Wesentlichen um die Zwischenspeicherung von Typinformationen und Typ-zu-Datenbank-Zuordnungsinformationen über verschiedene Verbindungen zum gleichen Modell hinweg. Der Metadatencache ist pro AppDomain eindeutig.

#### <a name="341-metadata-caching-algorithm"></a>3.4.1 Metadaten-Caching-Algorithmus

1.  Metadateninformationen für ein Modell werden in einer ItemCollection für jede EntityConnection gespeichert.
    -   Nebenbei ist zu beachten, dass es verschiedene ItemCollection-Objekte für verschiedene Teile des Modells gibt. StoreItemCollections enthält z. B. die Informationen zum Datenbankmodell. ObjectItemCollection enthält Informationen zum Datenmodell. EdmItemCollection enthält Informationen zum konzeptionellen Modell.

2.  Wenn zwei Verbindungen dieselbe Verbindungszeichenfolge verwenden, verwenden sie dieselbe ItemCollection-Instanz.
3.  Funktional äquivalent, aber textlich unterschiedliche Verbindungszeichenfolgen können zu unterschiedlichen Metadaten-Caches führen. Wir tokenisieren Verbindungszeichenfolgen, daher sollte das einfache Ändern der Reihenfolge der Token zu freigegebenen Metadaten führen. Zwei Verbindungszeichenfolgen, die funktional gleich erscheinen, können jedoch nach der Tokenisierung nicht als identisch ausgewertet werden.
4.  Die ItemCollection wird regelmäßig auf Verwendung überprüft. Wenn festgestellt wird, dass vor kurzem nicht auf einen Arbeitsbereich zugegriffen wurde, wird er beim nächsten Cache-Sweep für die Bereinigung markiert.
5.  Das bloße Erstellen einer EntityConnection führt dazu, dass ein Metadatencache erstellt wird (obwohl die darin enthaltenen Elementsammlungen erst initialisiert werden, wenn die Verbindung geöffnet wird). Dieser Arbeitsbereich verbleibt im Arbeitsspeicher, bis der Caching-Algorithmus feststellt, dass er nicht "in Gebrauch" ist.

Das Kundenberatungsteam hat einen Blogbeitrag verfasst, in dem beschrieben wird, wie ein Verweis auf \< https://docs.microsoft.com/archive/blogs/appfabriccat/holding-a-reference-to-the-ef-metadataworkspace-for-wcf-services>eine ItemCollection angezeigt wird, um eine "Veraltung" bei der Verwendung großer Modelle zu vermeiden: .

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a>3.4.2 Die Beziehung zwischen Metadaten-Caching und Abfrageplan-Caching

Die Abfrageplan-Cacheinstanz befindet sich in der ItemCollection von Store-Typen des MetadataWorkspace. Dies bedeutet, dass zwischengespeicherte Speicherbefehle für Abfragen für jeden Kontext verwendet werden, der mit einem bestimmten MetadataWorkspace instanziiert wird. Es bedeutet auch, dass, wenn Sie zwei Verbindungen Zeichenfolgen haben, die etwas unterschiedlich sind und nicht übereinstimmen nach dem Tokenisieren, Sie werden verschiedene Abfrageplan-Cache-Instanzen haben.

### <a name="35-results-caching"></a>3.5 Zwischenspeicherung der Ergebnisse

Bei der Ergebniszwischenspeicherung (auch als "Zwischenspeicherung der zweiten Ebene" bezeichnet) behalten Sie die Ergebnisse von Abfragen in einem lokalen Cache. Beim Ausgeben einer Abfrage sehen Sie zunächst, ob die Ergebnisse lokal verfügbar sind, bevor Sie den Speicher abfragen. Obwohl die Zwischenspeicherung von Ergebnissen nicht direkt von Entity Framework unterstützt wird, ist es möglich, mithilfe eines Wrapping-Anbieters einen Cache der zweiten Ebene hinzuzufügen. Ein Beispiel für den Umbruchanbieter mit einem Cache der zweiten Ebene ist Alachisofts [Entity Framework Second Level Cache basierend auf NCache](https://www.alachisoft.com/ncache/entity-framework.html).

Diese Implementierung der Zwischenspeicherung der zweiten Ebene ist eine eingefügte Funktionalität, die stattfindet, nachdem der LINQ-Ausdruck ausgewertet (und funcletisiert) wurde und der Abfrageausführungsplan berechnet oder aus dem Cache der ersten Ebene abgerufen wird. Der Cache der zweiten Ebene speichert dann nur die Rohdatenbankergebnisse, sodass die Materialisierungspipeline danach noch ausgeführt wird.

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a>3.5.1 Zusätzliche Referenzen für das Zwischenspeichern von Ergebnissen mit dem Wrapping-Anbieter

-   Julie Lerman hat einen MSDN-Artikel "Second-Level Caching in Entity Framework und Windows Azure" geschrieben, der enthält, wie der Beispielumbruchanbieter aktualisiert wird, um Windows Server AppFabric-Caching zu verwenden:[https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)
-   Wenn Sie mit Entity Framework 5 arbeiten, enthält der Teamblog einen Beitrag, in dem beschrieben \< https://docs.microsoft.com/archive/blogs/adonet/ef-caching-with-jarek-kowalskis-provider>wird, wie Dinge mit dem Caching-Anbieter für Entity Framework 5: ausgeführt werden. Es enthält auch eine T4-Vorlage, um das Hinzufügen der Zwischenspeicherung der 2. Ebene zu Ihrem Projekt zu automatisieren.

## <a name="4-autocompiled-queries"></a>4 Automatisch kompilierte Abfragen

Wenn eine Abfrage für eine Datenbank mit Entity Framework ausgegeben wird, muss sie eine Reihe von Schritten durchlaufen, bevor die Ergebnisse tatsächlich materialisiert werden. ein solcher Schritt ist die Abfragekompilierung. Entity SQL-Abfragen hatten bekanntermaßen eine gute Leistung, da sie automatisch zwischengespeichert werden, sodass Sie beim zweiten oder dritten Mal dieselbe Abfrage ausführen, kann sie den Plancompiler überspringen und stattdessen den zwischengespeicherten Plan verwenden.

Entity Framework 5 führte auch die automatische Zwischenspeicherung für LINQ-zu-Entitäten-Abfragen ein. In früheren Editionen von Entity Framework, die eine CompiledQuery erstellen, um Ihre Leistung zu beschleunigen, war dies eine gängige Praxis, da Dies Ihre LINQ to Entities-Abfrage zwischengespeichert machen würde. Da das Zwischenspeichern jetzt automatisch ohne die Verwendung einer CompiledQuery erfolgt, nennen wir diese Funktion "autokompilierte Abfragen". Weitere Informationen zum Abfrageplancache und dessen Mechanik finden Sie unter Zwischenspeichern von Abfrageplans.

Entity Framework erkennt, wann eine Abfrage neu kompiliert werden muss, und zwar, wenn die Abfrage aufgerufen wird, auch wenn sie zuvor kompiliert wurde. Häufige Bedingungen, die dazu führen, dass die Abfrage neu kompiliert wird, sind:

-   Ändern der MergeOption, die Ihrer Abfrage zugeordnet ist. Die zwischengespeicherte Abfrage wird nicht verwendet, stattdessen wird der Plancompiler erneut ausgeführt, und der neu erstellte Plan wird zwischengespeichert.
-   Ändern des Werts von ContextOptions.UseCSharpNullComparisonBehavior. Sie erhalten den gleichen Effekt wie das Ändern der MergeOption.

Andere Bedingungen können verhindern, dass Ihre Abfrage den Cache verwendet. Typische Beispiele:

-   Verwenden von IEnumerable&lt;T&gt;. Enthält&lt;&gt;(T-Wert).
-   Verwenden von Funktionen, die Abfragen mit Konstanten erzeugen.
-   Verwenden der Eigenschaften eines nicht zugeordneten Objekts.
-   Verknüpfen der Abfrage mit einer anderen Abfrage, die neu kompiliert werden muss.

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a>4.1 Verwenden von IEnumerable&lt;T&gt;. Enthält&lt;&gt;T (T-Wert)

Entity Framework speichert keine Abfragen zwischen,&lt;die&gt;IEnumerable T aufrufen. Enthält&lt;&gt;T (T-Wert) für eine In-Memory-Auflistung, da die Werte der Auflistung als flüchtig betrachtet werden. Die folgende Beispielabfrage wird nicht zwischengespeichert, daher wird sie immer vom Plancompiler verarbeitet:

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

Beachten Sie, dass die Größe der IEnumerable, für die Contains ausgeführt wird, bestimmt, wie schnell oder wie langsam Ihre Abfrage kompiliert wird. Die Leistung kann erheblich beeinträchtigt werden, wenn große Sammlungen wie die im obigen Beispiel gezeigte verwendet werden.

Entity Framework 6 enthält Optimierungen für die&lt;&gt;Art und Weise, wie IEnumerable T . Enthält&lt;&gt;T (T-Wert) funktioniert, wenn Abfragen ausgeführt werden. Der generierte SQL-Code ist viel schneller zu erstellen und lesbarer, und in den meisten Fällen wird er auch schneller auf dem Server ausgeführt.

### <a name="42-using-functions-that-produce-queries-with-constants"></a>4.2 Verwenden von Funktionen, die Abfragen mit Konstanten erzeugen

Die LINQ-Operatoren Skip(), Take(), Contains() und DefautIfEmpty() erzeugen keine SQL-Abfragen mit Parametern, sondern setzen die an sie übergebenen Werte als Konstanten. Aus diesem Grund verschmutzen Abfragen, die andernfalls möglicherweise identisch sind, den Abfrageplancache sowohl auf dem EF-Stack als auch auf dem Datenbankserver und werden nur dann wiederverwendet, wenn dieselben Konstanten in einer nachfolgenden Abfrageausführung verwendet werden. Beispiel:

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

In diesem Beispiel wird die Abfrage jedes Mal, wenn diese Abfrage mit einem anderen Wert für id ausgeführt wird, in einen neuen Plan kompiliert.

Achten Sie insbesondere bei Paging auf die Verwendung von Skip and Take. In EF6 weisen diese Methoden eine Lambda-Überladung auf, die den zwischengespeicherten Abfrageplan effektiv wiederverwendbar macht, da EF an diese Methoden übergebene Variablen erfassen und in SQLparameter übersetzen kann. Dies hilft auch, den Cache-Cleaner zu halten, da andernfalls jede Abfrage mit einer anderen Konstante für Skip and Take einen eigenen Abfrageplancacheeintrag erhalten würde.

Betrachten Sie den folgenden Code, der suboptimal ist, aber nur als Beispiel für diese Abfrageklasse gedacht ist:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

Eine schnellere Version desselben Codes würde den Aufruf von Skip mit einem Lambda beinhalten:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

Der zweite Ausschnitt kann bis zu 11 % schneller ausgeführt werden, da bei jeder Ausführung der Abfrage derselbe Abfrageplan verwendet wird, wodurch CPU-Zeit gespart wird und eine Verschmutzung des Abfragecache vermieden wird. Da sich der Parameter zu Überspringen in einem Verschluss befindet, könnte der Code jetzt genauso gut wie folgt aussehen:

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a>4.3 Verwenden der Eigenschaften eines nicht zugeordneten Objekts

Wenn eine Abfrage die Eigenschaften eines nicht zugeordneten Objekttyps als Parameter verwendet, wird die Abfrage nicht zwischengespeichert. Beispiel:

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

In diesem Beispiel wird davon ausgegangen, dass die Klasse NonMappedType nicht Teil des Entitätsmodells ist. Diese Abfrage kann leicht geändert werden, um keinen nicht zugeordneten Typ zu verwenden, und stattdessen eine lokale Variable als Parameter für die Abfrage verwenden:

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

In diesem Fall kann die Abfrage zwischengespeichert werden und profitiert vom Abfrageplancache.

### <a name="44-linking-to-queries-that-require-recompiling"></a>4.4 Verknüpfen mit Abfragen, die eine Neukompilierung erfordern

Wenn Sie nach dem gleichen Beispiel wie oben eine zweite Abfrage haben, die auf einer Abfrage basiert, die neu kompiliert werden muss, wird auch die gesamte zweite Abfrage neu kompiliert. Hier ist ein Beispiel, um dieses Szenario zu veranschaulichen:

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

Das Beispiel ist generisch, zeigt jedoch, wie die Verknüpfung mit firstQuery dazu führt, dass secondQuery nicht zwischengespeichert werden kann. Wenn firstQuery keine Abfrage war, die eine Neukompilierung erfordert, wäre secondQuery zwischengespeichert worden.

## <a name="5-notracking-queries"></a>5 NoTracking-Abfragen

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a>5.1 Deaktivieren der Änderungsverfolgung, um den Aufwand für die Verwaltung des Bundesstaates zu reduzieren

Wenn Sie sich in einem schreibgeschützten Szenario befinden und den Aufwand für das Laden der Objekte in den ObjectStateManager vermeiden möchten, können Sie "No Tracking"-Abfragen ausstellen.Die Änderungsnachverfolgung kann auf Abfrageebene deaktiviert werden.

Beachten Sie jedoch, dass Sie durch das Deaktivieren der Änderungsverfolgung den Objektcache effektiv deaktivieren. Wenn Sie eine Entität abfragen, können wir die Materialisierung nicht überspringen, indem wir die zuvor materialisierten Abfrageergebnisse aus dem ObjectStateManager abrufen. Wenn Sie wiederholt nach denselben Entitäten im gleichen Kontext abfragen, wird möglicherweise ein Leistungsvorteil aus der Aktivierung der Änderungsnachverfolgung angezeigt.

Bei Abfragen mit ObjectContext erinnern sich ObjectQuery- und ObjectSet-Instanzen an eine MergeOption, sobald sie festgelegt wurde, und Abfragen, die darauf zusammengesetzt sind, erben die effektive MergeOption der übergeordneten Abfrage. Bei Verwendung von DbContext kann die Nachverfolgung durch Aufrufen des Modifikators AsNoTracking() im DbSet deaktiviert werden.

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a>5.1.1 Deaktivieren der Änderungsnachverfolgung für eine Abfrage bei Verwendung von DbContext

Sie können den Modus einer Abfrage auf NoTracking umstellen, indem Sie einen Aufruf an die AsNoTracking()-Methode in der Abfrage verketten. Im Gegensatz zu ObjectQuery verfügen die DbSet- und DbQuery-Klassen in der DbContext-API nicht über eine veränderliche Eigenschaft für die MergeOption.

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a>5.1.2 Deaktivieren der Änderungsnachverfolgung auf Abfrageebene mithilfe von ObjectContext

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a>5.1.3 Deaktivieren der Änderungsnachverfolgung für einen gesamten Entitätssatz mithilfe von ObjectContext

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a>5.2 Testmetriken, die den Leistungsvorteil von NoTracking-Abfragen demonstrieren

In diesem Test betrachten wir die Kosten für das Ausfüllen des ObjectStateManagers, indem wir Tracking mit NoTracking-Abfragen für das Navision-Modell vergleichen. Eine Beschreibung des Navision-Modells und der ausgeführten Abfragetypen finden Sie im Anhang. In diesem Test durchlaufen wir die Liste der Abfragen und führen jede einzelne einmal aus. Wir haben zwei Varianten des Tests ausgeführt, einmal mit NoTracking-Abfragen und einmal mit der Standard-Merge-Option "AppendOnly". Wir haben jede Variante 3 Mal ausgeführt und nehmen den Mittelwert der Läufe. Zwischen den Tests löschen wir den Abfragecache auf dem SQL Server und verkleinern den tempdb, indem wir die folgenden Befehle ausführen:

1.  DBCC DROPCLEANBUFFERS
2.  DBCC FREEPROCCACHE
3.  DBCC SHRINKDATABASE (tempdb, 0)

Testergebnisse, Median über 3 Durchläufe:

|                        | KEIN TRACKING – ARBEITSSET | KEINE VERFOLGUNG – ZEIT | NUR ANFÜGEN – ARBEITSSET | NUR ANFÜGEN – ZEIT |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| **Entity Framework 5** | 460361728                 | 1163536 ms         | 596545536                 | 1273042 ms         |
| **Entity Framework 6** | 647127040                 | 190228 ms          | 832798720                 | 195521 ms          |

Entity Framework 5 hat am Ende der Ausführung einen kleineren Speicherbedarf als Entity Framework 6. Der zusätzliche Speicher, der von Entity Framework 6 verbraucht wird, ist das Ergebnis zusätzlicher Speicherstrukturen und Code, die neue Features und eine bessere Leistung ermöglichen.

Es gibt auch einen deutlichen Unterschied im Speicherbedarf bei der Verwendung von ObjectStateManager. Entity Framework 5 erhöhte seinen Platzbedarf um 30 %, wenn es alle Entitäten, die wir aus der Datenbank erhalten haben, nachverfolgt. Entity Framework 6 erhöhte dabei seinen Fußabdruck um 28 %.

Zeitlich übertrifft Entity Framework 6 Entity Framework 5 in diesem Test mit großem Abstand. Entity Framework 6 hat den Test in etwa 16 % der von Entity Framework 5 verbrauchten Zeit abgeschlossen. Darüber hinaus benötigt Entity Framework 5 9 % mehr Zeit, wenn der ObjectStateManager verwendet wird. Im Vergleich dazu verwendet Entity Framework 6 3 % mehr Zeit bei der Verwendung von ObjectStateManager.

## <a name="6-query-execution-options"></a>6 Abfrageausführungsoptionen

Entity Framework bietet verschiedene Abfragemöglichkeiten. Wir werfen einen Blick auf die folgenden Optionen, vergleichen die Vor- und Nachteile der einzelnen Optionen und untersuchen deren Leistungsmerkmale:

-   LINQ zu Entitäten.
-   Kein Tracking-LINQ auf Entitäten.
-   Entity SQL über eine ObjectQuery.
-   Entity SQL über einen EntityCommand.
-   ExecuteStoreQuery.
-   SqlQuery.
-   Compiledquery.

### <a name="61-linq-to-entities-queries"></a>6.1 LINQ-zu-Entitäten-Abfragen

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

**Vorteile**

-   Geeignet für CUD-Operationen.
-   Vollständig materialisierte Objekte.
-   Einfachstes Schreiben mit Syntax, die in die Programmiersprache integriert ist.
-   Gute Leistung.

**Nachteile**

-   Bestimmte technische Einschränkungen, wie z. B.:
    -   Muster, die DefaultIfEmpty für OUTER JOIN-Abfragen verwenden, führen zu komplexeren Abfragen als einfache OUTER JOIN-Anweisungen in Entity SQL.
    -   Sie können LIKE immer noch nicht mit allgemeinem Musterabgleich verwenden.

### <a name="62-no-tracking-linq-to-entities-queries"></a>6.2 Keine Nachverfolgung von LINQ-Zu Entitäten-Abfragen

Wenn der Kontext ObjectContext ableitet:

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

Wenn der Kontext DbContext ableitet:

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

**Vorteile**

-   Verbesserte Leistung gegenüber regulären LINQ-Abfragen.
-   Vollständig materialisierte Objekte.
-   Einfachstes Schreiben mit Syntax, die in die Programmiersprache integriert ist.

**Nachteile**

-   Nicht geeignet für CUD-Operationen.
-   Bestimmte technische Einschränkungen, wie z. B.:
    -   Muster, die DefaultIfEmpty für OUTER JOIN-Abfragen verwenden, führen zu komplexeren Abfragen als einfache OUTER JOIN-Anweisungen in Entity SQL.
    -   Sie können LIKE immer noch nicht mit allgemeinem Musterabgleich verwenden.

Beachten Sie, dass Abfragen, die skalare Eigenschaften projizieren, auch dann nicht nachverfolgt werden, wenn NoTracking nicht angegeben ist. Beispiel:

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

Diese spezielle Abfrage gibt nicht explizit an, noTracking zu sein, aber da sie keinen Typ materialisiert, der dem Objektstatus-Manager bekannt ist, wird das materialisierte Ergebnis nicht nachverfolgt.

### <a name="63-entity-sql-over-an-objectquery"></a>6.3 Entity SQL über eine ObjectQuery

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

**Vorteile**

-   Geeignet für CUD-Operationen.
-   Vollständig materialisierte Objekte.
-   Unterstützt die Zwischenspeicherung von Abfrageplanen.

**Nachteile**

-   Beinhaltet Textabfragezeichenfolgen, die anfälliger für Benutzerfehler sind als Abfragekonstrukte, die in die Sprache integriert sind.

### <a name="64-entity-sql-over-an-entity-command"></a>6.4 Entity SQL über einen Entitätsbefehl

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

-   Unterstützt die Zwischenspeicherung von Abfrageplanen in .NET 4.0 (Planzwischenspeicherung wird von allen anderen Abfragetypen in .NET 4.5 unterstützt).

**Nachteile**

-   Beinhaltet Textabfragezeichenfolgen, die anfälliger für Benutzerfehler sind als Abfragekonstrukte, die in die Sprache integriert sind.
-   Nicht geeignet für CUD-Operationen.
-   Die Ergebnisse werden nicht automatisch materialisiert und müssen vom Datenleser gelesen werden.

### <a name="65-sqlquery-and-executestorequery"></a>6.5 SqlQuery und ExecuteStoreQuery

SqlQuery in der Datenbank:

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

SqlQuery auf DbSet:

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

ExecyteStoreQuery:

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

**Vorteile**

-   Im Allgemeinen schnellste Leistung seit dem Plancompiler umgangen wird.
-   Vollständig materialisierte Objekte.
-   Geeignet für CUD-Operationen, wenn sie aus dem DbSet verwendet werden.

**Nachteile**

-   Die Abfrage ist textlich und fehleranfällig.
-   Die Abfrage ist an ein bestimmtes Backend gebunden, indem speichersemantics anstelle der konzeptionellen Semantik verwendet wird.
-   Wenn die Vererbung vorhanden ist, muss die handgefertigte Abfrage die Zuordnungsbedingungen für den angeforderten Typ berücksichtigen.

### <a name="66-compiledquery"></a>6.6 KompilierteAbfrage

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

**Vorteile**

-   Bietet eine Leistungsverbesserung von bis zu 7 % gegenüber regulären LINQ-Abfragen.
-   Vollständig materialisierte Objekte.
-   Geeignet für CUD-Operationen.

**Nachteile**

-   Erhöhte Komplexität und Programmieraufwand.
-   Die Leistungsverbesserung geht beim Komponieren über einer kompilierten Abfrage verloren.
-   Einige LINQ-Abfragen können nicht als CompiledQuery geschrieben werden, z. B. Projektionen anonymer Typen.

### <a name="67-performance-comparison-of-different-query-options"></a>6.7 Leistungsvergleich verschiedener Abfrageoptionen

Einfache Mikrobenchmarks, bei denen die Kontexterstellung nicht zeitlich geplant wurde, wurden auf die Probe gestellt. Wir haben die Abfrage 5000-mal für eine Reihe nicht zwischengespeicherter Entitäten in einer kontrollierten Umgebung gemessen. Diese Zahlen sind mit einer Warnung zu nehmen: Sie spiegeln nicht die tatsächlichen Zahlen wider, die von einer Anwendung erzeugt werden, sondern sie sind vielmehr eine sehr genaue Messung, wie viel von einem Leistungsunterschied es gibt, wenn verschiedene Abfrageoptionen Äpfel-zu-Äpfel verglichen werden, ohne die Kosten für die Schaffung eines neuen Kontextes.

| EF  | Test                                 | Zeit (ms) | Arbeitsspeicher   |
|:----|:-------------------------------------|:----------|:---------|
| EF5 | ObjectContext ESQL                   | 2414      | 38801408 |
| EF5 | ObjectContext Linq-Abfrage             | 2692      | 38277120 |
| EF5 | DbContext Linq Query Keine Nachverfolgung     | 2818      | 41840640 |
| EF5 | DbContext Linq-Abfrage                 | 2930      | 41771008 |
| EF5 | ObjectContext Linq Query Keine Nachverfolgung | 3013      | 38412288 |
|     |                                      |           |          |
| EF6 | ObjectContext ESQL                   | 2059      | 46039040 |
| EF6 | ObjectContext Linq-Abfrage             | 3074      | 45248512 |
| EF6 | DbContext Linq Query Keine Nachverfolgung     | 3125      | 47575040 |
| EF6 | DbContext Linq-Abfrage                 | 3420      | 47652864 |
| EF6 | ObjectContext Linq Query Keine Nachverfolgung | 3593      | 45260800 |

![EF5-Mikro-Benchmarks, 5000 warme Iterationen](~/ef6/media/ef5micro5000warm.png)

![EF6-Mikro-Benchmarks, 5000 warme Iterationen](~/ef6/media/ef6micro5000warm.png)

Mikrobenchmarks reagieren sehr empfindlich auf kleine Änderungen im Code. In diesem Fall ist die Differenz zwischen den Kosten von Entity Framework 5 und Entity Framework 6 auf das Hinzufügen von [Abhör-](~/ef6/fundamentals/logging-and-interception.md) und [Transaktionsverbesserungen](~/ef6/saving/transactions.md)zurückzuführen. Diese Zahlen von Mikrobenchmarks sind jedoch eine verstärkte Vision in ein sehr kleines Fragment dessen, was Entity Framework tut. Reale Szenarien warmer Abfragen sollten beim Upgrade von Entity Framework 5 auf Entity Framework 6 keine Leistungsregression sehen.

Um die reale Leistung der verschiedenen Abfrageoptionen zu vergleichen, haben wir 5 separate Testvarianten erstellt, in denen wir eine andere Abfrageoption verwenden, um alle Produkte auszuwählen, deren Kategoriename "Getränke" ist. Jede Iteration umfasst die Kosten für das Erstellen des Kontexts und die Kosten für die Materialisierung aller zurückgegebenen Entitäten. 10 Iterationen werden unzeitbeschränkt ausgeführt, bevor die Summe von 1000 zeitgezeitierten Iterationen verwendet wird. Die angezeigten Ergebnisse sind die mediane Ausführung aus 5 Durchläufen jedes Tests. Weitere Informationen finden Sie in Anhang B, der den Code für den Test enthält.

| EF  | Test                                        | Zeit (ms) | Arbeitsspeicher   |
|:----|:--------------------------------------------|:----------|:---------|
| EF5 | ObjectContext-Entitätsbefehl                | 621       | 39350272 |
| EF5 | DbContext Sql-Abfrage in der Datenbank             | 825       | 37519360 |
| EF5 | ObjectContext Store-Abfrage                   | 878       | 39460864 |
| EF5 | ObjectContext Linq Query Keine Nachverfolgung        | 969       | 38293504 |
| EF5 | ObjectContext Entity Sql mit Objektabfrage | 1089      | 38981632 |
| EF5 | ObjectContext Kompilierte Abfrage                | 1099      | 38682624 |
| EF5 | ObjectContext Linq-Abfrage                    | 1152      | 38178816 |
| EF5 | DbContext Linq Query Keine Nachverfolgung            | 1208      | 41803776 |
| EF5 | DbContext Sql-Abfrage auf DbSet                | 1414      | 37982208 |
| EF5 | DbContext Linq-Abfrage                        | 1574      | 41738240 |
|     |                                             |           |          |
| EF6 | ObjectContext-Entitätsbefehl                | 480       | 47247360 |
| EF6 | ObjectContext Store-Abfrage                   | 493       | 46739456 |
| EF6 | DbContext Sql-Abfrage in der Datenbank             | 614       | 41607168 |
| EF6 | ObjectContext Linq Query Keine Nachverfolgung        | 684       | 46333952 |
| EF6 | ObjectContext Entity Sql mit Objektabfrage | 767       | 48865280 |
| EF6 | ObjectContext Kompilierte Abfrage                | 788       | 48467968 |
| EF6 | DbContext Linq Query Keine Nachverfolgung            | 878       | 47554560 |
| EF6 | ObjectContext Linq-Abfrage                    | 953       | 47632384 |
| EF6 | DbContext Sql-Abfrage auf DbSet                | 1023      | 41992192 |
| EF6 | DbContext Linq-Abfrage                        | 1290      | 47529984 |


![EF5-Warmabfrage 1000 Iterationen](~/ef6/media/ef5warmquery1000.png)

![EF6-Warmabfrage 1000 Iterationen](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> Der Vollständigkeit halber haben wir eine Variante eingeschlossen, bei der wir eine Entity SQL-Abfrage für einen EntityCommand ausführen. Da für solche Abfragen jedoch keine Ergebnisse vorliegen, ist der Vergleich nicht unbedingt Äpfel zu Äpfeln. Der Test beinhaltet eine enge Annäherung an die Materialisierung, um den Vergleich fairer zu machen.

In diesem End-to-End-Fall übertrifft Entity Framework 6 Entity Framework 5 aufgrund von Leistungsverbesserungen an mehreren Teilen des&lt;&gt; Stacks, einschließlich einer viel leichteren DbContext-Initialisierung und schnelleren MetadataCollection T-Suchstellen.

## <a name="7-design-time-performance-considerations"></a>7 Überlegungen zur Entwurfszeitleistung

### <a name="71-inheritance-strategies"></a>7.1 Vererbungsstrategien

Eine weitere Leistungsüberlegung bei der Verwendung von Entity Framework ist die vererbungsstrategie, die Sie verwenden. Entity Framework unterstützt 3 grundlegende Vererbungstypen und deren Kombinationen:

-   Tabelle pro Hierarchie (TPH) – wobei jeder Vererbungssatz einer Tabelle mit einer Diskriminatorspalte zugeordnet wird, um anzugeben, welcher bestimmte Typ in der Hierarchie in der Zeile dargestellt wird.
-   Tabelle pro Typ (TPT) – wobei jeder Typ eine eigene Tabelle in der Datenbank hat; Die untergeordneten Tabellen definieren nur die Spalten, die die übergeordnete Tabelle nicht enthält.
-   Tabelle pro Klasse (TPC) – wobei jeder Typ eine eigene vollständige Tabelle in der Datenbank hat; Die untergeordneten Tabellen definieren alle ihre Felder, einschließlich der in übergeordneten Typen definierten.

Wenn Ihr Modell die TPT-Vererbung verwendet, sind die generierten Abfragen komplexer als die Abfragen, die mit den anderen Vererbungsstrategien generiert werden, was zu längeren Ausführungszeiten im Speicher führen kann.Es dauert in der Regel länger, Abfragen über ein TPT-Modell zu generieren und die resultierenden Objekte zu materialisieren.

Weitere Informationen finden Sie im MSDN-Blogbeitrag "Performance Considerations when using \< https://docs.microsoft.com/archive/blogs/adonet/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework>TPT (Table per Type) Inheritance in the Entity Framework": .

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a>7.1.1 Vermeidung von TPT in Model First- oder Code First-Anwendungen

Wenn Sie ein Modell über eine vorhandene Datenbank mit einem TPT-Schema erstellen, haben Sie nicht viele Optionen. Wenn Sie jedoch eine Anwendung mit Model First oder Code First erstellen, sollten Sie die TPT-Vererbung aus Leistungsgründen vermeiden.

Wenn Sie Model First im Entity Designer-Assistenten verwenden, erhalten Sie TPT für jede Vererbung in Ihrem Modell. Wenn Sie mit Model First zu einer TPH-Vererbungsstrategie wechseln möchten, können Sie das \< http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>)"Entity Designer Database Generation Power Pack" verwenden, das in der Visual Studio-Galerie ( verfügbar ist.

Wenn Sie Code First verwenden, um die Zuordnung eines Modells mit Vererbung zu konfigurieren, verwendet EF standardmäßig TPH, daher werden alle Entitäten in der Vererbungshierarchie derselben Tabelle zugeordnet. Weitere Informationen finden Sie im Abschnitt "Mapping with the Fluent API" im Artikel "Code First in Entity Framework4.1" im MSDN Magazine ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)).

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a>7.2 Upgrade von EF4 zur Verbesserung der Modellgenerierungszeit

Eine SQL Server-spezifische Verbesserung des Algorithmus, der die Speicherschicht (SSDL) des Modells generiert, ist in Entity Framework 5 und 6 und als Aktualisierung von Entity Framework 4 verfügbar, wenn Visual Studio 2010 SP1 installiert ist. Die folgenden Testergebnisse zeigen die Verbesserung beim Generieren eines sehr großen Modells, in diesem Fall des Navision-Modells. Weitere Informationen dazu finden Sie in Anhang C.

Das Modell enthält 1005 Entitätssätze und 4227 Zuordnungssätze.

| Konfiguration                              | Aufschlüsselung der verbrauchten Zeit                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Visual Studio 2010, Entity Framework 4     | SSDL-Generierung: 2 Std. 27 min <br/> Mapping-Generierung: 1 Sekunde <br/> CSDL-Generierung: 1 Sekunde <br/> ObjectLayer-Generierung: 1 Sekunde <br/> Ansicht Generation: 2 h 14 min |
| Visual Studio 2010 SP1, Entity Framework 4 | SSDL-Generierung: 1 Sekunde <br/> Mapping-Generierung: 1 Sekunde <br/> CSDL-Generierung: 1 Sekunde <br/> ObjectLayer-Generierung: 1 Sekunde <br/> Ansicht Generation: 1 Std. 53 min   |
| Visual Studio 2013, Entity Framework 5     | SSDL-Generierung: 1 Sekunde <br/> Mapping-Generierung: 1 Sekunde <br/> CSDL-Generierung: 1 Sekunde <br/> ObjectLayer-Generierung: 1 Sekunde <br/> Ansicht Generation: 65 Minuten    |
| Visual Studio 2013, Entity Framework 6     | SSDL-Generierung: 1 Sekunde <br/> Mapping-Generierung: 1 Sekunde <br/> CSDL-Generierung: 1 Sekunde <br/> ObjectLayer-Generierung: 1 Sekunde <br/> Ansichtsgenerierung: 28 Sekunden.   |


Es ist erwähnenswert, dass beim Generieren der SSDL die Last fast vollständig auf sql Server ausgegeben wird, während der Cliententwicklungscomputer im Leerlauf darauf wartet, dass die Ergebnisse vom Server zurückkommen. DBAs sollten diese Verbesserung besonders zu schätzen wissen. Es ist auch erwähnenswert, dass im Wesentlichen die gesamten Kosten der Modellgenerierung jetzt in View Generation stattfinden.

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a>7.3 Aufteilen großer Modelle mit Datenbank zuerst und Modell zuerst

Wenn die Modellgröße zunimmt, wird die Designeroberfläche unübersichtlich und schwer zu bedienen. In der Regel betrachten wir ein Modell mit mehr als 300 Entitäten als zu groß, um den Designer effektiv verwenden zu können. Der folgende Blogbeitrag beschreibt mehrere Optionen \< https://docs.microsoft.com/archive/blogs/adonet/working-with-large-models-in-entity-framework-part-2>zum Aufteilen großer Modelle: .

Der Beitrag wurde für die erste Version von Entity Framework geschrieben, aber die Schritte gelten weiterhin.

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a>7.4 Leistungserwägungen mit dem Entitätsdatenquellensteuerelement

Wir haben Fälle in Multithread-Leistungs- und Stresstests gesehen, bei denen sich die Leistung einer Webanwendung mit entityDataSource Control erheblich verschlechtert. Die zugrunde liegende Ursache ist, dass EntityDataSource wiederholt MetadataWorkspace.LoadFromAssembly für die Assemblys aufruft, auf die von der Webanwendung verwiesen wird, um die Typen zu ermitteln, die als Entitäten verwendet werden sollen.

Die Lösung besteht darin, den ContextTypeName der EntityDataSource auf den Typnamen der abgeleiteten ObjectContext-Klasse festzulegen. Dadurch wird der Mechanismus deaktiviert, der alle assemblys referenzierten Assemblys nach Entitätstypen scannt.

Durch Festlegen des Felds ContextTypeName wird auch ein Funktionsproblem verhindert, bei dem EntityDataSource in .NET 4.0 eine ReflectionTypeLoadException auslöst, wenn ein Typ nicht über Reflektion aus einer Assembly geladen werden kann. Dieses Problem wurde in .NET 4.5 behoben.

### <a name="75-poco-entities-and-change-tracking-proxies"></a>7.5 POCO-Entitäten und Änderungsverfolgungsproxys

Mit Entity Framework können Sie benutzerdefinierte Datenklassen zusammen mit Ihrem Datenmodell verwenden, ohne Änderungen an den Datenklassen selbst vorzunehmen. Dies bedeutet, dass Sie POCO-Objekte (Plain-old CLR objects), z. B. vorhandene Domänenobjekte, mit dem Datenmodell verwenden können. Diese POCO-Datenklassen (auch als persistenzununistierte Objekte bezeichnet), die Entitäten zugeordnet sind, die in einem Datenmodell definiert sind, unterstützen den größten Teil derselben Abfrage, einfügen, aktualisieren und löschen Verhalten als Entitätstypen, die von den Entitätsdatenmodell-Tools generiert werden.

Entity Framework kann auch Proxyklassen erstellen, die von Ihren POCO-Typen abgeleitet sind, die verwendet werden, wenn Sie Features wie verzögertes Laden und automatische Änderungsnachverfolgung für POCO-Entitäten aktivieren möchten. Ihre POCO-Klassen müssen bestimmte Anforderungen erfüllen, damit Entity Framework [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)Proxys verwenden kann, wie hier beschrieben: .

Chancenverfolgungsproxys benachrichtigen den Objektstatus-Manager jedes Mal, wenn sich der Wert ihrer Entitäten geändert hat, sodass Entity Framework den aktuellen Status Ihrer Entitäten immer kennt. Dies geschieht, indem dem Text der Settermethoden Ihrer Eigenschaften Benachrichtigungsereignisse hinzugefügt und der Objektstatus-Manager solche Ereignisse verarbeitet. Beachten Sie, dass das Erstellen einer Proxyentität in der Regel teurer ist als das Erstellen einer nicht-proxy-POCO-Entität aufgrund des hinzugefügten Satzes von Ereignissen, die von Entity Framework erstellt wurden.

Wenn eine POCO-Entität nicht über einen Änderungsverfolgungsproxy verfügt, werden Änderungen gefunden, indem der Inhalt Ihrer Entitäten mit einer Kopie eines vorherigen gespeicherten Status verglichen wird. Dieser tiefe Vergleich wird zu einem langwierigen Prozess, wenn Sie viele Entitäten in Ihrem Kontext haben oder wenn Ihre Entitäten über eine sehr große Anzahl von Eigenschaften verfügen, auch wenn sich keines davon seit dem letzten Vergleich geändert hat.

Zusammengefasst: Sie zahlen beim Erstellen des Änderungsverfolgungsproxys einen Leistungseinschlag, aber die Änderungsnachverfolgung hilft Ihnen, den Änderungserkennungsprozess zu beschleunigen, wenn Ihre Entitäten über viele Eigenschaften verfügen oder wenn Sie viele Entitäten in Ihrem Modell haben. Für Entitäten mit einer kleinen Anzahl von Eigenschaften, bei denen die Anzahl der Entitäten nicht zu stark wächst, ist das Nachverfolgen von Änderungen möglicherweise nicht von großer Vorteil.

## <a name="8-loading-related-entities"></a>8 Laden verwandter Entitäten

### <a name="81-lazy-loading-vs-eager-loading"></a>8.1 Lazy Loading vs. Eager Loading

Entity Framework bietet verschiedene Möglichkeiten zum Laden der Entitäten, die mit Ihrer Zielentität verknüpft sind. Wenn Sie z. B. Produkte abfragen, gibt es verschiedene Möglichkeiten, wie die zugehörigen Aufträge in den Objektstatus-Manager geladen werden. Aus Leistungssicht ist die größte Frage beim Laden verwandter Entitäten die Frage, ob Lazy Loading oder Eager Loading verwendet werden soll.

Wenn Sie Eager Loading verwenden, werden die verknüpften Entitäten zusammen mit dem Zielentitätssatz geladen. Sie verwenden eine Include-Anweisung in Ihrer Abfrage, um anzugeben, welche verknüpften Entitäten Sie einführen möchten.

Bei Verwendung von Lazy Loading führt die ursprüngliche Abfrage nur den Zielentitätssatz ein. Wenn Sie jedoch auf eine Navigationseigenschaft zugreifen, wird eine weitere Abfrage für den Speicher ausgegeben, um die verknüpfte Entität zu laden.

Sobald eine Entität geladen wurde, werden alle weiteren Abfragen für die Entität direkt aus dem Objektstatus-Manager geladen, unabhängig davon, ob Sie verzögertes Laden oder eifriges Laden verwenden.

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a>8.2 Wie Sie zwischen Lazy Loading und Eager Loading wählen

Wichtig ist, dass Sie den Unterschied zwischen Lazy Loading und Eager Loading verstehen, damit Sie die richtige Wahl für Ihre Anwendung treffen können. Auf diese Weise können Sie den Kompromiss zwischen mehreren Anforderungen für die Datenbank und einer einzelnen Anforderung, die möglicherweise eine große Nutzlast enthalten, auswerten. Es kann sinnvoll sein, eifriges Laden in einigen Teilen Ihrer Anwendung und verzögertes Laden in anderen Teilen zu verwenden.

Als Beispiel für das, was unter der Haube passiert, nehmen Sie an, Sie möchten nach den Kunden fragen, die in Großbritannien leben und deren Auftragsanzahl.

**Verwenden von Eager Loading**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

**Verwenden von Lazy Loading**

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

Wenn Sie eifriges Laden verwenden, geben Sie eine einzige Abfrage aus, die alle Kunden und alle Aufträge zurückgibt. Der Store-Befehl sieht wie folgt aus:

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

Wenn Sie das verzögerte Laden verwenden, geben Sie zunächst die folgende Abfrage aus:

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

Und jedes Mal, wenn Sie auf die Orders-Navigationseigenschaft eines Kunden zugreifen, wird eine andere Abfrage wie die folgende für den Shop ausgegeben:

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

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a>8.2.1 Lazy Loading versus Eager Loading Cheat Sheet

Es gibt nicht so etwas wie eine Einheitsgröße passt-alles, um eifriges Laden im Vergleich zu faulen Laden zu wählen. Versuchen Sie zuerst, die Unterschiede zwischen beiden Strategien zu verstehen, damit Sie eine gut informierte Entscheidung treffen können; Überlegen Sie auch, ob der Code an eines der folgenden Szenarien anpasst:

| Szenario                                                                    | Unser Vorschlag                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Müssen Sie von den abgerufenen Entitäten auf viele Navigationseigenschaften zugreifen? | **Nein** - Beide Optionen werden wahrscheinlich tun. Wenn die Nutzlast, die Ihre Abfrage mit sich bringt, jedoch nicht zu groß ist, können Sie Leistungsvorteile erzielen, wenn Sie Eager-Laden verwenden, da weniger Netzwerk-Rundfahrten erforderlich sind, um Ihre Objekte zu materialisieren. <br/> <br/> **Ja** - Wenn Sie von den Entitäten aus auf viele Navigationseigenschaften zugreifen müssen, verwenden Sie mehrere Include-Anweisungen in Ihrer Abfrage mit Eager-Laden. Je mehr Entitäten Sie einschließen, desto größer ist die Nutzlast, die Ihre Abfrage zurückgibt. Nachdem Sie drei oder mehr Entitäten in Ihre Abfrage aufgenommen haben, sollten Sie zu Lazy loading wechseln. |
| Wissen Sie genau, welche Daten zur Laufzeit benötigt werden?                   | **Nein** - Faules Laden wird besser für Sie sein. Andernfalls können Sie am Ende nach Daten fragen, die Sie nicht benötigen. <br/> <br/> **Ja** - Eager Laden ist wahrscheinlich Ihre beste Wette; Es wird helfen, ganze Sets schneller zu laden. Wenn Ihre Abfrage das Abrufen einer sehr großen Datenmenge erfordert und dies zu langsam wird, versuchen Sie stattdessen, das Laden lazy zu löschen.                                                                                                                                                                                                                                                       |
| Wird Ihr Code weit von Ihrer Datenbank entfernt ausgeführt? (erhöhte Netzwerklatenz)  | **Nein** - Wenn die Netzwerklatenz kein Problem ist, kann die Verwendung von Lazy-Laden Ihren Code vereinfachen. Denken Sie daran, dass sich die Topologie Ihrer Anwendung ändern kann, daher ist die Datenbanknähe nicht selbstverständlich. <br/> <br/> **Ja** - Wenn das Netzwerk ein Problem ist, können nur Sie entscheiden, was besser zu Ihrem Szenario passt. In der Regel eager Laden wird besser sein, weil es weniger Hin- und Rückfahrten erfordert.                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a>8.2.2 Leistungsprobleme bei mehreren Includes

Wenn wir Leistungsfragen hören, die Probleme mit der Serverantwortzeit beinhalten, ist die Ursache des Problems häufig Abfragen mit mehreren Include-Anweisungen. Obwohl das Einschließen verwandter Entitäten in eine Abfrage leistungsfähig ist, ist es wichtig zu verstehen, was unter der Abdeckung geschieht.

Es dauert relativ lange, bis eine Abfrage mit mehreren Include-Anweisungen durch unseren internen Plancompiler geht, um den Store-Befehl zu erstellen. Der Großteil dieser Zeit wird damit verbracht, die resultierende Abfrage zu optimieren. Der generierte Store-Befehl enthält je nach Zuordnung eine äußere Verknüpfung oder Union für jeden Include. Abfragen wie diese bringen große verbundene Diagramme aus Ihrer Datenbank in einer einzigen Nutzlast ein, die alle Bandbreitenprobleme beheben, insbesondere wenn die Nutzlast stark redundiert (z. B. wenn mehrere Ebenen von Include verwendet werden, um Zuordnungen in die 1:n-Richtung zu durchlaufen).

Sie können nach Fällen suchen, in denen Ihre Abfragen zu große Nutzlasten zurückgeben, indem Sie auf die zugrunde liegende TSQL für die Abfrage zugreifen, indem Sie ToTraceString verwenden und den Store-Befehl in SQL Server Management Studio ausführen, um die Nutzlastgröße anzuzeigen. In solchen Fällen können Sie versuchen, die Anzahl der Include-Anweisungen in Ihrer Abfrage zu reduzieren, um nur die benötigten Daten einzusenden. Oder Sie können Ihre Abfrage in eine kleinere Sequenz von Unterabfragen aufteilen, z. B.:

**Vor dem Aufbrechen der Abfrage:**

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

**Nach dem Aufbrechen der Abfrage:**

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

Dies funktioniert nur bei nachverfolgten Abfragen, da wir die Fähigkeit des Kontexts nutzen, Identitätsauflösung und Zuordnungsfixup automatisch durchzuführen.

Wie beim verzögerten Laden wird der Kompromiss mehr Abfragen für kleinere Nutzlasten sein. Sie können auch Projektionen einzelner Eigenschaften verwenden, um explizit nur die Daten auszuwählen, die Sie von jeder Entität benötigen, aber Sie laden in diesem Fall keine Entitäten, und Aktualisierungen werden nicht unterstützt.

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a>8.2.3 Problemumgehung, um das verzögerte Laden von Eigenschaften zu erhalten

Entity Framework unterstützt derzeit kein verzögertes Laden von skalaren oder komplexen Eigenschaften. In Fällen, in denen Sie über eine Tabelle verfügen, die ein großes Objekt wie ein BLOB enthält, können Sie die Tabellenaufteilung verwenden, um die großen Eigenschaften in eine separate Entität zu trennen. Angenommen, Sie haben eine Produkttabelle, die eine varbinary Fotospalte enthält. Wenn Sie in Ihren Abfragen nicht häufig auf diese Eigenschaft zugreifen müssen, können Sie die Tabellenaufteilung verwenden, um nur die Teile der Entität einzuschalten, die Sie normalerweise benötigen. Die Entität, die das Produktfoto darstellt, wird nur geladen, wenn Sie es explizit benötigen.

Eine gute Ressource, die zeigt, wie die Tabellenaufteilung aktiviert werden kann, \< http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>ist Gil Finks Blogbeitrag "Table Splitting in Entity Framework": .

## <a name="9-other-considerations"></a>9 Sonstige Erwägungen

### <a name="91-server-garbage-collection"></a>9.1 Server-Garbage Collection

Bei einigen Benutzern kann es zu Ressourcenkonflikten kommen, die die erwartete Parallelität einschränkt, wenn der Garbage Collector nicht ordnungsgemäß konfiguriert ist. Wenn EF in einem Multithreadszenario oder in einer Anwendung verwendet wird, die einem serverseitigen System ähnelt, stellen Sie sicher, dass Sie die Servergarbage Collection aktivieren. Dies geschieht über eine einfache Einstellung in Ihrer Anwendungskonfigurationsdatei:

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

Dies sollte Den Threadkonflikt verringern und den Durchsatz in CPU-gesättigten Szenarien um bis zu 30 % erhöhen. Im Allgemeinen sollten Sie immer testen, wie sich Ihre Anwendung mit der klassischen Garbage Collection (die besser auf UI- und Client-seitige Szenarien abgestimmt ist) sowie der Server-Garbage Collection verhält.

### <a name="92-autodetectchanges"></a>9.2 AutoDetectChanges

Wie bereits erwähnt, zeigt Entity Framework möglicherweise Leistungsprobleme an, wenn der Objektcache über viele Entitäten verfügt. Bestimmte Vorgänge, z. B. Hinzufügen, Entfernen, Suchen, Posten und SaveChanges, lösen Aufrufe von DetectChanges aus, die je nach Anzahl des Objektcache eine große CPU-Menge verbrauchen können. Der Grund dafür ist, dass der Objektcache und der Objektstatus-Manager versuchen, bei jedem Vorgang, der in einem Kontext ausgeführt wird, so synchronisiert wie möglich zu bleiben, sodass die erzeugten Daten in einer Vielzahl von Szenarien garantiert korrekt sind.

Im Allgemeinen ist es eine gute Praxis, die automatische Änderungserkennung von Entity Framework während der gesamten Lebensdauer Ihrer Anwendung aktiviert zu lassen. Wenn Ihr Szenario durch die hohe CPU-Auslastung negativ beeinflusst wird und Ihre Profile darauf hinweisen, dass der Schuldige der Aufruf von DetectChanges ist, sollten Sie AutoDetectChanges vorübergehend im vertraulichen Teil des Codes deaktivieren:

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

Bevor Sie AutoDetectChanges deaktivieren, ist es gut zu verstehen, dass Entity Framework dadurch möglicherweise nicht mehr in der Lage ist, bestimmte Informationen über die Änderungen zu verfolgen, die an den Entitäten vorgenommen werden. Wenn sie falsch behandelt werden, kann dies zu Dateninkonsistenzen in Ihrer Anwendung führen. Weitere Informationen zum Deaktivieren von AutoDetectChanges finden Sie unter \< http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>.

### <a name="93-context-per-request"></a>9.3 Kontext pro Anforderung

Die Kontexte von Entity Framework sollen als kurzlebige Instanzen verwendet werden, um die optimale Leistungserfahrung zu bieten. Es wird erwartet, dass Kontexte kurzlebig und verworfen werden und daher sehr leicht sind und Metadaten nach Möglichkeit wiederverwendet werden. In Webszenarien ist es wichtig, dies im Auge zu behalten und keinen Kontext für mehr als die Dauer einer einzelnen Anforderung zu haben. Ebenso sollte in Nicht-Web-Szenarien der Kontext basierend auf Ihrem Verständnis der verschiedenen Ebenen der Zwischenspeicherung im Entity Framework verworfen werden. Im Allgemeinen sollte es vermieden werden, eine Kontextinstanz während der gesamten Lebensdauer der Anwendung sowie Kontexte pro Thread und statischen Kontexten zu haben.

### <a name="94-database-null-semantics"></a>9.4 Datenbank null Semantik

Entity Framework generiert standardmäßig SQL-Code\# mit C-Null-Vergleichssemantik. Betrachten Sie die folgende Beispielabfrage:

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

In diesem Beispiel vergleichen wir eine Reihe von nullierbaren Variablen mit nullierbaren Eigenschaften in der Entität, z. B. SupplierID und UnitPrice. In der generierten SQL für diese Abfrage wird gefragt, ob der Parameterwert mit dem Spaltenwert identisch ist oder ob sowohl der Parameter als auch die Spaltenwerte null sind. Dadurch wird die Art und Weise ausgeblendet, wie\# der Datenbankserver NULL-Dateien verarbeitet, und es wird eine konsistente C-Null-Erfahrung für verschiedene Datenbankanbieter bereitgestellt. Andererseits ist der generierte Code etwas verworren und funktioniert möglicherweise nicht gut, wenn die Anzahl der Vergleiche in der where-Anweisung der Abfrage auf eine große Anzahl anwächst.

Eine Möglichkeit, mit dieser Situation umzugehen, ist die Verwendung von Datenbank null Semantik. Beachten Sie, dass sich dies\# möglicherweise anders verhält als die C-Null-Semantik, da Entity Framework jetzt einfacheres SQL generiert, das die Art und Weise verfügbar macht, wie das Datenbankmodul NULL-Werte verarbeitet. Die Datenbanknullsemantik kann pro Kontext mit einer einzigen Konfigurationszeile für die Kontextkonfiguration aktiviert werden:

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

Kleine bis mittlere Abfragen zeigen keine spürbare Leistungsverbesserung bei verwendung der Datenbanknullsemantik an, aber der Unterschied wird bei Abfragen mit einer großen Anzahl potenzieller Nullvergleiche deutlicher.

In der obigen Beispielabfrage betrug der Leistungsunterschied weniger als 2 % in einem Mikrobenchmark, der in einer kontrollierten Umgebung ausgeführt wird.

### <a name="95-async"></a>9.5 Async

Entity Framework 6 hat die Unterstützung von asynchronen Vorgängen eingeführt, wenn sie unter .NET 4.5 oder höher ausgeführt werden. In den meisten Stellen profitieren Anwendungen mit E/A-Konflikten am meisten von der Verwendung asynchroner Abfrage- und Speichervorgänge. Wenn Ihre Anwendung nicht unter E/A-Konflikten leidet, wird die Verwendung von async im besten Fall synchron ausgeführt und gibt das Ergebnis in der gleichen Zeit zurück wie ein synchroner Aufruf, oder im schlimmsten Fall verschieben Sie einfach die Ausführung zu einer asynchronen Aufgabe und fügen Sie dem Abschluss Des Szenarios zusätzliche Zeit hinzu.

Informationen dazu, wie asynchrone Programmierung funktioniert, helfen Ihnen bei der Entscheidung, ob async die Leistung Ihrer Anwendung verbessern [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)wird. Weitere Informationen zur Verwendung von async-Vorgängen in Entity Framework finden Sie unter [Async Query und Save](~/ef6/fundamentals/async.md
).

### <a name="96-ngen"></a>9.6 NGEN

Entity Framework 6 wird nicht in der Standardinstallation von .NET Framework installiert. Daher sind die Entity Framework-Assemblys standardmäßig nicht NGEN'd, was bedeutet, dass der gesamte Entity Framework-Code denselben JIT-Kosten unterliegt wie jede andere MSIL-Assembly. Dies kann die F5-Erfahrung während der Entwicklung und auch das kalte Start-up Ihrer Anwendung in den Produktionsumgebungen beeinträchtigen. Um die CPU- und Speicherkosten von JIT'ing zu reduzieren, ist es ratsam, die Entity Framework-Images entsprechend zu nGEN. Weitere Informationen zur Verbesserung der Startleistung von Entity Framework 6 mit NGEN finden Sie unter Verbessern der [Startleistung mit NGen](~/ef6/fundamentals/performance/ngen.md).

### <a name="97-code-first-versus-edmx"></a>9.7 Code First versus EDMX

Entity Framework begründet das Problem der Impedanzkonfliktzwischenen zwischen objektorientierter Programmierung und relationalen Datenbanken, indem eine in-Memory-Darstellung des konzeptionellen Modells (der Objekte), des Speicherschemas (die Datenbank) und einer Zuordnung zwischen den beiden enthalten ist. Diese Metadaten werden als Entitätsdatenmodell oder kurz EDM bezeichnet. Aus diesem EDM leitet Entity Framework die Ansichten ab, um Daten von den Objekten im Speicher in die Datenbank und zurück zu umrunden.

Wenn Entity Framework mit einer EDMX-Datei verwendet wird, die formal das konzeptionelle Modell, das Speicherschema und die Zuordnung angibt, muss die Modellladephase nur überprüfen, ob das EDM korrekt ist (z. B. sicherstellen, dass keine Zuordnungen fehlen), generieren Sie dann die Ansichten, überprüfen Sie dann die Ansichten, und haben diese Metadaten für die Verwendung bereit. Erst dann kann eine Abfrage ausgeführt oder neue Daten im Datenspeicher gespeichert werden.

Der Code First-Ansatz ist im Kern ein ausgeklügelter Entity Data Model Generator. Das Entity Framework muss aus dem bereitgestellten Code ein EDM erstellen; Dies geschieht durch Die Analyse der am Modell beteiligten Klassen, das Anwenden von Konventionen und das Konfigurieren des Modells über die Fluent-API. Nach der Entwicklung des EDM verhält sich entity Framework im Wesentlichen genauso wie eine EDMX-Datei im Projekt. Das Erstellen des Modells aus Code First fügt daher zusätzliche Komplexität hinzu, die im Vergleich zu einem EDMX zu einer langsameren Startzeit für das Entity Framework führt. Die Kosten hängen vollständig von der Größe und Komplexität des Modells ab, das gerade erstellt wird.

Bei der Entscheidung für die Verwendung von EDMX im Vergleich zu Code First ist es wichtig zu wissen, dass die durch Code First eingeführte Flexibilität die Kosten für den erstmaligen Erstellen des Modells erhöht. Wenn Ihre Anwendung die Kosten dieser Erstlast aushalten kann, ist code First in der Regel der bevorzugte Weg.

## <a name="10-investigating-performance"></a>10 Untersuchung der Leistung

### <a name="101-using-the-visual-studio-profiler"></a>10.1 Verwenden des Visual Studio-Profilers

Wenn Sie Leistungsprobleme mit entity Framework haben, können Sie einen Profiler wie den in Visual Studio integrierten verwenden, um zu sehen, wo ihre Anwendung ihre Zeit verbringt. Dies ist das Tool, das wir verwendet haben, um die Kreisdiagramme im Blogbeitrag "Exploring the Performance of the ADO.NET Entity Framework - Part 1" zu generieren (der \< https://docs.microsoft.com/archive/blogs/adonet/exploring-the-performance-of-the-ado-net-entity-framework-part-1>) zeigt, wo Entity Framework seine Zeit während kalter und warmer Abfragen verbringt.

Der Blogbeitrag "Profiling Entity Framework using the Visual Studio 2010 Profiler" des Daten- und Modellierungskundenberatungsteams zeigt ein beispieldafür, wie sie den Profiler zur Untersuchung eines Leistungsproblems verwendet haben.\<https://docs.microsoft.com/archive/blogs/dmcat/profiling-entity-framework-using-the-visual-studio-2010-profiler>. Dieser Beitrag wurde für eine Windows-Anwendung geschrieben. Wenn Sie eine Webanwendung profilieren müssen, funktionieren die Tools Windows Performance Recorder (WPR) und Windows Performance Analyzer (WPA) möglicherweise besser als die Arbeit von Visual Studio aus. WPR und WPA sind Teil des Windows Performance Toolkit, das [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)im Windows Assessment and Deployment Kit ( enthalten ist).

### <a name="102-applicationdatabase-profiling"></a>10.2 Anwendungs-/Datenbankprofilerstellung

Tools wie der in Visual Studio integrierte Profiler geben an, wo Ihre Anwendung Zeit verbringt.Ein anderer Profilertyp ist verfügbar, der je nach Bedarf eine dynamische Analyse der ausgeführten Anwendung in der Produktion oder vor der Produktion durchführt und nach häufigen Fallstricken und Antimustern des Datenbankzugriffs sucht.

Zwei kommerziell erhältliche Profiler sind Entity \< http://efprof.com>) Framework Profiler \< http://ormprofiler.com>)( und ORMProfiler ( .

Wenn es sich bei Ihrer Anwendung um eine MVC-Anwendung mit Code First handelt, können Sie Den MiniProfiler von StackExchange verwenden. Scott Hanselman beschreibt dieses Tool \< http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>in seinem Blog unter: .

Weitere Informationen zum Profilieren der Datenbankaktivität Ihrer Anwendung finden Sie im Artikel des MSDN Magazine von Julie Lerman mit dem Titel [Profiling Database Activity in the Entity Framework](https://msdn.microsoft.com/magazine/gg490349.aspx).

### <a name="103-database-logger"></a>10.3 Datenbank-Logger

Wenn Sie Entity Framework 6 verwenden, sollten Sie auch die integrierte Protokollierungsfunktion verwenden. Die Database-Eigenschaft des Kontexts kann angewiesen werden, ihre Aktivität über eine einfache einzeilige Konfiguration zu protokollieren:

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

In diesem Beispiel wird die Datenbankaktivität in der Konsole protokolliert, aber&lt;die&gt; Log-Eigenschaft kann so konfiguriert werden, dass sie einen beliebigen Aktionszeichenfolgendelegat aufruft.

Wenn Sie die Datenbankprotokollierung ohne Neukompilierung aktivieren möchten und Entity Framework 6.1 oder höher verwenden, können Sie dies tun, indem Sie einen Interceptor in der Datei web.config oder app.config Ihrer Anwendung hinzufügen.

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

Weitere Informationen zum Hinzufügen von Protokollierung ohne \< http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>erneutes Kompilieren finden Sie unter .

## <a name="11-appendix"></a>11 Anhang

### <a name="111-a-test-environment"></a>11.1 A. Testumgebung

Diese Umgebung verwendet ein 2-Computer-Setup mit der Datenbank auf einem separaten Computer von der Clientanwendung. Maschinen befinden sich im selben Rack, daher ist die Netzwerklatenz relativ niedrig, aber realistischer als eine Umgebung mit einem Computer.

#### <a name="1111-app-server"></a>11.1.1 App-Server

##### <a name="11111-software-environment"></a>11.1.1.1 Softwareumgebung

-   Entity Framework 4-Softwareumgebung
    -   Betriebssystemname: Windows Server 2008 R2 Enterprise SP1.
    -   Visual Studio 2010 – Ultimativ.
    -   Visual Studio 2010 SP1 (nur für einige Vergleiche).
-   Entity Framework 5 und 6 Softwareumgebung
    -   Betriebssystemname: Windows 8.1 Enterprise
    -   Visual Studio 2013 – Ultimativ.

##### <a name="11112-hardware-environment"></a>11.1.1.2 Hardwareumgebung

-   Dual-Prozessor: Intel(R) Xeon(R) CPU L5520 W3530 bei 2,27 GHz, 2261 Mhz8 GHz, 4 Kern(e), 84 Logische Prozessoren).
-   2412 GB RamRAM.
-   136 GB SCSI250GB SATA 7200 rpm 3GB/s Laufwerk aufgeteilt in 4 Partitionen.

#### <a name="1112-db-server"></a>11.1.2 DB-Server

##### <a name="11121-software-environment"></a>11.1.2.1 Softwareumgebung

-   Betriebssystemname: Windows Server 2008 R28.1 Enterprise SP1.
-   SQL Server 2008 R22012.

##### <a name="11122-hardware-environment"></a>11.1.2.2 Hardwareumgebung

-   Einzelprozessor: Intel(R) Xeon(R) CPU L5520 bei 2,27 GHz, 2261 MhzES-1620 0 bei 3,60 GHz, 4 Kern(e), 8 logische Prozessoren.
-   824 GB RamRAM.
-   465 GB ATA500GB SATA 7200 rpm 6GB/s Laufwerk aufgeteilt in 4 Partitionen.

### <a name="112-b-query-performance-comparison-tests"></a>11.2 B. Abfrageleistungsvergleichstests

Das Northwind-Modell wurde zum Ausführen dieser Tests verwendet. Sie wurde aus der Datenbank mithilfe des Entity Framework-Designers generiert. Anschließend wurde der folgende Code verwendet, um die Leistung der Abfrageausführungsoptionen zu vergleichen:

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

### <a name="113-c-navision-model"></a>11.3 C. Navision Modell

Die Navision-Datenbank ist eine große Datenbank, die zum Demonmitwird von Microsoft Dynamics – NAV verwendet wird. Das generierte konzeptionelle Modell enthält 1005 Entitätssätze und 4227 Zuordnungssätze. Das im Test verwendete Modell ist "flach" – es wurde keine Vererbung hinzugefügt.

#### <a name="1131-queries-used-for-navision-tests"></a>11.3.1 Abfragen, die für Navision-Tests verwendet werden

Die Abfrageliste, die mit dem Navision-Modell verwendet wird, enthält 3 Kategorien von Entity SQL-Abfragen:

##### <a name="11311-lookup"></a>11.3.1.1 Suche

Eine einfache Suchabfrage ohne Aggregationen

-   Anzahl: 16232
-   Beispiel:

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a>11.3.1.2 SingleAggregating

Eine normale BI-Abfrage mit mehreren Aggregationen, aber keine Zwischensummen (einzelne Abfrage)

-   Anzahl: 2313
-   Beispiel:

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

Wobei MDF\_\_SessionLogin Time\_Max() im Modell wie folgt definiert ist:

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a>11.3.1.3 AggregationSubtotals

Eine BI-Abfrage mit Aggregationen und Zwischensummen (über alle Union)

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
