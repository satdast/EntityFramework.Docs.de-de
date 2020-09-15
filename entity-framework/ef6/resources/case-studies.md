---
title: Fallstudien für Entity Framework-EF6
description: Fallstudien für Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.openlocfilehash: 342929f70aa09704c934bd1d89e177f6953bfc43
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073820"
---
# <a name="microsoft-case-studies-for-entity-framework"></a>Microsoft-Fallstudien für Entity Framework
In den Fallstudien auf dieser Seite werden einige reale Produktionsprojekte hervorgehoben, die Entity Framework eingesetzt haben.
> [!NOTE]
> Die detaillierten Versionen dieser Fallstudien sind auf der Microsoft-Website nicht mehr verfügbar. Daher wurden die Verknüpfungen entfernt.

## <a name="epicor"></a>Epicor
Epicor ist ein großes globales Softwareunternehmen (mit mehr als 400 Entwicklern), das ERP-Lösungen (Enterprise Resource Planning) für Unternehmen in mehr als 150 Ländern entwickelt.
Das Hauptprodukt Epicor 9 basiert auf einer Dienst orientierten Architektur (SOA), die die .NET Framework verwendet.
Im Vergleich zu zahlreichen Kundenanforderungen zur Unterstützung von LINQ (Language Integrated Query) und der Reduzierung der Last auf ihren Back-End-SQL-Servern entschied sich das Team für ein Upgrade auf Visual Studio 2010 und den .NET Framework 4,0.
Mit dem Entity Framework 4,0 konnten diese Ziele erreichen und die Entwicklung und Wartung erheblich vereinfachen.
Insbesondere die Rich T4-Unterstützung des Entity Framework ermöglichte es Ihnen, die vollständige Kontrolle über den generierten Code zu übernehmen und automatisch in Funktionen zum Speichern von Funktionen zu erstellen, wie z. b. vorkompilierte Abfragen und Caching.

> "Wir haben vor kurzem einige Leistungstests mit vorhandenem Code durchgeführt, und wir konnten die Anforderungen auf SQL Server um 90 Prozent senken.
Dies liegt an der ADO.NET Entity Framework 4. " – Erik Johnson, Vice President, Produktforschung  

## <a name="veracity-solutions"></a>Verkraft Lösungen
Wenn Sie ein Softwaresystem für die Ereignis Planung erworben haben, das nur schwer gewartet und über langfristige Lösungen erweitert werden sollte, verwenden Sie Visual Studio 2010, um es als leistungsstarke und benutzerfreundliche Rich-Internet-Anwendung zu schreiben, die auf Silverlight 4 basiert.
Mithilfe von .NET RIA-Diensten konnten Sie schnell eine Dienst Schicht auf der Entity Framework erstellen, die die Code Duplizierung vermieden und eine allgemeine Validierungs-und Authentifizierungs Logik für alle Ebenen ermöglichte.  

> "Wir waren bei der ersten Einführung auf dem Entity Framework, und die Entity Framework 4 hat sich als noch besser erwiesen.
Tools wurden verbessert, und es ist einfacher, die EDMX-Dateien zu bearbeiten, die das konzeptionelle Modell, das Speichermodell und die Zuordnung zwischen diesen Modellen definieren... Mit dem Entity Framework kann ich die Datenzugriffs Schicht an einem Tag arbeiten – und die Datenzugriffs Schicht erstellen, wenn ich sie gehe.
Der Entity Framework ist unsere de-facto-Datenzugriffs Schicht. Ich weiß nicht, warum jemand das nicht verwendet. " – Joe McBride, Senior Developer

## <a name="nec-display-solutions-of-america"></a>NEC-Anzeige Lösungen von Amerika
NEC wollte den Markt für digitale, auf dem Markt basierende Werbung mit einer Lösung durchführen, um Werbung und Netzwerk Besitzern zu nutzen und seine eigenen Einnahmen zu steigern.
Um dies zu erreichen, wurde ein paar Webanwendungen gestartet, die die manuellen Prozesse automatisieren, die in einer herkömmlichen Werbekampagne erforderlich sind.
Die Websites wurden mit ASP.net, Silverlight 3, AJAX und WCF erstellt, zusammen mit den Entity Framework in der Datenzugriffs Ebene, um mit SQL Server 2008 zu kommunizieren.

> "Mit SQL Server haben wir gespürt, dass wir den Durchsatz, den wir für die Bereitstellung von Werbung und Netzwerken benötigten, mit Informationen in Echtzeit erhalten konnten, und die Zuverlässigkeit, um sicherzustellen, dass die Informationen in unseren unternehmenskritischen Anwendungen immer verfügbar wären"-Mike Corcoran, Director of IT

## <a name="darwin-dimensions"></a>Darwin-Dimensionen
Mit einer Vielzahl von Microsoft-Technologien hat das Team von Darwin die Entwicklung von evolver-einem Online-avatarportal festgelegt, mit dem Consumer beeindruckende, sehr große Avatare zur Verwendung in spielen, Animationen und sozialen Netzwerkseiten erstellen können.
Mit den Produktivitätsvorteilen der Entity Framework und dem Abrufen von Komponenten wie Windows Workflow Foundation (WF) und Windows Server AppFabric (einem hochgradig skalierbaren in-Memory-Anwendungscache) war das Team in der Lage, ein fantastisches Produkt in 35% weniger Entwicklungszeit bereitzustellen.
Obwohl Teammitglieder in mehrere Länder aufgeteilt werden, folgt das Team einem flexiblen Entwicklungsprozess mit wöchentlichen Releases.

 > "Wir versuchen nicht, Technologien für die Technologie zu entwickeln. Als Start ist es wichtig, dass wir Technologien nutzen, die Zeit und Geld sparen.
 .Net war die Wahl für eine schnelle, kostengünstige Entwicklung. " – Zachary Olsen, Architekt  

## <a name="silverware"></a>Silverware
Mit mehr als 15 Jahren Erfahrung bei der Entwicklung von POS-Lösungen (Point of Sale) für kleine und mittelständische Restaurant Gruppen hat das Entwicklungsteam von Silverware festgelegt, dass das Produkt mit weiteren Features auf Unternehmensebene erweitert werden muss, um größere Restaurantketten zu gewinnen.
Mit der neuesten Version der Entwicklungs Tools von Microsoft konnten Sie die neue Lösung viermal schneller als zuvor erstellen.
Wichtige neue Features, wie z. b. Linq und die Entity Framework, erleichtern das Wechseln von Crystal Reports zu SQL Server 2008 und SQL Server Reporting Services (SSRS), um Ihre Anforderungen an die Datenspeicherung und-Berichterstellung zu erfüllen.

> "Die effektive Datenverwaltung ist entscheidend für den Erfolg von Silverware – und aus diesem Grund haben wir uns entschieden, SQL Reporting zu übernehmen." -Nicholas romanidis, Director of IT/Software Engineering
