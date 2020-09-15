---
title: Aufteilung von Designer Entitäten-EF6
description: Aufteilung von Designer Entitäten in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/entity-splitting
ms.openlocfilehash: fa25629bae1a35b2d792e2b890e87db6c99c37ab
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073135"
---
# <a name="designer-entity-splitting"></a>Aufteilung von Designer Entitäten
In dieser exemplarischen Vorgehensweise wird gezeigt, wie ein Entitätstyp zwei Tabellen zugeordnet wird, indem ein Modell mit dem Entity Framework Designer (EF-Designer) geändert wird. Sie können eine Entität mehreren Tabellen zuordnen, sofern für die Tabellen ein gemeinsamer Schlüssel vorhanden ist. Die Prinzipien, nach denen ein Entitätstyp zwei Tabellen zugeordnet wird, lassen sich leicht auf das Mapping zu mehr als zwei Tabellen erweitern.

Die folgende Abbildung zeigt die Hauptfenster, die bei der Arbeit mit dem EF-Designer verwendet werden.

![EF-Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>Voraussetzungen

Visual Studio 2012 oder Visual Studio 2010, Ultimate, Premium, Professional oder Web Express Edition.

## <a name="create-the-database"></a>Erstellen der Datenbank

Der Datenbankserver, der mit Visual Studio installiert wird, unterscheidet sich abhängig von der installierten Version von Visual Studio:

-   Wenn Sie Visual Studio 2012 verwenden, erstellen Sie eine localdb-Datenbank.
-   Wenn Sie Visual Studio 2010 verwenden, erstellen Sie eine SQL Express-Datenbank.

Zuerst erstellen wir eine Datenbank mit zwei Tabellen, die in einer einzelnen Entität kombiniert werden.

-   Öffnen Sie Visual Studio.
-   **Ansicht- &gt; Server-Explorer**
-   Klicken Sie mit der rechten Maustaste auf **Datenverbindungen- &gt; Verbindung hinzufügen...**
-   Wenn Sie über Server-Explorer keine Verbindung mit einer Datenbank hergestellt haben, müssen Sie **Microsoft SQL Server** als Datenquelle auswählen.
-   Stellen Sie eine Verbindung mit localdb oder SQL Express her, je nachdem, welche installiert wurde.
-   Geben Sie **entitysplit** als Datenbanknamen ein.
-   Wählen Sie **OK** aus, und Sie werden gefragt, ob Sie eine neue Datenbank erstellen möchten, und wählen Sie **Ja** aus.
-   Die neue Datenbank wird nun in Server-Explorer
-   Wenn Sie Visual Studio 2012 verwenden
    -   Klicken Sie im Server-Explorer mit der rechten Maustaste auf die Datenbank, und wählen Sie **Neue Abfrage** aus.
    -   Kopieren Sie den folgenden SQL-Befehl in die neue Abfrage, klicken Sie mit der rechten Maustaste auf die Abfrage, und wählen Sie **Ausführen** .
-   Wenn Sie Visual Studio 2010 verwenden
    -   Wählen Sie **Data- &gt; Transact SQL-Editor- &gt; neue Abfrage Verbindung...**
    -   Geben Sie ein **. \\ SQLExpress** als Servername, und klicken Sie auf **OK** .
    -   Wählen Sie in der Dropdown-Datei am oberen Rand des Abfrage-Editors die **entitysplit** -Datenbank aus.
    -   Kopieren Sie das folgende SQL in die neue Abfrage, klicken Sie mit der rechten Maustaste auf die Abfrage, und wählen Sie **SQL ausführen** aus.

``` SQL
CREATE TABLE [dbo].[Person] (
[PersonId] INT IDENTITY (1, 1) NOT NULL,
[FirstName] NVARCHAR (200) NULL,
[LastName] NVARCHAR (200) NULL,
CONSTRAINT [PK_Person] PRIMARY KEY CLUSTERED ([PersonId] ASC)
);

CREATE TABLE [dbo].[PersonInfo] (
[PersonId] INT NOT NULL,
[Email] NVARCHAR (200) NULL,
[Phone] NVARCHAR (50) NULL,
CONSTRAINT [PK_PersonInfo] PRIMARY KEY CLUSTERED ([PersonId] ASC),
CONSTRAINT [FK_Person_PersonInfo] FOREIGN KEY ([PersonId]) REFERENCES [dbo].[Person] ([PersonId]) ON DELETE CASCADE
);
```

## <a name="create-the-project"></a>Erstellen des Projekts

-   Zeigen Sie im Menü **Datei** auf **Neu**, und klicken Sie dann auf **Projekt**.
-   Klicken Sie im linken Bereich auf **Visual C \# **, und wählen Sie dann die Vorlage **Konsolenanwendung** aus.
-   Geben Sie als Name für das Projekt **matztityytablessample** ein, und klicken Sie auf **OK**.
-   Klicken Sie auf **Nein** , wenn Sie dazu aufgefordert werden, die im ersten Abschnitt erstellte SQL-Abfrage zu speichern.

## <a name="create-a-model-based-on-the-database"></a>Erstellen eines Modells auf der Grundlage der Datenbank

-   Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen, zeigen Sie auf **Hinzufügen**, und klicken Sie dann auf **Neues Element**.
-   Wählen Sie im linken Menü **Daten** aus, und wählen Sie dann im Bereich Vorlagen die Option **ADO.NET Entity Data Model** aus.
-   Geben Sie als Dateiname **matztitydetablesmodel. edmx** ein, und klicken Sie dann auf **Hinzufügen**.
-   Wählen Sie im Dialogfeld Modell Inhalte auswählen die Option **aus Datenbank generieren aus**, und klicken Sie dann auf **Weiter.**
-   Wählen **Sie in**der Dropdown-Dropdown-Option die **entitysplitverbindung** aus
-   Aktivieren Sie im Dialogfeld Wählen Sie Ihre Datenbankobjekte aus das Kontrollkästchen neben dem Knoten **Tabellen**   .
    Dadurch werden alle Tabellen aus der **entitysplit** -Datenbank dem Modell hinzugefügt.
-   Klicken Sie auf **Finish**.

Die Entity Designer, die eine Entwurfs Oberfläche zum Bearbeiten des Modells bereitstellt, wird angezeigt.

## <a name="map-an-entity-to-two-tables"></a>Zuordnen einer Entität zu zwei Tabellen

In diesem Schritt aktualisieren wir den Entitätstyp **Person** , um Daten aus den Tabellen " **Person** " und " **personinfo** " zu kombinieren.

-   Wählen Sie die **e-Mail-**   und **Telefon** Eigenschaften der **personinfo **-Entität aus, und drücken Sie **STRG + X** -Taste.
-   Wählen Sie die Entität **Person **aus, und drücken Sie **STRG + V** Tasten.
-   Wählen Sie auf der Entwurfs Oberfläche die **personinfo**   -Entität aus, und drücken Sie auf der Tastatur die Schaltfläche **Löschen** .
-   Klicken Sie auf **Nein** , wenn Sie gefragt werden, ob Sie die **personinfo** -Tabelle aus dem Modell entfernen möchten. Wir sind im Begriff, Sie der **Person** -Entität zuzuordnen.

    ![Löschen von Tabellen](~/ef6/media/deletetables.png)

Die nächsten Schritte erfordern das Fenster " **Mappingdetails**"   . Wenn dieses Fenster nicht angezeigt wird, klicken Sie mit der rechten Maustaste auf die Entwurfs Oberfläche, und wählen Sie **Mappingdetails**.

-   Wählen Sie **Person**den   Entitätstyp Person aus, und klicken Sie im Fenster Mappingdetails auf ** &lt; Tabelle oder Sicht &gt; Hinzufügen**    **Mapping Details**   .
-   Wählen **PersonInfo **Sie in   der Dropdown Liste personinfo aus.
    Das Fenster **Mappingdetails**   wird mit Standard Spalten Zuordnungen aktualisiert. diese sind für das Szenario in Ordnung.

Der **Person**   Entitätstyp Person ist nun der **Person**   -Tabelle und der **personinfo**-   Tabelle zugeordnet.

![Zuordnung 2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a>Verwenden des Modells

-   Fügen Sie den folgenden Code in die Main-Methode ein.

``` csharp
    using (var context = new EntitySplittingEntities())
    {
        var person = new Person
        {
            FirstName = "John",
            LastName = "Doe",
            Email = "john@example.com",
            Phone = "555-555-5555"
        };

        context.People.Add(person);
        context.SaveChanges();

        foreach (var item in context.People)
        {
            Console.WriteLine(item.FirstName);
        }
    }
```

-   Kompilieren Sie die Anwendung, und führen Sie sie aus.

Die folgenden T-SQL-Anweisungen wurden für die Datenbank ausgeführt, weil diese Anwendung ausgeführt wurde. 

-   Die folgenden zwei **Insert** -Anweisungen wurden als Ergebnis der Ausführung des Kontexts ausgeführt. SaveChanges (). Die Daten werden von der **Person** -Entität übernommen und zwischen den Tabellen " **Person** " und " **personinfo** " aufgeteilt.

    ![1 einfügen](~/ef6/media/insert1.png)

    ![2 einfügen](~/ef6/media/insert2.png)
-   Die folgende **Select** -Anweisung wurde als Ergebnis der Enumeration der Personen in der Datenbank ausgeführt. Dabei werden die Daten aus der Tabelle " **Person** " und " **personinfo** " kombiniert.

    ![Kombinieren von Person-und personinfo-Daten auswählen](~/ef6/media/select.png)
