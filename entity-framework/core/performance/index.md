---
title: Einführung in die Leistung – EF Core
description: Leistungsleitfaden zur effizienten Verwendung von Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/miscellaneous/performance/index
ms.openlocfilehash: 14400d81ea3c93e2ebf40e8e585a457abf31478f
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657685"
---
# <a name="introduction-to-performance"></a>Einführung in die Leistung

Die Datenbankleistung ist ein breites und komplexes Thema, das einen ganzen Stapel von Komponenten umfasst: die Datenbank, das Netzwerk, der Datenbanktreiber und die Datenzugriffsebenen, wie z. B. EF Core. Obwohl allgemeine Ebenen und O/RMs wie EF Core die Anwendungsentwicklung erheblich vereinfachen und die Wartbarkeit von Anwendungen verbessern, können sie unter Umständen opak sein und leistungskritische interne Details verbergen, wie z. B. die SQL-Anweisung, die gerade ausgeführt wird. In diesem Artikel wird eine Übersicht darüber geboten, wie Sie mit EF Core eine gute Leistung erzielen und häufige Fehler vermeiden, die die Anwendungsleistung beeinträchtigen können.

## <a name="identify-bottlenecks-and-measure-measure-measure"></a>Erkennen von Engpässen und gründliches Messen

Wie immer bei der Leistung ist es wichtig, keine übereilte Optimierung vorzunehmen, wenn Daten zu einem Problem fehlen. Denken Sie an die Worte von Donald Knuth: „Eine vorzeitige Optimierung ist die Wurzel alles Bösen“. Im Artikel [Leistungsdiagnose](xref:core/performance/performance-diagnosis) werden verschiedene Methoden erläutert, um herauszufinden, wo in der Datenbanklogik Ihre Anwendung Zeit verbringt und wie Sie bestimmte problematische Bereiche ermitteln können. Wenn eine langsame Abfrage ermittelt wurde, kann die Lösungsfindung beginnen: Fehlt Ihrer Datenbank ein Index? Sollten Sie andere Abfragemuster ausprobieren?

Legen Sie Codebenchmarks und mögliche Alternativen immer selbst fest. Der Artikel zur Leistungsdiagnose enthält einen Beispielvergleichstest mit BenchmarkDotNet, den Sie als Vorlage für Ihre eigenen Benchmarks verwenden können. Gehen Sie nicht davon aus, dass allgemeine öffentliche Benchmarks unverändert auf Ihren speziellen Anwendungsfall angewendet werden können. Eine Vielzahl von Faktoren wie die Datenbanklatenz, die Abfragekomplexität und die tatsächlichen Datenmengen in Ihren Tabellen können sich maßgeblich darauf auswirken, welche Lösung die beste ist. Viele öffentliche Benchmarks werden z. B. unter idealen Netzwerkbedingungen, in denen die Latenzzeit zur Datenbank fast null beträgt, und mit extrem einfachen Abfragen ausgeführt, die kaum Verarbeitung (oder Datenträger-E/A) auf Datenbankseite erfordern. Obwohl sie für den Vergleich des Mehraufwands zur Laufzeit, den verschiedene Datenzugriffsebenen aufweisen, von Bedeutung sind, sind die offengelegten Unterschiede in einer realen Anwendung, in der die Datenbank die tatsächliche Arbeit ausführt und die Latenz zur Datenbank ein bedeutender Leistungsfaktor ist, vernachlässigbar.

## <a name="aspects-of-data-access-performance"></a>Aspekte der Datenzugriffsleistung

Die gesamte Datenzugriffsleistung kann in die folgenden allgemeinen Kategorien aufgegliedert werden:

* **Reine Datenbankleistung:** Bei relationalen Datenbanken übersetzt EF die LINQ-Abfragen der Anwendung in die SQL-Anweisungen, die von der Datenbank ausgeführt werden. Diese SQL-Anweisungen können mehr oder weniger effizient ausgeführt werden. Der richtige Index an der richtigen Stelle kann beträchtliche Unterschiede in der SQL-Leistung hervorrufen. Auch das Neuschreiben Ihrer LINQ-Abfrage kann bewirken, dass EF eine bessere SQL-Abfrage generiert.
* **Netzwerkdatenübertragung:** Wie bei jedem Netzwerksystem ist es wichtig, die Datenmenge zu begrenzen, die bei der Übertragung hin und her gesendet wird. Dadurch wird sichergestellt, dass Sie nur Daten senden und laden, die Sie tatsächlich benötigen, und beim Laden verwandter Entitäten den Effekt der sogenannten „kartesischen Explosion“ vermeiden.
* **Netzwerkroundtrips:** Abgesehen von der Datenmenge zählen auch die Netzwerkroundtrips, da die Zeit, die für die Ausführung einer Abfrage in der Datenbank benötigt wird, von der Zeit beeinflusst werden kann, die Pakete für die Übertragung zwischen Ihrer Anwendung und Ihrer Datenbank benötigen. Der Roundtripmehraufwand hängt stark von Ihrer Umgebung ab. Je weiter entfernt der Datenbankserver ist, desto höher sind die Latenz und die Kosten für jeden Roundtrip. Seit der Einführung der Cloud sind Anwendungen zunehmend weiter von der Datenbank entfernt, und kommunikationsintensive Anwendungen, die zu viele Roundtrips ausführen, weisen eine schlechtere Leistung auf. Daher ist es wichtig, genau zu verstehen, wann Ihre Anwendung die Datenbank kontaktiert, wie viele Roundtrips sie ausführt und ob diese Zahl minimiert werden kann.
* **EF-Mehraufwand zur Laufzeit:** Schließlich führt EF selbst zu einem zusätzlichen Laufzeitmehraufwand für Datenbankvorgänge: EF muss Ihre Abfragen aus LINQ in SQL kompilieren (obwohl dies normalerweise nur einmal erfolgen sollte), die Änderungsnachverfolgung ruft ebenfalls einen gewissen Mehraufwand hervor (kann jedoch deaktiviert werden) usw. In der Praxis ist der Mehraufwand von EF für reale Anwendungen oftmals wahrscheinlich unerheblich, da die Gesamtzeit vornehmlich von der Ausführungszeit der Abfragen in der Datenbank und der Netzwerklatenz abhängt. Es ist jedoch wichtig zu verstehen, wie Ihre Optionen lauten und wie Sie einige Fehler vermeiden können.

## <a name="know-whats-happening-under-the-hood"></a>Abläufe unter der Haube

EF ermöglicht es Entwicklern, sich auf die Geschäftslogik zu konzentrieren, indem SQL erstellt wird und Ergebnisse materialisiert sowie andere Aufgaben durchgeführt werden. Wie jede andere Ebene oder Abstraktion wird tendenziell verborgen, was unter der Haube geschieht, z. B. die tatsächlichen SQL-Abfragen, die gerade ausgeführt werden. Die Leistung ist nicht in allen Anwendungen notwendigerweise ein kritischer Aspekt. In den Anwendungen, in denen dies der Fall ist, ist es jedoch von entscheidender Bedeutung, dass der Entwickler versteht, welche Vorteile EF bietet: Überprüfen von ausgehenden SQL-Abfragen, Verfolgen von Roundtrips zum Sicherstellen, dass das N+1-Problem nicht auftritt usw.

## <a name="cache-outside-the-database"></a>Zwischenspeichern außerhalb der Datenbank

Die effizienteste Methode für die Interaktion mit einer Datenbank besteht darin, überhaupt nicht mit ihr zu interagieren. Anders ausgedrückt: Wenn der Datenbankzugriff als Leistungsengpass in der Anwendung angezeigt wird, kann es sinnvoll sein, bestimmte Ergebnisse außerhalb der Datenbank zwischenzuspeichern, um die Anforderungen zu minimieren. Obwohl das Zwischenspeichern die Komplexität steigert, ist es ein besonders wesentlicher Bestandteil jeder skalierbaren Anwendung: Während die Logikschicht durch Hinzufügen zusätzlicher Server, die eine höhere Auslastung bewältigen, problemlos skaliert werden kann, ist eine Skalierung der Datenbankschicht meist weitaus komplizierter.
