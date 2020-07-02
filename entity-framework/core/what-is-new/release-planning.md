---
title: Releaseplanung für EF Core
author: ajcvickers
ms.date: 01/28/2020
uid: core/what-is-new/release_planning
ms.openlocfilehash: df933ac2462fcc18c53f49d862836fd2d6a4dd99
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370187"
---
# <a name="release-planning-process"></a>Die Releaseplanung

Benutzer fragen oftmals, wie bestimmte Features für bestimmte Releases ausgewählt werden.
In diesem Dokument finden Sie Informationen zum verwendeten Prozess.
Der Prozess wird ständig weiterentwickelt, wenn sich manche Pläne als noch vielversprechender herausstellen. Die grundsätzlichen Ideen bleiben jedoch konstant.

## <a name="different-kinds-of-releases"></a>Verschiedene Releasearten

Verschiedene Arten von Releases beinhalten verschiedene Arten von Änderungen.
Das bedeutet wiederum, dass sich die Releaseplanung von Releaseart zu Releaseart unterscheidet.

### <a name="patch-releases"></a>Patchreleases

Bei Patchreleases wird nur der „Patch“-Teil einer Version geändert.
Bei EF Core 3.1.**1** handelt es sich beispielsweise um ein Release, bei dem Probleme repariert werden, die in EF Core 3.1.**0** festgestellt wurden.

Patchreleases dienen dazu, kritische Fehler für Kunden zu beheben.
Das bedeutet, dass Patchreleases keine neuen Features einführen.
Besondere Umstände ausgenommen dürfen Patchreleases keine API-Änderungen beinhalten.

Es muss sich um einen sehr schwerwiegenden Fehler handeln, damit er im Rahmen eines Patchrelease behoben wird.
Das liegt daran, dass ein Patchrelease keinesfalls neue Fehler einführen darf.
Deshalb geht es beim Entscheidungsprozess um hohen Nutzen und niedriges Risiko.

In folgenden Fällen wird ein Problem sehr wahrscheinlich per Patchrelease behoben:
  * Ein Fehler betrifft mehrere Kunden
  * Beim Fehler handelt es sich um eine Regression eines früheren Release
  * Der Fehler führt zu Datenbeschädigung

In folgenden Fällen ist es eher unwahrscheinlich, dass ein Problem per Patchrelease behoben wird:
  * Es gibt ausreichend anderweitige Möglichkeiten zur Problemumgehung
  * Die Behebung eines Fehlers könnte sehr wahrscheinlich zu Fehlern an anderer Stelle führen
  * Beim Fehler handelt es sich um einen selten auftretenden Fall

Die Wahrscheinlichkeit, dass ein Fehler per Patchrelease behoben wird, wird im Laufe der Lebenszeit eines Release mit [langfristigem Support (Long-Term Support, LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) immer geringer. Das liegt daran, dass für LTS-Releases die Stabilität im Vordergrund steht.

Die endgültige Entscheidung darüber, ob ein Fehler per Patchrelease behoben wird oder nicht, wird von den bei Microsoft für .NET Verantwortlichen getroffen.

### <a name="minor-releases"></a>Nebenversionen

Bei Nebenversionen bleibt der „Hauptteil“ einer Version unberührt.
Bei EF Core 3.**1**.0 handelt es sich beispielsweise um ein Release, für das Verbesserungen an EF Core 3.**0**.0. vorgenommen wurden.

Folgendes trifft auf Nebenversionen zu:
* Nebenversionen sollen die Qualität und Features vorheriger Releases verbessern
* Hauptversionen enthalten in der Regel Fehlerbehebungen sowie neue Features
* Nebenversionen enthalten keine absichtlichen Breaking Changes
* Für Nebenversionen werden einige Vorschauversion vor dem eigentlichen Release an NuGet gepusht

### <a name="major-releases"></a>Hauptversionen

Bei Hauptversionen ändert sich die Versionsnummer des „Hauptteils“ von EF.
Bei EF Core **3**.0.0 handelt es sich beispielsweise um eine Hauptversion, bei der im Vergleich zu EF Core 2.2.x wesentliche Unterschiede bestehen.

Folgendes trifft auf Hauptversionen zu:
* Nebenversionen sollen die Qualität und Features vorheriger Releases verbessern
* Hauptversionen enthalten in der Regel Fehlerbehebungen sowie neue Features
  * Bei einigen der neuen Features kann es sich um erhebliche Änderungen der Funktionsweise von EF Core handeln
* Hauptversionen enthalten in der Regel absichtliche Breaking Changes
  * Breaking Changes sind aufgrund immer neuer Erkenntnisse ein essentieller Bestandteil für die Weiterentwicklung von EF Core
  * Breaking Changes werden jedoch nur sehr bedacht eingeführt, da sie sich stark auf Kunden auswirken können. In der Vergangenheit wurden Breaking Changes bei Microsoft möglicherweise zu inflationär genutzt. Ab sofort sollen Änderungen an Apps in Form von Breaking Changes minimiert werden. Außerdem sollen Änderungen reduziert werden, die zu Fehlern bei Datenbankanbietern und Erweiterungen führen.
* Für Hauptversionen werden viele Vorschauversion vor dem eigentlichen Release an NuGet gepusht

## <a name="planning-for-majorminor-releases"></a>Planung von Haupt- und Nebenversionen

### <a name="github-issue-tracking"></a>Problemverfolgung mit GitHub

Bei GitHub ([https://github.com/dotnet/efcore](https://github.com/dotnet/efcore)) handelt es sich um die sogenannte „Source of Truth“ für alle Planungen im Zusammenhang mit EF Core.

Probleme auf GitHub verfügen über Folgendes:

* Ein Zustand
  * Als [Open](https://github.com/dotnet/efcore/issues) (Nicht geklärt) markierte Probleme wurden noch nicht behoben.
  * Als [Closed](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aclosed) (Geschlossen) markierte Probleme wurden bereits behoben.
    * Alle behobenen Probleme werden [mit „closed-fixed“ (Geschlossen-Behoben) markiert](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Aclosed-fixed+is%3Aclosed). Ein Problem, das als „closed-fixed“ markiert wurde, ist behoben und zusammengeführt, wurde allerdings möglicherweise noch nicht per Release veröffentlicht.
    * Weitere `closed-`-Bezeichnungen weisen darauf hin, dass es andere Gründe dafür gab, ein Problem als geschlossen zu markieren. Duplikate werden beispielsweise als „closed-duplicate“ (Geschlossen-Duplikat) markiert.
* Ein Typ
  * [Bugs](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-bug) (Fehler) stehen für Fehler.
  * [Enhancements](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-enhancement) (Verbesserungen) stehen für neue Features oder eine verbesserte Funktionalität bestehender Features.
* Ein Meilenstein
  * [Probleme ohne Meilenstein](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+no%3Amilestone) werden noch vom Team diskutiert. Die Entscheidung darüber, wie mit dem Problem umgegangen werden soll, wurde noch nicht getroffen, oder eine bereits getroffene Entscheidung wird noch angezweifelt.
  * Bei [Problemen im Backlog-Meilenstein](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog) handelt es sich um Elemente, an denen das EF-Team im Rahmen eines zukünftigen Release eventuell arbeiten möchte.
    * Probleme im Backlog können [mit „consider-for-next-release“ (Für das nächste Release berücksichtigen) markiert](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Aconsider-for-next-release) werden, was bedeutet, dass das jeweilige Arbeitselement im Rahmen des nächsten Release wahrscheinlich berücksichtigt werden wird.
  * Bei nicht geklärten Fehlern in einem Meilenstein mit Versionsnummer handelt es sich um Elemente, an denen das Team im Rahmen der entsprechenden Version arbeiten möchte. Hinter [diesem Link](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A5.0.0) finden Sie beispielsweise die Probleme, an denen im Rahmen von EF Core 5.0 gearbeitet werden soll.
  * Bei als geklärt gekennzeichneten Problemen in einem Meilenstein mit Versionsnummer handelt es sich um Probleme, die im Rahmen der entsprechenden Version behoben wurden. Das Release der Version hat aber möglicherweise noch nicht stattgefunden. Hinter [diesem Link](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A3.0.0+is%3Aclosed) finden Sie beispielsweise die Probleme, die im Rahmen von EF Core 3.0 behoben wurden.
* Abstimmungen
  * In einer Abstimmung können Sie auf beste Weise deutlich machen, dass ein Fehler für Sie relevant ist.
  * Fügen Sie einem Problem einfach einen Daumen nach oben (👍) hinzu, um abzustimmen. Hinter [diesem Link](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) finden Sie beispielweise die Probleme, für die am häufigsten abgestimmt wurde.
  * Sie können auch gern einen Kommentar verfassen und konkrete Gründe nennen, warum Sie ein bestimmtes Feature benötigen, wenn Sie Ihren Standpunkt dadurch besser verdeutlichen können. Kommentare wie „+1“ sind in diesem Fall jedoch nicht hilfreich.

### <a name="the-planning-process"></a>Planungsprozess

Beim Planungsprozess muss mehr berücksichtigt werden, als einfach nur die am häufigsten angeforderten Features aus dem Backlog abzuarbeiten.
Das liegt daran, dass Microsoft auf viele verschiedene Arten Feedback von verschiedenen Beteiligten erhält.
Der konkrete Umfang eines Release wird dann auf Grundlage folgender Stichpunkte ausgearbeitet:

* Input von Kunden
* Input anderer Beteiligter
* Strategische Ausrichtung
* Verfügbare Ressourcen
* Zeitplan

Dabei stellen sich u. a. folgende Fragen:

1. **Wie viele Entwickler werden das Feature zukünftig verwenden, und welche Verbesserungen für die Anwendungen oder Benutzererfahrung bietet es?** Um diese Frage zu beantworten, sammeln wir Feedback aus verschiedensten Quellen, zu denen auch Kommentare und abgegebene Stimmen zählen. Ein weiterer zu berücksichtigender Punkt sind konkrete Verpflichtungen wichtigen Kunden gegenüber.

2. **Welche Problemumgehungen können genutzt werden, wenn dieses Feature noch nicht implementiert wird?** Viele Entwickler können z.B. eine Verknüpfungstabelle zuordnen, um die fehlende Unterstützung für native m:n-Unterstützung zu umgehen. Natürlich wollen nicht alle Entwickler diese Option nutzen, doch ausschlaggebend ist, dass viele es können.

3. **Wird durch die Implementierung dieses Features die Architektur von EF Core weiterentwickelt, sodass die Implementierung anderer Features erleichtert wird?** In der Regel werden Features bevorzugt, die als Bausteine für andere Features dienen können. Beispielsweise können Eigenschaftenbehälterentitäten beim Entwickeln der m:n-Unterstützung helfen, und Entitätskonstruktoren haben unsere Lazy Loading-Unterstützung ermöglicht.

4. **Ist dieses Feature ein Erweiterungspunkt?** In der Regel werden Erweiterungspunkte regulären Features vorgezogen, da Entwickler damit eigene Verhaltensweisen verknüpfen und so einen Teil der fehlenden Funktionalität ersetzen können.

5. **Welche Synergien erzeugt das Feature in Kombination mit anderen Produkten?** Normalerweise werden Features bevorzugt, die die Verwendbarkeit von EF Core mit anderen Produkten wie .NET Core, der aktuellen Version von Visual Studio, Microsoft Azure usw. deutlich verbessern.

6. **Welche Qualifikationen haben die Personen, die am Feature arbeiten, und wie lassen sich diese Ressourcen am besten einsetzen?** Alle Mitglieder des EF-Teams und alle Mitwirkenden aus der Community können auf unterschiedliche Erfahrungsschätze in verschiedenen Bereichen zurückgreifen. Dementsprechend muss geplant werden. Selbst wenn „alle mit anpacken“ und an einem bestimmten Feature wie GroupBy-Übersetzungen oder m:n arbeiten möchten, ist das nicht immer der beste Ansatz.
