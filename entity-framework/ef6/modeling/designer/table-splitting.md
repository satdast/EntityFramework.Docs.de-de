---
title: Aufteilung der Designer Tabelle-EF6
description: Aufteilen von Designer Tabellen in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 452f17c3-9f26-4de4-9894-8bc036e23b0f
uid: ef6/modeling/designer/table-splitting
ms.openlocfilehash: 55642d7c3b4efc7467e8a40e68b4144728df270f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620377"
---
# <a name="designer-table-splitting"></a>Aufteilen von Designer Tabellen
In dieser exemplarischen Vorgehensweise wird veranschaulicht, wie Sie mehrere Entitäts Typen einer einzelnen Tabelle zuordnen, indem Sie ein Modell mit dem Entity Framework Designer (EF-Designer) ändern.

Ein Grund, warum Sie die Tabellen Aufteilung verwenden möchten, verzögert das Laden einiger Eigenschaften, wenn Lazy Loading zum Laden der Objekte verwendet wird.Sie können die Eigenschaften, die möglicherweise sehr große Datenmengen enthalten, in eine separate Entität aufteilen und Sie nur bei Bedarf laden.

Die folgende Abbildung zeigt die Hauptfenster, die bei der Arbeit mit dem EF-Designer verwendet werden.

![EF-Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>Voraussetzungen

Um die exemplarische Vorgehensweise nachzuvollziehen, benötigen Sie Folgendes:

- Eine aktuelle Version von Visual Studio.
- Die [Beispieldatenbank "School](xref:ef6/resources/school-database)".

## <a name="set-up-the-project"></a>Einrichten des Projekts

In dieser exemplarischen Vorgehensweise wird Visual Studio 2012 verwendet.

-   Öffnen Sie Visual Studio 2012.
-   Zeigen Sie im Menü **Datei** auf **Neu**, und klicken Sie dann auf **Projekt**.
-   Klicken Sie im linken Bereich auf Visual C \# , und wählen Sie dann die Vorlage Konsolenanwendung aus.
-   Geben Sie **tablesplittingsample** als Namen für das Projekt ein, und klicken Sie auf **OK**.

## <a name="create-a-model-based-on-the-school-database"></a>Erstellen eines Modells auf der Grundlage der Datenbank "School"

-   Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen, zeigen Sie auf **Hinzufügen**, und klicken Sie dann auf **Neues Element**.
-   Wählen Sie im linken Menü **Daten** aus, und wählen Sie dann im Bereich Vorlagen die Option **ADO.NET Entity Data Model** aus.
-   Geben Sie als Dateiname **tablesplittingmodel. edmx** ein, und klicken Sie dann auf **Hinzufügen**.
-   Wählen Sie im Dialogfeld Modell Inhalte auswählen die Option **aus Datenbank generieren aus**, und klicken Sie dann auf **Weiter.**
-   Klicken Sie auf neue Verbindung. Geben Sie im Dialogfeld Verbindungs Eigenschaften den Servernamen ein (z. b. **(localdb) \\ mssqllocaldb**), wählen Sie die Authentifizierungsmethode aus, geben Sie **School**   als Datenbanknamen School ein, und klicken Sie dann auf **OK**.
    Das Dialogfeld Wählen Sie Ihre Datenverbindung aus wird mit Ihrer Daten bankverbindungs Einstellung aktualisiert.
-   Erweitern Sie im Dialogfeld Wählen Sie Ihre Datenbankobjekte aus den Knoten **Tabellen**,   und überprüfen Sie die Tabelle **Person** . Dadurch wird die angegebene Tabelle dem Modell " **School** " hinzugefügt.
-   Klicken Sie auf **Finish**.

Die Entity Designer, die eine Entwurfs Oberfläche zum Bearbeiten des Modells bereitstellt, wird angezeigt. Alle Objekte, die Sie im Dialogfeld **Wählen Sie Ihre Datenbankobjekte**ausgewählt   haben, werden dem Modell hinzugefügt.

## <a name="map-two-entities-to-a-single-table"></a>Ordnen Sie zwei Entitäten einer einzelnen Tabelle zu.

In diesem Abschnitt teilen Sie die Entität **Person** in zwei Entitäten auf und ordnen Sie dann einer einzelnen Tabelle zu.

> [!NOTE]
> Die **Person** -Entität enthält keine Eigenschaften, die möglicherweise eine große Datenmenge enthalten. Sie wird nur als Beispiel verwendet.

-   Klicken Sie mit der rechten Maustaste auf einen leeren Bereich der Entwurfs Oberfläche, zeigen Sie auf **Neu hinzufügen**, und klicken Sie auf **Entität**.
    Das Dialogfeld **neue Entität**wird   angezeigt.
-   Geben **Sie**   als **Entitäts Name** und **PersonID** als Name der **Schlüsseleigenschaft** hireinfo ein.
-   Klicken Sie auf **OK**.
-   Ein neuer Entitätstyp wird erstellt und auf der Entwurfsoberfläche angezeigt.
-   Wählen Sie die **HireDate**   -Eigenschaft des Entitäts Typs **Person**aus,   und drücken Sie **STRG + X** -Taste.
-   Wählen Sie die **hireinfo**   -Entität aus, und drücken Sie **STRG + V** Tasten.
-   Erstellen Sie eine Zuordnung zwischen **Person** und **hireinfo**. Klicken Sie hierzu mit der rechten Maustaste auf einen leeren Bereich der Entwurfs Oberfläche, zeigen Sie auf **Neu hinzufügen**, **und klicken Sie dann auf Zuordnung**.
-   Das Dialogfeld Zuordnung **Hinzufügen**wird   angezeigt. Der **personhireinfo** -Name wird standardmäßig angegeben.
-   Geben Sie die Multiplizität **1 (eins)** an beiden Enden der Beziehung an.
-   Klicken Sie auf **OK**.

Der nächste Schritt erfordert das Fenster " **Mappingdetails**"   . Wenn dieses Fenster nicht angezeigt wird, klicken Sie mit der rechten Maustaste auf die Entwurfs Oberfläche, und wählen Sie **Mappingdetails**.

-   Wählen Sie den **hireinfo**   -Entitätstyp aus, und klicken Sie im Fenster Mappingdetails auf ** &lt; Tabelle oder Sicht &gt; Hinzufügen**    **Mapping Details**   .
-   Wählen Sie in der Dropdown Liste ** &lt; Tabelle oder &gt; Sicht hinzufügen**die Option **Person** aus   . Die Liste enthält Tabellen oder Sichten, denen die ausgewählte Entität zugeordnet werden kann.
    Die entsprechenden Eigenschaften sollten standardmäßig zugeordnet werden.

    ![Mapping-Eigenschaften](~/ef6/media/mapping.png)

-   Wählen Sie auf der Entwurfs Oberfläche die Zuordnung **personhireinfo** aus.
-   Klicken Sie mit der rechten Maustaste auf die Zuordnung der Entwurfs Oberfläche, und wählen Sie **Eigenschaften**aus.
-   Wählen Sie im Fenster **Eigenschaften** die Eigenschaft **Referenzielle Einschränkungen** aus, und klicken Sie auf die Schaltfläche mit den Auslassungs Punkten.
-   Wählen Sie in der Dropdown Liste **Prinzipal** die Option **Person** aus.
-   Klicken Sie auf **OK**.

 

## <a name="use-the-model"></a>Verwenden des Modells

-   Fügen Sie den folgenden Code in die Main-Methode ein.

``` csharp
    using (var context = new SchoolEntities())
    {
        Person person = new Person()
        {
            FirstName = "Kimberly",
            LastName = "Morgan",
            Discriminator = "Instructor",
        };

        person.HireInfo = new HireInfo()
        {
            HireDate = DateTime.Now
        };

        // Add the new person to the context.
        context.People.Add(person);

        // Insert a row into the Person table.  
        context.SaveChanges();

        // Execute a query against the Person table.
        // The query returns columns that map to the Person entity.
        var existingPerson = context.People.FirstOrDefault();

        // Execute a query against the Person table.
        // The query returns columns that map to the Instructor entity.
        var hireInfo = existingPerson.HireInfo;

        Console.WriteLine("{0} was hired on {1}",
            existingPerson.LastName, hireInfo.HireDate);
    }
```
-   Kompilieren Sie die Anwendung, und führen Sie sie aus.

Die folgenden T-SQL-Anweisungen wurden als Ergebnis der Ausführung dieser Anwendung für die Datenbank " **School** " ausgeführt. 

-   Der folgende **Insert-Vorgang** wurde als Ergebnis der Ausführung des Kontexts ausgeführt. SaveChanges () und kombiniert Daten aus den Entitäten **Person** und **hireinfo** .

    ![Einfügen von Person-und hireinfo-Daten](~/ef6/media/insert.png)

-   Die folgende **Select** -Anweisung wurde als Ergebnis der Ausführung des Kontexts ausgeführt. People. FirstOrDefault () und wählt nur die Spalten aus, die **Person** zugeordnet sind.

    ![Wählen Sie 1 aus.](~/ef6/media/select1.png)

-   Die folgende **Select** -Anweisung wurde als Ergebnis des Zugriffs auf die Navigations Eigenschaft existingperson. Instructor ausgeführt und wählt nur die Spalten aus, die **hireinfo** zugeordnet sind.

    ![2 auswählen](~/ef6/media/select2.png)
