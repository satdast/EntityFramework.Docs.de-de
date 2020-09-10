---
title: Database First-EF6
description: Database First in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: cc6ffdb3-388d-4e79-a201-01ec2577c949
uid: ef6/modeling/designer/workflows/database-first
ms.openlocfilehash: 095fc6d6882df306816e65995ff314f1a0e7178a
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620445"
---
# <a name="database-first"></a>Database First
Dieses Video und die schrittweise exemplarische Vorgehensweise bieten eine Einführung in die Database First Entwicklung mit Entity Framework. Mit Database First können Sie ein Modell aus einer vorhandenen Datenbank umkehren. Das Modell wird in einer EDMX-Datei (edmx-Erweiterung) gespeichert und kann in der Entity Framework Designer angezeigt und bearbeitet werden. Die Klassen, mit denen Sie in Ihrer Anwendung interagieren, werden automatisch aus der EDMX-Datei generiert.

## <a name="watch-the-video"></a>Video ansehen
Dieses Video bietet eine Einführung in die Database First Entwicklung mit Entity Framework. Mit Database First können Sie ein Modell aus einer vorhandenen Datenbank umkehren. Das Modell wird in einer EDMX-Datei (edmx-Erweiterung) gespeichert und kann in der Entity Framework Designer angezeigt und bearbeitet werden. Die Klassen, mit denen Sie in Ihrer Anwendung interagieren, werden automatisch aus der EDMX-Datei generiert.

**Präsentation:**[Rowan Miller](https://romiller.com/)

**Video**: [WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv)  |  [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)

## <a name="pre-requisites"></a>Voraussetzungen

Sie müssen mindestens Visual Studio 2010 oder Visual Studio 2012 installiert haben, um diese exemplarische Vorgehensweise abzuschließen.

Wenn Sie Visual Studio 2010 verwenden, müssen Sie auch [nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installieren.

 

## <a name="1-create-an-existing-database"></a>1. Erstellen Sie eine vorhandene Datenbank.

Wenn Sie eine vorhandene Datenbank als Ziel haben, wird Sie in der Regel bereits erstellt, aber in dieser exemplarischen Vorgehensweise müssen wir eine Datenbank erstellen, auf die zugegriffen werden kann.

Der Datenbankserver, der mit Visual Studio installiert wird, unterscheidet sich abhängig von der installierten Version von Visual Studio:

-   Wenn Sie Visual Studio 2010 verwenden, erstellen Sie eine SQL Express-Datenbank.
-   Wenn Sie Visual Studio 2012 verwenden, erstellen Sie eine [localdb](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) -Datenbank.

 

Nun generieren wir die Datenbank.

-   Öffnen Sie Visual Studio.
-   **Ansicht- &gt; Server-Explorer**
-   Klicken Sie mit der rechten Maustaste auf **Datenverbindungen- &gt; Verbindung hinzufügen...**
-   Wenn Sie über Server-Explorer keine Verbindung mit einer Datenbank hergestellt haben, müssen Sie Microsoft SQL Server als Datenquelle auswählen.

    ![Datenquelle auswählen](~/ef6/media/selectdatasource.png)

-   Stellen Sie eine Verbindung mit localdb oder SQL Express her, je nachdem, welche installiert wurde, und geben Sie **databasefirst. blogals** Datenbanknamen ein.

    ![SQL Express-Verbindung (DF)](~/ef6/media/sqlexpressconnectiondf.png)

    ![Localdb-Verbindung (DF)](~/ef6/media/localdbconnectiondf.png)

-   Wählen Sie **OK** aus, und Sie werden gefragt, ob Sie eine neue Datenbank erstellen möchten, und wählen Sie **Ja** aus.

    ![Dialog Feld Create Database](~/ef6/media/createdatabasedialog.png)

-   Die neue Datenbank wird nun in Server-Explorer angezeigt, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **neue Abfrage** aus.
-   Kopieren Sie den folgenden SQL-Befehl in die neue Abfrage, klicken Sie mit der rechten Maustaste auf die Abfrage, und wählen Sie **Ausführen** .

``` SQL
CREATE TABLE [dbo].[Blogs] (
    [BlogId] INT IDENTITY (1, 1) NOT NULL,
    [Name] NVARCHAR (200) NULL,
    [Url]  NVARCHAR (200) NULL,
    CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
);

CREATE TABLE [dbo].[Posts] (
    [PostId] INT IDENTITY (1, 1) NOT NULL,
    [Title] NVARCHAR (200) NULL,
    [Content] NTEXT NULL,
    [BlogId] INT NOT NULL,
    CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
    CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
);
```

## <a name="2-create-the-application"></a>2. Erstellen der Anwendung

Um dies zu gewährleisten, erstellen wir eine einfache Konsolenanwendung, die die Database First für den Datenzugriff verwendet:

-   Öffnen Sie Visual Studio.
-   **Datei- &gt; New- &gt; Project...**
-   Wählen Sie im Menü auf der linken Seite und **Konsolenanwendung** **Windows** aus.
-   Geben Sie **databasefirstsample** als Name ein.
-   Klicken Sie auf **OK**.

 

## <a name="3-reverse-engineer-model"></a>3. Reverse-Engineering-Modell

Wir verwenden Entity Framework Designer, die als Teil von Visual Studio enthalten ist, um das Modell zu erstellen.

-   **Project- &gt; Neues Element hinzufügen...**
-   Wählen Sie im linken Menü **Daten** aus, und klicken Sie dann auf **ADO.NET Entity Data Model**
-   Geben Sie als Name **bloggingmodel** ein, und klicken Sie auf **OK** .
-   Dadurch wird der **Entity Data Model-Assistent** gestartet.
-   Wählen Sie **aus Datenbank generieren aus** , und klicken Sie auf **weiter**

    ![Schritt 1 des Assistenten](~/ef6/media/wizardstep1.png)

-   Wählen Sie die Verbindung mit der Datenbank aus, die Sie im ersten Abschnitt erstellt haben, geben Sie **bloggingcontext** als Namen der Verbindungs Zeichenfolge ein, und klicken Sie auf **weiter** .

    ![Assistent Schritt 2](~/ef6/media/wizardstep2.png)

-   Aktivieren Sie das Kontrollkästchen neben "Tables", um alle Tabellen zu importieren, und klicken Sie auf "Fertigstellen"

    ![Assistent Schritt 3](~/ef6/media/wizardstep3.png)

 

Sobald der Reverse-Engineering-Prozess abgeschlossen ist, wird das neue Modell dem Projekt hinzugefügt und geöffnet, damit Sie es im Entity Framework Designer anzeigen können. Außerdem wurde dem Projekt eine App.config Datei mit den Verbindungsdetails für die Datenbank hinzugefügt.

![Modell anfänglich](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a>Weitere Schritte in Visual Studio 2010

Wenn Sie in Visual Studio 2010 arbeiten, müssen Sie einige zusätzliche Schritte ausführen, um ein Upgrade auf die neueste Version von Entity Framework durchzuführen. Das Upgrade ist wichtig, da Sie Ihnen Zugriff auf eine verbesserte API-Oberfläche, die viel einfacher zu verwenden ist, sowie auf die neuesten Fehlerbehebungen bietet.

Zuerst müssen wir die neueste Version von Entity Framework von nuget erhalten.

-   **Project – &gt; Nuget-Pakete verwalten...** 
     *Wenn Sie nicht über die Option " **nuget-Pakete verwalten...** " verfügen, sollten Sie die [neueste Version von nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installieren.*
-   Auswählen der Registerkarte " **Online** "
-   Auswählen des " **EntityFramework** "-Pakets
-   Klicken Sie auf **Install** (Installieren).

Als nächstes müssen wir das Modell austauschen, um Code zu generieren, der die dbcontext-API nutzt, die in späteren Versionen von Entity Framework eingeführt wurde.

-   Klicken Sie mit der rechten Maustaste auf eine leere Stelle des Modells im EF-Designer, und wählen Sie **Code Generierungs Element hinzufügen... aus.**
-   Wählen Sie im linken Menü **Online Vorlagen** aus, und suchen Sie nach **dbcontext** .
-   Wählen Sie den EF **5. x dbcontext Generator für \# C aus**, geben Sie **bloggingmodel** als Name ein, und klicken Sie auf **Hinzufügen** .

    ![Dbcontext-Vorlage](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a>4. lesen & schreiben von Daten

Nachdem wir nun über ein Modell verfügen, ist es an der Zeit, es für den Zugriff auf einige Daten zu verwenden. Die Klassen, die für den Zugriff auf Daten verwendet werden, werden automatisch für Sie basierend auf der EDMX-Datei generiert.

*Dieser Screenshot ist von Visual Studio 2012. Wenn Sie Visual Studio 2010 verwenden, befinden sich die Dateien BloggingModel.TT und BloggingModel.Context.tt direkt unter Ihrem Projekt, anstatt Sie in der EDMX-Datei zu untersuchen.*

![Generierte Klassen (DF)](~/ef6/media/generatedclassesdf.png)

 

Implementieren Sie die Main-Methode in Program.cs, wie unten gezeigt. Mit diesem Code wird eine neue Instanz des Kontexts erstellt und anschließend verwendet, um einen neuen Blog einzufügen. Anschließend wird eine LINQ-Abfrage verwendet, um alle Blogs aus der Datenbank alphabetisch nach Titel abzurufen.

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

Sie können die Anwendung jetzt ausführen und testen.

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
 

## <a name="5-dealing-with-database-changes"></a>5. Umgang mit Daten Bank Änderungen

Nun ist es an der Zeit, Änderungen am Datenbankschema vorzunehmen, wenn wir diese Änderungen vornehmen, müssen wir auch unser Modell aktualisieren, um diese Änderungen widerzuspiegeln.

Der erste Schritt besteht darin, einige Änderungen am Datenbankschema vorzunehmen. Wir fügen dem Schema eine Tabelle "Users" (Benutzer) hinzu.

-   Klicken Sie in Server-Explorer mit der rechten Maustaste auf die Datenbank **databasefirst. Blog,** und wählen Sie **neue Abfrage** aus.
-   Kopieren Sie den folgenden SQL-Befehl in die neue Abfrage, klicken Sie mit der rechten Maustaste auf die Abfrage, und wählen Sie **Ausführen** .

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

Nachdem das Schema aktualisiert wurde, ist es an der Zeit, das Modell mit diesen Änderungen zu aktualisieren.

-   Klicken Sie mit der rechten Maustaste auf eine leere Stelle des Modells im EF-Designer, und wählen Sie "Modell aus Datenbank aktualisieren..." aus. Dadurch wird der Update-Assistent gestartet.
-   Aktivieren Sie auf der Registerkarte hinzufügen des Update-Assistenten das Kontrollkästchen neben Tabellen, was bedeutet, dass wir neue Tabellen aus dem Schema hinzufügen möchten.
    *Auf der Registerkarte Aktualisieren werden alle vorhandenen Tabellen im Modell angezeigt, die während des Updates auf Änderungen geprüft werden. Auf den Registerkarten löschen werden alle Tabellen angezeigt, die aus dem Schema entfernt wurden. Sie werden auch als Teil des Updates aus dem Modell entfernt. Die Informationen auf diesen beiden Registerkarten werden automatisch erkannt und dienen nur zu Informationszwecken. Sie können keine Einstellungen ändern.*

    ![Aktualisierungs-Assistent](~/ef6/media/refreshwizard.png)

-   Klicken Sie im Update-Assistenten auf Fertigstellen.

 

Das Modell wird nun so aktualisiert, dass es eine neue Benutzer Entität enthält, die der Benutzertabelle zugeordnet ist, die der Datenbank hinzugefügt wurde.

![Modell aktualisiert](~/ef6/media/modelupdated.png)

## <a name="summary"></a>Zusammenfassung

In dieser exemplarischen Vorgehensweise haben wir uns mit Database First Entwicklung beschäftigt, mit der wir ein Modell im EF-Designer basierend auf einer vorhandenen Datenbank erstellen konnten. Anschließend haben wir dieses Modell verwendet, um Daten aus der Datenbank zu lesen und zu schreiben. Schließlich haben wir das Modell aktualisiert, um Änderungen widerzuspiegeln, die wir am Datenbankschema vorgenommen haben.
