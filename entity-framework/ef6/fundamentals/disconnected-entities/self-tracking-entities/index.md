---
title: Entitäten mit Selbstnachverfolgung – EF6
description: Entitäten mit Selbstnachverfolgung in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/disconnected-entities/self-tracking-entities/index
ms.openlocfilehash: 56bcd79d0c9949b9ccc06efe4dbc5a36bb88b158
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065549"
---
# <a name="self-tracking-entities"></a>Entitäten mit Selbstnachverfolgung

> [!IMPORTANT]
> Es wird nicht mehr empfohlen, die Vorlage für Entitäten mit Selbstnachverfolgung zu verwenden. Die Vorlage ist nur für die Unterstützung vorhandener Anwendungen weiterhin verfügbar. Wenn für Ihre Anwendung die Arbeit mit getrennten Diagrammen von Entitäten erforderlich ist, sollten Sie daher Alternativen erwägen, wie z.B. [nachverfolgbare Entitäten](https://trackableentities.github.io/). Diese Technologie ähnelt den Entitäten mit Selbstnachverfolgung und wird von der Community aktiver entwickelt. Alternativ dazu können Sie auch benutzerdefinierten Code mithilfe von APIs auf niedriger Ebene zur Änderungsnachverfolgung schreiben.

In einer auf Entity Framework basierenden Anwendung ist ein Kontext für das Nachverfolgen von Änderungen in den Objekten zuständig. Verwenden Sie anschließend zum Speichern der Änderungen in der Datenbank die SaveChanges-Methode. Bei der Arbeit mit N-schichtigen Anwendungen wird die Verbindung der Entitätsobjekte zum Kontext in der Regel getrennt. Sie müssen dann entscheiden, wie Änderungen nachverfolgt und an den Kontext zurückgemeldet werden. Mithilfe von Entitäten mit Selbstnachverfolgung (Self-Tracking Entities, STEs) können Sie Änderungen in jeder Ebene nachverfolgen und anschließend zur Speicherung in einem Kontext wiedergeben.  

Verwenden Sie STEs nur dann, wenn der Kontext auf einer Ebene, auf der die Änderungen am Objektdiagramm vorgenommen werden, nicht verfügbar ist. Wenn der Kontext jedoch verfügbar ist, ist die Verwendung von STEs nicht notwendig, da der Kontext selbst Änderungsnachverfolgungen vornimmt.  

Dieses Vorlagenelement generiert zwei TT-Dateien (Textvorlagen):  

- Mit der Datei **\<model name\>.tt** werden die Entitätstypen und eine Hilfsklasse generiert, die die Logik zur Änderungsnachverfolgung enthält, die von Entitäten mit Selbstnachverfolgung verwendet wird. Zudem enthält sie die Erweiterungsmethoden, mit denen der Status von Entitäten mit Selbstnachverfolgung festgelegt werden kann.  
- Mit der Datei **\<model name\>.Context.tt** werden ein abgeleiteter Kontext und eine Erweiterungsklasse generiert, die **ApplyChanges**-Methoden für die Klassen **ObjectContext** und **ObjectSet** enthält. Diese Methoden untersuchen die Änderungsnachverfolgungsinformationen, die im Diagramm der Entitäten mit Selbstnachverfolgung enthalten sind, um die Vorgänge abzuleiten, die ausgeführt werden müssen, um die Änderungen in der Datenbank zu speichern.  

## <a name="get-started"></a>Erste Schritte  

Informationen zu den ersten Schritten finden Sie auf der Seite [Self-Tracking Entities Walkthrough (Exemplarische Vorgehensweise zu Entitäten mit Selbstnachverfolgung)](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough).  

## <a name="functional-considerations-when-working-with-self-tracking-entities"></a>Funktionale Überlegungen für die Verwendung von Entitäten mit Selbstnachverfolgung  
> [!IMPORTANT]
> Es wird nicht mehr empfohlen, die Vorlage für Entitäten mit Selbstnachverfolgung zu verwenden. Die Vorlage ist nur für die Unterstützung vorhandener Anwendungen weiterhin verfügbar. Wenn für Ihre Anwendung die Arbeit mit getrennten Diagrammen von Entitäten erforderlich ist, sollten Sie daher Alternativen erwägen, wie z.B. [nachverfolgbare Entitäten](https://trackableentities.github.io/). Diese Technologie ähnelt den Entitäten mit Selbstnachverfolgung und wird von der Community aktiver entwickelt. Alternativ dazu können Sie auch benutzerdefinierten Code mithilfe von APIs auf niedriger Ebene zur Änderungsnachverfolgung schreiben.

Beachten Sie bei der Verwendung von Entitäten mit Selbstnachverfolgung Folgendes:  

- Stellen Sie sicher, dass das Clientprojekt einen Verweis auf die Assembly mit den Entitätstypen enthält. Wenn Sie dem Clientprojekt nur den Dienstverweis hinzufügen, verwendet das Clientprojekt die WCF-Proxytypen und nicht die tatsächlichen Entitätstypen mit Selbstnachverfolgung. Dies bedeutet, dass Sie nicht die automatisierten Benachrichtigungsfunktionen abrufen, die die Nachverfolgung der Entitäten auf dem Client verwalten. Wenn Sie die Entitätstypen absichtlich nicht einschließen möchten, müssen Sie Informationen der Änderungsnachverfolgung auf dem Client manuell festlegen, damit die Änderungen an den Dienst zurückgesendet werden.  
- Aufrufe des Dienstvorgangs sollten zustandslos sein und eine neue Instanz des Objektkontexts erstellen. Es wird auch empfohlen, Objektkontext in einem **Using**-Block zu erstellen.  
- Wenn Sie das auf dem Client geänderte Diagramm an den Dienst senden und anschließend auf dem Client weiterhin dasselbe Diagramm verwenden möchten, müssen Sie das Diagramm auf dem Client manuell durchlaufen und die **AcceptChanges**-Methode für alle Objekte aufrufen, um die Änderungsnachverfolgung zurückzusetzen.  

    > Wenn Objekte im Diagramm Eigenschaften mit datenbankgenerierten Werten (z.B. Identitäts- oder Parallelitätswerte) enthalten, ersetzt Entity Framework die Werte dieser Eigenschaften durch die datenbankgenerierten Werte, nachdem die **SaveChanges**-Methode aufgerufen wurde. Sie können den Dienstvorgang implementieren, um gespeicherte Objekte oder eine Liste generierter Eigenschaftenwerte für die Objekte an den Client zurückzugeben. Der Client müsste dann die Objektinstanzen oder Objekteigenschaftenwerte durch die vom Dienstvorgang zurückgegebenen Objekte oder Eigenschaftenwerte ersetzen.  
- Das Zusammenführen von Diagrammen aus mehreren Dienstanforderungen kann zu Objekten mit doppelten Schlüsselwerten im resultierenden Diagramm führen. Entity Framework entfernt die Objekte mit doppelten Schlüsseln nicht, wenn Sie die **ApplyChanges**-Methode aufrufen. Stattdessen wird eine Ausnahme ausgelöst. Halten Sie sich zur Vermeidung von Diagrammen mit doppelten Schlüsselwerten an eines der im folgenden Blog beschriebenen Muster: [Self-Tracking Entities: ApplyChanges and duplicate entities (Entitäten mit Selbstnachverfolgung: ApplyChanges und doppelte Entitäten)](https://go.microsoft.com/fwlink/?LinkID=205119&clcid=0x409).  
- Wenn Sie die Beziehung zwischen Objekten durch Festlegen der Fremdschlüsseleigenschaft ändern, wird die Verweisnavigationseigenschaft auf NULL festgelegt und nicht mit der entsprechenden Prinzipalentität auf dem Client synchronisiert. Nachdem das Diagramm an den Objektkontext angefügt wurde (z.B. nach dem Aufrufen der **ApplyChanges**-Methode), werden die Fremdschlüssel- und Navigationseigenschaften synchronisiert.  

    > Die nicht erfolgte Synchronisierung einer Verweisnavigationseigenschaft mit dem entsprechenden Prinzipalobjekt kann ein Problem sein, wenn Sie die Löschweitergabe für die Fremdschlüsselbeziehung festgelegt haben. Wenn Sie das Prinzipalobjekt löschen, wird der Löschvorgang nicht an die abhängigen Objekte weitergegeben. Wenn Sie Löschweitergaben festgelegt haben, verwenden Sie Navigationseigenschaften zum Ändern der Beziehungen, statt die Fremdschlüsseleigenschaft festzulegen.  
- Mit Entitäten mit Selbstnachverfolgung kann kein Lazy Loading durchgeführt werden.  
- Eine binäre Serialisierung sowie die Serialisierung in ASP.NET-Zustandsverwaltungsobjekte werden nicht von Entitäten mit Selbstnachverfolgung unterstützt. Sie können jedoch die Vorlage anpassen, um die Unterstützung der binären Serialisierung hinzuzufügen. Weitere Informationen finden Sie unter [Using Binary Serialization and ViewState with Self-Tracking Entities (Verwenden von binärer Serialisierung und ViewState mit Entitäten mit Selbstnachverfolgung)](https://go.microsoft.com/fwlink/?LinkId=199208).  

## <a name="security-considerations"></a>Sicherheitsüberlegungen  

Die folgenden Sicherheitsüberlegungen sollten Sie beim Arbeiten mit Entitäten mit Selbstnachverfolgung berücksichtigen:  

- Ein Dienst sollte Anforderungen nicht vertrauen, Daten von einem nicht vertrauenswürdigen Client oder über einen nicht vertrauenswürdigen Kanal abzurufen oder zu aktualisieren. Ein Client muss authentifiziert werden: Es sollte ein sicherer Kanal oder ein Nachrichtenumschlag verwendet werden. Clientanforderungen zum Aktualisieren oder Abrufen von Daten müssen überprüft werden, um sicherzustellen, dass sie den erwarteten und ordnungsgemäßen Änderungen für das jeweilige Szenario entsprechen.  
- Vertrauliche Informationen sollten nicht als Entitätsschlüssel verwendet werden (z. B. Sozialversicherungsnummern). Auf diese Weise wird das Risiko verringert, versehentlich vertrauliche Informationen in den Entitätsdiagrammen mit Selbstnachverfolgung für einen nicht vollständig vertrauenswürdigen Client zu serialisieren. Bei unabhängigen Zuordnungen kann zudem der ursprüngliche Schlüssel einer Entität an den Client gesendet werden, der sich auf den zu serialisierenden Schlüssel bezieht.  
- Um die Weitergabe von Ausnahmemeldungen mit vertraulichen Daten an die Clientebene zu verhindern, sollten Aufrufe von **ApplyChanges** und **SaveChanges** auf Serverebene von Ausnahmebehandlungscode umschlossen sein.  
