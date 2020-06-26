---
title: Code First-Migrationen in Team Umgebungen-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 4c2d9a95-de6f-4e97-9738-c1f8043eff69
ms.openlocfilehash: 7ab66a8ec29b60bb2e0102fbca5745d352b07325
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370408"
---
# <a name="code-first-migrations-in-team-environments"></a>Code First Migrations in Team Environments (Code First-Migrationen in Teamumgebungen)
> [!NOTE]
> In diesem Artikel wird davon ausgegangen, dass Sie wissen, wie Code First-Migrationen in einfachen Szenarien verwendet wird. Andernfalls müssen Sie [Code First-Migrationen](~/ef6/modeling/code-first/migrations/index.md) lesen, bevor Sie fortfahren.

## <a name="grab-a-coffee-you-need-to-read-this-whole-article"></a>Holen Sie sich einen Kaffee. Sie müssen diesen Artikel lesen.

Die Probleme in Team Umgebungen sind hauptsächlich das Zusammenführen von Migrationen, wenn zwei Entwickler in Ihrer lokalen Codebasis Migrationen generiert haben. Die Schritte zum Lösen dieser Probleme sind recht einfach, Sie erfordern jedoch ein solides Verständnis der Funktionsweise von Migrationen. Fahren Sie nicht einfach mit dem Ende fort – nehmen Sie sich die Zeit, den gesamten Artikel zu lesen, um sicherzustellen, dass Sie erfolgreich sind.

## <a name="some-general-guidelines"></a>Allgemeine Richtlinien

Bevor wir uns mit der Verwaltung von Zusammenführungs Migrationen befassen, die von mehreren Entwicklern generiert werden, finden Sie hier einige allgemeine Richtlinien, die Sie für einen erfolgreichen Einstieg einrichten

### <a name="each-team-member-should-have-a-local-development-database"></a>Jedes Teammitglied sollte über eine lokale Entwicklungs Datenbank verfügen.

Migrationen verwenden die ** \_ \_ migrationshistory** -Tabelle, um zu speichern, welche Migrationen auf die Datenbank angewendet wurden. Wenn Sie über mehrere Entwickler verfügen, die unterschiedliche Migrationen erzeugen, während Sie versuchen, auf dieselbe Datenbank zu Zielen (und somit eine ** \_ \_ migrationshistory** -Tabelle freigeben), wird die Migration sehr verwirrend.

Wenn Sie Teammitglieder haben, die keine Migrationen erzeugen, besteht natürlich kein Problem, dass Sie eine zentrale Entwicklungs Datenbank gemeinsam nutzen.

### <a name="avoid-automatic-migrations"></a>Vermeiden automatischer Migrationen

Die untere Zeile ist, dass automatische Migrationen anfänglich in den Team Umgebungen gut aussehen, aber in Wirklichkeit funktionieren Sie einfach nicht. Wenn Sie wissen möchten, lesen Sie – falls nicht, können Sie mit dem nächsten Abschnitt fortfahren.

Automatische Migrationen ermöglichen es Ihnen, das Datenbankschema so zu aktualisieren, dass es dem aktuellen Modell entspricht, ohne Code Dateien (Code basierte Migrationen) generieren zu müssen. Automatische Migrationen funktionieren in einer Team Umgebung sehr gut, wenn Sie Sie zuvor verwendet haben und nie Code basierte Migrationen generiert haben. Das Problem besteht darin, dass automatische Migrationen eingeschränkt sind und keine Reihe von Vorgängen verarbeiten können – Eigenschaften-/Spalten Umbenennungen, Verschieben von Daten in eine andere Tabelle usw. Um diese Szenarien zu behandeln, erzeugen Sie am Ende Code basierte Migrationen (und Bearbeiten des Gerüst Codes), die zwischen den Änderungen gemischt werden, die von automatischen Migrationen behandelt werden. Dadurch ist es nahezu unmöglich, Änderungen zusammenzuführen, wenn zwei Entwickler Migrationen einchecken.

## <a name="screencasts"></a>Screencasts

Wenn Sie lieber einen Screencast sehen möchten, als diesen Artikel zu lesen, befassen sich die beiden folgenden Videos mit dem gleichen Inhalt wie dieser Artikel.

### <a name="video-one-migrations---under-the-hood"></a>Video 1: "Migrationen" im Hintergrund

[Dieser Screencast](https://channel9.msdn.com/blogs/ef/migrations-under-the-hood) erläutert, wie Migrationen Informationen über das Modell nachverfolgt und verwendet, um Modelländerungen zu erkennen.

### <a name="video-two-migrations---team-environments"></a>Video 2: "Migrationen-Team Umgebungen"

Auf Grundlage der Konzepte aus dem vorherigen Video behandelt [dieser Screencast](https://channel9.msdn.com/blogs/ef/migrations-team-environments) die Probleme, die in einer Team Umgebung auftreten, und wie Sie Sie beheben können.

## <a name="understanding-how-migrations-works"></a>Funktionsweise von Migrationen

Der Schlüssel für die erfolgreiche Verwendung von Migrationen in einer Team Umgebung ist ein grundlegendes Verständnis dafür, wie Migrationen Informationen über das Modell zum Erkennen von Modelländerungen nachverfolgen und verwenden.

### <a name="the-first-migration"></a>Erste Migration

Wenn Sie die erste Migration zu Ihrem Projekt hinzufügen, führen Sie in der Paket-Manager-Konsole etwas wie " **Add-Migration First** " aus. Die von diesem Befehl ausgeführten Schritte auf hoher Ebene sind unten dargestellt.

![Erste Migration](~/ef6/media/firstmigration.png)

Das aktuelle Modell wird aus Ihrem Code berechnet (1). Die erforderlichen Datenbankobjekte werden dann vom Modell unterschiedlich berechnet (2) – da es sich hierbei um die erste Migration handelt, verwendet das Modell nur ein leeres Modell für den Vergleich. Die erforderlichen Änderungen werden an den Code-Generator weitergegeben, um den erforderlichen Migrations Code (3) zu erstellen, der dann Ihrer Visual Studio-Projekt Mappe hinzugefügt wird (4).

Zusätzlich zum eigentlichen Migrations Code, der in der Haupt Codedatei gespeichert ist, generiert Migrationen auch einige zusätzliche Code Behind-Dateien. Bei diesen Dateien handelt es sich um Metadaten, die von Migrationen verwendet werden. Sie sollten nicht bearbeitet werden. Eine dieser Dateien ist eine Ressourcen Datei (. resx), die eine Momentaufnahme des Modells zu dem Zeitpunkt enthält, zu dem die Migration generiert wurde. Sie sehen, wie dies im nächsten Schritt verwendet wird.

An diesem Punkt würden Sie wahrscheinlich " **Update-Database** " ausführen, um die Änderungen auf die Datenbank anzuwenden, und dann die Implementierung anderer Bereiche Ihrer Anwendung durchführen.

### <a name="subsequent-migrations"></a>Nachfolgende Migrationen

Wenn Sie zu einem späteren Zeitpunkt einige Änderungen an Ihrem Modell vornehmen – in unserem Beispiel fügen wir dem **Blog**eine **URL** -Eigenschaft hinzu. Anschließend geben Sie einen Befehl wie " **Add-Migration addurl** " aus, um eine Migration durchzusetzen, um die entsprechenden Daten Bank Änderungen anzuwenden. Die von diesem Befehl ausgeführten Schritte auf hoher Ebene sind unten dargestellt.

![Zweite Migration](~/ef6/media/secondmigration.png)

Ebenso wie das letzte Mal wird das aktuelle Modell aus Code (1) berechnet. Dieses Mal werden jedoch Migrationen durchlaufen, damit das vorherige Modell von der neuesten Migration (2) abgerufen wird. Diese beiden Modelle werden untersucht, um die erforderlichen Daten Bank Änderungen zu finden (3), und der Prozess wird wie zuvor abgeschlossen.

Dieser Prozess wird für alle weiteren Migrationen verwendet, die Sie dem Projekt hinzufügen.

### <a name="why-bother-with-the-model-snapshot"></a>Warum sollten Sie sich mit der Modell Momentaufnahme beschäftigen?

Sie Fragen sich vielleicht, warum EF mit der Modell Momentaufnahme zu tun hat – warum nicht nur die Datenbank. Wenn dies der Fall ist, lesen Sie weiter. Wenn Sie nicht interessiert sind, können Sie diesen Abschnitt überspringen.

EF gibt eine Reihe von Gründen für die Modell Momentaufnahme aus:

-   Dadurch kann Ihre Datenbank vom EF-Modell abweichen. Diese Änderungen können direkt in der Datenbank vorgenommen werden, oder Sie können den erstellten Code in den Migrationen ändern, um die Änderungen vorzunehmen. Dies sind einige Beispiele für diese Vorgehensweise:
    -   Sie möchten eine eingefügte und aktualisierte Spalte zu einer oder mehreren Tabellen hinzufügen, aber Sie möchten diese Spalten nicht in das EF-Modell einschließen. Wenn in der Datenbank Migrationen durchgeführt wurden, würde es immer wieder versuchen, diese Spalten jedes Mal zu löschen, wenn Sie ein Gerüst für eine Migration verwenden. Mit der Modell Momentaufnahme erkennt EF immer nur legitime Änderungen am Modell.
    -   Sie möchten den Text einer gespeicherten Prozedur ändern, die für Updates verwendet wird, um die Protokollierung einzubeziehen. Wenn die Migrationen diese gespeicherte Prozedur aus der Datenbank betrachten, wird diese regelmäßig wieder auf die Definition zurückgesetzt, die EF erwartet. Durch die Verwendung der Modell Momentaufnahme erstellt EF immer nur einen gerüstalten Code, um die gespeicherte Prozedur zu ändern, wenn Sie die Form der Prozedur im EF-Modell ändern.
    -   Die gleichen Prinzipien gelten für das Hinzufügen zusätzlicher Indizes, einschließlich zusätzlicher Tabellen in der Datenbank, die Zuordnung von EF zu einer Daten Bank Sicht, die sich über einer Tabelle befindet, usw.
-   Das EF-Modell enthält mehr als nur die Form der Datenbank. Das gesamte Modell ermöglicht Migrationen, Informationen zu den Eigenschaften und Klassen in Ihrem Modell und deren Zuordnung zu den Spalten und Tabellen zu überprüfen. Diese Informationen ermöglichen es, dass Migrationen in dem Code, den Sie Gerüsten, intelligenter werden. Wenn Sie z. b. den Namen der Spalte ändern, die eine Eigenschaft migriert, können Sie die Umbenennung erkennen, indem Sie sehen, dass es sich um dieselbe Eigenschaft handelt – etwas, das nicht ausgeführt werden kann, wenn Sie nur über das Datenbankschema verfügen. 

## <a name="what-causes-issues-in-team-environments"></a>Was verursacht Probleme in Team Umgebungen?

Der im vorherigen Abschnitt behandelte Workflow funktioniert hervorragend, wenn Sie ein einzelner Entwickler an einer Anwendung arbeiten. Es funktioniert auch gut in einer Team Umgebung, wenn Sie die einzige Person sind, die Änderungen am Modell vornimmt. In diesem Szenario können Sie Modelländerungen vornehmen, Migrationen generieren und diese an die Quell Code Verwaltung senden. Andere Entwickler können Ihre Änderungen synchronisieren und **Update-Database** ausführen, damit die Schema Änderungen angewendet werden.

Es treten Probleme auf, wenn mehrere Entwickler Änderungen am EF-Modell vornehmen und gleichzeitig an die Quell Code Verwaltung senden. Was EF fehlt, ist eine erstklassige Möglichkeit, ihre lokalen Migrationen mit Migrationen zusammenzuführen, die von einem anderen Entwickler seit der letzten Synchronisierung an die Quell Code Verwaltung übermittelt wurden.

## <a name="an-example-of-a-merge-conflict"></a>Ein Beispiel für einen Mergekonflikt

Zunächst sehen wir uns ein konkretes Beispiel für einen solchen Mergekonflikt an. Wir arbeiten mit dem Beispiel fort, das wir zuvor gesehen haben. Als Ausgangspunkt gehen wir davon aus, dass die Änderungen aus dem vorherigen Abschnitt vom ursprünglichen Entwickler eingeglichen wurden. Wir verfolgen zwei Entwickler nach, wenn Sie Änderungen an der Codebasis vornehmen.

Wir verfolgen das EF-Modell und die Migrationen durch eine Reihe von Änderungen. Als Ausgangspunkt haben beide Entwickler mit dem Quellcodeverwaltungs-Repository synchronisiert, wie in der folgenden Abbildung dargestellt.

![Startpunkt](~/ef6/media/startingpoint.png)

Developer \# 1 und Developer \# 2 nimmt nun einige Änderungen am EF-Modell in der lokalen Codebasis vor. Entwickler \# 1 fügt dem **Blog** – eine **Bewertungs** Eigenschaft hinzu und generiert eine **addrating** -Migration, um die Änderungen auf die Datenbank anzuwenden. Developer \# 2 fügt dem **Blog** – eine **Readers** -Eigenschaft hinzu und generiert die entsprechende Migration von **adressadern** . Beide Entwickler führen **Update-Database**aus, um die Änderungen auf Ihre lokalen Datenbanken anzuwenden und dann die Entwicklung der Anwendung fortzusetzen.

> [!NOTE]
> Migrationen wird ein Zeitstempel vorangestellt. Daher stellt unsere Grafik dar, dass die Migration der adressader von Developer \# 2 nach der Migration von Developer \# 1 erfolgt. Ob Entwickler \# 1 oder \# 2 die Migration zuerst generiert haben, ist kein Unterschied zu den Problemen bei der Arbeit in einem Team oder dem Verfahren zum Zusammenführen der Daten, die wir im nächsten Abschnitt betrachten werden.

![Lokale Änderungen](~/ef6/media/localchanges.png)

Es ist ein glücklicher Tag für Developer \# 1, da Sie Ihre Änderungen zuerst übermitteln. Da keine andere Person eingehakt hat, weil Sie Ihr Repository synchronisiert haben, können Sie Ihre Änderungen nur übermitteln, ohne eine Zusammenführung auszuführen.

![Übermitteln](~/ef6/media/submit.png)

Jetzt ist es an der Zeit \# , Entwickler 2 zu übermitteln. Sie sind nicht so glücklich. Da eine andere Person Änderungen gesendet hat, seit Sie synchronisiert wurden, müssen Sie die Änderungen abrufen und zusammenführen. Das Quell Code Verwaltungssystem kann die Änderungen wahrscheinlich auf Codeebene automatisch zusammenführen, da Sie sehr einfach sind. Der Status des \# lokalen Repository von Developer 2 nach der Synchronisierung wird in der folgenden Abbildung dargestellt. 

![Pull](~/ef6/media/pull.png)

Auf dieser Stufe \# kann Developer 2 **Update-Database** ausführen, wodurch die neue Migrations **AddRating** Migration (die noch nicht auf \# die Datenbank von Developer 2 angewendet wurde) erkannt und angewendet wird. Nun wird die Spalte **Bewertung** der Tabelle **Blogs** hinzugefügt, und die Datenbank ist mit dem Modell synchron.

Es gibt jedoch einige Probleme:

1.  Obwohl **Update-Database** die Migration **addrating** anwendet, wird auch eine Warnung ausgegeben: die *Datenbank kann nicht so aktualisiert werden, dass Sie mit dem aktuellen Modell identisch ist, weil ausstehende Änderungen vorhanden sind und die automatische Migration deaktiviert ist...*
    Das Problem besteht darin, dass in der in der letzten Migration (dem**adressader**) gespeicherten Modell Momentaufnahme die **Bewertungs** Eigenschaft im **Blog** fehlt (da Sie beim Generieren der Migration nicht Teil des Modells war). Code First erkennt, dass das Modell in der letzten Migration nicht mit dem aktuellen Modell identisch ist, und löst die Warnung aus.
2.  Das Ausführen der Anwendung führt zu einer InvalidOperationException, die besagt, dass sich*das Modell, das den bloggingcontext-Kontext unterstützt, seit der Erstellung der Datenbank geändert hat. Verwenden Sie Code First-Migrationen, um die Datenbank zu aktualisieren... "*
    Das Problem ist, dass die in der letzten Migration gespeicherte Modell Momentaufnahme nicht mit dem aktuellen Modell identisch ist.
3.  Schließlich würden wir erwarten, dass durch das Ausführen von " **Add-Migration** " nun eine leere Migration generiert wird (da es keine Änderungen gibt, die auf die Datenbank angewendet werden müssen). Doch da Migrationen das aktuelle Modell mit dem der letzten Migration vergleichen (was die **Bewertungs** Eigenschaft fehlt), wird ein weiterer **AddColumn** -Befehl zum Hinzufügen in der **Bewertungs** Spalte erstellt. Natürlich würde diese Migration bei **Update-Database** fehlschlagen, da die **Bewertungs** Spalte bereits vorhanden ist.

## <a name="resolving-the-merge-conflict"></a>Auflösen des Mergekonflikts

Die gute Nachricht ist, dass es nicht zu schwierig ist, den Merge manuell zu behandeln – vorausgesetzt, Sie haben ein Verständnis für die Funktionsweise von Migrationen. Wenn Sie also diesen Abschnitt übersprungen haben... Leider müssen Sie den Rest des Artikels zuerst lesen.

Es gibt zwei Möglichkeiten: am einfachsten ist es, eine leere Migration zu generieren, die das richtige aktuelle Modell als Momentaufnahme hat. Die zweite Option besteht darin, die Momentaufnahme in der letzten Migration zu aktualisieren, damit die richtige Modell Momentaufnahme vorhanden ist. Die zweite Option ist ein wenig schwieriger und kann nicht in jedem Szenario verwendet werden, aber Sie ist auch sauberer, da Sie keine zusätzliche Migration hinzufügen muss.

### <a name="option-1-add-a-blank-merge-migration"></a>Option 1: Hinzufügen einer leeren Migration "Merge"

Bei dieser Option generieren wir lediglich eine leere Migration, um sicherzustellen, dass die aktuelle Migration die richtige Modell Momentaufnahme enthält.

Diese Option kann unabhängig von der Person verwendet werden, die die letzte Migration generiert hat. In dem Beispiel, das dem Entwickler \# 2 folgt, wird die Zusammenführung übernommen und die letzte Migration generiert. Diese Schritte können jedoch auch verwendet werden, wenn Developer \# 1 die letzte Migration generiert hat. Diese Schritte gelten auch, wenn mehrere Migrationen beteiligt sind – wir haben gerade zwei untersucht, um Sie einfach zu halten.

Der folgende Prozess kann für diese Vorgehensweise verwendet werden, beginnend ab dem Zeitpunkt, an dem Sie wissen, dass Sie über Änderungen verfügen, die über die Quell Code Verwaltung synchronisiert werden müssen.

1.  Stellen Sie sicher, dass alle ausstehenden Modelländerungen in der lokalen Codebasis in eine Migration geschrieben wurden. Mit diesem Schritt wird sichergestellt, dass Sie keine legitimen Änderungen übersehen, wenn es an der Zeit ist, die leere Migration zu generieren.
2.  Mit Quell Code Verwaltung synchronisieren.
3.  Führen Sie **Update-Database** aus, um alle neuen Migrationen anzuwenden, die andere Entwickler eingecheckten.
    **_Hinweis:_** *Wenn Sie keine Warnungen vom Update-Database-Befehl erhalten, gab es keine neuen Migrationen von anderen Entwicklern, und es besteht keine Notwendigkeit, eine weitere Zusammenführung auszuführen.*
4.  Ausführen von **Add-Migration &lt; Pick \_ a \_ Name &gt; – ignorechanges** (z. b. **Add-Migration Merge – ignorechanges**). Dadurch wird eine Migration mit allen Metadaten (einschließlich einer Momentaufnahme des aktuellen Modells) generiert, aber alle Änderungen, die erkannt werden, beim Vergleich des aktuellen Modells mit der Momentaufnahme in den letzten Migrationen werden ignoriert (was bedeutet, dass Sie eine leere **up** -und **down** -Methode erhalten).
5.  Führen Sie **Update-Database** aus, um die neueste Migration mit den aktualisierten Metadaten erneut anzuwenden.
6.  Setzen Sie die Entwicklung fort, oder übermitteln Sie die Quell Code Verwaltung (nachdem Sie die Komponententests natürlich ausgeführt haben).

\#Nach diesem Ansatz wird der lokale Codebasis von Developer 2 angezeigt.

![Zusammenführen der Migration](~/ef6/media/mergemigration.png)

### <a name="option-2-update-the-model-snapshot-in-the-last-migration"></a>Option 2: Aktualisieren der Modell Momentaufnahme in der letzten Migration

Diese Option ist mit Option 1 sehr ähnlich, entfernt jedoch die zusätzliche leere Migration – da wir Sie sehen können, wer zusätzliche Code Dateien in der Projekt Mappe wünscht.

**Diese Vorgehensweise ist nur möglich, wenn die aktuelle Migration nur in der lokalen Codebasis vorhanden ist und noch nicht an die Quell Code Verwaltung übermittelt wurde (z. b. wenn die letzte Migration vom Benutzer generiert wurde, der die Zusammenführung durchgeführt hat)**. Das Bearbeiten der Metadaten von Migrationen, die andere Entwickler möglicherweise bereits auf Ihre Entwicklungs Datenbank angewendet haben – oder noch schlimmer auf eine Produktionsdatenbank – kann zu unerwarteten Nebeneffekten führen. Während des Vorgangs wird für die letzte Migration in der lokalen Datenbank ein Rollback ausgeführt und mit aktualisierten Metadaten erneut angewendet.

Während sich die letzte Migration nur in der lokalen Codebasis befinden muss, gibt es keine Einschränkungen hinsichtlich der Anzahl oder Reihenfolge der Migrationen, die Sie fortsetzen. Es können mehrere Migrationen von mehreren verschiedenen Entwicklern vorhanden sein, und die gleichen Schritte werden angewendet – wir haben gerade nur zwei untersucht, um Sie einfach zu halten.

Der folgende Prozess kann für diese Vorgehensweise verwendet werden, beginnend ab dem Zeitpunkt, an dem Sie wissen, dass Sie über Änderungen verfügen, die über die Quell Code Verwaltung synchronisiert werden müssen.

1.  Stellen Sie sicher, dass alle ausstehenden Modelländerungen in der lokalen Codebasis in eine Migration geschrieben wurden. Mit diesem Schritt wird sichergestellt, dass Sie keine legitimen Änderungen übersehen, wenn es an der Zeit ist, die leere Migration zu generieren.
2.  Synchronisierung mit der Quell Code Verwaltung.
3.  Führen Sie **Update-Database** aus, um alle neuen Migrationen anzuwenden, die andere Entwickler eingecheckten.
    **_Hinweis:_** *Wenn Sie keine Warnungen vom Update-Database-Befehl erhalten, gab es keine neuen Migrationen von anderen Entwicklern, und es besteht keine Notwendigkeit, eine weitere Zusammenführung auszuführen.*
4.  Führen Sie **Update-Database – targetmigration &lt; zweite \_ Letzte \_ Migration &gt; ** aus (in dem Beispiel, das folgt, wäre " **Update-Database" – targetmigration addrating**). Dadurch wird die Datenbank wieder in den Zustand der zweiten letzten Migration versetzt – wodurch die letzte Migration von der Datenbank deaktiviert wird.
    **_Hinweis:_** *dieser Schritt ist erforderlich, damit die Metadaten der Migration sicher bearbeitet werden können, da die Metadaten auch in der \_ \_ migrationshistorytable der Datenbank gespeichert werden. Aus diesem Grund sollten Sie diese Option nur verwenden, wenn die letzte Migration nur in der lokalen Codebasis erfolgt. Wenn die letzte Migration für andere Datenbanken angewendet wurde, müssten Sie auch ein Rollback durchführen und die letzte Migration erneut anwenden, um die Metadaten zu aktualisieren.* 
5.  Führen **Sie den vollständigen Namen der Add-Migration &lt; \_ \_ einschließlich \_ Zeitstempel \_ der \_ letzten \_ Migration aus** &gt; (in dem Beispiel, das folgt, wie etwa **Add-Migration 201311062215252- \_ adressader**).
    **_Hinweis:_** *Sie müssen den Zeitstempel einschließen, damit Migrationen wissen, dass Sie die vorhandene Migration bearbeiten möchten, anstatt eine neue zu Gerüstbau.*
    Dadurch werden die Metadaten für die letzte Migration entsprechend dem aktuellen Modell aktualisiert. Wenn der Befehl abgeschlossen ist, erhalten Sie die folgende Warnung, aber genau das ist das, was Sie möchten. "*Nur der Designer Code für die Migration der 201311062215252 \_ -adressader wurde neu gerüsteht. Verwenden Sie den Parameter "-Force", um die gesamte Migration neu zu erstellen.*
6.  Führen Sie **Update-Database** aus, um die neueste Migration mit den aktualisierten Metadaten erneut anzuwenden.
7.  Setzen Sie die Entwicklung fort, oder übermitteln Sie die Quell Code Verwaltung (nachdem Sie die Komponententests natürlich ausgeführt haben).

\#Nach diesem Ansatz wird der lokale Codebasis von Developer 2 angezeigt.

![Aktualisierte Metadaten](~/ef6/media/updatedmetadata.png)

## <a name="summary"></a>Zusammenfassung

Bei der Verwendung von Code First-Migrationen in einer Team Umgebung gibt es einige Herausforderungen. Ein grundlegendes Verständnis der Funktionsweise von Migrationen und einige einfache Ansätze zum Auflösen von Mergekonflikten erleichtern es Ihnen, diese Herausforderungen zu meistern.

Das grundlegende Problem sind falsche Metadaten, die in der letzten Migration gespeichert wurden. Dies bewirkt, dass Code First fälschlicherweise erkennen, dass das aktuelle Modell und das Datenbankschema nicht identisch sind, und dass bei der nächsten Migration falscher Code Gerüstbau ist. Diese Situation kann umgangen werden, indem eine leere Migration mit dem richtigen Modell erzeugt oder die Metadaten in der letzten Migration aktualisiert werden.
