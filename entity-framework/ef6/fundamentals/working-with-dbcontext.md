---
title: Arbeiten mit dbcontext-EF6
description: Arbeiten mit dbcontext in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: b0e6bddc-8a87-4d51-b1cb-7756df938c23
uid: ef6/fundamentals/working-with-dbcontext
ms.openlocfilehash: 7845d401cb0b8910cbfbba80eca2fd098c051b7d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618208"
---
# <a name="working-with-dbcontext"></a>Arbeiten mit DbContext

Um Entity Framework zum Abfragen, einfügen, aktualisieren und Löschen von Daten mithilfe von .NET-Objekten verwenden zu können, müssen Sie zunächst [ein Modell erstellen](xref:ef6/modeling/index) , das die im Modell definierten Entitäten und Beziehungen den Tabellen in einer Datenbank zuordnet.

Sobald Sie über ein Modell verfügen, wird die primäre Klasse, mit der Ihre Anwendung interagiert, `System.Data.Entity.DbContext` (häufig als Kontext Klasse bezeichnet) behandelt. Sie können einen mit einem Modell verknüpften dbcontext für Folgendes verwenden:
- Schreiben und Ausführen von Abfragen   
- Materialisieren von Abfrage Ergebnissen als Entitäts Objekte
- An diesen Objekten vorgenommene Änderungen nachverfolgen
- Objektänderungen in der Datenbank beibehalten
- Binden von Objekten im Arbeitsspeicher an UI-Steuerelemente

Diese Seite enthält eine Anleitung zum Verwalten der Kontext Klasse.  

## <a name="defining-a-dbcontext-derived-class"></a>Definieren einer von dbcontext abgeleiteten Klasse  

Die empfohlene Vorgehensweise, um mit Kontext zu arbeiten, besteht darin, eine Klasse zu definieren, die von dbcontext abgeleitet ist und dbset-Eigenschaften verfügbar macht, die Sammlungen der angegebenen Entitäten im Kontext darstellen. Wenn Sie mit dem EF-Designer arbeiten, wird der Kontext für Sie generiert. Wenn Sie mit Code First arbeiten, schreiben Sie den Kontext in der Regel selbst.  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

Wenn Sie über einen Kontext verfügen, können Sie über diese Eigenschaften Abfragen, hinzufügen (using- `Add` oder- `Attach` Methoden) oder (mithilfe von `Remove` ) Entitäten im Kontext entfernen. Der Zugriff `DbSet` auf eine Eigenschaft für ein Kontext Objekt stellt eine Start Abfrage dar, die alle Entitäten des angegebenen Typs zurückgibt. Beachten Sie, dass beim Zugreifen auf eine Eigenschaft die Abfrage nicht ausgeführt wird. Eine Abfrage wird ausgeführt, wenn Folgendes gilt:  

- Sie wird durch eine `foreach` (C#)-Anweisung oder eine `For Each` (Visual Basic)-Anweisung aufgezählt.  
- Sie wird durch einen Auflistungs Vorgang, z `ToArray` . b., oder, aufgezählt `ToDictionary` `ToList` .  
- LINQ-Operatoren, z `First` . b. oder, `Any` werden im äußersten Teil der Abfrage angegeben.  
- Eine der folgenden Methoden wird aufgerufen: die `Load` Erweiterungsmethode,, `DbEntityEntry.Reload`  `Database.ExecuteSqlCommand` und `DbSet<T>.Find` , wenn eine Entität mit dem angegebenen Schlüssel nicht gefunden wurde, die bereits im Kontext geladen wurde.  

## <a name="lifetime"></a>Lebensdauer  

Die Lebensdauer des Kontexts beginnt, wenn die Instanz erstellt wird, und endet, wenn die Instanz entweder verworfen oder Garbage Collection durchgeführt wird. Verwenden Sie **die Verwendung von, wenn alle** Ressourcen, die die Kontext Steuerelemente haben, am Ende des Blocks verworfen werden sollen. Wenn Sie **mit**verwenden, erstellt der Compiler automatisch einen try/after-Block und ruft **die Freigabe im letzten Block auf** .  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

Im folgenden finden Sie einige allgemeine Richtlinien für die Entscheidung über die Lebensdauer des Kontexts:  

- Verwenden Sie bei der Arbeit mit Webanwendungen eine Kontext Instanz pro Anforderung.  
- Verwenden Sie beim Arbeiten mit Windows Presentation Foundation (WPF) oder Windows Forms eine Kontext Instanz pro Formular. Auf diese Weise können Sie Funktionen zur Änderungs Nachverfolgung verwenden, die der Kontext bereitstellt.  
- Wenn die Kontext Instanz von einem Container für die Abhängigkeitsinjektion erstellt wird, liegt es in der Regel in der Verantwortung des Containers, den Kontext zu verwerfen.
- Wenn der Kontext im Anwendungscode erstellt wird, sollten Sie den Kontext verwerfen, wenn er nicht mehr benötigt wird.  
- Beim Arbeiten mit einem Kontext mit langer Ausführungszeit sollten Sie Folgendes beachten:  
    - Wenn Sie weitere Objekte und ihre Verweise in den Arbeitsspeicher laden, kann sich die Arbeitsspeicher Nutzung des Kontexts schnell erhöhen. Dies kann zu Leistungseinbußen führen.  
    - Der Kontext ist nicht Thread sicher und sollte daher nicht für mehrere Threads freigegeben werden, die gleichzeitig daran arbeiten.
    - Wenn eine Ausnahme bewirkt, dass sich der Kontext in einem nicht wiederherstellbaren Zustand befindet, kann die gesamte Anwendung beendet werden.  
    - Je größer der zeitliche Abstand zwischen der Abfrage der Daten und ihrer Aktualisierung, desto höher ist die Wahrscheinlichkeit, dass Parallelitätsprobleme auftreten.  

## <a name="connections"></a>Verbindungen  

Standardmäßig verwaltet der Kontext Verbindungen mit der Datenbank. Der Kontext wird geöffnet und schließt die Verbindungen nach Bedarf. Der Kontext öffnet z. b. eine Verbindung, um eine Abfrage auszuführen, und schließt dann die Verbindung, wenn alle Resultsets verarbeitet wurden.  

In bestimmten Fällen ist es erforderlich, den Zeitpunkt für das Öffnen und Schließen der Verbindung genauer zu steuern. Wenn Sie z. b. mit SQL Server Compact arbeiten, wird häufig empfohlen, für die Lebensdauer der Anwendung eine separate offene Verbindung mit der Datenbank beizubehalten, um die Leistung zu verbessern. Mit der `Connection`-Eigenschaft können Sie diesen Vorgang manuell steuern.  
