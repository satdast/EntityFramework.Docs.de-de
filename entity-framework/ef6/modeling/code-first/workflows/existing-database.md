---
title: Code First zu einer vorhandenen Datenbank EF6
description: Code First in eine vorhandene Datenbank in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/workflows/existing-database
ms.openlocfilehash: d26c5f2ced6b327615cc78bcb2bb6d188b012c14
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073989"
---
# <a name="code-first-to-an-existing-database"></a>Code First an eine vorhandene Datenbank
Dieses Video und die schrittweise exemplarische Vorgehensweise bieten eine Einführung in Code First Entwicklung für eine vorhandene Datenbank. Mit Code First können Sie das Modell mithilfe von C- \# oder VB.NET-Klassen definieren. Optional kann eine zusätzliche Konfiguration mithilfe von Attributen in ihren Klassen und Eigenschaften oder mithilfe einer fließenden API ausgeführt werden.

## <a name="watch-the-video"></a>Video ansehen
Dieses Video ist [nun auf Channel 9 verfügbar](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-).

## <a name="pre-requisites"></a>Voraussetzungen

Sie müssen **Visual Studio 2012** oder **Visual Studio 2013** installiert haben, um diese exemplarische Vorgehensweise abzuschließen.

Sie benötigen auch Version **6,1** (oder höher) der **Entity Framework Tools für Visual Studio** . Weitere Informationen zum Installieren der neuesten Version des Entity Framework Tools finden [Sie unter Get Entity Framework](xref:ef6/fundamentals/install) .

## <a name="1-create-an-existing-database"></a>1. Erstellen Sie eine vorhandene Datenbank.

Wenn Sie eine vorhandene Datenbank als Ziel haben, wird Sie in der Regel bereits erstellt, aber in dieser exemplarischen Vorgehensweise müssen wir eine Datenbank erstellen, auf die zugegriffen werden kann.

Nun generieren wir die Datenbank.

-   Öffnen Sie Visual Studio.
-   **Ansicht- &gt; Server-Explorer**
-   Klicken Sie mit der rechten Maustaste auf **Datenverbindungen- &gt; Verbindung hinzufügen...**
-   Wenn Sie über **Server-Explorer** keine Verbindung mit einer Datenbank hergestellt haben, müssen Sie **Microsoft SQL Server** als Datenquelle auswählen.

    ![Datenquelle auswählen](~/ef6/media/selectdatasource.png)

-   Stellen Sie eine Verbindung mit ihrer localdb-Instanz her, und geben Sie als Datenbanknamen **Blog**

    ![Localdb-Verbindung](~/ef6/media/localdbconnection.png)

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

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('The Visual Studio Blog', 'http://blogs.msdn.com/visualstudio/')

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('.NET Framework Blog', 'http://blogs.msdn.com/dotnet/')
```

## <a name="2-create-the-application"></a>2. Erstellen der Anwendung

Um dies zu gewährleisten, wird eine einfache Konsolenanwendung erstellt, die Code First für den Datenzugriff verwendet:

-   Öffnen Sie Visual Studio.
-   **Datei- &gt; New- &gt; Project...**
-   Wählen Sie im Menü auf der linken Seite und **Konsolenanwendung** **Windows** aus.
-   Geben Sie **codefirstexistingdatabasesample** als Name ein.
-   Klicken Sie auf **OK**.

 

## <a name="3-reverse-engineer-model"></a>3. Reverse-Engineering-Modell

Wir verwenden die Entity Framework Tools für Visual Studio, um uns dabei zu unterstützen, Code zu generieren, der der Datenbank zugeordnet wird. Diese Tools erzeugen lediglich Code, den Sie bei Bedarf auch per Hand eingeben können.

-   **Project- &gt; Neues Element hinzufügen...**
-   Wählen Sie im linken Menü **Daten** aus, und klicken Sie dann auf **ADO.NET Entity Data Model**
-   Geben Sie **bloggingcontext** als Name ein, und klicken Sie auf **OK** .
-   Dadurch wird der **Entity Data Model-Assistent** gestartet.
-   Wählen Sie **aus Datenbank Code First aus** , und klicken Sie auf **weiter**

    ![Assistent ein CFE](~/ef6/media/wizardonecfe.png)

-   Wählen Sie die Verbindung mit der Datenbank, die Sie im ersten Abschnitt erstellt haben, und klicken Sie auf **weiter** .

    ![Assistent zwei CFE](~/ef6/media/wizardtwocfe.png)

-   Aktivieren Sie das Kontrollkästchen neben **Tabellen** , um alle Tabellen zu importieren, **und klicken Sie**

    ![Assistent drei CFE](~/ef6/media/wizardthreecfe.png)

Nachdem der Reverse-Engineering-Prozess abgeschlossen wurde, wird dem Projekt eine Reihe von Elementen hinzugefügt. wir sehen uns nun an, was hinzugefügt wurde.

### <a name="configuration-file"></a>Konfigurationsdatei

Eine App.config-Datei wurde dem Projekt hinzugefügt. diese Datei enthält die Verbindungs Zeichenfolge für die vorhandene Datenbank.

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

*Sie sehen auch einige andere Einstellungen in der Konfigurationsdatei. Dies sind die standardmäßigen EF-Einstellungen, die Code First, wo Datenbanken erstellt werden sollen. Da wir eine Zuordnung zu einer vorhandenen Datenbank durchführt, werden diese Einstellungen in unserer Anwendung ignoriert.*

### <a name="derived-context"></a>Abgeleiteter Kontext

Dem Projekt wurde eine **bloggingcontext** -Klasse hinzugefügt. Der Kontext stellt eine Sitzung mit der Datenbank dar und ermöglicht das Abfragen und Speichern von Daten.
Der Kontext macht eine **dbset- &lt; TEntity &gt; ** für jeden Typ in unserem Modell verfügbar. Sie werden auch bemerken, dass der Standardkonstruktor einen Basiskonstruktor mit der Syntax **Name =** aufruft. Dadurch wird Code First, dass die für diesen Kontext zu verwendende Verbindungs Zeichenfolge aus der Konfigurationsdatei geladen werden soll.

``` csharp
public partial class BloggingContext : DbContext
    {
        public BloggingContext()
            : base("name=BloggingContext")
        {
        }

        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
        }
    }
```

*Sie sollten immer die Syntax **Name =** verwenden, wenn Sie eine Verbindungs Zeichenfolge in der Konfigurationsdatei verwenden. Dadurch wird sichergestellt, dass, wenn die Verbindungs Zeichenfolge nicht vorhanden ist, Entity Framework ausgelöst wird, anstatt eine neue Datenbank gemäß der Konvention zu erstellen.*

### <a name="model-classes"></a>Modellklassen

Schließlich wurden dem Projekt auch ein **Blog** und eine **Post** -Klasse hinzugefügt. Dabei handelt es sich um die Domänen Klassen, aus denen unser Modell besteht. Sie sehen Daten Anmerkungen, die auf die Klassen angewendet werden, um die Konfiguration anzugeben, bei der die Code First Konventionen nicht mit der Struktur der vorhandenen Datenbank übereinstimmen. Beispielsweise wird die **StringLength** -Anmerkung auf **Blog.Name** und " **Blog. URL** " angezeigt, da Sie eine maximale Länge von **200** in der Datenbank aufweist (der Code First Standard ist die Verwendung der maximale-Länge, die vom Datenbankanbieter unterstützt wird- **nvarchar (max)** in SQL Server).

``` csharp
public partial class Blog
{
    public Blog()
    {
        Posts = new HashSet<Post>();
    }

    public int BlogId { get; set; }

    [StringLength(200)]
    public string Name { get; set; }

    [StringLength(200)]
    public string Url { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

## <a name="4-reading--writing-data"></a>4. lesen & schreiben von Daten

Nachdem wir nun über ein Modell verfügen, ist es an der Zeit, es für den Zugriff auf einige Daten zu verwenden. Implementieren Sie die **Main** -Methode in **Program.cs** , wie unten gezeigt. Mit diesem Code wird eine neue Instanz des Kontexts erstellt und anschließend verwendet, um einen neuen **Blog**einzufügen. Anschließend wird eine LINQ-Abfrage verwendet, um alle **Blogs** aus der Datenbank alphabetisch nach **Titel**abzurufen.

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
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a>Was geschieht, wenn meine Datenbank geändert wird?

Der Assistent für die Code First-Datenbank ist darauf ausgelegt, einen Ausgangspunkt Satz von Klassen zu generieren, die Sie dann optimieren und ändern können. Wenn das Datenbankschema geändert wird, können Sie entweder die Klassen manuell bearbeiten oder einen weiteren umgekehrten Ingenieur ausführen, um die Klassen zu überschreiben.

## <a name="using-code-first-migrations-to-an-existing-database"></a>Verwenden von Code First-Migrationen für eine vorhandene Datenbank

Wenn Sie Code First-Migrationen mit einer vorhandenen Datenbank verwenden möchten, lesen Sie [Code First-Migrationen zu einer vorhandenen Datenbank](xref:ef6/modeling/code-first/migrations/existing-database).

## <a name="summary"></a>Zusammenfassung

In dieser exemplarischen Vorgehensweise haben wir Code First Entwicklung mit einer vorhandenen Datenbank untersucht. Wir haben die Entity Framework Tools für Visual Studio verwendet, um einen Satz von Klassen umzukehren, die der Datenbank zugeordnet sind und zum Speichern und Abrufen von Daten verwendet werden können.
