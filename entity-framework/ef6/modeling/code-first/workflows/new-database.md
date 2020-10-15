---
title: Code First einer neuen Datenbank EF6
description: Code First einer neuen Datenbank in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/workflows/new-database
ms.openlocfilehash: 9d0082ac5226fff066d3e18c9164e2230c84b285
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065018"
---
# <a name="code-first-to-a-new-database"></a>Code First to a New Database (Code First für eine neue Datenbank)
Dieses Video und die schrittweise exemplarische Vorgehensweise bieten eine Einführung in Code First Entwicklung für eine neue Datenbank. Dieses Szenario umfasst das Ziel einer Datenbank, die nicht vorhanden ist und Code First erstellt wird, oder eine leere Datenbank, der Code First neue Tabellen hinzugefügt werden. Mit Code First können Sie das Modell mithilfe von C- \# oder VB.NET-Klassen definieren. Die zusätzliche Konfiguration kann optional mithilfe von Attributen in ihren Klassen und Eigenschaften oder mithilfe einer fließenden API ausgeführt werden.

## <a name="watch-the-video"></a>Video ansehen
Dieses Video bietet eine Einführung in Code First Entwicklung für eine neue Datenbank. Dieses Szenario umfasst das Ziel einer Datenbank, die nicht vorhanden ist und Code First erstellt wird, oder eine leere Datenbank, der Code First neue Tabellen hinzugefügt werden. Code First ermöglicht es Ihnen, das Modell mit c#-oder VB.NET-Klassen zu definieren. Die zusätzliche Konfiguration kann optional mithilfe von Attributen in ihren Klassen und Eigenschaften oder mithilfe einer fließenden API ausgeführt werden.

**Präsentation:**[Rowan Miller](https://romiller.com/)

**Video**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv)  |  [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)

## <a name="pre-requisites"></a>Voraussetzungen

Sie müssen mindestens Visual Studio 2010 oder Visual Studio 2012 installiert haben, um diese exemplarische Vorgehensweise abzuschließen.

Wenn Sie Visual Studio 2010 verwenden, müssen Sie auch [nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installieren.

## <a name="1-create-the-application"></a>1. Erstellen Sie die Anwendung.

Um die Dinge einfach zu halten, erstellen wir eine einfache Konsolenanwendung, die Code First verwendet, um Daten Zugriffe auszuführen.

-   Öffnen Sie Visual Studio.
-   **Datei- &gt; New- &gt; Project...**
-   Wählen Sie im Menü auf der linken Seite und **Konsolenanwendung** **Windows** aus.
-   Geben Sie **codefirstnewdatabasesample** als Name ein.
-   Klicken Sie auf **OK**.

## <a name="2-create-the-model"></a>2. Erstellen des Modells

Definieren wir ein sehr einfaches Modell mithilfe von Klassen. Wir definieren Sie lediglich in der Program.cs-Datei, aber in einer realen Anwendung würden Sie Ihre Klassen in separate Dateien und potenziell ein separates Projekt aufteilen.

Fügen Sie unter der Programm Klassendefinition in Program.cs die beiden folgenden Klassen hinzu.

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; }
    public virtual Blog Blog { get; set; }
}
```

Sie werden feststellen, dass die beiden Navigations Eigenschaften (Blog. Posts und Post. Blog) virtuell sind. Dies ermöglicht die Lazy Load-Funktion von Entity Framework. Lazy Load bedeutet, dass der Inhalt dieser Eigenschaften automatisch aus der Datenbank geladen wird, wenn Sie versuchen, darauf zuzugreifen.

## <a name="3-create-a-context"></a>3. Erstellen eines Kontexts

Nun ist es an der Zeit, einen abgeleiteten Kontext zu definieren, der eine Sitzung mit der Datenbank darstellt, sodass wir Daten Abfragen und speichern können. Wir definieren einen Kontext, der von System. Data. Entity. dbcontext abgeleitet ist und &lt; &gt; für jede Klasse in unserem Modell eine typisierte dbset TEntity verfügbar macht.

Wir beginnen jetzt mit der Verwendung von Typen aus der Entity Framework daher müssen wir das nuget-Paket "EntityFramework" hinzufügen.

-   **Project – &gt; nuget-Pakete verwalten...**
    Hinweis: Wenn Sie nicht über die **nuget-Pakete verwalten verfügen...** Option Sie sollten die [neueste Version von nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installieren.
-   Auswählen der Registerkarte " **Online** "
-   Auswählen des " **EntityFramework** "-Pakets
-   Klicken Sie auf **Install** (Installieren).

Fügen Sie am Anfang von Program.cs eine using-Anweisung für System. Data. Entity hinzu.

``` csharp
using System.Data.Entity;
```

Fügen Sie unter der Post-Klasse in Program.cs den folgenden abgeleiteten Kontext hinzu.

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

Im folgenden finden Sie eine komplette Liste der Komponenten, die Program.cs jetzt enthalten sollte.

``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Entity;

namespace CodeFirstNewDatabaseSample
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }

    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }
}
```

Dies ist der gesamte Code, der zum Speichern und Abrufen von Daten erforderlich ist. Natürlich gibt es im Hintergrund etwas, das wir in einem Moment sehen werden, aber zuerst sehen wir uns dies in Aktion an.

## <a name="4-reading--writing-data"></a>4. lesen & schreiben von Daten

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
### <a name="wheres-my-data"></a>Wo sind meine Daten?

Gemäß der Konvention hat dbcontext eine Datenbank für Sie erstellt.

-   Wenn eine lokale SQL Express-Instanz verfügbar ist (standardmäßig mit Visual Studio 2010 installiert), hat Code First die Datenbank auf dieser Instanz erstellt.
-   Wenn SQL Express nicht verfügbar ist, werden Code First versuchen, [localdb](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (standardmäßig mit Visual Studio 2012 installiert) zu verwenden.
-   Die Datenbank wird nach dem voll qualifizierten Namen des abgeleiteten Kontexts benannt, in unserem Fall **codefirstnewdatabasesample. bloggingcontext.**

Dabei handelt es sich nur um die Standard Konventionen, und es gibt verschiedene Möglichkeiten, die Datenbank zu ändern, die Code First verwendet. Weitere Informationen finden Sie im Thema **wie dbcontext das Modell und die Datenbankverbindung** erkennt.
Sie können mit Server-Explorer in Visual Studio eine Verbindung mit dieser Datenbank herstellen.

-   **Ansicht- &gt; Server-Explorer**
-   Klicken Sie mit der rechten Maustaste auf **Datenverbindungen** , und wählen Sie **Verbindung hinzufügen aus.**
-   Wenn Sie über Server-Explorer keine Verbindung mit einer Datenbank hergestellt haben, müssen Sie Microsoft SQL Server als Datenquelle auswählen.

    ![Datenquelle auswählen](~/ef6/media/selectdatasource.png)

-   Stellen Sie eine Verbindung mit localdb oder SQL Express her, je nachdem, welche installiert wurde.

Wir können nun das Schema überprüfen, das von Code First erstellt wurde.

![Schema anfänglich](~/ef6/media/schemainitial.png)

Dbcontext hat festgelegt, welche Klassen in das Modell aufgenommen werden sollen, indem wir die von uns definierten dbset-Eigenschaften betrachten. Anschließend wird der Standardsatz von Code First Konventionen verwendet, um Tabellen-und Spaltennamen zu bestimmen, Datentypen zu ermitteln, Primärschlüssel zu suchen usw. Später in dieser exemplarischen Vorgehensweise wird erläutert, wie Sie diese Konventionen überschreiben können.

## <a name="5-dealing-with-model-changes"></a>5. Umgang mit Modelländerungen

Nun ist es an der Zeit, einige Änderungen an unserem Modell vorzunehmen, wenn wir diese Änderungen vornehmen, müssen wir auch das Datenbankschema aktualisieren. Zu diesem Zweck wird eine Funktion namens "Code First-Migrationen" oder "Migrationen" verwendet.

Migrationen ermöglichen es uns, einen geordneten Satz von Schritten zu haben, die beschreiben, wie das Datenbankschema aktualisiert (und herabgestuft) werden kann. Jeder dieser Schritte, die als Migration bezeichnet wird, enthält Code, der die anzuwendenden Änderungen beschreibt. 

Der erste Schritt besteht darin, Code First-Migrationen für den bloggingcontext zu aktivieren.

-   **Tools- &gt; Bibliothekspaket-Manager- &gt; Paket-Manager-Konsole**
-   Führen Sie in der Paket-Manager-Konsole den Befehl **Enable-Migrations** aus.
-   Dem Projekt wurde ein neuer Migrations Ordner hinzugefügt, der zwei Elemente enthält:
    -   **Configuration.cs** – diese Datei enthält die Einstellungen, die Migrationen zum Migrieren von bloggingcontext verwenden. Wir müssen nichts für diese exemplarische Vorgehensweise ändern, aber hier können Sie Seed-Daten angeben, Anbieter für andere Datenbanken registrieren, den Namespace ändern, in dem Migrationen generiert werden usw.
    -   ** &lt; Zeitstempel &gt; \_ InitialCreate.cs** – Dies ist die erste Migration. Sie stellt die Änderungen dar, die bereits auf die Datenbank angewendet wurden, damit Sie aus einer leeren Datenbank zu einer Datenbank mit den Tabellen Blogs und Posts geführt werden. Obwohl wir Code First diese Tabellen für uns automatisch erstellen lassen, haben wir nun die Migration in eine Migration durchführen. Code First wurde auch in unserer lokalen Datenbank aufgezeichnet, dass diese Migration bereits angewendet wurde. Der Zeitstempel des Datei namens wird zu bestellzwecken verwendet.

    Nehmen wir nun eine Änderung an unserem Modell vor. Fügen Sie der Blog-Klasse eine URL-Eigenschaft hinzu:

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   Führen Sie den Befehl **Add-Migration addurl** in der Paket-Manager-Konsole aus.
    Der Add-Migration-Befehl überprüft seit der letzten Migration nach Änderungen und richtet eine neue Migration mit gefundenen Änderungen ein. Wir können Migrationen einen Namen übergeben. in diesem Fall rufen wir die Migration "addUrl" auf.
    Der Gerüst Code besagt, dass wir eine URL-Spalte, die Zeichen folgen Daten enthalten kann, zum dbo hinzufügen müssen. Blogs-Tabelle. Bei Bedarf könnten wir den gerüdierten Code bearbeiten, dies ist in diesem Fall jedoch nicht erforderlich.

``` csharp
namespace CodeFirstNewDatabaseSample.Migrations
{
    using System;
    using System.Data.Entity.Migrations;

    public partial class AddUrl : DbMigration
    {
        public override void Up()
        {
            AddColumn("dbo.Blogs", "Url", c => c.String());
        }

        public override void Down()
        {
            DropColumn("dbo.Blogs", "Url");
        }
    }
}
```

-   Führen Sie den Befehl **Update-Database** in der Paket-Manager-Konsole aus. Mit diesem Befehl werden alle ausstehenden Migrationen auf die Datenbank angewendet. Unsere InitialCreate-Migration wurde bereits angewendet, sodass bei der Migration nur die neue addurl-Migration angewendet wird.
    Tipp: Sie können den Schalter **– verbose** verwenden, wenn Sie Update-Database aufrufen, um die SQL-Anweisung anzuzeigen, die für die Datenbank ausgeführt wird.

Die neue URL-Spalte wird nun der Tabelle Blogs in der-Datenbank hinzugefügt:

![Schema mit URL](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a>6. Daten Anmerkungen

Bisher haben wir EF das Modell nur mit seinen Standard Konventionen ermitteln lassen, aber es gibt Zeiten, in denen unsere Klassen nicht den Konventionen folgen und eine weitere Konfiguration durchführen müssen. Hierfür gibt es zwei Optionen: Wir betrachten die Daten Anmerkungen in diesem Abschnitt und dann die fließende API im nächsten Abschnitt.

-   Fügen wir unserem Modell eine Benutzerklasse hinzu.

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   Wir müssen dem abgeleiteten Kontext auch eine Menge hinzufügen.

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   Wenn wir versuchen, eine Migration hinzuzufügen, wird eine Fehlermeldung angezeigt, die besagt, dass für*EntityType ' User ' kein Schlüssel definiert ist. Hiermit wird der Schlüssel für diesen EntityType definiert. "* EF kann nicht wissen, dass der Benutzername der Primärschlüssel für den Benutzer sein sollte.
-   Wir verwenden jetzt Daten Anmerkungen, damit wir am Anfang von Program.cs eine using-Anweisung hinzufügen müssen.

```csharp
using System.ComponentModel.DataAnnotations;
```

-   Kommentieren Sie nun die UserName-Eigenschaft, um festzustellen, dass es sich um den Primärschlüssel handelt.

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   Verwenden Sie den Befehl **Add-Migration adduser** zum Gerüstbau einer Migration, um diese Änderungen auf die Datenbank anzuwenden.
-   Führen Sie den **Update-Database-** Befehl aus, um die neue Migration auf die Datenbank anzuwenden.

Die neue Tabelle wird nun der-Datenbank hinzugefügt:

![Schema mit Benutzern](~/ef6/media/schemawithusers.png)

Die vollständige Liste der von EF unterstützten Anmerkungen lautet wie folgt:

-   [Keyattribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [StringLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [MaxLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   ["-Attribut"](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [RequiredAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [Timestampattribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [Complextypeattribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [ColumnAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [TableAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [Invertspropertyattribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [Fremdschlüssel Attribut](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [Databasegeneratedattribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [Notmappeer dattribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a>7. fließende API

Im vorherigen Abschnitt haben wir uns mit der Verwendung von Daten Anmerkungen beschäftigt, um zu ergänzen oder zu überschreiben, was von der Konvention erkannt wurde. Die andere Möglichkeit, das Modell zu konfigurieren, ist über die Code First fließende API.

Die meisten Modell Konfigurationen können mithilfe von einfachen Daten Anmerkungen durchgeführt werden. Die fließende API ist eine fortgeschrittenere Methode zum Angeben der Modell Konfiguration, die alles abdeckt, was Daten Anmerkungen zusätzlich zu einer erweiterten Konfiguration tun können, die mit Daten Anmerkungen nicht möglich ist. Daten Anmerkungen und die fließende API können gleichzeitig verwendet werden.

Für den Zugriff auf die fließende API überschreiben Sie die onmodelcreating-Methode in dbcontext. Nehmen wir an, dass wir die Spalte, in der "User. Display Name" gespeichert ist, in "Display Name" umbenennen möchten \_ .

-   Überschreiben Sie die onmodelcreating-Methode auf bloggingcontext mit dem folgenden Code:

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>()
            .Property(u => u.DisplayName)
            .HasColumnName("display_name");
    }
}
```

-   Verwenden Sie den Befehl **Add-Migration changedisplayname** , um ein Gerüst für eine Migration zum Anwenden dieser Änderungen auf die Datenbank zu verwenden.
-   Führen Sie den **Update-Database-** Befehl aus, um die neue Migration auf die Datenbank anzuwenden.

Die Spalte Display Name wurde nun in Anzeige Name umbenannt \_ :

![Schema mit umbenannten anzeigen Amen](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a>Zusammenfassung

In dieser exemplarischen Vorgehensweise haben wir Code First Entwicklung mit einer neuen Datenbank untersucht. Wir haben ein Modell mithilfe von Klassen definiert und dann das Modell verwendet, um eine Datenbank zu erstellen und Daten zu speichern und abzurufen. Nachdem die Datenbank erstellt wurde, haben wir Code First-Migrationen verwendet, um das Schema zu ändern, als das Modell entwickelt wurde. Außerdem wurde erläutert, wie ein Modell mithilfe von Daten Anmerkungen und der fließenden API konfiguriert wird.
