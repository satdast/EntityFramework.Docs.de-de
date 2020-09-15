---
title: Code First-Migrationen mit einer vorhandenen Datenbank EF6
description: Code First-Migrationen mit einer vorhandenen Datenbank in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/existing-database
ms.openlocfilehash: 5dd38f3fb9a59805a9b23b7da5e770995866f715
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072108"
---
# <a name="code-first-migrations-with-an-existing-database"></a>Code First-Migrationen mit einer vorhandenen Datenbank
> [!NOTE]
> **EF 4.3** und höher: die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 4,1 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.

In diesem Artikel wird die Verwendung von Code First-Migrationen mit einer vorhandenen Datenbank behandelt, die nicht von Entity Framework erstellt wurde.

> [!NOTE]
> In diesem Artikel wird davon ausgegangen, dass Sie wissen, wie Code First-Migrationen in einfachen Szenarien verwendet wird. Andernfalls müssen Sie [Code First-Migrationen](xref:ef6/modeling/code-first/migrations/index) lesen, bevor Sie fortfahren.

## <a name="screencasts"></a>Screencasts

Wenn Sie lieber einen Screencast sehen möchten, als diesen Artikel zu lesen, befassen sich die beiden folgenden Videos mit dem gleichen Inhalt wie dieser Artikel.

### <a name="video-one-migrations---under-the-hood"></a>Video 1: "Migrationen" im Hintergrund

[Dieser Screencast](https://channel9.msdn.com/blogs/ef/migrations-under-the-hood) erläutert, wie Migrationen Informationen über das Modell nachverfolgt und verwendet, um Modelländerungen zu erkennen.

### <a name="video-two-migrations---existing-databases"></a>Video 2: "Migrationen-vorhandene Datenbanken"

Wenn Sie auf die Konzepte aus dem vorherigen Video aufbauen, wird in [diesem Screencast](https://channel9.msdn.com/blogs/ef/migrations-existing-databases) erläutert, wie Migrationen mit einer vorhandenen Datenbank aktiviert und verwendet werden.

## <a name="step-1-create-a-model"></a>Schritt 1: Erstellen eines Modells

Der erste Schritt besteht darin, ein Code First Modell zu erstellen, das Ihre vorhandene Datenbank als Ziel verwendet. Im Thema [Code First zu einer vorhandenen Datenbank](xref:ef6/modeling/code-first/workflows/existing-database) finden Sie eine ausführliche Anleitung zur Vorgehensweise.

>[!NOTE]
> Es ist wichtig, die restlichen Schritte in diesem Thema auszuführen, bevor Sie Änderungen am Modell vornehmen, die Änderungen am Datenbankschema erfordern. Die folgenden Schritte erfordern, dass das Modell mit dem Datenbankschema synchron ist.

## <a name="step-2-enable-migrations"></a>Schritt 2: Aktivieren von Migrationen

Der nächste Schritt besteht darin, Migrationen zu aktivieren. Hierzu können Sie den Befehl **enable-Migrationen** in der Paket-Manager-Konsole ausführen.

Mit diesem Befehl wird ein Ordner in der Projekt Mappe mit dem Namen Migrationen erstellt und eine einzelne Klasse als Konfiguration bezeichnet. Die Konfigurations Klasse ist der Ort, an dem Sie Migrationen für Ihre Anwendung konfigurieren. Weitere Informationen dazu finden Sie im [Code First-Migrationen](xref:ef6/modeling/code-first/migrations/index) Thema.

## <a name="step-3-add-an-initial-migration"></a>Schritt 3: Hinzufügen einer ersten Migration

Nachdem Migrationen erstellt und auf die lokale Datenbank angewendet wurden, können Sie diese Änderungen auch auf andere Datenbanken anwenden. Beispielsweise kann die lokale Datenbank eine Testdatenbank sein, und Sie können die Änderungen letztendlich auch auf eine Produktionsdatenbank und/oder andere Entwickler Testdatenbanken anwenden. Es gibt zwei Optionen für diesen Schritt, und Sie sollten auswählen, ob das Schema anderer Datenbanken leer ist oder derzeit mit dem Schema der lokalen Datenbank übereinstimmt.

-   **Option 1: vorhandenes Schema als Ausgangspunkt verwenden.** Sie sollten diesen Ansatz verwenden, wenn in Zukunft andere Datenbanken, auf die Migrationen angewendet werden, das gleiche Schema wie Ihre lokale Datenbank haben. Beispielsweise können Sie diese verwenden, wenn die lokale Testdatenbank derzeit V1 der Produktionsdatenbank entspricht und Sie diese Migrationen später anwenden, um die Produktionsdatenbank auf v2 zu aktualisieren.
-   **Option 2: Verwenden Sie eine leere Datenbank als Ausgangspunkt.** Sie sollten diesen Ansatz verwenden, wenn andere Datenbanken, auf die in Zukunft migriert wird, leer sind (oder noch nicht vorhanden sind). Beispielsweise können Sie diese verwenden, wenn Sie mit der Entwicklung ihrer Anwendung mit einer Testdatenbank, aber ohne Migrationen begonnen haben und später eine Produktionsdatenbank von Grund auf neu erstellen möchten.

### <a name="option-one-use-existing-schema-as-a-starting-point"></a>Option 1: vorhandenes Schema als Ausgangspunkt verwenden

Code First-Migrationen verwendet eine Momentaufnahme des Modells, das in der letzten Migration gespeichert ist, um Änderungen am Modell zu erkennen (Ausführliche Informationen hierzu finden Sie in [Code First-Migrationen in Team Umgebungen](xref:ef6/modeling/code-first/migrations/teams)). Da wir davon ausgehen, dass die Datenbanken bereits über das Schema des aktuellen Modells verfügen, generieren wir eine leere Migration (No-OP), die das aktuelle Modell als Momentaufnahme hat.

1.  Führen Sie den Befehl **Add-Migration InitialCreate – ignorechanges** in der Paket-Manager-Konsole aus. Dadurch wird eine leere Migration mit dem aktuellen Modell als Momentaufnahme erstellt.
2.  Führen Sie den Befehl **Update-Database** in der Paket-Manager-Konsole aus. Dadurch wird die InitialCreate-Migration auf die Datenbank angewendet. Da die tatsächliche Migration keine Änderungen enthält, wird der migrationshistory-Tabelle einfach eine Zeile hinzugefügt, die \_ \_ anzeigt, dass diese Migration bereits angewendet wurde.

### <a name="option-two-use-empty-database-as-a-starting-point"></a>Option 2: leere Datenbank als Ausgangspunkt verwenden

In diesem Szenario sind Migrationen erforderlich, um die gesamte Datenbank von Grund auf neu erstellen zu können – einschließlich der Tabellen, die bereits in der lokalen Datenbank vorhanden sind. Wir generieren eine InitialCreate-Migration, die Logik zum Erstellen des vorhandenen Schemas enthält. Anschließend wird die vorhandene Datenbank so aussehen, wie diese Migration bereits angewendet wurde.

1.  Führen Sie den Befehl **Add-Migration InitialCreate** in der Paket-Manager-Konsole aus. Dadurch wird eine Migration erstellt, um das vorhandene Schema zu erstellen.
2.  Kommentieren Sie den gesamten Code in der up-Methode der neu erstellten Migration aus. Auf diese Weise können wir die Migration auf die lokale Datenbank anwenden, ohne zu versuchen, alle Tabellen neu zu erstellen usw., die bereits vorhanden sind.
3.  Führen Sie den Befehl **Update-Database** in der Paket-Manager-Konsole aus. Dadurch wird die InitialCreate-Migration auf die Datenbank angewendet. Da die tatsächliche Migration keine Änderungen enthält (da wir Sie vorübergehend auskommentiert haben), wird der migrationshistory-Tabelle einfach eine Zeile hinzugefügt, die \_ \_ anzeigt, dass diese Migration bereits angewendet wurde.
4.  Kommentieren Sie den Code in der up-Methode aus. Dies bedeutet, dass beim Anwenden dieser Migration auf zukünftige Datenbanken das Schema, das bereits in der lokalen Datenbank vorhanden war, durch Migrationen erstellt wird.

## <a name="things-to-be-aware-of"></a>Worauf Sie achten sollten

Es gibt einige Dinge, die Sie beachten müssen, wenn Sie Migrationen für eine vorhandene Datenbank verwenden.

### <a name="defaultcalculated-names-may-not-match-existing-schema"></a>Standard-/berechnete Namen dürfen nicht mit vorhandenem Schema identisch sein

Migrationen geben explizit Namen für Spalten und Tabellen an, wenn ein Gerüst für Migrationen festgelegt wird. Es gibt jedoch andere Datenbankobjekte, für die Migrationen beim Anwenden der Migrationen einen Standardnamen für berechnen. Dies schließt Indizes und FOREIGN KEY-Einschränkungen ein. Wenn Sie ein vorhandenes Schema als Ziel haben, Stimmen diese berechneten Namen möglicherweise nicht mit den in der Datenbank vorhandenen Daten identisch.

Im folgenden finden Sie einige Beispiele für den Fall, dass Sie sich Folgendes bewusst sein müssen:

**Wenn Sie "Option 1: vorhandenes Schema als Ausgangspunkt verwenden" aus Schritt 3 verwendet haben:**

-   Wenn zukünftige Änderungen im Modell eine Änderung oder Löschung eines Datenbankobjekts erfordern, das anders benannt ist, müssen Sie die Gerüst Migration ändern, um den richtigen Namen anzugeben. Die Migrations-APIs verfügen über einen optionalen Name-Parameter, mit dem Sie dies tun können.
    Beispielsweise kann Ihr vorhandenes Schema über eine Post-Tabelle mit einer BlogId-Fremdschlüssel Spalte verfügen, die einen Index namens indexfk \_ BlogId aufweist. Standardmäßig erwarten Migrationen jedoch, dass dieser Index "IX BlogId" genannt wird \_ . Wenn Sie eine Änderung an Ihrem Modell vornehmen, die dazu führt, dass dieser Index gelöscht wird, müssen Sie den aufgerücherten DropIndex-Befehl ändern, um den indexfk- \_ BlogId-Namen anzugeben.

**Wenn Sie "Option 2: leere Datenbank als Ausgangspunkt verwenden" aus Schritt 3 verwendet haben:**

-   Wenn Sie versuchen, die Down-Methode der anfänglichen Migration (d. h. das Zurücksetzen auf eine leere Datenbank) für die lokale Datenbank auszuführen, tritt möglicherweise ein Fehler auf, weil bei der Migration versucht wird, Indizes und Fremdschlüssel Einschränkungen mithilfe falscher Namen zu löschen. Dies wirkt sich nur auf die lokale Datenbank aus, da andere Datenbanken mithilfe der up-Methode der anfänglichen Migration von Grund auf neu erstellt werden.
    Wenn Sie die vorhandene lokale Datenbank auf einen leeren Zustand herabstufen möchten, ist es am einfachsten, dies manuell durchzuführen, indem Sie entweder die Datenbank löschen oder alle Tabellen löschen. Nach dieser anfänglichen Herabstufung werden alle Datenbankobjekte mit den Standardnamen neu erstellt, sodass dieses Problem nicht mehr auftritt.
-   Wenn zukünftige Änderungen im Modell eine Änderung oder Löschung eines Datenbankobjekts erfordern, das anders benannt ist, funktioniert dies nicht für Ihre vorhandene lokale Datenbank – da die Namen nicht den Standardwerten entsprechen. Allerdings funktioniert es für Datenbanken, die von Grund auf neu erstellt wurden, da Sie die Standardnamen verwendet haben, die von Migrationen ausgewählt werden.
    Sie können diese Änderungen entweder manuell für Ihre lokale vorhandene Datenbank vornehmen, oder Sie sollten in Erwägung ziehen, dass Ihre Datenbank von Grund auf neu erstellt wird – wie auf anderen Computern.
-   Datenbanken, die mit der up-Methode der anfänglichen Migration erstellt wurden, können sich geringfügig von der lokalen Datenbank unterscheiden, da die berechneten Standardnamen für Indizes und FOREIGN KEY-Einschränkungen verwendet werden. Möglicherweise verfügen Sie auch über zusätzliche Indizes, da Migrationen standardmäßig Indizes für Fremdschlüssel Spalten erstellen – Dies ist möglicherweise nicht der Fall in der ursprünglichen lokalen Datenbank.

### <a name="not-all-database-objects-are-represented-in-the-model"></a>Nicht alle Datenbankobjekte werden im Modell dargestellt.

Datenbankobjekte, die nicht Teil des Modells sind, werden nicht von Migrationen behandelt. Dies kann Sichten, gespeicherte Prozeduren, Berechtigungen, Tabellen enthalten, die nicht Teil des Modells sind, zusätzliche Indizes usw.

Im folgenden finden Sie einige Beispiele für den Fall, dass Sie sich Folgendes bewusst sein müssen:

-   Unabhängig von der Option, die Sie in "Schritt 3" gewählt haben, ist es bei zukünftigen Änderungen im Modell erforderlich, diese zusätzlichen Objekte zu ändern oder zu löschen. Wenn Sie z. b. eine Spalte mit einem zusätzlichen Index löschen, wissen Migrationen nicht, ob der Index gelöscht werden soll. Sie müssen diese manuell der Gerüst Migration hinzufügen.
-   Wenn Sie "Option 2: leere Datenbank als Ausgangspunkt verwenden" verwendet haben, werden diese zusätzlichen Objekte nicht von der up-Methode der anfänglichen Migration erstellt.
    Sie können die nach-oben-und die nach-unten-Methode ändern, um diese zusätzlichen Objekte bei Bedarf zu übernehmen. Für Objekte, die in der Migrations-API nicht unterstützt werden – z. b. Ansichten – können Sie die [SQL](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.sql.aspx) -Methode verwenden, um unformatierten SQL-Daten auszuführen und zu erstellen/zu löschen.
